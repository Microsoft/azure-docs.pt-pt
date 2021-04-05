---
title: Migrar dados da pós-Menstrual para a conta API API da Azure Cosmos DB Cassandra utilizando Apache Kafka
description: Aprenda a usar o Kafka Connect para sincronizar dados de PostgreSQL a Azure Cosmos DB Cassandra API em tempo real.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 01/05/2021
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 0038219ee8c1721ff5ab2be76231d33d2bd9064d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98203070"
---
# <a name="migrate-data-from-postgresql-to-azure-cosmos-db-cassandra-api-account-using-apache-kafka"></a>Migrar dados da pós-Menstrual para a conta API API da Azure Cosmos DB Cassandra utilizando Apache Kafka
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Cassandra API em Azure Cosmos DB tornou-se uma ótima escolha para cargas de trabalho empresariais em execução em Apache Cassandra por várias razões tais como:

* **Economia de custos significativa:** Você pode economizar custos com Azure Cosmos DB, que inclui o custo de VM's, largura de banda, e quaisquer licenças oracle aplicáveis. Além disso, não é preciso gerir os centros de dados, servidores, armazenamento SSD, networking e custos de eletricidade.

* **Melhor escalabilidade e disponibilidade:** Elimina pontos únicos de falha, melhor escalabilidade e disponibilidade para as suas aplicações.

* **Sem despesas gerais de gestão e monitorização:** Como um serviço de nuvem totalmente gerido, a Azure Cosmos DB remove a sobrecarga de gestão e monitorização de uma miríade de configurações.

