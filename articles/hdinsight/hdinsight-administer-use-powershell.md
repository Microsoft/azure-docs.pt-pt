---
title: Gerir os clusters Apache Hadoop com PowerShell - Azure HDInsight
description: Aprenda a executar tarefas administrativas para os clusters Apache Hadoop em HDInsight utilizando o Azure PowerShell.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/13/2020
ms.openlocfilehash: 104975e6424ed96d43434a588997957033c31d93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560359"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Gerir os clusters Apache Hadoop em HDInsight utilizando o Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

A Azure PowerShell pode ser usada para controlar e automatizar a implementação e gestão das suas cargas de trabalho no Azure. Neste artigo, aprende-se a gerir os clusters [Apache Hadoop](https://hadoop.apache.org/) no Azure HDInsight utilizando o módulo Azure PowerShell Az. Para a lista dos cmdlets HDInsight PowerShell, consulte a [referência Az.HDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight).

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O Módulo PowerShell [Az](https://docs.microsoft.com/powershell/azure/overview) instalado.

## <a name="create-clusters"></a>Criar clusters

Consulte [criar clusters baseados em Linux no HDInsight usando o Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Aglomerados de listas

Utilize o seguinte comando para listar todos os clusters na subscrição atual:

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>Mostrar cluster

Utilize o seguinte comando para mostrar detalhes de um cluster específico na subscrição atual:

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Eliminar aglomerados

Utilize o seguinte comando para eliminar um cluster:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

Também pode eliminar um cluster removendo o grupo de recursos que contém o cluster. A eliminação de um grupo de recursos elimina todos os recursos do grupo, incluindo a conta de armazenamento por defeito.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Aglomerados de escala

A função de dimensionamento do cluster permite alterar o número de nós de trabalhador usados por um cluster que está a funcionar no Azure HDInsight sem ter de recriar o cluster. Para alterar o tamanho do cluster Hadoop utilizando o Azure PowerShell, execute o seguinte comando de uma máquina de cliente:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 Para obter mais informações sobre clusters de escala, consulte [clusters Scale HDInsight](./hdinsight-scaling-best-practices.md).

## <a name="update-http-user-credentials"></a>Atualizar credenciais de utilizador HTTP

[Set-AzHDInsightGatewayCredential](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightgatewaycredential) define as credenciais http gateway de um cluster Azure HDInsight.

```powershell
$clusterName = "CLUSTERNAME"
$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Set-AzHDInsightGatewayCredential -ClusterName $clusterName -HttpCredential $credential
```

## <a name="find-the-default-storage-account"></a>Encontre a conta de armazenamento por defeito

O seguinte script PowerShell demonstra como obter o nome da conta de armazenamento padrão e as informações relacionadas:

```powershell
#Connect-AzAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```

## <a name="find-the-resource-group"></a>Encontre o grupo de recursos

No modo Gestor de Recursos, cada cluster HDInsight pertence a um grupo de recursos Azure.  Para encontrar o grupo de recursos:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```

## <a name="submit-jobs"></a>Submeter postos de trabalho

**Para submeter mapReduce jobs**

Ver [Executar os exemplos MapReduce incluídos no HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Para submeter empregos apache hive**

Consulte [as consultas de Hiv Apache run usando PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**Para submeter os empregos apache Sqoop**

Consulte [O Apache Sqoop com HDInsight](hadoop/hdinsight-use-sqoop.md).

**Para submeter empregos apache Oozie**

Consulte [O Apache Oozie com Apache Hadoop para definir e executar um fluxo de trabalho em HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Enviar dados para armazenamento de Blob Azure

Consulte [Upload data to HDInsight (Carregar dados para o HDInsight)](hdinsight-upload-data.md).

## <a name="see-also"></a>Veja também

* [Cmdlets Az.HDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight/?view=azps-3.1.0#hdinsight)
* [Gerir os clusters Apache Hadoop em HDInsight utilizando o portal Azure](hdinsight-administer-use-portal-linux.md)
* [Administrar o HDInsight utilizando uma interface de linha de comando](hdinsight-administer-use-command-line.md)
* [Criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Submeta os empregos apache Hadoop programáticamente](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Get started with Azure HDInsight (Introdução ao Azure HDInsight)](hadoop/apache-hadoop-linux-tutorial-get-started.md)
