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

    - request: HttpServletRequest 

