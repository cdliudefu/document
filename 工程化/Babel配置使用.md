### babel版本变化

现在大部分使用babel的项目都是用的babel7.x了，但是还是需要去了解一下babel每个版本的一个重大变化，本篇主要是简单来记录下我了解的babel几个版本的重大变化

### babel5.x

我看官网babel5已经没有了，，，但是在我了解的过程中，有好多博客都说有babel5，，，差不多就是说babel5就是一个全家桶，类似于vue-cli那样的东西，包括各种包和插件，babel5大概就是让你通过一次安装，尽可能的可以达到所有你想要的东西。

### babel6.x

babel5升级到babel6最主要的原因可能就是之前都让大家一次npm就把所有的东西都安装了，但是有的东西是不需要的，导致包的体积很大，所有babel6就把一些包和插件都拆了出来可以去独立安装，我的猜测是这样的。。。

babel6的改变：

- 把之前的一次性安装拆成了一些核心安装包：babel-core、babel-cli、babel-polyfill等等。。。
- 插件化，根据自己的需求去手动的添加plugin，这点我觉得可以，一切都讲究个按需加载嘛；
- 可以使用.babelrc 文件来配置babel，方便自定义的配置，难道说babel5只能在package.json中配置？知道的大佬可以给我评论下0.0
- 添加了预设preset，这个预设在我学到的时候再细说，听说很牛逼；
- babel5可以直接把require的转成import，babel6就不行，如果用了require需要加上.default才可以，不过我 觉得直接改成import就好了，有次我也遇到了这个问题，引入的一个第三方包里面有用 require，我的解决方案是引入了一个叫@babel/plugin-transform-modules-commonjs插件就把require 编译成import了；
- 差不多了，babel6先了解这么多，还想了解的可以去babel官网看看。

### babel7.x

来了来了，最牛逼的babel7

- 安装包都使用npm的scope包@balbel/xxx，不会再出现和别的包名冲突；
- 所有阶段预设state-x均已弃用，使用plugin代替，官网也有一个升级方案，可以安装babel-upgrade包，它会把你之前有用过的stage-x自动换成对应的plugin，我没用过，有需求的可以去看看

```
https://github.com/babel/babel/blob/master/packages/babel-preset-stage-0/README.md
```

- 更强大的env
- 之前有的插件是带着类似于es2015这样的(等等)，比如@babel/plugin-transform-es2015-classes，现在直接写@babel/plugin-transform-classes就可以
- 有些包被删了，比如babel-core/register.js，应直接用@babel/register
- 差不多先这些

#### 简介

babel是一个广泛使用的转码器，可以将ES6代码转化为ES5代码，从而在现有环境执行，这意味着，你可以现在就用ES6编写程序，而不用担心现有环境是否支持。

Babel是一个工具链，主要用于在当前和较旧的浏览器或环境中将ECMAScript 2015+代码转换为JavaScript的向后兼容版本。以下是Babel可以为您做的主要事情：

