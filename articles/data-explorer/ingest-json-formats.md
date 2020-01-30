---
title: Ingerir dados formatados JSON no Azure Data Explorer
description: Saiba como ingerir dados formatados JSON no Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: d293b76e004d693813a074cb8551a86cb3c0bec2
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772337"
---
# <a name="ingest-json-formatted-sample-data-into-azure-data-explorer"></a>Ingerir dados de amostras formatadoj no Azure Data Explorer

Este artigo mostra-lhe como ingerir dados formatados JSON numa base de dados do Azure Data Explorer. Você começará com exemplos simples de JSON cru e mapeado, continuará a json multi-lined, e depois abordará schemas JSON mais complexos contendo matrizes e dicionários.  Os exemplos detalham o processo de ingerir dados formados JSON usando C#a linguagem de consulta Kusto (KQL), ou Python. A linguagem de consulta Kusto `ingest` comandos de controlo são executados diretamente no ponto final do motor. Em cenários de produção, a ingestão é executada ao serviço de Gestão de Dados utilizando bibliotecas de clientes ou ligações de dados. Leia [os dados da Ingest utilizando a biblioteca Azure Data Explorer Python](/azure/data-explorer/python-ingest-data) e os dados [ingest utilizando o Azure Data Explorer .NET Standard SDK](/azure/data-explorer/net-standard-ingest-data) para um walk-through no que diz respeito à ingestão de dados com estas bibliotecas de clientes.

## <a name="prerequisites"></a>Pré-requisitos

[Um cluster e uma base de dados de teste](create-cluster-database-portal.md)

## <a name="the-json-format"></a>O formato JSON

O Azure Data Explorer suporta dois formatos de ficheiroJ:
* `json`: Linha separada JSON. Cada linha nos dados de entrada tem exatamente um registo JSON.
* `multijson`: JSON multi-forrado. O parser ignora os separadores de linha e lê um registo da posição anterior até ao fim de um JSON válido.

### <a name="ingest-and-map-json-formatted-data"></a>Ingerir e mapear dados formatados JSON

A ingestão de dados formatados JSON requer que especifique o *formato* utilizando propriedade de [ingestão](/azure/kusto/management/data-ingestion/index#ingestion-properties). A ingestão de dados da JSON requer [mapeamento,](/azure/kusto/management/mappings)que mapeia uma entrada de fonte JSON na sua coluna alvo. Ao ingerir dados, utilize a propriedade pré-definida `jsonMappingReference` ingestão ou especifique a propriedade `jsonMapping`ingestão. Este artigo utilizará a propriedade `jsonMappingReference` ingestão, que é predefinida na tabela utilizada para a ingestão. Nos exemplos abaixo, começaremos por ingerir registos JSON como dados brutos para uma única tabela de colunas. Então usaremos o mapeamento para ingerir cada propriedade na sua coluna mapeada. 

### <a name="simple-json-example"></a>Exemplo JSON simples

O exemplo seguinte é um JSON simples, com uma estrutura plana. Os dados têm informação sobre temperatura e humidade, recolhida por vários dispositivos. Cada disco é marcado com uma identificação e carimbo de tempo.

```json
{
    "timestamp": "2019-05-02 15:23:50.0369439",
    "deviceId": "2945c8aa-f13e-4c48-4473-b81440bb5ca2",
    "messageId": "7f316225-839a-4593-92b5-1812949279b3",
    "temperature": 31.0301639051317,
    "humidity": 62.0791099602725
}
```

## <a name="ingest-raw-json-records"></a>Registos JSON crus 

Neste exemplo, ingere os registos da JSON como dados brutos numa única tabela de colunas. A manipulação de dados, utilizando consultas e atualizar a política é feita após a ingestão dos dados.

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

Use a linguagem de consulta Kusto para ingerir dados num formato JSON cru.

1. Inicie sessão em [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. Selecione **Adicionar cluster**.

1. Na caixa de diálogo do **cluster Adicionar,** introduza o URL do cluster na forma `https://<ClusterName>.<Region>.kusto.windows.net/`, e, em seguida, selecione **Adicionar**.

1. Colhe no comando seguinte e selecione **Executar** para criar a tabela.

    ```Kusto
    .create table RawEvents (Event: dynamic)
    ```

    Esta consulta cria uma tabela com uma única coluna `Event` de um tipo de dados [dinâmico.](/azure/kusto/query/scalar-data-types/dynamic)

1. Crie o mapeamento JSON.

    ```Kusto
    .create table RawEvents ingestion json mapping 'RawEventMapping' '[{"column":"Event","path":"$"}]'
    ```

    Este comando cria um mapeamento, e mapeia o caminho raiz JSON `$` para a coluna `Event`.

1. Ingerir dados na tabela `RawEvents`.

    ```Kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=RawEventMapping)
    ```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

Utilize C# para ingerir dados em formato JSON cru.

1. Crie a mesa `RawEvents`.

    ```C#
    var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
    var kustoConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder);

    var table = "RawEvents";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Events", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Crie o mapeamento JSON.
    
    ```C#
    var tableMapping = "RawEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                new JsonColumnMapping {ColumnName = "Events", JsonPath = "$"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```
    Este comando cria um mapeamento, e mapeia o caminho raiz JSON `$` para a coluna `Event`.

1. Ingerir dados na tabela `RawEvents`.

    ```C#
    var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var ingestConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder);
    
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

> [!NOTE]
> Os dados são agregados de acordo com a política de [lotação,](/azure/kusto/concepts/batchingpolicy)resultando numa latência de alguns minutos.

# <a name="pythontabpython"></a>[Python](#tab/python)

Use python para ingerir dados em formato JSON cru.

1. Crie a mesa `RawEvents`.

    ```Python
    KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_URI, AAD_TENANT_ID)
    KUSTO_CLIENT = KustoClient(KCSB_DATA)
    TABLE = "RawEvents"

    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Events: dynamic)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Crie o mapeamento JSON.

    ```Python
    MAPPING = "RawEventMapping"
    CREATE_MAPPING_COMMAND = ".create table " + TABLE + " ingestion json mapping '" + MAPPING + """' '[{"column":"Event","path":"$"}]'"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Ingerir dados na tabela `RawEvents`.

    ```Python
    INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(INGEST_URI, AAD_TENANT_ID)
    INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    > [!NOTE]
    > Os dados são agregados de acordo com a política de [lotação,](/azure/kusto/concepts/batchingpolicy)resultando numa latência de alguns minutos.

