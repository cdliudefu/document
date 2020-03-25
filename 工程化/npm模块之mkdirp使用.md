**mkdirp**这是一款在node.js中像`mkdir -p`一样递归创建目录及其子目录。

[更多使用文档请点击访问mkdirp工具官网](https://www.npmjs.com/package/mkdirp)。

## 安装

一键安装不多解释

```
npm install -g mkdirp
```

## 使用

例子：

```
var mkdirp = require('mkdirp');
    
mkdirp('/tmp/foo/bar/baz', function (err) {
    if (err) console.error(err)
    else console.log('pow!')
});
```

输出:

```
pow!
```

现在 `/tmp/foo/bar/baz` 目录就会出现了，是不是很简单。

**API**

```
mkdirp(dir, opts, cb)
```

使用八进制权限字符串`opts.mode`在`dir`处创建新目录和任何必要的子目录。如果`opts`是非对象，它将被视为`opts.mode`。

如果未指定`opts.mode`，则默认为`0777 & (~process.umask())`。

`cb(err,made)`引发错误，或者是必须创建的第一个`made`目录（如果有的话）。

您可以选择通过传入`opts.fs`传入备用fs实现。您的实现应该有`opts.fs.mkdir(path,mode,cb)`和`opts.fs.stat(path,cb)`。

```
mkdirp.sync(dir, opts)
```

使用八进制权限字符串`opts.mode`在`dir`同步创建新目录和任何必要的子目录。如果`opts`是非对象，它将被视为`opts.mode`。

如果未指定`opts.mode`，则默认为`0777 & (~process.umask())`。

返回必须创建的第一个目录（如果有）。

您可以选择通过传入`opts.fs`传入备用fs实现。您的实现应该有`opts.fs.mkdirSync(path,mode)`和`opts.fs.statSync(path)`。

这个包还附带了mkdirp命令。

```
usage: mkdirp [DIR1,DIR2..] {OPTIONS}

  Create each supplied directory including any necessary parent directories that
  don't yet exist.
  
  If the directory already exists, do nothing.

OPTIONS are:

  -m, --mode   If a directory needs to be created, set the mode as an octal
               permission string.
```
