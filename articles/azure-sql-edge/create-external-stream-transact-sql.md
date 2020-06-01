---
title: CRIAR FLUXO EXTERNO (Transact-SQL) - Aresta SQL Azure (Pré-visualização)
description: Conheça a declaração DE FLUXO EXTERNO CREATE em Azure SQL Edge (Pré-visualização)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 9e1f672a62ee7687fec9cea96ca03240c893ba95
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233336"
---
# <a name="create-external-stream-transact-sql"></a>CRIAR FLUXO EXTERNO (Transact-SQL)

O objeto STREAM EXTERNO tem um duplo propósito tanto de entrada como de saída. Pode ser usado como uma entrada para consultar dados de streaming de serviços de ingestão de eventos, tais como hubs Azure Event ou IoT ou usado como uma saída para especificar onde e como armazenar resultados de uma consulta de streaming.

Um FLUXO EXTERNO também pode ser especificado e criado como uma saída e entrada para serviços como o Event Hub ou o armazenamento blob. Isto é para cenários de acorrentamento em que uma consulta de streaming está persistindo resulta no fluxo externo como saída e outra consulta de streaming a partir do mesmo fluxo externo que a entrada. 


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
- [FORMATO DE FICHEIRO EXTERNO](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **LOCALIZAÇÃO**: Especifica o nome para os dados reais ou localização na fonte de dados. No caso de um edge hub ou objeto de fluxo Kafka, a localização especifica o nome do tópico Edge Hub ou Kafka para ler ou escrever para.
- **INPUT_OPTIONS**: Especifique as opções como pares de valor-chave para serviços como Os Hubs de Evento e IOT que são entradas para consultas de streaming
    - CONSUMER_GROUP: Os Hubs de Eventos e IoT limitam o número de leitores dentro de um grupo de consumidores (a 5). Deixar este campo vazio utilizará o grupo de consumidores "$Default".
      - Aplica-se a Centros de Eventos e IOT
    - TIME_POLICY: Descreve se deve deixar cair eventos ou ajustar o tempo do evento quando eventos tardios ou eventos fora de ordem passam o seu valor de tolerância.
      - Aplica-se a Centros de Eventos e IOT
    - LATE_EVENT_TOLERANCE: O máximo de atraso aceitável. O atraso representa a diferença entre a marca de tempo do evento e o relógio do sistema.
      - Aplica-se a Centros de Eventos e IOT
    - OUT_OF_ORDER_EVENT_TOLERANCE: Os eventos podem chegar fora de ordem depois de terem feito a viagem desde a entrada até à consulta de streaming. Estes eventos podem ser aceites como está, ou pode optar por uma pausa por um período definido para reordená-los.
      - Aplica-se a Centros de Eventos e IOT
- **OUTPUT_OPTIONS**: Especifique as opções como pares de valor-chave para serviços suportados que são saídas para consultas de streaming 
  - REJECT_POLICY: DROP / ReTRY Espécies as políticas de tratamento de erros de dados quando ocorrem erros de conversão de dados. 
    - Aplica-se a todas as saídas suportadas 
  - MINIMUM_ROWS:  
    Filas mínimas necessárias por lote escrito para uma saída. Para o Parquet, cada lote criará um novo ficheiro. 
    - Aplica-se a todas as saídas suportadas 
  - MAXIMUM_TIME:  
    Tempo máximo de espera por lote. Após este período, o lote será escrito para a saída mesmo que o requisito de linhas mínimas não seja cumprido. 
    - Aplica-se a todas as saídas suportadas 
  - PARTITION_KEY_COLUMN:  
    A coluna que é usada para a chave de partição. 
    - Aplica-se ao tema Event Hub e Service Bus 
  - PROPERTY_COLUMNS:  
    Uma lista separada por vírgula dos nomes das colunas de saída que serão anexadas a mensagens como propriedades personalizadas se fornecidas.  
    - Aplica-se ao tópico e à fila do Event Hub e do Service Bus 
  - SYSTEM_PROPERTY_COLUMNS:  
    Uma coleção com formato JSON de nome/pars de valor de nomes de propriedades do sistema e colunas de saída a serem povoadas em mensagens de Service Bus. por exemplo, {"MessageId": "coluna1", "PartitionKey": "coluna2"} 
    - Aplica-se ao tópico e fila do Service Bus 
  - PARTITION_KEY:  
    O nome da coluna de saída que contém a chave de partição. A chave de partição é um identificador único para a partição dentro de uma dada tabela que forma a primeira parte da chave primária de uma entidade. É um valor de cadeia que pode ter até 1 KB de tamanho. 
    - Aplica-se ao armazenamento de mesa e função Azure 
  - ROW_KEY:  
    O nome da coluna de saída que contém a tecla de linha. A chave de linha é um identificador único para uma entidade dentro de uma determinada partição. É a segunda parte da chave primária de uma entidade. A tecla de linha é um valor de corda que pode ter até 1 KB de tamanho. 
    - Aplica-se ao armazenamento de mesa e função Azure 
  - BATCH_SIZE:  
    Isto representa o número de transações para armazenamento de mesas onde o máximo pode ir até 100 registos. Para as Funções Azure, isto representa o tamanho do lote nos bytes enviados para a função por chamada - o padrão é de 256 kB. 
    - Aplica-se ao armazenamento de mesa e função Azure 
  - MAXIMUM_BATCH_COUNT:  
    O número máximo de eventos enviados para a função por chamada para a função Azure - o padrão é de 100. Para a Base de Dados SQL, este número representa o número máximo de registos enviados com cada transação de inserção a granel - o padrão é de 10.000. 
    - Aplica-se à base de dados SQL e à função Azure 
  - STAGING_AREA: DADOS EXTERNOS OBJETO FONTE DE DADOS para Armazenamento blob A área de paragem para ingestão de dados de alto rendimento no SQL Data Warehouse 
    - Aplica-se ao Armazém de Dados SQL


## <a name="examples"></a>Exemplos

### <a name="example-1---edgehub"></a>Exemplo 1 - EdgeHub

Tipo: Entrada ou saída<br>
Parâmetros:
- Entrada ou saída
  - Alias 
  - Formato de serialização de eventos 
  - Codificação 
- Apenas entrada: 
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


### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>Exemplo 2 - Base de Dados Azure SQL, Aresta Azure SQL, Servidor SQL

Tipo: Saída<br>
Parâmetros:
- Alias de saída  
- Base de dados (necessária para base de dados SQL) 
- Servidor (obrigatório para base de dados SQL) 
- Nome de utilizador (obrigatório para base de dados SQL) 
- Palavra-passe (necessária para base de dados SQL) 
- Tabela 
- Fundir todas as divisórias de entrada num único esquema de partilha de escrita ou de herança de passo ou entrada de consulta anterior (necessário para a Base de Dados SQL) 
- Contagem de lotes max 

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

- Servidor de bootstrap kafka 
- Nome do tópico kafka 
- Contagem de partição de origem 

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

Tipo: Entrada ou saída<br>
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
  - Codificação 
- Apenas entrada: 
  - Divisórias (entrada) 
  - Tipo de compressão de evento (entrada) 
- Apenas saída: 
  - Linhas mínimas (saída) 
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

Tipo: Entrada ou saída<br>
Parâmetros:
- Entrada ou saída:
  - Alias 
  - Espaço de nome de ônibus de serviço 
  - O nome do hub de eventos 
  - Nome da política do Hub de Eventos 
  - Chave política do Centro de Eventos 
  - Formato de serialização de eventos 
  - Codificação 
- Apenas entrada: 
  - Grupo de consumidor do Hub de Eventos 
  - Tipo de compressão de eventos 
- Apenas saída: 
  - Coluna-chave de partição 
  - Colunas de propriedade 

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

### <a name="example-6---iot-hub"></a>Exemplo 6 - IOT Hub

Tipo: Entrada<br>
Parâmetros:

- Alias de entrada 
- IoT Hub 
- Ponto Final 
- Nome da política de acesso partilhado 
- Chave de política de acesso compartilhado 
- Grupo de consumidores 
- Formato de serialização de eventos 
- Codificação 
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
- Server 
- Nome de utilizador 
- Palavra-passe 
- Tabela 
- Área de paragem (para COPY) 

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


### <a name="example-9---service-bus-topic-same-properties-as-queue"></a>Exemplo 9 - Tópico de autocarro de serviço (as mesmas propriedades da fila)

Tipo: Saída<br>
Parâmetros:
- Alias de saída 
- Espaço de nome de ônibus de serviço 
- Nome tópico 
- Nome da política de tópicos 
- Chave de política de tópicos 
- Colunas de propriedade 
- Colunas de propriedade do sistema 
- Formato de serialização de eventos 
- Codificação 

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
- ID do documento 


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

### <a name="example-12---azure-function"></a>Exemplo 12 - Função azul

Tipo: Saída<br>
Parâmetros:
- Function app 
- Função 
- Chave 
- Tamanho do lote máximo 
- Contagem de lotes max 

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

