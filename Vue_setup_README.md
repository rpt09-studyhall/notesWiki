# 1. Vue setup

This is just some notes on setting up vue. For more of a tutorial on vue syntax, see vuejs.org.
For crash course I personally enjoyed this video : https://www.youtube.com/watch?v=4deVCNJq3qc&t=2474s

<!-- TOC -->

- [1. Vue setup](#1-vue-setup)
  - [1.1. Install vue-cli](#11-install-vue-cli)
  - [1.2. Add custom webpack routines](#12-add-custom-webpack-routines)
  - [1.3. Add scss](#13-add-scss)
- [2. Run dev server](#2-run-dev-server)

<!-- /TOC -->

## 1.1. Install vue-cli

First install the vue-cli tool , which is similar to `create-react-app `. I'm sure there is a more manual way of setting up a vue boilerplate app, but i've only done it this way.

```bash
# install cli globally
$ npm install -g @vue/cli
# cd to your projects folder of choice
$ cd my-projects/
# create an app, will create a new folder with its own package.json
$ vue create my-project
# follow the cli questions...i just leave default
```

This will give you something like below. 
```bash
├── README.md
├── babel.config.js
├── dist
│   ├── css
│   ├── favicon.ico
│   ├── img
│   ├── index.html
│   └── js
├── package-lock.json
├── package.json
├── public
│   ├── favicon.ico
│   └── index.html
└── src
    ├── App.vue
    ├── assets
    ├── components
    └── main.js

```

## 1.2. Add custom webpack routines

It has a built in webpack config, that you can extend via vue.config.js so add that by doing
```bash
# add vue config file
$ touch vue.config.js
# you can extend the built in webpack by adding this to that file
# // vue.config.js example
# module.exports = {
#   configureWebpack: {
#     // ... add webpack stuff here
#   }
# }
```

## 1.3. Add scss 

Scss is also built into the built in webpack, but unless you added the plugin in the cli command you'll have to install the dependencies

```bash
# Sass install dependencies
$ npm install -D sass-loader sass
```

Then in your *.vue component files you can just specify via the `lang='scss'` in the style tag

```html
<style lang="scss">
$color: red;
</style>
```

# 2. Run dev server 

the vue create routine also provides a bunch of npm scripts to run..`npm run serve`will launch the dev version

```
    "serve": "vue-cli-service serve --open",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint"
```