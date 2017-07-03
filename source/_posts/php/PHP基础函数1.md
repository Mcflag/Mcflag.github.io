title: PHP基础函数1
date: 2016-04-08 11:55:00
tags: [PHP]
categories: [PHP]
---

## 摘要
PHP基础函数和使用方法。

<!--more-->

## CSS使用要点

在HTML中使用CSS，第一种是使用style标签：

	<style type="text/css">
	<!-- CSS规则 -->
	</style>

另一种引用方式是使用link标签。

	<link href="styles.css" rel="stylesheet" type="text/css" />

## HTML使用的要点

主要是HTML中指明使用的编码，否则中文容易会出现乱码。在<head>和</head>之间增加一个<meta />标签。

	<meta http-equiv="content-type" content="text/html;charset=utf-8" />

## PHP使用方式

HTML文件中的PHP代码需要包裹在<?php和?>中间。

## echo，print()，printf()，print_f()和var_dump()

1、echo可以一次输出多个值，多个值之间用逗号分隔。echo是语言结构，而并不是真正的函数，因此不能作为表达式的一部分使用。因此正确的使用方法是：

	echo "Hello","World";

语法错误：

	echo("Hello","World");//echo不能使用小括号。

2、print()打印一个值，如果字符串成功显示则返回true，否则返回false。

3、printf()该函数输出格式化的字符串。format规定字符串以及如何格式化其中的变量。例如：

	$str="Hello";
	$number=123;
	printf("%s world.Day number %u", $str, $number);

如果%符号多于arg参数，则必须使用占位符。占位符被插入%符号之后，由数字和“\$”组成。

	$number=123;
	printf("With 2 decimals: %1\$.2f <br/>With no decimals: %1\$u",$number);

4、print_r()和var_dump()：这两个函数主要用于打印数组，数组会以括起来的键值对列表形式显示。但是print_r()输出布尔值和NULL的结果没有意义，因为都是打印“\n”。因此使用var_dump()函数更适合调试。

## nl2br()函数和nl2p()

nl2br()函数在字符串中的每个新行(\n)之前插入HTML换行符(<br/>)。例如：

	<?php
		$str="Welcome to
		www.aaaaa.com";
		echo nl2br($str);
	?>

那么在运行结果的HTML代码：

	Welcome to <br/>
	www.aaaaa.com

## 设置表单form的处理php脚本

使用form的action属性来设置使用哪个脚本处理form传递的数据。method属性用于设置使用何种方式传递数据，可以是“post”或者“get”。比如：

	<form action="handle.php" method="post">
		<p>Name:<input type="text" name="name" size="20"/></p>
		<input type="submit" name="submit" value="Send My Feedback"/>
	</form>

在handle.php中接收表单的数据，如果是post方法传递的话数据存放在$_POST[]数组中，如果是get方法传递的话数据存放在$_GET[]数组中。

	$name=$_POST['name'];

使用如上的语句可以接收到前一个表单中传入的name数值。

## 开始或者关闭设置

php中使用ini_set函数进行设置，比如开启显示错误信息的设置：

	ini_set('display_errors',1);

另外可以设置调整输出哪些错误信息。(E_ALL|E_STRICT)表示最高级别的错误报告。

	error_reporting(E_ALL | E_STRICT);//E_ALL并不包含E_STRICT

错误控制运算符（@），当在一个函数名之前使用该运算符时，可以阻止该函数调用可能产生的任何错误消息或警告，该运算符不能阻止错误的发生，只是防止立即显示出错消息：

	@function_name();

## round()，ceil()，floor()和number_format()函数

round()是对浮点数进行四舍五入，例如：

	<?php
	echo round(3.4);         // 3
	echo round(3.6);         // 4
	echo round(3.6, 0);      // 4
	echo round(1.95583, 2);  // 1.96
	echo round(1241757, -3); // 1242000
	echo round(5.055, 2);    // 5.06
	?>

