# 描述 #

针对常见的漏洞和攻击（比如XSS攻击），Angular提供了一些内置的保护。应用级别的保护不是本章讨论的范围。

## XSS攻击 ##
跨站脚本(XSS)允许攻击者将恶意代码注入到页面中。这些代码可以偷取用户数据 （特别是它们的登录数据），还可以冒充用户执行操作。它是Web上最常见的攻击方式之一。

为了放在XSS攻击，需要放置恶意的代码进入DOM。

## Angular的跨站脚本安全策略 ##

Angular默认所有的值都是不安全的；当值从模板中以属性(property)、DOM元素属性（Attribute）,Css绑定样式类或者{{expression}}等途径进入到DOM的时候，Angular会对这些值进行无害化处理，对不可信任的值进行编码。

**例外**：Angular的模板中的值默认是安全的。

## Angular的无害化环境 ##

Angular定义了四个安全环境 - HTML，样式，URL，和资源URL：

- HTML：值需要被解释为HTML时使用，比如当绑定到innerHTML时。
- 样式：值需要作为CSS绑定到style属性时使用。
- URL：值需要被用作URL属性时使用
	
		<a href>

- 资源URL：值需要被当做代码而加载并执行时使用

		<script src>
Angular会对前三项中种不可信的值进行无害化处理。但Angular无法对第四种资源URL进行无害化，因为它们可能包含任何代码

### 无害化处理 ###

- {{}}：插值表达式中，会对expression进行编码，HTML不会被解释，故可以显示特殊符号，如尖括号（因为被编码了）
- innerHTML：内部HTML会进行无害化处理，移除特定标签字段，保留安全的内容
- 避免直接使用DOM API，尽量使用Angular的模板
- 使用内容安全策略
- 服务端XSS攻击防护（设置XSS过滤器）
- 使用离线模板编译器（动态模板生成需要慎重，因为模板会绕过Angular的自带保护机制）


### 设置信任白名单 ###

注入DomSanitizer服务，然后调用下面的方法之一，你就可以把一个值标记为可信任的。

- bypassSecurityTrustHtml
- bypassSecurityTrustScript
- bypassSecurityTrustStyle
- bypassSecurityTrustUrl
- bypassSecurityTrustResourceUrl

		constructor(private sanitizer: DomSanitizer) {
 		this.trustedUrl = sanitizer.bypassSecurityTrustUrl( 'javascript:alert("Hi there")');

## HTTP级别的漏洞 ##

### 跨站请求伪造 ###

在CSRF中，攻击者欺骗用户，让他们访问一个假冒页面，该页面带有恶意代码，秘密向服务器发送恶意请求。

为了防止这种攻击，必须保证用户的请求是从自己的应用中发出的；
一般的防护策略是：服务器随机生成一个token到cookie；客户端获取该cookie，并根据token设定访问头；
这个技术之所以有效，是因为所有浏览器都实现了**同源策略**。只有设置cookie的网站的代码可以访问该站的cookie，并为该站的请求设置自定义页头。

Angular的http客户端在其XSRFStrategy中具有对这项技术的内置支持。 默认的CookieXSRFStrategy会被自动开启 在发送每个请求之前，CookieXSRFStrategy查询名为XSRF-TOKEN的cookie，并设置一个名为X-XSRF-TOKEN的HTTP请求头，并把该cookie的值赋给它。

	{ provide: XSRFStrategy, useValue: new CookieXSRFStrategy('myCookieName', 'My-Header-Name') }
	或者
	{ provide: XSRFStrategy, useClass: MyXSRFStrategy }

### XSSI跨站脚本包含 ###

一般是老的浏览器才会出现；暂不考虑

	
	


