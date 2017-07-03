title: 12、Swift类
date: 2017-04-13 21:27:06
tags: [Swift]
categories: [Swift]
---

## 摘要
Swift类
<!--more-->


## 类的定义和声明
	
类的定义跟结构的定义一致，只不过使用关键字class。类中可以声明属性，方法。

	class Person{
		var firstName:String
		var lastName:String
		var career:String?

		init(firstName:String,lastName:String,career:String){
			self.firstName=firstName
			self.lastName=lastName
			self.career=career
		}

		init(firstName:String,lastName:String){
			self.firstName=firstName
			self.lastName=lastName
		}

		func fullName()->String{
			return firstName+” ”+lastName
		}
	}

类是引用类型。如果类的实例是常量，但是属性有变量的时候，实例仍然能够改变变量属性的值。但是结构不可以。

## 类的等价
	
“==”不能用于类的比较，只能用于值类型的等价判断。
	
但是可以使用“===”来判断引用类型是否相等。实际上是判断两个类是否指向同一个内存空间。“!==”判断不等。

## 类和结构体的使用选择
	
描述值的时候用结构体，描述物体的时候用类。
	
结构体是值类型，类是引用类型。
	
可以被继承的是类。
