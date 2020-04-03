### 背景

按照 Ant Design 官网用 React 脚手构建的后台项目，刚接手项目的时候大概30条路由左右，我的用的机子是 Mac 8G 内存，打包完成需要耗时2分钟左右，决定优化一下。

**项目技术栈**： React + React Router + TypeScript + Ant Design 

 

**构建时间慢可能的原因：**

1. React 脚手架默认打包构建出来的文件包含 map 文件
2. Ant Desigin 以及项目中使用的第三方模块太大
3. babel-loader 编译过程慢

**React 脚手架修改 Webpack 配置方案：**

1. npm run rject 暴露出 Webpack 配置信息，直接进行修改。
2. 使用 [react-app-rewired](https://github.com/timarney/react-app-rewired) （一个对 create-react-app 进行自定义配置的社区解决方案）Ant Design 官网推荐。

自定义`Webpack`配置步骤：

1. 基础配置
2. 开发环境配置
3. 生产环境配置

### 使用 customize-cra 修改React 脚手架配置实践

**1、准备工作**

npm i react-app-rewired customize-cra --save-dev 安装 `react-app-rewired` ,`customize-cra`，它提供一些修改 React 脚手架默认配置函数,具体参见：<https://github.com/arackaf/customize-cra>

安装后，修改 `package.json` 文件的 `scripts`

```
"scripts": {
    "start": "react-app-rewired start",
    "build": "react-app-rewired build",
    "test": "react-app-rewired test",
}
```

项目根目录创建一个 `config-overrides.js` 用于修改`Webpack` 配置。

```
Ant Desigin` 提供了一个按需加载的 babel 插件 `babel-plugin-import
antd-dayjs-webpack-plugin`是`Ant Desigin` 官方推荐的插件，用于替换`moment.js
```

安装 `npm i babel-plugin-import --save-dev`,并修改`config-overrides.js` 配置文件

`override`函数用来覆盖`React`脚手架`Webpack`配置；`fixBabelImports`修改`babel`配置

```
const { override, fixBabelImports，addWebpackPlugin } = require('customize-cra');
const AntdDayjsWebpackPlugin = require('antd-dayjs-webpack-plugin');
module.exports = override(
  fixBabelImports('import', {
    libraryName: 'antd',
    libraryDirectory: 'es',
    style: 'css',
 }),
   addWebpackPlugin(new AntdDayjsWebpackPlugin())
);
```

以上是`Ant Desigin`推荐的做法。

**2、首屏加载优化**

`npm i react-loadable customize-cra --save`安装`react-loadable`模块,然后在路由文件里使用如下，`loading`组件可以自定义。这样打包的时候会为每个路由生成一个`chunk`，以此来实现组件的动态加载。

需要安装`"@babel/plugin-syntax-dynamic-import`这个插件，编译`import()`这种语法

```
import Loadable from 'react-loadable';
const Index = Loadable({
    loader:() => import('../components/Index'),
    loading:SpinLoading
});
```

**3、去掉 map 文件**

首先安装依赖包`webpack-stats-plugin` `webpack-bundle-analyzer` 前者为了统计打包时间会在打包后的文件夹里生成一个`stats.json`文件，后者用来分析打包后的各个模块的大小。

`process.env.GENERATE_SOURCEMAP = "false";`用来去掉打包后的`map`文件

```
const { override, fixBabelImports } = require('customize-cra');
const { StatsWriterPlugin }  = require("webpack-stats-plugin");
const AntdDayjsWebpackPlugin = require('antd-dayjs-webpack-plugin');
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin
let startTime = Date.now()
if(process.env.NODE_ENV === 'production') process.env.GENERATE_SOURCEMAP = "false"

// 自定义生产环境配置
const productionConfig = (config) =>{
    if(config.mode === 'production'){
        config.plugins.push(...[
            new StatsWriterPlugin({
                fields: null,
                transform: (data) => {
                  let endTime = Date.now()
                  data = {
                    Time: (endTime - startTime)/1000 + 's'
                  }
                  return JSON.stringify(data, null, 2);
                }
            }),
            new BundleAnalyzerPlugin()
        ])
    }
    return config
}
module.exports = override(
  productionConfig,
  fixBabelImports('import', {
    libraryName: 'antd',
    libraryDirectory: 'es',
    style: 'css',
 }),
  addWebpackPlugin(new AntdDayjsWebpackPlugin())
);
```

去掉`map`文件的打包时间，大约60s左右。查看打包后生成的分析图发现`Ant Desigin`的一些组件被重复打包，打包出来一共有13M多。

**4、更细化分包**

在`productionConfig`配置添加，在入口文件添加`vendors`用来分离稳定不变的模块；`common`用来抽离复用模块；`styles`将`css`文件抽离成一个文件；

```
// 针对生产环境修改配置
const productionConfig = (config) =>{
    if(config.mode === 'production'){
        const splitChunksConfig = config.optimization.splitChunks;
        if (config.entry && config.entry instanceof Array) {
            config.entry = {
                main: config.entry,
                vendors: ["react", "react-dom", "react-router-dom", "react-router"]
            }
        } else if (config.entry && typeof config.entry === 'object') {
            config.entry.vendors = ["react", "react-loadable","react-dom", "react-router-dom","react-router"];
        }
      Object.assign(splitChunksConfig, {
            cacheGroups: {
                vendors: {
                    test: "vendors",
                    name: 'vendors',
                    priority:10,
                },
                common: {
                    name: 'common',
                    minChunks: 2,
                    minSize: 30000,
                    chunks: 'all'
                },
                styles: {
                    name: 'styles',
                    test: /\.css$/,
                    chunks: 'all',
                    priority: 9,
                    enforce: true
                }
            }
        })
        config.plugins.push(...[
            new StatsWriterPlugin({
                fields: null,
                transform: (data) => {
                  let endTime = Date.now()
                  data = {
                    Time: (endTime - startTime)/1000 + 's'
                  }
                 return JSON.stringify(data, null, 2);
                }
            }),
            new BundleAnalyzerPlugin()
        ])
    }
    return config
}
```

以上实际打包运行大约35S左右，实际打包后的模块一共2.41M，打包后生成的分析图发现`Ant Design`有个图标库特别大，大约有520kb，但是实际项目中用到的图标特别少。到此不想继续折腾`React`脚手架了，还不如重新配置一套`Webpack`替换脚手架。

**5、总结**

- `React`脚手架配置过重，对于庞大的后台系统不实用
- `Ant Design`的图标库没有按需加载的功能
- 修改`React`脚手架配置太麻烦

**自定义Webpack配置实践**

**1、结果：**替换掉脚手架后，陆陆续续新增路由到100条左右，打包耗时大概20s-30S之间，业务代码打包后1.49M，可以接受。

**2、优化点：**

- 利用`autodll-webpack-plugin`插件，生产环境通过预编译的手段将`Ant` `React` 等稳定的模块全部先抽离出来，只打包编译业务代码。
- `babel-loader` 开启缓存
- 利用`happypack`加快编译速度
- 生产环境不开启`devtool`
- 细化分包
- 针对项目轻量级配置（后台项目，基本只在 Chrome 浏览器下使用）

**问题：**

- 抽离出来的第三方模块大概有3M多，经过`zip`大概也有800多`Kb`,首屏加载比较慢。如果结合`externals`属性将这些静态资源放置到`CDN`上或许加载会更快。

**3、基础配置：**

放于`webpack.base.config.js`文件

1、安装`babel`模块，使用的是`babel7.0`版本。

```
npm i install babel-loader @babel/core @babel/preset-env @babel/preset-react @babel/preset-typescript @babel/plugin-transform-runtime --save-dev
npm i @babel/runtime-corejs3 --save
```

在更目录下创建`babel.config.js` `babel`的配置文件

```
module.exports = function (api) {
    api.cache(true);
    const presets = ["@babel/env","@babel/preset-react","@babel/preset-typescript"];
    const plugins = [
        ["@babel/plugin-transform-runtime", {
          corejs: 3,
        }],
        "@babel/plugin-syntax-dynamic-import",
        "@babel/plugin-proposal-class-properties",
      ];
      if (process.env.NODE_ENVN !== "production") {
        plugins.push(["import", {
            "libraryName": "antd", // 引入库名称
            "libraryDirectory": "es", // 来源,default: lib
            "style": "css" // 全部,or 按需'css'
          }]);
      }
    return {
      presets,
      plugins
    };
  }
```

2、`babel-loader`配置：

```
const os = require('os');
const HappyPack = require('happypack');
const happyThreadPool = HappyPack.ThreadPool({ size: os.cpus().length });
module.exports = {
  .....
module: {
    rules: [
      {
        test: /\.(ts|tsx|js|jsx)$/, 
        exclude: /node_modules/,
        loaders: ['happypack/loader?id=babel']
      }
 },
 plugins: [
    new HappyPack({
      id: 'babel',
      threadPool: happyThreadPool,
      loaders: [{
        loader:'babel-loader',
        options: {
          cacheDirectory: true
        }
      }]
    })
  ]
}
```

3、`mini-css-extract-plugin` 插件打包抽离CSS到单独的文件

```
var MiniCssExtractPlugin = require('mini-css-extract-plugin');
var NODE_ENV = process.env.NODE_ENV
var devMode = NODE_ENV !== 'production';
var utils = require('./utils')
module.exports = {
 ....
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          {
            loader: devMode ? 'style-loader' : MiniCssExtractPlugin.loader,
            options: {
              // only enable hot in development
              hmr: devMode,
              // if hmr does not work, this is a forceful method.
              reloadAll: true,
            },
          },
          "css-loader"
        ],
      },
    ]
  },
  plugins: [
    new MiniCssExtractPlugin({
      //utils.assetsPath 打包后存放的地址
      filename: devMode ? '[name].css' : utils.assetsPath('css/[name].[chunkhash].css'),
      chunkFilename: devMode ? '[name].css' : utils.assetsPath('css/[name].[chunkhash].css'),
      ignoreOrder: false, // Enable to remove warnings about conflicting order
    })
  ]
}
```

4、`html-webpack-plugin` 生成`html` 文件

```
const HtmlWebpackPlugin = require('html-webpack-plugin')
var htmlTplPath = path.join(__dirname, '../public/')
module.exports = {
 ....
  plugins: [
     new HtmlWebpackPlugin({
      filename: 'index.html',
      template: htmlTplPath + 'index.html',
      inject: true,
    })
  ]
}
```

5、`webpack.DefinePlugin`生成业务代码可以获取的变量，可以区分环境

```
const webpack = require('webpack')
module.exports = {
 ....
 plugins: [
    new webpack.DefinePlugin({
      'process.env.NODE_ENV': JSON.stringify(devMode ? 'development' : 'production'),
      'perfixerURL': JSON.stringify('//yzadmin.111.com.cn')
    }),
}
```

**4、开发环境配置：**

放于`webpack.development.config.js`文件

 

```
var path = require('path');
var webpack = require('webpack');
var merge = require('webpack-merge');
var FriendlyErrorsPlugin = require('friendly-errors-webpack-plugin')
var entryScriptPath = path.join(__dirname, '../src/')
var base = require('./webpack.base.config');
module.exports = merge(base, {
  entry: {
    app: [entryScriptPath+'index'] // Your appʼs entry point
  },
  output: {
    path: path.join(__dirname, '../dist/'),
    filename: '[name].js',
    chunkFilename: '[name].[chunkhash].js'
  },
  module: {
  },
  plugins: [
    new webpack.ContextReplacementPlugin(/moment[\/\\]locale$/, /zh-cn/),
    new webpack.HotModuleReplacementPlugin(),
    new FriendlyErrorsPlugin(),
  ]
});
```

`start.js`文件,用于`npm start`启动本地服务

```
var webpack = require('webpack');
var opn = require('opn')
var WebpackDevServer = require('webpack-dev-server');
var config = require('./webpack.development.config');
config.entry.app.unshift("webpack-dev-server/client?http://127.0.0.1:9000/", "webpack/hot/dev-server");
new WebpackDevServer(webpack(config), {
  publicPath: config.output.publicPath,
  hot: true,
  historyApiFallback: {
    index: '/public'
  }
}).listen(9000, '127.0.0.1', function (err, result) {
  if (err) {
    return console.log(err);
  }
  opn('http://127.0.0.1:9000/')
});
```

**5、生产环境配置：**

放于`webpack.production.config.js`文件

```
const path = require('path')
const merge = require('webpack-merge')
const baseWebpackConfig = require('./webpack.base.config');
const config = require('./webpack.env.config')
const utils = require('./utils')
const AutoDllPlugin = require('autodll-webpack-plugin');
const TerserJSPlugin = require('terser-webpack-plugin');
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin');
function resolve(dir) {
  return path.join(__dirname, '..', dir)
}
const webpackConfig = merge(baseWebpackConfig, {
  module: {
  },
  devtool: config.build.productionSourceMap ? '#source-map' : false,
  entry: {
    app: resolve('src/index'),
  },
  output: {
    path: config.build.assetsRoot,
    filename: utils.assetsPath('js/[name].[chunkhash].js'),
    chunkFilename: utils.assetsPath('js/[name].[chunkhash].js')
  },
  optimization: {
    minimizer: [new TerserJSPlugin({}), new OptimizeCSSAssetsPlugin({})],
    splitChunks: {
      cacheGroups: {
        common: {
          test: /[\\/]src[\\/]/,
          name: 'common',
          chunks: 'all',
          priority: 2,
          minChunks: 2,
        },
        // 分离css到一个css文件
        styles: {
          name: 'styles',
          test: /\.css$/,
          chunks: 'all',
          priority: 9,
          enforce: true,
        }
      }
    },
    runtimeChunk: {
      name:"manifest"
    }
  },
  plugins: [
    new AutoDllPlugin({
      inject: true, // will inject the DLL bundles to index.html
      filename: '[name].dll.js',
      path: './dll',
      entry: {
        // 第三方库
        react: ["react","react-dom","react-router", "react-router-dom",'react-loadable'],
        antd:  ['antd/es'],
        untils:  ['qs','qrcode'],
        plugins:['braft-editor','js-export-excel']
      }
    })
  ]
})

if (config.build.productionGzip) {
  const CompressionWebpackPlugin = require('compression-webpack-plugin')

  webpackConfig.plugins.push(
    new CompressionWebpackPlugin({
      filename: '[path].gz[query]',
      algorithm: 'gzip',
      test: new RegExp(
        '\\.(' +
        config.build.productionGzipExtensions.join('|') +
        ')$'
      ),
      threshold: 10240,
      minRatio: 0.8
    })
  )
}

if (config.build.bundleAnalyzerReport) {
  const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin
  webpackConfig.plugins.push(new BundleAnalyzerPlugin())
}
module.exports = webpackConfig
```

`build.js` 用于`npm run build`构建

```
process.env.NODE_ENV = 'production'

var ora = require('ora')
var path = require('path')
var chalk = require('chalk')
var shell = require('shelljs')
var webpack = require('webpack')
var config = require('./webpack.env.config')
var webpackConfig = require('./webpack.production.config')

var spinner = ora('building for production...')
spinner.start()
var assetsPath = path.join(config.build.assetsRoot, config.build.assetsSubDirectory)
shell.config.silent = true
shell.rm('-rf', assetsPath)
shell.mkdir('-p', assetsPath)
shell.cp('-R', 'static/*', assetsPath)
shell.config.silent = false

webpack(webpackConfig, function (err, stats) {
  spinner.stop()
  if (err) throw err
  process.stdout.write(stats.toString({
    colors: true,
    modules: false,
    children: false,
    chunks: false,
    chunkModules: false
  }) + '\n\n')

  console.log(chalk.cyan('  Build complete.\n'))
  console.log(chalk.yellow(
    '  Tip: built files are meant to be served over an HTTP server.\n' +
    '  Opening index.html over file:// won\'t work.\n'
  ))
})
```

修改`package.json`

```
"scripts": {
    "start": "node webpack/start.js",
    "build": "node webpack/build.js"
  },
```

**6、总结**

- 通过本次实践大致对`Webpack`有了初步了解，但关于`Webpack`的内部原理没有仔细探究过
- 对一些脚手架做配置修改的前提是需要了解`Webpack`基础内容
- 优化一个项目首先需要知道是大致什么原因造成的，可以利用的工具有`speed-measure-webpack-plugin`,`webpack-bundle-analyzer`等
- 项目虽然加入了`typeScript`但并没有很好的利用`typeScript`

### 最后

`Webpack`配置`demo`地址：<https://github.com/ccokl/webpack/tree/master/_webpack_for_ant>
