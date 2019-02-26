---
title: Executar tarefas de Apache Sqoop com o Azure HDInsight (Apache Hadoop)
description: Saiba como utilizar o Azure PowerShell a partir de uma estação de trabalho para executar o Sqoop import e export entre um cluster de Hadoop e uma base de dados SQL do Azure.
ms.reviewer: jasonh
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: 4e56d22dde5fca50d17c055be93db5b91deeb287
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819178"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Utilizar o Apache Sqoop com o Hadoop no HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como utilizar o Apache Sqoop para importar e exportar entre o cluster do HDInsight e a base de dados SQL do Azure ou a base de dados do SQL Server no HDInsight.

Embora o Apache Hadoop é uma opção natural para processar os dados não estruturados e semiestruturados, tais como registos e ficheiros, também pode haver a necessidade de processar dados estruturados que são armazenados em bases de dados relacionais.

[Apache Sqoop] [ sqoop-user-guide-1.4.4] é uma ferramenta projetada para transferir dados entre clusters do Hadoop e bases de dados relacionais. Pode usá-lo para importar dados de um sistema de gerenciamento de banco de dados relacional (RDBMS), como SQL Server, MySQL ou Oracle para o sistema de ficheiros distribuído Hadoop (HDFS), transformar os dados no Hadoop com o MapReduce ou o Apache Hive e, em seguida, exportar os dados de volta para um RDBMS . Neste tutorial, está a utilizar uma base de dados do SQL Server da base de dados relacional.

Para versões de Sqoop que são suportadas nos clusters do HDInsight, consulte [quais são as novidades nas versões do cluster fornecidas pelo HDInsight?][hdinsight-versions]

## <a name="understand-the-scenario"></a>Compreender o cenário

Cluster de HDInsight vem com alguns dados de exemplo. Utilize os seguintes dois exemplos:

* Um ficheiro de registo Apache Log4j, que está localizado em */example/data/sample.log*. Os seguintes registos são extraídos a partir do ficheiro:
  
        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...
* Uma tabela do Hive com o nome *hivesampletable*, que faz referência ao arquivo de dados localizado na */hive/warehouse/hivesampletable*. A tabela contém alguns dados de dispositivos móveis. 
  
  | Campo | Tipo de dados |
  | --- | --- |
  | ID de cliente |string |
  | querytime |string |
  | mercado |string |
  | deviceplatform |string |
  | devicemake |string |
  | devicemodel |string |
  | state |string |
  | País |string |
  | querydwelltime |double |
  | SessionID |bigint |
  | sessionpagevieworder |bigint |

Neste tutorial, vai utilizar esses dois conjuntos de dados para testar o Sqoop import e exportar.

## <a name="create-cluster-and-sql-database"></a>Criar o cluster e a base de dados SQL
Esta secção mostra-lhe como criar um cluster, uma base de dados SQL e os esquemas de banco de dados SQL para executar o tutorial, utilizando o portal do Azure e um modelo Azure Resource Manager. O modelo pode ser encontrado na [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). O modelo do Resource Manager chama um pacote de bacpac para implementar os esquemas de tabela para a base de dados SQL.  O pacote de bacpac está localizado no contentor de blob público, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Se pretender utilizar um contentor privado para os ficheiros bacpac, utilize os seguintes valores no modelo:
   
