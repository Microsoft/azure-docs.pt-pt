---
title: Começar com o armazenamento de blob usando o Visual Studio (ASP.NET Core)
description: Como começar a usar o armazenamento Azure Blob num projeto Visual Studio ASP.NET Core depois de ter criado uma conta de armazenamento usando serviços conectados do Estúdio Visual
services: storage
author: ghogen
manager: jillfra
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 81df41470c893f569fd17345e8bdf4b29641ec64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298829"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Inicie com serviços conectados de armazenamento Azure Blob e Visual Studio (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

Este artigo descreve como começar a usar o armazenamento Azure Blob no Visual Studio depois de ter criado ou referenciado uma conta de armazenamento Azure num projeto ASP.NET Core utilizando a funcionalidade Visual Studio **Connected Services.** A operação **Connected Services** instala os pacotes NuGet apropriados para aceder ao armazenamento do Azure no seu projeto e adiciona a cadeia de ligação para a conta de armazenamento aos seus ficheiros de configuração do projeto. (Consulte a [documentação](https://azure.microsoft.com/documentation/services/storage/) de armazenamento para obter informações gerais sobre o Armazenamento de Azure.)

O armazenamento da Azure Blob é um serviço para armazenar grandes quantidades de dados não estruturados que podem ser acedidos a partir de qualquer parte do mundo através de HTTP ou HTTPS. Uma única bolha pode ser de qualquer tamanho. Blobs podem ser coisas como imagens, ficheiros de áudio e vídeo, dados brutos e ficheiros de documentos. Este artigo descreve como começar com o armazenamento de blob depois de criar uma conta de armazenamento Azure usando o Visual Studio **Connected Services** num projeto ASP.NET Core.

Tal como os ficheiros vivem em pastas, as bolhas de armazenamento vivem em contentores. Depois de criar uma bolha, cria-se um ou mais contentores nessa bolha. Por exemplo, numa bolha chamada "Scrapbook", pode criar recipientes chamados "imagens" para armazenar imagens e outro chamado "áudio" para armazenar ficheiros áudio. Depois de criar os recipientes, pode enviar ficheiros individuais para eles. Ver [Quickstart: Upload, download e list blobs usando .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md) para mais informações sobre bolhas manipuladoras programáticas.

Algumas das APIs de Armazenamento Azure são assíncronas, e o código neste artigo pressupõe que os métodos de asincronização estão a ser usados. Consulte [a programação assíncrona](https://docs.microsoft.com/dotnet/csharp/async) para obter mais informações.

## <a name="access-blob-containers-in-code"></a>Aceder a recipientes blob em código

Para aceder programáticamente a blobs em ASP.NET projetos Core, você precisa adicionar o seguinte código se ainda não estiver presente:

1. Adicione as `using` declarações necessárias:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Obtenha `CloudStorageAccount` um objeto que represente a informação da sua conta de armazenamento. Utilize o seguinte código para obter as informações da sua linha de ligação de armazenamento e da sua conta de armazenamento a partir da configuração do serviço Azure:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Utilize `CloudBlobClient` um objeto `CloudBlobContainer` para obter uma referência a um recipiente existente na sua conta de armazenamento:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Criar um recipiente em código

Também pode utilizar `CloudBlobClient` o para criar um recipiente `CreateIfNotExistsAsync`na sua conta de armazenamento, ligando para:

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Para disponibilizar os ficheiros dentro do contentor a todos, coloque o recipiente em público:

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor

Para fazer upload de um ficheiro blob num recipiente, obtenha uma referência de recipiente e use-a para obter uma referência blob. Em seguida, faça upload de qualquer `UploadFromStreamAsync` fluxo de dados para essa referência, chamando o método. Esta operação cria a bolha se ainda não estiver lá, e substitui uma bolha existente. 

```cs
// Get a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with the contents of a local file
// named "myfile".
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    await blockBlob.UploadFromStreamAsync(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Para listar as bolhas num recipiente, primeiro obtenha uma `ListBlobsSegmentedAsync` referência de recipiente e, em seguida, ligue para o seu método para recuperar as bolhas e/ou diretórios dentro dele. Para aceder ao rico conjunto de `IListBlobItem`propriedades e métodos `CloudPageBlob`para `CloudBlobDirectory` um devolvido, lançá-lo para um `CloudBlockBlob`, ou objeto. Se não conhece o tipo de bolha, utilize uma verificação de tipo para determinar a que se deve lançar.

```cs
BlobContinuationToken token = null;
do
{
    BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
    token = resultSegment.ContinuationToken;

    foreach (IListBlobItem item in resultSegment.Results)
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
        }

        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);
        }

        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }
} while (token != null);
```

Ver [Quickstart: Upload, download e list blobs usando .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container) para outras formas de listar o conteúdo de um recipiente blob.

## <a name="download-a-blob"></a>Transferir um blob

Para baixar uma bolha, primeiro obtenha uma referência à `DownloadToStreamAsync` bolha, em seguida, ligue para o método. O exemplo que `DownloadToStreamAsync` se segue utiliza o método para transferir o conteúdo da bolha para um objeto de fluxo que pode então guardar como ficheiro local.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Ver [Quickstart: Upload, download e list blobs usando .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs) para outras formas de guardar bolhas como ficheiros.

## <a name="delete-a-blob"></a>Eliminar um blob

Para eliminar uma bolha, primeiro obtenha uma referência à `DeleteAsync` bolha, em seguida, ligue para o método:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
