---
title: CRIAR FLUXO EXTERNO (Transact-SQL) - Aresta Azure SQL
description: Conheça a declaração DE FLUXO EXTERNO CREATE em Azure SQL Edge
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 203abe2b6def478dc1747dd4ce638b5b62707612
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101659227"
---
# <a name="create-external-stream-transact-sql"></a>CRIAR FLUXO EXTERNO (Transact-SQL)

O objeto STREAM EXTERNO tem um duplo propósito tanto de entrada como de fluxo de saída. Pode ser usado como uma entrada para consultar dados de streaming de serviços de ingestão de eventos, tais como Azure Event Hub, Azure IoT Hub (ou Edge Hub) ou Kafka ou pode ser usado como uma saída para especificar onde e como armazenar resultados de uma consulta de streaming.

Um FLUXO EXTERNO também pode ser especificado e criado como uma saída e entrada para serviços como o Event Hub ou o armazenamento blob. Isto facilita cenários de acorrentamento em que uma consulta de streaming está a persistir resulta no fluxo externo como saída e outra leitura de streaming a partir do mesmo fluxo externo que a entrada.

A Azure SQL Edge suporta atualmente apenas as seguintes fontes de dados como entradas e saídas de fluxo.

| Tipo de fonte de dados | Input | Saída | Description |
|------------------|-------|--------|------------------|
| Hub Azure IoT Edge | Y | Y | Fonte de dados para ler e escrever dados de streaming para um hub Azure IoT Edge. Para mais informações, consulte [o IoT Edge Hub.](../iot-edge/iot-edge-runtime.md#iot-edge-hub)|
| Base de Dados SQL | N | Y | Ligação de fonte de dados para escrever dados de streaming para a Base de Dados SQL. A base de dados pode ser uma base de dados local em Azure SQL Edge, ou uma base de dados remota no SQL Server ou na Base de Dados Azure SQL.|
| Kafka | Y | N | Fonte de dados para ler dados de streaming de um tópico kafka. O suporte kafka não está disponível para a versão ARM64 do Azure SQL Edge.|



## <a name="syntax"></a>Sintaxe

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
( <column_definition> [, <column_definition> ] * ) -- Used for Inputs - optional 
WITH  ( <with_options> )

<column_definition> ::=
  column_name <column_data_type>

<data_type> ::=
[ type_schema_name . ] type_name
    [ ( precision [ , scale ] | max ) ]

<with_options> ::=
  DATA_SOURCE = data_source_name, 
  LOCATION = location_name, 
  [FILE_FORMAT = external_file_format_name], --Used for Inputs - optional 
  [<optional_input_options>],
  [<optional_output_options>], 
  TAGS = <tag_column_value>

<optional_input_options> ::= 
  INPUT_OPTIONS = '[<Input_options_data>]'

<Input_option_data> ::= 
      <input_option_values> [ , <input_option_values> ]

<input_option_values> ::=
  PARTITIONS: [number_of_partitions]
  | CONSUMER_GROUP: [ consumer_group_name ] 
  | TIME_POLICY: [ time_policy ] 
  | LATE_EVENT_TOLERANCE: [ late_event_tolerance_value ] 
  | OUT_OF_ORDER_EVENT_TOLERANCE: [ out_of_order_tolerance_value ]
  
<optional_output_options> ::= 
  OUTPUT_OPTIONS = '[<output_option_data>]'

<output_option_data> ::= 
      <output_option_values> [ , <output_option_values> ]

<output_option_values> ::=
   REJECT_POLICY: [ reject_policy ] 
   | MINIMUM_ROWS: [ row_value ] 
   | MAXIMUM_TIME: [ time_value_minutes] 
   | PARTITION_KEY_COLUMN: [ partition_key_column_name ] 
   | PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | SYSTEM_PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | PARTITION_KEY: [ partition_key_name ] 
   | ROW_KEY: [ row_key_name ] 
   | BATCH_SIZE: [ batch_size_value ] 
   | MAXIMUM_BATCH_COUNT: [ batch_value ] 
   | STAGING_AREA: [ blob_data_source ]
 
<tag_column_value> ::= -- Reserved for Future Usage
); 
```

## <a name="arguments"></a>Argumentos

- [DATA_SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [FILE_FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **LOCALIZAÇÃO**: Especifica o nome para os dados reais ou localização na fonte de dados. 
   - Para objetos de fluxo Edge Hub ou Kafka, a localização especifica o nome do tópico Edge Hub ou Kafka para ler ou escrever para.
   - Para objetos de fluxo SQL (SQL Server, Azure SQL Database ou Azure SQL Edge) a localização especifica o nome da tabela. Se o fluxo for criado na mesma base de dados e esquema que a tabela de destino, basta o nome da tabela. Caso contrário, tem de qualificar totalmente (<database_name.schema_name.table_name) o nome da tabela.
   - Para a localização do objeto de fluxo de armazenamento Azure Blob refere-se ao padrão do caminho a utilizar dentro do recipiente blob. Para obter mais informações sobre esta funcionalidade, consulte (/articles/stream-analytics/stream-analytics-define-outputs.md#blob-storage-and-azure-data-lake-gen2)

- **INPUT_OPTIONS**: Especifique as opções como pares de valor-chave para serviços como Kafka, IoT Edge Hub que são entradas para consultas de streaming
    - PARTIÇÕES: Número de divisórias definidas para um tópico. O número máximo de divisórias que podem ser utilizadas é limitado a 32.
      - Aplica-se a Streams de Entrada Kafka
    - CONSUMER_GROUP: Os Hubs de Eventos e IoT limitam o número de leitores dentro de um grupo de consumidores (a 5). Deixar este campo vazio utilizará o grupo de consumidores "$Default".
      - Reservado para uso futuro. Não se aplica ao Azure SQL Edge.  
    - TIME_POLICY: Descreve se deve deixar cair eventos ou ajustar o tempo do evento quando eventos tardios ou eventos fora de ordem passam o seu valor de tolerância.
      - Reservado para uso futuro. Não se aplica ao Azure SQL Edge.
    - LATE_EVENT_TOLERANCE: O máximo de atraso aceitável. O atraso representa a diferença entre a marca de tempo do evento e o relógio do sistema.
      - Reservado para uso futuro. Não se aplica ao Azure SQL Edge.
    - OUT_OF_ORDER_EVENT_TOLERANCE: Os eventos podem chegar fora de ordem depois de terem feito a viagem desde a entrada até à consulta de streaming. Estes eventos podem ser aceites como está, ou pode optar por uma pausa por um período definido para reordená-los.
      - Reservado para uso futuro. Não se aplica ao Azure SQL Edge.
        
- **OUTPUT_OPTIONS**: Especifique as opções como pares de valor-chave para serviços suportados que são saídas para consultas de streaming 
  - REJECT_POLICY: | DROP ReTRY Espécies as políticas de tratamento de erros de dados quando ocorrem erros de conversão de dados. 
    - Aplica-se a todas as saídas suportadas 
  - MINIMUM_ROWS:  
    Filas mínimas necessárias por lote escrito para uma saída. Para o Parquet, cada lote criará um novo ficheiro. 
    - Aplica-se a todas as saídas suportadas 
  - MAXIMUM_TIME:  
    Tempo máximo de espera em minutos por lote. Após este período, o lote será escrito para a saída mesmo que o requisito de linhas mínimas não seja cumprido. 
    - Aplica-se a todas as saídas suportadas 
  - PARTITION_KEY_COLUMN:  
    A coluna que é usada para a chave de partição. 
    - Reservado para uso futuro. Não se aplica ao Azure SQL Edge.
  - PROPERTY_COLUMNS:  
    Uma lista separada por vírgula dos nomes das colunas de saída que serão anexadas a mensagens como propriedades personalizadas se fornecidas.  
    - Reservado para uso futuro. Não se aplica ao Azure SQL Edge. 
  - SYSTEM_PROPERTY_COLUMNS:  
    Uma coleção com formato JSON de nome/pars de valor de nomes de propriedades do sistema e colunas de saída a serem povoadas em mensagens de Service Bus. por exemplo, {"MessageId": "coluna1", "PartitionKey": "coluna2"} 
    - Reservado para uso futuro. Não se aplica ao Azure SQL Edge. 
  - PARTITION_KEY:  
    O nome da coluna de saída que contém a chave de partição. A chave de partição é um identificador único para a partição dentro de uma dada tabela que forma a primeira parte da chave primária de uma entidade. É um valor de cadeia que pode ter até 1 KB de tamanho. 
    - Reservado para uso futuro. Não se aplica ao Azure SQL Edge.
  - ROW_KEY:  
    O nome da coluna de saída que contém a tecla de linha. A chave de linha é um identificador único para uma entidade dentro de uma determinada partição. É a segunda parte da chave primária de uma entidade. A tecla de linha é um valor de corda que pode ter até 1 KB de tamanho. 
    - Reservado para uso futuro. Não se aplica ao Azure SQL Edge.
  - BATCH_SIZE:  
    Isto representa o número de transações para armazenamento de mesas onde o máximo pode ir até 100 registos. Para as Funções Azure, isto representa o tamanho do lote nos bytes enviados para a função por chamada - o padrão é de 256 kB. 
    - Reservado para uso futuro. Não se aplica ao Azure SQL Edge. 
  - MAXIMUM_BATCH_COUNT:  
    O número máximo de eventos enviados para a função por chamada para a função Azure - o padrão é de 100. Para a Base de Dados SQL, este número representa o número máximo de registos enviados com cada transação de inserção a granel - o padrão é de 10.000. 
    - Aplica-se a todas as saídas baseadas em SQL 
  - STAGING_AREA: DADOS EXTERNOS FONTE DE DADOS Objeto ao Armazenamento blob A área de paragem para ingestão de dados de alto rendimento em Azure Synapse Analytics 
    - Reservado para uso futuro. Não se aplica ao Azure SQL Edge.


## <a name="examples"></a>Exemplos

### <a name="example-1---edgehub"></a>Exemplo 1 - EdgeHub

Tipo: Entrada ou saída<br>

Sintaxe:

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = JSON, 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   FILE_FORMAT = myFileFormat, 
   LOCATION = '<mytopicname>', 
   OUTPUT_OPTIONS =   
     'REJECT_TYPE: Drop'
);
```

### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>Exemplo 2 - Base de Dados Azure SQL, Aresta Azure SQL, Servidor SQL

Tipo: Saída<br>

Sintaxe:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>', 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = '<my_server_name>.database.windows.net', 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>', 
  CREDENTIAL = SQLCredName 
); 

CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = '<DatabaseName>.<SchemaName>.<TableName>' ,
   --Note: If table is contained in the database, <TableName> should be sufficient 
    OUTPUT_OPTIONS =  
      'REJECT_TYPE: Drop'
); 
```

### <a name="example-3---kafka"></a>Exemplo 3 - Kafka

Tipo: Entrada<br>

Sintaxe:
```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = 'kafka://<kafkaserver>:<ipaddress>', 
  CREDENTIAL = kafkaCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = JSON, 
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
); 

CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = '<KafkaTopicName>', 
    FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      'PARTITIONS: 5'
); 
```

## <a name="see-also"></a>Ver também

- [FLUXO EXTERNO DROP (Transact-SQL)](drop-external-stream-transact-sql.md)
