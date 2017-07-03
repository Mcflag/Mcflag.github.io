title: 2、Swift运算符
date: 2017-04-13 21:27:06
tags: [Swift]
categories: [Swift]
---

## 摘要
Swift运算符
<!--more-->

## 基础运算符
	
＋、－、＊、／、％运算符，单目运算符－、＋、＋、－－。

## 比较运算符
	
==、!=、>、>=、<、<=，引用变量的比较运算符：===、!==

## 逻辑运算符
	
非：!，与：&&，或：||。

## 三目运算符
	
	question ? result1 : result2

**经过测试，在三目运算符的问号前面需要有一个空格**

##　区间运算符
	
* 闭区间运算符：[a,b]或者a…b	
* 前闭后开区间：[a,b)或者a..<b

## for-in
	
在一个区间中进行循环，如：

	for index in 1…10{
		index	//index值为1到10，而且里面的index是一个常量，不能被改变
	}
	
还有前闭后开区间

	let courses=[1,2,3,4,5,6,7,8,9,0]
	for i in 0..<courses.count{
		print(courses[i])	//打印数组中的每个值
	}
	
