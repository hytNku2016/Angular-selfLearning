# 路由与导航 #

大多数带路由的应用都要在index.html的<head>标签下先添加一个<base>元素，来告诉路由器该如何合成导航用的URL。
如果app文件夹是该应用的根目录（就像我们的范例应用一样），那就把href的值设置为下面这样：
	
	<base href>

每个带路由的Angular应用都有一个Router（路由器）服务的单例对象。

	import { RouterModule, Routes } from '@angular/router';

每个Route都会把一个URL的path映射到一个组件。 注意，path不能以斜杠（/）开头

**注意**：路由器使用先匹配者优先的策略来选择路由。 通配符路由是路由配置中最没有特定性的那个，因此务必确保它是配置中的最后一个路由。

## 路由连接与路由出口 ##

	<nav>	
		<!--页面路由导航元素,类似ui-sref-->
	    <a routerLink="/crisis-center" routerLinkActive="active">Crisis Center</a>
	    <a routerLink="/heroes" routerLinkActive="active">Heroes</a>
 	</nav>
	<!--具体导航的组件渲染容器，类似ui-view-->
	<router-outlet></router-outlet>

a标签上的RouterLink指令让路由器得以控制这个a元素。 这里的导航路径是固定的，因此可以把一个字符串赋给routerLink（“一次性”绑定）;如果需要更加动态的导航路径，那就把它绑定到一个返回链接参数数组的模板表达式。

每个a标签上的RouterLinkActive指令可以帮用户在外观上区分出当前选中的“活动”路由。 当与它关联的RouterLink被激活时，路由器会把CSS类active添加到这个元素上。 我们可以把该指令添加到a元素或它的父元素上。

使用了[routerLinkActiveOptions]='{ exact: true }'，那么只有在其URL与当前URL精确匹配时才会激活指定的RouterLink

**一个模板中只能有一个未命名的<router-outlet>**

	
## 重定向路由 ##
	
	 { path: '',   redirectTo: '/heroes', pathMatch: 'full' }

重定向路由需要一个pathMatch属性，来告诉路由器如何用URL去匹配路由的路径，否则路由器就会报错
pathMatch还可以制定为prefix； 

## 移除重复路由模块 ##

特性模块提供的路由会被组合（追加）进特性模块所导入的模块中的路由；这种机制让我们可以继续在特性模块中定义路由而不用修改主模块中的配置（因为主模块中也导入了RouterModule,RouterModule会提供一个单例的路由服务）。所以如果在两个地方定义了同一个路由，需要移除“重复”路由。

## 路由导入顺序 ##

路由配置的顺序很重要。 路由器会接受第一个匹配上导航所要求的路径的那个路由。

当所有的路由都在一个模块：AppRoutingModule中时，需要把默认路由和通配符路由放在最后；如果路由配置不在同一个模块中，则每个路由模块都会根据导入的顺序把自己的路由配置追加进去。 这样，我们需要注意把**通配符路由**放置在最后导入。

## 路由参数 ##

一般来说，路由导航动作发起有两种方式：

- 通过routerLink 点击链接来进行页面导航
- 通过内部函数命令式的导航到目标页

无论哪种方式，都可以为路由添加导航的连接参数。
链接参数数组中有两个条目：目标路由的path（路径），和一个用来指定所选的路由参数。
		
	//页面导航方式：表达式方式
	[routerLink]="['/detail',hero.id]"
	//内部函数导航方式
	this.router.navigate(['/hero', hero.id]);

## ActivatedRoute：一站式获取路由信息 ##

路由的路径和参数可以通过注入进来的一个名叫ActivatedRoute的路由服务来获取

