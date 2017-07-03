title: 4、Swift字符串
date: 2017-04-4 21:27:06
tags: [Swift]
categories: [Swift]
---


## 摘要
Swift字符串
<!--more-->


## 字符串基础
	
字符串声明：
	let str:String=“Hello”
	let str2=String(“Hello”)

字符串连接同样适用“＋”。
	
字符串插值：
 
	let age=18
	let s=“My age is \(age)”	//输出My age is 18

“\”斜杠可以转义字符

## Character和Unicode

Character是字符的意思，它与String不是同样的类型，并且swift中没有自动类型转换，因此连接字符和字符串的时候需要强制转换

在swift中哪怕是中文和表情也是一个字符，因为swift是使用的unicode字符集。

## String.index和Range
	
Swift中的String不能直接类似数组使用方括号和数字的方式来得到某一个字符。只能通过String.index的方式。本质原因在于字符串的方括号中必须使用索引这种类型，而不是整数Int类型。如：

	var str=“Hello, Swift”
	let startIndex=str.startIndex
	str[startIndex]	//得到H
	
字符索引有：

	let startIndex=str.startIndex
	let endIndex=str.endIndex
	startIndex.advancedBy(5)	//startIndex后面的第5个索引
	startIndex.successor()	//起点索引的后一个索引
	endIndex.predecessor()	//终点索引的前一个

但是如果超出了边界，比如想得到起点的前一个或者终点的后一个就会报错。

Range就是一个区间范围，如：

	let range=str.startIndex..<str.startIndex.advancedBy(5)

String中有很多的方法可以直接使用。

## String格式化和NSString
	
String现在有了格式化方法，比如：

	let f=123.322323
	let s:String =String(format:”%.2f”, f)	//保留两位小数

使用NSString

	let s2=NSString(format:”one third is %.2f”,1.0/3.0) as String	//这里的s2就是一个String类型了

如果要去除空格的话可以使用trim()函数，

	let result=“  abc   ”.trim()

但是可以使用NSString中的一个方法，这个方法更加强大一些，如：

	let str=“   - - - -  Hello - - - - - -       ” as NSString
	str.stringByTrimmingCharactersInSet( NSCharacterSet(charactersInString: “ -“) )
	
这个方法就是把NSCharacterSet字符集中的字符从字符串中去掉的方法，可以自己定制去掉的字符。


	
	
