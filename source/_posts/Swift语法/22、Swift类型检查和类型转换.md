title: 22、Swift类型检查和类型转换
date: 2017-04-13 21:27:06
tags: [Swift]
categories: [Swift]
---

## 摘要
Swift类型检查和类型转换
<!--more-->


## 类型检查is
	
使用is关键字来判断是否是某个类型，如：

	if a is Int {
		...
	}else if a is String{
		...
	}

## 类型转换as
	
使用as关键字可以尝试进行类型转换。as?表示尝试转换，as!表示强制转换。

	class Num{
	}
	class SmallNum:Num{
	}
	var num=Num()
	var small=SmallNum()

	num as SmallNum	//报错
	small as Num	//正确，子类转为父类才能成功

## 检查协议
	
is后可以接协议名称，表示判断是否遵守协议。
	
用as接协议表示尝试转换成遵守该协议的类型。

## NSObject，AnyObject，Any
	
* NSObject是所有OC语言中的类型的父类。
* AnyObject是任何一个任何对象类型。范围比NSObject还要广。函数不是AnyObject。
* Any可以包含函数，范围比AnyObject更广。
