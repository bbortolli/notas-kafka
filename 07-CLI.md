# Command Line Interface

## Kafka Topics

```shell
# Listar topicos
bin/kafka-topics.sh --bootstrap-server localhost:9092 --list

# Criar
bin/kafka-topics.sh --create --topic nome-topico --bootstrap-server localhost:9092
```

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
