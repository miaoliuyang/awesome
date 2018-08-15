1. 设置NODE_ENV
   ```
   "build": "NODE_ENV='production' webpack -p"
   "build": "SET NODE_ENV='production' && webpack -p" //WINDOWS
   ```
2. 修改react-scripts启动端口
   ```
    "start": "SET PORT=3001 && react-scripts start" //WINDOWS
   ```
2. webpack.config.js模板
   ```
    var path = require('path');
    var HtmlWebpackPlugin = require('html-webpack-plugin');
    var webpack = require('webpack');

    var config = {
      entry: './app/index.js',
      output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'index_bundle.js',
        publicPath: '/'
      },
      module: {
        rules: [
          { test: /\.(js)$/, use: 'babel-loader' },
          { test: /\.css$/, use: [ 'style-loader', 'css-loader' ]}
        ]
      },
      devServer: {
        historyApiFallback: true,
      },
      plugins: [
        new HtmlWebpackPlugin({
          template: 'app/index.html'
        })
      ]
    };

    if (process.env.NODE_ENV === 'production') {
      config.plugins.push(
        new webpack.DefinePlugin({
          'process.env': {
            'NODE_ENV': JSON.stringify(process.env.NODE_ENV)
          }
        }),
        new webpack.optimize.UglifyJsPlugin()
      )
    }

    module.exports = config;
   ```
