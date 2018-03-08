---
title: JavaSocket应用
date: 2018-01-23 22:50:50
tags:
---


## InetAdress类
> InetAdress类：没有构造方法，不能new一个对象
> InetAddress类用于标示网络上的硬件资源，表示互联网协议（IP)地址
获取本机的InetAddress实例

<!--more-->

```bash
public static void main(String[] args) throws UnknownHostException {

        InetAddress ia = InetAddress.getLocalHost();
        System.out.println("本机地址"+ia.getHostAddress());
        System.out.println("本机名"+ia.getHostName());
        System.out.println(ia.getCanonicalHostName());
        byte[] ads = ia.getAddress();
        System.out.println("本机IP"+ Arrays.toString(ads));
        System.out.println(ia);

        //InetAddress ia1 = InetAddress.getByName("2013-20161213BF");
        InetAddress ia1 = InetAddress.getByName("192.168.1.139");
        System.out.println("本机地址"+ia1.getHostAddress());
        System.out.println("本机名"+ia1.getHostName());

    }
```

## URL的应用

>1. URL (Uniform Resource Locator) 统一资源定位符，表示Internet上某一资源的地址
>2. URL由两部分组成：协议名称和资源名称，中间用冒号隔开
>3. 在Java.net包中，提供URL类中来表示URL

```bash
public static void main(String[] args) {

        try {
            //创建实例
            URL imooc = new URL("Http://www.imooc.com");
            //?表示传递的参数 #后面表示锚点
            URL url = new URL(imooc,"/index.html?username=tom#test");
            System.out.println("协议"+url.getProtocol());
            System.out.println("主机地址"+url.getHost());
            //如果未指定端口号，则使用默认端口号，此时getPost()方法返回值为-1
            System.out.println("端口"+url.getPort());
            System.out.println("文件路径"+url.getPath());
            System.out.println("文件名"+url.getFile());
            System.out.println("相对路径"+url.getRef());
            System.out.println("传入字符串"+url.getQuery());
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
    }
```

## 使用URL读取页面内容
```bash
public static void main(String[] args) {

        try {
            //创建实例
            URL imooc = new URL("Http://www.baidu.com");
            //通过URL来获取输入流
            InputStream is = imooc.openStream();
            //把字节输入流转化为字符输入流，制定字符编码
            InputStreamReader isr = new InputStreamReader(is,"utf-8");
            //为字符输入流添加缓冲
            BufferedReader br = new BufferedReader(isr);
            String data = br.readLine();//读取数据
            while (data!=null){
                System.out.println(data);
                data = br.readLine();
            }
            br.close();
            isr.close();
            is.close();
        } catch (MalformedURLException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## Socket通信模型
![](https://vking.top/img/Socket01.png)
## Socket通信实现
>1. 创建ServerSocket和Socket
>2. 打开链接到Socket的输入/输出流
>3. 按照协议对Socket进行读写操作
>4. 关闭输入输出流、关闭Socket

```bash
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.*;
/**
 * 基于TCP的socket通讯，实现用户登录
 * 服务端
 */
public class Server {
    public static void main(String[] args) {

        ServerSocket serverSocket = null;

        try {
            //1.创建serverSocket 服务端绑定端口为8888
            serverSocket = new ServerSocket(8888);
            //2.设定监听，等待链接
            Socket socket=  serverSocket.accept();
            //2.捕获输入流，并读取信息
            InputStream is= socket.getInputStream();
            InputStreamReader isr = new InputStreamReader(is);
            BufferedReader br = new BufferedReader(isr);
            String msg;
            while ( (msg = br.readLine()) != null){
                System.out.println("Server back message" + msg);
            }
            socket.shutdownInput();//关闭输入流
            //4.关闭资源
            br.close();
            isr.close();
            is.close();
            socket.close();
            serverSocket.close();
        } catch (IOException e) {
            e.printStackTrace();
        }

    }

}

```
```bash
import java.io.IOException;
import java.io.OutputStream;
import java.io.PrintWriter;
import java.net.Socket;
/**
 * 客户端
 */
public class client {


