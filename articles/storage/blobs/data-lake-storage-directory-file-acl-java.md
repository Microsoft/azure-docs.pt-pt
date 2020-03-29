---
title: Armazenamento de lagos Azure Data Gen2 Java SDK para ficheiros & ACLs
description: Utilize bibliotecas de armazenamento Azure para Java para gerir listas de diretórios e arquivos e listas de controlo de acesso de diretórios (ACL) em contas de armazenamento que tenham espaço hierárquico (HNS) habilitado.
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 45870dd7d3035b6b49340fd6e8016794088e775a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061563"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Use java para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2

Este artigo mostra-lhe como usar java para criar e gerir diretórios, ficheiros e permissões em contas de armazenamento que têm espaço hierárquico (HNS) habilitado. 

[Pacote (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [Amostras](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [Referência API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html) | [Gen1 para gen2 mapeamento](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [Dar Feedback](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tem espaço de nome hierárquico (HNS) ativado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar uma.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Para começar, abra [esta página](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) e encontre a versão mais recente da biblioteca Java. Em seguida, abra o ficheiro *pom.xml* no seu editor de texto. Adicione um elemento de dependência que referencia esta versão.

Se planeia autenticar a sua aplicação de cliente utilizando o Azure Ative Directory (AD), adicione uma dependência à Biblioteca de Clientes Secretos Azure. Ver [Adicionar o pacote secreto da Biblioteca de Clientes ao seu projeto.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project)

Em seguida, adicione estas declarações de importação ao seu ficheiro de código.

```java
import com.azure.core.credential.TokenCredential;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.RolePermissions;
```

## <a name="connect-to-the-account"></a>Ligar à conta 

Para utilizar os cortes neste artigo, terá de criar uma instância **DataLakeServiceClient** que represente a conta de armazenamento. 

### <a name="connect-by-using-an-account-key"></a>Conecte-se utilizando uma chave de conta

Esta é a maneira mais fácil de se ligar a uma conta. 

Este exemplo cria uma instância **DataLakeServiceClient** utilizando uma chave de conta.

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      
```

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Conecte-se utilizando o Diretório Ativo Azure (Azure AD)

Pode utilizar a biblioteca de [clientes de identidade Azure para a Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) autenticar a sua aplicação com a Azure AD.

Este exemplo cria uma instância **DataLakeServiceClient** utilizando um ID do cliente, um segredo de cliente e um ID de inquilino.  Para obter estes valores, consulte [Adquirir um símbolo da Azure AD para autorizar pedidos de uma aplicação de cliente](../common/storage-auth-aad-app.md).

```java
static public DataLakeServiceClient GetDataLakeServiceClient
    (String accountName, String clientId, String ClientSecret, String tenantID){

    String endpoint = "https://" + accountName + ".dfs.core.windows.net";
        
    ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
    .clientId(clientId)
    .clientSecret(ClientSecret)
    .tenantId(tenantID)
    .build();
           
    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();
    return builder.credential(clientSecretCredential).endpoint(endpoint).buildClient();
 } 
```

> [!NOTE]
> Para mais exemplos, consulte a biblioteca de [clientes de identidade Azure para](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) documentação java.


## <a name="create-a-file-system"></a>Criar um sistema de ficheiros

Um sistema de ficheiros funciona como um recipiente para os seus ficheiros. Pode criar um, ligando para o método **DataLakeServiceClient.createFileSystem.**

Este exemplo cria um `my-file-system`sistema de ficheiros chamado . 

```java
static public DataLakeFileSystemClient CreateFileSystem
(DataLakeServiceClient serviceClient){

    return serviceClient.createFileSystem("my-file-system");
}
```

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório, ligando para o método **DataLakeFileSystemClient.createDirectory.**

Este exemplo adiciona um `my-directory` diretório nomeado a um sistema de `my-subdirectory`ficheiros, e depois adiciona um sub-directório nomeado . 

```java
static public DataLakeDirectoryClient CreateDirectory
(DataLakeServiceClient serviceClient, String fileSystemName){
    
    DataLakeFileSystemClient fileSystemClient =
    serviceClient.getFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.createDirectory("my-directory");

    return directoryClient.createSubDirectory("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Mudar o nome ou mover um diretório

Mude o nome ou mova um diretório chamando o método **DataLakeDirectoryClient.rename.** Passe o caminho do diretório desejado um parâmetro. 

Este exemplo renomea um sub-directório para o nome `my-subdirectory-renamed`.

```java
static public DataLakeDirectoryClient
    RenameDirectory(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-subdirectory-renamed");
}
```

Este exemplo move um `my-subdirectory-renamed` diretório nomeado para um sub-directório de um diretório chamado `my-directory-2`. 

```java
static public DataLakeDirectoryClient MoveDirectory
(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory-renamed");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Eliminar um diretório

Elimine um diretório ligando para o método **DataLakeDirectoryClient.deleteWithResponse.**

Este exemplo elimina um `my-directory`diretório chamado .   

```java
static public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient){
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    directoryClient.deleteWithResponse(true, null, null, null);
}
```

## <a name="manage-a-directory-acl"></a>Gerir um diretório ACL

Este exemplo recebe e, em seguida, `my-directory`define o ACL de um diretório chamado . Este exemplo dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo próprio apenas ler e executar permissões, e dá a todos os outros acesso de leitura.

> [!NOTE]
> Se a sua aplicação autorizar o acesso utilizando o Azure Ative Directory (Azure AD), certifique-se de que o responsável pela segurança que a sua aplicação utiliza para autorizar o acesso foi atribuído à função de [Proprietário de Dados blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)de armazenamento . Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de [acesso em Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```java
static public void ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        directoryClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = directoryAccessControl.getAccessControlList();
       
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
             
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);
  
    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);
  
    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);
  
    PathPermissions permissions = new PathPermissions();
  
    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    directoryClient.setPermissions(permissions, null, null);

    pathPermissions = directoryClient.getAccessControl().getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}

```

## <a name="upload-a-file-to-a-directory"></a>Faça upload de um ficheiro para um diretório

Em primeiro lugar, crie uma referência de ficheiro no directório-alvo, criando uma instância da classe **DataLakeFileClient.** Faça upload de um ficheiro ligando para o método **DataLakeFileClient.append.** Certifique-se de completar o upload ligando para o método **DataLakeFileClient.FlushAsync.**

Este exemplo envia um ficheiro de `my-directory`texto para um diretório chamado .'

```java
static public void UploadFile(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.createFile("uploaded-file.txt");

    File file = new File("C:\\mytestfile.txt");

    InputStream targetStream = new FileInputStream(file);

    long fileSize = file.length();

    fileClient.append(targetStream, 0, fileSize);

    fileClient.flush(fileSize);
}
```

> [!TIP]
> Se o tamanho do ficheiro for grande, o seu código terá de fazer várias chamadas para o método **DataLakeFileClient.append.** Considere utilizar o método **DataLakeFileClient.uploadFromFile.** Assim, pode sopor todo o ficheiro numa única chamada. 
>
> Consulte a secção seguinte, por exemplo.

## <a name="upload-a-large-file-to-a-directory"></a>Faça upload de um grande ficheiro para um diretório

Utilize o método **DataLakeFileClient.uploadFromFile** para carregar ficheiros grandes sem ter de fazer várias chamadas para o método **DataLakeFileClient.append.**

```java
static public void UploadFileBulk(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.getFileClient("uploaded-file.txt");

    fileClient.uploadFromFile("C:\\mytestfile.txt");

    }

```


## <a name="manage-a-file-acl"></a>Gerir um ficheiro ACL

Este exemplo recebe e, em seguida, `upload-file.txt`define o ACL de um ficheiro chamado . Este exemplo dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo próprio apenas ler e executar permissões, e dá a todos os outros acesso de leitura.

> [!NOTE]
> Se a sua aplicação autorizar o acesso utilizando o Azure Ative Directory (Azure AD), certifique-se de que o responsável pela segurança que a sua aplicação utiliza para autorizar o acesso foi atribuído à função de [Proprietário de Dados blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)de armazenamento . Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de [acesso em Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```java
static public void ManageFileACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    PathAccessControl fileAccessControl =
        fileClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = fileAccessControl.getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
           
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);

    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);

    PathPermissions permissions = new PathPermissions();

    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    fileClient.setPermissions(permissions, null, null);

    pathPermissions = fileClient.getAccessControl().getAccessControlList();
   
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}
```

## <a name="download-from-a-directory"></a>Baixar de um diretório

Em primeiro lugar, crie uma instância **DataLakeFileClient** que represente o ficheiro que pretende descarregar. Utilize o método **DataLakeFileClient.read** para ler o ficheiro. Utilize qualquer API de processamento de ficheiros .NET para guardar bytes do fluxo para um ficheiro. 

```java
static public void DownloadFile(DataLakeFileSystemClient fileSystemClient)
    throws FileNotFoundException, java.io.IOException{

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    File file = new File("C:\\downloadedFile.txt");

    OutputStream targetStream = new FileOutputStream(file);

    fileClient.read(targetStream);

    targetStream.close();
      
}

```

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Este exemplo, imprime os nomes de cada ficheiro `my-directory`que está localizado num diretório chamado .

```java
static public void ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient){
        
    ListPathsOptions options = new ListPathsOptions();
    options.setPath("my-directory");
     
    PagedIterable<PathItem> pagedIterable = 
    fileSystemClient.listPaths(options, null);

    java.util.Iterator<PathItem> iterator = pagedIterable.iterator();
       
    PathItem item = iterator.next();

    while (item != null)
    {
        System.out.println(item.getName());


        if (!iterator.hasNext())
        {
            break;
        }
            
        item = iterator.next();
    }

}
```

## <a name="see-also"></a>Consulte também

* [Documentação de referência da API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html)
* [Pacote (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [Amostras](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Gen1 para mapeamento Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Dar Feedback](https://github.com/Azure/azure-sdk-for-java/issues)