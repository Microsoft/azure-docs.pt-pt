---
title: Utilize .NET para definir ACLs em Azure Data Lake Storage Gen2
description: Utilize .NET para gerir listas de controlo de acesso (ACL) em contas de armazenamento que tenham espaço hierárquico (HNS) ativado.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 626e89d8d758d5fe31ef6c913076a9154274bb61
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654230"
---
# <a name="use-net-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Utilize .NET para gerir ACLs em Azure Data Lake Storage Gen2

Este artigo mostra-lhe como usar .NET para obter, definir e atualizar as listas de controlo de acesso de diretórios e ficheiros.

A herança ACL já está disponível para novos itens infantis que são criados sob um diretório de pais. Mas também pode adicionar, atualizar e remover ACLs de forma recorrente nos itens infantis existentes de um diretório parental sem ter que fazer estas alterações individualmente para cada item de criança.

[Pacote (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  [Amostras](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  Amostra ACL [recursiva](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)  |  Referência API [](/dotnet/api/azure.storage.files.datalake)  |  Mapeamento da [Gen1 para a Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [Dar feedback](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento que tem espaço hierárquico de nome (HNS) ativado. Siga [estas](create-data-lake-storage-account.md) instruções para criar uma.

- Versão Azure CLI `2.6.0` ou superior.

- Uma das seguintes permissões de segurança:

  - Um diretor de [segurança](../../role-based-access-control/overview.md#security-principal) Azure Ative (AD) provisionado que foi atribuído a função de Proprietário de [Dados blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) de armazenamento no âmbito do contentor-alvo, grupo de recursos-mãe ou subscrição.  

  - Utilizador próprio do contentor-alvo ou diretório ao qual planeia aplicar as definições ACL. Para definir os ACLs de forma recorrente, isto inclui todos os itens para crianças no contentor-alvo ou diretório.
  
  - Chave da conta de armazenamento.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Para começar, instale o pacote [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet.

1. Abra uma janela de comando (Por exemplo: Windows PowerShell).

2. A partir do seu diretório de projeto, instale o pacote de pré-visualização Azure.Storage.Files.DataLake utilizando o `dotnet add package` comando.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.6.0 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

   Em seguida, adicione estas declarações usando o topo do seu ficheiro de código.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   ```

## <a name="connect-to-the-account"></a>Ligar à conta

Para utilizar os snippets neste artigo, terá de criar uma instância [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) que represente a conta de armazenamento. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Conecte-se utilizando o Azure Ative Directory (AD)

> [!NOTE]
> Se estiver a utilizar o Azure Ative Directory (Azure AD) para autorizar o acesso, certifique-se de que o seu principal de segurança foi atribuído à [função de Proprietário de Dados blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua alteração, consulte o modelo de controlo de  [acesso na Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

Pode utilizar a biblioteca de [clientes de identidade Azure para .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) para autenticar a sua aplicação com Azure AD.

Depois de instalar a embalagem, adicione esta declaração utilizando a declaração no topo do seu ficheiro de código.

```csharp
using Azure.Identity;
```

Arranja uma identificação com um cliente, um segredo de cliente, e uma identificação de inquilino. Para isso, consulte [Adquirir um token da Azure AD para autorizar pedidos de uma aplicação do cliente.](../common/storage-auth-aad-app.md) Como parte desse processo, terás de atribuir um dos seguintes papéis de [controlo de acesso baseado em funções (Azure RBAC)](../../role-based-access-control/overview.md) ao teu diretor de segurança. 

|Função|Capacidade de definição de ACL|
|--|--|
|[Proprietário dos Dados do Armazenamento de Blobs](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Todos os diretórios e ficheiros na conta.|
|[Contribuinte de Dados do Armazenamento de Blobs](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Apenas diretórios e ficheiros pertencentes ao diretor de segurança.|

Este exemplo cria um exemplo [de DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) usando um ID de cliente, um segredo de cliente e uma identificação de inquilino.  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Para mais exemplos, consulte a biblioteca do [cliente de identidade Azure para](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) obter documentação .NET..

### <a name="connect-by-using-an-account-key"></a>Conecte-se usando uma chave de conta

Esta é a maneira mais fácil de ligar a uma conta.

Este exemplo cria uma instância [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) utilizando uma chave de conta.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>Definir ACLs

Quando *configurar* um ACL, **substitui-se** toda a ACL, incluindo todas as suas entradas. Se pretender alterar o nível de permissão de um principal de segurança ou adicionar um novo princípio de segurança à ACL sem afetar outras entradas existentes, deverá *atualizar* o ACL em vez disso. Para atualizar um ACL em vez de substituí-lo, consulte a secção [ACLs update](#update-acls) deste artigo.  

Se optar por *definir* a ACL, deve adicionar uma entrada para o utilizador próprio, uma entrada para o grupo de propriedade e uma entrada para todos os outros utilizadores. Para saber mais sobre o utilizador próprio, o grupo de propriedade, e todos os outros utilizadores, consulte [Utilizadores e identidades.](data-lake-storage-access-control.md#users-and-identities)

Esta secção mostra-lhe como:

- Definir o ACL de um diretório
- Definir o ACL de um arquivo
- Definir ACLs recursivamente

### <a name="set-the-acl-of-a-directory"></a>Definir o ACL de um diretório

Obtenha a lista de controlo de acesso (ACL) de um diretório, ligando para o método [DataLakeDirectoryClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) e definir o ACL chamando o método [DataLakeDirectoryClient.SetAccessControlList.](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist)

Este exemplo recebe e define o ACL de um diretório chamado `my-directory` . A cadeia `user::rwx,group::r-x,other::rw-` dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo que só lê e executa permissões, e dá a todos os outros a leitura e a escrita permissão.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ACLDirectory":::

Você também pode obter e definir o ACL do diretório de raiz de um recipiente. Para obter o diretório de raiz, passe uma corda vazia `""` () para o método [DataLakeFileSystemClient.GetDirectoryClient.](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getdirectoryclient)

### <a name="set-the-acl-of-a-file"></a>Definir o ACL de um arquivo

Obtenha a lista de controlo de acesso (ACL) de um ficheiro, ligando para o método [DataLakeFileClient.GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) e de definição do método ACL, chamando o método [DataLakeFileClient.SetAccessControlList.](/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist)

Este exemplo recebe e define o ACL de um ficheiro chamado `my-file.txt` . A cadeia `user::rwx,group::r-x,other::rw-` dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo que só lê e executa permissões, e dá a todos os outros a leitura e a escrita permissão.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_FileACL":::

### <a name="set-acls-recursively"></a>Definir ACLs recursivamente

Desenhe os ACLs de forma recorrente, chamando o método **DataLakeDirectoryClient.SetAccessControlRecursiveAsync.** Passe este método uma [Lista](/dotnet/api/system.collections.generic.list-1) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Cada [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) define uma entrada ACL.

Se pretender definir uma entrada ACL **predefinitiva,** pode definir a propriedade [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) do [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) para **ser verdadeira**. 

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . Este método aceita um parâmetro booleano nomeado `isDefaultScope` que especifica se deve definir o ACL predefinido. Este parâmetro é utilizado no construtor do [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). As entradas da ACL dão ao utilizador que leia, escreva e execute permissões, dá ao grupo que só lê e executa permissões, e não dá acesso a todos os outros. A última entrada ACL neste exemplo dá a um utilizador específico o ID do objeto ""xxxxxxx-xxxx-xxxx-xxxx-xxxx" ler e executar permissões.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_SetACLRecursively":::

Para ver um exemplo que define os ACLs de forma recursiva em lotes especificando um tamanho do lote, consulte a [amostra](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET .

## <a name="update-acls"></a>Atualizar ACLs

Quando *atualiza* um ACL, modifica o ACL em vez de substituir o ACL. Por exemplo, pode adicionar um novo princípio de segurança à ACL sem afetar outros princípios de segurança listados na ACL.  Para substituir o ACL em vez de atualizá-lo, consulte a secção [De Definição de ACLs](#set-acls) deste artigo.

Esta secção mostra-lhe como:

- Atualizar um ACL
- Atualizar ACLs recursivamente

### <a name="update-an-acl"></a>Atualizar um ACL

Em primeiro lugar, obtenha o ACL de um diretório ligando para o método [DataLakeDirectoryClient.GetAccessControlAsync.](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) Copie a lista de entradas ACL para uma nova [lista](/dotnet/api/system.collections.generic.list-1) de [objetos PathAccessControl.](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) Em seguida, localize a entrada que pretende atualizar e substitua-a na lista. Desa ajuste o ACL chamando o método [DataLakeDirectoryClient.SetAccessControlList.](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist)

Este exemplo atualiza a raiz ACL de um recipiente substituindo a entrada ACL para todos os outros utilizadores. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACL":::

### <a name="update-acls-recursively"></a>Atualizar ACLs recursivamente

Para atualizar um ACL de forma recorrente, crie um novo objeto ACL com a entrada ACL que pretende atualizar e, em seguida, utilize esse objeto na atualização da operação ACL. Não obtenha o ACL existente, basta fornecer entradas ACL para serem atualizadas.

Atualize um ACL recursivamente chamando o método **DataLakeDirectoryClient.UpdateAccessControlRecursiveAsync.**  Passe este método uma [Lista](/dotnet/api/system.collections.generic.list-1) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Cada [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) define uma entrada ACL.

Se pretender atualizar uma entrada ACL **predefinitiva,** pode definir a propriedade [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) do [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) para **ser verdadeira**.

Este exemplo atualiza uma entrada ACL com permissão de escrita. Este método aceita um parâmetro booleano nomeado `isDefaultScope` que especifica se deve atualizar o ACL predefinido. Este parâmetro é utilizado no construtor do [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACLsRecursively":::

Para ver um exemplo que atualiza os ACLs de forma recursiva em lotes especificando um tamanho do lote, consulte a [amostra](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET .

## <a name="remove-acl-entries"></a>Remover entradas ACL

Pode remover uma ou mais entradas ACL. Esta secção mostra-lhe como:

- Remover uma entrada ACL
- Remover as entradas ACL recursivamente

### <a name="remove-an-acl-entry"></a>Remover uma entrada ACL

Em primeiro lugar, obtenha o ACL de um diretório ligando para o método [DataLakeDirectoryClient.GetAccessControlAsync.](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) Copie a lista de entradas ACL para uma nova [lista](/dotnet/api/system.collections.generic.list-1) de [objetos PathAccessControl.](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) Em seguida, localize a entrada que pretende remover e ligue para o método [Remover](/dotnet/api/system.collections.ilist.remove) da coleção. Desa ajuste o ACL atualizado chamando o método [DataLakeDirectoryClient.SetAccessControlList.](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist)

Este exemplo atualiza a raiz ACL de um recipiente substituindo a entrada ACL para todos os outros utilizadores. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>Remover as entradas ACL recursivamente

Para remover as entradas ACL de forma recorrente, crie um novo objeto ACL para a entrada ACL ser removida e, em seguida, utilize esse objeto para remover a operação ACL. Não obtenha o ACL existente, basta fornecer as entradas ACL para serem removidas. 

Remova as entradas ACL chamando o método **DataLakeDirectoryClient.RemoveAccessControlRecursiveAsync.** Passe este método uma [Lista](/dotnet/api/system.collections.generic.list-1) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Cada [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) define uma entrada ACL. 

Se pretender remover uma entrada ACL **predefinitiva,** pode definir a propriedade [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) do [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) para **ser verdadeira**. 

Este exemplo remove uma entrada ACL da ACL do diretório denominado `my-parent-directory` . Este método aceita um parâmetro booleano nomeado `isDefaultScope` que especifica se deve remover a entrada do ACL predefinido. Este parâmetro é utilizado no construtor do [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLRecursively":::

Para ver um exemplo que remove os ACLs de forma recorrente em lotes especificando um tamanho do lote, consulte a [amostra](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET .

## <a name="recover-from-failures"></a>Recuperar de falhas

Pode encontrar erros de tempo de execução ou permissão ao modificar os ACLs de forma recorrente. Para erros de tempo de execução, reinicie o processo desde o início. Podem ocorrer erros de permissão se o diretor de segurança não tiver permissão suficiente para modificar a ACL de um diretório ou ficheiro que está na hierarquia do diretório a ser modificado. Aborde a questão da permissão e, em seguida, opte por retomar o processo a partir do ponto de falha, utilizando um token de continuação, ou reiniciar o processo desde o início. Não tens de usar o símbolo de continuação se preferires recomeçar desde o início. Pode re-aplicar as entradas ACL sem qualquer impacto negativo.

Este exemplo devolve um sinal de continuação em caso de falha. A aplicação pode voltar a chamar este método de exemplo depois de o erro ter sido abordado, e passar no token de continuação. Se este método de exemplo for chamado pela primeira vez, a aplicação pode passar num valor para `null` o parâmetro simbólico de continuação. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ResumeContinuationToken":::

Para ver um exemplo que define os ACLs de forma recursiva em lotes especificando um tamanho do lote, consulte a [amostra](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET .

Se quiser que o processo seja concluído ininterruptamente por erros de permissão, pode especificar isso.

Para garantir que o processo se completa ininterruptamente, passe num objeto **AccessControlChangedOptions** e desempere a propriedade **ContinueOnFailure** desse objeto para ``true`` .

Este exemplo define as entradas ACL de forma recursiva. Se este código encontrar um erro de permissão, regista essa falha e continua a execução. Este exemplo imprime o número de falhas na consola.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ContinueOnFailure":::

Para ver um exemplo que define os ACLs de forma recursiva em lotes especificando um tamanho do lote, consulte a [amostra](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET .

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Ver também

- [Documentação de referência da API](/dotnet/api/azure.storage.files.datalake)
- [Pacote (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [Amostras](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Mapeamento da Gen1 para a Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Dar feedback](https://github.com/Azure/azure-sdk-for-net/issues)
- [Modelo de controlo de acesso em Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Listas de controlo de acesso (ACLs) em Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)