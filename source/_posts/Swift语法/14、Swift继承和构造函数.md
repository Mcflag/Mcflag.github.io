title: 14、Swift继承和构造函数
date: 2017-04-13 21:27:06
tags: [Swift]
categories: [Swift]
---

## 摘要
Swift继承和构造函数
<!--more-->


## 继承
	
继承的写法就是使用”:”来表示，如：

	class A{
	}
	class B:A{
	}
	
这样就表示B继承A，A是B的父类，继承之后，子类能够使用父类的属性和方法。

在class前面增加final表示不希望该类被继承。也就是说该类不能有子类了。

	final class C:User{

	}
	
C类是不能够被其他类继承的。

## 多态性
	
swift在多态性上的表现跟其他语言中是一样的，把不同的子类对象都当作父类来看，可以屏蔽不同子类对象之间的差异。

## 重载
	
子类想要重新定义父类的方法，可以使用override关键字。

	override var description: String{
		return “name is \(name)”
	}

## 两段式构造
	
父类的属性必须用父类的构造函数初始化。通过super关键字调用。称为两段式构造，按我的理解第一阶段是上溯至父类，调用父类的构造函数初始化，第二阶段就是下行，将自己的属性初始化完成。

	class A{
		var name:String
		var password:String
		init(name:String,password:String){
			self.name=name
			self.password=password
		}
	}
	class B:A{
		var email:String
		init(name:String,password:String,email:String){
			self.email=email
			super.init(name:name,password:password)
		}
	}

## 便利构造函数和指定构造函数
	
构造函数有默认参数，构造函数可以被重载的。

	
调用自己的构造函数的构造函数要写self.init()，而且该init外面需要加上convenience。

	init(){
		//……
	}
	convenience init(){
		self.init()
	}

默认情况下，所有的构造方法都是指定构造函数Designated。只有便利构造函数可以调用self.init()，便利构造函数不能被重写或者super。

## 构造函数的继承
	
* 如果子类没有实现任何父类的指定构造函数，则自动继承父类的所有指定构造函数。
* 如果子类实现类父类所有的指定构造函数，则自动继承父类的所有便利构造函数。

## required构造函数
	
如果希望子类必须实现的构造函数就定义为required，子类中实现的required构造函数不用增加override，只需要required关键字即可。
