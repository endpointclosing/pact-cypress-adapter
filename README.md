# Endpoint Pact Cypress Adapter
Custom implementation to handle OAS properties. Specifically to handle oneOf, allOf, anyOf, or none.

# Pact Cypress Adapter
[![Build and test](https://github.com/pactflow/cypress-pact-adapter/actions/workflows/test-and-build.yaml/badge.svg)](https://github.com/pactflow/cypress-pact-adapter/actions/workflows/test-and-build.yaml) [![npm version](https://badge.fury.io/js/@endpoint%2Fpact-cypress-adapter.svg)](https://badge.fury.io/js/@endpoint%2Fpact-cypress-adapter)

Generate Pact contracts from your existing Cypress tests. 

> Accelerate your entry into contract testing with the Cypress development experience you know and love. — With Pact Cypress Adapter you can get the extra layer of testing safety, easily using existing mocks you’ve created with Cypress. 
>
> Read our [blog post](https://pactflow.io/blog/use-cypress-in-contract-testing/) to find out more, otherwise dive-right in.

## Installation
**NPM**:
```bash
npm i -D @endpoint/pact-cypress-adapter
```

**yarn**:
```bash
yarn add -D @endpoint/pact-cypress-adapter
```

Then, setup your cypress plugin at `cypress/plugins/index.js`

```js
const pactCypressPlugin = require('@endpoint/pact-cypress-adapter/dist/plugin')
const fs = require('fs')

module.exports = (on, config) => {
  pactCypressPlugin(on, config, fs)
}
```

Finally, update cypress/support/index.js file to include cypress-pact commands via adding:
```js
import '@endpoint/pact-cypress-adapter'
```

## Configuration
By default, this plugin omits most cypress auto-generated HTTP headers. 
### Add more headers to blocklist
To exclude other headers in your pact, add them as a list of strings in `cypress.json` under key `env.headersBlocklist`. Eg. in your `cypress.json`
```js
{
    ...otherCypressConfig,
    "env": {
        "headersBlocklist": ["ignore-me-globally"]
    }
}
```

Note: Header blocklist can be set up at test level. Check command [cy.setupPactHeaderBlocklist](/#cy.setupPactHeaderBlocklist([headers]))

### Ignore cypress auto-generated header blocklist
To stop cypress auto-generated HTTP headers being omitted by the plugin,  set `env.ignoreDefaultBlocklist` in your `cypress.json`. Eg. in your `cypress.json`
```js
{
    ...otherCypressConfig,
    "env": {
        "headersBlocklist": ["ignore-me-globally"],
        "ignoreDefaultBlocklist": true

    }
}
```

## Commands 
### cy.setupPact(consumerName:string, providerName: string)
Configure your consumer and provider name

**Example**
```js
before(() => {
    cy.setupPact('ui-consumer', 'api-provider')
})
```
### cy.usePactWait([alias] | alias)
Listen to aliased `cy.intercept` network call(s), record network request and response to a pact file.
[Usage and example](https://docs.cypress.io/api/commands/intercept) about `cy.intercept`

**Example**
```js
before(() => {
    cy.setupPact('ui-consumer', 'api-provider')
    cy.intercept('GET', '/users').as('getAllUsers')
})

//... cypress test

after(() => {
    cy.usePactWait(['getAllUsers'])
})

```

### cy.setupPactHeaderBlocklist([headers])
Add a list of headers that will be excluded in a pact at test case level

**Example**
```js
before(() => {
    cy.setupPact('ui-consumer', 'api-provider')
    cy.intercept('GET', '/users', headers: {'ignore-me': 'ignore me please'}).as('getAllUsers')
    cy.setupPactHeaderBlocklist(['ignore-me'])
})

//... cypress test

after(() => {
    cy.usePactWait(['getAllUsers'])
})
```

### cy.usePactRequest(option, alias) and cy.usePactGet([alias] | alias)
Use `cy.usePactRequest` to initiate network calls and use `cy.usePactGet` to record network request and response to a pact file.

Convenience wrapper for `cy.request(options).as(alias)` 

- Accepts a valid Cypress request options argument [Cypress request options argument](https://docs.cypress.io/api/commands/request#Arguments) 

**Example**
```js

before(() => {
    cy.setupPact('ui-consumer', 'api-provider')
    cy.usePactRequest(
      {
        method: 'GET',
        url: '/users',
      },
      'getAllUsers'
    )
})

//... cypress test

after(() => {
    cy.usePactGet(['getAllUsers'])
})

```

## Example Project
Check out a simple react app example project at [/example/todo-example](/example/todo-example/)