    public static void main(String[] args) {

        try {
            Socket socket = new Socket("localhost",8888);
            OutputStream os =socket.getOutputStream();
            PrintWriter pw = new PrintWriter(os);
            pw.write("用户名：admin;密码：123");
            pw.flush();
            socket.shutdownOutput();

            pw.close();
            os.close();
            socket.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```
## 多线程服务器
> 应用多线程来实现服务器与多客户端之间的通信
基本步骤
1.服务器端创建ServerSocket，循环调用accept()等待客户端连接
2.客户端创建一个socket并请求和服务器端连接
3.服务器端接收客户端请求，创建socket与该客户建立专线连接
4.建立连接的两个socket在一个单独的线程上对话
5.服务器端继续等待新的连接

```bash
import java.io.*;
import java.net.Socket;

/**
 * 服务器端线程处理类
 */
public class threadServer extends Thread {

    Socket socket = null;
    public threadServer(Socket socket){
        this.socket = socket;
    }

    @Override
    public void run() {
        InputStream is = null;
        InputStreamReader isr = null;
        BufferedReader br = null;
        OutputStream os = null;
        PrintWriter pw = null;
        try {
            //捕获输入流，并读取信息
            is= socket.getInputStream();
            isr = new InputStreamReader(is);
            br = new BufferedReader(isr);
            String msg;
            while ( (msg = br.readLine()) != null) {
                System.out.println("Server back message" + msg);
            }
            socket.shutdownInput();//关闭输入流

            os = socket.getOutputStream();
            pw = new PrintWriter(os);
            pw.write("Server Connected:欢迎");
            pw.flush();
            socket.shutdownOutput();//关闭输出流

        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            try {
            if(pw != null)
                pw.close();
            if (os !=null)
                os.close();
            if (br != null)
                br.close();
            if (isr != null)
                isr.close();
            if (is != null)
                is.close();
            socket.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

    }
}
```

```bash
import JavaSocket.threadServer;

import java.io.*;
import java.net.*;

/**
 * 基于TCP的socket通讯，实现用户登录
 * 服务端
 */
public class Server {
    public static void main(String[] args) {

        ServerSocket serverSocket = null;
        int count = 0;
        try {
            //1.创建serverSocket 服务端绑定端口为8888
            serverSocket = new ServerSocket(8888);
	    System.out.println("***服务器已启动，等待客户端链接***");
            while (true) {
           	 //设定监听，等待链接
                Socket socket = serverSocket.accept();
                threadServer threadServer = new threadServer(socket);
                threadServer.start();
                count++;
                System.out.println("共有["+count+"]客户端");
                System.out.println("您是第["+count+"]个");
                InetAddress ia = socket.getInetAddress();
                System.out.println("第["+count+"]个链接你的地址为"+ia.getAddress());
                System.out.println("第["+count+"]个链接你的主机地址为"+ia.getHostAddress());
                System.out.println("第["+count+"]个链接你的主机名称"+ia.getHostName());
            }
        } catch (IOException e) {
            e.printStackTrace();
        }

    }

}
```

```bash
import java.io.*;
import java.net.Socket;

/**
 * 客户端
 */
public class client {


    public static void main(String[] args) {

        try {
            Socket socket = new Socket("localhost",8888);
            OutputStream os =socket.getOutputStream();
            PrintWriter pw = new PrintWriter(os);
            pw.write("用户名：cking;密码：123");
            pw.flush();
            socket.shutdownOutput();//关闭输出流
            InputStream is= socket.getInputStream();
            InputStreamReader isr = new InputStreamReader(is);
            BufferedReader br = new BufferedReader(isr);
            String msg;
            while ( (msg = br.readLine()) != null) {
                System.out.println("Server back message" + msg);
            }
            socket.shutdownInput();//关闭输入流
            br.close();
            isr.close();
            is.close();
            pw.close();
            os.close();
            socket.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```

## 基于UDP的Socket编程

> 1.UDP协议（用户数据报协议）是无连接、不可靠、无序的，特点是速度比较快
2.进行数据传输时i，首先要将要传输的数据定义成数据报（Datagram)，在数据报中指明数据所要达到的Socket（主机地址和端口号）,然后再将数据报发送出去
3.相关的操作类：DatagramPacket:表示数据报包 DatagramSocket:进行端到端通信的类

### 服务器端
> 1.创建DatagramSocket,指定端口号
2.创建DatagramPacket
3.接收客户端发送的数据信息
4.读取数据


### 客户端实现步骤
> 1.定义发送信息
2.创建DatagramPacket,包含将要发送的信息
3.创建DatagramSocket
4.发送数据

```bash
mport java.io.IOException;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.InetAddress;
import java.net.SocketException;

/**
 * 服务器端
 */
public class UDPServer {
    public static void main(String[] args) {


        try {
            //创建服务器端DatagramSocket,指定端口
            DatagramSocket socket = new DatagramSocket(8800);
            //创建数据报，用于接受客户端发送的数据
            byte[] bytes = new byte[1024];
            DatagramPacket dp = new DatagramPacket(bytes,bytes.length);
            //接受客户端的数据
            System.out.println("**服务器已启动等待客户端数据**");
            socket.receive(dp);//此方法在接受到数据之前一直处于阻塞
            //读取数据
            String info = new String(bytes,0,dp.getLength());
            System.out.println("Client msg ："+info);

            /**
             * 响应数据
             */
            //定义客户端地址、端口号、数据
            InetAddress ia =InetAddress.getByName("localhost");
            int port = dp.getPort();
            byte[] data = "欢迎".getBytes();
            //创建数据报，包含发送信息
            DatagramPacket packet = new DatagramPacket(data,data.length,ia,port);
            //发送数据报
            socket.send(packet);
            //关闭资源
            socket.close();
        } catch (SocketException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

}
import java.io.IOException;
import java.net.*;
```
```bash
/**
 * 客户端
 */
public class UDPClient {

    public static void main(String[] args) {
        try {
            //定义服务器的地址、端口号、数据
            InetAddress ia =InetAddress.getByName("localhost");
            int port = 8800;
            byte[] data = "用户名:admin;密码:123".getBytes();
            //创建数据报，包含发送信息
            DatagramPacket packet = new DatagramPacket(data,data.length,ia,port);
            //创建DatagramSocket对象
            DatagramSocket socket = new DatagramSocket();
            //发送数据报
            socket.send(packet);
            /*
            就收响应数据
             */
            //创建数据报，用于接受服务器响应数据
            byte[] bytes = new byte[1024];
            DatagramPacket dp = new DatagramPacket(bytes,bytes.length);
            //接受客户端的数据
            socket.receive(dp);//此方法在接受到数据之前一直处于阻塞
            //读取数据
            String info = new String(bytes,0,dp.getLength());
            System.out.println("My Client Server msg ："+info);
            socket.close();
        } catch (UnknownHostException e) {
            e.printStackTrace();
        } catch (SocketException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }



}
```

## 总结
> 1.使用多线程编写网络程序时，可以将线程的优先级降低，线程的优先级默认是五，降低到四.这是经验
2.在最后直接关闭socket即可，socket的关闭会自动的关闭输入输出流
3.可以将一个对象序列化之后，通过流的方式在网络上传输
4.网络上传输文件，也是将文件转换为流，通过流的方式在网络上传输
