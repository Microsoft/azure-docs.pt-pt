---
title: Modelo de dados para telemetria métrica - Insights de aplicação Azure
description: Modelo de dados de Insights de Aplicação para telemetria métrica
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 3e4a1fc3de58b8e65ab9c7a288bdf3eb37e7bae0
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671975"
---
# <a name="metric-telemetry-application-insights-data-model"></a>Telemetria métrica: Modelo de dados de Insights de Aplicação

Existem dois tipos de telemetria métrica suportados por Insights de [Aplicação:](../../azure-monitor/app/app-insights-overview.md)medição única e métrica pré-agregada. A medição única é apenas um nome e valor. A métrica pré-agregada especifica o valor mínimo e máximo da métrica no intervalo de agregação e desvio padrão da mesma.

A telemetria métrica pré-agregada pressupõe que o período de agregação foi de um minuto.

Existem vários nomes métricos bem conhecidos apoiados por Application Insights. Estas métricas colocadas na tabela performanceCounters.

Sistema de representação métrica e contadores de processos:

| **nome .NET**             | **Nome agnóstico da plataforma** | **Nome da API rest** | **Descrição**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Trabalho em curso... | [processadorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | CPU máquina total
| `\Memory\Available Bytes`                 | Trabalho em curso... | [memóriaAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | Mostra a quantidade de memória física, em bytes, disponível para processos em execução no computador. É calculado resumindo a quantidade de espaço nas listas de memória zeroed, grátis e de espera. A memória gratuita está pronta para ser utilizada; a memória zero consiste em páginas de memória cheias de zeros para impedir que os processos posteriores vejam os dados utilizados por um processo anterior; a memória de espera é a memória que foi removida do conjunto de trabalho de um processo (a sua memória física) a caminho do disco, mas ainda está disponível para ser recordada. Ver [Objeto de Memória](https://msdn.microsoft.com/library/ms804008.aspx)
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Trabalho em curso... | [processoCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | CPU do processo que acolhe a aplicação
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Trabalho em curso... | [processoPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | memória usada pelo processo de hospedagem da aplicação
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Trabalho em curso... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | taxa de operações de I/O executa por processo de hospedagem da aplicação
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Trabalho em curso... | [pedidosPerSegundo](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | taxa de pedidos processados por aplicação 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Trabalho em curso... | [excepçõesPerSegundo](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | taxa de exceções lançadas por aplicação
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Trabalho em curso... | [solicitarExecuçãoTempo](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | tempo médio de execução de pedidos
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Trabalho em curso... | [solicitaInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | número de pedidos à espera do processamento em uma fila

## <a name="name"></a>Nome

Nome da métrica que gostaria de ver no portal Application Insights e UI. 

## <a name="value"></a>Valor

Valor único para a medição. Soma das medições individuais para a agregação.

## <a name="count"></a>Contagem

Peso métrico da métrica agregada. Não deve ser definido para uma medição.

## <a name="min"></a>Mín.

Valor mínimo da métrica agregada. Não deve ser definido para uma medição.

## <a name="max"></a>Máx.

Valor máximo da métrica agregada. Não deve ser definido para uma medição.

## <a name="standard-deviation"></a>Desvio padrão

Desvio padrão da métrica agregada. Não deve ser definido para uma medição.

## <a name="custom-properties"></a>Propriedades personalizadas

Métrica com a propriedade personalizada `CustomPerfCounter` definida para `true` indicar que a métrica representa o contador de desempenho das janelas. Estas métricas colocadas na tabela performanceCounters. Não em Métricas personalizadas. Também o nome desta métrica é analisado para extrair nomes de categoria, contador e instâncias.

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Passos seguintes

- Saiba como usar a API de Insights de [Aplicação para eventos e métricas personalizadas.](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric)
- Consulte o modelo de [dados](data-model.md) para tipos de Insights de Aplicação e modelo de dados.
- Confira [as plataformas](../../azure-monitor/app/platforms.md) suportadas por Application Insights.
