---
title: Ingestão de blob de ponta a ponta no Azure Data Explorer usandoC#
description: Neste artigo, você aprende a ingerir BLOBs no Azure Data Explorer com um exemplo de ponta a ponta usando C#o.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 7d737319c9ddc8040a7cae6f7a9991c625cc4fcd
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809604"
---
# <a name="end-to-end-blob-ingestion-into-azure-data-explorer-using-c"></a>Ingestão de blob de ponta a ponta no Azure Data Explorer usandoC#

> [!div class="op_single_selector"]
> * [C#](end-to-end-csharp.md)
> * [Python](end-to-end-python.md)
>

O Azure Data Explorer é um serviço de exploração de dados rápido e escalonável para dados de log e telemetria. Este artigo fornece um exemplo de ponta a ponta sobre como ingerir dados do armazenamento de BLOBs para o Azure Data Explorer. Você aprenderá como criar programaticamente um grupo de recursos, uma conta de armazenamento e um contêiner, um hub de eventos e um cluster de Data Explorer do Azure e um banco de dados. Você também aprenderá como configurar programaticamente o Data Explorer do Azure para ingerir dados da nova conta de armazenamento.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="install-c-nuget"></a>Instalar C# o NuGet

* Instale o [Microsoft. Azure. Management. Kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Instale o [Microsoft. Azure. Management. ResourceManager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager).
* Instale o [Microsoft. Azure. Management. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/).
* Instale o [Microsoft. Azure. Storage. blob](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/).
* Instale o [Microsoft. REST. ClientRuntime. Azure. Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) para autenticação.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

[!INCLUDE [data-explorer-e2e-event-grid-resource-template](../../includes/data-explorer-e2e-event-grid-resource-template.md)]

## <a name="code-example"></a>Exemplo de código 

O exemplo de código a seguir fornece um processo passo a passo que resulta na ingestão de dados no Azure Data Explorer. Primeiro, você cria um grupo de recursos e recursos do Azure, como uma conta de armazenamento e um contêiner, um hub de eventos e um cluster de Data Explorer do Azure e um banco de dados. Em seguida, você cria uma assinatura de grade de eventos e um mapeamento de tabela e coluna no banco de dados Data Explorer do Azure. Por fim, você cria a conexão de dados para configurar o Azure Data Explorer para ingerir dados da nova conta de armazenamento. 

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
string location = "West Europe";
string locationSmallCase = "westeurope";
string azureResourceTemplatePath = @"xxxxxxxxx\template.json";//path to the Azure Resource Manager template json from the previous section

string deploymentName = "e2eexample";
string resourceGroupName = deploymentName + "resourcegroup";
string eventHubName = deploymentName + "eventhub";
string eventHubNamespaceName = eventHubName + "ns";
string storageAccountName = deploymentName + "storage";
string storageContainerName = deploymentName + "storagecontainer";
string eventGridSubscriptionName = deploymentName + "eventgrid";
string kustoClusterName = deploymentName + "kustocluster";
string kustoDatabaseName = deploymentName + "kustodatabase";
string kustoTableName = "Events";
string kustoColumnMappingName = "Events_CSV_Mapping";
string kustoDataConnectionName = deploymentName + "kustoeventgridconnection";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);
Console.WriteLine("Step 1: Create a new resource group in your Azure subscription to manage all the resources for using Azure Data Explorer.");
resourceManagementClient.SubscriptionId = subscriptionId;
await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(resourceGroupName,
    new ResourceGroup() { Location = locationSmallCase });

Console.WriteLine(
    "Step 2: Create a Blob Storage, a container in the Storage account, an Event Hub, an Azure Data Explorer cluster, and database by using an Azure Resource Manager template.");
var parameters = $"{{\"eventHubNamespaceName\":{{\"value\":\"{eventHubNamespaceName}\"}},\"eventHubName\":{{\"value\":\"{eventHubName}\"}},\"storageAccountName\":{{\"value\":\"{storageAccountName}\"}},\"containerName\":{{\"value\":\"{storageContainerName}\"}},\"kustoClusterName\":{{\"value\":\"{kustoClusterName}\"}},\"kustoDatabaseName\":{{\"value\":\"{kustoDatabaseName}\"}}}}";
string template = File.ReadAllText(azureResourceTemplatePath, Encoding.UTF8);
await resourceManagementClient.Deployments.CreateOrUpdateAsync(resourceGroupName, deploymentName,
    new Deployment(new DeploymentProperties(DeploymentMode.Incremental, template: template,
        parameters: parameters)));

Console.WriteLine(
    "Step 3: Create an Event Grid subscription to publish blob events created in a specific container to an Event Hub.");
var eventGridClient = new EventGridManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};
string storageResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
string eventHubResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.EventHub/namespaces/{eventHubNamespaceName}/eventhubs/{eventHubName}";
await eventGridClient.EventSubscriptions.CreateOrUpdateAsync(storageResourceId, eventGridSubscriptionName,
    new EventSubscription()
    {
        Destination = new EventHubEventSubscriptionDestination(eventHubResourceId),
        Filter = new EventSubscriptionFilter()
        {
            SubjectBeginsWith = $"/blobServices/default/containers/{storageContainerName}",
            IncludedEventTypes = new List<string>(){"Microsoft.Storage.BlobCreated"}
        }
    });