- url: 该路由路径的Observable对象。它的值是一个由路径中各个部件组成的字符串数组。
- data: 该路由提供的data对象的一个Observable对象。还包含从resolve守卫中解析出来的值。
- params: 包含该路由的必选参数和可选参数的Observable对象。
- queryParams: 一个包含对所有路由都有效的查询参数的Observable对象。
- fragment: 一个包含对所有路由都有效的片段值的Observable对象。
- outlet: RouterOutlet的名字，用于指示渲染该路由的位置。对于未命名的RouterOutlet，这个名字是primary。
- routeConfig: 与该路由的原始路径对应的配置信息。
- parent: 当使用子路由时，它是一个包含父路由信息的ActivatedRoute对象。
- firstChild: 包含子路由列表中的第一个ActivatedRoute对象。
- children: 包含当前路由下激活的全部子路由。


		ngOnInit() {
		  this.route.params
		    // (+) converts string 'id' to a number
		    .switchMap((params: Params) => this.service.getHero(+params['id']))
		    .subscribe((hero: Hero) => this.hero = hero);
		}

ngOnInit对每个实例只调用一次。 我们需要一种方式来检测在同一个实例中路由参数什么时候发生了变化。 而params属性这个可观察对象（Observable）干净漂亮的处理了这种情况。

**当在组件中订阅一个可观察对象时，我们通常总是要在组件销毁时取消这个订阅。**ActivateRoute中的各种可观察对象就是属于少数例外情况不需要取消订阅。

## 子路由配置 ##

路由的子路由使用children来指定：
	
	const crisisCenterRoutes: Routes = [
	  {
	    path: 'crisis-center',
	    component: CrisisCenterComponent,
		//指定一级子路由
	    children: [
	      {
	        path: '',
	        component: CrisisListComponent,
			//指定二级子路由
	        children: [
	          {
	            path: ':id',
				//Crisis Detail路由是Crisis List的子路由,当我们选择了另一个危机时，CrisisDetailComponent会被复用(这样会减小开支）。
	            component: CrisisDetailComponent
	          },
	          {
	            path: '',
	            component: CrisisCenterHomeComponent
	          }
	        ]
	      }
	    ]
	  }
	];
	
	@NgModule({
	  imports: [
		// 特性路由配置必须使用RouterModule.forChild()
	    RouterModule.forChild(crisisCenterRoutes)
	  ],
	  exports: [
	    RouterModule
	  ]
	})
	export class CrisisCenterRoutingModule { }



## 第二路由 ##

定义命名出口：

	<router-outlet></router-outlet>
	<!--定义命名出口-->
	<router-outlet name="popup"></router-outlet>

导航到第二路由

	<a [routerLink]="[{ outlets: { popup: ['compose'] } }]">Contact</a>
	//在命令API中
	this.router.navigate([{ outlets: { popup: 'compose' }}]);

每个第二出口都有自己独立的导航，跟主出口的导航彼此独立。 修改主出口中的当前路由并不会影响到popup出口中的。 

**清楚第二路由**

	//把popup这个RouterOutlet设置为null会清除该出口，并且从当前URL中移除第二路由popup。	
	this.router.navigate([{ outlets: { popup: null }}]);

## 守卫路由 ##

用户不能在任何时候导航到任何地方，路由的导航需要做一些限定，比如：

- 该用户可能无权导航到目标组件。
- 可能用户得先登录（认证）。
- 在显示目标组件前，我们可能得先获取某些数据。
- 在离开组件前，我们可能要先保存修改。
- 我们可能要询问用户：你是否要放弃本次更改，而不用保存它们

因此，需要为路由添加守卫。

守卫返回一个值，以控制路由器的行为：

1. 如果它返回true，导航过程会继续
1. 如果它返回false，导航过程会终止，且用户会留在原地。
1. 守卫还可以告诉路由器导航到别处，这样也取消当前的导航。

**路由守卫可以以同步的方式返回一个布尔值，更多时候是返回一个Observable<boolean>或Promise<boolean>，并且路由器会等待这个可观察对象被解析为true或false**


路由器支持多种守卫：

- 用CanActivate来处理导航到某路由的情况。
- 用CanActivateChild处理导航到子路由的情况。
- 用CanDeactivate来处理从当前路由离开的情况。
- 用Resolve在路由激活之前获取路由数据。
- 用CanLoad来处理异步导航到某特性模块的情况。

TODO :案例编写



