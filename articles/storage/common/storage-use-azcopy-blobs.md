---
title: Transferir dados de ou para o armazenamento da Blob Azure utilizando o AzCopy v10 [ Microsoft Docs
description: Este artigo contém uma coleção de comandos exemplo AzCopy que ajudam a criar recipientes, copiar ficheiros e sincronizar diretórios entre sistemas de ficheiros locais e contentores.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: b676c2647fbf7c93d271e1d7f68653452125e39b
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82137200"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Transferir dados com armazenamento AzCopy e Blob

O AzCopy é um utilitário de linha de comando que pode utilizar para copiar dados para, a partir ou entre contas de armazenamento. Este artigo contém comandos de exemplo que funcionam com o armazenamento blob.

> [!TIP]
> Os exemplos deste artigo encerram argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as conchas de comando, exceto na Shell de Comando do Windows (cmd.exe). Se estiver a utilizar uma Shell de Comando windows (cmd.exe), encerre argumentos de caminho com citações duplas (") em vez de citações únicas (').

## <a name="get-started"></a>Introdução

Veja o [Artigo da AzCopy](storage-use-azcopy-v10.md) para baixar o AzCopy e aprender sobre as formas como pode fornecer credenciais de autorização ao serviço de armazenamento.

> [!NOTE]
> Os exemplos deste artigo assumem que autenticaram a `AzCopy login` sua identidade usando o comando. A AzCopy utiliza então a sua conta Azure AD para autorizar o acesso aos dados no armazenamento blob.
>
> Se preferir utilizar um token SAS para autorizar o acesso a dados blob, então pode anexar esse símbolo ao URL de recursos em cada comando AzCopy.
>
> Por exemplo: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Criar um contentor

Pode utilizar a [azcopy fazer](storage-ref-azcopy-make.md) o comando para criar um recipiente. Os exemplos nesta secção criam `mycontainer`um recipiente chamado .

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Exemplo** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Exemplo** (espaço hierárquico) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

Para obter documentos de referência detalhados, consulte a [azcopy fazer](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Carregar ficheiros

Pode utilizar o comando de cópia de [azcópia](storage-ref-azcopy-copy.md) para fazer upload de ficheiros e diretórios do seu computador local.

Esta secção contém os seguintes exemplos:

> [!div class="checklist"]
> * Carregar um ficheiro
> * Faça upload de um diretório
> * Faça upload do conteúdo de um diretório 
> * Carregar ficheiros específicos

> [!TIP]
> Pode ajustar o seu funcionamento de upload utilizando bandeiras opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Faça upload de ficheiros como Append Blobs ou Page Blobs.|**--blob-tipo**=\[BlockBlob\|PageBlob AppendBlob\|\]|
> |Faça o upload para um nível de acesso específico (como o nível de arquivo).|**-bloco-blob-tier**=\[None\|\|Hot\|Cool Archive\]|
> 
> Para obter uma lista completa, consulte [as opções](storage-ref-azcopy-copy.md#options).

### <a name="upload-a-file"></a>Carregar um ficheiro

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Exemplo** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

Também pode fazer o upload de um ficheiro utilizando um símbolo wildcard (*) em qualquer lugar do caminho de ficheiro ou nome de ficheiro. Por exemplo: `'C:\myDirectory\*.txt'` `C:\my*\*.txt`ou .

### <a name="upload-a-directory"></a>Faça upload de um diretório

Este exemplo copia um diretório (e todos os ficheiros desse diretório) para um recipiente de bolhas. O resultado é um diretório no recipiente com o mesmo nome.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Para copiar para um diretório dentro do recipiente, apenas especifique o nome desse diretório na sua cadeia de comando.

|    |     |
|--------|-----------|
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Se especificar o nome de um diretório que não existe no recipiente, a AzCopy cria um novo diretório com esse nome.

### <a name="upload-the-contents-of-a-directory"></a>Faça upload do conteúdo de um diretório

Pode fazer o upload do conteúdo de um diretório sem copiar o próprio diretório contendo utilizando o símbolo wildcard (*).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Exemplo** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> Acomode `--recursive` a bandeira para carregar ficheiros em todas as subdirectões.

### <a name="upload-specific-files"></a>Carregar ficheiros específicos

Pode especificar nomes completos de ficheiros ou usar nomes parciais com caracteres wildcard (*).

#### <a name="specify-multiple-complete-file-names"></a>Especificar vários nomes completos de ficheiros

Utilize o comando de cópia `--include-path` [azcopia](storage-ref-azcopy-copy.md) com a opção. Separe os nomes individuais`;`de ficheiros utilizando um ponto e vírgula ().

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

Neste exemplo, a AzCopy `C:\myDirectory\photos` transfere `C:\myDirectory\documents\myFile.txt` o diretório e o ficheiro. Tem de incluir `--recursive` a opção de `C:\myDirectory\photos` transferir todos os ficheiros no diretório.

Também pode excluir ficheiros `--exclude-path` utilizando a opção. Para saber mais, consulte os docs de referência de cópia de [azcopia.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Use caracteres wildcard

Utilize o comando de cópia `--include-pattern` [azcopia](storage-ref-azcopy-copy.md) com a opção. Especifique nomes parciais que incluam os caracteres wildcard. Nomes separados utilizando`;`um semicolin ( ). 

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

Também pode excluir ficheiros `--exclude-pattern` utilizando a opção. Para saber mais, consulte os docs de referência de cópia de [azcopia.](storage-ref-azcopy-copy.md)

As `--include-pattern` `--exclude-pattern` opções e opções aplicam-se apenas aos nomes de ficheiros e não ao caminho.  Se quiser copiar todos os ficheiros de texto que existem numa árvore de diretório, use a opção `–recursive` para obter toda a árvore do diretório e, em seguida, use o `–include-pattern` e especifique `*.txt` para obter todos os ficheiros de texto.

## <a name="download-files"></a>Transferir ficheiros

Pode utilizar o comando de cópia de [azcopy](storage-ref-azcopy-copy.md) para transferir bolhas, diretórios e contentores para o seu computador local.

Esta secção contém os seguintes exemplos:

> [!div class="checklist"]
> * Transferir um ficheiro
> * Baixar um diretório
> * Descarregue o conteúdo de um diretório
> * Descarregue ficheiros específicos

> [!TIP]
> Pode ajustar a sua operação de descarregamento utilizando bandeiras opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Descomprimir automaticamente os ficheiros.|**--descomprimir**|
> |Especifique o quão detalhada deseja que as suas entradas de registo relacionadas com cópias sejam.|**--log-level**=\[\|WARNING\|\|ERROR INFO NONE\]|
> |Especifique se e como substituir os ficheiros e bolhas conflituosos no destino.|**-, substituir**=\[\|verdadeiro\|falso seSourceNewer\|pronta\]|
> 
> Para obter uma lista completa, consulte [as opções](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Se `Content-md5` o valor patrimonial de uma bolha contiver um haxixe, a AzCopy calcula um haxixe MD5 para dados `Content-md5` descarregados e verifica que o haxixe MD5 armazenado na propriedade do blob corresponde ao haxixe calculado. Se estes valores não corresponderem, o download falha a `--check-md5=NoCheck` menos `--check-md5=LogOnly` que se sobrecarregue este comportamento por despesas ou ao comando de cópia.

### <a name="download-a-file"></a>Transferir um ficheiro

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Baixar um diretório

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Este exemplo resulta num `C:\myDirectory\myBlobDirectory` diretório nomeado que contém todos os ficheiros descarregados.

### <a name="download-the-contents-of-a-directory"></a>Descarregue o conteúdo de um diretório

Pode descarregar o conteúdo de um diretório sem copiar o próprio diretório contendo utilizando o símbolo wildcard (*).

> [!NOTE]
> Atualmente, este cenário é suportado apenas para contas que não têm um espaço de nome hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Anexar `--recursive` a bandeira para descarregar ficheiros em todos os subdiretórios.

### <a name="download-specific-files"></a>Descarregue ficheiros específicos

Pode especificar nomes completos de ficheiros ou usar nomes parciais com caracteres wildcard (*).

#### <a name="specify-multiple-complete-file-names"></a>Especificar vários nomes completos de ficheiros

Utilize o comando de cópia `--include-path` [azcopia](storage-ref-azcopy-copy.md) com a opção. Separe os nomes individuais`;`de ficheiros utilizando um semicolin ( ).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

Neste exemplo, a AzCopy `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` transfere `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` o diretório e o ficheiro. Tem de incluir `--recursive` a opção de `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` transferir todos os ficheiros no diretório.

Também pode excluir ficheiros `--exclude-path` utilizando a opção. Para saber mais, consulte os docs de referência de cópia de [azcopia.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Use caracteres wildcard

Utilize o comando de cópia `--include-pattern` [azcopia](storage-ref-azcopy-copy.md) com a opção. Especifique nomes parciais que incluam os caracteres wildcard. Nomes separados utilizando`;`um semicolin ( ).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Também pode excluir ficheiros `--exclude-pattern` utilizando a opção. Para saber mais, consulte os docs de referência de cópia de [azcopia.](storage-ref-azcopy-copy.md)

As `--include-pattern` `--exclude-pattern` opções e opções aplicam-se apenas aos nomes de ficheiros e não ao caminho.  Se quiser copiar todos os ficheiros de texto que existem numa árvore de diretório, use a opção `–recursive` para obter toda a árvore do diretório e, em seguida, use o `–include-pattern` e especifique `*.txt` para obter todos os ficheiros de texto.

## <a name="copy-blobs-between-storage-accounts"></a>Copiar blobs entre contas de armazenamento

Pode utilizar o AzCopy para copiar bolhas para outras contas de armazenamento. A operação de cópia é sincronizada, por isso, quando o comando regressa, isso indica que todos os ficheiros foram copiados. 

O AzCopy utiliza [APIs](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url) [servidor-a-servidor](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) , por isso os dados são copiados diretamente entre servidores de armazenamento. Estas operações de cópia não utilizam a largura de banda da rede do seu computador. Pode aumentar a entrada destas operações, `AZCOPY_CONCURRENCY_VALUE` fixando o valor da variável ambiental. Para saber mais, consulte a [entrada do Otimize.](storage-use-azcopy-configure.md#optimize-throughput)

> [!NOTE]
> Este cenário tem as seguintes limitações na versão atual.
>
> - Tem de anexar um token SAS a cada URL de origem. Se fornecer credenciais de autorização utilizando o Azure Ative Directory (AD), só pode omitir o token SAS a partir do URL de destino.
>-  As contas de armazenamento de blocos premium não suportam níveis de acesso. Omita o nível de acesso de uma `s2s-preserve-access-tier` `false` bolha da `--s2s-preserve-access-tier=false`operação de cópia, definindo o (por exemplo: ).

Esta secção contém os seguintes exemplos:

> [!div class="checklist"]
> * Copiar uma bolha para outra conta de armazenamento
> * Copiar um diretório para outra conta de armazenamento
> * Copiar um recipiente para outra conta de armazenamento
> * Copie todos os recipientes, diretórios e ficheiros para outra conta de armazenamento

Estes exemplos também funcionam com contas que têm um espaço de nome hierárquico. O [acesso multi-protocolo ao Armazenamento](../blobs/data-lake-storage-multi-protocol-access.md) de Data Lake permite-lhe utilizar a mesma sintaxe de URL ()`blob.core.windows.net`nessas contas.

> [!TIP]
> Pode ajustar o seu funcionamento da cópia utilizando bandeiras opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Copiar ficheiros como Append Blobs ou Page Blobs.|**--blob-tipo**=\[BlockBlob\|PageBlob AppendBlob\|\]|
> |Copiar para um nível de acesso específico (como o nível de arquivo).|**-bloco-blob-tier**=\[None\|\|Hot\|Cool Archive\]|
> |Descomprimir automaticamente os ficheiros.|**--descompress gzip**=\[\|esvaziar\]|
> 
> Para obter uma lista completa, consulte [as opções](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-blob-to-another-storage-account"></a>Copiar uma bolha para outra conta de armazenamento

Use a mesma sintaxe de URL para`blob.core.windows.net`contas que tenham um espaço de nome hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Copiar um diretório para outra conta de armazenamento

Use a mesma sintaxe de URL para`blob.core.windows.net`contas que tenham um espaço de nome hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Exemplo** (espaço hierárquico)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Copiar um recipiente para outra conta de armazenamento

Use a mesma sintaxe de URL para`blob.core.windows.net`contas que tenham um espaço de nome hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Exemplo** (espaço hierárquico)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Copie todos os recipientes, diretórios e bolhas para outra conta de armazenamento

Use a mesma sintaxe de URL para`blob.core.windows.net`contas que tenham um espaço de nome hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **Exemplo** (espaço hierárquico)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Synchronize ficheiros

Pode sincronizar o conteúdo de um sistema de ficheiros local com um recipiente de bolhas. Também pode sincronizar recipientes e diretórios virtuais uns com os outros. A sincronização é só de ida. Por outras palavras, escolhe-se qual destes dois pontos finais é a fonte e qual é o destino. A sincronização também utiliza o servidor para o servidor APIs. Os exemplos apresentados nesta secção também funcionam com contas que têm um espaço de nome hierárquico. 

> [!NOTE]
> O lançamento atual do AzCopy não sincroniza entre outras fontes e destinos (por exemplo: armazenamento de ficheiros ou baldes S3 da Amazon Web Services (AWS).

O comando [de sincronização](storage-ref-azcopy-sync.md) compara nomes de ficheiros e últimos selos de tempo modificados. Detete a `--delete-destination` bandeira `true` opcional `prompt` num valor ou para apagar ficheiros no diretório de destino se esses ficheiros já não existirem no diretório de origem.

Se definir `--delete-destination` a `true` bandeira para a AzCopy, apaga ficheiros sem fornecer uma solicitação. Se desejar que apareça uma solicitação antes de `--delete-destination` o `prompt`AzCopy apagar um ficheiro, coloque a bandeira para .

> [!NOTE]
> Para evitar supressões acidentais, certifique-se de `--delete-destination=prompt|true` que ativa a função [de eliminação suave](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) antes de utilizar a bandeira.

> [!TIP]
> Pode ajustar a sua operação de sincronização utilizando bandeiras opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Especifique a estritamente hashes MD5 deve ser validada ao descarregar.|**-check-md5**=\[NoCheck\|LogOnly\|FailIfDifferent\|FailIfDifferentDifferentOrMissing\]|
> |Excluir ficheiros com base num padrão.|**-excluir-caminho**|
> |Especifique o quão detalhada sê-lo-á.|**--log-level**=\[\|WARNING\|\|ERROR INFO NONE\]|
> 
> Para obter uma lista completa, consulte [as opções](storage-ref-azcopy-sync.md#options).

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Atualizar um recipiente com alterações num sistema de ficheiros local

Neste caso, o contentor é o destino, e o sistema de ficheiros local é a fonte. 

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Atualizar um sistema de ficheiros local com alterações a um contentor

Neste caso, o sistema de ficheiros local é o destino, e o contentor é a fonte.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Exemplo** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>Atualizar um recipiente com alterações noutro recipiente

O primeiro recipiente que aparece neste comando é a fonte. O segundo é o destino.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Atualizar um diretório com alterações a um diretório em outra partilha de ficheiros

O primeiro diretório que aparece neste comando é a fonte. O segundo é o destino.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>Passos seguintes

Encontre mais exemplos em qualquer um destes artigos:

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)

- [Tutorial: migrar dados no local para o armazenamento na cloud com o AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Transferir dados com a AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)

- [Transferir dados com baldes AzCopy e Amazon S3](storage-use-azcopy-s3.md)

- [Configure, otimize e problemas AzCopy](storage-use-azcopy-configure.md)
