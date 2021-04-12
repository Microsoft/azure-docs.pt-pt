---
title: Gerir propriedades e metadados para uma bolha com .NET - Azure Storage
description: Saiba como definir e recuperar as propriedades do sistema e armazenar metadados personalizados em bolhas na sua conta de Armazenamento Azure utilizando a biblioteca de clientes .NET.
services: storage
author: twooley
ms.author: twooley
ms.date: 09/25/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 24ec646c2928570c67a7f71481f2ca0191f1c8b9
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280212"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>Gerir propriedades blob e metadados com .NET

Além dos dados que contêm, as bolhas suportam as propriedades do sistema e os metadados definidos pelo utilizador. Este artigo mostra como gerir as propriedades do sistema e metadados definidos pelo utilizador com a biblioteca de [clientes de Armazenamento Azure para .NET](/dotnet/api/overview/azure/storage).

## <a name="about-properties-and-metadata"></a>Sobre propriedades e metadados

- **Propriedades do sistema**: As propriedades do sistema existem em cada recurso de armazenamento Blob. Alguns podem ser lidos ou definidos, enquanto outros são apenas de leitura. Sob as capas, algumas propriedades do sistema correspondem a certos cabeçalhos HTTP padrão. A biblioteca cliente Azure Storage para .NET mantém estas propriedades para si.

- **Metadados definidos pelo utilizador**: Os metadados definidos pelo utilizador consistem num ou mais pares de valor-nome que especifica para um recurso de armazenamento Blob. Pode utilizar metadados para armazenar valores adicionais com o recurso. Os valores dos metadados são apenas para os seus próprios fins, e não afetam a forma como o recurso se comporta.

> [!NOTE]
> As tags de índice blob também fornecem a capacidade de armazenar atributos de chave/valor definidos pelo utilizador arbitrários ao lado de um recurso de armazenamento Azure Blob. Embora semelhantes aos metadados, apenas as tags de índice blob são automaticamente indexadas e tornadas pescáveis pelo serviço de blob nativo. Os metadados não podem ser indexados e consultados a menos que utilize um serviço separado, como a Azure Search.
>
> Para saber mais sobre esta funcionalidade, consulte [Gerir e encontrar dados sobre o armazenamento da Azure Blob com índice blob (pré-visualização)](storage-manage-find-blobs.md).

## <a name="set-and-retrieve-properties"></a>Definir e recuperar propriedades

