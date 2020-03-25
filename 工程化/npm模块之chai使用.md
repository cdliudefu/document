**chai**这是一款用于节点和浏览器的BDD / TDD断言库，可以与任何javascript测试框架完美地配对。

[更多使用文档请点击访问chai工具官网](https://www.npmjs.com/package/chai)。

## 安装

一键安装不多解释

```
npm install --save-dev chai
```

## 使用

**浏览器**
 您也可以在浏览器中使用它。通过npm安装并使用chai.js下载中找到的文件。例如：

```
<script src="./node_modules/chai/chai.js"></script>
```

导入库中的代码，然后挑选的款式之一，你想使用-要么`assert`，`expect`或`should`：

```
var chai = require('chai');  
var assert = chai.assert;    // 使用断言样式
var expect = chai.expect;    // 使用Expect样式
var should = chai.should();  // 使用Should样式
```

本机模块用法（在全球注册chai测试样式）

```
require('chai/register-assert');  // 使用断言样式
require('chai/register-expect');  // 使用Expect样式
require('chai/register-should');  // 使用Should样式
```

本地模块的用法（作为局部变量）

```
const { assert } = require('chai');  // 使用断言样式
const { expect } = require('chai');  //使用Expect样式
const { should } = require('chai');  // 使用Should样式
should();  // 修改Object.prototype

const { expect, use } = require('chai');  // 创建局部变量`expect`和`use`; 对于插件使用很有用
```

本机模块用法（全局注册chai测试样式）

```
import 'chai/register-assert';  // Using Assert style
import 'chai/register-expect';  // Using Expect style
import 'chai/register-should';  // Using Should style
```

本机模块用法（仅本地导入）

```
import { assert } from 'chai';  // 使用断言样式
import { expect } from 'chai';  // 使用expect风格
import { should } from 'chai';  // 使用Should样式
should();  // 修改Object.prototype 
```

与摩卡搭配使用

```
mocha spec.js -r chai/register-assert  # 使用断言样式 
mocha spec.js -r chai/register-expect  # 使用Expect样式 
mocha spec.js -r chai/register-should  # 使用Should样式
```

[更多文档中阅读有关这些样式的更多信息 ](http://chaijs.com/guide/styles/)。

**外挂程式**
 Chai提供了健壮的插件架构，用于扩展Chai的断言和接口。

- 需要插件吗？查看[官方插件列表](http://chaijs.com/plugins)。
- 要构建插件吗？阅读插件9api文档](<http://chaijs.com/guide/plugins/>)。
- 有一个插件并想要列出吗？只需将以下关键字添加到package.json中： 
  - `chai-plugin`
  - `browser` 如果您的插件可以在浏览器以及Node.js中运行
  - `browser-only` 如果您的插件不适用于Node.js
