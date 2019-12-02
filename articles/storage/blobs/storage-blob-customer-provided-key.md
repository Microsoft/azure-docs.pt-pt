---
title: Especifique uma chave fornecida pelo cliente em uma solicitação para o armazenamento de BLOBs com o .NET-armazenamento do Azure
description: Saiba como especificar uma chave fornecida pelo cliente em uma solicitação para o armazenamento de BLOBs usando o .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 1c282b1b9a4693da2aa71831a503d443660b65c1
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666640"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Especifique uma chave fornecida pelo cliente em uma solicitação para o armazenamento de BLOBs com o .NET

Os clientes que fazem solicitações no armazenamento de BLOBs do Azure têm a opção de fornecer uma chave de criptografia em uma solicitação individual. Incluir a chave de criptografia na solicitação fornece controle granular das configurações de criptografia para operações de armazenamento de BLOBs. As chaves fornecidas pelo cliente (versão prévia) podem ser armazenadas em Azure Key Vault ou em outro repositório de chaves.

Este artigo mostra como especificar uma chave fornecida pelo cliente em uma solicitação com o .NET.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>Exemplo: usar uma chave fornecida pelo cliente para carregar um blob

O exemplo a seguir cria uma chave fornecida pelo cliente e usa essa chave para carregar um blob. O código carrega um bloco e, em seguida, confirma a lista de blocos para gravar o blob no armazenamento do Azure.

```csharp
async static Task UploadBlobWithClientKey(string accountName, string containerName,
    string blobName, Stream data, byte[] key)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri accountUri = new Uri("https://" + accountName + blobServiceEndpointSuffix);

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = new CustomerProvidedKey(key)
    };

    // Create a client object for the Blob service, including options.
    BlobServiceClient serviceClient = new BlobServiceClient(accountUri, 
        new DefaultAzureCredential(), options);

    // Create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient = serviceClient.GetBlobContainerClient(containerName);

    // Create a new block blob client object.
    // The blob client retains the credential and client options.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>Passos seguintes

- [Criptografia de armazenamento do Azure para dados em repouso](../common/storage-service-encryption.md)
- [Autorizar o acesso a BLOBs e filas com Azure Active Directory e identidades gerenciadas para recursos do Azure](../common/storage-auth-aad-msi.md)
