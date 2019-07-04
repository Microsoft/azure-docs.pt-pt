---
title: Personalizar as configurações de cluster do Azure HDInsight com o arranque de
description: Saiba como personalizar a configuração de cluster do HDInsight através de programação com .net, PowerShell e do Resource Manager modelos.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 50db9a588cd953bbd0e912ec942194a8deeffe4c
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484048"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Personalizar clusters do HDInsight com o arranque de

Scripts de arranque de configuração permitem-lhe instalar e configurar componentes no Azure HDInsight, através de programação. 

Há três abordagens para especificar definições de ficheiro de configuração, como o cluster de HDInsight foi criado:

* Utilizar o Azure PowerShell
* Utilizar o .NET SDK
* Utilizar o modelo do Azure Resource Manager

Por exemplo, utilizar estes métodos de programação, pode configurar opções nesses arquivos:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* Server.Properties (configuração do Mediador kafka)

Para obter informações sobre como instalar componentes adicionais no cluster de HDInsight durante a hora de criação, consulte [HDInsight personalizar clusters com ação de Script (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="prerequisites"></a>Pré-requisitos

* Se utilizar o PowerShell, terá do [módulo Az](https://docs.microsoft.com/powershell/azure/overview).

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

O seguinte código de PowerShell personaliza uma [Apache Hive](https://hive.apache.org/) configuração:

> [!IMPORTANT]  
> O parâmetro `Spark2Defaults` poderá ter de ser utilizado com [Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue). Pode passar valores vazios para o parâmetro, conforme mostrado no exemplo de código abaixo.


```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

New-AzHDInsightCluster `
    -ResourceGroupName $existingResourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -Config $config
```

Um script do PowerShell de trabalho completo pode ser encontrado na [apêndice](#appendix-powershell-sample).

**Para verificar a alteração:**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu à esquerda, clique em **clusters do HDInsight**. Se não o vir, clique em **todos os serviços** primeiro.
3. Clique no cluster que acabou de criar utilizando o script do PowerShell.
4. Clique em **Dashboard** da parte superior do painel para abrir a IU do Ambari.
5. Clique em **Hive** no menu à esquerda.
6. Clique em **HiveServer2** partir **resumo**.
7. Clique nas **configurações** separador.
8. Clique em **Hive** no menu à esquerda.
9. Clique nas **avançadas** separador.
10. Desloque para baixo e, em seguida, expanda **Advanced hive site**.
11. Procure **hive.metastore.client.socket.timeout** na secção.

Alguns exemplos mais sobre como personalizar a outros ficheiros de configuração:

```xml
# hdfs-site.xml configuration
$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

# core-site.xml configuration
$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

# mapred-site.xml configuration
$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

# oozie-site.xml configuration
$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
```

## <a name="use-net-sdk"></a>Utilizar o .NET SDK
Ver [baseado em Linux criar clusters no HDInsight com o SDK .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

## <a name="use-resource-manager-template"></a>Utilize o Gestor de recursos de modelo
Pode utilizar o arranque no modelo do Resource Manager:

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![HDInsight Hadoop personaliza o modelo Azure Resource Manager arranque de configuração do cluster](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Consulte também
* [Criar clusters do Apache Hadoop no HDInsight][hdinsight-provision-cluster] fornece instruções sobre como criar um cluster do HDInsight com outras opções personalizadas.
* [Desenvolver scripts de ação de Script para o HDInsight][hdinsight-write-script]
* [Instalar e utilizar o Apache Spark em clusters do HDInsight][hdinsight-install-spark]
* [Instalar e usar o Apache Giraph nos clusters do HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions-linux.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fases durante a criação do cluster"

## <a name="appendix-powershell-sample"></a>Apêndice: Exemplo do PowerShell

Este script do PowerShell cria um cluster do HDInsight e personaliza uma definição de ramo de registo. Certifique-se de que introduza os valores para `$nameToken`, `$httpPassword`, e `$sshPassword`.

> [!WARNING]  
> Tipo de conta de armazenamento `BlobStorage` não pode ser utilizado para clusters do HDInsight.


```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>" 
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = '<ENTER A PASSWORD>' 

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = '<ENTER A PASSWORD>' 
#endregion

####################################
# Service names and varialbes
####################################
#region - service names
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

$resourceGroupName = $namePrefix + "rg"
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName

$location = "East US"
#endregion


####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -Location $location `
    -ClusterSizeInNodes 1 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -Config $config

####################################
# Verify the cluster
####################################
Get-AzHDInsightCluster `
    -ClusterName $hdinsightClusterName

#endregion
```
