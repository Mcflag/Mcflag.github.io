title: 8、Swift函数
date: 2017-04-8 21:27:06
tags: [Swift]
categories: [Swift]
---

## 摘要
Swift函数
<!--more-->


## 函数声明

	func sayHello(name:String) -> String{
		return “Hello ”+name
	}

swift中的函数有函数名，参数，返回值类型，函数体
	
参数和返回值都可以是可选型，返回值可以不写，也可以显式的表示没有返回值

	func sayHello() -> (){
		print(“Hello”)
	}
	
或者：

	func sayHello(){
		print(“Hello”)
	}

## 使用元组返回多个值

	func findMaxAndMin(numbers:[Int])->(max:Int,min:Int)?{
		guard numbers.count>0 else{
			return nil
		}
    
		var minValue=numbers[0]
		var maxValue=numbers[0]
		for number in numbers{
			minValue=minValue<number ?minValue:number
			maxValue=maxValue>number ?maxValue:number
		}

		return (maxValue,minValue)
	}

	var scores:[Int]?=[202,1234,5678,223,334,982,555]

	if let result=findMaxAndMin(scores ?? []){
		print("The max score is \(result.max)")
		print("The min score is \(result.min)")
	}

## 内部参数名，外部参数名

函数中一个参数可以有一个内部函数名，一个外部函数名。调用的时候从第二个参数开始需要加上参数名，比如：

	func sayHello (name:String, greetingWord greeting:String)->String{

	}
	
如果参数只有一个名称，如name，那么即用在函数内部，调用也用同样的名称。如果一个参数同时有两个名称，那么前一个用于外部调用，后一个用于函数内使用。如greetingWord用于调用，greeting用于函数内使用。

如果想省略外部参数名可以使用下划线，如：

	func add(num1:Int, _ num2:Int)->{	//使用下划线代替外部参数名
		return num1+num2
	}
	
调用时就可以直接不写参数名了

	add(5,6)	//而不是add(5,num2:6)

## 默认参数和可变参数
	
如果在函数定义时给参数赋了默认值，那么调用时就可以不用给该参数赋值了。

变长的参数：

	func mean(numbers:Double … )->Double{
		//将变成参数number当作一个数组来看待
	}

一个函数只能有一个变长的参数，但是不像其他的语言，swift中的变长参数不用放在最后，可以放在任意位置。

## 常量参数，变量参数，inout参数
	
函数实际上传入的参数是一个常量，因此在函数内改变参数值时会出错。
	
如果想要在函数内将参数当做一个变量，那么简单的声明时在参数前加上一个var，就可以了。但是这种时候只是传入的参数的拷贝，只能在函数内变化，函数外部的值并没有变化。因为函数是值传入。

	func swapTwoInts(var a:Int, var _ b:Int){
		(a,b)=(b,a)
	}
	var x=1
	var y=2
	swapTwoInts(x,y)	//实际上x=1，y=2

这时就需要按引用传入，在函数声明时将var替换为inout，调用函数时在参数前加上&符号

	func swapTwoInts(inout a:Int, inout _ b:Int){
		(a,b)=(b,a)
	}
	var x=1
	var y=2
	swapTwoInts(&x, &y)	//实际上x=2，y=1

在swift中数组，字典，集合都是按值传入的，所以设计函数的时候都需要注意如果想要改变传入的这些结构，需要按引用传入。

## 使用函数类型
	
函数可以当做变量，可以使用函数式编程。

	func add( a: Int, _ b: Int)->Int{
		return a+b
	}
	let addFunc=add	//这里的addFunc类型就是函数类型，类型是(Int,Int)->Int
	addFunc(3,4)	//与调用add(3,4)一样

如果返回值为空，类型中用Void体现。如：(Int ,Int)->Void

## 函数式编程

	func changeScores(inout scores:[Int],by changeScore:(Int)->Int){
		for (index,score) in scores.enumerate(){
			scores[index]=changeScore(score)
		}
	}

	func changeScore1(score:Int)->Int{
		return Int(Double(score)/150.0*100.0)
	}

	var scores1=[88,101,134,127,150]
	changeScores(&scores1,by:changeScore1)	
	
这里就是将处理方式抽出来，调用时作为参数传入，那么就可以很容易的定义更多的处理方式，而不用重写过多的相同的逻辑。

这就是map操作，它定义在数组上，要实现同样的功能，使用map如下：

	scores.map(changeScore1)

同样功能的还有filter，遍历每一个元素并进行筛选。
	
reduce遍历每一个元素，并按照定义的形式将其聚合成一个值。

## 函数作为返回值，函数的嵌套
	
比如：

	func price1(weight:Int)->Int{
		return 1*weight 
	}

	func price2(weight:Int)->Int{
		return 3*weight
	}

	func chooseAWay(weight:Int)->(Int)->Int{
		return weight<=10?price1:price2
	}

	let weight=15
	chooseAWay(15)(15)	//前一个函数调用返回一个函数，然后再次进行函数调用

在swift中，一个函数可以声明在另一个函数内部，并且只在另一个函数内部使用。
	

