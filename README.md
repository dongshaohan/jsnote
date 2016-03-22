## 持续更新ing（觉得对你有用就star一个呗～）
1. underfined不是关键字，要让一个变量指向未定义或删除该变量，`xxx = underfined`是错误的写法，因为
underfined可以当成一个变量来定义，就是说`var underfined = xxx`这样是合法的，
所以正确写法是使用void 加上任何数字，如`var xxx = void 0`，void加数字结果总是返回underfined。
（ps: typeof undefined  // 结果为underfined）

2. underfined、null、0、false、NaN、空字符串的逻辑结果均为false

3. IE专属
	
	```javascript
	var msie = document.documentMode; // documentMode is an IE-only property
	
	var ie = /*@cc_on !@*/false;
	```

4. 数组传递和复制
	
	```javascript
	ar a = [1,2,3];
	var b = a;
	delete b[1];
	console.log(a); // [1, undefined, 3]
	
	var a = [4,5,6];
	var b = a.slice(0);
	delete b[1];
	console.log(a); // [4, 5, 6]
	console.log(b); // [4, undefined, 6]
	```

5. 关于length
	1. 字符串的length值等于字符串个数.
	* 数组的length值等于数组长度.
	* 函数的length值等于形参个数.
	* arguments的length值等于实参个数.
	* object对象无length值.

6. 字符串API `charCodeAt`返回的unicode编码，通过toString(16)转成16进制，利用正则  
`/\u(00)“转换后的16位编码”/`可精确匹配。

7. 关于浮点数的运算误差
所有的支持二进制浮点数运算（绝大部分都是 IEEE 754[1] 的实现）都存在浮点数的运算误差。
	
	```javascript
	0.7 + 0.1  // 输出 0.7999999999  
	0.2 + 0.6  // 输出 0.8
	```

	其原因就是，在有限的存储空间下，绝大部分的十进制小数都不能用二进制浮点数来精确表示。例如，0.1 这个简单的十进制小数就不能用二进制浮点数来表示。
	你输入两个十进制数，转化为二进制运算过后再转化回来，在转化过程中自然会有损失了，
	但一般的损失往往在乘除运算中比较多，而JS在简单的加减法里也会出现这类问题，你也看到了，这个误差也是非常小的，但是却是不该出现的。
	
	```javascript
	// 解决方法  
	// 通过toFixed方法来指定四舍五入的小数位数  
	(0.2 + 0.1).toFixed(1)  // 0.3  
	(0.2 * 0.1).toFixed(1)  // 0.2
	```

8. 关于Ajax回调函数执行window.open等打开新窗口方法浏览器不支持解决方案。
	
	```javascript
	// 执行ajax时关闭异步，也就是把asnyc属性设置为false即可解决
	$.ajax({
		...
		asnyc: false
	});
	```

9. 今天面试YY遇到一道javascript笔试题，大概意思就是数组去重，当时自己写的方法不够高效，过后科普了一下，以此记录下来。

	```javascript
	function unique (arr) {
		var ret = []
		var hash = {}

		for ( var i = 0, len = arr.length; i < len; i++ ) {
			var elem = arr[i];
    		
    		if ( !hash[elem] ) {
      			ret.push(elem);
  				hash[elem] = true;
    		}
  		}
  		return ret
  	}
	```

10. YY笔试题，js获取当前url参数值接口

	```javascript
	function getQueryString (name) {
	    var reg = new RegExp( "(^|&)"+ name +"=([^&]*)(&|$)", "i" );
	    var url = window.location.search; // 得到当前url的查询字串(?以及后面的字段)
	    var ret = url.substr(1).match(reg); // reg没有全局标志，所以ret[0]是完整的匹配，arr[1]是第一个括号里捕获的字串，依此类推。

	    if ( ret ) 
	   		return unescape(ret[2]); // ret[2]保存的是reg第二个括号捕获的字串，unescape用来解码url中的字符。
	   	else 
	   		return null;
	}
	```

11. JSONP跨域原理解析
	
	```javascript
	原理：利用在页面中创建<script>节点的方法向不同域提交HTTP请求并且可在url中指定回调函数，  
		  获取json数据并在指定的回调函数中执行。  

	缺点：如果返回的数据格式有问题或返回失败了，并不会报错。而且只支持GET而不支持POST等其它类型的HTTP请求。
	```

