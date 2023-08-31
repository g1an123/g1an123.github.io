---
title: sharkctf_2023
date: 2023-08-22 12:51:24
tags:
- writeup
- web
categories:
- ctf
---

`暑假事情有点多，没啥时间写，只写完了web题，写篇wp水水博客。`

---
## 彩蛋
1azy_fish加了彩蛋，找一找？不用爆破（把服务器日坏了就不好玩了

### 敏感路径
这题刚开始看以为是F12题，一直没找到，后来翻插件发现有敏感目录 /goat。
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-22%2013.09.03.png)
推荐一下这个插件：findsomething（尊嘟很好用）

### F12查看网页源码
直接拼接就是一个F12题了，F12查看源码得到flag
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-22%2013.07.01.png)



## Ez_http
VidocQwQ说http好简单，我们一起学习吧。

![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-22%2013.12.24.png)

### XFF头ip绕过
使用hackbar进行操作添加XFF头。
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-22%2013.15.43.png)

### Referer来源绕过

![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-22%2013.17.39.png)

### UA绕过
UA头一般用来确定浏览器类型
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-22%2013.19.12.png)


## view source
VidocQwQ邀请你一起打游戏辣，2048都会吧？

### 查看源码
看题目意思应该是要查看源码，但是禁用了F12和右键。
不过可以通过谷歌浏览器的设置打开开发者工具
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-22%2013.24.19.png)
源码发现提示hint.php
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-22%2013.25.33.png)

### 源码查询发现路由及参数
直接拼接访问的话发现什么也没有。
于是回到前面查看源码，但是代码有点长。
于是猜测hint.php是否用以传递成绩参数之类的，全局搜索看看是否有暴露相关路由。
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-22%2013.30.16.png)
发现相关源码，于是直接可以拼接获得flag
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-22%2013.32.50.png)

## Ez_eval
VidocQwQ写了个php，留下了一个函数，你们知道怎么使用吗？

###  开局送源码
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-22%2013.34.04.png)
审计发现通过word传参命令执行，过滤flag字段。
### 通配符绕过
`?word=system("cat /f*");`
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-22%2013.37.01.png)

## Ez_SQL
### 手工做法：
#### 查看源码有提示
提示将select替换为空。
绕过办法：分拆绕过，过滤时重组形成payload
#### 构造语句
先order by 确定一下字段数，
`username=123'order by 4--+`
发现字段为4。使用hackerbar辅助构造payload
```username=123'union selselectect group_concat(schema_name),2,3,4 from information_schema.schemata-+```
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-22%2013.46.22.png)
然后就使用相同方式构造注入语句拿下flag。

### 工具梭哈
sqlmap一把梭，具体使用就自己查一查了哈。


## 哈斯哈斯哈斯（bt
1azy_fish觉得md5很好用，就用一堆md5保护了flag，使它不被坏人拿走。

### 弱口令
开局一个登录框，弱口令是试一下，得到：
admin:admin

### 查看源码+传参
跳转后页面看看源码得到提示。
`你给我hint我给你hint`
猜测传参hint。

### php审计

![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-22%2013.55.11.png)


```php
<?php  
error_reporting(0);  
require_once('/flag.php');   
if(isset($_GET['hint'])){    highlight_file(__FILE__);  
}  
else{  
    include_once "loginok.html";  
}  
$a=$_GET['a'];  
$b=$_GET['b'];  
$hash=$_COOKIE['hash'];  
$word=$_POST['word'];  
if($a !== $b & md5($a) === md5($b)){  
    echo ' WOW,u are so cool ';  
    echo strlen($flag);  
}  
if (preg_match('/^1952(.*?)NUAA$/', $word)){  
    if(intval($word) === intval(strrev($word))){  
        echo " 宝贝,flag快出来了哦,加油捏 ";  
        echo md5($flag);  
    }  
}  
if ($hash === md5($flag . $word))  
      echo " Wooooooo!You cracked the md5. Here is your flag " . $flag;  
?>
```


