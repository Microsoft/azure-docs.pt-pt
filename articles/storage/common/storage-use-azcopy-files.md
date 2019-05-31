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
ms.openlocfilehash: 5d11d5c0da350b9abf2e2b06a11a1690cf3f6922
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247113"
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

Pode utilizar o AzCopy `copy` comando para carregar ficheiros e pastas do computador local.

Esta secção contém os exemplos seguintes:

> [!div class="checklist"]
> * Carregar um ficheiro
> * Carregar uma pasta
> * Carregar ficheiros através da utilização de carateres universais

> [!NOTE]
> AzCopy não calcular e armazenar o código de hash md5 do arquivo automaticamente. Se pretender que o AzCopy para fazer isso, em seguida, anexe o `--put-md5` sinalizador para cada comando de cópia. Dessa forma, quando o arquivo é baixado, AzCopy calcula um hash MD5 para transferir dados e verifica que o hash MD5 armazenados no arquivo de `Content-md5` propriedade coincide com o hash calculado.

### <a name="upload-a-file"></a>Carregar um ficheiro

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **Exemplo** | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-folder"></a>Carregar uma pasta

Neste exemplo copia uma pasta (e todos os ficheiros nessa pasta) para uma partilha de ficheiros. O resultado é uma pasta na partilha de ficheiros com o mesmo nome.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "<local-folder-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **Exemplo** | `azcopy copy "C:\myFolder" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Para copiar para uma pasta na partilha de ficheiros, basta Especifica o nome da pasta na sua cadeia de caracteres de comando.

|    |     |
|--------|-----------|
| **Exemplo** | `azcopy copy "C:\myFolder" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Se especificar o nome de uma pasta que não existe na partilha de ficheiros, o AzCopy cria uma nova pasta com esse nome.

### <a name="upload-the-contents-of-a-folder"></a>Carregue o conteúdo de uma pasta

Pode carregar o conteúdo de uma pasta sem copiar a pasta que contém em si, utilizando o símbolo de caráter universal (*).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "<local-folder-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<folder-path>?<SAS-token>` |
| **Exemplo** | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Acrescentar a `--recursive` sinalizador para carregar ficheiros em todas as subpastas.

## <a name="download-files"></a>Transferir ficheiros

Pode utilizar o AzCopy `copy` partilhas de comando para transferir os ficheiros, pastas e ficheiros para o computador local.

Esta secção contém os exemplos seguintes:

> [!div class="checklist"]
> * Transferir um ficheiro
> * Uma pasta de transferência
> * Transferir ficheiros utilizando carateres universais

> [!NOTE]
> Se o `Content-md5` valor de propriedade de um ficheiro contém um hash, o AzCopy calcula um hash MD5 para dados transferidos e verifica que o hash MD5 armazenados no arquivo de `Content-md5` propriedade coincide com o hash calculado. Se estes valores não corresponderem, o download falha, a menos que substituir este comportamento ao acrescentar `--check-md5=NoCheck` ou `--check-md5=LogOnly` para o comando de cópia.

### <a name="download-a-file"></a>Transferir um ficheiro

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **Exemplo** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder\myTextFile.txt"` |

### <a name="download-a-folder"></a>Uma pasta de transferência

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<folder-path>?<SAS-token>" "<local-folder-path>" --recursive` |
| **Exemplo** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder"  --recursive` |

Neste exemplo resulta numa pasta chamada `C:\myFolder\myFileShareFolder` que contém todos os ficheiros transferidos.

### <a name="download-the-contents-of-a-folder"></a>Transferir o conteúdo de uma pasta

Pode transferir o conteúdo de uma pasta sem copiar a pasta que contém em si, utilizando o símbolo de caráter universal (*).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-folder-path>/"` |
| **Exemplo** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder"` |

> [!NOTE]
> Acrescentar a `--recursive` sinalizador para transferir os ficheiros em todas as subpastas.

## <a name="next-steps"></a>Passos Seguintes

Encontre mais exemplos em qualquer um dos seguintes artigos:

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)

- [Transferir dados com AzCopy e armazenamento de BLOBs](storage-use-azcopy-blobs.md)

- [Transferir dados com AzCopy e o Amazon S3 registos](storage-use-azcopy-s3.md)

- [Configurar, otimizar e resolver problemas relacionados com o AzCopy](storage-use-azcopy-configure.md)