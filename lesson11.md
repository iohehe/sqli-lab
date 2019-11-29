# lesson11

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

