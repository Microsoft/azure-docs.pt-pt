---
title: 'Início rápido: Ingestão de dados com o SDK do Azure Data Explorer .NET Standard (pré-visualização)'
description: Neste início rápido, irá aprender a ingerir dados de (carga) no Explorador de dados do Azure com o SDK de .NET padrão.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 11/18/2018
ms.openlocfilehash: 0197ae8077a00111e005e5686efcd2597b995bcb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58007033"
---
# <a name="quickstart-ingest-data-using-the-azure-data-explorer-net-standard-sdk-preview"></a>Início rápido: Ingestão de dados com o SDK do Azure Data Explorer .NET Standard (pré-visualização)

Explorador de dados do Azure (ADX) é um serviço de exploração de dados rápida e altamente escalável para dados de telemetria e de registo. ADX fornece duas bibliotecas de cliente para o .NET Standard: uma [biblioteca de ingestão](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest.NETStandard) e [uma biblioteca de dados](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard). Estas bibliotecas permitem ingerir (carregar) dados para um cluster e consultar dados a partir do código. Neste início rápido, primeiro vai criar uma tabela e o mapeamento de dados num cluster de teste. Em seguida, colocar em fila uma ingestão ao cluster e validar os resultados.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

* [Um cluster e uma base de dados de teste](create-cluster-database-portal.md)

## <a name="install-the-ingest-library"></a>Instalar a biblioteca de ingestão

```
Install-Package Microsoft.Azure.Kusto.Ingest.NETStandard
```

## <a name="authentication"></a>Authentication

Para autenticar uma aplicação, o Azure Data Explorer utiliza o ID de inquilino do AAD. Para localizar o ID de inquilino, utilize o seguinte URL, substituindo o domínio pelo *SeuDomínio*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Por exemplo, se o seu domínio for *contoso.com*, o URL é: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Clique neste URL para ver os resultados; a primeira linha é igual à seguinte. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Neste caso, o ID de inquilino é `6babcaad-604b-40ac-a9d7-9fd97c0b779f`.

Este exemplo utiliza um utilizador do AAD e a palavra-passe para a autenticação para aceder ao cluster. Também pode utilizar o certificado de aplicação do AAD e a chave de aplicação do AAD. Definir os seus valores para `tenantId`, `user`, e `password` antes de executar esse código.

```csharp
var tenantId = "<TenantId>";
var user = "<User>";
var password = "<Password>";
```

