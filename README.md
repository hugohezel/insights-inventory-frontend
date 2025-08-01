[![Build Status](https://img.shields.io/github/actions/workflow/status/RedhatInsights/insights-inventory-frontend/test.yml?branch=master)](https://github.com/RedHatInsights/insights-inventory-frontend/actions/workflows/test.yml) [![GitHub release](https://img.shields.io/github/release/RedHatInsights/insights-inventory-frontend.svg)](https://github.com/RedHatInsights/insights-inventory-frontend/releases/latest) [![codecov](https://codecov.io/gh/RedHatInsights/insights-inventory-frontend/branch/master/graph/badge.svg?token=XC4AD7NQFW)](https://codecov.io/gh/RedHatInsights/insights-inventory-frontend)

# Insights Inventory Frontend

This is the frontend application for [Insights Inventory](https://github.com/RedHatInsights/insights-inventory). It is based on the [insights-frontend-starter-app](git@github.com:RedHatInsights/insights-frontend-starter-app.git).

## Documentation

The repository contains components covered with the documentation comments using JSDoc markup language. The HTML documentation can be generated with the `npm run docs` script. Additionally, the documentation is automatically built with the [Generate and Release Documentation](/.github/workflows//docs.yml) GitHub action.
The deployed documentation is available at https://redhatinsights.github.io/insights-inventory-frontend.

## First time setup

### Quick start

1. Make sure you have [`Node.js`](https://nodejs.org/en/) (current LTS) and [`npm`](https://www.npmjs.com/) installed.
2. Run [script to patch your `/etc/hosts`](https://github.com/RedHatInsights/insights-proxy/blob/master/scripts/patch-etc-hosts.sh).
3. Make sure you are using [Red Hat proxy](http://hdn.corp.redhat.com/proxy.pac).

## Running locally

1. Clone the repository.
2. Install dependencies with `npm install`.
3. Run development server with `npm run start:proxy`. See `dev.webpack.config.js` and `package.json` (npm scripts) for more options and parameters available.
4. Local version of the app will be available at `https://stage.foo.redhat.com:1337/insights/inventory/`. If you run with slightly different setup (for example, using production environment), you should still see the generated URL in your terminal, the webpack script output.

## Testing your changes

We use Jest with React Testing Library to write unit tests. For larger pieces of code or components, we utilize Cypress. For testing commands shortcuts (like `npm run test`, `npm run test:ct`, etc.), take a look at the package.json file which lists available scripts.

Before opening a pull request, you can run `npm run verify:local` to make sure your changes pass automated tests (Jest and Cypress) and linter (both JS and CSS linters). We also execute [husky](https://typicode.github.io/husky/) hooks with every commit to make sure the changes pass basic lint checks.

## Testing with Playwright

Copy the example env file (`playwright_example.env`) and create a file named:.env For local development only the BASE_URL:https://stage.foo.redhat.com:1337 is required, which is already set in the example config.
You also need to add the username and password of your Stage testing account to the .env file.

Install the test runner
`npm install --save-dev @playwright/test`

Install Playwright browsers and dependencies 
`npx playwright install`

OR

If using any OS other than Fedora/Rhel (i.e., Mac, Ubuntu Linux):

`npx playwright install  --with-deps`

## Proxy setup to execute the test case using the local frontend : [consoledot-testing-proxy](https://github.com/dvagner/consoledot-testing-proxy)
```
 podman run -d -e HTTPS_PROXY=$RH_PROXY_URL -p 1337:1337 -v "$(pwd)/config:/config:ro,Z" --replace --name consoledot-testing-proxy quay.io/dvagner/consoledot-testing-proxy
```

`npx playwright test` will run the complete playwright test suite.

`npx playwright test --headed` will run the complete suite in a vnc-like browser so you can watch its interactions.

**Run the complete test file:**

`npx playwright test test_navigation.test.ts`

**Run a single test:**

`npx playwright test test_navigation.test.ts -g "User can navigate to the workspaces page via the menu"`

## Commit conventions

In order to keep our commits style consistent and the commits history easy to read, we utilize [semantic-release](https://github.com/semantic-release/semantic-release) which follows [Angular Commit Message Conventions](https://github.com/angular/angular/blob/main/CONTRIBUTING.md#-commit-message-format). Also, there is a commitlint check run on all branches which ensures that all the commits meet the expected format (`<type>(<scope>): <short summary>`). Following this standard and specifying at least the type and summary for each commit helps to automatically generate a changelog of changes.

## Testing federated modules with another application

If you want to test federated modules (such as InventoryTable or SystemDetail) in another application, you can utilise `LOCAL_APPS` environment variable and deploy the needed application on separate ports. To learn more about the variable, see https://github.com/RedHatInsights/frontend-components/tree/master/packages/config#running-multiple-local-frontend-applications.

### Example

We'll take for example [insights-advisor-frontend](https://github.com/RedHatInsights/insights-advisor-frontend).

Open new terminal, navigate to Advisor repository, and run it on a separate port without proxy:

```
npm run start -- --port=8003
```

In a separate terminal, run Inventory with proxy enabled and list Advisor:

```
LOCAL_APPS=advisor:8003~http npm run start:proxy
```

## Mocking Inventory API

This is one of the advanced methods to test frontend locally without waiting for API to have desired endpoints available. 

Inventory frontend has support for https://github.com/stoplightio/prism CLI. The CLI reads the OpenAPI schema, spins up a localhost server and serves dynamically generated responses for Inventory API endpoints (/hosts, /groups, etc.).

1. Verify package.json `config` section for the correct URL to OpenAPI schema (contains remote URL by default).
2. Verify dev.webpack.config.js `customProxy` settings. There you can specify which endpoints to proxy and modify request/response headers.
3. Run `npm run mock-server` to start the mock server. The first output must list the endpoints that are generated by the localhost server.
4. In another terminal, run `npm run start:mock` or `npm run start:mock:beta` to start the webpack server either in stage-stable or stabe-beta environment. The scripts set the MOCK variable to true and the customProxy is enabled.

## Inventory table and detail

We are serving inventory through federated modules, this means both inventory table and inventory detail is served to you in runtime. No need to install and rebuild when something changes in inventory.

### Applications using InventoryTable

These applications import `InventoryTable` component through federated modules:

- [vulnerability-ui](https://github.com/RedHatInsights/vulnerability-ui)
- [insights-remediations-frontend](https://github.com/RedHatInsights/insights-remediations-frontend)
- [sed-frontend](https://github.com/RedHatInsights/sed-frontend)
- [tasks-frontend](https://github.com/RedHatInsights/tasks-frontend)
- [compliance-frontend](https://github.com/RedHatInsights/compliance-frontend)
- [patchman-ui](https://github.com/RedHatInsights/patchman-ui)
- [malware-detection-frontend](https://github.com/RedHatInsights/malware-detection-frontend)
- [ros-frontend](https://github.com/RedHatInsights/ros-frontend)
- [insights-advisor-frontend](https://github.com/RedHatInsights/insights-advisor-frontend)
- [edge-frontend](https://github.com/RedHatInsights/edge-frontend)

## Documentation Links

* Components
  * [inventory](https://github.com/RedHatInsights/insights-inventory-frontend/blob/master/doc/inventory.md)
    * [props table](https://github.com/RedHatInsights/insights-inventory-frontend/blob/master/doc/props_table.md)
    * [props detail](https://github.com/RedHatInsights/insights-inventory-frontend/blob/master/doc/props_detail.md)
    * [custom fetch function](https://github.com/RedHatInsights/insights-inventory-frontend/blob/master/doc/custom_fetch.md)
    * [hide filters](https://github.com/RedHatInsights/insights-inventory-frontend/blob/master/doc/hide_filters.md)
    * [general info tab](https://github.com/RedHatInsights/insights-inventory-frontend/blob/master/doc/general_info.md)
  * [inventory_header](https://github.com/RedHatInsights/insights-inventory-frontend/blob/master/doc/inventory_header.md)

## Release process change in stage env

We use the new release process in stage environment that uses containers for deplyoment. Containerized Frontends are the new way to build, package and deploy the applications.

## Common Problems You Might Encounter

* Some APIs we use require the latest version of their client package in order to enjoy the latest properties they provide.
In case you checked the Network tab in the console and had a look at the requiered API call that should contain a property you need to fetch and use, but did not see this property in the list of properties in the Response tab, make sure you have the latest version of the client package that contains this API.
To make sure the versions align, 
Have a look at your `package.json` file and compare the appropriate client package version (that should have the API you need) with the latest published version on npmjs.com.
In case they don't match, update this client package to it's latest version by running this command: `npm i @redhat-cloud-services/{name-of-client-package}@latest`

Then, re-install the modules by running this command: `rm -rf node_modules && npm install`

And re-run the application.
This should solve this issue.

In case these steps did not solve your issue, it is possible that the latest package had not been released yet.
Please contact the appropriate team to release the package you are using, and go over the described process of updating the client package version again.
