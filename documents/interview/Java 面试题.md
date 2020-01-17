#### 多线程：

1. 进程和线程的区别:

    进程通俗来讲就是运行中的程序, 包括一系列的系统资源: 例如打开的文件, 等待的信号, 处理器的状态, 地址空间等, 一个进程包含一个或多个线程执行单元. 线程可以共享进程的资源, 又可以独立调度, 每一个线程都有自己的程序计数器, 方法栈, 程序计数器.

    - Java 实现线程的三种方式:
        - 使用内核线程实现:

            程序一般会使用轻量级进程(Light Weigth Process), 每个轻量级进程都由一个内核线程支持. 

        - 使用用户线程实现:

        - 使用用户线程加轻量级进程混合实现: 轻量级进程作为用户线程和内核线程之间的桥梁.

    - 线程调度方式:

        - 协同式调度(Cooperative Threads-Scheduling)

            线程执行完才通知系统要切换到另外一个线程

        - 抢占式调度(Preemptive Threads-Scheduling)

            由系统来分配执行时间, 线程的切换不由线程本身决定

2. 守护线程(Daemon Thread): 

    是运行在后台的线程, 专门用于服务其它的线程. 当所有前台线程执行完毕后, 守护线程也将被终止. 在 Java 中垃圾回收线程就是特殊的守护线程.

3. 创建线程执行体的方式:

    - 继承 Thread 类并重写 run() 方法

    - 实现 Runnable 接口

    - 实现 Callable 接口

        `Callable` 的 `call()` 方法可以有返回值并且可以声明抛出异常. Java 提供 `FutureTask` 利用适配器模式将 `Callable` 的 `call()` 方法适配为 `Runnable` 的 `run()` 方法.  `FutureTask` 一次只能被一个线程执行, 若没能获取到则直接返回, 不会执行 `call()` 方法.

4. runnable 和 callable 的区别:

    `Callable` 的 `call()` 方法可以有返回值并且可以声明抛出异常. Java 提供 `FutureTask` 利用适配器模式将 `Callable` 的 `call()` 方法适配为 `Runnable` 的 `run()` 方法.  `FutureTask` 一次只能被一个线程执行, 若没能获取到则直接返回, 不会执行 `call()` 方法.

5. 线程的状态:

    - NEW: 尚未启动

    - RUNNABLE: 正在执行中

    - BLOCKED: 等待锁的释放

        阻塞状态是从逻辑上来讲的, 就好比你进入一个房间, 但是现在房间被锁住了, 你没有房间的钥匙, 所以你被阻止在房间外了. 实际上底层仍然是将线程置于调度器不能调度的状态.

    - WAITING: 等待其它线程的通知

        当调用 `Object#wait()` 方法, `Thread#join()` 方法, `LockSupport#park()` 方法时, 都会进入到该状态.

        该状态仍然是一个逻辑上的状态, 就好比你让朋友打电话叫你起床, 你在睡觉的时候就好比调用了 `Object#wait()` 方法, 此时你就处于了 `WAITING` 状态, 实际上底层仍然是将线程处于调度器不能调度的状态. 当朋友打电话的时候就好比调用了`Object#notifyAll()`方法, 这时你就处于 `NEW` 状态了. 

    - TIMED_WAITING: 等待其它线程的通知, 指定时间等待

    - TERMINATED: 线程终止

    在 Linux 内核中并没有阻塞或等待状态, Linux 内核中与之相对应的是 `TASK_INTERRUPITLBLE` 状态. 处于该状态的线程在等待某个条件发生或者等待一个信号, 此时无法被调度器调度, 当条件发生或信号抵达时, 才会改变为 `TASK_RUNNING` 状态; 处于 `TASK_RUNNING` 状态的线程要么正在执行, 要么正在等待被调度.
    
6. sleep() 和 wait() 区别:

    `sleep()` 方法是 `Thread` 类的静态方法, 而 `wait()` 是对象的实例方法; 调用 `sleep()` 方法不会释放当前线程的锁, 而调用 `wait()` 方法会释放当前线程持有的锁; `sleep()` 方法到时间会自动恢复, `wait()` 方法可以通过 `notify()` 或 `notifyAll()` 方法恢复.

7. notify() 和 notifyAll() 区别:

    `nofityAll()` 会唤醒等待队列中所有的线程, 并将它们放入到同步队列中参与锁的竞争; `nofity()` 会唤醒等待队列中等待时间最长的线程.

9. Atomic 的原理:

    Atomic 主要利用底层硬件的原子性 CAS 指令和 volatitle 关键字保证原子操作; 底层的 CAS 指令保证了 Compare-Set 这一操作的原子性, volatitle 关键字保证了更改的数据对其它的可见性:  volatitle 修饰的变量在值修改之后会将新的值立即同步回主内存中, volatitle 修饰的变量在读取值的时候会先从主存中刷新变量.
    
