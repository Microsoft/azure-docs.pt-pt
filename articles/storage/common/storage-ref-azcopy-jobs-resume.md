---
title: retomada de trabalhos do azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando retomar trabalhos do azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8786da42f000a2f13279499159a7af424aa10748
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195792"
---
# <a name="azcopy-jobs-resume"></a>retomada de trabalhos do azcopy

Retoma o trabalho existente com a ID de trabalho fornecida.

## <a name="synopsis"></a>Resumo

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|--destino-Cadeia de caracteres SAS|SAS de destino do destino para determinada JobId.|
|--excluir cadeia de caracteres|Sem Exclua essas transferências com falha ao retomar o trabalho. Os arquivos devem ser separados por '; '.|
|-h,--ajuda|Mostra o conteúdo da ajuda para o comando retomar.|
|--incluir Cadeia de caracteres|Filtro: inclua apenas essas transferências com falha ao retomar o trabalho. Os arquivos devem ser separados por '; '.|
|--origem – cadeia de caracteres SAS |SAS de origem da origem para determinada JobId.|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

|Opção|Descrição|
|---|---|
|--Cap-Mbps UInt32|Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.|
|--Cadeia de caracteres de tipo de saída|Formato da saída do comando. As opções incluem: Text, JSON. O valor padrão é "text".|

## <a name="see-also"></a>Consulte também

- [trabalhos do azcopy](storage-ref-azcopy-jobs.md)
