---
title: JavaIO流输入输出流-字节流
date: 2018-01-18 17:14:16
tags:
---
### IO流分为输入流、输出流
输入输出流 又分为字节流、字符流

### 字节流
1. InputStream：抽象了应用程序读取数据的方式 
2. OutputStream：抽象了应用程序写 出数据的方式
3. EOF = End 读到-1就读到结尾

<!--more-->

### 输入流基本方法
```bash
$ int b = in.read();读取一个字节无符号填充到int低八位.-1是EOF
$ in.read(byte[] buf) 读取数据填充到字节数组buf
$ in.read(byte[] buf,int start, int size)读取数据到字节数组buf从buf的start位置开始存放size长度分数据
```

### 输出流的基本方法
```bash
$ out.write(int b) //写出一个byte到流，b的低8位
$ out.write(byte[] buf) //将buf字节数组都写到流
$ out.write(byte[] buf, int start,int size) //字节数组buf从start位置开始写size长度的字节到流
```

## FileInputStream
### 具体实现了在文件上读取数据
## FileInputStream
### 实现了向文件写出byte数据方法

```bash
$ public static void printHex(String fileName) throws IOException {
$         //把文件转化为字节流十六进制进行读操作
$         FileInputStream fis = new FileInputStream(fileName);
$         int b;
$         int i = 1;
$         while ((b=fis.read()) != -1){
$             if(b <= 0xf ){System.out.print("0");}
$             System.out.print(Integer.toHexString(b)+" ");
$             if (i%10==0){
$                 System.out.println();
$             }
$             i++;
$         }
$         fis.close();
$     }
```
## 使用字节数组读取文件
```bash
$  //不确定文件大小（推荐）
$  FileInputStream fis = new FileInputStream(fileName);
$        byte[] bytes = new byte[1024];
$        int num;
$        while ((num=fis.read(bytes,0,bytes.length))!=-1){
$            for (int i = 0; i < num ; i++) {
$                //& 0xff byte为8位,int为32位为避免数据转换出错将高24位去除
$                System.out.print(Integer.toHexString(bytes[i] & 0xff)+" ");
$            }
$        }
```

```bash
$ FileInputStream fis = new FileInputStream(fileName);
$        byte[] bytes = new byte[1024];
$        int b;
$        int i=1;
$        if ((b=fis.read(bytes,0,bytes.length))!=-1){
$            for (int j = 0; j < b; j++) {
$                System.out.print(Integer.toHexString(bytes[i] & 0xff)+ " ");
$                if (i%10==0)
$                    System.out.println();
$                i++;
$            }
$        }
$        fis.close();
```
## FileOutPutStream
```bash
$ FileOutputStream fos = new FileOutputStream(fileName);
$        fos.write('A');
$        fos.write('B');
$        int a =10;
$        fos.write(a>>>24);
$        fos.write(a>>>16);
$        fos.write(a>>>8);
$        fos.write(a);
$        byte[] utf8 = "中国".getBytes("utf-8");
$        fos.write(utf8);
$        fos.close(); 
```
## 文件复制操作
```bash
$   public static void copyFile(File srcFile,File destFile)throws IOException{
$        if(!srcFile.exists()){
$            new IllegalArgumentException("文件/文件夹不存在");
$        }
$        if (srcFile.isFile()){
$            new IllegalArgumentException("目标不是一个文件");
$        }
$        FileInputStream fis = new FileInputStream(srcFile);
$        FileOutputStream fos = new FileOutputStream(destFile);
$        byte[] bytes = new byte[1024];
$        int num ;
$        while ((num=fis.read(bytes,0,bytes.length)) != -1){
$                fos.write(bytes,0,num);
$                fos.flush();
$        }
$
$       fis.close();
$        fos.close();
$   	}
```
## DataInputStream/DataOutPutStream
### 是对“流”的扩展,可以更加方便读取int,long,String等类型数据

## DataInputStream
### writeInt()/writeDouble()/writeUTF()


## BufferedInputStream/BufferedOutputStream
这两个流类位IO提供了带缓冲区的操作，一般打开文件进行写入或读取是，都会加上缓冲，这种流模式提高了IO的性能从应用程序中把输入放入文件，相当于将一缸水倒入另一缸水

### FileOutputStream、DataoutputStream与BufferedOutputStream:
1. FileOutputStream--->write()方法相当于一滴一滴地把水“转移”过去
2. DataOutputStream-->writeXxx()方法会方便一些，相当于一瓢一瓢（比如int 4滴4滴转移）把水“转移”过去
3. BufferedOutputStream--->write方法更方便，相当于一飘一瓢先放入桶中(缓冲区)，再从桶中倒入到另一个缸中
4. 读写效率对比：使用字节数组作为缓冲的流 > 使用原生缓冲的流 > 不使用缓冲的流
```bash
//计算时延
long start=System.currentTimeMillis();
long end=System.currentTimeMillis();
System.out.println(end-start);
```
### 后记
```bash
使用缓冲字节流复制确实是最快的方式，但对于小文件10M以下的文件体现不出优势，对于百兆文件正确使用，时间可以控制到50ms内
复制文件最快的做法是将批量读取到的字节数组使用缓冲写入到文件，在机器性能范围内字节数组越大越快。
在循环写入的过程中不需要使用flush，缓冲输入流在关闭的时候会将所有缓冲区的数据全部写入文件，
使用flush刷新缓冲就失去了缓冲的意义。最后关闭IO流和文件流应该在finally中关闭，
否则IO异常时执行不到close语句，IO流仍然没有关闭。
```

