---
title: Use Java para definir ACLs em Azure Data Lake Storage Gen2
description: Utilize bibliotecas de armazenamento Azure para Java para gerir listas de controlo de acesso (ACL) em contas de armazenamento que tenham espaço hierárquico (HNS) ativado.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: e7d6156fe5cd8ab32ff159bda64e0c06cfbac406
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654229"
---
# <a name="use-java-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Use Java para gerir ACLs em Azure Data Lake Storage Gen2

Este artigo mostra-lhe como usar Java para obter, definir e atualizar as listas de controlo de acesso de diretórios e ficheiros. 

A herança ACL já está disponível para novos itens infantis que são criados sob um diretório de pais. Mas também pode adicionar, atualizar e remover ACLs de forma recorrente nos itens infantis existentes de um diretório parental sem ter que fazer estas alterações individualmente para cada item de criança. 

[Pacote (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  |  [Amostras](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  |  Referência API [](/java/api/overview/azure/storage-file-datalake-readme)  |  Mapeamento da [Gen1 para a Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Dar feedback](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento que tem espaço hierárquico de nome (HNS) ativado. Siga [estas](create-data-lake-storage-account.md) instruções para criar uma.

- Versão Azure CLI `2.6.0` ou superior.

- Uma das seguintes permissões de segurança:

  - Um diretor de [segurança](../../role-based-access-control/overview.md#security-principal) Azure Ative (AD) provisionado que foi atribuído a função de Proprietário de [Dados blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) de armazenamento no âmbito do contentor-alvo, grupo de recursos-mãe ou subscrição.  

  - Utilizador próprio do contentor-alvo ou diretório ao qual planeia aplicar as definições ACL. Para definir os ACLs de forma recorrente, isto inclui todos os itens para crianças no contentor-alvo ou diretório.
  
  - Chave da conta de armazenamento.

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

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Conecte-se utilizando o Azure Ative Directory (Azure AD)

Pode utilizar a biblioteca de [clientes de identidade Azure para a Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) para autenticar a sua aplicação com Azure AD.

Arranja uma identificação com um cliente, um segredo de cliente, e uma identificação de inquilino. Para isso, consulte [Adquirir um token da Azure AD para autorizar pedidos de uma aplicação do cliente.](../common/storage-auth-aad-app.md) Como parte desse processo, terás de atribuir um dos seguintes papéis de [controlo de acesso baseado em funções (Azure RBAC)](../../role-based-access-control/overview.md) ao teu diretor de segurança. 

|Função|Capacidade de definição de ACL|
|--|--|
|[Proprietário dos Dados do Armazenamento de Blobs](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Todos os diretórios e ficheiros na conta.|
|[Contribuinte de Dados do Armazenamento de Blobs](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Apenas diretórios e ficheiros pertencentes ao diretor de segurança.|

Este exemplo cria um exemplo **de DataLakeServiceClient** usando um ID de cliente, um segredo de cliente e uma identificação de inquilino.  

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> Para mais exemplos, consulte a biblioteca de [clientes de identidade Azure para](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) documentação java.

### <a name="connect-by-using-an-account-key"></a>Conecte-se usando uma chave de conta

Esta é a maneira mais fácil de ligar a uma conta. 

Este exemplo cria uma instância **DataLakeServiceClient** utilizando uma chave de conta.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>Definir ACLs

Quando *configurar* um ACL, **substitui-se** toda a ACL, incluindo todas as suas entradas. Se pretender alterar o nível de permissão de um principal de segurança ou adicionar um novo princípio de segurança à ACL sem afetar outras entradas existentes, deverá *atualizar* o ACL em vez disso. Para atualizar um ACL em vez de substituí-lo, consulte a secção [ACLs update](#update-acls) deste artigo.  

Se optar por *definir* a ACL, deve adicionar uma entrada para o utilizador próprio, uma entrada para o grupo de propriedade e uma entrada para todos os outros utilizadores. Para saber mais sobre o utilizador próprio, o grupo de propriedade, e todos os outros utilizadores, consulte [Utilizadores e identidades.](data-lake-storage-access-control.md#users-and-identities)

Esta secção mostra-lhe como:

- Definir o ACL de um diretório
- Definir o ACL de um arquivo
- Definir ACLs recursivamente 

### <a name="set-the-acl-of-a-directory"></a>Definir o ACL de um diretório

Este exemplo recebe e, em seguida, define o ACL de um diretório chamado `my-directory` . Este exemplo dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo que só lê e executa permissões, e dá a todos os outros acessos de leitura.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageDirectoryACLs":::

Você também pode obter e definir o ACL do diretório de raiz de um recipiente. Para obter o diretório de raiz, passe uma corda vazia `""` () para o método **DataLakeFileSystemClient.getDirectoryClient.**

### <a name="set-the-acl-of-a-file"></a>Definir o ACL de um arquivo

Este exemplo recebe e, em seguida, define o ACL de um ficheiro chamado `upload-file.txt` . Este exemplo dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo que só lê e executa permissões, e dá a todos os outros acessos de leitura.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageFileACLs":::

### <a name="set-acls-recursively"></a>Definir ACLs recursivamente

Desenhe os ACLs de forma recorrente, chamando o método **DataLakeDirectoryClient.setAccessControlRecursive.** Passe este método uma [Lista](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) de [objetos PathAccessControlEntry.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) Cada [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) define uma entrada ACL.

Se pretender definir uma entrada ACL **predefinida,** pode ligar para o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passar num valor **de verdade**. 

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . Este método aceita um parâmetro booleano nomeado `isDefaultScope` que especifica se deve definir o ACL predefinido. Este parâmetro é utilizado em cada chamada para o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). As entradas da ACL dão ao utilizador que leia, escreva e execute permissões, dá ao grupo que só lê e executa permissões, e não dá acesso a todos os outros. A última entrada ACL neste exemplo dá a um utilizador específico com o objeto ID "xxxxxxx-xxxx-xxxx-xxxx-xxxx" ler e executar permissões.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_SetACLRecursively":::

## <a name="update-acls"></a>Atualizar ACLs

Quando *atualiza* um ACL, modifica o ACL em vez de substituir o ACL. Por exemplo, pode adicionar um novo princípio de segurança à ACL sem afetar outros princípios de segurança listados na ACL.  Para substituir o ACL em vez de atualizá-lo, consulte a secção [De Definição de ACLs](#set-acls) deste artigo.

Esta secção mostra-lhe como:

- Atualizar um ACL
- Atualizar ACLs recursivamente

### <a name="update-an-acl"></a>Atualizar um ACL

Em primeiro lugar, obtenha o ACL de um diretório chamando o método [PathAccessControl.getAccessControlList.](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist) Copie a lista de entradas ACL para um novo objeto **de lista** do tipo [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry). Em seguida, localize a entrada que pretende atualizar e substitua-a na lista. Desa ajuste o ACL chamando o método [DataLakeDirectoryClient.setAccessControlList.](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist)

Este exemplo atualiza o ACL de um diretório nomeado `my-parent-directory` substituindo a entrada para todos os outros utilizadores. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACL":::

Você também pode obter e definir o ACL do diretório de raiz de um recipiente. Para obter o diretório de raiz, passe uma corda vazia `""` () para o método **DataLakeFileSystemClient.getDirectoryClient.**

### <a name="update-acls-recursively"></a>Atualizar ACLs recursivamente

Para atualizar um ACL de forma recorrente, crie um novo objeto ACL com a entrada ACL que pretende atualizar e, em seguida, utilize esse objeto na atualização da operação ACL. Não obtenha o ACL existente, basta fornecer entradas ACL para serem atualizadas.

Atualize os ACLs de forma recorrente, ligando para o método **DataLakeDirectoryClient.updateAccessControlRecursive.**  Passe este método uma [Lista](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) de [objetos PathAccessControlEntry.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) Cada [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) define uma entrada ACL. 

Se pretender atualizar uma entrada ACL **predefinida,** pode então o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passar num valor **de verdade**. 

Este exemplo atualiza uma entrada ACL com permissão de escrita. Este método aceita um parâmetro booleano nomeado `isDefaultScope` que especifica se deve atualizar o ACL predefinido. Este parâmetro é utilizado na chamada para o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACLRecursively":::

## <a name="remove-acl-entries"></a>Remover entradas ACL

Pode remover uma ou mais entradas ACL. Esta secção mostra-lhe como:

- Remover uma entrada ACL
- Remover as entradas ACL recursivamente

### <a name="remove-an-acl-entry"></a>Remover uma entrada ACL

Em primeiro lugar, obtenha o ACL de um diretório chamando o método [PathAccessControl.getAccessControlList.](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist) Copie a lista de entradas ACL para um novo objeto **de lista** do tipo [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry). Em seguida, localize a entrada que pretende remover e ligue para o método **Remover** do objeto **Lista.** Desa ajuste o ACL atualizado chamando o método [DataLakeDirectoryClient.setAccessControlList.](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist)

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>Remover as entradas ACL recursivamente

Para remover as entradas ACL de forma recorrente, crie um novo objeto ACL para a entrada ACL ser removida e, em seguida, utilize esse objeto para remover a operação ACL. Não obtenha o ACL existente, basta fornecer as entradas ACL para serem removidas.

Remova as entradas ACL chamando o método **DataLakeDirectoryClient.removeAccessControlRecursive.** Passe este método uma [Lista](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) de [objetos PathAccessControlEntry.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) Cada [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) define uma entrada ACL. 

Se pretender remover uma entrada ACL **predefinida,** pode então o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passar num valor **de verdade**.  

Este exemplo remove uma entrada ACL da ACL do diretório denominado `my-parent-directory` . Este método aceita um parâmetro booleano nomeado `isDefaultScope` que especifica se deve remover a entrada do ACL predefinido. Este parâmetro é utilizado na chamada para o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLRecursively":::

## <a name="recover-from-failures"></a>Recuperar de falhas

Pode encontrar erros de tempo de execução ou permissão. Para erros de tempo de execução, reinicie o processo desde o início. Podem ocorrer erros de permissão se o diretor de segurança não tiver permissão suficiente para modificar a ACL de um diretório ou ficheiro que está na hierarquia do diretório a ser modificado. Aborde a questão da permissão e, em seguida, opte por retomar o processo a partir do ponto de falha, utilizando um token de continuação, ou reiniciar o processo desde o início. Não tens de usar o símbolo de continuação se preferires recomeçar desde o início. Pode re-aplicar as entradas ACL sem qualquer impacto negativo.

Este exemplo devolve um sinal de continuação em caso de falha. A aplicação pode voltar a chamar este método de exemplo depois de o erro ter sido abordado, e passar no token de continuação. Se este método de exemplo for chamado pela primeira vez, a aplicação pode passar num valor para `null` o parâmetro simbólico de continuação.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ResumeSetACLRecursively":::

Se quiser que o processo seja concluído ininterruptamente por erros de permissão, pode especificar isso.

Para garantir que o processo é concluído ininterruptamente, ligue para o método **setContinueOnFailure** de um objeto [PathSetAccessControlRecursiveOptions](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passe num valor **verdadeiro**.

Este exemplo define as entradas ACL de forma recursiva. Se este código encontrar um erro de permissão, regista essa falha e continua a execução. Este exemplo imprime o número de falhas na consola.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ContinueOnFailure":::

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Ver também

- [Documentação de referência da API](/java/api/overview/azure/storage-file-datalake-readme)
- [Pacote (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
- [Amostras](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
- [Mapeamento da Gen1 para a Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Dar feedback](https://github.com/Azure/azure-sdk-for-java/issues)
- [Modelo de controlo de acesso em Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Listas de controlo de acesso (ACLs) em Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)