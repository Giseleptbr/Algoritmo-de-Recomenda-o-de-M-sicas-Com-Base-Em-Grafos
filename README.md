# Music Recommendation Graph – Neo4j + Million Song Dataset

## Visão Geral

Este projeto implementa a modelagem de um sistema de recomendação musical baseado em grafos utilizando Neo4j.  
O objetivo é representar usuários, músicas, artistas e gêneros como nós interconectados, permitindo análises relacionais e recomendações personalizadas via consultas Cypher.

O sistema foi projetado com foco em:
- Modelagem de relacionamentos complexos
- Recomendação baseada em comportamento
- Integração com dados reais do domínio musical

---

## Objetivo do Desafio

Desenvolver um modelo de grafo capaz de:

- Representar usuários, músicas, artistas e gêneros como nós
- Representar interações do usuário como relacionamentos com propriedades
- Permitir consultas Cypher para recomendação musical personalizada

---

## Base de Dados Utilizada

### Million Song Dataset + Spotify + Last.fm

Dataset disponível no Kaggle contendo:

- Features de áudio das músicas
- Metadados de artistas
- Tags e gêneros musicais
- Histórico de escuta de usuários

Principais tipos de dados presentes:

| Tipo | Descrição |
|---|---|
| Tracks | Informações musicais e features de áudio |
| Artists | Artistas associados às músicas |
| Tags | Classificações sociais (Last.fm) |
| Genres | Categorias musicais |
| User History | Interações de escuta |

Exemplo de Features de Áudio:
- danceability  
- energy  
- valence  
- tempo  

---

## Modelagem do Grafo

### Nós (Entidades)

| Label | Descrição |
|---|---|
| User | Usuários do sistema |
| Track | Músicas |
| Artist | Artistas |
| Genre | Gêneros musicais |
| Tag | Tags sociais |

---

### Relacionamentos

| Relacionamento | Origem → Destino | Propriedades |
|---|---|---|
| LISTENED_TO | User → Track | times, source, date |
| LIKED | User → Track | likedAt |
| FOLLOWS | User → Artist/User | since |
| PERFORMED_BY | Track → Artist | — |
| IN_GENRE | Track → Genre | — |
| TAGGED | Track → Tag | weight |

---

## Exemplo de Estrutura do Grafo

User → escuta → Track  
Track → pertence → Genre  
Track → é performada por → Artist  

---

## Query Cypher Principal (Recomendação)

### Recomendação baseada em gênero já escutado pelo usuário

```cypher
MATCH (u:User {userId: 1})-[:LISTENED_TO]->(t:Track)-[:IN_GENRE]->(g:Genre)
MATCH (rec:Track)-[:IN_GENRE]->(g)
WHERE NOT (u)-[:LISTENED_TO]->(rec)
RETURN DISTINCT rec.title AS recommended_track, g.name AS genre
LIMIT 10;

## Outras Queries Úteis

### Tracks mais escutadas por usuário

```cypher
MATCH (u:User)-[l:LISTENED_TO]->(t:Track)
RETURN u.name, t.title, l.times
ORDER BY l.times DESC
LIMIT 10;

### Artistas favoritos baseados em escuta

```cypher
MATCH (u:User {userId: 1})-[:LISTENED_TO]->(t:Track)-[:PERFORMED_BY]->(a:Artist)
RETURN a.name, count(*) AS listens
ORDER BY listens DESC;
 
### Arquitetura Lógica
User
 ├ LISTENED_TO → Track
 ├ LIKED → Track
 └ FOLLOWS → Artist

Track
 ├ PERFORMED_BY → Artist
 ├ IN_GENRE → Genre
 └ TAGGED → Tag

#### Tecnologias Utilizadas

Neo4j

Cypher Query Language

Kaggle Dataset (Million Song Dataset)

Modelagem em Grafos

Graph Recommendation Systems
