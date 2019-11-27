---
title: 'Início rápido: biblioteca de armazenamento de BLOBs do Azure V12-.NET'
description: Neste guia de início rápido, você aprende a usar a biblioteca de cliente de armazenamento de BLOBs do Azure versão 12 para .NET para criar um contêiner e um blob no armazenamento de BLOB (objeto). Em seguida, vai aprender a transferir o blob para o computador local e a listar todos os blobs num contentor.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 8a4ebf6c2ddf3e361e306ae37ad8dabb052e0efc
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423989"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-net"></a>Início rápido: biblioteca de cliente do armazenamento de BLOBs do Azure V12 para .NET

Introdução à biblioteca de cliente do armazenamento de BLOBs do Azure V12 para .NET. O Armazenamento de Blobs do Azure é a solução de armazenamento de objetos da Microsoft para a cloud. Siga as etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas. O Armazenamento de blobs está otimizado para armazenar quantidades em grande escala de dados não estruturados.

> [!NOTE]
> Para começar a usar a versão anterior do SDK, consulte [início rápido: biblioteca de cliente do armazenamento de BLOBs do Azure para .net](storage-quickstart-blobs-dotnet-legacy.md).

Use a biblioteca de cliente do armazenamento de BLOBs do Azure V12 para .NET para:

* Criar um contentor
* Carregar um blob no armazenamento do Azure
* Listar todos os BLOBs em um contêiner
* Baixe o blob em seu computador local
* Eliminar um contentor

