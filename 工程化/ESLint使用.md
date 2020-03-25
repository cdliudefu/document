[ESLint 官方文档 About 页面 ](https://eslint.cn/docs/about/)分 About 和 Philosophy 两个部分对 ESLint 做了介绍，简洁明了，值得一读。

借助 ESLint，可将 **静态代码分析** 和 **问题代码协助修复** 集成到 **编码**、**提交** 和 **打包** 过程中，及早发现并协助修复代码中：

- 有语法错误的部分
- 不符合约定的样式准则的部分
- 不符合约定的最佳实践的部分

在项目开发中获得如下收益：

- 在执行代码之前发现并修复语法错误，减少调试耗时和潜在 bug
- 保证项目的编码风格统一，提高可维护性
- 督促团队成员在编码时遵守约定的最佳实践，提高代码质量

## 安装

```
# Local Installation
npm install -D eslint

# Global Installation
npm install -g eslint
```

*注意*

- ESLint 借助 nodejs 的模块化机制引用插件。所以，全局安装的 ESLint 只能使用全局安装的 ESLint 插件，局部安装的 ESLint 只能使用局部安装的 ESLint 插件。

## 命令行接口

> 官方文档：[eslint.org/docs/user-g…](https://eslint.cn/docs/user-guide/command-line-interface)

 

### 命令模式

```
# eslint [选项] [操作目标]
eslint [options] [file|dir|glob]*
```

*注意*

- 当使用 glob 语法指定操作目标时，glob 模式会被 shell 解析，解析结果可能因 shell 不同而不同。如果想要使用 node 的 glob 语法，必须使用引号将 glob 模式串引起来（在 Windows 中必须使用双引号）。示例：

```
eslint "lib/**"
```

### 命令选项

使用 ESLint 命令时，可以通过 eslint -h 概览命令选项，找到自己需要的选项，然后去官方文档 [命令行接口部分](https://eslint.cn/docs/user-guide/command-line-interface) 查看该选项的详细描述。

```
eslint [options] file.js [file.js] [dir]

Basic configuration:
  --no-eslintrc                  Disable use of configuration from .eslintrc.*
  -c, --config path::String      Use this configuration, overriding .eslintrc.* config options if present
  --env [String]                 Specify environments
  --ext [String]                 Specify JavaScript file extensions - default: .js
  --global [String]              Define global variables
  --parser String                Specify the parser to be used
  --parser-options Object        Specify parser options
  --resolve-plugins-relative-to path::String  A folder where plugins should be resolved from, CWD by default

Specifying rules and plugins:
  --rulesdir [path::String]      Use additional rules from this directory
  --plugin [String]              Specify plugins
  --rule Object                  Specify rules

Fixing problems:
  --fix                          Automatically fix problems
  --fix-dry-run                  Automatically fix problems without saving the changes to the file system
  --fix-type Array               Specify the types of fixes to apply (problem, suggestion, layout)

Ignoring files:
  --ignore-path path::String     Specify path of ignore file
  --no-ignore                    Disable use of ignore files and patterns
  --ignore-pattern [String]      Pattern of files to ignore (in addition to those in .eslintignore)

Using stdin:
  --stdin                        Lint code provided on <STDIN> - default: false
  --stdin-filename String        Specify filename to process STDIN as

Handling warnings:
  --quiet                        Report errors only - default: false
  --max-warnings Int             Number of warnings to trigger nonzero exit code - default: -1

Output:
  -o, --output-file path::String  Specify file to write report to
  -f, --format String            Use a specific output format - default: stylish
  --color, --no-color            Force enabling/disabling of color

Inline configuration comments:
  --no-inline-config             Prevent comments from changing config or rules
  --report-unused-disable-directives  Adds reported errors for unused eslint-disable directives

Caching:
  --cache                        Only check changed files - default: false
  --cache-file path::String      Path to the cache file. Deprecated: use --cache-location - default: .eslintcache
  --cache-location path::String  Path to the cache file or directory

Miscellaneous:
  --init                         Run config initialization wizard - default: false
  --debug                        Output debugging information
  -h, --help                     Show help
  -v, --version                  Output the version number
  --print-config path::String    Print the configuration for the given file
```

 

有些选项可接收一组参数，这类选项支持两种传参方式（有一个选项例外：--ignore-pattern 不支持第二种方式）：

1. 多次指定同一选项，每次接收一个不同的参数
2. 将参数列表用逗号分隔，一次传给选项

示例：

```
# 1.
eslint --ext .jsx --ext .js lib/

# 2.
eslint --ext .jsx,.js lib/
```

 

## 配置

### 相对路径解析

配置文件中包含的相对路径和 glob 模式都是基于当前配置文件的路径进行解析的。

### 两种主要的配置方式

1. 配置注释 - 在目标文件中使用注释语法嵌入配置信息。这种配置只对当前文件有效。
2. 配置文件 - 在 JavaScript、JSON 或 YAML 文件中定义配置信息。对于这种配置方式，配置信息可以写在单独的  .eslintrc.*  文件中，或者写在  package.json  文件的 eslintConfig  字段中。这种配置对配置文件所在目录及其子目录树中的所有文件有效。

### 配置文件格式

- JavaScript - use  .eslintrc.js  文件导出一个包含配置信息的对象。
- YAML - 使用  .eslintrc.yaml  或  .eslintrc.yml  定义配置。
- JSON - 使用  .eslintrc.json  定义配置信息，JSON 文件中支持 JavaScript 注释。
- package.json - 在  package.json  文件中增加一个 eslintConfig 字段，在该字段中定义配置信息。
- .eslintrc - 已废弃

如果在同一个目录中有多个配置文件，则它们中间只有一个是有效的，优先级如下：

1. .eslintrc.js
2. .eslintrc.yaml 
3. .eslintrc.yml
4. .eslintrc.json
5. .eslintrc 
6. package.json

### 配置层叠

> 官方文档：[eslint.org/docs/user-g…](https://eslint.cn/docs/user-guide/configuring)

 

#### 项目级配置文件

在同一个项目中，可以为不同目录指定不同的配置文件。

当 lint 一个文件时，ESLint ：

1. 从文件所在目录开始，在目录树中依次向上搜索配置文件，直到系统根目录，或者直到找到一个包含  root: true  配置参数的配置文件，最终得到一个配置文件列表。
2. 合并上一步得到的配置文件列表中的配置项，得到最终的配置文件配置。合并配置项时，在目录树中距离 lint 目标越近的配置文件具有越高的优先级。
3. 执行其余步骤。

我觉得总结下来就是：找到 root: true 就停止寻找配置文件，多配置合并，配置文件优先级就近。

#### 完整的配置层叠关系

ESLint 配置除了通过配置文件指定，还可以通过注释内嵌或命令行选项指定。Lint 一个文件所使用的最终配置是各种配置合并的结果，合并时各种配置的优先级如下：

1. 注释内嵌配置
   1. /*eslint-disable*/ and /*eslint-enable*/
   2. /*global*/
   3. /*eslint*/
   4. /*eslint-env*/
2. 通过命令行选项指定的配置
   1. --global
   2. --rule
   3. --env
   4. -c, --config
3. 项目级配置文件
4. 如果没有找到项目级配置文件，并且没有或没有找到通过 --config 指定配置文件，则去用户家目录（~/）下搜索配置文件。

如果上述配置均不存在，则 ESLint 抛出 noConfigError 。

 

### 配置参数（[各个参数详解](http://eslint.cn/docs/user-guide/configuring#specifying-parser-options)）

- parser

  指定 ESLint 使用的语法分析器。ESLint 兼容的语法分析器有：[Esprima](https://www.npmjs.com/package/esprima)、[Babel-ESLint](https://www.npmjs.com/package/babel-eslint)、[@typescript-eslint/parser](https://www.npmjs.com/package/@typescript-eslint/parser)，ESLint 默认使用 [Esprima](https://www.npmjs.com/package/esprima)。

- parserOptions

  指定语法分析器选项，默认使用的语法分析器支持如下几个选项： ecmaVersion 、 sourceType 、 ecmaFeatures 。示例：

  ```
  {
    "parserOptions": {
      "ecmaVersion": 6,
      "sourceType": "module",
      "ecmaFeatures": {
        "jsx": true
      }
    }
  }
  ```

  不同的语法分析器可能具有不同的选项。

- env

  指定执行环境，一个执行环境会预定义一组全局变量。示例：

  使用注释配置

  ```
  /* eslint-env node, mocha */
  ```

   

  使用配置文件：

  ```
  {
    "env": {
      "browser": true,
      "node": true
    }
  }
  ```

  ```
  
  ```

- globals

  使用未在当前文件中定义的全局变量时，会命中  no-undef  规则，通过 globals 配置指定的全局变量无视  no-undef  规则。示例：

  使用注释配置

  ```
  /* global var1, var2 */
  
  /* global var1:writable, var2:writable */
  ```

   

  在配置文件中指定：

  ```
  {
    "globals": {
      "var1": "writable",
      "var2": "readonly"
    }
  }
  ```

   

- plugins

  插件是第三方定制的规则集合，plugins参数用于指定第三方插件，虽然 官方提供了上百种的规则可供选择，但是这还不够，因为官方的规则只能检查标准的 JavaScript 语法，如果你写的是 JSX 或者 Vue  单文件组件，ESLint 的规则就开始束手无策了，这个时候就需要安装 ESLint 的插件，来定制一些特定的规则进行检查，ESLint  的插件与扩展一样有固定的命名格式，以  eslint-plugin- 开头，插件名中的  eslint-plugin-  使用的时候也可以省略这个头。示例：

  ```
  {
    "plugins": [
      "plugin1",
      "eslint-plugin-plugin2"
    ]
  }
  ```

  使用插件时必须安装其 npm 包。

  *注意*

  - 受限于 Node 的模块化机制，全局安装的 ESLint 只能使用全局安装的插件，局部安装的 ESLint 只能使用局部安装的插件，不支持全局插件和局部插件的混合使用。

- rules

  指定 ESLint 校验规则。

  所有规则独立于其他规则，可单独配置。

  ESLint 内置规则：[eslint.org/docs/rules/](https://eslint.cn/docs/rules/)

  规则组成：规则名、错误级别、附加选项

  规则配置模式：

  ```
  规则名: [错误级别, 附加选项]
  
  规则名: 错误级别
  ```

  ```
   
  ```

  示例：

  ```
  "rules": {
    "semi": ["error", "always"],
    "quotes": ["error", "double"]
  }
  ```

   

  在上述示例中，"semi" 和 "quotes" 是规则名，"error" 是规则错误级别，"always" 和 "quotes" 分别是 semi 和 quotes 各自特有的附加选项。

  ESLint 定义了三种错误级别：

  - "off" 或 0 - 关闭， 代表关闭此条规则，即不做关于此规则的检测。
  - "warn" 或 1 - 警告， 此条规则设置为需要进行*warning*警告的规则，eslint程序的返回码不变。
  - "error" 或 2 - 错误， 将此条规则设置为需要进行`error`报错的规则，eslint程序的返回码将变为1。。

  使用注释配置

  ```
  /* eslint eqeqeq: "off", curly: "error" */
  
  // 如果规则有额外选项，可以用数组字面量语法
  /* eslint quotes: ["error", "double"], curly: 2 */
  ```

   

  使用配置文件

  ```
  {
    "rules": {
      "eqeqeq": "off",
      "curly": "error",
      "quotes": ["error", "double"]
    }
  }
  ```

   

  可在文件中使用注释配置禁用全部规则或指定规则：

  - 块级禁用

    ```
    /* eslint-disable */
    alert('foo');
    /* eslint-enable */
    
    /* eslint-disable no-alert, no-console */
    alert('foo');
    console.log('bar');
    /* eslint-enable no-alert, no-console */
    ```

     

  - 文件级禁用

    ```
    /* eslint-disable */
    alert('foo');
    
    /* eslint-disable no-alert */
    alert('foo');
    ```

  - 在指定行中禁用

    ```
    alert('foo'); // eslint-disable-line
    alert('foo'); // eslint-disable-line no-alert
    alert('foo'); // eslint-disable-line no-alert, quotes, semi
    alert('foo'); /* eslint-disable-line */
    alert('foo'); /* eslint-disable-line no-alert */
    alert('foo'); /* eslint-disable-line no-alert, quotes, semi   */
    
    // eslint-disable-next-line
    // eslint-disable-next-line no-alert
    // eslint-disable-next-line no-alert, quotes, semi
    /* eslint-disable-next-line */
    /* eslint-disable-next-line no-alert */
    /* eslint-disable-next-line no-alert, quotes, semi */
    alert('foo');
    ```

    ```
    /*eslint-disable*/  // 以下禁用检查
    /*eslint-enable*/ // 以下启用检查
    /*global*/ // 全局
    /*eslint*/
    /*eslint-env*/ // 局部修改环境变量
    // eslint-disable-line // 当前行跳过检查
    // eslint-disable-next-line // 下一行跳过检查
    ```

     

- overrides

  排除 ESLint 校验规则。示例：

  ```
  {
    "rules": {...},
    "overrides": [
      {
        "files": ["*-test.js","*.spec.js"],
        "excludedFiles": "*.test.js",
        "rules": {
          "no-unused-expressions": "off"
        }
      }
    ]
  }
  ``` 

  对于匹配  overrides.files  且不匹配  overrides.excludedFiles  的 文件， overrides.rules  中的规则会覆盖 rules  中的同名规则。

- settings

  指定一个对象，这个对象会被提供给每一个将要执行的规则。

- root

  ESLint 在目录树种搜索配置文件时，若遇到 root 参数为 true 的配置文件，就会停止搜索，否则沿着目录树一直向父级目录的方向搜索到系统根目录为止。

- extends

  配置文件可以在已有配置的基础上进行扩展，extends 用于指定基础配置。该参数的值为以下之一：

  - 一个指定基础配置来源的字符串
  - 一个指定基础配置来源的字符串的数组：数组中每个配置扩展它前面的配置

  ESLint 支持递归扩展配置，所以基础配置中也可包含 extends 参数,扩展就是直接使用别人已经写好的 lint 规则，方便快捷。

  当指定基础配置时，rules 参数指定的规则可按如下几种方式进行扩展：

  - 启用基础配置中没有规则
  - 继承基础配置中的规则，改变其错误级别，但不改变其附加选项： 
    - 基础配置: "eqeqeq": ["error", "allow-null"]
    - 扩展配置: "eqeqeq": "warn"
    - 最终有效配置："eqeqeq": ["warn", "allow-null"]
  - 覆盖基础配置中的规则: 
    - 基础配置："quotes": ["error", "single", "avoid-escape"]
    - 扩展配置："quotes": ["error", "single"]
    - 最终有效配置: "quotes": ["error", "single"]

  使用 extends 指定的基础配置来源可以是：

  - eslint:recommended，启用 [ESLint 内置规则集](https://eslint.cn/docs/rules/) 的一个子集。

  - 共享配置包，一类导出 ESLint 配置对象的 npm 包，比如 [eslint-config-standard](https://www.npmjs.com/package/eslint-config-standard)、[eslint-config-airbnb](https://www.npmjs.com/package/eslint-config-airbnb) 等。共享配置包需要安装才能使用，配置时可省略报名中的 eslint-config- 前缀。如果你觉得自己的配置十分满意，也可以将自己的 lint 配置发布到 npm 包，只要将包名命名为 eslint-config-xxx 即可，同时，需要在 package.json 的 peerDependencies 字段中声明你依赖的 ESLint 的版本号。。

    示例：

    ```
    {
      "extends": ["airbnb"]
      "rules": {
        "no-set-state": "off"
      }
    }
    ```
     

  - 插件导出的命名配置， plugin 开头的是扩展是插件类型，也可以直接在 plugins 属性中进行设置，其配置值由以下几部分组成：

    - plugin:
    - 插件包名（可省略前缀，例如：react）
    - /
    - 配置名（例如：recommended）

    示例：

    ```
    {
      "plugins": [
        "react"
      ],
      "extends": [
        "eslint:recommended",
        "plugin:react/recommended"
      ],
      "rules": {
        "no-set-state": "off"
      }
    }
    ```

  - 配置文件，通过配置文件的绝对路径或相对路径指定。

    示例：

    ```
    {
      "extends": [
        "./node_modules/coding-standard/eslintDefaults.js",
        "./node_modules/coding-standard/.eslintrc-es6",
        "./node_modules/coding-standard/.eslintrc-jsx",
        "./my-eslint-config.js"
      ],
      "rules": {
        "eqeqeq": "warn"
      }
    }
    ```

  - eslint:all，启动所有 ESLint 内置规则，不推荐。

- 忽略文件和目录

  使用 .eslintignore 文件指定要忽略的文件和目录，忽略模式的指定使用 .gitignore 的 [规范](https://git-scm.com/docs/gitignore)。

  ESLint 默认忽略 /node_modules/* 和 /bower_components/*。

  示例：

  ```
  # /node_modules/* and /bower_components/* in the project root are ignored by default
  
  # Ignore built files except build/index.js
  build/*
  !build/index.js
  /node_modules
  node_modules
  /node_modules/*
  /dist
  dist
  /dist/*
  ```

  #### 使用 eslint-config-standard

[eslint-config-standard](https://www.npmjs.com/package/eslint-config-standard)标准风格插件

安装

```
npm install eslint-config-standard
```

安装相关需要的其它插件

```
npm install --save-dev eslint-config-standard eslint-plugin-standard eslint-plugin-promise eslint-plugin-import eslint-plugin-node
```

然后，将其添加到您的`.eslintrc`文件中：

```
{
  "extends": "standard"
}
```

####  使用 babel-eslint

[babel-eslint](https://www.npmjs.com/package/babel-eslint) 是一款用于 ESLint 的语法分析器，它支持使用 ESLint 分析所有 babel 兼容的代码。

使用 babel-eslint 需要安装 babel 并提供 babel 配置文件。

ESLint 核心规则不支持实验阶段的语法，如果要使用这部分语法，需要配合 [eslint-plugin-babel](https://github.com/babel/eslint-plugin-babel) 使用。

安装

```
# 安装 babel-eslint
npm install -D babel-eslint

# 安装 eslint-plugin-babel
npm install -D eslint-plugin-babel
 
```

配置

```
{
    "parser": "babel-eslint",
    "plugins": ["babel"]
}
```

#### 使用 eslint-plugin-import

 eslint-plugin-import  用于校验es6的import规则，如果增加import plugin，在我们使用webpack的时候，如果你配置了resove.config.js的alias，那么我们希望import plugin的校验规则会从这里取模块的路径，此时需要配置

```
“rules”: {},
"settings": {
        // 使用webpack中配置的resolve路径
        "import/resolver": "webpack" 
}
```

  参考：

[ESLint 使用指南](https://juejin.im/post/5c92e11b5188251571729ef0#heading-15)

[深入理解 ESLint](https://segmentfault.com/a/1190000019896962#articleHeader0)