O seguinte exemplo de código define as `ContentType` propriedades e as propriedades do sistema numa `ContentLanguage` bolha.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Para definir propriedades numa bolha, ligue [para SetHttpHeaders](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.sethttpheaders) ou [SetHttpHeadersAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.sethttpheadersasync). Quaisquer propriedades não explicitamente definidas são limpas. O seguinte exemplo de código primeiro obtém as propriedades existentes na bolha, em seguida, usa-as para povoar os cabeçalhos que não estão sendo atualizados.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_SetBlobProperties":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
public static async Task SetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        Console.WriteLine("Setting blob properties.");

        // You must explicitly set the MIME ContentType every time
        // the properties are updated or the field will be cleared.
        blob.Properties.ContentType = "text/plain";
        blob.Properties.ContentLanguage = "en-us";

        // Set the blob's properties.
        await blob.SetPropertiesAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```
---

O seguinte exemplo de código obtém as propriedades do sistema de uma bolha e exibe alguns dos valores.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadBlobProperties":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Recuperar metadados e valores de propriedade para um recurso de armazenamento Blob é um processo em duas etapas. Antes de poder ler estes valores, deve ir buscá-los explicitamente, chamando o `FetchAttributes` ou `FetchAttributesAsync` o método. A exceção a esta regra é que os `Exists` métodos e `ExistsAsync` métodos chamam o método adequado `FetchAttributes` sob as capas. Quando se chama um destes métodos, não precisa de ligar `FetchAttributes` também.

> [!IMPORTANT]
> Se verificar que os valores de propriedade ou metadados para um recurso de armazenamento não foram povoados, verifique se o seu código chama o `FetchAttributes` ou `FetchAttributesAsync` o método.

Para recuperar propriedades blob, ligue para o `FetchAttributes` ou método na sua bolha para `FetchAttributesAsync` povoar a `Properties` propriedade.

```csharp
private static async Task GetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        // Fetch the blob properties.
        await blob.FetchAttributesAsync();

        // Display some of the blob's property values.
        Console.WriteLine(" ContentLanguage: {0}", blob.Properties.ContentLanguage);
        Console.WriteLine(" ContentType: {0}", blob.Properties.ContentType);
        Console.WriteLine(" Created: {0}", blob.Properties.Created);
        Console.WriteLine(" LastModified: {0}", blob.Properties.LastModified);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```
---

## <a name="set-and-retrieve-metadata"></a>Definir e recuperar metadados

Pode especificar os metadados como um ou mais pares de valor-nome num recurso de bolha ou contentor. Para definir metadados, adicione pares de valor-nome à `Metadata` coleção no recurso. Em seguida, ligue para um dos seguintes métodos para escrever os valores:

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

- [SetMetadata](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.setmetadata)
- [SetMetadataAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.setmetadataasync)

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)
---

Os pares de nome/valor dos metadados são cabeçalhos HTTP válidos e devem aderir a todas as restrições que regem os cabeçalhos HTTP. Os nomes dos metadados devem ser válidos Nomes de cabeçalhos HTTP e identificadores C# válidos, podem conter apenas caracteres ASCII e devem ser tratados como insensíveis a casos. [Base64-codificar](/dotnet/api/system.convert.tobase64string) ou [codificar](/dotnet/api/system.web.httputility.urlencode) valores de metadados de URL que contenham caracteres não ASCII.

O nome dos seus metadados deve estar em conformidade com as convenções de nomeação dos identificadores C#. Os nomes dos metadados mantêm o caso utilizado quando foram criados, mas são insensíveis a casos quando definidos ou lidos. Se dois ou mais cabeçalhos de metadados usando o mesmo nome forem submetidos para um recurso, o armazenamento Azure Blob devolve o código de erro HTTP 400 (Mau Pedido).

O seguinte exemplo de código define metadados numa bolha. Um valor é definido usando o método da `Add` coleção. O outro valor é definido utilizando a sintaxe de chave/valor implícito.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_AddBlobMetadata":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
public static async Task AddBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Add metadata to the blob by calling the Add method.
        blob.Metadata.Add("docType", "textDocuments");

        // Add metadata to the blob by using key/value syntax.
        blob.Metadata["category"] = "guidance";

        // Set the blob's metadata.
        await blob.SetMetadataAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```
---

O seguinte exemplo de código lê os metadados numa bolha.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Para recuperar metadados, ligue para o método [GetProperties](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.getproperties) ou [GetPropertiesAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.getpropertiesasync) no seu blob ou recipiente para povoar a recolha de [metadados](/dotnet/api/azure.storage.blobs.models.blobproperties.metadata) e, em seguida, leia os valores, como mostrado no exemplo abaixo. Os métodos **GetProperties** recuperam propriedades blob e metadados numa única chamada. Isto é diferente dos APIs REST que requerem chamadas separadas para [obter propriedades blob](/rest/api/storageservices/get-blob-properties) e [obter metadados blob](/rest/api/storageservices/get-blob-metadata).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadBlobMetadata":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Para recuperar metadados, ligue para o `FetchAttributes` ou método na sua bolha ou recipiente para preencher a recolha `FetchAttributesAsync` `Metadata` e, em seguida, leia os valores, como mostra o exemplo abaixo.

```csharp
public static async Task ReadBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Fetch blob attributes in order to populate 
        // the blob's properties and metadata.
        await blob.FetchAttributesAsync();

        Console.WriteLine("Blob metadata:");

        // Enumerate the blob's metadata.
        foreach (var metadataItem in blob.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```
---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Ver também

- [Definir operação Blob Properties](/rest/api/storageservices/set-blob-properties)
- [Obtenha a operação Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Definir operação de metadados blob](/rest/api/storageservices/set-blob-metadata)
- [Obtenha a operação de Metadados Blob](/rest/api/storageservices/get-blob-metadata)