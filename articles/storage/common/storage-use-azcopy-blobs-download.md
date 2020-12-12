---
title: Descarregue blobs do armazenamento Azure Blob usando AzCopy v10 Microsoft Docs
description: Este artigo contém uma coleção de comandos de exemplo AzCopy que o ajudam a descarregar bolhas do armazenamento Azure Blob.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: ea8300447b9aa596e8678038982771263a4c76f6
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358780"
---
# <a name="download-blobs-from-azure-blob-storage-by-using-azcopy-v10"></a>Descarregue bolhas do armazenamento Azure Blob usando AzCopy v10

Você pode baixar blobs e diretórios a partir do armazenamento Blob usando o utilitário de linha de comando AzCopy v10. 

Para ver exemplos de outros tipos de tarefas, tais como carregar ficheiros, sincronizar com o armazenamento blob ou copiar bolhas entre contas, consulte os links apresentados na secção [Etapas Seguintes](#next-steps) deste artigo.

## <a name="get-started"></a>Introdução

Consulte o [artigo da AzCopy](storage-use-azcopy-v10.md) para descarregar o AzCopy e saiba como pode fornecer credenciais de autorização ao serviço de armazenamento.

> [!NOTE] 
> Os exemplos deste artigo assumem que forneceu credenciais de autorização utilizando o Azure Ative Directory (Azure AD).
>
> Se preferir usar um token SAS para autorizar o acesso a dados blob, então pode anexar esse símbolo ao URL de recursos em cada comando AzCopy. Por exemplo: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="download-a-blob"></a>Transferir um blob

Descarregue uma bolha utilizando o comando [de cópia azcopia.](storage-ref-azcopy-copy.md)

> [!TIP]
> Este exemplo encerra argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

> [!NOTE]
> Se o `Content-md5` valor da propriedade de uma bolha contiver um haxixe, a AzCopy calcula um haxixe MD5 para dados descarregados e verifica que o hash MD5 armazenado na propriedade do blob corresponde ao `Content-md5` haxixe calculado. Se estes valores não corresponderem, o download falha a menos que anula este comportamento por ap pendência `--check-md5=NoCheck` ou para o comando de `--check-md5=LogOnly` cópia.

## <a name="download-a-directory"></a>Faça o download de um diretório

Faça o download de um diretório utilizando o comando [de cópia azcopia.](storage-ref-azcopy-copy.md)

> [!TIP]
> Este exemplo encerra argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Este exemplo resulta num diretório nomeado `C:\myDirectory\myBlobDirectory` que contém todas as bolhas descarregadas.

## <a name="download-directory-contents"></a>Baixar conteúdos de diretório

Pode transferir o conteúdo de um diretório sem copiar o próprio diretório com o símbolo de caráter universal (*).

> [!TIP]
> Este exemplo encerra argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

> [!NOTE]
> Atualmente, este cenário é suportado apenas para contas que não têm um espaço hierárquico de nomes.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

Anexar a `--recursive` bandeira para descarregar ficheiros em todas as subdiretivas.

## <a name="download-specific-blobs"></a>Baixar bolhas específicas

Pode descarregar bolhas específicas utilizando nomes de ficheiros completos, nomes parciais com caracteres wildcard (*), ou utilizando datas e horários. 

> [!TIP]
> Estes exemplos encerram argumentos de caminho com citações simples ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

#### <a name="specify-multiple-complete-blob-names"></a>Especificar vários nomes completos de blob

Utilize o comando [de cópia azcopia](storage-ref-azcopy-copy.md) com a `--include-path` opção. Separe os nomes individuais das bolhas utilizando uma emicolina `;` ().

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

Neste exemplo, a AzCopy transfere o `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` diretório e o `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` ficheiro. Inclua `--recursive` a opção de transferir todas as bolhas no `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` diretório.

Também pode excluir bolhas utilizando a `--exclude-path` opção. Para saber mais, consulte os docs de referência [de cópias de azcopia.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Use caracteres wildcard

Utilize o comando [de cópia azcopia](storage-ref-azcopy-copy.md) com a `--include-pattern` opção. Especifique nomes parciais que incluem os caracteres wildcard. Separar nomes utilizando uma esímina `;` ().

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Também pode excluir bolhas utilizando a `--exclude-pattern` opção. Para saber mais, consulte os docs de referência [de cópias de azcopia.](storage-ref-azcopy-copy.md)

As `--include-pattern` `--exclude-pattern` opções aplicam-se apenas aos nomes blob e não ao caminho.  Se quiser copiar todos os ficheiros de texto (bolhas) que existem numa árvore de diretório, use `–recursive` a opção para obter todo o diretório e, em seguida, use o `–include-pattern` e especificar para obter todos os `*.txt` ficheiros de texto.

#### <a name="download-blobs-that-were-modified-before-or-after-a-date-and-time"></a>Descarregar bolhas que foram modificadas antes ou depois de uma data e hora 

Utilize o comando [de cópia azcopia](storage-ref-azcopy-copy.md) com a `--include-before` opção ou `--include-after` opção. Especifique uma data e hora no formato ISO-8601 (Por exemplo: `2020-08-19T15:04:00Z` ). 

Os seguintes exemplos descarregam ficheiros que foram modificados na ou após a data especificada.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |

Para obter uma referência detalhada, consulte os documentos de referência da cópia da [azcopia.](storage-ref-azcopy-copy.md)

#### <a name="download-previous-versions-of-a-blob"></a>Descarregue versões anteriores de uma bolha

Se tiver ativado [a versão blob,](../blobs/versioning-enable.md)pode descarregar uma ou mais versões anteriores de uma bolha. 

Em primeiro lugar, crie um ficheiro de texto que contenha uma lista de [IDs](../blobs/versioning-overview.md)de versão. Cada ID de versão deve aparecer numa linha separada. Por exemplo: 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

Em seguida, use o comando [de cópia azcopia](storage-ref-azcopy-copy.md) com a `--list-of-versions` opção. Especificar a localização do ficheiro de texto que contém a lista de versões (por exemplo: `D:\\list-of-versions.txt` ).  

#### <a name="download-a-blob-snapshot"></a>Faça o download de um instantâneo blob

Pode descarregar uma [imagem de bolha](/azure/storage/blobs/snapshots-overview.md) referindo o valor **DateTime** de uma imagem de bolha. 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>?sharesnapshot=<DateTime-of-snapshot>' '<local-file-path>'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |

> [!NOTE]
> Se estiver a utilizar um token SAS para autorizar o acesso aos dados do blob, em seguida, apedte o **instantâneo DateTime** após o token SAS. Por exemplo: `'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z'`.

## <a name="download-with-optional-flags"></a>Descarregue com bandeiras opcionais

Pode ajustar a sua operação de descarregamento utilizando bandeiras opcionais. Aqui estão alguns exemplos.

|Cenário|Sinalizador|
|---|---|
|Descompressão automática de ficheiros.|**--descompressão**|
|Especifique o quão detalhado pretende que as suas entradas de registo relacionados com cópias sejam.|**--nível** = \[ de log \|INFORMAÇÃO DE ERRO \| DE AVISO \| NENHUMA\]|
|Especifique se e como substituir os ficheiros e bolhas conflituosos no destino.|**--overwrite** = \[ falso verdadeiro \| \| seSourceNewer \| prompt\]|

Para obter uma lista completa, consulte [as opções.](storage-ref-azcopy-copy.md#options)

## <a name="next-steps"></a>Passos seguintes

Encontre mais exemplos nestes artigos:

- [Exemplos: Carregar](storage-use-azcopy-blobs-upload.md)
- [Exemplos: Copiar entre conta](storage-use-azcopy-blobs-copy.md)
- [Exemplos: Sincronizar](storage-use-azcopy-blobs-synchronize.md)
- [Exemplos: Registos Amazon S3](storage-use-azcopy-s3.md)
- [Exemplos: Ficheiros Azure](storage-use-azcopy-files.md)
- [Tutorial: migrar dados no local para o armazenamento na cloud com o AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Configurar, otimizar e resolver problemas AzCopy](storage-use-azcopy-configure.md)