# Practical and fast frontend testing
Prepared for a HelsinkiJS meetup.

## The super general section (not restricted to React/Redux)
### What I mean with ”frontend testing”?
* A human perspective
	* Verifying that a user sees what you expect him to see and is able to achieve his or her goals within a reasonable timeframe
* A developer jargon pespective
	* Functional testing: verifying that user-facing parts of your app behave as expected
	* Performance testing: detecting performance bottlenecks

### Why should you write frontend tests?
* *It improves your code quality:* It encourages you to write easy-to-understand modular code.
* *You sleep better:* You need to panic less whether different parts of your app behave correctly or not
* *It speeds you up:* Tests serve as an up-to-date documentation which is faster to digest than reading all the source code for a given feature.

### How to start writing frontend tests?
* Incrementally; trying to get from zero to 100% test coverage tends to be unrealistic
* Don’t aim for covering everything; focus on critical interaction paths
* One easy-to-adapt strategy that worked well for us has been writing integration tests for each new regression bug we encounter 
* The more frontend tests you have, the lower is the barrier to write more

### How to scope your tests?
* A visual demonstration would probably make sense here
* Try to match your tests with the reality of a user; obscure unit tests aren’t often very useful
* If you are time-constrained, write tests that cover as much as possible at once (i.e. the frontend UI interactions, frontend & backend business logic, database interactions)

### Doesn’t including the real backend to your tests slow you down?
* It definitely does to some degree, but for us it has been a worthwhile tradeoff
* You can write a script that wraps the test runner and initializes the backend before tests and tears it down afterwards

## How to implement frontend tests
…for those dinosaurs who are still using those old-fashioned libs.

### Different ways of testing
* Tests that use a simulated implementation of DOM
	* Everything runs in a NodeJS environment
	* Doesn’t support all browser fanciness (like HTML5 canvas)
	* Popular testing toolboxes: Jest (Jasmine + Enzyme + JSDom)

* Tests that use a real browser
	* Renders the app on a browser like Chromium
	* All web APIs available
	* Popular testing toolboxes: Chromeless, PhantomJS, NightmareJS

### Real browser tests are hard
/disclaimer: this is strongly opinionated/

* They are pretty cumbersome to write and modify
	* Writing small, atomic test cases is hard because you can’t easily initialize the environment to a desired state
	* You are restricted to CSS selectors which are often ugly – for instance `#FilterPopover .List-item:first-child>a`
	* You aren’t able to store references to DOM elements to variables for later use
* They are relatively slow and unstable
	* Browsers are slow and complex
	* You have to use polling-based methods like `waitForComponentToAppear(cssSelector)` which incur an overhead
	* We didn’t find a way to get rid of frequent random failures
* The test infrastructure is more complicated
	* In CI a 3rd party solution like Sauce is often required


## The Metabase approach to frontend testing
### 1. Use Jest for simulating a complete app
* Use Jest instead of a browser testing framework
* If possible, render the complete app (including the global app state) instead of individual React components
* Include routing and use a simulated browser history for being able to navigate through in-app links
	* `react-router`  with `history.createMemoryHistory()` in our case

### 2. Refer to React components instead of using CSS selectors
* Use Enzyme for querying elements – harness the full power of your React component hierarchy instead of writing plain CSS selectors!
`app.find(QueryBuilder).find(EntityMenu)`

### 3. Watch for changes in Redux store


### 4. If verifying the app state from UI is hard, check the app state directly from a Redux store
* 

```
// Should have visualization type set to Pin map
const card = getCard(store.getState())
expect(card.display).toBe("map")
```

### How to do this all?
You have to create a modified version of Redux store in order to use it efficiently in test environment and to watch for changes in Redux store. See 

### Additional reading (stolen from Hacker News)
https://blog.nelhage.com/2016/12/how-i-test/

## See more
All presentation material is available in https://github.com/attekei/practical-frontend-testing

Compare this old Selenium test case:
https://github.com/metabase/metabase/blob/v0.24.0/frontend/test/e2e/dashboards/dashboards.spec.js

To a completely rewritten one:
https://github.com/metabase/metabase/blob/master/frontend/test/dashboards/dashboards.integ.spec.js