Console.WriteLine("Step 4: Create a table (with three columns: EventTime, EventId, and EventSummary) and column mapping in your Azure Data Explorer database.");
var kustoUri = $"https://{kustoClusterName}.{locationSmallCase}.kusto.windows.net";
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(kustoUri)
{
    InitialCatalog = kustoDatabaseName,
    FederatedSecurity = true,
    ApplicationClientId = clientId,
    ApplicationKey = clientSecret,
    Authority = tenantId
};

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            kustoTableName,
            new[]
            {
                Tuple.Create("EventTime", "System.DateTime"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("EventSummary", "System.String"),
            });

    kustoClient.ExecuteControlCommand(command);

    command = CslCommandGenerator.GenerateTableCsvMappingCreateCommand(
        kustoTableName,
        kustoColumnMappingName,
        new[]
        {
            new CsvColumnMapping { ColumnName = "EventTime", CslDataType="dateTime", Ordinal = 0 },
            new CsvColumnMapping { ColumnName = "EventId", CslDataType="int", Ordinal = 1 },
            new CsvColumnMapping { ColumnName = "EventSummary", CslDataType="string", Ordinal = 2 },
        });
    kustoClient.ExecuteControlCommand(command);
}

Console.WriteLine("Step 5: Add an Event Grid data connection. Azure Data Explorer will automatically ingest the data when new blobs are created.");
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, kustoClusterName,
                kustoDatabaseName, dataConnectionName: kustoDataConnectionName, new EventGridDataConnection(storageResourceId, eventHubResourceId, consumerGroup: "$Default", location: location, tableName:kustoTableName, mappingRuleName: kustoColumnMappingName, dataFormat: "csv"));

```
| **Definição** | **Descrição do campo** |
|---|---|---|
| tenantId | Sua ID de locatário. Também conhecida como ID de diretório.|
| subscriptionId | A ID da assinatura que você usa para a criação de recursos.|
| clientId | A ID do cliente do aplicativo que pode acessar recursos em seu locatário.|
| clientSecret | O segredo do cliente do aplicativo que pode acessar recursos em seu locatário. |

## <a name="test-the-code-example"></a>Testar o exemplo de código

1. Carregar um arquivo na conta de armazenamento

    ```csharp
    string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=xxxxxxxxxxxxxx;AccountKey=xxxxxxxxxxxxxx;EndpointSuffix=core.windows.net";
    var cloudStorageAccount = CloudStorageAccount.Parse(storageConnectionString);
    CloudBlobClient blobClient = cloudStorageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(storageContainerName);
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("test.csv");
    var blobContent = @"2007-01-01 00:00:00.0000000,2592,Several trees down
    2007-01-01 00:00:00.0000000,4171,Winter Storm";
    await blockBlob.UploadTextAsync(blobContent);
    ```
    |**Definição** | **Descrição do campo**|
    |---|---|---|
    | storageConnectionString | A cadeia de conexão da conta de armazenamento criada programaticamente.|

2. Executar uma consulta de teste no Azure Data Explorer

    ```csharp
    var kustoUri = $"https://{kustoClusterName}.{locationSmallCase}.kusto.windows.net";
    var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(kustoUri)
    {
        InitialCatalog = kustoDatabaseName,
        FederatedSecurity = true,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };
    using (var kustoClient = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
    {
        var query = $"{kustoTableName} | take 10";
        using (var reader = kustoClient.ExecuteQuery(query) as DataTableReader2)
        {// Print the contents of each of the result sets. 
            while (reader.Read())
            {
                Console.WriteLine($"{reader[0]}, {reader[1]}, {reader[2]}");
            }
        }
    }
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Para excluir o grupo de recursos e limpar os recursos, use o seguinte comando:

```csharp
await resourceManagementClient.ResourceGroups.DeleteAsync(resourceGroupName);
```

## <a name="next-steps"></a>Passos seguintes

*  [Crie um cluster de data Explorer do Azure e um banco de dados](create-cluster-database-csharp.md) para saber mais sobre outras maneiras de criar um cluster e um banco de dados.
* O [Azure data Explorer ingestão de dados](ingest-data-overview.md) para saber mais sobre os métodos de ingestão.
* [Início rápido: consultar dados no Azure data Explorer](web-query-data.md) Interface do usuário da Web.
* [Escreva consultas](write-queries.md) com linguagem de consulta Kusto.