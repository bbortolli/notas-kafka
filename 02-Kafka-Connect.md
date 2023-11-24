# Kafka Connect (Connector Core API)

Sistema para integração de dados. Um cliente externo que roda fora dos Brokers
- Componente plugavel
- Interface para sistemas externos e para o Kafka
- Connector Source atuam como Producers
- Connector Sink atuam como Consumers

Componente do Apache Kafka® utilizado para realizar integração de streaming entre o Kafka e outros sistemas, como bancos de dados, serviços em nuvem, índices de pesquisa, sistemas de arquivos e armazenamentos de chave-valor.
O Kafka Connect facilita a transmissão de dados de várias fontes para o Kafka e a transmissão de dados do Kafka para vários destinos.

Existem literalmente centenas de conectores diferentes disponíveis para o Kafka Connect. Alguns dos mais populares incluem:
- RDBMS (Oracle, SQL Server, Db2, Postgres, MySQL)
- Cloud object stores (Amazon S3, Azure Blob Storage, Google Cloud Storage)
- Message queues (ActiveMQ, IBM MQ, RabbitMQ)
- NoSQL and document stores (Elasticsearch, MongoDB, Cassandra)
- Cloud data warehouses (Snowflake, Google BigQuery, Amazon Redshift)

# Como funciona

O Kafka Connect é executado em seu próprio processo, separado dos corretores do Kafka. Ele é distribuído, escalável e tolerante a falhas, proporcionando as mesmas características que você conhece e aprecia no Kafka em si.
Seu uso não requer programação. É totalmente baseado em configuração, tornando-o disponível para uma ampla gama de usuários, não apenas desenvolvedores. Além da ingestão e egresso de dados, o Kafka Connect também pode realizar transformações leves nos dados conforme eles passam.
Sempre que você deseja transmitir dados para o Kafka de outro sistema ou transmitir dados do Kafka para outro lugar, o Kafka Connect deve ser a primeira coisa que vem à mente.

# Connectors

Ao executar o Kafka Connect, instâncias de plugins do conector fornecem a integração entre sistemas externos de dados e o framework do Kafka Connect. Esses plugins do conector são componentes reutilizáveis que definem como os conectores de origem devem capturar dados das fontes de dados para um tópico do Kafka e também como os conectores de destino devem copiar dados dos tópicos do Kafka para serem reconhecidos por um sistema de destino. Ao cuidar de toda essa lógica padrão para você, os plugins permitem que você comece a usar o Kafka Connect rapidamente e concentre-se nos seus dados.

# Kafka Connect Workers
Em última análise, os workers do Kafka Connect são apenas processos JVM que você pode implantar em hardware físico ou contêineres.

O Terraform é uma opção em alguns provedores de nuvem.
E, é claro, há o Docker, que você pode utilizar tanto para instalações locais quanto baseadas em nuvem.

# Whats the structure of a partition?
R: A topic partition is an actual storage unit of Kafka messages which can be assumed as a Kafka message queue. The number of partitions per topic are configurable while creating it. Messages in a partition are segregated into multiple segments to ease finding a message by its offset.
  Each segment is composed of the following two indexes (files):
  - An offset to position index, that allows Kafka to find the starting position of a message.
  - A timestamp to offset index, that allows Kafka to find messages for a timestamp.

# REST API

- Informações básicas do cluster (versao worker, commit e kafka cluster id)
```shell
curl http://localhost:8083/
```

- Plugins instalados
```shell
curl -s localhost:8083/connector-plugins
```

- List Connector Instances
```shell
curl -s -X GET "http://localhost:8083/connectors/"
```

- Inspect Config and Status for a Connector
```shell
curl -i -X GET -H  "Content-Type:application/json" http://localhost:8083/connectors/sink-elastic-orders-00/config
```

- Delete a Connector
```shell
curl -s -X DELETE "http://localhost:8083/connectors/sink-elastic-orders-00"
```

# Metricas Confluent
Confluent Metrics API
The Confluent Cloud Metrics API provides actionable operational metrics about your Confluent Cloud deployment. This is a queryable HTTP API in which the user will POST a query written in JSON and get back a time series of metrics specified by the query.

Metrics API endpoints are available to:
- List metric descriptors
- List resource descriptors
- Query metric values
- Export metric values
- Query label values
