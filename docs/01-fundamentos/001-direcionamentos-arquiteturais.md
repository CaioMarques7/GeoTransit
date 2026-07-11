# 001 - Direcionamentos Arquiteturais

## Objetivo

Este documento descreve os direcionamentos arquiteturais que orientarão todas as decisões técnicas deste projeto.

Ao longo da implementação, qualquer escolha de tecnologia, padrão arquitetural ou estratégia de processamento deverá ser justificada com base nos requisitos apresentados aqui.

---

# Cenário de Negócio

Uma plataforma de mobilidade urbana deve responder rapidamente às consultas realizadas por usuários que desejam saber:

* qual é a parada mais próxima de sua localização;
* quais linhas atendem essa parada;
* qual veículo chegará primeiro;
* qual o tempo estimado até sua chegada.

A plataforma deve manter boa experiência de uso mesmo durante períodos de alta demanda, caracterizados por milhões de consultas concorrentes distribuídas em grandes centros urbanos.

---

# Requisitos Funcionais

## RF-01

A plataforma deve localizar as paradas mais próximas da posição geográfica informada pelo usuário.

---

## RF-02

A plataforma deve identificar as linhas que atendem cada parada encontrada.

---

## RF-03

A plataforma deve correlacionar informações do GTFS Static e GTFS Realtime para estimar a próxima chegada de veículos.

---

## RF-04

A plataforma deve retornar os resultados ordenados pelo menor tempo estimado de chegada.

---

## RF-05

A plataforma deve atualizar automaticamente as informações provenientes do GTFS Static.

---

## RF-06

A plataforma deve atualizar continuamente as informações provenientes do GTFS Realtime.

---

## RF-07

As atualizações do GTFS não devem interromper as consultas em andamento.

---

# Requisitos Não Funcionais

## Performance

### RNF-01

A consulta deve possuir latência suficientemente baixa para proporcionar experiência fluida ao usuário.

---

### RNF-02

A latência deve permanecer estável mesmo durante períodos de alta concorrência.

---

## Escalabilidade

### RNF-03

A arquitetura deve privilegiar escalabilidade horizontal.

---

### RNF-04

O aumento da quantidade de usuários não deve provocar crescimento proporcional da complexidade computacional de cada requisição.

---

## Disponibilidade

### RNF-05

Atualizações do GTFS Static não devem provocar indisponibilidade da plataforma.

---

### RNF-06

Falhas durante a atualização de um novo dataset não devem afetar a versão atualmente publicada.

---

## Consistência

### RNF-07

Consultas simultâneas devem utilizar sempre uma mesma versão do GTFS Static.

---

### RNF-08

Informações provenientes do GTFS Realtime podem apresentar consistência eventual.

---

## Observabilidade

### RNF-09

A plataforma deverá disponibilizar métricas suficientes para análise de desempenho e operação.

---

# Restrições

* Utilizar GTFS Static como fonte oficial dos dados operacionais.
* Utilizar GTFS Realtime como fonte oficial dos dados em tempo real.
* Considerar atualização periódica do GTFS Static.
* Considerar atualização contínua do GTFS Realtime.
* Não alterar o contrato público da API durante as evoluções arquiteturais.

---

# Premissas

* Os dados do GTFS Static possuem baixa frequência de alteração.
* O volume de consultas é significativamente maior que o volume de atualizações.
* Usuários geograficamente próximos tendem a realizar consultas semelhantes.
* A maior parte das consultas é composta por operações de leitura.
* O custo computacional de pré-processamento é aceitável quando reduz o custo das consultas.

---

# Atributos de Qualidade Prioritários

## 1. Performance

O tempo de resposta da API deve ser minimizado.

Grande parte do processamento deverá ocorrer fora do caminho crítico da requisição.

---

## 2. Escalabilidade

A arquitetura deve permitir crescimento do volume de consultas sem aumento proporcional do esforço computacional.

---

## 3. Disponibilidade

A atualização dos datasets não deve interromper a operação da plataforma.

---

## 4. Simplicidade Operacional

A solução deverá privilegiar componentes amplamente conhecidos, reduzindo complexidade de implantação e manutenção.

---

## 5. Evolução

Novas estratégias de indexação, projeção ou processamento deverão poder ser incorporadas sem alterar os consumidores da API.

---

# Riscos Arquiteturais

| ID   | Risco                                                  | Impacto                             |
| ---- | ------------------------------------------------------ | ----------------------------------- |
| R-01 | Crescimento contínuo do volume de consultas            | Aumento da latência                 |
| R-02 | Atualização incompleta do GTFS                         | Dados inconsistentes                |
| R-03 | Indisponibilidade durante publicação de novos datasets | Interrupção do serviço              |
| R-04 | Crescimento do processamento em tempo de requisição    | Redução da escalabilidade           |
| R-05 | Dependência excessiva do banco relacional              | Saturação da camada de persistência |

---

# Hipóteses Arquiteturais

Este projeto parte das seguintes hipóteses:

1. O processamento offline é mais barato do que repetir o mesmo processamento durante milhões de consultas.

2. Modelos especializados de leitura são mais eficientes do que consultas compostas executadas sob demanda.

3. Dados estáticos e dados em tempo real possuem características distintas e devem ser tratados por pipelines independentes.

4. O principal papel da API deve ser disponibilizar informações previamente materializadas, e não compor informações durante a requisição.

---

# Princípios Arquiteturais

Durante todo o desenvolvimento deste projeto, as decisões arquiteturais deverão seguir os seguintes princípios:

* Processar cedo sempre que possível.
* Consultar estruturas materializadas.
* Separar processamento offline de processamento online.
* Evitar repetição de trabalho.
* Manter pipelines independentes.
* Tornar a API o componente mais simples possível.
* Preferir simplicidade operacional antes de otimizações prematuras.
