# Plan — Arquitetura e decisões

## Stack

- Node.js 24 + Express 5.2.1 com CommonJS (justificativa: API pequena, simples de gerar e testar).
- Persistência em memória, pois o contrato não exige banco de dados.
- Testes nativos do Node, evitando dependências adicionais.

## Estrutura de arquivos a gerar

```text
app.js             # Express, rotas, validações, regras e memória
server.js          # inicia o servidor na porta 8005
api.test.js        # testes HTTP que refletem tests.md
package.json       # dependências e comandos
package-lock.json  # versões resolvidas das dependências
Containerfile      # imagem Node para testes e execução
.dockerignore      # exclui node_modules, .git e segredos
README.md          # instruções de teste e execução
```

## Decisões

1. `app.js` deve exportar `createApp`, sem abrir uma porta. Cada chamada cria seu próprio armazenamento, permitindo testes independentes.
2. Usar uma função de relógio que possa ser substituída nos testes. Em produção, usar o instante atual; nos testes, controlar o horário sem esperar tempo real.
3. Comparar datas pelo instante representado e retornar os horários com fuso `-03:00`.
4. Cada fração de 15 minutos custa 150 centavos. Arredondar as frações para cima, aplicar a tolerância sem descontá-la e limitar a cobrança a 6000 centavos por bilhete.
5. Os testes devem iniciar servidores em portas livres e encerrá-los ao terminar.
6. Usar um único container, pois existe somente a API com armazenamento em memória.

### Cálculo da cobrança

Usar esta função para calcular o valor em centavos a partir da duração em minutos, conforme nossa variante:

```js
function calculateCostCents(minutes) {
  if (minutes <= 10) return 0;
  const fractions = Math.ceil(minutes / 15);
  return Math.min(fractions * 150, 6000);
}
```

Passou da tolerância, cobra o tempo inteiro, sem descontar os 10 minutos. A fração é arredondada pra cima e o valor fica limitado a 6000 centavos por bilhete.

## Preparação

Executar na pasta da solução gerada:

```sh
docker run --rm -v "$PWD:/app" -w /app node:24-slim sh -ec '
npm init -y
npm install --save-exact express@5.2.1
npm pkg set type=commonjs
npm pkg set "scripts.start=node server.js"
npm pkg set "scripts.test=node --test api.test.js"
'
```

## Container e verificação

Gerar `Containerfile` com imagem `node:24-slim`, diretório `/app`, instalação por `npm ci --omit=dev`, cópia da aplicação e dos testes, usuário `node`, `EXPOSE 8005` e comando `npm start`.

Gerar `.dockerignore` excluindo `node_modules`, `.git`, `.env` e `.env.*`.

Depois de gerar a aplicação e os testes, executar:

```sh
docker build -f Containerfile -t zona-azul .
docker run --rm zona-azul npm test
docker run --rm -p 8005:8005 zona-azul
```

Corrigir falhas e repetir as verificações afetadas. Documentar os comandos no README e informar que os dados são perdidos ao reiniciar.