# Practical and fast frontend testing
Prepared for a HelsinkiJS meetup.

## Presentation

Download the slides: [PDF](https://github.com/attekei/practical-frontend-testing/blob/master/frontend-testing.pdf?raw=true) / [Keynote](https://github.com/attekei/practical-frontend-testing/blob/master/frontend-testing.key?raw=true)

## Appendix: Technical implementation

This didn't fit in to the 20min presentation itself although I'm sure that it is useful for many folks who use React/Redux!

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
	
### Metabase approach to frontend testing

It's specific to React+Redux only. I explain our approach very briefly – browse [Metabase test source code](https://github.com/metabase/metabase/blob/master/frontend/test/) for seeing our implementation in detail.

* Use Jest instead of a browser testing framework
* If possible, render the complete app (including the global app state) instead of individual React components
* Include routing and use a simulated browser history for being able to navigate through in-app links
	* `react-router`  with `history.createMemoryHistory()` in our case
* Use Enzyme for querying elements – harness the full power of your React component hierarchy instead of writing plain CSS selectors!
* Create a new Redux store for each test. This is a neat way to keep your tests isolated from each other.
* Between interactions you want to wait for the app to reach a desired state. This is convenient to do by waiting for Redux actions to complete. Sadly there aren't good open source libraries that do that for you yet! See how this currently is done in Metabase codebase:
https://github.com/metabase/metabase/blob/master/frontend/test/__support__/integrated_tests.js

### Our tests before and after migrating from Selenium browser tests

An example how spending some time on your test infrastructure can make your tests faster, more stable and more fun to write.

Compare this old Selenium test case:
https://github.com/metabase/metabase/blob/v0.24.0/frontend/test/e2e/dashboards/dashboards.spec.js

To a completely rewritten one:
https://github.com/metabase/metabase/blob/master/frontend/test/dashboards/dashboards.integ.spec.js
