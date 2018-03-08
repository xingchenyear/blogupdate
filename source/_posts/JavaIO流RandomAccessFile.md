---
title: JavaIO流RandomAccessFile
date: 2018-01-18 14:23:39
tags:
---

### RandomAccessFile
``` bash
java提供的对文件内容的访问，即可以读文件，也可以写文件
支持随机访问文件可以访问文件任意位置
```
<!--more-->
### java的文件模型
```bash
在硬盘上的文件时 byte byte byte存储的，是数据的集合
```

### 打开文件
```bash
两种模式：“rw”（读写），“r”（只读）
RandomAccessFile raf = new RandomAccessFile(File,"rw")
文件指针，打开文件时指针在开头 pointer = 0
```

### 写方法：
```bash
raf.write(int)--->只写一个字节（后8位），同时指针指向下一个位置，准备再次写入
```

### 读方法：
```bash
int b = raf.read()--->读一个字节
```

### 注意关闭流
```bash
raf.close()
```

### 常用操作
```bash
$ getFilePointer() 获取当前指针所在位置
$ write() 向文件写入byte
$ writeInt() 向文件写入int
$ length() 文件长度
$ read() 读取文件内容
$ seek() 移动指针位置第几位

```
### Code
```bash
 File demofile = new File("E://javaio");
        if(!demofile.exists()){
            demofile.mkdir();
        }

        File file = new File(demofile,"text.dat");
        if (!file.exists()){
            file.createNewFile();
        }
        RandomAccessFile randomAccessFile = new RandomAccessFile(file,"rw");
        System.out.println(randomAccessFile.getFilePointer());
        randomAccessFile.write('A');
        System.out.println(randomAccessFile.getFilePointer());
        randomAccessFile.write('B');
        int i =0x7fffffff;
        System.out.println(randomAccessFile.getFilePointer());
        randomAccessFile.write(i>>>24);
        randomAccessFile.write(i>>>16);
        randomAccessFile.write(i>>>8);
        randomAccessFile.write(i);
        System.out.println(randomAccessFile.getFilePointer());

        randomAccessFile.seek(0);
        byte[] buf = new byte[(int)randomAccessFile.length()];
        randomAccessFile.read(buf);
        System.out.println(Arrays.toString(buf));

        randomAccessFile.close();
```
 


