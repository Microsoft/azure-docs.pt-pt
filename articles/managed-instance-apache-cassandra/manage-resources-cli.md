---
title: Gerir a Azure Managed Instance para os recursos da Apache Cassandra utilizando o Azure CLI
description: Conheça os comandos comuns para automatizar a gestão da sua Azure Managed Instance para Apache Cassandra usando Azure CLI.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/15/2021
ms.author: thvankra
ms.openlocfilehash: ea28bf21424f0624b4f1bb5856a17672c1c7b106
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875454"
---
# <a name="manage-azure-managed-instance-for-apache-cassandra-resources-using-azure-cli-preview"></a>Gerir a exemplo gerida do Azure para os recursos de Apache Cassandra utilizando o Azure CLI (Preview)

Este artigo descreve comandos comuns para automatizar a gestão do seu Azure Managed Instance para aglomerados Apache Cassandra usando Azure CLI.

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Este artigo requer a versão Azure CLI 2.17.1 ou superior. Se estiver a utilizar o Azure Cloud Shell, a versão mais recente já está instalada.
>
> Gerir a Azure Managed Instance para os recursos da Apache Cassandra não pode ser renomeado, uma vez que isto viola a forma como o Azure Resource Manager funciona com URIs de recursos.

## <a name="azure-managed-instance-for-apache-cassandra-clusters"></a>Azure Managed Instance para aglomerados Apache Cassandra

As seguintes secções demonstram como gerir a instância gerida do Azure para os agrupamentos Apache Cassandra, incluindo:

* [Criar um cluster de instâncias gerido](#create-cluster)
* [Eliminar um cluster de instâncias gerido](#delete-cluster)
* [Obtenha os detalhes do cluster](#get-cluster-details)
* [Obtenha o estado do nó de cluster](#get-cluster-status)
* [Listar clusters por grupo de recursos](#list-clusters-resource-group)
* [List clusters por ID de subscrição](#list-clusters-subscription)

### <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Criar um cluster de instâncias gerido

Crie um Azure Managed Instance para o aglomerado Apache Cassandra utilizando o [cluster az gerido-cassandra criar](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_create) comando:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
location='West US'
delegatedManagementSubnetId='/subscriptions/<subscription id>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/management'
initialCassandraAdminPassword='myPassword'

# You can override the cluster name if the original name is not legal for an Azure resource.
# overrideClusterName='ClusterNameIllegalForAzureResource'
# the default Cassandra version is v3.11

az managed-cassandra cluster create \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName \
    --location $location \
    --delegated-management-subnet-id $delegatedManagementSubnetId \
    --initial-cassandra-admin-password $initialCassandraAdminPassword \
```

### <a name="delete-a-managed-instance-cluster"></a><a id="delete-cluster"></a>Eliminar um cluster de instâncias gerido

Eliminar um cluster utilizando o [comando de eliminação do agrupamento az gerido-cassandra:](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_delete)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster delete \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-details"></a><a id="get-cluster-details"></a>Obtenha os detalhes do cluster

Obtenha detalhes do cluster utilizando o comando [de show de cluster az gerido-cassandra:](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_show)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster show \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-node-status"></a><a id="get-cluster-status"></a>Obtenha o estado do nó de cluster

Obtenha detalhes do cluster utilizando o comando de nó de nó de aglomerado gerido [az:](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_node_status)

```azurecli-interactive
clusterName='cassandra-hybrid-cluster'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster node-status \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="list-the-clusters-by-resource-group"></a><a id="list-clusters-resource-group"></a>Listar os clusters por grupo de recursos

Listar os clusters por grupo de recursos utilizando o comando da [lista de agrupamentos az gerido-cassandra:](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_list)

```azurecli-interactive
subscriptionId='MySubscriptionId'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster list\
    --resource-group $resourceGroupName
```

### <a name="list-clusters-by-subscription-id"></a><a id="list-clusters-subscription"></a>List clusters por ID de subscrição

Listar os clusters por ID por assinatura utilizando o comando da [lista de agrupamentos az gerido-cassandra:](/cli/azure/managed-cassandra?view=azure-cli-latest&preserve-view=true)

```azurecli-interactive
# set your subscription id
az account set -s <subscription id>

az managed-cassandra cluster list
```

## <a name="the-managed-instance-datacenters"></a><a id="managed-instance-datacenter"></a>Os datacenters de instância geridos

As seguintes secções demonstram como gerir a exemplo gerida do Azure para os centros de dados Apache Cassandra, incluindo:

* [Criar um datacenter](#create-datacenter)
* [Eliminar um datacenter](#delete-datacenter)
* [Obtenha detalhes do datacenter](#get-datacenter-details)
* [Atualizar ou escalar um datacenter](#update-datacenter)
* [Obtenha centros de dados num cluster](#get-datacenters-cluster)

### <a name="create-a-datacenter"></a><a id="create-datacenter"></a>Criar um datacenter

Crie um datacenter utilizando o [datacenter gerido az-cassandra criar](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_create) comando:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus2'
delegatedSubnetId='/subscriptions/<Subscription_ID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter create \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --data-center-location $dataCenterLocation \
    --delegated-subnet-id $delegatedSubnetId \
    --node-count 3 
```

### <a name="delete-a-datacenter"></a><a id="delete-datacenter"></a>Eliminar um datacenter

Eliminar um datacenter utilizando o comando [de eliminação de datacenter gerido az-cassandra:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_delete)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter delete \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="get-datacenter-details"></a><a id="get-datacenter-details"></a>Obtenha detalhes do datacenter

Obtenha detalhes do datacenter utilizando o comando [do datacenter gerido az:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_show)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter show \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="update-or-scale-a-datacenter"></a><a id="update-datacenter"></a>Atualizar ou escalar um datacenter

Atualizar ou escalar um datacenter (para escalar o valor do nodeCount) utilizando o comando de atualização do [datacenter gerido az:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_update)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus'
delegatedSubnetId= '/subscriptions/<Subscription_ID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter update \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --node-count 13 
```

### <a name="get-the-datacenters-in-a-cluster"></a><a id="get-datacenters-cluster"></a>Obtenha os datacenters em um cluster

Obtenha centros de dados num cluster utilizando o comando da [lista de datacenter gerido az:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_list)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra datacenter list \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName
```

## <a name="next-steps"></a>Passos seguintes

* [Criar um cluster de instâncias gerido a partir do portal Azure](create-cluster-portal.md)
* [Implementar um Cluster de Faíscas Apache Gerido com Tijolos de Dados Azure](deploy-cluster-databricks.md)
