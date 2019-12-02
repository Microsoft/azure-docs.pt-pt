---
title: Criar um cluster de Data Explorer do Azure e um banco de dados usandoC#
description: Saiba como criar um cluster de Data Explorer do Azure e um banco de dados usando oC#
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 1260b7754f80a3d008084f632d299c684162628e
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667884"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Criar um cluster de Data Explorer do Azure e um banco de dados usandoC#

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Modelo do Azure Resource Manager](create-cluster-database-resource-manager.md)

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerido que permite realizar análises em tempo real em volumes grandes de transmissão de dados a partir de aplicações, sites, dispositivos IoT e muito mais. Para usar o Azure Data Explorer, primeiro crie um cluster e crie um ou mais bancos de dados nesse cluster. Em seguida, você pode ingerir (carregar) dados em um banco de dado para poder executar consultas nele. Neste artigo, você cria um cluster e um banco de dados usando C#o.

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver o Visual Studio 2019 instalado, poderá baixar e usar o [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)gratuito. Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.
* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="install-c-nuget"></a>Instalar C# o NuGet

* Instale o [pacote NuGet do Azure data Explorer (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Instale o [pacote NuGet Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) para autenticação.

## <a name="authentication"></a>Autenticação
Para executar os exemplos neste artigo, precisamos de um aplicativo do Azure AD e uma entidade de serviço que possa acessar recursos. Marque [criar um aplicativo do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) para criar um aplicativo gratuito do Azure AD e adicionar a atribuição de função no escopo da assinatura. Ele também mostra como obter as `Directory (tenant) ID`, `Application ID`e `Client Secret`.

## <a name="create-the-azure-data-explorer-cluster"></a>Criar o cluster de Data Explorer do Azure

1. Crie seu cluster usando o seguinte código:

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
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var cluster = new Cluster(location, sku);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```

   |**Definição** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | clusterName | *mykustocluster* | O nome desejado do cluster.|
   | skuName | *Standard_D13_v2* | A SKU que será usada para o cluster. |
   | tier | *Standard* | A camada de SKU. |
   | capacidade | *automática* | O número de instâncias do cluster. |
   | resourceGroupName | *testrg* | O nome do grupo de recursos em que o cluster será criado. |

    > [!NOTE]
    > **Criar um cluster** é uma operação de execução demorada, portanto, é altamente recomendável usar CreateOrUpdateAsync, em vez de CreateOrUpdate. 

1. Execute o seguinte comando para verificar se o cluster foi criado com êxito:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Se o resultado contiver `ProvisioningState` com o valor `Succeeded`, o cluster foi criado com êxito.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Criar o banco de dados no cluster de Data Explorer do Azure

1. Crie seu banco de dados usando o seguinte código:

    ```csharp
    var hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    var softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    var databaseName = "mykustodatabase";
    var database = new Database(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);

    await kustoManagementClient.Databases.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, database);
    ```

   |**Definição** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | clusterName | *mykustocluster* | O nome do cluster em que o banco de dados será criado.|
   | NomeDoBancoDeDados | *mykustodatabase* | O nome do seu banco de dados.|
   | resourceGroupName | *testrg* | O nome do grupo de recursos em que o cluster será criado. |
   | softDeletePeriod | *3650:00:00:00* | A quantidade de tempo que os dados serão mantidos disponíveis para consulta. |
   | hotCachePeriod | *3650:00:00:00* | A quantidade de tempo que os dados serão mantidos no cache. |

2. Execute o seguinte comando para ver o banco de dados que você criou:

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName);
    ```

Agora você tem um cluster e um banco de dados.

## <a name="clean-up-resources"></a>Limpar recursos

* Se você planeja seguir nossos outros artigos, mantenha os recursos que você criou.
* Para limpar os recursos, exclua o cluster. Quando você exclui um cluster, ele também exclui todos os bancos de dados nele. Use o seguinte comando para excluir o cluster:

    ```csharp
    kustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>Passos seguintes

* [Ingerir dados usando o SDK do Azure Data Explorer .NET Standard (versão prévia)](net-standard-ingest-data.md)
