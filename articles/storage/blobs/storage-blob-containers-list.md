---
title: Lista de recipientes blob com .NET - Armazenamento Azure
description: Saiba como listar os recipientes blob na sua conta de Armazenamento Azure utilizando a biblioteca de clientes .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 155b8f5d50c7b106daff8dab4df17200b844c988
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79135909"
---
# <a name="list-blob-containers-with-net"></a>Listar recipientes blob com .NET

Quando lista os contentores numa conta de Armazenamento Azure a partir do seu código, pode especificar várias opções para gerir a forma como os resultados são devolvidos do Armazenamento Azure. Este artigo mostra como listar contentores utilizando a [biblioteca de clientes azure storage para .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).  

## <a name="understand-container-listing-options"></a>Compreender as opções de listagem de contentores

Para listar os contentores na sua conta de armazenamento, ligue para um dos seguintes métodos:

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

As sobrecargas para estes métodos fornecem opções adicionais para gerir a forma como os contentores são devolvidos pela operação de listagem. Estas opções são descritas nas seguintes secções.

### <a name="manage-how-many-results-are-returned"></a>Gerir quantos resultados são devolvidos

Por predefinição, uma operação de listagem retorna até 5000 resultados de cada vez. Para devolver um conjunto menor de resultados, forneça um valor não zero para o parâmetro `maxresults` ao ligar para um dos métodos **Segmentados listContainer.**

Se a sua conta de armazenamento contiver mais de 5000 contentores, ou se tiver especificado um valor para `maxresults` de modo a que a operação de cotação deredere um subconjunto de contentores na conta de armazenamento, então o Armazenamento Azure devolve um símbolo de *continuação* com a lista de contentores. Um token de continuação é um valor opaco que pode usar para recuperar o próximo conjunto de resultados do Armazenamento Azure.

No seu código, verifique o valor do símbolo de continuação para determinar se é nulo. Quando o sinal de continuação é nulo, então o conjunto de resultados está completo. Se o token de continuação não for nulo, ligue novamente para **listContainersSegmented** ou **ListContainersSegmentedAsync,** passando no token de continuação para recuperar o próximo conjunto de resultados, até que o token de continuação seja nulo.

### <a name="filter-results-with-a-prefix"></a>Resultados do filtro com prefixo

Para filtrar a lista de recipientes, especifique uma corda para o parâmetro `prefix`. A cadeia de prefixo pode incluir um ou mais caracteres. O Azure Storage devolve apenas os contentores cujos nomes começam com esse prefixo.

### <a name="return-metadata"></a>Devolver metadados

Para devolver metadados do recipiente com os resultados, especifique o valor **metadados** para a enumeração [De Listagem de Contentores.](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) O Azure Storage inclui metadados com cada recipiente devolvido, pelo que não precisa de chamar também um dos métodos **FetchApara** recuperar os metadados do recipiente.

## <a name="example-list-containers"></a>Exemplo: Lista de contentores

O exemplo seguinte lista os recipientes numa conta de armazenamento que começa com um prefixo especificado. O exemplo lista os contentores em incrementos de 5 resultados de cada vez, e usa o token de continuação para obter o próximo segmento de resultados. O exemplo também devolve metadados de contentores com os resultados.

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix)
{
    Console.WriteLine("List all containers beginning with prefix {0}, plus container metadata:", prefix);

    try
    {
        ContainerResultSegment resultSegment = null;
        BlobContinuationToken continuationToken = null;

        do
        {
            // List containers beginning with the specified prefix, returning segments of 5 results each.
            // Passing null for the maxResults parameter returns the max number of results (up to 5000).
            // Requesting the container's metadata with the listing operation populates the metadata,
            // so it's not necessary to also call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, 5, continuationToken, null, null);

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

            // Get the continuation token. If not null, get the next segment.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0} : {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Consulte também

[Lista de contentores](/rest/api/storageservices/list-containers2)
[enumerando recursos blob](/rest/api/storageservices/enumerating-blob-resources)
