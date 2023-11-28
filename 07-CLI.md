# Command Line Interface

## Kafka Topics

```shell
# Listar topicos
bin/kafka-topics.sh --bootstrap-server localhost:9092 --list

# Criar
bin/kafka-topics.sh --create --topic nome-topico --bootstrap-server localhost:9092
```

--topics-with-overrides: If set when describing topics, only show topics that have overridden configs.

--unavailable-partitions: If set when describing topics, only show partitions whose leader is not available.

--under-min-isr-partitions: If set when describing topics, only show partitions whose ISR count is less than the configured minimum.

--under-replicated-partitions: If set when describing topics, only show under replicated partitions.

## Acess Control List (ACL)
```shell
bin/kafka-acls.sh --authorizer-properties zookeeper.connect=localhost:2181 --add --allow-principal User:Bob --allow-host test-host --operation read --operation write --topic test-topic
```
## Zookeeper

Rodar o zookeeper
```shell
bin/zookeeper-server-start.sh config/zookeeper.properties
```

```shell

```


```shell

```


```shell

```
