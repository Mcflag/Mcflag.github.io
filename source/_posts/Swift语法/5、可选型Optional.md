title: 5、可选型Optional
date: 2017-04-5 21:27:06
tags: [Swift]
categories: [Swift]
---

## 摘要
可选型Optional
<!--more-->


## 可选型和解包
	
如果某个变量为空，可以赋为nil，这里的nil表示“没有”，“空值”，这个nil是一个特殊的单独的类型。
	
可选型其实是一个枚举类型，比如Optional<Int>表示值要么是Int，要么是nil。
	
?表示是一个可选型

	var age:Int?=nil
	var height:Int?=180
	var height:Optional<Int>=Optional<Int>(180)	//这句与上面一句等价

使用可选型的时候需要解包，简单的方法是在后面加上“!”号，表示强制解包。但是如果变量是nil的时候仍然会出现问题。所以就需要判断语句来判断是否为空

	if errorcode != nil{
		“The errorcode is ”+ errorcode!
	}else{
		“No error”
	}
	
另外可以使用if let方式

	if let errorcode = errorcode{
		“The errorcode is ”+ errorcode
	}else{
		“No error”
	}

如果有多个可选型需要解包，可以一次性解包多个变量：

	if let x=errorcode,y=errormessage{
		“errorcode is ”+x+”\n errormessage is ”+y
	}

或者将解包和where匹配放在一起执行

	if let errorcode=errorcode,errormessage=errormessage where errorcode==“404”{
		print(“Page not found”)
	}

## Optional Chaining和Nil-Coalesce

optional chaining是调用属性，方法等的过程，它的特性体现在用于请求和调用的目标可能为nil，如果目标有值调用成功，如果目标为nil，调用失败。多次请求或调用可以被链接在一起，如果任何一个节点失败都将导致整个链的失败。

	var errorMessage:String? = nil
	var upperString=errorMessage?.uppercaseString

对于不期望某个optional值为nil时使用??为nil做赋值保险操作，符号后的返回值为非optional。如：

	let message=errorMessage ?? “No error”	//双问号前后都需要空格

## 更多实际使用
	
元组既可以整体被设为可选型，也可以其中某个分量被设为可选型。
	
还有比如将String转为Int时返回的就是一个可选型，

	var ageInput:String=“16”
	var age=Int(ageInput)	//如果input的不是数字转换不正确，那么age就是nil，所以age本身是一个可选型

## 隐式可选型

在声明的时候不使用?而使用!，如：

	var errorMessage:String!=nil
	
那么在使用时就不用再加!了，而直接使用变量

	“The message is ”+errorMessage	//这句就不会报错了
	
但是隐式可选型可能会出错，它经常用在类的定义中。

