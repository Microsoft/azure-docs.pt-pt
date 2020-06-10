---
title: Criar um trabalho de streaming T-SQL em Azure SQL Edge (Pré-visualização)
description: Saiba como criar empregos stream Analytics em Azure SQL Edge (Preview)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 4b09df3110907d58badda2c389b9ee39a9b02532
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636194"
---
# <a name="create-stream-analytics-job-in-azure-sql-edge-preview"></a>Criar trabalho stream analytics em Azure SQL Edge (Pré-visualização) 

Este artigo explica como criar um trabalho de streaming T-SQL em Azure SQL Edge (Preview). Para criar um trabalho de streaming em SQL Edge, são necessários os seguintes passos

1. Criar os objetos de entrada e saída de fluxo externos
2. Defina a consulta de trabalho em streaming como parte da criação de emprego em streaming.

> [!NOTE]
> Para ativar a funcionalidade de streaming T-SQL em Azure SQL Edge, ative o TF 11515 como opção de arranque ou utilize o comando [DBCC TRACEON.]( https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) Para obter mais informações sobre como permitir a utilização de bandeiras de traços utilizando o ficheiro mssql.conf, consulte [o Configure utilizando um ficheiro mssql.conf](configure.md#configure-by-using-an-mssqlconf-file). Este requisito será removido em futuras atualizações do Azure SQL Edge (Preview).

## <a name="configure-an-external-stream-input-and-output-object"></a>Configure um objeto de entrada e saída de fluxo externo

O Streaming T-SQL utiliza a funcionalidade de fonte de dados externa do SQL Server para definir as fontes de dados associadas às entradas e saídas de fluxo externos do trabalho de streaming. São necessários os seguintes comandos T-SQL para criar um objeto externo de entrada ou saída de fluxo.

[Criar Formato de Ficheiro Externo (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql)

[Criar Origem de Dados Externa (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)

[CRIAR FLUXO EXTERNO (Transact-SQL)](#example-create-an-external-stream-object-sql-database)

Além disso, no caso de o SQL Edge (ou SQL Server, Azure SQL) ser utilizado como fluxo de saída, o comando T-SQL [CREATE DATABASE SCOPED (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) é necessário para definir as credenciais para aceder à base de dados SQL.

### <a name="supported-input-and-output-stream-data-sources"></a>Fontes de dados de entrada e fluxo de saída suportadas

A Azure SQL Edge suporta atualmente apenas as seguintes fontes de dados como entradas e saídas de fluxo.

| Tipo de Origem de Dados | Input | Saída | Descrição |
|------------------|-------|--------|------------------|
| Azure IoT Edge Hub | S | S | Fonte de dados para ler/escrever dados de streaming para um Azure IoT Edge Hub. Para mais informações sobre o Azure IoT Edge Hub, consulte [o IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)|
| SQL Database | N | S | Ligação de fonte de dados para escrever dados de streaming para a Base de Dados SQL. A Base de Dados SQL pode ser uma base de dados sql edge local ou um servidor SQL remoto ou base de dados Azure SQL|
| Armazenamento de Blobs do Azure | N | S | Fonte de dados para escrever dados para uma bolha numa conta de armazenamento Azure. |
| Kafka | S | N | Fonte de dados para ler dados de streaming de um tópico kafka. Este adaptador está atualmente disponível apenas para a versão Intel/AMD do Azure SQL Edge e não está disponível para a versão ARM64 do SQL Edge.|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>Exemplo: Criar um objeto de entrada/saída de fluxo externo para o Azure IoT Edge Hub

O exemplo abaixo cria um objeto de fluxo externo para o Edge Hub. Para criar uma fonte externa de dados de entrada/saída para o Azure IoT Edge Hub, é necessário primeiro criar um Formato de Ficheiro Externo para o SQL compreender o layout dos dados que estão a ser lidos/escritos também.

1. Criar um formato de ficheiro externo com o tipo de formato JSON.

    ```sql
    Create External file format InputFileFormat
    WITH (  
       format_type = JSON,
    )
    go
    ```

2. Crie uma Fonte de Dados Externa para o Hub IoT Edge. O script T-SQL abaixo cria uma ligação de fonte de dados a um hub Edge que funciona no mesmo anfitrião do estivador que o SQL Edge.

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput WITH (
    LOCATION = 'edgehub://'
    )
    go
    ```

3. Crie o objeto de fluxo externo para o Hub IoT Edge. O Script T-SQL abaixo cria um objeto de fluxo para o Edge Hub. No caso de um objeto de fluxo de hub edge, o parâmetro LOCATION é o nome do tópico/canal do hub de borda que está a ser lido ou escrito.

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors WITH (
    DATA_SOURCE = EdgeHubInput,
    FILE_FORMAT = InputFileFormat,
    LOCATION = N'TemperatureSensors',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    go
    ```

### <a name="example-create-an-external-stream-object-sql-database"></a>Exemplo: Criar uma base de dados SQL de objeto de fluxo externo

O exemplo abaixo cria um objeto de fluxo externo para a base de dados local SQL Edge. 

1. Crie uma chave principal na base de dados. Isto é necessário para encriptar o segredo credencial.

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. Crie uma credencial de âmbito de base de dados para aceder à fonte do SQL Server. O exemplo a seguir cria uma credencial para a fonte de dados externo com IDENTIDADE = 'username' e SECRET = 'password'.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. Criar uma fonte de dados externa com CREATE EXTERNAL DATA SOURCE. O seguinte exemplo:

    * Cria uma fonte de dados externa chamada LocalSQLOutput
    * Identifica a fonte de dados externa (LOCALIZAÇÃO = <vendor> ' <server> <port> []'). No exemplo, aponta para um caso local de SQL Edge.
    * Finalmente, o exemplo usa a credencial criada anteriormente.

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
    LOCATION = 'sqlserver://tcp:.,1433'
    ,CREDENTIAL = SQLCredential
    )
    go
    ```

4. Crie o objeto De corrente externa. O exemplo abaixo cria um objeto de fluxo externo que aponta para um *dbo de mesa. Medidas de temperatura* na base de dados *MySQLDatabase*.

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements WITH (
    DATA_SOURCE = LocalSQLOutput,
    LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>Crie o trabalho de streaming e as consultas de streaming

Utilize o procedimento **de sys.sp_create_streaming_job** de sistema armazenado para definir as consultas de streaming e criar o trabalho de streaming. O **procedimento sp_create_streaming_job** armazenado tem dois parâmetros

- job_name - Nome do trabalho de streaming. Os nomes de trabalho em streaming são únicos em todos os casos.
- statement - [Stream Analytics Consulta Desemisso](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?) baseado em declarações de consulta de streaming.

O exemplo abaixo cria um simples trabalho de streaming com uma consulta de streaming. Esta consulta lê as entradas do Edge Hub e escreve para o *dbo. Medidas de temperatura* na base de dados.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

O exemplo abaixo cria um trabalho de streaming mais complexo com múltiplas consultas diferentes, incluindo uma consulta que usa a função de AnomalyDetection_ChangePoint embutido para identificar anomalias nos dados de temperatura.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly,
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>Iniciar, parar, largar e monitorizar trabalhos de streaming

Para iniciar um trabalho de streaming em SQL Edge, execute o **procedimento sys.sp_start_streaming_job** armazenado. O procedimento armazenado requer o mesmo trabalho de streaming para iniciar, como entrada.

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

Para parar um trabalho de streaming em SQL Edge, execute o **procedimento sys.sp_stop_streaming_job** armazenado. O procedimento armazenado requer que o mesmo trabalho de streaming pare, como entrada.

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

Para deixar cair (ou eliminar) um trabalho de streaming em SQL Edge, execute o procedimento **sys.sp_drop_streaming_job** armazenado. O procedimento armazenado requer que o mesmo trabalho de streaming caia, como entrada.

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

Para obter o estado atual de um trabalho de streaming em SQL Edge, execute o procedimento **armazenado sys.sp_get_streaming_job.** O procedimento armazenado requer que o mesmo trabalho de streaming caia, uma vez que o nome e as saídas são o nome e o estado atual do trabalho de streaming.

```sql
exec sys.sp_get_streaming_job @name=N'StreamingJob1'
        WITH RESULT SETS
(
       (
       name nvarchar(256),
       status nvarchar(256)
       )
)
```

O trabalho de streaming pode estar em qualquer um dos seguintes estados

| Estado | Descrição |
|--------| ------------|
| Criado | O trabalho de streaming foi criado, mas ainda não foi iniciado |
| A iniciar | O trabalho de streaming está na fase inicial |
| Idle | O trabalho de streaming está em execução, no entanto não há nenhuma entrada para o processo |
| Em processamento | O trabalho de streaming está a funcionar e está a processar entradas. Este estado indica um estado saudável para o trabalho de streaming |
| Degradado | O trabalho de streaming está em execução, no entanto houve alguns erros de entrada/produção/des-serialização não fatal durante o processamento de entradas. O trabalho de entrada continuará a funcionar, mas vai deixar cair entradas que encontram erros |
| Parada | O trabalho de streaming foi interrompido. |
| Falhou | O trabalho de streaming falhou. Isto é geralmente uma indicação de um erro fatal durante o processamento |

## <a name="next-steps"></a>Passos Seguintes

- [Ver metadados associados a trabalhos de streaming em Azure SQL Edge (Preview)](streaming-catalog-views.md) 
- [Criar uma corrente externa](create-external-stream-transact-sql.md)
