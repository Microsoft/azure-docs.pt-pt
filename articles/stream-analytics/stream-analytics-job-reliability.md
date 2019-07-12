---
title: Evitar interrupções de serviços em tarefas do Azure Stream Analytics
description: Este artigo descreve orientações sobre a realização de suas tarefas do Stream Analytics atualizar resiliente.
services: stream-analytics
author: jseb225
ms.author: sidram
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: e38f8a923daa210d8aa5b56631e5f8157d4b3f70
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620870"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garante a confiabilidade de tarefa do Stream Analytics durante as atualizações de serviço

Parte do que está a ser um serviço totalmente gerido é a capacidade para introduzir novas funcionalidades de serviço e aprimoramentos rapidamente. Como resultado, o Stream Analytics pode ter uma atualização de serviço implementar numa base semanal (ou com mais frequência). Independentemente da quantidade de teste é realizado ainda há um risco de que uma tarefa existente, em execução pode ser interrompidos devido à introdução de um bug. Se estiver a executar missão crítica das tarefas, estes riscos tem de ser evitada. Pode reduzir este risco através do Azure seguinte **[região emparelhada](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** modelo. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Como é que as regiões emparelhadas do Azure abordar esta questão?

Stream Analytics garante tarefas em regiões emparelhadas são atualizadas em lotes separados. Assim existe um intervalo de tempo suficiente entre as atualizações para identificar potenciais problemas e resolvê-los.

_Com exceção da Índia Central_ (cujo região emparelhada, Sul da Índia, não tem a presença de Stream Analytics), a implementação de uma atualização para o Stream Analytics não ocorreria ao mesmo tempo num conjunto de regiões emparelhadas. Implementações em várias regiões **no mesmo grupo de** pode ocorrer **ao mesmo tempo**.

O artigo sobre **[disponibilidade e regiões emparelhadas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** tem as informações mais atualizadas em que regiões são emparelhadas.

Recomenda-se para implementar tarefas idênticas em ambas as regiões emparelhadas. Em seguida, deve [monitorizar estas tarefas](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) para serem notificadas quando ocorre algo inesperado. Se um dos seguintes tarefas ends cópia de segurança num [estado de falha](https://docs.microsoft.com/azure/stream-analytics/job-states) após uma atualização de serviço do Stream Analytics, pode contactar o suporte ao cliente para ajudar a identificar a causa raiz. Também deve efetuar a ativação pós-falha nenhum consumidor jusante para o resultado da tarefa em bom estado.

## <a name="next-steps"></a>Passos Seguintes

* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar tarefas do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da REST API de gestão de análise de Stream](https://msdn.microsoft.com/library/azure/dn835031.aspx)
