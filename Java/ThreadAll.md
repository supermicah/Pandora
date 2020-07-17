# 线程

## 线程名称
```java
// 表示当前是被哪个线程调用的，如下面的这个最终调用名词为 `main`
public class Run1 {
    public static void main(String[] args) {
        System.out.println(Thread.currentThread().getName());
    }
}

```
1. 当`new Thread`时，调用构造方法的为`main`，但是Thread中`run`方法是新建出来的Thread的name。
2. 如果Thread采用的不是`start`，而是`run`方法时，则不是多线程，是当前线程直接执行，所以结果为`main`对应的name。

## `sleep`
