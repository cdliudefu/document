## 1. 盒模型 ##
- html文档中的每个元素都被描绘成矩形盒子，这些矩形盒子通过一个模型来描述其占用的空间，这个模型称盒模型，盒模型通过四个边界来描述：margin(外边距)、border(边框)、padding(内边距)、content(内容区域)
- css的盒模型是css的基础，同时也是难点，盒模型分为IE盒模型和W3C标准盒模型
- IE盒模型和W3C标准模型的区别是：

		1、W3C标准盒模型：
		属性width,height只包含内容content,不包含border和padding;
		2、IE盒模型：
		属性width,height包含border和padding，即是content+padding+border;

- 在ie8+浏览器中使用哪个盒模型可以由box-sizing(css新增的属性)控制，默认值为**content-box**,即标准盒模型；如果将box-sizing设置为**border-box**则用的是IE盒模型。
- 在ie6,7,8中DOCTYPE缺失会触发IE模式，在当前W3C标准中盒模型是可以通过box-sizing自由的进行切换的。

		box-sizing属性值：
		box-sizing:content-box;/* 标准模型 */
		box-sizing:border-box; /*IE模型*/

		值为：content-box(标准盒模型值)
		width=内容的宽度
		height=内容的高度
		值为：border-box(IE盒模型)
		width=border+padding+内容的宽度
		height=border+padding+内容的高度

- css的盒模型由content(内容),padding(内边距),border(边框),margin(外边距)组成。但盒子的大小由content+padding+border这几部分决定,把margin算进去的那是盒子占据的位置，而不是盒子的大小。
- 在编写页面代码时尽量使用标准的W3C模型（需在页面中声明DOCTYPE类型），这样可以避免多个浏览器对同一页面的不兼容。若不声明DOCTYPE类型，IE浏览器会将盒子模型解释为IE盒子模型，FireFox等会将其解释为W3C盒子模型，或声明了，所有的浏览器都会把盒模型解析为W3C盒模型。

## 2.css优先级确定 ##

- 每个选择器都有权值，权值越大越优先
- 继承的样式优先级低于自身指定样式
- ！important优先级最高 js也无法修改
- 权值相同时，靠近元素的样式优先级高 顺序为内联样式表（标签内部）> 内部样式表（当前文件中）> 外部样式表（外部文件中）

## 3.导入css方式中link和@import区别 ##
- link是XHTML标签，除了加载CSS外，还可以定义RSS等其他事务，@import属于CSS范畴，只能加载CSS
- link引用CSS时，在页面载入时同时加载，@import需要页面网页完全载入以后加载
- link无兼容问题，@import在CSS2.1提出，低版本的浏览器不支持
- link支持使用JavaScript控制DOM去改变样式，而@import不支持
