title: 18、Swift协议Protocol
date: 2017-04-18 21:27:06
tags: [Swift]
categories: [Swift]
---

## 摘要
Swift协议Protocol
<!--more-->


## 协议的基础
	
协议使用关键字Protocol定义。它实际上跟java中的interface接口类似，只需要声明包含有函数而不需要实现。如：

	protocol Pet{
		var name:String{get set}
		var birthPlace:String{get}

		func playWith()
		func fed(food:String)
	}

协议本身也可以当做一个类型来看待的。
	
协议的属性必须是var不能设置为常亮。使用协议必须用另一个类或者结构体来实现。协议规定的部分必须要实现。

	struct Dog:Pet{
		var name:String=“Puppy”
		let birthPlace:String=“WH”
	}
	
或者

	class Cat:Pet{
	}

为了规定协议必须被某种类型来实现，可以这么写：

	protocol Pet:class{
	}

表示Pet协议必须被class实现。

## 协议和构造函数
	
既有继承又有实现协议的时候，继承的父类名称必须写在冒号后的第一个。因为swift是单继承的。

协议规定了init()方法，那么实现协议的类中也必须写同样的init()方法，并且前面需要加上required关键字。

## 为何使用协议
	
很多时候并不能通过继承方式来描述所有的情况，将某些共性抽象出来作为协议，由各个类来实现，与继承结合在一起能够描述的更好。

## 类型别名(type alias)和关联类型(associatedtype)

	typealias Length=Double
	extension Double{
		var km:Length{return self*1000.0}
	}
	
实际上与不用别名时一样，但是更加明确

	extension Double{
		var km:Double{return self*1000.0}
	}

associatedtype关联类型其实就是typealias一样起别名的作用，只是如果在协议中必须使用associatedtype

	protocol Calculable{
		associatedtype WeightType
		var weight:WeightType {get}
	}

## 标准库中的常用协议

* Equatable：相等。必须重载“==”运算符，自动实现了“!=”
* Comparable：可比较，必须重载“<”运算符，自动实现其他了比较运算符。
* CustomStringConvertible：类似toString方法。swift中接受了这个协议必须实现一个description属性。
* BooleanType：可以被视作布尔型，必须覆盖一个boolValue属性。

如：

	extension Int:BooleanType{
		public var boolValue:Bool{
			return self != 0
		}
	}
	var wins=0
	if !wins{
		print(“Great”)
	}
	
实现了直接将Int视为Bool类型的功能，为0返回false，不为0返回true
