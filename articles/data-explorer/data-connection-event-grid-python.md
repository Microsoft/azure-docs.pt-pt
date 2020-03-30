---
title: Criar uma ligação de dados da Rede de Eventos para o Azure Data Explorer utilizando python
description: Neste artigo, aprende-se a criar uma ligação de dados da Rede de Eventos para o Azure Data Explorer utilizando o Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 1439383598517f57bc77e718d4ded7f53941d3bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444201"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-python"></a>Criar uma ligação de dados da Rede de Eventos para o Azure Data Explorer utilizando python

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C #](data-connection-event-grid-csharp.md)
> * [Pitão](data-connection-event-grid-python.md)
> * [Modelo Azure Resource Manager](data-connection-event-grid-resource-manager.md)

Neste artigo, cria uma ligação de dados da Rede de Eventos para o Azure Data Explorer utilizando o Python. O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. O Azure Data Explorer oferece ingestão, ou carregamento de dados, a partir de Hubs de Eventos, Hubs IoT e bolhas escritas para recipientes blob.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Python 3.4+](https://www.python.org/downloads/).

* [Um cluster e base de dados.](create-cluster-database-python.md)

* [Mapeamento de mesa e coluna.](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)

* [Bases de dados e políticas de mesa](database-table-policies-csharp.md) (opcional).

* [Uma conta de armazenamento com uma subscrição da Grelha de Eventos.](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account)

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Adicione uma ligação de dados da Grelha de Eventos

O exemplo que se segue mostra como adicionar uma ligação de dados da Rede de Eventos programáticamente. Consulte a criação de uma ligação de dados da Rede de [Eventos no Azure Data Explorer](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) para adicionar uma ligação de dados da Rede de Eventos utilizando o portal Azure.


```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventGridDataConnection
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
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
data_connection_name = "myeventhubconnect"
#The event hub and storage account that are created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx"
storage_account_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx"
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=EventGridDataConnection(storage_account_resource_id=storage_account_resource_id, event_hub_resource_id=event_hub_resource_id, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```
|**Definição** | **Valor sugerido** | **Descrição do campo**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | Sua identificação do inquilino. Também conhecido como ID diretório.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | O ID de subscrição que utiliza para a criação de recursos.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | A identificação do cliente da aplicação que pode aceder a recursos no seu inquilino.|
| client_secret | *xxxxxxxxxxxxxx* | O segredo do cliente da aplicação que pode aceder a recursos no seu inquilino. |
| resource_group_name | *testrg* | O nome do grupo de recursos que contém o seu cluster.|
| cluster_name | *mykustocluster* | O nome do seu aglomerado.|
| database_name | *mykustodatabase* | O nome da base de dados do alvo no seu cluster.|
| data_connection_name | *myeventhubconnect* | O nome desejado da sua ligação de dados.|
| table_name | *Eventos de Tempestade* | O nome da tabela-alvo na base de dados do alvo.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | O nome do mapeamento da coluna relacionado com a mesa-alvo.|
| data_format | *csv* | O formato de dados da mensagem.|
| event_hub_resource_id | *ID de recursos* | O ID de recursos do seu Hub de Eventos onde a Grelha de Eventos está configurada para enviar eventos. |
| storage_account_resource_id | *ID de recursos* | A identificação de recursos da sua conta de armazenamento que detém os dados para ingestão. |
| consumer_group | *$Default* | O grupo de consumidores do seu Hub de Eventos.|
| localização | *E.U.A. Central* | A localização do recurso de ligação de dados.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]