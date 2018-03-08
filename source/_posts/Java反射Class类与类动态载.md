---
title: Java反射Class类与类动态载
date: 2018-01-21 21:28:49
tags:
---


## Class类，类类型的概念
```bash
在面向对象的世界中，万事万物皆对象。Java语言中，静态的成员，
类是对象，类是Java.lang.class的实例对象
```
<!--more-->
### 如何表示
三种表示方式
```bash
 //1.实例在告诉我们任何一个类都有一个隐含的静态成员变量class
Class c1 = new Foo();
 //2.已知该类的对象通过getClass方法
Class c2 = Foo.getClass();
 //官方 c1，c2表示了Foo类的类类型（Class Type）
 //万事万物皆对象，类也是对象，是Class的对象 这个对象我们称为类类型
 // 个人理解类类型意思是 类的类型 
 //c1 or c2 都是Foo的类类型 但一个类只可能是Class类的一个实例对象
System.out.print(c1==c2)
 //3.通过类名字实例化
Class c3 = Class.forName("com.xc.Foo");//填全称 会有异常
 //通常可以通过类类型创建该类的实例对象
Foo f1 = (Foo)c1.newInstance //需要做强制类型转换 需要有无参构造方法 会有异常
```
## Class动态加载类的方法
> 通过Class.forName("类的全称")
## 类的加载方式（2种）
### 1、静态加载类
> 编译时，加载的类是静态加载类。
> Foo foo = new Foo();
> 创建对象是静态加载类，编译时就要加载所有要用到的类，如果其中 1 个类有问题，就无法通过编译。解决该问题，可以使用动态加载类。
```bash
class office
{
    pubic static void main(String[] args){
        if("wrod".equals(args[0])){
            Word word = new Word();
            word.start();
        }
        if("Excel".equals(args[0])){
            Excel excel = new Excel();
            excel.start();
        }
    }
}
class word 
{
    public void start(){
        System.out.print("word..start..");
    }
}
```
### 2、动态加载类
> 运行时，加载的类是动态加载类
> Class.forName("com.reflect.Foo");
> 编译时不检查用到的类是否存在，运行时再检查。
```bash
class officeBetter
{
    pubic static void main(String[] args){
       try{
        //动态加载
        Class c = Class.forNname(args[0]);
        office o = (office)c.newInstance();
       }catch(Expction e){  
           e.printStackTrace();
       }
    }
}
interface office
{
    void start();
}

class word implements office
{
    public void start(){
        System.out.print("word..start..");
    }
}
```
## 获取方法信息

>基本数据类型，void关键字都存在类类型
```bash
class.getMethods()方法获取是该类的所有public方法，包括从父类继承的方法；
class.getDeclareMethods()方法获取该类自行声明的所有方法，不论访问权限；
method.getName()获取方法名
method.getReturnType()获取方法的返回值
method.getParameterTypes()，获取方法的参数类型的类类型数组class[]
```
### 获取方法信息：

> 基本的数据类型，void关键字等都存在类类型

Class c = 基类.class （int,String,double,void等）

> Class类的基本API操作的
```bash
c.getName()可以获取类的名称
c.getSimpleName();//不包含包名的类的名称
c.getMethods()获取类的【public方法】集合，【包括继承来的】
*****
注意【所有方法都是Method类的对象】
c.getDeclaredMethods()获取的是所有该类【自己声明】的方法，【不问访问权限】
```
### Method类提供了一些操作方法的方法
```bash
1. getReturnType()得到该方法的返回值类型的类类型（class），如int.class String.class
2. getName()得到方法的名称
3. getParameterTypes()获得参数列表类型的类类型，如参数为(int,int)则得到
Class c1 = int.class; int的类类型
Class c2 = String.class; String类的类类型 String类字节码
Class c3 = double.class; double这个数据类类型的字节码表示方式
Class c4 = Double.class; Double这个类的类类型字节码表示方式
Class c5 = void.class; 表达了void这个类的类类型
getName为这个类的类类型的具体名称 
c1.getName ---> int
c2.getName ---> java.lang.String 类的全称
c2.getSimpleName ---> String 不包含包名的类的名称
```
```bash

public void printClassMessage(String obj){
         //获取类的类类型
        Class c = obj.getClass();
         //获取类的名称
        String className = c.getName();
        System.out.println("obj = [" + className + "]");
         //获取类的方法
         /**
          * Method类，是方法对象
          * 一个成员方法就是一个Method对象
          * getMethods()方法获取的是所有public的函数，
          * 包括从父类继承而来的
          *getDeclaredMethods()方法是获取该类所有自己声明的方法，不论访问权限
          */
        Method[] methods1 = c.getMethods();
        for (int i = 0; i <methods1.length ; i++) {
             //得到方法返回值类型
            Class returnType = methods1[i].getReturnType();
            System.out.println("返回值 = [" + returnType.getName() + "]");
             //得到方法名称
            System.out.println("方法名 = [" + methods1[i].getName() + "]");
             //获取参数类型
            Class[] paramType = methods1[i].getParameterTypes();
            for (Class class1:paramType) {
                System.out.println("参数类型 = [" + class1.getName() + "]");
            }
        }
    }
```


