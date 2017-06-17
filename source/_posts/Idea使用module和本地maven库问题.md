---
title: Idea使用module和本地maven库问题
date: 2016-12-26 16:59:27
tags: 
	- maven
	- IDEA 
categories: IDEA
---
### 问题1  module间引用

* 现在我有一个项目，然后里面有三个模块，manager引用了contact的jar包，那如何在调试的时候可以进入contract项目呢，结构如下 
<!-- more -->
![](http://img.blog.csdn.net/20161024113956453 ) 

* 在文件–项目结构 菜单下，进入modules 
![](http://img.blog.csdn.net/20161024114302594 )  


 * 添加一个模块的引用就可以了，打码的那个就是结果 
![](http://img.blog.csdn.net/20161024114400704 ) 

### 问题2  install项目到本地maven库

如何让manage站点使用contract修改后的jar包，而且不需要将contract上传到nexus库里 
调试的时候，并不希望每次修改一个contract接口jar包还得上传，所以我们可以这样 
* 确定你的manage引用的contract版本和你contract项目的版本一致 
![](http://img.blog.csdn.net/20161024114658502 )  
  ![](http://img.blog.csdn.net/20161024114816625 )
* 然后maven-install你的contract项目，这样你本地的maven 库会更新，如果没更新，删掉重新打包 
![](http://img.blog.csdn.net/20161024115429493 )

搞定!