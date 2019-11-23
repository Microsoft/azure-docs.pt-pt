---
title: Monitorar métricas do Azure Analysis Services Server | Microsoft Docs
description: Saiba como Analysis Services usar o Metrics Explorer do Azure, uma ferramenta gratuita no portal, para ajudá-lo a monitorar o desempenho e a integridade de seus servidores.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9e6eab38e0f7bd55fa671aa8c1e99693eeb54c84
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572737"
---
# <a name="monitor-server-metrics"></a>Monitorizar as métricas do servidor

Analysis Services fornece métricas no Azure Metrics Explorer, uma ferramenta gratuita no portal, para ajudá-lo a monitorar o desempenho e a integridade de seus servidores. Por exemplo, monitore a memória e o uso da CPU, o número de conexões de cliente e o consumo de recursos de consulta. Analysis Services usa a mesma estrutura de monitoramento que a maioria dos outros serviços do Azure. Para saber mais, confira [introdução ao Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md).

Para executar um diagnóstico mais aprofundado, controlar o desempenho e identificar tendências em vários recursos de serviço em um grupo de recursos ou assinatura, use [Azure monitor](../azure-monitor/overview.md). Azure Monitor (serviço) pode resultar em um serviço faturável.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Para monitorar as métricas de um servidor de Analysis Services

1. Em portal do Azure, selecione **métricas**.

    ![Monitorizar no portal do Azure](./media/analysis-services-monitor/aas-monitor-portal.png)

