---
title: azcopy remover | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0cc366ab2cdad9c7258dca905d8f4a06472119fe
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195909"
---
# <a name="azcopy-remove"></a>azcopy remover

Exclui entidades de Azure Storage Blob, arquivo e Azure Data Lake Storage Gen2.

## <a name="synopsis"></a>Resumo

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="examples"></a>Exemplos

Remova um único blob com SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Remova um diretório virtual inteiro com uma SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Remova somente os BLOBs superiores dentro de um diretório virtual, mas não seus subdiretórios:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Remova um subconjunto de BLOBs em um diretório virtual (por exemplo: somente arquivos jpg e PDF, ou se o nome do blob for "exactname"):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

Remova um diretório virtual inteiro, mas exclua determinados BLOBs do escopo (por exemplo: cada blob que começa com foo ou termina com bar):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Remover um único arquivo de Data Lake Storage Gen2 (incluir e excluir sem suporte):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Remover um único diretório de Data Lake Storage Gen2 (incluir e excluir sem suporte):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|--excluir cadeia de caracteres|Exclua os arquivos onde o nome corresponde à lista padrão. Por exemplo: *. jpg;* . PDF; exatoname|
|-h,--ajuda|Mostrar o conteúdo da ajuda para o comando remover.|
|--incluir Cadeia de caracteres|Inclua apenas os arquivos em que o nome corresponde à lista padrão. Por exemplo: *. jpg;* . PDF; exatoname|
|--Cadeia de caracteres de nível de log|Defina o detalhamento de log para o arquivo de log. Os níveis disponíveis incluem: INFORMAÇÕES (todas as solicitações/respostas), aviso (respostas lentas), erro (somente solicitações com falha) e nenhum (nenhum log de saída). (padrão "INFO")|
|--recursivo|Examine os subdiretórios recursivamente ao sincronizar entre diretórios.|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

|Opção|Descrição|
|---|---|
|--Cap-Mbps UInt32|Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.|
|--Cadeia de caracteres de tipo de saída|Formato da saída do comando. As opções incluem: Text, JSON. O valor padrão é "text".|

## <a name="see-also"></a>Consulte também

- [azcopy](storage-ref-azcopy.md)
