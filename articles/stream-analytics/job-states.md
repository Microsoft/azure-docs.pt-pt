---
title: Azure Stream Analytics afirma
description: Este artigo descreve os quatro estados diferentes de um trabalho de Stream Analytics; correndo, parado, degradado, e falhou.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 008493c6eb8840752be3815d30a5df5bad42aff2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023862"
---
# <a name="azure-stream-analytics-job-states"></a>Azure Stream Analytics afirma

Um trabalho da Stream Analytics pode estar num dos quatro estados a qualquer momento: correr, parar, degradar-se ou falhar. Pode encontrar o estado do seu trabalho na página geral do seu trabalho stream Analytics no portal Azure. 

| Estado | Descrição | Ações recomendadas |
| --- | --- | --- |
| **Em Execução** | O seu trabalho está a ser feito em eventos de leitura Azure provenientes das fontes de entrada definidas, processando-os e escrevendo os resultados para os lavatórios de saída configurados. | É uma boa prática acompanhar o desempenho do seu trabalho através da monitorização de [métricas chave.](./stream-analytics-set-up-alerts.md#scenarios-to-monitor) |
| **Parada** | O seu trabalho está parado e não processa eventos. | ND | 
| **Degradado** | Pode haver problemas intermitentes com as ligações de entrada e saída. Estes erros são chamados erros transitórios que podem fazer com que o seu trabalho entre num estado degradado. Stream Analytics tentará imediatamente recuperar de tais erros e regressar a um estado de funcionamento (em poucos minutos). Estes erros podem acontecer devido a problemas de rede, disponibilidade de outros recursos Azure, erros de desseialização, etc. O desempenho do seu trabalho pode ser impactado quando o trabalho está em estado de degradação.| Pode olhar para os [registos de diagnóstico ou de atividade](./stream-analytics-job-diagnostic-logs.md#debugging-using-activity-logs) para saber mais sobre a causa destes erros transitórios. Em casos como erros de deserialização, recomenda-se que tome medidas corretivas para garantir que os eventos não sejam mal formados. Se o trabalho continuar a atingir o limite de utilização de recursos, tente aumentar o número de SU ou [paralelizar o seu trabalho](./stream-analytics-parallelization.md). Noutros casos em que não possa tomar qualquer medida, o Stream Analytics tentará recuperar para um estado *de Corrida.* <br> Pode utilizar a métrica [do atraso da marca de água](./stream-analytics-set-up-alerts.md#scenarios-to-monitor) para entender se estes erros transitórios estão a afetar o desempenho do seu trabalho.|
| **Com falhas** | O seu trabalho encontrou um erro crítico que resultou num estado falhado. Os acontecimentos não são lidos e processados. Os erros de tempo de execução são uma causa comum para os postos de trabalho acabarem num estado falhado. | Pode [configurar alertas](./stream-analytics-set-up-alerts.md#set-up-alerts-in-the-azure-portal) para que seja notificado quando o trabalho for para o estado falhado. <br> <br>Pode depurar usando [registos](./stream-analytics-job-diagnostic-logs.md#debugging-using-activity-logs) de atividade e recursos para identificar a causa raiz e resolver o problema.|

## <a name="next-steps"></a>Passos seguintes
* [Alertas de configuração para trabalhos do Azure Stream Analytics](stream-analytics-set-up-alerts.md)
* [Métricas disponíveis em Stream Analytics](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics)
* [Resolução de problemas utilizando registos de atividade e recursos](./stream-analytics-job-diagnostic-logs.md)