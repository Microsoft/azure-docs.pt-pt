---
title: Gerir clusters do Apache Hadoop no HDInsight com o SDK de .NET - Azure
description: Saiba como executar tarefas administrativas para os clusters do Apache Hadoop no HDInsight com o SDK de .NET do HDInsight.
services: hdinsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: d46b1cb5bb8a0b65a9e1b38d5331977132f7eb40
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226458"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Gerir clusters do Apache Hadoop no HDInsight com o .NET SDK
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Saiba como gerir clusters do HDInsight através de [HDInsight.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight).

**Pré-requisitos**

Antes de começar este artigo, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="connect-to-azure-hdinsight"></a>Ligar ao Azure HDInsight

Terá dos seguintes pacotes NuGet:

```
Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
Install-Package Microsoft.Azure.Management.ResourceManager -Pre
Install-Package Microsoft.Azure.Management.HDInsight
```

O exemplo de código seguinte mostra como ligar ao Azure antes de poder administrar clusters do HDInsight com a sua subscrição do Azure.

```csharp
using System;
using Microsoft.Azure;
using Microsoft.Azure.Management.HDInsight;
using Microsoft.Azure.Management.HDInsight.Models;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace HDInsightManagement
{
    class Program
    {
        private static HDInsightManagementClient _hdiManagementClient;
        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

        static void Main(string[] args)
        {
            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            // insert code here

            System.Console.WriteLine("Press ENTER to continue");
            System.Console.ReadLine();
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
        {
            var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
            var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                ClientId, 
                new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                PromptBehavior.Always, 
                UserIdentifier.AnyUser);
            return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
        }
        /// <summary>
        /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
        /// </summary>
        /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
        /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
        /// <param name="authToken">An authentication token for your Azure subscription</param>
        static void EnableHDInsight(TokenCloudCredentials authToken)
        {
            // Create a client for the Resource manager and set the subscription ID
            var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
            resourceManagementClient.SubscriptionId = SubscriptionId;
            // Register the HDInsight provider
            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        }
    }
}
```

Deverá ver uma linha de comandos quando executar esse programa.  Se não quiser ver a linha de comandos, veja [criar aplicações de HDInsight de .NET de autenticação não interativa](hdinsight-create-non-interactive-authentication-dotnet-applications.md).

## <a name="create-clusters"></a>Criar clusters
Consulte [baseado em Linux criar clusters no HDInsight com o SDK .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)

## <a name="list-clusters"></a>Listar clusters
O fragmento de código seguinte apresenta uma lista de clusters e algumas propriedades:

```csharp
var results = _hdiManagementClient.Clusters.List();
foreach (var name in results.Clusters) {
    Console.WriteLine("Cluster Name: " + name.Name);
    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
    Console.WriteLine("\t Cluster location: " + name.Location);
    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
}
```

## <a name="delete-clusters"></a>Eliminar clusters
Utilize o seguinte fragmento de código para eliminar um cluster de forma síncrona ou assíncrona: 

```csharp
_hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
_hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
```

## <a name="scale-clusters"></a>Dimensionar clusters
O funcionalidade de dimensionamento do cluster permite-lhe alterar o número de nós de trabalho utilizado por um cluster que está em execução no Azure HDInsight sem ter de voltar a criar o cluster.