12. 也是YY一道面试题，考察this作用域。
	
	```javascript
	var foo = {
		bar: '你好',
		func: function () {
			alert(this.bar);
		}
	},
	bar = foo.func;
	bar();
	/* 
	 * 答案：function () { alert(this.bar) }
	 * 把foo.func函数赋值给全局变量bar然后执行，这等价于执行全局函数。全局函数的this是指向window的，
	 * 所以alert(this.bar)等于alert(window.bar)，也就是alert函数自身。
	 */
	```

13. 定义一个函数function add (x) { }，实现alert( add(2)(3)(4) )的结果能够等于9，且可复用。
	
	```javascript
	/* 
	 * 这实际上就是 currying（柯里化），也就是把一个多变量的函数变成一系列单变量的函数。
	 * 每个函数接收一个参数，然后返回一个接收余下参数并返回结果的新函数。
	 * 这个过程中利用了闭包（closure）。也就是说，这种情况下，是一个函数返回另一个函数。
	 */
	function add (x) {
	    var sum = x;
	    var all = function (y) {
	        sum = i + y;
	        return all;
	    };
	    all.toString = all.valueOf = function () {
	        return sum;
	    };
	    return all;
	}
	/* 
	 * 这个知识点主要用到，在JavaScript中，打印和相加计算，会分别调用toString或valueOf函数，
	 * 所以我们可以重写all的toString或valueOf方法，使其返回sum的值。
	 */

	/*
	 * 另外飘逸的写法
	 */
	function add (num) { 
	  	num += ~~add; 
	  	add.num = num; 
	  	return add; 
	} 
	add.valueOf = add.toString = function () { return add.num }; 
	alert( add(3)(4)(5)(6) ); // 18
	```

14. 下面这道题，同样是在面试YY时遇到，主要考察作用域、变量声明、anguments。

	```javascript
	var b = 10;  
	function a (c) {  
		alert(b);
	    var b = 100;  
	    alert(b);  
	    anguments[0] = 2; 
	    alert(c);  
	}  
	a(3);
	alert(b);

	/* 
	 * 答案：underfined 100 2 10
	 * 1、a作用域内，js引擎会先查找所有声明的变量并赋值underfined，b已经声明，但此时还未赋值，所以b的值还是underfined。
	 * 2、b已经赋值100，alert结果100。
	 * 3、c传进去时是3，但c = anguments[0] = 2，即此时c等于2。
	 * 4、当前环境是全局作用域下，变量b在该环境下的值为10，故alert的结果为10。
	 */
	```

15. 了解变量声明提升和上下文对象。
	
	```javascript
	var a = 10;  
	function test () {  
	    a = 100;  
	    alert(a);  
	    alert(this.a);  
	    var a;  
	    alert(a);  
	}  
	test();  

	/* 
	 * 答案：100 10 100
	 * 1、test作用域内，var a由于变量声明提升，a ＝ 100并不是重置全局变量a，而是对当前作用域下a的赋值，所以alert的结果为100。
	 * 2、全局函数的this对指向window对象，所以this.a等于window.a，故alert的结果为10。
	 * 3、由于变量声明提升和赋值，此时的a还是100。
	 */
	```

