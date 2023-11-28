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

## Majority Quorum
Por padrão, o ZK usa o quorum por maioria e isso quer dizer que nos protocolos que requerem "votação", precisa ter a maioria dos votos

O quorum sempre tem numeros impares de servidores: 1, 3, 5, 7 ... 2n + 1

Dessa maneira o ZK é tolerante nesses casos a uma queda de N servidores.
Exemplo: 9 servidores (n=4) logo pode cair 4 servidores que o ZK continua seu protocolo.

## ZK Shell
The role of bin/zookeeper-shell.sh is to connect Zookeeper and query the registered information through commands, which is essentially the syntax of Zookeeper.

For example you can check whether all the Kafka nodes are running properly.

## Quantidade sugerida

- 1 Zookeeper server is only good for development purposes because no single server can go down.
- 3 Zookeeper servers can be perfectly distributed, but only one server can go down for strict majority. This setup is appropriate for small to medium production environments.
- 5 Zookeeper servers can be perfectly distributed, and two servers can go down for strict majority. The setup needs good performance for all the communication between all Zookeeper servers, so this setup fits really big clusters.