ceil()是进一法取整

	<?php
	echo ceil(4.3);    // 5
	echo ceil(9.999);  // 10
	?>

floor()是舍去法取整

	<?php
	echo floor(4.3);   // 4
	echo floor(9.999); // 9
	?>

number_format()格式化数字为千分位

	<?php
	$number = 1234;
	number_format($number);//1,234
	number_format($number, 2, ',', '');//1234,00
	$number = 1234.564;
	number_format($number, 2, ',', '');//1234,56
	$number = 1234.5678;
	number_format($number, 2, '.', '');//1234.57
	?>

## mt_rand()和rand()函数

mt_rand()和rand()两个函数都接受两个参数，表示随机的最小值和最大值。

	mt_rand(1,99);//在1-99中随机取值
	rand(1,99);//在1-99中随机取值

## 去除输入的HTML标签

有一个潜在的安全性问题，用户如果在表单中输入HTML字符，这将会对页面格式产生影响，或许还会引发安全方面的问题。可以使用一些PHP函数来处理HTML标签。

**htmlspecialchars()函数将特定的HTML标签转换为实体版本。**

**htmlentities()函数将所有的HTML标签转换为实体版本。**

**strip_tags()函数移除所有的HTML和PHP标签。**

## 字符串的编码和解码

urlencode()函数可以通过URL安全地将任意值传送到PHP脚本。这个函数接受一个字符串，并对之编码。
urldecode()函数可以接吗，但是实际上PHP能够自动将它获取到的大部分值进行解码。

## strtok()

strtok()函数使用一个预定的分隔符作为标记，从大字符串中创建子字符串。

	$first=strtok($_POST['name'],' ');

上面的代码表示从前一个字符串中取出空格之前所有的内容。之后还可以使用同样的函数取出后面的内容

	<?php
	$string = "Hello world. Beautiful day today.";
	$token = strtok($string, " ");
	
	while ($token !== false)
	{
	echo "$token<br>";
	$token = strtok(" ");
	}
	?>

## substr()函数

substr()函数从字符串中取出子字符串。接受的第二个参数表示从哪里开始提取，负数表示的是从结尾开始提取。

	$string='ardvbck';
	$sub=substr($string,0,3);//子字符串是ard
	$sub=substr($string,-3,2);//子字符串是bc
	$sub=substr($string,-3);//子字符串是bck

## strlen()函数和str_word_count()

strlen()函数可以计算字符串中字符的数量。str_word_count()函数可以计算单词数量。

## str_ireplace()和str_replace()函数替换字符串

str_ireplace()函数进行不区分大小写的查询和替换。

str_replace()函数进行区分大小写的查询和替换。

例子：

	$me='Larry E. Ullman';
	$me=str_ireplace('E.','Edward',$me);//现在$me值变为Larry Edward Ullman

## 字符串处理函数

ucfirst()将字符串第一个字母变成大写。

ucwords()将字符串每个单词的第一个字母变成大写。

strtoupper()将整个字符串都变为大写。

strtolower()将整个字符串都变为小写。

trim()函数取出字符串前后的空白，包括空格、换行符、制表符。

## 验证函数

empty()函数，用来检验是否一个给定的变量拥有除0和空字符串之外的其他值。当变量没有值时（或者值为0或者一个空字符串），它将返回TRUE，反之则返回FALSE。

isset()函数，当变量拥有值时（包括0、false或者空字符串）时，isset()函数返回TRUE，反之则返回FALSE。isset()主要判断只有没有定义的值时才会返回false。

is_numberic()函数只有提交的是一个有效的数字类型的值时返回true，反之返回false。正数，小数，甚至字符串如果是有效的数字都可以通过该函数验证。

## 超全局变量

$_SERVER、$_GET、$_POST、$_COOKIE、$_SESSION、$_ENV一同都被称为超全局变量。

## array_merge()函数

