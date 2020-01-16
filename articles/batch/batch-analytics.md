---
title: Análise do lote do Azure | Microsoft Docs
description: Referência para a análise de lote do Azure.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: jushiman
ms.openlocfilehash: 39a8bfb6a48bf55ae9f2ec36f7716959e6ada9dd
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76027395"
---
# <a name="batch-analytics"></a>Batch Analytics
Os tópicos na análise do lote contêm informações de referência para os eventos e alertas disponíveis para os recursos do serviço de lote.

Consulte [log de diagnóstico do lote do Azure](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) para obter mais informações sobre como habilitar e consumir logs de diagnóstico do lote.

## <a name="diagnostic-logs"></a>Registos de diagnósticos

O serviço de lote do Azure emite os eventos de log de diagnóstico a seguir durante o tempo de vida de determinados recursos do lote.

**Eventos de registo do serviço**
* [Criação de conjunto](batch-pool-create-event.md)
* [Início de eliminação de conjunto](batch-pool-delete-start-event.md)
* [Conjunto de eliminação completa](batch-pool-delete-complete-event.md)
* [Início de redimensionamento de conjunto](batch-pool-resize-start-event.md)
* [Conclusão de redimensionamento de conjunto](batch-pool-resize-complete-event.md)
* [Zahájení úlohy](batch-task-start-event.md)
* [Tarefa concluída](batch-task-complete-event.md)
* [Falha de tarefa](batch-task-fail-event.md)