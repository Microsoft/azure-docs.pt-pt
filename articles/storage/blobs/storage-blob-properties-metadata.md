---
title: Gerenciar Propriedades e metadados para um blob com o .NET – armazenamento do Azure
description: Saiba como definir e recuperar propriedades do sistema e armazenar metadados personalizados em BLOBs em sua conta de armazenamento do Azure usando a biblioteca de cliente .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 03171e395bb23da05ee5420f60abca9a16ffb774
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72600296"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>Gerenciar Propriedades de BLOB e metadados com o .NET

Além dos dados que eles contêm, os BLOBs dão suporte a propriedades do sistema e metadados definidos pelo usuário. Este artigo mostra como gerenciar Propriedades do sistema e metadados definidos pelo usuário com a [biblioteca de cliente de armazenamento do Azure para .net](/dotnet/api/overview/azure/storage/client).

## <a name="about-properties-and-metadata"></a>Sobre propriedades e metadados

- **Propriedades do sistema**: existem propriedades do sistema em cada recurso de armazenamento de BLOBs. Alguns deles podem ser lidos ou definidos, enquanto outros são somente leitura. Nos bastidores, algumas propriedades do sistema correspondem a determinados cabeçalhos HTTP padrão. A biblioteca de cliente de armazenamento do Azure para .NET mantém essas propriedades para você.

- **Metadados definidos pelo usuário**: os metadados definidos pelo usuário consistem em um ou mais pares de nome-valor que você especifica para um recurso de armazenamento de BLOBs. Você pode usar metadados para armazenar valores adicionais com o recurso. Os valores de metadados são apenas para suas próprias finalidades e não afetam a forma como o recurso se comporta.

Recuperar metadados e valores de propriedade para um recurso de armazenamento de BLOBs é um processo de duas etapas. Antes de ler esses valores, você deve obtê-los explicitamente chamando o método `FetchAttributes` ou `FetchAttributesAsync`. A exceção a essa regra é que os métodos `Exists` e `ExistsAsync` chamam o método `FetchAttributes` apropriado nos bastidores. Ao chamar um desses métodos, você não precisa também chamar `FetchAttributes`.

> [!IMPORTANT]
> Se você achar que os valores de propriedade ou metadados de um recurso de armazenamento não foram preenchidos, verifique se o seu código chama o método `FetchAttributes` ou `FetchAttributesAsync`.

## <a name="set-and-retrieve-properties"></a>Definir e recuperar propriedades

O exemplo de código a seguir define o `ContentType` e `ContentLanguage` Propriedades do sistema em um blob.

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

Para recuperar propriedades de BLOB, chame o método `FetchAttributes` ou `FetchAttributesAsync` em seu blob para preencher a propriedade `Properties`. O exemplo de código a seguir obtém as propriedades do sistema de um blob e exibe alguns dos valores:

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

Você pode especificar metadados como um ou mais pares de nome-valor em um recurso de contêiner ou BLOB. Para definir metadados, adicione pares de nome-valor à coleção de `Metadata` no recurso. Em seguida, chame um dos seguintes métodos para gravar os valores:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

Os pares de nome/valor de metadados são cabeçalhos HTTP válidos e devem aderir a todas as restrições que regem os cabeçalhos HTTP. Os nomes de metadados devem ser nomes de cabeçalho HTTP C# válidos e identificadores válidos, podem conter apenas caracteres ASCII e devem ser tratados como não diferenciando maiúsculas de minúsculas. Valores [de metadados de codificação Base64](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) ou [de codificação de URL](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode) que contêm caracteres não ASCII.

O nome dos metadados deve estar em conformidade com as convenções de C# nomenclatura para identificadores. Os nomes de metadados mantêm o caso usado quando eles foram criados, mas não diferenciam maiúsculas de minúsculas quando definidos ou lidos. Se dois ou mais cabeçalhos de metadados que usam o mesmo nome forem enviados para um recurso, o armazenamento de BLOBs do Azure retornará o código de erro HTTP 400 (solicitação inadequada).

O exemplo de código a seguir define os metadados em um blob. Um valor é definido usando o método de `Add` da coleção. O outro valor é definido usando a sintaxe de chave/valor implícita.

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

Para recuperar metadados, chame o método `FetchAttributes` ou `FetchAttributesAsync` em seu BLOB ou contêiner para preencher a coleção de `Metadata` e, em seguida, leia os valores, conforme mostrado no exemplo abaixo.

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

## <a name="see-also"></a>Ver também

- [Definir operação de propriedades de BLOB](/rest/api/storageservices/set-blob-properties)
- [Operação de obtenção de propriedades de BLOB](/rest/api/storageservices/get-blob-properties)
- [Operação de definição de metadados de BLOB](/rest/api/storageservices/set-blob-metadata)
- [Operação de obtenção de metadados de BLOB](/rest/api/storageservices/set-blob-metadata)
