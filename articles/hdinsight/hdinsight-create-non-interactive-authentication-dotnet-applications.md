---
title: Criar aplicações de .NET de autenticação não interativa no Azure HDInsight
description: Saiba como criar aplicativos Microsoft .NET de autenticação não interativa no Azure HDInsight.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: b07a932ef048aa894af990baa57b87529d9da3aa
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717402"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Criar uma aplicação do HDInsight de .NET de autenticação não interativa
Pode executar a aplicação do Microsoft .NET do Azure HDInsight sob a identidade da aplicação (não interativa) ou sob a identidade do utilizador com sessão iniciada a aplicação (interativa). Este artigo mostra-lhe como criar uma aplicação .NET para ligar ao Azure e gerir o HDInsight de autenticação não interativa. Para obter um exemplo de uma aplicação interativa, veja [ligue-se ao Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight). 

Desde a sua aplicação .NET não interativa, terá de:

* O ID de inquilino da subscrição do Azure (também chamado de um *ID de diretório*). Ver [obter ID de inquilino](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id).
* O ID de cliente de aplicação do Azure Active Directory (Azure AD). Ver [criar uma aplicação do Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) e [obter um ID de aplicação](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key).
* Chave secreta da aplicação do Azure AD. Ver [chave de autenticação da aplicação de Get](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key).

## <a name="prerequisites"></a>Pré-requisitos
* Um cluster do HDInsight. Consulte a [tutorial de introdução](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Atribuir uma função de aplicação do Azure AD
Atribuir a aplicação do Azure AD uma [função](../role-based-access-control/built-in-roles.md), para conceder permissão para efetuar ações. Pode definir o âmbito no nível da subscrição, no grupo de recursos ou ao recurso. As permissões são herdadas para níveis inferiores de âmbito. (Por exemplo, adicionar uma aplicação à função do leitor para um grupo de recursos significa que o aplicativo pode ler o grupo de recursos e todos os recursos no mesmo.) Neste tutorial, é possível definir o âmbito ao nível do grupo de recursos. Para obter mais informações, consulte [utilize atribuições de funções para gerir o acesso aos recursos da sua subscrição do Azure](../role-based-access-control/role-assignments-portal.md).

**Para adicionar a função de proprietário para a aplicação do Azure AD**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu à esquerda, selecione **Grupos de recursos**.
3. Selecione o grupo de recursos que tem o cluster de HDInsight no qual irá executar sua consulta do Hive mais tarde neste tutorial. Se tiver um grande número de grupos de recursos, pode utilizar o filtro para encontrar aquele que pretende.
4. No menu de grupo de recursos, selecione **controlo de acesso (IAM)**.
5. Selecione o **atribuições de funções** guia para ver as atribuições de função atual.
6. Na parte superior da página, selecione **adicionar atribuição de função**.
7. Siga as instruções para adicionar a função de proprietário para a sua aplicação do Azure AD. Depois de adicionar a função com êxito, o aplicativo está listado em função de proprietário. 

## <a name="develop-an-hdinsight-client-application"></a>Desenvolver uma aplicação de cliente do HDInsight

1. Crie uma aplicação de consola C#.
2. Adicione as seguintes [NuGet](https://www.nuget.org/) pacotes:

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


## <a name="next-steps"></a>Passos Seguintes
* [Criar um Azure Active Directory principal de aplicações e serviço no portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md).
* Saiba como [autenticar um principal de serviço com o Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).
* Saiba mais sobre [controlo de acesso baseado em função do Azure (RBAC)](../role-based-access-control/role-assignments-portal.md).
