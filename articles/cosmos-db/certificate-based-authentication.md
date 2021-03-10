---
title: Autenticação baseada em certificados com Azure Cosmos DB e Ative Directory
description: Saiba como configurar uma identidade AD Azure para autenticação baseada em certificados para aceder às chaves de acesso a partir de Azure Cosmos DB.
author: voellm
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: 84cbc681d0974e91561daf8918dff389226fa7aa
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553974"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Autenticação baseada em certificados para uma identidade AD Azure para aceder a chaves a partir de uma conta DB da Azure Cosmos
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A autenticação baseada em certificados permite que a aplicação cliente seja autenticada através da utilização do Azure Active Directory (AAD) com um certificado de cliente. Pode executar a autenticação baseada em certificados num computador onde precisa de uma identidade, como um computador no local ou uma máquina virtual no Azure. A sua aplicação pode então ler as teclas DB do Azure Cosmos sem ter as chaves diretamente na aplicação. Este artigo descreve como criar uma aplicação AD Azure de amostra, configurgá-la para autenticação baseada em certificados, assinar no Azure usando a nova identidade da aplicação e, em seguida, recuperar as chaves da sua conta Azure Cosmos. Este artigo utiliza a Azure PowerShell para configurar as identidades e fornece uma aplicação de amostra C# que autentica e acede às chaves da sua conta Azure Cosmos.  

## <a name="prerequisites"></a>Pré-requisitos

* Instale a [versão mais recente](/powershell/azure/install-az-ps) do Azure PowerShell.

