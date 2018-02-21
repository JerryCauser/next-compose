# next-compose
Compose next-plugins in a simple way

## Installation

```sh
npm install -D next-compose
```

or

```sh
yarn add -D next-compose
```

## Usage

Simple example with TypeScript and Sass plugins:

```js
const withTS = require('@zeit/next-typescript')
const withSass = require('@zeit/next-sass')
const compose = require('next-compose')

const tsConfig = {/** ts config here */}
const sassConfig = {/** sass config here */}

module.exports = compose([
  [withTS, tsConfig],
  [withSass, sassConfig],
  {
    webpack: (config) => {
      /**some special code */
      return config
    }
  }
])
```

Example with build `vendor.css` and `app.css` from different input extensions:

```js
const withLess = require('@zeit/next-less')
const withSass = require('@zeit/next-sass')
const ExtractTextPlugin = require('extract-text-webpack-plugin')
const commonsChunkConfig = require('@zeit/next-css/commons-chunk-config')
const compose = require('./scripts/next-compose')}

const extractVendorCSSPlugin = new ExtractTextPlugin('static/vendor.css')
const extractAppCSSPlugin = new ExtractTextPlugin('static/app.css')

module.exports = compose([
  [withLess, {
    cssLoaderOptions: { modules: false },
    lessLoaderOptions: { /** less loader options */ },
    extractCSSPlugin: extractVendorCSSPlugin
  }],
  [withSass, {
    cssLoaderOptions: {
      modules: true,
      localIdentName: '[local]-[hash:base64:5]',
    },
    sassLoaderOptions: { /** sass loader options */ },
    extractCSSPlugin: extractAppCSSPlugin,
  }],
  {
    webpack(config, options) {
      config.plugins.push(extractVendorCSSPlugin)
      config.plugins.push(extractAppCSSPlugin)

      if (!options.isServer) {
        config = commonsChunkConfig(config, /\.(less|scss|sass)$/)
      }

      return config
    }
  }
])
```