## <a name="construct-the-connection-string"></a>Construir a cadeia de ligação
Agora construa a cadeia de ligação. Irá criar a tabela de destino e o mapeamento num passo posterior.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var database = "<DatabaseName>";

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };
```

## <a name="set-source-file-information"></a>Definir as informações do ficheiro de origem

Defina o caminho para o ficheiro de origem. Este exemplo utiliza um ficheiro de exemplo alojado no Armazenamento de Blobs do Azure. O conjunto de dados de exemplo **StormEvents** contém dados relacionados com Meteorologia dos [Centros Nacionais de Informações Ambientais](https://www.ncdc.noaa.gov/stormevents/).

```csharp
var blobPath = "https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
```

## <a name="create-a-table-on-your-test-cluster"></a>Criar uma tabela no cluster de teste
Criar uma tabela chamada `StormEvents` que corresponda ao esquema dos dados no `StormEvents.csv` ficheiro.

```csharp
var table = "StormEvents";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("StartTime", "System.DateTime"),
                Tuple.Create("EndTime", "System.DateTime"),
                Tuple.Create("EpisodeId", "System.Int32"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("State", "System.String"),
                Tuple.Create("EventType", "System.String"),
                Tuple.Create("InjuriesDirect", "System.Int32"),
                Tuple.Create("InjuriesIndirect", "System.Int32"),
                Tuple.Create("DeathsDirect", "System.Int32"),
                Tuple.Create("DeathsIndirect", "System.Int32"),
                Tuple.Create("DamageProperty", "System.Int32"),
                Tuple.Create("DamageCrops", "System.Int32"),
                Tuple.Create("Source", "System.String"),
                Tuple.Create("BeginLocation", "System.String"),
                Tuple.Create("EndLocation", "System.String"),
                Tuple.Create("BeginLat", "System.Double"),
                Tuple.Create("BeginLon", "System.Double"),
                Tuple.Create("EndLat", "System.Double"),
                Tuple.Create("EndLon", "System.Double"),
                Tuple.Create("EpisodeNarrative", "System.String"),
                Tuple.Create("EventNarrative", "System.String"),
                Tuple.Create("StormSummary", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="define-ingestion-mapping"></a>Definir o mapeamento de ingestão

Mapear dados de entrada de CSV para os nomes de coluna utilizados ao criar a tabela.
Aprovisionar um [objeto de mapeamento de coluna CSV](/azure/kusto/management/tables#create-ingestion-mapping) nessa tabela

```csharp
var tableMapping = "StormEvents_CSV_Mapping";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCsvMappingCreateCommand(
            table,
            tableMapping,
            new[]
            {
                new CsvColumnMapping { ColumnName = "StartTime", Ordinal = 0 },
                new CsvColumnMapping { ColumnName = "EndTime", Ordinal = 1 },
                new CsvColumnMapping { ColumnName = "EpisodeId", Ordinal = 2 },
                new CsvColumnMapping { ColumnName = "EventId", Ordinal = 3 },
                new CsvColumnMapping { ColumnName = "State", Ordinal = 4 },
                new CsvColumnMapping { ColumnName = "EventType", Ordinal = 5 },
                new CsvColumnMapping { ColumnName = "InjuriesDirect", Ordinal = 6 },
                new CsvColumnMapping { ColumnName = "InjuriesIndirect", Ordinal = 7 },
                new CsvColumnMapping { ColumnName = "DeathsDirect", Ordinal = 8 },
                new CsvColumnMapping { ColumnName = "DeathsIndirect", Ordinal = 9 },
                new CsvColumnMapping { ColumnName = "DamageProperty", Ordinal = 10 },
                new CsvColumnMapping { ColumnName = "DamageCrops", Ordinal = 11 },
                new CsvColumnMapping { ColumnName = "Source", Ordinal = 12 },
                new CsvColumnMapping { ColumnName = "BeginLocation", Ordinal = 13 },
                new CsvColumnMapping { ColumnName = "EndLocation", Ordinal = 14 },
                new CsvColumnMapping { ColumnName = "BeginLat", Ordinal = 15 },
                new CsvColumnMapping { ColumnName = "BeginLon", Ordinal = 16 },
                new CsvColumnMapping { ColumnName = "EndLat", Ordinal = 17 },
                new CsvColumnMapping { ColumnName = "EndLon", Ordinal = 18 },
                new CsvColumnMapping { ColumnName = "EpisodeNarrative", Ordinal = 19 },
                new CsvColumnMapping { ColumnName = "EventNarrative", Ordinal = 20 },
                new CsvColumnMapping { ColumnName = "StormSummary", Ordinal = 21 },
            });

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="queue-a-message-for-ingestion"></a>Colocar uma mensagem em fila para ingestão

Uma mensagem da fila para extrair os dados do armazenamento de BLOBs e ingerir dados no ADX.

```csharp
var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
var ingestConnectionStringBuilder =
    new KustoConnectionStringBuilder(ingestUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };

using (var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder))
{
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.csv,
            CSVMappingReference = tableMapping,
            IgnoreFirstRecord = true
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
}
```

## <a name="validate-data-was-ingested-into-the-table"></a>Validar dados foi ingeridos para a tabela

Aguarde cinco a dez minutos para a ingestão em fila agendar a ingestão e carregar os dados para ADX. Em seguida, execute o seguinte código para obter a contagem de registos na tabela `StormEvents`.

```csharp
using (var cslQueryProvider = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
{
    var query = $"{table} | count";

    var results = cslQueryProvider.ExecuteQuery<long>(query);
    Console.WriteLine(results.Single());
}
```

## <a name="run-troubleshooting-queries"></a>Executar consultas de resolução de problemas

Inicie sessão no [https://dataexplorer.azure.com](https://dataexplorer.azure.com) e ligue ao cluster. Execute o seguinte comando na base de dados para ver se ocorreram quaisquer falhas de ingestão nas últimas quatro horas. Substitua o nome da base de dados antes de executar.

```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Execute o seguinte comando para ver o estado de todas as operações de ingestão nas últimas quatro horas. Substitua o nome da base de dados antes de executar.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Limpar recursos

Se tenciona seguir os nossos inícios rápidos e tutoriais, mantenha os recursos que criou. Caso contrário, execute o seguinte comando na base de dados para limpar a tabela `StormEvents`.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Escrever consultas](write-queries.md)
