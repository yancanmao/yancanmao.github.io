---
layout:     post
title:      "cookie-session学习心得"
subtitle:   " \"cookie-session理解\""
date:       2017-03-16 12:00:00
author:     "MYC"
header-img: "img/post-bg-universe.jpg"
catalog: true
tags:
    - 学习 
    - cookie-session
    - web
---


## 理解Cookie和Session机制

### cookie和session提出的背景

HTTP的无状态：HTTP协议是无状态的，指的是协议对于事务处理没有记忆能力，服务器不知道客户端是什么状态。也就是说，打开一个服务器上的网页和你之前打开这个服务器上的网页之间没有任何联系。

### 解决方法

会话（Session）跟踪是Web程序中常用的技术，用来跟踪用户的整个会话。常用的会话跟踪技术是Cookie与Session。

cookie通过在客户端记录信息确定用户身份，Session通过在服务器端记录信息确定用户身份。

### cookie和session原理

> cookie和session是一种会话跟踪技术

> cookie的工作原理

![](http://i.imgur.com/BMnzCdx.png)

1.	客户端浏览器发送一个http请求给web服务器；
2.	Web服务器会有一个响应，同时以set-cookie的方式将cookie返回给客户端浏览器，客户端浏览器会将这个cookie保存下来；
3.	当客户端浏览器下一次再去请求web服务器的时候，会带上这个cookie；
4.	Web服务器接受到这个请求，便会知道这个浏览器想要做什么，同时给出一个回应；

> session的工作原理

![](http://i.imgur.com/bxlVBq6.png)

1.	客户端（浏览器A）第一次访问php1页面的时候，启动session_start()，生成一个sessionID，服务器响应客户端请求时，会将sessionID以cookie的方式保存到浏览器A；
2.	当浏览器A请求php2页面的时候，会把自己保存在cookie中的sessionID带到服务器，服务器会根据sessionID来找到对应的文件，从而响应；
3.	同理，对于客户端浏览器B而言，也是一样的；

### cookie和session的区别

cookie的形式：小段的文本信息(key=>value键值对)

![](http://i.imgur.com/vkPpldI.png)

session的形式:details|a:3:{s:4:”name”;s:7:”fengjie”;s:3:”age”;s:2:”18”;s:6:”marray”;b:0;}

![](http://i.imgur.com/DbieqjD.png)
![](http://i.imgur.com/LT7nrZ5.png)

seesion在客户端是以cookie的形式存放的
cookie和session的区别与联系

保存方式：cookie保存的客户端（浏览器），session保存在服务器
安全性：session比cookie安全；
性能：session消耗服务器资源，cookie消耗的是本地磁盘
联系：session建立在cookie之上

### cookie & session的应用场景

使用方法

	cookie使用：setcookie(name,value,expire,[path,domain,secure])

	session使用:1.session_start();2.$_SESSION[‘key’]= $value

### 总结：

☞ cookie—-用户去商店买东西，但是没有带钱，这个时候商店就给顾客打一个欠条（相当于回送一个cookie），用户下一次再来买东西时，只要带上这个欠条（cookie),那么店主就知道这个顾客是来还钱来了；

☞ session—-第一种方式是把欠条给到用户，而这种方式是把欠条由店主保存，给客户一个欠条的编号，下一次顾客来的时候只要拿出这个编号和店主“碰个头”，就可以了；

综合以上两种方式，我们可以发现，第一种方式不安全，因为这个欠条直接由客户保存（店主没有保存），如果这个客户不讲诚信，下次来不带欠条（客户端禁用了cookie），那么店主就亏了；所以，通常都是使用第二种方式将cookie和session结合起来使用；

### 链接分享

[你必须了解的Session的本质](http://netsecurity.51cto.com/art/201402/428721.htm)

[深入理解HTTP Session](http://lavasoft.blog.51cto.com/62575/275589/)

[Cookie/Session机制详解 ](http://blog.csdn.net/fangaoxin/article/details/6952954)

[老生常谈session,cookie的区别，安全性](http://blog.51yip.com/php/938.html)

[Cookie + Session + OAuth + SSO](http://zhongxiao37s-wiki.readthedocs.org/en/latest/network/OAuthSSO.html)

[跨域访问，可能点不进去](http://hc1988.diandian.com/post/2012-01-13/16279824)

[web的工作方式，http协议简介 ](http://my.oschina.net/cxz001/blog/331671)

[session的基本原理](http://www.cnblogs.com/ohmygirl/p/internal-5.html)

