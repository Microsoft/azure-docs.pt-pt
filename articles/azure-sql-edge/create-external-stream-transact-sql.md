---
title: CRIAR FLUXO EXTERNO (Transact-SQL) - Borda SQL Azure (Pré-visualização)
description: Conheça a declaração create external STREAM em Azure SQL Edge (Pré-visualização)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: d4ad446d43c90eee1c48ee2ba94585242805fa7d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597934"
---
# <a name="create-external-stream-transact-sql"></a>CRIAR FLUXO EXTERNO (Transact-SQL)

O objeto STREAM EXTERNO tem um duplo propósito tanto de uma entrada como de saída. Pode ser usado como uma entrada para consultar dados de streaming de serviços de ingestão de eventos, tais como hubs De Evento Sou OIT ou usado como uma saída para especificar onde e como armazenar resultados de uma consulta de streaming.

Um STREAM EXTERNO também pode ser especificado e criado como uma saída e entrada para serviços como O Hub de Eventos ou armazenamento blob. Isto é para cenários de acorrentar em que uma consulta de streaming persiste resultados para o fluxo externo como saída e outra leitura de consulta de streaming a partir do mesmo fluxo externo que a entrada. 


## <a name="syntax"></a>Sintaxe

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
(column definition [,column definitions ] * ) --Used for input - optional 
WITH  
(  
  DATA_SOURCE = <data_source_name>, 
  LOCATION = <location_name>, 
  EXTERNAL_FILE_FORMAT = <external_file_format_name>, --Used for input - optional 
   
  INPUT_OPTIONS =  
    ‘CONSUMER_GROUP=<consumer_group_name>; 
    ‘TIME_POLICY=<time_policy>; 
    LATE_EVENT_TOLERANCE=<late_event_tolerance_value>; 
    OUT_OF_ORDER_EVENT_TOLERANCE=<out_of_order_tolerance_value> 
     
    /* Edge options */ 
  , 
  OUTPUT_OPTIONS =  
    ‘REJECT_POLICY=<reject_policy>; 
    MINIMUM_ROWS=<row_value>; 
    MAXIMUM_TIME=<time_value_minutes>; 
    PARTITION_KEY_COLUMN=<partition_key_column_name>; 
    PROPERTY_COLUMNS=(); 
    SYSTEM_PROPERTY_COLUMNS=(); 
    PARTITION_KEY=<partition_key_name>; 
    ROW_KEY=<row_key_name>; 
    BATCH_SIZE=<batch_size_value>; 
    MAXIMUM_BATCH_COUNT=<batch_value>;  
    STAGING_AREA=<blob_data_source>’ 
     
    /* Edge options */ TAGS=<tag_column_value> 
); 
```


## <a name="arguments"></a>Argumentos


- [FONTE DE DADOS EXTERNOS](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [FORMATO DE ARQUIVO EXTERNO](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **LOCALIZAÇÃO**: Especifica o nome dos dados reais ou localização na fonte de dados. No caso de um objeto de fluxo Edge Hub ou Kafka, a localização especifica o nome do tópico Edge Hub ou Kafka para ler ou escrever.
- **INPUT_OPTIONS**: Especificar opções como pares de valor-chave para serviços como O Evento e Hubs IOT que são inputs para consultas de streaming
    - CONSUMER_GROUP: Os Hubs de Eventos e IoT limitam o número de leitores dentro de um grupo de consumidores (a 5). Deixar este campo vazio utilizará o grupo de consumidores "$Default".
      - Aplica-se aos Hubs de Eventos e IOT
    - TIME_POLICY: Descreve se deve abandonar os eventos ou ajustar o tempo do evento quando eventos tardios ou fora de ordem passam o seu valor de tolerância.
      - Aplica-se aos Hubs de Eventos e IOT
    - LATE_EVENT_TOLERANCE: O atraso máximo aceitável. O atraso representa a diferença entre a marca ção do evento e o relógio do sistema.
      - Aplica-se aos Hubs de Eventos e IOT
    - OUT_OF_ORDER_EVENT_TOLERANCE: Os eventos podem chegar fora de ordem depois de terem feito a viagem desde a entrada até à consulta de streaming. Estes eventos podem ser aceites como está, ou pode optar por fazer uma pausa por um determinado período para os reencomendar.
      - Aplica-se aos Hubs de Eventos e IOT
- **OUTPUT_OPTIONS**: Especificar opções como pares de valor-chave para serviços suportados que são saídas para consultas de streaming 
  - REJECT_POLICY: DROP [ DROP ] RETRY Species as políticas de tratamento de erros de dados quando ocorrem erros de conversão de dados. 
    - Aplica-se a todas as saídas suportadas 
  - MINIMUM_ROWS:  
    Filas mínimas exigidas por lote escrito a uma saída. Para o Parquet, cada lote criará um novo ficheiro. 
    - Aplica-se a todas as saídas suportadas 
  - MAXIMUM_TIME:  
    Tempo máximo de espera por lote. Após este tempo, o lote será escrito à saída mesmo que o requisito mínimo das linhas não seja cumprido. 
    - Aplica-se a todas as saídas suportadas 
  - PARTITION_KEY_COLUMN:  
    A coluna que é usada para a chave de partição. 
    - Aplica-se ao tópico do Hub de Eventos e do Autocarro de Serviço 
  - PROPERTY_COLUMNS:  
    Uma lista separada da vírvia dos nomes das colunas de saída que serão anexadas a mensagens como propriedades personalizadas se fornecidas.  
    - Aplica-se ao tópico e fila do Event Hub e do Ônibus de Serviço 
  - SYSTEM_PROPERTY_COLUMNS:  
    Uma coleção formatada jSON de nome/valor par de nomes de propriedade do sistema e colunas de saída a serem povoadas em mensagens de ônibus de serviço. por exemplo{ "MessageId": "column1", "PartitionKey": "column2"} 
    - Aplica-se ao tópico e fila do ônibus de serviço 
  - PARTITION_KEY:  
    O nome da coluna de saída que contém a chave de partição. A chave de partição é um identificador único para a partição dentro de uma determinada tabela que forma a primeira parte da chave primária de uma entidade. É um valor de cadeia que pode ter até 1 KB de tamanho. 
    - Aplica-se ao armazenamento de mesa e à função Azure 
  - ROW_KEY:  
    O nome da coluna de saída que contém a tecla da linha. A chave da linha é um identificador único para uma entidade dentro de uma determinada partição. Forma a segunda parte da chave principal de uma entidade. A chave da linha é um valor de cadeia que pode ter até 1 KB de tamanho. 
    - Aplica-se ao armazenamento de mesa e à função Azure 
  - BATCH_SIZE:  
    Isto representa o número de transações para armazenamento de mesa onde o máximo pode ir até 100 registos. Para as Funções Azure, isto representa o tamanho do lote em bytes enviados para a função por chamada - o padrão é de 256 kB. 
    - Aplica-se ao armazenamento de mesa e à função Azure 
  - MAXIMUM_BATCH_COUNT:  
    Número máximo de eventos enviados para a função por chamada para função Azure - predefinido é de 100. Para a Base de Dados SQL, isto representa o número máximo de registos enviados com cada transação de inserção a granel - o padrão é de 10.000. 
    - Aplica-se à Base de Dados SQL e à função Azure 
  - STAGING_AREA: OBJETO DE FONTE DE DADOS EXTERNOs ao Armazenamento de Blob A área de preparação para ingestão de dados de alta suposição no Armazém de Dados SQL 
    - Aplica-se ao Armazém de Dados SQL


## <a name="examples"></a>Exemplos

### <a name="example-1---edgehub"></a>Exemplo 1 - EdgeHub

Tipo: Entrada ou Saída<br>
Parâmetros:
- Entrada ou Saída
  - Alias 
  - Formato de serialização de eventos 
  - Encoding 
- Entrada apenas: 
  - Tipo de compressão de eventos 

Sintaxe:

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = 'JSON', 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   EXTERNAL_FILE_FORMAT = myFileFormat, 
   LOCATION = ‘<mytopicname>’, 
   OUTPUT_OPTIONS =   
     ‘REJECT_TYPE: Drop’ 
);
```


### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>Exemplo 2 - Base de Dados Azure SQL, Borda SQL Azur, Servidor SQL

Tipo: Saída<br>
Parâmetros:
- Alias de saída  
- Base de Dados (necessária para base de dados SQL) 
- Servidor (necessário para base de dados SQL) 
- Nome de utilizador (necessário para base de dados SQL) 
- Palavra-passe (necessária para base de dados SQL) 
- Tabela 
- Fundir todas as divisórias de entrada num único esquema de repartição de escrita ou herdade de etapa ou entrada de consulta anterior (necessária para base de dados SQL) 
- Contagem máxima de lote 

Sintaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Database/Edge 
CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = ‘<DatabaseName>.<SchemaName>.<TableName>’ 
   --Note: If table is container in the database, <TableName> should be sufficient 
   --Note: Do not need external file format in this case 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop 
); 
```

### <a name="example-3---kafka"></a>Exemplo 3 - Kafka

Tipo: Entrada<br>
Parâmetros:

- Servidor de botas Kafka 
- Nome tópico de Kafka 
- Contagem de divisórias de origem 

Sintaxe:

```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = ' kafka://<kafkaserver>:<ipaddress>’, 
  CREDENTIAL = kafkaCredName 
 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = ‘<KafkaTopicName>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 5’ 
); 
```

### <a name="example-4---blob-storage"></a>Exemplo 4 - Armazenamento blob

Tipo: Entrada ou Saída<br>
Parâmetros:
- Entrada ou saída:
  - Alias 
  - Conta de armazenamento 
  - Chave da conta de armazenamento 
  - Contentor 
  - Padrão do caminho 
  - Formato de data 
  - Formato de tempo 
  - Formato de serialização de eventos 
  - Encoding 
- Entrada apenas: 
  - Divisórias (entrada) 
  - Tipo de compressão de eventos (entrada) 
- Apenas saída: 
  - Filas mínimas (saída) 
  - Tempo máximo (saída) 
  - Modo de autenticação (saída) 

Sintaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = 'AccountKey'; 
 
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredNameMSI 
WITH IDENTITY = 'Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyBlobStorage_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATE_FORMAT = 'YYYY/MM/DD HH', --Both Date and Time format 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyBlobStorage_tweets, 
    LOCATION = ‘<path_pattern>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 1’, 
  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: (), 
      MINUMUM_ROWS: 100000, 
      MAXIMUM_TIME: 60’ 
); 
```

