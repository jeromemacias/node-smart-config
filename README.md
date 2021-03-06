# rob-config

[![NPM version](http://img.shields.io/npm/v/rob-config.svg)](https://www.npmjs.org/package/rob-config)
![node-current](https://img.shields.io/node/v/rob-config)
[![Build Status](https://travis-ci.org/jeromemacias/node-rob-config.svg?branch=master)](https://travis-ci.org/jeromemacias/node-rob-config)

Robust configuration module for nodejs, built on top of [`convict`](https://github.com/mozilla/node-convict).

## Installation

`npm install rob-config --save`

## Why another config package?

[`config`](https://github.com/lorenwest/node-config) is a very simple and powerfull config package but it lacks from schema and validation.

[`convict`](https://github.com/mozilla/node-convict) is not simple to use, but way more robust and overridable.

So I wanted something:

-   Easy to use like `config`
-   With schema and documentation like `convict`
-   With possibility to override with env variable or command line argument like `convict`
-   With a command to validate config for dev AND before deployment to production
-   With a command to see the builded config depends on env and default value (easier to debug)

Here it is and it's very easy to migrate from `config` or `convict`.

## Usage

Define a config schema, simple object.

See https://github.com/mozilla/node-convict#the-schema for documentation about schema definition.

For example: `config/schema.js`

```js
module.exports = {
    env: {
        doc: 'The applicaton environment.',
        format: ['production', 'staging', 'integration', 'development', 'test'],
        default: 'development',
        env: 'NODE_ENV',
    },
    api: {
        port: {
            doc: 'The API port',
            format: 'port',
            default: 3000,
        },
        timeout: {
            doc: 'The API timeout',
            format: 'nat',
            default: 60 * 1000, // 1 minutes
        },
    },
};
```

Then, define the first config file.

For example: `config/development.js`

```js
module.exports = {
    api: {
        port: 3001, // test with "nothing" value to see validation error
    },
};
```

You can, optionnaly, define a `config/formats.js` to add one or more custom format to convict.

See https://github.com/mozilla/node-convict#custom-format-checking for documentation about custom formats.

`convict.addFormats()` will be call under the hood.

For exemple: `config/formats.js`

```js
module.exports = {
    'float-percent': {
        validate: function (val) {
            if (val !== 0 && (!val || val > 1 || val < 0)) {
                throw new Error('must be a float between 0 and 1, inclusive');
            }
        },
        coerce: function (val) {
            return parseFloat(val, 10);
        },
    },
};
```

### Display your builded configuration

Run `./node_modules/.bin/rob-config show`:

![Display final configuration](example/screenshot/show.png?raw=true)

### Validate your configuration against schema

Run `./node_modules/.bin/rob-config validate`:

![Validate configuration ok](example/screenshot/validate-ok.png?raw=true)

In case of error:

![Validate configuration error](example/screenshot/validate-error.png?raw=true)

### Display the schema description

Run `./node_modules/.bin/rob-config describe`:

![Schema description](example/screenshot/describe.png?raw=true)

### Use it in your project

```js
const config = require('rob-config');

console.log(config.get('api.port'));
```

### Change config dir

You can set env variable `ROB_CONFIG_DIR` with the relative path of your project configuration directory.

## Versioning

To keep better organization of releases we follow the [Semantic Versioning 2.0.0](http://semver.org/) guidelines.

See [Releases](https://github.com/jeromemacias/node-rob-config/releases) for detailed changelog.

## License

[MIT License](/LICENSE) © [Jérôme Macias](https://github.com/jeromemacias)
