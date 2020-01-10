---
title: Listar BLOBs com .NET-armazenamento do Azure
description: Saiba como listar BLOBs em um contêiner em sua conta de armazenamento do Azure usando a biblioteca de cliente .NET. Os exemplos de código mostram como listar BLOBs em uma listagem simples ou como listar BLOBs hierarquicamente, como se eles fossem organizados em diretórios ou pastas.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 3deb3bd0ff66bb31691c4c380595771e3bf2c681
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75691060"
---
# <a name="list-blobs-with-net"></a>Listar BLOBs com .NET

Ao listar BLOBs do seu código, você pode especificar várias opções para gerenciar como os resultados são retornados do armazenamento do Azure. Você pode especificar o número de resultados a serem retornados em cada conjunto de resultados e, em seguida, recuperar os conjuntos subsequentes. Você pode especificar um prefixo para retornar os BLOBs cujos nomes começam com esse caractere ou cadeia de caracteres. E você pode listar os BLOBs em uma estrutura de listagem simples ou hierarquicamente. Uma listagem hierárquica retorna BLOBs como se elas fossem organizadas em pastas. 

Este artigo mostra como listar BLOBs usando a [biblioteca de cliente de armazenamento do Azure para .net](/dotnet/api/overview/azure/storage/client).  

## <a name="understand-blob-listing-options"></a>Entender as opções de listagem de BLOB

Para listar os BLOBs em uma conta de armazenamento, chame um destes métodos:

