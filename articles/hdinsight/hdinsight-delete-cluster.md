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
ms.openlocfilehash: d91161127b3fd3b63d7a5cc76df2fbeb33b62f34
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92479838"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Elimine um cluster HDInsight usando o seu navegador, PowerShell ou o Azure CLI

A faturação do cluster do HDInsight tem início quando o cluster é criado e termina quando é eliminado. A faturação é pro-rated por minuto, por isso deve sempre apagar o seu cluster quando já não estiver em uso. Neste documento, aprende-se a eliminar um cluster utilizando o [portal Azure,](https://portal.azure.com)o [módulo Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/), e o [Azure CLI](/cli/azure/).

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
