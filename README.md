# How to publish npm packages

Make sure you have Node installed on your machine (this also gives you `npm` out of the box). You can install Node from [here](https://nodejs.org/en/download/). If you would like to use `yarn` instead of `npm`, you can install it from [here](https://yarnpkg.com/en/docs/install#debian-stable).

### Creating the project directory

The first step is to create the project directory which you aim to publish. This step requires you to have yarn or npm installed.

```
mkdir project-directory-name
cd project-directory-name
yarn init
```

The CLI wizard will guide you through a series of steps to generate your `package.json` configuration. Alternatively you can use `yarn init -y` flag to get a default configuration.

### The all important package.json

After you initialize your npm package, you will have a package.json file which should look something like this.

```json
{
  "name": "npm-packager",
  "version": "1.0.0",
  "description": "Repo to standardize my way of publishing npm packages",
  "main": "index.js",
  "repository": "https://github.com/ashwanth1109/npm-packager",
  "author": "Ashwanth A R",
  "license": "MIT"
}
```

### Creating your source code

The next step is the meat and potatoes of what you want to achieve. Writing the source code for your package.

Typically source code is written in a directory called `src` in order to separate it from the rest of the repo. This also makes it easier to bundle all your code into a lib or dist folder after you've pre-processed your code for production use.

In this example we write a simple function in javascript which adds two numbers since our focus is on publishing the package.

```js
const addTwoNumbers = (a, b) => a + b;

export default addTwoNumbers;
```

There we have it. Our source code for the package. We're almost ready to publish it but not quite.

### Installing babel

The code we have written above uses arrow functions which does not work in older browsers. You can see the current status of support [here](https://caniuse.com/#feat=arrow-functions). In order to make our code work across all environments, we use a compiler (called Babel) to compile our code and package it.

We install the dependancies (as dev dependancies) for Babel using the following command. These are dev dependancies because our packaged code does not depend on

```
yarn add -D @babel/core @babel/cli @babel/node @babel/preset-env
```

Your `package.json` should now have these devDependencies:

```json
{
  "devDependencies": {
    "@babel/cli": "^7.6.0",
    "@babel/core": "^7.6.0",
    "@babel/node": "^7.6.0",
    "@babel/preset-env": "^7.6.0"
  }
}
```

Babel is a JavaScript compiler that converts ES6+ code into a backwards compatible version of JavaScript in old browsers or environments. The Babel CLI (@babel/cli) is a command line tool that provides your command line with the ability to run the babel core API (@babel/core) on your source code. Babel Node (@babel/node) provides you with the ability to run ES6+ code on Node.js.

With just these tools you will find that there is a lot more work that you need to do in order to micromanage the syntax transforms that are needed by your target environment. To simplify this, we add a preset (@babel/preset-env) which does this work for you in a concise manner.

### Building our compiled code

Before we build our babel transpiled code, we need to tell our babel configuration to use the preset-env that we installed. We do this by creating a `.babelrc` file as below:

```json
{
  "presets": ["@babel/preset-env"]
}
```

Finally, we are ready to build our production-ready source code using babel. We need to add a build script in our `package.json` as follows to do this.

```json
{
  "scripts": {
    "build": "babel src --out-dir lib"
  }
}
```

We can run our script using the following command.

```
yarn build
```

You should notice a `lib` folder that contains our babel transpiled code which will look something like this.

```js
"use strict";

Object.defineProperty(exports, "__esModule", {
  value: true
});
exports["default"] = void 0;

var addTwoNumbers = function addTwoNumbers(a, b) {
  return a + b;
};

var _default = addTwoNumbers;
exports["default"] = _default;
```

You need to transpile (and build) your code every time before you publish it to npm. Adding a `prepare` script will automate this for you, so that you can directly just publish your package without running build every time.

```json
{
  "scripts": {
    "build": "babel src --out-dir lib",
    "prepare": "yarn build"
  }
}
```

### Publishing your npm package

Before you publish your npm package, you want to tell npm to publish your package only with certain files and folders (whitelist). You can choose instead to tell npm to avoid certain files/folders (blacklist) using the `.npmignore` file however I find blacklist more troublesome, so we opt to whitelist. You can read more about this [here](https://docs.npmjs.com/misc/developers#keeping-files-out-of-your-package) and [here](https://medium.com/@jdxcode/for-the-love-of-god-dont-use-npmignore-f93c08909d8d).

We want npm to publish our `lib` folder. You can do so by including it in your files in the `package.json` file.

```json
{
  "files": ["/lib"]
}
```

It will always automatically include your `package.json`, `README`, `CHANGELOG` and `LICENSE` files, so you don't have to mention them. It is a good idea that you ensure all these files are present in your repo with the information that you want on them. The MIT license can be found [here](https://opensource.org/licenses/MIT). Your CHANGELOG.md can look something like this:

```
### 1.0.0 [7th September, 2019] FEATURE

- Initial Release: Function to add two numbers
```

We are now officially ready to publish.

You need to authenticate your npm account before publishing your package.

```
npm login
```

You can do so by entering the following command in your CLI. The wizard will ask you to enter your USERNAME, PASSWORD and EMAIL. Once you do that, any package you publish will be automatically associated with these credentials.

To publish, run:

```
npm publish
```
