---
title: Azure Data Lake Storage Gen2 Java SDK para ficheiros & ACLs
description: Utilize bibliotecas de armazenamento Azure para Java para gerir diretórios e listas de controlo de acesso a ficheiros e diretórios (ACL) em contas de armazenamento que tenham espaço hierárquico (HNS) habilitado.
author: normesta
ms.service: storage
ms.date: 01/11/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 1cc6954569c509c977634a8e1cdd52c5c55b2100
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108132"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Use a Java para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2

Este artigo mostra-lhe como usar Java para criar e gerir diretórios, ficheiros e permissões em contas de armazenamento que têm espaço hierárquico (HNS) ativado. 

[Pacote (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  |  [Amostras](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  |  Referência API [](/java/api/overview/azure/storage-file-datalake-readme)  |  Mapeamento da [Gen1 para a Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Dar feedback](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tem espaço hierárquico de nome (HNS) ativado. Siga [estas](../common/storage-account-create.md) instruções para criar uma.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Para começar, abra [esta página](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) e encontre a versão mais recente da biblioteca Java. Em seguida, abra o ficheiro *pom.xml* no seu editor de texto. Adicione um elemento de dependência que faz referência a essa versão.

Se planeia autenticar a sua aplicação de cliente utilizando o Azure Ative Directory (AD), adicione uma dependência à Biblioteca de Clientes Secretos Azure. Consulte [a adição do pacote secret Client Library ao seu projeto.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project)

Em seguida, adicione estas declarações de importação ao seu ficheiro de código.

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.AccessControlChangeCounters;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

## <a name="connect-to-the-account"></a>Ligar à conta 

Para utilizar os snippets neste artigo, terá de criar uma instância **DataLakeServiceClient** que represente a conta de armazenamento. 

### <a name="connect-by-using-an-account-key"></a>Conecte-se usando uma chave de conta

Esta é a maneira mais fácil de ligar a uma conta. 

Este exemplo cria uma instância **DataLakeServiceClient** utilizando uma chave de conta.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Conecte-se utilizando o Azure Ative Directory (Azure AD)

Pode utilizar a biblioteca de [clientes de identidade Azure para a Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) para autenticar a sua aplicação com Azure AD.

Este exemplo cria um exemplo **de DataLakeServiceClient** usando um ID de cliente, um segredo de cliente e uma identificação de inquilino.  Para obter estes valores, consulte [Adquirir um token da Azure AD para autorizar pedidos de uma aplicação do cliente.](../common/storage-auth-aad-app.md)

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> Para mais exemplos, consulte a biblioteca de [clientes de identidade Azure para](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) documentação java.


## <a name="create-a-container"></a>Criar um contentor

Um contentor funciona como um sistema de ficheiros para os seus ficheiros. Pode criar um chamando o método **DataLakeServiceClient.createFileSystem.**

Este exemplo cria um recipiente chamado `my-file-system` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateFileSystem":::

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório chamando o método **DataLakeFileSystemClient.createDirectory.**

Este exemplo adiciona um diretório nomeado `my-directory` a um contentor e, em seguida, adiciona um sub-diretório chamado `my-subdirectory` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Mudar de nome ou mover um diretório

Mude o nome ou mova um diretório chamando o método **DataLakeDirectoryClient.rename.** Passe o caminho do diretório desejado um parâmetro. 

Este exemplo renomea um sub-diretório para o nome `my-subdirectory-renamed` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_RenameDirectory":::

Este exemplo move um diretório nomeado `my-subdirectory-renamed` para um sub-diretório de um diretório chamado `my-directory-2` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>Eliminar um diretório

Elimine um diretório chamando o método **DataLakeDirectoryClient.deleteWithResponse.**

Este exemplo elimina um diretório chamado `my-directory` .   

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Faça o upload de um ficheiro para um diretório

Em primeiro lugar, crie uma referência de ficheiro no directório-alvo criando uma instância da classe **DataLakeFileClient.** Faça o upload de um ficheiro ligando para o método **DataLakeFileClient.append.** Certifique-se de completar o upload ligando para o método **DataLakeFileClient.FlushAsync.**

Este exemplo envia um ficheiro de texto para um diretório chamado `my-directory` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFile":::

> [!TIP]
> Se o tamanho do seu ficheiro for grande, o seu código terá de escoar várias chamadas para o método **DataLakeFileClient.append.** Considere usar o método **DataLakeFileClient.uploadFromFile.** Assim, podes carregar o ficheiro inteiro numa única chamada. 
>
> Consulte a secção seguinte para dar um exemplo.

## <a name="upload-a-large-file-to-a-directory"></a>Faça upload de um ficheiro grande para um diretório

Utilize o método **DataLakeFileClient.uploadFromFile** para carregar ficheiros grandes sem ter de fazer várias chamadas para o método **DataLakeFileClient.append.**

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Descarregue a partir de um diretório

Em primeiro lugar, crie uma instância **DataLakeFileClient** que represente o ficheiro que pretende descarregar. Utilize o método **DataLakeFileClient.read** para ler o ficheiro. Utilize qualquer API de processamento de ficheiros .NET para guardar bytes do fluxo para um ficheiro. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DownloadFile":::

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Este exemplo, imprime os nomes de cada ficheiro que está localizado num diretório denominado `my-directory` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_ListFilesInDirectory":::

## <a name="manage-access-control-lists-acls"></a>Gerir listas de controlo de acesso (ACLs)

Pode obter, definir e atualizar permissões de acesso de diretórios e ficheiros.

> [!NOTE]
> Se estiver a utilizar o Azure Ative Directory (Azure AD) para autorizar o acesso, certifique-se de que o seu principal de segurança foi atribuído à [função de Proprietário de Dados blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de  [acesso na Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="manage-a-directory-acl"></a>Gerir um diretório ACL

Este exemplo recebe e, em seguida, define o ACL de um diretório chamado `my-directory` . Este exemplo dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo que só lê e executa permissões, e dá a todos os outros acessos de leitura.

> [!NOTE]
> Se a sua aplicação autorizar o acesso utilizando o Azure Ative Directory (Azure AD), certifique-se de que o principal de segurança que a sua aplicação utiliza para autorizar o acesso foi atribuído à [função de Proprietário de Dados blob de armazenamento.](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de  [acesso na Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageDirectoryACLs":::

Você também pode obter e definir o ACL do diretório de raiz de um recipiente. Para obter o diretório de raiz, passe uma corda vazia `""` () para o método **DataLakeFileSystemClient.getDirectoryClient.**

### <a name="manage-a-file-acl"></a>Gerir um ficheiro ACL

Este exemplo recebe e, em seguida, define o ACL de um ficheiro chamado `upload-file.txt` . Este exemplo dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo que só lê e executa permissões, e dá a todos os outros acessos de leitura.

> [!NOTE]
> Se a sua aplicação autorizar o acesso utilizando o Azure Ative Directory (Azure AD), certifique-se de que o principal de segurança que a sua aplicação utiliza para autorizar o acesso foi atribuído à [função de Proprietário de Dados blob de armazenamento.](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de  [acesso na Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageFileACLs":::

### <a name="set-an-acl-recursively"></a>Desaprote um ACL de forma recursiva

Pode adicionar, atualizar e remover ACLs de forma recorrente nos itens infantis existentes de um diretório de pais sem ter de escamar estas alterações individualmente para cada item infantil. Para obter mais informações, consulte [as listas de controlo de acesso (ACLs) de forma recorrente para Azure Data Lake Storage Gen2](recursive-access-control-lists.md).

## <a name="see-also"></a>Ver também

* [Documentação de referência da API](/java/api/overview/azure/storage-file-datalake-readme)
* [Pacote (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [Amostras](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Mapeamento da Gen1 para a Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Dar feedback](https://github.com/Azure/azure-sdk-for-java/issues)