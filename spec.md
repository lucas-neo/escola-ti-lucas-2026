# Spec — Zona Azul Digital

## Objetivo

Desenvolver uma API pra abrir e encerrar bilhetes de estacionamento por carro, listar os bilhetes e gerar relatório diário. Somente API.

**URL BASE:** http://localhost:8005

## UC1 — Abrir bilhete

**Intenção:** Eu como usuário quero abrir um bilhete novo pro carro.

**Rota:** `POST /bilhetes`

**Entrada:**

- `placa`: string obrigatória, com exatamente 7 caracteres alfanuméricos maiúsculos.
- `entrada`: string opcional em ISO-8601 com fuso horário. Se não informar, usa o instante atual.

**Critérios de aceite:**

1. Quando eu mandar uma placa válida sem bilhete aberto e com entrada válida ou ausente, a API deve abrir o bilhete e retornar `201` com `id`, `placa`, `entrada` e `status: "aberto"`.
2. Quando eu informar uma entrada válida, a API deve usar esse instante pra abrir o bilhete.
3. Quando eu não mandar a entrada, a API deve usar o instante atual.
4. A API deve retornar a entrada em ISO-8601 com fuso `-03:00`, preservando o instante informado.
5. Se a placa tiver faltando ou for inválida, então a API deve retornar `422` com `{"erro":"placa_invalida"}`.
6. Se a entrada informada não estiver em ISO-8601 com fuso, então a API deve retornar `422` com `{"erro":"entrada_invalida"}`.
7. Se os dados forem válidos e a placa já tiver bilhete aberto, então a API deve retornar `409` com `{"erro":"bilhete_em_aberto"}`, sem criar outro bilhete.
8. A API deve validar o formato dos campos antes de verificar o conflito de placa.

## UC2 — Encerrar bilhete

**Intenção:** Eu como usuário quero encerrar o bilhete e ver quanto tempo ficou e quanto vou pagar.

**Rota:** `POST /bilhetes/{id}/encerramento`

**Entrada:**

- `id`: identificador do bilhete na rota.
- Não precisa enviar body.

**Critérios de aceite:**

1. Quando eu encerrar um bilhete aberto que existe, a API deve registrar o instante atual como saída, mudar o estado para `"encerrado"`, calcular a duração entre entrada e saída e retornar `200` com `id`, `placa`, `entrada`, `saida`, `minutos` e `valor_centavos`.
2. A API deve retornar entrada e saída em ISO-8601 com fuso `-03:00`.
3. Quando passar da tolerância, a API deve dividir o tempo total em minutos por 15, arredondar a quantidade de frações pra cima e cobrar 150 centavos por fração.
4. Quando a duração for múltiplo exato de 15 minutos, a API deve cobrar só essas frações. Se passar desse múltiplo, deve cobrar a próxima fração, respeitando o teto.
5. Se a conta passar de 6000 centavos, então a API deve cobrar só 6000. Esse teto vale por bilhete, mesmo que atravesse mais de um dia.
6. A API deve retornar `valor_centavos` como inteiro em centavos e aplicar a tolerância do UC7.
7. Quando encerrar o bilhete, a API deve tirar ele da lista de ativos e manter no histórico da placa.
8. Se o bilhete não existir, então a API deve retornar `404` com `{"erro":"bilhete_nao_encontrado"}`.
9. Se o bilhete já tiver encerrado, então a API deve retornar `409` com `{"erro":"bilhete_ja_encerrado"}`.

## UC3 — Listar ativos

**Intenção:** Eu como usuário quero ver os bilhetes que ainda estão abertos.

**Rota:** `GET /bilhetes/ativos`

**Entrada:** Nenhuma.

**Critérios de aceite:**

1. Quando eu consultar os ativos, a API deve retornar `200` com um array de todos os bilhetes abertos, mais recentes primeiro.
2. A API não deve colocar bilhetes encerrados ou cancelados nessa lista.
3. Quando não tiver bilhete aberto, a API deve retornar `200` com `[]`.

## UC4 — Relatório diário

**Intenção:** Eu como usuário quero consultar o relatório de um dia.

**Rota:** `GET /relatorios/diario?data=AAAA-MM-DD`

**Entrada:**

- `data`: string obrigatória na query string, formato `AAAA-MM-DD`.

**Critérios de aceite:**

