# Java知识体系

## 数据结构

比如HashMap、ArrayList与LinkedList异同点，ConcurrentHashMap；

1. HashMap负载因子是什么，capacity * 负载因子 = hash触发扩容容量，越小Hash表越稀疏，默认为0.75.;
2. 为什么capacity大小为2次幂（根据定义的hash算法，为2次幂时发生碰撞概率比较低）？
3. WeakHashMap？
4. 对B+树的理解，应用在数据库索引；
5. BlockingQueue，队列为空或满时会(阻塞|抛异常|return false)处理拉或放的线程。
    1. ConcurrentBlockingQueue，生成者消费者cas抢占存取

## JVM知识

1. java内存模型
    1. 堆，存放对象
        1. eden区，新生代
        2. survivor区*2，老年代
    2. 栈，进入方法时会创建栈帧，存放局部变量表、句柄、方法出入口、基本数据
    3. PermGen\Metaspace\方法区，存放类信息、常量(池)、静态变量
2. 对内存溢出的理解，包括：
    1. StackOverFlow、
    2. OutOfMemory 堆溢出
    3. OutOfMemory PermGen永生代，当加载类元信息过多时会触发。java8使用元空间得到解决，元空间占用本地内存空间。
3. GC
    1. GC模式
        1. Partial GC/Monior GC：并不收集整个GC堆的模式
            1. Young GC：只收集young gen的GC；
            2. Old GC：只收集old gen的GC（只有CMS的concurrent collection是这个模式；
            3. Mixed GC：收集整个young gen以及部分old gen的GC（只有G1有这个模式。
        2. Full GC/Major GC：收集整个，包括young、old、perm gen。
    1. GC算法：标记清楚（Mark-Sweep）、复制（Copying）、标记压缩（Mark-Compact）、分代收集（Generational Collection）；
    2. 垃圾收集器：
        1. Serial收集器：会导致服务暂停
        2. ParNew收集器：Serial多线程版
        3. Parallel收集器：Parallel Old收集器
        4. CMS收集器
            1. 作用在老年堆
        5. G1收集器
            1. 分堆gc
4. 动态代理：实现InvocationHandler接口定义代理的before、after，使用Proxy创建代理对象，jvm会为之生成class文件
5. 反射：动态获取一个class的属性、可执行方法信息，运行时才决定创建什么样的示例；
6. 类加载：
    1. tomcat类加载机制：http://lengyun3566.iteye.com/blog/1683972
    2. JDK自带类加载器的枚举与作用；
        1. Bootstrap
        2. ExtClassLoader
        3. AppClassLoader
    3. 对双亲委托的理解；
    4. 应用场景：https://www.zhihu.com/question/46719811
        1. 隔离
        2. 热加载
        3. 加载配置文件
        4. 代码保护：先混淆代码，自定义classLoader时重写findClass方式实现代码混淆后解密

## Spring基础知识：
1. 对AOP、IOC的理解；
2. 对动态代理与cglib的理解；
3. 对事务管理的理解，包括：编程式事务与声明式事务。声明式事务依赖AOP，编码简单；编程式事务使用TransactionTemplate。
4. Spring事务传播机制：
    1. required：有就加入，没有就新建
    2. supports：有就加入，没有就非事务
    3. mandatory：有就加入，没有就抛出异常
    4. requires_new：有就将当前方法创建独立子事务，没有就新建
    5. not_support：如果有，方法的执行与否不受当前事务影响，没有就非事务
    6. never：如果有，直接抛出异常，没有就非事务
    7. nested：如果有，嵌套事务方式运行，没有就新建
    8. 嵌套：父事务回滚会影响嵌套事务；子事务：父事务回滚不影响嵌套子事务

## 高并发多线程：
1. __线程池的理解__，(coreSize, BlockingQueue, maxSize, fullHandleStrategy)；
2. 线程间的通信：共享内存、wait notify、生产者消费者模式；
3. Wait notity的理解与使用注意；
4. 对volatile、Atomic的理解；
    1. Volatile关键字，通过在生成的最终汇编代码加了Lock指令，可以保证程序的『有序性』、『可见性』，性能上优于synchronized关键字，但是不能保证原子性。
        1. 可见性：线程对当前共享变量的写入，其他线程都可以立即感知到。如何实现：从cpu缓存写入时，别的cpu对当前缓存读取无效，知道cpu缓存写入并强制更新到内存，此时别的cpu会强制从内存读取。
        2. 有序性，编译器为了性能考虑，实际指令不等于用户编程的指令。但是Volatile关键字会保证当执行到当前行时，前面的代码都已经执行完毕。
5. 乐观锁悲观锁；
6. ReentrantLock锁，公平锁（FIFO）非公平锁(抢占式）；
7. 线程池：FixedThreadPool、SingleThreadExecutor、CachedThreadPool、ScheduledThreadPool；
8. 对幂等的理解；
9. 并发事务：http://www.jianshu.com/p/71a79d838443
    1. 【丢失更新】：提交或回滚覆盖
    2. 【未提交读】：一个事务读到了另一个未提交的事务写的数据
    3. 【不可重复读】：一个事务中两次读同一行数据，可是这两次读到的数据不一样
    4. 【幻读】：一个事务中两次查询，但第二次查询比第一次查询多了或少了几行或几列数据
10. 分布式事务：
    1. 两阶段提交；
    2. 三阶段提交；
    3. TCC，Try\Commit\Cancel
11. 隔离级别：
    1. Read uncommitted：一个事务可以读取另一个未提交事务的数据
    2. Read committed：一个事务等待另个事务提交后才能读取数据
    3. Repeatable read：事务开启时，不再允许其他修改操作
    4. Serialzabled：最高级别数据，强制串行化处理
12. NIO
    1. SelectionKey
        1. OP_CONNECT 连接事件，matster
        2. OP_ACCEPT 数据接收事件，通常会随后往通道内注册写事件 master
        3. OP_READ 数据读取事件，通知通道内存在可读，worker
        4. OP_WRITE 数据写事件，可以往通道内写了，worker

## Java8
1. Lambda表达式与实现原理；
2. Stream API；
3. Java8新特性；
    1. 元空间是什么。

##  分布式
1. 一致性Hash算法（方便机器上线下线），paxos算法；
2. 分布式队列的使用，举例常用的分布式队列中间件并比较异同；
3. 分布式锁的理解，并举例，包括zookeeper、redis;
4. 对分布式事务的理解与处理；
5. Paxos，两阶段，提议者、接收者，选举并决定最终消息一致性
6.  分布式系统设计的关键点
    1. 服务注册发现，心跳检测，数据上报
    2. 分布式一致性：paxos、推送、同步
    3. 容错，容灾，宕机重启（记录日志）
    4. 通信：NIO
    5. 数据交换：序列化反序列化，协议约定，消息结构
    6. 蓄洪、泄洪、熔断、超时
    7. 消息中间件
    8. 分布式事务
7. 消息中间件，以下维度进行比较：
    1. 消息持久化：
        1. 方式：异步、同步；
        2. 持久化是批量还是按条（批量可以保证高吞吐；
        3. 写文件、写HDFS、写DB；
    2. 推或拉，推能满足即时性，拉能保证消费者负载；
    3. 队列是否支持分布式，Hash。

## 算法
1. 举例排序算法，什么是稳定的排序，快排过程。
