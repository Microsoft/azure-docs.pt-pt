---
title: Estados de tarefa do Stream Analytics do Azure
description: Este artigo descreve os diferentes Estados de uma tarefa do Stream Analytics
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/06/2019
ms.openlocfilehash: 28e0e69d3a6a4d3a38146cbf2c49426b3b16c784
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789457"
---
# <a name="azure-stream-analytics-job-states"></a>Estados de tarefa do Stream Analytics do Azure

Uma tarefa do Stream Analytics pode ser de uma das quatro Estados num determinado momento. Pode encontrar o estado da tarefa na página de descrição geral da tarefa do Stream Analytics no portal do Azure. 

| Estado | Descrição | Ações recomendadas |
| --- | --- | --- |
| **Em execução** | A tarefa está em execução no Azure ler eventos provenientes de origens de entrada definidas, processá-los e escrever os resultados para os sinks de saída configurados. | É melhor prática para controlar o desempenho de seu trabalho através da monitorização [métricas-chave](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). |
| **Parado** | Seu trabalho é interrompido e não processa eventos. | ND | 
| **Degradado** | Erros transitórios provavelmente têm maior impacto na sua tarefa. Stream Analytics imediatamente tentará recuperar esses erros e retornar a um Estado de execução (dentro de alguns minutos). Estes erros podem acontecer devido a problemas de rede, a disponibilidade dos outros recursos do Azure, desserialização erros etc. Desempenho de seu trabalho poderá ser afetado quando a tarefa está em estado degradado.| Pode examinar os [registos de diagnóstico ou atividade](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) para saber mais sobre a causa destes erros transitórios. Em casos como erros de desserialização, recomenda-se para tomar medidas corretivas para se certificar de eventos não são um formato incorreto. Se a tarefa mantém for atingido o limite de utilização de recursos, tente aumentar o número SU ou [paralelizar seu trabalho](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). Em outros casos em que não é possível efetuar qualquer ação, o Stream Analytics irá tentar recuperar para uma *em execução* estado.  |
| **Falhou** | Sua tarefa encontrou um erro crítico, resultando em estado de falha. Eventos não são lidos e processados. Erros de tempo de execução são uma causa comum para terminar em estado de falha de tarefas. | Pode [configurar alertas](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) para que seja notificado quando o trabalho vai para o estado de falha. <br> <br>Pode depurar usando [atividade e os registos de diagnóstico](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) para identificar a causa raiz e resolver o problema.|

## <a name="next-steps"></a>Passos Seguintes
* [Configurar alertas para tarefas do Azure Stream Analytics](stream-analytics-set-up-alerts.md)
* [Métricas disponíveis no Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Resolver problemas com a atividade e os registos de diagnóstico](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
