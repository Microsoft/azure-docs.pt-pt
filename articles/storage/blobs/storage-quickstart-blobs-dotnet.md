---
title: 'Início rápido: Biblioteca de cliente de armazenamento de BLOBs do Azure para .NET'
description: Neste guia de início rápido, você aprende a usar a biblioteca de cliente de armazenamento de BLOBs do Azure para .NET para criar um contêiner e um blob no armazenamento de BLOB (objeto). Em seguida, vai aprender a transferir o blob para o computador local e a listar todos os blobs num contentor.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 40ee855aa01a9b24e35fb9863221e3607786d407
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722024"
---
# <a name="quickstart-azure-blob-storage-client-library-for-net"></a>Início rápido: Biblioteca de cliente de armazenamento de BLOBs do Azure para .NET

Introdução à biblioteca de cliente de armazenamento de BLOBs do Azure para .NET. O armazenamento de BLOBs do Azure é a solução de armazenamento de objetos da Microsoft para a nuvem. Siga as etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas. O armazenamento de BLOBs é otimizado para armazenar grandes quantidades de dados não estruturados.

Use a biblioteca de cliente do armazenamento de BLOBs do Azure para .NET para:

* Criar um contentor
* Definir permissões em um contêiner
* Criar um blob no armazenamento do Azure
* Baixe o blob em seu computador local
* Listar todos os BLOBs em um contêiner
* Eliminar um contentor