array_merge()函数合并两个数组。

	$new_array=array_merge($array1,$array2);

## 对数组的foreach用法

foreach可以循环遍历数组的每个元素。

	foreach($array as $key => $value) 

或者

	foreach($array as $value)

## 数组排序函数

sort()对值进行排序而键不变。

rsort()对值进行反向排序，同样键不变。

asort()对值进行排序同时保持键值之间的对应关系。

arsort()对值进行反向排序同时保持键值之间的对应关系。

ksort()对键进行排序同时保持键值之间的对应关系。

krsort()对键进行反向排序同时保持键值之间的对应关系。

shuffle()用来对数组中的元素顺序进行随机重组。

## explode()和implode()函数

explode()将引入的字符串转换为数组。

	$array=explode(' ',$_POST['words']);//该代码将$_POST['words']按照空格分解成数组。

implode()将数组转换为字符串。

	$str=implode('<br/>',$array);//将数组每一项用“<br/>”组合起来。

## 表单传递数组

实际上将name属性中设置为同样的数组型的值即可。比如name="weekdays[]"这样的形式。

	<p>Event Days:
	<input type="checkbox" name="days[]" value="Sunday" />Sun
	<input type="checkbox" name="days[]" value="Monday" />Mon
	<input type="checkbox" name="days[]" value="Tuesday" />Tue
	<input type="checkbox" name="days[]" value="Wednesday" />Wed
	<input type="checkbox" name="days[]" value="Thursday" />Thu
	<input type="checkbox" name="days[]" value="Friday" />Fri
	<input type="checkbox" name="days[]" value="Saturday" />Sat
	</p>

## include()和require()

require()语句的性能与include()相类似，都是包括并运行指定文件。不同之处在于：对include()语句来说，在执行文件时每次都要进行读取和评估；而对于require()来说，文件只处理一次（实际上，文件内容替换require()语句）。这就意味着如果可能执行多次的代码，则使用require()效率比较高。另外一方面，如果每次执行代码时是读取不同的文件，或者有通过一组文件迭代的循环，就使用include()语句。

require的使用方法如：require("myfile.php")，这个语句通常放在PHP脚本程序的最前面。PHP程序在执行前，就会先读入require()语句所引入的文件，使它变成PHP脚本文件的一部分。include使用方法和require一样如：include("myfile.php")，而这个语句一般是放在流程控制的处理区段中。PHP脚本文件在读到include()语句时，才将它包含的文件读取进来。这种方式，可以把程式执行时的流程简单化。

PHP系统在加载PHP程序时有一个伪编译过程，可使程序运行速度加快。但incluce的文档仍为解释执行。include的文件中出错了，主程序继续往下执行，require的文件出错了，主程序也停了，所以包含的文件出错对系统影响不大的话（如界面文件）就用include，否则用require。

require()和include()语句是语言结构，不是真正的函数，可以像php中其他的语言结构一样，例如echo()可以使用echo("ab")形式，也可以使用echo "abc"形式输出字符串abc。require()和include()语句也可以不加圆括号而直接加参数。

include_once()和require_once()语句也是在脚本执行期间包括运行指定文件。此行为和include()语句及require()类似，使用方法也一样。唯一区别是如果该文件中的代码已经被包括了，则不会再次包括。这两个语句应该用于在脚本执行期间，同一个文件有可能被包括超过一次的情况下，确保它只被包括一次，以避免函数重定义以及变量重新赋值等问题。

include引入文件的时候，如果碰到错误，会给出提示，并继续运行下边的代码。

require引入文件的时候，如果碰到错误，会给出提示，并停止运行下边的代码。

## 定义常量

使用define()来定义常量。例如

	define('TITLE','Article title')；

检查是否有定义的常量：

	defined('TITLE');// 如果有定义则返回true，没有则返回false

## 使用日期和时间

通过date()函数返回格式化的时间。比如date('l F j,Y')可以返回Wednesday January 26,2011。

