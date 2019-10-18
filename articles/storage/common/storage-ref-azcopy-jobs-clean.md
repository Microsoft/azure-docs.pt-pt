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
ms.openlocfilehash: 31529155ee44b2bcfad90e8634053403dfe8fc8c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518515"
---
# <a name="azcopy-jobs-clean"></a>limpeza de trabalhos do azcopy

Remover todos os arquivos de log e de plano de todos os trabalhos

```
azcopy jobs clean [flags]
```

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

## <a name="see-also"></a>Ver também

- [trabalhos do azcopy](storage-ref-azcopy-jobs.md)
