1. 实现原理:

   ```java
   /**
    * Possible state transitions:
    * NEW -> COMPLETING -> NORMAL
    * NEW -> COMPLETING -> EXCEPTIONAL
    * NEW -> CANCELLED
    * NEW -> INTERRUPTING -> INTERRUPTED
    */
   
   /* 这个状态是 volatile 修饰的, 
    * 客户线程通过判断该状态来判断线程的运行的状态
    * 服务线程在完成任务后会对该状态进行更新
    */
   private volatile int state;
   private static final int NEW          = 0;
   private static final int COMPLETING   = 1;
   private static final int NORMAL       = 2;
   private static final int EXCEPTIONAL  = 3;
   private static final int CANCELLED    = 4;
   private static final int INTERRUPTING = 5;
   private static final int INTERRUPTED  = 6;
   
   public V get() throws InterruptedException, ExecutionException {
       int s = state;
       if (s <= COMPLETING)
           // 阻塞当前线程
           s = awaitDone(false, 0L);
       return report(s);
   }
   
   private int awaitDone(boolean timed, long nanos)
       throws InterruptedException {
       final long deadline = timed ? System.nanoTime() + nanos : 0L;
       WaitNode q = null;
       boolean queued = false;
       for (;;) {
           if (Thread.interrupted()) {
               removeWaiter(q);
               throw new InterruptedException();
           }
   
           int s = state;
           if (s > COMPLETING) {
               if (q != null)
                   q.thread = null;
               return s;
           }
           else if (s == COMPLETING) // cannot time out yet
               Thread.yield();
           else if (q == null)
               q = new WaitNode();
           else if (!queued)
               queued = UNSAFE.compareAndSwapObject(this, waitersOffset,
                                                    q.next = waiters, q);
           else if (timed) {
               nanos = deadline - System.nanoTime();
               if (nanos <= 0L) {
                   removeWaiter(q);
                   return state;
               }
               LockSupport.parkNanos(this, nanos);
           }
           else
               LockSupport.park(this);
       }
   }
   ```

   

2. 参考:

   [1] : [Java多线程 - Future模式](<https://www.jianshu.com/p/949d44f3d9e3>)

   [2] : [深入理解Future模式原理与技术](<https://www.javazhiyin.com/21056.html>)

   [3] : [彻底理解Java的Future模式](https://www.cnblogs.com/cz123/p/7693064.html)

