# Schism
Schism is a Persistent World, Real-Time Strategy game.

# Microservice Architecture Diagram
```mermaid
graph TD
    A[schism-ui] --> B{schism-gateway}
    B --> C[schism-service]
    B --> D[schism-entity]
    D --> E(redis cache)
    C --> E
    C --> F(sql)
```

# Road-Map
```mermaid
gantt
dateFormat  YYY-MM-DD
title Near-Term Project Goals

section Entities
Standup gateway :done, des1, 2022-09-15, 2022-10-04
Standup service-entity :done, des2, 2022-10-04, 2022-10-08
Add MyEntity queries and mutations to service-entity : des3, after des2, 5d
Add MyEntity queries to service-ui : des4, after des3, 10d
```

## schism-entity tasks
- create permissions and permission groups -> lock down access to super-user mutations
- add range field to entity object

## Deployment Tasks
- Investigate Dockerfiles for each repo
- Create a kubenernetes cluster and service-level configurations for each repo

## Tech Debt
- Refactor name of schism-service to schism-world