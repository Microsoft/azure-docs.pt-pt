---
title: 'Início rápido: Utilizar o .NET para criar um blob no armazenamento de objetos - armazenamento do Azure'
description: Neste início rápido, vai aprender a utilizar a biblioteca de cliente do Armazenamento do Microsoft Azure para .NET para criar um contentor e um blob no armazenamento de Blobs (objetos). Em seguida, vai aprender a transferir o blob para o computador local e a listar todos os blobs num contentor.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 11/14/2018
ms.author: tamram
ms.openlocfilehash: 50bb13ecaa9e6076f00749d54b492a1e6663a93e
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287116"
---
# <a name="quickstart-use-net-to-create-a-blob-in-object-storage"></a>Início rápido: Utilizar o .NET para criar um blob no armazenamento de objetos

Neste início rápido, vai aprender a utilizar a biblioteca de cliente do Armazenamento do Microsoft Azure para .NET para criar um contentor e um blob no armazenamento de Blobs (objetos). Em seguida, vai aprender a transferir o blob para o computador local e a listar todos os blobs num contentor.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Em seguida, transfira e instale o .NET Core 2.0 referente ao seu sistema operativo. Se estiver a executar o Windows, pode instalar o Visual Studio e utilizar o .NET Framework, se preferir. Também pode optar por instalar um editor para utilizar com o sistema operativo.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

