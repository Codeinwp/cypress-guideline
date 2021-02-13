# Guideline for Cypress tests
A guide line for writing and running Cypress tests locally.

**Table of Contents:**
<!-- no toc -->
- [Setting up the environment for Cypress](#setting-up-the-environment-for-cypress)
- [Guideline](#guideline)
  - [Linting](#linting)
  - [Arrow function vs Named functions](#arrow-function-vs-named-functions)
  - [Tests should be idempotent.](#tests-should-be-idempotent)
  - [Always create tests for bugfixes.](#always-create-tests-for-bugfixes)
  - [Avoid implicit waits.](#avoid-implicit-waits)
  - [Don't be afraid to write long tests with multiple assertions.](#dont-be-afraid-to-write-long-tests-with-multiple-assertions)
  - [Don't be afraid of creating functions to avoid duplicated code on a single spec.](#dont-be-afraid-of-creating-functions-to-avoid-duplicated-code-on-a-single-spec)
  - [If you are repeating an action over multiple specs, you should create a command.](#if-you-are-repeating-an-action-over-multiple-specs-you-should-create-a-command)
  - [Use "Should" Assertion Style to assert Cypress related commands.](#use-should-assertion-style-to-assert-cypress-related-commands)
  - [Use "Expect" Assertion Style syntax to assert single values like request or response body.](#use-expect-assertion-style-syntax-to-assert-single-values-like-request-or-response-body)

# Setting up the environment for Cypress
For setting the environment, check the guide for each repo. Please have Docker already installed before continuing; using Docker is the best way to ensure that the tests will run in the same environment as the CI build; you can install Docker [here](https://www.docker.com/products/docker-desktop).

To open Cypress test runner, you can use `yarn cypress open` when using yarn as package manager or `npx cypress open` when using npm.

# Guideline
It would be best if you read this before writing any Cypress tests.
This guideline uses [Cypress Best Practices](https://docs.cypress.io/guides/references/best-practices.html#article) and [mochaJS documentation](https://mochajs.org) as a reference.

## Linting
We are using [ESLint](http://eslint.org) with [Prettier](http://prettier.io) for formatting the code. Please set them up in your local environment to ensure that you are following the code standard. You can check for errors by running `eslint cypress/integration`.

## Arrow function vs Named functions
Cypress runs Mocha as the test runner. Since Mocha does not recommend using arrow functions (https://mochajs.org/#arrow-functions), we will use traditional functions in Mocha describers (describe, context, it, before and after hooks). For the other functions, we will use arrow functions as the pattern (e.g., functions inside spec and commands.)

## Tests should be idempotent.
All the setup needed for a test needs to be inside a before or beforeEach hook. This way, we ensure that the test cases are running without being dependent on other tests to run and gives us the same result each time it runs; this enables us to run parallel test cases without any problems. An excellent way to check if the test is independent is to set the .only after the spec for the test and run it locally like in the example:

```javascript
describe('Multiple tests', function () {
	it.only('Spec 1', function () {
		//this test will run because of the .only function
	});

	it('Spec 2', function () {
		// this test will not run since the .only function is on the other test
	});
});
```
 
## Always create tests for bugfixes.
We don't like to fix the same bug repeatedly, and automated tests can help us. When creating a PR for a bugfix, make sure to create a test that covers that scenario. If you need any help on building a test for the bug, feel free to contact [@gutoslv](https://github.com/gutoslv).

## Avoid implicit waits.
Cypress commands will automatically wait for something to happen; avoid waiting for an arbitrary period of milliseconds since they cost performance time for tests. You can read more about it [here](https://docs.cypress.io/guides/references/best-practices.html#Unnecessary-Waiting).

## Don't be afraid to write long tests with multiple assertions.
When coming from unit tests, you may feel tempted to write only one assertion per spec, but this is not necessary for Cypress.
We are writing end-to-end tests here. We want to ensure that the spec covers everything related to that scenario or functionality; writing multiple assertions should not be a problem. Cypress commands also have some default assertions that you can check [here](https://docs.cypress.io/guides/core-concepts/introduction-to-cypress.html#Default-Assertions).

## Don't be afraid of creating functions to avoid duplicated code on a single spec.
If you find yourself repeating the same piece of code over and over inside a spec, you can use Javascript functions to avoid code duplication. Try to [Don't Repeat Yourself, but Sometimes Repeat Yourself](https://spin.atomicobject.com/2020/02/18/sometimes-repeat-yourself/), BUT...

## If you are repeating an action over multiple specs, you should create a command.
Cypress enables us to create our [Custom Commands](https://docs.cypress.io/api/cypress-api/custom-commands.html#Syntax). We can use this to develop commands based on actions that we will use on multiple specs since we are already using the [app action pattern](https://www.cypress.io/blog/2019/01/03/stop-using-page-objects-and-start-using-app-actions/#application-actions-limitations).

## Use "Should" Assertion Style to assert Cypress related commands.
To assert anything related to Cypress commands like `cy.get()`, please use the [Should Assertion Style](https://www.chaijs.com/guide/styles/#should), here is an example:
```javascript
cy.get('selector').should('have.value', 'expected valued');
```

## Use "Expect" Assertion Style syntax to assert single values like request or response body.
To assert anything related to Cypress commands like `cy.request()` responses, please use the [Expect Assertion Style](https://www.chaijs.com/guide/styles/#expect), here is an example:
```javascript
cy.request(url, body).then((response) => {
    expect(response.body.value).to.contain('something');
});
```

# Pull Requests
When creating a new PR containing Cypress tests, please add [@gutoslv](https://github.com/gutoslv) as a reviewer.