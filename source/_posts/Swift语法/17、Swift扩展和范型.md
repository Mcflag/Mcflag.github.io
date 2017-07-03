title: 17、Swift扩展和范型
date: 2017-04-13 21:27:06
tags: [Swift]
categories: [Swift]
---

## 摘要
Swift扩展和范型
<!--more-->


## 扩展的基础
	
使用extension关键字对某个类进行扩展，特别适合对于第三方的库进行扩展的操作。

	class A{

	}
	extension A{
		func blah(){

		}
	}
	var a=A()
	a.blah()	//可以使用

扩展不能扩展存储型的属性，只能扩展计算型属性。
	
扩展中增加的构造函数必须使用convenience构建便利构造函数，必须调用self.init()的方法。

## 嵌套类型
	
可以在支持的类型中定义嵌套的枚举，类和结构体。

	class UI{
		enum Theme{
			case DayMode
			case NightMode
		}
	}

typealias关键字是起别名的作用。
	
使用的时候就可以UI.Theme.DayMode来使用。在swift中已经有类似的定义，String.index

## 扩展标准库

	extension Int{
		var square:Int{
			return self*self
		}
	}
	
简单的就能够给标准库进行扩展了。

## 泛型
	
Generic表示泛型，使用<>尖括号包起来类型，跟java一样也可以使用T表示通用类型。

	func swapThings<T>(inout _ a:T,inout _ b:T){
		(a,b)=(b,a)
	}

## 泛型类型
	
类似Array<Int>这样的就是表示泛型。下面看一个设计栈的例子

	struct Stack<T>{
		var items=[T]()
		func isEmpty()->Bool{
			return items.count==0
		}

		mutating func push(item:T){
			items.append(item)
		}

		mutating func pop->T?{
			guard !self.isEmpty() else{
				return nil
			}

			return items.removeLast()
		}
	}

	extension Stack{
		func top()->T?{
			return items.last
		}
	}

甚至也可以构造包含两个或两个以上的泛型的类型。如

	struct Pair<T1,T2>{
	}
