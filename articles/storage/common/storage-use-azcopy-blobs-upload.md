---
title: Faça upload de ficheiros para o armazenamento do Azure Blob utilizando a AzCopy v10 | Microsoft Docs
description: Este artigo contém uma coleção de comandos de exemplo AzCopy que o ajudam a enviar ficheiros para o armazenamento de Azure Blob.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 352497f0f4d23250abe9f84121f358589664002b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502917"
---
# <a name="upload-files-to-azure-blob-storage-by-using-azcopy"></a>Faça upload de ficheiros para o armazenamento do Azure Blob utilizando o AzCopy

Pode fazer o upload de ficheiros e diretórios para o armazenamento blob utilizando o utilitário de linha de comando AzCopy v10. 

Para ver exemplos para outros tipos de tarefas, tais como o descarregamento de bolhas, sincronização com armazenamento Blob ou cópia de bolhas entre contas, consulte os links apresentados na secção [Etapas Seguintes](#next-steps) deste artigo.

## <a name="get-started"></a>Introdução

Consulte o [artigo da AzCopy](storage-use-azcopy-v10.md) para descarregar o AzCopy e saiba como pode fornecer credenciais de autorização ao serviço de armazenamento.

> [!NOTE] 
> Os exemplos deste artigo assumem que forneceu credenciais de autorização utilizando o Azure Ative Directory (Azure AD).
>
> Se preferir usar um token SAS para autorizar o acesso a dados blob, então pode anexar esse símbolo ao URL de recursos em cada comando AzCopy. Por exemplo: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Criar um contentor

Pode utilizar a [azcopia para criar](storage-ref-azcopy-make.md) um recipiente.

> [!TIP]
> Este exemplo encerra argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

Este é um exemplo de tabela:

**Syntax**

`azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'`

**Exemplo**

```azcopy
https://mystorageaccount.blob.core.windows.net/mycontainer
```

**Exemplo (espaço hierárquico)**

```azcopy
https://mystorageaccount.dfs.core.windows.net/mycontainer
```

Para obter documentos de referência detalhados, consulte [a azcopia make](storage-ref-azcopy-make.md).

## <a name="upload-a-file"></a>Carregar um ficheiro

Faça o upload de um ficheiro utilizando o comando [de cópia azcopia.](storage-ref-azcopy-copy.md)

> [!TIP]
> Este exemplo encerra argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

**Syntax**

`azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'`

**Exemplo**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

**Exemplo (espaço hierárquico)**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'
```

Também pode fazer o upload de um ficheiro utilizando um símbolo wildcard (*) em qualquer lugar do caminho do ficheiro ou nome de ficheiro. Por exemplo: `'C:\myDirectory\*.txt'` ou `C:\my*\*.txt` . .

## <a name="upload-a-directory"></a>Faça upload de um diretório

Faça o upload de um diretório utilizando o comando [de cópia azcopia.](storage-ref-azcopy-copy.md) 

Este exemplo copia um diretório (e todos os ficheiros desse diretório) para um recipiente de bolhas. O resultado é um diretório no contentor com o mesmo nome.

> [!TIP]
> Este exemplo encerra argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

**Syntax**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive`

**Exemplo**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive
```

**Exemplo (espaço hierárquico)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive
```

Para copiar para um diretório dentro do contentor, basta especificar o nome desse diretório na sua cadeia de comando.

**Exemplo**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive
```

**Exemplo (espaço hierárquico)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive
```

Se especificar o nome de um diretório que não existe no contentor, o AzCopy cria um novo diretório com esse nome.

## <a name="upload-directory-contents"></a>Carregar conteúdos de diretório

Faça o upload do conteúdo de um diretório utilizando o comando [de cópia azcopia.](storage-ref-azcopy-copy.md) Utilize o símbolo wildcard (*) para carregar o conteúdo sem copiar o próprio diretório.

> [!TIP]
> Este exemplo encerra argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

**Syntax**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` 

**Exemplo**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'
```

**Exemplo (espaço hierárquico)**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'
```

Anexar a `--recursive` bandeira para carregar ficheiros em todas as subdiretivas.

## <a name="upload-specific-files"></a>Fazer upload de ficheiros específicos

Pode fazer o upload de ficheiros específicos utilizando nomes de ficheiros completos, nomes parciais com caracteres wildcard (*), ou utilizando datas e horários.

> [!TIP]
> Estes exemplos encerram argumentos de caminho com citações simples ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

### <a name="specify-multiple-complete-file-names"></a>Especifique vários nomes completos de ficheiros

Utilize o comando [de cópia azcopia](storage-ref-azcopy-copy.md) com a `--include-path` opção. Separe os nomes individuais dos ficheiros utilizando um ponto e vírgula `;` ().

**Syntax** 

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` 

**Exemplo**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive'
```

**Exemplo (espaço hierárquico)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive'
```

Neste exemplo, a AzCopy transfere o `C:\myDirectory\photos` diretório e o `C:\myDirectory\documents\myFile.txt` ficheiro. Inclua `--recursive` a opção de transferir todos os ficheiros do `C:\myDirectory\photos` diretório.

Também pode excluir ficheiros utilizando a `--exclude-path` opção. Para saber mais, consulte os docs de referência [de cópias de azcopia.](storage-ref-azcopy-copy.md)

### <a name="use-wildcard-characters"></a>Use caracteres wildcard

Utilize o comando [de cópia azcopia](storage-ref-azcopy-copy.md) com a `--include-pattern` opção. Especifique nomes parciais que incluem os caracteres wildcard. Separar nomes utilizando uma esímina `;` (). 

**Syntax**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` 

**Exemplo**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'
```

**Exemplo (espaço hierárquico)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'
```

Também pode excluir ficheiros utilizando a `--exclude-pattern` opção. Para saber mais, consulte os docs de referência [de cópias de azcopia.](storage-ref-azcopy-copy.md)

As `--include-pattern` `--exclude-pattern` opções aplicam-se apenas aos dados de filenames e não ao caminho.  Se quiser copiar todos os ficheiros de texto que existem numa árvore de diretório, use `–recursive` a opção para obter todo o diretório e, em seguida, use o `–include-pattern` e especificar para obter todos os `*.txt` ficheiros de texto.

### <a name="upload-files-that-were-modified-before-or-after-a-date-and-time"></a>Carregar ficheiros que foram modificados antes ou depois de uma data e hora 

Utilize o comando [de cópia azcopia](storage-ref-azcopy-copy.md) com a `--include-before` opção ou `--include-after` opção. Especifique uma data e hora no formato ISO-8601 (Por exemplo: `2020-08-19T15:04:00Z` ). 

Os seguintes exemplos carregam ficheiros que foram modificados na ou após a data especificada.

**Syntax**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` 

**Exemplo**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'
```

**Exemplo (espaço hierárquico)**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'
```

Para obter uma referência detalhada, consulte os documentos de referência da cópia da [azcopia.](storage-ref-azcopy-copy.md)

## <a name="upload-with-index-tags"></a>Upload com etiquetas de índice

Pode carregar um ficheiro e adicionar [tags de índice blob (pré-visualização)](../blobs/storage-manage-find-blobs.md) à bolha-alvo.  

Se estiver a utilizar a autorização Azure AD, o seu principal de segurança deve ser atribuído à função [de Proprietário de Dados de Armazenamento blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) ou deve ser dada permissão à operação do `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [fornecedor de recursos Azure](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) através de uma função Azure personalizada. Se estiver a utilizar um token de Assinatura de Acesso Partilhado (SAS), esse token deve fornecer acesso às etiquetas da bolha através da `t` permissão SAS.

Para adicionar tags, utilize a `--blob-tags` opção juntamente com um url codificado par de valores-chave. Por exemplo, para adicionar a chave `my tag` e um `my tag value` valor, adicionaria `--blob-tags='my%20tag=my%20tag%20value'` ao parâmetro de destino. 

Separe várias etiquetas de índice utilizando um ampersand `&` ().  Por exemplo, se pretender adicionar uma chave `my second tag` e um `my second tag value` valor, a cadeia completa de opções será `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

Os exemplos a seguir mostram como utilizar a `--blob-tags` opção.

> [!TIP]
> Este exemplo encerra argumentos de caminho com citações únicas ('). Utilize aspas únicas em todas as cápsulas de comando, exceto no Windows Command Shell (cmd.exe). Se estiver a utilizar uma Concha de Comando do Windows (cmd.exe), encobre argumentos de caminho com citações duplas ("") em vez de cotações individuais (').

**Faça upload de um ficheiro**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

**Faça upload de um diretório**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

**Carregar conteúdos de diretório**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

> [!NOTE]
> Se especificar um diretório para a fonte, todas as bolhas que são copiadas para o destino terão as mesmas etiquetas que especifica no comando.

## <a name="upload-with-optional-flags"></a>Upload com bandeiras opcionais

Pode ajustar a sua operação de upload utilizando bandeiras opcionais. Aqui estão alguns exemplos.

|Scenario|Sinalizador|
|---|---|
|Carregue os ficheiros como Blobs de Acréscimo ou Blobs de Páginas.|**--blob-type** = \[ \|Apendb BlockBlob PageBlob \|\]|
|Carregue para uma camada de acesso específica (como a camada de arquivo).|**--bloco-blob-tier** = \[ Nenhum \| Arquivo Quente \| Cool \|\]|

Para obter uma lista completa, consulte [as opções.](storage-ref-azcopy-copy.md#options)

## <a name="next-steps"></a>Passos seguintes

Encontre mais exemplos nestes artigos:

- [Exemplos: Transferência](storage-use-azcopy-blobs-download.md)
- [Exemplos: Copiar entre contas](storage-use-azcopy-blobs-copy.md)
- [Exemplos: Sincronizar](storage-use-azcopy-blobs-synchronize.md)
- [Exemplos: Registos Amazon S3](storage-use-azcopy-s3.md)
- [Exemplos: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Exemplos: Ficheiros Azure](storage-use-azcopy-files.md)
- [Tutorial: migrar dados no local para o armazenamento na cloud com o AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Consulte estes artigos para configurar definições, otimizar o desempenho e resolver problemas:

- [Definições de configuração azCopy](storage-ref-azcopy-configuration-settings.md)
- [Otimizar o desempenho da AzCopy](storage-use-azcopy-optimize.md)
- [Problemas problemas problemas AzCopy V10 em armazenamento Azure usando ficheiros de log](storage-use-azcopy-configure.md)