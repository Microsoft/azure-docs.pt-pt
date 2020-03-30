---
title: Migrar para ferramentas do Gestor de Recursos Azure para o HDInsight
description: Como migrar para ferramentas de desenvolvimento do Gestor de Recursos Azure para clusters HDInsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 76eb3a135f7a32a30cfa62546a644bc77cf39998
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934591"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrar para ferramentas de desenvolvimento baseadas em Gestor de Recursos Azure para clusters HDInsight

O HDInsight está a depreciar as ferramentas baseadas em Serviços Azure (ASM) para o HDInsight. Se tiver utilizado o Azure PowerShell, o Azure Classic CLI ou o HDInsight .NET SDK para trabalhar com os clusters HDInsight, é encorajado a utilizar as versões Azure Resource Manager de PowerShell, CLI e .NET SDK para avançar. Este artigo fornece indicações sobre como migrar para a nova abordagem baseada em Recursos. Sempre que aplicável, este documento destaca as diferenças entre as abordagens asm e do Gestor de Recursos para o HDInsight.

> [!IMPORTANT]  
> O suporte para PowerShell, CLI e .NET SDK baseados em ASM será interrompido em 1 de janeiro de **2017**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migrating-azure-classic-cli-to-azure-resource-manager"></a>ClI clássico azure migratório para gestor de recursos azure

> [!IMPORTANT]  
> O Azure CLI não fornece suporte para trabalhar com clusters HDInsight. Ainda pode utilizar o Azure Classic CLI com hDInsight, no entanto o Azure Classic CLI está depreceed.

Seguem-se os comandos básicos para trabalhar com o HDInsight através do Azure CLassic CLI:

* `azure hdinsight cluster create`- cria um novo cluster HDInsight
* `azure hdinsight cluster delete`- elimina um cluster HDInsight existente
* `azure hdinsight cluster show`- mostrar informações sobre um cluster existente
* `azure hdinsight cluster list`- lista clusters HDInsight para a sua subscrição Azure

Utilize `-h` o interruptor para inspecionar os parâmetros e os interruptores disponíveis para cada comando.

### <a name="new-commands"></a>Novos comandos
Os novos comandos disponíveis com o Gestor de Recursos Azure são:

* `azure hdinsight cluster resize`- altera dinamicamente o número de nós dos trabalhadores no cluster
* `azure hdinsight cluster enable-http-access`- permite o acesso dos HTTPs ao cluster (por defeito)
* `azure hdinsight cluster disable-http-access`- desativa o acesso dos HTTPs ao cluster
* `azure hdinsight script-action`- fornece comandos para criar/gerir Ações de Script num cluster
* `azure hdinsight config`- fornece comandos para a criação de `hdinsight cluster create` um ficheiro de configuração que pode ser utilizado com o comando para fornecer informações de configuração.

### <a name="deprecated-commands"></a>Comandos precatiados
Se utilizar `azure hdinsight job` os comandos para submeter postos de trabalho ao seu cluster HDInsight, estes comandos não estão disponíveis através dos comandos do Gestor de Recursos. Se precisar de submeter programaticamente trabalhos ao HDInsight a partir de scripts, deve utilizar as APIs REST fornecidas pelo HDInsight. Para obter mais informações sobre a apresentação de trabalhos utilizando APIs REST, consulte os seguintes documentos.

* [Executar mapeiaEmpregos Com Hadoop no HDInsight usando cURL](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Executar consultas de Hiv Apache com Apache Hadoop no HDInsight usando cURL](hadoop/apache-hadoop-use-hive-curl.md)


