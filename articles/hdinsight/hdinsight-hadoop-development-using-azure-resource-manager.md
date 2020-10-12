---
title: Migrar para ferramentas do Azure Resource Manager para HDInsight
description: Como migrar para ferramentas de desenvolvimento do Azure Resource Manager para clusters HDInsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive, devx-track-azurecli
ms.topic: how-to
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: faf13f580f6600e761cdaa9927fee4efa2b5995f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87500185"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrar para ferramentas de desenvolvimento baseadas em recursos Azure para clusters HDInsight

O HDInsight está a depreciar ferramentas baseadas em Azure Service Manager (ASM) para o HDInsight. Se tiver usado Azure PowerShell, Azure Classic CLI ou o HDInsight .NET SDK para trabalhar com clusters HDInsight, é encorajado a utilizar as versões Azure Resource Manager de PowerShell, CLI e .NET SDK. Este artigo fornece indicações sobre como migrar para a nova abordagem baseada em Gestores de Recursos. Sempre que aplicável, este documento destaca as diferenças entre as abordagens ASM e Resource Manager para HDInsight.

> [!IMPORTANT]  
> O suporte para PowerShell, CLI e .NET SDK baseados na ASM será interrompido a **1 de janeiro de 2017**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migrating-azure-classic-cli-to-azure-resource-manager"></a>CLI clássico da Azure migrando para O Gestor de Recursos Azure

> [!IMPORTANT]  
> O Azure CLI não fornece suporte para trabalhar com clusters HDInsight. Você ainda pode usar Azure Classic CLI com HDInsight, no entanto Azure Classic CLI é depreciado.

Seguem-se os comandos básicos para trabalhar com a HDInsight através do CLi Azure CLassic:

* `azure hdinsight cluster create` - cria um novo cluster HDInsight
* `azure hdinsight cluster delete` - elimina um cluster HDInsight existente
* `azure hdinsight cluster show` - mostrar informações sobre um cluster existente
* `azure hdinsight cluster list` - lista clusters HDInsight para a sua subscrição Azure

Utilize o `-h` interruptor para inspecionar os parâmetros e interruptores disponíveis para cada comando.

### <a name="new-commands"></a>Novos comandos
Novos comandos disponíveis com Azure Resource Manager são:

* `azure hdinsight cluster resize` - altera dinamicamente o número de nós de trabalhadores no cluster
* `azure hdinsight cluster enable-http-access` - permite o acesso dos HTTPs ao cluster (por defeito)
* `azure hdinsight cluster disable-http-access` - desativa o acesso dos HTTPs ao cluster
* `azure hdinsight script-action` - fornece comandos para criar/gerir ações de script num cluster
* `azure hdinsight config` - fornece comandos para a criação de um ficheiro de configuração que pode ser utilizado com o `hdinsight cluster create` comando para fornecer informações de configuração.

### <a name="deprecated-commands"></a>Comandos precatados
Se utilizar os `azure hdinsight job` comandos para submeter postos de trabalho ao seu cluster HDInsight, estes comandos não estão disponíveis através dos comandos do Gestor de Recursos. Se precisar de submeter programáticamente empregos para HDInsight a partir de scripts, deverá utilizar as APIs REST fornecidas por HDInsight. Para obter mais informações sobre a submissão de postos de trabalho utilizando ASE DO REST, consulte os seguintes documentos.

* [Executar trabalhos de MapReduce com Hadoop em HDInsight usando cURL](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Executar consultas de Colmeia Apache com Apache Hadoop em HDInsight usando cURL](hadoop/apache-hadoop-use-hive-curl.md)


