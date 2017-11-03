---
title: My third dive into the front-end sea
author: Sidharth Mishra <sidmishraw>
---

I actually had fun working with *React*! But, I was frustrated with *Webpack*.
After much deliberation, I decided to use *Webpack* to only bundle my JS code as libraries.


The repository for the library I built is found [here](https://github.com/sidmishraw/gitHubLike).


The JS library I built using *React* and *Webpack* allows the user to create cards of their [GitHub](https://github.com) repositories. The usage is as simple as:

---

```js
  githubLike.GitHubCards.render("sidmishraw", "projectsList", () => {
    /// WIP, will make a language carousel thingy
    jQuery("#languagesIDabbleIn").html(
      `<ol class="pinned-repos-list mb-4">` +
      githubLike.GitHubCards.getMyLanguages() +
      `</ol>`
    );
    /// WIP, will make a language carousel thingy
    stopLoader();
  });
```

---

My library was exported with the name `githubLike` by *Webpack*. `githubLike` exposes 2 objects named `GitHubCards` and `GitHubLangColors`. `GitHubCards` has a `render` function that can be used to render the GitHub project cards which I made with *React*. I also exposed a function of the `GitHubCards` called `getMyLanguages`. `getMyLanguages` fetches the user's programming languages from their GitHub repositories.

The signature of `render` is `render(userName, elementId, callback)`. `userName` is the name of the user whose GitHub repositories are to be fetched. `elementId` is the ID of the DOM element(div) within which *React* will render the components. `callback` is the callback function that gets executed after the component has been rendered to the screen.

---

To achieve this, I had to modify my `webpack.config.js` to look like the following:

```js
/* node.js imports */
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');                 // webpack plugin import
const ExtractTextWebpackPlugin = require("extract-text-webpack-plugin");  // Extracts text and makes into a new file
/* node.js imports */

const HtmlWebpackPluginConfig = new HtmlWebpackPlugin(
  {
    template: __dirname + '/src/app.html',
    filename: './bin/app.html',
    inject: 'body'
  }
);

module.exports = {
  entry: {
    // "github-components": path.join(__dirname, "/src/github-components.js"),
    // "github-colors": path.join(__dirname, "/src/github-colors.js"),
    "app": path.join(__dirname, "/src/app.js")
  },
  output: {
    filename: "./bin/github-like.bundle.js",
    libraryTarget: "var",            //  When your library is loaded, the return value of your entry point will be assigned to a variable
    library: "githubLike"            // name of the variable the library is exposed as
    // In this case it is exposed as "githubLike"
    // So, the consumer can just include the "github-like.bundle.js" in script tag
    // and then call the function like 
    // githubLike.GitHubCards.render("sidmishraw", "root", () => console.log("Done!"))
    // to render the cards.
  },
  externals: [            // externalizes the dependencies, forcing the user to have these in their projects
    "React",           // it is `React` that we are using from react.js, so the dependency is on `React`
    "Component",       // and so on. So the consumer needs to have these variables in their namespace or
    "ReactDOM",        // JS file to use this library. In my case, not externalizing in order 
    //to create a fat jar like bundle
    "jQuery"
  ],
  module: {
    loaders: [
      // css loaders
      {
        test: /\.css$/,
        loaders: ExtractTextWebpackPlugin.extract({
          fallback: "style-loader",
          use: "css-loader"
        })
      },
      {
        test: /\.js$/,
        loader: 'babel-loader',
        exclude: /node_modules/,
        query: {
          presets: ["env", 'react']            // babel recommends to use presets like this now
        }
      },
      {
        test: /\.jsx$/,
        loader: 'babel-loader',
        exclude: /node_modules/,
        query: {
          presets: ["env", 'react']            // babel recommends to use presets like this now
        }
      }
    ]
  },
  plugins: [
    new ExtractTextWebpackPlugin(
      {
        filename: "./bin/styles.css",
        allChunks: true
      }
    ),
    HtmlWebpackPluginConfig
  ]
};
```

---

The `module.exports` in the `webpack.config.js` now has 1 entry point(was trying with multiple but realized that in the end I need one big fat bundle xD). I made sure that my `app.js` acts as the exporter(because I was building a library).

My `app.js` looked like:

---

```js
import { GitHubCards } from "./github-components.js";
import { GitHubLangColors } from "./github-colors.js";
import "./app.css";

module.exports.GitHubCards = GitHubCards;
module.exports.GitHubLangColors = GitHubLangColors;
```

---

This reminded me of Python's `__init__.py` and how its usage for resolving

---

```python 
from module_name import *
```

---

styled imports.

Since *Webpack* builds a dependency graph starting from the dependencies of the `entry`, once it gets my `app.js` it looked for `github-components.js`, `github-colors.js`, and `app.css`. My `github-components.js` depended on `jQuery`, `react`, and `react-dom` internally and so on.

So, when *Webpack* built the bundle, it jammed everything into `github-like.bundle.js`(analogous to a fat jar xD)

Since I wanted to export my `app.js` as a library, I used the `libraryTarget: "var"` and `library: "githubLike" ` fields in the `output` field of the *Webpack* configuration in `webpack.config.js`.

The `libraryTarget: "var"` and `library: "githubLike"` add a `var githubLike = (function(){...})..` to the `github-like.bundle.js` file so that it can be used as a library.

A snippet of the code from `github-like.bundle.js` looks like:

---

```js
var githubLike =
/******/ (function(modules) { // webpackBootstrap
/******/ 	// The module cache
/******/ 	var installedModules = {};
/******/ ....

```

---

Now, the user of my library(the consumer) can access the `GitHubCards` and `GitHubLangColors` by adding the `github-like.bundle.js` via a `script` tag and calling them like `githubLike.GitHubCards` and `githubLike.GitHubLangColors` respectively. For example,

by adding the following script tag to the html document's `<head>`

---

```html
<script src="/js/github-like.bundle.js"></script>
```

---

and the following function call in one of the included JS files, 

---

```js
githubLike.GitHubCards.render("sidmishraw", "projectsList", () => {
  /// WIP, will make a language carousel thingy
  jQuery("#languagesIDabbleIn").html(
    `<ol class="pinned-repos-list mb-4">` +
    githubLike.GitHubCards.getMyLanguages() +
    `</ol>`
  );
  /// WIP, will make a language carousel thingy
  stopLoader();
});
```

---

the consumer can use the `githubLike.GitHubCards` object to render GitHub repository/project cards.

</br>

> Note: I externalized the `jQuery`, `react` and `react-dom` dependencies because I didn't want to bundle them together.
  This is useful in cases where the consumer already has jQuery, react and react-dom scripts added to their projects.
  It also forces the consumer to use these in their project.

The thing I noticed while mentioning the `externals` in the `webpack.config.js` was that, they are supposed to be actual objects and functions that were imported rather than the files. In my case, it has to be `React`, `Component`, `ReactDOM` and `jQuery` since I used these objects in my JS files.

> Note: When I tried to use `jQuery`, `react` and `react-dom` (file/module names), *Webpack* was not happy and the build failed.

To bundle *CSS* files, I had to use the `extract-text-webpack-plugin`, `css-loader` and `style-loader`. The `extract-text-webpack-plugin` extracted the CSS files and ran them through the loaders and bundled them into a file called `styles.css`. This was only used for testing, in my final project build(this blog), I already had the CSS files included in the `default.html` template hence didn't require it :)

My `package.json` had also changed to accomodate my new requirements and it looked like this:

---

```json
{
  "name": "static-site-gen",
  "version": "1.0.0",
  "description": "Creating React.js card for displaying my GitHub projects",
  "main": "main.js",
  "author": "Sidharth Mishra",
  "license": "MIT",
  "private": false,
  "dependencies": {
    "jquery": "3.2.1",
    "react": "16.0.0",
    "react-dom": "16.0.0"
  },
  "devDependencies": {
    "babel-core": "6.26.0",
    "babel-loader": "7.1.2",
    "babel-preset-env": "1.6.1",
    "babel-preset-react": "6.24.1",
    "css-loader": "0.28.7",
    "extract-text-webpack-plugin": "3.0.2",
    "html-webpack-plugin": "2.30.1",
    "path": "0.12.7",
    "style-loader": "0.19.0",
    "webpack": "3.8.1",
    "webpack-dev-server": "2.9.3"
  },
  "scripts": {
    "start": "webpack-dev-server",
    "build": "webpack"
  }
}
```

---

I had added `build` and `start` script functions to the `package.json` and this enabled me to call *Webpack*'s server and bundling through `yarn`.

---

```bash-shell
yarn start --watch    # for calling the live reloading webpack-dev-server; for testing

yarn build            # the actual bundling happens here, bin directory is generated with all bundles
```

---

In my blog, the `GitHubCards` component is used in the [Projects page](/projects.html)


To conclude, all in all, *React* and *Webpack* were cool to work with. I now have a better understanding of the front-end sea. 

Will continue to dive deeper into the front-end sea ...


---


> Note: [Part#2](/posts/2017-11-01-front-end-sea-2.html)  can be found [here](/posts/2017-11-01-front-end-sea-2.html).


### References:

[1] Calhoun, D., "What is amd, commonjs, and umd?", https://www.davidbcalhoun.com/2014/what-is-amd-commonjs-and-umd/

[2] Webpack docs, "Authoring libraries", https://webpack.js.org/guides/author-libraries/

[3] Webpack text extraction plugin docs, https://github.com/webpack-contrib/extract-text-webpack-plugin

[4] Nader, D., "Beginner's guide to Webpack", https://medium.com/javascript-training/beginner-s-guide-to-webpack-b1f1a3638460

[5] React docs, "React.Component and Lifecycle methods", https://reactjs.org/docs/react-component.html#shouldcomponentupdate

[6] ozh, github-colors, https://github.com/ozh/github-colors/blob/master/colors.json