9. synchronized 和 ReentrantLock 区别:

    ReentrantLock 使用起来比较灵活, 但是需要手动获取与释放锁, synchronized 不需要手动释放和开启锁; ReentrantLock 只适用于代码块锁, synchronized 可用于修饰方法, 代码块; volatile 标记的变量不会被编译器优化, synchronized 标记的变量可以被编译器优化.

10. synchronized 底层实现:

    synchronized 是由一对 monitorenter/monitorexit 指令实现的, monitor 对象是同步实现的基本单元. 在 JDK 1.6 之前, 是依赖操作系统底层的排它锁来实现的, 这时需要从用户态切换到内核态, 这种转换需要消耗处理器的时间, 如果代码块中的内容过于简单, 状态切换的时间可能比用户代码执行的时间还要长.

    JDK 1.6 之后引入了偏向锁, 轻量级锁. 

    - 偏向锁: 是指一段同步代码一直被一个线程所访问, 那么该线程会自动获取锁, 降低获取锁的代价(解决锁的重入问题).
    - 轻量级锁: 是指当锁是偏向锁时, 被另外的线程所访问, 偏向锁就会升级为轻量级锁, 其它线程会通过自旋的形式尝试获取锁, 不会阻塞, 从而提高性能(解决两个线程锁的竞争问题).
    - 重量级锁: 当两个锁参与竞争轻量级锁时, 通过自旋等待的线程超过一定的次数, 或者有第三个线程来竞争锁时, 轻量级锁会升级为重量级锁(这里的重量级指的是会通过系统调用来将线程挂起).

    

11. 线程池的状态:

     - RUNNING: 接受新的任务, 处理等待队列中的任务.
     - SHUTDONW: 不接受新的任务提交, 但是会继续处理等待队列中的任务.
     - STOP: 不接受新的任务提交, 不再处理等待队列中的任务, 中断正在执行任务的线程.
     - TYDING: 所有任务都销毁了, workCount 为 0, 线程池的状态在转换wie TIDYING 状态时, 会执行钩子方法 `terminated()`.
     - TERMINATED:  `terminated()` 方法结束后, 线程池的状态就会变成这个.

