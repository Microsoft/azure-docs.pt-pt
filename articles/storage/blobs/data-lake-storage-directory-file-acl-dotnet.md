---
title: Utilize .NET para gerir dados em Azure Data Lake Storage Gen2
description: Utilize a biblioteca de clientes Azure Storage para .NET para gerir diretórios e ficheiros em contas de armazenamento que têm espaço hierárquico ativado.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 52e993a22a512a94c8b5b8b050205db0c4ce0b1b
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650325"
---
# <a name="use-net-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Utilize .NET para gerir diretórios e ficheiros em Azure Data Lake Storage Gen2

Este artigo mostra-lhe como usar .NET para criar e gerir diretórios e ficheiros em contas de armazenamento que têm um espaço hierárquico de nomes.

Para saber como obter, definir e atualizar as listas de controlo de acesso (ACL) de diretórios e ficheiros, consulte [Use .NET para gerir ACLs em Azure Data Lake Storage Gen2](data-lake-storage-acl-dotnet.md).

[Pacote (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  [Amostras](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  Referência API [](/dotnet/api/azure.storage.files.datalake)  |  Mapeamento da [Gen1 para a Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [Dar feedback](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento que tem espaço hierárquico habilitado. Siga [estas](create-data-lake-storage-account.md) instruções para criar uma.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Para começar, instale o pacote [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet.

Para obter mais informações sobre como instalar pacotes NuGet, consulte [instalar e gerir pacotes em Estúdio Visual utilizando o Gestor de Pacotes NuGet](/nuget/consume-packages/install-use-packages-visual-studio).

Em seguida, adicione estas declarações usando o topo do seu ficheiro de código.

```csharp
using Azure;
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;

```

## <a name="connect-to-the-account"></a>Ligar à conta

Para utilizar os snippets neste artigo, terá de criar uma instância [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) que represente a conta de armazenamento. 

### <a name="connect-by-using-an-account-key"></a>Conecte-se usando uma chave de conta

Esta é a maneira mais fácil de ligar a uma conta. 

Este exemplo cria uma instância [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) utilizando uma chave de conta.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Conecte-se utilizando o Azure Ative Directory (Azure AD)

Pode utilizar a biblioteca de [clientes de identidade Azure para .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) para autenticar a sua aplicação com Azure AD.

Este exemplo cria um exemplo [de DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) usando um ID de cliente, um segredo de cliente e uma identificação de inquilino.  Para obter estes valores, consulte [Adquirir um token da Azure AD para autorizar pedidos de uma aplicação do cliente.](../common/storage-auth-aad-app.md)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Para mais exemplos, consulte a biblioteca do [cliente de identidade Azure para](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) obter documentação .NET..

## <a name="create-a-container"></a>Criar um contentor

Um contentor funciona como um sistema de ficheiros para os seus ficheiros. Pode criar um chamando o método [DataLakeServiceClient.CreateFileSystem.](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync)

Este exemplo cria um recipiente chamado `my-file-system` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório chamando o método [DataLakeFileSystemClient.CreateDirectoryAsync.](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync)

Este exemplo adiciona um diretório nomeado `my-directory` a um contentor e, em seguida, adiciona um sub-diretório chamado `my-subdirectory` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Mudar de nome ou mover um diretório

Mude o nome ou mova um diretório chamando o método [DataLakeDirectoryClient.RenameAsync.](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) Passe o caminho do diretório desejado um parâmetro. 

Este exemplo renomea um sub-diretório para o nome `my-subdirectory-renamed` .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_RenameDirectory":::

Este exemplo move um diretório nomeado `my-subdirectory-renamed` para um sub-diretório de um diretório chamado `my-directory-2` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>Eliminar um diretório

Elimine um diretório chamando o método [DataLakeDirectoryClient.Delete.](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete)

Este exemplo elimina um diretório chamado `my-directory` .  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Faça o upload de um ficheiro para um diretório

Em primeiro lugar, crie uma referência de ficheiro no directório-alvo criando uma instância da classe [DataLakeFileClient.](/dotnet/api/azure.storage.files.datalake.datalakefileclient) Faça o upload de um ficheiro ligando para o método [DataLakeFileClient.AppendAsync.](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) Certifique-se de completar o upload ligando para o método [DataLakeFileClient.FlushAsync.](/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync)

Este exemplo envia um ficheiro de texto para um diretório chamado `my-directory` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFile":::

> [!TIP]
> Se o tamanho do seu ficheiro for grande, o seu código terá de escoar várias chamadas para o [DataLakeFileClient.AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). Considere usar o método [DataLakeFileClient.UploadAsync.](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) Assim, podes carregar o ficheiro inteiro numa única chamada.
>
> Consulte a secção seguinte para dar um exemplo.

## <a name="upload-a-large-file-to-a-directory"></a>Faça upload de um ficheiro grande para um diretório

Utilize o método [DataLakeFileClient.UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) para carregar ficheiros grandes sem ter de fazer várias chamadas para o método [DataLakeFileClient.AppendAsync.](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Descarregue a partir de um diretório 

Em primeiro lugar, crie uma instância [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) que represente o ficheiro que pretende descarregar. Utilize o método [DataLakeFileClient.ReadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) e analise o valor de devolução para obter um objeto [Stream.](/dotnet/api/system.io.stream) Utilize qualquer API de processamento de ficheiros .NET para guardar bytes do fluxo para um ficheiro. 

Este exemplo utiliza um [BinaryReader](/dotnet/api/system.io.binaryreader) e um [FileStream](/dotnet/api/system.io.filestream) para guardar bytes num ficheiro. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DownloadBinaryFromDirectory":::

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Liste os conteúdos do diretório chamando o método [FileSystemClient.GetPathsAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) e, em seguida, enumerando através dos resultados.

Este exemplo, imprime os nomes de cada ficheiro que está localizado num diretório denominado `my-directory` .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Ver também

- [Documentação de referência da API](/dotnet/api/azure.storage.files.datalake)
- [Pacote (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [Amostras](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Mapeamento da Gen1 para a Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Dar feedback](https://github.com/Azure/azure-sdk-for-net/issues)