16. JavaScript replace(RegExp, Function)解析
	
	template引擎的实现原理是通过正则匹配目标字串，replace传匿名函数对即将替换目标文本的字符串进行操作，想要了解template实现原理，我们要先了解replace的高级用法和实现原理。
	```javascript
	var str = 'dongshaohan';
	var reg = /a/g;
	str.replace(reg, function () {
		for ( var i = 0, len = arguments.length; i < len; i++ ) {   
        	console.log("第"+ (i + 1) +"个参数的值："+ arguments[i]);   
		};   
	});
	// 结果如下
	第1个参数的值：a
	第2个参数的值：6
	第3个参数的值：dongshaohan
	第1个参数的值：a
	第2个参数的值：9
	第3个参数的值：dongshaohan
	/*
	 * 匿名函数执行了两次，因为匹配到两次
	 * 第一个参数是通过reg匹配出来的结果，也就是表示匹配到的字符
	 * 第二个参数是匹配结果在str中的索引位置
	 * 第三个参数是str字符串本身
	 */
	
	var str = 'dongshaohan';
	var reg = /(h)a/g;
	str.replace(reg, function () {
		for ( var i = 0, len = arguments.length; i < len; i++ ) {   
        	console.log("第"+ (i + 1) +"个参数的值："+ arguments[i]);   
		};   
	});
	// 结果如下
	第1个参数的值：ha
	第2个参数的值：h
	第3个参数的值：5
	第4个参数的值：dongshaohan
	第1个参数的值：ha
	第2个参数的值：h
	第3个参数的值：8
	第4个参数的值：dongshaohan
	/*
	 * 匿名函数执行了两次，因为匹配到两次
	 * 第一个参数是通过reg匹配出来的结果，也就是表示匹配到的字符
	 * 第二个参数是reg中分组(h)所产生的结果
	 * 第三个参数是匹配结果在str中的索引位置
	 * 第四个参数是str字符串本身
	 */

	var str = 'dongshaohan';
	var reg = /(h)(a)/g;
	str.replace(reg, function () {
		for ( var i = 0, len = arguments.length; i < len; i++ ) {   
        	console.log("第"+ (i + 1) +"个参数的值："+ arguments[i]);   
		};   
	});
	// 结果如下
	第1个参数的值：ha
	第2个参数的值：h
	第3个参数的值：a
	第4个参数的值：5
	第5个参数的值：dongshaohan
	第1个参数的值：ha
	第2个参数的值：h
	第3个参数的值：a
	第4个参数的值：8
	第5个参数的值：dongshaohan
	/*
	 * 匿名函数执行了两次，因为匹配到两次
	 * 第一个参数是通过reg匹配出来的结果，也就是表示匹配到的字符
	 * 第二个参数是reg中分组(h)所产生的结果
	 * 第三个参数是reg中分组(a)所产生的结果
	 * 第四个参数是匹配结果在str中的索引位置
	 * 第五个参数是str字符串本身
	 */

	/* 最终结论 
	 * 第一个参数是reg这个完整的正则匹配出来的结果
     * 倒数第二个参数是第一个参数（匹配结果）在目标字符串中的索引位置
     * 最后一个参数是目标字符串本身
     * 如果reg中存在分组，那么参数列表的长度N>3一定成立，且第2到N-2个参数，分别为reg中分组所产生的匹配结果
     * 然后我们经常用到的一般都是第一个和倒数第二个，有时候能把一些基础的东西琢磨透，更能在开发中提高效率
     */
	```

17. 简单template实现原理。

	模板写法:
	```javascript
	<ul>
    <% for ( var i in items ) { %>
        <li class='<%= items[i].status %>'><%= items[i].text %></li>
    <% } %>
	</ul>
	```
	实现原理：
	* 遇到普通的文本直接当成字符串拼接。
	* 遇到`interpolate`(即`<%= %>`)，将其中的内容当成变量拼接在字符串中。
	* 遇到`evaluate`(即`<% %>`)，直接当成代码。

18. 货币快速换算
	
	```javascript
	var s = '1234567.89';
	parseFloat(s).toLocaleString(); // 1,234,567.89
	s.replace(/(\d)(?=(?:\d{3})+(?:\.|$))/g, '$1,'); // 1,234,567.89
	```

19. 奇淫技巧

	```javascript
	向下取整Math.floor，可以用|0，可以用~~，也可以用右移符>>代替。
	var a= 1.2|0; // 1
	var a = ~~1.2; // 1 （比Math.floor快4倍左右）
	var a = 1.2>>0; // 1
	/* 但是两者最好都只用在正整数上，因为只是舍掉了小数部分。Math.floor(-1.2)应该为-2，这两种方法的结果为-1 */

	用( new Function(stringCode) )()比eval快50倍。

	转数字用+
	var a = +'1234'; // 1234

	合并数组
	var a = [1,2,3];
	var b = [4,5,6];
	Array.prototype.push.apply(a, b);
	console.log(a); // [1,2,3,4,5,6]

	交换值
	a = [b, b=a][0];

	快速取数组最大和最小值
	Math.max.apply(Math, [1,2,3]); // 3
	Math.min.apply(Math, [1,2,3]); // 1

	运算符-->叫做趋向于，可以声明一个变量 然后让他 趋向于 另一个数。
	var x = 10; while (x --> 0)console.log(x); 
	// 9,8,7,6,5,4,3,2,1,0
	```

