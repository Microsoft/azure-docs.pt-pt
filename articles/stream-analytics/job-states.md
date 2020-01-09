---
title: Azure Stream Analytics Estados de trabalho
description: Este artigo descreve os quatro Estados diferentes de um trabalho de Stream Analytics; em execução, parado, degradado e com falha.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 454474333cac94dc25deae8196e9ba45bcb3a574
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75359771"
---
# <a name="azure-stream-analytics-job-states"></a>Azure Stream Analytics Estados de trabalho

Um trabalho de Stream Analytics pode estar em um dos quatro Estados em um determinado momento: em execução, parado, degradado ou com falha. Você pode encontrar o estado do seu trabalho na página de visão geral do trabalho Stream Analytics no portal do Azure. 

| Estado | Descrição | Ações recomendadas |
| --- | --- | --- |
| **Executado** | Seu trabalho está em execução no Azure lendo eventos provenientes das fontes de entrada definidas, processando-os e gravando os resultados nos coletores de saída configurados. | É uma prática recomendada controlar o desempenho do trabalho monitorando [métricas de chave](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). |
| **Interrompido** | Seu trabalho foi interrompido e não processa eventos. | N/D | 
| **Degradado** | Pode haver problemas intermitentes com as conexões de entrada e saída. Esses erros são chamados de erros transitórios que podem fazer seu trabalho entrar em um estado degradado. Stream Analytics tentará imediatamente se recuperar desses erros e retornar para um estado de execução (dentro de alguns minutos). Esses erros podem ocorrer devido a problemas de rede, disponibilidade de outros recursos do Azure, erros de desserialização etc. O desempenho do trabalho pode ser afetado quando o trabalho está em estado degradado.| Você pode examinar os [logs de atividade ou diagnóstico](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) para saber mais sobre a causa desses erros transitórios. Em casos como erros de desserialização, é recomendável tomar uma ação corretiva para garantir que os eventos não sejam malformados. Se o trabalho continuar a atingir o limite de utilização de recursos, tente aumentar o número de SU ou [paralelizar seu trabalho](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). Em outros casos em que você não pode realizar nenhuma ação, Stream Analytics tentará recuperar em um estado de *execução* . <br> Você pode usar a métrica de [atraso de marca d' água](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) para entender se esses erros transitórios estão afetando o desempenho do trabalho.|
| **Falhou** | Seu trabalho encontrou um erro crítico que resulta em um estado de falha. Os eventos não são lidos e processados. Os erros de tempo de execução são uma causa comum para trabalhos que terminam em um estado de falha. | Você pode [configurar alertas](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) para que você seja notificado quando o trabalho entrar em estado de falha. <br> <br>Você pode depurar usando [logs de diagnóstico e de atividade](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) para identificar a causa raiz e resolver o problema.|

## <a name="next-steps"></a>Passos seguintes
* [Alertas de instalação para trabalhos de Azure Stream Analytics](stream-analytics-set-up-alerts.md)
* [Métricas disponíveis no Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Solucionar problemas usando logs de atividade e diagnóstico](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
