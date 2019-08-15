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
ms.openlocfilehash: 49780ec342ea168d27ab8a029c41a1c18a6ffcc4
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019055"
---
# <a name="supported-metrics-with-azure-monitor"></a>Métricas com suporte com Azure Monitor

O Azure Monitor fornece várias maneiras de interagir com as métricas, incluindo o gráfico delas no portal, acessá-las por meio da API REST ou consultá-las usando o PowerShell ou a CLI. Abaixo está uma lista completa de todas as métricas disponíveis atualmente com o pipeline de métrica do Azure Monitor. Outras métricas podem estar disponíveis no portal ou usando APIs herdadas. Esta lista abaixo inclui apenas as métricas disponíveis usando o pipeline de métrica de Azure Monitor consolidado. Para consultar e acessar essas métricas, use a [versão de api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Atualmente, o envio de métricas multidimensionais através das definições de diagnóstico não é suportado. As métricas com dimensões são exportadas como métricas dimensionais simples e agregadas em valores de dimensões.
>
> *Por exemplo*: A métrica ' mensagens de entrada ' em um hub de eventos pode ser explorada e colocada em gráfico em um nível por fila. No entanto, se for exportada através das definições de diagnóstico, a métrica será representada como todas as mensagens recebidas em todas as filas do hub do Hub de Eventos.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|Average|QPU. Intervalo de 0-100 para S1, 0-200 para S2 e 0-400 para S4|ServerResourceType|
|memory_metric|Memória|Bytes|Average|Memória. Intervalo de 0-25 GB para S1, 0-50 GB para S2 e 0-100 GB para S4|ServerResourceType|
|TotalConnectionRequests|Total de solicitações de conexão|Count|Average|Total de solicitações de conexão. Essas são entradas.|ServerResourceType|
|SuccessfullConnectionsPerSec|Conexões com êxito por segundo|CountPerSecond|Average|Taxa de conclusões de conexão com êxito.|ServerResourceType|
|TotalConnectionFailures|Total de falhas de conexão|Count|Average|Total de tentativas de conexão com falha.|ServerResourceType|
|CurrentUserSessions|Sessões de usuário atuais|Count|Average|Número atual de sessões de usuário estabelecidas.|ServerResourceType|
|QueryPoolBusyThreads|Threads ocupados do pool de consulta|Count|Average|Número de threads ocupados no pool de threads de consulta.|ServerResourceType|
|CommandPoolJobQueueLength|Comprimento da fila de trabalhos do pool de comandos|Count|Average|Número de trabalhos na fila do pool de threads de comando.|ServerResourceType|
|ProcessingPoolJobQueueLength|Tamanho da fila de trabalhos do pool de processamento|Count|Average|Número de trabalhos que não são de e/s na fila do pool de threads de processamento.|ServerResourceType|
|CurrentConnections|Ligação: Conexões atuais|Count|Average|Número atual de conexões de cliente estabelecidas.|ServerResourceType|
|CleanerCurrentPrice|Memória: Preço atual do limpador|Count|Average|Preço atual da memória, $/byte/time, normalizado para 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Memória: Limpeza de memória recolhida|Bytes|Average|Quantidade de memória, em bytes, sujeita a limpeza pelo limpador de segundo plano.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memória: Limpeza de memória não recolhida|Bytes|Average|Quantidade de memória, em bytes, não sujeita a limpeza pelo limpador de segundo plano.|ServerResourceType|
|MemoryUsage|Memória: Utilização da Memória|Bytes|Average|Uso de memória do processo do servidor conforme usado no cálculo do preço de memória de limpeza. Igual ao contador Process\PrivateBytes mais o tamanho dos dados mapeados na memória, ignorando qualquer memória que foi mapeada ou alocada pelo VertiPaq (mecanismo analítico na memória) xVelocity excedendo o limite de memória do mecanismo xVelocity.|ServerResourceType|
|MemoryLimitHard|Memória: Limite de memória rígido|Bytes|Average|Limite de memória rígido, do arquivo de configuração.|ServerResourceType|
|MemoryLimitHigh|Memória: Limite de memória alto|Bytes|Average|Limite de memória alto, do arquivo de configuração.|ServerResourceType|
|MemoryLimitLow|Memória: Limite de memória baixo|Bytes|Average|Limite de memória baixo, do arquivo de configuração.|ServerResourceType|
|MemoryLimitVertiPaq|Memória: Limite de memória VertiPaq|Bytes|Average|Limite na memória, do arquivo de configuração.|ServerResourceType|
|Quota|Memória: Quota|Bytes|Average|Cota de memória atual, em bytes. A cota de memória também é conhecida como concessão de memória ou reserva de memória.|ServerResourceType|
|QuotaBlocked|Memória: Cota bloqueada|Count|Average|Número atual de solicitações de cota que são bloqueadas até que outras cotas de memória sejam liberadas.|ServerResourceType|
|VertiPaqNonpaged|Memória: VertiPaq não paginável|Bytes|Average|Bytes de memória bloqueados no conjunto de trabalho para uso pelo mecanismo na memória.|ServerResourceType|
|VertiPaqPaged|Memória: VertiPaq paginado|Bytes|Average|Bytes de memória paginável em uso para dados na memória.|ServerResourceType|
|RowsReadPerSec|A processar: Linhas lidas por segundo|CountPerSecond|Average|Taxa de linhas lidas de todos os bancos de dados relacionais.|ServerResourceType|
|RowsConvertedPerSec|A processar: Linhas convertidas por segundo|CountPerSecond|Average|Taxa de linhas convertidas durante o processamento.|ServerResourceType|
|RowsWrittenPerSec|A processar: Linhas gravadas por segundo|CountPerSecond|Average|Taxa de linhas gravadas durante o processamento.|ServerResourceType|
|CommandPoolBusyThreads|Threads Threads ocupados do pool de comandos|Count|Average|Número de threads ocupados no pool de threads de comando.|ServerResourceType|
|CommandPoolIdleThreads|Threads Threads ociosos do pool de comandos|Count|Average|Número de threads ociosos no pool de threads de comando.|ServerResourceType|
|LongParsingBusyThreads|Threads Threads ocupados da análise longa|Count|Average|Número de threads ocupados no pool de threads da análise longa.|ServerResourceType|
|LongParsingIdleThreads|Threads Threads ociosos da análise longa|Count|Average|Número de threads ociosos no pool de threads da análise longa.|ServerResourceType|
|LongParsingJobQueueLength|Threads Comprimento da fila de trabalhos de análise longa|Count|Average|Número de trabalhos na fila do pool de threads de análise longa.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Threads Processando threads de trabalho de e/s de pool ocupado|Count|Average|Número de threads executando trabalhos de e/s no pool de threads de processamento.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Threads Threads de não-e/s ocupados do pool de processamento|Count|Average|Número de threads que executam trabalhos que não são de e/s no pool de threads de processamento.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Threads Tamanho da fila de trabalhos de e/s do pool de processamento|Count|Average|Número de trabalhos de e/s na fila do pool de threads de processamento.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Threads Processando threads de trabalho de e/s de pool ocioso|Count|Average|Número de threads ociosos para trabalhos de e/s no pool de threads de processamento.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Threads Threads de não-e/s ociosos do pool de processamento|Count|Average|Número de threads ociosos no pool de threads de processamento dedicados a trabalhos que não são de e/s.|ServerResourceType|
|QueryPoolIdleThreads|Threads Threads ociosos do pool de consulta|Count|Average|Número de threads ociosos para trabalhos de e/s no pool de threads de processamento.|ServerResourceType|
|QueryPoolJobQueueLength|Threads Comprimento da fila de trabalhos do pool de consultas|Count|Average|Número de trabalhos na fila do pool de threads de consulta.|ServerResourceType|
|ShortParsingBusyThreads|Threads Threads ocupados da análise resumida|Count|Average|Número de threads ocupados no pool de threads da análise resumida.|ServerResourceType|
|ShortParsingIdleThreads|Threads Threads ociosos da análise resumida|Count|Average|Número de threads ociosos no pool de threads da análise resumida.|ServerResourceType|
|ShortParsingJobQueueLength|Threads Comprimento da fila de trabalhos da análise resumida|Count|Average|Número de trabalhos na fila do pool de threads da análise resumida.|ServerResourceType|
|memory_thrashing_metric|Ultrapaginação de memória|Percent|Average|Média de ultrapaginação de memória.|ServerResourceType|
|mashup_engine_qpu_metric|QPU do mecanismo M|Count|Average|Uso de QPU por processos do mecanismo mashup|ServerResourceType|
|mashup_engine_memory_metric|Memória do mecanismo M|Bytes|Average|Uso de memória pelos processos do mecanismo mashup|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Pedidos|Pedidos|Count|Total|O número total de solicitações de gateway em um determinado período. Ele pode ser dividido por várias dimensões para ajudá-lo a diagnosticar problemas. |Local, BackendResponseCode, LastErrorReason, GatewayResponseCode|
|TotalRequests|Total de solicitações do gateway|Count|Total|O número total de solicitações de gateway em um determinado período. Essa métrica foi preterida, é recomendável usar a `Requests` nova métrica. |Localização, nome do host|
|SuccessfulRequests|Solicitações de gateway bem-sucedidas|Count|Total|O número total de solicitações de gateway bem-sucedidas em um determinado período. Essa métrica foi preterida, é recomendável usar a `Requests` nova métrica.|Localização, nome do host|
|UnauthorizedRequests|Solicitações de gateway não autorizadas|Count|Total| O número total de solicitações de gateway não autorizadas em um determinado período. Essa métrica foi preterida, é recomendável usar a `Requests` nova métrica.|Localização, nome do host|
|FailedRequests|Solicitações de gateway com falha|Count|Total|O número total de solicitações de gateway com falha em um determinado período. Essa métrica foi preterida, é recomendável usar a `Requests` nova métrica.|Localização, nome do host|
|OtherRequests|Outras solicitações de gateway|Count|Total|O número total de solicitações de gateway em um determinado período que não se enquadram nas categorias bem-sucedidas, não autorizadas ou com falha. Essa métrica foi preterida, é recomendável usar a `Requests` nova métrica. |Localização, nome do host|
|Duration|Duração geral das solicitações de gateway|Milissegundos|Average|O tempo entre o gerenciamento de API recebe uma solicitação de um cliente e quando ele retorna uma resposta ao cliente.|Localização, nome do host|
|Capacidade|Capacidade|Percent|Average|Indicador de carga em uma instância de gerenciamento de API para tomar decisões informadas sobre a possibilidade de dimensionar a instância para acomodar mais carga.|Location|
|EventHubTotalEvents|Total de eventos do EventHub|Count|Total|O número total de eventos enviados ao EventHub do gerenciamento de API em um determinado período.|Location|
|EventHubSuccessfulEvents|Eventos de EventHub bem-sucedidos|Count|Total|O número total de eventos de EventHub bem-sucedidos em um determinado período.|Location|
|EventHubTotalFailedEvents|Eventos de EventHub com falha|Count|Total|O número total de eventos de EventHub com falha em um determinado período.|Location|
|EventHubRejectedEvents|Eventos de EventHub rejeitados|Count|Total|O número total de eventos do EventHub rejeitados (configuração incorreta ou não autorizado) em um determinado período.|Location|
|EventHubThrottledEvents|Eventos de EventHub limitados|Count|Total|O número total de eventos de EventHub limitado em um determinado período.|Location|
|EventHubTimedoutEvents|Eventos do EventHub com tempo limite excedido|Count|Total|O número total de eventos de EventHub expirados em um determinado período.|Location|
|EventHubDroppedEvents|Eventos do EventHub ignorados|Count|Total|O número total de eventos ignorados devido ao limite de tamanho de fila atingido em um determinado período.|Location|
|EventHubTotalBytesSent|Tamanho dos eventos do EventHub|Bytes|Total|O tamanho total dos eventos do EventHub em bytes em um determinado período.|Location|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalJob|Total de trabalhos|Count|Total|O número total de trabalhos|Runbook, status|
|TotalUpdateDeploymentRuns|Total de execuções de implantação de atualização|Count|Total|Total de execuções de implantação de atualização de software|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|Total de execuções do computador de implantação de atualização|Count|Total|O computador de implantação de atualização de software total é executado em uma execução de implantação de atualização de software|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CoreCount|Contagem de núcleos dedicados|Count|Total|Número total de núcleos dedicados na conta do lote|Nenhuma dimensão|
|TotalNodeCount|Contagem de nós dedicados|Count|Total|Número total de nós dedicados na conta do lote|Nenhuma dimensão|
|LowPriorityCoreCount|Contagem de núcleos de LowPriority|Count|Total|Número total de núcleos de baixa prioridade na conta do lote|Nenhuma dimensão|
|TotalLowPriorityNodeCount|Contagem de nós de baixa prioridade|Count|Total|Número total de nós de baixa prioridade na conta do lote|Nenhuma dimensão|
|CreatingNodeCount|Criando contagem de nós|Count|Total|Número de nós sendo criados|Nenhuma dimensão|
|StartingNodeCount|Contagem de nós inicial|Count|Total|Número de nós iniciando|Nenhuma dimensão|
|WaitingForStartTaskNodeCount|Aguardando a contagem de nó de tarefa inicial|Count|Total|Número de nós aguardando a conclusão da tarefa inicial|Nenhuma dimensão|
|StartTaskFailedNodeCount|Falha na contagem de nós da tarefa inicial|Count|Total|Número de nós em que a tarefa inicial falhou|Nenhuma dimensão|
|IdleNodeCount|Contagem de nós ociosos|Count|Total|Número de nós ociosos|Nenhuma dimensão|
|OfflineNodeCount|Contagem de nós offline|Count|Total|Número de nós offline|Nenhuma dimensão|
|RebootingNodeCount|Contagem de nós de reinicialização|Count|Total|Número de nós de reinicialização|Nenhuma dimensão|
|ReimagingNodeCount|Contagem de nós de reimagem|Count|Total|Número de nós de reimagem|Nenhuma dimensão|
|RunningNodeCount|Contagem de nós em execução|Count|Total|Número de nós em execução|Nenhuma dimensão|
|LeavingPoolNodeCount|Saindo da contagem de nós do pool|Count|Total|Número de nós saindo do pool|Nenhuma dimensão|
|UnusableNodeCount|Contagem de nós inutilizáveis|Count|Total|Número de nós inutilizáveis|Nenhuma dimensão|
|PreemptedNodeCount|Contagem de nós preempção|Count|Total|Número de nós admitidos|Nenhuma dimensão|
|TaskStartEvent|Eventos de início de tarefa|Count|Total|Número total de tarefas que foram iniciadas|Nenhuma dimensão|
|TaskCompleteEvent|Eventos de conclusão de tarefa|Count|Total|Número total de tarefas que foram concluídas|Nenhuma dimensão|
|TaskFailEvent|Eventos de falha de tarefa|Count|Total|Número total de tarefas que foram concluídas em um estado de falha|Nenhuma dimensão|
|PoolCreateEvent|Eventos de criação de pool|Count|Total|Número total de pools que foram criados|Nenhuma dimensão|
|PoolResizeStartEvent|Eventos de início de redimensionamento do pool|Count|Total|Número total de redimensionamentos de pool que foram iniciados|Nenhuma dimensão|
|PoolResizeCompleteEvent|Eventos completos de redimensionamento de pool|Count|Total|Número total de redimensionamentos de pool que foram concluídos|Nenhuma dimensão|
|PoolDeleteStartEvent|Eventos de início de exclusão de pool|Count|Total|Número total de exclusões de pool que iniciaram|Nenhuma dimensão|
|PoolDeleteCompleteEvent|Eventos completos de exclusão de pool|Count|Total|Número total de exclusões de pool que foram concluídas|Nenhuma dimensão|
|JobDeleteCompleteEvent|Eventos completos de exclusão de trabalho|Count|Total|Número total de trabalhos que foram excluídos com êxito.|Nenhuma dimensão|
|JobDeleteStartEvent|Eventos de início de exclusão de trabalho|Count|Total|Número total de trabalhos que foram solicitados a serem excluídos.|Nenhuma dimensão|
|JobDisableCompleteEvent|Trabalho de desabilitar eventos concluídos|Count|Total|Número total de trabalhos que foram desabilitados com êxito.|Nenhuma dimensão|
|JobDisableStartEvent|Eventos de início de desabilitação do trabalho|Count|Total|Número total de trabalhos que foram solicitados para serem desabilitados.|Nenhuma dimensão|
|JobStartEvent|Eventos de início do trabalho|Count|Total|Número total de trabalhos iniciados com êxito.|Nenhuma dimensão|
|JobTerminateCompleteEvent|Eventos de término de trabalho concluídos|Count|Total|Número total de trabalhos que foram encerrados com êxito.|Nenhuma dimensão|
|JobTerminateStartEvent|Eventos de início de término do trabalho|Count|Total|Número total de trabalhos que foram solicitados a serem encerrados.|Nenhuma dimensão|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|connectedclients|Clientes Ligados|Count|Máximo||ShardId|
|totalcommandsprocessed|Total de Operações|Count|Total||ShardId|
|cachehits|Acertos na Cache|Count|Total||ShardId|
|cachemisses|Falhas na Cache|Count|Total||ShardId|
|comandos|Obtém|Count|Total||ShardId|
|comandos|Conjuntos|Count|Total||ShardId|
|operationsPerSecond|Operações por segundo|Count|Máximo||ShardId|
|evictedkeys|Chaves Excluídas|Count|Total||ShardId|
|totalkeys|Chaves Totais|Count|Máximo||ShardId|
|expiredkeys|Chaves Expiradas|Count|Total||ShardId|
|usedmemory|Memória Utilizada|Bytes|Máximo||ShardId|
|usedmemorypercentage|Porcentagem de memória usada|Percent|Máximo||ShardId|
|usedmemoryRss|Memória RSS usada|Bytes|Máximo||ShardId|
|serverLoad|Carga do Servidor|Percent|Máximo||ShardId|
|cacheWrite|Escrita na Cache|BytesPerSecond|Máximo||ShardId|
|cacheRead|Leitura da Cache|BytesPerSecond|Máximo||ShardId|
|percentProcessorTime|CPU|Percent|Máximo||ShardId|
|cacheLatency|Microssegundos de latência de cache (versão prévia)|Count|Average||Fragmentid, Amostratype|
|erros|Erros|Count|Máximo||ShardId, ErrorType|
|connectedclients0|Clientes conectados (fragmento 0)|Count|Máximo||Nenhuma dimensão|
|totalcommandsprocessed0|Total de operações (fragmento 0)|Count|Total||Nenhuma dimensão|
|cachehits0|Acertos do cache (fragmento 0)|Count|Total||Nenhuma dimensão|
|cachemisses0|Erros de cache (fragmento 0)|Count|Total||Nenhuma dimensão|
|getcommands0|Gets (fragmento 0)|Count|Total||Nenhuma dimensão|
|setcommands0|Sets (fragmento 0)|Count|Total||Nenhuma dimensão|
|operationsPerSecond0|Operações por segundo (fragmento 0)|Count|Máximo||Nenhuma dimensão|
|evictedkeys0|Chaves removidas (fragmento 0)|Count|Total||Nenhuma dimensão|
|totalkeys0|Total de chaves (fragmento 0)|Count|Máximo||Nenhuma dimensão|
|expiredkeys0|Chaves expiradas (fragmento 0)|Count|Total||Nenhuma dimensão|
|usedmemory0|Memória usada (fragmento 0)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss0|Memória RSS usada (fragmento 0)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad0|Carga do servidor (fragmento 0)|Percent|Máximo||Nenhuma dimensão|
|cacheWrite0|Gravação no cache (fragmento 0)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead0|Leitura de cache (fragmento 0)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime0|CPU (fragmento 0)|Percent|Máximo||Nenhuma dimensão|
|connectedclients1|Clientes conectados (fragmento 1)|Count|Máximo||Nenhuma dimensão|
|totalcommandsprocessed1|Total de operações (fragmento 1)|Count|Total||Nenhuma dimensão|
|cachehits1|Acertos do cache (fragmento 1)|Count|Total||Nenhuma dimensão|
|cachemisses1|Erros de cache (fragmento 1)|Count|Total||Nenhuma dimensão|
|getcommands1|Gets (fragmento 1)|Count|Total||Nenhuma dimensão|
|setcommands1|Sets (fragmento 1)|Count|Total||Nenhuma dimensão|
|operationsPerSecond1|Operações por segundo (fragmento 1)|Count|Máximo||Nenhuma dimensão|
|evictedkeys1|Chaves removidas (fragmento 1)|Count|Total||Nenhuma dimensão|
|totalkeys1|Total de chaves (fragmento 1)|Count|Máximo||Nenhuma dimensão|
|expiredkeys1|Chaves expiradas (fragmento 1)|Count|Total||Nenhuma dimensão|
|usedmemory1|Memória usada (fragmento 1)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss1|Memória RSS usada (fragmento 1)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad1|Carga do servidor (fragmento 1)|Percent|Máximo||Nenhuma dimensão|
|cacheWrite1|Gravação no cache (fragmento 1)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead1|Leitura do cache (fragmento 1)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime1|CPU (fragmento 1)|Percent|Máximo||Nenhuma dimensão|
|connectedclients2|Clientes conectados (fragmento 2)|Count|Máximo||Nenhuma dimensão|
|totalcommandsprocessed2|Total de operações (fragmento 2)|Count|Total||Nenhuma dimensão|
|cachehits2|Acertos do cache (fragmento 2)|Count|Total||Nenhuma dimensão|
|cachemisses2|Erros de cache (fragmento 2)|Count|Total||Nenhuma dimensão|
|getcommands2|Gets (fragmento 2)|Count|Total||Nenhuma dimensão|
|setcommands2|Sets (fragmento 2)|Count|Total||Nenhuma dimensão|
|operationsPerSecond2|Operações por segundo (fragmento 2)|Count|Máximo||Nenhuma dimensão|
|evictedkeys2|Chaves removidas (fragmento 2)|Count|Total||Nenhuma dimensão|
|totalkeys2|Total de chaves (fragmento 2)|Count|Máximo||Nenhuma dimensão|
|expiredkeys2|Chaves expiradas (fragmento 2)|Count|Total||Nenhuma dimensão|
|usedmemory2|Memória usada (fragmento 2)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss2|Memória RSS usada (fragmento 2)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad2|Carga do servidor (fragmento 2)|Percent|Máximo||Nenhuma dimensão|
|cacheWrite2|Gravação no cache (fragmento 2)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead2|Leitura de cache (fragmento 2)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime2|CPU (fragmento 2)|Percent|Máximo||Nenhuma dimensão|
|connectedclients3|Clientes conectados (fragmento 3)|Count|Máximo||Nenhuma dimensão|
|totalcommandsprocessed3|Total de operações (fragmento 3)|Count|Total||Nenhuma dimensão|
|cachehits3|Acertos do cache (fragmento 3)|Count|Total||Nenhuma dimensão|
|cachemisses3|Erros de cache (fragmento 3)|Count|Total||Nenhuma dimensão|
|getcommands3|Gets (fragmento 3)|Count|Total||Nenhuma dimensão|
|setcommands3|Sets (fragmento 3)|Count|Total||Nenhuma dimensão|
|operationsPerSecond3|Operações por segundo (fragmento 3)|Count|Máximo||Nenhuma dimensão|
|evictedkeys3|Chaves removidas (fragmento 3)|Count|Total||Nenhuma dimensão|
|totalkeys3|Total de chaves (fragmento 3)|Count|Máximo||Nenhuma dimensão|
|expiredkeys3|Chaves expiradas (fragmento 3)|Count|Total||Nenhuma dimensão|
|usedmemory3|Memória usada (fragmento 3)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss3|Memória RSS usada (fragmento 3)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad3|Carga do servidor (fragmento 3)|Percent|Máximo||Nenhuma dimensão|
|cacheWrite3|Gravação no cache (fragmento 3)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead3|Leitura do cache (fragmento 3)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime3|CPU (fragmento 3)|Percent|Máximo||Nenhuma dimensão|
|connectedclients4|Clientes conectados (fragmento 4)|Count|Máximo||Nenhuma dimensão|
|totalcommandsprocessed4|Total de operações (fragmento 4)|Count|Total||Nenhuma dimensão|
|cachehits4|Acertos do cache (fragmento 4)|Count|Total||Nenhuma dimensão|
|cachemisses4|Erros de cache (fragmento 4)|Count|Total||Nenhuma dimensão|
|getcommands4|Gets (fragmento 4)|Count|Total||Nenhuma dimensão|
|setcommands4|Sets (fragmento 4)|Count|Total||Nenhuma dimensão|
|operationsPerSecond4|Operações por segundo (fragmento 4)|Count|Máximo||Nenhuma dimensão|
|evictedkeys4|Chaves removidas (fragmento 4)|Count|Total||Nenhuma dimensão|
|totalkeys4|Total de chaves (fragmento 4)|Count|Máximo||Nenhuma dimensão|
|expiredkeys4|Chaves expiradas (fragmento 4)|Count|Total||Nenhuma dimensão|
|usedmemory4|Memória usada (fragmento 4)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss4|Memória RSS usada (fragmento 4)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad4|Carga do servidor (fragmento 4)|Percent|Máximo||Nenhuma dimensão|
|cacheWrite4|Gravação no cache (fragmento 4)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead4|Leitura do cache (fragmento 4)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime4|CPU (fragmento 4)|Percent|Máximo||Nenhuma dimensão|
|connectedclients5|Clientes conectados (fragmento 5)|Count|Máximo||Nenhuma dimensão|
|totalcommandsprocessed5|Total de operações (fragmento 5)|Count|Total||Nenhuma dimensão|
|cachehits5|Acertos do cache (fragmento 5)|Count|Total||Nenhuma dimensão|
|cachemisses5|Erros de cache (fragmento 5)|Count|Total||Nenhuma dimensão|
|getcommands5|Gets (fragmento 5)|Count|Total||Nenhuma dimensão|
|setcommands5|Sets (fragmento 5)|Count|Total||Nenhuma dimensão|
|operationsPerSecond5|Operações por segundo (fragmento 5)|Count|Máximo||Nenhuma dimensão|
|evictedkeys5|Chaves removidas (fragmento 5)|Count|Total||Nenhuma dimensão|
|totalkeys5|Total de chaves (fragmento 5)|Count|Máximo||Nenhuma dimensão|
|expiredkeys5|Chaves expiradas (fragmento 5)|Count|Total||Nenhuma dimensão|
|usedmemory5|Memória usada (fragmento 5)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss5|Memória RSS usada (fragmento 5)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad5|Carga do servidor (fragmento 5)|Percent|Máximo||Nenhuma dimensão|
|cacheWrite5|Gravação no cache (fragmento 5)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead5|Leitura do cache (fragmento 5)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime5|CPU (fragmento 5)|Percent|Máximo||Nenhuma dimensão|
|connectedclients6|Clientes conectados (fragmento 6)|Count|Máximo||Nenhuma dimensão|
|totalcommandsprocessed6|Total de operações (fragmento 6)|Count|Total||Nenhuma dimensão|
|cachehits6|Acertos do cache (fragmento 6)|Count|Total||Nenhuma dimensão|
|cachemisses6|Erros de cache (fragmento 6)|Count|Total||Nenhuma dimensão|
|getcommands6|Gets (fragmento 6)|Count|Total||Nenhuma dimensão|
|setcommands6|Sets (fragmento 6)|Count|Total||Nenhuma dimensão|
|operationsPerSecond6|Operações por segundo (fragmento 6)|Count|Máximo||Nenhuma dimensão|
|evictedkeys6|Chaves removidas (fragmento 6)|Count|Total||Nenhuma dimensão|
|totalkeys6|Total de chaves (fragmento 6)|Count|Máximo||Nenhuma dimensão|
|expiredkeys6|Chaves expiradas (fragmento 6)|Count|Total||Nenhuma dimensão|
|usedmemory6|Memória usada (fragmento 6)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss6|Memória RSS usada (fragmento 6)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad6|Carga do servidor (fragmento 6)|Percent|Máximo||Nenhuma dimensão|
|cacheWrite6|Gravação no cache (fragmento 6)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead6|Leitura do cache (fragmento 6)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime6|CPU (fragmento 6)|Percent|Máximo||Nenhuma dimensão|
|connectedclients7|Clientes conectados (fragmento 7)|Count|Máximo||Nenhuma dimensão|
|totalcommandsprocessed7|Total de operações (fragmento 7)|Count|Total||Nenhuma dimensão|
|cachehits7|Acertos do cache (fragmento 7)|Count|Total||Nenhuma dimensão|
|cachemisses7|Erros de cache (fragmento 7)|Count|Total||Nenhuma dimensão|
|getcommands7|Gets (fragmento 7)|Count|Total||Nenhuma dimensão|
|setcommands7|Sets (fragmento 7)|Count|Total||Nenhuma dimensão|
|operationsPerSecond7|Operações por segundo (fragmento 7)|Count|Máximo||Nenhuma dimensão|
|evictedkeys7|Chaves removidas (fragmento 7)|Count|Total||Nenhuma dimensão|
|totalkeys7|Total de chaves (fragmento 7)|Count|Máximo||Nenhuma dimensão|
|expiredkeys7|Chaves expiradas (fragmento 7)|Count|Total||Nenhuma dimensão|
|usedmemory7|Memória usada (fragmento 7)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss7|Memória RSS usada (fragmento 7)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad7|Carga do servidor (fragmento 7)|Percent|Máximo||Nenhuma dimensão|
|cacheWrite7|Gravação no cache (fragmento 7)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead7|Leitura do cache (fragmento 7)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime7|CPU (fragmento 7)|Percent|Máximo||Nenhuma dimensão|
|connectedclients8|Clientes conectados (fragmento 8)|Count|Máximo||Nenhuma dimensão|
|totalcommandsprocessed8|Total de operações (fragmento 8)|Count|Total||Nenhuma dimensão|
|cachehits8|Acertos do cache (fragmento 8)|Count|Total||Nenhuma dimensão|
|cachemisses8|Erros de cache (fragmento 8)|Count|Total||Nenhuma dimensão|
|getcommands8|Gets (fragmento 8)|Count|Total||Nenhuma dimensão|
|setcommands8|Sets (fragmento 8)|Count|Total||Nenhuma dimensão|
|operationsPerSecond8|Operações por segundo (fragmento 8)|Count|Máximo||Nenhuma dimensão|
|evictedkeys8|Chaves removidas (fragmento 8)|Count|Total||Nenhuma dimensão|
|totalkeys8|Total de chaves (fragmento 8)|Count|Máximo||Nenhuma dimensão|
|expiredkeys8|Chaves expiradas (fragmento 8)|Count|Total||Nenhuma dimensão|
|usedmemory8|Memória usada (fragmento 8)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss8|Memória RSS usada (fragmento 8)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad8|Carga do servidor (fragmento 8)|Percent|Máximo||Nenhuma dimensão|
|cacheWrite8|Gravação no cache (fragmento 8)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead8|Leitura de cache (fragmento 8)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime8|CPU (fragmento 8)|Percent|Máximo||Nenhuma dimensão|
|connectedclients9|Clientes conectados (fragmento 9)|Count|Máximo||Nenhuma dimensão|
|totalcommandsprocessed9|Total de operações (fragmento 9)|Count|Total||Nenhuma dimensão|
|cachehits9|Acertos do cache (fragmento 9)|Count|Total||Nenhuma dimensão|
|cachemisses9|Erros de cache (fragmento 9)|Count|Total||Nenhuma dimensão|
|getcommands9|Gets (fragmento 9)|Count|Total||Nenhuma dimensão|
|setcommands9|Sets (fragmento 9)|Count|Total||Nenhuma dimensão|
|operationsPerSecond9|Operações por segundo (fragmento 9)|Count|Máximo||Nenhuma dimensão|
|evictedkeys9|Chaves removidas (fragmento 9)|Count|Total||Nenhuma dimensão|
|totalkeys9|Total de chaves (fragmento 9)|Count|Máximo||Nenhuma dimensão|
|expiredkeys9|Chaves expiradas (fragmento 9)|Count|Total||Nenhuma dimensão|
|usedmemory9|Memória usada (fragmento 9)|Bytes|Máximo||Nenhuma dimensão|
|usedmemoryRss9|Memória RSS usada (fragmento 9)|Bytes|Máximo||Nenhuma dimensão|
|serverLoad9|Carga do servidor (fragmento 9)|Percent|Máximo||Nenhuma dimensão|
|cacheWrite9|Gravação no cache (fragmento 9)|BytesPerSecond|Máximo||Nenhuma dimensão|
|cacheRead9|Leitura do cache (fragmento 9)|BytesPerSecond|Máximo||Nenhuma dimensão|
|percentProcessorTime9|CPU (fragmento 9)|Percent|Máximo||Nenhuma dimensão|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Average|A percentagem de unidades do computador alocadas que estão atualmente em utilização pela Máquina Virtual ou Máquinas Virtuais.|Nenhuma dimensão|
|Entrada de Rede|Entrada de Rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela Máquina Virtual ou Máquinas Virtuais (Tráfego de Entrada).|Nenhuma dimensão|
|Saída de Rede|Saída de Rede|Bytes|Total|O número de bytes saídos de todas as interfaces de rede pela(s) Máquina(s) Virtual(is) (Tráfego de Saída).|Nenhuma dimensão|
|Bytes Lidos de Disco/Seg|Leitura de Disco|BytesPerSecond|Average|Média de bytes lidos do disco durante o período de monitorização.|Nenhuma dimensão|
|Bytes Escritos em Disco/Seg|Escrita de Disco|BytesPerSecond|Average|Média de bytes escritos no disco durante o período de monitorização.|Nenhuma dimensão|
|Operações/Seg de Leitura do Disco|Operações/Seg de Leitura do Disco|CountPerSecond|Average|IOPS de Leitura do Disco.|Nenhuma dimensão|
|Operações/Seg de Escrita de Disco|Operações/Seg de Escrita de Disco|CountPerSecond|Average|IOPS de Escrita de Disco.|Nenhuma dimensão|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Average|A percentagem de unidades do computador alocadas que estão atualmente em utilização pela Máquina Virtual ou Máquinas Virtuais.|RoleInstanceId|
|Entrada de Rede|Entrada de Rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela Máquina Virtual ou Máquinas Virtuais (Tráfego de Entrada).|RoleInstanceId|
|Saída de Rede|Saída de Rede|Bytes|Total|O número de bytes saídos de todas as interfaces de rede pela(s) Máquina(s) Virtual(is) (Tráfego de Saída).|RoleInstanceId|
|Bytes Lidos de Disco/Seg|Leitura de Disco|BytesPerSecond|Average|Média de bytes lidos do disco durante o período de monitorização.|RoleInstanceId|
|Bytes Escritos em Disco/Seg|Escrita de Disco|BytesPerSecond|Average|Média de bytes escritos no disco durante o período de monitorização.|RoleInstanceId|
|Operações/Seg de Leitura do Disco|Operações/Seg de Leitura do Disco|CountPerSecond|Average|IOPS de Leitura do Disco.|RoleInstanceId|
|Operações/Seg de Escrita de Disco|Operações/Seg de Escrita de Disco|CountPerSecond|Average|IOPS de Escrita de Disco.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalCalls|Total de Chamadas|Count|Total|Número total de chamadas.|ApiName, OperationName, região|
|SuccessfulCalls|Chamadas com êxito|Count|Total|Número de chamadas com êxito.|ApiName, OperationName, região|
|TotalErrors|Total de Erros|Count|Total|Número total de chamadas com resposta de erro (código de resposta HTTP 4xx ou 5xx).|ApiName, OperationName, região|
|BlockedCalls|Chamadas Bloqueadas|Count|Total|Número de chamadas que excederam o limite da tarifa ou quota.|ApiName, OperationName, região|
|ServerErrors|Erros no Servidor|Count|Total|Número de chamadas com erro interno do serviço (código de resposta HTTP 5xx).|ApiName, OperationName, região|
|ClientErrors|Erros do Cliente|Count|Total|Número de chamadas com erro do lado do cliente (código de resposta HTTP 4xx).|ApiName, OperationName, região|
|Dados de|Entrada de Dados|Bytes|Total|Tamanho dos dados recebidos em bytes.|ApiName, OperationName, região|
|DataOut|Saída de Dados|Bytes|Total|Tamanho dos dados enviados em bytes.|ApiName, OperationName, região|
|Latência|Latência|Milissegundos|Average|Latência em milisegundos.|ApiName, OperationName, região|
|CharactersTranslated|Carateres Traduzidos|Count|Total|Número total de carateres na receção do pedido de texto.|ApiName, OperationName, região|
|CharactersTrained|Caracteres treinados|Count|Total|Número total de caracteres treinados.|ApiName, OperationName, região|
|SpeechSessionDuration|Duração da Sessão de Voz|Segundos|Total|Duração total da sessão de voz em segundos.|ApiName, OperationName, região|
|TotalTransactions|Total de Transações|Count|Total|Número total de transações.|Nenhuma dimensão|
|TotalTokenCalls|Chamadas de Token Totais|Count|Total|Número total de chamadas de token.|ApiName, OperationName, região|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades do computador alocadas que estão atualmente em utilização pela Máquina Virtual ou Máquinas Virtuais|Nenhuma dimensão|
|Entrada de Rede|Rede em Faturável|Bytes|Total|O número de bytes faturáveis recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada)|Nenhuma dimensão|
|Saída de Rede|Saída de rede Faturável|Bytes|Total|O número de bytes faturáveis de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída)|Nenhuma dimensão|
|Bytes de Leitura do Disco|Bytes de Leitura do Disco|Bytes|Total|Bytes lidos a partir do disco durante o período de monitorização|Nenhuma dimensão|
|Bytes de Escrita de Disco|Bytes de Escrita de Disco|Bytes|Total|Bytes escritos no disco durante o período de monitorização|Nenhuma dimensão|
|Operações/Seg de Leitura do Disco|Operações/Seg de Leitura do Disco|CountPerSecond|Average|IOPS de Leitura do Disco|Nenhuma dimensão|
|Operações/Seg de Escrita de Disco|Operações/Seg de Escrita de Disco|CountPerSecond|Average|IOPS de Escrita de Disco|Nenhuma dimensão|
|Créditos CPU Restantes|Créditos CPU Restantes|Count|Average|Número total de créditos disponíveis para rajada|Nenhuma dimensão|
|Créditos CPU Consumidos|Créditos CPU Consumidos|Count|Average|Número total de créditos consumidos pela Máquina Virtual|Nenhuma dimensão|
|Bytes Lidos por Disco/seg|Bytes Lidos do Disco de Dados/seg (Preterido)|CountPerSecond|Average|Bytes/Seg lidos a partir de um único disco durante o período de monitorização|SlotId|
|Bytes Escritos por Disco/seg|Bytes Escritos do Disco de Dados/Seg (Preterido)|CountPerSecond|Average|Bytes/Seg escritos para um único disco durante o período de monitorização|SlotId|
|Operações de Leitura Por Disco/Seg|Operações de Leitura do Disco de Dados/Seg (Preterido)|CountPerSecond|Average|Ler IOPS de um único disco durante o período de monitorização|SlotId|
|Operações de Escrita Por Disco/Seg|Operações de Escrita do Disco de Dados/Seg (Preterido)|CountPerSecond|Average|Escreva IOPS de um único disco durante o período de monitorização|SlotId|
|Por Disco de QD|Disco de Dados QD (Preterido)|Count|Average|Profundidade da Fila do Disco de Dados (ou Comprimento da Fila)|SlotId|
|Bytes de Leitura por Disco de SO/Seg.|Bytes/Seg da Leitura de Discos do SO (Preterido)|CountPerSecond|Average|Bytes/Seg lidos a partir de um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Bytes de Escrita por Disco de SO/Seg.|Bytes/Seg Escrita de Discos do SO (Preterido)|CountPerSecond|Average|Bytes/Seg escritos para um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Operações de Leitura por Disco de SO/seg|Operações/ Seg. da Leitura de Discos do SO (Preterido)|CountPerSecond|Average|Ler IOPS de um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Operações de Escrita Por Disco de SO/Seg|Operações/Seg Escrita de Discos do SO (Preterido)|CountPerSecond|Average|Escreva IOPS de um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Por Disco de Sistema Operativo QD|Disco do SO QD (Preterido)|Count|Average|Profundidade da Fila de Disco de Sistema Operativo (ou Comprimento da Fila)|Nenhuma dimensão|
|Bytes Lidos do Disco de Dados/seg|Bytes de Leitura de Disco de Dados/Seg (Pré-visualização)|CountPerSecond|Average|Bytes/Seg lidos a partir de um único disco durante o período de monitorização|LUN|
|Bytes Escritos do Disco de Dados/seg|Bytes de Escrita de Disco de Dados/Seg (Pré-visualização)|CountPerSecond|Average|Bytes/Seg escritos para um único disco durante o período de monitorização|LUN|
|Operações de Leitura em Disco de Dados/Seg|Operações de Leitura de Disco de Dados/Seg (Pré-visualização)|CountPerSecond|Average|Ler IOPS de um único disco durante o período de monitorização|LUN|
|Operações de Escrita em Disco de Dados/Seg|Operações de Escrita de Disco de Dados/Seg (Pré-visualização)|CountPerSecond|Average|Escreva IOPS de um único disco durante o período de monitorização|LUN|
|Profundidade da Fila do Disco de Dados|Profundidade da Fila do Discos de Dados (Pré-visualização)|Count|Average|Profundidade da Fila do Disco de Dados (ou Comprimento da Fila)|LUN|
|Bytes/seg da Leitura de Discos do SO|Bytes de Leitura de Disco de SO/Seg (Pré-visualização)|CountPerSecond|Average|Bytes/Seg lidos a partir de um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Bytes/sec da Escrita de Discos do SO|Bytes de Escrita de Disco de SO/Seg (Pré-visualização)|CountPerSecond|Average|Bytes/Seg escritos para um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Operações de Leitura de Disco de SO/Seg.|Operações de Leitura de Disco de SO/Seg (Pré-visualização)|CountPerSecond|Average|Ler IOPS de um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Operações de Escrita de Disco de SO/Seg|Operações de Escrita de Disco de SO/Seg (Pré-visualização)|CountPerSecond|Average|Escreva IOPS de um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Profundidade da Fila do Disco do SO|Profundidade da Fila do Disco do SO (Pré-visualização)|Count|Average|Profundidade da Fila de Disco de Sistema Operativo (ou Comprimento da Fila)|Nenhuma dimensão|
|Fluxos de Entrada|Fluxos de Entrada (Pré-visualização)|Count|Average|Os Fluxos de Entrada são o número de fluxos atuais na direção de entrada (tráfego a entrar da VM)|Nenhuma dimensão|
|Fluxos de Saída|Fluxos de Saída (Pré-visualização)|Count|Average|Os Fluxos de Saída são o número de fluxos atuais na direção de saída (tráfego a sair da VM)|Nenhuma dimensão|
|Taxa de Criação Máxima de Fluxos de Entrada|Taxa de Criação Máxima de Fluxos de Entrada (Pré-visualização)|CountPerSecond|Average|A taxa de criação máxima de fluxos de entrada (tráfego que vai para a VM)|Nenhuma dimensão|
|Taxa de Criação Máxima de Fluxos de Saída|Taxa de Criação Máxima de Fluxos de Saída (Pré-visualização)|CountPerSecond|Average|A taxa de criação máxima de fluxos de saída (tráfego que sai da VM)|Nenhuma dimensão|
|Acerto de Leitura na Cache do Disco de Dados Premium|Acerto de Leitura na Cache do Disco de Dados Premium (Pré-visualização)|Percent|Average|Acerto de Leitura na Cache do Disco de Dados Premium|LUN|
|Falha de Acerto de Leitura na Cache do Disco de Dados Premium|Falha de Acerto de Leitura na Cache do Disco de Dados Premium (Pré-visualização)|Percent|Average|Falha de Acerto de Leitura na Cache do Disco de Dados Premium|LUN|
|Acerto de Leitura na Cache do Disco do SO Premium|Acerto de Leitura na Cache do Disco do SO Premium (Pré-visualização)|Percent|Average|Acerto de Leitura na Cache do Disco do SO Premium|Nenhuma dimensão|
|Falha de Acerto de Leitura na Cache do Disco do SO Premium|Falha de Acerto de Leitura na Cache do Disco do SO Premium (Pré-visualização)|Percent|Average|Falha de Acerto de Leitura na Cache do Disco do SO Premium|Nenhuma dimensão|
|Total de rede|Total de rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela Máquina Virtual ou Máquinas Virtuais (Tráfego de Entrada)|Nenhuma dimensão|
|Total de saída de rede|Total de saída de rede|Bytes|Total|O número de bytes saídos de todas as interfaces de rede pela(s) Máquina(s) Virtual(is) (Tráfego de Saída)|Nenhuma dimensão|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades do computador alocadas que estão atualmente em utilização pela Máquina Virtual ou Máquinas Virtuais|VMName|
|Entrada de Rede|Rede em Faturável|Bytes|Total|O número de bytes faturáveis recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada)|VMName|
|Saída de Rede|Saída de rede Faturável|Bytes|Total|O número de bytes faturáveis de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída)|VMName|
|Bytes de Leitura do Disco|Bytes de Leitura do Disco|Bytes|Total|Bytes lidos a partir do disco durante o período de monitorização|VMName|
|Bytes de Escrita de Disco|Bytes de Escrita de Disco|Bytes|Total|Bytes escritos no disco durante o período de monitorização|VMName|
|Operações/Seg de Leitura do Disco|Operações/Seg de Leitura do Disco|CountPerSecond|Average|IOPS de Leitura do Disco|VMName|
|Operações/Seg de Escrita de Disco|Operações/Seg de Escrita de Disco|CountPerSecond|Average|IOPS de Escrita de Disco|VMName|
|Créditos CPU Restantes|Créditos CPU Restantes|Count|Average|Número total de créditos disponíveis para rajada|Nenhuma dimensão|
|Créditos CPU Consumidos|Créditos CPU Consumidos|Count|Average|Número total de créditos consumidos pela Máquina Virtual|Nenhuma dimensão|
|Bytes Lidos por Disco/seg|Bytes Lidos do Disco de Dados/seg (Preterido)|CountPerSecond|Average|Bytes/Seg lidos a partir de um único disco durante o período de monitorização|SlotId|
|Bytes Escritos por Disco/seg|Bytes Escritos do Disco de Dados/Seg (Preterido)|CountPerSecond|Average|Bytes/Seg escritos para um único disco durante o período de monitorização|SlotId|
|Operações de Leitura Por Disco/Seg|Operações de Leitura do Disco de Dados/Seg (Preterido)|CountPerSecond|Average|Ler IOPS de um único disco durante o período de monitorização|SlotId|
|Operações de Escrita Por Disco/Seg|Operações de Escrita do Disco de Dados/Seg (Preterido)|CountPerSecond|Average|Escreva IOPS de um único disco durante o período de monitorização|SlotId|
|Por Disco de QD|Disco de Dados QD (Preterido)|Count|Average|Profundidade da Fila do Disco de Dados (ou Comprimento da Fila)|SlotId|
|Bytes de Leitura por Disco de SO/Seg.|Bytes/Seg da Leitura de Discos do SO (Preterido)|CountPerSecond|Average|Bytes/Seg lidos a partir de um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Bytes de Escrita por Disco de SO/Seg.|Bytes/Seg Escrita de Discos do SO (Preterido)|CountPerSecond|Average|Bytes/Seg escritos para um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Operações de Leitura por Disco de SO/seg|Operações/ Seg. da Leitura de Discos do SO (Preterido)|CountPerSecond|Average|Ler IOPS de um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Operações de Escrita Por Disco de SO/Seg|Operações/Seg Escrita de Discos do SO (Preterido)|CountPerSecond|Average|Escreva IOPS de um único disco durante o período de monitorização para o disco do SO|Nenhuma dimensão|
|Por Disco de Sistema Operativo QD|Disco do SO QD (Preterido)|Count|Average|Profundidade da Fila de Disco de Sistema Operativo (ou Comprimento da Fila)|Nenhuma dimensão|
|Bytes Lidos do Disco de Dados/seg|Bytes de Leitura de Disco de Dados/Seg (Pré-visualização)|CountPerSecond|Average|Bytes/Seg lidos a partir de um único disco durante o período de monitorização|LUN, VMName|
|Bytes Escritos do Disco de Dados/seg|Bytes de Escrita de Disco de Dados/Seg (Pré-visualização)|CountPerSecond|Average|Bytes/Seg escritos para um único disco durante o período de monitorização|LUN, VMName|
|Operações de Leitura em Disco de Dados/Seg|Operações de Leitura de Disco de Dados/Seg (Pré-visualização)|CountPerSecond|Average|Ler IOPS de um único disco durante o período de monitorização|LUN, VMName|
|Operações de Escrita em Disco de Dados/Seg|Operações de Escrita de Disco de Dados/Seg (Pré-visualização)|CountPerSecond|Average|Escreva IOPS de um único disco durante o período de monitorização|LUN, VMName|
|Profundidade da Fila do Disco de Dados|Profundidade da Fila do Discos de Dados (Pré-visualização)|Count|Average|Profundidade da Fila do Disco de Dados (ou Comprimento da Fila)|LUN, VMName|
|Bytes/seg da Leitura de Discos do SO|Bytes de Leitura de Disco de SO/Seg (Pré-visualização)|CountPerSecond|Average|Bytes/Seg lidos a partir de um único disco durante o período de monitorização para o disco do SO|VMName|
|Bytes/sec da Escrita de Discos do SO|Bytes de Escrita de Disco de SO/Seg (Pré-visualização)|CountPerSecond|Average|Bytes/Seg escritos para um único disco durante o período de monitorização para o disco do SO|VMName|
|Operações de Leitura de Disco de SO/Seg.|Operações de Leitura de Disco de SO/Seg (Pré-visualização)|CountPerSecond|Average|Ler IOPS de um único disco durante o período de monitorização para o disco do SO|VMName|
|Operações de Escrita de Disco de SO/Seg|Operações de Escrita de Disco de SO/Seg (Pré-visualização)|CountPerSecond|Average|Escreva IOPS de um único disco durante o período de monitorização para o disco do SO|VMName|
|Profundidade da Fila do Disco do SO|Profundidade da Fila do Disco do SO (Pré-visualização)|Count|Average|Profundidade da Fila de Disco de Sistema Operativo (ou Comprimento da Fila)|VMName|
|Fluxos de Entrada|Fluxos de Entrada (Pré-visualização)|Count|Average|Os Fluxos de Entrada são o número de fluxos atuais na direção de entrada (tráfego a entrar da VM)|VMName|
|Fluxos de Saída|Fluxos de Saída (Pré-visualização)|Count|Average|Os Fluxos de Saída são o número de fluxos atuais na direção de saída (tráfego a sair da VM)|VMName|
|Taxa de Criação Máxima de Fluxos de Entrada|Taxa de Criação Máxima de Fluxos de Entrada (Pré-visualização)|CountPerSecond|Average|A taxa de criação máxima de fluxos de entrada (tráfego que vai para a VM)|VMName|
|Taxa de Criação Máxima de Fluxos de Saída|Taxa de Criação Máxima de Fluxos de Saída (Pré-visualização)|CountPerSecond|Average|A taxa de criação máxima de fluxos de saída (tráfego que sai da VM)|VMName|
|Acerto de Leitura na Cache do Disco de Dados Premium|Acerto de Leitura na Cache do Disco de Dados Premium (Pré-visualização)|Percent|Average|Acerto de Leitura na Cache do Disco de Dados Premium|LUN, VMName|
|Falha de Acerto de Leitura na Cache do Disco de Dados Premium|Falha de Acerto de Leitura na Cache do Disco de Dados Premium (Pré-visualização)|Percent|Average|Falha de Acerto de Leitura na Cache do Disco de Dados Premium|LUN, VMName|
|Acerto de Leitura na Cache do Disco do SO Premium|Acerto de Leitura na Cache do Disco do SO Premium (Pré-visualização)|Percent|Average|Acerto de Leitura na Cache do Disco do SO Premium|VMName|
|Falha de Acerto de Leitura na Cache do Disco do SO Premium|Falha de Acerto de Leitura na Cache do Disco do SO Premium (Pré-visualização)|Percent|Average|Falha de Acerto de Leitura na Cache do Disco do SO Premium|VMName|
|Total de rede|Total de rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela Máquina Virtual ou Máquinas Virtuais (Tráfego de Entrada)|VMName|
|Total de saída de rede|Total de saída de rede|Bytes|Total|O número de bytes saídos de todas as interfaces de rede pela(s) Máquina(s) Virtual(is) (Tráfego de Saída)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Os|Utilização da CPU|Count|Average|Utilização da CPU em todos os núcleos em milinúcleos.|containerName|
|MemoryUsage|Utilização da Memória|Bytes|Average|Utilização da memória total em bytes.|containerName|
|NetworkBytesReceivedPerSecond|Bytes de Rede Recebidos Por Segundo|Bytes|Average|Os bytes de rede recebidos por segundo.|Nenhuma dimensão|
|NetworkBytesTransmittedPerSecond|Bytes de Rede Transmitidos por Segundo|Bytes|Average|Os bytes de rede transmitidos por segundo.|Nenhuma dimensão|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalPullCount|Contagem total de pull|Count|Average|Número de pulls de imagem no total|Nenhuma dimensão|
|SuccessfulPullCount|Contagem de pull bem-sucedida|Count|Average|Número de pulls de imagem bem-sucedidos|Nenhuma dimensão|
|TotalPushCount|Contagem total de push|Count|Average|Número de envios por push de imagem no total|Nenhuma dimensão|
|SuccessfulPushCount|Contagem de push bem-sucedida|Count|Average|Número de Pushes de imagem bem-sucedidos|Nenhuma dimensão|
|RunDuration|Duração da execução|Milissegundos|Total|Duração da execução em milissegundos|Nenhuma dimensão|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Número total de núcleos de CPU disponíveis em um cluster gerenciado|Count|Total|Número total de núcleos de CPU disponíveis em um cluster gerenciado|Nenhuma dimensão|
|kube_node_status_allocatable_memory_bytes|Quantidade total de memória disponível em um cluster gerenciado|Bytes|Total|Quantidade total de memória disponível em um cluster gerenciado|Nenhuma dimensão|
|kube_pod_status_ready|Número de pods no estado pronto|Count|Total|Número de pods no estado pronto|namespace, Pod|
|kube_node_status_condition|Status para várias condições de nó|Count|Total|Status para várias condições de nó|condição, status, status2, nó|
|kube_pod_status_phase|Número de pods por fase|Count|Total|Número de pods por fase|fase, namespace, Pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights chamadas à API|Count|Total||Nenhuma dimensão|
|DCIMappingImportOperationSuccessfulLines|Mapeando linhas com êxito da operação de importação|Count|Total||Nenhuma dimensão|
|DCIMappingImportOperationFailedLines|Linhas com falha na operação de importação de mapeamento|Count|Total||Nenhuma dimensão|
|DCIMappingImportOperationTotalLines|Mapeamento de linhas totais da operação de importação|Count|Total||Nenhuma dimensão|
|DCIMappingImportOperationRuntimeInSeconds|Mapeando tempo de execução da operação de importação em segundos|Segundos|Total||Nenhuma dimensão|
|DCIOutboundProfileExportSucceeded|Exportação do perfil de saída bem-sucedida|Count|Total||Nenhuma dimensão|
|DCIOutboundProfileExportFailed|Falha na exportação do perfil de saída|Count|Total||Nenhuma dimensão|
|DCIOutboundProfileExportDuration|Duração da exportação do perfil de saída|Segundos|Total||Nenhuma dimensão|
|DCIOutboundKpiExportSucceeded|Exportação de KPI de saída bem-sucedida|Count|Total||Nenhuma dimensão|
|DCIOutboundKpiExportFailed|Falha na exportação do KPI de saída|Count|Total||Nenhuma dimensão|
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
|ImportASAValuesFailed|Falha na importação de valores ASA de conta|Count|Total||Nenhuma dimensão|
|ImportASAValuesSucceeded|Contagem de importação de valores ASA com êxito|Count|Total||Nenhuma dimensão|
|DCIProfilesCount|Contagem de instâncias de perfil|Count|Último||Nenhuma dimensão|
|DCIInteractionsPerMonthCount|A contagem de interações por mês|Count|Último||Nenhuma dimensão|
|DCIKpisCount|Contagem de KPIs|Count|Último||Nenhuma dimensão|
|DCISegmentsCount|Contagem de Segmentos|Count|Último||Nenhuma dimensão|
|DCIPredictiveMatchPoliciesCount|Contagem de correspondência preditiva|Count|Último||Nenhuma dimensão|
|DCIPredictionsCount|Contagem de previsão|Count|Último||Nenhuma dimensão|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|NICReadThroughput|Débito de Leitura (Rede)|BytesPerSecond|Average|A taxa de transferência de leitura da interface de rede no dispositivo no período de relatório para todos os volumes no gateway.|InstanceName|
|NICWriteThroughput|Escrever Débito (Rede)|BytesPerSecond|Average|A taxa de transferência de gravação da interface de rede no dispositivo no período de relatório para todos os volumes no gateway.|InstanceName|
|CloudReadThroughputPerShare|Débito de Transferência na Cloud (Partilha)|BytesPerSecond|Average|A taxa de transferência de download para o Azure de um compartilhamento durante o período de relatório.|Partilhar|
|CloudUploadThroughputPerShare|Débito de Carregamento na Cloud (Partilha)|BytesPerSecond|Average|A taxa de transferência de upload para o Azure de um compartilhamento durante o período de relatório.|Partilhar|
|BytesUploadedToCloudPerShare|Bytes Carregados na Cloud (Partilha)|Bytes|Average|O número total de bytes que são carregados no Azure de um compartilhamento durante o período de relatório.|Partilhar|
|TotalCapacity|Capacidade Total|Bytes|Average|Capacidade Total|Nenhuma dimensão|
|AvailableCapacity|Capacidade Disponível|Bytes|Average|A capacidade disponível em bytes durante o período de relatórios.|Nenhuma dimensão|
|CloudUploadThroughput|Débito de Carregamento na Cloud|BytesPerSecond|Average|A taxa de transferência de upload de nuvem para o Azure durante o período de relatório.|Nenhuma dimensão|
|CloudReadThroughput|Taxa de transferência de download da nuvem|BytesPerSecond|Average|A taxa de transferência de download de nuvem para o Azure durante o período de relatório.|Nenhuma dimensão|
|BytesUploadedToCloud|Bytes Carregados na Cloud (Dispositivo)|Bytes|Average|O número total de bytes que são carregados no Azure de um dispositivo durante o período de relatório.|Nenhuma dimensão|
|HyperVVirtualProcessorUtilization|Computação Edge - Percentagem da CPU|Percent|Average|Percentagem de utilização da CPU|InstanceName|
|HyperVMemoryUtilization|Computação Edge - Utilização de Memória|Percent|Average|Quantidade de RAM em Utilização|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|FailedRuns|Execuções Falhadas|Count|Total||pipelinename, ActivityName|
|SuccessfulRuns|Execuções Com Êxito|Count|Total||pipelinename, ActivityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PipelineFailedRuns|Métricas de execuções de pipeline com falha|Count|Total||FailureType, nome|
|PipelineSucceededRuns|Métricas de execuções de pipeline com êxito|Count|Total||FailureType, nome|
|ActivityFailedRuns|Métricas de execuções de atividade com falha|Count|Total||ActivityType, Pipelinename, Falhatype, nome|
|ActivitySucceededRuns|Métricas de execuções de atividades bem-sucedidas|Count|Total||ActivityType, Pipelinename, Falhatype, nome|
|TriggerFailedRuns|Métricas de execuções de gatilho com falha|Count|Total||Nome, Falhatype|
|TriggerSucceededRuns|Métricas de execuções de gatilho com êxito|Count|Total||Nome, Falhatype|
|IntegrationRuntimeCpuPercentage|Utilização da CPU do Integration Runtime|Percent|Average||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Memória disponível do Integration Runtime|Bytes|Average||IntegrationRuntimeName, NodeName|
|MaxAllowedResourceCount|Contagem máxima de entidades permitidas|Count|Máximo||Nenhuma dimensão|
|MaxAllowedFactorySizeInGbUnits|Tamanho máximo de fábrica permitido (unidade GB)|Count|Máximo||Nenhuma dimensão|
|ResourceCount|Contagem total de entidades|Count|Máximo||Nenhuma dimensão|
|FactorySizeInGbUnits|Tamanho total de fábrica (unidade GB)|Count|Máximo||Nenhuma dimensão|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|JobEndedSuccess|Trabalhos com êxito|Count|Total|Contagem de trabalhos bem-sucedidos.|Nenhuma dimensão|
|JobEndedFailure|Trabalhos com falha|Count|Total|Contagem de trabalhos com falha.|Nenhuma dimensão|
|JobEndedCancelled|Trabalhos cancelados|Count|Total|Contagem de trabalhos cancelados.|Nenhuma dimensão|
|JobAUEndedSuccess|Tempo de AU bem-sucedido|Segundos|Total|Tempo total de AU para trabalhos bem-sucedidos.|Nenhuma dimensão|
|JobAUEndedFailure|Tempo de AU com falha|Segundos|Total|Tempo total de AU para trabalhos com falha.|Nenhuma dimensão|
|JobAUEndedCancelled|Hora de AU cancelada|Segundos|Total|Tempo total de AU para trabalhos cancelados.|Nenhuma dimensão|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalStorage|Armazenamento Total|Bytes|Máximo|Quantidade total de dados armazenados na conta.|Nenhuma dimensão|
|Gravado em|Dados Escritos|Bytes|Total|Quantidade total de dados gravados na conta.|Nenhuma dimensão|
|DataRead|Leitura de dados|Bytes|Total|Quantidade total de dados lidos da conta.|Nenhuma dimensão|
|WriteRequests|Solicitações de gravação|Count|Total|Contagem de solicitações de gravação de dados para a conta.|Nenhuma dimensão|
|ReadRequests|Solicitações de leitura|Count|Total|Contagem de solicitações de leitura de dados para a conta.|Nenhuma dimensão|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentual de CPU|Percent|Average|Percentual de CPU|Nenhuma dimensão|
|memory_percent|Porcentagem de memória|Percent|Average|Porcentagem de memória|Nenhuma dimensão|
|io_consumption_percent|Porcentagem de e/s|Percent|Average|Porcentagem de e/s|Nenhuma dimensão|
|storage_percent|Porcentagem de armazenamento|Percent|Average|Porcentagem de armazenamento|Nenhuma dimensão|
|storage_used|Armazenamento utilizado|Bytes|Average|Armazenamento utilizado|Nenhuma dimensão|
|storage_limit|Limite de armazenamento|Bytes|Average|Limite de armazenamento|Nenhuma dimensão|
|serverlog_storage_percent|Porcentagem de armazenamento de log do servidor|Percent|Average|Porcentagem de armazenamento de log do servidor|Nenhuma dimensão|
|serverlog_storage_usage|Armazenamento de log do servidor usado|Bytes|Average|Armazenamento de log do servidor usado|Nenhuma dimensão|
|serverlog_storage_limit|Limite de armazenamento de log do servidor|Bytes|Average|Limite de armazenamento de log do servidor|Nenhuma dimensão|
|active_connections|Conexões ativas|Count|Average|Conexões ativas|Nenhuma dimensão|
|connections_failed|Ligações com Falhas|Count|Total|Ligações com Falhas|Nenhuma dimensão|
|seconds_behind_master|Atraso de replicação em segundos|Count|Average|Atraso de replicação em segundos|Nenhuma dimensão|
|backup_storage_used|Armazenamento de backup usado|Bytes|Average|Armazenamento de backup usado|Nenhuma dimensão|
|network_bytes_egress|Saída de Rede|Bytes|Total|Saída de rede entre conexões ativas|Nenhuma dimensão|
|network_bytes_ingress|Entrada de Rede|Bytes|Total|Entrada de rede em conexões ativas|Nenhuma dimensão|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentual de CPU|Percent|Average|Percentual de CPU|Nenhuma dimensão|
|memory_percent|Porcentagem de memória|Percent|Average|Porcentagem de memória|Nenhuma dimensão|
|io_consumption_percent|Porcentagem de e/s|Percent|Average|Porcentagem de e/s|Nenhuma dimensão|
|storage_percent|Porcentagem de armazenamento|Percent|Average|Porcentagem de armazenamento|Nenhuma dimensão|
|storage_used|Armazenamento utilizado|Bytes|Average|Armazenamento utilizado|Nenhuma dimensão|
|storage_limit|Limite de armazenamento|Bytes|Average|Limite de armazenamento|Nenhuma dimensão|
|serverlog_storage_percent|Porcentagem de armazenamento de log do servidor|Percent|Average|Porcentagem de armazenamento de log do servidor|Nenhuma dimensão|
|serverlog_storage_usage|Armazenamento de log do servidor usado|Bytes|Average|Armazenamento de log do servidor usado|Nenhuma dimensão|
|serverlog_storage_limit|Limite de armazenamento de log do servidor|Bytes|Average|Limite de armazenamento de log do servidor|Nenhuma dimensão|
|active_connections|Conexões ativas|Count|Average|Conexões ativas|Nenhuma dimensão|
|connections_failed|Ligações com Falhas|Count|Total|Ligações com Falhas|Nenhuma dimensão|
|seconds_behind_master|Atraso de replicação em segundos|Count|Average|Atraso de replicação em segundos|Nenhuma dimensão|
|backup_storage_used|Armazenamento de backup usado|Bytes|Average|Armazenamento de backup usado|Nenhuma dimensão|
|network_bytes_egress|Saída de Rede|Bytes|Total|Saída de rede entre conexões ativas|Nenhuma dimensão|
|network_bytes_ingress|Entrada de Rede|Bytes|Total|Entrada de rede em conexões ativas|Nenhuma dimensão|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentual de CPU|Percent|Average|Percentual de CPU|Nenhuma dimensão|
|memory_percent|Porcentagem de memória|Percent|Average|Porcentagem de memória|Nenhuma dimensão|
|io_consumption_percent|Porcentagem de e/s|Percent|Average|Porcentagem de e/s|Nenhuma dimensão|
|storage_percent|Porcentagem de armazenamento|Percent|Average|Porcentagem de armazenamento|Nenhuma dimensão|
|storage_used|Armazenamento utilizado|Bytes|Average|Armazenamento utilizado|Nenhuma dimensão|
|storage_limit|Limite de armazenamento|Bytes|Average|Limite de armazenamento|Nenhuma dimensão|
|serverlog_storage_percent|Porcentagem de armazenamento de log do servidor|Percent|Average|Porcentagem de armazenamento de log do servidor|Nenhuma dimensão|
|serverlog_storage_usage|Armazenamento de log do servidor usado|Bytes|Average|Armazenamento de log do servidor usado|Nenhuma dimensão|
|serverlog_storage_limit|Limite de armazenamento de log do servidor|Bytes|Average|Limite de armazenamento de log do servidor|Nenhuma dimensão|
|active_connections|Conexões ativas|Count|Average|Conexões ativas|Nenhuma dimensão|
|connections_failed|Ligações com Falhas|Count|Total|Ligações com Falhas|Nenhuma dimensão|
|backup_storage_used|Armazenamento de backup usado|Bytes|Average|Armazenamento de backup usado|Nenhuma dimensão|
|network_bytes_egress|Saída de Rede|Bytes|Total|Saída de rede entre conexões ativas|Nenhuma dimensão|
|network_bytes_ingress|Entrada de Rede|Bytes|Total|Entrada de rede em conexões ativas|Nenhuma dimensão|
|pg_replica_log_delay_in_seconds|Atraso de réplica|Segundos|Máximo|Atraso de réplica em segundos|Nenhuma dimensão|
|pg_replica_log_delay_in_bytes|Atraso máximo entre réplicas|Bytes|Máximo|Atraso em bytes da réplica mais defasante|Nenhuma dimensão|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentual de CPU|Percent|Average|Percentual de CPU|Nenhuma dimensão|
|memory_percent|Porcentagem de memória|Percent|Average|Porcentagem de memória|Nenhuma dimensão|
|IOPS|IOPS|Count|Average|Operações de e/s por segundo|Nenhuma dimensão|
|storage_percent|Porcentagem de armazenamento|Percent|Average|Porcentagem de armazenamento|Nenhuma dimensão|
|storage_used|Armazenamento utilizado|Bytes|Average|Armazenamento utilizado|Nenhuma dimensão|
|active_connections|Conexões ativas|Count|Average|Conexões ativas|Nenhuma dimensão|
|network_bytes_egress|Saída de Rede|Bytes|Total|Saída de rede entre conexões ativas|Nenhuma dimensão|
|network_bytes_ingress|Entrada de Rede|Bytes|Total|Entrada de rede em conexões ativas|Nenhuma dimensão|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentativas de envio de mensagem de telemetria|Count|Total|Número de mensagens de telemetria do dispositivo para a nuvem que tentaram ser enviadas para o Hub IoT|Nenhuma dimensão|
|d2c.telemetry.ingress.success|Mensagens de telemetria enviadas|Count|Total|Número de mensagens de telemetria do dispositivo para a nuvem enviadas com êxito para o Hub IoT|Nenhuma dimensão|
|c2d.commands.egress.complete.success|Comandos concluídos|Count|Total|Número de comandos da nuvem para o dispositivo concluídos com êxito pelo dispositivo|Nenhuma dimensão|
|c2d.commands.egress.abandon.success|Comandos abandonados|Count|Total|Número de comandos da nuvem para o dispositivo abandonados pelo dispositivo|Nenhuma dimensão|
|c2d.commands.egress.reject.success|Comandos rejeitados|Count|Total|Número de comandos da nuvem para o dispositivo rejeitados pelo dispositivo|Nenhuma dimensão|
|devices.totalDevices|Total de dispositivos (preterido)|Count|Total|Número de dispositivos registrados no Hub IoT|Nenhuma dimensão|
|devices.connectedDevices.allProtocol|Dispositivos conectados (preterido) |Count|Total|Número de dispositivos conectados ao seu hub IoT|Nenhuma dimensão|
|d2c.telemetry.egress.success|Roteamento: mensagens de telemetria entregues|Count|Total|O número de vezes que as mensagens foram entregues com êxito a todos os pontos de extremidade usando o roteamento do Hub IoT. Se uma mensagem for roteada para vários pontos de extremidade, esse valor aumentará em um para cada entrega bem-sucedida. Se uma mensagem for entregue ao mesmo ponto de extremidade várias vezes, esse valor aumentará em um para cada entrega bem-sucedida.|Nenhuma dimensão|
|d2c.telemetry.egress.dropped|Roteamento: mensagens de telemetria eliminadas |Count|Total|O número de vezes que as mensagens foram removidas pelo roteamento do Hub IoT devido a pontos de extremidade inativos. Esse valor não conta as mensagens entregues à rota de fallback, pois as mensagens ignoradas não são entregues lá.|Nenhuma dimensão|
|d2c.telemetry.egress.orphaned|Roteamento: mensagens de telemetria órfãs |Count|Total|O número de vezes que as mensagens ficaram órfãs pelo roteamento do Hub IoT porque não corresponderam a nenhuma regra de roteamento (incluindo a norma de fallback). |Nenhuma dimensão|
|d2c.telemetry.egress.invalid|Roteamento: mensagens de telemetria incompatíveis|Count|Total|O número de vezes que o roteamento do Hub IoT falhou ao entregar mensagens devido a uma incompatibilidade com o ponto de extremidade. Esse valor não inclui repetições.|Nenhuma dimensão|
|d2c.telemetry.egress.fallback|Roteamento: mensagens entregues ao fallback|Count|Total|O número de vezes que o roteamento do Hub IoT fornece mensagens ao ponto de extremidade associado à rota de fallback.|Nenhuma dimensão|
|d2c.endpoints.egress.eventHubs|Roteamento: mensagens entregues ao Hub de eventos|Count|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para pontos de extremidade do hub de eventos.|Nenhuma dimensão|
|d2c.endpoints.latency.eventHubs|Roteamento: latência de mensagem para o Hub de eventos|Milissegundos|Average|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem em um ponto de extremidade do hub de eventos.|Nenhuma dimensão|
|d2c.endpoints.egress.serviceBusQueues|Roteamento: mensagens entregues à fila do barramento de serviço|Count|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens aos pontos de extremidade da fila do barramento de serviço.|Nenhuma dimensão|
|d2c.endpoints.latency.serviceBusQueues|Roteamento: latência de mensagem para a fila do barramento de serviço|Milissegundos|Average|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade da fila do barramento de serviço.|Nenhuma dimensão|
|d2c.endpoints.egress.serviceBusTopics|Roteamento: mensagens entregues ao tópico do barramento de serviço|Count|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para pontos de extremidade de tópico do barramento de serviço.|Nenhuma dimensão|
|d2c.endpoints.latency.serviceBusTopics|Roteamento: latência de mensagem para o tópico do barramento de serviço|Milissegundos|Average|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade de tópico do barramento de serviço.|Nenhuma dimensão|
|d2c.endpoints.egress.builtIn.events|Roteamento: mensagens entregues a mensagens/eventos|Count|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para o ponto de extremidade interno (mensagens/eventos). Essa métrica só começa a funcionar quando o roteamento está https://aka.ms/iotrouting) habilitado (para o Hub IOT.|Nenhuma dimensão|
|d2c.endpoints.latency.builtIn.events|Roteamento: latência de mensagem para mensagens/eventos|Milissegundos|Average|A latência média (milissegundos) entre a entrada da mensagem e o Hub IoT e a entrada da mensagem de telemetria no ponto de extremidade interno (mensagens/eventos). Essa métrica só começa a funcionar quando o roteamento está https://aka.ms/iotrouting) habilitado (para o Hub IOT.|Nenhuma dimensão|
|d2c.endpoints.egress.storage|Roteamento: mensagens entregues ao armazenamento|Count|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para pontos de extremidade de armazenamento.|Nenhuma dimensão|
|d2c.endpoints.latency.storage|Roteamento: latência de mensagem para armazenamento|Milissegundos|Average|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade de armazenamento.|Nenhuma dimensão|
|d2c.endpoints.egress.storage.bytes|Roteamento: dados entregues ao armazenamento|Bytes|Total|A quantidade de dados (bytes) de roteamento do Hub IoT entregues aos pontos de extremidade de armazenamento.|Nenhuma dimensão|
|d2c.endpoints.egress.storage.blobs|Roteamento: BLOBs entregues ao armazenamento|Count|Total|O número de vezes que o roteamento do Hub IoT forneceu BLOBs para pontos de extremidade de armazenamento.|Nenhuma dimensão|
|EventGridDeliveries|Entregas da grade de eventos (visualização)|Count|Total|O número de eventos do Hub IoT publicados na grade de eventos. Use a dimensão de resultado para o número de solicitações bem-sucedidas e com falha. A dimensão EventType mostra o tipo de evento https://aka.ms/ioteventgrid) (.|Resultado, EventType|
|EventGridLatency|A latência média (milissegundos) de quando o evento do Hub IOT foi gerado quando o evento foi publicado na grade de eventos. Esse número é uma média entre todos os tipos de evento. Use a dimensão EventType para ver a latência de um tipo específico de evento.|EventType|
|d2c.twin.read.success|Leituras de entrelaçamento bem-sucedidas de dispositivos|Count|Total|A contagem de todas as leituras de entrelaças iniciadas pelo dispositivo bem-sucedidas.|Nenhuma dimensão|
|d2c.twin.read.failure|Leituras de entrelaçamento com falha de dispositivos|Count|Total|A contagem de todas as leituras de entrelaças iniciadas pelo dispositivo com falha.|Nenhuma dimensão|
|d2c.twin.read.size|Tamanho da resposta de leituras de entrelaçamento de dispositivos|Bytes|Average|A média, o mínimo e o máximo de todas as leituras de entrelaçadas iniciadas pelo dispositivo bem-sucedidas.|Nenhuma dimensão|
|d2c.twin.update.success|Atualizações de atualização com êxito de dispositivos|Count|Total|A contagem de todas as atualizações de atualização do dispositivo iniciadas com êxito.|Nenhuma dimensão|
|d2c.twin.update.failure|Atualizações de atualização de falha de dispositivos|Count|Total|A contagem de todas as atualizações de atualização do dispositivo iniciadas com falha.|Nenhuma dimensão|
|d2c.twin.update.size|Tamanho de atualizações de atualização de papel dos dispositivos|Bytes|Average|O tamanho médio, mínimo e máximo de todas as atualizações de atualização do dispositivo iniciadas com êxito.|Nenhuma dimensão|
|C2D. Methods. Success|Invocações de método diretos bem-sucedidas|Count|Total|A contagem de todas as chamadas de método diretas bem-sucedidas.|Nenhuma dimensão|
|c2d.methods.failure|Invocações de método direto com falha|Count|Total|A contagem de todas as chamadas de método diretas com falha.|Nenhuma dimensão|
|c2d.methods.requestSize|Tamanho da solicitação de invocações de método direto|Bytes|Average|A média, o mínimo e o máximo de todas as solicitações de método diretas bem-sucedidas.|Nenhuma dimensão|
|c2d.methods.responseSize|Tamanho da resposta de invocações de método diretas|Bytes|Average|A média, o mínimo e o máximo de todas as respostas de método diretas bem-sucedidas.|Nenhuma dimensão|
|c2d.twin.read.success|Leituras de cópia com êxito do back-end|Count|Total|A contagem de todas as leituras de cópia de bits iniciadas de back-end bem-sucedidas.|Nenhuma dimensão|
|c2d.twin.read.failure|Leituras de cópia com falha do back-end|Count|Total|A contagem de todas as leituras de cópia de bits iniciadas pelo back-end com falha.|Nenhuma dimensão|
|c2d.twin.read.size|Tamanho da resposta de leituras de cópia do back-end|Bytes|Average|A média, mín e máx de todas as leituras de cópia do back-end iniciadas com êxito.|Nenhuma dimensão|
|c2d.twin.update.success|Atualizações de cópia bem-sucedida do back-end|Count|Total|A contagem de todas as atualizações de cópia do back-end iniciadas com êxito.|Nenhuma dimensão|
|c2d.twin.update.failure|Atualizações de cópia com falha do back-end|Count|Total|A contagem de todas as atualizações de cópia de bits iniciadas pelo back-end com falha.|Nenhuma dimensão|
|c2d.twin.update.size|Tamanho das atualizações de cópia do back-end|Bytes|Average|O tamanho médio, mínimo e máximo de todas as atualizações de cópia do back-end iniciadas com êxito.|Nenhuma dimensão|
|twinQueries.success|Consultas de entrelaçamento bem-sucedidas|Count|Total|A contagem de todas as consultas de myup com êxito.|Nenhuma dimensão|
|twinQueries.failure|Consultas de entrelaçamento com falha|Count|Total|A contagem de todas as consultas de entrelaçamento com falha.|Nenhuma dimensão|
|twinQueries.resultSize|Tamanho do resultado de consultas de entrelaçamento|Bytes|Average|A média, o mínimo e o máximo do tamanho do resultado de todas as consultas de myup com êxito.|Nenhuma dimensão|
|jobs.createTwinUpdateJob.success|Criações bem-sucedidas de trabalhos de atualização de entrelaçamento|Count|Total|A contagem de todas as criações de trabalhos de atualização de entrelaçamento com êxito.|Nenhuma dimensão|
|jobs.createTwinUpdateJob.failure|Criações com falha de trabalhos de atualização de entrelaçamento|Count|Total|A contagem de todas as falhas na criação de trabalhos de atualização de entrelaçamento.|Nenhuma dimensão|
|jobs.createDirectMethodJob.success|Criações de trabalhos de invocação de método com êxito|Count|Total|A contagem de toda a criação bem-sucedida de trabalhos de invocação de método direto.|Nenhuma dimensão|
|jobs.createDirectMethodJob.failure|Criações com falha de trabalhos de invocação de método|Count|Total|A contagem de todas as falhas na criação de trabalhos de invocação de método direto.|Nenhuma dimensão|
|jobs.listJobs.success|Chamadas com êxito para listar trabalhos|Count|Total|A contagem de todas as chamadas bem-sucedidas para listar trabalhos.|Nenhuma dimensão|
|jobs.listJobs.failure|Chamadas com falha para listar trabalhos|Count|Total|A contagem de todas as chamadas com falha para listar trabalhos.|Nenhuma dimensão|
|jobs.cancelJob.success|Cancelamentos de trabalho bem-sucedidos|Count|Total|A contagem de todas as chamadas bem-sucedidas para cancelar um trabalho.|Nenhuma dimensão|
|jobs.cancelJob.failure|Cancelamentos de trabalho com falha|Count|Total|A contagem de todas as chamadas com falha para cancelar um trabalho.|Nenhuma dimensão|
|jobs.queryJobs.success|Consultas de trabalho com êxito|Count|Total|A contagem de todas as chamadas bem-sucedidas para os trabalhos de consulta.|Nenhuma dimensão|
|jobs.queryJobs.failure|Consultas de trabalho com falha|Count|Total|A contagem de todas as chamadas com falha para trabalhos de consulta.|Nenhuma dimensão|
|jobs.completed|Trabalhos concluídos|Count|Total|A contagem de todos os trabalhos concluídos.|Nenhuma dimensão|
|jobs.failed|Trabalhos com falha|Count|Total|A contagem de todos os trabalhos com falha.|Nenhuma dimensão|
|d2c.telemetry.ingress.sendThrottle|Número de erros de limitação|Count|Total|Número de erros de limitação devido a aceleradores de taxa de transferência do dispositivo|Nenhuma dimensão|
|dailyMessageQuotaUsed|Número total de mensagens usadas|Count|Average|Número total de mensagens usadas hoje. Esse é um valor cumulativo que é redefinido para zero às 00:00 UTC todos os dias.|Nenhuma dimensão|
|deviceDataUsage|Uso total de dados do dispositivo|Bytes|Total|Bytes transferidos de e para todos os dispositivos conectados ao IotHub|Nenhuma dimensão|
|totalDeviceCount|Total de dispositivos (visualização)|Count|Average|Número de dispositivos registrados no Hub IoT|Nenhuma dimensão|
|connectedDeviceCount|Dispositivos conectados (visualização)|Count|Average|Número de dispositivos conectados ao seu hub IoT|Nenhuma dimensão|
|Figura|Métricas de configuração|Count|Total|Métricas para operações de configuração|Nenhuma dimensão|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RegistrationAttempts|Tentativas de registro|Count|Total|Número de tentativas de registro de dispositivo|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Dispositivos atribuídos|Count|Total|Número de dispositivos atribuídos a um hub IoT|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Tentativas de atestado|Count|Total|Número de tentativas de atestado de dispositivo|ProvisioningServiceName, status, protocolo|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AvailableStorage|Armazenamento disponível|Bytes|Total|Armazenamento total disponível relatado à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|CassandraConnectionClosures|Fechamentos de conexão do Cassandra|Count|Total|Número de conexões Cassandra que foram fechadas, relatadas a uma granularidade de 1 minuto|Região, ClosureReason|
|CassandraRequestCharges|Encargos de solicitação do Cassandra|Count|Total|RUs consumido para solicitações Cassandra feitas|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Solicitações Cassandra|Count|Count|Número de solicitações Cassandra feitas|DatabaseName, CollectionName, região, OperationType, ResourceType, ErrorCode|
|DataUsage|Utilização de Dados|Bytes|Total|Uso total de dados relatados à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|DocumentCount|Contagem de documentos|Count|Total|Contagem total de documentos relatados à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|DocumentQuota|Cota de documentos|Bytes|Total|Cota de armazenamento total relatada à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|IndexUsage|Uso do índice|Bytes|Total|Uso total do índice relatado à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|MetadataRequests|Solicitações de metadados|Count|Count|Contagem de solicitações de metadados. Cosmos DB mantém a coleção de metadados do sistema para cada conta, que permite enumerar coleções, bancos de dados, etc e suas configurações, gratuitamente.|DatabaseName, CollectionName, região, StatusCode, |
|MongoRequestCharge|Encargo de solicitação do Mongo|Count|Total|Unidades de solicitação Mongo consumidas|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|MongoRequests|Solicitações Mongo|Count|Count|Número de solicitações Mongo feitas|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|ProvisionedThroughput|Débito Aprovisionado|Count|Máximo|Débito Aprovisionado|DatabaseName, CollectionName|
|ReplicationLatency|Latência de replicação P99|Milissegundos|Average|Latência de replicação P99 nas regiões de origem e de destino para a conta habilitada para Geografia|SourceRegion, TargetRegion|
|Indisponibilidade|Disponibilidade do serviço|Percent|Average|Disponibilidade de solicitações de conta em uma hora, granularidade de dia ou mês|Nenhuma dimensão|
|TotalRequestUnits|Total de unidades de solicitação|Count|Total|Unidades de solicitação consumidas|DatabaseName, CollectionName, região, StatusCode, OperationType|
|TotalRequests|Total de Pedidos|Count|Count|Número de solicitações feitas|DatabaseName, CollectionName, região, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|Count|Total|Total de eventos publicados neste tópico|Nenhuma dimensão|
|PublishFailCount|Publicar eventos com falha|Count|Total|Falha no total de eventos ao publicar neste tópico|ErrorType, erro|
|UnmatchedEventCount|Eventos sem correspondência|Count|Total|Total de eventos que não correspondem a nenhuma das assinaturas de evento deste tópico|Nenhuma dimensão|
|PublishSuccessLatencyInMs|Latência de êxito na publicação|Count|Total|Latência de êxito de publicação em milissegundos|Nenhuma dimensão|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|MatchedEventCount|Eventos de Correspondência|Count|Total|Total de eventos correspondidos a esta assinatura de evento|Nenhuma dimensão|
|DeliveryAttemptFailCount|Eventos com falha na entrega|Count|Total|O total de eventos não pôde ser entregue a esta assinatura de evento|Erro, ErrorType|
|DeliverySuccessCount|Eventos entregues|Count|Total|Total de eventos entregues a esta assinatura de evento|Nenhuma dimensão|
|DestinationProcessingDurationInMs|Duração do processamento de destino|Milissegundos|Average|Duração do processamento de destino em milissegundos|Nenhuma dimensão|
|DroppedEventCount|Eventos ignorados|Count|Total|Total de eventos descartados correspondentes a esta assinatura de evento|DropReason|
|DeadLetteredCount|Eventos inativos|Count|Total|Total de eventos mortos inativos correspondentes a esta assinatura de evento|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|Count|Total|Total de eventos publicados neste tópico|Nenhuma dimensão|
|PublishFailCount|Eventos com falha|Count|Total|Falha no total de eventos ao publicar neste tópico|ErrorType, erro|
|UnmatchedEventCount|Eventos sem correspondência|Count|Total|Total de eventos que não correspondem a nenhuma das assinaturas de evento deste tópico|Nenhuma dimensão|
|PublishSuccessLatencyInMs|Latência de êxito na publicação|Count|Total|Latência de êxito de publicação em milissegundos|Nenhuma dimensão|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfulRequests|Pedidos com Êxito|Count|Total|Pedidos Com Êxito do Microsoft.EventHub.|EntityName |
|ServerErrors|Erros de Servidor.|Count|Total|Erros de Servidor do Microsoft.EventHub.|EntityName |
|UserErrors|Erros do Utilizador.|Count|Total|Erros de Utilizador do Microsoft.EventHub.|EntityName |
|QuotaExceededErrors|Erros de Exceção de Quota.|Count|Total|Erros de Quota Excedida do Microsoft.EventHub.|EntityName |
|ThrottledRequests|Pedidos Limitados.|Count|Total|Pedidos Limitados do Microsoft.EventHub.|EntityName |
|IncomingRequests|Pedidos Recebidos|Count|Total|Pedidos Recebidos do Microsoft.EventHub.|EntityName|
|IncomingMessages|Mensagens Recebidas|Count|Total|Mensagens Recebidas do Microsoft.EventHub.|EntityName|
|OutgoingMessages|Mensagens Enviadas|Count|Total|Mensagens Enviadas do Microsoft.EventHub.|EntityName|
|IncomingBytes|Bytes Recebidos.|Bytes|Total|Bytes Recebidos do Microsoft.EventHub.|EntityName|
|OutgoingBytes|Bytes Enviados.|Bytes|Total|Bytes Enviados do Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Count|Average|Total de Ligações Ativas do Microsoft.EventHub.|Nenhuma dimensão|
|ConnectionsOpened|Ligações Abertas.|Count|Average|Ligações Abertas do Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Ligações Fechadas.|Count|Average|Ligações Fechadas do Microsoft.EventHub.|EntityName|
|CaptureBacklog|Registo de tarefas pendentes de Recolha.|Count|Total|Registo de tarefas pendentes de Recolha do Microsoft.EventHub.|EntityName|
|CapturedMessages|Mensagens Recolhidas.|Count|Total|Mensagens Recolhidas do Microsoft.EventHub.|EntityName|
|CapturedBytes|Bytes Recolhidos.|Bytes|Total|Bytes Recolhidos do Microsoft.EventHub.|EntityName|
|Size|Size|Bytes|Average|Tamanho de um EventHub em Bytes.|EntityName|
|INREQS|Solicitações de entrada (preteridas)|Count|Total|Total de solicitações de envio de entrada para um namespace (preterido)|Nenhuma dimensão|
|SUCCREQ|Solicitações com êxito (preteridas)|Count|Total|Total de solicitações bem-sucedidas para um namespace (preterido)|Nenhuma dimensão|
|FAILREQ|Solicitações com falha (preteridas)|Count|Total|Total de solicitações com falha para um namespace (preterido)|Nenhuma dimensão|
|SVRBSY|Erros do servidor ocupado (preterido)|Count|Total|Total de erros do servidor ocupado para um namespace (preterido)|Nenhuma dimensão|
|INTERR|Erros de servidor interno (preterido)|Count|Total|Total de erros internos do servidor para um namespace (preterido)|Nenhuma dimensão|
|MISCERR|Outros erros (preterido)|Count|Total|Total de solicitações com falha para um namespace (preterido)|Nenhuma dimensão|
|INMSGS|Mensagens de entrada (preteridas) (preteridas)|Count|Total|Total de mensagens de entrada para um namespace. Essa métrica foi preterida. Use a métrica de mensagens de entrada em vez disso (preterido)|Nenhuma dimensão|
|EHINMSGS|Mensagens Recebidas (preteridas)|Count|Total|Total de mensagens de entrada para um namespace (preterido)|Nenhuma dimensão|
|MENSAGENS INENVIADAS|Mensagens de saída (preteridas) (preteridas)|Count|Total|Total de mensagens de saída para um namespace. Essa métrica foi preterida. Use a métrica de mensagens de saída em vez disso (preterido)|Nenhuma dimensão|
|EHOUTMSGS|Mensagens Enviadas (preteridas)|Count|Total|Total de mensagens de saída para um namespace (preterido)|Nenhuma dimensão|
|EHINMBS|Bytes de entrada (preterido) (preterido)|Bytes|Total|Taxa de transferência de mensagem de entrada do hub de eventos para um namespace. Essa métrica foi preterida. Use a métrica de bytes de entrada em vez disso (preterido)|Nenhuma dimensão|
|EHINBYTES|Bytes recebidos (preteridos)|Bytes|Total|Taxa de transferência de mensagem de entrada do hub de eventos para um namespace (preterido)|Nenhuma dimensão|
|EHOUTMBS|Bytes de saída (preteridos) (preterido)|Bytes|Total|Taxa de transferência de mensagem de saída do hub de eventos para um namespace. Essa métrica foi preterida. Use a métrica de bytes de saída em vez disso (preterido)|Nenhuma dimensão|
|EHOUTBYTES|Bytes Enviados (preteridos)|Bytes|Total|Taxa de transferência de mensagem de saída do hub de eventos para um namespace (preterido)|Nenhuma dimensão|
|EHABL|Arquivar mensagens de pendências (preterido)|Count|Total|Mensagens de arquivamento do hub de eventos no backlog para um namespace (preterido)|Nenhuma dimensão|
|EHAMSGS|Arquivar mensagens (preterido)|Count|Total|Mensagens arquivadas do hub de eventos em um namespace (preterido)|Nenhuma dimensão|
|EHAMBS|Taxa de transferência de mensagem de arquivamento (preterido)|Bytes|Total|Taxa de transferência de mensagem arquivada do hub de eventos em um namespace (preterido)|Nenhuma dimensão|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfulRequests|Solicitações com êxito (versão prévia)|Count|Total|Pedidos Com Êxito do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|ServerErrors|Erros de Servidor. (Pré-visualização)|Count|Total|Erros de Servidor do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|UserErrors|Erros do Utilizador. (Pré-visualização)|Count|Total|Erros de Utilizador do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|QuotaExceededErrors|Erros de Exceção de Quota. (Pré-visualização)|Count|Total|Erros de Quota Excedida do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|ThrottledRequests|Pedidos Limitados. (Pré-visualização)|Count|Total|Pedidos Limitados do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|IncomingRequests|Solicitações de entrada (versão prévia)|Count|Total|Pedidos Recebidos do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|IncomingMessages|Mensagens de entrada (versão prévia)|Count|Total|Mensagens Recebidas do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|OutgoingMessages|Mensagens de saída (versão prévia)|Count|Total|Mensagens Enviadas do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|IncomingBytes|Bytes Recebidos. (Pré-visualização)|Bytes|Total|Bytes Recebidos do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|OutgoingBytes|Bytes Enviados. (Pré-visualização)|Bytes|Total|Bytes Enviados do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|ActiveConnections|ActiveConnections (visualização)|Count|Average|Total de Ligações Ativas do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|ConnectionsOpened|Ligações Abertas. (Pré-visualização)|Count|Average|Ligações Abertas do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|ConnectionsClosed|Ligações Fechadas. (Pré-visualização)|Count|Average|Ligações Fechadas do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|CaptureBacklog|Registo de tarefas pendentes de Recolha. (Pré-visualização)|Count|Total|Registo de tarefas pendentes de Recolha do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|CapturedMessages|Mensagens Recolhidas. (Pré-visualização)|Count|Total|Mensagens Recolhidas do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|CapturedBytes|Bytes Recolhidos. (Pré-visualização)|Bytes|Total|Bytes Recolhidos do Microsoft.EventHub. (Pré-visualização)|Nenhuma dimensão|
|CPU|CPU (versão prévia)|Percent|Máximo|Utilização da CPU para o Cluster de Hub de Eventos enquanto percentagem|Role|
|AvailableMemory|Memória disponível (versão prévia)|Count|Máximo|Memória disponível para o Cluster de Hub de Eventos em bytes|Role|

## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/clusters

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|GatewayRequests|Solicitações de gateway|Count|Total|Número de solicitações de gateway|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Solicitações de gateway categorizadas|Count|Total|Número de solicitações de gateway por categorias (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Número de trabalhadores ativos|Count|Máximo|Número de trabalhadores ativos|ClusterDnsName, Metricname|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ObservedMetricValue|Valor de Métrica observado|Count|Average|O valor calculado pelo dimensionamento automático quando este foi executado|MetricTriggerSource|
|MetricThreshold|Limiar de Métrica|Count|Average|O limiar de dimensionamento automático configurado quando este foi executado.|MetricTriggerRule|
|ObservedCapacity|Capacidade observada|Count|Average|A capacidade reportada para dimensionamento automático quando este foi executado.|Nenhuma dimensão|
|ScaleActionsInitiated|Ações de Dimensionamento Iniciadas|Count|Total|A direção da operação de dimensionamento.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Visualização pública)

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Disponibilidade|Percentagem|Average|Percentual de testes de disponibilidade concluídos com êxito|availabilityResult/nome, availabilityResult/local|
|availabilityResults/contagem|Testes de disponibilidade|Count|Count|Contagem de testes de disponibilidade|availabilityResult/nome, availabilityResult/local, availabilityResult/êxito|
|availabilityResults/duration|Duração do teste de disponibilidade|Milissegundos|Average|Duração do teste de disponibilidade|availabilityResult/nome, availabilityResult/local, availabilityResult/êxito|
|browserTimings/networkDuration|Tempo de ligação da rede de carregamento de páginas|Milissegundos|Average|Tempo entre a solicitação de usuário e a conexão de rede. Inclui a pesquisa de DNS e a conexão de transporte.|Nenhuma dimensão|
|browserTimings/processingDuration|Tempo de processamento do cliente|Milissegundos|Average|Tempo entre o recebimento do último byte de um documento até que o DOM seja carregado. As solicitações assíncronas ainda podem estar sendo processadas.|Nenhuma dimensão|
|browserTimings/receiveDuration|Tempo de resposta de receção|Milissegundos|Average|Tempo entre o primeiro e último bytes ou até à interrupção da ligação.|Nenhuma dimensão|
|browserTimings/sendDuration|Tempo de resposta de envio|Milissegundos|Average|Tempo entre a ligação de rede e a receção do primeiro byte.|Nenhuma dimensão|
|browserTimings/totalDuration|Tempo de carregamento de páginas do browser|Milissegundos|Average|Tempo entre o pedido do utilizador e o carregamento de DOM, de folhas de estilos, de scripts e de imagens.|Nenhuma dimensão|
|dependências/contagem|Chamadas de dependência|Count|Count|Número de chamadas efetuadas pela aplicação aos recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/êxito, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|dependências/duração|Duração da dependência|Milissegundos|Average|Duração das chamadas efetuadas pela aplicação aos recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/êxito, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|dependências/com falha|Falhas de chamada de dependência|Count|Count|Número de chamadas de dependência falhadas efetuadas pela aplicação para os recursos externos.|dependência/tipo, dependência/performanceBucket, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|pageViews/contagem|Visualizações da página|Count|Count|Número de vistas de página.|operação/sintética|
|pageViews/duração|Duração de carregamento da visualização de página|Milissegundos|Average|Duração de carregamento da visualização de página|operação/sintética|
|performanceCounters/requestExecutionTime|Tempo de execução do pedido HTTP|Milissegundos|Average|Tempo de execução do pedido mais recente.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Pedidos HTTP na fila de aplicação|Count|Average|Tamanho da fila de pedidos da aplicação.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Taxa de pedido HTTP|CountPerSecond|Average|Classificação de todos os pedidos da aplicação por segundo do ASP.NET.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Taxa de exceção|CountPerSecond|Average|Contagem de exceções processadas e não processadas comunicadas ao Windows, incluindo exceções .NET e exceções não geridas que foram convertidas em exceções .NET.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Velocidade de E/S do processo|BytesPerSecond|Average|Total de bytes por segundo lidos e escritos em ficheiros, na rede e em dispositivos.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|CPU do processo|Percent|Average|A porcentagem de tempo decorrido em que todos os threads de processo usaram o processador para executar instruções. Isso pode variar entre 0 e 100. Essa métrica indica o desempenho apenas do processo W3wp.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Tempo do processador|Percent|Average|Percentagem de tempo que o processador despende em threads não inativos.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Memória disponível|Bytes|Average|Memória física imediatamente disponível para alocação a um processo ou para utilização do sistema.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Bytes privados do processo|Bytes|Average|Memória atribuída exclusivamente aos processos da aplicação monitorizada.|cloud/roleInstance|
|solicitações/duração|Tempo de resposta do servidor|Milissegundos|Average|Tempo entre a receção de um pedido HTTP e a conclusão do envio da resposta.|solicitação/performanceBucket, solicitação/resultCode, operação/sintética, nuvem/roleInstance, solicitação/êxito, nuvem/roleName|
|solicitações/contagem|Pedidos ao servidor|Count|Count|Número de pedidos HTTP concluídos.|solicitação/performanceBucket, solicitação/resultCode, operação/sintética, nuvem/roleInstance, solicitação/êxito, nuvem/roleName|
|solicitações/com falha|Pedidos falhados|Count|Count|Contagem de solicitações HTTP marcadas como com falha. Na maioria dos casos, essas são solicitações com um código de resposta > = 400 e não iguais a 401.|solicitação/performanceBucket, solicitação/resultCode, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|solicitações/taxa|Taxa de solicitações do servidor|CountPerSecond|Average|Taxa de solicitações de servidor por segundo|solicitação/performanceBucket, solicitação/resultCode, operação/sintética, nuvem/roleInstance, solicitação/êxito, nuvem/roleName|
|exceções/contagem|Exceções|Count|Count|Contagem combinada de todas as exceções não identificadas.|Cloud/roleName, nuvem/roleInstance, cliente/tipo|
|exceções/navegador|Exceções do browser|Count|Count|Número de exceções não identificadas emitidas no browser.|Nenhuma dimensão|
|exceções/servidor|Exceções de servidor|Count|Count|Número de exceções não identificadas emitidas na aplicação de servidor.|Cloud/roleName, nuvem/roleInstance|
|rastreamentos/contagem|Rastreios|Count|Count|Contagem do documento de rastreio|Trace/nível, operação/sintético, nuvem/roleName, nuvem/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServiceApiHit|Total de Cliques na API do Serviço|Count|Count|Número total de cliques na API do serviço|ActivityType, ActivityName|
|ServiceApiLatency|Latência Global da API do Serviço|Milissegundos|Average|Latência global dos pedidos da API do serviço|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Resultados de Api de Serviço Total|Count|Count|Número total de resultados da API do serviço|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/clusters

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CacheUtilization|Utilização de cache|Percent|Average|Nível de utilização no escopo do cluster|Nenhum|
|QueryDuration|Duração da consulta|Milissegundos|Average|Duração das consultas em segundos|Estado de consulta|
|IngestionUtilization|Utilização de ingestão|Percent|Average|Taxa de Slots de ingestão usados no cluster|Nenhum|
|KeepAlive|Keep Alive|Count|Average|Verificação de sanidade indica que o cluster responde às consultas|Nenhum|
|IngestionVolumeInMB|Volume de ingestão (em MB)|Count|Total|Volume geral de dados ingeridos para o cluster (em MB)|Base de Dados|
|IngestionLatencyInSeconds|Latência de ingestão (em segundos)|Segundos|Average|O tempo de ingestão da origem (por exemplo, a mensagem está no EventHub) para o cluster em segundos|Nenhum|
|EventProcessedForEventHubs|Eventos processados (para hubs de eventos)|Count|Total|Número de eventos processados pelo cluster ao ingerir o Hub de eventos|Nenhum|
|IngestionResult|Resultado da ingestão|Count|Count|Número de operações de ingestão|Estado|
|CPU|CPU|Percent|Average|Nível de utilização da CPU|Nenhum|
| ContinuousExportNumOfRecordsExported | Número de registros exportados na exportação contínua | Count | Total | Número de registros exportados para cada artefato de armazenamento gravado durante a operação de exportação  | Nenhum |
| ExportUtilization | Utilização da exportação | Percent | Máximo | Utilização da exportação | Nenhum |
| ContinuousExportPendingCount | Contagem de exportação contínua pendente | Count | Máximo | O número de trabalhos de exportação contínuos pendentes prontos para execução | Nenhum |
| ContinuousExportMaxLatenessMinutes | Minutos de atraso máximo de exportação contínua | Count | Máximo | O tempo máximo em minutos de todas as exportações contínuas que estão pendentes e prontas para execução | Nenhum |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Utilização|Utilização|Count|Count|Contagem de chamadas de API|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RunsStarted|Execuções Iniciadas|Count|Total|Número de execuções de fluxo de trabalho iniciadas.|Nenhuma dimensão|
|RunsCompleted|Execuções Concluídas|Count|Total|Número de execuções de fluxo de trabalho concluídas.|Nenhuma dimensão|
|RunsSucceeded|Execuções com Êxito|Count|Total|Número de execuções de fluxo de trabalho com êxito.|Nenhuma dimensão|
|RunsFailed|Execuções Falhadas|Count|Total|Número de execuções de fluxo de trabalho falhadas.|Nenhuma dimensão|
|RunsCancelled|Execuções canceladas|Count|Total|Número de execuções de fluxo de trabalho canceladas.|Nenhuma dimensão|
|RunLatency|Latência de Execução|Segundos|Average|Latência de execuções de fluxo de trabalho concluídas.|Nenhuma dimensão|
|RunSuccessLatency|Latência de Execução com Êxito|Segundos|Average|Latência de execuções de fluxo de trabalho com êxito.|Nenhuma dimensão|
|RunThrottledEvents|Eventos Limitados em relação à Execução|Count|Total|Número de eventos limitados em relação à ação ou acionador de fluxo de trabalho.|Nenhuma dimensão|
|RunFailurePercentage|Percentagem da Falha de Execução|Percent|Total|Percentagem das execuções de fluxo de trabalho falhadas.|Nenhuma dimensão|
|ActionsStarted|Ações Iniciadas |Count|Total|Número de ações de fluxo de trabalho iniciadas.|Nenhuma dimensão|
|ActionsCompleted|Ações Concluídas |Count|Total|Número de ações de fluxo de trabalho concluídas.|Nenhuma dimensão|
|ActionsSucceeded|Ações com Êxito |Count|Total|Número de ações de fluxo de trabalho com êxito.|Nenhuma dimensão|
|ActionsFailed|Ações Falhadas|Count|Total|Número de ações de fluxo de trabalho falhadas.|Nenhuma dimensão|
|ActionsSkipped|Ações Ignoradas |Count|Total|Número de ações de fluxo de trabalho ignoradas.|Nenhuma dimensão|
|ActionLatency|Latência da Ação |Segundos|Average|Latência das ações de fluxo de trabalho concluídas.|Nenhuma dimensão|
|ActionSuccessLatency|Latência de Ações com Êxito |Segundos|Average|Latência de ações de fluxo de trabalho com êxito.|Nenhuma dimensão|
|ActionThrottledEvents|Eventos Limitados em relação a Ação|Count|Total|Número de eventos limitados em relação à ação de fluxo de trabalho.|Nenhuma dimensão|
|TriggersStarted|Acionadores Iniciados |Count|Total|Número de acionadores de fluxo de trabalho iniciados.|Nenhuma dimensão|
|TriggersCompleted|Acionadores Concluídos |Count|Total|Número de acionadores de fluxo de trabalho concluídos.|Nenhuma dimensão|
|TriggersSucceeded|Acionadores Com Êxito |Count|Total|Número de acionadores de fluxo de trabalho com êxito.|Nenhuma dimensão|
|TriggersFailed|Acionadores Com Falha |Count|Total|Número de acionadores de fluxo de trabalho falhados.|Nenhuma dimensão|
|TriggersSkipped|Acionadores Ignorados|Count|Total|Número de acionadores de fluxo de trabalho ignorados.|Nenhuma dimensão|
|TriggersFired|Acionadores Disparados |Count|Total|Número de acionadores de fluxo de trabalho desencadeados.|Nenhuma dimensão|
|TriggerLatency|Latência de Acionador |Segundos|Average|Latência de acionadores de fluxo de trabalho concluídos.|Nenhuma dimensão|
|TriggerFireLatency|Latência de Disparo de Acionador |Segundos|Average|Latência de acionadores de fluxo de trabalho desencadeados.|Nenhuma dimensão|
|TriggerSuccessLatency|Latência de Êxito de Acionador |Segundos|Average|Latência de acionadores de fluxo de trabalho com êxito.|Nenhuma dimensão|
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
|RunsCancelled|Execuções canceladas|Count|Total|Número de execuções de fluxo de trabalho canceladas.|Nenhuma dimensão|
|RunLatency|Latência de Execução|Segundos|Average|Latência de execuções de fluxo de trabalho concluídas.|Nenhuma dimensão|
|RunSuccessLatency|Latência de Execução com Êxito|Segundos|Average|Latência de execuções de fluxo de trabalho com êxito.|Nenhuma dimensão|
|RunThrottledEvents|Eventos Limitados em relação à Execução|Count|Total|Número de eventos limitados em relação à ação ou acionador de fluxo de trabalho.|Nenhuma dimensão|
|RunStartThrottledEvents|Executar eventos restritos de início|Count|Total|Número de eventos restritos de início de fluxo de trabalho.|Nenhuma dimensão|
|RunFailurePercentage|Percentagem da Falha de Execução|Percent|Total|Percentagem das execuções de fluxo de trabalho falhadas.|Nenhuma dimensão|
|ActionsStarted|Ações Iniciadas |Count|Total|Número de ações de fluxo de trabalho iniciadas.|Nenhuma dimensão|
|ActionsCompleted|Ações Concluídas |Count|Total|Número de ações de fluxo de trabalho concluídas.|Nenhuma dimensão|
|ActionsSucceeded|Ações com Êxito |Count|Total|Número de ações de fluxo de trabalho com êxito.|Nenhuma dimensão|
|ActionsFailed|Ações Falhadas |Count|Total|Número de ações de fluxo de trabalho falhadas.|Nenhuma dimensão|
|ActionsSkipped|Ações Ignoradas |Count|Total|Número de ações de fluxo de trabalho ignoradas.|Nenhuma dimensão|
|ActionLatency|Latência da Ação |Segundos|Average|Latência das ações de fluxo de trabalho concluídas.|Nenhuma dimensão|
|ActionSuccessLatency|Latência de Ações com Êxito |Segundos|Average|Latência de ações de fluxo de trabalho com êxito.|Nenhuma dimensão|
|ActionThrottledEvents|Eventos Limitados em relação a Ação|Count|Total|Número de eventos limitados em relação à ação de fluxo de trabalho.|Nenhuma dimensão|
|TriggersStarted|Acionadores Iniciados |Count|Total|Número de acionadores de fluxo de trabalho iniciados.|Nenhuma dimensão|
|TriggersCompleted|Acionadores Concluídos |Count|Total|Número de acionadores de fluxo de trabalho concluídos.|Nenhuma dimensão|
|TriggersSucceeded|Acionadores Com Êxito |Count|Total|Número de acionadores de fluxo de trabalho com êxito.|Nenhuma dimensão|
|TriggersFailed|Acionadores Com Falha |Count|Total|Número de acionadores de fluxo de trabalho falhados.|Nenhuma dimensão|
|TriggersSkipped|Acionadores Ignorados|Count|Total|Número de acionadores de fluxo de trabalho ignorados.|Nenhuma dimensão|
|TriggersFired|Acionadores Disparados |Count|Total|Número de acionadores de fluxo de trabalho desencadeados.|Nenhuma dimensão|
|TriggerLatency|Latência de Acionador |Segundos|Average|Latência de acionadores de fluxo de trabalho concluídos.|Nenhuma dimensão|
|TriggerFireLatency|Latência de Disparo de Acionador |Segundos|Average|Latência de acionadores de fluxo de trabalho desencadeados.|Nenhuma dimensão|
|TriggerSuccessLatency|Latência de Êxito de Acionador |Segundos|Average|Latência de acionadores de fluxo de trabalho com êxito.|Nenhuma dimensão|
|TriggerThrottledEvents|Eventos Limitados de Acionador|Count|Total|Número de eventos limitados em relação ao acionador de fluxo de trabalho.|Nenhuma dimensão|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Utilização do Processador de Fluxo de Trabalho para o Ambiente de Serviço de Integração|Percent|Average|Utilização do processador de fluxo de trabalho para o ambiente de serviço de integração.|Nenhuma dimensão|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Utilização da Memória de Fluxo de Trabalho para o Ambiente de Serviço de Integração|Percent|Average|Utilização da memória de fluxo de trabalho para o ambiente de serviço de integração.|Nenhuma dimensão|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Utilização do Processador do Conector para o Ambiente de Serviço de Integração|Percent|Average|Utilização do processador do conector para o ambiente de serviço de integração.|Nenhuma dimensão|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Utilização da Memória do Conector para o Ambiente de Serviço de Integração|Percent|Average|Utilização da memória do conector para o ambiente de serviço de integração.|Nenhuma dimensão|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Execuções concluídas|Execuções concluídas|Count|Total|Número de execuções concluídas com êxito para este espaço de trabalho|Cenário|
|Execuções iniciadas|Execuções iniciadas|Count|Total|Número de execuções iniciadas para este espaço de trabalho|Cenário|
|Execuções Falhadas|Execuções Falhadas|Count|Total|Número de execuções com falha para este espaço de trabalho|Cenário|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Utilização|Utilização|Count|Count|Contagem de chamadas de API|ApiCategory, ApiName, ResultType, ResponseCode|
|Disponibilidade|Disponibilidade|Percentagem|Average|Disponibilidade das APIs|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AverageOtherLatency|Latência média de outras|MS/op|Average|Média de outra latência (que não é leitura ou gravação) em milissegundos por operação|Nenhuma dimensão|
|AverageReadLatency|Latência média de leitura|MS/op|Average|Média de latência de leitura em milissegundos por operação|Nenhuma dimensão|
|AverageTotalLatency|Latência total média|MS/op|Average|Latência total média em milissegundos por operação|Nenhuma dimensão|
|AverageWriteLatency|Latência média de gravação|MS/op|Average|Latência média de gravação em milissegundos por operação|Nenhuma dimensão|
|FilesystemOtherOps|Outras operações do sistema de arquivos|Ops|Average|Número de outras operações do sistema de arquivos (que não são de leitura ou gravação)|Nenhuma dimensão|
|FilesystemReadOps|Operações de leitura do sistema de arquivos|Ops|Average|Número de operações de leitura do sistema de arquivos|Nenhuma dimensão|
|FilesystemTotalOps|Total de operações do sistema de arquivos|Ops|Average|Soma de todas as operações do sistema de arquivos|Nenhuma dimensão|
|FilesystemWriteOps|Operações de gravação do sistema de arquivos|Ops|Average|Número de operações de gravação do sistema de arquivos|Nenhuma dimensão|
|IoBytesPerOtherOps|Bytes de e/s por outra Ops|bytes/op|Average|Número de bytes de entrada/saída por outras operações (que não são de leitura ou gravação)|Nenhuma dimensão|
|IoBytesPerReadOps|Bytes de e/s por operações de leitura|bytes/op|Average|Número de bytes de entrada/saída por operação de leitura|Nenhuma dimensão|
|IoBytesPerTotalOps|Bytes de e/s por operação em todas as operações|bytes/op|Average|Soma de todas as operações de bytes in/out|Nenhuma dimensão|
|IoBytesPerWriteOps|Bytes de e/s por operações de gravação|bytes/op|Average|Número de bytes de entrada/saída por operação de gravação|Nenhuma dimensão|
|OtherIops|Outro IOPS|operações/segundo|Average|Outra operação de entrada/saída por segundo|Nenhuma dimensão|
|OtherThroughput|Outra taxa de transferência|MBps|Average|Outra taxa de transferência (que não é de leitura ou gravação) em megabytes por segundo|Nenhuma dimensão|
|ReadIops|IOPS de leitura|operações/segundo|Average|Operações de leitura/saída por segundo|Nenhuma dimensão|
|ReadThroughput|Taxa de transferência de leitura|MBps|Average|Taxa de transferência de leitura em megabytes por segundo|Nenhuma dimensão|
|TotalIops|IOPS total|operações/segundo|Average|Soma de todas as operações de entrada/saída por segundo|Nenhuma dimensão|
|TotalThroughput|Débito total|MBps|Average|Soma de toda a taxa de transferência em megabytes por segundo|Nenhuma dimensão|
|VolumeAllocatedSize|Tamanho alocado do volume|bytes|Average|Tamanho alocado do volume (não os bytes reais usados)|Nenhuma dimensão|
|VolumeLogicalSize|Tamanho lógico do volume|bytes|Average|Tamanho lógico do volume (bytes usados)|Nenhuma dimensão|
|VolumeSnapshotSize|Tamanho do instantâneo de volume|bytes|Average|Tamanho de todos os instantâneos no volume|Nenhuma dimensão|
|WriteIops|IOPS de gravação|operações/segundo|Average|Operações de gravação/saída por segundo|Nenhuma dimensão|
|WriteThroughput|Taxa de transferência de gravação|MBps|Average|Taxa de transferência de gravação em megabytes por segundo|Nenhuma dimensão|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Tamanho alocado do pool de volumes|bytes|Average|Tamanho alocado do pool (não os bytes reais usados)|Nenhuma dimensão|
|VolumePoolAllocatedUsed|Pool de volumes alocado usado|bytes|Average|Tamanho usado para o pool alocado|Nenhuma dimensão|
|VolumePoolTotalLogicalSize|Tamanho lógico total do pool de volumes|bytes|Average|Soma do tamanho lógico de todos os volumes pertencentes ao pool|Nenhuma dimensão|
|VolumePoolTotalSnapshotSize|Tamanho do instantâneo total do pool de volumes|bytes|Average|Soma de todos os instantâneos no pool|Nenhuma dimensão|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BytesSentRate|Bytes enviados|Count|Total|Número de bytes enviados pela interface de rede|Nenhuma dimensão|
|BytesReceivedRate|Bytes recebidos|Count|Total|Número de bytes recebidos pela interface de rede|Nenhuma dimensão|
|PacketsSentRate|Pacotes enviados|Count|Total|Número de pacotes enviados pela interface de rede|Nenhuma dimensão|
|PacketsReceivedRate|Pacotes recebidos|Count|Total|Número de pacotes recebidos pela interface de rede|Nenhuma dimensão|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|VipAvailability|Disponibilidade do caminho de dados|Count|Average|Média de disponibilidade de caminho de dados de Load Balancer por duração de tempo|FrontendIPAddress, FrontendPort|
|DipAvailability|Status da investigação de integridade|Count|Average|Média Load Balancer status de investigação de integridade por duração de tempo|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Contagem de bytes|Count|Total|Número total de bytes transmitidos no período de tempo|FrontendIPAddress, FrontendPort, direção|
|PacketCount|Contagem de pacotes|Count|Total|Número total de pacotes transmitidos no período de tempo|FrontendIPAddress, FrontendPort, direção|
|SYNCount|Contagem de SYN|Count|Total|Número total de pacotes SYN transmitidos no período de tempo|FrontendIPAddress, FrontendPort, direção|
|SnatConnectionCount|Contagem de conexões SNAT|Count|Total|Número total de novas conexões SNAT criadas dentro do período de tempo|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Portas SNAT alocadas (visualização)|Count|Total|Número total de portas SNAT alocadas no período de tempo|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Portas SNAT usadas (visualização)|Count|Total|Número total de portas SNAT usadas no período de tempo|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueryVolume|Volume de consulta|Count|Total|Número de consultas servidas para uma zona DNS|Nenhuma dimensão|
|RecordSetCount|Contagem de conjuntos de registros|Count|Máximo|Número de conjuntos de registros em uma zona DNS|Nenhuma dimensão|
|RecordSetCapacityUtilization|Utilização da capacidade do conjunto de registros|Percent|Máximo|Porcentagem da capacidade do conjunto de registros utilizada por uma zona DNS|Nenhuma dimensão|

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
|IfUnderDDoSAttack|Sob ataque de DDoS ou não|Count|Máximo|Sob ataque de DDoS ou não|Nenhuma dimensão|
|DDoSTriggerTCPPackets|Pacotes TCP de entrada para disparar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes TCP de entrada para disparar a mitigação de DDoS|Nenhuma dimensão|
|DDoSTriggerUDPPackets|Pacotes UDP de entrada para disparar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes UDP de entrada para disparar a mitigação de DDoS|Nenhuma dimensão|
|DDoSTriggerSYNPackets|Pacotes de entrada SYN para disparar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes de entrada SYN para disparar a mitigação de DDoS|Nenhuma dimensão|
|VipAvailability|Disponibilidade do caminho de dados|Count|Average|Média de disponibilidade de endereço IP por duração de tempo|Port|
|ByteCount|Contagem de bytes|Count|Total|Número total de bytes transmitidos no período de tempo|Porta, direção|
|PacketCount|Contagem de pacotes|Count|Total|Número total de pacotes transmitidos no período de tempo|Porta, direção|
|SynCount|Contagem de SYN|Count|Total|Número total de pacotes SYN transmitidos no período de tempo|Porta, direção|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ApplicationRuleHit|Contagem de impacto de regras de aplicativo|Count|Total|Número de vezes que as regras de aplicativo foram atingidas|Status, motivo, protocolo|
|NetworkRuleHit|Contagem de impacto das regras de rede|Count|Total|Número de vezes que as regras de rede foram atingidas|Status, motivo, protocolo|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Débito|Débito|BytesPerSecond|Total|Número de bytes por segundo atendidos pelo gateway de aplicativo|Nenhuma dimensão|
|UnhealthyHostCount|Contagem de hosts não íntegros|Count|Average|Número de hosts de back-end não íntegros|BackendSettingsPool|
|HealthyHostCount|Contagem de hosts íntegros|Count|Average|Número de hosts de back-end íntegros|BackendSettingsPool|
|TotalRequests|Total de Pedidos|Count|Total|Contagem de solicitações bem-sucedidas que o gateway de aplicativo serviu|BackendSettingsPool|
|FailedRequests|Pedidos com Falhas|Count|Total|Contagem de solicitações com falha que o gateway de aplicativo serviu|BackendSettingsPool|
|ResponseStatus|Status da resposta|Count|Total|Status de resposta http retornado pelo gateway de aplicativo|HttpStatusGroup|
|CurrentConnections|Conexões atuais|Count|Total|Contagem de conexões atuais estabelecidas com o gateway de aplicativo|Nenhuma dimensão|
|CapacityUnits|Unidades de capacidade atuais|Count|Average|Unidades de capacidade consumidas|Nenhuma dimensão|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AverageBandwidth|Largura de banda S2S do gateway|BytesPerSecond|Average|Largura de banda de site a site média de um gateway em bytes por segundo|Nenhuma dimensão|
|P2SBandwidth|Largura de banda P2S do gateway|BytesPerSecond|Average|Largura de banda de ponto a site média de um gateway em bytes por segundo|Nenhuma dimensão|
|P2SConnectionCount|Contagem de conexões P2S|Count|Máximo|Contagem de conexões ponto a site de um gateway|Protocol|
|TunnelAverageBandwidth|Largura de banda do túnel|BytesPerSecond|Average|Largura de banda média de um túnel em bytes por segundo|ConnectionName, RemoteIP|
|TunnelEgressBytes|Bytes de saída do túnel|Bytes|Total|Bytes de saída de um túnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Bytes de entrada de túnel|Bytes|Total|Bytes de entrada de um túnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Pacotes de saída de túnel|Count|Total|Contagem de pacotes de saída de um túnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Pacotes de entrada de túnel|Count|Total|Contagem de pacotes de entrada de um túnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Descarte de pacotes incompatíveis TS de saída de túnel|Count|Total|Contagem de descarte de pacotes de saída da incompatibilidade de seletor de tráfego de um túnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Descarte de pacotes incompatíveis TS de entrada de túnel|Count|Total|Contagem de eliminação de pacotes de entrada da incompatibilidade de seletor de tráfego de um túnel|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits de entrada do Azure por segundo|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits de saída do Azure por segundo|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits de entrada do Azure por segundo|Nenhuma dimensão|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits de saída do Azure por segundo|Nenhuma dimensão|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits de entrada do Azure por segundo|Nenhuma dimensão|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits de saída do Azure por segundo|Nenhuma dimensão|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QpsByEndpoint|Consultas por ponto de extremidade retornadas|Count|Total|Número de vezes que um ponto de extremidade do Gerenciador de tráfego foi retornado no período de tempo fornecido|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Status do ponto de extremidade por ponto de extremidade|Count|Máximo|1 se o status de investigação de um ponto de extremidade for "habilitado", caso contrário, 0.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ProbesFailedPercent|% De investigações com falha|Percent|Average|% de investigações de monitoramento de conectividade com falha|Nenhuma dimensão|
|AverageRoundtripMs|Média Tempo de ida e volta (MS)|Milissegundos|Average|Tempo de ida e volta médio da rede (MS) para investigações de monitoramento de conectividade enviadas entre a origem e o destino|Nenhuma dimensão|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RequestCount|Número de Pedidos|Count|Total|O número de solicitações de cliente atendidas pelo proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|Solicitações|Tamanho da solicitação|Bytes|Total|O número de bytes enviados como solicitações de clientes para o proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|Respostas|Tamanho da resposta|Bytes|Total|O número de bytes enviados como respostas do proxy HTTP/S para clientes|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Contagem de solicitações de back-end|Count|Total|O número de solicitações enviadas do proxy HTTP/S para back-ends|HttpStatus, HttpStatusGroup, back-end|
|BackendRequestLatency|Latência de solicitação de back-end|Milissegundos|Average|O tempo calculado a partir de quando a solicitação foi enviada pelo proxy HTTP/S para o back-end até que o proxy HTTP/S receba o último byte de resposta do back-end|Back-end|
|TotalLatency|Latência total|Milissegundos|Average|O tempo calculado a partir de quando a solicitação do cliente foi recebida pelo proxy HTTP/S até que o cliente tenha reconhecido o último byte de resposta do proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Percentual de integridade de back-end|Percent|Average|A porcentagem de investigações de integridade bem-sucedidas do proxy HTTP/S para back-ends|Back-end, Httpsettings|
|WebApplicationFirewallRequestCount|Contagem de solicitações de firewall do aplicativo Web|Count|Total|O número de solicitações de cliente processadas pelo firewall do aplicativo Web|PolicyName, RuleName, ação|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|registration.all|Operações de Registo|Count|Total|Contagem de todas as operações de registo com êxito (consultas e eliminações de atualizações de criações). |Nenhuma dimensão|
|registration.create|Operações de Criação de Registo|Count|Total|Contagem de todas as criações de registo com êxito.|Nenhuma dimensão|
|registration.update|Operações de Atualização de Registo|Count|Total|Contagem de todas as atualizações de registo com êxito.|Nenhuma dimensão|
|registration.get|Operações de Leitura de Registo|Count|Total|Contagem de todas as consultas de registo com êxito.|Nenhuma dimensão|
|registration.delete|Operações de Eliminação de Registo|Count|Total|Contagem de todas as eliminações de registo com êxito.|Nenhuma dimensão|
|recebidos|Mensagens Recebidas|Count|Total|Contagem de todas as chamadas à API enviadas com êxito. |Nenhuma dimensão|
|incoming.scheduled|Notificações Push Agendadas Enviadas|Count|Total|Notificações por push agendadas canceladas|Nenhuma dimensão|
|incoming.scheduled.cancel|Notificações por push agendadas canceladas|Count|Total|Notificações por push agendadas canceladas|Nenhuma dimensão|
|agendado. pendente|Notificações Agendadas Pendentes|Count|Total|Notificações Agendadas Pendentes|Nenhuma dimensão|
|instalação. todos|Operações de Gestão de Instalação|Count|Total|Operações de Gestão de Instalação|Nenhuma dimensão|
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
|outgoing.wns.invalidcredentials|Erros de Autorização do WNS (Credenciais Inválidas)|Count|Total|Contagem de pushes falhados porque o PNS não aceitou as credenciais fornecidas ou porque as credenciais estão bloqueadas. (O Windows Live não reconhece as credenciais).|Nenhuma dimensão|
|outgoing.wns.badchannel|Erro de Canal Incorreto do WNS|Count|Total|A contagem de Pushes que falharam porque o ChannelURI no registro não foi reconhecido (status do WNS: 404 não encontrado).|Nenhuma dimensão|
|outgoing.wns.expiredchannel|Erro de Canal Expirado do WNS|Count|Total|A contagem de Pushes que falharam porque o ChannelURI está expirado (status do WNS: 410).|Nenhuma dimensão|
|outgoing.wns.throttled|Notificações Limitadas do WNS|Count|Total|A contagem de Pushes que falharam porque o WNS está limitando este aplicativo (status do WNS: 406 não aceitável).|Nenhuma dimensão|
|outgoing.wns.tokenproviderunreachable|Erros de Autorização do WNS (Inacessível)|Count|Total|O Windows Live não está acessível.|Nenhuma dimensão|
|outgoing.wns.invalidtoken|Erros de Autorização do WNS (Token Inválido)|Count|Total|O token fornecido ao WNS não é válido (status do WNS: 401 não autorizado).|Nenhuma dimensão|
|outgoing.wns.wrongtoken|Erros de Autorização do WNS (Token Incorreto)|Count|Total|O token fornecido ao WNS é válido, mas para outro aplicativo (status do WNS: 403 Proibido). Isso pode acontecer se o ChannelURI no registro estiver associado a outro aplicativo. Verifique se o aplicativo cliente está associado ao mesmo aplicativo cujas credenciais estão no Hub de notificação.|Nenhuma dimensão|
|outgoing.wns.invalidnotificationformat|Formato de Notificação Inválido do WNS|Count|Total|O formato da notificação é inválido (status do WNS: 400). Observe que o WNS não rejeita todas as cargas inválidas.|Nenhuma dimensão|
|outgoing.wns.invalidnotificationsize|Erro de Tamanho de Notificação Inválido do WNS|Count|Total|A carga de notificação é muito grande (status do WNS: 413).|Nenhuma dimensão|
|outgoing.wns.channelthrottled|Canal do WNS Limitado|Count|Total|A notificação foi descartada porque o ChannelURI no registro está limitado (cabeçalho de resposta do WNS: X-WNS-NotificationStatus: channelThrottled).|Nenhuma dimensão|
|outgoing.wns.channeldisconnected|Canal do WNS Desligado|Count|Total|A notificação foi descartada porque o ChannelURI no registro está limitado (cabeçalho de resposta do WNS: X-WNS-DeviceConnectionStatus: desconectado).|Nenhuma dimensão|
|outgoing.wns.dropped|Notificações Removidas do WNS|Count|Total|A notificação foi removida porque o ChannelURI do registo está limitado (X-WNS-NotificationStatus: removido mas não X-WNS-DeviceConnectionStatus: desligado).|Nenhuma dimensão|
|outgoing.wns.pnserror|Erros do WNS|Count|Total|As notificações não foram entregues devido a erros de comunicação com o WNS.|Nenhuma dimensão|
|outgoing.wns.authenticationerror|Erros de Autenticação do WNS|Count|Total|Notificação não entregue devido a erros de comunicação com o Windows Live, com credenciais inválidas ou token errado.|Nenhuma dimensão|
|outgoing.apns.success|Notificações com Êxito do APNS|Count|Total|Contagem de todas as notificações com êxito.|Nenhuma dimensão|
|outgoing.apns.invalidcredentials|Erros de Autorização do APNS|Count|Total|Contagem de pushes falhados porque o PNS não aceitou as credenciais fornecidas ou porque as credenciais estão bloqueadas.|Nenhuma dimensão|
|outgoing.apns.badchannel|Erro de Canal Incorreto do APNS|Count|Total|A contagem de Pushes que falharam porque o token é inválido (código de status do protocolo binário APNS: 8. Código de status do protocolo HTTP APNS: 400 com "BadDeviceToken").|Nenhuma dimensão|
|outgoing.apns.expiredchannel|Erro de Canal Expirado do APNS|Count|Total|Contagem de tokens invalidados pelo canal de relatórios do APNS.|Nenhuma dimensão|
|outgoing.apns.invalidnotificationsize|Erro de Tamanho de Notificação Inválido do APNS|Count|Total|A contagem de Pushes que falharam porque o conteúdo era muito grande (código de status do protocolo binário APNS: 7).|Nenhuma dimensão|
|outgoing.apns.pnserror|Erros do APNS|Count|Total|Contagem de pushes falhados devido a erros de comunicação com o APNS.|Nenhuma dimensão|
|outgoing.gcm.success|Notificações com Êxito do GCM|Count|Total|Contagem de todas as notificações com êxito.|Nenhuma dimensão|
|outgoing.gcm.invalidcredentials|Erros de Autorização do GCM (Credenciais Inválidas)|Count|Total|Contagem de pushes falhados porque o PNS não aceitou as credenciais fornecidas ou porque as credenciais estão bloqueadas.|Nenhuma dimensão|
|outgoing.gcm.badchannel|Erro de Canal Incorreto do GCM|Count|Total|A contagem de Pushes que falharam porque o RegistrationId no registro não foi reconhecido (resultado do GCM: Registro inválido).|Nenhuma dimensão|
|outgoing.gcm.expiredchannel|Erro de Canal Expirado do GCM|Count|Total|A contagem de Pushes que falharam porque o RegistrationId no registro expirou (resultado do GCM: Não registrado).|Nenhuma dimensão|
|outgoing.gcm.throttled|Notificações Limitadas do GCM|Count|Total|A contagem de Pushes que falharam porque o GCM restringiu este aplicativo (código de status do GCM: 501-599 ou resultado: não disponível).|Nenhuma dimensão|
|outgoing.gcm.invalidnotificationformat|Formato de Notificação Inválido do GCM|Count|Total|A contagem de Pushes que falharam porque a carga não foi formatada corretamente (resultado do GCM: InvalidDataKey ou InvalidTtl).|Nenhuma dimensão|
|outgoing.gcm.invalidnotificationsize|Erro de Tamanho de Notificação Inválido do GCM|Count|Total|A contagem de Pushes que falharam porque o conteúdo era muito grande (resultado do GCM: MessageTooBig).|Nenhuma dimensão|
|outgoing.gcm.wrongchannel|Erro de Canal Errado do GCM|Count|Total|A contagem de Pushes que falharam porque o RegistrationId no registro não está associado ao aplicativo atual (resultado do GCM: InvalidPackageName).|Nenhuma dimensão|
|outgoing.gcm.pnserror|Erros do GCM|Count|Total|Contagem de pushes falhados devido a erros de comunicação com o GCM.|Nenhuma dimensão|
|outgoing.gcm.authenticationerror|Erros de Autenticação do GCM|Count|Total|A contagem de Pushes que falharam porque o PNS não aceitou as credenciais fornecidas as credenciais estão bloqueadas ou a SenderId não está configurada corretamente no aplicativo (resultado do GCM: MismatchedSenderId).|Nenhuma dimensão|
|outgoing.mpns.success|Notificações com Êxito do MPNS|Count|Total|Contagem de todas as notificações com êxito.|Nenhuma dimensão|
|outgoing.mpns.invalidcredentials|Credenciais Inválidas do MPNS|Count|Total|Contagem de pushes falhados porque o PNS não aceitou as credenciais fornecidas ou porque as credenciais estão bloqueadas.|Nenhuma dimensão|
|outgoing.mpns.badchannel|Erro de Canal Incorreto do MPNS|Count|Total|A contagem de Pushes que falharam porque o ChannelURI no registro não foi reconhecido (status do MPNS: 404 não encontrado).|Nenhuma dimensão|
|outgoing.mpns.throttled|Notificações Limitadas do MPNS|Count|Total|A contagem de Pushes que falharam porque o MPNS está limitando este aplicativo (WNS MPNS: 406 não aceitável).|Nenhuma dimensão|
|outgoing.mpns.invalidnotificationformat|Formato de Notificação Inválido do MPNS|Count|Total|Contagem de pushes falhados porque o payload da notificação era demasiado grande.|Nenhuma dimensão|
|outgoing.mpns.channeldisconnected|Canal do MPNS Desligado|Count|Total|A contagem de Pushes que falharam porque o ChannelURI no registro foi desconectado (status do MPNS: 412 não encontrado).|Nenhuma dimensão|
|outgoing.mpns.dropped|Notificações Removidas do MPNS|Count|Total|A contagem de Pushes que foram descartados pelo MPNS (cabeçalho de resposta MPNS: X-NotificationStatus: QueueFull ou suprimido).|Nenhuma dimensão|
|outgoing.mpns.pnserror|Erros do MPNS|Count|Total|Contagem de pushes falhados devido a erros de comunicação com o MPNS.|Nenhuma dimensão|
|outgoing.mpns.authenticationerror|Erros de Autenticação do MPNS|Count|Total|Contagem de pushes falhados porque o PNS não aceitou as credenciais fornecidas ou porque as credenciais estão bloqueadas.|Nenhuma dimensão|
|notificationhub.pushes|Todas as Notificações Enviadas|Count|Total|Todas as notificações enviadas do hub de notificações|Nenhuma dimensão|
|incoming.all.requests|Todos os Pedidos Recebidos|Count|Total|Total de pedidos recebidos de um hub de notificações|Nenhuma dimensão|
|incoming.all.failedrequests|Todos os Pedidos Falhados Recebidos|Count|Total|Total de pedidos falhados recebidos de um hub de notificações|Nenhuma dimensão|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Average_% de inodes livres|% De inodes livres|Count|Average|Average_% de inodes livres|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de espaço livre|% De espaço livre|Count|Average|Average_% de espaço livre|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Inodes de Average_% usados|% De inodes usados|Count|Average|Inodes de Average_% usados|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de espaço usado|% De espaço utilizado|Count|Average|Average_% de espaço usado|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Bytes de leitura de Average_Disk/s|Bytes Lidos de Disco/seg|Count|Average|Bytes de leitura de Average_Disk/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Leituras de Average_Disk/s|Leituras de disco/seg|Count|Average|Leituras de Average_Disk/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Disk transferências de transferência/s|As transferências de disco/seg|Count|Average|Average_Disk transferências de transferência/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Bytes de gravação de Average_Disk/s|Bytes Escritos em Disco/seg|Count|Average|Bytes de gravação de Average_Disk/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Gravações de Average_Disk/s|Escritas de disco/seg|Count|Average|Gravações de Average_Disk/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Free megabytes|Megabytes livres|Count|Average|Average_Free megabytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Logical bytes de disco/s|Bytes de disco lógico/seg|Count|Average|Average_Logical bytes de disco/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de memória disponível|% De memória disponível|Count|Average|Average_% de memória disponível|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de espaço de permuta disponível|% De espaço de permuta disponível|Count|Average|Average_% de espaço de permuta disponível|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% used Memory|% De memória usada|Count|Average|Average_% used Memory|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de espaço de permuta utilizado|% De espaço de permuta utilizado|Count|Average|Average_% de espaço de permuta utilizado|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Memória de Mbytes de Average_Available|MBytes de memória disponíveis|Count|Average|Memória de Mbytes de Average_Available|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Permuta de Mbytes de Average_Available|Troca de MBytes disponíveis|Count|Average|Permuta de Mbytes de Average_Available|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Leituras de Average_Page/s|Leituras de página/s|Count|Average|Leituras de Average_Page/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Gravações de Average_Page/s|Gravações de página/s|Count|Average|Gravações de Average_Page/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Pages/s|Páginas/s|Count|Average|Average_Pages/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Espaço de permuta de Mbytes Average_Used|Espaço de permuta usado em Mbytes|Count|Average|Espaço de permuta de Mbytes Average_Used|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Mbytes de memória Average_Used|Mbytes de memória usados|Count|Average|Mbytes de memória Average_Used|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total bytes transmitidos|Total de Bytes transmitidos|Count|Average|Average_Total bytes transmitidos|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total bytes recebidos|Total de Bytes recebidos|Count|Average|Average_Total bytes recebidos|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total bytes|Total de Bytes|Count|Average|Average_Total bytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Pacotes Average_Total transmitidos|Total de pacotes transmitidos|Count|Average|Pacotes Average_Total transmitidos|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Pacotes Average_Total recebidos|Total de pacotes recebidos|Count|Average|Pacotes Average_Total recebidos|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Erros de RX Average_Total|Total de erros de RX|Count|Average|Erros de RX Average_Total|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Erros de TX Average_Total|Total de erros TX|Count|Average|Erros de TX Average_Total|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Colisões de Average_Total|Total de colisões|Count|Average|Colisões de Average_Total|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Disco seg/leitura|Média Disco seg/leitura|Count|Average|Average_Avg. Disco seg/leitura|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Disco seg/transferência|Média Disco seg/transferência|Count|Average|Average_Avg. Disco seg/transferência|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Disco seg/escritas|Média Disco seg/escritas|Count|Average|Average_Avg. Disco seg/escritas|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Physical bytes de disco/s|Bytes de disco físico/s|Count|Average|Average_Physical bytes de disco/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Pct tempo privilegiado|Percentual de tempo privilegiado|Count|Average|Average_Pct tempo privilegiado|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Pct tempo de usuário|Tempo de usuário do PCT|Count|Average|Average_Pct tempo de usuário|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|KBytes de memória Average_Used|KBytes de memória usada|Count|Average|KBytes de memória Average_Used|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Memória compartilhada Average_Virtual|Memória compartilhada virtual|Count|Average|Memória compartilhada Average_Virtual|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% tempo de DPC|% De tempo de DPC|Count|Average|Average_% tempo de DPC|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de tempo ocioso|% De tempo ocioso|Count|Average|Average_% de tempo ocioso|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% tempo de interrupção|% De tempo de interrupção|Count|Average|Average_% tempo de interrupção|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% IO tempo de espera|% De tempo de espera de e/s|Count|Average|Average_% IO tempo de espera|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de tempo bom|% De tempo adequado|Count|Average|Average_% de tempo bom|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de tempo privilegiado|% De tempo privilegiado|Count|Average|Average_% de tempo privilegiado|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% tempo do processador|% Tempo do processador|Count|Average|Average_% tempo do processador|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de tempo de usuário|% De tempo do usuário|Count|Average|Average_% de tempo de usuário|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Memória física Average_Free|Memória física livre|Count|Average|Memória física Average_Free|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Free espaço em arquivos de paginação|Espaço livre em arquivos de paginação|Count|Average|Average_Free espaço em arquivos de paginação|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Memória virtual Average_Free|Memória virtual livre|Count|Average|Memória virtual Average_Free|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Processes|Processos|Count|Average|Average_Processes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Size armazenados em arquivos de paginação|Tamanho armazenado em arquivos de paginação|Count|Average|Average_Size armazenados em arquivos de paginação|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Uptime|Tempo de atividade|Count|Average|Average_Uptime|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Users|Utilizadores|Count|Average|Average_Users|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Disco seg/leitura|Média Disco seg/leitura|Count|Average|Average_Avg. Disco seg/leitura|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Disco seg/escritas|Média Disco seg/escritas|Count|Average|Average_Avg. Disco seg/escritas|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Comprimento da fila de disco Average_Current|Comprimento da fila de disco atual|Count|Average|Comprimento da fila de disco Average_Current|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Leituras de Average_Disk/s|Leituras de disco/seg|Count|Average|Leituras de Average_Disk/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Disk transferências de transferência/s|As transferências de disco/seg|Count|Average|Average_Disk transferências de transferência/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Gravações de Average_Disk/s|Escritas de disco/seg|Count|Average|Gravações de Average_Disk/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Free megabytes|Megabytes livres|Count|Average|Average_Free megabytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de espaço livre|% De espaço livre|Count|Average|Average_% de espaço livre|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Available Mbytes|MBytes disponíveis|Count|Average|Average_Available Mbytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% bytes confirmados em uso|% Bytes confirmados em uso|Count|Average|Average_% bytes confirmados em uso|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Bytes recebidos/s|Bytes recebidos/seg|Count|Average|Average_Bytes recebidos/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Bytes enviados/s|Bytes enviados/seg|Count|Average|Average_Bytes enviados/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Total de Average_Bytes/s|Total de bytes/s|Count|Average|Total de Average_Bytes/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% tempo do processador|% Tempo do processador|Count|Average|Average_% tempo do processador|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Comprimento da fila de Average_Processor|Comprimento da fila do processador|Count|Average|Comprimento da fila de Average_Processor|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Heartbeat|Heartbeat|Count|Total|Heartbeat|Computer, OSType, Version, SourceComputerId|
|Atualizar|Atualizar|Count|Average|Atualizar|Computador, produto, classificação, UpdateState, opcional, aprovado|
|Evento|Evento|Count|Average|Evento|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueryDuration|Duração da consulta|Milissegundos|Average|Duração da consulta DAX no último intervalo|Nenhuma dimensão|
|QueryPoolJobQueueLength|Threads Comprimento da fila de trabalhos do pool de consultas|Count|Average|Número de trabalhos na fila do pool de threads de consulta.|Nenhuma dimensão|
|qpu_high_utilization_metric|Alta utilização de QPU|Count|Total|QPU alta utilização no último minuto, 1 para alta utilização de QPU, caso contrário 0|Nenhuma dimensão|
|memory_metric|Memória|Bytes|Average|Memória. Intervalo de 0-3 GB para a1, 0-5 GB para a2, 0-10 GB para a3, 0-25 GB para A4, 0-50 GB para a5 e 0-100 GB para a6|Nenhuma dimensão|
|memory_thrashing_metric|Ultrapaginação de memória|Percent|Average|Média de ultrapaginação de memória.|Nenhuma dimensão|

## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/namespaces

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
|SearchLatency|Latência de pesquisa|Segundos|Average|Latência de pesquisa média para o serviço de pesquisa|Nenhuma dimensão|
|SearchQueriesPerSecond|Pesquisar consultas por segundo|CountPerSecond|Average|Pesquisar consultas por segundo para o serviço de pesquisa|Nenhuma dimensão|
|ThrottledSearchQueriesPercentage|Percentagem de pesquisas de consultas otimizadas|Percent|Average|Porcentagem de consultas de pesquisa que foram limitadas para o serviço de pesquisa|Nenhuma dimensão|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfulRequests|Solicitações com êxito (versão prévia)|Count|Total|Total de solicitações bem-sucedidas para um namespace (versão prévia)|EntityName|
|ServerErrors|Erros de Servidor. (Pré-visualização)|Count|Total|Erros de Servidor do Microsoft.ServiceBus. (Pré-visualização)|EntityName|
|UserErrors|Erros do Utilizador. (Pré-visualização)|Count|Total|Erros de Utilizador do Microsoft.ServiceBus. (Pré-visualização)|EntityName|
|ThrottledRequests|Pedidos Limitados. (Pré-visualização)|Count|Total|Pedidos Limitados do Microsoft.ServiceBus. (Pré-visualização)|EntityName|
|IncomingRequests|Solicitações de entrada (versão prévia)|Count|Total|Pedidos Recebidos do Microsoft.ServiceBus. (Pré-visualização)|EntityName|
|IncomingMessages|Mensagens de entrada (versão prévia)|Count|Total|Mensagens Recebidas do Microsoft.ServiceBus. (Pré-visualização)|EntityName|
|OutgoingMessages|Mensagens de saída (versão prévia)|Count|Total|Mensagens Enviadas do Microsoft.ServiceBus. (Pré-visualização)|EntityName|
|ActiveConnections|ActiveConnections (visualização)|Count|Total|Total de Ligações Ativas do Microsoft.ServiceBus. (Pré-visualização)|Nenhuma dimensão|
|Size|Tamanho (versão prévia)|Bytes|Average|Tamanho de um(a) Fila/Tópico em Bytes. (Pré-visualização)|EntityName|
|Mensagens|Contagem de mensagens num(a) Fila/Tópico. (Pré-visualização)|Count|Average|Contagem de mensagens num(a) Fila/Tópico. (Pré-visualização)|EntityName|
|ActiveMessages|Contagem de mensagens ativas num(a) Fila/Tópico. (Pré-visualização)|Count|Average|Contagem de mensagens ativas num(a) Fila/Tópico. (Pré-visualização)|EntityName|
|DeadletteredMessages|Contagem de mensagens não entregues num Fila/Tópico. (Pré-visualização)|Count|Average|Contagem de mensagens não entregues num Fila/Tópico. (Pré-visualização)|EntityName|
|ScheduledMessages|Contagem de mensagens agendadas numa Fila/Tópico. (Pré-visualização)|Count|Average|Contagem de mensagens agendadas numa Fila/Tópico. (Pré-visualização)|EntityName|
|CPUXNS|Utilização da CPU por espaço de nomes|Percent|Máximo|Métrica de utilização do espaço de nomes da CPU do service bus premium|Nenhuma dimensão|
|WSXNS|Utilização do tamanho da memória por espaço de nomes|Percent|Máximo|Métrica de utilização de memória do espaço de nomes do service bus premium|Nenhuma dimensão|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Count|Average|CPU alocada para este contêiner em milicores|ApplicationName, ServiceName, CodePackageName, ServiceId|
|AllocatedMemory|AllocatedMemory|Bytes|Average|Memória alocada para este contêiner em MB|ApplicationName, ServiceName, CodePackageName, ServiceId|
|ActualCpu|ActualCpu|Count|Average|Uso real da CPU em milicores|ApplicationName, ServiceName, CodePackageName, ServiceId|
|ActualMemory|ActualMemory|Bytes|Average|Uso real da memória em MB|ApplicationName, ServiceName, CodePackageName, ServiceId|
|CpuUtilization|CpuUtilization|Percent|Average|Utilização da CPU para este contêiner como porcentagem de AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceId|
|MemoryUtilization|MemoryUtilization|Percent|Average|Utilização da CPU para este contêiner como porcentagem de AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceId|
|ApplicationStatus|ApplicationStatus|Count|Average|Status do aplicativo de malha Service Fabric|ApplicationName, status|
|ServiceStatus|ServiceStatus|Count|Average|Status de integridade de um serviço no aplicativo de malha Service Fabric|ApplicationName, status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Count|Average|Status de integridade de uma réplica de serviço no aplicativo de malha Service Fabric|ApplicationName, status, ServiceName, nome de réplica|
|ContainerStatus|ContainerStatus|Count|Average|Status do contêiner no aplicativo de malha Service Fabric|ApplicationName, ServiceName, CodePackageName, ServiceId, status|
|RestartCount|RestartCount|Count|Average|Reiniciar a contagem de um contêiner no aplicativo de malha Service Fabric|ApplicationName, status, ServiceName, nome de réplica, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ConnectionCount|Contagem de conexões|Count|Máximo|A quantidade de conexão do usuário.|Ponto Final|
|MessageCount|Contagem de mensagens|Count|Total|A quantidade total de mensagens.|Nenhuma dimensão|
|InboundTraffic|Tráfego de Entrada|Bytes|Total|O tráfego de entrada do serviço|Nenhuma dimensão|
|OutboundTraffic|Tráfego de Saída|Bytes|Total|O tráfego de saída do serviço|Nenhuma dimensão|
|UserErrors|Erros do usuário|Percent|Máximo|A porcentagem de erros do usuário|Nenhuma dimensão|
|SystemErrors|Erros do sistema|Percent|Máximo|A porcentagem de erros do sistema|Nenhuma dimensão|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentagem de CPU|Percent|Average|Percentagem de CPU|Nenhuma dimensão|
|physical_data_read_percent|Percentagem de E/S de Dados|Percent|Average|Percentagem de E/S de Dados|Nenhuma dimensão|
|log_write_percent|Percentagem de E/S de Registo|Percent|Average|Percentagem de E/S de Registo|Nenhuma dimensão|
|dtu_consumption_percent|Percentagem de DTU|Percent|Average|Percentagem de DTU|Nenhuma dimensão|
|armazenamento|Espaço de dados utilizado|Bytes|Máximo|Tamanho total do banco de dados|Nenhuma dimensão|
|connection_successful|Conexões com êxito|Count|Total|Conexões com êxito|Nenhuma dimensão|
|connection_failed|Ligações com Falhas|Count|Total|Ligações com Falhas|Nenhuma dimensão|
|blocked_by_firewall|Bloqueado pelo firewall|Count|Total|Bloqueado pelo firewall|Nenhuma dimensão|
|bloqueado|Impasses|Count|Total|Impasses|Nenhuma dimensão|
|storage_percent|Porcentagem de espaço de dados usada|Percent|Máximo|Percentagem de tamanho da Base de Dados|Nenhuma dimensão|
|xtp_storage_percent|Porcentagem de armazenamento OLTP na memória|Percent|Average|Porcentagem de armazenamento OLTP na memória|Nenhuma dimensão|
|workers_percent|Porcentagem de trabalhadores|Percent|Average|Porcentagem de trabalhadores|Nenhuma dimensão|
|sessions_percent|Porcentagem de sessões|Percent|Average|Porcentagem de sessões|Nenhuma dimensão|
|dtu_limit|Limite de DTU|Count|Average|Limite de DTU|Nenhuma dimensão|
|dtu_used|DTU usado|Count|Average|DTU usado|Nenhuma dimensão|
|cpu_limit|Limite de CPU|Count|Average|Limite de CPU|Nenhuma dimensão|
|cpu_used|CPU usada|Count|Average|CPU usada|Nenhuma dimensão|
|dwu_limit|Limite de DWU|Count|Máximo|Limite de DWU|Nenhuma dimensão|
|dwu_consumption_percent|Porcentagem de DWU|Percent|Máximo|Porcentagem de DWU|Nenhuma dimensão|
|dwu_used|DWU usado|Count|Máximo|DWU usado|Nenhuma dimensão|
|dw_cpu_percent|Percentual de CPU no nível de nó DW|Percent|Average|Percentual de CPU no nível de nó DW|DwLogicalNodeId|
|dw_physical_data_read_percent|Porcentagem de e/s de dados de nível de nó DW|Percent|Average|Porcentagem de e/s de dados de nível de nó DW|DwLogicalNodeId|
    |cache_hit_percent|Porcentagem de acesso ao cache|Percent|Máximo|Porcentagem de acesso ao cache|Nenhuma dimensão|
