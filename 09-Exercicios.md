# Exercicios

### Which of the following is not an Avro primitive type?
- A. date [X]
- B. long
- C. string
- D. null
- E. int

R: date é um tipo lógico para Avro

### What happens if you write the following code in your Producer?
producer.send(producerRecord).get()
- A. Increase compression
- B. Decrease throughput [X]
- C. Force all broker in Kafka to ack the producerRecord
- D. Increase batching

R: Usar Future.get() força a espera de resposta do Kafka e dessa forma limita o throughput

### We have a store selling shoes. What dataset is great candidate to be modeled as KTable in KafkaStreams?
- A. Money made until now [X]
- B. Inventory contents right now
- C. The transaction stream
- D. Items returned [X]

R: A e D pois são agregações e estas são persistidas em tabelas. Já os streams de dados devem ser modelados como KStream a fim de evitar exposição de dados.

### Where are KSQL-related data and metadata stored?
- A. Kafka Topics [X]
- B. PostgreSQL
- C. Schema Registry
- D. Zookeeper

R: são persistido e feito sobre o KSQL command topic. Cada KSQL-Server tem uma versão da metastore na sua prórpia memória

### To prevent network-induced duplicates when producing to kafka,i should use:
- A. retries=20000
- B. enable.idempotence=true [X]
- C. max.in.flight.requests.per.connection=1
- D. batch.size=1

R: A configuração que lida com duplicidade é idempotencia, logo é necessária ativar essa config.

### Which of the following Streams operators are stateful?
- A. flatmap
- B. aggregate [X]
- C. count [X]
- D. peek
- E. reduce [X]
- F. joining [X]

R: Por definição

### In Avro, removing or adding a field that has a default is a _ schema evolution.
- A. breaking
- B. forward
- C. full [X]
- D. backward

R: Forward e Backward podem remover/adicionar campos com caracteristicas diferentes. Nessa situação está compativel com remover e adicionar um campo de mesma característica. Logo é full

### When auto.create.topics.enable is true, which circunstances it automatically create a new topic?
- A. Client request metadata for a topic [X]
- B. Consumer reads messagem from a topic [X]
- C. Client alters number of partitions
- D. Producer send message to a topic [X]

R: Por definição, são nessas 3 circustâncias onde essa flag cria o tópico.

### Which actions trigger partition rebalance for a consumer group?
- A. Increse partitions of a topic [X]
- B. Add new consumer to the group [X]
- C. Remove broker from the cluster
- D. Consumer in the group shutdown [X]

R: Por definição, são nessas 3 circustâncias onde ocorre o rebalanceamento.

### When enable.auto.commit = false, what happens when close() is called on the consumer object?
- A. uncommited offsets are commited
- B. group coord. will discover that the consumer stopped sending heartbeats. it will rebalance after session.timeout.ms
- C. a rebalance in the consumer group will happen automatically [X]

R: Chamar o close() causa rebalanceamento imediatamente pois o consumer não estará mais disponível

### in Java, Avro SpecificRecords classes are:
- A. auto generated from Avro Schema
- B. auto generated from Avro Schema + Maven/Gradle plugin [X]
- C. written manual by programmer

R: Criado a partir das classes records geradas

### Same key goes to same partition, unless:
- A. number of brokers change
- B. number o producers change
- C. number of partitions change [X]
- D. replication factor change

R: Criado a partir das classes records geradas

### Send a specific key to a single partition and any other key to the rest:
- A. add metadata to producer record
- B. define broker routing rule
- C. not possible
- D. create custom partitioner [X]

R: criar um partitioner próprio com a lógica necessária

### Which offset commit strategy should be used in an at-most once consuming scenario?
R: Fazer commit antes de processar
At-most once significa que será processado exatamente uma vez ou não.

### What kind of Kafka Streams window has fixed-size, is non-overlapping and gap-less?
R: Tumbling

### Running Kafka in production, what's a proper value for setting the java heap size?
R: 6GB

### Using Kafka Connect Source to read data from an external database, which processes do the actual work and execute connectors and tasks?
R: Workers

### What's the main benefit for adding a callback when sending a record?
R: Lidar de maneira assincrona com resposta/erros

### By default, what's the encryption mechanism used by Apache Kafka?
R: PLAINTEXT

### What option best describes a Kafka Broker?
R: Um processo JVM que está rodando em uma máquina e servindo de host para tópicos. Cada broker é identificado com seu id unico, inteiro e atua como um intermediador entre os produtores e consumidores. O Broker recebe mensagens dos producers e permite que os consumers façam fetch das mensagens por tópicos, partição e offset.

