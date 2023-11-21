# KafkaStreams (Core API)

Usando a API Streams, as seguintes operações de joins são suportadas e dependendo dos operandos, podem ser windowed or non-windowed:

- KStream-to-KStream (windowed): Inner join, left join and outer join supported.
- KTable-to-KTable (non-windowed): Inner join, left join and outer join supported.
- KStream-to-KTable (non-windowed): Inner join and left join supported. Outer join not supported.
- KStream-to-GlobalKTable (non-windowed): Inner join and left join supported. Outer join not supported.

## KStream
Um KStream é uma abstração de um fluxo de registros, onde cada registro de dados representa um dado autocontido no conjunto de dados ilimitado. Usando a analogia da tabela, os registros de dados em um fluxo de registros são sempre interpretados como um "INSERT" - pense em adicionar mais entradas a um append-only - porque nenhum registro substitui uma linha existente com a mesma chave. Exemplos incluem uma transação com cartão de crédito, um evento de visualização de página ou uma entrada de log do servidor.

Para ilustrar, vamos imaginar que os dois registros de dados a seguir estão sendo enviados para o stream:


("alice", 1) --> ("alice", 3)

Se o seu aplicativo de processamento de fluxo somasse os valores por usuário, retornaria 4 para Alice. Por quê? Porque o segundo registro de dados não seria considerado uma atualização do registro anterior. Compare esse comportamento do KStream com o KTable abaixo, que retornaria 3 para Alice.

## KTable

Um KTable é uma abstração de um fluxo de log de alterações, onde cada registro de dados representa uma atualização. Mais precisamente, o valor em um registro de dados é interpretado como uma "ATUALIZAÇÃO" do último valor para a mesma chave de registro, se houver (se uma chave correspondente ainda não existir, a atualização será considerada um "INSERT"). Usando a analogia da tabela, um registro de dados em um fluxo de log de alterações é interpretado como um UPSERT, também conhecido como INSERT/UPDATE, porque qualquer linha existente com a mesma chave é sobrescrita. Além disso, os valores nulos são interpretados de maneira especial: um registro com um valor nulo representa um "DELETE" ou uma lápide para a chave do registro.

Para ilustrar, vamos imaginar que os dois registros de dados a seguir estão sendo enviados para o fluxo:


("alice", 1) --> ("alice", 3)

Se o seu aplicativo de processamento de fluxo somasse os valores por usuário, retornaria 3 para Alice. Por quê? Porque o segundo registro de dados seria considerado uma atualização do registro anterior.

Efeitos da compactação de log do Kafka: Outra maneira de pensar sobre KStream e KTable é a seguinte: se você armazenasse um KTable em um tópico do Kafka, provavelmente desejaria habilitar a funcionalidade de compactação de log do Kafka, por exemplo, para economizar espaço de armazenamento.

No entanto, não seria seguro habilitar a compactação de log no caso de um KStream porque, assim que a compactação de log começasse a eliminar registros de dados mais antigos com a mesma chave, ela quebraria a semântica dos dados. Para retomar o exemplo de ilustração, de repente você obteria um 3 para Alice em vez de um 4 porque a compactação de log teria removido o registro de dados ("Alice", 1). Portanto, a compactação de log é perfeitamente segura para um KTable (fluxo de log de alterações), mas é um erro para um KStream (fluxo de registros).

Já vimos um exemplo de um fluxo de log de alterações na seção de streams e tabelas. Outro exemplo são os registros de captura de dados de alteração (CDC) no log de alterações de um banco de dados relacional, representando qual linha em uma tabela de banco de dados foi inserida, atualizada ou excluída.

O KTable também fornece a capacidade de procurar valores atuais de registros de dados por chaves. Essa funcionalidade de pesquisa de tabela está disponível por meio de operações de junção (consulte também Junção no Guia do Desenvolvedor) e através de Consultas Interativas.

