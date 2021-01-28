---
title: Autenticação não interativa .NET aplicação - Azure HDInsight
description: Saiba como criar aplicações microsoft .NET de autenticação não interativa no Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/23/2019
ms.openlocfilehash: 121c5850ef47999f54d206b95b69e10775d3e5c9
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946083"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Criar uma aplicação de autenticação não interativa .NET HDInsight

Execute a sua aplicação Microsoft .NET Azure HDInsight, quer sob a própria identidade da aplicação (não interativa) quer sob a identidade do utilizador inscrito da aplicação (interativa). Este artigo mostra-lhe como criar uma aplicação de autenticação não interativa .NET para ligar ao Azure e gerir o HDInsight. Para obter uma amostra de uma aplicação interativa, consulte [Connect to Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight).

A partir da sua aplicação .NET não interativa, necessita:

* O seu ID de inquilino de assinatura Azure (também chamado de *ID de diretório).* Consulte [a identificação do inquilino.](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)
* O ID do cliente de aplicação Azure Ative Directory (Azure AD). Consulte [criar uma aplicação de Diretório Ativo Azure](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) e [obter um ID de aplicação](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
* A chave secreta da aplicação AD AZure. Consulte [a chave de autenticação da aplicação](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).

## <a name="prerequisites"></a>Pré-requisitos

Um aglomerado HDInsight. Veja o [tutorial de início.](hadoop/apache-hadoop-linux-tutorial-get-started.md)

## <a name="assign-a-role-to-the-azure-ad-application"></a>Atribuir um papel à aplicação AD Azure

Atribua à sua aplicação AZure AD um [papel,](../role-based-access-control/built-in-roles.md)para lhe conceder permissões para realizar ações. Pode definir o âmbito ao nível da subscrição, grupo de recursos ou recurso. As permissões são herdadas para níveis mais baixos de âmbito. Por exemplo, adicionar uma aplicação à função Reader para um grupo de recursos significa que a aplicação pode ler o grupo de recursos e quaisquer recursos nele. Neste artigo, define-se o âmbito ao nível do grupo de recursos. Para obter mais informações, consulte [utilizar atribuições de funções para gerir o acesso aos seus recursos de subscrição Azure.](../role-based-access-control/role-assignments-portal.md)

**Para adicionar a função do Proprietário à aplicação AD Azure**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para o grupo de recursos que tem o cluster HDInsight no qual irá executar a sua consulta de Hive mais tarde neste artigo. Se tiver um grande número de grupos de recursos, pode usar o filtro para encontrar o que deseja.
1. No menu do grupo de recursos, selecione **Access control (IAM)**.
1. Selecione o **separador funções para** ver as atribuições de funções atuais.
1. No topo da página, selecione **+ Adicionar**.
1. Siga as instruções para adicionar a função Proprietário à sua aplicação AD Azure. Depois de adicionar a função com sucesso, a aplicação é listada sob a função Proprietário.

## <a name="develop-an-hdinsight-client-application"></a>Desenvolver uma aplicação de cliente HDInsight

1. Crie uma aplicação de consola C#.
2. Adicione os seguintes pacotes [NuGet:](https://www.nuget.org/)

    * `Install-Package Microsoft.Azure.Common.Authentication -Pre`
    * `Install-Package Microsoft.Azure.Management.HDInsight -Pre`
    * `Install-Package Microsoft.Azure.Management.Resources -Pre`

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

## <a name="next-steps"></a>Próximos passos

* [Criar um Azure Ative Directory e um diretor de serviço no portal Azure.](../active-directory/develop/howto-create-service-principal-portal.md)
* Saiba como [autenticar um diretor de serviço com o Azure Resource Manager.](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* Saiba mais sobre [o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md).
