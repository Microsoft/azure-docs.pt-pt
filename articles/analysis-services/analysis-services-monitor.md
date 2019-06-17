---
title: Monitorizar as métricas de servidor Azure Analysis Services | Documentos da Microsoft
description: Saiba como monitorizar as métricas do servidor de Analysis Services no portal do Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: cdffa8e138062a91bd1876ac6e44728c47d9cdd7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61065068"
---
# <a name="monitor-server-metrics"></a>Monitorizar as métricas do servidor

O Analysis Services fornece métricas para o ajudar a monitorizar o desempenho e estado de funcionamento dos seus servidores. Por exemplo, pode monitorize a utilização da CPU e memória, o número de ligações de cliente e o consumo de recursos de consulta. Analysis Services utiliza a mesma arquitetura de monitorização, como a maioria dos outros serviços do Azure. Para obter mais informações, consulte [métricas no Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

Para efetuar mais diagnósticos aprofundados, acompanhar o desempenho e identifique as tendências em vários recursos de serviço num grupo de recursos ou subscrição, utilize [do Azure Monitor](https://azure.microsoft.com/services/monitor/). O Azure Monitor (serviço) pode resultar num serviço sujeito a faturação.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Para monitorizar as métricas para um servidor do Analysis Services

1. No portal do Azure, selecione **métricas**.

    ![Monitorizar no portal do Azure](./media/analysis-services-monitor/aas-monitor-portal.png)

2. Na **métricas disponíveis**, selecione as métricas para incluir no gráfico. 

    ![Gráfico de monitor](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Métricas do servidor

Utilize esta tabela para determinar as métricas que são recomendadas para o seu cenário de monitorização. Apenas as métricas da mesma unidade podem ser mostradas no mesmo gráfico.

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Comprimento de fila da tarefa de conjunto de comandos|Count|Média|Número de tarefas na fila do pool de threads de comando.|
|CurrentConnections|Ligação: Ligações atuais|Count|Média|Número atual de ligações de cliente estabelecidas.|
|CurrentUserSessions|Sessões de utilizador atual|Count|Média|Número atual de sessões de utilizador estabelecidas.|
|mashup_engine_memory_metric|Memória do motor M|Bytes|Média|Utilização da memória através de processos de motor de mashup|
|mashup_engine_qpu_metric|M Engine QPU|Contagem|Média|Utilização QPU através de processos de motor de mashup|
|memory_metric|Memória|Bytes|Média|Memória. Intervalo de 0-25 GB para S1, 0-50 GB para S2 e 0-100 GB para S4|
|memory_thrashing_metric|Degradação de memória|Percentagem|Média|Degradação de memória média.|
|CleanerCurrentPrice|Memória: Preço atual de limpeza|Count|Média|Preço atual de memória, $/byte/tempo, normalizado para 1000.|
|CleanerMemoryNonshrinkable|Memória: Mais limpo nonshrinkable de memória|Bytes|Média|Quantidade de memória, em bytes, não são sujeitos à remoção limpeza de segundo plano.|
|CleanerMemoryShrinkable|Memória: Memória de limpeza Reduzível|Bytes|Média|Quantidade de memória, em bytes, sujeitos à remoção limpeza de segundo plano.|
|MemoryLimitHard|Memória: Limite de memória|Bytes|Média|Limite restrito da memória, do ficheiro de configuração.|
|MemoryLimitHigh|Memória: Máximo de limite de memória|Bytes|Média|Limite máximo de memória do ficheiro de configuração.|
|MemoryLimitLow|Memória: Mínimo de limite de memória|Bytes|Média|Limite mínimo de memória do ficheiro de configuração.|
|MemoryLimitVertiPaq|Memória: Limite de memória VertiPaq|Bytes|Média|Limite de memória do ficheiro de configuração.|
|MemoryUsage|Memória: Utilização da memória|Bytes|Média|Utilização da memória do processo de servidor utilizado para calcular o preço de memória mais limpo. Igual ao contador Process\PrivateBytes mais o tamanho dos dados de mapeamento de memória, ignorando qualquer memória, que foi mapeada ou alocada pelo motor de análise dentro da memória (VertiPaq) em excesso relativamente ao mecanismo de limite de memória.|
|Quota|Memória: Quota|Bytes|Média|Quota de memória atual, em bytes. Quota de memória também é conhecido como uma reserva de memória ou de concessão de memória.|
|QuotaBlocked|Memória: Quotas bloqueadas|Count|Média|Número atual de pedidos de quota que estão bloqueados até outras quotas de memória serem libertas.|
|VertiPaqNonpaged|Memória: VertiPaq Nonpaged|Bytes|Média|Bytes de memória bloqueada no conjunto de trabalho para utilização pelo mecanismo de dentro da memória.|
|VertiPaqPaged|Memória: VertiPaq paginado|Bytes|Média|Bytes de memória paga em utilização para dados na memória.|
|ProcessingPoolJobQueueLength|Comprimento de fila da tarefa de conjunto de processamento|Count|Média|Número de tarefas não-I/O na fila do pool de threads de processamento.|
|RowsConvertedPerSec|Processamento: Linhas convertidas por segundo|CountPerSecond|Média|Taxa de linhas convertidas durante o processamento.|
|RowsReadPerSec|Processamento: Linhas lidas por segundo|CountPerSecond|Média|Taxa de linhas lidas de todas as bases de dados relacionais.|
|RowsWrittenPerSec|Processamento: Linhas escritas por segundo|CountPerSecond|Média|Taxa de linhas escritas durante o processamento.|
|qpu_metric|QPU|Contagem|Média|QPU. Intervalo de 0-100 para S1, 0-200 para S2 e 0-400 para S4|
|QueryPoolBusyThreads|Threads ocupadas do conjunto de consulta|Count|Média|Número de threads ocupados no conjunto de threads de consulta.|
|SuccessfullConnectionsPerSec|Ligações com êxito por segundo|CountPerSecond|Média|Taxa de conclusões de ligação com êxito.|
|CommandPoolBusyThreads|Threads: Threads ocupadas do conjunto de comandos|Contagem|Média|Número de threads ocupados no conjunto de threads de comando.|
|CommandPoolIdleThreads|Threads: Threads inativas do conjunto de comandos|Contagem|Média|Número de threads inativas no conjunto de threads de comando.|
|LongParsingBusyThreads|Threads: Threads ocupadas de análise longa|Contagem|Média|Número de threads ocupados no conjunto de threads de análise longa.|
|LongParsingIdleThreads|Threads: Threads Inativas de análise longa|Count|Média|Número de threads inativas no conjunto de threads de análise longa.|
|LongParsingJobQueueLength|Threads: Comprimento da fila de tarefas análise longa|Count|Média|Número de tarefas na fila do pool de threads de análise longa.|
|ProcessingPoolIOJobQueueLength|Threads: Comprimento de fila de trabalho de e/s do conjunto de processamento|Contagem|Média|Número de tarefas de e/s na fila do pool de threads de processamento.|
|ProcessingPoolBusyIOJobThreads|Threads: Threads de trabalho de e/s ocupados do conjunto de processamento|Count|Média|Número de threads a executar tarefas de e/s no conjunto de threads de processamento.|
|ProcessingPoolBusyNonIOThreads|Threads: Pool de threads não-I/O ocupado a processar|Count|Média|Número de threads a executar tarefas de não-I/O no conjunto de threads de processamento.|
|ProcessingPoolIdleIOJobThreads|Threads: Threads de trabalho de e/s inativas do conjunto de processamento|Contagem|Média|Número de threads inativas para tarefas de e/s no pool de threads de processamento.|
|ProcessingPoolIdleNonIOThreads|Threads: Os threads não-I/O inativas do conjunto de processamento|Contagem|Média|Número de threads inativas no conjunto de threads de processamento dedicadas a tarefas não-I/O.|
|QueryPoolIdleThreads|Threads: Consultar threads inativas do conjunto|Count|Média|Número de threads inativas para tarefas de e/s no pool de threads de processamento.|
|QueryPoolJobQueueLength|Threads: Comprimento de fila da tarefa de conjunto de consulta|Count|Média|Número de tarefas na fila do pool de threads de consulta.|
|ShortParsingBusyThreads|Threads: Threads ocupadas de análise curtas|Count|Média|Número de threads ocupados no conjunto de threads de análise curta.|
|ShortParsingIdleThreads|Threads: Threads Inativas de análise curtas|Count|Média|Número de threads inativas no conjunto de threads de análise curta.|
|ShortParsingJobQueueLength|Threads: Comprimento da fila de tarefa análise curta|Contagem|Média|Número de tarefas na fila do pool de threads de análise curta.|
|TotalConnectionFailures|Falhas de ligação total|Count|Média|Total de tentativas de ligação.|
|TotalConnectionRequests|Pedidos de ligação total|Count|Média|Pedidos de ligação total. |

## <a name="next-steps"></a>Passos Seguintes
[Monitorização no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md)   
[Métricas no Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)   
[Métricas no Azure Monitor REST API](/rest/api/monitor/metrics)
