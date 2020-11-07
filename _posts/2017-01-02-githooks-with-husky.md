---
layout: default
author: Jacob Lorenzen
---
# Git hooks with Husky

When doing javascript development it is common to have linting and test tasks in your package.json file.

It is easy to forget to run these common tasks before pushing code and this can result in a broken build (if using continues integration) or the next developer will see the issues when pulling down the latest code.

A way to work around this problem is to use git hooks that will allow you to hook into the git workflow to run tasks. Git hooks are not easy to share across a development team as git hooks are located in the .git/hooks folder.

## Husky to the rescue

Luckily a npm package called Husky can help solve this issue. Husky describes itself as “Git hooks made easy”. After using it I must agree that it does indeed do just that!

Installing Husky is as simple as an npm install from the terminal in your project.

`npm install husky --save-dev`

The hooks can be inserted into yourpackage.json file in the scripts section using a pre-defined naming convention.

In the below example I’m setting up pre-push and pre-commit hooks to run my unit tests.

```json
{
  "name": "husky-example",
  "version": "1.0.0",
  "description": "husky example",
  "main": "src/index.js",
  "scripts": {
    "test": "mocha",
    "precommit": "npm test",
    "prepush": "npm test"
  },
  "author": "Jacob Lorenzen",
  "license": "MIT",
  "devDependencies": {
    "expect": "^1.20.2",
    "husky": "^0.12.0",
    "mocha": "^3.2.0"
  }
}
```

This setup can be shared across a development team and should help eliminate trivial linting issues or broken tests being pushed to the remote server.

## Running multiple tasks with npm-run-all

If there is a need to run multiple tasks you can use the package npm-run-all to combine all the tasks into one task and reference that task in your Husky git hook.

In the below example I’m combining thetestand linttask into a validate task that my Husky git hooks can reference.

```json
{
  "name": "husky-example",
  "version": "1.0.0",
  "description": "husky example",
  "main": "src/index.js",
  "scripts": {
    "test": "mocha",
    "lint": "standard",
    "validate": "npm-run-all --parallel test lint",
    "precommit": "npm run validate",
    "prepush": "npm run validate"
  },
  "author": "Jacob Lorenzen",
  "license": "MIT",
  "devDependencies": {
    "expect": "^1.20.2",
    "husky": "^0.12.0",
    "mocha": "^3.2.0",
    "npm-run-all": "^4.0.0",
    "standard": "^8.6.0"
  },
  "standard": {
    "globals": [
      "describe",
      "it"
    ]
  }
}
```

If for some reason you want to bypass this mechanism your can add the--no-verifyflag before doing your commit.

`git commit -m "bypass git hooks" --no-verify`

[Husky](https://www.npmjs.com/package/husky) and [npm-run-all](https://www.npmjs.com/package/npm-run-all) is definitely two libraries that I will keep using going forward as they help solve a problem I’ve experience many times myself.