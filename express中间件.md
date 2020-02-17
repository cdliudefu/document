## cookie-parser
用于解析cookie的中间件，添加中间件后，req具备cookies属性，通过req.cookies.xxx可以访问cookie的值
语法：app.use(cookieParser(secret,options))
secret是可以参数，用于对cookie进行签名，通过它可以判断客户端是否修改了cookie,这是处于安全考虑，参数是任意字符串
options:可选参数，是一个JSON对象，可以包括path,expires,maxAge,domain,secure,httpOnly
```js
$ npm install cookie-parser
var express = require('express')
var cookieParser = require('cookie-parser')
var app = express()
app.use(cookieParser())
app.get('/',function(req,res){
console.log('cookies',req.cookies)
})
app.listen(8080)
```
## express-session
session运行在服务器端，当客户第一次访问服务器时，可以将客户的登陆信息保存，当客户访问其他页面时，可以判断客户的登陆状态，做出提示，相当于登陆拦截，session可以和redis或者数据库等结合做持久化操作，当服务器挂掉时也不会导致某些客户信息（购物车）丢失。
当浏览器访问服务器并发送第一次请求时，服务器端会创建一个session对象，生产一额类似于key,value的键值对，然后key作为cookie返回到浏览器端，浏览器下次再访问时，携带key(cookie)找到对应的session(value)，客户的信息都保存在session中。
```js
$ npm i express-session
var express = require('express')
var session = require('express-session')
var app = express()
app.use(session(options))
app.listen(8080)
```
options常用的选择项如下：
- name：默认的connect.sid,可自定义
- store：session存储器实例
- secret：用于对cookie进行签名，通过它可以判断出客户是否修改了cookie，这是处于安全考虑，参数是任意字符串
- cookie：对session cookie的设置，默认值{path:'/',httpOnly:true,secure:false,maxAge:null}
- genid：是个函数，调用它来生成一个新的会话ID，默认是使用UID2库
- rolling：强制对每个相应的cookie，重置到期日期，默认false
- resave：每一次都重新保存，即使没修改过，默认true
- proxy：true/false，是否支持trust proxy，需要设置app.enable('trust proxy')一般无需设置
常用方法：
- session.destroy()：删除session，当检测到客户端关闭时调用
- session.reload()：当session有修改时，刷新session
- session.regenerate()：将已有session初始化
- session.save()：保存session
```js
var express = require('express')
var cookieParser = require('cookie-parser')
var session = require('express-session')

app.use(cookieParser('sessiontest'))
app.use(session({
secret:'sessiontest',//与cookieparser中设置一致
resave:true,
saveuninitialized:true
}))
app.listen(8080)
```
```js
//第一次请求时保存用户信息
router.get('/',function(req,res,next){
var user = {
name:'andy',
age:22
address:'cd'
}
req.session.user = user;
res.render('index',{
title:'test session',
name:'sessiontest'
})
})
```
```js
//访问时判断用户是否登陆
router.get('/',function(req,res,next){
	if(req.session.user){
	 var user = req.session.user
	 var name = user.name;
	 res.send('hello'+name)
	}else{
	res.send('还没有登陆')
	}
})
```
## serve-favicon
设置网站的favicon图标
$ npm i serve-favicon
```js
var express = require('express')
var favicon = require('serve-favicon')
var app = express()
app.use(favicon(path.join(__dirname,'public','favicon.ico')))
app.listen(8080)
```
## body-parser
bodyParser用于解析客户端请求的body中的内容，内部使用JSON编码处理，URL编码处理以及对文件上传的处理
$ npm i body-parser
```js
var express = require('express')
var bodyParser = require('body-parser')
var app = express()
//parse application/x-www-form-urlencoded
app.use(bodyParser.urlencoded({extended:false}))
//parse application/json
app.use(bodyParser.json())
app.use(function(req,res){
res.setHeader('Content-Type','text/plain')
res.write('you are right')
res.end(JSON.stringify(req.body,null,2))
})
```
以上是底层中间件用法：这将拦截和解析所有的请求，这种用法是全局的，use方法调用body-parser实例，且use方法没有设置路由路径，这样的body-parser实例就会对该APP所有的请求进行拦截和解析
2、 特定路由下的中间件用法：这种用法是针对特定路由下的特定请求的，只有请求该路由时，中间件才会拦截和解析该请求；也即这种用法是局部的；也是最常用的一个方式
```js
var express = require('express')
var bodyParser = require('body-parser')
var app = express()
//parse application/json
var jsonParser = bodyParser.json()
//parse application/x-www-urlencoded
var urlencodedparser = bodyParser.urlencoded({extended:false})

// POST /login gets urlencoded bodies
app.post('/login', urlencodedParser, function (req, res) {
 if (!req.body) return res.sendStatus(400)
 res.send('welcome, ' + req.body.username)
})
// POST /api/users gets JSON bodies
app.post('/api/users', jsonParser, function (req, res) {
 if (!req.body) return res.sendStatus(400)
 // create user in req.body
})
```
express的post（或者get）方法调用body-parser实例；且该方法有设置路由路径；这样的body-parser实例就会对该post（或者get）的请求进行拦截和解析
3、设置Content-Type 属性；用于修改和设定中间件解析的body内容类型
```js
// parse various different custom JSON types as JSON
app.use(bodyParser.json({ type: 'application/*+json' });
 
// parse some custom thing into a Buffer
app.use(bodyParser.raw({ type: 'application/vnd.custom-type' }));
 
// parse an HTML body into a string
app.use(bodyParser.text({ type: 'text/html' }));
```
## morgan
morgan是一个node.js关于http请求的express默认的日志中间件
$ npm i morgan
```js
var express = require('express');
var app = express();
var morgan = require('morgan');
app.use(morgan('short'))
app.use(function(req, res, next){
    res.send('ok');
});

app.listen(3000);
```
morgan支持stream配置项，可以通过它来实现将日志落地的效果，
```js
var express = require('express');
var app = express();
var morgan = require('morgan');
var fs = require('fs');
var path = require('path');

var accessLogStream = fs.createWriteStream(path.join(__dirname, 'access.log'), {flags: 'a'});

app.use(morgan('short', {stream: accessLogStream}));
app.use(function(req, res, next){
    res.send('ok');
});

app.listen(3000);
```
morgan的API非常少，使用频率最高的就是morgan()，作用是返回一个express日志中间件
语法：morgan(format, options)
- format：可选，morgan与定义了几种日志格式，每种格式都有对应的名称，比如combined、short等，默认是default
- options：可选，配置项，包含stream（常用）、skip、immediate
- stream：日志的输出流配置，默认是process.stdout
- skip：是否跳过日志记录
- immediate：布尔值，默认是false。当为true时，一收到请求，就记录日志；如果为false，则在请求返回后，再记录日志

