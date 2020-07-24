---
title: Criar ou apagar um recipiente blob com .NET - Azure Storage
description: Saiba como criar ou apagar um recipiente blob na sua conta de Armazenamento Azure utilizando a biblioteca de clientes .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/22/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: abf4cb33fa953ec9a257397551b3d17752fe67f5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87070737"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>Criar ou apagar um recipiente em Azure Storage com .NET

As bolhas no Azure Storage são organizadas em contentores. Antes de poder carregar uma bolha, tem primeiro de criar um recipiente. Este artigo mostra como criar e apagar contentores com a biblioteca cliente de [Armazenamento Azure para .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="name-a-container"></a>Nomeie um recipiente

Um nome de recipiente deve ser um nome DNS válido, uma vez que faz parte do URI único utilizado para dirigir-se ao recipiente ou às suas bolhas. Siga estas regras ao nomear um recipiente:

- Os nomes dos contentores podem ter entre 3 e 63 caracteres de comprimento.
- Os nomes dos recipientes devem começar com uma letra ou número, e podem conter apenas letras minúsculas, números e o caráter (-) do painel.
- Dois ou mais caracteres de traços consecutivos não são permitidos em nomes de contentores.

O URI para um recipiente está neste formato:

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Criar um contentor

Para criar um recipiente, ligue para um dos seguintes métodos:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

- [Criar](/dotnet/api/azure.storage.blobs.blobcontainerclient.create)
- [Criar Async](/dotnet/api/azure.storage.blobs.blobcontainerclient.createasync)
- [CriarExistsIfNotExists](/dotnet/api/azure.storage.blobs.blobcontainerclient.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.createifnotexistsasync)

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

- [Criar](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [Criar Async](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [CriarExistsIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)
---

Os métodos **Criar** e **CriarAync** lançam uma exceção se já existir um recipiente com o mesmo nome.

Os **métodos CreateIfNotExists** e **CreateIfNotExistsAsync** devolvem um valor Boolean que indica se o recipiente foi criado. Se um recipiente com o mesmo nome já existe, estes métodos devolvem **Falso** para indicar que um novo recipiente não foi criado.

Os contentores são criados imediatamente por baixo da conta de armazenamento. Não é possível nidificar um contentor debaixo de outro.

O exemplo a seguir cria um recipiente assíncronamente:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="CreateSampleContainerAsync":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```
---

## <a name="create-the-root-container"></a>Criar o recipiente raiz

Um recipiente de raiz serve como recipiente padrão para a sua conta de armazenamento. Cada conta de armazenamento pode ter um recipiente de raiz, que deve ser nomeado *$root*. O recipiente de raiz deve ser explicitamente criado ou eliminado.

Pode fazer referência a uma bolha armazenada no recipiente de raiz sem incluir o nome do recipiente raiz. O recipiente de raiz permite-lhe fazer referência a uma bolha no nível superior da hierarquia da conta de armazenamento. Por exemplo, pode fazer referência a uma bolha que se encontra no recipiente raiz da seguinte forma:

`https://myaccount.blob.core.windows.net/default.html`

O exemplo a seguir cria o recipiente raiz sincronizado:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="CreateRootContainer":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```
---

## <a name="delete-a-container"></a>Eliminar um contentor

Para eliminar um recipiente em .NET, utilize um dos seguintes métodos:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

- [Eliminar](/dotnet/api/azure.storage.blobs.blobcontainerclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteasync)
- [DeleteIfExists](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteifexistsasync)

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

- [Eliminar](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)
---

Os métodos **Eliminar** e **EliminarAsync** lançam uma exceção se o recipiente não existir.

Os **métodos DeleteIfExists** e **DeleteIfExistsAsync** devolvem um valor Boolean que indica se o recipiente foi eliminado. Se o recipiente especificado não existir, então estes métodos retornam **Falso** para indicar que o recipiente não foi apagado.

Depois de apagar um recipiente, não pode criar um recipiente com o mesmo nome durante *pelo menos* 30 segundos. A tentativa de criar um recipiente com o mesmo nome falhará com o código de erro HTTP 409 (Conflito). Quaisquer outras operações no recipiente ou nas bolhas que contém falharão com o código de erro HTTP 404 (Não Encontrado).

O exemplo a seguir elimina o recipiente especificado e trata da exceção se o recipiente não existir:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="DeleteSampleContainerAsync":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
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

O exemplo a seguir mostra como eliminar todos os recipientes que começam com um prefixo especificado.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="DeleteContainersWithPrefixAsync":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            await container.DeleteAsync();
        }

        Console.WriteLine();
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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Ver também

- [Criar operação de contentores](/rest/api/storageservices/create-container)
- [Operação Eliminar Contentor](/rest/api/storageservices/delete-container)
