---
title: Depuração baseada em dados no Azure Stream Analytics
description: Este artigo descreve como resolver problemas o seu trabalho de Azure Stream Analytics usando o diagrama de trabalho e métricas no portal Azure.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 00e1175cc85f1c34260c5e03ed3f81ac25cf7448
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836503"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Depuração orientada para dados com o diagrama de trabalhos

O diagrama de trabalho na lâmina **de monitorização** do portal Azure pode ajudá-lo a visualizar o seu pipeline de trabalho. Mostra entradas, saídas e passos de consultas. Pode utilizar o diagrama de trabalhos para examinar as métricas de cada passo, de modo a isolar mais depressa a origem de um problema durante a resolução de problemas.

## <a name="using-the-job-diagram"></a>Usando o diagrama de trabalho

No portal Azure, durante um trabalho de Stream Analytics, sob **SUPORTE + RESOLUÇÃO DE PROBLEMAS,** selecione **o diagrama de trabalho:**

![Diagrama de trabalho com métricas - localização](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Selecione cada passo de consulta para ver a secção correspondente num painel de edição de consulta. Um gráfico métrico para o passo é apresentado num painel inferior na página.

![Diagrama de trabalho com métricas - trabalho básico](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Para ver as divisórias da entrada do Azure Event Hubs, selecione **. . . . .** Aparece um menu de contexto. Também pode ver a fusão de entrada.

![Diagrama de trabalho com métricas - expandir a partição](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Para ver o gráfico métrico para apenas uma única divisória, selecione o nó de partição. As métricas são mostradas na parte inferior da página.

![Diagrama de trabalho com métricas - mais métricas](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Para ver o gráfico de métricas para uma fusão, selecione o nó de fusão. O gráfico seguinte mostra que nenhum evento foi abandonado ou ajustado.

![Diagrama de trabalho com métricas - grelha](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Para ver os detalhes do valor e tempo métricos, aponte para o gráfico.

![Diagrama de trabalho com métricas - hover](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Resolução de problemas usando métricas

A métrica **QueryLastProcessedTime** indica quando um passo específico recebeu dados. Olhando para a topologia, pode trabalhar para trás a partir do processador de saída para ver que passo não está a receber dados. Se um passo não for obter dados, vá ao passo de consulta antes dele. Verifique se o passo de consulta anterior tem uma janela de tempo e se já passou tempo suficiente para que ele passasse para os dados de saída. (Note que as janelas do tempo são arrancadas à hora.)
 
Se o passo de consulta anterior for um processador de entrada, utilize as métricas de entrada para ajudar a responder às seguintes questões direcionadas. Podem ajudá-lo a determinar se um trabalho está a obter dados das suas fontes de entrada. Se a consulta estiver particionada, examine cada partição.
 
### <a name="how-much-data-is-being-read"></a>Quantos dados estão a ser lidos?

*   **InputEventsSourcesTotal** é o número de unidades de dados lidas. Por exemplo, o número de bolhas.
*   **InputEventsTotal** é o número de eventos lidos. Esta métrica está disponível por partição.
*   **InputEventsInBytesTotal** é o número de bytes lidos.
*   **InputEventsLastArrivalTime** é atualizado com o tempo enqueado de cada evento recebido.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>O tempo está a avançar? Se forem lidos os eventos reais, a pontuação poderá não ser emitida.

*   **InputEventsLastPunctuationTime** indica quando uma pontuação foi emitida para fazer com que a hora continue a avançar. Se a pontuação não for emitida, o fluxo de dados pode ser bloqueado.
 
### <a name="are-there-any-errors-in-the-input"></a>Há algum erro na entrada?

*   **InputEventsEventDataNullTotal** é uma contagem de eventos que têm dados nulos.
*   **InputEventsSerializerErrorsTotal** é uma contagem de eventos que não puderam ser desserializados corretamente.
*   **InputEventsDegradedTotal** é uma contagem de eventos que tiveram um problema que não seja a desserialização.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Os eventos estão a ser abandonados ou ajustados?

*   **InputEventsEarlyTotal** é o número de eventos que têm um carimbo de tempo de aplicação antes da marca de água elevada.
*   **InputEventsLateTotal** é o número de eventos que têm um carimbo de tempo de aplicação após a marca de água elevada.
*   **InputEventsDropBeforeApplicationStartTimeTotal** é o número que os eventos diminuíram antes da hora de início do trabalho.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Estamos a ficar para trás na leitura de dados?

*   **Input Events Backlogged (Total)** diz-lhe quantas mais mensagens precisam de ser lidas para os Centros de Eventos e inputs do Hub Azure IoT. Quando este número é superior a 0, significa que o seu trabalho não pode processar os dados tão rapidamente quanto está a chegar. Neste caso, poderá ter de aumentar o número de Unidades de Streaming e/ou certificar-se de que o seu trabalho pode ser paralelo. Pode ver mais informações sobre isso na página de [paraleção](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization)de consultas . 


## <a name="get-help"></a>Obter ajuda
Para obter assistência adicional, experimente o nosso [Microsoft Q&Uma página de perguntas para o Azure Stream Analytics](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html). 

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar tarefas do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência rest API de gestão stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
