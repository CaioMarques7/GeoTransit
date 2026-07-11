# 004 - Visão Arquitetural

## Objetivo

Este documento apresenta a visão arquitetural da plataforma.

Seu propósito é estabelecer como os diferentes componentes colaboram para responder consultas geoespaciais de alta concorrência, preservando baixa latência e simplicidade operacional.

Mais do que definir tecnologias, este documento descreve a filosofia adotada para construção da solução.

---

# Visão

A arquitetura proposta parte de uma premissa simples:

> **Nenhuma requisição deve executar trabalho que poderia ter sido realizado anteriormente.**

Sempre que possível, o processamento será antecipado para pipelines especializados.

A API passa a ser responsável apenas por disponibilizar informações previamente preparadas.

---

# Separação de Responsabilidades

A plataforma é organizada em três grandes pipelines independentes.

Cada pipeline possui objetivos, ritmo de atualização e responsabilidades distintas.

---

# Pipeline de Dados Estáticos

Responsável por transformar arquivos GTFS em estruturas otimizadas para consulta.

Características:

* processamento periódico;
* alto volume de processamento;
* nenhuma interação com usuários;
* publicação versionada.

Objetivo:

Produzir modelos de leitura espaciais.

---

# Pipeline de Dados em Tempo Real

Responsável por consumir continuamente eventos provenientes do GTFS Realtime.

Características:

* atualização contínua;
* processamento incremental;
* baixa latência;
* alta frequência.

Objetivo:

Manter previsões de chegada atualizadas.

---

# Pipeline de Consulta

Responsável exclusivamente pelo atendimento das requisições realizadas pelos consumidores.

Características:

* processamento mínimo;
* consultas em estruturas materializadas;
* baixa latência;
* escalabilidade horizontal.

Objetivo:

Responder rapidamente às consultas dos usuários.

---

# Fluxo Arquitetural

A plataforma pode ser compreendida como três fluxos independentes que convergem para um único modelo de leitura.

```text
GTFS Static

↓

Pipeline de Importação

↓

Read Model
```

```text
GTFS Realtime

↓

Pipeline de Atualização

↓

Projection
```

```text
Usuário

↓

API

↓

Projection

↓

Resposta
```

---

# Filosofia de Processamento

A arquitetura procura mover o processamento do caminho crítico da requisição para pipelines assíncronos.

Sempre que um cálculo puder ser realizado antecipadamente, ele deverá ser executado fora da API.

Essa estratégia reduz significativamente o custo computacional de cada consulta.

---

# Filosofia de Dados

Os diferentes conjuntos de dados possuem características distintas.

Por esse motivo, cada um deles recebe um tratamento específico.

## Dados Estáticos

* versionados;
* processados offline;
* publicados integralmente.

## Dados Dinâmicos

* continuamente atualizados;
* incrementalmente processados;
* utilizados para atualizar projeções.

## Modelos de Leitura

* especializados;
* materializados;
* otimizados para consulta.

---

# Papel da API

A API deixa de ser responsável por construir respostas.

Seu único objetivo passa a ser disponibilizar modelos previamente preparados.

Essa mudança reduz acoplamento, simplifica manutenção e melhora escalabilidade.

---

# Evolução Esperada

Novas estratégias de indexação espacial, mecanismos de projeção ou tecnologias de armazenamento poderão ser incorporadas sem alterar o contrato público da API.

A arquitetura foi concebida para evoluir substituindo pipelines internos, preservando a experiência dos consumidores.

---

# Princípio Fundamental

Toda decisão arquitetural deste projeto deverá respeitar o seguinte princípio:

> **Processar cedo. Consultar rápido. Evoluir continuamente.**

Esse princípio resume a filosofia adotada por toda a plataforma.
