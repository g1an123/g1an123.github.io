---
title: 陇剑杯 2021 misc webshell
date: 2023-08-23 22:17:32
tags:
- misc
- 流量分析
categories:
- ctf
- writeup
---
## 陇剑杯 2021  webshell（问1）
```
题目描述：

单位网站被黑客挂马，请您从流量中分析出webshell，进行回答：
黑客登录系统使用的密码是_____________。。得到的flag请使用NSSCTF{}格式提交。
```

题目描述说是登录系统，根据日常经验，一般登录操作使用的都是POST请求，于是我们直接在流量包中搜索POST请求看看。

```
http.request.method==POST
```

![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-23%2022.32.34.png)
然后追踪流看看，运气比较好第一个就是。
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-23%2022.35.35.png)
交差下班。


## 陇剑杯 2021webshell（问2）
```
单位网站被黑客挂马，请您从流量中分析出webshell，进行回答：
黑客修改了一个日志文件，文件的绝对路径为_____________。（请确认绝对路径后再提交）。得到的flag请使用NSSCTF{}格式提交。
```
关键词：日志。直接搜索.log看看.
发现东西有点多。
需要再想个法子缩小范围。
想到既然是通过挂马进行修改，那么看看他的🐎子是啥类型的，我们就可以通过请求类型进行进一步缩小范围。
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-23%2022.42.47.png)
发现一列1.php，应该就是🐎子了，再看一眼确定没错，是post马子。

然后就一个个看，第一个就是。
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-23%2022.49.21.png)
但是发现只是相对路径，所以还要结合传马的位置拼接得到：
`/var/www/html/data/Runtime/Logs/Home/21_08_07.log`



## 陇剑杯 2021webshell（问3）
```
题目描述：

单位网站被黑客挂马，请您从流量中分析出webshell，进行回答：
黑客获取webshell之后，权限是______？得到的flag请使用NSSCTF{}格式提交。
```
看到权限，可能会想到通过whoami这些命令进行过滤，但是出来的东西有点多。
我们不妨直接猜一手，因为一般权限无非root 和www-data.
得到是www-data

## 陇剑杯 2021webshell（问4）
```
题目描述：

单位网站被黑客挂马，请您从流量中分析出webshell，进行回答：
黑客写入的webshell文件名是_____________。(请提交带有文件后缀的文件名，例如x.txt)。得到的flag请使用NSSCTF{}格式提交。
```
这一问就不用说了，前面就已经看出来1.php就是马子了。
要是非问为啥，那原因就是看他流量特征，一眼蚁剑
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-23%2023.04.38.png)

<a href="https://www.freebuf.com/articles/network/204796.html">常见webshell客户端流量特征</a>


## 陇剑杯 2021webshell（问5）
```
题目描述：

单位网站被黑客挂马，请您从流量中分析出webshell，进行回答：
黑客上传的代理工具客户端名字是_____________。（如有字母请全部使用小写）。得到的flag请使用NSSCTF{}格式提交。
```
这里我一开始是想找上传的哪个数据包的，后来误打误撞发现了一个类似于 `ls` 命令的返回包。发现了frpc.ini
于是直接就能得出就是frpc了。

还有正常解法，就是看看比较大的包，大概率就是上传的包了。

## 陇剑杯 2021webshell（问6）
```
题目描述：

单位网站被黑客挂马，请您从流量中分析出webshell，进行回答：
黑客代理工具的回连服务端IP是_____________。得到的flag请使用NSSCTF{}格式提交。

开启环境0
```
这题我目前也不是很清楚，最后找到frpc.ini文件的时候是需要hex解码的。
最后就得到反连的ip是192.168.239.123
## 陇剑杯 2021webshell（问7）
```
题目描述：

单位网站被黑客挂马，请您从流量中分析出webshell，进行回答：
黑客的socks5的连接账号、密码是______。（中间使用#号隔开，例如admin#passwd）。得到的flag请使用NSSCTF{}格式提交。

开启环境0
```
都在hex解密之后的frpc.ini中
![](https://cdn.jsdelivr.net/gh/g1an123/blogimage@main/%E6%88%AA%E5%B1%8F2023-08-24%2000.16.24.png)
