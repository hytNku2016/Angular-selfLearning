# 描述 #

>  **管道可以在模板中转换显示的内容**

管道的本质是：把数据作为输入，然后转换它，给出期望的输出

## 内置的管道 ##

Angular内置了一些管道：DatePipe，UpperCasePipe、LowerCasePipe、CurrencyPipe和PercentPipe；
它们可以使用在任何模板中。

## 管道参数化 ##

在管道名后面添加一个冒号( : )再跟一个参数值，来为管道添加参数(比如currency:'EUR')；如果我们的管道可以接受多个参数，那么就用冒号来分隔这些参数值(比如slice:1:5)。

可以借助属性绑定，动态制定参数

## 链式管道 ##

把管道链在一起，以组合出一些潜在的有用功能
	
	The chained hero's birthday is
	{{ birthday | date | uppercase}}

结果显示为：**APR 15, 1988**

## 自定义管道 ##

	import { Pipe, PipeTransform } from '@angular/core';
	/*
	 * Usage:
	 *   value | exponentialStrength:exponent
	 * Example:
	 *   {{ 2 |  exponentialStrength:10}}
	 *   formats to: 1024
	*/
	@Pipe({name: 'exponentialStrength'})
	export class ExponentialStrengthPipe implements PipeTransform {
	  transform(value: number, exponent: string): number {
	    let exp = parseFloat(exponent);
	    return Math.pow(value, isNaN(exp) ? 1 : exp);
	  }
	}

**PipeTransform接口的transform方法，该方法接受一个输入值和一些可选参数，并返回转换后的值**

## 纯(pure)管道与非纯(impure)管道 ##

Angular只有在它检测到输入值发生了纯变更时才会执行纯管道；纯变更是指对**原始类型值**(String、Number、Boolean、Symbol)的更改， 或者对**对象引用**(Date、Array、Function、Object)的更改。

Angular会在每个组件的变更检测周期中执行非纯管道。 非纯管道可能会被调用很多次，和每个按键或每次鼠标移动一样频繁。

## 非纯AsyncPipe ##

AsyncPipe接受一个Promise或Observable作为输入，并且自动订阅这个输入，最终返回它们给出的值。

TODO：将来需要再进一步学习