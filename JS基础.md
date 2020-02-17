## 1. 获得元素宽高的方式： ##
- **dom.style.width/height**：只能获取到dom元素内联样式所设置的宽高，也就是说如果该节点的样式是在style标签中或外联的css文件中设置的话，这种方法是获取不到的。
- **dom.currentStyle.width/height**：获取的是在页面渲染完成后的结果，就是说不管是哪种方式设置的样式，都能获取到，单这种方式只有**IE浏览器支持**
- **window.getComputedStyle(dom).width/height**：这种方式兼容更多的浏览器，通用性好些
- **dom.getBoundingClientRect().width/height**：这个方式是根据元素在视窗中的绝对位置来获取宽高的
- **dom.offsetWidth/offsetHeight**：当前元素的实际宽高，兼容性最好。

## 2.拓展获得宽高的方式 ##

- 获取屏幕的宽高（屏幕分辨率）：window.screen.width/height;
- 获取屏幕工作区域的宽高（去掉状态栏）：window.screen.availWidth/availHeight;
- 网页全文的宽高（有内容和无内容）：document.body.scrollWidth/scrollHeight;
- 滚动条卷上去的高度和宽度：
		
		IE:document.body.scrollTop/scrollLeft;
		谷歌：document.documentElement.scrollTop/scrollLeft;

- 网页可见区域的高度和宽度（不加边线）：document.body.clientHeight/clientWidth;
- 网页可见区域的高度和宽度（加边线）：document.body.offsetWidth/offsetHeight;

## 3.数组去重 ##
- 思路1：定义一个新数组，并存放原数组的第一个元素，然后源数组一一和新数组的元素对比，若不同则存放在新数组中；
- 思路2：先将原数组排序，在与相邻的进行比较，如果不同则存入新数组；
- 思路3：利用对象属性存在的特性，如果没有该属性则存入新数组
- 思路4：利用ES6的Set对象去重

	    let arr= [1, 2, 3, 3, 5, 7, 2, 6, 8];
	    console.log([...new Set(arr)]);
	    //数组合并去重排序
	    let arr1 = [1, 25, 2, 26, 1234, 6, 213];
	    let arr2 = [2, 6, 2134, 6, 31, 623];
	    let c = [...new Set([...arr1, ...arr2])].sort((a, b) => {
	    	return a - b;
	    });

## 4.JavaScript的this原理： ##
JavaScript是一个文本作用域的语言，就是说，一个变量的作用域在写这个变量的时候确定，this关键字是为了在JS中加入动态作用域而做的努力，所谓动态作用域就是说变量的作用范围，是根据函数调用的位置而定的，从这个角度来理解this就简单了，this是JS中的动态作用域机制，具体来说有四种，优先级有低到高分别是：

- 默认的this绑定，就是说在一个函数中使用this，但是没有为this绑定对象，这种情况下，非严格默认，this就是全局变量，Node环境中的global，浏览器环境中的window。
- 隐式绑定：使用obj.foo()这样的语法来调用函数的时候，函数foo中的this绑定到obj对象
- 显示绑定：foo.call(obj,...),foo.apply(obj,[...]).foo.bind(obj,...);
- 构造绑定：new foo()这种情况，无论foo是否做了绑定，都要创建一个新的对象，然后foo中的this引用这个对象。--《you don't know js》

## 5.node调试工具 ##
    const koa = require('koa');
    const router = require('koa-route');
    const app = new koa();
    const main = ctx=>{
    ctx.response.body='hello world';
    }
    const welcome = (ctx,name)=>{
    ctx.response.body='hello '+name;
    }
    app.use(router.get('/',main));
    app.use(route.get('/:name',welcome));
    app.listen(3000);
    console.log('listening on port 3000');
    
    //启动脚本
    node --inspect app.js
    // 访问浏览器
    http://127.0.0.1:3000
    // 打开调试工具
     A:在chrome浏览器中输入
      chrome://inspect或about:inspect
     B:开发工具
      直接F12 打开开发工具，点击node绿色标识
    
     - 调试非服务脚本
      node --inspect-brk=9229 app.js
      // --inspect-brk指定第一行就设置断点
      - 忘记写--inspect怎么办
      node app.js
      //启动另一个窗口查找上面脚本的进程号
       ps ax | grep app.js 
       // app.js的进程号是30464
       node -e 'process._debugProcess(30464)'

## 6、Lodash模块 ##

lodash是一个一致性、模块化、高性能的 JavaScript 实用工具库。它通过降低array、number、objects、string等等的使用难度从而让javascript变得更简单，其模块化非常用于于:

-  遍历array、object和string
-  对值进行操作和检测
-  创建符合功能的函数
