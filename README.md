# First, create a dir

## Obs: Apague tudo que está fora da pasta frontend/ primeiro

```javascript
cd frontend/
mkdir build-utils
touch webpack.production.js
```

# Second, install all dependencies

```javascript
Copy & Paste in package.json in devDependencies:
    "@babel/core": "^7.2.0",
    "@babel/plugin-syntax-dynamic-import": "^7.2.0",
    "@babel/preset-env": "^7.2.0",
    "@babel/preset-react": "^7.0.0",
    "babel-loader": "^8.0.4",
    "babel-plugin-syntax-dynamic-import": "^6.18.0",
    "babel-plugin-transform-class-properties": "^6.24.1",
    "babel-plugin-transform-es2015-modules-commonjs": "^6.26.2",
    "copy-webpack-plugin": "^5.0.2",
    "css-loader": "^1.0.1",
    "file-loader": "^2.0.0",
    "html-webpack-plugin": "^3.2.0",
    "mini-css-extract-plugin": "^0.4.5",
    "optimize-css-assets-webpack-plugin": "^5.0.1",
    "react-hot-loader": "^4.3.12",
    "sass-loader": "^7.1.0",
    "style-loader": "^0.23.1",
    "uglifyjs-webpack-plugin": "^2.0.1",
    "url-loader": "^1.1.2",
    "webpack": "^4.27.0",
    "webpack-cli": "^3.1.2",
    "webpack-dev-server": "^3.1.10",
    "webpack-merge": "^4.1.4"
```

# Trêis, copy and paste code with build-utils/webpack.production.js

```javascript
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const UglifyJsPlugin = require("uglifyjs-webpack-plugin");
const OptimizeCSSAssetsPlugin = require("optimize-css-assets-webpack-plugin");
const CopyPlugin = require("copy-webpack-plugin");
const path = require("path");

module.exports = () => ({
  devtool: "nosource-source-map",
  output: {
    filename: "production.js"
  },
  optimization: {
    minimizer: [
      new UglifyJsPlugin({
        cache: true,
        parallel: true,
        sourceMap: true // set to true if you want JS source maps for css
      }),
      new OptimizeCSSAssetsPlugin({})
    ]
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: "[name].css",
      chunkFilename: "[id].css"
    }),
    new CopyPlugin([
      {
        from: "public/**/*",
        to: "",
        ignore: ["*.js", "*.html"],
        flatten: true
      }
    ])
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, "css-loader"]
      }
    ]
  }
});
```

# Cuatro, create a .babelrc and webpack.config.js in frontend/

```javascript
touch .babelrc
touch webpack.config.js
```

and copy and paste:

```javascript
/**
 * .babelrc
 *
 */
{
  "presets": [
    "@babel/preset-react",
    [
      "@babel/preset-env",
      {
        "targets": {
          "browsers": "last 2 versions"
        },
        "modules": false,
        "loose": false
      }
    ]
  ],
  "plugins": [
    "transform-class-properties",
    "react-hot-loader/babel",
    "syntax-dynamic-import"
  ],
  "env": {
    "test": {
      "plugins": ["transform-es2015-modules-commonjs"]
    }
  }
}
```

and

```javascript
/**
 * webpack.config.js
 */
const path = require("path");
const webpack = require("webpack");
const webpackMerge = require("webpack-merge");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const modeConfiguration = env => require(`./build-utils/webpack.${env}`)(env);

module.exports = ({ mode } = { mode: "production" }) => {
  console.log(`mode is: ${mode}`);

  return webpackMerge(
    {
      mode,
      entry: "./src/index.js",
      devServer: {
        hot: true,
        open: true
      },
      output: {
        publicPath: "/",
        path: path.resolve(__dirname, ".."),
        filename: "bundled.js"
      },
      module: {
        rules: [
          {
            test: /\.(js|jsx)$/,
            exclude: /node_modules/,
            loader: "babel-loader"
          }
        ]
      },
      plugins: [
        new HtmlWebpackPlugin({
          template: "./public/index.html"
        }),
        new webpack.HotModuleReplacementPlugin()
      ]
    },
    modeConfiguration(mode)
  );
};
```

# 5, install dependencies and configure scripts

```javascript
yarn; // in frontend/
```

```javascript
/**
 * package.json scripts add after "deploy"
*/
"webpack": "webpack",
"webpack-dev-server": "webpack-dev-server",
"dev": "npm run webpack-dev-server -- --env.mode development",
"prod": "npm run webpack -- --env.mode production"
```

and GoToHell ops "GoToCell"

```javascript
yarn run prod
```
