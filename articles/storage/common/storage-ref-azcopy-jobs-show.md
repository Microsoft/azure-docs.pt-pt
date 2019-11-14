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
ms.openlocfilehash: 7b5f566757dd77a61f252b123d0c9c1b74303fbe
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034126"
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

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com o armazenamento de BLOBs e AzCopy](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e o armazenamento de arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

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

## <a name="see-also"></a>Consulte também

- [trabalhos do azcopy](storage-ref-azcopy-jobs.md)
