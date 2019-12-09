---
title: Modelo de dados para telemetria de métrica-Aplicativo Azure insights
description: Modelo de dados Application Insights para telemetria de métrica
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: f697d7543a8551993640ef8c6d6c98fee35c0d68
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74922229"
---
# <a name="metric-telemetry-application-insights-data-model"></a>Telemetria de métrica: modelo de dados de Application Insights

Há dois tipos de telemetria de métricas com suporte pelo [Application insights](../../azure-monitor/app/app-insights-overview.md): medição única e métrica de agregação. A medida única é apenas um nome e valor. A métrica previamente agregada especifica o valor mínimo e máximo da métrica no intervalo de agregação e o desvio padrão dela.

A telemetria de métricas previamente agregadas pressupõe que o período de agregação foi de um minuto.

Há vários nomes de métrica conhecidos com suporte pelo Application Insights. Essas métricas são colocadas na tabela performanceCounters.

Métrica que representa os contadores do sistema e do processo:

| **Nome do .NET**             | **Nome independente da plataforma** | **Nome da API REST** | **Descrição**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Trabalho em andamento... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | CPU total do computador
| `\Memory\Available Bytes`                 | Trabalho em andamento... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | Mostra a quantidade de memória física, em bytes, disponível para processos em execução no computador. Ele é calculado somando-se a quantidade de espaço nas listas de memória zero, livre e em espera. A memória livre está pronta para uso; a memória zero consiste em páginas de memória preenchidas com zeros para evitar que processos posteriores vejam os dados usados por um processo anterior; a memória em espera é a memória que foi removida do conjunto de trabalho de um processo (sua memória física) para a rota para o disco, mas ainda está disponível para ser rechamada. Consulte o [objeto de memória](https://msdn.microsoft.com/library/ms804008.aspx)
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Trabalho em andamento... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | CPU do processo que hospeda o aplicativo
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Trabalho em andamento... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | memória usada pelo processo que hospeda o aplicativo
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Trabalho em andamento... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | taxa de operações de e/s executadas pelo processo que hospeda o aplicativo
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Trabalho em andamento... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | taxa de solicitações processadas pelo aplicativo 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Trabalho em andamento... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | taxa de exceções geradas pelo aplicativo
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Trabalho em andamento... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | tempo médio de execução de solicitações
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Trabalho em andamento... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | número de solicitações aguardando o processamento em uma fila

## <a name="name"></a>Nome

Nome da métrica que você gostaria de ver no Portal Application Insights e na interface do usuário. 

## <a name="value"></a>Valor

Valor único para medição. Soma de medidas individuais para a agregação.

## <a name="count"></a>Contagem

Peso da métrica da métrica agregada. Não deve ser definido para uma medida.

## <a name="min"></a>Mín.

Valor mínimo da métrica agregada. Não deve ser definido para uma medida.

## <a name="max"></a>Máx

Valor máximo da métrica agregada. Não deve ser definido para uma medida.

## <a name="standard-deviation"></a>Desvio padrão

Desvio padrão da métrica agregada. Não deve ser definido para uma medida.

## <a name="custom-properties"></a>Propriedades personalizadas

A métrica com a propriedade personalizada `CustomPerfCounter` definida como `true` indicar que a métrica representa o contador de desempenho do Windows. Essas métricas são colocadas na tabela performanceCounters. Não está em customMetrics. Além disso, o nome dessa métrica é analisado para extrair os nomes de categoria, contador e instância.

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Passos seguintes

- Saiba como usar a [API Application insights para métricas e eventos personalizados](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric).
- Consulte [modelo de dados](data-model.md) para tipos de Application insights e modelo de dados.
- Confira as [plataformas](../../azure-monitor/app/platforms.md) com suporte pelo Application insights.
