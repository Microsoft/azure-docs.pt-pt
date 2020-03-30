---
title: Análise de Lote Azure
description: Os tópicos no Batch Analytics contêm informações de referência para os eventos e alertas disponíveis para os recursos de serviço do Lote.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025967"
---
# <a name="batch-analytics"></a>Batch Analytics
Os tópicos no Batch Analytics contêm informações de referência para os eventos e alertas disponíveis para os recursos de serviço do Lote.

Consulte o registo de diagnóstico do [Lote Azure](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) para obter mais informações sobre a ativação e consumo de registos de diagnóstico do Lote.

## <a name="diagnostic-logs"></a>Registos de diagnósticos

O serviço Azure Batch emite os seguintes eventos de registo de diagnóstico durante a vida útil de certos recursos do Lote.

**Eventos de Registo de Serviço**
* [Piscina criar](batch-pool-create-event.md)
* [Piscina apaga início](batch-pool-delete-start-event.md)
* [Piscina apaga completo](batch-pool-delete-complete-event.md)
* [Início de redimensionado da piscina](batch-pool-resize-start-event.md)
* [Redimensionar a piscina completa](batch-pool-resize-complete-event.md)
* [Início da tarefa](batch-task-start-event.md)
* [Tarefa completa](batch-task-complete-event.md)
* [Falha de tarefa](batch-task-fail-event.md)