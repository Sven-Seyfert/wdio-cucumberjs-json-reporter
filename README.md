# wdio-cucumberjs-json-reporter
A WDIO reporter that creates CucumberJS JSON files for WebdriverIO V5+

[![dependencies Status](https://david-dm.org/wswebcreation/wdio-cucumberjs-json-reporter/status.svg)](https://david-dm.org/wswebcreation/wdio-cucumberjs-json-reporter)
[![Build Status](https://travis-ci.org/wswebcreation/wdio-cucumberjs-json-reporter.svg?branch=master)](https://travis-ci.org/wswebcreation/wdio-cucumberjs-json-reporter)
[![Coverage Status](https://coveralls.io/repos/github/wswebcreation/wdio-cucumberjs-json-reporter/badge.svg?branch=master)](https://coveralls.io/github/wswebcreation/wdio-cucumberjs-json-reporter?branch=master)
[![MIT license](http://img.shields.io/badge/license-MIT-brightgreen.svg)](http://opensource.org/licenses/MIT)

[![NPM](https://nodei.co/npm/wdio-cucumberjs-json-reporter.png)](https://nodei.co/npm/wdio-cucumberjs-json-reporter/)

> **THIS MODULE CAN ONLY WORK WITH WebdriverIO V5+!**

> **THIS MODULE IS NOT A REPLACEMENT OF [wdio-multiple-cucumber-html-reporter](https://github.com/wswebcreation/wdio-multiple-cucumber-html-reporter). THAT MODULE ONLY SUPPORTS WEBDRIVERIO V4 AND ALSO CREATES A REPORT. THIS MODULE ONLY CREATES A JSON, NO REPORT!!**

## What does it do
This reporter will generate a **Cucumber JSON file** for each feature that is being tested. The JSON file can be used with whatever report you want to use like for example [multiple-cucumber-html-reporter](https://github.com/wswebcreation/multiple-cucumber-html-reporter).

It will also add metadata about the running instance to the feature file and last but not least, it will give you the opportunity to add attachments to the JSON output.

## Installation
The easiest way is to keep `wdio-cucumberjs-json-reporter` as a devDependency in your `package.json`.

```json
{
  "devDependencies": {
    "wdio-cucumberjs-json-reporter": "^1.0.0"
  }
}
```

You can simple do it by:

```bash
npm install wdio-cucumberjs-json-reporter --save-dev
```

so it will automatically be added to your `package.json`

Instructions on how to install `WebdriverIO` can be found [here](http://webdriver.io/guide/getstarted/install.html).

## Configuration
Configure the output directory and the language in your wdio.conf.js file:

```js
exports.config = {
    // ...
    reporters: [
        // Like this with the default options, see the options below
        'cucumberjs-json',

        // OR like this if you want to set the folder and the language
        [ 'cucumberjs-json', {
                jsonFolder: '.tmp/new/',
                language: 'en',
            },
        ],
    ],
  // ...
}
```

> DON'T USE BOTH WAYS OF ADDING THE REPORTER, THIS IS JUST AN EXAMPLE!

## Options
### `jsonFolder`
- **Type:** `String`
- **Mandatory:** No
- **Default:** `.tmp/json/`

The directory where the JSON files, generated by this report, will be stored, relative from where the script is started.

**N.B.:** If you use a npm script from the command line, like for example `npm run test` the `jsonFolder` will be relative from the path
where the script is executed. Executing it from the root of your project will also create the `jsonFolder` in the root of you project.

### `language`
- **Type:** `String`
- **Mandatory:** No
- **Default:** `en`

The language in which the Gherkin scenarios are written (defaults to English). The list of language codes and its keywords can be found [here](https://cucumber.io/docs/gherkin/reference/#overview).

## Metadata
As said, this report can automatically store the metadata of the current machine / device the feature has been executed on.

To customize this you can add IT by adding the following object to your `capabilities`

```js
// Example wdio.conf.js
exports.config = {
    //..
    capabilities: [
        {
            browserName: 'chrome',
            // Add this
            'cjson:metadata': {
                // For a browser
                browser: {
                    name: 'chrome',
                    version: '58',
                },
                // for an app
                app: {
                  name: 'name.of.app.ipa',
                  version: '1.2.3',
                },
                device: 'MacBook Pro 15',
                platform: {
                    name: 'OSX',
                    version: '10.12.6'
                }
            },
        },
    ],
};
```

> The metadata object needs to have the `cjson` prefix, otherwise it will not work!

### Metadata values
#### `metadata.app.name`
- **Type:** `string`

**e.g.:** The name of the app.

#### `metadata.app.version`
- **Type:** `string`

**e.g.:** The version of the app.

#### `metadata.browser.name`
- **Type:** `string`
- **Possible values:** `internet explorer | edge | chrome | firefox | safari`

#### `metadata.browser.version`
- **Type:** `string`

**e.g.:** The version of the browser, this can be added manual or retrieved during the execution of the tests to get the exact version number.

#### `metadata.device`
- **Type:** `string`

**e.g.:** A name that represents the type of device. For example, if you run it on a virtual machine, you can place it here `Virtual Machine`,
or the name of the mobile, like for example `iPhone 7 Plus`.

#### `metadata.platform.name`
- **Type:** `string`
- **Possible values:** `windows | osx | linux | ubuntu | android | ios`

#### `metadata.platform.version`
- **Type:** `string`

**e.g.:** The version of the platform

> If you don't provide the `browser`-object in the metadata, this module will automatically determine it for you. **It will always override it with the most recent value it can determine.**

> If you don't provide the `device` and or the `platform`-object it will be defaulted for you to `not known`

> If you don't provide a `browser.name` or a `browser.version` the module will try to determine this automatically.

## Attachment
You have the option to attach data to the JSON file in all these hooks / steps:

- Before(All)
- After(All)
- Given
- When
- Then
- And

The only thing you need to provide is the following code in your step files.

```js
import cucumberJson from 'wdio-cucumberjs-json-reporter';

// Attach a string (if no type is provided it will automatically default to `text/plain`
cucumberJson.attach('just a string');
cucumberJson.attach('just a second string', 'text/plain');

// Attach JSON
cucumberJson.attach({"json-string": true}, 'application/json');

// Attach a screenshot in a before hook
cucumberJson.attach(browser.takeScreenshot(), 'image/png');
```

## Use it with multiple-cucumber-html-reporter
The previous module for WebdriverIO V4, [wdio-multiple-cucumber-html-reporter](https://github.com/wswebcreation/wdio-multiple-cucumber-html-reporter),
had a build in connection with the [multiple-cucumber-html-reporter](https://github.com/wswebcreation/multiple-cucumber-html-reporter)-module. **This is not the case for this
reporter** because the new setup of WebdriverIO V5 is based on a instance which doesn't allow me to use the `onPrepare` and `onComplete` hook.

If you still want to use the [multiple-cucumber-html-reporter](https://github.com/wswebcreation/multiple-cucumber-html-reporter)-module you can add the following to your config file.

- Install the module with

    ```bash
    npm install multiple-cucumber-html-reporter --save-dev
    ```

  Maybe even install `fs-extra` to remove the report folder before you start all  sessions.

    ```bash
    npm install fs-extra --save-dev
    ```

- Add this to your configuration file

    ```js
    // Import the module
    const { generate } = require('multiple-cucumber-html-reporter');
    const { removeSync } = require('fs-extra');

    // Example wdio.conf.js
    exports.config = {
      //..

      // =====
      // Hooks
      // =====
      /**
       * Gets executed once before all workers get launched.
       */
      onPrepare: () => {
        // Remove the `.tmp/` folder that holds the json and report files
        removeSync('.tmp/');
      },
      /**
       * Gets executed after all workers got shut down and the process is about to exit.
       */
      onComplete: () => {
        // Generate the report when it all tests are done
        generate({
          // Required
          // This part needs to be the same path where you store the JSON files
          // default = '.tmp/json/'
          jsonDir: '.tmp/json/',
          reportPath: '.tmp/report/',
          // for more options see https://github.com/wswebcreation/multiple-cucumber-html-reporter#options
        });
      }
    }
    ```
