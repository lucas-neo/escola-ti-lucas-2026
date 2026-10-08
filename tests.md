# Tests — Cenários de teste (TDD)

Cada item abaixo vira um teste em `api.test.js`, usando `node:test` e `node:assert`. Os casos de borda são obrigatórios.

| # | Cenário | Tipo |
| --- | --- | --- |
| T1 | Abrir bilhete com placa `ABC1D23` e entrada válida retorna 201 com `id`, `placa`, `entrada` e `status: "aberto"` | feliz |
| T2 | Abrir bilhete com placa de 6 caracteres retorna 422 com `erro: "placa_invalida"` | borda |
| T3 | Encerrar bilhete aberto após 16 minutos retorna 200 com `minutos: 16` e `valor_centavos: 300`, cobrando duas frações de 15 minutos | borda |
| T4 | Encerrar bilhete com exatamente 10 minutos retorna 200 com `valor_centavos: 0`, dentro da tolerância | borda |
| T5 | Encerrar bilhete com 11 minutos retorna 200 com `valor_centavos: 150`, sem descontar os 10 minutos de tolerância | borda |
| T6 | Encerrar bilhete após 601 minutos retorna 200 com `valor_centavos: 6000`, respeitando o teto | borda |
| T7 | Abrir outro bilhete para uma placa com bilhete aberto retorna 409 com `erro: "bilhete_em_aberto"` | borda |
| T8 | Cancelar bilhete aberto retorna 200 com `status: "cancelado"`, sem os campos `saida` e `valor_centavos` | feliz |
| T9 | Listar ativos após abrir um bilhete e cancelar outro retorna 200 com array contendo apenas o bilhete que permaneceu aberto | feliz |