d - 表示月里的某天（01-31）<br/>
n - 表示月（1-12）<br/>
m - 表示月（01-12）<br/>
F - 表示月（February）<br/>
M - 三个字母表示月（Feb）<br/>
j - 1或2位数字表示月份中的一天（8）<br/>
Y - 表示年（四位数 2016）<br/>
y - 表示年（二位数 16）<br/>
l(小写的L) - 表示周里的某天（Monday） <br/>
D - 三个字母表示一周中的某一天（Mon）<br/>
g - 12小时格式（1-12） <br/>
G - 24小时格式（1-24） <br/>
h - 带有首位零的12小时格式（06） <br/>
H - 带有首位零的24小时格式（01-24） <br/>
i - 带有首位零的分钟（06） <br/>
s - 带有首位零的秒（00 -59）<br/>
a - 小写的午前和午后（am 或 pm）<br/>
A - 大写的午前和午后（AM 或 PM）<br/>

time()函数返回当前时间的时间戳。

mktime(hour, minute, second, month, day, year);函数可以返回一个给定的时间和日期的时间戳。

date_default_timezone_set(timezone)函数设置时区。中国时区可以设置为“PRC”，或者“Asia/Shanghai”。

## 输出缓冲

某些函数只能在没有任何东西被发送到浏览器之前调用，这些函数包括header()、setcookie()和session_start()。如果在Web浏览器已经收到了一些文本、HTML或哪怕是一个空格之后调用这些函数，就会得到一个HTTP头已发送错误消息。可以采用输出缓冲来解决。

在一般PHP脚本中，PHP标签之外的任何HTML都会立即发送到Web浏览器，一旦执行了print语句，所有的打印内容也是如此。利用输出缓冲HTML和打印的数据将被放到缓冲中，当脚本执行结束后，缓冲将被发送到Web浏览器或者如果需要的话，缓冲可以清空而不发送到Web浏览器。

要启用输出缓冲，在页面最顶端使用ob_start()，在脚本结尾处调用ob_end_flush()函数，将积累下来的缓冲发送到Web浏览器。或者使用ob_end_clean()函数删除缓冲的数据。

## Cookie的使用

创建cookie：

	setcookie($cookie_name,$cookie_value);

读取cookie，直接使用$_COOKIE['$cookie_name']方式得到，出于安全考虑，cookie的值不会被直接打印出来，取而代之的做法是，通过htmlentities()来阻止因为用户对cookie的值进行操纵而可能造成的不良后果。

	htmlentities($_COOKIE['font_size']);

向cookie添加参数，cookie能够添加的参数多达5个：

	setcookie(name,value,expiration,path,domain,secure,httponly);

expiration指的是过期时间，time()+3600就是指1小时后国旗。

	setcookie(name,value,time()+3600);

使用path选项，可以限制仅当用户在域中user文件夹中时cookie才存在，可以使用一个单独的斜线（/）表示根目录。。

	setcookie(name,value,time()+3600,'/subfolder/');

cookie已经被指定给一个域，因此域参数可以用来限制一个子域的cookie，如www.example.com.

	setcookie(name,value,time()+3600,'','www.example.com');

参数secure的值指明一个cookie应当只能通过安全HTTPS连接传送。值1指明必须使用安全连接，值0指明安全连接并不必要。

	setcookie('cart','82ABC3021',time()+3600,'', 'shop.example.com',1);

最后一个属性（httponly）是PHP5.2版本中新添加的属性，它可以用来限制对cookie的访问（例如防止用JavaScript读取cookie），但不是所有的浏览器都支持。

删除cookie，仍然使用setcookie：

	setcookie('font_size','',time()-600,'/');

## session的使用

session就像是保存在服务器中的cookie。cookie在某些方面比session更有优势，更加易于创建和检索，对服务器造成的处理压力更少，通常情况下能够持续更长时间。
	
