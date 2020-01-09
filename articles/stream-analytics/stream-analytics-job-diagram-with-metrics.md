---
title: Depuração controlada por dados no Azure Stream Analytics
description: Este artigo descreve como solucionar problemas de Azure Stream Analytics trabalho usando o diagrama de trabalho e as métricas no portal do Azure.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 3c0c29e1793e56efae8d13cb01d57faf257d8805
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426082"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Depuração controlada por dados usando o diagrama de trabalho

O diagrama de trabalho na folha **monitoramento** do portal do Azure pode ajudá-lo a visualizar o pipeline de trabalho. Mostra entradas, saídas e passos de consultas. Pode utilizar o diagrama de trabalhos para examinar as métricas de cada passo, de modo a isolar mais depressa a origem de um problema durante a resolução de problemas.

## <a name="using-the-job-diagram"></a>Usando o diagrama de trabalho

No portal do Azure, enquanto estiver em um trabalho de Stream Analytics, em **suporte + solução de problemas**, selecione **diagrama de trabalho**:

![Diagrama de trabalho com métricas-localização](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Selecione cada etapa de consulta para ver a seção correspondente em um painel de edição de consulta. Um gráfico de métrica para a etapa é exibido em um painel inferior na página.

![Diagrama de trabalho com métricas-trabalho básico](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Para ver as partições de entrada dos hubs de eventos do Azure, selecione **...** Um menu de contexto é exibido. Você também pode ver a mesclagem de entrada.

![Diagrama de trabalho com métricas-expandir partição](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Para ver o gráfico de métrica para apenas uma única partição, selecione o nó partição. As métricas são mostradas na parte inferior da página.

![Diagrama de trabalho com métricas-mais métricas](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Para ver o gráfico de métricas de uma fusão, selecione o nó de fusão. O gráfico a seguir mostra que nenhum evento foi descartado ou ajustado.

![Diagrama de trabalho com métricas-grade](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Para ver os detalhes do valor e da hora da métrica, aponte para o gráfico.

![Diagrama de trabalho com métricas-focalizar](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Solucionar problemas usando métricas

A métrica **QueryLastProcessedTime** indica quando uma etapa específica recebeu dados. Examinando a topologia, você pode trabalhar para trás do processador de saída para ver qual etapa não está recebendo dados. Se uma etapa não estiver obtendo dados, vá para a etapa de consulta logo antes dela. Verifique se a etapa de consulta anterior tem uma janela de tempo e se o tempo suficiente foi passado para que ela gere dados. (Observe que as janelas de tempo são encaixadas na hora.)
 
Se a etapa de consulta anterior for um processador de entrada, use as métricas de entrada para ajudar a responder as perguntas de destino a seguir. Eles podem ajudá-lo a determinar se um trabalho está obtendo dados de suas fontes de entrada. Se a consulta estiver particionada, examine cada partição.
 
### <a name="how-much-data-is-being-read"></a>Qual a quantidade de dados que está sendo lida?

*   **InputEventsSourcesTotal** é o número de unidades de dados lidas. Por exemplo, o número de BLOBs.
*   **InputEventsTotal** é o número de eventos lidos. Esta métrica está disponível por partição.
*   **Inputeventstotal** é o número de bytes lidos.
*   **InputEventsLastArrivalTime** é atualizado com cada tempo de enfileiramento do evento recebido.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>O tempo está avançando? Se os eventos reais forem lidos, a pontuação não poderá ser emitida.

*   **InputEventsLastPunctuationTime** indica quando uma pontuação foi emitida para fazer com que a hora continue a avançar. Se a pontuação não for emitida, o fluxo de dados poderá ser bloqueado.
 
### <a name="are-there-any-errors-in-the-input"></a>Há erros na entrada?

*   **InputEventsEventDataNullTotal** é uma contagem de eventos que têm dados nulos.
*   **InputEventsSerializerErrorsTotal** é uma contagem de eventos que não puderam ser desserializados corretamente.
*   **InputEventsDegradedTotal** é uma contagem de eventos que tiveram um problema diferente de desserialização.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Os eventos estão sendo descartados ou ajustados?

*   **InputEventsEarlyTotal** é o número de eventos que têm um carimbo de data/hora do aplicativo antes da marca d' água alta.
*   **InputEventsLateTotal** é o número de eventos que têm um carimbo de data/hora do aplicativo após a marca d' água alta.
*   **InputEventsDroppedBeforeApplicationStartTimeTotal** é o número de eventos descartados antes da hora de início do trabalho.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Estamos atrás de ler os dados?

*   **Eventos de entrada pendências (total)** informa quantas mensagens mais precisam ser lidas para os hubs de eventos e as entradas do Hub IOT do Azure. Quando esse número é maior que 0, isso significa que seu trabalho não pode processar os dados tão rapidamente quanto eles chegam. Nesse caso, talvez seja necessário aumentar o número de unidades de streaming e/ou garantir que seu trabalho possa ser paralelizado. Você pode ver mais informações sobre isso na [página de paralelização de consulta](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). 


## <a name="get-help"></a>Obter ajuda
Para obter assistência adicional, experimente nosso [Fórum de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar tarefas do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da REST API de gestão de análise de Stream](https://msdn.microsoft.com/library/azure/dn835031.aspx)