20. 高效复制对象
	
	```javascript
	// 浅复制
	var newObject = jQuery.extend({}, oldObject);

	// 深复制 较慢
	var newObject = jQuery.extend(true, {}, oldObject);

	// 比深复制快10% - 20% 但对Date对象不适用
	var newObject = JSON.parse(JSON.stringify(obj));
	```
	
21. 37互娱一道印象比较深的javascript笔试题
	
	```javascript
	var x = 20;
	var temp = {
	    x: 40,
	    foo: function () {
	        var x = 10;
	        return this.x;
	    }
	};

	console.log( temp.foo() );  			// 40 this对象为temp，this.x等于temp.x
	console.log( (temp.foo)() ); 			// 40 
	console.log( (temp.foo = temp.foo)() ); // 20 
	console.log( (temp.foo, temp.foo)() );	// 20 
	console.log( temp.foo.apply(window) );  // 20 apply使temp.foo的this对象为window上下文，this.x等于window.x
	console.log( temp.foo.apply(temp) ); 	// 40 apply使temp.foo的this对象指向temp上下文，this.x等于temp.x
	```

22. 一道javascript笔试题
	
	```javascript
	var a = 10; 
	a.pro = 10; 
	console.log(a.pro + a); 
	 
	var s = 'hello'; 
	s.pro = 'world'; 
	console.log(s.pro + s); 

	/*
	 * 答案: NaN undefinedhello
	 * 给基本类型数据加属性不报错，但是引用的话返回undefined，10+undefined返回NaN，
	 * 而undefined和string相加时转变成了字符串。
	 */
	```

23. 考察arguments

	```javascript
	var length = 10;
	function fn () {
	    alert(this.length);
	}
	var obj = {
	    length: 5,
	    method: function (fn) {
	        fn();
	        arguments[0]();
	    }
	}
	obj.method(fn);

	/*
	 * 答案: 10 1
	 * 这里的坑主要是arguments，我们知道取对象属于除了点操作符还可以用中括号，这里fn的scope是arguments，
	 * 即fn内的this===arguments，调用时仅传了一个参数fn，因此length为1。
	 */
	```

24. 函数声明优于变量声明 

	```javascript
	console.log(typeof fn); 
	function fn () {}; 
	var fn; 

	/*
	 * 答案: function
	 * 因为函数声明优于变量声明。
	 * 我们知道在代码逐行执行前，函数声明和变量声明会提前进行，而函数声明又会优于变量声明，
	 * 这里的优于可以理解为晚于变量声明后，
	 * 如果函数名和变量名相同，函数声明就能覆盖变量声明。所以以上代码将函数声明和变量声明调换顺序还是一样结果。
	 */
	```

