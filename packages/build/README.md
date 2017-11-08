# @loopback/build

This module contains a set of common scripts and default configurations to build LoopBack 4 or other TypeScript modules, including:

- lb-tsc: Use [`tsc`](https://www.typescriptlang.org/docs/handbook/compiler-options.html) to compile typescript files
- lb-dist: Detect the correct distribution target: `dist` => ES2017, `dist6` => ES2015
- lb-tslint: Run [`tslint`](https://github.com/palantir/tslint)
- lb-prettier: Run [`prettier`](https://github.com/prettier/prettier)
- lb-nyc: Run [`nyc`](https://github.com/istanbuljs/nyc)

These scripts first try to locate the CLI from target project dependencies and fall back to bundled ones in `@loopback/build`.

# Usage

To use `@loopback/build` for your package:

1. Run the following command to add `@loopback/build` as a dev dependency.

`npm i @loopback/build --save-dev`

2. Configure your project package.json as follows:
```json
"scripts": {
    "build": "npm run build:dist && npm run build:dist6",
    "build:current": "lb-tsc",
    "build:dist": "lb-tsc es2017",
    "build:dist6": "lb-tsc es2015",
    "build:apidocs": "lb-apidocs",
    "tslint": "lb-tslint",
    "tslint:fix": "npm run tslint -- --fix",
    "prettier:cli": "lb-prettier \"**/*.ts\"",
    "prettier:check": "npm run prettier:cli -- -l",
    "prettier:fix": "npm run prettier:cli -- --write",
    "lint": "npm run prettier:check && npm run tslint",
    "lint:fix": "npm run prettier:fix && npm run tslint:fix",
    "clean": "lb-clean loopback-grpc*.tgz dist*",
    "prepare": "npm run build && npm run build:apidocs",
    "pretest": "npm run build:current",
    "acceptance": "lb-dist mocha --opts ../../test/mocha.opts 'DIST/test/acceptance/**/*.js'",
    "integration": "lb-dist mocha --opts ../../test/mocha.opts 'DIST/test/integration/**/*.js'",
    "test": "lb-dist mocha --opts ../../test/mocha.opts 'DIST/test/unit/**/*.js' 'DIST/test/integration/**/*.js' 'DIST/test/acceptance/**/*.js'",
    "unit": "lb-dist mocha --opts ../../test/mocha.opts 'DIST/test/unit/**/*.js'",
    "verify": "npm pack && tar xf loopback-grpc*.tgz && tree package && npm run clean"
  },
```

Now you run the scripts, such as:

- `npm run build` - Compile TypeScript files
- `npm test` - Run all mocha tests
- `npm run lint` - Run `tslint` and `prettier` on source files

3. Override default configurations in your project

- lb-tsc
  
  By default, `lb-tsc` searches your project's root directory for `tsconfig.build.json` then `tsconfig.json`. If neither of them exists, a `tsconfig.json` will be created to extend from `./node_modules/@loopback/build/config/tsconfig.common.json`.

  To customize the configuration:

  - Create `tsconfig.build.json` or `tsconfig.json` in your project's root directory
    ```json
    {
      "$schema": "http://json.schemastore.org/tsconfig",
      "extends": "./node_modules/@loopback/build/config/tsconfig.common.json",
      "compilerOptions": {
        "rootDir": "."
      },
      "include": ["src", "test"]
    }
    ```

  - Set options explicity for the script
    ```
    lb-tsc -p tsconfig.json --target es2017 --outDir dist
    ```

    For more information, see https://www.typescriptlang.org/docs/handbook/compiler-options.html.

- lb-tslint

  By default, `lb-tslint` searches your project's root directory for `tslint.build.json` then `tslint.json`. If neither of them exists, it falls back to `./node_modules/@loopback/build/config/tslint.common.json`.

  `lb-tslint` also depends on `tsconfig.build.json` or `tsconfig.json` to reference the project.

  To customize the configuration:

  - Create `tslint.build.json` in your project's root directory, for example:
    ```json
    {
      "$schema": "http://json.schemastore.org/tslint",
      "extends": [
        "./node_modules/@loopback/build/config/tslint.common.json"
      ],
      // This configuration files enabled rules which require type checking
      // and therefore cannot be run by Visual Studio Code TSLint extension
      // See https://github.com/Microsoft/vscode-tslint/issues/70
      "rules": {
        // These rules find errors related to TypeScript features.


        // These rules catch common errors in JS programming or otherwise
        // confusing constructs that are prone to producing bugs.

        "await-promise": true,
        "no-floating-promises": true,
        "no-void-expression": [true, "ignore-arrow-function-shorthand"]
      }
    }
    ```

  - Set options explicitly for the script
    ```
    lb-tslint -c tslint.json -p tsconfig.json
    ```

    For more information, see https://palantir.github.io/tslint/usage/cli/.

4. Run builds

```
npm run build
```
# License

MIT