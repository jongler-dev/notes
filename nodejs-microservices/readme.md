# Recommended node.js microservice code structure

```
lib/
  L controllers/
    L index.js
    L module1.controller.js
    L module2.controller.js
  L models/
    L index.js (exporting the dynamoDB directory)
    L baseModels/
      L entity1.js
      L entity2.js
    L dynamoDB/
      L entity1.js
      L enitity2.js
    L (optional) another db directory, same structure as dynamoDB dir.
  L routes/
    L index.js (exporting v1 and v2)
    L middlewares.js (used in express’s route functions)
    L v1/
      L module1.js
      L module2.js
    L v2/ (similar structure to v1)
  L services
    L index.js
    L service1.js
    L service2.js
  L utils (logging, error handling, etc)

tests
  L L0/
  L L1/
  L mocks/
  L resources/
```

## Notes

- **controllers**: aware of express req/res but not aware of the routes.
- **models**: no express stuff or business logic, just db stuff, ORM, etc.).
  > Note: the selection of the db used is not exposed to other modules, they only import entities from the models dir).
  - **baseModels**: one js file per entity used by the microservice, with a constructor.
  - **dynamoDB**: one js file per entity, with db functions specific to each entity, but intended for dynamoDB only.
- **routes**: each module.js file is a list of routes only, but it calls controllers and middlewares functions, e.g.:

  ```
  router.get(’/’, someController.getSomething())
  router.get(’/:id’, someController.getSomethingById())
  router.post(’/:id’, middleware.someMW1, middleware.someMW2,  someController.updateSomething())
  ```

- **services**: Contain business logic. Not aware of express, so no req/res.
- **tests**
  - **L0**: sanity tests, tests using mocks, otherwise zero dependencies on other services.
  - **L1**: e2e flows
  - **mocks**: mocks of other services (in our system) and 3rd party services (e.g aws, k8s, etc).
  - **resources**: static files used for testing.
