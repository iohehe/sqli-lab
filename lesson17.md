---
description: 过滤
---

# lesson17

## 过滤函数

从此开始， 加入了过滤函数。 我们不但要构造可用payload，还要绕过过滤。

## lesson17

* input

```php
// take the variables
if(isset($_POST['uname']) && isset($_POST['passwd']))

{
//making sure uname is not injectable
$uname=check_input($_POST['uname']);  

$passwd=$_POST['passwd'];

```

可以看到 `$uname`处嵌套了`chek_input, 这就造成了字符限制。`

* sanitize

```php
function check_input($value){
	if(!empty($value))
	{
			// truncation (see comments)
			$value = substr($value,0,15);
	}

	// Stripslashes if magic quotes enabled
	if (get_magic_quotes_gpc())
	{
			$value = stripslashes($value);
	}

  // Quote if not a number
	if (!ctype_digit($value))
	{
		  $value = "'" . mysql_real_escape_string($value) . "'";
	}
	else
	{
		  $value = intval($value);
	}
	return $value;
	}
```

这个过滤函数做了三件事：

1. 截断，匹配0,1个字符
2. 如果开启了`get_magic_quotes_gpc()` 则去掉反斜杠转义（[http://www.nowamagic.net/librarys/veda/detail/1012](http://www.nowamagic.net/librarys/veda/detail/1012)）
3. 如果是数字型则`intval` 其他`mysql_real_escape_string`

* sink

```php
// connectivity 
@$sql="SELECT username, password FROM users WHERE username= $uname LIMIT 0,1";

$result=mysql_query($sql);
$row = mysql_fetch_array($result);
//echo $row;
if($row)
{
  //echo '<font color= "#0000ff">';	
  $row1 = $row['username'];  	
	//echo 'Your Login name:'. $row1;
	$update="UPDATE users SET password = '$passwd' WHERE username='$row1'";
	mysql_query($update);
  echo "<br>";
  ...
```

这儿有两句sql，两次查询， 从业务功能上看，这是一个用户更新密码点，先查到用户信息，在通用户名去更新密码。

看第一句sql看上去不需要引号闭合。但是在过滤函数中如果不是数字就给加上单引号了， 这样就防护了数字型过滤。看上去比较难搞...

看看lesson17的介绍是 `Update Query- Error based` 也就是提示使用第二句。这里的`$passwd`是一个可控变量，而且在传入的时候并没有`check_input` 那么截断后边的`usernan` 我就控制了一个update语句。

这里首先你要在第一条语句能够查处内容来，才能通if的控制条件， username处填admin, 来到第二处，控制了一个单引号闭合的$passwd。

来看他的回显：

```php
		if (mysql_error())
		{
			echo '<font color= "#FFFF00" font size = 3 >';
			print_r(mysql_error());
			echo "</br></br>";
			echo "</font>";
		}
		else
		{
			echo '<font color= "#FFFF00" font size = 3 >';
			//echo " You password has been successfully updated " ;		
			echo "<br>";
			echo "</font>";
		}
```

这里有错误的回显，因此我们可以在这里构造报错注入：

* payload:
  * username: admin
  * new password: ' and \(updatexml\(1,concat\(0x7e,\(select user\(\)\),0x7e\),1\)\)\#

