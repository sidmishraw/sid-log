---
title: My first dive into the front-end sea
author: Sidharth Mishra <sidmishraw>
---

My dive into the *front-end sea* began with my decision to make myself this personal website.

I thought of using *Webpack*, *ES6(or Purescript or Livescript)*, *Babel*, *Yarn* and *React.js* for my personal website.

> `Note: Of course, jQuery was implicit xD`

---

### Webpack:

*Webpack* allows us to develop Javascript application in modular way, that is - 
*Webpack* is a bundler that bundles all the dependencies together.

*Webpack* defines itself as:

> "At its core, webpack is a module bundler for modern JavaScript applications. When webpack processes your application, it recursively builds a dependency graph that includes every module your application needs, then packages all of those modules into one or more bundles." [2]

---

### Installing yarn:

I installed *yarn* through *npm* by using the following command:

```bash-shell
npm install -g yarn
```

Another alternative for installing *yarn* is installing it via *Homebrew* on macOS.

```bash-shell
brew update && brew install yarn
```

> Note: At the time of writing this log, yarn was at version `1.2.1`
  ```bash-shell
  yarn --version # 1.2.1
  ```

</br>
<p></p>
</br>

___

## The dive begins:

---

#### 1. Project directory setup:
  
```bash-shell
mkdir static-site-gen && cd static-site-gen

yarn init
```

*yarn* like *npm* created a `package.json` at the project root.

My `package.json` looked like:

```json
{
  "name": "static-site-gen",
  "version": "1.0.0",
  "description": "Creating React.js card for displaying my GitHub projects",
  "main": "main.js",
  "author": "Sidharth Mishra",
  "license": "MIT",
  "private": false
}
```

---

#### 2. Webpack installation:

Installed *webpack* through *yarn* using the command below:

```bash-shell
yarn add webpack webpack-dev-server path

yarn add html-webpack-plugin
```

Once installed, `yarn.lock` file and `node_modules` directory were visible in the project directory.

The `html-webpack-plugin` plugin is used because *React* uses *JSX* (*Javascript* and *XML*) and *ES6*. Many browsers do not support ES6 and JSX yet. Then, after babel transpiles the ES6 and JSX code into ES5 and `babel-loader` bundles the JS code, the bundled output is inserted into the `app.html` file automatically by the `html-webpack-plugin`.

Next, created a file named `webpack.config.js` in the project directory.

```javascript
/* node.js imports */
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');   // webpack plugin import
/* node.js imports */

const HtmlWebpackPluginConfig = new HtmlWebpackPlugin(
  {
    template: __dirname + '/src/app.html',
    filename: 'app.html',
    inject: 'body'
  }
);

module.exports = {
  entry: __dirname + '/src/app.js',
  output: {
    path: __dirname + '/bin',
    filename: 'app.bundle.js'
  },
  module: {
    loaders: [
      { test: /\.js$/, loader: 'babel-loader', exclude: /node_modules/ },
      { test: /\.jsx$/, loader: 'babel-loader', exclude: /node_modules/ }
    ]
  },
  plugins: [HtmlWebpackPluginConfig]
};
```

  * The `entry` key requires the entry file for the application (also the index.js or main.js)

    An entry point indicates which module webpack should use to begin building out its internal dependency graph. After entering the entry point, webpack will figure out which other modules and libraries that entry point depends on (directly and indirectly).

    I had it set to:
    
    ```javascript
    entry: __dirname + '/src/app.js'      // __dirname is the Node.js global constant referring to the present working directory
                                          // since webpack.config.js is located in the project root, __dirname is the project root.
    ```

  * The `output` key requires the location in which the bundled JS code is meant to be saved. (something like the `dist` or `build` or `bin` directories)

  * The `loaders` key property helps in transformations that occur in the application. More info [here](https://webpack.github.io/docs/list-of-loaders.html)


---

####  3. Babel and Babel preset installation:

*Babel* is a transpiler used to transpile ES6 code into ES5 because ES6 is not yet supported by all browsers.

```bash-shell
yarn add babel-loader babel-core babel-preset-es2017 babel-preset-react --dev

touch .babelrc
```

  > Note: `--dev` flag is used since *Babel* is a development dependency.

Added the following snippet to the .babelrc` file:

```json
{
  "presets": [
    "es2017",
    "react"
  ]
}
```

The above snippets informs *Babel* about compiling ES6 and React's JSX.

---

#### 4. React setup:

As per the information provided in the `webpack.config.js` file, I created a directory named `src` inside the project directory.

```bash-shell
mkdir src && cd src

touch app.js app.html
```

I added the following content to the `app.html`:

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>My first dive into the front-end sea</title>
</head>

<body>
  <div id="root">
  </div>
</body>

</html>
```

Then I added the following content to the `app.js`:

```javascript
alert("My first dive into the front-end sea begins with comprehension of react");
```

---

#### 5. Running the setup:

To run the application, I added the `script` field into my `package.json`
  
```javascript
{
  ...,
  "scripts": {
    "start": "webpack-dev-server"
  }
}
```

After saving everything, used the following command to run:

```bash-shell
yarn start --watch
```

Webpack bundled everything and launched a server at `localhost:8080`. But to visit the page I had to visit `localhost:8080/app.html`

After installing all the dependencies my `package.json` looked like:

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
    "html-webpack-plugin": "^2.30.1",
    "path": "^0.12.7",
    "webpack": "^3.8.1",
    "webpack-dev-server": "^2.9.3"
  },
  "devDependencies": {
    "babel-core": "^6.26.0",
    "babel-loader": "^7.1.2",
    "babel-preset-es2017": "^6.24.1",
    "babel-preset-react": "^6.24.1"
  },
  "scripts": {
    "start": "webpack-dev-server"
  }
}
```

---

</br>

Rest continued in [Part#2](/posts/2017-11-01-front-end-sea-2.html)

</br>

### References:

[1] Kayode, T. "Setting up react with webpack 3.0, yarn and babel", https://www.codementor.io/goodnesskay/setting-up-react-with-webpack-3-0-yarn-and-babel-9ftd5phqz

[2] Webpack, https://webpack.js.org/concepts/