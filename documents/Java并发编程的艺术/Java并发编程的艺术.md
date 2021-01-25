---
typora-root-url: ./
---

# Java内存模型(JMM)

## Java内存模型的基础

**并发编程两个关键问题**

1. 线程之间如何通信
    * 共享内存：读写共享内存的数据
    * 消息传递
2. 线程之间如何同步

**同步**：程序中用于控制不同线程间操作发生相对顺序的机制

### java内存模型的抽象结构

线程之间共享变量存储在主内存中

每个线程都有私有的本地内存，存储了主内存中共享变量的副本

JMM抽象示意图

![java内存模型抽象结构](img\java内存模型抽象结构.png)

线程间通信示意图：

![java线程间通信示意](img\java线程间通信.png)

| 屏障类型   | 指令示例                   | 说明                                                         |
| ---------- | -------------------------- | ------------------------------------------------------------ |
| LoadLoad   | Load1；LoadLoad；Load2     | 确保Load1数据的装载先于Load2及所有后续装载指令的装载         |
| StoreStore | Store1；StoreStore；Store2 | 确保Store1数据对其他处理器可见（刷新到内存）先于Store2及所有后续存储指令的存储 |
| LoadStore  | Load1；LoadStore；Store2   | 确保Load1数据装载先于Store2及所有后续的存储指令刷新到内存    |
| StoreLoad  | Store1；StoreLoad；Load2   | 确保Store1数据对其他处理器变得可见（指刷新到内存）先于Load2及所有后续装载指令的装载。StoreLoad会使该屏障之前的所有内存访问指令（存储和装载指令）完成之后，才执行该屏障之后的内存访问指令 |

**happens-before**

如果一个操作要对另一个操作**可见（并不代表操作顺序一致）**，那么两个操作之间必须存在happens-before关系

## 重排序

**目的**：优化程序性能

**as-if-series**:不管怎么重排序，程序的执行结果不能被改变；为了遵守as-if-series，编译器和处理器不会对存在数据依赖关系的操作做重排序

## 顺序一致性

## volatile内存语义

* 当读一个volatile变量时，JMM会把该线程对应的本地内存置为无效，线程接下来将从主内存中读取共享变量（***注意是所有的共享变量，不光是volatile变量***）
* 当写一个volatile变量时，JMM会把该线程对应的本地内存中的共享变量值刷新到主内存（***注意是所有的共享变量，不光是volatile变量***）

![volatile重排序规则表](img\volatile重排序规则表.png)

**破坏重排序规则可能造成的问题示例**

- 普通写 -> volatile写

    解析：

```java
class Example {
    private boolean volatile flag = false;
    private int a = 0;
    
    public void A() {
        // 1. 普通写
        a = 1;
        // 2. volatile写
        flag = true;
    }
    
    public void B() {
        // 3. 普通读
        if(flag == true) {
            // 4. 普通写
            b = a;
        }
    }
}
```

- 普通读 -> volatile写

    解析：

```java
class Example {
    private boolean volatile flag = false;
    private int a = 1;
    
    public void A() {
        // 1. 普通读
        if(a == 0) {}
        	// 2. volatile写
        	flag = true;
    	}
    }
    
    public void B() {
        // 3. 普通读
        if(flag == true) {
            // 4. 普通写
            a = 3;
        }
    }
}
```

- volatile读 -> volatile写

    解析：

```java
class Example {
    
    private boolean volatile boolean flag = false;
    
    private int volatile int a = 0;
    
    private int b = 0;
    
    public void A() {
        // 1. volatile读
    	if(a == 1) {
            // 2. volatile写
            flag = true
        }
    }
    
    public void B() {
        // 3. volatile读
        if(flag == true) {
            // 4. 普通写
            b = 1;
        }
    }
}
```

- volatile写 -> volatile写

    解析：

```java
class Example {
    
    private volatile boolean flag = false;
    private volatile int a = 0;
    private int b = 0;
    
    public void A() {
        // 1. volatile写
        a = 1;
        // 2. volatile写
        flag = true;
    }
    
    public void B() {
        // 3. volatile读
        if(flag == true) {
            // 4. 普通写
            b = a;
        }
    }
}
```

- volatile写 -> volatile读

```java
class Example {
    private volatile int a = 0;
    private volatile int b = 0;
    private int c = 0;
    
    public void A() {
        c = 1;
		a = 1;
        c = b
    }
    
    public void B() {
        if(c == 1) {
            b = 1;
        }
    }
}
```

- volatile读 -> 普通读

```java

```

- volatile读 -> 普通写

```java

```

- volatile读 -> volatile读

```java

```

## 锁的内存语义

## final域的内存语义

## happens-before

## 双重检查锁定与延迟初始化

## Java内存模型综述



# Java并发编程基础

## 线程

### 概念

* 现代操作系统调度的最小单元，也叫轻量既进程，在一个进程里可以创建多个线程

* 线程拥有各自的计数器、堆栈和局部变量，并且能够访问共享的内存变量
* 处理器在线程上高速切换

### 为什么使用多线程

* 更多的处理器核心
* 更快的响应时间
* 更好的编程模型

### 线程优先级

**范围**： 1~10，默认为5，优先级高的线程分配到的CUP时间片高于优先级低的线程

**策略**：针对频繁阻塞的线程需要设置较高的优先级，CPU密集型的线程设置较低的线程优先级（防止CPU独占）

**注意**：程序的正确性不能依赖于线程的优先级，部分平台下不支持线程优先级的设定

### 线程的状态

| 状态名称 | 状态说明 |
| :-: | :------: |
| NEW | 初始状态，线程被创建，还没有调start()方法 |
| RUNNABLE | 运行状态，Java将就绪态和运行态统称为运行态 |
| BLOCKED | 阻塞状态：线程阻塞于锁 |
| WAITING | 等待状态：当前线程需要等待其他线程作出一些特定的动作（通知或中断） |
| TIME_WAITING | 超时等待状态：不同于WAITING，可以在指定时间内自行返回 |
| TERMINATED | 终止状态：当前线程已经执行完毕 |

![Java线程状态迁移](img\2021-01-18_232234.png)





