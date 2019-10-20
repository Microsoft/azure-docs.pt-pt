---
title: Listar contêineres de blob com .NET-armazenamento do Azure
description: Saiba como listar contêineres de BLOB em sua conta de armazenamento do Azure usando a biblioteca de cliente .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: b7e5b0725049fa5de95f435c848502c36a3a1726
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598130"
---
# <a name="list-blob-containers-with-net"></a>Listar contêineres de blob com .NET

Ao listar os contêineres em uma conta de armazenamento do Azure do seu código, você pode especificar várias opções para gerenciar como os resultados são retornados do armazenamento do Azure. Este artigo mostra como listar contêineres usando a [biblioteca de cliente de armazenamento do Azure para .net](/dotnet/api/overview/azure/storage/client).  

## <a name="understand-container-listing-options"></a>Entender as opções de listagem de contêiner

Para listar os contêineres em sua conta de armazenamento, chame um dos seguintes métodos:

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

As sobrecargas para esses métodos fornecem opções adicionais para o gerenciamento de como os contêineres são retornados pela operação de listagem. Essas opções são descritas nas seções a seguir.

### <a name="manage-how-many-results-are-returned"></a>Gerenciar quantos resultados são retornados

Por padrão, uma operação de listagem retorna até 5000 resultados por vez. Para retornar um conjunto menor de resultados, forneça um valor diferente de zero para o parâmetro `maxresults` ao chamar um dos métodos **ListContainerSegmented** .

Se sua conta de armazenamento contiver mais de 5000 contêineres ou se você tiver especificado um valor para `maxresults` de modo que a operação de listagem retorne um subconjunto de contêineres na conta de armazenamento, o armazenamento do Azure retornará um *token de continuação* com a lista de recipientes. Um token de continuação é um valor opaco que você pode usar para recuperar o próximo conjunto de resultados do armazenamento do Azure.

Em seu código, verifique o valor do token de continuação para determinar se ele é nulo. Quando o token de continuação é nulo, o conjunto de resultados é concluído. Se o token de continuação não for nulo, chame **ListContainersSegmented** ou **ListContainersSegmentedAsync** novamente, passando o token de continuação para recuperar o próximo conjunto de resultados, até que o token de continuação seja nulo.

### <a name="filter-results-with-a-prefix"></a>Filtrar resultados com um prefixo

Para filtrar a lista de contêineres, especifique uma cadeia de caracteres para o parâmetro `prefix`. A cadeia de caracteres de prefixo pode incluir um ou mais caracteres. Em seguida, o armazenamento do Azure retorna somente os contêineres cujos nomes começam com esse prefixo.

### <a name="return-metadata"></a>Retornar metadados

Para retornar metadados de contêiner com os resultados, especifique o valor de **metadados** para a enumeração [ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) . O armazenamento do Azure inclui metadados com cada contêiner retornado, portanto, você também não precisa chamar um dos métodos **fetchattributes** para recuperar os metadados do contêiner.

## <a name="example-list-containers"></a>Exemplo: listar contêineres

O exemplo a seguir lista de forma assíncrona os contêineres em uma conta de armazenamento que começam com um prefixo especificado. O exemplo lista os contêineres em incrementos de 5 resultados por vez e usa o token de continuação para obter o próximo segmento de resultados. O exemplo também retorna metadados de contêiner com os resultados.

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
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

## <a name="see-also"></a>Ver também

[Listar contêineres](/rest/api/storageservices/list-containers2) 
[enumerando recursos de blob](/rest/api/storageservices/enumerating-blob-resources)
