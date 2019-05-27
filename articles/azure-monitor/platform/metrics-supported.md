---
title: Monitor do Azure suportada métricas por tipo de recurso
description: Lista de métricas disponíveis para cada tipo de recurso com o Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: f3907cbc46bd226ef8e90d2061f2d36b2afabf8a
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957152"
---
# <a name="supported-metrics-with-azure-monitor"></a>Métricas suportadas com o Azure Monitor

O Azure Monitor proporciona várias formas de interagir com métricas, incluindo gráficos-las no portal, o acesso aos mesmos através da API REST ou consultando-os com o PowerShell ou CLI. Segue-se uma lista completa de todas as métricas atualmente disponíveis com o pipeline de métrico do Azure Monitor. Outras métricas poderão estar disponíveis no portal ou através de APIs herdadas. Esta lista abaixo inclui apenas as métricas disponíveis com o pipeline de métrico do Azure Monitor consolidado. Para consultar e aceder a estas métricas, utilize o [2018-01-01 api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Atualmente, o envio de métricas multidimensionais através das definições de diagnóstico não é suportado. As métricas com dimensões são exportadas como métricas dimensionais simples e agregadas em valores de dimensões.
>
> *Por exemplo*: A métrica "Mensagens recebidas" num Hub de eventos pode ser explorada e representada um por nível de fila. No entanto, se for exportada através das definições de diagnóstico, a métrica será representada como todas as mensagens recebidas em todas as filas do hub do Hub de Eventos.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|Média|QPU. Intervalo de 0-100 para S1, 0-200 para S2 e 0-400 para S4|ServerResourceType|
|memory_metric|Memória|Bytes|Média|Memória. Intervalo de 0-25 GB para S1, 0-50 GB para S2 e 0-100 GB para S4|ServerResourceType|
|TotalConnectionRequests|Pedidos de ligação total|Count|Média|Pedidos de ligação total. Estes são chegadas.|ServerResourceType|
|SuccessfullConnectionsPerSec|Ligações com êxito por segundo|CountPerSecond|Média|Taxa de conclusões de ligação com êxito.|ServerResourceType|
|TotalConnectionFailures|Falhas de ligação total|Count|Média|Total de tentativas de ligação.|ServerResourceType|
|CurrentUserSessions|Sessões de utilizador atual|Count|Média|Número atual de sessões de utilizador estabelecidas.|ServerResourceType|
|QueryPoolBusyThreads|Threads ocupadas do conjunto de consulta|Count|Média|Número de threads ocupados no conjunto de threads de consulta.|ServerResourceType|
|CommandPoolJobQueueLength|Comprimento de fila da tarefa de conjunto de comandos|Count|Média|Número de tarefas na fila do pool de threads de comando.|ServerResourceType|
|ProcessingPoolJobQueueLength|Comprimento de fila da tarefa de conjunto de processamento|Count|Média|Número de tarefas não-I/O na fila do pool de threads de processamento.|ServerResourceType|
|CurrentConnections|Ligação: Ligações atuais|Count|Média|Número atual de ligações de cliente estabelecidas.|ServerResourceType|
|CleanerCurrentPrice|Memória: Preço atual de limpeza|Count|Média|Preço atual de memória, $/byte/tempo, normalizado para 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Memória: Memória de limpeza Reduzível|Bytes|Média|Quantidade de memória, em bytes, sujeitos à remoção limpeza de segundo plano.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memória: Mais limpo nonshrinkable de memória|Bytes|Média|Quantidade de memória, em bytes, não são sujeitos à remoção limpeza de segundo plano.|ServerResourceType|
|MemoryUsage|Memória: Utilização da Memória|Bytes|Média|Utilização da memória do processo de servidor utilizado para calcular o preço de memória mais limpo. Igual ao contador Process\PrivateBytes mais o tamanho dos dados de mapeamento de memória, ignorando qualquer memória que foi mapeada ou alocada pelo mecanismo de análise dentro da memória do xVelocity (VertiPaq) em excesso do limite de memória do motor xVelocity.|ServerResourceType|
|MemoryLimitHard|Memória: Limite de memória|Bytes|Média|Limite restrito da memória, do ficheiro de configuração.|ServerResourceType|
|MemoryLimitHigh|Memória: Máximo de limite de memória|Bytes|Média|Limite máximo de memória do ficheiro de configuração.|ServerResourceType|
|MemoryLimitLow|Memória: Mínimo de limite de memória|Bytes|Média|Limite mínimo de memória do ficheiro de configuração.|ServerResourceType|
|MemoryLimitVertiPaq|Memória: Limite de memória VertiPaq|Bytes|Média|Limite de memória do ficheiro de configuração.|ServerResourceType|
|Quota|Memória: Quota|Bytes|Média|Quota de memória atual, em bytes. Quota de memória também é conhecido como uma reserva de memória ou de concessão de memória.|ServerResourceType|
|QuotaBlocked|Memória: Quotas bloqueadas|Count|Média|Número atual de pedidos de quota que estão bloqueados até outras quotas de memória serem libertas.|ServerResourceType|
|VertiPaqNonpaged|Memória: VertiPaq Nonpaged|Bytes|Média|Bytes de memória bloqueada no conjunto de trabalho para utilização pelo mecanismo de dentro da memória.|ServerResourceType|
|VertiPaqPaged|Memória: VertiPaq paginado|Bytes|Média|Bytes de memória paga em utilização para dados na memória.|ServerResourceType|
|RowsReadPerSec|A processar: Linhas lidas por segundo|CountPerSecond|Média|Taxa de linhas lidas de todas as bases de dados relacionais.|ServerResourceType|
|RowsConvertedPerSec|A processar: Linhas convertidas por segundo|CountPerSecond|Média|Taxa de linhas convertidas durante o processamento.|ServerResourceType|
|RowsWrittenPerSec|A processar: Linhas escritas por segundo|CountPerSecond|Média|Taxa de linhas escritas durante o processamento.|ServerResourceType|
|CommandPoolBusyThreads|Threads: Threads ocupadas do conjunto de comandos|Count|Média|Número de threads ocupados no conjunto de threads de comando.|ServerResourceType|
|CommandPoolIdleThreads|Threads: Threads inativas do conjunto de comandos|Count|Média|Número de threads inativas no conjunto de threads de comando.|ServerResourceType|
|LongParsingBusyThreads|Threads: Threads ocupadas de análise longa|Count|Média|Número de threads ocupados no conjunto de threads de análise longa.|ServerResourceType|
|LongParsingIdleThreads|Threads: Threads Inativas de análise longa|Count|Média|Número de threads inativas no conjunto de threads de análise longa.|ServerResourceType|
|LongParsingJobQueueLength|Threads: Comprimento da fila de tarefas análise longa|Count|Média|Número de tarefas na fila do pool de threads de análise longa.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Threads: Threads de trabalho de e/s ocupados do conjunto de processamento|Count|Média|Número de threads a executar tarefas de e/s no conjunto de threads de processamento.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Threads: Pool de threads não-I/O ocupado a processar|Count|Média|Número de threads a executar tarefas de não-I/O no conjunto de threads de processamento.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Threads: Comprimento de fila de trabalho de e/s do conjunto de processamento|Count|Média|Número de tarefas de e/s na fila do pool de threads de processamento.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Threads: Threads de trabalho de e/s inativas do conjunto de processamento|Count|Média|Número de threads inativas para tarefas de e/s no pool de threads de processamento.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Threads: Os threads não-I/O inativas do conjunto de processamento|Count|Média|Número de threads inativas no conjunto de threads de processamento dedicadas a tarefas não-I/O.|ServerResourceType|
|QueryPoolIdleThreads|Threads: Consultar threads inativas do conjunto|Count|Média|Número de threads inativas para tarefas de e/s no pool de threads de processamento.|ServerResourceType|
|QueryPoolJobQueueLength|Threads: Comprimento de fila da tarefa de conjunto de consulta|Count|Média|Número de tarefas na fila do pool de threads de consulta.|ServerResourceType|
|ShortParsingBusyThreads|Threads: Threads ocupadas de análise curtas|Count|Média|Número de threads ocupados no conjunto de threads de análise curta.|ServerResourceType|
|ShortParsingIdleThreads|Threads: Threads Inativas de análise curtas|Count|Média|Número de threads inativas no conjunto de threads de análise curta.|ServerResourceType|
|ShortParsingJobQueueLength|Threads: Comprimento da fila de tarefa análise curta|Count|Média|Número de tarefas na fila do pool de threads de análise curta.|ServerResourceType|
|memory_thrashing_metric|Degradação de memória|Percent|Média|Degradação de memória média.|ServerResourceType|
|mashup_engine_qpu_metric|M Engine QPU|Count|Média|Utilização QPU através de processos de motor de mashup|ServerResourceType|
|mashup_engine_memory_metric|Memória do motor M|Bytes|Média|Utilização da memória através de processos de motor de mashup|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalRequests|Pedidos de Gateway totais|Count|Total|Número de pedidos do gateway|Localização, nome de anfitrião|
|SuccessfulRequests|Pedidos de Gateway com êxito|Count|Total|Número de pedidos de gateway com êxito|Localização, nome de anfitrião|
|UnauthorizedRequests|Pedidos de Gateway não autorizados|Count|Total|Número de pedidos de gateway não autorizados|Localização, nome de anfitrião|
|FailedRequests|Pedidos de Gateway com falhas|Count|Total|Número de falhas em pedidos do gateway|Localização, nome de anfitrião|
|OtherRequests|Outros pedidos de Gateway|Count|Total|Número de outros pedidos de gateway|Localização, nome de anfitrião|
|Duração|Duração geral dos pedidos de Gateway|Milissegundos|Média|Geral duração dos pedidos do Gateway em milissegundos|Localização, nome de anfitrião|
|Capacidade|Capacidade|Percent|Média|Métrica de utilização para o serviço de ApiManagement|Location|
|EventHubTotalEvents|Eventos de total EventHub|Count|Total|Número de eventos enviados para o EventHub|Location|
|EventHubSuccessfulEvents|Eventos de EventHub com êxito|Count|Total|Número de eventos de EventHub com êxito|Location|
|EventHubTotalFailedEvents|Eventos de falha de EventHub|Count|Total|Número de eventos de EventHub com falhas|Location|
|EventHubRejectedEvents|Eventos de EventHub rejeitados|Count|Total|Número de eventos de EventHub rejeitados (configuração de errado ou não autorizado)|Location|
|EventHubThrottledEvents|Eventos limitados de EventHub|Count|Total|Número de eventos limitados de EventHub|Location|
|EventHubTimedoutEvents|Atingiu o tempo limite de eventos de EventHub|Count|Total|Excedeu o número de eventos de EventHub|Location|
|EventHubDroppedEvents|Eventos ignorados do EventHub|Count|Total|Número de eventos foi ignorada devido ao limite de tamanho da fila atingido|Location|
|EventHubTotalBytesSent|Tamanho de eventos de EventHub|Bytes|Total|Tamanho total de eventos de EventHub em bytes|Location|
|Pedidos|Pedidos|Count|Total|Pedidos do gateway|Localização, BackendResponseCode, LastErrorReason, GatewayResponseCode|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalJob|Total de tarefas|Count|Total|O número total de tarefas|Runbook, o Estado|
|TotalUpdateDeploymentRuns|Execuções da implementação de atualização total|Count|Total|Execuções de implementação de atualização de total software|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|Execuções do computador de implementação de atualização total|Count|Total|Computador de implementação de atualização de total software é executado numa implementação de atualização de software executar|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CoreCount|Número de núcleos dedicados|Count|Total|Número total de núcleos dedicados da conta do batch|Nenhuma dimensão|
|TotalNodeCount|Contagem de nós dedicado|Count|Total|Número total de nós dedicados da conta do batch|Nenhuma dimensão|
|LowPriorityCoreCount|Número de núcleos de LowPriority|Count|Total|Número total de núcleos de prioridade baixa na conta do batch|Nenhuma dimensão|
|TotalLowPriorityNodeCount|Contagem de nós de baixa prioridade|Count|Total|Número total de nós de baixa prioridade na conta do batch|Nenhuma dimensão|
|CreatingNodeCount|Criação de contagem de nós|Count|Total|Número de nós ser criada|Nenhuma dimensão|
|StartingNodeCount|A partir de contagem de nós|Count|Total|Número de nós partir de|Nenhuma dimensão|
|WaitingForStartTaskNodeCount|A aguardar para contagem de nós de tarefa de início|Count|Total|Número de nós aguardar para começar a conclusão da tarefa|Nenhuma dimensão|
|StartTaskFailedNodeCount|Contagem de nós de falha de tarefa de início|Count|Total|Número de nós em que não conseguiu iniciar tarefa|Nenhuma dimensão|
|IdleNodeCount|Contagem de nós de inatividade|Count|Total|Número de nós Inativos|Nenhuma dimensão|
|OfflineNodeCount|Contagem de nós offline|Count|Total|Número de nós offline|Nenhuma dimensão|
|RebootingNodeCount|Contagem de nós a reiniciar|Count|Total|Número de nós a reiniciar|Nenhuma dimensão|
|ReimagingNodeCount|A recriar imagem da contagem de nós|Count|Total|Número de nós recriação total de imagens|Nenhuma dimensão|
|RunningNodeCount|Contagem de nós em execução|Count|Total|Número de nós de execução|Nenhuma dimensão|
|LeavingPoolNodeCount|Deixando a contagem de nós do conjunto|Count|Total|Número de nós, deixando o conjunto|Nenhuma dimensão|
|UnusableNodeCount|Contagem de nós inutilizável|Count|Total|Número de nós inutilizáveis|Nenhuma dimensão|
|PreemptedNodeCount|Contagem de nós admitiu preempção for|Count|Total|Número de nós admitiu preempção for|Nenhuma dimensão|
|TaskStartEvent|Eventos de início de tarefa|Count|Total|Número total de tarefas que foram iniciadas|Nenhuma dimensão|
|TaskCompleteEvent|Eventos de conclusão de tarefa|Count|Total|Número total de tarefas que foram concluídas|Nenhuma dimensão|
|TaskFailEvent|Eventos de falha de tarefa|Count|Total|Número total de tarefas que foram concluídas em estado de falha|Nenhuma dimensão|
|PoolCreateEvent|Eventos de criação de conjunto|Count|Total|Número total de agrupamentos que foram criados|Nenhuma dimensão|
|PoolResizeStartEvent|Eventos de início de redimensionamento de conjunto|Count|Total|Número total de redimensiona conjunto que foram iniciadas|Nenhuma dimensão|
|PoolResizeCompleteEvent|Eventos de conclusão de redimensionamento de conjunto|Count|Total|Número total de redimensiona conjunto que foram concluídas|Nenhuma dimensão|
|PoolDeleteStartEvent|Eventos de início de eliminação de conjunto|Count|Total|Número total de eliminações de agrupamento que já iniciou|Nenhuma dimensão|
|PoolDeleteCompleteEvent|Conjunto eliminar eventos concluída|Count|Total|Número total de eliminações de conjunto que foram concluídas|Nenhuma dimensão|
|JobDeleteCompleteEvent|Tarefa eliminar eventos concluída|Count|Total|Número total de tarefas que foram eliminados com êxito.|Nenhuma dimensão|
|JobDeleteStartEvent|Eventos de início de eliminação de tarefa|Count|Total|Número total de tarefas que foram solicitadas para serem eliminados.|Nenhuma dimensão|
|JobDisableCompleteEvent|Eventos de conclusão do trabalho Disable|Count|Total|Número total de tarefas que foram desativados com êxito.|Nenhuma dimensão|
|JobDisableStartEvent|Eventos de início do trabalho de Desativação|Count|Total|Número total de tarefas que foram solicitadas para ser desativado.|Nenhuma dimensão|
|JobStartEvent|Eventos de início de tarefa|Count|Total|Número total de tarefas que foram iniciadas com êxito.|Nenhuma dimensão|
|JobTerminateCompleteEvent|Tarefa terminar eventos concluídos|Count|Total|Número total de tarefas que foi terminada com êxito.|Nenhuma dimensão|
|JobTerminateStartEvent|Tarefa terminar eventos de início|Count|Total|Número total de tarefas que foram solicitadas para ser terminada.|Nenhuma dimensão|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|connectedclients|Clientes Ligados|Count|Máximo||ShardId|
|totalcommandsprocessed|Total de Operações|Count|Total||ShardId|
|cachehits|Acertos na Cache|Count|Total||ShardId|
|cachemisses|Falhas na Cache|Count|Total||ShardId|
|getcommands|Obtém|Count|Total||ShardId|
|setcommands|Conjuntos|Count|Total||ShardId|
|operationsPerSecond|Operações por segundo|Count|Máximo||ShardId|
|evictedkeys|Chaves Excluídas|Count|Total||ShardId|
|totalkeys|Chaves Totais|Count|Máximo||ShardId|
|expiredkeys|Chaves Expiradas|Count|Total||ShardId|
|usedmemory|Memória Utilizada|Bytes|Máximo||ShardId|
|usedmemorypercentage|Percentagem de memória utilizada|Percent|Máximo||ShardId|
|usedmemoryRss|RSS de memória utilizada|Bytes|Máximo||ShardId|
|serverLoad|Carga do Servidor|Percent|Máximo||ShardId|
|cacheWrite|Escrita na Cache|BytesPerSecond|Máximo||ShardId|
|cacheRead|Leitura da Cache|BytesPerSecond|Máximo||ShardId|
|percentProcessorTime|CPU|Percent|Máximo||ShardId|
|cacheLatency|Latência de cache microssegundos (pré-visualização)|Count|Média||ShardId, SampleType|
|erros|Erros|Count|Máximo||ShardId, ErrorType|
|connectedclients0|Clientes ligados (partição horizontal 0)|Count|Máximo||Nenhuma dimensão|
|totalcommandsprocessed0|Total de operações (partição horizontal 0)|Count|Total||Nenhuma dimensão|
|cachehits0|Acertos na cache (partição horizontal 0)|Count|Total||Nenhuma dimensão|
|cachemisses0|Falhas de acerto na cache (partição horizontal 0)|Count|Total||Nenhuma dimensão|
|getcommands0|Obtém (partição horizontal 0)|Count|Total||Nenhuma dimensão|
|setcommands0|Conjuntos de (partição horizontal 0)|Count|Total||Nenhuma dimensão|
|operationsPerSecond0|Operações por segundo (partição horizontal 0)|Count|Máximo||Nenhuma dimensão|
|evictedkeys0|Chaves excluídas (partição horizontal 0)|Count|Total||Nenhuma dimensão|
|totalkeys0|Chaves totais (partição horizontal 0)|Count|Máximo||Nenhuma dimensão|
|expiredkeys0|Chaves expiradas (partição horizontal 0)|Count|Total||Nenhuma dimensão|
|usedmemory0|Memória utilizada (partição horizontal 0)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss0|Memória utilizada RSS (partição horizontal 0)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad0|Carga do servidor (partição horizontal 0)|Percent|Máximo||Nenhuma dimensão|
|cacheWrite0|Escrita na cache (partição horizontal 0)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead0|Leitura da cache (partição horizontal 0)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime0|CPU (partição horizontal 0)|Percent|Máximo||Nenhuma dimensão|
|connectedclients1|Clientes ligados (partição horizontal 1)|Count|Máximo||Nenhuma dimensão|
|totalcommandsprocessed1|Total de operações (partição horizontal 1)|Count|Total||Nenhuma dimensão|
|cachehits1|Acertos na cache (partição horizontal 1)|Count|Total||Nenhuma dimensão|
|cachemisses1|Falhas de acerto na cache (partição horizontal 1)|Count|Total||Nenhuma dimensão|
|getcommands1|Obtém (partição horizontal 1)|Count|Total||Nenhuma dimensão|
|setcommands1|Conjuntos de (partição horizontal 1)|Count|Total||Nenhuma dimensão|
|operationsPerSecond1|Operações por segundo (partição horizontal 1)|Count|Máximo||Nenhuma dimensão|
|evictedkeys1|Chaves excluídas (partição horizontal 1)|Count|Total||Nenhuma dimensão|
|totalkeys1|Chaves totais (partição horizontal 1)|Count|Máximo||Nenhuma dimensão|
|expiredkeys1|Chaves expiradas (partição horizontal 1)|Count|Total||Nenhuma dimensão|
|usedmemory1|Memória utilizada (partição horizontal 1)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss1|Memória utilizada RSS (partição horizontal 1)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad1|Carga do servidor (partição horizontal 1)|Percent|Máximo||Nenhuma dimensão|
|cacheWrite1|Escrita na cache (partição horizontal 1)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead1|Leitura da cache (partição horizontal 1)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime1|CPU (partição horizontal 1)|Percent|Máximo||Nenhuma dimensão|
|connectedclients2|Clientes ligados (partição horizontal 2)|Count|Máximo||Nenhuma dimensão|
|totalcommandsprocessed2|Total de operações (partição horizontal 2)|Count|Total||Nenhuma dimensão|
|cachehits2|Acertos na cache (partição horizontal 2)|Count|Total||Nenhuma dimensão|
|cachemisses2|Falhas de acerto na cache (partição horizontal 2)|Count|Total||Nenhuma dimensão|
|getcommands2|Obtém (partição horizontal 2)|Count|Total||Nenhuma dimensão|
|setcommands2|Conjuntos de (partição horizontal 2)|Count|Total||Nenhuma dimensão|
|operationsPerSecond2|Operações por segundo (partição horizontal 2)|Count|Máximo||Nenhuma dimensão|
|evictedkeys2|Chaves excluídas (partição horizontal 2)|Count|Total||Nenhuma dimensão|
|totalkeys2|Chaves totais (partição horizontal 2)|Count|Máximo||Nenhuma dimensão|
|expiredkeys2|Chaves expiradas (partição horizontal 2)|Count|Total||Nenhuma dimensão|
|usedmemory2|Memória utilizada (partição horizontal 2)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss2|Memória utilizada RSS (partição horizontal 2)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad2|Carga do servidor (partição horizontal 2)|Percent|Máximo||Nenhuma dimensão|
|cacheWrite2|Escrita na cache (partição horizontal 2)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead2|Leitura da cache (partição horizontal 2)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime2|CPU (partição horizontal 2)|Percent|Máximo||Nenhuma dimensão|
|connectedclients3|Clientes ligados (partição horizontal 3)|Count|Máximo||Nenhuma dimensão|
|totalcommandsprocessed3|Total de operações (partição horizontal 3)|Count|Total||Nenhuma dimensão|
|cachehits3|Acertos na cache (partição horizontal 3)|Count|Total||Nenhuma dimensão|
|cachemisses3|Falhas de acerto na cache (partição horizontal 3)|Count|Total||Nenhuma dimensão|
|getcommands3|Obtém (partição horizontal 3)|Count|Total||Nenhuma dimensão|
|setcommands3|Conjuntos de (partição horizontal 3)|Count|Total||Nenhuma dimensão|
|operationsPerSecond3|Operações por segundo (partição horizontal 3)|Count|Máximo||Nenhuma dimensão|
|evictedkeys3|Chaves excluídas (partição horizontal 3)|Count|Total||Nenhuma dimensão|
|totalkeys3|Chaves totais (partição horizontal 3)|Count|Máximo||Nenhuma dimensão|
|expiredkeys3|Chaves expiradas (partição horizontal 3)|Count|Total||Nenhuma dimensão|
|usedmemory3|Memória utilizada (partição horizontal 3)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss3|Memória utilizada RSS (partição horizontal 3)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad3|Carga do servidor (partição horizontal 3)|Percent|Máximo||Nenhuma dimensão|
|cacheWrite3|Escrita na cache (partição horizontal 3)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead3|Leitura da cache (partição horizontal 3)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime3|CPU (partição horizontal 3)|Percent|Máximo||Nenhuma dimensão|
|connectedclients4|Clientes ligados (partição horizontal 4)|Count|Máximo||Nenhuma dimensão|
|totalcommandsprocessed4|Total de operações (partição horizontal 4)|Count|Total||Nenhuma dimensão|
|cachehits4|Acertos na cache (partição horizontal 4)|Count|Total||Nenhuma dimensão|
|cachemisses4|Falhas de acerto na cache (partição horizontal 4)|Count|Total||Nenhuma dimensão|
|getcommands4|Obtém (partição horizontal 4)|Count|Total||Nenhuma dimensão|
|setcommands4|Conjuntos de (partição horizontal 4)|Count|Total||Nenhuma dimensão|
|operationsPerSecond4|Operações por segundo (partição horizontal 4)|Count|Máximo||Nenhuma dimensão|
|evictedkeys4|Chaves excluídas (partição horizontal 4)|Count|Total||Nenhuma dimensão|
|totalkeys4|Chaves totais (partição horizontal 4)|Count|Máximo||Nenhuma dimensão|
|expiredkeys4|Chaves expiradas (partição horizontal 4)|Count|Total||Nenhuma dimensão|
|usedmemory4|Memória utilizada (partição horizontal 4)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss4|Memória utilizada RSS (partição horizontal 4)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad4|Carga do servidor (partição horizontal 4)|Percent|Máximo||Nenhuma dimensão|
|cacheWrite4|Escrita na cache (partição horizontal 4)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead4|Leitura da cache (partição horizontal 4)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime4|CPU (partição horizontal 4)|Percent|Máximo||Nenhuma dimensão|
|connectedclients5|Clientes ligados (partição horizontal 5)|Count|Máximo||Nenhuma dimensão|
|totalcommandsprocessed5|Total de operações (partição horizontal 5)|Count|Total||Nenhuma dimensão|
|cachehits5|Acertos na cache (partição horizontal 5)|Count|Total||Nenhuma dimensão|
|cachemisses5|Falhas de acerto na cache (partição horizontal 5)|Count|Total||Nenhuma dimensão|
|getcommands5|Obtém (partição horizontal 5)|Count|Total||Nenhuma dimensão|
|setcommands5|Conjuntos de (partição horizontal 5)|Count|Total||Nenhuma dimensão|
|operationsPerSecond5|Operações por segundo (partição horizontal 5)|Count|Máximo||Nenhuma dimensão|
|evictedkeys5|Chaves excluídas (partição horizontal 5)|Count|Total||Nenhuma dimensão|
|totalkeys5|Chaves totais (partição horizontal 5)|Count|Máximo||Nenhuma dimensão|
|expiredkeys5|Chaves expiradas (partição horizontal 5)|Count|Total||Nenhuma dimensão|
|usedmemory5|Memória utilizada (partição horizontal 5)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss5|Memória utilizada RSS (partição horizontal 5)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad5|Carga do servidor (partição horizontal 5)|Percent|Máximo||Nenhuma dimensão|
|cacheWrite5|Escrita na cache (partição horizontal 5)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead5|Leitura da cache (partição horizontal 5)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime5|CPU (partição horizontal 5)|Percent|Máximo||Nenhuma dimensão|
|connectedclients6|Clientes ligados (partição horizontal 6)|Count|Máximo||Nenhuma dimensão|
|totalcommandsprocessed6|Total de operações (partição horizontal 6)|Count|Total||Nenhuma dimensão|
|cachehits6|Acertos na cache (partição horizontal 6)|Count|Total||Nenhuma dimensão|
|cachemisses6|Falhas de acerto na cache (partição horizontal 6)|Count|Total||Nenhuma dimensão|
|getcommands6|Obtém (partição horizontal 6)|Count|Total||Nenhuma dimensão|
|setcommands6|Conjuntos de (partição horizontal 6)|Count|Total||Nenhuma dimensão|
|operationsPerSecond6|Operações por segundo (partição horizontal 6)|Count|Máximo||Nenhuma dimensão|
|evictedkeys6|Chaves excluídas (partição horizontal 6)|Count|Total||Nenhuma dimensão|
|totalkeys6|Chaves totais (partição horizontal 6)|Count|Máximo||Nenhuma dimensão|
|expiredkeys6|Chaves expiradas (partição horizontal 6)|Count|Total||Nenhuma dimensão|
|usedmemory6|Memória utilizada (partição horizontal 6)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss6|Memória utilizada RSS (partição horizontal 6)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad6|Carga do servidor (partição horizontal 6)|Percent|Máximo||Nenhuma dimensão|
|cacheWrite6|Escrita na cache (partição horizontal 6)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead6|Leitura da cache (partição horizontal 6)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime6|CPU (partição horizontal 6)|Percent|Máximo||Nenhuma dimensão|
|connectedclients7|Clientes ligados (partição horizontal 7)|Count|Máximo||Nenhuma dimensão|
|totalcommandsprocessed7|Total de operações (partição horizontal 7)|Count|Total||Nenhuma dimensão|
|cachehits7|Acertos na cache (partição horizontal 7)|Count|Total||Nenhuma dimensão|
|cachemisses7|Falhas de acerto na cache (partição horizontal 7)|Count|Total||Nenhuma dimensão|
|getcommands7|Obtém (partição horizontal 7)|Count|Total||Nenhuma dimensão|
|setcommands7|Conjuntos de (partição horizontal 7)|Count|Total||Nenhuma dimensão|
|operationsPerSecond7|Operações por segundo (partição horizontal 7)|Count|Máximo||Nenhuma dimensão|
|evictedkeys7|Chaves excluídas (partição horizontal 7)|Count|Total||Nenhuma dimensão|
|totalkeys7|Chaves totais (partição horizontal 7)|Count|Máximo||Nenhuma dimensão|
|expiredkeys7|Chaves expiradas (partição horizontal 7)|Count|Total||Nenhuma dimensão|
|usedmemory7|Memória utilizada (partição horizontal 7)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss7|Memória utilizada RSS (partição horizontal 7)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad7|Carga do servidor (partição horizontal 7)|Percent|Máximo||Nenhuma dimensão|
|cacheWrite7|Escrita na cache (partição horizontal 7)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead7|Leitura da cache (partição horizontal 7)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime7|CPU (partição horizontal 7)|Percent|Máximo||Nenhuma dimensão|
|connectedclients8|Clientes ligados (partição horizontal 8)|Count|Máximo||Nenhuma dimensão|
|totalcommandsprocessed8|Total de operações (partição horizontal 8)|Count|Total||Nenhuma dimensão|
|cachehits8|Acertos na cache (partição horizontal 8)|Count|Total||Nenhuma dimensão|
|cachemisses8|Falhas de acerto na cache (partição horizontal 8)|Count|Total||Nenhuma dimensão|
|getcommands8|Obtém (partição horizontal 8)|Count|Total||Nenhuma dimensão|
|setcommands8|Conjuntos de (partição horizontal 8)|Count|Total||Nenhuma dimensão|
|operationsPerSecond8|Operações por segundo (partição horizontal 8)|Count|Máximo||Nenhuma dimensão|
|evictedkeys8|Chaves excluídas (partição horizontal 8)|Count|Total||Nenhuma dimensão|
|totalkeys8|Chaves totais (partição horizontal 8)|Count|Máximo||Nenhuma dimensão|
|expiredkeys8|Chaves expiradas (partição horizontal 8)|Count|Total||Nenhuma dimensão|
|usedmemory8|Memória utilizada (partição horizontal 8)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss8|Memória utilizada RSS (partição horizontal 8)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad8|Carga do servidor (partição horizontal 8)|Percent|Máximo||Nenhuma dimensão|
|cacheWrite8|Escrita na cache (partição horizontal 8)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead8|Leitura da cache (partição horizontal 8)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime8|CPU (partição horizontal 8)|Percent|Máximo||Nenhuma dimensão|
|connectedclients9|Clientes ligados (partição horizontal 9)|Count|Máximo||Nenhuma dimensão|
|totalcommandsprocessed9|Total de operações (partição horizontal 9)|Count|Total||Nenhuma dimensão|
|cachehits9|Acertos na cache (partição horizontal 9)|Count|Total||Nenhuma dimensão|
|cachemisses9|Falhas de acerto na cache (partição horizontal 9)|Count|Total||Nenhuma dimensão|
|getcommands9|Obtém (partição horizontal 9)|Count|Total||Nenhuma dimensão|
|setcommands9|Conjuntos de (partição horizontal 9)|Count|Total||Nenhuma dimensão|
|operationsPerSecond9|Operações por segundo (partição horizontal 9)|Count|Máximo||Nenhuma dimensão|
|evictedkeys9|Chaves excluídas (partição horizontal 9)|Count|Total||Nenhuma dimensão|
|totalkeys9|Chaves totais (partição horizontal 9)|Count|Máximo||Nenhuma dimensão|
|expiredkeys9|Chaves expiradas (partição horizontal 9)|Count|Total||Nenhuma dimensão|
|usedmemory9|Memória utilizada (partição horizontal 9)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss9|Memória utilizada RSS (partição horizontal 9)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad9|Carga do servidor (partição horizontal 9)|Percent|Máximo||Nenhuma dimensão|
|cacheWrite9|Escrita na cache (partição horizontal 9)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead9|Leitura da cache (partição horizontal 9)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime9|CPU (partição horizontal 9)|Percent|Máximo||Nenhuma dimensão|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades do computador alocadas que estão atualmente em utilização pela Máquina Virtual ou Máquinas Virtuais.|Nenhuma dimensão|
|Entrada de Rede|Entrada de Rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela Máquina Virtual ou Máquinas Virtuais (Tráfego de Entrada).|Nenhuma dimensão|
|Saída de Rede|Saída de Rede|Bytes|Total|O número de bytes saídos de todas as interfaces de rede pela(s) Máquina(s) Virtual(is) (Tráfego de Saída).|Nenhuma dimensão|
|Bytes Lidos de Disco/Seg|Leitura de Disco|BytesPerSecond|Média|Média de bytes lidos do disco durante o período de monitorização.|Nenhuma dimensão|
|Bytes Escritos em Disco/Seg|Escrita de Disco|BytesPerSecond|Média|Média de bytes escritos no disco durante o período de monitorização.|Nenhuma dimensão|
|Operações/Seg de Leitura do Disco|Operações/Seg de Leitura do Disco|CountPerSecond|Média|IOPS de Leitura do Disco.|Nenhuma dimensão|
|Operações/Seg de Escrita de Disco|Operações/Seg de Escrita de Disco|CountPerSecond|Média|IOPS de Escrita de Disco.|Nenhuma dimensão|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades do computador alocadas que estão atualmente em utilização pela Máquina Virtual ou Máquinas Virtuais.|RoleInstanceId|
|Entrada de Rede|Entrada de Rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela Máquina Virtual ou Máquinas Virtuais (Tráfego de Entrada).|RoleInstanceId|
|Saída de Rede|Saída de Rede|Bytes|Total|O número de bytes saídos de todas as interfaces de rede pela(s) Máquina(s) Virtual(is) (Tráfego de Saída).|RoleInstanceId|
|Bytes Lidos de Disco/Seg|Leitura de Disco|BytesPerSecond|Média|Média de bytes lidos do disco durante o período de monitorização.|RoleInstanceId|
|Bytes Escritos em Disco/Seg|Escrita de Disco|BytesPerSecond|Média|Média de bytes escritos no disco durante o período de monitorização.|RoleInstanceId|
|Operações/Seg de Leitura do Disco|Operações/Seg de Leitura do Disco|CountPerSecond|Média|IOPS de Leitura do Disco.|RoleInstanceId|
|Operações/Seg de Escrita de Disco|Operações/Seg de Escrita de Disco|CountPerSecond|Média|IOPS de Escrita de Disco.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalCalls|Total de Chamadas|Count|Total|Número total de chamadas.|Região de ApiName, OperationName,|
|SuccessfulCalls|Chamadas com êxito|Count|Total|Número de chamadas com êxito.|Região de ApiName, OperationName,|
|TotalErrors|Total de Erros|Count|Total|Número total de chamadas com resposta de erro (código de resposta HTTP 4xx ou 5xx).|Região de ApiName, OperationName,|
|BlockedCalls|Chamadas Bloqueadas|Count|Total|Número de chamadas que excederam o limite da tarifa ou quota.|Região de ApiName, OperationName,|
|ServerErrors|Erros no Servidor|Count|Total|Número de chamadas com erro interno do serviço (código de resposta HTTP 5xx).|Região de ApiName, OperationName,|
|ClientErrors|Erros do Cliente|Count|Total|Número de chamadas com erro do lado do cliente (código de resposta HTTP 4xx).|Região de ApiName, OperationName,|
|DataIn|Entrada de Dados|Bytes|Total|Tamanho dos dados recebidos em bytes.|Região de ApiName, OperationName,|
|DataOut|Saída de Dados|Bytes|Total|Tamanho dos dados enviados em bytes.|Região de ApiName, OperationName,|
|Latência|Latência|Milissegundos|Média|Latência em milisegundos.|Região de ApiName, OperationName,|
|CharactersTranslated|Carateres Traduzidos|Count|Total|Número total de carateres na receção do pedido de texto.|Região de ApiName, OperationName,|
|CharactersTrained|Carateres preparados|Count|Total|Número total de carateres treinados.|Região de ApiName, OperationName,|
|SpeechSessionDuration|Duração da Sessão de Voz|Segundos|Total|Duração total da sessão de voz em segundos.|Região de ApiName, OperationName,|
|TotalTransactions|Total de Transações|Count|Total|Número total de transações.|Nenhuma dimensão|
|TotalTokenCalls|Chamadas de Token Totais|Count|Total|Número total de chamadas de token.|Região de ApiName, OperationName,|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades do computador alocadas que estão atualmente em utilização pela Máquina Virtual ou Máquinas Virtuais|Nenhuma dimensão|
|Entrada de Rede|Rede no Faturáveis|Bytes|Total|O número de bytes cobrar recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada)|Nenhuma dimensão|
|Saída de Rede|Out cobrar de rede|Bytes|Total|O número de bytes cobrar saídos de todas as interfaces de rede, as máquinas virtuais (tráfego de saída)|Nenhuma dimensão|
|Bytes de Leitura do Disco|Bytes de Leitura do Disco|Bytes|Total|Bytes lidos a partir do disco durante o período de monitorização|Nenhuma dimensão|
|Bytes de Escrita de Disco|Bytes de Escrita de Disco|Bytes|Total|Bytes escritos no disco durante o período de monitorização|Nenhuma dimensão|
|Operações/Seg de Leitura do Disco|Operações/Seg de Leitura do Disco|CountPerSecond|Média|IOPS de Leitura do Disco|Nenhuma dimensão|
|Operações/Seg de Escrita de Disco|Operações/Seg de Escrita de Disco|CountPerSecond|Média|IOPS de Escrita de Disco|Nenhuma dimensão|
|Créditos CPU Restantes|Créditos CPU Restantes|Count|Média|Número total de créditos disponíveis para rajada|Nenhuma dimensão|
|Créditos CPU Consumidos|Créditos CPU Consumidos|Count|Média|Número total de créditos consumidos pela Máquina Virtual|Nenhuma dimensão|
|Bytes Lidos por Disco/seg|Bytes Lidos do Disco de Dados/seg (Preterido)|CountPerSecond|Média|Bytes/Seg lidos a partir de um único disco durante o período de monitorização|SlotId|
|Bytes Escritos por Disco/seg|Bytes Escritos do Disco de Dados/Seg (Preterido)|CountPerSecond|Média|Bytes/Seg escritos para um único disco durante o período de monitorização|SlotId|
|Operações de Leitura Por Disco/Seg|Operações de Leitura do Disco de Dados/Seg (Preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitorização|SlotId|
|Operações de Escrita Por Disco/Seg|Operações de Escrita do Disco de Dados/Seg (Preterido)|CountPerSecond|Média|Escreva IOPS de um único disco durante o período de monitorização|SlotId|
|Por Disco de QD|Disco de Dados QD (Preterido)|Count|Média|Profundidade da Fila do Disco de Dados (ou Comprimento da Fila)|SlotId|
|Bytes de Leitura por Disco de SO/Seg.|Bytes/Seg da Leitura de Discos do SO (Preterido)|CountPerSecond|Média|Bytes/Seg lidos a partir de um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Bytes de Escrita por Disco de SO/Seg.|Bytes/Seg Escrita de Discos do SO (Preterido)|CountPerSecond|Média|Bytes/Seg escritos para um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Operações de Leitura por Disco de SO/seg|Operações/ Seg. da Leitura de Discos do SO (Preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Operações de Escrita Por Disco de SO/Seg|Operações/Seg Escrita de Discos do SO (Preterido)|CountPerSecond|Média|Escreva IOPS de um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Por Disco de Sistema Operativo QD|Disco do SO QD (Preterido)|Count|Média|Profundidade da Fila de Disco de Sistema Operativo (ou Comprimento da Fila)|Nenhuma dimensão|
|Bytes Lidos do Disco de Dados/seg|Bytes de Leitura de Disco de Dados/Seg (Pré-visualização)|CountPerSecond|Média|Bytes/Seg lidos a partir de um único disco durante o período de monitorização|LUN|
|Bytes Escritos do Disco de Dados/seg|Bytes de Escrita de Disco de Dados/Seg (Pré-visualização)|CountPerSecond|Média|Bytes/Seg escritos para um único disco durante o período de monitorização|LUN|
|Operações de Leitura em Disco de Dados/Seg|Operações de Leitura de Disco de Dados/Seg (Pré-visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitorização|LUN|
|Operações de Escrita em Disco de Dados/Seg|Operações de Escrita de Disco de Dados/Seg (Pré-visualização)|CountPerSecond|Média|Escreva IOPS de um único disco durante o período de monitorização|LUN|
|Profundidade da Fila do Disco de Dados|Profundidade da Fila do Discos de Dados (Pré-visualização)|Count|Média|Profundidade da Fila do Disco de Dados (ou Comprimento da Fila)|LUN|
|Bytes/seg da Leitura de Discos do SO|Bytes de Leitura de Disco de SO/Seg (Pré-visualização)|CountPerSecond|Média|Bytes/Seg lidos a partir de um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Bytes/sec da Escrita de Discos do SO|Bytes de Escrita de Disco de SO/Seg (Pré-visualização)|CountPerSecond|Média|Bytes/Seg escritos para um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Operações de Leitura de Disco de SO/Seg.|Operações de Leitura de Disco de SO/Seg (Pré-visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Operações de Escrita de Disco de SO/Seg|Operações de Escrita de Disco de SO/Seg (Pré-visualização)|CountPerSecond|Média|Escreva IOPS de um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Profundidade da Fila do Disco do SO|Profundidade da Fila do Disco do SO (Pré-visualização)|Count|Média|Profundidade da Fila de Disco de Sistema Operativo (ou Comprimento da Fila)|Nenhuma dimensão|
|Fluxos de Entrada|Fluxos de Entrada (Pré-visualização)|Count|Média|Os Fluxos de Entrada são o número de fluxos atuais na direção de entrada (tráfego a entrar da VM)|Nenhuma dimensão|
|Fluxos de Saída|Fluxos de Saída (Pré-visualização)|Count|Média|Os Fluxos de Saída são o número de fluxos atuais na direção de saída (tráfego a sair da VM)|Nenhuma dimensão|
|Taxa de Criação Máxima de Fluxos de Entrada|Taxa de Criação Máxima de Fluxos de Entrada (Pré-visualização)|CountPerSecond|Média|A taxa de criação máxima de fluxos de entrada (tráfego que vai para a VM)|Nenhuma dimensão|
|Taxa de Criação Máxima de Fluxos de Saída|Taxa de Criação Máxima de Fluxos de Saída (Pré-visualização)|CountPerSecond|Média|A taxa de criação máxima de fluxos de saída (tráfego que sai da VM)|Nenhuma dimensão|
|Acerto de Leitura na Cache do Disco de Dados Premium|Acerto de Leitura na Cache do Disco de Dados Premium (Pré-visualização)|Percent|Média|Acerto de Leitura na Cache do Disco de Dados Premium|LUN|
|Falha de Acerto de Leitura na Cache do Disco de Dados Premium|Falha de Acerto de Leitura na Cache do Disco de Dados Premium (Pré-visualização)|Percent|Média|Falha de Acerto de Leitura na Cache do Disco de Dados Premium|LUN|
|Acerto de Leitura na Cache do Disco do SO Premium|Acerto de Leitura na Cache do Disco do SO Premium (Pré-visualização)|Percent|Média|Acerto de Leitura na Cache do Disco do SO Premium|Nenhuma dimensão|
|Falha de Acerto de Leitura na Cache do Disco do SO Premium|Falha de Acerto de Leitura na Cache do Disco do SO Premium (Pré-visualização)|Percent|Média|Falha de Acerto de Leitura na Cache do Disco do SO Premium|Nenhuma dimensão|
|Rede no Total|Rede no Total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela Máquina Virtual ou Máquinas Virtuais (Tráfego de Entrada)|Nenhuma dimensão|
|Rede total|Rede total|Bytes|Total|O número de bytes saídos de todas as interfaces de rede pela(s) Máquina(s) Virtual(is) (Tráfego de Saída)|Nenhuma dimensão|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades do computador alocadas que estão atualmente em utilização pela Máquina Virtual ou Máquinas Virtuais|VMName|
|Entrada de Rede|Rede no Faturáveis|Bytes|Total|O número de bytes cobrar recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada)|VMName|
|Saída de Rede|Out cobrar de rede|Bytes|Total|O número de bytes cobrar saídos de todas as interfaces de rede, as máquinas virtuais (tráfego de saída)|VMName|
|Bytes de Leitura do Disco|Bytes de Leitura do Disco|Bytes|Total|Bytes lidos a partir do disco durante o período de monitorização|VMName|
|Bytes de Escrita de Disco|Bytes de Escrita de Disco|Bytes|Total|Bytes escritos no disco durante o período de monitorização|VMName|
|Operações/Seg de Leitura do Disco|Operações/Seg de Leitura do Disco|CountPerSecond|Média|IOPS de Leitura do Disco|VMName|
|Operações/Seg de Escrita de Disco|Operações/Seg de Escrita de Disco|CountPerSecond|Média|IOPS de Escrita de Disco|VMName|
|Créditos CPU Restantes|Créditos CPU Restantes|Count|Média|Número total de créditos disponíveis para rajada|Nenhuma dimensão|
|Créditos CPU Consumidos|Créditos CPU Consumidos|Count|Média|Número total de créditos consumidos pela Máquina Virtual|Nenhuma dimensão|
|Bytes Lidos por Disco/seg|Bytes Lidos do Disco de Dados/seg (Preterido)|CountPerSecond|Média|Bytes/Seg lidos a partir de um único disco durante o período de monitorização|SlotId|
|Bytes Escritos por Disco/seg|Bytes Escritos do Disco de Dados/Seg (Preterido)|CountPerSecond|Média|Bytes/Seg escritos para um único disco durante o período de monitorização|SlotId|
|Operações de Leitura Por Disco/Seg|Operações de Leitura do Disco de Dados/Seg (Preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitorização|SlotId|
|Operações de Escrita Por Disco/Seg|Operações de Escrita do Disco de Dados/Seg (Preterido)|CountPerSecond|Média|Escreva IOPS de um único disco durante o período de monitorização|SlotId|
|Por Disco de QD|Disco de Dados QD (Preterido)|Count|Média|Profundidade da Fila do Disco de Dados (ou Comprimento da Fila)|SlotId|
|Bytes de Leitura por Disco de SO/Seg.|Bytes/Seg da Leitura de Discos do SO (Preterido)|CountPerSecond|Média|Bytes/Seg lidos a partir de um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Bytes de Escrita por Disco de SO/Seg.|Bytes/Seg Escrita de Discos do SO (Preterido)|CountPerSecond|Média|Bytes/Seg escritos para um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Operações de Leitura por Disco de SO/seg|Operações/ Seg. da Leitura de Discos do SO (Preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Operações de Escrita Por Disco de SO/Seg|Operações/Seg Escrita de Discos do SO (Preterido)|CountPerSecond|Média|Escreva IOPS de um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Por Disco de Sistema Operativo QD|Disco do SO QD (Preterido)|Count|Média|Profundidade da Fila de Disco de Sistema Operativo (ou Comprimento da Fila)|Nenhuma dimensão|
|Bytes Lidos do Disco de Dados/seg|Bytes de Leitura de Disco de Dados/Seg (Pré-visualização)|CountPerSecond|Média|Bytes/Seg lidos a partir de um único disco durante o período de monitorização|LUN, VMName|
|Bytes Escritos do Disco de Dados/seg|Bytes de Escrita de Disco de Dados/Seg (Pré-visualização)|CountPerSecond|Média|Bytes/Seg escritos para um único disco durante o período de monitorização|LUN, VMName|
|Operações de Leitura em Disco de Dados/Seg|Operações de Leitura de Disco de Dados/Seg (Pré-visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitorização|LUN, VMName|
|Operações de Escrita em Disco de Dados/Seg|Operações de Escrita de Disco de Dados/Seg (Pré-visualização)|CountPerSecond|Média|Escreva IOPS de um único disco durante o período de monitorização|LUN, VMName|
|Profundidade da Fila do Disco de Dados|Profundidade da Fila do Discos de Dados (Pré-visualização)|Count|Média|Profundidade da Fila do Disco de Dados (ou Comprimento da Fila)|LUN, VMName|
|Bytes/seg da Leitura de Discos do SO|Bytes de Leitura de Disco de SO/Seg (Pré-visualização)|CountPerSecond|Média|Bytes/Seg lidos a partir de um único disco durante o período de monitorização para o disco do SO|VMName|
|Bytes/sec da Escrita de Discos do SO|Bytes de Escrita de Disco de SO/Seg (Pré-visualização)|CountPerSecond|Média|Bytes/Seg escritos para um único disco durante o período de monitorização para o disco do SO|VMName|
|Operações de Leitura de Disco de SO/Seg.|Operações de Leitura de Disco de SO/Seg (Pré-visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitorização para o disco do SO|VMName|
|Operações de Escrita de Disco de SO/Seg|Operações de Escrita de Disco de SO/Seg (Pré-visualização)|CountPerSecond|Média|Escreva IOPS de um único disco durante o período de monitorização para o disco do SO|VMName|
|Profundidade da Fila do Disco do SO|Profundidade da Fila do Disco do SO (Pré-visualização)|Count|Média|Profundidade da Fila de Disco de Sistema Operativo (ou Comprimento da Fila)|VMName|
|Fluxos de Entrada|Fluxos de Entrada (Pré-visualização)|Count|Média|Os Fluxos de Entrada são o número de fluxos atuais na direção de entrada (tráfego a entrar da VM)|VMName|
|Fluxos de Saída|Fluxos de Saída (Pré-visualização)|Count|Média|Os Fluxos de Saída são o número de fluxos atuais na direção de saída (tráfego a sair da VM)|VMName|
|Taxa de Criação Máxima de Fluxos de Entrada|Taxa de Criação Máxima de Fluxos de Entrada (Pré-visualização)|CountPerSecond|Média|A taxa de criação máxima de fluxos de entrada (tráfego que vai para a VM)|VMName|
|Taxa de Criação Máxima de Fluxos de Saída|Taxa de Criação Máxima de Fluxos de Saída (Pré-visualização)|CountPerSecond|Média|A taxa de criação máxima de fluxos de saída (tráfego que sai da VM)|VMName|
|Acerto de Leitura na Cache do Disco de Dados Premium|Acerto de Leitura na Cache do Disco de Dados Premium (Pré-visualização)|Percent|Média|Acerto de Leitura na Cache do Disco de Dados Premium|LUN, VMName|
|Falha de Acerto de Leitura na Cache do Disco de Dados Premium|Falha de Acerto de Leitura na Cache do Disco de Dados Premium (Pré-visualização)|Percent|Média|Falha de Acerto de Leitura na Cache do Disco de Dados Premium|LUN, VMName|
|Acerto de Leitura na Cache do Disco do SO Premium|Acerto de Leitura na Cache do Disco do SO Premium (Pré-visualização)|Percent|Média|Acerto de Leitura na Cache do Disco do SO Premium|VMName|
|Falha de Acerto de Leitura na Cache do Disco do SO Premium|Falha de Acerto de Leitura na Cache do Disco do SO Premium (Pré-visualização)|Percent|Média|Falha de Acerto de Leitura na Cache do Disco do SO Premium|VMName|
|Rede no Total|Rede no Total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela Máquina Virtual ou Máquinas Virtuais (Tráfego de Entrada)|VMName|
|Rede total|Rede total|Bytes|Total|O número de bytes saídos de todas as interfaces de rede pela(s) Máquina(s) Virtual(is) (Tráfego de Saída)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuUsage|Utilização da CPU|Count|Média|Utilização da CPU em todos os núcleos em milinúcleos.|containerName|
|MemoryUsage|Utilização da Memória|Bytes|Média|Utilização da memória total em bytes.|containerName|
|NetworkBytesReceivedPerSecond|Bytes de Rede Recebidos Por Segundo|Bytes|Média|Os bytes de rede recebidos por segundo.|Nenhuma dimensão|
|NetworkBytesTransmittedPerSecond|Bytes de Rede Transmitidos por Segundo|Bytes|Média|Os bytes de rede transmitidos por segundo.|Nenhuma dimensão|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalPullCount|Contagem total de extração|Count|Média|Número de imagem extrai no total|Nenhuma dimensão|
|SuccessfulPullCount|Contagem de solicitação com êxito|Count|Média|Número de pedidos de imagens com êxito|Nenhuma dimensão|
|TotalPushCount|Contagem total de Push|Count|Média|Número de imagem de pushes no total|Nenhuma dimensão|
|SuccessfulPushCount|Contagem de Push com êxito|Count|Média|Número de pushes de imagem com êxito|Nenhuma dimensão|
|RunDuration|A duração da execução|Milissegundos|Total|A duração da execução em milissegundos|Nenhuma dimensão|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Número total de núcleos de cpu disponíveis num cluster gerido|Count|Total|Número total de núcleos de cpu disponíveis num cluster gerido|Nenhuma dimensão|
|kube_node_status_allocatable_memory_bytes|Quantidade total de memória disponível num cluster gerido|Bytes|Total|Quantidade total de memória disponível num cluster gerido|Nenhuma dimensão|
|kube_pod_status_ready|Número de pods no estado pronto|Count|Total|Número de pods no estado pronto|espaço de nomes, pod|
|kube_node_status_condition|Estados para várias condições de nó|Count|Total|Estados para várias condições de nó|condição, estado, status2, nó|
|kube_pod_status_phase|Número de pods por fase|Count|Total|Número de pods por fase|fase, o espaço de nomes, o pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|DCIApiCalls|Chamadas de API de informações de cliente|Count|Total||Nenhuma dimensão|
|DCIMappingImportOperationSuccessfulLines|Mapeamento de operação de importação linhas com êxito|Count|Total||Nenhuma dimensão|
|DCIMappingImportOperationFailedLines|Mapeamento de operação de importação falha linhas|Count|Total||Nenhuma dimensão|
|DCIMappingImportOperationTotalLines|Mapeamento de operação de importação linhas Total|Count|Total||Nenhuma dimensão|
|DCIMappingImportOperationRuntimeInSeconds|Runtime de operação de importação de mapeamento em segundos|Segundos|Total||Nenhuma dimensão|
|DCIOutboundProfileExportSucceeded|Perfil de saída a exportação foi concluída com êxito|Count|Total||Nenhuma dimensão|
|DCIOutboundProfileExportFailed|Falha na exportação de perfil de saída|Count|Total||Nenhuma dimensão|
|DCIOutboundProfileExportDuration|Duração de exportação de perfil de saída|Segundos|Total||Nenhuma dimensão|
|DCIOutboundKpiExportSucceeded|Saída de Kpi de exportação foi concluída com êxito|Count|Total||Nenhuma dimensão|
|DCIOutboundKpiExportFailed|Falha na exportação de Kpi de saída|Count|Total||Nenhuma dimensão|
|DCIOutboundKpiExportDuration|Duração de saída de Kpi de exportação|Segundos|Total||Nenhuma dimensão|
|DCIOutboundKpiExportStarted|Exportação de Kpi de saída iniciada|Segundos|Total||Nenhuma dimensão|
|DCIOutboundKpiRecordCount|Contagem de registo de Kpi de saída|Segundos|Total||Nenhuma dimensão|
|DCIOutboundProfileExportCount|Contagem de exportação de perfil de saída|Segundos|Total||Nenhuma dimensão|
|DCIOutboundInitialProfileExportFailed|Falha na exportação de saída perfil inicial|Segundos|Total||Nenhuma dimensão|
|DCIOutboundInitialProfileExportSucceeded|Exportação de saída perfil inicial foi concluída com êxito|Segundos|Total||Nenhuma dimensão|
|DCIOutboundInitialKpiExportFailed|Falha na exportação de Kpi inicial saída|Segundos|Total||Nenhuma dimensão|
|DCIOutboundInitialKpiExportSucceeded|Saída exportação de Kpi inicial foi concluída com êxito|Segundos|Total||Nenhuma dimensão|
|DCIOutboundInitialProfileExportDurationInSeconds|Duração de exportação de saída perfil inicial em segundos|Segundos|Total||Nenhuma dimensão|
|AdlaJobForStandardKpiFailed|Tarefa do Adla para Kpi padrão falhada em segundos|Segundos|Total||Nenhuma dimensão|
|AdlaJobForStandardKpiTimeOut|Tarefa do Adla para padrão de Kpi de tempo limite em segundos|Segundos|Total||Nenhuma dimensão|
|AdlaJobForStandardKpiCompleted|Tarefa do Adla para Kpi padrão foi concluída em segundos|Segundos|Total||Nenhuma dimensão|
|ImportASAValuesFailed|Contagem de falha na importação ASA valores|Count|Total||Nenhuma dimensão|
|ImportASAValuesSucceeded|Valores ASA de importação foi concluída com êxito contagem|Count|Total||Nenhuma dimensão|
|DCIProfilesCount|Contagem de instâncias de perfil|Count|Último||Nenhuma dimensão|
|DCIInteractionsPerMonthCount|Interações por mês contagem|Count|Último||Nenhuma dimensão|
|DCIKpisCount|Contagem de KPI|Count|Último||Nenhuma dimensão|
|DCISegmentsCount|Contagem de Segmentos|Count|Último||Nenhuma dimensão|
|DCIPredictiveMatchPoliciesCount|Contagem de correspondência preditiva|Count|Último||Nenhuma dimensão|
|DCIPredictionsCount|Contagem de predição|Count|Último||Nenhuma dimensão|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|NICReadThroughput|Débito de Leitura (Rede)|BytesPerSecond|Média|O débito de leitura da interface de rede no dispositivo o período de relatório para todos os volumes no gateway.|InstanceName|
|NICWriteThroughput|Escrever Débito (Rede)|BytesPerSecond|Média|O débito de escrita da interface de rede no dispositivo o período de relatório para todos os volumes no gateway.|InstanceName|
|CloudReadThroughputPerShare|Débito de Transferência na Cloud (Partilha)|BytesPerSecond|Média|O débito de transferência para o Azure de um compartilhamento durante o período de relatório.|Partilhar|
|CloudUploadThroughputPerShare|Débito de Carregamento na Cloud (Partilha)|BytesPerSecond|Média|O débito de carregamento para o Azure de um compartilhamento durante o período de relatório.|Partilhar|
|BytesUploadedToCloudPerShare|Bytes Carregados na Cloud (Partilha)|Bytes|Média|O número total de bytes que é carregado para o Azure a partir de uma partilha durante o período de relatório.|Partilhar|
|Capacidade total da|Capacidade Total|Bytes|Média|Capacidade Total|Nenhuma dimensão|
|AvailableCapacity|Capacidade Disponível|Bytes|Média|A capacidade disponível em bytes durante o período de relatórios.|Nenhuma dimensão|
|CloudUploadThroughput|Débito de Carregamento na Cloud|BytesPerSecond|Média|O cloud débito de carregamento para o Azure durante o período de relatório.|Nenhuma dimensão|
|CloudReadThroughput|Débito de transferência de cloud|BytesPerSecond|Média|O cloud download débito para o Azure durante o período de relatório.|Nenhuma dimensão|
|BytesUploadedToCloud|Bytes Carregados na Cloud (Dispositivo)|Bytes|Média|O número total de bytes que é carregado para o Azure a partir de um dispositivo durante o período de relatório.|Nenhuma dimensão|
|HyperVVirtualProcessorUtilization|Computação Edge - Percentagem da CPU|Percent|Média|Percentagem de utilização da CPU|InstanceName|
|HyperVMemoryUtilization|Computação Edge - Utilização de Memória|Percent|Média|Quantidade de RAM em Utilização|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|FailedRuns|Execuções Falhadas|Count|Total||pipelineName, activityName|
|SuccessfulRuns|Execuções Com Êxito|Count|Total||pipelineName, activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PipelineFailedRuns|Falha de métricas de execuções de pipeline|Count|Total||FailureType, nome|
|PipelineSucceededRuns|Foi efetuada com êxito as métricas de execuções de pipeline|Count|Total||FailureType, nome|
|ActivityFailedRuns|Falha de métricas de execuções de atividade|Count|Total||ActivityType, PipelineName, FailureType, nome|
|ActivitySucceededRuns|Foi efetuada com êxito as métricas de execuções de atividade|Count|Total||ActivityType, PipelineName, FailureType, nome|
|TriggerFailedRuns|Falha de métricas de execuções do acionador|Count|Total||Nome, FailureType|
|TriggerSucceededRuns|Foi efetuada com êxito as métricas de execuções do acionador|Count|Total||Nome, FailureType|
|IntegrationRuntimeCpuPercentage|Utilização de CPU do runtime de integração|Percent|Média||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Memória disponível de runtime de integração|Bytes|Média||IntegrationRuntimeName, NodeName|
|MaxAllowedResourceCount|Contagem máxima de entidades permitidos|Count|Máximo||Nenhuma dimensão|
|MaxAllowedFactorySizeInGbUnits|Um tamanho de fábrica (unidade de GB) de máximo permitido|Count|Máximo||Nenhuma dimensão|
|ResourceCount|Contagem total de entidades|Count|Máximo||Nenhuma dimensão|
|FactorySizeInGbUnits|Tamanho de fábrica total (GB de unidade)|Count|Máximo||Nenhuma dimensão|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|JobEndedSuccess|Tarefas concluídas com êxito|Count|Total|Contagem de tarefas concluídas com êxito.|Nenhuma dimensão|
|JobEndedFailure|Tarefas falhadas|Count|Total|Contagem de tarefas com falhas.|Nenhuma dimensão|
|JobEndedCancelled|Tarefas canceladas|Count|Total|Contagem de tarefas foi canceladas.|Nenhuma dimensão|
|JobAUEndedSuccess|Tempo de AU com êxito|Segundos|Total|Tempo total de AU para tarefas concluídas com êxito.|Nenhuma dimensão|
|JobAUEndedFailure|Tempo de AU falhadas|Segundos|Total|Tempo total de AU para tarefas com falhas.|Nenhuma dimensão|
|JobAUEndedCancelled|Tempo de AU canceladas|Segundos|Total|Tempo total de AU para as tarefas canceladas.|Nenhuma dimensão|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalStorage|Armazenamento Total|Bytes|Máximo|Quantidade total de dados armazenados na conta.|Nenhuma dimensão|
|DataWritten|Dados Escritos|Bytes|Total|Quantidade total de dados escritos para a conta.|Nenhuma dimensão|
|DataRead|Leitura de dados|Bytes|Total|Quantidade total de dados de leitura da conta.|Nenhuma dimensão|
|WriteRequests|Pedidos de escrita|Count|Total|Contagem de dados de pedidos de escrita para a conta.|Nenhuma dimensão|
|ReadRequests|Pedidos de leitura|Count|Total|Contagem de dados de leitura pedidos para a conta.|Nenhuma dimensão|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentagem de CPU|Percent|Média|Percentagem de CPU|Nenhuma dimensão|
|memory_percent|Percentagem de memória|Percent|Média|Percentagem de memória|Nenhuma dimensão|
|io_consumption_percent|Percentagem de e/s|Percent|Média|Percentagem de e/s|Nenhuma dimensão|
|storage_percent|Percentagem de armazenamento|Percent|Média|Percentagem de armazenamento|Nenhuma dimensão|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhuma dimensão|
|storage_limit|Limite de armazenamento|Bytes|Média|Limite de armazenamento|Nenhuma dimensão|
|serverlog_storage_percent|Percentagem de armazenamento de registo do servidor|Percent|Média|Percentagem de armazenamento de registo do servidor|Nenhuma dimensão|
|serverlog_storage_usage|Armazenamento de registo de servidor utilizado|Bytes|Média|Armazenamento de registo de servidor utilizado|Nenhuma dimensão|
|serverlog_storage_limit|Limite de armazenamento de registo do servidor|Bytes|Média|Limite de armazenamento de registo do servidor|Nenhuma dimensão|
|active_connections|Ligações ativas|Count|Média|Ligações ativas|Nenhuma dimensão|
|connections_failed|Ligações com Falhas|Count|Total|Ligações com Falhas|Nenhuma dimensão|
|seconds_behind_master|Atraso de replicação em segundos|Count|Média|Atraso de replicação em segundos|Nenhuma dimensão|
|backup_storage_used|Armazenamento de cópia de segurança utilizado|Bytes|Média|Armazenamento de cópia de segurança utilizado|Nenhuma dimensão|
|network_bytes_egress|Saída de Rede|Bytes|Total|Rede horizontalmente, através de ligações de Active Directory|Nenhuma dimensão|
|network_bytes_ingress|Entrada de Rede|Bytes|Total|Na rede através de ligações de Active Directory|Nenhuma dimensão|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentagem de CPU|Percent|Média|Percentagem de CPU|Nenhuma dimensão|
|memory_percent|Percentagem de memória|Percent|Média|Percentagem de memória|Nenhuma dimensão|
|io_consumption_percent|Percentagem de e/s|Percent|Média|Percentagem de e/s|Nenhuma dimensão|
|storage_percent|Percentagem de armazenamento|Percent|Média|Percentagem de armazenamento|Nenhuma dimensão|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhuma dimensão|
|storage_limit|Limite de armazenamento|Bytes|Média|Limite de armazenamento|Nenhuma dimensão|
|serverlog_storage_percent|Percentagem de armazenamento de registo do servidor|Percent|Média|Percentagem de armazenamento de registo do servidor|Nenhuma dimensão|
|serverlog_storage_usage|Armazenamento de registo de servidor utilizado|Bytes|Média|Armazenamento de registo de servidor utilizado|Nenhuma dimensão|
|serverlog_storage_limit|Limite de armazenamento de registo do servidor|Bytes|Média|Limite de armazenamento de registo do servidor|Nenhuma dimensão|
|active_connections|Ligações ativas|Count|Média|Ligações ativas|Nenhuma dimensão|
|connections_failed|Ligações com Falhas|Count|Total|Ligações com Falhas|Nenhuma dimensão|
|seconds_behind_master|Atraso de replicação em segundos|Count|Média|Atraso de replicação em segundos|Nenhuma dimensão|
|backup_storage_used|Armazenamento de cópia de segurança utilizado|Bytes|Média|Armazenamento de cópia de segurança utilizado|Nenhuma dimensão|
|network_bytes_egress|Saída de Rede|Bytes|Total|Rede horizontalmente, através de ligações de Active Directory|Nenhuma dimensão|
|network_bytes_ingress|Entrada de Rede|Bytes|Total|Na rede através de ligações de Active Directory|Nenhuma dimensão|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentagem de CPU|Percent|Média|Percentagem de CPU|Nenhuma dimensão|
|memory_percent|Percentagem de memória|Percent|Média|Percentagem de memória|Nenhuma dimensão|
|io_consumption_percent|Percentagem de e/s|Percent|Média|Percentagem de e/s|Nenhuma dimensão|
|storage_percent|Percentagem de armazenamento|Percent|Média|Percentagem de armazenamento|Nenhuma dimensão|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhuma dimensão|
|storage_limit|Limite de armazenamento|Bytes|Média|Limite de armazenamento|Nenhuma dimensão|
|serverlog_storage_percent|Percentagem de armazenamento de registo do servidor|Percent|Média|Percentagem de armazenamento de registo do servidor|Nenhuma dimensão|
|serverlog_storage_usage|Armazenamento de registo de servidor utilizado|Bytes|Média|Armazenamento de registo de servidor utilizado|Nenhuma dimensão|
|serverlog_storage_limit|Limite de armazenamento de registo do servidor|Bytes|Média|Limite de armazenamento de registo do servidor|Nenhuma dimensão|
|active_connections|Ligações ativas|Count|Média|Ligações ativas|Nenhuma dimensão|
|connections_failed|Ligações com Falhas|Count|Total|Ligações com Falhas|Nenhuma dimensão|
|backup_storage_used|Armazenamento de cópia de segurança utilizado|Bytes|Média|Armazenamento de cópia de segurança utilizado|Nenhuma dimensão|
|network_bytes_egress|Saída de Rede|Bytes|Total|Rede horizontalmente, através de ligações de Active Directory|Nenhuma dimensão|
|network_bytes_ingress|Entrada de Rede|Bytes|Total|Na rede através de ligações de Active Directory|Nenhuma dimensão|
|pg_replica_log_delay_in_seconds|Atraso de réplica|Segundos|Máximo|Atraso de réplica em segundos|Nenhuma dimensão|
|pg_replica_log_delay_in_bytes|Atraso máximo entre as réplicas|Bytes|Máximo|Atraso em bytes do mais lagging réplica|Nenhuma dimensão|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentagem de CPU|Percent|Média|Percentagem de CPU|Nenhuma dimensão|
|memory_percent|Percentagem de memória|Percent|Média|Percentagem de memória|Nenhuma dimensão|
|iops|IOPS|Count|Média|Operações de e/s por segundo|Nenhuma dimensão|
|storage_percent|Percentagem de armazenamento|Percent|Média|Percentagem de armazenamento|Nenhuma dimensão|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhuma dimensão|
|active_connections|Ligações ativas|Count|Média|Ligações ativas|Nenhuma dimensão|
|network_bytes_egress|Saída de Rede|Bytes|Total|Rede horizontalmente, através de ligações de Active Directory|Nenhuma dimensão|
|network_bytes_ingress|Entrada de Rede|Bytes|Total|Na rede através de ligações de Active Directory|Nenhuma dimensão|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentativas de envio de mensagens de telemetria|Count|Total|Número de mensagens de telemetria do dispositivo-para-cloud tentada a serem enviados ao seu hub IoT|Nenhuma dimensão|
|d2c.telemetry.ingress.success|Mensagens de telemetria enviadas|Count|Total|Número de mensagens de telemetria do dispositivo-para-cloud enviado com êxito ao seu hub IoT|Nenhuma dimensão|
|c2d.commands.egress.complete.success|Comandos concluídos|Count|Total|Número de comandos do cloud-para-dispositivo foi concluída com êxito pelo dispositivo|Nenhuma dimensão|
|c2d.commands.egress.abandon.success|Comandos abandonados|Count|Total|Número de comandos do cloud-para-dispositivo abandonadas pelo dispositivo|Nenhuma dimensão|
|c2d.commands.egress.reject.success|Comandos rejeitados|Count|Total|Número de comandos do cloud-para-dispositivo rejeitado pelo dispositivo|Nenhuma dimensão|
|devices.totalDevices|Total de dispositivos (preterido)|Count|Total|Número de dispositivos registados no seu hub IoT|Nenhuma dimensão|
|devices.connectedDevices.allProtocol|Dispositivos ligados (preteridos) |Count|Total|Número de dispositivos ligados ao seu hub IoT|Nenhuma dimensão|
|d2c.telemetry.egress.success|Encaminhamento: mensagens de telemetria entregues|Count|Total|O número de vezes que as mensagens foram entregues com êxito a todos os pontos finais através do IoT Hub encaminhamento. Se uma mensagem é encaminhada para vários pontos de extremidade, esse valor aumenta um para cada entrega concluída com êxito. Se uma mensagem é enviada para o mesmo ponto final várias vezes, esse valor aumenta um para cada entrega concluída com êxito.|Nenhuma dimensão|
|d2c.telemetry.egress.dropped|Encaminhamento: mensagens de telemetria removidas |Count|Total|O número de vezes que as mensagens foram removidas pelo IoT Hub encaminhamento devido a pontos de extremidade inativos. Este valor não conta mensagens entregues a rota de contingência, como mensagens de ignorados não são entregues lá.|Nenhuma dimensão|
|d2c.telemetry.egress.orphaned|Encaminhamento: mensagens de telemetria órfãos |Count|Total|O número de vezes que as mensagens foram órfão pelo encaminhamento do IoT Hub porque eles não correspondam a quaisquer regras de encaminhamento (incluindo a regra de contingência). |Nenhuma dimensão|
|d2c.telemetry.egress.invalid|Encaminhamento: mensagens de telemetria incompatíveis|Count|Total|O número de vezes que o IoT Hub encaminhamento não foi possível entregar mensagens devido a uma incompatibilidade com o ponto final. Este valor não inclui tentativas.|Nenhuma dimensão|
|d2c.telemetry.egress.fallback|Encaminhamento: mensagens entregues para contingência|Count|Total|O número de vezes que o IoT Hub encaminhamento entregar mensagens para o ponto final associado a rota de contingência.|Nenhuma dimensão|
|d2c.endpoints.egress.eventHubs|Encaminhamento: mensagens entregues ao Hub de eventos|Count|Total|O número de vezes que o IoT Hub com êxito de encaminhamento entregar mensagens para pontos finais do Hub de eventos.|Nenhuma dimensão|
|d2c.endpoints.latency.eventHubs|Encaminhamento: latência da mensagem para o Hub de eventos|Milissegundos|Média|A latência média (milissegundos) entre a receção de mensagem para o IoT Hub e entrada de mensagem num ponto de extremidade do Hub de eventos.|Nenhuma dimensão|
|d2c.endpoints.egress.serviceBusQueues|Encaminhamento: mensagens entregues à fila do Service Bus|Count|Total|O número de vezes que o IoT Hub com êxito de encaminhamento entregar mensagens para pontos finais de fila do Service Bus.|Nenhuma dimensão|
|d2c.endpoints.latency.serviceBusQueues|Encaminhamento: latência de mensagem de fila do Service Bus|Milissegundos|Média|A latência média (milissegundos) entre a receção de mensagem para o IoT Hub e entrada de mensagem de telemetria para um ponto de extremidade de fila do Service Bus.|Nenhuma dimensão|
|d2c.endpoints.egress.serviceBusTopics|Encaminhamento: mensagens entregues ao tópico do Service Bus|Count|Total|O número de vezes encaminhamento com êxito o IoT Hub entregar mensagens para pontos finais de tópico do Service Bus.|Nenhuma dimensão|
|d2c.endpoints.latency.serviceBusTopics|Encaminhamento: latência da mensagem para o tópico do Service Bus|Milissegundos|Média|A latência média (milissegundos) entre a receção de mensagem para o IoT Hub e entrada de mensagem de telemetria para um ponto de extremidade do tópico do Service Bus.|Nenhuma dimensão|
|d2c.endpoints.egress.builtIn.events|Encaminhamento: mensagens entregues para mensagens/eventos|Count|Total|O número de vezes que o IoT Hub com êxito de encaminhamento entregar mensagens para o ponto final incorporado (mensagens/eventos).|Nenhuma dimensão|
|d2c.endpoints.latency.builtIn.events|Encaminhamento: latência da mensagem para mensagens/eventos|Milissegundos|Média|A latência média (milissegundos) entre a receção de mensagem para o IoT Hub e entrada de mensagem de telemetria para o ponto final incorporado (mensagens/eventos).|Nenhuma dimensão|
|d2c.endpoints.egress.storage|Encaminhamento: mensagens entregues para o armazenamento|Count|Total|O número de vezes encaminhamento com êxito o IoT Hub entregar mensagens para pontos finais de armazenamento.|Nenhuma dimensão|
|d2c.endpoints.latency.storage|Encaminhamento: latência da mensagem para armazenamento|Milissegundos|Média|A latência média (milissegundos) entre a receção de mensagem para o IoT Hub e entrada de mensagem de telemetria para um ponto de final de armazenamento.|Nenhuma dimensão|
|d2c.endpoints.egress.storage.bytes|Encaminhamento: dados entregues para o armazenamento|Bytes|Total|A quantidade de dados (bytes) IoT Hub encaminhamento entregues aos pontos finais de armazenamento.|Nenhuma dimensão|
|d2c.endpoints.egress.storage.blobs|Encaminhamento: blobs é entregue ao armazenamento|Count|Total|O número de vezes que o IoT Hub encaminhamento entregue blobs para pontos finais de armazenamento.|Nenhuma dimensão|
|EventGridDeliveries|Entregas de grelha de eventos (pré-visualização)|Count|Total|O número de pedidos para o IoT Hub emitir eventos para o Event Grid. Este número inclui pedidos com êxito ou falhados. Utilize a dimensão de resultado para o número de tipos de resposta diferente. Para ver de onde os pedidos são provenientes, utilize a dimensão de EventType.|Resultado, EventType|
|EventGridLatency|Latência de grelha de eventos (pré-visualização)|Milissegundos|Média|A latência média (milissegundos) entre a receção de eventos para o IoT Hub e entrada de eventos no Event Grid. Este número é uma média entre todos os tipos de eventos. Utilize a dimensão de EventType para ver a latência de um tipo específico de evento.|EventType|
|d2c.twin.read.success|Lê a partir do dispositivos duplos com êxito|Count|Total|Contagem de leituras de iniciada pelo dispositivo duplo tudo com êxito.|Nenhuma dimensão|
|d2c.twin.read.failure|Falha de leituras de twin de dispositivos|Count|Total|A contagem de todos os falha leituras iniciada pelo dispositivo duplo.|Nenhuma dimensão|
|d2c.twin.read.size|Tamanho de resposta de leituras de twin de dispositivos|Bytes|Média|A média, Mín e máx. de tudo com êxito iniciada pelo dispositivo duplo leituras.|Nenhuma dimensão|
|d2c.twin.update.success|Atualizações de duplo com êxito a partir de dispositivos|Count|Total|Contagem de todos os com êxito atualizações de iniciada pelo dispositivo duplo.|Nenhuma dimensão|
|d2c.twin.update.failure|Falha de atualizações de twin de dispositivos|Count|Total|A contagem de todos os falha atualizações iniciadas pelo dispositivo duplo.|Nenhuma dimensão|
|d2c.twin.update.size|Tamanho de atualizações de twin de dispositivos|Bytes|Média|A média, Mín e tamanho máximo de todos os bem-sucedida iniciada pelo dispositivo duplo atualizações.|Nenhuma dimensão|
|c2d.methods.success|Invocações de método direto com êxito|Count|Total|Contagem de chamadas de método direto tudo com êxito.|Nenhuma dimensão|
|c2d.methods.failure|Falha de invocações de método direto|Count|Total|A contagem de todos os falha chamadas de método direto.|Nenhuma dimensão|
|c2d.methods.requestSize|Tamanho do pedido de invocações de método direto|Bytes|Média|A média, Mín e máx. de bem-sucedida de todos os pedidos de método direto.|Nenhuma dimensão|
|c2d.methods.responseSize|Tamanho de resposta de invocações de método direto|Bytes|Média|A média, Mín e máx. de respostas de método direto tudo com êxito.|Nenhuma dimensão|
|c2d.twin.read.success|Lê a partir do back-end duplo com êxito|Count|Total|Contagem de leituras de back-end-iniciada duplo tudo com êxito.|Nenhuma dimensão|
|c2d.twin.read.failure|Leituras de duplo com falha de back-end|Count|Total|A contagem de todos os falha leituras duplo iniciada pelo back-end.|Nenhuma dimensão|
|c2d.twin.read.size|Tamanho de resposta do duplo lê a partir do back-end|Bytes|Média|A média, Mín e máx. de todos os bem-sucedida iniciada pelo back-end duplo leituras.|Nenhuma dimensão|
|c2d.twin.update.success|Duplo com êxito as atualizações a partir de back-end|Count|Total|Contagem de todos os com êxito atualizações de duplo iniciada pelo back-end.|Nenhuma dimensão|
|c2d.twin.update.failure|Atualizações de duplo com falha de back-end|Count|Total|A contagem de todos os falha atualizações duplo iniciada pelo back-end.|Nenhuma dimensão|
|c2d.twin.update.size|Tamanho de atualizações de duplo de back-end|Bytes|Média|A média, Mín e tamanho máximo de todos os bem-sucedida iniciada pelo back-end duplo atualizações.|Nenhuma dimensão|
|twinQueries.success|Consultas de gémeos com êxito|Count|Total|Contagem de todas as consultas de gémeos com êxito.|Nenhuma dimensão|
|twinQueries.failure|Consultas de falhados duplo|Count|Total|Contagem de todas as consultas de falhados duplo.|Nenhuma dimensão|
|twinQueries.resultSize|Tamanho de resultados de consultas de gémeos|Bytes|Média|A média, Mín e máx. do tamanho do resultado de todas as consultas de gémeos com êxito.|Nenhuma dimensão|
|jobs.createTwinUpdateJob.success|Criações com êxito das tarefas de atualização de duplo|Count|Total|Contagem de criação com êxito todas as tarefas de atualização de duplo.|Nenhuma dimensão|
|jobs.createTwinUpdateJob.failure|Criações com falhas de tarefas de atualização de duplo|Count|Total|Contagem de todos os falha na criação de tarefas de atualização de duplo.|Nenhuma dimensão|
|jobs.createDirectMethodJob.success|Criações com êxito das tarefas de invocação de método|Count|Total|Contagem de criação com êxito todas as tarefas de invocação do método direto.|Nenhuma dimensão|
|jobs.createDirectMethodJob.failure|Criações com falhas de tarefas de invocação de método|Count|Total|Contagem de todos os falha na criação de tarefas de invocação do método direto.|Nenhuma dimensão|
|jobs.listJobs.success|Chamadas com êxito à lista de tarefas|Count|Total|Contagem de todas as chamadas com êxito à lista de tarefas.|Nenhuma dimensão|
|jobs.listJobs.failure|Falha de chamadas para a lista de tarefas|Count|Total|Contagem de todas as chamadas falhadas para lista de tarefas.|Nenhuma dimensão|
|jobs.cancelJob.success|Cancelamentos de tarefas com êxito|Count|Total|Contagem de todas as chamadas com êxito para cancelar uma tarefa.|Nenhuma dimensão|
|jobs.cancelJob.failure|Cancelamentos de tarefas com falhas|Count|Total|Contagem de todas as chamadas falhadas para cancelar uma tarefa.|Nenhuma dimensão|
|jobs.queryJobs.success|Consultas de tarefa concluída com êxito|Count|Total|Contagem de todas as chamadas com êxito para tarefas de consulta.|Nenhuma dimensão|
|jobs.queryJobs.failure|Consultas de tarefa falhadas|Count|Total|Contagem de todas as chamadas falhadas para tarefas de consulta.|Nenhuma dimensão|
|jobs.completed|Trabalhos concluídos|Count|Total|Contagem de todas as tarefas concluídas.|Nenhuma dimensão|
|jobs.failed|Tarefas falhadas|Count|Total|Contagem de todas as tarefas falhadas.|Nenhuma dimensão|
|d2c.telemetry.ingress.sendThrottle|Número de erros de limitação|Count|Total|Limita o número de erros de limitação devido ao débito de dispositivo|Nenhuma dimensão|
|dailyMessageQuotaUsed|Número total de mensagens utilizada|Count|Média|Número de total de mensagens usados hoje em dia. Este é um valor cumulativo que é reposto a zero 00:00 UTC todos os dias.|Nenhuma dimensão|
|deviceDataUsage|Utilização de dados do total de dispositivos|Bytes|Total|Bytes transferidos de e para quaisquer dispositivos ligados ao IotHub|Nenhuma dimensão|
|totalDeviceCount|Total de dispositivos (pré-visualização)|Count|Média|Número de dispositivos registados no seu hub IoT|Nenhuma dimensão|
|connectedDeviceCount|Dispositivos ligados (pré-visualização)|Count|Média|Número de dispositivos ligados ao seu hub IoT|Nenhuma dimensão|
|Configurações|Métricas de configuração|Count|Total|Métricas para operações de configuração|Nenhuma dimensão|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RegistrationAttempts|Tentativas de registo|Count|Total|Número de tentativa de registos de dispositivos|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Dispositivos atribuídos|Count|Total|Número de dispositivos atribuídos a um hub IoT|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Tentativas de atestado|Count|Total|Número de atestados dispositivo tentada|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AvailableStorage|Armazenamento disponível|Bytes|Total|Total de armazenamento disponível reportados na granularidade de 5 minutos|Região de CollectionName, DatabaseName,|
|CassandraConnectionClosures|Fechamentos de ligação de Cassandra|Count|Total|Número de ligações de Cassandra que foram fechados, comunicado com uma granularidade de 1 minuto|Região, ClosureReason|
|CassandraRequestCharges|Custos de pedido de Cassandra|Count|Total|RUs consumidas para pedidos de Cassandra efetuados|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Pedidos de Cassandra|Count|Count|Número de pedidos de Cassandra efetuados|DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|DataUsage|Utilização de Dados|Bytes|Total|Utilização total de dados reportada na granularidade de 5 minutos|Região de CollectionName, DatabaseName,|
|DocumentCount|Contagem de Documentos|Count|Total|Contagem de documentos total comunicados na granularidade de 5 minutos|Região de CollectionName, DatabaseName,|
|DocumentQuota|Quota de documento|Bytes|Total|Quota de armazenamento total comunicado na granularidade de 5 minutos|Região de CollectionName, DatabaseName,|
|IndexUsage|Uso do índice|Bytes|Total|Uso do índice total comunicado na granularidade de 5 minutos|Região de CollectionName, DatabaseName,|
|MetadataRequests|Pedidos de metadados|Count|Count|Contagem de pedidos de metadados. O cosmos DB mantém a coleção de metadados do sistema para cada conta, que permite que enumere coleções, bases de dados, etc, e suas configurações, sem encargos.|DatabaseName, CollectionName, Region, StatusCode, |
|MongoRequestCharge|Encargos de pedidos do mongo|Count|Total|Unidades de pedido de mongo consumidas|DatabaseName, CollectionName, região, CommandName, código de erro|
|MongoRequests|Pedidos de mongo|Count|Count|Número de pedidos de Mongo efetuados|DatabaseName, CollectionName, região, CommandName, código de erro|
|ProvisionedThroughput|Débito Aprovisionado|Count|Máximo|Débito Aprovisionado|DatabaseName, CollectionName|
|ReplicationLatency|P99 Latência de replicação|Milissegundos|Média|P99 a latência de replicação entre regiões de origem e de destino para a conta geo-ativada|SourceRegion, TargetRegion|
|ServiceAvailability|Disponibilidade do serviço|Percent|Média|Disponibilidade de pedidos de conta na granularidade de uma hora, dia ou mês|Nenhuma dimensão|
|TotalRequestUnits|Unidades de pedido total|Count|Total|Solicitar que unidades consumidas|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|TotalRequests|Total de Pedidos|Count|Count|Número de pedidos efetuados|DatabaseName, CollectionName, Region, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|Count|Total|Total de eventos publicados neste tópico|Nenhuma dimensão|
|PublishFailCount|Publicar eventos de falha|Count|Total|Falha ao publicar para este tópico de total de eventos|ErrorType, Error|
|UnmatchedEventCount|Eventos sem correspondência|Count|Total|Total de eventos não corresponde a qualquer uma das subscrições de eventos para este tópico|Nenhuma dimensão|
|PublishSuccessLatencyInMs|Publicar a latência com êxito|Count|Total|Publicar a latência com êxito em milissegundos|Nenhuma dimensão|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|MatchedEventCount|Eventos de Correspondência|Count|Total|Total de eventos correspondida a esta subscrição de evento|Nenhuma dimensão|
|DeliveryAttemptFailCount|Eventos de falha de entrega|Count|Total|Total de eventos não foi possível entregar a esta subscrição de evento|Error, ErrorType|
|DeliverySuccessCount|Eventos fornecidos|Count|Total|Total de eventos fornecidos a esta subscrição de evento|Nenhuma dimensão|
|DestinationProcessingDurationInMs|Duração de processamento de destino|Milissegundos|Média|Duração de processamento de destino em milissegundos|Nenhuma dimensão|
|DroppedEventCount|Eventos ignorados|Count|Total|Total de eventos ignorados correspondentes a esta subscrição de evento|DropReason|
|DeadLetteredCount|Eventos entregues|Count|Total|Total de eventos entregues correspondentes a esta subscrição de evento|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|Count|Total|Total de eventos publicados neste tópico|Nenhuma dimensão|
|PublishFailCount|Eventos de falha|Count|Total|Falha ao publicar para este tópico de total de eventos|ErrorType, Error|
|UnmatchedEventCount|Eventos sem correspondência|Count|Total|Total de eventos não corresponde a qualquer uma das subscrições de eventos para este tópico|Nenhuma dimensão|
|PublishSuccessLatencyInMs|Publicar a latência com êxito|Count|Total|Publicar a latência com êxito em milissegundos|Nenhuma dimensão|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfulRequests|Pedidos com Êxito|Count|Total|Pedidos Com Êxito do Microsoft.EventHub.|EntityName, |
|ServerErrors|Erros de Servidor.|Count|Total|Erros de Servidor do Microsoft.EventHub.|EntityName, |
|UserErrors|Erros do Utilizador.|Count|Total|Erros de Utilizador do Microsoft.EventHub.|EntityName, |
|QuotaExceededErrors|Erros de Exceção de Quota.|Count|Total|Erros de Quota Excedida do Microsoft.EventHub.|EntityName, |
|ThrottledRequests|Pedidos Limitados.|Count|Total|Pedidos Limitados do Microsoft.EventHub.|EntityName, |
|IncomingRequests|Pedidos Recebidos|Count|Total|Pedidos Recebidos do Microsoft.EventHub.|EntityName|
|IncomingMessages|Mensagens Recebidas|Count|Total|Mensagens Recebidas do Microsoft.EventHub.|EntityName|
|OutgoingMessages|Mensagens Enviadas|Count|Total|Mensagens Enviadas do Microsoft.EventHub.|EntityName|
|IncomingBytes|Bytes Recebidos.|Bytes|Total|Bytes Recebidos do Microsoft.EventHub.|EntityName|
|OutgoingBytes|Bytes Enviados.|Bytes|Total|Bytes Enviados do Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Count|Média|Total de Ligações Ativas do Microsoft.EventHub.|Nenhuma dimensão|
|ConnectionsOpened|Ligações Abertas.|Count|Média|Ligações Abertas do Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Ligações Fechadas.|Count|Média|Ligações Fechadas do Microsoft.EventHub.|EntityName|
|CaptureBacklog|Registo de tarefas pendentes de Recolha.|Count|Total|Registo de tarefas pendentes de Recolha do Microsoft.EventHub.|EntityName|
|CapturedMessages|Mensagens Recolhidas.|Count|Total|Mensagens Recolhidas do Microsoft.EventHub.|EntityName|
|CapturedBytes|Bytes Recolhidos.|Bytes|Total|Bytes Recolhidos do Microsoft.EventHub.|EntityName|
|Tamanho|Tamanho|Bytes|Média|Tamanho de um EventHub em Bytes.|EntityName|
|INREQS|Pedidos recebidos (preteridos)|Count|Total|Entrada total enviar pedidos para um espaço de nomes (preterido)|Nenhuma dimensão|
|SUCCREQ|Pedidos com êxito (preteridos)|Count|Total|Total de pedidos com êxito para um espaço de nomes (preterido)|Nenhuma dimensão|
|FAILREQ|Pedidos falhados (preteridos)|Count|Total|Total de pedidos falhados para um espaço de nomes (preterido)|Nenhuma dimensão|
|SVRBSY|Erros de servidor ocupado (preteridos)|Count|Total|Erros de servidor total ocupado para um espaço de nomes (preterido)|Nenhuma dimensão|
|INTERR|Erros de servidor interno (preteridos)|Count|Total|Erros de servidor interno total para um espaço de nomes (preterido)|Nenhuma dimensão|
|MISCERR|Outros erros (preteridos)|Count|Total|Total de pedidos falhados para um espaço de nomes (preterido)|Nenhuma dimensão|
|INMSGS|Mensagens recebidas (preteridas) (preteridas)|Count|Total|Total de mensagens de entrada para um espaço de nomes. Esta métrica é preterida. Em vez disso, utilize a métrica de mensagens recebidas (preteridas)|Nenhuma dimensão|
|EHINMSGS|Mensagens Recebidas (preteridas)|Count|Total|Total de mensagens de entrada para um espaço de nomes (preterido)|Nenhuma dimensão|
|OUTMSGS|Mensagens enviadas (preteridas) (preteridas)|Count|Total|Total de mensagens enviadas para um espaço de nomes. Esta métrica é preterida. Em vez disso, utilize métrica das mensagens enviadas (preteridas)|Nenhuma dimensão|
|EHOUTMSGS|Mensagens Enviadas (preteridas)|Count|Total|Total de mensagens enviadas para um espaço de nomes (preterido)|Nenhuma dimensão|
|EHINMBS|Bytes recebidos (preteridos) (descontinuado)|Bytes|Total|Débito Hub de eventos recebido mensagem para um espaço de nomes. Esta métrica é preterida. Em vez disso, utilize métrica de bytes recebidos (preteridos)|Nenhuma dimensão|
|EHINBYTES|Bytes recebidos (preteridos)|Bytes|Total|Débito de mensagem de entrada de Hub do evento para um espaço de nomes (preterido)|Nenhuma dimensão|
|EHOUTMBS|Bytes enviados (preteridos) (descontinuado)|Bytes|Total|Débito Hub de eventos enviar mensagem para um espaço de nomes. Esta métrica é preterida. Em vez disso, utilize métrica de bytes enviados (preteridos)|Nenhuma dimensão|
|EHOUTBYTES|Bytes Enviados (preteridos)|Bytes|Total|Débito de mensagem de saída de Hub do evento para um espaço de nomes (preterido)|Nenhuma dimensão|
|EHABL|Arquivar mensagens de registo de segurança (preterido)|Count|Total|Mensagens de arquivo do Hub de eventos no registo de segurança para um espaço de nomes (preterido)|Nenhuma dimensão|
|EHAMSGS|Arquivar mensagens (preterido)|Count|Total|Mensagens (preterido) um espaço de nomes de arquivo do Hub de eventos|Nenhuma dimensão|
|EHAMBS|Arquivar débito de mensagens (preterido)|Bytes|Total|Débito de mensagem arquivados do Hub de eventos num espaço de nomes (preterido)|Nenhuma dimensão|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfulRequests|Pedidos com êxito (pré-visualização)|Count|Total|Pedidos Com Êxito do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|ServerErrors|Erros de Servidor. (Pré-visualização)|Count|Total|Erros de Servidor do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|UserErrors|Erros do Utilizador. (Pré-visualização)|Count|Total|Erros de Utilizador do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|QuotaExceededErrors|Erros de Exceção de Quota. (Pré-visualização)|Count|Total|Erros de Quota Excedida do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|ThrottledRequests|Pedidos Limitados. (Pré-visualização)|Count|Total|Pedidos Limitados do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|IncomingRequests|Pedidos de entrada (pré-visualização)|Count|Total|Pedidos Recebidos do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|IncomingMessages|Mensagens de entrada (pré-visualização)|Count|Total|Mensagens Recebidas do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|OutgoingMessages|Mensagens de saída (pré-visualização)|Count|Total|Mensagens Enviadas do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|IncomingBytes|Bytes Recebidos. (Pré-visualização)|Bytes|Total|Bytes Recebidos do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|OutgoingBytes|Bytes Enviados. (Pré-visualização)|Bytes|Total|Bytes Enviados do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|ActiveConnections|ActiveConnections (pré-visualização)|Count|Média|Total de Ligações Ativas do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|ConnectionsOpened|Ligações Abertas. (Pré-visualização)|Count|Média|Ligações Abertas do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|ConnectionsClosed|Ligações Fechadas. (Pré-visualização)|Count|Média|Ligações Fechadas do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|CaptureBacklog|Registo de tarefas pendentes de Recolha. (Pré-visualização)|Count|Total|Registo de tarefas pendentes de Recolha do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|CapturedMessages|Mensagens Recolhidas. (Pré-visualização)|Count|Total|Mensagens Recolhidas do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|CapturedBytes|Bytes Recolhidos. (Pré-visualização)|Bytes|Total|Bytes Recolhidos do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|CPU|CPU (pré-visualização)|Percent|Máximo|Utilização da CPU para o Cluster de Hub de Eventos enquanto percentagem|Função|
|AvailableMemory|Memória disponível (pré-visualização)|Count|Máximo|Memória disponível para o Cluster de Hub de Eventos em bytes|Função|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|GatewayRequests|Pedidos do gateway|Count|Total|Número de pedidos do gateway|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Pedidos do Gateway categorizados|Count|Total|Número de pedidos de gateway por categorias (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Número de Workers Active Directory|Count|Máximo|Número de Workers Active Directory|ClusterDnsName, MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ObservedMetricValue|Valor de Métrica observado|Count|Média|O valor calculado pelo dimensionamento automático quando este foi executado|MetricTriggerSource|
|MetricThreshold|Limiar de Métrica|Count|Média|O limiar de dimensionamento automático configurado quando este foi executado.|MetricTriggerRule|
|ObservedCapacity|Capacidade observada|Count|Média|A capacidade reportada para dimensionamento automático quando este foi executado.|Nenhuma dimensão|
|ScaleActionsInitiated|Ações de Dimensionamento Iniciadas|Count|Total|A direção da operação de dimensionamento.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Pré-visualização pública)

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Disponibilidade|Percentagem|Média|Percentagem de testes de disponibilidade concluída com êxito|availabilityResult/nome, availabilityResult/localização|
|availabilityResults/count|Testes de disponibilidade|Count|Count|Contagem de testes de disponibilidade|availabilityResult/nome, availabilityResult/localização, availabilityResult/com êxito|
|availabilityResults/duration|Duração do teste de disponibilidade|Milissegundos|Média|Duração do teste de disponibilidade|availabilityResult/nome, availabilityResult/localização, availabilityResult/com êxito|
|browserTimings/networkDuration|Tempo de ligação da rede de carregamento de páginas|Milissegundos|Média|Tempo entre a ligação de rede e de pedido do utilizador. Inclui a ligação de transporte e de pesquisa DNS.|Nenhuma dimensão|
|browserTimings/processingDuration|Tempo de processamento do cliente|Milissegundos|Média|Tempo entre a receção do último byte de um documento, até que o DOM ser carregado. Pedidos assíncronos podem continuar a ser processados.|Nenhuma dimensão|
|browserTimings/receiveDuration|Tempo de resposta de receção|Milissegundos|Média|Tempo entre o primeiro e último bytes ou até à interrupção da ligação.|Nenhuma dimensão|
|browserTimings/sendDuration|Tempo de resposta de envio|Milissegundos|Média|Tempo entre a ligação de rede e a receção do primeiro byte.|Nenhuma dimensão|
|browserTimings/totalDuration|Tempo de carregamento de páginas do browser|Milissegundos|Média|Tempo entre o pedido do utilizador e o carregamento de DOM, de folhas de estilos, de scripts e de imagens.|Nenhuma dimensão|
|/ Contagem de dependências|Chamadas de dependência|Count|Count|Número de chamadas efetuadas pela aplicação aos recursos externos.|tipo de dependência /, dependência/performanceBucket, dependência/sucesso, operação/synthetic, na cloud/roleInstance, roleName/na cloud|
|dependencies/duration|Duração da dependência|Milissegundos|Média|Duração das chamadas efetuadas pela aplicação aos recursos externos.|tipo de dependência /, dependência/performanceBucket, dependência/sucesso, operação/synthetic, na cloud/roleInstance, roleName/na cloud|
|dependencies/failed|Falhas de chamada de dependência|Count|Count|Número de chamadas de dependência falhadas efetuadas pela aplicação para os recursos externos.|tipo de dependência /, dependência/performanceBucket, operação/synthetic, na cloud/roleInstance, roleName/na cloud|
|pageViews/count|Visualizações da página|Count|Count|Número de vistas de página.|operation/synthetic|
|pageViews/duration|Duração de carregamento da visualização de página|Milissegundos|Média|Duração de carregamento da visualização de página|operation/synthetic|
|performanceCounters/requestExecutionTime|Tempo de execução do pedido HTTP|Milissegundos|Média|Tempo de execução do pedido mais recente.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Pedidos HTTP na fila de aplicação|Count|Média|Tamanho da fila de pedidos da aplicação.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Taxa de pedido HTTP|CountPerSecond|Média|Classificação de todos os pedidos da aplicação por segundo do ASP.NET.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Taxa de exceção|CountPerSecond|Média|Contagem de exceções processadas e não processadas comunicadas ao Windows, incluindo exceções .NET e exceções não geridas que foram convertidas em exceções .NET.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Velocidade de E/S do processo|BytesPerSecond|Média|Total de bytes por segundo lidos e escritos em ficheiros, na rede e em dispositivos.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|CPU do processo|Percent|Média|A percentagem de tempo decorrido que todos os threads de processos utilizados pelo processador para executar instruções. Isso pode variar entre 0 e 100. Esta métrica indica apenas o desempenho do processo w3wp.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Tempo do processador|Percent|Média|Percentagem de tempo que o processador despende em threads não inativos.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Memória disponível|Bytes|Média|Memória física imediatamente disponível para alocação a um processo ou para utilização do sistema.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Bytes privados do processo|Bytes|Média|Memória atribuída exclusivamente aos processos da aplicação monitorizada.|cloud/roleInstance|
|requests/duration|Tempo de resposta do servidor|Milissegundos|Média|Tempo entre a receção de um pedido HTTP e a conclusão do envio da resposta.|pedido/performanceBucket, pedido/resultCode, operação/synthetic, na cloud/roleInstance, pedido/sucesso, roleName/na cloud|
|solicitações/contagem|Pedidos ao servidor|Count|Count|Número de pedidos HTTP concluídos.|pedido/performanceBucket, pedido/resultCode, operação/synthetic, na cloud/roleInstance, pedido/sucesso, roleName/na cloud|
|requests/failed|Pedidos falhados|Count|Count|Número de pedidos HTTP marcados com falhados. Na maioria dos casos estes são pedidos com um código de resposta > = 400 e não igual a 401.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|taxa de pedidos /|Taxa de pedidos do servidor|CountPerSecond|Média|Taxa de pedidos de servidor por segundo|pedido/performanceBucket, pedido/resultCode, operação/synthetic, na cloud/roleInstance, pedido/sucesso, roleName/na cloud|
|Contagem de exceções /|Exceções|Count|Count|Contagem combinada de todas as exceções não identificadas.|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/browser|Exceções do browser|Count|Count|Número de exceções não identificadas emitidas no browser.|Nenhuma dimensão|
|exceptions/server|Exceções de servidor|Count|Count|Número de exceções não identificadas emitidas na aplicação de servidor.|cloud/roleName, cloud/roleInstance|
|rastreios/contagem|Rastreios|Count|Count|Contagem do documento de rastreio|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServiceApiHit|Total de Cliques na API do Serviço|Count|Count|Número total de cliques na API do serviço|ActivityType, ActivityName|
|ServiceApiLatency|Latência Global da API do Serviço|Milissegundos|Média|Latência global dos pedidos da API do serviço|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Resultados de Api de Serviço Total|Count|Count|Número total de resultados da API do serviço|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ClusterDataCapacityFactor|Utilização da cache|Percent|Média|Nível de utilização no âmbito do cluster|Nenhuma dimensão|
|QueryDuration|Duração de consulta|Milissegundos|Média|Duração dos consultas em segundos|QueryStatus|
|IngestionsLoadFactor|Utilização de ingestão|Percent|Média|Rácio de ranhuras de ingestão utilizado no cluster|Nenhuma dimensão|
|IsEngineAnsweringQuery|Keep Alive de|Count|Média|Verificação da funcionalidade indica que o cluster responderá às consultas|Nenhuma dimensão|
|IngestCommandOriginalSizeInMb|Volume de ingestão (em MB)|Count|Total|Volume global dos dados ingeridos para o cluster (em MB)|Nenhuma dimensão|
|IngestedEventAgeSeconds|Latência de ingestão (em segundos)|Segundos|Média|Tempo de ingestão da origem (por exemplo, mensagem estiver EventHub) para o cluster em segundos|Nenhuma dimensão|
|EventRecievedFromEventHub|Eventos processados (para os Hubs de eventos)|Count|Total|Número de eventos processados pelo cluster, ao ingerir do Hub de eventos|Nenhuma dimensão|
|IngestionResult|Resultado de ingestão|Count|Count|Número de operações de ingestão|IngestionResultDetails|
|EngineCPU|CPU|Percent|Média|Nível de utilização da CPU|Nenhuma dimensão|

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Utilização|Utilização|Count|Count|Contagem de chamadas|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RunsStarted|Execuções Iniciadas|Count|Total|Número de execuções de fluxo de trabalho iniciadas.|Nenhuma dimensão|
|RunsCompleted|Execuções Concluídas|Count|Total|Número de execuções de fluxo de trabalho concluídas.|Nenhuma dimensão|
|RunsSucceeded|Execuções com Êxito|Count|Total|Número de execuções de fluxo de trabalho com êxito.|Nenhuma dimensão|
|RunsFailed|Execuções Falhadas|Count|Total|Número de execuções de fluxo de trabalho falhadas.|Nenhuma dimensão|
|RunsCancelled|Execuções canceladas|Count|Total|Número de fluxo de trabalho é executado foi cancelado.|Nenhuma dimensão|
|RunLatency|Latência de Execução|Segundos|Média|Latência de execuções de fluxo de trabalho concluídas.|Nenhuma dimensão|
|RunSuccessLatency|Latência de Execução com Êxito|Segundos|Média|Latência de execuções de fluxo de trabalho com êxito.|Nenhuma dimensão|
|RunThrottledEvents|Eventos Limitados em relação à Execução|Count|Total|Número de eventos limitados em relação à ação ou acionador de fluxo de trabalho.|Nenhuma dimensão|
|RunFailurePercentage|Percentagem da Falha de Execução|Percent|Total|Percentagem das execuções de fluxo de trabalho falhadas.|Nenhuma dimensão|
|ActionsStarted|Ações Iniciadas |Count|Total|Número de ações de fluxo de trabalho iniciadas.|Nenhuma dimensão|
|ActionsCompleted|Ações Concluídas |Count|Total|Número de ações de fluxo de trabalho concluídas.|Nenhuma dimensão|
|ActionsSucceeded|Ações com Êxito |Count|Total|Número de ações de fluxo de trabalho com êxito.|Nenhuma dimensão|
|ActionsFailed|Ações Falhadas|Count|Total|Número de ações de fluxo de trabalho falhadas.|Nenhuma dimensão|
|ActionsSkipped|Ações Ignoradas |Count|Total|Número de ações de fluxo de trabalho ignoradas.|Nenhuma dimensão|
|ActionLatency|Latência da Ação |Segundos|Média|Latência das ações de fluxo de trabalho concluídas.|Nenhuma dimensão|
|ActionSuccessLatency|Latência de Ações com Êxito |Segundos|Média|Latência de ações de fluxo de trabalho com êxito.|Nenhuma dimensão|
|ActionThrottledEvents|Eventos Limitados em relação a Ação|Count|Total|Número de eventos limitados em relação à ação de fluxo de trabalho.|Nenhuma dimensão|
|TriggersStarted|Acionadores Iniciados |Count|Total|Número de acionadores de fluxo de trabalho iniciados.|Nenhuma dimensão|
|TriggersCompleted|Acionadores Concluídos |Count|Total|Número de acionadores de fluxo de trabalho concluídos.|Nenhuma dimensão|
|TriggersSucceeded|Acionadores Com Êxito |Count|Total|Número de acionadores de fluxo de trabalho com êxito.|Nenhuma dimensão|
|TriggersFailed|Acionadores Com Falha |Count|Total|Número de acionadores de fluxo de trabalho falhados.|Nenhuma dimensão|
|TriggersSkipped|Acionadores Ignorados|Count|Total|Número de acionadores de fluxo de trabalho ignorados.|Nenhuma dimensão|
|TriggersFired|Acionadores Disparados |Count|Total|Número de acionadores de fluxo de trabalho desencadeados.|Nenhuma dimensão|
|TriggerLatency|Latência de Acionador |Segundos|Média|Latência de acionadores de fluxo de trabalho concluídos.|Nenhuma dimensão|
|TriggerFireLatency|Latência de Disparo de Acionador |Segundos|Média|Latência de acionadores de fluxo de trabalho desencadeados.|Nenhuma dimensão|
|TriggerSuccessLatency|Latência de Êxito de Acionador |Segundos|Média|Latência de acionadores de fluxo de trabalho com êxito.|Nenhuma dimensão|
|TriggerThrottledEvents|Eventos Limitados de Acionador|Count|Total|Número de eventos limitados em relação ao acionador de fluxo de trabalho.|Nenhuma dimensão|
|BillableActionExecutions|Execuções de Ação a Cobrar|Count|Total|Número de execuções de ação de fluxo de trabalho a serem faturadas.|Nenhuma dimensão|
|BillableTriggerExecutions|Execuções de Acionador a Cobrar|Count|Total|Número de execuções do acionador de fluxo de trabalho a serem faturadas.|Nenhuma dimensão|
|TotalBillableExecutions|Total de Execuções a Cobrar|Count|Total|Número de execuções de fluxo de trabalho a serem faturadas.|Nenhuma dimensão|
|BillingUsageNativeOperation|Utilização da Faturação para Execuções de Operações Nativas|Count|Total|Número de execuções de operações nativas que estão a ser faturadas.|Nenhuma dimensão|
|BillingUsageStandardConnector|Utilização da Faturação para Execuções de Conector Standard|Count|Total|Número de execuções de conector standard que estão a ser faturadas.|Nenhuma dimensão|
|BillingUsageStorageConsumption|Utilização da Faturação para Execuções de Consumo de Armazenamento|Count|Total|Número de execuções de consumo de armazenamento a serem faturadas.|Nenhuma dimensão|
|BillingUsageNativeOperation|Utilização da Faturação para Execuções de Operações Nativas|Count|Total|Número de execuções de operações nativas que estão a ser faturadas.|Nenhuma dimensão|
|BillingUsageStandardConnector|Utilização da Faturação para Execuções de Conector Standard|Count|Total|Número de execuções de conector standard que estão a ser faturadas.|Nenhuma dimensão|
|BillingUsageStorageConsumption|Utilização da Faturação para Execuções de Consumo de Armazenamento|Count|Total|Número de execuções de consumo de armazenamento a serem faturadas.|Nenhuma dimensão|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RunsStarted|Execuções Iniciadas|Count|Total|Número de execuções de fluxo de trabalho iniciadas.|Nenhuma dimensão|
|RunsCompleted|Execuções Concluídas|Count|Total|Número de execuções de fluxo de trabalho concluídas.|Nenhuma dimensão|
|RunsSucceeded|Execuções com Êxito|Count|Total|Número de execuções de fluxo de trabalho com êxito.|Nenhuma dimensão|
|RunsFailed|Execuções Falhadas|Count|Total|Número de execuções de fluxo de trabalho falhadas.|Nenhuma dimensão|
|RunsCancelled|Execuções canceladas|Count|Total|Número de fluxo de trabalho é executado foi cancelado.|Nenhuma dimensão|
|RunLatency|Latência de Execução|Segundos|Média|Latência de execuções de fluxo de trabalho concluídas.|Nenhuma dimensão|
|RunSuccessLatency|Latência de Execução com Êxito|Segundos|Média|Latência de execuções de fluxo de trabalho com êxito.|Nenhuma dimensão|
|RunThrottledEvents|Eventos Limitados em relação à Execução|Count|Total|Número de eventos limitados em relação à ação ou acionador de fluxo de trabalho.|Nenhuma dimensão|
|RunStartThrottledEvents|Início da execução limitado a eventos|Count|Total|Eventos limitados em relação a número do início da execução de fluxo de trabalho.|Nenhuma dimensão|
|RunFailurePercentage|Percentagem da Falha de Execução|Percent|Total|Percentagem das execuções de fluxo de trabalho falhadas.|Nenhuma dimensão|
|ActionsStarted|Ações Iniciadas |Count|Total|Número de ações de fluxo de trabalho iniciadas.|Nenhuma dimensão|
|ActionsCompleted|Ações Concluídas |Count|Total|Número de ações de fluxo de trabalho concluídas.|Nenhuma dimensão|
|ActionsSucceeded|Ações com Êxito |Count|Total|Número de ações de fluxo de trabalho com êxito.|Nenhuma dimensão|
|ActionsFailed|Ações Falhadas |Count|Total|Número de ações de fluxo de trabalho falhadas.|Nenhuma dimensão|
|ActionsSkipped|Ações Ignoradas |Count|Total|Número de ações de fluxo de trabalho ignoradas.|Nenhuma dimensão|
|ActionLatency|Latência da Ação |Segundos|Média|Latência das ações de fluxo de trabalho concluídas.|Nenhuma dimensão|
|ActionSuccessLatency|Latência de Ações com Êxito |Segundos|Média|Latência de ações de fluxo de trabalho com êxito.|Nenhuma dimensão|
|ActionThrottledEvents|Eventos Limitados em relação a Ação|Count|Total|Número de eventos limitados em relação à ação de fluxo de trabalho.|Nenhuma dimensão|
|TriggersStarted|Acionadores Iniciados |Count|Total|Número de acionadores de fluxo de trabalho iniciados.|Nenhuma dimensão|
|TriggersCompleted|Acionadores Concluídos |Count|Total|Número de acionadores de fluxo de trabalho concluídos.|Nenhuma dimensão|
|TriggersSucceeded|Acionadores Com Êxito |Count|Total|Número de acionadores de fluxo de trabalho com êxito.|Nenhuma dimensão|
|TriggersFailed|Acionadores Com Falha |Count|Total|Número de acionadores de fluxo de trabalho falhados.|Nenhuma dimensão|
|TriggersSkipped|Acionadores Ignorados|Count|Total|Número de acionadores de fluxo de trabalho ignorados.|Nenhuma dimensão|
|TriggersFired|Acionadores Disparados |Count|Total|Número de acionadores de fluxo de trabalho desencadeados.|Nenhuma dimensão|
|TriggerLatency|Latência de Acionador |Segundos|Média|Latência de acionadores de fluxo de trabalho concluídos.|Nenhuma dimensão|
|TriggerFireLatency|Latência de Disparo de Acionador |Segundos|Média|Latência de acionadores de fluxo de trabalho desencadeados.|Nenhuma dimensão|
|TriggerSuccessLatency|Latência de Êxito de Acionador |Segundos|Média|Latência de acionadores de fluxo de trabalho com êxito.|Nenhuma dimensão|
|TriggerThrottledEvents|Eventos Limitados de Acionador|Count|Total|Número de eventos limitados em relação ao acionador de fluxo de trabalho.|Nenhuma dimensão|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Utilização do Processador de Fluxo de Trabalho para o Ambiente de Serviço de Integração|Percent|Média|Utilização do processador de fluxo de trabalho para o ambiente de serviço de integração.|Nenhuma dimensão|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Utilização da Memória de Fluxo de Trabalho para o Ambiente de Serviço de Integração|Percent|Média|Utilização da memória de fluxo de trabalho para o ambiente de serviço de integração.|Nenhuma dimensão|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Utilização do Processador do Conector para o Ambiente de Serviço de Integração|Percent|Média|Utilização do processador do conector para o ambiente de serviço de integração.|Nenhuma dimensão|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Utilização da Memória do Conector para o Ambiente de Serviço de Integração|Percent|Média|Utilização da memória do conector para o ambiente de serviço de integração.|Nenhuma dimensão|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Execuções concluídas|Execuções concluídas|Count|Total|Número de execuções concluído com êxito para esta área de trabalho|Cenário|
|Execuções iniciadas|Execuções iniciadas|Count|Total|Número de execuções iniciado para esta área de trabalho|Cenário|
|Execuções Falhadas|Execuções Falhadas|Count|Total|Número de execuções com falhas para esta área de trabalho|Cenário|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Utilização|Utilização|Count|Count|Contagem de chamadas|ApiCategory, ApiName, ResultType, ResponseCode|
|Disponibilidade|Disponibilidade|Percentagem|Média|Disponibilidade das APIs|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AverageOtherLatency|Outra latência média|ms/op|Média|Média de outra latência em milissegundos por operação (que não é de leitura ou escrita)|Nenhuma dimensão|
|AverageReadLatency|Média de latência de leitura|ms/op|Média|Média de latência em milissegundos por operação de leitura|Nenhuma dimensão|
|AverageTotalLatency|Latência média de total|ms/op|Média|Latência total média em milissegundos por operação|Nenhuma dimensão|
|AverageWriteLatency|Latência média de escrita|ms/op|Média|Latência média de escrita em milissegundos por operação|Nenhuma dimensão|
|FilesystemOtherOps|Sistema de ficheiros outras operações|OPS|Média|Número de sistema de ficheiros outras operações (que não é de leitura ou escrita)|Nenhuma dimensão|
|FilesystemReadOps|Operações de leitura do sistema de ficheiros|OPS|Média|Operações de leitura do número de sistema de ficheiros|Nenhuma dimensão|
|FilesystemTotalOps|Ops total do sistema de ficheiros|OPS|Média|Soma de todas as operações de sistema de ficheiros|Nenhuma dimensão|
|FilesystemWriteOps|Operações de escrita do sistema de ficheiros|OPS|Média|Número de operações de escrita do sistema de ficheiros|Nenhuma dimensão|
|IoBytesPerOtherOps|Bytes de e/s por outras operações|bytes/op|Média|Número no/saída de bytes por outras operações (que não é de leitura ou escrita)|Nenhuma dimensão|
|IoBytesPerReadOps|Bytes de e/s por operações de leitura|bytes/op|Média|Número de bytes por operação de leitura In/out|Nenhuma dimensão|
|IoBytesPerTotalOps|Bytes de e/s por op em todas as operações|bytes/op|Média|Soma de todos os de entrada/saída da operação de bytes|Nenhuma dimensão|
|IoBytesPerWriteOps|Bytes de e/s por operações de escrita|bytes/op|Média|Número no/saída de bytes por operação de escrita|Nenhuma dimensão|
|OtherIops|Outros iops|operations/second|Média|Outro de entrada/saída da operação por segundo|Nenhuma dimensão|
|OtherThroughput|Outro débito|MBps|Média|Outro débito em megabytes por segundo (ou seja, não de leitura ou escrita)|Nenhuma dimensão|
|ReadIops|Iops de leitura|operations/second|Média|Ler operações por segundo In/out|Nenhuma dimensão|
|ReadThroughput|Débito de leitura|MBps|Média|Débito de leitura em megabytes por segundo|Nenhuma dimensão|
|TotalIops|Total de iops|operations/second|Média|Soma de todos os de entrada/saída operações por segundo|Nenhuma dimensão|
|TotalThroughput|Débito total|MBps|Média|Soma de todos os débito em megabytes por segundo|Nenhuma dimensão|
|VolumeAllocatedSize|Atribuído o tamanho do volume|bytes|Média|Tamanho alocado do volume (bytes utilizado não real)|Nenhuma dimensão|
|VolumeLogicalSize|Tamanho lógico do volume|bytes|Média|Tamanho lógico do volume (bytes utilizados)|Nenhuma dimensão|
|VolumeSnapshotSize|Tamanho do instantâneo de volume|bytes|Média|Tamanho de todos os instantâneos no volume|Nenhuma dimensão|
|WriteIops|Iops de escrita|operations/second|Média|Escrita de entrada/saída operações por segundo|Nenhuma dimensão|
|WriteThroughput|Escrever o débito|MBps|Média|Escrever o débito em megabytes por segundo|Nenhuma dimensão|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Tamanho do volume de agrupamento alocado|bytes|Média|Tamanho alocado do conjunto (bytes utilizado não real)|Nenhuma dimensão|
|VolumePoolAllocatedUsed|Conjunto de volume alocado utilizado|bytes|Média|Tamanho utilizado alocado do conjunto|Nenhuma dimensão|
|VolumePoolTotalLogicalSize|Tamanho lógico total do volume conjunto|bytes|Média|Soma do tamanho lógico de todos os volumes que pertencem ao conjunto|Nenhuma dimensão|
|VolumePoolTotalSnapshotSize|Tamanho total do instantâneo do volume de agrupamento|bytes|Média|Soma de todos os instantâneos em conjunto|Nenhuma dimensão|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BytesSentRate|Bytes enviados|Count|Total|Número de bytes a Interface de rede enviados|Nenhuma dimensão|
|BytesReceivedRate|Bytes recebidos|Count|Total|Número de bytes a Interface de rede recebido|Nenhuma dimensão|
|PacketsSentRate|Pacotes enviados|Count|Total|Número de pacotes de Interface de rede enviados|Nenhuma dimensão|
|PacketsReceivedRate|Pacotes recebidos|Count|Total|Número de pacotes a Interface de rede recebido|Nenhuma dimensão|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|VipAvailability|Disponibilidade do caminho de dados|Count|Média|Disponibilidade de caminho do Balanceador de carga dados média por duração de tempo|FrontendIPAddress, FrontendPort|
|DipAvailability|Estado de sonda de estado de funcionamento|Count|Média|Média Load Balancer sonda o estado de funcionamento por duração de tempo|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Contagem de bytes|Count|Total|Número total de Bytes transmitidos num período de tempo|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Contagem de pacotes|Count|Total|Número total de pacotes transmitidos num período de tempo|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|Contagem de SYN|Count|Total|Número total de pacotes de SYN transmitidos num período de tempo|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Total de ligações SNAT|Count|Total|Número total de novas ligações de SNAT criado dentro do período de tempo|ConnectionState FrontendIPAddress, BackendIPAddress,|
|AllocatedSnatPorts|As portas alocadas SNAT (pré-visualização)|Count|Total|Número total de portas SNAT alocados num período de tempo|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Portas utilizadas de SNAT (pré-visualização)|Count|Total|Número total de portas SNAT utilizadas num período de tempo|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueryVolume|Volume de consultas|Count|Total|Número de consultas fornecidas para uma zona DNS|Nenhuma dimensão|
|RecordSetCount|Contagem de conjunto de registros|Count|Máximo|Número de conjuntos de registos numa zona DNS|Nenhuma dimensão|
|RecordSetCapacityUtilization|Utilização da capacidade do conjunto de registos|Percent|Máximo|% Da capacidade do conjunto de registos utilizada por uma zona DNS|Nenhuma dimensão|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PacketsInDDoS|Pacotes de entrada DDoS|CountPerSecond|Máximo|Pacotes de entrada DDoS|Nenhuma dimensão|
|PacketsDroppedDDoS|Pacotes de entrada ignorados DDoS|CountPerSecond|Máximo|Pacotes de entrada ignorados DDoS|Nenhuma dimensão|
|PacketsForwardedDDoS|Pacotes de entrada encaminhado DDoS|CountPerSecond|Máximo|Pacotes de entrada encaminhado DDoS|Nenhuma dimensão|
|TCPPacketsInDDoS|Pacotes de entrada de TCP DDoS|CountPerSecond|Máximo|Pacotes de entrada de TCP DDoS|Nenhuma dimensão|
|TCPPacketsDroppedDDoS|Entrada de TCP de pacotes ignorados DDoS|CountPerSecond|Máximo|Entrada de TCP de pacotes ignorados DDoS|Nenhuma dimensão|
|TCPPacketsForwardedDDoS|Pacotes de TCP de entrada encaminhados DDoS|CountPerSecond|Máximo|Pacotes de TCP de entrada encaminhados DDoS|Nenhuma dimensão|
|UDPPacketsInDDoS|Pacotes de entrada UDP DDoS|CountPerSecond|Máximo|Pacotes de entrada UDP DDoS|Nenhuma dimensão|
|UDPPacketsDroppedDDoS|Pacotes de UDP de entrada ignorados DDoS|CountPerSecond|Máximo|Pacotes de UDP de entrada ignorados DDoS|Nenhuma dimensão|
|UDPPacketsForwardedDDoS|Pacotes de UDP de entrada encaminhados DDoS|CountPerSecond|Máximo|Pacotes de UDP de entrada encaminhados DDoS|Nenhuma dimensão|
|BytesInDDoS|Bytes de entrada DDoS|BytesPerSecond|Máximo|Bytes de entrada DDoS|Nenhuma dimensão|
|BytesDroppedDDoS|Bytes de entrada ignorados DDoS|BytesPerSecond|Máximo|Bytes de entrada ignorados DDoS|Nenhuma dimensão|
|BytesForwardedDDoS|Bytes de entrada encaminhado DDoS|BytesPerSecond|Máximo|Bytes de entrada encaminhado DDoS|Nenhuma dimensão|
|TCPBytesInDDoS|Bytes de entrada TCP DDoS|BytesPerSecond|Máximo|Bytes de entrada TCP DDoS|Nenhuma dimensão|
|TCPBytesDroppedDDoS|Bytes TCP entradas ignorados DDoS|BytesPerSecond|Máximo|Bytes TCP entradas ignorados DDoS|Nenhuma dimensão|
|TCPBytesForwardedDDoS|Bytes TCP entradas reencaminhados DDoS|BytesPerSecond|Máximo|Bytes TCP entradas reencaminhados DDoS|Nenhuma dimensão|
|UDPBytesInDDoS|Bytes de entrada UDP DDoS|BytesPerSecond|Máximo|Bytes de entrada UDP DDoS|Nenhuma dimensão|
|UDPBytesDroppedDDoS|Bytes UDP entradas ignorados DDoS|BytesPerSecond|Máximo|Bytes UDP entradas ignorados DDoS|Nenhuma dimensão|
|UDPBytesForwardedDDoS|Bytes UDP entradas reencaminhados DDoS|BytesPerSecond|Máximo|Bytes UDP entradas reencaminhados DDoS|Nenhuma dimensão|
|IfUnderDDoSAttack|Em DDoS de ataque ou não|Count|Máximo|Em DDoS de ataque ou não|Nenhuma dimensão|
|DDoSTriggerTCPPackets|Pacotes de entrada de TCP para acionar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes de entrada de TCP para acionar a mitigação de DDoS|Nenhuma dimensão|
|DDoSTriggerUDPPackets|Pacotes de UDP de entrada para acionar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes de UDP de entrada para acionar a mitigação de DDoS|Nenhuma dimensão|
|DDoSTriggerSYNPackets|Pacotes de entrada de SYN para acionar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes de entrada de SYN para acionar a mitigação de DDoS|Nenhuma dimensão|
|VipAvailability|Disponibilidade do caminho de dados|Count|Média|Disponibilidade média de endereço IP por duração de tempo|Port|
|ByteCount|Contagem de bytes|Count|Total|Número total de Bytes transmitidos num período de tempo|Porta, direção|
|PacketCount|Contagem de pacotes|Count|Total|Número total de pacotes transmitidos num período de tempo|Porta, direção|
|SynCount|Contagem de SYN|Count|Total|Número total de pacotes de SYN transmitidos num período de tempo|Porta, direção|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ApplicationRuleHit|Contagem de acessos a regras de aplicações|Count|Total|Número de vezes que foram acessos a regras de aplicação|Protocolo de status, motivo,|
|NetworkRuleHit|Contagem de ocorrências de regras de rede|Count|Total|Número de vezes que foram acessos a regras de rede|Protocolo de status, motivo,|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Débito|Débito|BytesPerSecond|Total|Número de bytes por segundo, que o Gateway de aplicação tem atendido|Nenhuma dimensão|
|UnhealthyHostCount|Contagem de anfitriões de mau estado de funcionamento|Count|Média|Número de anfitriões de back-end mau estado de funcionamento|BackendSettingsPool|
|HealthyHostCount|Contagem de anfitriões em bom estado|Count|Média|Número de anfitriões de back-end em bom estado|BackendSettingsPool|
|TotalRequests|Total de Pedidos|Count|Total|Contagem de pedidos com êxito, que também serviram Gateway de aplicação|BackendSettingsPool|
|FailedRequests|Pedidos com Falhas|Count|Total|Contagem de pedidos falhados, que também serviram Gateway de aplicação|BackendSettingsPool|
|ResponseStatus|Estado da resposta|Count|Total|Estado de resposta de HTTP retornado pelo Gateway de aplicação|HttpStatusGroup|
|CurrentConnections|Ligações atuais|Count|Total|Contagem de ligações atuais estabelecidas com o Gateway de aplicação|Nenhuma dimensão|
|CapacityUnits|Unidades de capacidade atuais|Count|Média|Unidades de capacidade consumidas|Nenhuma dimensão|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AverageBandwidth|Largura de banda do gateway S2S|BytesPerSecond|Média|Largura de banda de site a site média de um gateway em bytes por segundo|Nenhuma dimensão|
|P2SBandwidth|Largura de banda do gateway P2S|BytesPerSecond|Média|Largura de banda de ponto a site média de um gateway em bytes por segundo|Nenhuma dimensão|
|P2SConnectionCount|Total de ligações de P2S|Count|Máximo|Total de ligações de ponto a site de um gateway|Protocol|
|TunnelAverageBandwidth|Largura de banda de túnel|BytesPerSecond|Média|Largura de banda média de um túnel em bytes por segundo|ConnectionName, RemoteIP|
|TunnelEgressBytes|Bytes de saída de túnel|Bytes|Total|Bytes de saída de um túnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Bytes de entrada de túnel|Bytes|Total|Bytes recebidos de um túnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Pacotes de saída de túnel|Count|Total|Contagem de pacotes de saída de um túnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Pacotes de entrada de túnel|Count|Total|Contagem de pacotes de entrada de um túnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Largada de pacotes de erro de correspondência de saída TS de túnel|Count|Total|Saída contagem de remoção de pacotes de erro de correspondência de Seletor de tráfego de um túnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Largada de pacotes de erro de correspondência de entrada TS de túnel|Count|Total|Contagem de lista de pacotes recebidos de erro de correspondência de Seletor de tráfego de um túnel|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits ingressing Azure por segundo|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits egressing Azure por segundo|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits ingressing Azure por segundo|Nenhuma dimensão|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits egressing Azure por segundo|Nenhuma dimensão|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits ingressing Azure por segundo|Nenhuma dimensão|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits egressing Azure por segundo|Nenhuma dimensão|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QpsByEndpoint|Consultas por ponto de extremidade devolvido|Count|Total|Número de vezes que um ponto de final do Gestor de tráfego foi devolvido num determinado período de tempo|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Estado do ponto final pelo ponto final|Count|Máximo|1 se sonda o um ponto de extremidade estado está "ativado", 0, caso contrário.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ProbesFailedPercent|Sondas de % falhou|Percent|Média|Falha de % de conectividade de sondas de monitorização|Nenhuma dimensão|
|AverageRoundtripMs|Média Tempo de ida e volta (ms)|Milissegundos|Média|Tempo de ida e volta da rede de médio (ms) para conectividade sondas enviadas entre a origem e destino de monitorização|Nenhuma dimensão|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RequestCount|Número de Pedidos|Count|Total|O número de pedidos de cliente atendidos pelo proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Tamanho do pedido|Bytes|Total|O número de bytes enviados como pedidos de clientes para o proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Tamanho de resposta|Bytes|Total|O número de bytes enviados, como as respostas, do proxy HTTP/S para clientes|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Contagem de pedidos de back-end|Count|Total|O número de pedidos enviados a partir do proxy HTTP/S para o back-ends|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Latência de pedido de back-end|Milissegundos|Média|O tempo calculado a partir de quando o pedido foi enviado pelo proxy HTTP/S para o back-end até que o proxy HTTP/S recebido o último byte de resposta de back-end|Back-end|
|TotalLatency|Latência total|Milissegundos|Média|O tempo calculado a partir de quando o pedido de cliente foi recebido pelo proxy HTTP/S até que o cliente confirmados o último byte de resposta do proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Percentagem de estado de funcionamento do back-end|Percent|Média|A percentagem de estado de funcionamento com êxito as sondas do proxy HTTP/S de back-ends|Back-end, BackendPool|
|WebApplicationFirewallRequestCount|Contagem de pedidos de Firewall de aplicação Web|Count|Total|O número de pedidos de clientes processados pelo Firewall de aplicações Web|PolicyName, RuleName, Action|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|registration.all|Operações de Registo|Count|Total|Contagem de todas as operações de registo com êxito (consultas e eliminações de atualizações de criações). |Nenhuma dimensão|
|registration.create|Operações de Criação de Registo|Count|Total|Contagem de todas as criações de registo com êxito.|Nenhuma dimensão|
|registration.update|Operações de Atualização de Registo|Count|Total|Contagem de todas as atualizações de registo com êxito.|Nenhuma dimensão|
|registration.get|Operações de Leitura de Registo|Count|Total|Contagem de todas as consultas de registo com êxito.|Nenhuma dimensão|
|registration.delete|Operações de Eliminação de Registo|Count|Total|Contagem de todas as eliminações de registo com êxito.|Nenhuma dimensão|
|entrada|Mensagens Recebidas|Count|Total|Contagem de todas as chamadas à API enviadas com êxito. |Nenhuma dimensão|
|incoming.scheduled|Notificações Push Agendadas Enviadas|Count|Total|Notificações Push agendadas canceladas|Nenhuma dimensão|
|incoming.scheduled.cancel|Notificações Push agendadas canceladas|Count|Total|Notificações Push agendadas canceladas|Nenhuma dimensão|
|scheduled.pending|Notificações Agendadas Pendentes|Count|Total|Notificações Agendadas Pendentes|Nenhuma dimensão|
|installation.all|Operações de Gestão de Instalação|Count|Total|Operações de Gestão de Instalação|Nenhuma dimensão|
|installation.get|Obter Operações de Instalação|Count|Total|Obter Operações de Instalação|Nenhuma dimensão|
|installation.upsert|Criar ou Atualizar Operações de Instalação|Count|Total|Criar ou Atualizar Operações de Instalação|Nenhuma dimensão|
|installation.patch|Corrigir Operações de Instalação|Count|Total|Corrigir Operações de Instalação|Nenhuma dimensão|
|installation.delete|Eliminar Operações de Instalação|Count|Total|Eliminar Operações de Instalação|Nenhuma dimensão|
|outgoing.allpns.success|Notificações com êxito|Count|Total|Contagem de todas as notificações com êxito.|Nenhuma dimensão|
|outgoing.allpns.invalidpayload|Erros de Payload|Count|Total|Contagem de pushes falhados porque o PNS devolveu um erro de payload inválido.|Nenhuma dimensão|
|outgoing.allpns.pnserror|Erros de Sistema de Notificação Externos|Count|Total|Contagem de pushes falhados porque ocorreu um problema ao comunicar com o PNS (excluindo problemas de autenticação).|Nenhuma dimensão|
|outgoing.allpns.channelerror|Erros de Canal|Count|Total|Contagem de pushes falhados porque o canal era inválido, não estava associado à aplicação correta, sofreu limitações ou expirou.|Nenhuma dimensão|
|outgoing.allpns.badorexpiredchannel|Erros de Canal Incorreto ou Expirado|Count|Total|Contagem de pushes falhados porque o canal/token/registrationId no registo expirou ou era inválido.|Nenhuma dimensão|
|outgoing.wns.success|Notificações com Êxito do WNS|Count|Total|Contagem de todas as notificações com êxito.|Nenhuma dimensão|
|outgoing.wns.invalidcredentials|Erros de Autorização do WNS (Credenciais Inválidas)|Count|Total|Contagem de pushes falhados porque o PNS não aceitou as credenciais fornecidas ou porque as credenciais estão bloqueadas. (Windows Live não reconhece as credenciais).|Nenhuma dimensão|
|outgoing.wns.badchannel|Erro de Canal Incorreto do WNS|Count|Total|Contagem de pushes falhados porque o ChannelURI do registo não foi reconhecido (estado do WNS: 404 não encontrado).|Nenhuma dimensão|
|outgoing.wns.expiredchannel|Erro de Canal Expirado do WNS|Count|Total|Contagem de pushes falhados porque o ChannelURI expirou (estado do WNS: 410 já não existe).|Nenhuma dimensão|
|outgoing.wns.throttled|Notificações Limitadas do WNS|Count|Total|Contagem de pushes falhados porque o WNS está a limitar esta aplicação (estado do WNS: 406 Não aceitável).|Nenhuma dimensão|
|outgoing.wns.tokenproviderunreachable|Erros de Autorização do WNS (Inacessível)|Count|Total|O Windows Live não está acessível.|Nenhuma dimensão|
|outgoing.wns.invalidtoken|Erros de Autorização do WNS (Token Inválido)|Count|Total|O token fornecido para o WNS não é válido (estado do WNS: Não autorizado 401).|Nenhuma dimensão|
|outgoing.wns.wrongtoken|Erros de Autorização do WNS (Token Incorreto)|Count|Total|O token fornecido para o WNS é válido mas para outra aplicação (estado do WNS: 403 Proibido). Isto pode acontecer se o ChannelURI do registo está associado a outra aplicação. Verifique se a aplicação de cliente está associada à mesma aplicação cujas credenciais estão no hub de notificação.|Nenhuma dimensão|
|outgoing.wns.invalidnotificationformat|Formato de Notificação Inválido do WNS|Count|Total|O formato da notificação é inválido (estado do WNS: 400). Tenha em atenção que o WNS não rejeita todos os payloads inválidos.|Nenhuma dimensão|
|outgoing.wns.invalidnotificationsize|Erro de Tamanho de Notificação Inválido do WNS|Count|Total|O payload de notificação é demasiado grande (estado do WNS: 413).|Nenhuma dimensão|
|outgoing.wns.channelthrottled|Canal do WNS Limitado|Count|Total|A notificação foi removida porque o ChannelURI do registo está limitado (cabeçalho de resposta do WNS: X notificationstatus: Channelthrottled de - WNS-).|Nenhuma dimensão|
|outgoing.wns.channeldisconnected|Canal do WNS Desligado|Count|Total|A notificação foi removida porque o ChannelURI do registo está limitado (cabeçalho de resposta do WNS: X-WNS-DeviceConnectionStatus: desligado).|Nenhuma dimensão|
|outgoing.wns.dropped|Notificações Removidas do WNS|Count|Total|A notificação foi removida porque o ChannelURI do registo está limitado (X-WNS-NotificationStatus: removido mas não X-WNS-DeviceConnectionStatus: desligado).|Nenhuma dimensão|
|outgoing.wns.pnserror|Erros do WNS|Count|Total|As notificações não foram entregues devido a erros de comunicação com o WNS.|Nenhuma dimensão|
|outgoing.wns.authenticationerror|Erros de Autenticação do WNS|Count|Total|Notificação não entregue devido a erros de comunicação com o Windows Live, com credenciais inválidas ou token errado.|Nenhuma dimensão|
|outgoing.apns.success|Notificações com Êxito do APNS|Count|Total|Contagem de todas as notificações com êxito.|Nenhuma dimensão|
|outgoing.apns.invalidcredentials|Erros de Autorização do APNS|Count|Total|Contagem de pushes falhados porque o PNS não aceitou as credenciais fornecidas ou porque as credenciais estão bloqueadas.|Nenhuma dimensão|
|outgoing.apns.badchannel|Erro de Canal Incorreto do APNS|Count|Total|Contagem de pushes falhados porque o token é inválido (código de estado do APNS: 8).|Nenhuma dimensão|
|outgoing.apns.expiredchannel|Erro de Canal Expirado do APNS|Count|Total|Contagem de tokens invalidados pelo canal de relatórios do APNS.|Nenhuma dimensão|
|outgoing.apns.invalidnotificationsize|Erro de Tamanho de Notificação Inválido do APNS|Count|Total|Contagem de pushes falhados porque o payload era demasiado grande (código de estado do APNS: 7).|Nenhuma dimensão|
|outgoing.apns.pnserror|Erros do APNS|Count|Total|Contagem de pushes falhados devido a erros de comunicação com o APNS.|Nenhuma dimensão|
|outgoing.gcm.success|Notificações com Êxito do GCM|Count|Total|Contagem de todas as notificações com êxito.|Nenhuma dimensão|
|outgoing.gcm.invalidcredentials|Erros de Autorização do GCM (Credenciais Inválidas)|Count|Total|Contagem de pushes falhados porque o PNS não aceitou as credenciais fornecidas ou porque as credenciais estão bloqueadas.|Nenhuma dimensão|
|outgoing.gcm.badchannel|Erro de Canal Incorreto do GCM|Count|Total|Contagem de pushes falhados porque o registrationId no registo não foi reconhecido (resultado do GCM: Registo inválido).|Nenhuma dimensão|
|outgoing.gcm.expiredchannel|Erro de Canal Expirado do GCM|Count|Total|Contagem de pushes falhados porque o registrationId no registo expirou (resultado do GCM: NotRegistered).|Nenhuma dimensão|
|outgoing.gcm.throttled|Notificações Limitadas do GCM|Count|Total|Contagem de pushes falhados porque o GCM limitou esta aplicação (código de estado do GCM: 501-599 ou resultado: indisponível).|Nenhuma dimensão|
|outgoing.gcm.invalidnotificationformat|Formato de Notificação Inválido do GCM|Count|Total|Contagem de pushes falhados porque o payload não estava formatado corretamente (resultado do GCM: InvalidDataKey ou InvalidTtl).|Nenhuma dimensão|
|outgoing.gcm.invalidnotificationsize|Erro de Tamanho de Notificação Inválido do GCM|Count|Total|Contagem de pushes falhados porque o payload era demasiado grande (resultado do GCM: MessageTooBig).|Nenhuma dimensão|
|outgoing.gcm.wrongchannel|Erro de Canal Errado do GCM|Count|Total|Contagem de pushes falhados porque o registrationId no registo não está associado à aplicação atual (resultado do GCM: InvalidPackageName).|Nenhuma dimensão|
|outgoing.gcm.pnserror|Erros do GCM|Count|Total|Contagem de pushes falhados devido a erros de comunicação com o GCM.|Nenhuma dimensão|
|outgoing.gcm.authenticationerror|Erros de Autenticação do GCM|Count|Total|Contagem de pushes falhados porque o PNS não aceitou as credenciais fornecidas as credenciais estão bloqueadas ou o SenderId não está configurado corretamente na aplicação (resultado do GCM: MismatchedSenderId).|Nenhuma dimensão|
|outgoing.mpns.success|Notificações com Êxito do MPNS|Count|Total|Contagem de todas as notificações com êxito.|Nenhuma dimensão|
|outgoing.mpns.invalidcredentials|Credenciais Inválidas do MPNS|Count|Total|Contagem de pushes falhados porque o PNS não aceitou as credenciais fornecidas ou porque as credenciais estão bloqueadas.|Nenhuma dimensão|
|outgoing.mpns.badchannel|Erro de Canal Incorreto do MPNS|Count|Total|Contagem de pushes falhados porque o ChannelURI do registo não foi reconhecido (estado do MPNS: 404 não encontrado).|Nenhuma dimensão|
|outgoing.mpns.throttled|Notificações Limitadas do MPNS|Count|Total|Contagem de pushes falhados porque o MPNS está a limitar esta aplicação (WNS MPNS: 406 Não aceitável).|Nenhuma dimensão|
|outgoing.mpns.invalidnotificationformat|Formato de Notificação Inválido do MPNS|Count|Total|Contagem de pushes falhados porque o payload da notificação era demasiado grande.|Nenhuma dimensão|
|outgoing.mpns.channeldisconnected|Canal do MPNS Desligado|Count|Total|Contagem de pushes falhados porque o ChannelURI do registo foi desligado (estado do MPNS: 412 não encontrado).|Nenhuma dimensão|
|outgoing.mpns.dropped|Notificações Removidas do MPNS|Count|Total|Contagem de pushes removidos pelo MPNS (cabeçalho de resposta do MPNS: X-NotificationStatus: QueueFull ou suprimido).|Nenhuma dimensão|
|outgoing.mpns.pnserror|Erros do MPNS|Count|Total|Contagem de pushes falhados devido a erros de comunicação com o MPNS.|Nenhuma dimensão|
|outgoing.mpns.authenticationerror|Erros de Autenticação do MPNS|Count|Total|Contagem de pushes falhados porque o PNS não aceitou as credenciais fornecidas ou porque as credenciais estão bloqueadas.|Nenhuma dimensão|
|notificationhub.pushes|Todas as Notificações Enviadas|Count|Total|Todas as notificações enviadas do hub de notificações|Nenhuma dimensão|
|incoming.all.requests|Todos os Pedidos Recebidos|Count|Total|Total de pedidos recebidos de um hub de notificações|Nenhuma dimensão|
|incoming.all.failedrequests|Todos os Pedidos Falhados Recebidos|Count|Total|Total de pedidos falhados recebidos de um hub de notificações|Nenhuma dimensão|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Average_ % de Inodes livres|% De Inodes livres|Count|Média|Average_ % de Inodes livres|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % de espaço livre|% De espaço livre|Count|Média|Average_ % de espaço livre|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % de Inodes utilizados|% De Inodes utilizados|Count|Média|Average_ % de Inodes utilizados|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % de espaço utilizado|% De espaço utilizado|Count|Média|Average_ % de espaço utilizado|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Bytes lidos/seg|Bytes Lidos de Disco/seg|Count|Média|Average_Disk Bytes lidos/seg|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk leituras/seg|Leituras de disco/seg|Count|Média|Average_Disk leituras/seg|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk transferências/seg|As transferências de disco/seg|Count|Média|Average_Disk transferências/seg|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Zapsané Bajty/s|Bytes Escritos em Disco/seg|Count|Média|Average_Disk Zapsané Bajty/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk escritas/seg|Escritas de disco/seg|Count|Média|Average_Disk escritas/seg|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Megabytes livres|Count|Média|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Bytes de disco/seg|Bytes de disco lógico/seg|Count|Média|Average_Logical Bytes de disco/seg|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % de memória disponível|% De memória disponível|Count|Média|Average_ % de memória disponível|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % de espaço de comutação disponível|% De espaço de comutação disponível|Count|Média|Average_ % de espaço de comutação disponível|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % de memória utilizada|% De memória utilizada|Count|Média|Average_ % de memória utilizada|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % de espaço de comutação utilizado|% De espaço de comutação utilizado|Count|Média|Average_ % de espaço de comutação utilizado|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|MBytes de memória Average_Available|MBytes de memória disponíveis|Count|Média|MBytes de memória Average_Available|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|MBytes de comutação Average_Available|Comutação em MBytes disponíveis|Count|Média|MBytes de comutação Average_Available|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page leituras/seg|Leituras de paginações/seg|Count|Média|Average_Page leituras/seg|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page escritas/seg|Páginas escritas/seg|Count|Média|Average_Page escritas/seg|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/seg|Páginas/seg|Count|Média|Average_Pages/seg|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Espaço de comutação em MBytes Average_Used|Espaço de comutação \ MBytes disponíveis|Count|Média|Espaço de comutação em MBytes Average_Used|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|MBytes de memória Average_Used|Memória utilizada em MBytes|Count|Média|MBytes de memória Average_Used|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bytes de Average_Total transmitidos|Total de Bytes transmitidos|Count|Média|Bytes de Average_Total transmitidos|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bytes de Average_Total recebidos|Total de Bytes recebidos|Count|Média|Bytes de Average_Total recebidos|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bytes de Average_Total|Total de Bytes|Count|Média|Bytes de Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pacotes de Average_Total transmitidos|Total de pacotes transmitidos|Count|Média|Pacotes de Average_Total transmitidos|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pacotes de Average_Total recebidos|Total de pacotes recebidos|Count|Média|Pacotes de Average_Total recebidos|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx erros|Total Rx de erros|Count|Média|Average_Total Rx erros|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx erros|Erros de total Tx|Count|Média|Average_Total Tx erros|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total colisões|Colisões total|Count|Média|Average_Total colisões|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disco seg/leitura|Média Disco seg/leitura|Count|Média|Average_Avg. Disco seg/leitura|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disco seg/transferência|Média Disco seg/transferência|Count|Média|Average_Avg. Disco seg/transferência|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disco seg/escritas|Média Disco seg/escritas|Count|Média|Average_Avg. Disco seg/escritas|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Bytes de disco/seg|Bytes de disco físico/seg|Count|Média|Average_Physical Bytes de disco/seg|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct de tempo privilegiado|Tempo de PCT privilegiado|Count|Média|Average_Pct de tempo privilegiado|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Tempo de utilizador Average_Pct|Tempo de utilizador de PCT|Count|Média|Tempo de utilizador Average_Pct|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Memória de Average_Used kBytes|KBytes de memória utilizada|Count|Média|Memória de Average_Used kBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual de memória compartilhada|Memória compartilhada virtual|Count|Média|Average_Virtual de memória compartilhada|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % de tempo de DPC|% De tempo DPC|Count|Média|Average_ % de tempo de DPC|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % de tempo inativo|% De tempo inativo|Count|Média|Average_ % de tempo inativo|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % de tempo de interrupção|% De tempo de interrupção|Count|Média|Average_ % de tempo de interrupção|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Tempo de espera de e/s de % de Average_|% De tempo de espera de e/s|Count|Média|Tempo de espera de e/s de % de Average_|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % de tempo Nice|% De tempo nice|Count|Média|Average_ % de tempo Nice|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % de tempo privilegiado|% De tempo privilegiado|Count|Média|Average_ % de tempo privilegiado|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % tempo do processador|% Tempo do processador|Count|Média|Average_ % tempo do processador|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % tempo de utilizador|% Tempo de utilizador|Count|Média|Average_ % tempo de utilizador|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Memória física de Average_Free|Memória física livre|Count|Média|Memória física de Average_Free|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free espaço nos ficheiros de paginação|Espaço livre em ficheiros de paginação|Count|Média|Average_Free espaço nos ficheiros de paginação|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Memória Virtual de Average_Free|Memória Virtual livre|Count|Média|Memória Virtual de Average_Free|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Processos|Count|Média|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size armazenados em ficheiros de paginação|Tamanho armazenado em ficheiros de paginação|Count|Média|Average_Size armazenados em ficheiros de paginação|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Tempo de atividade|Count|Média|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Utilizadores|Count|Média|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disco seg/leitura|Média Disco seg/leitura|Count|Média|Average_Avg. Disco seg/leitura|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disco seg/escritas|Média Disco seg/escritas|Count|Média|Average_Avg. Disco seg/escritas|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Comprimento da fila de disco Average_Current|Comprimento de fila de disco atual|Count|Média|Comprimento da fila de disco Average_Current|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk leituras/seg|Leituras de disco/seg|Count|Média|Average_Disk leituras/seg|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk transferências/seg|As transferências de disco/seg|Count|Média|Average_Disk transferências/seg|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk escritas/seg|Escritas de disco/seg|Count|Média|Average_Disk escritas/seg|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Megabytes livres|Count|Média|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % de espaço livre|% De espaço livre|Count|Média|Average_ % de espaço livre|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|MBytes de Average_Available|MBytes disponíveis|Count|Média|MBytes de Average_Available|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % comprometida Bytes em utilização|% Bytes dedicados em utilização|Count|Média|Average_ % comprometida Bytes em utilização|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes recebidos/seg|Bytes recebidos/seg|Count|Média|Average_Bytes recebidos/seg|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes enviados/seg|Bytes enviados/seg|Count|Média|Average_Bytes enviados/seg|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Total de Average_Bytes/seg|Bytes totais/seg|Count|Média|Total de Average_Bytes/seg|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % tempo do processador|% Tempo do processador|Count|Média|Average_ % tempo do processador|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Comprimento da fila Average_Processor|Comprimento da fila de processador|Count|Média|Comprimento da fila Average_Processor|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Heartbeat|Heartbeat|Count|Total|Heartbeat|Computer, OSType, Version, SourceComputerId|
|Actualizar|Actualizar|Count|Média|Actualizar|Computador, produto, classificação, UpdateState, opcional, aprovado|
|Evento|Evento|Count|Média|Evento|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueryDuration|Duração de consulta|Milissegundos|Média|Duração de consulta de DAX no último intervalo|Nenhuma dimensão|
|QueryPoolJobQueueLength|Threads: Comprimento de fila da tarefa de conjunto de consulta|Count|Média|Número de tarefas na fila do pool de threads de consulta.|Nenhuma dimensão|
|qpu_high_utilization_metric|Elevada utilização de QPU|Count|Total|Elevada utilização de QPU no último minuto, 1 para a utilização de QPU elevada, 0; caso contrário|Nenhuma dimensão|
|memory_metric|Memória|Bytes|Média|Memória. Intervalo de 0 a 3 GB para A1, 0 a 5 GB para A2, 0 a 10 GB para A3, 0-25 GB para A4, 0-50 GB das séries a5 e 0-100 GB para A6|Nenhuma dimensão|
|memory_thrashing_metric|Degradação de memória|Percent|Média|Degradação de memória média.|Nenhuma dimensão|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Count|Total|ListenerConnections com êxito do Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Count|Total|ClientError em ListenerConnections do Microsoft.Relay.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Count|Total|ServerError em ListenerConnections do Microsoft.Relay.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Count|Total|SenderConnections com êxito do Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Count|Total|ClientError em SenderConnections do Microsoft.Relay.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Count|Total|ServerError em SenderConnections do Microsoft.Relay.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Count|Total|ListenerConnections totais do Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Count|Total|Pedidos de SenderConnections totais do Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Count|Total|ActiveConnections totais do Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Count|Total|ActiveListeners totais do Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Count|Total|BytesTransferred totais do Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Count|Total|ListenerDisconnects totais do Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Count|Total|SenderDisconnects totais do Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SearchLatency|Latência de pesquisa|Segundos|Média|Latência média de pesquisa para o serviço de pesquisa|Nenhuma dimensão|
|SearchQueriesPerSecond|Pesquisar consultas por segundo|CountPerSecond|Média|Pesquisar consultas por segundo para o serviço de pesquisa|Nenhuma dimensão|
|ThrottledSearchQueriesPercentage|Percentagem de pesquisas de consultas otimizadas|Percent|Média|Percentagem de consultas de pesquisa que foram otimizados para o serviço de pesquisa|Nenhuma dimensão|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfulRequests|Pedidos com êxito (pré-visualização)|Count|Total|Total de pedidos com êxito para um espaço de nomes (pré-visualização)|EntityName|
|ServerErrors|Erros de Servidor. (Pré-visualização)|Count|Total|Erros de Servidor do Microsoft.ServiceBus. (Pré-visualização)|EntityName|
|UserErrors|Erros do Utilizador. (Pré-visualização)|Count|Total|Erros de Utilizador do Microsoft.ServiceBus. (Pré-visualização)|EntityName|
|ThrottledRequests|Pedidos Limitados. (Pré-visualização)|Count|Total|Pedidos Limitados do Microsoft.ServiceBus. (Pré-visualização)|EntityName|
|IncomingRequests|Pedidos de entrada (pré-visualização)|Count|Total|Pedidos Recebidos do Microsoft.ServiceBus. (Pré-visualização)|EntityName|
|IncomingMessages|Mensagens de entrada (pré-visualização)|Count|Total|Mensagens Recebidas do Microsoft.ServiceBus. (Pré-visualização)|EntityName|
|OutgoingMessages|Mensagens de saída (pré-visualização)|Count|Total|Mensagens Enviadas do Microsoft.ServiceBus. (Pré-visualização)|EntityName|
|ActiveConnections|ActiveConnections (pré-visualização)|Count|Total|Total de Ligações Ativas do Microsoft.ServiceBus. (Pré-visualização)|Nenhuma dimensão|
|Tamanho|Tamanho (pré-visualização)|Bytes|Média|Tamanho de um(a) Fila/Tópico em Bytes. (Pré-visualização)|EntityName|
|Mensagens|Contagem de mensagens num(a) Fila/Tópico. (Pré-visualização)|Count|Média|Contagem de mensagens num(a) Fila/Tópico. (Pré-visualização)|EntityName|
|ActiveMessages|Contagem de mensagens ativas num(a) Fila/Tópico. (Pré-visualização)|Count|Média|Contagem de mensagens ativas num(a) Fila/Tópico. (Pré-visualização)|EntityName|
|DeadletteredMessages|Contagem de mensagens não entregues num Fila/Tópico. (Pré-visualização)|Count|Média|Contagem de mensagens não entregues num Fila/Tópico. (Pré-visualização)|EntityName|
|ScheduledMessages|Contagem de mensagens agendadas numa Fila/Tópico. (Pré-visualização)|Count|Média|Contagem de mensagens agendadas numa Fila/Tópico. (Pré-visualização)|EntityName|
|CPUXNS|Utilização da CPU por espaço de nomes|Percent|Máximo|Métrica de utilização do espaço de nomes da CPU do service bus premium|Nenhuma dimensão|
|WSXNS|Utilização do tamanho da memória por espaço de nomes|Percent|Máximo|Métrica de utilização de memória do espaço de nomes do service bus premium|Nenhuma dimensão|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Count|Média|CPU atribuída a este contentor em milinúcleos|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Bytes|Média|Memória alocada para este contentor em MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Count|Média|Utilização de CPU real em milinúcleos|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Bytes|Média|Uso real de memória em MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Percent|Média|Utilização de CPU para esse contentor como percentagem do AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Percent|Média|Utilização de CPU para esse contentor como percentagem do AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Count|Média|Estado da aplicação de malha do Service Fabric|ApplicationName, estado|
|ServiceStatus|ServiceStatus|Count|Média|Estado de funcionamento de um serviço no aplicativo de malha do Service Fabric|ApplicationName, Status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Count|Média|Estado de funcionamento de uma réplica de serviço no aplicativo de malha do Service Fabric|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Count|Média|Estado do contentor no aplicativo de malha do Service Fabric|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|RestartCount|RestartCount|Count|Média|Reiniciar contagem de um contentor no aplicativo de malha do Service Fabric|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ConnectionCount|Total de ligações|Count|Máximo|A quantidade de ligação do utilizador.|Ponto Final|
|MessageCount|Contagem de Mensagens|Count|Total|A quantidade total de mensagens.|Nenhuma dimensão|
|InboundTraffic|Tráfego de entrada|Bytes|Total|O tráfego de entrada do serviço|Nenhuma dimensão|
|OutboundTraffic|Tráfego de saída|Bytes|Total|O tráfego de saída do serviço|Nenhuma dimensão|
|UserErrors|Erros de utilizador|Percent|Máximo|A percentagem de erros de utilizador|Nenhuma dimensão|
|SystemErrors|Erros de sistema|Percent|Máximo|A percentagem de erros de sistema|Nenhuma dimensão|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentagem de CPU |Percent|Média|Percentagem de CPU |Nenhuma dimensão|
|physical_data_read_percent|Percentagem de E/S de Dados|Percent|Média|Percentagem de E/S de Dados|Nenhuma dimensão|
|log_write_percent|Percentagem de E/S de Registo|Percent|Média|Percentagem de E/S de Registo|Nenhuma dimensão|
|dtu_consumption_percent|Percentagem de DTU|Percent|Média|Percentagem de DTU|Nenhuma dimensão|
|armazenamento|Espaço de dados utilizado|Bytes|Máximo|Tamanho total da base de dados|Nenhuma dimensão|
|connection_successful|Ligações com êxito|Count|Total|Ligações com êxito|Nenhuma dimensão|
|connection_failed|Ligações com Falhas|Count|Total|Ligações com Falhas|Nenhuma dimensão|
|blocked_by_firewall|Bloqueado pela Firewall|Count|Total|Bloqueado pela Firewall|Nenhuma dimensão|
|Deadlock|Impasses|Count|Total|Impasses|Nenhuma dimensão|
|storage_percent|Percentagem de espaço utilizado de dados|Percent|Máximo|Percentagem de tamanho da Base de Dados|Nenhuma dimensão|
|xtp_storage_percent|Percentagem de armazenamento OLTP dentro da memória|Percent|Média|Percentagem de armazenamento OLTP dentro da memória|Nenhuma dimensão|
|workers_percent|Percentagem de funções de trabalho|Percent|Média|Percentagem de funções de trabalho|Nenhuma dimensão|
|sessions_percent|Percentagem de sessões|Percent|Média|Percentagem de sessões|Nenhuma dimensão|
|dtu_limit|Limite DTU|Count|Média|Limite DTU|Nenhuma dimensão|
|dtu_used|DTUS utilizadas|Count|Média|DTUS utilizadas|Nenhuma dimensão|
|cpu_limit|Limite de CPU|Count|Média|Limite de CPU|Nenhuma dimensão|
|cpu_used|Utilizada de CPU|Count|Média|Utilizada de CPU|Nenhuma dimensão|
|dwu_limit|Limite de DWU|Count|Máximo|Limite de DWU|Nenhuma dimensão|
|dwu_consumption_percent|Percentagem DWU|Percent|Máximo|Percentagem DWU|Nenhuma dimensão|
|dwu_used|DWU utilizado|Count|Máximo|DWU utilizado|Nenhuma dimensão|
|dw_cpu_percent|Percentagem de CPU de nível do nó de armazém de dados|Percent|Média|Percentagem de CPU de nível do nó de armazém de dados|DwLogicalNodeId|
|dw_physical_data_read_percent|Percentagem de es de dados de nível de nó do armazém de dados|Percent|Média|Percentagem de es de dados de nível de nó do armazém de dados|DwLogicalNodeId|
    |cache_hit_percent|Percentagem de acertos na cache|Percent|Máximo|Percentagem de acertos na cache|Nenhuma dimensão|
|cache_used_percent|Percentagem de cache utilizada|Percent|Máximo|Percentagem de cache utilizada|Nenhuma dimensão|
|local_tempdb_usage_percent|Percentagem de local tempdb|Percent|Média|Percentagem de local tempdb|Nenhuma dimensão|
|app_cpu_billed|Aplicação da CPU faturada|Count|Total|Aplicação da CPU faturada|Nenhuma dimensão|
|app_cpu_percent|Percentagem de CPU de aplicação|Percent|Média|Percentagem de CPU de aplicação|Nenhuma dimensão|
|app_memory_percent|Percentagem de memória utilizada de aplicação|Percent|Média|Percentagem de memória utilizada de aplicação|Nenhuma dimensão|
|allocated_data_storage|Espaço de dados atribuído|Bytes|Média|Espaço de dados atribuído|Nenhuma dimensão|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentagem de CPU |Percent|Média|Percentagem de CPU |Nenhuma dimensão|
|physical_data_read_percent|Percentagem de E/S de Dados|Percent|Média|Percentagem de E/S de Dados|Nenhuma dimensão|
|log_write_percent|Percentagem de E/S de Registo|Percent|Média|Percentagem de E/S de Registo|Nenhuma dimensão|
|dtu_consumption_percent|Percentagem de DTU|Percent|Média|Percentagem de DTU|Nenhuma dimensão|
|storage_percent|Percentagem de espaço utilizado de dados||Percent|Média|Percentagem de armazenamento|Nenhuma dimensão|
|workers_percent|Percentagem de funções de trabalho|Percent|Média|Percentagem de funções de trabalho|Nenhuma dimensão|
|sessions_percent|Percentagem de sessões|Percent|Média|Percentagem de sessões|Nenhuma dimensão|
|eDTU_limit|limite de eDTU|Count|Média|limite de eDTU|Nenhuma dimensão|
|storage_limit|Tamanho máximo de dados|Bytes|Média|Limite de armazenamento|Nenhuma dimensão|
|eDTU_used|eDTU utilizado|Count|Média|eDTU utilizado|Nenhuma dimensão|
|storage_used|Espaço de dados utilizado|Bytes|Média|Armazenamento utilizado|Nenhuma dimensão|
|xtp_storage_percent|Percentagem de armazenamento OLTP dentro da memória|Percent|Média|Percentagem de armazenamento OLTP dentro da memória|Nenhuma dimensão|
|cpu_limit|Limite de CPU|Count|Média|Limite de CPU|Nenhuma dimensão|
|cpu_used|Utilizada de CPU|Count|Média|Utilizada de CPU|Nenhuma dimensão|
|allocated_data_storage|Espaço de dados atribuído|Bytes|Média|Espaço de dados atribuído|Nenhuma dimensão|
|allocated_data_storage_percent|Percentagem de espaço alocado de dados|Percent|Máximo|Percentagem de espaço alocado de dados|Nenhuma dimensão|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|virtual_core_count|Número de núcleos virtuais|Count|Média|Número de núcleos virtuais|Nenhuma dimensão|
|avg_cpu_percent|Percentagem de CPU média|Percent|Média|Percentagem de CPU média|Nenhuma dimensão|
|reserved_storage_mb|Espaço de armazenamento reservado|Count|Média|Espaço de armazenamento reservado|Nenhuma dimensão|
|storage_space_used_mb|Espaço de armazenamento utilizado|Count|Média|Espaço de armazenamento utilizado|Nenhuma dimensão|
|io_requests|Contagem de pedidos de e/s|Count|Média|Contagem de pedidos de e/s|Nenhuma dimensão|
|io_bytes_read|Bytes de e/s de leitura|Bytes|Média|Bytes de e/s de leitura|Nenhuma dimensão|
|io_bytes_written|Bytes de e/s escritos|Bytes|Média|Bytes de e/s escritos|Nenhuma dimensão|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|UsedCapacity|Capacidade utilizada|Bytes|Média|Capacidade da conta utilizada|Nenhuma dimensão|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com êxito ou falhados, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, autenticação de GeoType, ApiName,|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Autenticação de GeoType, ApiName,|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Autenticação de GeoType, ApiName,|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada no AverageE2ELatency.|Autenticação de GeoType, ApiName,|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Autenticação de GeoType, ApiName,|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada Considerando o valor de TotalBillableRequests e dividindo-o pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam em disponibilidade reduzida para o serviço de armazenamento ou a operação de API especificada.|Autenticação de GeoType, ApiName,|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BlobCapacity|Capacidade do Blob|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Blob a conta de armazenamento, em bytes.|BlobType, Tier|
|BlobCount|Contagem de Blobs|Contagem|Total|O número de blobs no serviço Blob da conta de armazenamento.|BlobType|       |BlobCount|Contagem de Blobs|Count|Média|O número de blobs no serviço Blob da conta de armazenamento.|BlobType, Tier|
|ContainerCount|Contagem do Contentor de Blobs|Contagem|Média|O número de contentores no serviço Blob da conta de armazenamento.|Nenhuma dimensão|
|IndexCapacity|Capacidade de Índice|Bytes|Média|A quantidade de armazenamento utilizada pelo Índice do ADLS Gen2 (Hierárquico) em bytes.|Nenhuma dimensão|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com êxito ou falhados, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, autenticação de GeoType, ApiName,|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Autenticação de GeoType, ApiName,|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Autenticação de GeoType, ApiName,|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada no AverageE2ELatency.|Autenticação de GeoType, ApiName,|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Autenticação de GeoType, ApiName,|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada Considerando o valor de TotalBillableRequests e dividindo-o pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam em disponibilidade reduzida para o serviço de armazenamento ou a operação de API especificada.|Autenticação de GeoType, ApiName,|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|FileCapacity|Capacidade de Ficheiros|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Ficheiro a conta de armazenamento, em bytes.|Nenhuma dimensão|
|FileCount|Contagem de ficheiros|Count|Média|O número de ficheiros no serviço Ficheiro da conta de armazenamento.|Nenhuma dimensão|
|FileShareCount|Contagem de Partilha de Ficheiros|Count|Média|O número de partilhas de ficheiros no serviço Ficheiro da conta de armazenamento.|Nenhuma dimensão|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com êxito ou falhados, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, autenticação de GeoType, ApiName,|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Autenticação de GeoType, ApiName,|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Autenticação de GeoType, ApiName,|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada no AverageE2ELatency.|Autenticação de GeoType, ApiName,|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Autenticação de GeoType, ApiName,|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada Considerando o valor de TotalBillableRequests e dividindo-o pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam em disponibilidade reduzida para o serviço de armazenamento ou a operação de API especificada.|Autenticação de GeoType, ApiName,|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueueCapacity|Capacidade de Fila|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Fila a conta de armazenamento, em bytes.|Nenhuma dimensão|
|QueueCount|Contagem de Filas|Contagem|Média|O número de filas no serviço Fila da conta de armazenamento.|Nenhuma dimensão|
|QueueMessageCount|Contagem de Mensagens em Fila|Contagem|Média|O número aproximado de mensagens em fila no serviço Fila da conta de armazenamento.|Nenhuma dimensão|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com êxito ou falhados, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, autenticação de GeoType, ApiName,|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Autenticação de GeoType, ApiName,|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Autenticação de GeoType, ApiName,|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada no AverageE2ELatency.|Autenticação de GeoType, ApiName,|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Autenticação de GeoType, ApiName,|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada Considerando o valor de TotalBillableRequests e dividindo-o pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam em disponibilidade reduzida para o serviço de armazenamento ou a operação de API especificada.|Autenticação de GeoType, ApiName,|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TableCapacity|Capacidade de Tabelas|Bytes|Média|A quantidade de armazenamento utilizado pelo serviço Tabela da conta de armazenamento, em bytes.|Nenhuma dimensão|
|TableCount|Contagem de Tabelas|Contagem|Média|O número de tabelas no serviço Tabela da conta de armazenamento.|Nenhuma dimensão|
|TableEntityCount|Contagem de Entidade de Tabelas|Contagem|Média|O número de entidades de tabelas no serviço Tabela da conta de armazenamento.|Nenhuma dimensão|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com êxito ou falhados, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, autenticação de GeoType, ApiName,|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Autenticação de GeoType, ApiName,|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Autenticação de GeoType, ApiName,|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada no AverageE2ELatency.|Autenticação de GeoType, ApiName,|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Autenticação de GeoType, ApiName,|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada Considerando o valor de TotalBillableRequests e dividindo-o pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam em disponibilidade reduzida para o serviço de armazenamento ou a operação de API especificada.|Autenticação de GeoType, ApiName,|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Resultado da sessão de sincronização|Count|Média|Métrica de registos de um valor de 1 para cada vez o ponto final do servidor com êxito é concluída uma sessão de sincronização com o ponto final da Cloud|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Ficheiros Sincronizados|Count|Total|Contagem de ficheiros sincronizados|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Os ficheiros não estão a sincronizar|Count|Total|Contagem de ficheiros que falharam na sincronização|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Bytes sincronizados|Bytes|Total|Tamanho total do ficheiro transferido para sessões de sincronização|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Estado Online do Servidor|Count|Máximo|Métrica que registos de um valor de 1 para cada tempo o servidor registado com êxito regista um heartbeat com ponto final da Cloud|NomeServidor|
|StorageSyncRecallIOTotalSizeBytes|Lembre-se camadas da cloud|Bytes|Total|Tamanho total dos dados recuperados pelo servidor|NomeServidor|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ResourceUtilization|% de utilização SU|Percent|Máximo|% de utilização SU|LogicalName, PartitionId|
|InputEvents|Eventos de Entrada|Count|Total|Eventos de Entrada|LogicalName, PartitionId|
|InputEventBytes|Bytes de Evento de Entrada|Bytes|Total|Bytes de Evento de Entrada|LogicalName, PartitionId|
|LateInputEvents|Eventos de Entrada atrasados|Count|Total|Eventos de Entrada atrasados|LogicalName, PartitionId|
|OutputEvents|Eventos de Saída|Count|Total|Eventos de Saída|LogicalName, PartitionId|
|ConversionErrors|Erros de Conversão de Dados|Count|Total|Erros de Conversão de Dados|LogicalName, PartitionId|
|Erros|Erros de Tempo de Execução|Count|Total|Erros de Tempo de Execução|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Eventos desordenados|Count|Total|Eventos desordenados|LogicalName, PartitionId|
|AMLCalloutRequests|Pedidos de Função|Count|Total|Pedidos de Função|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Falha no pedido de funções|Count|Total|Falha no pedido de funções|LogicalName, PartitionId|
|AMLCalloutInputEvents|Eventos de Função|Count|Total|Eventos de Função|LogicalName, PartitionId|
|DeserializationError|Erros de Desserialização de entrada|Count|Total|Erros de Desserialização de entrada|LogicalName, PartitionId|
|EarlyInputEvents|Eventos de Entrada Antigos|Count|Total|Eventos de Entrada Antigos|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Atraso de Marca de Água|Segundos|Máximo|Atraso de Marca de Água|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Eventos de Entrada Pendentes|Count|Máximo|Eventos de Entrada Pendentes|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Origens de Entrada Recebidas|Count|Total|Origens de Entrada Recebidas|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|IngressReceivedMessages|Entrada recebeu mensagens|Count|Total|Contagem de mensagens de leitura do hub de eventos ou do IoT hub todas as origens de eventos|Nenhuma dimensão|
|IngressReceivedInvalidMessages|Entrada recebeu mensagens inválidas|Count|Total|Contagem de mensagens inválidas ler a partir do hub de eventos de todos os ou o hub IoT origens de eventos|Nenhuma dimensão|
|IngressReceivedBytes|Bytes recebidos de entrada|Bytes|Total|Contagem de bytes lidos de todas as origens de eventos|Nenhuma dimensão|
|IngressStoredBytes|Entrada armazenados Bytes|Bytes|Total|Tamanho total de eventos processados e disponíveis para consulta com êxito|Nenhuma dimensão|
|IngressStoredEvents|Entrada armazenados eventos|Count|Total|Contagem de eventos plana disponíveis para consulta e processados com êxito|Nenhuma dimensão|
|IngressReceivedMessagesTimeLag|Intervalo de tempo de mensagens recebidas de entrada|Segundos|Máximo|Diferença entre a hora em que a mensagem é colocado em fila na origem de evento e o momento do processamento de entrada|Nenhuma dimensão|
|IngressReceivedMessagesCountLag|Atraso de contagem de mensagens recebidas de entrada|Count|Média|Número de partição e a sequência de mensagem a ser processado na entrada da origem de diferença entre o número de sequência de mensagem do último colocados em fila de eventos|Nenhuma dimensão|
|WarmStorageMaxProperties|Propriedades de máx. de armazenamento de acesso pouco frequente|Count|Máximo|Número máximo de propriedades utilizadas permitido pelo ambiente para S1/S2 SKU e o número máximo de propriedades permitido pelo Store quente para PAYG SKU|Nenhuma dimensão|
|WarmStorageUsedProperties|O armazenamento de acesso pouco frequente utilizado propriedades |Count|Máximo|Número de propriedades utilizadas pelo ambiente de S1/S2 SKU e o número de propriedades utilizadas pelo Store quente para PAYG SKU|Nenhuma dimensão|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|IngressReceivedMessages|Entrada recebeu mensagens|Count|Total|Contagem de mensagens ler a partir da origem de evento|Nenhuma dimensão|
|IngressReceivedInvalidMessages|Entrada recebeu mensagens inválidas|Count|Total|Contagem de mensagens inválidas, ler a partir da origem de evento|Nenhuma dimensão|
|IngressReceivedBytes|Bytes recebidos de entrada|Bytes|Total|Contagem de bytes lidos a partir da origem de evento|Nenhuma dimensão|
|IngressStoredBytes|Entrada armazenados Bytes|Bytes|Total|Tamanho total de eventos processados e disponíveis para consulta com êxito|Nenhuma dimensão|
|IngressStoredEvents|Entrada armazenados eventos|Count|Total|Contagem de eventos plana disponíveis para consulta e processados com êxito|Nenhuma dimensão|
|IngressReceivedMessagesTimeLag|Intervalo de tempo de mensagens recebidas de entrada|Segundos|Máximo|Diferença entre a hora em que a mensagem é colocado em fila na origem de evento e o momento do processamento de entrada|Nenhuma dimensão|
|IngressReceivedMessagesCountLag|Atraso de contagem de mensagens recebidas de entrada|Count|Média|Número de partição e a sequência de mensagem a ser processado na entrada da origem de diferença entre o número de sequência de mensagem do último colocados em fila de eventos|Nenhuma dimensão|
|WarmStorageMaxProperties|Propriedades de máx. de armazenamento de acesso pouco frequente|Count|Máximo|Número máximo de propriedades utilizadas permitido pelo ambiente para S1/S2 SKU e o número máximo de propriedades permitido pelo Store quente para PAYG SKU|Nenhuma dimensão|
|WarmStorageUsedProperties|O armazenamento de acesso pouco frequente utilizado propriedades |Count|Máximo|Número de propriedades utilizadas pelo ambiente de S1/S2 SKU e o número de propriedades utilizadas pelo Store quente para PAYG SKU|Nenhuma dimensão|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Bytes Lidos de Disco/Seg|BytesPerSecond|Média|Débito de disco de média devido a operações de leitura ao longo do período de exemplo.|Nenhuma dimensão|
|DiskWriteBytesPerSecond|Bytes Escritos em Disco/Seg|BytesPerSecond|Média|Débito de disco de média devido a operações de escrita ao longo do período de exemplo.|Nenhuma dimensão|
|Bytes de Leitura do Disco|Bytes de Leitura do Disco|Bytes|Total|Taxa de transferência total do disco devido a operações de leitura ao longo do período de exemplo.|Nenhuma dimensão|
|Bytes de Escrita de Disco|Bytes de Escrita de Disco|Bytes|Total|Taxa de transferência total do disco devido a operações de escrita ao longo do período de exemplo.|Nenhuma dimensão|
|DiskReadOperations|Operações de leitura do disco|Count|Total|O número de e/s de operações de leitura no período de exemplo anterior. Tenha em atenção que estas operações podem estar disponíveis em tamanho normal.|Nenhuma dimensão|
|DiskWriteOperations|Operações de escrita de disco|Count|Total|O número de e/s de operações de escrita no período de exemplo anterior. Tenha em atenção que estas operações podem estar disponíveis em tamanho normal.|Nenhuma dimensão|
|Operações/Seg de Leitura do Disco|Operações/Seg de Leitura do Disco|CountPerSecond|Média|O número médio de e/s de operações de leitura no período de exemplo anterior. Tenha em atenção que estas operações podem estar disponíveis em tamanho normal.|Nenhuma dimensão|
|Operações/Seg de Escrita de Disco|Operações/Seg de Escrita de Disco|CountPerSecond|Média|O número médio de e/s de operações de escrita no período de exemplo anterior. Tenha em atenção que estas operações podem estar disponíveis em tamanho normal.|Nenhuma dimensão|
|DiskReadLatency|Latência de leitura do disco|Milissegundos|Média|Total de latência de leitura. A soma do dispositivo e o kernel latências de leitura.|Nenhuma dimensão|
|DiskWriteLatency|Latência de escrita de disco|Milissegundos|Média|Latência de escrita total. A soma do dispositivo e o kernel latências de escrita.|Nenhuma dimensão|
|NetworkInBytesPerSecond|Rede no Bytes/seg|BytesPerSecond|Média|Débito de rede médio para o tráfego recebido.|Nenhuma dimensão|
|NetworkOutBytesPerSecond|Rede lá fora de Bytes/seg|BytesPerSecond|Média|Débito de rede médio para o tráfego transmitido.|Nenhuma dimensão|
|Entrada de Rede|Entrada de Rede|Bytes|Total|Débito de rede total para o tráfego recebido.|Nenhuma dimensão|
|Saída de Rede|Saída de Rede|Bytes|Total|Débito de rede total para o tráfego transmitido.|Nenhuma dimensão|
|MemoryUsed|Memória utilizada|Bytes|Média|A quantidade de memória da máquina que está a ser utilizado pela VM.|Nenhuma dimensão|
|MemoryGranted|Memória concedida|Bytes|Média|A quantidade de memória que foi concedida à VM através do anfitrião. Memória não for concedida ao anfitrião até que ele utilizado uma vez e concedido memória pode ser trocada ou ballooned distância se precisar do VMkernel a memória.|Nenhuma dimensão|
|MemoryActive|Active Directory de memória|Bytes|Média|A quantidade de memória utilizada pela VM no últimos pequeno período de tempo. Este é o número de "true" de quantidade de memória a VM atualmente tem necessidade de. Memória adicional, não utilizada pode ser trocada ou ballooned sem afetar o desempenho do convidado.|Nenhuma dimensão|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A utilização da CPU. Este valor é comunicado com 100%, que representa todos os núcleos de processador no sistema. Por exemplo, uma VM de 2 vias com 50% de um sistema de quatro núcleos completamente está a utilizar dois núcleos.|Nenhuma dimensão|
|PercentageCpuReady|Percentagem da CPU pronto|Milissegundos|Total|O tempo de preparação é o tempo gasto aguardando CPU(s) fiquem disponíveis no intervalo de atualização anteriores.|Nenhuma dimensão|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuPercentage|Percentagem de CPU|Percent|Média|Percentagem de CPU|Instância|
|MemoryPercentage|Percentagem de Memória|Percent|Média|Percentagem de Memória|Instância|
|DiskQueueLength|Comprimento da Fila de Discos|Count|Média|Comprimento da Fila de Discos|Instância|
|HttpQueueLength|Comprimento da Fila de http|Count|Média|Comprimento da Fila de http|Instância|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Saída de Dados|Bytes|Total|Saída de Dados|Instância|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (excluindo as funções)

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuTime|Tempo de CPU|Segundos|Total|Tempo de CPU|Instância|
|Pedidos|Pedidos|Count|Total|Pedidos|Instância|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Saída de Dados|Bytes|Total|Saída de Dados|Instância|
|Http101|Http 101|Count|Total|Http 101|Instância|
|Http2xx|Http 2xx|Count|Total|Http 2xx|Instância|
|Http3xx|Http 3xx|Count|Total|Http 3xx|Instância|
|Http401|Http 401|Count|Total|Http 401|Instância|
|Http403|Http 403|Count|Total|Http 403|Instância|
|Http404|Http 404|Count|Total|Http 404|Instância|
|Http406|Http 406|Count|Total|Http 406|Instância|
|Http4xx|Http 4xx|Count|Total|Http 4xx|Instância|
|Http5xx|Erros de Servidor http|Count|Total|Erros de Servidor http|Instância|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|AverageMemoryWorkingSet|Média do conjunto de trabalho de memória|Bytes|Média|Média do conjunto de trabalho de memória|Instância|
|AverageResponseTime|Tempo de Resposta Médio|Segundos|Média|Tempo de Resposta Médio|Instância|
|AppConnections|Ligações|Count|Média|Ligações|Instância|
|Identificadores|N.º de Identificadores|Count|Média|N.º de Identificadores|Instância|
|Threads|Número de Threads|Count|Média|Número de Threads|Instância|
|PrivateBytes|Bytes Privados|Bytes|Média|Bytes Privados|Instância|
|IoReadBytesPerSecond|Ler Bytes Por Segundo de E/S|BytesPerSecond|Total|Ler Bytes Por Segundo de E/S|Instância|
|IoWriteBytesPerSecond|Bytes de Escrita Por Segundo de E/S|BytesPerSecond|Total|Bytes de Escrita Por Segundo de E/S|Instância|
|IoOtherBytesPerSecond|Outros Bytes Por Segundo de E/S|BytesPerSecond|Total|Outros Bytes Por Segundo de E/S|Instância|
|IoReadOperationsPerSecond|Bytes de Leitura Por Segundo de E/S|BytesPerSecond|Total|Bytes de Leitura Por Segundo de E/S|Instância|
|IoWriteOperationsPerSecond|Operações de Escrita Por Segundo de E/S|BytesPerSecond|Total|Operações de Escrita Por Segundo de E/S|Instância|
|IoOtherOperationsPerSecond|Outras Operações Por Segundo de E/S|BytesPerSecond|Total|Outras Operações Por Segundo de E/S|Instância|
|RequestsInApplicationQueue|Pedidos na Fila de Aplicação|Count|Média|Pedidos na Fila de Aplicação|Instância|
|CurrentAssemblies|Assemblagens Actuais|Count|Média|Assemblagens Actuais|Instância|
|TotalAppDomains|Domínios de Aplicação Totais|Count|Média|Domínios de Aplicação Totais|Instância|
|TotalAppDomainsUnloaded|Domínios de Aplicação Totais Descarregados|Count|Média|Domínios de Aplicação Totais Descarregados|Instância|
|Gen0Collections|Libertações de Memória da Ger 0|Count|Total|Libertações de Memória da Ger 0|Instância|
|Gen1Collections|Libertações de Memória da Ger 1|Count|Total|Libertações de Memória da Ger 1|Instância|
|Gen2Collections|Libertações de Memória da Ger 2|Count|Total|Libertações de Memória da Ger 2|Instância|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funções)

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Saída de Dados|Bytes|Total|Saída de Dados|Instância|
|Http5xx|Erros de Servidor http|Count|Total|Erros de Servidor http|Instância|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|AverageMemoryWorkingSet|Média do conjunto de trabalho de memória|Bytes|Média|Média do conjunto de trabalho de memória|Instância|
|FunctionExecutionUnits|Unidades de Execução de Funções|MB / milissegundos|Total|[Unidades de execução de função](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instância|
|FunctionExecutionCount|Contagem de Execuções de Função|Count|Total|Contagem de Execuções de Função|Instância|
|PrivateBytes|Bytes Privados|Bytes|Média|Bytes Privados|Instância|
|IoReadBytesPerSecond|Ler Bytes Por Segundo de E/S|BytesPerSecond|Total|Ler Bytes Por Segundo de E/S|Instância|
|IoWriteBytesPerSecond|Bytes de Escrita Por Segundo de E/S|BytesPerSecond|Total|Bytes de Escrita Por Segundo de E/S|Instância|
|IoOtherBytesPerSecond|Outros Bytes Por Segundo de E/S|BytesPerSecond|Total|Outros Bytes Por Segundo de E/S|Instância|
|IoReadOperationsPerSecond|Bytes de Leitura Por Segundo de E/S|BytesPerSecond|Total|Bytes de Leitura Por Segundo de E/S|Instância|
|IoWriteOperationsPerSecond|Operações de Escrita Por Segundo de E/S|BytesPerSecond|Total|Operações de Escrita Por Segundo de E/S|Instância|
|IoOtherOperationsPerSecond|Outras Operações Por Segundo de E/S|BytesPerSecond|Total|Outras Operações Por Segundo de E/S|Instância|
|RequestsInApplicationQueue|Pedidos na Fila de Aplicação|Count|Média|Pedidos na Fila de Aplicação|Instância|
|CurrentAssemblies|Assemblagens Actuais|Count|Média|Assemblagens Actuais|Instância|
|TotalAppDomains|Domínios de Aplicação Totais|Count|Média|Domínios de Aplicação Totais|Instância|
|TotalAppDomainsUnloaded|Domínios de Aplicação Totais Descarregados|Count|Média|Domínios de Aplicação Totais Descarregados|Instância|
|Gen0Collections|Libertações de Memória da Ger 0|Count|Total|Libertações de Memória da Ger 0|Instância|
|Gen1Collections|Libertações de Memória da Ger 1|Count|Total|Libertações de Memória da Ger 1|Instância|
|Gen2Collections|Libertações de Memória da Ger 2|Count|Total|Libertações de Memória da Ger 2|Instância|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuTime|Tempo de CPU|Segundos|Total|Tempo de CPU|Instância|
|Pedidos|Pedidos|Count|Total|Pedidos|Instância|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Saída de Dados|Bytes|Total|Saída de Dados|Instância|
|Http101|Http 101|Count|Total|Http 101|Instância|
|Http2xx|Http 2xx|Count|Total|Http 2xx|Instância|
|Http3xx|Http 3xx|Count|Total|Http 3xx|Instância|
|Http401|Http 401|Count|Total|Http 401|Instância|
|Http403|Http 403|Count|Total|Http 403|Instância|
|Http404|Http 404|Count|Total|Http 404|Instância|
|Http406|Http 406|Count|Total|Http 406|Instância|
|Http4xx|Http 4xx|Count|Total|Http 4xx|Instância|
|Http5xx|Erros de Servidor http|Count|Total|Erros de Servidor http|Instância|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|AverageMemoryWorkingSet|Média do conjunto de trabalho de memória|Bytes|Média|Média do conjunto de trabalho de memória|Instância|
|AverageResponseTime|Tempo de Resposta Médio|Segundos|Média|Tempo de Resposta Médio|Instância|
|FunctionExecutionUnits|Unidades de Execução de Funções|Count|Total|Unidades de Execução de Funções|Instância|
|FunctionExecutionCount|Contagem de Execuções de Função|Count|Total|Contagem de Execuções de Função|Instância|
|AppConnections|Ligações|Count|Média|Ligações|Instância|
|Identificadores|N.º de Identificadores|Count|Média|N.º de Identificadores|Instância|
|Threads|Número de Threads|Count|Média|Número de Threads|Instância|
|PrivateBytes|Bytes Privados|Bytes|Média|Bytes Privados|Instância|
|IoReadBytesPerSecond|Ler Bytes Por Segundo de E/S|BytesPerSecond|Total|Ler Bytes Por Segundo de E/S|Instância|
|IoWriteBytesPerSecond|Bytes de Escrita Por Segundo de E/S|BytesPerSecond|Total|Bytes de Escrita Por Segundo de E/S|Instância|
|IoOtherBytesPerSecond|Outros Bytes Por Segundo de E/S|BytesPerSecond|Total|Outros Bytes Por Segundo de E/S|Instância|
|IoReadOperationsPerSecond|Bytes de Leitura Por Segundo de E/S|BytesPerSecond|Total|Bytes de Leitura Por Segundo de E/S|Instância|
|IoWriteOperationsPerSecond|Operações de Escrita Por Segundo de E/S|BytesPerSecond|Total|Operações de Escrita Por Segundo de E/S|Instância|
|IoOtherOperationsPerSecond|Outras Operações Por Segundo de E/S|BytesPerSecond|Total|Outras Operações Por Segundo de E/S|Instância|
|RequestsInApplicationQueue|Pedidos na Fila de Aplicação|Count|Média|Pedidos na Fila de Aplicação|Instância|
|CurrentAssemblies|Assemblagens Actuais|Count|Média|Assemblagens Actuais|Instância|
|TotalAppDomains|Domínios de Aplicação Totais|Count|Média|Domínios de Aplicação Totais|Instância|
|TotalAppDomainsUnloaded|Domínios de Aplicação Totais Descarregados|Count|Média|Domínios de Aplicação Totais Descarregados|Instância|
|Gen0Collections|Libertações de Memória da Ger 0|Count|Total|Libertações de Memória da Ger 0|Instância|
|Gen1Collections|Libertações de Memória da Ger 1|Count|Total|Libertações de Memória da Ger 1|Instância|
|Gen2Collections|Libertações de Memória da Ger 2|Count|Total|Libertações de Memória da Ger 2|Instância|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Pedidos|Pedidos|Count|Total|Pedidos|Instância|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Saída de Dados|Bytes|Total|Saída de Dados|Instância|
|Http101|Http 101|Count|Total|Http 101|Instância|
|Http2xx|Http 2xx|Count|Total|Http 2xx|Instância|
|Http3xx|Http 3xx|Count|Total|Http 3xx|Instância|
|Http401|Http 401|Count|Total|Http 401|Instância|
|Http403|Http 403|Count|Total|Http 403|Instância|
|Http404|Http 404|Count|Total|Http 404|Instância|
|Http406|Http 406|Count|Total|Http 406|Instância|
|Http4xx|Http 4xx|Count|Total|Http 4xx|Instância|
|Http5xx|Erros de Servidor http|Count|Total|Erros de Servidor http|Instância|
|AverageResponseTime|Tempo de Resposta Médio|Segundos|Média|Tempo de Resposta Médio|Instância|
|CpuPercentage|Percentagem de CPU|Percent|Média|Percentagem de CPU|Instância|
|MemoryPercentage|Percentagem de Memória|Percent|Média|Percentagem de Memória|Instância|
|DiskQueueLength|Comprimento da Fila de Discos|Count|Média|Comprimento da Fila de Discos|Instância|
|HttpQueueLength|Comprimento da Fila de http|Count|Média|Comprimento da Fila de http|Instância|
|ActiveRequests|Pedidos Ativos|Count|Total|Pedidos Ativos|Instância|
|TotalFrontEnds|Total de Front Ends|Count|Média|Total de Front Ends|Nenhuma dimensão|
|SmallAppServicePlanInstances|Trabalhadores do Plano de Serviço de Aplicações de Pequena Dimensão|Count|Média|Trabalhadores do Plano de Serviço de Aplicações de Pequena Dimensão|Nenhuma dimensão|
|MediumAppServicePlanInstances|Trabalhadores do Plano de Serviço de Aplicações de Média Dimensão|Count|Média|Trabalhadores do Plano de Serviço de Aplicações de Média Dimensão|Nenhuma dimensão|
|LargeAppServicePlanInstances|Trabalhadores do Plano de Serviço de Aplicações de Grande Dimensão|Count|Média|Trabalhadores do Plano de Serviço de Aplicações de Grande Dimensão|Nenhuma dimensão|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|WorkersTotal|Trabalhadores Totais|Count|Média|Trabalhadores Totais|Nenhuma dimensão|
|WorkersAvailable|Trabalhadores Disponíveis|Count|Média|Trabalhadores Disponíveis|Nenhuma dimensão|
|WorkersUsed|Trabalhadores Utilizados|Count|Média|Trabalhadores Utilizados|Nenhuma dimensão|
|CpuPercentage|Percentagem de CPU|Percent|Média|Percentagem de CPU|Instância|
|MemoryPercentage|Percentagem de Memória|Percent|Média|Percentagem de Memória|Instância|

## <a name="next-steps"></a>Passos Seguintes
* [Leia sobre as métricas no Azure Monitor](data-platform.md)
* [Criar alertas em métricas](alerts-overview.md)
* [Exportar métricas para o armazenamento, o Hub de eventos ou o Log Analytics](diagnostic-logs-overview.md)
