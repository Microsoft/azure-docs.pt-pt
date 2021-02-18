---
title: Use python para gerir dados em Azure Data Lake Storage Gen2
description: Use python para gerir diretórios e ficheiros em contas de armazenamento que têm espaço hierárquico habilitado.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: a143c0aa19241b532cabff95fe6bf85679e4007c
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652297"
---
# <a name="use-python-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Use python para gerir diretórios e ficheiros em Azure Data Lake Storage Gen2

Este artigo mostra-lhe como usar python para criar e gerir diretórios e ficheiros em contas de armazenamento que têm um espaço hierárquico de nomes.

Para saber como obter, definir e atualizar as listas de controlo de acesso (ACL) de diretórios e ficheiros, consulte [Use Python para gerir ACLs em Azure Data Lake Storage Gen2](data-lake-storage-acl-python.md).

[Pacote (Índice de Pacote Python)](https://pypi.org/project/azure-storage-file-datalake/)  |  [Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  Referência API [](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  Mapeamento da [Gen1 para a Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Dar feedback](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento que tem espaço hierárquico habilitado. Siga [estas](create-data-lake-storage-account.md) instruções para criar uma.

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

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Conecte-se utilizando o Azure Ative Directory (Azure AD)

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

## <a name="see-also"></a>Ver também

- [Documentação de referência da API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [Pacote (Índice de Pacote Python)](https://pypi.org/project/azure-storage-file-datalake/)
- [Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Mapeamento da Gen1 para a Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Dar feedback](https://github.com/Azure/azure-sdk-for-python/issues)