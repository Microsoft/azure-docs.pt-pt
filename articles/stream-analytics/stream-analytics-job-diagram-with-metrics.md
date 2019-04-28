---
title: Depuração no Azure Stream Analytics condicionada por dados
description: Este artigo descreve como resolver problemas de sua tarefa do Azure Stream Analytics, utilizando o diagrama de tarefas e as métricas no portal do Azure.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 4a6d359b27b9a2e52d71ed5f8547041645147605
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479971"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Depuração usando o diagrama de tarefas condicionadas por dados

O diagrama de tarefas no **monitorização** painel no portal do Azure pode ajudá-lo a visualizar o seu pipeline de tarefas. Mostra entradas, saídas e passos de consultas. Pode utilizar o diagrama de trabalhos para examinar as métricas de cada passo, de modo a isolar mais depressa a origem de um problema durante a resolução de problemas.

## <a name="using-the-job-diagram"></a>Usando o diagrama de tarefas

No portal do Azure, enquanto estiver numa tarefa do Stream Analytics, sob **suporte + resolução de problemas**, selecione **diagrama de tarefas**:

![Diagrama de tarefas com a métrica - localização](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Selecione cada passo de consulta para ver a seção correspondente numa painel de edição de consultas. Um gráfico de métricas para o passo é apresentado num painel inferior da página.

![Diagrama de tarefas com a métrica - trabalho básico](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Para ver as partições da entrada de Event Hubs do Azure, selecione **...** É apresentado um menu de contexto. Também pode ver a fusão de entrada.

![Diagrama de tarefas com a métrica - expanda partição](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Para ver o gráfico de métricas para uma única partição, selecione o nó de partição. As métricas são apresentadas na parte inferior da página.

![Diagrama de tarefas com a métrica - mais métricas](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Para ver o gráfico de métricas para uma fusão, selecione o nó de intercalação de pedidos. A tabela a seguir mostra o que não existem eventos foram removidos ou ajustados.

![Diagrama de tarefas com a métrica - grade](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Para ver os detalhes do valor de métrica e a hora, aponte para o gráfico.

![Diagrama com métricas de tarefa – Paire o rato](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Resolver problemas utilizando as métricas

O **QueryLastProcessedTime** métrica indica quando um passo específico recebeu dados. Ao examinar a topologia, pode trabalhar para trás do processador de saída para ver qual etapa não está a receber dados. Se um passo não é obter dados, vá para o passo de consulta apenas antes dele. Verifique se o passo de consulta anterior tenha uma janela de tempo e, se houve tempo suficiente para o mesmo para dados de saída. (Observe esse tempo windows é ajustado à hora.)
 
Se o passo de consulta anterior é um processador de entrada, utilize as métricas de entrada para ajudar a responder às seguintes questões direcionadas. Eles podem ajudar a determinar se uma tarefa está a receber dados de suas fontes de entrada. Se a consulta estiver particionada, examine cada partição.
 
### <a name="how-much-data-is-being-read"></a>A quantidade de dados está a ser lida?

*   **InputEventsSourcesTotal** é o número de unidades de dados de leitura. Por exemplo, o número de blobs.
*   **InputEventsTotal** é o número de eventos lidos. Esta métrica está disponível por partição.
*   **InputEventsInBytesTotal** é o número de bytes lidos.
*   **InputEventsLastArrivalTime** é atualizada com a hora de colocados em fila de todos os eventos recebidos.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>Hora avança? Se forem lidos os eventos reais, a pontuação poderá não ser emitida.

*   **InputEventsLastPunctuationTime** indica quando uma pontuação foi emitida para fazer com que a hora continue a avançar. Se a pontuação não for emitida, o fluxo de dados pode ser bloqueado.
 
### <a name="are-there-any-errors-in-the-input"></a>Existem erros na entrada?

*   **InputEventsEventDataNullTotal** é uma contagem de eventos com dados nulos.
*   **InputEventsSerializerErrorsTotal** é uma contagem dos eventos que não foi possível anular corretamente.
*   **InputEventsDegradedTotal** é uma contagem de eventos que tinham um problema que com a desserialização.
 
### <a name="are-events-being-dropped-or-adjusted"></a>São eventos que está a ser removidos ou ajustados?

*   **InputEventsEarlyTotal** é o número de eventos que tenham um timestamp de aplicação antes do limite superior de tamanho.
*   **InputEventsLateTotal** é o número de eventos que tenham um timestamp de aplicação depois do limite superior de tamanho.
*   **InputEventsDroppedBeforeApplicationStartTimeTotal** é o número de eventos removidos antes da hora de início de tarefa.
 
### <a name="are-we-falling-behind-in-reading-data"></a>São nós atrasado na leitura de dados?

*   **Eventos pendentes (Total) de entrada** indica quantas mais mensagens têm de ser lidas para entradas de Hubs de eventos e o IoT Hub do Azure. Quando este número é superior a 0, significa que a tarefa não é possível processar os dados mais rapidamente à medida que surgem. Neste caso se pretender aumentar o número de unidades de transmissão em fluxo e/ou certifique-se de que a tarefa pode ser colocado em paralelo. Pode ver mais informações sobre isso no [página de paralelização de consultas](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). 


## <a name="get-help"></a>Obter ajuda
Para obter mais ajuda, experimente nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Passos Seguintes
* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar tarefas do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da REST API de gestão de análise de Stream](https://msdn.microsoft.com/library/azure/dn835031.aspx)
