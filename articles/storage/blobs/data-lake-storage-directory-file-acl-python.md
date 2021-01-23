---
title: Azure Data Lake Storage Gen2 Python SDK para ficheiros & ACLs
description: Utilize listas de controlo de acesso a ficheiros e diretórios python (ACL) em contas de armazenamento que tenham espaço hierárquico (HNS) ativado.
author: normesta
ms.service: storage
ms.date: 01/22/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: 5036930c7bb49578582fbc1b347b11518579b53e
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740623"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Use python para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2

Este artigo mostra-lhe como usar python para criar e gerir diretórios, ficheiros e permissões em contas de armazenamento que têm espaço hierárquico (HNS) ativado. 

[Pacote (Índice de Pacote Python)](https://pypi.org/project/azure-storage-file-datalake/)  |  [Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  Referência API [](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  Mapeamento da [Gen1 para a Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Dar feedback](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tem espaço hierárquico de nome (HNS) ativado. Siga [estas](../common/storage-account-create.md) instruções para criar uma.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Instale a biblioteca de clientes Azure Data Lake Storage para Python utilizando [pip](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake
```

Adicione estas declarações de importação ao topo do seu ficheiro de código.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Ligar à conta

Para utilizar os snippets neste artigo, terá de criar uma instância **DataLakeServiceClient** que represente a conta de armazenamento. 

### <a name="connect-by-using-an-account-key"></a>Conecte-se usando uma chave de conta

Esta é a maneira mais fácil de ligar a uma conta. 

Este exemplo cria uma instância **DataLakeServiceClient** utilizando uma chave de conta.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::
 
- Substitua o `storage_account_name` valor do espaço reservado pelo nome da sua conta de armazenamento.

- Substitua o `storage_account_key` valor do espaço reservado pela chave de acesso à sua conta de armazenamento.

### <a name="connect-by-using-azure-active-directory-ad"></a>Conecte-se utilizando o Azure Ative Directory (AD)

Pode utilizar a biblioteca de [clientes de identidade Azure para python](https://pypi.org/project/azure-identity/) para autenticar a sua aplicação com Azure AD.

Este exemplo cria um exemplo **de DataLakeServiceClient** usando um ID de cliente, um segredo de cliente e uma identificação de inquilino.  Para obter estes valores, consulte [Adquirir um token da Azure AD para autorizar pedidos de uma aplicação do cliente.](../common/storage-auth-aad-app.md)

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Para mais exemplos, consulte a biblioteca de [clientes de identidade Azure para](https://pypi.org/project/azure-identity/) documentação Python.

## <a name="create-a-container"></a>Criar um contentor

Um contentor funciona como um sistema de ficheiros para os seus ficheiros. Pode criar um chamando o **método FileSystemDataLakeServiceClient.create_file_system.**

Este exemplo cria um recipiente chamado `my-file-system` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório chamando o método **FileSystemClient.create_directory.**

Este exemplo adiciona um diretório nomeado `my-directory` a um contentor. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Mudar de nome ou mover um diretório

Mude o nome ou mova um diretório chamando o método **DataLakeDirectoryClient.rename_directory.** Passe o caminho do diretório desejado um parâmetro. 

Este exemplo renomea um sub-diretório para o nome `my-subdirectory-renamed` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_RenameDirectory":::

## <a name="delete-a-directory"></a>Eliminar um diretório

Elimine um diretório chamando o método **DataLakeDirectoryClient.delete_directory.**

Este exemplo elimina um diretório chamado `my-directory` .  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Faça o upload de um ficheiro para um diretório 

Em primeiro lugar, crie uma referência de ficheiro no directório-alvo criando uma instância da classe **DataLakeFileClient.** Faça o upload de um ficheiro chamando o método **DataLakeFileClient.append_data.** Certifique-se de completar o upload ligando para o método **DataLakeFileClient.flush_data.**

Este exemplo envia um ficheiro de texto para um diretório chamado `my-directory` .   

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFile":::

> [!TIP]
> Se o tamanho do seu ficheiro for grande, o seu código terá de então fazer várias chamadas para o método **DataLakeFileClient.append_data.** Considere usar o método **DataLakeFileClient.upload_data.** Assim, podes carregar o ficheiro inteiro numa única chamada. 

## <a name="upload-a-large-file-to-a-directory"></a>Faça upload de um ficheiro grande para um diretório

Utilize o método **DataLakeFileClient.upload_data** para carregar ficheiros grandes sem ter de fazer várias chamadas para o método **DataLakeFileClient.append_data.**

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Descarregue a partir de um diretório 

Abra um arquivo local para escrever. Em seguida, crie um caso **DataLakeFileClient** que represente o ficheiro que pretende descarregar. Ligue para o **DataLakeFileClient.read_file** para ler bytes do ficheiro e, em seguida, escreva esses bytes para o arquivo local. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DownloadFromDirectory":::

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Liste o conteúdo do diretório chamando o método **FileSystemClient.get_paths** e, em seguida, enumerando através dos resultados.

Este exemplo, imprime o caminho de cada subdireção e arquivo que está localizado num diretório denominado `my-directory` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_ListFilesInDirectory":::

## <a name="manage-access-control-lists-acls"></a>Gerir listas de controlo de acesso (ACLs)

Pode obter, definir e atualizar permissões de acesso de diretórios e ficheiros.

> [!NOTE]
> Se estiver a utilizar o Azure Ative Directory (Azure AD) para autorizar o acesso, certifique-se de que o seu principal de segurança foi atribuído à [função de Proprietário de Dados blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de  [acesso na Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="manage-directory-acls"></a>Gerir acLs de diretório

Obtenha a lista de controlo de acesso (ACL) de um diretório, chamando o método **DataLakeDirectoryClient.get_access_control** e definir o ACL chamando o método **DataLakeDirectoryClient.set_access_control.**

> [!NOTE]
> Se a sua aplicação autorizar o acesso utilizando o Azure Ative Directory (Azure AD), certifique-se de que o principal de segurança que a sua aplicação utiliza para autorizar o acesso foi atribuído à [função de Proprietário de Dados blob de armazenamento.](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de  [acesso na Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

Este exemplo recebe e define o ACL de um diretório chamado `my-directory` . A cadeia `rwxr-xrw-` dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo que só lê e executa permissões, e dá a todos os outros a leitura e a escrita permissão.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ACLDirectory":::

Você também pode obter e definir o ACL do diretório de raiz de um recipiente. Para obter o diretório de raiz, ligue para o método **FileSystemClient._get_root_directory_client.**

### <a name="manage-file-permissions"></a>Gerir permissões de ficheiros

Obtenha a lista de controlo de acesso (ACL) de um ficheiro chamando o método **DataLakeFileClient.get_access_control** e definir o ACL chamando o método **DataLakeFileClient.set_access_control.**

> [!NOTE]
> Se a sua aplicação autorizar o acesso utilizando o Azure Ative Directory (Azure AD), certifique-se de que o principal de segurança que a sua aplicação utiliza para autorizar o acesso foi atribuído à [função de Proprietário de Dados blob de armazenamento.](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de  [acesso na Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

Este exemplo recebe e define o ACL de um ficheiro chamado `my-file.txt` . A cadeia `rwxr-xrw-` dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo que só lê e executa permissões, e dá a todos os outros a leitura e a escrita permissão.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_FileACL":::

### <a name="set-an-acl-recursively"></a>Desaprote um ACL de forma recursiva

Pode adicionar, atualizar e remover ACLs de forma recorrente nos itens infantis existentes de um diretório de pais sem ter de escamar estas alterações individualmente para cada item infantil. Para obter mais informações, consulte [as listas de controlo de acesso (ACLs) de forma recorrente para Azure Data Lake Storage Gen2](recursive-access-control-lists.md).

## <a name="see-also"></a>Veja também

* [Documentação de referência da API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
* [Pacote (Índice de Pacote Python)](https://pypi.org/project/azure-storage-file-datalake/)
* [Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Mapeamento da Gen1 para a Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Dar feedback](https://github.com/Azure/azure-sdk-for-python/issues)