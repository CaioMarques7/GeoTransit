# 005 - Modelo de leitura

## Objetivo

Este documento descreve os modelos de leitura utilizados pela plataforma.

A arquitetura proposta adota o princípio de materialização antecipada das consultas, deslocando o processamento para pipelines especializados.

Como consequência, a API deixa de construir respostas durante a requisição e passa apenas a consultar estruturas previamente preparadas.

---

# Motivação

A principal pergunta de negócio é:

> **"Qual é o próximo ônibus que passará perto de mim?"**

Essa pergunta pode ser decomposta em duas consultas independentes:

1. Qual é a parada mais próxima?
2. Quais são as próximas chegadas dessa parada?

Cada uma possui características diferentes de atualização e armazenamento.

Por esse motivo, foram definidos dois modelos de leitura independentes.

---

# Spatial Projection

## Objetivo

Materializar consultas geoespaciais.

### Entrada

Latitude

Longitude

### Saída

StopId

Distance

A Spatial Projection é responsável exclusivamente por localizar rapidamente a parada mais próxima.

Nenhuma informação operacional é armazenada nesta estrutura.

---

# Características

Origem:

GTFS Static

Atualização:

Periódica

Mutabilidade:

Baixa

Prioridade:

Velocidade de consulta

---

# Arrival Projection

## Objetivo

Materializar previsões de chegada.

### Entrada

StopId

### Saída

Lista ordenada de chegadas.

Cada elemento contém:

* Route
* Trip
* Vehicle
* ETA
* LastUpdate

---

# Características

Origem:

GTFS Realtime

Atualização:

Contínua

Mutabilidade:

Alta

Prioridade:

Atualização incremental

---

# Separação das Projeções

As duas projeções possuem ciclos de vida completamente distintos.

| Característica            | Spatial Projection | Arrival Projection         |
| ------------------------- | ------------------ | -------------------------- |
| Origem                    | GTFS Static        | GTFS Realtime              |
| Frequência de atualização | Baixa              | Alta                       |
| Volume de escrita         | Baixo              | Alto                       |
| Volume de leitura         | Muito alto         | Muito alto                 |
| Objetivo                  | Localizar paradas  | Informar próximas chegadas |

A separação reduz acoplamento entre dados estáticos e dados dinâmicos.

---

# Pipeline de Construção

## Spatial Projection

GTFS Static

↓

Parser

↓

Normalizer

↓

Spatial Builder

↓

Spatial Projection

---

## Arrival Projection

GTFS Realtime

↓

Realtime Consumer

↓

ETA Engine

↓

Arrival Builder

↓

Arrival Projection

---

# Fluxo da Consulta

Uma consulta é executada em duas etapas.

Etapa 1

Localização

↓

Spatial Projection

↓

StopId

Etapa 2

StopId

↓

Arrival Projection

↓

Próximos ônibus

Essa abordagem reduz significativamente o processamento realizado durante cada requisição.

---

# Modelo Conceitual

A arquitetura não depende de uma tecnologia específica.

Conceitualmente, os modelos de leitura armazenam:

Spatial Projection

* StopId
* Latitude
* Longitude

Arrival Projection

* StopId
* Upcoming Arrivals

Upcoming Arrival

* Route
* Trip
* Vehicle
* ETA
* Timestamp

---

# Independência Tecnológica

Os modelos apresentados neste documento representam estruturas conceituais.

A implementação poderá utilizar diferentes tecnologias de armazenamento, desde que preserve o mesmo contrato lógico.

Isso permite substituir mecanismos de infraestrutura sem alterar o comportamento da API ou dos consumidores.

---

# Benefícios

A adoção de modelos especializados de leitura proporciona:

* redução do processamento por requisição;
* simplificação da API;
* desacoplamento entre dados estáticos e dinâmicos;
* evolução independente dos pipelines;
* maior previsibilidade de desempenho;
* facilidade de escalabilidade horizontal.

---

# Considerações Finais

A principal decisão arquitetural deste projeto consiste em tratar consultas geoespaciais e previsões de chegada como problemas distintos.

Embora ambas sejam utilizadas na mesma requisição, seus ciclos de atualização, padrões de acesso e responsabilidades são diferentes.

A separação em projeções independentes permite que cada modelo seja otimizado de acordo com suas características, preservando simplicidade e desempenho.
