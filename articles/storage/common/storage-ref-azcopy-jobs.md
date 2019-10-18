---
title: trabalhos do azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy Jobs.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 2422b16eb89ef6f1a6a1eb703d88f0ff2b76422e
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514726"
---
# <a name="azcopy-jobs"></a>azcopy jobs

Subcomandos relacionados ao gerenciamento de trabalhos.

## <a name="examples"></a>Exemplos

```azcopy
azcopy jobs show [jobID]
```

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|-h,--ajuda|Mostrar o conteúdo da ajuda para o comando trabalhos.|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

|Opção|Descrição|
|---|---|
|--Cap-Mbps UInt32|Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.|
|--Cadeia de caracteres de tipo de saída|Formato da saída do comando. As opções incluem: Text, JSON. O valor padrão é "text".|

## <a name="see-also"></a>Ver também

- [azcopy](storage-ref-azcopy.md)
- [lista de trabalhos do azcopy](storage-ref-azcopy-jobs-list.md)
- [retomada de trabalhos do azcopy](storage-ref-azcopy-jobs-resume.md)
- [azcopy trabalhos de exibição](storage-ref-azcopy-jobs-show.md)
