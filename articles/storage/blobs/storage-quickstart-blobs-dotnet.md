---
title: 'Início rápido: Biblioteca de clientes do Blob storage do Azure para .NET'
description: Neste início rápido, irá aprender a utilizar a biblioteca de cliente de armazenamento de Blobs do Azure para .NET para criar um contentor e um blob no armazenamento de Blob (objeto). Em seguida, vai aprender a transferir o blob para o computador local e a listar todos os blobs num contentor.
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: mhopkins
ms.subservice: blobs
ms.openlocfilehash: c5e9981c6854ff778775631f1d671189830e564b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67435757"
---
# <a name="quickstart-azure-blob-storage-client-library-for-net"></a>Início rápido: Biblioteca de clientes do Blob storage do Azure para .NET

Introdução à biblioteca de cliente do armazenamento de Blobs do Azure para .NET. Armazenamento de Blobs do Azure é a solução de armazenamento de objeto da Microsoft para a nuvem. Siga os passos para instalar o pacote e experimentar o código de exemplo para tarefas básicas. Armazenamento de Blobs está otimizado para armazenar grandes quantidades de dados não estruturados.

Utilize a biblioteca de cliente do armazenamento de Blobs do Azure para .NET para:

* Criar um contentor
* Definir permissões num contentor
* Criar um blob no armazenamento do Azure
* Transferir o blob para o computador local
* Listar todos os blobs num contentor
* Eliminar um contentor

