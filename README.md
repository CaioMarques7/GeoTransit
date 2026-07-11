# GeoTransit - Um caso de estudo arquitetural para um problema real

## O problema

Imagine uma plataforma de transporte público utilizada diariamente por milhões de pessoas, onde cada usuário deseja uma resposta simples:

> **"Qual é o próximo ônibus que passará perto de mim?"**

Embora essa pergunta pareça trivial sob a perspectiva do usuário, respondê-la em tempo real exige correlacionar grandes volumes de dados geoespaciais, informações estáticas de operação das linhas e atualizações contínuas da posição dos veículos.

Uma implementação tradicional normalmente resolve esse problema executando toda a composição das informações durante cada requisição.

De forma simplificada, o fluxo costuma ser:

1. Receber a localização do usuário.
2. Encontrar as paradas mais próximas.
3. Identificar quais linhas atendem essas paradas.
4. Consultar os veículos em operação.
5. Estimar o tempo de chegada de cada veículo.
6. Ordenar os resultados.
7. Retornar a melhor opção ao usuário.

Embora esse fluxo seja funcional, ele apresenta uma característica importante:

**Todo o trabalho é repetido para cada nova requisição.**

Em grandes centros urbanos, milhões de usuários podem realizar essa consulta simultaneamente, principalmente durante os horários de pico. Nesse cenário, pequenas ineficiências arquiteturais tornam-se rapidamente gargalos capazes de comprometer a experiência do usuário e aumentar significativamente o custo operacional da plataforma.

---

# Motivação

A principal hipótese deste estudo é que a arquitetura pode ser reorganizada para deslocar parte significativa do processamento para pipelines especializados, produzindo modelos de leitura preparados antecipadamente.

Essa abordagem reduz a quantidade de processamento realizada durante cada requisição e transforma consultas complexas em operações de leitura simples, preservando baixa latência mesmo sob elevada concorrência.

Mais do que otimizar uma tecnologia específica, o objetivo é investigar como decisões arquiteturais influenciam desempenho, escalabilidade, simplicidade operacional e custo de manutenção.

---

# Objetivo

Documentar o processo de tomada de decisão arquitetural utilizado para construir uma plataforma de consultas geoespaciais de alta performance e escalável, baseada em GTFS, explorando diferentes estratégias de processamento, materialização de dados e otimização de leitura.

Ao longo do projeto serão discutidos:

* modelagem do domínio;
* decomposição arquitetural;
* decisões de arquitetura (ADRs);
* diagramas C4;
* diagramas de sequência;
* estratégias de materialização;
* pipelines de processamento;
* benchmarks;
* trade-offs entre diferentes soluções.

---

# Evolução Arquitetural

Este projeto acompanha a evolução de diferentes abordagens para o mesmo problema.

```text
Banco Relacional

↓

Consultas Espaciais

↓

Read Models

↓

Projeções Materializadas

↓

Arquitetura Orientada à Leitura
```

O objetivo não é defender uma tecnologia específica, mas compreender como diferentes decisões arquiteturais influenciam desempenho, escalabilidade e simplicidade operacional.

---

# Tecnologias

A implementação utilizará principalmente:

* .NET
* Redis
* SQL Server
* GTFS Static
* GTFS Realtime

Entretanto, a arquitetura proposta não restringe ou correlaciona a solução final à tecnologias específicas. Dessa forma, qualquer um dos componentes de infraestrutura pode ser modificado sem alterar o modelo arquitetural.

---

# Público-alvo

Este projeto destina-se a profissionais interessados em:

* Arquitetura de Software
* Sistemas Distribuídos
* Engenharia de Plataforma
* Modelagem Geoespacial
* GTFS
* Processamento de Dados
* Arquiteturas Orientadas à Leitura

---

# Licença

Este projeto é disponibilizado apenas para fins educacionais e de demonstração arquitetural.
