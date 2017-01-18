# Medea

!["The Lion Strangler", carving of Heracles and the Nemean Lion](https://raw.githubusercontent.com/HowlingEverett/nemea/master/images/nemeanlion.jpg)

Kill the beast lurking in your monorepo.

## What Is It For?

Javascript monorepos are growing in popularity, and for good reason. The Node.js
philosophy of building an application or library as a composite of many small 
modules is fine, but the actual method of working that way gets mired pretty
quickly into a mess of `npm install` and manual rebuilds, especially if you're
doing any transpilation in your small modules (and I bet you are).

[Lerna](https://github.com/lerna/lerna) solves the small-module-monorepo problem
via symlinks (similar to `npm link`). For quite a few use cases, this is fine.
If you're running a development server out of your monorepo, however,
particularly a hot-reloading development server, you'll have run afoul of the
beast. `webpack-dev-server` and other `express` variants can quickly slow to a
halt while trying to resolve circular symlinks, which any non-trivial monorepo
will end up creating.

nemea is a development utility that gives you the local-dev benefits of a
monorepo without symlinks. Instead, it watches your packages, rebuilds them if
required, and moves their distribution version into your `node_modules`. That's
all it does.

## Install

`npm install @howlingeverett/nemea --save-dev`

You can install it globally for convenience, if you like. If running locally,
we recommend creating a script in your `package.json` to simplify the path
to the binary

```js
{
  //... package.json
  "scripts": {
    // ... other scripts
    "dev": "nemea"
  }
}
```

## Configure and Run

Run nemea using its binary:

```
nemea
```

nemea looks for a file called `nemea.config.json` in the root of your repo. If
you want to call the file something else, call `nemea` with the `--config` flag:

```
nemea --config path/to/myconfig.json
```

At the moment, the config file requires a single key: a file, directory, `glob`,
or array of the former.

```js
{
  "packages": [
    "packages/**/*.js"
  ],
  "excludePaths": [
    "packages/node_modules/**/*"
  ]
}
```

By default, nemea runs in watch mode, where it watches changes to files matched
by the `packages` config key, and rebuilds and redistributes changed packages
into the monorepo's top-level `node_modules` directory.

It's also possible to run a complete repackage once. This is useful for building
your monorepo in a deployment environment in the case where you don't want to
publish your packages to `npm` or a private registry.

```
" Do a complete redeploy, but don't run the file watching server
nemea --deploy
```