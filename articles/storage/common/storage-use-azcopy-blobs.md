---
title: Transferir dados de ou para armazenamento de Blobs do Azure utilizando o AzCopy v10 | Documentos da Microsoft
description: Este artigo contém uma coleção de AzCopy exemplo comandos que o ajudam a criar contentores, copiam arquivos e sincronize diretórios entre sistemas de ficheiros local e de contentores.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 83e32a1e8f77604330a9f3aba0e011a0a0851e2f
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625615"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Transferir dados com AzCopy e armazenamento de BLOBs

O AzCopy é um utilitário de linha de comandos que pode utilizar para copiar dados para, de ou entre contas de armazenamento. Este artigo contém comandos de exemplo que funcionam com o armazenamento de Blobs.

## <a name="get-started"></a>Introdução

Consulte a [introdução ao AzCopy](storage-use-azcopy-v10.md) artigo para transferir o AzCopy e conheça as formas que pode fornecer credenciais de autorização para o serviço de armazenamento.

> [!NOTE]
> Os exemplos neste artigo partem do princípio de que foi autenticado sua identidade através do `AzCopy login` comando. O AzCopy, em seguida, utiliza a conta do Azure AD para autorizar o acesso aos dados no armazenamento de Blobs.
>
> Se usaria um token SAS em vez disso, para autorizar o acesso aos dados de BLOBs, em seguida, pode acrescentar esse token para o URL de recurso em cada comando do AzCopy.
>
> Por exemplo: `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"`.

## <a name="create-a-container"></a>Criar um contentor

Pode utilizar o AzCopy `make` comando para criar um contentor. Os exemplos nesta secção criar um contentor com o nome `mycontainer`.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **Exemplo** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **Exemplo** (espaço de nomes hierárquico) | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>Carregar ficheiros

Pode utilizar o AzCopy `copy` comando para carregar ficheiros e diretórios do computador local.

Esta secção contém os exemplos seguintes:

> [!div class="checklist"]
> * Carregar um ficheiro
> * Carregar um diretório
> * Carregar ficheiros através da utilização de carateres universais

> [!NOTE]
> AzCopy não calcular e armazenar o código de hash md5 do arquivo automaticamente. Se pretender que o AzCopy para fazer isso, em seguida, anexe o `--put-md5` sinalizador para cada comando de cópia. Dessa forma, quando o blob é baixado, AzCopy calcula um hash MD5 para transferir dados e verifica que o hash MD5 armazenados no blob de `Content-md5` propriedade coincide com o hash calculado.

### <a name="upload-a-file"></a>Carregar um ficheiro

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **Exemplo** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **Exemplo** (espaço de nomes hierárquico) | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> AzCopy por predefinição carrega dados para blobs de blocos. Para carregar ficheiros como Blobs de acréscimo, ou os Blobs de página, utilize o sinalizador `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

### <a name="upload-a-directory"></a>Carregar um diretório

Neste exemplo copia um diretório (e todos os ficheiros nesse diretório) para um contentor de Blobs. O resultado é um diretório no contentor com o mesmo nome.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Exemplo** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Exemplo** (espaço de nomes hierárquico) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

Para copiar para um diretório dentro do contêiner, basta Especifica o nome do diretório na sua cadeia de caracteres de comando.

|    |     |
|--------|-----------|
| **Exemplo** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |
| **Exemplo** (espaço de nomes hierárquico) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

Se especificar o nome de um diretório que não existe no contentor, o AzCopy cria um novo diretório com esse nome.

### <a name="upload-the-contents-of-a-directory"></a>Carregue o conteúdo de um diretório

Pode carregar o conteúdo de um diretório sem copiar o próprio diretório que contém usando o símbolo de caráter universal (*).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "<local-directory-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **Exemplo** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory"` |
| **Exemplo** (espaço de nomes hierárquico) | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory"` |

> [!NOTE]
> Acrescentar a `--recursive` sinalizador para carregar ficheiros em todos os subdiretórios.

## <a name="download-files"></a>Transferir ficheiros

Pode utilizar o AzCopy `copy` comando para transferir blobs, diretórios e contentores para o computador local.

Esta secção contém os exemplos seguintes:

> [!div class="checklist"]
> * Transferir um ficheiro
> * Transferir um diretório
> * Transferir ficheiros utilizando carateres universais

> [!NOTE]
> Se o `Content-md5` valor de propriedade de um blob contém um hash, o AzCopy calcula um hash MD5 para dados transferidos e verifica que o hash MD5 armazenados no blob de `Content-md5` propriedade coincide com o hash calculado. Se estes valores não corresponderem, o download falha, a menos que substituir este comportamento ao acrescentar `--check-md5=NoCheck` ou `--check-md5=LogOnly` para o comando de cópia.

