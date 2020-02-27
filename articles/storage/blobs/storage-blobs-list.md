---
title: Lista de blobs com .NET - Armazenamento Azure
description: Aprenda a listar bolhas num contentor na sua conta de Armazenamento Azure utilizando a biblioteca de clientes .NET. Exemplos de código mostram como listar bolhas numa listagem plana, ou como listar as bolhas hierárquicas hierárquicas, como se fossem organizadas em diretórios ou pastas.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: b51016b1b73b51cd1f1ec827f05fc9a93dd4cb38
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650712"
---
# <a name="list-blobs-with-net"></a>Lista de bolhas com .NET

Quando lista bolhas do seu código, pode especificar várias opções para gerir a forma como os resultados são devolvidos do Armazenamento Azure. Pode especificar o número de resultados a devolver em cada conjunto de resultados e, em seguida, recuperar os conjuntos seguintes. Pode especificar um prefixo para devolver bolhas cujos nomes começam com esse carácter ou corda. E pode listar bolhas numa estrutura plana de listagem, ou hierárquica. Uma listagem hierárquica devolve bolhas como se estivessem organizadas em pastas. 

Este artigo mostra como listar bolhas usando a [biblioteca de clientes Azure Storage para .NET](/dotnet/api/overview/azure/storage/client).  

## <a name="understand-blob-listing-options"></a>Compreender as opções de listagem de blob

Para listar as bolhas numa conta de armazenamento, ligue para um destes métodos:

- [CloudBlobClient.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

Para listar as bolhas num recipiente, ligue para um destes métodos:

- [CloudBlobContainer.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

As sobrecargas para estes métodos fornecem opções adicionais para gerir a forma como as bolhas são devolvidas pela operação de listagem. Estas opções são descritas nas seguintes secções.

### <a name="manage-how-many-results-are-returned"></a>Gerir quantos resultados são devolvidos

Por predefinição, uma operação de listagem retorna até 5000 resultados de cada vez. Para devolver um conjunto menor de resultados, forneça um valor não zero para o parâmetro `maxresults` ao ligar para um dos **métodos ListBlobs.**

Se uma operação de listagem devolver mais de 5000 blobs, ou se tiver especificado um valor para `maxresults` de modo a que a operação de cotação derede um subconjunto de contentores na conta de armazenamento, então o Armazenamento Azure devolve um sinal de *continuação* com a lista de bolhas. Um token de continuação é um valor opaco que pode usar para recuperar o próximo conjunto de resultados do Armazenamento Azure.

No seu código, verifique o valor do símbolo de continuação para determinar se é nulo. Quando o sinal de continuação é nulo, então o conjunto de resultados está completo. Se o sinal de continuação não for nulo, volte a ligar para a operação de listagem, passando no sinal de continuação para recuperar o próximo conjunto de resultados, até que o token de continuação seja nulo.

### <a name="filter-results-with-a-prefix"></a>Resultados do filtro com prefixo

Para filtrar a lista de recipientes, especifique uma corda para o parâmetro `prefix`. A cadeia de prefixo pode incluir um ou mais caracteres. O Azure Storage devolve apenas as bolhas cujos nomes começam com esse prefixo.

### <a name="return-metadata"></a>Devolver metadados

Para devolver metadados blob com os resultados, especifique o valor **metadados** para a enumeração [BlobListingDetails.](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) O Azure Storage inclui metadados com cada bolha devolvida, pelo que não precisa de chamar um dos métodos **FetchAS** neste contexto para recuperar os metadados blob.

### <a name="flat-listing-versus-hierarchical-listing"></a>Listagem plana versus listagem hierárquica

As bolhas no Armazenamento Azure são organizadas num paradigma plano, em vez de um paradigma hierárquico (como um sistema de ficheiros clássico). No entanto, pode organizar blobs em *diretórios virtuais* de forma a imitar uma estrutura de pastas. Um diretório virtual faz parte do nome da bolha e é indicado pelo carácter delimitador.

Para organizar blobs em diretórios virtuais, use um personagem delimitador no nome blob. O carácter delimitador predefinido é um corte para a frente (/), mas pode especificar qualquer personagem como delimitador.

Se nomear as suas bolhas usando um delimitador, então pode optar por listar as bolhas hierárquicas. Para uma operação hierárquica de listagem, o Azure Storage devolve quaisquer diretórios virtuais e bolhas por baixo do objeto-mãe. Pode chamar a operação de listagem de forma recursiva para atravessar a hierarquia, semelhante à forma como atravessaria um sistema de ficheiros clássico programáticamente.

## <a name="use-a-flat-listing"></a>Use uma listagem plana

Por predefinição, uma operação de listagem devolve bolhas numa listagem plana. Numa listagem plana, as bolhas não são organizadas por diretório virtual.

O exemplo seguinte lista as bolhas no recipiente especificado utilizando uma listagem plana, com um tamanho de segmento opcional especificado, e escreve o nome blob para uma janela de consola.

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

A saída da amostra é semelhante a:

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

## <a name="use-a-hierarchical-listing"></a>Use uma listagem hierárquica

Quando você chama uma operação de listagem hierárquica, o Armazenamento Azure devolve os diretórios virtuais e as bolhas no primeiro nível da hierarquia. A propriedade [Prefix](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) de cada diretório virtual está definida para que você possa passar o prefixo em uma chamada recursiva para recuperar o próximo diretório.

Para enumerar as bolhas hierárquicas, defina o parâmetro `useFlatBlobListing` do método de listagem para **falso**.

O exemplo seguinte lista as bolhas no recipiente especificado utilizando uma listagem plana, com um tamanho de segmento opcional especificado, e escreve o nome blob para a janela da consola.

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken = null;

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

A saída da amostra é semelhante a:

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
> Os instantâneos blob não podem ser listados numa operação hierárquica de listagem.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Passos seguintes

- [Lista blobs](/rest/api/storageservices/list-blobs)
- [Enumerando os recursos blob](/rest/api/storageservices/enumerating-blob-resources)
