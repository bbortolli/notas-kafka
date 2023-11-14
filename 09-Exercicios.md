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
