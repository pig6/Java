本文将循序渐进的方式为大家介绍ThreadLocal方面的知识点，希望大家能够理解透彻，在实战与面试中能游刃有余！

## 笔记目的
* 介绍ThreadLocal概念
* 介绍ThreadLocal实现原理
* 内存泄漏问题
* 最佳实践

## 概念
* 产生的诉求：每条线程都需要存取一个同名变量，但每条线程中该变量的值均不相同。
* 思路：
	* 使用一个线程共享的Map<Thread,Object>，Map中的key为线程对象，value即为需要存储的值。
	* 那么，我们只需要通过map.get(Thread.currentThread())即可获取本线程中该变量的值。
	* 问题：需要同步，效率低下，即便有juc包下的ConcurrentHashMap也只能降低锁的粒度，效率依旧低下。
* ThreadLocal就此诞生
	* 这个类顾名思义可以理解为线程本地变量。
	* 也就是说每个线程往这个ThreadLocal中读写是线程隔离，互相之间不会影响的。它提供了一种将可变数据通过每个线程有自己的独立副本从而实现线程封闭的机制。
	* demo
	```
	public class MyTest {

	    private static ThreadLocal<String> traceLocal = new ThreadLocal<>();

	    public static void main(String[] args) {
	        new Thread(() -> {
	            traceLocal.set("123");
	            System.out.println(traceLocal.get());
	        }).start();
	        new Thread(() -> {
	            traceLocal.set("456");
	            System.out.println(traceLocal.get());
	        }).start();
	    }

	}
	```

## 实现原理
* set
	* ThreadLocal先获取当前线程，然后通过当前线程获取ThreadLocalMap对象，然后对获取的map对象进行判断，若已存在，则直接覆盖old value；否则直接新建一个map
	* ![set](https://mmbiz.qpic.cn/mmbiz_png/K07fFKibvoT3yn07yQ3QqSQibWBBHHmNQZjRnniaDXqQAJ46FhSByFDkEvrFsnicjjOiaXficibergq7PVYkgDbl72iaHA/640?wx_fmt=png)
* get
	* ThreadLocal先获取当前线程，然后根据获取到的当前线程去取ThreadLocalMap对象，然后再通过当前ThreadLocal对象获取，对应的value
	* ![get](https://mmbiz.qpic.cn/mmbiz_png/K07fFKibvoT3yn07yQ3QqSQibWBBHHmNQZSw2pgyhgaan7eMY1P03mrxA8tb4TarCLniat33kDstKYX1hwIhuSHCw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
* remove
	* 首先是通过当前线程获取ThreadLocalMap对象，然后remove掉当前的threadlocal
	* ![remove](https://mmbiz.qpic.cn/mmbiz_png/K07fFKibvoT3yn07yQ3QqSQibWBBHHmNQZUxxWt03M9VlsGsMSwCq3iaWmdyVz4Fm0bJzWOia7Ic0ianvNAKgKOX9Jw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
* ThreadLocalMap
	* 每个Thread维护一个ThreadLocalMap变量threadLocals
	* ![map](https://mmbiz.qpic.cn/mmbiz_png/K07fFKibvoT3yn07yQ3QqSQibWBBHHmNQZ0nU40phdkw3N8vibH3qHTETyf5lBQ917hovs8NR7IEokCuAXtoHtxJQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
	这个映射表的key是ThreadLocal实例本身，value是真正需要存储的Object。
	* 也就是说ThreadLocal本身并不存储值，它只是作为一个key来让线程从ThreadLocalMap获取value。
	* ThreadLocalMap是使用ThreadLocal的弱引用作为Key的

## 内存泄漏问题
* 内存泄漏的过程
	* 因为线程的ThreadLocalMap的key为ThreadLocal的弱引用
	* 如果ThreadLocal没有额外的强引用指向它
	* 那么GC的时候这个ThreadLocal对象就会被回收
	* 线程的ThreadLocal对象的key就会变成null，value和value指向的对象还是强引用
	* 因为ThreadLocal已经回收了，所以线程没法再操作到自己保存的关于这个ThreadLocal的value，如果这个线程迟迟不结束的话，这个value就被内存泄漏了。。。
* 为什么使用弱引用
	* 如果使用强引用，程序在ThreadLocal对象不再使用时显示置为null，但是因为被ThreadLocalMap强引用了，所以对象还是无法被回收（但是其实这个时候程序已经希望ThreadLocal被回收了）
	* 所以弱引用是为了解决内存泄漏
	* 但是弱引用使用不当又会导致产生新的内存泄漏情况
* 对于弱引用可能产生的新的内存泄漏情况的处理设计
	* ThreadLocalMap的设计中已经考虑到这种情况，也加上了一些防护措施：在ThreadLocal的get(),set(),remove()的时候都会有清除线程ThreadLocalMap里key为null的value的逻辑

## 最佳实践
* 1.将ThreadLocal变量定义为private static
* 2.用完就即remove()
* JDK建议将ThreadLocal变量定义为private static的，这样的话ThreadLocal的生命周期就更长，由于一直存在ThreadLocal的强引用，所以ThreadLocal就不会回收，也就能保证任何时候都能根据ThreadLocal的弱引用访问到Entry的value值，然后remove，防止内存泄露 

