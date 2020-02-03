---
title: Adicione os principais da base de dados para o Azure Data Explorer utilizandoC#
description: Neste artigo, aprende-se a adicionar os principais da C#base de dados do Azure Data Explorer utilizando .
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 797d1253d44739f2026563e3df72bc85a8ef382e
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965037"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-c"></a>Adicione os principais da base de dados para o Azure Data Explorer utilizandoC#

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Modelo do Azure Resource Manager](database-principal-resource-manager.md)

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. Neste artigo, adicione os principais da base C#de dados do Azure Data Explorer utilizando .

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver o Visual Studio 2019 instalado, poderá baixar e usar o [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)gratuito. Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.
* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Criar um cluster e base de dados.](create-cluster-database-csharp.md)

## <a name="install-c-nuget"></a>Instalar C# o NuGet

* Instale [Microsoft. Azure. Management. Kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Instale [microsoft.rest.clientRuntime.Azure.Autenticação](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) para autenticação.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Adicione um diretor de base de dados

O exemplo que se segue mostra como adicionar uma base de dados principal programáticamente.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
string principalAssignmentName = "databasePrincipalAssignment1";
string principalId = "xxxxxxxx";//User email, application ID, or security group name
string role = "Admin";//Admin, Ingestor, Monitor, User, UnrestrictedViewers, Viewer
string tenantIdForPrincipal = tenantId;
string principalType = "App";//User, App, or Group

var databasePrincipalAssignment = new DatabasePrincipalAssignment(principalId, role, principalType, tenantId: tenantIdForPrincipal);
await kustoManagementClient.DatabasePrincipalAssignments.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, principalAssignmentName, databasePrincipalAssignment);
```

|**Definição** | **Valor sugerido** | **Descrição do campo**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | Sua ID de locatário. Também conhecido como ID diretório.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | A ID da assinatura que você usa para a criação de recursos.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | A ID do cliente do aplicativo que pode acessar recursos em seu locatário.|
| clientSecret | *xxxxxxxxxxxxxx* | O segredo do cliente do aplicativo que pode acessar recursos em seu locatário. |
| resourceGroupName | *testrg* | O nome do grupo de recursos que contém o seu cluster.|
| clusterName | *mykustocluster* | O nome do seu aglomerado.|
| databaseName | *mykustodatabase* | O nome do seu banco de dados.|
| nome principal de atribuição | *base de dadosPrincipalAssignment1* | O nome do recurso principal da sua base de dados.|
| principalId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | O ID principal, que pode ser o e-mail do utilizador, o ID da aplicação ou o nome do grupo de segurança.|
| role | *ADM* | O papel do seu principal base de dados, que pode ser 'Administrador', 'Ingestor', 'Monitor', 'User', 'UnrestrictedViewers', 'Viewer'.|
| inquilinoIdForPrincipal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | A identificação do inquilino do diretor.|
| principalType | *Aplicação* | O tipo de comitente, que pode ser 'Utilizador', 'App' ou 'Grupo'|

## <a name="next-steps"></a>Passos seguintes

* [Ingerir dados utilizando a biblioteca do Nó do Explorador de Dados Azure](node-ingest-data.md)
