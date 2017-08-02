# 描述 #
component是Angular2的装饰器，

----------
- npm package : @angular/core
- Module: import {Component} from '@angular/core';
- Source: core/src/metadata/directives.ts

----------

# 如何使用 #

    @Component({
		selector: 'greet',
		template: 'Hello {{name}}!'
	})
	export class Greet {
	  name: string = 'World';
	}
	
## 使用描述 ##
component装饰器允许你将一个typescript类描述为一个Angular2的组件，并且该装饰器提供额外的元数据来决定该组件如何被处理、初始化以及在运行时期如何运用。

组件是Angular应用中的核心UI构建模块。一个Angular应用实际上是一个component的树形结构。Angular组件本质上是Angular Directives的子集；但component根directives不同的地方在于，component一般含有模板，并且一个DOM元素标签上只能绑定一个组件；

一个component必须属于一个NgModule，以方便该component能被其他的component或者Application所引用。为了指定模板属于某个模块，必须将该component放置在NgModule的declarations的列表中；
	
component可以通过实现生命周期钩子函数来控制component的运行时行为；

## 元数据描述 ##

1. animations - 该组件的动画效果列表
1. changeDetection - 组件所使用的改变检测策略
1. encapsulation - 该组件使用的样式封装策略
1. entryComponents - 动态插入到此组件的视图中的组件列表
1. exportAs - 组件实例在模板中导出的名称
1. host - 类属性与宿主元素事件、属性、元素属性的绑定的映射
1. inputs - 组件输入的绑定数据对应的类属性
1. interpolation -此组件模板中使用的自定义插值标记
1. moduleId - 定义此组件的文件的ES / CommonJS模块ID
1. outputs - 该组件输出的事件对应的类属性
1. providers - 该组件及其子组件所需要的服务对应的提供者
1. queries - 配置可以注入到该组件的查询对象
1. selector - CSS选择器，用来在HTML模板中标识该组件
1. styleUrls - 应用到该组件试图的样式表的文件路径列表
1. styles - 应用到该组件试图的内联样式表
1. template - 该组件使用的内联模板
1. templateUrl - 该组件使用的外部视图模板文件的路径
1. viewProviders - 该组件及其子组件的视图提供者

## 例子 ##

