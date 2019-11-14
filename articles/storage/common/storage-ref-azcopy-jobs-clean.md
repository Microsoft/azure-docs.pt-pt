---
title: limpeza de trabalhos do azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando de limpeza de trabalhos do azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7ae14c3606dfe6bffa8481682843f3f2e85c2131
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033728"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

Remover todos os arquivos de log e de plano de todos os trabalhos

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com o armazenamento de BLOBs e AzCopy](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e o armazenamento de arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exemplos

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Opções

**-h,--ajuda**                Ajuda para limpar.

**--with-status** a cadeia de caracteres remove apenas os trabalhos com esse status, valores disponíveis: cancelado, concluído, falha, InProgress, todos (padrão "todos")

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

**--Cap-Mbps UInt32**      Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.

**--** formato da cadeia de caracteres do tipo de saída da saída do comando. As opções incluem: Text, JSON. O valor padrão é ' Text '. (padrão "texto")

## <a name="see-also"></a>Consulte também

- [trabalhos do azcopy](storage-ref-azcopy-jobs.md)
