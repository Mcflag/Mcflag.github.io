title: 10、Swift枚举
date: 2017-04-13 21:27:06
tags: [Swift]
categories: [Swift]
---

## 摘要
Swift字符串
<!--more-->


## 枚举的声明和定义
	
枚举定义的是一个新的数据类型。

	enum Season{
		case Spring
		case Summer
		case Autumn
		case Winter
	}
	
或者简单的写成一行

	enum Season{
		case Spring,Summer,Autumn,Winter
	}
	
使用的时候可以如此使用

	let sea=Season.Spring
	let sea:Season=.Spring

如上两种方式都可以正确调用，在明确知道是枚举类型的时候，枚举类型的名称可以省略，但是点“.”不能够省略。

## 原始值Raw Value

	enum Season:Int{
		case Spring=1
		case Summer=2
		case Autumn=3
		case Winter=4
	}
	
这样就能把枚举值与原始值关联起来，这里定义的值是不能变化的。

## 关联值Associate Value
	
还可以将每一个枚举变量关联到变量。甚至是不一样的类型的变量。

	enum Status{
		case Success(Int)
		case Error(String)
	}
	
	let a=Status.Success(100)	//关联的数值就是100
	
关联的内容实际是一个元组，因此可以关联有多个分量。

## 可选型
	
可选型实际是一个枚举类型，也就是说可选型的完整样式是这样的：

	var age:Optional<Int>=Optional.Some(44)
	age=.None	//这就是nil

而我们使用的时候是使用的简单的样式

	var age:Int?=44
	age=nil

解包的过程也是同理

	switch age{
		case .None:
			//……
		case let .Some(age):
			//……
	}
	
简单的对可选型的解包为

	if let age=age{
		//……
	}else{
		//……
	}

## 枚举递归
	
递归定义的枚举类型在enum前面需要增加一个关键字：indirect，也可以将indirect写在包含递归的case前面

	indirect enum Object{
		case Number
		case Add(Object,Object)
	}

## mutating方法
	
mutating方法表示可以修改枚举量自身的方法。比如：

	enum Switch{
		case On
		case Off

		mutating func click(){
			switch self{
			case On:
				self = .Off
			case Off:
				self = .On
			}
		}
	}

	var button = Switch.Off
	button.click()