R: A Kafka Broker is nothing more, nothing less, than a JVM process that runs on a machine and hosts topics. Each broker is identified with its unique ID (integer) and acts as the middleman between producers and consumers. The broker receives messages from producers and allows consumers to fetch messages by topic, partition and offset.

### What admin api do?
R: Admin API serve para gerenciar e inspecionar tópicos, brokers, ACLs e outrs objetos Kafka.

### Where are the ACLs stored in a Kafka cluster by default?
R: Inside the broker's data directory

### is KSQL ANSI SQL compliant?
R: Nao
(KSQL is not ANSI SQL compliant, for now there are no defined standards on streaming SQL languages)

### What information isn't stored inside of Zookeeper? (select two)
- A. Schema Registry schemas [X]
- B. Consumer offset [X]
- C. ACL inforomation
- D. Controller registration
- E. Broker registration info

R: A fica no tópico __schemas e B fica no tópico __consumer_offsets

### There are two consumers C1 and C2 belonging to the same group G subscribed to topics T1 and T2. Each of the topics has 3 partitions. How will the partitions be assigned to consumers with Partition Assigner being Round Robin Assigner?

- A. C1 will be assigned partitions 0 and 2 from T1 and partition 1 from T2. C2 will have partition 1 from T1 and partitions 0 and 2 from T2. [X]
- B. Two consumers cannot read from two topics at the same time
- C. C1 will be assigned partitions 0 and 1 from T1 and T2, C2 will be assigned partition 2 from T1 and T2.
- D. All consumers will read from all partitions

R: A. 2 topicos, 3 particoes = 6 partições. Cada consumidor fica com 3. Isso já é o suficiente para responder.
Mas também no Round Robin a ordem das particoes fica na ordem:
T1.0 -> C1 atribuido
T1.1 -> C2 atribuido
T1.2 -> C1 atribuido
T2.0 -> C2 atribuido
T2.1 -> C1 atribuido
T2.2 -> C2 atribuido

### With 3 brokers, a kafka topic has a replication factor of 3 and min.insync.replicas setting of 2. How many brokers can go down before a producer with acks=all can't produce?

- A. 0
- B. 1 [X]
- C. 2
- D. 3

R: acks=all e min.insync.replicas=2 significa que precisa de pelo menos 2 broker precisam estar de pé. Como no cenário são 3, logo pode cair apenas 1.

### The rule "same key goes to the same partition" is true unless...

1. Number of producer changes
2. Number of partition changes [X]
3. Number of replication factor changes

R: Se mudar o número de partições, os hashs das mensagens não garantem mais que vão ao mesmo lugar de antes.

### You have a Kafka cluster and all the topics have a replication factor of 3. One intern at your company stopped a broker, and accidentally deleted all the data of that broker on the disk. What will happen if the broker is restarted?

- A. The broker will start, and other topics will also be deleted as the broker data on the disk got deleted
- B. The broker will start, and won't be online until all the data it needs to have is replicated from other leaders [X]
- C. The broker will crash
- D. The broker will start, and won't have any data. If the broker comes leader, we have a data loss

R: B; Kafka replication mechanism makes it resilient to the scenarios where the broker lose data on disk, but can recover from replicating from other brokers.

### When using the Confluent Kafka Distribution, where does the schema registry reside?

- A. As a separate JVM component [X]
- B. As an in-memory plugin on your Zookeeper cluster
- C. As an in-memory plugin on your Kafka Brokers
- D. As an in-memory plugin on your Kafka Connect Workers

R: Schema registry is a separate application that provides RESTful interface for storing and retrieving Avro schemas.

### By default, which replica will be elected as a partition leader? (select two)

Options:
- A. Preferred leader broker if it is in-sync and auto.leader.rebalance.enable=true
- B. Any of the replicas [X]
- C. Preferred leader broker if it is in-sync and auto.leader.rebalance.enable=false
- D. An in-sync replica [X]

R: B, D. Preferred leader is a broker that was leader when topic was created. It is preferred because when partitions are first created, the leaders are balanced between brokers. Otherwise, any of the in-sync replicas (ISR) will be elected leader, as long as unclean.leader.election=false (by default)

### A topic "sales" is being produced to in the Americas region. You are mirroring this topic using Mirror Maker to the European region. From there, you are only reading the topic for analytics purposes. What kind of mirroring is this?

Options:
- A. Passive-Passive
- B. Active-Active
- C. Active-Passive

