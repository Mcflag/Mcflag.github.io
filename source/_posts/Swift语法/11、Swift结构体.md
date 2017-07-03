title: 11、Swift结构体
date: 2017-04-13 21:27:06
tags: [Swift]
categories: [Swift]
---

## 摘要
Swift结构体
<!--more-->

## 结构体定义
	
使用关键字struct定义结构体，比如：

	struct Location{
		let latitude:Double
		let longitude:Double
	}

	let appleLocation=Location(latitude:37.3230,longitude:-122.0322)

结构体也可以嵌套使用

## 构造函数
	
结构体的初始化的时候属性需要按照定义的顺序来赋初值。结构体中的构造函数是init()。

	struct Location{
		let latitude:Double
		let longitude:Double

		init(_ x:Double,_ y:Double){
			self.latitude=x
			self.longitude=y
		}
	}

结构体里的可选型类型的属性默认为nil，但是其他类型都不会设置默认值，如果没有赋值那么就会处于未初始化的状态，如果使用就会报错。

## 可失败的构造函数
	
使用Failable-Initializer，就是在init后加上？，可能会初始化失败。那么初始化的时候就用解包的方式来初始化。

	init?(number:Int){
		guard let number>0,let number<1000 else{
			return nil
		}
		self.number=number
	}

## 结构和枚举中定义方法
	
方法跟函数一样的定义方式，只是使用的时候将结构体实例化了之后，直接使用”.”加方法名的方式调用。

## 结构体和枚举是值类型
	
复制的时候是拷贝。

## 基本类型几乎都是结构体
	
Int、Float、Double、Bool、String、Array、Set、Dictionary都是结构体。

## mutating方法
	
结构中的方法如果要修改自身的时候，用普通的func不能实现，需要在方法前加上mutating关键字。

	mutating func change(){
		self.x += 1
		self.y -= 1
	}