## HTTPS 
通过express搭建HTTPS加密服务器
```js
var fs = require('fs')
var options = {
key:fs.readFileSync('E://ssl/myserver.key'),
cert:fs.readFileSync('E://ssl/myserver.crt'),
passphrase:'1234'
}
var https = require('https')
var express = require('express')
var app = express()
app.get('/',function(req,res){
res.send('hello world')
})
var server = https.createServer(options,app)
server.listen(8084)
console.log('server is running on port 8084')
```
## 数据库
express应用添加连接数据库的能力，只需要加载相应的数据库的node.js驱动即可，
### MySQL
$ npm install mysql
```js
var mysql = require('mysql')
var connection = mysql.createConnection({
host:'localhost',
user:'dbuser',
password:'dbpassword'
})
connection.connect()
connection.query('select 1+1 as solution',function(err,rows,fileds){
if(err){throw err}
console.log(rows[0])
})
connection.end()
```
### mongoDB
$ npm i mongoskin
```js
var db = require('mongoskin').db('localhost:27017/animals');
db.collection('mamals').find().toArray(function(err, result) {
  if (err) throw err;
  console.log(result);
});
```
## 上传文件
网页插入上传文件的表单
```html
<form action="/pictures/upload" method="POST" enctype="multipart/form-data">
  Select an image to upload:
  <input type="file" name="image">
  <input type="submit" value="Upload Image">
</form>
```
服务器脚本建立指向/upload目录的路由。这时可以安装multer模块，它提供了上传文件的许多功能
```js
var express = require('express');
var router = express.Router();
var multer = require('multer');
var uploading = multer({
  dest: __dirname + '../public/uploads/',
  // 设定限制，每次最多上传1个文件，文件大小不超过1MB
  limits: {fileSize: 1000000, files:1},
})
router.post('/upload', uploading, function(req, res) {})
module.exports = router
```
