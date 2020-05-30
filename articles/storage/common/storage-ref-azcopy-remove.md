---
title: azcopia remover / Microsoft Docs
description: Este artigo fornece informações de referência para o comando de remoção de azcopia.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 05/04/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ab085b9a41120a9f56c1c2e39a89def8c3893747
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2020
ms.locfileid: "84221075"
---
# <a name="azcopy-remove"></a>azcopy remove

Elimine as bolhas ou ficheiros de uma conta de armazenamento Azure.

## <a name="synopsis"></a>Sinopse

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceptuais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Dados de transferência com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com a AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)
- [Configurar, otimizar e resolver problemas AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exemplos

Remova uma única bolha com SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Remova um diretório virtual inteiro com um SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Remova apenas as bolhas superiores dentro de um diretório virtual, mas não os seus sub-directórios:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Remova um subconjunto de bolhas num diretório virtual (por exemplo: apenas ficheiros jpg e pdf, ou se o nome da bolha for "nome exato"):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include-pattern="*.jpg;*.pdf;exactName"
```

Remova um diretório virtual inteiro, mas exclua certas bolhas do âmbito (por exemplo: cada bolha que comece com foo ou termine com barra):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude-pattern="foo*;*bar"
```

Remova bolhas específicas e diretórios virtuais colocando os seus caminhos relativos (NÃO codificados por URL) num ficheiro:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

Remova um único ficheiro de uma conta Blob Storage que tenha um espaço hierárquico (incluir/excluir não suportado).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Remova um único diretório de uma conta Blob Storage que tenha um espaço hierárquico de nomes (incluir/excluir não suportado):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Opções

**--Eliminar** o fio de instantâneos Por predefinição, a operação de eliminação falha se uma bolha tiver instantâneos. Especificar 'incluir' para remover a bolha de raiz e todas as suas imagens; especificar alternativamente 'apenas' para remover apenas os instantâneos, mas manter a bolha de raiz.

**...-excluir** a cadeia de caminhos Excluir estes caminhos ao remover. Esta opção não suporta caracteres wildcard (*). Verifica o prefixo relativo do caminho. Por exemplo: myFolder;myFolder/subDirName/file.pdf.

**--excluir-padrão** excluir ficheiros onde o nome corresponde à lista de padrões. Por exemplo: *.jpg;*. pdf;nome exato

**--força-se-ler-apenas**    Ao eliminar um ficheiro ou pasta Azure Files, force a eliminação a funcionar mesmo que o objeto existente tenha o seu conjunto de atributos apenas de leitura

**-h, -- ajuda** para remover

**--incluir** a corda do caminho Inclua apenas estes caminhos ao remover. Esta opção não suporta caracteres wildcard (*). Verifica o prefixo relativo do caminho. Por exemplo: myFolder;myFolder/subDirName/file.pdf

**-- incluir** a cadeia de padrão Inclua apenas ficheiros onde o nome corresponde à lista de padrões. Por exemplo: *.jpg;*. pdf;nome exato

**--lista de ficheiros** a cadeia Define a localização de um ficheiro que contém a lista de ficheiros e diretórios a eliminar. Os caminhos relativos devem ser delimitados por quebras de linha, e os caminhos NÃO devem ser codificados por URL.

**--cadeia de nível de log** Define a verbosidade do registo para o ficheiro de registo. Os níveis disponíveis incluem: INFO (todos os pedidos/respostas), ADVERTÊNCIA (respostas lentas), ERROR (apenas pedidos falhados) e NENHUM (sem registos de saída). (predefinido 'INFO') (predefinição "INFO")

**--recursivo**                Procure sub-directórios recursivamente ao sincronizar entre diretórios.

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

|Opção|Descrição|
|---|---|
|--cap-mbps uint32|Reduz a taxa de transferência, em megabits por segundo. A produção momentesa pode variar ligeiramente da tampa. Se esta opção for definida para zero, ou for omitida, a produção não está limitada.|
|--cadeia do tipo de saída|Formato da saída do comando. As escolhas incluem: texto, json. O valor predefinido é "texto".|
|--cadeia de sufixos de confiança da Microsoft-Sufixes   |Especifica sufixos de domínio adicionais onde podem ser enviados tokens de login do Azure Ative Directory.  O padrão é '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;* core.usgovcloudapi.net.' Qualquer listado aqui é adicionado ao padrão. Para a segurança, só deve colocar os domínios microsoft Azure aqui. Separe várias entradas com pontos e vírgulas.|

## <a name="see-also"></a>Ver também

- [azcopia](storage-ref-azcopy.md)
