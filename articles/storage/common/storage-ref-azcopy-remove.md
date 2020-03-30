---
title: azcopy remover [ remover ] Microsoft Docs
description: Este artigo fornece informações de referência para a remoção de azcopia do comando.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: abce1acb88e920c0de7bbb6447ec9d838f10486c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033985"
---
# <a name="azcopy-remove"></a>azcopy remove

Elimine bolhas ou ficheiros de uma conta de armazenamento Azure.

## <a name="synopsis"></a>Sinopse

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com a AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)
- [Configure, otimize e problemas AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exemplos

Remova uma única bolha com SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Remova todo um diretório virtual com um SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Remova apenas as bolhas superiores dentro de um diretório virtual, mas não os seus subdiretórios:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Remova um subconjunto de bolhas num diretório virtual (por exemplo: apenas ficheiros JPG e pdf, ou se o nome blob for "exactName"):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

Remova todo um diretório virtual, mas exclua certas bolhas do âmbito (por exemplo: cada bolha que comece com foo ou termina com barra):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Remova bolhas específicas e diretórios virtuais colocando os seus caminhos relativos (NÃO codificados por URL) num ficheiro:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

Remova um único ficheiro de uma conta blob storage que tenha um espaço de nome hierárquico (inclua/exclua não suportado).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Remova um único diretório uma conta blob storage que tenha um espaço de nome hierárquico (incluir/excluir não suportado):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Opções

**--excluir-caminho string**      Exclua estes caminhos ao remover. Esta opção não suporta caracteres wildcard (*). Verifica o prefixo relativo do caminho. Por exemplo: myFolder;myFolder/subDirName/file.pdf.

**-excluir a** cadeia de padrão Exclua ficheiros onde o nome corresponde à lista de padrões. Por exemplo: *.jpg;* pdf;exactNome

**-h, -ajuda** para remover

**--incluir a** corda do caminho Inclua apenas estes caminhos ao remover. Esta opção não suporta caracteres wildcard (*). Verifica o prefixo relativo do caminho. Por exemplo: myFolder;myFolder/subDirName/file.pdf

**-incluir** a cadeia de padrões Inclua apenas ficheiros onde o nome corresponde à lista de padrões. Por exemplo: *.jpg;* pdf;exactNome

**--lista de ficheiros** define a localização de um ficheiro que contém a lista de ficheiros e diretórios a eliminar. Os caminhos relativos devem ser delimitados por quebras de linha, e os caminhos NÃO devem ser codificados por URL.

**-cadeia de log-level** Defina a verbosidade do registo para o ficheiro de registo. Os níveis disponíveis incluem: INFO (todos os pedidos/respostas), AVISO (respostas lentas), ERROR (apenas pedidos falhados) e NENHUM (nenhum registo de saída). ('INFO' predefinido) ("INFO" padrão)

**-recursivo**                Procure sub-directórios de forma recursiva ao sincronizar entre diretórios.

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

|Opção|Descrição|
|---|---|
|--cap-mbps uint32|Cobre a taxa de transferência, em megabits por segundo. A entrada momentânea pode variar ligeiramente a partir da tampa. Se esta opção estiver definida para zero, ou for omitida, a entrada não está limitada.|
|--cadeia tipo saída|Formato da saída do comando. As escolhas incluem: texto, json. O valor predefinido é "texto".|

## <a name="see-also"></a>Consulte também

- [azcopy](storage-ref-azcopy.md)
