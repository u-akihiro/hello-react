# Reactが動く環境をスクラッチで構築してみる

↓の記事を参考に進めていく。
[Creating a React App… From Scratch. – Noteworthy - The Journal Blog](https://blog.usejournal.com/creating-a-react-app-from-scratch-f3c693b84658)

# セットアップ

package.jsonの生成
`yarn init`

後々使うので先にgitの準備をしておく
`git init`

まずは下記のディレクトリ構成にする
```
public
src
```

`.gitignore` に `node_module` と `dist` を追加しておく

``` .gitignore
node_module <- nodeモジュールの実ファイルが保存されるディレクトリ
dist <- 最終的なコンパイルファイルが出力されるディレクトリ
```

publicディレクトリ静的ファイルやindex.htmlを置く場所になる。さっそくindex.htmlをpublicディレクトリに作成してみる。内容は下記のとおり。

``` html
<!-- sourced from https://raw.githubusercontent.com/reactjs/reactjs.org/master/static/html/single-file-example.html -->
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <title>React Starter</title>
  </head>
  <body>
    <div id="root"></div>
    <noscript>
      You need to enable JavaScript to run this app.
    </noscript>
    <script src="../dist/bundle.js"></script>
  </body>
</html>
```

# Babel

babelをインストール

```
yarn add babel-core@6.26.3 babel-cli@6.26.0 babel-preset-env@1.7.0 babel-preset-react@6.24.1 --dev
```

babelの設定ファイル(.babelrc)を作成

``` .babelrc
{
  "presets": ["env", "react"]
}
```

# Webpack

webpackをインストール

```
yarn add webpack@4.12.0 webpack-cli@3.0.8 webpack-dev-server@3.1.4 style-loader@0.21.0 css-loader@0.28.11 babel-loader@7.1.4 --dev
```

webpackの設定ファイル(webpack.config.js)を下記の内容で作成

``` json
const path = require("path");
const webpack = require("webpack");

module.exports = {
  entry: "./src/index.js",
  mode: "development",
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        exclude: /(node_modules|bower_components)/,
        loader: 'babel-loader',
        options: { presets: ['env'] }
      },
      {
        test: /\.css$/,
        use: [ 'style-loader', 'css-loader' ]
      }
    ]
  },
  resolve: { extensions: ['*', '.js', '.jsx'] },
  output: {
    path: path.resolve(__dirname, "dist/"),
    publicPath: "/dist/",
    filename: "bundle.js"
  },
  devServer: {
    contentBase: path.join(__dirname, "public/"),
    port: 3000,
    publicPath: "http://localhost:3000/dist/",
    hotOnly: true
  },
  plugins: [ new webpack.HotModuleReplacementPlugin() ]
};
```


# React

reactのインストール

```
yarn add react@16.4.1 react-dom@16.4.1
```

srcディレクトリ下に`index.js`というファイルを作成する。
```
src/index.js
```

作成したら下記の内容を記述する。

``` js
import React from "react";
import ReactDOM from "react-dom";
import App from "./App.js";
ReactDOM.render(
  <App />,
  document.getElementById("root")
);
```

srcディレクトリ下に`App.js`を作成する。
```
src/App.js
```

作成したら下記の内容を記述する。
``` js
import React, { Component} from "react";
import "./App.css";

class App extends Component{
  render(){
    return(
      <div className="App">
        <h1> Hello, World! </h1>
      </div>
    );
  }
}

export default App;
```

srcディレクトリ下に`App.css`を作成する。
```
src/App.css
```

作成したら下記の内容を記述する。
``` scss
.App {
  margin: 1rem;
  font-family: Arial, Helvetica, sans-serif;
}
```

最終的に下記の様なディレクトリ構成になる。

```
├── package.json
├── public
│   └── index.html
├── src
│   ├── App.js
│   └── index.js
├── webpack.config.js
└── yarn.lock
```

# 補足
  
`webpack-dev-server` はビルドした内容をメモリ上に保存する。そのため、本番用のビルドファイルが必要な場合は下記のコマンドを実行して取得する必要がある。
```
yarn webpack --mode production
```