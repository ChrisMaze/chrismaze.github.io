---
layout: single
title:  "Singleton Design Pattern"
date:   2021-03-28 11:35:10 +0800
toc: true
tags:
  - design-pattern
  - Backend
---

# 设计模式 - 单例模式(Singleton Design Pattern)
## Conception
一个类只允许创建一个对象（或者实例），那这个类就是一个单例类，这种设计模式就叫作单例设计模式，简称单例模式。

### 单例模式解决的问题:
1. 处理资源访问冲突(对象级别的锁，且对象唯一)
>例如： Logger等容易造成资源冲突的对象。
2. 表示全局唯一类
> 例如：配置文件；唯一递增ID号码生成器等

### 实现单例模式的要点
> - 构造函数需要是 private 访问权限的，这样才能避免外部通过 new 创建实例；
> - 考虑对象创建时的线程安全问题；
> - 考虑是否支持延迟加载；
> - 考虑 getInstance() 性能是否高（是否加锁）。

## Implementation
单例模式有5中经典的实现方式：
1. 饿汉式
```java
public class IdGenerator { 
  private final AtomicLong id = new AtomicLong(0);
  private static final IdGenerator instance = new IdGenerator();
  private IdGenerator() {}
  public static IdGenerator getInstance() {
    return instance;
  }
  public long getId() { 
    return id.incrementAndGet();
  }
}
```
> - 在类加载的期间，就已经将 instance 静态实例初始化好了，所以，instance 实例的创建是线程安全的。  
> - 不支持延迟加载实例

2. 懒汉式
```java
public class IdGenerator { 
  private final AtomicLong id = new AtomicLong(0);
  private static IdGenerator instance;
  private IdGenerator() {}
  public static synchronized IdGenerator getInstance() {
    if (instance == null) {
      instance = new IdGenerator();
    }
    return instance;
  }
  public long getId() { 
    return id.incrementAndGet();
  }
}
```
> - 支持延迟加载
> - 会导致频繁加锁、释放锁，以及并发度低等问题，频繁的调用会产生性能瓶颈
3. 双重检测
```java
public class IdGenerator { 
  private final AtomicLong id = new AtomicLong(0);
  private static IdGenerator instance;
  private IdGenerator() {}
  public static IdGenerator getInstance() {
    if (instance == null) {
      synchronized(IdGenerator.class) {
        if (instance == null)
        instance = new IdGenerator();
      }
    }
    return instance;
  }
  public long getId() { 
    return id.incrementAndGet();
  }
}
```
既支持延迟加载、又支持高并发  

    **Question**: 为什么要二次判断 `instance == null`
    > - 当instance未创建时，instance == null 为true  
    > - 多个线程同时进入第一层if  
    > - 此时开始竞争资源  
    > - 线程1 抢到资源， 进行二次判断， instance == null 为true  
    > - 创建instance  
    > - 释放锁  
    > - 线程2 抢到资源，进行二次判断， instance == null 为false  
    > - 这样不会创建新的instance  

4. 静态内部类
```java
public class IdGenerator { 
  private final AtomicLong id = new AtomicLong(0);
  private static class SingletonHolder {
    private static final IdGenerator instance = new IdGenerator();
  } 
  private IdGenerator() {}
  public static IdGenerator getInstance() {
    return SingletonHolder.instance;
  }
  public long getId() { 
    return id.incrementAndGet();
  }
}
```
> 支持延迟加载，也支持高并发，实现起来也比双重检测简单。
> instance 的唯一性、创建过程的线程安全性，都由 JVM 来保证。
5. 枚举
    ```java
    public enum IdGenerator { 
      INSTANCE;
      private final AtomicLong id = new AtomicLong(0);
      
      public long getId() { 
        return id.incrementAndGet();
      }
    }
    ```
> 最简单的实现方式，基于枚举类型的单例实现。
