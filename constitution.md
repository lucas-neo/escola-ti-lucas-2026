# Constitution — Regras persistentes do projeto

## Regras

1. Usar **Node.js 24 + Express 5.2.1**, JavaScript com CommonJS. Express deve ser a única dependência externa direta.
2. A API deve preservar as rotas, campos, códigos HTTP e corpos de erro definidos em `contrato.json`. O contrato prevalece sobre exemplos incompatíveis.
3. Valores monetários devem ser representados em **centavos inteiros**. O tempo médio deve arredondar `0,5` para cima.
4. Datas e horários retornados devem estar em ISO-8601 com fuso **`-03:00`**. Comparações de horários devem considerar o instante representado.
5. A API deve escutar em **`0.0.0.0:8005`**, sem exigir variável de ambiente.
6. Armazenar os bilhetes **em memória**, em um único processo. Cada aplicação criada deve possuir estado próprio; os dados são perdidos ao reiniciar.
7. Usar **`node:test`, `node:assert/strict` e `fetch`** nos testes. Cobrir cada regra de negócio e seus limites, conforme `tests.md`.
8. Instalação, testes e execução da solução gerada devem ocorrer em **containers Node**. Gerar `Containerfile`, `.dockerignore`, `package.json`, `package-lock.json` e README com os comandos necessários.
9. Gerar o lockfile na instalação inicial e preservá-lo nas instalações seguintes com `npm ci`.
10. Não incluir `node_modules`, arquivos Git ou segredos no contexto de construção da imagem. Registrar somente comandos e verificações efetivamente executados; informar impedimentos.

## Organização do código

```text
app.js        # configura e exporta createApp, sem iniciar servidor
server.js     # cria a aplicação e inicia o servidor na porta 8005
api.test.js   # testes HTTP dos cenários definidos em tests.md
```

Essa separação permite criar aplicações independentes nos testes, sem iniciar o servidor de `server.js`. Os testes podem abrir seu próprio servidor em uma porta livre e devem encerrá-lo ao terminar.

## Parâmetros da variante

- `TARIFA_HORA_CENTAVOS = 600`
- `FRACAO_MINUTOS = 15`
- `TETO_DIARIO_CENTAVOS = 6000`
- `PORTA_SERVICO = 8005`
- `TOLERANCIA_MINUTOS = 10`

**URL BASE:** http://localhost:8005