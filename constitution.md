# Constitution — Regras persistentes do projeto

1. Código e identificadores internos em inglês; documentação em português. Rotas e campos da API devem manter os nomes exatos do contrato.
2. Usar Node.js 24 + Express 5.2.1, com CommonJS. Express deve ser a única dependência externa direta.
3. A API deve respeitar `contrato.json` e `spec.md`. O contrato prevalece sobre exemplos incompatíveis.
4. Valores monetários devem ser centavos inteiros. O tempo médio deve arredondar `0,5` para cima.
5. Datas e horários retornados devem usar ISO-8601 com fuso `-03:00`, preservando o instante representado.
6. Persistência em memória, em um único processo. Cada aplicação deve ter estado próprio; reiniciar perde os dados.
7. Testes com `node:test`, `node:assert/strict` e `fetch`. Cada regra de negócio deve ter ao menos um teste de borda.
8. Instalação, testes e execução devem ocorrer em containers Node. Preservar o lockfile e usar `npm ci` após a instalação inicial.
9. O servidor deve escutar em `0.0.0.0:8005`, sem exigir variável de ambiente.
10. Não incluir arquivos Git, `node_modules` ou segredos na imagem. Informar somente verificações realmente executadas.

## Parâmetros da variante

- TARIFA_HORA_CENTAVOS = 600
- FRACAO_MINUTOS = 15
- TETO_DIARIO_CENTAVOS = 6000
- PORTA_SERVICO = 8005
- TOLERANCIA_MINUTOS = 10