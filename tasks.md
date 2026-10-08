# Tasks — Decomposição

- [ ] T1 — Preparar o projeto em um container Node e criar `package.json` e lockfile com os comandos do `plan.md`.
- [ ] T2 — Criar `app.js` com `createApp`, memória separada por aplicação e relógio controlável nos testes. Criar `server.js` para iniciar o servidor na porta 8005.
- [ ] T3 — Implementar abertura de bilhete e uma vaga por placa (UC1 e UC8), validando os campos antes de verificar conflitos.
- [ ] T4 — Implementar encerramento e tolerância (UC2 e UC7), calculando frações, centavos e teto conforme a spec.
- [ ] T5 — Implementar cancelamento (UC5), sem cobrança e mantendo o bilhete no histórico.
- [ ] T6 — Implementar listagem de ativos e histórico por placa (UC3 e UC6), com os mais recentes primeiro.
- [ ] T7 — Implementar relatório diário (UC4), com os campos da spec e arredondamento da média com 0,5 para cima.
- [ ] T8 — Criar `api.test.js` seguindo `tests.md`, cobrindo sucesso, erros e casos de borda. Cada teste deve controlar seus dados e horário, sem esperar tempo real.
- [ ] T9 — Criar `Containerfile`, `.dockerignore` e `README.md` com os comandos para testar e iniciar a API.
- [ ] T10 — Construir a imagem, executar todos os testes no container e conferir se a API inicia na porta 8005. Corrigir as falhas e executar novamente os testes afetados.