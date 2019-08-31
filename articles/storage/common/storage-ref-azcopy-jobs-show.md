---
title: azcopy trabalhos de exibição | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy Jobs show.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d4369bd0c986ee20a0796436fea47509a711de4f
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195961"
---
# <a name="azcopy-jobs-show"></a>azcopy trabalhos de exibição

Mostra informações detalhadas para a ID de trabalho fornecida.

## <a name="synopsis"></a>Resumo

Se apenas a ID do trabalho for fornecida sem um sinalizador, o resumo do progresso do trabalho será retornado.

Se o `with-status` sinalizador for definido, a lista de transferências no trabalho com o valor especificado será mostrada.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|-h,--ajuda|Mostra o conteúdo da ajuda para o comando show.|
|--com-cadeia de caracteres de status|Liste apenas as transferências de trabalho com esse status, valores disponíveis: Iniciado, êxito, falha|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

|Opção|Descrição|
|---|---|
|--Cap-Mbps UInt32|Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.|
|--Cadeia de caracteres de tipo de saída|Formato da saída do comando. As opções incluem: Text, JSON. O valor padrão é "text".|

## <a name="see-also"></a>Consulte também

- [trabalhos do azcopy](storage-ref-azcopy-jobs.md)
