---
title: Utilize .NET para gerir propriedades e metadados para um recipiente blob
titleSuffix: Azure Storage
description: Saiba como definir e recuperar propriedades do sistema e armazenar metadados personalizados em recipientes blob na sua conta De armazenamento Azure utilizando a biblioteca de clientes .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c66b521b5cd75825fcafe07b24d5d527c45f5153
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79135926"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>Gerir propriedades de contentores e metadados com .NET

Os recipientes blob suportam propriedades do sistema e metadados definidos pelo utilizador, além dos dados que contêm. Este artigo mostra como gerir as propriedades do sistema e os metadados definidos pelo utilizador com a [biblioteca de clientes do Azure Storage para .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-properties-and-metadata"></a>Sobre propriedades e metadados

- **Propriedades do sistema**: Existem propriedades do sistema em cada recurso de armazenamento Blob. Alguns podem ser lidos ou definidos, enquanto outros são apenas para leitura. Sob as coberturas, algumas propriedades do sistema correspondem a determinados cabeçalhos HTTP padrão. A biblioteca de clientes Azure Storage para .NET mantém estas propriedades para si.

- **Metadados definidos pelo utilizador**: Os metadados definidos pelo utilizador consistem num ou mais pares de valor de nome que especifica para um recurso de armazenamento Blob. Pode utilizar metadados para armazenar valores adicionais com o recurso. Os valores dos metadados são apenas para os seus próprios fins, e não afetam o comportamento do recurso.

Recuperar valores de propriedade e metadados para um recurso de armazenamento Blob é um processo em duas etapas. Antes de poder ler estes valores, deve rebusca-los explicitamente, ligando para o método **FetchAttributes** ou **FetchAttributesAsync.** A exceção a esta regra é que os métodos **Existentes** e **Existentes Async** chamam o método **FetchAttributes** apropriado sob as capas. Quando chama um destes métodos, também não precisa de chamar **FetchAttributes**.

> [!IMPORTANT]
> Se verificar que os valores de propriedade ou metadados de um recurso de armazenamento não foram povoados, verifique se o seu código chama o método **FetchAttributes** ou **FetchASAsync.**

Os pares de nome/valor de metadados são cabeçalhos HTTP válidos, pelo que devem aderir a todas as restrições que regem os cabeçalhos HTTP. Os nomes de metadados devem ser nomes de cabeçalho http válidos e identificadores C# válidos, podem conter apenas caracteres ASCII, e devem ser tratados como insensíveis a casos. Os valores dos metadados que contenham caracteres não ASCII devem ser codificados pelo Base64 ou codificados por URL.

## <a name="retrieve-container-properties"></a>Recuperar propriedades de contentores

Para recuperar as propriedades do contentor, ligue para um dos seguintes métodos:

- [FetchATributos](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchASAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

O seguinte exemplo de código adquire as propriedades do sistema de um contentor e escreve alguns valores de propriedade para uma janela de consola:

```csharp
private static async Task ReadContainerPropertiesAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch some container properties and write out their values.
        await container.FetchAttributesAsync();
        Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri);
        Console.WriteLine("Public access level: {0}", container.Properties.PublicAccess);
        Console.WriteLine("Last modified time in UTC: {0}", container.Properties.LastModified);
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

Pode especificar metadados como um ou mais pares de valor de nome num recurso blob ou contentor. Para definir metadados, adicione pares de valor de nome à recolha de **Metadados** no recurso e, em seguida, ligue para um dos seguintes métodos para escrever os valores:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

O nome dos seus metadados deve estar em conformidade com as convenções de nomeação para identificadores C#. Os nomes dos metadados preservam o caso com o qual foram criados, mas são insensíveis aos casos quando definidos ou lidos. Se dois ou mais cabeçalhos de metadados com o mesmo nome forem submetidos para um recurso, o armazenamento blob devolve o código de erro HTTP 400 (Pedido Mau).

O exemplo de código que se segue define metadados num recipiente. Um valor é definido usando o método **Add** da coleção. O outro valor é definido utilizando a sintaxe implícita da chave/valor. Ambos são válidos.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        // Set the container's metadata.
        await container.SetMetadataAsync();
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

Para recuperar metadados, ligue para o método **FetchAttributes** ou **FetchAttributesAsync** na sua bolha ou recipiente para povoar a recolha de **Metadados** e, em seguida, leia os valores, como mostra o exemplo abaixo.

```csharp
public static async Task ReadContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch container attributes in order to populate the container's properties and metadata.
        await container.FetchAttributesAsync();

        // Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
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

- [Obter operação Propriedades de Contentores](/rest/api/storageservices/get-container-properties)
- [Definir operação de metadados de contentores](/rest/api/storageservices/set-container-metadata)
- [Obtenha operação de metadados de contentores](/rest/api/storageservices/set-container-metadata)
