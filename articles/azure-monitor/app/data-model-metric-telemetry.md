---
title: Modelo de dados de telemetria de informações de aplicação do Azure - métrica de telemetria | Documentos da Microsoft
description: Modelo de dados do Application Insights para a telemetria de métrica
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 0973c86d055ff4ebbe7e5a3c4a2ca4e3dcabc6a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60900465"
---
# <a name="metric-telemetry-application-insights-data-model"></a>Telemetria de métrica: Modelo de dados do Application Insights

Existem dois tipos de telemetria de métrica suportadas pelo [Application Insights](../../azure-monitor/app/app-insights-overview.md): único medição e métrica pré-agregados. Medida única é apenas um nome e valor. Métrica pré-agregados Especifica o valor mínimo e máximo da métrica no intervalo de agregação e desvio padrão do mesmo.

Telemetria de métrica pré-agregados pressupõe esse período de agregação foi um minuto.

Existem vários nomes de métrica conhecidos suportados pelo Application Insights. Estas métricas são colocados na tabela de performanceCounters.

Métrica que representa os contadores de sistema e de processo:

| **Nome do .NET**             | **Nome de independente de plataforma** | **Nome da REST API** | **Descrição**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Trabalho em curso... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | CPU de máquinas totais
| `\Memory\Available Bytes`                 | Trabalho em curso... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | Mostra a quantidade de memória física, em bytes, disponíveis para processos em execução no computador. Ele é calculado ao somar a quantidade de espaço nas listas do zerado, gratuito e de reserva de memória. Memória livre está pronta para uso; memória zerada consiste em páginas de memória preenchido com zeros para impedir que os processos posteriores visualização de dados utilizados por um processo anterior; memória em modo de espera é a memória que foi removida do conjunto de trabalho de um processo (sua memória física) caminho para o disco, mas ainda está disponível para que possa ser recuperada. Consulte [de objetos de memória](https://msdn.microsoft.com/library/ms804008.aspx)
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Trabalho em curso... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | CPU do processo que aloja a aplicação
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Trabalho em curso... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | memória utilizada pelo processo que aloja a aplicação
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Trabalho em curso... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | taxa de operações de e/s de execuções do processo que hospeda o aplicativo
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Trabalho em curso... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | taxa de pedidos processados pela aplicação 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Trabalho em curso... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | taxa de exceções emitidas pela aplicação
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Trabalho em curso... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | tempo de execução de pedidos médios
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Trabalho em curso... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | número de pedidos a aguardar o processamento numa fila

## <a name="name"></a>Name

Nome da métrica que gostariam de ver no portal do Application Insights e da interface do Usuário. 

## <a name="value"></a>Value

Valor único para a medida. Soma de medidas individuais para a agregação.

## <a name="count"></a>Count

Métrica ponderação da métrica agregada. Não deve ser definido para uma medida.

## <a name="min"></a>Mín.

Valor mínimo da métrica agregado. Não deve ser definido para uma medida.

## <a name="max"></a>Máx.

Valor máximo da métrica agregado. Não deve ser definido para uma medida.

## <a name="standard-deviation"></a>Desvio padrão

Desvio padrão da métrica agregado. Não deve ser definido para uma medida.

## <a name="custom-properties"></a>Propriedades personalizadas

Métrica com a propriedade personalizada `CustomPerfCounter` definido como `true` indicam que a métrica representa o contador de desempenho do windows. Estas métricas são colocados na tabela de performanceCounters. Não está no customMetrics. Também o nome desta métrica é analisado para extrair a categoria, contador e nomes de instância.

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Passos Seguintes

- Aprenda a usar [API do Application Insights para métricas e eventos personalizados](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric).
- Ver [modelo de dados](data-model.md) para o modelo de tipos e dados do Application Insights.
- Confira [plataformas](../../azure-monitor/app/platforms.md) suportada pelo Application Insights.
