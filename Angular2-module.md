# 描述 #
Angular模块可以把应用组织成多个内聚的功能模块

本质上来说，Angular模块是使用@NgModule装饰器装饰的类。@NgModule会接受一个元数据，用来告诉Angular如何去编译和运行模块代码。

**模块是组织和使用外部扩展应用的最佳途径**

Angular模块把组件、指令、管道打包成内聚的功能块，每个模块聚焦于一个特性区域，业务领域，工作流或者通用工具。

模块可能会在启动时主动加载或者由路由器进行异步懒加载。

## 应用根模块 ##

一个程序只能有一个跟模块，可以有很多特性模块。

@NgModule装饰器用来为模块定义元数据。当 Angular 引导应用时，它会在 DOM 中渲染AppComponent，并把结果放进index.html的<my-app>元素标记内部。

    import { NgModule }      from '@angular/core';
	import { BrowserModule } from '@angular/platform-browser';
	import { AppComponent }  from './app.component';
	
	@NgModule({
	  imports:      [ BrowserModule ],//每个运行在浏览器中的应用都必须导入BrowserModule
	  declarations: [ AppComponent ],
	  bootstrap:    [ AppComponent ]//@NgModule.bootstrap属性把AppComponent标记为引导 (bootstrap) 组件
	})
	export class AppModule { }

# main.ts引导 #
Angular应用中，通过main.ts 来配置应用的引导选项。
针对浏览器平台，有两种引导方式：

- 即时编译器（JiT）动态引导
- 预编译（AoT)静态引导

## JiT动态引导 ##

Angular编译器会在浏览器中编译并引导改程序。

	// The browser platform with a compiler
	import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
	
	// The app module
	import { AppModule } from './app/app.module';
	
	// Compile and launch the module
	platformBrowserDynamic().bootstrapModule(AppModule);


## AoT静态引导 ##

>  **静态方案可以生成更小、启动更快的应用，建议优先使用它，特别是在移动设备或高延迟网络下。**

使用静态选项，Angular 编译器作为构建流程的一部分提前运行，生成一组类工厂。它们的核心就是AppModuleNgFactory。

    // The browser platform without a compiler
	import { platformBrowser } from '@angular/platform-browser';
	
	// The app module factory produced by the static offline compiler
	import { AppModuleNgFactory } from './app/app.module.ngfactory';
	
	// Launch with the app module factory.
	platformBrowser().bootstrapModuleFactory(AppModuleNgFactory);

**无论是 JiT 还是 AoT 编译器都会从同一份AppModule源码中生成一个AppModuleNgFactory类。 JiT 编译器动态地在浏览器的内存中创建这个工厂类。 AoT 编译器把工厂输出成一个物理文件，也就是我们在静态版本main.ts中导入的那个。**

# 模块元数据 #

-  **declarations** ： 声明该模块所拥有的 **组件、管道、指令等视图类型元素**
-  **imports** : 导入该模块所依赖的其他模块
-  **providers**: 声明该模块所需要的服务逻辑提供商；指定应用程序的根级别需要使用的service。（Angular2中没有模块级别的service，所有在NgModule中声明的Provider都是注册在根级别的Dependency Injector中）
-  **exports** : 导出本模块开放出来的类，以供其他模块使用；可以导出模块，也可以导出组件等视图类
-  **bootstrap** : 配置根模块

**组件、指令和管道只能属于一个模块。永远不要再次声明属于其它模块的类。如果需要应用其他模块中的组件、指令、管道，只需要将导入该模块即可**

## @NgModule.providers 作用范围 ##
Angular中，模块没有自己的注入器，@NgModule.providers具有全应用级作用域,也就是说，它们也可用于整个应用的注入中。

**作为一个通用的规则，带提供商的模块应该只被导入一次，且最好在应用的根模块中导入。 那里也是配置、包装和改写这些服务的最佳位置;
总是在根模块AppModule中注册全应用级服务，而不要在根组件AppComponent中。更一般地说，优先把提供商注册进模块中，而不是组件中。**

模块的@NgModule.providers的全应用级作用域导致一个问题：导入的提供商很容易被由其它导入模块中的同类提供商替换掉；

解决：
	
1. 不要依赖于“启动时加载”模块的providers。
2. 让需要限定作用域范围的模块懒加载；因为Angular给了惰性加载模块自己的子注入器。 该模块中的提供商只对由该注入器创建的组件树可见。
3. 如果必须在应用程序启动时主动加载该特定范围的模块，就改成在组件中提供该服务。

## 特性模块 ##

特性模块是带有@NgModule装饰器及其元数据的类，就像根模块一样。 特性模块的元数据和根模块的元数据的属性是一样的。

**根模块和特性模块还共享着相同的执行环境。 它们共享着同一个依赖注入器，这意味着某个模块中定义的服务在所有模块中也都能用。**

特性模块与根模块的区别

- 我们引导根模块来启动应用，但导入特性模块来扩展应用。
- 特性模块可以对其它模块暴露或隐藏自己的实现。
- 在其它任何模块中都不要导入BrowserModule。 特性模块和惰性加载模块应该改成导入CommonModule

**特性模块不会继承其他模块对组件、指令、管道的访问权，即declarations是私有的；模块之间的imports也是私有的；只有providers是共享全局作用域**

非懒加载的模块需要在根模块中导入，以便在应用启动时加载它的路由和组件。

## 懒加载模块 ##
懒加载模块是特性模块的一种。一般通过路由懒加载。

具体加载过程参考路由家在机制。

惰性加载模块的位置是字符串而不是类型。 在本应用中，该字符串同时标记出了模块文件和模块类，两者用#分隔开。

	比如：{ path: 'heroes', loadChildren: 'app/hero/hero.module#HeroModule' }

## 共享（shared）模块 ##

在创建应用中，可能会有一些公共的组件、指令、管道；此时，可以对这些公共资源归并到sharedModule中，并共享给需要它们的模块中。

不要在共享模块中把应用级单例添加到providers中。 否则如果一个惰性加载模块导入了此共享模块，就会导致它自己也生成一份此服务的实例。

## 核心（core）模块 ##
有一些只用于AppComponent的模板的一次性的组件（例如：加载动画、消息浮层和模态对话框等），如果把它们留在根目录，还是显得太大、太乱了，我们可以把它们收集到单独的CoreModule中，并且只在应用启动时导入它一次，而不会在其它地方导入它。

**特别的：** 应用通常还有很多全应用级别的单例服务，当程序启动时，每个服务都只能在应用的“根注入器”中注册一次，以保证全应用使用的是同一个单例服务。

- 禁止多次导入coreModule( **主要防止在懒加载中添加核心组件** ）
	
		constructor (@Optional() @SkipSelf() parentModule: CoreModule) {
		  if (parentModule) {
		    throw new Error(
		      'CoreModule is already loaded. Import it in the AppModule only');
		  }
		}	

- 用静态方法 CoreModule.forRoot 配置核心服务

	1.	在核心模块中提供并配置服务

			static forRoot(config: UserServiceConfig): ModuleWithProviders {
		   		return {
			      ngModule: CoreModule,
			      providers: [
			        {provide: UserServiceConfig, useValue: config }
			      ]
			    };
			  }
	
	2. 在核心服务中添加可选配置

			constructor(@Optional() config: UserServiceConfig) {
			  if (config) { this._userName = config.userName; }
			}