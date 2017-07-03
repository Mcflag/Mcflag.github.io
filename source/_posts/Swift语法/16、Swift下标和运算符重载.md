title: 16、Swift下标和运算符重载
date: 2017-04-16 21:27:06
tags: [Swift]
categories: [Swift]
---

## 摘要
Swift下标和运算符重载
<!--more-->


## 下标基础
	
使用subscript函数定义下标：

	struct Vector3{
		var x:Double=0.0
		var y:Double=0.0
		var z:Double=0.0

		subscript(index:Int)->Double?{
			switch index{
			case 0:return x
			case 1:return y
			case 2:return z
			default:return nil
			}
		}

		subscript(axis:String)->Double?{
			switch index{
			case “x”,”X”:return x
			case “y”,”Y”:return y
			case “z”,”Z”:return z
			default:return nil
			}
		}
	}
	var v=Vector3(x:1.0,y:2.0,z:3.0)
	
本来只能按照v.x这种方式调用，现在可以使用v[0]或v[“z”]这种方式调用了。但是超过下标范围就只能变为nil。

	subscript(index:Int)->Double?{
		get{
			switch index{
			case 0:return x
			case 1:return y
			case 2:return z
			default:return nil
			}
		}

		set{
			guard let newValue=newValue else{return}
			switch index{
			case 0:x=newValue
			case 1:y=newValue
			case 2:z=newValue
			default:return
			}
		}
	}
	
有了set方法之后就能够直接设置值了v[0]=100.0这种赋值方式就是正确的了

## 多维下标

	subscript(x:Int,y:Int)->Double{
		assert(x>=0 && x<100 && y>=0 && y<100,”Out of range”)	//断言，为真执行，为假退出输出错误信息
		return data[x][y]
	}
	
实际调用的时候需要使用m[1,1]这种方式，而不是数组调用的形式。
	
但是如果要支持m[1][1]这种写法的话，就可以改进一下subscript方法

	subscript(row:Int)->[Double]{
		get{
			assert(row>=0 && row<100,”Out of range”)
			return data[row]
		}

		set(vector){
			assert(vector.count==100,”Column number does not match”)
			data[row]=vector
		}
	}

## 运算符重载
	
比如需要重载”+”可以直接写

	func + (left:Vector3,right:Vector3)->Vector3{
		return Vector3(x:left.x+right.x,y:left.y+right.y,z:left.z+right.z)
	}
	func * (left:Vector3,right:Vector3)->Double{
		return left.x*right.x+left.y*right.y+left.z*right.z
	}
	
其他的运算符都可以重载。
	
对于单目运算符，是当作前缀使用，需要在func前加上prefix，比如”-”既可以作为减号，也可以作为负号。

	prefix func - (vector:Vector3)->Vector3{
		return Vector3(x:-vector.x,y:-vector.y,z:-vector.z)
	}
	
后缀的单目运算符则需要增加postfix关键字。

## 重载比较运算符

	func == (left:Vector3,right:Vector3)->Bool{
		return left.x==right.x && left.y==right.y && left.z==right.z
	}

## 自定义运算符
	
自定义的运算符需要在运算符重载方法前增加一句话，告诉编译器定义的是运算符而不是方法

	postfix operator +++{}
	postfix func +++(inout vector:Vector3)->Vector3{
		//some thing…
	}

同理，前缀按照这么声明

	prefix operator +++{}
	
双目运算符用infix声明

	infix operator ^{ associativity left precedence 140}	//左结合，优先级140

对于双目运算符，定义后面的花括号中还可以写上特性，比如结合性associativity，结合性用于连接使用的时候。还可以定义优先级precedence。优先级为140是加法的优先级。
	
如果不定义，结合性默认为None，优先级默认为100。
