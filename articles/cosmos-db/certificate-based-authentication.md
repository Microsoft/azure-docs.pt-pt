---
title: Autenticação do Azure Active Directory com base em certificado com o Azure Cosmos DB
description: Saiba como configurar uma identidade do Azure AD para autenticação baseada em certificado para aceder às chaves do Azure Cosmos DB.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: eb8c98df0f015244adf06a9b57f2223509f1f081
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082966"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-account"></a>Autenticação baseada em certificado de identidade do Azure AD para aceder às chaves de uma conta do Cosmos do Azure

Autenticação baseada em certificado permite que a aplicação de cliente ser autenticado utilizando o Azure Active Directory (Azure AD) com um certificado de cliente. Pode efetuar a autenticação baseada em certificados numa máquina em que precisa que uma identidade, como uma máquina no local ou a máquina virtual no Azure. Seu aplicativo, em seguida, pode ler as chaves do Azure Cosmo DB sem ter das chaves diretamente na aplicação. Este artigo descreve como criar um exemplo de aplicação do Azure AD, configurá-lo para a autenticação baseada em certificado, inicie sessão no Azure com a identidade de aplicação nova e, em seguida, ele obtém as chaves da sua conta do Cosmos do Azure. Este artigo utiliza o Azure PowerShell para configurar as identidades e fornece um C# aplicação de exemplo que efetua a autenticação e obtenha acesso a chaves da sua conta do Cosmos do Azure.  

## <a name="prerequisites"></a>Pré-requisitos

* Instalar o [versão mais recente](/powershell/azure/install-az-ps) do Azure PowerShell.

