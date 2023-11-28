# Configurações

## Tópicos
- auto.create.topics.enable: em alguns comandos o tópico é criado caso nao exista e essa flag for true
- replication.factor
- min.insync.replicas
- retention.ms

## Encriptação
- Plaintext (DEFAULT)
- SSL (Ao settar não tem como usar o Zero Copy Data Transfer)

## Broker
- log.dir
- broker.id (deve ser único)
- port

## Consumer
- enable.auto.commit
- fetch.min.bytes (aumenta throughput em troca de latencia)
- fetch.siza (CFG MAIS IMPORTANTE DO CONSUMER !)
- auto.offset.reset:
  - earliest: automatically reset the offset to the earliest offset
  - latest: automatically reset the offset to the latest offset
  - none: throw exception to the consumer if no previous offset is found for the consumer's group

### Exececao do Consumer
NotLeaderForPartitionException: client envia metadata para mesmo broker para o topico e pega o lider

### Métricas
- bytes-consumed-rate

## Producer
- max.in.flight.requests.per.connection: se permitir mais que 1 , o batch poderá haver falha em uma mensagem anterior.
Exemplo de 3: msg A falha, B ok e C ok. No retry de A vai aparece após B e C

- enable.idempotence: The idempotent producer feature addresses these issues ensuring that messages always get delivered, in the right order and without duplicates.

- compression.type: Producers usually send data that is text-based, for example with JSON data. In this case, it is important to apply compression to optimize for throughput. Compression is more effective the bigger the batch of messages being sent to Kafka. Possible values are: lz4 (recommended for performance),snappy, zstd, gzip.

- batch.size: With batching strategy of Kafka producers, you can batch messages going to the same partition, which means they collect multiple messages to send together in a single request. The most important step you can take to optimize throughput is to tune the producer batching to increase the batch size and the time spent waiting for the batch to populate with messages. Larger batch sizes result in fewer requests, which reduces load on producers and the broker CPU overhead to process each request.

- linger.ms: It's the number of milliseconds a producer is willing to wait before sending a batch out (defaults to 0). By introducing some lag, we increase the changes of messages being sent together in a batch. If the batch is full (batch.size) before the end of linger.ms period, it will be sent to Kafka right away.
- batch.size
- acks
  a. 0, nao espera ninguem
  b. 1, espera OK do lider
  c. all, espera lider e replicas

### Excecoes do Producer

1: Non-Retriable exceptions (fatal, the message will never be sent):

- InvalidTopicException
- OffsetMetadataTooLargeException
- RecordBatchTooLargeException
- RecordTooLargeException
- UnknownServerException

2: Retriable exceptions (transient, may be covered by increasing the number of retries):
- CorruptRecordException
- InvalidMetadataException
- NotEnoughReplicasAfterAppendException
- NotEnoughReplicasException
- OffsetOutOfRangeException
- TimeoutException
- UnknownTopicOrPartitionException

### Métricas

- batch-size-max: The max number of bytes sent per partition per-request.
- compression-rate-avg: The average compression rate of record batches, defined as the average ratio of the compressed batch size over the uncompressed size.
- request-latency-avg: The average request latency in ms.

## ksqlDL
- delete.topic.enable : quando true, os topicos internos sao limpos

## Zookeeper
The minimum configuration keywords that must be defined in the configuration file are:

- clientPort: The port to listen for client connections; that is, the port that clients attempt to connect to.
- dataDir: The location where ZooKeeper will store the in-memory database snapshots and, unless specified otherwise, the transaction log of updates to the database.
- tickTime: The length of a single tick, which is the basic time unit used by ZooKeeper, as measured in milliseconds. It is used to regulate heartbeats, and timeouts.

# Outras

- unclean.leader.election.enable:
  - true: Disponibilidade por durabilidade (data-loss)
  - false: durabilidade/data-loss é mais importante do que disponibilidade
