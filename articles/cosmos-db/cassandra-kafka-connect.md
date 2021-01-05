---
title: Integre Apache Kafka e Azure Cosmos DB Cassandra API usando Kafka Connect
description: Saiba como ingerir dados de Kafka a Azure Cosmos DB Cassandra API usando DataStax Apache Kafka Connector
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 12/14/2020
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 25972ba2bb30c39838c4822a42af292e8d8b1dba
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803634"
---
# <a name="ingest-data-from-apache-kafka-into-azure-cosmos-db-cassandra-api-using-kafka-connect"></a>Ingerir dados de Apache Kafka em Azure Cosmos DB Cassandra API usando Kafka Connect
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

As aplicações cassandra existentes podem facilmente trabalhar com a [API API AZURE Cosmos DB Cassandra](cassandra-introduction.md) devido à sua [compatibilidade com o condutor CQLv4.](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) Aproveita esta capacidade para se integrar com plataformas de streaming como [a Apache Kafka](https://kafka.apache.org/) e trazer dados para a Azure Cosmos DB.

Os dados em Apache Kafka (tópicos) só são úteis quando consumidos por outras aplicações ou ingeridos noutros sistemas. É possível construir uma solução utilizando as APIs [de Produtor/Consumidor de Kafka](https://kafka.apache.org/documentation/#api) [usando um SDK de idioma e cliente à sua escolha.](https://cwiki.apache.org/confluence/display/KAFKA/Clients) O Kafka Connect fornece uma solução alternativa. É uma plataforma para transmitir dados entre Apache Kafka e outros sistemas de uma forma escalável e fiável. Uma vez que o Kafka Connect suporta os conectores da prateleira que incluem a Cassandra, não é necessário escrever código personalizado para integrar Kafka com a Azure Cosmos DB Cassandra API. 

Neste artigo, vamos usar o [conector Apache Kafka de](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html)código aberto, que funciona no topo da estrutura Kafka Connect para ingerir registos de um tópico Kafka em filas de uma ou mais tabelas de Cassandra. O exemplo fornece uma configuração reutilizável utilizando o Docker Compose. Isto é bastante conveniente, uma vez que permite-lhe arrancar todos os componentes necessários localmente com um único comando. Estes componentes incluem Kafka, Zookeeper, Kafka Connect worker, e a aplicação do gerador de dados da amostra.

Aqui está uma repartição dos componentes e suas definições de serviço - pode consultar o ficheiro completo `docker-compose` [no repo GitHub](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/docker-compose.yaml).

- Kafka e Zookeeper usam imagens [de debezium.](https://hub.docker.com/r/debezium/kafka/)
- Para funcionar como um recipiente Docker, o Conector Apache Kafka de datas é assado em cima de uma imagem docker existente - [debezium/connect-base](https://github.com/debezium/docker-images/tree/master/connect-base/1.2). Esta imagem inclui uma instalação de Kafka e suas bibliotecas Kafka Connect, tornando assim realmente conveniente adicionar conectores personalizados. Pode consultar o [Dockerfile.](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/connector/Dockerfile)
- As `data-generator` sementes de serviço geradas aleatoriamente (JSON) no tópico `weather-data` Kafka. Pode consultar o código e `Dockerfile` [no repo GitHub](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/data-generator/)

## <a name="prerequisites"></a>Pré-requisitos

* [Provisão de uma conta Azure Cosmos DB Cassandra API](create-cassandra-dotnet.md#create-a-database-account)

* [Use cqlsh ou concha aloiada para validação](cassandra-support.md#hosted-cql-shell-preview)

* Instalar [Docker](https://docs.docker.com/get-docker/) e [Docker Compose](https://docs.docker.com/compose/install)

## <a name="create-keyspace-tables-and-start-the-integration-pipeline"></a>Criar Keyspace, tabelas e iniciar o pipeline de integração

Utilizando o portal Azure, crie o Espaço Chave Cassandra e as tabelas necessárias para a aplicação de demonstração.

> [!NOTE]
> Use o mesmo Keyspace e nomes de tabelas como abaixo

```sql
CREATE KEYSPACE weather WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE weather.data_by_state (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (state, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE weather.data_by_station (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (station_id, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

Clone o gitHub repo:

```bash
git clone https://github.com/Azure-Samples/cosmosdb-cassandra-kafka
cd cosmosdb-cassandra-kafka
```

Inicie todos os serviços:

```shell
docker-compose --project-name kafka-cosmos-cassandra up --build
```

> [!NOTE]
> Pode demorar algum tempo a descarregar e a iniciar os contentores: este é apenas um processo único.

Para confirmar se todos os contentores já começaram:

```shell
docker-compose -p kafka-cosmos-cassandra ps
```

A aplicação do gerador de dados começará a bombear dados para o `weather-data` tópico em Kafka. Também pode fazer uma verificação rápida da sanidade para confirmar. Espreite para o contentor Docker que está a executar o trabalhador de ligação Kafka:


```bash
docker exec -it kafka-cosmos-cassandra_cassandra-connector_1 bash
```

Assim que cair na concha do recipiente, basta iniciar o habitual processo de consumidor de consolas Kafka e deverá ver os dados meteorológicos (em formato JSON) a fluir.

```bash
cd ../bin
./kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic weather-data
```

## <a name="cassandra-sink-connector-setup"></a>Configuração do conector Cassandra Sink

Copie o conteúdo JSON abaixo num ficheiro (pode nomeá-lo). `cassandra-sink-config.json` Terá de atualizá-lo de acordo com a sua configuração e o resto desta secção fornecerá orientação em torno deste tópico.

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "weather-data",
        "contactPoints": "<cosmos db account name>.cassandra.cosmos.azure.com",
        "port": 10350,
        "loadBalancing.localDc": "<cosmos db region e.g. Southeast Asia>",
        "auth.username": "<enter username for cosmosdb account>",
        "auth.password": "<enter password for cosmosdb account>",
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "/etc/alternatives/jre/lib/security/cacerts/",
        "ssl.keystore.password": "changeit",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "topic.weather-data.weather.data_by_state.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "topic.weather-data.weather.data_by_station.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter",
        "value.converter.schemas.enable": false,
        "offset.flush.interval.ms": 10000
    }
}
```

Aqui está um resumo dos atributos:

**Conectividade básica**

- `contactPoints`: inserir o ponto de contacto para Cosmos DB Cassandra
- `loadBalancing.localDc`: entrar na região para a conta de Cosmos DB, por exemplo, sudeste asiático
- `auth.username`: insira o nome de utilizador
- `auth.password`: introduzir a senha
- `port`: insira o valor da porta (isto `10350` é, não `9042` . deixá-lo como está)

**Configuração SSL**

A Azure Cosmos DB impõe conectividade segura sobre o conector [SSL](database-security.md) e o conector Kafka Connectr também suporta SSL.

- `ssl.keystore.path`: caminho para a loja de chaves JDK no recipiente - `/etc/alternatives/jre/lib/security/cacerts/`
- `ssl.keystore.password`: Palavra-passe de teclas JDK (padrão)
- `ssl.hostnameValidation`: Viramos a validação do nome de anfitrião do próprio nó
- `ssl.provider`: `JDK` é utilizado como provedor SSL

**Parâmetros genéricos**

- `key.converter`: Usamos o conversor de cordas `org.apache.kafka.connect.storage.StringConverter`
- `value.converter`: uma vez que os dados em tópicos kafka é JSON, nós fazemos uso de `org.apache.kafka.connect.json.JsonConverter`
- `value.converter.schemas.enable`: Uma vez que a nossa carga útil JSON não tem um esquema associado a ele (para efeitos da app de demonstração), precisamos instruir a Kafka Connect a não procurar um esquema definindo este atributo para `false` . Não fazê-lo resultará em falhas.

### <a name="install-the-connector"></a>Instale o conector

Instale o conector utilizando o ponto final Kafka Connect REST:

```shell
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

Para verificar o estado:

```
curl http://localhost:8080/connectors/kafka-cosmosdb-sink/status
```

Se tudo correr bem, o conector deve começar a tecer a sua magia. Deve autenticar a Azure Cosmos DB e começar a ingerir dados do tópico Kafka ( `weather-data` ) nas tabelas Cassandra - `weather.data_by_state` e `weather.data_by_station`

Agora pode consultar os dados nas tabelas. Dirija-se ao portal Azure, traga a concha CQL para a sua conta DB Azure Cosmos.

:::image type="content" source="./media/cassandra-kafka-connect/cqlsh.png" alt-text="Open CQLSH":::

## <a name="query-data-from-azure-cosmos-db"></a>Dados de consulta da Azure Cosmos DB

Verifique as `data_by_state` `data_by_station` mesas. Aqui estão algumas consultas de amostra para começar:

```sql
select * from weather.data_by_state where state = 'state-1';
select * from weather.data_by_state where state IN ('state-1', 'state-2');
select * from weather.data_by_state where state = 'state-3' and ts > toTimeStamp('2020-11-26');

select * from weather.data_by_station where station_id = 'station-1';
select * from weather.data_by_station where station_id IN ('station-1', 'station-2');
select * from weather.data_by_station where station_id IN ('station-2', 'station-3') and ts > toTimeStamp('2020-11-26');
```

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

* [Aprovisionar o débito em contentores e bases de dados](set-throughput.md) 
* [Principais práticas de partição](partitioning-overview.md#choose-partitionkey)
* [Estimativa RU/s usando os artigos de planificador de capacidadeS do Azure Cosmos DB](estimate-ru-with-capacity-planner.md)
