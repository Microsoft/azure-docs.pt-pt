---
title: Transferir dados para ou a partir de Ficheiros Azure utilizando a AzCopy v10 Microsoft Docs
description: Transfira dados com a AzCopy e armazenamento de ficheiros.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: a836f4ce40f4d2e0871f99122d25bb6c6f346d05
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527885"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Transferir dados com o AzCopy e armazenamento de ficheiros 

O AzCopy é um utilitário da linha de comando que pode utilizar para copiar blobs ou ficheiros de/para uma conta de armazenamento. Este artigo contém comandos de exemplo que funcionam com ficheiros Azure.

Antes de começar, consulte o [artigo do Get start with AzCopy](storage-use-azcopy-v10.md) para baixar a AzCopy e familiarizar-se com a ferramenta.

> [!TIP]
> Os exemplos deste artigo encerram argumentos de caminho com citações simples ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

## <a name="create-file-shares"></a>Criar partilhas de ficheiros

Pode utilizar o comando [de azcopia](storage-ref-azcopy-make.md) para criar uma partilha de ficheiros. O exemplo nesta secção cria uma partilha de ficheiros denominada `myfileshare` .

|    |     |
|--------|-----------|
| **Syntax** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'` |
| **Exemplo** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Para obter documentos de referência detalhados, consulte [a azcopia make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Carregar ficheiros

Pode utilizar o comando de cópia de [azcopia](storage-ref-azcopy-copy.md) para fazer upload de ficheiros e diretórios a partir do computador local.

Esta secção contém os seguintes exemplos:

> [!div class="checklist"]
> * Carregar um ficheiro
> * Faça upload de um diretório
> * Faça upload do conteúdo de um diretório
> * Faça upload de um ficheiro específico

> [!TIP]
> Pode ajustar a sua operação de upload utilizando bandeiras opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Copiar listas de controlo de acesso (ACLs) juntamente com os ficheiros.|**---conserva-smb-permissões** = \[ verdadeiro \| falso\]|
> |Copie informações de propriedade SMB juntamente com os ficheiros.|**--preservar-smb-info** = \[ verdadeiro \| falso\]|
> |Faça upload de ficheiros como Append Blobs ou Page Blobs.|**--blob-type** = \[ \|Apendb BlockBlob PageBlob \|\]|
> |Faça o upload para um nível de acesso específico (como o nível de arquivo).|**--bloco-blob-tier** = \[ Nenhum \| Arquivo Quente \| Cool \|\]|
> 
> Para obter uma lista completa, consulte [as opções.](storage-ref-azcopy-copy.md#options)

> [!NOTE]
> O AzCopy não calcula automaticamente e armazena o código de haxixe md5 do ficheiro. Se quiser que a AzCopy faça isso, apite a `--put-md5` bandeira a cada comando de cópia. Assim, quando o ficheiro é descarregado, o AzCopy calcula um haxixe MD5 para dados descarregados e verifica que o hash MD5 armazenado na propriedade do ficheiro `Content-md5` corresponde ao haxixe calculado.

### <a name="upload-a-file"></a>Carregar um ficheiro

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **Exemplo** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Também pode fazer o upload de um ficheiro utilizando um símbolo wildcard (*) em qualquer lugar do caminho do ficheiro ou nome de ficheiro. Por exemplo: `'C:\myDirectory\*.txt'` ou `C:\my*\*.txt` . .

### <a name="upload-a-directory"></a>Faça upload de um diretório

Este exemplo copia um diretório (e todos os ficheiros desse diretório) para uma partilha de ficheiros. O resultado é um diretório na partilha de ficheiros com o mesmo nome.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Para copiar para um diretório dentro da partilha de ficheiros, basta especificar o nome desse diretório na sua cadeia de comando.

|    |     |
|--------|-----------|
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Se especificar o nome de um diretório que não existe na partilha de ficheiros, a AzCopy cria um novo diretório com esse nome.

### <a name="upload-the-contents-of-a-directory"></a>Faça upload do conteúdo de um diretório

Pode carregar o conteúdo de um diretório sem copiar o próprio diretório contendo a utilização do símbolo wildcard (*).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Anexar a `--recursive` bandeira para carregar ficheiros em todos os sub-directórios.

### <a name="upload-specific-files"></a>Fazer upload de ficheiros específicos

Pode especificar nomes completos de ficheiros ou usar nomes parciais com caracteres wildcard (*).

#### <a name="specify-multiple-complete-file-names"></a>Especifique vários nomes completos de ficheiros

Utilize o comando [de cópia azcopia](storage-ref-azcopy-copy.md) com a `--include-path` opção. Separe os nomes individuais dos ficheiros utilizando um ponto e vírgula `;` ().

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

Neste exemplo, a AzCopy transfere o `C:\myDirectory\photos` diretório e o `C:\myDirectory\documents\myFile.txt` ficheiro. Tem de incluir a `--recursive` opção de transferir todos os ficheiros do `C:\myDirectory\photos` diretório.

Também pode excluir ficheiros utilizando a `--exclude-path` opção. Para saber mais, consulte os docs de referência [de cópias de azcopia.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Use caracteres wildcard

Utilize o comando [de cópia azcopia](storage-ref-azcopy-copy.md) com a `--include-pattern` opção. Especifique nomes parciais que incluem os caracteres wildcard. Separar nomes utilizando um ponto e vírgula `;` ().

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

Também pode excluir ficheiros utilizando a `--exclude-pattern` opção. Para saber mais, consulte os docs de referência [de cópias de azcopia.](storage-ref-azcopy-copy.md)

As `--include-pattern` `--exclude-pattern` opções aplicam-se apenas aos dados de filenames e não ao caminho.  Se quiser copiar todos os ficheiros de texto que existem numa árvore de diretório, use `–recursive` a opção para obter todo o diretório e, em seguida, use o `–include-pattern` e especificar para obter todos os `*.txt` ficheiros de texto.

## <a name="download-files"></a>Transferir ficheiros

Pode utilizar o comando de cópia de [azcopia](storage-ref-azcopy-copy.md) para descarregar ficheiros, diretórios e partilhas de ficheiros para o seu computador local.

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
> |Copiar listas de controlo de acesso (ACLs) juntamente com os ficheiros.|**---conserva-smb-permissões** = \[ verdadeiro \| falso\]|
> |Copie informações de propriedade SMB juntamente com os ficheiros.|**--preservar-smb-info** = \[ verdadeiro \| falso\]|
> |Descompressão automática de ficheiros.|**--descompressão**|
> 
> Para obter uma lista completa, consulte [as opções.](storage-ref-azcopy-copy.md#options)

> [!NOTE]
> Se o `Content-md5` valor da propriedade de um ficheiro contiver um haxixe, a AzCopy calcula um haxixe MD5 para dados descarregados e verifica que o haxixe MD5 armazenado na propriedade do ficheiro corresponde ao `Content-md5` haxixe calculado. Se estes valores não corresponderem, o download falha a menos que anula este comportamento por ap pendência `--check-md5=NoCheck` ou para o comando de `--check-md5=LogOnly` cópia.

### <a name="download-a-file"></a>Transferir um ficheiro

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Faça o download de um diretório

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

Este exemplo resulta num diretório nomeado `C:\myDirectory\myFileShareDirectory` que contém todos os ficheiros descarregados.

### <a name="download-the-contents-of-a-directory"></a>Descarregue o conteúdo de um diretório

Pode descarregar o conteúdo de um diretório sem copiar o próprio diretório contendo o próprio símbolo wildcard (*).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Anexar a `--recursive` bandeira para descarregar ficheiros em todos os sub-directórios.

### <a name="download-specific-files"></a>Descarregue ficheiros específicos

Pode especificar nomes completos de ficheiros ou usar nomes parciais com caracteres wildcard (*).

#### <a name="specify-multiple-complete-file-names"></a>Especifique vários nomes completos de ficheiros

Utilize o comando [de cópia azcopia](storage-ref-azcopy-copy.md) com a `--include-path` opção. Separe os nomes individuais dos ficheiros utilizando um ponto e vírgula `;` ().

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

Neste exemplo, a AzCopy transfere o `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` diretório e o `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` ficheiro. Tem de incluir a `--recursive` opção de transferir todos os ficheiros do `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` diretório.

Também pode excluir ficheiros utilizando a `--exclude-path` opção. Para saber mais, consulte os docs de referência [de cópias de azcopia.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Use caracteres wildcard

Utilize o comando [de cópia azcopia](storage-ref-azcopy-copy.md) com a `--include-pattern` opção. Especifique nomes parciais que incluem os caracteres wildcard. Separar nomes utilizando um ponto e vírgula `;` ().

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Também pode excluir ficheiros utilizando a `--exclude-pattern` opção. Para saber mais, consulte os docs de referência [de cópias de azcopia.](storage-ref-azcopy-copy.md)

As `--include-pattern` `--exclude-pattern` opções aplicam-se apenas aos dados de filenames e não ao caminho.  Se quiser copiar todos os ficheiros de texto que existem numa árvore de diretório, use `–recursive` a opção para obter todo o diretório e, em seguida, use o `–include-pattern` e especificar para obter todos os `*.txt` ficheiros de texto.

## <a name="copy-files-between-storage-accounts"></a>Copiar ficheiros entre contas de armazenamento

Pode utilizar o AzCopy para copiar ficheiros para outras contas de armazenamento. A operação de cópia é sincronizada, pelo que quando o comando é devolvido, indica que todos os ficheiros foram copiados.

O AzCopy utiliza [APIs](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url) [de servidor a servidor,](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) por isso os dados são copiados diretamente entre servidores de armazenamento. Estas operações de cópia não utilizam a largura de banda de rede do seu computador. Pode aumentar a produção destas operações definindo o valor da `AZCOPY_CONCURRENCY_VALUE` variável ambiente. Para saber mais, consulte [a produção otimiza.](storage-use-azcopy-configure.md#optimize-throughput)

Esta secção contém os seguintes exemplos:

> [!div class="checklist"]
> * Copie um ficheiro para outra conta de armazenamento
> * Copie um diretório para outra conta de armazenamento
> * Copie uma parte de ficheiro para outra conta de armazenamento
> * Copie todas as ações de ficheiros, diretórios e ficheiros para outra conta de armazenamento

> [!TIP]
> Pode ajustar a sua operação de cópia utilizando bandeiras opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Copiar listas de controlo de acesso (ACLs) juntamente com os ficheiros.|**---conserva-smb-permissões** = \[ verdadeiro \| falso\]|
> |Copie informações de propriedade SMB juntamente com os ficheiros.|**--preservar-smb-info** = \[ verdadeiro \| falso\]|
> |Copie ficheiros como Apêndice Blobs ou Page Blobs.|**--blob-type** = \[ \|Apendb BlockBlob PageBlob \|\]|
> |Copiar para um nível de acesso específico (como o nível de arquivo).|**--bloco-blob-tier** = \[ Nenhum \| Arquivo Quente \| Cool \|\]|
> 
> Para obter uma lista completa, consulte [as opções.](storage-ref-azcopy-copy.md#options)

### <a name="copy-a-file-to-another-storage-account"></a>Copie um ficheiro para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Copie um diretório para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>Copie uma parte de ficheiro para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Copie todas as ações de ficheiros, diretórios e ficheiros para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>Sincronizar ficheiros

Pode sincronizar o conteúdo de uma partilha de ficheiros com outra partilha de ficheiros. Também pode sincronizar o conteúdo de um diretório numa partilha de ficheiros com o conteúdo de um diretório que está localizado noutra partilha de ficheiros. A sincronização é só de ida. Por outras palavras, você escolhe qual destes dois pontos finais é a fonte e qual é o destino. A sincronização também utiliza o servidor para as APIs do servidor.

> [!NOTE]
> Atualmente, este cenário é suportado apenas para contas que não têm um espaço hierárquico de nomes. A versão atual do AzCopy não sincroniza entre os Ficheiros Azure e o Blob Storage.

O comando [de sincronização](storage-ref-azcopy-sync.md) compara nomes de ficheiros e últimos cartões de tempo modificados. Desa estama a `--delete-destination` bandeira opcional a um valor de ou para apagar `true` `prompt` ficheiros no diretório de destino se esses ficheiros já não existirem no diretório de origem.

Se colocar a `--delete-destination` bandeira no `true` AzCopy elimina ficheiros sem fornecer uma solicitação. Se desejar que apareça uma solicitação antes de o AzCopy apagar um ficheiro, coloque a `--delete-destination` bandeira em `prompt` .

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

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Atualizar uma partilha de ficheiros com alterações a outra partilha de ficheiros

A primeira partilha de ficheiros que aparece neste comando é a fonte. O segundo é o destino.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Atualizar um diretório com alterações a um diretório em outra partilha de ficheiros

O primeiro diretório que aparece neste comando é a fonte. O segundo é o destino.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>Atualizar uma partilha de ficheiros para corresponder ao conteúdo de uma imagem de partilha

A primeira partilha de ficheiros que aparece neste comando é a fonte. No final do URI, apeia a cadeia `&sharesnapshot=` seguida do valor **DateTime** do instantâneo. 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

Para saber mais sobre fotos partilhadas, consulte [a visão geral das fotos de partilha para Azure Files](https://docs.microsoft.com/azure/storage/files/storage-snapshots-files).

## <a name="next-steps"></a>Próximos passos

Encontre mais exemplos em qualquer um destes artigos:

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)

- [Transferir dados com AzCopy e armazenamento de bolhas](storage-use-azcopy-blobs.md)

- [Transfira dados com baldes AzCopy e Amazon S3](storage-use-azcopy-s3.md)

- [Configurar, otimizar e resolver problemas AzCopy](storage-use-azcopy-configure.md)