[Documentação de referência de API](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet) | [código fonte da biblioteca](https://github.com/Azure/azure-storage-net/tree/master/Blob) | [pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/) | [exemplos](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure - [criá-lo gratuitamente](https://azure.microsoft.com/free/)
* Conta de armazenamento do Azure - [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [SDK 2.1 do NET Core](https://dotnet.microsoft.com/download/dotnet-core), ou posterior, do seu sistema operativo

## <a name="setting-up"></a>Como configurar

Esta secção explica como preparar um projeto para trabalhar com a biblioteca de cliente do armazenamento de Blobs do Azure para .NET.

### <a name="create-the-project"></a>Criar o projeto

Primeiro, crie uma aplicação .NET Core com o nome **BLOBs-quickstart**.

1. Na janela de consola (por exemplo, cmd, o PowerShell ou Bash), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome **blob-quickstart**. Este comando cria um simples "Hello World" C# projeto com um único arquivo de origem: **Program.cs**.

   ```console
   dotnet new console -n blob-quickstart
   ```

2. Mude para o recém-criado **BLOBs-quickstart** pasta e a aplicação para verificar que tudo estiver bem de compilação.

   ```console
   cd blob-quickstart
   ```

   ```console
   dotnet build
   ```

A saída esperada da compilação deve ter um aspeto semelhante ao seguinte:

```output
C:\QuickStarts\blob-quickstart> dotnet build
Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 44.31 ms for C:\QuickStarts\blob-quickstart\blob-quickstart.csproj.
  blob-quickstart -> C:\QuickStarts\blob-quickstart\bin\Debug\netcoreapp2.1\blob-quickstart.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:03.08
```

### <a name="install-the-package"></a>Instalar o pacote

Ainda no diretório da aplicação, instale a biblioteca de cliente de armazenamento de Blobs do Azure para o pacote do .NET utilizando o `dotnet add package` comando.

```console
dotnet add package Microsoft.Azure.Storage.Blob
```

### <a name="set-up-the-app-framework"></a>Configurar a estrutura de aplicação

Do diretório do projeto:

1. Abra o ficheiro Program.cs no seu editor
2. Remover os **Console. WriteLine** instrução
3. Adicione **usando** diretivas
4. Criar uma **ProcessAsync** método onde irão residir o código principal para o exemplo
5. Modo assíncrono chamar o **ProcessAsync** método a partir de **principal**

Aqui está o código:

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

namespace blob_quickstart
{
    class Program
    {
        public static void Main()
        {
            Console.WriteLine("Azure Blob Storage - .NET quickstart sample\n");

            // Run the examples asynchronously, wait for the results before proceeding
            ProcessAsync().GetAwaiter().GetResult();

            Console.WriteLine("Press any key to exit the sample application.");
            Console.ReadLine();
        }

        private static async Task ProcessAsync()
        {
        }
    }
}
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>Copiar as credenciais do Portal do Azure

A aplicação de exemplo tem de autenticar o acesso à sua conta de armazenamento. Para autenticar, adicione as credenciais da conta de armazenamento para a aplicação como uma cadeia de ligação. Veja as credenciais da conta de armazenamento através dos seguintes passos:

1. Navegue para o [portal do Azure](https://portal.azure.com).
2. Localize a sua conta de armazenamento.
3. Na secção **Definições** da descrição geral da conta de armazenamento, selecione **Chaves de acesso**. Aqui, pode ver as chaves de acesso da conta e a cadeia de ligação completa para cada chave.
4. Encontre o valor da **Cadeia de ligação** em **key1** e selecione o botão **Copiar** para copiar a cadeia de ligação. Irá adicionar o valor da cadeia de ligação para uma variável de ambiente no próximo passo.

    ![Captura de ecrã que mostra como copiar uma cadeia de ligação do portal do Azure](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento

Após ter copiado a cadeia de ligação, escreva-a numa nova variável de ambiente no computador local que está a executar a aplicação. Para definir a variável de ambiente, abra uma janela da consola e siga as instruções relevantes para o seu sistema operativo. Substitua `<yourconnectionstring>` pela cadeia de ligação real.

Depois de adicionar a variável de ambiente, poderá ter de reiniciar todos os programas em execução que serão necessário ler a variável de ambiente. Por exemplo, se estiver a utilizar o Visual Studio como editor, reinicie o Visual Studio antes de executar o exemplo.

#### <a name="windows"></a>Windows

```cmd
setx STORAGE_CONNECTION_STRING "<yourconnectionstring>"
```

#### <a name="linux"></a>Linux

```bash
export STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="macos"></a>MacOS

```bash
export STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

## <a name="object-model"></a>Modelo de objeto

Armazenamento de Blobs do Azure está otimizado para armazenar grandes quantidades de dados não estruturados. Dados não estruturados são os dados que não cumprem a um modelo de dados específica ou a definição, como texto ou dados binários. Armazenamento de BLOBs oferece três tipos de recursos:

* A conta de armazenamento.
* Um contentor na conta de armazenamento
* Um blob num contentor

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama da arquitetura de armazenamento de BLOBs](./media/storage-quickstart-blobs-dotnet/blob1.png)

Utilize as seguintes classes do .NET para interagir com estes recursos:

* [CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount): O **CloudStorageAccount** classe representa a sua conta de armazenamento do Azure. Utilize esta classe para autorizar o acesso ao armazenamento de Blobs com as chaves de acesso da conta.
* [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient): O **CloudBlobClient** classe fornece um ponto de acesso ao serviço de BLOBs no seu código.
* [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer): O **CloudBlobContainer** classe representa um contentor de BLOBs no seu código.
* [CloudBlockBlob](//dotnet/api/microsoft.azure.storage.blob.cloudblockblob): O **CloudBlockBlob** objeto representa um blob de blocos em seu código. Os blobs de blocos são constituídos por blocos de dados que podem ser geridos individualmente.

## <a name="code-examples"></a>Exemplos de código

Estes trechos de código de exemplo mostram-lhe como realizar o seguinte com a biblioteca de cliente de armazenamento de Blobs do Azure para .NET:

   * [Autenticar o cliente](#authenticate-the-client)
   * [Criar um contentor](#create-a-container)
   * [Definir permissões num contentor](#set-permissions-on-a-container)
   * [Carregar blobs para um contentor](#upload-blobs-to-a-container)
   * [Listar os blobs num contentor](#list-the-blobs-in-a-container)
   * [Transferir blobs](#download-blobs)
   * [Eliminar um contentor](#delete-a-container)

### <a name="authenticate-the-client"></a>Autenticar o cliente

O código a seguir verifica se a variável de ambiente contém uma cadeia de ligação que pode ser analisada para criar uma [CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount?view=azure-dotnet) objeto apontando para a conta de armazenamento. Para verificar se a cadeia de ligação é válida, utilize o método [TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse?view=azure-dotnet). Se o método **TryParse** for bem-sucedido, inicializa a variável *storageAccount* e devolve o valor **true**.

Adicione este código dentro do **ProcessAsync** método:

```csharp
// Retrieve the connection string for use with the application. The storage 
// connection string is stored in an environment variable on the machine 
// running the application called STORAGE_CONNECTION_STRING. If the 
// environment variable is created after the application is launched in a 
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("STORAGE_CONNECTION_STRING");

// Check whether the connection string can be parsed.
CloudStorageAccount storageAccount;
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob
    // storage here.
    // ADD OTHER OPERATIONS HERE
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'STORAGE_CONNECTION_STRING' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the application.");
    Console.ReadLine();
}
```

> [!NOTE]
> Para executar o restante das operações neste artigo, substitua **/ / adicionar outras operações aqui** no código acima com os trechos de código nas secções seguintes.

### <a name="create-a-container"></a>Criar um contentor

Para criar o contentor, primeiro crie uma instância do objeto [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient), que aponte para o Armazenamento de blobs na sua conta de armazenamento. Em seguida, crie uma instância do objeto [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) e depois crie o contentor.

Neste caso, o código chama o [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync) método para criar o contentor. É anexado um valor GUID ao nome do contentor para garantir que é exclusivo. Num ambiente de produção, muitas vezes, é preferível utilizar o [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync) método para criar um contentor apenas se ainda não exista.

> [!IMPORTANT]
> Os nomes dos contentores têm de estar em minúscula. Para obter mais informações sobre a atribuição de nomes de contentores e blobs, veja [Nomenclatura e Referenciação de Contentores, Blobs e Metadados](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

```csharp
// Create the CloudBlobClient that represents the 
// Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and 
// append a GUID value to it to make the name unique.
CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.GetContainerReference("quickstartblobs" + 
        Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();
```

### <a name="set-permissions-on-a-container"></a>Definir permissões num contentor

Definir as permissões no contentor, para que os blobs no contentor sejam públicos. Se um blob for público, pode ser acedido anonimamente por qualquer cliente.

```csharp
// Set the permissions so the blobs are public.
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-a-container"></a>Carregar blobs para um contentor

O fragmento de código seguinte obtém uma referência para um **CloudBlockBlob** objeto ao chamar os [GetBlockBlobReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getblockblobreference) método no contentor criado na secção anterior. Em seguida, carrega o ficheiro local selecionado para o blob ao chamar o [UploadFromFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromfileasync) método. Este método cria o blob, caso ainda não exista, ou substitui-o se o mesmo já existir.

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
string sourceFile = Path.Combine(localPath, localFileName);
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

Listar os blobs no contentor utilizando o [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync) método. Neste caso, apenas um blob foi adicionado ao contentor, para que a operação de listagem devolve apenas esse único blob.

Se existirem demasiados blobs a devolver numa só chamada (por predefinição, mais de 5000), o método **ListBlobsSegmentedAsync** devolve um segmento do conjunto de resultados total e um token de continuação. Para obter o segmento seguinte de blobs, indique o token de continuação devolvido pela chamada anterior e assim sucessivamente, até o token de continuação ser nulo. Um token de continuação nulo indica que foram obtidos todos os blobs. O código mostra como usar a continuação token para fins de melhores práticas.

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

Transferir o blob criado anteriormente para o sistema de arquivos local ao utilizar o [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) método. O código de exemplo adiciona um sufixo downloaded"ao nome do blob, para que possa ver ambos os ficheiros no sistema de arquivos local.

```csharp
// Download the blob to a local file, using the reference created earlier.
// Append the string "_DOWNLOADED" before the .txt extension so that you 
// can see both files in MyDocuments.
string destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);
```

### <a name="delete-a-container"></a>Eliminar um contentor

O código a seguir limpa os recursos a aplicação que criou ao eliminar o contentor inteiro com [Deleteasync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync). Se quiser, também pode eliminar os ficheiros locais.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the example files, " +
    "example container, and exit the application.");
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

## <a name="run-the-code"></a>Executar o código

Esta aplicação cria um ficheiro de teste no local **MyDocuments** pasta e carrega-o para o armazenamento de Blobs. O exemplo, em seguida, lista os blobs no contentor e transfere o ficheiro com um novo nome para que pode comparar os ficheiros novos e antigos.

Se estiver a utilizar o Visual Studio como editor, pode premir **F5** para executar.

Caso contrário, navegue para o diretório de aplicações, em seguida, criar e executar a aplicação.

```console
dotnet build
```

```console
dotnet run
```

O resultado da aplicação de exemplo é semelhante ao seguinte exemplo:

```output
Azure Blob storage - .NET Quickstart example

Created container 'quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f'

Temp file = C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-96
97c748f4db.txt
Uploading to Blob storage as blob 'QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f
4db.txt'

Listing blobs in container.
https://storagesamples.blob.core.windows.net/quickstartblobs33c90d2a-eabd-4236-
958b-5cc5949e731f/QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt

Downloading blob to C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926
-a9da-9697c748f4db_DOWNLOADED.txt

Press any key to delete the example files and example container.
```

Quando premir a tecla **Enter**, a aplicação elimina o contentor de armazenamento e os ficheiros. Antes de os eliminar, procure os dois ficheiros na pasta **MyDocuments**. Pode abri-los e constatar que são idênticos. Copie o URL do blob a partir da janela da consola e cole-o num browser para ver o conteúdo do blob.

Depois de verificar os ficheiros, prima qualquer tecla para concluir a demonstração e eliminar os ficheiros de teste.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu a carregar, transferir e listar blobs com o .NET.

Para saber como criar uma aplicação web que carrega uma imagem para o armazenamento de BLOBs, avance para:

> [!div class="nextstepaction"]
> [Carregar e processar imagens](storage-upload-process-images.md)

* Para saber mais sobre o .NET Core, veja [Introdução ao .NET em 10 minutos](https://www.microsoft.com/net/learn/get-started/).
* Para explorar uma aplicação de exemplo que pode implementar a partir do Visual Studio para Windows, veja [Exemplo de Aplicação Web de Galeria de Fotografias do .NET com o Armazenamento de Blobs do Azure](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/).
