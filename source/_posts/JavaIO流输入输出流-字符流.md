---
title: JavaIO流输入输出流-字符流
date: 2018-01-19 23:18:24
tags:
---
## 字节字符转换流

### 认识文本和文本文件
```bash
java的文本(char)是16位无符号整数，是字符的unicode编码（双字节编码)
文件是byte byte byte ...的数据序列
文本文件是文本(char)序列按照某种编码方案(utf-8,utf-16be,gbk)序列化为byte的存储结果
```
<!--more-->
### 字符流(Reader Writer)---->操作的是文本文本文件
```bash
字符的处理，一次处理一个字符
字符的底层任然是基本的字节序列
字符流的基本实现
   InputStreamReader   完成byte流解析为char流,按照编码解析
   OutputStreamWriter  提供char流到byte流，按照编码处理  
   FileReader/FileWriter
```
### 字符流的过滤器
```bash
   //BufferedReader   ---->readLine 一次读一行
   //BufferedWriter/PrintWriter   ---->写一行    
 public static void brAndBw() throws IOException {
        BufferedReader br=null;
        BufferedWriter bw=null;
        try {
            br = new BufferedReader(new InputStreamReader(new FileInputStream("E:\\VSC.dat")));
            bw = new BufferedWriter(new OutputStreamWriter(new FileOutputStream("E:\\javaio\\VSC.dat")));
            String line;
            while ((line=br.readLine())!= null){
                System.out.println(line);
                bw.write(line);//无换行
                //bw.newLine();//添加换行
            }
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            bw.close();
            br.close();
        }
    }
```


### 读文件示例
```bash
 public static void main(String[] args) throws IOException {
        FileInputStream fis=null;
        InputStreamReader isr=null;
        try {
            fis = new FileInputStream("G://path.txt");
            isr = new InputStreamReader(fis,"utf-8");//默认为项目编码可指定
            int c;
            while ((c=isr.read())!=-1){
                System.out.print((char) c);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            isr.close();
            fis.close();
        }
```
### 文件复制示例
```bash
public static void main(String[] args) throws IOException {
        FileInputStream fis=null;
        InputStreamReader isr=null;
        FileOutputStream fos=null;
        OutputStreamWriter osw=null;
        try {
            fis = new FileInputStream("G://path.txt");
            isr = new InputStreamReader(fis,"utf-8");//默认为项目编码可指定
            fos = new FileOutputStream("G://path2.txt");
            osw = new OutputStreamWriter(fos);
            int c;
            char[] a = new char[1024];
            while ((c = isr.read(a,0,a.length)) != -1){
                String s = new String(a,0,c);
                System.out.println(s);
                osw.write(a,0,c);

            }
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            fis.close();
            osw.close();
        }


    }
```

## 字节字符读写流
```bash
public static void fileCopyByFrAndFw() throws IOException {
        FileWriter fw = null;
        FileReader fr = null;
        int c;
        char[] chars = new char[1024];
        try {
            fr = new FileReader("e://javaio//text.dat");
            fw = new FileWriter("e://text.dat");//FileWriter("e://text.dat",true)追加操作
            while ((c = fr.read(chars,0,chars.length))!=-1){
                System.out.println(chars);
                fw.write(chars,0,c);

            }
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            fw.close();
            fr.close();
        }

    }
```




