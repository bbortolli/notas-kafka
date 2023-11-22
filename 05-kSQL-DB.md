# kSQL DB

- DB otimizado para processamento de eventos
- Roda num cluster próprio e adjacente ao Kafka Cluster
- programa de processamento de eventos escrito em SQL
- CLI, REST API, Java lib e integração com Kafka Connect

## Transformando Dados

ksqlDB permite transformar eventos em um stream e enviar para um novo stream.
Exemplo: transformar um unix timestamp em um formato legível.
Exemplo: remover dados sensíveis de eventos em um stream interno para enviar para outro lugar

ksqlDB pode ser usada para realizar diversas transformações quando lidando com dados:

- Changing data types (CAST)
- Reformatting date/time fields (TIMESTAMPTOSTRING)
- Changing field names (AS)
- Dropping fields
- Concatenating fields (CONCAT)

## Exemplos

```sql
CREATE STREAM people WITH (KAFKA_TOPIC='topic1', VALUE_FORMAT='AVRO');

CREATE STREAM MOVEMENTS (PERSON VARCHAR KEY, LOCATION VARCHAR)
  WITH (VALUE_FORMAT='JSON', PARTITIONS=1, KAFKA_TOPIC='movements');

CREATE STREAM ORDERS_NY AS
  SELECT *
    FROM ORDERS
    WHERE ADDRESS->STATE='New York';

CREATE STREAM ny_orders AS SELECT * FROM ORDERS WHERE
 ADDRESS->STATE='NY' EMIT CHANGES;

CREATE STREAM ORDERS_ENRICHED AS
  SELECT O.*,
         I.*,
         O.ORDERUNITS * I.UNIT_COST AS TOTAL_ORDER_VALUE,
  FROM ORDERS O
       LEFT OUTER JOIN ITEMS I
       ON O.ITEMID = I.ID ;
```
