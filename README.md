# sap-cap-devcontainer

A vscode devcontainer for SAP CAP / UI5 development

This devcontainer is based on the `javascript-node:20` docker image which use Node.js 20 LTS.

Added features in the docker container include:

- Based on the official Node.js image (https://github.com/devcontainers/images/tree/main/src/javascript-node)
    - `node` user
    - zsh installed
- SAP CAP (Cloud Application Programming model)
    - SQLite
    - cds-dk v7.9.2 (april 2024)
    - mbt (latest for linux/arm64)
    - CF cli v8
- SAPUI5 tooling & generators
- Neovim
- fzf (Fuzzy Finder)
- Yeoman
- Github Copilot
- Chromium and chromium-driver with wdio.
- Shell setup [arm64 version defaults to zsh which has inbuilt completion; amd64 version defaults to bash and adds :bash-completion]

The devcontainer config file adds:

- Forwards ports 4004 and 3010.
- Installs the cf cli mutliapps plugin (doesn't seem to work from the Dockerfile).
- Set the cf api region (change this to match your region) (deafults to APJ).
- Mount the host computer home folder as /host-home-folder in the container - for easy access to files on the host computer.
- Adds features for github cli; shell history; some shell utils.

# devcontainer config files
Two sets of config files exist:
- .devcontainer folder - for macos silicon
- .devcontainer/linux_amd64 - for intel type architectures (older macs and windows).

Vscode only allows one set of these config files inside the root .devcontainer directory when working with named docker volumes so we need to duplicate one set - I have chosen to use the apple silicon files.

# APP TESTING
This container has chromium and chromium-driver installed for headless browser testing. To use WebDriver.io (wdio) as a test driver install it as per instructions here: https://webdriver.io/docs/gettingstarted and use the [QUnit service](https://github.com/mauriciolauffer/wdio-qunit-service) to link it to your SAPUI5 QUnit tests and OPA5 integration tests.

> We use wdio because Karma is deprecated.

e.g. Run from your CAP project root folder:
```
npm i --save-dev @wdio/cli

# run configuration wizard
npx wdio config

npm install wdio-qunit-service --save-dev
```

You can skip the configuration wizard and manually create the wdio.conf.js file as shown below - this is setup for testing SAPUI5 apps.

Sample wdio.conf.js file (place this file inside the `app/<my-app>/webapp/test` folder):
```js
exports.config = {
    //
    // ====================
    // Runner Configuration
    // ====================
    // WebdriverIO supports running e2e tests as well as unit and component tests.
    runner: 'local',
    //
    // ==================
    // Specify Test Files
    // ==================
    // Define which test specs should run. The pattern is relative to the directory
    // of the configuration file being run.
    //
    // The specs are defined as an array of spec files (optionally using wildcards
    // that will be expanded). The test for each spec file will be run in a separate
    // worker process. In order to have a group of spec files run in the same worker
    // process simply enclose them in an array within the specs array.
    //
    // The path of the spec files will be resolved relative from the directory of
    // of the config file unless it's absolute.
    //
    specs: [
        // ToDo: define location for spec files here
        './**/*.test.js'
    ],
    // Patterns to exclude.
    exclude: [
        // 'path/to/excluded/files'
    ],
    //
    // ============
    // Capabilities
    // ============
    // Define your capabilities here. WebdriverIO can run multiple capabilities at the same
    // time. Depending on the number of capabilities, WebdriverIO launches several test
    // sessions. Within your capabilities you can overwrite the spec and exclude options in
    // order to group specific specs to a specific capability.
    //
    // First, you can define how many instances should be started at the same time. Let's
    // say you have 3 different capabilities (Chrome, Firefox, and Safari) and you have
    // set maxInstances to 1; wdio will spawn 3 processes. Therefore, if you have 10 spec
    // files and you set maxInstances to 10, all spec files will get tested at the same time
    // and 30 processes will get spawned. The property handles how many capabilities
    // from the same test should run tests.
    //
    maxInstances: 10,
    //
    // If you have trouble getting all important capabilities together, check out the
    // Sauce Labs platform configurator - a great tool to configure your capabilities:
    // https://saucelabs.com/platform/platform-configurator
    //
    capabilities: [{
        browserName: 'chrome',
        'goog:chromeOptions': {
            binary: '/usr/bin/chromium',
            args: ['headless', 'disable-gpu']
        },
        'wdio:chromedriverOptions': {
            binary: '/usr/bin/chromedriver'
        }
    }],

    //
    // ===================
    // Test Configurations
    // ===================
    // Define all options that are relevant for the WebdriverIO instance here
    //
    // Level of logging verbosity: trace | debug | info | warn | error | silent
    logLevel: 'error',
    //
    // Set specific log levels per logger
    // loggers:
    // - webdriver, webdriverio
    // - @wdio/browserstack-service, @wdio/devtools-service, @wdio/sauce-service
    // - @wdio/mocha-framework, @wdio/jasmine-framework
    // - @wdio/local-runner
    // - @wdio/sumologic-reporter
    // - @wdio/cli, @wdio/config, @wdio/utils
    // Level of logging verbosity: trace | debug | info | warn | error | silent
    // logLevels: {
    //     webdriver: 'info',
    //     '@wdio/appium-service': 'info'
    // },
    //
    // If you only want to run your tests until a specific amount of tests have failed use
    // bail (default is 0 - don't bail, run all tests).
    bail: 0,
    //
    // Set a base URL in order to shorten url command calls. If your `url` parameter starts
    // with `/`, the base url gets prepended, not including the path portion of your baseUrl.
    // If your `url` parameter starts without a scheme or `/` (like `some/path`), the base url
    // gets prepended directly.
    // baseUrl: 'http://localhost:8080',
    //
    // Default timeout for all waitFor* commands.
    waitforTimeout: 10000,
    //
    // Default timeout in milliseconds for request
    // if browser driver or grid doesn't send response
    connectionRetryTimeout: 120000,
    //
    // Default request retries count
    connectionRetryCount: 3,
    //
    // Test runner services
    // Services take over a specific job you don't want to take care of. They enhance
    // your test setup with almost no effort. Unlike plugins, they don't add new
    // commands. Instead, they hook themselves up into the test process.
    // services: [],
    //
    // Framework you want to run your specs with.
    // The following are supported: Mocha, Jasmine, and Cucumber
    // see also: https://webdriver.io/docs/frameworks
    //
    // Make sure you have the wdio adapter package for the specific framework installed
    // before running any tests.
    framework: 'mocha',
    
    //
    // The number of times to retry the entire specfile when it fails as a whole
    // specFileRetries: 1,
    //
    // Delay in seconds between the spec file retry attempts
    // specFileRetriesDelay: 0,
    //
    // Whether or not retried spec files should be retried immediately or deferred to the end of the queue
    // specFileRetriesDeferred: false,
    //
    // Test reporter for stdout.
    // The only one supported by default is 'dot'
    // see also: https://webdriver.io/docs/dot-reporter
    reporters: ['spec'],

    // Options to be passed to Mocha.
    // See the full list at http://mochajs.org/
    mochaOpts: {
        ui: 'bdd',
        timeout: 60000
    },

    services: ['qunit'],
}

```

In your root package json add a script:
```
"test": "wdio run ./app/basicapp/webapp/test/wdio.conf.js"
```

Check the wdio QUnit service for details on linking to sapui5 Qunit and OPA5 tests.

Here is an example `integration.test.js`:
```
describe('QUnit test page OPA', () => {
    it('should pass QUnit OPA tests - LOCAL', async () => {
      const url = 'http://localhost:4004/basicapp/webapp/test/integration/opaTests.qunit.html';
      await browser.url(url);
      const qunitResults = await browser.getQUnitResults();
      expect(qunitResults).toBeTruthy();
    });
  });
```

Essentially the wdio test framework is just delegating to existing sapui5 tests.

Now with `cds w` running in one terminal you can use `npn run test` to execute your SAPUI5 tests.

## npm scripts
Setup some handy npm scripts for testing. Here is an example (note the use of the `concurrently` package to allow for the running of `cds serve` and the wdio tests at the same time:

```
"scripts": {
    "start": "cds-serve",
    "test": "wdio run ./app/basicapp/webapp/test/wdio.conf.js",
    "test-cicd": "concurrently -n CDS,===WDIO=== -c blue,green --success first --kill-others --hide CDS \"npm start\" \"npm run test\"",
    "watch-basicapp": "cds watch --open basicapp/webapp/index.html?sap-ui-xx-viewCache=false",
    "test-basicapp": "cds watch --open basicapp/webapp/test/integration/opaTests.qunit.html"
  },
```

`npm run test` will run wdio but requires the cds service to be already running.

`npm run test-cicd` uses the concurrently module to start the cds service and once its up then run the wdio tests.
