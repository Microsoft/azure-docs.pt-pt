---
title: Autenticar o acesso a blobs e filas com identidades geridas para recursos do Azure - armazenamento do Azure | Documentos da Microsoft
description: Armazenamento de BLOBs e filas do Azure suporta a autenticação do Azure Active Directory com identidades geridas para recursos do Azure. Pode utilizar identidades geridas para recursos do Azure para autenticar o acesso a blobs e filas de aplicações em execução em máquinas virtuais do Azure, aplicações de funções, os conjuntos de dimensionamento de máquinas virtuais e outros.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: daa3bd49fcb9970a4a4a026f764cc195423e83d1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154367"
---
# <a name="authenticate-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Autenticar o acesso a blobs e filas com o Azure Active Directory e de identidades geridas para recursos do Azure

Armazenamento de BLOBs e filas do Azure suporta a autenticação do Azure Active Directory (Azure AD) com [geridos identidades para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md). Gerido identidades para recursos do Azure podem autorizar o acesso ao blob e os dados de fila utilizando as credenciais do Azure AD a partir de aplicações em execução em máquinas virtuais do Azure (VMs), aplicações de funções, os conjuntos de dimensionamento de máquinas virtuais e outros serviços. Usando identidades geridas para recursos do Azure em conjunto com a autenticação do Azure AD, é possível evitar o armazenamento de credenciais com as aplicações que são executados na cloud.  

Este artigo mostra como autorizar o acesso aos dados de BLOBs ou filas com uma identidade gerida a partir de uma VM do Azure. 

## <a name="enable-managed-identities-on-a-vm"></a>Ativar identidades geridas numa VM

Antes de poder utilizar identidades geridas para recursos do Azure para autorizar o acesso para blobs e filas da VM, primeiro tem de ativar identidades geridas para recursos do Azure na VM. Para saber como ativar identidades geridas para recursos do Azure, consulte um dos seguintes artigos:

- [Portal do Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [SDKs do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>Conceder permissões para uma identidade do Azure AD gerido

Para autorizar um pedido para o serviço de fila ou Blob de uma identidade gerida no seu aplicativo de armazenamento do Azure, primeiro de configurar as definições de controlo (RBAC) de acesso baseado em funções para essa identidade gerida. O armazenamento do Azure define as funções RBAC que abranger as permissões para dados de BLOBs e filas. Quando lhe for atribuída a função RBAC uma identidade gerida, a identidade gerida receber essas permissões para dados de BLOBs ou filas no âmbito adequado. 

Para obter mais informações sobre a atribuição de funções RBAC, consulte um dos seguintes artigos:

- [Conceder acesso a dados BLOBs e filas do Azure com o RBAC no portal do Azure](storage-auth-aad-rbac-portal.md)
- [Conceder acesso a dados de BLOBs e filas do Azure com o RBAC com a CLI do Azure](storage-auth-aad-rbac-cli.md)
- [Conceder acesso a dados de BLOBs e filas do Azure com o RBAC com o PowerShell](storage-auth-aad-rbac-powershell.md)

## <a name="authorize-with-a-managed-identity-access-token"></a>Autorizar com um token de acesso de identidade gerida

Para autorizar os pedidos com o armazenamento de BLOBs e filas com uma identidade gerida, a sua aplicação ou script tem de adquirir um token de OAuth. O [autenticação de aplicações do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) biblioteca de cliente para .NET (pré-visualização) simplifica o processo de aquisição e renovar um token a partir do código.

A biblioteca de cliente de autenticação da aplicação gere automaticamente a autenticação. A biblioteca utiliza credenciais do programador para autenticar durante o desenvolvimento local. Com as credenciais de desenvolvedor durante o desenvolvimento local é mais seguro porque não é necessário criar as credenciais do Azure AD ou partilhar as credenciais entre os desenvolvedores. Quando a solução for implementada posteriormente para o Azure, a biblioteca muda automaticamente para com as credenciais do aplicativo.

Para utilizar a biblioteca de autenticação da aplicação num aplicativo de armazenamento do Azure, instalar o pacote de pré-visualização mais recente do [Nuget]((https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)), bem como a versão mais recente do [biblioteca de clientes de armazenamento do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). Adicione as seguintes **usando** instruções para o seu código:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
```

A biblioteca de autenticação de aplicação fornece o **AzureServiceTokenProvider** classe. Uma instância desta classe pode ser passada para um retorno de chamada que obtém um token e, em seguida, renova-o antes de expirar.

O exemplo seguinte obtém um token e utiliza-o para criar um blob novo, em seguida, usa o mesmo token para ler o blob.

```csharp
const string blobName = "https://storagesamples.blob.core.windows.net/sample-container/blob1.txt";

// Get the initial access token and the interval at which to refresh it.
AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
var tokenAndFrequency = TokenRenewerAsync(azureServiceTokenProvider, 
                                            CancellationToken.None).GetAwaiter().GetResult();

// Create storage credentials using the initial token, and connect the callback function 
// to renew the token just before it expires
TokenCredential tokenCredential = new TokenCredential(tokenAndFrequency.Token, 
                                                        TokenRenewerAsync,
                                                        azureServiceTokenProvider, 
                                                        tokenAndFrequency.Frequency.Value);

StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a blob using the storage credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName), 
                                            storageCredentials);

