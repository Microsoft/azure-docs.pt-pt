---
title: Gerir propriedades e metadados para uma bolha com .NET - Azure Storage
description: Saiba como definir e recuperar as propriedades do sistema e armazenar metadados personalizados em bolhas na sua conta de Armazenamento Azure utilizando a biblioteca de clientes .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 3d86b6e39d6199d2f0268070cfa5456e512daa49
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84465886"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>Gerir propriedades blob e metadados com .NET

Além dos dados que contêm, as bolhas suportam as propriedades do sistema e os metadados definidos pelo utilizador. Este artigo mostra como gerir as propriedades do sistema e metadados definidos pelo utilizador com a biblioteca de [clientes de Armazenamento Azure para .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-properties-and-metadata"></a>Sobre propriedades e metadados

- **Propriedades do sistema**: As propriedades do sistema existem em cada recurso de armazenamento Blob. Alguns podem ser lidos ou definidos, enquanto outros são apenas de leitura. Sob as capas, algumas propriedades do sistema correspondem a certos cabeçalhos HTTP padrão. A biblioteca cliente Azure Storage para .NET mantém estas propriedades para si.

- **Metadados definidos pelo utilizador**: Os metadados definidos pelo utilizador consistem num ou mais pares de valor-nome que especifica para um recurso de armazenamento Blob. Pode utilizar metadados para armazenar valores adicionais com o recurso. Os valores dos metadados são apenas para os seus próprios fins, e não afetam a forma como o recurso se comporta.

> [!NOTE]
> As tags Blob Index também fornecem a capacidade de armazenar atributos de chave/valor definidos pelo utilizador arbitrários ao lado de um recurso de armazenamento Blob. Embora semelhantes aos metadados, apenas as tags Blob Index são automaticamente indexadas e tornadas consultadas pelo serviço de blob nativo. Os metadados não podem ser indexados e consultados de forma nativa, a menos que utilize um serviço separado, como a Azure Search.
>
> Para saber mais sobre esta funcionalidade, consulte [Gerir e encontrar dados sobre o Armazenamento Azure Blob com Índice blob (Pré-visualização)](storage-manage-find-blobs.md).

Recuperar metadados e valores de propriedade para um recurso de armazenamento Blob é um processo em duas etapas. Antes de poder ler estes valores, deve ir buscá-los explicitamente, chamando o `FetchAttributes` ou `FetchAttributesAsync` o método. A exceção a esta regra é que os `Exists` métodos e `ExistsAsync` métodos chamam o método adequado `FetchAttributes` sob as capas. Quando se chama um destes métodos, não precisa de ligar `FetchAttributes` também.

> [!IMPORTANT]
> Se verificar que os valores de propriedade ou metadados para um recurso de armazenamento não foram povoados, verifique se o seu código chama o `FetchAttributes` ou `FetchAttributesAsync` o método.

## <a name="set-and-retrieve-properties"></a>Definir e recuperar propriedades

O seguinte exemplo de código define as `ContentType` propriedades e as propriedades do sistema numa `ContentLanguage` bolha.

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

Para recuperar propriedades blob, ligue para o `FetchAttributes` ou método na sua bolha para `FetchAttributesAsync` povoar a `Properties` propriedade. O seguinte exemplo de código obtém as propriedades do sistema de uma bolha e exibe alguns dos valores:

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

Pode especificar os metadados como um ou mais pares de valor-nome num recurso de bolha ou contentor. Para definir metadados, adicione pares de valor-nome à `Metadata` coleção no recurso. Em seguida, ligue para um dos seguintes métodos para escrever os valores:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

Os pares de nome/valor dos metadados são cabeçalhos HTTP válidos e devem aderir a todas as restrições que regem os cabeçalhos HTTP. Os nomes dos metadados devem ser válidos Nomes de cabeçalhos HTTP e identificadores C# válidos, podem conter apenas caracteres ASCII e devem ser tratados como insensíveis a casos. [Base64-codificar](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) ou [codificar](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode) valores de metadados de URL que contenham caracteres não ASCII.

O nome dos seus metadados deve estar em conformidade com as convenções de nomeação dos identificadores C#. Os nomes dos metadados mantêm o caso utilizado quando foram criados, mas são insensíveis a casos quando definidos ou lidos. Se dois ou mais cabeçalhos de metadados usando o mesmo nome forem submetidos para um recurso, o armazenamento Azure Blob devolve o código de erro HTTP 400 (Mau Pedido).

O seguinte exemplo de código define metadados numa bolha. Um valor é definido usando o método da `Add` coleção. O outro valor é definido utilizando a sintaxe de chave/valor implícito.

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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Veja também

- [Definir operação Blob Properties](/rest/api/storageservices/set-blob-properties)
- [Obtenha a operação Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Definir operação de metadados blob](/rest/api/storageservices/set-blob-metadata)
- [Obtenha a operação de Metadados Blob](/rest/api/storageservices/get-blob-metadata)
