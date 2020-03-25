[StyleLint](https://stylelint.io/) 是一个强大的、现代化的css检测工具，与ESLint类似，是通过定义一系列的编码风格规则帮助我们避免样式表中出现的错误。

#### 安装stylelint
```js
npm i stylelint -D
```
安装stylint-config-standard和stylelint-order
```js
npm i stylelint-config-standard stylelint-order -D
```
其中，stylelint是运行工具，stylelint-config-standard是stylelint的推荐配置，stylelint-order是CSS属性排序插件(先写定位，再写盒模型，再写内容区样式，最后写 CSS3 相关属性)。
####  配置方式：
按顺序查找，任何一项有值，就会结束查找
- 在 package.json 中的stylelint属性指定规则
- 在 .stylelintrc 文件中指定，文件格式可以是JSON或者YAML。也可以给该文件加后缀，像这样： .stylelintrc.json , .stylelintrc.yaml , .stylelintrc.yml , .stylelintrc.js
- stylelint.config.js 文件，该文件 exports 一个配置对象
#### 语法格式：
rules优先级大于extends，建议采用extends方式统一管理
```js
module.exports = {
  processors: [],
  plugins: [],
  extends: "stylelint-config-standard", // 这是官方推荐的方式
  rules: {
    "at-rule-empty-line-before": "always"|"never",
    "at-rule-name-case": "lower"|"upper",
    "block-no-empty": true,
  }
};
```
#### 配置项解析

### rules

`rules`是一个对象，属性名为规则名称，属性值为规则取值，它告诉`stylelint`该检查什么，该怎么报错。所有规则默认都是关闭的。

所有`stylelint`的规则详情戳[这里](https://stylelint.io/user-guide/rules)。

#### 规则名称

- 由连字符组成的小写单词
- 由两个部分组成： 
  - 第一部分描述该规则应用于什么东西
  - 第二部分表示该规则检查了什么

```
"number-leading-zero"
// ↑          ↑
// the thing  what the rule is checking
```

规则应用于整个样式表时只包含，第二个部分：

```
"no-eol-whitespace"
"indentation"
//    ↑
// what the rules are checking
```

规则取值

规则类型不同，支持的值也不同，所有取值戳[这里](https://stylelint.io/user-guide/example-config)，以下是几个示例：

```
{
  "rules": {
    "at-rule-blacklist": string|[],
    "at-rule-empty-line-before": "always"|"never",
    "at-rule-name-case": "lower"|"upper",
    "block-no-empty": true
    ...
  }
}
```

值为 null 时表示禁用该规则：

```
{
  "rules": {
    "block-no-empty": null
  }
}
```

除了规则本身的取值之外，`stylelint`还支持一些自定义配置，给规则传递一个数组即可，数组第一项是规则值，第二项是自定义配置。

 

```
"selector-pseudo-class-no-unknown": [true, {
  "ignorePseudoClasses": ["global"]
}]
```

通过配置项，你可以指定：

- `severity`，错误级别，取值`"warning"`或者`"error"`。默认情况下，所有规则的错误级别都是`"error"`，通过`defaultSeverity`可以修改此默认值。不过，需要针对某规则修改错误级别就需要用到该属性。

```
"indentation": [2, { 
  "severity": "warning" 
}]
```

- `message`，自定义错误信息。

```
"color-hex-case": ["lower", {
    "message": "Lowercase letters are easier to distinguish from numbers"
}]
```

如果你需要严格的定制，写一个[自定义格式化器](https://stylelint.io/developer-guide/formatters)会给你最大控制权

#### extends 扩展插件

`stylelint的配置可以 *extend* 一个已存在的配置文件(无论是你自己的还是第三方的配置)。当一个配置继承了里一个配置，它将会添加自己的属性并覆盖原有的属性`。

你也可以将`extends`设置为一个数组，每一项都是一个独立的`stylelint`配置项，后一项将会覆盖前一项，而接下来你自己书写的 rules 规则可以覆盖他们所有。

以下示例中，`rules`中的`indentation`和`number-leading-zero`将会覆盖`stylelint-config-standard`中对应的规则。

```
{
  "extends": "stylelint-config-standard",
  "rules": {
    "indentation": "tab",
    "number-leading-zero": null
  }
}
```

以下，`./myExtendableConfig`中的配置将会覆盖`stylelint-config-standard`中的对应配置，而`rules`中的`indentation`将会覆盖`./myExtendableConfig`中对应的规则。

```
{
  "extends": [
    "stylelint-config-standard",
    "./myExtendableConfig"
  ],
  "rules": {
    "indentation": "tab"
  }
}
```

说清楚优先级之后，我们来详细了解一下`extends`的取值情况。

`extends`的值实际上一个`定位器`（或者一个包含若干`定位器`的数组），所有可以通过`require`来使用的资源都可以作为`extends`的值。因此，可以使用 Node 的 `require.resolve()` 算法适应任何格式。这意味着一个“定位器”可以是：

- `node_modules`中的某个模块名称 ，该模块的主文件需要返回一个配置对象 (比如，`stylelint-config-standard`；模块的 `main` 文件必须是一个有效的 JSON 配置)
- 一个带有 `.js` 或 `.json` 扩展名的文件 (which makes sense 如果你在 Node 上下文中创建了一个 JS 对象，并将它传入也是有效的)的绝对路径。
- 一个带有 `.js` 或 `.json` 扩展名的文件的相对路径，相对于引用的配置 (例如，如果 configA 是 `extends: "../configB"`，我们将查找 `configB` 相对于 configA)。

*正因为extends，你可以创建和使用可分享的 stylelint 配置。* 如果你要发布你的配置到 npm，在你的 `package.json` 文件中使用 `stylelint-config` 关键字。

#### plugins 插件列表：

插件一般是由社区提供的，对`stylelint`已有规则进行扩展，一般可以支持一些非标准的`css`语法检查或者其他特殊用途。一个插件会提供一个或者多个检查规则。

`plugins`是一个数组，包含一组插件的`定位器`，这些`定位器`的取值跟`extends`一致。

`plugins`声明后还需要在`rules`中使用它，具体规则名称以及可能的取值需要去查看每个插件的文档。

```
{
  "plugins": [
    "../some-rule-set.js"
  ],
  "rules": {
    "some-rule-set/first-rule": "everything",
    "some-rule-set/second-rule": "nothing",
    "some-rule-set/third-rule": "everything"
  }
}
```

[获取更多插件](https://stylelint.io/user-guide/plugins)。

#### processors 处理器列表：

你还可以在`stylelint`的处理流中加入自己的处理函数，就是这里的`processors`。

`processors`只能作为  *命令行*  和  *Node API*  使用，`PostCss`的插件会忽略它们。

通过`processors`，我们可以让`styleline`去处理`html`中`style`标签里面的`css`代码，`MarkDown`里面的`css`代码块或者`js`里面一段包含`css`的字符串。

使用方法如下：

```
{
  "processors": [
    "stylelint-html-processor",
    [ "some-other-processor", { "optionOne": true, "optionTwo": false } ]
  ],
  "rules": {..}
}
```

 

```
processors的每一项也是一个定位器，需要额外的参数时，可以传递一个数组，第一项是定位器，第二项是需要的参数。
```

[获取更多处理器。](https://stylelint.io/user-guide/processors)

###  defaultSeverity

所有在第二个选项中没有指定严重级别的规则的默认严重级别。`severity` 可用的值为：

- `"warning"`

- #### `"error"`

### ignoreFiles

一个文件匹配规则，或者一组文件匹配规则，来指定需要忽略的文件。

更高效的忽略文件的方法是通过 .stylelintignore 文件或者调整一下你的文件匹配规则。

 

ignoreFiles方式

```
// .stylelintignore
*.js
*.png
*.eot
*.ttf
*.woff
```

#### 片段禁用规则

```
/* stylelint-disable */
/* （请说明禁止检测的理由）前端组件限制类名 */
  .cropper_topContainer .img-preview {
    border: 0 none;
  }
/* stylelint-enable */
```

 

## fix方式

- stylelint --fix 就能搞定 [更多语法规则
  ](https://stylelint.io/user-guide/cli)

一键fix

在 package.json 中的 scripts 添加指令，然后 npm run lintcss 即可

```
{
  "scripts": {
    "lintcss": "stylelint 'src/**/*.css' --fix",
  }
}
```

#### 手动fix

不放心的话，就针对指定文件自己执行， **文件一定要用""括起来**

```
stylelint "src/less/bulma-cloud.less" --fix
```

#### 问题排除： 

![img](https://img2018.cnblogs.com/i-beta/733258/202002/733258-20200209104836684-1072463196.png)

错误提示：

```
Unexpected unknown at-rule "@function" (at-rule-no-unknown) 

Unexpected unknown at-rule "@each" (at-rule-no-unknown) 

Unexpected unknown at-rule "@if" (at-rule-no-unknown) 

Unexpected unknown at-rule "@return" (at-rule-no-unknown)
```

 

 

 最开始只安装了官方推荐的定义规则"stylelint-config-standard",我想是不是我缺少插件的问题，所以我就多安装了一 个"stylelint-scss"插件，错误已然存在，最后通过"stylelint-scss"的github找到了答案，就是需要添加排除规则。[具体内容请点击访问](https://github.com/kristerkari/stylelint-scss/issues/86)

添加排除规则之后就好了。

```
{
  "extends": [
    "stylelint-config-standard",
    "stylelint-config-css-modules"
  ],
  "plugins": [
    "stylelint-scss"
  ],
  "rules": {
    "at-rule-no-unknown": [ true, {
      ignoreAtRules: ['extend', 'at-root', 'debug', 'warn', 'error', 'if', 'else', 'for', 'each', 'while', 'mixin', 'include', 'content', 'return', 'function']
    }]
  }
}
```

#### 其他

1. 在样式之前标注“/* stylelint-disable */”，可忽略配置的规则；标注“/* stylelint-enable */“，开启配置的规则
2. “/* stylelint-enable */“必须在“/* stylelint-disable */”之后使用
3. 忽略指定的内容：
    （1）.stylelintrc
    {
    "rules": {
    "unit-no-unknown": [true, ignoreUnits:["xxx"]]
    }
    }
    （2）package.json && stylelint.config.js
    {
    "rules": {
    "at-rule-no-unknown": [true, {"ignoreAtRules": ["function", "if",  "return", "include", "extend", "mixin", "else", "while", "for",  "each"]}]
    }
    }

配置文件

 1.
 {
 "rules": {
 "color-hex-case": "lower",
 "color-no-invalid-hex": true,
 "at-rule-name-space-after": "always",
 "at-rule-semicolon-newline-after": "always",
 "at-rule-no-unknown": [true, ignoreAtRules: ["function", "if", "return",  "include", "extend", "mixin", "else", "while", "for", "each"]],
 "function-comma-space-after": "always",
 "function-linear-gradient-no-nonstandard-direction": true,
 "function-url-quotes": "always",
 "function-whitespace-after": "always",
 "number-leading-zero": "never",
 "number-no-trailing-zeros": true,
 "string-no-newline": true,
 "string-quotes": "double",
 "unit-case": "lower",
 "unit-no-unknown": true,
 "value-keyword-case": "lower",
 "value-list-comma-newline-after": "always-multi-line",
 "value-list-comma-space-after": "always",
 "shorthand-property-no-redundant-values": true,
 "declaration-bang-space-after": "never",
 "declaration-bang-space-before": "always",
 "declaration-colon-space-after": "always",
 "declaration-colon-space-before": "never",
 "declaration-block-no-ignored-properties": true,
 "declaration-block-no-shorthand-property-overrides": true,
 "declaration-block-semicolon-newline-after": "always-multi-line",
 "declaration-block-semicolon-newline-before": "never-multi-line",
 "declaration-block-semicolon-space-after": "always-single-line",
 "declaration-block-semicolon-space-before": "never",
 "declaration-block-trailing-semicolon": "always",
 "block-no-empty": true,
 "block-opening-brace-space-after": "always-single-line",
 "block-opening-brace-space-before": "always",
 "block-closing-brace-space-before": "always-single-line",
 "selector-attribute-brackets-space-inside": "always",
 "selector-attribute-operator-space-after": "always",
 "selector-attribute-operator-space-before": "always",
 "selector-combinator-space-after": "always",
 "selector-combinator-space-before": "always",
 "selector-max-compound-selectors": 4,
 "selector-no-universal": true,
 "selector-pseudo-class-case": "lower",
 "selector-pseudo-class-no-unknown": true,
 "selector-pseudo-element-case": "lower",
 "selector-pseudo-element-no-unknown": true,
 "selector-type-case": "lower",
 "selector-max-empty-lines": 0,
 "selector-list-comma-newline-before": "never-multi-line",
 "selector-list-comma-space-after": "always-single-line",
 "rule-nested-empty-line-before": "never",
 "media-query-list-comma-newline-after": "always-multi-line",
 "media-query-list-comma-space-after": "always-single-line",
 "comment-whitespace-inside": "always",
 "max-empty-lines": 1,
 "max-nesting-depth": 4,
 "no-descending-specificity": true,
 "no-duplicate-selectors": true,
 "no-empty-source": true,
 "no-eol-whitespace": true,
 "no-extra-semicolons": true,
 "no-invalid-double-slash-comments": true,
 "no-unknown-animations": true
 }
 }

#### 配置文件说明

| 属性名                                                       | 属性值             | 描述                                                       |
| ------------------------------------------------------------ | ------------------ | ---------------------------------------------------------- |
| [color-hex-case](http://stylelint.io/user-guide/rules/color-hex-case/) | lower              | 颜色值为小写字母                                           |
| [color-no-invalid-hex](http://stylelint.io/user-guide/rules/color-no-invalid-hex) | true               | 颜色值不能为无效值                                         |
| [at-rule-name-space-after](http://stylelint.io/user-guide/rules/at-rule-name-space-after) | always             | @xx 后需空格                                               |
| [at-rule-semicolon-new-new-line](http://stylelint.io/user-guide/rules/at-rule-semicolon-new-new-line) | alway              | @xx 分号后要换行                                           |
| [function-calc-no-unspaced-operator](http://stylelint.io/user-guide/rules/function-calc-no-unspaced-operator) | true               | 方法中的计算符号左右需空格                                 |
| [function-comma-space-after](http://stylelint.io/user-guide/rules/function-comma-space-after) | always             | 方法中逗号后需空格                                         |
| [function-linear-gradient-no-nonstandard-direction](http://stylelint.io/user-guide/rules/function-linear-gradient-no-nonstandard-direction) | true               | linear-gradient()内参数严格按照css规范                     |
| [function-max-line-lines](http://stylelint.io/user-guide/rules/function-max-line-lines) | 0                  | 方法中参数允许0行空行                                      |
| [function-parentheses-newline-inside](http://stylelint.io/user-guide/rules/function-parentheses-newline-inside) | never-multi-line   | 方法中参数允许逗号后换行                                   |
| [function-url-quotes](http://stylelint.io/user-guide/rules/function-url-quotes) | always             | 地址一定要写引号                                           |
| [function-whitespace-after](http://stylelint.io/user-guide/rules/function-whitespace-after) | always             | 方法之间一定要空格                                         |
| [number-leading-zero](http://stylelint.io/user-guide/rules/number-leading-zero) | never              | 数字不以0开头。错误：0.5；正确：.5                         |
| [number-no-trailing-zeros](http://stylelint.io/user-guide/rules/number-no-trailing-zeros) | true               | 不能有数字末尾多余的0。错误：1.000；正确：1                |
| [string-no-newline](http://stylelint.io/user-guide/rules/string-no-newline) | true               | 字符串之前不能有“\n"                                       |
| [string-quotes](http://stylelint.io/user-guide/rules/string-quotes) | double             | 字符串最外层用双引号，而不是单引号                         |
| [unit-case](http://stylelint.io/user-guide/rules/unit-case)  | lower              | 单位小写                                                   |
| [unit-no-unknown](http://stylelint.io/user-guide/rules/unit-no-unknown) | true               | 不允许未知的单位                                           |
| [value-keyword-case](http://stylelint.io/user-guide/rules/value-keyword-case) | lower              | 属性值小写                                                 |
| [value-list-comma-newline-after](http://stylelint.io/user-guide/rules/value-list-comma-newline-after) | always-multi-line  | 属性值列不允许逗号前换行                                   |
| [value-list-comma-space-after](http://stylelint.io/user-guide/rules/value-list-comma-space-after) | always             | 属性值列表逗号后需空格                                     |
| [shorthand-properyu-no-redundat-values](http://stylelint.io/user-guide/rules/shorthand-properyu-no-redundat-values) | true               | 可简写的属性不重复写，错误：margin: 1px 1px;               |
| [declaration-bang-space-after](http://stylelint.io/user-guide/rules/declaration-bang-space-after) | never              | !important中！后不空格                                     |
| [declatation-bang-space-before](http://stylelint.io/user-guide/rules/declatation-bang-space-before) | always             | !important中！前空一格                                     |
| [declatation-colon-space-after](http://stylelint.io/user-guide/rules/declatation-colon-space-after) | always             | 属性名冒号后空一格                                         |
| [declatation-colon-space-before](http://stylelint.io/user-guide/rules/declatation-colon-space-before) | never              | 属性名冒号前不空格                                         |
| [declatation-block-no-ignored-properties](http://stylelint.io/user-guide/rules/declatation-block-no-ignored-properties) | true               | 禁止那些由于在同一规则的另一个属性值忽略的属性值。         |
| [declaration-block-no-shorthand-property-overrides](http://stylelint.io/user-guide/rules/declaration-block-no-shorthand-property-overrides) | true               | 错误：border-top-width: 1px; border: 2px solid blue;       |
| [declaration-block-semicolon-newline-after](http://stylelint.io/user-guide/rules/declaration-block-semicolon-newline-after) | always-multi-line  | 一个模块要么整个模块一行显示，要么分号后分行显示           |
| [declaration-block-semicolon-space-after](http://stylelint.io/user-guide/rules/declaration-block-semicolon-space-after) | always-single-line | 属性之间分号后空一格或换行                                 |
| [declaration-block-semicolon-newline-before](http://stylelint.io/user-guide/rules/declaration-block-semicolon-newline-before) | never-multi-line   | 分号前不允许换行                                           |
| [declaration-block-semicolon-space-before](http://stylelint.io/user-guide/rules/declaration-block-semicolon-space-before） | never              | 分号前不允许空格                                           |
| [declaration-block-trailing-semicolon](http://stylelint.io/user-guide/rules/declaration-block-trailing-semicolon) | always             | 模块内最后一个属性必须有分号                               |
| [block-no-empty](http://stylelint.io/user-guide/rules/block-no-empty) | true               | 不允许模块内为空                                           |
| [block-opening-brace-space-after](http://stylelint.io/user-guide/rules/block-opening-brace-space-after) | always-single-line | 模块单行时“{”后空一格                                      |
| [block-opening-brace-space-before](http://stylelint.io/user-guide/rules/block-opening-brace-space-before) | always             | “{”前空一格                                                |
| [block-closing-brace-space-before](http://stylelint.io/user-guide/rules/block-closing-brace-space-before) | always-single-line | 模块单行时“}”前空一格                                      |
| [selector-attribute-brackets-space-inside](http://stylelint.io/user-guide/rules/selector-attribute-brackets-space-inside) | always             | “[”后空一格，“]”前空一格                                   |
| [selector-attribute-operator-space-after](http://stylelint.io/user-guide/rules/selector-attribute-operator-space-after) | always             | “[]”内的“=”后空一格                                        |
| [selector-attribute-operator-space-before](http://stylelint.io/user-guide/rules/selector-attribute-operator-space-before) | always             | “[]”内的“=”前空一格                                        |
| [selector-combinator-space-after](http://stylelint.io/user-guide/rules/selector-combinator-space-after） | always             | 选择器后空一格，例如：ul> li                               |
| [selector-combinator-space-before](http://stylelint.io/user-guide/rules/selector-combinator-space-before) | always             | 选择器前空一格，例如：ul >li                               |
| [selector-max-compound-selectors](http://stylelint.io/user-guide/rules/selector-max-compound-selectors) | 4                  | 最多4层选择器                                              |
| [selector-no-universal](http://stylelint.io/user-guide/rules/selector-no-universal) | true               | 不允许通用选择器*                                          |
| [selector-pseudo-class-case](http://stylelint.io/user-guide/rules/selector-pseudo-class-case) | lower              | 伪类选择器小写（：hover之类）                              |
| [selector-pseudo-class-no-unknown](http://stylelint.io/user-guide/rules/selector-pseudo-class-no-unknown) | true               | 不允许css规范外的伪类选择器（：hover之类）                 |
| [selector-pseudo-element-case](http://stylelint.io/user-guide/rules/selector-pseudo-element-case) | lower              | 伪类选择器小写（：: before之类）                           |
| [selector-pseudo-element-no-unknown](http://stylelint.io/user-guide/rules/selector-pseudo-element-no-unknown) | true               | 不允许css规范外的伪类选择器（：: before之类）              |
| [selector-type-case](http://stylelint.io/user-guide/rules/selector-type-case | lower              | 标签选择器小写                                             |
| [selector-max-empty-lines](http://stylelint.io/user-guide/rules/selector-max-empty-lines) | 0                  | 选择器共用一个模块，选择器之间允许0行空白                  |
| [selector-list-comma-newline-before](http://stylelint.io/user-guide/rules/selector-list-comma-newline-before) | never-multi-line   | 选择器共用一个模块，选择器之间要么一行，要么逗号后换行     |
| [selector-list-comma-space-after](http://stylelint.io/user-guide/rules/selector-list-comma-space-after) | always-single-line | 选择器共用一个模块，选择器一行时，逗号后空一格             |
| [rule-nested-empty-line-before](http://stylelint.io/user-guide/rules/rule-nested-empty-line-before) | never              | 嵌套规则前不空行                                           |
| [media-query-list-comma-newline-before](http://stylelint.io/user-guide/rules/media-query-list-comma-newline-before) | always-single-line | 媒体查询列表中不允许逗号之前换行                           |
| [media-query-list-comma-space-after](http://stylelint.io/user-guide/rules/media-query-list-comma-space-after) | always-single-line | 媒体查询列表中单行逗号之后空一格                           |
| [comment-whitespace-inside](http://stylelint.io/user-guide/rules/comment-whitespace-inside) | always             | 注释符之间空格。错误：/*\* haha \*/；正确：/\* haha \*/    |
| [max-empty-lines](http://stylelint.io/user-guide/rules/max-empty-lines) | 1                  | 内容之间最多允许1行空白行                                  |
| [max-nesting-depth](http://stylelint.io/user-guide/rules/max-nesting-depth) | 4                  | sass中允许嵌套的深度为4                                    |
| [no-descending-specificity](http://stylelint.io/user-guide/rules/no-descending-specificity) | true               | 选择的同一元素，不允许权重较轻的选择器出现在权重较重的之后 |
| [no-duplicate-selectors](http://stylelint.io/user-guide/rules/no-duplicate-selectors) | true               | 一个样式表不允许相同的选择器出现                           |
| [no-empty-source](http://stylelint.io/user-guide/rules/no-empty-source) | true               | 样式表不允许为空                                           |
| [no-eol-whitespace](http://stylelint.io/user-guide/rules/no-eol-whitespace) | true               | 样式表末尾不允许空行                                       |
| [no-extra-semicolons](http://stylelint.io/user-guide/rules/no-extra-semicolons) | true               | 不允许多余的分号                                           |
| [no-invalid-double-slash-comments](http://stylelint.io/user-guide/rules/no-invalid-double-slash-comments) | true               | 不允许//注释                                               |
| [no-unknown-animations](http://stylelint.io/user-guide/rules/no-unknown-animations) | true>              | animation的name不能是keyframes没有定义的                   |
