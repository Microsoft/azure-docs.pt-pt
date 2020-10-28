---
title: Transfira dados para ou a partir do armazenamento da Azure Blob utilizando a AzCopy v10 Microsoft Docs
description: Este artigo contém uma coleção de comandos de exemplo AzCopy que o ajudam a criar contentores, copiar ficheiros e sincronizar diretórios entre sistemas de ficheiros locais e contentores.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 294adce3dc312003d72336bd0752ba3aba5eaace
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792859"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Dados de transferência com armazenamento AzCopy e Blob

O AzCopy é um utilitário de linha de comando que pode usar para copiar dados para, a partir ou entre contas de armazenamento. Este artigo contém comandos de exemplo que funcionam com o armazenamento blob.

> [!TIP]
> Os exemplos deste artigo encerram argumentos de caminho com citações simples ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

## <a name="get-started"></a>Introdução

Consulte o [artigo da AzCopy](storage-use-azcopy-v10.md) para descarregar o AzCopy e saiba como pode fornecer credenciais de autorização ao serviço de armazenamento.

> [!NOTE]
> Os exemplos deste artigo assumem que autenticaste a tua identidade usando o `AzCopy login` comando. A AzCopy utiliza então a sua conta Azure AD para autorizar o acesso aos dados no armazenamento blob.
>
> Se preferir usar um token SAS para autorizar o acesso a dados blob, então pode anexar esse símbolo ao URL de recursos em cada comando AzCopy.
>
> Por exemplo: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Criar um contentor

Pode utilizar a [azcopia para criar](storage-ref-azcopy-make.md) um recipiente. Os exemplos desta secção criam um recipiente chamado `mycontainer` .

|    |     |
|--------|-----------|
| **Syntax** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Exemplo** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Exemplo** (espaço hierárquico) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