---

## <a name="ingest-mapped-json-records"></a>Ingerir registos da JSON mapeados

Neste exemplo, ingere dados de registos da JSON. Cada propriedade JSON é mapeada para uma única coluna na tabela. 

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Crie uma nova tabela, com um esquema semelhante aos dados de entrada da JSON. Usaremos esta mesa para todos os seguintes exemplos e comandos ingerir. 

    ```Kusto
    .create table Events (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)
    ```

1. Crie o mapeamento JSON.

    ```Kusto
    .create table Events ingestion json mapping 'FlatEventMapping' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'
    ```

    Neste mapeamento, tal como definido pelo esquema de mesa, as entradas `timestamp` serão ingeridas para a coluna `Time` como `datetime` tipos de dados.

1. Ingerir dados na tabela `Events`.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=FlatEventMapping)
    ```

    O ficheiro 'simple.json' tem alguns registos JSON separados de linha. O formato é `json`, e o mapeamento utilizado no comando ingerir é o `FlatEventMapping` que criou.

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. Crie uma nova tabela, com um esquema semelhante aos dados de entrada da JSON. Usaremos esta mesa para todos os seguintes exemplos e comandos ingerir. 

    ```C#
    var table = "Events";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Time", "System.DateTime"),
                Tuple.Create("Device", "System.String"),
                Tuple.Create("MessageId", "System.String"),
                Tuple.Create("Temperature", "System.Double"),
                Tuple.Create("Humidity", "System.Double"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Crie o mapeamento JSON.

    ```C#
    var tableMapping = "FlatEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.timestamp"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.deviceId"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.messageId"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.temperature"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.humidity"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

    Neste mapeamento, tal como definido pelo esquema de mesa, as entradas `timestamp` serão ingeridas para a coluna `Time` como `datetime` tipos de dados.    

1. Ingerir dados na tabela `Events`.

    ```C#
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

    O ficheiro 'simple.json' tem alguns registos JSON separados de linha. O formato é `json`, e o mapeamento utilizado no comando ingerir é o `FlatEventMapping` que criou.

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Crie uma nova tabela, com um esquema semelhante aos dados de entrada da JSON. Usaremos esta mesa para todos os seguintes exemplos e comandos ingerir. 

    ```Python
    TABLE = "RawEvents"
    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Crie o mapeamento JSON.

    ```Python
    MAPPING = "FlatEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Ingerir dados na tabela `Events`.

    ```Python
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    O ficheiro 'simple.json' tem alguns registos jSON separados. O formato é `json`, e o mapeamento utilizado no comando ingerir é o `FlatEventMapping` que criou.    
---

## <a name="ingest-multi-lined-json-records"></a>Ingerir registos JSON multi-lined

Neste exemplo, ingere sontes JSON multi-lineados. Cada propriedade JSON é mapeada para uma única coluna na tabela. O ficheiro 'multilined.json' tem alguns registos da JSON. O formato `multijson` diz ao motor para ler os registos pela estrutura JSON.

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

Ingerir dados na tabela `Events`.

```Kusto
.ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=FlatEventMapping)
```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

Ingerir dados na tabela `Events`.

```C#
var tableMapping = "FlatEventMapping";
var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
var properties =
    new KustoQueuedIngestionProperties(database, table)
    {
        Format = DataSourceFormat.multijson,
        IngestionMappingReference = tableMapping
    };

ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Ingerir dados na tabela `Events`.

