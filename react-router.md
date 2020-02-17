## 1、React-router和React-router-dom: ##
	- React-router提供了一些router的核心API，包括Router、Route、Switch等，但是它没有提供dom操作进行跳转的api
	- React-router-dom提供了BrowserRouter、Route、Link等API，我们可以通过dom的事件控制路由，例如点击一个按钮进行跳转，大多情况下我们是这种情况，所以在开发过程中，我们更多使用React-router-dom。
## 2、React-router-dom的核心用法 ##
 - HashRouter和BrowserRouter：它们两个是路由的基本，需要将它们包裹在最外层，我们只要选择其一就可以了
 - HashRouter：曾使用过的react-router2，经常发现url中会有个#，例如localhost：3000/#；HashRouter就会出现这种情况，它是通过hash值来对路由进行控制：
 ```
import {HashRouter,Route} from 'react-router-dom'
render(
	<HashRouter>
		<Route path="/" component={Index}>Index</Route>
	</HashRouter>	
) 
 ```
 - BrowserRouter:在某种情况，我们不需要这个#，因为它看起来很怪，这时我们就需要用到BrowserRouter;它的原理是使用HTML5 history API (pushState, replaceState, popState)来使你的内容随着url动态改变的：
 ```
import {BrowserRouter,Route} from 'react-router-dom'
render(
	<BrowserRouter basename="/calendar">
		<Route path="/" component={Index}>Index</Route>
	</BrowserRouter>	
) 
 ```
 basename,如果你的文件放在服务器的二级目录下则可以使用它，当你的主页前面是有一级目录calendar时，统一会显示主页的内容.
 - Route
Route:是路由的一个原材料，它是控制路径对应显示的组件。我们经常用的是exact、path以及component属性。
exact控制匹配到/路径时不会再继续向下匹配，path标识路由的路径，component表示路径对应显示的组件。后面我们将结合NavLink完成一个很基本的路由使用。同时我们可以设置例如/second/:id的方式来控制页面的显示，这需要配合Link或者NavLink配合使用。
## 3、React-router2的API ##
 - 组件
 ```
 Router
 Link
 IndexLink
 RoutingContext
 ```
 - 组件配置
 ```
 Route
 PlainRoute
 Redirect
 IndexRoute
 IndexRedirect
 ```
- mixins
```
生命周期
history
RouteCOntext
```
- Utilities
```
useRoutes
match
createRoutes
PropTypes
```
## 4、React-router4的API ##
- BrowserRouter
```
basename:string
getUserConfirmation:func
forceRefresh:bool
keyLength:number
children:node
```
- HashRouter
```
basename:string
getUserConfirmation;func
hashType:string
children:node
```
- Link
```
to:string | object
replace:bool
innerRef:func
```
- NavLink
```
activeClassName:string
activeStyle:object
exact:bool
strict:bool
isActive:func
location:object
ariaCurrent:string
```
- Router
```
history:object
children:node
```
- StaticRouter
```
basename:string
location:string | object
context:object
children:node
```
- Route
```
Route render methods
Router props
component
render:func
children:func
path:string | string[]
exact:bool
strict:bool
location:object
sensitive:bool
```
- Switch
```
location:object
children:node
```
- history
```
history is mutable
```
- location
- match
- matchPath
```
pathname
props
```
- withRouter
```
Component wrappedCpmponent
wrappedComponentRef:func
```
