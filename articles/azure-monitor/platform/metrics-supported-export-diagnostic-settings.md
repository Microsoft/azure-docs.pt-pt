---
title: Métricas de plataforma Azure Monitor exportáveis por meio de configurações de diagnóstico
description: Lista de métricas disponíveis para cada tipo de recurso com Azure Monitor.
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
author: rboucher
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 5d38786d3c7b852d3a9b65cd366eed68ebbb01e3
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76152957"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Métricas de plataforma Azure Monitor exportáveis por meio de configurações de diagnóstico

O Azure Monitor fornece [métricas de plataforma](data-platform-metrics.md) por padrão sem configuração. Ele fornece várias maneiras de interagir com as métricas de plataforma, incluindo o gráfico delas no portal, acessá-las por meio da API REST ou consultá-las usando o PowerShell ou a CLI. Consulte [métricas – com suporte](metrics-supported.md) para obter uma lista completa de métricas de plataforma disponíveis atualmente com o pipeline de métrica consolidado do Azure monitor. Para consultar e acessar essas métricas, use a [versão de api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Outras métricas podem estar disponíveis no portal ou usando APIs herdadas.

Você pode exportar as métricas de plataforma do pipeline do Azure monitor para outros locais de uma das duas maneiras.
1. Usando [configurações de diagnóstico](diagnostic-settings.md) para enviar para log Analytics, hubs de eventos ou armazenamento do Azure.
2. Usar a [API REST de métricas](https://docs.microsoft.com/rest/api/monitor/metrics/list)

Devido a complexidades no back-end de Azure Monitor, nem todas as métricas são exportáveis usando configurações de diagnóstico. A tabela a seguir lista os que podem e não podem ser exportados usando as configurações de diagnóstico.

Exportável por meio de configurações de diagnóstico? | ResourceType | Métrica | MetricDisplayName | Unidade | AggregationType
|----|-----|------|----|----|-----|
Sim | Microsoft.AnalysisServices/servers | CleanerCurrentPrice | Memória: preço atual do limpador | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | CleanerMemoryNonshrinkable | Memória: memória de limpeza não reduzida | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | CleanerMemoryShrinkable | Memória: memória de limpeza recolhida | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | CommandPoolBusyThreads | Threads: threads ocupados do pool de comandos | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | CommandPoolIdleThreads | Threads: threads ociosos do pool de comandos | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | CommandPoolJobQueueLength | Comprimento da fila de trabalhos do pool de comandos | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | CurrentConnections | Conexão: conexões atuais | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | CurrentUserSessions | Sessões de usuário atuais | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | LongParsingBusyThreads | Threads: threads ocupados da análise longa | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | LongParsingIdleThreads | Threads: threads ociosos da análise longa | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | LongParsingJobQueueLength | Threads: comprimento da fila de trabalhos de análise longa | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | mashup_engine_memory_metric | Memória do mecanismo M | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | mashup_engine_private_bytes_metric | Bytes privados do mecanismo M | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | mashup_engine_qpu_metric | QPU do mecanismo M | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | mashup_engine_virtual_bytes_metric | Bytes virtuais do mecanismo M | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | memory_metric | Memória | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | memory_thrashing_metric | Degradação de Memória | Percentagem | Média
Sim | Microsoft.AnalysisServices/servers | MemoryLimitHard | Memória: limite de memória rígido | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | MemoryLimitHigh | Memória: limite de memória alto | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | MemoryLimitLow | Memória: limite de memória baixo | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | MemoryLimitVertiPaq | Memória: limite de memória VertiPaq | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | MemoryUsage | Memória: uso de memória | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | private_bytes_metric | Bytes Privados | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | ProcessingPoolBusyIOJobThreads | Threads: processando threads de trabalho de e/s de pool ocupado | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | ProcessingPoolBusyNonIOThreads | Threads: threads de não-e/s ocupados do pool de processamento | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | ProcessingPoolIdleIOJobThreads | Threads: processando threads de trabalho de e/s de pool ocioso | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | ProcessingPoolIdleNonIOThreads | Threads: threads de não-e/s ociosos do pool de processamento | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | ProcessingPoolIOJobQueueLength | Threads: tamanho da fila de trabalhos de e/s do pool de processamento | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | ProcessingPoolJobQueueLength | Tamanho da fila de trabalhos do pool de processamento | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | qpu_metric | QPU | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | QueryPoolBusyThreads | Threads ocupados do pool de consulta | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | QueryPoolIdleThreads | Threads: threads ociosos do pool de consulta | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | QueryPoolJobQueueLength | Threads: comprimento da fila de trabalhos do pool de consultas | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | Quota | Memória: cota | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | QuotaBlocked | Memória: cota bloqueada | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | RowsConvertedPerSec | Processamento: linhas convertidas por segundo | CountPerSecond | Média
Sim | Microsoft.AnalysisServices/servers | RowsReadPerSec | Processamento: linhas lidas por segundo | CountPerSecond | Média
Sim | Microsoft.AnalysisServices/servers | RowsWrittenPerSec | Processamento: linhas gravadas por segundo | CountPerSecond | Média
Sim | Microsoft.AnalysisServices/servers | ShortParsingBusyThreads | Threads: threads ocupados da análise resumida | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | ShortParsingIdleThreads | Threads: threads ociosos da análise resumida | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | ShortParsingJobQueueLength | Threads: comprimento da fila de trabalhos da análise curta | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | SuccessfullConnectionsPerSec | Conexões com êxito por segundo | CountPerSecond | Média
Sim | Microsoft.AnalysisServices/servers | TotalConnectionFailures | Total de falhas de conexão | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | TotalConnectionRequests | Total de solicitações de conexão | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | VertiPaqNonpaged | Memória: VertiPaq não paginável | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | VertiPaqPaged | Memória: VertiPaq paginável | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | virtual_bytes_metric | Bytes Virtuais | Bytes | Média
Sim | Microsoft.ApiManagement/service | BackendDuration | Duração de solicitações de back-end | Milissegundos | Média
Sim | Microsoft.ApiManagement/service | Capacidade | Capacidade | Percentagem | Média
Sim | Microsoft.ApiManagement/service | Duração | Duração geral das solicitações de gateway | Milissegundos | Média
Sim | Microsoft.ApiManagement/service | EventHubDroppedEvents | Eventos do EventHub ignorados | Contagem | Total
Sim | Microsoft.ApiManagement/service | EventHubRejectedEvents | Eventos de EventHub rejeitados | Contagem | Total
Sim | Microsoft.ApiManagement/service | EventHubSuccessfulEvents | Eventos de EventHub bem-sucedidos | Contagem | Total
Sim | Microsoft.ApiManagement/service | EventHubThrottledEvents | Eventos de EventHub limitados | Contagem | Total
Sim | Microsoft.ApiManagement/service | EventHubTimedoutEvents | Eventos do EventHub com tempo limite excedido | Contagem | Total
Sim | Microsoft.ApiManagement/service | EventHubTotalBytesSent | Tamanho dos eventos do EventHub | Bytes | Total
Sim | Microsoft.ApiManagement/service | EventHubTotalEvents | Total de eventos do EventHub | Contagem | Total
Sim | Microsoft.ApiManagement/service | EventHubTotalFailedEvents | Eventos de EventHub com falha | Contagem | Total
Sim | Microsoft.ApiManagement/service | FailedRequests | Solicitações de gateway com falha (preteridas) | Contagem | Total
Sim | Microsoft.ApiManagement/service | OtherRequests | Outras solicitações de gateway (preteridas) | Contagem | Total
Sim | Microsoft.ApiManagement/service | Pedidos | Pedidos | Contagem | Total
Sim | Microsoft.ApiManagement/service | SuccessfulRequests | Solicitações de gateway com êxito (preteridas) | Contagem | Total
Sim | Microsoft.ApiManagement/service | TotalPedidos | Total de solicitações do gateway (preterido) | Contagem | Total
Sim | Microsoft.ApiManagement/service | UnauthorizedRequests | Solicitações de gateway não autorizadas (preteridas) | Contagem | Total
Sim | Microsoft. AppPlatform/Spring | AppCpuUsagePercentage | Porcentagem de uso de CPU do aplicativo | Percentagem | Média
Sim | Microsoft. AppPlatform/Spring | AppMemoryCommitted | Memória de aplicativo atribuída | Bytes | Média
Sim | Microsoft. AppPlatform/Spring | AppMemoryMax | Memória máxima do aplicativo | Bytes | Máximo
Sim | Microsoft. AppPlatform/Spring | AppMemoryUsed | Memória usada do aplicativo | Bytes | Média
Sim | Microsoft. AppPlatform/Spring | GCPauseTotalCount | Contagem de pausar do GC | Contagem | Total
Sim | Microsoft. AppPlatform/Spring | GCPauseTotalTime | Tempo total de pausa do GC | Milissegundos | Total
Sim | Microsoft. AppPlatform/Spring | MaxOldGenMemoryPoolBytes | Tamanho máximo de dados de geração antiga disponíveis | Bytes | Média
Sim | Microsoft. AppPlatform/Spring | OldGenMemoryPoolBytes | Tamanho de dados de geração antiga | Bytes | Média
Sim | Microsoft. AppPlatform/Spring | OldGenPromotedBytes | Promover para tamanho de dados de geração antiga | Bytes | Máximo
Sim | Microsoft. AppPlatform/Spring | SystemCpuUsagePercentage | Porcentagem de uso da CPU do sistema | Percentagem | Média
Sim | Microsoft. AppPlatform/Spring | TomcatErrorCount | Erro global do Tomcat | Contagem | Total
Sim | Microsoft. AppPlatform/Spring | TomcatReceivedBytes | Total de bytes recebidos do Tomcat | Bytes | Total
Sim | Microsoft. AppPlatform/Spring | TomcatRequestMaxTime | Tempo máximo de solicitação do Tomcat | Milissegundos | Máximo
Sim | Microsoft. AppPlatform/Spring | TomcatRequestTotalCount | Contagem total de solicitações do Tomcat | Contagem | Total
Sim | Microsoft. AppPlatform/Spring | TomcatRequestTotalTime | Tempos totais da solicitação do Tomcat | Milissegundos | Total
Sim | Microsoft. AppPlatform/Spring | TomcatResponseAvgTime | Tempo médio da solicitação do Tomcat | Milissegundos | Média
Sim | Microsoft. AppPlatform/Spring | TomcatSentBytes | Total de bytes enviados do Tomcat | Bytes | Total
Sim | Microsoft. AppPlatform/Spring | TomcatSessionActiveCurrentCount | Contagem de sessões ativas de Tomcat | Contagem | Total
Sim | Microsoft. AppPlatform/Spring | TomcatSessionActiveMaxCount | Contagem ativa máxima da sessão do Tomcat | Contagem | Total
Sim | Microsoft. AppPlatform/Spring | TomcatSessionAliveMaxTime | Tempo de atividade máx de sessão do Tomcat | Milissegundos | Máximo
Sim | Microsoft. AppPlatform/Spring | TomcatSessionCreatedCount | Contagem de sessões criadas do Tomcat | Contagem | Total
Sim | Microsoft. AppPlatform/Spring | TomcatSessionExpiredCount | Contagem de sessões expiradas do Tomcat | Contagem | Total
Sim | Microsoft. AppPlatform/Spring | TomcatSessionRejectedCount | Contagem rejeitada da sessão Tomcat | Contagem | Total
Sim | Microsoft. AppPlatform/Spring | YoungGenPromotedBytes | Promover para tamanho de dados de geração jovem | Bytes | Máximo
Sim | Microsoft.Automation/automationAccounts | TotalJob | Total de trabalhos | Contagem | Total
Sim | Microsoft.Automation/automationAccounts | TotalUpdateDeploymentMachineRuns | Total de execuções do computador de implantação de atualização | Contagem | Total
Sim | Microsoft.Automation/automationAccounts | TotalUpdateDeploymentRuns | Total de execuções de implantação de atualização | Contagem | Total
Não | Microsoft.Batch/batchAccounts | CoreCount | Contagem de núcleos dedicados | Contagem | Total
Não | Microsoft.Batch/batchAccounts | CreatingNodeCount | Criando contagem de nós | Contagem | Total
Não | Microsoft.Batch/batchAccounts | IdleNodeCount | Contagem de nós ociosos | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | JobDeleteCompleteEvent | Eventos completos de exclusão de trabalho | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | JobDeleteStartEvent | Eventos de início de exclusão de trabalho | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | JobDisableCompleteEvent | Trabalho de desabilitar eventos concluídos | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | JobDisableStartEvent | Eventos de início de desabilitação do trabalho | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | JobStartEvent | Eventos de início do trabalho | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | JobTerminateCompleteEvent | Eventos de término de trabalho concluídos | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | JobTerminateStartEvent | Eventos de início de término do trabalho | Contagem | Total
Não | Microsoft.Batch/batchAccounts | LeavingPoolNodeCount | Saindo da contagem de nós do pool | Contagem | Total
Não | Microsoft.Batch/batchAccounts | LowPriorityCoreCount | Contagem de núcleos de LowPriority | Contagem | Total
Não | Microsoft.Batch/batchAccounts | OfflineNodeCount | Contagem de nós offline | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | PoolCreateEvent | Eventos de criação de pool | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | PoolDeleteCompleteEvent | Eventos completos de exclusão de pool | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | PoolDeleteStartEvent | Eventos de início de exclusão de pool | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | PoolResizeCompleteEvent | Eventos completos de redimensionamento de pool | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | PoolResizeStartEvent | Eventos de início de redimensionamento do pool | Contagem | Total
Não | Microsoft.Batch/batchAccounts | PreemptedNodeCount | Contagem de nós preempção | Contagem | Total
Não | Microsoft.Batch/batchAccounts | RebootingNodeCount | Contagem de nós de reinicialização | Contagem | Total
Não | Microsoft.Batch/batchAccounts | ReimagingNodeCount | Contagem de nós de reimagem | Contagem | Total
Não | Microsoft.Batch/batchAccounts | RunningNodeCount | Contagem de nós em execução | Contagem | Total
Não | Microsoft.Batch/batchAccounts | StartingNodeCount | Contagem de nós inicial | Contagem | Total
Não | Microsoft.Batch/batchAccounts | StartTaskFailedNodeCount | Falha na contagem de nós da tarefa inicial | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | TaskCompleteEvent | Eventos de conclusão de tarefa | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | TaskFailEvent | Eventos de falha de tarefa | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | TaskStartEvent | Eventos de início de tarefa | Contagem | Total
Não | Microsoft.Batch/batchAccounts | TotalLowPriorityNodeCount | Contagem de nós de baixa prioridade | Contagem | Total
Não | Microsoft.Batch/batchAccounts | TotalNodeCount | Contagem de nós dedicados | Contagem | Total
Não | Microsoft.Batch/batchAccounts | UnusableNodeCount | Contagem de nós inutilizáveis | Contagem | Total
Não | Microsoft.Batch/batchAccounts | WaitingForStartTaskNodeCount | Aguardando a contagem de nó de tarefa inicial | Contagem | Total
Sim | Microsoft. BatchAI/Workspaces | Núcleos ativos | Núcleos ativos | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Nós ativos | Nós ativos | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Núcleos ociosos | Núcleos ociosos | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Nós ociosos | Nós ociosos | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Trabalho concluído | Trabalho concluído | Contagem | Total
Sim | Microsoft. BatchAI/Workspaces | Trabalho enviado | Trabalho enviado | Contagem | Total
Sim | Microsoft. BatchAI/Workspaces | Deixando núcleos | Deixando núcleos | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Saindo de nós | Saindo de nós | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Núcleos preempçãos | Núcleos preempçãos | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Nós admitidos | Nós admitidos | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Porcentagem de utilização de cota | Porcentagem de utilização de cota | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Total de núcleos | Total de núcleos | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Total de nós | Total de nós | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Núcleos inutilizáveis | Núcleos inutilizáveis | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Nós inutilizáveis | Nós inutilizáveis | Contagem | Média
Sim | Microsoft.Blockchain/blockchainMembers | ConnectionAccepted | Conexões aceitas | Contagem | Total
Sim | Microsoft.Blockchain/blockchainMembers | ConnectionActive | Conexões ativas | Contagem | Média
Sim | Microsoft.Blockchain/blockchainMembers | ConnectionHandled | Conexões manipuladas | Contagem | Total
Sim | Microsoft.Blockchain/blockchainMembers | CpuUsagePercentageInDouble | Porcentagem de uso da CPU | Percentagem | Máximo
Sim | Microsoft.Blockchain/blockchainMembers | IOReadBytes | Bytes de leitura de e/s | Bytes | Total
Sim | Microsoft.Blockchain/blockchainMembers | IOWriteBytes | Bytes de gravação de e/s | Bytes | Total
Sim | Microsoft.Blockchain/blockchainMembers | MemoryLimit | Limite de memória | Bytes | Média
Sim | Microsoft.Blockchain/blockchainMembers | MemoryUsage | Utilização de Memória | Bytes | Média
Sim | Microsoft.Blockchain/blockchainMembers | MemoryUsagePercentageInDouble | Porcentagem de uso da memória | Percentagem | Média
Sim | Microsoft.Blockchain/blockchainMembers | PendingTransactions | Transações pendentes | Contagem | Média
Sim | Microsoft.Blockchain/blockchainMembers | ProcessedBlocks | Blocos processados | Contagem | Total
Sim | Microsoft.Blockchain/blockchainMembers | ProcessedTransactions | Transações processadas | Contagem | Total
Sim | Microsoft.Blockchain/blockchainMembers | QueuedTransactions | Transações em fila | Contagem | Média
Sim | Microsoft.Blockchain/blockchainMembers | RequestHandled | Solicitações manipuladas | Contagem | Total
Sim | Microsoft.Blockchain/blockchainMembers | StorageUsage | Uso do armazenamento | Bytes | Média
Sim | Microsoft.Cache/redis | cachehits | Acertos do cache | Contagem | Total
Sim | Microsoft.Cache/redis | cachehits0 | Acertos do cache (fragmento 0) | Contagem | Total
Sim | Microsoft.Cache/redis | cachehits1 | Acertos do cache (fragmento 1) | Contagem | Total
Sim | Microsoft.Cache/redis | cachehits2 | Acertos do cache (fragmento 2) | Contagem | Total
Sim | Microsoft.Cache/redis | cachehits3 | Acertos do cache (fragmento 3) | Contagem | Total
Sim | Microsoft.Cache/redis | cachehits4 | Acertos do cache (fragmento 4) | Contagem | Total
Sim | Microsoft.Cache/redis | cachehits5 | Acertos do cache (fragmento 5) | Contagem | Total
Sim | Microsoft.Cache/redis | cachehits6 | Acertos do cache (fragmento 6) | Contagem | Total
Sim | Microsoft.Cache/redis | cachehits7 | Acertos do cache (fragmento 7) | Contagem | Total
Sim | Microsoft.Cache/redis | cachehits8 | Acertos do cache (fragmento 8) | Contagem | Total
Sim | Microsoft.Cache/redis | cachehits9 | Acertos do cache (fragmento 9) | Contagem | Total
Sim | Microsoft.Cache/redis | cacheLatency | Microssegundos de latência de cache (versão prévia) | Contagem | Média
Sim | Microsoft.Cache/redis | cachemisses | Erros de cache | Contagem | Total
Sim | Microsoft.Cache/redis | cachemisses0 | Erros de cache (fragmento 0) | Contagem | Total
Sim | Microsoft.Cache/redis | cachemisses1 | Erros de cache (fragmento 1) | Contagem | Total
Sim | Microsoft.Cache/redis | cachemisses2 | Erros de cache (fragmento 2) | Contagem | Total
Sim | Microsoft.Cache/redis | cachemisses3 | Erros de cache (fragmento 3) | Contagem | Total
Sim | Microsoft.Cache/redis | cachemisses4 | Erros de cache (fragmento 4) | Contagem | Total
Sim | Microsoft.Cache/redis | cachemisses5 | Erros de cache (fragmento 5) | Contagem | Total
Sim | Microsoft.Cache/redis | cachemisses6 | Erros de cache (fragmento 6) | Contagem | Total
Sim | Microsoft.Cache/redis | cachemisses7 | Erros de cache (fragmento 7) | Contagem | Total
Sim | Microsoft.Cache/redis | cachemisses8 | Erros de cache (fragmento 8) | Contagem | Total
Sim | Microsoft.Cache/redis | cachemisses9 | Erros de cache (fragmento 9) | Contagem | Total
Sim | Microsoft.Cache/redis | cacheRead | Leitura de cache | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheRead0 | Leitura de cache (fragmento 0) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheRead1 | Leitura do cache (fragmento 1) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheRead2 | Leitura de cache (fragmento 2) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheRead3 | Leitura do cache (fragmento 3) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheRead4 | Leitura do cache (fragmento 4) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheRead5 | Leitura do cache (fragmento 5) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheRead6 | Leitura do cache (fragmento 6) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheRead7 | Leitura do cache (fragmento 7) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheRead8 | Leitura de cache (fragmento 8) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheRead9 | Leitura do cache (fragmento 9) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheWrite | Gravação de cache | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheWrite0 | Gravação no cache (fragmento 0) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheWrite1 | Gravação no cache (fragmento 1) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheWrite2 | Gravação no cache (fragmento 2) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheWrite3 | Gravação no cache (fragmento 3) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheWrite4 | Gravação no cache (fragmento 4) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheWrite5 | Gravação no cache (fragmento 5) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheWrite6 | Gravação no cache (fragmento 6) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheWrite7 | Gravação no cache (fragmento 7) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheWrite8 | Gravação no cache (fragmento 8) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheWrite9 | Gravação no cache (fragmento 9) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | connectedclients | Clientes Ligados | Contagem | Máximo
Sim | Microsoft.Cache/redis | connectedclients0 | Clientes conectados (fragmento 0) | Contagem | Máximo
Sim | Microsoft.Cache/redis | connectedclients1 | Clientes conectados (fragmento 1) | Contagem | Máximo
Sim | Microsoft.Cache/redis | connectedclients2 | Clientes conectados (fragmento 2) | Contagem | Máximo
Sim | Microsoft.Cache/redis | connectedclients3 | Clientes conectados (fragmento 3) | Contagem | Máximo
Sim | Microsoft.Cache/redis | connectedclients4 | Clientes conectados (fragmento 4) | Contagem | Máximo
Sim | Microsoft.Cache/redis | connectedclients5 | Clientes conectados (fragmento 5) | Contagem | Máximo
Sim | Microsoft.Cache/redis | connectedclients6 | Clientes conectados (fragmento 6) | Contagem | Máximo
Sim | Microsoft.Cache/redis | connectedclients7 | Clientes conectados (fragmento 7) | Contagem | Máximo
Sim | Microsoft.Cache/redis | connectedclients8 | Clientes conectados (fragmento 8) | Contagem | Máximo
Sim | Microsoft.Cache/redis | connectedclients9 | Clientes conectados (fragmento 9) | Contagem | Máximo
Sim | Microsoft.Cache/redis | erros | Erros | Contagem | Máximo
Sim | Microsoft.Cache/redis | evictedkeys | Chaves removidas | Contagem | Total
Sim | Microsoft.Cache/redis | evictedkeys0 | Chaves removidas (fragmento 0) | Contagem | Total
Sim | Microsoft.Cache/redis | evictedkeys1 | Chaves removidas (fragmento 1) | Contagem | Total
Sim | Microsoft.Cache/redis | evictedkeys2 | Chaves removidas (fragmento 2) | Contagem | Total
Sim | Microsoft.Cache/redis | evictedkeys3 | Chaves removidas (fragmento 3) | Contagem | Total
Sim | Microsoft.Cache/redis | evictedkeys4 | Chaves removidas (fragmento 4) | Contagem | Total
Sim | Microsoft.Cache/redis | evictedkeys5 | Chaves removidas (fragmento 5) | Contagem | Total
Sim | Microsoft.Cache/redis | evictedkeys6 | Chaves removidas (fragmento 6) | Contagem | Total
Sim | Microsoft.Cache/redis | evictedkeys7 | Chaves removidas (fragmento 7) | Contagem | Total
Sim | Microsoft.Cache/redis | evictedkeys8 | Chaves removidas (fragmento 8) | Contagem | Total
Sim | Microsoft.Cache/redis | evictedkeys9 | Chaves removidas (fragmento 9) | Contagem | Total
Sim | Microsoft.Cache/redis | expiredkeys | Chaves expiradas | Contagem | Total
Sim | Microsoft.Cache/redis | expiredkeys0 | Chaves expiradas (fragmento 0) | Contagem | Total
Sim | Microsoft.Cache/redis | expiredkeys1 | Chaves expiradas (fragmento 1) | Contagem | Total
Sim | Microsoft.Cache/redis | expiredkeys2 | Chaves expiradas (fragmento 2) | Contagem | Total
Sim | Microsoft.Cache/redis | expiredkeys3 | Chaves expiradas (fragmento 3) | Contagem | Total
Sim | Microsoft.Cache/redis | expiredkeys4 | Chaves expiradas (fragmento 4) | Contagem | Total
Sim | Microsoft.Cache/redis | expiredkeys5 | Chaves expiradas (fragmento 5) | Contagem | Total
Sim | Microsoft.Cache/redis | expiredkeys6 | Chaves expiradas (fragmento 6) | Contagem | Total
Sim | Microsoft.Cache/redis | expiredkeys7 | Chaves expiradas (fragmento 7) | Contagem | Total
Sim | Microsoft.Cache/redis | expiredkeys8 | Chaves expiradas (fragmento 8) | Contagem | Total
Sim | Microsoft.Cache/redis | expiredkeys9 | Chaves expiradas (fragmento 9) | Contagem | Total
Sim | Microsoft.Cache/redis | comandos | Visível | Contagem | Total
Sim | Microsoft.Cache/redis | getcommands0 | Gets (fragmento 0) | Contagem | Total
Sim | Microsoft.Cache/redis | getcommands1 | Gets (fragmento 1) | Contagem | Total
Sim | Microsoft.Cache/redis | getcommands2 | Gets (fragmento 2) | Contagem | Total
Sim | Microsoft.Cache/redis | getcommands3 | Gets (fragmento 3) | Contagem | Total
Sim | Microsoft.Cache/redis | getcommands4 | Gets (fragmento 4) | Contagem | Total
Sim | Microsoft.Cache/redis | getcommands5 | Gets (fragmento 5) | Contagem | Total
Sim | Microsoft.Cache/redis | getcommands6 | Gets (fragmento 6) | Contagem | Total
Sim | Microsoft.Cache/redis | getcommands7 | Gets (fragmento 7) | Contagem | Total
Sim | Microsoft.Cache/redis | getcommands8 | Gets (fragmento 8) | Contagem | Total
Sim | Microsoft.Cache/redis | getcommands9 | Gets (fragmento 9) | Contagem | Total
Sim | Microsoft.Cache/redis | operationsPerSecond | Operações por segundo | Contagem | Máximo
Sim | Microsoft.Cache/redis | operationsPerSecond0 | Operações por segundo (fragmento 0) | Contagem | Máximo
Sim | Microsoft.Cache/redis | operationsPerSecond1 | Operações por segundo (fragmento 1) | Contagem | Máximo
Sim | Microsoft.Cache/redis | operationsPerSecond2 | Operações por segundo (fragmento 2) | Contagem | Máximo
Sim | Microsoft.Cache/redis | operationsPerSecond3 | Operações por segundo (fragmento 3) | Contagem | Máximo
Sim | Microsoft.Cache/redis | operationsPerSecond4 | Operações por segundo (fragmento 4) | Contagem | Máximo
Sim | Microsoft.Cache/redis | operationsPerSecond5 | Operações por segundo (fragmento 5) | Contagem | Máximo
Sim | Microsoft.Cache/redis | operationsPerSecond6 | Operações por segundo (fragmento 6) | Contagem | Máximo
Sim | Microsoft.Cache/redis | operationsPerSecond7 | Operações por segundo (fragmento 7) | Contagem | Máximo
Sim | Microsoft.Cache/redis | operationsPerSecond8 | Operações por segundo (fragmento 8) | Contagem | Máximo
Sim | Microsoft.Cache/redis | operationsPerSecond9 | Operações por segundo (fragmento 9) | Contagem | Máximo
Sim | Microsoft.Cache/redis | percentProcessorTime | CPU | Percentagem | Máximo
Sim | Microsoft.Cache/redis | percentProcessorTime0 | CPU (fragmento 0) | Percentagem | Máximo
Sim | Microsoft.Cache/redis | percentProcessorTime1 | CPU (fragmento 1) | Percentagem | Máximo
Sim | Microsoft.Cache/redis | percentProcessorTime2 | CPU (fragmento 2) | Percentagem | Máximo
Sim | Microsoft.Cache/redis | percentProcessorTime3 | CPU (fragmento 3) | Percentagem | Máximo
Sim | Microsoft.Cache/redis | percentProcessorTime4 | CPU (fragmento 4) | Percentagem | Máximo
Sim | Microsoft.Cache/redis | percentProcessorTime5 | CPU (fragmento 5) | Percentagem | Máximo
Sim | Microsoft.Cache/redis | percentProcessorTime6 | CPU (fragmento 6) | Percentagem | Máximo
Sim | Microsoft.Cache/redis | percentProcessorTime7 | CPU (fragmento 7) | Percentagem | Máximo
Sim | Microsoft.Cache/redis | percentProcessorTime8 | CPU (fragmento 8) | Percentagem | Máximo
Sim | Microsoft.Cache/redis | percentProcessorTime9 | CPU (fragmento 9) | Percentagem | Máximo
Sim | Microsoft.Cache/redis | serverLoad | Carga do servidor | Percentagem | Máximo
Sim | Microsoft.Cache/redis | serverLoad0 | Carga do servidor (fragmento 0) | Percentagem | Máximo
Sim | Microsoft.Cache/redis | serverLoad1 | Carga do servidor (fragmento 1) | Percentagem | Máximo
Sim | Microsoft.Cache/redis | serverLoad2 | Carga do servidor (fragmento 2) | Percentagem | Máximo
Sim | Microsoft.Cache/redis | serverLoad3 | Carga do servidor (fragmento 3) | Percentagem | Máximo
Sim | Microsoft.Cache/redis | serverLoad4 | Carga do servidor (fragmento 4) | Percentagem | Máximo
Sim | Microsoft.Cache/redis | serverLoad5 | Carga do servidor (fragmento 5) | Percentagem | Máximo
Sim | Microsoft.Cache/redis | serverLoad6 | Carga do servidor (fragmento 6) | Percentagem | Máximo
Sim | Microsoft.Cache/redis | serverLoad7 | Carga do servidor (fragmento 7) | Percentagem | Máximo
Sim | Microsoft.Cache/redis | serverLoad8 | Carga do servidor (fragmento 8) | Percentagem | Máximo
Sim | Microsoft.Cache/redis | serverLoad9 | Carga do servidor (fragmento 9) | Percentagem | Máximo
Sim | Microsoft.Cache/redis | comandos | Conjuntos | Contagem | Total
Sim | Microsoft.Cache/redis | setcommands0 | Sets (fragmento 0) | Contagem | Total
Sim | Microsoft.Cache/redis | setcommands1 | Sets (fragmento 1) | Contagem | Total
Sim | Microsoft.Cache/redis | setcommands2 | Sets (fragmento 2) | Contagem | Total
Sim | Microsoft.Cache/redis | setcommands3 | Sets (fragmento 3) | Contagem | Total
Sim | Microsoft.Cache/redis | setcommands4 | Sets (fragmento 4) | Contagem | Total
Sim | Microsoft.Cache/redis | setcommands5 | Sets (fragmento 5) | Contagem | Total
Sim | Microsoft.Cache/redis | setcommands6 | Sets (fragmento 6) | Contagem | Total
Sim | Microsoft.Cache/redis | setcommands7 | Sets (fragmento 7) | Contagem | Total
Sim | Microsoft.Cache/redis | setcommands8 | Sets (fragmento 8) | Contagem | Total
Sim | Microsoft.Cache/redis | setcommands9 | Sets (fragmento 9) | Contagem | Total
Sim | Microsoft.Cache/redis | totalcommandsprocessed | Total de operações | Contagem | Total
Sim | Microsoft.Cache/redis | totalcommandsprocessed0 | Total de operações (fragmento 0) | Contagem | Total
Sim | Microsoft.Cache/redis | totalcommandsprocessed1 | Total de operações (fragmento 1) | Contagem | Total
Sim | Microsoft.Cache/redis | totalcommandsprocessed2 | Total de operações (fragmento 2) | Contagem | Total
Sim | Microsoft.Cache/redis | totalcommandsprocessed3 | Total de operações (fragmento 3) | Contagem | Total
Sim | Microsoft.Cache/redis | totalcommandsprocessed4 | Total de operações (fragmento 4) | Contagem | Total
Sim | Microsoft.Cache/redis | totalcommandsprocessed5 | Total de operações (fragmento 5) | Contagem | Total
Sim | Microsoft.Cache/redis | totalcommandsprocessed6 | Total de operações (fragmento 6) | Contagem | Total
Sim | Microsoft.Cache/redis | totalcommandsprocessed7 | Total de operações (fragmento 7) | Contagem | Total
Sim | Microsoft.Cache/redis | totalcommandsprocessed8 | Total de operações (fragmento 8) | Contagem | Total
Sim | Microsoft.Cache/redis | totalcommandsprocessed9 | Total de operações (fragmento 9) | Contagem | Total
Sim | Microsoft.Cache/redis | totalkeys | Total de chaves | Contagem | Máximo
Sim | Microsoft.Cache/redis | totalkeys0 | Total de chaves (fragmento 0) | Contagem | Máximo
Sim | Microsoft.Cache/redis | totalkeys1 | Total de chaves (fragmento 1) | Contagem | Máximo
Sim | Microsoft.Cache/redis | totalkeys2 | Total de chaves (fragmento 2) | Contagem | Máximo
Sim | Microsoft.Cache/redis | totalkeys3 | Total de chaves (fragmento 3) | Contagem | Máximo
Sim | Microsoft.Cache/redis | totalkeys4 | Total de chaves (fragmento 4) | Contagem | Máximo
Sim | Microsoft.Cache/redis | totalkeys5 | Total de chaves (fragmento 5) | Contagem | Máximo
Sim | Microsoft.Cache/redis | totalkeys6 | Total de chaves (fragmento 6) | Contagem | Máximo
Sim | Microsoft.Cache/redis | totalkeys7 | Total de chaves (fragmento 7) | Contagem | Máximo
Sim | Microsoft.Cache/redis | totalkeys8 | Total de chaves (fragmento 8) | Contagem | Máximo
Sim | Microsoft.Cache/redis | totalkeys9 | Total de chaves (fragmento 9) | Contagem | Máximo
Sim | Microsoft.Cache/redis | usedmemory | Memória usada | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemory0 | Memória usada (fragmento 0) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemory1 | Memória usada (fragmento 1) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemory2 | Memória usada (fragmento 2) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemory3 | Memória usada (fragmento 3) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemory4 | Memória usada (fragmento 4) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemory5 | Memória usada (fragmento 5) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemory6 | Memória usada (fragmento 6) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemory7 | Memória usada (fragmento 7) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemory8 | Memória usada (fragmento 8) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemory9 | Memória usada (fragmento 9) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemorypercentage | Porcentagem de memória usada | Percentagem | Máximo
Sim | Microsoft.Cache/redis | usedmemoryRss | Memória RSS usada | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemoryRss0 | Memória RSS usada (fragmento 0) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemoryRss1 | Memória RSS usada (fragmento 1) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemoryRss2 | Memória RSS usada (fragmento 2) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemoryRss3 | Memória RSS usada (fragmento 3) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemoryRss4 | Memória RSS usada (fragmento 4) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemoryRss5 | Memória RSS usada (fragmento 5) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemoryRss6 | Memória RSS usada (fragmento 6) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemoryRss7 | Memória RSS usada (fragmento 7) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemoryRss8 | Memória RSS usada (fragmento 8) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemoryRss9 | Memória RSS usada (fragmento 9) | Bytes | Máximo
Não | Microsoft.ClassicCompute/domainNames/slots/roles | Bytes de leitura de disco/s | Leitura de disco | BytesPerSecond | Média
Sim | Microsoft.ClassicCompute/domainNames/slots/roles | Operações de leitura de disco/s | Operações de leitura de disco/s | CountPerSecond | Média
Não | Microsoft.ClassicCompute/domainNames/slots/roles | Bytes de gravação no disco/s | Gravação de disco | BytesPerSecond | Média
Sim | Microsoft.ClassicCompute/domainNames/slots/roles | Operações de gravação de disco/s | Operações de gravação de disco/s | CountPerSecond | Média
Sim | Microsoft.ClassicCompute/domainNames/slots/roles | Entrada na Rede | Entrada na Rede | Bytes | Total
Sim | Microsoft.ClassicCompute/domainNames/slots/roles | Saída da Rede | Saída da Rede | Bytes | Total
Sim | Microsoft.ClassicCompute/domainNames/slots/roles | Percentagem da CPU | Percentagem da CPU | Percentagem | Média
Não | Microsoft.ClassicCompute/virtualMachines | Bytes de leitura de disco/s | Leitura de disco | BytesPerSecond | Média
Sim | Microsoft.ClassicCompute/virtualMachines | Operações de leitura de disco/s | Operações de leitura de disco/s | CountPerSecond | Média
Não | Microsoft.ClassicCompute/virtualMachines | Bytes de gravação no disco/s | Gravação de disco | BytesPerSecond | Média
Sim | Microsoft.ClassicCompute/virtualMachines | Operações de gravação de disco/s | Operações de gravação de disco/s | CountPerSecond | Média
Sim | Microsoft.ClassicCompute/virtualMachines | Entrada na Rede | Entrada na Rede | Bytes | Total
Sim | Microsoft.ClassicCompute/virtualMachines | Saída da Rede | Saída da Rede | Bytes | Total
Sim | Microsoft.ClassicCompute/virtualMachines | Percentagem da CPU | Percentagem da CPU | Percentagem | Média
Sim | Microsoft.ClassicStorage/storageAccounts | Disponibilidade | Disponibilidade | Percentagem | Média
Sim | Microsoft.ClassicStorage/storageAccounts | Saída | Saída | Bytes | Total
Sim | Microsoft.ClassicStorage/storageAccounts | Entrada | Entrada | Bytes | Total
Sim | Microsoft.ClassicStorage/storageAccounts | SuccessE2ELatency | Latência de E2E Com Êxito | Milissegundos | Média
Sim | Microsoft.ClassicStorage/storageAccounts | SuccessServerLatency | Latência de Servidor Com Êxito | Milissegundos | Média
Sim | Microsoft.ClassicStorage/storageAccounts | Transações | Transações | Contagem | Total
Não | Microsoft.ClassicStorage/storageAccounts | UsedCapacity | Capacidade utilizada | Bytes | Média
Sim | Microsoft. ClassicStorage/storageAccounts/blobservices | Disponibilidade | Disponibilidade | Percentagem | Média
Não | Microsoft. ClassicStorage/storageAccounts/blobservices | BlobCapacity | Capacidade do Blob | Bytes | Média
Não | Microsoft. ClassicStorage/storageAccounts/blobservices | BlobCount | Contagem de Blobs | Contagem | Média
Sim | Microsoft. ClassicStorage/storageAccounts/blobservices | ContainerCount | Contagem do Contentor de Blobs | Contagem | Média
Sim | Microsoft. ClassicStorage/storageAccounts/blobservices | Saída | Saída | Bytes | Total
Não | Microsoft. ClassicStorage/storageAccounts/blobservices | IndexCapacity | Capacidade de Índice | Bytes | Média
Sim | Microsoft. ClassicStorage/storageAccounts/blobservices | Entrada | Entrada | Bytes | Total
Sim | Microsoft. ClassicStorage/storageAccounts/blobservices | SuccessE2ELatency | Latência de E2E Com Êxito | Milissegundos | Média
Sim | Microsoft. ClassicStorage/storageAccounts/blobservices | SuccessServerLatency | Latência de Servidor Com Êxito | Milissegundos | Média
Sim | Microsoft. ClassicStorage/storageAccounts/blobservices | Transações | Transações | Contagem | Total
Sim | Microsoft. ClassicStorage/storageAccounts/fileservices | Disponibilidade | Disponibilidade | Percentagem | Média
Sim | Microsoft. ClassicStorage/storageAccounts/fileservices | Saída | Saída | Bytes | Total
Não | Microsoft. ClassicStorage/storageAccounts/fileservices | Capacidade de | Capacidade do arquivo | Bytes | Média
Não | Microsoft. ClassicStorage/storageAccounts/fileservices | FileCount | Contagem de arquivos | Contagem | Média
Não | Microsoft. ClassicStorage/storageAccounts/fileservices | FileShareCount | Contagem de compartilhamento de arquivos | Contagem | Média
Não | Microsoft. ClassicStorage/storageAccounts/fileservices | FileShareQuota | Tamanho da cota de compartilhamento de arquivos | Bytes | Média
Não | Microsoft. ClassicStorage/storageAccounts/fileservices | FileShareSnapshotCount | Contagem de instantâneos de compartilhamento de arquivos | Contagem | Média
Não | Microsoft. ClassicStorage/storageAccounts/fileservices | FileShareSnapshotSize | Tamanho do instantâneo de compartilhamento de arquivos | Bytes | Média
Sim | Microsoft. ClassicStorage/storageAccounts/fileservices | Entrada | Entrada | Bytes | Total
Sim | Microsoft. ClassicStorage/storageAccounts/fileservices | SuccessE2ELatency | Latência de E2E Com Êxito | Milissegundos | Média
Sim | Microsoft. ClassicStorage/storageAccounts/fileservices | SuccessServerLatency | Latência de Servidor Com Êxito | Milissegundos | Média
Sim | Microsoft. ClassicStorage/storageAccounts/fileservices | Transações | Transações | Contagem | Total
Sim | Microsoft. ClassicStorage/storageAccounts/queueservices | Disponibilidade | Disponibilidade | Percentagem | Média
Sim | Microsoft. ClassicStorage/storageAccounts/queueservices | Saída | Saída | Bytes | Total
Sim | Microsoft. ClassicStorage/storageAccounts/queueservices | Entrada | Entrada | Bytes | Total
Sim | Microsoft. ClassicStorage/storageAccounts/queueservices | QueueCapacity | Capacidade de Fila | Bytes | Média
Sim | Microsoft. ClassicStorage/storageAccounts/queueservices | QueueCount | Contagem de Filas | Contagem | Média
Sim | Microsoft. ClassicStorage/storageAccounts/queueservices | QueueMessageCount | Contagem de Mensagens em Fila | Contagem | Média
Sim | Microsoft. ClassicStorage/storageAccounts/queueservices | SuccessE2ELatency | Latência de E2E Com Êxito | Milissegundos | Média
Sim | Microsoft. ClassicStorage/storageAccounts/queueservices | SuccessServerLatency | Latência de Servidor Com Êxito | Milissegundos | Média
Sim | Microsoft. ClassicStorage/storageAccounts/queueservices | Transações | Transações | Contagem | Total
Sim | Microsoft. ClassicStorage/storageAccounts/tabelaservices | Disponibilidade | Disponibilidade | Percentagem | Média
Sim | Microsoft. ClassicStorage/storageAccounts/tabelaservices | Saída | Saída | Bytes | Total
Sim | Microsoft. ClassicStorage/storageAccounts/tabelaservices | Entrada | Entrada | Bytes | Total
Sim | Microsoft. ClassicStorage/storageAccounts/tabelaservices | SuccessE2ELatency | Latência de E2E Com Êxito | Milissegundos | Média
Sim | Microsoft. ClassicStorage/storageAccounts/tabelaservices | SuccessServerLatency | Latência de Servidor Com Êxito | Milissegundos | Média
Sim | Microsoft. ClassicStorage/storageAccounts/tabelaservices | TableCapacity | Capacidade de Tabelas | Bytes | Média
Sim | Microsoft. ClassicStorage/storageAccounts/tabelaservices | TableCount | Contagem de Tabelas | Contagem | Média
Sim | Microsoft. ClassicStorage/storageAccounts/tabelaservices | TableEntityCount | Contagem de Entidade de Tabelas | Contagem | Média
Sim | Microsoft. ClassicStorage/storageAccounts/tabelaservices | Transações | Transações | Contagem | Total
Sim | Microsoft.CognitiveServices/accounts | BlockedCalls | Chamadas bloqueadas | Contagem | Total
Sim | Microsoft.CognitiveServices/accounts | CharactersTrained | Caracteres treinados | Contagem | Total
Sim | Microsoft.CognitiveServices/accounts | CharactersTranslated | Caracteres traduzidos | Contagem | Total
Sim | Microsoft.CognitiveServices/accounts | ClientErrors | Erros do cliente | Contagem | Total
Sim | Microsoft.CognitiveServices/accounts | Dados de | Entrada de Dados | Bytes | Total
Sim | Microsoft.CognitiveServices/accounts | DataOut | Saída de Dados | Bytes | Total
Sim | Microsoft.CognitiveServices/accounts | Latência | Latência | Milissegundos | Média
Sim | Microsoft.CognitiveServices/accounts | ServerErrors | Erros do servidor | Contagem | Total
Sim | Microsoft.CognitiveServices/accounts | SpeechSessionDuration | Duração da sessão de fala | Segundos | Total
Sim | Microsoft.CognitiveServices/accounts | SuccessfulCalls | Chamadas com êxito | Contagem | Total
Sim | Microsoft.CognitiveServices/accounts | TotalCalls | Total de chamadas | Contagem | Total
Sim | Microsoft.CognitiveServices/accounts | TotalErrors | Total de Erros | Contagem | Total
Sim | Microsoft.CognitiveServices/accounts | TotalTokenCalls | Total de chamadas de token | Contagem | Total
Sim | Microsoft.CognitiveServices/accounts | TotalTransactions | Total de transações | Contagem | Total
Sim | Microsoft.Compute/virtualMachines | Créditos de CPU consumidos | Créditos de CPU consumidos | Contagem | Média
Sim | Microsoft.Compute/virtualMachines | Créditos de CPU restantes | Créditos de CPU restantes | Contagem | Média
Sim | Microsoft.Compute/virtualMachines | Profundidade da Fila do Disco de Dados | Profundidade da fila do disco de dados (visualização) | Contagem | Média
Sim | Microsoft.Compute/virtualMachines | Bytes de leitura do disco de dados/s | Bytes de leitura do disco de dados/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Operações de leitura de disco de dados/s | Operações de leitura de disco de dados/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Bytes de gravação do disco de dados/s | Bytes de gravação do disco de dados/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Operações de gravação do disco de dados/s | Operações de gravação do disco de dados/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Bytes de leitura de disco | Bytes de leitura de disco | Bytes | Total
Sim | Microsoft.Compute/virtualMachines | Operações de leitura de disco/s | Operações de leitura de disco/s | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Bytes de gravação no disco | Bytes de gravação no disco | Bytes | Total
Sim | Microsoft.Compute/virtualMachines | Operações de gravação de disco/s | Operações de gravação de disco/s | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Fluxos de Entrada | Fluxos de Entrada | Contagem | Média
Sim | Microsoft.Compute/virtualMachines | Taxa máxima de criação de fluxos de entrada | Taxa máxima de criação de fluxos de entrada (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Entrada na Rede | Rede em faturável (preterida) | Bytes | Total
Sim | Microsoft.Compute/virtualMachines | Total de rede | Total de rede | Bytes | Total
Sim | Microsoft.Compute/virtualMachines | Saída da Rede | Saída de rede faturável (preterida) | Bytes | Total
Sim | Microsoft.Compute/virtualMachines | Total de saída de rede | Total de saída de rede | Bytes | Total
Sim | Microsoft.Compute/virtualMachines | Profundidade da Fila do Disco do SO | Profundidade da fila de disco do so (visualização) | Contagem | Média
Sim | Microsoft.Compute/virtualMachines | Bytes de leitura do disco do so/s | Bytes de leitura do disco do so/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Operações de leitura de disco do so/s | Operações de leitura de disco do so/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Bytes de gravação de disco do sistema operacional/s | Bytes de gravação de disco do sistema operacional/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Operações de gravação de disco do so/s | Operações de gravação de disco do so/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Sistema operacional por disco QD | Disco do so QD (preterido) | Contagem | Média
Sim | Microsoft.Compute/virtualMachines | Bytes de leitura por disco do so/s | Bytes de leitura do disco do so/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Operações de leitura de so por disco/s | Operações de leitura de disco do so/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Bytes de gravação por disco do so/s | Bytes de gravação de disco do sistema operacional/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Operações de gravação de so por disco/s | Operações de gravação de disco do sistema operacional/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Fluxos de saída | Fluxos de saída | Contagem | Média
Sim | Microsoft.Compute/virtualMachines | Taxa máxima de criação de fluxos de saída | Taxa máxima de criação de fluxos de saída (versão prévia) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | QD por disco | Disco de dados QD (preterido) | Contagem | Média
Sim | Microsoft.Compute/virtualMachines | Bytes de leitura por disco/s | Bytes de leitura do disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Operações de leitura por disco/s | Operações de leitura de disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Bytes de gravação por disco/s | Bytes de gravação do disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Operações de gravação por disco/s | Operações de gravação do disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Percentagem da CPU | Percentagem da CPU | Percentagem | Média
Sim | Microsoft.Compute/virtualMachines | Cache do disco de dados Premium-acesso de leitura | Leitura do cache do disco de dados Premium (versão prévia) | Percentagem | Média
Sim | Microsoft.Compute/virtualMachines | Erro de leitura do cache do disco de dados Premium | Cache do disco de dados Premium-erro de leitura (visualização) | Percentagem | Média
Sim | Microsoft.Compute/virtualMachines | Cache de disco do sistema operacional Premium-acesso de leitura | Cache de disco do sistema operacional Premium-acesso de leitura (versão prévia) | Percentagem | Média
Sim | Microsoft.Compute/virtualMachines | Erro de leitura do cache de disco do so premium | Erro de leitura do cache de disco do so premium (visualização) | Percentagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Créditos de CPU consumidos | Créditos de CPU consumidos | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Créditos de CPU restantes | Créditos de CPU restantes | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Profundidade da Fila do Disco de Dados | Profundidade da fila do disco de dados (visualização) | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Bytes de leitura do disco de dados/s | Bytes de leitura do disco de dados/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Operações de leitura de disco de dados/s | Operações de leitura de disco de dados/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Bytes de gravação do disco de dados/s | Bytes de gravação do disco de dados/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Operações de gravação do disco de dados/s | Operações de gravação do disco de dados/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Bytes de leitura de disco | Bytes de leitura de disco | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets | Operações de leitura de disco/s | Operações de leitura de disco/s | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Bytes de gravação no disco | Bytes de gravação no disco | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets | Operações de gravação de disco/s | Operações de gravação de disco/s | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Fluxos de Entrada | Fluxos de Entrada | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Taxa máxima de criação de fluxos de entrada | Taxa máxima de criação de fluxos de entrada (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Entrada na Rede | Rede em faturável (preterida) | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets | Total de rede | Total de rede | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets | Saída da Rede | Saída de rede faturável (preterida) | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets | Total de saída de rede | Total de saída de rede | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets | Profundidade da Fila do Disco do SO | Profundidade da fila de disco do so (visualização) | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Bytes de leitura do disco do so/s | Bytes de leitura do disco do so/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Operações de leitura de disco do so/s | Operações de leitura de disco do so/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Bytes de gravação de disco do sistema operacional/s | Bytes de gravação de disco do sistema operacional/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Operações de gravação de disco do so/s | Operações de gravação de disco do so/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Sistema operacional por disco QD | Disco do so QD (preterido) | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Bytes de leitura por disco do so/s | Bytes de leitura do disco do so/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Operações de leitura de so por disco/s | Operações de leitura de disco do so/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Bytes de gravação por disco do so/s | Bytes de gravação de disco do sistema operacional/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Operações de gravação de so por disco/s | Operações de gravação de disco do sistema operacional/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Fluxos de saída | Fluxos de saída | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Taxa máxima de criação de fluxos de saída | Taxa máxima de criação de fluxos de saída (versão prévia) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | QD por disco | Disco de dados QD (preterido) | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Bytes de leitura por disco/s | Bytes de leitura do disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Operações de leitura por disco/s | Operações de leitura de disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Bytes de gravação por disco/s | Bytes de gravação do disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Operações de gravação por disco/s | Operações de gravação do disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Percentagem da CPU | Percentagem da CPU | Percentagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Cache do disco de dados Premium-acesso de leitura | Leitura do cache do disco de dados Premium (versão prévia) | Percentagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Erro de leitura do cache do disco de dados Premium | Cache do disco de dados Premium-erro de leitura (visualização) | Percentagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Cache de disco do sistema operacional Premium-acesso de leitura | Cache de disco do sistema operacional Premium-acesso de leitura (versão prévia) | Percentagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Erro de leitura do cache de disco do so premium | Erro de leitura do cache de disco do so premium (visualização) | Percentagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Créditos de CPU consumidos | Créditos de CPU consumidos | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Créditos de CPU restantes | Créditos de CPU restantes | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Profundidade da Fila do Disco de Dados | Profundidade da fila do disco de dados (visualização) | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bytes de leitura do disco de dados/s | Bytes de leitura do disco de dados/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operações de leitura de disco de dados/s | Operações de leitura de disco de dados/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bytes de gravação do disco de dados/s | Bytes de gravação do disco de dados/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operações de gravação do disco de dados/s | Operações de gravação do disco de dados/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bytes de leitura de disco | Bytes de leitura de disco | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operações de leitura de disco/s | Operações de leitura de disco/s | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bytes de gravação no disco | Bytes de gravação no disco | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operações de gravação de disco/s | Operações de gravação de disco/s | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Fluxos de Entrada | Fluxos de Entrada | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Taxa máxima de criação de fluxos de entrada | Taxa máxima de criação de fluxos de entrada (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Entrada na Rede | Rede em faturável (preterida) | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Total de rede | Total de rede | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Saída da Rede | Saída de rede faturável (preterida) | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Total de saída de rede | Total de saída de rede | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Profundidade da Fila do Disco do SO | Profundidade da fila de disco do so (visualização) | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bytes de leitura do disco do so/s | Bytes de leitura do disco do so/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operações de leitura de disco do so/s | Operações de leitura de disco do so/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bytes de gravação de disco do sistema operacional/s | Bytes de gravação de disco do sistema operacional/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operações de gravação de disco do so/s | Operações de gravação de disco do so/s (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Sistema operacional por disco QD | Disco do so QD (preterido) | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bytes de leitura por disco do so/s | Bytes de leitura do disco do so/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operações de leitura de so por disco/s | Operações de leitura de disco do so/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bytes de gravação por disco do so/s | Bytes de gravação de disco do sistema operacional/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operações de gravação de so por disco/s | Operações de gravação de disco do sistema operacional/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Fluxos de saída | Fluxos de saída | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Taxa máxima de criação de fluxos de saída | Taxa máxima de criação de fluxos de saída (versão prévia) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | QD por disco | Disco de dados QD (preterido) | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bytes de leitura por disco/s | Bytes de leitura do disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operações de leitura por disco/s | Operações de leitura de disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bytes de gravação por disco/s | Bytes de gravação do disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operações de gravação por disco/s | Operações de gravação do disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Percentagem da CPU | Percentagem da CPU | Percentagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Cache do disco de dados Premium-acesso de leitura | Leitura do cache do disco de dados Premium (versão prévia) | Percentagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Erro de leitura do cache do disco de dados Premium | Cache do disco de dados Premium-erro de leitura (visualização) | Percentagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Cache de disco do sistema operacional Premium-acesso de leitura | Cache de disco do sistema operacional Premium-acesso de leitura (versão prévia) | Percentagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Erro de leitura do cache de disco do so premium | Erro de leitura do cache de disco do so premium (visualização) | Percentagem | Média
Sim | Microsoft.ContainerInstance/containerGroups | Os | Utilização da CPU | Contagem | Média
Sim | Microsoft.ContainerInstance/containerGroups | MemoryUsage | Utilização de Memória | Bytes | Média
Sim | Microsoft.ContainerInstance/containerGroups | NetworkBytesReceivedPerSecond | Bytes de rede recebidos por segundo | Bytes | Média
Sim | Microsoft.ContainerInstance/containerGroups | NetworkBytesTransmittedPerSecond | Bytes de rede transmitidos por segundo | Bytes | Média
Sim | Microsoft.ContainerRegistry/registries | RunDuration | Duração da execução | Milissegundos | Total
Sim | Microsoft.ContainerRegistry/registries | SuccessfulPullCount | Contagem de pull bem-sucedida | Contagem | Média
Sim | Microsoft.ContainerRegistry/registries | SuccessfulPushCount | Contagem de push bem-sucedida | Contagem | Média
Sim | Microsoft.ContainerRegistry/registries | TotalPullCount | Contagem total de pull | Contagem | Média
Sim | Microsoft.ContainerRegistry/registries | TotalPushCount | Contagem total de push | Contagem | Média
Não | Microsoft.ContainerService/managedClusters | kube_node_status_allocatable_cpu_cores | Número total de núcleos de CPU disponíveis em um cluster gerenciado | Contagem | Média
Não | Microsoft.ContainerService/managedClusters | kube_node_status_allocatable_memory_bytes | Quantidade total de memória disponível em um cluster gerenciado | Bytes | Média
Não | Microsoft.ContainerService/managedClusters | kube_node_status_condition | Status para várias condições de nó | Contagem | Média
Não | Microsoft.ContainerService/managedClusters | kube_pod_status_phase | Número de pods por fase | Contagem | Média
Não | Microsoft.ContainerService/managedClusters | kube_pod_status_ready | Número de pods no estado pronto | Contagem | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | AvailableCapacity | Capacidade disponível | Bytes | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | BytesUploadedToCloud | Bytes de nuvem carregados (dispositivo) | Bytes | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | BytesUploadedToCloudPerShare | Bytes de nuvem carregados (compartilhamento) | Bytes | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudReadThroughput | Taxa de transferência de download da nuvem | BytesPerSecond | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudReadThroughputPerShare | Taxa de transferência de download de nuvem (compartilhamento) | BytesPerSecond | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudUploadThroughput | Taxa de transferência de upload de nuvem | BytesPerSecond | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudUploadThroughputPerShare | Taxa de transferência de upload de nuvem (compartilhamento) | BytesPerSecond | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | HyperVMemoryUtilization | Computação de borda-uso de memória | Percentagem | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | HyperVVirtualProcessorUtilization | Computação de borda – percentual de CPU | Percentagem | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | NICReadThroughput | Taxa de transferência de leitura (rede) | BytesPerSecond | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | NICWriteThroughput | Taxa de transferência de gravação (rede) | BytesPerSecond | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | TotalCapacity | Capacidade Total | Bytes | Média
Sim | Microsoft.DataFactory/datafactories | FailedRuns | Execuções Falhadas | Contagem | Total
Sim | Microsoft.DataFactory/datafactories | SuccessfulRuns | Execuções com êxito | Contagem | Total
Sim | Microsoft.DataFactory/factories | ActivityCancelledRuns | Métricas de execuções de atividade canceladas | Contagem | Total
Sim | Microsoft.DataFactory/factories | ActivityFailedRuns | Métricas de execuções de atividade com falha | Contagem | Total
Sim | Microsoft.DataFactory/factories | ActivitySucceededRuns | Métricas de execuções de atividades bem-sucedidas | Contagem | Total
Sim | Microsoft.DataFactory/factories | FactorySizeInGbUnits | Tamanho total de fábrica (unidade GB) | Contagem | Máximo
Sim | Microsoft.DataFactory/factories | IntegrationRuntimeAvailableMemory | Memória disponível do Integration Runtime | Bytes | Média
Sim | Microsoft.DataFactory/factories | IntegrationRuntimeAverageTaskPickupDelay | Duração da fila do Integration Runtime | Segundos | Média
Sim | Microsoft.DataFactory/factories | IntegrationRuntimeCpuPercentage | Utilização da CPU do Integration Runtime | Percentagem | Média
Sim | Microsoft.DataFactory/factories | IntegrationRuntimeQueueLength | Comprimento da fila do Integration Runtime | Contagem | Média
Sim | Microsoft.DataFactory/factories | MaxAllowedFactorySizeInGbUnits | Tamanho máximo de fábrica permitido (unidade GB) | Contagem | Máximo
Sim | Microsoft.DataFactory/factories | MaxAllowedResourceCount | Contagem máxima de entidades permitidas | Contagem | Máximo
Sim | Microsoft.DataFactory/factories | PipelineCancelledRuns | Métricas de execuções de pipeline canceladas | Contagem | Total
Sim | Microsoft.DataFactory/factories | PipelineFailedRuns | Métricas de execuções de pipeline com falha | Contagem | Total
Sim | Microsoft.DataFactory/factories | PipelineSucceededRuns | Métricas de execuções de pipeline com êxito | Contagem | Total
Sim | Microsoft.DataFactory/factories | ResourceCount | Contagem total de entidades | Contagem | Máximo
Sim | Microsoft.DataFactory/factories | TriggerCancelledRuns | O gatilho cancelado executa métricas | Contagem | Total
Sim | Microsoft.DataFactory/factories | TriggerFailedRuns | Métricas de execuções de gatilho com falha | Contagem | Total
Sim | Microsoft.DataFactory/factories | TriggerSucceededRuns | Métricas de execuções de gatilho com êxito | Contagem | Total
Sim | Microsoft.DataLakeAnalytics/accounts | JobAUEndedCancelled | Hora da AU cancelada | Segundos | Total
Sim | Microsoft.DataLakeAnalytics/accounts | JobAUEndedFailure | Tempo de AU com falha | Segundos | Total
Sim | Microsoft.DataLakeAnalytics/accounts | JobAUEndedSuccess | Tempo de AU bem-sucedido | Segundos | Total
Sim | Microsoft.DataLakeAnalytics/accounts | JobEndedCancelled | Trabalhos cancelados | Contagem | Total
Sim | Microsoft.DataLakeAnalytics/accounts | JobEndedFailure | Trabalhos com falha | Contagem | Total
Sim | Microsoft.DataLakeAnalytics/accounts | JobEndedSuccess | Trabalhos com êxito | Contagem | Total
Sim | Microsoft.DataLakeStore/accounts | DataRead | Leitura de dados | Bytes | Total
Sim | Microsoft.DataLakeStore/accounts | Gravado em | Dados gravados | Bytes | Total
Sim | Microsoft.DataLakeStore/accounts | ReadRequests | Solicitações de leitura | Contagem | Total
Sim | Microsoft.DataLakeStore/accounts | TotalStorage | Armazenamento Total | Bytes | Máximo
Sim | Microsoft.DataLakeStore/accounts | WriteRequests | Solicitações de gravação | Contagem | Total
Sim | Microsoft.DBforMariaDB/servers | active_connections | Conexões ativas | Contagem | Média
Sim | Microsoft.DBforMariaDB/servers | backup_storage_used | Armazenamento de backup usado | Bytes | Média
Sim | Microsoft.DBforMariaDB/servers | connections_failed | Ligações com Falhas | Contagem | Total
Sim | Microsoft.DBforMariaDB/servers | cpu_percent | Percentual de CPU | Percentagem | Média
Sim | Microsoft.DBforMariaDB/servers | io_consumption_percent | Porcentagem de e/s | Percentagem | Média
Sim | Microsoft.DBforMariaDB/servers | memory_percent | Porcentagem de memória | Percentagem | Média
Sim | Microsoft.DBforMariaDB/servers | network_bytes_egress | Saída da Rede | Bytes | Total
Sim | Microsoft.DBforMariaDB/servers | network_bytes_ingress | Entrada na Rede | Bytes | Total
Sim | Microsoft.DBforMariaDB/servers | seconds_behind_master | Atraso de replicação em segundos | Contagem | Máximo
Sim | Microsoft.DBforMariaDB/servers | serverlog_storage_limit | Limite de armazenamento de log do servidor | Bytes | Média
Sim | Microsoft.DBforMariaDB/servers | serverlog_storage_percent | Porcentagem de armazenamento de log do servidor | Percentagem | Média
Sim | Microsoft.DBforMariaDB/servers | serverlog_storage_usage | Armazenamento de log do servidor usado | Bytes | Média
Sim | Microsoft.DBforMariaDB/servers | storage_limit | Limite de armazenamento | Bytes | Máximo
Sim | Microsoft.DBforMariaDB/servers | storage_percent | Porcentagem de armazenamento | Percentagem | Média
Sim | Microsoft.DBforMariaDB/servers | storage_used | Armazenamento utilizado | Bytes | Média
Sim | Microsoft.DBforMySQL/servers | active_connections | Conexões ativas | Contagem | Média
Sim | Microsoft.DBforMySQL/servers | backup_storage_used | Armazenamento de backup usado | Bytes | Média
Sim | Microsoft.DBforMySQL/servers | connections_failed | Ligações com Falhas | Contagem | Total
Sim | Microsoft.DBforMySQL/servers | cpu_percent | Percentual de CPU | Percentagem | Média
Sim | Microsoft.DBforMySQL/servers | io_consumption_percent | Porcentagem de e/s | Percentagem | Média
Sim | Microsoft.DBforMySQL/servers | memory_percent | Porcentagem de memória | Percentagem | Média
Sim | Microsoft.DBforMySQL/servers | network_bytes_egress | Saída da Rede | Bytes | Total
Sim | Microsoft.DBforMySQL/servers | network_bytes_ingress | Entrada na Rede | Bytes | Total
Sim | Microsoft.DBforMySQL/servers | seconds_behind_master | Atraso de replicação em segundos | Contagem | Máximo
Sim | Microsoft.DBforMySQL/servers | serverlog_storage_limit | Limite de armazenamento de log do servidor | Bytes | Máximo
Sim | Microsoft.DBforMySQL/servers | serverlog_storage_percent | Porcentagem de armazenamento de log do servidor | Percentagem | Média
Sim | Microsoft.DBforMySQL/servers | serverlog_storage_usage | Armazenamento de log do servidor usado | Bytes | Média
Sim | Microsoft.DBforMySQL/servers | storage_limit | Limite de armazenamento | Bytes | Máximo
Sim | Microsoft.DBforMySQL/servers | storage_percent | Porcentagem de armazenamento | Percentagem | Média
Sim | Microsoft.DBforMySQL/servers | storage_used | Armazenamento utilizado | Bytes | Média
Sim | Microsoft.DBforPostgreSQL/servers | active_connections | Conexões ativas | Contagem | Média
Sim | Microsoft.DBforPostgreSQL/servers | backup_storage_used | Armazenamento de backup usado | Bytes | Média
Sim | Microsoft.DBforPostgreSQL/servers | connections_failed | Ligações com Falhas | Contagem | Total
Sim | Microsoft.DBforPostgreSQL/servers | cpu_percent | Percentual de CPU | Percentagem | Média
Sim | Microsoft.DBforPostgreSQL/servers | io_consumption_percent | Porcentagem de e/s | Percentagem | Média
Sim | Microsoft.DBforPostgreSQL/servers | memory_percent | Porcentagem de memória | Percentagem | Média
Sim | Microsoft.DBforPostgreSQL/servers | network_bytes_egress | Saída da Rede | Bytes | Total
Sim | Microsoft.DBforPostgreSQL/servers | network_bytes_ingress | Entrada na Rede | Bytes | Total
Sim | Microsoft.DBforPostgreSQL/servers | pg_replica_log_delay_in_bytes | Atraso máximo entre réplicas | Bytes | Máximo
Sim | Microsoft.DBforPostgreSQL/servers | pg_replica_log_delay_in_seconds | Atraso de réplica | Segundos | Máximo
Sim | Microsoft.DBforPostgreSQL/servers | serverlog_storage_limit | Limite de armazenamento de log do servidor | Bytes | Máximo
Sim | Microsoft.DBforPostgreSQL/servers | serverlog_storage_percent | Porcentagem de armazenamento de log do servidor | Percentagem | Média
Sim | Microsoft.DBforPostgreSQL/servers | serverlog_storage_usage | Armazenamento de log do servidor usado | Bytes | Média
Sim | Microsoft.DBforPostgreSQL/servers | storage_limit | Limite de armazenamento | Bytes | Máximo
Sim | Microsoft.DBforPostgreSQL/servers | storage_percent | Porcentagem de armazenamento | Percentagem | Média
Sim | Microsoft.DBforPostgreSQL/servers | storage_used | Armazenamento utilizado | Bytes | Média
Sim | Microsoft.DBforPostgreSQL/serversv2 | active_connections | Conexões ativas | Contagem | Média
Sim | Microsoft.DBforPostgreSQL/serversv2 | cpu_percent | Percentual de CPU | Percentagem | Média
Sim | Microsoft.DBforPostgreSQL/serversv2 | IOPS | IOPS | Contagem | Média
Sim | Microsoft.DBforPostgreSQL/serversv2 | memory_percent | Porcentagem de memória | Percentagem | Média
Sim | Microsoft.DBforPostgreSQL/serversv2 | network_bytes_egress | Saída da Rede | Bytes | Total
Sim | Microsoft.DBforPostgreSQL/serversv2 | network_bytes_ingress | Entrada na Rede | Bytes | Total
Sim | Microsoft.DBforPostgreSQL/serversv2 | storage_percent | Porcentagem de armazenamento | Percentagem | Média
Sim | Microsoft.DBforPostgreSQL/serversv2 | storage_used | Armazenamento utilizado | Bytes | Média
Sim | Microsoft. Devices/conta | digitaltwins. telemetria. nós | Espaço reservado para telemetria do nó digital gêmeos | Contagem | Total
Sim | Microsoft.Devices/IotHubs | c2d.commands.egress.abandon.success | C2D mensagens abandonadas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | c2d.commands.egress.complete.success | Entregas de mensagem C2D concluídas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | c2d.commands.egress.reject.success | Mensagens C2D rejeitadas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | c2d.methods.failure | Invocações de método direto com falhas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | c2d.methods.requestSize | Pedir tamanho de invocações de método direto | Bytes | Média
Sim | Microsoft.Devices/IotHubs | c2d.methods.responseSize | Tamanho da resposta de invocações de método direto | Bytes | Média
Sim | Microsoft.Devices/IotHubs | C2D. Methods. Success | Invocações de método diretos bem-sucedidas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | c2d.twin.read.failure | Leituras duplas com falhas de back-end | Contagem | Total
Sim | Microsoft.Devices/IotHubs | c2d.twin.read.size | Tamanho da resposta de leituras duplas de back-end | Bytes | Média
Sim | Microsoft.Devices/IotHubs | c2d.twin.read.success | Leituras de cópia com êxito do back-end | Contagem | Total
Sim | Microsoft.Devices/IotHubs | c2d.twin.update.failure | Atualizações duplas com falhas do back-end | Contagem | Total
Sim | Microsoft.Devices/IotHubs | c2d.twin.update.size | Tamanho das atualizações de cópia do back-end | Bytes | Média
Sim | Microsoft.Devices/IotHubs | c2d.twin.update.success | Atualizações duplas com êxito do back-end | Contagem | Total
Sim | Microsoft.Devices/IotHubs | C2DMessagesExpired | C2D mensagens expiradas (visualização) | Contagem | Total
Sim | Microsoft.Devices/IotHubs | Figura | Métricas de Configuração | Contagem | Total
Não | Microsoft.Devices/IotHubs | connectedDeviceCount | Dispositivos ligados (pré-visualização) | Contagem | Média
Sim | Microsoft.Devices/IotHubs | d2c.endpoints.egress.builtIn.events | Roteamento: mensagens entregues a mensagens/eventos | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.endpoints.egress.eventHubs | Roteamento: mensagens entregues ao Hub de eventos | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusQueues | Roteamento: mensagens entregues à fila do barramento de serviço | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusTopics | Roteamento: mensagens entregues ao tópico do barramento de serviço | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage | Roteamento: mensagens entregues ao armazenamento | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.blobs | Encaminhamento: blobs entregues ao armazenamento | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.bytes | Encaminhamento: dados entregues ao armazenamento | Bytes | Total
Sim | Microsoft.Devices/IotHubs | d2c.endpoints.latency.builtIn.events | Roteamento: latência de mensagem para mensagens/eventos | Milissegundos | Média
Sim | Microsoft.Devices/IotHubs | d2c.endpoints.latency.eventHubs | Encaminhamento: latência de mensagem para o Hub de Eventos | Milissegundos | Média
Sim | Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusQueues | Encaminhamento: latência de mensagem para a Fila do Service Bus | Milissegundos | Média
Sim | Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusTopics | Roteamento: latência de mensagem para o tópico do barramento de serviço | Milissegundos | Média
Sim | Microsoft.Devices/IotHubs | d2c.endpoints.latency.storage | Roteamento: latência de mensagem para armazenamento | Milissegundos | Média
Sim | Microsoft.Devices/IotHubs | d2c.telemetry.egress.dropped | Roteamento: mensagens de telemetria eliminadas  | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.telemetry.egress.fallback | Roteamento: mensagens entregues ao fallback | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.telemetry.egress.invalid | Roteamento: mensagens de telemetria incompatíveis | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.telemetry.egress.orphaned | Roteamento: mensagens de telemetria órfãs  | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.telemetry.egress.success | Roteamento: mensagens de telemetria entregues | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.allProtocol | Tentativas de envio de mensagem de telemetria | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.sendThrottle | Número de erros de limitação | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.success | Mensagens de telemetria enviadas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.twin.read.failure | Leituras duplas com falhas de dispositivos | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.twin.read.size | Tamanho da resposta de leituras duplas de dispositivos | Bytes | Média
Sim | Microsoft.Devices/IotHubs | d2c.twin.read.success | Leituras de entrelaçamento bem-sucedidas de dispositivos | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.twin.update.failure | Atualizações duplas com falhas dos dispositivos | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.twin.update.size | Tamanho de atualizações de atualização de papel dos dispositivos | Bytes | Média
Sim | Microsoft.Devices/IotHubs | d2c.twin.update.success | Atualizações de atualização com êxito de dispositivos | Contagem | Total
Sim | Microsoft.Devices/IotHubs | dailyMessageQuotaUsed | Número total de mensagens usadas | Contagem | Média
Sim | Microsoft.Devices/IotHubs | deviceDataUsage | Uso total de dados do dispositivo | Bytes | Total
Sim | Microsoft.Devices/IotHubs | deviceDataUsageV2 | Uso total de dados do dispositivo (versão prévia) | Bytes | Total
Sim | Microsoft.Devices/IotHubs | devices.connectedDevices.allProtocol | Dispositivos ligados (preteridos)  | Contagem | Total
Sim | Microsoft.Devices/IotHubs | devices.totalDevices | Total de dispositivos (preteridos) | Contagem | Total
Sim | Microsoft.Devices/IotHubs | EventGridDeliveries | Entregas da grade de eventos (visualização) | Contagem | Total
Sim | Microsoft.Devices/IotHubs | EventGridLatency | Latência da grade de eventos (versão prévia) | Milissegundos | Média
Sim | Microsoft.Devices/IotHubs | jobs.cancelJob.failure | Cancelamentos de tarefas com falhas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | jobs.cancelJob.success | Cancelamentos de trabalho bem-sucedidos | Contagem | Total
Sim | Microsoft.Devices/IotHubs | jobs.completed | Tarefas concluídas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.failure | Criações com falhas de tarefas de invocação de método | Contagem | Total
Sim | Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.success | Criações de trabalhos de invocação de método com êxito | Contagem | Total
Sim | Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.failure | Criações com falhas de tarefas de atualização duplas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.success | Criações bem-sucedidas de trabalhos de atualização de entrelaçamento | Contagem | Total
Sim | Microsoft.Devices/IotHubs | jobs.failed | Tarefas com falhas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | jobs.listJobs.failure | Chamadas com falhas para listar tarefas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | jobs.listJobs.success | Chamadas com êxito para listar trabalhos | Contagem | Total
Sim | Microsoft.Devices/IotHubs | jobs.queryJobs.failure | Consultas de tarefa com falhas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | jobs.queryJobs.success | Consultas de trabalho com êxito | Contagem | Total
Não | Microsoft.Devices/IotHubs | totalDeviceCount | Total de dispositivos (visualização) | Contagem | Média
Sim | Microsoft.Devices/IotHubs | twinQueries.failure | Consultas duplas com falhas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | twinQueries.resultSize | Tamanho do resultado de consultas de entrelaçamento | Bytes | Média
Sim | Microsoft.Devices/IotHubs | twinQueries.success | Consultas de entrelaçamento bem-sucedidas | Contagem | Total
Sim | Microsoft.Devices/provisioningServices | AttestationAttempts | Tentativas de atestado | Contagem | Total
Sim | Microsoft.Devices/provisioningServices | DeviceAssignments | Dispositivos atribuídos | Contagem | Total
Sim | Microsoft.Devices/provisioningServices | RegistrationAttempts | Tentativas de registro | Contagem | Total
Não | Microsoft.DocumentDB/databaseAccounts | AvailableStorage | Armazenamento Disponível | Bytes | Total
Não | Microsoft.DocumentDB/databaseAccounts | CassandraConnectionClosures | Fechamentos de conexão do Cassandra | Contagem | Total
Não | Microsoft.DocumentDB/databaseAccounts | CassandraRequestCharges | Encargos de solicitação do Cassandra | Contagem | Total
Não | Microsoft.DocumentDB/databaseAccounts | CassandraRequests | Solicitações Cassandra | Contagem | Contagem
Não | Microsoft.DocumentDB/databaseAccounts | DataUsage | Utilização de Dados | Bytes | Total
Sim | Microsoft.DocumentDB/databaseAccounts | DeleteVirtualNetwork | DeleteVirtualNetwork | Contagem | Contagem
Não | Microsoft.DocumentDB/databaseAccounts | DocumentCount | Contagem de documentos | Contagem | Total
Não | Microsoft.DocumentDB/databaseAccounts | DocumentQuota | Cota de documentos | Bytes | Total
Não | Microsoft.DocumentDB/databaseAccounts | IndexUsage | Uso do índice | Bytes | Total
Não | Microsoft.DocumentDB/databaseAccounts | MetadataRequests | Solicitações de metadados | Contagem | Contagem
Sim | Microsoft.DocumentDB/databaseAccounts | MongoRequestCharge | Encargo de solicitação do Mongo | Contagem | Total
Sim | Microsoft.DocumentDB/databaseAccounts | MongoRequests | Solicitações Mongo | Contagem | Contagem
Não | Microsoft.DocumentDB/databaseAccounts | MongoRequestsCount | Taxa de solicitação de Mongo | CountPerSecond | Média
Não | Microsoft.DocumentDB/databaseAccounts | MongoRequestsDelete | Taxa de solicitação de exclusão de Mongo | CountPerSecond | Média
Não | Microsoft.DocumentDB/databaseAccounts | MongoRequestsInsert | Taxa de solicitação de inserção de Mongo | CountPerSecond | Média
Não | Microsoft.DocumentDB/databaseAccounts | MongoRequestsQuery | Taxa de solicitação de consulta do Mongo | CountPerSecond | Média
Não | Microsoft.DocumentDB/databaseAccounts | MongoRequestsUpdate | Taxa de solicitação de atualização do Mongo | CountPerSecond | Média
Não | Microsoft.DocumentDB/databaseAccounts | ProvisionedThroughput | Débito Aprovisionado | Contagem | Máximo
Sim | Microsoft.DocumentDB/databaseAccounts | ReplicationLatency | Latência de replicação P99 | Milissegundos | Média
Não | Microsoft.DocumentDB/databaseAccounts | Indisponibilidade | Disponibilidade do serviço | Percentagem | Média
Sim | Microsoft.DocumentDB/databaseAccounts | TotalPedidos | Total de Pedidos | Contagem | Contagem
Sim | Microsoft.DocumentDB/databaseAccounts | TotalRequestUnits | Total de unidades de solicitação | Contagem | Total
Não | Microsoft. EnterpriseKnowledgeGraph/serviços | FailureCount | Contagem de Falhas | Contagem | Contagem
Não | Microsoft. EnterpriseKnowledgeGraph/serviços | SuccessCount | Contagens com Êxito | Contagem | Contagem
Não | Microsoft. EnterpriseKnowledgeGraph/serviços | SuccessLatency | Latência de êxito | Milissegundos | Média
Não | Microsoft. EnterpriseKnowledgeGraph/serviços | TransactionCount | Contagem de transações | Contagem | Contagem
Sim | Microsoft.EventGrid/domains | DeadLetteredCount | Eventos inativos | Contagem | Total
Não | Microsoft.EventGrid/domains | DeliveryAttemptFailCount | Eventos com falha na entrega | Contagem | Total
Sim | Microsoft.EventGrid/domains | DeliverySuccessCount | Eventos entregues | Contagem | Total
Não | Microsoft.EventGrid/domains | DestinationProcessingDurationInMs | Duração do processamento de destino | Milissegundos | Média
Sim | Microsoft.EventGrid/domains | DroppedEventCount | Eventos ignorados | Contagem | Total
Sim | Microsoft.EventGrid/domains | MatchedEventCount | Eventos correspondentes | Contagem | Total
Sim | Microsoft.EventGrid/domains | PublishFailCount | Publicar eventos com falha | Contagem | Total
Sim | Microsoft.EventGrid/domains | PublishSuccessCount | Eventos publicados | Contagem | Total
Sim | Microsoft.EventGrid/domains | PublishSuccessLatencyInMs | Latência de êxito na publicação | Contagem | Total
Sim | Microsoft.EventGrid/eventSubscriptions | DeadLetteredCount | Eventos inativos | Contagem | Total
Não | Microsoft.EventGrid/eventSubscriptions | DeliveryAttemptFailCount | Eventos com falha na entrega | Contagem | Total
Sim | Microsoft.EventGrid/eventSubscriptions | DeliverySuccessCount | Eventos entregues | Contagem | Total
Não | Microsoft.EventGrid/eventSubscriptions | DestinationProcessingDurationInMs | Duração do processamento de destino | Milissegundos | Média
Sim | Microsoft.EventGrid/eventSubscriptions | DroppedEventCount | Eventos ignorados | Contagem | Total
Sim | Microsoft.EventGrid/eventSubscriptions | MatchedEventCount | Eventos correspondentes | Contagem | Total
Sim | Microsoft.EventGrid/extensionTopics | PublishFailCount | Publicar eventos com falha | Contagem | Total
Sim | Microsoft.EventGrid/extensionTopics | PublishSuccessCount | Eventos publicados | Contagem | Total
Sim | Microsoft.EventGrid/extensionTopics | PublishSuccessLatencyInMs | Latência de êxito na publicação | Contagem | Total
Sim | Microsoft.EventGrid/extensionTopics | UnmatchedEventCount | Eventos sem correspondência | Contagem | Total
Sim | Microsoft.EventGrid/topics | PublishFailCount | Publicar eventos com falha | Contagem | Total
Sim | Microsoft.EventGrid/topics | PublishSuccessCount | Eventos publicados | Contagem | Total
Sim | Microsoft.EventGrid/topics | PublishSuccessLatencyInMs | Latência de êxito na publicação | Contagem | Total
Sim | Microsoft.EventGrid/topics | UnmatchedEventCount | Eventos sem correspondência | Contagem | Total
Não | Microsoft.EventHub/clusters | ActiveConnections | ActiveConnections | Contagem | Média
Não | Microsoft.EventHub/clusters | AvailableMemory | Memória Disponível | Percentagem | Máximo
Não | Microsoft.EventHub/clusters | CaptureBacklog | Capturar registro posterior. | Contagem | Total
Não | Microsoft.EventHub/clusters | CapturedBytes | Bytes capturados. | Bytes | Total
Não | Microsoft.EventHub/clusters | CapturedMessages | Mensagens capturadas. | Contagem | Total
Não | Microsoft.EventHub/clusters | ConnectionsClosed | Conexões fechadas. | Contagem | Média
Não | Microsoft.EventHub/clusters | ConnectionsOpened | Conexões abertas. | Contagem | Média
Não | Microsoft.EventHub/clusters | CPU | CPU | Percentagem | Máximo
Sim | Microsoft.EventHub/clusters | IncomingBytes | Bytes de entrada. | Bytes | Total
Sim | Microsoft.EventHub/clusters | IncomingMessages | Mensagens de entrada | Contagem | Total
Sim | Microsoft.EventHub/clusters | IncomingRequests | Solicitações de entrada | Contagem | Total
Sim | Microsoft.EventHub/clusters | OutgoingBytes | Bytes de saída. | Bytes | Total
Sim | Microsoft.EventHub/clusters | OutgoingMessages | Mensagens de saída | Contagem | Total
Não | Microsoft.EventHub/clusters | QuotaExceededErrors | A cota excedeu erros. | Contagem | Total
Não | Microsoft.EventHub/clusters | ServerErrors | Erros do servidor. | Contagem | Total
Não | Microsoft.EventHub/clusters | SuccessfulRequests | Solicitações bem-sucedidas | Contagem | Total
Não | Microsoft.EventHub/clusters | ThrottledRequests | Solicitações limitadas. | Contagem | Total
Não | Microsoft.EventHub/clusters | UserErrors | Erros do usuário. | Contagem | Total
Não | Microsoft.EventHub/namespaces | ActiveConnections | ActiveConnections | Contagem | Média
Não | Microsoft.EventHub/namespaces | CaptureBacklog | Capturar registro posterior. | Contagem | Total
Não | Microsoft.EventHub/namespaces | CapturedBytes | Bytes capturados. | Bytes | Total
Não | Microsoft.EventHub/namespaces | CapturedMessages | Mensagens capturadas. | Contagem | Total
Não | Microsoft.EventHub/namespaces | ConnectionsClosed | Conexões fechadas. | Contagem | Média
Não | Microsoft.EventHub/namespaces | ConnectionsOpened | Conexões abertas. | Contagem | Média
Sim | Microsoft.EventHub/namespaces | EHABL | Arquivar mensagens de pendências (preterido) | Contagem | Total
Sim | Microsoft.EventHub/namespaces | EHAMBS | Taxa de transferência de mensagem de arquivamento (preterido) | Bytes | Total
Sim | Microsoft.EventHub/namespaces | EHAMSGS | Arquivar mensagens (preterido) | Contagem | Total
Sim | Microsoft.EventHub/namespaces | EHINBYTES | Bytes de entrada (preterido) | Bytes | Total
Sim | Microsoft.EventHub/namespaces | EHINMBS | Bytes de entrada (obsoletos) (preterido) | Bytes | Total
Sim | Microsoft.EventHub/namespaces | EHINMSGS | Mensagens de entrada (preteridas) | Contagem | Total
Sim | Microsoft.EventHub/namespaces | EHOUTBYTES | Bytes de saída (preteridos) | Bytes | Total
Sim | Microsoft.EventHub/namespaces | EHOUTMBS | Bytes de saída (obsoletos) (preterido) | Bytes | Total
Sim | Microsoft.EventHub/namespaces | EHOUTMSGS | Mensagens de saída (preteridas) | Contagem | Total
Sim | Microsoft.EventHub/namespaces | FAILREQ | Solicitações com falha (preteridas) | Contagem | Total
Sim | Microsoft.EventHub/namespaces | IncomingBytes | Bytes de entrada. | Bytes | Total
Sim | Microsoft.EventHub/namespaces | IncomingMessages | Mensagens de entrada | Contagem | Total
Sim | Microsoft.EventHub/namespaces | IncomingRequests | Solicitações de entrada | Contagem | Total
Sim | Microsoft.EventHub/namespaces | INMSGS | Mensagens de entrada (obsoletas) (preteridas) | Contagem | Total
Sim | Microsoft.EventHub/namespaces | INREQS | Solicitações de entrada (preteridas) | Contagem | Total
Sim | Microsoft.EventHub/namespaces | INTERR | Erros de servidor interno (preterido) | Contagem | Total
Sim | Microsoft.EventHub/namespaces | MISCERR | Outros erros (preterido) | Contagem | Total
Sim | Microsoft.EventHub/namespaces | OutgoingBytes | Bytes de saída. | Bytes | Total
Sim | Microsoft.EventHub/namespaces | OutgoingMessages | Mensagens de saída | Contagem | Total
Sim | Microsoft.EventHub/namespaces | Mensagens inenviadas | Mensagens de saída (obsoletas) (preteridas) | Contagem | Total
Não | Microsoft.EventHub/namespaces | QuotaExceededErrors | A cota excedeu erros. | Contagem | Total
Não | Microsoft.EventHub/namespaces | ServerErrors | Erros do servidor. | Contagem | Total
Não | Microsoft.EventHub/namespaces | Tamanho | Tamanho | Bytes | Média
Não | Microsoft.EventHub/namespaces | SuccessfulRequests | Solicitações bem-sucedidas | Contagem | Total
Sim | Microsoft.EventHub/namespaces | SUCCREQ | Solicitações com êxito (preteridas) | Contagem | Total
Sim | Microsoft.EventHub/namespaces | SVRBSY | Erros do servidor ocupado (preterido) | Contagem | Total
Não | Microsoft.EventHub/namespaces | ThrottledRequests | Solicitações limitadas. | Contagem | Total
Não | Microsoft.EventHub/namespaces | UserErrors | Erros do usuário. | Contagem | Total
Sim | Microsoft. HDInsight/clusters | CategorizedGatewayRequests | Solicitações de gateway categorizadas | Contagem | Total
Sim | Microsoft. HDInsight/clusters | GatewayRequests | Solicitações de gateway | Contagem | Total
Sim | Microsoft. HDInsight/clusters | NumActiveWorkers | Número de trabalhadores ativos | Contagem | Máximo
Sim | Microsoft. HDInsight/clusters | ScalingRequests | Solicitações de dimensionamento | Contagem | Máximo
Sim | Microsoft.Insights/AutoscaleSettings | MetricThreshold | Limite de métrica | Contagem | Média
Sim | Microsoft.Insights/AutoscaleSettings | ObservedCapacity | Capacidade observada | Contagem | Média
Sim | Microsoft.Insights/AutoscaleSettings | ObservedMetricValue | Valor de métrica observado | Contagem | Média
Sim | Microsoft.Insights/AutoscaleSettings | ScaleActionsInitiated | Ações de escala iniciadas | Contagem | Total
Sim | Microsoft.Insights/Components | availabilityResults/availabilityPercentage | Disponibilidade | Percentagem | Média
Não | Microsoft.Insights/Components | availabilityResults/contagem | Testes de disponibilidade | Contagem | Contagem
Sim | Microsoft.Insights/Components | availabilityResults/duration | Duração do teste de disponibilidade | Milissegundos | Média
Sim | Microsoft.Insights/Components | browserTimings/networkDuration | Tempo de conexão de rede de carregamento de página | Milissegundos | Média
Sim | Microsoft.Insights/Components | browserTimings/processingDuration | Tempo de processamento do cliente | Milissegundos | Média
Sim | Microsoft.Insights/Components | browserTimings/receiveDuration | Tempo de resposta de recebimento | Milissegundos | Média
Sim | Microsoft.Insights/Components | browserTimings/sendDuration | Enviar tempo de solicitação | Milissegundos | Média
Sim | Microsoft.Insights/Components | browserTimings/totalDuration | Tempo de carregamento da página do navegador | Milissegundos | Média
Não | Microsoft.Insights/Components | dependências/contagem | Chamadas de dependência | Contagem | Contagem
Sim | Microsoft.Insights/Components | dependências/duração | Duração da dependência | Milissegundos | Média
Não | Microsoft.Insights/Components | dependências/com falha | Falhas de chamada de dependência | Contagem | Contagem
Não | Microsoft.Insights/Components | exceções/navegador | Exceções de browser | Contagem | Contagem
Sim | Microsoft.Insights/Components | exceções/contagem | Exceções | Contagem | Contagem
Não | Microsoft.Insights/Components | exceções/servidor | Exceções de servidor | Contagem | Contagem
Sim | Microsoft.Insights/Components | pageViews/contagem | Exibições de página | Contagem | Contagem
Sim | Microsoft.Insights/Components | pageViews/duração | Tempo de carregamento da exibição de página | Milissegundos | Média
Sim | Microsoft.Insights/Components | performanceCounters/exceptionsPerSecond | Taxa de exceção | CountPerSecond | Média
Sim | Microsoft.Insights/Components | performanceCounters/memoryAvailableBytes | Memória disponível | Bytes | Média
Sim | Microsoft.Insights/Components | performanceCounters/processCpuPercentage | CPU do processo | Percentagem | Média
Sim | Microsoft.Insights/Components | performanceCounters/processIOBytesPerSecond | Taxa de e/s de processo | BytesPerSecond | Média
Sim | Microsoft.Insights/Components | performanceCounters/processorCpuPercentage | Tempo do processador | Percentagem | Média
Sim | Microsoft.Insights/Components | performanceCounters/processPrivateBytes | Processar bytes particulares | Bytes | Média
Sim | Microsoft.Insights/Components | performanceCounters/requestExecutionTime | Tempo de execução da solicitação HTTP | Milissegundos | Média
Sim | Microsoft.Insights/Components | performanceCounters/requestsInQueue | Solicitações HTTP na fila do aplicativo | Contagem | Média
Sim | Microsoft.Insights/Components | performanceCounters/requestsPerSecond | Taxa de solicitação HTTP | CountPerSecond | Média
Não | Microsoft.Insights/Components | solicitações/contagem | Solicitações do servidor | Contagem | Contagem
Sim | Microsoft.Insights/Components | solicitações/duração | Tempo de resposta do servidor | Milissegundos | Média
Não | Microsoft.Insights/Components | solicitações/com falha | Pedidos falhados | Contagem | Contagem
Não | Microsoft.Insights/Components | solicitações/taxa | Taxa de solicitações do servidor | CountPerSecond | Média
Sim | Microsoft.Insights/Components | rastreamentos/contagem | Rastreios | Contagem | Contagem
Sim | Microsoft.KeyVault/vaults | ServiceApiHit | Total de acertos da API de serviço | Contagem | Contagem
Sim | Microsoft.KeyVault/vaults | ServiceApiLatency | Latência geral da API de serviço | Milissegundos | Média
Sim | Microsoft.KeyVault/vaults | ServiceApiResult | Total de resultados da API de serviço | Contagem | Contagem
Sim | Microsoft. Kusto/clusters | CacheUtilization | Utilização de cache | Percentagem | Média
Sim | Microsoft. Kusto/clusters | ContinuousExportMaxLatenessMinutes | Minutos de atraso máximo de exportação contínua | Contagem | Máximo
Sim | Microsoft. Kusto/clusters | ContinuousExportNumOfRecordsExported | Exportação contínua-num de registros exportados | Contagem | Total
Sim | Microsoft. Kusto/clusters | ContinuousExportPendingCount | Contagem de exportação contínua pendente | Contagem | Máximo
Sim | Microsoft. Kusto/clusters | ContinuousExportResult | Resultado da exportação contínua | Contagem | Contagem
Sim | Microsoft. Kusto/clusters | CPU | CPU | Percentagem | Média
Sim | Microsoft. Kusto/clusters | EventsProcessedForEventHubs | Eventos processados (para hubs de evento/IoT) | Contagem | Total
Sim | Microsoft. Kusto/clusters | ExportUtilization | Utilização da exportação | Percentagem | Máximo
Sim | Microsoft. Kusto/clusters | IngestionLatencyInSeconds | Latência de ingestão (em segundos) | Segundos | Média
Sim | Microsoft. Kusto/clusters | IngestionResult | Resultado da ingestão | Contagem | Contagem
Sim | Microsoft. Kusto/clusters | IngestionUtilization | Utilização de ingestão | Percentagem | Média
Sim | Microsoft. Kusto/clusters | IngestionVolumeInMB | Volume de ingestão (em MB) | Contagem | Total
Sim | Microsoft. Kusto/clusters | KeepAlive | Keep Alive | Contagem | Média
Sim | Microsoft. Kusto/clusters | QueryDuration | Duração da consulta | Milissegundos | Média
Sim | Microsoft. Kusto/clusters | SteamingIngestRequestRate | Taxa de solicitação de ingestão de streaming | Contagem | RateRequestsPerSecond
Sim | Microsoft. Kusto/clusters | StreamingIngestDataRate | Taxa de dados de ingestão de streaming | Contagem | Média
Sim | Microsoft. Kusto/clusters | StreamingIngestDuration | Duração da ingestão de streaming | Milissegundos | Média
Sim | Microsoft. Kusto/clusters | StreamingIngestResults | Resultado de ingestão de streaming | Contagem | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | ActionLatency | Latência da ação  | Segundos | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | ActionsCompleted | Ações concluídas  | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | ActionsFailed | Ações com falha  | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | ActionsSkipped | Ações ignoradas  | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | ActionsStarted | Ações iniciadas  | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | ActionsSucceeded | Ações com êxito  | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | ActionSuccessLatency | Latência de êxito da ação  | Segundos | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | ActionThrottledEvents | Eventos com restrição de ação | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentConnectorMemoryUsage | Uso de memória do conector para Ambiente de Serviço de Integração | Percentagem | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentConnectorProcessorUsage | Uso do processador do conector para Ambiente de Serviço de Integração | Percentagem | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentWorkflowMemoryUsage | Uso de memória do fluxo de trabalho para Ambiente de Serviço de Integração | Percentagem | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentWorkflowProcessorUsage | Uso do processador de fluxo de trabalho para Ambiente de Serviço de Integração | Percentagem | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | RunFailurePercentage | Percentual de falha de execução | Percentagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | RunLatency | Latência de execução | Segundos | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | RunsCancelled | Execuções canceladas | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | RunsCompleted | Execuções concluídas | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | RunsFailed | Execuções com falha | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | RunsStarted | Execuções iniciadas | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | RunsSucceeded | Execuções com êxito | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | RunStartThrottledEvents | Executar eventos restritos de início | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | RunSuccessLatency | Latência de execução bem-sucedida | Segundos | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | RunThrottledEvents | Executar eventos limitados | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | TriggerFireLatency | Latência de disparo de gatilho  | Segundos | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | TriggerLatency | Latência de gatilho  | Segundos | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | TriggersCompleted | Gatilhos concluídos  | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | TriggersFailed | Gatilhos com falha  | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | TriggersFired | Gatilhos acionados  | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | TriggersSkipped | Gatilhos ignorados | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | TriggersStarted | Gatilhos iniciados  | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | TriggersSucceeded | Gatilhos com êxito  | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | TriggerSuccessLatency | Latência de êxito do gatilho  | Segundos | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | TriggerThrottledEvents | Disparar eventos restringidos | Contagem | Total
Sim | Microsoft.Logic/workflows | ActionLatency | Latência da ação  | Segundos | Média
Sim | Microsoft.Logic/workflows | ActionsCompleted | Ações concluídas  | Contagem | Total
Sim | Microsoft.Logic/workflows | ActionsFailed | Ações com falha  | Contagem | Total
Sim | Microsoft.Logic/workflows | ActionsSkipped | Ações ignoradas  | Contagem | Total
Sim | Microsoft.Logic/workflows | ActionsStarted | Ações iniciadas  | Contagem | Total
Sim | Microsoft.Logic/workflows | ActionsSucceeded | Ações com êxito  | Contagem | Total
Sim | Microsoft.Logic/workflows | ActionSuccessLatency | Latência de êxito da ação  | Segundos | Média
Sim | Microsoft.Logic/workflows | ActionThrottledEvents | Eventos com restrição de ação | Contagem | Total
Sim | Microsoft.Logic/workflows | BillableActionExecutions | Execuções de ações faturáveis | Contagem | Total
Sim | Microsoft.Logic/workflows | BillableTriggerExecutions | Execuções de gatilho Faturável | Contagem | Total
Sim | Microsoft.Logic/workflows | BillingUsageNativeOperation | Uso de cobrança para execuções de operação nativa | Contagem | Total
Sim | Microsoft.Logic/workflows | BillingUsageNativeOperation | Uso de cobrança para execuções de operação nativa | Contagem | Total
Sim | Microsoft.Logic/workflows | BillingUsageStandardConnector | Uso de cobrança para execuções de conector padrão | Contagem | Total
Sim | Microsoft.Logic/workflows | BillingUsageStandardConnector | Uso de cobrança para execuções de conector padrão | Contagem | Total
Sim | Microsoft.Logic/workflows | BillingUsageStorageConsumption | Uso de cobrança para execuções de consumo de armazenamento | Contagem | Total
Sim | Microsoft.Logic/workflows | BillingUsageStorageConsumption | Uso de cobrança para execuções de consumo de armazenamento | Contagem | Total
Sim | Microsoft.Logic/workflows | RunFailurePercentage | Percentual de falha de execução | Percentagem | Total
Sim | Microsoft.Logic/workflows | RunLatency | Latência de execução | Segundos | Média
Sim | Microsoft.Logic/workflows | RunsCancelled | Execuções canceladas | Contagem | Total
Sim | Microsoft.Logic/workflows | RunsCompleted | Execuções concluídas | Contagem | Total
Sim | Microsoft.Logic/workflows | RunsFailed | Execuções com falha | Contagem | Total
Sim | Microsoft.Logic/workflows | RunsStarted | Execuções iniciadas | Contagem | Total
Sim | Microsoft.Logic/workflows | RunsSucceeded | Execuções com êxito | Contagem | Total
Sim | Microsoft.Logic/workflows | RunStartThrottledEvents | Executar eventos restritos de início | Contagem | Total
Sim | Microsoft.Logic/workflows | RunSuccessLatency | Latência de execução bem-sucedida | Segundos | Média
Sim | Microsoft.Logic/workflows | RunThrottledEvents | Executar eventos limitados | Contagem | Total
Sim | Microsoft.Logic/workflows | TotalBillableExecutions | Total de execuções faturáveis | Contagem | Total
Sim | Microsoft.Logic/workflows | TriggerFireLatency | Latência de disparo de gatilho  | Segundos | Média
Sim | Microsoft.Logic/workflows | TriggerLatency | Latência de gatilho  | Segundos | Média
Sim | Microsoft.Logic/workflows | TriggersCompleted | Gatilhos concluídos  | Contagem | Total
Sim | Microsoft.Logic/workflows | TriggersFailed | Gatilhos com falha  | Contagem | Total
Sim | Microsoft.Logic/workflows | TriggersFired | Gatilhos acionados  | Contagem | Total
Sim | Microsoft.Logic/workflows | TriggersSkipped | Gatilhos ignorados | Contagem | Total
Sim | Microsoft.Logic/workflows | TriggersStarted | Gatilhos iniciados  | Contagem | Total
Sim | Microsoft.Logic/workflows | TriggersSucceeded | Gatilhos com êxito  | Contagem | Total
Sim | Microsoft.Logic/workflows | TriggerSuccessLatency | Latência de êxito do gatilho  | Segundos | Média
Sim | Microsoft.Logic/workflows | TriggerThrottledEvents | Disparar eventos restringidos | Contagem | Total
Sim | Microsoft.MachineLearningServices/workspaces | Núcleos ativos | Núcleos ativos | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Nós ativos | Nós ativos | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Execuções concluídas | Execuções concluídas | Contagem | Total
Sim | Microsoft.MachineLearningServices/workspaces | Execuções Falhadas | Execuções Falhadas | Contagem | Total
Sim | Microsoft.MachineLearningServices/workspaces | Núcleos ociosos | Núcleos ociosos | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Nós ociosos | Nós ociosos | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Deixando núcleos | Deixando núcleos | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Saindo de nós | Saindo de nós | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Falha na Implantação de Modelo | Falha na Implantação de Modelo | Contagem | Total
Sim | Microsoft.MachineLearningServices/workspaces | Implantação de Modelo iniciado | Implantação de Modelo iniciado | Contagem | Total
Sim | Microsoft.MachineLearningServices/workspaces | Implantação de Modelo com êxito | Implantação de Modelo com êxito | Contagem | Total
Sim | Microsoft.MachineLearningServices/workspaces | Falha no registro do modelo | Falha no registro do modelo | Contagem | Total
Sim | Microsoft.MachineLearningServices/workspaces | Registro de modelo bem-sucedido | Registro de modelo bem-sucedido | Contagem | Total
Sim | Microsoft.MachineLearningServices/workspaces | Núcleos preempçãos | Núcleos preempçãos | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Nós admitidos | Nós admitidos | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Porcentagem de utilização de cota | Porcentagem de utilização de cota | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Execuções iniciadas | Execuções iniciadas | Contagem | Total
Sim | Microsoft.MachineLearningServices/workspaces | Total de núcleos | Total de núcleos | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Total de nós | Total de nós | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Núcleos inutilizáveis | Núcleos inutilizáveis | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Nós inutilizáveis | Nós inutilizáveis | Contagem | Média
Sim | Microsoft.Maps/accounts | Disponibilidade | Disponibilidade | Percentagem | Média
Não | Microsoft.Maps/accounts | Utilização | Utilização | Contagem | Contagem
Sim | Microsoft. Media/mediaservices | AssetCount | Contagem de ativos | Contagem | Média
Sim | Microsoft. Media/mediaservices | AssetQuota | Cota de ativos | Contagem | Média
Sim | Microsoft. Media/mediaservices | AssetQuotaUsedPercentage | Porcentagem de cota de ativos usada | Percentagem | Média
Sim | Microsoft. Media/mediaservices | ContentKeyPolicyCount | Contagem de política de chave de conteúdo | Contagem | Média
Sim | Microsoft. Media/mediaservices | ContentKeyPolicyQuota | Cota de política de chave de conteúdo | Contagem | Média
Sim | Microsoft. Media/mediaservices | ContentKeyPolicyQuotaUsedPercentage | Porcentagem de cota usada da política de chave de conteúdo | Percentagem | Média
Sim | Microsoft. Media/mediaservices | StreamingPolicyCount | Contagem de políticas de streaming | Contagem | Média
Sim | Microsoft. Media/mediaservices | StreamingPolicyQuota | Cota de política de streaming | Contagem | Média
Sim | Microsoft. Media/mediaservices | StreamingPolicyQuotaUsedPercentage | Porcentagem de cota usada da política de streaming | Percentagem | Média
Sim | Microsoft. Media/mediaservices/streamingEndpoints | Saída | Saída | Bytes | Total
Sim | Microsoft. Media/mediaservices/streamingEndpoints | Pedidos | Pedidos | Contagem | Total
Sim | Microsoft. Media/mediaservices/streamingEndpoints | SuccessE2ELatency | Latência de ponta a ponta com êxito | Milissegundos | Média
Sim | Microsoft. Microservices4Spring/appClusters | GCPauseTotalCount | Contagem de pausar do GC | Contagem | Total
Sim | Microsoft. Microservices4Spring/appClusters | GCPauseTotalTime | Tempo total de pausa do GC | Milissegundos | Total
Sim | Microsoft. Microservices4Spring/appClusters | MaxOldGenMemoryPoolBytes | Tamanho máximo de dados de geração antiga disponíveis | Bytes | Média
Sim | Microsoft. Microservices4Spring/appClusters | OldGenMemoryPoolBytes | Tamanho de dados de geração antiga | Bytes | Média
Sim | Microsoft. Microservices4Spring/appClusters | OldGenPromotedBytes | Promover para tamanho de dados de geração antiga | Bytes | Máximo
Sim | Microsoft. Microservices4Spring/appClusters | ServiceCpuUsagePercentage | Porcentagem de uso da CPU do serviço | Percentagem | Média
Sim | Microsoft. Microservices4Spring/appClusters | ServiceMemoryCommitted | Memória de serviço atribuída | Bytes | Média
Sim | Microsoft. Microservices4Spring/appClusters | ServiceMemoryMax | Memória máxima do serviço | Bytes | Máximo
Sim | Microsoft. Microservices4Spring/appClusters | ServiceMemoryUsed | Memória usada pelo serviço | Bytes | Média
Sim | Microsoft. Microservices4Spring/appClusters | SystemCpuUsagePercentage | Porcentagem de uso da CPU do sistema | Percentagem | Média
Sim | Microsoft. Microservices4Spring/appClusters | TomcatErrorCount | Erro global do Tomcat | Contagem | Total
Sim | Microsoft. Microservices4Spring/appClusters | TomcatReceivedBytes | Total de bytes recebidos do Tomcat | Bytes | Total
Sim | Microsoft. Microservices4Spring/appClusters | TomcatRequestMaxTime | Tempo máximo de solicitação do Tomcat | Milissegundos | Máximo
Sim | Microsoft. Microservices4Spring/appClusters | TomcatRequestTotalCount | Contagem total de solicitações do Tomcat | Contagem | Total
Sim | Microsoft. Microservices4Spring/appClusters | TomcatRequestTotalTime | Tempos totais da solicitação do Tomcat | Milissegundos | Total
Sim | Microsoft. Microservices4Spring/appClusters | TomcatResponseAvgTime | Tempo médio da solicitação do Tomcat | Milissegundos | Média
Sim | Microsoft. Microservices4Spring/appClusters | TomcatSentBytes | Total de bytes enviados do Tomcat | Bytes | Total
Sim | Microsoft. Microservices4Spring/appClusters | TomcatSessionActiveCurrentCount | Contagem de sessões ativas de Tomcat | Contagem | Total
Sim | Microsoft. Microservices4Spring/appClusters | TomcatSessionActiveMaxCount | Contagem ativa máxima da sessão do Tomcat | Contagem | Total
Sim | Microsoft. Microservices4Spring/appClusters | TomcatSessionAliveMaxTime | Tempo de atividade máx de sessão do Tomcat | Milissegundos | Máximo
Sim | Microsoft. Microservices4Spring/appClusters | TomcatSessionCreatedCount | Contagem de sessões criadas do Tomcat | Contagem | Total
Sim | Microsoft. Microservices4Spring/appClusters | TomcatSessionExpiredCount | Contagem de sessões expiradas do Tomcat | Contagem | Total
Sim | Microsoft. Microservices4Spring/appClusters | TomcatSessionRejectedCount | Contagem rejeitada da sessão Tomcat | Contagem | Total
Sim | Microsoft. Microservices4Spring/appClusters | YoungGenPromotedBytes | Promover para tamanho de dados de geração jovem | Bytes | Máximo
Sim | Microsoft.NetApp/netAppAccounts/capacityPools | VolumePoolAllocatedUsed | Pool de volumes alocado usado | Bytes | Média
Sim | Microsoft.NetApp/netAppAccounts/capacityPools | VolumePoolTotalLogicalSize | Tamanho lógico total do pool de volumes | Bytes | Média
Sim | Microsoft. NetApp/netAppAccounts/capacityPools/volumes | AverageReadLatency | Latência média de leitura | Milissegundos | Média
Sim | Microsoft. NetApp/netAppAccounts/capacityPools/volumes | AverageWriteLatency | Latência média de gravação | Milissegundos | Média
Sim | Microsoft. NetApp/netAppAccounts/capacityPools/volumes | ReadIops | IOPS de leitura | CountPerSecond | Média
Sim | Microsoft. NetApp/netAppAccounts/capacityPools/volumes | VolumeLogicalSize | Tamanho lógico do volume | Bytes | Média
Sim | Microsoft. NetApp/netAppAccounts/capacityPools/volumes | VolumeSnapshotSize | Tamanho do instantâneo de volume | Bytes | Média
Sim | Microsoft. NetApp/netAppAccounts/capacityPools/volumes | WriteIops | IOPS de gravação | CountPerSecond | Média
Não | Microsoft.Network/applicationGateways | ApplicationGatewayTotalTime | Tempo total do gateway de aplicativo | Milissegundos | Média
Não | Microsoft.Network/applicationGateways | AvgRequestCountPerHealthyHost | Solicitações por minuto por host íntegro | Contagem | Média
Não | Microsoft.Network/applicationGateways | BackendConnectTime | Tempo de conexão de back-end | Milissegundos | Média
Não | Microsoft.Network/applicationGateways | BackendFirstByteResponseTime | Tempo de resposta do primeiro byte do back-end | Milissegundos | Média
Não | Microsoft.Network/applicationGateways | BackendLastByteResponseTime | Tempo de resposta do último byte do back-end | Milissegundos | Média
Sim | Microsoft.Network/applicationGateways | BackendResponseStatus | Status da resposta de back-end | Contagem | Total
Sim | Microsoft.Network/applicationGateways | BlockedCount | Distribuição de regra de solicitações bloqueadas do firewall do aplicativo Web | Contagem | Total
Sim | Microsoft.Network/applicationGateways | BlockedReqCount | Contagem de solicitações bloqueadas do firewall do aplicativo Web | Contagem | Total
Sim | Microsoft.Network/applicationGateways | BytesReceived | Bytes Recebidos | Bytes | Total
Sim | Microsoft.Network/applicationGateways | BytesSent | Bytes Enviados | Bytes | Total
Não | Microsoft.Network/applicationGateways | CapacityUnits | Unidades de capacidade atuais | Contagem | Média
Não | Microsoft.Network/applicationGateways | ClientRtt | RTT do cliente | Milissegundos | Média
Não | Microsoft.Network/applicationGateways | ComputeUnits | Unidades de computação atuais | Contagem | Média
Sim | Microsoft.Network/applicationGateways | CurrentConnections | Conexões atuais | Contagem | Total
Sim | Microsoft.Network/applicationGateways | FailedRequests | Pedidos com Falhas | Contagem | Total
Sim | Microsoft.Network/applicationGateways | HealthyHostCount | Contagem de hosts íntegros | Contagem | Média
Sim | Microsoft.Network/applicationGateways | MatchedCount | Distribuição da regra total do firewall do aplicativo Web | Contagem | Total
Sim | Microsoft.Network/applicationGateways | ResponseStatus | Status da resposta | Contagem | Total
Não | Microsoft.Network/applicationGateways | Débito | Débito | BytesPerSecond | Média
Sim | Microsoft.Network/applicationGateways | TlsProtocol | Protocolo TLS do cliente | Contagem | Total
Sim | Microsoft.Network/applicationGateways | TotalPedidos | Total de Pedidos | Contagem | Total
Sim | Microsoft.Network/applicationGateways | UnhealthyHostCount | Contagem de hosts não íntegros | Contagem | Média
Sim | Microsoft.Network/azurefirewalls | ApplicationRuleHit | Contagem de impacto de regras de aplicativo | Contagem | Total
Sim | Microsoft.Network/azurefirewalls | Dataprocessod | Dados processados | Bytes | Total
Sim | Microsoft.Network/azurefirewalls | FirewallHealth | Estado de integridade do firewall | Percentagem | Média
Sim | Microsoft.Network/azurefirewalls | NetworkRuleHit | Contagem de impacto das regras de rede | Contagem | Total
Sim | Microsoft.Network/azurefirewalls | SNATPortUtilization | Utilização da porta SNAT | Percentagem | Média
Sim | Microsoft.Network/connections | BitsInPerSecond | BitsInPerSecond | CountPerSecond | Média
Sim | Microsoft.Network/connections | BitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Média
Sim | Microsoft.Network/dnszones | QueryVolume | Volume de consulta | Contagem | Total
Não | Microsoft.Network/dnszones | RecordSetCapacityUtilization | Utilização da capacidade do conjunto de registros | Percentagem | Máximo
Sim | Microsoft.Network/dnszones | RecordSetCount | Contagem de conjuntos de registros | Contagem | Máximo
Sim | Microsoft.Network/expressRouteCircuits | ArpAvailability | Disponibilidade do ARP | Percentagem | Média
Sim | Microsoft.Network/expressRouteCircuits | BgpAvailability | Disponibilidade de BGP | Percentagem | Média
Não | Microsoft.Network/expressRouteCircuits | BitsInPerSecond | BitsInPerSecond | CountPerSecond | Média
Não | Microsoft.Network/expressRouteCircuits | BitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Média
Não | Microsoft.Network/expressRouteCircuits | GlobalReachBitsInPerSecond | GlobalReachBitsInPerSecond | CountPerSecond | Média
Não | Microsoft.Network/expressRouteCircuits | GlobalReachBitsOutPerSecond | GlobalReachBitsOutPerSecond | CountPerSecond | Média
Não | Microsoft.Network/expressRouteCircuits | QosDropBitsInPerSecond | DroppedInBitsPerSecond | CountPerSecond | Média
Não | Microsoft.Network/expressRouteCircuits | QosDropBitsOutPerSecond | DroppedOutBitsPerSecond | CountPerSecond | Média
Sim | Microsoft.Network/expressRouteCircuits/peerings | BitsInPerSecond | BitsInPerSecond | CountPerSecond | Média
Sim | Microsoft.Network/expressRouteCircuits/peerings | BitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Média
Não | Microsoft. Network/expressRouteGateways | ErGatewayConnectionBitsInPerSecond | BitsInPerSecond | CountPerSecond | Média
Não | Microsoft. Network/expressRouteGateways | ErGatewayConnectionBitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Média
Sim | Microsoft. Network/expressRoutePorts | Adminstate | Adminstate | Contagem | Média
Sim | Microsoft. Network/expressRoutePorts | LineProtocol | LineProtocol | Contagem | Média
Sim | Microsoft. Network/expressRoutePorts | PortBitsInPerSecond | BitsInPerSecond | CountPerSecond | Média
Sim | Microsoft. Network/expressRoutePorts | PortBitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Média
Sim | Microsoft. Network/expressRoutePorts | RxLightLevel | RxLightLevel | Contagem | Média
Sim | Microsoft. Network/expressRoutePorts | TxLightLevel | TxLightLevel | Contagem | Média
Sim | Microsoft.Network/frontdoors | BackendHealthPercentage | Percentual de integridade de back-end | Percentagem | Média
Sim | Microsoft.Network/frontdoors | BackendRequestCount | Contagem de solicitações de back-end | Contagem | Total
Sim | Microsoft.Network/frontdoors | BackendRequestLatency | Latência de solicitação de back-end | Milissegundos | Média
Sim | Microsoft.Network/frontdoors | BillableResponseSize | Tamanho de resposta Faturável | Bytes | Total
Sim | Microsoft.Network/frontdoors | RequestCount | Número de Pedidos | Contagem | Total
Sim | Microsoft.Network/frontdoors | Solicitações | Tamanho da solicitação | Bytes | Total
Sim | Microsoft.Network/frontdoors | Respostas | Tamanho da resposta | Bytes | Total
Sim | Microsoft.Network/frontdoors | TotalLatency | Latência total | Milissegundos | Média
Sim | Microsoft.Network/frontdoors | WebApplicationFirewallRequestCount | Contagem de solicitações de firewall do aplicativo Web | Contagem | Total
Não | Microsoft.Network/loadBalancers | AllocatedSnatPorts | Portas SNAT alocadas (visualização) | Contagem | Total
Sim | Microsoft.Network/loadBalancers | ByteCount | Contagem de bytes | Contagem | Total
Sim | Microsoft.Network/loadBalancers | DipAvailability | Status da investigação de integridade | Contagem | Média
Sim | Microsoft.Network/loadBalancers | PacketCount | Contagem de pacotes | Contagem | Total
Sim | Microsoft.Network/loadBalancers | SnatConnectionCount | Contagem de conexões SNAT | Contagem | Total
Sim | Microsoft.Network/loadBalancers | SYNCount | Contagem de SYN | Contagem | Total
Não | Microsoft.Network/loadBalancers | UsedSnatPorts | Portas SNAT usadas (visualização) | Contagem | Total
Sim | Microsoft.Network/loadBalancers | VipAvailability | Disponibilidade do caminho de dados | Contagem | Média
Sim | Microsoft.Network/networkInterfaces | BytesReceivedRate | Bytes Recebidos | Bytes | Total
Sim | Microsoft.Network/networkInterfaces | BytesSentRate | Bytes Enviados | Bytes | Total
Sim | Microsoft.Network/networkInterfaces | PacketsReceivedRate | Pacotes recebidos | Contagem | Total
Sim | Microsoft.Network/networkInterfaces | PacketsSentRate | Pacotes enviados | Contagem | Total
Sim | Microsoft.Network/networkWatchers/connectionMonitors | AverageRoundtripMs | Média de tempo de ida e volta (MS) | Milissegundos | Média
Sim | Microsoft.Network/networkWatchers/connectionMonitors | ChecksFailedPercent | Verificações por cento com falha (versão prévia) | Percentagem | Média
Sim | Microsoft.Network/networkWatchers/connectionMonitors | ProbesFailedPercent | % De investigações com falha | Percentagem | Média
Sim | Microsoft.Network/networkWatchers/connectionMonitors | RoundTripTimeMs | Tempo de ida e volta (MS) (visualização) | Milissegundos | Média
Sim | Microsoft.Network/publicIPAddresses | ByteCount | Contagem de bytes | Contagem | Total
Sim | Microsoft.Network/publicIPAddresses | BytesDroppedDDoS | Bytes de entrada eliminados DDoS | BytesPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | BytesForwardedDDoS | Bytes de entrada reencaminhados DDoS | BytesPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | BytesInDDoS | Bytes de entrada DDoS | BytesPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | DDoSTriggerSYNPackets | Pacotes de entrada SYN para acionar mitigação de DDoS | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | DDoSTriggerTCPPackets | Pacotes TCP de entrada para disparar a mitigação de DDoS | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | DDoSTriggerUDPPackets | Pacotes UDP de entrada para disparar a mitigação de DDoS | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | IfUnderDDoSAttack | Sob ataque de DDoS ou não | Contagem | Máximo
Sim | Microsoft.Network/publicIPAddresses | PacketCount | Contagem de pacotes | Contagem | Total
Sim | Microsoft.Network/publicIPAddresses | PacketsDroppedDDoS | Pacotes de entrada eliminados DDoS | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | PacketsForwardedDDoS | Pacotes de entrada reencaminhados DDoS | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | PacketsInDDoS | Pacotes de entrada DDoS | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | SynCount | Contagem de SYN | Contagem | Total
Sim | Microsoft.Network/publicIPAddresses | TCPBytesDroppedDDoS | DDoS de bytes de TCP de entrada eliminados | BytesPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | TCPBytesForwardedDDoS | DDoS de bytes TCP de entrada encaminhados | BytesPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | TCPBytesInDDoS | Bytes TCP de entrada DDoS | BytesPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | TCPPacketsDroppedDDoS | DDoS de pacotes TCP de entrada removidos | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | TCPPacketsForwardedDDoS | DDoS de pacotes TCP de entrada encaminhados | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | TCPPacketsInDDoS | DDoS de pacotes TCP de entrada | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | UDPBytesDroppedDDoS | DDoS de bytes UDP de entrada eliminados | BytesPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | UDPBytesForwardedDDoS | DDoS de bytes de UDP de entrada encaminhados | BytesPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | UDPBytesInDDoS | DDoS de bytes de UDP de entrada | BytesPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | UDPPacketsDroppedDDoS | DDoS de pacotes UDP de entrada removidos | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | UDPPacketsForwardedDDoS | DDoS de pacotes UDP de entrada encaminhados | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | UDPPacketsInDDoS | DDoS de pacotes UDP de entrada | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | VipAvailability | Disponibilidade do caminho de dados | Contagem | Média
Sim | Microsoft.Network/trafficManagerProfiles | ProbeAgentCurrentEndpointStateByProfileResourceId | Status do ponto de extremidade por ponto de extremidade | Contagem | Máximo
Sim | Microsoft.Network/trafficManagerProfiles | QpsByEndpoint | Consultas por ponto de extremidade retornadas | Contagem | Total
Sim | Microsoft.Network/virtualNetworkGateways | AverageBandwidth | Largura de banda S2S do gateway | BytesPerSecond | Média
Sim | Microsoft.Network/virtualNetworkGateways | P2SBandwidth | Largura de banda P2S do gateway | BytesPerSecond | Média
Sim | Microsoft.Network/virtualNetworkGateways | P2SConnectionCount | Contagem de conexões P2S | Contagem | Máximo
Sim | Microsoft.Network/virtualNetworkGateways | TunnelAverageBandwidth | Largura de banda do túnel | BytesPerSecond | Média
Sim | Microsoft.Network/virtualNetworkGateways | TunnelEgressBytes | Bytes de saída do túnel | Bytes | Total
Sim | Microsoft.Network/virtualNetworkGateways | TunnelEgressPacketDropTSMismatch | Descarte de pacotes incompatíveis TS de saída de túnel | Contagem | Total
Sim | Microsoft.Network/virtualNetworkGateways | TunnelEgressPackets | Pacotes de saída de túnel | Contagem | Total
Sim | Microsoft.Network/virtualNetworkGateways | TunnelIngressBytes | Bytes de entrada de túnel | Bytes | Total
Sim | Microsoft.Network/virtualNetworkGateways | TunnelIngressPacketDropTSMismatch | Descarte de pacotes incompatíveis TS de entrada de túnel | Contagem | Total
Sim | Microsoft.Network/virtualNetworkGateways | TunnelIngressPackets | Pacotes de entrada de túnel | Contagem | Total
Sim | Microsoft.Network/virtualNetworks | PingMeshAverageRoundtripMs | Tempo de ida e volta para pings em uma VM | Milissegundos | Média
Sim | Microsoft.Network/virtualNetworks | PingMeshProbesFailedPercent | Pings com falha em uma VM | Percentagem | Média
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | recebidos | Mensagens de entrada | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.failedrequests | Todas as solicitações com falha de entrada | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.requests | Todas as solicitações de entrada | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled | Notificações por push agendadas enviadas | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled.cancel | Notificações por push agendadas canceladas | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | instalação. todos | Operações de gerenciamento de instalação | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.delete | Excluir operações de instalação | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.get | Obter operações de instalação | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.patch | Operações de instalação de patch | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.upsert | Criar ou atualizar operações de instalação | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | notificationhub.pushes | Todas as notificações de saída | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.badorexpiredchannel | Erros de canal insatisfatórios ou expirados | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.channelerror | Erros de canal | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.invalidpayload | Erros de carga | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.pnserror | Erros do sistema de notificação externa | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.success | Notificações bem-sucedidas | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.badchannel | Erro de canal insatisfatório de APNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.expiredchannel | Erro de canal expirado APNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidcredentials | Erros de autorização de APNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidnotificationsize | Erro de tamanho de notificação inválido do APNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.pnserror | Erros de APNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.success | Notificações bem-sucedidas do APNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.authenticationerror | Erros de autenticação do GCM | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.badchannel | Erro de canal insatisfatório do GCM | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.expiredchannel | Erro de canal expirado do GCM | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidcredentials | Erros de autorização do GCM (credenciais inválidas) | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationformat | Formato de notificação inválido do GCM | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationsize | Erro de tamanho de notificação inválido do GCM | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.pnserror | Erros do GCM | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.success | Notificações de êxito do GCM | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.throttled | Notificações limitadas do GCM | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.wrongchannel | Erro de canal incorreto do GCM | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.authenticationerror | Erros de autenticação do MPNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.badchannel | Erro de canal insatisfatório do MPNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.channeldisconnected | Canal do MPNS desconectado | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.dropped | Notificações ignoradas do MPNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidcredentials | Credenciais inválidas do MPNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidnotificationformat | Formato de notificação inválido do MPNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.pnserror | Erros do MPNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.success | Notificações bem-sucedidas do MPNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.throttled | Notificações limitadas do MPNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.authenticationerror | Erros de autenticação WNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.badchannel | Erro de canal insatisfatório do WNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channeldisconnected | Canal do WNS desconectado | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channelthrottled | Canal do WNS limitado | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.dropped | Notificações descartadas do WNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.expiredchannel | Erro de canal expirado do WNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidcredentials | Erros de autorização do WNS (credenciais inválidas) | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationformat | Formato de notificação inválido do WNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationsize | Erro de tamanho de notificação inválido do WNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidtoken | Erros de autorização do WNS (token inválido) | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.pnserror | Erros de WNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.success | Notificações com êxito do WNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.throttled | Notificações limitadas do WNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.tokenproviderunreachable | Erros de autorização do WNS (inacessível) | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.wrongtoken | Erros de autorização do WNS (token incorreto) | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.all | Operações de registro | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.create | Operações de criação de registro | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.delete | Operações de exclusão de registro | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.get | Operações de leitura de registro | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.update | Operações de atualização de registro | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | agendado. pendente | Notificações agendadas pendentes | Contagem | Total
Sim | Microsoft.OperationalInsights/workspaces | % De memória disponível Average_ | % De memória disponível | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% de espaço de permuta disponível | % De espaço de permuta disponível | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% de bytes confirmados em uso | % Bytes confirmados em uso | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% tempo de DPC | % De tempo de DPC | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% de inodes livres | % De inodes livres | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% de espaço livre | % De espaço livre | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% de espaço livre | % De espaço livre | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% de tempo ocioso | % de Tempo Inativo | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% tempo de interrupção | % De tempo de interrupção | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% de tempo de espera de e/s | % De tempo de espera de e/s | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% de tempo interessante | % De tempo adequado | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% de tempo privilegiado | % De tempo privilegiado | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% de tempo do processador | % Tempo do processador | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% de tempo do processador | % Tempo do processador | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% de inodes usados | % De inodes usados | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | % De memória usada Average_ | % De memória usada | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% de espaço usado | % De espaço utilizado | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% de espaço de permuta utilizado | % De espaço de permuta utilizado | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Tempo de usuário do Average_% | % De tempo do usuário | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Mbytes de Average_Available | MBytes disponíveis | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Memória de Mbytes Average_Available | MBytes de memória disponíveis | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Permuta de Average_Available Mbytes | Troca de MBytes disponíveis | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Avg. Disco seg/leitura | Média de disco s/leitura | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Avg. Disco seg/leitura | Média de disco s/leitura | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Avg. Disco seg/transferência | Média de disco s/transferência | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Avg. Disco seg/escritas | Média de disco s/gravação | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Avg. Disco seg/escritas | Média de disco s/gravação | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Bytes recebidos/s | Bytes recebidos/seg | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Bytes enviados/s | Bytes enviados/seg | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Total de Average_Bytes/s | Total de bytes/s | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Comprimento da fila de disco Average_Current | Comprimento da fila de disco atual | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Bytes de leitura de Average_Disk/s | Bytes Lidos de Disco/seg | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Leituras de Average_Disk/s | Leituras de disco/seg | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Leituras de Average_Disk/s | Leituras de disco/seg | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Transferências de Average_Disk/s | As transferências de disco/seg | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Transferências de Average_Disk/s | As transferências de disco/seg | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Bytes de gravação de Average_Disk/s | Bytes Escritos em Disco/seg | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Gravações de Average_Disk/s | Escritas de disco/seg | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Gravações de Average_Disk/s | Escritas de disco/seg | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Free megabytes | Megabytes livres | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Free megabytes | Megabytes livres | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Memória física Average_Free | Memória física livre | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Espaço de Average_Free em arquivos de paginação | Espaço livre em arquivos de paginação | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Memória virtual Average_Free | Memória virtual livre | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Bytes de disco de Average_Logical/s | Bytes de disco lógico/seg | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Leituras de Average_Page/s | Leituras de Paginações/seg | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Gravações de Average_Page/s | Escritas de Paginações/seg | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Pages/s | Páginas/seg | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Pct tempo privilegiado | Percentual de tempo privilegiado | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Pct tempo do usuário | Tempo de usuário do PCT | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Bytes de disco de Average_Physical/s | Bytes de disco físico/s | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Processes | Processos | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Comprimento da fila de Average_Processor | Comprimento da fila de processador | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Size armazenados em arquivos de paginação | Tamanho armazenado em arquivos de paginação | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Bytes de Average_Total | Total de bytes | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Bytes de Average_Total recebidos | Total de Bytes recebidos | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Bytes de Average_Total transmitidos | Total de Bytes transmitidos | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Colisões de Average_Total | Total de colisões | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Total pacotes recebidos | Total de pacotes recebidos | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Pacotes de Average_Total transmitidos | Total de pacotes transmitidos | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Erros de Average_Total RX | Total de erros de RX | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Total erros de TX | Total de erros TX | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Uptime | Tempo de atividade | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Espaço de permuta de Average_Used Mbytes | Espaço de permuta usado em Mbytes | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | KBytes de memória Average_Used | KBytes de memória usada | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Mbytes de memória Average_Used | Mbytes de memória usados | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Users | Utilizadores | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Virtual memória compartilhada | Memória compartilhada virtual | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Evento | Evento | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Heartbeat | Heartbeat | Contagem | Total
Sim | Microsoft.OperationalInsights/workspaces | Atualizar | Atualizar | Contagem | Média
Sim | Microsoft.PowerBIDedicated/capacities | memory_metric | Memória | Bytes | Média
Sim | Microsoft.PowerBIDedicated/capacities | memory_thrashing_metric | Ultrapaginação de memória (conjuntos de valores) | Percentagem | Média
Sim | Microsoft.PowerBIDedicated/capacities | qpu_high_utilization_metric | Maior Utilização de QPU | Contagem | Total
Sim | Microsoft.PowerBIDedicated/capacities | QueryDuration | Duração da consulta (conjuntos de valores) | Milissegundos | Média
Sim | Microsoft.PowerBIDedicated/capacities | QueryPoolJobQueueLength | Comprimento da fila de trabalhos do pool de consultas (conjuntos de os) | Contagem | Média
Não | Microsoft. Relay/namespaces | ActiveConnections | ActiveConnections | Contagem | Total
Não | Microsoft. Relay/namespaces | ActiveListeners | ActiveListeners | Contagem | Total
Sim | Microsoft. Relay/namespaces | BytesTransferred | BytesTransferred | Contagem | Total
Não | Microsoft. Relay/namespaces | ListenerConnections-ClientError | ListenerConnections-ClientError | Contagem | Total
Não | Microsoft. Relay/namespaces | ListenerConnections-ServerError | ListenerConnections-ServerError | Contagem | Total
Não | Microsoft. Relay/namespaces | ListenerConnections-Success | ListenerConnections-Success | Contagem | Total
Não | Microsoft. Relay/namespaces | ListenerConnections-TotalRequests | ListenerConnections-TotalRequests | Contagem | Total
Não | Microsoft. Relay/namespaces | ListenerDisconnects | ListenerDisconnects | Contagem | Total
Não | Microsoft. Relay/namespaces | SenderConnections-ClientError | SenderConnections-ClientError | Contagem | Total
Não | Microsoft. Relay/namespaces | SenderConnections-ServerError | SenderConnections-ServerError | Contagem | Total
Não | Microsoft. Relay/namespaces | SenderConnections-Success | SenderConnections-Success | Contagem | Total
Não | Microsoft. Relay/namespaces | SenderConnections-TotalRequests | SenderConnections-TotalRequests | Contagem | Total
Não | Microsoft. Relay/namespaces | SenderDisconnects | SenderDisconnects | Contagem | Total
Sim | Microsoft.Search/searchServices | SearchLatency | Latência de pesquisa | Segundos | Média
Sim | Microsoft.Search/searchServices | SearchQueriesPerSecond | Pesquisar consultas por segundo | CountPerSecond | Média
Sim | Microsoft.Search/searchServices | ThrottledSearchQueriesPercentage | Percentual de consultas de pesquisa limitadas | Percentagem | Média
Não | Microsoft.ServiceBus/namespaces | ActiveConnections | ActiveConnections | Contagem | Total
Não | Microsoft.ServiceBus/namespaces | ActiveMessages | Contagem de mensagens ativas em uma fila/tópico. | Contagem | Média
Não | Microsoft.ServiceBus/namespaces | ConnectionsClosed | Conexões fechadas. | Contagem | Média
Não | Microsoft.ServiceBus/namespaces | ConnectionsOpened | Conexões abertas. | Contagem | Média
Não | Microsoft.ServiceBus/namespaces | CPUXNS | CPU (preterido) | Percentagem | Máximo
Não | Microsoft.ServiceBus/namespaces | DeadletteredMessages | Contagem de mensagens mortas em uma fila/tópico. | Contagem | Média
Sim | Microsoft.ServiceBus/namespaces | IncomingMessages | Mensagens de entrada | Contagem | Total
Sim | Microsoft.ServiceBus/namespaces | IncomingRequests | Solicitações de entrada | Contagem | Total
Não | Microsoft.ServiceBus/namespaces | Mensagens | Contagem de mensagens em uma fila/tópico. | Contagem | Média
Não | Microsoft.ServiceBus/namespaces | NamespaceCpuUsage | CPU | Percentagem | Máximo
Não | Microsoft.ServiceBus/namespaces | NamespaceMemoryUsage | Utilização de Memória | Percentagem | Máximo
Sim | Microsoft.ServiceBus/namespaces | OutgoingMessages | Mensagens de saída | Contagem | Total
Não | Microsoft.ServiceBus/namespaces | ScheduledMessages | Contagem de mensagens agendadas em uma fila/tópico. | Contagem | Média
Não | Microsoft.ServiceBus/namespaces | ServerErrors | Erros do servidor. | Contagem | Total
Não | Microsoft.ServiceBus/namespaces | Tamanho | Tamanho | Bytes | Média
Não | Microsoft.ServiceBus/namespaces | SuccessfulRequests | Solicitações bem-sucedidas | Contagem | Total
Não | Microsoft.ServiceBus/namespaces | ThrottledRequests | Solicitações limitadas. | Contagem | Total
Não | Microsoft.ServiceBus/namespaces | UserErrors | Erros do usuário. | Contagem | Total
Não | Microsoft.ServiceBus/namespaces | WSXNS | Uso de memória (preterido) | Percentagem | Máximo
Não | Microsoft.ServiceFabricMesh/applications | ActualCpu | ActualCpu | Contagem | Média
Não | Microsoft.ServiceFabricMesh/applications | ActualMemory | ActualMemory | Bytes | Média
Não | Microsoft.ServiceFabricMesh/applications | AllocatedCpu | AllocatedCpu | Contagem | Média
Não | Microsoft.ServiceFabricMesh/applications | AllocatedMemory | AllocatedMemory | Bytes | Média
Não | Microsoft.ServiceFabricMesh/applications | ApplicationStatus | ApplicationStatus | Contagem | Média
Não | Microsoft.ServiceFabricMesh/applications | ContainerStatus | ContainerStatus | Contagem | Média
Não | Microsoft.ServiceFabricMesh/applications | CpuUtilization | CpuUtilization | Percentagem | Média
Não | Microsoft.ServiceFabricMesh/applications | MemoryUtilization | MemoryUtilization | Percentagem | Média
Não | Microsoft.ServiceFabricMesh/applications | RestartCount | RestartCount | Contagem | Média
Não | Microsoft.ServiceFabricMesh/applications | ServiceReplicaStatus | ServiceReplicaStatus | Contagem | Média
Não | Microsoft.ServiceFabricMesh/applications | ServiceStatus | ServiceStatus | Contagem | Média
Sim | Microsoft.SignalRService/SignalR | ConnectionCount | Contagem de conexões | Contagem | Máximo
Sim | Microsoft.SignalRService/SignalR | InboundTraffic | Tráfego de Entrada | Bytes | Total
Sim | Microsoft.SignalRService/SignalR | MessageCount | Contagem de mensagens | Contagem | Total
Sim | Microsoft.SignalRService/SignalR | OutboundTraffic | Tráfego de Saída | Bytes | Total
Sim | Microsoft.SignalRService/SignalR | SystemErrors | Erros do sistema | Percentagem | Máximo
Sim | Microsoft.SignalRService/SignalR | UserErrors | Erros do usuário | Percentagem | Máximo
Sim | Microsoft.Sql/managedInstances | avg_cpu_percent | Percentual médio de CPU | Percentagem | Média
Sim | Microsoft.Sql/managedInstances | io_bytes_read | Bytes de e/s lidos | Bytes | Média
Sim | Microsoft.Sql/managedInstances | io_bytes_written | Bytes de e/s gravados | Bytes | Média
Sim | Microsoft.Sql/managedInstances | io_requests | Contagem de solicitações de e/s | Contagem | Média
Sim | Microsoft.Sql/managedInstances | reserved_storage_mb | Espaço de armazenamento reservado | Contagem | Média
Sim | Microsoft.Sql/managedInstances | storage_space_used_mb | Espaço de armazenamento usado | Contagem | Média
Sim | Microsoft.Sql/managedInstances | virtual_core_count | Contagem de núcleos virtuais | Contagem | Média
Não | Microsoft.Sql/servers | database_dtu_consumption_percent | Percentagem de DTU | Percentagem | Média
Não | Microsoft.Sql/servers | database_storage_used | Espaço de dados usado | Bytes | Média
Sim | Microsoft.Sql/servers | dtu_consumption_percent | Percentagem de DTU | Percentagem | Média
Sim | Microsoft.Sql/servers | dtu_used | DTU usado | Contagem | Média
Sim | Microsoft.Sql/servers | storage_used | Espaço de dados usado | Bytes | Média
Sim | Microsoft.Sql/servers/databases | allocated_data_storage | Espaço de dados alocado | Bytes | Média
Sim | Microsoft.Sql/servers/databases | app_cpu_billed | CPU do aplicativo cobrada | Contagem | Total
Sim | Microsoft.Sql/servers/databases | app_cpu_percent | Porcentagem de CPU do aplicativo | Percentagem | Média
Sim | Microsoft.Sql/servers/databases | app_memory_percent | Porcentagem de memória do aplicativo | Percentagem | Média
Sim | Microsoft.Sql/servers/databases | blocked_by_firewall | Bloqueado pelo firewall | Contagem | Total
Sim | Microsoft.Sql/servers/databases | cache_hit_percent | Porcentagem de acesso ao cache | Percentagem | Máximo
Sim | Microsoft.Sql/servers/databases | cache_used_percent | Percentual de cache usado | Percentagem | Máximo
Sim | Microsoft.Sql/servers/databases | connection_failed | Ligações com Falhas | Contagem | Total
Sim | Microsoft.Sql/servers/databases | connection_successful | Conexões com êxito | Contagem | Total
Sim | Microsoft.Sql/servers/databases | cpu_limit | Limite de CPU | Contagem | Média
Sim | Microsoft.Sql/servers/databases | cpu_percent | Percentagem de CPU | Percentagem | Média
Sim | Microsoft.Sql/servers/databases | cpu_used | CPU usada | Contagem | Média
Sim | Microsoft.Sql/servers/databases | bloqueado | Deadlocks | Contagem | Total
Sim | Microsoft.Sql/servers/databases | dtu_consumption_percent | Percentagem de DTU | Percentagem | Média
Sim | Microsoft.Sql/servers/databases | dtu_limit | Limite de DTU | Contagem | Média
Sim | Microsoft.Sql/servers/databases | dtu_used | DTU usado | Contagem | Média
Sim | Microsoft.Sql/servers/databases | dwu_consumption_percent | Porcentagem de DWU | Percentagem | Máximo
Sim | Microsoft.Sql/servers/databases | dwu_limit | Limite de DWU | Contagem | Máximo
Sim | Microsoft.Sql/servers/databases | dwu_used | DWU usado | Contagem | Máximo
Sim | Microsoft.Sql/servers/databases | local_tempdb_usage_percent | Porcentagem de tempdb local | Percentagem | Média
Sim | Microsoft.Sql/servers/databases | log_write_percent | Percentual de e/s de log | Percentagem | Média
Sim | Microsoft.Sql/servers/databases | memory_usage_percent | Porcentagem de memória | Percentagem | Máximo
Sim | Microsoft.Sql/servers/databases | physical_data_read_percent | Percentagem de ES de Dados | Percentagem | Média
Sim | Microsoft.Sql/servers/databases | sessions_percent | Porcentagem de sessões | Percentagem | Média
Sim | Microsoft.Sql/servers/databases | armazenamento | Espaço de dados usado | Bytes | Máximo
Sim | Microsoft.Sql/servers/databases | storage_percent | Porcentagem de espaço de dados usada | Percentagem | Máximo
Sim | Microsoft.Sql/servers/databases | tempdb_data_size | Tamanho do arquivo de dados tempdb em kilobytes | Contagem | Máximo
Sim | Microsoft.Sql/servers/databases | tempdb_log_size | Tamanho do arquivo de log de tempdb em kilobytes | Contagem | Máximo
Sim | Microsoft.Sql/servers/databases | tempdb_log_used_percent | Log de porcentagem de tempdb usado | Percentagem | Máximo
Sim | Microsoft.Sql/servers/databases | workers_percent | Porcentagem de trabalhadores | Percentagem | Média
Sim | Microsoft.Sql/servers/databases | xtp_storage_percent | Porcentagem de armazenamento OLTP na memória | Percentagem | Média
Sim | Microsoft.Sql/servers/elasticPools | allocated_data_storage | Espaço de dados alocado | Bytes | Média
Sim | Microsoft.Sql/servers/elasticPools | allocated_data_storage_percent | Porcentagem alocada de espaço de dados | Percentagem | Máximo
Sim | Microsoft.Sql/servers/elasticPools | cpu_limit | Limite de CPU | Contagem | Média
Sim | Microsoft.Sql/servers/elasticPools | cpu_percent | Percentagem de CPU | Percentagem | Média
Sim | Microsoft.Sql/servers/elasticPools | cpu_used | CPU usada | Contagem | Média
Não | Microsoft.Sql/servers/elasticPools | database_allocated_data_storage | Espaço de dados alocado | Bytes | Média
Não | Microsoft.Sql/servers/elasticPools | database_cpu_limit | Limite de CPU | Contagem | Média
Não | Microsoft.Sql/servers/elasticPools | database_cpu_percent | Percentagem de CPU | Percentagem | Média
Não | Microsoft.Sql/servers/elasticPools | database_cpu_used | CPU usada | Contagem | Média
Não | Microsoft.Sql/servers/elasticPools | database_dtu_consumption_percent | Percentagem de DTU | Percentagem | Média
Não | Microsoft.Sql/servers/elasticPools | database_eDTU_used | eDTU usado | Contagem | Média
Não | Microsoft.Sql/servers/elasticPools | database_log_write_percent | Percentual de e/s de log | Percentagem | Média
Não | Microsoft.Sql/servers/elasticPools | database_physical_data_read_percent | Percentagem de ES de Dados | Percentagem | Média
Não | Microsoft.Sql/servers/elasticPools | database_sessions_percent | Porcentagem de sessões | Percentagem | Média
Não | Microsoft.Sql/servers/elasticPools | database_storage_used | Espaço de dados usado | Bytes | Média
Não | Microsoft.Sql/servers/elasticPools | database_workers_percent | Porcentagem de trabalhadores | Percentagem | Média
Sim | Microsoft.Sql/servers/elasticPools | dtu_consumption_percent | Percentagem de DTU | Percentagem | Média
Sim | Microsoft.Sql/servers/elasticPools | eDTU_limit | limite de eDTU | Contagem | Média
Sim | Microsoft.Sql/servers/elasticPools | eDTU_used | eDTU usado | Contagem | Média
Sim | Microsoft.Sql/servers/elasticPools | log_write_percent | Percentual de e/s de log | Percentagem | Média
Sim | Microsoft.Sql/servers/elasticPools | physical_data_read_percent | Percentagem de ES de Dados | Percentagem | Média
Sim | Microsoft.Sql/servers/elasticPools | sessions_percent | Porcentagem de sessões | Percentagem | Média
Sim | Microsoft.Sql/servers/elasticPools | storage_limit | Tamanho máximo de dados | Bytes | Média
Sim | Microsoft.Sql/servers/elasticPools | storage_percent | Porcentagem de espaço de dados usada | Percentagem | Média
Sim | Microsoft.Sql/servers/elasticPools | storage_used | Espaço de dados usado | Bytes | Média
Sim | Microsoft.Sql/servers/elasticPools | tempdb_data_size | Tamanho do arquivo de dados tempdb em kilobytes | Contagem | Máximo
Sim | Microsoft.Sql/servers/elasticPools | tempdb_log_size | Tamanho do arquivo de log de tempdb em kilobytes | Contagem | Máximo
Sim | Microsoft.Sql/servers/elasticPools | tempdb_log_used_percent | Log de porcentagem de tempdb usado | Percentagem | Máximo
Sim | Microsoft.Sql/servers/elasticPools | workers_percent | Porcentagem de trabalhadores | Percentagem | Média
Sim | Microsoft.Sql/servers/elasticPools | xtp_storage_percent | Porcentagem de armazenamento OLTP na memória | Percentagem | Média
Sim | Microsoft.Storage/storageAccounts | Disponibilidade | Disponibilidade | Percentagem | Média
Sim | Microsoft.Storage/storageAccounts | Saída | Saída | Bytes | Total
Sim | Microsoft.Storage/storageAccounts | Entrada | Entrada | Bytes | Total
Sim | Microsoft.Storage/storageAccounts | SuccessE2ELatency | Latência de E2E Com Êxito | Milissegundos | Média
Sim | Microsoft.Storage/storageAccounts | SuccessServerLatency | Latência de Servidor Com Êxito | Milissegundos | Média
Sim | Microsoft.Storage/storageAccounts | Transações | Transações | Contagem | Total
Não | Microsoft.Storage/storageAccounts | UsedCapacity | Capacidade utilizada | Bytes | Média
Sim | Microsoft.Storage/storageAccounts/blobServices | Disponibilidade | Disponibilidade | Percentagem | Média
Não | Microsoft.Storage/storageAccounts/blobServices | BlobCapacity | Capacidade do Blob | Bytes | Média
Não | Microsoft.Storage/storageAccounts/blobServices | BlobCount | Contagem de Blobs | Contagem | Média
Sim | Microsoft.Storage/storageAccounts/blobServices | ContainerCount | Contagem do Contentor de Blobs | Contagem | Média
Sim | Microsoft.Storage/storageAccounts/blobServices | Saída | Saída | Bytes | Total
Não | Microsoft.Storage/storageAccounts/blobServices | IndexCapacity | Capacidade de Índice | Bytes | Média
Sim | Microsoft.Storage/storageAccounts/blobServices | Entrada | Entrada | Bytes | Total
Sim | Microsoft.Storage/storageAccounts/blobServices | SuccessE2ELatency | Latência de E2E Com Êxito | Milissegundos | Média
Sim | Microsoft.Storage/storageAccounts/blobServices | SuccessServerLatency | Latência de Servidor Com Êxito | Milissegundos | Média
Sim | Microsoft.Storage/storageAccounts/blobServices | Transações | Transações | Contagem | Total
Sim | Microsoft.Storage/storageAccounts/fileServices | Disponibilidade | Disponibilidade | Percentagem | Média
Sim | Microsoft.Storage/storageAccounts/fileServices | Saída | Saída | Bytes | Total
Não | Microsoft.Storage/storageAccounts/fileServices | Capacidade de | Capacidade do arquivo | Bytes | Média
Não | Microsoft.Storage/storageAccounts/fileServices | FileCount | Contagem de arquivos | Contagem | Média
Não | Microsoft.Storage/storageAccounts/fileServices | FileShareCount | Contagem de compartilhamento de arquivos | Contagem | Média
Não | Microsoft.Storage/storageAccounts/fileServices | FileShareQuota | Tamanho da cota de compartilhamento de arquivos | Bytes | Média
Não | Microsoft.Storage/storageAccounts/fileServices | FileShareSnapshotCount | Contagem de instantâneos de compartilhamento de arquivos | Contagem | Média
Não | Microsoft.Storage/storageAccounts/fileServices | FileShareSnapshotSize | Tamanho do instantâneo de compartilhamento de arquivos | Bytes | Média
Sim | Microsoft.Storage/storageAccounts/fileServices | Entrada | Entrada | Bytes | Total
Sim | Microsoft.Storage/storageAccounts/fileServices | SuccessE2ELatency | Latência de E2E Com Êxito | Milissegundos | Média
Sim | Microsoft.Storage/storageAccounts/fileServices | SuccessServerLatency | Latência de Servidor Com Êxito | Milissegundos | Média
Sim | Microsoft.Storage/storageAccounts/fileServices | Transações | Transações | Contagem | Total
Sim | Microsoft.Storage/storageAccounts/queueServices | Disponibilidade | Disponibilidade | Percentagem | Média
Sim | Microsoft.Storage/storageAccounts/queueServices | Saída | Saída | Bytes | Total
Sim | Microsoft.Storage/storageAccounts/queueServices | Entrada | Entrada | Bytes | Total
Sim | Microsoft.Storage/storageAccounts/queueServices | QueueCapacity | Capacidade de Fila | Bytes | Média
Sim | Microsoft.Storage/storageAccounts/queueServices | QueueCount | Contagem de Filas | Contagem | Média
Sim | Microsoft.Storage/storageAccounts/queueServices | QueueMessageCount | Contagem de Mensagens em Fila | Contagem | Média
Sim | Microsoft.Storage/storageAccounts/queueServices | SuccessE2ELatency | Latência de E2E Com Êxito | Milissegundos | Média
Sim | Microsoft.Storage/storageAccounts/queueServices | SuccessServerLatency | Latência de Servidor Com Êxito | Milissegundos | Média
Sim | Microsoft.Storage/storageAccounts/queueServices | Transações | Transações | Contagem | Total
Sim | Microsoft.Storage/storageAccounts/tableServices | Disponibilidade | Disponibilidade | Percentagem | Média
Sim | Microsoft.Storage/storageAccounts/tableServices | Saída | Saída | Bytes | Total
Sim | Microsoft.Storage/storageAccounts/tableServices | Entrada | Entrada | Bytes | Total
Sim | Microsoft.Storage/storageAccounts/tableServices | SuccessE2ELatency | Latência de E2E Com Êxito | Milissegundos | Média
Sim | Microsoft.Storage/storageAccounts/tableServices | SuccessServerLatency | Latência de Servidor Com Êxito | Milissegundos | Média
Sim | Microsoft.Storage/storageAccounts/tableServices | TableCapacity | Capacidade de Tabelas | Bytes | Média
Sim | Microsoft.Storage/storageAccounts/tableServices | TableCount | Contagem de Tabelas | Contagem | Média
Sim | Microsoft.Storage/storageAccounts/tableServices | TableEntityCount | Contagem de Entidade de Tabelas | Contagem | Média
Sim | Microsoft.Storage/storageAccounts/tableServices | Transações | Transações | Contagem | Total
Sim | Microsoft. StorageCache/caches | ClientIOPS | IOPS de cliente total | Contagem | Média
Sim | Microsoft. StorageCache/caches | ClientLatency | Latência média do cliente | Milissegundos | Média
Sim | Microsoft. StorageCache/caches | ClientLockIOPS | IOPS de bloqueio de cliente | CountPerSecond | Média
Sim | Microsoft. StorageCache/caches | ClientMetadataReadIOPS | IOPS de leitura de metadados do cliente | CountPerSecond | Média
Sim | Microsoft. StorageCache/caches | ClientMetadataWriteIOPS | IOPS de gravação de metadados do cliente | CountPerSecond | Média
Sim | Microsoft. StorageCache/caches | ClientReadIOPS | IOPS de leitura do cliente | CountPerSecond | Média
Sim | Microsoft. StorageCache/caches | ClientReadThroughput | Média de taxa de transferência de leitura do cache | BytesPerSecond | Média
Sim | Microsoft. StorageCache/caches | ClientWriteIOPS | IOPS de gravação de cliente | CountPerSecond | Média
Sim | Microsoft. StorageCache/caches | ClientWriteThroughput | Taxa de transferência média de gravação no cache | BytesPerSecond | Média
Sim | Microsoft. StorageCache/caches | StorageTargetAsyncWriteThroughput | Taxa de transferência de gravação assíncrona StorageTarget | BytesPerSecond | Média
Sim | Microsoft. StorageCache/caches | StorageTargetFillThroughput | Taxa de transferência de preenchimento do StorageTarget | BytesPerSecond | Média
Sim | Microsoft. StorageCache/caches | StorageTargetHealth | Integridade do destino de armazenamento | Contagem | Média
Sim | Microsoft. StorageCache/caches | StorageTargetIOPS | Total de IOPS de StorageTarget | Contagem | Média
Sim | Microsoft. StorageCache/caches | StorageTargetLatency | Latência de StorageTarget | Milissegundos | Média
Sim | Microsoft. StorageCache/caches | StorageTargetMetadataReadIOPS | IOPS de leitura de metadados StorageTarget | CountPerSecond | Média
Sim | Microsoft. StorageCache/caches | StorageTargetMetadataWriteIOPS | IOPS de gravação de metadados StorageTarget | CountPerSecond | Média
Sim | Microsoft. StorageCache/caches | StorageTargetReadAheadThroughput | Taxa de transferência de leitura antecipada de StorageTarget | BytesPerSecond | Média
Sim | Microsoft. StorageCache/caches | StorageTargetReadIOPS | IOPS de leitura StorageTarget | CountPerSecond | Média
Sim | Microsoft. StorageCache/caches | StorageTargetSyncWriteThroughput | Taxa de transferência de gravação síncrona StorageTarget | BytesPerSecond | Média
Sim | Microsoft. StorageCache/caches | StorageTargetTotalReadThroughput | Taxa de transferência de leitura total de StorageTarget | BytesPerSecond | Média
Sim | Microsoft. StorageCache/caches | StorageTargetTotalWriteThroughput | Taxa de transferência total de gravação de StorageTarget | BytesPerSecond | Média
Sim | Microsoft. StorageCache/caches | StorageTargetWriteIOPS | IOPS de gravação StorageTarget | Contagem | Média
Sim | Microsoft. StorageCache/caches | Tempo de atividade | Tempo de atividade | Contagem | Média
Sim | microsoft.storagesync/storageSyncServices | ServerSyncSessionResult | Resultado da sessão de sincronização | Contagem | Média
Sim | microsoft.storagesync/storageSyncServices | StorageSyncBatchTransferredFileBytes | Bytes sincronizados | Bytes | Total
Sim | microsoft.storagesync/storageSyncServices | StorageSyncRecalledNetworkBytesByApplication | Tamanho de recall de camadas de nuvem por aplicativo | Bytes | Total
Sim | microsoft.storagesync/storageSyncServices | StorageSyncRecalledTotalNetworkBytes | Tamanho de recall em camadas de nuvem | Bytes | Total
Sim | microsoft.storagesync/storageSyncServices | StorageSyncRecallIOTotalSizeBytes | Recall de camadas de nuvem | Bytes | Total
Sim | microsoft.storagesync/storageSyncServices | StorageSyncRecallThroughputBytesPerSecond | Taxa de transferência de recall em camadas de nuvem | BytesPerSecond | Média
Sim | microsoft.storagesync/storageSyncServices | StorageSyncServerHeartbeat | Status online do servidor | Contagem | Máximo
Sim | microsoft.storagesync/storageSyncServices | StorageSyncSyncSessionAppliedFilesCount | Arquivos sincronizados | Contagem | Total
Sim | microsoft.storagesync/storageSyncServices | StorageSyncSyncSessionPerItemErrorsCount | Arquivos não sincronizando | Contagem | Total
Sim | Microsoft. storagesync/storageSyncServices/registeredServers | ServerHeartbeat | Status online do servidor | Contagem | Máximo
Sim | Microsoft. storagesync/storageSyncServices/registeredServers | ServerRecallIOTotalSizeBytes | Recall de camadas de nuvem | Bytes | Total
Sim | Microsoft. storagesync/storageSyncServices/syncGroups | SyncGroupBatchTransferredFileBytes | Bytes sincronizados | Bytes | Total
Sim | Microsoft. storagesync/storageSyncServices/syncGroups | SyncGroupSyncSessionAppliedFilesCount | Arquivos sincronizados | Contagem | Total
Sim | Microsoft. storagesync/storageSyncServices/syncGroups | SyncGroupSyncSessionPerItemErrorsCount | Arquivos não sincronizando | Contagem | Total
Sim | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointBatchTransferredFileBytes | Bytes sincronizados | Bytes | Total
Sim | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointSyncSessionAppliedFilesCount | Arquivos sincronizados | Contagem | Total
Sim | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointSyncSessionPerItemErrorsCount | Arquivos não sincronizando | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutFailedRequests | Falha no pedido de funções | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutInputEvents | Eventos de Função | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutRequests | Pedidos de Função | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | ConversionErrors | Erros de Conversão de Dados | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | DeserializationError | Erros de Desserialização de entrada | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | DroppedOrAdjustedEvents | Eventos fora de ordem | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | EarlyInputEvents | Eventos de Entrada Antigos | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | Erros | Erros de Tempo de Execução | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | InputEventBytes | Bytes de Evento de Entrada | Bytes | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | InputEvents | Eventos de Entrada | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | InputEventsSourcesBacklogged | Eventos de Entrada Pendentes | Contagem | Máximo
Sim | Microsoft.StreamAnalytics/streamingjobs | InputEventsSourcesPerSecond | Origens de Entrada Recebidas | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | LateInputEvents | Eventos de Entrada atrasados | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | OutputEvents | Eventos de Saída | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | OutputWatermarkDelaySeconds | Atraso de Marca de Água | Segundos | Máximo
Sim | Microsoft.StreamAnalytics/streamingjobs | ResourceUtilization | % de utilização SU | Percentagem | Máximo
Sim | Microsoft.VMwareCloudSimple/virtualMachines | Bytes de leitura de disco | Bytes de leitura de disco | Bytes | Total
Sim | Microsoft.VMwareCloudSimple/virtualMachines | Operações de leitura de disco/s | Operações de leitura de disco/s | CountPerSecond | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | Bytes de gravação no disco | Bytes de gravação no disco | Bytes | Total
Sim | Microsoft.VMwareCloudSimple/virtualMachines | Operações de gravação de disco/s | Operações de gravação de disco/s | CountPerSecond | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | DiskReadBytesPerSecond | Bytes de leitura de disco/s | BytesPerSecond | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | DiskReadLatency | Latência de leitura de disco | Milissegundos | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | DiskReadOperations | Operações de leitura de disco | Contagem | Total
Sim | Microsoft.VMwareCloudSimple/virtualMachines | DiskWriteBytesPerSecond | Bytes de gravação no disco/s | BytesPerSecond | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | DiskWriteLatency | Latência de gravação de disco | Milissegundos | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | DiskWriteOperations | Operações de gravação de disco | Contagem | Total
Sim | Microsoft.VMwareCloudSimple/virtualMachines | MemoryActive | Memória ativa | Bytes | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | MemoryGranted | Memória concedida | Bytes | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | MemoryUsed | Memória usada | Bytes | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | Entrada na Rede | Entrada na Rede | Bytes | Total
Sim | Microsoft.VMwareCloudSimple/virtualMachines | Saída da Rede | Saída da Rede | Bytes | Total
Sim | Microsoft.VMwareCloudSimple/virtualMachines | NetworkInBytesPerSecond | Rede em bytes/s | BytesPerSecond | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | NetworkOutBytesPerSecond | Bytes de saída de rede/s | BytesPerSecond | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | Percentagem da CPU | Percentagem da CPU | Percentagem | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | PercentageCpuReady | Percentual de CPU pronta | Milissegundos | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | ActiveRequests | Solicitações ativas | Contagem | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | AverageResponseTime | Tempo de Resposta Médio | Segundos | Média
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | BytesReceived | Entrada de Dados | Bytes | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | BytesSent | Saída de Dados | Bytes | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | CpuPercentage | Percentagem de CPU | Percentagem | Média
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | DiskQueueLength | Comprimento da fila de disco | Contagem | Média
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | Http101 | Http 101 | Contagem | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | Http2xx | 2xx http | Contagem | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | Http3xx | 3xx http | Contagem | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | Http401 | Http 401 | Contagem | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | Http403 | Http 403 | Contagem | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | Http404 | Http 404 | Contagem | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | Http406 | Http 406 | Contagem | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | Http4xx | 4xx http | Contagem | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | Http5xx | Erros do servidor http | Contagem | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | HttpQueueLength | Comprimento da fila http | Contagem | Média
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | LargeAppServicePlanInstances | Trabalhos grandes do plano do serviço de aplicativo | Contagem | Média
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances | Trabalhos do plano do serviço de aplicativo médio | Contagem | Média
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | MemoryPercentage | Porcentagem de memória | Percentagem | Média
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | Pedidos | Pedidos | Contagem | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | SmallAppServicePlanInstances | Trabalhos do plano do serviço de aplicativo pequeno | Contagem | Média
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | TotalFrontEnds | Total de front-ends | Contagem | Média
Sim | Microsoft.Web/hostingEnvironments/workerPools | CpuPercentage | Percentagem de CPU | Percentagem | Média
Sim | Microsoft.Web/hostingEnvironments/workerPools | MemoryPercentage | Porcentagem de memória | Percentagem | Média
Sim | Microsoft.Web/hostingEnvironments/workerPools | WorkersAvailable | Trabalhos disponíveis | Contagem | Média
Sim | Microsoft.Web/hostingEnvironments/workerPools | WorkersTotal | Total de trabalhadores | Contagem | Média
Sim | Microsoft.Web/hostingEnvironments/workerPools | WorkersUsed | Trabalhadores usados | Contagem | Média
Sim | Microsoft.Web/serverfarms | BytesReceived | Entrada de Dados | Bytes | Total
Sim | Microsoft.Web/serverfarms | BytesSent | Saída de Dados | Bytes | Total
Sim | Microsoft.Web/serverfarms | CpuPercentage | Percentagem de CPU | Percentagem | Média
Sim | Microsoft.Web/serverfarms | DiskQueueLength | Comprimento da fila de disco | Contagem | Média
Sim | Microsoft.Web/serverfarms | HttpQueueLength | Comprimento da fila http | Contagem | Média
Sim | Microsoft.Web/serverfarms | MemoryPercentage | Porcentagem de memória | Percentagem | Média
Sim | Microsoft.Web/serverfarms | TcpCloseWait | Espera TCP de fechamento | Contagem | Média
Sim | Microsoft.Web/serverfarms | TcpClosing | Fechamento de TCP | Contagem | Média
Sim | Microsoft.Web/serverfarms | TcpEstablished | TCP estabelecido | Contagem | Média
Sim | Microsoft.Web/serverfarms | TcpFinWait1 | Espera de TCP fin 1 | Contagem | Média
Sim | Microsoft.Web/serverfarms | TcpFinWait2 | Espera de TCP Fin 2 | Contagem | Média
Sim | Microsoft.Web/serverfarms | TcpLastAck | Última confirmação TCP | Contagem | Média
Sim | Microsoft.Web/serverfarms | TcpSynReceived | SYN TCP recebido | Contagem | Média
Sim | Microsoft.Web/serverfarms | TcpSynSent | SYN TCP enviado | Contagem | Média
Sim | Microsoft.Web/serverfarms | TcpTimeWait | Espera de tempo TCP | Contagem | Média
Sim | Microsoft.Web/sites | AppConnections | Ligações | Contagem | Média
Sim | Microsoft.Web/sites | AverageMemoryWorkingSet | Conjunto de trabalho de memória média | Bytes | Média
Sim | Microsoft.Web/sites | AverageResponseTime | Tempo de Resposta Médio | Segundos | Média
Sim | Microsoft.Web/sites | BytesReceived | Entrada de Dados | Bytes | Total
Sim | Microsoft.Web/sites | BytesSent | Saída de Dados | Bytes | Total
Sim | Microsoft.Web/sites | CpuTime | Tempo de CPU | Segundos | Total
Sim | Microsoft.Web/sites | CurrentAssemblies | Assemblies atuais | Contagem | Média
Sim | Microsoft.Web/sites | FunctionExecutionCount | Contagem de execução de função | Contagem | Total
Sim | Microsoft.Web/sites | FunctionExecutionUnits | Unidades de execução de função | Contagem | Total
Sim | Microsoft.Web/sites | Gen0Collections | Coletas de lixo de Gen 0 | Contagem | Total
Sim | Microsoft.Web/sites | Gen1Collections | Coletas de lixo da Gen 1 | Contagem | Total
Sim | Microsoft.Web/sites | Gen2Collections | Coletas de lixo da Gen 2 | Contagem | Total
Sim | Microsoft.Web/sites | Alça | Contagem de Identificadores | Contagem | Média
Sim | Microsoft.Web/sites | HealthCheckStatus | Status da verificação de integridade | Contagem | Média
Sim | Microsoft.Web/sites | Http101 | Http 101 | Contagem | Total
Sim | Microsoft.Web/sites | Http2xx | 2xx http | Contagem | Total
Sim | Microsoft.Web/sites | Http3xx | 3xx http | Contagem | Total
Sim | Microsoft.Web/sites | Http401 | Http 401 | Contagem | Total
Sim | Microsoft.Web/sites | Http403 | Http 403 | Contagem | Total
Sim | Microsoft.Web/sites | Http404 | Http 404 | Contagem | Total
Sim | Microsoft.Web/sites | Http406 | Http 406 | Contagem | Total
Sim | Microsoft.Web/sites | Http4xx | 4xx http | Contagem | Total
Sim | Microsoft.Web/sites | Http5xx | Erros do servidor http | Contagem | Total
Sim | Microsoft.Web/sites | HttpResponsetime | Tempo de resposta | Segundos | Média
Sim | Microsoft.Web/sites | IoOtherBytesPerSecond | E/s outros bytes por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites | IoOtherOperationsPerSecond | E/s outras operações por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites | IoReadBytesPerSecond | Bytes de leitura de e/s por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites | IoReadOperationsPerSecond | Operações de leitura de e/s por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites | IoWriteBytesPerSecond | Bytes de gravação de e/s por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites | IoWriteOperationsPerSecond | Operações de gravação de e/s por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites | MemoryWorkingSet | Conjunto de trabalho da memória | Bytes | Média
Sim | Microsoft.Web/sites | PrivateBytes | Bytes Privados | Bytes | Média
Sim | Microsoft.Web/sites | Pedidos | Pedidos | Contagem | Total
Sim | Microsoft.Web/sites | RequestsInApplicationQueue | Solicitações na fila de aplicativos | Contagem | Média
Sim | Microsoft.Web/sites | Threads | Número de Threads | Contagem | Média
Sim | Microsoft.Web/sites | TotalAppDomains | Total de domínios de aplicativo | Contagem | Média
Sim | Microsoft.Web/sites | TotalAppDomainsUnloaded | Total de domínios de aplicativo descarregados | Contagem | Média
Sim | Microsoft.Web/sites/slots | AppConnections | Ligações | Contagem | Média
Sim | Microsoft.Web/sites/slots | AverageMemoryWorkingSet | Conjunto de trabalho de memória média | Bytes | Média
Sim | Microsoft.Web/sites/slots | AverageResponseTime | Tempo de Resposta Médio | Segundos | Média
Sim | Microsoft.Web/sites/slots | BytesReceived | Entrada de Dados | Bytes | Total
Sim | Microsoft.Web/sites/slots | BytesSent | Saída de Dados | Bytes | Total
Sim | Microsoft.Web/sites/slots | CpuTime | Tempo de CPU | Segundos | Total
Sim | Microsoft.Web/sites/slots | CurrentAssemblies | Assemblies atuais | Contagem | Média
Sim | Microsoft.Web/sites/slots | FunctionExecutionCount | Contagem de execução de função | Contagem | Total
Sim | Microsoft.Web/sites/slots | FunctionExecutionUnits | Unidades de execução de função | Contagem | Total
Sim | Microsoft.Web/sites/slots | Gen0Collections | Coletas de lixo de Gen 0 | Contagem | Total
Sim | Microsoft.Web/sites/slots | Gen1Collections | Coletas de lixo da Gen 1 | Contagem | Total
Sim | Microsoft.Web/sites/slots | Gen2Collections | Coletas de lixo da Gen 2 | Contagem | Total
Sim | Microsoft.Web/sites/slots | Alça | Contagem de Identificadores | Contagem | Média
Sim | Microsoft.Web/sites/slots | HealthCheckStatus | Status da verificação de integridade | Contagem | Média
Sim | Microsoft.Web/sites/slots | Http101 | Http 101 | Contagem | Total
Sim | Microsoft.Web/sites/slots | Http2xx | 2xx http | Contagem | Total
Sim | Microsoft.Web/sites/slots | Http3xx | 3xx http | Contagem | Total
Sim | Microsoft.Web/sites/slots | Http401 | Http 401 | Contagem | Total
Sim | Microsoft.Web/sites/slots | Http403 | Http 403 | Contagem | Total
Sim | Microsoft.Web/sites/slots | Http404 | Http 404 | Contagem | Total
Sim | Microsoft.Web/sites/slots | Http406 | Http 406 | Contagem | Total
Sim | Microsoft.Web/sites/slots | Http4xx | 4xx http | Contagem | Total
Sim | Microsoft.Web/sites/slots | Http5xx | Erros do servidor http | Contagem | Total
Sim | Microsoft.Web/sites/slots | HttpResponsetime | Tempo de resposta | Segundos | Média
Sim | Microsoft.Web/sites/slots | IoOtherBytesPerSecond | E/s outros bytes por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites/slots | IoOtherOperationsPerSecond | E/s outras operações por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites/slots | IoReadBytesPerSecond | Bytes de leitura de e/s por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites/slots | IoReadOperationsPerSecond | Operações de leitura de e/s por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites/slots | IoWriteBytesPerSecond | Bytes de gravação de e/s por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites/slots | IoWriteOperationsPerSecond | Operações de gravação de e/s por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites/slots | MemoryWorkingSet | Conjunto de trabalho da memória | Bytes | Média
Sim | Microsoft.Web/sites/slots | PrivateBytes | Bytes Privados | Bytes | Média
Sim | Microsoft.Web/sites/slots | Pedidos | Pedidos | Contagem | Total
Sim | Microsoft.Web/sites/slots | RequestsInApplicationQueue | Solicitações na fila de aplicativos | Contagem | Média
Sim | Microsoft.Web/sites/slots | Threads | Número de Threads | Contagem | Média
Sim | Microsoft.Web/sites/slots | TotalAppDomains | Total de domínios de aplicativo | Contagem | Média
Sim | Microsoft.Web/sites/slots | TotalAppDomainsUnloaded | Total de domínios de aplicativo descarregados | Contagem | Média
