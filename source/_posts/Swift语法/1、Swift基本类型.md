title: 1、Swift基本类型
date: 2017-04-13 21:27:06
tags: [Swift]
categories: [Swift]
---

## 摘要
Swift基本类型
<!--more-->

## 常量变量和声明

### 常量 let

	let maxNum=1000

### 变量 var

变量必须在声明和初始化之后才能使用。

	var index=2

### 声明

	let website:String=“www.ccooy.com”

## 常用类型

### Int整型

Int和Uint来表示整型，Uint无符号，Int有符号。还存在Int8和UInt8，Int16和UInt16，Int32和UInt32，Int64和UInt64。来表示多种长度的Int。

* 十进制：let decimalInt:Int=17
* 二进制：let binaryInt:Int=0b10001
* 八进制：let octalInt:Int=0o21
* 十六进制：let hexInt:Int=0x11

可以使用下划线“_”来分割数字：let bignum=1_000_000，仍然是1000000的Int

### Float，Double，CGFloat浮点数

整型，浮点型计算需要强制类型转换。

CGFloat多用在图形UI中，比如UIColor()里传入的RBG色值必须是CGFloat的类型

### Boolean布尔值
	
只有true和false两种

### String字符串
	
只能使用双引号

### Tuple元组

将多个不同的值集合成一个数据：可以用任意多个值，不同的值可以是不同类型

	var point=(5,2)
	var point2:(Int,Int)=(x:10,y:2)
	var httpResponse=(404,”Not found”)

使用元组中的数据，可以按point.0和point.1来用，也可以按照point2.x和point2.y来使用，还可以按照

	var (statusCode,statusMessage)=httpResponse

然后分别使用两个变量。

PS.元组可以被用来交换两个变量的值

	var a=1,b=2
	(a,b)=(b,a)	//这时a=2，b=1

### Tips

使用下划线”_”来忽略一些值，这种写法经常使用

	let loginResult=(true,”word”)
	let ( isLoginSuccess , _ )=loginResult
	if isLoginSuccess{
		print(“Login success!”)
	}

## Print方法

print可以设置间隔符和结束符：separator和terminator

	print(1,2,3,separator:” — ”,terminator:”:)”)
	
输出结果：1 — 2 — 3:)

字符串插值：使用\()的格式，括号中写变量名就可以在字符串中直接使用变量的值。如：

	let x=1,y=2
	print(x,”*”,y,”=”,x*y)
	print(“\(x)*\(y)=\(x*y)”)

这两句输出是一样的

## 注释

使用//或者/* */来表示单行和多行注释。特殊的是多行注释中可以嵌套多行注释。

	/*  12345 /* ～～～～ */ 67890 */ 这样注释是可以的




