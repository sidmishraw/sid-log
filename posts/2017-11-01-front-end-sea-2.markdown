---
title: My second dive into the front-end sea
author: Sidharth Mishra <sidmishraw>
---

</br>

Continuing over from [Part#1](/posts/2017-11-01-front-end-sea-1.html)

After setting up the base project with *Babel*, *Webpack* and testing them out, I started dabbling in *React* itself.

Before jumping into *React*, logging a brief review about *Webpack*.

</br>
</br>

### Webpack, do I need it?

Looking at how webpack bundles stuff, it does prove its place in my stack.

</br>
</br>

### Is Webpack inspiring?

I like bundlers and package managers, but, *Webpack* is verbose :(

The verbosity is manageable if the `webpack.config.js` is not changed all the time. 

  > Note: Looking at how people implement their builds, I'm assuming it is not changed most of the time.

*Webpack* is inspiring. Using code to cut down on redundant tasks is awesome and should be the norm.

</br>
</br>

### Brief dive into Webpack basics (ripping off of Webpack docs, sry folks but it is well written xD):

</br>

Webpack has 4 core concepts:

</br>

* [**Entry**](https://webpack.js.org/concepts/#entry)

    An entry point indicates which module webpack should use to begin building out its internal dependency graph. After entering the entry point, webpack will figure out which other modules and libraries that entry point depends on (directly and indirectly).

    Basically, the `entry` is the root of the dependency graph.

    The following code snippet shows how `entry` field is specified in the `webpack.config.js`
  
    ```javascript
    module.exports = {
      entry: './path/to/my/entry/file.js'
    };
    ```

</br>

* [Output](https://webpack.js.org/concepts/#output)

    The output property tells webpack where to emit the bundles it creates and how to name these files. You can configure this part of the process by specifying an output field in your configuration:

    The following code snippet shows how `output` field is configured in the `webpack.config.js`

    ```javascript
    /* node.js imports */
    const path = require('path');
    /* node.js imports */

    module.exports = {
      entry: './path/to/my/entry/file.js',
      output: {
        path: path.resolve(__dirname, 'dist'),            // path to the output directory, __dirname is the Node.js
                                                          // global variable denoting the current directory of the file.
        filename: 'my-first-webpack.bundle.js'            // the name of the bundle
      }
    };
    ```

</br>

* [Loaders](https://webpack.js.org/concepts/#loaders)

    Loaders enable webpack to process more than just JavaScript files (webpack itself only understands JavaScript). They give you the ability to leverage webpack's bundling capabilities for all kinds of files by converting them to valid `modules` that webpack can process.

    Essentially, webpack loaders transform all types of files into modules that can be included in your application's dependency graph.

    At a high level, loaders have two purposes in your webpack config. They work to:

      * Identify which file or files should be transformed by a certain loader (with the test property).

      * Transform those files so that they can be added to your dependency graph (and eventually your bundle). (use property)

    The following code snippet shows the `loader` configuration in `webpack.config.js`:

    ```javascript
    const path = require('path');

    const config = {
      entry: './path/to/my/entry/file.js',
      output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'my-first-webpack.bundle.js'
      },
      module: {                                       
        rules: [
          {                                           // a loader
            test: /\.txt$/,                           // the loader's `test` property
            use: 'raw-loader'                         // the loader's `use` property
          }
        ]
      }
    };

    module.exports = config;
    ```

</br>

* [Plugins](https://webpack.js.org/concepts/#plugins)

    While loaders are used to transform certain types of modules, plugins can be leveraged to perform a wider range of tasks. Plugins range from bundle optimization and minification all the way to defining environment-like variables. The plugin interface is extremely powerful and can be used to tackle a wide variety of tasks.

    In order to use a plugin, you need to `require()` it and add it to the `plugins` array. Most plugins are customizable through options. Since you can use a plugin multiple times in a config for different purposes, you need to create an instance of it by calling it with the `new` operator.

    Plugins are installed via `npm` or `yarn`

    For eg:

    ```javascript
    /* node.js imports */
    const HtmlWebpackPlugin = require('html-webpack-plugin');   // installed via npm
    const webpack = require('webpack');                         // to access built-in plugins
    const path = require('path');
    /* node.js imports */

    const config = {
      entry: './path/to/my/entry/file.js',
      output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'my-first-webpack.bundle.js'
      },
      module: {
        rules: [
          { test: /\.txt$/, use: 'raw-loader' }
        ]
      },
      plugins: [
        new webpack.optimize.UglifyJsPlugin(),
        new HtmlWebpackPlugin({template: './src/index.html'})
      ]
    };

    module.exports = config;
    ```

    I used the `html-webpack-plugin` plugin for my site and installed it with *yarn*.


</br>
</br>

### Diving into React (balls deep):

* Installed `react` and `react-dom`:

    Added `react` and `react-dom` as dependecies. These constitute the *React.js* library.

    ```bash-shell
    yarn add react react-dom
    ```

    This installed `react` and `react-dom` that were added as dependencies.

* Added *React.js* code to my `app.js`:

    ```javascript
    import React, { Component } from "react";
    import ReactDOM from "react-dom";

    class Hello extends Component {
      render() {
        return (
          <p> Hey {this.props.name} !</p>
        );
      }
    }

    ReactDOM.render(<Hello name="Sid"/>, document.getElementById("root"));
    ```

* Started app using the following command:

    ```bash-shell
    yarn start --watch
    ```

    Nothing happened, xD

    `There was issue with Babel, the guide I was following was too old.`

    Uninstalled `babel-preset-es2017` and `babel-preset-es2015`.

    Moved over to `babel-preset-env` as recommended by **babel.io**.

    My new `package.json` looked like: (removed unnecessary dev dependencies in the production code)

    ```
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
        "react": "^16.0.0",
        "react-dom": "^16.0.0"
      },
      "devDependencies": {
        "webpack": "^3.8.1",
        "webpack-dev-server": "^2.9.3",
        "babel-core": "^6.26.0",
        "babel-loader": "^7.1.2",
        "babel-preset-react": "^6.24.1",
        "babel-preset-env": "^1.6.1"
      },
      "scripts": {
        "start": "webpack-dev-server"
      }
    }
    ```

    Problem seems that `.babelrc` was not needed. Updated the project by deleting the `.babelrc`

    Instead updated the `webpack.config.js` to look like:

    ```javascript
      /* node.js imports */
      const path = require('path');
      const HtmlWebpackPlugin = require('html-webpack-plugin');   // webpack plugin import
      /* node.js imports */

      const HtmlWebpackPluginConfig = new HtmlWebpackPlugin(
        {
          template: __dirname + '/src/app.html',
          filename: 'app.html',
          inject: 'head'
        }
      );

      module.exports = {
        entry: __dirname + '/src/app.jsx',
        output: {
          path: __dirname + '/bin',
          filename: 'app.bundle.js'
        },
        module: {
          loaders: [
            {
              test: /\.js$/,
              loader: 'babel-loader',
              exclude: /node_modules/i,
              query: {
                presets: ['es2015', 'react']              // babel recommends to use presets like this now
              }
            },
            {
              test: /\.jsx$/,
              loader: 'babel-loader',
              exclude: /node_modules/i,
              query: {
                presets: ['es2015', 'react']            // babel recommends to use presets like this now
              }
            }
          ]
        },
        plugins: [HtmlWebpackPluginConfig]
      };
    ```

</br>
</br>

### Feels bad man:

It still failed. To solve the problem, I purged the `node_modules` and moved content in the `package.json` around to streamline my dependencies.

The way babel worked was also giving me a lot of pain. (This is expected when there are so many items to configure)

Next, updated the `.babelrc` file to

```json
{
  "plugins": [
    "transform-react-jsx"
  ],
  "presets": [
    "react",
    "env"
  ]
}
```

and then, updated the `webpack.config.js` file to:

```js
/* node.js imports */
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');   // webpack plugin import
/* node.js imports */

const HtmlWebpackPluginConfig = new HtmlWebpackPlugin(
  {
    template: __dirname + '/src/app.html',
    filename: 'app.html',
    inject: 'head'
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
      {
        test: /\.js$/,
        loader: 'babel-loader',
        exclude: /node_modules/i,
      },
      {
        test: /\.jsx$/,
        loader: 'babel-loader',
        exclude: /node_modules/i,
      }
    ]
  },
  plugins: [HtmlWebpackPluginConfig]
};
```

`And, the code compiled, but still I didn't see react stuff show up :(` (feels bad man T_T)

After going through the src code, I patched up `package.json`, `webpack.config.js` and `.babelrc` to the following and it worked!

------

#### webpack.config.js

```js
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
  plugins: [HtmlWebpackPluginConfig]
};
```

------

#### package.json

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
    "react": "^16.0.0",
    "react-dom": "^16.0.0"
  },
  "devDependencies": {
    "path": "^0.12.7",
    "html-webpack-plugin": "^2.30.1",
    "webpack": "^3.8.1",
    "webpack-dev-server": "^2.9.3",
    "babel-core": "^6.26.0",
    "babel-loader": "^7.1.2",
    "babel-preset-react": "^6.24.1",
    "babel-preset-env": "^1.6.1"
  },
  "scripts": {
    "start": "webpack-dev-server"
  }
}
```

------

#### .babelrc

```json
{
  "plugins": [
    "transform-react-jsx"
  ],
  "presets": [
    "react",
    "env"
  ]
}
```

------

</br>

### Conclusion of the second dive:

I was able to run *React* and this ends my first test dive into front-end sea.

More in [Part#3](2017-11-02-front-end-sea-3.html)

</br>

### References:

[1] Webpack docs, https://webpack.js.org/concepts/

[2] Twilio blog, https://www.twilio.com/blog/2015/08/setting-up-react-for-es6-with-webpack-and-babel-2.html

[3] Reactjs docs, https://reactjs.org/docs/installation.html#adding-react-to-an-existing-application

[4] babel docs, https://babeljs.io/docs/setup/#installation

[5] My codepen, https://codepen.io/sidmishraw/pen/XzmBQX?editors=0010