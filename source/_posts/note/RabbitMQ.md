---
title: RabbitMQ
excerpt: note
layout: note
tags:
  - code
categories:
  - - note
index_img: /img/code.webp
date: 2025-08-01 22:22:16
---
# RabbitMQ可靠性
RabbitMQ 通过多个环节的机制组合来保障消息的可靠性，下面将从消息的生命周期详细解释每个环节的保障机制。

## 一、生产者到Broker的可靠性保障
### 1. 事务机制（不推荐，性能差）
``` java
try {
    channel.txSelect(); // 开启事务
    channel.basicPublish(exchange, routingKey, MessageProperties.PERSISTENT_TEXT_PLAIN, message.getBytes());
    channel.txCommit(); // 提交事务
} catch (Exception e) {
    channel.txRollback(); // 回滚事务
    // 重发消息
}
```
- 缺点：吞吐量下降约250倍

### 2. 确认机制（推荐方案）
``` java
// 开启确认模式
channel.confirmSelect();

// 异步确认监听
channel.addConfirmListener(new ConfirmListener() {
    @Override
    public void handleAck(long deliveryTag, boolean multiple) {
        // 消息已确认
    }
    
    @Override
    public void handleNack(long deliveryTag, boolean multiple) {
        // 消息未确认，需要重发
    }
});

// 发送消息
channel.basicPublish(exchange, routingKey, MessageProperties.PERSISTENT_TEXT_PLAIN, message.getBytes());
```
#### 实现原理：

1. Broker接收消息后写入磁盘（如果是持久化消息）

2. Broker完成写入后发送Confirm确认给生产者

3. 生产者收到确认后从重发队列移除该消息

## 二、Broker内部可靠性保障
### 1. 持久化机制
```java
// 持久化交换机
channel.exchangeDeclare("exchange", "direct", true);

// 持久化队列
channel.queueDeclare("queue", true, false, false, null);

// 持久化消息
channel.basicPublish("exchange", "routingKey", 
    MessageProperties.PERSISTENT_TEXT_PLAIN, // deliveryMode=2
    message.getBytes());
```
#### 存储机制：

1. 消息先写入内存，然后根据策略刷盘

2. 持久化消息会写入磁盘文件.rdq

3. 非持久化消息只保存在内存

### 2. 镜像队列（高可用）
```bash
# 设置镜像策略
rabbitmqctl set_policy ha-all "^ha." '{"ha-mode":"all"}'
```
#### 工作原理：

1. 每个镜像队列包含一个master和多个mirror

2. 所有操作先在master完成，然后同步到mirror

3. 如果master宕机，最老的mirror会成为新的master

## 三、Broker到消费者的可靠性保障
### 1. 手动ACK机制
``` java
channel.basicConsume(queueName, false, (consumerTag, delivery) -> {
    try {
        // 处理消息
        processMessage(delivery.getBody());
        
        // 手动确认
        channel.basicAck(delivery.getEnvelope().getDeliveryTag(), false);
    } catch (Exception e) {
        // 处理失败，拒绝消息（可设置是否重新入队）
        channel.basicNack(delivery.getEnvelope().getDeliveryTag(), false, true);
    }
});
```
#### 消息状态变化：

1. 消息被投递给消费者后处于unacked状态

2. 收到ACK后从队列删除

3. 收到NACK或连接断开时重新变为ready状态

### 2. 消费限流（QoS）
```java
// 每次最多预取10条消息
channel.basicQos(10);
```
- 防止消费者过载

- 避免消息堆积在消费者端

## 四、异常情况处理
### 1. 死信队列（DLX）
```java
Map<String, Object> args = new HashMap<>();
args.put("x-dead-letter-exchange", "dlx.exchange");
args.put("x-dead-letter-routing-key", "dlx.routingKey");
channel.queueDeclare("normal.queue", true, false, false, args);
```
#### 触发条件：

消息被拒绝且requeue=false

消息TTL过期

队列达到最大长度

### 2. 备份交换机（AE）
```java
Map<String, Object> args = new HashMap<>();
args.put("alternate-exchange", "unrouted.exchange");
channel.exchangeDeclare("main.exchange", "direct", false, false, args);
```
## 五、最佳实践组合方案
1. 生产者端：

- 开启Confirm机制

- 实现Confirm监听接口进行重试

- 消息设置持久化标志

2. Broker端：

- 交换机、队列设置为持久化

- 配置镜像队列策略

- 设置合理的磁盘报警阈值

3. 消费者端：

- 关闭autoAck，使用手动确认

- 实现消费幂等性处理

- 设置合理的QoS预取值

4. 监控措施：

- 监控消息积压情况

- 设置消息TTL防止无限堆积

- 实现报警机制

## 六、可靠性测试方法
1. 生产者测试：

- 断网测试：验证消息重发机制

- Broker宕机测试：验证Confirm机制

2. Broker测试：

- 节点宕机测试：验证镜像队列切换

- 磁盘满测试：验证持久化机制

3. 消费者测试：

- 消费过程中断测试：验证消息重新入队

- 消费失败测试：验证NACK机制

通过这些机制的组合使用，可以构建从生产到消费的完整可靠性保障体系，确保消息在各个环节都不会丢失。