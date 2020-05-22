---
title: php对libcurl库的支持cURL
date: 2016-11-20 17:22:00
tags: [php,curl]
categories:
- curl
---

首先介绍一下curl，php支持扩展库`libcurl`，curl模拟浏览器来传输数据。
因为是模拟浏览器，所以支持很多传输协议：
FTP, FTPS, HTTP, HTTPS, GOPHER, TELNET, DICT, FILE 以及 LDAP
HTTP基于表单的上传，代理，cookies，和用户名+密码的认证
用处很多，也可以用来做python爬虫类似的功能，比什么file和file_get_contents要强很多。

**简单点说curl分四步：**
初始化，新建curl资源
设置url选项 //curl_setopt()  最重要，最有意思的一步。
抓取url，传递到浏览器
关闭curl，释放资源
curl_setopt($ch, option, value)函数将为一个CURL会话设置选项。option参数是你想要的设置，value是这个选项给定的值。
eg:
```php
<?php
$ch = curl_init() //url可以这里写，也可以在后面写。
//CURLOPT_URL 需要获取的URL地址
curl_setopt($ch, CURLOPT_URL, $url); //url可以这里写，也可以在init写。
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($ch, CURLOPT_HEADER, 0);
//执行并获取HTML文档内容
$output = curl_exec($ch);
//释放资源
curl_close($ch);
?>
```
<!--more-->
eg: 如果我们只想把获取到的内容输入到文件，而不是直接输出给浏览器，我们就可以使用CURLOPT_RETURNTRANSFER选项！
这一点也是比较常用到的。
```php
<?php
$ch = curl_init($url) ;
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, FALSE);
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, FALSE);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true) ; // 获取数据返回
curl_setopt($ch, CURLOPT_BINARYTRANSFER, true) ; // 在启用 CURLOPT_RETURNTRANSFER 时候将获取数据返回
$result = curl_exec($ch);
curl_close($ch);
?>
```

