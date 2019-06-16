---
title: Transferir dados de ficheiros do Azure ou para utilizando o AzCopy v10 | Documentos da Microsoft
description: Transferir dados com AzCopy e o ficheiro de armazenamento.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 69d7136396c3d989e63b8956d3e703cc7f9666c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66687942"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Transferir dados com AzCopy e o ficheiro de armazenamento 

O AzCopy é um utilitário de linha de comandos que pode utilizar para copiar blobs ou ficheiros de ou para uma conta de armazenamento. Este artigo contém comandos de exemplo que funcionam com o serviço ficheiros do Azure.

Antes de começar, consulte a [introdução ao AzCopy](storage-use-azcopy-v10.md) artigo para transferir o AzCopy e se familiarize com a ferramenta.

## <a name="create-file-shares"></a>Criar partilhas de ficheiros

Pode utilizar o AzCopy `make` comando para criar uma partilha de ficheiros. O exemplo nesta secção cria uma partilha de ficheiros com o nome `myfileshare`.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **Exemplo** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>Carregar ficheiros

Pode utilizar o AzCopy `copy` comando para carregar ficheiros e diretórios do computador local.

Esta secção contém os exemplos seguintes:

> [!div class="checklist"]
> * Carregar um ficheiro
> * Carregar um diretório
> * Carregar ficheiros através da utilização de carateres universais

> [!NOTE]
> AzCopy não calcular e armazenar o código de hash md5 do arquivo automaticamente. Se pretender que o AzCopy para fazer isso, em seguida, anexe o `--put-md5` sinalizador para cada comando de cópia. Dessa forma, quando o arquivo é baixado, AzCopy calcula um hash MD5 para transferir dados e verifica que o hash MD5 armazenados no arquivo de `Content-md5` propriedade coincide com o hash calculado.

### <a name="upload-a-file"></a>Carregar um ficheiro

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **Exemplo** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-directory"></a>Carregar um diretório

Neste exemplo copia um diretório (e todos os ficheiros nesse diretório) para uma partilha de ficheiros. O resultado é um diretório na partilha de ficheiros com o mesmo nome.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **Exemplo** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Para copiar para um diretório numa partilha de ficheiros, basta Especifica o nome do diretório na sua cadeia de caracteres de comando.

|    |     |
|--------|-----------|
| **Exemplo** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Se especificar o nome de um diretório que não existe na partilha de ficheiros, o AzCopy cria um novo diretório com esse nome.

### <a name="upload-the-contents-of-a-directory"></a>Carregue o conteúdo de um diretório

Pode carregar o conteúdo de um diretório sem copiar o próprio diretório que contém usando o símbolo de caráter universal (*).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "<local-directory-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **Exemplo** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Acrescentar a `--recursive` sinalizador para carregar ficheiros em todos os subdiretórios.

## <a name="download-files"></a>Transferir ficheiros

Pode utilizar o AzCopy `copy` partilhas de comando para baixar arquivos, diretórios e ficheiros para o computador local.

Esta secção contém os exemplos seguintes:

> [!div class="checklist"]
> * Transferir um ficheiro
> * Transferir um diretório
> * Transferir ficheiros utilizando carateres universais

> [!NOTE]
> Se o `Content-md5` valor de propriedade de um ficheiro contém um hash, o AzCopy calcula um hash MD5 para dados transferidos e verifica que o hash MD5 armazenados no arquivo de `Content-md5` propriedade coincide com o hash calculado. Se estes valores não corresponderem, o download falha, a menos que substituir este comportamento ao acrescentar `--check-md5=NoCheck` ou `--check-md5=LogOnly` para o comando de cópia.

### <a name="download-a-file"></a>Transferir um ficheiro

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **Exemplo** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Transferir um diretório

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>" "<local-directory-path>" --recursive` |
| **Exemplo** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"  --recursive` |

Neste exemplo resulta num diretório chamado `C:\myDirectory\myFileShareDirectory` que contém todos os ficheiros transferidos.

### <a name="download-the-contents-of-a-directory"></a>Transferir o conteúdo de um diretório

Pode transferir o conteúdo de um diretório sem copiar o próprio diretório que contém usando o símbolo de caráter universal (*).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-directory-path>/"` |
| **Exemplo** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"` |

> [!NOTE]
> Acrescentar a `--recursive` sinalizador para transferir os ficheiros em todos os subdiretórios.

## <a name="next-steps"></a>Passos Seguintes

Encontre mais exemplos em qualquer um dos seguintes artigos:

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)

- [Transferir dados com AzCopy e armazenamento de BLOBs](storage-use-azcopy-blobs.md)

- [Transferir dados com AzCopy e o Amazon S3 registos](storage-use-azcopy-s3.md)

- [Configurar, otimizar e resolver problemas relacionados com o AzCopy](storage-use-azcopy-configure.md)