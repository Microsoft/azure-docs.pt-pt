---
title: Criar uma conexão de dados do hub de eventos para o Azure Data Explorer usandoC#
description: Neste artigo, você aprenderá a criar uma conexão de dados do hub de eventos para o Azure C#data Explorer usando o.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 741232bb7ed6ecf3d20711c1f6248f8e6d6215e8
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72031685"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-c"></a>Criar uma conexão de dados do hub de eventos para o Azure Data Explorer usandoC#

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. O Azure Data Explorer oferece ingestão (carregamento de dados) de hubs de eventos, hubs IoT e Blobs gravados em contêineres de BLOB. Neste artigo, você cria uma conexão de dados do hub de eventos para o Azure C#data Explorer usando o.

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver o Visual Studio 2019 instalado, poderá baixar e usar o [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)gratuito. Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

* Criar [um cluster e um banco de dados](create-cluster-database-csharp.md)

* Criar [mapeamento de tabela e coluna](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)

* Definir [políticas de banco de dados e tabela](database-table-policies-csharp.md) (opcional)

* Crie um [Hub de eventos com dados para ingestão](ingest-data-event-hub.md#create-an-event-hub). 

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Adicionar uma conexão de dados do hub de eventos

O exemplo a seguir mostra como adicionar uma conexão de dados do hub de eventos de forma programática. Consulte [conectar-se ao Hub de eventos](ingest-data-event-hub.md#connect-to-the-event-hub) para adicionar uma conexão de dados do hub de eventos usando o portal do Azure.

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
//The event hub that is created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName, 
    new EventHubDataConnection(eventHubResourceId, consumerGroup, location: location, tableName: tableName, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Definição** | **Valor sugerido** | **Descrição do campo**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Sua ID de locatário. Também conhecida como ID de diretório.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | A ID da assinatura que você usa para a criação de recursos.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | A ID do cliente do aplicativo que pode acessar recursos em seu locatário.|
| clientSecret | *xxxxxxxxxxxxxx* | O segredo do cliente do aplicativo que pode acessar recursos em seu locatário.|
| resourceGroupName | *testrg* | O nome do grupo de recursos que contém o cluster.|
| clusterName | *mykustocluster* | O nome do cluster.|
| databaseName | *mykustodatabase* | O nome do banco de dados de destino no cluster.|
| dataconnectionname | *myeventhubconnect* | O nome desejado da sua conexão de dados.|
| tableName | *StormEvents* | O nome da tabela de destino no banco de dados de destino.|
| mappingRuleName | *StormEvents_CSV_Mapping* | O nome do mapeamento de coluna relacionado à tabela de destino.|
| Formato de DataFormat | *CSV* | O formato de dados da mensagem.|
| eventHubResourceId | *ID do recurso* | A ID de recurso do hub de eventos que contém os dados para ingestão. |
| consumerGroup | *$Default* | O grupo de consumidores do hub de eventos.|
| location | *EUA Central* | O local do recurso de conexão de dados.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
