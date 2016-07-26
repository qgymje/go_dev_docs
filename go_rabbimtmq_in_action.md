RabbimtMQ Go client in action
====

准备工作
==

安装rabbitmq server, 以macOS为例
```
$ brew install rabbitmq
```
安装成功后, 启动server
```
$ rabbitmqctl status

Status of node rabbit@localhost ...
[{pid,1051},
 {running_applications,
      [{rabbitmq_management_visualiser,"RabbitMQ Visualiser","3.6.1"},]}]
......
```
RabbitMQ是Erlang写的, 因此输出数据也是Erlang的结构,这里是list嵌套tuple, 这里不作详述


安装client
```
$ go get -u -v github.com/streadway/amqp
```


正式编码
===

建立Connection
----
```
conn, err := amqp.Dial("amqp://guest:guest@localhost:5672/")
if err != nil {
    log.Fatal(err)
}
defer conn.Close()
```

连接Channel
----
```
ch, err := conn.Channel()
if err != nil {
    log.Fatal(err)
}

```

声明Exchnage
----
```
//func (me *Channel) ExchangeDeclare(name, kind string, durable, autoDelete, internal, noWait bool, args Table) error 
if err := ch.ExchangeDeclare(
    exchange, // exchange name
    "direct",     // exchange type, 类型: direct, fanout, topic, 三种不同的类型, 详情看mq手册
    true,     // durable 指exchange是否持久化, 也就是保存到nmesia数据库中
    false,    // autoDelete 是exchnage是否自动删除
    false,
    false,
    nil,
); err != nil {
    log.Fatal(err)
}

```

生产者发消息
----
```
//func (me *Channel) Publish(exchange, key string, mandatory, immediate bool, msg Publishing) error 
err := ch.Publish(
    exchange,
    routingKey, // routing key is here
    false,
    false,
    amqp.Publishing{
        Headers:         amqp.Table{},
        ContentType:     "text/palin",
        ContentEncoding: "",
        DeliveryMode:    amqp.Transient,
        Body:            []byte("hello world"),
        Priority:        0,
      },
)
```

生产者消息发送确认
----
```
func confirm(ch *amqp.Channel) {
	// 确保消息收到
	//func (me *Channel) Confirm(noWait bool) error
	if err := ch.Confirm(false); err != nil {
		log.Fatal(err)
	}

	//func (me *Channel) NotifyPublish(confirm chan Confirmation) chan Confirmation
	confirms := ch.NotifyPublish(make(chan amqp.Confirmation, 1))
	go func() {
		for {
			if confirmed := <-confirms; confirmed.Ack {
				log.Printf("confirmed ack, tag: %d", confirmed.DeliveryTag) // tag should be increased!
			} else {
				log.Printf("failed ack: tag: %d", confirmed.DeliveryTag)
			}
		}
	}()
}

```

消费者声明队列以及绑定队列
----
消费者与生产者是分开的两个部分, 通常是两个程序, 但建立连接与创建通道是一样的流程, 消费者需要声明队列
```
//func (me *Channel) QueueDeclare(name string, durable, autoDelete, exclusive, noWait bool, args Table) (Queue, error)
q, err := ch.QueueDeclare(
    "hello",
    false,
    false,
    false,
    false,
    nil,
)
if err != nil {
    log.Fatal(err)
}

//func (me *Channel) QueueBind(name, key, exchange string, noWait bool, args Table) error
if err = ch.QueueBind(
    q.Name,
    "hello-key", // here is the binding key
    "hello",     // exchange name
    false,
    nil,
); err != nil {
    log.Fatal(err)
}
```

消费者接收消息
----
```
//func (me *Channel) Consume(queue, consumer string, autoAck, exclusive, noLocal, noWait bool, args Table) (<-chan Delivery, error)
msgs, err := ch.Consume(
    q.Name,
    "",    // consumer
    false, // auto-ack
    false, // exclusive
    false, // no-local
    false,
    nil,
)
if err != nil {
    log.Fatal(err)
}

```

之后完善细节(TODO, will never do, I guess...)
