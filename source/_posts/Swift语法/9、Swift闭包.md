title: 9、Swift闭包
date: 2017-04-9 21:27:06
tags: [Swift]
categories: [Swift]
---

## 摘要
Swift闭包
<!--more-->


## 闭包的基本语法
	
闭包本质就是一个函数，它实际上类似匿名函数。闭包中没有外部函数名，只有内部函数名。如：

	arr.sort({ (a:Int,b:Int)->Bool in return a>b})
	arr.sort({ a,b in return a>b})
	arr.sort({ a,b in a>b})
	arr.sort({ $0>$1})
	arr.sort(>)

实际上上面的写法都是对的，这说明闭包的用法非常灵活。

## 结尾闭包Trailing Closure
	
如果闭包是最后一个传入的参数时，可以把闭包从小括号中提出来。

	arr.sort(){a,b in
		return a>b
	}

如果前面没有别的参数了，可以省略括号

	arr.sort{a,b in
		return a>b
	}

## 内容捕获
	
闭包有一个特性，与数组不同，闭包可以使用外面的内容。这容易引起内存相关的问题，但是如果使用基本数据类型的话，不会产生内存泄漏的问题。如：

	var num=500
	arr.sort{ a,b in
		abs(a-num)<abs(b-num)
	}
	
在闭包中很容易使用外部的变量num，但是如果使用函数的时候它需要(Int,Int)->Int，是无法传入第三个参数的。

## 闭包和函数是引用类型
	
如果给函数定义不同的名称，它们实际指的是同一个函数。即使使用不同名称来调用函数，也相当于调用的同一个函数。
