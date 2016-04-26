#Express 学习笔记

## 1. Express介绍
express是一个基于node.js，自身的功能及其简单，完全是由路由和中间件构成的一个WEB开发框架。一个Express应用本质上就是在不断的调用各种中间件。

## Express 中间件
**中间件（Middleware）** 是一个函数，它可以访问请求对象（request object (req)）, 响应对象（response object (res)）, 和 web 应用中处于请求-响应循环流程中的中间件，一般被命名为 next 的变量。

要在Express中使用中间件，可以使用app.use方法:
	
	app.use([path,] function [, function...])

当没有指定path时，默认路径为'/'。当你为某个路径绑定了中间件，则当

以该路径为基础的路径被访问时，则都会应用该中间件。比如，你为'/abcd'路径设置了中间件，则对'/abcd/xxx'的访问都会应用该中间件。

中间件的函数原型如下:

	function (req, res, next)
	
第一个参数是Request对象req。第二个参数是Response对象res。第三个则是用来驱动中间件调用链的函数next，如果你想让后面的中间件继续处理请求，就需要调用next方法

## Express 路由

路由是指如何定义应用的端点（URIs）以及如何响应客户端的请求。Express的路由可以理解为是Expressz中间件的一种。

express对象可以针对常见的HTTP方法指定路由，使用下面的方法：

	app.METHOD(path, callback [, callback ...])
	
METHOD可以是GET、POST等HTTP方法的小写，例如app.get，app.post。path部分呢，既可以是字符串字面量，也可以是正则表达式。

使用express构建Web服务器时，很重要的一部分工作就是决定怎么响应针对某个路径的请求，也即路由处理。

## 静态文件处理
在要处理诸如css、javascript之类的静态文件，可以使用Express的内置中间件**express.static** 进行处理。

将包含静态资源的目录的名称传递给 express.static 中间件函数，以便开始直接提供这些文件。例如，使用以下代码在名为 public 的目录中提供图像、CSS 文件和 JavaScript 文件：

	app.use(express.static('public'));
	
现在，可以装入位于 public 目录中的文件：

	http://localhost:3000/images/kitten.jpg
	http://localhost:3000/css/style.css
	http://localhost:3000/js/app.js
	http://localhost:3000/images/bg.png
	http://localhost:3000/hello.html
	
(**注意：Express 相对于静态目录查找文件，因此静态目录的名称不是此 URL 的一部分。**)

要使用多个静态资源目录，请多次调用 express.static 中间件函数：s

	app.use(express.static('public'));
	app.use(express.static('files'));
	
Express 以您使用 express.static 中间件函数设置静态目录的顺序来查找文件。

要为 express.static 函数提供的文件创建虚拟路径前缀（路径并不实际存在于文件系统中），请为静态目录指定安装路径，如下所示：

	app.use('/static', express.static('public'));

现在，可以装入具有 /static 路径前缀的 public 目录中的文件：

	http://localhost:3000/static/images/kitten.jpg
	http://localhost:3000/static/css/style.css
	http://localhost:3000/static/js/app.js
	http://localhost:3000/static/images/bg.png
	http://localhost:3000/static/hello.html

然而，向 express.static 函数提供的路径相对于您在其中启动 node 进程的目录。如果从另一个目录运行 Express 应用程序，那么对于提供资源的目录使用绝对路径会更安全：

	app.use('/static', express.static(__dirname + '/public'));	