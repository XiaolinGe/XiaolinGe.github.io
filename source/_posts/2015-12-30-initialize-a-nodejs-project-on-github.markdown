---
layout: post
title: "Initialize a NodeJS project on Github"
date: 2015-12-30 16:10:55 +0800
comments: true
categories: 
---

## Github
New Repository in Github

## Terminal

Create your project directory in Terminal
```
mkdir (project name)
```
Tell git to make a repo
```
git init
```
Initialise your project
```
npm init
```
Create a develop branch to develop on it
```
git checkout -b develop
```
<!--more-->

## Webpack
Create enviroment for Webpack and React
[``Webpack and React gitbook``](https://fakefish.github.io/react-webpack-cookbook/index.html "https://fakefish.github.io/react-webpack-cookbook/index.html")

### Install Webpack
You should get Webpack installed. We'll do a local install and save it as a project dependency.
```
npm i webpack --save-dev
```

### Project Structure
Structure your project like this:

* /app
    * main.js
    * component.js
* /build
    * bundle.js (自动创建)
    * index.html
* package.json
* webpack.config.js

### Set Webpack
let's set up webpack.config.js

webpack.config.js
```
var path = require('path');


module.exports = {
    entry: path.resolve(__dirname, 'app/main.js'),
    output: {
        path: path.resolve(__dirname, 'build'),
        filename: 'bundle.js',
    },
};
```

### Build Application

Set up /app like this:

app/component.js
```
'use strict';


module.exports = function () {
    var element = document.createElement('h1');

    element.innerHTML = 'Hello world';

    return element;
};
```

app/main.js
```
'use strict';
var component = require('./component.js');


document.body.appendChild(component());
```

Now run webpack in your terminal and your application will be built. A bundle.js file will appear in your /build folder. 
```
webpack
```

build/index.html
```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8"/>
  </head>
  <body>
    <script src="bundle.js"></script>
  </body>
</html>
```
Your index.html file in the build/ folder will need to load up the application.
```
xdg-open index.html
```

### Set package.json scripts

```
npm i webpack --save
```
Add the following to package.json:

package.json
```
 "scripts": {
    "build": "webpack"
  }
```

To invoke a build, you can hit npm run build now.
```
npm run build
```

### Set webpack-dev-server

As a first step, hit npm i webpack-dev-server --save. 

```
npm i webpack-dev-server --save
```

In addition we'll need to tweak package.json scripts section to include it. Here's the basic idea:

package.json
```
{
  "scripts": {
    "build": "webpack",
    "dev": "webpack-dev-server --devtool eval --progress --colors --hot --content-base build"
  }
}
```

When you run npm run dev from your terminal it will execute the command stated as a value on the dev property.
```
npm run dev
```
This is what it does:

* webpack-dev-server - Starts a web service on localhost:8080
* --devtool eval - Creates source urls for your code.
Making you able to pinpoint by filename and line number where any errors are thrown
* --progress - Will show progress of bundling your application
* --colors - Yay, colors in the terminal!
* --content-base build - Points to the output directory configured


### Automatically refresh browser


We added a script that refreshes the application when a change occurs. You will also need to add an entry point to your configuration:

webpack.config.js

```
var path = require('path');

module.exports = {
    entry: [
      'webpack/hot/dev-server',
      'webpack-dev-server/client?http://localhost:8080',
      path.resolve(__dirname, 'app/main.js')
    ],
    output: {
        path: path.resolve(__dirname, 'build'),
        filename: 'bundle.js'
    }
};
```
Now your application will automatically refresh on file changes.

You can visit http://localhost:8080/index.html on your browser
### Default Enviroment

In the example above we created our own index.html file to give more freedom and control. It is also possible to run the application from http://localhost:8080/webpack-dev-server/bundle. This will fire up a default index.html file that you do not control. It also fires this file up in an iFrame allowing for a status bar to indicate the status of the rebundling process.


### Require File


## React

### Install React
```
npm install react --save
```


### Babel

To use the JSX syntax you will need webpack to transform your JavaScript. This is the job of a loader. We'll use Babel as it's nice and has plenty of features.

You need to install version5 not version6 

```
npm install babel@5 --save
npm install babel-loader@5 --save
```
webpack.config.js
```
var path = require('path');
var node_modules = path.resolve(__dirname, 'node_modules');
var pathToReact = path.resolve(node_modules, 'react/dist/react.min.js');

var config = {
    entry: [
        'webpack/hot/dev-server',
        'webpack-dev-server/client?http://localhost:8080',
        path.resolve(__dirname, 'app/main.js')
    ],
     resolve: {
        alias: {
          'react': pathToReact
        }
    },
    output: {
        path: path.resolve(__dirname, 'build'),
        filename: 'bundle.js'
    },
     module: {
        loaders: [{
            test: /\.(js|jsx)$/,
            loader: 'babel-loader'
            }],
         noParse: [pathToReact]
    }
};

module.exports = config;

```

### Use ReactJS
component.jsx

```
import React from 'react';

export default class Hello extends React.Component {
  render() {
    return <h1>Hello world</h1>;
  }
}
```

main.js
```
import React from 'react';
import Hello from './component.jsx';

main();

function main() {
    React.render(<Hello />, document.getElementById('app'));
}
```

build/index.html
```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8"/>
  </head>
  <body>
    <div id="app"></div>

    <script src="http://localhost:8080/webpack-dev-server.js"></script>
    <script src="bundle.js"></script>
  </body>
</html>
```

## Load CSS, LESS, SASS, images, fonts

```
npm install css-loader style-loader --save-dev
npm install less-loader --save
npm install sass-loader --save
npm install url-loader --save-dev
```


webpack.config.js
```
var path = require('path');
var node_modules = path.resolve(__dirname, 'node_modules');
var pathToReact = path.resolve(node_modules, 'react/dist/react.min.js');

var config = {
    entry: [
        'webpack/hot/dev-server',
        'webpack-dev-server/client?http://localhost:8080',
        path.resolve(__dirname, 'app/main.js')
    ],
     resolve: {
        alias: {
          'react': pathToReact
        }
    },
    output: {
        path: path.resolve(__dirname, 'build'),
        filename: 'bundle.js'
    },
     module: {
        loaders: [
            {
                test: /\.(js|jsx)$/,
                loader: 'babel-loader'
            } , {
                test: /\.css$/, // Only .css files
                loader: 'style!css' // Run both loaders
            }, {
                test: /\.less$/,
                loader: 'style!css!less'
            }, {
                test: /\.scss$/,
                loader: 'style!css!sass'
            },  {
                test: /\.(png|jpg)$/,
                loader: 'url?limit=25000'
            },  {
                test: /\.woff$/,
                loader: 'url?limit=100000'
            }],
          noParse: [pathToReact]
    }
};

module.exports = config;
```

## Production Config

Configure a script to run in your package.json file

```
{
  "name": "my-project",
  "version": "0.0.0",
  "description": "My awesome project!",
  "main": "app/main.js",
  "scripts": {
    "dev": "webpack-dev-server --devtool eval --progress --colors --hot --content-base build",
    "deploy": "NODE_ENV=production webpack -p --config webpack.production.config.js"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^1.4.13",
    "webpack-dev-server": "^1.6.6"
  },
  "dependencies": {}
}
```

Create a production config

webpack.production.config.js

```
var path = require('path');
var node_modules_dir = path.resolve(__dirname, 'node_modules');

var config = {
  entry: path.resolve(__dirname, 'app/main.js'),
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
  },
    module: {
        loaders: [{
            test: /\.(js|jsx)$/,
            // There is not need to run the loader through
            // vendors
            // 这里再也不需通过任何第三方来加载
            exclude: [node_modules_dir],
            loader: 'babel'
        }]
    }
};

module.exports = config;
```
Run npm run deploy in the root of the project

```
npm run deploy
```

### Splitting vendors bundle, using multiple entry points


webpack.production.config.js
```
var path = require('path');
var webpack = require('webpack');
var node_modules_dir = path.resolve(__dirname, 'node_modules');

var config = {
  entry: {
    app: path.resolve(__dirname, 'app/main.js'),
    mobile: path.resolve(__dirname, 'app/mobile.js'),

    // Since react is installed as a node module, node_modules/react,
    // we can point to it directly, just like require('react');
    // 当 React 作为一个 node 模块安装的时候，
    // 我们可以直接指向它，就比如 require('react')
    vendors: ['react'] // 其他库
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].js' // 注意我们使用了变量
  },
  module: {
      loaders: [{
      test: /\.(js|jsx)$/,
      exclude: [node_modules_dir],
      loader: 'babel'
    }]
  },
  plugins: [
    new webpack.optimize.CommonsChunkPlugin('vendors', 'vendors.js')
  ]
};

module.exports = config;
```
This configuration will create two files in the dist/ folder. app.js and vendors.js.

Remember to add both files to your HTML file, or you will get the error: Uncaught ReferenceError: webpackJsonp is not defined.

```
<script src="app.js"></script> //不确定路径
<script src="vendors.js"></script>
```

###  Isomorphic render
inject the state
