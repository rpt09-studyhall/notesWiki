React Notes (in babel-browser vs. babel-cli/webpack)
=============================

This is a readme on how to get a boilerplate babel / webpack / react setup since its (alot) and hard to remember everything from the ground up! Hope it helps.

Table of Contents
----
- [React with babel browser (no babel-cli / webpack)](#react-with-babel-browser)
- [Setup React with babel  (babel / webpack)](#react-with-webpack)
  - [Create boilerplate project folder](#boilerplate-project)
  - [Install React, Babel, Webpack](#npm-dependencies)
  - [NPM cleanup](#npm-cleanup)
  - [Create config files: .babelrc + webpack.config.js](#babelrc-webpack-config)
  - [Edit package.json scripts](#package-json-scripts)  
  - [Add a boilerplate React App](#simple-react-app)
  - [Build bundle via webpack / webpack-dev-server hot reloading](#run-webpack)
  - [Deployment / Better file setup / working with css + html files](#deployment)

<a id="react-with-babel-browser"></a>

React with babel browser (no babel-cli / webpack)
------------------------------

Often for very simple react non-production applications / testing, having a webpack / babel command line config can be overwhelming, especially when you're just starting out. There is an option for simple apps (again, this would be difficult with tons of external components..). React can be precompiled at browser runtime instead of using `babel-cli` by using babel's `browser.min.js` in-line cdn script. An example can be seen in this [Grocery Todo list](https://github.com/cjm771/rpt09-react-components/) todo app. 

### First we'll start with our html page

This is a basic html page that does not require any server to work. It can be open up in the `file://` protocol. Note below the `text/babel` in the script tag. This tells the browser to not execute as `javascript`, but `babel browser` will instead read this file and transpile into executing javasript.
``` html

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>React Playground</title>
  <!-- babel will compile your script @ runtime with this -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/babel-core/5.8.23/browser.min.js"></script>
  <!-- react dependencies -->
  <script src="https://fb.me/react-with-addons-0.14.6.js"></script>
  <script src="https://fb.me/react-dom-0.14.6.js"></script>
</head>
<body>
  <!-- entry div for react (could be anything) -->
  <div id='app'>
  </div>
  <!-- main entry (add other component js/jsx here if desired) -->
  <script type='text/babel' src='app.js'></script>
</body>
</html>
```

### now lets look at app.js

`app.js` is where you would place your typical react component(s). You can of course break components out into seperate files / es6 modules, but remember to add it to your `index.html` page.

``` js

// use a stateless
const GroceryItem = (props) => {
  return <div>...typical react stuff...</div>
} 

// or use a stateful
class GroceryList extends React.Component {
  //...typical react stuff
  render() {
    return <div>...typical react stuff...</div>
  }
}

//render to our entry div in the dom
const grocList = ReactDOM.render(<GroceryList id='groceryList' items={['apples', 'oranges', 'cold brew', 'olive oil']} />, document.getElementById("app"));

```


<a id='react-with-webpack'></a>

Setup React with babel  (babel / webpack)
-----------------------------------------

### why babel tho

So the above option is fine for quick tests / dead simple non-production apps. But if our development version is in a `src/` folder as react `jsx`... our preferred solution would be to use `babel-cli` to create a `compiled/` or `dist/` version of your app that would be served to your users in plain ol' javascript, rather than expensively compiling on the spot. 

### why webpack tho

If we just relied on `babel-cli` , we would still end up with tons of files to have to include in our `index.html` page, not to mention performance issues with serving possibly tons of dependencies and compiled components. `webpack` is a *bundler*, it'll take our *X* amount of files and produce *1* bundled `app.js` file. 

<a id='boilerplate-project'></a>
### start a boiler plate project
First we'll start by creating a project with our boilerplate code. Note: in windows, this `&&` should be replaced with `;` semicolons for combining shell commands.

``` sh
# unix...start project folder (its long but it'll make all your boilerplate files)
$> mkdir myProj && cd myProj &&  touch index.html &&  mkdir src &&  cd src &&  touch index.js &&  mkdir components && touch components/App.js && cd ../../ 
```
-- OR --

``` sh
# if in windows...
$> mkdir myProj; cd myProj;  touch index.html;  mkdir src; cd src; touch index.js; mkdir components; touch components/App.js; cd ../../; 
```

You should now have something like this. `index.html` is our browser entry.  `src/index.js` will render the root component `components/App.js` to the dom to kick things off. Future components will live in `components/`

```
MYPROJ/
│ + index.html
│
└───src/
    │  + index.js
    │
    └───components/
           + App.js
```

<a id='npm-dependencies'></a>
### npm init + install dependencies

Ok so hopefully you have a package manager installed . I'm going to be using `npm` here, even though we're not going to run any `node.js` code, so don't worry about that. 
The key things is to install 3 things: `react`, `babel` for webpack specifically, and `webpack` itself. Also similar to `babel-loader` there is a `css-loader` you can install for use with webpack.

Note: `--save` for deployment dependencies and `--save-dev` for development only dependencies.

``` sh 

$> cd MYPROJ/
# init for our package.json to appear in our root (follow directions as you normally would)
$> npm init 
# install our react dependencies (react + react-dom)
$> npm install --save react react-dom
# install babel (webpack mode)
# since we're using webpack, we need to get babel-core (core), babel-loader@7  (webpack plugin using babel 6.x) + other babel presets/ es6 future presets (for .babelrc)
$> npm install --save-dev babel-core babel-loader@7 babel-preset-react babel-preset-env babel-preset-stage-2
# install webpack (the core), webpack-dev-server (if you don't have a hot reloading option), and webpack-cli for use in our package.json
$> npm install --save-dev webpack webpack-dev-server webpack-cli


# ... PSSTTTT..extra (TOTALLY OPTIONAL)
# (optional) babel-cli, maybe if you want to manually run babel not via webpack, install this (if you haven't already)
$> npm install -g babel-cli
# (also optional) css-loader...expound on this some other time.
$> npm install --save-dev style-loader css-loader
```

<a id='npm-cleanup'></a>
### Package cleanup! 

Below is optional, ..buttttt afterword its probably smart to check your packages are good (since this stuff is changing so rapidly, make sure versions are not conflicting, missing dependencies, and what not)

``` sh
# check your installed modules (--depth=0 to not list subdependecies)
$> npm ls --depth=0
# if any weirdness , red errors show up...the following usually will clean it up
$> npm dedupe
```
So now your project folder should look something like this. Note: `node_modules/` and `package.json` to be new. You might even have a `package-lock.json` which shouldn't look that new to you hopefully.


```
* note: denotes new

MYPROJ/
│ - index.html
│ - package.json *
│
│───node_modules/ *
│
└───src/
    │  - index.js
    │
    └───components/
           - App.js
```

<a id='babelrc-webpack-config'></a>
### Setup Config files: .babelrc + webpack.config.js

Two files we'll need for configuration. Note: Conceivably you can supply babel `presets` in the webpack.config.js, but we'll keep it simple and seperate.

  - *.babelrc*
    - `babel` to configure what are called `presets`, to say "hey i want you to transpile these things (react, es6, experimental) to es5". By default, `babel` doesn't know if you have a react app or maybe just an es6 app or something else!
  - *webpack.config.js*
    - for `webpack`, we need to provide an _1) entry_, _2) modules_ with _loaders_, and finally an _3) output_. to say "hey start my app at  entrypoint `index.js` i want these types of files aka `modules` (js/css/etc) to run through a `loader`, in our case the  `babel-loader`, and be spit out into an output destination `dist/app.bundle.js`".

#### So lets do it. We'll create both files.

``` sh 
# create our babel config file in our root
$> touch .babelrc
# create our webpack config file in our root
$> touch webpack.config.js
```

#### edit .babelrc

*.babelrc* is a javascript json style file. Ours will just use the `presets` option, but other options are available, see 
[Official Babel site](https://babeljs.io/docs/en/options) for full docs. Were gonna use 3 presets, note that `stage-2` (optional) is not yet approved experimental features on top of es6 stuff (`env`) and `react` is react stuff ;p. Pretty simple. You may see `stage-0` in liu of `stage-2` or maybe `stage-1`. The smaller the number the more experimental features that preset consumes, so `stage-0` consumes `stage-1` consumes `stage-2` and so on.. if you're not sure you even need experimental features best to do some research before hand.

``` js
{
  "presets": [
    "env",
    "react",
    "stage-2"
  ]
}
```
#### edit webpack.config.js

*webpack.config.js* takes the form of a [node module](https://nodejs.org/api/modules.html). We're using a slew of the options which I'll go over but more info can be found on [webpack's configuation page](https://webpack.js.org/configuration/#options). 

Note: I've added an * for those crucial options to be considered.
  
  - `entry`* - where is the start of our app? in our case its src/index.js. See [entry](https://webpack.js.org/configuration/entry-context) for more info.
  - `module`* - this is the true work horse of webpack, it tells what to look for with a `test` (regex) and what to exclude with a `exclude` regex. To specify the loader to use you can use the `use` array. Note: In our boilerplate instance we're using `babel-loader` solely..just for *.js files but you can uncomment out the `css-loader` module below if you also want to bundle css and have installed the optional modules mentioned above!  See [module](https://webpack.js.org/configuration/module) for more info.
  - `resolve` - resolve can do a lot but when we're importing modules, we use `extensions` to say "hey if i say `import {coolMethod} from 'util'`" then attempt to try these extensions (e.g. util.js, util.jsx, etc..). See [extensions](https://webpack.js.org/configuration/resolve/#resolve-extensions) for more info.
  - `output`* - where do we put the bundled app? `path` is the actual filesystem path it will go. `publicPath` is the http version / html path for relative urls, so if we deploy from root than _/dist/assets_ would be the relative root http pointer. See [output](https://webpack.js.org/configuration/output/#output) for more info.
  - `devServer` - if you're gonna be using webpack-dev-server, here's where you can specify port and folder to deploy + hotload from! This will make more sense in the next section where we edit our `start` script in `package.json`. See [devServer](https://webpack.js.org/configuration/dev-server/) for more info.

``` js

const webpack = require("webpack");

module.exports = {
  entry: [
    './src/index.js'
  ],
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        exclude: /node_modules/,
        use: ['babel-loader']
      }
    // , {
    // test: /\.css$/,
    // use: [ 'style-loader', 'css-loader' ]
    // },
    ]
  },
  resolve: {
    extensions: ['*', '.js', '.jsx']
  },
  output: {
    path: __dirname + '/dist/assets/',
    publicPath: '/dist/assets/',
    filename: 'app.bundle.js'
  },
  devServer: {
    contentBase: './',
    port: 3000
  }
};
```

Cool so now our project should look like this. Don't worry we're nearly to greatness fam. 

```
* note: denotes new

MYPROJ/
│ - index.html
│ - package.json 
│ - .babelrc *
│ - webpack.config.js *
│
│───node_modules/ 
│
└───src/
    │  - index.js
    │
    └───components/
           - App.js
```
<a id='package-json-scripts'></a>
### Edit package.json scripts section

So now we're ready to setup our package.json. There's probably more in there than just `scripts`..but this is the config option we're concerned with. We're gonna add two scripts, rename them what you like:

  - `build`: something to build our `webpack` bundle once, mainly used for deployment
  - `buildWatch`: this is an alternative to `devServer` (see next description)..perhaps you want to use another browser refresh server like `live-server --wait=500`, so this would skip browser refresh, but still  watches for file changes 
  - `devServer`: this will be our dev server using `webpack-dev-server` that watches for file changes and refreshes our browser after a new compile. 



Makes sense right? See below. Woop ok now we can start writing app goodness.

*package.json*
``` js
{
  // ...
  "scripts": {
     "build":  "webpack -d --config ./webpack.config.js --mode production",
     "buildWatch": "webpack -d --watch --config ./webpack.config.js --mode development",
     "devServer": "webpack-dev-server --config ./webpack.config.js --mode development"
   },
  // ...
}
```

<a id='simple-react-app'></a>

Writing a simple react app
----------------------------

OK now we're ready to test our setup. We're gonna edit 3 files:

  - `index.html` - html root
  - `src/index.js` - our entry way to dom
  - `src/components/App.js` - our root component
  
  Im going to just put the boilerplate code below:

*index.html*


``` html 
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Webpack test</title>
</head>
<body>
  <!-- entry div for react (could be anything) -->
  <div id='app'>
  </div>
  <!-- remember our public path resolves to assets and we're deploying from dist/ -->
  <script type='text/javascript' src='dist/assets/app.bundle.js'></script>
</body>
</html>
```

*src/index.js*

``` js

import ReactDOM from 'react-dom';
import React from 'react';
import App from './components/App.js';

const app = ReactDOM.render(<App/>, document.getElementById("app"));

```

*src/components/App.js*

``` js

import React from 'react';
export default class App extends React.Component {
  constructor(props) {
    super(props);
  }

  render() {
    return (
      <div>
        <h1>This is a webpack/babel/react app thing</h1>
        <p>If using webpack-dev-server, Editing any sinnnnnngle portion of this and saving will automatically
        rebundle this bad boy</p>
      </div>
    );
  }
};
```
<a id='run-webpack'></a>

Running webpack build / dev-server
-----------------------
Now in your command prompt, try your two npm commands. 

``` sh
#build it just once 
$> npm run build
# (do you see the dist/ folder now?! try opening up your index.html page and it should render alright!)
# GREAT! if you're getting any errors, check if all your presets are properly installed and check your code for syntax errors
# 
#...now let run our dev server
$> npm run devServer
# is your server running on localhost:3000? Check if you have any errors and make sure your publicPath is set relative to your server root!
```

Alright you're all ready to go :)

<a id='deployment'></a>

Deployment (might fold this into how I start all projects)
-------------



Ok so we are bundling our javascript files (+ possibly css if you did those optional stuff). Lets move our html/image files over to dist as well.. we'll do this with `file-loader` + bundle css files if not doing so already with  `css-loader `. 

In fact! Lets refactor our file structure so that we can deploy all our files from `dist` folder and keep all our working files in `src` file. 

``` sh
# source folder (reactor yours !)
src
├── css
│   └── style.css
├── index.html
└── js
    ├── components
    │   └── App.js
    └── index.js

# dist folder 
dist/
├── index.html
└── js
    └── app.bundle.js

```

We'll install any loaders we dont have already!

``` sh
# css loader if you don't have
$> npm install --save-dev style-loader css-loader
# file loader for html 
$> npm install --save-dev file-loader
```

Now in webpack we'll add the file loader config and css if you don't have it yet.
Note the addition of `css-loader` and `file-loader`. We include some options for copying the `[name]` and `[ext]` extension for naming and to put the index.html to the parent of our entry file (so the root of dist!)



### webpack.config.js (refactor!)
```
const webpack = require("webpack");

module.exports = {
  entry: [
    './src/js/index.js'
  ],
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        exclude: /node_modules/,
        use: ['babel-loader']
      },
      {
        test: /\.(html)$/,
        loader: 'file-loader',
        options: {
          name: '[name].[ext]',
          outputPath: '../'
        }
      }
    , {
        test: /\.css$/,
        use: [ 'style-loader', 'css-loader' ]
      }
    ]
  },
  resolve: {
    extensions: ['*', '.js', '.jsx']
  },
  output: {
    path: __dirname + '/dist/js',
    publicPath: '/dist/js',
    filename: 'app.bundle.js'
  },
  devServer: {
    contentBase: './',
    port: 3000
  }
};
```

### importing html/css/html 

Usage of file/css loader example below. You simply import to tell bundler to copy/bundle those files over. No need of using these variable necessary. Note: file loader would be used similarly for image files and other assets

index.js
``` js
// to include copying html over do such like so
import html from '../index.html';
//  for css files (which will be included in bundle.js file)
import css from '../css/style.css';
```

Fullstack hotloading
----------

Nodemon + a hot browser refresh option? how can we do that? TODO!!

