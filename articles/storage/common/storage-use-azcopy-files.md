---
title: Transferir dados de ou para ficheiros Azure utilizando o AzCopy v10  Microsoft Docs
description: Transferir dados com AzCopy e armazenamento de ficheiros.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 8aa0e5304825b3f016694a40b3fc1e176518237a
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526693"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Transferir dados com a AzCopy e armazenamento de ficheiros 

O AzCopy é um utilitário de linha de comando que pode utilizar para copiar bolhas ou ficheiros de ou para uma conta de armazenamento. Este artigo contém comandos de exemplo que funcionam com ficheiros Azure.

Antes de começar, consulte o Artigo Get started com o artigo [da AzCopy](storage-use-azcopy-v10.md) para baixar o AzCopy e familiarizar-se com a ferramenta.

## <a name="create-file-shares"></a>Criar ações de ficheiros

Pode usar a [azcopy fazer](storage-ref-azcopy-make.md) o comando para criar uma partilha de ficheiros. O exemplo nesta secção cria uma parte de ficheiro chamada `myfileshare`.

> [!TIP]
> Os exemplos nesta secção encerram argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as conchas de comando, exceto na Shell de Comando do Windows (cmd.exe). Se estiver a utilizar uma Shell de Comando windows (cmd.exe), encerre argumentos de caminho com citações duplas (") em vez de citações únicas (').

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>'` |
| **Exemplo** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Para obter documentos de referência detalhados, consulte a [azcopy fazer](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Carregar ficheiros

Pode utilizar o comando de cópia de [azcópia](storage-ref-azcopy-copy.md) para fazer upload de ficheiros e diretórios do seu computador local.

Esta secção contém os exemplos seguintes:

> [!div class="checklist"]
> * Carregar um ficheiro
> * Faça upload de um diretório
> * Faça upload do conteúdo de um diretório
> * Faça upload de um ficheiro específico

> [!NOTE]
> A AzCopy não calcula e armazena automaticamente o código de haxixe md5 do ficheiro. Se quiser que o AzCopy o faça, acomode a bandeira `--put-md5` a cada comando de cópia. Desta forma, quando o ficheiro é descarregado, a AzCopy calcula um haxixe MD5 para dados descarregados e verifica que o haxixe MD5 armazenado na propriedade `Content-md5` do ficheiro corresponde ao haxixe calculado.

Para obter documentos de referência detalhados, consulte a cópia da [azcopy](storage-ref-azcopy-copy.md).

> [!TIP]
> Os exemplos nesta secção encerram argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as conchas de comando, exceto na Shell de Comando do Windows (cmd.exe). Se estiver a utilizar uma Shell de Comando windows (cmd.exe), encerre argumentos de caminho com citações duplas (") em vez de citações únicas (').

### <a name="upload-a-file"></a>Carregar um ficheiro

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **Exemplo** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Também pode fazer o upload de um ficheiro utilizando um símbolo wildcard (*) em qualquer lugar do caminho de ficheiro ou nome de ficheiro. Por exemplo: `'C:\myDirectory\*.txt'`, ou `C:\my*\*.txt`.

### <a name="upload-a-directory"></a>Faça upload de um diretório

Este exemplo copia um diretório (e todos os ficheiros desse diretório) para uma partilha de ficheiros. O resultado é um diretório no ficheiro com o mesmo nome.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Para copiar para um diretório dentro da partilha de ficheiros, apenas especifique o nome desse diretório na sua cadeia de comando.

|    |     |
|--------|-----------|
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Se especificar o nome de um diretório que não existe na partilha de ficheiros, a AzCopy cria um novo diretório com esse nome.

### <a name="upload-the-contents-of-a-directory"></a>Faça upload do conteúdo de um diretório

Pode fazer o upload do conteúdo de um diretório sem copiar o próprio diretório contendo utilizando o símbolo wildcard (*).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Acomode a bandeira `--recursive` para carregar ficheiros em todos os subdiretórios.

### <a name="upload-specific-files"></a>Carregar ficheiros específicos

Pode especificar nomes completos de ficheiros ou usar nomes parciais com caracteres wildcard (*).

#### <a name="specify-multiple-complete-file-names"></a>Especificar vários nomes completos de ficheiros

Utilize o comando de cópia de [azcopia](storage-ref-azcopy-copy.md) com a opção `--include-path`. Separe os nomes individuais de ficheiros utilizando um ponto e vírgula (`;`).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

Neste exemplo, a AzCopy transfere o diretório `C:\myDirectory\photos` e o ficheiro `C:\myDirectory\documents\myFile.txt`. Você precisa incluir a opção `--recursive` para transferir todos os ficheiros no diretório `C:\myDirectory\photos`.

Também pode excluir ficheiros utilizando a opção `--exclude-path`. Para saber mais, consulte os docs de referência de cópia de [azcopia.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Use caracteres wildcard

Utilize o comando de cópia de [azcopia](storage-ref-azcopy-copy.md) com a opção `--include-pattern`. Especifique nomes parciais que incluam os caracteres wildcard. Nomes separados utilizando um semicolin (`;`).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

Também pode excluir ficheiros utilizando a opção `--exclude-pattern`. Para saber mais, consulte os docs de referência de cópia de [azcopia.](storage-ref-azcopy-copy.md)

As opções `--include-pattern` e `--exclude-pattern` aplicam-se apenas aos nomes de ficheiros e não ao caminho.  Se quiser copiar todos os ficheiros de texto que existem numa árvore de diretório, use a opção `–recursive` para obter toda a árvore do diretório e, em seguida, use o `–include-pattern` e especifique `*.txt` para obter todos os ficheiros de texto.

## <a name="download-files"></a>Transferir ficheiros

Pode utilizar o comando de cópia de [azcopy](storage-ref-azcopy-copy.md) para transferir ficheiros, diretórios e partilhas de ficheiros para o seu computador local.

Esta secção contém os exemplos seguintes:

> [!div class="checklist"]
> * Transferir um ficheiro
> * Baixar um diretório
> * Descarregue o conteúdo de um diretório
> * Descarregue ficheiros específicos

> [!NOTE]
> Se o valor de propriedade `Content-md5` de um ficheiro contiver um haxixe, a AzCopy calcula um haxixe MD5 para dados descarregados e verifica que o haxixe MD5 armazenado na propriedade `Content-md5` do ficheiro corresponde ao haxixe calculado. Se estes valores não corresponderem, o download falha a menos que se sobrecarregue este comportamento, anexando `--check-md5=NoCheck` ou `--check-md5=LogOnly` ao comando de cópia.

Para obter documentos de referência detalhados, consulte a cópia da [azcopy](storage-ref-azcopy-copy.md).

> [!TIP]
> Os exemplos nesta secção encerram argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as conchas de comando, exceto na Shell de Comando do Windows (cmd.exe). Se estiver a utilizar uma Shell de Comando windows (cmd.exe), encerre argumentos de caminho com citações duplas (") em vez de citações únicas (').

### <a name="download-a-file"></a>Transferir um ficheiro

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Baixar um diretório

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

Este exemplo resulta num diretório chamado `C:\myDirectory\myFileShareDirectory` que contém todos os ficheiros descarregados.

### <a name="download-the-contents-of-a-directory"></a>Descarregue o conteúdo de um diretório

Pode descarregar o conteúdo de um diretório sem copiar o próprio diretório contendo utilizando o símbolo wildcard (*).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Anexar a bandeira `--recursive` para descarregar ficheiros em todos os subdiretórios.

### <a name="download-specific-files"></a>Descarregue ficheiros específicos

Pode especificar nomes completos de ficheiros ou usar nomes parciais com caracteres wildcard (*).

#### <a name="specify-multiple-complete-file-names"></a>Especificar vários nomes completos de ficheiros

Utilize o comando de cópia de [azcopia](storage-ref-azcopy-copy.md) com a opção `--include-path`. Separe os nomes individuais de ficheiros utilizando um semicolin (`;`).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

Neste exemplo, a AzCopy transfere o diretório `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` e o ficheiro `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt`. Você precisa incluir a opção `--recursive` para transferir todos os ficheiros no diretório `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos`.

Também pode excluir ficheiros utilizando a opção `--exclude-path`. Para saber mais, consulte os docs de referência de cópia de [azcopia.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Use caracteres wildcard

Utilize o comando de cópia de [azcopia](storage-ref-azcopy-copy.md) com a opção `--include-pattern`. Especifique nomes parciais que incluam os caracteres wildcard. Nomes separados utilizando um semicolin (`;`).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Também pode excluir ficheiros utilizando a opção `--exclude-pattern`. Para saber mais, consulte os docs de referência de cópia de [azcopia.](storage-ref-azcopy-copy.md)

As opções `--include-pattern` e `--exclude-pattern` aplicam-se apenas aos nomes de ficheiros e não ao caminho.  Se quiser copiar todos os ficheiros de texto que existem numa árvore de diretório, use a opção `–recursive` para obter toda a árvore do diretório e, em seguida, use o `–include-pattern` e especifique `*.txt` para obter todos os ficheiros de texto.

## <a name="copy-files-between-storage-accounts"></a>Copiar ficheiros entre contas de armazenamento

Pode utilizar o AzCopy para copiar ficheiros para outras contas de armazenamento. A operação de cópia é sincronizada, por isso, quando o comando regressa, isso indica que todos os ficheiros foram copiados.

O AzCopy utiliza [APIs](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url) [servidor-a-servidor](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) , por isso os dados são copiados diretamente entre servidores de armazenamento. Estas operações de cópia não utilizam a largura de banda da rede do seu computador. Pode aumentar a entrada destas operações, fixando o valor da variável ambiente `AZCOPY_CONCURRENCY_VALUE`. Para saber mais, consulte a [entrada do Otimize.](storage-use-azcopy-configure.md#optimize-throughput)

Esta secção contém os exemplos seguintes:

> [!div class="checklist"]
> * Copiar um ficheiro para outra conta de armazenamento
> * Copiar um diretório para outra conta de armazenamento
> * Copiar uma parte de ficheiro para outra conta de armazenamento
> * Copie todas as ações de ficheiros, diretórios e ficheiros para outra conta de armazenamento

Para obter documentos de referência detalhados, consulte a cópia da [azcopy](storage-ref-azcopy-copy.md).

> [!TIP]
> Os exemplos nesta secção encerram argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as conchas de comando, exceto na Shell de Comando do Windows (cmd.exe). Se estiver a utilizar uma Shell de Comando windows (cmd.exe), encerre argumentos de caminho com citações duplas (") em vez de citações únicas (').

### <a name="copy-a-file-to-another-storage-account"></a>Copiar um ficheiro para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Copiar um diretório para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>Copiar uma parte de ficheiro para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Copie todas as ações de ficheiros, diretórios e ficheiros para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>Synchronize ficheiros

Pode sincronizar o conteúdo de uma partilha de ficheiros com outra partilha de ficheiros. Também pode sincronizar o conteúdo de um diretório numa partilha de ficheiros com o conteúdo de um diretório que está localizado noutra partilha de ficheiros. A sincronização é só de ida. Por outras palavras, escolhe-se qual destes dois pontos finais é a fonte e qual é o destino. A sincronização também utiliza o servidor para o servidor APIs.

> [!NOTE]
> Atualmente, este cenário é suportado apenas para contas que não têm um espaço de nome hierárquico. O lançamento atual do AzCopy não sincroniza entre o Azure Files e o Blob Storage.

O comando [de sincronização](storage-ref-azcopy-sync.md) compara nomes de ficheiros e últimos selos de tempo modificados. Detete a bandeira `--delete-destination` opcional num valor de `true` ou `prompt` para apagar ficheiros no diretório de destino se esses ficheiros já não existirem no diretório de origem.

Se definir a bandeira `--delete-destination` para `true` a AzCopy elimina ficheiros sem fornecer uma solicitação. Se desejar que apareça uma solicitação antes de o AzCopy apagar um ficheiro, desloque a bandeira `--delete-destination` para `prompt`.

Para obter documentos de referência detalhados, consulte a sincronização da [azcopy](storage-ref-azcopy-sync.md).

> [!TIP]
> Os exemplos nesta secção encerram argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as conchas de comando, exceto na Shell de Comando do Windows (cmd.exe). Se estiver a utilizar uma Shell de Comando windows (cmd.exe), encerre argumentos de caminho com citações duplas (") em vez de citações únicas (').

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Atualizar uma partilha de ficheiros com alterações a outra parte de ficheiro

A primeira partilha de ficheiros que aparece neste comando é a fonte. O segundo é o destino.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Atualizar um diretório com alterações a um diretório em outra partilha de ficheiros

O primeiro diretório que aparece neste comando é a fonte. O segundo é o destino.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="next-steps"></a>Passos seguintes

Encontre mais exemplos em qualquer um destes artigos:

- [Começar com a AzCopy](storage-use-azcopy-v10.md)

- [Transferir dados com armazenamento azCopy e blob](storage-use-azcopy-blobs.md)

- [Transferir dados com baldes AzCopy e Amazon S3](storage-use-azcopy-s3.md)

- [Configure, otimize e problemas AzCopy](storage-use-azcopy-configure.md)
