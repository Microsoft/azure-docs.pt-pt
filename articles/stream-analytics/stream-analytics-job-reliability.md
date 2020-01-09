---
title: Evitar interrupções de serviços em tarefas do Azure Stream Analytics
description: Este artigo descreve orientações sobre a realização de suas tarefas do Stream Analytics atualizar resiliente.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d2d21e081b274bd985c48dac91fff5203a6b5b8a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425992"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garante a confiabilidade de tarefa do Stream Analytics durante as atualizações de serviço

Parte do que está a ser um serviço totalmente gerido é a capacidade para introduzir novas funcionalidades de serviço e aprimoramentos rapidamente. Como resultado, o Stream Analytics pode ter uma atualização de serviço implementar numa base semanal (ou com mais frequência). Independentemente da quantidade de teste é realizado ainda há um risco de que uma tarefa existente, em execução pode ser interrompidos devido à introdução de um bug. Se você estiver executando trabalhos de missão crítica, esses riscos precisam ser evitados. Você pode reduzir esse risco seguindo o modelo de **[região emparelhada](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** do Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Como é que as regiões emparelhadas do Azure abordar esta questão?

Stream Analytics garante tarefas em regiões emparelhadas são atualizadas em lotes separados. Como resultado, há um intervalo de tempo suficiente entre as atualizações para identificar possíveis problemas e corrigi-los.

_Com exceção da Índia Central_ (cujo região emparelhada, Sul da Índia, não tem a presença de Stream Analytics), a implementação de uma atualização para o Stream Analytics não ocorreria ao mesmo tempo num conjunto de regiões emparelhadas. Implementações em várias regiões **no mesmo grupo de** pode ocorrer **ao mesmo tempo**.

O artigo sobre **[disponibilidade e regiões emparelhadas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** tem as informações mais atualizadas em que regiões são emparelhadas.

É recomendável implantar trabalhos idênticos em ambas as regiões emparelhadas. Em seguida, você deve [monitorar esses trabalhos](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) para ser notificado quando algo inesperado ocorrer. Se um desses trabalhos terminar em um estado de [falha](https://docs.microsoft.com/azure/stream-analytics/job-states) após uma atualização de serviço Stream Analytics, você poderá entrar em contato com o atendimento ao cliente para ajudar a identificar a causa raiz. Você também deve fazer failover de todos os consumidores downstream para a saída do trabalho íntegro.

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar tarefas do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da REST API de gestão de análise de Stream](https://msdn.microsoft.com/library/azure/dn835031.aspx)
