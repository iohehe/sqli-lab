---
description: 登陆框-post注入
---

# lesson11-lesson16

## 登陆框

登陆框是web的常见身份验证方式， 大体操作流程是输账户密码-&gt;拼接查库-&gt;验证登陆。

这里和之前的get的sql注入不同了，他除了可以跑库，还有一个身份验证绕过功能。俗称万能密码。所谓万能，就是不依据真是验证，构造逻辑真，从而绕过验证。登陆也是一般ctf此类问题的主要目的。

## lesson11

* input: 

```php
if(isset($_POST['uname']) && isset($_POST['passwd']))
{
	$uname=$_POST['uname'];
    $passwd=$_POST['passwd'];
```

这里传参post方法。

* sink

```php
	// connectivity 
	@$sql="SELECT username, password FROM users WHERE username='$uname' and password='$passwd' LIMIT 0,1";
	$result=mysql_query($sql);
	$row = mysql_fetch_array($result);
```

* 登陆框使用了单引号闭合。
* 查询逻辑是双查选定第一个。判定条件是根据$row，如果有则成功。



* output:

```php
	if($row)
	{
  		//echo '<font color= "#0000ff">';	
  		
  		echo "<br>";
		echo '<font color= "#FFFF00" font size = 4>';
		//echo " You Have successfully logged in " ;
		echo '<font size="3" color="#0000ff">';	
		echo "<br>";
		echo 'Your Login name:'. $row['username'];
		echo "<br>";
		echo 'Your Password:' .$row['password'];
		echo "<br>";
		echo "</font>";
		echo "<br>";
		echo "<br>";
		echo '<img src="../images/flag.jpg"   />';	
		
  		echo "</font>";
  	}
	else  
	{
		echo '<font color= "#0000ff" font size="3">';
		//echo "Try again looser";
		print_r(mysql_error());
		echo "</br>";
		echo "</br>";
		echo "</br>";
		echo '<img src="../images/slap.jpg"   />';	
		echo "</font>";  
	}
}

```

有错误信息，能报错，有数据库查询内容，可union\(查到内容的情况下\)。可逻辑。

* 这种登陆框一般是要求绕过身份验证而不是获取信息，所以这里的payload给的是如何登陆成功：

  这里的关键还是说如何使得sql语句获得内容，以为此处的sql语句是一个双条件查询，即查询了username又查询了password，因此我们有两个输入点：

```php
	@$sql="SELECT username, password FROM users WHERE username='$uname' and password='$passwd' LIMIT 0,1";
```

我们可以选在username处截断，然后注入'\|\|1=1\#，就可以登陆表中第一个用户。当然需要登陆指定用户如`admin`, 可使用'admin&&1=1\# 无密码登陆，当然admin这个用户必须有，不然逻辑与操作自带控制流，半闭合不执行后边内容。在password处一个道理，需要用逻辑或，查第一个。当然也可以拼接联合注入。 既然外显，可查信息，如：' union select user\(\),2\#，让本来的sql什么也查不到，自然union的就是第一条\(也就是外显的\)结果了。

## lesson 12

* sink:

```php
$uname='"'.$uname.'"';
	$passwd='"'.$passwd.'"'; 
```

双引号闭合

```php
	@$sql="SELECT username, password FROM users WHERE username=($uname) and password=($passwd) LIMIT 0,1";
```

加单括号闭合



* output: 同lesson11一样， 可报错可回显。
* payload: 
  * 指定查询： username:`admin") and 1=1#`
  * `第一条： username: ") or 1#`

## lesson 13

* sink:

```php
	@$sql="SELECT username, password FROM users WHERE username=('$uname') and password=('$passwd') LIMIT 0,1";
```

单引号家括号闭合，也是双查寻验数量

* output 

```php
	if($row)
	{
  		//echo '<font color= "#0000ff">';	
  		
  		echo "<br>";
		echo '<font color= "#FFFF00" font size = 4>';
		//echo " You Have successfully logged in " ;
		echo '<font size="3" color="#0000ff">';	
		echo "<br>";
		//echo 'Your Login name:'. $row['username'];
		//echo "<br>";
		//echo 'Your Password:' .$row['password'];
		//echo "<br>";
		echo "</font>";
		echo "<br>";
		echo "<br>";
		echo '<img src="../images/flag.jpg"   />';	
		
  		echo "</font>";
  	}
	else  
	{
		echo '<font color= "#0000ff" font size="3">';
		//echo "Try again looser";
		print_r(mysql_error());
		echo "</br>";
		echo "</br>";
		echo "</br>";
		echo '<img src="../images/slap.jpg"   />';	
		echo "</font>";  
	}
}
```

这里还可以报错，但是不能回显了，你是不是有些慌？ 没关系。我们可以通过报错信息获悉闭合形式

```php
You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''#') and password=('') LIMIT 0,1' at line 1

```

我们还可以通过登陆成功和失败的两张照片来进行布尔型盲注。

## lesson 14

* sink:

```php
$uname='"'.$uname.'"';
$passwd='"'.$passwd.'"'; 
@$sql="SELECT username, password FROM users WHERE username=$uname and password=$passwd LIMIT 0,1";
```

双引号闭合双查询，验证有无查询到内容与前相同。

```php

	if($row)
	{
  		//echo '<font color= "#0000ff">';	
  		
  		echo "<br>";
		echo '<font color= "#FFFF00" font size = 4>';
		//echo " You Have successfully logged in " ;
		echo '<font size="3" color="#0000ff">';	
		echo "<br>";
		//echo 'Your Login name:'. $row['username'];
		//echo "<br>";
		//echo 'Your Password:' .$row['password'];
		//echo "<br>";
		echo "</font>";
		echo "<br>";
		echo "<br>";
		echo '<img src="../images/flag.jpg" />';	
		
  		echo "</font>";
  	}
	else  
	{
		echo '<font color= "#0000ff" font size="3">';
		//echo "Try again looser";
		print_r(mysql_error());
		echo "</br>";
		echo "</br>";
		echo "</br>";
		echo '<img src="../images/slap.jpg"  />';	
		echo "</font>";  
	}
```

同lesson13

## lesson15

* sink 

```php
	@$sql="SELECT username, password FROM users WHERE username='$uname' and password='$passwd' LIMIT 0,1";

```

* output

```php
if($row)
	{
  		//echo '<font color= "#0000ff">';	
  		
  		echo "<br>";
		echo '<font color= "#FFFF00" font size = 4>';
		//echo " You Have successfully logged in\n\n " ;
		echo '<font size="3" color="#0000ff">';	
		echo "<br>";
		//echo 'Your Login name:'. $row['username'];
		echo "<br>";
		//echo 'Your Password:' .$row['password'];
		echo "<br>";
		echo "</font>";
		echo "<br>";
		echo "<br>";
		echo '<img src="../images/flag.jpg"  />';	
		
  		echo "</font>";
  	}
	else  
	{
		echo '<font color= "#0000ff" font size="3">';
		//echo "Try again looser";
		//print_r(mysql_error());
		echo "</br>";
		echo "</br>";
		echo "</br>";
		echo '<img src="../images/slap.jpg"   />';	
		echo "</font>";  
	}
	
```

这里没有显错帮助了，所以如何构造闭合？猜吧，盲注吧，之后再加上各种关键字过滤，哈哈哈哈哈，同lesson 14。

## lesson 16

登陆框盲注之：

```php
$uname='"'.$uname.'"';
	passwd='"'.$passwd.'"'; 
	@$sql="SELECT username, password FROM users WHERE username=($uname) and password=($passwd) LIMIT 0,1";

```



