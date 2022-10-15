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

### Schism-UI Tasks
- Fix dreadful css
- Add getEntitiesICanSee query to get entities
  - [Done] Removed creation of mock entities in entityReducer
  - Found that world/CHUNK queries happen in ChunkManager. Perhaps a good place to put getEntitiesICanSee is EntityManager
  - Remaining questions:
    - What a reasonable way and rate to call the query repeatedly?
  

### Schism-entity Tasks
- [done] Need a way to add fields/methods to entities received from the backend
- consider creating different EntityInput types for create and update mutations with different required fields. E.g., update might only require an id and ownerId (and one other field to do the update). This would make it impossible to arbitrarialy update field's on another player's entities. 
- Add mutations for:
  - Creating new units
  - Damaging another player's units
  - Performing unit actions
- need to decide whether and how to store, validate, and represent unit actions server-side. E.g., maybe all actions would initially run client-side while they're simultaneously being validated and disseminated to other clients
- create permissions and permission groups -> lock down access to super-user mutations
- add range field to entity object

### Deployment Tasks
- Investigate Dockerfiles for each repo
- Create a kubenernetes cluster and service-level configurations for each repo

### Tech Debt
- Refactor name of schism-service to schism-world
- Choose either using x,y,z fields or array (schism-entity sends back xyz, schism-ui uses both)
- Use a dictionary for myUnits in schism-ui instead of an array
  ```
  // Update header text
  Object.entries(dict).map(([key, entity]) => console.log(entity.id))
  // things to check
  // map - done
  // .length- done
  // myUnits - done
  ```
- Pattern for updating myEntities (dict) from server: https://jsfiddle.net/s92qgL3c/
```
console.log('original myUnits:')
const refToObjOne = myUnits.obj1;
console.log('refToObjOne', refToObjOne)
const myEntitiesObj = Object.entries(unitsICanSee)
.filter(([key, entity]) => entity.ownerId === 'me')
.reduce((acc, [key, entity]) => {
  if (entity.ownerId !== 'me') return acc;
  assign(acc, { [key]: entity })
  return acc;
}, myUnits)
console.log(myEntitiesObj)
```

# Local Development Tips:
* Start the services in this order:
  1. schism-service and its redis cache
  1. schism-entity
  1. schism-gateway (compose the supergraph first, if necessary)
  1. schism-ui
* Login through schism-ui and grab the authorization token. It can be added to apollo sandbox as a Shared Header to provide authentication and identity.
* The federated graph is available on port 4000, and the subgraphs for schism-service and schism-entity are available on 4010 and 4011, respectively.