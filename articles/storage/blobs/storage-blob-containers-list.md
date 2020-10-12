---
title: Listar recipientes blob com .NET - Azure Storage
description: Saiba como listar os recipientes blob na sua conta de Armazenamento Azure utilizando a biblioteca de clientes .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: f443cd5603e6ca0f60dc0e69b734bfa46138d476
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89018948"
---
# <a name="list-blob-containers-with-net"></a>Listar recipientes blob com .NET

Quando lista os contentores numa conta de Armazenamento Azure a partir do seu código, pode especificar uma série de opções para gerir como os resultados são devolvidos do Azure Storage. Este artigo mostra como listar os recipientes utilizando a biblioteca do [cliente do Azure Storage para .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).  

## <a name="understand-container-listing-options"></a>Compreender opções de listagem de contentores

Para listar os recipientes na sua conta de armazenamento, ligue para um dos seguintes métodos:

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

As sobrecargas destes métodos fornecem opções adicionais para gerir a forma como os contentores são devolvidos pela operação de cotação. Estas opções são descritas nas seguintes secções.

### <a name="manage-how-many-results-are-returned"></a>Gerir quantos resultados são devolvidos

Por predefinição, uma operação de listagem retorna até 5000 resultados de cada vez. Para devolver um conjunto menor de resultados, forneça um valor não zero para o `maxresults` parâmetro ao chamar um dos métodos **ListContainerSegmented.**

Se a sua conta de armazenamento contiver mais de 5000 contentores, ou se tiver especificado um valor para `maxresults` tal que a operação de cotação devolva um subconjunto de contentores na conta de armazenamento, então o Azure Storage devolve um *sinal de continuação* com a lista de contentores. Um token de continuação é um valor opaco que pode usar para recuperar o próximo conjunto de resultados do Azure Storage.

No seu código, verifique o valor da ficha de continuação para determinar se é nulo. Quando o token de continuação é nulo, então o conjunto de resultados está completo. Se o token de continuação não for nulo, então ligue para **ListContainersSegmented** ou **ListContainersSegmentedAsync** novamente, passando na ficha de continuação para recuperar o próximo conjunto de resultados, até que o token de continuação seja nulo.

### <a name="filter-results-with-a-prefix"></a>Filtrar os resultados com um prefixo

Para filtrar a lista de recipientes, especifique uma corda para o `prefix` parâmetro. A cadeia de prefixo pode incluir um ou mais caracteres. A Azure Storage devolve então apenas os recipientes cujos nomes começam com esse prefixo.

### <a name="return-metadata"></a>Metadados de devolução

Para devolver metadados de contentores com os resultados, especifique o valor **dos metadados** para a enumeração [ContainerListingDetails.](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) O Azure Storage inclui metadados com cada recipiente devolvido, pelo que não é necessário também ligar para um dos **métodos FetchAttributes** para recuperar os metadados do contentor.

## <a name="example-list-containers"></a>Exemplo: Lista de contentores

O exemplo seguinte assíncronamente lista os recipientes numa conta de armazenamento que começa com um prefixo especificado. O exemplo lista os contentores em incrementos de 5 resultados de cada vez, e usa o token de continuação para obter o próximo segmento de resultados. O exemplo também devolve metadados de contentores com os resultados.

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

[Recipientes de lista](/rest/api/storageservices/list-containers2) 
 [Enumerando recursos blob](/rest/api/storageservices/enumerating-blob-resources)