1. Quando eu mandar uma data válida, a API deve retornar `200` com `data`, `total_bilhetes`, `faturamento_centavos` e `tempo_medio_minutos` daquele dia.
2. A API deve calcular o tempo médio usando só os bilhetes encerrados no dia que eu pedi, considerando a data da saída no fuso `-03:00`.
3. Quando a média tiver parte fracionária, a API deve arredondar pro inteiro mais próximo, com `0,5` pra cima. Exemplo: 44,5 deve retornar 45.
4. A API deve retornar `faturamento_centavos` como inteiro em centavos.
5. Se a data tiver faltando ou fora do formato, então a API deve retornar `422` com `{"erro":"data_invalida"}`.

## UC5 — Cancelar bilhete

**Intenção:** Eu como usuário quero cancelar um bilhete aberto sem cobrar nada.

**Rota:** `POST /bilhetes/{id}/cancelamento`

**Entrada:**

- `id`: identificador do bilhete na rota.
- Não precisa enviar body.

**Critérios de aceite:**

1. Quando eu cancelar um bilhete aberto que existe, a API deve mudar o estado para `"cancelado"` e retornar `200` com `id`, `placa`, `entrada` e `status: "cancelado"`.
2. Quando cancelar, a API não deve gerar cobrança nem os campos `saida` e `valor_centavos`.
3. Quando cancelar o bilhete, a API deve tirar ele dos ativos e manter no histórico da placa.
4. Se o bilhete não existir, então a API deve retornar `404` com `{"erro":"bilhete_nao_encontrado"}`.
5. Se o bilhete já tiver encerrado ou cancelado, então a API deve retornar `409` com `{"erro":"bilhete_nao_aberto"}`.

## UC6 — Histórico por placa

**Intenção:** Eu como usuário quero ver todos os bilhetes de uma placa.

**Rota:** `GET /bilhetes?placa={placa}`

**Entrada:**

- `placa`: string obrigatória na query string, com exatamente 7 caracteres alfanuméricos maiúsculos.

**Critérios de aceite:**

1. Quando eu mandar uma placa válida, a API deve retornar `200` com um array de todos os bilhetes dela, mais recentes primeiro.
2. A API deve incluir bilhetes de qualquer status: `"aberto"`, `"encerrado"` e `"cancelado"`.
3. A API deve retornar só os bilhetes da placa que eu mandei.
4. Quando a placa nunca tiver estacionado, a API deve retornar `200` com `[]`.
5. Se a placa tiver faltando ou for inválida, então a API deve retornar `422` com `{"erro":"placa_invalida"}`.

## UC7 — Tolerância gratuita

**Intenção:** Eu como usuário quero que dentro da tolerância não cobre nada.

**Rota:** Regra aplicada em `POST /bilhetes/{id}/encerramento`.

**Entrada:**

- Mesma requisição do UC2, sem campo novo.
- Duração do bilhete e `TOLERANCIA_MINUTOS = 10`, definido pela variante.

**Critérios de aceite:**

1. Quando a duração for menor ou igual a 10 minutos, a API deve retornar `valor_centavos: 0`.
2. Quando passar de 10 minutos, mesmo por um minuto, a API deve cobrar o tempo inteiro desde o primeiro minuto, usando as frações e o teto do UC2.
3. A API não deve descontar os 10 minutos da duração usada na conta.

## UC8 — Uma vaga por placa

**Intenção:** Eu como usuário quero impedir que o mesmo carro tenha dois bilhetes abertos ao mesmo tempo.

**Rota:** Regra aplicada em `POST /bilhetes`.

**Entrada:**

- `placa`: string obrigatória, com exatamente 7 caracteres alfanuméricos maiúsculos.
- `entrada`: string opcional em ISO-8601 com fuso horário. Mesma requisição do UC1.

**Critérios de aceite:**

1. Enquanto a placa tiver um bilhete aberto, quando eu tentar abrir outro com dados válidos, a API deve retornar `409` com `{"erro":"bilhete_em_aberto"}`, sem criar outro bilhete.
2. Quando o anterior tiver encerrado ou cancelado, a API deve permitir abrir um novo pra mesma placa, conforme o UC1.
3. Se os dados tiverem formato inválido, então a API deve retornar o erro `422` correspondente do UC1 antes de verificar o conflito.