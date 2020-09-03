#### 1、线程安全

##### 原子性

> 对共享内存的操作必须是要么全部执行直到执行结束，且中间过程不能被任何外部因素打断，要么就不执行。

##### 可见性

> 多线程操作共享内存时，执行结果能够及时的同步到共享内存，确保其他线程对此结果及时可见。

##### 有序性

> 程序的执行顺序按照代码顺序执行，在单线程环境下，程序的执行都是有序的，但是在多线程环境下，JMM 为了性能优化，编译器和处理器会对<u>指令进行重排</u>，程序的执行会变成无序

保证线程安全的常用方式

| 方式         | 含义           | 原则                                                         |
| ------------ | -------------- | ------------------------------------------------------------ |
| lock         | 锁             | 重量型（消耗内存较多），原子性，可见的，顺序：公平锁         |
| synchronized | 同步的，互斥的 | 重量型（消耗内存较多），原子性，可见的                       |
| volitail     | 易变的         | 轻量型（消耗内存较少），非原子性，可见的，主要针对共享变量，改变时候其他线程中也随时会改变，但依旧是非原子性的，不是任何场景适合使用 |

#### 2、线程同步

##### synchronized关键字

保证方法或代码块操作的原子性

<u>1、同步代码块采用monitorenter、monitorexit指令显式的实现。</u>

<u>2、同步方法则使用ACC_SYNCHRONIZED标记符隐式的实现</u>。

> **Monitor** 的工作机理
>
> 1. **enter**：线程进入同步方法中。**monitorenter**，进入左边的entry set
> 2. **acquire**：为了继续执行临界区代码，线程必须获取 Monitor 锁**acquire**。如果获取锁成功，将成为该监视者对象的拥有者。任一时刻内，监视者对象只属于一个活动线程（**The Owner**）
> 3. **release**：拥有监视者对象的线程可以调用 **wait()** 进入等待集合（**Wait Set**），同时**释放监视锁**，进入等待状态**wait set**。
> 4. **notify&acquire**：其他线程（**the owner**）调用 **notify() / notifyAll()** 接口唤醒等待集合（**Wait Set**）中的线程，这些等待的线程需要**重新获取**监视锁后才能执行 wait() 之后的代码。
> 5. **release&exit**：同步方法执行完毕了，线程退出临界区，并释放监视锁。

##### volatile

保证被 Volatile 关键字描述变量的操作具有可见性和有序性（禁止指令重排

>1.Volatile 只对基本类型 (byte、char、short、int、long、float、double、boolean) 的赋值 操作和对象的引⽤赋值操作有效。
>2 对于 `i++` 此类复合操作， Volatile 无法保证其有序性和原子性。
>3.相对 Synchronized 来说 Volatile 更加轻量一些。

##### java.util.concurrent.atomic

> `java.util.concurrent.atomic` 包提供了一系列的 `AtomicBoolean`、`AtomicInteger`、`AtomicLong` 等类。使用这些类来声明变量可以保证对其操作具有原子性来保证线程安全。
>
> 实现原理上与 Synchronized 使用 Monitor（监视锁）保证资源在多线程环境下阻塞互斥访问不同，`java.util.concurrent.atomic` 包下的各原子类基于 `CAS(CompareAndSwap)` 操作原理实现。
>
> [CAS](https://link.zhihu.com/?target=https%3A//link.juejin.im/%3Ftarget%3Dhttps%3A%2F%2Fwww.jianshu.com%2Fp%2Fae25eb3cfb5d) 又称无锁操作，一种乐观锁策略，原理就是多线程环境下各线程访问共享变量不会加锁阻塞排队，线程不会被挂起。通俗来讲就是一直循环对比，如果有访问冲突则重试，直到没有冲突为止。
>
> 1、CAS是一个**原子操作**，它**比较**一个内存位置的值并且**只有相等时修改**这个内存位置的值为新的值，保证了新的值总是基于最新的信息计算的，如果有其他线程在这期间修改了这个值则CAS失败。CAS返回是否成功或者内存位置原来的值用于判断是否CAS成功。
>
> 2、JVM中的CAS操作是利用了CPU处理器提供的**CMPXCHG指令(compare X change)**实现的。

##### Lock

Lock 也是 `java.util.concurrent` 包下的一个接口，定义了一系列的锁操作方法。Lock 接口主要有 ReentrantLock，ReentrantReadWriteLock.ReadLock，ReentrantReadWriteLock.WriteLock 实现类。与 Synchronized 不同是 Lock 提供了获取锁和释放锁等相关接口，使得使用上更加灵活，同时也可以做更加复杂的操作