---
title: Personalizar as configurações de cluster do Azure HDInsight usando a inicialização
description: Saiba como personalizar a configuração do cluster HDInsight programaticamente usando modelos .net, PowerShell e Resource Manager.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 15d08b14e38f097e8e9c3e0db893efb1d6efe44d
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098675"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Personalizar os clusters HDInsight usando a inicialização

Os scripts de Bootstrap permitem que você instale e configure componentes no Azure HDInsight programaticamente.

Há três abordagens para definir as configurações do arquivo de configuração à medida que seu cluster HDInsight é criado:

* Utilizar o Azure PowerShell
* Utilizar o .NET SDK
* Utilizar o modelo do Azure Resource Manager

Por exemplo, usando esses métodos programáticos, você pode configurar opções nesses arquivos:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* Server. Properties (configuração do Kafka-Broker)

Para obter informações sobre como instalar componentes adicionais no cluster HDInsight durante o tempo de criação, consulte [Personalizar clusters hdinsight usando a ação de script (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="prerequisites"></a>Pré-requisitos

* Se estiver usando o PowerShell, você precisará do [módulo AZ](https://docs.microsoft.com/powershell/azure/overview).

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

O seguinte código do PowerShell personaliza uma configuração de [Apache Hive](https://hive.apache.org/) :

> [!IMPORTANT]  
> O parâmetro `Spark2Defaults` pode precisar ser usado com [Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue). Você pode passar valores vazios para o parâmetro, conforme mostrado no exemplo de código abaixo.

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

Um script completo do PowerShell de trabalho pode ser encontrado no [Apêndice](#appendix-powershell-sample).

**Para verificar a alteração:**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu à esquerda, clique em **clusters HDInsight**. Se você não o vir, clique em **todos os serviços** primeiro.
3. Clique no cluster que você acabou de criar usando o script do PowerShell.
4. Clique em **painel** na parte superior da folha para abrir a interface do usuário do amAmbari.
5. Clique em **Hive** no menu à esquerda.
6. Clique em **HiveServer2** do **Resumo**.
7. Clique na guia **configurações** .
8. Clique em **Hive** no menu à esquerda.
9. Clique na guia **avançado** .
10. Role para baixo e, em seguida, expanda **avançado Hive-site**.
11. Procure **Hive. metastore. cliente. Socket. Timeout** na seção.

Alguns outros exemplos de personalização de outros arquivos de configuração:

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
Consulte [Criar clusters baseados em Linux no HDInsight usando o SDK do .net](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

## <a name="use-resource-manager-template"></a>Utilizar o modelo do Resource Manager
Você pode usar a inicialização no modelo do Resource Manager:

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![O Hadoop personaliza o modelo de Azure Resource Manager de inicialização de cluster](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Consulte também

* [Criar Apache Hadoop clusters no HDInsight][hdinsight-provision-cluster] fornece instruções sobre como criar um cluster HDInsight usando outras opções personalizadas.
* [Desenvolver scripts de ação de script para o HDInsight][hdinsight-write-script]
* [Instalar e usar o Apache Spark em clusters HDInsight][hdinsight-install-spark]
* [Instale e use o Apache o giraph em clusters HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions-linux.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Estágios durante a criação do cluster"

## <a name="appendix-powershell-sample"></a>Anexo Exemplo do PowerShell

Esse script do PowerShell cria um cluster HDInsight e personaliza uma configuração de Hive. Certifique-se de inserir valores `$nameToken`para `$httpPassword`, e `$sshPassword`.

> [!WARNING]  
> O tipo `BlobStorage` de conta de armazenamento não pode ser usado para clusters HDInsight.

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
