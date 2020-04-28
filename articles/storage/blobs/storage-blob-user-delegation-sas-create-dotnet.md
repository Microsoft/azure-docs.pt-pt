---
title: Utilizar .NET para criar uma delegação de utilizadores SAS para um recipiente ou bolha
titleSuffix: Azure Storage
description: Saiba como criar uma delegação de utilizadores SAS com credenciais de Diretório Ativo Azure utilizando a biblioteca de clientes .NET para armazenamento Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 385d2c3b88bc2e4d653dae2dc9670cb9e9388faf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "75371841"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net"></a>Criar uma delegação de utilizadores SAS para um recipiente ou bolha com .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Este artigo mostra como utilizar credenciais de Diretório Ativo Azure (Azure AD) para criar uma delegação de utilizadores SAS para um contentor ou blob com a biblioteca de clientes Azure Storage para .NET.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-rbac-roles-for-access-to-data"></a>Atribuir funções RBAC para acesso aos dados

Quando um diretor de segurança da AD Azure tenta aceder a dados blob, esse diretor de segurança deve ter permissões para o recurso. Quer o diretor de segurança seja uma identidade gerida no Azure ou um código de execução de conta de utilizador Azure AD no ambiente de desenvolvimento, o diretor de segurança deve ser atribuído a uma função RBAC que conceda acesso a dados blob no Armazenamento Azure. Para obter informações sobre a atribuição de permissões via RBAC, consulte a secção intitulada **Assign RBAC para direitos** de acesso em [Autorizao acesso a blobs e filas Azure utilizando o Diretório Ativo Azure](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Para saber mais sobre como autenticar com a biblioteca de clientes Azure Identity a partir do Armazenamento Azure, consulte a secção intitulada **Authenticate com a biblioteca Identidade Azure** em [Autorizar o acesso a blobs e filas com o Azure Ative Directory e identidades geridas para a Azure Resources.](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)

## <a name="add-using-directives"></a>Adicionar com diretivas

Adicione as `using` seguintes diretivas ao seu código para utilizar as bibliotecas de clientes Azure Identity e Azure Storage.

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

## <a name="get-an-authenticated-token-credential"></a>Obtenha uma credencial simbólica autenticada

Para obter uma credencial simbólica que o seu código pode usar para autorizar pedidos de Armazenamento Azure, crie uma instância da classe [DefaultAzureCredential.](/dotnet/api/azure.identity.defaultazurecredential)

O seguinte código de corte mostra como obter a credencial simbólica autenticada e usá-lo para criar um cliente de serviço para armazenamento Blob:

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Obtenha a chave da delegação do utilizador

Todos os SAS são assinados com uma chave. Para criar uma delegação de utilizadores SAS, primeiro deve solicitar uma chave de delegação de utilizadores, que é depois utilizada para assinar o SAS. A chave da delegação do utilizador é análoga à chave da conta utilizada para assinar um serviço SAS ou uma conta SAS, exceto que se baseia nas suas credenciais De AD Azure. Quando um cliente solicita uma chave de delegação de utilizadores utilizando um token OAuth 2.0, o Azure Storage devolve a chave da delegação do utilizador em nome do utilizador.

Assim que tiver a chave da delegação do utilizador, pode utilizar essa chave para criar qualquer número de assinaturas de acesso partilhados da delegação de utilizadores, ao longo da vida útil da chave. A chave da delegação do utilizador é independente do token OAuth 2.0 usado para adquiri-lo, pelo que o símbolo não precisa de ser renovado enquanto a chave ainda for válida. Pode especificar que a chave é válida por um período de até 7 dias.

Utilize um dos seguintes métodos para solicitar a chave da delegação do utilizador:

- [Chave de Delegação getuser](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

O seguinte código de snippet obtém a chave da delegação do utilizador e escreve as suas propriedades:

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

## <a name="create-the-sas-token"></a>Criar o símbolo SAS

Os seguintes programas de código criam um novo [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) e fornecem os parâmetros para a delegação de utilizadores SAS. O corte chama então os [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) para obter a corda token SAS. Finalmente, o código constrói o URI completo, incluindo o endereço de recursos e token SAS.

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

## <a name="example-get-a-user-delegation-sas"></a>Exemplo: Obtenha uma delegação de utilizadores SAS

O seguinte método de exemplo mostra o código completo para autenticar o diretor de segurança e criar a delegação de utilizadores SAS:

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

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Exemplo: Leia uma bolha com uma delegação de utilizadores SAS

O exemplo seguinte testa a delegação de utilizadores SAS criada no exemplo anterior a partir de uma aplicação simulada do cliente. Se o SAS for válido, a aplicação do cliente é capaz de ler o conteúdo da bolha. Se o SAS for inválido, por exemplo, se tiver expirado, o Azure Storage devolve o código de erro 403 (Proibido).

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

## <a name="see-also"></a>Consulte também

- [Conceder acesso limitado aos recursos de Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../common/storage-sas-overview.md)
- [Obtenha a operação chave da delegação do utilizador](/rest/api/storageservices/get-user-delegation-key)
- [Criar uma delegação de utilizadores SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
