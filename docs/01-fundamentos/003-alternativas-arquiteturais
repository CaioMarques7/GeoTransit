# 003 - Alternativas Arquiteturais

## Objetivo

Este documento apresenta as principais alternativas arquiteturais consideradas para solucionar o problema proposto.

O objetivo não é identificar uma solução universalmente superior, mas compreender os trade-offs envolvidos em cada abordagem e justificar as decisões adotadas ao longo deste projeto.

---

# O Problema

O desafio consiste em responder, com baixa latência, consultas do tipo:

> "Qual é o próximo ônibus que passará perto de mim?"

Essa resposta depende da correlação entre dados geoespaciais estáticos e informações operacionais em tempo real.

Em cenários de alta concorrência, repetir todo esse processamento durante cada requisição torna-se inviável.

---

# Alternativa 1 — Banco Relacional com Índice Espacial

## Descrição

Toda consulta é executada diretamente sobre o banco relacional utilizando recursos espaciais nativos.

Fluxo:

Localização → SQL Server → Spatial Index → STDistance → Paradas → GTFS Realtime → ETA

## Vantagens

* Arquitetura simples.
* Poucos componentes.
* Alta consistência.
* Fácil manutenção.

## Desvantagens

* Grande quantidade de processamento durante cada consulta.
* Forte dependência do banco relacional.
* Escalabilidade limitada pelo mecanismo de consulta.

## Avaliação

Adequada para pequenos volumes de consultas.

---

# Alternativa 2 — Read Model Geoespacial (Redis)

## Descrição

As paradas passam a ser materializadas em um modelo especializado de leitura utilizando recursos geoespaciais em memória.

Fluxo:

GTFS Static → Worker → Redis Geospatial

Consulta:

Localização → Redis → Paradas → GTFS Realtime → ETA

## Vantagens

* Baixa latência.
* Redução significativa da carga sobre o banco relacional.
* Implementação relativamente simples.
* Excelente escalabilidade horizontal.

## Desvantagens

* Ainda existe composição durante a requisição.
* A API continua responsável por correlacionar informações.

## Avaliação

Excelente equilíbrio entre desempenho e simplicidade operacional.

---

# Alternativa 3 — Grid Espacial / H3

## Descrição

O espaço geográfico é discretizado em células previamente conhecidas.

Cada célula mantém referências para as paradas mais relevantes daquela região.

Consulta:

Localização → Célula → Paradas

## Vantagens

* Consulta extremamente rápida.
* Lookup praticamente constante.
* Excelente previsibilidade.

## Desvantagens

* Pipeline de construção mais complexo.
* Maior consumo de memória.
* Necessidade de gerenciamento do índice espacial.

## Avaliação

Adequada para plataformas de escala muito elevada.

---

# Alternativa 4 — Projeções Materializadas

## Descrição

A arquitetura deixa de responder perguntas em tempo de execução.

Passa a manter respostas continuamente atualizadas.

Exemplo:

Parada

↓

Próximas chegadas

↓

ETA

↓

Linha

A API apenas consulta projeções prontas.

## Vantagens

* Latência mínima.
* API extremamente simples.
* Processamento deslocado para pipelines especializados.
* Excelente escalabilidade.

## Desvantagens

* Maior complexidade de implementação.
* Necessidade de processamento contínuo.
* Maior esforço operacional.

## Avaliação

Arquitetura orientada a leitura, indicada para ambientes de alta concorrência.

---

# Comparativo

| Critério                     | SQL Spatial | Redis Geospatial | Grid/H3     | Projeções   |
| ---------------------------- | ----------- | ---------------- | ----------- | ----------- |
| Simplicidade                 | Alta        | Alta             | Média       | Média       |
| Latência                     | Média       | Muito baixa      | Muito baixa | Muito baixa |
| Escalabilidade               | Média       | Alta             | Alta        | Muito alta  |
| Processamento por requisição | Alto        | Médio            | Baixo       | Muito baixo |
| Processamento offline        | Baixo       | Médio            | Alto        | Alto        |
| Complexidade operacional     | Baixa       | Baixa            | Média       | Média       |
| Evolução futura              | Média       | Alta             | Alta        | Muito alta  |

---

# Evolução Arquitetural

Uma característica importante deste estudo é que essas alternativas não são excludentes.

Na prática, elas representam diferentes estágios de maturidade arquitetural.

A evolução pode ocorrer da seguinte forma:

Banco Relacional

↓

Read Model Geoespacial

↓

Indexação Espacial Especializada

↓

Projeções Materializadas

Cada etapa desloca parte do processamento para fora do caminho crítico da requisição.

---

# Decisão Arquitetural

Este projeto adotará uma arquitetura baseada em modelos especializados de leitura.

A estratégia consiste em separar completamente:

* processamento dos dados estáticos;
* processamento dos dados em tempo real;
* disponibilização das consultas.

A API deixa de ser responsável por compor informações durante a requisição e passa a atuar como uma camada de acesso a projeções previamente materializadas.

Essa decisão atende diretamente aos principais direcionadores arquiteturais definidos neste projeto:

* redução da latência;
* escalabilidade horizontal;
* simplicidade da API;
* processamento antecipado;
* minimização do trabalho executado durante cada consulta.
