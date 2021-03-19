---
title: Aceda a contentores públicos e bolhas de forma anónima com .NET
titleSuffix: Azure Storage
description: Utilize a biblioteca de clientes Azure Storage para .NET para aceder a contentores e bolhas públicas de forma anónima.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/02/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 2437c5b3272163b3931d7417c84e761c591aec85
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90088821"
---
# <a name="access-public-containers-and-blobs-anonymously-with-net"></a>Aceda a contentores públicos e bolhas de forma anónima com .NET

O Azure Storage suporta o acesso público opcional para recipientes e bolhas. Os clientes podem aceder a contentores e blobs públicos de forma anónima utilizando as bibliotecas de clientes do Azure Storage, bem como utilizando outras ferramentas e utilitários que suportem o acesso de dados ao Azure Storage.

Este artigo mostra como aceder a um contentor público ou bolha a partir de .NET. Para obter informações sobre a configuração do acesso anónimo a um recipiente, consulte [o público anónimo Configure acesso a contentores e bolhas](anonymous-read-access-configure.md). Para obter informações sobre a prevenção de todo o acesso anónimo a uma conta de armazenamento, consulte [Prevent Anonymous Public Read Access to Containers and Blobs](anonymous-read-access-prevent.md).

Um cliente que acede a contentores e bolhas anonimamente pode usar construtores que não requerem credenciais. Os exemplos a seguir mostram algumas formas diferentes de referenciar contentores e bolhas de forma anónima.

## <a name="create-an-anonymous-client-object"></a>Criar um objeto cliente anónimo

Pode criar um novo objeto de cliente de serviço para acesso anónimo, fornecendo o ponto final de armazenamento Blob para a conta. No entanto, também deve saber o nome de um contentor nessa conta que está disponível para acesso anónimo.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_CreateAnonymousBlobClient":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint for your account.
    CloudBlobClient blobClient = new CloudBlobClient(
        new Uri(@"https://storagesamples.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. 
    // Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

---

## <a name="reference-a-container-anonymously"></a>Referenciar um recipiente anonimamente

Se tiver o URL num recipiente que esteja disponível anonimamente, pode usá-lo para fazer referência direta ao recipiente.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_ListBlobsAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    // Note this is only possible when the container supports full public read access.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

---

## <a name="reference-a-blob-anonymously"></a>Referência uma bolha anonimamente

Se tiver o URL para uma bolha disponível para acesso anónimo, pode fazer referência à bolha diretamente utilizando esse URL:

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_DownloadBlobAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

---

## <a name="next-steps"></a>Passos seguintes

- [Configurar o público anónimo ler acesso a contentores e bolhas](anonymous-read-access-configure.md)
- [Impedir que o público anónimo leia o acesso a contentores e bolhas](anonymous-read-access-prevent.md)
- [Autorizar o acesso ao Armazenamento Azure](../common/storage-auth.md)