```Python
MAPPING = "FlatEventMapping"
BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.JSON?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
```

---

## <a name="ingest-json-records-containing-arrays"></a>Registos JSON que contêm matrizes

Tipos de dados de matriz são uma coleção ordenada de valores. A ingestão de um array JSON é feita por uma política de [atualização.](/azure/kusto/management/update-policy) O JSON é ingerido como está numa mesa intermédia. Uma política de atualização executa uma função pré-definida na tabela `RawEvents`, reinteando os resultados para a tabela-alvo. Ingeriremos dados com a seguinte estrutura:

```json
{
    "records": 
    [
        {
            "timestamp": "2019-05-02 15:23:50.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "7f316225-839a-4593-92b5-1812949279b3",
            "temperature": 31.0301639051317,
            "humidity": 62.0791099602725
        },
        {
            "timestamp": "2019-05-02 15:23:51.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "57de2821-7581-40e4-861e-ea3bde102364",
            "temperature": 33.7529423105311,
            "humidity": 75.4787976739364
        }
    ]
}
```

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Crie uma função `update policy` que expanda a recolha de `records` para que cada valor da coleção receba uma linha separada, utilizando o operador `mv-expand`. Usaremos `RawEvents` de mesa como mesa de origem e `Events` como mesa-alvo.

    ```Kusto
    .create function EventRecordsExpand() {
        RawEvents
        | mv-expand records = Event
        | project
            Time = todatetime(records["timestamp"]),
            Device = tostring(records["deviceId"]),
            MessageId = tostring(records["messageId"]),
            Temperature = todouble(records["temperature"]),
            Humidity = todouble(records["humidity"])
    }
    ```

1. O esquema recebido pela função deve coincidir com o esquema da mesa-alvo. Utilize `getschema` operador para rever o esquema.

    ```Kusto
    EventRecordsExpand() | getschema
    ```

1. Adicione a política de atualização à tabela-alvo. Esta política irá automaticamente executar a consulta sobre quaisquer dados recentemente ingeridos na tabela intermédia `RawEvents` e ingerir os resultados na tabela `Events`. Defina uma política de retenção zero para evitar a persistência da tabela intermédia.

    ```Kusto
    .alter table Events policy update @'[{"Source": "RawEvents", "Query": "EventRecordsExpand()", "IsEnabled": "True"}]'
    ```