可以使用session_start()创建、访问或删除session。这个函数将试图在session首次启动时发送一个cookie，因此它必须在任何HTML或空白被发送至web浏览器之前调用。因此在使用session的页面中，必须在脚本的起始行调用函数session_start()。

当第一次开启session时，会产生一个随机的session ID，并且会向浏览器发送一个名为PHPSESSID的cookie。

在需要向session中存数据时可以用下面代码：

	session_start();
	$_SESSION['email']=$_POST['email'];
	$_SESSION['loggedin']=time();

删除session的话使用如下方法：

	session_start();
	unset($_SESSION);//删除session变量
	$_SESSION=array();//充值session数组

如果要在服务器上删除session数据可以使用：

	session_destroy();

## 带参数可选的函数

参数可选也就是包含默认值。如：

	function make_text_input($name, $value, $option=20){}

第三个参数$option就是可选的。

函数如果需要有返回值的话，增加一个return语句即可。

## global声明变量

global声明的意思是希望这个函数内的变量能够指向函数外具有相同名称的变量，global声明将一个拥有局部作用域的局部变量变为拥有全局作用域的全局变量。

在函数体内定义的global变量，函数体外可以使用。在函数体外定义的global变量不能再函数体内使用。

	$global $a;
	$a=123;
	function f()
	{
		echo $a; //错误,
	}
	//再看看下面一例
	function f()
	{
		global $a;
		$a=123;
	}
	f();
	echo $a; //正确,可以使用

## 文件路径

引用计算机上的任何文件或目录都有两种方式：使用绝对路径或相对路径，绝对路径从计算机的根目录开始，相对路径从当前的工作目录开始：

	fileA.txt（当前目录）
	./fileA.txt（当前目录）
	dirB/fileB.txt（dirB目录中）
	../fileC.txt（父目录中）
	../dirD/fileD.txt（同级目录中）

将两个圆点放在一起可以表示当前目录的父目录，一个单独的圆点表示当前目录自身，如果一个文件的名字由一个圆点开始，则该文件时隐藏的。

## 写入文件

写入文件最简单的方法是file_put_contents($file,$data)函数，这个函数是PHP5加入的。写入的数据可以是数组，但是只能是一维数组，不能使用多维数组。

如果文件不存在，函数会尝试创建它，如果文件存在，文件的内容会被新的数据取代，如果需要将新内容追加到文件中，加入FILE_APPEND作为第三个参数。

当文件中追加数据时，通常需要每块数据独占一行，因此每次提交时都应该以一个换行符结束，或者可以使用PHP_EOL，表示操作系统的换行符。

或者使用旧方法写入文件：首先打开文件，写入文件，关闭文件。

	$fp=fopen($file,mode);
	fwrite($fp,$data.PHP_EOL);
	fclose($fp);

文件打开的模式

	r 只读；从文件的起始位置开始读取
	r+ 读或写；从文件的起始位置开始读写
	W 只写；如果文件不存在则创建文件，并且覆盖现有内容
	W+ 读或写；如果文件不存在则创建文件，并且在写入时覆盖现有内容
	a 只写；如果文件不存在则创建文件，将新数据追加到文件末尾
	a+ 读或写，如果文件不存在则创建文件，将新数据追加到文件末尾。（写入时）
	x 只写；如果文件不存在则创建文件，如果文件存在则什么也不做（并发出一个警告）
	x+ 读或写；如果文件不存在则创建文件，如果文件存在则什么也不做（并在写入时发出一个警告）
	b 二进制 推荐使用以便获得可移植性。

is_writable($file)函数可以用来判断文件是否可写。

## 锁定文件

PHP5.1以上的版本可以直接在file_put_contents()函数中加入第三个参数LOCK_EX常量。如果要同时使用LOCK_EX和FILE_APPEND常量，可以使用二元OR运算符（|）。如：

	file_put_contents($file,$data,FILE_APPEND | LOCK_EX);

