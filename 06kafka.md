# kafka

### 常用命令

创建topic

```shell
kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic spark
```

生产者

```shell
./kafka-console-producer.sh --broker-list 127.0.0.1:9092 --topic test
```

消费者

```shell
./kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --topic test
```