2. Em **métrica**, selecione as métricas a serem incluídas no gráfico. 

    ![Gráfico de monitor](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Métricas do servidor

Use esta tabela para determinar quais métricas são melhores para seu cenário de monitoramento. Somente as métricas da mesma unidade podem ser mostradas no mesmo gráfico.

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Comprimento da fila de trabalhos do pool de comandos|Contagem|Média|Número de trabalhos na fila do pool de threads de comando.|
|CurrentConnections|Conexão: conexões atuais|Contagem|Média|Número atual de conexões de cliente estabelecidas.|
|CurrentUserSessions|Sessões de usuário atuais|Contagem|Média|Número atual de sessões de usuário estabelecidas.|
|mashup_engine_memory_metric|Memória do mecanismo M|Bytes|Média|Uso de memória pelos processos do mecanismo mashup|
|mashup_engine_qpu_metric|QPU do mecanismo M|Contagem|Média|Uso de QPU por processos do mecanismo mashup|
|memory_metric|Memória|Bytes|Média|Memória. Intervalo de 0-25 GB para S1, 0-50 GB para S2 e 0-100 GB para S4|
|memory_thrashing_metric|Ultrapaginação de memória|Percentagem|Média|Média de ultrapaginação de memória.|
|CleanerCurrentPrice|Memória: preço atual do limpador|Contagem|Média|Preço atual da memória, $/byte/time, normalizado para 1000.|
|CleanerMemoryNonshrinkable|Memória: memória de limpeza não reduzida|Bytes|Média|Quantidade de memória, em bytes, não sujeita a limpeza pelo limpador de segundo plano.|
|CleanerMemoryShrinkable|Memória: memória de limpeza recolhida|Bytes|Média|Quantidade de memória, em bytes, sujeita a limpeza pelo limpador de segundo plano.|
|MemoryLimitHard|Memória: limite de memória rígido|Bytes|Média|Limite de memória rígido, do arquivo de configuração.|
|MemoryLimitHigh|Memória: limite de memória alto|Bytes|Média|Limite de memória alto, do arquivo de configuração.|
|MemoryLimitLow|Memória: limite de memória baixo|Bytes|Média|Limite de memória baixo, do arquivo de configuração.|
|MemoryLimitVertiPaq|Memória: limite de memória VertiPaq|Bytes|Média|Limite na memória, do arquivo de configuração.|
|MemoryUsage|Memória: uso de memória|Bytes|Média|Uso de memória do processo do servidor conforme usado no cálculo do preço de memória de limpeza. Igual ao contador Process\PrivateBytes mais o tamanho dos dados mapeados na memória, ignorando qualquer memória, que foi mapeada ou alocada pelo mecanismo analítico na memória (VertiPaq) que excede o limite de memória do mecanismo.|
|private_bytes_metric|Bytes particulares |Bytes|Média|A quantidade total de memória que o processo do mecanismo de Analysis Services e os processos de contêiner do mashup alocaram, não incluindo a memória compartilhada com outros processos.|
|virtual_bytes_metric|Bytes virtuais |Bytes|Média|O tamanho atual do espaço de endereço virtual que Analysis Services processo do mecanismo e os processos de contêiner do mashup estão usando.|
|mashup_engine_private_bytes_metric|Bytes privados do mecanismo M |Bytes|Média|A quantidade total de processos de contêiner de mashup de memória alocados, não incluindo a memória compartilhada com outros processos.|
|mashup_engine_virtual_bytes_metric|Bytes virtuais do mecanismo M |Bytes|Média|O tamanho atual dos processos de contêiner do mashup de espaço de endereço virtual está usando.|
|Quota|Memória: cota|Bytes|Média|Cota de memória atual, em bytes. A cota de memória também é conhecida como concessão de memória ou reserva de memória.|
|QuotaBlocked|Memória: cota bloqueada|Contagem|Média|Número atual de solicitações de cota que são bloqueadas até que outras cotas de memória sejam liberadas.|
|VertiPaqNonpaged|Memória: VertiPaq não paginável|Bytes|Média|Bytes de memória bloqueados no conjunto de trabalho para uso pelo mecanismo na memória.|
|VertiPaqPaged|Memória: VertiPaq paginável|Bytes|Média|Bytes de memória paginável em uso para dados na memória.|
|ProcessingPoolJobQueueLength|Tamanho da fila de trabalhos do pool de processamento|Contagem|Média|Número de trabalhos que não são de e/s na fila do pool de threads de processamento.|
|RowsConvertedPerSec|Processamento: linhas convertidas por segundo|CountPerSecond|Média|Taxa de linhas convertidas durante o processamento.|
|RowsReadPerSec|Processamento: linhas lidas por segundo|CountPerSecond|Média|Taxa de linhas lidas de todos os bancos de dados relacionais.|
|RowsWrittenPerSec|Processamento: linhas gravadas por segundo|CountPerSecond|Média|Taxa de linhas gravadas durante o processamento.|
|qpu_metric|QPU|Contagem|Média|QPU. Intervalo de 0-100 para S1, 0-200 para S2 e 0-400 para S4|
|QueryPoolBusyThreads|Threads ocupados do pool de consulta|Contagem|Média|Número de threads ocupados no pool de threads de consulta.|
|SuccessfullConnectionsPerSec|Conexões com êxito por segundo|CountPerSecond|Média|Taxa de conclusões de conexão com êxito.|
|CommandPoolBusyThreads|Threads: threads ocupados do pool de comandos|Contagem|Média|Número de threads ocupados no pool de threads de comando.|
|CommandPoolIdleThreads|Threads: threads ociosos do pool de comandos|Contagem|Média|Número de threads ociosos no pool de threads de comando.|
|LongParsingBusyThreads|Threads: threads ocupados da análise longa|Contagem|Média|Número de threads ocupados no pool de threads da análise longa.|
|LongParsingIdleThreads|Threads: threads ociosos da análise longa|Contagem|Média|Número de threads ociosos no pool de threads da análise longa.|
|LongParsingJobQueueLength|Threads: comprimento da fila de trabalhos de análise longa|Contagem|Média|Número de trabalhos na fila do pool de threads de análise longa.|
|ProcessingPoolIOJobQueueLength|Threads: tamanho da fila de trabalhos de e/s do pool de processamento|Contagem|Média|Número de trabalhos de e/s na fila do pool de threads de processamento.|
|ProcessingPoolBusyIOJobThreads|Threads: processando threads de trabalho de e/s de pool ocupado|Contagem|Média|Número de threads executando trabalhos de e/s no pool de threads de processamento.|
|ProcessingPoolBusyNonIOThreads|Threads: threads de não-e/s ocupados do pool de processamento|Contagem|Média|Número de threads que executam trabalhos que não são de e/s no pool de threads de processamento.|
|ProcessingPoolIdleIOJobThreads|Threads: processando threads de trabalho de e/s de pool ocioso|Contagem|Média|Número de threads ociosos para trabalhos de e/s no pool de threads de processamento.|
|ProcessingPoolIdleNonIOThreads|Threads: threads de não-e/s ociosos do pool de processamento|Contagem|Média|Número de threads ociosos no pool de threads de processamento dedicados a trabalhos que não são de e/s.|
|QueryPoolIdleThreads|Threads: threads ociosos do pool de consulta|Contagem|Média|Número de threads ociosos para trabalhos de e/s no pool de threads de processamento.|
|QueryPoolJobQueueLength|Threads: comprimento da fila de trabalhos do pool de consultas|Contagem|Média|Número de trabalhos na fila do pool de threads de consulta.|
|ShortParsingBusyThreads|Threads: threads ocupados da análise resumida|Contagem|Média|Número de threads ocupados no pool de threads da análise resumida.|
|ShortParsingIdleThreads|Threads: threads ociosos da análise resumida|Contagem|Média|Número de threads ociosos no pool de threads da análise resumida.|
|ShortParsingJobQueueLength|Threads: comprimento da fila de trabalhos da análise curta|Contagem|Média|Número de trabalhos na fila do pool de threads da análise resumida.|
|TotalConnectionFailures|Total de falhas de conexão|Contagem|Média|Total de tentativas de conexão com falha.|
|TotalConnectionRequests|Total de solicitações de conexão|Contagem|Média|Total de solicitações de conexão. |

## <a name="next-steps"></a>Passos seguintes
[Visão geral de Azure Monitor](../azure-monitor/overview.md)      
[Introdução ao Metrics Explorer do Azure](../azure-monitor/platform/metrics-getting-started.md)      
[Métricas em Azure Monitor API REST](/rest/api/monitor/metrics)
