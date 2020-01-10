---
title: Compreender a tarefa de monitorização no Azure Stream Analytics
description: Este artigo descreve como monitorizar tarefas de Azure Stream Analytics no portal do Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2018
ms.custom: seodec18
ms.openlocfilehash: 4e9f90035816269d2d41781be34d0d8080628b12
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431655"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Compreender a monitorização de tarefa do Stream Analytics e como monitorizar consultas

## <a name="introduction-the-monitor-page"></a>Introdução: A página objeto da monitorização
O portal do Azure superfícies principais métricas de desempenho que podem ser usadas para monitorar e solucionar problemas de desempenho de consulta e de trabalho. Para ver estas métricas, navegue para a tarefa de Stream Analytics está interessado em ver as métricas para e ver que o **monitorização** secção na página de descrição geral.  

![Monitorização de ligação de tarefa do Stream Analytics](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Será apresentada a janela conforme mostrado:

![Dashboard de monitorização de tarefa do Stream Analytics](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Métricas disponíveis para o Stream Analytics
| Métrica                 | Definição                               |
| ---------------------- | ---------------------------------------- |
| Eventos de Entrada Pendentes       | Número de eventos de entrada que estão pendentes. Um valor diferente de zero para essa métrica implica que seu trabalho não é capaz de acompanhar o número de eventos de entrada. Se esse valor for lentamente crescente ou consistentemente diferente de zero, você deverá escalar horizontalmente seu trabalho. Você pode saber mais visitando [entender e ajustar as unidades de streaming](stream-analytics-streaming-unit-consumption.md). |
| Erros de Conversão de Dados | Número de eventos de saída que não foi possível converter para o esquema de saída esperada. A política de erro pode ser alterada para "descartar" para descartar eventos que encontrem esse cenário. |
| Eventos de Entrada Antigos       | Eventos cujo carimbo de data/hora do aplicativo é anterior ao horário de chegada em mais de 5 minutos. |
| Falha no pedido de funções | Número de chamadas de função, com falhas do Azure Machine Learning (se presente). |
| Eventos de Função        | Número de eventos enviados para a função do Azure Machine Learning (se presente). |
| Pedidos de Função      | Número de chamadas para a função do Azure Machine Learning (se presente). |
| Erros de Desserialização de entrada       | Número de eventos de entrada que não puderam ser desserializados.  |
| Bytes de Evento de Entrada      | Quantidade de dados recebidos pela tarefa de Stream Analytics, em bytes. Isto pode ser utilizado para validar que eventos estão a ser enviados para a origem de entrada. |
| Eventos de Entrada           | Número de registros desserializados dos eventos de entrada. Essa contagem não inclui eventos de entrada que resultam em erros de desserialização. |
| Origens de Entrada Recebidas       | Número de mensagens recebidas pelo trabalho. Para o Hub de eventos, uma mensagem é um único EventData. Para BLOB, uma mensagem é um único BLOB. Observe que as fontes de entrada são contadas antes da desserialização. Se houver erros de desserialização, as fontes de entrada poderão ser maiores que os eventos de entrada. Caso contrário, ele pode ser menor ou igual a eventos de entrada, pois cada mensagem pode conter vários eventos. |
| Eventos de Entrada atrasados      | Eventos que chegaram depois da janela de tolerância de chegada tardia configurada. Saiba mais sobre [Azure Stream Analytics considerações sobre ordem de evento](stream-analytics-out-of-order-and-late-events.md) . |
| Eventos de fora de ordem    | Número de eventos recebidos fora de ordem que foram removidos ou tendo em conta um período de tempo ajustado com base na política de ordenação de eventos. Isso pode ser afetado pela configuração da definição de janela de tolerância de fora de ordem. |
| Eventos de Saída          | Quantidade de dados enviados por tarefa do Stream Analytics para o destino de saída, no número de eventos. |
| Erros de Tempo de Execução         | Número total de erros relacionados ao processamento de consulta (excluindo erros encontrados ao ingerir eventos ou resultados de saída) |
| % de utilização SU       | A utilização da unidade de transmissão em fluxo é atribuída a uma tarefa do separador escala da tarefa. Deve este indicador atingir 80% ou acima, há grande probabilidade de que o processamento de eventos poderá ser atrasado ou parado o progresso. |
| Atraso de Marca de Água       | O atraso de limite de tamanho máximo todas as partições de todos os resultados da tarefa. |

Você pode usar essas métricas para [monitorar o desempenho de seu trabalho de Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Personalizar a monitorização no portal do Azure
Pode ajustar o tipo de gráfico, métricas apresentadas e intervalo nas definições de editar gráfico de tempo. Para obter detalhes, consulte [como personalizar monitorização](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Gráfico de tempos de monitor de consulta do Stream Analytics](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Saída mais recente
Outro ponto de dados interessantes para monitorizar a tarefa é a hora da última saída, mostrada na página de descrição geral.
Desta vez, é a hora de aplicação (ou seja, o tempo com o carimbo de hora a partir dos dados de eventos) de saída mais recentes do seu trabalho.

## <a name="get-help"></a>Obter ajuda
Para mais assistência, tente ler o nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
