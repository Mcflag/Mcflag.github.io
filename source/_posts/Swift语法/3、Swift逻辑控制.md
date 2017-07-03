title: 3、Swift逻辑控制
date: 2017-04-3 21:27:06
tags: [Swift]
categories: [Swift]
---

## 摘要
Swift逻辑控制
<!--more-->

## 循环结构for-in和for

	var result=1
	var base=2
	for _ in 1…10{	//下划线表示不关心循环变量，只需要循环10次
		result*=base	//求2的10次方的值
	}

另外有类似C语言的循环

	for var i = -99;i<=99;i+=1{	
		i*i
	}
	
赋初值的时候=号两侧都需要空格。i+=1不要写成i++，因为i++这种写法会被swift3废弃。

这里还有一个提示，就是C-style的写法在之后的Swift中可能会被废弃，推荐使用for-in。
	
那么这里就需要使用stride方法，该方法返回一个任意可变步长类型值的序列。

	for index in 0.stride(to: 10, by: 1) {
		print("\(index)”)
	}
	
## 循环结构while和repeat

	while condition{
		statements
	}

	repeat{
		statements
	} while condition

condition可以不用括号括起来，statement即使只有一句也需要使用花括号

	
arc4random()会生成9位的随机正整数，生成[0..x)之间的整数需要取余

使用arc4random()%x

arc4random_uniform()可以直接在括号中填范围，如arc4random_uniform(x)得到0到x-1之间的整数

## break和continue

continue执行后马上开始下一次循环，break执行后直接退出循环

## if-else-else if

	if condition1{
		statements
	}else if condition2{
		statements
	}else{
		statements
	}

	switch some{
	case value1:
		statements
	case value2:
		statements
	default:
		statements
	}

condition可以不用括号括起来，statement即使只有一句也需要使用花括号。
	
switch语句必须在case中将每一种可能性都穷举出来，或者添加default条件，不然会报错。

switch默认每一种case执行完后就会直接退出，不需要写break，而且switch的case中条件可以并列，如：

	switch rating{
	case “a”,”A”:
		print(“Great”)
	case “b”,”B”:
		print(“So-so”)
	default:
		print(“Bad”)
	}

switch可以对任意的基础类型进行判断，如区间，元组，元组中也可以使用下划线“_”来忽略某维度的值。

假如希望switch中的某个case执行完后接着执行下一条符合的case，可以在case语句中增加一个关键字：fallthrough

## 控制转移

前面了解了break，continue，fallthrough三个控制转移的关键字。

还有另一种方式，给循环或其他控制结构起名，break和continue后接名字的做法直接对起名对控制结构产生效果。

	findAnswer:for m in 1…400{
		for n in 1…400{
			if m*m*m*m-n*n==15*m*n {
				print (m,n)
				break findAnswer	//break本来只能退出当前循环，但是这么写就能退出｀外层循环了
			}
		}
	}

此外还有return和throw两种控制转移

## where和模式匹配

	let point=(3,3)
	switch point{
	case let(x,y) where x==y:
		print(“point is on x=y”)
	default:
		pint(“some”)
	}

还有另一种模式匹配的方式，将case放在if中

	if case 10…19 = age where age>=18{
		print(“You are a teenager and in a college.”)
	}

	let vector=(4,0)
	if case (let x,0)=vector where x>2 && x<5{
		print (“Vector”)
	}

将case放在for中

	for case let i in 1…100 where i % 3 == 0{
		print (i)
	}

## guard关键字

在函数中需要确保各种条件，使用guard关键字来确保条件正确。如：

	fun buy (money: Int, price:Int,capacity:Int,volume:Int){
		guard money>=price else{
			return
		}

		guard capacity>=volume else{
			return
		}

		print(“success”)
	}