## 成员变量的类类型
>* 成员变量也是对象
>* java.lang.reflect.field
>* Field类封装了关于成员变量的操作
>* getFields()方法获取的是所有public的成员变量的信息
>* getDeclaredFields获取的是该类自己声明的成员变量的信息
>* @param obj
 ```bash    
    public void printBian(String obj){
        Class cl = obj.getClass();
        Field[] fs = cl.getDeclaredFields();
        for (int i = 0; i <fs.length ; i++){
            //得到成员变量的类型的类类型
             Class fieldType = fs[i].getType();
            String typeName = fieldType.getName();
            System.out.print(typeName +" ");
            //得到成员变量的名称
            String fieldName = fs[i].getName();
            System.out.println(fieldName);
        }
    }
```

## 构造函数的类类型
>* 构造函数是对象，java.long.Constructor 封装了构造函数的信息方法
>* getConstructors() 获取所有public的构造方法
>* @param obj

```bash
    public void printConstructor(String obj){
        Class c = obj.getClass();
        Constructor[] cs = c.getDeclaredConstructors();
        for (int i = 0; i <cs.length ; i++) {
            System.out.print(cs[i].getName()+" (");
            Class[] classes = cs[i].getParameterTypes();
            for (Class classtype:classes) {
                System.out.print(classtype.getName()+",");
            }
            System.out.println(")");
        }

    }
```

## 方法的反射

### 如何获取某个方法与方法的反射
>1. 方法的名称和方法的参数列表才能唯一确定一个方法
>2. 通过getmethod（方法名，参数...)
    getMethod获取的是public的方法
    getDelcaredMethod自己声明的方法
>3. 通过invoke（对象名，参数...）进行反射操作

```bash
public static void main(String[] args) {
        A a1 = new A();
        //获取a1的类类型
        Class c = a1.getClass();
        //获取类类型的方法
        try {
                //method 是获取名为print 参数为int，int的方法
                //getMethod获取的是public的方法
                //getDelcaredMethod自己声明的方法
            //Method m = c.getMethod("print",new Class[]{int.class,int.class});
            //Method m = c.getMethod("print",int.class,int.class);
            Method m = c.getMethod("print",String.class,String.class);
            //object 是方法的返回值没有返回值为null
            //m.invoke(a1,new Object[]{10,20});
            Object o = m.invoke(a1,"hello","world");
            // 当没有参数列表时
            //Method m2 = c.getMethod("print");
            Method m2 = c.getMethod("print",new Class[]{});
            m2.invoke(a1);
        } catch (Exception e) {
            e.printStackTrace();
        }

    }
}
class A{
    public void print(){
        System.out.println("空");
    }
    public void print(int a,int b){
        System.out.println(a+b);
    }
    public void print(String s1,String s2){
        System.out.println(s1+s2);
    }
```

## 集合泛型的本质

>* 编译之后集合的泛型是去泛型化的
>* Java中集合的泛型，是防止错误输入的，只在编译阶段有效，
>* 绕过编译就无效了
>* 验证：我们可以通过方法的反射来操作，绕过编译

```bash
public static void main(String[] args) {
		ArrayList list = new ArrayList();
		
		ArrayList<String> list1 = new ArrayList<String>();
		list1.add("hello");
		//list1.add(20);错误的
		Class c1 = list.getClass();
		Class c2 = list1.getClass();
		System.out.println(c1 == c2);
		//反射的操作都是编译之后的操作
		/*
		 * c1==c2结果返回true说明编译之后集合的泛型是去泛型化的
		 * Java中集合的泛型，是防止错误输入的，只在编译阶段有效，
		 * 绕过编译就无效了
		 * 验证：我们可以通过方法的反射来操作，绕过编译
		 */
		try {
			Method m = c2.getMethod("add", Object.class);
			m.invoke(list1, 20);//绕过编译操作就绕过了泛型
			System.out.println(list1.size());
			System.out.println(list1);
			/*for (String string : list1) {
				System.out.println(string);
			}*///现在不能这样遍历
		} catch (Exception e) {
		  e.printStackTrace();
		}
	}

```