1. Ingerir dados na tabela `RawEvents`.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=RawEventMapping)
    ```

1. Reveja os dados na tabela `Events`.

    ```Kusto
    Events
    ```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. Crie uma função de atualização que expanda a recolha de `records` para que cada valor da coleção receba uma linha separada, utilizando o operador `mv-expand`. Usaremos `RawEvents` de mesa como mesa de origem e `Events` como mesa-alvo.   

    ```C#
    var command =
        CslCommandGenerator.GenerateCreateFunctionCommand(
            "EventRecordsExpand",
            "UpdateFunctions",
            string.Empty,
            null,
            @"RawEvents
                | mv-expand records = Event
                | project
                    Time = todatetime(records['timestamp']),
                    Device = tostring(records['deviceId']),
                    MessageId = tostring(records['messageId']),
                    Temperature = todouble(records['temperature']),
                    Humidity = todouble(records['humidity'])",
            ifNotExists: false);

    kustoClient.ExecuteControlCommand(command);
    ```

    > [!NOTE]
    > O esquema recebido pela função deve coincidir com o esquema da mesa-alvo.

1. Adicione a política de atualização à tabela-alvo. Esta política irá automaticamente executar a consulta sobre quaisquer dados recentemente ingeridos na tabela intermédia `RawEvents` e ingerir os seus resultados na tabela `Events`. Defina uma política de retenção zero para evitar a persistência da tabela intermédia.

    ```C#
    var command =
        ".alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]";

    kustoClient.ExecuteControlCommand(command);
    ```

1. Ingerir dados na tabela `RawEvents`.

    ```C#
    var table = "RawEvents";
    var tableMapping = "RawEventMapping";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```
    
1. Reveja os dados na tabela `Events`.

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Crie uma função de atualização que expanda a recolha de `records` para que cada valor da coleção receba uma linha separada, utilizando o operador `mv-expand`. Usaremos `RawEvents` de mesa como mesa de origem e `Events` como mesa-alvo.   

    ```Python
    CREATE_FUNCTION_COMMAND = 
        '''.create function EventRecordsExpand() { 
            RawEvents
            | mv-expand records = Event
            | project
                Time = todatetime(records["timestamp"]),
                Device = tostring(records["deviceId"]),
                MessageId = tostring(records["messageId"]),
                Temperature = todouble(records["temperature"]),
                Humidity = todouble(records["humidity"])
            }'''
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_FUNCTION_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

    > [!NOTE]
    > O esquema recebido pela função tem de coincidir com o esquema da mesa-alvo.

1. Adicione a política de atualização à tabela-alvo. Esta política irá automaticamente executar a consulta sobre quaisquer dados recentemente ingeridos na tabela intermédia `RawEvents` e ingerir os seus resultados na tabela `Events`. Defina uma política de retenção zero para evitar a persistência da tabela intermédia.

    ```Python
    CREATE_UPDATE_POLICY_COMMAND = 
        """.alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_UPDATE_POLICY_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Ingerir dados na tabela `RawEvents`.

    ```Python
    TABLE = "RawEvents"
    MAPPING = "RawEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

1. Reveja os dados na tabela `Events`.

---    

## <a name="ingest-json-records-containing-dictionaries"></a>Registos JSON que contêm dicionários

JSON estruturado no dicionário contém pares de valor-chave. Os registos json são submetidos a mapeamento de ingestão usando expressão lógica no `JsonPath`. Pode ingerir dados com a seguinte estrutura:

```json
{
    "event": 
    [
        {
            "Key": "timestamp",
            "Value": "2019-05-02 15:23:50.0000000"
        },
        {
            "Key": "deviceId",
            "Value": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71"
        },
        {
            "Key": "messageId",
            "Value": "7f316225-839a-4593-92b5-1812949279b3"
        },
        {
            "Key": "temperature",
            "Value": 31.0301639051317
        },
        {
            "Key": "humidity",
            "Value": 62.0791099602725
        }
    ]
}
```

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Crie um mapeamento JSON.

    ```Kusto
    .create table Events ingestion json mapping 'KeyValueEventMapping' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'
    ```

1. Ingerir dados na tabela `Events`.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=KeyValueEventMapping)
    ```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. Crie um mapeamento JSON.

    ```C#
    var tableName = "Events";
    var tableMapping = "KeyValueEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.event[?(@.Key == 'timestamp')]"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.event[?(@.Key == 'deviceId')]"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.event[?(@.Key == 'messageId')]"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.event[?(@.Key == 'temperature')]"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.event[?(@.Key == 'humidity')]"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Ingerir dados na tabela `Events`.

    ```C#
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Crie um mapeamento JSON.

    ```Python
    MAPPING = "KeyValueEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Ingerir dados na tabela `Events`.

     ```Python
    MAPPING = "KeyValueEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)u
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

---    

## <a name="next-steps"></a>Passos seguintes

* [Visão geral da ingestão de dados](ingest-data-overview.md)
* [Escrever consultas](write-queries.md)