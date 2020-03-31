---
title: Adicione os principais da base de dados para o Azure Data Explorer utilizando python
description: Neste artigo, aprende-se a adicionar os principais da base de dados do Azure Data Explorer utilizando o Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 8b9c4f4d5427b326c273558db0bff808068b192a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965011"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-python"></a>Adicione os principais da base de dados para o Azure Data Explorer utilizando python

> [!div class="op_single_selector"]
> * [C #](database-principal-csharp.md)
> * [Pitão](database-principal-python.md)
> * [Modelo Azure Resource Manager](database-principal-resource-manager.md)

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. Neste artigo, adiciona os principais da base de dados do Azure Data Explorer utilizando o Python.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Criar um cluster e uma base de dados](create-cluster-database-python.md)

## <a name="install-python-package"></a>Instalar pacote Python

Para instalar o pacote Python para o Azure Data Explorer (Kusto), abra uma solicitação de comando que tenha python no seu caminho. Execute este comando:

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Adicione um diretor de base de dados

O exemplo que se segue mostra como adicionar uma base de dados principal programáticamente.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabasePrincipalAssignment
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
#The cluster and database that is created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.database_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, principal_assignment_name= principal_assignment_name, parameters=DatabasePrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**Definição** | **Valor sugerido** | **Descrição do campo**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | Sua identificação do inquilino. Também conhecido como ID diretório.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | O ID de subscrição que utiliza para a criação de recursos.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | A identificação do cliente da aplicação que pode aceder a recursos no seu inquilino.|
| client_secret | *xxxxxxxxxxxxxx* | O segredo do cliente da aplicação que pode aceder a recursos no seu inquilino. |
| resource_group_name | *testrg* | O nome do grupo de recursos que contém o seu cluster.|
| cluster_name | *mykustocluster* | O nome do seu aglomerado.|
| database_name | *mykustodatabase* | O nome da sua base de dados.|
| principal_assignment_name | *base de dadosPrincipalAssignment1* | O nome do recurso principal da sua base de dados.|
| principal_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | O ID principal, que pode ser o e-mail do utilizador, o ID da aplicação ou o nome do grupo de segurança.|
| papel | *Administração* | O papel do seu principal base de dados, que pode ser 'Administrador', 'Ingestor', 'Monitor', 'User', 'UnrestrictedViewers', 'Viewer'.|
| tenant_id_for_principal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | A identificação do inquilino do diretor.|
| principal_type | *Aplicação* | O tipo de comitente, que pode ser 'Utilizador', 'App' ou 'Grupo'|

## <a name="next-steps"></a>Passos seguintes

* [Ingerir dados utilizando a biblioteca Python do Explorador de Dados Azure](python-ingest-data.md)
