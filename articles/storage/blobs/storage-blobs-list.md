---
title: Lista de bolhas com .NET - Azure Storage
description: Saiba como listar as bolhas num recipiente na sua conta de Armazenamento Azure utilizando a biblioteca de clientes .NET. Exemplos de código mostram como listar bolhas numa listagem plana, ou como listar blobs hierárquicos, como se estivessem organizados em diretórios ou pastas.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/16/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: ddd19c90c8c47016497e2c3b00e04595a94e7715
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95543073"
---
# <a name="list-blobs-with-net"></a>Lista de bolhas com .NET

Ao listar as bolhas do seu código, pode especificar uma série de opções para gerir a forma como os resultados são devolvidos do Azure Storage. Pode especificar o número de resultados para retornar em cada conjunto de resultados e, em seguida, recuperar os conjuntos subsequentes. Pode especificar um prefixo para devolver bolhas cujos nomes começam com esse personagem ou corda. E pode listar bolhas numa estrutura plana de listagem, ou hierarquicamente. Uma listagem hierárquica retorna as bolhas como se estivessem organizadas em pastas.

Este artigo mostra como listar bolhas usando a biblioteca de [clientes Azure Storage para .NET](/dotnet/api/overview/azure/storage).  

## <a name="understand-blob-listing-options"></a>Compreenda as opções de listagem de blob

Para listar as bolhas numa conta de armazenamento, ligue para um destes métodos:

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

- [BlobContainerClient.GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs)
- [BlobContainerClient.GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync)
- [BlobContainerClient.GetBlobsByHierarchy](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchy)
- [BlobContainerClient.GetBlobsByHierarchyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchyasync)

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

- [CloudBlobClient.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

Para listar as bolhas num recipiente, chame um destes métodos:

- [CloudBlobContainer.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

As sobrecargas para estes métodos fornecem opções adicionais para gerir a forma como as bolhas são devolvidas pela operação de listagem. Estas opções são descritas nas seguintes secções.

---

### <a name="manage-how-many-results-are-returned"></a>Gerir quantos resultados são devolvidos

Por predefinição, uma operação de listagem retorna até 5000 resultados de cada vez, mas pode especificar o número de resultados que deseja que cada operação de listagem devolva. Os exemplos apresentados neste artigo mostram-lhe como devolver os resultados em páginas.

### <a name="filter-results-with-a-prefix"></a>Filtrar os resultados com um prefixo

Para filtrar a lista de bolhas, especifique uma corda para o `prefix` parâmetro. A cadeia de prefixo pode incluir um ou mais caracteres. A Azure Storage devolve então apenas as bolhas cujos nomes começam com o prefixo.

### <a name="return-metadata"></a>Metadados de devolução

Pode devolver metadados blob com os resultados.

- Se estiver a utilizar o .NET v12 SDK, especifique o valor **dos metadados** para a enumeração [blobTraits.](/dotnet/api/azure.storage.blobs.models.blobtraits)

- Se estiver a utilizar o .NET v11 SDK, especifique o valor **dos metadados** para a enumeração [BlobListingDetails.](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) O Azure Storage inclui metadados com cada bolha devolvida, pelo que não precisa de ligar para um dos **métodos FetchAttributes** neste contexto para recuperar os metadados blob.

### <a name="list-blob-versions-or-snapshots"></a>Listar versões blob ou instantâneos

Para listar versões blob ou instantâneos com a biblioteca de clientes .NET v12, especifique o parâmetro [BlobStates](/dotnet/api/azure.storage.blobs.models.blobstates) com o campo **Versão** ou **Snapshot.** Versões e instantâneos estão listados do mais antigo ao mais recente. Para obter mais informações sobre as versões de listagem, consulte [as versões Blob list](versioning-enable.md#list-blob-versions).

### <a name="flat-listing-versus-hierarchical-listing"></a>Listagem plana versus listagem hierárquica

As bolhas no Azure Storage são organizadas num paradigma plano, em vez de um paradigma hierárquico (como um sistema de ficheiros clássico). No entanto, pode organizar blobs em *diretórios virtuais* para imitar uma estrutura de pasta. Um diretório virtual faz parte do nome da bolha e é indicado pelo personagem delimiter.

Para organizar bolhas em diretórios virtuais, use um personagem delimiter no nome blob. O carácter delimiter predefinido é um corte para a frente (/), mas pode especificar qualquer personagem como o delimiter.

Se nomear as suas bolhas usando um delimiter, então pode optar por listar as bolhas hierárquicamente. Para uma operação de listagem hierárquica, o Azure Storage devolve quaisquer diretórios e bolhas virtuais por baixo do objeto principal. Pode ligar para a operação de listagem de forma recorrente para atravessar a hierarquia, semelhante à forma como atravessaria um sistema de ficheiros clássico programáticamente.

## <a name="use-a-flat-listing"></a>Use uma listagem plana

Por predefinição, uma operação de listagem devolve bolhas numa listagem plana. Numa listagem plana, as bolhas não são organizadas por diretório virtual.

O exemplo a seguir lista as bolhas no recipiente especificado utilizando uma listagem plana, com um tamanho de segmento opcional especificado, e escreve o nome blob para uma janela da consola.

Se ativou a funcionalidade de espaço hierárquico na sua conta, os diretórios não são virtuais. Em vez disso, são objetos concretos e independentes. Portanto, os diretórios aparecem na lista como bolhas de comprimento zero.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobsFlatListing":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Se uma operação de listagem devolver mais de 5000 blobs, ou se o número de bolhas disponíveis exceder o número especificado, então o Azure Storage devolve um sinal de *continuação* com a lista de bolhas. Um token de continuação é um valor opaco que pode usar para recuperar o próximo conjunto de resultados do Azure Storage.

No seu código, verifique o valor da ficha de continuação para determinar se é nulo. Quando o token de continuação é nulo, então o conjunto de resultados está completo. Se o token de continuação não for nulo, então ligue novamente para a operação de listagem, passando na ficha de continuação para recuperar o próximo conjunto de resultados, até que o token de continuação seja nulo.

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

---

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

Quando se chama uma operação de listagem hierárquica, o Azure Storage devolve os diretórios virtuais e as bolhas ao primeiro nível da hierarquia. A propriedade [prefixo](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) de cada diretório virtual é definida para que você possa passar o prefixo em uma chamada recursiva para recuperar o próximo diretório.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Para listar os blobs hierárquicos, ligue para o [método BlobContainerClient.GetBlobsByHierarchy,](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchy)ou o método [BlobContainerClient.GetBlobsByHierarchyAsync.](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchyasync)

O exemplo a seguir lista as bolhas no recipiente especificado utilizando uma listagem hierárquica, com um tamanho de segmento opcional especificado, e escreve o nome blob na janela da consola.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobsHierarchicalListing":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Para listar as bolhas hierárquicamente, descreva `useFlatBlobListing` o parâmetro do método de listagem como **falso**.

O exemplo a seguir lista as bolhas no recipiente especificado utilizando uma listagem plana, com um tamanho de segmento opcional especificado, e escreve o nome blob na janela da consola.

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

---

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
> As imagens blob não podem ser listadas numa operação de listagem hierárquica.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Passos seguintes

- [Blobs de lista](/rest/api/storageservices/list-blobs)
- [Enumerando recursos blob](/rest/api/storageservices/enumerating-blob-resources)