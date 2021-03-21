---
title: Especifique uma chave fornecida pelo cliente num pedido de armazenamento blob com .NET - Azure Storage
description: Saiba como especificar uma chave fornecida pelo cliente num pedido de armazenamento Blob usando .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: c3096da8b3c83dbfe8cfdd6a5fa4d177241334de
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97693512"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Especifique uma chave fornecida pelo cliente num pedido de armazenamento blob com .NET

Os clientes que fazem pedidos contra o armazenamento Azure Blob têm a opção de fornecer uma chave de encriptação AES-256 num pedido individual. A inclusão da chave de encriptação no pedido fornece controlo granular sobre as definições de encriptação para operações de armazenamento Blob. As chaves fornecidas pelo cliente podem ser armazenadas no Cofre da Chave Azure ou noutra loja de chaves.

Este artigo mostra como especificar uma chave fornecida pelo cliente num pedido com .NET.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Para saber mais sobre como autenticar com a biblioteca de clientes da Identidade Azure, consulte a [biblioteca de clientes da Identidade Azure para .NET](/dotnet/api/overview/azure/identity-readme).

## <a name="use-a-customer-provided-key-to-write-to-a-blob"></a>Use uma chave fornecida pelo cliente para escrever a uma bolha

O exemplo a seguir fornece uma chave AES-256 ao carregar uma bolha com a biblioteca de clientes v12 para armazenamento Blob. O exemplo utiliza o objeto [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) para autorizar o pedido de escrita com a Azure AD, mas também pode autorizar o pedido com credenciais de Chave Partilhada.

```csharp
async static Task UploadBlobWithClientKey(Uri blobUri,
                                          Stream data,
                                          byte[] key,
                                          string keySha256)
{
    // Create a new customer-provided key.
    // Key must be AES-256.
    var cpk = new CustomerProvidedKey(key);

    // Check the key's encryption hash.
    if (cpk.EncryptionKeyHash != keySha256)
    {
        throw new InvalidOperationException("The encryption key is corrupted.");
    }

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = cpk
    };

    // Create the client object with options specified.
    BlobClient blobClient = new BlobClient(
        blobUri,
        new DefaultAzureCredential(),
        options);

    // If the container may not exist yet,
    // create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient =
        blobClient.GetParentBlobContainerClient();

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
