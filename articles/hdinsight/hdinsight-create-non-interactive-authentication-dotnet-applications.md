---
title: Aplicação de autenticação não interativa .NET - Azure HDInsight
description: Saiba como criar aplicações de autenticação não interativa Microsoft .NET no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: 5e6a0586bc750f8972586920c15dbb297295aa20
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79371278"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Criar uma aplicação de autenticação não interativa .NET HDInsight

Execute a sua aplicação Microsoft .NET Azure HDInsight, quer sob a identidade da própria aplicação (não interativa) quer sob a identidade do utilizador inscrito da aplicação (interativa). Este artigo mostra-lhe como criar uma aplicação de autenticação não interativa .NET para ligar ao Azure e gerir o HDInsight. Para obter uma amostra de uma aplicação interativa, consulte [Connect to Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight).

A partir da sua aplicação .NET não interativa, precisa de:

* O seu ID de inquilino de subscrição Azure (também chamado de ID de *diretório).* Ver [Obter identificação do inquilino](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
* O Id do cliente do cliente do cliente do cliente do Azure Ative Directory (Azure AD). Consulte [criar uma aplicação de Diretório Ativo Azure](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) e obter um ID de [aplicação](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
* A chave secreta da aplicação da AD Azure. Ver [Obter chave de autenticação de aplicações](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

## <a name="prerequisites"></a>Pré-requisitos

Um cluster HDInsight. Veja o [tutorial de início.](hadoop/apache-hadoop-linux-tutorial-get-started.md)

## <a name="assign-a-role-to-the-azure-ad-application"></a>Atribuir uma função à aplicação Azure AD

Atribuir ao seu pedido de AD Azure um [papel,](../role-based-access-control/built-in-roles.md)para lhe conceder permissões para realizar ações. Pode definir o âmbito ao nível da subscrição, grupo de recursos ou recurso. As permissões são herdadas para níveis mais baixos de âmbito. Por exemplo, adicionar uma aplicação ao papel do Leitor para um grupo de recursos significa que a aplicação pode ler o grupo de recursos e quaisquer recursos nele. Neste artigo, você define o âmbito ao nível do grupo de recursos. Para mais informações, consulte [As atribuições de funções de utilização para gerir o acesso aos seus recursos de subscrição do Azure.](../role-based-access-control/role-assignments-portal.md)

**Para adicionar a função proprietário à aplicação Azure AD**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para o grupo de recursos que tem o cluster HDInsight no qual irá executar a sua consulta da Hive mais tarde neste artigo. Se tiver um grande número de grupos de recursos, pode usar o filtro para encontrar o que deseja.
1. No menu do grupo de recursos, selecione **controlo de acesso (IAM)**.
1. Selecione o separador de **atribuições de role** para ver as atribuições de funções atuais.
1. No topo da página, selecione **+ Adicionar**.
1. Siga as instruções para adicionar a função Proprietário à sua aplicação Azure AD. Depois de adicionar com sucesso o papel, a aplicação está listada sob a função Proprietário.

## <a name="develop-an-hdinsight-client-application"></a>Desenvolver uma aplicação de cliente HDInsight

1. Crie uma aplicação de consola C#.
2. Adicione os seguintes pacotes [NuGet:](https://www.nuget.org/)

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. Executar o seguinte código:

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

* [Crie um diretor de diretório ativo azure e diretor de serviço no portal Azure.](../active-directory/develop/howto-create-service-principal-portal.md)
* Saiba como autenticar um diretor de serviço com o Gestor de [Recursos Azure.](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* Conheça o Controlo de [Acesso baseado em Papel Azure (RBAC)](../role-based-access-control/role-assignments-portal.md).
