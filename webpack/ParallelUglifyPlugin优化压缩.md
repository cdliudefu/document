webpack默认提供了UglifyJS插件来压缩JS代码，但是它使用的是单线程压缩代码，也就是说多个js文件需要被压缩，它需要一 个个文件进行压缩。所以说在正式环境打包压缩代码速度非常慢(因为压缩JS代码需要先把代码解析成用Object抽象表示的AST语法树，再去应用各种规 则分析和处理AST，导致这个过程耗时非常大)。
使用 UglifyJS 压缩代码如下：

```
module.exports = {
  plugins: [
    new webpack.optimize.UglifyJsPlugin({
      sourceMap: true,
      compress: {
        warnings: false
      }
    }),
  ]
}
```

上一节介绍的HappyPack的思想是使用多个子进程去解析和编译JS,css,等，这样就可以并行处理多个子任务，多个子任务完成后，再将结果 发到主进程中，有了这个思想后，因此 ParallelUglifyPlugin  插件就产生了，当webpack有多个JS文件需要输出和压缩时候，原来会使用UglifyJS去一个个压缩并且输出，但是 ParallelUglifyPlugin插件则会开启多个子进程，把对多个文件压缩的工作分别给多个子进程去完成，但是每个子进程还是通过 UglifyJS去压缩代码。无非就是变成了并行处理该压缩了，并行处理多个子任务，效率会更加的提高。

**1. 安装 webpack-parallel-uglify-plugin 插件**
命令如下安装：

```
npm i -D webpack-parallel-uglify-plugin
```

然后在webpack.config.js 配置代码如下：

```
// 引入 ParallelUglifyPlugin 插件
const ParallelUglifyPlugin = require('webpack-parallel-uglify-plugin');

module.exports = {
  plugins: [
    // 使用 ParallelUglifyPlugin 并行压缩输出JS代码
    new ParallelUglifyPlugin({
      // 传递给 UglifyJS的参数如下：
      uglifyJS: {
        output: {
          /*
           是否输出可读性较强的代码，即会保留空格和制表符，默认为输出，为了达到更好的压缩效果，
           可以设置为false
          */
          beautify: false,
          /*
           是否保留代码中的注释，默认为保留，为了达到更好的压缩效果，可以设置为false
          */
          comments: false
        },
        compress: {
          /*
           是否在UglifyJS删除没有用到的代码时输出警告信息，默认为输出，可以设置为false关闭这些作用
           不大的警告
          */
          warnings: false,

          /*
           是否删除代码中所有的console语句，默认为不删除，开启后，会删除所有的console语句
          */
          drop_console: true,

          /*
           是否内嵌虽然已经定义了，但是只用到一次的变量，比如将 var x = 1; y = x, 转换成 y = 5, 默认为不
           转换，为了达到更好的压缩效果，可以设置为false
          */
          collapse_vars: true,

          /*
           是否提取出现了多次但是没有定义成变量去引用的静态值，比如将 x = 'xxx'; y = 'xxx'  转换成
           var a = 'xxxx'; x = a; y = a; 默认为不转换，为了达到更好的压缩效果，可以设置为false
          */
          reduce_vars: true
        }
      }
    }),
  ]
}
```

在通过 new ParallelUglifyPlugin() 实列化时，支持以下参数配置如下：

**test:** 使用正则去匹配哪些文件需要被 ParallelUglifyPlugin 压缩，默认是 /.js$/.
**include:** 使用正则去包含被 ParallelUglifyPlugin 压缩的文件，默认为 [].
**exclude:** 使用正则去不包含被 ParallelUglifyPlugin 压缩的文件，默认为 [].
**cacheDir:** 缓存压缩后的结果，下次遇到一样的输入时直接从缓存中获取压缩后的结果并返回，cacheDir 用于配置缓存存放的目录路径。默认不会缓存，想开启缓存请设置一个目录路径。

**workerCount：**开启几个子进程去并发的执行压缩。默认是当前运行电脑的 CPU 核数减去1。
**sourceMap：**是否为压缩后的代码生成对应的Source Map, 默认不生成，开启后耗时会大大增加，一般不会将压缩后的代码的
sourceMap发送给网站用户的浏览器。
uglifyJS：用于压缩 ES5 代码时的配置，Object 类型，直接透传给 UglifyJS 的参数。
uglifyES：用于压缩 ES6 代码时的配置，Object 类型，直接透传给 UglifyES 的参数。

因此 使用 ParallelUglifyPlugin 实列会有以下参数配置项：

```
new ParallelUglifyPlugin({
  uglifyJS: {},
  test: /.js$/g,
  include: [],
  exclude: [],
  cacheDir: '',
  workerCount: '',
  sourceMap: false
});
```
