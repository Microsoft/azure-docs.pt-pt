---
title: Aplicativo .NET de autenticação não interativa-Azure HDInsight
description: Saiba como criar aplicativos Microsoft .NET de autenticação não interativa no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: 1fbb4ef2341148de4026f47fc06a54bbfa60fff6
ms.sourcegitcommit: 801e9118fae92f8eef8d846da009dddbd217a187
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/27/2019
ms.locfileid: "75500131"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Criar um aplicativo .NET HDInsight de autenticação não interativa

Execute o aplicativo Microsoft .NET Azure HDInsight na própria identidade do aplicativo (não interativo) ou sob a identidade do usuário conectado do aplicativo (interativo). Este artigo mostra como criar um aplicativo .NET de autenticação não interativa para se conectar ao Azure e gerenciar o HDInsight. Para obter uma amostra de um aplicativo interativo, consulte [conectar-se ao Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight).

Em seu aplicativo .NET não interativo, você precisa de:

* Sua ID de locatário de assinatura do Azure (também chamada de *ID de diretório*). Consulte [obter ID do locatário](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
* A ID do cliente do aplicativo Azure Active Directory (Azure AD). Consulte [criar um aplicativo Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) e [obter uma ID do aplicativo](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
* A chave secreta do aplicativo do Azure AD. Consulte [obter chave de autenticação de aplicativo](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

## <a name="prerequisites"></a>Pré-requisitos

Cluster HDInsight An. Consulte o [tutorial de introdução](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Atribuir uma função ao aplicativo do Azure AD

Atribua um aplicativo do Azure AD a uma [função](../role-based-access-control/built-in-roles.md), para conceder a ele permissões para executar ações. Você pode definir o escopo no nível da assinatura, do grupo de recursos ou do recurso. As permissões são herdadas para níveis inferiores de escopo. Por exemplo, a adição de um aplicativo à função leitor para um grupo de recursos significa que o aplicativo pode ler o grupo de recursos e todos os recursos nele. Neste artigo, você define o escopo no nível do grupo de recursos. Para obter mais informações, consulte [usar atribuições de função para gerenciar o acesso aos recursos de assinatura do Azure](../role-based-access-control/role-assignments-portal.md).

**Para adicionar a função de proprietário ao aplicativo do Azure AD**

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Navegue até o grupo de recursos que tem o cluster HDInsight no qual você executará a consulta do hive posteriormente neste artigo. Se você tiver um grande número de grupos de recursos, poderá usar o filtro para encontrar o que desejar.
1. No menu do grupo de recursos, selecione **controle de acesso (iam)** .
1. Selecione a guia **atribuições de função** para ver as atribuições de função atuais.
1. Na parte superior da página, selecione **+ Adicionar**.
1. Siga as instruções para adicionar a função de proprietário ao seu aplicativo do Azure AD. Depois de adicionar a função com êxito, o aplicativo será listado na função proprietário.

## <a name="develop-an-hdinsight-client-application"></a>Desenvolver um aplicativo cliente do HDInsight

1. Crie uma aplicação de consola C#.
2. Adicione os seguintes pacotes [NuGet](https://www.nuget.org/) :

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. Execute o seguinte código:

    ```csharp
    using System;
    using System.Security;
    using Microsoft.Azure;
    using Microsoft.Azure.Common.Authentication;
    using Microsoft.Azure.Common.Authentication.Factories;
    using Microsoft.Azure.Common.Authentication.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.HDInsight;
    
    namespace CreateHDICluster
    {
        internal class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
    
            private static Guid SubscriptionId = new Guid("<Enter your Azure subscription ID>");
            private static string tenantID = "<Enter your tenant ID (also called directory ID)>";
            private static string applicationID = "<Enter your application ID>";
            private static string secretKey = "<Enter the application secret key>";
    
            private static void Main(string[] args)
            {
                var key = new SecureString();
                foreach (char c in secretKey) { key.AppendChar(c); }
    
                var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
    
                var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                resourceManagementClient.Providers.Register("Microsoft.HDInsight");
    
                _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
    
                var results = _hdiManagementClient.Clusters.List();
                foreach (var name in results.Clusters)
                {
                    Console.WriteLine("Cluster Name: " + name.Name);
                    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                    Console.WriteLine("\t Cluster location: " + name.Location);
                    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                }
                Console.WriteLine("Press Enter to continue");
                Console.ReadLine();
            }
    
            /// Get the access token for a service principal and provided key.          
            public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
            {
                var authFactory = new AuthenticationFactory();
                var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                var accessToken =
                    authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
    
                return new TokenCloudCredentials(accessToken);
            }
    
            public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
            {
                return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
            }
        }
    }
    ```

## <a name="next-steps"></a>Passos seguintes

* [Crie um aplicativo Azure Active Directory e uma entidade de serviço no portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md).
* Saiba como [autenticar uma entidade de serviço com o Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).
* Saiba mais sobre o [RBAC (controle de acesso baseado em função) do Azure](../role-based-access-control/role-assignments-portal.md).