[Kafka Connect](https://kafka.apache.org/documentation/#connect) é uma plataforma para transmitir dados entre [Apache Kafka](https://kafka.apache.org/) e outros sistemas de forma escalável e fiável. Suporta vários conectores fora da prateleira, o que significa que não precisa de código personalizado para integrar sistemas externos com Apache Kafka.

Este artigo demonstrará como utilizar uma combinação de conectores Kafka para criar um pipeline de dados para sincronizar continuamente registos de uma base de dados relacional, como [PostgreSQL](https://www.postgresql.org/) a [Azure Cosmos DB Cassandra API](cassandra-introduction.md).

## <a name="overview"></a>Descrição Geral

Aqui está uma visão geral de alto nível do fluxo final apresentado neste artigo.

Os dados da tabela PostgreSQL serão empurrados para Apache Kafka utilizando o [conector Debezium PostgreSQL,](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html)que é um conector **de origem** Kafka Connect. As inserções, atualizações ou eliminação dos registos na tabela PostgreSQL serão capturadas como `change data` eventos e enviadas para o tópico(s) de Kafka. O [conector Apache Kafka (conector](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html) **de pia** Kafka Connect), forma a segunda parte do oleoduto. Sincronizará os eventos de alteração de dados do tópico Kafka para as tabelas API da Azure Cosmos DB Cassandra.

> [!NOTE]
> A utilização de funcionalidades específicas do conector Apache Kafka do DataStax permite-nos empurrar dados para várias tabelas. Neste exemplo, o conector ajudar-nos-á a persistir na alteração dos registos de dados para duas tabelas Cassandra que podem suportar diferentes requisitos de consulta.

## <a name="prerequisites"></a>Pré-requisitos

* [Provisão de uma conta Azure Cosmos DB Cassandra API](create-cassandra-dotnet.md#create-a-database-account)
* [Use cqlsh ou concha aloiada para validação](cassandra-support.md#hosted-cql-shell-preview)
* JDK 8 ou superior
* [Estivador](https://www.docker.com/) (opcional)

## <a name="base-setup"></a>Configuração base

### <a name="set-up-postgresql-database-if-you-havent-already"></a>Configurar a base de dados PostgreSQL se ainda não o fez. 

Esta pode ser uma base de dados existente no local ou pode [descarregar e instalar uma](https://www.postgresql.org/download/) na sua máquina local. Também é possível usar um [recipiente Docker.](https://hub.docker.com/_/postgres)

Para iniciar um recipiente:

```bash
docker run --rm -p 5432:5432 -e POSTGRES_PASSWORD=<enter password> postgres
```

Ligue-se à sua instância PostgreSQL utilizando [`psql`](https://www.postgresql.org/docs/current/app-psql.html) o cliente:

```bash
psql -h localhost -p 5432 -U postgres -W -d postgres
```

Criar uma tabela para armazenar informações de pedidos de amostragem:

```sql
CREATE SCHEMA retail;

CREATE TABLE retail.orders_info (
    orderid SERIAL NOT NULL PRIMARY KEY,
    custid INTEGER NOT NULL,
    amount INTEGER NOT NULL,
    city VARCHAR(255) NOT NULL,
    purchase_time VARCHAR(40) NOT NULL
);
```

### <a name="using-the-azure-portal-create-the-cassandra-keyspace-and-the-tables-required-for-the-demo-application"></a>Utilizando o portal Azure, crie o Espaço Chave Cassandra e as tabelas necessárias para a aplicação de demonstração.

> [!NOTE]
> Use o mesmo Keyspace e nomes de tabelas como abaixo

```sql
CREATE KEYSPACE retail WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE retail.orders_by_customer (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (customer_id, purchase_time)) WITH CLUSTERING ORDER BY (purchase_time DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE retail.orders_by_city (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (city,order_id)) WITH cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

### <a name="setup-apache-kafka"></a>Configuração Apache Kafka 

Este artigo usa um cluster local, mas você pode escolher qualquer outra opção. [Baixe Kafka,](https://kafka.apache.org/downloads)desaperte-o, inicie o agrupamento Zookeeper e Kafka.

```bash
cd <KAFKA_HOME>/bin

#start zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

#start kafka (in another terminal)
bin/kafka-server-start.sh config/server.properties
```

### <a name="setup-connectors"></a>Conectores de configuração

Instale o conector Debezium PostgreSQL e DataStax Apache Kafka. Descarregue o arquivo plug-in do conector Debezium PostgreSQL. Por exemplo, para baixar a versão 1.3.0 do conector (o mais tardar no momento da escrita), utilize [este link](https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.3.0.Final/debezium-connector-postgres-1.3.0.Final-plugin.tar.gz). Descarregue o conector DataStax Apache Kafka a partir [deste link](https://downloads.datastax.com/#akc).

Desaperte os arquivos do conector e copie os ficheiros JAR para o [plugin.path kafka Connect](https://kafka.apache.org/documentation/#connectconfigs).


```bash
cp <path_to_debezium_connector>/*.jar <KAFKA_HOME>/libs
cp <path_to_cassandra_connector>/*.jar <KAFKA_HOME>/libs
```

> Para mais informações, consulte a documentação [Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) e [DataStax.](https://docs.datastax.com/en/kafka/doc/)

## <a name="configure-kafka-connect-and-start-data-pipeline"></a>Configurar o Kafka Connect e iniciar o pipeline de dados

### <a name="start-kafka-connect-cluster"></a>Iniciar cluster Kafka Connect

```bash
cd <KAFKA_HOME>/bin
./connect-distributed.sh ../config/connect-distributed.properties
```

### <a name="start-postgresql-connector-instance"></a>Iniciar a instância do conector PostgreSQL

Guarde a configuração do conector (JSON) para um exemplo de ficheiro `pg-source-config.json`

```json
{
    "name": "pg-orders-source",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "localhost",
        "database.port": "5432",
        "database.user": "postgres",
        "database.password": "password",
        "database.dbname": "postgres",
        "database.server.name": "myserver",
        "plugin.name": "wal2json",
        "table.include.list": "retail.orders_info",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter"
    }
}
```

Para iniciar a instância do conector PostgreSQL:

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-config.json http://localhost:8083/connectors
```

> [!NOTE]
> Para eliminar, pode utilizar: `curl -X DELETE http://localhost:8083/connectors/pg-orders-source`


### <a name="insert-data"></a>Inserir dados

A `orders_info` tabela contém detalhes de encomenda, tais como ID de encomenda, ID do cliente, cidade, etc. Povoar a tabela com dados aleatórios utilizando o SQL abaixo.

```sql
insert into retail.orders_info (
    custid, amount, city, purchase_time
)
select
    random() * 10000 + 1,
    random() * 200,
    ('{New Delhi,Seattle,New York,Austin,Chicago,Cleveland}'::text[])[ceil(random()*3)],
    NOW() + (random() * (interval '1 min'))
from generate_series(1, 10) s(i);
```

Deve inserir 10 registos na mesa. Certifique-se de atualizar o número de registos `generate_series(1, 10)` abaixo, de acordo com os seus requisitos exemplo, para inserir `100` registos, uso `generate_series(1, 100)`

Para confirmar:

```bash
select * from retail.orders_info;
```

Verifique os eventos de captura de dados de alteração no tópico Kafka

> [!NOTE]
> Note que o nome tópico é `myserver.retail.orders_info` o que, de acordo com a [convenção de conector](https://debezium.io/documentation/reference/1.3/connectors/postgresql.html#postgresql-topic-names)

```bash
cd <KAFKA_HOME>/bin

./kafka-console-consumer.sh --topic myserver.retail.orders_info --bootstrap-server localhost:9092 --from-beginning
```

Deve ver os eventos de dados de alteração no formato JSON.

### <a name="start-datastax-apache-kafka-connector-instance"></a>Iniciar caso de conector Apache Kafka

Guarde a configuração do conector (JSON) para um exemplo de ficheiro `cassandra-sink-config.json` e atualize as propriedades de acordo com o ambiente.

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "myserver.retail.orders_info",
        "contactPoints": "<Azure Cosmos DB account name>.cassandra.cosmos.azure.com",
        "loadBalancing.localDc": "<Azure Cosmos DB region e.g. Southeast Asia>",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "<path to JDK keystore path e.g. <JAVA_HOME>/jre/lib/security/cacerts>",
        "ssl.keystore.password": "<keystore password: it is 'changeit' by default>",
        "port": 10350,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "auth.username": "<Azure Cosmos DB user name (same as account name)>",
        "auth.password": "<Azure Cosmos DB password>",
        "topic.myserver.retail.orders_info.retail.orders_by_customer.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "topic.myserver.retail.orders_info.retail.orders_by_city.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "transforms": "unwrap",
        "transforms.unwrap.type": "io.debezium.transforms.ExtractNewRecordState",
        "offset.flush.interval.ms": 10000
    }
}
```

Para iniciar a instância do conector:

```bash
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

O conector deve entrar em ação e o gasoduto de ponta a ponta de PostgreSQL a Azure Cosmos DB estará operacional.

### <a name="query-azure-cosmos-db"></a>Consultar o Azure Cosmos DB

Verifique as mesas cassandra em Azure Cosmos DB. Aqui estão algumas das consultas que pode experimentar:

```sql
select count(*) from retail.orders_by_customer;
select count(*) from retail.orders_by_city;

select * from retail.orders_by_customer;
select * from retail.orders_by_city;

select * from retail.orders_by_city where city='Seattle';
select * from retail.orders_by_customer where customer_id = 10;
```

Pode continuar a inserir mais dados no PostgreSQL e confirmar que os registos estão sincronizados com a Azure Cosmos DB.

## <a name="next-steps"></a>Passos seguintes

* [Integre Apache Kafka e Azure Cosmos DB Cassandra API usando Kafka Connect](cassandra-kafka-connect.md)
* [Integre Apache Kafka Connect em Azure Event Hubs (Preview) com Debezium para captura de dados de mudança](../event-hubs/event-hubs-kafka-connect-debezium.md)
* [Migrar dados da Oracle para a Azure Cosmos DB Cassandra API usando Blitzz](oracle-migrate-cosmos-db-blitzz.md)
* [Aprovisionar o débito em contentores e bases de dados](set-throughput.md) 
* [Principais práticas de partição](partitioning-overview.md#choose-partitionkey)
* [Estimativa RU/s usando os artigos de planificador de capacidadeS do Azure Cosmos DB](estimate-ru-with-capacity-planner.md)

