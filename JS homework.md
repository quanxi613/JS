=============

##JS练习

1.通过递归的方式实现深度clone

预备知识：

各种数据类型的赋值方法及类型判断方法

值类型和引用类型的区别

数组和对象的遍历方法

递归算法

具体描述

实现方法clone，可以用其针对可能是任何类型的对象，进行值拷贝

例如

	var a = 1;
	var b = clone(a);
	b = 2;
	console.log(a, b); // 1, 2
	--------
	var a = [1, 2];
	var b = clone(a);
	a[0] = 'changed a';
	b[1] = 'changed b';
	console.log(a, b); // a:['changed a', 2], b:[1, changed b]
	--------
	var a = {
	    key1: 'value1',
	    key2: [{
	        id: 1,
	        data: [10, 20]
	    }, {
	        id: 2,
	        data: [20, 30]
	    }]
	}
	var b = clone(a);
	b.key1 = 'value changed';
	b.key2[1].data[0] = 'changed data';
	console.log(a, b);


实现代码：

	function clone(obj){

			var o = obj instanceof Array ? [] : {};

			for( var i in obj){

				if (obj[i] instanceof Object) {

					o[i] = clone(obj[i]);	//深度拷贝

				}

				else {
					o[i] = obj[i];
				}
			}		
			return o;
		}

2.实现一个简单的DOM选择器

具体描述
实现一个方法$，可以完成以下功能

（1）可以通过id获取一个DOM

例如：

	<div id="dom1"></div>
	......
	$("#dom1") // 返回一个数组，数组中唯一的元素为id为dom1的div

（2）可以通过css的class name获取一组DOM

例如：

	<div class="style1"></div>
	<p class="style1 style2"></p>
	.....
	$(".style1") // 返回一个数组，数组中两个元素，为class含有style1的div及p
	$(".style2 .style1") // 返回一个数组，数组中一个元素，为class含有style1, style2的p

实现代码

	function $(str){

		var oDom = [];
		var reBlank = /^\s+|\s+$/g;
		//去掉首尾空格
		var str1 = str.replace(reBlank, '').replace(/\#|\./g, '');
		var re1 = /^#/;
			

		if(re1.test(str)){


			oDom.push(document.getElementById(str1));

		}

		else{

			var aEle = document.getElementsByTagName("body")[0].getElementsByTagName('*');
			var re = new RegExp('\\b'+str1+'\\b', 'i');

			for (var i = 0, len = aEle.length; i < len; i++){
					
				if(re.test(aEle[i].className)){

					oDom.push(aEle[i]);

				}					

			}
				

		}

		return oDom;

	}


3.实现对DOM进行CSS样式类的设置

具体描述

1. 实现一个方法 addClass，可以为某个DOM增加样式类

例如：

	<div id="a" class="class-a"></div>
	.....
	addClass(document.getElementById("a"), "class-a class-b class-c");

执行后

	<div id="a" class="class-a class-b class-c"></div>

2. 实现一个方法 removeClass，可以为某个DOM删除样式类

	<div id="a" class="class-a class-b class-c"></div>
	removeClass(document.getElementById("a"), "class-a class-c");

执行后

	<div id="a" class="class-b"></div>

实现代码

	function removeClass(obj, sClass){

		var reBlank = /^\s+|\s+$/g;
		//去掉首尾空格
		var str = sClass.replace(reBlank, '')
		var sClassName = obj.className;
		var re;
		var aClass = str.split(" ");

		if(sClassName != null){

			for(var i = 0, len = aClass.length; i<len; i++){

				re = new RegExp(aClass[i], 'g');
				sClassName = obj.className = re.test(sClassName) ? sClassName.replace(re, '') : sClassName;

			}								

		}

	}

4.实现一个简单的router，通过url可以找到相应的Action

通过锚点来做router的输入

具体描述

通过URL中#后面的内容，去执行相应的代码

例如

URL为http://localhost/index.html#test时，执行test()

URL为http://localhost/index.html#test/a时，执行test(a)

URL为http://localhost/index.html#test/a/b时，执行test(a, b)

注意异常处理

实现代码：

	function router(url){

			var str = url.substring(url.indexOf("#")+1);
			var aStr = str.split("\/");
			var fnName = aStr.shift();
			var sArgument = aStr.join(",");

			eval(fnName+".call(null, "+sArgument+")");

		}




5.实现一个自定义事件处理对象notice，或者称为消息中心对象

具体描述：

任何对象可以通过notice.on方法注册要监听什么消息（事件），当监听到时进行响应某个方法

例如：

	notice.on("something happen", someFunction);
	notice.on("OnLine", function () {
	    sendHomeWork();
	});

任何对象通过notice.send方法可以发送任何消息（事件）
notice.send("Some Message");

实现代码：

	function Notice(){

		this.handlers ={};

		this.addListener = function(name, handler) {

    		(this.handlers[name]||(this.handlers[name]=[])).push(handler);

 		};

	} 

	Notice.prototype = {


		on: function(arg1, arg2) {
		   
			 this.addListener(arg1,arg2);			    
			    
		},

			 
		send: function(name, args) {

		 if (this.handlers[name]) {

			 var hs = this.handlers[name];
			 hs.every(function(h) {h(args)});

		  }
		}			
	}


	var notice = new Notice();
	notice.on("something", function(args){

		alert("success!");

	});

	notice.send("something");