R: C. This is active-passing as the replicated topic is used for read-only purposes only

### Partition leader election is done by

- A. The consumers
- B. The Kafka Broker that is the Controller
- C. Zookeeper [X]
- D. Vote amongst the brokers

R: C; o Zookeper é o responsável por eleger

### Your topic is log compacted and you are sending a message with the key K and value null. What will happen?

Options:
- A. The broker will delete all messages with the key K upon cleanup [X]
- B. The producer will throw a Runtime exception
- C. The broker will delete the message with the key K and null value only upon cleanup
- D. The message will get ignored by the Kafka broker

R: A

### Which Kafka CLI should you use to consume from a topic?

Options:
- A. kafka-console-consumer [X]
- B. kafka-topics
- C. kafka-console
- D. kafka-consumer-groups

R: A; Exemplo: kafka-console-consumer --bootstrap-server 127.0.0.1:9092 --topic test --from-beginning

### You have a Zookeeper cluster that needs to be able to withstand the loss of 2 servers and still be able to function. What size should your Zookeeper cluster have?

Options:
- A. 4
- B. 5
- C. 2
- D. 3
- E. 6

R: B; Your Zookeeper cluster needs to have an odd number of servers, and must maintain a majority of servers up to be able to vote. Therefore, a 2N+1 zookeeper cluster can survive to N zookeeper being down, so here the right answer is N=2, 2*N+1=5

### Select all the way for one consumer to subscribe simultaneously to the following topics - topic.history, topic.sports, topic.politics? (select two)

Options:
- A. consumer.subscribe(Pattern.compile("topic\..*"));
- B. consumer.subscribe("topic.history"); consumer.subscribe("topic.sports"); consumer.subscribe("topic.politics");
- C. consumer.subscribePrefix("topic.");
- D. consumer.subscribe(Arrays.asList("topic.history", "topic.sports", "topic.politics"));

R: A e D

### A Zookeeper ensemble contains 5 servers. What is the maximum number of servers that can go missing and the ensemble still run?

Options:
- A. 3
- B. 4
- C. 2 [X]
- D. 1

R: C; majority consists of 3 zk nodes for 5 nodes zk cluster, so 2 can fail

### When using plain JSON data with Connect, you see the following error messageorg.apache.kafka.connect.errors.DataExceptionJsonDeserializer with schemas.enable requires "schema" and "payload" fields and may not contain additional fields. How will you fix the error?

Options:
- A. Set key.converter, value.converter to JsonConverter and the schema registry url
- B. Use Single Message Transforms to add schema and payload fields in the message
- C. Set key.converter.schemas.enable and value.converter.schemas.enable to false [X]
- D. Set key.converter, value.converter to AvroConverter and the schema registry url

R: C; You will need to set the schemas.enable parameters for the converter to false for plain text with no schema.

### Which of the following setting increases the chance of batching for a Kafka Producer?

Options:
- A. Increase batch.size
- B. Increase message.max.bytes
- C. Increase the number of producer threads
- D. Increase linger.ms [X]

R: D; linger.ms forces the producer to wait to send messages, hence increasing the chance of creating batches

### You are doing complex calculations using a machine learning framework on records fetched from a Kafka topic. It takes more about 6 minutes to process a record batch, and the consumer enters rebalances even though it's still running. How can you improve this scenario?

Options:
- A. Increase max.poll.interval.ms to 600000  [X]
- B. Increase heartbeat.interval.ms to 600000
- C. Increase session.timeout.ms to 600000
- D. Add consumers to the consumer group and kill them right away

R: A; Here, we need to change the setting max.poll.interval.ms (default 300000) to its double in order to tell Kafka a consumer should be considered dead if the consumer only if it hasn't called the .poll() method in 10 minutes instead of 5.

### What happens when broker.rack configuration is provided in broker configuration in Kafka cluster?

- A. You can use the same broker.id as long as they have different broker.rack configuration
- B. Replicas for a partition are placed in the same rack
- C. Replicas for a partition are spread across different racks [X]
- D. Each rack contains all the topics and partitions, effectively making Kafka highly available

R: C; Partitions for newly created topics are assigned in a rack alternating manner, this is the only change broker.rack does

# Dumps

https://www.dumpsmate.com/CCDAK-exam.html

https://www.study4exam.com/confluent/free-ccdak-questions

https://www.marks4sure.com/ccdak-confluent-certified-developer-for-apache-kafka-certification-examination-questions.html

https://www.certshero.com/confluent/ccdak/practice-test
