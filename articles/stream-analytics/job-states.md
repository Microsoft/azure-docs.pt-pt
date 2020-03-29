---
title: Estados de trabalho da Azure Stream Analytics
description: Este artigo descreve os quatro estados diferentes de um trabalho de Stream Analytics; correndo, parou, degradou, e falhou.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 454474333cac94dc25deae8196e9ba45bcb3a574
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75359771"
---
# <a name="azure-stream-analytics-job-states"></a>Estados de trabalho da Azure Stream Analytics

Um trabalho de Stream Analytics pode estar num dos quatro estados a qualquer momento: correr, parar, degradar ou falhar. Pode encontrar o estado do seu trabalho na página de visão geral do seu trabalho stream analytics no portal Azure. 

| Estado | Descrição | Ações recomendadas |
| --- | --- | --- |
| **A executar** | O seu trabalho está a decorrer em eventos de leitura do Azure provenientes das fontes de entrada definidas, processando-os e escrevendo os resultados para os lavatórios de saída configurados. | É uma boa prática acompanhar o desempenho do seu trabalho monitorizando [as métricas-chave.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) |
| **Parada** | O seu trabalho está parado e não processa eventos. | ND | 
| **Degradado** | Pode haver problemas intermitentes com as suas ligações de entrada e saída. Estes erros são chamados erros transitórios que podem fazer com que o seu trabalho entre num estado degradado. O Stream Analytics tentará imediatamente recuperar de tais erros e regressar a um estado de Execução (dentro de poucos minutos). Estes erros podem ocorrer devido a problemas de rede, disponibilidade de outros recursos Azure, erros de desserialização, etc. O desempenho do seu trabalho pode ser impactado quando o trabalho está em estado degradado.| Pode olhar para os [registos](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) de diagnóstico ou atividade para saber mais sobre a causa destes erros transitórios. Em casos como erros de desserialização, recomenda-se que tome medidas corretivas para garantir que os eventos não sejam mal formados. Se o trabalho continuar a atingir o limite de utilização dos recursos, tente aumentar o número de SU ou [paralelamente ao seu trabalho](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). Noutros casos em que não pode tomar qualquer ação, o Stream Analytics tentará recuperar para um estado de *Corrida.* <br> Pode utilizar a métrica de atraso da [marca de água](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) para perceber se estes erros transitórios estão a afetar o desempenho do seu trabalho.|
| **Falhou** | O seu trabalho encontrou um erro crítico que resultou num estado falhado. Os acontecimentos não são lidos e processados. Os erros de tempo de execução são uma causa comum para os empregos que acabam num estado falhado. | Pode [configurar alertas](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) para que seja notificado quando o trabalho for para o estado falhado. <br> <br>Pode depurar a utilização de [registos](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) de atividade e diagnóstico para identificar a causa da raiz e resolver o problema.|

## <a name="next-steps"></a>Passos seguintes
* [Alertas de configuração para empregos da Azure Stream Analytics](stream-analytics-set-up-alerts.md)
* [Métricas disponíveis no Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Resolução de problemas utilizando atividade e registos de diagnóstico](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
