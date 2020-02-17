## 1、webpack4笔记 ##
 - webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle。
 - webpack4将命令行相关代码迁移出去了，需要安装webpack-cli或webpack-command;
 `npx webpack index.js -o build.js`
 - webpack4引入了模式(mode),它有development、production、none,默认使用production.

 		npx webpack index.js -o build.js --mode developmennt

 - 当不指定打包文件，webpack4会默认查找src/index.js，并且默认会输出到dist/main.js文件夹下，这是webpack4引入的一个约定。

 		npx webpack --mode development
 - webpack4命令监控文件:

 		npx webpack --mode development --watch
 - webpack4刷新浏览器，提供webpack-dev-server来解决，它是一个基于expressjs的开发服务器，提供实时刷新浏览器页面的功能，不过webpack-dev-server进入维护模式，文档中推荐使用webpack-serve替代。

	 	npx webpack-serve
 - 打包：在完成项目开发后，输出文件给生产环境部署，执行：

  		npx webpack --mode production
 - 清理 dist:clean-webpack-plugin。

 		npx webpack --mode production
	webpack 就会在构建前清空 dist 目录。
 - html-webpack-plugin:自动生成 index.html:

 2、常见场景
 - 分离应用程序app和第三方库{venodr}入口：

	const config = {
		entry: {
    		app: './src/app.js',
    		vendors: './src/vendors.js'
		}
	};
    webpack从app.js和vendor.js开始创建依赖图，这些依赖图是彼此完全分离、独立的，这种只有一个入口起点的单页应用程序中。
    此设置允许使用commonsChunkplugin从应用程序中提取vendor引用到vendor bundle并把引用vendor的部分替换为__webpack_require__()调用。如果应用程序bundle中没有vendor代码，那么你可以在webpack中实现被称为长效缓存的通用模式。

 - 多页面应用程序：

		 const config = {
		  entry: {
		    pageOne: './src/pageOne/index.js',
		    pageTwo: './src/pageTwo/index.js',
		    pageThree: './src/pageThree/index.js'
		  }
		};
webpack 需要 3 个独立分离的依赖图，在多页应用中服务器将为你获取一个新的 HTML 文档。页面重新加载新文档，并且资源被重新下载。然而，这给了我们特殊的机会去做很多事，使用 CommonsChunkPlugin 为每个页面间的应用程序共享代码创建 bundle。由于入口起点增多，多页应用能够复用入口起点之间的大量代码/模块，从而可以极大地从这些技术中受益
 - 模式：提供 mode 配置选项，告知 webpack 使用相应模式的内置优化。

		选项	                         描述
		development            会将 process.env.NODE_ENV 的值设为 development。启用 NamedChunksPlugin 和 NamedModulesPlugin。
		production            会将 process.env.NODE_ENV 的值设为 production。启用 FlagDependencyUsagePlugin, FlagIncludedChunksPlugin, ModuleConcatenationPlugin, NoEmitOnErrorsPlugin, OccurrenceOrderPlugin, SideEffectsFlagPlugin 和 UglifyJsPlugin.
配置后可以对应的默认启动：

		// webpack.development.config.js
		module.exports = {
		+ mode: 'development'
		- plugins: [
		-   new webpack.NamedModulesPlugin(),
		-   new webpack.DefinePlugin({ "process.env.NODE_ENV": JSON.stringify("development") }),
		- ]
		}
		// webpack.production.config.js
		module.exports = {
		+  mode: 'production',
		-  plugins: [
		-    new UglifyJsPlugin(/* ... */),
		-    new webpack.DefinePlugin({ "process.env.NODE_ENV": JSON.stringify("production") }),
		-    new webpack.optimize.ModuleConcatenationPlugin(),
		-    new webpack.NoEmitOnErrorsPlugin()
		-  ]
		}
 - 使用 loader：在你的应用程序中，有三种使用 loader 的方式：

	     配置（推荐）：在 webpack.config.js 文件中指定 loader。

	     内联：在每个 import 语句中显式指定 loader。
    
	    import Styles from 'style-loader!css-loader?modules!./styles.css';
    
	     CLI：在 shell 命令中指定它们。
	    webpack --module-bind jade-loader --module-bind 'css=style-loader!css-loader'
 - 模块热替换：<br/>
 模块热替换(HMR - Hot Module Replacement)功能会在应用程序运行过程中替换、添加或删除模块，而无需重新加载整个页面。主要是通过以下几种方式，来显著加快开发速度：

	A:保留在完全重新加载页面时丢失的应用程序状态。
	B:只更新变更内容，以节省宝贵的开发时间。
	C:调整样式更加快速 - 几乎相当于在浏览器调试器中更改样式。
 - ES2015 中的 import 和 export 语句已经被标准化。虽然大多数浏览器还无法支持它们，但是 webpack 却能够提供开箱即用般的支持。
 - 代码分离:代码分离是 webpack 中最引人注目的特性之一。此特性能够把代码分离到不同的 bundle 中，然后可以按需加载或并行加载这些文件。代码分离可以用于获取更小的 bundle，以及控制资源加载优先级，如果使用合理，会极大影响加载时间.
 有三种常用的代码分离方法：

	    入口起点：使用 entry 配置手动地分离代码。
	    防止重复：使用 CommonsChunkPlugin 去重和分离 chunk。
	    动态导入：通过模块的内联函数调用来分离代码。
 - 


## 3、常见插件 ##
 - html-webpack-plugin
 - html-webpack-template
 - clean-webpack-plugin
 - webpack-manifest-plugin
 - webpack-dev-server
 - hot-module-replacement
 - react-hot-loader
 - commons-chunk-plugin:
 CommonsChunkPlugin 插件可以将公共的依赖模块提取到已有的入口 chunk 中，或者提取到一个新生成的 chunk。
 - extract-text-webpack-plugin:用于将 CSS 从主应用程序中分离
 - bundle-loader:用于分离代码和延迟加载生成的 bundle。
 - 
