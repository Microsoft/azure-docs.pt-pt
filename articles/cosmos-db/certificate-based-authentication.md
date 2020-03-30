---
title: Autenticação baseada em certificado saca-se com Azure Cosmos DB e Diretório Ativo
description: Saiba como configurar uma identidade Azure AD para autenticação baseada em certificados para aceder a chaves do Azure Cosmos DB.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: 085280a8064e4d12ac63939ada7cdb296d47dc70
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365783"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Autenticação baseada em certificado para uma identidade Azure AD para aceder a chaves de uma conta Azure Cosmos DB

A autenticação baseada em certificados permite que a aplicação cliente seja autenticada através da utilização do Azure Active Directory (AAD) com um certificado de cliente. Pode executar a autenticação baseada em certificados num computador onde precisa de uma identidade, como um computador no local ou uma máquina virtual no Azure. A sua aplicação pode então ler as teclas DB Do Azure Cosmos sem ter as chaves diretamente na aplicação. Este artigo descreve como criar uma aplicação AD da amostra, configurá-la para autenticação baseada em certificados, assinar em Azure usando a nova identidade de aplicação, e depois recupera as chaves da sua conta Azure Cosmos. Este artigo utiliza o Azure PowerShell para configurar as identidades e fornece uma aplicação de amostra C# que autentica e acede chaves da sua conta Azure Cosmos.  

## <a name="prerequisites"></a>Pré-requisitos

* Instale a [versão mais recente](/powershell/azure/install-az-ps) do Azure PowerShell.

