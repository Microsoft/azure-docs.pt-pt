---
title: Migrar para o Azure Resource Manager Tools para HDInsight
description: Como migrar para as ferramentas de desenvolvimento Azure Resource Manager para clusters HDInsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 76eb3a135f7a32a30cfa62546a644bc77cf39998
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934591"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrando para ferramentas de desenvolvimento baseadas em Azure Resource Manager para clusters HDInsight

O HDInsight está preterindo as ferramentas baseadas no Azure Service Manager (ASM) para o HDInsight. Se você estiver usando Azure PowerShell, a CLI clássica do Azure ou o SDK do .NET do HDInsight para trabalhar com clusters HDInsight, você será incentivado a usar as versões Azure Resource Manager do PowerShell, da CLI e do SDK do .NET no futuro. Este artigo fornece dicas sobre como migrar para a nova abordagem baseada no Resource Manager. Sempre que aplicável, este documento realça as diferenças entre as abordagens do ASM e do Resource Manager para o HDInsight.

> [!IMPORTANT]  
> O suporte para o PowerShell, a CLI e o SDK do .NET baseados em ASM será interrompido em **1º de janeiro de 2017**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migrating-azure-classic-cli-to-azure-resource-manager"></a>Migrando a CLI clássica do Azure para Azure Resource Manager

> [!IMPORTANT]  
> CLI do Azure não oferece suporte para trabalhar com clusters HDInsight. Você ainda pode usar a CLI clássica do Azure com o HDInsight, no entanto, a CLI clássica do Azure foi preterida.

Veja a seguir os comandos básicos para trabalhar com o HDInsight por meio da CLI clássica do Azure:

* `azure hdinsight cluster create`-cria um novo cluster HDInsight
* `azure hdinsight cluster delete`-exclui um cluster HDInsight existente
* `azure hdinsight cluster show`-exibir informações sobre um cluster existente
* `azure hdinsight cluster list`-listar clusters HDInsight para sua assinatura do Azure

Use a opção `-h` para inspecionar os parâmetros e as opções disponíveis para cada comando.

### <a name="new-commands"></a>Novos comandos
Os novos comandos disponíveis com Azure Resource Manager são:

* `azure hdinsight cluster resize`-altera dinamicamente o número de nós de trabalho no cluster
* `azure hdinsight cluster enable-http-access` – habilita o acesso HTTPs ao cluster (ativado por padrão)
* `azure hdinsight cluster disable-http-access`-desabilita o acesso HTTPs ao cluster
* `azure hdinsight script-action`-fornece comandos para criar/gerenciar ações de script em um cluster
* `azure hdinsight config`-fornece comandos para criar um arquivo de configuração que pode ser usado com o comando `hdinsight cluster create` para fornecer informações de configuração.

### <a name="deprecated-commands"></a>Comandos preteridos
Se você usar os comandos de `azure hdinsight job` para enviar trabalhos para o cluster HDInsight, esses comandos não estarão disponíveis por meio dos comandos do Resource Manager. Se precisar enviar trabalhos por meio de programação para o HDInsight de scripts, você deverá usar as APIs REST fornecidas pelo HDInsight. Para obter mais informações sobre como enviar trabalhos usando APIs REST, consulte os documentos a seguir.

* [Executar trabalhos MapReduce com Hadoop no HDInsight usando ondulação](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Executar Apache Hive consultas com Apache Hadoop no HDInsight usando a ondulação](hadoop/apache-hadoop-use-hive-curl.md)


