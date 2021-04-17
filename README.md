# Create ElectronJS + React app from scratch
## NPM
* Initialize NPM project:
```bash
npm init -y
```
* Create `package.json` scripts
```json
{
    ...

    "scripts": {
        "start": "webpack serve --open --mode development",
        "test": "echo \"Error: no test specified\" && exit 1",
        "format": "prettier --write \"src/**/*.js\"",
        "eslint-fix": "eslint --fix \"src/**/*.js\"",
        "build": "webpack --mode production"
    },

    ...
}
```
## React
* Install React
```bash
npm install react react-dom
```
* Create `./src/index.js`
```js
import React from 'react';
import ReactDOM from 'react-dom';
import "./style/main.less";
class Welcome extends React.Component {
    render () {
        return <h1>Hello World!</h1>;
    }
}
ReactDOM.render(<Welcome />, document.getElementById('root'));
```
* Create `./public/index.html`
```html
<!DOCTYPE html>
<html>
  <head>
    <title>My React Configuration Setup</title>
  </head>
  <body>
    <h1>HEADER</h1>
    <div id="root"></div>
  </body>
</html>
```
## Webpack
* Install Webpack
```
npm install --save-dev webpack webpack-dev-server webpack-cli html-webpack-plugin
```
* Create `webpack.config.js`
```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    mode: 'development',
    devtool: 'inline-source-map',
    entry: './src/index.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        publicPath: '/',
        filename: 'bundle.js'
    },
    devServer: {
        contentBase: "./dist"
    },
    module: {
        rules: [
            {
                test: /\.(js|jsx)$/,
                exclude: /node_modules/,
                use: ['babel-loader', 'eslint-loader']
            },
            {
                test: /\.(less|css)$/,
                use: [
                    'style-loader',
                    'css-loader',
                    'less-loader'
                ]
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: path.resolve('./public/index.html')
        })
    ]
};
```
## Babel
* Install Babel
```
npm install --save-dev @babel/core @babel/preset-env
    @babel/preset-react babel-loader
```
* Create `.babelrc`
```json
{
    "presets": [
        "@babel/preset-env",
        "@babel/preset-react"
    ]
}
```
## Prettier
* Install Prettier formatter
```
npm install --save-dev --save-exact prettier
```
* Create `.prettierrc`
```json
{
    "semi": true,
    "singleQuote": true,
    "trailingComma": "es5"
}
```
## ESLint
* Install ESLint linter
```
npm --save-dev install eslint eslint-loader babel-eslint
    eslint-config-react eslint-plugin-react
```
* Create `.eslintrc`
```json
{
    "parser": "babel-eslint",
    "extends": "react",
    "env": {
        "browser": true,
        "node": true
    },
    "settings": {
        "react": {
            "version": "detect"
        }
    }
}
```
## CSS LESS
* Install CSS LESS processor
```
npm install --save-dev less less-loader css-loader style-loader
```
* Create `header.less`
```css
.header {
    background-color: #3d3d;
}
```
* Create `./src/style/main.less`
```css
@import "header.less";
@color: #101297;
body {
    background-color: @color;
}
```

You can now run your React app with
```
npm start
```

---

We will now turn this React app into an ElectronJS + React app


## ElectronJS
* Install ElectronJS dependencies
```
npm install concurrently electron electron-builder wait-on
```
* Create `./public/electron.js` file

```javascript
const electron = require('electron');
const path = require('path');
const isDev = require('electron-is-dev');

const app = electron.app;
const BrowserWindow = electron.BrowserWindow;

let mainWindow;

function createWindow () {
    mainWindow = new BrowserWindow({ width: 900, height: 680 });
    mainWindow.loadURL(isDev ? "http://localhost:8080" : `file://${path.join(__dirname, "../build/index.html")}`);
    mainWindow.on('closed', () => (mainWindow = null));
}

app.on('ready', createWindow);
app.on('window-all-closed', () => {
    if (process.platform !== 'darwin') {
        app.quit();
    }
});
app.on('activate', () => {
    if (mainWindow === null) {
        createWindow();
    }
});
```
* Update `package.json` file
```json
...

"main": "./public/electron.js",

...

"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "format": "prettier --write \"src/**/*.js\"",
    "eslint-fix": "eslint --fix \"src/**/*.js\"",
    "build": "webpack --mode production",
    "electron-start": "electron .",
    "start-react-open": "webpack serve --open --mode development",
    "start-react": "webpack serve --mode development",
    "start": "concurrently \"cross-env BROWSER=none npm run start-react\" \"wait-on http://localhost:8080 && electron .\""
  },

...
```

You can now run your ElectronJS + React app with
```
npm start
```