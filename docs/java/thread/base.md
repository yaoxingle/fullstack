#### 1、生命周期

![](images/2018070117435683.jpg)

#### 2、线程状态

Java线程具有五中基本状态

##### 新建状态（New）

当线程对象对创建后，即进入了新建状态，如：Thread t = new MyThread();

##### 就绪状态（Runnable）

当调用线程对象的start()方法（t.start();），线程即进入就绪状态。处于就绪状态的线程，只是说明此线程已经做好了准备，随时等待CPU调度执行，并不是说执行了t.start()此线程立即就会执行；

##### 运行状态（Running）

当CPU开始调度处于就绪状态的线程时，此时线程才得以真正执行，即进入到运行状态。注：就   绪状态是进入到运行状态的唯一入口，也就是说，线程要想进入运行状态执行，首先必须处于就绪状态中；

##### 阻塞状态（Blocked）

处于运行状态中的线程由于某种原因，暂时放弃对CPU的使用权，停止执行，此时进入阻塞状态，直到其进入到就绪状态，才 有机会再次被CPU调用以进入到运行状态。根据阻塞产生的原因不同，阻塞状态又可以分为三种：

​	1.等待阻塞：运行状态中的线程执行wait()方法，使本线程进入到等待阻塞状态；位于对象等待池中的阻塞状态（Blocked in object’s wait pool）

​	2.同步阻塞 -- 线程在获取synchronized同步锁失败(因为锁被其它线程所占用)，它会进入同步阻塞状态；（位于对象锁池中的阻塞状态（Blocked in object’s lock pool））

​	3.其他阻塞（Otherwise Blocked） -- 通过调用线程的sleep()或join()或发出了I/O请求时，线程会进入到阻塞状态。当sleep()状态超时、join()等待线程终止或者超时、或者I/O处理完毕时，线程重新转入就绪状态。

##### 死亡状态（Dead）

线程执行完了或者因异常退出了run()方法，该线程结束生命周期。

#### 3、常用方法

| 方法                  | 含义                                                         |
| --------------------- | ------------------------------------------------------------ |
| sleep()               | 暂停阻塞等待一段时间，时间过了就继续。<font color='red'>注意这个是不释放“锁”的</font> |
| wait()                | 也是阻塞和等待，但是需要notify来唤醒。<font color='red'>会释放锁</font> |
| join()                | 在一个线程中调用other.join(),将等待other执行完后才继续本线程 |
| notify()、notifyAll() | 唤醒线程                                                     |
| yield()               | 当前线程可转让cpu控制权，让别的就绪状态线程运行（切换），也会等待阻塞一段时间，但是时间不是由客户控制了 |
| interrupte()          | 打断线程，代替过时方法stop()；interrupt， 它没有stop那么的粗暴，因为可以用catch捕捉到InterruptedException这个异常 |
| setPriority()         | MIN_PRIORITY 最小优先级=1 ， NORM_PRIORITY 默认优先级=5 ，MAX_PRIORITY 最大优先级=10 |

wait()需要先获取对象的monitor锁，才能调用，否则报出异常java.lang.IllegalMonitorStateException，常用写法：

```java
synchronized(obj){
            try {
                obj.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
```



