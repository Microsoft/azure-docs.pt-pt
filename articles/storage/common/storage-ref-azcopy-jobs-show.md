---
title: azcopy trabalhos de exibição | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy Jobs show.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 42b8bde9adb6980ff2c7004d43b02fc1fdc38363
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513445"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Mostra informações detalhadas para a ID de trabalho fornecida.

## <a name="synopsis"></a>Resumo

Se apenas a ID do trabalho for fornecida sem um sinalizador, o resumo do progresso do trabalho será retornado.

A contagem de bytes e a porcentagem concluída que aparece quando você executa esse comando refletem somente os arquivos que são concluídos no trabalho. Eles não refletem os arquivos parcialmente concluídos.

Se o sinalizador de `with-status` for definido, a lista de transferências no trabalho com o valor especificado será mostrada.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|-h,--ajuda|Mostra o conteúdo da ajuda para o comando show.|
|--com-cadeia de caracteres de status|Lista somente as transferências de trabalho com esse status, valores disponíveis: iniciado, êxito, falha|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

|Opção|Descrição|
|---|---|
|--Cap-Mbps UInt32|Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.|
|--Cadeia de caracteres de tipo de saída|Formato da saída do comando. As opções incluem: Text, JSON. O valor padrão é "text".|

## <a name="see-also"></a>Ver também

- [trabalhos do azcopy](storage-ref-azcopy-jobs.md)