> [!NOTE]  
> Apenas clusters com o HDInsight versão 3.1.3 ou superior são suportadas. Se tiver a certeza de que a versão do seu cluster, consulte a página de propriedades.  Ver [listar e Mostrar clusters](hdinsight-administer-use-portal-linux.md#showClusters).

O impacto de alterar o número de nós de dados para cada tipo de cluster suportada pelo HDInsight:

* Apache Hadoop
  
    Pode facilmente aumentar o número de nós de trabalho num cluster do Hadoop que está a ser executado sem afetar todas as tarefas em execução ou pendentes. Também podem ser submetidas a novas tarefas enquanto a operação estiver em curso. Falhas numa operação de dimensionamento são processadas corretamente para que o cluster está sempre deixado no estado funcional.
  
    Quando um cluster do Hadoop é reduzido, reduzindo o número de nós de dados, alguns dos serviços do cluster são reiniciados. Isso faz com que tudo em execução e tarefas pendentes a falhar após a conclusão da operação de dimensionamento. Pode, no entanto, volte a submeter as tarefas depois de concluída a operação.
* Apache HBase
  
    Pode facilmente adicionar ou remover nós ao cluster do HBase durante a execução. Servidores regionais são balanceadas automaticamente dentro de alguns minutos a concluir a operação de dimensionamento. No entanto, também manualmente pode balancear servidores regionais iniciando sessão no nó principal do cluster e executar os seguintes comandos a partir de uma janela de linha de comandos:
  
    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```
* Apache Storm
  
    Pode facilmente adicionar ou remover nós de dados ao seu cluster do Storm, enquanto estiver em execução. Mas, após a conclusão bem-sucedida da operação de dimensionamento, terá de reequilibrar a topologia.
  
    Reequilíbrio pode ser feito de duas formas:
  
  * IU da web do Storm
  * Ferramenta de interface de linha de comandos (CLI)
    
    Consulte a [documentação do Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obter mais detalhes.
    
    A IU da web de Storm está disponível no cluster do HDInsight:
    
    ![Reequilíbrio de dimensionamento do HDInsight Storm](./media/hdinsight-administer-use-powershell/hdinsight-portal-scale-cluster-storm-rebalance.png)
    
    Eis um exemplo como utilizar o comando da CLI para reequilibrar a topologia do Storm:
    
    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

O fragmento de código seguinte mostra como redimensionar um cluster de forma síncrona ou assíncrona:

```csharp
_hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
_hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
```

## <a name="grantrevoke-access"></a>Conceder/revogar acesso
Clusters do HDInsight tem os seguintes serviços da web HTTP (todos estes serviços tem pontos de extremidade RESTful):

* ODBC
* JDBC
* Apache Ambari
* Apache Oozie
* Apache Templeton

Por predefinição, estes serviços são concedidos para o acesso. Pode revogar/conceder o acesso. Para revogar:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = false,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

Para conceder:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = enable,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

> [!NOTE]  
> Ao conceder/revogar o acesso irá repor o nome de utilizador do cluster e a palavra-passe.

Também pode ser feito através do Portal. Ver [Apache Hadoop gerir clusters no HDInsight com o portal do Azure](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>Atualize as credenciais de utilizador HTTP
É o mesmo procedimento como conceder/revogar HTTP acesso.  Se o cluster tiver sido concedido o acesso HTTP, tem primeiro de o revogar.  E, em seguida, conceder o acesso com novas credenciais de utilizador HTTP.

## <a name="find-the-default-storage-account"></a>Localizar a conta de armazenamento predefinida
O fragmento de código seguinte demonstra como obter o nome de conta do storage predefinida e a chave de conta de armazenamento padrão para um cluster.

```csharp
var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
foreach (var key in results.Configuration.Keys)
{
    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
}
```

## <a name="submit-jobs"></a>Submeter tarefas
**Submeter tarefas de MapReduce**

Ver [exemplos de executar o MapReduce no HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Para submeter tarefas do Apache Hive** 

Ver [executar Apache consultas do Hive com o .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).

**Para submeter tarefas do Apache Pig**

Ver [tarefas de executar o Apache Pig com o .NET SDK](hadoop/apache-hadoop-use-pig-dotnet-sdk.md).

**Submeter tarefas do Apache Sqoop**

Ver [utilizar o Apache Sqoop com o HDInsight](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md).

**Submeter tarefas do Apache Oozie**

Ver [utilização Apache Oozie com o Hadoop para definir e executar um fluxo de trabalho no HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Carregar dados para o armazenamento de Blobs do Azure
Consulte [Upload data to HDInsight (Carregar dados para o HDInsight)][hdinsight-upload-data].

## <a name="see-also"></a>Consultar Também
* [Documentação de referência do SDK de .NET do HDInsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Gerir clusters do Apache Hadoop no HDInsight com o portal do Azure](hdinsight-administer-use-portal-linux.md)
* [Administrar o HDInsight, através de uma interface de linha de comandos][hdinsight-admin-cli]
* [Criar clusters do HDInsight][hdinsight-provision]
* [Upload data to HDInsight (Carregar dados para o HDInsight)][hdinsight-upload-data]
* [Get started with Azure HDInsight (Introdução ao Azure HDInsight)][hdinsight-get-started]

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md


