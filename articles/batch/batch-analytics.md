---
title: Azure Batch Analytics
description: Os tópicos no Batch Analytics contêm informações de referência para os eventos e alertas disponíveis para os recursos de serviço batch.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: 0d55ecd7f10e267a9cb469dffcdf26c131c8ce41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91849516"
---
# <a name="batch-analytics"></a>Batch Analytics

Os tópicos nesta secção contêm informações de referência para os eventos e alertas disponíveis para os recursos de serviço batch.

Consulte [o registo de diagnóstico do Azure Batch](./batch-diagnostics.md) para obter mais informações sobre a ativação e consumo de registos de diagnóstico do Lote.

## <a name="diagnostic-logs"></a>Registos de diagnósticos

O serviço Azure Batch emite os seguintes eventos de registo de diagnóstico durante a vida útil de certos recursos do Lote.

### <a name="service-log-events"></a>Eventos de Registo de Serviço

- [Criar piscina](batch-pool-create-event.md)
- [Início de eliminação de piscinas](batch-pool-delete-start-event.md)
- [Excluir piscina completa](batch-pool-delete-complete-event.md)
- [Início de redimensionar piscina](batch-pool-resize-start-event.md)
- [Piscina redimensionar completo](batch-pool-resize-complete-event.md)
- [Autoescala da piscina](batch-pool-autoscale-event.md)
- [Início de tarefa](batch-task-start-event.md)
- [Tarefa concluída](batch-task-complete-event.md)
- [Falha de tarefa](batch-task-fail-event.md)
- [Falha no horário de tarefas](batch-task-schedule-fail-event.md)