* Se não tiver uma [subscrição do Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), criar um [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="register-an-app-in-azure-ad"></a>Registar uma aplicação no Azure AD

Neste passo, irá registar uma aplicação web de exemplo na sua conta do Azure AD. Esta aplicação mais tarde é utilizada para ler as chaves da sua conta do Cosmos do Azure. Utilize os seguintes passos para registar uma aplicação: 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

1. Abra o Azure **do Active Directory** painel, aceda ao painel de registos de aplicação e selecione **novo registo**. 

   ![Novo registo de aplicação no Active Directory](./media/certificate-based-authentication/new-app-registration.png)

1. Preencher o **registar uma aplicação** formulário com os seguintes detalhes:  

   * **Nome** – forneça um nome para a sua aplicação, pode ser qualquer nome como "sampleApp".
   * **Tipos de conta suportados** – escolha **contas neste diretório organizacional apenas (diretório predefinição)** para permitir que os recursos no seu diretório atual para aceder a esta aplicação. 
   * **URL de redirecionamento** – escolha a aplicação do tipo **Web** e fornecer um URL onde a sua aplicação estiver alojada, pode ser qualquer URL. Neste exemplo, pode fornecer um URL de teste como `https://sampleApp.com` tem problema, mesmo se a aplicação não existe.

   ![Registar uma aplicação web de exemplo](./media/certificate-based-authentication/register-sample-web-app.png)

1. Selecione **registar** depois de preencher o formulário.

1. Depois da aplicação é registada, anote o **Application(client) ID** e **ID de objeto**, irá utilizar estes detalhes nos passos seguintes. 

   ![Obter os IDs de aplicação e o objeto](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>Instalar o módulo do AzureAD

Neste passo, irá instalar o módulo do PowerShell do Azure AD. Este módulo é necessário para obter o ID da aplicação que registou no passo anterior e associar um certificado autoassinado para essa aplicação. 

1. Abra o ISE do Windows PowerShell com direitos de administrador. Se ainda não o tiver feito, instale o módulo do PowerShell de AZ e ligar à sua subscrição. Se tiver várias subscrições, pode definir o contexto da subscrição atual, conforme mostrado nos seguintes comandos:

   ```powershell

   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. Instalar e importar os [AzureAD](/powershell/module/azuread/?view=azureadps-2.0) módulo

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Inicie sessão no seu Azure AD

Inicie sessão no seu Azure AD em que registou a aplicação. Utilize o comando Connect-AzureAD para iniciar sessão na sua conta, introduza as credenciais da conta do Azure na janela de pop-up. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

Abra outra instância do ISE do Windows PowerShell e execute os seguintes comandos para criar um certificado autoassinado e ler a chave associada ao certificado:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Criar a credencial com base em certificado 

Em seguida, execute os seguintes comandos para obter o ID de objeto da sua aplicação e criar a credencial baseada em certificados. Neste exemplo, definimos o certificado para expirar após um ano, pode defini-la para qualquer data de fim necessária.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

O comando acima resulta numa saída semelhante à captura de ecrã abaixo:

![Saída de criação de credenciais baseada em certificado](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Configurar a sua conta do Cosmos do Azure para utilizar a nova identidade

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

1. Navegue até à sua conta do Cosmos do Azure, abra a **controlo de acesso (IAM)** painel.

1. Selecione **Add** e **adicionar atribuição de função**. Adicionar sampleApp que criou no passo anterior com **contribuinte** função, conforme mostrado na captura de ecrã seguinte:

   ![Configurar conta de Cosmos do Azure para utilizar a nova identidade](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. Selecione **guardar** depois de preencher o formulário


## <a name="access-the-keys-from-powershell"></a>As chaves de acesso a partir do PowerShell

Neste passo, irá iniciar sessão no Azure utilizando o aplicativo e o certificado que criou e acessar chaves da sua conta do Azure Cosmos. 

1. Inicialmente, limpe credenciais da conta do Azure que utilizou para iniciar sessão na sua conta. Pode limpar credenciais com o seguinte comando:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Em seguida valide que pode iniciar sessão no portal do Azure com credenciais do aplicativo e aceder às chaves do Azure Cosmos DB:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Invoke-AzResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <Resource_Group_Name_of_your_Azure_Cosmos_account> -ResourceName <Your_Azure_Cosmos_Account_Name> 
   ```

O comando anterior irá apresentar as chaves de mestra primárias e secundárias da sua conta do Cosmos do Azure. Pode ver o registo de atividade da sua conta do Cosmos do Azure para validar que a solicitação de chaves de get foi concluída com êxito e o evento é iniciado pelo aplicativo "sampleApp". 
 
![Validar a chamada de chaves de get no Azure AD](./media/certificate-based-authentication/activity-log-validate-results.png)


## <a name="access-the-keys-from-a-c-application"></a>Aceder às chaves de um C# aplicação 

Também pode validar este cenário ao aceder às chaves a partir de um C# aplicação. O seguinte C# consola de aplicação, o que podem aceder a chaves do Azure Cosmos DB com a aplicação registada no Active Directory. Certifique-se atualizar o tenantId, clientID, certName, nome do grupo de recursos, ID de subscrição do Azure Cosmos detalhes do nome da conta antes de executar o código. 

```csharp
using System;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Linq;
using System.Net.Http;
using System.Security.Cryptography.X509Certificates;
using System.Threading;
using System.Threading.Tasks;
 
namespace TodoListDaemonWithCert
{
    class Program
    {
        private static string aadInstance = "https://login.windows.net/";
        private static string tenantId = "<Your_Tenant_ID>";
        private static string clientId = "<Your_Client_ID>";
        private static string certName = "<Your_Certificate_Name>";
 
        private static int errorCode = 0;
        static int Main(string[] args)
        {
            MainAync().Wait();
            Console.ReadKey();
 
            return 0;
        } 
 
        static async Task MainAync()
        {
            string authContextURL = aadInstance + tenantId;
            AuthenticationContext authContext = new AuthenticationContext(authContextURL);
            X509Certificate2 cert = ReadCertificateFromStore(certName);
 
            ClientAssertionCertificate credential = new ClientAssertionCertificate(clientId, cert);
            AuthenticationResult result = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);
            if (result == null)
            {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }
 
            string token = result.AccessToken;
            string subscriptionId = "<Your_Subscription_ID>";
            string rgName = "<ResourceGroup_of_your_Cosmos_account>";
            string accountName = "<Your_Cosmos_account_name>";
            string cosmosDBRestCall = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2015-04-08";
 
            Uri restCall = new Uri(cosmosDBRestCall);
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Remove("Authorization");
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + token);
            HttpResponseMessage response = await httpClient.PostAsync(restCall, null);
 
            Console.WriteLine("Got result {0} and keys {1}", response.StatusCode.ToString(), response.Content.ReadAsStringAsync().Result);
        }
 
 
        /// <summary>
        /// Reads the certificate
        /// </summary>
        private static X509Certificate2 ReadCertificateFromStore(string certName)
        {
            X509Certificate2 cert = null;
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection certCollection = store.Certificates;
 
            // Find unexpired certificates.
            X509Certificate2Collection currentCerts = certCollection.Find(X509FindType.FindByTimeValid, DateTime.Now, false);
 
            // From the collection of unexpired certificates, find the ones with the correct name.
            X509Certificate2Collection signingCert = currentCerts.Find(X509FindType.FindBySubjectName, certName, false);
 
            // Return the first certificate in the collection, has the right name and is current.
            cert = signingCert.OfType<X509Certificate2>().OrderByDescending(c => c.NotBefore).FirstOrDefault();
            store.Close();
            return cert;
        }
 
 
        /// <summary>
        /// Get an access token from Azure AD using client credentials.
        /// If the attempt to get a token fails because the server is unavailable, retry twice after 3 seconds each
        /// </summary>
        private static async Task<AuthenticationResult> GetAccessToken(AuthenticationContext authContext, string resourceUri, ClientAssertionCertificate cert)
        {
            //
            // Get an access token from Azure AD using client credentials.
            // If the attempt to get a token fails because the server is unavailable, retry twice after 3 seconds each.
            //
            AuthenticationResult result = null;
            int retryCount = 0;
            bool retry = false;
 
            do
            {
                retry = false;
                errorCode = 0;
 
                try
                {
                    result = await authContext.AcquireTokenAsync(resourceUri, cert);
                }
                catch (AdalException ex)
                {
                    if (ex.ErrorCode == "temporarily_unavailable")
                    {
                        retry = true;
                        retryCount++;
                        Thread.Sleep(3000);
                    }
 
                    Console.WriteLine(
                        String.Format("An error occurred while acquiring a token\nTime: {0}\nError: {1}\nRetry: {2}\n",
                        DateTime.Now.ToString(),
                        ex.ToString(),
                        retry.ToString()));
 
                    errorCode = -1;
                }
 
            } while ((retry == true) && (retryCount < 3));
            return result;
        }
    }
}
```

Este script gera as chaves de mestra primárias e secundárias, conforme mostrado na captura de ecrã seguinte:

![saída da aplicação csharp](./media/certificate-based-authentication/csharp-application-output.png)

Semelhante à seção anterior, pode ver o registo de atividade da sua conta do Cosmos do Azure para validar que o evento de solicitação de chaves de get é iniciado pelo aplicativo "sampleApp". 


## <a name="next-steps"></a>Passos Seguintes

* [Chaves do Azure Cosmos seguras com o Azure Key Vault](access-secrets-from-keyvault.md)

* [Atributos de segurança para o Azure Cosmos DB](cosmos-db-security-attributes.md)