# Spec — Zona Azul Digital

## Objetivo

Desenvolver uma API para abrir e encerrar bilhetes de estacionamento por carro, listar bilhetes e gerar relatórios diários. Somente API.

**URL BASE:** http://localhost:8005

## UC1 — Abrir bilhete

**Intenção:**

**Rota:** `POST /bilhetes`

**Entrada:**

- `placa`: string obrigatória, com 7 caracteres alfanuméricos maiúsculos.
- `entrada`: string opcional, em ISO-8601 com fuso horário. Quando ausente, utiliza o instante atual.

**Critérios de aceite:**

## UC2 — Encerrar bilhete

**Intenção:**

**Rota:** `POST /bilhetes/{id}/encerramento`

**Entrada:**

- `id`: identificador do bilhete, parâmetro de rota.

**Critérios de aceite:**

## UC3 — Listar ativos

**Intenção:**

**Rota:** `GET /bilhetes/ativos`

**Entrada:** 

**Critérios de aceite:**

## UC4 — Relatório diário

**Intenção:**

**Rota:** `GET /relatorios/diario?data=AAAA-MM-DD`

**Entrada:**

- `data`: string obrigatória na query string, formato `AAAA-MM-DD`.

**Critérios de aceite:**

## UC5 — Cancelar bilhete

**Intenção:**

**Rota:** `POST /bilhetes/{id}/cancelamento`

**Entrada:**

- `id`: identificador do bilhete, informado como parâmetro de rota.

**Critérios de aceite:**

## UC6 — Histórico por placa

**Intenção:**

**Rota:** `GET /bilhetes?placa={placa}`

**Entrada:**

- `placa`: string obrigatória na query string, com exatamente 7 caracteres alfanuméricos maiúsculos.

**Critérios de aceite:**

## UC7 — Tolerância gratuita

**Intenção:**

**Rota:** Regra aplicada em `POST /bilhetes/{id}/encerramento`.

**Entrada:**

- Mesma requisição do UC2, sem novos campos.
- Para aplicar a regra, utilizar a duração do bilhete e `TOLERANCIA_MINUTOS = 10`, definido pela variante.

**Critérios de aceite:**

## UC8 — Uma vaga por placa

**Intenção:**

**Rota:** Regra aplicada em `POST /bilhetes`.

**Entrada:** 
- `placa`: string obrigatória, com 7 caracteres alfanuméricos maiúsculos.
- `entrada`: string opcional, em ISO-8601 com fuso horário. Quando ausente, utiliza o instante atual.

- Mesma requisição do UC1.
- A verificação de bilhete aberto utiliza a `placa` informada

**Critérios de aceite:**