---
title: Introdução ao blob storage e o Visual Studio ligados a serviços (ASP.NET Core) | Documentos da Microsoft
description: Como começar a utilizar o armazenamento de Blobs do Azure num projeto do Visual Studio ASP.NET Core depois de criar uma conta de armazenamento com os serviços ligados do Visual Studio
services: storage
author: ghogen
manager: douge
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: 388c4d5f28e87f5cfe26336771d30fa44c6f9ef0
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123013"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Introdução ao Azure Blob storage e o Visual Studio ligados a serviços (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

Este artigo descreve como começar a utilizar o armazenamento de Blobs do Azure no Visual Studio depois de ter criado ou referenciados uma conta de armazenamento do Azure num projeto do ASP.NET Core, utilizando o Visual Studio **serviços ligados** funcionalidade. O **serviços ligados** operação instala os pacotes de NuGet adequados para aceder ao armazenamento do Azure no seu projeto e adiciona a cadeia de ligação para a conta de armazenamento para ficheiros de configuração do projeto. (Consulte [documentação do armazenamento](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o armazenamento do Azure.)

Armazenamento de Blobs do Azure é um serviço para armazenar grandes quantidades de dados não estruturados, que podem ser acedidos em qualquer local no mundo através de HTTP ou HTTPS. Um blob único pode ser qualquer tamanho. BLOBs podem ser coisas como imagens, arquivos de áudio e vídeo, dados não processados e arquivos do documento. Este artigo descreve como começar com o armazenamento de BLOBs, depois de criar uma conta de armazenamento do Azure utilizando o Visual Studio **serviços ligados** num projeto do ASP.NET Core.

Assim como ficheiros residam em pastas, os blobs de armazenamento em direto em contentores. Depois de criar um blob, crie um ou mais contentores desse blob. Por exemplo, num blob chamado "Scrapbook", pode criar contentores chamados "imagens" para armazenar imagens e outro chamado "áudio" para armazenar os arquivos de áudio. Depois de criar os contentores, pode carregar ficheiros individuais a eles. Consulte [início rápido: Carregar, transferir e listar blobs através do .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md) para obter mais informações sobre a manipulação programática de blobs.

Algumas das APIs de armazenamento do Azure são assíncronas e o código neste artigo supõe que métodos async que estão a ser utilizados. Ver [programação assíncrona](https://docs.microsoft.com/dotnet/csharp/async) para obter mais informações.

## <a name="access-blob-containers-in-code"></a>Contentores de BLOBs de acesso no código

Para acessar programaticamente os blobs em projetos do ASP.NET Core, tem de adicionar o código a seguir se não estiver já presente:

1. Adicione as informações necessárias `using` instruções:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Obter um `CloudStorageAccount` objeto que representa as suas informações de conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e as informações de conta de armazenamento da configuração do serviço do Azure:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Utilize um `CloudBlobClient` objeto para obter um `CloudBlobContainer` referência a um contentor existente na sua conta de armazenamento:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Criar um contentor no código

Também pode utilizar o `CloudBlobClient` para criar um contentor na conta de armazenamento ao chamar `CreateIfNotExistsAsync`:

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Para disponibilizar os ficheiros dentro do contentor para todos os utilizadores, defina o contentor como público:

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor

Para carregar um ficheiro de BLOBs para um contentor, obtenha uma referência de contentor e utilizá-lo para obter uma referência de blob. Em seguida, carregar qualquer fluxo de dados para essa referência ao chamar o `UploadFromStreamAsync` método. Esta operação cria o blob, se não estiver lá e substitui um blob existente. 

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

Para listar os blobs num contentor, obtenha primeiro uma referência de contentor, em seguida, chame seu `ListBlobsSegmentedAsync` método para recuperar os blobs e/ou diretórios dentro do mesmo. Para aceder ao conjunto avançado de propriedades e métodos de um `IListBlobItem`, o transmitir para um `CloudBlockBlob`, `CloudPageBlob`, ou `CloudBlobDirectory` objeto. Se não souber o tipo de blob, utilize uma verificação de tipo para determinar qual convertê-lo para.

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

Consulte [início rápido: Carregar, transferir e listar blobs através do .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container) para outras formas de listar o conteúdo de um contentor de Blobs.

## <a name="download-a-blob"></a>Transferir um blob

Para transferir um blob, obtenha primeiro uma referência para o blob, em seguida, chame o `DownloadToStreamAsync` método. O exemplo seguinte utiliza o `DownloadToStreamAsync` método para transferir os conteúdos do blob para um objeto de fluxo que, em seguida, pode guardar como um ficheiro local.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Consulte [início rápido: Carregar, transferir e listar blobs através do .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs) para outras formas de guardar blobs como ficheiros.

## <a name="delete-a-blob"></a>Eliminar um blob

Para eliminar um blob, obtenha primeiro uma referência para o blob, em seguida, chame o `DeleteAsync` método:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Passos Seguintes

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
