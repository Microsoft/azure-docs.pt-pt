---
title: Azure Monitor supported metrics by resource type
description: List of metrics available for each resource type with Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 30e2b880f32f896098778942deb67d7ced9f5c2d
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484213"
---
# <a name="supported-metrics-with-azure-monitor"></a>Supported metrics with Azure Monitor

Azure Monitor provides several ways to interact with metrics, including charting them in the portal, accessing them through the REST API, or querying them using PowerShell or CLI. Below is a complete list of all metrics currently available with Azure Monitor's metric pipeline. Other metrics may be available in the portal or using legacy APIs. This list below only includes metrics available using the consolidated Azure Monitor metric pipeline. To query for and access these metrics please use the [2018-01-01 api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Atualmente, o envio de métricas multidimensionais através das definições de diagnóstico não é suportado. As métricas com dimensões são exportadas como métricas dimensionais simples e agregadas em valores de dimensões.
>
> *Por exemplo*: a métrica “Mensagens Recebidas” num hub do Hub de Eventos pode ser explorada e representada ao nível da linha. No entanto, se for exportada através das definições de diagnóstico, a métrica será representada como todas as mensagens recebidas em todas as filas do hub do Hub de Eventos.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|qpu_metric|QPU|Contagem|Média|QPU. Range 0-100 for S1, 0-200 for S2 and 0-400 for S4|ServerResourceType|
|memory_metric|Memória|Bytes|Média|Memory. Range 0-25 GB for S1, 0-50 GB for S2 and 0-100 GB for S4|ServerResourceType|
|private_bytes_metric|Private Bytes |Bytes|Média|The total amount of memory the Analysis Services engine process and Mashup container processes have allocated, not including memory shared with other processes.|ServerResourceType|
|virtual_bytes_metric|Virtual Bytes |Bytes|Média|The current size of the virtual address space that Analysis Services engine process and Mashup container processes are using.|ServerResourceType|
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
|Quota|Memory: Quota|Bytes|Média|Current memory quota, in bytes. Memory quota is also known as a memory grant or memory reservation.|ServerResourceType|
|QuotaBlocked|Memory: Quota Blocked|Contagem|Média|Current number of quota requests that are blocked until other memory quotas are freed.|ServerResourceType|
|VertiPaqNonpaged|Memory: VertiPaq Nonpaged|Bytes|Média|Bytes of memory locked in the working set for use by the in-memory engine.|ServerResourceType|
|VertiPaqPaged|Memory: VertiPaq Paged|Bytes|Média|Bytes of paged memory in use for in-memory data.|ServerResourceType|
|RowsReadPerSec|Processing: Rows read per sec|CountPerSecond|Média|Rate of rows read from all relational databases.|ServerResourceType|
|RowsConvertedPerSec|Processing: Rows converted per sec|CountPerSecond|Média|Rate of rows converted during processing.|ServerResourceType|
|RowsWrittenPerSec|Processing: Rows written per sec|CountPerSecond|Média|Rate of rows written during processing.|ServerResourceType|
|CommandPoolBusyThreads|Threads: Command pool busy threads|Contagem|Média|Number of busy threads in the command thread pool.|ServerResourceType|
|CommandPoolIdleThreads|Threads: Command pool idle threads|Contagem|Média|Number of idle threads in the command thread pool.|ServerResourceType|
|LongParsingBusyThreads|Threads: Long parsing busy threads|Contagem|Média|Number of busy threads in the long parsing thread pool.|ServerResourceType|
|LongParsingIdleThreads|Threads: Long parsing idle threads|Contagem|Média|Number of idle threads in the long parsing thread pool.|ServerResourceType|
|LongParsingJobQueueLength|Threads: Long parsing job queue length|Contagem|Média|Number of jobs in the queue of the long parsing thread pool.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Threads: Processing pool busy I/O job threads|Contagem|Média|Number of threads running I/O jobs in the processing thread pool.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Threads: Processing pool busy non-I/O threads|Contagem|Média|Number of threads running non-I/O jobs in the processing thread pool.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Threads: Processing pool I/O job queue length|Contagem|Média|Number of I/O jobs in the queue of the processing thread pool.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Threads: Processing pool idle I/O job threads|Contagem|Média|Number of idle threads for I/O jobs in the processing thread pool.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Threads: Processing pool idle non-I/O threads|Contagem|Média|Number of idle threads in the processing thread pool dedicated to non-I/O jobs.|ServerResourceType|
|QueryPoolIdleThreads|Threads: Query pool idle threads|Contagem|Média|Number of idle threads for I/O jobs in the processing thread pool.|ServerResourceType|
|QueryPoolJobQueueLength|Threads: Query pool job queue length|Contagem|Média|Number of jobs in the queue of the query thread pool.|ServerResourceType|
|ShortParsingBusyThreads|Threads: Short parsing busy threads|Contagem|Média|Number of busy threads in the short parsing thread pool.|ServerResourceType|
|ShortParsingIdleThreads|Threads: Short parsing idle threads|Contagem|Média|Number of idle threads in the short parsing thread pool.|ServerResourceType|
|ShortParsingJobQueueLength|Threads: Short parsing job queue length|Contagem|Média|Number of jobs in the queue of the short parsing thread pool.|ServerResourceType|
|memory_thrashing_metric|Memory Thrashing|Percentagem|Média|Average memory thrashing.|ServerResourceType|
|mashup_engine_qpu_metric|M Engine QPU|Contagem|Média|QPU usage by mashup engine processes|ServerResourceType|
|mashup_engine_memory_metric|M Engine Memory|Bytes|Média|Memory usage by mashup engine processes|ServerResourceType|
|mashup_engine_private_bytes_metric|M Engine Private Bytes |Bytes|Média|The total amount of memory Mashup container processes have allocated, not including memory shared with other processes.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M Engine Virtual Bytes |Bytes|Média|The current size of the virtual address space Mashup container processes are using.|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|Pedidos|Pedidos|Contagem|Total|The total number of gateway requests in a given period. It can be sliced by various dimensions to help you diagnose issues. |Location, BackendResponseCode, LastErrorReason, GatewayResponseCode|
|TotalRequests|Total Gateway Requests|Contagem|Total|The total number of gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric. |Location, Hostname|
|SuccessfulRequests|Successful Gateway Requests|Contagem|Total|The total number of successful gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric.|Location, Hostname|
|UnauthorizedRequests|Unauthorized Gateway Requests|Contagem|Total| The total number of unauthorized gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric.|Location, Hostname|
|FailedRequests|Failed Gateway Requests|Contagem|Total|The total number of failed gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric.|Location, Hostname|
|OtherRequests|Other Gateway Requests|Contagem|Total|The total number of gateway requests in a given period that do not fall into the successful, unauthorized, or failed categories. This metric has been deprecated, we recommend using the new `Requests` metric. |Location, Hostname|
|Duração|Overall Duration of Gateway Requests|Milliseconds|Média|The time between when API Management receives a request from a client and when it returns a response to the client.|Location, Hostname|
|Capacidade|Capacidade|Percentagem|Média|Indicator of load on an API Management instance for making informed decisions whether to scale the instance to accommodate more load.|Localização|
|EventHubTotalEvents|Total EventHub Events|Contagem|Total|The total number of events sent to EventHub from API Management in a given period.|Localização|
|EventHubSuccessfulEvents|Successful EventHub Events|Contagem|Total|The total number of successful EventHub events in a given period.|Localização|
|EventHubTotalFailedEvents|Failed EventHub Events|Contagem|Total|The total number of failed EventHub events in a given period.|Localização|
|EventHubRejectedEvents|Rejected EventHub Events|Contagem|Total|The total number of rejected EventHub events (wrong configuration or unauthorized) in a given period.|Localização|
|EventHubThrottledEvents|Throttled EventHub Events|Contagem|Total|The total number of throttled EventHub events in a given period.|Localização|
|EventHubTimedoutEvents|Timed Out EventHub Events|Contagem|Total|The total number of timed out EventHub events in a given period.|Localização|
|EventHubDroppedEvents|Dropped EventHub Events|Contagem|Total|The total number of events skipped because of queue size limit reached in a given period.|Localização|
|EventHubTotalBytesSent|Size of EventHub Events|Bytes|Total|The total size of EventHub events in bytes in a given period.|Localização|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalJob|Total Jobs|Contagem|Total|The total number of jobs|Runbook, Status|
|TotalUpdateDeploymentRuns|Total Update Deployment Runs|Contagem|Total|Total software update deployment runs|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|Total Update Deployment Machine Runs|Contagem|Total|Total software update deployment machine runs in a software update deployment run|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|CoreCount|Dedicated Core Count|Contagem|Total|Total number of dedicated cores in the batch account|No Dimensions|
|TotalNodeCount|Dedicated Node Count|Contagem|Total|Total number of dedicated nodes in the batch account|No Dimensions|
|LowPriorityCoreCount|LowPriority Core Count|Contagem|Total|Total number of low-priority cores in the batch account|No Dimensions|
|TotalLowPriorityNodeCount|Low-Priority Node Count|Contagem|Total|Total number of low-priority nodes in the batch account|No Dimensions|
|CreatingNodeCount|Creating Node Count|Contagem|Total|Number of nodes being created|No Dimensions|
|StartingNodeCount|Starting Node Count|Contagem|Total|Number of nodes starting|No Dimensions|
|WaitingForStartTaskNodeCount|Waiting For Start Task Node Count|Contagem|Total|Number of nodes waiting for the Start Task to complete|No Dimensions|
|StartTaskFailedNodeCount|Start Task Failed Node Count|Contagem|Total|Number of nodes where the Start Task has failed|No Dimensions|
|IdleNodeCount|Idle Node Count|Contagem|Total|Number of idle nodes|No Dimensions|
|OfflineNodeCount|Offline Node Count|Contagem|Total|Number of offline nodes|No Dimensions|
|RebootingNodeCount|Rebooting Node Count|Contagem|Total|Number of rebooting nodes|No Dimensions|
|ReimagingNodeCount|Reimaging Node Count|Contagem|Total|Number of reimaging nodes|No Dimensions|
|RunningNodeCount|Running Node Count|Contagem|Total|Number of running nodes|No Dimensions|
|LeavingPoolNodeCount|Leaving Pool Node Count|Contagem|Total|Number of nodes leaving the Pool|No Dimensions|
|UnusableNodeCount|Unusable Node Count|Contagem|Total|Number of unusable nodes|No Dimensions|
|PreemptedNodeCount|Preempted Node Count|Contagem|Total|Number of preempted nodes|No Dimensions|
|TaskStartEvent|Task Start Events|Contagem|Total|Total number of tasks that have started|No Dimensions|
|TaskCompleteEvent|Task Complete Events|Contagem|Total|Total number of tasks that have completed|No Dimensions|
|TaskFailEvent|Task Fail Events|Contagem|Total|Total number of tasks that have completed in a failed state|No Dimensions|
|PoolCreateEvent|Pool Create Events|Contagem|Total|Total number of pools that have been created|No Dimensions|
|PoolResizeStartEvent|Pool Resize Start Events|Contagem|Total|Total number of pool resizes that have started|No Dimensions|
|PoolResizeCompleteEvent|Pool Resize Complete Events|Contagem|Total|Total number of pool resizes that have completed|No Dimensions|
|PoolDeleteStartEvent|Pool Delete Start Events|Contagem|Total|Total number of pool deletes that have started|No Dimensions|
|PoolDeleteCompleteEvent|Pool Delete Complete Events|Contagem|Total|Total number of pool deletes that have completed|No Dimensions|
|JobDeleteCompleteEvent|Job Delete Complete Events|Contagem|Total|Total number of jobs that have been successfully deleted.|No Dimensions|
|JobDeleteStartEvent|Job Delete Start Events|Contagem|Total|Total number of jobs that have been requested to be deleted.|No Dimensions|
|JobDisableCompleteEvent|Job Disable Complete Events|Contagem|Total|Total number of jobs that have been successfully disabled.|No Dimensions|
|JobDisableStartEvent|Job Disable Start Events|Contagem|Total|Total number of jobs that have been requested to be disabled.|No Dimensions|
|JobStartEvent|Job Start Events|Contagem|Total|Total number of jobs that have been successfully started.|No Dimensions|
|JobTerminateCompleteEvent|Job Terminate Complete Events|Contagem|Total|Total number of jobs that have been successfully terminated.|No Dimensions|
|JobTerminateStartEvent|Job Terminate Start Events|Contagem|Total|Total number of jobs that have been requested to be terminated.|No Dimensions|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|connectedclients|Connected Clients|Contagem|Máximo||ShardId|
|totalcommandsprocessed|Total Operations|Contagem|Total||ShardId|
|cachehits|Cache Hits|Contagem|Total||ShardId|
|cachemisses|Cache Misses|Contagem|Total||ShardId|
|getcommands|Gets|Contagem|Total||ShardId|
|setcommands|Sets|Contagem|Total||ShardId|
|operationsPerSecond|Operations Per Second|Contagem|Máximo||ShardId|
|evictedkeys|Evicted Keys|Contagem|Total||ShardId|
|totalkeys|Total Keys|Contagem|Máximo||ShardId|
|expiredkeys|Expired Keys|Contagem|Total||ShardId|
|usedmemory|Used Memory|Bytes|Máximo||ShardId|
|usedmemorypercentage|Used Memory Percentage|Percentagem|Máximo||ShardId|
|usedmemoryRss|Used Memory RSS|Bytes|Máximo||ShardId|
|serverLoad|Server Load|Percentagem|Máximo||ShardId|
|cacheWrite|Cache Write|BytesPerSecond|Máximo||ShardId|
|cacheRead|Cache Read|BytesPerSecond|Máximo||ShardId|
|percentProcessorTime|CPU|Percentagem|Máximo||ShardId|
|cacheLatency|Cache Latency Microseconds (Preview)|Contagem|Média||ShardId, SampleType|
|erros|Erros|Contagem|Máximo||ShardId, ErrorType|
|connectedclients0|Connected Clients (Shard 0)|Contagem|Máximo||No Dimensions|
|totalcommandsprocessed0|Total Operations (Shard 0)|Contagem|Total||No Dimensions|
|cachehits0|Cache Hits (Shard 0)|Contagem|Total||No Dimensions|
|cachemisses0|Cache Misses (Shard 0)|Contagem|Total||No Dimensions|
|getcommands0|Gets (Shard 0)|Contagem|Total||No Dimensions|
|setcommands0|Sets (Shard 0)|Contagem|Total||No Dimensions|
|operationsPerSecond0|Operations Per Second (Shard 0)|Contagem|Máximo||No Dimensions|
|evictedkeys0|Evicted Keys (Shard 0)|Contagem|Total||No Dimensions|
|totalkeys0|Total Keys (Shard 0)|Contagem|Máximo||No Dimensions|
|expiredkeys0|Expired Keys (Shard 0)|Contagem|Total||No Dimensions|
|usedmemory0|Used Memory (Shard 0)|Bytes|Máximo||No Dimensions|
|usedmemoryRss0|Used Memory RSS (Shard 0)|Bytes|Máximo||No Dimensions|
|serverLoad0|Server Load (Shard 0)|Percentagem|Máximo||No Dimensions|
|cacheWrite0|Cache Write (Shard 0)|BytesPerSecond|Máximo||No Dimensions|
|cacheRead0|Cache Read (Shard 0)|BytesPerSecond|Máximo||No Dimensions|
|percentProcessorTime0|CPU (Shard 0)|Percentagem|Máximo||No Dimensions|
|connectedclients1|Connected Clients (Shard 1)|Contagem|Máximo||No Dimensions|
|totalcommandsprocessed1|Total Operations (Shard 1)|Contagem|Total||No Dimensions|
|cachehits1|Cache Hits (Shard 1)|Contagem|Total||No Dimensions|
|cachemisses1|Cache Misses (Shard 1)|Contagem|Total||No Dimensions|
|getcommands1|Gets (Shard 1)|Contagem|Total||No Dimensions|
|setcommands1|Sets (Shard 1)|Contagem|Total||No Dimensions|
|operationsPerSecond1|Operations Per Second (Shard 1)|Contagem|Máximo||No Dimensions|
|evictedkeys1|Evicted Keys (Shard 1)|Contagem|Total||No Dimensions|
|totalkeys1|Total Keys (Shard 1)|Contagem|Máximo||No Dimensions|
|expiredkeys1|Expired Keys (Shard 1)|Contagem|Total||No Dimensions|
|usedmemory1|Used Memory (Shard 1)|Bytes|Máximo||No Dimensions|
|usedmemoryRss1|Used Memory RSS (Shard 1)|Bytes|Máximo||No Dimensions|
|serverLoad1|Server Load (Shard 1)|Percentagem|Máximo||No Dimensions|
|cacheWrite1|Cache Write (Shard 1)|BytesPerSecond|Máximo||No Dimensions|
|cacheRead1|Cache Read (Shard 1)|BytesPerSecond|Máximo||No Dimensions|
|percentProcessorTime1|CPU (Shard 1)|Percentagem|Máximo||No Dimensions|
|connectedclients2|Connected Clients (Shard 2)|Contagem|Máximo||No Dimensions|
|totalcommandsprocessed2|Total Operations (Shard 2)|Contagem|Total||No Dimensions|
|cachehits2|Cache Hits (Shard 2)|Contagem|Total||No Dimensions|
|cachemisses2|Cache Misses (Shard 2)|Contagem|Total||No Dimensions|
|getcommands2|Gets (Shard 2)|Contagem|Total||No Dimensions|
|setcommands2|Sets (Shard 2)|Contagem|Total||No Dimensions|
|operationsPerSecond2|Operations Per Second (Shard 2)|Contagem|Máximo||No Dimensions|
|evictedkeys2|Evicted Keys (Shard 2)|Contagem|Total||No Dimensions|
|totalkeys2|Total Keys (Shard 2)|Contagem|Máximo||No Dimensions|
|expiredkeys2|Expired Keys (Shard 2)|Contagem|Total||No Dimensions|
|usedmemory2|Used Memory (Shard 2)|Bytes|Máximo||No Dimensions|
|usedmemoryRss2|Used Memory RSS (Shard 2)|Bytes|Máximo||No Dimensions|
|serverLoad2|Server Load (Shard 2)|Percentagem|Máximo||No Dimensions|
|cacheWrite2|Cache Write (Shard 2)|BytesPerSecond|Máximo||No Dimensions|
|cacheRead2|Cache Read (Shard 2)|BytesPerSecond|Máximo||No Dimensions|
|percentProcessorTime2|CPU (Shard 2)|Percentagem|Máximo||No Dimensions|
|connectedclients3|Connected Clients (Shard 3)|Contagem|Máximo||No Dimensions|
|totalcommandsprocessed3|Total Operations (Shard 3)|Contagem|Total||No Dimensions|
|cachehits3|Cache Hits (Shard 3)|Contagem|Total||No Dimensions|
|cachemisses3|Cache Misses (Shard 3)|Contagem|Total||No Dimensions|
|getcommands3|Gets (Shard 3)|Contagem|Total||No Dimensions|
|setcommands3|Sets (Shard 3)|Contagem|Total||No Dimensions|
|operationsPerSecond3|Operations Per Second (Shard 3)|Contagem|Máximo||No Dimensions|
|evictedkeys3|Evicted Keys (Shard 3)|Contagem|Total||No Dimensions|
|totalkeys3|Total Keys (Shard 3)|Contagem|Máximo||No Dimensions|
|expiredkeys3|Expired Keys (Shard 3)|Contagem|Total||No Dimensions|
|usedmemory3|Used Memory (Shard 3)|Bytes|Máximo||No Dimensions|
|usedmemoryRss3|Used Memory RSS (Shard 3)|Bytes|Máximo||No Dimensions|
|serverLoad3|Server Load (Shard 3)|Percentagem|Máximo||No Dimensions|
|cacheWrite3|Cache Write (Shard 3)|BytesPerSecond|Máximo||No Dimensions|
|cacheRead3|Cache Read (Shard 3)|BytesPerSecond|Máximo||No Dimensions|
|percentProcessorTime3|CPU (Shard 3)|Percentagem|Máximo||No Dimensions|
|connectedclients4|Connected Clients (Shard 4)|Contagem|Máximo||No Dimensions|
|totalcommandsprocessed4|Total Operations (Shard 4)|Contagem|Total||No Dimensions|
|cachehits4|Cache Hits (Shard 4)|Contagem|Total||No Dimensions|
|cachemisses4|Cache Misses (Shard 4)|Contagem|Total||No Dimensions|
|getcommands4|Gets (Shard 4)|Contagem|Total||No Dimensions|
|setcommands4|Sets (Shard 4)|Contagem|Total||No Dimensions|
|operationsPerSecond4|Operations Per Second (Shard 4)|Contagem|Máximo||No Dimensions|
|evictedkeys4|Evicted Keys (Shard 4)|Contagem|Total||No Dimensions|
|totalkeys4|Total Keys (Shard 4)|Contagem|Máximo||No Dimensions|
|expiredkeys4|Expired Keys (Shard 4)|Contagem|Total||No Dimensions|
|usedmemory4|Used Memory (Shard 4)|Bytes|Máximo||No Dimensions|
|usedmemoryRss4|Used Memory RSS (Shard 4)|Bytes|Máximo||No Dimensions|
|serverLoad4|Server Load (Shard 4)|Percentagem|Máximo||No Dimensions|
|cacheWrite4|Cache Write (Shard 4)|BytesPerSecond|Máximo||No Dimensions|
|cacheRead4|Cache Read (Shard 4)|BytesPerSecond|Máximo||No Dimensions|
|percentProcessorTime4|CPU (Shard 4)|Percentagem|Máximo||No Dimensions|
|connectedclients5|Connected Clients (Shard 5)|Contagem|Máximo||No Dimensions|
|totalcommandsprocessed5|Total Operations (Shard 5)|Contagem|Total||No Dimensions|
|cachehits5|Cache Hits (Shard 5)|Contagem|Total||No Dimensions|
|cachemisses5|Cache Misses (Shard 5)|Contagem|Total||No Dimensions|
|getcommands5|Gets (Shard 5)|Contagem|Total||No Dimensions|
|setcommands5|Sets (Shard 5)|Contagem|Total||No Dimensions|
|operationsPerSecond5|Operations Per Second (Shard 5)|Contagem|Máximo||No Dimensions|
|evictedkeys5|Evicted Keys (Shard 5)|Contagem|Total||No Dimensions|
|totalkeys5|Total Keys (Shard 5)|Contagem|Máximo||No Dimensions|
|expiredkeys5|Expired Keys (Shard 5)|Contagem|Total||No Dimensions|
|usedmemory5|Used Memory (Shard 5)|Bytes|Máximo||No Dimensions|
|usedmemoryRss5|Used Memory RSS (Shard 5)|Bytes|Máximo||No Dimensions|
|serverLoad5|Server Load (Shard 5)|Percentagem|Máximo||No Dimensions|
|cacheWrite5|Cache Write (Shard 5)|BytesPerSecond|Máximo||No Dimensions|
|cacheRead5|Cache Read (Shard 5)|BytesPerSecond|Máximo||No Dimensions|
|percentProcessorTime5|CPU (Shard 5)|Percentagem|Máximo||No Dimensions|
|connectedclients6|Connected Clients (Shard 6)|Contagem|Máximo||No Dimensions|
|totalcommandsprocessed6|Total Operations (Shard 6)|Contagem|Total||No Dimensions|
|cachehits6|Cache Hits (Shard 6)|Contagem|Total||No Dimensions|
|cachemisses6|Cache Misses (Shard 6)|Contagem|Total||No Dimensions|
|getcommands6|Gets (Shard 6)|Contagem|Total||No Dimensions|
|setcommands6|Sets (Shard 6)|Contagem|Total||No Dimensions|
|operationsPerSecond6|Operations Per Second (Shard 6)|Contagem|Máximo||No Dimensions|
|evictedkeys6|Evicted Keys (Shard 6)|Contagem|Total||No Dimensions|
|totalkeys6|Total Keys (Shard 6)|Contagem|Máximo||No Dimensions|
|expiredkeys6|Expired Keys (Shard 6)|Contagem|Total||No Dimensions|
|usedmemory6|Used Memory (Shard 6)|Bytes|Máximo||No Dimensions|
|usedmemoryRss6|Used Memory RSS (Shard 6)|Bytes|Máximo||No Dimensions|
|serverLoad6|Server Load (Shard 6)|Percentagem|Máximo||No Dimensions|
|cacheWrite6|Cache Write (Shard 6)|BytesPerSecond|Máximo||No Dimensions|
|cacheRead6|Cache Read (Shard 6)|BytesPerSecond|Máximo||No Dimensions|
|percentProcessorTime6|CPU (Shard 6)|Percentagem|Máximo||No Dimensions|
|connectedclients7|Connected Clients (Shard 7)|Contagem|Máximo||No Dimensions|
|totalcommandsprocessed7|Total Operations (Shard 7)|Contagem|Total||No Dimensions|
|cachehits7|Cache Hits (Shard 7)|Contagem|Total||No Dimensions|
|cachemisses7|Cache Misses (Shard 7)|Contagem|Total||No Dimensions|
|getcommands7|Gets (Shard 7)|Contagem|Total||No Dimensions|
|setcommands7|Sets (Shard 7)|Contagem|Total||No Dimensions|
|operationsPerSecond7|Operations Per Second (Shard 7)|Contagem|Máximo||No Dimensions|
|evictedkeys7|Evicted Keys (Shard 7)|Contagem|Total||No Dimensions|
|totalkeys7|Total Keys (Shard 7)|Contagem|Máximo||No Dimensions|
|expiredkeys7|Expired Keys (Shard 7)|Contagem|Total||No Dimensions|
|usedmemory7|Used Memory (Shard 7)|Bytes|Máximo||No Dimensions|
|usedmemoryRss7|Used Memory RSS (Shard 7)|Bytes|Máximo||No Dimensions|
|serverLoad7|Server Load (Shard 7)|Percentagem|Máximo||No Dimensions|
|cacheWrite7|Cache Write (Shard 7)|BytesPerSecond|Máximo||No Dimensions|
|cacheRead7|Cache Read (Shard 7)|BytesPerSecond|Máximo||No Dimensions|
|percentProcessorTime7|CPU (Shard 7)|Percentagem|Máximo||No Dimensions|
|connectedclients8|Connected Clients (Shard 8)|Contagem|Máximo||No Dimensions|
|totalcommandsprocessed8|Total Operations (Shard 8)|Contagem|Total||No Dimensions|
|cachehits8|Cache Hits (Shard 8)|Contagem|Total||No Dimensions|
|cachemisses8|Cache Misses (Shard 8)|Contagem|Total||No Dimensions|
|getcommands8|Gets (Shard 8)|Contagem|Total||No Dimensions|
|setcommands8|Sets (Shard 8)|Contagem|Total||No Dimensions|
|operationsPerSecond8|Operations Per Second (Shard 8)|Contagem|Máximo||No Dimensions|
|evictedkeys8|Evicted Keys (Shard 8)|Contagem|Total||No Dimensions|
|totalkeys8|Total Keys (Shard 8)|Contagem|Máximo||No Dimensions|
|expiredkeys8|Expired Keys (Shard 8)|Contagem|Total||No Dimensions|
|usedmemory8|Used Memory (Shard 8)|Bytes|Máximo||No Dimensions|
|usedmemoryRss8|Used Memory RSS (Shard 8)|Bytes|Máximo||No Dimensions|
|serverLoad8|Server Load (Shard 8)|Percentagem|Máximo||No Dimensions|
|cacheWrite8|Cache Write (Shard 8)|BytesPerSecond|Máximo||No Dimensions|
|cacheRead8|Cache Read (Shard 8)|BytesPerSecond|Máximo||No Dimensions|
|percentProcessorTime8|CPU (Shard 8)|Percentagem|Máximo||No Dimensions|
|connectedclients9|Connected Clients (Shard 9)|Contagem|Máximo||No Dimensions|
|totalcommandsprocessed9|Total Operations (Shard 9)|Contagem|Total||No Dimensions|
|cachehits9|Cache Hits (Shard 9)|Contagem|Total||No Dimensions|
|cachemisses9|Cache Misses (Shard 9)|Contagem|Total||No Dimensions|
|getcommands9|Gets (Shard 9)|Contagem|Total||No Dimensions|
|setcommands9|Sets (Shard 9)|Contagem|Total||No Dimensions|
|operationsPerSecond9|Operations Per Second (Shard 9)|Contagem|Máximo||No Dimensions|
|evictedkeys9|Evicted Keys (Shard 9)|Contagem|Total||No Dimensions|
|totalkeys9|Total Keys (Shard 9)|Contagem|Máximo||No Dimensions|
|expiredkeys9|Expired Keys (Shard 9)|Contagem|Total||No Dimensions|
|usedmemory9|Used Memory (Shard 9)|Bytes|Máximo||No Dimensions|
|usedmemoryRss9|Used Memory RSS (Shard 9)|Bytes|Máximo||No Dimensions|
|serverLoad9|Server Load (Shard 9)|Percentagem|Máximo||No Dimensions|
|cacheWrite9|Cache Write (Shard 9)|BytesPerSecond|Máximo||No Dimensions|
|cacheRead9|Cache Read (Shard 9)|BytesPerSecond|Máximo||No Dimensions|
|percentProcessorTime9|CPU (Shard 9)|Percentagem|Máximo||No Dimensions|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|The percentage of allocated compute units that are currently in use by the Virtual Machine(s).|No Dimensions|
|Entrada na Rede|Entrada na Rede|Bytes|Total|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic).|No Dimensions|
|Saída da Rede|Saída da Rede|Bytes|Total|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic).|No Dimensions|
|Disk Read Bytes/Sec|Disk Read|BytesPerSecond|Média|Average bytes read from disk during monitoring period.|No Dimensions|
|Disk Write Bytes/Sec|Disk Write|BytesPerSecond|Média|Average bytes written to disk during monitoring period.|No Dimensions|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Média|Disk Read IOPS.|No Dimensions|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Média|Disk Write IOPS.|No Dimensions|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|The percentage of allocated compute units that are currently in use by the Virtual Machine(s).|RoleInstanceId|
|Entrada na Rede|Entrada na Rede|Bytes|Total|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic).|RoleInstanceId|
|Saída da Rede|Saída da Rede|Bytes|Total|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic).|RoleInstanceId|
|Disk Read Bytes/Sec|Disk Read|BytesPerSecond|Média|Average bytes read from disk during monitoring period.|RoleInstanceId|
|Disk Write Bytes/Sec|Disk Write|BytesPerSecond|Média|Average bytes written to disk during monitoring period.|RoleInstanceId|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Média|Disk Read IOPS.|RoleInstanceId|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Média|Disk Write IOPS.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalCalls|Total Calls|Contagem|Total|Total number of calls.|ApiName, OperationName, Region|
|SuccessfulCalls|Successful Calls|Contagem|Total|Number of successful calls.|ApiName, OperationName, Region|
|TotalErrors|Total Errors|Contagem|Total|Total number of calls with error response (HTTP response code 4xx or 5xx).|ApiName, OperationName, Region|
|BlockedCalls|Blocked Calls|Contagem|Total|Number of calls that exceeded rate or quota limit.|ApiName, OperationName, Region|
|ServerErrors|Server Errors|Contagem|Total|Number of calls with service internal error (HTTP response code 5xx).|ApiName, OperationName, Region|
|ClientErrors|Client Errors|Contagem|Total|Number of calls with client side error (HTTP response code 4xx).|ApiName, OperationName, Region|
|DataIn|Data In|Bytes|Total|Size of incoming data in bytes.|ApiName, OperationName, Region|
|DataOut|Data Out|Bytes|Total|Size of outgoing data in bytes.|ApiName, OperationName, Region|
|Latência|Latência|MilliSeconds|Média|Latency in milliseconds.|ApiName, OperationName, Region|
|CharactersTranslated|Characters Translated|Contagem|Total|Total number of characters in incoming text request.|ApiName, OperationName, Region|
|CharactersTrained|Characters Trained|Contagem|Total|Total number of characters trained.|ApiName, OperationName, Region|
|SpeechSessionDuration|Speech Session Duration|Segundos|Total|Total duration of speech session in seconds.|ApiName, OperationName, Region|
|TotalTransactions|Total Transactions|Contagem|Total|Total number of transactions.|No Dimensions|
|TotalTokenCalls|Total Token Calls|Contagem|Total|Total number of token calls.|ApiName, OperationName, Region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|The percentage of allocated compute units that are currently in use by the Virtual Machine(s)|No Dimensions|
|Entrada na Rede|Network In Billable|Bytes|Total|The number of billable bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|No Dimensions|
|Saída da Rede|Network Out Billable|Bytes|Total|The number of billable bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|No Dimensions|
|Disk Read Bytes|Disk Read Bytes|Bytes|Total|Bytes read from disk during monitoring period|No Dimensions|
|Disk Write Bytes|Disk Write Bytes|Bytes|Total|Bytes written to disk during monitoring period|No Dimensions|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Média|Disk Read IOPS|No Dimensions|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Média|Disk Write IOPS|No Dimensions|
|CPU Credits Remaining|CPU Credits Remaining|Contagem|Média|Total number of credits available to burst|No Dimensions|
|CPU Credits Consumed|CPU Credits Consumed|Contagem|Média|Total number of credits consumed by the Virtual Machine|No Dimensions|
|Per Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Média|Bytes/Sec read from a single disk during monitoring period|SlotId|
|Per Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Média|Bytes/Sec written to a single disk during monitoring period|SlotId|
|Per Disk Read Operations/Sec|Data Disk Read Operations/Sec (Deprecated)|CountPerSecond|Média|Read IOPS from a single disk during monitoring period|SlotId|
|Per Disk Write Operations/Sec|Data Disk Write Operations/Sec (Deprecated)|CountPerSecond|Média|Write IOPS from a single disk during monitoring period|SlotId|
|Per Disk QD|Data Disk QD (Deprecated)|Contagem|Média|Data Disk Queue Depth(or Queue Length)|SlotId|
|OS Per Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Média|Bytes/Sec read from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Média|Bytes/Sec written to a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Read Operations/Sec|OS Disk Read Operations/Sec (Deprecated)|CountPerSecond|Média|Read IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Operations/Sec|OS Disk Write Operations/Sec (Deprecated)|CountPerSecond|Média|Write IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk QD|OS Disk QD (Deprecated)|Contagem|Média|OS Disk Queue Depth(or Queue Length)|No Dimensions|
|Data Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Preview)|CountPerSecond|Média|Bytes/Sec read from a single disk during monitoring period|LUN|
|Data Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Preview)|CountPerSecond|Média|Bytes/Sec written to a single disk during monitoring period|LUN|
|Data Disk Read Operations/Sec|Data Disk Read Operations/Sec (Preview)|CountPerSecond|Média|Read IOPS from a single disk during monitoring period|LUN|
|Data Disk Write Operations/Sec|Data Disk Write Operations/Sec (Preview)|CountPerSecond|Média|Write IOPS from a single disk during monitoring period|LUN|
|Data Disk Queue Depth|Data Disk Queue Depth (Preview)|Contagem|Média|Data Disk Queue Depth(or Queue Length)|LUN|
|OS Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Preview)|CountPerSecond|Média|Bytes/Sec read from a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Preview)|CountPerSecond|Média|Bytes/Sec written to a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Read Operations/Sec|OS Disk Read Operations/Sec (Preview)|CountPerSecond|Média|Read IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Write Operations/Sec|OS Disk Write Operations/Sec (Preview)|CountPerSecond|Média|Write IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Queue Depth|OS Disk Queue Depth (Preview)|Contagem|Média|OS Disk Queue Depth(or Queue Length)|No Dimensions|
|Inbound Flows|Inbound Flows (Preview)|Contagem|Média|Inbound Flows are number of current flows in the inbound direction (traffic going into the VM)|No Dimensions|
|Outbound Flows|Outbound Flows (Preview)|Contagem|Média|Outbound Flows are number of current flows in the outbound direction (traffic going out of the VM)|No Dimensions|
|Inbound Flows Maximum Creation Rate|Inbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Média|The maximum creation rate of inbound flows (traffic going into the VM)|No Dimensions|
|Outbound Flows Maximum Creation Rate|Outbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Média|The maximum creation rate of outbound flows (traffic going out of the VM)|No Dimensions|
|Premium Data Disk Cache Read Hit|Premium Data Disk Cache Read Hit (Preview)|Percentagem|Média|Premium Data Disk Cache Read Hit|LUN|
|Premium Data Disk Cache Read Miss|Premium Data Disk Cache Read Miss (Preview)|Percentagem|Média|Premium Data Disk Cache Read Miss|LUN|
|Premium OS Disk Cache Read Hit|Premium OS Disk Cache Read Hit (Preview)|Percentagem|Média|Premium OS Disk Cache Read Hit|No Dimensions|
|Premium OS Disk Cache Read Miss|Premium OS Disk Cache Read Miss (Preview)|Percentagem|Média|Premium OS Disk Cache Read Miss|No Dimensions|
|Network In Total|Network In Total|Bytes|Total|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|No Dimensions|
|Network Out Total|Network Out Total|Bytes|Total|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|No Dimensions|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|The percentage of allocated compute units that are currently in use by the Virtual Machine(s)|VMName|
|Entrada na Rede|Network In Billable|Bytes|Total|The number of billable bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|VMName|
|Saída da Rede|Network Out Billable|Bytes|Total|The number of billable bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|VMName|
|Disk Read Bytes|Disk Read Bytes|Bytes|Total|Bytes read from disk during monitoring period|VMName|
|Disk Write Bytes|Disk Write Bytes|Bytes|Total|Bytes written to disk during monitoring period|VMName|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Média|Disk Read IOPS|VMName|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Média|Disk Write IOPS|VMName|
|CPU Credits Remaining|CPU Credits Remaining|Contagem|Média|Total number of credits available to burst|No Dimensions|
|CPU Credits Consumed|CPU Credits Consumed|Contagem|Média|Total number of credits consumed by the Virtual Machine|No Dimensions|
|Per Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Média|Bytes/Sec read from a single disk during monitoring period|SlotId|
|Per Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Média|Bytes/Sec written to a single disk during monitoring period|SlotId|
|Per Disk Read Operations/Sec|Data Disk Read Operations/Sec (Deprecated)|CountPerSecond|Média|Read IOPS from a single disk during monitoring period|SlotId|
|Per Disk Write Operations/Sec|Data Disk Write Operations/Sec (Deprecated)|CountPerSecond|Média|Write IOPS from a single disk during monitoring period|SlotId|
|Per Disk QD|Data Disk QD (Deprecated)|Contagem|Média|Data Disk Queue Depth(or Queue Length)|SlotId|
|OS Per Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Média|Bytes/Sec read from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Média|Bytes/Sec written to a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Read Operations/Sec|OS Disk Read Operations/Sec (Deprecated)|CountPerSecond|Média|Read IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Operations/Sec|OS Disk Write Operations/Sec (Deprecated)|CountPerSecond|Média|Write IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk QD|OS Disk QD (Deprecated)|Contagem|Média|OS Disk Queue Depth(or Queue Length)|No Dimensions|
|Data Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Preview)|CountPerSecond|Média|Bytes/Sec read from a single disk during monitoring period|LUN, VMName|
|Data Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Preview)|CountPerSecond|Média|Bytes/Sec written to a single disk during monitoring period|LUN, VMName|
|Data Disk Read Operations/Sec|Data Disk Read Operations/Sec (Preview)|CountPerSecond|Média|Read IOPS from a single disk during monitoring period|LUN, VMName|
|Data Disk Write Operations/Sec|Data Disk Write Operations/Sec (Preview)|CountPerSecond|Média|Write IOPS from a single disk during monitoring period|LUN, VMName|
|Data Disk Queue Depth|Data Disk Queue Depth (Preview)|Contagem|Média|Data Disk Queue Depth(or Queue Length)|LUN, VMName|
|OS Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Preview)|CountPerSecond|Média|Bytes/Sec read from a single disk during monitoring period for OS disk|VMName|
|OS Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Preview)|CountPerSecond|Média|Bytes/Sec written to a single disk during monitoring period for OS disk|VMName|
|OS Disk Read Operations/Sec|OS Disk Read Operations/Sec (Preview)|CountPerSecond|Média|Read IOPS from a single disk during monitoring period for OS disk|VMName|
|OS Disk Write Operations/Sec|OS Disk Write Operations/Sec (Preview)|CountPerSecond|Média|Write IOPS from a single disk during monitoring period for OS disk|VMName|
|OS Disk Queue Depth|OS Disk Queue Depth (Preview)|Contagem|Média|OS Disk Queue Depth(or Queue Length)|VMName|
|Inbound Flows|Inbound Flows (Preview)|Contagem|Média|Inbound Flows are number of current flows in the inbound direction (traffic going into the VM)|VMName|
|Outbound Flows|Outbound Flows (Preview)|Contagem|Média|Outbound Flows are number of current flows in the outbound direction (traffic going out of the VM)|VMName|
|Inbound Flows Maximum Creation Rate|Inbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Média|The maximum creation rate of inbound flows (traffic going into the VM)|VMName|
|Outbound Flows Maximum Creation Rate|Outbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Média|The maximum creation rate of outbound flows (traffic going out of the VM)|VMName|
|Premium Data Disk Cache Read Hit|Premium Data Disk Cache Read Hit (Preview)|Percentagem|Média|Premium Data Disk Cache Read Hit|LUN, VMName|
|Premium Data Disk Cache Read Miss|Premium Data Disk Cache Read Miss (Preview)|Percentagem|Média|Premium Data Disk Cache Read Miss|LUN, VMName|
|Premium OS Disk Cache Read Hit|Premium OS Disk Cache Read Hit (Preview)|Percentagem|Média|Premium OS Disk Cache Read Hit|VMName|
|Premium OS Disk Cache Read Miss|Premium OS Disk Cache Read Miss (Preview)|Percentagem|Média|Premium OS Disk Cache Read Miss|VMName|
|Network In Total|Network In Total|Bytes|Total|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|VMName|
|Network Out Total|Network Out Total|Bytes|Total|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuUsage|CPU Usage|Contagem|Média|CPU usage on all cores in millicores.|containerName|
|MemoryUsage|Memory Usage|Bytes|Média|Total memory usage in byte.|containerName|
|NetworkBytesReceivedPerSecond|Network Bytes Received Per Second|Bytes|Média|The network bytes received per second.|No Dimensions|
|NetworkBytesTransmittedPerSecond|Network Bytes Transmitted Per Second|Bytes|Média|The network bytes transmitted per second.|No Dimensions|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalPullCount|Total Pull Count|Contagem|Média|Number of image pulls in total|No Dimensions|
|SuccessfulPullCount|Successful Pull Count|Contagem|Média|Number of successful image pulls|No Dimensions|
|TotalPushCount|Total Push Count|Contagem|Média|Number of image pushes in total|No Dimensions|
|SuccessfulPushCount|Successful Push Count|Contagem|Média|Number of successful image pushes|No Dimensions|
|RunDuration|Run Duration|Milliseconds|Total|Run Duration in milliseconds|No Dimensions|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Total number of available cpu cores in a managed cluster|Contagem|Total|Total number of available cpu cores in a managed cluster|No Dimensions|
|kube_node_status_allocatable_memory_bytes|Total amount of available memory in a managed cluster|Bytes|Total|Total amount of available memory in a managed cluster|No Dimensions|
|kube_pod_status_ready|Number of pods in Ready state|Contagem|Total|Number of pods in Ready state|namespace, pod|
|kube_node_status_condition|Statuses for various node conditions|Contagem|Total|Statuses for various node conditions|condition, status, status2, node|
|kube_pod_status_phase|Number of pods by phase|Contagem|Total|Number of pods by phase|phase, namespace, pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API Calls|Contagem|Total||No Dimensions|
|DCIMappingImportOperationSuccessfulLines|Mapping Import Operation Successful Lines|Contagem|Total||No Dimensions|
|DCIMappingImportOperationFailedLines|Mapping Import Operation Failed Lines|Contagem|Total||No Dimensions|
|DCIMappingImportOperationTotalLines|Mapping Import Operation Total Lines|Contagem|Total||No Dimensions|
|DCIMappingImportOperationRuntimeInSeconds|Mapping Import Operation Runtime In Seconds|Segundos|Total||No Dimensions|
|DCIOutboundProfileExportSucceeded|Outbound Profile Export Succeeded|Contagem|Total||No Dimensions|
|DCIOutboundProfileExportFailed|Outbound Profile Export Failed|Contagem|Total||No Dimensions|
|DCIOutboundProfileExportDuration|Outbound Profile Export Duration|Segundos|Total||No Dimensions|
|DCIOutboundKpiExportSucceeded|Outbound Kpi Export Succeeded|Contagem|Total||No Dimensions|
|DCIOutboundKpiExportFailed|Outbound Kpi Export Failed|Contagem|Total||No Dimensions|
|DCIOutboundKpiExportDuration|Outbound Kpi Export Duration|Segundos|Total||No Dimensions|
|DCIOutboundKpiExportStarted|Outbound Kpi Export Started|Segundos|Total||No Dimensions|
|DCIOutboundKpiRecordCount|Outbound Kpi Record Count|Segundos|Total||No Dimensions|
|DCIOutboundProfileExportCount|Outbound Profile Export Count|Segundos|Total||No Dimensions|
|DCIOutboundInitialProfileExportFailed|Outbound Initial Profile Export Failed|Segundos|Total||No Dimensions|
|DCIOutboundInitialProfileExportSucceeded|Outbound Initial Profile Export Succeeded|Segundos|Total||No Dimensions|
|DCIOutboundInitialKpiExportFailed|Outbound Initial Kpi Export Failed|Segundos|Total||No Dimensions|
|DCIOutboundInitialKpiExportSucceeded|Outbound Initial Kpi Export Succeeded|Segundos|Total||No Dimensions|
|DCIOutboundInitialProfileExportDurationInSeconds|Outbound Initial Profile Export Duration In Seconds|Segundos|Total||No Dimensions|
|AdlaJobForStandardKpiFailed|Adla Job For Standard Kpi Failed In Seconds|Segundos|Total||No Dimensions|
|AdlaJobForStandardKpiTimeOut|Adla Job For Standard Kpi TimeOut In Seconds|Segundos|Total||No Dimensions|
|AdlaJobForStandardKpiCompleted|Adla Job For Standard Kpi Completed In Seconds|Segundos|Total||No Dimensions|
|ImportASAValuesFailed|Import ASA Values Failed Count|Contagem|Total||No Dimensions|
|ImportASAValuesSucceeded|Import ASA Values Succeeded Count|Contagem|Total||No Dimensions|
|DCIProfilesCount|Profile Instance Count|Contagem|Last||No Dimensions|
|DCIInteractionsPerMonthCount|Interactions per Month Count|Contagem|Last||No Dimensions|
|DCIKpisCount|KPI Count|Contagem|Last||No Dimensions|
|DCISegmentsCount|Segment Count|Contagem|Last||No Dimensions|
|DCIPredictiveMatchPoliciesCount|Predictive Match Count|Contagem|Last||No Dimensions|
|DCIPredictionsCount|Prediction Count|Contagem|Last||No Dimensions|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|NICReadThroughput|Read Throughput (Network)|BytesPerSecond|Média|The read throughput of the network interface on the device in the reporting period for all volumes in the gateway.|InstanceName|
|NICWriteThroughput|Write Throughput (Network)|BytesPerSecond|Média|The write throughput of the network interface on the device in the reporting period for all volumes in the gateway.|InstanceName|
|CloudReadThroughputPerShare|Cloud Download Throughput (Share)|BytesPerSecond|Média|The download throughput to Azure from a share during the reporting period.|Partilhar|
|CloudUploadThroughputPerShare|Cloud Upload Throughput (Share)|BytesPerSecond|Média|The upload throughput to Azure from a share during the reporting period.|Partilhar|
|BytesUploadedToCloudPerShare|Cloud Bytes Uploaded (Share)|Bytes|Média|The total number of bytes that is uploaded to Azure from a share during the reporting period.|Partilhar|
|TotalCapacity|Total Capacity|Bytes|Média|Total Capacity|No Dimensions|
|AvailableCapacity|Available Capacity|Bytes|Média|The available capacity in bytes during the reporting period.|No Dimensions|
|CloudUploadThroughput|Cloud Upload Throughput|BytesPerSecond|Média|The cloud upload throughput  to Azure during the reporting period.|No Dimensions|
|CloudReadThroughput|Cloud Download Throughput|BytesPerSecond|Média|The cloud download throughput to Azure during the reporting period.|No Dimensions|
|BytesUploadedToCloud|Cloud Bytes Uploaded (Device)|Bytes|Média|The total number of bytes that is uploaded to Azure from a device during the reporting period.|No Dimensions|
|HyperVVirtualProcessorUtilization|Edge Compute - Percentage CPU|Percentagem|Média|Percent CPU Usage|InstanceName|
|HyperVMemoryUtilization|Edge Compute - Memory Usage|Percentagem|Média|Amount of RAM in Use|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|FailedRuns|Failed Runs|Contagem|Total||pipelineName, activityName|
|SuccessfulRuns|Successful Runs|Contagem|Total||pipelineName, activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|PipelineFailedRuns|Failed pipeline runs metrics|Contagem|Total||FailureType, Name|
|PipelineSucceededRuns|Succeeded pipeline runs metrics|Contagem|Total||FailureType, Name|
|ActivityFailedRuns|Failed activity runs metrics|Contagem|Total||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Succeeded activity runs metrics|Contagem|Total||ActivityType, PipelineName, FailureType, Name|
|TriggerFailedRuns|Failed trigger runs metrics|Contagem|Total||Name, FailureType|
|TriggerSucceededRuns|Succeeded trigger runs metrics|Contagem|Total||Name, FailureType|
|IntegrationRuntimeCpuPercentage|Integration runtime CPU utilization|Percentagem|Média||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Integration runtime available memory|Bytes|Média||IntegrationRuntimeName, NodeName|
|MaxAllowedResourceCount|Maximum allowed entities count|Contagem|Máximo||No Dimensions|
|MaxAllowedFactorySizeInGbUnits|Maximum allowed factory size (GB unit)|Contagem|Máximo||No Dimensions|
|ResourceCount|Total entities count|Contagem|Máximo||No Dimensions|
|FactorySizeInGbUnits|Total factory size (GB unit)|Contagem|Máximo||No Dimensions|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|JobEndedSuccess|Successful Jobs|Contagem|Total|Count of successful jobs.|No Dimensions|
|JobEndedFailure|Failed Jobs|Contagem|Total|Count of failed jobs.|No Dimensions|
|JobEndedCancelled|Canceled Jobs|Contagem|Total|Count of canceled jobs.|No Dimensions|
|JobAUEndedSuccess|Successful AU Time|Segundos|Total|Total AU time for successful jobs.|No Dimensions|
|JobAUEndedFailure|Failed AU Time|Segundos|Total|Total AU time for failed jobs.|No Dimensions|
|JobAUEndedCancelled|Canceled AU Time|Segundos|Total|Total AU time for canceled jobs.|No Dimensions|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalStorage|Armazenamento Total|Bytes|Máximo|Total amount of data stored in the account.|No Dimensions|
|DataWritten|Data Written|Bytes|Total|Total amount of data written to the account.|No Dimensions|
|DataRead|Data Read|Bytes|Total|Total amount of data read from the account.|No Dimensions|
|WriteRequests|Write Requests|Contagem|Total|Count of data write requests to the account.|No Dimensions|
|ReadRequests|Read Requests|Contagem|Total|Count of data read requests to the account.|No Dimensions|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Percentagem|Média|CPU percent|No Dimensions|
|memory_percent|Memory percent|Percentagem|Média|Memory percent|No Dimensions|
|io_consumption_percent|IO percent|Percentagem|Média|IO percent|No Dimensions|
|storage_percent|Storage percent|Percentagem|Média|Storage percent|No Dimensions|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|No Dimensions|
|storage_limit|Storage limit|Bytes|Média|Storage limit|No Dimensions|
|serverlog_storage_percent|Server Log storage percent|Percentagem|Média|Server Log storage percent|No Dimensions|
|serverlog_storage_usage|Server Log storage used|Bytes|Média|Server Log storage used|No Dimensions|
|serverlog_storage_limit|Server Log storage limit|Bytes|Média|Server Log storage limit|No Dimensions|
|active_connections|Active Connections|Contagem|Média|Active Connections|No Dimensions|
|connections_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|No Dimensions|
|seconds_behind_master|Replication lag in seconds|Contagem|Média|Replication lag in seconds|No Dimensions|
|backup_storage_used|Backup Storage used|Bytes|Média|Backup Storage used|No Dimensions|
|network_bytes_egress|Saída da Rede|Bytes|Total|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Network In across active connections|No Dimensions|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Percentagem|Média|CPU percent|No Dimensions|
|memory_percent|Memory percent|Percentagem|Média|Memory percent|No Dimensions|
|io_consumption_percent|IO percent|Percentagem|Média|IO percent|No Dimensions|
|storage_percent|Storage percent|Percentagem|Média|Storage percent|No Dimensions|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|No Dimensions|
|storage_limit|Storage limit|Bytes|Média|Storage limit|No Dimensions|
|serverlog_storage_percent|Server Log storage percent|Percentagem|Média|Server Log storage percent|No Dimensions|
|serverlog_storage_usage|Server Log storage used|Bytes|Média|Server Log storage used|No Dimensions|
|serverlog_storage_limit|Server Log storage limit|Bytes|Média|Server Log storage limit|No Dimensions|
|active_connections|Active Connections|Contagem|Média|Active Connections|No Dimensions|
|connections_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|No Dimensions|
|seconds_behind_master|Replication lag in seconds|Contagem|Média|Replication lag in seconds|No Dimensions|
|backup_storage_used|Backup Storage used|Bytes|Média|Backup Storage used|No Dimensions|
|network_bytes_egress|Saída da Rede|Bytes|Total|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Network In across active connections|No Dimensions|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Percentagem|Média|CPU percent|No Dimensions|
|memory_percent|Memory percent|Percentagem|Média|Memory percent|No Dimensions|
|io_consumption_percent|IO percent|Percentagem|Média|IO percent|No Dimensions|
|storage_percent|Storage percent|Percentagem|Média|Storage percent|No Dimensions|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|No Dimensions|
|storage_limit|Storage limit|Bytes|Média|Storage limit|No Dimensions|
|serverlog_storage_percent|Server Log storage percent|Percentagem|Média|Server Log storage percent|No Dimensions|
|serverlog_storage_usage|Server Log storage used|Bytes|Média|Server Log storage used|No Dimensions|
|serverlog_storage_limit|Server Log storage limit|Bytes|Média|Server Log storage limit|No Dimensions|
|active_connections|Active Connections|Contagem|Média|Active Connections|No Dimensions|
|connections_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|No Dimensions|
|backup_storage_used|Backup Storage used|Bytes|Média|Backup Storage used|No Dimensions|
|network_bytes_egress|Saída da Rede|Bytes|Total|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Network In across active connections|No Dimensions|
|pg_replica_log_delay_in_seconds|Replica Lag|Segundos|Máximo|Replica lag in seconds|No Dimensions|
|pg_replica_log_delay_in_bytes|Max Lag Across Replicas|Bytes|Máximo|Lag in bytes of the most lagging replica|No Dimensions|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Percentagem|Média|CPU percent|No Dimensions|
|memory_percent|Memory percent|Percentagem|Média|Memory percent|No Dimensions|
|iops|IOPS|Contagem|Média|IO Operations per second|No Dimensions|
|storage_percent|Storage percent|Percentagem|Média|Storage percent|No Dimensions|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|No Dimensions|
|active_connections|Active Connections|Contagem|Média|Active Connections|No Dimensions|
|network_bytes_egress|Saída da Rede|Bytes|Total|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Network In across active connections|No Dimensions|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Telemetry message send attempts|Contagem|Total|Number of device-to-cloud telemetry messages attempted to be sent to your IoT hub|No Dimensions|
|d2c.telemetry.ingress.success|Telemetry messages sent|Contagem|Total|Number of device-to-cloud telemetry messages sent successfully to your IoT hub|No Dimensions|
|c2d.commands.egress.complete.success|Commands completed|Contagem|Total|Number of cloud-to-device commands completed successfully by the device|No Dimensions|
|c2d.commands.egress.abandon.success|Commands abandoned|Contagem|Total|Number of cloud-to-device commands abandoned by the device|No Dimensions|
|c2d.commands.egress.reject.success|Commands rejected|Contagem|Total|Number of cloud-to-device commands rejected by the device|No Dimensions|
|devices.totalDevices|Total devices (deprecated)|Contagem|Total|Number of devices registered to your IoT hub|No Dimensions|
|devices.connectedDevices.allProtocol|Connected devices (deprecated) |Contagem|Total|Number of devices connected to your IoT hub|No Dimensions|
|d2c.telemetry.egress.success|Routing: telemetry messages delivered|Contagem|Total|The number of times messages were successfully delivered to all endpoints using IoT Hub routing. If a message is routed to multiple endpoints, this value increases by one for each successful delivery. If a message is delivered to the same endpoint multiple times, this value increases by one for each successful delivery.|No Dimensions|
|d2c.telemetry.egress.dropped|Routing: telemetry messages dropped |Contagem|Total|The number of times messages were dropped by IoT Hub routing due to dead endpoints. This value does not count messages delivered to fallback route as dropped messages are not delivered there.|No Dimensions|
|d2c.telemetry.egress.orphaned|Routing: telemetry messages orphaned |Contagem|Total|The number of times messages were orphaned by IoT Hub routing because they didn't match any routing rules (including the fallback rule). |No Dimensions|
|d2c.telemetry.egress.invalid|Routing: telemetry messages incompatible|Contagem|Total|The number of times IoT Hub routing failed to deliver messages due to an incompatibility with the endpoint. This value does not include retries.|No Dimensions|
|d2c.telemetry.egress.fallback|Routing: messages delivered to fallback|Contagem|Total|The number of times IoT Hub routing delivered messages to the endpoint associated with the fallback route.|No Dimensions|
|d2c.endpoints.egress.eventHubs|Routing: messages delivered to Event Hub|Contagem|Total|The number of times IoT Hub routing successfully delivered messages to Event Hub endpoints.|No Dimensions|
|d2c.endpoints.latency.eventHubs|Routing: message latency for Event Hub|Milliseconds|Média|The average latency (milliseconds) between message ingress to IoT Hub and message ingress into an Event Hub endpoint.|No Dimensions|
|d2c.endpoints.egress.serviceBusQueues|Routing: messages delivered to Service Bus Queue|Contagem|Total|The number of times IoT Hub routing successfully delivered messages to Service Bus queue endpoints.|No Dimensions|
|d2c.endpoints.latency.serviceBusQueues|Routing: message latency for Service Bus Queue|Milliseconds|Média|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into a Service Bus queue endpoint.|No Dimensions|
|d2c.endpoints.egress.serviceBusTopics|Routing: messages delivered to Service Bus Topic|Contagem|Total|The number of times IoT Hub routing successfully delivered messages to Service Bus topic endpoints.|No Dimensions|
|d2c.endpoints.latency.serviceBusTopics|Routing: message latency for Service Bus Topic|Milliseconds|Média|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into a Service Bus topic endpoint.|No Dimensions|
|d2c.endpoints.egress.builtIn.events|Routing: messages delivered to messages/events|Contagem|Total|The number of times IoT Hub routing successfully delivered messages to the built-in endpoint (messages/events). This metric only starts working when routing is enabled (https://aka.ms/iotrouting) for the IoT hub.|No Dimensions|
|d2c.endpoints.latency.builtIn.events|Routing: message latency for messages/events|Milliseconds|Média|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into the built-in endpoint (messages/events). This metric only starts working when routing is enabled (https://aka.ms/iotrouting) for the IoT hub.|No Dimensions|
|d2c.endpoints.egress.storage|Routing: messages delivered to storage|Contagem|Total|The number of times IoT Hub routing successfully delivered messages to storage endpoints.|No Dimensions|
|d2c.endpoints.latency.storage|Routing: message latency for storage|Milliseconds|Média|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into a storage endpoint.|No Dimensions|
|d2c.endpoints.egress.storage.bytes|Routing: data delivered to storage|Bytes|Total|The amount of data (bytes) IoT Hub routing delivered to storage endpoints.|No Dimensions|
|d2c.endpoints.egress.storage.blobs|Routing: blobs delivered to storage|Contagem|Total|The number of times IoT Hub routing delivered blobs to storage endpoints.|No Dimensions|
|EventGridDeliveries|Event Grid deliveries (preview)|Contagem|Total|The number of IoT Hub events published to Event Grid. Use the Result dimension for the number of successful and failed requests. EventType dimension shows the type of event (https://aka.ms/ioteventgrid).|Result, EventType|
|EventGridLatency|The average latency (milliseconds) from when the Iot Hub event was generated to when the event was published to Event Grid. This number is an average between all event types. Use the EventType dimension to see latency of a specific type of event.|EventType|
|d2c.twin.read.success|Successful twin reads from devices|Contagem|Total|The count of all successful device-initiated twin reads.|No Dimensions|
|d2c.twin.read.failure|Failed twin reads from devices|Contagem|Total|The count of all failed device-initiated twin reads.|No Dimensions|
|d2c.twin.read.size|Response size of twin reads from devices|Bytes|Média|The average, min, and max of all successful device-initiated twin reads.|No Dimensions|
|d2c.twin.update.success|Successful twin updates from devices|Contagem|Total|The count of all successful device-initiated twin updates.|No Dimensions|
|d2c.twin.update.failure|Failed twin updates from devices|Contagem|Total|The count of all failed device-initiated twin updates.|No Dimensions|
|d2c.twin.update.size|Size of twin updates from devices|Bytes|Média|The average, min, and max size of all successful device-initiated twin updates.|No Dimensions|
|c2d.methods.success|Successful direct method invocations|Contagem|Total|The count of all successful direct method calls.|No Dimensions|
|c2d.methods.failure|Failed direct method invocations|Contagem|Total|The count of all failed direct method calls.|No Dimensions|
|c2d.methods.requestSize|Request size of direct method invocations|Bytes|Média|The average, min, and max of all successful direct method requests.|No Dimensions|
|c2d.methods.responseSize|Response size of direct method invocations|Bytes|Média|The average, min, and max of all successful direct method responses.|No Dimensions|
|c2d.twin.read.success|Successful twin reads from back end|Contagem|Total|The count of all successful back-end-initiated twin reads.|No Dimensions|
|c2d.twin.read.failure|Failed twin reads from back end|Contagem|Total|The count of all failed back-end-initiated twin reads.|No Dimensions|
|c2d.twin.read.size|Response size of twin reads from back end|Bytes|Média|The average, min, and max of all successful back-end-initiated twin reads.|No Dimensions|
|c2d.twin.update.success|Successful twin updates from back end|Contagem|Total|The count of all successful back-end-initiated twin updates.|No Dimensions|
|c2d.twin.update.failure|Failed twin updates from back end|Contagem|Total|The count of all failed back-end-initiated twin updates.|No Dimensions|
|c2d.twin.update.size|Size of twin updates from back end|Bytes|Média|The average, min, and max size of all successful back-end-initiated twin updates.|No Dimensions|
|twinQueries.success|Successful twin queries|Contagem|Total|The count of all successful twin queries.|No Dimensions|
|twinQueries.failure|Failed twin queries|Contagem|Total|The count of all failed twin queries.|No Dimensions|
|twinQueries.resultSize|Twin queries result size|Bytes|Média|The average, min, and max of the result size of all successful twin queries.|No Dimensions|
|jobs.createTwinUpdateJob.success|Successful creations of twin update jobs|Contagem|Total|The count of all successful creation of twin update jobs.|No Dimensions|
|jobs.createTwinUpdateJob.failure|Failed creations of twin update jobs|Contagem|Total|The count of all failed creation of twin update jobs.|No Dimensions|
|jobs.createDirectMethodJob.success|Successful creations of method invocation jobs|Contagem|Total|The count of all successful creation of direct method invocation jobs.|No Dimensions|
|jobs.createDirectMethodJob.failure|Failed creations of method invocation jobs|Contagem|Total|The count of all failed creation of direct method invocation jobs.|No Dimensions|
|jobs.listJobs.success|Successful calls to list jobs|Contagem|Total|The count of all successful calls to list jobs.|No Dimensions|
|jobs.listJobs.failure|Failed calls to list jobs|Contagem|Total|The count of all failed calls to list jobs.|No Dimensions|
|jobs.cancelJob.success|Successful job cancellations|Contagem|Total|The count of all successful calls to cancel a job.|No Dimensions|
|jobs.cancelJob.failure|Failed job cancellations|Contagem|Total|The count of all failed calls to cancel a job.|No Dimensions|
|jobs.queryJobs.success|Successful job queries|Contagem|Total|The count of all successful calls to query jobs.|No Dimensions|
|jobs.queryJobs.failure|Failed job queries|Contagem|Total|The count of all failed calls to query jobs.|No Dimensions|
|jobs.completed|Completed jobs|Contagem|Total|The count of all completed jobs.|No Dimensions|
|jobs.failed|Failed jobs|Contagem|Total|The count of all failed jobs.|No Dimensions|
|d2c.telemetry.ingress.sendThrottle|Number of throttling errors|Contagem|Total|Number of throttling errors due to device throughput throttles|No Dimensions|
|dailyMessageQuotaUsed|Total number of messages used|Contagem|Média|Number of total messages used today. This is a cumulative value that is reset to zero at 00:00 UTC every day.|No Dimensions|
|deviceDataUsage|Total device data usage|Bytes|Total|Bytes transferred to and from any devices connected to IotHub|No Dimensions|
|totalDeviceCount|Total devices (preview)|Contagem|Média|Number of devices registered to your IoT hub|No Dimensions|
|connectedDeviceCount|Connected devices (preview)|Contagem|Média|Number of devices connected to your IoT hub|No Dimensions|
|configurations|Configuration Metrics|Contagem|Total|Metrics for Configuration Operations|No Dimensions|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|RegistrationAttempts|Registration attempts|Contagem|Total|Number of device registrations attempted|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Devices assigned|Contagem|Total|Number of devices assigned to an IoT hub|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Attestation attempts|Contagem|Total|Number of device attestations attempted|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|AvailableStorage|Available Storage|Bytes|Total|Total available storage reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|CassandraConnectionClosures|Cassandra Connection Closures|Contagem|Total|Number of Cassandra connections that were closed, reported at a 1 minute granularity|Region, ClosureReason|
|CassandraRequestCharges|Cassandra Request Charges|Contagem|Total|RUs consumed for Cassandra requests made|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Cassandra Requests|Contagem|Contagem|Number of Cassandra requests made|DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|DataUsage|Utilização de Dados|Bytes|Total|Total data usage reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|DocumentCount|Document Count|Contagem|Total|Total document count reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|DocumentQuota|Document Quota|Bytes|Total|Total storage quota reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|IndexUsage|Index Usage|Bytes|Total|Total index usage reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|MetadataRequests|Metadata Requests|Contagem|Contagem|Count of metadata requests. Cosmos DB maintains system metadata collection for each account, that allows you to enumerate collections, databases, etc., and their configurations, free of charge.|DatabaseName, CollectionName, Region, StatusCode, |
|MongoRequestCharge|Mongo Request Charge|Contagem|Total|Mongo Request Units Consumed|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequests|Mongo Requests|Contagem|Contagem|Number of Mongo Requests Made|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|ProvisionedThroughput|Débito Aprovisionado|Contagem|Máximo|Débito Aprovisionado|DatabaseName, CollectionName|
|ReplicationLatency|P99 Replication Latency|MilliSeconds|Média|P99 Replication Latency across source and target regions for geo-enabled account|SourceRegion, TargetRegion|
|ServiceAvailability|Service Availability|Percentagem|Média|Account requests availability at one hour, day or month granularity|No Dimensions|
|TotalRequestUnits|Total Request Units|Contagem|Total|Request Units consumed|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|TotalRequests|Total de Pedidos|Contagem|Contagem|Number of requests made|DatabaseName, CollectionName, Region, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events|Contagem|Total|Total events published to this topic|No Dimensions|
|PublishFailCount|Publish Failed Events|Contagem|Total|Total events failed to publish to this topic|ErrorType, Error|
|UnmatchedEventCount|Unmatched Events|Contagem|Total|Total events not matching any of the event subscriptions for this topic|No Dimensions|
|PublishSuccessLatencyInMs|Publish Success Latency|Contagem|Total|Publish success latency in milliseconds|No Dimensions|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|MatchedEventCount|Matched Events|Contagem|Total|Total events matched to this event subscription|No Dimensions|
|DeliveryAttemptFailCount|Delivery Failed Events|Contagem|Total|Total events failed to deliver to this event subscription|Error, ErrorType|
|DeliverySuccessCount|Delivered Events|Contagem|Total|Total events delivered to this event subscription|No Dimensions|
|DestinationProcessingDurationInMs|Destination Processing Duration|Milliseconds|Média|Destination processing duration in milliseconds|No Dimensions|
|DroppedEventCount|Dropped Events|Contagem|Total|Total dropped events matching to this event subscription|DropReason|
|DeadLetteredCount|Dead Lettered Events|Contagem|Total|Total dead lettered events matching to this event subscription|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events|Contagem|Total|Total events published to this topic|No Dimensions|
|PublishFailCount|Failed Events|Contagem|Total|Total events failed to publish to this topic|ErrorType, Error|
|UnmatchedEventCount|Unmatched Events|Contagem|Total|Total events not matching any of the event subscriptions for this topic|No Dimensions|
|PublishSuccessLatencyInMs|Publish Success Latency|Contagem|Total|Publish success latency in milliseconds|No Dimensions|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfulRequests|Successful Requests|Contagem|Total|Successful Requests for Microsoft.EventHub.|EntityName, |
|ServerErrors|Server Errors.|Contagem|Total|Server Errors for Microsoft.EventHub.|EntityName, |
|UserErrors|User Errors.|Contagem|Total|User Errors for Microsoft.EventHub.|EntityName, |
|QuotaExceededErrors|Quota Exceeded Errors.|Contagem|Total|Quota Exceeded Errors for Microsoft.EventHub.|EntityName, |
|ThrottledRequests|Throttled Requests.|Contagem|Total|Throttled Requests for Microsoft.EventHub.|EntityName, |
|IncomingRequests|Incoming Requests|Contagem|Total|Incoming Requests for Microsoft.EventHub.|EntityName|
|IncomingMessages|Incoming Messages|Contagem|Total|Incoming Messages for Microsoft.EventHub.|EntityName|
|OutgoingMessages|Outgoing Messages|Contagem|Total|Outgoing Messages for Microsoft.EventHub.|EntityName|
|IncomingBytes|Incoming Bytes.|Bytes|Total|Incoming Bytes for Microsoft.EventHub.|EntityName|
|OutgoingBytes|Outgoing Bytes.|Bytes|Total|Outgoing Bytes for Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Contagem|Média|Total Active Connections for Microsoft.EventHub.|No Dimensions|
|ConnectionsOpened|Connections Opened.|Contagem|Média|Connections Opened for Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Connections Closed.|Contagem|Média|Connections Closed for Microsoft.EventHub.|EntityName|
|CaptureBacklog|Capture Backlog.|Contagem|Total|Capture Backlog for Microsoft.EventHub.|EntityName|
|CapturedMessages|Captured Messages.|Contagem|Total|Captured Messages for Microsoft.EventHub.|EntityName|
|CapturedBytes|Captured Bytes.|Bytes|Total|Captured Bytes for Microsoft.EventHub.|EntityName|
|Tamanho|Tamanho|Bytes|Média|Size of an EventHub in Bytes.|EntityName|
|INREQS|Incoming Requests (Deprecated)|Contagem|Total|Total incoming send requests for a namespace (Deprecated)|No Dimensions|
|SUCCREQ|Successful Requests (Deprecated)|Contagem|Total|Total successful requests for a namespace (Deprecated)|No Dimensions|
|FAILREQ|Failed Requests (Deprecated)|Contagem|Total|Total failed requests for a namespace (Deprecated)|No Dimensions|
|SVRBSY|Server Busy Errors (Deprecated)|Contagem|Total|Total server busy errors for a namespace (Deprecated)|No Dimensions|
|INTERR|Internal Server Errors (Deprecated)|Contagem|Total|Total internal server errors for a namespace (Deprecated)|No Dimensions|
|MISCERR|Other Errors (Deprecated)|Contagem|Total|Total failed requests for a namespace (Deprecated)|No Dimensions|
|INMSGS|Incoming Messages (Deprecated) (Deprecated)|Contagem|Total|Total incoming messages for a namespace. This metric is deprecated. Please use Incoming Messages metric instead (Deprecated)|No Dimensions|
|EHINMSGS|Incoming Messages (Deprecated)|Contagem|Total|Total incoming messages for a namespace (Deprecated)|No Dimensions|
|OUTMSGS|Outgoing Messages (Deprecated) (Deprecated)|Contagem|Total|Total outgoing messages for a namespace. This metric is deprecated. Please use Outgoing Messages metric instead (Deprecated)|No Dimensions|
|EHOUTMSGS|Outgoing Messages (Deprecated)|Contagem|Total|Total outgoing messages for a namespace (Deprecated)|No Dimensions|
|EHINMBS|Incoming bytes (Deprecated) (Deprecated)|Bytes|Total|Event Hub incoming message throughput for a namespace. This metric is deprecated. Please use Incoming bytes metric instead (Deprecated)|No Dimensions|
|EHINBYTES|Incoming bytes (Deprecated)|Bytes|Total|Event Hub incoming message throughput for a namespace (Deprecated)|No Dimensions|
|EHOUTMBS|Outgoing bytes (Deprecated) (Deprecated)|Bytes|Total|Event Hub outgoing message throughput for a namespace. This metric is deprecated. Please use Outgoing bytes metric instead (Deprecated)|No Dimensions|
|EHOUTBYTES|Outgoing bytes (Deprecated)|Bytes|Total|Event Hub outgoing message throughput for a namespace (Deprecated)|No Dimensions|
|EHABL|Archive backlog messages (Deprecated)|Contagem|Total|Event Hub archive messages in backlog for a namespace (Deprecated)|No Dimensions|
|EHAMSGS|Archive messages (Deprecated)|Contagem|Total|Event Hub archived messages in a namespace (Deprecated)|No Dimensions|
|EHAMBS|Archive message throughput (Deprecated)|Bytes|Total|Event Hub archived message throughput in a namespace (Deprecated)|No Dimensions|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfulRequests|Successful Requests (Preview)|Contagem|Total|Successful Requests for Microsoft.EventHub. (Pré-visualização)|No Dimensions|
|ServerErrors|Server Errors. (Pré-visualização)|Contagem|Total|Server Errors for Microsoft.EventHub. (Pré-visualização)|No Dimensions|
|UserErrors|User Errors. (Pré-visualização)|Contagem|Total|User Errors for Microsoft.EventHub. (Pré-visualização)|No Dimensions|
|QuotaExceededErrors|Quota Exceeded Errors. (Pré-visualização)|Contagem|Total|Quota Exceeded Errors for Microsoft.EventHub. (Pré-visualização)|No Dimensions|
|ThrottledRequests|Throttled Requests. (Pré-visualização)|Contagem|Total|Throttled Requests for Microsoft.EventHub. (Pré-visualização)|No Dimensions|
|IncomingRequests|Incoming Requests (Preview)|Contagem|Total|Incoming Requests for Microsoft.EventHub. (Pré-visualização)|No Dimensions|
|IncomingMessages|Incoming Messages (Preview)|Contagem|Total|Incoming Messages for Microsoft.EventHub. (Pré-visualização)|No Dimensions|
|OutgoingMessages|Outgoing Messages (Preview)|Contagem|Total|Outgoing Messages for Microsoft.EventHub. (Pré-visualização)|No Dimensions|
|IncomingBytes|Incoming Bytes. (Pré-visualização)|Bytes|Total|Incoming Bytes for Microsoft.EventHub. (Pré-visualização)|No Dimensions|
|OutgoingBytes|Outgoing Bytes. (Pré-visualização)|Bytes|Total|Outgoing Bytes for Microsoft.EventHub. (Pré-visualização)|No Dimensions|
|ActiveConnections|ActiveConnections (Preview)|Contagem|Média|Total Active Connections for Microsoft.EventHub. (Pré-visualização)|No Dimensions|
|ConnectionsOpened|Connections Opened. (Pré-visualização)|Contagem|Média|Connections Opened for Microsoft.EventHub. (Pré-visualização)|No Dimensions|
|ConnectionsClosed|Connections Closed. (Pré-visualização)|Contagem|Média|Connections Closed for Microsoft.EventHub. (Pré-visualização)|No Dimensions|
|CaptureBacklog|Capture Backlog. (Pré-visualização)|Contagem|Total|Capture Backlog for Microsoft.EventHub. (Pré-visualização)|No Dimensions|
|CapturedMessages|Captured Messages. (Pré-visualização)|Contagem|Total|Captured Messages for Microsoft.EventHub. (Pré-visualização)|No Dimensions|
|CapturedBytes|Captured Bytes. (Pré-visualização)|Bytes|Total|Captured Bytes for Microsoft.EventHub. (Pré-visualização)|No Dimensions|
|CPU|CPU (Preview)|Percentagem|Máximo|CPU utilization for the Event Hub Cluster as a percentage|Função|
|AvailableMemory|Available Memory (Preview)|Contagem|Máximo|Available memory for the Event Hub Cluster in bytes|Função|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|GatewayRequests|Gateway Requests|Contagem|Total|Number of gateway requests|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Categorized Gateway Requests|Contagem|Total|Number of gateway requests by categories (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Number of Active Workers|Contagem|Máximo|Number of Active Workers|ClusterDnsName, MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|ObservedMetricValue|Observed Metric Value|Contagem|Média|The value computed by autoscale when executed|MetricTriggerSource|
|MetricThreshold|Metric Threshold|Contagem|Média|The configured autoscale threshold when autoscale ran.|MetricTriggerRule|
|ObservedCapacity|Observed Capacity|Contagem|Média|The capacity reported to autoscale when it executed.|No Dimensions|
|ScaleActionsInitiated|Scale Actions Initiated|Contagem|Total|The direction of the scale operation.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Public Preview)

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Disponibilidade|Percentagem|Média|Percentage of successfully completed availability tests|availabilityResult/name, availabilityResult/location|
|availabilityResults/count|Testes de disponibilidade|Contagem|Contagem|Count of availability tests|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|Availability test duration|MilliSeconds|Média|Availability test duration|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|Page load network connect time|MilliSeconds|Média|Time between user request and network connection. Includes DNS lookup and transport connection.|No Dimensions|
|browserTimings/processingDuration|Client processing time|MilliSeconds|Média|Time between receiving the last byte of a document until the DOM is loaded. Async requests may still be processing.|No Dimensions|
|browserTimings/receiveDuration|Receiving response time|MilliSeconds|Média|Time between the first and last bytes, or until disconnection.|No Dimensions|
|browserTimings/sendDuration|Send request time|MilliSeconds|Média|Time between network connection and receiving the first byte.|No Dimensions|
|browserTimings/totalDuration|Browser page load time|MilliSeconds|Média|Time from user request until DOM, stylesheets, scripts and images are loaded.|No Dimensions|
|dependencies/count|Dependency calls|Contagem|Contagem|Count of calls made by the application to external resources.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/duration|Dependency duration|MilliSeconds|Média|Duration of calls made by the application to external resources.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/failed|Dependency call failures|Contagem|Contagem|Count of failed dependency calls made by the application to external resources.|dependency/type, dependency/performanceBucket, operation/synthetic, cloud/roleInstance, cloud/roleName|
|pageViews/count|Page views|Contagem|Contagem|Count of page views.|operation/synthetic|
|pageViews/duration|Page view load time|MilliSeconds|Média|Page view load time|operation/synthetic|
|performanceCounters/requestExecutionTime|HTTP request execution time|MilliSeconds|Média|Execution time of the most recent request.|cloud/roleInstance|
|performanceCounters/requestsInQueue|HTTP requests in application queue|Contagem|Média|Length of the application request queue.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|HTTP request rate|CountPerSecond|Média|Rate of all requests to the application per second from ASP.NET.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Exception rate|CountPerSecond|Média|Count of handled and unhandled exceptions reported to windows, including .NET exceptions and unmanaged exceptions that are converted into .NET exceptions.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Process IO rate|BytesPerSecond|Média|Total bytes per second read and written to files, network and devices.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Process CPU|Percentagem|Média|The percentage of elapsed time that all process threads used the processor to execute instructions. This can vary between 0 to 100. This metric indicates the performance of w3wp process alone.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Processor time|Percentagem|Média|The percentage of time that the processor spends in non-idle threads.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Available memory|Bytes|Média|Physical memory immediately available for allocation to a process or for system use.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Process private bytes|Bytes|Média|Memory exclusively assigned to the monitored application's processes.|cloud/roleInstance|
|requests/duration|Server response time|MilliSeconds|Média|Time between receiving an HTTP request and finishing sending the response.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/count|Server requests|Contagem|Contagem|Count of HTTP requests completed.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/failed|Pedidos falhados|Contagem|Contagem|Count of HTTP requests marked as failed. In most cases these are requests with a response code >= 400 and not equal to 401.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|requests/rate|Server request rate|CountPerSecond|Média|Rate of server requests per second|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|exceptions/count|Exceções|Contagem|Contagem|Combined count of all uncaught exceptions.|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/browser|Exceções de browser|Contagem|Contagem|Count of uncaught exceptions thrown in the browser.|No Dimensions|
|exceptions/server|Server exceptions|Contagem|Contagem|Count of uncaught exceptions thrown in the server application.|cloud/roleName, cloud/roleInstance|
|traces/count|Traces|Contagem|Contagem|Trace document count|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServiceApiHit|Total Service Api Hits|Contagem|Contagem|Number of total service api hits|ActivityType, ActivityName|
|ServiceApiLatency|Overall Service Api Latency|Milliseconds|Média|Overall latency of service api requests|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Total Service Api Results|Contagem|Contagem|Number of total service api results|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|CacheUtilization|Cache Utilization|Percentagem|Média|Utilization level in the cluster scope|Nenhuma|
|QueryDuration|Query Duration|Milliseconds|Média|Queries’ duration in seconds|Query Status|
|IngestionUtilization|Ingestion Utilization|Percentagem|Média|Ratio of used ingestion slots in the cluster|Nenhuma|
|KeepAlive|Keep Alive|Contagem|Média|Sanity check indicates the cluster responds to queries|Nenhuma|
|IngestionVolumeInMB|Ingestion Volume (In MB)|Contagem|Total|Overall volume of ingested data to the cluster (in MB)|Base de Dados|
|IngestionLatencyInSeconds|Ingestion Latency (In seconds)|Segundos|Média|Ingestion time from the source (e.g. message is in EventHub) to the cluster in seconds|Nenhuma|
|EventProcessedForEventHubs|Events Processed (for Event Hubs)|Contagem|Total|Number of events processed by the cluster when ingesting from Event Hub|Nenhuma|
|IngestionResult|Ingestion Result|Contagem|Contagem|Number of ingestion operations|Estado|
|CPU|CPU|Percentagem|Média|CPU utilization level|Nenhuma|
| ContinuousExportNumOfRecordsExported | Number of records exported in continuous export | Contagem | Total | Number of records exported for every storage artifact written during the export operation  | Nenhuma |
| ExportUtilization | Export Utilization | Percentagem | Máximo | Export utilization | Nenhuma |
| ContinuousExportPendingCount | Continuous Export Pending Count | Contagem | Máximo | The number of pending continuous export jobs ready for execution | Nenhuma |
| ContinuousExportMaxLatenessMinutes | Continuous Export Max Lateness Minutes | Contagem | Máximo | The max time in minutes of all continuous exports which are pending and ready for execution | Nenhuma |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|Utilização|Utilização|Contagem|Contagem|Count of API calls|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Contagem|Total|Number of workflow runs started.|No Dimensions|
|RunsCompleted|Runs Completed|Contagem|Total|Number of workflow runs completed.|No Dimensions|
|RunsSucceeded|Runs Succeeded|Contagem|Total|Number of workflow runs succeeded.|No Dimensions|
|RunsFailed|Runs Failed|Contagem|Total|Number of workflow runs failed.|No Dimensions|
|RunsCancelled|Runs Canceled|Contagem|Total|Number of workflow runs canceled.|No Dimensions|
|RunLatency|Run Latency|Segundos|Média|Latency of completed workflow runs.|No Dimensions|
|RunSuccessLatency|Run Success Latency|Segundos|Média|Latency of succeeded workflow runs.|No Dimensions|
|RunThrottledEvents|Run Throttled Events|Contagem|Total|Number of workflow action or trigger throttled events.|No Dimensions|
|RunFailurePercentage|Run Failure Percentage|Percentagem|Total|Percentage of workflow runs failed.|No Dimensions|
|ActionsStarted|Actions Started |Contagem|Total|Number of workflow actions started.|No Dimensions|
|ActionsCompleted|Actions Completed |Contagem|Total|Number of workflow actions completed.|No Dimensions|
|ActionsSucceeded|Actions Succeeded |Contagem|Total|Number of workflow actions succeeded.|No Dimensions|
|ActionsFailed|Actions Failed|Contagem|Total|Number of workflow actions failed.|No Dimensions|
|ActionsSkipped|Actions Skipped |Contagem|Total|Number of workflow actions skipped.|No Dimensions|
|ActionLatency|Action Latency |Segundos|Média|Latency of completed workflow actions.|No Dimensions|
|ActionSuccessLatency|Action Success Latency |Segundos|Média|Latency of succeeded workflow actions.|No Dimensions|
|ActionThrottledEvents|Action Throttled Events|Contagem|Total|Number of workflow action throttled events..|No Dimensions|
|TriggersStarted|Triggers Started |Contagem|Total|Number of workflow triggers started.|No Dimensions|
|TriggersCompleted|Triggers Completed |Contagem|Total|Number of workflow triggers completed.|No Dimensions|
|TriggersSucceeded|Triggers Succeeded |Contagem|Total|Number of workflow triggers succeeded.|No Dimensions|
|TriggersFailed|Triggers Failed |Contagem|Total|Number of workflow triggers failed.|No Dimensions|
|TriggersSkipped|Triggers Skipped|Contagem|Total|Number of workflow triggers skipped.|No Dimensions|
|TriggersFired|Triggers Fired |Contagem|Total|Number of workflow triggers fired.|No Dimensions|
|TriggerLatency|Trigger Latency |Segundos|Média|Latency of completed workflow triggers.|No Dimensions|
|TriggerFireLatency|Trigger Fire Latency |Segundos|Média|Latency of fired workflow triggers.|No Dimensions|
|TriggerSuccessLatency|Trigger Success Latency |Segundos|Média|Latency of succeeded workflow triggers.|No Dimensions|
|TriggerThrottledEvents|Trigger Throttled Events|Contagem|Total|Number of workflow trigger throttled events.|No Dimensions|
|BillableActionExecutions|Billable Action Executions|Contagem|Total|Number of workflow action executions getting billed.|No Dimensions|
|BillableTriggerExecutions|Billable Trigger Executions|Contagem|Total|Number of workflow trigger executions getting billed.|No Dimensions|
|TotalBillableExecutions|Total Billable Executions|Contagem|Total|Number of workflow executions getting billed.|No Dimensions|
|BillingUsageNativeOperation|Billing Usage for Native Operation Executions|Contagem|Total|Number of native operation executions getting billed.|No Dimensions|
|BillingUsageStandardConnector|Billing Usage for Standard Connector Executions|Contagem|Total|Number of standard connector executions getting billed.|No Dimensions|
|BillingUsageStorageConsumption|Billing Usage for Storage Consumption Executions|Contagem|Total|Number of storage consumption executions getting billed.|No Dimensions|
|BillingUsageNativeOperation|Billing Usage for Native Operation Executions|Contagem|Total|Number of native operation executions getting billed.|No Dimensions|
|BillingUsageStandardConnector|Billing Usage for Standard Connector Executions|Contagem|Total|Number of standard connector executions getting billed.|No Dimensions|
|BillingUsageStorageConsumption|Billing Usage for Storage Consumption Executions|Contagem|Total|Number of storage consumption executions getting billed.|No Dimensions|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Contagem|Total|Number of workflow runs started.|No Dimensions|
|RunsCompleted|Runs Completed|Contagem|Total|Number of workflow runs completed.|No Dimensions|
|RunsSucceeded|Runs Succeeded|Contagem|Total|Number of workflow runs succeeded.|No Dimensions|
|RunsFailed|Runs Failed|Contagem|Total|Number of workflow runs failed.|No Dimensions|
|RunsCancelled|Runs Canceled|Contagem|Total|Number of workflow runs canceled.|No Dimensions|
|RunLatency|Run Latency|Segundos|Média|Latency of completed workflow runs.|No Dimensions|
|RunSuccessLatency|Run Success Latency|Segundos|Média|Latency of succeeded workflow runs.|No Dimensions|
|RunThrottledEvents|Run Throttled Events|Contagem|Total|Number of workflow action or trigger throttled events.|No Dimensions|
|RunStartThrottledEvents|Run Start Throttled Events|Contagem|Total|Number of workflow run start throttled events.|No Dimensions|
|RunFailurePercentage|Run Failure Percentage|Percentagem|Total|Percentage of workflow runs failed.|No Dimensions|
|ActionsStarted|Actions Started |Contagem|Total|Number of workflow actions started.|No Dimensions|
|ActionsCompleted|Actions Completed |Contagem|Total|Number of workflow actions completed.|No Dimensions|
|ActionsSucceeded|Actions Succeeded |Contagem|Total|Number of workflow actions succeeded.|No Dimensions|
|ActionsFailed|Actions Failed |Contagem|Total|Number of workflow actions failed.|No Dimensions|
|ActionsSkipped|Actions Skipped |Contagem|Total|Number of workflow actions skipped.|No Dimensions|
|ActionLatency|Action Latency |Segundos|Média|Latency of completed workflow actions.|No Dimensions|
|ActionSuccessLatency|Action Success Latency |Segundos|Média|Latency of succeeded workflow actions.|No Dimensions|
|ActionThrottledEvents|Action Throttled Events|Contagem|Total|Number of workflow action throttled events..|No Dimensions|
|TriggersStarted|Triggers Started |Contagem|Total|Number of workflow triggers started.|No Dimensions|
|TriggersCompleted|Triggers Completed |Contagem|Total|Number of workflow triggers completed.|No Dimensions|
|TriggersSucceeded|Triggers Succeeded |Contagem|Total|Number of workflow triggers succeeded.|No Dimensions|
|TriggersFailed|Triggers Failed |Contagem|Total|Number of workflow triggers failed.|No Dimensions|
|TriggersSkipped|Triggers Skipped|Contagem|Total|Number of workflow triggers skipped.|No Dimensions|
|TriggersFired|Triggers Fired |Contagem|Total|Number of workflow triggers fired.|No Dimensions|
|TriggerLatency|Trigger Latency |Segundos|Média|Latency of completed workflow triggers.|No Dimensions|
|TriggerFireLatency|Trigger Fire Latency |Segundos|Média|Latency of fired workflow triggers.|No Dimensions|
|TriggerSuccessLatency|Trigger Success Latency |Segundos|Média|Latency of succeeded workflow triggers.|No Dimensions|
|TriggerThrottledEvents|Trigger Throttled Events|Contagem|Total|Number of workflow trigger throttled events.|No Dimensions|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Workflow Processor Usage for Integration Service Environment|Percentagem|Média|Workflow processor usage for integration service environment.|No Dimensions|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Workflow Memory Usage for Integration Service Environment|Percentagem|Média|Workflow memory usage for integration service environment.|No Dimensions|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Connector Processor Usage for Integration Service Environment|Percentagem|Média|Connector processor usage for integration service environment.|No Dimensions|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Connector Memory Usage for Integration Service Environment|Percentagem|Média|Connector memory usage for integration service environment.|No Dimensions|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|Completed Runs|Completed Runs|Contagem|Total|Number of runs completed successfully for this workspace|Cenário|
|Started Runs|Started Runs|Contagem|Total|Number of runs started for this workspace|Cenário|
|Failed Runs|Failed Runs|Contagem|Total|Number of runs failed for this workspace|Cenário|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|Utilização|Utilização|Contagem|Contagem|Count of API calls|ApiCategory, ApiName, ResultType, ResponseCode|
|Disponibilidade|Disponibilidade|Percentagem|Média|Availability of the APIs|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|AverageOtherLatency|Average other latency|ms/op|Média|Average other latency (that is not read or write) in milliseconds per operation|No Dimensions|
|AverageReadLatency|Average read latency|ms/op|Média|Average read latency in milliseconds per operation|No Dimensions|
|AverageTotalLatency|Average total latency|ms/op|Média|Average total latency in milliseconds per operation|No Dimensions|
|AverageWriteLatency|Average write latency|ms/op|Média|Average write latency in milliseconds per operation|No Dimensions|
|FilesystemOtherOps|Filesystem other ops|ops|Média|Number of filesystem other operations (that is not read or write)|No Dimensions|
|FilesystemReadOps|Filesystem read ops|ops|Média|Number of filesystem read operations|No Dimensions|
|FilesystemTotalOps|Filesystem total ops|ops|Média|Sum of all filesystem operations|No Dimensions|
|FilesystemWriteOps|Filesystem write ops|ops|Média|Number of filesystem write operations|No Dimensions|
|IoBytesPerOtherOps|Io bytes per other ops|bytes/op|Média|Number of In/out bytes per other operations (that is not read or write)|No Dimensions|
|IoBytesPerReadOps|Io bytes per read ops|bytes/op|Média|Number of In/out bytes per read operation|No Dimensions|
|IoBytesPerTotalOps|Io bytes per op across all operations|bytes/op|Média|Sum of all In/out bytes operation|No Dimensions|
|IoBytesPerWriteOps|Io bytes per write ops|bytes/op|Média|Number of In/out bytes per write operation|No Dimensions|
|OtherIops|Other iops|operations/second|Média|Other In/out operation per second|No Dimensions|
|OtherThroughput|Other throughput|MBps|Média|Other throughput (that is not read or write) in megabytes per second|No Dimensions|
|ReadIops|Read iops|operations/second|Média|Read In/out operations per second|No Dimensions|
|ReadThroughput|Read throughput|MBps|Média|Read throughput in megabytes per second|No Dimensions|
|TotalIops|Total iops|operations/second|Média|Sum of all In/out operations per second|No Dimensions|
|TotalThroughput|Total throughput|MBps|Média|Sum of all throughput in megabytes per second|No Dimensions|
|VolumeAllocatedSize|Volume allocated size|bytes|Média|Allocated size of the volume (Not the actual used bytes)|No Dimensions|
|VolumeLogicalSize|Volume logical size|bytes|Média|Logical size of the volume (used bytes)|No Dimensions|
|VolumeSnapshotSize|Volume snapshot size|bytes|Média|Size of all snapshots in volume|No Dimensions|
|WriteIops|Write iops|operations/second|Média|Write In/out operations per second|No Dimensions|
|WriteThroughput|Write throughput|MBps|Média|Write throughput in megabytes per second|No Dimensions|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Volume pool allocated size|bytes|Média|Allocated size of the pool (Not the actual used bytes)|No Dimensions|
|VolumePoolAllocatedUsed|Volume pool allocated used|bytes|Média|Allocated used size of the pool|No Dimensions|
|VolumePoolTotalLogicalSize|Volume pool total logical size|bytes|Média|Sum of the logical size of all the volumes belonging to the pool|No Dimensions|
|VolumePoolTotalSnapshotSize|Volume pool total snapshot size|bytes|Média|Sum of all snapshots in pool|No Dimensions|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|BytesSentRate|Bytes Sent|Contagem|Total|Number of bytes the Network Interface sent|No Dimensions|
|BytesReceivedRate|Bytes Received|Contagem|Total|Number of bytes the Network Interface received|No Dimensions|
|PacketsSentRate|Packets Sent|Contagem|Total|Number of packets the Network Interface sent|No Dimensions|
|PacketsReceivedRate|Packets Received|Contagem|Total|Number of packets the Network Interface received|No Dimensions|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|VipAvailability|Data Path Availability|Contagem|Média|Average Load Balancer data path availability per time duration|FrontendIPAddress, FrontendPort|
|DipAvailability|Health Probe Status|Contagem|Média|Average Load Balancer health probe status per time duration|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Byte Count|Contagem|Total|Total number of Bytes transmitted within time period|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Packet Count|Contagem|Total|Total number of Packets transmitted within time period|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|SYN Count|Contagem|Total|Total number of SYN Packets transmitted within time period|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|SNAT Connection Count|Contagem|Total|Total number of new SNAT connections created within time period|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Allocated SNAT Ports (Preview)|Contagem|Total|Total number of SNAT ports allocated within time period|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Used SNAT Ports (Preview)|Contagem|Total|Total number of SNAT ports used within time period|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueryVolume|Query Volume|Contagem|Total|Number of queries served for a DNS zone|No Dimensions|
|RecordSetCount|Record Set Count|Contagem|Máximo|Number of Record Sets in a DNS zone|No Dimensions|
|RecordSetCapacityUtilization|Record Set Capacity Utilization|Percentagem|Máximo|Percent of Record Set capacity utilized by a DNS zone|No Dimensions|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|PacketsInDDoS|Inbound packets DDoS|CountPerSecond|Máximo|Inbound packets DDoS|No Dimensions|
|PacketsDroppedDDoS|Inbound packets dropped DDoS|CountPerSecond|Máximo|Inbound packets dropped DDoS|No Dimensions|
|PacketsForwardedDDoS|Inbound packets forwarded DDoS|CountPerSecond|Máximo|Inbound packets forwarded DDoS|No Dimensions|
|TCPPacketsInDDoS|Inbound TCP packets DDoS|CountPerSecond|Máximo|Inbound TCP packets DDoS|No Dimensions|
|TCPPacketsDroppedDDoS|Inbound TCP packets dropped DDoS|CountPerSecond|Máximo|Inbound TCP packets dropped DDoS|No Dimensions|
|TCPPacketsForwardedDDoS|Inbound TCP packets forwarded DDoS|CountPerSecond|Máximo|Inbound TCP packets forwarded DDoS|No Dimensions|
|UDPPacketsInDDoS|Inbound UDP packets DDoS|CountPerSecond|Máximo|Inbound UDP packets DDoS|No Dimensions|
|UDPPacketsDroppedDDoS|Inbound UDP packets dropped DDoS|CountPerSecond|Máximo|Inbound UDP packets dropped DDoS|No Dimensions|
|UDPPacketsForwardedDDoS|Inbound UDP packets forwarded DDoS|CountPerSecond|Máximo|Inbound UDP packets forwarded DDoS|No Dimensions|
|BytesInDDoS|Inbound bytes DDoS|BytesPerSecond|Máximo|Inbound bytes DDoS|No Dimensions|
|BytesDroppedDDoS|Inbound bytes dropped DDoS|BytesPerSecond|Máximo|Inbound bytes dropped DDoS|No Dimensions|
|BytesForwardedDDoS|Inbound bytes forwarded DDoS|BytesPerSecond|Máximo|Inbound bytes forwarded DDoS|No Dimensions|
|TCPBytesInDDoS|Inbound TCP bytes DDoS|BytesPerSecond|Máximo|Inbound TCP bytes DDoS|No Dimensions|
|TCPBytesDroppedDDoS|Inbound TCP bytes dropped DDoS|BytesPerSecond|Máximo|Inbound TCP bytes dropped DDoS|No Dimensions|
|TCPBytesForwardedDDoS|Inbound TCP bytes forwarded DDoS|BytesPerSecond|Máximo|Inbound TCP bytes forwarded DDoS|No Dimensions|
|UDPBytesInDDoS|Inbound UDP bytes DDoS|BytesPerSecond|Máximo|Inbound UDP bytes DDoS|No Dimensions|
|UDPBytesDroppedDDoS|Inbound UDP bytes dropped DDoS|BytesPerSecond|Máximo|Inbound UDP bytes dropped DDoS|No Dimensions|
|UDPBytesForwardedDDoS|Inbound UDP bytes forwarded DDoS|BytesPerSecond|Máximo|Inbound UDP bytes forwarded DDoS|No Dimensions|
|IfUnderDDoSAttack|Under DDoS attack or not|Contagem|Máximo|Under DDoS attack or not|No Dimensions|
|DDoSTriggerTCPPackets|Inbound TCP packets to trigger DDoS mitigation|CountPerSecond|Máximo|Inbound TCP packets to trigger DDoS mitigation|No Dimensions|
|DDoSTriggerUDPPackets|Inbound UDP packets to trigger DDoS mitigation|CountPerSecond|Máximo|Inbound UDP packets to trigger DDoS mitigation|No Dimensions|
|DDoSTriggerSYNPackets|Inbound SYN packets to trigger DDoS mitigation|CountPerSecond|Máximo|Inbound SYN packets to trigger DDoS mitigation|No Dimensions|
|VipAvailability|Data Path Availability|Contagem|Média|Average IP Address availability per time duration|Porta|
|ByteCount|Byte Count|Contagem|Total|Total number of Bytes transmitted within time period|Port, Direction|
|PacketCount|Packet Count|Contagem|Total|Total number of Packets transmitted within time period|Port, Direction|
|SynCount|SYN Count|Contagem|Total|Total number of SYN Packets transmitted within time period|Port, Direction|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|ApplicationRuleHit|Application rules hit count|Contagem|Total|Number of times Application rules were hit|Status, Reason, Protocol|
|NetworkRuleHit|Network rules hit count|Contagem|Total|Number of times Network rules were hit|Status, Reason, Protocol|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|Débito|Débito|BytesPerSecond|Total|Number of bytes per second the Application Gateway has served|No Dimensions|
|UnhealthyHostCount|Unhealthy Host Count|Contagem|Média|Number of unhealthy backend hosts|BackendSettingsPool|
|HealthyHostCount|Healthy Host Count|Contagem|Média|Number of healthy backend hosts|BackendSettingsPool|
|TotalRequests|Total de Pedidos|Contagem|Total|Count of successful requests that Application Gateway has served|BackendSettingsPool|
|FailedRequests|Pedidos com Falhas|Contagem|Total|Count of failed requests that Application Gateway has served|BackendSettingsPool|
|ResponseStatus|Response Status|Contagem|Total|Http response status returned by Application Gateway|HttpStatusGroup|
|CurrentConnections|Current Connections|Contagem|Total|Count of current connections established with Application Gateway|No Dimensions|
|CapacityUnits|Current Capacity Units|Contagem|Média|Capacity Units consumed|No Dimensions|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|AverageBandwidth|Gateway S2S Bandwidth|BytesPerSecond|Média|Average site-to-site bandwidth of a gateway in bytes per second|No Dimensions|
|P2SBandwidth|Gateway P2S Bandwidth|BytesPerSecond|Média|Average point-to-site bandwidth of a gateway in bytes per second|No Dimensions|
|P2SConnectionCount|P2S Connection Count|Contagem|Máximo|Point-to-site connection count of a gateway|Protocolo|
|TunnelAverageBandwidth|Tunnel Bandwidth|BytesPerSecond|Média|Average bandwidth of a tunnel in bytes per second|ConnectionName, RemoteIP|
|TunnelEgressBytes|Tunnel Egress Bytes|Bytes|Total|Outgoing bytes of a tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Tunnel Ingress Bytes|Bytes|Total|Incoming bytes of a tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Tunnel Egress Packets|Contagem|Total|Outgoing packet count of a tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Tunnel Ingress Packets|Contagem|Total|Incoming packet count of a tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Tunnel Egress TS Mismatch Packet Drop|Contagem|Total|Outgoing packet drop count from traffic selector mismatch of a tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Tunnel Ingress TS Mismatch Packet Drop|Contagem|Total|Incoming packet drop count from traffic selector mismatch of a tunnel|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits ingressing Azure per second|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits egressing Azure per second|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits ingressing Azure per second|No Dimensions|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits egressing Azure per second|No Dimensions|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits ingressing Azure per second|No Dimensions|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits egressing Azure per second|No Dimensions|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|QpsByEndpoint|Queries by Endpoint Returned|Contagem|Total|Number of times a Traffic Manager endpoint was returned in the given time frame|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Endpoint Status by Endpoint|Contagem|Máximo|1 if an endpoint's probe status is "Enabled", 0 otherwise.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Probes Failed|Percentagem|Média|% of connectivity monitoring probes failed|No Dimensions|
|AverageRoundtripMs|Avg. Round-trip Time (ms)|MilliSeconds|Média|Average network round-trip time (ms) for connectivity monitoring probes sent between source and destination|No Dimensions|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|RequestCount|Número de Pedidos|Contagem|Total|The number of client requests served by the HTTP/S proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Request Size|Bytes|Total|The number of bytes sent as requests from clients to the HTTP/S proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Response Size|Bytes|Total|The number of bytes sent as responses from HTTP/S proxy to clients|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Backend Request Count|Contagem|Total|The number of requests sent from the HTTP/S proxy to backends|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Backend Request Latency|MilliSeconds|Média|The time calculated from when the request was sent by the HTTP/S proxy to the backend until the HTTP/S proxy received the last response byte from the backend|Backend|
|TotalLatency|Total Latency|MilliSeconds|Média|The time calculated from when the client request was received by the HTTP/S proxy until the client acknowledged the last response byte from the HTTP/S proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Backend Health Percentage|Percentagem|Média|The percentage of successful health probes from the HTTP/S proxy to backends|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Web Application Firewall Request Count|Contagem|Total|The number of client requests processed by the Web Application Firewall|PolicyName, RuleName, Action|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|registration.all|Registration Operations|Contagem|Total|The count of all successful registration operations (creations updates queries and deletions). |No Dimensions|
|registration.create|Registration Create Operations|Contagem|Total|The count of all successful registration creations.|No Dimensions|
|registration.update|Registration Update Operations|Contagem|Total|The count of all successful registration updates.|No Dimensions|
|registration.get|Registration Read Operations|Contagem|Total|The count of all successful registration queries.|No Dimensions|
|registration.delete|Registration Delete Operations|Contagem|Total|The count of all successful registration deletions.|No Dimensions|
|incoming|Incoming Messages|Contagem|Total|The count of all successful send API calls. |No Dimensions|
|incoming.scheduled|Scheduled Push Notifications Sent|Contagem|Total|Scheduled Push Notifications Canceled|No Dimensions|
|incoming.scheduled.cancel|Scheduled Push Notifications Canceled|Contagem|Total|Scheduled Push Notifications Canceled|No Dimensions|
|scheduled.pending|Pending Scheduled Notifications|Contagem|Total|Pending Scheduled Notifications|No Dimensions|
|installation.all|Installation Management Operations|Contagem|Total|Installation Management Operations|No Dimensions|
|installation.get|Get Installation Operations|Contagem|Total|Get Installation Operations|No Dimensions|
|installation.upsert|Create or Update Installation Operations|Contagem|Total|Create or Update Installation Operations|No Dimensions|
|installation.patch|Patch Installation Operations|Contagem|Total|Patch Installation Operations|No Dimensions|
|installation.delete|Delete Installation Operations|Contagem|Total|Delete Installation Operations|No Dimensions|
|outgoing.allpns.success|Successful notifications|Contagem|Total|The count of all successful notifications.|No Dimensions|
|outgoing.allpns.invalidpayload|Payload Errors|Contagem|Total|The count of pushes that failed because the PNS returned a bad payload error.|No Dimensions|
|outgoing.allpns.pnserror|External Notification System Errors|Contagem|Total|The count of pushes that failed because there was a problem communicating with the PNS (excludes authentication problems).|No Dimensions|
|outgoing.allpns.channelerror|Channel Errors|Contagem|Total|The count of pushes that failed because the channel was invalid not associated with the correct app throttled or expired.|No Dimensions|
|outgoing.allpns.badorexpiredchannel|Bad or Expired Channel Errors|Contagem|Total|The count of pushes that failed because the channel/token/registrationId in the registration was expired or invalid.|No Dimensions|
|outgoing.wns.success|WNS Successful Notifications|Contagem|Total|The count of all successful notifications.|No Dimensions|
|outgoing.wns.invalidcredentials|WNS Authorization Errors (Invalid Credentials)|Contagem|Total|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked. (Windows Live does not recognize the credentials).|No Dimensions|
|outgoing.wns.badchannel|WNS Bad Channel Error|Contagem|Total|The count of pushes that failed because the ChannelURI in the registration was not recognized (WNS status: 404 not found).|No Dimensions|
|outgoing.wns.expiredchannel|WNS Expired Channel Error|Contagem|Total|The count of pushes that failed because the ChannelURI is expired (WNS status: 410 Gone).|No Dimensions|
|outgoing.wns.throttled|WNS Throttled Notifications|Contagem|Total|The count of pushes that failed because WNS is throttling this app (WNS status: 406 Not Acceptable).|No Dimensions|
|outgoing.wns.tokenproviderunreachable|WNS Authorization Errors (Unreachable)|Contagem|Total|Windows Live is not reachable.|No Dimensions|
|outgoing.wns.invalidtoken|WNS Authorization Errors (Invalid Token)|Contagem|Total|The token provided to WNS is not valid (WNS status: 401 Unauthorized).|No Dimensions|
|outgoing.wns.wrongtoken|WNS Authorization Errors (Wrong Token)|Contagem|Total|The token provided to WNS is valid but for another application (WNS status: 403 Forbidden). This can happen if the ChannelURI in the registration is associated with another app. Check that the client app is associated with the same app whose credentials are in the notification hub.|No Dimensions|
|outgoing.wns.invalidnotificationformat|WNS Invalid Notification Format|Contagem|Total|The format of the notification is invalid (WNS status: 400). Note that WNS does not reject all invalid payloads.|No Dimensions|
|outgoing.wns.invalidnotificationsize|WNS Invalid Notification Size Error|Contagem|Total|The notification payload is too large (WNS status: 413).|No Dimensions|
|outgoing.wns.channelthrottled|WNS Channel Throttled|Contagem|Total|The notification was dropped because the ChannelURI in the registration is throttled (WNS response header: X-WNS-NotificationStatus:channelThrottled).|No Dimensions|
|outgoing.wns.channeldisconnected|WNS Channel Disconnected|Contagem|Total|The notification was dropped because the ChannelURI in the registration is throttled (WNS response header: X-WNS-DeviceConnectionStatus: disconnected).|No Dimensions|
|outgoing.wns.dropped|WNS Dropped Notifications|Contagem|Total|The notification was dropped because the ChannelURI in the registration is throttled (X-WNS-NotificationStatus: dropped but not X-WNS-DeviceConnectionStatus: disconnected).|No Dimensions|
|outgoing.wns.pnserror|WNS Errors|Contagem|Total|Notification not delivered because of errors communicating with WNS.|No Dimensions|
|outgoing.wns.authenticationerror|WNS Authentication Errors|Contagem|Total|Notification not delivered because of errors communicating with Windows Live invalid credentials or wrong token.|No Dimensions|
|outgoing.apns.success|APNS Successful Notifications|Contagem|Total|The count of all successful notifications.|No Dimensions|
|outgoing.apns.invalidcredentials|APNS Authorization Errors|Contagem|Total|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|outgoing.apns.badchannel|APNS Bad Channel Error|Contagem|Total|The count of pushes that failed because the token is invalid (APNS binary protocol status code: 8. APNS HTTP protocol status code: 400 with "BadDeviceToken").|No Dimensions|
|outgoing.apns.expiredchannel|APNS Expired Channel Error|Contagem|Total|The count of token that were invalidated by the APNS feedback channel.|No Dimensions|
|outgoing.apns.invalidnotificationsize|APNS Invalid Notification Size Error|Contagem|Total|The count of pushes that failed because the payload was too large (APNS binary protocol status code: 7).|No Dimensions|
|outgoing.apns.pnserror|APNS Errors|Contagem|Total|The count of pushes that failed because of errors communicating with APNS.|No Dimensions|
|outgoing.gcm.success|GCM Successful Notifications|Contagem|Total|The count of all successful notifications.|No Dimensions|
|outgoing.gcm.invalidcredentials|GCM Authorization Errors (Invalid Credentials)|Contagem|Total|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|outgoing.gcm.badchannel|GCM Bad Channel Error|Contagem|Total|The count of pushes that failed because the registrationId in the registration was not recognized (GCM result: Invalid Registration).|No Dimensions|
|outgoing.gcm.expiredchannel|GCM Expired Channel Error|Contagem|Total|The count of pushes that failed because the registrationId in the registration was expired (GCM result: NotRegistered).|No Dimensions|
|outgoing.gcm.throttled|GCM Throttled Notifications|Contagem|Total|The count of pushes that failed because GCM throttled this app (GCM status code: 501-599 or result:Unavailable).|No Dimensions|
|outgoing.gcm.invalidnotificationformat|GCM Invalid Notification Format|Contagem|Total|The count of pushes that failed because the payload was not formatted correctly (GCM result: InvalidDataKey or InvalidTtl).|No Dimensions|
|outgoing.gcm.invalidnotificationsize|GCM Invalid Notification Size Error|Contagem|Total|The count of pushes that failed because the payload was too large (GCM result: MessageTooBig).|No Dimensions|
|outgoing.gcm.wrongchannel|GCM Wrong Channel Error|Contagem|Total|The count of pushes that failed because the registrationId in the registration is not associated to the current app (GCM result: InvalidPackageName).|No Dimensions|
|outgoing.gcm.pnserror|GCM Errors|Contagem|Total|The count of pushes that failed because of errors communicating with GCM.|No Dimensions|
|outgoing.gcm.authenticationerror|GCM Authentication Errors|Contagem|Total|The count of pushes that failed because the PNS did not accept the provided credentials the credentials are blocked or the SenderId is not correctly configured in the app (GCM result: MismatchedSenderId).|No Dimensions|
|outgoing.mpns.success|MPNS Successful Notifications|Contagem|Total|The count of all successful notifications.|No Dimensions|
|outgoing.mpns.invalidcredentials|MPNS Invalid Credentials|Contagem|Total|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|outgoing.mpns.badchannel|MPNS Bad Channel Error|Contagem|Total|The count of pushes that failed because the ChannelURI in the registration was not recognized (MPNS status: 404 not found).|No Dimensions|
|outgoing.mpns.throttled|MPNS Throttled Notifications|Contagem|Total|The count of pushes that failed because MPNS is throttling this app (WNS MPNS: 406 Not Acceptable).|No Dimensions|
|outgoing.mpns.invalidnotificationformat|MPNS Invalid Notification Format|Contagem|Total|The count of pushes that failed because the payload of the notification was too large.|No Dimensions|
|outgoing.mpns.channeldisconnected|MPNS Channel Disconnected|Contagem|Total|The count of pushes that failed because the ChannelURI in the registration was disconnected (MPNS status: 412 not found).|No Dimensions|
|outgoing.mpns.dropped|MPNS Dropped Notifications|Contagem|Total|The count of pushes that were dropped by MPNS (MPNS response header: X-NotificationStatus: QueueFull or Suppressed).|No Dimensions|
|outgoing.mpns.pnserror|MPNS Errors|Contagem|Total|The count of pushes that failed because of errors communicating with MPNS.|No Dimensions|
|outgoing.mpns.authenticationerror|MPNS Authentication Errors|Contagem|Total|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|notificationhub.pushes|All Outgoing Notifications|Contagem|Total|All outgoing notifications of the notification hub|No Dimensions|
|incoming.all.requests|All Incoming Requests|Contagem|Total|Total incoming requests for a notification hub|No Dimensions|
|incoming.all.failedrequests|All Incoming Failed Requests|Contagem|Total|Total incoming failed requests for a notification hub|No Dimensions|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|Average_% Free Inodes|% Free Inodes|Contagem|Média|Average_% Free Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% Free Space|Contagem|Média|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Inodes|% Used Inodes|Contagem|Média|Average_% Used Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Space|% Used Space|Contagem|Média|Average_% Used Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|Disk Read Bytes/sec|Contagem|Média|Average_Disk Read Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Disk Reads/sec|Contagem|Média|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Contagem|Média|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|Disk Write Bytes/sec|Contagem|Média|Average_Disk Write Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Disk Writes/sec|Contagem|Média|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Free Megabytes|Contagem|Média|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|Logical Disk Bytes/sec|Contagem|Média|Average_Logical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Memory|% Available Memory|Contagem|Média|Average_% Available Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Swap Space|% Available Swap Space|Contagem|Média|Average_% Available Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Memory|% Used Memory|Contagem|Média|Average_% Used Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Swap Space|% Used Swap Space|Contagem|Média|Average_% Used Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Memory|Available MBytes Memory|Contagem|Média|Average_Available MBytes Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Swap|Available MBytes Swap|Contagem|Média|Average_Available MBytes Swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Reads/sec|Page Reads/sec|Contagem|Média|Average_Page Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Writes/sec|Page Writes/sec|Contagem|Média|Average_Page Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Pages/sec|Contagem|Média|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|Used MBytes Swap Space|Contagem|Média|Average_Used MBytes Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|Used Memory MBytes|Contagem|Média|Average_Used Memory MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|Total Bytes Transmitted|Contagem|Média|Average_Total Bytes Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|Total Bytes Received|Contagem|Média|Average_Total Bytes Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|Total Bytes|Contagem|Média|Average_Total Bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|Total Packets Transmitted|Contagem|Média|Average_Total Packets Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Received|Total Packets Received|Contagem|Média|Average_Total Packets Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|Total Rx Errors|Contagem|Média|Average_Total Rx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx Errors|Total Tx Errors|Contagem|Média|Average_Total Tx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Collisions|Total Collisions|Contagem|Média|Average_Total Collisions|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Read|Avg. Disk sec/Read|Contagem|Média|Average_Avg. Disk sec/Read|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Transfer|Avg. Disk sec/Transfer|Contagem|Média|Average_Avg. Disk sec/Transfer|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Write|Avg. Disk sec/Write|Contagem|Média|Average_Avg. Disk sec/Write|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|Physical Disk Bytes/sec|Contagem|Média|Average_Physical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|Pct Privileged Time|Contagem|Média|Average_Pct Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct User Time|Pct User Time|Contagem|Média|Average_Pct User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory kBytes|Used Memory kBytes|Contagem|Média|Average_Used Memory kBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|Virtual Shared Memory|Contagem|Média|Average_Virtual Shared Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC Time|% DPC Time|Contagem|Média|Average_% DPC Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Idle Time|% Idle Time|Contagem|Média|Average_% Idle Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Interrupt Time|% Interrupt Time|Contagem|Média|Average_% Interrupt Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO Wait Time|% IO Wait Time|Contagem|Média|Average_% IO Wait Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Nice Time|% Nice Time|Contagem|Média|Average_% Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|% Privileged Time|Contagem|Média|Average_% Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% Processor Time|Contagem|Média|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% User Time|% User Time|Contagem|Média|Average_% User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Physical Memory|Free Physical Memory|Contagem|Média|Average_Free Physical Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|Free Space in Paging Files|Contagem|Média|Average_Free Space in Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Virtual Memory|Free Virtual Memory|Contagem|Média|Average_Free Virtual Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Processos|Contagem|Média|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|Size Stored In Paging Files|Contagem|Média|Average_Size Stored In Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Uptime|Contagem|Média|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Utilizadores|Contagem|Média|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Read|Avg. Disk sec/Read|Contagem|Média|Average_Avg. Disk sec/Read|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Write|Avg. Disk sec/Write|Contagem|Média|Average_Avg. Disk sec/Write|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|Current Disk Queue Length|Contagem|Média|Average_Current Disk Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Disk Reads/sec|Contagem|Média|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Contagem|Média|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Disk Writes/sec|Contagem|Média|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Free Megabytes|Contagem|Média|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% Free Space|Contagem|Média|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|Available MBytes|Contagem|Média|Average_Available MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|% Committed Bytes In Use|Contagem|Média|Average_% Committed Bytes In Use|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Bytes Received/sec|Contagem|Média|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Bytes Sent/sec|Contagem|Média|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Bytes Total/sec|Contagem|Média|Average_Bytes Total/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% Processor Time|Contagem|Média|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Queue Length|Processor Queue Length|Contagem|Média|Average_Processor Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Heartbeat|Heartbeat|Contagem|Total|Heartbeat|Computer, OSType, Version, SourceComputerId|
|Atualizar|Atualizar|Contagem|Média|Atualizar|Computer, Product, Classification, UpdateState, Optional, Approved|
|Evento|Evento|Contagem|Média|Evento|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueryDuration|Query Duration|Milliseconds|Média|DAX Query duration in last interval|No Dimensions|
|QueryPoolJobQueueLength|Threads: Query pool job queue length|Contagem|Média|Number of jobs in the queue of the query thread pool.|No Dimensions|
|qpu_high_utilization_metric|QPU High Utilization|Contagem|Total|QPU High Utilization In Last Minute, 1 For High QPU Utilization, Otherwise 0|No Dimensions|
|memory_metric|Memória|Bytes|Média|Memory. Range 0-3 GB for A1, 0-5 GB for A2, 0-10 GB for A3, 0-25 GB for A4, 0-50 GB for A5 and 0-100 GB for A6|No Dimensions|
|memory_thrashing_metric|Memory Thrashing|Percentagem|Média|Average memory thrashing.|No Dimensions|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Contagem|Total|Successful ListenerConnections for Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Contagem|Total|ClientError on ListenerConnections for Microsoft.Relay.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Contagem|Total|ServerError on ListenerConnections for Microsoft.Relay.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Contagem|Total|Successful SenderConnections for Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Contagem|Total|ClientError on SenderConnections for Microsoft.Relay.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Contagem|Total|ServerError on SenderConnections for Microsoft.Relay.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Contagem|Total|Total ListenerConnections for Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Contagem|Total|Total SenderConnections requests for Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Contagem|Total|Total ActiveConnections for Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Contagem|Total|Total ActiveListeners for Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Contagem|Total|Total BytesTransferred for Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Contagem|Total|Total ListenerDisconnects for Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Contagem|Total|Total SenderDisconnects for Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|SearchLatency|Search Latency|Segundos|Média|Average search latency for the search service|No Dimensions|
|SearchQueriesPerSecond|Search queries per second|CountPerSecond|Média|Search queries per second for the search service|No Dimensions|
|ThrottledSearchQueriesPercentage|Throttled search queries percentage|Percentagem|Média|Percentage of search queries that were throttled for the search service|No Dimensions|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfulRequests|Successful Requests (Preview)|Contagem|Total|Total successful requests for a namespace (Preview)|EntityName|
|ServerErrors|Server Errors. (Pré-visualização)|Contagem|Total|Server Errors for Microsoft.ServiceBus. (Pré-visualização)|EntityName|
|UserErrors|User Errors. (Pré-visualização)|Contagem|Total|User Errors for Microsoft.ServiceBus. (Pré-visualização)|EntityName|
|ThrottledRequests|Throttled Requests. (Pré-visualização)|Contagem|Total|Throttled Requests for Microsoft.ServiceBus. (Pré-visualização)|EntityName|
|IncomingRequests|Incoming Requests (Preview)|Contagem|Total|Incoming Requests for Microsoft.ServiceBus. (Pré-visualização)|EntityName|
|IncomingMessages|Incoming Messages (Preview)|Contagem|Total|Incoming Messages for Microsoft.ServiceBus. (Pré-visualização)|EntityName|
|OutgoingMessages|Outgoing Messages (Preview)|Contagem|Total|Outgoing Messages for Microsoft.ServiceBus. (Pré-visualização)|EntityName|
|ActiveConnections|ActiveConnections (Preview)|Contagem|Total|Total Active Connections for Microsoft.ServiceBus. (Pré-visualização)|No Dimensions|
|Tamanho|Size (Preview)|Bytes|Média|Size of an Queue/Topic in Bytes. (Pré-visualização)|EntityName|
|Mensagens|Count of messages in a Queue/Topic. (Pré-visualização)|Contagem|Média|Count of messages in a Queue/Topic. (Pré-visualização)|EntityName|
|ActiveMessages|Count of active messages in a Queue/Topic. (Pré-visualização)|Contagem|Média|Count of active messages in a Queue/Topic. (Pré-visualização)|EntityName|
|DeadletteredMessages|Count of dead-lettered messages in a Queue/Topic. (Pré-visualização)|Contagem|Média|Count of dead-lettered messages in a Queue/Topic. (Pré-visualização)|EntityName|
|ScheduledMessages|Count of scheduled messages in a Queue/Topic. (Pré-visualização)|Contagem|Média|Count of scheduled messages in a Queue/Topic. (Pré-visualização)|EntityName|
|CPUXNS|CPU usage per namespace|Percentagem|Máximo|Service bus premium namespace CPU usage metric|No Dimensions|
|WSXNS|Memory size usage per namespace|Percentagem|Máximo|Service bus premium namespace memory usage metric|No Dimensions|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Contagem|Média|Cpu allocated to this container in millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Bytes|Média|Memory allocated to this container in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Contagem|Média|Actual CPU usage in millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Bytes|Média|Actual memory usage in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Percentagem|Média|Utilization of CPU for this container as percentage of AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Percentagem|Média|Utilization of CPU for this container as percentage of AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Contagem|Média|Status of Service Fabric Mesh application|ApplicationName, Status|
|ServiceStatus|ServiceStatus|Contagem|Média|Health Status of a service in Service Fabric Mesh application|ApplicationName, Status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Contagem|Média|Health Status of a service replica in Service Fabric Mesh application|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Contagem|Média|Status of the container in Service Fabric Mesh application|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|RestartCount|RestartCount|Contagem|Média|Restart count of a container in Service Fabric Mesh application|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|ConnectionCount|Connection Count|Contagem|Máximo|The amount of user connection.|Ponto Final|
|MessageCount|Message Count|Contagem|Total|The total amount of messages.|No Dimensions|
|InboundTraffic|Tráfego de Entrada|Bytes|Total|The inbound traffic of service|No Dimensions|
|OutboundTraffic|Tráfego de Saída|Bytes|Total|The outbound traffic of service|No Dimensions|
|UserErrors|User Errors|Percentagem|Máximo|The percentage of user errors|No Dimensions|
|SystemErrors|System Errors|Percentagem|Máximo|The percentage of system errors|No Dimensions|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|allocated_data_storage|Data space allocated|Bytes|Média|Data space allocated. Not applicable to data warehouses.|No Dimensions|
|app_cpu_billed|App CPU billed|Contagem|Total|App CPU billed. Applies to serverless databases.|No Dimensions|
|app_cpu_percent|App CPU percentage|Percentagem|Média|App CPU percentage. Applies to serverless databases.|No Dimensions|
|app_memory_percent|App memory used percentage|Percentagem|Média|App memory used percentage. Applies to serverless databases.|No Dimensions|
|blocked_by_firewall|Blocked by Firewall|Contagem|Total|Blocked by Firewall|No Dimensions|
|cache_hit_percent|Cache hit percentage|Percentagem|Máximo|Cache hit percentage. Applies only to data warehouses.|No Dimensions|
|cache_used_percent|Cache used percentage|Percentagem|Máximo|Cache used percentage. Applies only to data warehouses.|No Dimensions|
|connection_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|No Dimensions|
|connection_successful|Successful Connections|Contagem|Total|Successful Connections|No Dimensions|
|cpu_percent|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|No Dimensions|
|cpu_limit|CPU limit|Contagem|Média|CPU limit. Applies to vCore-based databases.|No Dimensions|
|cpu_used|CPU used|Contagem|Média|CPU used. Applies to vCore-based databases.|No Dimensions|
|deadlock|Deadlocks|Contagem|Total|Deadlocks. Not applicable to data warehouses.|No Dimensions|
|diff_backup_size_bytes|Differential backup storage size|Bytes|Máximo|Cumulative differential backup storage size. Applies to General Purpose and Business Critical databases. Does not currently apply to Manage instance databases.|No Dimensions|
|dtu_limit|DTU Limit|Contagem|Média|DTU Limit. Applies to DTU-based databases.|No Dimensions|
|dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|DTU percentage. Applies to DTU-based databases.|No Dimensions|
|dtu_used|DTU used|Contagem|Média|DTU used. Applies to DTU-based databases.|No Dimensions|
|dw_cpu_percent|DW node level CPU percentage|Percentagem|Média|DW node level CPU percentage|DwLogicalNodeId|
|dw_physical_data_read_percent|DW node level Data IO percentage|Percentagem|Média|DW node level Data IO percentage|DwLogicalNodeId|
|dwu_consumption_percent|DWU percentage|Percentagem|Máximo|DWU percentage. Applies only to data warehouses.|No Dimensions|
|dwu_limit|DWU limit|Contagem|Máximo|DWU limit. Applies only to data warehouses.|No Dimensions|
|dwu_used|DWU used|Contagem|Máximo|DWU used. Applies only to data warehouses.|No Dimensions|
|full_backup_size_bytes|Full backup storage size|Bytes|Máximo|Cumulative full backup storage size. Applies to General Purpose and Business Critical databases. Does not currently apply to Manage instance databases.|No Dimensions|
|local_tempdb_usage_percent|Local tempdb percentage|Percentagem|Média|Local tempdb percentage. Applies only to data warehouses.|No Dimensions|
|log_backup_size_bytes|Log backup storage size|Bytes|Máximo|Cumulative log backup storage size. Applies to General Purpose and Business Critical databases. Does not currently apply to Manage instance databases.|No Dimensions|
|log_write_percent|Log IO percentage|Percentagem|Média|Log IO percentage. Not applicable to data warehouses.|No Dimensions|
|physical_data_read_percent|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|No Dimensions|
|sessions_percent|Sessions percentage|Percentagem|Média|Sessions percentage. Not applicable to data warehouses.|No Dimensions|
|sqlserver_process_core_percent|SQL Server process core percent|Percentagem|Máximo|This metric is a placeholder and not populated at this time.|No Dimensions|
|sqlserver_process_memory_percent|SQL Server process memory percent|Percentagem|Máximo|This metric is a placeholder and not populated at this time.|No Dimensions|
|armazenamento|Data space used|Bytes|Máximo|Total database size. Not applicable to data warehouses.|No Dimensions|
|storage_percent|Data space used percent|Percentagem|Máximo|Database size percentage. Not applicable to data warehouses or hyperscale databases.|No Dimensions|
|tempdb_data_size|Tempdb Data File Size Kilobytes|Contagem|Máximo|Tempdb Data File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_size|Tempdb Log File Size Kilobytes|Contagem|Máximo|Tempdb Log File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_used_percent|Tempdb Percent Log Used|Percentagem|Máximo|Tempdb Percent Log Used. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|workers_percent|Workers percentage|Percentagem|Média|Workers percentage. Not applicable to data warehouses.|No Dimensions|
|xtp_storage_percent|In-Memory OLTP storage percent|Percentagem|Média|In-Memory OLTP storage percent. Not applicable to data warehouses.|No Dimensions|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|allocated_data_storage|Data space allocated|Bytes|Média|Data space allocated|No Dimensions|
|allocated_data_storage_percent|Data space allocated percent|Percentagem|Máximo|Data space allocated percent|No Dimensions|
|cpu_limit|CPU limit|Contagem|Média|CPU limit. Applies to vCore-based elastic pools.|No Dimensions|
|cpu_percent|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|No Dimensions|
|cpu_used|CPU used|Contagem|Média|CPU used. Applies to vCore-based elastic pools.|No Dimensions|
|dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|DTU percentage. Applies to DTU-based elastic pools.|No Dimensions|
|eDTU_limit|eDTU limit|Contagem|Média|eDTU limit. Applies to DTU-based elastic pools.|No Dimensions|
|eDTU_used|eDTU used|Contagem|Média|eDTU used. Applies to DTU-based elastic pools.|No Dimensions|
|log_write_percent|Log IO percentage|Percentagem|Média|Log IO percentage|No Dimensions|
|physical_data_read_percent|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|No Dimensions|
|sessions_percent|Sessions percentage|Percentagem|Média|Sessions percentage|No Dimensions|
|storage_limit|Data max size|Bytes|Média|Storage limit|No Dimensions|
|storage_percent|Data space used percent||Percentagem|Média|Storage percentage|No Dimensions|
|storage_used|Data space used|Bytes|Média|Armazenamento utilizado|No Dimensions|
|sqlserver_process_core_percent|SQL Server process core percent|Percentagem|Máximo|This metric is a placeholder and not populated at this time.|No Dimensions|
|sqlserver_process_memory_percent|SQL Server process memory percent|Percentagem|Máximo|This metric is a placeholder and not populated at this time.|No Dimensions|
|tempdb_data_size|Tempdb Data File Size Kilobytes|Contagem|Máximo|Tempdb Data File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_size|Tempdb Log File Size Kilobytes|Contagem|Máximo|Tempdb Log File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_used_percent|Tempdb Percent Log Used|Percentagem|Máximo|Tempdb Percent Log Used. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|workers_percent|Workers percentage|Percentagem|Média|Workers percentage|No Dimensions|
|xtp_storage_percent|In-Memory OLTP storage percent|Percentagem|Média|In-Memory OLTP storage percent|No Dimensions|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|avg_cpu_percent|Average CPU percentage|Percentagem|Média|Average CPU percentage|No Dimensions|
|io_bytes_read|IO bytes read|Bytes|Média|IO bytes read|No Dimensions|
|io_requests|IO requests count|Contagem|Média|IO requests count|No Dimensions|
|io_bytes_written|IO bytes written|Bytes|Média|IO bytes written|No Dimensions|
|reserved_storage_mb|Storage space reserved|Contagem|Média|Storage space reserved|No Dimensions|
|storage_space_used_mb|Storage space used|Contagem|Média|Storage space used|No Dimensions|
|virtual_core_count|Virtual core count|Contagem|Média|Virtual core count|No Dimensions|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|UsedCapacity|Used capacity|Bytes|Média|Account used capacity|No Dimensions|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Entrada|Entrada|Bytes|Total|The amount of ingress data, in bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Authentication|
|Saída|Saída|Bytes|Total|The amount of egress data, in bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Média|The average latency used by Azure Storage to process a successful request, in milliseconds. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Média|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Authentication|
|Disponibilidade|Disponibilidade|Percentagem|Média|The percentage of availability for the storage service or the specified API operation. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|BlobCapacity|Blob Capacity|Bytes|Média|The amount of storage used by the storage account’s Blob service in bytes.|BlobType, Tier|
|BlobCount|Blob Count|Contagem|Total|The number of Blob in the storage account’s Blob service.|BlobType|       |BlobCount|Blob Count|Contagem|Média|The number of Blob in the storage account’s Blob service.|BlobType, Tier|
|ContainerCount|Blob Container Count|Contagem|Média|The number of containers in the storage account’s Blob service.|No Dimensions|
|IndexCapacity|Index Capacity|Bytes|Média|The amount of storage used by ADLS Gen2 (Hierarchical) Index in bytes.|No Dimensions|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Entrada|Entrada|Bytes|Total|The amount of ingress data, in bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Authentication|
|Saída|Saída|Bytes|Total|The amount of egress data, in bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Média|The average latency used by Azure Storage to process a successful request, in milliseconds. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Média|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Authentication|
|Disponibilidade|Disponibilidade|Percentagem|Média|The percentage of availability for the storage service or the specified API operation. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|FileCapacity|File Capacity|Bytes|Média|The amount of storage used by the storage account’s File service in bytes.|No Dimensions|
|FileCount|File Count|Contagem|Média|The number of file in the storage account’s File service.|No Dimensions|
|FileShareCount|File Share Count|Contagem|Média|The number of file shares in the storage account’s File service.|No Dimensions|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Entrada|Entrada|Bytes|Total|The amount of ingress data, in bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Authentication|
|Saída|Saída|Bytes|Total|The amount of egress data, in bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Média|The average latency used by Azure Storage to process a successful request, in milliseconds. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Média|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Authentication|
|Disponibilidade|Disponibilidade|Percentagem|Média|The percentage of availability for the storage service or the specified API operation. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueueCapacity|Queue Capacity|Bytes|Média|The amount of storage used by the storage account’s Queue service in bytes.|No Dimensions|
|QueueCount|Queue Count|Contagem|Média|The number of queue in the storage account’s Queue service.|No Dimensions|
|QueueMessageCount|Queue Message Count|Contagem|Média|The approximate number of queue messages in the storage account’s Queue service.|No Dimensions|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Entrada|Entrada|Bytes|Total|The amount of ingress data, in bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Authentication|
|Saída|Saída|Bytes|Total|The amount of egress data, in bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Média|The average latency used by Azure Storage to process a successful request, in milliseconds. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Média|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Authentication|
|Disponibilidade|Disponibilidade|Percentagem|Média|The percentage of availability for the storage service or the specified API operation. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|TableCapacity|Table Capacity|Bytes|Média|The amount of storage used by the storage account’s Table service in bytes.|No Dimensions|
|TableCount|Table Count|Contagem|Média|The number of table in the storage account’s Table service.|No Dimensions|
|TableEntityCount|Table Entity Count|Contagem|Média|The number of table entities in the storage account’s Table service.|No Dimensions|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Entrada|Entrada|Bytes|Total|The amount of ingress data, in bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Authentication|
|Saída|Saída|Bytes|Total|The amount of egress data, in bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Média|The average latency used by Azure Storage to process a successful request, in milliseconds. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Média|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Authentication|
|Disponibilidade|Disponibilidade|Percentagem|Média|The percentage of availability for the storage service or the specified API operation. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Sync Session Result|Contagem|Média|Metric that logs a value of 1 each time the Server Endpoint successfully completes a Sync Session with the Cloud Endpoint|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Files Synced|Contagem|Total|Count of Files synced|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Files not syncing|Contagem|Total|Count of files failed to sync|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Bytes synced|Bytes|Total|Total file size transferred for Sync Sessions|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Server Online Status|Contagem|Máximo|Metric that logs a value of 1 each time the registered server successfully records a heartbeat with the Cloud Endpoint|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Cloud tiering recall|Bytes|Total|Total size of data recalled by the server|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|ResourceUtilization|SU % Utilization|Percentagem|Máximo|SU % Utilization|LogicalName, PartitionId|
|InputEvents|Input Events|Contagem|Total|Input Events|LogicalName, PartitionId|
|InputEventBytes|Input Event Bytes|Bytes|Total|Input Event Bytes|LogicalName, PartitionId|
|LateInputEvents|Late Input Events|Contagem|Total|Late Input Events|LogicalName, PartitionId|
|OutputEvents|Output Events|Contagem|Total|Output Events|LogicalName, PartitionId|
|ConversionErrors|Data Conversion Errors|Contagem|Total|Data Conversion Errors|LogicalName, PartitionId|
|Erros|Runtime Errors|Contagem|Total|Runtime Errors|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Out of order Events|Contagem|Total|Out of order Events|LogicalName, PartitionId|
|AMLCalloutRequests|Function Requests|Contagem|Total|Function Requests|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Failed Function Requests|Contagem|Total|Failed Function Requests|LogicalName, PartitionId|
|AMLCalloutInputEvents|Function Events|Contagem|Total|Function Events|LogicalName, PartitionId|
|DeserializationError|Input Deserialization Errors|Contagem|Total|Input Deserialization Errors|LogicalName, PartitionId|
|EarlyInputEvents|Early Input Events|Contagem|Total|Early Input Events|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Watermark Delay|Segundos|Máximo|Watermark Delay|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Backlogged Input Events|Contagem|Máximo|Backlogged Input Events|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Input Sources Received|Contagem|Total|Input Sources Received|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages|Contagem|Total|Count of messages read from all Event hub or IoT hub event sources|No Dimensions|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages|Contagem|Total|Count of invalid messages read from all Event hub or IoT hub event sources|No Dimensions|
|IngressReceivedBytes|Ingress Received Bytes|Bytes|Total|Count of bytes read from all event sources|No Dimensions|
|IngressStoredBytes|Ingress Stored Bytes|Bytes|Total|Total size of events successfully processed and available for query|No Dimensions|
|IngressStoredEvents|Ingress Stored Events|Contagem|Total|Count of flattened events successfully processed and available for query|No Dimensions|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag|Segundos|Máximo|Difference between the time that the message is enqueued in the event source and the time it is processed in Ingress|No Dimensions|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag|Contagem|Média|Difference between the sequence number of last enqueued message in the event source partition and sequence number of message being processed in Ingress|No Dimensions|
|WarmStorageMaxProperties|Warm Storage Max Properties|Contagem|Máximo|Maximum number of properties used allowed by the environment for S1/S2 SKU and maximum number of properties allowed by Warm Store for PAYG SKU|No Dimensions|
|WarmStorageUsedProperties|Warm Storage Used Properties |Contagem|Máximo|Number of properties used by the environment for S1/S2 SKU and number of properties used by Warm Store for PAYG SKU|No Dimensions|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages|Contagem|Total|Count of messages read from the event source|No Dimensions|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages|Contagem|Total|Count of invalid messages read from the event source|No Dimensions|
|IngressReceivedBytes|Ingress Received Bytes|Bytes|Total|Count of bytes read from the event source|No Dimensions|
|IngressStoredBytes|Ingress Stored Bytes|Bytes|Total|Total size of events successfully processed and available for query|No Dimensions|
|IngressStoredEvents|Ingress Stored Events|Contagem|Total|Count of flattened events successfully processed and available for query|No Dimensions|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag|Segundos|Máximo|Difference between the time that the message is enqueued in the event source and the time it is processed in Ingress|No Dimensions|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag|Contagem|Média|Difference between the sequence number of last enqueued message in the event source partition and sequence number of message being processed in Ingress|No Dimensions|
|WarmStorageMaxProperties|Warm Storage Max Properties|Contagem|Máximo|Maximum number of properties used allowed by the environment for S1/S2 SKU and maximum number of properties allowed by Warm Store for PAYG SKU|No Dimensions|
|WarmStorageUsedProperties|Warm Storage Used Properties |Contagem|Máximo|Number of properties used by the environment for S1/S2 SKU and number of properties used by Warm Store for PAYG SKU|No Dimensions|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|BytesPerSecond|Média|Average disk throughput due to read operations over the sample period.|No Dimensions|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|BytesPerSecond|Média|Average disk throughput due to write operations over the sample period.|No Dimensions|
|Disk Read Bytes|Disk Read Bytes|Bytes|Total|Total disk throughput due to read operations over the sample period.|No Dimensions|
|Disk Write Bytes|Disk Write Bytes|Bytes|Total|Total disk throughput due to write operations over the sample period.|No Dimensions|
|DiskReadOperations|Disk Read Operations|Contagem|Total|The number of IO read operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|DiskWriteOperations|Disk Write Operations|Contagem|Total|The number of IO write operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Média|The average number of IO read operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Média|The average number of IO write operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|DiskReadLatency|Disk Read Latency|Milliseconds|Média|Total read latency. The sum of the device and kernel read latencies.|No Dimensions|
|DiskWriteLatency|Disk Write Latency|Milliseconds|Média|Total write latency. The sum of the device and kernel write latencies.|No Dimensions|
|NetworkInBytesPerSecond|Network In Bytes/Sec|BytesPerSecond|Média|Average network throughput for received traffic.|No Dimensions|
|NetworkOutBytesPerSecond|Network Out Bytes/Sec|BytesPerSecond|Média|Average network throughput for transmitted traffic.|No Dimensions|
|Entrada na Rede|Entrada na Rede|Bytes|Total|Total network throughput for received traffic.|No Dimensions|
|Saída da Rede|Saída da Rede|Bytes|Total|Total network throughput for transmitted traffic.|No Dimensions|
|MemoryUsed|Memory Used|Bytes|Média|The amount of machine memory that is in use by the VM.|No Dimensions|
|MemoryGranted|Memory Granted|Bytes|Média|The amount of memory that was granted to the VM by the host. Memory is not granted to the host until it is touched one time and granted memory may be swapped out or ballooned away if the VMkernel needs the memory.|No Dimensions|
|MemoryActive|Memory Active|Bytes|Média|The amount of memory used by the VM in the past small window of time. This is the "true" number of how much memory the VM currently has need of. Additional, unused memory may be swapped out or ballooned with no impact to the guest's performance.|No Dimensions|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|The CPU utilization. This value is reported with 100% representing all processor cores on the system. As an example, a 2-way VM using 50% of a four-core system is completely using two cores.|No Dimensions|
|PercentageCpuReady|Percentage CPU Ready|Milliseconds|Total|Ready time is the time spend waiting for CPU(s) to become available in the past update interval.|No Dimensions|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuPercentage|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Instância|
|MemoryPercentage|Memory Percentage|Percentagem|Média|Memory Percentage|Instância|
|DiskQueueLength|Disk Queue Length|Contagem|Média|Disk Queue Length|Instância|
|HttpQueueLength|Http Queue Length|Contagem|Média|Http Queue Length|Instância|
|BytesReceived|Data In|Bytes|Total|Data In|Instância|
|BytesSent|Data Out|Bytes|Total|Data Out|Instância|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (excluding functions)

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuTime|CPU Time|Segundos|Total|CPU Time|Instância|
|Pedidos|Pedidos|Contagem|Total|Pedidos|Instância|
|BytesReceived|Data In|Bytes|Total|Data In|Instância|
|BytesSent|Data Out|Bytes|Total|Data Out|Instância|
|Http101|Http 101|Contagem|Total|Http 101|Instância|
|Http2xx|Http 2xx|Contagem|Total|Http 2xx|Instância|
|Http3xx|Http 3xx|Contagem|Total|Http 3xx|Instância|
|Http401|Http 401|Contagem|Total|Http 401|Instância|
|Http403|Http 403|Contagem|Total|Http 403|Instância|
|Http404|Http 404|Contagem|Total|Http 404|Instância|
|Http406|Http 406|Contagem|Total|Http 406|Instância|
|Http4xx|Http 4xx|Contagem|Total|Http 4xx|Instância|
|Http5xx|Http Server Errors|Contagem|Total|Http Server Errors|Instância|
|MemoryWorkingSet|Memory working set|Bytes|Média|Memory working set|Instância|
|AverageMemoryWorkingSet|Average memory working set|Bytes|Média|Average memory working set|Instância|
|AverageResponseTime|Average Response Time|Segundos|Média|Average Response Time|Instância|
|AppConnections|Ligações|Contagem|Média|Ligações|Instância|
|Handles|Handle Count|Contagem|Média|Handle Count|Instância|
|Threads|Thread Count|Contagem|Média|Thread Count|Instância|
|PrivateBytes|Private Bytes|Bytes|Média|Private Bytes|Instância|
|IoReadBytesPerSecond|IO Read Bytes Per Second|BytesPerSecond|Total|IO Read Bytes Per Second|Instância|
|IoWriteBytesPerSecond|IO Write Bytes Per Second|BytesPerSecond|Total|IO Write Bytes Per Second|Instância|
|IoOtherBytesPerSecond|IO Other Bytes Per Second|BytesPerSecond|Total|IO Other Bytes Per Second|Instância|
|IoReadOperationsPerSecond|IO Read Operations Per Second|BytesPerSecond|Total|IO Read Operations Per Second|Instância|
|IoWriteOperationsPerSecond|IO Write Operations Per Second|BytesPerSecond|Total|IO Write Operations Per Second|Instância|
|IoOtherOperationsPerSecond|IO Other Operations Per Second|BytesPerSecond|Total|IO Other Operations Per Second|Instância|
|RequestsInApplicationQueue|Requests In Application Queue|Contagem|Média|Requests In Application Queue|Instância|
|CurrentAssemblies|Current Assemblies|Contagem|Média|Current Assemblies|Instância|
|TotalAppDomains|Total App Domains|Contagem|Média|Total App Domains|Instância|
|TotalAppDomainsUnloaded|Total App Domains Unloaded|Contagem|Média|Total App Domains Unloaded|Instância|
|Gen0Collections|Gen 0 Garbage Collections|Contagem|Total|Gen 0 Garbage Collections|Instância|
|Gen1Collections|Gen 1 Garbage Collections|Contagem|Total|Gen 1 Garbage Collections|Instância|
|Gen2Collections|Gen 2 Garbage Collections|Contagem|Total|Gen 2 Garbage Collections|Instância|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (functions)

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|BytesReceived|Data In|Bytes|Total|Data In|Instância|
|BytesSent|Data Out|Bytes|Total|Data Out|Instância|
|Http5xx|Http Server Errors|Contagem|Total|Http Server Errors|Instância|
|MemoryWorkingSet|Memory working set|Bytes|Média|Memory working set|Instância|
|AverageMemoryWorkingSet|Average memory working set|Bytes|Média|Average memory working set|Instância|
|FunctionExecutionUnits|Function Execution Units|MB / Milliseconds|Total|[Function Execution Units](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instância|
|FunctionExecutionCount|Function Execution Count|Contagem|Total|Function Execution Count|Instância|
|PrivateBytes|Private Bytes|Bytes|Média|Private Bytes|Instância|
|IoReadBytesPerSecond|IO Read Bytes Per Second|BytesPerSecond|Total|IO Read Bytes Per Second|Instância|
|IoWriteBytesPerSecond|IO Write Bytes Per Second|BytesPerSecond|Total|IO Write Bytes Per Second|Instância|
|IoOtherBytesPerSecond|IO Other Bytes Per Second|BytesPerSecond|Total|IO Other Bytes Per Second|Instância|
|IoReadOperationsPerSecond|IO Read Operations Per Second|BytesPerSecond|Total|IO Read Operations Per Second|Instância|
|IoWriteOperationsPerSecond|IO Write Operations Per Second|BytesPerSecond|Total|IO Write Operations Per Second|Instância|
|IoOtherOperationsPerSecond|IO Other Operations Per Second|BytesPerSecond|Total|IO Other Operations Per Second|Instância|
|RequestsInApplicationQueue|Requests In Application Queue|Contagem|Média|Requests In Application Queue|Instância|
|CurrentAssemblies|Current Assemblies|Contagem|Média|Current Assemblies|Instância|
|TotalAppDomains|Total App Domains|Contagem|Média|Total App Domains|Instância|
|TotalAppDomainsUnloaded|Total App Domains Unloaded|Contagem|Média|Total App Domains Unloaded|Instância|
|Gen0Collections|Gen 0 Garbage Collections|Contagem|Total|Gen 0 Garbage Collections|Instância|
|Gen1Collections|Gen 1 Garbage Collections|Contagem|Total|Gen 1 Garbage Collections|Instância|
|Gen2Collections|Gen 2 Garbage Collections|Contagem|Total|Gen 2 Garbage Collections|Instância|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuTime|CPU Time|Segundos|Total|CPU Time|Instância|
|Pedidos|Pedidos|Contagem|Total|Pedidos|Instância|
|BytesReceived|Data In|Bytes|Total|Data In|Instância|
|BytesSent|Data Out|Bytes|Total|Data Out|Instância|
|Http101|Http 101|Contagem|Total|Http 101|Instância|
|Http2xx|Http 2xx|Contagem|Total|Http 2xx|Instância|
|Http3xx|Http 3xx|Contagem|Total|Http 3xx|Instância|
|Http401|Http 401|Contagem|Total|Http 401|Instância|
|Http403|Http 403|Contagem|Total|Http 403|Instância|
|Http404|Http 404|Contagem|Total|Http 404|Instância|
|Http406|Http 406|Contagem|Total|Http 406|Instância|
|Http4xx|Http 4xx|Contagem|Total|Http 4xx|Instância|
|Http5xx|Http Server Errors|Contagem|Total|Http Server Errors|Instância|
|MemoryWorkingSet|Memory working set|Bytes|Média|Memory working set|Instância|
|AverageMemoryWorkingSet|Average memory working set|Bytes|Média|Average memory working set|Instância|
|AverageResponseTime|Average Response Time|Segundos|Média|Average Response Time|Instância|
|FunctionExecutionUnits|Function Execution Units|Contagem|Total|Function Execution Units|Instância|
|FunctionExecutionCount|Function Execution Count|Contagem|Total|Function Execution Count|Instância|
|AppConnections|Ligações|Contagem|Média|Ligações|Instância|
|Handles|Handle Count|Contagem|Média|Handle Count|Instância|
|Threads|Thread Count|Contagem|Média|Thread Count|Instância|
|PrivateBytes|Private Bytes|Bytes|Média|Private Bytes|Instância|
|IoReadBytesPerSecond|IO Read Bytes Per Second|BytesPerSecond|Total|IO Read Bytes Per Second|Instância|
|IoWriteBytesPerSecond|IO Write Bytes Per Second|BytesPerSecond|Total|IO Write Bytes Per Second|Instância|
|IoOtherBytesPerSecond|IO Other Bytes Per Second|BytesPerSecond|Total|IO Other Bytes Per Second|Instância|
|IoReadOperationsPerSecond|IO Read Operations Per Second|BytesPerSecond|Total|IO Read Operations Per Second|Instância|
|IoWriteOperationsPerSecond|IO Write Operations Per Second|BytesPerSecond|Total|IO Write Operations Per Second|Instância|
|IoOtherOperationsPerSecond|IO Other Operations Per Second|BytesPerSecond|Total|IO Other Operations Per Second|Instância|
|RequestsInApplicationQueue|Requests In Application Queue|Contagem|Média|Requests In Application Queue|Instância|
|CurrentAssemblies|Current Assemblies|Contagem|Média|Current Assemblies|Instância|
|TotalAppDomains|Total App Domains|Contagem|Média|Total App Domains|Instância|
|TotalAppDomainsUnloaded|Total App Domains Unloaded|Contagem|Média|Total App Domains Unloaded|Instância|
|Gen0Collections|Gen 0 Garbage Collections|Contagem|Total|Gen 0 Garbage Collections|Instância|
|Gen1Collections|Gen 1 Garbage Collections|Contagem|Total|Gen 1 Garbage Collections|Instância|
|Gen2Collections|Gen 2 Garbage Collections|Contagem|Total|Gen 2 Garbage Collections|Instância|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|Pedidos|Pedidos|Contagem|Total|Pedidos|Instância|
|BytesReceived|Data In|Bytes|Total|Data In|Instância|
|BytesSent|Data Out|Bytes|Total|Data Out|Instância|
|Http101|Http 101|Contagem|Total|Http 101|Instância|
|Http2xx|Http 2xx|Contagem|Total|Http 2xx|Instância|
|Http3xx|Http 3xx|Contagem|Total|Http 3xx|Instância|
|Http401|Http 401|Contagem|Total|Http 401|Instância|
|Http403|Http 403|Contagem|Total|Http 403|Instância|
|Http404|Http 404|Contagem|Total|Http 404|Instância|
|Http406|Http 406|Contagem|Total|Http 406|Instância|
|Http4xx|Http 4xx|Contagem|Total|Http 4xx|Instância|
|Http5xx|Http Server Errors|Contagem|Total|Http Server Errors|Instância|
|AverageResponseTime|Average Response Time|Segundos|Média|Average Response Time|Instância|
|CpuPercentage|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Instância|
|MemoryPercentage|Memory Percentage|Percentagem|Média|Memory Percentage|Instância|
|DiskQueueLength|Disk Queue Length|Contagem|Média|Disk Queue Length|Instância|
|HttpQueueLength|Http Queue Length|Contagem|Média|Http Queue Length|Instância|
|ActiveRequests|Active Requests|Contagem|Total|Active Requests|Instância|
|TotalFrontEnds|Total Front Ends|Contagem|Média|Total Front Ends|No Dimensions|
|SmallAppServicePlanInstances|Small App Service Plan Workers|Contagem|Média|Small App Service Plan Workers|No Dimensions|
|MediumAppServicePlanInstances|Medium App Service Plan Workers|Contagem|Média|Medium App Service Plan Workers|No Dimensions|
|LargeAppServicePlanInstances|Large App Service Plan Workers|Contagem|Média|Large App Service Plan Workers|No Dimensions|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Métrica|Metric Display Name|Unidade|Aggregation Type|Descrição|Dimensões|
|---|---|---|---|---|---|
|WorkersTotal|Total Workers|Contagem|Média|Total Workers|No Dimensions|
|WorkersAvailable|Available Workers|Contagem|Média|Available Workers|No Dimensions|
|WorkersUsed|Used Workers|Contagem|Média|Used Workers|No Dimensions|
|CpuPercentage|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Instância|
|MemoryPercentage|Memory Percentage|Percentagem|Média|Memory Percentage|Instância|

## <a name="next-steps"></a>Passos seguintes
* [Read about metrics in Azure Monitor](data-platform.md)
* [Create alerts on metrics](alerts-overview.md)
* [Export metrics to storage, Event Hub, or Log Analytics](resource-logs-overview.md)