Para obter informações sobre outras maneiras de executar Apache Hadoop MapReduce, Apache Hive e Apache Pig interativamente, consulte [usar o MapReduce com Hadoop no hdinsight](hadoop/hdinsight-use-mapreduce.md), [usar Apache Hive com Apache Hadoop no HDInsight](hadoop/hdinsight-use-hive.md)e [usar o Apache Pig com o Apache Hadoop no hdinsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Exemplos
**Criando um cluster**

* Comando antigo (ASM)-`azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Novo `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword` de comando

**Excluindo um cluster**

* Comando antigo (ASM)-`azure hdinsight cluster delete myhdicluster`
* Novo `azure hdinsight cluster delete mycluster -g myresourcegroup` de comando

**Listar clusters**

* Comando antigo (ASM)-`azure hdinsight cluster list`
* Novo `azure hdinsight cluster list` de comando

> [!NOTE]  
> Para o comando de lista, especificar o grupo de recursos usando `-g` retornará apenas os clusters no grupo de recursos especificado.

**Mostrar informações do cluster**

* Comando antigo (ASM)-`azure hdinsight cluster show myhdicluster`
* Novo `azure hdinsight cluster show myhdicluster -g myresourcegroup` de comando

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migrando Azure PowerShell para Azure Resource Manager
As informações gerais sobre Azure PowerShell no modo de Azure Resource Manager podem ser encontradas em [usando Azure PowerShell com Azure Resource Manager](../powershell-azure-resource-manager.md).

Os cmdlets Azure PowerShell Resource Manager podem ser instalados lado a lado com os cmdlets do ASM. Os cmdlets dos dois modos podem ser diferenciados por seus nomes.  O modo do Gerenciador de recursos tem *AzHDInsight* nos nomes de cmdlet comparando a *AZUREHDINSIGHT* no modo asm.  Por exemplo, *New-AzHDInsightCluster* versus *New-AzureHDInsightCluster*. Parâmetros e opções podem ter nomes de notícias e há muitos novos parâmetros disponíveis ao usar o Gerenciador de recursos.  Por exemplo, vários cmdlets exigem uma nova opção chamada *-ResourceGroupName*. 

Antes de poder usar os cmdlets do HDInsight, você deve se conectar à sua conta do Azure e criar um novo grupo de recursos:

* [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)
* [New-AzResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Cmdlets renomeados
Para listar os cmdlets do HDInsight ASM no console do Windows PowerShell:

    help *azurehdinsight*

A tabela a seguir lista os cmdlets do ASM e seus nomes no modo do Gerenciador de recursos:

| Cmdlets do ASM | Cmdlets do Gerenciador de recursos |
| --- | --- |
| Add-AzureHDInsightConfigValue |[Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue) |
| Add-AzureHDInsightMetastore |[Add-AzHDInsightMetastore](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightmetastore) |
| Add-AzureHDInsightScriptAction |[Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) |
| Add-AzureHDInsightStorage |[Add-AzHDInsightStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightstorage) |
| Get-AzureHDInsightCluster |[Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) |
| Get-AzureHDInsightJob |[Get-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjob) |
| Get-AzureHDInsightJobOutput |[Get-AzHDInsightJobOutput](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjoboutput) |
| Get-AzureHDInsightProperty |[Get-AzHDInsightProperty](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightproperty) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/grant-azurermhdinsighthttpservicesaccess) |
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
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/revoke-azurermhdinsighthttpservicesaccess) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/revoke-azhdinsightrdpservicesaccess) |
| Set-AzureHDInsightClusterSize |[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) |
| Set-AzureHDInsightDefaultStorage |[Set-AzHDInsightDefaultStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightdefaultstorage) |
| Start-AzureHDInsightJob |[Start-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/start-azhdinsightjob) |
| Stop-AzureHDInsightJob |[Stop-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/stop-azhdinsightjob) |
| Use-AzureHDInsightCluster |[Use-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/use-azhdinsightcluster) |
| Wait-AzureHDInsightJob |[Wait-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/wait-azhdinsightjob) |

### <a name="new-cmdlets"></a>Novos cmdlets
A seguir estão os novos cmdlets que estão disponíveis apenas no modo do Gerenciador de recursos. 

**Cmdlets relacionados à ação de script:**

* **Get-AzHDInsightPersistedScriptAction**: Obtém as ações de script persistentes para um cluster e lista-as em ordem cronológica ou obtém detalhes de uma ação de script persistente especificada. 
* **Get-AzHDInsightScriptActionHistory**: Obtém o histórico de ações de script para um cluster e o lista em ordem cronológica inversa ou obtém detalhes de uma ação de script executada anteriormente. 
* **Remove-AzHDInsightPersistedScriptAction**: Remove uma ação de script persistente de um cluster HDInsight.
* **Set-AzHDInsightPersistedScriptAction**: define uma ação de script executada anteriormente como uma ação de script persistente.
* **Submit-AzHDInsightScriptAction**: envia uma nova ação de script para um cluster do Azure HDInsight. 

Para obter informações adicionais de uso, consulte [Personalizar clusters HDInsight baseados em Linux usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md).

**Cmdlets relacionados à identidade do cluster:**

* **Add-AzHDInsightClusterIdentity**: adiciona uma identidade de cluster a um objeto de configuração de cluster para que o cluster HDInsight possa acessar Azure data Lake Storage. Consulte [criar um cluster HDInsight com data Lake Storage usando Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Exemplos
**Criar cluster**

Comando antigo (ASM): 

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


**Excluir cluster**

Comando antigo (ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

Novo comando:

    Remove-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Listar cluster**

Comando antigo (ASM):

    Get-AzureHDInsightCluster

Novo comando:

    Get-AzHDInsightCluster 

**Mostrar cluster**

Comando antigo (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Novo comando:

    Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Outros exemplos
* [Criar clusters HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Enviar trabalhos de Apache Hive](hadoop/apache-hadoop-use-hive-powershell.md)
* [Enviar trabalhos do Apache Sqoop](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migrando para o novo SDK do .NET do HDInsight
O SDK do .NET do HDInsight baseado no gerenciamento de serviços do Azure [(ASM)](https://msdn.microsoft.com/library/azure/mt416619.aspx) agora está preterido. Você é incentivado a usar o [SDK .net do HDInsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)baseado no gerenciamento de recursos do Azure baseado no Resource Manager. Os seguintes pacotes HDInsight baseados em ASM estão sendo preteridos.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Esta seção fornece ponteiros para mais informações sobre como executar determinadas tarefas usando o SDK baseado no Gerenciador de recursos.

| Como... usando o SDK do HDInsight baseado no Resource Manager | Ligações |
| --- | --- |
| SDK do Azure HDInsight para .NET|Consulte [SDK do Azure HDInsight para .net](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet) |
| Autenticar aplicativos interativamente usando o Azure Active Directory com o SDK do .NET |Consulte [executar consultas de Apache Hive usando o SDK do .net](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). O trecho de código neste artigo usa a abordagem de autenticação interativa. |
| Autenticar aplicativos de forma não interativa usando Azure Active Directory com o SDK do .NET |Consulte [criar aplicativos não interativos para o HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Enviar um trabalho de Apache Hive usando o SDK do .NET |Consulte [enviar trabalhos de Apache Hive](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Enviar um trabalho do Apache Sqoop usando o SDK do .NET |Consulte [enviar trabalhos do Apache Sqoop](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Listar clusters HDInsight usando o SDK do .NET |Consulte [listar clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Dimensionar clusters HDInsight usando o SDK do .NET |Consulte [dimensionar clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Conceder/revogar acesso a clusters HDInsight usando o SDK do .NET |Consulte [conceder/revogar acesso a clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Atualizar credenciais de usuário HTTP para clusters HDInsight usando o SDK do .NET |Consulte [Atualizar credenciais de usuário http para clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Localizar a conta de armazenamento padrão para clusters HDInsight usando o SDK do .NET |Consulte [localizar a conta de armazenamento padrão para clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Excluir clusters HDInsight usando o SDK do .NET |Consulte [excluir clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Exemplos
Veja a seguir alguns exemplos de como uma operação é executada usando o SDK baseado em ASM e o trecho de código equivalente para o SDK baseado no Gerenciador de recursos.

**Criando um cliente CRUD de cluster**

* Comando antigo (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Novo comando (autorização de entidade de serviço)
  
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
* Novo comando (autorização do usuário)
  
        //User auth
        //This will log the application in on behalf of the user.
        //The end-user will see a login popup.
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);

**Criando um cluster**

* Comando antigo (ASM)
  
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

**Habilitando o acesso HTTP**

* Comando antigo (ASM)
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* Novo comando
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**Excluindo um cluster**

* Comando antigo (ASM)
  
        client.DeleteCluster(dnsName);
* Novo comando
  
        client.Clusters.Delete(resourceGroup, dnsname);

