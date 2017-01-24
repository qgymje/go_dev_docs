# Redis vs NSQ vs RabbitMQ

## 概述
并不是比较性能问题，而是比较作为队列，其数据分发形式的异同

### Redis

Redis使用```lpush+brpop``` 或 ```rpush+blpop```时，作为消费端，其模式是当有多个消费端时，消息会按Round-Robin模式分发
Redis可以```brpop```或```blpop```也可以同时监听多个list, 接收多方数据
Redis的pubsub模式支持消息的广播

### NSQ

redis使用topic + channel模式，当有多个消费端使用同一个channel时，消息是按Round-Robin模式分发，如果有不同的channel，则注册不同channel的消费者会收到相同的信息


### RabbitMQ

RabbitMQ 则通过更多的抽象，提供更强大的消息分发模式，比如Reids的模式，则通过设计exchange的类型为```direct```可实现，而实现NSQ的topic + channel的模式则通过设置exchange的类型为```fanout```即可实现

除此之外RabbitMQ提供了更多的功能，比如有更灵活的topic模式的exchange, 生产者Confirmation机制, 消费者ACK机制, 数据存储，排它队列等
