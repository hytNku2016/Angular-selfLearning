## 描述 ##

Angular应用程序以及Angular本身都依赖于很多第三方包(包括Angular自己)提供的特性和功能。 这些包由Node包管理器(npm)负责安装和维护。

**Node.js和npm是做Angular开发的基础。**

## package.json ##

package.json包含两组包：dependencies和devDependencies。

dependencies下的这些包是**运行本应用**的基础，而devDependencies下的只在**开发此应用时**才用得到。 

通过为install命令添加--production参数，可以在生产环境下安装时排除devDependencies下的包
	
	npm install my-application --production

## dependencies ##

dependencies区下有三类包：

- **特性**-- 特性包为应用程序提供了框架和工具方面的能力。
- **填充(Polyfills)**-- 填充包弥合了不同浏览器上的JavaScript实现方面的差异。
- **其它**-- 其它库对本应用提供支持，比如bootstrap包提供了HTML中的小部件和样式。

![](https://github.com/hytNku2016/Angular-selfLearning/blob/master/imgs/dependencies.PNG)
