# Testing with karma

> Part of Open Web Component Recommendation [open-wc](https://github.com/open-wc/open-wc/)

We want to provide a good set of defaults on how to facilitate your web component.

[![CircleCI](https://circleci.com/gh/open-wc/open-wc.svg?style=shield)](https://circleci.com/gh/open-wc/open-wc)
[![BrowserStack Status](https://www.browserstack.com/automate/badge.svg?badge_key=M2UrSFVRang2OWNuZXlWSlhVc3FUVlJtTDkxMnp6eGFDb2pNakl4bGxnbz0tLUE5RjhCU0NUT1ZWa0NuQ3MySFFWWnc9PQ==--86f7fac07cdbd01dd2b26ae84dc6c8ca49e45b50)](https://www.browserstack.com/automate/public-build/M2UrSFVRang2OWNuZXlWSlhVc3FUVlJtTDkxMnp6eGFDb2pNakl4bGxnbz0tLUE5RjhCU0NUT1ZWa0NuQ3MySFFWWnc9PQ==--86f7fac07cdbd01dd2b26ae84dc6c8ca49e45b50)
[![Renovate enabled](https://img.shields.io/badge/renovate-enabled-brightgreen.svg)](https://renovatebot.com/)

## Browser testing
Web components are at the cutting edge of browser support. Test solutions which run a simulated browser are often not suitable due to missing features. We therefore recommend running tests in a real browser. With headless chrome and firefox this very convenient to set up and use, and will give more testing confidence as you can run the same tests across multiple browsers.

Karma is a solid tool for running tests in the browser.

## Types of tests
Karma can be used both for running unit tests, as well as for running more complex e2e/integration tests.

## Karma config presets
Configuration should be customized per project, We provide several presets which can be used as a starting point. You can copy paste the presets into your own project for full control, or you can extend it and add your project specific requirements.

The intention for the presets is to be able to write browser runnable code, with some exceptions such bare module imports.

### Features
- Serves static files
- Runs tests through mocha
- Deep object diffs in mocha errors
- Test coverage through instanbul
- Resolves imports by bundling through webpack
- Supports `import.meta.url`
- Supports older browsers through the es5 preset

### Using
- Runner via [karma](https://karma-runner.github.io/)
- Building via [webpack](https://webpack.js.org/) via [karma-webpack](https://github.com/webpack-contrib/karma-webpack)
- Test Coverage via [istanbul](https://istanbul.js.org/) via [istanbul-instrumenter-loader](https://github.com/webpack-contrib/istanbul-instrumenter-loader)

### Setup
```bash
npx -p yo -p generator-open-wc -c 'yo open-wc:testing-karma'
```

::: tip Info
This is part of the default [open-wc](https://open-wc.org/) recommendation
:::

### Manual setup
For a minimal setup, extend the base config and specify where your tests are:

```javascript
// filename: karma.conf.js
const createBaseConfig = require('@open-wc/testing-karma/create-karma-config');

module.exports = (config) => {
  const baseConfig = createBaseConfig(config);

  config.set({
    ...baseConfig,

    files: [
      // if --grep flag is set, run those tests instead
      config.grep ? config.grep : 'test/**/*.test.js',
    ],
  });
};
```
Add to your scripts: `"test": "karma start karma.conf.js"` and run your tests with: `npm run test`.

### Testing single files or folders
By default karma runs all your test files. To test only a single file or folder, use the `--grep` flag. Make sure you are handling the grep option in your config, see the above example. Pass which files to test to the grep flag: `npm run test -- --grep test/foo/bar.test.js`.

Grep supports file globs.

### Watch mode
Use `npm run test -- --auto-watch=true --single-run=false` to keep karma running. Any code changes will trigger a re-run of your tests.

### Debugging in the browser
While testing, it can be useful to debug your tests in a real browser so that you can use the browser's dev tools.

Use `npm run test -- --single-run=false` to keep karma running. Then open the URL printed by karma when it boots up. By default this is `http://localhost:9876/`. Click the debug button in the top right corner, or go directly to `http://localhost:9876/debug.html`. You can bookmark this page for easy access.

Open the dev tools to see your test results in the console. For debugging, set breakpoints or add `debugger` statements to your code.

### Testing against older browsers
The default karma preset runs in browsers which support modern language features and web component. At the time of writing this is the latest chrome, firefox and safari. Edge is working on support.

To speed up development, we avoid compiling code or loading unnecessary polyfills. To run your tests against older browsers, create a es5 config which extends the es5 preset. This config takes care of compiling to es5, and loading the necessary es2015 and web component polyfills.


```javascript
// filename: karma.es5.config.js
const createBaseConfig = require('@open-wc/testing-karma/create-karma-es5-config');

module.exports = (config) => {
  const baseConfig = createBaseConfig(config);

  config.set({
    ...baseConfig,

    files: [
      ...baseConfig.files,
      config.grep ? config.grep : 'test/**/*.test.js',
    ],
  });
};
```

Then run your tests with: `karma start karma.es5.config.js`
