---
title: Como eliminar um cluster HDInsight - Azure
description: Informações sobre as várias formas de eliminar um cluster Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 3903500e87dd162779d1732a4cfe405ba34e702c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87074737"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Elimine um cluster HDInsight usando o seu navegador, PowerShell ou o Azure CLI

A faturação do cluster do HDInsight tem início quando o cluster é criado e termina quando é eliminado. A faturação é pro-rated por minuto, por isso deve sempre apagar o seu cluster quando já não estiver em uso. Neste documento, aprende-se a eliminar um cluster utilizando o [portal Azure,](https://portal.azure.com)o [módulo Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/), e o [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> A eliminação de um cluster HDInsight não elimina as contas de Armazenamento Azure ou o Armazenamento do Lago de Dados associado ao cluster. No futuro, poderá reutilizar os dados armazenados nesses serviços.

## <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. A partir do menu esquerdo, navegue para **todos os serviços**  >  **Analytics**  >  **HDInsight clusters** e selecione o seu cluster.

3. A partir da vista predefinitiva, selecione o ícone **Eliminar.** Siga o pedido para eliminar o seu cluster.

    ![HDInsight eliminar botão de cluster](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

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
