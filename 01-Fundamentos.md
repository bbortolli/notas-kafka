# Fundamentos

## O que é o Apache Kafka?
O Apache Kafka é uma plataforma de streaming de eventos usada para coletar, processar, armazenar e integrar dados em grande escala.

## Linguagens
Kafka é feito em Java e Scala

## O que são Eventos?
Um evento é qualquer tipo de ação, incidente ou mudança identificada ou registrada por um programa.

## Tópicos
A unidade mais fundamental de organização no Apache Kafka é o tópico, que é algo semelhante a uma tabela em um banco de dados relacional. Você cria diferentes tópicos para armazenar diferentes tipos de eventos e tópicos diferentes para armazenar versões filtradas e transformadas do mesmo tipo de evento.
Um tópico é um registro de eventos (commit log) e são fáceis de entender, pois são estruturas de dados simples com semântica bem conhecida.

1. são append only: ao escrever uma nova mensagem em um registro, ela sempre vai para o final.
2. só pode ser lido procurando um offset e em seguida examinando entradas sequenciais no log
3. os eventos são imutáveis; uma vez que algo aconteceu, é extremamente difícil desfazê-lo.

Os logs também são fundamentalmente duráveis. É possível configurar o quão durável serão os logs.

## Comunicação point-to-point

Nesse tipo de comunicação que é DIFERENTE DO KAFKA existem 3 partes:
1. Sender (produz a mensagem)
2. Receiver (consome a mensagem)
3. Queue (fila onde se escreve a mensagem)

A mensagem só pode ser consumida uma unica vez

## Comunicação Kafka
Via TCP

# Particionamento
O particionamento divide o os tópicos em varias partes, cada uma dos quais pode existir em um nó separado no cluster Kafka. Dessa forma, o trabalho de armazenar mensagens, escrever novas mensagens e processar mensagens existentes pode ser dividido entre muitos nós no cluster.

Depois de dividir um tópico em partições, precisamos de uma maneira de decidir quais mensagens escrever em quais partições. Normalmente, se uma mensagem não tiver uma chave, as mensagens subsequentes serão distribuídas de forma round-robin (circular) entre todas as partições do tópico. Nesse caso, todas as partições recebem uma parcela igual dos dados, mas não existirá ordenação das mensagens. Se a mensagem tiver uma chave, a partição de destino será calculada a partir de um hash da chave e assim permite que o Kafka garanta que as mensagens com a mesma chave sempre caiam na mesma partição e, portanto, estejam sempre em ordem.

Partições são estruturadas de segmento que contém 2 índices: timestamp e offset

# Kafka Brokers
É uma máquina rodando o processo do Kafka. Não necessáriamente máquinas físicas separadas, podendo ser virtuais também. São independentes entre si e cada um desses gerencia um conjunto de partições. Os brokers são responsáveis para lidar com as requisições de escrita e leitura, e também lidam com a replicação das partições entre si.
Cada broker deve conter um único id e um cenário multi-broker.

# Replication
O dado original é escrito e lida no broker leader e também nele persiste o dado no caso da escrita. Após isso são feitas cópias desses dados para os followers e com isso é possível obter um certo nível de segurança dos dados e de tolerância a falhas.
Se um nó/broker do cluster cair, um outro tomará sua posição.

# Serializer
Responsável por transformar a mensagem da aplicação para o Kafka.
Para o kafka tudo são bytes

# Deserializer
Responsável por transformar a mensagem do Kafka para a aplicação

# Partitioner
Classe responsável por definir em qual partição a mensagem com uma dada chave será escrita.

# Kafka Producers (Producer Core Api)
Responsável por escrever mensagens no Kafka.
Para enviar uma mensagem o seguinte fluxo acontece:
1. Criar um objeto ProducerRecord
   - construtores (tv, tkv)
2. Chamar o método .send()
   - existe a opcao .get() para sync
3. Serializer
4. Partitioner
5. 1. Em caso de sucesso retorna um Future de metadados
   2. Em caso de falha, faz retry ou lança exception

*OBS*
1. A requisição do envio da mensagem é feita para o Lider da partição
2. Não precisa de coordenação de grupo. é thread safe


# Kafka Consumers (Consumer Core Api)
Responsável por ler as mensagens de uma ou mais partições do(s) tópico(s) no qual ele faz subscribe.

*OBS*: é necessário coordenação, não é thread safe

# Maneiras de subscribe em tópicos
```java
// Pattern
consumer.subscribe(Pattern.compile("*.topic"));
// Array
consumer.subscribe(Array.asList("topic-a", "topic-b"));
```

# Consumer Group
Conjunto de consumers no qual consumirão de um mesmo tópico e dessa maneira é possível escalar a leitura desse tópico até no máximo um fator de 1 pra 1, ou seja, 1 partição para cada consumidor. Consumers a mais que o número de partições ficaram ociosos.

## Group Coordinator
é um dos brokers do cluster

## Leader do Grupo de Consumo
O primeiro a se conectar no grupo

## Partition Leader
Feito pelo Broker que é Controller

## Rebalanceamento do grupo
Feito pelo coordinator do grupo
Dois casos diferentes causa um rebalanceamento:
1. Quando um consumidor sai do grupo (ele notifica o Coordinator)
2. Um membro entra no grupo

Quando isso acontece, as partições são reatribuidas aos membros do grupo de forma proporcional entre cada um deles

## Estratégias de atribuição de partição
- RangeAssignor (DEFAULT):
Nesse modo cada consumidor recebe um segmento igual de cada topico.
Exemplo: C1 recebe partições 0 e 1 do T1 e do T2. C2 recebe partições 2 do T1 e do T2

- RoundRobinAssignor
Uma partição de cada vez é atribuida a um consumidor. Um tópico de cada vez

## Estratégias de commit
- at-least once: commit no final/após processar
- at-most once: commit no começo/antes de processar

# Admin API
Usada para fazer o gerenciamento e inspeção de tópicos, brokers, ACLs e outros objetos do Kafka.

# MirrorMaker
Kafka administrators can replicate topics, topic configurations, consumer groups, and ACLs from one or more source Kafka clusters to one or more target Kafka clusters, i. e., across cluster environments.

Multi-datacenter deployments enable use-cases such as:

- Active -> Active: geo-localized deployments: allows users to access a near-by data center to optimize their architecture for low latency and high performance. Replication can be done in both sides and data can be produced and consumed in every cluster.

- Active-> Passive: disaster recover (DR) deployments: in an event of a partial or complete datacenter disaster, allow failing over applications to use Confluent Platform in a different datacenter.

- Active -> Passive: centralized analytics: Aggregate data from multiple Apache Kafka clusters into one location for organization-wide analytics. No producers will be used at the replicated cluster, it's just for consuming data for analytic purposes.

- Aggregation (e. g., from many clusters to one): A->K, B->K, C->K.

- Fan-out (e. g., from one to many clusters): K->A, K->B, K->C.

- Forwarding: A->B, B->C, C->D.
