---
title: Criar uma conexão de dados do Hub IoT para o Azure Data Explorer usandoC#
description: Neste artigo, você aprenderá a criar uma conexão de dados do Hub IoT para o Azure Data Explorer C#usando o.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: bcc80000be5e061a37601f05a2a245aac031fc15
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72031659"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-c-preview"></a>Criar uma conexão de dados do Hub IoT para o Azure C# data Explorer usando (visualização)

> [!div class="op_single_selector"]
> * [Portal](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. O Azure Data Explorer oferece ingestão (carregamento de dados) de hubs de eventos, hubs IoT e Blobs gravados em contêineres de BLOB. Neste artigo, você cria uma conexão de dados do Hub IoT para o Azure Data Explorer C#usando o.

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver o Visual Studio 2019 instalado, poderá baixar e usar o [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)gratuito. Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

* Criar [um cluster e um banco de dados](create-cluster-database-csharp.md)

* Criar [mapeamento de tabela e coluna](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)

* Definir [políticas de banco de dados e tabela](database-table-policies-csharp.md) (opcional)

* Crie um [Hub IOT com uma política de acesso compartilhado configurada](ingest-data-iot-hub.md#create-an-iot-hub).

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Adicionar uma conexão de dados do Hub IoT 

O exemplo a seguir mostra como adicionar uma conexão de dados do Hub IoT programaticamente. Consulte [conectar a tabela de data Explorer do Azure ao Hub IOT](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) para adicionar uma conexão de dados do Hub IOT usando o portal do Azure.

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
//The IoT hub that is created as part of the Prerequisites
var iotHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
var sharedAccessPolicyName = "iothubforread";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdate(resourceGroupName, clusterName, databaseName, dataConnectionName,
            new IotHubDataConnection(iotHubResourceId, consumerGroup, sharedAccessPolicyName, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Definição** | **Valor sugerido** | **Descrição do campo**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Sua ID de locatário. Também conhecida como ID de diretório.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | A ID da assinatura que você usa para a criação de recursos.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | A ID do cliente do aplicativo que pode acessar recursos em seu locatário.|
| clientSecret | *xxxxxxxxxxxxxx* | O segredo do cliente do aplicativo que pode acessar recursos em seu locatário. |
| resourceGroupName | *testrg* | O nome do grupo de recursos que contém o cluster.|
| clusterName | *mykustocluster* | O nome do cluster.|
| databaseName | *mykustodatabase* | O nome do banco de dados de destino no cluster.|
| dataconnectionname | *myeventhubconnect* | O nome desejado da sua conexão de dados.|
| tableName | *StormEvents* | O nome da tabela de destino no banco de dados de destino.|
| mappingRuleName | *StormEvents_CSV_Mapping* | O nome do mapeamento de coluna relacionado à tabela de destino.|
| Formato de DataFormat | *CSV* | O formato de dados da mensagem.|
| iotHubResourceId | *ID do recurso* | A ID de recurso do Hub IoT que contém os dados para ingestão. |
| sharedAccessPolicyName | *iothubforread* | O nome da política de acesso compartilhado que define as permissões para dispositivos e serviços para se conectar ao Hub IoT. |
| consumerGroup | *$Default* | O grupo de consumidores do hub de eventos.|
| location | *EUA Central* | O local do recurso de conexão de dados.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