- Instale o [.NET Core para Windows](https://www.microsoft.com/net/download/windows) ou o [.NET Framework](https://www.microsoft.com/net/download/windows) (incluído no Visual Studio para Windows)
- Instale o [Visual Studio para Windows](https://www.visualstudio.com/). Se estiver a utilizar o .NET Core, instalar o Visual Studio é opcional.  

Para obter informações sobre como escolher entre o .NET Core e o .NET Framework, veja [Escolher entre o .NET Core e o .NET Framework para aplicações de servidor](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

- Instale o [.NET Core para Linux](https://www.microsoft.com/net/download/linux)
- Opcionalmente, instale o [Visual Studio Code](https://www.visualstudio.com/) e a [extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp&dotnetid=963890049.1518206068)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

- Instale o [.NET Core para macOS](https://www.microsoft.com/net/download/macos).
- Opcionalmente, instale o [Visual Studio para Mac](https://www.visualstudio.com/vs/visual-studio-mac/)

---

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

A aplicação de exemplo utilizada neste início rápido é uma aplicação de consola básica. Pode explorar a aplicação de exemplo no [GitHub](https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart).

Utilize o [git](https://git-scm.com/) para transferir uma cópia da aplicação para o seu ambiente de desenvolvimento. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

Este comando clona o repositório para a sua pasta local do git. Para abrir a solução do Visual Studio, procure a pasta *storage-blobs-dotnet-quickstart*, abra-a e faça duplo clique em *storage-blobs-dotnet-quickstart.sln*. 

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento

Para executar a aplicação, tem de indicar a cadeia de ligação da sua conta de armazenamento. A aplicação de exemplo lê a cadeia de ligação da variável de um ambiente e utiliza-a para autorizar os pedidos ao Armazenamento do Azure.

Após ter copiado a cadeia de ligação, escreva-a numa nova variável de ambiente no computador local que está a executar a aplicação. Para definir a variável de ambiente, abra uma janela da consola e siga as instruções relevantes para o seu sistema operativo. Substitua `<yourconnectionstring>` pela cadeia de ligação real:

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

Depois de adicionar a variável de ambiente, poderá ter de reiniciar todos os programas em execução que irão precisar de ler a variável de ambiente, incluindo a janela da consola. Por exemplo, se estiver a utilizar o Visual Studio como editor, reinicie o Visual Studio antes de executar o exemplo. 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export storageconnectionstring=<yourconnectionstring>
```

Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela da consola para que as alterações entrem em vigor.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Edite o seu .bash_profile e adicione a variável de ambiente:

```bash
export STORAGE_CONNECTION_STRING=<yourconnectionstring>
```

Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela da consola para que as alterações entrem em vigor.

---

## <a name="run-the-sample"></a>Executar o exemplo

Este exemplo cria um ficheiro de teste na sua pasta **MyDocuments** local e carrega-o no Armazenamento de blobs. Em seguida, o exemplo lista os blobs no contentor e transfere o ficheiro com um novo nome para que possa comparar o ficheiro antigo com o novo. 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Se estiver a utilizar o Visual Studio como editor, pode premir **F5** para executar. 

Caso contrário, navegue para o diretório de aplicações e execute a aplicação com o comando `dotnet run`.

```
dotnet run
```

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Navegue para o diretório de aplicações e execute a aplicação com o comando `dotnet run`.

```
dotnet run
```

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Navegue para o diretório de aplicações e execute a aplicação com o comando `dotnet run`.

```
dotnet run
```

---

O resultado do exemplo de aplicação é semelhante ao seguinte:

```
Azure Blob storage - .NET Quickstart sample

Created container 'quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f'

Temp file = C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt
Uploading to Blob storage as blob 'QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt'

Listing blobs in container.
https://storagesamples.blob.core.windows.net/quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f/QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt

Downloading blob to C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db_DOWNLOADED.txt

Press any key to delete the sample files and example container.
```

Quando premir a tecla **Enter**, a aplicação elimina o contentor de armazenamento e os ficheiros. Antes de os eliminar, procure os dois ficheiros na pasta **MyDocuments**. Pode abri-los e constatar que são idênticos. Copie o URL do blob a partir da janela da consola e cole-o num browser para ver o conteúdo do blob.

Depois de verificar os ficheiros, prima qualquer tecla para concluir a demonstração e eliminar os ficheiros de teste. Agora que sabe o que o exemplo faz, abra o ficheiro Program.cs para ver o código. 

## <a name="understand-the-sample-code"></a>Compreender o código de exemplo

Em seguida, explore o código de exemplo para compreender como funciona.

### <a name="try-parsing-the-connection-string"></a>Experimentar a análise da cadeia de ligação

A primeira ação realizada pelo exemplo consiste em verificar se a variável de ambiente contém uma cadeia de ligação passível de análise de modo a criar um objeto [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) que aponte para a conta de armazenamento. Para verificar se a cadeia de ligação é válida, utilize o método [TryParse](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.tryparse). Se o método **TryParse** for bem-sucedido, inicializa a variável *storageAccount* e devolve o valor **true**.

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable on the machine running the application called storageconnectionstring.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell or application needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");

// Check whether the connection string can be parsed.
CloudStorageAccount storageAccount;
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob storage here.
    ...
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the sample application.");
    Console.ReadLine();
}
```

### <a name="create-the-container-and-set-permissions"></a>Criar o contentor e definir permissões

Em seguida, o exemplo cria um contentor e define as respetivas permissões de modo a que os blobs existentes no contentor sejam públicos. Se um blob for público, pode ser acedido anonimamente por qualquer cliente.

Para criar o contentor, primeiro crie uma instância do objeto [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient), que aponte para o Armazenamento de blobs na sua conta de armazenamento. Em seguida, crie uma instância do objeto [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer) e depois crie o contentor. 

Neste caso, o exemplo chama o método [CreateAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createasync) para criar o contentor. É anexado um valor GUID ao nome do contentor para garantir que é exclusivo. Num ambiente de produção, é muitas vezes preferível utilizar o método [CreateIfNotExistsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createifnotexistsasync) para criar um contentor apenas se este ainda não existir e evitar conflitos de nomenclatura.

> [!IMPORTANT]
> Os nomes dos contentores têm de estar em minúscula. Para obter mais informações sobre a atribuição de nomes de contentores e blobs, veja [Nomenclatura e Referenciação de Contentores, Blobs e Metadados](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).


```csharp
// Create the CloudBlobClient that represents the Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and append a GUID value to it to make the name unique. 
CloudBlobContainer cloudBlobContainer = cloudBlobClient.GetContainerReference("quickstartblobs" + Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contentor

Em seguida, o exemplo carrega um ficheiro local para um blob de blocos. O exemplo de código obtém uma referência a um objeto **CloudBlockBlob** ao chamar o método [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference) no contentor criado na secção anterior. Em seguida, carrega o ficheiro selecionado para o blob ao chamar o método [UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync). Este método cria o blob, caso ainda não exista, ou substitui-o se o mesmo já existir. 

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the blob address, then upload the file to the blob.
// Use the value of localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

O exemplo lista os blobs no contentor com o método [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync). No caso do exemplo, foi adicionado apenas um blob ao contentor, pelo que a operação de listagem devolve apenas esse único blob.

Se existirem demasiados blobs a devolver numa só chamada (por predefinição, mais de 5000), o método **ListBlobsSegmentedAsync** devolve um segmento do conjunto de resultados total e um token de continuação. Para obter o segmento seguinte de blobs, indique o token de continuação devolvido pela chamada anterior e assim sucessivamente, até o token de continuação ser nulo. Um token de continuação nulo indica que foram obtidos todos os blobs. O código de exemplo mostra como utilizar o token de continuação por uma questão de demonstração das melhores práticas.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    // Get the value of the continuation token returned by the listing call.
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
} while (blobContinuationToken != null); // Loop while the continuation token is not null. 

```

### <a name="download-blobs"></a>Transferir blobs

Em seguida, o exemplo transfere o blob criado anteriormente para o seu sistema de ficheiros local através do método [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync). O código de exemplo adiciona o sufixo "_DOWNLOADED" ao nome do blob para que possa ver ambos os ficheiros no sistema de ficheiros local.

```csharp
// Download the blob to a local file, using the reference created earlier. 
// Append the string "_DOWNLOADED" before the .txt extension so that you can see both files in MyDocuments.
destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);  
```

### <a name="clean-up-resources"></a>Limpar recursos

O exemplo limpa os recursos que criou ao eliminar o contentor inteiro com [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync). Se quiser, também pode eliminar os ficheiros locais.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the sample files, example container, and exit the application.");
Console.ReadLine();
// Clean up resources. This includes the container and the two temp files.
Console.WriteLine("Deleting the container");
if (cloudBlobContainer != null)
{
    await cloudBlobContainer.DeleteIfExistsAsync();
}
Console.WriteLine("Deleting the source, and downloaded files");
File.Delete(sourceFile);
File.Delete(destinationFile);
```

## <a name="resources-for-developing-net-applications-with-blobs"></a>Recursos para desenvolver aplicações .NET com blobs

Consulte estes recursos adicionais para o desenvolvimento de .NET com Armazenamento de blobs:

### <a name="binaries-and-source-code"></a>Binários e código fonte

- Transfira o pacote NuGet para obter a versão mais recente da [biblioteca de cliente .NET](https://www.nuget.org/packages/WindowsAzure.Storage/) do Armazenamento do Azure. 
- Veja o [código fonte da biblioteca de cliente .NET](https://github.com/Azure/azure-storage-net) no GitHub.

### <a name="client-library-reference-and-samples"></a>Referência e exemplos da biblioteca de cliente

- Consulte a [Referência da API de .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage) para obter mais informações sobre a biblioteca de cliente .NET.
- Explore os [Exemplos de armazenamento de blobs](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob) escritos com a biblioteca de cliente .NET.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu a carregar, transferir e listar blobs com o .NET. 

Para saber como criar uma aplicação Web que carrega uma imagem para o Armazenamento de blobs, avance para o tópico [Carregar dados de imagem na cloud com o Armazenamento do Azure](storage-upload-process-images.md).

> [!div class="nextstepaction"]
> [Procedimentos de Operações de Armazenamento de Blobs](storage-dotnet-how-to-use-blobs.md)

- Para saber mais sobre o .NET Core, veja [Introdução ao .NET em 10 minutos](https://www.microsoft.com/net/learn/get-started/).
- Para explorar uma aplicação de exemplo que pode implementar a partir do Visual Studio para Windows, veja [Exemplo de Aplicação Web de Galeria de Fotografias do .NET com o Armazenamento de Blobs do Azure](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/).
 
