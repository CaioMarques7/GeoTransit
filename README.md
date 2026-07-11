# GeoTransit - Um caso de estudo arquitetural para um problema real

## O problema

Imagine uma plataforma de transporte público utilizada diariamente por milhões de pessoas, onde cada usuário deseja uma resposta simples:

> **"Qual é o próximo ônibus que passará perto de mim?"**

Em grandes centros urbanos, milhões de usuários podem realizar consultas semelhantes ao mesmo tempo, tornando inviável repetir todo o processamento durante cada requisição.

Responder essa pergunta exige correlacionar dois conjuntos de dados muito diferentes:

* informações estáticas sobre a operação do transporte (GTFS Static);
* informações dinâmicas sobre a posição dos veículos (GTFS Realtime).

Este estudo documenta a evolução arquitetural de uma solução construída para responder esse desafio.

---

# Objetivo

Documentar o processo de tomada de decisão arquitetural utilizado para construir uma plataforma de consultas geoespaciais de alta performance e escalável, explorando diferentes estratégias de modelagem, processamento e materialização de dados.

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
