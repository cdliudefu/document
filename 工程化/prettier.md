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
#### 与eslint搭配使用的时候通常需要安装以下几个相关联的包:
```js
npm i prettier eslint-config-prettier eslint-plugin-prettier -D
```
