---
title: 'Adicione os principais da base de dados para o Azure Data Explorer utilizando C #'
description: Neste artigo, aprende-se a adicionar os principais da base de dados do Azure Data Explorer utilizando c#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 797d1253d44739f2026563e3df72bc85a8ef382e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965037"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-c"></a>Adicione os principais da base de dados para o Azure Data Explorer utilizando C #

> [!div class="op_single_selector"]
> * [C #](database-principal-csharp.md)
> * [Pitão](database-principal-python.md)
> * [Modelo Azure Resource Manager](database-principal-resource-manager.md)

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. Neste artigo, adiciona os principais da base de dados do Azure Data Explorer utilizando C#.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver o Visual Studio 2019 instalado, pode descarregar e utilizar a Edição Comunitária **gratuita** do [Visual Studio 2019.](https://www.visualstudio.com/downloads/) Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.
* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Criar um cluster e base de dados.](create-cluster-database-csharp.md)

## <a name="install-c-nuget"></a>Instalar C# NuGet

* Instale [microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
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
| inquilinoId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | Sua identificação do inquilino. Também conhecido como ID diretório.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | O ID de subscrição que utiliza para a criação de recursos.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | A identificação do cliente da aplicação que pode aceder a recursos no seu inquilino.|
| clienteSecret | *xxxxxxxxxxxxxx* | O segredo do cliente da aplicação que pode aceder a recursos no seu inquilino. |
| resourceGroupName | *testrg* | O nome do grupo de recursos que contém o seu cluster.|
| clusterName | *mykustocluster* | O nome do seu aglomerado.|
| nome da base de dados | *mykustodatabase* | O nome da sua base de dados.|
| nome principal de atribuição | *base de dadosPrincipalAssignment1* | O nome do recurso principal da sua base de dados.|
| principais | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | O ID principal, que pode ser o e-mail do utilizador, o ID da aplicação ou o nome do grupo de segurança.|
| papel | *Administração* | O papel do seu principal base de dados, que pode ser 'Administrador', 'Ingestor', 'Monitor', 'User', 'UnrestrictedViewers', 'Viewer'.|
| inquilinoIdForPrincipal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | A identificação do inquilino do diretor.|
| principalType | *Aplicação* | O tipo de comitente, que pode ser 'Utilizador', 'App' ou 'Grupo'|

## <a name="next-steps"></a>Passos seguintes

* [Ingerir dados utilizando a biblioteca do Nó do Explorador de Dados Azure](node-ingest-data.md)
