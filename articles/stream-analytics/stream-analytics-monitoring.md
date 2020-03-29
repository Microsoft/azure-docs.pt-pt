---
title: Compreender a monitorização do trabalho no Azure Stream Analytics
description: Este artigo descreve como monitorizar os trabalhos da Azure Stream Analytics no portal Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2018
ms.custom: seodec18
ms.openlocfilehash: 4e9f90035816269d2d41781be34d0d8080628b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431655"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Compreender a monitorização do trabalho do Stream Analytics e como monitorizar as consultas

## <a name="introduction-the-monitor-page"></a>Introdução: A página do monitor
O portal Azure apresenta métricas de desempenho chave que podem ser usadas para monitorizar e resolver problemas a sua consulta e desempenho no trabalho. Para ver estas métricas, navegue para o trabalho do Stream Analytics para o qual está interessado em ver métricas e ver a secção **de Monitorização** na página 'Visão Geral'.  

![Ligação de monitorização de trabalho stream Analytics](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

A janela aparecerá como mostrado:

![Painel de monitorização de trabalho stream Analytics](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Métricas disponíveis para Stream Analytics
| Métrica                 | Definição                               |
| ---------------------- | ---------------------------------------- |
| Eventos de entrada atrasados       | Número de eventos de entrada que estão atrasados. Um valor não nulo para esta métrica implica que o seu trabalho não é capaz de acompanhar o número de eventos que chegam. Se este valor está lentamente a aumentar ou a não zero de forma consistente, deve aumentar o seu trabalho. Pode aprender mais visitando [Compreender e ajustar unidades de streaming.](stream-analytics-streaming-unit-consumption.md) |
| Erros de Conversão de Dados | Número de eventos de produção que não puderam ser convertidos para o esquema de saída esperado. A política de erro pode ser alterada para 'Drop' para deixar cair eventos que se deparam com este cenário. |
| Eventos de entrada precoce       | Eventos cujo carimbo de tempo de candidatura é mais cedo do que a hora de chegada por mais de 5 minutos. |
| Pedidos de função falhados | Número de chamadas de função de aprendizagem automática azure falhadas (se presentes). |
| Eventos de Função        | Número de eventos enviados para a função Azure Machine Learning (se presente). |
| Pedidos de Função      | Número de chamadas para a função Azure Machine Learning (se presente). |
| Erros de desserialização de entrada       | Número de eventos de entrada que não podiam ser desserializados.  |
| Bytes de Evento de Entrada      | Quantidade de dados recebidos pelo trabalho do Stream Analytics, em bytes. Isto pode ser usado para validar que os eventos estão a ser enviados para a fonte de entrada. |
| Eventos de entrada           | Número de registos desserializados dos eventos de entrada. Esta contagem não inclui eventos que resultem em erros de desserialização. |
| Fontes de entrada recebidas       | Número de mensagens recebidas pelo trabalho. Para o Event Hub, uma mensagem é um único EventData. Para Blob, uma mensagem é uma única bolha. Por favor, note que as fontes de entrada são contadas antes da desserialização. Se houver erros de desserialização, as fontes de entrada podem ser maiores do que os eventos de entrada. Caso contrário, pode ser menos ou igual a eventos de entrada, uma vez que cada mensagem pode conter vários eventos. |
| Eventos de entrada tardia      | Eventos que chegaram mais tarde do que a janela de tolerância à chegada tardia configurada. Saiba mais sobre as considerações de ordem de [eventos do Azure Stream Analytics.](stream-analytics-out-of-order-and-late-events.md) |
| Eventos fora de ordem    | Número de eventos recebidos fora de ordem que foram retirados ou dado um carimbo de tempo ajustado, com base na Política de Ordenação de Eventos. Isto pode ser impactado pela configuração da definição da janela de tolerância fora de ordem. |
| Eventos de Saída          | Quantidade de dados enviados pelo trabalho do Stream Analytics para o alvo de saída, em número de eventos. |
| Erros de tempo de execução         | Número total de erros relacionados com o processamento de consultas (excluindo erros encontrados durante a ingestão de eventos ou resultados de produção) |
| SU % Utilização       | A utilização das Unidades de Streaming(s) atribuídas a um trabalho a partir do separador Escala do trabalho. Se este indicador chegar aos 80%, ou acima, existe uma grande probabilidade de que o processamento de eventos possa ser adiado ou parado de fazer progressos. |
| Atraso na marca de água       | O atraso máximo da marca de água em todas as divisórias de todas as saídas no trabalho. |

Pode utilizar estas métricas para [monitorizar o desempenho do seu trabalho](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor)em Stream Analytics . 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Monitorização personalizada no portal Azure
Pode ajustar o tipo de gráfico, métricas mostradas e intervalo de tempo nas definições do Gráfico de Edição. Para mais detalhes, consulte [Como Personalizar a Monitorização](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Gráfico de tempo de monitor de consulta stream Analytics](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Última saída
Outro ponto de dados interessante para monitorizar o seu trabalho é o momento da última saída, mostrado na página 'Visão Geral'.
Desta vez é o tempo de candidatura (isto é, o tempo que utiliza o carimbo de tempo dos dados do evento) da mais recente saída do seu trabalho.

## <a name="get-help"></a>Obter ajuda
Para mais assistência, tente ler o nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
