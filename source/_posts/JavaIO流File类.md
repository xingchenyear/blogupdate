---
title: JavaIO流File类
date: 2018-01-17 17:44:11
tags:
---
## JavaIO流File类
### File类常用操作

``` bash
$ exists()常用于判断文件或目录是否存在
$ delete() 删除文件/文件夹。  
$ separator 设置分隔符。（\\或/）  
$ isFile() 判断是否为文件。（若文件不存在，也返回false）  
$ createNewFile() 创建文件(注意文件与文件夹的区别)。  
$ getAbsolutePath() 返回绝对路径。  
$ getName() 返回文件/目录的名字。  
$ getParent() 返回父目录路径。
$ list() 用于列出当前目录的子目录不包含子目录下的内容
$ mkdir() 用于创建文件夹
$ 
```
<!--more-->
### 递归
``` bash
$ public static void file3(File dir){
$         if (dir.isDirectory()) {
$            File[] files = dir.listFiles();
$           for (File file:files) {
$                 file3(file);
$             }
$         }else {
$             System.out.println(dir.toString());
$         }
$    }
$
```




