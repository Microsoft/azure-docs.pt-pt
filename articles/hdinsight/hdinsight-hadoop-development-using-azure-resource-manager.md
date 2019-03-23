---
title: Migrar para o Azure Resource Manager tools para o HDInsight
description: Como migrar para ferramentas de desenvolvimento do Azure Resource Manager para clusters do HDInsight
services: hdinsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 501f215ae3daf24db6307b4f8afb0c7d3271d8a5
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361868"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrar para as ferramentas de desenvolvimento baseado no Azure Resource Manager para clusters do HDInsight

HDInsight está a descontinuar a ferramentas baseadas no Azure Service Manager ASM para HDInsight. Se tiver utilizado o Azure PowerShell, CLI clássica do Azure ou o HDInsight .NET SDK para trabalhar com clusters do HDInsight, são incentivadas a usar as versões do Azure Resource Manager PowerShell, CLI e SDK do .NET no futuro. Este artigo fornece indicações sobre como migrar para a nova abordagem baseada no Resource Manager. Sempre que aplicável, este documento destaca as diferenças entre as abordagens ASM e o Resource Manager para o HDInsight.

> [!IMPORTANT]  
> O suporte para ASM baseada no PowerShell, CLI, e o .NET SDK irá descontinuar no **dia 1 de Janeiro de 2017**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migrating-azure-classic-cli-to-azure-resource-manager"></a>Migrar CLI clássica do Azure para o Azure Resource Manager

> [!IMPORTANT]  
> CLI do Azure não fornece suporte para trabalhar com clusters do HDInsight. Pode continuar a utilizar CLI clássica do Azure com o HDInsight, no entanto, o CLI clássica do Azure foi preterida.

Seguem-se os comandos básicos para trabalhar com o HDInsight através da CLI clássica do Azure:

* `azure hdinsight cluster create` -cria um novo cluster do HDInsight
* `azure hdinsight cluster delete` -Elimina um cluster do HDInsight existente
* `azure hdinsight cluster show` -apresentar informações sobre um cluster existente
* `azure hdinsight cluster list` – Apresenta uma lista de clusters do HDInsight para a sua subscrição do Azure

Utilize o `-h` comutador para inspecionar os parâmetros e as opções disponíveis para cada comando.

### <a name="new-commands"></a>Novos comandos
Novos comandos disponíveis com o Azure Resource Manager são:

* `azure hdinsight cluster resize` -altera de forma dinâmica o número de nós de trabalho do cluster
* `azure hdinsight cluster enable-http-access` -permite o acesso de HTTPs para o cluster (em por predefinição)
* `azure hdinsight cluster disable-http-access` -Desabilita o acesso de HTTPs para o cluster
* `azure hdinsight script-action` -fornece comandos para criar/gerir as ações de Script num cluster
* `azure hdinsight config` -fornece comandos para criar um ficheiro de configuração que pode ser utilizado com o `hdinsight cluster create` comando para fornecer informações de configuração.

### <a name="deprecated-commands"></a>Comandos preteridos
Se utilizar o `azure hdinsight job` comandos para submeter tarefas ao cluster do HDInsight, esses comandos não estão disponíveis com os comandos do Gestor de recursos. Se precisar de programaticamente submeter tarefas para o HDInsight a partir de scripts, deve usar as APIs de REST fornecidas pelo HDInsight. Para obter mais informações sobre como submeter tarefas com as APIs REST, consulte os seguintes documentos.

* [Executar tarefas de MapReduce com o Hadoop no HDInsight com o cURL](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Executar consultas do Apache Hive com o Apache Hadoop no HDInsight com o cURL](hadoop/apache-hadoop-use-hive-curl.md)
* [Executar tarefas do Apache Pig com o Apache Hadoop no HDInsight com o cURL](hadoop/apache-hadoop-use-pig-curl.md)

