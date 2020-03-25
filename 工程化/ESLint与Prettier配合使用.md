## ESLint 与 Prettier配合使用

 首先肯定是需要安装 prettier ，并且你的项目中已经使用了 ESLint ，有 eslintrc.js 配置文件。

 

```
npm i -D prettier
```

#### 配合ESLint检测代码风格

安装插件：

```
npm i -D eslint-plugin-prettier
```

 

 eslint-plugin-prettier 插件会调用 prettier对你的代码风格进行检查，其原理是先使用 prettier对你的代码进行格式化，然后与格式化之前的代码进行对比，如果过出现了不一致，这个地方就会被 prettier进行标记。

接下来，我们需要在 eslintrc.js 的 rules 中添加， "prettier/prettier": "error" ，表示被 prettier 标记的地方抛出错误信息。

```

//.eslintrc.js
{
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": "error"
  }
}
```

 

借助ESLint的autofix功能，在保存代码的时候，自动将抛出error的地方进行fix。
如果你的eslint是直接通过cli方式启动的，那么只需要在后面加上fix即可，如： eslint --fix 。（eslint的fix功能会修改源文件！切记！！！）

#### 如果与已存在的插件冲突怎么办

```
npm i -D eslint-config-prettier
```

通过使用eslint-config-prettier配置，能够关闭一些不必要的或者是与prettier冲突的lint选项。这样我们就不会看到一些error同时出现两次。使用的时候需要确保，这个配置在extends的最后一项。

```
//.eslintrc.js
{
  extends: [
    'standard', //使用standard做代码规范
    "prettier",
  ],
}
这里有个文档，列出了会与prettier冲突的配置项。
```

#### 同时使用上面两项配置

如果你同时使用了上述的两种配置，那么你可以通过如下方式，简化你的配置。

```
//.eslintrc.js
{
  "extends": ["plugin:prettier/recommended"]
}
```

最后贴一下我们项目中的完整配置，是在vue-cli生成的代码基础上修改的，并且使用standard做代码规范：

```
module.exports = {
  root: true,
  parserOptions: {
    parser: 'babel-eslint'
  },
  env: {
    browser: true,
    es6: true
  },
  extends: [
    // https://github.com/standard/standard/blob/master/docs/RULES-en.md
    'standard',
    // https://github.com/vuejs/eslint-plugin-vue#priority-a-essential-error-prevention
    // consider switching to `plugin:vue/strongly-recommended` or `plugin:vue/recommended` for stricter rules.
    'plugin:vue/essential',
    "plugin:prettier/recommended",
  ],
  // required to lint *.vue files
  plugins: [
    'vue'
  ],
  // add your custom rules here
  rules: {
    "prettier/prettier": "error",
    // allow async-await
    'generator-star-spacing': 'off',
    // allow debugger during development
    'no-debugger': process.env.NODE_ENV === 'production' ? 'error' : 'off'
  }
}
```

#### 什么？你们项目没有启用ESLint

 

不要慌，没有ESLint也不要怕，可以通过[onchange](https://www.npmjs.com/package/onchange)进行代码的监听，然后自动格式化代码。只要在package.json的scripts下添加如下代码，然后使用 npm run format ，我们就能监听src目录下所有的js文件并进行格式化：

```
"scripts": {
  "format": "onchange 'src/**/*.js' -- prettier --write {{changed}}"
}
```

当你想格式化的文件不止js文件时，也可以添加多个文件列表。

```
"scripts": {
  "format": "onchange 'test/**/*.js' 'src/**/*.js' 'src/**/*.vue' -- prettier --write {{changed}}"
}
```

当然，你也能够在编辑器中配置对prettier的支持，具体支持哪些编辑器，请戳[这里](https://prettier.io/docs/en/editors.html)



参考：

[使用ESLint+Prettier来统一前端代码风格](https://juejin.im/post/5b27a326e51d45588a7dac57#heading-8)
