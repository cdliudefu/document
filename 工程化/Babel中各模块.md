说明：babel默认是只会去转义JS语法的，不会去转换新的API，比如想Promise,Generator,Symbol这种全局API对象，Babel是不会去编译的。

### [babel-core或@babel/core](http://babeljs.io/docs/usage/api/)

babel-core/@babel/core是作为babel的核心存在，babel的核心api都在这个模块里面，主要是一些对代码进行转换的核心方法，比如：transform。
```
npm install D @babel/core
```
### [babel-cli或@babel/cli](http://babeljs.io/docs/usage/cli/)

babel-cli是一个内置通过命令行对js文件进行换码的工具。
```
npm install @babel/cli -D
```
使用方法：

- 直接在命令行输出转译后的代码 

  ```shell
   npx babel script.js
  ```

- 指定输出文件 

  ```shell
    babel script.js --out-file build.js
    或者是
    babel script.js -o build.js
  ```

让我们来编写了一个具有箭头函数的代码：

`//script.js`

`const array = [1,2,3].map((item, index)=> item * 2);`

然后在命令行执行 babel script.js，发现输出的代码好像没有转译。

![babel转译](http://ovdk1wiaq.bkt.clouddn.com/17-10-16/1390829.jpg)

因为我们没有告诉babel要转译哪些类型，现在看看怎么指定转译代码中的箭头函数。

```shell
babel --plugins transform-es2015-arrow-functions script.js
```

![转译箭头函数](http://ovdk1wiaq.bkt.clouddn.com/17-10-16/38822081.jpg)

或者在目录里添加一个.babelrc文件，内容如下：

```json
{
    "plugins": [
        "transform-es2015-arrow-functions"
    ]
}
```

.babelrc是babel的全局配置文件，所有的babel操作（包括babel-core、babel-node）基本都会来读取这个配置

##### babel-node

babel-node是随babel-cli一起安装的，只要安装了babel-cli就会自带babel-node。
在命令行输入babel-node会启动一个REPL（Read-Eval-Print-Loop），这是一个支持ES6的js执行环境。

![测试babel-node](http://ovdk1wiaq.bkt.clouddn.com/17-10-17/40708997.jpg)

其实不用babel-node，直接在node下，只要node版本大于6大部分ES6语法已经支持，况且现在node的版本已经到了8.7.0。

![node环境箭头函数测试](http://ovdk1wiaq.bkt.clouddn.com/17-10-17/67395028.jpg)

babel-node还能直接用来执行js脚本，与直接使用node命令类似，只是会在执行过程中进行babel的转译，并且babel官方不建议在生产环境直接这样使用，因为babel实时编译产生的代码会缓存在内存中，导致内存占用过高，所以我们了解了解就好。

```shell
babel-node script.js
```

##### 4、[babel-register](http://babeljs.io/docs/usage/babel-register/) 

babel-register字面意思能看出来，这是babel的一个注册器，它在底层改写了node的require方法，引入babel-register之后所有require并以.es6, .es, .jsx 和 .js为后缀的模块都会经过babel的转译。

同样通过箭头函数做个实验：

`//test.js`

`const name = 'shenfq';`

`module.exports = ()=> {`

` const json = {name};`

` return json;`

`};`

`//main.js`

`require('babel-register');`

`var test = require('./test.js'); //test.js中的es6语法将被转译成es5

`console.log(test.toString()); //通过toString方法，看看控制台输出的函数是否被转译`

![register转译](http://ovdk1wiaq.bkt.clouddn.com/17-10-17/66788446.jpg)

默认babel-register会忽略对node_modules目录下模块的转译，如果要开启可以进行如下配置。

`require("babel-register")({`

` ignore: false`

`});`

babel-register与babel-core会同时安装，在babel-core中会有一个register.js文件，所以引入babel-register有两种方法：

`require('babel-core/register');`

`require('babel-register');`

但是官方不推荐第一种方法，因为babel-register已经独立成了一个模块，在babel-core的register.js文件中有如下注释。

> TODO: eventually deprecate this console.trace(“use the `babel-register` package instead of `babel-core/register`“);

##### 5、[babel-polyfill](http://babeljs.io/docs/usage/babel-register/) 

polyfill这个单词翻译成中文是`垫片`的 意思，详细点解释就是桌子的桌脚有一边矮一点，拿一个东西把桌子垫平。polyfill在代码中的作用主要是用已经存在的语法和api实现一些浏览器还没 有实现的api，对浏览器的一些缺陷做一些修补。例如Array新增了includes方法，我想使用，但是低版本的浏览器上没有，我就得做兼容处理：

`if (!Array.prototype.includes) {`

` Object.defineProperty(Array.prototype, 'includes', {`

` value: function(searchElement, fromIndex){`

` if (this == null) {`

` throw new TypeError('"this" is null or not defined');`

` }`

` var o = Object(this);`

` var len = o.length >>> 0;`

` if (len === 0) {`

` return false;`

` }`

` var n = fromIndex | 0;`

` var k = Math.max(n >= 0 ? n : len - Math.abs(n), 0);`

` while (k < len) {`

` if (o[k] === searchElement) {`

` return true;`

` }`

` k++;`

` }`

` return false;`

` }`

` });`

`}`

上面简单的提供了一个includes方法的polyfill，代码来自[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/includes)。

理 解polyfill的意思之后，再来说说babel为什么存在polyfill。因为babel的转译只是语法层次的转译，例如箭头函数、解构赋值、 class，对一些新增api以及全局函数（例如：Promise）无法进行转译，这个时候就需要在代码中引入babel-polyfill，让代码完美 支持ES6+环境。前面介绍的babel-node就会自动在代码中引入babel-polyfill包。

引入方法：

`//在代码的最顶部进行require或者import`

`require("babel-polyfill");`

`import "babel-polyfill";`

`//如果使用webpack，也可以在文件入口数组引入`

`module.exports = {`

` entry: ["babel-polyfill", "./app/js"]`

`};`

但很多时候我们并不会使用所有ES6+语法，全局添加所有垫片肯定会让我们的代码量上升，之后会介绍其他添加垫片的方式。

### @babel/plugin*
babel是基于插件化的，就是如果对某功能需要转移的话，就安装相应的插件
箭头函数转换插件：@babel/plugin-transform-arrow-functions
```
/* study.js */
const study = () => {}

/* study-compiled.js */
const study = function () {};
```
es6的数值指数运算插件：@babel/plugin-transform-exponentiation-operator
```
/* study.js */
const exponentiation = 2 ** 2

/* study-compiled.js */
const exponentiation = Math.pow(2, 2);
```
### @babel/preset* 预设
从上面的插件化我们知道需要哪个插件就引入，那么问题来了，es6新增的语法那么都，总不能一个插件一个插件去添加吧，因此就用到了prests预设，就是预先设定好一些东西，省得一个个去引入插件，比如preset-env(es6+规范语法的插件集合),preset-state(一些处理尚在提案阶段的语法的插件集合，babel7+被废弃了)，preset-react(处理react语法的插件集合)等
- **@babel/preset-env** 是一个智能预设，配置了它就可以用es6+去书写代码，而且是按需加载所需要的插件
```
/* babel.config.js */

module.exports = {
  presets: [
    "@babel/preset-env"
  ],
  plugins: [
  ]
}
```
对应的es6语法编译
```
/* study.js */
const study = () => {}

const arr1 = [1, 2, 33]
const arr2 = [...arr1]

const exponentiation = 2 ** 2

// 新增API
new Promise(() => {})
new Map()

/* study-compiled.js */
var study = function study() {};

var arr1 = [1, 2, 33];
var arr2 = [].concat(arr1);
var exponentiation = Math.pow(2, 2);

// 新增API
new Promise(function () {});
new Map();
```
我们发现es6+的语法被编译了，但是新增的API方法并没有编译
** browserslist**集成
关于preset-env，可以提供一个targets配置项指定运行环境，当然也可以在package.json文件里配置或新建.browserslistrc文件来指定对应的目标环境。[browserslist配置源](https://github.com/browserslist/browserslist#queries)

```
/* babel.config.js */
module.exports = {
  presets: [
    [
      "@babel/preset-env", {
        'targets': {
          'browsers': ['ie >= 8', 'iOS 7'] // 支持ie8，直接使用iOS浏览器版本
        }
      }
    ]
  ],
  plugins: [
  ]
}
```
- **@babel/polyfill(由core-js2和regenerator-runtime组成的一个集成包)
像promise,map,set,Generator,Symbol这种API的babel并不给转义，因为babel默认是只会转义JS语法的，不会去转换新的API，@babel/polyfill就是这些新增API的垫片，可以转换es6+新增的API
```
/* study.js */
import '@babel/polyfill'
// 新增API
new Promise(function () {});

/* study-compiled.js */
require("@babel/polyfill");
// 新增API
new Promise(function () {});
```
> 说明：import被转译成require，如果想要编译出来的模块引入规则还是import,则可以在prest-env的配置项中添加"modules":false即可，modules的options:"amd" | "umd" | "systemjs" | "commonjs" | "cjs" | "auto" | false，默认为"auto"
>
但问题来了，有时候我们项目里并没有用到那么多新增的API，但是@babel/polyfill会把所有浏览器环境的polyfill都引入，整个包的体积就会很大，想要对目标环境按需引入相应的polyfill应该怎么办呢，这时就可以使用preset-env的配置项中的**useBuiltIns**属性来按需引入polyfill
```
/* babel.config.js */

module.exports = {
  presets: [
    [
      "@babel/preset-env", {
        "modules": false,
        "useBuiltIns": "entry",
        'targets': {
          'browsers': ['ie >= 8', 'iOS 7'] // 支持ie8，直接使用iOS浏览器版本
        }
      }
    ]
  ],
  plugins: [
  ]
}
```
这个时候就会在入口处只把所有ie8以上以及iOS 7浏览器不支持api的polyfill引入进来。最终效果：
```
/* study.js */
import '@babel/polyfill'
// 新增API
new Promise(function () {});

/* study-compiled.js */
import "core-js/modules/es6.array.copy-within";
import "core-js/modules/es6.array.every";
...//省略若干
import "core-js/modules/web.immediate";
import "core-js/modules/web.dom.iterable";
import "regenerator-runtime/runtime";
// 新增API
new Promise(function () {});
```
此时你会发现，import '@babel/polyfill'没有了，引入的是我们目标环境相应的polyfill。但是有没有发现引入的都是import 'core-js/...'的内容，标题已经说啦，**@babel/polyfil**是由core-js2和regenerator-runtime组成的一个集成包。假如我的项目里面只用到了Promise这个API，能不能只给我引入Promise相应的API呢？答案是必可以！，让我们先来好好了解下preset-env的配置项中的useBuiltIns属性:
**useBuiltIns**
选项："usage"|"entry"|false 默认为false
entry:我们已经用过了，意义就是在入口处将根据我们配置的浏览器兼容，将目标浏览器环境所有不支持的API都引入。
usage:就很nb了，当配置成usage的时候，babel会扫描你的每个文件，然后检查你都用到了哪些新的API，跟进我们配置的浏览器兼容，只引入相应API的polyfill，我们把useBuiltIns属性设置为usage再来看下编译效果：
```
/* study.js */
import '@babel/polyfill'
// 新增API
new Promise(function () {});

/* study-compiled.js */
import "core-js/modules/es.object.to-string";
import "core-js/modules/es.promise";
// 新增API
new Promise(function () {});
```
当我们使用useBuiltIns选项的时候，你的命令行里面是不是显示了一坨这样的警告，大概是在配置文件中未指定core-js版本时，默认会使用core-js2：
> WARNING: We noticed you're using the useBuiltIns option without declaring a core-js version. Currently, we assume version 2.x when no version is passed. Since this default version will likely change in future versions of Babel, we recommend explicitly setting the core-js version you are using via the corejs option.
>
前面也说到了 @babel/polyfil 是由core-js2和regenerator-runtime组成的一个集成包，现在core-js3已经发布了，而且很稳定。但是core-js2在18年的时候已经不再维护了；**@babel/polyfil**引入的是2不是3，并且 @babel/polyfill 在babel7.4.0已经不再推荐使用了，要废掉(好像是因为@babel/polyfill不支持core-js2平滑的过渡到core-js3)。所以core-js官方现在推荐我们使用polyfill的时候直接引入core-js和regenerator-runtime/runtime这两个包完全取代 @babel/polyfil 来为了防止重大更改。
当然，我们需要在preset-env配置项中指定core-js版本，这样就不会再有警告⚠️了：
```
/* babel.config.js */
module.exports = {
  presets: [
    [
      "@babel/preset-env", {
        "modules": false,
        "useBuiltIns": "entry",
        "corejs": "3",
        'targets': {
          'browsers': ['not ie >= 8', 'iOS 7'] // 支持ie8，直接使用iOS浏览器版本7
        }
      }
    ]
  ],
  plugins: [
  ]
}
```
- **@babel/runtime**(依赖@babel/helpers和regenerator-runtime)
有的时候一些语法的转换会比较复杂，babel会引入一些helper函数，比如说对es6的class进行转换：
```
/* study.js */
class Test {}

/* study-compiled.js */
function _classCallCheck(instance, Constructor) { if (!(instance instanceof Constructor)) { throw new TypeError("Cannot call a class as a function"); } }

var Test = function Test() {
  _classCallCheck(this, Test);
};
```
可以看到上面引入了helper函数来处理class的转换。但是问题又来了，如果好多文件都使用到了复杂语法的转换，这个还是简单点的，有些helper函数是很复杂代码量很多的，那岂不是每个文件都会定义一遍这些个函数，每个文件的代码会很多？如果说可以把这些helper函数都抽离到一个公共的包里，用到的地方只需要引入对应的函数即可，我们的编译出来的代码量会大大滴减少，这个时候就需要用到 @babel/plugin-transform-runtime 插件来配合@babel/runtime进行使用。记得先安装一波，然后在插件选项中加入 @babel/plugin-transform-runtime 这个插件，然后我们来看看编译后的效果：
```
/* study.js */
class Test {}

/* study-compiled.js */
import _classCallCheck from "@babel/runtime/helpers/classCallCheck";

var Test = function Test() {
  _classCallCheck(this, Test);
};
```
@babel/plugin-transform-runtime还有一个最重要的作用：比如说像上面我们说的Promise就需要提供相应的polyfill去解决，这样做会有一个副作用，就是会污染全局变量。如果我们只是在一个业务项目这样搞还好，也没别人要用到。但是如果我们是在维护一个公共的东西，比如公共组件库，我们这样搞，你的一些polyfill可能会把一些全局的api给改掉，副作用就会很明显，别人用你的组件库的时候就可能会出问题。@babel/plugin-transform-runtime插件为我们提供了一个配置项corejs，他可以给这些polyfill提供一个沙箱环境，这样就不会污染到全局变量，无副作用你说美不美。

记得安装 @babel/runtime-corejs2 这个包(稳定版用2就可以)，注意如果不配置的话，是不会提供沙箱环境的。然后在 @babel/plugin-transform-runtime 插件配置corejs：
```
/* babel.config.js */
module.exports = {
  presets: [
    [
      "@babel/preset-env",
      {
        "modules": false,
        "useBuiltIns": "usage",
        "corejs": "3",
        'targets': {
          'browsers': ["ie >= 8", "iOS 7"] // 支持ie8，直接使用iOS浏览器版本
        }
      }
    ]
  ],
  plugins: [
    [
      "@babel/plugin-transform-runtime",
      {
        "corejs": 2
      }
    ]
  ]
}
```

在你修改了babel配置项之后一定要记得重启编译命令，否则不会生效
维护公共组件库或者一些别的公共库推荐要使用@babel/runtime配合@babel/plugin-transform-runtime来建立沙箱环境
------

#### .babelrc

前面已经介绍了babel常用的一些模块，接下来看看babel的配置文件 `.babelrc`。

后面的后缀rc来自linux中，使用过linux就知道linux中很多rc结尾的文件，比如`.bashrc`，rc是`run command`的缩写，翻译成中文就是运行时的命令，表示程序执行时就会来调用这个文件。

babel所有的操作基本都会来读取这个配置文件，除了一些在回调函数中设置options参数的，如果没有这个配置文件，会从`package.json`文件的babel属性中读取配置。

##### plugins

先简单介绍下 plugins ，babel中的插件，通过配置不同的插件才能告诉babel，我们的代码中有哪些是需要转译的。

这里有一个babel官网的[插件列表](http://babeljs.io/docs/plugins/)，里面有目前babel支持的全部插件。

举个例子：

1.  

   `{`

2.  

   ` "plugins": [`

3.  

   ` "transform-es2015-arrow-functions", //转译箭头函数`

4.  

   ` "transform-es2015-classes", //转译class语法`

5.  

   ` "transform-es2015-spread", //转译数组解构`

6.  

   ` "transform-es2015-for-of" //转译for-of`

7.  

   ` ]`

8.  

   `}`

9.  

   `//如果要为某个插件添加配置项，按如下写法：`

10.  

    `{`

11.  

    ` "plugins":[`

12.  

    ` //改为数组，第二个元素为配置项`

13.  

    ` ["transform-es2015-arrow-functions", { "spec": true }]`

14.  

    ` ]`

15.  

    `}`

16.  

     

上面这些都只是语法层次的转译，前面说过有些api层次的东西需要引入polyfill，同样babel也有一系列插件来支持这些。

1.  

   `{`

2.  

   ` "plugins":[`

3.  

   ` //如果我们在代码中使用Object.assign方法，就用如下插件`

4.  

   ` "transform-object-assign"`

5.  

   ` ]`

6.  

   `}`

7.  

    

8.  

   `//写了一个使用Object.assign的代码如下：`

9.  

   `const people = Object.assign({}, {`

10.  

    ` name: 'shenfq'`

11.  

    `});`

12.  

    `//经过babel转译后如下：`

13.  

    `var _extends = Object.assign || function(target){ for (var i = 1; i < arguments.length; i++) { var source = arguments[i]; for (var key in source) { if (Object.prototype.hasOwnProperty.call(source, key)) { target[key] = source[key]; } } } return target; };`

14.  

     

15.  

    `const people = _extends({}, {`

16.  

    ` name: 'shenfq'`

17.  

    `});`

18.  

     

这 种通过transform添加的polyfill只会引入到当前模块中，试想实际开发中存在多个模块使用同一个api，每个模块都引入相同的 polyfill，大量重复的代码出现在项目中，这肯定是一种灾难。另外一个个的引入需要polyfill的transform挺麻烦的，而且不能保证手 动引入的transform一定正确，等会会提供一个解决方案：`transform-runtime`。

除了添加polyfill，babel还有一个工具包helpers，如果你有安装babel-cli，你可以直接通过下面的命令把这个工具包输出：

```shell
./node_modules/.bin/babel-external-helpers > helpers.js
```

这个工具包类似于babel的utils模块，就像我们项目中的utils一样，很多地方都会用到，例如babel实现Object.assign就是使用的helpers中的_extend方法。为了避免同一个文件多次引用babel的助手函数，通过`external-helpers`插件，能够把这些助手函数抽出放到文件顶部，避免多次引用。

1.  

   `//安装： cnpm install --save-dev babel-plugin-external-helpers`

2.  

    

3.  

   `//配置`

4.  

   `{`

5.  

   ` "plugins": ["external-helpers"]`

6.  

   `}`

7.  

    

虽 然这个插件能避免一个文件多次引用助手函数，但是并不能直接避免多个文件内重复引用，这与前面说到的通过transform添加polyfill是一样的 问题，这些引用都只是module级别的，在打包工具盛行的今天，需要考虑如何减少多个模块重复引用相同代码造成代码冗余。

当然也可以在每个需要使用helpers的js文件顶部直接引入之前生成的helpers文件既可，通过打包工具将这个公共模块进行抽离。

1.  

   `require('helpers');`

2.  

    

在说完babel的helpers之后就到了插件系统的最后的一个插件：`transform-runtime`。前面在transform-polyfill的时候也有提到这个插件，之所以把它放到helpers后面是因为这个插件能自动为项目引入polyfill和helpers。

```shell
cnpm install -D babel-plugin-transform-runtime babel-runtime
```

transform-runtime这个插件依赖于babel-runtime，所以安装transform-runtime的同时最好也安装babel-runtime，为了防止一些不必要的错误。babel-runtime由三个部分组成：

1. core-js

   > core-js极其强悍，通过ES3实现了大部分的ES5、6、7的垫片，作者zloirock是来自战斗名族的程序员，一个人维护着core-js，听说他最近还在找工作，上面是core-js的github地址，感兴趣可以去看看。

2. regenerator

   > regenerator来自facebook的一个库，用于实现 generator functions。

3. helpers 

   > babel的一些工具函数，没错，这个helpers和前面使用babel-external-helpers生成的helpers是同一个东西

从babel-runtime的package.json文件中也能看出，runtime依赖了哪些东西。

![babel-runtime的package.json](http://ovdk1wiaq.bkt.clouddn.com/17-10-22/66492657.jpg)

安装有babel-runtime之后要引入helpers可以使用如下方式：

1.  

   `require('babel-runtime/helpers');`

2.  

    

使用runtime的时候还有一些配置项：

1.  

   `{`

2.  

   ` "plugins": [`

3.  

   ` ["transform-runtime", {`

4.  

   ` "helpers": false, //自动引入helpers`

5.  

   ` "polyfill": false, //自动引入polyfill（core-js提供的polyfill）`

6.  

   ` "regenerator": true, //自动引入regenerator`

7.  

   ` }]`

8.  

   ` ]`

9.  

   `}`

10.  

     

**比较transform-runtime与babel-polyfill引入垫片的差异：**

1. 使用 runtime是按需引入，需要用到哪些polyfill，runtime就自动帮你引入哪些，不需要再手动一个个的去配置plugins，只是引入的 polyfill不是全局性的，有些局限性。而且runtime引入的polyfill不会改写一些实例方法，比如Object和Array原型链上的方 法，像前面提到的`Array.protype.includes`。
2. babel- polyfill就能解决runtime的那些问题，它的垫片是全局的，而且全能，基本上ES6中要用到的polyfill在babel- polyfill中都有，它提供了一个完整的ES6+的环境。babel官方建议只要不在意babel-polyfill的体积，最好进行全局引入，因为 这是最稳妥的方式。
3. 一般的建 议是开发一些框架或者库的时候使用不会污染全局作用域的babel-runtime，而开发web应用的时候可以全局引入babel-polyfill避 免一些不必要的错误，而且大型web应用中全局引入babel-polyfill可能还会减少你打包后的文件体积（相比起各个模块引入重复的 polyfill来说）。

------

##### presets

显然这样一个一个配置插件会非常的麻烦，为了方便，babel为我们提供了一个配置项叫做persets（预设）。

预设就是一系列插件的集合，就好像修图一样，把上次修图的一些参数保存为一个预设，下次就能直接使用。

如果要转译ES6语法，只要按如下方式配置即可：

1.  

   `//先安装ES6相关preset： cnpm install -D babel-preset-es2015`

2.  

   `{`

3.  

   ` "presets": ["es2015"]`

4.  

   `}`

5.  

    

6.  

   `//如果要转译的语法不止ES6，还有各个提案阶段的语法也想体验，可以按如下方式。`

7.  

   `//安装需要的preset： cnpm install -D babel-preset-stage-0 babel-preset-stage-1 babel-preset-stage-2 babel-preset-stage-3`

8.  

   `{`

9.  

   ` "presets": [`

10.  

    ` "es2015",`

11.  

    ` "stage-0",`

12.  

    ` "stage-1",`

13.  

    ` "stage-2",`

14.  

    ` "stage-3",`

15.  

    ` ]`

16.  

    `}`

17.  

     

18.  

    `//同样babel也能直接转译jsx语法，通过引入react的预设`

19.  

    `//cnpm install -D babel-preset-react`

20.  

    `{`

21.  

    ` "presets": [`

22.  

    ` "es2015",`

23.  

    ` "react"`

24.  

    ` ]`

25.  

    `}`

26.  

     

不过上面这些preset官方现在都已经不推荐了，官方**唯一推荐**preset：`babel-preset-env`。

这款preset能灵活决定加载哪些插件和polyfill，不过还是得开发者手动进行一些配置。

1.  

   `// cnpm install -D babel-preset -env`

2.  

   `{`

3.  

   ` "presets": [`

4.  

   ` ["env", {`

5.  

   ` "targets": { //指定要转译到哪个环境`

6.  

   ` //浏览器环境`

7.  

   ` "browsers": ["last 2 versions", "safari >= 7"],`

8.  

   ` //node环境`

9.  

   ` "node": "6.10", //"current" 使用当前版本的node`

10.  

     

11.  

    ` },`

12.  

    ` //是否将ES6的模块化语法转译成其他类型`

13.  

    ` //参数："amd" | "umd" | "systemjs" | "commonjs" | false，默认为'commonjs'`

14.  

    ` "modules": 'commonjs',`

15.  

    ` //是否进行debug操作，会在控制台打印出所有插件中的log，已经插件的版本`

16.  

    ` "debug": false,`

17.  

    ` //强制开启某些模块，默认为[]`

18.  

    ` "include": ["transform-es2015-arrow-functions"],`

19.  

    ` //禁用某些模块，默认为[]`

20.  

    ` "exclude": ["transform-es2015-for-of"],`

21.  

    ` //是否自动引入polyfill，开启此选项必须保证已经安装了babel-polyfill`

22.  

    ` //参数：Boolean，默认为false.`

23.  

    ` "useBuiltIns": false`

24.  

    ` }]`

25.  

    ` ]`

26.  

    `}`

27.  

     

关于最后一个参数`useBuiltIns`，有两点必须要注意：

1. 如果useBuiltIns为true，项目中必须引入babel-polyfill。
2. babel-polyfill只能被引入一次，如果多次引入会造成全局作用域的冲突。

做了个实验，同样的代码，只是`.babelrc`配置中一个开启了`useBuiltIns`，一个没有，两个js文件体积相差70K，[戳我看看](https://github.com/Shenfq/studyBabel/tree/master/7-babel-env)。

  文件 大小 

|                   |       |
| ----------------- | ----- |
| useBuiltIns.js    | 189kb |
| notUseBuiltIns.js | 259kb |

**最后啰嗦一句**

关于polyfill还有个叫做[polyfill.io](https://polyfill.io/v2/docs/)的神器，只要在浏览器引入

> <https://cdn.polyfill.io/v2/polyfill.js>

服务器会更具浏览器的UserAgent返回对应的polyfill文件，很神奇，可以说这是目前最优雅的解决polyfill过大的方案。

# core-js

这个名词肯定很多人没听过，今天也是在配置babelpolyfill方法发现的

## 起因

[![uRp8Og.png](https://s2.ax1x.com/2019/10/07/uRp8Og.png)](https://imgchr.com/i/uRp8Og)

在使用useBuiltIns:usage按需加载polyfill时，npm run build，就出现上述的提示

```js
{
  "presets": [
    ["@babel/preset-env", {
      "useBuiltIns": "usage",
      "targets": {
        "chrome": "58",
        "ie": "11"
      }
    }]
  ]
}
```

大意是说我们使用了useBuiltIns选项但是没有指定core-js警告，但是告诉你默认是使用core-js@2，但是这个默认行为以后可能会发生改变，最好显示设置core.js，然后列出了@2和@3两个版本

## core-js介绍

其实core-js是我们能够使用新的API的最重要的包，然而一般情况它隐藏在webpack编译后的代码中，我们一般不会去查看，所以容易被遗 忘，我们在webpack生成环境下，查看编译后的代码，可以看到例如includes就是从core-js导出到我们的代码去的

![uR9QE9.png](https://s2.ax1x.com/2019/10/07/uR9QE9.png)

### core-js是什么

- **它是JavaScript标准库的polyfill**
- 它尽可能的进行模块化，让你能选择你需要的功能
- **它可以不污染全局空间**
- 它和babel高度集成，可以对core-js的引入进行最大程度的优化

## 升级core-js@3动机

目前我们使用的都默认是core-js@2，它在一年半之前就封锁了分支，至此之后的特性都只会添加到core-js@3，这里有一个生产例子，使用了core-js@2不支持的新特性，导致错误

**core-js@2出现的问题**

[Vue-cli使用flat报错](https://juejin.im/post/5d7efbbb6fb9a06b2650c74a)

**vue-cli也会在V4升级core-js**

[Roadmap for Vue-cli4](https://github.com/vuejs/vue-cli/issues/3649)

**core-js@3添加的特性**

[具体core-js@3才有的特性看这里](https://github.com/zloirock/core-js/blob/master/docs/2019-03-19-core-js-3-babel-and-a-look-into-the-future.md#babelpreset-env)

## core-js@3 特性概览

- 支持ECMAScript稳定功能，引入core-js@3冻结期间的新功能，比如flat
- 加入到ES2016-ES2019中的提案，现在已经被标记为稳定功能
- **更新了提案的实现，增加了proposals配置项，由于提案阶段不稳定，需要谨慎使用**
- **增加了对一些web标准的支持，比如URL 和 URLSearchParams**
- **现在支持原型方法，同时不污染原型**
- 删除了过时的特性

## core-js@3与babel

以前我们实现API的时候，会引入整个polyfill,其实polyfill只是包括了以下两个包

```js
core-js
regenerator-runtime
```

**core-js@3升级之后弃用了@babel/polyfill**，以下是等价实现

```js
// babel.config.js
presets: [
  ["@babel/preset-env", {
    useBuiltIns: "entry", // or "usage"
    corejs: 3,
  }]
]

import "core-js/stable";
import "regenerator-runtime/runtime";
```

## 总结

core-js@3废弃了babel-polyfill，实现了完全无污染的API转译，非常有潜力，但是其暂时会增加打包体积，这个还得看未来普及度上来之后的权衡
