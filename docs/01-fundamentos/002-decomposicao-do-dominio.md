# 002 - Decomposição do Domínio

## Objetivo

Este documento identifica as capacidades de negócio necessárias para responder à principal pergunta deste projeto:

> **Qual é o próximo ônibus que chegará próximo do usuário?**

A decomposição apresentada aqui será utilizada como base para a definição dos componentes arquiteturais nas próximas etapas.

---

# Visão Geral

Embora o domínio seja baseado em GTFS, o sistema não existe para processar arquivos GTFS.

Seu propósito é disponibilizar informações de mobilidade urbana de forma rápida, escalável e confiável.

Assim, o domínio foi organizado em capacidades independentes, cada uma responsável por uma parte do fluxo de negócio.

---

# Capacidades

## 1. Gerenciamento de Datasets

Responsável por obter, validar, importar e publicar novas versões do GTFS Static.

### Responsabilidades

* Download do GTFS
* Validação do arquivo
* Extração
* Normalização
* Versionamento
* Publicação

---

## 2. Indexação Espacial

Responsável por transformar os dados geográficos em estruturas otimizadas para consulta.

### Responsabilidades

* Organizar paradas
* Construir índice espacial
* Disponibilizar consultas por proximidade

---

## 3. Processamento de Tempo Real

Responsável por consumir continuamente o GTFS Realtime.

### Responsabilidades

* Receber atualizações
* Atualizar estado dos veículos
* Identificar viagens ativas
* Atualizar estimativas

---

## 4. Motor de Estimativas (ETA Engine)

Responsável por transformar posições de veículos em estimativas de chegada.

### Responsabilidades

* Correlacionar Static e Realtime
* Calcular ETA
* Atualizar projeções

---

## 5. Materialização das Consultas

Responsável por manter modelos de leitura preparados para consumo.

### Responsabilidades

* Próximos ônibus por parada
* Próximos ônibus por região
* Atualização incremental

---

## 6. Consulta Pública

Responsável por disponibilizar informações aos consumidores.

### Responsabilidades

* Receber localização
* Identificar parada
* Recuperar projeção
* Retornar resposta

Nenhuma regra de negócio complexa deve existir nesta capacidade.

---

# Fluxo de Dependências

A relação entre as capacidades pode ser representada da seguinte forma:

```text
GTFS Static

↓

Gerenciamento de Dataset

↓

Indexação Espacial

↓

Materialização

↓

Consulta
```

Em paralelo:

```text
GTFS Realtime

↓

Processamento

↓

ETA Engine

↓

Materialização

↓

Consulta
```

Observe que ambas convergem para o mesmo modelo de leitura.

---

# Agregados do Domínio

Os principais agregados identificados são:

## Dataset

Representa uma versão publicada do GTFS.

---

## Stop

Representa uma parada física.

---

## Route

Representa uma linha de transporte.

---

## Trip

Representa uma viagem planejada.

---

## Vehicle

Representa um veículo em operação.

---

## Arrival Prediction

Representa uma previsão de chegada para uma parada.

---

## Read Projection

Representa um modelo otimizado para consulta.

É o principal agregado de leitura da arquitetura.

---

# Eventos do Domínio

Os seguintes eventos passam a existir naturalmente.

## DatasetPublished

Nova versão do GTFS publicada.

---

## VehiclePositionUpdated

Posição de veículo atualizada.

---

## ArrivalPredictionUpdated

Estimativa recalculada.

---

## ProjectionUpdated

Modelo de leitura atualizado.

---

# Resultado da Decomposição

Após a decomposição do domínio, observa-se que o sistema deixa de ser composto por uma única API responsável por todas as decisões.

Em seu lugar, surgem capacidades especializadas responsáveis por produzir modelos de leitura continuamente atualizados.

A API passa a exercer apenas o papel de disponibilizar essas informações aos consumidores.

Essa mudança reduz significativamente o trabalho executado durante cada requisição e desloca o processamento para pipelines especializados.
