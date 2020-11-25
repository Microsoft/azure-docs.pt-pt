---
title: Compreender a monitorização de emprego no Azure Stream Analytics
description: Este artigo descreve como monitorizar os trabalhos do Azure Stream Analytics no portal Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2018
ms.custom: seodec18
ms.openlocfilehash: f8e0d49c2a35a1e97c79e6d4b8c867a4a8ab88b3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006683"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Compreender a monitorização do trabalho do Stream Analytics e como monitorizar consultas

## <a name="introduction-the-monitor-page"></a>Introdução: A página do monitor
O portal Azure apresenta métricas de desempenho chave que podem ser usadas para monitorizar e resolver problemas na sua consulta e desempenho no trabalho. Para ver estas métricas, navegue no trabalho stream Analytics que está interessado em ver métricas e ver a secção **de Monitorização** na página 'Vista Geral'.  

![Link de monitorização de trabalho do Stream Analytics](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

A janela aparecerá como mostrado:

![Painel de monitorização de trabalho stream Analytics](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Métricas disponíveis para Stream Analytics
| Metric                 | Definição                               |
| ---------------------- | ---------------------------------------- |
| Eventos de entrada recuados       | Número de eventos de entrada que são retrospados. Um valor não-zero para esta métrica implica que o seu trabalho não é capaz de acompanhar o número de eventos que chegam. Se este valor estiver a aumentar lentamente ou consistentemente não zero, deverá escalonar o seu trabalho. Pode aprender mais visitando [Compreender e ajustar Unidades de Streaming.](stream-analytics-streaming-unit-consumption.md) |
| Erros de Conversão de Dados | Número de eventos de saída que não puderam ser convertidos para o esquema de saída esperado. A política de erros pode ser alterada para 'Drop' para deixar cair eventos que encontrem este cenário. |
| Eventos de entrada antecipada       | Eventos cuja hora de inscrição é mais cedo do que a hora de chegada em mais de 5 minutos. |
| Pedidos de função falhadas | Número de chamadas falhadas da função de aprendizagem automática Azure (se presente). |
| Eventos de Função        | Número de eventos enviados para a função Azure Machine Learning (se presente). |
| Pedidos de Função      | Número de chamadas para a função Azure Machine Learning (se presente). |
| Erros de deserialização de entrada       | Número de eventos de entrada que não podiam ser desseerializados.  |
| Insutos de Eventos      | Quantidade de dados recebidos pelo trabalho stream Analytics, em bytes. Isto pode ser usado para validar que os eventos estão a ser enviados para a fonte de entrada. |
| Eventos de Entrada           | Número de registos deserializados dos eventos de entrada. Esta contagem não inclui eventos de entrada que resultem em erros de dessaseialização. Os mesmos eventos podem ser ingeridos pelo Stream Analytics várias vezes em cenários como recuperações internas e uniões auto-juntas. Por isso, recomenda-se que não espere que as métricas de Eventos de Entrada e Resultados de Saída correspondam se o seu trabalho tiver uma simples consulta de 'passagem'. |
| Fontes de entrada recebidas       | Número de mensagens recebidas pelo trabalho. Para o Event Hub, uma mensagem é um único EventData. Para Blob, uma mensagem é uma única bolha. Por favor, note que as fontes de entrada são contadas antes da desseerialização. Se houver erros de deserialização, as fontes de entrada podem ser maiores do que os eventos de entrada. Caso contrário, pode ser inferior ou igual a eventos de entrada, uma vez que cada mensagem pode conter vários eventos. |
| Eventos de entrada tardia      | Eventos que chegaram mais tarde do que a janela de tolerância à chegada tardia configurada. Saiba mais sobre as considerações da [ordem do evento Azure Stream Analytics](./stream-analytics-time-handling.md) . |
| Eventos fora de ordem    | Número de eventos recebidos fora de ordem que foram retirados ou receberam um timetamp ajustado, com base na Política de Ordenamento de Eventos. Isto pode ser impactado pela configuração da definição da janela de tolerância fora da ordem. |
| Eventos de saída          | Quantidade de dados enviados pelo trabalho stream Analytics para o alvo de saída, em número de eventos. |
| Erros de tempo de execução         | Número total de erros relacionados com o processamento de consultas (excluindo os erros encontrados ao ingerir eventos ou resultados de saída) |
| SU % Utilização       | Se a utilização dos recursos for consistentemente superior a 80%, o atraso da marca de água está a aumentar e o número de eventos recuados está a aumentar, considere o aumento das unidades de streaming. A elevada utilização indica que o trabalho está a utilizar perto dos recursos máximos atribuídos. |
| Atraso da marca de água       | O máximo de atraso da marca de água em todas as divisórias de todas as saídas no trabalho. |

Pode utilizar estas métricas para [monitorizar o desempenho do seu trabalho stream Analytics](./stream-analytics-set-up-alerts.md#scenarios-to-monitor). 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Personalização monitorização no portal Azure
Pode ajustar o tipo de gráfico, as métricas apresentadas e o intervalo de tempo nas definições do Gráfico de Edição. Para mais detalhes, consulte [como personalizar a monitorização.](../azure-monitor/platform/data-platform.md)

  ![Gráfico de tempo de monitorização de consulta de stream Analytics](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Última saída
Outro ponto de dados interessante para monitorizar o seu trabalho é a hora da última saída, mostrada na página 'Visão Geral'.
Desta vez é o tempo de aplicação (ou seja, o tempo que utiliza o relógio a partir dos dados do evento) da última saída do seu trabalho.

## <a name="get-help"></a>Obter ajuda
Para mais assistência, experimente o nosso [Microsoft Q&Uma página de perguntas para Azure Stream Analytics](/answers/topics/azure-stream-analytics.html)

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](/rest/api/streamanalytics/)
