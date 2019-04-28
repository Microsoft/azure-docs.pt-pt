---
title: Gerir clusters do Apache Hadoop no HDInsight com o PowerShell - Azure
description: Saiba como executar tarefas administrativas para os clusters do Apache Hadoop no HDInsight com o Azure PowerShell.
ms.reviewer: tyfox
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: tylerfox
ms.openlocfilehash: a7e129f43b957b271c77f451ab198a9068bb0797
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096370"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Gerir clusters do Apache Hadoop no HDInsight com o Azure PowerShell
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

O Azure PowerShell pode ser utilizado para controlar e automatizar a implementação e gestão das cargas de trabalho no Azure. Neste artigo, irá aprender a gerir [Apache Hadoop](https://hadoop.apache.org/) clusters no Azure HDInsight com o módulo Azure PowerShell Az. Para obter a lista dos cmdlets do PowerShell do HDInsight, consulte a [Az.HDInsight referência](https://docs.microsoft.com/powershell/module/az.hdinsight).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* O PowerShell [módulo Az](https://docs.microsoft.com/powershell/azure/overview) instalado.

## <a name="create-clusters"></a>Criar clusters
Consulte [baseado em Linux criar clusters no HDInsight com o Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Listar clusters
Utilize o seguinte comando para listar todos os clusters na subscrição atual:

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>Mostrar o cluster
Utilize o seguinte comando para mostrar os detalhes de um cluster específico na subscrição atual:

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Eliminar clusters
Utilize o seguinte comando para eliminar um cluster:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

Também pode eliminar um cluster ao remover o grupo de recursos que contém o cluster. A eliminar um grupo de recursos elimina todos os recursos no grupo, incluindo a conta de armazenamento predefinida.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Dimensionar clusters
O funcionalidade de dimensionamento do cluster permite-lhe alterar o número de nós de trabalho utilizado por um cluster que está em execução no Azure HDInsight sem ter de voltar a criar o cluster.

O impacto de alterar o número de nós de dados para cada tipo de cluster suportada pelo HDInsight:

* Apache Hadoop

    Pode facilmente aumentar o número de nós de trabalho num cluster do Hadoop que está a ser executado sem afetar todas as tarefas em execução ou pendentes. Também podem ser submetidas a novas tarefas enquanto a operação estiver em curso. Falhas numa operação de dimensionamento são processadas corretamente para que o cluster está sempre deixado no estado funcional.

    Quando um cluster do Hadoop é reduzido, reduzindo o número de nós de dados, alguns dos serviços do cluster são reiniciados. A reiniciar serviços faz com que tudo em execução e tarefas pendentes a falhar após a conclusão da operação de dimensionamento. Pode, no entanto, volte a submeter as tarefas depois de concluída a operação.
* Apache HBase

    Pode facilmente adicionar ou remover nós ao cluster do HBase durante a execução. Servidores regionais são balanceadas automaticamente dentro de alguns minutos a concluir a operação de dimensionamento. No entanto, pode equilibrar também manualmente os servidores regionais iniciando sessão ao nó principal do cluster e, em seguida, execute os seguintes comandos a partir de uma janela de linha de comandos:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

* Apache Storm

    Pode facilmente adicionar ou remover nós de dados ao seu cluster do Storm, enquanto estiver em execução. Mas, após a conclusão bem-sucedida da operação de dimensionamento, terá de reequilibrar a topologia.

    Reequilíbrio pode ser feito de duas formas:

  * IU da web do Storm
  * Ferramenta de interface de linha de comandos (CLI)

    Consulte a [documentação do Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obter mais detalhes.

    A IU da web de Storm está disponível no cluster do HDInsight:

    ![Reequilíbrio de dimensionamento do HDInsight storm](./media/hdinsight-administer-use-powershell/hdinsight.portal.scale.cluster.png)

    Eis um exemplo como utilizar o comando da CLI para reequilibrar a topologia do Storm:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Para alterar o tamanho de cluster do Hadoop com o Azure PowerShell, execute o seguinte comando a partir de uma máquina de cliente:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```


## <a name="grantrevoke-access"></a>Conceder/revogar acesso
Clusters do HDInsight tem os seguintes serviços da web HTTP (todos estes serviços tem pontos de extremidade RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Por predefinição, estes serviços são concedidos para o acesso. Pode revogar/conceder o acesso. Para revogar:

```powershell
Revoke-AzHDInsightHttpServicesAccess -ClusterName <Cluster Name>
```

Para conceder:

```powershell
$clusterName = "<HDInsight Cluster Name>"

# Credential option 1
$hadoopUserName = "admin"
$hadoopUserPassword = "<Enter the Password>"
$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

# Credential option 2
#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Grant-AzHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential
```

> [!NOTE]  
> Ao conceder/revogar o acesso ao repor o nome de utilizador do cluster e a palavra-passe.

Também podem ser feitos conceder e revogar o acesso através do portal. Ver [Apache Hadoop gerir clusters no HDInsight com o portal do Azure](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>Atualize as credenciais de utilizador HTTP
É o mesmo procedimento como conceder/revogar HTTP acesso. Se o cluster tiver sido concedido o acesso HTTP, tem primeiro de o revogar.  E, em seguida, conceder o acesso com novas credenciais de utilizador HTTP.

## <a name="find-the-default-storage-account"></a>Localizar a conta de armazenamento predefinida
O script do PowerShell seguinte demonstra como obter o nome de conta do storage predefinida e as informações relacionadas:

> [!IMPORTANT]  
> Os valores para `DefaultStorageAccount`, e `DefaultStorageContainer` não são devolvidos da [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) quando [transferência segura](../storage/common/storage-require-secure-transfer.md) está ativada na conta de armazenamento.


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


## <a name="find-the-resource-group"></a>Encontrar o grupo de recursos
No modo Resource Manager, cada cluster de HDInsight pertence a um grupo de recursos do Azure.  Para localizar o grupo de recursos:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```


## <a name="submit-jobs"></a>Submeter tarefas
**Submeter tarefas de MapReduce**

Ver [executar os exemplos de MapReduce incluídos no HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Para submeter tarefas do Apache Hive**

Ver [executar Apache consultas do Hive com o PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**Para submeter tarefas do Apache Pig**

Ver [tarefas de executar o Apache Pig com o PowerShell](hadoop/apache-hadoop-use-pig-powershell.md).

**Submeter tarefas do Apache Sqoop**

Ver [utilizar o Apache Sqoop com o HDInsight](hadoop/hdinsight-use-sqoop.md).

**Submeter tarefas do Apache Oozie**

Ver [utilização Apache Oozie com o Apache Hadoop para definir e executar um fluxo de trabalho no HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Carregar dados para o armazenamento de Blobs do Azure

Ver [carregar dados para o HDInsight](hdinsight-upload-data.md).

## <a name="see-also"></a>Consultar Também

* [Documentação de referência de cmdlets do HDInsight](https://msdn.microsoft.com/library/azure/dn479228.aspx)
* [Gerir clusters do Apache Hadoop no HDInsight com o portal do Azure](hdinsight-administer-use-portal-linux.md)
* [Administrar o HDInsight, através de uma interface de linha de comandos](hdinsight-administer-use-command-line.md)
* [Criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Submeter tarefas do Apache Hadoop por meio de programação](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