- 转换语法
- 目标环境中缺少的Polyfill功能（通过[@babel/polyfill](https://babeljs.io/docs/en/babel-polyfill)）
- 源代码转换（codemods）
- 和更多！（查看这些[视频](https://babeljs.io/videos.html)以获取灵感）

**可调试的**

源映射支持，因此您可以轻松调试已编译的代码。

**符合规格**

Babel尝试尽可能合理地遵守ECMAScript标准。作为性能的折衷方案，它可能还具有一些特定的选项，使其更符合规范。

**紧凑**

Babel尝试使用尽可能少的代码，而不依赖庞大的运行时。
 在某些情况下，这可能很难做到，并且针对特定转换的“松散”选项可能会折衷规范对可读性，文件大小和速度的要求。

### babel版本安装的对应：
webpack4.x | babel-loader 8.x | babel 7.x
```bat
npm install -D babel-loader @babel/core @babel/preset-env webpack
```
webpack4.x | babel-loader 7.x | babel 6.x
```bat
npm install -D babel-loader@7 babel-core babel-preset-env webpack
```
如果遇到版本不匹配有以下解决：
1、 回退低版本
```
npm install -D babel-loader@7 babel-core babel-preset-env
```
2、更新到最高版本:
```
npm install -D babel-loader @babel/core @babel/preset-env webpack
```


#### babel之配置文件.babelrc

在Babel执行编译的过程中，会从项目的根目录下的 .babelrc文件中读取配置。.babelrc是一个json格式的文件。

　**简单配置预览：**

```
{
    "plugins": [],  // 插件配置
    "presets": []   // 预设配置
}

// 这里 .babelrc 配置的


// 官网建议用 module 的形式来配置，bable.config.js

const presets = []
const plugins = []
module.exports =  { presets, plugins }
```

　　**配置介绍：**

　　　　presets ：是某一类 plugin 的集合，包含了某一类插件的所有功能。

　　　　plugin ： 将某一种需要转化的代码，转为浏览器可以执行代码。

　　**编译的执行顺序：**

　　　　1、执行 plugins 中所有的插件

　　　　2、plugins 的插件，按照顺序依赖编译

　　　　3、所有 plugins 的插件执行完成，在执行 presets 预设。

　　　　4、presets 预设，按照倒序的顺序执行。(从最后一个执行)

　　　　5、完成编译。

在.babelrc配置文件中，主要是对预设(presets) 和 插件(plugins) 进行配置。.babelrc配置文件一般为如下：

```
{
  "plugins": [
     [
      "transform-runtime",
      {
        "polyfill": false
      }
     ]
   ],
   "presets": [
     [
       "env",
       {
         "modules": false
       }
     ],
     "stage-2",
     "react"
  ]
}
```

**2.1 plugins**
该属性是告诉babel要使用那些插件，这些插件可以控制如何转换代码。

**1. 理解 babel-polyfill 和 babel-runtime 及 babel-plugin-transform-runtime**

   Babel默认只转换新的javascript语法，而不转换新的API，比如 Iterator, Generator, Set,  Maps, Proxy, Reflect,Symbol,Promise 等全局对象。以及一些在全局对象上的方法(比如  Object.assign)都不会转码。
比如说，ES6在Array对象上新增了Array.form方法，Babel就不会转码这个方法，如果想让这个方法运行，必须使用 babel-polyfill来转换等。

因此：babel-polyfill和babel-runtime就是为了解决新的API与这种全局对象或全局对象方法不足的问题，因此可以使用这两个插件可以转换的。

**那么他们两者的区别是什么？**
**babel-polyfill** 的原理是当运行环境中并没有实现的一些方法，babel-polyfill会做兼容。
**babel-runtime** 它是将es6编译成es5去执行。我们使用es6的语法来编写，最终会通过babel-runtime编译成es5.也就是说，不管浏览器是否支持ES6，只要是ES6的语法，它都会进行转码成ES5.所以就有很多冗余的代码。

**babel-polyfill** 它是通过向全局对象和内置对象的prototype上添加方法来实现的。比如运行环境中不支持Array.prototype.find 方法，引入polyfill, 我们就可以使用es6方法来编写了，但是缺点就是会造成全局空间污染。

**babel-runtime:** 它不会污染全局对象和内置对象的原型，比如说我们需要Promise，我们只需要import Promise from 'babel-runtime/core-js/promise'即可，这样不仅避免污染全局对象，而且可以减少不必要的代码。

虽然 babel-runtime 可以解决  babel-polyfill中的避免污染全局对象，但是它自己也有缺点的，比如上，如果我现在有100个文件甚至更多的话，难道我们需要一个个文件加 import Promise from 'babel-runtime/core-js/promise'  吗？那这样肯定是不行的，因此这个时候出来一个 叫 babel-plugin-transform-runtime，
它就可以帮助我们去避免手动引入 import的痛苦，并且它还做了公用方法的抽离。比如说我们有100个模块都使用promise，但是promise的polyfill仅仅存在1份。
这就是 babel-plugin-transform-runtime 插件的作用。

**2. 理解 babel-plugin-transform-runtime 的配置一些选项**

因此通过上面的理解，我们可以对 transform-runtime 通过如下配置 plugins; 如下代码：

```
{
  'plugins': [
    [
      'transform-runtime', 
      {
        'helpers': false,
        'polyfill': false,
        'regenerator': true,
        'moduleName': 'babel-runtime'
      }
    ]
  ]
}
```

配置项可以看官网，[查看官网](http://babeljs.io/docs/en/babel-plugin-transform-runtime/#helpers)

**helpers: 默认值为true，**表示是否开启内联的babel helpers(即babel或者环境本来存在的某些对象方法函数)如：extends，etc这样的
在调用模块名字时将被替换名字。

**polyfill：默认值为true**，表示是否把内置的东西(Promise, Set, Map)等转换成非全局污染的。

**regenerator：默认值为true，**是否开启generator函数转换成使用regenerator runtime来避免污染全局域。

**moduleName：默认值为 babel-runtime，**当调用辅助 设置模块（module）名字/路径.
比如如下这样设置：

```
{
  "moduleName": "flavortown/runtime"
}
```

import引入文件如下这个样子：

```
import extends from 'flavortown/runtime/helpers/extends';
```

**3 presets**
presets属性告诉Babel要转换的源码使用了哪些新的语法特性，presets是一组Plugins的集合。

**3.1 理解 babel-preset-env** 

比如：

**babel-preset-es2015: 可以将es6的代码编译成es5.**
**babel-preset-es2016: 可以将es7的代码编译为es6.**
**babel-preset-es2017: 可以将es8的代码编译为es7.**
**babel-preset-latest: 支持现有所有ECMAScript版本的新特性。**

举个列子，比如我们需要转换es6语法，我们可以在 .babelrc的plugins中按需引入一下插件，比如：
check-es2015-constants、es2015-arrow-functions、es2015-block-scoped-functions等等几十个不同作用的plugin：
那么配置项可能是如下方式：

```
// .babelrc
{
  "plugins": [
    "check-es2015-constants",
    "es2015-arrow-functions",
    "es2015-block-scoped-functions",
    // ...
  ]
}
```

但是Babel团队为了方便，将同属ES2015的几十个Transform  Plugins集合到babel-preset-es2015一个Preset中，这样我们只需要在.babelrc的presets加入es2015一 个配置就可以完成全部ES2015语法的支持了：
如下配置：

```
// .babelrc
{
  "presets": [
    "es2015"
  ]
}
```

但是我们随着时间的推移，将来可能会有跟多的版本插件,比如 bebel-preset-es2018,.... 等等。
因此 babel-preset-env 出现了，它的功能类似于 babel-preset-latest，它会根据目标环境选择不支持的新特性来转译。

首先需要在项目中安装，如下命令：

```
npm install babel-preset-env --save-dev
```

在.babelrc配置文件中 可以如下简单的配置：

```
{
  "presets": ['env']
}
```

我们还可以仅仅配置项目所支持的浏览器的配置

\1. 支持每个浏览器最后两个版本和safari大于等于7版本所需的polyfill代码转换，我们可以如下配置：

```
{
  'presets': [
    ['env', {
      'target': {
        'browsers': ['last 2 versions', 'safari >= 7']
      }
    }]
  ]
}
```

\2. 支持市场份额超过5%的浏览器，可以如下配置：

```
{
  'presets': [
    ['env', {
      'target': {
        'browsers': '> 5%'
      }
    }]
  ]
}
```

\3. 指定浏览器版本，可以如下配置：

```
{
  'presets': [
    ['env', {
      'target': {
        'chrome': 56
      }
    }]
  ]
}
```

**Node.js**
如果通过Babel编译Node.js代码的话，可以设置 "target.node" 是 'current', 含义是 支持的是当前运行版本的nodejs。
如下配置代码：

```
{
  "presets": [
    ["env", {
      "targets": {
        "node": "current"
      }
    }]
  ]
}
```

**理解 babel-preset-env 中的选项配置：**
**1. targets: {[string]: number | string }, 默认为{};**
含义是支持一个运行环境的对象，比如支持node版本；可以如下配置： node: '6.0';
运行环境: chrome, opera, edge, firefox, safari, ie, ios, android, node, electron

**2. targets.browsers <Array | string>**
支持浏览器的配置项，该配置项使用方式可以到 browserslist来查询[（https://github.com/browserslist/browserslist）](https://github.com/browserslist/browserslist)
比如上面的 支持每个浏览器最后两个版本和safari大于等于7版本。如上配置。

**3. modules** 
该参数的含义是：启用将ES6模块语法转换为另一种模块类型。将该设置为false就不会转换模块。默认为 'commonjs'.
该值可以有如下：
'amd' | 'umd' | 'systemjs' | 'commonjs' | false

我们在项目中一般会看到如下配置，设置modules: false, 如下代码配置：

```
"presets": [
   'env',
   {
     'modules': false
   }
]
```

这样做的目的是：以前我们需要使用babel来将ES6的模块语法转换为AMD,  CommonJS，UMD之类的模块化标准语法，但是现在webpack都帮我做了这件事了，所以我们不需要babel来做，因此需要在babel配置项 中设置modules为false，因为它默认值是commonjs, 否则的话，会产生冲突。

**4. loose, 该参数值默认为false**。
含义是：允许它们为这个 preset 的任何插件启用”loose” 转换。

**5. include: 包含一些插件，默认为 [];**
比如包含箭头函数，可以如下配置：

```
{
  "presets": [
    ["env", {
      "targets": {
        "browsers": ["last 2 versions", "safari >= 7"]
      },
      "include": ["transform-es2015-arrow-functions", "es6.map"]
    }]
  ]
}
```

**6. exclude； 排除哪些插件，默认为 [];**
比如 排除生成器，可以如下配置：

```
{
  "presets": [
    ["env", {
      "targets": {
        "browsers": ["last 2 versions", "safari >= 7"]
      },
      "exclude": ["transform-regenerator", "es6.set"]
    }]
  ]
}
```

**3.2 理解 babel-presets-stage-x**
官方预设(preset),  有两种，一个是按年份(babel-preset-es2017)，一个是按阶段(babel-preset-stage-0)。 这主要是根据TC39  委员会ECMASCRPIT 发布流程来制定的。因此到目前为止 有4个不同的阶段预设：

```
babel-preset-stage-0

babel-preset-stage-1

babel-preset-stage-2

babel-preset-stage-3
```

以上每种预设都依赖于紧随的后期阶段预设，数字越小，阶段越靠后，存在依赖关系。也就是说stage-0是包括stage-1的，以此类推。因此  stage-0包含stage-1/2/3的内容。所以如果我们不知道需要哪个stage-x的话，直接引入stage-0就好了。

**stage0** [(https://babeljs.io/docs/en/babel-preset-stage-0) ](https://babeljs.io/docs/en/babel-preset-stage-0)只是一个美好激进的想法，一些 Babel 插件实现了对这些特性的支持 ，但是不确定是否会被定为标准.

**stage1** [(https://babeljs.io/docs/en/babel-preset-stage-1)](https://babeljs.io/docs/en/babel-preset-stage-1) 值得被纳入标准的特性.

**stage2** [(https://babeljs.io/docs/en/babel-preset-stage-2)](https://babeljs.io/docs/en/babel-preset-stage-2) 该特性规范己经被起草，将会被纳入标准里.

**stage3** [(https://babeljs.io/docs/en/babel-preset-stage-3) ](https://babeljs.io/docs/en/babel-preset-stage-3)该特性规范已经定稿，大浏览器厂商和 Node.js 社区己开始着手实现.

但是在我们使用的时候只需要安装你想要的阶段就可以了：比如 babel-preset-stage-2， 安装命令如下：

```
npm install --save-dev babel-preset-stage-2
```

[回到顶部](https://www.cnblogs.com/tugenhua0707/p/9452471.html#_labelTop)

二：在webpack中配置babel

在上面了解了babel后，现在我们需要知道如何在webpack中使用它了。由于babel所做的事情是转换代码，所有需要使用loader去转换，因此我们需要配置babel-loader。

在安装babel-loader之前，我们需要安装babel-core， 因为babel-core是Babel编译器的核心，因此也就意味着如果我们需要使用babel-loader进行es6转码的话，我们首先需要安装 babel-core, 安装命令如下即可：

```
npm install --save-dev babel-core
```

然后我们再安装 babel-loader, 命令如下：

```
npm install --save-dev babel-loader
```

接着我们需要安装 babel-preset-env, babel-plugin-transform-runtime, babel-preset-stage-2, 如下命令安装

```
npm install --save-dev  babel-preset-env babel-plugin-transform-runtime babel-preset-stage-2
```

因此 .babelrc 配置如下即可：

```
{
  "plugins": [
     [
      "transform-runtime",
      {
        "polyfill": false
      }
     ]
   ],
   "presets": [
     [
       "env",
       {
         "modules": false
       }
     ],
     "stage-2"
  ]
}
```

### 1、主要插件介绍

 

　　　　**babel-preset-es2015 ( ES2015 / ES6 插件集合 )**

　　　　将 ES2015(es6) 的 javascript 代码 转化 浏览器兼容 javascript 代码

 

　　　　arrow-functions　　：箭头函数

　　　　block-scoped-functions　　：函数命名的方式转话，改为赋值的形式

　　　　block-scoping　　：let  转为 var

　　　　classes　　： es6 的类转化

　　　　computed-properties　　：属性命名的转化，如：var obj = { ['x'+a]: 1 }

　　　　destructuring　　：析构函数转化

　　　　duplicate-keys　　：属性优化

　　　　for-of　　：for of 的转化

　　　　function-name　　：优化函数命名

　　　　instanceof　　：instanceof 转化

　　　　literals　　：不同类型复制优化

　　　　new-target　　：继承 extends 转化

　　　　object-super　　：super 转化

　　　　parameters　　：params ，(a=1,b=2)=>{} , 默认参数转化

　　　　shorthand-properties　　：对象属性转化 {a,b} => {a:a,b:b}

　　　　spread　　：扩展属性转化 {...other}

　　　　sticky-regex　　：regex 优化，用 new Regex构建

　　　　template-literals　　：字符串模版 

　　　　typeof-symbol　　：symbol 转化

　　　　unicode-regex　　：将正则的匹配字符串转为 unicode

　　　　

　　　　

　　　　**babel-preset-es2016 （ ES2016 / ES7 插件集合 ）**

　　　　将 ES2016(es7) 的 javascript 代码 转为 浏览器兼容代码

 

　　　　exponentiation-operator　　：幂运算转化  x **= 3 ，x的3次幂

 

 

　　　　**babel-preset-es2017（ ES2017 / es8 插件集合 ）**

　　　　将 ES2017(es8) 的代码进行转化　　　　

 

　　　　async-to-generator　　： 转化 async/await 转为 generator 函数

 

 

　　　　**babel-preset-stage-x （ ES2018/stage 插件集合 ）**

　　　　将 stage 阶段的代码转化

 

　　　　async-generator-function　　：将异步函数进行转化

　　　　dotall-regex　　：点号正则优化

　　　　object-rest-spread　　：解析构优化

　　　　optional-catch-binding　　：catch 加 finally 优化

　　　　unicode-proprty-regex　　

 

　　　　

　　　　**Modules**

　　　　modules-amd / modules-commonjs / modules-systemjs / modules-umd

　　　　将不同的 export default x  转为不同的模块形式 amd / commonjs / systemjs / umd

 

 

　　　　**Experiental （ 试验阶段 ）**

 

　　　　class-properties　　：class 的静态属性转化

　　　　decorators　　　　：装饰器转化

　　　　do-expressions　　

　　　　export-default-from　　：export 优化

　　　　export-namespace-from　　：export 优化

　　　　function-bind　　：bind 函数转化，obj::fun => fun.bind(obj)  ::obj.fun => obj.fun(obj)

　　　　function-sent　　

　　　　logical-assignment-operators　　　

　　　　nullish-coalescing-operaor

　　　　numeric-separator

　　　　optional-chaining

　　　　pipeline-opertor

　　　　throw-expressions　　　　

 

 　　　  **Minification（ babel-minify ）**

　　　　运用代码压缩优化插件预设

 

　　　　...

　　　　这里自行可以去官网上查看...

 

　　　　

　　　　**babel-preset-react ( react 集合 )**

　　　　react 的相关编译转化...

　　　　jsx 和 element 的一下转化

　　　　直接添加 react 的预设就可以了。

 

　　

　　　　**Other 其他一些插件**

　　　　

　　　　jscript　　：严格模式命名

　　　　object-assgin　　：assgin 转化

　　　　regenerator　　：generator 函数的转化

　　　　object-set-prototype-of-to-assign　　：setPrototype 转化

　　　　runtime　

 

 

### 　　2、主要 presets 介绍

　　　　

　　　　**@babel/preset-dev**

 

　　　　是一个智能预设。

　　　　根据浏览器和运行环境，自动的确定 babel 插件和 polyfills 。

　　　　没有任何配置的情况下，和 babel-preset-latest 一样 ( es2015/es2016/es2017一起使用 )。

```
{
  "presets": [ "dev" ]  
}
```

　　

　　配置及其详解：

　　

```
{
　　"presets": [
　　　　[
　　　　　　'@babel/preset-env',
　　　　　　{
　　　　　　　　'target':{
　　　　　　　　　　"browser":["ie>=8","chrome>=62"],
　　　　　　　　　　"node":"8.9.0",
　　　　　　　　　　"safari":"tp"
　　　　　　　　},
　　　　　　　　"modules":false,
　　　　　　　　"debug":true,
　　　　　　　　"uglify":true
　　　　　　　　"useBuiltIns":true
　　　　　　}
　　　　]
　　]
}
```

　　

　　target : 描述您为项目配置的 支持/定位 的环境

　　　　browsers ：浏览器的支持情况，将返回支持浏览器列表。 会被 target.ie 覆盖

　　　　　　[ 'ie>=8' ]    支持 ie8 的环境

　　　　　　"default"     默认

　　　　node ：指定是 node 环境，且可以指定版本

　　　　safari : safari 版本

　　modules : 启用将 es6 转为其他模块

　　debug ： 是否启用 console.log　

　　include / exclude : 必须启用的 plugin 功能 / 不启用的 plugin 功能

　　uglify : 压缩代码

　　useBuiltIns ： 

　　　　false ： 引用所有的 babel-polyfill ， 在 webpack 中添加 babel-polyfill 入口处：

　　　　　　　   entry:[ 'babel-polyfill' , 'index.js' ] , 引用所有的 polyfill，体积变大

　　　　true ： 引用部分，根据配置的 preset-env 环境，引用 polyfill ，

　　　　　　　  在入口文件中要引用 babel-polyfill

 

 

　　**@babel/preset-react**

　　这都是一些 react 相关的集合

　　不需要介绍了，有需要自己去官网浏览下.

　　　　

　　 

　　**Stage-x**

　　草案中的预设..

　　该插件集合不存在 preset-env 集合中，所以需要引用....

 

 

### 　　3、babel-polyfill 兼容方法

 

　　babel的 preset-dev 中只会转化新的 Javascript 语法，不会转化新的 api。

　　例如：Generator / Set / Map 等 

　　就是实现 babel 转化后的代码中，仍有不兼容的代码的转化。

 

　　**原理：**

　　babel-folyfill 是修改全局的对象的原型，添加不兼容的 api 方法，或者修改不兼容的 api 方法。

　　

 

　　**用法：**

 

　　1、在入口文件的顶部添加：

```
require('@babel-polyfill');

import '@babel-polyfill'
```

　　

　　2、webpack 中添加

 

```
{
　　entry:['@babel-polyfill','./app.js']
}
```

　　在 webpack 的配置文件中添加。

 

　　3、直接在 html 中引用，在打包 js 的文件之前

 

```
<script src = './babel-polyfill.js' ></script>
```

　　

###  

### 　　**4、transform-runtime 插件**

###  

　　对 babel 转化后的代码，进行再次转化，与 babel-polyfill 一样，解决不兼容的全局 api 。

　　与 babel-polyfill 不同是它不是添加/修改全局对象。

　　它是对不兼容的方法进行特殊处理，也就是添加辅助方法来做兼容。

　　并且 transform-runtime 是在需要进行兼容转化时候引用。

　　transform-runtime 是依赖 babel-runtime ，且辅助方法都是引用的 babel-runtime

　　

 

　　特点：

　　不会污染全局变量，因为不会修改全局对象

　　没有修改实例的方法，所以有些方法不能实现。

 

```
 ["transform-runtime", {
　　　　"corejs":false,
      "helpers": false,
      "polyfill": false,
      "regenerator": true,
      "moduleName": "babel-runtime"
 }]
```

　　

　　helpers ： 是否使用 @babel-runtime/helpers 来代替内部的 helpers

　　coresjs ： 是否用 @babel-runtime/corejs 中的辅助方法来替换 Map / Set 等方法

　　polyfill : 是否用 @babel-runtime 的辅助函数来代替 polyfill

　　regenerator ： 是否用 辅助函数来代替 async / generator 函数

　　moduleName ： 引用时候名字

 

 

### 　　**5、@babel-register**

 

　　通过 @babel-register 来注册当前所有的代码需要转码

 

```
var fs = require('fs');
var babelConfig = JSON.parse(fs.readFileSync('./.babelrc'));
require('babel-register')(babelConfig);
```

　　

　　很显然，这里可以传入一个 .babelrc 配置文件来进行转码时候的配置。

　　只需要添加到入口文件中。

　　node 端就可以这这样实现 babel 的转码。

###  

### 　　6、tools

　　babel-parser  : babel 的解析器，用于解析 Javascript / jsx / Typescript / flow 等代码

　　babel-core ： babel 的核心文件，用于转码的

　　babel-generator ： 代码的转化，源码和转化代码

　　babel-code-frame : 代码转化位置等转码映射

　　babel-helpers ： 辅助器方法

　　babel-runtime ： 提供了一些列的辅助方法，与 polyfill 类似

　　...

 

##  

## @bable/babel-cli 命令行工具

 

　　用于命令行编译文件，可以进行配置以及输出等操作。

 

### 　　用法：

```
babel  input.js -options
// 编译 input.js  文件
```

　　

### 　　配置 ：

　　-o ： 输出路径

　　-w ：监听编译文件，实时编译

　　-s  ：输出source-map ，可以内敛

　　-d  ：当编译一个目录时候，-d 输出目录

　　--ignore ： 忽略的文件

　　--plugins ： 使用插件，用 , 隔开

　　--presets ： 使用预设，用 , 隔开

　　--config-file ： 使用 .babelrc 文件路径

　　

 

 

## .babelrc 配置详解

 

### 　　配置转化起 babel 三种不同作用：

　　

　　　　1、用于语法转义的配置。 preset-env / stage-x 主要用于 es 的语法转义，

　　　　2、用于补丁的配置，即 polyfill 的配置。主要是 transform-runtime 。

　　　　3、用于不同框架的配置，如：react的jsx /flow 等

 

### 　　例子：

```
{
　　"presets":[
　　　　"react",
　　　　["env",{...}],
　　　　"stage-0"
　　],
　　"plugins":[
　　　　"pluginName",
　　　　["transform-runtime",{...}]
　　],
　　"ignore":["path",'file'],
　　"miniied":boolean,
　　"comments":boolean,
　　"env":{
　　　　"test":{
　　　　　　"presets":[],
　　　　　　"plugins":[]
　　　　}
　　},
　　...
}
```

　　

　　

### 　　主要几个配置

　　　　presets ： 预设，插件的集合，倒序执行

　　　　plugins ： 插件，先执行插件，在执行预设，顺序执行

　　　　ignore ： 忽略的文件

　　　　minify ： 压缩代码

　　　　common : 是否需要注释

　　　　env ： 设置不同的环境，应用不同的配置，配置取值：BABEL_ENV，如若没有取 NODE_ENV 的值，默认为 development.

 　　

　　　　**其他可以自行在官网浏览...**

 

 

 

### 　　常见预设

　　env  ： 用于替换 es2015 /  es2016 / es2017 的预设。根据环境引入插件

　　react ：react的插件集合

　　react-optimize ： react 代码优化，如去除 propsType 减少生产上面代码

　　stage-x ：草案代码插件集合

　　flow ： flow 插件集合

　　minify ： 代码优化的集合

　　typescript ： typescript 插件集合



### 　　常用插件

　　transform-async-to-generator ： 异步函数 async/await 插件

　　transform-decorators-leagacy : 装饰器插件

　　syntax-dynamic-import ：import() 插件

　　transform-runtime ： 辅助器插件，用于ployfill

　　transform-object-rest-spread : 用于合并 var test = {a:1,b2};var t = {...test,n:1}

　　transform-funciton-bind : 用于编译 obj::fun => fun.bind(obj)

　　transform-es2015-modules-commonjs ：es6 module 在 node 使用
