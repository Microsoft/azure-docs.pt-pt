---
title: Gerir clusters de HDInsight do Azure com a CLI do Azure
description: Saiba como utilizar a CLI do Azure para gerir os clusters do HDInsight do Azure. Os tipos de cluster incluem Apache Hadoop, Spark, HBase, Storm, Kafka, Interactive Query e serviços de ML.
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tyfox
ms.openlocfilehash: 7c12831c43762ddc776e8d5701f002be97992cbc
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65859968"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Gerir clusters de HDInsight do Azure com a CLI do Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Aprenda a usar [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) para gerir os clusters do HDInsight do Azure. A interface de linha de comandos (CLI) do Azure é a experiência de linha de comandos entre plataformas da Microsoft para gerir os recursos do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* CLI do Azure. Se ainda não instalou a CLI do Azure, consulte [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) para obter os passos.

* Um cluster do Apache Hadoop no HDInsight. Ver [introdução ao HDInsight no Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Ligar ao Azure

Inicie sessão na sua subscrição do Azure. Se planear utilizar o Azure Cloud Shell, em seguida, selecione simplesmente **experimente** no canto superior direito do bloco de código. Caso contrário, introduza o comando abaixo:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Listar clusters

Uso [lista de hdinsight az](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) para clusters de lista. Editar os comandos abaixo, substituindo `RESOURCE_GROUP_NAME` com o nome do seu grupo de recursos, em seguida, introduza os comandos:

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>Mostrar o cluster

Uso [show de hdinsight az](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) para mostrar as informações para um cluster especificado. Editar o comando abaixo, substituindo `RESOURCE_GROUP_NAME`, e `CLUSTER_NAME` com as informações relevantes, em seguida, introduza o comando:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Eliminar clusters

Uso [eliminar az hdinsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) para eliminar um cluster especificado. Editar o comando abaixo, substituindo `RESOURCE_GROUP_NAME`, e `CLUSTER_NAME` com as informações relevantes, em seguida, introduza o comando:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Também pode eliminar um cluster ao eliminar o grupo de recursos que contém o cluster. Tenha em atenção que esta ação irá eliminar todos os recursos no grupo, incluindo a conta de armazenamento predefinida.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Dimensionar clusters

Uso [redimensionar az hdinsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) para redimensionar o cluster de HDInsight especificado para o tamanho especificado. Editar o comando abaixo, substituindo `RESOURCE_GROUP_NAME`, e `CLUSTER_NAME` com as informações relevantes. Substitua `TARGET_INSTANCE_COUNT` com o número pretendido de nós de trabalho para o seu cluster. Para obter mais informações sobre o dimensionamento de clusters, veja [clusters do HDInsight de escala](./hdinsight-scaling-best-practices.md). Introduza o comando:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --target-instance-count TARGET_INSTANCE_COUNT
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu como efetuar diferentes tarefas administrativas de cluster HDInsight. Para obter mais informações, consulte os artigos seguintes:

* [Gerir clusters do Apache Hadoop no HDInsight com o portal do Azure](hdinsight-administer-use-portal-linux.md)
* [Administrar o HDInsight com o Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Introdução à CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