12. 线程池:

     * 原理: 

         传统的多线程是针对每一个任务都去创建一个线程, 创建线程会消耗系统的资源, 如果任务很多但是每个任务又都很小, 那么就会导致大量的系统资源被消耗和浪费. 而使用线程池则可以降低资源的消耗, 线程池将任务和线程的执行分隔开来, 使得一个线程可以获取多个任务来执行, 这样减少了创建线程的数量, 使用线程池可以带来如下的好处:

         * 降低资源的消耗: 通过重复利用已创建的线程降低线程创建和销毁造成的消耗.
         * 提高相应速度: 当任务到达时, 任务可以不需要等到线程创建就能立即执行.
         * 提高线程的可管理性: 可以同意分配, 调优和监控线程.

         当提交一个新任务到线程池时:

         * 线程池判断**核心线程池里的线程**是否都在执行任务. 如果不是, 则创建一个新的工作线程来执行任务. 如果是, 则进入下一个流程.
         * 线程池判断工作队列是否已经满. 如果工作队列没有满, 则将新提交的任务存储在这个工作队列中. 如果满了, 则进入下一个流程.
         * 线程池判断**线程池的线程**是否都处于工作状态. 如果没有, 则创建一个新的工作线程来执行任务. 如果满了, 则交给饱和策略来处理这个任务.

     * 线程池的创建:

         - FixedThreadPool: 可重用固定线程数的线程池

             ```java
             public static ExecutorService newFixedThreadPool(int nThreads) {
                 /*
                  * corePoolSize 和 maximumPoolSize 都被设置为固定值
                  * 当线程池的线程数大于 corePoolSize 时, keepAliveTime 为多于的空闲线程等待新任务
                  * 的最长时间, 超过这个时间后多余的线程将被终止. 这里把 keepAliveTime 设置为 0L, 意		 * 味着多于的空闲线程会被立即终止
                  */
                 return new ThreadPoolExecutor(nThreads, nThreads,
                                               0L, TimeUnit.MILLISECONDS,
                                               new LinkedBlockingQueue<Runnable>());
             }
             ```

             `FixedThreadPool` 使用无界队列 `LinkedBlockingQueue` 作为线程池的工作队列(队列的容量为 Integer.MAX_VALUE). 使用无界队列的影响:

             - 当线程池中的线程数量达到 `corePoolSize` 后, 新任务将在无界队列中等待, 因此线程池的线程数不会超过 `corePoolSize` .
             - 由于上面原因, 使用无界队列时, `maximumPoolSize` 将是一个无效参数.
             - 由于上面两个原因, 使用无界队列时, `keepAliveTime` 将是一个无效参数.
             - 由于使用无界队列, 运行中的 `FixedThreadPool` 不会拒绝任务.

         - SingleThreadExecutor: 单个线程数的线程池

             ``` Java
             public static ExecutorService newSingleThreadExecutor() {
                 /*
                  * corePoolSize 和 maximumPoolSize 都被设置为 1, 其余和 FixedThreadPool 相同
                  */
                 return new FinalizableDelegatedExecutorService
                     (new ThreadPoolExecutor(1, 1,
                                             0L, TimeUnit.MILLISECONDS,
                                             new LinkedBlockingQueue<Runnable>()));
             }
             ```

         - CachedThreadPool: 根据需要创建新线程的线程池    

             ```java
              public static ExecutorService newCachedThreadPool() {
                  /*
                   * 这里的 corePoolSize 被设置为 0, maximumPoolSize 
                   * 被设置为 Integer.MAX_VALUE, keepAliveTime 设置为
                   * 60L
                   */
                  return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                                60L, TimeUnit.SECONDS,
                                                new SynchronousQueue<Runnable>());
              }
             ```

             `CachedThreadPool` 使用没有容量的 `SynchronousQueue` 作为线程池的工作队列. 由于 `CachedThreadPool` 的 `maximumPoolSize` 是无界的, 如果主线程提交任务的速度高于 `maximumPool` 中线程处理任务的速度, `CachedThreadPool` 会不断创建新的线程.

         - newSingleThreadScheduledPool(): 创建单个线程的可进行定时或周期性工作调度的线程池

         - newThreadScheduledPool(): 创建多个线程的可进行定时或周期性工作调度的线程池

         - newWorkStealingPool(): 创建持有足够的线程的线程池来支持给定的并行级别. 默认会创建和处理器核心数量相同的线程.

     * 线程池关闭:

         关闭线程池可以调用线程池的 `shutdown()` 或 `shutdownNow()` 方法来关闭线程池. 它们的原理是遍历线程池中的工作线程, 然后逐个调用线程的 `interrupt()` 方法来中断线程, 所以无法响应中断的线程永远无法终止.

         `shutdownNow()` 先将线程池的状态设置 `STOP`, 然后尝试**停止所有的正在执行或暂停任务**的线程, 并**返回等待执行任务的列表**;  `shutdown()` 只是将线程池的状态设置为 `SHUTDOWN` 状态, 然后**中断所有没有正在执行任务**(正在执行的线程不中断, 等待它们执行完毕)的线程.

         只要调用了上面两个方法中任意一个, `isShutdown()` 方法就会返回 true. 当所有的任务都已经关闭后, 才表示线程池关闭成功, 这时调用 `isTerminated()` 方法返回 true.

14. 线程池 submit() 和 execute() 方法区别:

`execute()` 执行 `Runnable` 类型任务, 无返回值; `submit()` 执行 `Callable` 类型任务, 有返回值, 在内部是通过将 `Callable` 包装成 `FutureTask` 执行.

15. 死锁:

    - 死锁举例:

        当线程 A 持有独占锁 a, 并尝试去获取独占锁 b 的同时, 线程 B 持有独占锁 b, 并尝试去获取独占锁 a 的情况下, 就会发生 AB 两个线程由于互相持有对方需要的锁, 而发生的阻塞现象.

    - 产生死锁的条件:
        - 互斥条件(mutual exclusion): 一个资源每次只能被一个线程使用, 此时如果有其它线程请求该资源, 则请求线程只能等待.
        - 请求与保持条件(hold and wait or resource holding): 线程中已经保持了至少一个资源, 但是又提出了新的资源请求, 而该资源已经被其它线程占有, 此时请求线程被阻塞, 但是自己对已经获得资源保持不放.
        - 不剥夺条件(no preemption): 线程未使用完的资源在未使用完毕之前, 不能被其它线程强行夺走, 即只能由获得该资源的线程自己来释放.
        - 循环等待条件(circle wait): 线程间形成守卫相接循环等待资源的关系. 在发生死锁时必然存在一个线程等待队列 {P1, P2, P3, ..., Pn}, 其中 P1 等待 P2 占有的资源, P2 等待 P3 占有的资源, ..., Pn 等待 P1 占有的资源, 形成一个线程等待环路.
    - 死锁避免:
        - 破环请求保持条件:
            - 静态分配: 每个进程在开始执行时就申请它所需要的全部资源.
            - 动态分配: 每个进程在申请所需要的资源时它本身不占用系统资源.
        - 破环不可剥夺条件:
            - 等待期间将占用的资源隐式的释放掉, 供其它线程使用, 等待的线程只有重新获取自己原有的资源以及新申请的资源才可以重新启动, 执行(例如调用 wait() 方法).
        - 破环循环等待条件:
            - 采用资源有序分配的基本思想. 将系统资源顺序进行编号, 将紧缺的, 稀少的资源采用较大的编号, 申请资源时必须按照编号的顺序执行, 有小编号资源的线程才能申请较大编号的资源.
        - 具体操作:
            - 尽量使用 `tryLock(long timeout, TimeUnit unit)` 的方法 (ReentrantLock、ReentrantReadWriteLock)，设置超时时间，超时可以退出防止死锁.
            - 尽量使用 Java. util. concurrent 并发类代替自己手写锁.
            - 尽量降低锁的使用粒度，尽量不要几个功能用同一把锁.
            - 尽量减少同步的代码块.

