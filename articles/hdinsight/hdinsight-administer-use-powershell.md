---
title: Gerenciar clusters de Apache Hadoop no HDInsight com o PowerShell – Azure
description: Saiba como executar tarefas administrativas para os clusters de Apache Hadoop no HDInsight usando Azure PowerShell.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: tyfox
ms.openlocfilehash: 751f064df271aeb0899a00aea8b1ff09e8b8bdf4
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077049"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Gerenciar clusters de Apache Hadoop no HDInsight usando Azure PowerShell
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell pode ser usado para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Neste artigo, você aprenderá a gerenciar clusters de [Apache Hadoop](https://hadoop.apache.org/) no Azure HDInsight usando o módulo Azure PowerShell AZ. Para obter a lista dos cmdlets do PowerShell do HDInsight, consulte a [referência AZ. HDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* O [módulo AZ](https://docs.microsoft.com/powershell/azure/overview) do PowerShell instalado.

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
O recurso de dimensionamento de cluster permite que você altere o número de nós de trabalho usados por um cluster que está sendo executado no Azure HDInsight sem precisar recriar o cluster.

O impacto da alteração do número de nós de dados para cada tipo de cluster com suporte no HDInsight:

* Apache Hadoop

    Você pode aumentar diretamente o número de nós de trabalho em um cluster Hadoop em execução sem afetar nenhum trabalho pendente ou em execução. Novos trabalhos também podem ser enviados enquanto a operação está em andamento. As falhas em uma operação de dimensionamento são normalmente manipuladas para que o cluster sempre fique em um estado funcional.

    Quando um cluster Hadoop é reduzido reduzindo o número de nós de dados, alguns dos serviços no cluster são reiniciados. A reinicialização de serviços faz com que todos os trabalhos em execução e pendentes falhem na conclusão da operação de dimensionamento. Você pode, no entanto, reenviar os trabalhos quando a operação for concluída.
* Apache HBase

    Você pode adicionar ou remover nós diretamente em seu cluster HBase enquanto ele está em execução. Os servidores regionais são balanceados automaticamente em alguns minutos após a conclusão da operação de dimensionamento. No entanto, você também pode balancear manualmente os servidores regionais fazendo logon no cabeçalho do cluster e, em seguida, executar os seguintes comandos em uma janela de prompt de comando:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

* Apache Storm

    Você pode adicionar ou remover nós de dados diretamente em seu cluster Storm enquanto ele estiver em execução. Mas, após a conclusão bem-sucedida da operação de dimensionamento, será necessário reequilibrar a topologia.

    O rebalanceamento pode ser realizado de duas maneiras:

  * Interface do usuário da Web do Storm
  * Ferramenta de interface de linha de comando (CLI)

    Consulte a [documentação do Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obter mais detalhes.

    A interface do usuário da Web do Storm está disponível no cluster HDInsight:

    ![Rebalanceamento de escala do HDInsight Storm](./media/hdinsight-administer-use-powershell/portal-scale-cluster.png)

    Aqui está um exemplo de como usar o comando da CLI para reequilibrar a topologia do Storm:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Para alterar o tamanho do cluster do Hadoop usando Azure PowerShell, execute o seguinte comando em um computador cliente:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```


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
$hadoopUserPassword = "<Enter the Password>"
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

## <a name="see-also"></a>Consultar Também

* [Documentação de referência do cmdlet do HDInsight](https://msdn.microsoft.com/library/azure/dn479228.aspx)
* [Gerenciar clusters de Apache Hadoop no HDInsight usando o portal do Azure](hdinsight-administer-use-portal-linux.md)
* [Administrar o HDInsight usando uma interface de linha de comando](hdinsight-administer-use-command-line.md)
* [Criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Enviar trabalhos de Apache Hadoop programaticamente](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
