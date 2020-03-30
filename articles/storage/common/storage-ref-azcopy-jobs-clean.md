---
title: azcopy jobs clean [ Microsoft Docs
description: Este artigo fornece informações de referência para o comando limpo de trabalhos de azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7ae14c3606dfe6bffa8481682843f3f2e85c2131
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033728"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

Remova todos os ficheiros de registo e plano para todos os trabalhos

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com a AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)
- [Configure, otimize e problemas AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exemplos

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Opções

**-h, --ajuda**                Ajuda para limpar.

**-com** a cadeia de estatuto apenas remova os postos de trabalho com este estatuto, valores disponíveis: Cancelado, Concluído, Falhado, InProgress, Tudo (padrão "All")

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

**--cap-mbps uint32**      Cobre a taxa de transferência, em megabits por segundo. A entrada momentânea pode variar ligeiramente a partir da tampa. Se esta opção estiver definida para zero, ou for omitida, a entrada não está limitada.

**-formato** de cadeia do tipo de saída da saída do comando. As escolhas incluem: texto, json. O valor predefinido é "texto". ("texto por defeito")

## <a name="see-also"></a>Consulte também

- [azcopy jobs](storage-ref-azcopy-jobs.md)
