title: 20、Swift错误处理
date: 2017-04-13 21:27:06
tags: [Swift]
categories: [Swift]
---

## 摘要
Swift错误处理
<!--more-->


## 强制退出程序
	
使用assert断言来判断是否中断程序。
	
assertionFailure强行结束程序，并且输出错误信息。
	
* precondition：程序执行起来的时候到此处也真正的退出。满足的时候也会退出。
* fatalError：强制结束程序

## ErrorType和错误处理
	
抛出异常，使用throws关键字。同时需要在enum中确认新的ErrorType。

	enum MyError: ErrorType {  
		case NotExist
		case OutOfRange
	}

	func vend(number:Int) throws {
		guard (number>0) else {
			throw MyError.OutOfRange
		}
		print(number)
	}

	try vend(-1)	//需要写try关键字，或者try!表示不处理异常，try?表示出错返回nil

更多时候使用do和catch两个关键字。

	do{
		try vend(-1)
	}catch{

	}

分别处理异常也可以使用

	do{
		try vend(-1)
	}catch MyError.NotExist{
		//…
	}catch MyError.OutOfRange(let num){
		print(“Must bigger than \(num)”)
	}catch{
		//…
	}

还可以接收错误信息，而不用处理每一种异常

	do{
		try vend(-1)
	}catch let error as MyError.OutOfRange{
		print(error)
	}catch{
		//…
	}

## defer
	
错误处理的时候如果出错最后可能有一些收尾的工作要做，那么可以使用defer关键字。在函数中defer包裹的逻辑是在函数的作用域完成之后执行。表示延迟执行。
	
不论是因为出错而结束或者是正确执行而结束。
	
多次声明defer，最先声明的defer最后执行。

	func A{
		defer{
			print(“end”)
		}
	}
	
因为defer在正确和错误结束后都会执行，它并不是错误处理的方式，而是一种控制转移，需要慎重的使用。
