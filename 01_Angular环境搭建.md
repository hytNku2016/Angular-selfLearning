# Angular2环境搭建 #

>主要是使用Angular CLI来进行环境搭建

本篇是大漠穷秋斗鱼视频讲解的笔记，结合个人实践，主要用于解决Angular环境问题；

	开发工具：windows x64bit ,Vscode, npm, 

	前提：安装nodejs

## node.js安装 ##
-	官网下载合适版本,目前版本是`v.6.11.0`
-	查看安装的node.js的版本 **【很重要，因为版本不同，功能支持程度是不一样的】** 
	
		node -v //v.6.11.0
-	node-sass被墙掉了，进入cnpm去安装；cnpm安装参考官网；
	
		全局空间路径:`C:\Users\Administrator\AppData\Roaming`;
-	npm 配置相关
		
		可以查看本地npm配置 npm config list
		具体配置使用 npm config set <key> <value>

## Angular CLI 配置使用 ##
> 参考官网 https://cli.angular.io/

-	安装
	
		npm install -g @angular/cli //推荐使用cnpm安装，并注意安装日志，防止安装过程不完整
-	如果正在使用Angular CLI 1.0.0-beta.28 或者更低版本，需要更新CLI

		1.	卸载angular-cli 包
			
			npm uninstall -g angular-cli
			npm uninstall --save-dev angular-cli
		2.	更新angular cli 到新版本；必须同时更新全局的包和项目的本地包
			-	更新全局的包
					
				npm uninstall -g @angular/cli
				npm cache clean
				npm install -g @angular/cli@latest
			-	更新本地工程包
			
				rmdir /S/Q node_modules dist//该命令针对windows cmd使用
				npm install --save-dev @angular/cli@latest
				npm install

-	创建新项目 (该过程会自动帮创建文档结构，并试图安装一些包）
		
		ng new projName	
-	进入项目目录，安装依赖 cnpm install;安装目录是工程下的node_modules中 **注意，该目录不需要提交到版本库中**
-	 编译服务 ng build --sourcemap --prod
-	 启动服务 ng serve

> 此时空项目就建立好了，尽量不要修改目录结构；
> 也可以使用现成的工程 niceFish（开源中国上）
> 工程IDE 采用visual studio code

## 跟后台对接 ##

> 将项目从branch切换到nicefish分支上；该分支上实现了对后台的访问对接

-	后端使用IDEA来开发
-	前后端分离，独立开发；
-	使用ngix来反向代理到前端目录编译后的dist目录；后端走/api开头的路径；



分离开发后 session 管理策略：
	
1. 粘滞性的session
2. session的拷贝
3. 共享session
4. 采用第三方托管session（如redis)

Angular如何处理SEO？

GitHub Angular上有universal项目；