### <a name="example-5---event-hub"></a>Exemplo 5 - Centro de Eventos

Tipo: Entrada ou Saída<br>
Parâmetros:
- Entrada ou saída:
  - Alias 
  - Espaço de nome de ônibus de serviço 
  - O nome do hub de eventos 
  - Nome da política do Hub de Eventos 
  - Chave política do Hub de Eventos 
  - Formato de serialização de eventos 
  - Encoding 
- Entrada apenas: 
  - Grupo de consumidor do Hub de Eventos 
  - Tipo de compressão de eventos 
- Apenas saída: 
  - Coluna de chaves de partição 
  - Colunas de propriedades 

Sintaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL eventHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyEventHub_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyEventHub_tweets, 
    LOCATION = ‘<topicname>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
 
    INPUT_OPTIONS =  
      ‘CONSUMER_GROUP: FirstConsumerGroup’, 
          
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: ()’ 
);
```

### <a name="example-6---iot-hub"></a>Exemplo 6 - Hub IoT

Tipo: Entrada<br>
Parâmetros:

- Alias de entrada 
- IoT Hub 
- Ponto Final 
- Nome da política de acesso partilhado 
- Chave da política de acesso partilhado 
- Grupo de consumidores 
- Formato de serialização de eventos 
- Encoding 
- Tipo de compressão de eventos 

Sintaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL IoTHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyIoTHub_tweets 
WITH 
(     
  LOCATION = ' iot://iot_hub_name.azure-devices.net’, 
  CREDENTIAL = IoTHubCredName 
);  

CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyIoTHub_tweets, 
    LOCATION = ‘<name>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘ENDPOINT: Messaging, 
      CONSUMER_GROUP: ‘FirstConsumerGroup’ 
); 
```

### <a name="example-7---azure-synapse-analytics-formerly-sql-data-warehouse"></a>Exemplo 7 - Azure Synapse Analytics (anteriormente SQL Data Warehouse)

