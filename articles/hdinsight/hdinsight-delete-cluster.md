---
title: Como eliminar um cluster do HDInsight - Azure
description: Informações sobre as várias formas que pode eliminar um cluster do HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: eca7b4f8bd7e91bc8dcb9bcc49ed3b981010aaee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721008"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Eliminar um cluster do HDInsight com o seu browser, o PowerShell ou a CLI do Azure

A faturação do cluster do HDInsight tem início quando o cluster é criado e termina quando é eliminado. A faturação é rateada por minuto, pelo que deve sempre eliminar o cluster quando deixar de ser utilizado. Neste documento, vai aprender a eliminar um cluster com o [portal do Azure](https://portal.azure.com), [módulo Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/overview)e o [da CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> Eliminar um cluster do HDInsight não elimina as contas de armazenamento do Azure ou o armazenamento do Data Lake associada ao cluster. É possível reutilizar os dados armazenados nesses serviços no futuro.

## <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No menu à esquerda, navegue até **todos os serviços** > **análise** > **clusters do HDInsight** e selecione o cluster.

3. A vista predefinida, selecione o **eliminar** ícone. Siga o pedido para eliminar o cluster.
   
    ![ícone Eliminar](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell-az-module"></a>Módulo do Azure PowerShell Az

Substitua `CLUSTERNAME` com o nome do cluster do HDInsight no código abaixo. Num prompt do PowerShell, introduza o seguinte comando para eliminar o cluster:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>CLI do Azure

Substitua `CLUSTERNAME` com o nome do cluster do HDInsight, e `RESOURCEGROUP` com o nome do grupo de recursos no código abaixo.  Num prompt de comando, introduza o seguinte procedimento para eliminar o cluster:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