```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

Se preferir utilizar o Azure PowerShell para criar o cluster e a base de dados SQL, veja [apêndice A](#appendix-a---a-powershell-sample).

> [!NOTE]  
> Importar com um modelo ou do portal do Azure só suporta a importação de um ficheiro BACPAC do armazenamento de Blobs do Azure.

**Para configurar o ambiente com um modelo de gestão de recursos**
1. Clique na imagem seguinte para abrir um modelo do Resource Manager no portal do Azure.         
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
2. Introduza as seguintes propriedades:

    - **Subscrição**: Introduza a sua subscrição do Azure.
    - **Grupo de recursos**: Criar um novo grupo de recursos do Azure ou selecione um grupo de recursos existente.  É um grupo de recursos para fins de gestão.  É um contentor de objetos.
    - **Localização**: Selecione uma região.
    - **ClusterName**: Introduza um nome para o cluster do Hadoop.
    - **Nome de início de sessão e palavra-passe do cluster**: O nome de início de sessão predefinido é admin.
    - **Nome de utilizador e palavra-passe do SSH**.
    - **Base de dados SQL nome do servidor de início de sessão e palavra-passe**.
    - **localização de _artifacts**: Utilize o valor predefinido, a menos que desejar usar seu próprio ficheiro backpac numa localização diferente.
    - **Token de Sas de localização de _artifacts**: Deixe em branco.
    - **Nome de ficheiro Bacpac**: Utilize o valor predefinido, a menos que queira utilizar o seu próprio ficheiro backpac.
     
        Os seguintes valores estão codificados na secção de variáveis:
        
        |Name|Value|
        |----|-----|
        | Nome predefinido da conta de armazenamento | &lt;ClusterName > armazenar |
        | Nome de servidor de base de dados SQL do Azure | &lt;ClusterName>dbserver |
        | Nome de base de dados SQL do Azure | &lt;ClusterName>db |
     
3. Selecione **concordo com os termos e condições indicados acima**.
4. Clique em **Comprar**. Verá um novo mosaico intitulado submeter a implementação para a implementação do modelo. A criação do cluster e da SQL Database demora cerca de 20 minutos.

Se optar por utilizar a base de dados SQL do Azure existente ou o Microsoft SQL Server

* **Base de dados SQL do Azure**: Tem de configurar uma regra de firewall para o servidor de base de dados SQL do Azure permitir o acesso da sua estação de trabalho. Para obter instruções sobre como criar uma base de dados SQL do Azure e configurando o firewall, consulte [começar a utilizar a base de dados SQL do Azure][sqldatabase-get-started]. 
  
  > [!NOTE]  
  > Por predefinição, uma base de dados SQL do Azure permite ligações a partir de serviços do Azure, como o Azure HDInsight. Se esta definição de firewall estiver desativada, terá de ativá-la a partir do portal do Azure. Para obter instruções sobre como criar uma base de dados SQL do Azure e configurar regras de firewall, consulte [criar e configurar a base de dados do SQL][sqldatabase-create-configure].

* **SQL Server**: Se o cluster do HDInsight na mesma rede virtual no Azure que o SQL Server, pode utilizar os passos neste artigo para importar e exportar dados para uma base de dados do SQL Server.
  
  > [!NOTE]  
  > HDInsight suporta apenas com base na localização de redes virtuais e não funciona atualmente com redes virtuais baseadas no grupo de afinidade.

  
  * Para criar e configurar uma rede virtual, consulte [criar uma rede virtual com o portal do Azure](../../virtual-network/quick-create-portal.md).
    
    * Quando estiver a utilizar o SQL Server no seu datacenter, tem de configurar a rede virtual como *site a site* ou *ponto a site*.
      
      > [!NOTE]  
      > Para **ponto a site** redes virtuais, do SQL Server tem de executar o cliente VPN do aplicativo de configuração, o que está disponível a partir do **Dashboard** da sua configuração de rede virtual do Azure.


    * Quando estiver a utilizar o SQL Server numa máquina virtual do Azure, qualquer configuração de rede virtual pode ser utilizada se a máquina virtual que aloja o SQL Server for um membro da mesma rede virtual como o HDInsight.
  * Para criar um cluster do HDInsight numa rede virtual, veja [criar Apache Hadoop clusters no HDInsight utilizando opções personalizadas](../hdinsight-hadoop-provision-linux-clusters.md)
    
    > [!NOTE]  
    > SQL Server também tem de permitir a autenticação. Tem de utilizar um início de sessão do SQL Server para concluir os passos neste artigo.


**Para validar a configuração**

1. Abra o grupo de recursos no portal do Azure. Deverá ver quatro recursos no grupo de:

    - o cluster
    - o servidor de base de dados
    - a base de dados
    - a conta de armazenamento predefinida

2. Abra a base de dados no Microsoft SQL Server Management Studio.  Deverá ver duas bases de dados implementados:

    ![O Azure HDInsight Sqoop SQL Management Studio](./media/hdinsight-use-sqoop/hdinsight-sqoop-sql-management-studio.png)


## <a name="run-sqoop-jobs"></a>Executar tarefas de Sqoop
HDInsight pode executar o Sqoop tarefas utilizando uma variedade de métodos. Utilize a tabela seguinte para decidir qual é o método adequado para si, em seguida, siga a ligação para obter instruções.

| **Utilize esta opção** se pretender que... | ... .an **interativo** shell | ... **batch** processamento | ... .with isso **sistema operativo do cluster** | ... .from isso **sistema operativo do cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux |Linux, Unix, Mac OS X ou Windows |
| [.NET SDK para Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |? |Linux ou Windows |Windows (por agora) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Linux ou Windows |Windows |

## <a name="limitations"></a>Limitações
* Exportação em massa - baseado em Linux com o HDInsight, o conector do Sqoop utilizado para exportar dados para o Microsoft SQL Server ou SQL Database do Azure não suporta atualmente inserções em massa.
* Criação de batches - com o HDInsight baseado em Linux, ao utilizar o `-batch` mude ao realizar inserções, Sqoop realiza várias inserções em vez das operações de inserção de criação de batches.

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu a utilizar o Sqoop. Para saber mais, consulte:

* [Utilizar o Apache Hive com o HDInsight](../hdinsight-use-hive.md)
* [Utilizar o Apache Pig com o HDInsight](../hdinsight-use-pig.md)
* [Carregar dados para o HDInsight][hdinsight-upload-data]: Encontre outros métodos para carregar dados para o armazenamento de Blobs do HDInsight/do Azure.

## <a name="appendix-a---a-powershell-sample"></a>Apêndice A – um exemplo do PowerShell
O exemplo do PowerShell executa as seguintes etapas:

1. Ligar ao Azure.
2. Criar um grupo de recursos do Azure. Para obter mais informações, consulte [utilizar o Azure PowerShell com o Azure Resource Manager](../../azure-resource-manager/manage-resource-groups-powershell.md)
3. Crie um servidor de base de dados do Azure SQL, uma base de dados SQL do Azure e duas tabelas. 
   
    Se utilizar o SQL Server em vez disso, utilize as seguintes instruções para criar as tabelas:
   
        CREATE TABLE [dbo].[log4jlogs](
         [t1] [nvarchar](50),
         [t2] [nvarchar](50),
         [t3] [nvarchar](50),
         [t4] [nvarchar](50),
         [t5] [nvarchar](50),
         [t6] [nvarchar](50),
         [t7] [nvarchar](50))
   
        CREATE TABLE [dbo].[mobiledata](
         [clientid] [nvarchar](50),
         [querytime] [nvarchar](50),
         [market] [nvarchar](50),
         [deviceplatform] [nvarchar](50),
         [devicemake] [nvarchar](50),
         [devicemodel] [nvarchar](50),
         [state] [nvarchar](50),
         [country] [nvarchar](50),
         [querydwelltime] [float],
         [sessionid] [bigint],
         [sessionpagevieworder][bigint])
   
    A maneira mais fácil para examinar as tabelas de base de dados e é usar o Visual Studio. O servidor de base de dados e a base de dados podem ser examinados com o portal do Azure.
4. Crie um cluster do HDInsight.
   
    Para examinar o cluster, pode utilizar o portal do Azure ou o Azure PowerShell.
5. Pré-processe o ficheiro de dados de origem.
   
    Neste tutorial, exporte um ficheiro de registo log4j (um arquivo delimitado) e uma tabela do Hive para uma base de dados SQL do Azure. Denomina-se o ficheiro delimitado */example/data/sample.log*. Anteriormente no tutorial, viu alguns exemplos de registos de log4j. No ficheiro de registo, existem algumas linhas vazias e algumas linhas como estas:
   
        java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
            at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)
   
    Esta situação funciona para outros exemplos que usam esses dados, mas antes, pode importar para a base de dados SQL do Azure ou o SQL Server, tem de remover estas exceções. Exportação de Sqoop falha se ocorrer uma cadeia vazia ou uma linha com um menor elemento que o número de campos definidos na tabela de base de dados SQL do Azure. A tabela de log4jlogs tem sete campos do tipo de cadeia de caracteres.
   
    Este procedimento cria um novo ficheiro no cluster: tutorials/usesqoop/data/sample.log. Para examinar o arquivo de dados modificados, pode utilizar o portal do Azure, uma ferramenta de Gerenciador de armazenamento do Azure ou do Azure PowerShell. [Introdução ao HDInsight] [ hdinsight-get-started] tem um código de exemplo com o Azure PowerShell para transferir um ficheiro e exibir o conteúdo do ficheiro.
6. Exporte um ficheiro de dados para a base de dados SQL do Azure.
   
    O ficheiro de origem é tutorials/usesqoop/data/sample.log. A tabela em que os dados são exportados para é chamada log4jlogs.
   
   > [!NOTE]  
   > Além de informações da cadeia de ligação, os passos nesta secção devem funcionar para uma base de dados SQL do Azure ou para o SQL Server. Estes passos foram testados, utilizando a seguinte configuração:
   > 
   > * **Configuração de ponto a site de rede virtual do Azure**: Uma rede virtual ligado ao cluster do HDInsight para um SQL Server no Centro de dados privado. Ver [configurar uma VPN de ponto a Site no Portal de gestão](../../vpn-gateway/vpn-gateway-point-to-site-create.md) para obter mais informações.
   > * **O Azure HDInsight**: Ver [criar clusters Hadoop no HDInsight utilizando opções personalizadas](../hdinsight-hadoop-provision-linux-clusters.md) para obter informações sobre como criar um cluster numa rede virtual.
   > * **SQL Server 2014**: Configurado para permitir a autenticação e a executar o cliente VPN do pacote de configuração para ligar de forma segura à rede virtual.
   > 
   > 
7. Exporte uma tabela do Hive para a base de dados SQL do Azure.
8. Importe a tabela de mobiledata para o cluster do HDInsight.
   
    Para examinar o arquivo de dados modificados, pode utilizar o portal do Azure, uma ferramenta de Gerenciador de armazenamento do Azure ou do Azure PowerShell.  [Introdução ao HDInsight] [ hdinsight-get-started] tem um código de exemplo sobre como utilizar o Azure PowerShell para transferir um ficheiro e exibir o conteúdo do ficheiro.

### <a name="the-powershell-sample"></a>O exemplo do PowerShell

```powershell
# Prepare an Azure SQL database to be used by the Sqoop tutorial

