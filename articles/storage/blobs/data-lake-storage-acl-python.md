---
title: Use python para gerir ACLs em Azure Data Lake Storage Gen2
description: Use Python gere listas de controlo de acesso (ACL) em contas de armazenamento que tenham espaço hierárquico (HNS) ativado.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: ba864aa1aa2462f21e05ab5e779c8e715d6bb973
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654220"
---
# <a name="use-python-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Use python para gerir ACLs em Azure Data Lake Storage Gen2

Este artigo mostra-lhe como usar o Python para obter, definir e atualizar as listas de controlo de acesso de diretórios e ficheiros. 

A herança ACL já está disponível para novos itens infantis que são criados sob um diretório de pais. Mas também pode adicionar, atualizar e remover ACLs de forma recorrente nos itens infantis existentes de um diretório parental sem ter que fazer estas alterações individualmente para cada item de criança. 

[Pacote (Índice de Pacote Python)](https://pypi.org/project/azure-storage-file-datalake/)  |  [Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  [Amostras ACL recursivas](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)  |  Referência API [](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  Mapeamento da [Gen1 para a Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Dar feedback](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento que tem espaço hierárquico de nome (HNS) ativado. Siga [estas](create-data-lake-storage-account.md) instruções para criar uma.

- Versão Azure CLI `2.6.0` ou superior.

- Uma das seguintes permissões de segurança:

  - Um diretor de [segurança](../../role-based-access-control/overview.md#security-principal) Azure Ative (AD) provisionado que foi atribuído a função de Proprietário de [Dados blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) de armazenamento no âmbito do contentor-alvo, grupo de recursos-mãe ou subscrição.  

  - Utilizador próprio do contentor-alvo ou diretório ao qual planeia aplicar as definições ACL. Para definir os ACLs de forma recorrente, isto inclui todos os itens para crianças no contentor-alvo ou diretório.
  
  - Chave da conta de armazenamento.

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

### <a name="connect-by-using-azure-active-directory-ad"></a>Conecte-se utilizando o Azure Ative Directory (AD)

> [!NOTE]
> Se estiver a utilizar o Azure Ative Directory (Azure AD) para autorizar o acesso, certifique-se de que o seu principal de segurança foi atribuído à [função de Proprietário de Dados blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua alteração, consulte o modelo de controlo de  [acesso na Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

Pode utilizar a biblioteca de [clientes de identidade Azure para python](https://pypi.org/project/azure-identity/) para autenticar a sua aplicação com Azure AD.

Arranja uma identificação com um cliente, um segredo de cliente, e uma identificação de inquilino. Para isso, consulte [Adquirir um token da Azure AD para autorizar pedidos de uma aplicação do cliente.](../common/storage-auth-aad-app.md) Como parte desse processo, terás de atribuir um dos seguintes papéis de [controlo de acesso baseado em funções (Azure RBAC)](../../role-based-access-control/overview.md) ao teu diretor de segurança. 

|Função|Capacidade de definição de ACL|
|--|--|
|[Proprietário dos Dados do Armazenamento de Blobs](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Todos os diretórios e ficheiros na conta.|
|[Contribuinte de Dados do Armazenamento de Blobs](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Apenas diretórios e ficheiros pertencentes ao diretor de segurança.|

Este exemplo cria um exemplo **de DataLakeServiceClient** usando um ID de cliente, um segredo de cliente e uma identificação de inquilino.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Para mais exemplos, consulte a biblioteca de [clientes de identidade Azure para](https://pypi.org/project/azure-identity/) documentação Python.

### <a name="connect-by-using-an-account-key"></a>Conecte-se usando uma chave de conta

Esta é a maneira mais fácil de ligar a uma conta.

Este exemplo cria uma instância **DataLakeServiceClient** utilizando uma chave de conta.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::
 
- Substitua o `storage_account_name` valor do espaço reservado pelo nome da sua conta de armazenamento.

- Substitua o `storage_account_key` valor do espaço reservado pela chave de acesso à sua conta de armazenamento.

## <a name="set-acls"></a>Definir ACLs

Quando *configurar* um ACL, **substitui-se** toda a ACL, incluindo todas as suas entradas. Se pretender alterar o nível de permissão de um principal de segurança ou adicionar um novo princípio de segurança à ACL sem afetar outras entradas existentes, deverá *atualizar* o ACL em vez disso. Para atualizar um ACL em vez de substituí-lo, consulte a secção [ACLs update](#update-acls-recursively) deste artigo.  

Esta secção mostra-lhe como:

- Definir o ACL de um diretório
- Definir o ACL de um arquivo

### <a name="set-the-acl-of-a-directory"></a>Definir o ACL de um diretório

Obtenha a lista de controlo de acesso (ACL) de um diretório, chamando o método **DataLakeDirectoryClient.get_access_control** e definir o ACL chamando o método **DataLakeDirectoryClient.set_access_control.**

Este exemplo recebe e define o ACL de um diretório chamado `my-directory` . A cadeia `rwxr-xrw-` dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo que só lê e executa permissões, e dá a todos os outros a leitura e a escrita permissão.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ACLDirectory":::

Você também pode obter e definir o ACL do diretório de raiz de um recipiente. Para obter o diretório de raiz, ligue para o método **FileSystemClient._get_root_directory_client.**

### <a name="set-the-acl-of-a-file"></a>Definir o ACL de um arquivo

Obtenha a lista de controlo de acesso (ACL) de um ficheiro chamando o método **DataLakeFileClient.get_access_control** e definir o ACL chamando o método **DataLakeFileClient.set_access_control.**

Este exemplo recebe e define o ACL de um ficheiro chamado `my-file.txt` . A cadeia `rwxr-xrw-` dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo que só lê e executa permissões, e dá a todos os outros a leitura e a escrita permissão.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_FileACL":::

## <a name="set-acls-recursively"></a>Definir ACLs recursivamente

Quando *configurar* um ACL, **substitui-se** toda a ACL, incluindo todas as suas entradas. Se pretender alterar o nível de permissão de um principal de segurança ou adicionar um novo princípio de segurança à ACL sem afetar outras entradas existentes, deverá *atualizar* o ACL em vez disso. Para atualizar um ACL em vez de substituí-lo, consulte a secção [ACLs update recursivamente](#update-acls-recursively) deste artigo.

Desaprote os ACLs de forma recursiva, chamando o método **DataLakeDirectoryClient.set_access_control_recursive.**

Se pretender definir uma entrada ACL **predefinida,** adicione a cadeia `default:` ao início de cada cadeia de entrada ACL.

Este exemplo define a ACL de um diretório chamado `my-parent-directory` .

Este método aceita um parâmetro booleano nomeado `is_default_scope` que especifica se deve definir o ACL predefinido. se esse parâmetro `True` for, a lista de entradas ACL é precedida com a cadeia `default:` .

As entradas da ACL dão ao utilizador que leia, escreva e execute permissões, dá ao grupo que só lê e executa permissões, e não dá acesso a todos os outros. A última entrada ACL neste exemplo dá a um utilizador específico o ID do objeto ""xxxxxxx-xxxx-xxxx-xxxx-xxxx" ler e executar permissões. Estas entradas dão ao utilizador que lê, escreve e executa permissões, dá ao grupo que só lê e executa permissões, e não dá acesso a todos os outros. A última entrada ACL neste exemplo dá a um utilizador específico o ID do objeto ""xxxxxxx-xxxx-xxxx-xxxx-xxxx" ler e executar permissões.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_SetACLRecursively":::

Para ver um exemplo que processa os ACLs de forma recursiva em lotes especificando um tamanho do lote, consulte a [amostra](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)de pitão .

## <a name="update-acls-recursively"></a>Atualizar ACLs recursivamente

Quando *atualiza* um ACL, modifica o ACL em vez de substituir o ACL. Por exemplo, pode adicionar um novo princípio de segurança à ACL sem afetar outros princípios de segurança listados na ACL.  Para substituir o ACL em vez de atualizá-lo, consulte a secção [De Definição de ACLs](#set-acls) deste artigo.

Para atualizar um ACL de forma recorrente, crie um novo objeto ACL com a entrada ACL que pretende atualizar e, em seguida, utilize esse objeto na atualização da operação ACL. Não obtenha o ACL existente, basta fornecer entradas ACL para serem atualizadas. Atualize um ACL recursivamente chamando o método **DataLakeDirectoryClient.update_access_control_recursive.** Se pretender atualizar uma entrada ACL **predefinida,** adicione a cadeia `default:` ao início de cada cadeia de entrada ACL.

Este exemplo atualiza uma entrada ACL com permissão de escrita.

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . Este método aceita um parâmetro booleano nomeado `is_default_scope` que especifica se deve atualizar o ACL predefinido. se esse parâmetro `True` for, a entrada ACL atualizada é precedida com a cadeia `default:` .  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_UpdateACLsRecursively":::

Para ver um exemplo que processa os ACLs de forma recursiva em lotes especificando um tamanho do lote, consulte a [amostra](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)de pitão .

## <a name="remove-acl-entries-recursively"></a>Remover as entradas ACL recursivamente

Pode remover uma ou mais entradas ACL. Para remover as entradas ACL de forma recorrente, crie um novo objeto ACL para a entrada ACL ser removida e, em seguida, utilize esse objeto para remover a operação ACL. Não obtenha o ACL existente, basta fornecer as entradas ACL para serem removidas. 

Remova as entradas ACL chamando o método **DataLakeDirectoryClient.remove_access_control_recursive.** Se pretender remover uma entrada ACL **predefinida,** adicione a cadeia `default:` ao início da cadeia de entrada ACL. 

Este exemplo remove uma entrada ACL da ACL do diretório denominado `my-parent-directory` . Este método aceita um parâmetro booleano nomeado `is_default_scope` que especifica se deve remover a entrada do ACL predefinido. se esse parâmetro `True` for, a entrada ACL atualizada é precedida com a cadeia `default:` . 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

Para ver um exemplo que processa os ACLs de forma recursiva em lotes especificando um tamanho do lote, consulte a [amostra](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)de pitão .

## <a name="recover-from-failures"></a>Recuperar de falhas

Pode encontrar erros de tempo de execução ou permissão. Para erros de tempo de execução, reinicie o processo desde o início. Podem ocorrer erros de permissão se o diretor de segurança não tiver permissão suficiente para modificar a ACL de um diretório ou ficheiro que está na hierarquia do diretório a ser modificado. Aborde a questão da permissão e, em seguida, opte por retomar o processo a partir do ponto de falha, utilizando um token de continuação, ou reiniciar o processo desde o início. Não tens de usar o símbolo de continuação se preferires recomeçar desde o início. Pode re-aplicar as entradas ACL sem qualquer impacto negativo.

Este exemplo devolve um sinal de continuação em caso de falha. A aplicação pode voltar a chamar este método de exemplo depois de o erro ter sido abordado, e passar no token de continuação. Se este método de exemplo for chamado pela primeira vez, a aplicação pode passar num valor para ``None`` o parâmetro simbólico de continuação.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ResumeContinuationToken":::

Para ver um exemplo que processa os ACLs de forma recursiva em lotes especificando um tamanho do lote, consulte a [amostra](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)de pitão .

Se quiser que o processo seja concluído ininterruptamente por erros de permissão, pode especificar isso.

Para garantir que o processo esteja concluído ininterruptamente, não passe um sinal de continuação no método **DataLakeDirectoryClient.set_access_control_recursive.**

Este exemplo define as entradas ACL de forma recursiva. Se este código encontrar um erro de permissão, regista essa falha e continua a execução. Este exemplo imprime o número de falhas na consola.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ContinueOnFailure":::

Para ver um exemplo que processa os ACLs de forma recursiva em lotes especificando um tamanho do lote, consulte a [amostra](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)de pitão .

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Ver também

- [Documentação de referência da API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [Pacote (Índice de Pacote Python)](https://pypi.org/project/azure-storage-file-datalake/)
- [Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Mapeamento da Gen1 para a Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Dar feedback](https://github.com/Azure/azure-sdk-for-python/issues)
- [Modelo de controlo de acesso em Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Listas de controlo de acesso (ACLs) em Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)