25. 提高web性能方法(不断补充中...)
	
	```javascript
	一、YaHoo Web优化的14条原则

	1.尽可能的减少HTTP的请求数
	/*
	http请求是要开销的，想办法减少请求数自然可以提高网页速度。常用的方法，合并css，js（将一个页面中的css和js文件分别合并）以及 Image maps和css sprites等。
	当然或许将css，js文件拆分多个是因为css结构，共用等方面的考虑。阿里巴巴中文站当时的做法是开发时依然分开开发，然后在后台 对js，css进行合并，
	这样对于浏览器来说依然是一个请求，但是开发时仍然能还原成多个，方便管理和重复引用。yahoo甚至建议将首页的css和js 直接写在页面文件里面，而不是外部引用。
	因为首页的访问量太大了，这么做也可以减少两个请求数。而事实上国内的很多门户都是这么做的。而css sprites是指只用将页面上的背景图合并成一张，
	然后通过css的background-position属性定义不过的值来取他的背景。
	*/

	2.使用CDN（内容分发网络）
	/*
	用户离web server的远近对响应时间也有很大影响。从用户角度看，把内容部署到多个地理位置分散的服务器上将有效提高页面装载速度。但是该从哪里开始呢？
	作为实现内容地理分布的第一步，不要试图重构web应用以适应分布架构。改变架构将导致多个周期性任务，如同步session状态，在多个server之间复制数据库交易。
	这样缩短用户与内容距离的尝试可能被应用架构改版所延迟，或阻止。
	我们还记得80-90%的最终用户响应时间花在下载页面中的各种元素上，如图像文件、样式表、脚本和Flash等。与其花在重构系统这个困难的任务上，还不如先分布静态内容。
	这不仅能大大减少响应时间，而且由于CDN的存在，分布静态内容非常容易实现。
	CDN是地理上分布的web server的集合，用于更高效地发布内容。通常基于网络远近来选择给具体用户服务的web server。
	一些大型网站拥有自己的CDN，但是使用如Akamai Technologies, Mirror Image Internet, 或 LimelightNetworks等CDN服务提供商的服务将是划算的。
	在Yahoo!把静态内容分布到CDN减少了用户影响时间20%或更多。切换到CDN的代码修改工作是很容易的，但能达到提高网站的速度。
	*/

	3.增加Expires Header
	/*
	现在越来越多的图片，脚本，css，flash被嵌入到页面中，当我们访问他们的时候势必会做许多次的http请求。其实我们可以通过设置Expires header 来缓存这些文件。
	Expire其实就是通过header报文来指定特定类型的文件在览器中的缓存时间。大多数的图片，flash在发布后都是不需要经常修改的，
	做了缓存以后这样浏览器以后就不需要再从服务器下载这些文件而是而直接从缓存中读取，这样再次访问页面的速度会大大加快
	*/

	4.启用Gzip压缩页面元素
	/*
	通过压缩HTTP响应内容可减少页面响应时间。从HTTP/1.1开始，web客户端在HTTP请求中通过Accept-Encoding头来表明支持的压缩类型，
	如：Accept-Encoding: gzip, deflate.
	如果Web server检查到Accept-Encoding头，它会使用客户端支持的方法来压缩HTTP响应，会设置Content-Encoding头，如：Content-Encoding: gzip。
	Gzip是目前最流行及有效的压缩方法。其他的方式如deflate，但它效果较差，也不够流行。通过Gzip，内容一般可减少70%。
	Gzip的思想就是把文件先在服务器端进行压缩，然后再传输。这样可以显著减少文件传输的大小。
	传输完毕后浏览器会重新对压缩过的内容进行解压缩，并执行。目前的浏览器都能“良好”地支持gzip
	*/

	5.将css放在页面最上面
	/*
	我们发现把样式表移到HEAD部分可以提高界面加载速度，因此这使得页面元素可以顺序显示。
	在很多浏览器下，如IE，把样式表放在document的底部的问题在于它禁止了网页内容的顺序显示。
	浏览器阻止显示以免重画页面元素，那用户只能看到空白页了。Firefox不会阻止显示，但这意味着当样式表下载后，有些页面元素可能需要重画，这导致闪烁问题。
	HTML规范明确要求样式表被定义在HEAD中，因此，为避免空白屏幕或闪烁问题，最好的办法是遵循HTML规范，把样式表放在HEAD中。
	*/

	6.将script放在页面最下面

	7.避免在CSS中使用Expressions

	8.把javascript和css都放到外部文件中

	9.减少DNS查询
	/*
	DNS用于映射主机名和IP地址，一般一次解析需要20～120毫秒。为达到更高的性能，DNS解析通常被多级别地缓存，
	如由ISP或局域网维护的caching server，本地机器操作系统的缓存（如windows上的DNS Client Service），浏览器。
	IE的缺省DNS缓存时间为30分钟，Firefox的缺省缓冲时间是1分钟。
	减少主机名可减少DNS查询的次数，但可能造成并行下载数的减少。避免DNS查询可减少响应时间，而减少并行下载数可能增加响应时间。
	一个可行的折中是把内容分布到至少2个，最多4个不同的主机名上。
	*/

	10.压缩 JavaScript 和 CSS

	11.避免重定向

	12.移除重复的脚本

	13.配置ETags

	14.缓存Ajax
	```

26. 提高前端工作效率的方法(不断补充中...)

	```javascript
	一.做好时间管理，要有工作紧急优先级
	/*
	1.做好计划。在自己效率高的时候，做一些难的事情；效率低的时候，做一些简单的事。
	2.一段时间只做一件事情。当一件事做的告一段落，再做另外一件事，而不是穿插着做。比如，写页面时，我会先写 HTML ，再写 CSS ，再切图，再写js
	*/

	二.用好工具
	三.经验和阅历
	四.使用新技术
	```