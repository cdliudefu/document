webpakc打包慢可以：

1. 排除不用的第三方包，
2. 使用`npm run build --report`去观察各个文件的大小，使用externals方案及引入cdn, 或者按需加载（比如： element-ui）
3. 处理 loader 时，配置 include，exclude，缩小 loader 检查范围
4. webpack-parallel-uglify-plugin插件, 减少的构建时间
5. eslint不检测或者缩小检测范围
6. 使用gzip压缩
```js
1、因webpack提供的UglifyJS插件采用单线程压缩，速度很慢。所以将此插件替换为webpack-parallel-uglify-plugin插件，此插件可以并行运行UglifyJS插件，可有效减少构建时间。

//首先下载插件 npm i -D webpack-parallel-uglify-plugin
//修改 webpack.prod.conf.js
//将引入的UglifyJS 和 作用代码注释掉 并替换成下方代码
const ParallelUglifyPlugin = require('webpack-parallel-uglify-plugin')
new ParallelUglifyPlugin({
          cacheDir: '.cache/',
          uglifyJS:{
              output: {
                  comments: false
              },
              compress: {
                  warnings: false,
                  drop_debugger: true,
                  drop_console: false
              }
          }
      }),
2、由于运行在node.js之上的webpack是单线程模型，所以webpack做事只能一件一件去做。HappyPack可以让webpack在同一时间处理多个任务，把任务分解给多个子进程去并发执行，处理完之后将结果发给主进程

//首先下载插件  npm install --save-dev happypack
//修改 webpack.base.conf.js
const HappyPack = require('happypack')
const os = require('os')
let happyThreadPool = HappyPack.ThreadPool({ size: os.cpus().length });
//加入此插件
plugins:[
      new HappyPack({
          id:'babel',
          loaders:['babel-loader?cacheDirectory=true'],
          threadPool:happyThreadPool
      })
  ],
//将js loader作用代码替换
// loader: 'babel-loader'    替换成下方loader
 loader: 'happypack/loader?id=babel',
3、使用dll plugin => dynamic link library plugin，其中UglifyJS插件也可进行替换，详细请看第一条。

//首先在build文件下创建一个js文件,webpack.dll.conf.js，并写入下方代码
const path = require("path")
const webpack = require("webpack")
const UglifyJsPlugin = require('uglifyjs-webpack-plugin')
const pkg = require('../package')//引入package文件，目的就是找到依赖

module.exports = {
    // 想要打包的模块的数组
    entry: {
        // vendor: ['axios', 'vue-router', 'vue','weixin-js-sdk','element-ui','vuex']
        vendor:Object.keys(pkg.dependencies)//取出所有依赖单独打包
    },
    output: {
        path: path.join(__dirname, '../static/js'), // 打包后文件输出的位置
        filename: '[name].dll.js',//生成的文件名字 默认为vendor.dll.js
        library: '[name]_library'//生成文件的映射关系，与下面的DLLPlugin配置相对应
    },
    plugins: [
        new webpack.DllPlugin({//生成一个json文件 里面是关于dll.js的配置信息
            path: path.join(__dirname, '.', '[name]-manifest.json'),
            name: '[name]_library',//与上面output中的配置对应
            context: __dirname//上下文环境路径，必须填写，为了与DLLReferencePlugin存在于同一上下文中，否则undefined
        }),
        // 压缩打包的文件
        new UglifyJsPlugin({
            uglifyOptions: {
            compress: {
                  warnings: false
            }
          },
      }),
    ]
}

//然后在webpack.prod.conf.js和webpack.dev.conf.js中加入当前插件
new webpack.DllReferencePlugin({
          context: __dirname,//与DllPlugin中的context保持一致
          /*这个地址对应webpack.dll.conf.js中生成的那个json文件的路径，这样webpack打包的时候
          会检测当前文件中的映射，不会把已经存在映射的包再次打包进bundle.js */
          manifest: require('./vendor-manifest.json')
      }),

//在package.json中新加一条npm命令，执行webpack.dll.conf.js文件
"dll": "webpack --config ./build/webpack.dll.conf.js"
//tips：每次添加新依赖后，一定要运行npm run dll这个命令一次。

//最后在index.html中引入static/js/vendor.dll.js文件
<script src="static/js/vendor.dll.js"></script>
```

可以自动化, html-webpack-tags-plugin 插件在生产环境时自动插入script标签
可以根据版本生成hash加在标签链接上

```js
// 自动externals配置, 打包阶段走cdn
        if (process.env.NODE_ENV === 'production') {
          const [webpackExternals, scripts] = formatExternal(externals)
          debug(`use externals ${JSON.stringify(webpackExternals)}`)
          scripts.forEach((url) => debug(`external link: ${url}`))
          chain.externals(webpackExternals)
          chain.plugin('InjectExternalsToHtml').use(HtmlWebpackTagsPlugin, [{ append: false, usePublicPath: false, scripts }])
        }
```

