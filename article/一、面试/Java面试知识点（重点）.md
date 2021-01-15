本篇笔记是我五年来的工作学习面试经验&记录，希望对大家有一些帮助

## java基础
* Object类的所有方法：getClass hashCode equals clone toString notify notifyAll wait finalize
* 八种基本数据类型&取值范围
* 自动拆装箱&包装类型的缓存机制
* ThreadLocal 理解String&不可变性 StringBuffer StringBuilder源码
* ArrayList LinkedList HashMap（重点，可以花一天时间彻底掌握） LinkedHashMap TreeMap HashSet LinkedHashSet TreeSet源码
* 异常体系结构
* Java8：理解函数式编程 流式操作 CompletableFuture

## 并发编程（必考，高并发的解决必要手段，对于juc以及线程模型的演进过程的掌握非常重要）
* 理解并发与并行
* 线程池的原理：理解每个参数
* Thread Runnable Callable 
* AQS（重要：常常会配合synchronized来说） Condition 
* Lock：ReentrantLock ReentrantReadWriteLock  
* Semaphore CountDownLatch CyclicBarrier 
* 各种Atomic
* ConcurrentHashMap BlockingQueue CopyOnWriteArrayList
* Executors
* 线程的状态转换：wait sleep notify notifyAll join park 
* 停止线程的方法
* 线程模型：理解BIO、NIO（重点）、AIO、了解netty
* 并发包就那些东西，掌握AQS之后其他的很快就打通了，所以不用

## jvm（《深入理解java虚拟机》没事就看一遍，每次都会有新的理解）
* jvm内存结构
* 常用调优参数
* gc算法
* gc的工具：CMS&G1重点&ZGC
* java内存模型：
	* 可见性、原子性、顺序性、happens-before、内存屏障、volatile、final
	* synchronized(对象头)：偏向锁->轻量级锁->重量级锁、monitor、锁优化、锁消除、锁粗化、自旋锁、可重入锁
* 常用工具以及命令
* 类加载过程
* 对象创建过程
* fullGC的排查思路
* 工具：jps, jstack, jmap、jstat, jconsole, jinfo, jhat, javap, Arthas

## 设计模式
* 创建型：单例模式、抽象工厂模式、建造者模式、工厂模式、原型模式。
* 结构型模式：适配器模式、桥接模式、装饰模式、组合模式、外观模式、享元模式、代理模式。
* 行为型模式：模版方法模式、命令模式、迭代器模式、观察者模式、中介者模式、备忘录模式、解释器模式（Interpreter模式）、状态模式、策略模式、职责链模式(责任链模式)、访问者模式。

## mysql（下面列出来的全是重点）
* 常用sql以及内置函数
* 索引
	* hash索引
	* b+树的索引
	* 聚集索引&非聚集索引
	* 回表
	* 联合索引：最左前缀&覆盖索引&索引下沉
* 事务&隔离级别&实现原理：readview undolog redolog mvcc
* 锁的使用&锁算法&行锁&表锁&乐观锁&悲观锁
* 连接查询的原理（算法）
* binlog&使用场景
* 分库分表：垂直&水平
* 工具：explain

## redis（《redis的设计与实现》看完这本书就行了，深入浅出，很快就可以看完和掌握，直接搞定redis）
* 熟练五种类型对象&适用场景：字符串 列表 哈希 集合 有序集合
* 了解底层数据结构：SDS 链表 字典 跳跃表 整数集合 压缩列表 
* 淘汰策略
* RDB&AOF
* 复制功能的实现
* Sentinel&集群的结构
* 发布订阅&事务
* 分布式锁
* 常见问题&解决方案：缓存击穿，缓存穿透，缓存雪崩

## web（感觉很少有人问了，应届生可能问的多些）
* 三次握手与四次挥手
* http/1.0 http/1.1 http/2之间的区别
* http中 get和post区别
* 理解https加密过程
* forward&redirect的区别
* CSRF&XSS攻击与防范
* 常见的web请求返回的状态码
* cookie&session
* Servlet&filter&listener
* DNS
* tomcat&nginx区别

## spring（说实话问到我的不多，感觉掌握下面几个足够了）
* IOC&DI&AOP
* 掌握常用注解
* 如何解决循环依赖
* 事务传播机制
* 事务失效场景
* springMVC的理解
* springboot的理解

## 分布式&高可用(平时多注意怎么处理好程序可能遇到的极端情况，保证服务的高可用)
* zookeeper
	* 理解使用：文件系统+监听器
	* 了解选举算法&分布式锁的实现
* mq（重要，有时候面试官直接一串mq连环炮就可以决定要不要一个人了）
	* 作用：异步 解耦 削峰
	* kafka（之后会就kafka写一个系列）
		* 设计架构
		* 如何保证高可用
		* 如何保证高吞吐
		* 如何实现局部有序
		* 重复消费的问题
		* 延迟消息
* 了解dubbo&spring cloud&Consul工作流程
* 限流&熔断：Hystrix/Resilience4j（高可用的必须中间件）
* 链路追踪（了解实现原理）

## 算法&数据结构（常刷leetcode）
* 栈、队列、链表、数组、哈希表、树、堆、图
* 快排&二分&分治&递归&滑动窗口/双指针&BFS&DFS

## 工具
* git多分支协作流程
* maven常用命令&生命周期
* linux常用命令：tail grep awk sed等等

## 备注
* 看着确实很绝望，up本人也是
* 个人难免有盲区，欢迎评论区指正&补充，会持续更新
* but
* 种一棵树，最好的时间是十年前，其次是现在，大家共勉
```
码字不易，欢迎点赞
```

