---
title: Integre Apache Kafka Connect em Azure Event Hubs (Preview) com Debezium para captura de dados de mudança
description: Este artigo fornece informações sobre como usar Debezium com Azure Event Hubs para Kafka.
ms.topic: how-to
author: abhirockzz
ms.author: abhishgu
ms.date: 08/11/2020
ms.openlocfilehash: ae3ef2e1f35be432558769c512845543867ef27a
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505414"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-preview-with-debezium-for-change-data-capture"></a>Integre o suporte do Apache Kafka Connect nos hubs de eventos Azure (Preview) com Debezium para captura de dados de mudança

**Change Data Capture (CDC)** é uma técnica usada para rastrear alterações ao nível da linha nas tabelas de bases de dados em resposta à criação, atualização e eliminação de operações. [Debezium](https://debezium.io/) é uma plataforma distribuída que se baseia em cima das funcionalidades de Captura de Dados de Mudança disponíveis em diferentes bases de dados (por exemplo, [descodagem lógica em PostgreSQL).](https://www.postgresql.org/docs/current/static/logicaldecoding-explanation.html) Fornece um conjunto de [conectores Kafka Connect](https://debezium.io/documentation/reference/1.2/connectors/index.html) que tocam em alterações ao nível da linha na tabela de bases de dados e convertem-nos em fluxos de eventos que são depois enviados para [Apache Kafka](https://kafka.apache.org/).

Este tutorial explica-lhe como configurar um sistema baseado na captura de dados de mudança em Azure usando [Azure Event Hubs](./event-hubs-about.md?WT.mc_id=devto-blog-abhishgu) (para Kafka), [Azure DB para PostgreSQL](../postgresql/overview.md) e Debezium. Utilizará o [conector Debezium PostgreSQL](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html) para transmitir modificações de base de dados de postgresQL a tópicos kafka em Azure Event Hubs

> [!NOTE]
> Este artigo contém referências ao termo *whitelist*, um termo que a Microsoft já não utiliza. Quando o termo for removido do software, vamos removê-lo deste artigo.

Neste tutorial, siga os seguintes passos:

> [!div class="checklist"]
> * Criar um espaço de nomes dos Hubs de Eventos
> * Configuração e configuração base de dados Azure para PostgreSQL
> * Configure e executar Kafka Connect com o conector PostgreSQL de Debezium
> * Captura de dados de alteração de teste
> * (Opcional) Consumir alterar eventos de dados com um `FileStreamSink` conector

## <a name="pre-requisites"></a>Pré-requisitos
Para completar esta caminhada, você vai precisar:

- Subscrição do Azure. Se não tiver uma subscrição, [crie uma conta gratuita](https://azure.microsoft.com/free/).
- Linux/MacOS
- Versão do Kafka (versão 1.1.1, Scala versão 2.11), disponível em [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1).
- Ler o artigo de introdução dos [Event Hubs for Apache Kafka](./event-hubs-for-kafka-ecosystem-overview.md) (Hubs de Eventos para Apache Kafka).

## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos
É necessário um espaço de nomes dos Hubs de Eventos para enviar e receber a partir de qualquer serviços dos Hubs de Eventos. Consulte [a criação de um centro de eventos](event-hubs-create.md) para obter instruções para criar um espaço de nome e um centro de eventos. Obtenha a cadeia de ligação dos Hubs de Eventos e o nome de domínio completamente qualificado (FQDN), para utilizar mais tarde. Para obter instruções, veja [Get an Event Hubs connection string](event-hubs-get-connection-string.md) (Obter uma cadeia de ligação dos Hubs de Eventos). 

## <a name="setup-and-configure-azure-database-for-postgresql"></a>Configuração e configuração base de dados Azure para PostgreSQL
[A azure Database for PostgreSQL](../postgresql/overview.md) é um serviço de base de dados relacional baseado na versão comunitária do motor de base de dados PostgreSQL de código aberto, e está disponível em duas opções de implementação: Single Server e Hyperscale (Citus). [Siga estas instruções](../postgresql/quickstart-create-server-database-portal.md) para criar uma Base de Dados Azure para servidor PostgreSQL utilizando o portal Azure. 

## <a name="setup-and-run-kafka-connect"></a>Configurar e executar Kafka Connect
Esta secção abrangerá os seguintes tópicos:

- Instalação do conector de debezium
- Configurar a Ligação kafka para centros de eventos
- Inicie o cluster Kafka Connect com o conector Debezium

### <a name="download-and-setup-debezium-connector"></a>Download e configurar o conector Debezium
Por favor, siga as instruções mais recentes na [documentação de Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) para descarregar e configurar o conector.

- Descarregue o arquivo plug-in do conector. Por exemplo, para descarregar a versão `1.2.0` do conector, utilize este link - https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.2.0.Final/debezium-connector-postgres-1.2.0.Final-plugin.tar.gz
- Extraia os ficheiros JAR e copie-os para o [plugin.path da Kafka Connect](https://kafka.apache.org/documentation/#connectconfigs).


### <a name="configure-kafka-connect-for-event-hubs"></a>Configurar o Kafka Connect para os Hubs de Eventos
Ao redirecionar o débito do Kafka Connect do Kafka para os Hubs de Eventos, é necessário fazer uma reconfiguração mínima.  O exemplo seguinte, `connect-distributed.properties`, ilustra como configurar o Connect para se autenticar e comunicar com o ponto final do Kafka nos Hubs de Eventos:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

> [!IMPORTANT]
> `{YOUR.EVENTHUBS.CONNECTION.STRING}`Substitua-a pela cadeia de ligação para o seu espaço de nomes 'Centros de Eventos'. Para obter instruções sobre a obtenção da cadeia de ligação, consulte [obter uma cadeia de ligação Dos Centros de Eventos](event-hubs-get-connection-string.md). Aqui está uma configuração de exemplo: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


### <a name="run-kafka-connect"></a>Executar o Kafka Connect
Neste passo, é iniciada uma função de trabalho do Kafka Connect localmente no modo distribuído e são utilizados os Hubs de Eventos para manter o estado do cluster.

1. Guarde o ficheiro anterior, `connect-distributed.properties`, localmente.  Confirme que substitui todos os valores dentro das chavetas.
2. Navegue para a localização da versão do Kafka no computador.
3. Corra `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties` e espere que o aglomerado comece.

> [!NOTE]
> Kafka Connect utiliza a API AdminClient Kafka para criar automaticamente tópicos com configurações recomendadas, incluindo compactação. Uma rápida observação ao espaço de nomes no portal do Azure mostra que os tópicos internos da função de trabalho do Connect foram criados de forma automática.
>
> Os tópicos internos kafka Connect **devem utilizar a compactação.**  A equipa do Event Hubs não é responsável pela fixação de configurações impróprias se os tópicos internos do Connect estiverem configurados incorretamente.

### <a name="configure-and-start-the-debezium-postgresql-source-connector"></a>Configure e inicie o conector de fonte PostgreSQL de Debezium

Crie um ficheiro de configuração ( `pg-source-connector.json` ) para o conector de fonte PostgreSQL - substitua os valores de acordo com a sua instância Azure PostgreSQL.

```json
{
    "name": "todo-connector",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "<replace with Azure PostgreSQL instance name>.postgres.database.azure.com",
        "database.port": "5432",
        "database.user": "<replace with database user name>",
        "database.password": "<replace with database password>",
        "database.dbname": "postgres",
        "database.server.name": "my-server",
        "plugin.name": "wal2json",
        "table.whitelist": "public.todos"
    }
}
```

> [!TIP]
> `database.server.name` atributo é um nome lógico que identifica e fornece um espaço de nome para o servidor/cluster de base de dados postgreSQL em particular sendo monitorizado.. Para obter informações detalhadas, consulte a [documentação da Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-property-database-server-name)

Para criar uma instância do conector, utilize o ponto final da API kafka Connect REST:

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-connector.json http://localhost:8083/connectors
```

Para verificar o estado do conector:

```bash
curl -s http://localhost:8083/connectors/todo-connector/status
```

## <a name="test-change-data-capture"></a>Captura de dados de alteração de teste
Para ver a captura de dados em ação, terá de criar/atualizar/eliminar registos na base de dados Azure PostgreSQL.

Comece por ligar à sua base de dados Azure PostgreSQL (o exemplo abaixo utiliza [psql)](https://www.postgresql.org/docs/12/app-psql.html)

```bash
psql -h <POSTGRES_INSTANCE_NAME>.postgres.database.azure.com -p 5432 -U <POSTGRES_USER_NAME> -W -d <POSTGRES_DB_NAME> --set=sslmode=require

e.g. 

psql -h my-postgres.postgres.database.azure.com -p 5432 -U testuser@my-postgres -W -d postgres --set=sslmode=require
```

**Criar uma tabela e inserir registos**

```sql
CREATE TABLE todos (id SERIAL, description VARCHAR(50), todo_status VARCHAR(10), PRIMARY KEY(id));

INSERT INTO todos (description, todo_status) VALUES ('setup postgresql on azure', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('setup kafka connect', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('configure and install connector', 'in-progress');
INSERT INTO todos (description, todo_status) VALUES ('start connector', 'pending');
```

O conector deve agora entrar em ação e enviar eventos de dados de alteração para um tópico de Event Hubs com o seguinte na,e `my-server.public.todos` , assumindo que tem `my-server` como valor e é a tabela `database.server.name` `public.todos` cujas alterações está a rastrear (de acordo com `table.whitelist` a configuração)

**Consulte o tópico dos Centros de Eventos**

Vamos introspeção do conteúdo do tópico para garantir que tudo está funcionando como esperado. O exemplo abaixo usa [`kafkacat`](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) , mas também pode criar um consumidor [usando qualquer uma das opções listadas aqui](apache-kafka-developer-guide.md)

Criar um ficheiro com `kafkacat.conf` o seguinte conteúdo:

```
metadata.broker.list=<enter event hubs namespace>.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanisms=PLAIN
sasl.username=$ConnectionString
sasl.password=<enter event hubs connection string>
```

> [!NOTE]
> Atualizar `metadata.broker.list` e `sasl.password` atributos de acordo com a `kafkacat.conf` informação do Event Hubs. 

Num terminal diferente, inicie um consumidor:

```bash
export KAFKACAT_CONFIG=kafkacat.conf
export BROKER=<enter event hubs namespace>.servicebus.windows.net:9093
export TOPIC=my-server.public.todos

kafkacat -b $BROKER -t $TOPIC -o beginning
```

Você deve ver as cargas JSON que representam os eventos de dados de mudança gerados em PostgreSQL em resposta às linhas que tinha acabado de adicionar à `todos` tabela. Aqui está um corte da carga útil:


```json
{
    "schema": {...},
    "payload": {
        "before": null,
        "after": {
            "id": 1,
            "description": "setup postgresql on azure",
            "todo_status": "complete"
        },
        "source": {
            "version": "1.2.0.Final",
            "connector": "postgresql",
            "name": "fullfillment",
            "ts_ms": 1593018069944,
            "snapshot": "last",
            "db": "postgres",
            "schema": "public",
            "table": "todos",
            "txId": 602,
            "lsn": 184579736,
            "xmin": null
        },
        "op": "c",
        "ts_ms": 1593018069947,
        "transaction": null
    }
```

O evento é composto pelo junto com o `payload` seu `schema` (omitido para a brevidade). Na `payload` secção, note como a operação de criação ( `"op": "c"` ) é representada - significa que era uma linha `"before": null` `INSERT` recém-ed, `after` fornece valores para as colunas na linha, `source` fornece os metadados de instância PostgreSQL de onde este evento foi recolhido, etc.

Pode tentar o mesmo com a atualização ou apagar operações também e introspeção nos eventos de dados de alteração. Por exemplo, para atualizar o estado da tarefa para `configure and install connector` (assumindo que `id` `3` é):

```sql
UPDATE todos SET todo_status = 'complete' WHERE id = 3;
```

## <a name="optional-install-filestreamsink-connector"></a>(Opcional) Instalar conector FileStreamSink
Agora que todas as alterações de `todos` mesa estão a ser capturadas no tópico Event Hubs, usaremos o conector FileStreamSink (que está disponível por padrão no Kafka Connect) para consumir estes eventos.

Crie um ficheiro de configuração ( `file-sink-connector.json` ) para o conector - substitua o `file` atributo de acordo com o seu sistema de ficheiros

```json
{
    "name": "cdc-file-sink",
    "config": {
        "connector.class": "org.apache.kafka.connect.file.FileStreamSinkConnector",
        "tasks.max": "1",
        "topics": "my-server.public.todos",
        "file": "<enter full path to file e.g. /Users/foo/todos-cdc.txt>"
    }
}
```

Para criar o conector e verificar o seu estado:

```bash
curl -X POST -H "Content-Type: application/json" --data @file-sink-connector.json http://localhost:8083/connectors

curl http://localhost:8083/connectors/cdc-file-sink/status
```

Inserir/atualizar/apagar registos de bases de dados e monitorizar os registos no ficheiro de pia de saída configurado:

```bash
tail -f /Users/foo/todos-cdc.txt
```


## <a name="cleanup"></a>Limpeza
O Kafka Connect cria tópicos dos Hubs de Eventos para armazenar configurações, deslocamentos e estados que persistem mesmo depois de o cluster do Connect ter sido removido. A menos que essa persistência seja pretendida, recomenda-se que os tópicos sejam eliminados. Também pode querer apagar o `my-server.public.todos` Centro de Eventos que foram criados durante esta caminhada.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os Centros de Eventos para Kafka, consulte os seguintes artigos:  

- [Espelhar um mediador de Kafka num hub de eventos](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ligar o Apache Spark a um hub de eventos](event-hubs-kafka-spark-tutorial.md)
- [Ligar o Apache Flink a um hub de eventos](event-hubs-kafka-flink-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Explorar exemplos no nosso GitHub)
- [Ligar o Akka Streams a um hub de eventos](event-hubs-kafka-akka-streams-tutorial.md)
- [Guia de desenvolvimento apache Kafka para hubs de eventos Azure](apache-kafka-developer-guide.md)