  [redis训练营课前须知(1).pdf](redis训练营课前须知(1).pdf) 

端口：6379 

Redis （remote dictionary service) 远程字典服务

特性：

1、快

2、持久化、过期、淘汰策略

3、高可用、集群

4、单线程命令执行

5、IO多路复用

数据类型：

string hash list set zet



内存淘汰

过期策略

​	定时过期

​	惰性过期

​	定期过期

淘汰策略

​	

| 策略            | 含义                                                         |
| --------------- | ------------------------------------------------------------ |
| volatile-lru    | 根据LRU算法删除设置了超时属性(expire)的键，直到腾出足够内存为止。如果没有可删除的键对象，回退到noeviction策略 |
| allkeys-lru     | 根据LRU算法删除键，不管数据有没有设置超时属性，直到腾出内存为止 |
| volatile-lfu    | 在带有过期时间的键中选中最不常用的                           |
| allkeys-lfu     | 在所有的建中选中最不常用                                     |
| volatile-random | 在带有过期时间的键中随机选择                                 |
| allkeys-random  | 随机删除所有键，直到腾出足够内存为止                         |
| volatile-ttl    | 根据键值对象的ttl对象；删除最近将要过期数据，如果没有，回退到noevicition |
| noeviction      | 默认策略，不会删除任何数据，拒绝所有写入操作并返回客户端错误信息OOM,此时Redis只相应读操作。 |

LRU least recently used 最近未使用

​	

​	

​	

​	

​	

​	

​	