## GlobalKTable
Apenas a DSL do Kafka Streams possui a noção de um GlobalKTable.

Assim como um KTable, um GlobalKTable é uma abstração de um fluxo de changelog, onde cada registro de dados representa uma atualização.

Um GlobalKTable difere de um KTable nos dados com os quais estão sendo populados, ou seja, quais dados do tópico subjacente do Kafka estão sendo lidos na tabela correspondente. De forma um pouco simplificada, imagine que você tenha um tópico de entrada com 5 partições. Em sua aplicação, você deseja ler este tópico em uma tabela. Além disso, você deseja executar sua aplicação em 5 instâncias para obter o máximo de paralelismo.

Se você ler o tópico de entrada em um KTable, então a instância "local" do KTable de cada instância da aplicação será populada com dados de apenas 1 partição das 5 partições do tópico.
Se você ler o tópico de entrada em um GlobalKTable, então a instância "local" do GlobalKTable de cada instância da aplicação será populada com dados de todas as partições do tópico.
O GlobalKTable fornece a capacidade de procurar os valores atuais dos registros de dados por chaves. Essa funcionalidade de pesquisa na tabela está disponível por meio de operações de junção.

Benefícios das tabelas globais:

- Junções mais convenientes e/ou eficientes: Notavelmente, as tabelas globais permitem que você faça * joins, elas suportam pesquisas por "chave estrangeira" (ou seja, você pode procurar dados na tabela não apenas pela chave do registro, mas também pelos dados nos valores do registro) e são mais eficientes ao encadear várias junções. Além disso, ao fazer junção com uma tabela global, os dados de entrada não precisam estar co-particionados.
- Podem ser usadas para "transmitir" informações para todas as instâncias em execução de sua aplicação.

Desvantagens das tabelas globais:

- Aumento do consumo de armazenamento local em comparação com o KTable (particionado) porque o tópico inteiro é rastreado.
- Aumento da carga de rede e do broker do Kafka em comparação com o KTable (particionado) porque o tópico inteiro é lido.

## Windowing

Se você está criando uma aplicação com o Kafka Streams ou o ksqlDB, e essa aplicação envolve agregações, é provável que você use janelas de tempo.
As agregações de dados acumulam ao longo do tempo e, sem um limite, essas agregações não param de acumular. É aí que entra o conceito de janelas (windowing), que define a quantidade de dados que pode se acumular.

https://www.confluent.io/blog/windowing-in-kafka-streams/

O windowing permite que você controle como agrupar registros que têm a mesma chave para operações com estado, como agregações ou junções, em chamadas "janelas". As janelas são rastreadas por chave de registro.
A DSL do Kafka Streams oferece suporte aos seguintes tipos de windowing:

- Size and Period
  - Tumbling time window (time-based): Fixed-size, non-overlapping, gap-less windows.
  - Hopping time window (time-based): Fixed-size, overlapping windows.

- Event-Triggered
  - Sliding time window (time-based): Fixed-size, overlapping windows that work on differences between record timestamps.
  - Session window (session-based): Dynamically-sized, non-overlapping, data-driven windows.

## Stateful Operations

Operações com estado dependem de um estado para processar entradas e produzir saídas, e requerem um estado armazenado associado ao processador de fluxo. Por exemplo, em operações de agregação, um estado de janela é usado para coletar os resultados de agregação mais recentes por janela. Em operações de join, um estado de janela é usado para coletar todos os registros recebidos até agora dentro dos limites da janela definida.

Observe que os estados armazenados são tolerantes a falhas. Em caso de falha, o Kafka Streams garante restaurar completamente todos os estados armazenados antes de retomar o processamento. Consulte Tolerância a Falhas para obter mais informações.

- Joining
- Count
- Aggregation
- Reduce

*Dica*: Jo.C.A.R

*Considerations*

