# Schema Registry

Define um contrato de mensagens.
Producer e Consumer buscam o Schema via http request e armazenam em cache

- Server process externo aos Kafka Brokers
- Mantem um Database de schemas (persistido em tópico interno ao Kafka)
- Mantem em cache no Schema Registry para melhor desempenho
- Componente dos Producers e Consumers
- Schema definido por tópico
- É chamado em tempo de produção de uma mensagem pelo Producer, chamada pela api REST
- Producer E Consumer evitam mensagem incompativel com o schema na escrita/leitura
- JSON Schema, Avro, Protobuff

## Configuração
- url
- credentials { username, password }
- Gradle: register { subject('...', 'x.avsc', 'AVRO'), subject('...', 'x.proto', 'PROTOBUF') }

## Protobuf
- triplas (nome, tipo, inteiro identificador)
- repetead para lista
- map<string ou int, double> (chave pode ser apenas string ou int)
- existe tipo Enum
- poder ter um campo que se refere a outro Schema, fazer import
- tipo oneof
- valores default de acordo com o tipo do campo

## Avro
- Estrutura de um tipo complexo
  1. type REQUIRED
  2. fields REQUIRED
  3. namespace

- Estrutura de um field
  1. type
  2. name
  3. Em alguns casos pode ser obrigatório conter symbols, items, default
  4. DOC é totalmente opcional

- field composto por name, type
- o tipo pode ser um array de tipos, funciona parecido com o "one of" do protobuf
- pode referenciar outros schemas
- Tipos primitivos:
  - null: no value.
  - boolean: a binary value.
  - int: 32-bit signed integer.
  - long: 64-bit signed integer.
  - float: single precision (32-bit) IEEE 754 floating-point number.
  - double: double precision (64-bit) IEEE 754 floating-point number.
  - bytes: sequence of 8-bit unsigned bytes.
  - string: unicode character sequence.

- Tipos complexos:
  - Record
  - Enum
  - Array
  - Map
  - Union
  - Fixed

## Schema Manegement

### Register
O parametro subject é passado e usado para identificacao. Como um namespace para o schema
Exemplos de nome: topico-chave, topico-valor
É gerado um id
Automaticamente é colocado a versao 1 para novos schemas

```sh
confluent schema-registry schema create \
  --subject <nome-schema> \
  --schema path/to/file.proto \
  --type PROTOBUF \
  --api-key KET \
  --api-secret SECRET
```
### Visualizando
```sh
confluent schema-registry schema describe \
  --subject <nome-schema>
  --version [N ou 'latest']
  --api-key KET \
  --api-secret SECRET

curl -u SECRET:KEY https://cluster/subjects/<nome-schema>/versions/latest

curl -u SECRET:KEY https://cluster/subjects/<nome-schema>/versions/n
```

### Ciclo de vida
1. Producer tenta serializa um Record / Consumer tenta desserializar um Record
2. (Des)Serializer procura no cache e nao encontra nada
3. Consulta o SchemaRegistry e guarda o id unico no cache
4. Record serializado e enviado ao broker/ Record desserializado e consumido

### Configurando consumer
```java
configs.put("value.deserializer", <DESERIALIZER_CLASS>)
configs.put("specific.avro.reader", <true ou false>)
configs.put("specific.protobuf.value.type", <protobuf_class_name>)
configs.put("json.value.type", <class_name>)
```

### Subject Strategies
1. TopicNameStrategy: topicA-value ( valor default )
2. RecordNameStrategy: io.confluent.Purchase-value
3. TopicRecordNameStrategy: topicA-io.confluent.Purchase-value

Para usar:
- Settar o subject name
- Configurar os clients
  - key.subject.name.strategy
  - value.subject.name.strategy

### Compatibilidade
Modos de compatibilidade

| Tipo       | Resolucao |
| :--------  | :-------- |
| BACKWARD   | Consumers usando NOVO schema conseguem ler versão antiga|
| FORWARD    | Consumers usando o schema ANTERIOR conseguem ler nova versão|
| FULL       | Combinação de BACKWARD e FORWARD |
| NONE       | Checagem desabilitada |

Modos transitivos compatibilidade
- vale o mesmo pra todos de cima mas extende para TODAS as versões (anteriores ou posteriores)
- nao tem o tipo 'NONE'

#### Backward
- deletar campos
- adicionar campos opcionais (que tenham valor default)
- atualizar CONSUMER primeiro

#### Forward
- deletar campos que tenham valor default (opcionais)
- adicionar novos campos
- atualizar PRODUCER primeiro

#### Full
- deletar campos com valor default (opcionais)
- adicionar campos com valor default (opcionais)
- não exige ordem para atualizar producer/consumer
