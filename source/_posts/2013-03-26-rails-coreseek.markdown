---
layout: post
title: "coreseek配置rails项目全文搜索"
date: 2013-03-26 22:07
comments: true
categories: [Study, ROR]
---
rails是ruby的web框架，由于rails框架的易用性，近几年出现了很多基于rails的网站，随着网站的发展，积累的数据会越来越多，有的时候我们可能要给网站升级，比如增加全文搜索功能，那就要用到我们说到的coreseek软件，coreseek是一款中文全文检索软件，coreseek本身是基于sphinx开发的，接下来介绍coreseek的安装与配置(需要安装包的可以给我留言)。
<!--more-->
##安装coreseek##
1.首先在安装coreseek之前，配置环境，安装编译需要的一些软件，运行命令:
```sh
sudo su
apt-get install make gcc g++ automake libtool mysql-client libmysqlclient15-dev libxml2-dev libexpat1-dev
```
2.bash中进入解压好的文件夹下，可以看到csft%文件夹和mmseg%文件夹等，首先要安装mmseg，如果成功之后，继续安装csft。<br>
3.进入mmseg文件夹中，开始安装mmseg，执行命令：
```sh
./bootstrap
./configure --prefix=path #path为mmseg的安装目录，例/usr/local/bin/mmseg3等
make
make install
cd ..
```
4.进入csft文件夹下，执行命令：
```sh	
sh buildconf.sh
./configure --prefix=/usr/local/bin/coreseek --without-unixodbc --with-mmseg --with-mmseg-includes=/usr/local/bin/mmseg3/include/mmseg/ --with-mmseg-libs=/usr/local/bin/mmseg3/lib/ --with-mysql #
第一个prefix参数值将做为coreseek的安装目录，之后的参数完全参照mmseg的安装目录进行设置
make
make install
cd ..
```
5.测试coreseek是否安装成功，执行如下命令：
```sh	
cd testpack
cat var/test/test.xml
/usr/local/bin/mmseg3/bin/mmseg -d /usr/local/bin/mmseg3/etc var/test/test.xml
/usr/local/bin/coreseek/bin/indexer -c etc/csft.conf --all #这里报错段错误，解决:打开csft.conf修改charset_dictpath为uni.lib目录
/usr/local/bin/coreseek/bin/search -c etc/csft.conf 网络 #如果coreseek安装成功，这时应该会返回一定的根据关键字’网络‘搜索到的结果。
```
##coreseek配置##
coreseek安装成功之后，就是在rails项目中配置coreseek，我们也可以总结为几个步骤。<br>
1.进入rails项目文件夹下config文件夹下新建sphinx.yml,配置文件很重要，需要与安装目录项目对应，配置代码大致如下：
```sh
development:
charset_type: zh_cn.utf-8
charset_dictpath: /usr/local/coreseek/var/data/
bin_path: /usr/local/bin
searchd_binary_name: searchd
indexer_binary_name: indexer
...
```
2.在bash中，cd到当前rails项目目录下，运行命令：
```sh	
rake ts:index #为配置的model建立索引
如果有报段错误之类的，这说明之前生成的中文字典出现问题，需要重新生成中文字典，在bash中重新进入到mmseg/data文件夹下

python build_unigram.py char.stat.txt Lexicon_full_words.txt > unigram.txt
mmseg -u unigram.txt #此命令会生成unigram.txt.uni
```
将生成的unigram.txt.uni重命名为uni.lib,将其复制到coreseek的安装目录/var/data/<br>
再次运行命令:
```sh
rake ts:index
```
配置的model的索引应该就成功建立。<br>
3.开启coreseek服务，可以在rails项目的console下运行search方法进行全文检索。
```sh
rake ts:start
```		
这些天一直在看coreseek配置，由于coreseek的官网无法访问，所以其中曲折只有亲身体会才会知道，本人才疏学浅，如有异议，还请不吝赐教。	
[版权所有](http://xeostream.github.com/blog/2013/03/26/rails-coreseek)，欢迎转载注明出处与作者。