#### 一、存储文件结构

- topic： 可以理解为一个消息队列的名字
- partition：为了实现扩展性，一个非常大的topic可以分布到多个 broker（即服务器）上，一个topic可以分为多个
- partition，每个partition是一个有序的队列
- segment：partition物理上由多个segment组成
- message：每个segment文件中实际存储的每一条数据就是message
- offset：每个partition都由一系列有序的、不可变的消息组成，这些消息被连续的追加到partition中，partition中的每个消息都有一个连续的序列号叫做offset,用于partition唯一标识一条消息

![1582190686836.png](E:\work\2020\learning\images\bVbDJMc)

#### 二、Partition分区

本例中topic名称为test-topic，默认设置partition为3，topic创建成功后默认的存储位置在：/tmp/kafka-logs下，分区分别以topic名称-分区数命名，(不考虑副本的情况)如下：

> //分布在不同的broker节点上  
> test-topic-0  
> test-topic-1  
> test-topic-2

分区文件下到底存了那些东西

> 如下，其实每个分区下保存了很多文件，而概念上我们把他叫segment，即每个分区都是又多个segment构成的，其中index（索引文件），log（数据文件），time index（时间索引文件）统称为一个segment
>
> test-topic-0  
> ├── 00000000000000000001.index  
> ├── 00000000000000000001.log  
> ├── 00000000000000000001.timeindex  
> ├── 00000000000000001018.index  
> ├── 00000000000000001018.log  
> ├── 00000000000000001018.timeindex  
> ├── 00000000000000002042.index  
> ├── 00000000000000002042.log  
> ├── 00000000000000002042.timeindex

#### 三、Segment存储

##### 3.1 使用kafka自带脚本发送测试数据

```shell
sh kafka-producer-perf-test.sh --topic test-topic --num-records 50000000 --record-size 1000 --throughput 10000000  --producer-props bootstrap.servers=192.168.60.201:9092
```

##### 3.2 使用kafka自带脚本Dump index

```shell
sh kafka-run-class.sh kafka.tools.DumpLogSegments --files /tmp/kafka-logs/test-topic-0/00000000000000001018.index --print-data-log
```

> offset: 1049 position: 16205  
> offset: 1065 position: 32410  
> offset: 1081 position: 48615  
> offset: 1097 position: 64820  
> offset: 1113 position: 81025  
> offset: 1129 position: 97230

通过dump index我们发现其实索引文件中其实就保存了offset和position，分别是消息的offset也就是具体那一条消息，position表示具体消息存储在log中的物理地址

**疑问一**：通过上面数据可以看出，kafka并不是每个offset都保存了，每隔6个offset存储一条索引数据，为什么在index文件中这些offset编号不是连续的呢？

> 因为index文件中并没有为数据文件中的每条消息都建立索引，而是采用了稀疏存储的方式，每隔一定字节的数据建立一条索引。这样避免了索引文件占用过多的空间，从而可以将索引文件保留在内存中。但缺点是没有建立索引的Message也不能一次定位到其在数据文件的位置，从而需要做一次顺序扫描，但是这次顺序扫描的范围就很小了。

##### 3.3 使用kafka自带脚本Dump log

```shell
sh kafka-run-class.sh kafka.tools.DumpLogSegments --files /tmp/kafka-logs/test-topic-0/00000000000000001018.log --print-data-log
```

##### 3.4消费者如何通过offset查找message？

假如我们想要读取offset=1066的message，需要通过下面2个步骤查找。

###### **1.查找segment file**

00000000000000000000.index表示最开始的文件，起始偏移量(offset)为0.第二个文件00000000000000001018.index的消息量起始偏移量为1019 = 1018 + 1.同样，第三个文件00000000000000002042.index的起始偏移量为2043=2042 + 1，其他后续文件依次类推，以起始偏移量命名并排序这些文件，只要根据offset 二分查找文件列表，就可以快速定位到具体文件。 当offset=1066时定位到00000000000000001018.index|log

###### **2.通过segment file查找message**

通过第一步定位到segment file，当offset=1066时，依次定位到00000000000000001018.index的元数据物理位置和00000000000000001018.log的物理偏移地址，此时我们只能拿到1065的物理偏移地址，然后再通过00000000000000368769.log顺序查找直到offset=1066为止。每个message都有固定的格式很容易判断是否是下一条消息

![1582270240253.png](E:\work\2020\learning\images\bVbDJNo)