---
title: Análise de Lote Azure
description: Os tópicos na análise do lote contêm informações de referência para os eventos e alertas disponíveis para os recursos do serviço de lote.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: d9538461dfa48a961f9325566b2adaf265e8daeb
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025967"
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