早期的PHP中则需要使用flock()函数。形如flock($file, locktype)，其中的locktype可以有如下值：

	LOCK_SH：用于读取的共享锁
	LOCK_EX：用于写入的独享锁
	LOCK_UN：释放一个锁
	LOCK_NB：非阻塞锁

要在写入时临时锁定一个文件可以使用下面的代码：

	$fp=fopen($file,'a+b');
	flock($fp,LOCK_EX);
	fwrite($fp,$data);
	flock($fp,LOCK_UN);

## 读取文件

文件中所有的内容按照一个字符串来读取：

	$data=file_get_contents($file);

如果文件每一行都有一些数据，最好使用file()函数，这里得到的$data变量就是一个数组，数组中每一项都是一行：

	$data=file($file);

## 处理文件上传

为了能够上传文件，必须对标准的HTML进行更改，首先form标签必须包含代码enctype="multipart/form-data"，让浏览器知道表单具备不同的类型。

	<form action="script.php" enctype="multipart/form-data" method="post">

其次必须添加一个特殊的隐藏输入框，它用来建议浏览器最大能够上传多大的文件

	<input type="hidden" name="MAX_FILE_SIZE" value="30000"/>

最后使用file元素创建所需的表单字段。

	<input type="file" name="picture" />

在PHP脚本中，可以引用$_FILES变量（文件领域的$_POST等价）来引用上传的文件，$_FILES包含5个元素：name——文件在用户计算机上使用的名字，type——文件的MIME类型，如image/jpg，size——文件的大小，tmp_name——文件存放在服务器上的临时文件名，error——如果发生错误则保存错误代码。

当文件上传后，服务器会将其放置在一个临时目录中，之后可以使用move_uploaded_file()函数将文件存放在其最终目标位置，第一个参数是在服务器上的临时文件名，第二个参数是移动目标的完整路径和文件名：

	move_uploaded_file($_FILES['picture']['tmp_name'],'/path/to/dest/filename');

一个上传文件的示例代码：

	<?php
	if($_SERVER['REQUEST_METHOD']=='POST'){
		if(move_uploaded_file($_FILES['the_file']['tmp_name'],"./what/{$_FILES['the_file']['name']}")){
			print '<p>Your file has been uploaded.</p>';
		}else{
			print '<p style="color:red;">Your file could not be uploaded because: ';

			switch ($_FILES['the_file']['error']) {
				case 1:
				print 'The file exceeds the upload_max_filesize setting in php.ini';
				break;
				case 2:
				print 'The file exceeds the MAX_FILE_SIZE setting in the HTML form';
				break;
				case 3:
				print 'The file was only partially uploaded';
				break;
				case 4:
				print 'No file was uploaded';
				break;
				case 6:
				print 'The temporary folder does not exist.';
				break;
				default:
				print 'Something unforeseen happened.';
				break;
			}
			print '.</p>';
		}
	}
	?>
	<form action="upload_file.php" enctype="multipart/form-data" method="post">
		<p>Upload a file using this form:</p>
		<input type="hidden" name="MAX_FILE_SIZE" value="300000"/>
		<p><input type="file" name="the_file"/></p>
		<p><input type="submit" name="submit" value="Upload This File"/></p>
	</form>

## 导航目录

要查找一个目录中的所有内容，最简单的选择是使用scandir()函数，返回一个数组，包含了从给定目录中找到的所有项。但是这个函数是PHP5中添加的，旧版的PHP需要使用opendir()，readdir()，closedir()代替。

还有filesize()和filemtime()函数用于检索文件的修改时间。还用is_dir()和is_file()函数判断是否是目录或者文件。glob()函数可以搜索名字与模式相匹配的文件目录（如*.jpg或filename*.doc）。fileperms()返回文件的权限，fileAtime()返回文件的最后访问时间，fileowner()返回拥有该文件的用户名称。basename()或dirname()函数可以用来返回完整路径字符串中的文件名或目录名。finfo_file()函数用于检测文件的MIME类型。

