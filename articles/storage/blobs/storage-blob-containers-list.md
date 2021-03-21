---
title: Listar recipientes blob com .NET - Azure Storage
description: Saiba como listar os recipientes blob na sua conta de Armazenamento Azure utilizando a biblioteca de clientes .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: a12fc991734fe74e450aa14a477f3a4500ba659c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96937258"
---
# <a name="list-blob-containers-with-net"></a>Listar recipientes blob com .NET

Quando lista os contentores numa conta de Armazenamento Azure a partir do seu código, pode especificar uma série de opções para gerir como os resultados são devolvidos do Azure Storage. Este artigo mostra como listar os recipientes utilizando a biblioteca do [cliente do Azure Storage para .NET](/dotnet/api/overview/azure/storage).  

## <a name="understand-container-listing-options"></a>Compreender opções de listagem de contentores

Para listar os recipientes na sua conta de armazenamento, ligue para um dos seguintes métodos:

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

- [GetBlobContainers](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainers)
- [GetBlobContainersAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainersasync)

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

---

As sobrecargas destes métodos fornecem opções adicionais para gerir a forma como os contentores são devolvidos pela operação de cotação. Estas opções são descritas nas seguintes secções.

### <a name="manage-how-many-results-are-returned"></a>Gerir quantos resultados são devolvidos

Por predefinição, uma operação de listagem retorna até 5000 resultados de cada vez. Para devolver um conjunto menor de resultados, forneça um valor não zero para o tamanho da página de resultados para devolver.

Se a sua conta de armazenamento contiver mais de 5000 contentores, ou se tiver especificado o tamanho de uma página de tal forma que a operação de listagem devolva um subconjunto de contentores na conta de armazenamento, então o Azure Storage devolve um *sinal de continuação* com a lista de contentores. Um token de continuação é um valor opaco que pode usar para recuperar o próximo conjunto de resultados do Azure Storage.

No seu código, verifique o valor do token de continuação para determinar se está vazio (para .NET v12) ou nulo (para .NET v11 e anterior). Quando o token de continuação é nulo, então o conjunto de resultados está completo. Se o token de continuação não for nulo, então chame o método de listagem novamente, passando na ficha de continuação para recuperar o próximo conjunto de resultados, até que o token de continuação seja nulo.

### <a name="filter-results-with-a-prefix"></a>Filtrar os resultados com um prefixo

Para filtrar a lista de recipientes, especifique uma corda para o `prefix` parâmetro. A cadeia de prefixo pode incluir um ou mais caracteres. A Azure Storage devolve então apenas os recipientes cujos nomes começam com esse prefixo.

### <a name="return-metadata"></a>Metadados de devolução

Para devolver metadados de contentores com os resultados, especifique o valor **dos metadados** para o [BlobContainerTraits](/dotnet/api/azure.storage.blobs.models.blobcontainertraits) enum (para .NET v12) ou [ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) enum (para .NET v11 e anteriormente). O Azure Storage inclui metadados com cada recipiente devolvido, pelo que não é necessário também recolher os metadados do contentor.

## <a name="example-list-containers"></a>Exemplo: Lista de contentores

O exemplo seguinte assíncronamente lista os recipientes numa conta de armazenamento que começa com um prefixo especificado. O exemplo lista os recipientes que começam com o prefixo especificado e devolve o número especificado de resultados por chamada à operação de cotação. Em seguida, usa o token de continuação para obter o próximo segmento de resultados. O exemplo também devolve metadados de contentores com os resultados.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="Snippet_ListContainers":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix,
                                                        int? segmentSize)
{
    Console.WriteLine("List containers beginning with prefix {0}, plus container metadata:", prefix);

    BlobContinuationToken continuationToken = null;
    ContainerResultSegment resultSegment;

    try
    {
        do
        {
            // List containers beginning with the specified prefix,
            // returning segments of 5 results each.
            // Passing in null for the maxResults parameter returns the maximum number of results (up to 5000).
            // Requesting the container's metadata as part of the listing operation populates the metadata,
            // so it's not necessary to call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, segmentSize, continuationToken, null, null);

            // Enumerate the containers returned.
            foreach (var container in resultSegment.Results)
            {
                Console.WriteLine("\tContainer:" + container.Name);

                // Write the container's metadata keys and values.
                foreach (var metadataItem in container.Metadata)
                {
                    Console.WriteLine("\t\tMetadata key: " + metadataItem.Key);
                    Console.WriteLine("\t\tMetadata value: " + metadataItem.Value);
                }
            }

            // Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);

        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Ver também

- [Recipientes de lista](/rest/api/storageservices/list-containers2)
- [Enumerando recursos blob](/rest/api/storageservices/enumerating-blob-resources)