// Upload text to the blob.
blob.UploadTextAsync(string.Format("This is a blob named {0}", blob.Name));

// Continue to make requests against Azure Storage. 
// The token is automatically refreshed as needed in the background.
do
{
    // Read blob contents
    Console.WriteLine("Time accessed: {0} Blob Content: {1}", 
                        DateTimeOffset.UtcNow, 
                        blob.DownloadTextAsync().Result);

    // Sleep for ten seconds, then read the contents of the blob again.
    Thread.Sleep(TimeSpan.FromSeconds(10));
} while (true);
```

O método de retorno de chamada verifica o tempo de expiração do token e renova-la conforme necessário:

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
    const string StorageResource = "https://storage.azure.com/";  

    // Use the same token provider to request a new token.
    var authResult = await ((AzureServiceTokenProvider)state).GetAuthenticationResultAsync(StorageResource);

    // Renew the token 5 minutes before it expires.
    var next = (authResult.ExpiresOn - DateTimeOffset.UtcNow) - TimeSpan.FromMinutes(5);
    if (next.Ticks < 0)
    {
        next = default(TimeSpan);
        Console.WriteLine("Renewing token...");
    }

    // Return the new token and the next refresh time.
    return new NewTokenAndFrequency(authResult.AccessToken, next);
}
```

Para obter mais informações sobre a biblioteca de autenticação da aplicação, consulte [autenticação de serviço a serviço para o Azure Key Vault com o .NET](../../key-vault/service-to-service-authentication.md). 

Para saber mais sobre como adquirir um token de acesso, veja [como utilizar identidades geridas para recursos do Azure numa VM do Azure para adquirir um token de acesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

> [!NOTE]
> Para autorizar pedidos relativos a dados de BLOBs ou filas com o Azure AD, tem de utilizar HTTPS para essas solicitações.

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre as funções do RBAC para o armazenamento do Azure, veja [gerir direitos de acesso aos dados de armazenamento com o RBAC](storage-auth-aad-rbac.md).
- Para saber como autorizar o acesso a contentores e filas de dentro dos seus aplicativos de armazenamento, veja [utilize o Azure AD com aplicações de armazenamento](storage-auth-aad-app.md).
- Para saber como executar comandos da CLI do Azure e o PowerShell com credenciais do Azure AD, veja [comandos de executar o Azure CLI ou o PowerShell com credenciais do Azure AD para aceder aos dados de BLOBs ou filas](storage-auth-aad-script.md).