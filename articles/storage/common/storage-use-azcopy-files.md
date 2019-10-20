---
title: Transferir dados de ou para arquivos do Azure usando AzCopy v10 | Microsoft Docs
description: Transferir dados com o AzCopy e o armazenamento de arquivos.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 3285a0a27455bde6d107451ac803872b9fba3280
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598047"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Transferir dados com o AzCopy e o armazenamento de arquivos 

AzCopy é um utilitário de linha de comando que você pode usar para copiar BLOBs ou arquivos de ou para uma conta de armazenamento. Este artigo contém comandos de exemplo que funcionam com os arquivos do Azure.

Antes de começar, consulte o artigo [introdução ao AzCopy](storage-use-azcopy-v10.md) para baixar o AzCopy e se familiarizar com a ferramenta.

## <a name="create-file-shares"></a>Criar compartilhamentos de arquivos

Você pode usar o comando [azcopy Make](storage-ref-azcopy-make.md) para criar um compartilhamento de arquivos. O exemplo nesta seção cria um compartilhamento de arquivos chamado `myfileshare`.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>'` |
| **Exemplo** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Para obter documentos de referência detalhados, consulte [Make azcopy](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Carregar ficheiros

Você pode usar o comando [azcopy Copy](storage-ref-azcopy-copy.md) para carregar arquivos e diretórios do seu computador local.

Esta seção contém os seguintes exemplos:

> [!div class="checklist"]
> * Carregar um ficheiro
> * Carregar um diretório
> * Carregar o conteúdo de um diretório
> * Carregar um arquivo específico

> [!NOTE]
> AzCopy não calcula e armazena automaticamente o código hash MD5 do arquivo. Se você quiser que o AzCopy faça isso, anexe o sinalizador `--put-md5` a cada comando de cópia. Dessa forma, quando o arquivo for baixado, AzCopy calculará um hash MD5 para dados baixados e verificará se o hash MD5 armazenado na propriedade `Content-md5` do arquivo corresponde ao hash calculado.

Para documentos de referência detalhados, consulte [cópia azcopy](storage-ref-azcopy-copy.md).

### <a name="upload-a-file"></a>Carregar um ficheiro

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>'` |
| **Exemplo** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Você também pode carregar um arquivo usando um símbolo curinga (*) em qualquer lugar no caminho do arquivo ou nome do arquivo. Por exemplo: `'C:\myDirectory\*.txt'` ou `C:\my*\*.txt`.

### <a name="upload-a-directory"></a>Carregar um diretório

Este exemplo copia um diretório (e todos os arquivos nesse diretório) para um compartilhamento de arquivos. O resultado é um diretório no compartilhamento de arquivos com o mesmo nome.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Para copiar para um diretório dentro do compartilhamento de arquivos, basta especificar o nome desse diretório na cadeia de caracteres de comando.

|    |     |
|--------|-----------|
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Se você especificar o nome de um diretório que não existe no compartilhamento de arquivos, o AzCopy criará um novo diretório com esse nome.

### <a name="upload-the-contents-of-a-directory"></a>Carregar o conteúdo de um diretório

Você pode carregar o conteúdo de um diretório sem copiar o próprio diretório contido usando o símbolo curinga (*).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Acrescente o sinalizador `--recursive` para carregar arquivos em todos os subdiretórios.

### <a name="upload-specific-files"></a>Carregar arquivos específicos

Você pode especificar nomes de arquivo completos ou usar nomes parciais com caracteres curinga (*).

#### <a name="specify-multiple-complete-file-names"></a>Especificar vários nomes de arquivo completos

Use o comando [azcopy Copy](storage-ref-azcopy-copy.md) com a opção `--include-path`. Separe os nomes de arquivo individuais usando um ponto-e-vírgula (`;`).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>' --include-path <semicolon-separated-file-list>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare' --include-path 'photos;documents\myFile.txt'` |

Neste exemplo, AzCopy transfere o diretório `C:\myDirectory\photos` e o arquivo `C:\myDirectory\documents\myFile.txt`. Você precisa incluir a opção `--recursive` para transferir todos os arquivos no diretório `C:\myDirectory\photos`.

Você também pode excluir arquivos usando a opção `--exclude-path`. Para saber mais, consulte [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Usar caracteres curinga

Use o comando [azcopy Copy](storage-ref-azcopy-copy.md) com a opção `--include-pattern`. Especifique nomes parciais que incluam os caracteres curinga. Separe os nomes usando um semicolin (`;`).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare' --include-pattern 'myFile*.txt;*.pdf*'` |

Você também pode excluir arquivos usando a opção `--exclude-pattern`. Para saber mais, consulte [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

As opções `--include-pattern` e `--exclude-pattern` se aplicam somente a nomes de filename e não ao caminho.  Se você quiser copiar todos os arquivos de texto que existem em uma árvore de diretório, use a opção `–recursive` para obter a árvore de diretórios inteira e use o `–include-pattern` e especifique `*.txt` para obter todos os arquivos de texto.

## <a name="download-files"></a>Transferir ficheiros

Você pode usar o comando [azcopy Copy](storage-ref-azcopy-copy.md) para baixar arquivos, diretórios e compartilhamentos de arquivos em seu computador local.

Esta seção contém os seguintes exemplos:

> [!div class="checklist"]
> * Transferir um ficheiro
> * Baixar um diretório
> * Baixar o conteúdo de um diretório
> * Baixar arquivos específicos

> [!NOTE]
> Se o valor da propriedade `Content-md5` de um arquivo contiver um hash, AzCopy calculará um hash MD5 para os dados baixados e verificará se o hash MD5 armazenado na propriedade `Content-md5` do arquivo corresponde ao hash calculado. Se esses valores não corresponderem, o download falhará, a menos que você substitua esse comportamento acrescentando `--check-md5=NoCheck` ou `--check-md5=LogOnly` ao comando de cópia.

Para documentos de referência detalhados, consulte [cópia azcopy](storage-ref-azcopy-copy.md).

### <a name="download-a-file"></a>Transferir um ficheiro

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' '<local-file-path>'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Baixar um diretório

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' '<local-directory-path>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

Este exemplo resulta em um diretório chamado `C:\myDirectory\myFileShareDirectory` que contém todos os arquivos baixados.

### <a name="download-the-contents-of-a-directory"></a>Baixar o conteúdo de um diretório

Você pode baixar o conteúdo de um diretório sem copiar o próprio diretório contido usando o símbolo curinga (*).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>' '<local-directory-path>/'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Acrescente o sinalizador `--recursive` para baixar arquivos em todos os subdiretórios.

### <a name="download-specific-files"></a>Baixar arquivos específicos

Você pode especificar nomes de arquivo completos ou usar nomes parciais com caracteres curinga (*).

#### <a name="specify-multiple-complete-file-names"></a>Especificar vários nomes de arquivo completos

Use o comando [azcopy Copy](storage-ref-azcopy-copy.md) com a opção `--include-path`. Separe os nomes de arquivo individuais usando um semicolin (`;`).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

Neste exemplo, AzCopy transfere o diretório `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` e o arquivo `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt`. Você precisa incluir a opção `--recursive` para transferir todos os arquivos no diretório `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos`.

Você também pode excluir arquivos usando a opção `--exclude-path`. Para saber mais, consulte [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Usar caracteres curinga

Use o comando [azcopy Copy](storage-ref-azcopy-copy.md) com a opção `--include-pattern`. Especifique nomes parciais que incluam os caracteres curinga. Separe os nomes usando um semicolin (`;`).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Você também pode excluir arquivos usando a opção `--exclude-pattern`. Para saber mais, consulte [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

As opções `--include-pattern` e `--exclude-pattern` se aplicam somente a nomes de filename e não ao caminho.  Se você quiser copiar todos os arquivos de texto que existem em uma árvore de diretório, use a opção `–recursive` para obter a árvore de diretórios inteira e use o `–include-pattern` e especifique `*.txt` para obter todos os arquivos de texto.

## <a name="copy-files-between-storage-accounts"></a>Copiar arquivos entre contas de armazenamento

Você pode usar o AzCopy para copiar arquivos para outras contas de armazenamento. A operação de cópia é síncrona, portanto, quando o comando retorna, isso indica que todos os arquivos foram copiados.

O AzCopy usa [APIs](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)de [servidor para servidor](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) , portanto, os dados são copiados diretamente entre os servidores de armazenamento. Essas operações de cópia não usam a largura de banda de rede do seu computador. Você pode aumentar a taxa de transferência dessas operações definindo o valor da variável de ambiente `AZCOPY_CONCURRENCY_VALUE`. Para saber mais, consulte [otimizar a taxa de transferência](storage-use-azcopy-configure.md#optimize-throughput).

Esta seção contém os seguintes exemplos:

> [!div class="checklist"]
> * Copiar um arquivo para outra conta de armazenamento
> * Copiar um diretório para outra conta de armazenamento
> * Copiar um compartilhamento de arquivos para outra conta de armazenamento
> * Copiar todos os compartilhamentos de arquivos, diretórios e arquivos para outra conta de armazenamento

Para obter documentos de referência detalhados, consulte [cópia azcopy](storage-ref-azcopy-copy.md).

### <a name="copy-a-file-to-another-storage-account"></a>Copiar um arquivo para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>'` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Copiar um diretório para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>Copiar um compartilhamento de arquivos para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Copiar todos os compartilhamentos de arquivos, diretórios e arquivos para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/' --recursive'` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Sincronizar arquivos

Você pode sincronizar o conteúdo de um compartilhamento de arquivos com outro compartilhamento de arquivos. Você também pode sincronizar o conteúdo de um diretório em um compartilhamento de arquivos com o conteúdo de um diretório localizado em outro compartilhamento de arquivos. A sincronização é unidirecional. Em outras palavras, você escolhe quais desses dois pontos de extremidade são a origem e qual deles é o destino. A sincronização também usa APIs de servidor para servidor.

> [!NOTE]
> Atualmente, esse cenário tem suporte apenas para contas que não têm um namespace hierárquico. A versão atual do AzCopy não é sincronizada entre os arquivos do Azure e o armazenamento de BLOBs.

O comando [Sync](storage-ref-azcopy-sync.md) compara os nomes de arquivo e os últimos carimbos de data/hora. Defina o sinalizador opcional `--delete-destination` como um valor de `true` ou `prompt` para excluir arquivos no diretório de destino se esses arquivos não existirem mais no diretório de origem.

Se você definir o sinalizador `--delete-destination` como `true` AzCopy excluirá arquivos sem fornecer um prompt. Se você quiser que um prompt apareça antes de AzCopy excluir um arquivo, defina o sinalizador `--delete-destination` como `prompt`.

Para obter documentos de referência detalhados, consulte [azcopy Sync](storage-ref-azcopy-sync.md).

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Atualizar um compartilhamento de arquivos com alterações em outro compartilhamento de arquivos

O primeiro compartilhamento de arquivos que aparece nesse comando é a origem. O segundo é o destino.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Atualizar um diretório com alterações em um diretório em outro compartilhamento de arquivos

O primeiro diretório que aparece nesse comando é a origem. O segundo é o destino.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory' --recursive` |

## <a name="next-steps"></a>Passos seguintes

Encontre mais exemplos em qualquer um destes artigos:

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)

- [Transferir dados com o armazenamento de BLOBs e AzCopy](storage-use-azcopy-blobs.md)

- [Transferir dados com os buckets AzCopy e Amazon S3](storage-use-azcopy-s3.md)

- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)