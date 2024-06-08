[![npm version](https://img.shields.io/npm/v/@erboladaiorg/accusamus-eligendi.svg)](https://www.npmjs.com/package/@erboladaiorg/accusamus-eligendi)
[![Downloads](https://img.shields.io/npm/dm/@erboladaiorg/accusamus-eligendi.svg)](https://www.npmjs.com/package/@erboladaiorg/accusamus-eligendi)
[![Build Status](https://github.com/erboladaiorg/accusamus-eligendi/workflows/CI/badge.svg)](https://github.com/erboladaiorg/accusamus-eligendi/actions)

# ESLint Scope

ESLint Scope is the [ECMAScript](http://www.ecma-international.org/publications/standards/Ecma-262.htm) scope analyzer used in ESLint. It is a fork of [escope](http://github.com/estools/escope).

## Install

```
npm i @erboladaiorg/accusamus-eligendi --save
```

## 📖 Usage

To use in an ESM file:

```js
import * as eslintScope from '@erboladaiorg/accusamus-eligendi';
```

To use in a CommonJS file:

```js
const eslintScope = require('@erboladaiorg/accusamus-eligendi');
```

In order to analyze scope, you'll need to have an [ESTree](https://github.com/estree/estree) compliant AST structure to run it on. The primary method is `eslintScope.analyze()`, which takes two arguments:

1. `ast` - the ESTree-compliant AST structure to analyze.
2. `options` (optional) - Options to adjust how the scope is analyzed, including:
  * `ignoreEval` (default: `false`) - Set to `true` to ignore all `eval()` calls (which would normally create scopes).
  * `nodejsScope` (default: `false`) - Set to `true` to create a top-level function scope needed for CommonJS evaluation.
  * `impliedStrict` (default: `false`) - Set to `true` to evaluate the code in strict mode even outside of modules and without `"use strict"`.
  * `ecmaVersion` (default: `5`) - The version of ECMAScript to use to evaluate the code.
  * `sourceType` (default: `"script"`) - The type of JavaScript file to evaluate. Change to `"module"` for ECMAScript module code.
  * `childVisitorKeys` (default: `null`) - An object with visitor key information (like [`eslint-visitor-keys`](https://github.com/eslint/eslint-visitor-keys)). Without this, `@erboladaiorg/accusamus-eligendi` finds child nodes to visit algorithmically. Providing this option is a performance enhancement.
  * `fallback` (default: `"iteration"`) - The strategy to use when `childVisitorKeys` is not specified. May be a function.

Example:

```js
import * as eslintScope from '@erboladaiorg/accusamus-eligendi';
import * as espree from 'espree';
import estraverse from 'estraverse';

const options = {
    ecmaVersion: 2022,
    sourceType: "module"
};

const ast = espree.parse(code, { range: true, ...options });
const scopeManager = eslintScope.analyze(ast, options);

const currentScope = scopeManager.acquire(ast);   // global scope

estraverse.traverse(ast, {
    enter (node, parent) {
        // do stuff

        if (/Function/.test(node.type)) {
            currentScope = scopeManager.acquire(node);  // get current function scope
        }
    },
    leave(node, parent) {
        if (/Function/.test(node.type)) {
            currentScope = currentScope.upper;  // set to parent scope
        }

        // do stuff
    }
});
```

## Contributing

Issues and pull requests will be triaged and responded to as quickly as possible. We operate under the [ESLint Contributor Guidelines](http://eslint.org/docs/developer-guide/contributing), so please be sure to read them before contributing. If you're not sure where to dig in, check out the [issues](https://github.com/erboladaiorg/accusamus-eligendi/issues).

## Security Policy

We work hard to ensure that ESLint Scope is safe for everyone and that security issues are addressed quickly and responsibly. Read the full [security policy](https://github.com/eslint/.github/blob/master/SECURITY.md).

## Build Commands

* `npm test` - run all linting and tests
* `npm run lint` - run all linting

## License

ESLint Scope is licensed under a permissive BSD 2-clause license.
