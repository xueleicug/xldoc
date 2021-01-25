

# 自增变量

```java
public static void main(String[] args) {
    int i = 1;
    i = i++;
    int j = i++;
    int k = i + ++i * i++;
    System.out.println("i = " + i);
    System.out.println("j = " + j);
    System.out.println("k = " + k);
}
```

# 单例模式

**要点**： 

1. 某个类只能有一个实例

    构造器私有化

2. 必须自行创建这个实例

    含有一个该类的静态变量来保存这个唯一的实例

3. 必须自行向整个系统提供这个实例

    对外提供获取该实例对象的方式

## 饿汉式

在类初始化时直接创建对象

```java
// 直接创建
// 不管是否需要这个对象，都直接创建
public class Singleton {
    
    public static final Sigleton INSTANCE = new Sigleton();
    
    private Singleton() {
    }
}
```

```java
// 枚举类型：表示该类型的对象只有有限的几个
// 限定为一个即为单例
public enum Singleton {
    INSTANCE
}
```

```java
// 静态代码块饿汉式
public class Singleton {
    
    public static final Singleton INSTANCE;
    
    static {
        // 可以进行一些初始化动作
        INSTANCE = new Singleton();
    }
    
    private Singleton() {
        
    }
}
```

## 懒汉式

```java
// 适用于单线程
public class Singleton {
    
    private static Singleton instance;
    
    private Singleton() {
        
    }
    
    public static Singleton getInstance() {
        if(instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

```java
// 适用于多线程
public class Singleton {
    
    private static volatile Singleton instance;
    
    private Singleton() {
        
    }
    
    public static Singleton getInstance() {
        //提高效率
        if(instance == null) {}
        	synchronized (Singleton.class) {
            	if(instance == null) {
            		instance = new Singleton();
        		}
        		return instance;
        	}
    	}
    }
}
```



```java
//  静态内部类形式
public class Singleton {
    
    private Singleton() {
        
    }
    
    public static Singleton getInstance() {
        return SingletonHolder.instance;
    }
    
    // 静态内部类不会随着外部类的加载和初始化而初始化
    private static class SingletonHolder {
        private static final Singleton instance = new Singleton();
    }
}
```

# 类初始化过程和实例初始化过程

```java
public class Father {
    private int i = test();
    private static int j = method();
    
    static {
        System.out.println("(1)");
    }
    
    Father() {
        System.out.println("(2)");
    }
    
    {
        System.out.println("(3)");
    }
    
    public int test() {
        System.out.printlin("(4)");
        return 1;
    }
    
    public static int method() {
        System.out.println("(5)");
        return 1;
    }
}

public class Son extends Father {
    private int i = test();
    
    private static int j = method();
    
    static {
        System.out.println("(6)");
    }
    
    Son() {
        Syste.out.println("(7)");
    }
    
    {
        System.out.println("(8)");
    }
    
    public int test() {
        System.out.println("(9)");
        return 1;
    }
    
    public static int method() {
        System.out.println("(10)");
        return 1;
    }
    
    public static void main(String[] args) {
        Son s1 = new Son();
        System.out.println();
        Son s2 = new Son();
    }
}


```



## 类初始化

1. 主方法所在类会初始化
2. 一个子类要初始化需要先初始化父类
3. 一个类的初始化就是执行<cInit>

## 实例初始化

## 方法的重写

**哪些方法不可以重写**

1. final 方法
2. 静态方法
3. private等子类不可见的方法

**对象多态性**



