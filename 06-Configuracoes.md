# Configurações

## Tópicos
- auto.create.topics.enable: em alguns comandos o tópico é criado caso nao exista e essa flag for true
- replication.factor
- min.insync.replicas

## Encriptação
- Plaintext (DEFAULT)
- SSL (Ao settar não tem como usar o Zero Copy Data Transfer)

## Broker
- log.dir
- broker.id (deve ser único)
- port

## Producer
- batch.size
- compression
- acks
  a. 0, nao espera ninguem
  b. 1, espera OK do lider
  c. all, espera lider e replicas

## ksqlDL
- delete.topic.enable : quando true, os topicos internos sao limpos
