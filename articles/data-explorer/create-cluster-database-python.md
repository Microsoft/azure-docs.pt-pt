---
title: 'Início rápido: Criar um cluster do Explorador de dados do Azure e a base de dados com o Python'
description: Saiba como criar um cluster do Explorador de dados do Azure e a base de dados com o Python.
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/25/2019
ms.openlocfilehash: 408b34db16f0d6d22340f0483b90ce5d72ffa613
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045206"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Criar um cluster do Explorador de dados do Azure e a base de dados com o Python

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerido que permite realizar análises em tempo real em volumes grandes de transmissão de dados a partir de aplicações, sites, dispositivos IoT e muito mais. Para utilizar o Explorador de dados do Azure, primeiro cria um cluster e criar um ou mais bases de dados desse cluster. Em seguida, ingerir (carregar) dados numa base de dados para que pode executar consultas em relação a ele. Neste início rápido, vai criar um cluster e uma base de dados com o Python.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="install-python-package"></a>Instalar pacote do Python

Para instalar o pacote do Python para o Explorador de dados do Azure (Kusto), abra um prompt de comando que tem o Python no respetivo caminho. Execute este comando:

```
pip install azure-mgmt-kusto
```

## <a name="create-the-azure-data-explorer-cluster"></a>Criar o cluster do Explorador de dados do Azure

1. Crie o cluster utilizando o seguinte comando:

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
   | cluster_name | *mykustocluster* | O nome do cluster pretendido.|
   | sku | *D13_v2* | O SKU que será utilizado para o seu cluster. |
   | resource_group_name | *testrg* | O nome do grupo de recursos onde será criado o cluster. |

    Existem parâmetros opcionais adicionais que pode utilizar, como a capacidade do cluster.
    
1. Definir [ *as suas credenciais*](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python)

1. Execute o seguinte comando para verificar se o cluster foi criado com êxito:

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

Se o resultado contém `provisioningState` com o `Succeeded` valor, em seguida, o cluster foi criado com êxito.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Criar a base de dados do cluster do Explorador de dados do Azure

1. Crie a base de dados, utilize o seguinte comando:

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
   | cluster_name | *mykustocluster* | O nome do cluster onde a base de dados será criado.|
   | database_name | *mykustodatabase* | O nome da base de dados.|
   | resource_group_name | *testrg* | O nome do grupo de recursos onde será criado o cluster. |
   | soft_delete_period | *dias 3650, 0: 00:00* | A quantidade de tempo que os dados serão mantidos disponíveis para consulta. |
   | hot_cache_period | *dias 3650, 0: 00:00* | A quantidade de tempo que os dados serão mantidos na cache. |

1. Execute o seguinte comando para ver a base de dados que criou:

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

Agora tem um cluster e uma base de dados.

## <a name="clean-up-resources"></a>Limpar recursos

* Se tenciona seguir os nossos inícios rápidos e tutoriais, mantenha os recursos que criou.
* Para limpar os recursos, elimine o cluster. Quando eliminar um cluster, também elimina todos os bancos de dados no mesmo. Utilize o seguinte comando para eliminar o cluster:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Início rápido: Ingestão de dados usando a biblioteca de Python de Explorador de dados do Azure](python-ingest-data.md)