16. ThreadLocal 原理及使用场景:

    ThreadLocal 为每个使用该变量的线程提供了独立的变量副本, 每一个线程都可以独立的改变自己的变量副本, 而不会影响其它线程所对应的副本. 使用场景有数据库连接和 Session 管理.

------

#### 反射:

1. 反射定义:

    在类加载后, JVM 会为该类生成一个对应 Class 对象, 通过该对象可以获取到该类的属性和方法, 并且可以获取该属性在某个对象上的值, 或者以该对象为调用者, 调用方法.

2. Java 序列化:

    将对象在内存中的状态写入到 IO 流中, 并且能够从 IO 流中恢复保存的对象的状态. 当有如下需求时需要序列化:

    - 想把内存中的对象状态保存到一个文件中或者数据库中
    - 想用套接字在网络上传送对象
    - 想通过 RMI (远程方法调用) 传输对象

3. 动态代理:

    - 定义: 在运行时动态地生成接口或者类实现代理类
    - 实现方式: JDK 原生的动态代理(针对接口)和 cglib 动态代理(针对子类)
    - 应用: Sprign AOP, Mabtis 的 Mapper 接口, 测试框架 Mock 等.

------

#### JVM 

1. JVM 组成:

    - 类加载器 (ClassLoader): 负责类的加载
    - 运行时数据区 (Runtime Data Area): 负责 Java 程序内存管理
    - 执行引擎 (Execution Engine): 负责将字节码翻译为底层指令
    - 本地库接口 (Native Interface): 负责调用其它语言的的接口

2. JVM 运行时数据区:

    - 程序计数器 (Program Counter Register): 当前线程所执行的字节码的行号指示器, 线程私有.
    - Java 虚拟机栈 (Java Virtual Machine Stacks):  每个方法在执行的时候都会创建一个栈帧 (Stack Frame) 用于存储局部变量表, 操作数栈, 动态链接, 方法出口等信息. 每一个方法从调用直到执行完毕, 就对应着一个栈帧在虚拟机栈中入栈到出栈的过程, 线程私有.
    - 本地方法栈 (Native Method Stacks): 与 Java 虚拟机栈类似, 服务于虚拟机使用的 Native 方法.
    - Java 堆 (Java Heap): 存放创建的对象实例, 线程共享, 在虚拟机启动时创建.
    - 方法区 (Method Area): 存储被虚拟机加载的类信息, 常量, 静态变量, 及时编译器编译后的代码等数据, 线程共享.
    - 运行时常量池 (Runtime Constant Pool): 该区域属于方法区的一部分. Class 文件中的常量池 (Constant Pool Table) 在类加载后就会进入方法区的运行时常量池中存放.

3. 堆和栈的区别:

    - 功能方面: 堆是用来存放对象的, 栈是用来执行程序的.
    - 共享性: 堆是线程共享的, 栈是线程私有的.
    - 空间大小: 堆大小远远大于栈.

4. 双亲委派模型:

    对于任意一个类, 都需要由加载它的类加载器和这个类本身一同确立在 JVM 中的唯一性. 类加载分类:

    - 根类加载器 (Bootstrap ClassLoader), 是虚拟机自身的一部分, 用来加载 `JAVA_HOME/jre/lib` 目录中的或者被 `-Xbootclasspath` 参数所指定路径中并且能够被虚拟机识别的类库.
    - 扩展类加载器 (Extension ClassLoader)： 负责加载 `JAVA_HOME/jre/lib/ext` 或者由 `java.ext.dirs` 系统变量指定的路径中的所有类库.
    - 系统类加载器 (System Classloader): 负责加载 Java 命令的 `-classpath` 选项, `java.class.path` 系统属性, 或者 `CLASSPATH` 环境变量所指定的类库.

    如果一个类加载器受到了类加载的请求, 它首先不会主动去加载这个类, 而是把这个请求委派给父类加载器去完成, 每一层的类加载其都是如此, 只有当父加载无法完成加载请求时, 子加载器才会尝试去加载类.

