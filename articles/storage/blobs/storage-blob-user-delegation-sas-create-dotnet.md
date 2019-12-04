---
title: Criar uma SAS de delegação de usuário para um contêiner ou BLOB com .NET (versão prévia) – armazenamento do Azure
description: Saiba como criar uma SAS de delegação de usuário (versão prévia) usando as credenciais de Azure Active Directory no armazenamento do Azure usando a biblioteca de cliente .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 32bcb12f39d65d63af1c6595c0d57c695ce0533f
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792212"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>Criar uma SAS de delegação de usuário para um contêiner ou BLOB com .NET (versão prévia)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Este artigo mostra como usar as credenciais do Azure Active Directory (AD do Azure) para criar uma SAS de delegação de usuário (versão prévia) para um contêiner ou BLOB com a biblioteca de cliente de armazenamento do Azure para .NET.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="authenticate-with-the-azure-identity-library"></a>Autenticar com a biblioteca de identidades do Azure

A biblioteca de cliente de identidade do Azure para .NET autentica uma entidade de segurança. Quando seu código está em execução no Azure, a entidade de segurança é uma identidade gerenciada para recursos do Azure.

Quando seu código está em execução no ambiente de desenvolvimento, a autenticação pode ser manipulada automaticamente ou pode exigir um logon do navegador, dependendo de quais ferramentas você está usando. O Microsoft Visual Studio dá suporte ao SSO (logon único), para que a conta de usuário ativa do Azure AD seja usada automaticamente para autenticação. Para obter mais informações sobre o SSO, consulte [logon único para aplicativos](../../active-directory/manage-apps/what-is-single-sign-on.md).

Outras ferramentas de desenvolvimento podem solicitar que você faça logon por meio de um navegador da Web. Você também pode usar uma entidade de serviço para autenticar do ambiente de desenvolvimento. Para obter mais informações, consulte [criar identidade para o aplicativo do Azure no portal](../../active-directory/develop/howto-create-service-principal-portal.md).

Após a autenticação, a biblioteca de cliente de identidade do Azure Obtém uma credencial de token. Essa credencial de token é encapsulada no objeto de cliente de serviço que você cria para executar operações no armazenamento do Azure. A biblioteca lida com isso para você com perfeição obtendo a credencial de token apropriada.

