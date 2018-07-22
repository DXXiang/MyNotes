# ThreadLocal

* 处理并发访问

* 泛型T

* 内部是一个Map，泛型为<Thread,T>
* 使用当前线程(Thread.currentThread())作为键，指定泛型T为值

## 方法

* void set(T value):保存值
* T get():获取值
* void remove():移除值

## 使用示例

```java
ThreadLocal<String> t1 = new ThreadLocal<>();
t1.set("hello");
```

* 通常用在一个类的成员上
* 多个线程访问它时，每个线程都有自己的副本，互不干扰
* Spring中把Connection放到了 ThreadLocal中

```java
Class User{
    private ThreadLocal<String> username = new ThreadLocal<>();
}
```