[](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet) | [Exemplos](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob) [do NuGet (pacote de](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/) | [código](https://github.com/Azure/azure-storage-net/tree/master/Blob) | -fonte) da biblioteca de documentação de referência de API

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="required-before-you-begin"></a>Necessário antes de começar

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* Conta de armazenamento do Azure – [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [SDK do NET Core](https://dotnet.microsoft.com/download/dotnet-core), ou posterior, para seu sistema operacional. Certifique-se de obter o SDK e não o tempo de execução.

## <a name="setting-up"></a>Configurando

Esta seção orienta você pela preparação de um projeto para trabalhar com a biblioteca de cliente de armazenamento de BLOBs do Azure para .NET.

### <a name="create-the-project"></a>Criar o projeto

Primeiro, crie um aplicativo .NET Core chamado *blob-QuickStart*.

1. Em uma janela de console (como cmd, PowerShell ou bash), use o `dotnet new` comando para criar um novo aplicativo de console com o nome *blob-QuickStart*. Este comando cria um projeto simples de " C# Olá, mundo" com um único arquivo de origem: *Program.cs*.

   ```console
   dotnet new console -n blob-quickstart
   ```

2. Alterne para a pasta *blob-QuickStart* recém criada e compile o aplicativo para verificar se tudo está bem.

   ```console
   cd blob-quickstart
   ```

   ```console
   dotnet build
   ```

A saída esperada da compilação deve ser semelhante a esta:

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

Ainda no diretório do aplicativo, instale o pacote biblioteca de cliente do armazenamento de BLOBs do Azure para `dotnet add package` .NET usando o comando.

```console
dotnet add package Microsoft.Azure.Storage.Blob
```

### <a name="set-up-the-app-framework"></a>Configurar a estrutura do aplicativo

No diretório do projeto:

1. Abra o arquivo *Program.cs* em seu editor
2. Remover a `Console.WriteLine` instrução
3. Adicionar `using` diretivas
4. Criar um `ProcessAsync` método em que o código principal do exemplo residirá
5. Chamar de forma `ProcessAsync` assíncrona o método de`Main`

Este é o código:

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

Após ter copiado a cadeia de ligação, escreva-a numa nova variável de ambiente no computador local que está a executar a aplicação. Para definir a variável de ambiente, abra uma janela da consola e siga as instruções relevantes para o seu sistema operativo. Substitua `<yourconnectionstring>` pela cadeia de conexão real.

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

Depois de adicionar a variável de ambiente no Windows, você deve iniciar uma nova instância da janela de comando.

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>MacOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

Depois de adicionar a variável de ambiente, reinicie os programas em execução que precisarão ler a variável de ambiente. Por exemplo, reinicie o seu ambiente ou editor de desenvolvimento antes de continuar.

## <a name="object-model"></a>Modelo de objeto

O armazenamento de BLOBs do Azure é otimizado para armazenar grandes quantidades de dados não estruturados. Dados não estruturados são dados que não aderem a um modelo ou definição de dados específico, como texto ou dados binários. O armazenamento de BLOBs oferece três tipos de recursos:

* A conta de armazenamento.
* Um contêiner na conta de armazenamento
* Um blob em um contêiner

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama da arquitetura de armazenamento de BLOBs](./media/storage-quickstart-blobs-dotnet/blob1.png)

Use as seguintes classes .NET para interagir com estes recursos:

* [CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount): A `CloudStorageAccount` classe representa sua conta de armazenamento do Azure. Use essa classe para autorizar o acesso ao armazenamento de BLOBs usando as chaves de acesso da conta.
* [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient): A `CloudBlobClient` classe fornece um ponto de acesso ao serviço blob em seu código.
* [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer): A `CloudBlobContainer` classe representa um contêiner de BLOB em seu código.
* [CloudBlockBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob): O `CloudBlockBlob` objeto representa um blob de blocos em seu código. Os blobs de blocos são constituídos por blocos de dados que podem ser geridos individualmente.

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código de exemplo mostram como executar o seguinte com a biblioteca de cliente de armazenamento de BLOBs do Azure para .NET:

   * [Autenticar o cliente](#authenticate-the-client)
   * [Criar um contêiner](#create-a-container)
   * [Definir permissões em um contêiner](#set-permissions-on-a-container)
   * [Carregar BLOBs em um contêiner](#upload-blobs-to-a-container)
   * [Listar os BLOBs em um contêiner](#list-the-blobs-in-a-container)
   * [Baixar BLOBs](#download-blobs)
   * [Eliminar um contentor](#delete-a-container)

### <a name="authenticate-the-client"></a>Autenticar o cliente

O código a seguir verifica se a variável de ambiente contém uma cadeia de conexão que pode ser analisada para criar um objeto [CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount?view=azure-dotnet) apontando para a conta de armazenamento. Para verificar se a cadeia de ligação é válida, utilize o método [TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse?view=azure-dotnet). Se `TryParse` for bem-sucedido, ele inicializa a `storageAccount` variável e retorna `true`.

Adicione este código dentro do `ProcessAsync` método:

```csharp
// Retrieve the connection string for use with the application. The storage 
// connection string is stored in an environment variable on the machine 
// running the application called CONNECT_STR. If the 
// environment variable is created after the application is launched in a 
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("CONNECT_STR");

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
        "Add an environment variable named 'CONNECT_STR' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the application.");
    Console.ReadLine();
}
```

> [!NOTE]
> Para executar o restante das operações neste artigo, substitua `// ADD OTHER OPERATIONS HERE` no código acima pelos trechos de código nas seções a seguir.

### <a name="create-a-container"></a>Criar um contentor

Para criar o contentor, primeiro crie uma instância do objeto [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient), que aponte para o Armazenamento de blobs na sua conta de armazenamento. Em seguida, crie uma instância do objeto [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) e depois crie o contentor.

Nesse caso, o código chama o método [createasync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync) para criar o contêiner. É anexado um valor GUID ao nome do contentor para garantir que é exclusivo. Em um ambiente de produção, geralmente é preferível usar o método [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync) para criar um contêiner somente se ele ainda não existir.

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

### <a name="set-permissions-on-a-container"></a>Definir permissões em um contêiner

Defina permissões no contêiner para que todos os BLOBs no contêiner sejam públicos. Se um blob for público, pode ser acedido anonimamente por qualquer cliente.

```csharp
// Set the permissions so the blobs are public.
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-a-container"></a>Carregar BLOBs em um contêiner

O trecho de código a seguir obtém uma referência `CloudBlockBlob` a um objeto chamando o método [GetBlockBlobReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getblockblobreference) no contêiner criado na seção anterior. Em seguida, ele carrega o arquivo local selecionado no blob chamando o método [UploadFromFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromfileasync) . Este método cria o blob, caso ainda não exista, ou substitui-o se o mesmo já existir.

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

Liste os BLOBs no contêiner usando o método [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync) . Nesse caso, apenas um blob foi adicionado ao contêiner, portanto, a operação de listagem retorna apenas um blob.

Se houver muitos BLOBs para retornar em uma chamada (por padrão, mais de 5000), o `ListBlobsSegmentedAsync` método retornará um segmento do conjunto de resultados total e um token de continuação. Para obter o segmento seguinte de blobs, indique o token de continuação devolvido pela chamada anterior e assim sucessivamente, até o token de continuação ser nulo. Um token de continuação nulo indica que foram obtidos todos os blobs. O código mostra como usar o token de continuação para fins de práticas recomendadas.

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

Baixe o blob criado anteriormente para o sistema de arquivos local usando o método [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) . O código de exemplo adiciona um sufixo de "_DOWNLOADED" ao nome do blob para que você possa ver ambos os arquivos no sistema de arquivos local.

```csharp
// Download the blob to a local file, using the reference created earlier.
// Append the string "_DOWNLOADED" before the .txt extension so that you 
// can see both files in MyDocuments.
string destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);
```

### <a name="delete-a-container"></a>Eliminar um contentor

O código a seguir limpa os recursos que o aplicativo criou excluindo o contêiner inteiro usando [CloudBlobContainer. DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync). Se quiser, também pode eliminar os ficheiros locais.

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

Este aplicativo cria um arquivo de teste na pasta local MyDocuments e o carrega no armazenamento de BLOBs. Em seguida, o exemplo lista os BLOBs no contêiner e baixa o arquivo com um novo nome para que você possa comparar os arquivos novos e antigos.

Navegue até o diretório do aplicativo e, em seguida, compile e execute o aplicativo.

```console
dotnet build
```

```console
dotnet run
```

A saída do aplicativo de exemplo é semelhante ao exemplo a seguir:

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

Quando premir a tecla **Enter**, a aplicação elimina o contentor de armazenamento e os ficheiros. Antes de os eliminar, procure os dois ficheiros na pasta *MyDocuments*. Pode abri-los e constatar que são idênticos. Copie o URL do blob a partir da janela da consola e cole-o num browser para ver o conteúdo do blob.

Depois de verificar os ficheiros, prima qualquer tecla para concluir a demonstração e eliminar os ficheiros de teste.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu a carregar, transferir e listar blobs com o .NET.

Para saber como criar um aplicativo Web que carregue uma imagem no armazenamento de BLOBs, continue em:

> [!div class="nextstepaction"]
> [Carregar e processar uma imagem](storage-upload-process-images.md)

* Para saber mais sobre o .NET Core, veja [Introdução ao .NET em 10 minutos](https://www.microsoft.com/net/learn/get-started/).
* Para explorar uma aplicação de exemplo que pode implementar a partir do Visual Studio para Windows, veja [Exemplo de Aplicação Web de Galeria de Fotografias do .NET com o Armazenamento de Blobs do Azure](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/).
