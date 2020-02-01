---
title: Azure Monitor supported metrics by resource type
description: List of metrics available for each resource type with Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 12/18/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 0210317ef74433b740feb043a1cc4f1f9bc2ef57
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901149"
---
# <a name="supported-metrics-with-azure-monitor"></a>Supported metrics with Azure Monitor

Azure Monitor provides several ways to interact with metrics, including charting them in the portal, accessing them through the REST API, or querying them using PowerShell or CLI. Below is a complete list of all metrics currently available with Azure Monitor's metric pipeline. Other metrics may be available in the portal or using legacy APIs. This list below only includes metrics available using the consolidated Azure Monitor metric pipeline. To query for and access these metrics please use the [2018-01-01 api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Atualmente, o envio de métricas multidimensionais através das definições de diagnóstico não é suportado. As métricas com dimensões são exportadas como métricas dimensionais simples e agregadas em valores de dimensões.
>
> *Por exemplo*: a métrica “Mensagens Recebidas” num hub do Hub de Eventos pode ser explorada e representada ao nível da linha. No entanto, se for exportada através das definições de diagnóstico, a métrica será representada como todas as mensagens recebidas em todas as filas do hub do Hub de Eventos.
>
> For a list of platform metrics exportable via diagnostic settings, see [this article](metrics-supported-export-diagnostic-settings.md).




## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|qpu_metric|QPU|Contagem|Média|QPU. Range 0-100 for S1, 0-200 for S2 and 0-400 for S4|ServerResourceType|
|memory_metric|Memória|Bytes|Média|A memória. Range 0-25 GB for S1, 0-50 GB for S2 and 0-100 GB for S4|ServerResourceType|
|private_bytes_metric|Bytes Privados|Bytes|Média|Private bytes.|ServerResourceType|
|virtual_bytes_metric|Virtual Bytes|Bytes|Média|Virtual bytes.|ServerResourceType|
|TotalConnectionRequests|Total Connection Requests|Contagem|Média|Total connection requests. These are arrivals.|ServerResourceType|
|SuccessfullConnectionsPerSec|Successful Connections Per Sec|CountPerSecond|Média|Rate of successful connection completions.|ServerResourceType|
|TotalConnectionFailures|Total Connection Failures|Contagem|Média|Total failed connection attempts.|ServerResourceType|
|CurrentUserSessions|Current User Sessions|Contagem|Média|Current number of user sessions established.|ServerResourceType|
|QueryPoolBusyThreads|Query Pool Busy Threads|Contagem|Média|Number of busy threads in the query thread pool.|ServerResourceType|
|CommandPoolJobQueueLength|Command Pool Job Queue Length|Contagem|Média|Number of jobs in the queue of the command thread pool.|ServerResourceType|
|ProcessingPoolJobQueueLength|Processing Pool Job Queue Length|Contagem|Média|Number of non-I/O jobs in the queue of the processing thread pool.|ServerResourceType|
|CurrentConnections|Connection: Current connections|Contagem|Média|Current number of client connections established.|ServerResourceType|
|CleanerCurrentPrice|Memory: Cleaner Current Price|Contagem|Média|Current price of memory, $/byte/time, normalized to 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Memory: Cleaner Memory shrinkable|Bytes|Média|Amount of memory, in bytes, subject to purging by the background cleaner.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memory: Cleaner Memory nonshrinkable|Bytes|Média|Amount of memory, in bytes, not subject to purging by the background cleaner.|ServerResourceType|
|MemoryUsage|Memory: Memory Usage|Bytes|Média|Memory usage of the server process as used in calculating cleaner memory price. Equal to counter Process\PrivateBytes plus the size of memory-mapped data, ignoring any memory which was mapped or allocated by the xVelocity in-memory analytics engine (VertiPaq) in excess of the xVelocity engine Memory Limit.|ServerResourceType|
|MemoryLimitHard|Memory: Memory Limit Hard|Bytes|Média|Hard memory limit, from configuration file.|ServerResourceType|
|MemoryLimitHigh|Memory: Memory Limit High|Bytes|Média|High memory limit, from configuration file.|ServerResourceType|
|MemoryLimitLow|Memory: Memory Limit Low|Bytes|Média|Low memory limit, from configuration file.|ServerResourceType|
|MemoryLimitVertiPaq|Memory: Memory Limit VertiPaq|Bytes|Média|In-memory limit, from configuration file.|ServerResourceType|
|Quota|Memory: Quota|Bytes|Média|Cota de memória atual, em bytes. A cota de memória também é conhecida como concessão de memória ou reserva de memória.|ServerResourceType|
|QuotaBlocked|Memória: cota bloqueada|Contagem|Média|Número atual de solicitações de cota que são bloqueadas até que outras cotas de memória sejam liberadas.|ServerResourceType|
|VertiPaqNonpaged|Memória: VertiPaq não paginável|Bytes|Média|Bytes de memória bloqueados no conjunto de trabalho para uso pelo mecanismo na memória.|ServerResourceType|
|VertiPaqPaged|Memória: VertiPaq paginável|Bytes|Média|Bytes de memória paginável em uso para dados na memória.|ServerResourceType|
|RowsReadPerSec|Processamento: linhas lidas por segundo|CountPerSecond|Média|Taxa de linhas lidas de todos os bancos de dados relacionais.|ServerResourceType|
|RowsConvertedPerSec|Processamento: linhas convertidas por segundo|CountPerSecond|Média|Taxa de linhas convertidas durante o processamento.|ServerResourceType|
|RowsWrittenPerSec|Processamento: linhas gravadas por segundo|CountPerSecond|Média|Taxa de linhas gravadas durante o processamento.|ServerResourceType|
|CommandPoolBusyThreads|Threads: threads ocupados do pool de comandos|Contagem|Média|Número de threads ocupados no pool de threads de comando.|ServerResourceType|
|CommandPoolIdleThreads|Threads: threads ociosos do pool de comandos|Contagem|Média|Número de threads ociosos no pool de threads de comando.|ServerResourceType|
|LongParsingBusyThreads|Threads: threads ocupados da análise longa|Contagem|Média|Número de threads ocupados no pool de threads da análise longa.|ServerResourceType|
|LongParsingIdleThreads|Threads: threads ociosos da análise longa|Contagem|Média|Número de threads ociosos no pool de threads da análise longa.|ServerResourceType|
|LongParsingJobQueueLength|Threads: comprimento da fila de trabalhos de análise longa|Contagem|Média|Número de trabalhos na fila do pool de threads de análise longa.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Threads: processando threads de trabalho de e/s de pool ocupado|Contagem|Média|Número de threads executando trabalhos de e/s no pool de threads de processamento.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Threads: threads de não-e/s ocupados do pool de processamento|Contagem|Média|Número de threads que executam trabalhos que não são de e/s no pool de threads de processamento.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Threads: tamanho da fila de trabalhos de e/s do pool de processamento|Contagem|Média|Número de trabalhos de e/s na fila do pool de threads de processamento.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Threads: processando threads de trabalho de e/s de pool ocioso|Contagem|Média|Número de threads ociosos para trabalhos de e/s no pool de threads de processamento.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Threads: threads de não-e/s ociosos do pool de processamento|Contagem|Média|Número de threads ociosos no pool de threads de processamento dedicados a trabalhos que não são de e/s.|ServerResourceType|
|QueryPoolIdleThreads|Threads: threads ociosos do pool de consulta|Contagem|Média|Número de threads ociosos para trabalhos de e/s no pool de threads de processamento.|ServerResourceType|
|QueryPoolJobQueueLength|Threads: fila de trabalhos do pool de consultas comprimento da|Contagem|Média|Número de trabalhos na fila do pool de threads de consulta.|ServerResourceType|
|ShortParsingBusyThreads|Threads: threads ocupados da análise resumida|Contagem|Média|Número de threads ocupados no pool de threads da análise resumida.|ServerResourceType|
|ShortParsingIdleThreads|Threads: threads ociosos da análise resumida|Contagem|Média|Número de threads ociosos no pool de threads da análise resumida.|ServerResourceType|
|ShortParsingJobQueueLength|Threads: comprimento da fila de trabalhos da análise curta|Contagem|Média|Número de trabalhos na fila do pool de threads da análise resumida.|ServerResourceType|
|memory_thrashing_metric|Goleada de memória|Percentagem|Média|Média de ultrapaginação de memória.|ServerResourceType|
|mashup_engine_qpu_metric|QPU do mecanismo M|Contagem|Média|Uso de QPU por processos do mecanismo mashup|ServerResourceType|
|mashup_engine_memory_metric|Memória do mecanismo M|Bytes|Média|Uso de memória pelos processos do mecanismo mashup|ServerResourceType|
|mashup_engine_private_bytes_metric|Bytes privados do mecanismo M|Bytes|Média|Uso de bytes privados por processos do mecanismo mashup.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Bytes virtuais do mecanismo M|Bytes|Média|Uso de bytes virtuais por processos do mecanismo mashup.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Total De Pedidos|Total de solicitações do gateway (preterido)|Contagem|Total|Número de solicitações de gateway-use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Localização, nome do host|
|SuccessfulRequests|Solicitações de gateway com êxito (preteridas)|Contagem|Total|Número de solicitações de gateway bem-sucedidas-use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Localização, nome do host|
|UnauthorizedRequests|Solicitações de gateway não autorizadas (preteridas)|Contagem|Total|Número de solicitações de gateway não autorizadas-use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Localização, nome do host|
|FailedRequests|Solicitações de gateway com falha (preteridas)|Contagem|Total|Número de falhas em solicitações de gateway-use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Localização, nome do host|
|OtherRequests|Outras solicitações de gateway (preteridas)|Contagem|Total|Número de outras solicitações de gateway – use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Localização, nome do host|
|Duração|Duração geral das solicitações de gateway|Milissegundos|Média|Duração geral de solicitações de gateway em milissegundos|Localização, nome do host|
|BackendDuration|Duração de solicitações de back-end|Milissegundos|Média|Duração de solicitações de back-end em milissegundos|Localização, nome do host|
|Capacidade|Capacidade|Percentagem|Média|Métrica de utilização para o serviço ApiManagement|Localização|
|EventHubTotalEvents|Total de eventos do EventHub|Contagem|Total|Número de eventos enviados ao EventHub|Localização|
|EventHubSuccessfulEvents|Eventos de EventHub bem-sucedidos|Contagem|Total|Número de eventos de EventHub bem-sucedidos|Localização|
|EventHubTotalFailedEvents|Eventos de EventHub com falha|Contagem|Total|Número de eventos de EventHub com falha|Localização|
|EventHubRejectedEvents|Eventos de EventHub rejeitados|Contagem|Total|Número de eventos do EventHub rejeitados (configuração incorreta ou não autorizada)|Localização|
|EventHubThrottledEvents|Eventos de EventHub limitados|Contagem|Total|Número de eventos regulados do EventHub|Localização|
|EventHubTimedoutEvents|Eventos do EventHub com tempo limite excedido|Contagem|Total|Número de eventos do EventHub com tempo limite excedido|Localização|
|EventHubDroppedEvents|Eventos do EventHub ignorados|Contagem|Total|Número de eventos ignorados devido ao limite de tamanho de fila atingido|Localização|
|EventHubTotalBytesSent|Tamanho dos eventos do EventHub|Bytes|Total|Tamanho total dos eventos do EventHub em bytes|Localização|
|Pedidos|Pedidos|Contagem|Total|Métricas de solicitação de gateway com várias dimensões|Local, nome do host, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Contagem|Contagem|Número total de solicitações HTTP de entrada.|Nenhuma|
|FailedHttpRequestCount|FailedHttpRequestCount|Contagem|Contagem|Solicitações HTTP com falha.|Nenhuma|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Contagem|Média|Latência em uma solicitação HTTP.|Nenhuma|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/Spring

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage|Porcentagem de uso da CPU do sistema|Percentagem|Média|O uso recente da CPU para todo o sistema|AppName, Pod|
|AppCpuUsagePercentage|Porcentagem de uso de CPU do aplicativo|Percentagem|Média|Porcentagem de uso da CPU da JVM do aplicativo|AppName, Pod|
|AppMemoryCommitted|Memória de aplicativo atribuída|Bytes|Média|Memória atribuída à JVM em bytes|AppName, Pod|
|AppMemoryUsed|Memória usada do aplicativo|Bytes|Média|Memória do aplicativo usada em bytes|AppName, Pod|
|AppMemoryMax|Memória máxima do aplicativo|Bytes|Máximo|A quantidade máxima de memória, em bytes, que pode ser usada para gerenciamento de memória|AppName, Pod|
|MaxOldGenMemoryPoolBytes|Tamanho máximo de dados de geração antiga disponíveis|Bytes|Média|Tamanho máximo do pool de memória de geração antiga|AppName, Pod|
|OldGenMemoryPoolBytes|Tamanho de dados de geração antiga|Bytes|Média|Tamanho do pool de memória de geração antiga após um GC completo|AppName, Pod|
|OldGenPromotedBytes|Promover para tamanho de dados de geração antiga|Bytes|Máximo|Contagem de aumentos positivos no tamanho do pool de memória de geração antiga antes do GC ser após o GC|AppName, Pod|
|YoungGenPromotedBytes|Promover para tamanho de dados de geração jovem|Bytes|Máximo|Incrementado para um aumento no tamanho do pool de memória de geração jovem após um GC antes do próximo|AppName, Pod|
|GCPauseTotalCount|Contagem de pausar do GC|Contagem|Total|Contagem de pausar do GC|AppName, Pod|
|GCPauseTotalTime|Tempo total de pausa do GC|Milissegundos|Total|Tempo total de pausa do GC|AppName, Pod|
|TomcatSentBytes|Total de bytes enviados do Tomcat|Bytes|Total|Total de bytes enviados do Tomcat|AppName, Pod|
|TomcatReceivedBytes|Total de bytes recebidos do Tomcat|Bytes|Total|Total de bytes recebidos do Tomcat|AppName, Pod|
|TomcatRequestTotalTime|Tempos totais da solicitação do Tomcat|Milissegundos|Total|Tempos totais da solicitação do Tomcat|AppName, Pod|
|TomcatRequestTotalCount|Contagem total de solicitações do Tomcat|Contagem|Total|Contagem total de solicitações do Tomcat|AppName, Pod|
|TomcatResponseAvgTime|Tempo médio da solicitação do Tomcat|Milissegundos|Média|Tempo médio da solicitação do Tomcat|AppName, Pod|
|TomcatRequestMaxTime|Tempo máximo de solicitação do Tomcat|Milissegundos|Máximo|Tempo máximo de solicitação do Tomcat|AppName, Pod|
|TomcatErrorCount|Erro global do Tomcat|Contagem|Total|Erro global do Tomcat|AppName, Pod|
|TomcatSessionActiveMaxCount|Contagem ativa máxima da sessão do Tomcat|Contagem|Total|Contagem ativa máxima da sessão do Tomcat|AppName, Pod|
|TomcatSessionAliveMaxTime|Tempo de atividade máx de sessão do Tomcat|Milissegundos|Máximo|Tempo de atividade máx de sessão do Tomcat|AppName, Pod|
|TomcatSessionActiveCurrentCount|Contagem de sessões ativas de Tomcat|Contagem|Total|Contagem de sessões ativas de Tomcat|AppName, Pod|
|TomcatSessionCreatedCount|Contagem de sessões criadas do Tomcat|Contagem|Total|Contagem de sessões criadas do Tomcat|AppName, Pod|
|TomcatSessionExpiredCount|Contagem de sessões expiradas do Tomcat|Contagem|Total|Contagem de sessões expiradas do Tomcat|AppName, Pod|
|TomcatSessionRejectedCount|Contagem rejeitada da sessão Tomcat|Contagem|Total|Contagem rejeitada da sessão Tomcat|AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalJob|Total de trabalhos|Contagem|Total|O número total de trabalhos|Runbook, status|
|TotalUpdateDeploymentRuns|Total de execuções de implantação de atualização|Contagem|Total|Total de execuções de implantação de atualização de software|SoftwareUpdateConfigurationName, status|
|TotalUpdateDeploymentMachineRuns|Total de execuções do computador de implantação de atualização|Contagem|Total|O computador de implantação de atualização de software total é executado em uma execução de implantação de atualização de software|SoftwareUpdateConfigurationName, status, Computadordedestino, SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CoreCount|Contagem de núcleos dedicados|Contagem|Total|Número total de núcleos dedicados na conta do lote|Nenhuma|
|TotalNodeCount|Contagem de nós dedicados|Contagem|Total|Número total de nós dedicados na conta do lote|Nenhuma|
|LowPriorityCoreCount|Contagem de núcleos de LowPriority|Contagem|Total|Número total de núcleos de baixa prioridade na conta do lote|Nenhuma|
|TotalLowPriorityNodeCount|Contagem de nós de baixa prioridade|Contagem|Total|Número total de nós de baixa prioridade na conta do lote|Nenhuma|
|CreatingNodeCount|Criando contagem de nós|Contagem|Total|Número de nós sendo criados|Nenhuma|
|StartingNodeCount|Contagem de nós inicial|Contagem|Total|Número de nós iniciando|Nenhuma|
|WaitingForStartTaskNodeCount|Aguardando a contagem de nó de tarefa inicial|Contagem|Total|Número de nós aguardando a conclusão da tarefa inicial|Nenhuma|
|StartTaskFailedNodeCount|Falha na contagem de nós da tarefa inicial|Contagem|Total|Número de nós em que a tarefa inicial falhou|Nenhuma|
|IdleNodeCount|Contagem de nós ociosos|Contagem|Total|Número de nós ociosos|Nenhuma|
|OfflineNodeCount|Contagem de nós offline|Contagem|Total|Número de nós offline|Nenhuma|
|RebootingNodeCount|Contagem de nós de reinicialização|Contagem|Total|Número de nós de reinicialização|Nenhuma|
|ReimagingNodeCount|Contagem de nós de reimagem|Contagem|Total|Número de nós de reimagem|Nenhuma|
|RunningNodeCount|Contagem de nós em execução|Contagem|Total|Número de nós em execução|Nenhuma|
|LeavingPoolNodeCount|Saindo da contagem de nós do pool|Contagem|Total|Número de nós saindo do pool|Nenhuma|
|UnusableNodeCount|Contagem de nós inutilizáveis|Contagem|Total|Número de nós inutilizáveis|Nenhuma|
|PreemptedNodeCount|Contagem de nós preempção|Contagem|Total|Número de nós admitidos|Nenhuma|
|TaskStartEvent|Eventos de início de tarefa|Contagem|Total|Número total de tarefas que foram iniciadas|Nenhuma|
|TaskCompleteEvent|Eventos de conclusão de tarefa|Contagem|Total|Número total de tarefas que foram concluídas|Nenhuma|
|TaskFailEvent|Eventos de falha de tarefa|Contagem|Total|Número total de tarefas que foram concluídas em um estado de falha|Nenhuma|
|PoolCreateEvent|Eventos de criação de pool|Contagem|Total|Número total de pools que foram criados|Nenhuma|
|PoolResizeStartEvent|Eventos de início de redimensionamento do pool|Contagem|Total|Número total de redimensionamentos de pool que foram iniciados|Nenhuma|
|PoolResizeCompleteEvent|Eventos completos de redimensionamento de pool|Contagem|Total|Número total de redimensionamentos de pool que foram concluídos|Nenhuma|
|PoolDeleteStartEvent|Eventos de início de exclusão de pool|Contagem|Total|Número total de exclusões de pool que iniciaram|Nenhuma|
|PoolDeleteCompleteEvent|Eventos completos de exclusão de pool|Contagem|Total|Número total de exclusões de pool que foram concluídas|Nenhuma|
|JobDeleteCompleteEvent|Eventos completos de exclusão de trabalho|Contagem|Total|Número total de trabalhos que foram excluídos com êxito.|Nenhuma|
|JobDeleteStartEvent|Eventos de início de exclusão de trabalho|Contagem|Total|Número total de trabalhos que foram solicitados a serem excluídos.|Nenhuma|
|JobDisableCompleteEvent|Trabalho de desabilitar eventos concluídos|Contagem|Total|Número total de trabalhos que foram desabilitados com êxito.|Nenhuma|
|JobDisableStartEvent|Eventos de início de desabilitação do trabalho|Contagem|Total|Número total de trabalhos que foram solicitados para serem desabilitados.|Nenhuma|
|JobStartEvent|Eventos de início do trabalho|Contagem|Total|Número total de trabalhos iniciados com êxito.|Nenhuma|
|JobTerminateCompleteEvent|Eventos de término de trabalho concluídos|Contagem|Total|Número total de trabalhos que foram encerrados com êxito.|Nenhuma|
|JobTerminateStartEvent|Eventos de início de término do trabalho|Contagem|Total|Número total de trabalhos que foram solicitados a serem encerrados.|Nenhuma|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft. BatchAI/Workspaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Trabalho enviado|Trabalho enviado|Contagem|Total|Número de trabalhos enviados|Cenário, ClusterName|
|Trabalho concluído|Trabalho concluído|Contagem|Total|Número de trabalhos concluídos|Cenário, ClusterName, Resultadotype|
|Total de nós|Total de nós|Contagem|Média|Número total de nós|Cenário, ClusterName|
|Nós ativos|Nós ativos|Contagem|Média|Número de nós em execução|Cenário, ClusterName|
|Nós ociosos|Nós ociosos|Contagem|Média|Número de nós ociosos|Cenário, ClusterName|
|Nós inutilizáveis|Nós inutilizáveis|Contagem|Média|Número de nós inutilizáveis|Cenário, ClusterName|
|Nós admitidos|Nós admitidos|Contagem|Média|Número de nós admitidos|Cenário, ClusterName|
|Saindo de nós|Saindo de nós|Contagem|Média|Número de nós de saída|Cenário, ClusterName|
|Total de núcleos|Total de núcleos|Contagem|Média|Número de núcleos totais|Cenário, ClusterName|
|Núcleos ativos|Núcleos ativos|Contagem|Média|Número de núcleos ativos|Cenário, ClusterName|
|Núcleos ociosos|Núcleos ociosos|Contagem|Média|Número de núcleos ociosos|Cenário, ClusterName|
|Núcleos inutilizáveis|Núcleos inutilizáveis|Contagem|Média|Número de núcleos inutilizáveis|Cenário, ClusterName|
|Núcleos preempçãos|Núcleos preempçãos|Contagem|Média|Número de núcleos admitidos|Cenário, ClusterName|
|Deixando núcleos|Deixando núcleos|Contagem|Média|Número de núcleos de saída|Cenário, ClusterName|
|Porcentagem de utilização de cota|Porcentagem de utilização de cota|Contagem|Média|Porcentagem de cota utilizada|Cenário, ClusterName, VmFamilyName, VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|Porcentagem de uso da CPU|Percentagem|Máximo|Porcentagem de uso da CPU|Nó|
|MemoryUsage|Utilização de Memória|Bytes|Média|Utilização de Memória|Nó|
|MemoryLimit|Limite de memória|Bytes|Média|Limite de memória|Nó|
|MemoryUsagePercentageInDouble|Porcentagem de uso da memória|Percentagem|Média|Porcentagem de uso da memória|Nó|
|StorageUsage|Uso do armazenamento|Bytes|Média|Uso do armazenamento|Nó|
|IOReadBytes|Bytes de leitura de e/s|Bytes|Total|Bytes de leitura de e/s|Nó|
|IOWriteBytes|Bytes de gravação de e/s|Bytes|Total|Bytes de gravação de e/s|Nó|
|ConnectionAccepted|Conexões aceitas|Contagem|Total|Conexões aceitas|Nó|
|ConnectionHandled|Conexões manipuladas|Contagem|Total|Conexões manipuladas|Nó|
|ConnectionActive|Conexões ativas|Contagem|Média|Conexões ativas|Nó|
|RequestHandled|Solicitações manipuladas|Contagem|Total|Solicitações manipuladas|Nó|
|ProcessedBlocks|Blocos processados|Contagem|Total|Blocos processados|Nó|
|ProcessedTransactions|Transações processadas|Contagem|Total|Transações processadas|Nó|
|PendingTransactions|Transações pendentes|Contagem|Média|Transações pendentes|Nó|
|QueuedTransactions|Transações em fila|Contagem|Média|Transações em fila|Nó|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|connectedclients|Clientes Conectados|Contagem|Máximo||ShardId|
|totalcommandsprocessed|Total de operações|Contagem|Total||ShardId|
|cachehits|Acertos do cache|Contagem|Total||ShardId|
|cachemisses|Erros de cache|Contagem|Total||ShardId|
|cachemissrate|Taxa de erros de cache|Percentagem|cachemissrate||ShardId|
|comandos|Visível|Contagem|Total||ShardId|
|comandos|Conjuntos|Contagem|Total||ShardId|
|operationsPerSecond|Operações por segundo|Contagem|Máximo||ShardId|
|evictedkeys|Chaves removidas|Contagem|Total||ShardId|
|totalkeys|Total de chaves|Contagem|Máximo||ShardId|
|expiredkeys|Chaves expiradas|Contagem|Total||ShardId|
|usedmemory|Memória usada|Bytes|Máximo||ShardId|
|usedmemorypercentage|Porcentagem de memória usada|Percentagem|Máximo||ShardId|
|usedmemoryRss|Memória RSS usada|Bytes|Máximo||ShardId|
|serverLoad|Carga do servidor|Percentagem|Máximo||ShardId|
|cacheWrite|Gravação de cache|BytesPerSecond|Máximo||ShardId|
|cacheRead|Leitura de cache|BytesPerSecond|Máximo||ShardId|
|percentProcessorTime|CPU|Percentagem|Máximo||ShardId|
|cacheLatency|Microssegundos de latência de cache (versão prévia)|Contagem|Média||ShardId|
|erros|Erros|Contagem|Máximo||Fragmentid, ErrorType|
|connectedclients0|Clientes conectados (fragmento 0)|Contagem|Máximo||Nenhuma|
|totalcommandsprocessed0|Total de operações (fragmento 0)|Contagem|Total||Nenhuma|
|cachehits0|Acertos do cache (fragmento 0)|Contagem|Total||Nenhuma|
|cachemisses0|Erros de cache (fragmento 0)|Contagem|Total||Nenhuma|
|getcommands0|Gets (fragmento 0)|Contagem|Total||Nenhuma|
|setcommands0|Sets (fragmento 0)|Contagem|Total||Nenhuma|
|operationsPerSecond0|Operações por segundo (fragmento 0)|Contagem|Máximo||Nenhuma|
|evictedkeys0|Chaves removidas (fragmento 0)|Contagem|Total||Nenhuma|
|totalkeys0|Total de chaves (fragmento 0)|Contagem|Máximo||Nenhuma|
|expiredkeys0|Chaves expiradas (fragmento 0)|Contagem|Total||Nenhuma|
|usedmemory0|Memória usada (fragmento 0)|Bytes|Máximo||Nenhuma|
|usedmemoryRss0|Memória RSS usada (fragmento 0)|Bytes|Máximo||Nenhuma|
|serverLoad0|Carga do servidor (fragmento 0)|Percentagem|Máximo||Nenhuma|
|cacheWrite0|Gravação no cache (fragmento 0)|BytesPerSecond|Máximo||Nenhuma|
|cacheRead0|Leitura de cache (fragmento 0)|BytesPerSecond|Máximo||Nenhuma|
|percentProcessorTime0|CPU (fragmento 0)|Percentagem|Máximo||Nenhuma|
|connectedclients1|Clientes conectados (fragmento 1)|Contagem|Máximo||Nenhuma|
|totalcommandsprocessed1|Total de operações (fragmento 1)|Contagem|Total||Nenhuma|
|cachehits1|Acertos do cache (fragmento 1)|Contagem|Total||Nenhuma|
|cachemisses1|Erros de cache (fragmento 1)|Contagem|Total||Nenhuma|
|getcommands1|Gets (fragmento 1)|Contagem|Total||Nenhuma|
|setcommands1|Sets (fragmento 1)|Contagem|Total||Nenhuma|
|operationsPerSecond1|Operações por segundo (fragmento 1)|Contagem|Máximo||Nenhuma|
|evictedkeys1|Chaves removidas (fragmento 1)|Contagem|Total||Nenhuma|
|totalkeys1|Total de chaves (fragmento 1)|Contagem|Máximo||Nenhuma|
|expiredkeys1|Chaves expiradas (fragmento 1)|Contagem|Total||Nenhuma|
|usedmemory1|Memória usada (fragmento 1)|Bytes|Máximo||Nenhuma|
|usedmemoryRss1|Memória RSS usada (fragmento 1)|Bytes|Máximo||Nenhuma|
|serverLoad1|Carga do servidor (fragmento 1)|Percentagem|Máximo||Nenhuma|
|cacheWrite1|Gravação no cache (fragmento 1)|BytesPerSecond|Máximo||Nenhuma|
|cacheRead1|Leitura do cache (fragmento 1)|BytesPerSecond|Máximo||Nenhuma|
|percentProcessorTime1|CPU (fragmento 1)|Percentagem|Máximo||Nenhuma|
|connectedclients2|Clientes conectados (fragmento 2)|Contagem|Máximo||Nenhuma|
|totalcommandsprocessed2|Total de operações (fragmento 2)|Contagem|Total||Nenhuma|
|cachehits2|Acertos do cache (fragmento 2)|Contagem|Total||Nenhuma|
|cachemisses2|Erros de cache (fragmento 2)|Contagem|Total||Nenhuma|
|getcommands2|Gets (fragmento 2)|Contagem|Total||Nenhuma|
|setcommands2|Sets (fragmento 2)|Contagem|Total||Nenhuma|
|operationsPerSecond2|Operações por segundo (fragmento 2)|Contagem|Máximo||Nenhuma|
|evictedkeys2|Chaves removidas (fragmento 2)|Contagem|Total||Nenhuma|
|totalkeys2|Total de chaves (fragmento 2)|Contagem|Máximo||Nenhuma|
|expiredkeys2|Chaves expiradas (fragmento 2)|Contagem|Total||Nenhuma|
|usedmemory2|Memória usada (fragmento 2)|Bytes|Máximo||Nenhuma|
|usedmemoryRss2|Memória RSS usada (fragmento 2)|Bytes|Máximo||Nenhuma|
|serverLoad2|Carga do servidor (fragmento 2)|Percentagem|Máximo||Nenhuma|
|cacheWrite2|Gravação no cache (fragmento 2)|BytesPerSecond|Máximo||Nenhuma|
|cacheRead2|Leitura de cache (fragmento 2)|BytesPerSecond|Máximo||Nenhuma|
|percentProcessorTime2|CPU (fragmento 2)|Percentagem|Máximo||Nenhuma|
|connectedclients3|Clientes conectados (fragmento 3)|Contagem|Máximo||Nenhuma|
|totalcommandsprocessed3|Total de operações (fragmento 3)|Contagem|Total||Nenhuma|
|cachehits3|Acertos do cache (fragmento 3)|Contagem|Total||Nenhuma|
|cachemisses3|Erros de cache (fragmento 3)|Contagem|Total||Nenhuma|
|getcommands3|Gets (fragmento 3)|Contagem|Total||Nenhuma|
|setcommands3|Sets (fragmento 3)|Contagem|Total||Nenhuma|
|operationsPerSecond3|Operações por segundo (fragmento 3)|Contagem|Máximo||Nenhuma|
|evictedkeys3|Chaves removidas (fragmento 3)|Contagem|Total||Nenhuma|
|totalkeys3|Total de chaves (fragmento 3)|Contagem|Máximo||Nenhuma|
|expiredkeys3|Chaves expiradas (fragmento 3)|Contagem|Total||Nenhuma|
|usedmemory3|Memória usada (fragmento 3)|Bytes|Máximo||Nenhuma|
|usedmemoryRss3|Memória RSS usada (fragmento 3)|Bytes|Máximo||Nenhuma|
|serverLoad3|Carga do servidor (fragmento 3)|Percentagem|Máximo||Nenhuma|
|cacheWrite3|Gravação no cache (fragmento 3)|BytesPerSecond|Máximo||Nenhuma|
|cacheRead3|Leitura do cache (fragmento 3)|BytesPerSecond|Máximo||Nenhuma|
|percentProcessorTime3|CPU (fragmento 3)|Percentagem|Máximo||Nenhuma|
|connectedclients4|Clientes conectados (fragmento 4)|Contagem|Máximo||Nenhuma|
|totalcommandsprocessed4|Total de operações (fragmento 4)|Contagem|Total||Nenhuma|
|cachehits4|Acertos do cache (fragmento 4)|Contagem|Total||Nenhuma|
|cachemisses4|Erros de cache (fragmento 4)|Contagem|Total||Nenhuma|
|getcommands4|Gets (fragmento 4)|Contagem|Total||Nenhuma|
|setcommands4|Sets (fragmento 4)|Contagem|Total||Nenhuma|
|operationsPerSecond4|Operações por segundo (fragmento 4)|Contagem|Máximo||Nenhuma|
|evictedkeys4|Chaves removidas (fragmento 4)|Contagem|Total||Nenhuma|
|totalkeys4|Total de chaves (fragmento 4)|Contagem|Máximo||Nenhuma|
|expiredkeys4|Chaves expiradas (fragmento 4)|Contagem|Total||Nenhuma|
|usedmemory4|Memória usada (fragmento 4)|Bytes|Máximo||Nenhuma|
|usedmemoryRss4|Memória RSS usada (fragmento 4)|Bytes|Máximo||Nenhuma|
|serverLoad4|Carga do servidor (fragmento 4)|Percentagem|Máximo||Nenhuma|
|cacheWrite4|Gravação no cache (fragmento 4)|BytesPerSecond|Máximo||Nenhuma|
|cacheRead4|Leitura do cache (fragmento 4)|BytesPerSecond|Máximo||Nenhuma|
|percentProcessorTime4|CPU (fragmento 4)|Percentagem|Máximo||Nenhuma|
|connectedclients5|Clientes conectados (fragmento 5)|Contagem|Máximo||Nenhuma|
|totalcommandsprocessed5|Total de operações (fragmento 5)|Contagem|Total||Nenhuma|
|cachehits5|Acertos do cache (fragmento 5)|Contagem|Total||Nenhuma|
|cachemisses5|Erros de cache (fragmento 5)|Contagem|Total||Nenhuma|
|getcommands5|Gets (fragmento 5)|Contagem|Total||Nenhuma|
|setcommands5|Sets (fragmento 5)|Contagem|Total||Nenhuma|
|operationsPerSecond5|Operações por segundo (fragmento 5)|Contagem|Máximo||Nenhuma|
|evictedkeys5|Chaves removidas (fragmento 5)|Contagem|Total||Nenhuma|
|totalkeys5|Total de chaves (fragmento 5)|Contagem|Máximo||Nenhuma|
|expiredkeys5|Chaves expiradas (fragmento 5)|Contagem|Total||Nenhuma|
|usedmemory5|Memória usada (fragmento 5)|Bytes|Máximo||Nenhuma|
|usedmemoryRss5|Memória RSS usada (fragmento 5)|Bytes|Máximo||Nenhuma|
|serverLoad5|Carga do servidor (fragmento 5)|Percentagem|Máximo||Nenhuma|
|cacheWrite5|Gravação no cache (fragmento 5)|BytesPerSecond|Máximo||Nenhuma|
|cacheRead5|Leitura do cache (fragmento 5)|BytesPerSecond|Máximo||Nenhuma|
|percentProcessorTime5|CPU (fragmento 5)|Percentagem|Máximo||Nenhuma|
|connectedclients6|Clientes conectados (fragmento 6)|Contagem|Máximo||Nenhuma|
|totalcommandsprocessed6|Total de operações (fragmento 6)|Contagem|Total||Nenhuma|
|cachehits6|Acertos do cache (fragmento 6)|Contagem|Total||Nenhuma|
|cachemisses6|Erros de cache (fragmento 6)|Contagem|Total||Nenhuma|
|getcommands6|Gets (fragmento 6)|Contagem|Total||Nenhuma|
|setcommands6|Sets (fragmento 6)|Contagem|Total||Nenhuma|
|operationsPerSecond6|Operações por segundo (fragmento 6)|Contagem|Máximo||Nenhuma|
|evictedkeys6|Chaves removidas (fragmento 6)|Contagem|Total||Nenhuma|
|totalkeys6|Total de chaves (fragmento 6)|Contagem|Máximo||Nenhuma|
|expiredkeys6|Chaves expiradas (fragmento 6)|Contagem|Total||Nenhuma|
|usedmemory6|Memória usada (fragmento 6)|Bytes|Máximo||Nenhuma|
|usedmemoryRss6|Memória RSS usada (fragmento 6)|Bytes|Máximo||Nenhuma|
|serverLoad6|Carga do servidor (fragmento 6)|Percentagem|Máximo||Nenhuma|
|cacheWrite6|Gravação no cache (fragmento 6)|BytesPerSecond|Máximo||Nenhuma|
|cacheRead6|Leitura do cache (fragmento 6)|BytesPerSecond|Máximo||Nenhuma|
|percentProcessorTime6|CPU (fragmento 6)|Percentagem|Máximo||Nenhuma|
|connectedclients7|Clientes conectados (fragmento 7)|Contagem|Máximo||Nenhuma|
|totalcommandsprocessed7|Total de operações (fragmento 7)|Contagem|Total||Nenhuma|
|cachehits7|Acertos do cache (fragmento 7)|Contagem|Total||Nenhuma|
|cachemisses7|Erros de cache (fragmento 7)|Contagem|Total||Nenhuma|
|getcommands7|Gets (fragmento 7)|Contagem|Total||Nenhuma|
|setcommands7|Sets (fragmento 7)|Contagem|Total||Nenhuma|
|operationsPerSecond7|Operações por segundo (fragmento 7)|Contagem|Máximo||Nenhuma|
|evictedkeys7|Chaves removidas (fragmento 7)|Contagem|Total||Nenhuma|
|totalkeys7|Total de chaves (fragmento 7)|Contagem|Máximo||Nenhuma|
|expiredkeys7|Chaves expiradas (fragmento 7)|Contagem|Total||Nenhuma|
|usedmemory7|Memória usada (fragmento 7)|Bytes|Máximo||Nenhuma|
|usedmemoryRss7|Memória RSS usada (fragmento 7)|Bytes|Máximo||Nenhuma|
|serverLoad7|Carga do servidor (fragmento 7)|Percentagem|Máximo||Nenhuma|
|cacheWrite7|Gravação no cache (fragmento 7)|BytesPerSecond|Máximo||Nenhuma|
|cacheRead7|Leitura do cache (fragmento 7)|BytesPerSecond|Máximo||Nenhuma|
|percentProcessorTime7|CPU (fragmento 7)|Percentagem|Máximo||Nenhuma|
|connectedclients8|Clientes conectados (fragmento 8)|Contagem|Máximo||Nenhuma|
|totalcommandsprocessed8|Total de operações (fragmento 8)|Contagem|Total||Nenhuma|
|cachehits8|Acertos do cache (fragmento 8)|Contagem|Total||Nenhuma|
|cachemisses8|Erros de cache (fragmento 8)|Contagem|Total||Nenhuma|
|getcommands8|Gets (fragmento 8)|Contagem|Total||Nenhuma|
|setcommands8|Sets (fragmento 8)|Contagem|Total||Nenhuma|
|operationsPerSecond8|Operações por segundo (fragmento 8)|Contagem|Máximo||Nenhuma|
|evictedkeys8|Chaves removidas (fragmento 8)|Contagem|Total||Nenhuma|
|totalkeys8|Total de chaves (fragmento 8)|Contagem|Máximo||Nenhuma|
|expiredkeys8|Chaves expiradas (fragmento 8)|Contagem|Total||Nenhuma|
|usedmemory8|Memória usada (fragmento 8)|Bytes|Máximo||Nenhuma|
|usedmemoryRss8|Memória RSS usada (fragmento 8)|Bytes|Máximo||Nenhuma|
|serverLoad8|Carga do servidor (fragmento 8)|Percentagem|Máximo||Nenhuma|
|cacheWrite8|Gravação no cache (fragmento 8)|BytesPerSecond|Máximo||Nenhuma|
|cacheRead8|Leitura de cache (fragmento 8)|BytesPerSecond|Máximo||Nenhuma|
|percentProcessorTime8|CPU (fragmento 8)|Percentagem|Máximo||Nenhuma|
|connectedclients9|Clientes conectados (fragmento 9)|Contagem|Máximo||Nenhuma|
|totalcommandsprocessed9|Total de operações (fragmento 9)|Contagem|Total||Nenhuma|
|cachehits9|Acertos do cache (fragmento 9)|Contagem|Total||Nenhuma|
|cachemisses9|Erros de cache (fragmento 9)|Contagem|Total||Nenhuma|
|getcommands9|Gets (fragmento 9)|Contagem|Total||Nenhuma|
|setcommands9|Sets (fragmento 9)|Contagem|Total||Nenhuma|
|operationsPerSecond9|Operações por segundo (fragmento 9)|Contagem|Máximo||Nenhuma|
|evictedkeys9|Chaves removidas (fragmento 9)|Contagem|Total||Nenhuma|
|totalkeys9|Total de chaves (fragmento 9)|Contagem|Máximo||Nenhuma|
|expiredkeys9|Chaves expiradas (fragmento 9)|Contagem|Total||Nenhuma|
|usedmemory9|Memória usada (fragmento 9)|Bytes|Máximo||Nenhuma|
|usedmemoryRss9|Memória RSS usada (fragmento 9)|Bytes|Máximo||Nenhuma|
|serverLoad9|Carga do servidor (fragmento 9)|Percentagem|Máximo||Nenhuma|
|cacheWrite9|Gravação no cache (fragmento 9)|BytesPerSecond|Máximo||Nenhuma|
|cacheRead9|Leitura do cache (fragmento 9)|BytesPerSecond|Máximo||Nenhuma|
|percentProcessorTime9|CPU (fragmento 9)|Percentagem|Máximo||Nenhuma|



## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Contagem de solicitações de firewall do aplicativo Web|Contagem|Total|O número de solicitações de cliente processadas pelo firewall do aplicativo Web|PolicyName, RuleName, ação|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A porcentagem de unidades de computação alocadas que estão atualmente em uso pelas máquinas virtuais.|Nenhuma|
|Entrada na Rede|Entrada na Rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada).|Nenhuma|
|Saída da Rede|Saída da Rede|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída).|Nenhuma|
|Bytes de leitura de disco/s|Leitura de disco|BytesPerSecond|Média|Média de bytes lidos do disco durante o período de monitoramento.|Nenhuma|
|Bytes de gravação no disco/s|Gravação de disco|BytesPerSecond|Média|Média de bytes gravados no disco durante o período de monitoramento.|Nenhuma|
|Operações de leitura de disco/s|Operações de leitura de disco/s|CountPerSecond|Média|IOPS de leitura de disco.|Nenhuma|
|Operações de gravação de disco/s|Operações de gravação de disco/s|CountPerSecond|Média|IOPS de gravação de disco.|Nenhuma|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A porcentagem de unidades de computação alocadas que estão atualmente em uso pelas máquinas virtuais.|RoleInstanceId|
|Entrada na Rede|Entrada na Rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada).|RoleInstanceId|
|Saída da Rede|Saída da Rede|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída).|RoleInstanceId|
|Bytes de leitura de disco/s|Leitura de disco|BytesPerSecond|Média|Média de bytes lidos do disco durante o período de monitoramento.|RoleInstanceId|
|Bytes de gravação no disco/s|Gravação de disco|BytesPerSecond|Média|Média de bytes gravados no disco durante o período de monitoramento.|RoleInstanceId|
|Operações de leitura de disco/s|Operações de leitura de disco/s|CountPerSecond|Média|IOPS de leitura de disco.|RoleInstanceId|
|Operações de gravação de disco/s|Operações de gravação de disco/s|CountPerSecond|Média|IOPS de gravação de disco.|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|UsedCapacity|Capacidade utilizada|Bytes|Média|Capacidade da conta utilizada|Nenhuma|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. ClassicStorage/storageAccounts/blobservices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BlobCapacity|Capacidade do Blob|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Blob a conta de armazenamento, em bytes.|BlobType, camada|
|BlobCount|Contagem de Blobs|Contagem|Média|O número de blobs no serviço Blob da conta de armazenamento.|BlobType, camada|
|ContainerCount|Contagem do Contentor de Blobs|Contagem|Média|O número de contentores no serviço Blob da conta de armazenamento.|Nenhuma|
|IndexCapacity|Capacidade indexada|Bytes|Média|A quantidade de armazenamento usada pelo índice ADLS Gen2 (hierárquico) em bytes.|Nenhuma|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. ClassicStorage/storageAccounts/tabelaservices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TableCapacity|Capacidade de Tabelas|Bytes|Média|A quantidade de armazenamento utilizado pelo serviço Tabela da conta de armazenamento, em bytes.|Nenhuma|
|TableCount|Contagem de Tabelas|Contagem|Média|O número de tabelas no serviço Tabela da conta de armazenamento.|Nenhuma|
|TableEntityCount|Contagem de Entidade de Tabelas|Contagem|Média|O número de entidades de tabelas no serviço Tabela da conta de armazenamento.|Nenhuma|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. ClassicStorage/storageAccounts/fileservices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Capacidade de|Capacidade do arquivo|Bytes|Média|A quantidade de armazenamento usada pelo serviço de arquivo da conta de armazenamento em bytes.|FileShare|
|FileCount|Contagem de arquivos|Contagem|Média|O número de arquivos no serviço de arquivo da conta de armazenamento.|FileShare|
|FileShareCount|Contagem de compartilhamento de arquivos|Contagem|Média|O número de compartilhamentos de arquivos no serviço de arquivo da conta de armazenamento.|Nenhuma|
|FileShareSnapshotCount|Contagem de instantâneos de compartilhamento de arquivos|Contagem|Média|O número de instantâneos presentes no serviço compartilhamento de arquivos da conta de armazenamento.|FileShare|
|FileShareSnapshotSize|Tamanho do instantâneo de compartilhamento de arquivos|Bytes|Média|A quantidade de armazenamento usada pelos instantâneos no serviço de arquivo da conta de armazenamento, em bytes.|FileShare|
|FileShareQuota|Tamanho da cota de compartilhamento de arquivos|Bytes|Média|O limite superior da quantidade de armazenamento que pode ser usada pelo serviço de arquivos do Azure em bytes.|FileShare|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, GeoType, ApiName, autenticação, FileShare|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação, FileShare|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação, FileShare|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|Geotipo, ApiName, autenticação, FileShare|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação, FileShare|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|Geotipo, ApiName, autenticação, FileShare|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. ClassicStorage/storageAccounts/queueservices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueueCapacity|Capacidade de Fila|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Fila a conta de armazenamento, em bytes.|Nenhuma|
|QueueCount|Contagem de Filas|Contagem|Média|O número de filas no serviço Fila da conta de armazenamento.|Nenhuma|
|QueueMessageCount|Contagem de Mensagens em Fila|Contagem|Média|O número aproximado de mensagens em fila no serviço Fila da conta de armazenamento.|Nenhuma|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|Geotipo, ApiName, autenticação|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalCalls|Total de chamadas|Contagem|Total|Número total de chamadas.|ApiName, OperationName, região|
|SuccessfulCalls|Chamadas com êxito|Contagem|Total|Número de chamadas bem-sucedidas.|ApiName, OperationName, região|
|TotalErrors|Erros Totais|Contagem|Total|Número total de chamadas com resposta de erro (código de resposta HTTP 4xx ou 5xx).|ApiName, OperationName, região|
|BlockedCalls|Chamadas bloqueadas|Contagem|Total|Número de chamadas que excederam a taxa ou o limite de cota.|ApiName, OperationName, região|
|ServerErrors|Erros do servidor|Contagem|Total|Número de chamadas com erro interno do serviço (código de resposta HTTP 5xx).|ApiName, OperationName, região|
|ClientErrors|Erros do cliente|Contagem|Total|Número de chamadas com erro do lado do cliente (código de resposta HTTP 4xx).|ApiName, OperationName, região|
|Dados de|Dados em|Bytes|Total|Tamanho dos dados de entrada em bytes.|ApiName, OperationName, região|
|DataOut|Dados out|Bytes|Total|Tamanho dos dados de saída em bytes.|ApiName, OperationName, região|
|Latência|Latência|Milissegundos|Média|Latência em milissegundos.|ApiName, OperationName, região|
|CharactersTranslated|Caracteres traduzidos|Contagem|Total|Número total de caracteres na solicitação de texto de entrada.|ApiName, OperationName, região|
|CharactersTrained|Caracteres treinados|Contagem|Total|Número total de caracteres treinados.|ApiName, OperationName, região|
|SpeechSessionDuration|Duração da sessão de fala|Segundos|Total|Duração total da sessão de fala em segundos.|ApiName, OperationName, região|
|TotalTransactions|Total de transações|Contagem|Total|Número total de transações.|Nenhuma|
|TotalTokenCalls|Total de chamadas de token|Contagem|Total|Número total de chamadas de token.|ApiName, OperationName, região|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades do computador alocadas que estão atualmente em utilização pela Máquina Virtual ou Máquinas Virtuais|Nenhuma|
|Entrada na Rede|Rede em faturável (preterida)|Bytes|Total|O número de bytes faturáveis recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada) (preterido)|Nenhuma|
|Saída da Rede|Saída de rede faturável (preterida)|Bytes|Total|O número de bytes faturáveis de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída) (preterido)|Nenhuma|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|Nenhuma|
|Bytes de gravação no disco|Bytes de gravação no disco|Bytes|Total|Bytes gravados no disco durante o período de monitoramento|Nenhuma|
|Operações de leitura de disco/s|Operações de leitura de disco/s|CountPerSecond|Média|IOPS de leitura de disco|Nenhuma|
|Operações de gravação de disco/s|Operações de gravação de disco/s|CountPerSecond|Média|IOPS de gravação de disco|Nenhuma|
|Créditos de CPU restantes|Créditos de CPU restantes|Contagem|Média|Número total de créditos disponíveis para intermitência|Nenhuma|
|Créditos de CPU consumidos|Créditos de CPU consumidos|Contagem|Média|Número total de créditos consumidos pela máquina virtual|Nenhuma|
|Bytes de leitura por disco/s|Bytes de leitura do disco de dados/s (preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|SlotId|
|Bytes de gravação por disco/s|Bytes de gravação do disco de dados/s (preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|SlotId|
|Operações de leitura por disco/s|Operações de leitura de disco de dados/s (preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|SlotId|
|Operações de gravação por disco/s|Operações de gravação do disco de dados/s (preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|SlotId|
|QD por disco|Disco de dados QD (preterido)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|SlotId|
|Bytes de leitura por disco do so/s|Bytes de leitura do disco do so/s (preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma|
|Bytes de gravação por disco do so/s|Bytes de gravação de disco do sistema operacional/s (preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Nenhuma|
|Operações de leitura de so por disco/s|Operações de leitura de disco do so/s (preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma|
|Operações de gravação de so por disco/s|Operações de gravação de disco do sistema operacional/s (preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma|
|Sistema operacional por disco QD|Disco do so QD (preterido)|Contagem|Média|Profundidade da fila de disco do so (ou comprimento da fila)|Nenhuma|
|Bytes de leitura do disco de dados/s|Bytes de leitura do disco de dados/s (visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|LUN|
|Bytes de gravação do disco de dados/s|Bytes de gravação do disco de dados/s (visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|LUN|
|Operações de leitura de disco de dados/s|Operações de leitura de disco de dados/s (visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|LUN|
|Operações de gravação do disco de dados/s|Operações de gravação do disco de dados/s (visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|LUN|
|Profundidade da fila do disco de dados|Profundidade da fila do disco de dados (visualização)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|LUN|
|Bytes de leitura do disco do so/s|Bytes de leitura do disco do so/s (visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma|
|Bytes de gravação de disco do sistema operacional/s|Bytes de gravação de disco do sistema operacional/s (visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Nenhuma|
|Operações de leitura de disco do so/s|Operações de leitura de disco do so/s (visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma|
|Operações de gravação de disco do so/s|Operações de gravação de disco do so/s (visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma|
|Profundidade da fila do disco osso|Profundidade da fila de disco do so (visualização)|Contagem|Média|Profundidade da fila de disco do so (ou comprimento da fila)|Nenhuma|
|Fluxos de entrada|Fluxos de entrada|Contagem|Média|Os fluxos de entrada são o número de fluxos atuais na direção de entrada (tráfego entrando na VM)|Nenhuma|
|Fluxos de saída|Fluxos de saída|Contagem|Média|Fluxos de saída são o número de fluxos atuais na direção de saída (tráfego saindo da VM)|Nenhuma|
|Taxa máxima de criação de fluxos de entrada|Taxa máxima de criação de fluxos de entrada (visualização)|CountPerSecond|Média|A taxa de criação máxima de fluxos de entrada (tráfego entrando na VM)|Nenhuma|
|Taxa máxima de criação de fluxos de saída|Taxa máxima de criação de fluxos de saída (versão prévia)|CountPerSecond|Média|A taxa de criação máxima de fluxos de saída (tráfego saindo da VM)|Nenhuma|
|Cache do disco de dados Premium-acesso de leitura|Leitura do cache do disco de dados Premium (versão prévia)|Percentagem|Média|Cache do disco de dados Premium-acesso de leitura|LUN|
|Erro de leitura do cache do disco de dados Premium|Cache do disco de dados Premium-erro de leitura (visualização)|Percentagem|Média|Erro de leitura do cache do disco de dados Premium|LUN|
|Cache de disco do sistema operacional Premium-acesso de leitura|Cache de disco do sistema operacional Premium-acesso de leitura (versão prévia)|Percentagem|Média|Cache de disco do sistema operacional Premium-acesso de leitura|Nenhuma|
|Erro de leitura do cache de disco do so premium|Erro de leitura do cache de disco do so premium (visualização)|Percentagem|Média|Erro de leitura do cache de disco do so premium|Nenhuma|
|Total de rede|Total de rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada)|Nenhuma|
|Total de saída de rede|Total de saída de rede|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída)|Nenhuma|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades do computador alocadas que estão atualmente em utilização pela Máquina Virtual ou Máquinas Virtuais|VMName|
|Entrada na Rede|Rede em faturável (preterida)|Bytes|Total|O número de bytes faturáveis recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada) (preterido)|VMName|
|Saída da Rede|Saída de rede faturável (preterida)|Bytes|Total|O número de bytes faturáveis de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída) (preterido)|VMName|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|VMName|
|Bytes de gravação no disco|Bytes de gravação no disco|Bytes|Total|Bytes gravados no disco durante o período de monitoramento|VMName|
|Operações de leitura de disco/s|Operações de leitura de disco/s|CountPerSecond|Média|IOPS de leitura de disco|VMName|
|Operações de gravação de disco/s|Operações de gravação de disco/s|CountPerSecond|Média|IOPS de gravação de disco|VMName|
|Créditos de CPU restantes|Créditos de CPU restantes|Contagem|Média|Número total de créditos disponíveis para intermitência|Nenhuma|
|Créditos de CPU consumidos|Créditos de CPU consumidos|Contagem|Média|Número total de créditos consumidos pela máquina virtual|Nenhuma|
|Bytes de leitura por disco/s|Bytes de leitura do disco de dados/s (preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|SlotId|
|Bytes de gravação por disco/s|Bytes de gravação do disco de dados/s (preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|SlotId|
|Operações de leitura por disco/s|Operações de leitura de disco de dados/s (preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|SlotId|
|Operações de gravação por disco/s|Operações de gravação do disco de dados/s (preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|SlotId|
|QD por disco|Disco de dados QD (preterido)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|SlotId|
|Bytes de leitura por disco do so/s|Bytes de leitura do disco do so/s (preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma|
|Bytes de gravação por disco do so/s|Bytes de gravação de disco do sistema operacional/s (preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Nenhuma|
|Operações de leitura de so por disco/s|Operações de leitura de disco do so/s (preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma|
|Operações de gravação de so por disco/s|Operações de gravação de disco do sistema operacional/s (preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma|
|Sistema operacional por disco QD|Disco do so QD (preterido)|Contagem|Média|Profundidade da fila de disco do so (ou comprimento da fila)|Nenhuma|
|Bytes de leitura do disco de dados/s|Bytes de leitura do disco de dados/s (visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|LUN, VMName|
|Bytes de gravação do disco de dados/s|Bytes de gravação do disco de dados/s (visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|LUN, VMName|
|Operações de leitura de disco de dados/s|Operações de leitura de disco de dados/s (visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|LUN, VMName|
|Operações de gravação do disco de dados/s|Operações de gravação do disco de dados/s (visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|LUN, VMName|
|Profundidade da fila do disco de dados|Profundidade da fila do disco de dados (visualização)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|LUN, VMName|
|Bytes de leitura do disco do so/s|Bytes de leitura do disco do so/s (visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|VMName|
|Bytes de gravação de disco do sistema operacional/s|Bytes de gravação de disco do sistema operacional/s (visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|VMName|
|Operações de leitura de disco do so/s|Operações de leitura de disco do so/s (visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|VMName|
|Operações de gravação de disco do so/s|Operações de gravação de disco do so/s (visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|VMName|
|Profundidade da fila do disco osso|Profundidade da fila de disco do so (visualização)|Contagem|Média|Profundidade da fila de disco do so (ou comprimento da fila)|VMName|
|Fluxos de entrada|Fluxos de entrada|Contagem|Média|Os fluxos de entrada são o número de fluxos atuais na direção de entrada (tráfego entrando na VM)|VMName|
|Fluxos de saída|Fluxos de saída|Contagem|Média|Fluxos de saída são o número de fluxos atuais na direção de saída (tráfego saindo da VM)|VMName|
|Taxa máxima de criação de fluxos de entrada|Taxa máxima de criação de fluxos de entrada (visualização)|CountPerSecond|Média|A taxa de criação máxima de fluxos de entrada (tráfego entrando na VM)|VMName|
|Taxa máxima de criação de fluxos de saída|Taxa máxima de criação de fluxos de saída (versão prévia)|CountPerSecond|Média|A taxa de criação máxima de fluxos de saída (tráfego saindo da VM)|VMName|
|Cache do disco de dados Premium-acesso de leitura|Leitura do cache do disco de dados Premium (versão prévia)|Percentagem|Média|Cache do disco de dados Premium-acesso de leitura|LUN, VMName|
|Erro de leitura do cache do disco de dados Premium|Cache do disco de dados Premium-erro de leitura (visualização)|Percentagem|Média|Erro de leitura do cache do disco de dados Premium|LUN, VMName|
|Cache de disco do sistema operacional Premium-acesso de leitura|Cache de disco do sistema operacional Premium-acesso de leitura (versão prévia)|Percentagem|Média|Cache de disco do sistema operacional Premium-acesso de leitura|VMName|
|Erro de leitura do cache de disco do so premium|Erro de leitura do cache de disco do so premium (visualização)|Percentagem|Média|Erro de leitura do cache de disco do so premium|VMName|
|Total de rede|Total de rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada)|VMName|
|Total de saída de rede|Total de saída de rede|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades do computador alocadas que estão atualmente em utilização pela Máquina Virtual ou Máquinas Virtuais|Nenhuma|
|Entrada na Rede|Rede em faturável (preterida)|Bytes|Total|O número de bytes faturáveis recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada) (preterido)|Nenhuma|
|Saída da Rede|Saída de rede faturável (preterida)|Bytes|Total|O número de bytes faturáveis de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída) (preterido)|Nenhuma|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|Nenhuma|
|Bytes de gravação no disco|Bytes de gravação no disco|Bytes|Total|Bytes gravados no disco durante o período de monitoramento|Nenhuma|
|Operações de leitura de disco/s|Operações de leitura de disco/s|CountPerSecond|Média|IOPS de leitura de disco|Nenhuma|
|Operações de gravação de disco/s|Operações de gravação de disco/s|CountPerSecond|Média|IOPS de gravação de disco|Nenhuma|
|Créditos de CPU restantes|Créditos de CPU restantes|Contagem|Média|Número total de créditos disponíveis para intermitência|Nenhuma|
|Créditos de CPU consumidos|Créditos de CPU consumidos|Contagem|Média|Número total de créditos consumidos pela máquina virtual|Nenhuma|
|Bytes de leitura por disco/s|Bytes de leitura do disco de dados/s (preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|SlotId|
|Bytes de gravação por disco/s|Bytes de gravação do disco de dados/s (preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|SlotId|
|Operações de leitura por disco/s|Operações de leitura de disco de dados/s (preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|SlotId|
|Operações de gravação por disco/s|Operações de gravação do disco de dados/s (preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|SlotId|
|QD por disco|Disco de dados QD (preterido)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|SlotId|
|Bytes de leitura por disco do so/s|Bytes de leitura do disco do so/s (preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma|
|Bytes de gravação por disco do so/s|Bytes de gravação de disco do sistema operacional/s (preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Nenhuma|
|Operações de leitura de so por disco/s|Operações de leitura de disco do so/s (preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma|
|Operações de gravação de so por disco/s|Operações de gravação de disco do sistema operacional/s (preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma|
|Sistema operacional por disco QD|Disco do so QD (preterido)|Contagem|Média|Profundidade da fila de disco do so (ou comprimento da fila)|Nenhuma|
|Bytes de leitura do disco de dados/s|Bytes de leitura do disco de dados/s (visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|LUN|
|Bytes de gravação do disco de dados/s|Bytes de gravação do disco de dados/s (visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|LUN|
|Operações de leitura de disco de dados/s|Operações de leitura de disco de dados/s (visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|LUN|
|Operações de gravação do disco de dados/s|Operações de gravação do disco de dados/s (visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|LUN|
|Profundidade da fila do disco de dados|Profundidade da fila do disco de dados (visualização)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|LUN|
|Bytes de leitura do disco do so/s|Bytes de leitura do disco do so/s (visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma|
|Bytes de gravação de disco do sistema operacional/s|Bytes de gravação de disco do sistema operacional/s (visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Nenhuma|
|Operações de leitura de disco do so/s|Operações de leitura de disco do so/s (visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma|
|Operações de gravação de disco do so/s|Operações de gravação de disco do so/s (visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma|
|Profundidade da fila do disco osso|Profundidade da fila de disco do so (visualização)|Contagem|Média|Profundidade da fila de disco do so (ou comprimento da fila)|Nenhuma|
|Fluxos de entrada|Fluxos de entrada|Contagem|Média|Os fluxos de entrada são o número de fluxos atuais na direção de entrada (tráfego entrando na VM)|Nenhuma|
|Fluxos de saída|Fluxos de saída|Contagem|Média|Fluxos de saída são o número de fluxos atuais na direção de saída (tráfego saindo da VM)|Nenhuma|
|Taxa máxima de criação de fluxos de entrada|Taxa máxima de criação de fluxos de entrada (visualização)|CountPerSecond|Média|A taxa de criação máxima de fluxos de entrada (tráfego entrando na VM)|Nenhuma|
|Taxa máxima de criação de fluxos de saída|Taxa máxima de criação de fluxos de saída (versão prévia)|CountPerSecond|Média|A taxa de criação máxima de fluxos de saída (tráfego saindo da VM)|Nenhuma|
|Cache do disco de dados Premium-acesso de leitura|Leitura do cache do disco de dados Premium (versão prévia)|Percentagem|Média|Cache do disco de dados Premium-acesso de leitura|LUN|
|Erro de leitura do cache do disco de dados Premium|Cache do disco de dados Premium-erro de leitura (visualização)|Percentagem|Média|Erro de leitura do cache do disco de dados Premium|LUN|
|Cache de disco do sistema operacional Premium-acesso de leitura|Cache de disco do sistema operacional Premium-acesso de leitura (versão prévia)|Percentagem|Média|Cache de disco do sistema operacional Premium-acesso de leitura|Nenhuma|
|Erro de leitura do cache de disco do so premium|Erro de leitura do cache de disco do so premium (visualização)|Percentagem|Média|Erro de leitura do cache de disco do so premium|Nenhuma|
|Total de rede|Total de rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada)|Nenhuma|
|Total de saída de rede|Total de saída de rede|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída)|Nenhuma|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Os|Utilização do CPU|Contagem|Média|Uso da CPU em todos os núcleos em milicores.|containerName|
|MemoryUsage|Utilização de Memória|Bytes|Média|Uso total de memória em byte.|containerName|
|NetworkBytesReceivedPerSecond|Bytes de rede recebidos por segundo|Bytes|Média|Os bytes de rede recebidos por segundo.|Nenhuma|
|NetworkBytesTransmittedPerSecond|Bytes de rede transmitidos por segundo|Bytes|Média|Os bytes de rede transmitidos por segundo.|Nenhuma|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalPullCount|Contagem total de pull|Contagem|Média|Número de pulls de imagem no total|Nenhuma|
|SuccessfulPullCount|Contagem de pull bem-sucedida|Contagem|Média|Número de pulls de imagem bem-sucedidos|Nenhuma|
|TotalPushCount|Contagem total de push|Contagem|Média|Número de envios por push de imagem no total|Nenhuma|
|SuccessfulPushCount|Contagem de push bem-sucedida|Contagem|Média|Número de Pushes de imagem bem-sucedidos|Nenhuma|
|RunDuration|Duração da execução|Milissegundos|Total|Duração da execução em milissegundos|Nenhuma|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Número total de núcleos de CPU disponíveis em um cluster gerenciado|Contagem|Média|Número total de núcleos de CPU disponíveis em um cluster gerenciado|Nenhuma|
|kube_node_status_allocatable_memory_bytes|Quantidade total de memória disponível em um cluster gerenciado|Bytes|Média|Quantidade total de memória disponível em um cluster gerenciado|Nenhuma|
|kube_pod_status_ready|Número de pods no estado pronto|Contagem|Média|Número de pods no estado pronto|namespace, Pod|
|kube_node_status_condition|Status para várias condições de nó|Contagem|Média|Status para várias condições de nó|condição, status, status2, nó|
|kube_pod_status_phase|Número de pods por fase|Contagem|Média|Número de pods por fase|fase, namespace, Pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfullRequests|Solicitações bem-sucedidas|Contagem|Total|Solicitações bem-sucedidas feitas pelo provedor personalizado|HttpMethod, CallPath, StatusCode|
|FailedRequests|Pedidos com Falhas|Contagem|Total|Obtém os logs disponíveis para provedores de recursos personalizados|HttpMethod, CallPath, StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|NICReadThroughput|Taxa de transferência de leitura (rede)|BytesPerSecond|Média|A taxa de transferência de leitura da interface de rede no dispositivo no período de relatório para todos os volumes no gateway.|InstanceName|
|NICWriteThroughput|Taxa de transferência de gravação (rede)|BytesPerSecond|Média|A taxa de transferência de gravação da interface de rede no dispositivo no período de relatório para todos os volumes no gateway.|InstanceName|
|CloudReadThroughputPerShare|Taxa de transferência de download de nuvem (compartilhamento)|BytesPerSecond|Média|A taxa de transferência de download para o Azure de um compartilhamento durante o período de relatório.|Partilhar|
|CloudUploadThroughputPerShare|Taxa de transferência de upload de nuvem (compartilhamento)|BytesPerSecond|Média|A taxa de transferência de upload para o Azure de um compartilhamento durante o período de relatório.|Partilhar|
|BytesUploadedToCloudPerShare|Bytes de nuvem carregados (compartilhamento)|Bytes|Média|O número total de bytes que são carregados no Azure de um compartilhamento durante o período de relatório.|Partilhar|
|TotalCapacity|Capacidade Total|Bytes|Média|Capacidade Total|Nenhuma|
|AvailableCapacity|Capacidade disponível|Bytes|Média|A capacidade disponível em bytes durante o período de relatório.|Nenhuma|
|CloudUploadThroughput|Taxa de transferência de upload de nuvem|BytesPerSecond|Média|A taxa de transferência de upload de nuvem para o Azure durante o período de relatório.|Nenhuma|
|CloudReadThroughput|Taxa de transferência de download da nuvem|BytesPerSecond|Média|A taxa de transferência de download de nuvem para o Azure durante o período de relatório.|Nenhuma|
|BytesUploadedToCloud|Bytes de nuvem carregados (dispositivo)|Bytes|Média|O número total de bytes que são carregados no Azure de um dispositivo durante o período de relatório.|Nenhuma|
|HyperVVirtualProcessorUtilization|Computação de borda – percentual de CPU|Percentagem|Média|Porcentagem de uso da CPU|InstanceName|
|HyperVMemoryUtilization|Computação de borda-uso de memória|Percentagem|Média|Quantidade de RAM em uso|InstanceName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|FailedRuns|Corridas falhadas|Contagem|Total||pipelinename, ActivityName|
|SuccessfulRuns|Execuções com êxito|Contagem|Total||pipelinename, ActivityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PipelineFailedRuns|Métricas de execuções de pipeline com falha|Contagem|Total||FailureType, nome|
|PipelineSucceededRuns|Métricas de execuções de pipeline com êxito|Contagem|Total||FailureType, nome|
|PipelineCancelledRuns|Métricas de execuções de pipeline canceladas|Contagem|Total||FailureType, nome|
|ActivityFailedRuns|Métricas de execuções de atividade com falha|Contagem|Total||ActivityType, Pipelinename, Falhatype, nome|
|ActivitySucceededRuns|Métricas de execuções de atividades bem-sucedidas|Contagem|Total||ActivityType, Pipelinename, Falhatype, nome|
|ActivityCancelledRuns|Métricas de execuções de atividade canceladas|Contagem|Total||ActivityType, Pipelinename, Falhatype, nome|
|TriggerFailedRuns|Métricas de execuções de gatilho com falha|Contagem|Total||Nome, Falhatype|
|TriggerSucceededRuns|Métricas de execuções de gatilho com êxito|Contagem|Total||Nome, Falhatype|
|TriggerCancelledRuns|O gatilho cancelado executa métricas|Contagem|Total||Nome, Falhatype|
|IntegrationRuntimeCpuPercentage|Utilização da CPU do Integration Runtime|Percentagem|Média||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Memória disponível do Integration Runtime|Bytes|Média||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAverageTaskPickupDelay|Duração da fila do Integration Runtime|Segundos|Média||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|Comprimento da fila do Integration Runtime|Contagem|Média||IntegrationRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|Contagem de nós disponíveis do Integration Runtime|Contagem|Média||IntegrationRuntimeName|
|MaxAllowedResourceCount|Contagem máxima de entidades permitidas|Contagem|Máximo||Nenhuma|
|MaxAllowedFactorySizeInGbUnits|Tamanho máximo de fábrica permitido (unidade GB)|Contagem|Máximo||Nenhuma|
|ResourceCount|Contagem total de entidades|Contagem|Máximo||Nenhuma|
|FactorySizeInGbUnits|Tamanho total de fábrica (unidade GB)|Contagem|Máximo||Nenhuma|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|JobEndedSuccess|Trabalhos com êxito|Contagem|Total|Contagem de trabalhos bem-sucedidos.|Nenhuma|
|JobEndedFailure|Trabalhos com falha|Contagem|Total|Contagem de trabalhos com falha.|Nenhuma|
|JobEndedCancelled|Trabalhos cancelados|Contagem|Total|Contagem de trabalhos cancelados.|Nenhuma|
|JobAUEndedSuccess|Tempo de AU bem-sucedido|Segundos|Total|Tempo total de AU para trabalhos bem-sucedidos.|Nenhuma|
|JobAUEndedFailure|Tempo de AU com falha|Segundos|Total|Tempo total de AU para trabalhos com falha.|Nenhuma|
|JobAUEndedCancelled|Hora da AU cancelada|Segundos|Total|Tempo total de AU para trabalhos cancelados.|Nenhuma|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalStorage|Armazenamento Total|Bytes|Máximo|Quantidade total de dados armazenados na conta.|Nenhuma|
|Gravado em|Dados gravados|Bytes|Total|Quantidade total de dados gravados na conta.|Nenhuma|
|DataRead|Leitura de dados|Bytes|Total|Quantidade total de dados lidos da conta.|Nenhuma|
|WriteRequests|Solicitações de gravação|Contagem|Total|Contagem de solicitações de gravação de dados para a conta.|Nenhuma|
|ReadRequests|Solicitações de leitura|Contagem|Total|Contagem de solicitações de leitura de dados para a conta.|Nenhuma|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentual de CPU|Percentagem|Média|Percentual de CPU|Nenhuma|
|memory_percent|Porcentagem de memória|Percentagem|Média|Porcentagem de memória|Nenhuma|
|io_consumption_percent|Porcentagem de e/s|Percentagem|Média|Porcentagem de e/s|Nenhuma|
|storage_percent|Porcentagem de armazenamento|Percentagem|Média|Porcentagem de armazenamento|Nenhuma|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhuma|
|storage_limit|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Nenhuma|
|serverlog_storage_percent|Porcentagem de armazenamento de log do servidor|Percentagem|Média|Porcentagem de armazenamento de log do servidor|Nenhuma|
|serverlog_storage_usage|Armazenamento de log do servidor usado|Bytes|Média|Armazenamento de log do servidor usado|Nenhuma|
|serverlog_storage_limit|Limite de armazenamento de log do servidor|Bytes|Média|Limite de armazenamento de log do servidor|Nenhuma|
|active_connections|Conexões ativas|Contagem|Média|Conexões ativas|Nenhuma|
|connections_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Nenhuma|
|seconds_behind_master|Atraso de replicação em segundos|Contagem|Máximo|Atraso de replicação em segundos|Nenhuma|
|backup_storage_used|Armazenamento de backup usado|Bytes|Média|Armazenamento de backup usado|Nenhuma|
|network_bytes_egress|Saída da Rede|Bytes|Total|Saída de rede entre conexões ativas|Nenhuma|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Entrada de rede em conexões ativas|Nenhuma|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentual de CPU|Percentagem|Média|Percentual de CPU|Nenhuma|
|memory_percent|Porcentagem de memória|Percentagem|Média|Porcentagem de memória|Nenhuma|
|io_consumption_percent|Porcentagem de e/s|Percentagem|Média|Porcentagem de e/s|Nenhuma|
|storage_percent|Porcentagem de armazenamento|Percentagem|Média|Porcentagem de armazenamento|Nenhuma|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhuma|
|storage_limit|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Nenhuma|
|serverlog_storage_percent|Porcentagem de armazenamento de log do servidor|Percentagem|Média|Porcentagem de armazenamento de log do servidor|Nenhuma|
|serverlog_storage_usage|Armazenamento de log do servidor usado|Bytes|Média|Armazenamento de log do servidor usado|Nenhuma|
|serverlog_storage_limit|Limite de armazenamento de log do servidor|Bytes|Máximo|Limite de armazenamento de log do servidor|Nenhuma|
|active_connections|Conexões ativas|Contagem|Média|Conexões ativas|Nenhuma|
|connections_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Nenhuma|
|seconds_behind_master|Atraso de replicação em segundos|Contagem|Máximo|Atraso de replicação em segundos|Nenhuma|
|backup_storage_used|Armazenamento de backup usado|Bytes|Média|Armazenamento de backup usado|Nenhuma|
|network_bytes_egress|Saída da Rede|Bytes|Total|Saída de rede entre conexões ativas|Nenhuma|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Entrada de rede em conexões ativas|Nenhuma|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentual de CPU|Percentagem|Média|Percentual de CPU|Nenhuma|
|memory_percent|Porcentagem de memória|Percentagem|Média|Porcentagem de memória|Nenhuma|
|io_consumption_percent|Porcentagem de e/s|Percentagem|Média|Porcentagem de e/s|Nenhuma|
|storage_percent|Porcentagem de armazenamento|Percentagem|Média|Porcentagem de armazenamento|Nenhuma|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhuma|
|storage_limit|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Nenhuma|
|serverlog_storage_percent|Porcentagem de armazenamento de log do servidor|Percentagem|Média|Porcentagem de armazenamento de log do servidor|Nenhuma|
|serverlog_storage_usage|Armazenamento de log do servidor usado|Bytes|Média|Armazenamento de log do servidor usado|Nenhuma|
|serverlog_storage_limit|Limite de armazenamento de log do servidor|Bytes|Máximo|Limite de armazenamento de log do servidor|Nenhuma|
|active_connections|Conexões ativas|Contagem|Média|Conexões ativas|Nenhuma|
|connections_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Nenhuma|
|backup_storage_used|Armazenamento de backup usado|Bytes|Média|Armazenamento de backup usado|Nenhuma|
|network_bytes_egress|Saída da Rede|Bytes|Total|Saída de rede entre conexões ativas|Nenhuma|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Entrada de rede em conexões ativas|Nenhuma|
|pg_replica_log_delay_in_seconds|Atraso de réplica|Segundos|Máximo|Atraso de réplica em segundos|Nenhuma|
|pg_replica_log_delay_in_bytes|Atraso máximo entre réplicas|Bytes|Máximo|Atraso em bytes da réplica mais defasante|Nenhuma|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentual de CPU|Percentagem|Média|Percentual de CPU|Nenhuma|
|memory_percent|Porcentagem de memória|Percentagem|Média|Porcentagem de memória|Nenhuma|
|IOPS|IOPS|Contagem|Média|Operações de e/s por segundo|Nenhuma|
|storage_percent|Porcentagem de armazenamento|Percentagem|Média|Porcentagem de armazenamento|Nenhuma|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhuma|
|active_connections|Conexões ativas|Contagem|Média|Conexões ativas|Nenhuma|
|network_bytes_egress|Saída da Rede|Bytes|Total|Saída de rede entre conexões ativas|Nenhuma|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Entrada de rede em conexões ativas|Nenhuma|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentativas de envio de mensagem de telemetria|Contagem|Total|Número de mensagens de telemetria do dispositivo para a nuvem que tentaram ser enviadas para o Hub IoT|Nenhuma|
|d2c.telemetry.ingress.success|Mensagens de telemetria enviadas|Contagem|Total|Número de mensagens de telemetria do dispositivo para a nuvem enviadas com êxito para o Hub IoT|Nenhuma|
|c2d.commands.egress.complete.success|Entregas de mensagem C2D concluídas|Contagem|Total|Número de entregas de mensagem da nuvem para o dispositivo concluídas com êxito pelo dispositivo|Nenhuma|
|c2d.commands.egress.abandon.success|C2D mensagens abandonadas|Contagem|Total|Número de mensagens da nuvem para o dispositivo abandonadas pelo dispositivo|Nenhuma|
|c2d.commands.egress.reject.success|Mensagens C2D rejeitadas|Contagem|Total|Número de mensagens da nuvem para o dispositivo rejeitadas pelo dispositivo|Nenhuma|
|C2DMessagesExpired|C2D mensagens expiradas (visualização)|Contagem|Total|Número de mensagens de nuvem para o dispositivo expiradas|Nenhuma|
|devices.totalDevices|Total de dispositivos (depreciados)|Contagem|Total|Número de dispositivos registrados no Hub IoT|Nenhuma|
|devices.connectedDevices.allProtocol|Dispositivos conectados (depreciados) |Contagem|Total|Número de dispositivos conectados ao seu hub IoT|Nenhuma|
|d2c.telemetry.egress.success|Roteamento: mensagens de telemetria entregues|Contagem|Total|O número de vezes que as mensagens foram entregues com êxito a todos os pontos de extremidade usando o roteamento do Hub IoT. Se uma mensagem for roteada para vários pontos de extremidade, esse valor aumentará em um para cada entrega bem-sucedida. Se uma mensagem for entregue ao mesmo ponto de extremidade várias vezes, esse valor aumentará em um para cada entrega bem-sucedida.|Nenhuma|
|d2c.telemetry.egress.dropped|Roteamento: mensagens de telemetria eliminadas |Contagem|Total|O número de vezes que as mensagens foram removidas pelo roteamento do Hub IoT devido a pontos de extremidade inativos. Esse valor não conta as mensagens entregues à rota de fallback, pois as mensagens ignoradas não são entregues lá.|Nenhuma|
|d2c.telemetry.egress.orphaned|Roteamento: mensagens de telemetria órfãs |Contagem|Total|O número de vezes que as mensagens ficaram órfãs pelo roteamento do Hub IoT porque não corresponderam a nenhuma regra de roteamento (incluindo a norma de fallback). |Nenhuma|
|d2c.telemetry.egress.invalid|Roteamento: mensagens de telemetria incompatíveis|Contagem|Total|O número de vezes que o roteamento do Hub IoT falhou ao entregar mensagens devido a uma incompatibilidade com o ponto de extremidade. Esse valor não inclui repetições.|Nenhuma|
|d2c.telemetry.egress.fallback|Roteamento: mensagens entregues ao fallback|Contagem|Total|O número de vezes que o roteamento do Hub IoT fornece mensagens ao ponto de extremidade associado à rota de fallback.|Nenhuma|
|d2c.endpoints.egress.eventHubs|Roteamento: mensagens entregues ao Hub de eventos|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para pontos de extremidade do hub de eventos.|Nenhuma|
|d2c.endpoints.latency.eventHubs|Encaminhamento: latência de mensagens para O Hub de Eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem em um ponto de extremidade do hub de eventos.|Nenhuma|
|d2c.endpoints.egress.serviceBusQueues|Roteamento: mensagens entregues à fila do barramento de serviço|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens aos pontos de extremidade da fila do barramento de serviço.|Nenhuma|
|d2c.endpoints.latency.serviceBusQueues|Encaminhamento: latência de mensagens para fila de autocarros de serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade da fila do barramento de serviço.|Nenhuma|
|d2c.endpoints.egress.serviceBusTopics|Roteamento: mensagens entregues ao tópico do barramento de serviço|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para pontos de extremidade de tópico do barramento de serviço.|Nenhuma|
|d2c.endpoints.latency.serviceBusTopics|Roteamento: latência de mensagem para o tópico do barramento de serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade de tópico do barramento de serviço.|Nenhuma|
|d2c.endpoints.egress.builtIn.events|Roteamento: mensagens entregues a mensagens/eventos|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para o ponto de extremidade interno (mensagens/eventos).|Nenhuma|
|d2c.endpoints.latency.builtIn.events|Roteamento: latência de mensagem para mensagens/eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem e o Hub IoT e a entrada da mensagem de telemetria no ponto de extremidade interno (mensagens/eventos).|Nenhuma|
|d2c.endpoints.egress.storage|Roteamento: mensagens entregues ao armazenamento|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para pontos de extremidade de armazenamento.|Nenhuma|
|d2c.endpoints.latency.storage|Roteamento: latência de mensagem para armazenamento|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade de armazenamento.|Nenhuma|
|d2c.endpoints.egress.storage.bytes|Encaminhamento: dados entregues ao armazenamento|Bytes|Total|A quantidade de dados (bytes) de roteamento do Hub IoT entregues aos pontos de extremidade de armazenamento.|Nenhuma|
|d2c.endpoints.egress.storage.blobs|Encaminhamento: bolhas entregues ao armazenamento|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu BLOBs para pontos de extremidade de armazenamento.|Nenhuma|
|EventGridDeliveries|Entregas da grade de eventos (visualização)|Contagem|Total|O número de eventos do Hub IoT publicados na grade de eventos. Use a dimensão de resultado para o número de solicitações bem-sucedidas e com falha. A dimensão EventType mostra o tipo de evento (https://aka.ms/ioteventgrid).|ResourceId, resultado, EventType|
|EventGridLatency|Latência da grade de eventos (versão prévia)|Milissegundos|Média|A latência média (milissegundos) de quando o evento do Hub IOT foi gerado quando o evento foi publicado na grade de eventos. Esse número é uma média entre todos os tipos de evento. Use a dimensão EventType para ver a latência de um tipo específico de evento.|ResourceId, EventType|
|d2c.twin.read.success|Leituras de entrelaçamento bem-sucedidas de dispositivos|Contagem|Total|A contagem de todas as leituras de entrelaças iniciadas pelo dispositivo bem-sucedidas.|Nenhuma|
|d2c.twin.read.failure|Leituras gémeas falhadas de dispositivos|Contagem|Total|A contagem de todas as leituras de entrelaças iniciadas pelo dispositivo com falha.|Nenhuma|
|d2c.twin.read.size|Tamanho da resposta das leituras gémeas dos dispositivos|Bytes|Média|A média, o mínimo e o máximo de todas as leituras de entrelaçadas iniciadas pelo dispositivo bem-sucedidas.|Nenhuma|
|d2c.twin.update.success|Atualizações de atualização com êxito de dispositivos|Contagem|Total|A contagem de todas as atualizações de atualização do dispositivo iniciadas com êxito.|Nenhuma|
|d2c.twin.update.failure|Atualizações duplas falhadas de dispositivos|Contagem|Total|A contagem de todas as atualizações de atualização do dispositivo iniciadas com falha.|Nenhuma|
|d2c.twin.update.size|Tamanho de atualizações de atualização de papel dos dispositivos|Bytes|Média|O tamanho médio, mínimo e máximo de todas as atualizações de atualização do dispositivo iniciadas com êxito.|Nenhuma|
|C2D. Methods. Success|Invocações de método diretos bem-sucedidas|Contagem|Total|A contagem de todas as chamadas de método diretas bem-sucedidas.|Nenhuma|
|c2d.methods.failure|Invocações de método direto falhado|Contagem|Total|A contagem de todas as chamadas de método diretas com falha.|Nenhuma|
|c2d.methods.requestSize|Solicitar dimensão das invocações do método direto|Bytes|Média|A média, o mínimo e o máximo de todas as solicitações de método diretas bem-sucedidas.|Nenhuma|
|c2d.methods.responseSize|Tamanho da resposta das invocações do método direto|Bytes|Média|A média, o mínimo e o máximo de todas as respostas de método diretas bem-sucedidas.|Nenhuma|
|c2d.twin.read.success|Leituras de cópia com êxito do back-end|Contagem|Total|A contagem de todas as leituras de cópia de bits iniciadas de back-end bem-sucedidas.|Nenhuma|
|c2d.twin.read.failure|Gémeo falhado lê a partir de trás|Contagem|Total|A contagem de todas as leituras de cópia de bits iniciadas pelo back-end com falha.|Nenhuma|
|c2d.twin.read.size|Tamanho da resposta de leituras gémeas da parte de trás|Bytes|Média|A média, mín e máx de todas as leituras de cópia do back-end iniciadas com êxito.|Nenhuma|
|c2d.twin.update.success|Atualizações gémeas bem sucedidas a partir de trás|Contagem|Total|A contagem de todas as atualizações de cópia do back-end iniciadas com êxito.|Nenhuma|
|c2d.twin.update.failure|Atualizações gémeas falhadas a partir de trás|Contagem|Total|A contagem de todas as atualizações de cópia de bits iniciadas pelo back-end com falha.|Nenhuma|
|c2d.twin.update.size|Tamanho das atualizações de cópia do back-end|Bytes|Média|O tamanho médio, mínimo e máximo de todas as atualizações de cópia do back-end iniciadas com êxito.|Nenhuma|
|twinQueries.success|Consultas de entrelaçamento bem-sucedidas|Contagem|Total|A contagem de todas as consultas de myup com êxito.|Nenhuma|
|twinQueries.failure|Perguntas gémeas falhadas|Contagem|Total|A contagem de todas as consultas de entrelaçamento com falha.|Nenhuma|
|twinQueries.resultSize|Tamanho do resultado de consultas de entrelaçamento|Bytes|Média|A média, o mínimo e o máximo do tamanho do resultado de todas as consultas de myup com êxito.|Nenhuma|
|jobs.createTwinUpdateJob.success|Criações bem-sucedidas de trabalhos de atualização de entrelaçamento|Contagem|Total|A contagem de todas as criações de trabalhos de atualização de entrelaçamento com êxito.|Nenhuma|
|jobs.createTwinUpdateJob.failure|Criações falhadas de empregos de atualização dupla|Contagem|Total|A contagem de todas as falhas na criação de trabalhos de atualização de entrelaçamento.|Nenhuma|
|jobs.createDirectMethodJob.success|Criações de trabalhos de invocação de método com êxito|Contagem|Total|A contagem de toda a criação bem-sucedida de trabalhos de invocação de método direto.|Nenhuma|
|jobs.createDirectMethodJob.failure|Criações falhadas de empregos de invocação de métodos|Contagem|Total|A contagem de todas as falhas na criação de trabalhos de invocação de método direto.|Nenhuma|
|jobs.listJobs.success|Chamadas com êxito para listar trabalhos|Contagem|Total|A contagem de todas as chamadas bem-sucedidas para listar trabalhos.|Nenhuma|
|jobs.listJobs.failure|Chamadas falhadas para listar empregos|Contagem|Total|A contagem de todas as chamadas com falha para listar trabalhos.|Nenhuma|
|jobs.cancelJob.success|Cancelamentos de trabalho bem-sucedidos|Contagem|Total|A contagem de todas as chamadas bem-sucedidas para cancelar um trabalho.|Nenhuma|
|jobs.cancelJob.failure|Cancelamentos de emprego falhados|Contagem|Total|A contagem de todas as chamadas com falha para cancelar um trabalho.|Nenhuma|
|jobs.queryJobs.success|Consultas de trabalho com êxito|Contagem|Total|A contagem de todas as chamadas bem-sucedidas para os trabalhos de consulta.|Nenhuma|
|jobs.queryJobs.failure|Consultas de trabalho falhadas|Contagem|Total|A contagem de todas as chamadas com falha para trabalhos de consulta.|Nenhuma|
|jobs.completed|Trabalhos concluídos|Contagem|Total|A contagem de todos os trabalhos concluídos.|Nenhuma|
|jobs.failed|Empregos falhados|Contagem|Total|A contagem de todos os trabalhos com falha.|Nenhuma|
|d2c.telemetry.ingress.sendThrottle|Número de erros de estrangulamento|Contagem|Total|Número de erros de limitação devido a aceleradores de taxa de transferência do dispositivo|Nenhuma|
|dailyMessageQuotaUsed|Número total de mensagens usadas|Contagem|Média|Número total de mensagens usadas hoje|Nenhuma|
|deviceDataUsage|Uso total de dados do dispositivo|Bytes|Total|Bytes transferidos de e para todos os dispositivos conectados ao IotHub|Nenhuma|
|deviceDataUsageV2|Uso total de dados do dispositivo (versão prévia)|Bytes|Total|Bytes transferidos de e para todos os dispositivos conectados ao IotHub|Nenhuma|
|totalDeviceCount|Total de dispositivos (visualização)|Contagem|Média|Número de dispositivos registrados no Hub IoT|Nenhuma|
|connectedDeviceCount|Dispositivos conectados (pré-visualização)|Contagem|Média|Número de dispositivos conectados ao seu hub IoT|Nenhuma|
|Figura|Métricas de Configuração|Contagem|Total|Métricas para operações de configuração|Nenhuma|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RegistrationAttempts|Tentativas de registro|Contagem|Total|Número de tentativas de registro de dispositivo|ProvisioningServiceName, IotHubName, status|
|DeviceAssignments|Dispositivos atribuídos|Contagem|Total|Número de dispositivos atribuídos a um hub IoT|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Tentativas de atestado|Contagem|Total|Número de tentativas de atestado de dispositivo|ProvisioningServiceName, status, protocolo|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Addregion|Região adicionada|Contagem|Contagem|Região adicionada|Região|
|AvailableStorage|Armazenamento disponível|Bytes|Total|Armazenamento total disponível relatado à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|CassandraConnectionClosures|Fechamentos de conexão do Cassandra|Contagem|Total|Número de conexões Cassandra que foram fechadas, relatadas a uma granularidade de 1 minuto|APIType, região, ClosureReason|
|CassandraRequestCharges|Encargos de solicitação do Cassandra|Contagem|Total|RUs consumido para solicitações Cassandra feitas|APIType, DatabaseName, CollectionName, região, OperationType, ResourceType|
|CassandraRequests|Solicitações Cassandra|Contagem|Contagem|Número de solicitações Cassandra feitas|APIType, DatabaseName, CollectionName, região, OperationType, ResourceType, ErrorCode|
|CreateAccount|Conta criada|Contagem|Contagem|Conta criada|Nenhuma|
|DataUsage|Utilização de Dados|Bytes|Total|Uso total de dados relatados à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|DeleteAccount|Conta excluída|Contagem|Contagem|Conta excluída|Nenhuma|
|DocumentCount|Contagem de documentos|Contagem|Total|Contagem total de documentos relatados à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|DocumentQuota|Cota de documentos|Bytes|Total|Cota de armazenamento total relatada à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|IndexUsage|Uso do índice|Bytes|Total|Uso total do índice relatado à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|MetadataRequests|Solicitações de metadados|Contagem|Contagem|Contagem de solicitações de metadados. Cosmos DB mantém a coleção de metadados do sistema para cada conta, que permite enumerar coleções, bancos de dados, etc e suas configurações, gratuitamente.|DatabaseName, CollectionName, região, StatusCode, função|
|MongoRequestCharge|Encargo de solicitação do Mongo|Contagem|Total|Unidades de solicitação Mongo consumidas|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|MongoRequests|Solicitações Mongo|Contagem|Contagem|Número de solicitações Mongo feitas|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|MongoRequestsCount|Taxa de solicitação de Mongo|CountPerSecond|Média|Contagem de solicitações Mongo por segundo|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|MongoRequestsDelete|Taxa de solicitação de exclusão de Mongo|CountPerSecond|Média|Solicitação de exclusão de Mongo por segundo|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|MongoRequestsInsert|Taxa de solicitação de inserção de Mongo|CountPerSecond|Média|Contagem de inserção de Mongo por segundo|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|MongoRequestsQuery|Taxa de solicitação de consulta do Mongo|CountPerSecond|Média|Solicitação de consulta Mongo por segundo|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|MongoRequestsUpdate|Taxa de solicitação de atualização do Mongo|CountPerSecond|Média|Solicitação de atualização de Mongo por segundo|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|ProvisionedThroughput|Débito Aprovisionado|Contagem|Máximo|Débito Aprovisionado|DatabaseName, CollectionName|
|RegionFailover|Falha na região|Contagem|Contagem|Falha na região|Nenhuma|
|RemoveRegion|Região removida|Contagem|Contagem|Região removida|Região|
|ReplicationLatency|Latência de replicação P99|Milissegundos|Média|Latência de replicação P99 nas regiões de origem e de destino para a conta habilitada para Geografia|SourceRegion,TargetRegion|
|Indisponibilidade|Disponibilidade do serviço|Percentagem|Média|Disponibilidade de solicitações de conta em uma hora, granularidade de dia ou mês|Nenhuma|
|TotalRequestUnits|Total de unidades de solicitação|Contagem|Total|Unidades de solicitação consumidas|DatabaseName, CollectionName, região, StatusCode, OperationType|
|Total De Pedidos|Total de Pedidos|Contagem|Contagem|Número de solicitações feitas|DatabaseName, CollectionName, região, StatusCode, OperationType|
|UpdateAccountKeys|Chaves de conta atualizadas|Contagem|Contagem|Chaves de conta atualizadas|KeyType|
|UpdateAccountNetworkSettings|Configurações de rede da conta atualizadas|Contagem|Contagem|Configurações de rede da conta atualizadas|Nenhuma|
|UpdateAccountReplicationSettings|Configurações de replicação de conta atualizadas|Contagem|Contagem|Configurações de replicação de conta atualizadas|Nenhuma|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft. EnterpriseKnowledgeGraph/serviços

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TransactionCount|Contagem de transações|Contagem|Contagem|Contagem total de transações|TransactionCount|
|SuccessCount|Contagem de Sucesso|Contagem|Contagem|Contagem de transações com êxito|SuccessCount|
|FailureCount|Contagem de falhas|Contagem|Contagem|Contagem de transações com falha|FailureCount|
|SuccessLatency|Latência de êxito|Milissegundos|Média|Latência de transações bem-sucedidas|SuccessCount|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Tópico|
|PublishFailCount|Publicar eventos com falha|Contagem|Total|Falha no total de eventos ao publicar neste tópico|Tópico, ErrorType, erro|
|PublishSuccessLatencyInMs|Latência de êxito na publicação|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Nenhuma|
|MatchedEventCount|Eventos correspondentes|Contagem|Total|Total de eventos correspondidos a esta assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Eventos com falha na entrega|Contagem|Total|O total de eventos não pôde ser entregue a esta assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, erro, ErrorType|
|DeliverySuccessCount|Eventos entregues|Contagem|Total|Total de eventos entregues a esta assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Eventos ignorados|Contagem|Total|Total de eventos descartados correspondentes a esta assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|DeadLetteredCount|Eventos inativos|Contagem|Total|Total de eventos mortos inativos correspondentes a esta assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Nenhuma|
|PublishFailCount|Publicar eventos com falha|Contagem|Total|Falha no total de eventos ao publicar neste tópico|ErrorType, erro|
|UnmatchedEventCount|Eventos sem correspondência|Contagem|Total|Total de eventos que não correspondem a nenhuma das assinaturas de evento deste tópico|Nenhuma|
|PublishSuccessLatencyInMs|Latência de êxito na publicação|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Nenhuma|
|MatchedEventCount|Eventos correspondentes|Contagem|Total|Total de eventos correspondidos a esta assinatura de evento|EventSubscriptionName|
|DeliveryAttemptFailCount|Eventos com falha na entrega|Contagem|Total|O total de eventos não pôde ser entregue a esta assinatura de evento|Erro, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Eventos entregues|Contagem|Total|Total de eventos entregues a esta assinatura de evento|EventSubscriptionName|
|DestinationProcessingDurationInMs|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|EventSubscriptionName|
|DroppedEventCount|Eventos ignorados|Contagem|Total|Total de eventos descartados correspondentes a esta assinatura de evento|DropReason,EventSubscriptionName|
|DeadLetteredCount|Eventos inativos|Contagem|Total|Total de eventos mortos inativos correspondentes a esta assinatura de evento|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|MatchedEventCount|Eventos correspondentes|Contagem|Total|Total de eventos correspondidos a esta assinatura de evento|Nenhuma|
|DeliveryAttemptFailCount|Eventos com falha na entrega|Contagem|Total|O total de eventos não pôde ser entregue a esta assinatura de evento|Erro, ErrorType|
|DeliverySuccessCount|Eventos entregues|Contagem|Total|Total de eventos entregues a esta assinatura de evento|Nenhuma|
|DestinationProcessingDurationInMs|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|Nenhuma|
|DroppedEventCount|Eventos ignorados|Contagem|Total|Total de eventos descartados correspondentes a esta assinatura de evento|DropReason|
|DeadLetteredCount|Eventos inativos|Contagem|Total|Total de eventos mortos inativos correspondentes a esta assinatura de evento|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Nenhuma|
|PublishFailCount|Publicar eventos com falha|Contagem|Total|Falha no total de eventos ao publicar neste tópico|ErrorType, erro|
|UnmatchedEventCount|Eventos sem correspondência|Contagem|Total|Total de eventos que não correspondem a nenhuma das assinaturas de evento deste tópico|Nenhuma|
|PublishSuccessLatencyInMs|Latência de êxito na publicação|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Nenhuma|



## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfulRequests|Solicitações bem-sucedidas|Contagem|Total|Solicitações bem-sucedidas para o Microsoft. EventHub.|EntityName, OperationResult|
|ServerErrors|Erros do servidor.|Contagem|Total|Erros de servidor para Microsoft. EventHub.|EntityName, OperationResult|
|UserErrors|Erros do usuário.|Contagem|Total|Erros de usuário para Microsoft. EventHub.|EntityName, OperationResult|
|QuotaExceededErrors|A cota excedeu erros.|Contagem|Total|A cota excedeu erros para o Microsoft. EventHub.|EntityName, OperationResult|
|ThrottledRequests|Solicitações limitadas.|Contagem|Total|Solicitações limitadas para Microsoft. EventHub.|EntityName, OperationResult|
|IncomingRequests|Solicitações de entrada|Contagem|Total|Solicitações de entrada para o Microsoft. EventHub.|EntityName|
|IncomingMessages|Mensagens de entrada|Contagem|Total|Mensagens de entrada para o Microsoft. EventHub.|EntityName|
|OutgoingMessages|Mensagens de saída|Contagem|Total|Mensagens de saída para o Microsoft. EventHub.|EntityName|
|IncomingBytes|Bytes de entrada.|Bytes|Total|Bytes de entrada para o Microsoft. EventHub.|EntityName|
|OutgoingBytes|Bytes de saída.|Bytes|Total|Bytes de saída para o Microsoft. EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Contagem|Média|Total de conexões ativas para o Microsoft. EventHub.|Nenhuma|
|ConnectionsOpened|Conexões abertas.|Contagem|Média|Conexões abertas para Microsoft. EventHub.|EntityName|
|ConnectionsClosed|Conexões fechadas.|Contagem|Média|Conexões fechadas para o Microsoft. EventHub.|EntityName|
|CaptureBacklog|Capturar registro posterior.|Contagem|Total|Capturar pendências para Microsoft. EventHub.|EntityName|
|CapturedMessages|Mensagens capturadas.|Contagem|Total|Mensagens capturadas para o Microsoft. EventHub.|EntityName|
|CapturedBytes|Bytes capturados.|Bytes|Total|Bytes capturados para o Microsoft. EventHub.|EntityName|
|Tamanho|Tamanho|Bytes|Média|Tamanho de um EventHub em bytes.|EntityName|
|INREQS|Solicitações de entrada (preteridas)|Contagem|Total|Total de solicitações de envio de entrada para um namespace (preterido)|Nenhuma|
|SUCCREQ|Solicitações com êxito (preteridas)|Contagem|Total|Total de solicitações bem-sucedidas para um namespace (preterido)|Nenhuma|
|FAILREQ|Solicitações com falha (preteridas)|Contagem|Total|Total de solicitações com falha para um namespace (preterido)|Nenhuma|
|SVRBSY|Erros do servidor ocupado (preterido)|Contagem|Total|Total de erros do servidor ocupado para um namespace (preterido)|Nenhuma|
|INTERR|Erros de servidor interno (preterido)|Contagem|Total|Total de erros internos do servidor para um namespace (preterido)|Nenhuma|
|MISCERR|Outros erros (preterido)|Contagem|Total|Total de solicitações com falha para um namespace (preterido)|Nenhuma|
|INMSGS|Mensagens de entrada (obsoletas) (preteridas)|Contagem|Total|Total de mensagens de entrada para um namespace. Essa métrica foi preterida. Use a métrica de mensagens de entrada em vez disso (preterido)|Nenhuma|
|EHINMSGS|Mensagens de entrada (preteridas)|Contagem|Total|Total de mensagens de entrada para um namespace (preterido)|Nenhuma|
|Mensagens inenviadas|Mensagens de saída (obsoletas) (preteridas)|Contagem|Total|Total de mensagens de saída para um namespace. Essa métrica foi preterida. Use a métrica de mensagens de saída em vez disso (preterido)|Nenhuma|
|EHOUTMSGS|Mensagens de saída (preteridas)|Contagem|Total|Total de mensagens de saída para um namespace (preterido)|Nenhuma|
|EHINMBS|Bytes de entrada (obsoletos) (preterido)|Bytes|Total|Taxa de transferência de mensagem de entrada do hub de eventos para um namespace. Essa métrica foi preterida. Use a métrica de bytes de entrada em vez disso (preterido)|Nenhuma|
|EHINBYTES|Bytes de entrada (preterido)|Bytes|Total|Taxa de transferência de mensagem de entrada do hub de eventos para um namespace (preterido)|Nenhuma|
|EHOUTMBS|Bytes de saída (obsoletos) (preterido)|Bytes|Total|Taxa de transferência de mensagem de saída do hub de eventos para um namespace. Essa métrica foi preterida. Use a métrica de bytes de saída em vez disso (preterido)|Nenhuma|
|EHOUTBYTES|Bytes de saída (preteridos)|Bytes|Total|Taxa de transferência de mensagem de saída do hub de eventos para um namespace (preterido)|Nenhuma|
|EHABL|Arquivar mensagens de pendências (preterido)|Contagem|Total|Mensagens de arquivamento do hub de eventos no backlog para um namespace (preterido)|Nenhuma|
|EHAMSGS|Arquivar mensagens (preterido)|Contagem|Total|Mensagens arquivadas do hub de eventos em um namespace (preterido)|Nenhuma|
|EHAMBS|Taxa de transferência de mensagem de arquivamento (preterido)|Bytes|Total|Taxa de transferência de mensagem arquivada do hub de eventos em um namespace (preterido)|Nenhuma|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfulRequests|Solicitações bem-sucedidas|Contagem|Total|Solicitações bem-sucedidas para o Microsoft. EventHub.|OperationResult|
|ServerErrors|Erros do servidor.|Contagem|Total|Erros de servidor para Microsoft. EventHub.|OperationResult|
|UserErrors|Erros do usuário.|Contagem|Total|Erros de usuário para Microsoft. EventHub.|OperationResult|
|QuotaExceededErrors|A cota excedeu erros.|Contagem|Total|A cota excedeu erros para o Microsoft. EventHub.|OperationResult|
|ThrottledRequests|Solicitações limitadas.|Contagem|Total|Solicitações limitadas para Microsoft. EventHub.|OperationResult|
|IncomingRequests|Solicitações de entrada|Contagem|Total|Solicitações de entrada para o Microsoft. EventHub.|Nenhuma|
|IncomingMessages|Mensagens de entrada|Contagem|Total|Mensagens de entrada para o Microsoft. EventHub.|Nenhuma|
|OutgoingMessages|Mensagens de saída|Contagem|Total|Mensagens de saída para o Microsoft. EventHub.|Nenhuma|
|IncomingBytes|Bytes de entrada.|Bytes|Total|Bytes de entrada para o Microsoft. EventHub.|Nenhuma|
|OutgoingBytes|Bytes de saída.|Bytes|Total|Bytes de saída para o Microsoft. EventHub.|Nenhuma|
|ActiveConnections|ActiveConnections|Contagem|Média|Total de conexões ativas para o Microsoft. EventHub.|Nenhuma|
|ConnectionsOpened|Conexões abertas.|Contagem|Média|Conexões abertas para Microsoft. EventHub.|Nenhuma|
|ConnectionsClosed|Conexões fechadas.|Contagem|Média|Conexões fechadas para o Microsoft. EventHub.|Nenhuma|
|CaptureBacklog|Capturar registro posterior.|Contagem|Total|Capturar pendências para Microsoft. EventHub.|Nenhuma|
|CapturedMessages|Mensagens capturadas.|Contagem|Total|Mensagens capturadas para o Microsoft. EventHub.|Nenhuma|
|CapturedBytes|Bytes capturados.|Bytes|Total|Bytes capturados para o Microsoft. EventHub.|Nenhuma|
|CPU|CPU|Percentagem|Máximo|Utilização da CPU para o cluster do hub de eventos como uma porcentagem|Função|
|AvailableMemory|Memória Disponível|Percentagem|Máximo|Memória disponível para o cluster do hub de eventos como uma porcentagem da memória total.|Função|


## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/clusters

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|GatewayRequests|Solicitações de gateway|Contagem|Total|Número de solicitações de gateway|HttpStatus|
|CategorizedGatewayRequests|Solicitações de gateway categorizadas|Contagem|Total|Número de solicitações de gateway por categorias (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|NumActiveWorkers|Número de trabalhadores ativos|Contagem|Máximo|Número de trabalhadores ativos|Nome métrico|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ObservedMetricValue|Valor de métrica observado|Contagem|Média|O valor calculado por autoescala quando executado|MetricTriggerSource|
|MetricThreshold|Limite de métrica|Contagem|Média|O limite de dimensionamento automático configurado quando o dimensionamento automático foi executado.|MetricTriggerRule|
|ObservedCapacity|Capacidade observada|Contagem|Média|A capacidade relatada para dimensionamento automático quando executada.|Nenhuma|
|ScaleActionsInitiated|Ações de escala iniciadas|Contagem|Total|A direção da operação de dimensionamento.|ScaleDirection|




## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Disponibilidade|Percentagem|Média|Percentual de testes de disponibilidade concluídos com êxito|availabilityResult/nome, availabilityResult/local|
|availabilityResults/contagem|Testes de disponibilidade|Contagem|Contagem|Contagem de testes de disponibilidade|availabilityResult/nome, availabilityResult/local, availabilityResult/êxito|
|availabilityResults/duration|Duração do teste de disponibilidade|Milissegundos|Média|Duração do teste de disponibilidade|availabilityResult/nome, availabilityResult/local, availabilityResult/êxito|
|browserTimings/networkDuration|Tempo de conexão de rede de carregamento de página|Milissegundos|Média|Tempo entre a solicitação de usuário e a conexão de rede. Inclui a pesquisa de DNS e a conexão de transporte.|Nenhuma|
|browserTimings/processingDuration|Tempo de processamento do cliente|Milissegundos|Média|Tempo entre o recebimento do último byte de um documento até que o DOM seja carregado. As solicitações assíncronas ainda podem estar sendo processadas.|Nenhuma|
|browserTimings/receiveDuration|Tempo de resposta de recebimento|Milissegundos|Média|Tempo entre o primeiro e o último byte, ou até a desconexão.|Nenhuma|
|browserTimings/sendDuration|Enviar tempo de solicitação|Milissegundos|Média|Tempo entre a conexão de rede e o recebimento do primeiro byte.|Nenhuma|
|browserTimings/totalDuration|Tempo de carregamento da página do navegador|Milissegundos|Média|Tempo desde a solicitação do usuário até que DOM, folhas de estilo, scripts e imagens sejam carregados.|Nenhuma|
|dependências/contagem|Chamadas de dependência|Contagem|Contagem|Contagem de chamadas feitas pelo aplicativo para recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/êxito, dependência/destino, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|dependências/duração|Duração da dependência|Milissegundos|Média|Duração das chamadas feitas pelo aplicativo para recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/êxito, dependência/destino, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|dependências/com falha|Falhas de chamada de dependência|Contagem|Contagem|Contagem de chamadas de dependência com falha feitas pelo aplicativo para recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/êxito, dependência/destino, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|pageViews/contagem|Vistas da página|Contagem|Contagem|Contagem de exibições de página.|operação/sintética, nuvem/roleName|
|pageViews/duração|Tempo de carregamento da exibição de página|Milissegundos|Média|Tempo de carregamento da exibição de página|operação/sintética, nuvem/roleName|
|performanceCounters/requestExecutionTime|Tempo de execução da solicitação HTTP|Milissegundos|Média|Tempo de execução da solicitação mais recente.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Solicitações HTTP na fila do aplicativo|Contagem|Média|Comprimento da fila de solicitações do aplicativo.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Taxa de solicitação HTTP|CountPerSecond|Média|Taxa de todas as solicitações para o aplicativo por segundo de ASP.NET.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Taxa de exceção|CountPerSecond|Média|Contagem de exceções manipuladas e não tratadas relatadas ao Windows, incluindo exceções .NET e exceções não gerenciadas que são convertidas em exceções .NET.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Taxa de e/s de processo|BytesPerSecond|Média|Total de bytes por segundo lidos e gravados em arquivos, rede e dispositivos.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|CPU do processo|Percentagem|Média|A porcentagem de tempo decorrido em que todos os threads de processo usaram o processador para executar instruções. Isso pode variar entre 0 e 100. Essa métrica indica o desempenho apenas do processo W3wp.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Tempo do processador|Percentagem|Média|A porcentagem de tempo que o processador gasta em threads não ociosos.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Memória disponível|Bytes|Média|Memória física disponível imediatamente para alocação para um processo ou para uso do sistema.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Processar bytes particulares|Bytes|Média|Memória atribuída exclusivamente aos processos do aplicativo monitorado.|cloud/roleInstance|
|solicitações/duração|Tempo de resposta do servidor|Milissegundos|Média|Tempo entre o recebimento de uma solicitação HTTP e a conclusão do envio da resposta.|solicitação/performanceBucket, solicitação/resultCode, operação/sintética, nuvem/roleInstance, solicitação/êxito, nuvem/roleName|
|solicitações/contagem|Solicitações do servidor|Contagem|Contagem|Contagem de solicitações HTTP concluída.|solicitação/performanceBucket, solicitação/resultCode, operação/sintética, nuvem/roleInstance, solicitação/êxito, nuvem/roleName|
|solicitações/com falha|Pedidos falhados|Contagem|Contagem|Contagem de solicitações HTTP marcadas como com falha. Na maioria dos casos, essas são solicitações com um código de resposta > = 400 e não iguais a 401.|solicitação/performanceBucket, solicitação/resultCode, solicitação/êxito, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|solicitações/taxa|Taxa de solicitações do servidor|CountPerSecond|Média|Taxa de solicitações de servidor por segundo|solicitação/performanceBucket, solicitação/resultCode, operação/sintética, nuvem/roleInstance, solicitação/êxito, nuvem/roleName|
|exceções/contagem|Exceções|Contagem|Contagem|Contagem combinada de todas as exceções não capturadas.|Cloud/roleName, nuvem/roleInstance, cliente/tipo|
|exceções/navegador|Exceções de browser|Contagem|Contagem|Contagem de exceções não capturadas lançadas no navegador.|cliente/IsServer, nuvem/roleName|
|exceções/servidor|Exceções de servidor|Contagem|Contagem|Contagem de exceções não capturadas lançadas no aplicativo de servidor.|cliente/IsServer, nuvem/roleName, nuvem/roleInstance|
|rastreamentos/contagem|Vestígios|Contagem|Contagem|Contagem de documentos de rastreamento|Trace/nível, operação/sintético, nuvem/roleName, nuvem/roleInstance|



## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServiceApiHit|Total de acertos da API de serviço|Contagem|Contagem|Número total de acertos da API de serviço|ActivityType, ActivityName|
|ServiceApiLatency|Latência geral da API de serviço|Milissegundos|Média|Latência geral de solicitações de API de serviço|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Total de resultados da API de serviço|Contagem|Contagem|Número total de resultados da API de serviço|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|Saturação do cofre geral|Percentagem|Média|Capacidade do cofre usada|Activitytable, ActivityName, transactionType|
|Disponibilidade|Disponibilidade geral do cofre|Percentagem|Média|Disponibilidade de solicitações do cofre|ActivityType, ActivityName, StatusCode, StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/clusters

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CacheUtilization|Utilização de cache|Percentagem|Média|Nível de utilização no escopo do cluster|Nenhuma|
|QueryDuration|Duração da consulta|Milissegundos|Média|Duração das consultas em segundos|ConsultaStatus|
|IngestionUtilization|Utilização de ingestão|Percentagem|Média|Taxa de Slots de ingestão usados no cluster|Nenhuma|
|KeepAlive|Keep Alive|Contagem|Média|Verificação de sanidade indica que o cluster responde às consultas|Nenhuma|
|IngestionVolumeInMB|Volume de ingestão (em MB)|Contagem|Total|Volume geral de dados ingeridos para o cluster (em MB)|Base de Dados|
|IngestionLatencyInSeconds|Latência de ingestão (em segundos)|Segundos|Média|O tempo de ingestão da origem (por exemplo, a mensagem está no EventHub) para o cluster em segundos|Nenhuma|
|EventsProcessedForEventHubs|Eventos processados (para hubs de evento/IoT)|Contagem|Total|Número de eventos processados pelo cluster ao ingerir de evento/Hub IoT|EventStatus|
|IngestionResult|Resultado da ingestão|Contagem|Contagem|Número de operações de ingestão|IngestionResultDetails|
|CPU|CPU|Percentagem|Média|Nível de utilização da CPU|Nenhuma|
|ContinuousExportNumOfRecordsExported|Exportação contínua – núm de registros exportados|Contagem|Total|Número de registros exportados, acionados para cada artefato de armazenamento gravado durante a operação de exportação|Nenhuma|
|ExportUtilization|Utilização da exportação|Percentagem|Máximo|Utilização da exportação|Nenhuma|
|ContinuousExportPendingCount|Contagem de exportação contínua pendente|Contagem|Máximo|O número de trabalhos de exportação contínuos pendentes prontos para execução|Nenhuma|
|ContinuousExportMaxLatenessMinutes|Minutos de atraso máximo de exportação contínua|Contagem|Máximo|A finalização máxima em minutos de todas as exportações contínuas pendentes e prontas para execução|Nenhuma|
|ContinuousExportResult|Resultado da exportação contínua|Contagem|Contagem|Indica se a exportação contínua foi bem-sucedida ou falhou|ContinuousExportName, resultado, banco de dados|
|StreamingIngestDuration|Duração da ingestão de streaming|Milissegundos|Média|Duração da ingestão de streaming em milissegundos|Nenhuma|
|StreamingIngestDataRate|Taxa de dados de ingestão de streaming|Contagem|Média|Taxa de dados de ingestão de streaming (MB por segundo)|Nenhuma|
|SteamingIngestRequestRate|Taxa de solicitação de ingestão de streaming|Contagem|RateRequestsPerSecond|Taxa de solicitação de ingestão de streaming (solicitações por segundo)|Nenhuma|
|StreamingIngestResults|Resultado de ingestão de streaming|Contagem|Média|Resultado de ingestão de streaming|Resultado|
|TotalNumberOfConcurrentQueries|Número total de consultas simultâneas|Contagem|Total|Número total de consultas simultâneas|Nenhuma|
|TotalNumberOfThrottledQueries|Número total de consultas limitadas|Contagem|Total|Número total de consultas limitadas|Nenhuma|
|TotalNumberOfThrottledCommands|Número total de comandos regulados|Contagem|Total|Número total de comandos regulados|CommandType|
|TotalNumberOfExtents|Número total de extensões|Contagem|Total|Número total de extensões de dados|Nenhuma|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RunsStarted|Execuções iniciadas|Contagem|Total|Número de execuções de fluxo de trabalho iniciadas.|Nenhuma|
|RunsCompleted|Execuções concluídas|Contagem|Total|Número de execuções de fluxo de trabalho concluídas.|Nenhuma|
|RunsSucceeded|Execuções com êxito|Contagem|Total|Número de execuções de fluxo de trabalho bem-sucedidas.|Nenhuma|
|RunsFailed|Execuções com falha|Contagem|Total|Número de execuções de fluxo de trabalho com falha.|Nenhuma|
|RunsCancelled|Execuções canceladas|Contagem|Total|Número de execuções de fluxo de trabalho canceladas.|Nenhuma|
|RunLatency|Latência de execução|Segundos|Média|Latência de execuções de fluxo de trabalho concluídas.|Nenhuma|
|RunSuccessLatency|Latência de execução bem-sucedida|Segundos|Média|Latência de execuções de fluxo de trabalho bem-sucedidas.|Nenhuma|
|RunThrottledEvents|Executar eventos limitados|Contagem|Total|Número de ações de fluxo de trabalho ou eventos restringidos de gatilho.|Nenhuma|
|RunStartThrottledEvents|Executar eventos restritos de início|Contagem|Total|Número de eventos restritos de início de fluxo de trabalho.|Nenhuma|
|RunFailurePercentage|Percentual de falha de execução|Percentagem|Total|Falha na porcentagem de execuções de fluxo de trabalho.|Nenhuma|
|ActionsStarted|Ações iniciadas |Contagem|Total|Número de ações de fluxo de trabalho iniciadas.|Nenhuma|
|ActionsCompleted|Ações concluídas |Contagem|Total|Número de ações de fluxo de trabalho concluídas.|Nenhuma|
|ActionsSucceeded|Ações com êxito |Contagem|Total|Número de ações de fluxo de trabalho bem-sucedidas.|Nenhuma|
|ActionsFailed|Ações com falha |Contagem|Total|Número de ações de fluxo de trabalho com falha.|Nenhuma|
|ActionsSkipped|Ações ignoradas |Contagem|Total|Número de ações de fluxo de trabalho ignoradas.|Nenhuma|
|ActionLatency|Latência da ação |Segundos|Média|Latência de ações de fluxo de trabalho concluídas.|Nenhuma|
|ActionSuccessLatency|Latência de êxito da ação |Segundos|Média|Latência de ações de fluxo de trabalho bem-sucedidas.|Nenhuma|
|ActionThrottledEvents|Eventos com restrição de ação|Contagem|Total|Número de eventos restritos da ação de fluxo de trabalho.|Nenhuma|
|TriggersStarted|Gatilhos iniciados |Contagem|Total|Número de gatilhos de fluxo de trabalho iniciados.|Nenhuma|
|TriggersCompleted|Gatilhos concluídos |Contagem|Total|Número de gatilhos de fluxo de trabalho concluídos.|Nenhuma|
|TriggersSucceeded|Gatilhos com êxito |Contagem|Total|Número de gatilhos de fluxo de trabalho bem-sucedidos.|Nenhuma|
|TriggersFailed|Gatilhos com falha |Contagem|Total|Número de gatilhos de fluxo de trabalho com falha.|Nenhuma|
|TriggersSkipped|Gatilhos ignorados|Contagem|Total|Número de gatilhos de fluxo de trabalho ignorados.|Nenhuma|
|TriggersFired|Gatilhos acionados |Contagem|Total|Número de gatilhos de fluxo de trabalho acionados.|Nenhuma|
|TriggerLatency|Latência de gatilho |Segundos|Média|Latência de gatilhos de fluxo de trabalho concluídos.|Nenhuma|
|TriggerFireLatency|Latência de disparo de gatilho |Segundos|Média|Latência de gatilhos de fluxo de trabalho acionados.|Nenhuma|
|TriggerSuccessLatency|Latência de êxito do gatilho |Segundos|Média|Latência de gatilhos de fluxo de trabalho bem-sucedidos.|Nenhuma|
|TriggerThrottledEvents|Disparar eventos restringidos|Contagem|Total|Número de eventos restringidos do gatilho de fluxo de trabalho.|Nenhuma|
|BillableActionExecutions|Execuções de ações faturáveis|Contagem|Total|Número de execuções de ação de fluxo de trabalho sendo faturadas.|Nenhuma|
|BillableTriggerExecutions|Execuções de gatilho Faturável|Contagem|Total|Número de execuções de gatilho de fluxo de trabalho sendo cobradas.|Nenhuma|
|TotalBillableExecutions|Total de execuções faturáveis|Contagem|Total|Número de execuções de fluxo de trabalho sendo cobradas.|Nenhuma|
|BillingUsageNativeOperation|Uso de cobrança para execuções de operação nativa|Contagem|Total|Número de execuções de operações nativas sendo cobradas.|Nenhuma|
|BillingUsageStandardConnector|Uso de cobrança para execuções de conector padrão|Contagem|Total|Número de execuções de conector padrão sendo cobradas.|Nenhuma|
|BillingUsageStorageConsumption|Uso de cobrança para execuções de consumo de armazenamento|Contagem|Total|Número de execuções de consumo de armazenamento sendo cobradas.|Nenhuma|
|BillingUsageNativeOperation|Uso de cobrança para execuções de operação nativa|Contagem|Total|Número de execuções de operações nativas sendo cobradas.|Nenhuma|
|BillingUsageStandardConnector|Uso de cobrança para execuções de conector padrão|Contagem|Total|Número de execuções de conector padrão sendo cobradas.|Nenhuma|
|BillingUsageStorageConsumption|Uso de cobrança para execuções de consumo de armazenamento|Contagem|Total|Número de execuções de consumo de armazenamento sendo cobradas.|Nenhuma|



## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RunsStarted|Execuções iniciadas|Contagem|Total|Número de execuções de fluxo de trabalho iniciadas.|Nenhuma|
|RunsCompleted|Execuções concluídas|Contagem|Total|Número de execuções de fluxo de trabalho concluídas.|Nenhuma|
|RunsSucceeded|Execuções com êxito|Contagem|Total|Número de execuções de fluxo de trabalho bem-sucedidas.|Nenhuma|
|RunsFailed|Execuções com falha|Contagem|Total|Número de execuções de fluxo de trabalho com falha.|Nenhuma|
|RunsCancelled|Execuções canceladas|Contagem|Total|Número de execuções de fluxo de trabalho canceladas.|Nenhuma|
|RunLatency|Latência de execução|Segundos|Média|Latência de execuções de fluxo de trabalho concluídas.|Nenhuma|
|RunSuccessLatency|Latência de execução bem-sucedida|Segundos|Média|Latência de execuções de fluxo de trabalho bem-sucedidas.|Nenhuma|
|RunThrottledEvents|Executar eventos limitados|Contagem|Total|Número de ações de fluxo de trabalho ou eventos restringidos de gatilho.|Nenhuma|
|RunStartThrottledEvents|Executar eventos restritos de início|Contagem|Total|Número de eventos restritos de início de fluxo de trabalho.|Nenhuma|
|RunFailurePercentage|Percentual de falha de execução|Percentagem|Total|Falha na porcentagem de execuções de fluxo de trabalho.|Nenhuma|
|ActionsStarted|Ações iniciadas |Contagem|Total|Número de ações de fluxo de trabalho iniciadas.|Nenhuma|
|ActionsCompleted|Ações concluídas |Contagem|Total|Número de ações de fluxo de trabalho concluídas.|Nenhuma|
|ActionsSucceeded|Ações com êxito |Contagem|Total|Número de ações de fluxo de trabalho bem-sucedidas.|Nenhuma|
|ActionsFailed|Ações com falha |Contagem|Total|Número de ações de fluxo de trabalho com falha.|Nenhuma|
|ActionsSkipped|Ações ignoradas |Contagem|Total|Número de ações de fluxo de trabalho ignoradas.|Nenhuma|
|ActionLatency|Latência da ação |Segundos|Média|Latência de ações de fluxo de trabalho concluídas.|Nenhuma|
|ActionSuccessLatency|Latência de êxito da ação |Segundos|Média|Latência de ações de fluxo de trabalho bem-sucedidas.|Nenhuma|
|ActionThrottledEvents|Eventos com restrição de ação|Contagem|Total|Número de eventos restritos da ação de fluxo de trabalho.|Nenhuma|
|TriggersStarted|Gatilhos iniciados |Contagem|Total|Número de gatilhos de fluxo de trabalho iniciados.|Nenhuma|
|TriggersCompleted|Gatilhos concluídos |Contagem|Total|Número de gatilhos de fluxo de trabalho concluídos.|Nenhuma|
|TriggersSucceeded|Gatilhos com êxito |Contagem|Total|Número de gatilhos de fluxo de trabalho bem-sucedidos.|Nenhuma|
|TriggersFailed|Gatilhos com falha |Contagem|Total|Número de gatilhos de fluxo de trabalho com falha.|Nenhuma|
|TriggersSkipped|Gatilhos ignorados|Contagem|Total|Número de gatilhos de fluxo de trabalho ignorados.|Nenhuma|
|TriggersFired|Gatilhos acionados |Contagem|Total|Número de gatilhos de fluxo de trabalho acionados.|Nenhuma|
|TriggerLatency|Latência de gatilho |Segundos|Média|Latência de gatilhos de fluxo de trabalho concluídos.|Nenhuma|
|TriggerFireLatency|Latência de disparo de gatilho |Segundos|Média|Latência de gatilhos de fluxo de trabalho acionados.|Nenhuma|
|TriggerSuccessLatency|Latência de êxito do gatilho |Segundos|Média|Latência de gatilhos de fluxo de trabalho bem-sucedidos.|Nenhuma|
|TriggerThrottledEvents|Disparar eventos restringidos|Contagem|Total|Número de eventos restringidos do gatilho de fluxo de trabalho.|Nenhuma|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Uso do processador de fluxo de trabalho para Ambiente de Serviço de Integração|Percentagem|Média|Uso do processador do fluxo de trabalho para o ambiente do serviço de integração.|Nenhuma|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Uso de memória do fluxo de trabalho para Ambiente de Serviço de Integração|Percentagem|Média|Uso de memória do fluxo de trabalho para o ambiente do serviço de integração.|Nenhuma|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Uso do processador do conector para Ambiente de Serviço de Integração|Percentagem|Média|Uso do processador do conector para o ambiente do serviço de integração.|Nenhuma|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Uso de memória do conector para Ambiente de Serviço de Integração|Percentagem|Média|Uso de memória do conector para o ambiente do serviço de integração.|Nenhuma|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Execuções concluídas|Execuções concluídas|Contagem|Total|Número de execuções concluídas com êxito para este espaço de trabalho|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Execuções iniciadas|Execuções iniciadas|Contagem|Total|Número de execuções iniciadas para este espaço de trabalho|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Corridas falhadas|Corridas falhadas|Contagem|Total|Número de execuções com falha para este espaço de trabalho|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Registro de modelo bem-sucedido|Registro de modelo bem-sucedido|Contagem|Total|Número de registros de modelo que foram bem-sucedidos neste espaço de trabalho|Cenário|
|Falha no registro do modelo|Falha no registro do modelo|Contagem|Total|Número de registros de modelo que falharam neste espaço de trabalho|Cenário, StatusCode|
|Implantação de Modelo iniciado|Implantação de Modelo iniciado|Contagem|Total|Número de implantações de modelo iniciadas neste espaço de trabalho|Cenário|
|Implantação de Modelo com êxito|Implantação de Modelo com êxito|Contagem|Total|Número de implantações de modelo que foram bem-sucedidas neste espaço de trabalho|Cenário|
|Falha na Implantação de Modelo|Falha na Implantação de Modelo|Contagem|Total|Número de implantações de modelo que falharam neste espaço de trabalho|Cenário, StatusCode|
|Total de nós|Total de nós|Contagem|Média|Número total de nós. Este total inclui alguns de Nós Ativos, Nós Ociosos, Nós Inutilizáveis, Nós Preempted, Deixando nós|Cenário, ClusterName|
|Nós ativos|Nós ativos|Contagem|Média|Número de nós active. Esses são os nós que estão executando ativamente um trabalho.|Cenário, ClusterName|
|Nós ociosos|Nós ociosos|Contagem|Média|Número de nós ociosos. Os nós ociosos são os nós que não estão executando nenhum trabalho, mas podem aceitar novos trabalhos, se disponíveis.|Cenário, ClusterName|
|Nós inutilizáveis|Nós inutilizáveis|Contagem|Média|Número de nós inutilizáveis. Os nós inutilizáveis não são funcionais devido a algum problema não resolvido. O Azure reciclará esses nós.|Cenário, ClusterName|
|Nós admitidos|Nós admitidos|Contagem|Média|Número de nós admitidos. Esses nós são os nós de baixa prioridade que são desfeitos do pool de nós disponível.|Cenário, ClusterName|
|Saindo de nós|Saindo de nós|Contagem|Média|Número de nós de saída. A saída de nós são os nós que acabaram de processar um trabalho e vão para o estado ocioso.|Cenário, ClusterName|
|Total de núcleos|Total de núcleos|Contagem|Média|Número de núcleos totais|Cenário, ClusterName|
|Núcleos ativos|Núcleos ativos|Contagem|Média|Número de núcleos ativos|Cenário, ClusterName|
|Núcleos ociosos|Núcleos ociosos|Contagem|Média|Número de núcleos ociosos|Cenário, ClusterName|
|Núcleos inutilizáveis|Núcleos inutilizáveis|Contagem|Média|Número de núcleos inutilizáveis|Cenário, ClusterName|
|Núcleos preempçãos|Núcleos preempçãos|Contagem|Média|Número de núcleos admitidos|Cenário, ClusterName|
|Deixando núcleos|Deixando núcleos|Contagem|Média|Número de núcleos de saída|Cenário, ClusterName|
|Porcentagem de utilização de cota|Porcentagem de utilização de cota|Contagem|Média|Porcentagem de cota utilizada|Cenário, ClusterName, VmFamilyName, VmPriority|
|CpuUtilization|CpuUtilization|Contagem|Média|CPU (versão prévia)|Cenário, runId, NodeId, Createdtime|
|GpuUtilization|GpuUtilization|Contagem|Média|GPU (visualização)|Cenário, runId, NodeId, Createdtime, DeviceID|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Utilização|Utilização|Contagem|Contagem|Contagem de chamadas de API|ApiCategory, ApiName, ResultType, ResponseCode|
|Disponibilidade|Disponibilidade|Percentagem|Média|Disponibilidade das APIs|ApiCategory, ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/mediaservices/streamingEndpoints

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Saída|Saída|Bytes|Total|A quantidade de dados de egresso, em bytes.|OutputFormat|
|SuccessE2ELatency|Latência de ponta a ponta com êxito|Milissegundos|Média|A latência média de solicitações bem-sucedidas em milissegundos.|OutputFormat|
|Pedidos|Pedidos|Contagem|Total|Solicitações para um ponto de extremidade de streaming.|OutputFormat, HttpStatusCode, ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/mediaservices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AssetQuota|Cota de ativos|Contagem|Média|Quantos ativos são permitidos para a conta de serviço de mídia atual|Nenhuma|
|AssetCount|Contagem de ativos|Contagem|Média|Quantos ativos já foram criados na conta de serviço de mídia atual|Nenhuma|
|AssetQuotaUsedPercentage|Porcentagem de cota de ativos usada|Percentagem|Média|Porcentagem de ativos usados na conta de serviço de mídia atual|Nenhuma|
|ContentKeyPolicyQuota|Cota de política de chave de conteúdo|Contagem|Média|Quantas políticas de chave de conteúdo são permitidas para a conta de serviço de mídia atual|Nenhuma|
|ContentKeyPolicyCount|Contagem de política de chave de conteúdo|Contagem|Média|Quantas políticas de chave de conteúdo já foram criadas na conta de serviço de mídia atual|Nenhuma|
|ContentKeyPolicyQuotaUsedPercentage|Porcentagem de cota usada da política de chave de conteúdo|Percentagem|Média|Porcentagem usada da política de chave de conteúdo na conta de serviço de mídia atual|Nenhuma|
|StreamingPolicyQuota|Cota de política de streaming|Contagem|Média|Quantas políticas de streaming são permitidas para a conta de serviço de mídia atual|Nenhuma|
|StreamingPolicyCount|Contagem de políticas de streaming|Contagem|Média|Quantas políticas de streaming já foram criadas na conta de serviço de mídia atual|Nenhuma|
|StreamingPolicyQuotaUsedPercentage|Porcentagem de cota usada da política de streaming|Percentagem|Média|Percentual usado pela política de streaming na conta de serviço de mídia atual|Nenhuma|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/volumes

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AverageReadLatency|Latência média de leitura|Milissegundos|Média|Média de latência de leitura em milissegundos por operação|Nenhuma|
|AverageWriteLatency|Latência média de gravação|Milissegundos|Média|Latência média de gravação em milissegundos por operação|Nenhuma|
|VolumeLogicalSize|Tamanho lógico do volume|Bytes|Média|Tamanho lógico do volume (bytes usados)|Nenhuma|
|VolumeSnapshotSize|Tamanho do instantâneo de volume|Bytes|Média|Tamanho de todos os instantâneos no volume|Nenhuma|
|ReadIops|IOPS de leitura|CountPerSecond|Média|Operações de leitura/saída por segundo|Nenhuma|
|WriteIops|IOPS de gravação|CountPerSecond|Média|Operações de gravação/saída por segundo|Nenhuma|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Pool de volumes alocado usado|Bytes|Média|Tamanho usado para o pool alocado|Nenhuma|
|VolumePoolTotalLogicalSize|Tamanho lógico total do pool de volumes|Bytes|Média|Soma do tamanho lógico de todos os volumes pertencentes ao pool|Nenhuma|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BytesSentRate|Bytes Enviados|Bytes|Total|Número de bytes enviados pela interface de rede|Nenhuma|
|BytesReceivedRate|Bytes Recebidos|Bytes|Total|Número de bytes recebidos pela interface de rede|Nenhuma|
|PacketsSentRate|Pacotes enviados|Contagem|Total|Número de pacotes enviados pela interface de rede|Nenhuma|
|PacketsReceivedRate|Pacotes recebidos|Contagem|Total|Número de pacotes recebidos pela interface de rede|Nenhuma|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|VipAvailability|Disponibilidade do caminho de dados|Contagem|Média|Média de disponibilidade de caminho de dados de Load Balancer por duração de tempo|FrontendIPAddress, FrontendPort|
|DipAvailability|Status da investigação de integridade|Contagem|Média|Média Load Balancer status de investigação de integridade por duração de tempo|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Contagem de bytes|Contagem|Total|Número total de bytes transmitidos no período de tempo|FrontendIPAddress, FrontendPort, direção|
|PacketCount|Contagem de pacotes|Contagem|Total|Número total de pacotes transmitidos no período de tempo|FrontendIPAddress, FrontendPort, direção|
|SYNCount|Contagem de SYN|Contagem|Total|Número total de pacotes SYN transmitidos no período de tempo|FrontendIPAddress, FrontendPort, direção|
|SnatConnectionCount|Contagem de conexões SNAT|Contagem|Total|Número total de novas conexões SNAT criadas dentro do período de tempo|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Portas SNAT alocadas (visualização)|Contagem|Total|Número total de portas SNAT alocadas no período de tempo|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|
|UsedSnatPorts|Portas SNAT usadas (visualização)|Contagem|Total|Número total de portas SNAT usadas no período de tempo|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueryVolume|Volume de consulta|Contagem|Total|Número de consultas servidas para uma zona DNS|Nenhuma|
|RecordSetCount|Contagem de conjuntos de registros|Contagem|Máximo|Número de conjuntos de registros em uma zona DNS|Nenhuma|
|RecordSetCapacityUtilization|Utilização da capacidade do conjunto de registros|Percentagem|Máximo|Porcentagem da capacidade do conjunto de registros utilizada por uma zona DNS|Nenhuma|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PacketsInDDoS|Pacotes de entrada DDoS|CountPerSecond|Máximo|Pacotes de entrada DDoS|Nenhuma|
|PacketsDroppedDDoS|Pacotes de entrada caíram DDoS|CountPerSecond|Máximo|Pacotes de entrada caíram DDoS|Nenhuma|
|PacketsForwardedDDoS|Pacotes de entrada encaminhadas DDoS|CountPerSecond|Máximo|Pacotes de entrada encaminhadas DDoS|Nenhuma|
|TCPPacketsInDDoS|DDoS de pacotes TCP de entrada|CountPerSecond|Máximo|DDoS de pacotes TCP de entrada|Nenhuma|
|TCPPacketsDroppedDDoS|DDoS de pacotes TCP de entrada removidos|CountPerSecond|Máximo|DDoS de pacotes TCP de entrada removidos|Nenhuma|
|TCPPacketsForwardedDDoS|DDoS de pacotes TCP de entrada encaminhados|CountPerSecond|Máximo|DDoS de pacotes TCP de entrada encaminhados|Nenhuma|
|UDPPacketsInDDoS|DDoS de pacotes UDP de entrada|CountPerSecond|Máximo|DDoS de pacotes UDP de entrada|Nenhuma|
|UDPPacketsDroppedDDoS|DDoS de pacotes UDP de entrada removidos|CountPerSecond|Máximo|DDoS de pacotes UDP de entrada removidos|Nenhuma|
|UDPPacketsForwardedDDoS|DDoS de pacotes UDP de entrada encaminhados|CountPerSecond|Máximo|DDoS de pacotes UDP de entrada encaminhados|Nenhuma|
|BytesInDDoS|Bytes de entrada|BytesPerSecond|Máximo|Bytes de entrada|Nenhuma|
|BytesDroppedDDoS|Bytes de entrada caíram DDoS|BytesPerSecond|Máximo|Bytes de entrada caíram DDoS|Nenhuma|
|BytesForwardedDDoS|Bytes de entrada encaminhadas DDoS|BytesPerSecond|Máximo|Bytes de entrada encaminhadas DDoS|Nenhuma|
|TCPBytesInDDoS|TCP de entrada bytes DDoS|BytesPerSecond|Máximo|TCP de entrada bytes DDoS|Nenhuma|
|TCPBytesDroppedDDoS|DDoS de bytes de TCP de entrada eliminados|BytesPerSecond|Máximo|DDoS de bytes de TCP de entrada eliminados|Nenhuma|
|TCPBytesForwardedDDoS|DDoS de bytes TCP de entrada encaminhados|BytesPerSecond|Máximo|DDoS de bytes TCP de entrada encaminhados|Nenhuma|
|UDPBytesInDDoS|DDoS de bytes de UDP de entrada|BytesPerSecond|Máximo|DDoS de bytes de UDP de entrada|Nenhuma|
|UDPBytesDroppedDDoS|DDoS de bytes UDP de entrada eliminados|BytesPerSecond|Máximo|DDoS de bytes UDP de entrada eliminados|Nenhuma|
|UDPBytesForwardedDDoS|DDoS de bytes de UDP de entrada encaminhados|BytesPerSecond|Máximo|DDoS de bytes de UDP de entrada encaminhados|Nenhuma|
|IfUnderDDoSAttack|Sob ataque de DDoS ou não|Contagem|Máximo|Sob ataque de DDoS ou não|Nenhuma|
|DDoSTriggerTCPPackets|Pacotes TCP de entrada para disparar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes TCP de entrada para disparar a mitigação de DDoS|Nenhuma|
|DDoSTriggerUDPPackets|Pacotes UDP de entrada para disparar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes UDP de entrada para disparar a mitigação de DDoS|Nenhuma|
|DDoSTriggerSYNPackets|Pacotes SYN de entrada para desencadear mitigação do DDoS|CountPerSecond|Máximo|Pacotes SYN de entrada para desencadear mitigação do DDoS|Nenhuma|
|VipAvailability|Disponibilidade do caminho de dados|Contagem|Média|Média de disponibilidade de endereço IP por duração de tempo|Porta|
|ByteCount|Contagem de bytes|Contagem|Total|Número total de bytes transmitidos no período de tempo|Porta, direção|
|PacketCount|Contagem de pacotes|Contagem|Total|Número total de pacotes transmitidos no período de tempo|Porta, direção|
|SynCount|Contagem de SYN|Contagem|Total|Número total de pacotes SYN transmitidos no período de tempo|Porta, direção|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Tempo de ida e volta para pings em uma VM|Milissegundos|Média|Tempo de ida e volta para pings enviados para uma VM de destino|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Pings com falha em uma VM|Percentagem|Média|Porcentagem do número de pings com falha para o total de pings enviados de uma VM de destino|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ApplicationRuleHit|Contagem de impacto de regras de aplicativo|Contagem|Total|Número de vezes que as regras de aplicativo foram atingidas|Status, motivo, protocolo|
|NetworkRuleHit|Contagem de impacto das regras de rede|Contagem|Total|Número de vezes que as regras de rede foram atingidas|Status, motivo, protocolo|
|FirewallHealth|Estado de integridade do firewall|Percentagem|Média|Estado de integridade do firewall|Status, motivo|
|Dataprocessod|Dados processados|Bytes|Total|Quantidade total de dados processados pelo firewall|Nenhuma|
|SNATPortUtilization|Utilização da porta SNAT|Percentagem|Média|Utilização da porta SNAT|Nenhuma|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Débito|Débito|BytesPerSecond|Média|Número de bytes por segundo atendidos pelo gateway de aplicativo|Nenhuma|
|UnhealthyHostCount|Contagem de hosts não íntegros|Contagem|Média|Número de hosts de back-end não íntegros|BackendSettingsPool|
|HealthyHostCount|Contagem de hosts íntegros|Contagem|Média|Número de hosts de back-end íntegros|BackendSettingsPool|
|Total De Pedidos|Total de Pedidos|Contagem|Total|Contagem de solicitações bem-sucedidas que o gateway de aplicativo serviu|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Solicitações por minuto por host íntegro|Contagem|Média|Contagem média de solicitações por minuto por host de back-end íntegro em um pool|BackendSettingsPool|
|FailedRequests|Pedidos com Falhas|Contagem|Total|Contagem de solicitações com falha que o gateway de aplicativo serviu|BackendSettingsPool|
|ResponseStatus|Status da resposta|Contagem|Total|Status de resposta http retornado pelo gateway de aplicativo|HttpStatusGroup|
|CurrentConnections|Conexões atuais|Contagem|Total|Contagem de conexões atuais estabelecidas com o gateway de aplicativo|Nenhuma|
|CpuUtilization|Utilização da CPU|Percentagem|Média|Utilização atual da CPU do gateway de aplicativo|Nenhuma|
|CapacityUnits|Unidades de capacidade atuais|Contagem|Média|Unidades de capacidade consumidas|Nenhuma|
|ComputeUnits|Unidades de computação atuais|Contagem|Média|Unidades de computação consumidas|Nenhuma|
|BackendResponseStatus|Status da resposta de back-end|Contagem|Total|O número de códigos de resposta HTTP gerados pelos membros de back-end. Isso não inclui nenhum código de resposta gerado pelo gateway de aplicativo.|BackendServer, Httpsettings, BackendHttpSetting, HttpStatusGroup|
|TlsProtocol|Protocolo TLS do cliente|Contagem|Total|O número de solicitações TLS e não TLS iniciadas pelo cliente que estabeleceram a conexão com o gateway de aplicativo. Para exibir a distribuição de protocolo TLS, filtre pelo protocolo TLS de dimensão.|Ouvinte, TlsProtocol|
|BytesSent|Bytes Enviados|Bytes|Total|O número total de bytes enviados pelo gateway de aplicativo para os clientes|Ouvinte|
|BytesReceived|Bytes Recebidos|Bytes|Total|O número total de bytes recebidos pelo gateway de aplicativo dos clientes|Ouvinte|
|ClientRtt|RTT do cliente|Milissegundos|Média|Tempo médio de ida e volta entre clientes e o gateway de aplicativo. Essa métrica indica quanto tempo leva para estabelecer conexões e retornar confirmações|Ouvinte|
|ApplicationGatewayTotalTime|Tempo total do gateway de aplicativo|Milissegundos|Média|Tempo médio que leva para que uma solicitação seja processada e sua resposta seja enviada. Isso é calculado como a média do intervalo desde o momento em que o gateway de aplicativo recebe o primeiro byte de uma solicitação HTTP até o momento em que a operação de envio de resposta é concluída. É importante observar que isso geralmente inclui o tempo de processamento do gateway de aplicativo, o tempo que os pacotes de solicitação e resposta estão viajando pela rede e o tempo que o servidor back-end levou para responder.|Ouvinte|
|BackendConnectTime|Tempo de conexão de back-end|Milissegundos|Média|Tempo gasto estabelecendo uma conexão com um servidor de back-end|Listener, BackendServer, Httpsettings, BackendHttpSetting|
|BackendFirstByteResponseTime|Tempo de resposta do primeiro byte do back-end|Milissegundos|Média|Intervalo de tempo entre o início do estabelecimento de uma conexão com o servidor back-end e o recebimento do primeiro byte do cabeçalho de resposta, aproximar tempo de processamento do servidor back-end|Listener, BackendServer, Httpsettings, BackendHttpSetting|
|BackendLastByteResponseTime|Tempo de resposta do último byte do back-end|Milissegundos|Média|Intervalo de tempo entre o início do estabelecimento de uma conexão com o servidor de back-end e o recebimento do último byte do corpo da resposta|Listener, BackendServer, Httpsettings, BackendHttpSetting|
|MatchedCount|Distribuição da regra total do firewall do aplicativo Web|Contagem|Total|Distribuição da regra total do firewall do aplicativo Web para o tráfego de entrada|Ruler, RuleId|
|BlockedCount|Distribuição de regra de solicitações bloqueadas do firewall do aplicativo Web|Contagem|Total|Distribuição de regra de solicitações bloqueadas do firewall do aplicativo Web|Ruler, RuleId|
|BlockedReqCount|Contagem de solicitações bloqueadas do firewall do aplicativo Web|Contagem|Total|Contagem de solicitações bloqueadas do firewall do aplicativo Web|Nenhuma|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AverageBandwidth|Largura de banda S2S do gateway|BytesPerSecond|Média|Largura de banda de site a site média de um gateway em bytes por segundo|Nenhuma|
|P2SBandwidth|Largura de banda P2S do gateway|BytesPerSecond|Média|Largura de banda de ponto a site média de um gateway em bytes por segundo|Nenhuma|
|P2SConnectionCount|Contagem de conexões P2S|Contagem|Máximo|Contagem de conexões ponto a site de um gateway|Protocolo|
|TunnelAverageBandwidth|Largura de banda do túnel|BytesPerSecond|Média|Largura de banda média de um túnel em bytes por segundo|ConnectionName, RemoteIP|
|TunnelEgressBytes|Bytes de saída do túnel|Bytes|Total|Bytes de saída de um túnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Bytes de entrada de túnel|Bytes|Total|Bytes de entrada de um túnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Pacotes de saída de túnel|Contagem|Total|Contagem de pacotes de saída de um túnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Pacotes de entrada de túnel|Contagem|Total|Contagem de pacotes de entrada de um túnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Descarte de pacotes incompatíveis TS de saída de túnel|Contagem|Total|Contagem de descarte de pacotes de saída da incompatibilidade de seletor de tráfego de um túnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Descarte de pacotes incompatíveis TS de entrada de túnel|Contagem|Total|Contagem de eliminação de pacotes de entrada da incompatibilidade de seletor de tráfego de um túnel|ConnectionName, RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Contagem|Média|Nível de luz Rx em dBm|Link, Lane|
|TxLightLevel|TxLightLevel|Contagem|Média|Nível de luz TX em dBm|Link, Lane|
|Adminstate|Adminstate|Contagem|Média|Estado do administrador da porta|Ligação|
|LineProtocol|LineProtocol|Contagem|Média|Status do protocolo de linha da porta|Ligação|
|PortBitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits de entrada do Azure por segundo|Ligação|
|PortBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits de saída do Azure por segundo|Ligação|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits de entrada do Azure por segundo|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits de saída do Azure por segundo|PeeringType|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|CountPerSecond|Média|Bits de entrada do Azure por segundo|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|CountPerSecond|Média|Bits de saída do Azure por segundo|PeeredCircuitSKey|
|BgpAvailability|Disponibilidade de BGP|Percentagem|Média|Disponibilidade de BGP de MSEE para todos os pares.|PeeringType, par|
|ArpAvailability|Disponibilidade do ARP|Percentagem|Média|Disponibilidade ARP de MSEE para todos os pares.|PeeringType, par|
|QosDropBitsInPerSecond|DroppedInBitsPerSecond|CountPerSecond|Média|Bits de entrada de dados eliminados por segundo|Nenhuma|
|QosDropBitsOutPerSecond|DroppedOutBitsPerSecond|CountPerSecond|Média|Bits de saída de dados eliminados por segundo|Nenhuma|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits de entrada do Azure por segundo|Nenhuma|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits de saída do Azure por segundo|Nenhuma|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits de entrada do Azure por segundo|Nenhuma|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits de saída do Azure por segundo|Nenhuma|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/expressRouteGateways

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits de entrada do Azure por segundo|Nome de ligação|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits de saída do Azure por segundo|Nome de ligação|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QpsByEndpoint|Consultas por ponto de extremidade retornadas|Contagem|Total|Número de vezes que um ponto de extremidade do Gerenciador de tráfego foi retornado no período de tempo fornecido|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Status do ponto de extremidade por ponto de extremidade|Contagem|Máximo|1 se o status de investigação de um ponto de extremidade for "habilitado", caso contrário, 0.|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ProbesFailedPercent|% sondas falhadas|Percentagem|Média|% das sondas de monitorização da conectividade falharam|Nenhuma|
|AverageRoundtripMs|Média de tempo de ida e volta (MS)|Milissegundos|Média|Tempo de ida e volta médio da rede (MS) para investigações de monitoramento de conectividade enviadas entre a origem e o destino|Nenhuma|
|ChecksFailedPercent|Verificações por cento com falha (versão prévia)|Percentagem|Média|% dos controlos de controlo da conectividade falharam|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, Destinationname, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|
|RoundTripTimeMs|Tempo de ida e volta (MS) (visualização)|Milissegundos|Média|Tempo de ida e volta em milissegundos para as verificações de monitoramento de conectividade|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, Destinationname, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RequestCount|Número de Pedidos|Contagem|Total|O número de solicitações de cliente atendidas pelo proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|Solicitações|Tamanho da solicitação|Bytes|Total|O número de bytes enviados como solicitações de clientes para o proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|Respostas|Tamanho da resposta|Bytes|Total|O número de bytes enviados como respostas do proxy HTTP/S para clientes|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BillableResponseSize|Tamanho de resposta Faturável|Bytes|Total|O número de bytes faturáveis (mínimo de 2 KB por solicitação) enviados como respostas do proxy HTTP/S para os clientes.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Contagem de solicitações de back-end|Contagem|Total|O número de solicitações enviadas do proxy HTTP/S para back-ends|HttpStatus, HttpStatusGroup, back-end|
|BackendRequestLatency|Latência de solicitação de back-end|Milissegundos|Média|O tempo calculado a partir de quando a solicitação foi enviada pelo proxy HTTP/S para o back-end até que o proxy HTTP/S receba o último byte de resposta do back-end|Backend|
|TotalLatency|Latência total|Milissegundos|Média|O tempo calculado a partir de quando a solicitação do cliente foi recebida pelo proxy HTTP/S até que o cliente tenha reconhecido o último byte de resposta do proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Percentual de integridade de back-end|Percentagem|Média|A porcentagem de investigações de integridade bem-sucedidas do proxy HTTP/S para back-ends|Back-end, Httpsettings|
|WebApplicationFirewallRequestCount|Contagem de solicitações de firewall do aplicativo Web|Contagem|Total|O número de solicitações de cliente processadas pelo firewall do aplicativo Web|PolicyName, RuleName, ação|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|registration.all|Operações de registro|Contagem|Total|A contagem de todas as operações de registro bem-sucedidas (cria consultas e exclusões de atualizações). |Nenhuma|
|registration.create|Operações de criação de registro|Contagem|Total|A contagem de todas as criações de registro bem-sucedidas.|Nenhuma|
|registration.update|Operações de atualização de registro|Contagem|Total|A contagem de todas as atualizações de registro bem-sucedidas.|Nenhuma|
|registration.get|Operações de leitura de registro|Contagem|Total|A contagem de todas as consultas de registro bem-sucedidas.|Nenhuma|
|registration.delete|Operações de exclusão de registro|Contagem|Total|A contagem de todas as exclusões de registro bem-sucedidas.|Nenhuma|
|recebidos|Mensagens de entrada|Contagem|Total|A contagem de todas as chamadas de API de envio bem-sucedidas. |Nenhuma|
|incoming.scheduled|Notificações por push agendadas enviadas|Contagem|Total|Notificações por push agendadas canceladas|Nenhuma|
|incoming.scheduled.cancel|Notificações por push agendadas canceladas|Contagem|Total|Notificações por push agendadas canceladas|Nenhuma|
|agendado. pendente|Notificações agendadas pendentes|Contagem|Total|Notificações agendadas pendentes|Nenhuma|
|instalação. todos|Operações de gerenciamento de instalação|Contagem|Total|Operações de gerenciamento de instalação|Nenhuma|
|installation.get|Obter operações de instalação|Contagem|Total|Obter operações de instalação|Nenhuma|
|installation.upsert|Criar ou atualizar operações de instalação|Contagem|Total|Criar ou atualizar operações de instalação|Nenhuma|
|installation.patch|Operações de instalação de patch|Contagem|Total|Operações de instalação de patch|Nenhuma|
|installation.delete|Excluir operações de instalação|Contagem|Total|Excluir operações de instalação|Nenhuma|
|outgoing.allpns.success|Notificações bem-sucedidas|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Nenhuma|
|outgoing.allpns.invalidpayload|Erros de carga|Contagem|Total|A contagem de Pushes que falharam porque o PNS retornou um erro de carga inadequada.|Nenhuma|
|outgoing.allpns.pnserror|Erros do sistema de notificação externa|Contagem|Total|A contagem de Pushes que falharam porque houve um problema de comunicação com o PNS (exclui problemas de autenticação).|Nenhuma|
|outgoing.allpns.channelerror|Erros de canal|Contagem|Total|A contagem de Pushes que falharam porque o canal era inválido não associado ao aplicativo correto limitado ou expirou.|Nenhuma|
|outgoing.allpns.badorexpiredchannel|Erros de canal insatisfatórios ou expirados|Contagem|Total|A contagem de Pushes que falharam porque o canal/token/RegistrationId no registro expirou ou é inválido.|Nenhuma|
|outgoing.wns.success|Notificações com êxito do WNS|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Nenhuma|
|outgoing.wns.invalidcredentials|Erros de autorização do WNS (credenciais inválidas)|Contagem|Total|A contagem de Pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas. (O Windows Live não reconhece as credenciais).|Nenhuma|
|outgoing.wns.badchannel|Erro de canal insatisfatório do WNS|Contagem|Total|A contagem de Pushes que falharam porque o ChannelURI no registro não foi reconhecido (status do WNS: 404 não encontrado).|Nenhuma|
|outgoing.wns.expiredchannel|Erro de canal expirado do WNS|Contagem|Total|A contagem de Pushes que falharam porque o ChannelURI está expirado (status do WNS: 410 out).|Nenhuma|
|outgoing.wns.throttled|Notificações limitadas do WNS|Contagem|Total|A contagem de Pushes que falharam porque o WNS está limitando este aplicativo (status do WNS: 406 não aceitável).|Nenhuma|
|outgoing.wns.tokenproviderunreachable|Erros de autorização do WNS (inacessível)|Contagem|Total|O Windows Live não está acessível.|Nenhuma|
|outgoing.wns.invalidtoken|Erros de autorização do WNS (token inválido)|Contagem|Total|O token fornecido ao WNS não é válido (status do WNS: 401 não autorizado).|Nenhuma|
|outgoing.wns.wrongtoken|Erros de autorização do WNS (token incorreto)|Contagem|Total|O token fornecido ao WNS é válido, mas para outro aplicativo (status do WNS: 403 Proibido). Isso pode acontecer se o ChannelURI no registro estiver associado a outro aplicativo. Verifique se o aplicativo cliente está associado ao mesmo aplicativo cujas credenciais estão no Hub de notificação.|Nenhuma|
|outgoing.wns.invalidnotificationformat|Formato de notificação inválido do WNS|Contagem|Total|O formato da notificação é inválido (status do WNS: 400). Observe que o WNS não rejeita todas as cargas inválidas.|Nenhuma|
|outgoing.wns.invalidnotificationsize|Erro de tamanho de notificação inválido do WNS|Contagem|Total|A carga de notificação é muito grande (status do WNS: 413).|Nenhuma|
|outgoing.wns.channelthrottled|Canal do WNS limitado|Contagem|Total|A notificação foi descartada porque o ChannelURI no registro está limitado (cabeçalho de resposta WNS: X-WNS-NotificationStatus: channelThrottled).|Nenhuma|
|outgoing.wns.channeldisconnected|Canal do WNS desconectado|Contagem|Total|A notificação foi descartada porque o ChannelURI no registro está limitado (cabeçalho de resposta do WNS: X-WNS-DeviceConnectionStatus: desconectado).|Nenhuma|
|outgoing.wns.dropped|Notificações descartadas do WNS|Contagem|Total|A notificação foi descartada porque o ChannelURI no registro está limitado (X-WNS-NotificationStatus: descartado, mas não X-WNS-DeviceConnectionStatus: desconectado).|Nenhuma|
|outgoing.wns.pnserror|Erros de WNS|Contagem|Total|Notificação não entregue devido a erros de comunicação com o WNS.|Nenhuma|
|outgoing.wns.authenticationerror|Erros de autenticação WNS|Contagem|Total|Notificação não entregue devido a erros de comunicação com credenciais inválidas do Windows Live ou token incorreto.|Nenhuma|
|outgoing.apns.success|Notificações bem-sucedidas do APNS|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Nenhuma|
|outgoing.apns.invalidcredentials|Erros de autorização de APNS|Contagem|Total|A contagem de Pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhuma|
|outgoing.apns.badchannel|Erro de canal insatisfatório de APNS|Contagem|Total|A contagem de Pushes que falharam porque o token é inválido (código de status APNS: 8).|Nenhuma|
|outgoing.apns.expiredchannel|Erro de canal expirado APNS|Contagem|Total|A contagem de tokens que foram invalidados pelo canal de comentários do APNS.|Nenhuma|
|outgoing.apns.invalidnotificationsize|Erro de tamanho de notificação inválido do APNS|Contagem|Total|A contagem de Pushes que falharam porque o conteúdo era muito grande (código de status do APNS: 7).|Nenhuma|
|outgoing.apns.pnserror|Erros de APNS|Contagem|Total|A contagem de Pushes que falharam devido a erros de comunicação com o APNS.|Nenhuma|
|outgoing.gcm.success|Notificações de êxito do GCM|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Nenhuma|
|outgoing.gcm.invalidcredentials|Erros de autorização do GCM (credenciais inválidas)|Contagem|Total|A contagem de Pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhuma|
|outgoing.gcm.badchannel|Erro de canal insatisfatório do GCM|Contagem|Total|A contagem de Pushes que falharam porque o RegistrationId no registro não foi reconhecido (resultado do GCM: registro inválido).|Nenhuma|
|outgoing.gcm.expiredchannel|Erro de canal expirado do GCM|Contagem|Total|A contagem de Pushes que falharam porque o RegistrationId no registro expirou (resultado do GCM: não registrado).|Nenhuma|
|outgoing.gcm.throttled|Notificações limitadas do GCM|Contagem|Total|A contagem de Pushes que falharam porque o GCM restringiu este aplicativo (código de status do GCM: 501-599 ou resultado: não disponível).|Nenhuma|
|outgoing.gcm.invalidnotificationformat|Formato de notificação inválido do GCM|Contagem|Total|A contagem de Pushes que falharam porque a carga não foi formatada corretamente (resultado do GCM: InvalidDataKey ou InvalidTtl).|Nenhuma|
|outgoing.gcm.invalidnotificationsize|Erro de tamanho de notificação inválido do GCM|Contagem|Total|A contagem de Pushes que falharam porque o conteúdo era muito grande (resultado do GCM: MessageTooBig).|Nenhuma|
|outgoing.gcm.wrongchannel|Erro de canal incorreto do GCM|Contagem|Total|A contagem de Pushes que falharam porque o RegistrationId no registro não está associado ao aplicativo atual (resultado do GCM: InvalidPackageName).|Nenhuma|
|outgoing.gcm.pnserror|Erros do GCM|Contagem|Total|A contagem de Pushes que falharam devido a erros de comunicação com o GCM.|Nenhuma|
|outgoing.gcm.authenticationerror|Erros de autenticação do GCM|Contagem|Total|A contagem de Pushes que falharam porque o PNS não aceitou as credenciais fornecidas as credenciais estão bloqueadas ou a SenderId não está configurada corretamente no aplicativo (resultado do GCM: MismatchedSenderId).|Nenhuma|
|outgoing.mpns.success|Notificações bem-sucedidas do MPNS|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Nenhuma|
|outgoing.mpns.invalidcredentials|Credenciais inválidas do MPNS|Contagem|Total|A contagem de Pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhuma|
|outgoing.mpns.badchannel|Erro de canal insatisfatório do MPNS|Contagem|Total|A contagem de Pushes que falharam porque o ChannelURI no registro não foi reconhecido (status do MPNS: 404 não encontrado).|Nenhuma|
|outgoing.mpns.throttled|Notificações limitadas do MPNS|Contagem|Total|A contagem de Pushes que falharam porque o MPNS está limitando este aplicativo (WNS MPNS: 406 não aceitável).|Nenhuma|
|outgoing.mpns.invalidnotificationformat|Formato de notificação inválido do MPNS|Contagem|Total|A contagem de Pushes que falharam porque a carga da notificação era muito grande.|Nenhuma|
|outgoing.mpns.channeldisconnected|Canal do MPNS desconectado|Contagem|Total|A contagem de Pushes que falharam porque o ChannelURI no registro foi desconectado (status do MPNS: 412 não encontrado).|Nenhuma|
|outgoing.mpns.dropped|Notificações ignoradas do MPNS|Contagem|Total|A contagem de Pushes que foram descartados pelo MPNS (cabeçalho de resposta MPNS: X-NotificationStatus: QueueFull ou suprimido).|Nenhuma|
|outgoing.mpns.pnserror|Erros do MPNS|Contagem|Total|A contagem de Pushes que falharam devido a erros de comunicação com o MPNS.|Nenhuma|
|outgoing.mpns.authenticationerror|Erros de autenticação do MPNS|Contagem|Total|A contagem de Pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhuma|
|notificationhub.pushes|Todas as notificações de saída|Contagem|Total|Todas as notificações de saída do hub de notificação|Nenhuma|
|incoming.all.requests|Todas as solicitações de entrada|Contagem|Total|Total de solicitações de entrada para um hub de notificação|Nenhuma|
|incoming.all.failedrequests|Todas as solicitações com falha de entrada|Contagem|Total|Total de solicitações de entrada com falha para um hub de notificação|Nenhuma|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Average_% Inodes Grátis|% Inodes livres|Contagem|Média|Average_% Inodes Grátis|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% espaço livre|% De espaço livre|Contagem|Média|Average_% espaço livre|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Inodes usados|% Inodos Usados|Contagem|Média|Average_% Inodes usados|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Espaço Usado|% De espaço utilizado|Contagem|Média|Average_% Espaço Usado|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Disk Ler Bytes/seg|Bytes Lidos de Disco/seg|Contagem|Média|Average_Disk Ler Bytes/seg|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|leituras Average_Disk/seg|Leituras de disco/seg|Contagem|Média|leituras Average_Disk/seg|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|transferências Average_Disk/seg|As transferências de disco/seg|Contagem|Média|transferências Average_Disk/seg|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Disk Escrever Bytes/seg|Bytes Escritos em Disco/seg|Contagem|Média|Average_Disk Escrever Bytes/seg|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Disk Escreve/seg|Escritas de disco/seg|Contagem|Média|Average_Disk Escreve/seg|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Free Megabytes|Megabytes livres|Contagem|Média|Average_Free Megabytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Logical Bytes/seg de disco|Bytes de disco lógico/seg|Contagem|Média|Average_Logical Bytes/seg de disco|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|memória disponível Average_%|% Memória Disponível|Contagem|Média|memória disponível Average_%|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% espaço disponível de swap|% Espaço de Troca Disponível %|Contagem|Média|Average_% espaço disponível de swap|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% memória usada|% Memória Usada|Contagem|Média|Average_% memória usada|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% espaço de troca usado|% Espaço de troca usado %|Contagem|Média|Average_% espaço de troca usado|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|memória Average_Available MBytes|MBytes de memória disponíveis|Contagem|Média|memória Average_Available MBytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Available Troca de MBytes|Troca de MBytes disponíveis|Contagem|Média|Average_Available Troca de MBytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Page Leituras/seg|Leituras da página/seg|Contagem|Média|Average_Page Leituras/seg|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Page Escreve/seg|Página Escritas/seg|Contagem|Média|Average_Page Escreve/seg|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Pages/seg|Páginas/seg|Contagem|Média|Average_Pages/seg|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Used MBytes Swap Space|Espaço de permuta usado em Mbytes|Contagem|Média|Average_Used MBytes Swap Space|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Used Memória MBytes|Mbytes de memória usados|Contagem|Média|Average_Used Memória MBytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total Bytes Transmitidos|Total de Bytes transmitidos|Contagem|Média|Average_Total Bytes Transmitidos|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total Bytes Recebidos|Total de Bytes recebidos|Contagem|Média|Average_Total Bytes Recebidos|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total Bytes|Total de bytes|Contagem|Média|Average_Total Bytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|pacotes de Average_Total transmitidos|Total de pacotes transmitidos|Contagem|Média|pacotes de Average_Total transmitidos|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|pacotes Average_Total recebidos|Total de pacotes recebidos|Contagem|Média|pacotes Average_Total recebidos|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total Erros Rx|Total de erros de RX|Contagem|Média|Average_Total Erros Rx|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|erros Average_Total Tx|Total de erros TX|Contagem|Média|erros Average_Total Tx|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|colisões Average_Total|Total de colisões|Contagem|Média|colisões Average_Total|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Disco seg/leitura|Média de disco s/leitura|Contagem|Média|Average_Avg. Disco seg/leitura|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Disco seg/transferência|Média de disco s/transferência|Contagem|Média|Average_Avg. Disco seg/transferência|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Disco seg/escritas|Média de disco s/gravação|Contagem|Média|Average_Avg. Disco seg/escritas|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Physical Bytes/seg de disco|Bytes de disco físico/s|Contagem|Média|Average_Physical Bytes/seg de disco|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Pct Tempo Privilegiado|Percentual de tempo privilegiado|Contagem|Média|Average_Pct Tempo Privilegiado|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|tempo de utilizador Average_Pct|Tempo de usuário do PCT|Contagem|Média|tempo de utilizador Average_Pct|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Used Memory kBytes|KBytes de memória usada|Contagem|Média|Average_Used Memory kBytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|memória partilhada Average_Virtual|Memória compartilhada virtual|Contagem|Média|memória partilhada Average_Virtual|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de tempo dPC|% Tempo dPC|Contagem|Média|Average_% de tempo dPC|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% tempo de inatividade|% Tempo de inatividade|Contagem|Média|Average_% tempo de inatividade|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de tempo de interrupção|% tempo de interrupção|Contagem|Média|Average_% de tempo de interrupção|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% IO Tempo de espera|% Io Tempo de Espera|Contagem|Média|Average_% IO Tempo de espera|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% bom tempo|% Bom Tempo|Contagem|Média|Average_% bom tempo|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% tempo privilegiado|% Tempo Privilegiado|Contagem|Média|Average_% tempo privilegiado|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% tempo do processador|% Tempo do processador|Contagem|Média|Average_% tempo do processador|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% tempo de utilizador|% Tempo de utilizador|Contagem|Média|Average_% tempo de utilizador|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|memória física Average_Free|Memória física livre|Contagem|Média|memória física Average_Free|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|espaço Average_Free em arquivos de paging|Espaço livre em arquivos de paginação|Contagem|Média|espaço Average_Free em arquivos de paging|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Free Memória Virtual|Memória virtual livre|Contagem|Média|Average_Free Memória Virtual|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Processes|Processos|Contagem|Média|Average_Processes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Size armazenado em arquivos de paging|Tamanho armazenado em arquivos de paginação|Contagem|Média|Average_Size armazenado em arquivos de paging|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Uptime|Tempo de atividade|Contagem|Média|Average_Uptime|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Users|Utilizadores|Contagem|Média|Average_Users|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Disco seg/leitura|Média de disco s/leitura|Contagem|Média|Average_Avg. Disco seg/leitura|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Disco seg/escritas|Média de disco s/gravação|Contagem|Média|Average_Avg. Disco seg/escritas|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|comprimento da fila do disco de Average_Current|Comprimento da fila de disco atual|Contagem|Média|comprimento da fila do disco de Average_Current|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|leituras Average_Disk/seg|Leituras de disco/seg|Contagem|Média|leituras Average_Disk/seg|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|transferências Average_Disk/seg|As transferências de disco/seg|Contagem|Média|transferências Average_Disk/seg|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Disk Escreve/seg|Escritas de disco/seg|Contagem|Média|Average_Disk Escreve/seg|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Free Megabytes|Megabytes livres|Contagem|Média|Average_Free Megabytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% espaço livre|% De espaço livre|Contagem|Média|Average_% espaço livre|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Available MBytes|MBytes disponíveis|Contagem|Média|Average_Available MBytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% bytes comprometidos em uso|% Bytes Comprometidos em Uso|Contagem|Média|Average_% bytes comprometidos em uso|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Bytes recebido/seg|Bytes recebidos/seg|Contagem|Média|Average_Bytes recebido/seg|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Bytes Enviado/seg|Bytes enviados/seg|Contagem|Média|Average_Bytes Enviado/seg|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Bytes Total/seg|Total de bytes/s|Contagem|Média|Average_Bytes Total/seg|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% tempo do processador|% Tempo do processador|Contagem|Média|Average_% tempo do processador|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|comprimento da fila Average_Processor|Comprimento da fila do processador|Contagem|Média|comprimento da fila Average_Processor|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Batimento cardíaco|Batimento cardíaco|Contagem|Total|Batimento cardíaco|Computador, OSType, versão, SourceComputerId|
|Atualizar|Atualizar|Contagem|Média|Atualizar|Computador, produto, classificação, UpdateState, opcional, aprovado|
|Evento|Evento|Contagem|Média|Evento|Origem, EventLog, computador, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft. emparelhamento/peeringServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PrefixLatency|Latência de prefixo|Milissegundos|Média|Latência de prefixo mediana|Prefixoname|

## <a name="microsoftpeeringpeerings"></a>Microsoft. emparelhamento/emparelhamentos

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SessionAvailabilityV4|Disponibilidade de sessão v4|Percentagem|Média|Disponibilidade da sessão v4|ConnectionId|
|SessionAvailabilityV6|Disponibilidade de sessão V6|Percentagem|Média|Disponibilidade da sessão V6|ConnectionId|
|IngressTrafficRate|Taxa de tráfego de entrada|BitsPerSecond|Média|Taxa de tráfego de entrada em bits por segundo|ConnectionId|
|EgressTrafficRate|Taxa de tráfego de saída|BitsPerSecond|Média|Taxa de tráfego de saída em bits por segundo|ConnectionId|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueryDuration|Duração da consulta|Milissegundos|Média|Duração da consulta DAX no último intervalo|Nenhuma dimensão|
|QueryPoolJobQueueLength|Threads: comprimento da fila de trabalhos do pool de consultas|Contagem|Média|Número de trabalhos na fila do pool de threads de consulta.|Nenhuma dimensão|
|qpu_high_utilization_metric|Alta Utilização qpu|Contagem|Total|QPU alta utilização no último minuto, 1 para alta utilização de QPU, caso contrário 0|Nenhuma dimensão|
|memory_metric|Memória|Bytes|Média|A memória. Intervalo de 0-3 GB para a1, 0-5 GB para a2, 0-10 GB para a3, 0-25 GB para A4, 0-50 GB para a5 e 0-100 GB para a6|Nenhuma dimensão|
|memory_thrashing_metric|Goleada de memória|Percentagem|Média|Média de ultrapaginação de memória.|Nenhuma dimensão|



## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/namespaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Contagem|Total|ListenerConnections com êxito para Microsoft. Relay.|EntityName, OperationResult|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Contagem|Total|ClientError em ListenerConnections para Microsoft. Relay.|EntityName, OperationResult|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Contagem|Total|ServerError em ListenerConnections para Microsoft. Relay.|EntityName, OperationResult|
|SenderConnections-Success|SenderConnections-Success|Contagem|Total|SenderConnections com êxito para Microsoft. Relay.|EntityName, OperationResult|
|SenderConnections-ClientError|SenderConnections-ClientError|Contagem|Total|ClientError em SenderConnections para Microsoft. Relay.|EntityName, OperationResult|
|SenderConnections-ServerError|SenderConnections-ServerError|Contagem|Total|ServerError em SenderConnections para Microsoft. Relay.|EntityName, OperationResult|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Contagem|Total|Total de ListenerConnections para Microsoft. Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Contagem|Total|Total de solicitações SenderConnections para Microsoft. Relay.|EntityName|
|ActiveConnections|ActiveConnections|Contagem|Total|Total de ActiveConnections para Microsoft. Relay.|EntityName|
|ActiveListeners|ActiveListeners|Contagem|Total|Total de ActiveListeners para Microsoft. Relay.|EntityName|
|BytesTransferred|BytesTransferred|Contagem|Total|Total de BytesTransferred para Microsoft. Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Contagem|Total|Total de ListenerDisconnects para Microsoft. Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Contagem|Total|Total de SenderDisconnects para Microsoft. Relay.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SearchLatency|Latência de pesquisa|Segundos|Média|Latência de pesquisa média para o serviço de pesquisa|Nenhuma|
|SearchQueriesPerSecond|Pesquisar consultas por segundo|CountPerSecond|Média|Pesquisar consultas por segundo para o serviço de pesquisa|Nenhuma|
|ThrottledSearchQueriesPercentage|Percentual de consultas de pesquisa limitadas|Percentagem|Média|Porcentagem de consultas de pesquisa que foram limitadas para o serviço de pesquisa|Nenhuma|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfulRequests|Solicitações bem-sucedidas|Contagem|Total|Total de solicitações bem-sucedidas para um namespace|EntityName, OperationResult|
|ServerErrors|Erros do servidor.|Contagem|Total|Erros de servidor para Microsoft. ServiceBus.|EntityName, OperationResult|
|UserErrors|Erros do usuário.|Contagem|Total|Erros de usuário para Microsoft. ServiceBus.|EntityName, OperationResult|
|ThrottledRequests|Solicitações limitadas.|Contagem|Total|Solicitações limitadas para Microsoft. ServiceBus.|EntityName, OperationResult|
|IncomingRequests|Solicitações de entrada|Contagem|Total|Solicitações de entrada para Microsoft. ServiceBus.|EntityName|
|IncomingMessages|Mensagens de entrada|Contagem|Total|Mensagens de entrada para Microsoft. ServiceBus.|EntityName|
|OutgoingMessages|Mensagens de saída|Contagem|Total|Mensagens de saída para Microsoft. ServiceBus.|EntityName|
|ActiveConnections|ActiveConnections|Contagem|Total|Total de conexões ativas para Microsoft. ServiceBus.|Nenhuma|
|ConnectionsOpened|Conexões abertas.|Contagem|Média|Conexões abertas para Microsoft. ServiceBus.|EntityName|
|ConnectionsClosed|Conexões fechadas.|Contagem|Média|Conexões fechadas para Microsoft. ServiceBus.|EntityName|
|Tamanho|Tamanho|Bytes|Média|Tamanho de uma fila/tópico em bytes.|EntityName|
|Mensagens|Contagem de mensagens em uma fila/tópico.|Contagem|Média|Contagem de mensagens em uma fila/tópico.|EntityName|
|ActiveMessages|Contagem de mensagens ativas em uma fila/tópico.|Contagem|Média|Contagem de mensagens ativas em uma fila/tópico.|EntityName|
|DeadletteredMessages|Contagem de mensagens mortas em uma fila/tópico.|Contagem|Média|Contagem de mensagens mortas em uma fila/tópico.|EntityName|
|ScheduledMessages|Contagem de mensagens agendadas em uma fila/tópico.|Contagem|Média|Contagem de mensagens agendadas em uma fila/tópico.|EntityName|
|NamespaceCpuUsage|CPU|Percentagem|Máximo|Métrica de uso de CPU do namespace Premium do barramento de serviço.|Réplica|
|NamespaceMemoryUsage|Utilização de Memória|Percentagem|Máximo|Métrica de uso de memória do namespace Premium do barramento de serviço.|Réplica|
|CPUXNS|CPU (preterido)|Percentagem|Máximo|Métrica de uso de CPU do namespace Premium do barramento de serviço. Essa métrica é preterida. Em vez disso, use a métrica de CPU (NamespaceCpuUsage).|Réplica|
|WSXNS|Uso de memória (preterido)|Percentagem|Máximo|Métrica de uso de memória do namespace Premium do barramento de serviço. Essa métrica foi preterida. Use a métrica de uso de memória (NamespaceMemoryUsage) em vez disso.|Réplica|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Contagem|Média|CPU alocada para este contêiner em núcleos de mili|ApplicationName, ServiceName, CodePackageName, ServiceId|
|AllocatedMemory|AllocatedMemory|Bytes|Média|Memória alocada para este contêiner em MB|ApplicationName, ServiceName, CodePackageName, ServiceId|
|ActualCpu|ActualCpu|Contagem|Média|Uso real da CPU em núcleos de mili|ApplicationName, ServiceName, CodePackageName, ServiceId|
|ActualMemory|ActualMemory|Bytes|Média|Uso real da memória em MB|ApplicationName, ServiceName, CodePackageName, ServiceId|
|CpuUtilization|CpuUtilization|Percentagem|Média|Utilização da CPU para este contêiner como porcentagem de AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceId|
|MemoryUtilization|MemoryUtilization|Percentagem|Média|Utilização da CPU para este contêiner como porcentagem de AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceId|
|ApplicationStatus|ApplicationStatus|Contagem|Média|Status do aplicativo de malha Service Fabric|ApplicationName, status|
|ServiceStatus|ServiceStatus|Contagem|Média|Status de integridade de um serviço no aplicativo de malha Service Fabric|ApplicationName, status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Contagem|Média|Status de integridade de uma réplica de serviço no aplicativo de malha Service Fabric|ApplicationName, status, ServiceName, nome de réplica|
|ContainerStatus|ContainerStatus|Contagem|Média|Status do contêiner no aplicativo de malha Service Fabric|ApplicationName, ServiceName, CodePackageName, ServiceId, status|
|RestartCount|RestartCount|Contagem|Média|Reiniciar a contagem de um contêiner no aplicativo de malha Service Fabric|ApplicationName, status, ServiceName, nome de réplica, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ConnectionCount|Contagem de conexões|Contagem|Máximo|A quantidade de conexão do usuário.|Ponto Final|
|MessageCount|Contagem de mensagens|Contagem|Total|A quantidade total de mensagens.|Nenhuma|
|InboundTraffic|Tráfego de Entrada|Bytes|Total|O tráfego de entrada do serviço|Nenhuma|
|OutboundTraffic|Tráfego de Saída|Bytes|Total|O tráfego de saída do serviço|Nenhuma|
|UserErrors|Erros do usuário|Percentagem|Máximo|A porcentagem de erros do usuário|Nenhuma|
|SystemErrors|Erros do sistema|Percentagem|Máximo|A porcentagem de erros do sistema|Nenhuma|





## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Nenhuma|
|physical_data_read_percent|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|Nenhuma|
|log_write_percent|Percentual de e/s de log|Percentagem|Média|Percentual de e/s de log. Não aplicável a data warehouses.|Nenhuma|
|dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|Porcentagem de DTU. Aplica-se a bancos de dados baseados em DTU.|Nenhuma|
|armazenamento|Espaço de dados usado|Bytes|Máximo|Espaço de dados usado. Não aplicável a data warehouses.|Nenhuma|
|connection_successful|Conexões com êxito|Contagem|Total|Conexões com êxito|Nenhuma|
|connection_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Nenhuma|
|blocked_by_firewall|Bloqueado pelo firewall|Contagem|Total|Bloqueado pelo firewall|Nenhuma|
|bloqueado|Deadlocks|Contagem|Total|Deadlocks. Não aplicável a data warehouses.|Nenhuma|
|storage_percent|Porcentagem de espaço de dados usada|Percentagem|Máximo|Porcentagem de espaço de dados usada. Não aplicável a data warehouses ou a bancos de dados de hiperescala.|Nenhuma|
|xtp_storage_percent|Porcentagem de armazenamento OLTP na memória|Percentagem|Média|Porcentagem de armazenamento OLTP na memória. Não aplicável a data warehouses.|Nenhuma|
|workers_percent|Porcentagem de trabalhadores|Percentagem|Média|Porcentagem de trabalhadores. Não aplicável a data warehouses.|Nenhuma|
|sessions_percent|Porcentagem de sessões|Percentagem|Média|Porcentagem de sessões. Não aplicável a data warehouses.|Nenhuma|
|dtu_limit|Limite de DTU|Contagem|Média|Limite de DTU. Aplica-se a bancos de dados baseados em DTU.|Nenhuma|
|dtu_used|DTU usado|Contagem|Média|DTU usado. Aplica-se a bancos de dados baseados em DTU.|Nenhuma|
|cpu_limit|Limite de CPU|Contagem|Média|Limite de CPU. Aplica-se a bancos de dados baseados em vCore.|Nenhuma|
|cpu_used|CPU usada|Contagem|Média|CPU usada. Aplica-se a bancos de dados baseados em vCore.|Nenhuma|
|dwu_limit|Limite de DWU|Contagem|Máximo|Limite de DWU. Aplica-se somente a data warehouses.|Nenhuma|
|dwu_consumption_percent|Porcentagem de DWU|Percentagem|Máximo|Porcentagem de DWU. Aplica-se somente a data warehouses.|Nenhuma|
|dwu_used|DWU usado|Contagem|Máximo|DWU usado. Aplica-se somente a data warehouses.|Nenhuma|
|cache_hit_percent|Porcentagem de acesso ao cache|Percentagem|Máximo|Porcentagem de acesso ao cache. Aplica-se somente a data warehouses.|Nenhuma|
|cache_used_percent|Percentual de cache usado|Percentagem|Máximo|Percentual de cache usado. Aplica-se somente a data warehouses.|Nenhuma|
|sqlserver_process_core_percent|Porcentagem de núcleo do processo de SQL Server|Percentagem|Máximo|Percentagem de utilização do CPU para o processo Do Servidor SQL, medida pelo sistema operativo. Atualmente disponível apenas para bases de dados sem servidores.|Nenhuma|
|sqlserver_process_memory_percent|Porcentagem de memória de processo SQL Server|Percentagem|Máximo|Percentagem de utilização da memória para o processo Do Servidor SQL, medida pelo sistema operativo. Atualmente disponível apenas para bases de dados sem servidores.|Nenhuma|
|tempdb_data_size|Tamanho do arquivo de dados tempdb em kilobytes|Contagem|Máximo|Tamanho do arquivo de dados tempdb em kilobytes. Não aplicável a data warehouses.|Nenhuma|
|tempdb_log_size|Tamanho do arquivo de log de tempdb em kilobytes|Contagem|Máximo|Tamanho do arquivo de log de tempdb em kilobytes. Não aplicável a data warehouses.|Nenhuma|
|tempdb_log_used_percent|Log de porcentagem de tempdb usado|Percentagem|Máximo|Log de porcentagem de tempdb usado. Não aplicável a data warehouses.|Nenhuma|
|local_tempdb_usage_percent|Porcentagem de tempdb local|Percentagem|Média|Porcentagem de tempdb local. Aplica-se somente a data warehouses.|Nenhuma|
|app_cpu_billed|CPU do aplicativo cobrada|Contagem|Total|CPU do aplicativo cobrada. Aplica-se a bancos de dados sem servidor.|Nenhuma|
|app_cpu_percent|Porcentagem de CPU do aplicativo|Percentagem|Média|Porcentagem de CPU do aplicativo. Aplica-se a bancos de dados sem servidor.|Nenhuma|
|app_memory_percent|Porcentagem de memória do aplicativo|Percentagem|Média|Porcentagem de memória do aplicativo. Aplica-se a bancos de dados sem servidor.|Nenhuma|
|allocated_data_storage|Espaço de dados alocado|Bytes|Média|Armazenamento de dados alocado. Não aplicável a data warehouses.|Nenhuma|
|memory_usage_percent|Porcentagem de memória|Percentagem|Máximo|Porcentagem de memória. Aplica-se somente a data warehouses.|Nenhuma|
|full_backup_size_bytes|Tamanho de armazenamento de backup completo|Bytes|Máximo|Tamanho de armazenamento de backup completo cumulativo. Aplica-se a bancos de dados baseados em vCore. Não aplicável a bancos de dados de hiperescala.|Nenhuma|
|diff_backup_size_bytes|Tamanho de armazenamento diferencial de backup|Bytes|Máximo|Tamanho do armazenamento de backup diferencial cumulativo. Aplica-se a bancos de dados baseados em vCore. Não aplicável a bancos de dados de hiperescala.|Nenhuma|
|log_backup_size_bytes|Tamanho do armazenamento de backup de log|Bytes|Máximo|Tamanho de armazenamento de backup de log cumulativo. Aplica-se a bancos de dados baseados em vCore. Não aplicável a bancos de dados de hiperescala.|Nenhuma|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Nenhuma|
|database_cpu_percent|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|DatabaseResourceId|
|physical_data_read_percent|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|Nenhuma|
|database_physical_data_read_percent|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|DatabaseResourceId|
|log_write_percent|Percentual de e/s de log|Percentagem|Média|Percentual de e/s de log|Nenhuma|
|database_log_write_percent|Percentual de e/s de log|Percentagem|Média|Percentual de e/s de log|DatabaseResourceId|
|dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|Porcentagem de DTU. Aplica-se a pools elásticos baseados em DTU.|Nenhuma|
|database_dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|Percentagem de DTU|DatabaseResourceId|
|storage_percent|Porcentagem de espaço de dados usada|Percentagem|Média|Porcentagem de espaço de dados usada|Nenhuma|
|workers_percent|Porcentagem de trabalhadores|Percentagem|Média|Porcentagem de trabalhadores|Nenhuma|
|database_workers_percent|Porcentagem de trabalhadores|Percentagem|Média|Porcentagem de trabalhadores|DatabaseResourceId|
|sessions_percent|Porcentagem de sessões|Percentagem|Média|Porcentagem de sessões|Nenhuma|
|database_sessions_percent|Porcentagem de sessões|Percentagem|Média|Porcentagem de sessões|DatabaseResourceId|
|eDTU_limit|limite de eDTU|Contagem|Média|limite de eDTU. Aplica-se a pools elásticos baseados em DTU.|Nenhuma|
|storage_limit|Tamanho máximo de dados|Bytes|Média|Tamanho máximo de dados|Nenhuma|
|eDTU_used|eDTU usado|Contagem|Média|eDTU usado. Aplica-se a pools elásticos baseados em DTU.|Nenhuma|
|database_eDTU_used|eDTU usado|Contagem|Média|eDTU usado|DatabaseResourceId|
|storage_used|Espaço de dados usado|Bytes|Média|Espaço de dados usado|Nenhuma|
|database_storage_used|Espaço de dados usado|Bytes|Média|Espaço de dados usado|DatabaseResourceId|
|xtp_storage_percent|Porcentagem de armazenamento OLTP na memória|Percentagem|Média|Porcentagem de armazenamento OLTP na memória|Nenhuma|
|cpu_limit|Limite de CPU|Contagem|Média|Limite de CPU. Aplica-se a pools elásticos baseados em vCore.|Nenhuma|
|database_cpu_limit|Limite de CPU|Contagem|Média|Limite de CPU|DatabaseResourceId|
|cpu_used|CPU usada|Contagem|Média|CPU usada. Aplica-se a pools elásticos baseados em vCore.|Nenhuma|
|database_cpu_used|CPU usada|Contagem|Média|CPU usada|DatabaseResourceId|
|sqlserver_process_core_percent|Porcentagem de núcleo do processo de SQL Server|Percentagem|Máximo|Uso da CPU como uma porcentagem do processo do BD SQL. Aplica-se a pools elásticos.|Nenhuma|
|sqlserver_process_memory_percent|Porcentagem de memória de processo SQL Server|Percentagem|Máximo|Uso de memória como uma porcentagem do processo do BD SQL. Aplica-se a pools elásticos.|Nenhuma|
|tempdb_data_size|Tamanho do arquivo de dados tempdb em kilobytes|Contagem|Máximo|Tamanho do arquivo de dados tempdb em kilobytes|Nenhuma|
|tempdb_log_size|Tamanho do arquivo de log de tempdb em kilobytes|Contagem|Máximo|Tamanho do arquivo de log de tempdb em kilobytes|Nenhuma|
|tempdb_log_used_percent|Log de porcentagem de tempdb usado|Percentagem|Máximo|Log de porcentagem de tempdb usado|Nenhuma|
|allocated_data_storage|Espaço de dados alocado|Bytes|Média|Espaço de dados alocado|Nenhuma|
|database_allocated_data_storage|Espaço de dados alocado|Bytes|Média|Espaço de dados alocado|DatabaseResourceId|
|allocated_data_storage_percent|Porcentagem alocada de espaço de dados|Percentagem|Máximo|Porcentagem alocada de espaço de dados|Nenhuma|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|Percentagem de DTU|ElasticPoolResourceId|
|database_dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|Percentagem de DTU|DatabaseResourceId,ElasticPoolResourceId|
|storage_used|Espaço de dados usado|Bytes|Média|Espaço de dados usado|ElasticPoolResourceId|
|database_storage_used|Espaço de dados usado|Bytes|Média|Espaço de dados usado|DatabaseResourceId,ElasticPoolResourceId|
|dtu_used|DTU usado|Contagem|Média|DTU usado|DatabaseResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|virtual_core_count|Contagem de núcleos virtuais|Contagem|Média|Contagem de núcleos virtuais|Nenhuma|
|avg_cpu_percent|Percentual médio de CPU|Percentagem|Média|Percentual médio de CPU|Nenhuma|
|reserved_storage_mb|Espaço de armazenamento reservado|Contagem|Média|Espaço de armazenamento reservado|Nenhuma|
|storage_space_used_mb|Espaço de armazenamento usado|Contagem|Média|Espaço de armazenamento usado|Nenhuma|
|io_requests|Contagem de solicitações de e/s|Contagem|Média|Contagem de solicitações de e/s|Nenhuma|
|io_bytes_read|Bytes de e/s lidos|Bytes|Média|Bytes de e/s lidos|Nenhuma|
|io_bytes_written|Bytes de e/s gravados|Bytes|Média|Bytes de e/s gravados|Nenhuma|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|UsedCapacity|Capacidade utilizada|Bytes|Média|Capacidade da conta utilizada|Nenhuma|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BlobCapacity|Capacidade do Blob|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Blob a conta de armazenamento, em bytes.|BlobType, camada|
|BlobCount|Contagem de Blobs|Contagem|Média|O número de blobs no serviço Blob da conta de armazenamento.|BlobType, camada|
|ContainerCount|Contagem do Contentor de Blobs|Contagem|Média|O número de contentores no serviço Blob da conta de armazenamento.|Nenhuma|
|IndexCapacity|Capacidade indexada|Bytes|Média|A quantidade de armazenamento usada pelo índice ADLS Gen2 (hierárquico) em bytes.|Nenhuma|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TableCapacity|Capacidade de Tabelas|Bytes|Média|A quantidade de armazenamento utilizado pelo serviço Tabela da conta de armazenamento, em bytes.|Nenhuma|
|TableCount|Contagem de Tabelas|Contagem|Média|O número de tabelas no serviço Tabela da conta de armazenamento.|Nenhuma|
|TableEntityCount|Contagem de Entidade de Tabelas|Contagem|Média|O número de entidades de tabelas no serviço Tabela da conta de armazenamento.|Nenhuma|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Capacidade de|Capacidade do arquivo|Bytes|Média|A quantidade de armazenamento usada pelo serviço de arquivo da conta de armazenamento em bytes.|FileShare|
|FileCount|Contagem de arquivos|Contagem|Média|O número de arquivos no serviço de arquivo da conta de armazenamento.|FileShare|
|FileShareCount|Contagem de compartilhamento de arquivos|Contagem|Média|O número de compartilhamentos de arquivos no serviço de arquivo da conta de armazenamento.|Nenhuma|
|FileShareSnapshotCount|Contagem de instantâneos de compartilhamento de arquivos|Contagem|Média|O número de instantâneos presentes no serviço compartilhamento de arquivos da conta de armazenamento.|FileShare|
|FileShareSnapshotSize|Tamanho do instantâneo de compartilhamento de arquivos|Bytes|Média|A quantidade de armazenamento usada pelos instantâneos no serviço de arquivo da conta de armazenamento, em bytes.|FileShare|
|FileShareQuota|Tamanho da cota de compartilhamento de arquivos|Bytes|Média|O limite superior da quantidade de armazenamento que pode ser usada pelo serviço de arquivos do Azure em bytes.|FileShare|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, GeoType, ApiName, autenticação, FileShare|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação, FileShare|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação, FileShare|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação, FileShare|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação, FileShare|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|Geotipo, ApiName, autenticação, FileShare|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueueCapacity|Capacidade de Fila|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Fila a conta de armazenamento, em bytes.|Nenhuma|
|QueueCount|Contagem de Filas|Contagem|Média|O número de filas no serviço Fila da conta de armazenamento.|Nenhuma|
|QueueMessageCount|Contagem de Mensagens em Fila|Contagem|Média|O número aproximado de mensagens em fila no serviço Fila da conta de armazenamento.|Nenhuma|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|Geotipo, ApiName, autenticação|





## <a name="microsoftstoragecachecaches"></a>Microsoft. StorageCache/caches

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ClientIOPS|IOPS de cliente total|Contagem|Média|A taxa de operações de arquivos do cliente processadas pelo cache.|Nenhuma|
|ClientLatency|Latência média do cliente|Milissegundos|Média|Latência média de operações de arquivo do cliente para o cache de armazenamento.|Nenhuma|
|ClientReadIOPS|IOPS de leitura do cliente|CountPerSecond|Média|Operações de leitura de cliente por segundo.|Nenhuma|
|ClientReadThroughput|Média de taxa de transferência de leitura do cache|BytesPerSecond|Média|Taxa de transferência de dados de leitura de cliente.|Nenhuma|
|ClientWriteIOPS|IOPS de gravação de cliente|CountPerSecond|Média|Operações de gravação de cliente por segundo.|Nenhuma|
|ClientWriteThroughput|Taxa de transferência média de gravação no cache|BytesPerSecond|Média|Taxa de transferência de dados de gravação de cliente.|Nenhuma|
|ClientMetadataReadIOPS|IOPS de leitura de metadados do cliente|CountPerSecond|Média|A taxa de operações de arquivo do cliente enviadas ao cache, excluindo leituras de dados, que não modificam o estado persistente.|Nenhuma|
|ClientMetadataWriteIOPS|IOPS de gravação de metadados do cliente|CountPerSecond|Média|A taxa de operações de arquivo do cliente enviadas ao cache, excluindo gravações de dados, que modificam o estado persistente.|Nenhuma|
|ClientLockIOPS|IOPS de bloqueio de cliente|CountPerSecond|Média|Operações de bloqueio de arquivo do cliente por segundo.|Nenhuma|
|StorageTargetHealth|Integridade do destino de armazenamento|Contagem|Média|Resultados boolianos de teste de conectividade entre o cache e os destinos de armazenamento.|Nenhuma|
|Tempo de atividade|Tempo de atividade|Contagem|Média|Resultados boolianos de teste de conectividade entre o cache e o sistema de monitoramento.|Nenhuma|
|StorageTargetIOPS|Total de IOPS de StorageTarget|Contagem|Média|A taxa de todas as operações de arquivo que o cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|IOPS de gravação StorageTarget|Contagem|Média|A taxa das operações de gravação de arquivo que o cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetAsyncWriteThroughput|Taxa de transferência de gravação assíncrona StorageTarget|BytesPerSecond|Média|A taxa em que o cache grava dados de forma assíncrona em um determinado StorageTarget. Essas são gravações oportunistas que não fazem com que os clientes bloqueiem.|StorageTarget|
|StorageTargetSyncWriteThroughput|Taxa de transferência de gravação síncrona StorageTarget|BytesPerSecond|Média|A taxa em que o cache grava dados de forma síncrona em um determinado StorageTarget. Essas são gravações que fazem com que os clientes bloqueiem.|StorageTarget|
|StorageTargetTotalWriteThroughput|Taxa de transferência total de gravação de StorageTarget|BytesPerSecond|Média|A taxa total em que o cache grava dados em um determinado StorageTarget.|StorageTarget|
|StorageTargetLatency|Latência de StorageTarget|Milissegundos|Média|A latência média de ida e volta de todas as operações de arquivo que o cache envia para um partricular StorageTarget.|StorageTarget|
|StorageTargetMetadataReadIOPS|IOPS de leitura de metadados StorageTarget|CountPerSecond|Média|A taxa de operações de arquivo que não modificam o estado persistente e excluindo a operação de leitura, que o cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetMetadataWriteIOPS|IOPS de gravação de metadados StorageTarget|CountPerSecond|Média|A taxa de operações de arquivo que modificam o estado persistente e excluindo a operação de gravação, que o cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetReadIOPS|IOPS de leitura StorageTarget|CountPerSecond|Média|A taxa de operações de leitura de arquivo que o cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetReadAheadThroughput|Taxa de transferência de leitura antecipada de StorageTarget|BytesPerSecond|Média|A taxa em que o cache lê de forma oportunista os dados do StorageTarget.|StorageTarget|
|StorageTargetFillThroughput|Taxa de transferência de preenchimento do StorageTarget|BytesPerSecond|Média|A taxa em que o cache lê os dados do StorageTarget para lidar com um erro de cache.|StorageTarget|
|StorageTargetTotalReadThroughput|Taxa de transferência de leitura total de StorageTarget|BytesPerSecond|Média|A taxa total em que o cache lê dados de um determinado StorageTarget.|StorageTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Resultado da sessão de sincronização|Contagem|Média|Métrica que registra um valor de 1 cada vez que o ponto de extremidade do servidor conclui com êxito uma sessão de sincronização com o ponto de extremidade de nuvem|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Arquivos sincronizados|Contagem|Total|Contagem de arquivos sincronizados|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Arquivos não sincronizando|Contagem|Total|Falha na sincronização da contagem de arquivos|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncBatchTransferredFileBytes|Bytes sincronizados|Bytes|Total|Tamanho total do arquivo transferido para sessões de sincronização|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncServerHeartbeat|Status online do servidor|Contagem|Máximo|Métrica que registra um valor de 1 cada vez que o servidor resigtered registra com êxito uma pulsação com o ponto de extremidade de nuvem|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Recall de camadas de nuvem|Bytes|Total|Tamanho total dos dados recuperados pelo servidor|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Tamanho de recall em camadas de nuvem|Bytes|Total|Tamanho dos dados recuperados|SyncGroupName, servername|
|StorageSyncRecallThroughputBytesPerSecond|Taxa de transferência de recall em camadas de nuvem|BytesPerSecond|Média|Tamanho da taxa de transferência de recall de dados|SyncGroupName, servername|
|StorageSyncRecalledNetworkBytesByApplication|Tamanho de recall de camadas de nuvem por aplicativo|Bytes|Total|Tamanho dos dados recuperados pelo aplicativo|SyncGroupName, servername, ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. storagesync/storageSyncServices/syncGroups

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|Arquivos sincronizados|Contagem|Total|Contagem de arquivos sincronizados|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Arquivos não sincronizando|Contagem|Total|Falha na sincronização da contagem de arquivos|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupBatchTransferredFileBytes|Bytes sincronizados|Bytes|Total|Tamanho total do arquivo transferido para sessões de sincronização|SyncGroupName,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|Arquivos sincronizados|Contagem|Total|Contagem de arquivos sincronizados|ServerEndpointName,SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Arquivos não sincronizando|Contagem|Total|Falha na sincronização da contagem de arquivos|ServerEndpointName,SyncDirection|
|ServerEndpointBatchTransferredFileBytes|Bytes sincronizados|Bytes|Total|Tamanho total do arquivo transferido para sessões de sincronização|ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. storagesync/storageSyncServices/registeredServers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServerHeartbeat|Status online do servidor|Contagem|Máximo|Métrica que registra um valor de 1 cada vez que o servidor resigtered registra com êxito uma pulsação com o ponto de extremidade de nuvem|ServerResourceId, servername|
|ServerRecallIOTotalSizeBytes|Recall de camadas de nuvem|Bytes|Total|Tamanho total dos dados recuperados pelo servidor|ServerResourceId, servername|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ResourceUtilization|% de utilização SU|Percentagem|Máximo|% de utilização SU|LogicalName, PartitionID|
|InputEvents|Eventos de Entrada|Contagem|Total|Eventos de Entrada|LogicalName, PartitionID|
|InputEventBytes|Bytes de Evento de Entrada|Bytes|Total|Bytes de Evento de Entrada|LogicalName, PartitionID|
|LateInputEvents|Eventos de Entrada atrasados|Contagem|Total|Eventos de Entrada atrasados|LogicalName, PartitionID|
|OutputEvents|Eventos de Saída|Contagem|Total|Eventos de Saída|LogicalName, PartitionID|
|ConversionErrors|Erros de Conversão de Dados|Contagem|Total|Erros de Conversão de Dados|LogicalName, PartitionID|
|Erros|Erros de Tempo de Execução|Contagem|Total|Erros de Tempo de Execução|LogicalName, PartitionID|
|DroppedOrAdjustedEvents|Eventos fora de ordem|Contagem|Total|Eventos fora de ordem|LogicalName, PartitionID|
|AMLCalloutRequests|Pedidos de Função|Contagem|Total|Pedidos de Função|LogicalName, PartitionID|
|AMLCalloutFailedRequests|Falha no pedido de funções|Contagem|Total|Falha no pedido de funções|LogicalName, PartitionID|
|AMLCalloutInputEvents|Eventos de Função|Contagem|Total|Eventos de Função|LogicalName, PartitionID|
|DeserializationError|Erros de Desserialização de entrada|Contagem|Total|Erros de Desserialização de entrada|LogicalName, PartitionID|
|EarlyInputEvents|Eventos de Entrada Antigos|Contagem|Total|Eventos de Entrada Antigos|LogicalName, PartitionID|
|OutputWatermarkDelaySeconds|Atraso de Marca de Água|Segundos|Máximo|Atraso de Marca de Água|LogicalName, PartitionID|
|InputEventsSourcesBacklogged|Eventos de Entrada Pendentes|Contagem|Máximo|Eventos de Entrada Pendentes|LogicalName, PartitionID|
|InputEventsSourcesPerSecond|Origens de Entrada Recebidas|Contagem|Total|Origens de Entrada Recebidas|LogicalName, PartitionID|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|IngressReceivedMessages|Mensagens de entrada recebidas|Contagem|Total|Contagem de mensagens lidas de todas as fontes de eventos do hub de eventos ou do Hub IoT|Nenhuma|
|IngressReceivedInvalidMessages|A entrada recebeu mensagens inválidas|Contagem|Total|Contagem de mensagens inválidas lidas de todas as fontes de eventos do hub de eventos ou do Hub IoT|Nenhuma|
|IngressReceivedBytes|Bytes de entrada recebidos|Bytes|Total|Contagem de bytes lidos de todas as origens do evento|Nenhuma|
|IngressStoredBytes|Bytes de entrada armazenados|Bytes|Total|Tamanho total de eventos processados e disponíveis com êxito para consulta|Nenhuma|
|IngressStoredEvents|Eventos de entrada armazenados|Contagem|Total|Contagem de eventos mesclados processados e disponíveis com êxito para consulta|Nenhuma|
|IngressReceivedMessagesTimeLag|Atraso de tempo de mensagens recebidas de entrada|Segundos|Máximo|Diferença entre a hora em que a mensagem é enfileirada na origem do evento e a hora em que ela é processada na entrada|Nenhuma|
|IngressReceivedMessagesCountLag|Atraso na contagem de mensagens recebidas de entrada|Contagem|Média|Diferença entre o número de sequência da última mensagem enfileirada na partição de origem do evento e o número de sequência de mensagens sendo processadas na entrada|Nenhuma|
|WarmStorageMaxProperties|Propriedades máximas de armazenamento quente|Contagem|Máximo|Número máximo de propriedades usadas permitidas pelo ambiente para SKU S1/S2 e número máximo de propriedades permitidas pelo armazenamento quente para o SKU PAYG|Nenhuma|
|WarmStorageUsedProperties|Propriedades usadas de armazenamento quente |Contagem|Máximo|Número de propriedades usadas pelo ambiente para SKU S1/S2 e número de propriedades usadas pelo armazenamento quente para SKU PAYG|Nenhuma|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|IngressReceivedMessages|Mensagens de entrada recebidas|Contagem|Total|Contagem de mensagens lidas da origem do evento|Nenhuma|
|IngressReceivedInvalidMessages|A entrada recebeu mensagens inválidas|Contagem|Total|Contagem de mensagens inválidas lidas da origem do evento|Nenhuma|
|IngressReceivedBytes|Bytes de entrada recebidos|Bytes|Total|Contagem de bytes lidos da origem do evento|Nenhuma|
|IngressStoredBytes|Bytes de entrada armazenados|Bytes|Total|Tamanho total de eventos processados e disponíveis com êxito para consulta|Nenhuma|
|IngressStoredEvents|Eventos de entrada armazenados|Contagem|Total|Contagem de eventos mesclados processados e disponíveis com êxito para consulta|Nenhuma|
|IngressReceivedMessagesTimeLag|Atraso de tempo de mensagens recebidas de entrada|Segundos|Máximo|Diferença entre a hora em que a mensagem é enfileirada na origem do evento e a hora em que ela é processada na entrada|Nenhuma|
|IngressReceivedMessagesCountLag|Atraso na contagem de mensagens recebidas de entrada|Contagem|Média|Diferença entre o número de sequência da última mensagem enfileirada na partição de origem do evento e o número de sequência de mensagens sendo processadas na entrada|Nenhuma|
|WarmStorageMaxProperties|Propriedades máximas de armazenamento quente|Contagem|Máximo|Número máximo de propriedades usadas permitidas pelo ambiente para SKU S1/S2 e número máximo de propriedades permitidas pelo armazenamento quente para o SKU PAYG|Nenhuma|
|WarmStorageUsedProperties|Propriedades usadas de armazenamento quente |Contagem|Máximo|Número de propriedades usadas pelo ambiente para SKU S1/S2 e número de propriedades usadas pelo armazenamento quente para SKU PAYG|Nenhuma|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Bytes de leitura de disco/s|BytesPerSecond|Média|Taxa de transferência média do disco devido a operações de leitura no período de exemplo.|Nenhuma|
|DiskWriteBytesPerSecond|Bytes de gravação no disco/s|BytesPerSecond|Média|Taxa de transferência média do disco devido a operações de gravação no período de exemplo.|Nenhuma|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Taxa de transferência total do disco devido a operações de leitura no período de exemplo.|Nenhuma|
|Bytes de gravação no disco|Bytes de gravação no disco|Bytes|Total|Taxa de transferência total do disco devido a operações de gravação no período de exemplo.|Nenhuma|
|DiskReadOperations|Operações de leitura de disco|Contagem|Total|O número de operações de leitura de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Nenhuma|
|DiskWriteOperations|Operações de gravação de disco|Contagem|Total|O número de operações de gravação de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Nenhuma|
|Operações de leitura de disco/s|Operações de leitura de disco/s|CountPerSecond|Média|O número médio de operações de leitura de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Nenhuma|
|Operações de gravação de disco/s|Operações de gravação de disco/s|CountPerSecond|Média|O número médio de operações de gravação de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Nenhuma|
|DiskReadLatency|Latência de leitura de disco|Milissegundos|Média|Latência de leitura total. A soma das latências de leitura do dispositivo e do kernel.|Nenhuma|
|DiskWriteLatency|Latência de gravação de disco|Milissegundos|Média|Latência de gravação total. A soma das latências de gravação do kernel e do dispositivo.|Nenhuma|
|NetworkInBytesPerSecond|Rede em bytes/s|BytesPerSecond|Média|Taxa de transferência média de rede para o tráfego recebido.|Nenhuma|
|NetworkOutBytesPerSecond|Bytes de saída de rede/s|BytesPerSecond|Média|Taxa de transferência média de rede para o tráfego transmitido.|Nenhuma|
|Entrada na Rede|Entrada na Rede|Bytes|Total|Taxa de transferência total de rede para o tráfego recebido.|Nenhuma|
|Saída da Rede|Saída da Rede|Bytes|Total|Taxa de transferência total de rede para o tráfego transmitido.|Nenhuma|
|MemoryUsed|Memória usada|Bytes|Média|A quantidade de memória da máquina que está sendo usada pela VM.|Nenhuma|
|MemoryGranted|Memória concedida|Bytes|Média|A quantidade de memória que foi concedida à VM pelo host. A memória não é concedida ao host até que ele seja tocado uma vez e a memória concedida possa ser trocada ou bloqueada se o VMkernel precisar da memória.|Nenhuma|
|MemoryActive|Memória ativa|Bytes|Média|A quantidade de memória usada pela VM na última pequena janela de tempo. Esse é o número "verdadeiro" da quantidade de memória necessária para a VM no momento. A memória adicional não utilizada pode ser trocada ou por balão sem impacto no desempenho do convidado.|Nenhuma|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A utilização da CPU. Este valor é reportado com 100% representando todos os núcleos de processador escarnecidos do sistema. Como exemplo, um VM de 2 vias usando 50% de um sistema de quatro núcleos está completamente usando dois núcleos.|Nenhuma|
|PercentageCpuReady|Percentual de CPU pronta|Milissegundos|Total|O tempo de preparação é o tempo gasto aguardando que as CPUs fiquem disponíveis no intervalo de atualização anterior.|Nenhuma|










## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuPercentage|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Instância|
|MemoryPercentage|Porcentagem de memória|Percentagem|Média|Porcentagem de memória|Instância|
|DiskQueueLength|Comprimento da fila de disco|Contagem|Média|Comprimento da fila de disco|Instância|
|HttpQueueLength|Comprimento da fila http|Contagem|Média|Comprimento da fila http|Instância|
|BytesReceived|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Dados out|Bytes|Total|Dados out|Instância|
|TcpSynSent|SYN TCP enviado|Contagem|Média|SYN TCP enviado|Instância|
|TcpSynReceived|SYN TCP recebido|Contagem|Média|SYN TCP recebido|Instância|
|TcpEstablished|TCP estabelecido|Contagem|Média|TCP estabelecido|Instância|
|TcpFinWait1|Espera de TCP fin 1|Contagem|Média|Espera de TCP fin 1|Instância|
|TcpFinWait2|Espera de TCP Fin 2|Contagem|Média|Espera de TCP Fin 2|Instância|
|TcpClosing|Fechamento de TCP|Contagem|Média|Fechamento de TCP|Instância|
|TcpCloseWait|Espera TCP de fechamento|Contagem|Média|Espera TCP de fechamento|Instância|
|TcpLastAck|Última confirmação TCP|Contagem|Média|Última confirmação TCP|Instância|
|TcpTimeWait|Espera de tempo TCP|Contagem|Média|Espera de tempo TCP|Instância|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/sites (exceto funções)

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuTime|Tempo de CPU|Segundos|Total|Tempo de CPU|Instância|
|Pedidos|Pedidos|Contagem|Total|Pedidos|Instância|
|BytesReceived|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Dados out|Bytes|Total|Dados out|Instância|
|Http101|Http 101|Contagem|Total|Http 101|Instância|
|Http2xx|2xx http|Contagem|Total|2xx http|Instância|
|Http3xx|3xx http|Contagem|Total|3xx http|Instância|
|Http401|Http 401|Contagem|Total|Http 401|Instância|
|Http403|Http 403|Contagem|Total|Http 403|Instância|
|Http404|Http 404|Contagem|Total|Http 404|Instância|
|Http406|Http 406|Contagem|Total|Http 406|Instância|
|Http4xx|4xx http|Contagem|Total|4xx http|Instância|
|Http5xx|Erros do servidor http|Contagem|Total|Erros do servidor http|Instância|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|AverageMemoryWorkingSet|Conjunto de trabalho de memória média|Bytes|Média|Conjunto de trabalho de memória média|Instância|
|AverageResponseTime|Tempo médio de resposta|Segundos|Média|Tempo médio de resposta|Instância|
|AppConnections|Ligações|Contagem|Média|Ligações|Instância|
|Alça|Contagem de pegas|Contagem|Média|Contagem de pegas|Instância|
|Fios|Contagem de fios|Contagem|Média|Contagem de fios|Instância|
|PrivateBytes|Bytes Privados|Bytes|Média|Bytes Privados|Instância|
|IoReadBytesPerSecond|Bytes de leitura de e/s por segundo|BytesPerSecond|Total|Bytes de leitura de e/s por segundo|Instância|
|IoWriteBytesPerSecond|Bytes de gravação de e/s por segundo|BytesPerSecond|Total|Bytes de gravação de e/s por segundo|Instância|
|IoOtherBytesPerSecond|E/s outros bytes por segundo|BytesPerSecond|Total|E/s outros bytes por segundo|Instância|
|IoReadOperationsPerSecond|Operações de leitura de e/s por segundo|BytesPerSecond|Total|Operações de leitura de e/s por segundo|Instância|
|IoWriteOperationsPerSecond|Operações de gravação de e/s por segundo|BytesPerSecond|Total|Operações de gravação de e/s por segundo|Instância|
|IoOtherOperationsPerSecond|E/s outras operações por segundo|BytesPerSecond|Total|E/s outras operações por segundo|Instância|
|RequestsInApplicationQueue|Solicitações na fila de aplicativos|Contagem|Média|Solicitações na fila de aplicativos|Instância|
|CurrentAssemblies|Assemblies atuais|Contagem|Média|Assemblies atuais|Instância|
|TotalAppDomains|Total de domínios de aplicativo|Contagem|Média|Total de domínios de aplicativo|Instância|
|TotalAppDomainsUnloaded|Total de domínios de aplicativo descarregados|Contagem|Média|Total de domínios de aplicativo descarregados|Instância|
|Gen0Collections|Coletas de lixo de Gen 0|Contagem|Total|Coletas de lixo de Gen 0|Instância|
|Gen1Collections|Coletas de lixo da Gen 1|Contagem|Total|Coletas de lixo da Gen 1|Instância|
|Gen2Collections|Coletas de lixo da Gen 2|Contagem|Total|Coletas de lixo da Gen 2|Instância|
|HealthCheckStatus|Status da verificação de integridade|Contagem|Média|Status da verificação de integridade|Instância|
|FileSystemUsage|Uso do sistema de arquivos|Bytes|Média|Uso do sistema de arquivos|Nenhuma|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/sites (funções)

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BytesReceived|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Dados out|Bytes|Total|Dados out|Instância|
|Http5xx|Erros do servidor http|Contagem|Total|Erros do servidor http|Instância|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|AverageMemoryWorkingSet|Conjunto de trabalho de memória média|Bytes|Média|Conjunto de trabalho de memória média|Instância|
|FunctionExecutionUnits|Unidades de execução de função|MB/milissegundos|Total|[Unidades de execução de função](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instância|
|FunctionExecutionCount|Contagem de execução de função|Contagem|Total|Contagem de execução de função|Instância|
|PrivateBytes|Bytes Privados|Bytes|Média|Bytes Privados|Instância|
|IoReadBytesPerSecond|Bytes de leitura de e/s por segundo|BytesPerSecond|Total|Bytes de leitura de e/s por segundo|Instância|
|IoWriteBytesPerSecond|Bytes de gravação de e/s por segundo|BytesPerSecond|Total|Bytes de gravação de e/s por segundo|Instância|
|IoOtherBytesPerSecond|E/s outros bytes por segundo|BytesPerSecond|Total|E/s outros bytes por segundo|Instância|
|IoReadOperationsPerSecond|Operações de leitura de e/s por segundo|BytesPerSecond|Total|Operações de leitura de e/s por segundo|Instância|
|IoWriteOperationsPerSecond|Operações de gravação de e/s por segundo|BytesPerSecond|Total|Operações de gravação de e/s por segundo|Instância|
|IoOtherOperationsPerSecond|E/s outras operações por segundo|BytesPerSecond|Total|E/s outras operações por segundo|Instância|
|RequestsInApplicationQueue|Solicitações na fila de aplicativos|Contagem|Média|Solicitações na fila de aplicativos|Instância|
|CurrentAssemblies|Assemblies atuais|Contagem|Média|Assemblies atuais|Instância|
|TotalAppDomains|Total de domínios de aplicativo|Contagem|Média|Total de domínios de aplicativo|Instância|
|TotalAppDomainsUnloaded|Total de domínios de aplicativo descarregados|Contagem|Média|Total de domínios de aplicativo descarregados|Instância|
|Gen0Collections|Coletas de lixo de Gen 0|Contagem|Total|Coletas de lixo de Gen 0|Instância|
|Gen1Collections|Coletas de lixo da Gen 1|Contagem|Total|Coletas de lixo da Gen 1|Instância|
|Gen2Collections|Coletas de lixo da Gen 2|Contagem|Total|Coletas de lixo da Gen 2|Instância|
|HealthCheckStatus|Status da verificação de integridade|Contagem|Média|Status da verificação de integridade|Instância|
|FileSystemUsage|Uso do sistema de arquivos|Bytes|Média|Uso do sistema de arquivos|Nenhuma|


## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuTime|Tempo de CPU|Segundos|Total|Tempo de CPU|Instância|
|Pedidos|Pedidos|Contagem|Total|Pedidos|Instância|
|BytesReceived|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Dados out|Bytes|Total|Dados out|Instância|
|Http101|Http 101|Contagem|Total|Http 101|Instância|
|Http2xx|2xx http|Contagem|Total|2xx http|Instância|
|Http3xx|3xx http|Contagem|Total|3xx http|Instância|
|Http401|Http 401|Contagem|Total|Http 401|Instância|
|Http403|Http 403|Contagem|Total|Http 403|Instância|
|Http404|Http 404|Contagem|Total|Http 404|Instância|
|Http406|Http 406|Contagem|Total|Http 406|Instância|
|Http4xx|4xx http|Contagem|Total|4xx http|Instância|
|Http5xx|Erros do servidor http|Contagem|Total|Erros do servidor http|Instância|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|AverageMemoryWorkingSet|Conjunto de trabalho de memória média|Bytes|Média|Conjunto de trabalho de memória média|Instância|
|AverageResponseTime|Tempo médio de resposta|Segundos|Média|Tempo médio de resposta|Instância|
|HttpResponsetime|Tempo de resposta|Segundos|Média|Tempo de resposta|Instância|
|FunctionExecutionUnits|Unidades de execução de função|Contagem|Total|Unidades de execução de função|Instância|
|FunctionExecutionCount|Contagem de execução de função|Contagem|Total|Contagem de execução de função|Instância|
|AppConnections|Ligações|Contagem|Média|Ligações|Instância|
|Alça|Contagem de pegas|Contagem|Média|Contagem de pegas|Instância|
|Fios|Contagem de fios|Contagem|Média|Contagem de fios|Instância|
|PrivateBytes|Bytes Privados|Bytes|Média|Bytes Privados|Instância|
|IoReadBytesPerSecond|Bytes de leitura de e/s por segundo|BytesPerSecond|Total|Bytes de leitura de e/s por segundo|Instância|
|IoWriteBytesPerSecond|Bytes de gravação de e/s por segundo|BytesPerSecond|Total|Bytes de gravação de e/s por segundo|Instância|
|IoOtherBytesPerSecond|E/s outros bytes por segundo|BytesPerSecond|Total|E/s outros bytes por segundo|Instância|
|IoReadOperationsPerSecond|Operações de leitura de e/s por segundo|BytesPerSecond|Total|Operações de leitura de e/s por segundo|Instância|
|IoWriteOperationsPerSecond|Operações de gravação de e/s por segundo|BytesPerSecond|Total|Operações de gravação de e/s por segundo|Instância|
|IoOtherOperationsPerSecond|E/s outras operações por segundo|BytesPerSecond|Total|E/s outras operações por segundo|Instância|
|RequestsInApplicationQueue|Solicitações na fila de aplicativos|Contagem|Média|Solicitações na fila de aplicativos|Instância|
|CurrentAssemblies|Assemblies atuais|Contagem|Média|Assemblies atuais|Instância|
|TotalAppDomains|Total de domínios de aplicativo|Contagem|Média|Total de domínios de aplicativo|Instância|
|TotalAppDomainsUnloaded|Total de domínios de aplicativo descarregados|Contagem|Média|Total de domínios de aplicativo descarregados|Instância|
|Gen0Collections|Coletas de lixo de Gen 0|Contagem|Total|Coletas de lixo de Gen 0|Instância|
|Gen1Collections|Coletas de lixo da Gen 1|Contagem|Total|Coletas de lixo da Gen 1|Instância|
|Gen2Collections|Coletas de lixo da Gen 2|Contagem|Total|Coletas de lixo da Gen 2|Instância|
|HealthCheckStatus|Status da verificação de integridade|Contagem|Média|Status da verificação de integridade|Instância|
|FileSystemUsage|Uso do sistema de arquivos|Bytes|Média|Uso do sistema de arquivos|Nenhuma|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Pedidos|Pedidos|Contagem|Total|Pedidos|Instância|
|BytesReceived|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Dados out|Bytes|Total|Dados out|Instância|
|Http101|Http 101|Contagem|Total|Http 101|Instância|
|Http2xx|2xx http|Contagem|Total|2xx http|Instância|
|Http3xx|3xx http|Contagem|Total|3xx http|Instância|
|Http401|Http 401|Contagem|Total|Http 401|Instância|
|Http403|Http 403|Contagem|Total|Http 403|Instância|
|Http404|Http 404|Contagem|Total|Http 404|Instância|
|Http406|Http 406|Contagem|Total|Http 406|Instância|
|Http4xx|4xx http|Contagem|Total|4xx http|Instância|
|Http5xx|Erros do servidor http|Contagem|Total|Erros do servidor http|Instância|
|AverageResponseTime|Tempo médio de resposta|Segundos|Média|Tempo médio de resposta|Instância|
|CpuPercentage|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Instância|
|MemoryPercentage|Porcentagem de memória|Percentagem|Média|Porcentagem de memória|Instância|
|DiskQueueLength|Comprimento da fila de disco|Contagem|Média|Comprimento da fila de disco|Instância|
|HttpQueueLength|Comprimento da fila http|Contagem|Média|Comprimento da fila http|Instância|
|ActiveRequests|Solicitações ativas|Contagem|Total|Solicitações ativas|Instância|
|TotalFrontEnds|Total de front-ends|Contagem|Média|Total de front-ends|Nenhuma|
|SmallAppServicePlanInstances|Trabalhos do plano do serviço de aplicativo pequeno|Contagem|Média|Trabalhos do plano do serviço de aplicativo pequeno|Nenhuma|
|MediumAppServicePlanInstances|Trabalhos do plano do serviço de aplicativo médio|Contagem|Média|Trabalhos do plano do serviço de aplicativo médio|Nenhuma|
|LargeAppServicePlanInstances|Trabalhos grandes do plano do serviço de aplicativo|Contagem|Média|Trabalhos grandes do plano do serviço de aplicativo|Nenhuma|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|WorkersTotal|Total de trabalhadores|Contagem|Média|Total de trabalhadores|Nenhuma|
|WorkersAvailable|Trabalhos disponíveis|Contagem|Média|Trabalhos disponíveis|Nenhuma|
|WorkersUsed|Trabalhadores usados|Contagem|Média|Trabalhadores usados|Nenhuma|
|CpuPercentage|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Instância|
|MemoryPercentage|Porcentagem de memória|Percentagem|Média|Porcentagem de memória|Instância|
## <a name="next-steps"></a>Passos seguintes
* [Leia sobre métricas no Azure Monitor](data-platform.md)
* [Criar alertas em métricas](alerts-overview.md)
* [Exportar métricas para armazenamento, Hub de eventos ou Log Analytics](platform-logs-overview.md)
