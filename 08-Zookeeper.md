# Zookeeper


Zookeeper is a top-level software developed by Apache that acts as a centralized service and is used to maintain naming and configuration data and to provide flexible and robust synchronization within distributed systems. Zookeeper keeps track of status of the Kafka cluster nodes and it also keeps track of Kafka topics, partitions etc.

Responsável por eleger o broker coordinator

Zookeeper's internal data structure is like a tree:

- each node is called a zNode

- each zNode has a path

- zNodes can be persistent or ephemeral

- each zNode can store data

- you cannot rename a zNode

- each zNode can be watched for changes

## zNodes

- Podem guardar dados
- Podem ser persistentes ou temporário
- Cada tem um path
- NÃO pode ser renomeado via CLI

## Acess Control List (ACL)
Provem controles de autorização para o cluster Kafka e é usado pelo zookeper
