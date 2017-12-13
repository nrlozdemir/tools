# gen-typescript-declarations

[![Build Status](https://travis-ci.org/Polymer/gen-typescript-declarations.svg?branch=master)](https://travis-ci.org/Polymer/gen-typescript-declarations)
[![NPM version](https://img.shields.io/npm/v/@polymer/gen-typescript-declarations.svg)](https://www.npmjs.com/package/@polymer/gen-typescript-declarations)

A library which generates TypeScript declarations for Polymer and custom
elements.

## How do I use the typings?

### Polymer

Typings for the Polymer 2.0 library will be included in future releases on
Bower in the `types/` directory ([pending
PR](https://github.com/Polymer/polymer/pull/4928)).

Install Polymer normally from Bower, and add a [triple-slash
directive](https://www.typescriptlang.org/docs/handbook/triple-slash-directives.html)
anywhere in your TypeScript project for the typings you require. Each HTML
import from Polymer has a corresponding typings file. For example, if you
depend on `polymer-element.html`:

```ts
/// <reference path="./bower_components/polymer/types/polymer-element.d.ts" />`

class MyElement extends Polymer.Element {
  ...
}
```

You may also be interested in the [Polymer
decorators](https://github.com/Polymer/polymer-decorators).

## How do I generate new typings?

You can run this package from the command line with `gen-tsd`, or as a library
with the `generateDeclarations` function.

It is recommended to integrate typings generation as part of your build/release
process:

```sh
$ npm install --save-dev @polymer/gen-typescript-declarations
```

Add a `gen-tsd` script to your `package.json`:

```js
{
  ...
  "scripts": {
    "gen-tsd": "gen-tsd"
  }
}
```

If you're using Bower, ensure you run `npm run gen-tsd` to generate the latest
typings and commit them to your repository before tagging each release.

If you're using NPM, you can instead add this script to the NPM `prepack`
script to generate and include typings in your NPM package every time you
publish.

## Config options

By default the `gen-tsd` command will read a file called `gen-tsd.json` in
your root directory. It has the following options:

* **`exclude`**`: string[]`

  Skip source files whose paths match any of these glob patterns. If
  `undefined`, defaults to excluding directories ending in "test" or "demo".

* **`removeReferences`**`: string[]`

  Remove any triple-slash references to these files, specified as paths
  relative to the analysis root directory.

* **`addReferences`**`: {[filepath: string]: string[]}`

  Additional files to insert as triple-slash reference statements. Given the
  map `a: b[]`, a will get an additional reference statement for each file
  path in b. All paths are relative to the analysis root directory.

## Using as a module

You can also use this package as a module:

```js
import {generateDeclarations} from 'gen-typescript-declarations';

const config = {
  "exclude": [
    "test/",
  ],
  "removeReferences": [
    "../shadycss/apply-shim.d.ts",
  ],
  "addReferences": {
    "lib/utils/boot.d.ts": [
      "extra-types.d.ts"
    ]
  }
}

// A map of d.ts file paths to file contents.
const declarations = await generateDeclarations('/my/root/dir', config);
```

## FAQ

### Why are some typings missing?
This library is based on [Polymer
Analyzer](https://github.com/Polymer/polymer-analyzer) which has limitations in
its static analysis. For certain patterns, Analyzer relies on additional JSDoc
annotations.

### Can I augment the generated typings with hand-written ones?
Yes, see the `addReferences` config option above.
