我们在项目进行打包后，会将开发中的多个文件代码打包到一个文件中，并且经过压缩，去掉多余的空格，且babel编译化后，最终会用于线上环境，那么这样
处理后的代码和源代码会有很大的差别，当有bug的时候，我们只能定位到压缩处理后的代码位置，无法定位到开发环境中的代码，对于开发不好调式，因此
sourceMap出现了，它就是为了解决不好调式代码问题的。

**2.1. eval**
  eval 会将每一个module模块，执行eval，执行后不会生成sourcemap文件，仅仅是在每一个模块后，增加sourceURL来关联模块处理前后对应的关系。在webpack中配置devtool: 'eval', 如下打包后的代码：

```
(function(modules) { // webpackBootstrap
  "use strict";
  eval("__webpack_require__.r(__webpack_exports__);\n/* harmony export (binding) */ __webpack_require__.d(__webpack_exports__, \"default\", function() { return printMe; });\n\nfunction printMe() {\n  console.log('11111111');\n}\n\n//# sourceURL=webpack:///./js/demo1.js?");

  /***/ "./js/main.js":
  /*!********************!*\
    !*** ./js/main.js ***!
    \********************/
  /*! no exports provided */
  /***/ 
  (function(module, __webpack_exports__, __webpack_require__) {
    "use strict";
    eval("__webpack_require__.r(__webpack_exports__);\n/* harmony import */ var _demo1_js__WEBPACK_IMPORTED_MODULE_0__ = __webpack_require__(/*! ./demo1.js */ \"./js/demo1.js\");\n\n\nconsole.log('main.js');\n\n//# sourceURL=webpack:///./js/main.js?");
  })
})
```

如上打包后的代码，每一个打包后的模块后面都增加了包含sourceURL的注释，sourceURL的值是压缩前存放的代码的位置，这样就通过sourceURL关联了压缩前后的代码。并没有为每一个模块生成相对应的sourcemap。

**优点是：**打包速度非常快，因为不需要生成sourcemap文件。
**缺点是：**由于会映射到转换后的代码，而不是映射到原始代码，所以不能正确的显示行数。

**2.2 source-map**

在webpack中配置加上 devtool: 'source-map' 配置完成后，source-map会为每一个打包后的模块生成独立的sourcemap文件，比如在package.json文件中 这样配置：

```
"scripts": {
  "build": "webpack --progress --colors --devtool source-map"
}
```

然后运行 npm run build 后，会在dist目录下生产map文件。我们继续打包后的代码如下：

```
(function(module, __webpack_exports__, __webpack_require__) {

  "use strict";
  __webpack_require__.r(__webpack_exports__);
  /* harmony import */ var _demo1_js__WEBPACK_IMPORTED_MODULE_0__ = __webpack_require__(/*! ./demo1.js */ "./js/demo1.js");
  __webpack_require__(/*! ../styles/main.css */ "./styles/main.css");
  console.log('main.js');
/***/ }),

  /***/ "./styles/main.css":
  /*!*************************!*\
    !*** ./styles/main.css ***!
    \*************************/
  /*! no static exports found */
  /***/ (function(module, exports) {

  // removed by extract-text-webpack-plugin

  /***/ })

  /******/ });
  //# sourceMappingURL=bundle.js.map
```

如上打包后的代码最后面一句代码是 //# sourceMappingURL=bundle.js.map ，同时在dist目录下会针对每一个模块生成响应的 .map文件，
比如我们在dist目录中会生成 bundle.js.map文件，我们可以打开看下这个文件代码会如下：

```
{
  "version":3,
  "sources":[
    "webpack:///webpack/bootstrap","webpack:///./js/demo1.js",
    "webpack:///./js/main.js","webpack:///./styles/main.css"
  ],
  "names":["printMe","console","log","require"],
  "mappings":";AAAA;AACA;;AAEA;AACA...",
  "file":"bundle.js",
  "sourcesContent":[],
  "sourceRoot": ""
}
```

上面生成后的map文件是一个javascript对象，可以被解析器读取，它主要有以下几个属性：

**version:** Source Map 的版本，目前为3.
**sources:** 转换前的文件，该项是一个数组，表示可能存在多个文件合并.
**names:** 转换前的所有变量名和属性名。
**mappings:** 记录位置信息的字符串。
**sourcesContent:** 转换前的文件内容列表，与sources列表依次对应。
**sourceRoot:** 转换前的文件所在的目录，如果与转换前的文件在同一个目录，该项为空。

chrome和firefox如何使用Source Map呢？

1**. 开启开发者工具**

使用快捷键 option + command + i; 或者在 菜单栏选择视图 -> 开发者 -> 开发者工具。

**2. 打开设置**

点击右上角的三个点的图标，选择Settings, 如下图所示：