Para obter informações sobre outras formas de executar Apache Hadoop MapReduce, Apache Hive e Apache Pig interativamente, consulte [Use MapReduce with Hadoop no HDInsight](hadoop/hdinsight-use-mapreduce.md), [Use Apache Hive com Apache Hadoop no HDInsight,](hadoop/hdinsight-use-hive.md)e [use o Porco Apache com Hadoop Apache no HDInsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Exemplos
**Criar um cluster**

* Antigo comando (ASM) -`azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Novo comando -`azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Apagar um aglomerado**

* Antigo comando (ASM) -`azure hdinsight cluster delete myhdicluster`
* Novo comando -`azure hdinsight cluster delete mycluster -g myresourcegroup`

**Aglomerados de listas**

* Antigo comando (ASM) -`azure hdinsight cluster list`
* Novo comando -`azure hdinsight cluster list`

> [!NOTE]  
> Para o comando da lista, `-g` especificar o grupo de recursos que utiliza irá devolver apenas os clusters do grupo de recursos especificado.

**Mostrar informações sobre cluster**

* Antigo comando (ASM) -`azure hdinsight cluster show myhdicluster`
* Novo comando -`azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migração Azure PowerShell para Gestor de Recursos Azure
As informações gerais sobre o Azure PowerShell no modo Gestor de Recursos Azure podem ser encontradas na Utilização da [PowerShell Azure com](../powershell-azure-resource-manager.md)o Gestor de Recursos Azure .

Os cmdlets do Gestor de Recursos Da PowerShell Azure podem ser instalados lado a lado com os cmdlets ASM. Os cmdlets dos dois modos podem ser distinguidos pelos seus nomes.  O modo Gestor de Recursos tem *a AzHDInsight* nos nomes cmdlet comparando com *o AzureHDInsight* no modo ASM.  Por exemplo, *New-AzHDInsightCluster* vs. *New-AzureHDInsightCluster*. Os parâmetros e interruptores podem ter nomes noticiosos, e existem muitos novos parâmetros disponíveis ao utilizar o Gestor de Recursos.  Por exemplo, vários cmdlets requerem um novo interruptor chamado *-ResourceGroupName*. 

Antes de poder utilizar os cmdlets HDInsight, deve ligar-se à sua conta Azure e criar um novo grupo de recursos:

* [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)
* [New-AzResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Cmdlets renomeados
Para listar os cmdlets HDInsight ASM na consola Windows PowerShell:

    help *azurehdinsight*

A tabela seguinte lista os cmdlets ASM e os seus nomes no modo Gestor de Recursos:

| Cmdlets ASM | Cmdlets gestor de recursos |
| --- | --- |
| Add-AzureHDInsightConfigValue |[Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue) |
| Add-AzureHDInsightMetastore |[Loja Add-AzHDInsightMetastore](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightmetastore) |
| Add-AzureHDInsightScriptAction |[Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) |
| Add-AzureHDInsightStorage |[Add-AzHDInsightStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightstorage) |
| Get-AzureHDInsightCluster |[Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) |
| Get-AzureHDInsightJob |[Get-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjob) |
| Get-AzureHDInsightJobOutput |[Get-AzHDInsightJobOutput](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjoboutput) |
| Get-AzureHDInsightProperty |[Get-AzHDInsightProperty](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightproperty) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/grant-azurermhdinsighthttpservicesaccess) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/grant-azhdinsightrdpservicesaccess) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzHDInsightHiveJob](https://docs.microsoft.com/powershell/module/az.hdinsight/invoke-azhdinsighthivejob) |
| New-AzureHDInsightCluster |[Novo AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[New-AzHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) |
| New-AzureHDInsightHiveJobDefinition |[New-AzHDInsightHiveJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsighthivejobdefinition) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzHDInsightMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightmapreducejobdefinition) |
| New-AzureHDInsightPigJobDefinition |[New-AzHDInsightPigJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightpigjobdefinition) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzHDInsightSqoopJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightsqoopjobdefinition) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzHDInsightStreamingMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightstreamingmapreducejobdefinition) |
| Remove-AzureHDInsightCluster |[Remover-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/remove-azhdinsightcluster) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/revoke-azurermhdinsighthttpservicesaccess) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/revoke-azhdinsightrdpservicesaccess) |
| Set-AzureHDInsightClusterSize |[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) |
| Set-AzureHDInsightDefaultStorage |[Set-AzHDInsightDefaultStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightdefaultstorage) |
| Start-AzureHDInsightJob |[Start-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/start-azhdinsightjob) |
| Stop-AzureHDInsightJob |[Stop-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/stop-azhdinsightjob) |
| Use-AzureHDInsightCluster |[Use-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/use-azhdinsightcluster) |
| Wait-AzureHDInsightJob |[Wait-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/wait-azhdinsightjob) |

### <a name="new-cmdlets"></a>Novos cmdlets
Seguem-se os novos cmdlets que só estão disponíveis no modo Gestor de Recursos. 

**Cmdlets relacionados com a ação do script:**

* **Get-AzHDInsightPersistedScriptAction**: Obtém as ações de script persistidas para um cluster e lista-as por ordem cronológica, ou obtém detalhes para uma ação de script persistida especificada. 
* **Get-AzHDInsightScriptActionHistory**: Obtém o histórico de ação do script para um cluster e lista-o em ordem cronológica inversa, ou obtém detalhes de uma ação de script previamente executada. 
* **Remove-AzHDInsightPersistedScriptAction**: Remove uma ação de script persistente de um cluster HDInsight.
* **Set-AzHDInsightPersistedScriptAction**: Define uma ação de script previamente executada como uma ação de script persistente.
* **Sub-AzHDInsightScriptAction**: Submete uma nova ação de script a um cluster Azure HDInsight. 

Para obter informações adicionais sobre o uso, consulte [os clusters HDInsight baseados em Linux utilizando](hdinsight-hadoop-customize-cluster-linux.md)a Ação do Script .

**Cmdlets relacionados com a identidade do cluster:**

* **Add-AzHDInsightClusterIdentity**: Adiciona uma identidade de cluster a um objeto de configuração de cluster para que o cluster HDInsight possa aceder ao Armazenamento de Lagos De Dados Azure. Consulte [criar um cluster HDInsight com armazenamento de data lake usando Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

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


**Eliminar o cluster**

Comando antigo (ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

Novo comando:

    Remove-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Cluster de listas**

Comando antigo (ASM):

    Get-AzureHDInsightCluster

Novo comando:

    Get-AzHDInsightCluster 

**Mostrar cluster**

Comando antigo (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Novo comando:

    Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Outras amostras
* [Criar clusters do HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Submeta empregos apache hive](hadoop/apache-hadoop-use-hive-powershell.md)
* [Submeta empregos Apache Sqoop](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migrando para o novo HDInsight .NET SDK
O [HDInsight (ASM) HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt416619.aspx) baseado em Serviços Azure está agora deprecilado. É encorajado a utilizar o [HDInsight .NET SDK baseado em](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)Gestão de Recursos azure .NET SDK . Os seguintes pacotes HDInsight baseados em ASM estão a ser depreciados.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Esta secção fornece indicações para mais informações sobre como executar certas tarefas usando o SDK baseado em Gestor de Recursos.

| Como... utilizando o HDInsight SDK baseado em Recursos | Ligações |
| --- | --- |
| Azure HDInsight SDK for .NET (SDK do Azure HDInsight para .NET)|Consulte [Azure HDInsight SDK para .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet) |
| Aplicações autenticadas utilizando interativamente o Diretório Ativo Azure com .NET SDK |Consulte [as consultas de Hiv-Hiv e Hive Run Apache utilizando .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). O código deste artigo utiliza a abordagem de autenticação interativa. |
| Aplicações autenticadas não interativas utilizando o Diretório Ativo Azure com .NET SDK |Ver [Criar aplicações não interativas para o HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Submeta um trabalho de Hive Apache usando .NET SDK |Ver [Submeter trabalhos da Colmeia Apache](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Submeta um trabalho apache Sqoop usando .NET SDK |Ver [Submeter empregos Apache Sqoop](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Lista de clusters HDInsight usando .NET SDK |Ver [Clusters HDInsight da Lista](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Clusters De Escala HDInsight usando .NET SDK |Ver [clusters De Escala HDInsight](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Conceder/revogar o acesso aos clusters HDInsight utilizando o .NET SDK |Ver [Grant/revogar o acesso aos clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Atualizar as credenciais de utilizador HTTP para clusters HDInsight usando .NET SDK |Consulte as credenciais de [utilizador do Update HTTP para clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Encontre a conta de armazenamento predefinida para clusters HDInsight utilizando .NET SDK |Consulte a conta de [armazenamento padrão para clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Eliminar clusters HDInsight usando .NET SDK |Ver [eliminar clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Exemplos
Seguem-se alguns exemplos de como uma operação é realizada utilizando o SDK baseado em ASM e o código equivalente para o SDK baseado em Recursos.

**Criação de um cliente CRUD cluster**

* Comando antigo (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Novo comando (Autorização principal de serviço)
  
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

**Habilitar o acesso http**

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

**Apagar um aglomerado**

* Comando antigo (ASM)
  
        client.DeleteCluster(dnsName);
* Novo comando
  
        client.Clusters.Delete(resourceGroup, dnsname);