Para obter informações sobre outras formas de executar o Apache Hadoop MapReduce, Apache Hive e Apache Pig interativamente, consulte [utilizar o MapReduce com o Hadoop no HDInsight](hadoop/hdinsight-use-mapreduce.md), [utilizar o Apache Hive com o Apache Hadoop no HDInsight](hadoop/hdinsight-use-hive.md), e [utilizar o Apache Pig com o Apache Hadoop no HDInsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Exemplos
**Criar um cluster**

* Antigo comando (ASM)- `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Novo comando- `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**A eliminar um cluster**

* Antigo comando (ASM)- `azure hdinsight cluster delete myhdicluster`
* Novo comando- `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Listar clusters**

* Antigo comando (ASM)- `azure hdinsight cluster list`
* Novo comando- `azure hdinsight cluster list`

> [!NOTE]  
> Para o comando de lista, especificando o grupo de recursos utilizando `-g` irá devolver apenas os clusters no grupo de recursos especificado.

**Mostrar informações de cluster**

* Antigo comando (ASM)- `azure hdinsight cluster show myhdicluster`
* Novo comando- `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migrar o Azure PowerShell para Azure Resource Manager
As informações gerais sobre o Azure PowerShell no modo Azure Resource Manager podem ser encontradas em [utilizar o Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md).

Os cmdlets do PowerShell do Azure Resource Manager podem ser instalados lado a lado com os cmdlets ASM. Os cmdlets de dois modos podem ser distinguidos pelas seus nomes.  Tem o modo Resource Manager *AzHDInsight* nos nomes de cmdlet comparando com *AzureHDInsight* no modo ASM.  Por exemplo, *New-AzHDInsightCluster* vs. *New-AzureHDInsightCluster*. Comutadores e parâmetros que podem ter nomes de notícias, e existem muitos novos parâmetros disponíveis quando utilizar o Gestor de recursos.  Por exemplo, vários cmdlets requerem um novo comutador chamado *- ResourceGroupName*. 

Antes de poder utilizar os cmdlets do HDInsight, tem de ligar à sua conta do Azure e criar um novo grupo de recursos:

* [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)
* [New-AzResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Cmdlets do nome mudado
Para listar os cmdlets de ASM do HDInsight na consola do Windows PowerShell:

    help *azurermhdinsight*

A tabela seguinte lista os cmdlets ASM e seus nomes no modo Resource Manager:

| Cmdlets ASM | Cmdlets do Resource Manager |
| --- | --- |
| Add-AzureHDInsightConfigValues |[Add-AzHDInsightConfigValues](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalues) |
| Add-AzureHDInsightMetastore |[Add-AzHDInsightMetastore](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightmetastore) |
| Add-AzureHDInsightScriptAction |[Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) |
| Add-AzureHDInsightStorage |[Add-AzHDInsightStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightstorage) |
| Get-AzureHDInsightCluster |[Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) |
| Get-AzureHDInsightJob |[Get-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjob) |
| Get-AzureHDInsightJobOutput |[Get-AzHDInsightJobOutput](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjoboutput) |
| Get-AzureHDInsightProperties |[Get-AzHDInsightProperties](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightproperties) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/grant-azhdinsighthttpservicesaccess) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/grant-azhdinsightrdpservicesaccess) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzHDInsightHiveJob](https://docs.microsoft.com/powershell/module/az.hdinsight/invoke-azhdinsighthivejob) |
| New-AzureHDInsightCluster |[New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[New-AzHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) |
| New-AzureHDInsightHiveJobDefinition |[New-AzHDInsightHiveJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsighthivejobdefinition) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzHDInsightMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightmapreducejobdefinition) |
| New-AzureHDInsightPigJobDefinition |[New-AzHDInsightPigJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightpigjobdefinition) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzHDInsightSqoopJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightsqoopjobdefinition) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzHDInsightStreamingMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightstreamingmapreducejobdefinition) |
| Remove-AzureHDInsightCluster |[Remove-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/remove-azhdinsightcluster) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/revoke-azhdinsighthttpservicesaccess) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/revoke-azhdinsightrdpservicesaccess) |
| Set-AzureHDInsightClusterSize |[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) |
| Set-AzureHDInsightDefaultStorage |[Set-AzHDInsightDefaultStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightdefaultstorage) |
| Start-AzureHDInsightJob |[Start-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/start-azhdinsightjob) |
| Stop-AzureHDInsightJob |[Stop-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/stop-azhdinsightjob) |
| Use-AzureHDInsightCluster |[Use-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/use-azhdinsightcluster) |
| Wait-AzureHDInsightJob |[Wait-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/wait-azhdinsightjob) |

