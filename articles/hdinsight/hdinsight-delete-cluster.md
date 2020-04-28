---
title: Como eliminar um cluster HDInsight - Azure
description: Informações sobre as várias formas de eliminar um cluster Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 2912777c001a36ec913e2cfd618091ada5aa107a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74807141"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Elimine um cluster HDInsight utilizando o seu navegador, PowerShell ou o Azure CLI

A faturação do cluster do HDInsight tem início quando o cluster é criado e termina quando é eliminado. A faturação é a favor por minuto, por isso deve sempre apagar o seu cluster quando já não estiver a ser utilizada. Neste documento, aprende-se a eliminar um cluster utilizando o [portal Azure,](https://portal.azure.com) [o módulo Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/overview)e o [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> A eliminação de um cluster HDInsight não elimina as contas de Armazenamento Azure ou o Armazenamento do Lago de Dados associado ao cluster. No futuro, poderá reutilizar os dados armazenados nesses serviços.

## <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. A partir do menu esquerdo, navegue para **todos os serviços** > **Analytics** > **HDInsight clusters** e selecione o seu cluster.

3. A partir da vista predefinida, selecione o ícone **Delete.** Siga a solicitação para apagar o seu cluster.

    ![HDInsight eliminar botão de cluster](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Substitua-a `CLUSTERNAME` com o nome do seu cluster HDInsight no código abaixo. A partir de um pedido powerShell, introduza o seguinte comando para eliminar o cluster:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>CLI do Azure

Substitua-o `CLUSTERNAME` pelo nome do seu `RESOURCEGROUP` cluster HDInsight e com o nome do seu grupo de recursos no código abaixo.  A partir de um pedido de comando, introduza o seguinte para eliminar o cluster:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