### <a name="download-a-file"></a>Transferir um ficheiro

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **Exemplo** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |
| **Exemplo** (espaço de nomes hierárquico) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Transferir um diretório

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>" "<local-directory-path>" --recursive` |
| **Exemplo** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |
| **Exemplo** (espaço de nomes hierárquico) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |

Neste exemplo resulta num diretório chamado `C:\myDirectory\myBlobDirectory` que contém todos os ficheiros transferidos.

### <a name="download-the-contents-of-a-directory"></a>Transferir o conteúdo de um diretório

Pode transferir o conteúdo de um diretório sem copiar o próprio diretório que contém usando o símbolo de caráter universal (*).

> [!NOTE]
> Atualmente, este cenário é suportado apenas para contas que não têm um espaço de nomes hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` |
| **Exemplo** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*" "C:\myDirectory"` |

> [!NOTE]
> Acrescentar a `--recursive` sinalizador para transferir os ficheiros em todos os subdiretórios.

## <a name="copy-blobs-between-storage-accounts"></a>Copiar blobs entre contas de armazenamento

Pode utilizar o AzCopy para copiar blobs para outras contas de armazenamento. A operação de cópia é síncrona, portanto, quando o comando retorna, o que indica que todos os ficheiros foram copiados.

> [!NOTE]
> Atualmente, este cenário é suportado apenas para contas que não têm um espaço de nomes hierárquico. 

AzCopy utiliza a [colocar o bloco de URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API, para que os dados são copiados diretamente entre os servidores de armazenamento. Estas operações de cópia não utilizam a largura de banda de rede do seu computador.

Esta secção contém os exemplos seguintes:

> [!div class="checklist"]
> * Copiar um blob para outra conta de armazenamento
> * Copiar um diretório para outra conta de armazenamento
> * Copiar um contentores para outra conta de armazenamento
> * Copiar todos os contentores, diretórios e ficheiros para outra conta de armazenamento

> [!NOTE]
> Na versão atual, precisa acrescentar um token SAS para cada URL de origem. Se fornecer credenciais de autorização com o Azure Active Directory (AD), pode omitir o token SAS apenas a partir do URL de destino. 

### <a name="copy-a-blob-to-another-storage-account"></a>Copiar um blob para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **Exemplo** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-directory-to-another-storage-account"></a>Copiar um diretório para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>" --recursive` |
| **Exemplo** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>Copiar um contentores para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Exemplo** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-directories-and-files-to-another-storage-account"></a>Copiar todos os contentores, diretórios e ficheiros para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **Exemplo** | `azcopy cp "https://mysourceaccount.blob.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>Sincronizar ficheiros

Pode sincronizar o conteúdo de um sistema de arquivos local com um contentor de Blobs. A sincronização é unidirecional. Em outras palavras, escolher qual desses dois pontos de extremidade é a origem e qual é o destino.

> [!NOTE]
> Atualmente, este cenário é suportado apenas para contas que não têm um espaço de nomes hierárquico. A versão atual do AzCopy não sincronizadas entre outras origens e destinos (por exemplo: Armazenamento de ficheiros ou registos do Amazon Web Services (AWS) S3).

O `sync` comando compara nomes de ficheiros e modificada pela última vez carimbos. Definir o `--delete-destination` sinalizador opcional para um valor de `true` ou `prompt` para eliminar ficheiros no diretório de destino, se esses arquivos já não existirem no diretório de origem.

Se definir o `--delete-destination` sinalizador para `true` AzCopy elimina ficheiros sem fornecer uma linha de comandos. Se pretender uma linha de comandos para aparecer antes AzCopy elimina um ficheiro, defina o `--delete-destination` sinalizador para `prompt`.

> [!NOTE]
> Para impedir eliminações acidentais, certifique-se de ativar a [eliminação de forma recuperável](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) funcionalidade antes de utilizar o `--delete-destination=prompt|true` sinalizador.

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Atualizar um contentor com as alterações para um sistema de ficheiros local

Neste caso, o contêiner é o destino e o sistema de arquivos local é a origem.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Exemplo** | `azcopy sync "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Atualizar um sistema de arquivos local com as alterações a um contentor

Neste caso, o sistema de arquivos local é o destino e o contentor é a origem.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myDirectory" --recursive` |
| **Exemplo** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myDirectory" --recursive` |
|

## <a name="next-steps"></a>Passos Seguintes

Encontre mais exemplos em qualquer um dos seguintes artigos:

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)

- [Tutorial: Migrar os dados no local para o armazenamento na nuvem com o AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Transferir dados com AzCopy e o ficheiro de armazenamento](storage-use-azcopy-files.md)

- [Transferir dados com AzCopy e o Amazon S3 registos](storage-use-azcopy-s3.md)

- [Configurar, otimizar e resolver problemas relacionados com o AzCopy](storage-use-azcopy-configure.md)