|cache_used_percent|Percentual de cache usado|Percent|Máximo|Percentual de cache usado|Nenhuma dimensão|
|local_tempdb_usage_percent|Porcentagem de tempdb local|Percent|Average|Porcentagem de tempdb local|Nenhuma dimensão|
|app_cpu_billed|CPU do aplicativo cobrada|Count|Total|CPU do aplicativo cobrada|Nenhuma dimensão|
|app_cpu_percent|Porcentagem de CPU do aplicativo|Percent|Average|Porcentagem de CPU do aplicativo|Nenhuma dimensão|
|app_memory_percent|Porcentagem de memória usada do aplicativo|Percent|Average|Porcentagem de memória usada do aplicativo|Nenhuma dimensão|
|allocated_data_storage|Espaço de dados atribuído|Bytes|Average|Espaço de dados atribuído|Nenhuma dimensão|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentagem de CPU|Percent|Average|Percentagem de CPU|Nenhuma dimensão|
|physical_data_read_percent|Percentagem de E/S de Dados|Percent|Average|Percentagem de E/S de Dados|Nenhuma dimensão|
|log_write_percent|Percentagem de E/S de Registo|Percent|Average|Percentagem de E/S de Registo|Nenhuma dimensão|
|dtu_consumption_percent|Percentagem de DTU|Percent|Average|Percentagem de DTU|Nenhuma dimensão|
|storage_percent|Porcentagem de espaço de dados usada||Percent|Average|Porcentagem de armazenamento|Nenhuma dimensão|
|workers_percent|Porcentagem de trabalhadores|Percent|Average|Porcentagem de trabalhadores|Nenhuma dimensão|
|sessions_percent|Porcentagem de sessões|Percent|Average|Porcentagem de sessões|Nenhuma dimensão|
|eDTU_limit|limite de eDTU|Count|Average|limite de eDTU|Nenhuma dimensão|
|storage_limit|Tamanho máximo de dados|Bytes|Average|Limite de armazenamento|Nenhuma dimensão|
|eDTU_used|eDTU usado|Count|Average|eDTU usado|Nenhuma dimensão|
|storage_used|Espaço de dados utilizado|Bytes|Average|Armazenamento utilizado|Nenhuma dimensão|
|xtp_storage_percent|Porcentagem de armazenamento OLTP na memória|Percent|Average|Porcentagem de armazenamento OLTP na memória|Nenhuma dimensão|
|cpu_limit|Limite de CPU|Count|Average|Limite de CPU|Nenhuma dimensão|
|cpu_used|CPU usada|Count|Average|CPU usada|Nenhuma dimensão|
|allocated_data_storage|Espaço de dados atribuído|Bytes|Average|Espaço de dados atribuído|Nenhuma dimensão|
|allocated_data_storage_percent|Porcentagem alocada de espaço de dados|Percent|Máximo|Porcentagem alocada de espaço de dados|Nenhuma dimensão|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|virtual_core_count|Contagem de núcleos virtuais|Count|Average|Contagem de núcleos virtuais|Nenhuma dimensão|
|avg_cpu_percent|Percentual médio de CPU|Percent|Average|Percentual médio de CPU|Nenhuma dimensão|
|reserved_storage_mb|Espaço de armazenamento reservado|Count|Average|Espaço de armazenamento reservado|Nenhuma dimensão|
|storage_space_used_mb|Espaço de armazenamento usado|Count|Average|Espaço de armazenamento usado|Nenhuma dimensão|
|io_requests|Contagem de solicitações de e/s|Count|Average|Contagem de solicitações de e/s|Nenhuma dimensão|
|io_bytes_read|Bytes de e/s lidos|Bytes|Average|Bytes de e/s lidos|Nenhuma dimensão|
|io_bytes_written|Bytes de e/s gravados|Bytes|Average|Bytes de e/s gravados|Nenhuma dimensão|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|UsedCapacity|Capacidade utilizada|Bytes|Média|Capacidade da conta utilizada|Nenhuma dimensão|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com êxito ou falhados, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada no AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada Considerando o valor de TotalBillableRequests e dividindo-o pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam em disponibilidade reduzida para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BlobCapacity|Capacidade do Blob|Bytes|Average|A quantidade de armazenamento utilizada pelo serviço Blob a conta de armazenamento, em bytes.|BlobType, camada|
|BlobCount|Contagem de Blobs|Contagem|Total|O número de blobs no serviço Blob da conta de armazenamento.|BlobType|       |BlobCount|Contagem de Blobs|Count|Average|O número de blobs no serviço Blob da conta de armazenamento.|BlobType, camada|
|ContainerCount|Contagem do Contentor de Blobs|Contagem|Média|O número de contentores no serviço Blob da conta de armazenamento.|Nenhuma dimensão|
|IndexCapacity|Capacidade de Índice|Bytes|Average|A quantidade de armazenamento utilizada pelo Índice do ADLS Gen2 (Hierárquico) em bytes.|Nenhuma dimensão|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com êxito ou falhados, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada no AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada Considerando o valor de TotalBillableRequests e dividindo-o pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam em disponibilidade reduzida para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Capacidade de|Capacidade de Ficheiros|Bytes|Average|A quantidade de armazenamento utilizada pelo serviço Ficheiro a conta de armazenamento, em bytes.|Nenhuma dimensão|
|FileCount|Contagem de ficheiros|Count|Average|O número de ficheiros no serviço Ficheiro da conta de armazenamento.|Nenhuma dimensão|
|FileShareCount|Contagem de Partilha de Ficheiros|Count|Average|O número de partilhas de ficheiros no serviço Ficheiro da conta de armazenamento.|Nenhuma dimensão|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com êxito ou falhados, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada no AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada Considerando o valor de TotalBillableRequests e dividindo-o pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam em disponibilidade reduzida para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueueCapacity|Capacidade de Fila|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Fila a conta de armazenamento, em bytes.|Nenhuma dimensão|
|QueueCount|Contagem de Filas|Contagem|Média|O número de filas no serviço Fila da conta de armazenamento.|Nenhuma dimensão|
|QueueMessageCount|Contagem de Mensagens em Fila|Contagem|Média|O número aproximado de mensagens em fila no serviço Fila da conta de armazenamento.|Nenhuma dimensão|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com êxito ou falhados, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada no AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada Considerando o valor de TotalBillableRequests e dividindo-o pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam em disponibilidade reduzida para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TableCapacity|Capacidade de Tabelas|Bytes|Média|A quantidade de armazenamento utilizado pelo serviço Tabela da conta de armazenamento, em bytes.|Nenhuma dimensão|
|TableCount|Contagem de Tabelas|Contagem|Média|O número de tabelas no serviço Tabela da conta de armazenamento.|Nenhuma dimensão|
|TableEntityCount|Contagem de Entidade de Tabelas|Contagem|Média|O número de entidades de tabelas no serviço Tabela da conta de armazenamento.|Nenhuma dimensão|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com êxito ou falhados, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada no AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada Considerando o valor de TotalBillableRequests e dividindo-o pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam em disponibilidade reduzida para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Resultado da sessão de sincronização|Count|Average|Métrica que registra um valor de 1 cada vez que o ponto de extremidade do servidor conclui com êxito uma sessão de sincronização com o ponto de extremidade de nuvem|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Ficheiros Sincronizados|Count|Total|Contagem de arquivos sincronizados|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Os ficheiros não estão a sincronizar|Count|Total|Falha na sincronização da contagem de arquivos|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Bytes sincronizados|Bytes|Total|Tamanho total do arquivo transferido para sessões de sincronização|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Estado Online do Servidor|Count|Máximo|Métrica que registra um valor de 1 cada vez que o servidor registrado registra com êxito uma pulsação com o ponto de extremidade de nuvem|NomeServidor|
|StorageSyncRecallIOTotalSizeBytes|Recall de camadas de nuvem|Bytes|Total|Tamanho total dos dados recuperados pelo servidor|NomeServidor|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ResourceUtilization|% de utilização SU|Percent|Máximo|% de utilização SU|LogicalName, PartitionID|
|InputEvents|Eventos de Entrada|Count|Total|Eventos de Entrada|LogicalName, PartitionID|
|InputEventBytes|Bytes de Evento de Entrada|Bytes|Total|Bytes de Evento de Entrada|LogicalName, PartitionID|
|LateInputEvents|Eventos de Entrada atrasados|Count|Total|Eventos de Entrada atrasados|LogicalName, PartitionID|
|OutputEvents|Eventos de Saída|Count|Total|Eventos de Saída|LogicalName, PartitionID|
|ConversionErrors|Erros de Conversão de Dados|Count|Total|Erros de Conversão de Dados|LogicalName, PartitionID|
|Erros|Erros de Tempo de Execução|Count|Total|Erros de Tempo de Execução|LogicalName, PartitionID|
|DroppedOrAdjustedEvents|Eventos desordenados|Count|Total|Eventos desordenados|LogicalName, PartitionID|
|AMLCalloutRequests|Pedidos de Função|Count|Total|Pedidos de Função|LogicalName, PartitionID|
|AMLCalloutFailedRequests|Falha no pedido de funções|Count|Total|Falha no pedido de funções|LogicalName, PartitionID|
|AMLCalloutInputEvents|Eventos de Função|Count|Total|Eventos de Função|LogicalName, PartitionID|
|DeserializationError|Erros de Desserialização de entrada|Count|Total|Erros de Desserialização de entrada|LogicalName, PartitionID|
|EarlyInputEvents|Eventos de Entrada Antigos|Count|Total|Eventos de Entrada Antigos|LogicalName, PartitionID|
|OutputWatermarkDelaySeconds|Atraso de Marca de Água|Segundos|Máximo|Atraso de Marca de Água|LogicalName, PartitionID|
|InputEventsSourcesBacklogged|Eventos de Entrada Pendentes|Count|Máximo|Eventos de Entrada Pendentes|LogicalName, PartitionID|
|InputEventsSourcesPerSecond|Origens de Entrada Recebidas|Count|Total|Origens de Entrada Recebidas|LogicalName, PartitionID|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|IngressReceivedMessages|Mensagens de entrada recebidas|Count|Total|Contagem de mensagens lidas de todas as fontes de eventos do hub de eventos ou do Hub IoT|Nenhuma dimensão|
|IngressReceivedInvalidMessages|A entrada recebeu mensagens inválidas|Count|Total|Contagem de mensagens inválidas lidas de todas as fontes de eventos do hub de eventos ou do Hub IoT|Nenhuma dimensão|
|IngressReceivedBytes|Bytes de entrada recebidos|Bytes|Total|Contagem de bytes lidos de todas as origens do evento|Nenhuma dimensão|
|IngressStoredBytes|Bytes de entrada armazenados|Bytes|Total|Tamanho total de eventos processados e disponíveis com êxito para consulta|Nenhuma dimensão|
|IngressStoredEvents|Eventos de entrada armazenados|Count|Total|Contagem de eventos mesclados processados e disponíveis com êxito para consulta|Nenhuma dimensão|
|IngressReceivedMessagesTimeLag|Atraso de tempo de mensagens recebidas de entrada|Segundos|Máximo|Diferença entre a hora em que a mensagem é enfileirada na origem do evento e a hora em que ela é processada na entrada|Nenhuma dimensão|
|IngressReceivedMessagesCountLag|Atraso na contagem de mensagens recebidas de entrada|Count|Average|Diferença entre o número de sequência da última mensagem enfileirada na partição de origem do evento e o número de sequência da mensagem sendo processada na entrada|Nenhuma dimensão|
|WarmStorageMaxProperties|Propriedades máximas de armazenamento quente|Count|Máximo|Número máximo de propriedades usadas permitidas pelo ambiente para SKU S1/S2 e número máximo de propriedades permitidas pelo armazenamento quente para o SKU PAYG|Nenhuma dimensão|
|WarmStorageUsedProperties|Propriedades usadas de armazenamento quente |Count|Máximo|Número de propriedades usadas pelo ambiente para SKU S1/S2 e número de propriedades usadas pelo armazenamento quente para SKU PAYG|Nenhuma dimensão|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|IngressReceivedMessages|Mensagens de entrada recebidas|Count|Total|Contagem de mensagens lidas da origem do evento|Nenhuma dimensão|
|IngressReceivedInvalidMessages|A entrada recebeu mensagens inválidas|Count|Total|Contagem de mensagens inválidas lidas da origem do evento|Nenhuma dimensão|
|IngressReceivedBytes|Bytes de entrada recebidos|Bytes|Total|Contagem de bytes lidos da origem do evento|Nenhuma dimensão|
|IngressStoredBytes|Bytes de entrada armazenados|Bytes|Total|Tamanho total de eventos processados e disponíveis com êxito para consulta|Nenhuma dimensão|
|IngressStoredEvents|Eventos de entrada armazenados|Count|Total|Contagem de eventos mesclados processados e disponíveis com êxito para consulta|Nenhuma dimensão|
|IngressReceivedMessagesTimeLag|Atraso de tempo de mensagens recebidas de entrada|Segundos|Máximo|Diferença entre a hora em que a mensagem é enfileirada na origem do evento e a hora em que ela é processada na entrada|Nenhuma dimensão|
|IngressReceivedMessagesCountLag|Atraso na contagem de mensagens recebidas de entrada|Count|Average|Diferença entre o número de sequência da última mensagem enfileirada na partição de origem do evento e o número de sequência da mensagem sendo processada na entrada|Nenhuma dimensão|
|WarmStorageMaxProperties|Propriedades máximas de armazenamento quente|Count|Máximo|Número máximo de propriedades usadas permitidas pelo ambiente para SKU S1/S2 e número máximo de propriedades permitidas pelo armazenamento quente para o SKU PAYG|Nenhuma dimensão|
|WarmStorageUsedProperties|Propriedades usadas de armazenamento quente |Count|Máximo|Número de propriedades usadas pelo ambiente para SKU S1/S2 e número de propriedades usadas pelo armazenamento quente para SKU PAYG|Nenhuma dimensão|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Bytes Lidos de Disco/Seg|BytesPerSecond|Average|Taxa de transferência média do disco devido a operações de leitura no período de exemplo.|Nenhuma dimensão|
|DiskWriteBytesPerSecond|Bytes Escritos em Disco/Seg|BytesPerSecond|Average|Taxa de transferência média do disco devido a operações de gravação no período de exemplo.|Nenhuma dimensão|
|Bytes de Leitura do Disco|Bytes de Leitura do Disco|Bytes|Total|Taxa de transferência total do disco devido a operações de leitura no período de exemplo.|Nenhuma dimensão|
|Bytes de Escrita de Disco|Bytes de Escrita de Disco|Bytes|Total|Taxa de transferência total do disco devido a operações de gravação no período de exemplo.|Nenhuma dimensão|
|DiskReadOperations|Operações de leitura de disco|Count|Total|O número de operações de leitura de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Nenhuma dimensão|
|DiskWriteOperations|Operações de gravação de disco|Count|Total|O número de operações de gravação de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Nenhuma dimensão|
|Operações/Seg de Leitura do Disco|Operações/Seg de Leitura do Disco|CountPerSecond|Average|O número médio de operações de leitura de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Nenhuma dimensão|
|Operações/Seg de Escrita de Disco|Operações/Seg de Escrita de Disco|CountPerSecond|Average|O número médio de operações de gravação de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Nenhuma dimensão|
|DiskReadLatency|Latência de leitura de disco|Milissegundos|Average|Latência de leitura total. A soma das latências de leitura do dispositivo e do kernel.|Nenhuma dimensão|
|DiskWriteLatency|Latência de gravação de disco|Milissegundos|Average|Latência de gravação total. A soma das latências de gravação do kernel e do dispositivo.|Nenhuma dimensão|
|NetworkInBytesPerSecond|Rede em bytes/s|BytesPerSecond|Average|Taxa de transferência média de rede para o tráfego recebido.|Nenhuma dimensão|
|NetworkOutBytesPerSecond|Bytes de saída de rede/s|BytesPerSecond|Average|Taxa de transferência média de rede para o tráfego transmitido.|Nenhuma dimensão|
|Entrada de Rede|Entrada de Rede|Bytes|Total|Taxa de transferência total de rede para o tráfego recebido.|Nenhuma dimensão|
|Saída de Rede|Saída de Rede|Bytes|Total|Taxa de transferência total de rede para o tráfego transmitido.|Nenhuma dimensão|
|MemoryUsed|Memória usada|Bytes|Average|A quantidade de memória da máquina que está sendo usada pela VM.|Nenhuma dimensão|
|MemoryGranted|Memória concedida|Bytes|Average|A quantidade de memória que foi concedida à VM pelo host. A memória não é concedida ao host até que ele seja tocado uma vez e a memória concedida possa ser trocada ou bloqueada se o VMkernel precisar da memória.|Nenhuma dimensão|
|MemoryActive|Memória ativa|Bytes|Average|A quantidade de memória usada pela VM na última pequena janela de tempo. Esse é o número "verdadeiro" da quantidade de memória necessária para a VM no momento. A memória adicional não utilizada pode ser trocada ou por balão sem impacto no desempenho do convidado.|Nenhuma dimensão|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Average|A utilização da CPU. Esse valor é relatado com 100% que representa todos os núcleos de processador no sistema. Por exemplo, uma VM de 2 vias usando 50% de um sistema de quatro núcleos está usando completamente dois núcleos.|Nenhuma dimensão|
|PercentageCpuReady|Percentual de CPU pronta|Milissegundos|Total|O tempo de preparação é o tempo gasto aguardando que as CPUs fiquem disponíveis no intervalo de atualização anterior.|Nenhuma dimensão|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuPercentage|Percentagem de CPU|Percent|Average|Percentagem de CPU|Instância|
|MemoryPercentage|Percentagem de Memória|Percent|Average|Percentagem de Memória|Instância|
|DiskQueueLength|Comprimento da Fila de Discos|Count|Average|Comprimento da Fila de Discos|Instância|
|HttpQueueLength|Comprimento da Fila de http|Count|Average|Comprimento da Fila de http|Instância|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Saída de Dados|Bytes|Total|Saída de Dados|Instância|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/sites (exceto funções)

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
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Average|Conjunto de trabalho de memória|Instância|
|AverageMemoryWorkingSet|Média do conjunto de trabalho de memória|Bytes|Average|Média do conjunto de trabalho de memória|Instância|
|AverageResponseTime|Tempo de Resposta Médio|Segundos|Average|Tempo de Resposta Médio|Instância|
|AppConnections|Ligações|Count|Average|Ligações|Instância|
|Identificadores|N.º de Identificadores|Count|Average|N.º de Identificadores|Instância|
|Threads|Número de Threads|Count|Average|Número de Threads|Instância|
|PrivateBytes|Bytes Privados|Bytes|Average|Bytes Privados|Instância|
|IoReadBytesPerSecond|Ler Bytes Por Segundo de E/S|BytesPerSecond|Total|Ler Bytes Por Segundo de E/S|Instância|
|IoWriteBytesPerSecond|Bytes de Escrita Por Segundo de E/S|BytesPerSecond|Total|Bytes de Escrita Por Segundo de E/S|Instância|
|IoOtherBytesPerSecond|Outros Bytes Por Segundo de E/S|BytesPerSecond|Total|Outros Bytes Por Segundo de E/S|Instância|
|IoReadOperationsPerSecond|Bytes de Leitura Por Segundo de E/S|BytesPerSecond|Total|Bytes de Leitura Por Segundo de E/S|Instância|
|IoWriteOperationsPerSecond|Operações de Escrita Por Segundo de E/S|BytesPerSecond|Total|Operações de Escrita Por Segundo de E/S|Instância|
|IoOtherOperationsPerSecond|Outras Operações Por Segundo de E/S|BytesPerSecond|Total|Outras Operações Por Segundo de E/S|Instância|
|RequestsInApplicationQueue|Pedidos na Fila de Aplicação|Count|Average|Pedidos na Fila de Aplicação|Instância|
|CurrentAssemblies|Assemblagens Actuais|Count|Average|Assemblagens Actuais|Instância|
|TotalAppDomains|Domínios de Aplicação Totais|Count|Average|Domínios de Aplicação Totais|Instância|
|TotalAppDomainsUnloaded|Domínios de Aplicação Totais Descarregados|Count|Average|Domínios de Aplicação Totais Descarregados|Instância|
|Gen0Collections|Libertações de Memória da Ger 0|Count|Total|Libertações de Memória da Ger 0|Instância|
|Gen1Collections|Libertações de Memória da Ger 1|Count|Total|Libertações de Memória da Ger 1|Instância|
|Gen2Collections|Libertações de Memória da Ger 2|Count|Total|Libertações de Memória da Ger 2|Instância|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/sites (funções)

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Saída de Dados|Bytes|Total|Saída de Dados|Instância|
|Http5xx|Erros de Servidor http|Count|Total|Erros de Servidor http|Instância|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Average|Conjunto de trabalho de memória|Instância|
|AverageMemoryWorkingSet|Média do conjunto de trabalho de memória|Bytes|Average|Média do conjunto de trabalho de memória|Instância|
|FunctionExecutionUnits|Unidades de Execução de Funções|MB/milissegundos|Total|[Unidades de execução de função](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instância|
|FunctionExecutionCount|Contagem de Execuções de Função|Count|Total|Contagem de Execuções de Função|Instância|
|PrivateBytes|Bytes Privados|Bytes|Average|Bytes Privados|Instância|
|IoReadBytesPerSecond|Ler Bytes Por Segundo de E/S|BytesPerSecond|Total|Ler Bytes Por Segundo de E/S|Instância|
|IoWriteBytesPerSecond|Bytes de Escrita Por Segundo de E/S|BytesPerSecond|Total|Bytes de Escrita Por Segundo de E/S|Instância|
|IoOtherBytesPerSecond|Outros Bytes Por Segundo de E/S|BytesPerSecond|Total|Outros Bytes Por Segundo de E/S|Instância|
|IoReadOperationsPerSecond|Bytes de Leitura Por Segundo de E/S|BytesPerSecond|Total|Bytes de Leitura Por Segundo de E/S|Instância|
|IoWriteOperationsPerSecond|Operações de Escrita Por Segundo de E/S|BytesPerSecond|Total|Operações de Escrita Por Segundo de E/S|Instância|
|IoOtherOperationsPerSecond|Outras Operações Por Segundo de E/S|BytesPerSecond|Total|Outras Operações Por Segundo de E/S|Instância|
|RequestsInApplicationQueue|Pedidos na Fila de Aplicação|Count|Average|Pedidos na Fila de Aplicação|Instância|
|CurrentAssemblies|Assemblagens Actuais|Count|Average|Assemblagens Actuais|Instância|
|TotalAppDomains|Domínios de Aplicação Totais|Count|Average|Domínios de Aplicação Totais|Instância|
|TotalAppDomainsUnloaded|Domínios de Aplicação Totais Descarregados|Count|Average|Domínios de Aplicação Totais Descarregados|Instância|
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
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Average|Conjunto de trabalho de memória|Instância|
|AverageMemoryWorkingSet|Média do conjunto de trabalho de memória|Bytes|Average|Média do conjunto de trabalho de memória|Instância|
|AverageResponseTime|Tempo de Resposta Médio|Segundos|Average|Tempo de Resposta Médio|Instância|
|FunctionExecutionUnits|Unidades de Execução de Funções|Count|Total|Unidades de Execução de Funções|Instância|
|FunctionExecutionCount|Contagem de Execuções de Função|Count|Total|Contagem de Execuções de Função|Instância|
|AppConnections|Ligações|Count|Average|Ligações|Instância|
|Identificadores|N.º de Identificadores|Count|Average|N.º de Identificadores|Instância|
|Threads|Número de Threads|Count|Average|Número de Threads|Instância|
|PrivateBytes|Bytes Privados|Bytes|Average|Bytes Privados|Instância|
|IoReadBytesPerSecond|Ler Bytes Por Segundo de E/S|BytesPerSecond|Total|Ler Bytes Por Segundo de E/S|Instância|
|IoWriteBytesPerSecond|Bytes de Escrita Por Segundo de E/S|BytesPerSecond|Total|Bytes de Escrita Por Segundo de E/S|Instância|
|IoOtherBytesPerSecond|Outros Bytes Por Segundo de E/S|BytesPerSecond|Total|Outros Bytes Por Segundo de E/S|Instância|
|IoReadOperationsPerSecond|Bytes de Leitura Por Segundo de E/S|BytesPerSecond|Total|Bytes de Leitura Por Segundo de E/S|Instância|
|IoWriteOperationsPerSecond|Operações de Escrita Por Segundo de E/S|BytesPerSecond|Total|Operações de Escrita Por Segundo de E/S|Instância|
|IoOtherOperationsPerSecond|Outras Operações Por Segundo de E/S|BytesPerSecond|Total|Outras Operações Por Segundo de E/S|Instância|
|RequestsInApplicationQueue|Pedidos na Fila de Aplicação|Count|Average|Pedidos na Fila de Aplicação|Instância|
|CurrentAssemblies|Assemblagens Actuais|Count|Average|Assemblagens Actuais|Instância|
|TotalAppDomains|Domínios de Aplicação Totais|Count|Average|Domínios de Aplicação Totais|Instância|
|TotalAppDomainsUnloaded|Domínios de Aplicação Totais Descarregados|Count|Average|Domínios de Aplicação Totais Descarregados|Instância|
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
|AverageResponseTime|Tempo de Resposta Médio|Segundos|Average|Tempo de Resposta Médio|Instância|
|CpuPercentage|Percentagem de CPU|Percent|Average|Percentagem de CPU|Instância|
|MemoryPercentage|Percentagem de Memória|Percent|Average|Percentagem de Memória|Instância|
|DiskQueueLength|Comprimento da Fila de Discos|Count|Average|Comprimento da Fila de Discos|Instância|
|HttpQueueLength|Comprimento da Fila de http|Count|Average|Comprimento da Fila de http|Instância|
|ActiveRequests|Pedidos Ativos|Count|Total|Pedidos Ativos|Instância|
|TotalFrontEnds|Total de Front Ends|Count|Average|Total de Front Ends|Nenhuma dimensão|
|SmallAppServicePlanInstances|Trabalhadores do Plano de Serviço de Aplicações de Pequena Dimensão|Count|Average|Trabalhadores do Plano de Serviço de Aplicações de Pequena Dimensão|Nenhuma dimensão|
|MediumAppServicePlanInstances|Trabalhadores do Plano de Serviço de Aplicações de Média Dimensão|Count|Average|Trabalhadores do Plano de Serviço de Aplicações de Média Dimensão|Nenhuma dimensão|
|LargeAppServicePlanInstances|Trabalhadores do Plano de Serviço de Aplicações de Grande Dimensão|Count|Average|Trabalhadores do Plano de Serviço de Aplicações de Grande Dimensão|Nenhuma dimensão|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|WorkersTotal|Trabalhadores Totais|Count|Average|Trabalhadores Totais|Nenhuma dimensão|
|WorkersAvailable|Trabalhadores Disponíveis|Count|Average|Trabalhadores Disponíveis|Nenhuma dimensão|
|WorkersUsed|Trabalhadores Utilizados|Count|Average|Trabalhadores Utilizados|Nenhuma dimensão|
|CpuPercentage|Percentagem de CPU|Percent|Average|Percentagem de CPU|Instância|
|MemoryPercentage|Percentagem de Memória|Percent|Average|Percentagem de Memória|Instância|

## <a name="next-steps"></a>Passos seguintes
* [Leia sobre métricas no Azure Monitor](data-platform.md)
* [Criar alertas em métricas](alerts-overview.md)
* [Exportar métricas para armazenamento, Hub de eventos ou Log Analytics](diagnostic-logs-overview.md)
