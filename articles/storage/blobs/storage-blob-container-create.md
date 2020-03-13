---
title: Criar ou eliminar um recipiente de bolha com .NET - Armazenamento Azure
description: Aprenda a criar ou apagar um recipiente de blob na sua conta de Armazenamento Azure utilizando a biblioteca de clientes .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: c95ed6dde3c00c0688ccfd58565fd112427c8899
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79135943"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>Criar ou eliminar um recipiente no Armazenamento Azure com .NET

As bolhas no Armazenamento Azure estão organizadas em contentores. Antes de poder fazer o upload de uma bolha, primeiro deve criar um recipiente. Este artigo mostra como criar e apagar contentores com a [biblioteca de clientes azure storage para .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="name-a-container"></a>Nomeie um recipiente

Um nome de recipiente deve ser um nome DNS válido, uma vez que faz parte do URI único utilizado para dirigir o recipiente ou as suas bolhas. Siga estas regras ao nomear um recipiente:

- Os nomes dos contentores podem ter entre 3 e 63 caracteres de comprimento.
- Os nomes do recipiente devem começar com uma letra ou número, e podem conter apenas letras minúsculas, números e o carácter do traço (-)
- Dois ou mais caracteres consecutivos de traços não são permitidos em nomes de contentores.

O URI para um recipiente está neste formato:

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Criar um contentor

Para criar um recipiente, ligue para um dos seguintes métodos:

- [Criar](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [Criarifnotexist](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

Os métodos **Create** and **CreateAsync** lançam uma exceção se um recipiente com o mesmo nome já existir.

Os métodos **CreateIfNotExists** e **CreateIfNotExistsAsync** devolvem um valor booleano indicando se o recipiente foi criado. Se um recipiente com o mesmo nome já existir, então estes métodos retornam **Falsos** para indicar que um novo recipiente não foi criado.

Os recipientes são criados imediatamente abaixo da conta de armazenamento. Não é possível nidificar um contentor debaixo do outro.

O exemplo seguinte cria um recipiente assincronicamente:

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

## <a name="create-the-root-container"></a>Criar o recipiente de raiz

Um recipiente de raiz serve como recipiente padrão para a sua conta de armazenamento. Cada conta de armazenamento pode ter um recipiente de raiz, que deve ser denominado *$root.* . . Deve criar ou eliminar explicitamente o recipiente de raiz.

Pode fazer referência a uma bolha armazenada no recipiente de raiz sem incluir o nome do recipiente de raiz. O recipiente de raiz permite-lhe fazer referência a uma bolha no nível superior da hierarquia da conta de armazenamento. Por exemplo, pode fazer referência a uma bolha que reside no recipiente de raiz da seguinte forma:

`https://myaccount.blob.core.windows.net/default.html`

O exemplo seguinte cria o recipiente de raiz sincronizadamente:

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

## <a name="delete-a-container"></a>Eliminar um contentor

Para eliminar um recipiente em .NET, utilize um dos seguintes métodos:

- [Eliminar](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [Eliminarifexista](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [Eliminar IfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)

Os métodos **Delete** e **DeleteAsync** atiram uma exceção se o recipiente não existir.

Os métodos **DeleteIfExists** e **DeleteIfExistsAsync** devolvem um valor booleano indicando se o recipiente foi eliminado. Se o recipiente especificado não existir, estes métodos retornam **falsos** para indicar que o recipiente não foi apagado.

Depois de apagar um recipiente, não pode criar um recipiente com o mesmo nome durante pelo menos 30 segundos, e possivelmente mais tempo. Enquanto o recipiente está a ser apagado, uma tentativa de criar um recipiente com o mesmo nome falhará com o código de erro HTTP 409 (Conflito). Quaisquer outras operações no recipiente ou nas bolhas que contém falharão com o código de erro HTTP 404 (Não Encontrado) enquanto o recipiente estiver a ser eliminado.

O seguinte exemplo elimina o recipiente especificado e trata da exceção se o recipiente não existir:

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

O exemplo que se segue mostra como eliminar todos os recipientes que começam com um prefixo especificado. O exemplo quebra o arrendamento se houver um arrendamento existente no contentor.

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            if (container.Properties.LeaseState == LeaseState.Leased)
            {
                await container.BreakLeaseAsync(null);
            }

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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Consulte também

- [Criar operação de contentores](/rest/api/storageservices/create-container)
- [Operação Eliminar Contentor](/rest/api/storageservices/delete-container)
