#RabbitMQ and Go: "Hello World"

[源文地址](https://www.rabbitmq.com/tutorials/tutorial-one-go.html)

## 简介

RabbitMQ是一个消息代理服务器. 它的宗旨很简单: 接收和传递消息. 你可以将其想像为一家邮局: 当你到把信放到邮箱的时候, 你十分确信邮递员最终将信寄到接收者.用这个比喻将Rabbitmq当作邮箱和邮局以及邮递员.

RabbitMQ与邮局主要的区别实际上是: 它不处理纸件, 而是接收, 保存以及传递数据.

一般来说, RabbitMQ和消息传递使用如下约定俗成.

*producer* 生产者意思是消息发送者. 一个发送消息的程序称为*producer*.使用下图表示:

![producer](https://www.rabbitmq.com/img/tutorials/producer.png)

*queue* 队列是邮箱(mailbox)的名字.它存在于RabbitMQ内.尽管消息在你的应用与RabbitMQ里流动, 但他们只能被存储在队列中.一个队列没有被设置一些限制, 它能够存储任意你想要的消息, 基本上可以把它当作一个无限的buffer. 很多生产者将消息发送到一个队列, 很多消费者(*consumer*)试着从队列里接收数据.一个队列可以使用如下图表示:

![queue_name](https://www.rabbitmq.com/img/tutorials/queue.png)

*consumer* 一个消费者是一个程序主要是等着接收消息. 使用下图表示:

![consumer](https://www.rabbitmq.com/img/tutorials/consumer.png)

## "Hello World"

在这部分教程里, 我们将写两个小的Go程序; 一个生产者(producer)发送单一消息, 一个消费者(consumer)接收消息然后打印出来.我们将只关注这些, 并且会掩饰一些Go RabbimtMQ API的细节. 这是一个"Hello World"消息.

在下面的实例图, "P"是生产者, C是消费者. 中间的红色块表示队列--RabbitMQ为消费者(Consumer)保存的消息缓存(buffer).

![p-q-c](https://www.rabbitmq.com/img/tutorials/python-one.png)

> Go RabbitMQ client library
> 
> RabbitMQ支持多种协议.此教程使用AMQP 0-9-1,它是一份开放的,标准的消息协议.有多种语言的RabbitMQ客户端,我们使用Go amqp客户端.
> 首先, 用go get 安装: 
> ```$ go get github.com/streadway/amqp```

现在我们安装了amqp客户端, 开始撸些代码吧.

## 发送

![sending](https://www.rabbitmq.com/img/tutorials/sending.png)




###译者注
1. 


