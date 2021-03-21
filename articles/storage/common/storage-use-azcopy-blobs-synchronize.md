---
title: Sincronizar com o armazenamento Azure Blob utilizando a AzCopy v10 | Microsoft Docs
description: Este artigo contém uma coleção de comandos de exemplo AzCopy que o ajudam a sincronizar com o armazenamento Azure Blob.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: ab1da88899ba2b90e303da107631e3878b3a8b58
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102635880"
---
# <a name="synchronize-with-azure-blob-storage-by-using-azcopy-v10"></a>Sincronizar com o armazenamento Azure Blob utilizando o AzCopy v10

Pode sincronizar o armazenamento local com o armazenamento Azure Blob utilizando o utilitário de linha de comando AzCopy v10. 

Pode sincronizar o conteúdo de um sistema de ficheiros local com um recipiente de bolhas. Também pode sincronizar contentores e diretórios virtuais entre si. A sincronização é uma maneira. Por outras palavras, você escolhe qual destes dois pontos finais é a fonte e qual é o destino. A sincronização também utiliza o servidor para as APIs do servidor. Os exemplos apresentados nesta secção também funcionam com contas que têm um espaço hierárquico de nomes. 

> [!NOTE]
> O lançamento atual do AzCopy não sincroniza entre outras fontes e destinos (por exemplo: armazenamento de ficheiros ou baldes S3 da Amazon Web Services (AWS).

Para ver exemplos de outros tipos de tarefas, como carregar ficheiros, descarregar bolhas ou copiar bolhas entre contas, consulte os links apresentados na secção [Etapas Seguintes](#next-steps) deste artigo.

## <a name="get-started"></a>Introdução

Consulte o [artigo da AzCopy](storage-use-azcopy-v10.md) para descarregar o AzCopy e saiba como pode fornecer credenciais de autorização ao serviço de armazenamento.

> [!NOTE] 
> Os exemplos deste artigo assumem que forneceu credenciais de autorização utilizando o Azure Ative Directory (Azure AD).
>
> Se preferir usar um token SAS para autorizar o acesso a dados blob, então pode anexar esse símbolo ao URL de recursos em cada comando AzCopy. Por exemplo: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="guidelines"></a>Diretrizes

- O comando [de sincronização](storage-ref-azcopy-sync.md) compara nomes de ficheiros e últimos cartões de tempo modificados. Desa estama a `--delete-destination` bandeira opcional a um valor de ou para apagar `true` `prompt` ficheiros no diretório de destino se esses ficheiros já não existirem no diretório de origem.

- Se colocar a `--delete-destination` bandeira para , a `true` AzCopy elimina ficheiros sem fornecer uma solicitação. Se desejar que apareça uma solicitação antes de o AzCopy apagar um ficheiro, coloque a `--delete-destination` bandeira em `prompt` .

- Para evitar supressões acidentais, certifique-se de que ativa a função [de eliminação suave](../blobs/soft-delete-blob-overview.md) antes de utilizar a `--delete-destination=prompt|true` bandeira.

## <a name="update-a-container-with-changes-to-a-local-file-system"></a>Atualizar um recipiente com alterações a um sistema de ficheiros local

Neste caso, o contentor é o destino e o sistema de ficheiros local é a origem. 

> [!TIP]
> Este exemplo encerra argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-local-file-system-with-changes-to-a-container"></a>Atualizar um sistema de ficheiros local com alterações a um contentor

Neste caso, o sistema de ficheiros local é o destino, e o contentor é a fonte.

> [!TIP]
> Este exemplo encerra argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Exemplo** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

## <a name="update-a-container-with-changes-in-another-container"></a>Atualizar um recipiente com alterações em outro recipiente

O primeiro recipiente que aparece neste comando é a fonte. O segundo é o destino.

> [!TIP]
> Este exemplo encerra argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-directory-with-changes-to-a-directory-in-another-container"></a>Atualizar um diretório com alterações a um diretório em outro recipiente

O primeiro diretório que aparece neste comando é a fonte. O segundo é o destino.

> [!TIP]
> Este exemplo encerra argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="synchronize-with-optional-flags"></a>Sincronizar com bandeiras opcionais

Pode ajustar a sua operação de sincronização utilizando bandeiras opcionais. Aqui estão alguns exemplos.

|Scenario|Sinalizador|
|---|---|
|Especifique como os hashes MD5 devem ser validados ao descarregar.|**--check-md5** = \[ NoCheck \| LogOnly \| FailIfDifferent \| FailIfDifferentOrMissing\]|
|Excluir ficheiros com base num padrão.|**--excluir caminho**|
|Especifique o quão detalhado pretende que as suas entradas de registo relacionadas com sincronização sejam.|**--nível** = \[ de log \|INFORMAÇÃO DE ERRO \| DE AVISO \| NENHUMA\]|

Para obter uma lista completa, consulte [as opções.](storage-ref-azcopy-sync.md#options)

## <a name="next-steps"></a>Passos seguintes

Encontre mais exemplos nestes artigos:

- [Exemplos: Carregar](storage-use-azcopy-blobs-upload.md)
- [Exemplos: Transferência](storage-use-azcopy-blobs-download.md)
- [Exemplos: Copiar entre contas](storage-use-azcopy-blobs-copy.md)
- [Exemplos: Registos Amazon S3](storage-use-azcopy-s3.md)
- [Exemplos: Ficheiros Azure](storage-use-azcopy-files.md)
- [Tutorial: migrar dados no local para o armazenamento na cloud com o AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Configurar, otimizar e resolver problemas AzCopy](storage-use-azcopy-configure.md)