#region - provide the following values

$subscriptionID = "<Enter your Azure Subscription ID>"

$sqlDatabaseLogin = "<Enter a SQL Database Login name>" #SQL Database server login
$sqlDatabasePassword = "<Enter a Password>"

$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = "<Enter a Password>"

$sshUserName = "sshuser" #HDInsight ssh username
$sshPassword = $httpPassword 

# used for creating Azure service names
$nameToken = "<Enter an alias>" 
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
#endregion

#region - variables

# Resource group variables
$resourceGroupName = $namePrefix + "rg"
$location = "East US 2" # used by all Azure services defined in this tutorial

# SQL database varialbes
$sqlDatabaseServerName = $namePrefix + "sqldbserver"
$sqlDatabaseName = $namePrefix + "sqldb"
$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
$sqlDatabaseMaxSizeGB = 10

# Used for retrieving external IP address and creating firewall rules
$ipAddressRestService = "https://bot.whatismyipaddress.com"
$fireWallRuleName = "UseSqoop"

# Used for creating tables and clustered indexes
$cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
    [t1] [nvarchar](50),
    [t2] [nvarchar](50),
    [t3] [nvarchar](50),
    [t4] [nvarchar](50),
    [t5] [nvarchar](50),
    [t6] [nvarchar](50),
    [t7] [nvarchar](50))"

$cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"

$cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
[clientid] [nvarchar](50),
[querytime] [nvarchar](50),
[market] [nvarchar](50),
[deviceplatform] [nvarchar](50),
[devicemake] [nvarchar](50),
[devicemodel] [nvarchar](50),
[state] [nvarchar](50),
[country] [nvarchar](50),
[querydwelltime] [float],
[sessionid] [bigint],
[sessionpagevieworder][bigint])"

$cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"

# HDInsight variables
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName
#endregion

# Treat all errors as terminating
$ErrorActionPreference = "Stop"

#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Connect-AzureRmAccount}
#endregion

#region - Create Azure resource group
Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
try{
    Get-AzureRmResourceGroup -Name $resourceGroupName
}
catch{
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
}
#endregion

#region - Create Azure SQL database server
Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
try{
    Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
catch{
    Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green

    $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

    $sqlDatabaseServerName = (New-AzureRmSqlServer `
                                -ResourceGroupName $resourceGroupName `
                                -ServerName $sqlDatabaseServerName `
                                -SqlAdministratorCredentials $credential `
                                -Location $location).ServerName
    Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan

    Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
    $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
    New-AzureRmSqlServerFirewallRule `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -FirewallRuleName "$fireWallRuleName-workstation" `
        -StartIpAddress $workstationIPAddress `
        -EndIpAddress $workstationIPAddress

    #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
    #Note that this allows Azure traffic from any Azure subscription to access the server.
    New-AzureRmSqlServerFirewallRule `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -FirewallRuleName "$fireWallRuleName-Azureservices" `
        -StartIpAddress "0.0.0.0" `
        -EndIpAddress "0.0.0.0"
}

#endregion

#region - Create and validate Azure SQL database
Write-Host "`nCreating an Azure SQL database ..." -ForegroundColor Green

try {
    Get-AzureRmSqlDatabase `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -DatabaseName $sqlDatabaseName
}
catch {
    Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
    New-AzureRMSqlDatabase `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -DatabaseName $sqlDatabaseName `
        -Edition "Standard" `
        -RequestedServiceObjectiveName "S1"
}

#endregion

#region - Create tables
Write-Host "Creating the log4jlogs table and the mobiledata table ..." -ForegroundColor Green

$conn = New-Object System.Data.SqlClient.SqlConnection
$conn.ConnectionString = $sqlDatabaseConnectionString
$conn.Open()

# Create the log4jlogs table and index
$cmd = New-Object System.Data.SqlClient.SqlCommand
$cmd.Connection = $conn
$cmd.CommandText = $cmdCreateLog4jTable
$ret = $cmd.ExecuteNonQuery()
$cmd.CommandText = $cmdCreateLog4jClusteredIndex
$cmd.ExecuteNonQuery()

# Create the mobiledata table and index
$cmd.CommandText = $cmdCreateMobileTable
$cmd.ExecuteNonQuery()
$cmd.CommandText = $cmdCreateMobileDataClusteredIndex
$cmd.ExecuteNonQuery()

$conn.close()

#endregion


#region - Create HDInsight cluster

Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green

# Create the default storage account
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -Type Standard_LRS

# Create the default Blob container
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
$defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey 
New-AzureStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageAccountContext 

# Create the HDInsight cluster
$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

New-AzureRmHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $HDInsightClusterName `
    -Location $location `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version 3.6 `
    -ClusterSizeInNodes 2 `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $defaultBlobContainerName  

# Validate the cluster
Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
#endregion

#region - pre-process the source file

Write-Host "Preprocessing the source file ..." -ForegroundColor Green

# This procedure creates a new file with $destBlobName
$sourceBlobName = "example/data/sample.log"
$destBlobName = "tutorials/usesqoop/data/sample.log"

# Define the connection string
$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"

# Create block blob objects referencing the source and destination blob.
$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
$storageClient = $storageAccount.CreateCloudBlobClient();
$storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
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

#endregion

#region - export a log file from the cluster to the SQL database

Write-Host "Preprocessing the source file ..." -ForegroundColor Green

$tableName_log4j = "log4jlogs"

# Connection string for Azure SQL Database.
# Comment if using SQL Server
$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
# Connection string for SQL Server.
# Uncomment if using SQL Server.
#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"

$exportDir_log4j = "/tutorials/usesqoop/data"

# Submit a Sqoop job
$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
$sqoopJob = Start-AzureRmHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose
Wait-AzureRmHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
Write-Host "Standard Output" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput

#endregion

#region - export a Hive table

$tableName_mobile = "mobiledata"
$exportDir_mobile = "/hive/warehouse/hivesampletable"

$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
$sqoopJob = Start-AzureRmHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose

Wait-AzureRmHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput

#endregion

#region - import a database

$targetDir_mobile = "/tutorials/usesqoop/importeddata/"

$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"

$sqoopJob = Start-AzureRmHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose

Wait-AzureRmHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput

#endregion
```


[azure-management-portal]: https://portal.azure.com/

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: ../hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../../sql-database/sql-database-get-started.md
[sqldatabase-create-configure]: ../../sql-database/sql-database-get-started.md

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: https://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
