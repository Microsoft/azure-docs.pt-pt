---
title: 'Quickstart: Biblioteca de armazenamento Azure Blob v12 - .NET'
description: Neste arranque rápido, aprende-se a utilizar a versão 12 da biblioteca do cliente de armazenamento Azure Blob para .NET para criar um recipiente e uma bolha no armazenamento blob (objeto). Em seguida, vai aprender a transferir o blob para o computador local e a listar todos os blobs num contentor.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 5cfb0430bc94d347afd75bc01170a71a7ad53565
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84711872"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-net"></a>Quickstart: Azure Blob storage client library v12 for .NET

Começa com a biblioteca de clientes de armazenamento Azure Blob v12 para .NET. O Armazenamento de Blobs do Azure é a solução de armazenamento de objetos da Microsoft para a cloud. Siga as medidas para instalar a embalagem e experimente o código de exemplo para tarefas básicas. O Armazenamento de blobs está otimizado para armazenar quantidades em grande escala de dados não estruturados.

> [!NOTE]
> Para começar com a versão SDK anterior, consulte [a biblioteca de clientes de armazenamento Quickstart: Azure Blob para .NET](storage-quickstart-blobs-dotnet-legacy.md).

Utilize a biblioteca de clientes de armazenamento Azure Blob v12 para .NET para:

* Criar um contentor
* Faça o upload de uma bolha para o Azure Storage
* Listar todas as bolhas em um recipiente
* Descarregue a bolha para o seu computador local
* Eliminar um contentor

[Documentação de](/dotnet/api/azure.storage.blobs)  |  referência da API [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)  |  [Amostras](https://docs.microsoft.com/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
* Conta de armazenamento Azure - [crie uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Corrente [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) para o seu sistema operativo. Certifique-se de pegar o SDK e não o tempo de execução.

## <a name="setting-up"></a>Configuração

Esta secção acompanha-o através da preparação de um projeto para trabalhar com a biblioteca de clientes de armazenamento Azure Blob v12 para .NET.

### <a name="create-the-project"></a>Criar o projeto

Criar uma aplicação .NET Core chamada *BlobQuickstartV12*.

1. Numa janela de consola (como cmd, PowerShell ou Bash), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome *BlobQuickstartV12*. Este comando cria um projeto simples "Hello World" C# com um único ficheiro de origem: *Program.cs*.

   ```console
   dotnet new console -n BlobQuickstartV12
   ```

1. Mude para o recém-criado *diretório BlobQuickstartV12.*

   ```console
   cd BlobQuickstartV12
   ```

1. Ao lado do *diretório BlobQuickstartV12,* crie outro diretório chamado *dados*. É aqui que os ficheiros de dados blob serão criados e armazenados.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Instale o pacote

Enquanto ainda está no diretório de aplicações, instale a biblioteca de clientes de armazenamento Azure Blob para o pacote .NET utilizando o `dotnet add package` comando.

```console
dotnet add package Azure.Storage.Blobs
```

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Do diretório do projeto:

1. Abra o ficheiro *Program.cs* no seu editor
1. Remova a `Console.WriteLine("Hello World!");` declaração
1. Adicionar `using` diretivas
1. Atualizar a `Main` declaração do método para apoiar o código async

Aqui está o código:

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System;
using System.IO;
using System.Threading.Tasks;

namespace BlobQuickstartV12
{
    class Program
    {
        static async Task Main()
        {
        }
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O armazenamento Azure Blob está otimizado para armazenar quantidades massivas de dados não estruturados. Os dados não estruturados são dados que não seguem uma definição ou um modelo de dados em particular, como por exemplo, texto ou dados binários. O armazenamento blob oferece três tipos de recursos:

* A conta de armazenamento
* Um recipiente na conta de armazenamento
* Uma bolha no recipiente

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama da arquitetura de armazenamento blob](./media/storage-blobs-introduction/blob1.png)

Utilize as seguintes classes .NET para interagir com estes recursos:

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient): A `BlobServiceClient` classe permite-lhe manipular os recursos de armazenamento do Azure e os recipientes blob.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): A `BlobContainerClient` classe permite-lhe manipular os recipientes de armazenamento Azure e as suas bolhas.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): A `BlobClient` classe permite-lhe manipular as bolhas de armazenamento Azure.
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): A `BlobDownloadInfo` classe representa as propriedades e conteúdos devolvidos do download de uma bolha.

## <a name="code-examples"></a>Exemplos de código

Estes excertos de código de exemplo mostram-lhe como executar o seguinte com a biblioteca do cliente de armazenamento Azure Blob para .NET:

