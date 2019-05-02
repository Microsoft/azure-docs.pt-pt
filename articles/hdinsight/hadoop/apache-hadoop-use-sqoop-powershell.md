---
title: Executar tarefas de Apache Sqoop com o PowerShell e Azure HDInsight
description: Saiba como utilizar o Azure PowerShell a partir de uma estação de trabalho para executar o Apache Sqoop import e export entre um cluster do Apache Hadoop e uma base de dados SQL do Azure.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: hrasheed
ms.openlocfilehash: 49375b00607f1f23a99855f8b80f5f5c3c4dac22
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64686661"
---
# <a name="run-apache-sqoop-jobs-by-using-azure-powershell-for-apache-hadoop-in-hdinsight"></a>Executar tarefas de Apache Sqoop com o Azure PowerShell para o Apache Hadoop no HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como utilizar o Azure PowerShell para executar tarefas do Apache Sqoop no Azure HDInsight para importar e exportar dados entre um cluster do HDInsight e uma base de dados SQL do Azure ou a base de dados do SQL Server. Neste exemplo exporta dados a partir `/tutorials/usesqoop/data/sample.log` da conta do storage predefinida e, em seguida, importa-o para uma tabela chamada `log4jlogs` numa base de dados do SQL Server. Este artigo é uma continuação de [utilizar o Apache Sqoop com o Hadoop no HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter os seguintes itens:

* Uma estação de trabalho com o Azure PowerShell [módulo AZ](https://docs.microsoft.com/powershell/azure/overview) instalado.

* Conclusão da [configurar o ambiente de teste](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) partir [utilização Apache Sqoop com o Hadoop no HDInsight](./hdinsight-use-sqoop.md).


## <a name="run-apache-sqoop-by-using-powershell"></a>Execute o Apache Sqoop com o PowerShell
O seguinte script do PowerShell previamente processa o arquivo de origem e, em seguida, exporta-o para uma base de dados SQL do Azure para a tabela `log4jlogs`. Substitua `CLUSTERNAME`, `CLUSTERPASSWORD`, e `SQLPASSWORD` com os valores utilizada a partir do pré-requisito.

```powershell 
<#------ BEGIN USER INPUT ------#>
$hdinsightClusterName = "CLUSTERNAME"
$httpUserName = "admin"  #default is admin, update as needed
$httpPassword = 'CLUSTERPASSWORD'
$sqlDatabasePassword = 'SQLPASSWORD'
<#------- END USER INPUT -------#>

# Other fixed variable that should be used as is
$sqlServerName = $hdinsightClusterName + "dbserver"
$sqlDatabaseName = $hdinsightClusterName + "db"
$tableName_log4j = "log4jlogs"
$exportDir_log4j = "/tutorials/usesqoop/data"
$sourceBlobName = "example/data/sample.log"
$destBlobName = "tutorials/usesqoop/data/sample.log"
$sqljdbcdriver = "/user/oozie/share/lib/sqoop/mssql-jdbc-7.0.0.jre8.jar"

$cluster = Get-AzHDInsightCluster -ClusterName $hdinsightClusterName
$defaultStorageAccountName = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
$defaultStorageContainer = $cluster.DefaultStorageContainer
$resourceGroup = $cluster.ResourceGroup

$sqlServer = Get-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $sqlServerName
$sqlServerLogin = $sqlServer.SqlAdministratorLogin
$sqlServerFQDN = $sqlServer.FullyQualifiedDomainName

#Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzContext}
catch{Connect-AzAccount}

#pre-process the source file
Write-Host "`nPreprocessing the source file ..." -ForegroundColor Green

# This procedure creates a new file with $destBlobName
# Define the connection string
$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroup `
                                -Name $defaultStorageAccountName)[0].Value

# Create block blob objects referencing the source and destination blob.
$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroup `
    -Name $defaultStorageAccountName

$storageContainer = ($storageAccount |Get-AzStorageContainer -Name $defaultStorageContainer).CloudBlobContainer

$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

# Define a MemoryStream and a StreamReader for reading from the source file
$stream = New-Object System.IO.MemoryStream
$stream = $sourceBlob.OpenRead()
$sReader = New-Object System.IO.StreamReader($stream)

# Define a MemoryStream and a StreamWriter for writing into the destination file
$memStream = New-Object System.IO.MemoryStream
$writeStream = New-Object System.IO.StreamWriter $memStream

# Pre-process the source blob
$exString = "java.lang.Exception:"
while(-Not $sReader.EndOfStream){
    $line = $sReader.ReadLine()
    $split = $line.Split(" ")

    # remove the "java.lang.Exception" from the first element of the array
    # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
    if ($split[0] -eq $exString){
        #create a new ArrayList to remove $split[0]
        $newArray = [System.Collections.ArrayList] $split
        $newArray.Remove($exString)

        # update $split and $line
        $split = $newArray
        $line = $newArray -join(" ")
    }

    # remove the lines that has less than 7 elements
    if ($split.count -ge 7){
        write-host $line
        $writeStream.WriteLine($line)
    }
}

# Write to the destination blob
$writeStream.Flush()
$memStream.Seek(0, "Begin")
$destBlob.UploadFromStream($memStream)

#export the log file from the cluster to the SQL database
Write-Host "Exporting the log file ..." -ForegroundColor Green

$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

# Connection string
$connectionString = "jdbc:sqlserver://$sqlServerFQDN;user=$sqlServerLogin@$sqlServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

# Submit a Sqoop job
$sqoopDef = New-AzHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1" `
    -Files $sqljdbcdriver

$sqoopJob = Start-AzHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef

Wait-AzHDInsightJob `
    -ResourceGroupName $resourceGroup `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroup `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultStorageContainer `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultStorageContainer `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput
```

## <a name="limitations"></a>Limitações
HDInsight baseado em Linux apresenta as seguintes limitações:

* Exportação em massa: O conector de Sqoop que é utilizado para exportar dados para o Microsoft SQL Server ou SQL Database do Azure não suporta atualmente inserções em massa.

* Processamento em lote: Ao utilizar o `-batch` mudar quando ele executa inserções, Sqoop realiza várias inserções em vez das operações de inserção de criação de batches. 

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu a utilizar o Sqoop. Para saber mais, consulte:

* [Utilizar o Apache Oozie com o HDInsight](../hdinsight-use-oozie-linux-mac.md): Utilize o Sqoop ação num fluxo de trabalho do Oozie.
* [Carregar dados para o HDInsight](../hdinsight-upload-data.md): Encontre outros métodos para carregar dados para o armazenamento do HDInsight ou de Blobs do Azure.

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
