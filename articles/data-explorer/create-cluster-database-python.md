---
title: Criar um cluster e base de dados Azure Data Explorer usando python
description: Aprenda a criar um cluster e base de dados do Azure Data Explorer utilizando python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 50e050a05fd364a4b1f880e3501b04274ffd360c
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444235"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Criar um cluster e base de dados Azure Data Explorer usando python

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [python](create-cluster-database-python.md)
> * [Modelo ARM](create-cluster-database-resource-manager.md)

Neste artigo, cria um cluster e base de dados azure Data Explorer utilizando python. O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerido que permite realizar análises em tempo real em volumes grandes de transmissão de dados a partir de aplicações, sites, dispositivos IoT e muito mais. Para utilizar o Azure Data Explorer, primeiro crie um cluster e crie uma ou mais bases de dados nesse cluster. Em seguida, ingerir, ou carregar, dados numa base de dados para que possa fazer perguntas contra ele.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Python 3.4+](https://www.python.org/downloads/).

* Um diretor de [aplicação e serviço azure AD que pode aceder a recursos.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) Obtenha valores para `Directory (tenant) ID`, `Application ID`e `Client Secret`.

## <a name="install-python-package"></a>Instalar pacote do Python

Para instalar o pacote Python para o Azure Data Explorer (Kusto), abra uma solicitação de comando que tenha python no seu caminho. Execute este comando:

```
pip install azure-common
pip install azure-mgmt-kusto
```
## <a name="authentication"></a>Autenticação
Para executar os exemplos neste artigo, precisamos de um Serviço De Aplicação E serviço Azure AD que possa aceder a recursos. Verifique [criar uma aplicação Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) para criar uma Aplicação AD Azure gratuita e adicionar atribuição de funções no âmbito de subscrição. Mostra também como obter o `Directory (tenant) ID`, `Application ID`e `Client Secret`.

## <a name="create-the-azure-data-explorer-cluster"></a>Criar o cluster Azure Data Explorer

1. Crie o seu cluster utilizando o seguinte comando:

    ```Python
    from azure.mgmt.kusto import KustoManagementClient
    from azure.mgmt.kusto.models import Cluster, AzureSku
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

    location = 'Central US'
    sku_name = 'Standard_D13_v2'
    capacity = 5
    tier = "Standard"
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    cluster = Cluster(location=location, sku=AzureSku(name=sku_name, capacity=capacity, tier=tier))
    
    kustoManagementClient = KustoManagementClient(credentials, subscription_id)
    
    cluster_operations = kustoManagementClient.clusters
    
    poller = cluster_operations.create_or_update(resource_group_name, cluster_name, cluster)
    ```

   |**Definição** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | cluster_name | *mykustocluster* | O nome desejado do seu aglomerado.|
   | sku_name | *Standard_D13_v2* | O SKU que será usado para o seu cluster. |
   | tier | *Standard* | O nível SKU. |
   | capacidade | *número* | O número de casos do aglomerado. |
   | resource_group_name | *testrg* | O nome do grupo de recursos onde o cluster será criado. |

    > [!NOTE]
    > **Criar um cluster** é uma operação de longa duração. O método **create_or_update** retorna uma instância de LROPoller, consulte a [classe LROPoller](/python/api/msrest/msrest.polling.lropoller?view=azure-python) para obter mais informações.

1. Execute o seguinte comando para verificar se o seu cluster foi criado com sucesso:

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

Se o resultado contiver `provisioningState` com o valor `Succeeded`, então o cluster foi criado com sucesso.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Criar a base de dados no cluster Azure Data Explorer

1. Crie a sua base de dados utilizando o seguinte comando:

    ```Python
    from azure.mgmt.kusto.models import Database
    from datetime import timedelta
    
    softDeletePeriod = timedelta(days=3650)
    hotCachePeriod = timedelta(days=3650)
    databaseName="mykustodatabase"
    
    database_operations = kusto_management_client.databases 
    _database = ReadWriteDatabase(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    #Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
    poller =database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

        [!NOTE]
        If you are using Python version 0.4.0 or below, use Database instead of ReadWriteDatabase.

   |**Definição** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | cluster_name | *mykustocluster* | O nome do seu cluster onde será criada a base de dados.|
   | database_name | *mykustodatabase* | O nome da sua base de dados.|
   | resource_group_name | *testrg* | O nome do grupo de recursos onde o cluster será criado. |
   | soft_delete_period | *3650 dias, 0:00:00* | O tempo que os dados serão mantidos disponíveis para consulta. |
   | hot_cache_period | *3650 dias, 0:00:00* | A quantidade de tempo que os dados serão mantidos em cache. |

1. Execute o seguinte comando para ver a base de dados que criou:

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

Agora tem um cluster e uma base de dados.

## <a name="clean-up-resources"></a>Limpar recursos

* Se planeia seguir os nossos outros artigos, guarde os recursos que criou.
* Para limpar os recursos, elimine o cluster. Ao eliminar um cluster, também elimina todas as bases de dados do mesmo. Utilize o seguinte comando para eliminar o seu cluster:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Passos seguintes

* [Ingerir dados utilizando a biblioteca Python do Explorador de Dados Azure](python-ingest-data.md)
