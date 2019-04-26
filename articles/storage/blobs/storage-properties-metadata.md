---
title: Definir e obter as propriedades do objeto e metadados no armazenamento do Azure | Documentos da Microsoft
description: Store metadados personalizados em objetos no armazenamento do Azure e definir e obter propriedades do sistema.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: tamram
ms.openlocfilehash: e85cfb6d7b44924ce7f17fdedb7f1b52350ab598
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60392403"
---
# <a name="set-and-retrieve-properties-and-metadata"></a>Definir e obter propriedades e metadados

Objetos em Propriedades do sistema de suporte de armazenamento do Azure e os metadados definidos pelo usuário, além dos dados que contêm. Este artigo aborda as propriedades do sistema de gestão e os metadados definidos pelo utilizador com o [biblioteca de clientes de armazenamento do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/).

* **Propriedades do sistema**: Propriedades do sistema de existir em cada recurso de armazenamento. Alguns deles podem ser lidos ou definidos, enquanto outras são só de leitura. Nos bastidores, algumas propriedades do sistema correspondem a determinados cabeçalhos HTTP padrão. Bibliotecas de cliente de armazenamento do Azure de manter essas propriedades para.

* **Metadados definidos pelo utilizador**: Metadados definidos pelo utilizador consiste num ou mais pares de nome-valor que especificar para um recurso de armazenamento do Azure. Pode usar os metadados para armazenar valores adicionais com um recurso. Valores de metadados são suas próprias finalidades e não afetam o comportamento do recurso.

Recuperando valores de propriedade e os metadados de um recurso de armazenamento é um processo de dois passos. Antes de pode ler esses valores, deve explicitamente buscá-los ao chamar o **FetchAttributes** ou **FetchAttributesAsync** método. A exceção é se chamasse o **Exists** ou **ExistsAsync** método num recurso. Quando chama um dos seguintes métodos, o armazenamento do Azure chama o adequado **FetchAttributes** método nos bastidores como parte da chamada para o **Exists** método.

> [!IMPORTANT]
> Se achar que não tenham sido populados valores de propriedade ou metadados para um recurso de armazenamento, em seguida, verifique que o seu código chama o **FetchAttributes** ou **FetchAttributesAsync** método.
>
> Pares de nome/valor de metadados são cabeçalhos HTTP válidos e, por isso, devem cumprir todas as restrições que regem os cabeçalhos HTTP. Nomes de metadados têm de ser válidos nomes de cabeçalho HTTP e válido C# identificadores, pode conter apenas carateres ASCII e devem ser tratados como maiúsculas de minúsculas. Os valores de metadados que contém carateres não ASCII devem ser codificada em Base64 ou com codificação URL.

## <a name="setting-and-retrieving-properties"></a>Configurando e recuperando propriedades
Para obter valores de propriedade, chamar o **FetchAttributesAsync** método no blob ou contentor para preencher as propriedades, em seguida, leia os valores.

Para definir as propriedades de um objeto, especifique a propriedade de valor, em seguida, chamar o **quais** método.

O exemplo de código seguinte cria um contentor, em seguida, escreve alguns dos seus valores de propriedade para uma janela da consola.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

//Create the service client object for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it does not already exist.
container.CreateIfNotExists();

// Fetch container properties and write out their values.
await container.FetchAttributesAsync();
Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
Console.WriteLine("ETag: {0}", container.Properties.ETag);
Console.WriteLine();
```

## <a name="setting-and-retrieving-metadata"></a>A definição e obtenção de metadados
Pode especificar metadados como um ou mais pares de nome-valor num recurso de blob ou contentor. Para definir metadados, adicionar os pares de nome-valor para o **metadados** coleção no recurso, em seguida, chamar os **SetMetadata** ou **SetMetadataAsync** método para salvar os valores para o serviço.

> [!NOTE]
> O nome de seus metadados deve estar em conformidade com as convenções de nomenclatura para os identificadores do c#.
>
>

O exemplo de código seguinte define metadados num contêiner. Um valor é definido através da coleção **adicionar** método. O outro valor é definido com a sintaxe de chave/valor implícito. Ambos são válidas.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    // Add some metadata to the container.
    container.Metadata.Add("docType", "textDocuments");
    container.Metadata["category"] = "guidance";

    // Set the container's metadata.
    await container.SetMetadataAsync();
}
```

Para obter os metadados, chame o **FetchAttributes** ou **FetchAttributesAsync** método no blob ou contentor para preencher o **metadados** coleção, em seguida, leia a valores, conforme mostrado no exemplo abaixo.

```csharp
public static async Task ListContainerMetadataAsync(CloudBlobContainer container)
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
```

## <a name="next-steps"></a>Passos Seguintes
* [Biblioteca de clientes de armazenamento do Azure para referência de .NET](/dotnet/api/?term=Microsoft.WindowsAzure.Storage)
* [Biblioteca de clientes de armazenamento do Azure para o pacote .NET NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/)
