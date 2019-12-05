---
title: Como excluir um cluster HDInsight – Azure
description: Informações sobre as várias maneiras que você pode excluir um cluster do Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 2912777c001a36ec913e2cfd618091ada5aa107a
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807141"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Excluir um cluster HDInsight usando seu navegador, o PowerShell ou o CLI do Azure

A faturação do cluster do HDInsight tem início quando o cluster é criado e termina quando é eliminado. A cobrança é proporcional por minuto, portanto, você sempre deve excluir o cluster quando ele não estiver mais em uso. Neste documento, você aprende a excluir um cluster usando o [portal do Azure](https://portal.azure.com), Azure PowerShell o [módulo Az](https://docs.microsoft.com/powershell/azure/overview)e o [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> A exclusão de um cluster HDInsight não exclui as contas de armazenamento do Azure nem Data Lake Storage associadas ao cluster. Você pode reutilizar os dados armazenados nesses serviços no futuro.

## <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No menu à esquerda, navegue até **todos os serviços** > **Analytics** > **clusters HDInsight** e selecione o cluster.

3. No modo de exibição padrão, selecione o ícone **excluir** . Siga o prompt para excluir o cluster.

    ![Botão excluir cluster do HDInsight](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Substitua `CLUSTERNAME` pelo nome do seu cluster HDInsight no código abaixo. Em um prompt do PowerShell, digite o seguinte comando para excluir o cluster:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>CLI do Azure

Substitua `CLUSTERNAME` pelo nome do seu cluster HDInsight e `RESOURCEGROUP` pelo nome do seu grupo de recursos no código a seguir.  Em um prompt de comando, digite o seguinte para excluir o cluster:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
