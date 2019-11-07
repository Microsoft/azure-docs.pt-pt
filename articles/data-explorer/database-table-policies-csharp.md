---
title: Criar políticas usando o SDK de Data Explorer C# do Azure
description: Neste artigo, você aprenderá a criar políticas usando C#o.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 8a5ea692bfdec7f676a80cc670f686af66152e6f
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606610"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-c"></a>Criar políticas de banco de dados e tabela para o Azure Data Explorer usandoC#

> [!div class="op_single_selector"]
> * [C#](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. Neste artigo, você criará políticas de banco de dados e de tabela para o C#Azure data Explorer usando o.

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio 2019. Se você não tiver o Visual Studio 2019, poderá baixar e usar o [visual Studio Community 2019](https://www.visualstudio.com/downloads/)gratuito. Certifique-se de selecionar **desenvolvimento do Azure** durante a instalação do Visual Studio.

* Uma subscrição do Azure. Se precisar, você pode criar uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

* [Um cluster de teste e um banco de dados](create-cluster-database-csharp.md).

* [Uma tabela de teste](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

## <a name="install-c-nuget"></a>Instalar C# o NuGet

* Instale o [pacote NuGet do Azure data Explorer (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

* Instale o [pacote NuGet Microsoft. Azure. Kusto. Data. netstandard](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/). (Opcional, para alterar as políticas de tabela.)

* Instale o [pacote NuGet Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/), para autenticação.

## <a name="authentication"></a>Autenticação
Para executar os exemplos neste artigo, você precisa de um aplicativo Azure Active Directory (Azure AD) e uma entidade de serviço que possa acessar recursos. Você pode usar o mesmo aplicativo do Azure AD para autenticação de [um cluster de teste e banco de dados](create-cluster-database-csharp.md#authentication). Se você quiser usar um aplicativo diferente do Azure AD, consulte [criar um aplicativo do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) para criar um aplicativo gratuito do Azure AD e adicionar a atribuição de função no escopo da assinatura. Este artigo também mostra como obter as `Directory (tenant) ID`, `Application ID`e `Client secret`. Talvez seja necessário adicionar o novo aplicativo do Azure AD como uma entidade de segurança no banco de dados. Para obter mais informações, consulte [Manage Azure data Explorer Database Permissions](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions).

## <a name="alter-database-retention-policy"></a>Política de retenção de ALTER DATABASE
Define uma política de retenção com um período de exclusão reversível de 10 dias.
    
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

## <a name="alter-database-cache-policy"></a>Política de cache de ALTER DATABASE
Define uma política de cache para o banco de dados. Os cinco dias anteriores de dados estarão no SSD do cluster.

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

## <a name="alter-table-cache-policy"></a>Alterar política de cache de tabela
Define uma política de cache para a tabela. Os cinco dias anteriores de dados estarão no SSD do cluster.

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

## <a name="add-a-new-principal-for-the-database"></a>Adicionar uma nova entidade de segurança para o banco de dados
Adiciona um novo aplicativo do Azure AD como entidade de segurança do banco de dados.

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

* [Leia mais sobre políticas de banco de dados e tabela](https://docs.microsoft.com/azure/kusto/management/policies)
