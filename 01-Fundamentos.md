# Fundamentos

## O que é o Apache Kafka?
O Apache Kafka é uma plataforma de streaming de eventos usada para coletar, processar, armazenar e integrar dados em grande escala.

## O que são Eventos?
Um evento é qualquer tipo de ação, incidente ou mudança identificada ou registrada por um programa.

## Tópicos
A unidade mais fundamental de organização no Apache Kafka é o tópico, que é algo semelhante a uma tabela em um banco de dados relacional. Você cria diferentes tópicos para armazenar diferentes tipos de eventos e tópicos diferentes para armazenar versões filtradas e transformadas do mesmo tipo de evento.
Um tópico é um registro de eventos (commit log) e são fáceis de entender, pois são estruturas de dados simples com semântica bem conhecida.

1. são append only: ao escrever uma nova mensagem em um registro, ela sempre vai para o final.
2. só pode ser lido procurando um offset e em seguida examinando entradas sequenciais no log
3. os eventos são imutáveis; uma vez que algo aconteceu, é extremamente difícil desfazê-lo.

Os logs também são fundamentalmente duráveis. É possível configurar o quão durável serão os logs.

# Particionamento
O particionamento divide o os tópicos em varias partes, cada uma dos quais pode existir em um nó separado no cluster Kafka. Dessa forma, o trabalho de armazenar mensagens, escrever novas mensagens e processar mensagens existentes pode ser dividido entre muitos nós no cluster.

Depois de dividir um tópico em partições, precisamos de uma maneira de decidir quais mensagens escrever em quais partições. Normalmente, se uma mensagem não tiver uma chave, as mensagens subsequentes serão distribuídas de forma round-robin (circular) entre todas as partições do tópico. Nesse caso, todas as partições recebem uma parcela igual dos dados, mas não existirá ordenação das mensagens. Se a mensagem tiver uma chave, a partição de destino será calculada a partir de um hash da chave e assim permite que o Kafka garanta que as mensagens com a mesma chave sempre caiam na mesma partição e, portanto, estejam sempre em ordem.

# Kafka Brokers
É uma máquina rodando o processo do Kafka. Não necessáriamente máquinas físicas separadas, podendo ser virtuais também. São independentes entre si e cada um desses gerencia um conjunto de partições. Os brokers são responsáveis para lidar com as requisições de escrita e leitura, e também lidam com a replicação das partições entre si.

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
2. Chamar o método .send()
3. Serializer
4. Partitioner
5. 1. Em caso de sucesso retorna um Future de metadados
   2. Em caso de falha, faz retry ou lança exception

# Kafka Consumers (Consumer Core Api)
Responsável por ler as mensagens de uma ou mais partições do(s) tópico(s) no qual ele faz subscribe.

# Consumer Group
Conjunto de consumers no qual consumirão de um mesmo tópico e dessa maneira é possível escalar a leitura desse tópico até no máximo um fator de 1 pra 1, ou seja, 1 partição para cada consumidor. Consumers a mais que o número de partições ficaram ociosos.