```php
大概有三层判断：
1`.if($a !== $b & md5($a) === md5($b)){`
2`.`if (preg_match('/^1952(.*?)NUAA$/', $word)){  
    if(intval($word) === intval(strrev($word))){``
3`.if ($hash === md5($flag . $word))`
```

### 1. 值不相同但md5加密后相同（数组绕过md5强比较）
- 通过数组类型一致，但值不一致。
- md5()函数无法处理数组，如果传入的为数组，会返回NULL，所以两个数组经过加密后得到的都是NULL,也就是相等的。
- 中间的为什么是&而不是&&。在此题当中都是成立的，详细的说明可以查看<a href=https://www.runoob.com/note/34429>菜鸟教程</a>

### 2.1952开头NUAA结尾，倒序并interval()后仍相同
- 正常进行输入的情况如下： ![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-22%2020.54.27.png)
相关定义可以自己去查一下（我懒）

大概思路: 正序的字符串比较好控制大小，而倒序的感觉有点难办。
小trick：科学计数法
我们可以通过科学计数法使得正向的字符串经过intval后变为0也就实现了绕过。

payload:
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-22%2021.05.58.png)

得到md5值

### 3.word置空进行绕过。
这里就不要想的太复杂哈哈哈哈哈哈哈。还得是小小姐。
word参数直接置空并且加上hash就可以得到flag了。

下班！


## 伤身体（ssti
1azy_fish沉迷于某六字游戏，在他大意的时候，快偷了他的flag！！！

### 弱口令登入
admin:password

### ssti注入
ssti注入我就大概知道个原理，于是呢就直接工具梭哈啦。
打开fenjing直接一把嗦，具体用法自己查一下吧。


## 来抽个奖？
1azy_fish觉得有随机数在，他就不会亏卡，快爆了他，让他血本无归。

#### php伪随机数漏洞
多次刷新发现随机数始终相同，于是猜测肯定使用的同一个种子。漏洞请往下翻翻。

### 爆破种子

爆破种子工具包 <a href=https://github.com/openwall/php_mt_seed>php_mt_seed</a>
使用教程看看README就行。


### 漏洞了解
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-22%2021.21.05.png)

```php
<?php  
mt_srand(5201314);  
echo mt_rand().PHP_EOL;  
echo mt_rand().PHP_EOL;  
echo mt_rand().PHP_EOL;  
echo mt_rand().PHP_EOL;  
?>
```
可以通过多次实验发现相同种子下，都是相同的数据，相同的顺序。

下班！

## 我不是op！
1azy_fish说他自己不是op，你可以登录它的管理员账号看看他到底是不是op吗？

尝试弱口令无果后随便打一个用户名，发现返回包
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-17%2012.01.55.png)
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-17%2012.02.06.png)
得到token一眼jwt，同时解编码message上面的unicode
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-17%2012.03.32.png)
然后带这个token去/protected
需要注意的点（这个token是jwt，需要放在cookie里面进行传参）
使用GET请求

构造后的burp请求包：
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-17%2012.07.12.png)

```
GET http://101.42.30.15:8306/protected HTTP/1.1
Host: 101.42.30.15:8306
Pragma: no-cache
Cache-Control: no-cache  
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.5666.197 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,\*/\*;q=0.8,application/signed-exchange;v=b3;q=0.7   
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7
Cookie: session=7fa98980-e33f-4e0b-9213-4c1616d16f94.62XYmGmHDaMHpnRNYQEY--rTTK0; token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluJyJ9.ATUoxudr6sa0eNyMUQqU155AeGVsuv90_CM-T_WVlKM
sec-ch-ua-platform: "Windows"
sec-ch-ua: "Google Chrome";v="113", "Chromium";v="113", "Not=A?Brand";v="24" 
sec-ch-ua-mobile: ?0
Connection: close
```
返回包里
`<!--secret_key = "Lazy_fish_Is_op?"-->`
应该就是jwt的密钥了
于是直接找个在线jwt网站篡改一下jwt就可以越权到admin了(网站： https://jwt.io/)
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-17%2012.12.01.png)
直接修改111为admin然后得到admin的 jwt
带这admin的jwt重新访问即可得到flag
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-17%2012.13.50.png)


Shark{1aZyfish_is_0pppppppppp!}