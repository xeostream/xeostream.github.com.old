---
layout: post
title: "RubyOnRails学习之rails框架"
date: 2013-03-16 17:58
comments: true
categories: [Study, ROR]
---
rails是基于MVC的web框架，model对应于active record，controller对应于action controller，view对应于action view。
<!--more-->
##Active record与model##
modle对应的active record 继承于ActiveRecord::Base，类似于Hibernate，原理是利用文件名自动查找对应的业务对象与数据库中的表，由于数据的持久化，在每个表对应的唯一一个active record中声明表中每列的属性，这个过程需要一一对应。建立active record对象有两种方法；一，通过new()方法建立，这种方法声明的active record对象不会提交给数据库，所以无法在数据库中得到体现；二，通过create()方法声明的对象可以做到active record与数据库的同步。声明对象时需要给对象的属性赋值，赋值方法主要为在声明对象的同时传入hash或者是代码块。rails的ORM不需要像Java中SSH的大量配置文件，这要归功于“约定大于配置”理念。rails的数据库的配置需要到项目下config/database.yml中配置，数据库的表名是active record的类名的复数形式，例:catalogs-Catalog，这些可以在ActiveRecord：Base基类提供的方法中设置。
##Action controller与controller##
controller对应的action controller继承于ActionController::Base，每个controller都有一个或者多个action(方法)，action构成所有的业务逻辑，action负责模版绘制，重定向，文本编辑等等。首先视图中的模块通过dispatcher连接到对应的controller，controller的处理过程是获得:action的键值，调用指定的action。每个action对应的模版在app/views/controllername目录下，名字相互对应，如果没有缺省的action和模版，就调用名为index的action，action每次只能完成一次绘制或者重定向。
##Action view与view##
view对应于action view继承于ActionView::Base，而ActionView::Base定义了三种模版。
>.rhtml文件，ruby代码嵌入html代码中，<% %>是嵌入的ruby处理代码，<=>是要显示的数据。<br>
>.rxml文件，生成xml文件。<br>
>.rjs文件，通过JavaScriptGenerator的对象page的方法对相应的html文件惊醒修改，这和rails的Ajax有关，之后我们在详细讨论。<br><br>
[版权所有](http://xeostream.github.com/blog/2013/03/16/rubyonrails-one)，欢迎转载注明出处与作者。
