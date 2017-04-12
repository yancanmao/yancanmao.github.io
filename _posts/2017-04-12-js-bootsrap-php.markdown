---
layout:     post
title:      "最近踩过的坑，学到的东西"
subtitle:   " \"前后端经验\""
date:       2017-04-12 12:00:00
author:     "MYC"
header-img: "img/post-bg-universe.jpg"
catalog: true
tags:
    - 学习 
    - jquery
    - bootsrap
    - php-api
    - web
---


### 前言

这么半个月一直在做一个基于yii框架的偏前端的逻辑系统，这里我使用了jquery，bootstrap，php，使用了mvc架构开发模块，写了表单，用了ajax，同时调用了他人给我提供的接口，整个系统的体量不算庞大，但是逻辑复杂，目前接触的第二大开发任务吧，最主要的是。。不会= =。

### 理解jquery

jQuery 是一个 JavaScript 库。

jQuery 极大地简化了 JavaScript 编程。

jQuery 很容易学习。

	$(function () {});

当界面元素加载完后会调用jquery，这时候可以用上面的代码进行声明，也可以用下面的代码进行声明，意思是一样的

	$(document).ready(function(){}

**$.fn**是指jquery的命名空间，加上fn上的方法及属性，会对jquery实例每一个有效。 

可以理解成$.fn可以声明一个jquery方法，这个方法可以想jquery的方法如Array()，each()等这种方法使用，就像在类中声明一个成员函数。

### jquery表单

jquery表单可以通过几种方法向后端传送数据：

- 一种是submit的方式，直接将所有表单input的内容发送到后端，然后跳转。
- 一种是通过ajax的方式以action将数据传送到后端然后进行处理，这种方法可以不跳转，并且可以处理input的数据，自己定义参数然后向后端发送。

### 选择器，取参数

jquery表单的发送自己感觉也是一个高深的学问，其中需要考虑选择器selector取参数的方法，如input型数据用$("input[name='XXX']")，select型数据用$("input[name='coupon_type']")，其他的就不枚举了。

取一个元素的其他元素用.attr()方法就可以取到，所以说在前端获取数据的方法其实挺多的，而且感觉一个样式可以通过很多种方法实现，一个效果也可以有很多种方法去完成，可以用jquery，可以用bootstrap自带方法，所以很多时候还是需要明白了需求之后再去开发会简单很多，直接百度可以搜出很多实现一个效果的方法。

### 接入一个效果有效方法

比如我最近接入一个checkbox树，这棵树直接通过代码写的话可能也写得出来，但是开发量绝对巨大，所以我在经过一段时间的搜索过后，发现有treeview这样一个文件树的库，调用这个之后在jquery里面进行动态生成，将会节约很多时间，剩下的就是将checkbox接入这棵树，整体来讲实现这个效果会缩短很多时间，但是一棵树肯定不可避免的会关联很多动作，这些又要通过jquery关联动作，所以整个页面会非常麻烦。

到目前为止我对jquery的开发终于有了一些体会，能够写一些东西了，按照目前的能力，应该能够独立开发一个界面，实现很多动态结果。

### 专业化

前端很多地方我觉得还是要注意专业化，比如在我们jquery处理完一个数据，这个数据是一个新数据，需要一个字段存储，那么这个时候为了方便调用，应该声明一个hidden的input标签进行存放，到时候表单可以较轻松的渠道，要做数据处理也会比较轻松。

### laravel 服务——验证

目前我使用过的验证功能是validate，这个方法可以验证参数是否符合自己写的规则

	$this->validate($request, [
	        'title' => 'required|unique:posts|max:255',
	        'body' => 'required',
	    ]);

当数据验证不成功时，也就是参数错误时，就可以使用下面这个来打印错误信息，然后通过返回数据查看哪里验证有问题

	return $validator->errors()->all();

详情参见：

[[ Laravel 5.2 文档 ] 服务 —— 验证](http://laravelacademy.org/post/3279.html)

### CArrayDataProvider()

CArrayDataProvider()是yii框架的一个方法吧，目前我是这样理解的。

	$rawData=Yii::app()->db->createCommand('SELECT * FROM tbl_user')->queryAll();
	// or using: $rawData=User::model()->findAll();
	$dataProvider=new CArrayDataProvider($rawData, array(
	    'id'=>'user',
	    'sort'=>array(
	        'attributes'=>array(
	             'id', 'username', 'email',
	        ),
	    ),
	    'pagination'=>array(
	        'pageSize'=>10,
	    ),
	));
	// $dataProvider->getData() will return a list of arrays.

官方给的示例长这样，实际使用的过程中，这个东西还是很强大的，通过yii给的方法，可以将从数据库提取的数组数据通过某种顺序排列，并且能够和前端进行交互，具体的流程我也只是体会，稍微能够在目前项目中运用，但是如果让我从零开始使用，我想还是不太会运用。

目前使用过在bootstrap中调用一个TbGridView的widget，将CArrayDataProvider()处理的数据赋给dataProvider，这样就能够直接将所有的数据以table的形式显示出来了，具体过程是在不明白，但是对前人的敬仰之情确实滔滔不绝。

所以还是在这里记录一个官方文档给出的说明吧，以后再去理解

[CArrayDataProvider](http://www.yiiframework.com/doc/api/1.1/CArrayDataProvider)