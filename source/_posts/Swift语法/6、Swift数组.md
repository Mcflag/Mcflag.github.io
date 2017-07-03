title: 6、Swift数组
date: 2017-04-6 21:27:06
tags: [Swift]
categories: [Swift]
---

## 摘要
Swift数组
<!--more-->

## 数组初始化
	
数组里面可以放不同类型的值，但是最好使用同类型的值存放在数组中。声明数组有多种办法

	var numbers=[0,1,2,3,4,”5”]

	var emptyArray1:[Int]=[]
	var emptyArray2:Array<Int> = []
	var emptyArray3=[Int]()
	var emptyArray4=Array<Int>()

也可以在声明的时候初始化

	var allZeros=Array(count:5,repeatedValue:0)	//声明全0的数组，数组有5个值

## 数组的基本用法
	
数组基本用法与其他语言中的方法类似，按下标来得到某个索引的值，但是索引可以是一个区间，如：

	numbers[2..<4]
	
数组遍历

	for index in 0..<numbers.count{
		numbers[index]
	}

	for number in numbers{
		print(number)
	}

	for (i,num) in numbers.enumerate(){
		print(“\(i+1):\(num)”)	//i是索引，num是值
	}

数组的比较，其他语言中数组是引用，即使值相同也不相等，但是swift中“==”比较的是值，而且数组是有序的集合，因此数组是可以比较并且能够相同的。

	var numbers=[1,2,3,4,5]
	var numbers2=[1,2,3,4,5]
	numbers==numbers2	//true

## 数组的更多操作
	
### 数组的增，删，改
	
向数组中添加元素，使用append函数，传入一个元素值，也可以使用+=，但是+=后面需要接一个数组。

	var numbers=[1,2,3,4,5,6]
	numbers.append(7)
	numbers+=[8]
	numbers.insert(9,atIndex:5)	//numbers变为[1,2,3,4,5,9,6,7,8]，atIndex小心数组越界

	numbers.removeLast()	//删除最后一个元素
	numbers.removeFirst()	//删除第一个元素
	numbers.removeAtIndex(5)	//删除index为5的元素
	numbers.removeRange(0..<4)	//删除范围内的元素
	numbers.removeAll()		//删除全部元素

	numbers[0]=10	//修改某个index的元素
	numbers[1…3]=[2]	//修改区间时并不用考虑修改的个数，而是直接将原数组中的范围替换

## 二维数组
	
实际是一维数组，其中每个元素还是一个一维数组

	var board:Array<Array<Int>> = [[1,2,3,4],[5,6,7],[8,9,0]]

## NSArray
	
NSArray是OC中的数组

	var array=[1,2,3,4,5] as NSArray
	
重要的区别在于NSArray是一个类，而swift中的Array是一个结构。NSArray是采用引用的方式传递的，Array是采用值的方式传递的。
	

