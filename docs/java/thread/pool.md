##### 一、四种线程池 

Java通过Executors提供四种线程池，分别为： 
1,newCachedThreadPool
创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程。 
2,newFixedThreadPool 
创建一个定长线程池，可控制线程最大并发数，超出的线程会在队列中等待。 
3,newScheduledThreadPool 
创建一个定长线程池，支持定时及周期性任务执行。 
4,newSingleThreadExecutor 
创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。



##### 二、五种状态

线程池的5种状态：RUNNING、SHUTDOWN、STOP、TIDYING、TERMINATED。

```
private static final int RUNNING    = -1 << COUNT_BITS;
private static final int SHUTDOWN   =  0 << COUNT_BITS;
private static final int STOP       =  1 << COUNT_BITS;
private static final int TIDYING    =  2 << COUNT_BITS;
private static final int TERMINATED =  3 << COUNT_BITS;
```

1. RUNNING：线程池一旦被创建，就处于 RUNNING 状态，任务数为 0，能够接收新任务，对已排队的任务进行处理。

2. SHUTDOWN：不接收新任务，但能处理已排队的任务。调用线程池的 shutdown() 方法，线程池由 RUNNING 转变为 SHUTDOWN 状态。

3. STOP：不接收新任务，不处理已排队的任务，并且会中断正在处理的任务。调用线程池的 shutdownNow() 方法，线程池由(RUNNING 或 SHUTDOWN ) 转变为 STOP 状态。

4. TIDYING：

- SHUTDOWN 状态下，任务数为 0， 其他所有任务已终止，线程池会变为 TIDYING 状态，会执行 terminated() 方法。线程池中的 terminated() 方法是空实现，可以重写该方法进行相应的处理。
- 线程池在 SHUTDOWN 状态，任务队列为空且执行中任务为空，线程池就会由 SHUTDOWN 转变为 TIDYING 状态。
- 线程池在 STOP 状态，线程池中执行中任务为空时，就会由 STOP 转变为 TIDYING 状态。

5. TERMINATED：线程池彻底终止。线程池在 TIDYING 状态执行完 terminated() 方法就会由 TIDYING 转变为 TERMINATED 状态。

   

##### 三、ThreadPoolExecutor构造方法

```java
// Java线程池的完整构造函数
public ThreadPoolExecutor(
  int corePoolSize, // 线程池长期维持的线程数，即使线程处于Idle状态，也不会回收。
  int maximumPoolSize, // 线程数的上限
  long keepAliveTime, TimeUnit unit, // 超过corePoolSize的线程的idle时长，/ 超过这个时间，多余的线程会被回收。
  BlockingQueue<Runnable> workQueue, // 任务的排队队列
  ThreadFactory threadFactory, // 新线程的产生方式
  RejectedExecutionHandler handler // 拒绝策略
)
```



##### 四、RejectedExecutionHandler提供了四种方式来处理任务拒绝策略

1、直接丢弃（DiscardPolicy）

2、丢弃队列中最老的任务(DiscardOldestPolicy)。

3、抛异常(AbortPolicy)

4、将任务分给调用线程来执行(CallerRunsPolicy)。

##### 

##### 五、线程池的工作顺序

> If fewer than corePoolSize threads are running, the Executor always prefers adding a new thread rather than queuing.
> If corePoolSize or more threads are running, the Executor always prefers queuing a request rather than adding a new thread.
> If a request cannot be queued, a new thread is created unless this would exceed maximumPoolSize, in which case, the task will be rejected.
>
> corePoolSize -> 任务队列 -> maximumPoolSize -> 拒绝策略



##### 六、获取单个结果

通过`submit()`向线程池提交任务后会返回一个`Future`，调用`V Future.get()`方法能够阻塞等待执行结果，`V get(long timeout, TimeUnit unit)`方法可以指定等待的超时时间。



##### 七、获取多个结果

如果向线程池提交了多个任务，要获取这些任务的执行结果，可以依次调用`Future.get()`获得。但对于这种场景，我们更应该使用[ExecutorCompletionService](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/ExecutorCompletionService.html)，该类的`take()`方法总是阻塞等待某一个任务完成，然后返回该任务的`Future`对象。向`CompletionService`批量提交任务后，只需调用相同次数的`CompletionService.take()`方法，就能获取所有任务的执行结果，获取顺序是任意的，取决于任务的完成顺序：

```java
void solve(Executor executor, Collection<Callable<Result>> solvers)
   throws InterruptedException, ExecutionException {
   CompletionService<Result> ecs = new ExecutorCompletionService<Result>(executor);// 构造器 
   for (Callable<Result> s : solvers)// 提交所有任务
       ecs.submit(s);
   int n = solvers.size();
   for (int i = 0; i < n; ++i) {// 获取每一个完成的任务
       Result r = ecs.take().get();
       if (r != null)
           use(r);
   }
}
```

