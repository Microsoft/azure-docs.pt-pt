---
title: lista de azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy List.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f02c1afadf18a7d3170eb178696487464e4a0bd3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034102"
---
# <a name="azcopy-list"></a>azcopy list

Lista as entidades em um determinado recurso.

## <a name="synopsis"></a>Resumo

Somente os contêineres de blob têm suporte na versão atual.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com o armazenamento de BLOBs e AzCopy](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e o armazenamento de arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exemplos

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|-h,--ajuda|Mostra o conteúdo da ajuda para o comando de lista.|
|--legível por máquina|Lista os tamanhos de arquivo em bytes.|
|--Mega-unidades|Exibe unidades em ordens de 1000, não 1024.|
|--com contagem alta|Conta o número total de arquivos e seus tamanhos.|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

|Opção|Descrição|
|---|---|
|--Cap-Mbps UInt32|Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.|
|--Cadeia de caracteres de tipo de saída|Formato da saída do comando. As opções incluem: Text, JSON. O valor padrão é "text".|

## <a name="see-also"></a>Consulte também

- [azcopy](storage-ref-azcopy.md)