Para obter documentos de referência detalhados, consulte [a azcopia make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Carregar ficheiros

Pode utilizar o comando de cópia de [azcopia](storage-ref-azcopy-copy.md) para fazer upload de ficheiros e diretórios a partir do computador local.

Esta secção contém os seguintes exemplos:

> [!div class="checklist"]
> * Carregar um ficheiro
> * Faça upload de um diretório
> * Faça upload do conteúdo de um diretório 
> * Fazer upload de ficheiros específicos

> [!TIP]
> Pode ajustar a sua operação de upload utilizando bandeiras opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Carregue os ficheiros como Blobs de Acréscimo ou Blobs de Páginas.|**--blob-type** = \[ \|Apendb BlockBlob PageBlob \|\]|
> |Carregue para uma camada de acesso específica (como a camada de arquivo).|**--bloco-blob-tier** = \[ Nenhum \| Arquivo Quente \| Cool \|\]|
> 
> Para obter uma lista completa, consulte [as opções.](storage-ref-azcopy-copy.md#options)

### <a name="upload-a-file"></a>Carregar um ficheiro

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Exemplo** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

Também pode fazer o upload de um ficheiro utilizando um símbolo wildcard (*) em qualquer lugar do caminho do ficheiro ou nome de ficheiro. Por exemplo: `'C:\myDirectory\*.txt'` ou `C:\my*\*.txt` . .

### <a name="upload-a-directory"></a>Faça upload de um diretório

Este exemplo copia um diretório (e todos os ficheiros desse diretório) para um recipiente de bolhas. O resultado é um diretório no contentor com o mesmo nome.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Para copiar para um diretório dentro do contentor, basta especificar o nome desse diretório na sua cadeia de comando.

|    |     |
|--------|-----------|
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Se especificar o nome de um diretório que não existe no contentor, o AzCopy cria um novo diretório com esse nome.

### <a name="upload-the-contents-of-a-directory"></a>Faça upload do conteúdo de um diretório

Pode carregar o conteúdo de um diretório sem copiar o próprio diretório contendo a utilização do símbolo wildcard (*).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Exemplo** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> Anexar a `--recursive` bandeira para carregar ficheiros em todos os sub-directórios.

### <a name="upload-specific-files"></a>Fazer upload de ficheiros específicos

Pode fazer o upload de ficheiros específicos utilizando nomes de ficheiros completos, nomes parciais com caracteres wildcard (*), ou utilizando datas e horários.

#### <a name="specify-multiple-complete-file-names"></a>Especifique vários nomes completos de ficheiros

Utilize o comando [de cópia azcopia](storage-ref-azcopy-copy.md) com a `--include-path` opção. Separe os nomes individuais dos ficheiros utilizando um ponto e vírgula `;` ().

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

Neste exemplo, a AzCopy transfere o `C:\myDirectory\photos` diretório e o `C:\myDirectory\documents\myFile.txt` ficheiro. Tem de incluir a `--recursive` opção de transferir todos os ficheiros do `C:\myDirectory\photos` diretório.

Também pode excluir ficheiros utilizando a `--exclude-path` opção. Para saber mais, consulte os docs de referência [de cópias de azcopia.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Use caracteres wildcard

Utilize o comando [de cópia azcopia](storage-ref-azcopy-copy.md) com a `--include-pattern` opção. Especifique nomes parciais que incluem os caracteres wildcard. Separar nomes utilizando uma esímina `;` (). 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

Também pode excluir ficheiros utilizando a `--exclude-pattern` opção. Para saber mais, consulte os docs de referência [de cópias de azcopia.](storage-ref-azcopy-copy.md)

As `--include-pattern` `--exclude-pattern` opções aplicam-se apenas aos dados de filenames e não ao caminho.  Se quiser copiar todos os ficheiros de texto que existem numa árvore de diretório, use `–recursive` a opção para obter todo o diretório e, em seguida, use o `–include-pattern` e especificar para obter todos os `*.txt` ficheiros de texto.

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>Carregar ficheiros que foram modificados após uma data e hora 

Utilize o comando [de cópia azcopia](storage-ref-azcopy-copy.md) com a `--include-after` opção. Especifique uma data e hora no formato ISO-8601 (Por exemplo: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'` |

Para obter uma referência detalhada, consulte os documentos de referência da cópia da [azcopia.](storage-ref-azcopy-copy.md)

## <a name="download-files"></a>Transferir ficheiros

Pode utilizar o comando de cópia de [azcopia](storage-ref-azcopy-copy.md) para descarregar bolhas, diretórios e contentores para o seu computador local.

Esta secção contém os seguintes exemplos:

> [!div class="checklist"]
> * Transferir um ficheiro
> * Faça o download de um diretório
> * Descarregue o conteúdo de um diretório
> * Descarregue ficheiros específicos

> [!TIP]
> Pode ajustar a sua operação de descarregamento utilizando bandeiras opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Descompressão automática de ficheiros.|**--descompressão**|
> |Especifique o quão detalhado pretende que as suas entradas de registo relacionados com cópias sejam.|**--nível** = \[ de log \|INFORMAÇÃO DE ERRO \| DE AVISO \| NENHUMA\]|
> |Especifique se e como substituir os ficheiros e bolhas conflituosos no destino.|**--overwrite** = \[ falso verdadeiro \| \| seSourceNewer \| prompt\]|
> 
> Para obter uma lista completa, consulte [as opções.](storage-ref-azcopy-copy.md#options)

> [!NOTE]
> Se o `Content-md5` valor da propriedade de uma bolha contiver um haxixe, a AzCopy calcula um haxixe MD5 para dados descarregados e verifica que o hash MD5 armazenado na propriedade do blob corresponde ao `Content-md5` haxixe calculado. Se estes valores não corresponderem, o download falha a menos que anula este comportamento por ap pendência `--check-md5=NoCheck` ou para o comando de `--check-md5=LogOnly` cópia.

### <a name="download-a-file"></a>Transferir um ficheiro

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Faça o download de um diretório

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Este exemplo resulta num diretório nomeado `C:\myDirectory\myBlobDirectory` que contém todos os ficheiros descarregados.

### <a name="download-the-contents-of-a-directory"></a>Descarregue o conteúdo de um diretório

Pode descarregar o conteúdo de um diretório sem copiar o próprio diretório contendo o próprio símbolo wildcard (*).

> [!NOTE]
> Atualmente, este cenário é suportado apenas para contas que não têm um espaço hierárquico de nomes.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Anexar a `--recursive` bandeira para descarregar ficheiros em todos os sub-directórios.

### <a name="download-specific-files"></a>Descarregue ficheiros específicos

Pode descarregar ficheiros específicos utilizando nomes de ficheiros completos, nomes parciais com caracteres wildcard (*), ou utilizando datas e horários. 

#### <a name="specify-multiple-complete-file-names"></a>Especifique vários nomes completos de ficheiros

Utilize o comando [de cópia azcopia](storage-ref-azcopy-copy.md) com a `--include-path` opção. Separe os nomes individuais dos ficheiros utilizando uma esímula `;` ().

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

Neste exemplo, a AzCopy transfere o `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` diretório e o `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` ficheiro. Tem de incluir a `--recursive` opção de transferir todos os ficheiros do `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` diretório.

Também pode excluir ficheiros utilizando a `--exclude-path` opção. Para saber mais, consulte os docs de referência [de cópias de azcopia.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Use caracteres wildcard

Utilize o comando [de cópia azcopia](storage-ref-azcopy-copy.md) com a `--include-pattern` opção. Especifique nomes parciais que incluem os caracteres wildcard. Separar nomes utilizando uma esímina `;` ().

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Também pode excluir ficheiros utilizando a `--exclude-pattern` opção. Para saber mais, consulte os docs de referência [de cópias de azcopia.](storage-ref-azcopy-copy.md)

As `--include-pattern` `--exclude-pattern` opções aplicam-se apenas aos dados de filenames e não ao caminho.  Se quiser copiar todos os ficheiros de texto que existem numa árvore de diretório, use `–recursive` a opção para obter todo o diretório e, em seguida, use o `–include-pattern` e especificar para obter todos os `*.txt` ficheiros de texto.

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>Descarregue ficheiros que foram modificados após uma data e hora 

Utilize o comando [de cópia azcopia](storage-ref-azcopy-copy.md) com a `--include-after` opção. Especifique uma data e hora no formato ISO-8601 (Por exemplo: `2020-08-19T15:04:00Z` ). 

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

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-directory-path>' --list-of-versions '<list-of-versions-file>'`|
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt' --list-of-versions 'D:\\list-of-versions.txt'` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt' --list-of-versions 'D:\\list-of-versions.txt'` |

O nome de cada ficheiro descarregado começa com o ID da versão seguido pelo nome da bolha. 

## <a name="copy-blobs-between-storage-accounts"></a>Copiar blobs entre contas de armazenamento

Pode utilizar o AzCopy para copiar blobs para outras contas de armazenamento. A operação de cópia é sincronizada, pelo que quando o comando é devolvido, indica que todos os ficheiros foram copiados. 

O AzCopy utiliza [APIs](/rest/api/storageservices/put-page-from-url) [de servidor a servidor,](/rest/api/storageservices/put-block-from-url) por isso os dados são copiados diretamente entre servidores de armazenamento. Estas operações de cópia não utilizam a largura de banda de rede do seu computador. Pode aumentar a produção destas operações definindo o valor da `AZCOPY_CONCURRENCY_VALUE` variável ambiente. Para saber mais, consulte [a produção otimiza.](storage-use-azcopy-configure.md#optimize-throughput)

> [!NOTE]
> Este cenário tem as seguintes limitações na versão atual.
>
> - Você tem que anexar um token SAS a cada URL de origem. Se fornecer credenciais de autorização utilizando o Azure Ative Directory (AD), pode omitir o token SAS apenas a partir do URL de destino. Certifique-se de que estabeleceu as funções adequadas na sua conta de destino. Ver [Opção 1: Utilizar o Diretório Ativo Azure](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory).
>-  As contas de armazenamento de blocos premium não suportam níveis de acesso. Omita o nível de acesso de uma bolha da operação de cópia, definindo `s2s-preserve-access-tier` o `false` a (Por exemplo: `--s2s-preserve-access-tier=false` ).

Esta secção contém os seguintes exemplos:

> [!div class="checklist"]
> * Copie uma bolha para outra conta de armazenamento
> * Copie um diretório para outra conta de armazenamento
> * Copie um recipiente para outra conta de armazenamento
> * Copie todos os contentores, diretórios e ficheiros para outra conta de armazenamento

Estes exemplos também funcionam com contas que têm um espaço hierárquico de nomes. [O acesso multi-protocolo no Armazenamento do Lago de Dados](../blobs/data-lake-storage-multi-protocol-access.md) permite-lhe utilizar a mesma sintaxe URL `blob.core.windows.net` () nessas contas.

> [!TIP]
> Pode ajustar a sua operação de cópia utilizando bandeiras opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Copiar bolhas como Block, Page ou Append Blobs.|**--blob-type** = \[ \|Apendb BlockBlob PageBlob \|\]|
> |Copiar para um nível de acesso específico (como o nível de arquivo).|**--bloco-blob-tier** = \[ Nenhum \| Arquivo Quente \| Cool \|\]|
> |Descompressão automática de ficheiros.|**--descompressão** = \[ gzip \| esvaziar\]|
> 
> Para obter uma lista completa, consulte [as opções.](storage-ref-azcopy-copy.md#options)

### <a name="copy-a-blob-to-another-storage-account"></a>Copie uma bolha para outra conta de armazenamento

Utilize a mesma sintaxe URL `blob.core.windows.net` para contas que tenham um espaço hierárquico de nomes.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Exemplo** (espaço hierárquico) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Copie um diretório para outra conta de armazenamento

Utilize a mesma sintaxe URL `blob.core.windows.net` para contas que tenham um espaço hierárquico de nomes.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Exemplo** (espaço hierárquico)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Copie um recipiente para outra conta de armazenamento

Utilize a mesma sintaxe URL `blob.core.windows.net` para contas que tenham um espaço hierárquico de nomes.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Exemplo** (espaço hierárquico)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Copie todos os contentores, diretórios e bolhas para outra conta de armazenamento

Utilize a mesma sintaxe URL `blob.core.windows.net` para contas que tenham um espaço hierárquico de nomes.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **Exemplo** (espaço hierárquico)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Sincronizar ficheiros

Pode sincronizar o conteúdo de um sistema de ficheiros local com um recipiente de bolhas. Também pode sincronizar contentores e diretórios virtuais entre si. A sincronização é só de ida. Por outras palavras, você escolhe qual destes dois pontos finais é a fonte e qual é o destino. A sincronização também utiliza o servidor para as APIs do servidor. Os exemplos apresentados nesta secção também funcionam com contas que têm um espaço hierárquico de nomes. 

> [!NOTE]
> O lançamento atual do AzCopy não sincroniza entre outras fontes e destinos (por exemplo: armazenamento de ficheiros ou baldes S3 da Amazon Web Services (AWS).

O comando [de sincronização](storage-ref-azcopy-sync.md) compara nomes de ficheiros e últimos cartões de tempo modificados. Desa estama a `--delete-destination` bandeira opcional a um valor de ou para apagar `true` `prompt` ficheiros no diretório de destino se esses ficheiros já não existirem no diretório de origem.

Se colocar a `--delete-destination` bandeira no `true` AzCopy elimina ficheiros sem fornecer uma solicitação. Se desejar que apareça uma solicitação antes de o AzCopy apagar um ficheiro, coloque a `--delete-destination` bandeira em `prompt` .

> [!NOTE]
> Para evitar supressões acidentais, certifique-se de que ativa a função [de eliminação suave](../blobs/soft-delete-blob-overview.md) antes de utilizar a `--delete-destination=prompt|true` bandeira.

> [!TIP]
> Pode ajustar a sua operação de sincronização utilizando bandeiras opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Especifique como os hashes MD5 devem ser validados ao descarregar.|**--check-md5** = \[ NoCheck \| LogOnly \| FailIfDifferent \| FailIfDifferentOrMissing\]|
> |Excluir ficheiros com base num padrão.|**--excluir caminho**|
> |Especifique o quão detalhado pretende que as suas entradas de registo relacionadas com sincronização sejam.|**--nível** = \[ de log \|INFORMAÇÃO DE ERRO \| DE AVISO \| NENHUMA\]|
> 
> Para obter uma lista completa, consulte [as opções.](storage-ref-azcopy-sync.md#options)

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Atualizar um recipiente com alterações a um sistema de ficheiros local

Neste caso, o contentor é o destino e o sistema de ficheiros local é a origem. 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Atualizar um sistema de ficheiros local com alterações a um contentor

Neste caso, o sistema de ficheiros local é o destino, e o contentor é a fonte.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Exemplo** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>Atualizar um recipiente com alterações em outro recipiente

O primeiro recipiente que aparece neste comando é a fonte. O segundo é o destino.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Atualizar um diretório com alterações a um diretório em outra partilha de ficheiros

O primeiro diretório que aparece neste comando é a fonte. O segundo é o destino.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>Passos seguintes

Encontre mais exemplos em qualquer um destes artigos:

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)

- [Tutorial: migrar dados no local para o armazenamento na cloud com o AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Transferir dados com o AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)

- [Transfira dados com baldes AzCopy e Amazon S3](storage-use-azcopy-s3.md)

- [Configurar, otimizar e resolver problemas AzCopy](storage-use-azcopy-configure.md)