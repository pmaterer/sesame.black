---
title: "Typescript and p5.js"
date: 2021-06-22T11:17:02-05:00
draft: false
tags:
- typescript
- p5.js
- node
---

I've been experimenting with [p5.js](https://p5js.org/) while learning [Typescript](https://www.typescriptlang.org/). Coming from Go, the Node ecosystem feels complex, with copious config files and third-party modules. I created a [GitHub template](https://github.com/pmaterer/typescript-template), which I use to start new projects.



## Tooling

My [template](https://github.com/pmaterer/typescript-template) utilizes a few tools:

* [Webpack](https://webpack.js.org/) for bundling.
* [Jest](https://jestjs.io/) for testing.
* [ESLint](https://eslint.org/) for linting.
* [Prettier](https://prettier.io/) for code formatting.

These tools then require some setup to be useful. Everything is rather straightforward. One thing of note is the Jest config. I'm using [Sass](https://sass-lang.com/) to write CSS. Webpack is using the [`style-loader`](https://webpack.js.org/loaders/style-loader/), which lets you `import` Sass files in Typescript. Jest will freak about this, so we need to tell it how to handle `sass` files, using the [`moduleNameMapper`](https://jestjs.io/docs/webpack#handling-static-assets) setting in `jest.config.js`:

```js
...
moduleNameMapper: {
  '\\.(jpg|jpeg|png|gif|eot|otf|webp|svg|ttf|woff|woff2|mp4|webm|wav|mp3|m4a|aac|oga)$': '<rootDir>/__mocks__/fileMock.js',
  '\\.(css|less)$': '<rootDir>/__mocks__/styleMock.js',
},
...
```

This mocks out all files that match the regex, which can be found in the [`__mocks__`](https://github.com/pmaterer/typescript-template/tree/main/__mocks__) directory.

## New Project

Now we can create a new repo off the [template](https://github.com/pmaterer/typescript-template). We need to change a few variables to match our new repo's name.

* `package.json`: Change: `.name`, `.description`, `.repository`, `.author`.
* `webpack.config.ts`: Change `config.plugins.HtmlWebpackPlugin.title`. This sets our HTML's title.

We can also delete everything in `index.ts` and `index.test.ts`.

Now run `yarn install` to get all the dependencies.

## Setup p5.js

First install p5.js: `yarn add p5`. Next install the p5.js type definitions for Typescript: `yarn add -D @types/p5`.

p5.js can run in ["instanced" or "global" mode](https://github.com/processing/p5.js/wiki/p5.js-overview#instantiation--namespace). By default p5.js functions are in the global namespace, polluting it. So we will use "instanced" mode, where p5.js functions are bound to a single instance.

In `index.ts`:

```typescript
import p5 from 'p5';

const sketch = (p: p5) => {
  p.setup = () => {
    const canvas = p.createCanvas(600, 600);
    canvas.parent("sketch");
  };

  p.draw = () => {
    p.background(0);
    p.fill(255);
    p.rect(100, 100, 50, 50);
  };
};

new p5(sketch);
```

That should do it!

### HTML

In the `p.setup` function I specify the `canvas` parent. This isn't necessary, but I like to have control over where the p5.js sketch is placed in the HTML. I have a `div#sketch` in my HTML which I can now put/nest wherever.
