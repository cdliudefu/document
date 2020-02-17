## console简述  ##
js原生中默认没有console对象，这是宿主对象（也就是浏览器）提供的内置对象，用于访问调试控制台，在不同的浏览器里效果可能不同。

  现在大部分的浏览器都是带有调试功能的，而低版本IE没有，所以console对象不存在。
## console的方法 ##
#### 1. console.log() / console.debug() / console.info() 

<p>
	console.log方法用于在控制台输出信息。它可以接受多个参数，逗号分隔。它会自动在每次输出的结尾，添加换行符。没有返回值回会返回undefined。
</p>
<p>
	如果第一个参数是格式字符串（使用了格式占位符），console.log方法将依次用后面的参数替换占位符，然后在进行输出。
</p>
<p>
	console.log方法和console.debug、console.info几乎用户完全一样，唯一不同的表现形式。
	IE不支持debug方法
</p>
    console.log('文字信息');
    console.info('提示信息');
    console.warn('警告信息');
    console.error('错误信息');
    
    占位符
    模式	    类型
    %s	      字符串
    %d,%i	  整数
    %f	      浮点数
    %o	      对象超链接
    %c	      CSS格式化样式

#### 2. console.assert() 
  接受至少两个参数，第一个参数的值或返回值为false的时候，将会在控制台上抛出一个异常并将其余参数作为异常描述输出。<br/>
	当第一个参数或返回值为真时，不输出内容<br/>
	当第一个参数或返回值为假时，输出后面的内容并抛出异常

    console.assert(false,123) //抛出错误，并且输出，返回undefined
    console.assert(true,123) //没有错误，返回undefined
    console.assert(true, "你永远看不见我");
    console.assert((function() { return true;})(), "你永远看不见我");
     
    console.assert(false, "你看得见我");
    console.assert((function() { return false;})(), "你看得见我");
#### 3. console.count() 
用于计数，输出它被调用了多少次。方法里面可以传入一个字符串作为参数，作为标签对执行次数进行分类

    (function() {
     for (var i = 0; i < 5; i++) { 
    console.count('count'); 
     }
    })();
    // 分类统计
    function greet(user) { 
      console.count(user); 
      return "hi " + user;
    }
    greet('bob')
    greet('alice')
    greet('bob')
#### 4.console.clear()
清空控制台内容
#### 5.console.dir() 
用来对一个对象进行检查，并以易于阅读和打印的格式显示
	var obj = {
	name: 'c',
	age: '20',
	type: '1'
	  };
	  console.dir(obj);
	 
	  var arr = [1,2,3]
	  console.dir(arr)
	 
	  var s = 'sdfs'
	  console.dir(s)
	 
	  var n = '123'
	  console.dir(n)
#### 6.console.error(),console.warn() 
两个方法用于输出错误和警告信息，一个黄色警告，一个是红色的错误形式.

	console.error('error红色错误');
	console.warn('warn黄色警告');
#### 7.console.table() 
可以将传入的对象或数组这些复合数据以表格形式输出。

    var arr= [ 
     { num: "1"},
     { num: "2"}, 
     { num: "3" }
    ];
    console.table(arr);
    
    var obj= {
    a:{ num: "1"},
    b:{ num: "2"},
    c:{ num: "3" }
    };
    console.table(obj);
#### 8.console.time(),console.timeEnd() 
这两个方法计算一个操作的执行的时间，console.time()是开始，console.timeEnd()是结束，可以传一个参数，参数为计时器的名称

	console.time('计时器1');
	for (var i = 0; i < 100; i++) {
	for (var j = 0; j < 100; j++) {}
	   }
	console.timeEnd('计时器1');
	console.time('计时器2');
	for (var i = 0; i < 1000; i++) {
	for (var j = 0; j < 1000; j++) {}
	   }
	console.timeEnd('计时器2');
#### 9.console.group(),console.groupCollapsed(),console.groupend() 
console.group()和console.groupCollapsed()方法用于将显示的信息分组，可以把信息进行折叠和展开，他们都可以传递一个参数，参数默认是分组名，用法都是一样的，唯一区别就是group是默认展开的，groupCollapsed，默认是收起的。

	console.group('第一层');
	console.group('第二层');
	
	console.log('error');
	console.error('error');
	console.warn('error');
	
	console.groupEnd(); 
	console.groupEnd();
#### 10.console.profile(),console.profileEnd() 
这两个方法就是比较高级点，用来新建一个性能测试器，可以评估某段代码的性能，可以传一个参数，为生产的性能测试器的名字。

	function profile() { 
		for (var i = 0; i < 10000; i++) { 
			i++;
		}
	}
	console.profile('性能分析');
	profile();
	console.profileEnd();
#### 11.console.trace() 
该方法用来追踪函数的调用过程，在复杂的架构中可以查找到对应的调用路径。

	function d(a) { 
	    console.trace();
	    return a;
	}
	function b(a) { 
	    return c(a);
	}
	function c(a) { 
	    return d(a);
	 }
	var a = b('123');