eg: 有时候会遇到curl传递证书,大致方式则如下。
```php
<?php
$url = 'https://mch.tenpay.com/refundapi/gateway/refund.xml';
$param = $signObj["unsign"] . "&sign=" . $signObj["sign"];
// 		Log::write($url);
$ch = curl_init($url) ;
// CURLOPT_POST启用时会发送一个常规的POST请求，类型为：application/x-www-form-urlencoded，就像表单提交的一样。
curl_setopt($ch, CURLOPT_POST, 1);
//CURLOPT_HEADER	启用时会将头文件的信息作为数据流输出
curl_setopt($ch, CURLOPT_HEADER, 0);
//CURLOPT_URL 需要获取的URL地址
curl_setopt($ch, CURLOPT_URL,$url);
//CURLOPT_SSL_VERIFYHOST 检查服务器SSL证书中是否存在一个公用名(common name)。译者注：公用名(Common Name)一般来讲就是填写你将要申请SSL证书的域名 (domain)或子域名(sub domain)。2 检查公用名是否存在，并且是否与提供的主机名匹配。
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, FALSE);
//全部数据使用HTTP协议中的"POST"操作来发送。要发送文件，在文件名前面加上@前缀并使用完整路径。这个参数可以通过urlencoded后的字符串类似'para1=val1&para2=val2&...'或使用一个以字段名为键值，字段数据为值的数组。如果value是一个数组，Content-Type头将会被设置成multipart/form-data
curl_setopt($ch, CURLOPT_POSTFIELDS, $param);
//CURLOPT_RETURNTRANSFER 把获取到的内容输入到文件
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);

// cert
//CURLOPT_SSLCERT 一个包含PEM格式证书的文件名
curl_setopt($ch, CURLOPT_SSLCERT, $filePem);
//CURLOPT_SSLCERTPASSWD 使用CURLOPT_SSLCERT证书需要的密码
curl_setopt($ch, CURLOPT_SSLCERTPASSWD, $partnerId);
//CURLOPT_SSLCERTTYPE 证书的类型。支持的格式有"PEM" (默认值), "DER"和"ENG"
curl_setopt($ch, CURLOPT_SSLCERTTYPE, "PEM");

// ca

//禁用后cURL将终止从服务端进行验证。使用CURLOPT_CAINFO选项设置证书使用CURLOPT_CAPATH选项设置证书目录 如果CURLOPT_SSL_VERIFYPEER(默认值为2)被启用，CURLOPT_SSL_VERIFYHOST需要被设置成TRUE否则设置为FALSE。	自cURL 7.10开始默认为TRUE。从cURL 7.10开始默认绑定安装
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, TRUE);
//CURLOPT_CAINFO 一个保存着1个或多个用来让服务端验证的证书的文件名。这个参数仅仅在和CURLOPT_SSL_VERIFYPEER一起使用时才有意义。 .
curl_setopt($ch, CURLOPT_CAINFO, $filePemCert);

$result = curl_exec($ch);
curl_close($ch);

?>
```
这边带上一个curl的方法函数：
```php
<?php
/**
	 * 	作用：使用证书，以post方式提交xml到对应的接口url
	 */
	function postXmlSSLCurl($xml,$url, $filePem, $filePemCert, $second=30)
	{
		$ch = curl_init();
		//超时时间
		curl_setopt($ch,CURLOPT_TIMEOUT,$second);
		//这里设置代理，如果有的话
        //curl_setopt($ch,CURLOPT_PROXY, '8.8.8.8');
        //curl_setopt($ch,CURLOPT_PROXYPORT, 8080);
        curl_setopt($ch,CURLOPT_URL, $url);
        curl_setopt($ch,CURLOPT_SSL_VERIFYPEER,FALSE);
        curl_setopt($ch,CURLOPT_SSL_VERIFYHOST,FALSE);
		//设置header
		curl_setopt($ch,CURLOPT_HEADER,FALSE);
		//要求结果为字符串且输出到屏幕上
		curl_setopt($ch,CURLOPT_RETURNTRANSFER,TRUE);
		//设置证书
		//使用证书：cert 与 key 分别属于两个.pem文件
		//默认格式为PEM，可以注释
		curl_setopt($ch,CURLOPT_SSLCERTTYPE,'PEM');
		curl_setopt($ch,CURLOPT_SSLCERT, $filePemCert);
		//默认格式为PEM，可以注释
		curl_setopt($ch,CURLOPT_SSLKEYTYPE,'PEM');
		curl_setopt($ch,CURLOPT_SSLKEY, $filePem);
		//post提交方式
		curl_setopt($ch,CURLOPT_POST, true);
		curl_setopt($ch,CURLOPT_POSTFIELDS,$xml);
		$data = curl_exec($ch);
		//返回结果
		if($data){
			Log::write($data);
			curl_close($ch);
			return $data;
		}
		else { 
			$error = curl_errno($ch);
			Log::write("curl出错，错误码:" . $error);
// 			echo "<a href='http://curl.haxx.se/libcurl/c/libcurl-errors.html'>错误原因查询</a></br>";
			curl_close($ch);
			return false;
		}
	}

	/**
	 * 	作用：以post方式提交xml到对应的接口url
	 */
	public function postXmlCurl($xml,$url,$second=30)
	{		
        //初始化curl        
       	$ch = curl_init();
		//设置超时
		curl_setopt($ch, CURLOP_TIMEOUT, $second);
        //这里设置代理，如果有的话
        //curl_setopt($ch,CURLOPT_PROXY, '8.8.8.8');
        //curl_setopt($ch,CURLOPT_PROXYPORT, 8080);
        curl_setopt($ch,CURLOPT_URL, $url);
        curl_setopt($ch,CURLOPT_SSL_VERIFYPEER,FALSE);
        curl_setopt($ch,CURLOPT_SSL_VERIFYHOST,FALSE);
		//设置header
		curl_setopt($ch, CURLOPT_HEADER, FALSE);
		//要求结果为字符串且输出到屏幕上
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, TRUE);
		//post提交方式
		curl_setopt($ch, CURLOPT_POST, TRUE);
		curl_setopt($ch, CURLOPT_POSTFIELDS, $xml);
		//运行curl
        $data = curl_exec($ch);
		curl_close($ch);
		//返回结果
		if($data)
		{
			curl_close($ch);
			return $data;
		}
		else 
		{ 
			$error = curl_errno($ch);
			Log::write("curl出错，错误码:" . $error); 
// 			echo "<a href='http://curl.haxx.se/libcurl/c/libcurl-errors.html'>错误原因查询</a></br>";
			curl_close($ch);
			return false;
		}
	}

?>
```
http://www.jb51.net/article/39331.htm?timer=tc
curl的一些option高级选项可参照互联网链接。

create By 
liecol-晓斌 
2016-11-20
