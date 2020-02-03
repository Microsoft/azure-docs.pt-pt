---
title: Adicione os principais do cluster para o Azure Data Explorer utilizando python
description: Neste artigo, aprende-se a adicionar diretores de cluster para o Azure Data Explorer utilizando python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 637efdfe31d1f2eb0eaa5dd532dd9e9e67de5ce2
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965141"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-python"></a>Adicione os principais do cluster para o Azure Data Explorer utilizando python

> [!div class="op_single_selector"]
> * [C#](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Modelo do Azure Resource Manager](cluster-principal-resource-manager.md)

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. Neste artigo, você adiciona diretores de cluster para O Explorador de Dados Azure usando Python.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Criar um cluster.](create-cluster-database-python.md)

## <a name="install-python-package"></a>Instalar pacote do Python

Para instalar o pacote do Python para o Data Explorer do Azure (Kusto), abra um prompt de comando que tenha o Python em seu caminho. Execute este comando:

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-cluster-principal"></a>Adicione um diretor de cluster

O exemplo que se segue mostra como adicionar um cluster principal programáticamente.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import ClusterPrincipalAssignment
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster that is created as part of the Prerequisites
cluster_name = "mykustocluster"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.cluster_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, principal_assignment_name= principal_assignment_name, parameters=ClusterPrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**Definição** | **Valor sugerido** | **Descrição do campo**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | Sua ID de locatário. Também conhecido como ID diretório.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | A ID da assinatura que você usa para a criação de recursos.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | A ID do cliente do aplicativo que pode acessar recursos em seu locatário.|
| client_secret | *xxxxxxxxxxxxxx* | O segredo do cliente do aplicativo que pode acessar recursos em seu locatário. |
| resource_group_name | *testrg* | O nome do grupo de recursos que contém o seu cluster.|
| cluster_name | *mykustocluster* | O nome do seu aglomerado.|
| principal_assignment_name | *clusterPrincipalAssignment1* | O nome do seu recurso principal do cluster.|
| principal_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | O ID principal, que pode ser o e-mail do utilizador, o ID da aplicação ou o nome do grupo de segurança.|
| role | *AllDatabasesAdmin* | O papel do seu diretor de cluster, que pode ser 'AllDatabasesAdmin' ou 'AllDatabasesViewer'.|
| tenant_id_for_principal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | A identificação do inquilino do diretor.|
| principal_type | *Aplicação* | O tipo de comitente, que pode ser 'Utilizador', 'App' ou 'Grupo'|

## <a name="next-steps"></a>Passos seguintes

* [Adicionar diretores de base de dados](database-principal-python.md)