* Se não tiver uma [subscrição do Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="register-an-app-in-azure-ad"></a>Registar uma aplicação no Azure AD

Neste passo, registará uma aplicação web de amostra na sua conta Azure AD. Esta aplicação é mais tarde utilizada para ler as chaves da sua conta Azure Cosmos DB. Utilize os seguintes passos para registar uma aplicação: 

1. Assine no [portal Azure.](https://portal.azure.com/)

1. Abra o painel de **Diretório Ativo** Azure, vá ao painel de **inscrições** da App e selecione **Nova inscrição.** 

   ![Novo registo de candidaturas no Diretório Ativo](./media/certificate-based-authentication/new-app-registration.png)

1. Preencha o **formulário de inscrição** com os seguintes detalhes:  

   * **Nome** – Forneça um nome para a sua aplicação, podendo ser qualquer nome como "sampleApp".
   * Tipos de **conta suportados** – Escolha **contas apenas neste diretório organizacional (Diretório Padrão)** para permitir que recursos no seu diretório atual acedam a esta aplicação. 
   * **Redirecione o URL** – Escolha a aplicação do tipo **Web** e forneça um URL onde a sua aplicação esteja hospedada, pode ser qualquer URL. Para este exemplo, pode fornecer um `https://sampleApp.com` URL de teste, como está tudo bem mesmo que a aplicação não exista.

   ![Registar uma aplicação web de amostra](./media/certificate-based-authentication/register-sample-web-app.png)

1. Selecione **Registar-se** depois de preencher o formulário.

1. Depois de a aplicação estar registada, tome nota do ID da **Aplicação (cliente)** e do ID do **Objeto,** utilizará estes detalhes nos próximos passos. 

   ![Obtenha a aplicação e as iDs de objeto](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>Instale o módulo AzureAD

Neste passo, irá instalar o módulo PowerShell Azure AD. Este módulo é necessário para obter a identificação da aplicação registada na etapa anterior e associar um certificado auto-assinado a essa aplicação. 

1. Abra o PowerShell ISE do Windows Com direitos de administrador. Se ainda não o fez, instale o módulo AZ PowerShell e ligue-se à sua subscrição. Se tiver várias subscrições, pode definir o contexto da subscrição atual, como mostrado nos seguintes comandos:

   ```powershell
   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. Instalar e importar o módulo [AzureAD](/powershell/module/azuread/?view=azureadps-2.0)

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Assine o seu Anúncio Azure

Assine o seu Anúncio Azure onde tenha registado o pedido. Utilize o comando Connect-AzureAD para iniciar sessão na sua conta, insira as credenciais da sua conta Azure na janela pop-up. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

Abra outra instância do Windows PowerShell ISE e execute os seguintes comandos para criar um certificado auto-assinado e leia a chave associada ao certificado:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Criar a credencial baseada em certificados 

Em seguida, execute os seguintes comandos para obter a identificação do objeto da sua aplicação e criar a credencial baseada em certificado. Neste exemplo, definimos o certificado para expirar após um ano, pode defini-lo para qualquer data final necessária.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

O comando acima resulta na saída semelhante à imagem abaixo:

![Produção de criação de credenciais baseada em certificado](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Configure a sua conta Azure Cosmos para usar a nova identidade

1. Assine no [portal Azure.](https://portal.azure.com/)

1. Navegue na sua conta Azure Cosmos, abra a lâmina de controlo de **acesso (IAM).**

1. Selecione **Adicionar** e Adicionar atribuição de **funções**. Adicione a amostraApp que criou no passo anterior com a função **De Colaborador,** como mostra a seguinte imagem:

   ![Configure conta Azure Cosmos para usar a nova identidade](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. Selecione **Guardar** depois de preencher o formulário

## <a name="register-your-certificate-with-azure-ad"></a>Registe o seu certificado com a Azure AD

Pode associar a credencial baseada em certificado saciar a aplicação do cliente em Azure AD do portal Azure. Para associar a credencial, deve fazer o upload do ficheiro de certificado com as seguintes etapas:

No registo da aplicação Azure para a aplicação do cliente:

1. Assine no [portal Azure.](https://portal.azure.com/)

1. Abra o painel de **Diretório Ativo** Azure, vá ao painel de **registos** da App e abra a aplicação de amostra que criou no passo anterior. 

1. Selecione **Certificados & segredos** e, em seguida, **faça upload de certificado**. Navegue no ficheiro de certificado que criou no passo anterior para carregar.

1. Selecione **Adicionar**. Após o upload do certificado, a impressão digital, a data de início e os valores de validade são apresentados.

## <a name="access-the-keys-from-powershell"></a>Aceda às teclas da PowerShell

Neste passo, você vai assinar no Azure usando a aplicação e o certificado que criou e aceder às chaves da sua conta Azure Cosmos. 

1. Inicialmente limpe as credenciais da conta Azure que usou para assinar na sua conta. Pode limpar credenciais utilizando o seguinte comando:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Em seguida, valide que pode entrar no portal Azure utilizando as credenciais da aplicação e acedendo às teclas Azure Cosmos DB:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Get-AzCosmosDBAccountKey `
      -ResourceGroupName "<Resource_Group_Name_of_your_Azure_Cosmos_account>" `
      -Name "<Your_Azure_Cosmos_Account_Name>" `
      -Type "Keys"
   ```

O comando anterior mostrará as chaves principais e secundárias da sua conta Azure Cosmos. Pode ver o registo de atividadeda sua conta Azure Cosmos para validar que o pedido de obter chaves foi bem sucedido e o evento é iniciado pela aplicação "sampleApp".

![Valide a chamada de obter chaves no Azure AD](./media/certificate-based-authentication/activity-log-validate-results.png)

## <a name="access-the-keys-from-a-c-application"></a>Aceda às chaves de uma aplicação C# 

Também pode validar este cenário acedendo a chaves a partir de uma aplicação C#. A seguinte aplicação de consola C#, que pode aceder às teclas Azure Cosmos DB utilizando a aplicação registada no Ative Directory. Certifique-se de atualizar os dados do nome do grupo de inquilinosId, clientID, certName, nome do grupo de recursos, ID de subscrição, detalhes do nome da conta Azure Cosmos antes de executar o código. 

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
    }
}
```

Este script produz as teclas principais e secundárias primárias e secundárias, como mostra a seguinte imagem:

![saída de aplicação csharp](./media/certificate-based-authentication/csharp-application-output.png)

À semelhança da secção anterior, pode ver o registo de atividadeda sua conta Azure Cosmos para validar que o evento de pedido de chaves de obter é iniciado pela aplicação "sampleApp". 


## <a name="next-steps"></a>Passos seguintes

* [Proteger as chaves do Azure Cosmos com o Azure Key Vault](access-secrets-from-keyvault.md)

* [Base de segurança para Azure Cosmos DB](security-baseline.md)
