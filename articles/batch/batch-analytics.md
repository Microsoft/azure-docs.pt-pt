---
title: Análise de lotes do Azure | Documentos da Microsoft
description: Referência para análise de lotes do Azure.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 999c3037196044250b8a12d6b6b380553e58c6ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60721560"
---
# <a name="batch-analytics"></a>Batch Analytics
Os tópicos na análise de lotes contêm informações de referência para os eventos e alertas disponíveis para recursos do serviço Batch.

Ver [registo de diagnósticos do Azure Batch](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) para obter mais informações sobre como habilitar e consumir registos de diagnóstico do Batch.

## <a name="diagnostic-logs"></a>Registos de diagnósticos

O serviço Azure Batch emite os seguintes eventos de registo de diagnóstico durante a duração de determinados recursos do Batch.

**Eventos de registo do serviço**
* [Criação de conjunto](batch-pool-create-event.md)
* [Início de eliminação de conjunto](batch-pool-delete-start-event.md)
* [Conjunto de eliminação completa](batch-pool-delete-complete-event.md)
* [Início de redimensionamento de conjunto](batch-pool-resize-start-event.md)
* [Conclusão de redimensionamento de conjunto](batch-pool-resize-complete-event.md)
* [Zahájení úlohy](batch-task-start-event.md)
* [Tarefa concluída](batch-task-complete-event.md)
* [Falha de tarefa](batch-task-fail-event.md)