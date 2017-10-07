# Creating an npm package

## Table of Contents

1. [Creating the repository](#creating-the-repository)
2. [Initializing the npm package](#initializing-the-npm-package)

## 1. Creating the repository

1. Create a folder with the name of your package
2. Initialize inside this folder an empty git repository
3. Create a repository in github
4. Add this github repo as the origin remote

```bash
mkdir my-new-package-name
cd my-new-package-name
git init
git remote add origin https://github.com/your-user-name/your-repo-name
```

After the repo is created we want to make the first commit. For this we'll add a `README.md` file. After this file is created we add it to the staging area to create the first commit. After creating the commit we want to push this to the remote. Because it's the first time pushing you need to set up the `upstream`, so when writing the `push` command we'll add a `-u` flag. :smile:

```bash
echo "#my-new-package-name" >> README.md
git add README.md
git commit -m "My first commit"
git push -u origin master
```

Now we have a fully set up git repo that's connected to a remote.

## 2. Initializing the npm package

If you type `npm init` you will be asked a series of questions with defaults to set up your npm package.

```bash
npm init
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

See `npm help json` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg>` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
####### You can add your answer or press enter to accept the default
package name: (my-new-package-name)
version: (1.0.0)
description: A template for creating npm packages
entry point: (index.js)
test command:
git repository: (https://github.com/pilarArr/my-npm-template.git)
keywords:
author:
license: (ISC) MIT
About to write to paht/to/package/my-npm-template/package.json:

{
  "name": "my-new-package-name",
  "version": "1.0.0",
  "description": "A template for creating npm packages",
  "main": "build/index.js",
  "scripts": {
    "test": "jest"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/pilarArr/my-npm-template.git"
  },
  "author": "pilarArr",
  "license": "MIT",
  "bugs": {
    "url": "https://github.com/pilarArr/my-npm-template/issues"
  },
  "homepage": "https://github.com/pilarArr/my-npm-template#readme"
}

Is this ok? (yes)
```

Now the `package.json` has been created in out folder with all the info we have introduced.

## 3. Set up babel for compilation

We have to install the `babel` packages so that we can write in ES6. We save this as dev dependencies

```
npm install --save-dev babel-cli babel-preset-env
```

After these are installed we want to add the `build` script to our `package.json`. With this script we tell babel that we want to compile all the files in the `src` folder to the `build` folder and also to ignore all the test files.

```json
"scripts": {
  "build": "babel src -d build --ignore '**/*.test.js'",
  "test": "jest"
},
```

The outcome we want is to make different files for all our functions and import them all to an `index.js` file that then compiles them all. Right now we have to define all our funcions in the index.js file.
( **WIP** (:wrench:   ) looks like webpacks can solve this)

We also need to add the babel configuration, this can be done by adding a `.babelrc` file to the root or by adding the configuration as an item to the `package.json` file. We'll do the later, so to our `package.json` file we'll add the following.

```json
"babel": {
    "presets": [
      "env"
    ]
  },
```

## 4. Testing

For the test environment we will use the `jest` test suite.
```
npm install --save-dev jest
```

For the script we have to add it to the `package.json` file like this:

```
"scripts": {
    "test": "jest",
  },
```

To make tests we create a new file `index.test.js`, and this would be a sample test:

```javascript
import { myfunction } from './index';

test('this is a sample test', () => (
  expect(myfunction(something)).toBe(somethingElse)
));
```

With this we've created our first tests, for more info on all the test you can do visit [`jest` website](https://facebook.github.io/jest/). With this we have the test enviroment set up, to run the tests we now write in the terminal and all the tests run, `jest` looks for files `*.test.js`
```
npm test
```

## 5. Watch for changes

We'll add two new script to watch for changes. We have to run this scripts on separate terminals and just leave them running as we write code.

```json
"scripts": {
    ...
    "dev": "babel src -d build --ignore '**/*.test.js' --watch",
    "test:watch": "jest --watch"
  },
```

1. One script will build the source code as we write it
    ```
    npm run dev
    ```
2. The other script will run the test we make as we write new code
    ```
    npm run test:watch
    ```

## 6. Test npm packages locally in another project using `npm link`

Before publishing the changes we might want to test our npm package in another project to see if it works. To do this we first open a terminal in the folder that contains our npm package and run `npm link`.

We now create another folder and initialize a default npm package with `npm init -y`. Now we run `npm link my-new-package-name`, with this we can now use the npm package in this new project as if we has run an `npm install`.

So in the new project we create a file named `index.js` where we will try this new package. For example:

```javascript
import myfunction from 'my-new-package-name';

const thisExample = myfunction(something);
console.log(thisExample);
```

Now if we run `node index.js` for this project in the terminal we can try using our new npm package in another project to see how it goes. So if you also run `npm run dev` for your npm package while you try it you can write code and try it at the smae time.

## 7. Adding the README

Add installation instructions and a simple example.

## 8. Linting

```
npm install --save-dev eslint eslint-config-airbnb-base eslint-plugin-import
```

Here we'll also add the configuration to the `package.json` file.

```json
"eslintConfig": {
    "extends": "airbnb-base",
    "env": {
      "node": true
    },
    "globals": {
      "expect": false
    },
    "rules": {
      "indent": [
        "error",
        2
      ],
      "no-tabs": [
        "error"
      ],
      "no-mixed-spaces-and-tabs": [
        "error"
      ],
      "import/no-unresolved": 0,
      "import/no-extraneous-dependencies": 0,
      "import/extensions": 0,
      "no-underscore-dangle": [
        "error"
      ],
      "class-methods-use-this": 0
    }
  },
```

## 9. Publishing you package

We will first add a `prepublish` script that runs our `build` script; this will ensure the built folder gets added to npm when published. We will also add a `.npmignore` so that only our built files get installed. We need to run` npm adduser` to log in to an npm account. We can then run `npm publish` to publish our package and view it on npm.

1. We add this script to our `package.json`, this command will run before publishig everytime.
- `"prepublish": "npm run build"`

2. Now we add the `.npmignore` file so that only the build folder gets published.

3. Next we log in to npm with `npm adduser`

4. Finally we run `npm publish`

## 10. Updating a published package with `np`

- Add a new script to `package.json`.
    ```json
    "release": "np"
    ```
- Now change something in the package
- When you run `np` it's going to ask you first if the update is patch, minor, ...
- Then it will make some checks, like making sure you have a clean working tree on git, check branches, etc...

And all done, you now have a working npm package.