5. 类加载过程:

    - 加载:  根据类的全限定名获取类的二进制流, 将这个二进制流代表的静态存储结构转化为方法区的运行时数据结构, 然后生成一个 `Class` 类的对象 (对于 HotSpot 虚拟机而言, 该对象存在于方法区).
    - 验证：检查加载的 Class 文件的正确性.
    - 准备:  为类变量的分配内存并设置类变量最初始值(在方法区分配变量).
    - 解析: 将常量池重的符号引用替换为直接引用的过程. 符号引用就是一个标识字符串, 直接引用相当于内存的地址.
    - 初始化: 对静态变量和静态代码初始块执行初始化工作.

6. 如何判断对象是否可以被回收:

    - 引用计数器: 为每个对象创建一个引用计数, 有对象时计数器加 1, 引用被释放时计数减 1, 当计数器为 0 时就可以被回收. 缺点是无法解决循环引用的问题. 
    - 可达性分析: 从 GC Roots 开始向下搜索, 搜索走过的路径称为引用链. 当一个对象到 GC Roots 没有任何引用链相连时, 则证明此对象是可以被回收的.

7. Java 的引用类型:

    - 强引用: 发生 GC 时不会被回收.
    - 软引用: 有用但不是必须的对象, 在发生内存溢出之前会被回收.
    - 弱引用: 有用但不是必须的对象, 在下一次 GC 时会被回收.
    - 虚引用: 无法通过虚引用获得对象, 虚引用的用途是在 GC 时返回一个通知.

8. JVM 垃圾回收算法:

    - 标记-清除算法(Mark-Sweep): 标记无用对象, 然后进行清除回收. 缺点: 效率不高, 无法清除垃圾碎片.
    - 复制算法: 将容量划分为两个大小相等的内存区域. 每次只使用其中的一块. 当一块用完的时候将或者的对象复制到另一块上, 然后把已使用的内存空间一次清理掉. 缺点: 内存使用率不高, 只有原来的一半.
    - 标记-整理算法(Mark-Compact): 标记无用对象, 让所有存活的对象都向一端移动, 然后直接清除掉端边界以外的内存.
    - 分代算法: 根据对象存活周期的不同将内存划分为几块, 一般是新生代和老年代, 新生代采用复制算法, 老年代采用标记清理算法.

9. JVM 垃圾回收器:

    - Serial: 最早的单线程串行垃圾回收器.
    - Serial Old: Serial 垃圾回收器的老年代版本, 同样也是单线程的。
    - ParNew: Serial 的多线程版本 (新生代收集器).
    - Parallel Scavenge: 是吞吐量优先的收集器 (吞吐量指的是 运行用户代码时间 / (运行用户代码时间 + 垃圾收集时间))
    - Parellel Old: 是 Parallel 老年代版本.
    - CMS: 一种以获取最短回收停顿时间为目标的收集器.
    - G1: 一种兼顾吞吐量和停顿时间的收集器.

10. CMS 垃圾回收器:

    CMS (Concurrent Mark-Sweep) 以牺牲吞吐量为代价来获得最短回收停顿时间的来及回收器. 对于要求服务器响应速度的应用上, 这种垃圾器非常合适.  它基于**标记-清除**算法实现, 整个过程分为四个部分:

    - 初始标记 (CMS initial mark)
    - 并发标记 (CMS concurrent mark)
    - 重新标记 (CMS remark)
    - 并发清除 (CMS concurrent sweep)

    其中初始标记和重新标记需要 `Stop The World`, 而并发标记和并发清除都可以和用户线程一起并发执行. 它的缺点如下:

    - 对 CPU 资源敏感
    - 无法处理浮动垃圾, 可能出现 `Concurrent Mode Failure` 失败导致另一次 `Full GC` 产生.
    - 可能导致大量空间碎片.

11. 新生代和老年代收集器:

    - 新生代回收器: Serial, ParNew, Parallel Scavenge
    - 老年代回收器: Serial Old, Parallel Old, CMS
    - 整堆回收器: G1

12. 分代垃圾回收器工作原理:

    分代回收器有两个分区: 老生代和新生代. 新生代默认的空间占总空间的 1/3, 老生代默认的空间占总空间的 2/3. 新生代使用的是复制算法, 新生代里有 3 个分区: Eden, To Survivor, From Survivor, 它们默认的占比是 

    8:1:1.

    大多数情况下, 对象在新生代 Eden 区分配. 当 Eden 区没有足够的空间进行分配时, 虚拟机将发起一次 Mino r GC. 大的对象可以直接在老年代进行分配, 避免在 Eden 区以及两个 Survivor 区之间发生大量的内存复制, 可以通过 `-XX:PretenureSizeThreshold` 设置大于这个值的对象直接在老年代分配. 虚拟机给每个对象定义一个对象年龄计数器, 对象在 Eden 出生并经过一个 Minor GC 后仍然存活, 并且能够被 Survivor 容纳的话, 将被移动到 Survivor 空间中, 并且对象年龄设为 1. 对象在 Survivor 区每熬过一次 Minor GC, 年龄就增加 1 岁. 当它的年龄增加到一定程度(默认 15 岁), 将会晋升到老年代中, 可以通过 `-XX:MaxTenuringThreshold=15` 来进行设置.  

