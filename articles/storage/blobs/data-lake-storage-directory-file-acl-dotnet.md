---
title: Azure Data Lake Storage Gen2 .NET SDK para ficheiros & ACLs
description: Utilize a biblioteca de clientes Azure Storage para gerir diretórios e listas de controlo de acesso de ficheiros e diretórios (ACL) em contas de armazenamento que tenham espaço hierárquico (HNS) habilitado.
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: b83d0d2d765b60585832f1a3e7c610f05eac075c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061571"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Use .NET para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2

Este artigo mostra-lhe como usar .NET para criar e gerir diretórios, ficheiros e permissões em contas de armazenamento que têm espaço hierárquico (HNS) habilitado. 

[Pacote (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [Amostras](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [Referência API](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake) | [Gen1 para gen2 mapeamento](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [Dar Feedback](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tem espaço de nome hierárquico (HNS) ativado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar uma.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Para começar, instale o pacote [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet.

Para obter mais informações sobre como instalar pacotes NuGet, consulte [Instalar e gerir pacotes no Estúdio Visual utilizando o NuGet Package Manager](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio).

Em seguida, adicione-as usando declarações no topo do seu ficheiro de código.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>Ligar à conta

Para utilizar os cortes neste artigo, terá de criar uma instância [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) que represente a conta de armazenamento. 

### <a name="connect-by-using-an-account-key"></a>Conecte-se utilizando uma chave de conta

Esta é a maneira mais fácil de se ligar a uma conta. 

Este exemplo cria uma instância [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) utilizando uma chave de conta.

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

### <a name="connect-by-using-azure-active-directory-ad"></a>Conecte-se utilizando o Diretório Ativo Azure (AD)

Pode utilizar a biblioteca de [clientes de identidade Azure para .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) autenticar a sua aplicação com a Azure AD.

Este exemplo cria uma instância [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) utilizando um ID do cliente, um segredo de cliente e um ID de inquilino.  Para obter estes valores, consulte [Adquirir um símbolo da Azure AD para autorizar pedidos de uma aplicação de cliente](../common/storage-auth-aad-app.md).

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

```

> [!NOTE]
> Para mais exemplos, consulte a biblioteca de [clientes de identidade Azure para obter documentação .NET..](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)

## <a name="create-a-file-system"></a>Criar um sistema de ficheiros

Um sistema de ficheiros funciona como um recipiente para os seus ficheiros. Pode criar um, ligando para o método [DataLakeServiceClient.CreateFileSystem.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync)

Este exemplo cria um `my-file-system`sistema de ficheiros chamado . 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório, ligando para o método [DataLakeFileSystemClient.CreateDirectoryAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync)

Este exemplo adiciona um `my-directory` diretório nomeado a um sistema de `my-subdirectory`ficheiros, e depois adiciona um sub-directório nomeado . 

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

## <a name="rename-or-move-a-directory"></a>Mudar o nome ou mover um diretório

Mude o nome ou mova um diretório ligando para o método [DataLakeDirectoryClient.RenameAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) Passe o caminho do diretório desejado um parâmetro. 

Este exemplo renomea um sub-directório para o nome `my-subdirectory-renamed`.

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

Este exemplo move um `my-subdirectory-renamed` diretório nomeado para um sub-directório de um diretório chamado `my-directory-2`. 

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

Elimine um diretório ligando para o método [DataLakeDirectoryClient.Delete.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete)

Este exemplo elimina um `my-directory`diretório chamado .  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>Gerir um diretório ACL

Obtenha a lista de controlo de acesso (ACL) de um diretório, ligando para o método [DataLakeDirectoryClient.GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) e definir o ACL, ligando para o método [DataLakeDirectoryClient.SetAccessControlList.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist)

> [!NOTE]
> Se a sua aplicação autorizar o acesso utilizando o Azure Ative Directory (Azure AD), certifique-se de que o responsável pela segurança que a sua aplicação utiliza para autorizar o acesso foi atribuído à função de [Proprietário de Dados blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)de armazenamento . Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de [acesso em Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Este exemplo recebe e define o ACL `my-directory`de um diretório chamado . A `user::rwx,group::r-x,other::rw-` corda dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo próprio apenas ler e executar permissões, e dá a todos os outros ler e escrever permissão.

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    Console.WriteLine(directoryAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    directoryClient.SetAccessControlList(accessControlList);

}

```

## <a name="upload-a-file-to-a-directory"></a>Faça upload de um ficheiro para um diretório

Em primeiro lugar, crie uma referência de ficheiro no directório-alvo, criando uma instância da classe [DataLakeFileClient.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) Faça upload de um ficheiro ligando para o método [DataLakeFileClient.AppendAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) Certifique-se de completar o upload ligando para o método [DataLakeFileClient.FlushAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync)

Este exemplo envia um ficheiro de `my-directory`texto para um diretório chamado .    

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
> Se o tamanho do ficheiro for grande, o seu código terá de fazer várias chamadas para o [DataLakeFileClient.AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). Considere utilizar o método [DataLakeFileClient.UploadAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) Assim, pode sopor todo o ficheiro numa única chamada. 
>
> Consulte a secção seguinte, por exemplo.

## <a name="upload-a-large-file-to-a-directory"></a>Faça upload de um grande ficheiro para um diretório

Utilize o método [DataLakeFileClient.UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) para carregar ficheiros grandes sem ter de fazer várias chamadas para o método [DataLakeFileClient.AppendAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync)

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

## <a name="manage-a-file-acl"></a>Gerir um ficheiro ACL

Obtenha a lista de controlo de acesso (ACL) de um ficheiro, ligando para o método [DataLakeFileClient.GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) e definir o ACL, ligando para o método [DataLakeFileClient.SetAccessControlList.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist)

> [!NOTE]
> Se a sua aplicação autorizar o acesso utilizando o Azure Ative Directory (Azure AD), certifique-se de que o responsável pela segurança que a sua aplicação utiliza para autorizar o acesso foi atribuído à função de [Proprietário de Dados blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)de armazenamento . Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de [acesso em Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Este exemplo recebe e define o `my-file.txt`ACL de um ficheiro chamado . A `user::rwx,group::r-x,other::rw-` corda dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo próprio apenas ler e executar permissões, e dá a todos os outros ler e escrever permissão.

```cs
public async Task ManageFileACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("hello.txt");

    PathAccessControl FileAccessControl =
        await fileClient.GetAccessControlAsync();

    Console.WriteLine(FileAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    fileClient.SetAccessControlList(accessControlList);
}
```

## <a name="download-from-a-directory"></a>Baixar de um diretório 

Em primeiro lugar, crie uma instância [DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) que represente o ficheiro que pretende descarregar. Utilize o método [DataLakeFileClient.ReadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) e analise o valor de devolução para obter um objeto [Stream.](https://docs.microsoft.com/dotnet/api/system.io.stream) Utilize qualquer API de processamento de ficheiros .NET para guardar bytes do fluxo para um ficheiro. 

Este exemplo utiliza um [BinaryReader](https://docs.microsoft.com/dotnet/api/system.io.binaryreader) e um [FileStream](https://docs.microsoft.com/dotnet/api/system.io.filestream) para guardar bytes para um ficheiro. 

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

Listar conteúdos de diretório, ligando para o método [FileSystemClient.GetPathsAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) e, em seguida, enumerando através dos resultados.

Este exemplo, imprime os nomes de cada ficheiro `my-directory`que está localizado num diretório chamado .

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

## <a name="see-also"></a>Consulte também

* [Documentação de referência da API](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake)
* [Pacote (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
* [Amostras](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Gen1 para mapeamento Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Dar Feedback](https://github.com/Azure/azure-sdk-for-net/issues)

