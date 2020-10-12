---
title: Utilize .NET para gerir propriedades e metadados para um recipiente blob
titleSuffix: Azure Storage
description: Saiba como definir e recuperar as propriedades do sistema e armazenar metadados personalizados em recipientes blob na sua conta de Armazenamento Azure utilizando a biblioteca de clientes .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/01/2020
ms.author: tamram
ms.custom: devx-track-csharp
ms.openlocfilehash: 115ab9767c23afd5876eb23821bd1283777cdb5e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89018931"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>Gerir propriedades de contentores e metadados com .NET

Os contentores blob suportam as propriedades do sistema e os metadados definidos pelo utilizador, além dos dados que contêm. Este artigo mostra como gerir as propriedades do sistema e metadados definidos pelo utilizador com a biblioteca de [clientes de Armazenamento Azure para .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-properties-and-metadata"></a>Sobre propriedades e metadados

- **Propriedades do sistema**: As propriedades do sistema existem em cada recurso de armazenamento Blob. Alguns podem ser lidos ou definidos, enquanto outros são apenas de leitura. Sob as capas, algumas propriedades do sistema correspondem a certos cabeçalhos HTTP padrão. A biblioteca cliente Azure Storage para .NET mantém estas propriedades para si.

- **Metadados definidos pelo utilizador**: Os metadados definidos pelo utilizador consistem num ou mais pares de valor-nome que especifica para um recurso de armazenamento Blob. Pode utilizar metadados para armazenar valores adicionais com o recurso. Os valores dos metadados são apenas para os seus próprios fins e não afetam o comportamento do recurso.

Os pares de nome/valor dos metadados são cabeçalhos HTTP válidos, pelo que devem aderir a todas as restrições que regem os cabeçalhos HTTP. Os nomes dos metadados devem ser válidos Nomes de cabeçalhos HTTP e identificadores C# válidos, podem conter apenas caracteres ASCII e devem ser tratados como insensíveis a casos. Os valores dos metadados que contenham caracteres não ASCII devem ser codificados ou codificados por URL.

## <a name="retrieve-container-properties"></a>Recuperar propriedades de contentores

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Para recuperar as propriedades do contentor, ligue para um dos seguintes métodos:

- [GetProperties](/dotnet/api/azure.storage.blobs.blobcontainerclient.getproperties)
- [GetPropertiesAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getpropertiesasync)

O seguinte exemplo de código requer as propriedades do sistema de um contentor e escreve alguns valores de propriedade para uma janela de consola:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadContainerProperties":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Recuperar valores de propriedade e metadados para um recurso de armazenamento Blob é um processo em duas etapas. Antes de poder ler estes valores, deve ir buscá-los explicitamente, chamando o método **FetchAttributes** ou **FetchAttributesAsync.** A exceção a esta regra é que os métodos **Exist e** **ExistsAsync** chamam o método **de FetchAttributes** adequado sob as capas. Quando você chama um destes métodos, você não precisa também chamar **FetchAttributes**.

> [!IMPORTANT]
> Se descobrir que os valores de propriedade ou metadados para um recurso de armazenamento não foram povoados, verifique se o seu código chama o método **FetchAttributes** ou **FetchAttributesAsync.**

Para recuperar as propriedades do contentor, ligue para um dos seguintes métodos:

- [FetchAttributes](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchAttributesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

O seguinte exemplo de código requer as propriedades do sistema de um contentor e escreve alguns valores de propriedade para uma janela de consola:

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

---

## <a name="set-and-retrieve-metadata"></a>Definir e recuperar metadados

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Pode especificar os metadados como um ou mais pares de valor-nome num recurso de bolha ou contentor. Para definir metadados, adicione pares de valor-nome a um objeto [IDictionário](/dotnet/api/system.collections.idictionary) e, em seguida, ligue para um dos seguintes métodos para escrever os valores:

- [SetMetadata](/dotnet/api/azure.storage.blobs.blobcontainerclient.setmetadata)
- [SetMetadataAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.setmetadataasync)

O nome dos seus metadados deve estar em conformidade com as convenções de nomeação dos identificadores C#. Os nomes dos metadados preservam o caso com o qual foram criados, mas são insensíveis a casos quando definidos ou lidos. Se dois ou mais cabeçalhos de metadados com o mesmo nome forem submetidos para um recurso, o blob de armazenamento separa e concatena os dois valores e devolve o código de resposta HTTP 200 (OK).

O seguinte exemplo de código define metadados num recipiente.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_AddContainerMetadata":::

Para recuperar metadados, ligue para um dos seguintes métodos:

- [GetProperties](/dotnet/api/azure.storage.blobs.blobcontainerclient.getproperties)
- [GetPropertiesAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getpropertiesasync).

Em seguida, leia os valores, como mostra o exemplo abaixo.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadContainerMetadata":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Pode especificar os metadados como um ou mais pares de valor-nome num recurso de bolha ou contentor. Para definir metadados, adicione pares de valor-nome à coleção **de metadados** no recurso e, em seguida, ligue para um dos seguintes métodos para escrever os valores:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

O nome dos seus metadados deve estar em conformidade com as convenções de nomeação dos identificadores C#. Os nomes dos metadados preservam o caso com o qual foram criados, mas são insensíveis a casos quando definidos ou lidos. Se dois ou mais cabeçalhos de metadados com o mesmo nome forem submetidos para um recurso, o blob de armazenamento separa e concatena os dois valores e devolve o código de resposta HTTP 200 (OK).

O seguinte exemplo de código define metadados num recipiente. Um valor é definido usando o método **Add** da coleção. O outro valor é definido utilizando a sintaxe de chave/valor implícito. Ambos são válidos.

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

Para obter metadados, ligue para o método **FetchAttributes** ou **FetchAttributesAsync** no seu blob ou recipiente para preencher a recolha de **metadados** e, em seguida, leia os valores, como mostrado no exemplo abaixo.

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

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Consulte também

- [Obter operação de propriedades de contentores](/rest/api/storageservices/get-container-properties)
- [Definir operação de metadados de contentores](/rest/api/storageservices/set-container-metadata)
- [Obtenha a operação de metadados de contentores](/rest/api/storageservices/get-container-metadata)