### <a name="new-cmdlets"></a>Novos cmdlets
Seguem-se os novos cmdlets que só estão disponíveis no modo Resource Manager. 

**Cmdlets relacionados com a ação de script:**

* **Get-AzHDInsightPersistedScriptAction**: Obtém as ações de script persistentes para um cluster e apresenta uma lista em ordem cronológica ou obtém os detalhes para uma ação de script persistentes especificado. 
* **Get-AzHDInsightScriptActionHistory**: Obtém o histórico de ações de script para um cluster e apresenta uma lista por ordem cronológica reversa ou obtém os detalhes de uma ação de script executados anteriormente. 
* **Remove-AzHDInsightPersistedScriptAction**: Remove uma ação de script persistentes de um cluster do HDInsight.
* **Set-AzHDInsightPersistedScriptAction**: Define uma ação de script executados anteriormente para ser uma ação de script persistentes.
* **Submit-AzHDInsightScriptAction**: Envia uma nova ação de script para um cluster do HDInsight do Azure. 

Para informações de utilização adicionais, consulte [HDInsight baseado em Linux personalizar clusters com ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

**Cmdlets relacionados com a identidade do cluster:**

* **Add-AzHDInsightClusterIdentity**: Adiciona uma identidade de cluster para um objeto de configuração de cluster para que o cluster de HDInsight possa acessar o armazenamento do Azure Data Lake. Ver [criar um cluster do HDInsight com o armazenamento do Data Lake com o Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Exemplos
**Criar cluster**

Antigo comando (ASM): 

    New-AzureHDInsightCluster `
        -Name $clusterName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainerName $containerName `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -Credential $httpCredential `
        -SshCredential $sshCredential

Novo comando:

    New-AzHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainer $containerName  `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpcredentials `
        -SshCredential $sshCredentials


**Eliminar cluster**

Antigo comando (ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

Novo comando:

    Remove-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Cluster de lista**

Antigo comando (ASM):

    Get-AzureHDInsightCluster

Novo comando:

    Get-AzHDInsightCluster 

**Mostrar o cluster**

Antigo comando (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Novo comando:

    Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Outros exemplos
* [Criar clusters do HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Submeter tarefas do Apache Hive](hadoop/apache-hadoop-use-hive-powershell.md)
* [Submeter tarefas do Apache Pig](hadoop/apache-hadoop-use-pig-powershell.md)
* [Submeter tarefas do Apache Sqoop](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migrar para o novo SDK de .NET do HDInsight
A gestão de serviço do Azure com base na [SDK de .NET do HDInsight (ASM)](https://msdn.microsoft.com/library/azure/mt416619.aspx) agora foi preterido. Recomendamos que utilize a gestão de recursos do Azure com base em [com base no Gestor de recursos do SDK .NET do HDInsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight). Os seguintes pacotes do HDInsight baseado em ASM vão ser preteridos.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Esta seção fornece indicadores para obter mais informações sobre como realizar determinadas tarefas utilizando o SDK baseado no Resource Manager.

| Como... com o SDK do HDInsight baseados no Resource Manager | Ligações |
| --- | --- |
| Criar clusters do HDInsight com o .NET SDK |Consulte [clusters do HDInsight criar com o .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| Personalizar um cluster com a ação de Script com o .NET SDK |Consulte [Linux de HDInsight personalizar clusters com ação de Script](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| Autenticar aplicações de forma interativa através do Azure Active Directory com o .NET SDK |Ver [executar Apache consultas do Hive com o .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). O fragmento de código neste artigo utiliza a abordagem de autenticação interativa. |
| Autenticar aplicações de forma não interativa com o Azure Active Directory com o .NET SDK |Consulte [criar aplicativos não-interativa para HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Submeter uma tarefa Apache Hive com o .NET SDK |Consulte [Apache Hive submeter tarefas](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Submeter uma tarefa de Apache Pig com o .NET SDK |Consulte [tarefas de submeter o Apache Pig](hadoop/apache-hadoop-use-pig-dotnet-sdk.md) |
| Submeter uma tarefa de Apache Sqoop com o .NET SDK |Consulte [Apache Sqoop submeter tarefas](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Listar clusters do HDInsight com o .NET SDK |Consulte [clusters do HDInsight de lista](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Dimensionar clusters do HDInsight com o .NET SDK |Consulte [clusters do HDInsight de dimensionamento](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Conceder/revogar o acesso aos clusters do HDInsight com o .NET SDK |Consulte [conceder/revogar o acesso aos clusters do HDInsight](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Atualize as credenciais de utilizador HTTP para os clusters do HDInsight com o .NET SDK |Consulte [credenciais de utilizador de HTTP de atualização para os clusters do HDInsight](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Localizar a conta de armazenamento predefinida para os clusters do HDInsight com o .NET SDK |Consulte [encontrar a conta de armazenamento predefinida para clusters do HDInsight](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Eliminar os clusters do HDInsight com o .NET SDK |Consulte [clusters do HDInsight eliminar](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Exemplos
Seguem-se alguns exemplos de como uma operação é efetuada utilizando o SDK do ASM e o fragmento de código equivalente para o SDK baseado no Resource Manager.

**Criação de um cliente CRUD do cluster**

* Antigo comando (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Novo comando (autorização de principal de serviço)
  
        //Service principal auth
        //This will log the application in as itself, rather than on behalf of a specific user.
        //For details, including how to set up the application, see:
        //   https://azure.microsoft.com/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* Novo comando (autorização do utilizador)
  
        //User auth
        //This will log the application in on behalf of the user.
        //The end-user will see a login popup.
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);

**Criar um cluster**

* Antigo comando (ASM)
  
        var clusterInfo = new ClusterCreateParameters
                    {
                        Name = dnsName,
                        DefaultStorageAccountKey = key,
                        DefaultStorageContainer = defaultStorageContainer,
                        DefaultStorageAccountName = storageAccountDnsName,
                        ClusterSizeInNodes = 1,
                        ClusterType = type,
                        Location = "West US",
                        UserName = "admin",
                        Password = "*******",
                        Version = version,
                        HeadNodeSize = NodeVMSize.Large,
                    };
        clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
        client.CreateCluster(clusterInfo);
* Novo comando
  
        var clusterCreateParameters = new ClusterCreateParameters
            {
                Location = "West US",
                ClusterType = "Hadoop",
                Version = "3.1",
                OSType = OSType.Linux,
                DefaultStorageAccountName = "mystorage.blob.core.windows.net",
                DefaultStorageAccountKey =
                    "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
                UserName = "hadoopuser",
                Password = "*******",
                HeadNodeSize = "ExtraLarge",
                RdpUsername = "hdirp",
                RdpPassword = ""*******",
                RdpAccessExpiry = new DateTime(2025, 3, 1),
                ClusterSizeInNodes = 5
            };
        var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
        clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);

**Ativar o acesso HTTP**

* Antigo comando (ASM)
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* Novo comando
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**A eliminar um cluster**

* Antigo comando (ASM)
  
        client.DeleteCluster(dnsName);
* Novo comando
  
        client.Clusters.Delete(resourceGroup, dnsname);