* Se não tiver uma [subscrição do Azure,](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="register-an-app-in-azure-ad"></a>Registar uma aplicação no Azure AD

Neste passo, registará uma aplicação web de amostra na sua conta Azure AD. Esta aplicação é posteriormente utilizada para ler as chaves da sua conta DB Azure Cosmos. Utilize as seguintes etapas para registar uma aplicação: 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

1. Abra o painel de **diretório ativo** Azure, vá ao painel **de inscrições da App** e selecione Novo **registo.** 

   :::image type="content" source="./media/certificate-based-authentication/new-app-registration.png" alt-text="Novo registo de candidaturas no Ative Directory":::

1. Preencha ao **Registo um formulário de inscrição** com os seguintes detalhes:  

   * **Nome** – Forneça um nome para a sua aplicação, pode ser qualquer nome como "sampleApp".
   * **Tipos de conta suportados** – Escolha **contas apenas neste diretório organizacional (Diretório padrão)** para permitir que os recursos no seu diretório atual acedam a esta aplicação. 
   * **Redirecionamento URL** – Escolha a aplicação do tipo **Web** e forneça um URL onde a sua aplicação está hospedada, pode ser qualquer URL. Para este exemplo, pode fornecer um URL de teste, uma vez `https://sampleApp.com` que está tudo bem mesmo que a aplicação não exista.

   :::image type="content" source="./media/certificate-based-authentication/register-sample-web-app.png" alt-text="Registar uma aplicação web de amostra":::

1. **Selecione Registar-se** depois de preencher o formulário.

1. Depois de registada a aplicação, tome nota do **ID da Aplicação (cliente)** e **do ID do Objeto,** utilizará estes detalhes nos próximos passos. 

   :::image type="content" source="./media/certificate-based-authentication/get-app-object-ids.png" alt-text="Obtenha os IDs de aplicação e objeto":::

## <a name="install-the-azuread-module"></a>Instale o módulo AzureAD

Neste passo, instalará o módulo Azure AD PowerShell. Este módulo é necessário para obter o ID da aplicação que registou no passo anterior e associar um certificado auto-assinado a essa aplicação. 

1. Abrir o Windows PowerShell ISE com direitos de administrador. Se ainda não o fez, instale o módulo AZ PowerShell e ligue-se à sua subscrição. Se tiver várias subscrições, pode definir o contexto da subscrição atual como mostrado nos seguintes comandos:

   ```powershell
   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. Instalar e importar o módulo [AzureAD](/powershell/module/azuread/)

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Inscreva-se no seu AD Azure

Inscreva-se no seu AD Azure onde registou o pedido. Utilize o comando Connect-AzureAD para iniciar scontabilidade na sua conta, insira as credenciais da sua conta Azure na janela pop-up. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

Abra outra instância do Windows PowerShell ISE e execute os seguintes comandos para criar um certificado auto-assinado e ler a chave associada ao certificado:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Criar a credencial baseada em certificados 

Em seguida, executar os seguintes comandos para obter o objeto ID da sua aplicação e criar a credencial baseada em certificado. Neste exemplo, definimos o certificado para expirar após um ano, pode fixá-lo para qualquer data de fim exigida.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

O comando acima resulta na saída semelhante à imagem abaixo:

:::image type="content" source="./media/certificate-based-authentication/certificate-based-credential-output.png" alt-text="Produção de criação credencial baseada em certificados":::

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Configure a sua conta Azure Cosmos para usar a nova identidade

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

1. Navegue na sua conta Azure Cosmos, abra a lâmina **de controlo de acesso (IAM).**

1. Selecione **adicionar** e **adicionar a atribuição de função**. Adicione a amostraApp que criou no passo anterior com o papel **de Contribuinte,** tal como mostrado na seguinte imagem:

   :::image type="content" source="./media/certificate-based-authentication/configure-cosmos-account-with-identify.png" alt-text="Configure conta Azure Cosmos para usar a nova identidade":::

1. **Selecione Guardar** depois de preencher o formulário

## <a name="register-your-certificate-with-azure-ad"></a>Registe o seu certificado com a Azure AD

Pode associar a credencial baseada em certificados com a aplicação do cliente em Azure AD a partir do portal Azure. Para associar a credencial, deve enviar o ficheiro do certificado com os seguintes passos:

No registo da app Azure para a aplicação do cliente:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

1. Abra o painel de **diretório ativo** Azure, vá ao painel **de registos** da App e abra a aplicação de amostra que criou no passo anterior. 

1. Selecione **Certificados & segredos** e, em seguida, **faça upload do certificado**. Navegue no ficheiro de certificado que criou no passo anterior para carregar.

1. Selecione **Adicionar**. Após o upload do certificado, a impressão digital, a data de início e os valores de validade são apresentados.

## <a name="access-the-keys-from-powershell"></a>Aceda às chaves da PowerShell

Neste passo, você vai assinar no Azure usando a aplicação e o certificado que criou e aceda às chaves da sua conta Azure Cosmos. 

1. Inicialmente, limpe as credenciais da conta Azure que usou para assinar na sua conta. Pode limpar as credenciais utilizando o seguinte comando:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Em seguida, valide que pode entrar no portal Azure utilizando as credenciais da aplicação e aceda às teclas DB do Azure Cosmos:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Get-AzCosmosDBAccountKey `
      -ResourceGroupName "<Resource_Group_Name_of_your_Azure_Cosmos_account>" `
      -Name "<Your_Azure_Cosmos_Account_Name>" `
      -Type "Keys"
   ```

O comando anterior mostrará as chaves primárias primárias primárias e secundárias da sua conta Azure Cosmos. Pode ver o registo de atividade da sua conta Azure Cosmos para validar que o pedido de chaves get conseguiu e o evento é iniciado pela aplicação "sampleApp".

:::image type="content" source="./media/certificate-based-authentication/activity-log-validate-results.png" alt-text="Validar a chamada de chaves no AD AZure":::

## <a name="access-the-keys-from-a-c-application"></a>Aceda às chaves a partir de uma aplicação C# 

Também pode validar este cenário acedendo a chaves a partir de uma aplicação C#. A seguinte aplicação de consola C#, que pode aceder às teclas DB do Azure Cosmos utilizando a aplicação registada no Ative Directory. Certifique-se de atualizar os dados do nome do inquilinoId, clientID, certName, nome do grupo de recursos, ID de assinatura, detalhes do nome da conta Azure Cosmos antes de executar o código. 

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

Este script produz as teclas primárias primárias primárias e secundárias, como mostrado na imagem seguinte:

:::image type="content" source="./media/certificate-based-authentication/csharp-application-output.png" alt-text="saída da aplicação csharp":::

Semelhante à secção anterior, pode ver o registo de atividade da sua conta Azure Cosmos para validar que o evento de pedido de chaves get é iniciado pela aplicação "sampleApp". 


## <a name="next-steps"></a>Passos seguintes

* [Proteger as chaves do Azure Cosmos com o Azure Key Vault](access-secrets-from-keyvault.md)

* [Linha de segurança para Azure Cosmos DB](security-baseline.md)