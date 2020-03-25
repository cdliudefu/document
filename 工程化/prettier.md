####  Prettier--code formatter这是一款格式化js、css代码插件
**prettier**是中文意思是“漂亮的、机灵的”，也是一个流行的代码格式化工具，他能解析代码，使用你自己设定的规则来重新打印出格式化规范的代码。
Prettier具有以下几个点：
- 可配置化
- 支持多种语言
- 集成多数的编辑器
- 简洁的配置项
####  如何对Prettier进行配置
一共有三种方式支持对Prettier进行配置：
1、根目录创建 **.prettierrc**文件，能够写入**YML**、**JSON**的配置格式，并且支持**.yaml**、**.yml**、**.json**、**.js**后缀
2、根目录创建**.prettier.config.js**文件，并对外**export**一个对象
3、在**package.json**中新建**prettier**属性
下面我那使用** prettierrc.js **的方式对** prettier ** 进行配置，同时说明配置作用。
```js
module.exports = {
      // 使用 2 个空格缩进
      tabWidth: 2,
      // 不使用缩进符，而使用空格
      useTabs: false,
      // 行尾需要有分号
      semi: true,
      // 使用单引号
      singleQuote: true,
      // 对象的 key 仅在必要时用引号
      quoteProps: 'as-needed',
      // jsx 不使用单引号，而使用双引号
      jsxSingleQuote: false,
      // 末尾不需要逗号
      trailingComma: 'none',
      // 大括号内的首尾需要空格
      bracketSpacing: true,
      // jsx 标签的反尖括号需要换行
      jsxBracketSameLine: false,
      // 箭头函数，只有一个参数的时候，也需要括号
      arrowParens: 'always',
      // 每个文件格式化的范围是文件的全部内容
      rangeStart: 0,
      rangeEnd: Infinity,
      // 不需要写文件开头的 @prettier
      requirePragma: false,
      // 不需要自动在文件开头插入 @prettier
      insertPragma: false,
      // 使用默认的折行标准
      proseWrap: 'preserve',
      // 根据显示样式决定 html 要不要折行
      htmlWhitespaceSensitivity: 'css',
      // 换行符使用 lf
      endOfLine: 'lf'
}
```

