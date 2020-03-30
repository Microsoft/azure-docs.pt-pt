---
title: 'Criar uma ligação de dados do Hub de Eventos para o Azure Data Explorer utilizando C #'
description: Neste artigo, aprende-se a criar uma ligação de dados do Event Hub para o Azure Data Explorer utilizando o C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: cf2a274b4f48b31739d6abba5cf87fa2a10d4ca1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667683"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-c"></a>Criar uma ligação de dados do Hub de Eventos para o Azure Data Explorer utilizando C #

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-hub.md)
> * [C #](data-connection-event-hub-csharp.md)
> * [Pitão](data-connection-event-hub-python.md)
> * [Modelo Azure Resource Manager](data-connection-event-hub-resource-manager.md)

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. O Azure Data Explorer oferece ingestão (carregamento de dados) de Centros de Eventos, Hubs IoT e bolhas escritas para recipientes blob. Neste artigo, cria uma ligação de dados do Event Hub para o Azure Data Explorer utilizando o C#.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver o Visual Studio 2019 instalado, pode descarregar e utilizar a Edição Comunitária **gratuita** do [Visual Studio 2019.](https://www.visualstudio.com/downloads/) Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.
* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* Criar [um cluster e base de dados](create-cluster-database-csharp.md)
* Criar [mapeamento](net-standard-ingest-data.md#create-a-table-on-your-test-cluster) de mesa e coluna
* Definir bases de [dados e políticas de tabela](database-table-policies-csharp.md) (opcional)
* Crie um Hub de [Eventos com dados para ingestão.](ingest-data-event-hub.md#create-an-event-hub) 

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Adicione uma ligação de dados do Event Hub

O exemplo que se segue mostra como adicionar uma ligação de dados do Event Hub programáticamente. Consulte [a ligação ao centro de eventos](ingest-data-event-hub.md#connect-to-the-event-hub) para adicionar uma ligação de dados do Event Hub utilizando o portal Azure.

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
| inquilinoId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | Sua identificação do inquilino. Também conhecido como ID diretório.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | O ID de subscrição que utiliza para a criação de recursos.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | A identificação do cliente da aplicação que pode aceder a recursos no seu inquilino.|
| clienteSecret | *xxxxxxxxxxxxxx* | O segredo do cliente da aplicação que pode aceder a recursos no seu inquilino.|
| resourceGroupName | *testrg* | O nome do grupo de recursos que contém o seu cluster.|
| clusterName | *mykustocluster* | O nome do seu aglomerado.|
| nome da base de dados | *mykustodatabase* | O nome da base de dados do alvo no seu cluster.|
| dataConnectionName | *myeventhubconnect* | O nome desejado da sua ligação de dados.|
| tableName | *Eventos de Tempestade* | O nome da tabela-alvo na base de dados do alvo.|
| mapeandoORuleName | *StormEvents_CSV_Mapping* | O nome do mapeamento da coluna relacionado com a mesa-alvo.|
| dataFormat | *csv* | O formato de dados da mensagem.|
| eventoHubResourceId | *ID de recursos* | O ID de recursos do seu Hub de Eventos que detém os dados para ingestão. |
| consumerGroup | *$Default* | O grupo de consumidores do seu Hub de Eventos.|
| localização | *E.U.A. Central* | A localização do recurso de ligação de dados.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
