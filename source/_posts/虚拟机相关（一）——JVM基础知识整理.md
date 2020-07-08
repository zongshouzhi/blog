---
title: 虚拟机相关（一）——JVM基础知识整理
categories: java
date: 2020-07-03 14:54:28
tags:
---




###### 效果与问题

* static 的变量为啥可以不用实例化对象？
* 使用static的变量会不会引起类加载？
* 未加载的类能不能反射到？
* import会不会引起类加载？无用的import要不要去掉？
* 所有的对象都分配在堆内存上吗？

###### 常量池

* 字符串常量池（字符串和包装类-127——128）在堆。运行时常量池在本地内存。

  先看最为熟悉的例子：

  ```java
  String a = new String("a");
  String b = new String("a");
  String c = "a";
  String d = "a";
  System.out.println(a == c);//false
  System.out.println(d == c);//true
  System.out.println(a == b);//false
  ```

   引发上面表现的原因，就是jvm内存分配导致的。在jvm堆内存中有一块被分配为常量池。

  注：堆中的常量池为 字符串常量池，及包括字符串和包装类-127——128的对象区间。真正的常量池在JDK1.6中位于方法区中，1.8之后跟着方法区一起分配在native内存中，脱离了jvm的内存的管理与大小限制。

* 类的常量池（class文件常量池）

  这个常量池跟我们要讲的jvm的常量池是两个概念，但是又是强相关的。所以需要先来把这个说清楚。

  每一个.java文件被编译成.class文件后，里面都有一个常量池的描述，在类加载的时候被jvm解析使用。

  看下最简单的例子：

  ```java
  package com.company;
  import other.Test1;
  public class Test {
      public Test(){
          new Test1();
      }
  }
  ```

​	   编译之后用javap -v Test 查看字节码内容：

```java
警告: 二进制文件Test包含com.company.Test
Classfile /Users/whartonyang/IdeaProjects/DemoYang/out/production/DemoYang/com/company/Test.class
  Last modified 2020-6-30; size 299 bytes
  MD5 checksum 1281ebfd5d4f1448cd4efe5f7c6a110c
  Compiled from "Test.java"
public class com.company.Test
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #5.#15         // java/lang/Object."<init>":()V
   #2 = Class              #16            // other/Test1
   #3 = Methodref          #2.#15         // other/Test1."<init>":()V
   #4 = Class              #17            // com/company/Test
   #5 = Class              #18            // java/lang/Object
   #6 = Utf8               <init>
   #7 = Utf8               ()V
   #8 = Utf8               Code
   #9 = Utf8               LineNumberTable
  #10 = Utf8               LocalVariableTable
  #11 = Utf8               this
  #12 = Utf8               Lcom/company/Test;
  #13 = Utf8               SourceFile
  #14 = Utf8               Test.java
  #15 = NameAndType        #6:#7          // "<init>":()V
  #16 = Utf8               other/Test1
  #17 = Utf8               com/company/Test
  #18 = Utf8               java/lang/Object
{
  public com.company.Test();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=2, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: new           #2                  // class other/Test1
         7: dup
         8: invokespecial #3                  // Method other/Test1."<init>":()V
        11: pop
        12: return
      LineNumberTable:
        line 4: 0
        line 5: 4
        line 6: 12
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0      13     0  this   Lcom/company/Test;
}
SourceFile: "Test.java"

```

Constant pool:所描述的内容则为编译后此文件的常量池部分。到此，我们可以解答一个问题：无用的import要不要删除？要的，不然最少是要生成常量池中一个常量，后面类加载的时候也会被jvm解析到常量池。（现在编译器在编译阶段都会优化掉没有使用的import）。

常量池描述了该文件的类名，字段，方法，父类，引用类的信息。

* 类信息常量池

  把每一个类的常量池抽象成一个描述对象，存储于常量池。

  常量池是不定大小的，每加载一个类就需要申请一定大小的内存来存放。

  注：常量池存储的只是一个描述信息和各个常量实力指针，真正的常量实例存储在方法区（永生代）

##### 类的生命周期

* 加载

  概述：就是.class文件加载到机器内存中，并在内存中构建出java类的原型——类模版对象。

  ```c++
  //类模版描述
  instanceKlass：
  jint _layout_helper;//布局类型
  juint _super_check_offset;
  Symbly* _name;//类名
  objArrayOop _methods;//java 类中定义的方法信息
  typeArrayOop _fields;//java 类中定义的字段信息
  oop _class_loader;//类加载器
  typeArrayOop _inner_classese;//内部类
  int _nonstatic_field_size;//非静态字段的大小
  int _static_field_size;//静态字段大小
  int _vtable_len;//虚方法表长度
  
  ```

  注：1.虚方法表是用来实现多态的。jvm用c++实现，virtual和虚方法表是c++实现多态的方式。java的每一个方法在解释成C++类实例的时候都被认为是一个虚方法。

  2.反射也是借助类模版来实现。

  3.import会不会引起类加载，无用的import在编译期间已经被编译器优化掉。使用到import会不会在加载这个类的时候引起被import类的类加载？

  事实上，import语句仅仅是个语法糖，是为了不写那一长串的全限定名。

  类的加载时机：new 关键字实例化对象，读写类的静态变量，反射加载类。

  预加载：JVM启动期间，会先加载一部分核心类库，包括：Object，String，Class，Cloneable，ClassLoader，Serializable，Thread，ThreadGroup，Properties，AccessibleObject,Field,Method,Float,Double,Byte,Short,Integer,Long。

  

* 连接

  概述：将字节码指令中对常量池中的索引引用转换为直接引用。

* 初始化

  概述：调用编译器生成<clinit>方法。进行静态变量及静态代码块初始化。

* 使用

  概述：程序实例化对象

  所有的对象都分配在堆内存上吗？

  解答上面问题需要知道两个概念：

  JIT（just-in-time）即时编译：在运行时将热点字节码编译为机器码，从而改善字节码编译语言性能的技术。

  逃逸：是指一个在方法内部被创建的对象不仅在方法内部被引用，还在方法外部被其他变量引用，这带来的后果是：在该方法执行完毕之后，改方法中创建的对象无法被GC回收，因为对象在方法外部还被引用着。

  逃逸分析很耗cpu资源，因此JVM不可能对每一个方法里的变量都进行逃逸分析，只有JVM触发JIT编译时才会进行逃逸分析。

  栈上分配：在未发生逃逸的情况下，及有可能将小的实例直接分配在栈上（栈上放不下几个大对象，所有分配的时候还会考虑对象所占空间的大小）

  堆内存分配的优化：

  TLAB（Thread Local Allocation Buffer）：线程本地分配缓存区，这是一个线程专用的内存分配区域。

  TLAB 能够解决直接在堆上安全分配所带来的线程同步性能消耗问题。堆内存时全局的，任何线程都能够在堆上申请空间，因此每次申请堆空间时都必须进行同步处理。

* 卸载

  概述：JVM垃圾回收，回收掉没有使用的类模版。