配置大概列出了这些，还有一些其他配置可以在[官方文档](https://prettier.io/docs/en/options.html)进行查阅。

注意一点，parser的配置项官网列出了如下可选项：

- `babylon`
- `flow`
- `typescript Since v1.4.0`
- `postcss Since v1.4.0`
- `json Since v1.5.0`
- `graphql Since v1.5.0`
- `markdown Since v1.8.0`

但是如果你使用了vue的单文件组件形式，记得将 `parser` 配置为vue，目前官方文档没有列出来。当然如果你自己写过AST的解析器，也可以用你自己的写的 `parser: require("./my-parser") `。

## Prettier + editorConfig 搭配使用处理旧项目文件

如果你有一个旧项目想通过`Prettier` + `editorConfig`来简单规范一下格式也可以使用这个工具：
 1.分别安装两个工具不必多说了
 2.创建  `.prettierrc` 配置文件
 该配置文件会继承 `editorConfig` 的配置
 3.向 `package.json` 添加可执行脚本: `prettier": "prettier --write ./* `
 例如：

```
prettier": "prettier --write './src/**/*.js' './src/**/*.jsx' './src/**/*.css' './src/**/*.scss'
```

4.创建  `.prettierignore`  文件，用来忽略一些文件 && 目录
 5.一切准备就绪之后 !!!!  `npm run prettier`  就可以看到提示文件更改记录了

`.prettierrc.js`配置文件：

```
# .prettierrc.js 配置文件

// https://prettier.io/docs/en/options.html


module.exports = {
  trailingComma: "none", // 数组 对象尾随不需要逗号
  bracketSpacing: true, // 对象空格 { foo: bar }, false=> {foo:bar}
  jsxBracketSameLine: false, // jsx闭标签是否另起一行 <Test></Test>
  arrowParens: 'always', // 箭头函数，只有一个参数的时候，也需要括号
  singleQuote: true, // 单引号
  semi: true, //是否在语句末尾就分号
};
```

`.prettierignore` 文件配置

```
# .prettierignore 文件配置
/node_modules
/dist
/src/lib/
/src/global/fonts
  

/docs
/.vscode
node_modules
.eslintrc.js
package.json
package-lock.json
```
# prettier 配置选项 (官网文档译文)

支持自定义的代码风格，可在CLI和API中使用。

### 单行代码的最大宽度 Print Width

指定代码换行的行长度。单行代码宽度超过指定的最大宽度，将会换行。

为了便于阅读，官方建议不要使用超过80个字符。我这边设置了160

| 默认 | CLI                 | API/.prettierrc/prettier.config.js/package.json["prettier"] |
| ---- | ------------------- | ----------------------------------------------------------- |
| 80   | --print-width <int> | printWidth: <int>                                           |

（如果在格式化 Markdown 时不想要换行，可以设置 Prose Wrap （是否换行） 选项以禁用它。）

### Tab宽度 Tab Width

指定每个缩进级别的空格数。

| 默认 | CLI               | API/.prettierrc/prettier.config.js/package.json["prettier"] |
| ---- | ----------------- | ----------------------------------------------------------- |
| 2    | --tab-width <int> | tabWidth: <int>                                             |

### Tab Tabs

使用制表符 (tab) 缩进行而不是空格 (space)。

> 1. 如果设置了制表符缩进，并且一个缩进单位4个空格，那么编辑器一个 tab 键，出现的间隔就是 "一"，一个大横杆
> 2. 如果设置了空格缩进，并且一个缩进单位4个空格，那么编辑器一个 tab 键，出现的间隔就是 "····"，四个小点
> 3. 上面两个长度单位是一样的，都是4个空格，但是空格缩进，在代码进行空白处选择的时候，是一格格选的，制表符缩进是一下子4格选的

| 默认  | CLI        | API/.prettierrc/prettier.config.js/package.json["prettier"] |
| ----- | ---------- | ----------------------------------------------------------- |
| false | --use-tabs | useTabs: <bool>                                             |

（Tabs 将用于缩进，但 Prettier 使用空格来对齐代码，例如三元组。）

### 分号 Semicolons

在语句末尾打印分号。

有效选项：

true - 在每个语句的末尾添加分号。
 false- 仅在可能引入ASI故障的行的开头添加分号。

| 默认 | CLI       | API/.prettierrc/prettier.config.js/package.json["prettier"] |
| ---- | --------- | ----------------------------------------------------------- |
| true | --no-semi | semi: <bool>                                                |

### 引号 Quotes

使用单引号而不是双引号。

笔记：

JSX 引用忽略此选项 - 请参阅 jsx-single-quote。
 如果引号数超过其他引号，则使用较少的引用将用于格式化字符串 

 示例：

```javascript
"I'm double quoted"
// 结果
"I'm double quoted"
```

和

```javascript
"This \"example\" is single quoted"
// 结果
'This "example" is single quoted'。
```

| 默认  | CLI            | API/.prettierrc/prettier.config.js/package.json["prettier"] |
| ----- | -------------- | ----------------------------------------------------------- |
| false | --single-quote | singleQuote: <bool>                                         |

### 引号属性 Quote Props

引用对象中的属性时更改。

有效选项：

"as-needed" - 仅在需要时在对象属性周围添加引号。

 "consistent" - 如果对象中至少有一个属性需要引号，则引用所有属性。

 "preserve" - 尊重对象属性中引号的输入用法。

| 默认        | CLI                                                 | API/.prettierrc/prettier.config.js/package.json["prettier"] |
| ----------- | --------------------------------------------------- | ----------------------------------------------------------- |
| "as-needed" | --quote-props <as-needed \| consistent \| preserve> | quoteProps: "<as-needed \| consistent \| preserve>"         |

### JSX引号 JSX Quotes

在JSX中使用单引号而不是双引号。

| 默认  | CLI                | API/.prettierrc/prettier.config.js/package.json["prettier"] |
| ----- | ------------------ | ----------------------------------------------------------- |
| false | --jsx-single-quote | jsxSingleQuote: <bool>                                      |

### 尾随逗号 Trailing Commas

多行时尽可能打印尾随逗号。（例如，单行数组永远不会得到尾随逗号。）

有效选项：

"none" - 没有尾随逗号。

 "es5" - 在ES5中有效的尾随逗号（对象，数组等）

 "all" - 尽可能使用尾随逗号（包括函数参数）。这需要 nodejs 8。

| 默认   | CLI                                   | API/.prettierrc/prettier.config.js/package.json["prettier"] |
| ------ | ------------------------------------- | ----------------------------------------------------------- |
| "none" | --trailing-comma <none \| es5 \| all> | trailingComma: "<none \| es5 \| all>"                       |

### 括号间距（这里指{、}） Bracket Spacing

在对象文字中打印括号之间的空格。

有效选项：

true- 例子：{ foo: bar }。

 false- 例子：{foo: bar}。

| 默认 | CLI                  | API/.prettierrc/prettier.config.js/package.json["prettier"] |
| ---- | -------------------- | ----------------------------------------------------------- |
| true | --no-bracket-spacing | bracketSpacing: <bool>                                      |

### JSX 括号（这里指<、>） JSX Brackets

将 > 多行 JSX 元素放在最后一行的末尾，而不是单独放在下一行（不适用于自闭元素）。

有效选项：

true - 示例：

```html
<button
  className="prettier-class"
  id="prettier-id"
  onClick={this.handleClick}>
  Click Here
</button>
```

false - 示例：

```html
<button
  className="prettier-class"
  id="prettier-id"
  onClick={this.handleClick}
>
  Click Here
</button>
```

| 默认  | CLI                     | API/.prettierrc/prettier.config.js/package.json["prettier"] |
| ----- | ----------------------- | ----------------------------------------------------------- |
| false | --jsx-bracket-same-line | jsxBracketSameLine: <bool>                                  |

### 箭头函数圆括号 Arrow Function Parentheses

首先在v1.9.0中提供

在单个箭头函数参数周围加上括号。

有效选项：

"avoid" - 尽可能省略parens。例：x => x

 "always" - 始终包括parens。例：(x) => x

| 默认    | CLI                              | API/.prettierrc/prettier.config.js/package.json["prettier"] |
| ------- | -------------------------------- | ----------------------------------------------------------- |
| "avoid" | --arrow-parens <avoid \| always> | arrowParens: "<avoid \| always>"                            |

### 范围 Range

仅格式化文件的一部分。

这两个选项可用于格式化以给定字符偏移量开始和结束的代码（分别包含和排除）

| 默认     | CLI                 | API/.prettierrc/prettier.config.js/package.json["prettier"] |
| -------- | ------------------- | ----------------------------------------------------------- |
| 0        | --range-start <int> | rangeStart: <int>                                           |
| Infinity | --range-end <int>   | rangeEnd: <int>                                             |

### 解析器 Parser

指定要使用的解析器。

Prettier 会自动从输入文件路径中推断出解析器，因此您不必更改此设置。

这两个 babel 和 flow 解析器都支持相同的 JavaScript 功能集（包括Flow类型注释）。

 它们可能在某些边缘情况下有所不同，因此如果您碰到其中一个，您可以尝试 flow 而不是 babel。

有效选项：

"babel"（来自 @babel/parser ）命名为 "babylon" until v1.16.0

 "babel-flow"（与解析器 "babel" 一样，但是明确地启用 Flow 解析以避免歧义）首先在v1.16.0中可用

 "flow"（通过 flow-parser）

 "typescript"（来自 @typescript-eslint/typescript-estree）首先在v1.4.0中提供

 "css"（通过 postcss-scss 和 postcss-less，自动检测使用哪个解释器）首先在v1.7.1中提供

 "scss"（与解析器 "css"一样，更喜欢 postcss-scss ）首先在v1.7.1中提供

 "less"（与解析器 "css"一样，更喜欢 postcss-less ）首先在v1.7.1中提供

 "json"（来自 @babel/parser parseExpression ）首先在v1.5.0中提供

 "json5"（与解析器 "json"一样，但输出为 json5 ）首先在v1.13.0中可用

 "json-stringify"（与解析器 "json"一样，但输出类似 JSON.stringify ）首先在v1.13.0中提供

 "graphql"（通过 graphql/language ）首先在v1.5.0中提供

 "markdown"（通过 remark-parse ）首先在v1.8.0中提供

 "mdx"（通过 remark-parse 和 @mdx-js/mdx ）首先在v1.15.0中提供

 "html"（通过 angular-html-parser ）首先在1.15.0中提供

 "vue"（与解析器 "html"一样，但也格式化于 vue 的语法）首先在1.10.0中可用

 "angular"（与解析器 "html"一样，但也通过 angular-estree-parser 格式化 augular 特定的语法）首先在1.15.0中提供

 "lwc"（与解析器 "html"一样，但也为 LWC 特定语法格式化）首先在1.17.0中可用

 "yaml"（通过 yaml 和 yaml-unist-parser ）首先在1.14.0中提供

 还支持自定义解析器。首先在v1.5.0中提供

| 默认 | CLI                                     | API/.prettierrc/prettier.config.js/package.json["prettier"] |
| ---- | --------------------------------------- | ----------------------------------------------------------- |
| 没有 | --parser <string>  --parser ./my-parser | parser: "<string>"  parser: require("./my-parser")          |

注意：默认值为"babylon"v1.13.0。

### 文件路径 File Path

指定用于推断要使用的解析器的文件名。

例如，以下将使用CSS解析器：

cat foo | prettier --stdin-filepath foo.css

| 默认 | CLI                       | API/.prettierrc/prettier.config.js/package.json["prettier"] |
| ---- | ------------------------- | ----------------------------------------------------------- |
| 没有 | --stdin-filepath <string> | filepath: "<string>"                                        |

### 需要编译指示 Require pragma

首先在v1.7.0中提供

Prettier 可以将自己限制为仅在文件顶部格式化包含特殊注释（称为pragma）的文件。

 当逐步将大型未格式化的代码库转换为更漂亮的代码库时，这非常有用。

例如，提供以下内容作为其第一个注释的文件将被格式化 --require-pragma：

```javascript
/**
 * @prettier
 */
```

或者

```javascript
/**
 * @format
 */
```

| 默认  | CLI              | API/.prettierrc/prettier.config.js/package.json["prettier"] |
| ----- | ---------------- | ----------------------------------------------------------- |
| false | --require-pragma | requirePragma: <bool>                                       |

### 插入Pragma Insert Pragma

首先在v1.8.0中提供

Prettier 可以在文件顶部插入一个特殊的 @format 标记，指定文件格式需要被格式化。

 当与 --require-pragma 选项一起使用时，这很有效。

 如果文件顶部已有 docblock，则此选项将使用 @format 标记向其添加换行符。

| 默认  | CLI             | API/.prettierrc/prettier.config.js/package.json["prettier"] |
| ----- | --------------- | ----------------------------------------------------------- |
| false | --insert-pragma | insertPragma: <bool>                                        |

### 是否换行 Prose Wrap

首先在v1.8.2中提供

默认情况下，Prettier 将按原样包含 markdown 文本，因为某些服务使用对行敏感的渲染器，例如 GitHub 注释和 BitBucket。

 在某些情况下，您可能希望依赖编辑器/查看器，因此此选项允许您选择退出 "never"。

有效选项：

"always" - 如果超过打印宽度，请换行。

 "never" - 不要换行。

 "preserve" - 按原样显示。首先在v1.9.0中提供

| 默认       | CLI                                        | API/.prettierrc/prettier.config.js/package.json["prettier"] |
| ---------- | ------------------------------------------ | ----------------------------------------------------------- |
| "preserve" | --prose-wrap <always \| never \| preserve> | proseWrap: "<always \| never \| preserve>"                  |

### HTML空白灵敏度 HTML Whitespace Sensitivity

首次在v1.15.0中提供

指定 HTML 文件的全局空白区域敏感度

有效选项：

"css"- 遵守CSS display属性的默认值。

 "strict" - 空格被认为是敏感的。

 "ignore" - 空格被认为是不敏感的。

html 中空格也会占位，影响布局，prettier 格式化的时候可能会将文本换行，造成布局错乱

```html
<a href="https://prettier.io/">Prettier is an opinionated code formatter.</a>
<!-- 变成 -->
<!-- "Prettier is an opinionated code formatter. " 另起一行，在页面的布局上就会多一个节点文本出来 -->
<a href="https://prettier.io/">
  Prettier is an opinionated code formatter.
</a>
```

| 默认  | CLI                                                     | API/.prettierrc/prettier.config.js/package.json["prettier"] |
| ----- | ------------------------------------------------------- | ----------------------------------------------------------- |
| "css" | --html-whitespace-sensitivity <css \| strict \| ignore> | htmlWhitespaceSensitivity: "<css \| strict \| ignore>"      |

### 行结束 End of Line

首次出现在1.15.0中

由于历史原因，在文本文件中存在两种常用的行结尾的风格。那是\n（或LF换行）和\r\n（或CRLF用于回车+换行）。

 前者在 Linux 和 macOS 上很常见，而后者在 Windows 上很普遍。可以在维基百科上找到解释其原因的一些细节。

默认情况下，Prettier 会保留给定文件已使用的行尾的风格。它还将一个文件中的混合行结尾转换为它在第一行末尾找到的结尾。

当人们在不同操作系统上协作项目时，很容易在中央 git 存储库中找到混合行结尾。

 Windows 用户也可能会意外地将已提交文件中的行结尾更改 LF 为 CRLF。

 这样做会产生很大的影响 git diff，如果在代码审查过程中没有注意到，那么file（git blame）的所有逐行历史都会丢失。

如果你想确保你的 git 存储库在 Prettier 所涵盖的文件中只包含 Linux 风格的行结尾：

1. 将 endOfLine 选项设置为 lf
2. 配置一个 pre-commit 钩子，运行 Prettier
3. 配置 Prettier 在CI管道中运行 --check flag
4. Windows用户在使用您的仓库之前，运行 git config core.autocrlf false，以便git 在  checkout 时不会转换 LF 为 CRLF。或者，您可以添加 * text=auto eol=lf 到 repo  的.gitattributes 文件来实现此目的。
    所有操作系统中的所有现代文本编辑器都能够在使用 \n（LF）时正确显示行结尾。但是，旧版本的 Windows 记事本会直观地将这些行压缩成一行。

有效选项：

"auto" - 维护现有的行结尾（通过查看第一行之后使用的内容来标准化一个文件中的混合值）

 "lf"- Line Feed only（\n），在 Linux 和 macOS 以及 git repos 内部很常见

 "crlf"- 回车符+换行符（\r\n），在 Windows 上很常见

 "cr"- 仅限回车符（\r），很少使用

| 默认   | CLI                                      | API/.prettierrc/prettier.config.js/package.json["prettier"] |
| ------ | ---------------------------------------- | ----------------------------------------------------------- |
| "auto" | --end-of-line <auto \| lf \| crlf \| cr> | endOfLine: "<auto \| lf \| crlf \| cr>"                     |

#### 与eslint搭配使用的时候通常需要安装以下几个相关联的包:

```js
npm i prettier eslint-config-prettier eslint-plugin-prettier -D
```
