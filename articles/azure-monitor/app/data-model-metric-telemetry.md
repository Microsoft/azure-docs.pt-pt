---
title: Modelo de dados para telemetria métrica - Azure Application Insights
description: Modelo de dados de Insights de Aplicação para telemetria métrica
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: f8e787b8a94987e7d68e12a0e4e69f4c63e21e19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87320583"
---
# <a name="metric-telemetry-application-insights-data-model"></a>Telemetria métrica: Modelo de dados de Insights de Aplicação

Existem dois tipos de telemetria métrica suportados pela [Application Insights](./app-insights-overview.md): medição única e métrica pré-agregada. Uma única medição é apenas um nome e um valor. A métrica pré-agregada especifica o valor mínimo e máximo da métrica no intervalo de agregação e desvio padrão da métrica.

A telemetria métrica pré-agregada pressupõe que o período de agregação foi de um minuto.

Existem vários nomes métricos bem conhecidos suportados pela Application Insights. Estas métricas colocadas na tabela performanceCounters.

Sistema de representação métrica e contadores de processos:

| **nome .NET**             | **Nome agnóstico da plataforma** | **NOME API DE DESCANSO** | **Descrição**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Trabalho em progresso... | [processadorEsPercente](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | CPU máquina total
| `\Memory\Available Bytes`                 | Trabalho em progresso... | [memóriaAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | Mostra a quantidade de memória física, em bytes, disponível para processos em execução no computador. É calculado resumindo a quantidade de espaço nas listas de memórias zero, livres e em espera. A memória gratuita está pronta a ser utilizada; A memória zero consiste em páginas de memória cheias de zeros para evitar que processos posteriores vejam dados utilizados por um processo anterior; memória de espera é memória que foi removida do conjunto de trabalho de um processo (sua memória física) a caminho do disco, mas ainda está disponível para ser recolhida. Ver [Objeto de Memória](/previous-versions/ms804008(v=msdn.10))
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Trabalho em progresso... | [processoCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | CPU do processo de acolhimento da aplicação
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Trabalho em progresso... | [processoPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | memória usada pelo processo de hospedagem da aplicação
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Trabalho em progresso... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | taxa de operações de E/S executa por processo de hospedagem da aplicação
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Trabalho em progresso... | [pedidosPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | taxa de pedidos processados por aplicação 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Trabalho em progresso... | [excepçõesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | taxa de exceções lançadas por aplicação
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Trabalho em progresso... | [solicitaçãoExecuçãoTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | tempo de execução de pedidos médios
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Trabalho em progresso... | [pedidosInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | número de pedidos à espera do processamento em uma fila

## <a name="name"></a>Name

Nome da métrica que gostaria de ver no portal Application Insights e UI. 

## <a name="value"></a>Valor

Valor único para medição. Soma de medições individuais para a agregação.

## <a name="count"></a>de palavras

Peso métrico da métrica agregada. Não deve ser definido para uma medição.

## <a name="min"></a>Mín

Valor mínimo da métrica agregada. Não deve ser definido para uma medição.

## <a name="max"></a>Máx

Valor máximo da métrica agregada. Não deve ser definido para uma medição.

## <a name="standard-deviation"></a>Desvio padrão

Desvio padrão da métrica agregada. Não deve ser definido para uma medição.

## <a name="custom-properties"></a>Propriedades personalizadas

Métrica com a propriedade personalizada `CustomPerfCounter` definida para indicar que a `true` métrica representa o contador de desempenho do windows. Estas métricas colocadas na tabela performanceCounters. Não em costumesMétricos. Também o nome desta métrica é analisado para extrair nomes de categoria, contador e instância.

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Passos seguintes

- Saiba como utilizar a [App Insights API para eventos e métricas personalizados.](./api-custom-events-metrics.md#trackmetric)
- Consulte [o modelo de dados](data-model.md) para os tipos de Insights de Aplicação e modelo de dados.
- Confira as [plataformas](./platforms.md) suportadas pela Application Insights.

