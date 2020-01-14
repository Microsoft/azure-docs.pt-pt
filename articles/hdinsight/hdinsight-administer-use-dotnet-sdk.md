---
title: Gerenciar clusters de Apache Hadoop no HDInsight com o SDK do .NET – Azure
description: Saiba como executar tarefas administrativas para os clusters de Apache Hadoop no HDInsight usando o SDK do .NET do HDInsight.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 432b8855ffb9542a1e052c8c97b52bcddeb5c824
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934743"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Gerenciar clusters de Apache Hadoop no HDInsight usando o SDK do .NET

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Saiba como gerenciar clusters HDInsight usando o [SDK do HDInsight.net](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight).

**Pré-requisitos**

Antes de começar este artigo, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="connect-to-azure-hdinsight"></a>Conectar-se ao Azure HDInsight

Você precisa dos seguintes pacotes NuGet:

```powershell
Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
Install-Package Microsoft.Azure.Management.ResourceManager -Pre
Install-Package Microsoft.Azure.Management.HDInsight
```

O exemplo de código a seguir mostra como se conectar ao Azure para que você possa administrar clusters HDInsight em sua assinatura do Azure.

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

Você deverá ver um prompt ao executar este programa.  Se você não quiser ver o prompt, consulte [criar aplicativos .net HDInsight de autenticação não interativa](hdinsight-create-non-interactive-authentication-dotnet-applications.md).


## <a name="list-clusters"></a>Listar clusters

O trecho de código a seguir lista clusters e algumas propriedades:

```csharp
var results = _hdiManagementClient.Clusters.List();
foreach (var name in results.Clusters) {
    Console.WriteLine("Cluster Name: " + name.Name);
    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
    Console.WriteLine("\t Cluster location: " + name.Location);
    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
}
```

## <a name="delete-clusters"></a>Excluir clusters

Use o seguinte trecho de código para excluir um cluster de forma síncrona ou assíncrona: 

```csharp
_hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
_hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
```

## <a name="scale-clusters"></a>Dimensionar clusters

O recurso de dimensionamento de cluster permite que você altere o número de nós de trabalho usados por um cluster que está sendo executado no Azure HDInsight sem precisar recriar o cluster.

> [!NOTE]  
> Há suporte apenas para clusters com o HDInsight versão 3.1.3 ou superior. Se não tiver certeza da versão do cluster, você poderá verificar a página Propriedades.  Consulte [listar e mostrar clusters](hdinsight-administer-use-portal-linux.md#showClusters).

O impacto da alteração do número de nós de dados para cada tipo de cluster com suporte no HDInsight:

* Apache Hadoop
  
    Você pode aumentar diretamente o número de nós de trabalho em um cluster Hadoop em execução sem afetar nenhum trabalho pendente ou em execução. Novos trabalhos também podem ser enviados enquanto a operação está em andamento. As falhas em uma operação de dimensionamento são normalmente manipuladas para que o cluster sempre fique em um estado funcional.
  
    Quando um cluster Hadoop é reduzido reduzindo o número de nós de dados, alguns dos serviços no cluster são reiniciados. Isso faz com que todos os trabalhos em execução e pendentes falhem na conclusão da operação de dimensionamento. Você pode, no entanto, reenviar os trabalhos quando a operação for concluída.
* Apache HBase
  
    Você pode adicionar ou remover nós diretamente em seu cluster HBase enquanto ele está em execução. Os servidores regionais são balanceados automaticamente em alguns minutos após a conclusão da operação de dimensionamento. No entanto, você também pode balancear manualmente os servidores regionais fazendo logon no cabeçalho do cluster e executando os seguintes comandos em uma janela de prompt de comando:
  

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Apache Storm
  
    Você pode adicionar ou remover nós de dados diretamente em seu cluster Storm enquanto ele estiver em execução. Mas, após a conclusão bem-sucedida da operação de dimensionamento, será necessário reequilibrar a topologia.
  
    O rebalanceamento pode ser realizado de duas maneiras:
  
  * Interface do usuário da Web do Storm
  * Ferramenta de interface de linha de comando (CLI)
    
    Consulte a documentação do [Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obter mais detalhes.
    
    A interface do usuário da Web do Storm está disponível no cluster HDInsight:
    
    ![Rebalanceamento de escala do HDInsight Storm](./media/hdinsight-administer-use-powershell/hdinsight-portal-scale-cluster-storm-rebalance.png)
    
    Aqui está um exemplo de como usar o comando da CLI para reequilibrar a topologia do Storm:
    

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

O trecho de código a seguir mostra como redimensionar um cluster de forma síncrona ou assíncrona:

```csharp
_hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
_hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
```

## <a name="grantrevoke-access"></a>Conceder/revogar acesso

Os clusters HDInsight têm os seguintes serviços Web HTTP (todos esses serviços têm pontos de extremidade RESTful):

* ODBC
* JDBC
* Apache Ambari
* Apache Oozie
* Apache Templeton

Por padrão, esses serviços são concedidos para acesso. Você pode revogar/conceder o acesso. Para revogar:

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
> Ao conceder/revogar o acesso, você redefinirá o nome de usuário e a senha do cluster.

Isso também pode ser feito por meio do Portal. Consulte [gerenciar clusters de Apache Hadoop no HDInsight usando o portal do Azure](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>Atualizar credenciais de usuário HTTP

É o mesmo procedimento que conceder/revogar acesso HTTP.  Se o cluster tiver recebido o acesso HTTP, você deverá primeiro revogá-lo.  E, em seguida, conceder o acesso com novas credenciais de usuário HTTP.

## <a name="find-the-default-storage-account"></a>Localizar a conta de armazenamento padrão

O trecho de código a seguir demonstra como obter o nome da conta de armazenamento padrão e a chave de conta de armazenamento padrão para um cluster.

```csharp
var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
foreach (var key in results.Configuration.Keys)
{
    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
}
```

## <a name="submit-jobs"></a>Enviar trabalhos

**Para enviar trabalhos MapReduce**

Consulte [executar exemplos de MapReduce no HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Para enviar trabalhos de Apache Hive** 

Consulte [executar consultas de Apache Hive usando o SDK do .net](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).

**Para enviar trabalhos do Apache Sqoop**

Consulte [usar o Apache Sqoop com o HDInsight](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md).

**Para enviar trabalhos do Apache Oozie**

Consulte [usar o Apache Oozie com o Hadoop para definir e executar um fluxo de trabalho no HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Carregar dados no armazenamento de BLOBs do Azure

Consulte [carregar dados no HDInsight][hdinsight-upload-data].

## <a name="see-also"></a>Veja também

* [Documentação de referência do SDK do .NET do HDInsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Gerenciar clusters de Apache Hadoop no HDInsight usando o portal do Azure](hdinsight-administer-use-portal-linux.md)
* [Administrar o HDInsight usando uma interface de linha de comando][hdinsight-admin-cli]
* [Criar clusters HDInsight][hdinsight-provision]
* [Carregar dados no HDInsight][hdinsight-upload-data]
* [Introdução ao Azure HDInsight][hdinsight-get-started]

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
