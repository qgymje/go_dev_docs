# Redis vs NSQ vs RabbitMQ

## 概述
并不是比较性能问题，而是比较作为队列，其数据分发形式的异同

### Redis

Redis使用```lpush+brpop``` 或 ```rpush+blpop```时，作为消费端，其模式是当有多个消费端时，消息会按Round-Robin模式分发

### NSQ

redis使用topic + channel模式，当有多个消费端使用同一个channel时，消息是按Round-Robin模式分发，如果有不同的channel，则注册不同channel的消费者会收到相同的信息


### RabbitMQ

RabbitMQ 则通过更多的抽象，提供更强大的消息分发模式，比如Reids的模式，则通过设计exchange的类型为```direct```可实现，而实现NSQ的topic + channel的模式则通过设置exchange的类型为```topic```即可实现

