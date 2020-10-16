---
title: Azure Data Lake Storage Gen2 Java SDK para ficheiros & ACLs
description: Utilize bibliotecas de armazenamento Azure para Java para gerir diretórios e listas de controlo de acesso a ficheiros e diretórios (ACL) em contas de armazenamento que tenham espaço hierárquico (HNS) habilitado.
author: normesta
ms.service: storage
ms.date: 09/10/2020
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: f3ab2a19179c80765ba69e748b1421caae200fb8
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92101244"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Use a Java para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2

Este artigo mostra-lhe como usar Java para criar e gerir diretórios, ficheiros e permissões em contas de armazenamento que têm espaço hierárquico (HNS) ativado. 

[Pacote (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  |  [Amostras](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  |  Referência API [API reference](/java/api/overview/azure/storage-file-datalake-readme)  |  Mapeamento da [Gen1 para a Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Dar feedback](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tem espaço hierárquico de nome (HNS) ativado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar uma.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Para começar, abra [esta página](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) e encontre a versão mais recente da biblioteca Java. Em seguida, abra o ficheiro *pom.xml* no seu editor de texto. Adicione um elemento de dependência que faz referência a essa versão.

Se planeia autenticar a sua aplicação de cliente utilizando o Azure Ative Directory (AD), adicione uma dependência à Biblioteca de Clientes Secretos Azure. Consulte [a adição do pacote secret Client Library ao seu projeto.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project)

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

Para utilizar os snippets neste artigo, terá de criar uma instância **DataLakeServiceClient** que represente a conta de armazenamento. 

### <a name="connect-by-using-an-account-key"></a>Conecte-se usando uma chave de conta

Esta é a maneira mais fácil de ligar a uma conta. 

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

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Conecte-se utilizando o Azure Ative Directory (Azure AD)

Pode utilizar a biblioteca de [clientes de identidade Azure para a Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) para autenticar a sua aplicação com Azure AD.

Este exemplo cria um exemplo **de DataLakeServiceClient** usando um ID de cliente, um segredo de cliente e uma identificação de inquilino.  Para obter estes valores, consulte [Adquirir um token da Azure AD para autorizar pedidos de uma aplicação do cliente.](../common/storage-auth-aad-app.md)

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


## <a name="create-a-container"></a>Criar um contentor

Um contentor funciona como um sistema de ficheiros para os seus ficheiros. Pode criar um chamando o método **DataLakeServiceClient.createFileSystem.**

Este exemplo cria um recipiente chamado `my-file-system` . 

```java
static public DataLakeFileSystemClient CreateFileSystem
(DataLakeServiceClient serviceClient){

    return serviceClient.createFileSystem("my-file-system");
}
```

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório chamando o método **DataLakeFileSystemClient.createDirectory.**

Este exemplo adiciona um diretório nomeado `my-directory` a um contentor e, em seguida, adiciona um sub-diretório chamado `my-subdirectory` . 

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

## <a name="rename-or-move-a-directory"></a>Mudar de nome ou mover um diretório

Mude o nome ou mova um diretório chamando o método **DataLakeDirectoryClient.rename.** Passe o caminho do diretório desejado um parâmetro. 

Este exemplo renomea um sub-diretório para o nome `my-subdirectory-renamed` .

```java
static public DataLakeDirectoryClient
    RenameDirectory(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-subdirectory-renamed");
}
```

Este exemplo move um diretório nomeado `my-subdirectory-renamed` para um sub-diretório de um diretório chamado `my-directory-2` . 

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

Elimine um diretório chamando o método **DataLakeDirectoryClient.deleteWithResponse.**

Este exemplo elimina um diretório chamado `my-directory` .   

```java
static public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient){
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    directoryClient.deleteWithResponse(true, null, null, null);
}
```

## <a name="manage-a-directory-acl"></a>Gerir um diretório ACL

Este exemplo recebe e, em seguida, define o ACL de um diretório chamado `my-directory` . Este exemplo dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo que só lê e executa permissões, e dá a todos os outros acessos de leitura.

> [!NOTE]
> Se a sua aplicação autorizar o acesso utilizando o Azure Ative Directory (Azure AD), certifique-se de que o principal de segurança que a sua aplicação utiliza para autorizar o acesso foi atribuído à [função de Proprietário de Dados blob de armazenamento.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de  [acesso na Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

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

Você também pode obter e definir o ACL do diretório de raiz de um recipiente. Para obter o diretório de raiz, passe uma corda vazia `""` () para o método **DataLakeFileSystemClient.getDirectoryClient.**

## <a name="upload-a-file-to-a-directory"></a>Faça o upload de um ficheiro para um diretório

Em primeiro lugar, crie uma referência de ficheiro no directório-alvo criando uma instância da classe **DataLakeFileClient.** Faça o upload de um ficheiro ligando para o método **DataLakeFileClient.append.** Certifique-se de completar o upload ligando para o método **DataLakeFileClient.FlushAsync.**

Este exemplo envia um ficheiro de texto para um diretório chamado `my-directory` .'

```java
static public void UploadFile(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.createFile("uploaded-file.txt");

    File file = new File("C:\\mytestfile.txt");

    InputStream targetStream = new BufferedInputStream(new FileInputStream(file));

    long fileSize = file.length();

    fileClient.append(targetStream, 0, fileSize);

    fileClient.flush(fileSize);
}
```

> [!TIP]
> Se o tamanho do seu ficheiro for grande, o seu código terá de escoar várias chamadas para o método **DataLakeFileClient.append.** Considere usar o método **DataLakeFileClient.uploadFromFile.** Assim, podes carregar o ficheiro inteiro numa única chamada. 
>
> Consulte a secção seguinte para dar um exemplo.

## <a name="upload-a-large-file-to-a-directory"></a>Faça upload de um ficheiro grande para um diretório

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

Este exemplo recebe e, em seguida, define o ACL de um ficheiro chamado `upload-file.txt` . Este exemplo dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo que só lê e executa permissões, e dá a todos os outros acessos de leitura.

> [!NOTE]
> Se a sua aplicação autorizar o acesso utilizando o Azure Ative Directory (Azure AD), certifique-se de que o principal de segurança que a sua aplicação utiliza para autorizar o acesso foi atribuído à [função de Proprietário de Dados blob de armazenamento.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de  [acesso na Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

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

## <a name="download-from-a-directory"></a>Descarregue a partir de um diretório

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

Este exemplo, imprime os nomes de cada ficheiro que está localizado num diretório denominado `my-directory` .

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

## <a name="see-also"></a>Ver também

* [Documentação de referência da API](/java/api/overview/azure/storage-file-datalake-readme)
* [Pacote (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [Amostras](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Mapeamento da Gen1 para a Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Dar feedback](https://github.com/Azure/azure-sdk-for-java/issues)