![img](https://images2018.cnblogs.com/blog/561794/201808/561794-20180812211901352-1149301449.png)

**3. 开启Source Map**
在Sources中，选中 Enable Javascript source maps 如下图所示

![img](https://images2018.cnblogs.com/blog/561794/201808/561794-20180812211921317-864630986.png)

开启完成后，我们在 package.json 配置如下代码：

```
scripts: {
  "dev": "webpack-dev-server --progress --colors --devtool source-map --hot --inline",
}
```

然后在main.js 代码中，添加如下代码：

```
require('../styles/main.css');
import demo1Func from './demo1.js';

console.log('main.js');
console.log(a)
```

如上a未定义，直接打印a，肯定会报错的。我们在命令行中 运行 npm run dev 后，打开页面会发现报错，报错如下：

![img](https://images2018.cnblogs.com/blog/561794/201808/561794-20180812212010952-1233881221.png)

然后我们点击 main.js:5 后，会进入main.js代码内，如下图：

![img](https://images2018.cnblogs.com/blog/561794/201808/561794-20180812212026503-1317935080.png)

**2.3 inline（比如 inline-source-map）**
该属性不会生成独立的 .map文件，而是将 .map文件以dataURL的形式插入。

如下代码：

```
/***/ (function(module, __webpack_exports__, __webpack_require__) {

"use strict";
__webpack_require__.r(__webpack_exports__);
/* harmony import */ var _demo1_js__WEBPACK_IMPORTED_MODULE_0__ = __webpack_require__(/*! ./demo1.js */ "./js/demo1.js");
__webpack_require__(/*! ../styles/main.css */ "./styles/main.css");


console.log('main.js');
console.log(a);

/***/ }),

/***/ "./styles/main.css":
/*!*************************!*\
  !*** ./styles/main.css ***!
  \*************************/
/*! no static exports found */
/***/ (function(module, exports) {

// removed by extract-text-webpack-plugin

/***/ })

/******/ });
//# sourceMappingURL=data:application/json;charset=utf-8;base64,eyJ2ZXJzaW9uIjozLCJzb3VyY2Vz.....
```

inline-source-map 使用缺点：它会使得bundle.js文件变得非常大，因为它需要把 sourceMappingURL 以dataurl的形式插入到bundle.js里面去。如下图所示：

![img](https://images2018.cnblogs.com/blog/561794/201808/561794-20180812212122947-1266214822.png)

**2.4 cheap(如：cheap-source-map)**

该属性在打包后同样会为每一个文件模块生成 .map文件，但是与source-map的区别在于cheap生成的 map文件会忽略原始代码中的列信息；
比如生成后的bundle.js.map中的mappings的代码如下：

```
"mappings":";AAAA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;AACA;;;;;;;;;;;;;AClFA;AAAA;AAAA;AACA;AACA;AACA;;;;;;;;;;
```

如上可以看到，它不会生成列的信息，有逗号就表示包含了列信息。增加该属性后，cheap就不会生成列信息，调式代码列信息没有什么用，因此使用cheap后，文件大小相对于source-map来讲，bundle.js 文件会变得更小。

如下图使用的是 source-map 生成的bundle.js.map 文件, 会包含列的信息，如下图所示：

![img](https://images2018.cnblogs.com/blog/561794/201808/561794-20180812212227040-564428730.png)

使用cheap属性后，也不会有loader模块之间对应的sourcemap，因为webpack打包最终会将所有的非js资源，通过 loader形式转换成js资源，比如 vue 中的文件，xx.vue -> vue-loader转换 -> js -> 压缩  -> 压缩后的js

所以说如果没有loader之间的sourcemap文件的话，那么在debug的时候，定义到压缩前的js中的时候，不能跟踪到vue中。

**2.5 module(如：cheap-module-source-map)**
该属性的配置也是生成一个没有列的信息的sourceMaps文件，同时loader的sourcemap也被简化成为只包含对应行的。

[回到顶部](https://www.cnblogs.com/tugenhua0707/p/9464984.html#_labelTop)

三：开发环境和线上环境如何选择sourceMap?

从上面的eval, inline, source-map, cheap, module中可以看到，各自属性值代表打包后的具体含义，因此我们可以分析下开发环境和正式环境要如何选择sourceMap；我们可以从如下几个方面考虑：

**1. 源代码中的列信息是没有任何作用，因此我们打包后的文件不希望包含列相关信息，只有行信息能建立打包前后的依赖关系。因此不管是开发环境或生产环境，我们都希望添加cheap的基本类型来忽略打包前后的列信息。**

**2. 不管是开发环境还是正式环境，我们都希望能定位到bug的源代码具体的位置，比如说某个vue文件报错了，我们希望能定位到具体的vue文件，因此我们也需要module配置。**

**3. 我们需要生成map文件的形式，因此我们需要增加 source-map属性。**

**4. 我们介绍了eval打包代码的时候，知道eval打包后的速度非常快，因为它不生成map文件，但是可以对eval组合使用 eval-source-map使用会将map文件以DataURL的形式存在打包后的js文件中，比如如下：**

![img](https://images2018.cnblogs.com/blog/561794/201808/561794-20180812212332038-199268065.png)

它的效果类似于inline的效果，因此在正式环境中不要使用 eval-source-map, 因为它会增加文件的大小，但是在开发环境中，可以试用下，因为他们打包的速度很快。

因此我们可以总结如下：

**在开发环境中我们可以使用** 

```
module.exports = {
  devtool: 'cheap-module-eval-source-map'
}
```

**在正式环境中我们可以使用**  

```
module.exports = { 
  devtool: 'cheap-module-source-map';
}
```

如上是总结的在开发环境和正式环境使用的sourcemap进行打包的简单思路。
