---
title: 'Java多线程 '
date: 2018-01-26 11:46:46
tags:
---
## Java多线程基础概念

> 程序是指令、数据及其组织形式的描述，进程是程序的实体。
进程：程序（任务）的执行过程，具有动态性；持有资源（共享内存，共享文件）和线程，是系统进行资源分配和调度的基本单位。
线程：有时被称为轻量级进程，是程序执行流的最小单元，线程共享进程的所有资源
进程和线程的关系：一对多，同一进程可以拥有多个线程
线程之间的关系（交互）：互斥、同步

<!--more-->

## 线程的实现方法

> 1、继承java.lang包下的Thread类，覆写Thread的run()方法
2、实现java.lang.Runnable接口，同样在run()方法中实现运行在线程上的代码

![](https://vking.top/img/Thread01.jpg)

### volatile

> volatile保证了线程能够正确的读取其他线程写入的值,解决了可见性的问题

### 多线程目的
> 多线程编程的目的，就是"最大限度地利用CPU资源",当某一线程的处理不需要占用CPU而只和I/O,OEMBIOS等资源打交道时，让需要占用CPU资源的其它线程有机会获得CPU资源。

```bash

/**
 * 线程1
 */
public class ArmyRunnable implements Runnable {

    //volatile 保证了可以正确读取其他线程写入的值
    //因为可见性的问题可能无法正确读到值可见性 jef JMM
    volatile boolean keepRunning = true;

    @Override
    public void run() {

        while (keepRunning){
            for (int i = 0; i < 5 ; i++) {
                System.out.println(Thread.currentThread().getName()+"当前执行["+i+"]");
                //让出处理器时间，下次调用不一定会调用哪个

                Thread.yield();
            }

        }
        System.out.println(Thread.currentThread().getName()+"执行结束");
    }
}

```

```bash

/**
 * 第二个线程
 */
public class PersonThread extends Thread{

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"03线程开始了");
        for (int i = 0; i < 10; i++) {
            System.out.println(Thread.currentThread().getName()+"03线程正在执行");

        }

        System.out.println(Thread.currentThread().getName()+"03线程结束了");

    }
}

```

```bash

/**
 * RUNING
 */
public class Stage extends Thread{

    @Override
    public void run() {

        System.out.println("Ready Go!!!");

        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }


        ArmyRunnable armyRunnable1 = new ArmyRunnable();
        ArmyRunnable armyRunnable2 = new ArmyRunnable();

        Thread thread1 = new Thread(armyRunnable1,"线程一");
        Thread thread2 = new Thread(armyRunnable2,"线程2");

        //启动线程
        thread1.start();
        thread2.start();

        //当前本线程休眠
        try {
            Thread.sleep(50);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        //线程03开始执行
        System.out.println("03线程开始执行");

        Thread sanCheng = new PersonThread();
        sanCheng.setName("03线程");
        System.out.println("03线程运行中");


        armyRunnable1.keepRunning = false;
        armyRunnable2.keepRunning = false;

        try {
            thread2.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        sanCheng.start();

        try {
            sanCheng.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("运行结束");

    }

    public static void main(String[] args) {
        new Stage().start();
    }
}

```

## 关于线程停止

> interrupt()方法不会中断一个正在运行的线程。这一方法实际上完成的是，在线程受到阻塞时抛出一个中断信号，这样线程就得以退出阻塞的状态。更确切的说，如果线程被Object.wait, Thread.join和Thread.sleep三种方法之一阻塞，那么，它将接收到一个中断异常（InterruptedException），从而提早地终结被阻塞状态，然后该线程还是继续运行的
@Java线程——如何正确停止线程
一、错误一：stop()方法
1、not stop：stop()方法会使线程戛然而止
2、使程序突然中止，无法完成完整的业务步骤，也无法进行清理工作
二、错误二：interrupt()方法
1、interrupt()方法只能设置interrupt标志位（且在线程阻塞情况下，标志位会被清除，更无法设置中断标志位），无法停止线程
三、正确方法：设置退出标志 
1、使用退出标志位来停止while循环
2、完成最后一次业务后跳出while循环后，之后进行一些清理工作



### 本人阵亡 线程是啥 啥是线程 我不知道wait synchronize notify notifyAll 我失忆了 
### wait set  Critical Section 都不知道  
