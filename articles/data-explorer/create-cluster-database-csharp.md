---
title: Criar um cluster e DB do Explorer de Dados Azure usandoC#
description: Saiba como criar um cluster e base de dados do Azure Data Explorer utilizando oC#
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 0c32d438ac8551f061343edb747e9fc035b498e2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246412"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Criar um cluster azure Data Explorer e base de dados usandoC#

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [python](create-cluster-database-python.md)
> * [Modelo do Azure Resource Manager](create-cluster-database-resource-manager.md)

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerido que permite realizar análises em tempo real em volumes grandes de transmissão de dados a partir de aplicações, sites, dispositivos IoT e muito mais. Para utilizar o Azure Data Explorer, cria-se primeiro um cluster e cria-se uma ou mais bases de dados nesse cluster. Em seguida, ingere (carregar) dados numa base de dados para que possa fazer perguntas contra ele. Neste artigo, cria-se um cluster C#e uma base de dados utilizando .

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver o Visual Studio 2019 instalado, pode descarregar e utilizar a Edição Comunitária **gratuita** do [Visual Studio 2019.](https://www.visualstudio.com/downloads/) Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.
* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

## <a name="authentication"></a>Autenticação
Para executar os exemplos neste artigo, precisamos de um Serviço De Aplicação E serviço Azure AD que possa aceder a recursos. Verifique [criar uma aplicação Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) para criar uma Aplicação AD Azure gratuita e adicionar atribuição de funções no âmbito de subscrição. Mostra também como obter o `Directory (tenant) ID`, `Application ID`e `Client Secret`.

## <a name="create-the-azure-data-explorer-cluster"></a>Criar o cluster Azure Data Explorer

1. Crie o seu cluster utilizando o seguinte código:

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
   | clusterName | *mykustocluster* | O nome desejado do seu aglomerado.|
   | skuName | *Standard_D13_v2* | O SKU que será usado para o seu cluster. |
   | tier | *Standard* | O nível SKU. |
   | capacidade | *número* | O número de casos do aglomerado. |
   | resourceGroupName | *testrg* | O nome do grupo de recursos onde o cluster será criado. |

    > [!NOTE]
    > **Criar um cluster** é uma operação de longo prazo, por isso é altamente recomendado utilizar o CreateOrUpdateAsync, em vez do CreateOrUpdate. 

1. Execute o seguinte comando para verificar se o seu cluster foi criado com sucesso:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Se o resultado contiver `ProvisioningState` com o valor `Succeeded`, então o cluster foi criado com sucesso.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Criar a base de dados no cluster Azure Data Explorer

1. Crie a sua base de dados utilizando o seguinte código:

    ```csharp
    var hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    var softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    var databaseName = "mykustodatabase";
    var database = new ReadWriteDatabase(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);

    await kustoManagementClient.Databases.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, database);
    ```

        [!NOTE]
        If you are using C# version 2.0.0 or below, use Database instead of ReadWriteDatabase.

   |**Definição** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | clusterName | *mykustocluster* | O nome do seu cluster onde será criada a base de dados.|
   | databaseName | *mykustodatabase* | O nome da sua base de dados.|
   | resourceGroupName | *testrg* | O nome do grupo de recursos onde o cluster será criado. |
   | softDeletePeriod | *3650:00:00:00* | O tempo que os dados serão mantidos disponíveis para consulta. |
   | hotCachePeriod | *3650:00:00:00* | A quantidade de tempo que os dados serão mantidos em cache. |

2. Execute o seguinte comando para ver a base de dados que criou:

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName) as ReadWriteDatabase;
    ```

Agora tem um cluster e uma base de dados.

## <a name="clean-up-resources"></a>Limpar recursos

* Se planeia seguir os nossos outros artigos, guarde os recursos que criou.
* Para limpar os recursos, elimine o cluster. Ao eliminar um cluster, também elimina todas as bases de dados do mesmo. Utilize o seguinte comando para eliminar o seu cluster:

    ```csharp
    kustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>Passos seguintes

* [Dados de ingestão utilizando o Azure Data Explorer .NET Standard SDK (Pré-visualização)](net-standard-ingest-data.md)
