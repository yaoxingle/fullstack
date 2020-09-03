1、producer.properties

max.request.size,默认是1M，上述一个record size就64M，因此可以配置大于64M即可，对应的需要配置message.max.bytes(需要保证比max.request.size配置的size大)
buffer.memory，默认是32M，需要修改成大于64M即可

2、server.properties

message.max.bytes，默认是大约是0.95M（1000012byte），需要保证比max.request.size大，当然如果你是集群情况下进行性能测试并且有副本，还需要对应的再配置参数replica.fetch.max.bytes(需要和message.max.bytes差不多即可，这样就会让副本能够快速同步)
