---
layout: post
title: 创建线程
categories: Java
tags:
- Thread
- Java
description: 创建线程
---

### 1.继承java.lang.Thread

{% highlight Java %}
public class ThreadDemo extends Thread {
    public void run() {
        System.out.println("currentThreadName:" + Thread.currentThread().getName());
    }
}
{% endhighlight %}


运行线程

{% highlight Java %}
ThreadDemo threadDemo = new ThreadDemo();
threadDemo.start();
{% endhighlight %}
 
### 2.实现Runnable 接口


{% highlight Java %}
public class ThreadDemo implements Runnable {
    public void run() {
        System.out.println("currentThreadName:" + Thread.currentThread().getName());
    }
}
{% endhighlight %}

运行线程

{% highlight Java %}
Thread thread = new Thread(new ThreadDemo());
thread.start();
{% endhighlight %}
