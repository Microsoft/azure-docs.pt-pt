---
title: Gerir clusters Azure HDInsight utilizando o Azure CLI
description: Aprenda a utilizar o Azure CLI para gerir os clusters Azure HDInsight. Os tipos de cluster incluem Apache Hadoop, Spark, HBase, Storm, Kafka, Interactive Query e ML Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 2c6495454e5ba2449d4b3c74a096681f74610813
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79272776"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Gerir clusters Azure HDInsight utilizando o Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Aprenda a utilizar o [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) para gerir os clusters Azure HDInsight. A interface de linha de comandos (CLI) do Azure é a experiência de linha de comandos entre plataformas da Microsoft para gerir os recursos do Azure.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Azure CLI. Se ainda não instalou o Azure CLI, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) para os passos.

* Um aglomerado Apache Hadoop no HDInsight. Ver [Começar com HDInsight no Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Ligar ao Azure

Inscreva-se na sua assinatura Azure. Se planeia utilizar a Azure Cloud Shell, então selecione **Experimente no** canto superior direito do bloco de código. Caso contrário, insira o comando abaixo:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Aglomerados de listas

Utilize a [lista az hdinsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) para listar clusters. Edite os comandos `RESOURCE_GROUP_NAME` abaixo substituindo pelo nome do seu grupo de recursos e, em seguida, introduza os comandos:

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

## <a name="show-cluster"></a>Mostrar cluster

Use [az hdinsight show](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) para mostrar informações para um cluster especificado. Editar o comando abaixo `RESOURCE_GROUP_NAME`substituindo, e `CLUSTER_NAME` com as informações relevantes, então insira o comando:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Eliminar aglomerados

Utilize [aaz hdinsight eliminar](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) para eliminar um cluster especificado. Editar o comando abaixo `RESOURCE_GROUP_NAME`substituindo, e `CLUSTER_NAME` com as informações relevantes, então insira o comando:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Também pode eliminar um cluster eliminando o grupo de recursos que contém o cluster. Nota: isto irá eliminar todos os recursos do grupo, incluindo a conta de armazenamento por defeito.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Aglomerados de escala

Utilize o [redimensionado az hdinsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) para redimensionar o cluster HDInsight especificado para o tamanho especificado. Editar o comando abaixo `RESOURCE_GROUP_NAME`substituindo, e `CLUSTER_NAME` com as informações relevantes. Substitua-o `WORKERNODE_COUNT` pelo número desejado de nós de trabalhador para o seu cluster. Para obter mais informações sobre clusters de escala, consulte [clusters Scale HDInsight](./hdinsight-scaling-best-practices.md). Insira o comando:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a executar diferentes tarefas administrativas de cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* [Gerir os clusters Apache Hadoop em HDInsight utilizando o portal Azure](hdinsight-administer-use-portal-linux.md)
* [Administrar o HDInsight utilizando o Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Get started with Azure HDInsight (Introdução ao Azure HDInsight)](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Introdução à CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