* [Obter a cadeia de ligação](#get-the-connection-string)
* [Criar um contentor](#create-a-container)
* [Carregar bolhas para um recipiente](#upload-blobs-to-a-container)
* [Listar os blobs num contentor](#list-the-blobs-in-a-container)
* [Transferir blobs](#download-blobs)
* [Eliminar um contentor](#delete-a-container)

### <a name="get-the-connection-string"></a>Obter a cadeia de ligação

O código abaixo recupera a cadeia de ligação para a conta de armazenamento a partir da variável ambiente criada na secção de cadeia de [ligação de armazenamento.](#configure-your-storage-connection-string)

Adicione este código dentro do `Main` método:

```csharp
Console.WriteLine("Azure Blob storage v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-container"></a>Criar um contentor

Decida o nome do novo recipiente. O código abaixo anexa um valor GUID ao nome do recipiente para garantir que é único.

> [!IMPORTANT]
> Os nomes dos contentores têm de estar em minúscula. Para obter mais informações sobre a atribuição de nomes de contentores e blobs, veja [Nomenclatura e Referenciação de Contentores, Blobs e Metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Crie uma instância da classe [BlobServiceClient.](/dotnet/api/azure.storage.blobs.blobserviceclient) Em seguida, ligue para o método [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) para criar o recipiente na sua conta de armazenamento.

Adicione este código ao fim do `Main` método:

```csharp
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

//Create a unique name for the container
string containerName = "quickstartblobs" + Guid.NewGuid().ToString();

// Create the container and return a container client object
BlobContainerClient containerClient = await blobServiceClient.CreateBlobContainerAsync(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Carregar bolhas para um recipiente

O seguinte corte de código:

1. Cria um ficheiro de texto no diretório de *dados* local.
1. Obtém uma referência a um objeto [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) chamando o método [GetBlobClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobclient) no recipiente a partir da secção [criar um recipiente.](#create-a-container)
1. Envia o ficheiro de texto local para a bolha, chamando o método [UploadAsync.](/dotnet/api/azure.storage.blobs.blobclient.uploadasync#Azure_Storage_Blobs_BlobClient_UploadAsync_System_IO_Stream_System_Boolean_System_Threading_CancellationToken_) Este método cria o blob, caso ainda não exista, ou substitui-o se o mesmo já existir.

Adicione este código ao fim do `Main` método:

```csharp
// Create a local file in the ./data/ directory for uploading and downloading
string localPath = "./data/";
string fileName = "quickstart" + Guid.NewGuid().ToString() + ".txt";
string localFilePath = Path.Combine(localPath, fileName);

// Write text to the file
await File.WriteAllTextAsync(localFilePath, "Hello, World!");

// Get a reference to a blob
BlobClient blobClient = containerClient.GetBlobClient(fileName);

Console.WriteLine("Uploading to Blob storage as blob:\n\t {0}\n", blobClient.Uri);

// Open the file and upload its data
using FileStream uploadFileStream = File.OpenRead(localFilePath);
await blobClient.UploadAsync(uploadFileStream, true);
uploadFileStream.Close();
```

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Enuncie as bolhas no recipiente chamando o método [GetBlobsAsync.](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) Neste caso, apenas uma bolha foi adicionada ao recipiente, pelo que a operação de listagem devolve apenas uma bolha.

Adicione este código ao fim do `Main` método:

```csharp
Console.WriteLine("Listing blobs...");

// List all blobs in the container
await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
{
    Console.WriteLine("\t" + blobItem.Name);
}
```

### <a name="download-blobs"></a>Transferir blobs

Descarregue a bolha anteriormente criada chamando o método [DownloadAsync.](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync) O código de exemplo adiciona um sufixo de "DOWNLOADED" ao nome do ficheiro para que possa ver ambos os ficheiros no sistema de ficheiros local.

Adicione este código ao fim do `Main` método:

```csharp
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension 
// so you can compare the files in the data directory
string downloadFilePath = localFilePath.Replace(".txt", "DOWNLOAD.txt");

Console.WriteLine("\nDownloading blob to\n\t{0}\n", downloadFilePath);

// Download the blob's contents and save it to a file
BlobDownloadInfo download = await blobClient.DownloadAsync();

using (FileStream downloadFileStream = File.OpenWrite(downloadFilePath))
{
    await download.Content.CopyToAsync(downloadFileStream);
    downloadFileStream.Close();
}
```

### <a name="delete-a-container"></a>Eliminar um contentor

O seguinte código limpa os recursos que a aplicação criou através da eliminação de todo o recipiente utilizando [o DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync). Também elimina os ficheiros locais criados pela app.

A aplicação faz uma pausa para a entrada do utilizador, ligando `Console.ReadLine` antes de eliminar a bolha, o recipiente e os ficheiros locais. Esta é uma boa oportunidade para verificar se os recursos foram efetivamente criados corretamente, antes de serem eliminados.

Adicione este código ao fim do `Main` método:

```csharp
// Clean up
Console.Write("Press any key to begin clean up");
Console.ReadLine();

Console.WriteLine("Deleting blob container...");
await containerClient.DeleteAsync();

Console.WriteLine("Deleting the local source and downloaded files...");
File.Delete(localFilePath);
File.Delete(downloadFilePath);

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>Executar o código

Esta aplicação cria um ficheiro de teste na sua pasta de *dados* local e envia-o para o armazenamento blob. O exemplo enumera então as bolhas no recipiente e descarrega o ficheiro com um novo nome para que possa comparar os ficheiros antigos e novos.

Navegue para o seu diretório de candidaturas, em seguida, construa e execute a aplicação.

```console
dotnet build
```

```console
dotnet run
```

A saída da app é semelhante ao seguinte exemplo:

```output
Azure Blob storage v12 - .NET quickstart sample

Uploading to Blob storage as blob:
         https://mystorageacct.blob.core.windows.net/quickstartblobs60c70d78-8d93-43ae-954d-8322058cfd64/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Listing blobs...
        quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Downloading blob to
        ./data/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31DOWNLOADED.txt

Press any key to begin clean up
Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Antes de iniciar o processo de limpeza, verifique se a pasta *de dados* é composta pelos dois ficheiros. Pode abri-los e constatar que são idênticos.

Depois de verificar os ficheiros, prima a tecla **'Entrar'** para eliminar os ficheiros de teste e terminar a demonstração.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a carregar, transferir e listar blobs com o .NET.

Para ver as aplicações de amostra de armazenamento Blob, continue a:

> [!div class="nextstepaction"]
> [Amostras SDK v12 .NET de armazenamento Azure Blob](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

* Para tutoriais, amostras, arranques rápidos e outra documentação, visite [a Azure para desenvolvedores .NET e .NET Core](/dotnet/azure/).
* Para saber mais sobre o .NET Core, veja [Introdução ao .NET em 10 minutos](https://www.microsoft.com/net/learn/get-started/).