Para obter mais informações sobre a biblioteca de cliente de identidade do Azure, consulte [biblioteca de cliente de identidade do Azure para .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

## <a name="assign-rbac-roles-for-access-to-data"></a>Atribuir funções RBAC para acesso aos dados

Quando uma entidade de segurança do Azure AD tenta acessar dados de BLOB, essa entidade de segurança deve ter permissões para o recurso. Se a entidade de segurança é uma identidade gerenciada no Azure ou uma conta de usuário do Azure AD executando código no ambiente de desenvolvimento, a entidade de segurança deve ser atribuída a uma função de RBAC que concede acesso a dados de blob no armazenamento do Azure. Para obter informações sobre a atribuição de permissões via RBAC, consulte a seção intitulada **atribuir funções RBAC para direitos de acesso** em [autorizar o acesso a BLOBs e filas do Azure usando o Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

## <a name="install-the-packages"></a>Instalar os pacotes

Os exemplos neste artigo usam a versão mais recente da [biblioteca de cliente de armazenamento do Azure para armazenamento de BLOBs](https://www.nuget.org/packages/Azure.Storage.Blobs). Para instalar o pacote, execute o seguinte comando no console do Gerenciador de pacotes NuGet:

```powershell
Install-Package Azure.Storage.Blobs
```

Os exemplos neste artigo também usam a versão mais recente da [biblioteca de cliente de identidade do Azure para .net](https://www.nuget.org/packages/Azure.Identity/) para autenticar com as credenciais do Azure AD. Para instalar o pacote, execute o seguinte comando no console do Gerenciador de pacotes NuGet:

```powershell
Install-Package Azure.Identity
```

## <a name="add-using-directives"></a>Adicionar com diretivas

Adicione as seguintes diretivas `using` ao seu código para usar as bibliotecas de cliente de armazenamento do Azure e identidade do Azure.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure;
using Azure.Identity;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="get-an-authenticated-token-credential"></a>Obter uma credencial de token autenticado

Para obter uma credencial de token que seu código pode usar para autorizar solicitações para o armazenamento do Azure, crie uma instância da classe [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) .

O trecho de código a seguir mostra como obter a credencial de token autenticado e usá-la para criar um cliente de serviço para o armazenamento de BLOBs:

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Obter a chave de delegação do usuário

Cada SAS é assinada com uma chave. Para criar uma SAS de delegação de usuário, você deve primeiro solicitar uma chave de delegação de usuário, que é usada para assinar a SAS. A chave de delegação de usuário é análoga à chave de conta usada para assinar uma SAS de serviço ou uma SAS de conta, exceto que ela depende de suas credenciais do Azure AD. Quando um cliente solicita uma chave de delegação de usuário usando um token OAuth 2,0, o armazenamento do Azure retorna a chave de delegação de usuário em nome do usuário.

Quando tiver a chave de delegação de usuário, você poderá usar essa chave para criar qualquer número de assinaturas de acesso compartilhado de delegação de usuário, durante o tempo de vida da chave. A chave de delegação de usuário é independente do token 2,0 do OAuth usado para adquiri-la, portanto, o token não precisa ser renovado, contanto que a chave ainda seja válida. Você pode especificar que a chave seja válida por um período de até 7 dias.

Use um dos seguintes métodos para solicitar a chave de delegação do usuário:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

O trecho de código a seguir obtém a chave de delegação do usuário e grava suas propriedades:

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>Criar o token SAS

O trecho de código a seguir mostra como criar um novo [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) e fornecer os parâmetros para a SAS de delegação de usuário. O trecho de código chama o [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) para obter a cadeia de caracteres de token SAS. Por fim, o código cria o URI completo, incluindo o endereço de recurso e o token SAS.

```csharp
// Create a SAS token that's valid for one hour.
BlobSasBuilder sasBuilder = new BlobSasBuilder()
{
    BlobContainerName = containerName,
    BlobName = blobName,
    Resource = "b",
    StartsOn = DateTimeOffset.UtcNow,
    ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
};

// Specify read permissions for the SAS.
sasBuilder.SetPermissions(BlobSasPermissions.Read);

// Use the key to get the SAS token.
string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

// Construct the full URI, including the SAS token.
UriBuilder fullUri = new UriBuilder()
{
    Scheme = "https",
    Host = string.Format("{0}.blob.core.windows.net", accountName),
    Path = string.Format("{0}/{1}", containerName, blobName),
    Query = sasToken
};
```

## <a name="example-get-a-user-delegation-sas"></a>Exemplo: obter uma SAS de delegação de usuário

O seguinte método de exemplo mostra o código completo para autenticar a entidade de segurança e criar a SAS de delegação de usuário:

```csharp
async static Task<Uri> GetUserDelegationSasBlob(string accountName, string containerName, string blobName)
{
    // Construct the blob endpoint from the account name.
    string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

    // Create a new Blob service client with Azure AD credentials.  
    BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint), 
                                                            new DefaultAzureCredential());

    // Get a user delegation key for the Blob service that's valid for seven days.
    // You can use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow, 
                                                                        DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
    Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);
    Console.WriteLine();

    // Create a SAS token that's valid for one hour.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        BlobContainerName = containerName,
        BlobName = blobName,
        Resource = "b",
        StartsOn = DateTimeOffset.UtcNow,
        ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
    };

    // Specify read permissions for the SAS.
    sasBuilder.SetPermissions(BlobSasPermissions.Read);

    // Use the key to get the SAS token.
    string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

    // Construct the full URI, including the SAS token.
    UriBuilder fullUri = new UriBuilder()
    {
        Scheme = "https",
        Host = string.Format("{0}.blob.core.windows.net", accountName),
        Path = string.Format("{0}/{1}", containerName, blobName),
        Query = sasToken
    };

    Console.WriteLine("User delegation SAS URI: {0}", fullUri);
    Console.WriteLine();
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Exemplo: ler um blob com uma SAS de delegação de usuário

O exemplo a seguir testa a SAS de delegação de usuário criada no exemplo anterior de um aplicativo cliente simulado. Se a SAS for válida, o aplicativo cliente poderá ler o conteúdo do blob. Se a SAS for inválida, por exemplo, se tiver expirado, o armazenamento do Azure retornará o código de erro 403 (proibido).

```csharp
private static async Task ReadBlobWithSasAsync(Uri sasUri)
{
    // Try performing blob operations using the SAS provided.

    // Create a blob client object for blob operations.
    BlobClient blobClient = new BlobClient(sasUri, null);

    // Download and read the contents of the blob.
    try
    {
        // Download blob contents to a stream and read the stream.
        BlobDownloadInfo blobDownloadInfo = await blobClient.DownloadAsync();
        using (StreamReader reader = new StreamReader(blobDownloadInfo.Content, true))
        {
            string line;
            while ((line = reader.ReadLine()) != null)
            {
                Console.WriteLine(line);
            }
        }

        Console.WriteLine();
        Console.WriteLine("Read operation succeeded for SAS {0}", sasUri);
        Console.WriteLine();
    }
    catch (RequestFailedException e)
    {
        // Check for a 403 (Forbidden) error. If the SAS is invalid, 
        // Azure Storage returns this error.
        if (e.Status == 403)
        {
            Console.WriteLine("Read operation failed for SAS {0}", sasUri);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Ver também

- [Operação de obtenção de chave de delegação de usuário](/rest/api/storageservices/get-user-delegation-key)
- [Criar uma SAS de delegação de usuário (API REST)](/rest/api/storageservices/create-user-delegation-sas)
