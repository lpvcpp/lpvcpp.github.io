---
title: "Thật bối rối với config của Webpack"
author: giangnh
description: "Webpack, javascript, khóc, than thở, bế tắc"
---

![It's fun](/assets/images/fun_meme.jpg)

Hôm trước có làm một repo nhỏ về việc tự động serve js, css, html, ... các kiểu, rồi chỉ cần chạy lệnh
`npm build` là tự động build ra các file đã được mini.
Mình đã nghe qua `webpack` có thể làm được điều đó, rồi mình dành ra 30p để đọc lại documents,
rồi dành ra tiếp 30p để đọc tiếp vì 30p đầu đọc chưa hiểu gì, sau 1 tiếng thì mình cũng viết được
đoạn code đơn giản:

~~~~rust
 cons path = require('path');

  module.exports = {
      entry: path.join(__dirname, 'src/app.js'),
      output: {
          path: path.join(__dirname, '/dist/'),
          filename: '[name].js'
      }
  }
~~~~

Nhưng nhiêu đó thôi thì chưa đủ để làm được những điều mình cần, rồi mình search thêm thì
cần phải thêm khác nhiều thứ khác :sad: nào là `HtmlPlugins`, `TextExtractPlugin`, `Babel`,
`CSSModule` gì đấy, mà mình không nhớ nỗi, rồi để đạt điều auto reload thì cần phải có 
`Webpack Hot Loader`, ôi mẹ ơi sao nhiều vãi, đã dành hết 2 tiếng vẫn chưa config được :sosad:.
Cuối cùng mình chợt nghĩ ra một ý định táo bạo là search boilerplate cho lẹ, tự config cái này
có khi cả tháng chưa xong :funny:.

Khi search được cái boilerplate ưng ý đáp ứng đủ các như cầu của mình, thì mình vào xem file `package.json`
nó có gì, thì ôi mẹ ơi

~~~~rust
"autoprefixer": "^6.0.3",
    "babel-eslint": "^4.1.6",
    "babel-loader": "^6.2.3",
    "babel-jscs": "^2.0.5",
    "babel-preset-stage-0": "^6.3.13",
    "chai": "^3.2.0",
    "cross-env": "^1.0.7",
    "css-loader": "^0.19.0",
    "eslint": "^1.5.0",
    "eslint-plugin-react": "^3.4.2",
    "express": "^4.13.3",
    "extract-text-webpack-plugin": "^0.8.2",
    "html-webpack-plugin": "^1.6.1",
    "jscs": "^2.1.1",
    "jsdom": "^6.5.1",
    "json-loader": "^0.5.3",
    "mocha": "^2.3.3",
    "mocha-jsdom": "^1.0.0",
    "postcss-loader": "^0.6.0",
    "rimraf": "^2.4.3",
    "sinon": "^1.16.1",
    "sinon-chai": "^2.8.0",
    "stats-webpack-plugin": "^0.2.1",
    "style-loader": "^0.12.4",
    "webpack": "^1.12.2",
    "webpack-dev-middleware": "^1.2.0",
    "webpack-hot-middleware": "^2.2.0"
~~~~

sao có mấy vài chức năng mà sao cần nhiều thế :smile:. Nhìn qua file `webpack.config.js` thì không bình luận thêm

~~~~rust
'use strict';

var path = require('path');
var webpack = require('webpack');
var HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  devtool: 'eval-source-map',
  entry: [
    'webpack-hot-middleware/client?reload=true',
    path.join(__dirname, 'app/main.js')
  ],
  output: {
    path: path.join(__dirname, '/dist/'),
    filename: '[name].js',
    publicPath: '/'
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: 'app/index.tpl.html',
      inject: 'body',
      filename: 'index.html'
    }),
    new webpack.optimize.OccurenceOrderPlugin(),
    new webpack.HotModuleReplacementPlugin(),
    new webpack.NoErrorsPlugin(),
    new webpack.DefinePlugin({
      'process.env.NODE_ENV': JSON.stringify('development')
    })
  ],
  module: {
    loaders: [{
      test: /\.js?$/,
      exclude: /node_modules/,
      loader: 'babel',
      query: {
        "presets": ["es2015", "stage-0"]
      }
    }, {
      test: /\.json?$/,
      loader: 'json'
    }, {
      test: /\.css$/,
      loader: 'style!css?modules&localIdentName=[name]---[local]---[hash:base64:5]'
    }]
  }
};
~~~~

Quá nhiều dependencies để tìm hiểu. Cảm giác 1 ngày tìm hiểu về thằng này là không đủ với mình.
Bài viết chỉ là suy nghĩ cá nhân, mong nhận được góp ý từ cao nhân.
