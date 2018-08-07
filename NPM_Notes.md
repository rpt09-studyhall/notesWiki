## What's npm?

>"npm is the package manager for JavaScript and the world's largest software registry, with approximately 3 billion downloads per week." [Aug. 2018]

Apparently, [these are the docs you're looking for](https://docs.npmjs.com/).

npm consists of three distinct components:

* the website: discover packages, set up profiles, and manage other aspects of your npm experience.
* the Command Line Interface ([CLI](https://docs.npmjs.com/cli/npm)) runs from a terminal.
* the [registry](https://docs.npmjs.com/misc/registry) is a large public database of JavaScript software and the meta-information surrounding it.

## Key Commands

Just a few here, the big list is [in the docs](https://docs.npmjs.com/).

### `npm init`

A step-by-step tool to build out the scaffolding for your project. It will prompt you for input for a few aspects of the project. Creates `package.json`. <https://docs.npmjs.com/cli/init> Generating a `package.json` with `npm init` doesn't really do anything, other than create a `package.json` file.

`npm init --yes` skips the prompts and gives you a default `package.json` quick-like.

### `npm install`

To [install a package](https://docs.npmjs.com/getting-started/installing-npm-packages-locally), or all packages listed as `dependencies` or `devDependencies` in your `package.json` <https://docs.npmjs.com/cli/install>.

`npm i` is an alias for `npm install`, so same same.

`npm install <package> --save` installs and adds the package (module) as a dependency of your project in the `dependencies` section of your `package.json`.

`npm install <package> --save-dev` installs and adds the package (module) as a development dependency of your project in the `devDependencies` section of your `package.json`.

`dependencies` are for production — whatever's needed for your project. `devDependencies` are used in development of your app — the packages (modules) that you use to build it, but don't need to use when it's running. Generally things like testing tools, a local server to speed up your development (like `live-server`), and more.

`npm install <package> -g` installs the package "globally" in your machine so you can use it across multiple projects, as opposed to just a local (in your project folder) install.

What's a module vs a package? See [Understanding Packages and Modules](https://docs.npmjs.com/getting-started/packages).

### `npm list`

`npm list -g --depth=0` shows your globally installed (top-level) packages, without all of their dependencies. Also `npm ls -g --depth=0` is same same.

### `npm update`

`npm update -g` [updates](https://docs.npmjs.com/cli/update) your global packages.

## Package.json

The `package.json` file is core to the Node.js ecosystem and is a basic part of understanding and working with Node.js, npm, and modern ScravaJipt. The package.json file is used as a manifest, storing information about applications, modules, packages, etc.

![](https://dzwonsemrish7.cloudfront.net/items/390d1H0M1S2Q2R2N381k/scravajipt.jpg)

[Working with `package.json`](https://docs.npmjs.com/getting-started/using-a-package.json) is helpful and [the specifics](https://docs.npmjs.com/files/package.json) are too.

### The `scripts` Property

The `scripts` property of a `package.json` file is simple, but can be handy. The `scripts` property has a set of entries; the key for each is a script name, and the corresponding value is a user-defined command to be executed. Scripts are frequently used for testing, building, and streamlining of the commands for working with a module. Like, say 

```
"scripts": {
    "test": "open SpecRunner.html",
    "start": "live-server"
  },
```

for example. Where `npm start` or `npm run start` fires up that hot reload action of `live-server`. [Moar deets here](https://docs.npmjs.com/misc/scripts).

## Tips

## Package Home Page

`npm home <package-name>` opens the home page for the package.

## Package Github Repo

`npm repo <package-name>` opens the Github repo of the package.

## Check for Updates

`npm outdated` checks for [outdated packages](https://docs.npmjs.com/cli/outdated) that have updates.

## Add npm Autocomplete

For bash in your terminal: `npm completion >> ~/.bashrc` or `npm completion >> ~/.bash_profile`, if you do dat.

For zsh in your terminal: `npm completion >> ~/.zshrc` Boomsauce.

## Automate `npm init` a Bit

If you `npm init` a bunch, you might

```
npm config set init.author.name "Jane Doe"
npm config set init.author.email "jane@doeknows.io"
npm config set init.author.url "http://jane.allthedoes.io"
npm config set init.license "Apache-2.0"
npm config set init.version "0.0.1"
```

