1. Queue 子接口:

   - BlockingQueue: 

     - add() 方法: 如果容量满了则抛出 `IllegalStateException`

     - offer() 方法: 如果容量满了则返回 false

       实际上实现是 `add()` 方法去调用了 `offer()` 方法

     - 实现类:

       - ArrayBlockingQueue: 在初始化值时需要指定容量, 底层依赖 `ReentrantLock` 实现的默认为非公平锁的锁.
       - LinkedBlockingQueue: 在初始化时指定容量, 否则默认容量大小为整数的大小,  底层也是依赖 `ReentrantLock` 实现的.

   - Deque 子接口: 实现了该接口就可以被当做队列或者栈来使用, 不推荐使用 `Stack`, 它是基于 `Vector` 实现的, 是线程安全的, 比较重量级.

     - 实现类:
       - LinkedArrayList: 
       - ArrayDequeue:

     

