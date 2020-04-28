---
title: Especifique uma chave fornecida pelo cliente num pedido de armazenamento Blob com .NET - Armazenamento Azure
description: Saiba como especificar uma chave fornecida pelo cliente num pedido de armazenamento Blob utilizando .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ea8254f108aed9d40e6970a27409035b1e10ab41
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74807005"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Especifique uma chave fornecida pelo cliente num pedido de armazenamento blob com .NET

Os clientes que fazem pedidos contra o armazenamento do Blob Azure têm a opção de fornecer uma chave de encriptação num pedido individual. A inclusão da chave de encriptação no pedido fornece controlo granular sobre as definições de encriptação para operações de armazenamento blob. As chaves fornecidas pelo cliente (pré-visualização) podem ser armazenadas no Cofre de Chaves Azure ou noutra loja-chave.

Este artigo mostra como especificar uma chave fornecida pelo cliente num pedido com .NET.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Para saber mais sobre como autenticar com a biblioteca de clientes Azure Identity a partir do Armazenamento Azure, consulte a secção intitulada **Authenticate com a biblioteca Identidade Azure** em [Autorizar o acesso a blobs e filas com o Azure Ative Directory e identidades geridas para a Azure Resources.](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>Exemplo: Utilize uma chave fornecida pelo cliente para carregar uma bolha

O exemplo seguinte cria uma chave fornecida pelo cliente e utiliza essa chave para carregar uma bolha. O código envia um bloco e, em seguida, compromete a lista de blocos para escrever a bolha para o Armazenamento Azure.

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

- [Encriptação azure storage para dados em repouso](../common/storage-service-encryption.md)
- [Autorizar acesso a blobs e filas com diretório ativo azure e identidades geridas para os Recursos Azure](../common/storage-auth-aad-msi.md)
