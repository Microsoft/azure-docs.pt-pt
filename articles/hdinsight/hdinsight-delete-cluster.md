---
title: Como eliminar um cluster HDInsight - Azure
description: Informações sobre as várias formas de eliminar um cluster Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive, devx-track-azurecli
ms.date: 11/29/2019
ms.openlocfilehash: 2160cfcd1f12f5effe7bb182eb665f85fec863af
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863399"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Elimine um cluster HDInsight usando o seu navegador, PowerShell ou o Azure CLI

A faturação do cluster do HDInsight tem início quando o cluster é criado e termina quando é eliminado. A faturação é pro-rated por minuto, por isso deve sempre apagar o seu cluster quando já não estiver em uso. Neste documento, aprende-se a eliminar um cluster utilizando o [portal Azure,](https://portal.azure.com)o [módulo Azure PowerShell Az](/powershell/azure/), e o [Azure CLI](/cli/azure/).

> [!IMPORTANT]  
> A eliminação de um cluster HDInsight não elimina as contas de Armazenamento Azure ou o Armazenamento do Lago de Dados associado ao cluster. No futuro, poderá reutilizar os dados armazenados nesses serviços.

## <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. A partir do menu esquerdo, navegue para **todos os serviços**  >  **Analytics**  >  **HDInsight clusters** e selecione o seu cluster.

3. A partir da vista predefinitiva, selecione o ícone **Eliminar.** Siga o pedido para eliminar o seu cluster.

    :::image type="content" source="./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png" alt-text="HDInsight eliminar botão de cluster":::

## <a name="azure-powershell"></a>Azure PowerShell

`CLUSTERNAME`Substitua-o pelo nome do seu cluster HDInsight no código abaixo. A partir de um pedido PowerShell, insira o seguinte comando para eliminar o cluster:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>CLI do Azure

`CLUSTERNAME`Substitua-o pelo nome do seu cluster HDInsight e `RESOURCEGROUP` pelo nome do seu grupo de recursos no código abaixo.  A partir de um pedido de comando, insira o seguinte para eliminar o cluster:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