[Documentação de referência de API](/dotnet/api/azure.storage.blobs) | [amostras](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples) de | do pacote de [código-fonte](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs) [(NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs/12.0.0) | 

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* Conta de armazenamento do Azure – [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [SDK do .NET Core](https://dotnet.microsoft.com/download/dotnet-core) atual para seu sistema operacional. Certifique-se de obter o SDK e não o tempo de execução.

## <a name="setting-up"></a>Configurando

Esta seção orienta você pela preparação de um projeto para trabalhar com a biblioteca de cliente do armazenamento de BLOBs do Azure V12 para .NET.

### <a name="create-the-project"></a>Criar o projeto

Crie um aplicativo .NET Core chamado *BlobQuickstartV12*.

1. Em uma janela de console (como cmd, PowerShell ou bash), use o comando `dotnet new` para criar um novo aplicativo de console com o nome *BlobQuickstartV12*. Este comando cria um projeto simples de " C# Olá, mundo" com um único arquivo de origem: *Program.cs*.

   ```console
   dotnet new console -n BlobQuickstartV12
   ```

1. Alterne para o diretório *BlobQuickstartV12* recém-criado.

   ```console
   cd BlobQuickstartV12
   ```

1. No lado do diretório *BlobQuickstartV12* , crie outro diretório chamado *Data*. É aqui que os arquivos de dados de blob serão criados e armazenados.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Instalar o pacote

Ainda no diretório do aplicativo, instale o pacote biblioteca de cliente do armazenamento de BLOBs do Azure para .NET usando o comando `dotnet add package`.

```console
dotnet add package Azure.Storage.Blobs
```

### <a name="set-up-the-app-framework"></a>Configurar a estrutura do aplicativo

No diretório do projeto:

1. Abra o arquivo *Program.cs* em seu editor
1. Remover a instrução `Console.WriteLine("Hello World!");`
1. Adicionar `using` diretivas
1. Atualizar a declaração do método de `Main` para dar suporte ao código assíncrono

Este é o código:

```csharp
using Azure.Storage;
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

[!INCLUDE [storage-quickstart-connection-string-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O armazenamento de BLOBs do Azure é otimizado para armazenar grandes quantidades de dados não estruturados. Dados não estruturados são dados que não aderem a um modelo ou definição de dados específico, como texto ou dados binários. O armazenamento de BLOBs oferece três tipos de recursos:

* A conta de armazenamento
* Um contêiner na conta de armazenamento
* Um blob no contêiner

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama da arquitetura de armazenamento de BLOBs](./media/storage-blob-introduction/blob1.png)

Use as seguintes classes .NET para interagir com estes recursos:

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient): a classe `BlobServiceClient` permite que você manipule os recursos de armazenamento do Azure e contêineres de BLOB.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): a classe `BlobContainerClient` permite que você manipule contêineres de armazenamento do Azure e seus BLOBs.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): a classe `BlobClient` permite que você manipule os blobs de armazenamento do Azure.
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): a classe `BlobDownloadInfo` representa as propriedades e o conteúdo retornado do download de um blob.

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código de exemplo mostram como executar o seguinte com a biblioteca de cliente de armazenamento de BLOBs do Azure para .NET:

* [Obter a cadeia de conexão](#get-the-connection-string)
* [Criar um contêiner](#create-a-container)
* [Carregar BLOBs em um contêiner](#upload-blobs-to-a-container)
* [Listar os BLOBs em um contêiner](#list-the-blobs-in-a-container)
* [Baixar BLOBs](#download-blobs)
* [Eliminar um contentor](#delete-a-container)

### <a name="get-the-connection-string"></a>Obter a cadeia de ligação

O código a seguir recupera a cadeia de conexão para a conta de armazenamento da variável de ambiente criada na seção [configurar sua cadeia de conexão de armazenamento](#configure-your-storage-connection-string) .

Adicione este código dentro do método `Main`:

```csharp
Console.WriteLine("Azure Blob storage v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called CONNECT_STR. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("CONNECT_STR");
```

### <a name="create-a-container"></a>Criar um contentor

Escolha um nome para o novo contêiner. O código a seguir acrescenta um valor de GUID ao nome do contêiner para garantir que ele seja exclusivo.

> [!IMPORTANT]
> Os nomes dos contentores têm de estar em minúscula. Para obter mais informações sobre a atribuição de nomes de contentores e blobs, veja [Nomenclatura e Referenciação de Contentores, Blobs e Metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Crie uma instância da classe [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) . Em seguida, chame o método [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) para criar o contêiner em sua conta de armazenamento.

Adicione este código ao final do método de `Main`:

```csharp
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

//Create a unique name for the container
string containerName = "quickstartblobs" + Guid.NewGuid().ToString();

// Create the container and return a container client object
BlobContainerClient containerClient = await blobServiceClient.CreateBlobContainerAsync(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Carregar BLOBs em um contêiner

O seguinte trecho de código:

1. Cria um arquivo de texto no diretório de *dados* local.
1. Obtém uma referência a um objeto [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) chamando o método [GetBlobClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobclient) no contêiner a partir da seção [criar um contêiner](#create-a-container) .
1. Carrega o arquivo de texto local para o blob chamando o método [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) . Este método cria o blob, caso ainda não exista, ou substitui-o se o mesmo já existir.

Adicione este código ao final do método de `Main`:

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
await blobClient.UploadAsync(uploadFileStream);
uploadFileStream.Close();
```

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Liste os BLOBs no contêiner chamando o método [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) . Nesse caso, apenas um blob foi adicionado ao contêiner, portanto, a operação de listagem retorna apenas um blob.

Adicione este código ao final do método de `Main`:

```csharp
Console.WriteLine("Listing blobs...");

// List all blobs in the container
await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
{
    Console.WriteLine("\t" + blobItem.Name);
}
```

### <a name="download-blobs"></a>Transferir blobs

Baixe o blob criado anteriormente chamando o método [DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync) . O código de exemplo adiciona um sufixo de "baixado" ao nome do arquivo para que você possa ver ambos os arquivos no sistema de arquivos local.

Adicione este código ao final do método de `Main`:

```csharp
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension so you can see both files in MyDocuments
string downloadFilePath = localFilePath.Replace(".txt", "DOWNLOAD.txt");

Console.WriteLine("\nDownloading blob to\n\t{0}\n", downloadFilePath);

// Download the blob's contents and save it to a file
BlobDownloadInfo download = await blobClient.DownloadAsync();

using FileStream downloadFileStream = File.OpenWrite(downloadFilePath);
await download.Content.CopyToAsync(downloadFileStream);
downloadFileStream.Close();
```

### <a name="delete-a-container"></a>Eliminar um contentor

O código a seguir limpa os recursos que o aplicativo criou excluindo o contêiner inteiro usando [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync). Ele também exclui os arquivos locais criados pelo aplicativo.

O aplicativo pausa a entrada do usuário chamando `Console.ReadLine` antes de excluir o blob, o contêiner e os arquivos locais. Essa é uma boa chance de verificar se os recursos foram realmente criados corretamente, antes de serem excluídos.

Adicione este código ao final do método de `Main`:

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

Este aplicativo cria um arquivo de teste na pasta local *MyDocuments* e o carrega no armazenamento de BLOBs. Em seguida, o exemplo lista os BLOBs no contêiner e baixa o arquivo com um novo nome para que você possa comparar os arquivos novos e antigos.

Navegue até o diretório do aplicativo e, em seguida, compile e execute o aplicativo.

```console
dotnet build
```

```console
dotnet run
```

A saída do aplicativo é semelhante ao exemplo a seguir:

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

Antes de começar o processo de limpeza, verifique os dois arquivos na pasta meus *documentos* . Pode abri-los e constatar que são idênticos.

Depois de verificar os arquivos, pressione a tecla **Enter** para excluir os arquivos de teste e concluir a demonstração.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a carregar, transferir e listar blobs com o .NET.

Para ver os aplicativos de exemplo de armazenamento de BLOBs, continue em:

> [!div class="nextstepaction"]
> [Exemplos do .NET do SDK do armazenamento de BLOBs do Azure V12](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

* Para obter tutoriais, exemplos, inícios rápidos e outras documentações, visite [Azure para .net e desenvolvedores do .NET Core](/dotnet/azure/).
* Para saber mais sobre o .NET Core, veja [Introdução ao .NET em 10 minutos](https://www.microsoft.com/net/learn/get-started/).
