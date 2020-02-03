---
title: Criar uma ligação de dados do Hub de Eventos para o Azure Data Explorer utilizando python
description: Neste artigo, aprende-se a criar uma ligação de dados do Event Hub para o Azure Data Explorer utilizando o Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 81aded7639cc0bed86c3d3ab3be9e6ef7b355734
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964537"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-python"></a>Criar uma ligação de dados do Hub de Eventos para o Azure Data Explorer utilizando python

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Modelo do Azure Resource Manager](data-connection-event-hub-resource-manager.md)

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. O Azure Data Explorer oferece ingestão (carregamento de dados) de Centros de Eventos, Hubs IoT e bolhas escritas para recipientes blob. Neste artigo, cria-se uma ligação de dados do Event Hub para o Azure Data Explorer utilizando o Python.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* Criar [um cluster e base de dados](create-cluster-database-python.md)
* Criar [mapeamento](python-ingest-data.md#create-a-table-on-your-cluster) de mesa e coluna
* Definir bases de [dados e políticas de tabela](database-table-policies-python.md) (opcional)
* Crie um Hub de [Eventos com dados para ingestão.](ingest-data-event-hub.md#create-an-event-hub) 

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Adicione uma ligação de dados do Event Hub

O exemplo que se segue mostra como adicionar uma ligação de dados do Event Hub programáticamente. Consulte [a ligação ao centro de eventos](ingest-data-event-hub.md#connect-to-the-event-hub) para adicionar uma ligação de dados do Event Hub utilizando o portal Azure.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventHubDataConnection
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
#The event hub that is created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"
#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                        parameters=EventHubDataConnection(event_hub_resource_id=event_hub_resource_id, consumer_group=consumer_group, location=location,
                                                                            table_name=table_name, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**Definição** | **Valor sugerido** | **Descrição do campo**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | Sua ID de locatário. Também conhecido como ID diretório.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | A ID da assinatura que você usa para a criação de recursos.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxx* | A ID do cliente do aplicativo que pode acessar recursos em seu locatário.|
| client_secret | *xxxxxxxxxxxxxx* | O segredo do cliente do aplicativo que pode acessar recursos em seu locatário. |
| resource_group_name | *testrg* | O nome do grupo de recursos que contém o seu cluster.|
| cluster_name | *mykustocluster* | O nome do seu aglomerado.|
| database_name | *mykustodatabase* | O nome da base de dados do alvo no seu cluster.|
| data_connection_name | *myeventhubconnect* | O nome desejado da sua ligação de dados.|
| table_name | *Eventos de Tempestade* | O nome da tabela-alvo na base de dados do alvo.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | O nome do mapeamento da coluna relacionado com a mesa-alvo.|
| data_format | *csv* | O formato de dados da mensagem.|
| event_hub_resource_id | *ID de recursos* | O ID de recursos do seu Hub de Eventos que detém os dados para ingestão. |
| consumer_group | *$Default* | O grupo de consumidores do seu Hub de Eventos.|
| localização | *Centro dos EUA* | A localização do recurso de ligação de dados.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]