13. JVM 调优工具:
    - jconsole: 用于对 JVM 中的内存, 线程和类进行监控
    - jvisualvm: 可以分析内存快照, 线程快照, 程序死锁, 监控内存的变化, gc 变化.
14. 常用的 JVM 调优参数:
    - -Xms: 初始化堆大小(-Xms2g)
    - -Xmx: 堆最大内存(-Xmx2g)
    - -Xmn:新生代大小(-Xmn10M)
    - -XX:+PrintGC: 开启打印 gc 信息
    - -XX:+PrintGCDetail: 打印 gc 详细信息
    - -XX:+HeapDumpOnOutOfMemoryError: 出现内存溢出异常时 Dump 出当前的内存堆转储快照
    - -Xss128k: 设置栈内存容量
    - -XX:NewRatio=4: 设置年轻的和老年代内存比例为 1:4
    - -XX:SurvivorRatio=8: 设置新生代 Eden 和 Survivor 比例为 8:2

1. 参考:

    [1] : [Java Virtual Machine (JVM) & its Architecture](https://www.guru99.com/java-virtual-machine-jvm.html)

    [2] : [The JVM Architecture Explained](https://dzone.com/articles/jvm-architecture-explained)

------

#### 对象拷贝:

1. 克隆:

    * 使用克隆原因:

        克隆的对象可能包含一些已经修改过的属性, 而 `new` 出来的对象的属性都还是初始化的值, 当需要一个新的对象来保存当前对象的状态就需要使用克隆.

    - 深拷贝和浅拷贝:

        浅克隆只是当对象被复制时只复制它本身和其中包含的基本类型的成员变量, 而引用类型的成员对象并没有复制; 深克隆除了本身被复制外, 对象所包含的成员变量也将复制.

    - 克隆实现:

        - 实现 `Cloneable` 接口并重写 `Object` 类中的 `clone()` 方法. 如果你覆盖率非 final 类中的 `clone()` 方法, 则应该返回一个通过调用 `super.clone()` 得到的对象, 如果所有的超类都遵循这条规则, 那么调用 `super.clone()` 最终会调用 `Object#clone()` 方法, 从而创建出正确的实例.

            ```java
            @Override
            public Person clone() {
                try {
                    return (Person)super.clone();
                } catch(CloneNotSupportException e) {
                    throw new AssertionError();
                }
            }
            ```

            

        - 实现 `Serializable` 接口, 通过对象的序列化和反序列化实现克隆, 可以实现真正的深度克隆. 

------

#### 异常:

1. 分类:

    - Error: 错误, 一般指与虚拟机相关的问题.

    - Exception: 异常.

        - 运行时异常(RuntimeException): 

            常见运行时异常: IndexOutOfBoundsException, NullPointerException, ClassCastException, ArithmeticException 

        - 必检异常:

            必检异常必须在程序中显示捕获, 或者在方法声明抛出异常. 子类在实现或重写父类方法时, 需要遵循***两小***原则, 即: **子类方法声明抛出的异常应该是父类方法声明抛出的异常的子类或相同; 子类方法声明抛出的异常不允许比父类方法声明抛出的异常多.**

            常见必检异常: IOException, FileNotFoundException, SocketException, SQLException, ReflectiveOperationException, ClassNotFoundException, NotSuchMethodException, NotSuchFieldException.

2. 异常相关处理:

    - 异常捕获:

        ```java
        try {
        	File file = new File("test.txt");
        } catch(FileNotFoundException e) {
        	e.printStackTrace();
        }
        ```

        

    - 声明抛出异常:

        ```java
        public File createFile() throws FileNotFoundException {
            File file = new File("test.txt");
            return file;
        }
        ```

    - 显示关闭资源:

        ```java
        // 在 try-catch-finally 块中, catch, finally 可以省略其中一个, 但是不能同时省略.
        try {
        	FileInputStream inputStream = new FileInputStream("test.txt");
        } catch(FileNotFoundException e) {
        	e.printStackTrace();
        } finally {
        	inputStream.close();
        }
        ```

    - 自动关闭资源:

        ```java
        try (FileInputStream inputStream = new FileInputStream("test.txt")) {
            // do some thing
        }
        ```

3. 异常运行分析:

    下面代码没有出现异常返回值为 1; 出现 Exception 异常返回 2; 出现 Exception 以外的异常, 方法非正常退出, 没有返回值.

    ```java
    public int inc() {
        int x;
        try {
            x = 1;
            return x;
        } catch(Exception e) {
            x = 2;
            return x;
        } finally {
            x = 3;
        }
    }
    ```

    ```java
    public int inc();
    	Code:
    		Stack = 1, Locals = 5, Args_size = 1
            // try 块中的代码对应字节码    
            0: iconst_1  // 将常量 1 推送至栈顶
            1: istore_1  // 将栈顶变量保存到第二个本地变量, 也就是 x 中(第一个保存的是 this), 此时 x = 1  
            2: iload_1   // 将第二个本地变量值推送至栈顶
            3: istore 4  // 将栈顶变量保存到第五个本地变量, 也就是 returnValue 中, 此时返回值为 1
            // finally 块中的代码对应字节码    
            5: iconst_3  // 将常量 3 推送至栈顶
            6: istore_1  // 将栈顶变量保存到第二个本地变量, 也就是 x 中, 此时 x = 3
            7: iload 4   // 将第五个本地变量, 也就是 returnValue 推送至栈顶
                
            9: ireturn   // 从当前方法返回 int , 也就是 returnValue, 到这里是方法正常结束返回
                
            // catch 块中的代码对应字节码, 当出现 Exception 异常时会走到这里
            10: astore_2 // 将变量保存到第三个本地变量, 也就是 e 中, 相当于给 e 赋值
            11: iconst_2 // 将常量 2 推送至栈顶
            12: istore_1 // 将栈顶变量保存到第二个本地变量, 也就是 x 中, 此时 x = 2
            13: iload_1  // 将第二个本地变量值推送至栈顶
            14: istore 4 // 将栈顶变量保存到第五个本地变量, 也就是 returnValue 中, 此时返回值为 2
            // finally 块中的代码对应字节码     
            16: iconst_3 // 将常量 3 推送至栈顶
            17: istore_1 // 将栈顶变量保存到第二个本地变量, 也就是 x 中, 此时 x = 3
            18: iload  4 // 将第五个本地变量, 也就是 returnValue 推送至栈顶
                   
            20: ireturn  // 从当前方法返回 int , 也就是 returnValue, 到这里是方法正常结束返回
            // 当出现不是 Exception 类型的异常时走到这里    
            21: astore_3 // 将变量保存到第四个本地变量
            22: iconst_3 // 将常量 3 推送至栈顶
            23: istore_1 // 将栈顶变量保存到第二个本地变量, 也就是 x 中, 此时 x = 3
            24: aload_3  // 将第四个本地变量的值推送至栈顶
            25: athrow   // 将栈顶的异常抛出
            
                
    ```

------

#### Java Web:

1. JSP 和 Servlet 区别:

    JSP(Java Server Page) 最终会生成一个对应的 `Servlet`, JSP 中的内容会在生成的 `Servlet` 的 `_jspService()` 方法中以流的形式生成, JSP 中声明的变量和方法将会称为该 `Servlet` 的成员变量. JSP 将视图的表现从 `Servlet` 中抽取出来, 避免了直接在 Java 代码中耦合大量的 HTML 标签. 

2. JSP 内置对象:

    JSP 的 9 大内置对象都是生成的对应的 `Servlet` 的 `_jspService()` 方法的局部变量

    - request: HttpServletRequest 对象实例, 该对象封装了一次请求, 客户端的请求参数都封装在该对象里. 常用方法有 `getParamter()`, `getParameterValues()`, `setAttribute()`, `getAttribute()`, `setCharacterEncoding()`.
    - response: HttpServletResponse 对象实例, 代表服务器对客户端的响应. response 对象常用于重定向, 常用的方法有 `getOutputStream()`, `setRedirect()`.
    - session: HttpSession 对象实例, 该对象代表一次会话, 当客户端浏览器与站点建立连接时, 会话开始; 当客户端关闭浏览器时, 会话结束. 服务端根据 sesssionId 进行区分.
    - pageContext: PageContext 对象实例, 代表 JSP 页面上下文. 常用方法有 `getServletContex()`, `getServletConfig()`, `getOut()`.
    - application: ServletContext 对象实例, 代表 JSP 所属的 Web 应用本身.
    - config: ServletConfig 对象实例, 代表该 JSP 的配置信息.
    - page: 代表页面本身, 也就是 Servlet 中的 this.
    - out: JspWriter 对象实例, 代表 JSP 页面的输出流, 用于输出内容, 形成 HTML 页面.
    - exception: Throwable 对象实例, 代表其它页面中的异常和错误, 只有当编译指令  page 的 isErrorPage 属性为 true 时, 该对象才可以使用.
    
3. JSP 四种作用域:

    - application: 对于整个 Web 页面有效, 一旦 JSP, Servlet 将数据放入 application 中, 该数据将可以被该应用下其它所有 JSP, Servlet 访问.
    - session: 仅对一次会话有效, 一旦 JSP, Servlet 将数据放入 session 中, 该数据将可以被本次会话的其它所有的 JSP, Servlet 访问.
    - request: 仅对本次请求有效, 一旦 JSP, Servlet 将数据放入 request 中, 该数据将可以被本次请求的其它所有的 JSP, Servlet 访问.
    - page: 仅对当前页面有效. 一旦 JSP, Servlet 将数据放入 request 中, 该数据只可以被当前页面的 JSP 脚本, 声明部分访问.

4. Session 和 Cookie 区别:

    // TODO 登陆的实现, 忘记在哪里看的可以从 JSESSIONID 中解析出用户名和密码了

    Cookie 是服务器在本地机器上存储的小段文本并随每个请求发送至同一个服务器. Cookie 采用的是在客户端保持状态的方案, 它的使用需要用户打开客户端的 Cookie 支持. Cookie 主要包括: 名字, 值, 过期时间, 路径和域. 路径与域一起构成了 Cookie 的作用范围. 若不设置过期时间, 则表示这个 Cookie 的生命周期为浏览器会话期间, 关闭浏览器, Cookie 就消失了.

    Session 机制是一种服务器端的机制, 客户端第一次访问服务器时, 为生成一个 sessionId, 这个 sessionId以 Cookie 形式保存在客户端,  服务器端根据这个 sessionId 就可以判断该请求是同一个客户端发送的请求.

    浏览器不支持 Cookie 解决办法: 

    - URL重写，就是把 sessionId 直接附加在 URL 路径的后面.
    - 表单隐藏字段。就是服务器会自动修改表单，添加一个隐藏字段，以便在表单提交时能够把 sessionId 传递回服务器

5. 避免 SQL 注入:

    - 使用预处理 PreparedStatement
    - 使用正则表达式过滤掉字符中的特殊字符

6. XSS 攻击以及避免:

    跨站脚本攻击 (Cross-Site Scripting) 是一种代码注入攻击. 攻击者通过在目标网站上注入恶意脚本, 使之在用户的浏览器上运行.

    避免的核心是必须对输入的数据做过滤处理.

7. CSRF 攻击及避免:

    跨站请求伪造 (Cross-Site Request Forgery) 是盗用者盗用了你的身份, 以你的名义发送恶意请求.

    - 验证请求来源地址
    - 关键操作添加验证码
    - 在请求地址添加 token 并验证(Spring Security 中默认开启)

8. 参考:

    [1] : [Cookie 与 Session 的区别](https://juejin.im/entry/5766c29d6be3ff006a31b84e)

    [2] : [sql注入基础原理](https://www.jianshu.com/p/078df7a35671)

    [3] : [如何防止XSS攻击？](https://tech.meituan.com/2018/09/27/fe-security.html)

    [4] : [如何防止CSRF攻击？](https://juejin.im/post/5bc009996fb9a05d0a055192)

------

#### 网络

1. HTTP 响应码 301 和 302 区别:

    - 301: 永久重定向. 301 对搜索引擎优化更有利.
    - 302: 暂时重定向. 302 可能导致域名劫持.

    浏览器在拿到服务器返回的这个状态码后会自动跳转到一个新的 URL 地址, 这个地址可以从响应的 `Location` 首部中获取.

2. forward 和 redirect 区别:

    - forward: forward 是发生在服务器端, 客户端并不知晓这个过程, 反映在浏览器就是浏览器地址不会发生变化. 服务器在进行转发到另一个 url 时, 可以共享客户端的 request 数据. 
    - redirect: redirect 会给客户端的第一次请求返回一个状态码为 302 的响应, 并且新的请求地址在响应的响应头的 `Location` 字段里面. 然后客户端获取到新的请求地址后, 向新的请求地址发送请求. 客户端是知晓重定向的过程的, 反映在浏览器就是浏览器地址会发生变化.

3. OSI 的七层模型:

    - 物理层: 利用传输介质为数据链路层提供物理连接, 实现比特流的透明传输.
    - 数据链路层: 负责建立和管理节点间的链路.
    - 网络层: 通过路由选择算法, 为报文或分组通过通信子网选择最恰当的路径.
    - 传输层: 向用户提供可靠的端到端的差错和流量控制, 保证报文的正确传输.
    - 会话层: 向两个实体的表示层提供建立和使用连接的方法.
    - 表示层: 处理用户信息的表示问题, 如编码, 数据格式转换和加密解密等.
    - 应用层: 直接向用户提供服务, 完成用户希望在网络上完成的各种工作.

4. 参考:

    [1] : [http状态码301和302详解及区别](https://blog.csdn.net/grandPang/article/details/47448395)

    [2] : [HTTP redirect: 301 (permanent) vs. 302 (temporary)](https://stackoverflow.com/questions/1393280/http-redirect-301-permanent-vs-302-temporary)

    

