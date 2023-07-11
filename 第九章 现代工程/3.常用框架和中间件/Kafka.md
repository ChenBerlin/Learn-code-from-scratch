# Kafka

Kafa是有Apache支持的一个开源流处理平台，由Scala和Java编写。当前互联网业务中常被用做实现消息队列。

## 消息队列

一般的消息队列采用`生产者-消费者模式`，双方围绕一个队列进行操作，生产者往队列中存放消息，消费者从队列中取出消息。互联网业务下，一条消息可能被多个消费者使用，同时业务可能只需要消费队列中特定的消息，此时这种传统的单一队列就不再满足需求了。

## Kafka的架构

### 发布-订阅模式

Kafka采用`发布-订阅模式`，将`生产者-消费者模式`中间的交互对象由队列改为了不同的`topic`。生产者（Producer）往`topic`中发布消息，消费者（Consumer）订阅`topic`。`topic`使用`单播`或者`广播`的方式，向订阅了它的消费者发送消息。

### Topic和Partition

`Partition`是Kafka下数据存储的基本单元。同一个`topic`的数据，会被分散的存储到多个`partition`中，这些partition可以在同一台机器上，也可以是在多台机器上。

### Consumer Group

同一个`topic`的数据，会广播给不同的`consumer group`；同一个`consumer group`中只有一个`worker`能拿到这个数据。

