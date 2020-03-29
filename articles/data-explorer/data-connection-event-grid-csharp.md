---
title: 'Criar uma ligação de dados da Rede de Eventos para o Azure Data Explorer utilizando C #'
description: Neste artigo, aprende-se a criar uma ligação de dados da Rede de Eventos para o Azure Data Explorer utilizando o C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 03963f60cc364dd36ad55c0a28e92e3b585bb38d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255084"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-c"></a>Criar uma ligação de dados da Rede de Eventos para o Azure Data Explorer utilizando C #

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C #](data-connection-event-grid-csharp.md)
> * [Pitão](data-connection-event-grid-python.md)
> * [Modelo Azure Resource Manager](data-connection-event-grid-resource-manager.md)


O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. O Azure Data Explorer oferece ingestão (carregamento de dados) de Centros de Eventos, Hubs IoT e bolhas escritas para recipientes blob. Neste artigo, cria uma ligação de dados da Rede de Eventos para o Azure Data Explorer utilizando c#.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver o Visual Studio 2019 instalado, pode descarregar e utilizar a Edição Comunitária **gratuita** do [Visual Studio 2019.](https://www.visualstudio.com/downloads/) Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.
* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* Criar [um cluster e base de dados](create-cluster-database-csharp.md)
* Criar [mapeamento](net-standard-ingest-data.md#create-a-table-on-your-test-cluster) de mesa e coluna
* Definir bases de [dados e políticas de tabela](database-table-policies-csharp.md) (opcional)
* Crie uma conta de armazenamento com uma subscrição da Grelha de [Eventos.](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account)

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Adicione uma ligação de dados da Grelha de Eventos

O exemplo que se segue mostra como adicionar uma ligação de dados da Rede de Eventos programáticamente. Consulte a criação de uma ligação de dados da Rede de [Eventos no Azure Data Explorer](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) para adicionar uma ligação de dados da Rede de Eventos utilizando o portal Azure.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The event hub and storage account that are created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var storageAccountResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName,
    new EventGridDataConnection(storageAccountResourceId, eventHubResourceId, consumerGroup, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Definição** | **Valor sugerido** | **Descrição do campo**|
|---|---|---|
| inquilinoId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | Sua identificação do inquilino. Também conhecido como ID diretório.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | O ID de subscrição que utiliza para a criação de recursos.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | A identificação do cliente da aplicação que pode aceder a recursos no seu inquilino.|
| clienteSecret | *xxxxxxxxxxxxxx* | O segredo do cliente da aplicação que pode aceder a recursos no seu inquilino. |
| resourceGroupName | *testrg* | O nome do grupo de recursos que contém o seu cluster.|
| clusterName | *mykustocluster* | O nome do seu aglomerado.|
| nome da base de dados | *mykustodatabase* | O nome da base de dados do alvo no seu cluster.|
| dataConnectionName | *myeventhubconnect* | O nome desejado da sua ligação de dados.|
| tableName | *Eventos de Tempestade* | O nome da tabela-alvo na base de dados do alvo.|
| mapeandoORuleName | *StormEvents_CSV_Mapping* | O nome do mapeamento da coluna relacionado com a mesa-alvo.|
| dataFormat | *csv* | O formato de dados da mensagem.|
| eventoHubResourceId | *ID de recursos* | O ID de recursos do seu Hub de Eventos onde a Grelha de Eventos está configurada para enviar eventos. |
| armazenamentoAccountResourceId | *ID de recursos* | A identificação de recursos da sua conta de armazenamento que detém os dados para ingestão. |
| consumerGroup | *$Default* | O grupo de consumidores do seu Hub de Eventos.|
| localização | *E.U.A. Central* | A localização do recurso de ligação de dados.|

## <a name="generate-sample-data"></a>Gerar dados de exemplo

Agora que o Azure Data Explorer e a conta de armazenamento estão ligados, pode criar dados de amostra e carregá-lo para o armazenamento de blob.

Este script cria um novo recipiente na sua conta de armazenamento, envia um ficheiro existente (como uma bolha) para esse recipiente e, em seguida, lista as bolhas no recipiente.

```csharp
var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName=<container_name>;
var blobName=<blob_name>;
var localFileName=<file_to_upload>;

// Creating the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set metadata and upload file to blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoIngestionMappingReference", "mapping_v2‬");
blob.UploadFromFile(localFileName);

// List blobs
var blobs = container.ListBlobs();
```

> [!NOTE]
> O Azure Data Explorer não apaga as bolhas pós ingestão. Mantenha as bolhas durante três a cinco dias utilizando o ciclo de vida de [armazenamento de Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) para gerir a eliminação de bolhas.

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
