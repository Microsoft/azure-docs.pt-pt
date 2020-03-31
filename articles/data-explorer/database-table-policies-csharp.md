---
title: Criar políticas utilizando o Azure Data Explorer C# SDK
description: Neste artigo, você vai aprender a criar políticas usando C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 17312840b0081056ad04723f2b2c241c47902021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667296"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-c"></a>Criar políticas de base de dados e tabelas para o Azure Data Explorer utilizando C #

> [!div class="op_single_selector"]
> * [C #](database-table-policies-csharp.md)
> * [Pitão](database-table-policies-python.md)
>

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. Neste artigo, irá criar políticas de base de dados e tabelas para o Azure Data Explorer utilizando C#.

## <a name="prerequisites"></a>Pré-requisitos

* Estúdio Visual 2019. Se não tiver o Visual Studio 2019, pode descarregar e utilizar o [Visual Studio Community 2019](https://www.visualstudio.com/downloads/) *gratuito* . Certifique-se de selecionar o **desenvolvimento do Azure** durante a configuração do Estúdio Visual.
* Uma subscrição do Azure. Se precisar, pode criar uma [conta Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Um cluster de teste e base de dados.](create-cluster-database-csharp.md)
* [Uma mesa de teste.](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)

## <a name="install-c-nuget"></a>Instalar C# NuGet

* Instale o pacote NuGet do [Azure Data Explorer (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Instale o [pacote Microsoft.Azure.Kusto.Data.NETStandard NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/). (Opcional, para mudar as políticas de tabela.)
* Instale o [pacote Microsoft.IdentityModel.Clients.ActiveDirectory NuGet,](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)para autenticação.

## <a name="authentication"></a>Autenticação
Para executar os exemplos neste artigo, você precisa de uma aplicação azure Ative Directory (Azure AD) e diretor de serviço que possa aceder a recursos. Pode utilizar a mesma aplicação Azure AD para autenticação a partir de [um cluster de teste e base de dados](create-cluster-database-csharp.md#authentication). Se pretender utilizar uma aplicação Azure AD diferente, consulte [criar uma aplicação Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) para criar uma aplicação Azure AD gratuita e adicionar atribuição de funções no âmbito de subscrição. Este artigo também mostra `Directory (tenant) ID`como `Application ID`obter `Client secret`o, e . Pode ser necessário adicionar a nova aplicação Azure AD como principal na base de dados. Para mais informações, consulte ['Manage Azure Data Explorer')](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions)

## <a name="alter-database-retention-policy"></a>Alterar a política de retenção de bases de dados
Estabelece uma política de retenção com um período de eliminação suave de 10 dias.
    
```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
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
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(softDeletePeriod: TimeSpan.FromDays(10)));
```

## <a name="alter-database-cache-policy"></a>Alterar a política de cache de base de dados
Estabelece uma política de cache para a base de dados. Os cinco dias anteriores de dados serão no cluster SSD.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
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
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(hotCachePeriod: TimeSpan.FromDays(5)));
```

## <a name="alter-table-cache-policy"></a>Alterar política de cache de mesa
Estabelece uma política de cache para a mesa. Os cinco dias anteriores de dados serão no cluster SSD.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var databaseName = "<DatabaseName>";
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var tableName = "<TableName>"

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = databaseName,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    //dataHotSpan and indexHotSpan should have the same value
    var hotSpan = TimeSpan.FromDays(5);
    var command1 = CslCommandGenerator.GenerateAlterTableCachingPolicyCommand(tableName: tableName,
                    dataHotSpan: hotSpan, indexHotSpan: hotSpan);

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="add-a-new-principal-for-the-database"></a>Adicione um novo diretor para a base de dados
Adiciona uma nova aplicação Azure AD como diretor a dinária para a base de dados.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var clientIdToAdd = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
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
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.AddPrincipalsAsync(resourceGroupName, clusterName, databaseName,
                new DatabasePrincipalListRequest()
                {
                    Value = new List<DatabasePrincipal>()
                    {
                        new DatabasePrincipal("Admin", "<database_principle_name>", "App", appId: clientIdToAdd, tenantName:tenantId)
                    }
                });
```
## <a name="next-steps"></a>Passos seguintes

* [Ler mais sobre bases de dados e políticas de tabela](https://docs.microsoft.com/azure/kusto/management/policies)
