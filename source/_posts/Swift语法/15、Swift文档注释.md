title: 15、Swift文档注释
date: 2017-04-15 21:27:06
tags: [Swift]
categories: [Swift]
---

## 摘要
Swift文档注释
<!--more-->


## 文档语法
	
* 多行注释：/** content */
* 单行注释：///
* 无序列表：“- ”横线和空格
* 有序列表：“1.”类似数字加点
* 代码：使用“```”包裹，&#96;&#96;&#96;代码&#96;&#96;&#96;

swift文档支持markdown语法。
	
* 标题：#,##表示不同等级的标题，一个#最大
* 下划线：用“=”或“-”包围
* 斜体：用“*”或“&#95;”包围，&#95;this&#95;
* 粗体：用”&#42;&#42;”或”&#95;&#95;”（两个下划线）包围，&#42;&#42;this&#42;&#42;
* 网址：&#91;显示的内容&#93;&#40;跳转的链接&#41;
* 图片：&#33;&#91;图片的文字信息&#93;&#40;图片地址&#41;

## Parameters,Returns,Throws
	
一般函数的注释可以对参数，返回值，抛出错误进行说明。

	/// There are a few keywords Xcode can recognize automatically. The format is like **- <Keyword>**:. The most common use one is **Parameter**, **Throws** and **Returns**.
	/// - Parameter item1: This is item1
	/// - Parameter item2: This is item2
	/// - Throws: `MyError.BothNilError` if both item1 and item2 are nil.
	/// - Returns: the result string.
	func showKeywordsCommentsWithItem1(item1: AnyObject?, item2: AnyObject?) throws -> String {
		if item1 == nil && item2 == nil{
			throw MyError.BothNilError
		}
		let text = "There are a few keywords Xcode can recognize automatically."
		return text
	}

	/// There are a few keywords Xcode can recognize automatically. The format is like **- <Keyword>**:. The most common use one is **Parameter**, **Throws** and **Returns**.
	///
	/// - Parameters:
	///   - item1: This is item1
	///   - item2: This is item2
	///
	/// - Throws: `MyError.BothNilError` if both item1 and item2 are nil.
	///
	/// - Returns: the result string.
	func showKeywordsCommentsWithItem2(item1: AnyObject?, item2: AnyObject?) throws -> String {
		if item1 == nil && item2 == nil{
			throw MyError.BothNilError
		}
		let text = "There are a few keywords Xcode can recognize automatically."
		return text
	}
	
针对第二段的说明，Parameters如果有多个并且聚合起来写，那么类似item1这种，前面必须有两个空格。也就是两个空格，横线，空格总共4个字符。

## 其他文档关键字
	
* Precondition：前置条件
* Postcondition：后置条件
* Requires：需要的内容
* Invariant：循环不变量
* Complexity：复杂度
* Important：重要信息
* Warning：警告信息
* Attention：同样是警告信息
* Note：记录
* Remark：评论与Note差不多

* Author：作者
* Authors：合作者
* Copyright：版权
* Date：日期
* Since：起始时间
* Version：版本

这些内容都在description中。

## MARK，TODO和FIXME
	
注释中显示一行空行

	//MARK: -
	
显示名称

	//MARK: - somename

	//TODO:相当于可以在类视图中可以方便定位，以及提醒自己。
	//FIXME:跟TODO是类似的效果。

