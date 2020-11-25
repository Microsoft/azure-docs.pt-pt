---
title: Azure Data Lake Storage Gen2 .NET SDK para ficheiros & ACLs
description: Utilize a biblioteca de clientes Azure Storage para gerir diretórios e listas de controlo de acesso a ficheiros e diretórios (ACL) em contas de armazenamento que tenham espaço hierárquico (HNS) habilitado.
author: normesta
ms.service: storage
ms.date: 08/26/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 01f23abe3ef06bc43a3f7043f48b75f684a4478e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913475"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Utilize .NET para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2

Este artigo mostra-lhe como usar .NET para criar e gerir diretórios, ficheiros e permissões em contas de armazenamento que têm espaço hierárquico (HNS) ativado. 

[Pacote (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  [Amostras](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  Referência API [API reference](/dotnet/api/azure.storage.files.datalake)  |  Mapeamento da [Gen1 para a Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [Dar feedback](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tem espaço hierárquico de nome (HNS) ativado. Siga [estas](../common/storage-account-create.md) instruções para criar uma.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Para começar, instale o pacote [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet.

Para obter mais informações sobre como instalar pacotes NuGet, consulte [instalar e gerir pacotes em Estúdio Visual utilizando o Gestor de Pacotes NuGet](/nuget/consume-packages/install-use-packages-visual-studio).

Em seguida, adicione estas declarações usando o topo do seu ficheiro de código.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>Ligar à conta

Para utilizar os snippets neste artigo, terá de criar uma instância [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) que represente a conta de armazenamento. 

### <a name="connect-by-using-an-account-key"></a>Conecte-se usando uma chave de conta

Esta é a maneira mais fácil de ligar a uma conta. 

Este exemplo cria uma instância [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) utilizando uma chave de conta.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

### <a name="connect-by-using-azure-active-directory-ad"></a>Conecte-se utilizando o Azure Ative Directory (AD)

Pode utilizar a biblioteca de [clientes de identidade Azure para .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) para autenticar a sua aplicação com Azure AD.

Este exemplo cria um exemplo [de DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) usando um ID de cliente, um segredo de cliente e uma identificação de inquilino.  Para obter estes valores, consulte [Adquirir um token da Azure AD para autorizar pedidos de uma aplicação do cliente.](../common/storage-auth-aad-app.md)

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    string accountName, string clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

```

> [!NOTE]
> Para mais exemplos, consulte a biblioteca do [cliente de identidade Azure para](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) obter documentação .NET..

## <a name="create-a-container"></a>Criar um contentor

Um contentor funciona como um sistema de ficheiros para os seus ficheiros. Pode criar um chamando o método [DataLakeServiceClient.CreateFileSystem.](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync)

Este exemplo cria um recipiente chamado `my-file-system` . 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório chamando o método [DataLakeFileSystemClient.CreateDirectoryAsync.](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync)

Este exemplo adiciona um diretório nomeado `my-directory` a um contentor e, em seguida, adiciona um sub-diretório chamado `my-subdirectory` . 

```cs
public async Task<DataLakeDirectoryClient> CreateDirectory
    (DataLakeServiceClient serviceClient, string fileSystemName)
{
    DataLakeFileSystemClient fileSystemClient =
        serviceClient.GetFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        await fileSystemClient.CreateDirectoryAsync("my-directory");

    return await directoryClient.CreateSubDirectoryAsync("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Mudar de nome ou mover um diretório

Mude o nome ou mova um diretório chamando o método [DataLakeDirectoryClient.RenameAsync.](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) Passe o caminho do diretório desejado um parâmetro. 

Este exemplo renomea um sub-diretório para o nome `my-subdirectory-renamed` .

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

Este exemplo move um diretório nomeado `my-subdirectory-renamed` para um sub-diretório de um diretório chamado `my-directory-2` . 

```cs
public async Task<DataLakeDirectoryClient> MoveDirectory
    (DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
            fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory-renamed");

    return await directoryClient.RenameAsync("my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Eliminar um diretório

Elimine um diretório chamando o método [DataLakeDirectoryClient.Delete.](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete)

Este exemplo elimina um diretório chamado `my-directory` .  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="upload-a-file-to-a-directory"></a>Faça o upload de um ficheiro para um diretório

Em primeiro lugar, crie uma referência de ficheiro no directório-alvo criando uma instância da classe [DataLakeFileClient.](/dotnet/api/azure.storage.files.datalake.datalakefileclient) Faça o upload de um ficheiro ligando para o método [DataLakeFileClient.AppendAsync.](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) Certifique-se de completar o upload ligando para o método [DataLakeFileClient.FlushAsync.](/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync)

Este exemplo envia um ficheiro de texto para um diretório chamado `my-directory` .    

```cs
public async Task UploadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = await directoryClient.CreateFileAsync("uploaded-file.txt");

    FileStream fileStream = 
        File.OpenRead("C:\\file-to-upload.txt");

    long fileSize = fileStream.Length;

    await fileClient.AppendAsync(fileStream, offset: 0);

    await fileClient.FlushAsync(position: fileSize);

}
```

> [!TIP]
> Se o tamanho do seu ficheiro for grande, o seu código terá de escoar várias chamadas para o [DataLakeFileClient.AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). Considere usar o método [DataLakeFileClient.UploadAsync.](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) Assim, podes carregar o ficheiro inteiro numa única chamada. 
>
> Consulte a secção seguinte para dar um exemplo.

## <a name="upload-a-large-file-to-a-directory"></a>Faça upload de um ficheiro grande para um diretório

Utilize o método [DataLakeFileClient.UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) para carregar ficheiros grandes sem ter de fazer várias chamadas para o método [DataLakeFileClient.AppendAsync.](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync)

```cs
public async Task UploadFileBulk(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.GetFileClient("uploaded-file.txt");

    FileStream fileStream =
        File.OpenRead("C:\\file-to-upload.txt");

    await fileClient.UploadAsync(fileStream);

}

```

## <a name="download-from-a-directory"></a>Descarregue a partir de um diretório 

Em primeiro lugar, crie uma instância [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) que represente o ficheiro que pretende descarregar. Utilize o método [DataLakeFileClient.ReadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) e analise o valor de devolução para obter um objeto [Stream.](/dotnet/api/system.io.stream) Utilize qualquer API de processamento de ficheiros .NET para guardar bytes do fluxo para um ficheiro. 

Este exemplo utiliza um [BinaryReader](/dotnet/api/system.io.binaryreader) e um [FileStream](/dotnet/api/system.io.filestream) para guardar bytes num ficheiro. 

```cs
public async Task DownloadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("my-image.png");

    Response<FileDownloadInfo> downloadResponse = await fileClient.ReadAsync();

    BinaryReader reader = new BinaryReader(downloadResponse.Value.Content);

    FileStream fileStream = 
        File.OpenWrite("C:\\my-image-downloaded.png");

    int bufferSize = 4096;

    byte[] buffer = new byte[bufferSize];

    int count;

    while ((count = reader.Read(buffer, 0, buffer.Length)) != 0)
    {
        fileStream.Write(buffer, 0, count);
    }

    await fileStream.FlushAsync();

    fileStream.Close();
}
```

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Liste os conteúdos do diretório chamando o método [FileSystemClient.GetPathsAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) e, em seguida, enumerando através dos resultados.

Este exemplo, imprime os nomes de cada ficheiro que está localizado num diretório denominado `my-directory` .

```cs
public async Task ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient)
{
    IAsyncEnumerator<PathItem> enumerator = 
        fileSystemClient.GetPathsAsync("my-directory").GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    PathItem item = enumerator.Current;

    while (item != null)
    {
        Console.WriteLine(item.Name);

        if (!await enumerator.MoveNextAsync())
        {
            break;
        }
                
        item = enumerator.Current;
    }

}
```

## <a name="manage-access-control-lists-acls"></a>Gerir listas de controlo de acesso (ACLs)

Pode obter, definir e atualizar permissões de acesso de diretórios e ficheiros.

> [!NOTE]
> Se estiver a utilizar o Azure Ative Directory (Azure AD) para autorizar o acesso, certifique-se de que o seu principal de segurança foi atribuído à [função de Proprietário de Dados blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de  [acesso na Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="manage-a-directory-acl"></a>Gerir um diretório ACL

Obtenha a lista de controlo de acesso (ACL) de um diretório, ligando para o método [DataLakeDirectoryClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) e definir o ACL chamando o método [DataLakeDirectoryClient.SetAccessControlList.](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist)

> [!NOTE]
> Se a sua aplicação autorizar o acesso utilizando o Azure Ative Directory (Azure AD), certifique-se de que o principal de segurança que a sua aplicação utiliza para autorizar o acesso foi atribuído à [função de Proprietário de Dados blob de armazenamento.](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de  [acesso na Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md). 

Este exemplo recebe e define o ACL de um diretório chamado `my-directory` . A cadeia `user::rwx,group::r-x,other::rw-` dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo que só lê e executa permissões, e dá a todos os outros a leitura e a escrita permissão.

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    foreach (var item in directoryAccessControl.AccessControlList)
    {
        Console.WriteLine(item.ToString());
    }


    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    directoryClient.SetAccessControlList(accessControlList);

}

```

Você também pode obter e definir o ACL do diretório de raiz de um recipiente. Para obter o diretório de raiz, passe uma corda vazia `""` () para o método [DataLakeFileSystemClient.GetDirectoryClient.](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getdirectoryclient)

### <a name="manage-a-file-acl"></a>Gerir um ficheiro ACL

Obtenha a lista de controlo de acesso (ACL) de um ficheiro, ligando para o método [DataLakeFileClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) e de definição do método ACL, chamando o método [DataLakeFileClient.SetAccessControlList.](/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist)

> [!NOTE]
> Se a sua aplicação autorizar o acesso utilizando o Azure Ative Directory (Azure AD), certifique-se de que o principal de segurança que a sua aplicação utiliza para autorizar o acesso foi atribuído à [função de Proprietário de Dados blob de armazenamento.](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de  [acesso na Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md). 

Este exemplo recebe e define o ACL de um ficheiro chamado `my-file.txt` . A cadeia `user::rwx,group::r-x,other::rw-` dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo que só lê e executa permissões, e dá a todos os outros a leitura e a escrita permissão.

```cs
public async Task ManageFileACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("hello.txt");

    PathAccessControl FileAccessControl =
        await fileClient.GetAccessControlAsync();

    foreach (var item in FileAccessControl.AccessControlList)
    {
        Console.WriteLine(item.ToString());
    }

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    fileClient.SetAccessControlList(accessControlList);
}
```

### <a name="set-an-acl-recursively"></a>Desaprote um ACL de forma recursiva

Pode adicionar, atualizar e remover ACLs de forma recorrente nos itens infantis existentes de um diretório de pais sem ter de escamar estas alterações individualmente para cada item infantil. Para obter mais informações, consulte [as listas de controlo de acesso (ACLs) de forma recorrente para Azure Data Lake Storage Gen2](recursive-access-control-lists.md).

## <a name="see-also"></a>Ver também

* [Documentação de referência da API](/dotnet/api/azure.storage.files.datalake)
* [Pacote (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
* [Amostras](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Mapeamento da Gen1 para a Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Dar feedback](https://github.com/Azure/azure-sdk-for-net/issues)