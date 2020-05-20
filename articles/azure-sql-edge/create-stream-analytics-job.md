---
title: Crie um trabalho de streaming T-SQL em Azure SQL Edge (Pré-visualização)
description: Saiba sobre a criação de empregos stream analytics em Azure SQL Edge (Pré-visualização)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7db7f9548a3daa86a53dd37fbe088661e8b7b17e
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83685169"
---
# <a name="create-stream-analytics-job-in-azure-sql-edge-preview"></a>Criar trabalho de Stream Analytics em Azure SQL Edge (Pré-visualização) 

Este artigo explica como criar um trabalho de Streaming T-SQL em Azure SQL Edge (Pré-visualização). Para criar um trabalho de streaming em SQL Edge, são necessários os seguintes passos

1. Criar os objetos de entrada e saída de fluxo externos
2. Defina a consulta de trabalho de streaming como parte da criação de trabalho em streaming.

> [!NOTE]
> Para ativar a funcionalidade de streaming T-SQL no Azure SQL Edge, ative o TF 11515 como opção de arranque ou utilize o comando [DBCC TRACEON.]( https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) Para obter mais informações sobre como ativar as bandeiras de rastreio utilizando o ficheiro mssql.conf, consulte [Configure utilizando o ficheiro mssql.conf](configure.md#configure-using-mssqlconf-file). Este requisito será removido em futuras atualizações do Azure SQL Edge (Pré-visualização).

## <a name="configure-an-external-stream-input-and-output-object"></a>Configure um objeto de entrada e saída de fluxo externo

O T-SQL Streaming utiliza a funcionalidade externa de fonte de dados do SQL Server para definir as fontes de dados associadas às inputs e saídas externas do fluxo. São necessários os seguintes comandos T-SQL para criar um objeto de entrada ou saída de fluxo externo.

[Criar Formato de Ficheiro Externo (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql)

[Criar Origem de Dados Externa (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)

[CRIAR FLUXO EXTERNO (Transact-SQL)](#example-create-an-external-stream-object-sql-database)

Adicionalmente, no caso de o comando SQL Edge (ou SQL Server, Azure SQL) ser utilizado como fluxo de saída, é necessário o comando T-SQL [(Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) para definir as credenciais de acesso à base de dados SQL.

### <a name="supported-input-and-output-stream-data-sources"></a>Fontes de dados de entrada e fluxo de saída suportadas

Atualmente, o Azure SQL Edge apenas suporta as seguintes fontes de dados como inputs e saídas de fluxo.

| Tipo de Origem de Dados | Input | Saída | Descrição |
|------------------|-------|--------|------------------|
| Hub de borda azure ioT | S | S | Fonte de Dados para ler/escrever dados de streaming para um Hub de Borda Azure IoT. Para mais informações sobre o Hub De Borda Azure IoT, consulte [o Hub IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)|
| Base de Dados SQL | N | S | Ligação de fonte de dados para escrever dados de streaming para base de dados SQL. A Base de Dados SQL pode ser uma base de dados Local SQL Edge ou um Servidor SQL remoto ou Base de Dados SQL Azure|
| Armazenamento de Blobs do Azure | N | S | Fonte de dados para escrever dados a uma bolha numa conta de armazenamento Azure. |
| Kafka | S | N | Fonte de dados para ler dados de streaming de um tópico de Kafka. Este adaptador está atualmente disponível apenas para versão Intel/AMD do Azure SQL Edge e não está disponível para a versão ARM64 do SQL Edge.|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>Exemplo: Criar um objeto de entrada/saída de fluxo externo para o Hub de Borda Azure IoT

O exemplo abaixo cria um objeto de fluxo externo para o Edge Hub. Para criar uma fonte externa de dados de entrada/saída para o Azure IoT Edge Hub, primeiro é necessário criar um formato de ficheiro externo para a SQL para compreender o layout dos dados que estão a ser lidos/escritos também.

1. Crie um formato de ficheiro externo com tipo de formato jSON.

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

3. Crie o objeto de fluxo externo para o Hub IoT Edge. O Script T-SQL abaixo criar um objeto de fluxo para o Edge Hub. No caso de um objeto de fluxo de hub Edge, o parâmetro LOCATION é o nome do tópico/canal do edge hub a ser lido ou escrito.

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

### <a name="example-create-an-external-stream-object-sql-database"></a>Exemplo: Criar uma base de dados sql de objeto de fluxo externo

O exemplo abaixo cria um objeto de fluxo externo para a base de dados Local SQL Edge. 

1. Crie uma chave-mestra na base de dados. Isto é necessário para encriptar o segredo credencial.

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. Crie uma credencial de base de dados para aceder à fonte do Servidor SQL. O exemplo seguinte cria uma credencial para a fonte de dados externa com IDENTIDADE = 'username' e SECRET = 'password'.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. Criar uma fonte de dados externa com a CREATE EXTERNAL DATA SOURCE. O seguinte exemplo:

    * Cria uma fonte de dados externa chamada LocalSQLOutput
    * Identifica a fonte de dados externa (LOCALIZAÇÃO = ' <vendor> :// <server> [: <port> ]'). No exemplo aponta para uma instância local de SQL Edge.
    * Finalmente, o exemplo usa a credencial criada anteriormente.

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
    LOCATION = 'sqlserver://tcp:.,1433'
    ,CREDENTIAL = SQLCredential
    )
    go
    ```

4. Crie o objeto De Fluxo Externo. O exemplo abaixo cria um objeto de fluxo externo apontando para um dbo de *mesa. Medidas de temperatura* na base de *dados MySQLDatabase*.

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements WITH (
    DATA_SOURCE = LocalSQLOutput,
    LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>Criar o trabalho de Streaming e as consultas de streaming

Utilize o procedimento armazenado pelo sistema **sys.sp_create_streaming_job** para definir as consultas de streaming e criar o trabalho de streaming. O procedimento **sp_create_streaming_job** armazenado leva dois parâmetros

- job_name - Nome do trabalho de streaming. Os nomes de trabalho de streaming são únicos em toda a instância.
- declaração - [Stream Analytics Consulta Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?) declarações de consulta de streaming.

O exemplo abaixo cria um simples trabalho de streaming com uma consulta de streaming. Esta consulta lê as inputs do Edge Hub e escreve para o *dbo. TemperaturaS Medidas* na base de dados.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

O exemplo abaixo cria um trabalho de streaming mais complexo com múltiplas consultas diferentes, incluindo uma consulta que usa a função de AnomalyDetection_ChangePoint incorporada para identificar anomalias nos dados de temperatura.

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

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>Iniciar, Parar, largar e monitorizar trabalhos de streaming

Para iniciar um trabalho de streaming em SQL Edge, execute o procedimento armazenado **sys.sp_start_streaming_job.** O procedimento armazenado requer o mesmo trabalho de streaming para começar, como entrada.

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

Para parar um trabalho de streaming em SQL Edge, execute o procedimento armazenado **sys.sp_stop_streaming_job.** O procedimento armazenado requer que o mesmo trabalho de streaming pare, como entrada.

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

Para deixar (ou apagar) um trabalho de streaming em SQL Edge, execute o procedimento armazenado **sys.sp_drop_streaming_job.** O procedimento armazenado requer que o mesmo trabalho de streaming desça, como entrada.

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

Para obter o estado atual de um trabalho de streaming em SQL Edge, execute o procedimento **sys.sp_get_streaming_job** armazenado. O procedimento armazenado requer que o mesmo trabalho de streaming desça, uma vez que a entrada e a saída supõe o nome e o estado atual do trabalho de streaming.

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

O trabalho de streaming pode estar em qualquer um dos seguintes estatutos

| Estado | Descrição |
|--------| ------------|
| Criado | O trabalho de streaming foi criado, mas ainda não foi iniciado |
| A iniciar | O trabalho de streaming está na fase inicial |
| Ocioso | O trabalho de streaming está em execução, no entanto não há entrada para processar |
| Em processamento | O trabalho de streaming está a decorrer e está a processar inputs. Este estado indica um estado saudável para o trabalho de streaming |
| Degradado | O trabalho de streaming está em execução, no entanto houve alguns erros não fatais de serialização/desserialização durante o processamento de entradas. O trabalho de entrada continuará a funcionar, mas vai deixar cair as inputs que encontram erros |
| Parada | O trabalho de streaming foi interrompido. |
| Falhou | O trabalho de streaming falhou. Isto é geralmente uma indicação de um erro fatal durante o processamento |

## <a name="next-steps"></a>Passos Seguintes

- [Ver metadados associados a trabalhos de streaming em Azure SQL Edge (Pré-visualização)](streaming-catalog-views.md) 
- [Criar um Fluxo Externo](create-external-stream-transact-sql.md)