Para obter informações sobre outras formas de executar Apache Hadoop MapReduce, Apache Hive e Apache Pig interativamente, consulte [Use MapReduce com Hadoop on HDInsight,](hadoop/hdinsight-use-mapreduce.md) [Use Apache Hive com Apache Hadoop em HDInsight,](hadoop/hdinsight-use-hive.md)e [Use Apache Pig com Apache Hadoop em HDInsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Exemplos
**Criar um cluster**

* Comando antigo (ASM) - `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Novo comando - `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Apagar um aglomerado**

* Comando antigo (ASM) - `azure hdinsight cluster delete myhdicluster`
* Novo comando - `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Agrupamentos de listas**

* Comando antigo (ASM) - `azure hdinsight cluster list`
* Novo comando - `azure hdinsight cluster list`

> [!NOTE]  
> Para o comando da lista, especificar o grupo de recursos que utiliza `-g` apenas devolverá os clusters no grupo de recursos especificado.

**Mostrar informações sobre clusters**

* Comando antigo (ASM) - `azure hdinsight cluster show myhdicluster`
* Novo comando - `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migração Azure PowerShell para Azure Resource Manager
As informações gerais sobre o Azure PowerShell no modo Azure Resource Manager podem ser encontradas na [Utilização do Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md).

Os cmdlets Azure PowerShell Resource Manager podem ser instalados lado a lado com os cmdlets ASM. Os cmdlets dos dois modos podem ser distinguidos pelos seus nomes.  O modo Gestor de Recursos tem *AzHDInsight* nos nomes cmdlet comparando com *AzureHDInsight* no modo ASM.  Por exemplo, *New-AzHDInsightCluster* vs. *New-AzureHDInsightCluster*. Parâmetros e comutadores podem ter nomes de notícias, e há muitos novos parâmetros disponíveis quando se utiliza o Gestor de Recursos.  Por exemplo, vários cmdlets requerem um novo interruptor chamado *-ResourceGroupName*. 

Antes de poder utilizar os cmdlets HDInsight, tem de ligar-se à sua conta Azure e criar um novo grupo de recursos:

* [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)
* [New-AzResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Cmdlets renomeados
Para listar os cmdlets ASM HDInsight na consola Windows PowerShell:

```powershell
help *azurehdinsight*
```

A tabela que se segue lista os cmdlets ASM e os seus nomes no modo Gestor de Recursos:

| Cmdlets ASM | Cmdlets gestor de recursos |
| --- | --- |
| Add-AzureHDInsightConfigValue |[Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue) |
| Add-AzureHDInsightMetastore |[Add-AzHDInsightMetastore](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightmetastore) |
| Add-AzureHDInsightScriptAction |[Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) |
| Add-AzureHDInsightStorage |[Add-AzHDInsightstorage](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightstorage) |
| Get-AzureHDInsightCluster |[Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) |
| Get-AzureHDInsightJob |[Get-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjob) |
| Get-AzureHDInsightJobOutput |[Get-AzHDInsightJobOutput](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjoboutput) |
| Get-AzureHDInsightProperty |[Get-AzHDInsightProperty](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightproperty) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/grant-azurermhdinsighthttpservicesaccess) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/grant-azhdinsightrdpservicesaccess) |
| Invoke-AzureHDInsightHiveJob |[Invocar-AzHDInsightHiveJob](https://docs.microsoft.com/powershell/module/az.hdinsight/invoke-azhdinsighthivejob) |
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
| Set-AzureHDInsightClusterSize |[Set-AzHDInsightClustersize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) |
| Set-AzureHDInsightDefaultStorage |[Set-AzHDInsightDefaultstorage](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightdefaultstorage) |
| Start-AzureHDInsightJob |[Start-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/start-azhdinsightjob) |
| Stop-AzureHDInsightJob |[Stop-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/stop-azhdinsightjob) |
| Use-AzureHDInsightCluster |[Use-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/use-azhdinsightcluster) |
| Wait-AzureHDInsightJob |[Wait-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/wait-azhdinsightjob) |

### <a name="new-cmdlets"></a>Novos cmdlets
Seguem-se os novos cmdlets que só estão disponíveis no modo Gestor de Recursos. 

**Cmdlets relacionados com a ação do script:**

* **Get-AzHDInsightPersistedScriptAction**: Obtém as ações de scripts persistidos para um cluster e lista-as por ordem cronológica, ou obtém detalhes para uma ação de script persistiu especificada. 
* **Get-AzHDInsightScriptActionStory**: Obtém o histórico de ação do script para um cluster e lista-o por ordem cronológica inversa, ou obtém detalhes de uma ação de script executada anteriormente. 
* **Remove-AzHDInsightPersistedScriptAction**: Remove uma ação de script persistido de um cluster HDInsight.
* **Set-AzHDInsightPersistedScriptAction**: Define uma ação de script executada anteriormente para ser uma ação de script persistido.
* **Submissão-AzHDInsightScriptAction**: Submete uma nova ação de script a um cluster Azure HDInsight. 

Para obter informações adicionais de utilização, consulte [os clusters HDInsight baseados em Linux utilizando a Script Action](hdinsight-hadoop-customize-cluster-linux.md).

**Cmdlets relacionados com a identidade do cluster:**

* **Add-AzHDInsightClusterIdentity**: Adiciona uma identidade de cluster a um objeto de configuração de cluster para que o cluster HDInsight possa aceder ao Azure Data Lake Storage. Consulte [Criar um cluster HDInsight com armazenamento de data lake usando Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Exemplos
**Criar cluster**

Comando antigo (ASM): 

```azurepowershell
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
```

Novo comando:

```azurepowershell
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
```

**Eliminar o cluster**

Comando antigo (ASM):

```azurepowershell
Remove-AzureHDInsightCluster -name $clusterName 
```

Novo comando:

```azurepowershell
Remove-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
```

**Aglomerado de listas**

Comando antigo (ASM):

```azurepowershell
Get-AzureHDInsightCluster
```

Novo comando:

```azurepowershell
Get-AzHDInsightCluster
```

**Mostrar cluster**

Comando antigo (ASM):

```azurepowershell
Get-AzureHDInsightCluster -Name $clusterName
```

Novo comando:

```azurepowershell
Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName
```

#### <a name="other-samples"></a>Outras amostras
* [Criar clusters do HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Submeta empregos da Colmeia Apache](hadoop/apache-hadoop-use-hive-powershell.md)
* [Submeta empregos apache Sqoop](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migrando para o novo HDInsight .NET SDK
O Azure Service Management Management [(ASM) HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt416619.aspx) está agora precedido. Você é encorajado a usar o [HdInsight .NET SDK baseado em Gestão de Recursos](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)Azure Baseado em Recursos . Os seguintes pacotes HDInsight baseados em ASM estão a ser depreciados.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Esta secção fornece indicações para mais informações sobre como executar determinadas tarefas usando o SDK baseado em recursos.

| Como... utilizando o HDInsight SDK baseado em recursos | Ligações |
| --- | --- |
| Azure HDInsight SDK for .NET (SDK do Azure HDInsight para .NET)|Ver [Azure HDInsight SDK para .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet) |
| Autenticar aplicações interativamente utilizando o Azure Ative Directy com .NET SDK |Consulte [as consultas de Hive Run Apache utilizando .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). O código snippet neste artigo utiliza a abordagem de autenticação interativa. |
| Autenticar aplicações não interativamente utilizando o Azure Ative Directory com .NET SDK |Ver [Criar aplicações não interativas para HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Submeter um trabalho de Hive Apache usando .NET SDK |Ver [Submeter trabalhos de Colmeia Apache](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Submeter um trabalho apache Sqoop usando .NET SDK |Ver [Submeter empregos apache sqoop](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Liste clusters HDInsight usando .NET SDK |Ver [lista de clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Clusters HDInsight escala usando .NET SDK |Ver [clusters hdinsight escala](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Conceder/revogar o acesso a clusters HDInsight utilizando .NET SDK |Ver [Acesso Grant/revogar a clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Atualizar credenciais de utilizador HTTP para clusters HDInsight usando .NET SDK |Consulte [as credenciais de utilizador HTTP da atualização para clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Encontre a conta de armazenamento predefinido para clusters HDInsight usando .NET SDK |Ver [Localizar a conta de armazenamento predefinido para clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Eliminar clusters HDInsight usando .NET SDK |Ver [Excluir clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Exemplos
Seguem-se alguns exemplos sobre a forma como uma operação é realizada utilizando o SDK baseado na ASM e o corte de código equivalente para o SDK baseado em recursos.

**Criar um cluster cliente CRUD**

* Comando antigo (ASM)

  ```azurecli
  //Certificate auth
  //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
  const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
  var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
  var client = HDInsightClient.Connect(cred);
  ```
* Novo comando (Autorização principal do serviço)

  ```azurecli
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
  ```

* Novo comando (autorização do utilizador)

  ```azurecli
  //User auth
  //This will log the application in on behalf of the user.
  //The end-user will see a login popup.
  
  var authFactory = new AuthenticationFactory();
  
  var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
  var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
  var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
  var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
  _hdiManagementClient = new HDInsightManagementClient(creds);
  ```

**Criar um cluster**

* Comando antigo (ASM)

  ```azurecli
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
  ```

* Novo comando

  ```azurecli
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
  ```

**Habilitar acesso HTTP**

* Comando antigo (ASM)

  ```azurecli
  client.EnableHttp(dnsName, "West US", "admin", "*******");
  ```

* Novo comando
  
  ```azurecli
  var httpParams = new HttpSettingsParameters
  {
         HttpUserEnabled = true,
         HttpUsername = "admin",
         HttpPassword = "*******",
  };
  client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);
  ```

**Apagar um aglomerado**

* Comando antigo (ASM)

  ```azurecli
  client.DeleteCluster(dnsName);
  ```

* Novo comando

  ```azurecli
  client.Clusters.Delete(resourceGroup, dnsname);
  ```
