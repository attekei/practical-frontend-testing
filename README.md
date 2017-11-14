# Practical and fast frontend testing
Prepared for a HelsinkiJS meetup.

## Presentation



## The Metabase approach to frontend testing

This is something I didn't cover in the presentation itself. It's specific to React+Redux only. I explain our approach very briefly – browse [Metabase test source code](https://github.com/metabase/metabase/blob/master/frontend/test/) for seeing our implementation in detail.

* Use Jest instead of a browser testing framework
* If possible, render the complete app (including the global app state) instead of individual React components
* Include routing and use a simulated browser history for being able to navigate through in-app links
	* `react-router`  with `history.createMemoryHistory()` in our case
* Use Enzyme for querying elements – harness the full power of your React component hierarchy instead of writing plain CSS selectors!
* Create a new Redux store for each test. This is a neat way to keep your tests isolated from each other.
* Between interactions you want to wait for the app to reach a desired state. This is convenient to do by waiting for Redux actions to complete. Sadly there aren't good open source libraries that do that for you yet! See how this currently is done in Metabase codebase:
https://github.com/metabase/metabase/blob/master/frontend/test/__support__/integrated_tests.js

## Our tests before and after migrating from Selenium browser tests

An example how spending some time on your test infrastructure can make your tests faster, more stable and more fun to write.

Compare this old Selenium test case:
https://github.com/metabase/metabase/blob/v0.24.0/frontend/test/e2e/dashboards/dashboards.spec.js

To a completely rewritten one:
https://github.com/metabase/metabase/blob/master/frontend/test/dashboards/dashboards.integ.spec.js