Tipo: Saída<br>
Parâmetros:
- Alias de saída 
- Base de Dados 
- Servidor 
- Nome de utilizador 
- Palavra-passe 
- Tabela 
- Área de preparação (para COPY) 

Sintaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTable 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
CREATE EXTERNAL STREAM MySQLTableOutput 
WITH ( 
   DATA_SOURCE = MyTargetSQLTable, 
   LOCATION = ‘<TableName>’ 
   --Note: Do not need external file format in this case 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     STAGING_AREA: staging_area_data_source’, 
); 
```


### <a name="example-8---table-storage"></a>Exemplo 8 - Armazenamento de mesa

Tipo: Saída<br>
Parâmetros:
- Alias de saída 
- Conta de armazenamento 
- Chave da conta de armazenamento 
- Nome da tabela 
- Chave de partição 
- Chave de linha 
- Tamanho do lote 

Sintaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL TableStorageCredName 
WITH IDENTITY = ‘Storage account Key’, 
SECRET = '<storage_account_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY: <column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


### <a name="example-9---service-bus-topic-same-properties-as-queue"></a>Exemplo 9 - Tópico de Autocarro de Serviço (as mesmas propriedades que a fila)

Tipo: Saída<br>
Parâmetros:
- Alias de saída 
- Espaço de nome de ônibus de serviço 
- Nome tópico 
- Nome da política do tópico 
- Chave da política do tópico 
- Colunas de propriedades 
- Colunas de propriedade do sistema 
- Formato de serialização de eventos 
- Encoding 

Sintaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL serviceBusCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyServiceBus_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = serviceBusCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM MyServiceBusOutput 
WITH ( 
   DATA_SOURCE = MyServiceBus_tweets, 
   LOCATION = ‘<topic_name>’, 
   EXTERNAL_FILE_FORMAT = myFileFormat 
       OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY_COLUMN: , 
     PROPERTY_COLUMNS: (), 
     SYSTEM_PROPERTY_COLUMNS: ()’ 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
           
); 
```


### <a name="example-10---cosmos-db"></a>Exemplo 10 - Cosmos DB

Tipo: Saída<br>
Parâmetros:
- Alias de saída 
- ID da Conta 
- Chave de conta 
- Base de Dados 
- Nome do contentor 
- Id documento 


Sintaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL cosmosDBCredName 
WITH IDENTITY = ‘Storage Account Key’, 
SECRET = '<accountKey>'; 
 
CREATE EXTERNAL DATA SOURCE MyCosmosDB_tweets 
WITH 
(     
  LOCATION = ' cosmosdb://accountid.documents.azure.com:443/ database’, 
  CREDENTIAL = cosmosDBCredName 
); 
 
CREATE EXTERNAL STREAM MyCosmosDBOutput 
WITH ( 
   DATA_SOURCE = MyCosmosDB_tweets, 
   LOCATION = ‘<container/documentID>’ 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop', 
     --Note: Do not need external file format in this case 
          
);
```



### <a name="example-11---power-bi"></a>Exemplo 11 - Power BI

Tipo: Saída<br>
Parâmetros:
- Alias de saída 
- Nome do conjunto de dados 
- Nome da tabela 


Sintaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL PBIDBCredName 
WITH IDENTITY = ‘Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyPbi_tweets 
WITH 
( 
  LOCATION = 'pbi://dataset/’, 
  CREDENTIAL = PBIDBCredName 
 
); 
 
CREATE EXTERNAL STREAM MyPbiOutput 
WITH ( 
   DATA_SOURCE = MyPbi_tweets, 
   LOCATION = 'tableName', 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop' 
        
);
```

### <a name="example-12---azure-function"></a>Exemplo 12 - Função Azure

Tipo: Saída<br>
Parâmetros:
- Function app 
- Função 
- Chave 
- Tamanho máximo do lote 
- Contagem máxima de lote 

Sintaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL AzureFunctionCredName 
WITH IDENTITY = ‘Function Key’, 
SECRET = '<function_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: 'Drop'      
     PARTITION_KEY: ‘<column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


## <a name="see-also"></a>Ver também

- [ALTER FLUXO EXTERNO (Transact-SQL)](alter-external-stream-transact-sql.md) 
- [FLUXO EXTERNO DROP (Transact-SQL)](drop-external-stream-transact-sql.md) 

