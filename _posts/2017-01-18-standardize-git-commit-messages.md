---
layout: post
title: Standardize Git commit messages
author: Jacob Lorenzen
tags: [npm, node]
---
# Standardize Git commit messages

Working in a team on a shared code base there is benefits in having standardized commit messages. You can use the commit messages to automate some tasks based on a predefined template for the commit messages that can be parsed by other tools.

One of the maybe not so obvious benefits is that it can help with automating semantic versioning of your software.

In javascript one package that does standardizing commit messages is the package commitizen. Commitizen allows you to hook in your own adapter to customize commit messages to your preferred style.

Predefined adapters are available such as cz-conventional-changelog. It has a template that matches what the Angular team has been using.

## Setup

Commitizen is installed using npm package manager.

`npm install commitizen --save-dev`

The next step is to use an adapter. I’m here using `cz-conventional-changelog`.

`npm install cz-conventional-changelog --save-dev`

Last you need to to let commitizen know about your adapter. This is done in `package.json` where you put in the config section for commitizen and point the path `tocz-conventional-changelog` adapter.

```json
{
  "config": {
    "commitizen": {
      "path": "cz-conventional-changelog"
    }
}
```

Typing `npm run commit` will prompt you to fill out an wizard that is presented to you.

![commitizen](/assets/images/commitizen.png)

The template is very easy to follow and helps create git commit messsages in a standard form. The drawback is that you have to run this using `npm run commit` which is not very intuitive.

Another drawback of the current setup is that it does not enforce your team to follow this commit style. There is currently no validation that the commit style is being followed. A better way would be to use git hooks to help enforce this rule. I have wrote about using [Husky](/2017-01-02-githooks-with-husky) to do this in a previous post.

In this case I will use the package `validate-commit-msg` to secure that commit messages are validated before being pushed to the server. I do so by using Husky and setup a git hook to validate the commit message using the validate-commit-msg package.

```json
{
  "scripts": {
     "commitmsg": "validate-commit-msg"
  }
}
```

In the above `package.json` I’ve setup a git hook that fires when doing a commit with a message.

## Semantic versioning

So how does this tie into semantic versioning? Now that we have a standardized commit message we can use it to our advantage by utilizing the package semantic-release.

The semantic-release package will help with creating the next version number for your node package. It does so by looking at previous commit messages since last release to figure out if it should increase the major, minor or patch number.

## Setting up semantic-release

For semantic-release to work you need to remove your version in `package.json`. I prefer to keep a generic version that semantic-release can override — otherwise npm complains. You will also need to specify a npm script that `semantic-release` runs.

```json
{
  "version": "0.0.0-semantic-release",
  "scripts": {
    "semantic-release": "semantic-release pre && npm publish && semantic-release post"
  }
}
```

In this case I’m running `npm publish` after the new version has been incremented.

For `npm publish` to work it needs a token to your npm account. This can all be generated using [semantic-release-cli](https://www.npmjs.com/package/semantic-release-cli) which can setup tokens for the different services — including Travis CI, and Github. Installing `semantic-release-cli` must be done globally.

`npm install -g semantic-release-cli`

After installing it you can run `semantic-release-cli setup` which will guide you through setting up access to your services.

By default it support Travis CI but it should also work with other CI’s per [documentation](https://www.npmjs.com/package/semantic-release-cli#other-ci-servers).

## Conclusion

The benefits of standardized commit message is that it allow you to hook in other tools that can help you automate mundane repetative tasks.

I hope this post can help inspire you to use some of these packages in your daily development life!