- [CloudBlobClient. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient. ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

Para listar os BLOBs em um contêiner, chame um destes métodos:

- [CloudBlobContainer. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer. ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

As sobrecargas para esses métodos fornecem opções adicionais para o gerenciamento de como os BLOBs são retornados pela operação de listagem. Essas opções são descritas nas seções a seguir.

### <a name="manage-how-many-results-are-returned"></a>Gerenciar quantos resultados são retornados

Por padrão, uma operação de listagem retorna até 5000 resultados por vez. Para retornar um conjunto menor de resultados, forneça um valor diferente de zero para o parâmetro `maxresults` ao chamar um dos métodos **ListBlobs** .

Se uma operação de listagem retornar mais de 5000 BLOBs ou se você tiver especificado um valor para `maxresults` de modo que a operação de listagem retorne um subconjunto de contêineres na conta de armazenamento, o armazenamento do Azure retornará um *token de continuação* com a lista de BLOBs. Um token de continuação é um valor opaco que você pode usar para recuperar o próximo conjunto de resultados do armazenamento do Azure.

Em seu código, verifique o valor do token de continuação para determinar se ele é nulo. Quando o token de continuação é nulo, o conjunto de resultados é concluído. Se o token de continuação não for nulo, chame a operação de listagem novamente, passando o token de continuação para recuperar o próximo conjunto de resultados, até que o token de continuação seja nulo.

### <a name="filter-results-with-a-prefix"></a>Filtrar resultados com um prefixo

Para filtrar a lista de contêineres, especifique uma cadeia de caracteres para o parâmetro `prefix`. A cadeia de caracteres de prefixo pode incluir um ou mais caracteres. Em seguida, o armazenamento do Azure retorna somente os BLOBs cujos nomes começam com esse prefixo.

### <a name="return-metadata"></a>Retornar metadados

Para retornar os metadados de blob com os resultados, especifique o valor de **metadados** para a enumeração [BlobListingDetails](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) . O armazenamento do Azure inclui metadados com cada blob retornado, portanto, você não precisa chamar um dos métodos **fetchattributes** nesse contexto para recuperar os metadados do blob.

### <a name="flat-listing-versus-hierarchical-listing"></a>Listagem simples versus listagem hierárquica

Os BLOBs no armazenamento do Azure são organizados em um paradigma simples, em vez de um paradigma hierárquico (como um sistema de arquivos clássico). No entanto, você pode organizar BLOBs em *diretórios virtuais* para imitar uma estrutura de pastas. Um diretório virtual faz parte do nome do blob e é indicado pelo caractere delimitador.

Para organizar BLOBs em diretórios virtuais, use um caractere delimitador no nome do blob. O caractere delimitador padrão é uma barra (/), mas você pode especificar qualquer caractere como o delimitador.

Se você nomear seus BLOBs usando um delimitador, poderá optar por listar os BLOBs hierarquicamente. Para uma operação de listagem hierárquica, o armazenamento do Azure retorna qualquer diretório virtual e blobs abaixo do objeto pai. Você pode chamar a operação de listagem recursivamente para atravessar a hierarquia, semelhante a como você percorreria um sistema de arquivos clássico programaticamente.

## <a name="use-a-flat-listing"></a>Usar uma listagem simples

Por padrão, uma operação de listagem retorna BLOBs em uma listagem simples. Em uma listagem simples, os BLOBs não são organizados por diretório virtual.

O exemplo a seguir lista os BLOBs no contêiner especificado usando uma listagem simples, com um tamanho de segmento opcional especificado, e grava o nome do blob em uma janela de console.

```csharp
private static async Task ListBlobsFlatListingAsync(CloudBlobContainer container, int? segmentSize)
{
    BlobContinuationToken continuationToken = null;
    CloudBlob blob;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned
        // and execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(string.Empty,
                true, BlobListingDetails.Metadata, segmentSize, continuationToken, null, null);

            foreach (var blobItem in resultSegment.Results)
            {
                // A flat listing operation returns only blobs, not virtual directories.
                blob = (CloudBlob)blobItem;

                // Write out some blob properties.
                Console.WriteLine("Blob name: {0}", blob.Name);
            }

            Console.WriteLine();

           // Get the continuation token and loop until it is null.
           continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

A saída de exemplo é semelhante a:

```
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

## <a name="use-a-hierarchical-listing"></a>Usar uma listagem hierárquica

Quando você chama uma operação de listagem hierarquicamente, o armazenamento do Azure retorna os diretórios virtuais e os BLOBs no primeiro nível da hierarquia. A propriedade [prefix](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) de cada diretório virtual é definida para que você possa passar o prefixo em uma chamada recursiva para recuperar o próximo diretório.

Para listar os BLOBs hierarquicamente, defina o parâmetro `useFlatBlobListing` do método de listagem como **false**.

O exemplo a seguir lista os BLOBs no contêiner especificado usando uma listagem simples, com um tamanho de segmento opcional especificado, e grava o nome do blob na janela do console.

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, continuationToken, null, null);
            foreach (var blobItem in resultSegment.Results)
            {
                // A hierarchical listing may return both virtual directories and blobs.
                if (blobItem is CloudBlobDirectory)
                {
                    dir = (CloudBlobDirectory)blobItem;

                    // Write out the prefix of the virtual directory.
                    Console.WriteLine("Virtual directory prefix: {0}", dir.Prefix);

                    // Call recursively with the prefix to traverse the virtual directory.
                    await ListBlobsHierarchicalListingAsync(container, dir.Prefix);
                }
                else
                {
                    // Write out the name of the blob.
                    blob = (CloudBlob)blobItem;

                    Console.WriteLine("Blob name: {0}", blob.Name);
                }
                Console.WriteLine();
            }

            // Get the continuation token and loop until it is null.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

A saída de exemplo é semelhante a:

```
Virtual directory prefix: FolderA/
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt

Virtual directory prefix: FolderA/FolderB/
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt

Virtual directory prefix: FolderA/FolderB/FolderC/
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

> [!NOTE]
> Instantâneos de BLOB não podem ser listados em uma operação de listagem hierárquica.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Passos seguintes

- [Listar BLOBs](/rest/api/storageservices/list-blobs)
- [Enumerando recursos de BLOB](/rest/api/storageservices/enumerating-blob-resources)
