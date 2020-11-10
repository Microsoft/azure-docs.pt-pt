---
title: Especifique uma chave fornecida pelo cliente num pedido de armazenamento blob com .NET - Azure Storage
description: Saiba como especificar uma chave fornecida pelo cliente num pedido de armazenamento Blob usando .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/20/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 50d592d0020ae1b5a704296ef68f5153f0207714
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427580"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Especifique uma chave fornecida pelo cliente num pedido de armazenamento blob com .NET

Os clientes que fazem pedidos contra o armazenamento Azure Blob têm a opção de fornecer uma chave de encriptação num pedido individual. A inclusão da chave de encriptação no pedido fornece controlo granular sobre as definições de encriptação para operações de armazenamento Blob. As chaves fornecidas pelo cliente podem ser armazenadas no Cofre da Chave Azure ou noutra loja de chaves.

Este artigo mostra como especificar uma chave fornecida pelo cliente num pedido com .NET.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Para saber mais sobre como autenticar com a biblioteca de clientes Azure Identity a partir do Azure Storage, consulte a secção intitulada **Autenticação com a biblioteca de Identidade Azure** em [Autorização de acesso a blobs e filas com O Diretório Ativo Azure e identidades geridas para Recursos Azure.](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>Exemplo: Utilize uma chave fornecida pelo cliente para carregar uma bolha

O exemplo a seguir cria uma chave fornecida pelo cliente e utiliza essa chave para carregar uma bolha. O código carrega um bloco e, em seguida, compromete a lista de blocos para escrever a bolha para o Azure Storage.

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

- [Fornecer uma chave de encriptação sobre um pedido para o armazenamento Blob](encryption-customer-provided-keys.md)
- [Azure Storage encryption for data at rest](../common/storage-service-encryption.md) (Encriptação do Armazenamento do Azure para dados inativos)
