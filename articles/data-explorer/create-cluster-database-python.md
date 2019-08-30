---
title: Criar um cluster de Data Explorer do Azure e um banco de dados usando Python
description: Saiba como criar um cluster de Data Explorer do Azure e um banco de dados usando o Python.
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: e3f58e596db26c04a8f3be4f87eb129fadf5e328
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141743"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Criar um cluster de Data Explorer do Azure e um banco de dados usando Python

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerido que permite realizar análises em tempo real em volumes grandes de transmissão de dados a partir de aplicações, sites, dispositivos IoT e muito mais. Para usar o Azure Data Explorer, primeiro crie um cluster e crie um ou mais bancos de dados nesse cluster. Em seguida, você pode ingerir (carregar) dados em um banco de dado para poder executar consultas nele. Neste artigo, você cria um cluster e um banco de dados usando o Python.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="install-python-package"></a>Instalar pacote do Python

Para instalar o pacote do Python para o Data Explorer do Azure (Kusto), abra um prompt de comando que tenha o Python em seu caminho. Execute este comando:

```
pip install azure-mgmt-kusto
```

## <a name="create-the-azure-data-explorer-cluster"></a>Criar o cluster de Data Explorer do Azure

1. Crie seu cluster usando o seguinte comando:

    ```Python
    from azure.mgmt.kusto.kusto_management_client import KustoManagementClient
    from azure.mgmt.kusto.models import Cluster, AzureSku

    credentials = xxxxxxxxxxxxxxx
    
    subscription_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx'
    location = 'Central US'
    sku = 'D13_v2'
    capacity = 5
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    cluster = Cluster(location=location, sku=AzureSku(name=sku, capacity=capacity))
    
    kustoManagementClient = KustoManagementClient(credentials, subscription_id)
    
    cluster_operations = kustoManagementClient.clusters
    
    cluster_operations.create_or_update(resource_group_name, cluster_name, cluster)
    ```

   |**Definição** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | cluster_name | *mykustocluster* | O nome desejado do cluster.|
   | sku | *D13_v2* | A SKU que será usada para o cluster. |
   | resource_group_name | *testrg* | O nome do grupo de recursos em que o cluster será criado. |

    Há parâmetros opcionais adicionais que você pode usar, como a capacidade do cluster.
    
1. Definir [ *suas credenciais*](/azure/python/python-sdk-azure-authenticate)

1. Execute o seguinte comando para verificar se o cluster foi criado com êxito:

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

Se o resultado contiver `provisioningState` com o `Succeeded` valor, o cluster foi criado com êxito.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Criar o banco de dados no cluster de Data Explorer do Azure

1. Crie seu banco de dados usando o seguinte comando:

    ```Python
    from azure.mgmt.kusto.models import Database
    from datetime import timedelta
    
    softDeletePeriod = timedelta(days=3650)
    hotCachePeriod = timedelta(days=3650)
    databaseName="mykustodatabase"
    
    database_operations = kusto_management_client.databases 
    _database = Database(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

   |**Definição** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | cluster_name | *mykustocluster* | O nome do cluster em que o banco de dados será criado.|
   | database_name | *mykustodatabase* | O nome do seu banco de dados.|
   | resource_group_name | *testrg* | O nome do grupo de recursos em que o cluster será criado. |
   | soft_delete_period | *3650 dias, 0:00:00* | A quantidade de tempo que os dados serão mantidos disponíveis para consulta. |
   | hot_cache_period | *3650 dias, 0:00:00* | A quantidade de tempo que os dados serão mantidos no cache. |

1. Execute o seguinte comando para ver o banco de dados que você criou:

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

Agora você tem um cluster e um banco de dados.

## <a name="clean-up-resources"></a>Limpar recursos

* Se você planeja seguir nossos outros artigos, mantenha os recursos que você criou.
* Para limpar os recursos, exclua o cluster. Quando você exclui um cluster, ele também exclui todos os bancos de dados nele. Use o seguinte comando para excluir o cluster:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Passos seguintes

* [Ingerir dados usando a biblioteca do Azure Data Explorer Python](python-ingest-data.md)