## 创建目录

创建目录的命令是mkdir()，格式是mkdir('directory_name', permissions)。

	mkdir($dir);
	mkdir($dir,0777);

如果需要创建多级目录，那么需要第三个参数，将第三个参数为true。

	mkdir($dir,0777,true);

如果php拥有权限，则可以使用rmdir()函数来删除现有目录。

## 增量读取文件

之前使用file()函数将整个文件读取到一个数组中，但是如果只需要读取文件的一部分，就需要使用fgets()函数。

fgets()函数返回具有指定长度的字符串，通常将该函数放置在while循环中，并用feof()函数来确保没有到达文件的结尾。例如：

	$fp=fopen($file,'rb');
	while(!feof($fp)){
		$string=fgets($fp,1024);
	}
	fclose($fp);

或者使用fgetcsv()函数，它使用给定的分隔符切分字符串，并返回一个数组。

	$array=fgetcsv($fp,length,delimiter);
	$array=fgetcsv($fp,1024);

该函数等价于将fgets()和explode()函数结合到一起使用。最后因为这些函数依赖于行尾指示符，所以最好采用额外的保护措施，即启用PHP的auto_detect_line_endings设置，可以使用如下设置：

	ini_set('auto_detect_line_endings',1);

下面的例子持续地读取文件，直到找到一组匹配的用户名和密码：

	ini_set('auto_detect_line_endings',1);
	$fp=fopen($file,'rb');
	while($line=fgetcsv($fp,200,"\t")){
		if(($line[0]==$_POST['username']) && ($line[1]==md5(trim($_POST['password'])))){
			break;
		}
	}
	fclose($fp);

## php与数据库

php仅用来将SQL语句发送到数据库应用程序，创建表、插入记录，检索到的一些记录甚至是错误，这些执行的结果会从数据库返回到PHP脚本。

PHP的mysql_query()函数将一条SQL命令发送到MySQL。比如：

	$result=mysql_query(SQL command,database connection);

使用数据库时需要建立一个数据库服务器的连接，使用如下连接方式：

	$dbc=mysql_connect(hostname,username,password);

使用完数据库之后需要关闭数据库：

	mysql_close($dbc);

## MySQL错误处理

常见的MySQL错误有：连接MySQL失败；选择数据库失败；无法运行查询；查询没有返回结果；数据没有插入到表中。使用mysql_error()函数返回关于错误的文本信息。

## 创建和选择数据库

要使用php创建数据库，可以使用mysql_query()函数结合CREATE DATABASE databasename的SQL命令：

	mysql_query('CREATE DATABASE somedb', $dbc);

当创建完数据库后，可以使用mysql_select_db()函数来选中该数据库：

	mysql_select_db('somedb', $dbc);

任何时候一个数据库只需要创建一次，但每次在其上运行查询之前都需要选中它。

## 创建表

创建新表的SQL语句如下：

	CREATE DATABASE tablename(column1 definition,column2 definition,etc...)

每一列的类型，常见的类型有TEXT，VARCHAR(变长字符串)，DATETIME和INT(整数)。

将第一个列创建为主键（primary key），因此一个简单的CREATE语句是：

	CREATE TABLE my_table(
		id INT UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
		information TEXT
	)

使用的时候就是使用mysql_query()：

	$query='CREATE TABLE entries(id INT PRIMARY KEY,title TEXT)';
	mysql_query($query,$dbc);

## 向数据库插入数据

要插入数据记录可以通过下面两种模式来使用INSERT的SQL语法：

	INSERT INTO tablename VALUES (value1,value2,value3,etc.)
	INSERT INTO tablename (column1_name,column2_name) VALUES (value1,value2)

## 安全查询语句

