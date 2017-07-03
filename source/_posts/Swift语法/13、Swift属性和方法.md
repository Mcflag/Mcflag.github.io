title: 13、Swift属性和方法
date: 2017-04-13 21:27:06
tags: [Swift]
categories: [Swift]
---

## 摘要
Swift属性和方法，这一节的内容适用与结构体和类。
<!--more-->
	

## 计算属性
	
依赖其他方法存在的属性就叫计算属性。如：
	
	struct Point{
		var x=0.0
		var y=0.0
	}

	struct Size{
		var width=0.0
		var height=0.0
	}

	class Rect{
		var origin=Point()
		var size=Size()

		//这里的center就是计算属性，它可以自动改变，必须显示声明类型
		var center:Point{

			//get方法是获取时调用的方法
			get{
				let centerX=origin.x+size.width/2
				let centerY=origin.y+size.height/2
				return Point(x:centerX,y:centerY)
			}

			//set方法是设置时调用的方法。
			set(newCenter){
				origin.x=newCenter.x-size.width/2
				origin.y=newCenter.y-size.height/2
			}
		}

		var area:Double{
			get{
				return size.width*size.height
			}
		}

		init(origin:Point,size:Size){
			self.origin=origin
			self.size=size
		}
	}

	var rect=Rect(origin:Point(),size:Size(width:10,height:5))
	rect.center

	rect.center=Point()
	rect	//更改了计算属性，其他的属性也按照set方法进行了更改。

## 类型属性
	
类型Int.max和Int.min这样的属性是定义在类型上的，就需要使用类型属性。方法是定义static属性。

	class Player{
		static var highScore
	}
	
使用的时候就得显示的写明类名如：Player.highScore

## 类型方法
	
定义在类型上的静态方法。同样在func前面加上static。

	class MyArray{
		static func defaultArray (num:Int)->[Int]?{
			guard num>0 else{
				return nil
			}
			var num=num
			var arr:[Int]=[]
			while num>0 {
				arr.append(0)
				num-=1
			}
			return arr
		}
	}

	if let myArray=MyArray.defaultArray(10){
		print(myArray)
	}

## 属性观察器

	class LightBulb{
		static let maxCurrent=30
		var current=0{
			willSet(newValue){
				print(“Will set an new value \(newValue)”)
			}

			didSet(oldCurrent){
				if current==LightBulb.maxCurrent{
					print(“Pay attention”)
				}else if current>LightBulb.maxCurrent{
					print(“It is too high,go back to old number”)
					current=oldCurrent
				}
				print(“The current is \(current)”)
			}
		}
	}
	
willSet是属性值改变之前调用的方法，didSet是属性值改变之后调用的方法。实际上newValue和oldValue是默认值。
	
有一种情况下，假如类或者结构中的属性在初始化时没有初值，但是使用的时候是有值的，也就是说它的值是通过其他的属性观察器或者其他方式得到的，那么有一段时间是nil，可以在属性的类型后面写一个!，表示可以为nil。如：

	class CA{
		var some:Int!
	}

重要：didSet和willSet不会在初始化阶段调用。
	
所以实际上还是需要在初始化的方法中将可以为空的值设置初值，只不过不用在初始化方法中向其传递参数了。

## 延迟属性
	
表示一种懒加载的思想。希望属性在用的时候就计算一次，然后将值记住。

	class Range{
		let start:Int
		let end:Int

		lazy var sum:Int={
			var res=0
			for i in self.start…self.end{
				res+=i
			}
			return res
		}()

		init?(start:Int,end:Int){
			guard start<end else {
				return nil
			}

			self.start=start
			self.end=end
		}
	}
	
使用lazy关键字表示懒加载。

## 访问控制
	
* public：可以被模块外访问
* internal：可以被本模块访问
* private：可以被本文件访问

未声明时默认未internal。

## 单例模式
	
同时只能有一个实例的时候就需要使用单例模式。swift中的单例可以这么写：

	public class Manager{
		public static let defaultManager=Manager()
		private init(){}
	}

使用时直接Manager.defaultManager即可。
