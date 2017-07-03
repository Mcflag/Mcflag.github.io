title: 7、Swift字典和集合
date: 2017-04-7 21:27:06
tags: [Swift]
categories: [Swift]
---

## 摘要
Swift字典和集合
<!--more-->

7.字典和集合

## 字典Dictionary

实际上就是键值对的集合。声明方式：

	var dict=[“java”:”android”,”swift”:”iOS”,”javascript”:”HTML5”]
	var dict:[String:String]=[“java”:”android”,”swift”:”iOS”,”javascript”:”HTML5”]
	var dict:Dictionary<String,String>=[“java”:”android”,”swift”:”iOS”,”javascript”:”HTML5”]
	var empty : [String : String] = [ : ]

通过键得到值：

	dict[“swift”]	//值为iOS，但是返回的类型是可选型，因为可能为nil
	
那么稳妥地方法就是解包

	if let value=dict[“swift”]{
		print(“swift是\(value)上的语言”)
	}
	
dict.keys和dict.values是一个LazyMapCollection<Dictionary<String,String>,String>，实际上可以直接将其变成数组，使用Array(dict.keys)这样的方式。但是也可以直接用for-in循环遍历

	for key in dict.keys{
		print(key)
	}
	for (key,value) in dict{
		print(“\(key):\(value)”)
	}

字典是无序的，而且健是不能重复的，比如：

	var dict1=[1:”1”,2:”2”,3:”3”]
	var dict2=[1:”1”,3:”3”,2:”2”]
	dict1==dict2	//true，字典是无序的，且键不能重复

## 字典的操作

	var user=[“name”:”chen”,”password”:”123456”,”program”:”swift”]
	user[“program”]=“android”	//直接改
	user.updateValue(“javascript”,forKey:”program”)	//函数改，这个函数返回旧的值

增加值的时候直接按照上面的方式写一个不存在的key即可
	
删除值的时候可以用user[“program”]=nil的方式，或者：

	if let email=user.removeValueForKey(“email”){	//removeValueForKey返回的是删除的值
		print(“电子邮箱\(email) 删除成功”)
	}

## 集合Set
	
集合和数组的样子是一样的，但是它是无序的，其中的数据不能重复，声明的时候需要显示的声明类型。

	var skills: Set<String> = [“android”,”swift”,”javascript”]
	var skills: Set = [“android”,”swift”,”javascript”]

数组能够很容易的转换为集合

	var skills=Set([“android”,”swift”,”javascript”])	//转为集合后可以去重，并且变为无序

对集合的方法

	skills.first	//first在数组中取出第一个元素，但是在集合中是随机取出一个元素，因为集合无序

## 集合操作
	
### 并集 union，unionInPlace
	
现在有两个Set，命名为setA和setB。

	setA.union(setB)	//setA没有改变
	setA.unionInPlace(setB)	//setA改变了

### 交集 intersect，intersectInPlace
	
某一个集合减去相交的部分，subtract，subtractInPlace
	
亦或，相当于两个集合中只出现了一次的元素 exclusiveOr，exclusiveOrInPlace

### 上面的方法都能够传入集合或者数组，结果都是集合。

* 判断是否是子集或真子集 isSubsetOf，isStrictSubsetOf
* 判断是否是超集或真超集 isSupersetOf，isStrictSupersetOf
* 判断两个集合是否相离，没有公共元素 isDisjointWith

## 选择合适的数据结构

* 数组：有序
* 集合：无序，元素唯一，提供集合操作交并补，快速查找
* 字典：键值对

