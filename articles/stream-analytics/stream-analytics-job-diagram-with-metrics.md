---
title: Depuração orientada por dados no Azure Stream Analytics
description: Este artigo descreve como resolver problemas no seu trabalho Azure Stream Analytics usando o diagrama de trabalho e métricas no portal Azure.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/01/2017
ms.openlocfilehash: 6d20454515088ccca87665d9b3b27c0d82c3cdf9
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020405"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Depuração orientada para dados com o diagrama de trabalhos

O diagrama de trabalho na lâmina **de monitorização** do portal Azure pode ajudá-lo a visualizar o seu pipeline de trabalho. Mostra entradas, saídas e passos de consultas. Pode utilizar o diagrama de trabalhos para examinar as métricas de cada passo, de modo a isolar mais depressa a origem de um problema durante a resolução de problemas.

## <a name="using-the-job-diagram"></a>Usando o diagrama de trabalho

No portal Azure, enquanto em um trabalho stream Analytics, em **SUPORTE + RESOLUÇÃO DE PROBLEMAS,** selecione **Diagrama de Trabalho**:

![Diagrama de trabalho com métricas - localização](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Selecione cada passo de consulta para ver a secção correspondente num painel de edição de consulta. Um gráfico métrico para o passo é apresentado num painel inferior na página.

![Diagrama de trabalho com métricas - trabalho básico](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Para ver as divisórias da entrada dos Hubs de Eventos Azure, **selecione . . . .** Um menu de contexto aparece. Também pode ver a fusão de entradas.

![Diagrama de trabalho com métricas - expandir a partição](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Para ver o gráfico métrico para apenas uma partição, selecione o nó de partição. As métricas são mostradas na parte inferior da página.

![Diagrama de trabalho com métricas - mais métricas](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Para ver o gráfico de métricas para uma fusão, selecione o nó de fusão. O gráfico seguinte mostra que nenhum evento foi abandonado ou ajustado.

![Diagrama de trabalho com métricas - grelha](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Para ver os detalhes do valor métrico e do tempo, aponte para o gráfico.

![Diagrama de trabalho com métricas - pairar](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Resolução de problemas utilizando métricas

A **métrica QueryLastProcessedTime** indica quando um passo específico recebeu dados. Olhando para a topologia, pode trabalhar para trás a partir do processador de saída para ver que passo não está a receber dados. Se um passo não for obter dados, vá para o passo de consulta pouco antes. Verifique se o passo de consulta anterior tem uma janela de tempo, e se o tempo suficiente passou para que os dados de produção. (Note que as janelas do tempo são estaladiços à hora.)
 
Se o passo de consulta anterior for um processador de entrada, utilize as métricas de entrada para ajudar a responder às seguintes perguntas direcionadas. Podem ajudá-lo a determinar se um trabalho está a obter dados das suas fontes de entrada. Se a consulta estiver particionada, examine cada partição.
 
### <a name="how-much-data-is-being-read"></a>Quantos dados estão a ser lidos?

*   **InputEventsSourcesTotal** é o número de unidades de dados lidas. Por exemplo, o número de bolhas.
*   **InputEventsTotal** é o número de eventos lidos. Esta métrica está disponível por partição.
*   **InputEventsInBytesTotal** é o número de bytes lidos.
*   **O InputEventsLastArrivalTime** é atualizado com o tempo de cada evento recebido.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>O tempo está a avançar? Se forem lidos os eventos reais, a pontuação poderá não ser emitida.

*   **InputEventsLastPunctuationTime** indica quando uma pontuação foi emitida para fazer com que a hora continue a avançar. Se a pontuação não for emitida, o fluxo de dados pode ser bloqueado.
 
### <a name="are-there-any-errors-in-the-input"></a>Há algum erro na entrada?

*   **InputEventsEventDataNullTotal** é uma contagem de eventos que têm dados nulos.
*   **InputEventsSerializerErrorsTotal** é uma contagem de eventos que não poderiam ser deseralizados corretamente.
*   **InputEventsDegradedTotal** é uma contagem de eventos que tiveram outro problema que não com a dessesserialização.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Os eventos estão a ser abandonados ou ajustados?

*   **InputEventsEarlyTotal** é o número de eventos que têm um carimbo de tempo de aplicação antes da marca de água alta.
*   **InputEventsLateTotal** é o número de eventos que têm um carimbo de tempo de aplicação após a marca de água alta.
*   **InputEventsDroppedBeforeApplicationStartTimeTotal** é o número de eventos que caiu antes da hora de início do trabalho.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Estamos a ficar para trás na leitura de dados?

*   **Eventos de entrada Backlogged (Total)** diz-lhe quantas mais mensagens precisam de ser lidas para entradas de Event Hubs e Azure IoT Hub. Quando este número é maior que 0, significa que o seu trabalho não pode processar os dados tão rápido quanto está a entrar. Neste caso, poderá ser necessário aumentar o número de Unidades de Streaming e/ou certificar-se de que o seu trabalho pode ser paralelo. Pode ver mais informações sobre isto na  [página de paralelização](./stream-analytics-parallelization.md)de consultas. 


## <a name="get-help"></a>Obter ajuda
Para assistência adicional, experimente o nosso [Microsoft Q&Uma página de perguntas para Azure Stream Analytics](/answers/topics/azure-stream-analytics.html). 

## <a name="next-steps"></a>Próximos passos
* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar tarefas do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência linguística de consulta de stream analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API de gestão de stream Analytics](/rest/api/streamanalytics/)