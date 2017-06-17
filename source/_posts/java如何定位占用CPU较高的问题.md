---
title: java如何定位占用CPU较高的问题
date: 2016-12-12 21:06:33
tags: java调试
categories: JAVA
---
编写Java项目时，经常会遇到两种问题。
* 1.Java进程占用CPU比较高
* 2.Java进程堆内存溢出  

今天主要跟大家聊一聊第一种问题如何排查
<!-- more -->
### 确定消耗CPU的Java进程

![](/uploads/定位占用CPU比较高的问题/1.png )  

从上图可以看到Java进程 27459 消耗的CPU比较高。
### 查找 27459 Java进程消耗CPU线程ID

方式一：`top -p [pid]`
执行命令后按`shift+h`(显示进程各线程运行情况)
- `top -p 27459`
- `shift + h`

如下图：  
![](/uploads/定位占用CPU比较高的问题/2.png )  

方式二: `ps -mp [pid] -o THREAD,tid,time`  
`ps -mp 27459 -o THREAD,tid,time`  
![](/uploads/定位占用CPU比较高的问题/3.png )  
方式三: `pidstat -p [pid] -t 1`

- `pidstat -p 27459 -t 1`

备注：pidstat命令需要安装sysstat软件包。 
![](/uploads/定位占用CPU比较高的问题/4.png ) 

从上面三种方式都可以看出PID是 27460的线程占用CPU比较高。
### 根据Java进程堆栈信息找到对应线程ID
- 1.对应线程ID转换成16进程  
`27460 = 0x6b44`
- 2.`jstack [pid]`  
 `jstack 27460 >> 27460.log`  
   
在27460.log文件中查找0x6b44对应的线程，如下图：   
![](/uploads/定位占用CPU比较高的问题/5.png ) 
备注：

- 1.jstack 输出的堆栈信息，线程id对应的16进制为小写，查找时要统一按照小写方式查找

- 2.jstack输出为当前瞬间的堆栈信息，如果遇到间断性出现CPU高的问题时，需要多输出几次 

从上面方式定位到代码Test.main(Test.java:4)处导致了CPU偏高的问题，那我们查看下代码具体如何实现的？

代码实现：
```java
	public class Test {
        public static void main(String[] args) {
            while(true) {}
        }
	}
```
从代码层面看该处实现了一个死循环，所以导致了线程占用CPU偏高的问题。