---
title: Azure Monitor métricas com suporte por tipo de recurso
description: Lista de métricas disponíveis para cada tipo de recurso com Azure Monitor.
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
# <a name="supported-metrics-with-azure-monitor"></a>Métricas com suporte com Azure Monitor

O Azure Monitor fornece várias maneiras de interagir com as métricas, incluindo o gráfico delas no portal, acessá-las por meio da API REST ou consultá-las usando o PowerShell ou a CLI. Abaixo está uma lista completa de todas as métricas disponíveis atualmente com o pipeline de métrica do Azure Monitor. Outras métricas podem estar disponíveis no portal ou usando APIs herdadas. Esta lista abaixo inclui apenas as métricas disponíveis usando o pipeline de métrica de Azure Monitor consolidado. Para consultar e acessar essas métricas, use a [versão de api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Atualmente, o envio de métricas multidimensionais através das definições de diagnóstico não é suportado. As métricas com dimensões são exportadas como métricas dimensionais simples e agregadas em valores de dimensões.
>
> *Por exemplo*: a métrica “Mensagens Recebidas” num hub do Hub de Eventos pode ser explorada e representada ao nível da linha. No entanto, se for exportada através das definições de diagnóstico, a métrica será representada como todas as mensagens recebidas em todas as filas do hub do Hub de Eventos.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|qpu_metric|QPU|Contagem|Média|QPU. Intervalo de 0-100 para S1, 0-200 para S2 e 0-400 para S4|ServerResourceType|
|memory_metric|Memória|Bytes|Média|Memória. Intervalo de 0-25 GB para S1, 0-50 GB para S2 e 0-100 GB para S4|ServerResourceType|
|private_bytes_metric|Bytes particulares |Bytes|Média|A quantidade total de memória que o processo do mecanismo de Analysis Services e os processos de contêiner do mashup alocaram, não incluindo a memória compartilhada com outros processos.|ServerResourceType|
|virtual_bytes_metric|Bytes virtuais |Bytes|Média|O tamanho atual do espaço de endereço virtual que Analysis Services processo do mecanismo e os processos de contêiner do mashup estão usando.|ServerResourceType|
|TotalConnectionRequests|Total de solicitações de conexão|Contagem|Média|Total de solicitações de conexão. Essas são entradas.|ServerResourceType|
|SuccessfullConnectionsPerSec|Conexões com êxito por segundo|CountPerSecond|Média|Taxa de conclusões de conexão com êxito.|ServerResourceType|
|TotalConnectionFailures|Total de falhas de conexão|Contagem|Média|Total de tentativas de conexão com falha.|ServerResourceType|
|CurrentUserSessions|Sessões de usuário atuais|Contagem|Média|Número atual de sessões de usuário estabelecidas.|ServerResourceType|
|QueryPoolBusyThreads|Threads ocupados do pool de consulta|Contagem|Média|Número de threads ocupados no pool de threads de consulta.|ServerResourceType|
|CommandPoolJobQueueLength|Comprimento da fila de trabalhos do pool de comandos|Contagem|Média|Número de trabalhos na fila do pool de threads de comando.|ServerResourceType|
|ProcessingPoolJobQueueLength|Tamanho da fila de trabalhos do pool de processamento|Contagem|Média|Número de trabalhos que não são de e/s na fila do pool de threads de processamento.|ServerResourceType|
|CurrentConnections|Conexão: conexões atuais|Contagem|Média|Número atual de conexões de cliente estabelecidas.|ServerResourceType|
|CleanerCurrentPrice|Memória: preço atual do limpador|Contagem|Média|Preço atual da memória, $/byte/time, normalizado para 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Memória: memória de limpeza recolhida|Bytes|Média|Quantidade de memória, em bytes, sujeita a limpeza pelo limpador de segundo plano.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memória: memória de limpeza não reduzida|Bytes|Média|Quantidade de memória, em bytes, não sujeita a limpeza pelo limpador de segundo plano.|ServerResourceType|
|MemoryUsage|Memória: uso de memória|Bytes|Média|Uso de memória do processo do servidor conforme usado no cálculo do preço de memória de limpeza. Igual ao contador Process\PrivateBytes mais o tamanho dos dados mapeados na memória, ignorando qualquer memória que foi mapeada ou alocada pelo VertiPaq (mecanismo analítico na memória) xVelocity excedendo o limite de memória do mecanismo xVelocity.|ServerResourceType|
|MemoryLimitHard|Memória: limite de memória rígido|Bytes|Média|Limite de memória rígido, do arquivo de configuração.|ServerResourceType|
|MemoryLimitHigh|Memória: limite de memória alto|Bytes|Média|Limite de memória alto, do arquivo de configuração.|ServerResourceType|
|MemoryLimitLow|Memória: limite de memória baixo|Bytes|Média|Limite de memória baixo, do arquivo de configuração.|ServerResourceType|
|MemoryLimitVertiPaq|Memória: limite de memória VertiPaq|Bytes|Média|Limite na memória, do arquivo de configuração.|ServerResourceType|
|Quota|Memória: cota|Bytes|Média|Cota de memória atual, em bytes. A cota de memória também é conhecida como concessão de memória ou reserva de memória.|ServerResourceType|
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
|QueryPoolJobQueueLength|Threads: comprimento da fila de trabalhos do pool de consultas|Contagem|Média|Número de trabalhos na fila do pool de threads de consulta.|ServerResourceType|
|ShortParsingBusyThreads|Threads: threads ocupados da análise resumida|Contagem|Média|Número de threads ocupados no pool de threads da análise resumida.|ServerResourceType|
|ShortParsingIdleThreads|Threads: threads ociosos da análise resumida|Contagem|Média|Número de threads ociosos no pool de threads da análise resumida.|ServerResourceType|
|ShortParsingJobQueueLength|Threads: comprimento da fila de trabalhos da análise curta|Contagem|Média|Número de trabalhos na fila do pool de threads da análise resumida.|ServerResourceType|
|memory_thrashing_metric|Ultrapaginação de memória|Percentagem|Média|Média de ultrapaginação de memória.|ServerResourceType|
|mashup_engine_qpu_metric|QPU do mecanismo M|Contagem|Média|Uso de QPU por processos do mecanismo mashup|ServerResourceType|
|mashup_engine_memory_metric|Memória do mecanismo M|Bytes|Média|Uso de memória pelos processos do mecanismo mashup|ServerResourceType|
|mashup_engine_private_bytes_metric|Bytes privados do mecanismo M |Bytes|Média|A quantidade total de processos de contêiner de mashup de memória alocados, não incluindo a memória compartilhada com outros processos.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Bytes virtuais do mecanismo M |Bytes|Média|O tamanho atual dos processos de contêiner do mashup de espaço de endereço virtual está usando.|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Pedidos|Pedidos|Contagem|Total|O número total de solicitações de gateway em um determinado período. Ele pode ser dividido por várias dimensões para ajudá-lo a diagnosticar problemas. |Local, BackendResponseCode, LastErrorReason, GatewayResponseCode|
|TotalRequests|Total de solicitações do gateway|Contagem|Total|O número total de solicitações de gateway em um determinado período. Essa métrica foi preterida, é recomendável usar a nova métrica de `Requests`. |Localização, nome do host|
|SuccessfulRequests|Solicitações de gateway bem-sucedidas|Contagem|Total|O número total de solicitações de gateway bem-sucedidas em um determinado período. Essa métrica foi preterida, é recomendável usar a nova métrica de `Requests`.|Localização, nome do host|
|UnauthorizedRequests|Solicitações de gateway não autorizadas|Contagem|Total| O número total de solicitações de gateway não autorizadas em um determinado período. Essa métrica foi preterida, é recomendável usar a nova métrica de `Requests`.|Localização, nome do host|
|FailedRequests|Solicitações de gateway com falha|Contagem|Total|O número total de solicitações de gateway com falha em um determinado período. Essa métrica foi preterida, é recomendável usar a nova métrica de `Requests`.|Localização, nome do host|
|OtherRequests|Outras solicitações de gateway|Contagem|Total|O número total de solicitações de gateway em um determinado período que não se enquadram nas categorias bem-sucedidas, não autorizadas ou com falha. Essa métrica foi preterida, é recomendável usar a nova métrica de `Requests`. |Localização, nome do host|
|Duração|Duração geral das solicitações de gateway|Milissegundos|Média|O tempo entre o gerenciamento de API recebe uma solicitação de um cliente e quando ele retorna uma resposta ao cliente.|Localização, nome do host|
|Capacidade|Capacidade|Percentagem|Média|Indicador de carga em uma instância de gerenciamento de API para tomar decisões informadas sobre a possibilidade de dimensionar a instância para acomodar mais carga.|Localização|
|EventHubTotalEvents|Total de eventos do EventHub|Contagem|Total|O número total de eventos enviados ao EventHub do gerenciamento de API em um determinado período.|Localização|
|EventHubSuccessfulEvents|Eventos de EventHub bem-sucedidos|Contagem|Total|O número total de eventos de EventHub bem-sucedidos em um determinado período.|Localização|
|EventHubTotalFailedEvents|Eventos de EventHub com falha|Contagem|Total|O número total de eventos de EventHub com falha em um determinado período.|Localização|
|EventHubRejectedEvents|Eventos de EventHub rejeitados|Contagem|Total|O número total de eventos do EventHub rejeitados (configuração incorreta ou não autorizado) em um determinado período.|Localização|
|EventHubThrottledEvents|Eventos de EventHub limitados|Contagem|Total|O número total de eventos de EventHub limitado em um determinado período.|Localização|
|EventHubTimedoutEvents|Eventos do EventHub com tempo limite excedido|Contagem|Total|O número total de eventos de EventHub expirados em um determinado período.|Localização|
|EventHubDroppedEvents|Eventos do EventHub ignorados|Contagem|Total|O número total de eventos ignorados devido ao limite de tamanho de fila atingido em um determinado período.|Localização|
|EventHubTotalBytesSent|Tamanho dos eventos do EventHub|Bytes|Total|O tamanho total dos eventos do EventHub em bytes em um determinado período.|Localização|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalJob|Total de trabalhos|Contagem|Total|O número total de trabalhos|Runbook, status|
|TotalUpdateDeploymentRuns|Total de execuções de implantação de atualização|Contagem|Total|Total de execuções de implantação de atualização de software|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|Total de execuções do computador de implantação de atualização|Contagem|Total|O computador de implantação de atualização de software total é executado em uma execução de implantação de atualização de software|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CoreCount|Contagem de núcleos dedicados|Contagem|Total|Número total de núcleos dedicados na conta do lote|Nenhuma dimensão|
|TotalNodeCount|Contagem de nós dedicados|Contagem|Total|Número total de nós dedicados na conta do lote|Nenhuma dimensão|
|LowPriorityCoreCount|Contagem de núcleos de LowPriority|Contagem|Total|Número total de núcleos de baixa prioridade na conta do lote|Nenhuma dimensão|
|TotalLowPriorityNodeCount|Contagem de nós de baixa prioridade|Contagem|Total|Número total de nós de baixa prioridade na conta do lote|Nenhuma dimensão|
|CreatingNodeCount|Criando contagem de nós|Contagem|Total|Número de nós sendo criados|Nenhuma dimensão|
|StartingNodeCount|Contagem de nós inicial|Contagem|Total|Número de nós iniciando|Nenhuma dimensão|
|WaitingForStartTaskNodeCount|Aguardando a contagem de nó de tarefa inicial|Contagem|Total|Número de nós aguardando a conclusão da tarefa inicial|Nenhuma dimensão|
|StartTaskFailedNodeCount|Falha na contagem de nós da tarefa inicial|Contagem|Total|Número de nós em que a tarefa inicial falhou|Nenhuma dimensão|
|IdleNodeCount|Contagem de nós ociosos|Contagem|Total|Número de nós ociosos|Nenhuma dimensão|
|OfflineNodeCount|Contagem de nós offline|Contagem|Total|Número de nós offline|Nenhuma dimensão|
|RebootingNodeCount|Contagem de nós de reinicialização|Contagem|Total|Número de nós de reinicialização|Nenhuma dimensão|
|ReimagingNodeCount|Contagem de nós de reimagem|Contagem|Total|Número de nós de reimagem|Nenhuma dimensão|
|RunningNodeCount|Contagem de nós em execução|Contagem|Total|Número de nós em execução|Nenhuma dimensão|
|LeavingPoolNodeCount|Saindo da contagem de nós do pool|Contagem|Total|Número de nós saindo do pool|Nenhuma dimensão|
|UnusableNodeCount|Contagem de nós inutilizáveis|Contagem|Total|Número de nós inutilizáveis|Nenhuma dimensão|
|PreemptedNodeCount|Contagem de nós preempção|Contagem|Total|Número de nós admitidos|Nenhuma dimensão|
|TaskStartEvent|Eventos de início de tarefa|Contagem|Total|Número total de tarefas que foram iniciadas|Nenhuma dimensão|
|TaskCompleteEvent|Eventos de conclusão de tarefa|Contagem|Total|Número total de tarefas que foram concluídas|Nenhuma dimensão|
|TaskFailEvent|Eventos de falha de tarefa|Contagem|Total|Número total de tarefas que foram concluídas em um estado de falha|Nenhuma dimensão|
|PoolCreateEvent|Eventos de criação de pool|Contagem|Total|Número total de pools que foram criados|Nenhuma dimensão|
|PoolResizeStartEvent|Eventos de início de redimensionamento do pool|Contagem|Total|Número total de redimensionamentos de pool que foram iniciados|Nenhuma dimensão|
|PoolResizeCompleteEvent|Eventos completos de redimensionamento de pool|Contagem|Total|Número total de redimensionamentos de pool que foram concluídos|Nenhuma dimensão|
|PoolDeleteStartEvent|Eventos de início de exclusão de pool|Contagem|Total|Número total de exclusões de pool que iniciaram|Nenhuma dimensão|
|PoolDeleteCompleteEvent|Eventos completos de exclusão de pool|Contagem|Total|Número total de exclusões de pool que foram concluídas|Nenhuma dimensão|
|JobDeleteCompleteEvent|Eventos completos de exclusão de trabalho|Contagem|Total|Número total de trabalhos que foram excluídos com êxito.|Nenhuma dimensão|
|JobDeleteStartEvent|Eventos de início de exclusão de trabalho|Contagem|Total|Número total de trabalhos que foram solicitados a serem excluídos.|Nenhuma dimensão|
|JobDisableCompleteEvent|Trabalho de desabilitar eventos concluídos|Contagem|Total|Número total de trabalhos que foram desabilitados com êxito.|Nenhuma dimensão|
|JobDisableStartEvent|Eventos de início de desabilitação do trabalho|Contagem|Total|Número total de trabalhos que foram solicitados para serem desabilitados.|Nenhuma dimensão|
|JobStartEvent|Eventos de início do trabalho|Contagem|Total|Número total de trabalhos iniciados com êxito.|Nenhuma dimensão|
|JobTerminateCompleteEvent|Eventos de término de trabalho concluídos|Contagem|Total|Número total de trabalhos que foram encerrados com êxito.|Nenhuma dimensão|
|JobTerminateStartEvent|Eventos de início de término do trabalho|Contagem|Total|Número total de trabalhos que foram solicitados a serem encerrados.|Nenhuma dimensão|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|connectedclients|Clientes conectados|Contagem|Máximo||ShardId|
|totalcommandsprocessed|Total de operações|Contagem|Total||ShardId|
|cachehits|Acertos do cache|Contagem|Total||ShardId|
|cachemisses|Erros de cache|Contagem|Total||ShardId|
|comandos|Visível|Contagem|Total||ShardId|
|comandos|Defina|Contagem|Total||ShardId|
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
|cacheLatency|Microssegundos de latência de cache (versão prévia)|Contagem|Média||Fragmentid, Amostratype|
|erros|Erros|Contagem|Máximo||ShardId, ErrorType|
|connectedclients0|Clientes conectados (fragmento 0)|Contagem|Máximo||Nenhuma dimensão|
|totalcommandsprocessed0|Total de operações (fragmento 0)|Contagem|Total||Nenhuma dimensão|
|cachehits0|Acertos do cache (fragmento 0)|Contagem|Total||Nenhuma dimensão|
|cachemisses0|Erros de cache (fragmento 0)|Contagem|Total||Nenhuma dimensão|
|getcommands0|Gets (fragmento 0)|Contagem|Total||Nenhuma dimensão|
|setcommands0|Sets (fragmento 0)|Contagem|Total||Nenhuma dimensão|
|operationsPerSecond0|Operações por segundo (fragmento 0)|Contagem|Máximo||Nenhuma dimensão|
|evictedkeys0|Chaves removidas (fragmento 0)|Contagem|Total||Nenhuma dimensão|
|totalkeys0|Total de chaves (fragmento 0)|Contagem|Máximo||Nenhuma dimensão|
|expiredkeys0|Chaves expiradas (fragmento 0)|Contagem|Total||Nenhuma dimensão|
|usedmemory0|Memória usada (fragmento 0)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss0|Memória RSS usada (fragmento 0)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad0|Carga do servidor (fragmento 0)|Percentagem|Máximo||Nenhuma dimensão|
|cacheWrite0|Gravação no cache (fragmento 0)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead0|Leitura de cache (fragmento 0)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime0|CPU (fragmento 0)|Percentagem|Máximo||Nenhuma dimensão|
|connectedclients1|Clientes conectados (fragmento 1)|Contagem|Máximo||Nenhuma dimensão|
|totalcommandsprocessed1|Total de operações (fragmento 1)|Contagem|Total||Nenhuma dimensão|
|cachehits1|Acertos do cache (fragmento 1)|Contagem|Total||Nenhuma dimensão|
|cachemisses1|Erros de cache (fragmento 1)|Contagem|Total||Nenhuma dimensão|
|getcommands1|Gets (fragmento 1)|Contagem|Total||Nenhuma dimensão|
|setcommands1|Sets (fragmento 1)|Contagem|Total||Nenhuma dimensão|
|operationsPerSecond1|Operações por segundo (fragmento 1)|Contagem|Máximo||Nenhuma dimensão|
|evictedkeys1|Chaves removidas (fragmento 1)|Contagem|Total||Nenhuma dimensão|
|totalkeys1|Total de chaves (fragmento 1)|Contagem|Máximo||Nenhuma dimensão|
|expiredkeys1|Chaves expiradas (fragmento 1)|Contagem|Total||Nenhuma dimensão|
|usedmemory1|Memória usada (fragmento 1)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss1|Memória RSS usada (fragmento 1)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad1|Carga do servidor (fragmento 1)|Percentagem|Máximo||Nenhuma dimensão|
|cacheWrite1|Gravação no cache (fragmento 1)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead1|Leitura do cache (fragmento 1)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime1|CPU (fragmento 1)|Percentagem|Máximo||Nenhuma dimensão|
|connectedclients2|Clientes conectados (fragmento 2)|Contagem|Máximo||Nenhuma dimensão|
|totalcommandsprocessed2|Total de operações (fragmento 2)|Contagem|Total||Nenhuma dimensão|
|cachehits2|Acertos do cache (fragmento 2)|Contagem|Total||Nenhuma dimensão|
|cachemisses2|Erros de cache (fragmento 2)|Contagem|Total||Nenhuma dimensão|
|getcommands2|Gets (fragmento 2)|Contagem|Total||Nenhuma dimensão|
|setcommands2|Sets (fragmento 2)|Contagem|Total||Nenhuma dimensão|
|operationsPerSecond2|Operações por segundo (fragmento 2)|Contagem|Máximo||Nenhuma dimensão|
|evictedkeys2|Chaves removidas (fragmento 2)|Contagem|Total||Nenhuma dimensão|
|totalkeys2|Total de chaves (fragmento 2)|Contagem|Máximo||Nenhuma dimensão|
|expiredkeys2|Chaves expiradas (fragmento 2)|Contagem|Total||Nenhuma dimensão|
|usedmemory2|Memória usada (fragmento 2)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss2|Memória RSS usada (fragmento 2)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad2|Carga do servidor (fragmento 2)|Percentagem|Máximo||Nenhuma dimensão|
|cacheWrite2|Gravação no cache (fragmento 2)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead2|Leitura de cache (fragmento 2)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime2|CPU (fragmento 2)|Percentagem|Máximo||Nenhuma dimensão|
|connectedclients3|Clientes conectados (fragmento 3)|Contagem|Máximo||Nenhuma dimensão|
|totalcommandsprocessed3|Total de operações (fragmento 3)|Contagem|Total||Nenhuma dimensão|
|cachehits3|Acertos do cache (fragmento 3)|Contagem|Total||Nenhuma dimensão|
|cachemisses3|Erros de cache (fragmento 3)|Contagem|Total||Nenhuma dimensão|
|getcommands3|Gets (fragmento 3)|Contagem|Total||Nenhuma dimensão|
|setcommands3|Sets (fragmento 3)|Contagem|Total||Nenhuma dimensão|
|operationsPerSecond3|Operações por segundo (fragmento 3)|Contagem|Máximo||Nenhuma dimensão|
|evictedkeys3|Chaves removidas (fragmento 3)|Contagem|Total||Nenhuma dimensão|
|totalkeys3|Total de chaves (fragmento 3)|Contagem|Máximo||Nenhuma dimensão|
|expiredkeys3|Chaves expiradas (fragmento 3)|Contagem|Total||Nenhuma dimensão|
|usedmemory3|Memória usada (fragmento 3)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss3|Memória RSS usada (fragmento 3)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad3|Carga do servidor (fragmento 3)|Percentagem|Máximo||Nenhuma dimensão|
|cacheWrite3|Gravação no cache (fragmento 3)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead3|Leitura do cache (fragmento 3)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime3|CPU (fragmento 3)|Percentagem|Máximo||Nenhuma dimensão|
|connectedclients4|Clientes conectados (fragmento 4)|Contagem|Máximo||Nenhuma dimensão|
|totalcommandsprocessed4|Total de operações (fragmento 4)|Contagem|Total||Nenhuma dimensão|
|cachehits4|Acertos do cache (fragmento 4)|Contagem|Total||Nenhuma dimensão|
|cachemisses4|Erros de cache (fragmento 4)|Contagem|Total||Nenhuma dimensão|
|getcommands4|Gets (fragmento 4)|Contagem|Total||Nenhuma dimensão|
|setcommands4|Sets (fragmento 4)|Contagem|Total||Nenhuma dimensão|
|operationsPerSecond4|Operações por segundo (fragmento 4)|Contagem|Máximo||Nenhuma dimensão|
|evictedkeys4|Chaves removidas (fragmento 4)|Contagem|Total||Nenhuma dimensão|
|totalkeys4|Total de chaves (fragmento 4)|Contagem|Máximo||Nenhuma dimensão|
|expiredkeys4|Chaves expiradas (fragmento 4)|Contagem|Total||Nenhuma dimensão|
|usedmemory4|Memória usada (fragmento 4)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss4|Memória RSS usada (fragmento 4)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad4|Carga do servidor (fragmento 4)|Percentagem|Máximo||Nenhuma dimensão|
|cacheWrite4|Gravação no cache (fragmento 4)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead4|Leitura do cache (fragmento 4)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime4|CPU (fragmento 4)|Percentagem|Máximo||Nenhuma dimensão|
|connectedclients5|Clientes conectados (fragmento 5)|Contagem|Máximo||Nenhuma dimensão|
|totalcommandsprocessed5|Total de operações (fragmento 5)|Contagem|Total||Nenhuma dimensão|
|cachehits5|Acertos do cache (fragmento 5)|Contagem|Total||Nenhuma dimensão|
|cachemisses5|Erros de cache (fragmento 5)|Contagem|Total||Nenhuma dimensão|
|getcommands5|Gets (fragmento 5)|Contagem|Total||Nenhuma dimensão|
|setcommands5|Sets (fragmento 5)|Contagem|Total||Nenhuma dimensão|
|operationsPerSecond5|Operações por segundo (fragmento 5)|Contagem|Máximo||Nenhuma dimensão|
|evictedkeys5|Chaves removidas (fragmento 5)|Contagem|Total||Nenhuma dimensão|
|totalkeys5|Total de chaves (fragmento 5)|Contagem|Máximo||Nenhuma dimensão|
|expiredkeys5|Chaves expiradas (fragmento 5)|Contagem|Total||Nenhuma dimensão|
|usedmemory5|Memória usada (fragmento 5)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss5|Memória RSS usada (fragmento 5)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad5|Carga do servidor (fragmento 5)|Percentagem|Máximo||Nenhuma dimensão|
|cacheWrite5|Gravação no cache (fragmento 5)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead5|Leitura do cache (fragmento 5)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime5|CPU (fragmento 5)|Percentagem|Máximo||Nenhuma dimensão|
|connectedclients6|Clientes conectados (fragmento 6)|Contagem|Máximo||Nenhuma dimensão|
|totalcommandsprocessed6|Total de operações (fragmento 6)|Contagem|Total||Nenhuma dimensão|
|cachehits6|Acertos do cache (fragmento 6)|Contagem|Total||Nenhuma dimensão|
|cachemisses6|Erros de cache (fragmento 6)|Contagem|Total||Nenhuma dimensão|
|getcommands6|Gets (fragmento 6)|Contagem|Total||Nenhuma dimensão|
|setcommands6|Sets (fragmento 6)|Contagem|Total||Nenhuma dimensão|
|operationsPerSecond6|Operações por segundo (fragmento 6)|Contagem|Máximo||Nenhuma dimensão|
|evictedkeys6|Chaves removidas (fragmento 6)|Contagem|Total||Nenhuma dimensão|
|totalkeys6|Total de chaves (fragmento 6)|Contagem|Máximo||Nenhuma dimensão|
|expiredkeys6|Chaves expiradas (fragmento 6)|Contagem|Total||Nenhuma dimensão|
|usedmemory6|Memória usada (fragmento 6)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss6|Memória RSS usada (fragmento 6)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad6|Carga do servidor (fragmento 6)|Percentagem|Máximo||Nenhuma dimensão|
|cacheWrite6|Gravação no cache (fragmento 6)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead6|Leitura do cache (fragmento 6)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime6|CPU (fragmento 6)|Percentagem|Máximo||Nenhuma dimensão|
|connectedclients7|Clientes conectados (fragmento 7)|Contagem|Máximo||Nenhuma dimensão|
|totalcommandsprocessed7|Total de operações (fragmento 7)|Contagem|Total||Nenhuma dimensão|
|cachehits7|Acertos do cache (fragmento 7)|Contagem|Total||Nenhuma dimensão|
|cachemisses7|Erros de cache (fragmento 7)|Contagem|Total||Nenhuma dimensão|
|getcommands7|Gets (fragmento 7)|Contagem|Total||Nenhuma dimensão|
|setcommands7|Sets (fragmento 7)|Contagem|Total||Nenhuma dimensão|
|operationsPerSecond7|Operações por segundo (fragmento 7)|Contagem|Máximo||Nenhuma dimensão|
|evictedkeys7|Chaves removidas (fragmento 7)|Contagem|Total||Nenhuma dimensão|
|totalkeys7|Total de chaves (fragmento 7)|Contagem|Máximo||Nenhuma dimensão|
|expiredkeys7|Chaves expiradas (fragmento 7)|Contagem|Total||Nenhuma dimensão|
|usedmemory7|Memória usada (fragmento 7)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss7|Memória RSS usada (fragmento 7)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad7|Carga do servidor (fragmento 7)|Percentagem|Máximo||Nenhuma dimensão|
|cacheWrite7|Gravação no cache (fragmento 7)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead7|Leitura do cache (fragmento 7)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime7|CPU (fragmento 7)|Percentagem|Máximo||Nenhuma dimensão|
|connectedclients8|Clientes conectados (fragmento 8)|Contagem|Máximo||Nenhuma dimensão|
|totalcommandsprocessed8|Total de operações (fragmento 8)|Contagem|Total||Nenhuma dimensão|
|cachehits8|Acertos do cache (fragmento 8)|Contagem|Total||Nenhuma dimensão|
|cachemisses8|Erros de cache (fragmento 8)|Contagem|Total||Nenhuma dimensão|
|getcommands8|Gets (fragmento 8)|Contagem|Total||Nenhuma dimensão|
|setcommands8|Sets (fragmento 8)|Contagem|Total||Nenhuma dimensão|
|operationsPerSecond8|Operações por segundo (fragmento 8)|Contagem|Máximo||Nenhuma dimensão|
|evictedkeys8|Chaves removidas (fragmento 8)|Contagem|Total||Nenhuma dimensão|
|totalkeys8|Total de chaves (fragmento 8)|Contagem|Máximo||Nenhuma dimensão|
|expiredkeys8|Chaves expiradas (fragmento 8)|Contagem|Total||Nenhuma dimensão|
|usedmemory8|Memória usada (fragmento 8)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss8|Memória RSS usada (fragmento 8)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad8|Carga do servidor (fragmento 8)|Percentagem|Máximo||Nenhuma dimensão|
|cacheWrite8|Gravação no cache (fragmento 8)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead8|Leitura de cache (fragmento 8)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime8|CPU (fragmento 8)|Percentagem|Máximo||Nenhuma dimensão|
|connectedclients9|Clientes conectados (fragmento 9)|Contagem|Máximo||Nenhuma dimensão|
|totalcommandsprocessed9|Total de operações (fragmento 9)|Contagem|Total||Nenhuma dimensão|
|cachehits9|Acertos do cache (fragmento 9)|Contagem|Total||Nenhuma dimensão|
|cachemisses9|Erros de cache (fragmento 9)|Contagem|Total||Nenhuma dimensão|
|getcommands9|Gets (fragmento 9)|Contagem|Total||Nenhuma dimensão|
|setcommands9|Sets (fragmento 9)|Contagem|Total||Nenhuma dimensão|
|operationsPerSecond9|Operações por segundo (fragmento 9)|Contagem|Máximo||Nenhuma dimensão|
|evictedkeys9|Chaves removidas (fragmento 9)|Contagem|Total||Nenhuma dimensão|
|totalkeys9|Total de chaves (fragmento 9)|Contagem|Máximo||Nenhuma dimensão|
|expiredkeys9|Chaves expiradas (fragmento 9)|Contagem|Total||Nenhuma dimensão|
|usedmemory9|Memória usada (fragmento 9)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss9|Memória RSS usada (fragmento 9)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad9|Carga do servidor (fragmento 9)|Percentagem|Máximo||Nenhuma dimensão|
|cacheWrite9|Gravação no cache (fragmento 9)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead9|Leitura do cache (fragmento 9)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime9|CPU (fragmento 9)|Percentagem|Máximo||Nenhuma dimensão|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A porcentagem de unidades de computação alocadas que estão atualmente em uso pelas máquinas virtuais.|Nenhuma dimensão|
|Entrada na Rede|Entrada na Rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada).|Nenhuma dimensão|
|Saída da Rede|Saída da Rede|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída).|Nenhuma dimensão|
|Bytes de leitura de disco/s|Leitura de disco|BytesPerSecond|Média|Média de bytes lidos do disco durante o período de monitoramento.|Nenhuma dimensão|
|Bytes de gravação no disco/s|Gravação de disco|BytesPerSecond|Média|Média de bytes gravados no disco durante o período de monitoramento.|Nenhuma dimensão|
|Operações de leitura de disco/s|Operações de leitura de disco/s|CountPerSecond|Média|IOPS de leitura de disco.|Nenhuma dimensão|
|Operações de gravação de disco/s|Operações de gravação de disco/s|CountPerSecond|Média|IOPS de gravação de disco.|Nenhuma dimensão|

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

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalCalls|Total de chamadas|Contagem|Total|Número total de chamadas.|ApiName, OperationName, região|
|SuccessfulCalls|Chamadas com êxito|Contagem|Total|Número de chamadas bem-sucedidas.|ApiName, OperationName, região|
|TotalErrors|Total de erros|Contagem|Total|Número total de chamadas com resposta de erro (código de resposta HTTP 4xx ou 5xx).|ApiName, OperationName, região|
|BlockedCalls|Chamadas bloqueadas|Contagem|Total|Número de chamadas que excederam a taxa ou o limite de cota.|ApiName, OperationName, região|
|ServerErrors|Erros do servidor|Contagem|Total|Número de chamadas com erro interno do serviço (código de resposta HTTP 5xx).|ApiName, OperationName, região|
|ClientErrors|Erros do cliente|Contagem|Total|Número de chamadas com erro do lado do cliente (código de resposta HTTP 4xx).|ApiName, OperationName, região|
|Dados de|Dados em|Bytes|Total|Tamanho dos dados de entrada em bytes.|ApiName, OperationName, região|
|DataOut|Saída de dados|Bytes|Total|Tamanho dos dados de saída em bytes.|ApiName, OperationName, região|
|Latência|Latência|Milissegundos|Média|Latência em milissegundos.|ApiName, OperationName, região|
|CharactersTranslated|Caracteres traduzidos|Contagem|Total|Número total de caracteres na solicitação de texto de entrada.|ApiName, OperationName, região|
|CharactersTrained|Caracteres treinados|Contagem|Total|Número total de caracteres treinados.|ApiName, OperationName, região|
|SpeechSessionDuration|Duração da sessão de fala|Segundos|Total|Duração total da sessão de fala em segundos.|ApiName, OperationName, região|
|TotalTransactions|Total de transações|Contagem|Total|Número total de transações.|Nenhuma dimensão|
|TotalTokenCalls|Total de chamadas de token|Contagem|Total|Número total de chamadas de token.|ApiName, OperationName, região|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades do computador alocadas que estão atualmente em utilização pela Máquina Virtual ou Máquinas Virtuais|Nenhuma dimensão|
|Entrada na Rede|Rede em Faturável|Bytes|Total|O número de bytes faturáveis recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada)|Nenhuma dimensão|
|Saída da Rede|Saída de rede Faturável|Bytes|Total|O número de bytes faturáveis de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída)|Nenhuma dimensão|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|Nenhuma dimensão|
|Bytes de gravação no disco|Bytes de gravação no disco|Bytes|Total|Bytes gravados no disco durante o período de monitoramento|Nenhuma dimensão|
|Operações de leitura de disco/s|Operações de leitura de disco/s|CountPerSecond|Média|IOPS de leitura de disco|Nenhuma dimensão|
|Operações de gravação de disco/s|Operações de gravação de disco/s|CountPerSecond|Média|IOPS de gravação de disco|Nenhuma dimensão|
|Créditos de CPU restantes|Créditos de CPU restantes|Contagem|Média|Número total de créditos disponíveis para intermitência|Nenhuma dimensão|
|Créditos de CPU consumidos|Créditos de CPU consumidos|Contagem|Média|Número total de créditos consumidos pela máquina virtual|Nenhuma dimensão|
|Bytes de leitura por disco/s|Bytes de leitura do disco de dados/s (preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|SlotId|
|Bytes de gravação por disco/s|Bytes de gravação do disco de dados/s (preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|SlotId|
|Operações de leitura por disco/s|Operações de leitura de disco de dados/s (preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|SlotId|
|Operações de gravação por disco/s|Operações de gravação do disco de dados/s (preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|SlotId|
|QD por disco|Disco de dados QD (preterido)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|SlotId|
|Bytes de leitura por disco do so/s|Bytes de leitura do disco do so/s (preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma dimensão|
|Bytes de gravação por disco do so/s|Bytes de gravação de disco do sistema operacional/s (preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Nenhuma dimensão|
|Operações de leitura de so por disco/s|Operações de leitura de disco do so/s (preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma dimensão|
|Operações de gravação de so por disco/s|Operações de gravação de disco do sistema operacional/s (preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma dimensão|
|Sistema operacional por disco QD|Disco do so QD (preterido)|Contagem|Média|Profundidade da fila de disco do so (ou comprimento da fila)|Nenhuma dimensão|
|Bytes de leitura do disco de dados/s|Bytes de leitura do disco de dados/s (visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|LUN|
|Bytes de gravação do disco de dados/s|Bytes de gravação do disco de dados/s (visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|LUN|
|Operações de leitura de disco de dados/s|Operações de leitura de disco de dados/s (visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|LUN|
|Operações de gravação do disco de dados/s|Operações de gravação do disco de dados/s (visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|LUN|
|Profundidade da fila do disco de dados|Profundidade da fila do disco de dados (visualização)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|LUN|
|Bytes de leitura do disco do so/s|Bytes de leitura do disco do so/s (visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma dimensão|
|Bytes de gravação de disco do sistema operacional/s|Bytes de gravação de disco do sistema operacional/s (visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Nenhuma dimensão|
|Operações de leitura de disco do so/s|Operações de leitura de disco do so/s (visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma dimensão|
|Operações de gravação de disco do so/s|Operações de gravação de disco do so/s (visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma dimensão|
|Profundidade da fila de disco do so|Profundidade da fila de disco do so (visualização)|Contagem|Média|Profundidade da fila de disco do so (ou comprimento da fila)|Nenhuma dimensão|
|Fluxos de entrada|Fluxos de entrada (visualização)|Contagem|Média|Os fluxos de entrada são o número de fluxos atuais na direção de entrada (tráfego entrando na VM)|Nenhuma dimensão|
|Fluxos de saída|Fluxos de saída (visualização)|Contagem|Média|Fluxos de saída são o número de fluxos atuais na direção de saída (tráfego saindo da VM)|Nenhuma dimensão|
|Taxa máxima de criação de fluxos de entrada|Taxa máxima de criação de fluxos de entrada (visualização)|CountPerSecond|Média|A taxa de criação máxima de fluxos de entrada (tráfego entrando na VM)|Nenhuma dimensão|
|Taxa máxima de criação de fluxos de saída|Taxa máxima de criação de fluxos de saída (versão prévia)|CountPerSecond|Média|A taxa de criação máxima de fluxos de saída (tráfego saindo da VM)|Nenhuma dimensão|
|Cache do disco de dados Premium-acesso de leitura|Leitura do cache do disco de dados Premium (versão prévia)|Percentagem|Média|Cache do disco de dados Premium-acesso de leitura|LUN|
|Erro de leitura do cache do disco de dados Premium|Cache do disco de dados Premium-erro de leitura (visualização)|Percentagem|Média|Erro de leitura do cache do disco de dados Premium|LUN|
|Cache de disco do sistema operacional Premium-acesso de leitura|Cache de disco do sistema operacional Premium-acesso de leitura (versão prévia)|Percentagem|Média|Cache de disco do sistema operacional Premium-acesso de leitura|Nenhuma dimensão|
|Erro de leitura do cache de disco do so premium|Erro de leitura do cache de disco do so premium (visualização)|Percentagem|Média|Erro de leitura do cache de disco do so premium|Nenhuma dimensão|
|Total de rede|Total de rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada)|Nenhuma dimensão|
|Total de saída de rede|Total de saída de rede|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída)|Nenhuma dimensão|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades do computador alocadas que estão atualmente em utilização pela Máquina Virtual ou Máquinas Virtuais|VMName|
|Entrada na Rede|Rede em Faturável|Bytes|Total|O número de bytes faturáveis recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada)|VMName|
|Saída da Rede|Saída de rede Faturável|Bytes|Total|O número de bytes faturáveis de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída)|VMName|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|VMName|
|Bytes de gravação no disco|Bytes de gravação no disco|Bytes|Total|Bytes gravados no disco durante o período de monitoramento|VMName|
|Operações de leitura de disco/s|Operações de leitura de disco/s|CountPerSecond|Média|IOPS de leitura de disco|VMName|
|Operações de gravação de disco/s|Operações de gravação de disco/s|CountPerSecond|Média|IOPS de gravação de disco|VMName|
|Créditos de CPU restantes|Créditos de CPU restantes|Contagem|Média|Número total de créditos disponíveis para intermitência|Nenhuma dimensão|
|Créditos de CPU consumidos|Créditos de CPU consumidos|Contagem|Média|Número total de créditos consumidos pela máquina virtual|Nenhuma dimensão|
|Bytes de leitura por disco/s|Bytes de leitura do disco de dados/s (preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|SlotId|
|Bytes de gravação por disco/s|Bytes de gravação do disco de dados/s (preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|SlotId|
|Operações de leitura por disco/s|Operações de leitura de disco de dados/s (preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|SlotId|
|Operações de gravação por disco/s|Operações de gravação do disco de dados/s (preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|SlotId|
|QD por disco|Disco de dados QD (preterido)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|SlotId|
|Bytes de leitura por disco do so/s|Bytes de leitura do disco do so/s (preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma dimensão|
|Bytes de gravação por disco do so/s|Bytes de gravação de disco do sistema operacional/s (preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Nenhuma dimensão|
|Operações de leitura de so por disco/s|Operações de leitura de disco do so/s (preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma dimensão|
|Operações de gravação de so por disco/s|Operações de gravação de disco do sistema operacional/s (preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhuma dimensão|
|Sistema operacional por disco QD|Disco do so QD (preterido)|Contagem|Média|Profundidade da fila de disco do so (ou comprimento da fila)|Nenhuma dimensão|
|Bytes de leitura do disco de dados/s|Bytes de leitura do disco de dados/s (visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|LUN, VMName|
|Bytes de gravação do disco de dados/s|Bytes de gravação do disco de dados/s (visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|LUN, VMName|
|Operações de leitura de disco de dados/s|Operações de leitura de disco de dados/s (visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|LUN, VMName|
|Operações de gravação do disco de dados/s|Operações de gravação do disco de dados/s (visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|LUN, VMName|
|Profundidade da fila do disco de dados|Profundidade da fila do disco de dados (visualização)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|LUN, VMName|
|Bytes de leitura do disco do so/s|Bytes de leitura do disco do so/s (visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|VMName|
|Bytes de gravação de disco do sistema operacional/s|Bytes de gravação de disco do sistema operacional/s (visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|VMName|
|Operações de leitura de disco do so/s|Operações de leitura de disco do so/s (visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|VMName|
|Operações de gravação de disco do so/s|Operações de gravação de disco do so/s (visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|VMName|
|Profundidade da fila de disco do so|Profundidade da fila de disco do so (visualização)|Contagem|Média|Profundidade da fila de disco do so (ou comprimento da fila)|VMName|
|Fluxos de entrada|Fluxos de entrada (visualização)|Contagem|Média|Os fluxos de entrada são o número de fluxos atuais na direção de entrada (tráfego entrando na VM)|VMName|
|Fluxos de saída|Fluxos de saída (visualização)|Contagem|Média|Fluxos de saída são o número de fluxos atuais na direção de saída (tráfego saindo da VM)|VMName|
|Taxa máxima de criação de fluxos de entrada|Taxa máxima de criação de fluxos de entrada (visualização)|CountPerSecond|Média|A taxa de criação máxima de fluxos de entrada (tráfego entrando na VM)|VMName|
|Taxa máxima de criação de fluxos de saída|Taxa máxima de criação de fluxos de saída (versão prévia)|CountPerSecond|Média|A taxa de criação máxima de fluxos de saída (tráfego saindo da VM)|VMName|
|Cache do disco de dados Premium-acesso de leitura|Leitura do cache do disco de dados Premium (versão prévia)|Percentagem|Média|Cache do disco de dados Premium-acesso de leitura|LUN, VMName|
|Erro de leitura do cache do disco de dados Premium|Cache do disco de dados Premium-erro de leitura (visualização)|Percentagem|Média|Erro de leitura do cache do disco de dados Premium|LUN, VMName|
|Cache de disco do sistema operacional Premium-acesso de leitura|Cache de disco do sistema operacional Premium-acesso de leitura (versão prévia)|Percentagem|Média|Cache de disco do sistema operacional Premium-acesso de leitura|VMName|
|Erro de leitura do cache de disco do so premium|Erro de leitura do cache de disco do so premium (visualização)|Percentagem|Média|Erro de leitura do cache de disco do so premium|VMName|
|Total de rede|Total de rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada)|VMName|
|Total de saída de rede|Total de saída de rede|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Os|Uso da CPU|Contagem|Média|Uso da CPU em todos os núcleos em milicores.|containerName|
|MemoryUsage|Uso de memória|Bytes|Média|Uso total de memória em byte.|containerName|
|NetworkBytesReceivedPerSecond|Bytes de rede recebidos por segundo|Bytes|Média|Os bytes de rede recebidos por segundo.|Nenhuma dimensão|
|NetworkBytesTransmittedPerSecond|Bytes de rede transmitidos por segundo|Bytes|Média|Os bytes de rede transmitidos por segundo.|Nenhuma dimensão|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalPullCount|Contagem total de pull|Contagem|Média|Número de pulls de imagem no total|Nenhuma dimensão|
|SuccessfulPullCount|Contagem de pull bem-sucedida|Contagem|Média|Número de pulls de imagem bem-sucedidos|Nenhuma dimensão|
|TotalPushCount|Contagem total de push|Contagem|Média|Número de envios por push de imagem no total|Nenhuma dimensão|
|SuccessfulPushCount|Contagem de push bem-sucedida|Contagem|Média|Número de Pushes de imagem bem-sucedidos|Nenhuma dimensão|
|RunDuration|Duração da execução|Milissegundos|Total|Duração da execução em milissegundos|Nenhuma dimensão|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Número total de núcleos de CPU disponíveis em um cluster gerenciado|Contagem|Total|Número total de núcleos de CPU disponíveis em um cluster gerenciado|Nenhuma dimensão|
|kube_node_status_allocatable_memory_bytes|Quantidade total de memória disponível em um cluster gerenciado|Bytes|Total|Quantidade total de memória disponível em um cluster gerenciado|Nenhuma dimensão|
|kube_pod_status_ready|Número de pods no estado pronto|Contagem|Total|Número de pods no estado pronto|namespace, Pod|
|kube_node_status_condition|Status para várias condições de nó|Contagem|Total|Status para várias condições de nó|condição, status, status2, nó|
|kube_pod_status_phase|Número de pods por fase|Contagem|Total|Número de pods por fase|fase, namespace, Pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights chamadas à API|Contagem|Total||Nenhuma dimensão|
|DCIMappingImportOperationSuccessfulLines|Mapeando linhas com êxito da operação de importação|Contagem|Total||Nenhuma dimensão|
|DCIMappingImportOperationFailedLines|Linhas com falha na operação de importação de mapeamento|Contagem|Total||Nenhuma dimensão|
|DCIMappingImportOperationTotalLines|Mapeamento de linhas totais da operação de importação|Contagem|Total||Nenhuma dimensão|
|DCIMappingImportOperationRuntimeInSeconds|Mapeando tempo de execução da operação de importação em segundos|Segundos|Total||Nenhuma dimensão|
|DCIOutboundProfileExportSucceeded|Exportação do perfil de saída bem-sucedida|Contagem|Total||Nenhuma dimensão|
|DCIOutboundProfileExportFailed|Falha na exportação do perfil de saída|Contagem|Total||Nenhuma dimensão|
|DCIOutboundProfileExportDuration|Duração da exportação do perfil de saída|Segundos|Total||Nenhuma dimensão|
|DCIOutboundKpiExportSucceeded|Exportação de KPI de saída bem-sucedida|Contagem|Total||Nenhuma dimensão|
|DCIOutboundKpiExportFailed|Falha na exportação do KPI de saída|Contagem|Total||Nenhuma dimensão|
|DCIOutboundKpiExportDuration|Duração da exportação do KPI de saída|Segundos|Total||Nenhuma dimensão|
|DCIOutboundKpiExportStarted|Exportação de KPI de saída iniciada|Segundos|Total||Nenhuma dimensão|
|DCIOutboundKpiRecordCount|Contagem de registros de KPI de saída|Segundos|Total||Nenhuma dimensão|
|DCIOutboundProfileExportCount|Contagem de exportação do perfil de saída|Segundos|Total||Nenhuma dimensão|
|DCIOutboundInitialProfileExportFailed|Falha na exportação do perfil inicial de saída|Segundos|Total||Nenhuma dimensão|
|DCIOutboundInitialProfileExportSucceeded|Exportação do perfil inicial de saída bem-sucedida|Segundos|Total||Nenhuma dimensão|
|DCIOutboundInitialKpiExportFailed|Falha ao exportar o KPI inicial de saída|Segundos|Total||Nenhuma dimensão|
|DCIOutboundInitialKpiExportSucceeded|Exportação de KPI inicial de saída bem-sucedida|Segundos|Total||Nenhuma dimensão|
|DCIOutboundInitialProfileExportDurationInSeconds|Duração da exportação do perfil inicial de saída em segundos|Segundos|Total||Nenhuma dimensão|
|AdlaJobForStandardKpiFailed|O trabalho do adla para KPI padrão falhou em segundos|Segundos|Total||Nenhuma dimensão|
|AdlaJobForStandardKpiTimeOut|Trabalho de adla para o tempo limite de KPI padrão em segundos|Segundos|Total||Nenhuma dimensão|
|AdlaJobForStandardKpiCompleted|Trabalho de adla para KPI padrão concluído em segundos|Segundos|Total||Nenhuma dimensão|
|ImportASAValuesFailed|Falha na importação de valores ASA de conta|Contagem|Total||Nenhuma dimensão|
|ImportASAValuesSucceeded|Contagem de importação de valores ASA com êxito|Contagem|Total||Nenhuma dimensão|
|DCIProfilesCount|Contagem de instâncias de perfil|Contagem|Último||Nenhuma dimensão|
|DCIInteractionsPerMonthCount|A contagem de interações por mês|Contagem|Último||Nenhuma dimensão|
|DCIKpisCount|Contagem de KPIs|Contagem|Último||Nenhuma dimensão|
|DCISegmentsCount|Contagem de segmentos|Contagem|Último||Nenhuma dimensão|
|DCIPredictiveMatchPoliciesCount|Contagem de correspondência preditiva|Contagem|Último||Nenhuma dimensão|
|DCIPredictionsCount|Contagem de previsão|Contagem|Último||Nenhuma dimensão|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|NICReadThroughput|Taxa de transferência de leitura (rede)|BytesPerSecond|Média|A taxa de transferência de leitura da interface de rede no dispositivo no período de relatório para todos os volumes no gateway.|InstanceName|
|NICWriteThroughput|Taxa de transferência de gravação (rede)|BytesPerSecond|Média|A taxa de transferência de gravação da interface de rede no dispositivo no período de relatório para todos os volumes no gateway.|InstanceName|
|CloudReadThroughputPerShare|Taxa de transferência de download de nuvem (compartilhamento)|BytesPerSecond|Média|A taxa de transferência de download para o Azure de um compartilhamento durante o período de relatório.|Partilhar|
|CloudUploadThroughputPerShare|Taxa de transferência de upload de nuvem (compartilhamento)|BytesPerSecond|Média|A taxa de transferência de upload para o Azure de um compartilhamento durante o período de relatório.|Partilhar|
|BytesUploadedToCloudPerShare|Bytes de nuvem carregados (compartilhamento)|Bytes|Média|O número total de bytes que são carregados no Azure de um compartilhamento durante o período de relatório.|Partilhar|
|TotalCapacity|Capacidade total|Bytes|Média|Capacidade total|Nenhuma dimensão|
|AvailableCapacity|Capacidade disponível|Bytes|Média|A capacidade disponível em bytes durante o período de relatório.|Nenhuma dimensão|
|CloudUploadThroughput|Taxa de transferência de upload de nuvem|BytesPerSecond|Média|A taxa de transferência de upload de nuvem para o Azure durante o período de relatório.|Nenhuma dimensão|
|CloudReadThroughput|Taxa de transferência de download da nuvem|BytesPerSecond|Média|A taxa de transferência de download de nuvem para o Azure durante o período de relatório.|Nenhuma dimensão|
|BytesUploadedToCloud|Bytes de nuvem carregados (dispositivo)|Bytes|Média|O número total de bytes que são carregados no Azure de um dispositivo durante o período de relatório.|Nenhuma dimensão|
|HyperVVirtualProcessorUtilization|Computação de borda – percentual de CPU|Percentagem|Média|Porcentagem de uso da CPU|InstanceName|
|HyperVMemoryUtilization|Computação de borda-uso de memória|Percentagem|Média|Quantidade de RAM em uso|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|FailedRuns|Execuções com falha|Contagem|Total||pipelinename, ActivityName|
|SuccessfulRuns|Execuções com êxito|Contagem|Total||pipelinename, ActivityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PipelineFailedRuns|Métricas de execuções de pipeline com falha|Contagem|Total||FailureType, nome|
|PipelineSucceededRuns|Métricas de execuções de pipeline com êxito|Contagem|Total||FailureType, nome|
|ActivityFailedRuns|Métricas de execuções de atividade com falha|Contagem|Total||ActivityType, Pipelinename, Falhatype, nome|
|ActivitySucceededRuns|Métricas de execuções de atividades bem-sucedidas|Contagem|Total||ActivityType, Pipelinename, Falhatype, nome|
|TriggerFailedRuns|Métricas de execuções de gatilho com falha|Contagem|Total||Nome, Falhatype|
|TriggerSucceededRuns|Métricas de execuções de gatilho com êxito|Contagem|Total||Nome, Falhatype|
|IntegrationRuntimeCpuPercentage|Utilização da CPU do Integration Runtime|Percentagem|Média||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Memória disponível do Integration Runtime|Bytes|Média||IntegrationRuntimeName, NodeName|
|MaxAllowedResourceCount|Contagem máxima de entidades permitidas|Contagem|Máximo||Nenhuma dimensão|
|MaxAllowedFactorySizeInGbUnits|Tamanho máximo de fábrica permitido (unidade GB)|Contagem|Máximo||Nenhuma dimensão|
|ResourceCount|Contagem total de entidades|Contagem|Máximo||Nenhuma dimensão|
|FactorySizeInGbUnits|Tamanho total de fábrica (unidade GB)|Contagem|Máximo||Nenhuma dimensão|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|JobEndedSuccess|Trabalhos com êxito|Contagem|Total|Contagem de trabalhos bem-sucedidos.|Nenhuma dimensão|
|JobEndedFailure|Trabalhos com falha|Contagem|Total|Contagem de trabalhos com falha.|Nenhuma dimensão|
|JobEndedCancelled|Trabalhos cancelados|Contagem|Total|Contagem de trabalhos cancelados.|Nenhuma dimensão|
|JobAUEndedSuccess|Tempo de AU bem-sucedido|Segundos|Total|Tempo total de AU para trabalhos bem-sucedidos.|Nenhuma dimensão|
|JobAUEndedFailure|Tempo de AU com falha|Segundos|Total|Tempo total de AU para trabalhos com falha.|Nenhuma dimensão|
|JobAUEndedCancelled|Hora de AU cancelada|Segundos|Total|Tempo total de AU para trabalhos cancelados.|Nenhuma dimensão|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalStorage|Armazenamento Total|Bytes|Máximo|Quantidade total de dados armazenados na conta.|Nenhuma dimensão|
|Gravado em|Dados gravados|Bytes|Total|Quantidade total de dados gravados na conta.|Nenhuma dimensão|
|DataRead|Leitura de dados|Bytes|Total|Quantidade total de dados lidos da conta.|Nenhuma dimensão|
|WriteRequests|Solicitações de gravação|Contagem|Total|Contagem de solicitações de gravação de dados para a conta.|Nenhuma dimensão|
|ReadRequests|Solicitações de leitura|Contagem|Total|Contagem de solicitações de leitura de dados para a conta.|Nenhuma dimensão|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentual de CPU|Percentagem|Média|Percentual de CPU|Nenhuma dimensão|
|memory_percent|Porcentagem de memória|Percentagem|Média|Porcentagem de memória|Nenhuma dimensão|
|io_consumption_percent|Porcentagem de e/s|Percentagem|Média|Porcentagem de e/s|Nenhuma dimensão|
|storage_percent|Porcentagem de armazenamento|Percentagem|Média|Porcentagem de armazenamento|Nenhuma dimensão|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhuma dimensão|
|storage_limit|Limite de armazenamento|Bytes|Média|Limite de armazenamento|Nenhuma dimensão|
|serverlog_storage_percent|Porcentagem de armazenamento de log do servidor|Percentagem|Média|Porcentagem de armazenamento de log do servidor|Nenhuma dimensão|
|serverlog_storage_usage|Armazenamento de log do servidor usado|Bytes|Média|Armazenamento de log do servidor usado|Nenhuma dimensão|
|serverlog_storage_limit|Limite de armazenamento de log do servidor|Bytes|Média|Limite de armazenamento de log do servidor|Nenhuma dimensão|
|active_connections|Conexões ativas|Contagem|Média|Conexões ativas|Nenhuma dimensão|
|connections_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Nenhuma dimensão|
|seconds_behind_master|Atraso de replicação em segundos|Contagem|Média|Atraso de replicação em segundos|Nenhuma dimensão|
|backup_storage_used|Armazenamento de backup usado|Bytes|Média|Armazenamento de backup usado|Nenhuma dimensão|
|network_bytes_egress|Saída da Rede|Bytes|Total|Saída de rede entre conexões ativas|Nenhuma dimensão|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Entrada de rede em conexões ativas|Nenhuma dimensão|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentual de CPU|Percentagem|Média|Percentual de CPU|Nenhuma dimensão|
|memory_percent|Porcentagem de memória|Percentagem|Média|Porcentagem de memória|Nenhuma dimensão|
|io_consumption_percent|Porcentagem de e/s|Percentagem|Média|Porcentagem de e/s|Nenhuma dimensão|
|storage_percent|Porcentagem de armazenamento|Percentagem|Média|Porcentagem de armazenamento|Nenhuma dimensão|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhuma dimensão|
|storage_limit|Limite de armazenamento|Bytes|Média|Limite de armazenamento|Nenhuma dimensão|
|serverlog_storage_percent|Porcentagem de armazenamento de log do servidor|Percentagem|Média|Porcentagem de armazenamento de log do servidor|Nenhuma dimensão|
|serverlog_storage_usage|Armazenamento de log do servidor usado|Bytes|Média|Armazenamento de log do servidor usado|Nenhuma dimensão|
|serverlog_storage_limit|Limite de armazenamento de log do servidor|Bytes|Média|Limite de armazenamento de log do servidor|Nenhuma dimensão|
|active_connections|Conexões ativas|Contagem|Média|Conexões ativas|Nenhuma dimensão|
|connections_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Nenhuma dimensão|
|seconds_behind_master|Atraso de replicação em segundos|Contagem|Média|Atraso de replicação em segundos|Nenhuma dimensão|
|backup_storage_used|Armazenamento de backup usado|Bytes|Média|Armazenamento de backup usado|Nenhuma dimensão|
|network_bytes_egress|Saída da Rede|Bytes|Total|Saída de rede entre conexões ativas|Nenhuma dimensão|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Entrada de rede em conexões ativas|Nenhuma dimensão|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentual de CPU|Percentagem|Média|Percentual de CPU|Nenhuma dimensão|
|memory_percent|Porcentagem de memória|Percentagem|Média|Porcentagem de memória|Nenhuma dimensão|
|io_consumption_percent|Porcentagem de e/s|Percentagem|Média|Porcentagem de e/s|Nenhuma dimensão|
|storage_percent|Porcentagem de armazenamento|Percentagem|Média|Porcentagem de armazenamento|Nenhuma dimensão|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhuma dimensão|
|storage_limit|Limite de armazenamento|Bytes|Média|Limite de armazenamento|Nenhuma dimensão|
|serverlog_storage_percent|Porcentagem de armazenamento de log do servidor|Percentagem|Média|Porcentagem de armazenamento de log do servidor|Nenhuma dimensão|
|serverlog_storage_usage|Armazenamento de log do servidor usado|Bytes|Média|Armazenamento de log do servidor usado|Nenhuma dimensão|
|serverlog_storage_limit|Limite de armazenamento de log do servidor|Bytes|Média|Limite de armazenamento de log do servidor|Nenhuma dimensão|
|active_connections|Conexões ativas|Contagem|Média|Conexões ativas|Nenhuma dimensão|
|connections_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Nenhuma dimensão|
|backup_storage_used|Armazenamento de backup usado|Bytes|Média|Armazenamento de backup usado|Nenhuma dimensão|
|network_bytes_egress|Saída da Rede|Bytes|Total|Saída de rede entre conexões ativas|Nenhuma dimensão|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Entrada de rede em conexões ativas|Nenhuma dimensão|
|pg_replica_log_delay_in_seconds|Atraso de réplica|Segundos|Máximo|Atraso de réplica em segundos|Nenhuma dimensão|
|pg_replica_log_delay_in_bytes|Atraso máximo entre réplicas|Bytes|Máximo|Atraso em bytes da réplica mais defasante|Nenhuma dimensão|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentual de CPU|Percentagem|Média|Percentual de CPU|Nenhuma dimensão|
|memory_percent|Porcentagem de memória|Percentagem|Média|Porcentagem de memória|Nenhuma dimensão|
|IOPS|IOPS|Contagem|Média|Operações de e/s por segundo|Nenhuma dimensão|
|storage_percent|Porcentagem de armazenamento|Percentagem|Média|Porcentagem de armazenamento|Nenhuma dimensão|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhuma dimensão|
|active_connections|Conexões ativas|Contagem|Média|Conexões ativas|Nenhuma dimensão|
|network_bytes_egress|Saída da Rede|Bytes|Total|Saída de rede entre conexões ativas|Nenhuma dimensão|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Entrada de rede em conexões ativas|Nenhuma dimensão|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentativas de envio de mensagem de telemetria|Contagem|Total|Número de mensagens de telemetria do dispositivo para a nuvem que tentaram ser enviadas para o Hub IoT|Nenhuma dimensão|
|d2c.telemetry.ingress.success|Mensagens de telemetria enviadas|Contagem|Total|Número de mensagens de telemetria do dispositivo para a nuvem enviadas com êxito para o Hub IoT|Nenhuma dimensão|
|c2d.commands.egress.complete.success|Comandos concluídos|Contagem|Total|Número de comandos da nuvem para o dispositivo concluídos com êxito pelo dispositivo|Nenhuma dimensão|
|c2d.commands.egress.abandon.success|Comandos abandonados|Contagem|Total|Número de comandos da nuvem para o dispositivo abandonados pelo dispositivo|Nenhuma dimensão|
|c2d.commands.egress.reject.success|Comandos rejeitados|Contagem|Total|Número de comandos da nuvem para o dispositivo rejeitados pelo dispositivo|Nenhuma dimensão|
|devices.totalDevices|Total de dispositivos (preterido)|Contagem|Total|Número de dispositivos registrados no Hub IoT|Nenhuma dimensão|
|devices.connectedDevices.allProtocol|Dispositivos conectados (preterido) |Contagem|Total|Número de dispositivos conectados ao seu hub IoT|Nenhuma dimensão|
|d2c.telemetry.egress.success|Roteamento: mensagens de telemetria entregues|Contagem|Total|O número de vezes que as mensagens foram entregues com êxito a todos os pontos de extremidade usando o roteamento do Hub IoT. Se uma mensagem for roteada para vários pontos de extremidade, esse valor aumentará em um para cada entrega bem-sucedida. Se uma mensagem for entregue ao mesmo ponto de extremidade várias vezes, esse valor aumentará em um para cada entrega bem-sucedida.|Nenhuma dimensão|
|d2c.telemetry.egress.dropped|Roteamento: mensagens de telemetria eliminadas |Contagem|Total|O número de vezes que as mensagens foram removidas pelo roteamento do Hub IoT devido a pontos de extremidade inativos. Esse valor não conta as mensagens entregues à rota de fallback, pois as mensagens ignoradas não são entregues lá.|Nenhuma dimensão|
|d2c.telemetry.egress.orphaned|Roteamento: mensagens de telemetria órfãs |Contagem|Total|O número de vezes que as mensagens ficaram órfãs pelo roteamento do Hub IoT porque não corresponderam a nenhuma regra de roteamento (incluindo a norma de fallback). |Nenhuma dimensão|
|d2c.telemetry.egress.invalid|Roteamento: mensagens de telemetria incompatíveis|Contagem|Total|O número de vezes que o roteamento do Hub IoT falhou ao entregar mensagens devido a uma incompatibilidade com o ponto de extremidade. Esse valor não inclui repetições.|Nenhuma dimensão|
|d2c.telemetry.egress.fallback|Roteamento: mensagens entregues ao fallback|Contagem|Total|O número de vezes que o roteamento do Hub IoT fornece mensagens ao ponto de extremidade associado à rota de fallback.|Nenhuma dimensão|
|d2c.endpoints.egress.eventHubs|Roteamento: mensagens entregues ao Hub de eventos|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para pontos de extremidade do hub de eventos.|Nenhuma dimensão|
|d2c.endpoints.latency.eventHubs|Roteamento: latência de mensagem para o Hub de eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem em um ponto de extremidade do hub de eventos.|Nenhuma dimensão|
|d2c.endpoints.egress.serviceBusQueues|Roteamento: mensagens entregues à fila do barramento de serviço|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens aos pontos de extremidade da fila do barramento de serviço.|Nenhuma dimensão|
|d2c.endpoints.latency.serviceBusQueues|Roteamento: latência de mensagem para a fila do barramento de serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade da fila do barramento de serviço.|Nenhuma dimensão|
|d2c.endpoints.egress.serviceBusTopics|Roteamento: mensagens entregues ao tópico do barramento de serviço|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para pontos de extremidade de tópico do barramento de serviço.|Nenhuma dimensão|
|d2c.endpoints.latency.serviceBusTopics|Roteamento: latência de mensagem para o tópico do barramento de serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade de tópico do barramento de serviço.|Nenhuma dimensão|
|d2c.endpoints.egress.builtIn.events|Roteamento: mensagens entregues a mensagens/eventos|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para o ponto de extremidade interno (mensagens/eventos). Essa métrica só começa a funcionar quando o roteamento está habilitado (https://aka.ms/iotrouting) para o Hub IoT.|Nenhuma dimensão|
|d2c.endpoints.latency.builtIn.events|Roteamento: latência de mensagem para mensagens/eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem e o Hub IoT e a entrada da mensagem de telemetria no ponto de extremidade interno (mensagens/eventos). Essa métrica só começa a funcionar quando o roteamento está habilitado (https://aka.ms/iotrouting) para o Hub IoT.|Nenhuma dimensão|
|d2c.endpoints.egress.storage|Roteamento: mensagens entregues ao armazenamento|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para pontos de extremidade de armazenamento.|Nenhuma dimensão|
|d2c.endpoints.latency.storage|Roteamento: latência de mensagem para armazenamento|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade de armazenamento.|Nenhuma dimensão|
|d2c.endpoints.egress.storage.bytes|Roteamento: dados entregues ao armazenamento|Bytes|Total|A quantidade de dados (bytes) de roteamento do Hub IoT entregues aos pontos de extremidade de armazenamento.|Nenhuma dimensão|
|d2c.endpoints.egress.storage.blobs|Roteamento: BLOBs entregues ao armazenamento|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu BLOBs para pontos de extremidade de armazenamento.|Nenhuma dimensão|
|EventGridDeliveries|Entregas da grade de eventos (visualização)|Contagem|Total|O número de eventos do Hub IoT publicados na grade de eventos. Use a dimensão de resultado para o número de solicitações bem-sucedidas e com falha. A dimensão EventType mostra o tipo de evento (https://aka.ms/ioteventgrid).|Resultado, EventType|
|EventGridLatency|A latência média (milissegundos) de quando o evento do Hub IOT foi gerado quando o evento foi publicado na grade de eventos. Esse número é uma média entre todos os tipos de evento. Use a dimensão EventType para ver a latência de um tipo específico de evento.|EventType|
|d2c.twin.read.success|Leituras de entrelaçamento bem-sucedidas de dispositivos|Contagem|Total|A contagem de todas as leituras de entrelaças iniciadas pelo dispositivo bem-sucedidas.|Nenhuma dimensão|
|d2c.twin.read.failure|Leituras de entrelaçamento com falha de dispositivos|Contagem|Total|A contagem de todas as leituras de entrelaças iniciadas pelo dispositivo com falha.|Nenhuma dimensão|
|d2c.twin.read.size|Tamanho da resposta de leituras de entrelaçamento de dispositivos|Bytes|Média|A média, o mínimo e o máximo de todas as leituras de entrelaçadas iniciadas pelo dispositivo bem-sucedidas.|Nenhuma dimensão|
|d2c.twin.update.success|Atualizações de atualização com êxito de dispositivos|Contagem|Total|A contagem de todas as atualizações de atualização do dispositivo iniciadas com êxito.|Nenhuma dimensão|
|d2c.twin.update.failure|Atualizações de atualização de falha de dispositivos|Contagem|Total|A contagem de todas as atualizações de atualização do dispositivo iniciadas com falha.|Nenhuma dimensão|
|d2c.twin.update.size|Tamanho de atualizações de atualização de papel dos dispositivos|Bytes|Média|O tamanho médio, mínimo e máximo de todas as atualizações de atualização do dispositivo iniciadas com êxito.|Nenhuma dimensão|
|C2D. Methods. Success|Invocações de método diretos bem-sucedidas|Contagem|Total|A contagem de todas as chamadas de método diretas bem-sucedidas.|Nenhuma dimensão|
|c2d.methods.failure|Invocações de método direto com falha|Contagem|Total|A contagem de todas as chamadas de método diretas com falha.|Nenhuma dimensão|
|c2d.methods.requestSize|Tamanho da solicitação de invocações de método direto|Bytes|Média|A média, o mínimo e o máximo de todas as solicitações de método diretas bem-sucedidas.|Nenhuma dimensão|
|c2d.methods.responseSize|Tamanho da resposta de invocações de método diretas|Bytes|Média|A média, o mínimo e o máximo de todas as respostas de método diretas bem-sucedidas.|Nenhuma dimensão|
|c2d.twin.read.success|Leituras de cópia com êxito do back-end|Contagem|Total|A contagem de todas as leituras de cópia de bits iniciadas de back-end bem-sucedidas.|Nenhuma dimensão|
|c2d.twin.read.failure|Leituras de cópia com falha do back-end|Contagem|Total|A contagem de todas as leituras de cópia de bits iniciadas pelo back-end com falha.|Nenhuma dimensão|
|c2d.twin.read.size|Tamanho da resposta de leituras de cópia do back-end|Bytes|Média|A média, mín e máx de todas as leituras de cópia do back-end iniciadas com êxito.|Nenhuma dimensão|
|c2d.twin.update.success|Atualizações de cópia bem-sucedida do back-end|Contagem|Total|A contagem de todas as atualizações de cópia do back-end iniciadas com êxito.|Nenhuma dimensão|
|c2d.twin.update.failure|Atualizações de cópia com falha do back-end|Contagem|Total|A contagem de todas as atualizações de cópia de bits iniciadas pelo back-end com falha.|Nenhuma dimensão|
|c2d.twin.update.size|Tamanho das atualizações de cópia do back-end|Bytes|Média|O tamanho médio, mínimo e máximo de todas as atualizações de cópia do back-end iniciadas com êxito.|Nenhuma dimensão|
|twinQueries.success|Consultas de entrelaçamento bem-sucedidas|Contagem|Total|A contagem de todas as consultas de myup com êxito.|Nenhuma dimensão|
|twinQueries.failure|Consultas de entrelaçamento com falha|Contagem|Total|A contagem de todas as consultas de entrelaçamento com falha.|Nenhuma dimensão|
|twinQueries.resultSize|Tamanho do resultado de consultas de entrelaçamento|Bytes|Média|A média, o mínimo e o máximo do tamanho do resultado de todas as consultas de myup com êxito.|Nenhuma dimensão|
|jobs.createTwinUpdateJob.success|Criações bem-sucedidas de trabalhos de atualização de entrelaçamento|Contagem|Total|A contagem de todas as criações de trabalhos de atualização de entrelaçamento com êxito.|Nenhuma dimensão|
|jobs.createTwinUpdateJob.failure|Criações com falha de trabalhos de atualização de entrelaçamento|Contagem|Total|A contagem de todas as falhas na criação de trabalhos de atualização de entrelaçamento.|Nenhuma dimensão|
|jobs.createDirectMethodJob.success|Criações de trabalhos de invocação de método com êxito|Contagem|Total|A contagem de toda a criação bem-sucedida de trabalhos de invocação de método direto.|Nenhuma dimensão|
|jobs.createDirectMethodJob.failure|Criações com falha de trabalhos de invocação de método|Contagem|Total|A contagem de todas as falhas na criação de trabalhos de invocação de método direto.|Nenhuma dimensão|
|jobs.listJobs.success|Chamadas com êxito para listar trabalhos|Contagem|Total|A contagem de todas as chamadas bem-sucedidas para listar trabalhos.|Nenhuma dimensão|
|jobs.listJobs.failure|Chamadas com falha para listar trabalhos|Contagem|Total|A contagem de todas as chamadas com falha para listar trabalhos.|Nenhuma dimensão|
|jobs.cancelJob.success|Cancelamentos de trabalho bem-sucedidos|Contagem|Total|A contagem de todas as chamadas bem-sucedidas para cancelar um trabalho.|Nenhuma dimensão|
|jobs.cancelJob.failure|Cancelamentos de trabalho com falha|Contagem|Total|A contagem de todas as chamadas com falha para cancelar um trabalho.|Nenhuma dimensão|
|jobs.queryJobs.success|Consultas de trabalho com êxito|Contagem|Total|A contagem de todas as chamadas bem-sucedidas para os trabalhos de consulta.|Nenhuma dimensão|
|jobs.queryJobs.failure|Consultas de trabalho com falha|Contagem|Total|A contagem de todas as chamadas com falha para trabalhos de consulta.|Nenhuma dimensão|
|jobs.completed|Trabalhos concluídos|Contagem|Total|A contagem de todos os trabalhos concluídos.|Nenhuma dimensão|
|jobs.failed|Trabalhos com falha|Contagem|Total|A contagem de todos os trabalhos com falha.|Nenhuma dimensão|
|d2c.telemetry.ingress.sendThrottle|Número de erros de limitação|Contagem|Total|Número de erros de limitação devido a aceleradores de taxa de transferência do dispositivo|Nenhuma dimensão|
|dailyMessageQuotaUsed|Número total de mensagens usadas|Contagem|Média|Número total de mensagens usadas hoje. Esse é um valor cumulativo que é redefinido para zero às 00:00 UTC todos os dias.|Nenhuma dimensão|
|deviceDataUsage|Uso total de dados do dispositivo|Bytes|Total|Bytes transferidos de e para todos os dispositivos conectados ao IotHub|Nenhuma dimensão|
|totalDeviceCount|Total de dispositivos (visualização)|Contagem|Média|Número de dispositivos registrados no Hub IoT|Nenhuma dimensão|
|connectedDeviceCount|Dispositivos conectados (visualização)|Contagem|Média|Número de dispositivos conectados ao seu hub IoT|Nenhuma dimensão|
|Figura|Métricas de configuração|Contagem|Total|Métricas para operações de configuração|Nenhuma dimensão|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RegistrationAttempts|Tentativas de registro|Contagem|Total|Número de tentativas de registro de dispositivo|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Dispositivos atribuídos|Contagem|Total|Número de dispositivos atribuídos a um hub IoT|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Tentativas de atestado|Contagem|Total|Número de tentativas de atestado de dispositivo|ProvisioningServiceName, status, protocolo|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AvailableStorage|Armazenamento disponível|Bytes|Total|Armazenamento total disponível relatado à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|CassandraConnectionClosures|Fechamentos de conexão do Cassandra|Contagem|Total|Número de conexões Cassandra que foram fechadas, relatadas a uma granularidade de 1 minuto|Região, ClosureReason|
|CassandraRequestCharges|Encargos de solicitação do Cassandra|Contagem|Total|RUs consumido para solicitações Cassandra feitas|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Solicitações Cassandra|Contagem|Contagem|Número de solicitações Cassandra feitas|DatabaseName, CollectionName, região, OperationType, ResourceType, ErrorCode|
|DataUsage|Utilização de Dados|Bytes|Total|Uso total de dados relatados à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|DocumentCount|Contagem de documentos|Contagem|Total|Contagem total de documentos relatados à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|DocumentQuota|Cota de documentos|Bytes|Total|Cota de armazenamento total relatada à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|IndexUsage|Uso do índice|Bytes|Total|Uso total do índice relatado à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|MetadataRequests|Solicitações de metadados|Contagem|Contagem|Contagem de solicitações de metadados. Cosmos DB mantém a coleção de metadados do sistema para cada conta, que permite enumerar coleções, bancos de dados, etc., e suas configurações, gratuitamente.|DatabaseName, CollectionName, região, StatusCode, |
|MongoRequestCharge|Encargo de solicitação do Mongo|Contagem|Total|Unidades de solicitação Mongo consumidas|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|MongoRequests|Solicitações Mongo|Contagem|Contagem|Número de solicitações Mongo feitas|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|ProvisionedThroughput|Débito Aprovisionado|Contagem|Máximo|Débito Aprovisionado|DatabaseName, CollectionName|
|ReplicationLatency|Latência de replicação P99|Milissegundos|Média|Latência de replicação P99 nas regiões de origem e de destino para a conta habilitada para Geografia|SourceRegion, TargetRegion|
|Indisponibilidade|Disponibilidade do serviço|Percentagem|Média|Disponibilidade de solicitações de conta em uma hora, granularidade de dia ou mês|Nenhuma dimensão|
|TotalRequestUnits|Total de unidades de solicitação|Contagem|Total|Unidades de solicitação consumidas|DatabaseName, CollectionName, região, StatusCode, OperationType|
|TotalRequests|Total de Pedidos|Contagem|Contagem|Número de solicitações feitas|DatabaseName, CollectionName, região, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Nenhuma dimensão|
|PublishFailCount|Publicar eventos com falha|Contagem|Total|Falha no total de eventos ao publicar neste tópico|ErrorType, erro|
|UnmatchedEventCount|Eventos sem correspondência|Contagem|Total|Total de eventos que não correspondem a nenhuma das assinaturas de evento deste tópico|Nenhuma dimensão|
|PublishSuccessLatencyInMs|Latência de êxito na publicação|Contagem|Total|Latência de êxito de publicação em milissegundos|Nenhuma dimensão|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|MatchedEventCount|Eventos correspondentes|Contagem|Total|Total de eventos correspondidos a esta assinatura de evento|Nenhuma dimensão|
|DeliveryAttemptFailCount|Eventos com falha na entrega|Contagem|Total|O total de eventos não pôde ser entregue a esta assinatura de evento|Erro, ErrorType|
|DeliverySuccessCount|Eventos entregues|Contagem|Total|Total de eventos entregues a esta assinatura de evento|Nenhuma dimensão|
|DestinationProcessingDurationInMs|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|Nenhuma dimensão|
|DroppedEventCount|Eventos ignorados|Contagem|Total|Total de eventos descartados correspondentes a esta assinatura de evento|DropReason|
|DeadLetteredCount|Eventos inativos|Contagem|Total|Total de eventos mortos inativos correspondentes a esta assinatura de evento|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Nenhuma dimensão|
|PublishFailCount|Eventos com falha|Contagem|Total|Falha no total de eventos ao publicar neste tópico|ErrorType, erro|
|UnmatchedEventCount|Eventos sem correspondência|Contagem|Total|Total de eventos que não correspondem a nenhuma das assinaturas de evento deste tópico|Nenhuma dimensão|
|PublishSuccessLatencyInMs|Latência de êxito na publicação|Contagem|Total|Latência de êxito de publicação em milissegundos|Nenhuma dimensão|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfulRequests|Solicitações bem-sucedidas|Contagem|Total|Solicitações bem-sucedidas para o Microsoft. EventHub.|EntityName |
|ServerErrors|Erros do servidor.|Contagem|Total|Erros de servidor para Microsoft. EventHub.|EntityName |
|UserErrors|Erros do usuário.|Contagem|Total|Erros de usuário para Microsoft. EventHub.|EntityName |
|QuotaExceededErrors|A cota excedeu erros.|Contagem|Total|A cota excedeu erros para o Microsoft. EventHub.|EntityName |
|ThrottledRequests|Solicitações limitadas.|Contagem|Total|Solicitações limitadas para Microsoft. EventHub.|EntityName |
|IncomingRequests|Solicitações de entrada|Contagem|Total|Solicitações de entrada para o Microsoft. EventHub.|EntityName|
|IncomingMessages|Mensagens de entrada|Contagem|Total|Mensagens de entrada para o Microsoft. EventHub.|EntityName|
|OutgoingMessages|Mensagens de saída|Contagem|Total|Mensagens de saída para o Microsoft. EventHub.|EntityName|
|IncomingBytes|Bytes de entrada.|Bytes|Total|Bytes de entrada para o Microsoft. EventHub.|EntityName|
|OutgoingBytes|Bytes de saída.|Bytes|Total|Bytes de saída para o Microsoft. EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Contagem|Média|Total de conexões ativas para o Microsoft. EventHub.|Nenhuma dimensão|
|ConnectionsOpened|Conexões abertas.|Contagem|Média|Conexões abertas para Microsoft. EventHub.|EntityName|
|ConnectionsClosed|Conexões fechadas.|Contagem|Média|Conexões fechadas para o Microsoft. EventHub.|EntityName|
|CaptureBacklog|Capturar registro posterior.|Contagem|Total|Capturar pendências para Microsoft. EventHub.|EntityName|
|CapturedMessages|Mensagens capturadas.|Contagem|Total|Mensagens capturadas para o Microsoft. EventHub.|EntityName|
|CapturedBytes|Bytes capturados.|Bytes|Total|Bytes capturados para o Microsoft. EventHub.|EntityName|
|Tamanho|Tamanho|Bytes|Média|Tamanho de um EventHub em bytes.|EntityName|
|INREQS|Solicitações de entrada (preteridas)|Contagem|Total|Total de solicitações de envio de entrada para um namespace (preterido)|Nenhuma dimensão|
|SUCCREQ|Solicitações com êxito (preteridas)|Contagem|Total|Total de solicitações bem-sucedidas para um namespace (preterido)|Nenhuma dimensão|
|FAILREQ|Solicitações com falha (preteridas)|Contagem|Total|Total de solicitações com falha para um namespace (preterido)|Nenhuma dimensão|
|SVRBSY|Erros do servidor ocupado (preterido)|Contagem|Total|Total de erros do servidor ocupado para um namespace (preterido)|Nenhuma dimensão|
|INTERR|Erros de servidor interno (preterido)|Contagem|Total|Total de erros internos do servidor para um namespace (preterido)|Nenhuma dimensão|
|MISCERR|Outros erros (preterido)|Contagem|Total|Total de solicitações com falha para um namespace (preterido)|Nenhuma dimensão|
|INMSGS|Mensagens de entrada (preteridas) (preteridas)|Contagem|Total|Total de mensagens de entrada para um namespace. Essa métrica foi preterida. Use a métrica de mensagens de entrada em vez disso (preterido)|Nenhuma dimensão|
|EHINMSGS|Mensagens de entrada (preteridas)|Contagem|Total|Total de mensagens de entrada para um namespace (preterido)|Nenhuma dimensão|
|Mensagens inenviadas|Mensagens de saída (preteridas) (preteridas)|Contagem|Total|Total de mensagens de saída para um namespace. Essa métrica foi preterida. Use a métrica de mensagens de saída em vez disso (preterido)|Nenhuma dimensão|
|EHOUTMSGS|Mensagens de saída (preteridas)|Contagem|Total|Total de mensagens de saída para um namespace (preterido)|Nenhuma dimensão|
|EHINMBS|Bytes de entrada (preterido) (preterido)|Bytes|Total|Taxa de transferência de mensagem de entrada do hub de eventos para um namespace. Essa métrica foi preterida. Use a métrica de bytes de entrada em vez disso (preterido)|Nenhuma dimensão|
|EHINBYTES|Bytes de entrada (preterido)|Bytes|Total|Taxa de transferência de mensagem de entrada do hub de eventos para um namespace (preterido)|Nenhuma dimensão|
|EHOUTMBS|Bytes de saída (preteridos) (preterido)|Bytes|Total|Taxa de transferência de mensagem de saída do hub de eventos para um namespace. Essa métrica foi preterida. Use a métrica de bytes de saída em vez disso (preterido)|Nenhuma dimensão|
|EHOUTBYTES|Bytes de saída (preteridos)|Bytes|Total|Taxa de transferência de mensagem de saída do hub de eventos para um namespace (preterido)|Nenhuma dimensão|
|EHABL|Arquivar mensagens de pendências (preterido)|Contagem|Total|Mensagens de arquivamento do hub de eventos no backlog para um namespace (preterido)|Nenhuma dimensão|
|EHAMSGS|Arquivar mensagens (preterido)|Contagem|Total|Mensagens arquivadas do hub de eventos em um namespace (preterido)|Nenhuma dimensão|
|EHAMBS|Taxa de transferência de mensagem de arquivamento (preterido)|Bytes|Total|Taxa de transferência de mensagem arquivada do hub de eventos em um namespace (preterido)|Nenhuma dimensão|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfulRequests|Solicitações com êxito (versão prévia)|Contagem|Total|Solicitações bem-sucedidas para o Microsoft. EventHub. (Pré-visualização)|Nenhuma dimensão|
|ServerErrors|Erros do servidor. (Pré-visualização)|Contagem|Total|Erros de servidor para Microsoft. EventHub. (Pré-visualização)|Nenhuma dimensão|
|UserErrors|Erros do usuário. (Pré-visualização)|Contagem|Total|Erros de usuário para Microsoft. EventHub. (Pré-visualização)|Nenhuma dimensão|
|QuotaExceededErrors|A cota excedeu erros. (Pré-visualização)|Contagem|Total|A cota excedeu erros para o Microsoft. EventHub. (Pré-visualização)|Nenhuma dimensão|
|ThrottledRequests|Solicitações limitadas. (Pré-visualização)|Contagem|Total|Solicitações limitadas para Microsoft. EventHub. (Pré-visualização)|Nenhuma dimensão|
|IncomingRequests|Solicitações de entrada (versão prévia)|Contagem|Total|Solicitações de entrada para o Microsoft. EventHub. (Pré-visualização)|Nenhuma dimensão|
|IncomingMessages|Mensagens de entrada (versão prévia)|Contagem|Total|Mensagens de entrada para o Microsoft. EventHub. (Pré-visualização)|Nenhuma dimensão|
|OutgoingMessages|Mensagens de saída (versão prévia)|Contagem|Total|Mensagens de saída para o Microsoft. EventHub. (Pré-visualização)|Nenhuma dimensão|
|IncomingBytes|Bytes de entrada. (Pré-visualização)|Bytes|Total|Bytes de entrada para o Microsoft. EventHub. (Pré-visualização)|Nenhuma dimensão|
|OutgoingBytes|Bytes de saída. (Pré-visualização)|Bytes|Total|Bytes de saída para o Microsoft. EventHub. (Pré-visualização)|Nenhuma dimensão|
|ActiveConnections|ActiveConnections (visualização)|Contagem|Média|Total de conexões ativas para o Microsoft. EventHub. (Pré-visualização)|Nenhuma dimensão|
|ConnectionsOpened|Conexões abertas. (Pré-visualização)|Contagem|Média|Conexões abertas para Microsoft. EventHub. (Pré-visualização)|Nenhuma dimensão|
|ConnectionsClosed|Conexões fechadas. (Pré-visualização)|Contagem|Média|Conexões fechadas para o Microsoft. EventHub. (Pré-visualização)|Nenhuma dimensão|
|CaptureBacklog|Capturar registro posterior. (Pré-visualização)|Contagem|Total|Capturar pendências para Microsoft. EventHub. (Pré-visualização)|Nenhuma dimensão|
|CapturedMessages|Mensagens capturadas. (Pré-visualização)|Contagem|Total|Mensagens capturadas para o Microsoft. EventHub. (Pré-visualização)|Nenhuma dimensão|
|CapturedBytes|Bytes capturados. (Pré-visualização)|Bytes|Total|Bytes capturados para o Microsoft. EventHub. (Pré-visualização)|Nenhuma dimensão|
|CPU|CPU (versão prévia)|Percentagem|Máximo|Utilização da CPU para o cluster do hub de eventos como uma porcentagem|Função|
|AvailableMemory|Memória disponível (versão prévia)|Contagem|Máximo|Memória disponível para o cluster do hub de eventos em bytes|Função|

## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/clusters

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|GatewayRequests|Solicitações de gateway|Contagem|Total|Número de solicitações de gateway|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Solicitações de gateway categorizadas|Contagem|Total|Número de solicitações de gateway por categorias (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Número de trabalhadores ativos|Contagem|Máximo|Número de trabalhadores ativos|ClusterDnsName, Metricname|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ObservedMetricValue|Valor de métrica observado|Contagem|Média|O valor calculado por autoescala quando executado|MetricTriggerSource|
|MetricThreshold|Limite de métrica|Contagem|Média|O limite de dimensionamento automático configurado quando o dimensionamento automático foi executado.|MetricTriggerRule|
|ObservedCapacity|Capacidade observada|Contagem|Média|A capacidade relatada para dimensionamento automático quando executada.|Nenhuma dimensão|
|ScaleActionsInitiated|Ações de escala iniciadas|Contagem|Total|A direção da operação de dimensionamento.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Visualização pública)

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Disponibilidade|Percentagem|Média|Percentual de testes de disponibilidade concluídos com êxito|availabilityResult/nome, availabilityResult/local|
|availabilityResults/contagem|Testes de disponibilidade|Contagem|Contagem|Contagem de testes de disponibilidade|availabilityResult/nome, availabilityResult/local, availabilityResult/êxito|
|availabilityResults/duration|Duração do teste de disponibilidade|Milissegundos|Média|Duração do teste de disponibilidade|availabilityResult/nome, availabilityResult/local, availabilityResult/êxito|
|browserTimings/networkDuration|Tempo de conexão de rede de carregamento de página|Milissegundos|Média|Tempo entre a solicitação de usuário e a conexão de rede. Inclui a pesquisa de DNS e a conexão de transporte.|Nenhuma dimensão|
|browserTimings/processingDuration|Tempo de processamento do cliente|Milissegundos|Média|Tempo entre o recebimento do último byte de um documento até que o DOM seja carregado. As solicitações assíncronas ainda podem estar sendo processadas.|Nenhuma dimensão|
|browserTimings/receiveDuration|Tempo de resposta de recebimento|Milissegundos|Média|Tempo entre o primeiro e o último byte, ou até a desconexão.|Nenhuma dimensão|
|browserTimings/sendDuration|Enviar tempo de solicitação|Milissegundos|Média|Tempo entre a conexão de rede e o recebimento do primeiro byte.|Nenhuma dimensão|
|browserTimings/totalDuration|Tempo de carregamento da página do navegador|Milissegundos|Média|Tempo desde a solicitação do usuário até que DOM, folhas de estilo, scripts e imagens sejam carregados.|Nenhuma dimensão|
|dependências/contagem|Chamadas de dependência|Contagem|Contagem|Contagem de chamadas feitas pelo aplicativo para recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/êxito, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|dependências/duração|Duração da dependência|Milissegundos|Média|Duração das chamadas feitas pelo aplicativo para recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/êxito, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|dependências/com falha|Falhas de chamada de dependência|Contagem|Contagem|Contagem de chamadas de dependência com falha feitas pelo aplicativo para recursos externos.|dependência/tipo, dependência/performanceBucket, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|pageViews/contagem|Exibições de página|Contagem|Contagem|Contagem de exibições de página.|operação/sintética|
|pageViews/duração|Tempo de carregamento da exibição de página|Milissegundos|Média|Tempo de carregamento da exibição de página|operação/sintética|
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
|solicitações/com falha|Pedidos falhados|Contagem|Contagem|Contagem de solicitações HTTP marcadas como com falha. Na maioria dos casos, essas são solicitações com um código de resposta > = 400 e não iguais a 401.|solicitação/performanceBucket, solicitação/resultCode, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|solicitações/taxa|Taxa de solicitações do servidor|CountPerSecond|Média|Taxa de solicitações de servidor por segundo|solicitação/performanceBucket, solicitação/resultCode, operação/sintética, nuvem/roleInstance, solicitação/êxito, nuvem/roleName|
|exceções/contagem|Exceções|Contagem|Contagem|Contagem combinada de todas as exceções não capturadas.|Cloud/roleName, nuvem/roleInstance, cliente/tipo|
|exceções/navegador|Exceções de browser|Contagem|Contagem|Contagem de exceções não capturadas lançadas no navegador.|Nenhuma dimensão|
|exceções/servidor|Exceções de servidor|Contagem|Contagem|Contagem de exceções não capturadas lançadas no aplicativo de servidor.|Cloud/roleName, nuvem/roleInstance|
|rastreamentos/contagem|Rastreamentos|Contagem|Contagem|Contagem de documentos de rastreamento|Trace/nível, operação/sintético, nuvem/roleName, nuvem/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServiceApiHit|Total de acertos da API de serviço|Contagem|Contagem|Número total de acertos da API de serviço|ActivityType, ActivityName|
|ServiceApiLatency|Latência geral da API de serviço|Milissegundos|Média|Latência geral de solicitações de API de serviço|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Total de resultados da API de serviço|Contagem|Contagem|Número total de resultados da API de serviço|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/clusters

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CacheUtilization|Utilização de cache|Percentagem|Média|Nível de utilização no escopo do cluster|Nenhum|
|QueryDuration|Duração da consulta|Milissegundos|Média|Duração das consultas em segundos|Estado de consulta|
|IngestionUtilization|Utilização de ingestão|Percentagem|Média|Taxa de Slots de ingestão usados no cluster|Nenhum|
|KeepAlive|Keep Alive|Contagem|Média|Verificação de sanidade indica que o cluster responde às consultas|Nenhum|
|IngestionVolumeInMB|Volume de ingestão (em MB)|Contagem|Total|Volume geral de dados ingeridos para o cluster (em MB)|Base de Dados|
|IngestionLatencyInSeconds|Latência de ingestão (em segundos)|Segundos|Média|O tempo de ingestão da origem (por exemplo, a mensagem está no EventHub) para o cluster em segundos|Nenhum|
|EventProcessedForEventHubs|Eventos processados (para hubs de eventos)|Contagem|Total|Número de eventos processados pelo cluster ao ingerir o Hub de eventos|Nenhum|
|IngestionResult|Resultado da ingestão|Contagem|Contagem|Número de operações de ingestão|Estado|
|CPU|CPU|Percentagem|Média|Nível de utilização da CPU|Nenhum|
| ContinuousExportNumOfRecordsExported | Número de registros exportados na exportação contínua | Contagem | Total | Número de registros exportados para cada artefato de armazenamento gravado durante a operação de exportação  | Nenhum |
| ExportUtilization | Utilização da exportação | Percentagem | Máximo | Utilização da exportação | Nenhum |
| ContinuousExportPendingCount | Contagem de exportação contínua pendente | Contagem | Máximo | O número de trabalhos de exportação contínuos pendentes prontos para execução | Nenhum |
| ContinuousExportMaxLatenessMinutes | Minutos de atraso máximo de exportação contínua | Contagem | Máximo | O tempo máximo em minutos de todas as exportações contínuas que estão pendentes e prontas para execução | Nenhum |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Utilização|Utilização|Contagem|Contagem|Contagem de chamadas de API|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RunsStarted|Execuções iniciadas|Contagem|Total|Número de execuções de fluxo de trabalho iniciadas.|Nenhuma dimensão|
|RunsCompleted|Execuções concluídas|Contagem|Total|Número de execuções de fluxo de trabalho concluídas.|Nenhuma dimensão|
|RunsSucceeded|Execuções com êxito|Contagem|Total|Número de execuções de fluxo de trabalho bem-sucedidas.|Nenhuma dimensão|
|RunsFailed|Execuções com falha|Contagem|Total|Número de execuções de fluxo de trabalho com falha.|Nenhuma dimensão|
|RunsCancelled|Execuções canceladas|Contagem|Total|Número de execuções de fluxo de trabalho canceladas.|Nenhuma dimensão|
|RunLatency|Latência de execução|Segundos|Média|Latência de execuções de fluxo de trabalho concluídas.|Nenhuma dimensão|
|RunSuccessLatency|Latência de execução bem-sucedida|Segundos|Média|Latência de execuções de fluxo de trabalho bem-sucedidas.|Nenhuma dimensão|
|RunThrottledEvents|Executar eventos limitados|Contagem|Total|Número de ações de fluxo de trabalho ou eventos restringidos de gatilho.|Nenhuma dimensão|
|RunFailurePercentage|Percentual de falha de execução|Percentagem|Total|Falha na porcentagem de execuções de fluxo de trabalho.|Nenhuma dimensão|
|ActionsStarted|Ações iniciadas |Contagem|Total|Número de ações de fluxo de trabalho iniciadas.|Nenhuma dimensão|
|ActionsCompleted|Ações concluídas |Contagem|Total|Número de ações de fluxo de trabalho concluídas.|Nenhuma dimensão|
|ActionsSucceeded|Ações com êxito |Contagem|Total|Número de ações de fluxo de trabalho bem-sucedidas.|Nenhuma dimensão|
|ActionsFailed|Ações com falha|Contagem|Total|Número de ações de fluxo de trabalho com falha.|Nenhuma dimensão|
|ActionsSkipped|Ações ignoradas |Contagem|Total|Número de ações de fluxo de trabalho ignoradas.|Nenhuma dimensão|
|ActionLatency|Latência da ação |Segundos|Média|Latência de ações de fluxo de trabalho concluídas.|Nenhuma dimensão|
|ActionSuccessLatency|Latência de êxito da ação |Segundos|Média|Latência de ações de fluxo de trabalho bem-sucedidas.|Nenhuma dimensão|
|ActionThrottledEvents|Eventos com restrição de ação|Contagem|Total|Número de eventos restritos da ação de fluxo de trabalho.|Nenhuma dimensão|
|TriggersStarted|Gatilhos iniciados |Contagem|Total|Número de gatilhos de fluxo de trabalho iniciados.|Nenhuma dimensão|
|TriggersCompleted|Gatilhos concluídos |Contagem|Total|Número de gatilhos de fluxo de trabalho concluídos.|Nenhuma dimensão|
|TriggersSucceeded|Gatilhos com êxito |Contagem|Total|Número de gatilhos de fluxo de trabalho bem-sucedidos.|Nenhuma dimensão|
|TriggersFailed|Gatilhos com falha |Contagem|Total|Número de gatilhos de fluxo de trabalho com falha.|Nenhuma dimensão|
|TriggersSkipped|Gatilhos ignorados|Contagem|Total|Número de gatilhos de fluxo de trabalho ignorados.|Nenhuma dimensão|
|TriggersFired|Gatilhos acionados |Contagem|Total|Número de gatilhos de fluxo de trabalho acionados.|Nenhuma dimensão|
|TriggerLatency|Latência de gatilho |Segundos|Média|Latência de gatilhos de fluxo de trabalho concluídos.|Nenhuma dimensão|
|TriggerFireLatency|Latência de disparo de gatilho |Segundos|Média|Latência de gatilhos de fluxo de trabalho acionados.|Nenhuma dimensão|
|TriggerSuccessLatency|Latência de êxito do gatilho |Segundos|Média|Latência de gatilhos de fluxo de trabalho bem-sucedidos.|Nenhuma dimensão|
|TriggerThrottledEvents|Disparar eventos restringidos|Contagem|Total|Número de eventos restringidos do gatilho de fluxo de trabalho.|Nenhuma dimensão|
|BillableActionExecutions|Execuções de ações faturáveis|Contagem|Total|Número de execuções de ação de fluxo de trabalho sendo faturadas.|Nenhuma dimensão|
|BillableTriggerExecutions|Execuções de gatilho Faturável|Contagem|Total|Número de execuções de gatilho de fluxo de trabalho sendo cobradas.|Nenhuma dimensão|
|TotalBillableExecutions|Total de execuções faturáveis|Contagem|Total|Número de execuções de fluxo de trabalho sendo cobradas.|Nenhuma dimensão|
|BillingUsageNativeOperation|Uso de cobrança para execuções de operação nativa|Contagem|Total|Número de execuções de operações nativas sendo cobradas.|Nenhuma dimensão|
|BillingUsageStandardConnector|Uso de cobrança para execuções de conector padrão|Contagem|Total|Número de execuções de conector padrão sendo cobradas.|Nenhuma dimensão|
|BillingUsageStorageConsumption|Uso de cobrança para execuções de consumo de armazenamento|Contagem|Total|Número de execuções de consumo de armazenamento sendo cobradas.|Nenhuma dimensão|
|BillingUsageNativeOperation|Uso de cobrança para execuções de operação nativa|Contagem|Total|Número de execuções de operações nativas sendo cobradas.|Nenhuma dimensão|
|BillingUsageStandardConnector|Uso de cobrança para execuções de conector padrão|Contagem|Total|Número de execuções de conector padrão sendo cobradas.|Nenhuma dimensão|
|BillingUsageStorageConsumption|Uso de cobrança para execuções de consumo de armazenamento|Contagem|Total|Número de execuções de consumo de armazenamento sendo cobradas.|Nenhuma dimensão|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RunsStarted|Execuções iniciadas|Contagem|Total|Número de execuções de fluxo de trabalho iniciadas.|Nenhuma dimensão|
|RunsCompleted|Execuções concluídas|Contagem|Total|Número de execuções de fluxo de trabalho concluídas.|Nenhuma dimensão|
|RunsSucceeded|Execuções com êxito|Contagem|Total|Número de execuções de fluxo de trabalho bem-sucedidas.|Nenhuma dimensão|
|RunsFailed|Execuções com falha|Contagem|Total|Número de execuções de fluxo de trabalho com falha.|Nenhuma dimensão|
|RunsCancelled|Execuções canceladas|Contagem|Total|Número de execuções de fluxo de trabalho canceladas.|Nenhuma dimensão|
|RunLatency|Latência de execução|Segundos|Média|Latência de execuções de fluxo de trabalho concluídas.|Nenhuma dimensão|
|RunSuccessLatency|Latência de execução bem-sucedida|Segundos|Média|Latência de execuções de fluxo de trabalho bem-sucedidas.|Nenhuma dimensão|
|RunThrottledEvents|Executar eventos limitados|Contagem|Total|Número de ações de fluxo de trabalho ou eventos restringidos de gatilho.|Nenhuma dimensão|
|RunStartThrottledEvents|Executar eventos restritos de início|Contagem|Total|Número de eventos restritos de início de fluxo de trabalho.|Nenhuma dimensão|
|RunFailurePercentage|Percentual de falha de execução|Percentagem|Total|Falha na porcentagem de execuções de fluxo de trabalho.|Nenhuma dimensão|
|ActionsStarted|Ações iniciadas |Contagem|Total|Número de ações de fluxo de trabalho iniciadas.|Nenhuma dimensão|
|ActionsCompleted|Ações concluídas |Contagem|Total|Número de ações de fluxo de trabalho concluídas.|Nenhuma dimensão|
|ActionsSucceeded|Ações com êxito |Contagem|Total|Número de ações de fluxo de trabalho bem-sucedidas.|Nenhuma dimensão|
|ActionsFailed|Ações com falha |Contagem|Total|Número de ações de fluxo de trabalho com falha.|Nenhuma dimensão|
|ActionsSkipped|Ações ignoradas |Contagem|Total|Número de ações de fluxo de trabalho ignoradas.|Nenhuma dimensão|
|ActionLatency|Latência da ação |Segundos|Média|Latência de ações de fluxo de trabalho concluídas.|Nenhuma dimensão|
|ActionSuccessLatency|Latência de êxito da ação |Segundos|Média|Latência de ações de fluxo de trabalho bem-sucedidas.|Nenhuma dimensão|
|ActionThrottledEvents|Eventos com restrição de ação|Contagem|Total|Número de eventos restritos da ação de fluxo de trabalho.|Nenhuma dimensão|
|TriggersStarted|Gatilhos iniciados |Contagem|Total|Número de gatilhos de fluxo de trabalho iniciados.|Nenhuma dimensão|
|TriggersCompleted|Gatilhos concluídos |Contagem|Total|Número de gatilhos de fluxo de trabalho concluídos.|Nenhuma dimensão|
|TriggersSucceeded|Gatilhos com êxito |Contagem|Total|Número de gatilhos de fluxo de trabalho bem-sucedidos.|Nenhuma dimensão|
|TriggersFailed|Gatilhos com falha |Contagem|Total|Número de gatilhos de fluxo de trabalho com falha.|Nenhuma dimensão|
|TriggersSkipped|Gatilhos ignorados|Contagem|Total|Número de gatilhos de fluxo de trabalho ignorados.|Nenhuma dimensão|
|TriggersFired|Gatilhos acionados |Contagem|Total|Número de gatilhos de fluxo de trabalho acionados.|Nenhuma dimensão|
|TriggerLatency|Latência de gatilho |Segundos|Média|Latência de gatilhos de fluxo de trabalho concluídos.|Nenhuma dimensão|
|TriggerFireLatency|Latência de disparo de gatilho |Segundos|Média|Latência de gatilhos de fluxo de trabalho acionados.|Nenhuma dimensão|
|TriggerSuccessLatency|Latência de êxito do gatilho |Segundos|Média|Latência de gatilhos de fluxo de trabalho bem-sucedidos.|Nenhuma dimensão|
|TriggerThrottledEvents|Disparar eventos restringidos|Contagem|Total|Número de eventos restringidos do gatilho de fluxo de trabalho.|Nenhuma dimensão|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Uso do processador de fluxo de trabalho para Ambiente de Serviço de Integração|Percentagem|Média|Uso do processador do fluxo de trabalho para o ambiente do serviço de integração.|Nenhuma dimensão|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Uso de memória do fluxo de trabalho para Ambiente de Serviço de Integração|Percentagem|Média|Uso de memória do fluxo de trabalho para o ambiente do serviço de integração.|Nenhuma dimensão|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Uso do processador do conector para Ambiente de Serviço de Integração|Percentagem|Média|Uso do processador do conector para o ambiente do serviço de integração.|Nenhuma dimensão|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Uso de memória do conector para Ambiente de Serviço de Integração|Percentagem|Média|Uso de memória do conector para o ambiente do serviço de integração.|Nenhuma dimensão|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Execuções concluídas|Execuções concluídas|Contagem|Total|Número de execuções concluídas com êxito para este espaço de trabalho|Cenário|
|Execuções iniciadas|Execuções iniciadas|Contagem|Total|Número de execuções iniciadas para este espaço de trabalho|Cenário|
|Execuções com falha|Execuções com falha|Contagem|Total|Número de execuções com falha para este espaço de trabalho|Cenário|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Utilização|Utilização|Contagem|Contagem|Contagem de chamadas de API|ApiCategory, ApiName, ResultType, ResponseCode|
|Disponibilidade|Disponibilidade|Percentagem|Média|Disponibilidade das APIs|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AverageOtherLatency|Latência média de outras|MS/op|Média|Média de outra latência (que não é leitura ou gravação) em milissegundos por operação|Nenhuma dimensão|
|AverageReadLatency|Latência média de leitura|MS/op|Média|Média de latência de leitura em milissegundos por operação|Nenhuma dimensão|
|AverageTotalLatency|Latência total média|MS/op|Média|Latência total média em milissegundos por operação|Nenhuma dimensão|
|AverageWriteLatency|Latência média de gravação|MS/op|Média|Latência média de gravação em milissegundos por operação|Nenhuma dimensão|
|FilesystemOtherOps|Outras operações do sistema de arquivos|Ops|Média|Número de outras operações do sistema de arquivos (que não são de leitura ou gravação)|Nenhuma dimensão|
|FilesystemReadOps|Operações de leitura do sistema de arquivos|Ops|Média|Número de operações de leitura do sistema de arquivos|Nenhuma dimensão|
|FilesystemTotalOps|Total de operações do sistema de arquivos|Ops|Média|Soma de todas as operações do sistema de arquivos|Nenhuma dimensão|
|FilesystemWriteOps|Operações de gravação do sistema de arquivos|Ops|Média|Número de operações de gravação do sistema de arquivos|Nenhuma dimensão|
|IoBytesPerOtherOps|Bytes de e/s por outra Ops|bytes/op|Média|Número de bytes de entrada/saída por outras operações (que não são de leitura ou gravação)|Nenhuma dimensão|
|IoBytesPerReadOps|Bytes de e/s por operações de leitura|bytes/op|Média|Número de bytes de entrada/saída por operação de leitura|Nenhuma dimensão|
|IoBytesPerTotalOps|Bytes de e/s por operação em todas as operações|bytes/op|Média|Soma de todas as operações de bytes in/out|Nenhuma dimensão|
|IoBytesPerWriteOps|Bytes de e/s por operações de gravação|bytes/op|Média|Número de bytes de entrada/saída por operação de gravação|Nenhuma dimensão|
|OtherIops|Outro IOPS|operações/segundo|Média|Outra operação de entrada/saída por segundo|Nenhuma dimensão|
|OtherThroughput|Outra taxa de transferência|MBps|Média|Outra taxa de transferência (que não é de leitura ou gravação) em megabytes por segundo|Nenhuma dimensão|
|ReadIops|IOPS de leitura|operações/segundo|Média|Operações de leitura/saída por segundo|Nenhuma dimensão|
|ReadThroughput|Taxa de transferência de leitura|MBps|Média|Taxa de transferência de leitura em megabytes por segundo|Nenhuma dimensão|
|TotalIops|IOPS total|operações/segundo|Média|Soma de todas as operações de entrada/saída por segundo|Nenhuma dimensão|
|TotalThroughput|Taxa de transferência total|MBps|Média|Soma de toda a taxa de transferência em megabytes por segundo|Nenhuma dimensão|
|VolumeAllocatedSize|Tamanho alocado do volume|bytes|Média|Tamanho alocado do volume (não os bytes reais usados)|Nenhuma dimensão|
|VolumeLogicalSize|Tamanho lógico do volume|bytes|Média|Tamanho lógico do volume (bytes usados)|Nenhuma dimensão|
|VolumeSnapshotSize|Tamanho do instantâneo de volume|bytes|Média|Tamanho de todos os instantâneos no volume|Nenhuma dimensão|
|WriteIops|IOPS de gravação|operações/segundo|Média|Operações de gravação/saída por segundo|Nenhuma dimensão|
|WriteThroughput|Taxa de transferência de gravação|MBps|Média|Taxa de transferência de gravação em megabytes por segundo|Nenhuma dimensão|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Tamanho alocado do pool de volumes|bytes|Média|Tamanho alocado do pool (não os bytes reais usados)|Nenhuma dimensão|
|VolumePoolAllocatedUsed|Pool de volumes alocado usado|bytes|Média|Tamanho usado para o pool alocado|Nenhuma dimensão|
|VolumePoolTotalLogicalSize|Tamanho lógico total do pool de volumes|bytes|Média|Soma do tamanho lógico de todos os volumes pertencentes ao pool|Nenhuma dimensão|
|VolumePoolTotalSnapshotSize|Tamanho do instantâneo total do pool de volumes|bytes|Média|Soma de todos os instantâneos no pool|Nenhuma dimensão|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BytesSentRate|Bytes enviados|Contagem|Total|Número de bytes enviados pela interface de rede|Nenhuma dimensão|
|BytesReceivedRate|Bytes recebidos|Contagem|Total|Número de bytes recebidos pela interface de rede|Nenhuma dimensão|
|PacketsSentRate|Pacotes enviados|Contagem|Total|Número de pacotes enviados pela interface de rede|Nenhuma dimensão|
|PacketsReceivedRate|Pacotes recebidos|Contagem|Total|Número de pacotes recebidos pela interface de rede|Nenhuma dimensão|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|VipAvailability|Disponibilidade do caminho de dados|Contagem|Média|Média de disponibilidade de caminho de dados de Load Balancer por duração de tempo|FrontendIPAddress, FrontendPort|
|DipAvailability|Status da investigação de integridade|Contagem|Média|Média Load Balancer status de investigação de integridade por duração de tempo|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Contagem de bytes|Contagem|Total|Número total de bytes transmitidos no período de tempo|FrontendIPAddress, FrontendPort, direção|
|PacketCount|Contagem de pacotes|Contagem|Total|Número total de pacotes transmitidos no período de tempo|FrontendIPAddress, FrontendPort, direção|
|SYNCount|Contagem de SYN|Contagem|Total|Número total de pacotes SYN transmitidos no período de tempo|FrontendIPAddress, FrontendPort, direção|
|SnatConnectionCount|Contagem de conexões SNAT|Contagem|Total|Número total de novas conexões SNAT criadas dentro do período de tempo|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Portas SNAT alocadas (visualização)|Contagem|Total|Número total de portas SNAT alocadas no período de tempo|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Portas SNAT usadas (visualização)|Contagem|Total|Número total de portas SNAT usadas no período de tempo|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueryVolume|Volume de consulta|Contagem|Total|Número de consultas servidas para uma zona DNS|Nenhuma dimensão|
|RecordSetCount|Contagem de conjuntos de registros|Contagem|Máximo|Número de conjuntos de registros em uma zona DNS|Nenhuma dimensão|
|RecordSetCapacityUtilization|Utilização da capacidade do conjunto de registros|Percentagem|Máximo|Porcentagem da capacidade do conjunto de registros utilizada por uma zona DNS|Nenhuma dimensão|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PacketsInDDoS|DDoS de pacotes de entrada|CountPerSecond|Máximo|DDoS de pacotes de entrada|Nenhuma dimensão|
|PacketsDroppedDDoS|DDoS de pacotes de entrada removidos|CountPerSecond|Máximo|DDoS de pacotes de entrada removidos|Nenhuma dimensão|
|PacketsForwardedDDoS|DDoS de pacotes de entrada encaminhados|CountPerSecond|Máximo|DDoS de pacotes de entrada encaminhados|Nenhuma dimensão|
|TCPPacketsInDDoS|DDoS de pacotes TCP de entrada|CountPerSecond|Máximo|DDoS de pacotes TCP de entrada|Nenhuma dimensão|
|TCPPacketsDroppedDDoS|DDoS de pacotes TCP de entrada removidos|CountPerSecond|Máximo|DDoS de pacotes TCP de entrada removidos|Nenhuma dimensão|
|TCPPacketsForwardedDDoS|DDoS de pacotes TCP de entrada encaminhados|CountPerSecond|Máximo|DDoS de pacotes TCP de entrada encaminhados|Nenhuma dimensão|
|UDPPacketsInDDoS|DDoS de pacotes UDP de entrada|CountPerSecond|Máximo|DDoS de pacotes UDP de entrada|Nenhuma dimensão|
|UDPPacketsDroppedDDoS|DDoS de pacotes UDP de entrada removidos|CountPerSecond|Máximo|DDoS de pacotes UDP de entrada removidos|Nenhuma dimensão|
|UDPPacketsForwardedDDoS|DDoS de pacotes UDP de entrada encaminhados|CountPerSecond|Máximo|DDoS de pacotes UDP de entrada encaminhados|Nenhuma dimensão|
|BytesInDDoS|DDoS de bytes de entrada|BytesPerSecond|Máximo|DDoS de bytes de entrada|Nenhuma dimensão|
|BytesDroppedDDoS|DDoS de bytes de entrada eliminados|BytesPerSecond|Máximo|DDoS de bytes de entrada eliminados|Nenhuma dimensão|
|BytesForwardedDDoS|DDoS de bytes de entrada encaminhados|BytesPerSecond|Máximo|DDoS de bytes de entrada encaminhados|Nenhuma dimensão|
|TCPBytesInDDoS|DDoS de bytes de TCP de entrada|BytesPerSecond|Máximo|DDoS de bytes de TCP de entrada|Nenhuma dimensão|
|TCPBytesDroppedDDoS|DDoS de bytes de TCP de entrada eliminados|BytesPerSecond|Máximo|DDoS de bytes de TCP de entrada eliminados|Nenhuma dimensão|
|TCPBytesForwardedDDoS|DDoS de bytes TCP de entrada encaminhados|BytesPerSecond|Máximo|DDoS de bytes TCP de entrada encaminhados|Nenhuma dimensão|
|UDPBytesInDDoS|DDoS de bytes de UDP de entrada|BytesPerSecond|Máximo|DDoS de bytes de UDP de entrada|Nenhuma dimensão|
|UDPBytesDroppedDDoS|DDoS de bytes UDP de entrada eliminados|BytesPerSecond|Máximo|DDoS de bytes UDP de entrada eliminados|Nenhuma dimensão|
|UDPBytesForwardedDDoS|DDoS de bytes de UDP de entrada encaminhados|BytesPerSecond|Máximo|DDoS de bytes de UDP de entrada encaminhados|Nenhuma dimensão|
|IfUnderDDoSAttack|Sob ataque de DDoS ou não|Contagem|Máximo|Sob ataque de DDoS ou não|Nenhuma dimensão|
|DDoSTriggerTCPPackets|Pacotes TCP de entrada para disparar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes TCP de entrada para disparar a mitigação de DDoS|Nenhuma dimensão|
|DDoSTriggerUDPPackets|Pacotes UDP de entrada para disparar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes UDP de entrada para disparar a mitigação de DDoS|Nenhuma dimensão|
|DDoSTriggerSYNPackets|Pacotes de entrada SYN para disparar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes de entrada SYN para disparar a mitigação de DDoS|Nenhuma dimensão|
|VipAvailability|Disponibilidade do caminho de dados|Contagem|Média|Média de disponibilidade de endereço IP por duração de tempo|Porta|
|ByteCount|Contagem de bytes|Contagem|Total|Número total de bytes transmitidos no período de tempo|Porta, direção|
|PacketCount|Contagem de pacotes|Contagem|Total|Número total de pacotes transmitidos no período de tempo|Porta, direção|
|SynCount|Contagem de SYN|Contagem|Total|Número total de pacotes SYN transmitidos no período de tempo|Porta, direção|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ApplicationRuleHit|Contagem de impacto de regras de aplicativo|Contagem|Total|Número de vezes que as regras de aplicativo foram atingidas|Status, motivo, protocolo|
|NetworkRuleHit|Contagem de impacto das regras de rede|Contagem|Total|Número de vezes que as regras de rede foram atingidas|Status, motivo, protocolo|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Débito|Débito|BytesPerSecond|Total|Número de bytes por segundo atendidos pelo gateway de aplicativo|Nenhuma dimensão|
|UnhealthyHostCount|Contagem de hosts não íntegros|Contagem|Média|Número de hosts de back-end não íntegros|BackendSettingsPool|
|HealthyHostCount|Contagem de hosts íntegros|Contagem|Média|Número de hosts de back-end íntegros|BackendSettingsPool|
|TotalRequests|Total de Pedidos|Contagem|Total|Contagem de solicitações bem-sucedidas que o gateway de aplicativo serviu|BackendSettingsPool|
|FailedRequests|Pedidos com Falhas|Contagem|Total|Contagem de solicitações com falha que o gateway de aplicativo serviu|BackendSettingsPool|
|ResponseStatus|Status da resposta|Contagem|Total|Status de resposta http retornado pelo gateway de aplicativo|HttpStatusGroup|
|CurrentConnections|Conexões atuais|Contagem|Total|Contagem de conexões atuais estabelecidas com o gateway de aplicativo|Nenhuma dimensão|
|CapacityUnits|Unidades de capacidade atuais|Contagem|Média|Unidades de capacidade consumidas|Nenhuma dimensão|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AverageBandwidth|Largura de banda S2S do gateway|BytesPerSecond|Média|Largura de banda de site a site média de um gateway em bytes por segundo|Nenhuma dimensão|
|P2SBandwidth|Largura de banda P2S do gateway|BytesPerSecond|Média|Largura de banda de ponto a site média de um gateway em bytes por segundo|Nenhuma dimensão|
|P2SConnectionCount|Contagem de conexões P2S|Contagem|Máximo|Contagem de conexões ponto a site de um gateway|Protocolo|
|TunnelAverageBandwidth|Largura de banda do túnel|BytesPerSecond|Média|Largura de banda média de um túnel em bytes por segundo|ConnectionName, RemoteIP|
|TunnelEgressBytes|Bytes de saída do túnel|Bytes|Total|Bytes de saída de um túnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Bytes de entrada de túnel|Bytes|Total|Bytes de entrada de um túnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Pacotes de saída de túnel|Contagem|Total|Contagem de pacotes de saída de um túnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Pacotes de entrada de túnel|Contagem|Total|Contagem de pacotes de entrada de um túnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Descarte de pacotes incompatíveis TS de saída de túnel|Contagem|Total|Contagem de descarte de pacotes de saída da incompatibilidade de seletor de tráfego de um túnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Descarte de pacotes incompatíveis TS de entrada de túnel|Contagem|Total|Contagem de eliminação de pacotes de entrada da incompatibilidade de seletor de tráfego de um túnel|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits de entrada do Azure por segundo|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits de saída do Azure por segundo|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits de entrada do Azure por segundo|Nenhuma dimensão|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits de saída do Azure por segundo|Nenhuma dimensão|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits de entrada do Azure por segundo|Nenhuma dimensão|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits de saída do Azure por segundo|Nenhuma dimensão|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QpsByEndpoint|Consultas por ponto de extremidade retornadas|Contagem|Total|Número de vezes que um ponto de extremidade do Gerenciador de tráfego foi retornado no período de tempo fornecido|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Status do ponto de extremidade por ponto de extremidade|Contagem|Máximo|1 se o status de investigação de um ponto de extremidade for "habilitado", caso contrário, 0.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ProbesFailedPercent|% De investigações com falha|Percentagem|Média|% de investigações de monitoramento de conectividade com falha|Nenhuma dimensão|
|AverageRoundtripMs|Média de tempo de ida e volta (MS)|Milissegundos|Média|Tempo de ida e volta médio da rede (MS) para investigações de monitoramento de conectividade enviadas entre a origem e o destino|Nenhuma dimensão|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RequestCount|Número de Pedidos|Contagem|Total|O número de solicitações de cliente atendidas pelo proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|Solicitações|Tamanho da solicitação|Bytes|Total|O número de bytes enviados como solicitações de clientes para o proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|Respostas|Tamanho da resposta|Bytes|Total|O número de bytes enviados como respostas do proxy HTTP/S para clientes|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Contagem de solicitações de back-end|Contagem|Total|O número de solicitações enviadas do proxy HTTP/S para back-ends|HttpStatus, HttpStatusGroup, back-end|
|BackendRequestLatency|Latência de solicitação de back-end|Milissegundos|Média|O tempo calculado a partir de quando a solicitação foi enviada pelo proxy HTTP/S para o back-end até que o proxy HTTP/S receba o último byte de resposta do back-end|End|
|TotalLatency|Latência total|Milissegundos|Média|O tempo calculado a partir de quando a solicitação do cliente foi recebida pelo proxy HTTP/S até que o cliente tenha reconhecido o último byte de resposta do proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Percentual de integridade de back-end|Percentagem|Média|A porcentagem de investigações de integridade bem-sucedidas do proxy HTTP/S para back-ends|Back-end, Httpsettings|
|WebApplicationFirewallRequestCount|Contagem de solicitações de firewall do aplicativo Web|Contagem|Total|O número de solicitações de cliente processadas pelo firewall do aplicativo Web|PolicyName, RuleName, ação|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|registration.all|Operações de registro|Contagem|Total|A contagem de todas as operações de registro bem-sucedidas (cria consultas e exclusões de atualizações). |Nenhuma dimensão|
|registration.create|Operações de criação de registro|Contagem|Total|A contagem de todas as criações de registro bem-sucedidas.|Nenhuma dimensão|
|registration.update|Operações de atualização de registro|Contagem|Total|A contagem de todas as atualizações de registro bem-sucedidas.|Nenhuma dimensão|
|registration.get|Operações de leitura de registro|Contagem|Total|A contagem de todas as consultas de registro bem-sucedidas.|Nenhuma dimensão|
|registration.delete|Operações de exclusão de registro|Contagem|Total|A contagem de todas as exclusões de registro bem-sucedidas.|Nenhuma dimensão|
|recebidos|Mensagens de entrada|Contagem|Total|A contagem de todas as chamadas de API de envio bem-sucedidas. |Nenhuma dimensão|
|incoming.scheduled|Notificações por push agendadas enviadas|Contagem|Total|Notificações por push agendadas canceladas|Nenhuma dimensão|
|incoming.scheduled.cancel|Notificações por push agendadas canceladas|Contagem|Total|Notificações por push agendadas canceladas|Nenhuma dimensão|
|agendado. pendente|Notificações agendadas pendentes|Contagem|Total|Notificações agendadas pendentes|Nenhuma dimensão|
|instalação. todos|Operações de gerenciamento de instalação|Contagem|Total|Operações de gerenciamento de instalação|Nenhuma dimensão|
|installation.get|Obter operações de instalação|Contagem|Total|Obter operações de instalação|Nenhuma dimensão|
|installation.upsert|Criar ou atualizar operações de instalação|Contagem|Total|Criar ou atualizar operações de instalação|Nenhuma dimensão|
|installation.patch|Operações de instalação de patch|Contagem|Total|Operações de instalação de patch|Nenhuma dimensão|
|installation.delete|Excluir operações de instalação|Contagem|Total|Excluir operações de instalação|Nenhuma dimensão|
|outgoing.allpns.success|Notificações bem-sucedidas|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Nenhuma dimensão|
|outgoing.allpns.invalidpayload|Erros de carga|Contagem|Total|A contagem de Pushes que falharam porque o PNS retornou um erro de carga inadequada.|Nenhuma dimensão|
|outgoing.allpns.pnserror|Erros do sistema de notificação externa|Contagem|Total|A contagem de Pushes que falharam porque houve um problema de comunicação com o PNS (exclui problemas de autenticação).|Nenhuma dimensão|
|outgoing.allpns.channelerror|Erros de canal|Contagem|Total|A contagem de Pushes que falharam porque o canal era inválido não associado ao aplicativo correto limitado ou expirou.|Nenhuma dimensão|
|outgoing.allpns.badorexpiredchannel|Erros de canal insatisfatórios ou expirados|Contagem|Total|A contagem de Pushes que falharam porque o canal/token/RegistrationId no registro expirou ou é inválido.|Nenhuma dimensão|
|outgoing.wns.success|Notificações com êxito do WNS|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Nenhuma dimensão|
|outgoing.wns.invalidcredentials|Erros de autorização do WNS (credenciais inválidas)|Contagem|Total|A contagem de Pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas. (O Windows Live não reconhece as credenciais).|Nenhuma dimensão|
|outgoing.wns.badchannel|Erro de canal insatisfatório do WNS|Contagem|Total|A contagem de Pushes que falharam porque o ChannelURI no registro não foi reconhecido (status do WNS: 404 não encontrado).|Nenhuma dimensão|
|outgoing.wns.expiredchannel|Erro de canal expirado do WNS|Contagem|Total|A contagem de Pushes que falharam porque o ChannelURI está expirado (status do WNS: 410 out).|Nenhuma dimensão|
|outgoing.wns.throttled|Notificações limitadas do WNS|Contagem|Total|A contagem de Pushes que falharam porque o WNS está limitando este aplicativo (status do WNS: 406 não aceitável).|Nenhuma dimensão|
|outgoing.wns.tokenproviderunreachable|Erros de autorização do WNS (inacessível)|Contagem|Total|O Windows Live não está acessível.|Nenhuma dimensão|
|outgoing.wns.invalidtoken|Erros de autorização do WNS (token inválido)|Contagem|Total|O token fornecido ao WNS não é válido (status do WNS: 401 não autorizado).|Nenhuma dimensão|
|outgoing.wns.wrongtoken|Erros de autorização do WNS (token incorreto)|Contagem|Total|O token fornecido ao WNS é válido, mas para outro aplicativo (status do WNS: 403 Proibido). Isso pode acontecer se o ChannelURI no registro estiver associado a outro aplicativo. Verifique se o aplicativo cliente está associado ao mesmo aplicativo cujas credenciais estão no Hub de notificação.|Nenhuma dimensão|
|outgoing.wns.invalidnotificationformat|Formato de notificação inválido do WNS|Contagem|Total|O formato da notificação é inválido (status do WNS: 400). Observe que o WNS não rejeita todas as cargas inválidas.|Nenhuma dimensão|
|outgoing.wns.invalidnotificationsize|Erro de tamanho de notificação inválido do WNS|Contagem|Total|A carga de notificação é muito grande (status do WNS: 413).|Nenhuma dimensão|
|outgoing.wns.channelthrottled|Canal do WNS limitado|Contagem|Total|A notificação foi descartada porque o ChannelURI no registro está limitado (cabeçalho de resposta WNS: X-WNS-NotificationStatus: channelThrottled).|Nenhuma dimensão|
|outgoing.wns.channeldisconnected|Canal do WNS desconectado|Contagem|Total|A notificação foi descartada porque o ChannelURI no registro está limitado (cabeçalho de resposta do WNS: X-WNS-DeviceConnectionStatus: desconectado).|Nenhuma dimensão|
|outgoing.wns.dropped|Notificações descartadas do WNS|Contagem|Total|A notificação foi descartada porque o ChannelURI no registro está limitado (X-WNS-NotificationStatus: descartado, mas não X-WNS-DeviceConnectionStatus: desconectado).|Nenhuma dimensão|
|outgoing.wns.pnserror|Erros de WNS|Contagem|Total|Notificação não entregue devido a erros de comunicação com o WNS.|Nenhuma dimensão|
|outgoing.wns.authenticationerror|Erros de autenticação WNS|Contagem|Total|Notificação não entregue devido a erros de comunicação com credenciais inválidas do Windows Live ou token incorreto.|Nenhuma dimensão|
|outgoing.apns.success|Notificações bem-sucedidas do APNS|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Nenhuma dimensão|
|outgoing.apns.invalidcredentials|Erros de autorização de APNS|Contagem|Total|A contagem de Pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhuma dimensão|
|outgoing.apns.badchannel|Erro de canal insatisfatório de APNS|Contagem|Total|A contagem de Pushes que falharam porque o token é inválido (código de status do protocolo binário APNS: 8. Código de status do protocolo HTTP APNS: 400 com "BadDeviceToken").|Nenhuma dimensão|
|outgoing.apns.expiredchannel|Erro de canal expirado APNS|Contagem|Total|A contagem de tokens que foram invalidados pelo canal de comentários do APNS.|Nenhuma dimensão|
|outgoing.apns.invalidnotificationsize|Erro de tamanho de notificação inválido do APNS|Contagem|Total|A contagem de Pushes que falharam porque o conteúdo era muito grande (código de status do protocolo binário APNS: 7).|Nenhuma dimensão|
|outgoing.apns.pnserror|Erros de APNS|Contagem|Total|A contagem de Pushes que falharam devido a erros de comunicação com o APNS.|Nenhuma dimensão|
|outgoing.gcm.success|Notificações de êxito do GCM|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Nenhuma dimensão|
|outgoing.gcm.invalidcredentials|Erros de autorização do GCM (credenciais inválidas)|Contagem|Total|A contagem de Pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhuma dimensão|
|outgoing.gcm.badchannel|Erro de canal insatisfatório do GCM|Contagem|Total|A contagem de Pushes que falharam porque o RegistrationId no registro não foi reconhecido (resultado do GCM: registro inválido).|Nenhuma dimensão|
|outgoing.gcm.expiredchannel|Erro de canal expirado do GCM|Contagem|Total|A contagem de Pushes que falharam porque o RegistrationId no registro expirou (resultado do GCM: não registrado).|Nenhuma dimensão|
|outgoing.gcm.throttled|Notificações limitadas do GCM|Contagem|Total|A contagem de Pushes que falharam porque o GCM restringiu este aplicativo (código de status do GCM: 501-599 ou resultado: não disponível).|Nenhuma dimensão|
|outgoing.gcm.invalidnotificationformat|Formato de notificação inválido do GCM|Contagem|Total|A contagem de Pushes que falharam porque a carga não foi formatada corretamente (resultado do GCM: InvalidDataKey ou InvalidTtl).|Nenhuma dimensão|
|outgoing.gcm.invalidnotificationsize|Erro de tamanho de notificação inválido do GCM|Contagem|Total|A contagem de Pushes que falharam porque o conteúdo era muito grande (resultado do GCM: MessageTooBig).|Nenhuma dimensão|
|outgoing.gcm.wrongchannel|Erro de canal incorreto do GCM|Contagem|Total|A contagem de Pushes que falharam porque o RegistrationId no registro não está associado ao aplicativo atual (resultado do GCM: InvalidPackageName).|Nenhuma dimensão|
|outgoing.gcm.pnserror|Erros do GCM|Contagem|Total|A contagem de Pushes que falharam devido a erros de comunicação com o GCM.|Nenhuma dimensão|
|outgoing.gcm.authenticationerror|Erros de autenticação do GCM|Contagem|Total|A contagem de Pushes que falharam porque o PNS não aceitou as credenciais fornecidas as credenciais estão bloqueadas ou a SenderId não está configurada corretamente no aplicativo (resultado do GCM: MismatchedSenderId).|Nenhuma dimensão|
|outgoing.mpns.success|Notificações bem-sucedidas do MPNS|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Nenhuma dimensão|
|outgoing.mpns.invalidcredentials|Credenciais inválidas do MPNS|Contagem|Total|A contagem de Pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhuma dimensão|
|outgoing.mpns.badchannel|Erro de canal insatisfatório do MPNS|Contagem|Total|A contagem de Pushes que falharam porque o ChannelURI no registro não foi reconhecido (status do MPNS: 404 não encontrado).|Nenhuma dimensão|
|outgoing.mpns.throttled|Notificações limitadas do MPNS|Contagem|Total|A contagem de Pushes que falharam porque o MPNS está limitando este aplicativo (WNS MPNS: 406 não aceitável).|Nenhuma dimensão|
|outgoing.mpns.invalidnotificationformat|Formato de notificação inválido do MPNS|Contagem|Total|A contagem de Pushes que falharam porque a carga da notificação era muito grande.|Nenhuma dimensão|
|outgoing.mpns.channeldisconnected|Canal do MPNS desconectado|Contagem|Total|A contagem de Pushes que falharam porque o ChannelURI no registro foi desconectado (status do MPNS: 412 não encontrado).|Nenhuma dimensão|
|outgoing.mpns.dropped|Notificações ignoradas do MPNS|Contagem|Total|A contagem de Pushes que foram descartados pelo MPNS (cabeçalho de resposta MPNS: X-NotificationStatus: QueueFull ou suprimido).|Nenhuma dimensão|
|outgoing.mpns.pnserror|Erros do MPNS|Contagem|Total|A contagem de Pushes que falharam devido a erros de comunicação com o MPNS.|Nenhuma dimensão|
|outgoing.mpns.authenticationerror|Erros de autenticação do MPNS|Contagem|Total|A contagem de Pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhuma dimensão|
|notificationhub.pushes|Todas as notificações de saída|Contagem|Total|Todas as notificações de saída do hub de notificação|Nenhuma dimensão|
|incoming.all.requests|Todas as solicitações de entrada|Contagem|Total|Total de solicitações de entrada para um hub de notificação|Nenhuma dimensão|
|incoming.all.failedrequests|Todas as solicitações com falha de entrada|Contagem|Total|Total de solicitações de entrada com falha para um hub de notificação|Nenhuma dimensão|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Average_% de inodes livres|% De inodes livres|Contagem|Média|Average_% de inodes livres|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de espaço livre|% De espaço livre|Contagem|Média|Average_% de espaço livre|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de inodes usados|% De inodes usados|Contagem|Média|Average_% de inodes usados|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de espaço usado|% De espaço utilizado|Contagem|Média|Average_% de espaço usado|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Bytes de leitura de Average_Disk/s|Bytes Lidos de Disco/seg|Contagem|Média|Bytes de leitura de Average_Disk/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Leituras de Average_Disk/s|Leituras de disco/seg|Contagem|Média|Leituras de Average_Disk/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Transferências de Average_Disk/s|As transferências de disco/seg|Contagem|Média|Transferências de Average_Disk/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Bytes de gravação de Average_Disk/s|Bytes Escritos em Disco/seg|Contagem|Média|Bytes de gravação de Average_Disk/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Gravações de Average_Disk/s|Escritas de disco/seg|Contagem|Média|Gravações de Average_Disk/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Free megabytes|Megabytes livres|Contagem|Média|Average_Free megabytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Bytes de disco de Average_Logical/s|Bytes de disco lógico/seg|Contagem|Média|Bytes de disco de Average_Logical/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|% De memória disponível Average_|% De memória disponível|Contagem|Média|% De memória disponível Average_|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de espaço de permuta disponível|% De espaço de permuta disponível|Contagem|Média|Average_% de espaço de permuta disponível|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|% De memória usada Average_|% De memória usada|Contagem|Média|% De memória usada Average_|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de espaço de permuta utilizado|% De espaço de permuta utilizado|Contagem|Média|Average_% de espaço de permuta utilizado|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Memória de Mbytes Average_Available|MBytes de memória disponíveis|Contagem|Média|Memória de Mbytes Average_Available|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Permuta de Average_Available Mbytes|Troca de MBytes disponíveis|Contagem|Média|Permuta de Average_Available Mbytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Leituras de Average_Page/s|Leituras de página/s|Contagem|Média|Leituras de Average_Page/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Gravações de Average_Page/s|Gravações de página/s|Contagem|Média|Gravações de Average_Page/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Pages/s|Páginas/s|Contagem|Média|Average_Pages/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Espaço de permuta de Average_Used Mbytes|Espaço de permuta usado em Mbytes|Contagem|Média|Espaço de permuta de Average_Used Mbytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Mbytes de memória Average_Used|Mbytes de memória usados|Contagem|Média|Mbytes de memória Average_Used|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Bytes de Average_Total transmitidos|Total de Bytes transmitidos|Contagem|Média|Bytes de Average_Total transmitidos|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Bytes de Average_Total recebidos|Total de Bytes recebidos|Contagem|Média|Bytes de Average_Total recebidos|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Bytes de Average_Total|Total de bytes|Contagem|Média|Bytes de Average_Total|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Pacotes de Average_Total transmitidos|Total de pacotes transmitidos|Contagem|Média|Pacotes de Average_Total transmitidos|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total pacotes recebidos|Total de pacotes recebidos|Contagem|Média|Average_Total pacotes recebidos|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Erros de Average_Total RX|Total de erros de RX|Contagem|Média|Erros de Average_Total RX|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total erros de TX|Total de erros TX|Contagem|Média|Average_Total erros de TX|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Colisões de Average_Total|Total de colisões|Contagem|Média|Colisões de Average_Total|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Disco seg/leitura|Média de disco s/leitura|Contagem|Média|Average_Avg. Disco seg/leitura|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Disco seg/transferência|Média de disco s/transferência|Contagem|Média|Average_Avg. Disco seg/transferência|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Disco seg/escritas|Média de disco s/gravação|Contagem|Média|Average_Avg. Disco seg/escritas|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Bytes de disco de Average_Physical/s|Bytes de disco físico/s|Contagem|Média|Bytes de disco de Average_Physical/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Pct tempo privilegiado|Percentual de tempo privilegiado|Contagem|Média|Average_Pct tempo privilegiado|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Pct tempo do usuário|Tempo de usuário do PCT|Contagem|Média|Average_Pct tempo do usuário|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|KBytes de memória Average_Used|KBytes de memória usada|Contagem|Média|KBytes de memória Average_Used|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Virtual memória compartilhada|Memória compartilhada virtual|Contagem|Média|Average_Virtual memória compartilhada|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% tempo de DPC|% De tempo de DPC|Contagem|Média|Average_% tempo de DPC|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de tempo ocioso|% De tempo ocioso|Contagem|Média|Average_% de tempo ocioso|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% tempo de interrupção|% De tempo de interrupção|Contagem|Média|Average_% tempo de interrupção|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de tempo de espera de e/s|% De tempo de espera de e/s|Contagem|Média|Average_% de tempo de espera de e/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de tempo interessante|% De tempo adequado|Contagem|Média|Average_% de tempo interessante|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de tempo privilegiado|% De tempo privilegiado|Contagem|Média|Average_% de tempo privilegiado|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de tempo do processador|% Tempo do processador|Contagem|Média|Average_% de tempo do processador|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Tempo de usuário do Average_%|% De tempo do usuário|Contagem|Média|Tempo de usuário do Average_%|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Memória física Average_Free|Memória física livre|Contagem|Média|Memória física Average_Free|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Espaço de Average_Free em arquivos de paginação|Espaço livre em arquivos de paginação|Contagem|Média|Espaço de Average_Free em arquivos de paginação|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Memória virtual Average_Free|Memória virtual livre|Contagem|Média|Memória virtual Average_Free|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Processes|Processos|Contagem|Média|Average_Processes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Size armazenados em arquivos de paginação|Tamanho armazenado em arquivos de paginação|Contagem|Média|Average_Size armazenados em arquivos de paginação|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Uptime|Tempo de atividade|Contagem|Média|Average_Uptime|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Users|Utilizadores|Contagem|Média|Average_Users|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Disco seg/leitura|Média de disco s/leitura|Contagem|Média|Average_Avg. Disco seg/leitura|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Disco seg/escritas|Média de disco s/gravação|Contagem|Média|Average_Avg. Disco seg/escritas|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Comprimento da fila de disco Average_Current|Comprimento da fila de disco atual|Contagem|Média|Comprimento da fila de disco Average_Current|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Leituras de Average_Disk/s|Leituras de disco/seg|Contagem|Média|Leituras de Average_Disk/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Transferências de Average_Disk/s|As transferências de disco/seg|Contagem|Média|Transferências de Average_Disk/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Gravações de Average_Disk/s|Escritas de disco/seg|Contagem|Média|Gravações de Average_Disk/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Free megabytes|Megabytes livres|Contagem|Média|Average_Free megabytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de espaço livre|% De espaço livre|Contagem|Média|Average_% de espaço livre|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Mbytes de Average_Available|MBytes disponíveis|Contagem|Média|Mbytes de Average_Available|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de bytes confirmados em uso|% Bytes confirmados em uso|Contagem|Média|Average_% de bytes confirmados em uso|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Bytes recebidos/s|Bytes recebidos/seg|Contagem|Média|Average_Bytes recebidos/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Bytes enviados/s|Bytes enviados/seg|Contagem|Média|Average_Bytes enviados/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Total de Average_Bytes/s|Total de bytes/s|Contagem|Média|Total de Average_Bytes/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de tempo do processador|% Tempo do processador|Contagem|Média|Average_% de tempo do processador|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Comprimento da fila de Average_Processor|Comprimento da fila do processador|Contagem|Média|Comprimento da fila de Average_Processor|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Batida|Batida|Contagem|Total|Batida|Computer, OSType, Version, SourceComputerId|
|Atualizar|Atualizar|Contagem|Média|Atualizar|Computador, produto, classificação, UpdateState, opcional, aprovado|
|Evento|Evento|Contagem|Média|Evento|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueryDuration|Duração da consulta|Milissegundos|Média|Duração da consulta DAX no último intervalo|Nenhuma dimensão|
|QueryPoolJobQueueLength|Threads: comprimento da fila de trabalhos do pool de consultas|Contagem|Média|Número de trabalhos na fila do pool de threads de consulta.|Nenhuma dimensão|
|qpu_high_utilization_metric|Alta utilização de QPU|Contagem|Total|QPU alta utilização no último minuto, 1 para alta utilização de QPU, caso contrário 0|Nenhuma dimensão|
|memory_metric|Memória|Bytes|Média|Memória. Intervalo de 0-3 GB para a1, 0-5 GB para a2, 0-10 GB para a3, 0-25 GB para A4, 0-50 GB para a5 e 0-100 GB para a6|Nenhuma dimensão|
|memory_thrashing_metric|Ultrapaginação de memória|Percentagem|Média|Média de ultrapaginação de memória.|Nenhuma dimensão|

## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/namespaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Contagem|Total|ListenerConnections com êxito para Microsoft. Relay.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Contagem|Total|ClientError em ListenerConnections para Microsoft. Relay.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Contagem|Total|ServerError em ListenerConnections para Microsoft. Relay.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Contagem|Total|SenderConnections com êxito para Microsoft. Relay.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Contagem|Total|ClientError em SenderConnections para Microsoft. Relay.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Contagem|Total|ServerError em SenderConnections para Microsoft. Relay.|EntityName|
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
|SearchLatency|Latência de pesquisa|Segundos|Média|Latência de pesquisa média para o serviço de pesquisa|Nenhuma dimensão|
|SearchQueriesPerSecond|Pesquisar consultas por segundo|CountPerSecond|Média|Pesquisar consultas por segundo para o serviço de pesquisa|Nenhuma dimensão|
|ThrottledSearchQueriesPercentage|Percentual de consultas de pesquisa limitadas|Percentagem|Média|Porcentagem de consultas de pesquisa que foram limitadas para o serviço de pesquisa|Nenhuma dimensão|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfulRequests|Solicitações com êxito (versão prévia)|Contagem|Total|Total de solicitações bem-sucedidas para um namespace (versão prévia)|EntityName|
|ServerErrors|Erros do servidor. (Pré-visualização)|Contagem|Total|Erros de servidor para Microsoft. ServiceBus. (Pré-visualização)|EntityName|
|UserErrors|Erros do usuário. (Pré-visualização)|Contagem|Total|Erros de usuário para Microsoft. ServiceBus. (Pré-visualização)|EntityName|
|ThrottledRequests|Solicitações limitadas. (Pré-visualização)|Contagem|Total|Solicitações limitadas para Microsoft. ServiceBus. (Pré-visualização)|EntityName|
|IncomingRequests|Solicitações de entrada (versão prévia)|Contagem|Total|Solicitações de entrada para Microsoft. ServiceBus. (Pré-visualização)|EntityName|
|IncomingMessages|Mensagens de entrada (versão prévia)|Contagem|Total|Mensagens de entrada para Microsoft. ServiceBus. (Pré-visualização)|EntityName|
|OutgoingMessages|Mensagens de saída (versão prévia)|Contagem|Total|Mensagens de saída para Microsoft. ServiceBus. (Pré-visualização)|EntityName|
|ActiveConnections|ActiveConnections (visualização)|Contagem|Total|Total de conexões ativas para Microsoft. ServiceBus. (Pré-visualização)|Nenhuma dimensão|
|Tamanho|Tamanho (versão prévia)|Bytes|Média|Tamanho de uma fila/tópico em bytes. (Pré-visualização)|EntityName|
|Mensagens|Contagem de mensagens em uma fila/tópico. (Pré-visualização)|Contagem|Média|Contagem de mensagens em uma fila/tópico. (Pré-visualização)|EntityName|
|ActiveMessages|Contagem de mensagens ativas em uma fila/tópico. (Pré-visualização)|Contagem|Média|Contagem de mensagens ativas em uma fila/tópico. (Pré-visualização)|EntityName|
|DeadletteredMessages|Contagem de mensagens mortas em uma fila/tópico. (Pré-visualização)|Contagem|Média|Contagem de mensagens mortas em uma fila/tópico. (Pré-visualização)|EntityName|
|ScheduledMessages|Contagem de mensagens agendadas em uma fila/tópico. (Pré-visualização)|Contagem|Média|Contagem de mensagens agendadas em uma fila/tópico. (Pré-visualização)|EntityName|
|CPUXNS|Uso da CPU por namespace|Percentagem|Máximo|Métrica de uso de CPU do namespace Premium do barramento de serviço|Nenhuma dimensão|
|WSXNS|Uso do tamanho da memória por namespace|Percentagem|Máximo|Métrica de uso de memória do namespace Premium do barramento de serviço|Nenhuma dimensão|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Contagem|Média|CPU alocada para este contêiner em milicores|ApplicationName, ServiceName, CodePackageName, ServiceId|
|AllocatedMemory|AllocatedMemory|Bytes|Média|Memória alocada para este contêiner em MB|ApplicationName, ServiceName, CodePackageName, ServiceId|
|ActualCpu|ActualCpu|Contagem|Média|Uso real da CPU em milicores|ApplicationName, ServiceName, CodePackageName, ServiceId|
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
|MessageCount|Contagem de mensagens|Contagem|Total|A quantidade total de mensagens.|Nenhuma dimensão|
|InboundTraffic|Tráfego de Entrada|Bytes|Total|O tráfego de entrada do serviço|Nenhuma dimensão|
|OutboundTraffic|Tráfego de Saída|Bytes|Total|O tráfego de saída do serviço|Nenhuma dimensão|
|UserErrors|Erros do usuário|Percentagem|Máximo|A porcentagem de erros do usuário|Nenhuma dimensão|
|SystemErrors|Erros do sistema|Percentagem|Máximo|A porcentagem de erros do sistema|Nenhuma dimensão|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|allocated_data_storage|Espaço de dados alocado|Bytes|Média|Espaço de dados alocado. Não aplicável a data warehouses.|Nenhuma dimensão|
|app_cpu_billed|CPU do aplicativo cobrada|Contagem|Total|CPU do aplicativo cobrada. Aplica-se a bancos de dados sem servidor.|Nenhuma dimensão|
|app_cpu_percent|Porcentagem de CPU do aplicativo|Percentagem|Média|Porcentagem de CPU do aplicativo. Aplica-se a bancos de dados sem servidor.|Nenhuma dimensão|
|app_memory_percent|Porcentagem de memória usada do aplicativo|Percentagem|Média|Porcentagem de memória usada do aplicativo. Aplica-se a bancos de dados sem servidor.|Nenhuma dimensão|
|blocked_by_firewall|Bloqueado pelo firewall|Contagem|Total|Bloqueado pelo firewall|Nenhuma dimensão|
|cache_hit_percent|Porcentagem de acesso ao cache|Percentagem|Máximo|Porcentagem de acesso ao cache. Aplica-se somente a data warehouses.|Nenhuma dimensão|
|cache_used_percent|Percentual de cache usado|Percentagem|Máximo|Percentual de cache usado. Aplica-se somente a data warehouses.|Nenhuma dimensão|
|connection_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Nenhuma dimensão|
|connection_successful|Conexões com êxito|Contagem|Total|Conexões com êxito|Nenhuma dimensão|
|cpu_percent|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Nenhuma dimensão|
|cpu_limit|Limite de CPU|Contagem|Média|Limite de CPU. Aplica-se a bancos de dados baseados em vCore.|Nenhuma dimensão|
|cpu_used|CPU usada|Contagem|Média|CPU usada. Aplica-se a bancos de dados baseados em vCore.|Nenhuma dimensão|
|bloqueado|Deadlocks|Contagem|Total|Deadlocks. Não aplicável a data warehouses.|Nenhuma dimensão|
|diff_backup_size_bytes|Tamanho de armazenamento diferencial de backup|Bytes|Máximo|Tamanho do armazenamento de backup diferencial cumulativo. Aplica-se a bancos de dados Uso Geral e Comercialmente Crítico. Não se aplica no momento a gerenciar bancos de dados de instância.|Nenhuma dimensão|
|dtu_limit|Limite de DTU|Contagem|Média|Limite de DTU. Aplica-se a bancos de dados baseados em DTU.|Nenhuma dimensão|
|dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|Porcentagem de DTU. Aplica-se a bancos de dados baseados em DTU.|Nenhuma dimensão|
|dtu_used|DTU usado|Contagem|Média|DTU usado. Aplica-se a bancos de dados baseados em DTU.|Nenhuma dimensão|
|dw_cpu_percent|Percentual de CPU no nível de nó DW|Percentagem|Média|Percentual de CPU no nível de nó DW|DwLogicalNodeId|
|dw_physical_data_read_percent|Porcentagem de e/s de dados de nível de nó DW|Percentagem|Média|Porcentagem de e/s de dados de nível de nó DW|DwLogicalNodeId|
|dwu_consumption_percent|Porcentagem de DWU|Percentagem|Máximo|Porcentagem de DWU. Aplica-se somente a data warehouses.|Nenhuma dimensão|
|dwu_limit|Limite de DWU|Contagem|Máximo|Limite de DWU. Aplica-se somente a data warehouses.|Nenhuma dimensão|
|dwu_used|DWU usado|Contagem|Máximo|DWU usado. Aplica-se somente a data warehouses.|Nenhuma dimensão|
|full_backup_size_bytes|Tamanho de armazenamento de backup completo|Bytes|Máximo|Tamanho de armazenamento de backup completo cumulativo. Aplica-se a bancos de dados Uso Geral e Comercialmente Crítico. Não se aplica no momento a gerenciar bancos de dados de instância.|Nenhuma dimensão|
|local_tempdb_usage_percent|Porcentagem de tempdb local|Percentagem|Média|Porcentagem de tempdb local. Aplica-se somente a data warehouses.|Nenhuma dimensão|
|log_backup_size_bytes|Tamanho do armazenamento de backup de log|Bytes|Máximo|Tamanho de armazenamento de backup de log cumulativo. Aplica-se a bancos de dados Uso Geral e Comercialmente Crítico. Não se aplica no momento a gerenciar bancos de dados de instância.|Nenhuma dimensão|
|log_write_percent|Percentual de e/s de log|Percentagem|Média|Percentual de e/s de log. Não aplicável a data warehouses.|Nenhuma dimensão|
|physical_data_read_percent|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|Nenhuma dimensão|
|sessions_percent|Porcentagem de sessões|Percentagem|Média|Porcentagem de sessões. Não aplicável a data warehouses.|Nenhuma dimensão|
|sqlserver_process_core_percent|Porcentagem de núcleo do processo de SQL Server|Percentagem|Máximo|Essa métrica é um espaço reservado e não é preenchida neste momento.|Nenhuma dimensão|
|sqlserver_process_memory_percent|Porcentagem de memória de processo SQL Server|Percentagem|Máximo|Essa métrica é um espaço reservado e não é preenchida neste momento.|Nenhuma dimensão|
|armazenamento|Espaço de dados usado|Bytes|Máximo|Tamanho total do banco de dados. Não aplicável a data warehouses.|Nenhuma dimensão|
|storage_percent|Porcentagem de espaço de dados usada|Percentagem|Máximo|Porcentagem de tamanho do banco de dados. Não aplicável a data warehouses ou a bancos de dados de hiperescala.|Nenhuma dimensão|
|tempdb_data_size|Tamanho do arquivo de dados tempdb em kilobytes|Contagem|Máximo|Tamanho do arquivo de dados tempdb em kilobytes. Não aplicável a data warehouses. Essa métrica estará disponível para bancos de dados usando o modelo de compra vCore ou 100 DTU e superior para modelos de compra baseados em DTU.|Nenhuma dimensão|
|tempdb_log_size|Tamanho do arquivo de log de tempdb em kilobytes|Contagem|Máximo|Tamanho do arquivo de log de tempdb em kilobytes. Não aplicável a data warehouses. Essa métrica estará disponível para bancos de dados usando o modelo de compra vCore ou 100 DTU e superior para modelos de compra baseados em DTU.|Nenhuma dimensão|
|tempdb_log_used_percent|Log de porcentagem de tempdb usado|Percentagem|Máximo|Log de porcentagem de tempdb usado. Não aplicável a data warehouses. Essa métrica estará disponível para bancos de dados usando o modelo de compra vCore ou 100 DTU e superior para modelos de compra baseados em DTU.|Nenhuma dimensão|
|workers_percent|Porcentagem de trabalhadores|Percentagem|Média|Porcentagem de trabalhadores. Não aplicável a data warehouses.|Nenhuma dimensão|
|xtp_storage_percent|Porcentagem de armazenamento OLTP na memória|Percentagem|Média|Porcentagem de armazenamento OLTP na memória. Não aplicável a data warehouses.|Nenhuma dimensão|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|allocated_data_storage|Espaço de dados alocado|Bytes|Média|Espaço de dados alocado|Nenhuma dimensão|
|allocated_data_storage_percent|Porcentagem alocada de espaço de dados|Percentagem|Máximo|Porcentagem alocada de espaço de dados|Nenhuma dimensão|
|cpu_limit|Limite de CPU|Contagem|Média|Limite de CPU. Aplica-se a pools elásticos baseados em vCore.|Nenhuma dimensão|
|cpu_percent|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Nenhuma dimensão|
|cpu_used|CPU usada|Contagem|Média|CPU usada. Aplica-se a pools elásticos baseados em vCore.|Nenhuma dimensão|
|dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|Porcentagem de DTU. Aplica-se a pools elásticos baseados em DTU.|Nenhuma dimensão|
|eDTU_limit|limite de eDTU|Contagem|Média|limite de eDTU. Aplica-se a pools elásticos baseados em DTU.|Nenhuma dimensão|
|eDTU_used|eDTU usado|Contagem|Média|eDTU usado. Aplica-se a pools elásticos baseados em DTU.|Nenhuma dimensão|
|log_write_percent|Percentual de e/s de log|Percentagem|Média|Percentual de e/s de log|Nenhuma dimensão|
|physical_data_read_percent|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|Nenhuma dimensão|
|sessions_percent|Porcentagem de sessões|Percentagem|Média|Porcentagem de sessões|Nenhuma dimensão|
|storage_limit|Tamanho máximo de dados|Bytes|Média|Limite de armazenamento|Nenhuma dimensão|
|storage_percent|Porcentagem de espaço de dados usada||Percentagem|Média|Porcentagem de armazenamento|Nenhuma dimensão|
|storage_used|Espaço de dados usado|Bytes|Média|Armazenamento utilizado|Nenhuma dimensão|
|sqlserver_process_core_percent|Porcentagem de núcleo do processo de SQL Server|Percentagem|Máximo|Essa métrica é um espaço reservado e não é preenchida neste momento.|Nenhuma dimensão|
|sqlserver_process_memory_percent|Porcentagem de memória de processo SQL Server|Percentagem|Máximo|Essa métrica é um espaço reservado e não é preenchida neste momento.|Nenhuma dimensão|
|tempdb_data_size|Tamanho do arquivo de dados tempdb em kilobytes|Contagem|Máximo|Tamanho do arquivo de dados tempdb em kilobytes. Não aplicável a data warehouses. Essa métrica estará disponível para bancos de dados usando o modelo de compra vCore ou 100 DTU e superior para modelos de compra baseados em DTU.|Nenhuma dimensão|
|tempdb_log_size|Tamanho do arquivo de log de tempdb em kilobytes|Contagem|Máximo|Tamanho do arquivo de log de tempdb em kilobytes. Não aplicável a data warehouses. Essa métrica estará disponível para bancos de dados usando o modelo de compra vCore ou 100 DTU e superior para modelos de compra baseados em DTU.|Nenhuma dimensão|
|tempdb_log_used_percent|Log de porcentagem de tempdb usado|Percentagem|Máximo|Log de porcentagem de tempdb usado. Não aplicável a data warehouses. Essa métrica estará disponível para bancos de dados usando o modelo de compra vCore ou 100 DTU e superior para modelos de compra baseados em DTU.|Nenhuma dimensão|
|workers_percent|Porcentagem de trabalhadores|Percentagem|Média|Porcentagem de trabalhadores|Nenhuma dimensão|
|xtp_storage_percent|Porcentagem de armazenamento OLTP na memória|Percentagem|Média|Porcentagem de armazenamento OLTP na memória|Nenhuma dimensão|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|avg_cpu_percent|Percentual médio de CPU|Percentagem|Média|Percentual médio de CPU|Nenhuma dimensão|
|io_bytes_read|Bytes de e/s lidos|Bytes|Média|Bytes de e/s lidos|Nenhuma dimensão|
|io_requests|Contagem de solicitações de e/s|Contagem|Média|Contagem de solicitações de e/s|Nenhuma dimensão|
|io_bytes_written|Bytes de e/s gravados|Bytes|Média|Bytes de e/s gravados|Nenhuma dimensão|
|reserved_storage_mb|Espaço de armazenamento reservado|Contagem|Média|Espaço de armazenamento reservado|Nenhuma dimensão|
|storage_space_used_mb|Espaço de armazenamento usado|Contagem|Média|Espaço de armazenamento usado|Nenhuma dimensão|
|virtual_core_count|Contagem de núcleos virtuais|Contagem|Média|Contagem de núcleos virtuais|Nenhuma dimensão|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|UsedCapacity|Capacidade utilizada|Bytes|Média|Capacidade da conta utilizada|Nenhuma dimensão|
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
|BlobCount|Contagem de Blobs|Contagem|Total|O número de blobs no serviço Blob da conta de armazenamento.|BlobType|       |BlobCount|Contagem de Blobs|Contagem|Média|O número de blobs no serviço Blob da conta de armazenamento.|BlobType, camada|
|ContainerCount|Contagem do Contentor de Blobs|Contagem|Média|O número de contentores no serviço Blob da conta de armazenamento.|Nenhuma dimensão|
|IndexCapacity|Capacidade do índice|Bytes|Média|A quantidade de armazenamento usada pelo índice ADLS Gen2 (hierárquico) em bytes.|Nenhuma dimensão|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Capacidade de|Capacidade do arquivo|Bytes|Média|A quantidade de armazenamento usada pelo serviço de arquivo da conta de armazenamento em bytes.|Nenhuma dimensão|
|FileCount|Contagem de arquivos|Contagem|Média|O número de arquivos no serviço de arquivo da conta de armazenamento.|Nenhuma dimensão|
|FileShareCount|Contagem de compartilhamento de arquivos|Contagem|Média|O número de compartilhamentos de arquivos no serviço de arquivo da conta de armazenamento.|Nenhuma dimensão|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueueCapacity|Capacidade de Fila|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Fila a conta de armazenamento, em bytes.|Nenhuma dimensão|
|QueueCount|Contagem de Filas|Contagem|Média|O número de filas no serviço Fila da conta de armazenamento.|Nenhuma dimensão|
|QueueMessageCount|Contagem de Mensagens em Fila|Contagem|Média|O número aproximado de mensagens em fila no serviço Fila da conta de armazenamento.|Nenhuma dimensão|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TableCapacity|Capacidade de Tabelas|Bytes|Média|A quantidade de armazenamento utilizado pelo serviço Tabela da conta de armazenamento, em bytes.|Nenhuma dimensão|
|TableCount|Contagem de Tabelas|Contagem|Média|O número de tabelas no serviço Tabela da conta de armazenamento.|Nenhuma dimensão|
|TableEntityCount|Contagem de Entidade de Tabelas|Contagem|Média|O número de entidades de tabelas no serviço Tabela da conta de armazenamento.|Nenhuma dimensão|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Resultado da sessão de sincronização|Contagem|Média|Métrica que registra um valor de 1 cada vez que o ponto de extremidade do servidor conclui com êxito uma sessão de sincronização com o ponto de extremidade de nuvem|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Arquivos sincronizados|Contagem|Total|Contagem de arquivos sincronizados|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Arquivos não sincronizando|Contagem|Total|Falha na sincronização da contagem de arquivos|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Bytes sincronizados|Bytes|Total|Tamanho total do arquivo transferido para sessões de sincronização|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Status online do servidor|Contagem|Máximo|Métrica que registra um valor de 1 cada vez que o servidor registrado registra com êxito uma pulsação com o ponto de extremidade de nuvem|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Recall de camadas de nuvem|Bytes|Total|Tamanho total dos dados recuperados pelo servidor|ServerName|

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
|IngressReceivedMessages|Mensagens de entrada recebidas|Contagem|Total|Contagem de mensagens lidas de todas as fontes de eventos do hub de eventos ou do Hub IoT|Nenhuma dimensão|
|IngressReceivedInvalidMessages|A entrada recebeu mensagens inválidas|Contagem|Total|Contagem de mensagens inválidas lidas de todas as fontes de eventos do hub de eventos ou do Hub IoT|Nenhuma dimensão|
|IngressReceivedBytes|Bytes de entrada recebidos|Bytes|Total|Contagem de bytes lidos de todas as origens do evento|Nenhuma dimensão|
|IngressStoredBytes|Bytes de entrada armazenados|Bytes|Total|Tamanho total de eventos processados e disponíveis com êxito para consulta|Nenhuma dimensão|
|IngressStoredEvents|Eventos de entrada armazenados|Contagem|Total|Contagem de eventos mesclados processados e disponíveis com êxito para consulta|Nenhuma dimensão|
|IngressReceivedMessagesTimeLag|Atraso de tempo de mensagens recebidas de entrada|Segundos|Máximo|Diferença entre a hora em que a mensagem é enfileirada na origem do evento e a hora em que ela é processada na entrada|Nenhuma dimensão|
|IngressReceivedMessagesCountLag|Atraso na contagem de mensagens recebidas de entrada|Contagem|Média|Diferença entre o número de sequência da última mensagem enfileirada na partição de origem do evento e o número de sequência da mensagem sendo processada na entrada|Nenhuma dimensão|
|WarmStorageMaxProperties|Propriedades máximas de armazenamento quente|Contagem|Máximo|Número máximo de propriedades usadas permitidas pelo ambiente para SKU S1/S2 e número máximo de propriedades permitidas pelo armazenamento quente para o SKU PAYG|Nenhuma dimensão|
|WarmStorageUsedProperties|Propriedades usadas de armazenamento quente |Contagem|Máximo|Número de propriedades usadas pelo ambiente para SKU S1/S2 e número de propriedades usadas pelo armazenamento quente para SKU PAYG|Nenhuma dimensão|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|IngressReceivedMessages|Mensagens de entrada recebidas|Contagem|Total|Contagem de mensagens lidas da origem do evento|Nenhuma dimensão|
|IngressReceivedInvalidMessages|A entrada recebeu mensagens inválidas|Contagem|Total|Contagem de mensagens inválidas lidas da origem do evento|Nenhuma dimensão|
|IngressReceivedBytes|Bytes de entrada recebidos|Bytes|Total|Contagem de bytes lidos da origem do evento|Nenhuma dimensão|
|IngressStoredBytes|Bytes de entrada armazenados|Bytes|Total|Tamanho total de eventos processados e disponíveis com êxito para consulta|Nenhuma dimensão|
|IngressStoredEvents|Eventos de entrada armazenados|Contagem|Total|Contagem de eventos mesclados processados e disponíveis com êxito para consulta|Nenhuma dimensão|
|IngressReceivedMessagesTimeLag|Atraso de tempo de mensagens recebidas de entrada|Segundos|Máximo|Diferença entre a hora em que a mensagem é enfileirada na origem do evento e a hora em que ela é processada na entrada|Nenhuma dimensão|
|IngressReceivedMessagesCountLag|Atraso na contagem de mensagens recebidas de entrada|Contagem|Média|Diferença entre o número de sequência da última mensagem enfileirada na partição de origem do evento e o número de sequência da mensagem sendo processada na entrada|Nenhuma dimensão|
|WarmStorageMaxProperties|Propriedades máximas de armazenamento quente|Contagem|Máximo|Número máximo de propriedades usadas permitidas pelo ambiente para SKU S1/S2 e número máximo de propriedades permitidas pelo armazenamento quente para o SKU PAYG|Nenhuma dimensão|
|WarmStorageUsedProperties|Propriedades usadas de armazenamento quente |Contagem|Máximo|Número de propriedades usadas pelo ambiente para SKU S1/S2 e número de propriedades usadas pelo armazenamento quente para SKU PAYG|Nenhuma dimensão|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Bytes de leitura de disco/s|BytesPerSecond|Média|Taxa de transferência média do disco devido a operações de leitura no período de exemplo.|Nenhuma dimensão|
|DiskWriteBytesPerSecond|Bytes de gravação no disco/s|BytesPerSecond|Média|Taxa de transferência média do disco devido a operações de gravação no período de exemplo.|Nenhuma dimensão|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Taxa de transferência total do disco devido a operações de leitura no período de exemplo.|Nenhuma dimensão|
|Bytes de gravação no disco|Bytes de gravação no disco|Bytes|Total|Taxa de transferência total do disco devido a operações de gravação no período de exemplo.|Nenhuma dimensão|
|DiskReadOperations|Operações de leitura de disco|Contagem|Total|O número de operações de leitura de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Nenhuma dimensão|
|DiskWriteOperations|Operações de gravação de disco|Contagem|Total|O número de operações de gravação de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Nenhuma dimensão|
|Operações de leitura de disco/s|Operações de leitura de disco/s|CountPerSecond|Média|O número médio de operações de leitura de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Nenhuma dimensão|
|Operações de gravação de disco/s|Operações de gravação de disco/s|CountPerSecond|Média|O número médio de operações de gravação de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Nenhuma dimensão|
|DiskReadLatency|Latência de leitura de disco|Milissegundos|Média|Latência de leitura total. A soma das latências de leitura do dispositivo e do kernel.|Nenhuma dimensão|
|DiskWriteLatency|Latência de gravação de disco|Milissegundos|Média|Latência de gravação total. A soma das latências de gravação do kernel e do dispositivo.|Nenhuma dimensão|
|NetworkInBytesPerSecond|Rede em bytes/s|BytesPerSecond|Média|Taxa de transferência média de rede para o tráfego recebido.|Nenhuma dimensão|
|NetworkOutBytesPerSecond|Bytes de saída de rede/s|BytesPerSecond|Média|Taxa de transferência média de rede para o tráfego transmitido.|Nenhuma dimensão|
|Entrada na Rede|Entrada na Rede|Bytes|Total|Taxa de transferência total de rede para o tráfego recebido.|Nenhuma dimensão|
|Saída da Rede|Saída da Rede|Bytes|Total|Taxa de transferência total de rede para o tráfego transmitido.|Nenhuma dimensão|
|MemoryUsed|Memória usada|Bytes|Média|A quantidade de memória da máquina que está sendo usada pela VM.|Nenhuma dimensão|
|MemoryGranted|Memória concedida|Bytes|Média|A quantidade de memória que foi concedida à VM pelo host. A memória não é concedida ao host até que ele seja tocado uma vez e a memória concedida possa ser trocada ou bloqueada se o VMkernel precisar da memória.|Nenhuma dimensão|
|MemoryActive|Memória ativa|Bytes|Média|A quantidade de memória usada pela VM na última pequena janela de tempo. Esse é o número "verdadeiro" da quantidade de memória necessária para a VM no momento. A memória adicional não utilizada pode ser trocada ou por balão sem impacto no desempenho do convidado.|Nenhuma dimensão|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A utilização da CPU. Esse valor é relatado com 100% que representa todos os núcleos de processador no sistema. Por exemplo, uma VM de 2 vias usando 50% de um sistema de quatro núcleos está usando completamente dois núcleos.|Nenhuma dimensão|
|PercentageCpuReady|Percentual de CPU pronta|Milissegundos|Total|O tempo de preparação é o tempo gasto aguardando que as CPUs fiquem disponíveis no intervalo de atualização anterior.|Nenhuma dimensão|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuPercentage|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Instância|
|MemoryPercentage|Porcentagem de memória|Percentagem|Média|Porcentagem de memória|Instância|
|DiskQueueLength|Comprimento da fila de disco|Contagem|Média|Comprimento da fila de disco|Instância|
|HttpQueueLength|Comprimento da fila http|Contagem|Média|Comprimento da fila http|Instância|
|BytesReceived|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Saída de dados|Bytes|Total|Saída de dados|Instância|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/sites (exceto funções)

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuTime|Tempo de CPU|Segundos|Total|Tempo de CPU|Instância|
|Pedidos|Pedidos|Contagem|Total|Pedidos|Instância|
|BytesReceived|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Saída de dados|Bytes|Total|Saída de dados|Instância|
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
|Alça|Contagem de identificadores|Contagem|Média|Contagem de identificadores|Instância|
|Threads|Contagem de threads|Contagem|Média|Contagem de threads|Instância|
|PrivateBytes|Bytes particulares|Bytes|Média|Bytes particulares|Instância|
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

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/sites (funções)

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BytesReceived|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Saída de dados|Bytes|Total|Saída de dados|Instância|
|Http5xx|Erros do servidor http|Contagem|Total|Erros do servidor http|Instância|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|AverageMemoryWorkingSet|Conjunto de trabalho de memória média|Bytes|Média|Conjunto de trabalho de memória média|Instância|
|FunctionExecutionUnits|Unidades de execução de função|MB/milissegundos|Total|[Unidades de execução de função](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instância|
|FunctionExecutionCount|Contagem de execução de função|Contagem|Total|Contagem de execução de função|Instância|
|PrivateBytes|Bytes particulares|Bytes|Média|Bytes particulares|Instância|
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

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuTime|Tempo de CPU|Segundos|Total|Tempo de CPU|Instância|
|Pedidos|Pedidos|Contagem|Total|Pedidos|Instância|
|BytesReceived|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Saída de dados|Bytes|Total|Saída de dados|Instância|
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
|FunctionExecutionUnits|Unidades de execução de função|Contagem|Total|Unidades de execução de função|Instância|
|FunctionExecutionCount|Contagem de execução de função|Contagem|Total|Contagem de execução de função|Instância|
|AppConnections|Ligações|Contagem|Média|Ligações|Instância|
|Alça|Contagem de identificadores|Contagem|Média|Contagem de identificadores|Instância|
|Threads|Contagem de threads|Contagem|Média|Contagem de threads|Instância|
|PrivateBytes|Bytes particulares|Bytes|Média|Bytes particulares|Instância|
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

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Pedidos|Pedidos|Contagem|Total|Pedidos|Instância|
|BytesReceived|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Saída de dados|Bytes|Total|Saída de dados|Instância|
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
|TotalFrontEnds|Total de front-ends|Contagem|Média|Total de front-ends|Nenhuma dimensão|
|SmallAppServicePlanInstances|Trabalhos do plano do serviço de aplicativo pequeno|Contagem|Média|Trabalhos do plano do serviço de aplicativo pequeno|Nenhuma dimensão|
|MediumAppServicePlanInstances|Trabalhos do plano do serviço de aplicativo médio|Contagem|Média|Trabalhos do plano do serviço de aplicativo médio|Nenhuma dimensão|
|LargeAppServicePlanInstances|Trabalhos grandes do plano do serviço de aplicativo|Contagem|Média|Trabalhos grandes do plano do serviço de aplicativo|Nenhuma dimensão|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|WorkersTotal|Total de trabalhadores|Contagem|Média|Total de trabalhadores|Nenhuma dimensão|
|WorkersAvailable|Trabalhos disponíveis|Contagem|Média|Trabalhos disponíveis|Nenhuma dimensão|
|WorkersUsed|Trabalhadores usados|Contagem|Média|Trabalhadores usados|Nenhuma dimensão|
|CpuPercentage|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Instância|
|MemoryPercentage|Porcentagem de memória|Percentagem|Média|Porcentagem de memória|Instância|

## <a name="next-steps"></a>Passos seguintes
* [Leia sobre métricas no Azure Monitor](data-platform.md)
* [Criar alertas em métricas](alerts-overview.md)
* [Exportar métricas para armazenamento, Hub de eventos ou Log Analytics](resource-logs-overview.md)