因为用户可以在提交的文本中包含撇号和其他查询语句作为输入，那么会破坏SQL查询。比如用户提交';DROP TABLE entries;作为输入，产生的查询为：

	INSERT INTO entries (entry_id, title) VALUES (0, '';DROP TABLE entries;')

这被称作SQL注入攻击，但是这个问题可以使用mysql_real_excape_string()函数对可能危险的字符进行转义。

## 从数据库中检索数据

依然使用mysql_query()函数，但检索数据和插入数据有所不同：必须将检索到的信息赋给一个变量，然后使用另一个函数获取数据。检索数据用SELECT查询：

	SELECT what columns FROM what table

从一个表中读取数据，最简单的查询是：

	SELECT * FROM tablename

将查询得到结果赋给一个变量，并使用：

	$result=mysql_query($query,$dbc);
	$row=mysql_fetch_array($result);

代码中$result是一个查询结果集的引用，mysql_fetch_array()函数从结果集中每次获取一行数据，并在这一个过程中创建一个数组。这个数组会使用已选择的列名作为索引。列名区分大小写。

如果查询返回多行数据，在循环中执行mysql_fetch_array()函数以获取所有行：

	while ($row = mysql_fetch_array($result)){
		// Do something with $row
	}

mysql_num_rows()函数返回由SELECT查询返回的记录行数。

## 删除数据库中的数据

从数据库中移除记录使用的SQL语法：

	DELETE FROM tablename WHERE column=value

如果只希望删除一条记录，可以为查询指定LIMIT子句：

	DELETE FROM tablename WHERE column=value LIMIT 1

仍然使用mysql_query()函数执行该查询，使用mysql_affected_rows()函数，该函数用于返回INSERT、DELETE或UPDATE查询影响到的行数。

通过使用TRUNCATE TABLE tablename可以清空一个表，TRUNCATE会完全删除并重建一个表。

## 更新数据库中的数据

更新数据库使用UPDATE，语法为

	UPDATE tablename SET column1_name=value,column2_name=value2 WHERE some_column=value

和其他查询一样，值是字符串应该将其放在单引号中。

## 常见的问题解答

**1、空白页：**如果在提交一个表单或加载一个php脚本之后，Web浏览器看到的是空白页，这可能是由于某个错误中断了页面的执行。首先检查HTML源代码，然后检查PHP是否有错误。

**2、undefined variable或undefined index错误：**检查变量或数组索引的拼写，或者在引用前对其初始化，还要确保变量带有值。

**3、变量不带有值：**可能是引用变量时拼写是否正确，确保正确使用了$_GET、$_POST、$_COOKIE和$_SESSION。如果需要的话使用print_r()函数来检查每个变量的名字和值。

**4、Call to undefined function...错误：**这种错误意味着试图使用一个在PHP中并不存在的函数，导致该错误产生的原因可能是函数名拼写错误，在调用函数前没有定义，或者使用了所在PHP版本所不支持的函数。

**5、Headers already sent错误：**该错误消息表明在Web浏览器已经接收到HTML或一个空白页之后调用了HTTP头相关的函数——header()、setcookie()、session_start()。复查在调用这些函数之前脚本中发生了什么，或使用输出缓冲来避免混乱。

**6、Access denied错误：**在使用数据库时看到了错误消息，确认使用的用户名，密码和主机可能不具备访问数据库的权限，请确认正在使用的值。

**7、Supplied argument is not a valid MySQL result resource错误：**这个消息意味着不恰当的使用了一个查询结果，则通常由于试图在一个没有返回任何记录的查询中检索行。

**8、预置的HTML表单值被截断：**必须将HTML表单中文本框控件的value属性值放置在双引号中，如果不这么做，只有第一个空格之前的部分会被设置为文本框控件的值。

**9、条件或循环语句产生非预期的行为：**检查使用的运算符，以及引用的变量确实正确。

**10、解析错误：**检查每条语句是否都以分号结尾，以及所有的引号、圆括号、方括号和花括号都是配对的。或者逐条检查语句是否正确。