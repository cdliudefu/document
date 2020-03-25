npm 从5.2版开始，增加了 npx 命令。它有很多用处，本文介绍该命令的主要使用场景。

Node 自带 npm 模块，所以可以直接使用 npx 命令。万一不能用，就要手动安装一下。

```
npm install -g npx
```

#### npx到底能干什么？

我们知道一般的node项目会用到各种工具和脚手架，我们通常使用两种命令安装，`npm install -g xxx`（全局安装）和`npm install -d xxx`（本地安装）。

### 全局安装劣势：

- 占用本机空间
   npm会在机器上创建一个目录（mac是/usr/local/lib/node_modules）存放所有global安装的包, 其实node_module占用的空间比较大的
- 版本问题：
   假如一个项目中的某一个依赖是全局安装的，也就意味着不同的开发人员使用的这个依赖版本完全基于本地的版本，也就会导致不同的开发人员使用不同的版本（例如：是不是你和同事本地的node、eslint、webpack等工具版本会不一样？你是不是很苦恼？）

npx 的出现就是帮你解决全局安装依赖和版本问题的。

当在执行`npx <command>`的时候，npx会做什么事情？

1. 帮你在本地（可以是当前项目中的也可以是本机全局）寻找这个 `command`

- 找到了： 就用本地的版本
- 没找到： 直接下载最新版本，完成命令要求

1. 使用完之后不会在你的本机或者项目留下任何东西

npx 优势也就很明显了：

- 不会污染本机
- 永远使用最新版本的依赖

## npx使用介绍

### 调用项目安装的模块

npx 调用项目内部安装的模块。比如，之前使用npm在项目内部安装了测试工具 Mocha。

```
npm install -d mocha
```

调用 Mocha ，只能在项目脚本和 `package.json` 的`scripts`字段里面， 如果想在命令行下调用，必须像下面这样。

```
# 项目的根目录下执行
node-modules/.bin/mocha --version
```

npx 就是想解决这个问题，让项目内部安装的模块用起来更方便，只要像下面这样调用就行了。

```
npx mocha --version
```

npx 的原理很简单，就是运行的时候，会到`node_modules/.bin`路径和环境变量`$PATH`里面，检查命令是否存在。如果依然找不到，就会帮你安装。（只会在你执行命令所在目录的依赖文件里和本机全局去找，不会去执行当前命令目录的‘隔壁邻居’项目去找！！！）

由于 npx 会检查环境变量`$PATH`，所以系统命令也可以调用。

```
# 等同于 ls
npx ls
```

注意，`Bash` 内置的命令不在`$PATH`里面，所以不能用。比如，`cd`是 `Bash` 命令，因此就不能用`npx cd`。

### 避免全局安装模块

除了调用项目内部模块，`npx` 还能避免全局安装的模块。比如，`create-react-app`这个模块是全局安装，`npx` 可以运行它，而且不进行全局安装。

```
npx create-react-app my-react-app
```

上面代码运行时，如果项目依赖和本机全局都没有安装脚手架的话，`npx` 将`create-react-app`下载到一个临时目录（cache目录），使用以后再删除。所以，以后再次执行上面的命令，都会重新下载`create-react-app`，执行完命令都会删除`create-react-app`。

下载全局模块时，npx 允许指定版本。

```
npx uglify-js@3.1.0 main.js -o ./dist/main.js
```

上面代码指定使用 3.1.0 版本的`uglify-js`压缩脚本。

注意，只要 `npx` 后面的模块无法在本地发现，就会下载同名模块。比如，本地没有安装`http-server`模块，下面的命令会自动下载该模块，在当前目录启动一个 Web 服务。

```
npx http-server
```

### --no-install 参数和--ignore-existing 参数

如果想让 npx 强制使用本地模块，不下载远程模块，可以使用`--no-install`参数。如果本地不存在该模块，就会报错。

```
npx --no-install http-server
```

反过来，如果忽略本地的同名模块，强制安装使用远程模块，可以使用`--ignore-existing`参数。比如，本地已经全局安装了create-react-app，但还是想使用远程模块，就用这个参数。

```
npx --ignore-existing create-react-app my-react-app
```

### 使用不同版本的 node

利用 npx 可以下载模块这个特点，可以指定某个版本的 Node 运行脚本。它的窍门就是使用 npm 的 [node 模块](https://www.npmjs.com/package/node)。

```
npx node@0.12.8 -v
v0.12.8
```

上面命令会使用 0.12.8 版本的 Node 执行脚本。原理是从 npm 下载这个版本的 node，使用后再删掉。

某些场景下，这个方法用来切换 Node 版本，要比 nvm 那样的版本管理器方便一些。

### -p 参数

npx的-p选项允许您指定要安装的包，并将其添加到正在运行的$PATH中。

`-p`参数用于指定 npx 所要安装的模块，所以上一节的命令可以写成下面这样。

```
npx -p node@0.12.8 node -v
v0.12.8
```

上面命令先指定安装`node@0.12.8`，然后再执行`node -v`命令。

`-p`参数对于需要安装多个模块的场景很有用。

```
npx -p lolcatjs -p cowsay [command]
```

使用`npx -p node@6 npm run test` :

- npx会帮助你下载`node@6`
- 将此时的环境变成`node@6`版本
- 使用`node@6`帮你执行`npm run test`
- 命令执行完毕之后不会修改你原来的node版本

指令可以理解为使用`node@6`版本运行`npm run test`

### -c 参数

如果 npx 安装多个模块，默认情况下，所执行的命令之中，只有第一个可执行项会使用 npx 安装的模块，后面的可执行项还是会交给 Shell 解释。

```
npx -p lolcatjs -p cowsay 'cowsay hello | lolcatjs'
# 报错
```

上面代码中，`cowsay hello | lolcatjs`执行时会报错，原因是第一项`cowsay`由 npx 解释，而第二项命令`localcatjs`由 `Shell `解释，但是`lolcatjs`并没有全局安装，所以报错。

`-c`参数可以将所有命令都用 npx 解释。有了它，下面代码就可以正常执行了。

```
npx -p lolcatjs -p cowsay -c 'cowsay hello | lolcatjs'
```

`-c`参数的另一个作用，是将环境变量带入所要执行的命令。举例来说，npm 提供当前项目的一些环境变量，可以用下面的命令查看。

```
npm run env | grep npm_
```

`-c`参数可以把这些 npm 的环境变量带入 npx 命令。

```
npx -c 'echo "$npm_package_name"'
```

上面代码会输出当前项目的项目名。

例子：

像之前我们使用yo创建webapp项目的时候，分三步安装的：

先安装yo

```
npm install yo  -g
```

再安装 generator

```
npm install -g generator-webapp
```

最后再创建webapp项目

```
yo webapp
```

那么我们现在用npx就可以一条命令安装了，这样本机全局既不会有yo和generator的包，还省去了两次命令操作，是不是非常简单了呀，命令如下：

```
npx -p yo -p generator-webapp -c yo webapp
```

> - `-p` 后面跟着需要安装的模块，可以连续安装多个
> - `-c` 后面跟着需要执行的命令

### 执行 GitHub 源码

npx 还可以执行 GitHub 上面的模块源码。

```
# 执行 Gist 代码
npx https://gist.github.com/zkat/4bc19503fe9e9309e2bfaa2c58074d32

# 执行仓库代码
npx github:piuccio/cowsay hello
```

注意，远程代码必须是一个模块，即必须包含`package.json`和入口脚本。

参考：
 [npx 使用教程](http://www.ruanyifeng.com/blog/2019/02/npx.html)