Operações com estado não emitem resultados imediatamente. O Kafka Streams possui um mecanismo de buffer interno que armazena em cache os resultados. Dois fatores controlam quando o buffer emite registros: os registros são emitidos quando o buffer está cheio (definido igualmente por instância entre o número de armazenamentos; são 10MB por padrão) e, por padrão, o Kafka Streams chama o commit a cada 30 segundos (você não chama o commit manualmente). Neste ponto, você veria uma atualização. Para ver cada atualização que passa pela sua agregação, você pode configurar o tamanho do seu cache para zero (o que também é útil para depuração).

Mesmo com o cache, você obterá vários resultados, então, para um único e final resultado com estado, você deve usar sobrecargas de supressão com operações de agregação/redução.

## Stateless
- Flatmap(values): KStream → KStream
- Map: KStream → KStream
- Map Values: KTable → KTable ou KStream → KStream
- Branch (KStream → KStream): Branch (or split) a KStream based on the supplied predicates into one or more KStream instances.
- Filter: KStream → KStream ou KTable → KTable
- Inverse Filter: KStream → KStream ou KTable → KTable
- Foreach: KStream → void ou Stream → void ou KTable → void
- GroupBy: KStream → KGroupedStream ou KTable → KGroupedTable
- GroupByKey: KStream → KGroupedStream
- Peek: KStream → KStream
- Print: KStream → void
- SelectKey: KStream → KStream
- Table to Stream: KTable → KStream

## Processor API

A Processor API no Kafka Streams oferece muito mais flexibilidade do que o DSL padrão, mas, por ser uma API de nível mais baixo, ela requer que você seja responsável por mais detalhes. Por exemplo, você precisa especificar sua topologia real nó a nó. A Processor API também oferece acesso direto aos state stores, ao contrário das operações de agregação que foram abordadas até este ponto, e permite que você faça chamadas diretas de commit. Uma das características mais poderosas da Processor API, que não pode ser feita usando o DSL, é que ela permite que você agende operações arbitrárias usando um "punctuator", uma função que você define e que é executada em algum intervalo regular. As pontuações podem ser acionadas pelo tempo de streaming, que, como mencionado acima, depende dos carimbos de data/hora dos próprios eventos, ou pelo tempo do relógio de parede, que depende do tempo do sistema da aplicação Kafka Streams - embora possa ser mais aproximado do que parece, já que um lote de registros muitas vezes precisa terminar de processar antes que o tempo do relógio de parede seja verificado. Note que também é possível misturar recursos do DSL e da Processor API.

- addSource
- addSink
- addProcessor
- addStateStore

## Conceitos de Tempo

Event Time: timestamp criado automaticamente pelo producer se não for criado manual

Log-Append Time: Quando a mensagem chega ao broker, ele sobrescreve o timestamp da mensagem por um seu prórpio ao fazer o append da mensagem

Esses timestamp direcionamento a atuação do Kafka Streams. As operações de windows vista anteriormente funcionam utilizando esses timestamp e não pelo relógio. No Streams o timestamp mais cedo é escolhido primeiro para processamento, e isso é feito por meio da interface TimeStampExtractor

O comportamento padrão é usar o timestamp de um ConsumerRecord, que tem um timestamp definido pelo produtor ou pelo broker. A implementação padrão do TimeStampExtractor é o FailOnInvalidTimestamp, o que significa que, se você obter um timestamp menor que zero, ele lançará uma exceção. Se você deseja usar um timestamp incorporado na chave ou no valor do registro em si, pode fornecer um TimeStampExtractor personalizado.

- O tempo de streaming, por definição, é o maior timestamp visto até agora e só avança, não retrocede. Se um registro fora de ordem chegar (significando um registro que é anterior ao tempo de streaming atual, mas ainda dentro da janela mais o grace period), o tempo de streaming permanece onde está.

- Late records têm timestamps fora do tempo de janela combinado e do grace period. O atraso de um registro é determinado subtraindo o tempo de streaming do timestamp do evento.
