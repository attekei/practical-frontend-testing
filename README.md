# Practical and fast frontend testing
Prepared for a HelsinkiJS meetup.

## Presentation



## The Metabase approach to frontend testing

I explain our approach very briefly – browse [Metabase test source code](https://github.com/metabase/metabase/blob/master/frontend/test/) for seeing our implementation in detail.

### 1. Use Jest for simulating a complete app
* Use Jest instead of a browser testing framework
* If possible, render the complete app (including the global app state) instead of individual React components
* Include routing and use a simulated browser history for being able to navigate through in-app links
	* `react-router`  with `history.createMemoryHistory()` in our case

### 2. Refer to React components instead of using CSS selectors
* Use Enzyme for querying elements – harness the full power of your React component hierarchy instead of writing plain CSS selectors!

Example:
```
expect(
  app.find(NewQueryOption)
    .filterWhere((component) => component.prop('title') === "Metrics")
    .length
).toBe(1)
```
### 3. Create a new Redux store for each test
* This is a neat way to keep your tests isolated from each other.

### 4. Watch for changes in Redux store
* Between interactions you want to wait for the app to reach a desired state. This is convenient to do by waiting for Redux actions to complete.

### 5. If verifying the app state from UI is hard, check the app state directly from a Redux store
* It's sometimes helpful to confirm that the app state is in a desired state instead of just checking that UI looks correct. You should check the state like this:

```
// Should have visualization type set to Pin map
const card = getCard(store.getState())
expect(card.display).toBe("map")
```

### 6. How to do this all?
* You have to create a modified version of Redux store in order to use it efficiently in test environment and to watch for changes in Redux store. Sadly there aren't good open source libraries that do that for you yet. See how this currently is done in Metabase codebase:
https://github.com/metabase/metabase/blob/master/frontend/test/__support__/integrated_tests.js

### Example of how our tests improved after a rewrite

Compare this old Selenium test case:
https://github.com/metabase/metabase/blob/v0.24.0/frontend/test/e2e/dashboards/dashboards.spec.js

To a completely rewritten one:
https://github.com/metabase/metabase/blob/master/frontend/test/dashboards/dashboards.integ.spec.js
