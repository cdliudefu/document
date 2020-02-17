## 1. html语义化 ##
<ul>
<li>根据内容的结构化（内容语义化），选择合适的标签（代码语义化）便于开发者阅读和写出更优雅的代码的同时让浏览器的爬虫和机器很好的解析。
</li>
<li>
需要注意的是：
<p>尽可能少的使用无语义的标签；</p>
<p>在语义不明显时，既可以使用div或p，尽量用P，因为P在默认情况下有上下间距，对兼容特殊终端有利；</p>
<p>不要使用纯样式标签，如b、font、u等，改用css设置；D.需要强调的文本，可以包含在strong或em标签中，能用样式就不用他们；</p>
<p>使用表格时，标题要用caption，表头用thead,主体部分用tbody包围,尾部用tfoot包围</p>
<p>表单域要用fieldset标签包起来，并且legend标签说明表单的用途</p>
<p>每个input标签对应的说明文本都需要使用label标签，并且通过为input设置id与label相对应关联起来</p>
</li>
</ul>
## 2. meta viewport解释 ##

		<!DOCTYPE html>  H5标准声明，使用 HTML5 doctype，不区分大小写
		<head lang=”en”> 标准的 lang 属性写法
		<meta charset=’utf-8′>声明文档使用的字符编码
		<meta http-equiv=”X-UA-Compatible” content=”IE=edge,chrome=1″/>   优先使用 IE 最新版本和 Chrome
		<meta name=”description” content=”不超过150个字符”/>   页面描述
		<meta name=”keywords” content=””/>  页面关键词
		<meta name=”author” content=”name, email@gmail.com”/>网页作者
		<meta name=”robots” content=”index,follow”/>  搜索引擎抓取
		<meta name=”viewport” content=”initial-scale=1, maximum-scale=3, minimum-scale=1, user-scalable=no”> 为移动设备添加 viewport
		<meta name=”apple-mobile-web-app-title” content=”标题”> iOS 设备 begin
		<meta name=”apple-mobile-web-app-capable” content=”yes”/>  添加到主屏后的标题（iOS 6 新增）
		是否启用 WebApp 全屏模式，删除苹果默认的工具栏和菜单栏
		<meta name=”apple-itunes-app” content=”app-id=myAppStoreID, affiliate-data=myAffiliateData, app-argument=myURL”>
		添加智能 App 广告条 Smart App Banner（iOS 6+ Safari）
		<meta name=”apple-mobile-web-app-status-bar-style” content=”black”/>
		<meta name=”format-detection” content=”telphone=no, email=no”/>  设置苹果工具栏颜色
		<meta name=”renderer” content=”webkit”>  启用360浏览器的极速模式(webkit)
		<meta http-equiv=”X-UA-Compatible” content=”IE=edge”> 避免IE使用兼容模式
		<meta http-equiv=”Cache-Control” content=”no-siteapp” />不让百度转码
		<meta name=”HandheldFriendly” content=”true”> 针对手持设备优化，主要是针对一些老的不识别viewport的浏览器，比如黑莓
		<meta name=”MobileOptimized” content=”320″>   微软的老式浏览器
		<meta name=”screen-orientation” content=”portrait”>   uc强制竖屏
		<meta name=”x5-orientation” content=”portrait”>QQ强制竖屏
		<meta name=”full-screen” content=”yes”>  UC强制全屏
		<meta name=”x5-fullscreen” content=”true”>   QQ强制全屏
		<meta name=”browsermode” content=”application”>   UC应用模式
		<meta name=”x5-page-mode” content=”app”>QQ应用模式
		<meta name=”msapplication-tap-highlight” content=”no”>windows phone 点击无高光
		设置页面不缓存
		<meta http-equiv=”pragma” content=”no-cache”>
		<meta http-equiv=”cache-control” content=”no-cache”>
		<meta http-equiv=”expires” content=”0″>
## 3. canvas 相关 ##
使用前需要获得上下文环境，暂不支持3d，常用api:

    1.fillRect(x,y,width,height)实心矩形
    2.strokeRect(x,y,width,height)空心矩形
    3.fillText("Hello world",200,200);实心文字
    4.strokeText("Hello world",200,300)空心文字
4. 
