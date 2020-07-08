---
title: Criar um trabalho de streaming T-SQL em Azure SQL Edge (Preview)
description: Saiba a criação de trabalhos stream Analytics em Azure SQL Edge (Preview).
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: fc6ab2c9c844350e83674ed96a0e79289c7f5b43
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255420"
---
# <a name="create-an-azure-stream-analytics-job-in-azure-sql-edge-preview"></a>Criar um trabalho Azure Stream Analytics em Azure SQL Edge (Preview) 

Este artigo explica como criar um trabalho de streaming T-SQL em Azure SQL Edge (Preview). Cria-se a entrada externa e os objetos de saída e, em seguida, define-se a consulta de trabalho em streaming como parte da criação de emprego em streaming.

> [!NOTE]
> Para ativar a funcionalidade de streaming T-SQL no Azure SQL Edge, ative o TF 11515 como opção de arranque ou utilize o comando [DBCC TRACEON.]( https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) Para obter mais informações sobre como permitir o rastreio de bandeiras utilizando um ficheiro mssql.conf, consulte [Configure utilizando um ficheiro mssql.conf](configure.md#configure-by-using-an-mssqlconf-file).

## <a name="configure-the-external-stream-input-and-output-objects"></a>Configure a entrada externa e os objetos de saída

O streaming T-SQL utiliza a funcionalidade de fonte de dados externa do SQL Server para definir as fontes de dados associadas às entradas e saídas de fluxo externos do trabalho de streaming. Utilize os seguintes comandos T-SQL para criar uma entrada ou objeto de saída externo:

- [Criar Formato de Ficheiro Externo (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql)

- [Criar Origem de Dados Externa (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)

- [CRIAR FLUXO EXTERNO (Transact-SQL)](#example-create-an-external-stream-object-to-azure-sql-database)

Além disso, se a Base de Dados Azure SQL Edge, SQL Server ou Azure SQL Database for utilizada como fluxo de saída, necessita da [CRIA DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql). Este comando T-SQL define as credenciais para aceder à base de dados.

### <a name="supported-input-and-output-stream-data-sources"></a>Fontes de dados de entrada e fluxo de saída suportadas

A Azure SQL Edge suporta atualmente apenas as seguintes fontes de dados como entradas e saídas de fluxo.

| Tipo de fonte de dados | Input | Saída | Descrição |
|------------------|-------|--------|------------------|
| Hub Azure IoT Edge | S | S | Fonte de dados para ler e escrever dados de streaming para um hub Azure IoT Edge. Para mais informações, consulte [o IoT Edge Hub.](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)|
| SQL Database | N | S | Ligação de fonte de dados para escrever dados de streaming para a Base de Dados SQL. A base de dados pode ser uma base de dados local em Azure SQL Edge, ou uma base de dados remota no SQL Server ou na Base de Dados Azure SQL.|
| Armazenamento de Blobs do Azure | N | S | Fonte de dados para escrever dados para uma bolha numa conta de armazenamento Azure. |
| Kafka | S | N | Fonte de dados para ler dados de streaming de um tópico kafka. Atualmente, este adaptador apenas se encontra disponível para versões Intel ou AMD do Azure SQL Edge. Não está disponível para a versão ARM64 do Azure SQL Edge.|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>Exemplo: Criar um objeto de entrada/saída de fluxo externo para o hub Azure IoT Edge

O exemplo a seguir cria um objeto de fluxo externo para o hub Azure IoT Edge. Para criar uma fonte externa de dados de entrada/saída para o hub Azure IoT Edge, primeiro é necessário criar um formato de ficheiro externo para a disposição dos dados que estão a ser lidos ou escritos também.

1. Crie um formato de ficheiro externo do tipo JSON.

    ```sql
    Create External file format InputFileFormat
    WITH (  
       format_type = JSON,
    )
    go
    ```

2. Crie uma fonte de dados externa para o hub Azure IoT Edge. O seguinte script T-SQL cria uma ligação de fonte de dados a um hub IoT Edge que funciona no mesmo anfitrião Docker que Azure SQL Edge.

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput WITH (
    LOCATION = 'edgehub://'
    )
    go
    ```

3. Crie o objeto de fluxo externo para o hub Azure IoT Edge. O seguinte script T-SQL cria um objeto de fluxo para o hub IoT Edge. No caso de um objeto de fluxo de hub IoT Edge, o parâmetro LOCATION é o nome do tópico do hub IoT Edge ou canal a ser lido ou escrito.

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

### <a name="example-create-an-external-stream-object-to-azure-sql-database"></a>Exemplo: Criar um objeto de fluxo externo para a Base de Dados Azure SQL

O exemplo a seguir cria um objeto de fluxo externo para a base de dados local em Azure SQL Edge. 

1. Crie uma chave principal na base de dados. Isto é necessário para encriptar o segredo credencial.

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. Crie uma credencial de âmbito de base de dados para aceder à fonte do SqL Server. O exemplo a seguir cria uma credencial para a fonte de dados externo, com identidade = nome de utilizador, e SECRET = palavra-passe.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. Criar uma fonte de dados externa com CREATE EXTERNAL DATA SOURCE. O seguinte exemplo:

    * Cria uma fonte de dados externa chamada *LocalSQLOutput*.
    * Identifica a fonte de dados externa (LOCALIZAÇÃO = <vendor> ' <server> <port> []'). No exemplo, aponta para um caso local de Azure SQL Edge.
    * Usa a credencial criada anteriormente.

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
    LOCATION = 'sqlserver://tcp:.,1433'
    ,CREDENTIAL = SQLCredential
    )
    go
    ```

4. Crie o objeto de fluxo externo. O exemplo a seguir cria um objeto de fluxo externo que aponta para um *dbo de mesa. Medidas de temperatura,* na base de dados *MySQLDatabase*.

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements WITH (
    DATA_SOURCE = LocalSQLOutput,
    LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>Crie o trabalho de streaming e as consultas de streaming

Utilize o `sys.sp_create_streaming_job` procedimento de armazenação do sistema para definir as consultas de streaming e criar o trabalho de streaming. O `sp_create_streaming_job` procedimento armazenado requer os seguintes parâmetros:

- `job_name`: O nome do trabalho de streaming. Os nomes de trabalho em streaming são únicos em todos os casos.
- `statement`: [Stream Analytics Consultas](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)de consulta de linguagem baseadas em linguagem.

O exemplo a seguir cria um simples trabalho de streaming com uma consulta de streaming. Esta consulta lê as entradas do hub IoT Edge e escreve `dbo.TemperatureMeasurements` na base de dados.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

O exemplo a seguir cria um trabalho de streaming mais complexo com múltiplas consultas diferentes. Estas consultas incluem uma que usa a função incorporada `AnomalyDetection_ChangePoint` para identificar anomalias nos dados de temperatura.

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

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>Começar, parar, largar e monitorizar os trabalhos de streaming

Para iniciar um trabalho de streaming em Azure SQL Edge, executar o `sys.sp_start_streaming_job` procedimento armazenado. O procedimento armazenado requer o início do nome do trabalho de streaming, como entrada.

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

Para parar um trabalho de streaming, executar o `sys.sp_stop_streaming_job` procedimento armazenado. O procedimento armazenado requer que o nome do trabalho de streaming pare, como entrada.

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

Para deixar cair (ou apagar) um trabalho de streaming, executar o `sys.sp_drop_streaming_job` procedimento armazenado. O procedimento armazenado requer que o nome do trabalho de streaming caia, como entrada.

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

Para obter o estado atual de um trabalho de streaming, executar o `sys.sp_get_streaming_job` procedimento armazenado. O procedimento armazenado requer que o nome do trabalho de streaming caia, como entrada. Produz o nome e o estado atual do trabalho de streaming.

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

O trabalho de streaming pode ter qualquer um dos seguintes estatutos:

| Estado | Descrição |
|--------| ------------|
| Criado | O trabalho de streaming foi criado, mas ainda não foi iniciado. |
| A iniciar | O trabalho de streaming está na fase inicial. |
| Períodos | O trabalho de streaming está a funcionar, mas não há nenhuma entrada para processar. |
| Em processamento | O trabalho de streaming está a funcionar e está a processar entradas. Este estado indica um estado saudável para o trabalho de streaming. |
| Degradado | O trabalho de streaming está a funcionar, mas houve alguns erros não fatais durante o processamento de entradas. O trabalho de entrada continuará a funcionar, mas deixará cair as entradas que encontram erros. |
| Parada | O trabalho de streaming foi interrompido. |
| Falhou | O trabalho de streaming falhou. Isto é geralmente uma indicação de um erro fatal durante o processamento. |

## <a name="next-steps"></a>Próximos passos

- [Ver metadados associados a trabalhos de streaming em Azure SQL Edge (Preview)](streaming-catalog-views.md) 
- [Criar um fluxo externo](create-external-stream-transact-sql.md)
