---
title: Gerir propriedades e metadados para uma bolha com .NET - Armazenamento Azure
description: Saiba como definir e recuperar propriedades do sistema e armazenar metadados personalizados em blobs na sua conta de Armazenamento Azure utilizando a biblioteca de clientes .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 74c023c06e7b28183a53772be6798419c91dd37a
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692454"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>Gerir propriedades blob e metadados com .NET

Além dos dados que contêm, as propriedades do sistema de suporte blobs e os metadados definidos pelo utilizador. Este artigo mostra como gerir as propriedades do sistema e os metadados definidos pelo utilizador com a [biblioteca de clientes do Azure Storage para .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-properties-and-metadata"></a>Sobre propriedades e metadados

- **Propriedades do sistema**: Existem propriedades do sistema em cada recurso de armazenamento Blob. Alguns podem ser lidos ou definidos, enquanto outros são apenas para leitura. Sob as coberturas, algumas propriedades do sistema correspondem a determinados cabeçalhos HTTP padrão. A biblioteca de clientes Azure Storage para .NET mantém estas propriedades para si.

- **Metadados definidos pelo utilizador**: Os metadados definidos pelo utilizador consistem num ou mais pares de valor de nome que especifica para um recurso de armazenamento Blob. Pode utilizar metadados para armazenar valores adicionais com o recurso. Os valores dos metadados são apenas para os seus próprios fins, e não afetam o comportamento do recurso.

> [!NOTE]
> As tags do Índice Blob também fornecem a capacidade de armazenar atributos de chave/valor definidos pelo utilizador arbitrários ao lado de um recurso de armazenamento Blob. Embora semelhantes aos metadados, apenas as tags do Índice Blob são automaticamente indexadas e tornadas consultadas pelo serviço de blob nativo. Os metadados não podem ser indexados e consultados de forma nativa, a menos que utilize um serviço separado, como o Azure Search.
>
> Para saber mais sobre esta funcionalidade, consulte [Gerir e encontrar dados sobre o Armazenamento de Blob Azure com índice blob (Pré-visualização)](storage-manage-find-blobs.md).

Recuperar metadados e valores de propriedade para um recurso de armazenamento Blob é um processo em duas etapas. Antes de poder ler estes valores, deve `FetchAttributes` rebusca-los explicitamente, chamando o ou o `FetchAttributesAsync` método. A exceção a esta `Exists` `ExistsAsync` regra é `FetchAttributes` que os métodos e métodos chamam o método adequado sob as coberturas. Quando se chama um destes métodos, também `FetchAttributes`não é preciso ligar.

> [!IMPORTANT]
> Se verificar que os valores de propriedade ou metadados para um recurso `FetchAttributes` `FetchAttributesAsync` de armazenamento não foram povoados, verifique se o seu código chama o ou o método.

## <a name="set-and-retrieve-properties"></a>Definir e recuperar propriedades

O exemplo de código `ContentType` `ContentLanguage` que se segue define as propriedades e do sistema numa bolha.

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

Para recuperar propriedades blob, ligue para o `FetchAttributes` ou `FetchAttributesAsync` método na sua bolha para povoar a `Properties` propriedade. O seguinte exemplo de código obtém propriedades do sistema de uma bolha e exibe alguns dos valores:

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

## <a name="set-and-retrieve-metadata"></a>Definir e recuperar metadados

Pode especificar metadados como um ou mais pares de valor de nome num recurso blob ou contentor. Para definir metadados, adicione pares de `Metadata` valor de nome à recolha no recurso. Em seguida, ligue para um dos seguintes métodos para escrever os valores:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

Os pares de nome/valor de metadados são cabeçalhos HTTP válidos e devem aderir a todas as restrições que regem os cabeçalhos HTTP. Os nomes de metadados devem ser nomes de cabeçalho http válidos e identificadores C# válidos, podem conter apenas caracteres ASCII, e devem ser tratados como insensíveis a casos. [Base64-codificar](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) ou [código URL](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode) valores de metadados contendo caracteres não ASCII.

O nome dos seus metadados deve estar em conformidade com as convenções de nomeação para identificadores C#. Os nomes dos metadados mantêm o caso usado quando foram criados, mas são insensíveis aos casos quando definidos ou lidos. Se dois ou mais cabeçalhos de metadados usando o mesmo nome forem submetidos para um recurso, o armazenamento do Azure Blob devolve o código de erro HTTP 400 (Pedido Mau).

O exemplo de código que se segue define metadados numa bolha. Um valor é definido usando `Add` o método da coleção. O outro valor é definido utilizando a sintaxe implícita da chave/valor.

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

Para recuperar metadados, `FetchAttributes` `FetchAttributesAsync` ligue para o ou o `Metadata` método na sua bolha ou contentor para povoar a recolha e, em seguida, leia os valores, como mostra o exemplo abaixo.

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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Consulte também

- [Definir operação Blob Properties](/rest/api/storageservices/set-blob-properties)
- [Obtenha a operação Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Definir operação de metadados blob](/rest/api/storageservices/set-blob-metadata)
- [Obtenha operação de metadados Blob](/rest/api/storageservices/get-blob-metadata)
