---
title: Gerenciar clusters de Apache Hadoop com o PowerShell-Azure HDInsight
description: Saiba como executar tarefas administrativas para os clusters de Apache Hadoop no HDInsight usando Azure PowerShell.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: e37571b0078b4966aab9f505ddf88c2edb353197
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435632"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Gerenciar clusters de Apache Hadoop no HDInsight usando Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell pode ser usado para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Neste artigo, você aprenderá a gerenciar clusters de [Apache Hadoop](https://hadoop.apache.org/) no Azure HDInsight usando o módulo Azure PowerShell AZ. Para obter a lista dos cmdlets do PowerShell do HDInsight, consulte a [referência AZ. HDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O [módulo AZ](https://docs.microsoft.com/powershell/azure/overview) do PowerShell instalado.

## <a name="create-clusters"></a>Criar clusters

Confira [Criar clusters baseados em Linux no HDInsight usando Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Listar clusters

Use o seguinte comando para listar todos os clusters na assinatura atual:

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>Mostrar cluster

Use o comando a seguir para mostrar os detalhes de um cluster específico na assinatura atual:

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Excluir clusters

Use o seguinte comando para excluir um cluster:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

Você também pode excluir um cluster removendo o grupo de recursos que contém o cluster. A exclusão de um grupo de recursos exclui todos os recursos do grupo, incluindo a conta de armazenamento padrão.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Dimensionar clusters

O recurso de dimensionamento de cluster permite que você altere o número de nós de trabalho usados por um cluster que está sendo executado no Azure HDInsight sem precisar recriar o cluster. Para alterar o tamanho do cluster do Hadoop usando Azure PowerShell, execute o seguinte comando em um computador cliente:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 Para obter mais informações sobre o dimensionamento de clusters, consulte [dimensionar clusters HDInsight](./hdinsight-scaling-best-practices.md).

## <a name="grantrevoke-access"></a>Conceder/revogar acesso

Os clusters HDInsight têm os seguintes serviços Web HTTP (todos esses serviços têm pontos de extremidade RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Por padrão, esses serviços são concedidos para acesso. Você pode revogar/conceder o acesso. Para revogar:

```powershell
Revoke-AzHDInsightHttpServicesAccess -ClusterName <Cluster Name>
```

Para conceder:

```powershell
$clusterName = "<HDInsight Cluster Name>"

# Credential option 1
$hadoopUserName = "admin"
$hadoopUserPassword = '<Enter the Password>'
$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

# Credential option 2
#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Grant-AzHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential
```

> [!NOTE]  
> Ao conceder/revogar o acesso, você redefine o nome de usuário e a senha do cluster.

A concessão e a revogação do acesso também podem ser feitas por meio do Portal. Consulte [gerenciar clusters de Apache Hadoop no HDInsight usando o portal do Azure](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>Atualizar credenciais de usuário HTTP

É o mesmo procedimento que conceder/revogar acesso HTTP. Se o cluster tiver recebido o acesso HTTP, você deverá primeiro revogá-lo.  E, em seguida, conceder o acesso com novas credenciais de usuário HTTP.

## <a name="find-the-default-storage-account"></a>Localizar a conta de armazenamento padrão

O script do PowerShell a seguir demonstra como obter o nome da conta de armazenamento padrão e as informações relacionadas:

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

## <a name="find-the-resource-group"></a>Localizar o grupo de recursos

No modo do Gerenciador de recursos, cada cluster HDInsight pertence a um grupo de recursos do Azure.  Para localizar o grupo de recursos:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```

## <a name="submit-jobs"></a>Enviar trabalhos

**Para enviar trabalhos MapReduce**

Consulte [executar os exemplos de MapReduce incluídos no HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Para enviar trabalhos de Apache Hive**

Consulte [executar consultas de Apache Hive usando o PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**Para enviar trabalhos do Apache Sqoop**

Consulte [usar o Apache Sqoop com o HDInsight](hadoop/hdinsight-use-sqoop.md).

**Para enviar trabalhos do Apache Oozie**

Consulte [usar o Apache Oozie com Apache Hadoop para definir e executar um fluxo de trabalho no HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Carregar dados no armazenamento de BLOBs do Azure

Consulte [carregar dados no HDInsight](hdinsight-upload-data.md).

## <a name="see-also"></a>Veja também

* [Cmdlets AZ. HDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight/?view=azps-3.1.0#hdinsight)
* [Gerenciar clusters de Apache Hadoop no HDInsight usando o portal do Azure](hdinsight-administer-use-portal-linux.md)
* [Administrar o HDInsight usando uma interface de linha de comando](hdinsight-administer-use-command-line.md)
* [Criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Enviar trabalhos de Apache Hadoop programaticamente](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
