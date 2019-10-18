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
ms.openlocfilehash: 4e2cd3b66385d37815d0fc51a70ca31aba25db29
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528622"
---
# <a name="supported-metrics-with-azure-monitor"></a>Métricas com suporte com Azure Monitor

O Azure Monitor fornece várias maneiras de interagir com as métricas, incluindo o gráfico delas no portal, acessá-las por meio da API REST ou consultá-las usando o PowerShell ou a CLI. Abaixo está uma lista completa de todas as métricas disponíveis atualmente com o pipeline de métrica do Azure Monitor. Outras métricas podem estar disponíveis no portal ou usando APIs herdadas. Esta lista abaixo inclui apenas as métricas disponíveis usando o pipeline de métrica de Azure Monitor consolidado. Para consultar e acessar essas métricas, use a [versão de api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Atualmente, o envio de métricas multidimensionais através das definições de diagnóstico não é suportado. As métricas com dimensões são exportadas como métricas dimensionais simples e agregadas em valores de dimensões.
>
> *Por exemplo*: a métrica “Mensagens Recebidas” num hub do Hub de Eventos pode ser explorada e representada ao nível da linha. No entanto, se for exportada através das definições de diagnóstico, a métrica será representada como todas as mensagens recebidas em todas as filas do hub do Hub de Eventos.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/Servers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
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

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
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


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalJob|Total de trabalhos|Contagem|Total|O número total de trabalhos|Runbook, status|
|TotalUpdateDeploymentRuns|Total de execuções de implantação de atualização|Contagem|Total|Total de execuções de implantação de atualização de software|SoftwareUpdateConfigurationName, status|
|TotalUpdateDeploymentMachineRuns|Total de execuções do computador de implantação de atualização|Contagem|Total|O computador de implantação de atualização de software total é executado em uma execução de implantação de atualização de software|SoftwareUpdateConfigurationName, status, Computadordedestino, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft. batch/batchAccounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CoreCount|Contagem de núcleos dedicados|Contagem|Total|Número total de núcleos dedicados na conta do lote|Sem dimensões|
|TotalNodeCount|Contagem de nós dedicados|Contagem|Total|Número total de nós dedicados na conta do lote|Sem dimensões|
|LowPriorityCoreCount|Contagem de núcleos de LowPriority|Contagem|Total|Número total de núcleos de baixa prioridade na conta do lote|Sem dimensões|
|TotalLowPriorityNodeCount|Contagem de nós de baixa prioridade|Contagem|Total|Número total de nós de baixa prioridade na conta do lote|Sem dimensões|
|CreatingNodeCount|Criando contagem de nós|Contagem|Total|Número de nós sendo criados|Sem dimensões|
|StartingNodeCount|Contagem de nós inicial|Contagem|Total|Número de nós iniciando|Sem dimensões|
|WaitingForStartTaskNodeCount|Aguardando a contagem de nó de tarefa inicial|Contagem|Total|Número de nós aguardando a conclusão da tarefa inicial|Sem dimensões|
|StartTaskFailedNodeCount|Falha na contagem de nós da tarefa inicial|Contagem|Total|Número de nós em que a tarefa inicial falhou|Sem dimensões|
|IdleNodeCount|Contagem de nós ociosos|Contagem|Total|Número de nós ociosos|Sem dimensões|
|OfflineNodeCount|Contagem de nós offline|Contagem|Total|Número de nós offline|Sem dimensões|
|RebootingNodeCount|Contagem de nós de reinicialização|Contagem|Total|Número de nós de reinicialização|Sem dimensões|
|ReimagingNodeCount|Contagem de nós de reimagem|Contagem|Total|Número de nós de reimagem|Sem dimensões|
|RunningNodeCount|Contagem de nós em execução|Contagem|Total|Número de nós em execução|Sem dimensões|
|LeavingPoolNodeCount|Saindo da contagem de nós do pool|Contagem|Total|Número de nós saindo do pool|Sem dimensões|
|UnusableNodeCount|Contagem de nós inutilizáveis|Contagem|Total|Número de nós inutilizáveis|Sem dimensões|
|PreemptedNodeCount|Contagem de nós preempção|Contagem|Total|Número de nós admitidos|Sem dimensões|
|TaskStartEvent|Eventos de início de tarefa|Contagem|Total|Número total de tarefas que foram iniciadas|Sem dimensões|
|TaskCompleteEvent|Eventos de conclusão de tarefa|Contagem|Total|Número total de tarefas que foram concluídas|Sem dimensões|
|TaskFailEvent|Eventos de falha de tarefa|Contagem|Total|Número total de tarefas que foram concluídas em um estado de falha|Sem dimensões|
|PoolCreateEvent|Eventos de criação de pool|Contagem|Total|Número total de pools que foram criados|Sem dimensões|
|PoolResizeStartEvent|Eventos de início de redimensionamento do pool|Contagem|Total|Número total de redimensionamentos de pool que foram iniciados|Sem dimensões|
|PoolResizeCompleteEvent|Eventos completos de redimensionamento de pool|Contagem|Total|Número total de redimensionamentos de pool que foram concluídos|Sem dimensões|
|PoolDeleteStartEvent|Eventos de início de exclusão de pool|Contagem|Total|Número total de exclusões de pool que iniciaram|Sem dimensões|
|PoolDeleteCompleteEvent|Eventos completos de exclusão de pool|Contagem|Total|Número total de exclusões de pool que foram concluídas|Sem dimensões|
|JobDeleteCompleteEvent|Eventos completos de exclusão de trabalho|Contagem|Total|Número total de trabalhos que foram excluídos com êxito.|Sem dimensões|
|JobDeleteStartEvent|Eventos de início de exclusão de trabalho|Contagem|Total|Número total de trabalhos que foram solicitados a serem excluídos.|Sem dimensões|
|JobDisableCompleteEvent|Trabalho de desabilitar eventos concluídos|Contagem|Total|Número total de trabalhos que foram desabilitados com êxito.|Sem dimensões|
|JobDisableStartEvent|Eventos de início de desabilitação do trabalho|Contagem|Total|Número total de trabalhos que foram solicitados para serem desabilitados.|Sem dimensões|
|JobStartEvent|Eventos de início do trabalho|Contagem|Total|Número total de trabalhos iniciados com êxito.|Sem dimensões|
|JobTerminateCompleteEvent|Eventos de término de trabalho concluídos|Contagem|Total|Número total de trabalhos que foram encerrados com êxito.|Sem dimensões|
|JobTerminateStartEvent|Eventos de início de término do trabalho|Contagem|Total|Número total de trabalhos que foram solicitados a serem encerrados.|Sem dimensões|

## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|connectedclients|Clientes conectados|Contagem|Máximo||Fragmentoid|
|totalcommandsprocessed|Total de operações|Contagem|Total||Fragmentoid|
|cachehits|Acertos do cache|Contagem|Total||Fragmentoid|
|cachemisses|Erros de cache|Contagem|Total||Fragmentoid|
|comandos|Visível|Contagem|Total||Fragmentoid|
|comandos|Defina|Contagem|Total||Fragmentoid|
|operationsPerSecond|Operações por segundo|Contagem|Máximo||Fragmentoid|
|evictedkeys|Chaves removidas|Contagem|Total||Fragmentoid|
|totalkeys|Total de chaves|Contagem|Máximo||Fragmentoid|
|expiredkeys|Chaves expiradas|Contagem|Total||Fragmentoid|
|usedmemory|Memória usada|Bytes|Máximo||Fragmentoid|
|usedmemorypercentage|Porcentagem de memória usada|Percentagem|Máximo||Fragmentoid|
|usedmemoryRss|Memória RSS usada|Bytes|Máximo||Fragmentoid|
|serverLoad|Carga do servidor|Percentagem|Máximo||Fragmentoid|
|cacheWrite|Gravação de cache|BytesPerSecond|Máximo||Fragmentoid|
|cacheRead|Leitura de cache|BytesPerSecond|Máximo||Fragmentoid|
|percentProcessorTime|CPU|Percentagem|Máximo||Fragmentoid|
|cacheLatency|Microssegundos de latência de cache (versão prévia)|Contagem|Média||Fragmentid, Amostratype|
|erros|Erros|Contagem|Máximo||Fragmentid, ErrorType|
|connectedclients0|Clientes conectados (fragmento 0)|Contagem|Máximo||Sem dimensões|
|totalcommandsprocessed0|Total de operações (fragmento 0)|Contagem|Total||Sem dimensões|
|cachehits0|Acertos do cache (fragmento 0)|Contagem|Total||Sem dimensões|
|cachemisses0|Erros de cache (fragmento 0)|Contagem|Total||Sem dimensões|
|getcommands0|Gets (fragmento 0)|Contagem|Total||Sem dimensões|
|setcommands0|Sets (fragmento 0)|Contagem|Total||Sem dimensões|
|operationsPerSecond0|Operações por segundo (fragmento 0)|Contagem|Máximo||Sem dimensões|
|evictedkeys0|Chaves removidas (fragmento 0)|Contagem|Total||Sem dimensões|
|totalkeys0|Total de chaves (fragmento 0)|Contagem|Máximo||Sem dimensões|
|expiredkeys0|Chaves expiradas (fragmento 0)|Contagem|Total||Sem dimensões|
|usedmemory0|Memória usada (fragmento 0)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss0|Memória RSS usada (fragmento 0)|Bytes|Máximo||Sem dimensões|
|serverLoad0|Carga do servidor (fragmento 0)|Percentagem|Máximo||Sem dimensões|
|cacheWrite0|Gravação no cache (fragmento 0)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead0|Leitura de cache (fragmento 0)|BytesPerSecond|Máximo||Sem dimensões|
|percentProcessorTime0|CPU (fragmento 0)|Percentagem|Máximo||Sem dimensões|
|connectedclients1|Clientes conectados (fragmento 1)|Contagem|Máximo||Sem dimensões|
|totalcommandsprocessed1|Total de operações (fragmento 1)|Contagem|Total||Sem dimensões|
|cachehits1|Acertos do cache (fragmento 1)|Contagem|Total||Sem dimensões|
|cachemisses1|Erros de cache (fragmento 1)|Contagem|Total||Sem dimensões|
|getcommands1|Gets (fragmento 1)|Contagem|Total||Sem dimensões|
|setcommands1|Sets (fragmento 1)|Contagem|Total||Sem dimensões|
|operationsPerSecond1|Operações por segundo (fragmento 1)|Contagem|Máximo||Sem dimensões|
|evictedkeys1|Chaves removidas (fragmento 1)|Contagem|Total||Sem dimensões|
|totalkeys1|Total de chaves (fragmento 1)|Contagem|Máximo||Sem dimensões|
|expiredkeys1|Chaves expiradas (fragmento 1)|Contagem|Total||Sem dimensões|
|usedmemory1|Memória usada (fragmento 1)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss1|Memória RSS usada (fragmento 1)|Bytes|Máximo||Sem dimensões|
|serverLoad1|Carga do servidor (fragmento 1)|Percentagem|Máximo||Sem dimensões|
|cacheWrite1|Gravação no cache (fragmento 1)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead1|Leitura do cache (fragmento 1)|BytesPerSecond|Máximo||Sem dimensões|
|percentProcessorTime1|CPU (fragmento 1)|Percentagem|Máximo||Sem dimensões|
|connectedclients2|Clientes conectados (fragmento 2)|Contagem|Máximo||Sem dimensões|
|totalcommandsprocessed2|Total de operações (fragmento 2)|Contagem|Total||Sem dimensões|
|cachehits2|Acertos do cache (fragmento 2)|Contagem|Total||Sem dimensões|
|cachemisses2|Erros de cache (fragmento 2)|Contagem|Total||Sem dimensões|
|getcommands2|Gets (fragmento 2)|Contagem|Total||Sem dimensões|
|setcommands2|Sets (fragmento 2)|Contagem|Total||Sem dimensões|
|operationsPerSecond2|Operações por segundo (fragmento 2)|Contagem|Máximo||Sem dimensões|
|evictedkeys2|Chaves removidas (fragmento 2)|Contagem|Total||Sem dimensões|
|totalkeys2|Total de chaves (fragmento 2)|Contagem|Máximo||Sem dimensões|
|expiredkeys2|Chaves expiradas (fragmento 2)|Contagem|Total||Sem dimensões|
|usedmemory2|Memória usada (fragmento 2)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss2|Memória RSS usada (fragmento 2)|Bytes|Máximo||Sem dimensões|
|serverLoad2|Carga do servidor (fragmento 2)|Percentagem|Máximo||Sem dimensões|
|cacheWrite2|Gravação no cache (fragmento 2)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead2|Leitura de cache (fragmento 2)|BytesPerSecond|Máximo||Sem dimensões|
|percentProcessorTime2|CPU (fragmento 2)|Percentagem|Máximo||Sem dimensões|
|connectedclients3|Clientes conectados (fragmento 3)|Contagem|Máximo||Sem dimensões|
|totalcommandsprocessed3|Total de operações (fragmento 3)|Contagem|Total||Sem dimensões|
|cachehits3|Acertos do cache (fragmento 3)|Contagem|Total||Sem dimensões|
|cachemisses3|Erros de cache (fragmento 3)|Contagem|Total||Sem dimensões|
|getcommands3|Gets (fragmento 3)|Contagem|Total||Sem dimensões|
|setcommands3|Sets (fragmento 3)|Contagem|Total||Sem dimensões|
|operationsPerSecond3|Operações por segundo (fragmento 3)|Contagem|Máximo||Sem dimensões|
|evictedkeys3|Chaves removidas (fragmento 3)|Contagem|Total||Sem dimensões|
|totalkeys3|Total de chaves (fragmento 3)|Contagem|Máximo||Sem dimensões|
|expiredkeys3|Chaves expiradas (fragmento 3)|Contagem|Total||Sem dimensões|
|usedmemory3|Memória usada (fragmento 3)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss3|Memória RSS usada (fragmento 3)|Bytes|Máximo||Sem dimensões|
|serverLoad3|Carga do servidor (fragmento 3)|Percentagem|Máximo||Sem dimensões|
|cacheWrite3|Gravação no cache (fragmento 3)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead3|Leitura do cache (fragmento 3)|BytesPerSecond|Máximo||Sem dimensões|
|percentProcessorTime3|CPU (fragmento 3)|Percentagem|Máximo||Sem dimensões|
|connectedclients4|Clientes conectados (fragmento 4)|Contagem|Máximo||Sem dimensões|
|totalcommandsprocessed4|Total de operações (fragmento 4)|Contagem|Total||Sem dimensões|
|cachehits4|Acertos do cache (fragmento 4)|Contagem|Total||Sem dimensões|
|cachemisses4|Erros de cache (fragmento 4)|Contagem|Total||Sem dimensões|
|getcommands4|Gets (fragmento 4)|Contagem|Total||Sem dimensões|
|setcommands4|Sets (fragmento 4)|Contagem|Total||Sem dimensões|
|operationsPerSecond4|Operações por segundo (fragmento 4)|Contagem|Máximo||Sem dimensões|
|evictedkeys4|Chaves removidas (fragmento 4)|Contagem|Total||Sem dimensões|
|totalkeys4|Total de chaves (fragmento 4)|Contagem|Máximo||Sem dimensões|
|expiredkeys4|Chaves expiradas (fragmento 4)|Contagem|Total||Sem dimensões|
|usedmemory4|Memória usada (fragmento 4)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss4|Memória RSS usada (fragmento 4)|Bytes|Máximo||Sem dimensões|
|serverLoad4|Carga do servidor (fragmento 4)|Percentagem|Máximo||Sem dimensões|
|cacheWrite4|Gravação no cache (fragmento 4)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead4|Leitura do cache (fragmento 4)|BytesPerSecond|Máximo||Sem dimensões|
|percentProcessorTime4|CPU (fragmento 4)|Percentagem|Máximo||Sem dimensões|
|connectedclients5|Clientes conectados (fragmento 5)|Contagem|Máximo||Sem dimensões|
|totalcommandsprocessed5|Total de operações (fragmento 5)|Contagem|Total||Sem dimensões|
|cachehits5|Acertos do cache (fragmento 5)|Contagem|Total||Sem dimensões|
|cachemisses5|Erros de cache (fragmento 5)|Contagem|Total||Sem dimensões|
|getcommands5|Gets (fragmento 5)|Contagem|Total||Sem dimensões|
|setcommands5|Sets (fragmento 5)|Contagem|Total||Sem dimensões|
|operationsPerSecond5|Operações por segundo (fragmento 5)|Contagem|Máximo||Sem dimensões|
|evictedkeys5|Chaves removidas (fragmento 5)|Contagem|Total||Sem dimensões|
|totalkeys5|Total de chaves (fragmento 5)|Contagem|Máximo||Sem dimensões|
|expiredkeys5|Chaves expiradas (fragmento 5)|Contagem|Total||Sem dimensões|
|usedmemory5|Memória usada (fragmento 5)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss5|Memória RSS usada (fragmento 5)|Bytes|Máximo||Sem dimensões|
|serverLoad5|Carga do servidor (fragmento 5)|Percentagem|Máximo||Sem dimensões|
|cacheWrite5|Gravação no cache (fragmento 5)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead5|Leitura do cache (fragmento 5)|BytesPerSecond|Máximo||Sem dimensões|
|percentProcessorTime5|CPU (fragmento 5)|Percentagem|Máximo||Sem dimensões|
|connectedclients6|Clientes conectados (fragmento 6)|Contagem|Máximo||Sem dimensões|
|totalcommandsprocessed6|Total de operações (fragmento 6)|Contagem|Total||Sem dimensões|
|cachehits6|Acertos do cache (fragmento 6)|Contagem|Total||Sem dimensões|
|cachemisses6|Erros de cache (fragmento 6)|Contagem|Total||Sem dimensões|
|getcommands6|Gets (fragmento 6)|Contagem|Total||Sem dimensões|
|setcommands6|Sets (fragmento 6)|Contagem|Total||Sem dimensões|
|operationsPerSecond6|Operações por segundo (fragmento 6)|Contagem|Máximo||Sem dimensões|
|evictedkeys6|Chaves removidas (fragmento 6)|Contagem|Total||Sem dimensões|
|totalkeys6|Total de chaves (fragmento 6)|Contagem|Máximo||Sem dimensões|
|expiredkeys6|Chaves expiradas (fragmento 6)|Contagem|Total||Sem dimensões|
|usedmemory6|Memória usada (fragmento 6)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss6|Memória RSS usada (fragmento 6)|Bytes|Máximo||Sem dimensões|
|serverLoad6|Carga do servidor (fragmento 6)|Percentagem|Máximo||Sem dimensões|
|cacheWrite6|Gravação no cache (fragmento 6)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead6|Leitura do cache (fragmento 6)|BytesPerSecond|Máximo||Sem dimensões|
|percentProcessorTime6|CPU (fragmento 6)|Percentagem|Máximo||Sem dimensões|
|connectedclients7|Clientes conectados (fragmento 7)|Contagem|Máximo||Sem dimensões|
|totalcommandsprocessed7|Total de operações (fragmento 7)|Contagem|Total||Sem dimensões|
|cachehits7|Acertos do cache (fragmento 7)|Contagem|Total||Sem dimensões|
|cachemisses7|Erros de cache (fragmento 7)|Contagem|Total||Sem dimensões|
|getcommands7|Gets (fragmento 7)|Contagem|Total||Sem dimensões|
|setcommands7|Sets (fragmento 7)|Contagem|Total||Sem dimensões|
|operationsPerSecond7|Operações por segundo (fragmento 7)|Contagem|Máximo||Sem dimensões|
|evictedkeys7|Chaves removidas (fragmento 7)|Contagem|Total||Sem dimensões|
|totalkeys7|Total de chaves (fragmento 7)|Contagem|Máximo||Sem dimensões|
|expiredkeys7|Chaves expiradas (fragmento 7)|Contagem|Total||Sem dimensões|
|usedmemory7|Memória usada (fragmento 7)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss7|Memória RSS usada (fragmento 7)|Bytes|Máximo||Sem dimensões|
|serverLoad7|Carga do servidor (fragmento 7)|Percentagem|Máximo||Sem dimensões|
|cacheWrite7|Gravação no cache (fragmento 7)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead7|Leitura do cache (fragmento 7)|BytesPerSecond|Máximo||Sem dimensões|
|percentProcessorTime7|CPU (fragmento 7)|Percentagem|Máximo||Sem dimensões|
|connectedclients8|Clientes conectados (fragmento 8)|Contagem|Máximo||Sem dimensões|
|totalcommandsprocessed8|Total de operações (fragmento 8)|Contagem|Total||Sem dimensões|
|cachehits8|Acertos do cache (fragmento 8)|Contagem|Total||Sem dimensões|
|cachemisses8|Erros de cache (fragmento 8)|Contagem|Total||Sem dimensões|
|getcommands8|Gets (fragmento 8)|Contagem|Total||Sem dimensões|
|setcommands8|Sets (fragmento 8)|Contagem|Total||Sem dimensões|
|operationsPerSecond8|Operações por segundo (fragmento 8)|Contagem|Máximo||Sem dimensões|
|evictedkeys8|Chaves removidas (fragmento 8)|Contagem|Total||Sem dimensões|
|totalkeys8|Total de chaves (fragmento 8)|Contagem|Máximo||Sem dimensões|
|expiredkeys8|Chaves expiradas (fragmento 8)|Contagem|Total||Sem dimensões|
|usedmemory8|Memória usada (fragmento 8)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss8|Memória RSS usada (fragmento 8)|Bytes|Máximo||Sem dimensões|
|serverLoad8|Carga do servidor (fragmento 8)|Percentagem|Máximo||Sem dimensões|
|cacheWrite8|Gravação no cache (fragmento 8)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead8|Leitura de cache (fragmento 8)|BytesPerSecond|Máximo||Sem dimensões|
|percentProcessorTime8|CPU (fragmento 8)|Percentagem|Máximo||Sem dimensões|
|connectedclients9|Clientes conectados (fragmento 9)|Contagem|Máximo||Sem dimensões|
|totalcommandsprocessed9|Total de operações (fragmento 9)|Contagem|Total||Sem dimensões|
|cachehits9|Acertos do cache (fragmento 9)|Contagem|Total||Sem dimensões|
|cachemisses9|Erros de cache (fragmento 9)|Contagem|Total||Sem dimensões|
|getcommands9|Gets (fragmento 9)|Contagem|Total||Sem dimensões|
|setcommands9|Sets (fragmento 9)|Contagem|Total||Sem dimensões|
|operationsPerSecond9|Operações por segundo (fragmento 9)|Contagem|Máximo||Sem dimensões|
|evictedkeys9|Chaves removidas (fragmento 9)|Contagem|Total||Sem dimensões|
|totalkeys9|Total de chaves (fragmento 9)|Contagem|Máximo||Sem dimensões|
|expiredkeys9|Chaves expiradas (fragmento 9)|Contagem|Total||Sem dimensões|
|usedmemory9|Memória usada (fragmento 9)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss9|Memória RSS usada (fragmento 9)|Bytes|Máximo||Sem dimensões|
|serverLoad9|Carga do servidor (fragmento 9)|Percentagem|Máximo||Sem dimensões|
|cacheWrite9|Gravação no cache (fragmento 9)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead9|Leitura do cache (fragmento 9)|BytesPerSecond|Máximo||Sem dimensões|
|percentProcessorTime9|CPU (fragmento 9)|Percentagem|Máximo||Sem dimensões|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A porcentagem de unidades de computação alocadas que estão atualmente em uso pelas máquinas virtuais.|Sem dimensões|
|Entrada na Rede|Entrada na Rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada).|Sem dimensões|
|Saída da Rede|Saída da Rede|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída).|Sem dimensões|
|Bytes de leitura de disco/s|Leitura de disco|BytesPerSecond|Média|Média de bytes lidos do disco durante o período de monitoramento.|Sem dimensões|
|Bytes de gravação no disco/s|Gravação de disco|BytesPerSecond|Média|Média de bytes gravados no disco durante o período de monitoramento.|Sem dimensões|
|Operações de leitura de disco/s|Operações de leitura de disco/s|CountPerSecond|Média|IOPS de leitura de disco.|Sem dimensões|
|Operações de gravação de disco/s|Operações de gravação de disco/s|CountPerSecond|Média|IOPS de gravação de disco.|Sem dimensões|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft. ClassicCompute/nome_do_domínio/Slots/funções

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A porcentagem de unidades de computação alocadas que estão atualmente em uso pelas máquinas virtuais.|RoleInstanceId|
|Entrada na Rede|Entrada na Rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada).|RoleInstanceId|
|Saída da Rede|Saída da Rede|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída).|RoleInstanceId|
|Bytes de leitura de disco/s|Leitura de disco|BytesPerSecond|Média|Média de bytes lidos do disco durante o período de monitoramento.|RoleInstanceId|
|Bytes de gravação no disco/s|Gravação de disco|BytesPerSecond|Média|Média de bytes gravados no disco durante o período de monitoramento.|RoleInstanceId|
|Operações de leitura de disco/s|Operações de leitura de disco/s|CountPerSecond|Média|IOPS de leitura de disco.|RoleInstanceId|
|Operações de gravação de disco/s|Operações de gravação de disco/s|CountPerSecond|Média|IOPS de gravação de disco.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. Cognitivaservices/contas

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalCalls|Total de chamadas|Contagem|Total|Número total de chamadas.|ApiName, OperationName, região|
|SuccessfulCalls|Chamadas com êxito|Contagem|Total|Número de chamadas bem-sucedidas.|ApiName, OperationName, região|
|TotalErrors|Total de erros|Contagem|Total|Número total de chamadas com resposta de erro (código de resposta HTTP 4xx ou 5xx).|ApiName, OperationName, região|
|BlockedCalls|Chamadas bloqueadas|Contagem|Total|Número de chamadas que excederam a taxa ou o limite de cota.|ApiName, OperationName, região|
|ServerErrors|Erros do servidor|Contagem|Total|Número de chamadas com erro interno do serviço (código de resposta HTTP 5xx).|ApiName, OperationName, região|
|ClientErrors|Erros do cliente|Contagem|Total|Número de chamadas com erro do lado do cliente (código de resposta HTTP 4xx).|ApiName, OperationName, região|
|Dados de|Dados em|Bytes|Total|Tamanho dos dados de entrada em bytes.|ApiName, OperationName, região|
|Data de|Saída de dados|Bytes|Total|Tamanho dos dados de saída em bytes.|ApiName, OperationName, região|
|Latência|Latência|Milissegundos|Média|Latência em milissegundos.|ApiName, OperationName, região|
|CharactersTranslated|Caracteres traduzidos|Contagem|Total|Número total de caracteres na solicitação de texto de entrada.|ApiName, OperationName, região|
|CharactersTrained|Caracteres treinados|Contagem|Total|Número total de caracteres treinados.|ApiName, OperationName, região|
|SpeechSessionDuration|Duração da sessão de fala|Segundos|Total|Duração total da sessão de fala em segundos.|ApiName, OperationName, região|
|TotalTransactions|Total de transações|Contagem|Total|Número total de transações.|Sem dimensões|
|TotalTokenCalls|Total de chamadas de token|Contagem|Total|Número total de chamadas de token.|ApiName, OperationName, região|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A porcentagem de unidades de computação alocadas que estão atualmente em uso pelas máquinas virtuais|Sem dimensões|
|Entrada na Rede|Rede em Faturável|Bytes|Total|O número de bytes faturáveis recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada)|Sem dimensões|
|Saída da Rede|Saída de rede Faturável|Bytes|Total|O número de bytes faturáveis de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída)|Sem dimensões|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|Sem dimensões|
|Bytes de gravação no disco|Bytes de gravação no disco|Bytes|Total|Bytes gravados no disco durante o período de monitoramento|Sem dimensões|
|Operações de leitura de disco/s|Operações de leitura de disco/s|CountPerSecond|Média|IOPS de leitura de disco|Sem dimensões|
|Operações de gravação de disco/s|Operações de gravação de disco/s|CountPerSecond|Média|IOPS de gravação de disco|Sem dimensões|
|Créditos de CPU restantes|Créditos de CPU restantes|Contagem|Média|Número total de créditos disponíveis para intermitência|Sem dimensões|
|Créditos de CPU consumidos|Créditos de CPU consumidos|Contagem|Média|Número total de créditos consumidos pela máquina virtual|Sem dimensões|
|Bytes de leitura por disco/s|Bytes de leitura do disco de dados/s (preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|SlotId|
|Bytes de gravação por disco/s|Bytes de gravação do disco de dados/s (preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|SlotId|
|Operações de leitura por disco/s|Operações de leitura de disco de dados/s (preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|SlotId|
|Operações de gravação por disco/s|Operações de gravação do disco de dados/s (preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|SlotId|
|QD por disco|Disco de dados QD (preterido)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|SlotId|
|Bytes de leitura por disco do so/s|Bytes de leitura do disco do so/s (preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Bytes de gravação por disco do so/s|Bytes de gravação de disco do sistema operacional/s (preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Sem dimensões|
|Operações de leitura de so por disco/s|Operações de leitura de disco do so/s (preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Operações de gravação de so por disco/s|Operações de gravação de disco do sistema operacional/s (preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Sistema operacional por disco QD|Disco do so QD (preterido)|Contagem|Média|Profundidade da fila de disco do so (ou comprimento da fila)|Sem dimensões|
|Bytes de leitura do disco de dados/s|Bytes de leitura do disco de dados/s (visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|LUN|
|Bytes de gravação do disco de dados/s|Bytes de gravação do disco de dados/s (visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|LUN|
|Operações de leitura de disco de dados/s|Operações de leitura de disco de dados/s (visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|LUN|
|Operações de gravação do disco de dados/s|Operações de gravação do disco de dados/s (visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|LUN|
|Profundidade da fila do disco de dados|Profundidade da fila do disco de dados (visualização)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|LUN|
|Bytes de leitura do disco do so/s|Bytes de leitura do disco do so/s (visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Bytes de gravação de disco do sistema operacional/s|Bytes de gravação de disco do sistema operacional/s (visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Sem dimensões|
|Operações de leitura de disco do so/s|Operações de leitura de disco do so/s (visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Operações de gravação de disco do so/s|Operações de gravação de disco do so/s (visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Profundidade da fila de disco do so|Profundidade da fila de disco do so (visualização)|Contagem|Média|Profundidade da fila de disco do so (ou comprimento da fila)|Sem dimensões|
|Fluxos de entrada|Fluxos de entrada (visualização)|Contagem|Média|Os fluxos de entrada são o número de fluxos atuais na direção de entrada (tráfego entrando na VM)|Sem dimensões|
|Fluxos de saída|Fluxos de saída (visualização)|Contagem|Média|Fluxos de saída são o número de fluxos atuais na direção de saída (tráfego saindo da VM)|Sem dimensões|
|Taxa máxima de criação de fluxos de entrada|Taxa máxima de criação de fluxos de entrada (visualização)|CountPerSecond|Média|A taxa de criação máxima de fluxos de entrada (tráfego entrando na VM)|Sem dimensões|
|Taxa máxima de criação de fluxos de saída|Taxa máxima de criação de fluxos de saída (versão prévia)|CountPerSecond|Média|A taxa de criação máxima de fluxos de saída (tráfego saindo da VM)|Sem dimensões|
|Cache do disco de dados Premium-acesso de leitura|Leitura do cache do disco de dados Premium (versão prévia)|Percentagem|Média|Cache do disco de dados Premium-acesso de leitura|LUN|
|Erro de leitura do cache do disco de dados Premium|Cache do disco de dados Premium-erro de leitura (visualização)|Percentagem|Média|Erro de leitura do cache do disco de dados Premium|LUN|
|Cache de disco do sistema operacional Premium-acesso de leitura|Cache de disco do sistema operacional Premium-acesso de leitura (versão prévia)|Percentagem|Média|Cache de disco do sistema operacional Premium-acesso de leitura|Sem dimensões|
|Erro de leitura do cache de disco do so premium|Erro de leitura do cache de disco do so premium (visualização)|Percentagem|Média|Erro de leitura do cache de disco do so premium|Sem dimensões|
|Total de rede|Total de rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada)|Sem dimensões|
|Total de saída de rede|Total de saída de rede|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída)|Sem dimensões|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A porcentagem de unidades de computação alocadas que estão atualmente em uso pelas máquinas virtuais|VMName|
|Entrada na Rede|Rede em Faturável|Bytes|Total|O número de bytes faturáveis recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada)|VMName|
|Saída da Rede|Saída de rede Faturável|Bytes|Total|O número de bytes faturáveis de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída)|VMName|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|VMName|
|Bytes de gravação no disco|Bytes de gravação no disco|Bytes|Total|Bytes gravados no disco durante o período de monitoramento|VMName|
|Operações de leitura de disco/s|Operações de leitura de disco/s|CountPerSecond|Média|IOPS de leitura de disco|VMName|
|Operações de gravação de disco/s|Operações de gravação de disco/s|CountPerSecond|Média|IOPS de gravação de disco|VMName|
|Créditos de CPU restantes|Créditos de CPU restantes|Contagem|Média|Número total de créditos disponíveis para intermitência|Sem dimensões|
|Créditos de CPU consumidos|Créditos de CPU consumidos|Contagem|Média|Número total de créditos consumidos pela máquina virtual|Sem dimensões|
|Bytes de leitura por disco/s|Bytes de leitura do disco de dados/s (preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|SlotId|
|Bytes de gravação por disco/s|Bytes de gravação do disco de dados/s (preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|SlotId|
|Operações de leitura por disco/s|Operações de leitura de disco de dados/s (preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|SlotId|
|Operações de gravação por disco/s|Operações de gravação do disco de dados/s (preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|SlotId|
|QD por disco|Disco de dados QD (preterido)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|SlotId|
|Bytes de leitura por disco do so/s|Bytes de leitura do disco do so/s (preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Bytes de gravação por disco do so/s|Bytes de gravação de disco do sistema operacional/s (preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Sem dimensões|
|Operações de leitura de so por disco/s|Operações de leitura de disco do so/s (preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Operações de gravação de so por disco/s|Operações de gravação de disco do sistema operacional/s (preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Sistema operacional por disco QD|Disco do so QD (preterido)|Contagem|Média|Profundidade da fila de disco do so (ou comprimento da fila)|Sem dimensões|
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

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. ContainerInstance/containerGroups

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Os|Uso da CPU|Contagem|Média|Uso da CPU em todos os núcleos em milicores.|containerName|
|MemoryUsage|Uso de memória|Bytes|Média|Uso total de memória em byte.|containerName|
|NetworkBytesReceivedPerSecond|Bytes de rede recebidos por segundo|Bytes|Média|Os bytes de rede recebidos por segundo.|Sem dimensões|
|NetworkBytesTransmittedPerSecond|Bytes de rede transmitidos por segundo|Bytes|Média|Os bytes de rede transmitidos por segundo.|Sem dimensões|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/registros

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalPullCount|Contagem total de pull|Contagem|Média|Número de pulls de imagem no total|Sem dimensões|
|SuccessfulPullCount|Contagem de pull bem-sucedida|Contagem|Média|Número de pulls de imagem bem-sucedidos|Sem dimensões|
|TotalPushCount|Contagem total de push|Contagem|Média|Número de envios por push de imagem no total|Sem dimensões|
|SuccessfulPushCount|Contagem de push bem-sucedida|Contagem|Média|Número de Pushes de imagem bem-sucedidos|Sem dimensões|
|RunDuration|Duração da execução|Milissegundos|Total|Duração da execução em milissegundos|Sem dimensões|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. ContainerService/managedClusters

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Número total de núcleos de CPU disponíveis em um cluster gerenciado|Contagem|Total|Número total de núcleos de CPU disponíveis em um cluster gerenciado|Sem dimensões|
|kube_node_status_allocatable_memory_bytes|Quantidade total de memória disponível em um cluster gerenciado|Bytes|Total|Quantidade total de memória disponível em um cluster gerenciado|Sem dimensões|
|kube_pod_status_ready|Número de pods no estado pronto|Contagem|Total|Número de pods no estado pronto|namespace, Pod|
|kube_node_status_condition|Status para várias condições de nó|Contagem|Total|Status para várias condições de nó|condição, status, status2, nó|
|kube_pod_status_phase|Número de pods por fase|Contagem|Total|Número de pods por fase|fase, namespace, Pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft. CustomerInsights/hubs

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights chamadas à API|Contagem|Total||Sem dimensões|
|DCIMappingImportOperationSuccessfulLines|Mapeando linhas com êxito da operação de importação|Contagem|Total||Sem dimensões|
|DCIMappingImportOperationFailedLines|Linhas com falha na operação de importação de mapeamento|Contagem|Total||Sem dimensões|
|DCIMappingImportOperationTotalLines|Mapeamento de linhas totais da operação de importação|Contagem|Total||Sem dimensões|
|DCIMappingImportOperationRuntimeInSeconds|Mapeando tempo de execução da operação de importação em segundos|Segundos|Total||Sem dimensões|
|DCIOutboundProfileExportSucceeded|Exportação do perfil de saída bem-sucedida|Contagem|Total||Sem dimensões|
|DCIOutboundProfileExportFailed|Falha na exportação do perfil de saída|Contagem|Total||Sem dimensões|
|DCIOutboundProfileExportDuration|Duração da exportação do perfil de saída|Segundos|Total||Sem dimensões|
|DCIOutboundKpiExportSucceeded|Exportação de KPI de saída bem-sucedida|Contagem|Total||Sem dimensões|
|DCIOutboundKpiExportFailed|Falha na exportação do KPI de saída|Contagem|Total||Sem dimensões|
|DCIOutboundKpiExportDuration|Duração da exportação do KPI de saída|Segundos|Total||Sem dimensões|
|DCIOutboundKpiExportStarted|Exportação de KPI de saída iniciada|Segundos|Total||Sem dimensões|
|DCIOutboundKpiRecordCount|Contagem de registros de KPI de saída|Segundos|Total||Sem dimensões|
|DCIOutboundProfileExportCount|Contagem de exportação do perfil de saída|Segundos|Total||Sem dimensões|
|DCIOutboundInitialProfileExportFailed|Falha na exportação do perfil inicial de saída|Segundos|Total||Sem dimensões|
|DCIOutboundInitialProfileExportSucceeded|Exportação do perfil inicial de saída bem-sucedida|Segundos|Total||Sem dimensões|
|DCIOutboundInitialKpiExportFailed|Falha ao exportar o KPI inicial de saída|Segundos|Total||Sem dimensões|
|DCIOutboundInitialKpiExportSucceeded|Exportação de KPI inicial de saída bem-sucedida|Segundos|Total||Sem dimensões|
|DCIOutboundInitialProfileExportDurationInSeconds|Duração da exportação do perfil inicial de saída em segundos|Segundos|Total||Sem dimensões|
|AdlaJobForStandardKpiFailed|O trabalho do adla para KPI padrão falhou em segundos|Segundos|Total||Sem dimensões|
|AdlaJobForStandardKpiTimeOut|Trabalho de adla para o tempo limite de KPI padrão em segundos|Segundos|Total||Sem dimensões|
|AdlaJobForStandardKpiCompleted|Trabalho de adla para KPI padrão concluído em segundos|Segundos|Total||Sem dimensões|
|ImportASAValuesFailed|Falha na importação de valores ASA de conta|Contagem|Total||Sem dimensões|
|ImportASAValuesSucceeded|Contagem de importação de valores ASA com êxito|Contagem|Total||Sem dimensões|
|DCIProfilesCount|Contagem de instâncias de perfil|Contagem|última||Sem dimensões|
|DCIInteractionsPerMonthCount|A contagem de interações por mês|Contagem|última||Sem dimensões|
|DCIKpisCount|Contagem de KPIs|Contagem|última||Sem dimensões|
|DCISegmentsCount|Contagem de segmentos|Contagem|última||Sem dimensões|
|DCIPredictiveMatchPoliciesCount|Contagem de correspondência preditiva|Contagem|última||Sem dimensões|
|DCIPredictionsCount|Contagem de previsão|Contagem|última||Sem dimensões|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|NICReadThroughput|Taxa de transferência de leitura (rede)|BytesPerSecond|Média|A taxa de transferência de leitura da interface de rede no dispositivo no período de relatório para todos os volumes no gateway.|Instância|
|NICWriteThroughput|Taxa de transferência de gravação (rede)|BytesPerSecond|Média|A taxa de transferência de gravação da interface de rede no dispositivo no período de relatório para todos os volumes no gateway.|Instância|
|CloudReadThroughputPerShare|Taxa de transferência de download de nuvem (compartilhamento)|BytesPerSecond|Média|A taxa de transferência de download para o Azure de um compartilhamento durante o período de relatório.|Partilhar|
|CloudUploadThroughputPerShare|Taxa de transferência de upload de nuvem (compartilhamento)|BytesPerSecond|Média|A taxa de transferência de upload para o Azure de um compartilhamento durante o período de relatório.|Partilhar|
|BytesUploadedToCloudPerShare|Bytes de nuvem carregados (compartilhamento)|Bytes|Média|O número total de bytes que são carregados no Azure de um compartilhamento durante o período de relatório.|Partilhar|
|TotalCapacity|Capacidade total|Bytes|Média|Capacidade total|Sem dimensões|
|AvailableCapacity|Capacidade disponível|Bytes|Média|A capacidade disponível em bytes durante o período de relatório.|Sem dimensões|
|CloudUploadThroughput|Taxa de transferência de upload de nuvem|BytesPerSecond|Média|A taxa de transferência de upload de nuvem para o Azure durante o período de relatório.|Sem dimensões|
|CloudReadThroughput|Taxa de transferência de download da nuvem|BytesPerSecond|Média|A taxa de transferência de download de nuvem para o Azure durante o período de relatório.|Sem dimensões|
|BytesUploadedToCloud|Bytes de nuvem carregados (dispositivo)|Bytes|Média|O número total de bytes que são carregados no Azure de um dispositivo durante o período de relatório.|Sem dimensões|
|HyperVVirtualProcessorUtilization|Computação de borda – percentual de CPU|Percentagem|Média|Porcentagem de uso da CPU|Instância|
|HyperVMemoryUtilization|Computação de borda-uso de memória|Percentagem|Média|Quantidade de RAM em uso|Instância|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft. datafactory/datafactorings

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|FailedRuns|Execuções com falha|Contagem|Total||pipelinename, ActivityName|
|SuccessfulRuns|Execuções com êxito|Contagem|Total||pipelinename, ActivityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft. datafactory/fábricas

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PipelineFailedRuns|Métricas de execuções de pipeline com falha|Contagem|Total||FailureType, nome|
|PipelineSucceededRuns|Métricas de execuções de pipeline com êxito|Contagem|Total||FailureType, nome|
|ActivityFailedRuns|Métricas de execuções de atividade com falha|Contagem|Total||ActivityType, Pipelinename, Falhatype, nome|
|ActivitySucceededRuns|Métricas de execuções de atividades bem-sucedidas|Contagem|Total||ActivityType, Pipelinename, Falhatype, nome|
|TriggerFailedRuns|Métricas de execuções de gatilho com falha|Contagem|Total||Nome, Falhatype|
|TriggerSucceededRuns|Métricas de execuções de gatilho com êxito|Contagem|Total||Nome, Falhatype|
|IntegrationRuntimeCpuPercentage|Utilização da CPU do Integration Runtime|Percentagem|Média||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Memória disponível do Integration Runtime|Bytes|Média||IntegrationRuntimeName, NodeName|
|MaxAllowedResourceCount|Contagem máxima de entidades permitidas|Contagem|Máximo||Sem dimensões|
|MaxAllowedFactorySizeInGbUnits|Tamanho máximo de fábrica permitido (unidade GB)|Contagem|Máximo||Sem dimensões|
|ResourceCount|Contagem total de entidades|Contagem|Máximo||Sem dimensões|
|FactorySizeInGbUnits|Tamanho total de fábrica (unidade GB)|Contagem|Máximo||Sem dimensões|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/accounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|JobEndedSuccess|Trabalhos com êxito|Contagem|Total|Contagem de trabalhos bem-sucedidos.|Sem dimensões|
|JobEndedFailure|Trabalhos com falha|Contagem|Total|Contagem de trabalhos com falha.|Sem dimensões|
|JobEndedCancelled|Trabalhos cancelados|Contagem|Total|Contagem de trabalhos cancelados.|Sem dimensões|
|JobAUEndedSuccess|Tempo de AU bem-sucedido|Segundos|Total|Tempo total de AU para trabalhos bem-sucedidos.|Sem dimensões|
|JobAUEndedFailure|Tempo de AU com falha|Segundos|Total|Tempo total de AU para trabalhos com falha.|Sem dimensões|
|JobAUEndedCancelled|Hora de AU cancelada|Segundos|Total|Tempo total de AU para trabalhos cancelados.|Sem dimensões|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. DataLakeStore/accounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalStorage|Armazenamento Total|Bytes|Máximo|Quantidade total de dados armazenados na conta.|Sem dimensões|
|Gravado em|Dados gravados|Bytes|Total|Quantidade total de dados gravados na conta.|Sem dimensões|
|DataRead|Leitura de dados|Bytes|Total|Quantidade total de dados lidos da conta.|Sem dimensões|
|WriteRequests|Solicitações de gravação|Contagem|Total|Contagem de solicitações de gravação de dados para a conta.|Sem dimensões|
|ReadRequests|Solicitações de leitura|Contagem|Total|Contagem de solicitações de leitura de dados para a conta.|Sem dimensões|

## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/servidores

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentual de CPU|Percentagem|Média|Percentual de CPU|Sem dimensões|
|memory_percent|Porcentagem de memória|Percentagem|Média|Porcentagem de memória|Sem dimensões|
|io_consumption_percent|Porcentagem de e/s|Percentagem|Média|Porcentagem de e/s|Sem dimensões|
|storage_percent|Porcentagem de armazenamento|Percentagem|Média|Porcentagem de armazenamento|Sem dimensões|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Sem dimensões|
|storage_limit|Limite de armazenamento|Bytes|Média|Limite de armazenamento|Sem dimensões|
|serverlog_storage_percent|Porcentagem de armazenamento de log do servidor|Percentagem|Média|Porcentagem de armazenamento de log do servidor|Sem dimensões|
|serverlog_storage_usage|Armazenamento de log do servidor usado|Bytes|Média|Armazenamento de log do servidor usado|Sem dimensões|
|serverlog_storage_limit|Limite de armazenamento de log do servidor|Bytes|Média|Limite de armazenamento de log do servidor|Sem dimensões|
|active_connections|Conexões ativas|Contagem|Média|Conexões ativas|Sem dimensões|
|connections_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Sem dimensões|
|seconds_behind_master|Atraso de replicação em segundos|Contagem|Média|Atraso de replicação em segundos|Sem dimensões|
|backup_storage_used|Armazenamento de backup usado|Bytes|Média|Armazenamento de backup usado|Sem dimensões|
|network_bytes_egress|Saída da Rede|Bytes|Total|Saída de rede entre conexões ativas|Sem dimensões|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Entrada de rede em conexões ativas|Sem dimensões|

## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/servidores

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentual de CPU|Percentagem|Média|Percentual de CPU|Sem dimensões|
|memory_percent|Porcentagem de memória|Percentagem|Média|Porcentagem de memória|Sem dimensões|
|io_consumption_percent|Porcentagem de e/s|Percentagem|Média|Porcentagem de e/s|Sem dimensões|
|storage_percent|Porcentagem de armazenamento|Percentagem|Média|Porcentagem de armazenamento|Sem dimensões|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Sem dimensões|
|storage_limit|Limite de armazenamento|Bytes|Média|Limite de armazenamento|Sem dimensões|
|serverlog_storage_percent|Porcentagem de armazenamento de log do servidor|Percentagem|Média|Porcentagem de armazenamento de log do servidor|Sem dimensões|
|serverlog_storage_usage|Armazenamento de log do servidor usado|Bytes|Média|Armazenamento de log do servidor usado|Sem dimensões|
|serverlog_storage_limit|Limite de armazenamento de log do servidor|Bytes|Média|Limite de armazenamento de log do servidor|Sem dimensões|
|active_connections|Conexões ativas|Contagem|Média|Conexões ativas|Sem dimensões|
|connections_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Sem dimensões|
|seconds_behind_master|Atraso de replicação em segundos|Contagem|Média|Atraso de replicação em segundos|Sem dimensões|
|backup_storage_used|Armazenamento de backup usado|Bytes|Média|Armazenamento de backup usado|Sem dimensões|
|network_bytes_egress|Saída da Rede|Bytes|Total|Saída de rede entre conexões ativas|Sem dimensões|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Entrada de rede em conexões ativas|Sem dimensões|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/servidores

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentual de CPU|Percentagem|Média|Percentual de CPU|Sem dimensões|
|memory_percent|Porcentagem de memória|Percentagem|Média|Porcentagem de memória|Sem dimensões|
|io_consumption_percent|Porcentagem de e/s|Percentagem|Média|Porcentagem de e/s|Sem dimensões|
|storage_percent|Porcentagem de armazenamento|Percentagem|Média|Porcentagem de armazenamento|Sem dimensões|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Sem dimensões|
|storage_limit|Limite de armazenamento|Bytes|Média|Limite de armazenamento|Sem dimensões|
|serverlog_storage_percent|Porcentagem de armazenamento de log do servidor|Percentagem|Média|Porcentagem de armazenamento de log do servidor|Sem dimensões|
|serverlog_storage_usage|Armazenamento de log do servidor usado|Bytes|Média|Armazenamento de log do servidor usado|Sem dimensões|
|serverlog_storage_limit|Limite de armazenamento de log do servidor|Bytes|Média|Limite de armazenamento de log do servidor|Sem dimensões|
|active_connections|Conexões ativas|Contagem|Média|Conexões ativas|Sem dimensões|
|connections_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Sem dimensões|
|backup_storage_used|Armazenamento de backup usado|Bytes|Média|Armazenamento de backup usado|Sem dimensões|
|network_bytes_egress|Saída da Rede|Bytes|Total|Saída de rede entre conexões ativas|Sem dimensões|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Entrada de rede em conexões ativas|Sem dimensões|
|pg_replica_log_delay_in_seconds|Atraso de réplica|Segundos|Máximo|Atraso de réplica em segundos|Sem dimensões|
|pg_replica_log_delay_in_bytes|Atraso máximo entre réplicas|Bytes|Máximo|Atraso em bytes da réplica mais defasante|Sem dimensões|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentual de CPU|Percentagem|Média|Percentual de CPU|Sem dimensões|
|memory_percent|Porcentagem de memória|Percentagem|Média|Porcentagem de memória|Sem dimensões|
|IOPS|IOPS|Contagem|Média|Operações de e/s por segundo|Sem dimensões|
|storage_percent|Porcentagem de armazenamento|Percentagem|Média|Porcentagem de armazenamento|Sem dimensões|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Sem dimensões|
|active_connections|Conexões ativas|Contagem|Média|Conexões ativas|Sem dimensões|
|network_bytes_egress|Saída da Rede|Bytes|Total|Saída de rede entre conexões ativas|Sem dimensões|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Entrada de rede em conexões ativas|Sem dimensões|

## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|D2C. telemetria. ingress. MyProtocol|Tentativas de envio de mensagem de telemetria|Contagem|Total|Número de mensagens de telemetria do dispositivo para a nuvem que tentaram ser enviadas para o Hub IoT|Sem dimensões|
|D2C. telemetria. entrada. Success|Mensagens de telemetria enviadas|Contagem|Total|Número de mensagens de telemetria do dispositivo para a nuvem enviadas com êxito para o Hub IoT|Sem dimensões|
|C2D. Commands. egresso. Complete. Success|Comandos concluídos|Contagem|Total|Número de comandos da nuvem para o dispositivo concluídos com êxito pelo dispositivo|Sem dimensões|
|C2D. Commands. egresso. Abandon. Success|Comandos abandonados|Contagem|Total|Número de comandos da nuvem para o dispositivo abandonados pelo dispositivo|Sem dimensões|
|C2D. Commands. egresso. rejeição. Success|Comandos rejeitados|Contagem|Total|Número de comandos da nuvem para o dispositivo rejeitados pelo dispositivo|Sem dimensões|
|dispositivos. totalDevices|Total de dispositivos (preterido)|Contagem|Total|Número de dispositivos registrados no Hub IoT|Sem dimensões|
|Devices. connectedDevices. MyProtocol|Dispositivos conectados (preterido) |Contagem|Total|Número de dispositivos conectados ao seu hub IoT|Sem dimensões|
|D2C. telemetria. egresso. Success|Roteamento: mensagens de telemetria entregues|Contagem|Total|O número de vezes que as mensagens foram entregues com êxito a todos os pontos de extremidade usando o roteamento do Hub IoT. Se uma mensagem for roteada para vários pontos de extremidade, esse valor aumentará em um para cada entrega bem-sucedida. Se uma mensagem for entregue ao mesmo ponto de extremidade várias vezes, esse valor aumentará em um para cada entrega bem-sucedida.|Sem dimensões|
|D2C. telemetria. egresso. Descartado|Roteamento: mensagens de telemetria eliminadas |Contagem|Total|O número de vezes que as mensagens foram removidas pelo roteamento do Hub IoT devido a pontos de extremidade inativos. Esse valor não conta as mensagens entregues à rota de fallback, pois as mensagens ignoradas não são entregues lá.|Sem dimensões|
|D2C. telemetria. egresso. órfão|Roteamento: mensagens de telemetria órfãs |Contagem|Total|O número de vezes que as mensagens ficaram órfãs pelo roteamento do Hub IoT porque não corresponderam a nenhuma regra de roteamento (incluindo a norma de fallback). |Sem dimensões|
|D2C. telemetria. egresso. inválido|Roteamento: mensagens de telemetria incompatíveis|Contagem|Total|O número de vezes que o roteamento do Hub IoT falhou ao entregar mensagens devido a uma incompatibilidade com o ponto de extremidade. Esse valor não inclui repetições.|Sem dimensões|
|D2C. telemetria. egresso. fallback|Roteamento: mensagens entregues ao fallback|Contagem|Total|O número de vezes que o roteamento do Hub IoT fornece mensagens ao ponto de extremidade associado à rota de fallback.|Sem dimensões|
|D2C. endpoints. egresso. eventHubs|Roteamento: mensagens entregues ao Hub de eventos|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para pontos de extremidade do hub de eventos.|Sem dimensões|
|D2C. endpoints. latência. eventHubs|Roteamento: latência de mensagem para o Hub de eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem em um ponto de extremidade do hub de eventos.|Sem dimensões|
|D2C. endpoints. egresso. serviceBusQueues|Roteamento: mensagens entregues à fila do barramento de serviço|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens aos pontos de extremidade da fila do barramento de serviço.|Sem dimensões|
|D2C. endpoints. latência. serviceBusQueues|Roteamento: latência de mensagem para a fila do barramento de serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade da fila do barramento de serviço.|Sem dimensões|
|D2C. endpoints. egresso. serviceBusTopics|Roteamento: mensagens entregues ao tópico do barramento de serviço|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para pontos de extremidade de tópico do barramento de serviço.|Sem dimensões|
|D2C. endpoints. latência. serviceBusTopics|Roteamento: latência de mensagem para o tópico do barramento de serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade de tópico do barramento de serviço.|Sem dimensões|
|D2C. endpoints. egresso. builtIn. Events|Roteamento: mensagens entregues a mensagens/eventos|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para o ponto de extremidade interno (mensagens/eventos). Essa métrica só começa a funcionar quando o roteamento está habilitado (https://aka.ms/iotrouting) para o Hub IoT.|Sem dimensões|
|D2C. endpoints. latência. builtIn. Events|Roteamento: latência de mensagem para mensagens/eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem e o Hub IoT e a entrada da mensagem de telemetria no ponto de extremidade interno (mensagens/eventos). Essa métrica só começa a funcionar quando o roteamento está habilitado (https://aka.ms/iotrouting) para o Hub IoT.|Sem dimensões|
|pontos de D2C. end. de saída. armazenamento|Roteamento: mensagens entregues ao armazenamento|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para pontos de extremidade de armazenamento.|Sem dimensões|
|D2C. pontos de extremidade. latência. armazenamento|Roteamento: latência de mensagem para armazenamento|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade de armazenamento.|Sem dimensões|
|D2C. endpoints. egresso. Storage. bytes|Roteamento: dados entregues ao armazenamento|Bytes|Total|A quantidade de dados (bytes) de roteamento do Hub IoT entregues aos pontos de extremidade de armazenamento.|Sem dimensões|
|D2C. endpoints. egresso. Storage. BLOBs|Roteamento: BLOBs entregues ao armazenamento|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu BLOBs para pontos de extremidade de armazenamento.|Sem dimensões|
|EventGridDeliveries|Entregas da grade de eventos (visualização)|Contagem|Total|O número de eventos do Hub IoT publicados na grade de eventos. Use a dimensão de resultado para o número de solicitações bem-sucedidas e com falha. A dimensão EventType mostra o tipo de evento (https://aka.ms/ioteventgrid).|Resultado, EventType|
|EventGridLatency|A latência média (milissegundos) de quando o evento do Hub IOT foi gerado quando o evento foi publicado na grade de eventos. Esse número é uma média entre todos os tipos de evento. Use a dimensão EventType para ver a latência de um tipo específico de evento.|EventType|
|D2C. Add. Read. Success|Leituras de entrelaçamento bem-sucedidas de dispositivos|Contagem|Total|A contagem de todas as leituras de entrelaças iniciadas pelo dispositivo bem-sucedidas.|Sem dimensões|
|D2C. entrelaçar. Read. Failure|Leituras de entrelaçamento com falha de dispositivos|Contagem|Total|A contagem de todas as leituras de entrelaças iniciadas pelo dispositivo com falha.|Sem dimensões|
|D2C. ". Read. Size"|Tamanho da resposta de leituras de entrelaçamento de dispositivos|Bytes|Média|A média, o mínimo e o máximo de todas as leituras de entrelaçadas iniciadas pelo dispositivo bem-sucedidas.|Sem dimensões|
|D2C. Add. Update. Success|Atualizações de atualização com êxito de dispositivos|Contagem|Total|A contagem de todas as atualizações de atualização do dispositivo iniciadas com êxito.|Sem dimensões|
|D2C. Add. Update. Failure|Atualizações de atualização de falha de dispositivos|Contagem|Total|A contagem de todas as atualizações de atualização do dispositivo iniciadas com falha.|Sem dimensões|
|D2C. Add. Update. Size|Tamanho de atualizações de atualização de papel dos dispositivos|Bytes|Média|O tamanho médio, mínimo e máximo de todas as atualizações de atualização do dispositivo iniciadas com êxito.|Sem dimensões|
|C2D. Methods. Success|Invocações de método diretos bem-sucedidas|Contagem|Total|A contagem de todas as chamadas de método diretas bem-sucedidas.|Sem dimensões|
|C2D. Methods. Failure|Invocações de método direto com falha|Contagem|Total|A contagem de todas as chamadas de método diretas com falha.|Sem dimensões|
|C2D. Methods. Requests|Tamanho da solicitação de invocações de método direto|Bytes|Média|A média, o mínimo e o máximo de todas as solicitações de método diretas bem-sucedidas.|Sem dimensões|
|C2D. Methods. Responseize|Tamanho da resposta de invocações de método diretas|Bytes|Média|A média, o mínimo e o máximo de todas as respostas de método diretas bem-sucedidas.|Sem dimensões|
|C2D. Add. Read. Success|Leituras de cópia com êxito do back-end|Contagem|Total|A contagem de todas as leituras de cópia de bits iniciadas de back-end bem-sucedidas.|Sem dimensões|
|C2D. entrelaçar. Read. Failure|Leituras de cópia com falha do back-end|Contagem|Total|A contagem de todas as leituras de cópia de bits iniciadas pelo back-end com falha.|Sem dimensões|
|C2D. ". Read. Size"|Tamanho da resposta de leituras de cópia do back-end|Bytes|Média|A média, mín e máx de todas as leituras de cópia do back-end iniciadas com êxito.|Sem dimensões|
|C2D. Add. Update. Success|Atualizações de cópia bem-sucedida do back-end|Contagem|Total|A contagem de todas as atualizações de cópia do back-end iniciadas com êxito.|Sem dimensões|
|C2D. Add. Update. Failure|Atualizações de cópia com falha do back-end|Contagem|Total|A contagem de todas as atualizações de cópia de bits iniciadas pelo back-end com falha.|Sem dimensões|
|C2D. Add. Update. Size|Tamanho das atualizações de cópia do back-end|Bytes|Média|O tamanho médio, mínimo e máximo de todas as atualizações de cópia do back-end iniciadas com êxito.|Sem dimensões|
|twinQueries. Success|Consultas de entrelaçamento bem-sucedidas|Contagem|Total|A contagem de todas as consultas de myup com êxito.|Sem dimensões|
|twinQueries. Failure|Consultas de entrelaçamento com falha|Contagem|Total|A contagem de todas as consultas de entrelaçamento com falha.|Sem dimensões|
|Resultados de twinQueries. Results|Tamanho do resultado de consultas de entrelaçamento|Bytes|Média|A média, o mínimo e o máximo do tamanho do resultado de todas as consultas de myup com êxito.|Sem dimensões|
|trabalhos. createTwinUpdateJob. Success|Criações bem-sucedidas de trabalhos de atualização de entrelaçamento|Contagem|Total|A contagem de todas as criações de trabalhos de atualização de entrelaçamento com êxito.|Sem dimensões|
|trabalhos. createTwinUpdateJob. Failure|Criações com falha de trabalhos de atualização de entrelaçamento|Contagem|Total|A contagem de todas as falhas na criação de trabalhos de atualização de entrelaçamento.|Sem dimensões|
|trabalhos. createDirectMethodJob. Success|Criações de trabalhos de invocação de método com êxito|Contagem|Total|A contagem de toda a criação bem-sucedida de trabalhos de invocação de método direto.|Sem dimensões|
|trabalhos. createDirectMethodJob. Failure|Criações com falha de trabalhos de invocação de método|Contagem|Total|A contagem de todas as falhas na criação de trabalhos de invocação de método direto.|Sem dimensões|
|trabalhos. listJobs. Success|Chamadas com êxito para listar trabalhos|Contagem|Total|A contagem de todas as chamadas bem-sucedidas para listar trabalhos.|Sem dimensões|
|trabalhos. listJobs. Failure|Chamadas com falha para listar trabalhos|Contagem|Total|A contagem de todas as chamadas com falha para listar trabalhos.|Sem dimensões|
|trabalhos. cancelJob. Success|Cancelamentos de trabalho bem-sucedidos|Contagem|Total|A contagem de todas as chamadas bem-sucedidas para cancelar um trabalho.|Sem dimensões|
|trabalhos. cancelJob. Failure|Cancelamentos de trabalho com falha|Contagem|Total|A contagem de todas as chamadas com falha para cancelar um trabalho.|Sem dimensões|
|trabalhos. queryJobs. Success|Consultas de trabalho com êxito|Contagem|Total|A contagem de todas as chamadas bem-sucedidas para os trabalhos de consulta.|Sem dimensões|
|trabalhos. queryJobs. Failure|Consultas de trabalho com falha|Contagem|Total|A contagem de todas as chamadas com falha para trabalhos de consulta.|Sem dimensões|
|trabalhos. concluído|Trabalhos concluídos|Contagem|Total|A contagem de todos os trabalhos concluídos.|Sem dimensões|
|trabalhos. com falha|Trabalhos com falha|Contagem|Total|A contagem de todos os trabalhos com falha.|Sem dimensões|
|D2C. telemetria. entrada. sendThrottle|Número de erros de limitação|Contagem|Total|Número de erros de limitação devido a aceleradores de taxa de transferência do dispositivo|Sem dimensões|
|dailyMessageQuotaUsed|Número total de mensagens usadas|Contagem|Média|Número total de mensagens usadas hoje. Esse é um valor cumulativo que é redefinido para zero às 00:00 UTC todos os dias.|Sem dimensões|
|deviceDataUsage|Uso total de dados do dispositivo|Bytes|Total|Bytes transferidos de e para todos os dispositivos conectados ao IotHub|Sem dimensões|
|totalDeviceCount|Total de dispositivos (visualização)|Contagem|Média|Número de dispositivos registrados no Hub IoT|Sem dimensões|
|connectedDeviceCount|Dispositivos conectados (visualização)|Contagem|Média|Número de dispositivos conectados ao seu hub IoT|Sem dimensões|
|Figura|Métricas de configuração|Contagem|Total|Métricas para operações de configuração|Sem dimensões|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RegistrationAttempts|Tentativas de registro|Contagem|Total|Número de tentativas de registro de dispositivo|ProvisioningServiceName, IotHubName, status|
|DeviceAssignments|Dispositivos atribuídos|Contagem|Total|Número de dispositivos atribuídos a um hub IoT|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Tentativas de atestado|Contagem|Total|Número de tentativas de atestado de dispositivo|ProvisioningServiceName, status, protocolo|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft. DocumentDB/databaseAccounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AvailableStorage|Armazenamento disponível|Bytes|Total|Armazenamento total disponível relatado à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|CassandraConnectionClosures|Fechamentos de conexão do Cassandra|Contagem|Total|Número de conexões Cassandra que foram fechadas, relatadas a uma granularidade de 1 minuto|Região, ClosureReason|
|CassandraRequestCharges|Encargos de solicitação do Cassandra|Contagem|Total|RUs consumido para solicitações Cassandra feitas|DatabaseName, CollectionName, região, OperationType, ResourceType|
|CassandraRequests|Solicitações Cassandra|Contagem|Contagem|Número de solicitações Cassandra feitas|DatabaseName, CollectionName, região, OperationType, ResourceType, ErrorCode|
|Uso de|Utilização de Dados|Bytes|Total|Uso total de dados relatados à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|DocumentCount|Contagem de documentos|Contagem|Total|Contagem total de documentos relatados à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|DocumentQuota|Cota de documentos|Bytes|Total|Cota de armazenamento total relatada à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|IndexUsage|Uso do índice|Bytes|Total|Uso total do índice relatado à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|MetadataRequests|Solicitações de metadados|Contagem|Contagem|Contagem de solicitações de metadados. Cosmos DB mantém a coleção de metadados do sistema para cada conta, que permite enumerar coleções, bancos de dados, etc., e suas configurações, gratuitamente.|DatabaseName, CollectionName, região, StatusCode, |
|MongoRequestCharge|Encargo de solicitação do Mongo|Contagem|Total|Unidades de solicitação Mongo consumidas|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|MongoRequests|Solicitações Mongo|Contagem|Contagem|Número de solicitações Mongo feitas|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|ProvisionedThroughput|Débito Aprovisionado|Contagem|Máximo|Débito Aprovisionado|DatabaseName, CollectionName|
|ReplicationLatency|Latência de replicação P99|Milissegundos|Média|Latência de replicação P99 nas regiões de origem e de destino para a conta habilitada para Geografia|SourceRegion, TargetRegion|
|Indisponibilidade|Disponibilidade do serviço|Percentagem|Média|Disponibilidade de solicitações de conta em uma hora, granularidade de dia ou mês|Sem dimensões|
|TotalRequestUnits|Total de unidades de solicitação|Contagem|Total|Unidades de solicitação consumidas|DatabaseName, CollectionName, região, StatusCode, OperationType|
|TotalRequests|Total de Pedidos|Contagem|Contagem|Número de solicitações feitas|DatabaseName, CollectionName, região, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/tópicos

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Sem dimensões|
|PublishFailCount|Publicar eventos com falha|Contagem|Total|Falha no total de eventos ao publicar neste tópico|ErrorType, erro|
|UnmatchedEventCount|Eventos sem correspondência|Contagem|Total|Total de eventos que não correspondem a nenhuma das assinaturas de evento deste tópico|Sem dimensões|
|PublishSuccessLatencyInMs|Latência de êxito na publicação|Contagem|Total|Latência de êxito de publicação em milissegundos|Sem dimensões|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|MatchedEventCount|Eventos correspondentes|Contagem|Total|Total de eventos correspondidos a esta assinatura de evento|Sem dimensões|
|DeliveryAttemptFailCount|Eventos com falha na entrega|Contagem|Total|O total de eventos não pôde ser entregue a esta assinatura de evento|Erro, ErrorType|
|DeliverySuccessCount|Eventos entregues|Contagem|Total|Total de eventos entregues a esta assinatura de evento|Sem dimensões|
|DestinationProcessingDurationInMs|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|Sem dimensões|
|DroppedEventCount|Eventos ignorados|Contagem|Total|Total de eventos descartados correspondentes a esta assinatura de evento|DropReason|
|DeadLetteredCount|Eventos inativos|Contagem|Total|Total de eventos mortos inativos correspondentes a esta assinatura de evento|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Sem dimensões|
|PublishFailCount|Eventos com falha|Contagem|Total|Falha no total de eventos ao publicar neste tópico|ErrorType, erro|
|UnmatchedEventCount|Eventos sem correspondência|Contagem|Total|Total de eventos que não correspondem a nenhuma das assinaturas de evento deste tópico|Sem dimensões|
|PublishSuccessLatencyInMs|Latência de êxito na publicação|Contagem|Total|Latência de êxito de publicação em milissegundos|Sem dimensões|

## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/namespaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfulRequests|Solicitações bem-sucedidas|Contagem|Total|Solicitações bem-sucedidas para o Microsoft. EventHub.|EntityName |
|ServerErrors|Erros do servidor.|Contagem|Total|Erros de servidor para Microsoft. EventHub.|EntityName |
|UserErrors|Erros do usuário.|Contagem|Total|Erros de usuário para Microsoft. EventHub.|EntityName |
|QuotaExceededErrors|A cota excedeu erros.|Contagem|Total|A cota excedeu erros para o Microsoft. EventHub.|EntityName |
|ThrottledRequests|Solicitações limitadas.|Contagem|Total|Solicitações limitadas para Microsoft. EventHub.|EntityName |
|IncomingRequests|Solicitações de entrada|Contagem|Total|Solicitações de entrada para o Microsoft. EventHub.|entityName|
|IncomingMessages|Mensagens de entrada|Contagem|Total|Mensagens de entrada para o Microsoft. EventHub.|entityName|
|OutgoingMessages|Mensagens de saída|Contagem|Total|Mensagens de saída para o Microsoft. EventHub.|entityName|
|IncomingBytes|Bytes de entrada.|Bytes|Total|Bytes de entrada para o Microsoft. EventHub.|entityName|
|OutgoingBytes|Bytes de saída.|Bytes|Total|Bytes de saída para o Microsoft. EventHub.|entityName|
|ActiveConnections|ActiveConnections|Contagem|Média|Total de conexões ativas para o Microsoft. EventHub.|Sem dimensões|
|ConnectionsOpened|Conexões abertas.|Contagem|Média|Conexões abertas para Microsoft. EventHub.|entityName|
|ConnectionsClosed|Conexões fechadas.|Contagem|Média|Conexões fechadas para o Microsoft. EventHub.|entityName|
|CaptureBacklog|Capturar registro posterior.|Contagem|Total|Capturar pendências para Microsoft. EventHub.|entityName|
|CapturedMessages|Mensagens capturadas.|Contagem|Total|Mensagens capturadas para o Microsoft. EventHub.|entityName|
|CapturedBytes|Bytes capturados.|Bytes|Total|Bytes capturados para o Microsoft. EventHub.|entityName|
|Tamanho|Tamanho|Bytes|Média|Tamanho de um EventHub em bytes.|entityName|
|INREQS|Solicitações de entrada (preteridas)|Contagem|Total|Total de solicitações de envio de entrada para um namespace (preterido)|Sem dimensões|
|SUCCREQ|Solicitações com êxito (preteridas)|Contagem|Total|Total de solicitações bem-sucedidas para um namespace (preterido)|Sem dimensões|
|FAILREQ|Solicitações com falha (preteridas)|Contagem|Total|Total de solicitações com falha para um namespace (preterido)|Sem dimensões|
|SVRBSY|Erros do servidor ocupado (preterido)|Contagem|Total|Total de erros do servidor ocupado para um namespace (preterido)|Sem dimensões|
|INTERR|Erros de servidor interno (preterido)|Contagem|Total|Total de erros internos do servidor para um namespace (preterido)|Sem dimensões|
|MISCERR|Outros erros (preterido)|Contagem|Total|Total de solicitações com falha para um namespace (preterido)|Sem dimensões|
|Inmsgs|Mensagens de entrada (preteridas) (preteridas)|Contagem|Total|Total de mensagens de entrada para um namespace. Essa métrica foi preterida. Use a métrica de mensagens de entrada em vez disso (preterido)|Sem dimensões|
|EHINMSGS|Mensagens de entrada (preteridas)|Contagem|Total|Total de mensagens de entrada para um namespace (preterido)|Sem dimensões|
|Mensagens inenviadas|Mensagens de saída (preteridas) (preteridas)|Contagem|Total|Total de mensagens de saída para um namespace. Essa métrica foi preterida. Use a métrica de mensagens de saída em vez disso (preterido)|Sem dimensões|
|EHOUTMSGS|Mensagens de saída (preteridas)|Contagem|Total|Total de mensagens de saída para um namespace (preterido)|Sem dimensões|
|EHINMBS|Bytes de entrada (preterido) (preterido)|Bytes|Total|Taxa de transferência de mensagem de entrada do hub de eventos para um namespace. Essa métrica foi preterida. Use a métrica de bytes de entrada em vez disso (preterido)|Sem dimensões|
|EHINBYTES|Bytes de entrada (preterido)|Bytes|Total|Taxa de transferência de mensagem de entrada do hub de eventos para um namespace (preterido)|Sem dimensões|
|EHOUTMBS|Bytes de saída (preteridos) (preterido)|Bytes|Total|Taxa de transferência de mensagem de saída do hub de eventos para um namespace. Essa métrica foi preterida. Use a métrica de bytes de saída em vez disso (preterido)|Sem dimensões|
|EHOUTBYTES|Bytes de saída (preteridos)|Bytes|Total|Taxa de transferência de mensagem de saída do hub de eventos para um namespace (preterido)|Sem dimensões|
|EHABL|Arquivar mensagens de pendências (preterido)|Contagem|Total|Mensagens de arquivamento do hub de eventos no backlog para um namespace (preterido)|Sem dimensões|
|EHAMSGS|Arquivar mensagens (preterido)|Contagem|Total|Mensagens arquivadas do hub de eventos em um namespace (preterido)|Sem dimensões|
|EHAMBS|Taxa de transferência de mensagem de arquivamento (preterido)|Bytes|Total|Taxa de transferência de mensagem arquivada do hub de eventos em um namespace (preterido)|Sem dimensões|

## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/clusters

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfulRequests|Solicitações com êxito (versão prévia)|Contagem|Total|Solicitações bem-sucedidas para o Microsoft. EventHub. (Pré-visualização)|Sem dimensões|
|ServerErrors|Erros do servidor. (Pré-visualização)|Contagem|Total|Erros de servidor para Microsoft. EventHub. (Pré-visualização)|Sem dimensões|
|UserErrors|Erros do usuário. (Pré-visualização)|Contagem|Total|Erros de usuário para Microsoft. EventHub. (Pré-visualização)|Sem dimensões|
|QuotaExceededErrors|A cota excedeu erros. (Pré-visualização)|Contagem|Total|A cota excedeu erros para o Microsoft. EventHub. (Pré-visualização)|Sem dimensões|
|ThrottledRequests|Solicitações limitadas. (Pré-visualização)|Contagem|Total|Solicitações limitadas para Microsoft. EventHub. (Pré-visualização)|Sem dimensões|
|IncomingRequests|Solicitações de entrada (versão prévia)|Contagem|Total|Solicitações de entrada para o Microsoft. EventHub. (Pré-visualização)|Sem dimensões|
|IncomingMessages|Mensagens de entrada (versão prévia)|Contagem|Total|Mensagens de entrada para o Microsoft. EventHub. (Pré-visualização)|Sem dimensões|
|OutgoingMessages|Mensagens de saída (versão prévia)|Contagem|Total|Mensagens de saída para o Microsoft. EventHub. (Pré-visualização)|Sem dimensões|
|IncomingBytes|Bytes de entrada. (Pré-visualização)|Bytes|Total|Bytes de entrada para o Microsoft. EventHub. (Pré-visualização)|Sem dimensões|
|OutgoingBytes|Bytes de saída. (Pré-visualização)|Bytes|Total|Bytes de saída para o Microsoft. EventHub. (Pré-visualização)|Sem dimensões|
|ActiveConnections|ActiveConnections (visualização)|Contagem|Média|Total de conexões ativas para o Microsoft. EventHub. (Pré-visualização)|Sem dimensões|
|ConnectionsOpened|Conexões abertas. (Pré-visualização)|Contagem|Média|Conexões abertas para Microsoft. EventHub. (Pré-visualização)|Sem dimensões|
|ConnectionsClosed|Conexões fechadas. (Pré-visualização)|Contagem|Média|Conexões fechadas para o Microsoft. EventHub. (Pré-visualização)|Sem dimensões|
|CaptureBacklog|Capturar registro posterior. (Pré-visualização)|Contagem|Total|Capturar pendências para Microsoft. EventHub. (Pré-visualização)|Sem dimensões|
|CapturedMessages|Mensagens capturadas. (Pré-visualização)|Contagem|Total|Mensagens capturadas para o Microsoft. EventHub. (Pré-visualização)|Sem dimensões|
|CapturedBytes|Bytes capturados. (Pré-visualização)|Bytes|Total|Bytes capturados para o Microsoft. EventHub. (Pré-visualização)|Sem dimensões|
|CPU|CPU (versão prévia)|Percentagem|Máximo|Utilização da CPU para o cluster do hub de eventos como uma porcentagem|Função|
|AvailableMemory|Memória disponível (versão prévia)|Contagem|Máximo|Memória disponível para o cluster do hub de eventos em bytes|Função|

## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/clusters

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|GatewayRequests|Solicitações de gateway|Contagem|Total|Número de solicitações de gateway|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Solicitações de gateway categorizadas|Contagem|Total|Número de solicitações de gateway por categorias (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Número de trabalhadores ativos|Contagem|Máximo|Número de trabalhadores ativos|ClusterDnsName, Metricname|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. insights/AutoscaleSettings

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ObservedMetricValue|Valor de métrica observado|Contagem|Média|O valor calculado por autoescala quando executado|MetricTriggerSource|
|MetricThreshold|Limite de métrica|Contagem|Média|O limite de dimensionamento automático configurado quando o dimensionamento automático foi executado.|MetricTriggerRule|
|ObservedCapacity|Capacidade observada|Contagem|Média|A capacidade relatada para dimensionamento automático quando executada.|Sem dimensões|
|ScaleActionsInitiated|Ações de escala iniciadas|Contagem|Total|A direção da operação de dimensionamento.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft. insights/Components

(Visualização pública)

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Disponibilidade|Percentagem|Média|Percentual de testes de disponibilidade concluídos com êxito|availabilityResult/nome, availabilityResult/local|
|availabilityResults/contagem|Testes de disponibilidade|Contagem|Contagem|Contagem de testes de disponibilidade|availabilityResult/nome, availabilityResult/local, availabilityResult/êxito|
|availabilityResults/duração|Duração do teste de disponibilidade|Milissegundos|Média|Duração do teste de disponibilidade|availabilityResult/nome, availabilityResult/local, availabilityResult/êxito|
|browserTimings/networkDuration|Tempo de conexão de rede de carregamento de página|Milissegundos|Média|Tempo entre a solicitação de usuário e a conexão de rede. Inclui a pesquisa de DNS e a conexão de transporte.|Sem dimensões|
|browserTimings/processingDuration|Tempo de processamento do cliente|Milissegundos|Média|Tempo entre o recebimento do último byte de um documento até que o DOM seja carregado. As solicitações assíncronas ainda podem estar sendo processadas.|Sem dimensões|
|browserTimings/receiveDuration|Tempo de resposta de recebimento|Milissegundos|Média|Tempo entre o primeiro e o último byte, ou até a desconexão.|Sem dimensões|
|browserTimings/sendDuration|Enviar tempo de solicitação|Milissegundos|Média|Tempo entre a conexão de rede e o recebimento do primeiro byte.|Sem dimensões|
|browserTimings/totalDuration|Tempo de carregamento da página do navegador|Milissegundos|Média|Tempo desde a solicitação do usuário até que DOM, folhas de estilo, scripts e imagens sejam carregados.|Sem dimensões|
|dependências/contagem|Chamadas de dependência|Contagem|Contagem|Contagem de chamadas feitas pelo aplicativo para recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/êxito, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|dependências/duração|Duração da dependência|Milissegundos|Média|Duração das chamadas feitas pelo aplicativo para recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/êxito, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|dependências/com falha|Falhas de chamada de dependência|Contagem|Contagem|Contagem de chamadas de dependência com falha feitas pelo aplicativo para recursos externos.|dependência/tipo, dependência/performanceBucket, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|pageViews/contagem|Exibições de página|Contagem|Contagem|Contagem de exibições de página.|operação/sintética|
|pageViews/duração|Tempo de carregamento da exibição de página|Milissegundos|Média|Tempo de carregamento da exibição de página|operação/sintética|
|performanceCounters/requestExecutionTime|Tempo de execução da solicitação HTTP|Milissegundos|Média|Tempo de execução da solicitação mais recente.|nuvem/roleInstance|
|performanceCounters/requestsInQueue|Solicitações HTTP na fila do aplicativo|Contagem|Média|Comprimento da fila de solicitações do aplicativo.|nuvem/roleInstance|
|performanceCounters/requestsPerSecond|Taxa de solicitação HTTP|CountPerSecond|Média|Taxa de todas as solicitações para o aplicativo por segundo de ASP.NET.|nuvem/roleInstance|
|performanceCounters/exceptionsPerSecond|Taxa de exceção|CountPerSecond|Média|Contagem de exceções manipuladas e não tratadas relatadas ao Windows, incluindo exceções .NET e exceções não gerenciadas que são convertidas em exceções .NET.|nuvem/roleInstance|
|performanceCounters/processIOBytesPerSecond|Taxa de e/s de processo|BytesPerSecond|Média|Total de bytes por segundo lidos e gravados em arquivos, rede e dispositivos.|nuvem/roleInstance|
|performanceCounters/processCpuPercentage|CPU do processo|Percentagem|Média|A porcentagem de tempo decorrido em que todos os threads de processo usaram o processador para executar instruções. Isso pode variar entre 0 e 100. Essa métrica indica o desempenho apenas do processo W3wp.|nuvem/roleInstance|
|performanceCounters/processorCpuPercentage|Tempo do processador|Percentagem|Média|A porcentagem de tempo que o processador gasta em threads não ociosos.|nuvem/roleInstance|
|performanceCounters/memoryAvailableBytes|Memória disponível|Bytes|Média|Memória física disponível imediatamente para alocação para um processo ou para uso do sistema.|nuvem/roleInstance|
|performanceCounters/processPrivateBytes|Processar bytes particulares|Bytes|Média|Memória atribuída exclusivamente aos processos do aplicativo monitorado.|nuvem/roleInstance|
|solicitações/duração|Tempo de resposta do servidor|Milissegundos|Média|Tempo entre o recebimento de uma solicitação HTTP e a conclusão do envio da resposta.|solicitação/performanceBucket, solicitação/resultCode, operação/sintética, nuvem/roleInstance, solicitação/êxito, nuvem/roleName|
|solicitações/contagem|Solicitações do servidor|Contagem|Contagem|Contagem de solicitações HTTP concluída.|solicitação/performanceBucket, solicitação/resultCode, operação/sintética, nuvem/roleInstance, solicitação/êxito, nuvem/roleName|
|solicitações/com falha|Pedidos falhados|Contagem|Contagem|Contagem de solicitações HTTP marcadas como com falha. Na maioria dos casos, essas são solicitações com um código de resposta > = 400 e não iguais a 401.|solicitação/performanceBucket, solicitação/resultCode, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|solicitações/taxa|Taxa de solicitações do servidor|CountPerSecond|Média|Taxa de solicitações de servidor por segundo|solicitação/performanceBucket, solicitação/resultCode, operação/sintética, nuvem/roleInstance, solicitação/êxito, nuvem/roleName|
|exceções/contagem|Exceções|Contagem|Contagem|Contagem combinada de todas as exceções não capturadas.|Cloud/roleName, nuvem/roleInstance, cliente/tipo|
|exceções/navegador|Exceções de browser|Contagem|Contagem|Contagem de exceções não capturadas lançadas no navegador.|Sem dimensões|
|exceções/servidor|Exceções de servidor|Contagem|Contagem|Contagem de exceções não capturadas lançadas no aplicativo de servidor.|Cloud/roleName, nuvem/roleInstance|
|rastreamentos/contagem|Rastreamentos|Contagem|Contagem|Contagem de documentos de rastreamento|Trace/nível, operação/sintético, nuvem/roleName, nuvem/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft. keyvault/cofres

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServiceApiHit|Total de acertos da API de serviço|Contagem|Contagem|Número total de acertos da API de serviço|ActivityType, ActivityName|
|ServiceApiLatency|Latência geral da API de serviço|Milissegundos|Média|Latência geral de solicitações de API de serviço|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Total de resultados da API de serviço|Contagem|Contagem|Número total de resultados da API de serviço|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/clusters

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CacheUtilization|Utilização de cache|Percentagem|Média|Nível de utilização no escopo do cluster|Nenhuma|
|QueryDuration|Duração da consulta|Milissegundos|Média|Duração das consultas em segundos|Status da consulta|
|IngestionUtilization|Utilização de ingestão|Percentagem|Média|Taxa de Slots de ingestão usados no cluster|Nenhuma|
|KeepAlive|Keep Alive|Contagem|Média|Verificação de sanidade indica que o cluster responde às consultas|Nenhuma|
|IngestionVolumeInMB|Volume de ingestão (em MB)|Contagem|Total|Volume geral de dados ingeridos para o cluster (em MB)|Base de Dados|
|IngestionLatencyInSeconds|Latência de ingestão (em segundos)|Segundos|Média|O tempo de ingestão da origem (por exemplo, a mensagem está no EventHub) para o cluster em segundos|Nenhuma|
|EventProcessedForEventHubs|Eventos processados (para hubs de eventos)|Contagem|Total|Número de eventos processados pelo cluster ao ingerir o Hub de eventos|Nenhuma|
|IngestionResult|Resultado da ingestão|Contagem|Contagem|Número de operações de ingestão|Estado|
|CPU|CPU|Percentagem|Média|Nível de utilização da CPU|Nenhuma|
| ContinuousExportNumOfRecordsExported | Número de registros exportados na exportação contínua | Contagem | Total | Número de registros exportados para cada artefato de armazenamento gravado durante a operação de exportação  | Nenhuma |
| ExportUtilization | Utilização da exportação | Percentagem | Máximo | Utilização da exportação | Nenhuma |
| ContinuousExportPendingCount | Contagem de exportação contínua pendente | Contagem | Máximo | O número de trabalhos de exportação contínuos pendentes prontos para execução | Nenhuma |
| ContinuousExportMaxLatenessMinutes | Minutos de atraso máximo de exportação contínua | Contagem | Máximo | O tempo máximo em minutos de todas as exportações contínuas que estão pendentes e prontas para execução | Nenhuma |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft. LocationBasedServices/accounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Utilização|Utilização|Contagem|Contagem|Contagem de chamadas de API|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/workflows

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RunsStarted|Execuções iniciadas|Contagem|Total|Número de execuções de fluxo de trabalho iniciadas.|Sem dimensões|
|RunsCompleted|Execuções concluídas|Contagem|Total|Número de execuções de fluxo de trabalho concluídas.|Sem dimensões|
|RunsSucceeded|Execuções com êxito|Contagem|Total|Número de execuções de fluxo de trabalho bem-sucedidas.|Sem dimensões|
|RunsFailed|Execuções com falha|Contagem|Total|Número de execuções de fluxo de trabalho com falha.|Sem dimensões|
|RunsCancelled|Execuções canceladas|Contagem|Total|Número de execuções de fluxo de trabalho canceladas.|Sem dimensões|
|RunLatency|Latência de execução|Segundos|Média|Latência de execuções de fluxo de trabalho concluídas.|Sem dimensões|
|RunSuccessLatency|Latência de execução bem-sucedida|Segundos|Média|Latência de execuções de fluxo de trabalho bem-sucedidas.|Sem dimensões|
|RunThrottledEvents|Executar eventos limitados|Contagem|Total|Número de ações de fluxo de trabalho ou eventos restringidos de gatilho.|Sem dimensões|
|RunFailurePercentage|Percentual de falha de execução|Percentagem|Total|Falha na porcentagem de execuções de fluxo de trabalho.|Sem dimensões|
|ActionsStarted|Ações iniciadas |Contagem|Total|Número de ações de fluxo de trabalho iniciadas.|Sem dimensões|
|ActionsCompleted|Ações concluídas |Contagem|Total|Número de ações de fluxo de trabalho concluídas.|Sem dimensões|
|ActionsSucceeded|Ações com êxito |Contagem|Total|Número de ações de fluxo de trabalho bem-sucedidas.|Sem dimensões|
|ActionsFailed|Ações com falha|Contagem|Total|Número de ações de fluxo de trabalho com falha.|Sem dimensões|
|ActionsSkipped|Ações ignoradas |Contagem|Total|Número de ações de fluxo de trabalho ignoradas.|Sem dimensões|
|ActionLatency|Latência da ação |Segundos|Média|Latência de ações de fluxo de trabalho concluídas.|Sem dimensões|
|ActionSuccessLatency|Latência de êxito da ação |Segundos|Média|Latência de ações de fluxo de trabalho bem-sucedidas.|Sem dimensões|
|ActionThrottledEvents|Eventos com restrição de ação|Contagem|Total|Número de eventos restritos da ação de fluxo de trabalho.|Sem dimensões|
|TriggersStarted|Gatilhos iniciados |Contagem|Total|Número de gatilhos de fluxo de trabalho iniciados.|Sem dimensões|
|TriggersCompleted|Gatilhos concluídos |Contagem|Total|Número de gatilhos de fluxo de trabalho concluídos.|Sem dimensões|
|TriggersSucceeded|Gatilhos com êxito |Contagem|Total|Número de gatilhos de fluxo de trabalho bem-sucedidos.|Sem dimensões|
|TriggersFailed|Gatilhos com falha |Contagem|Total|Número de gatilhos de fluxo de trabalho com falha.|Sem dimensões|
|TriggersSkipped|Gatilhos ignorados|Contagem|Total|Número de gatilhos de fluxo de trabalho ignorados.|Sem dimensões|
|TriggersFired|Gatilhos acionados |Contagem|Total|Número de gatilhos de fluxo de trabalho acionados.|Sem dimensões|
|TriggerLatency|Latência de gatilho |Segundos|Média|Latência de gatilhos de fluxo de trabalho concluídos.|Sem dimensões|
|TriggerFireLatency|Latência de disparo de gatilho |Segundos|Média|Latência de gatilhos de fluxo de trabalho acionados.|Sem dimensões|
|TriggerSuccessLatency|Latência de êxito do gatilho |Segundos|Média|Latência de gatilhos de fluxo de trabalho bem-sucedidos.|Sem dimensões|
|TriggerThrottledEvents|Disparar eventos restringidos|Contagem|Total|Número de eventos restringidos do gatilho de fluxo de trabalho.|Sem dimensões|
|BillableActionExecutions|Execuções de ações faturáveis|Contagem|Total|Número de execuções de ação de fluxo de trabalho sendo faturadas.|Sem dimensões|
|BillableTriggerExecutions|Execuções de gatilho Faturável|Contagem|Total|Número de execuções de gatilho de fluxo de trabalho sendo cobradas.|Sem dimensões|
|TotalBillableExecutions|Total de execuções faturáveis|Contagem|Total|Número de execuções de fluxo de trabalho sendo cobradas.|Sem dimensões|
|BillingUsageNativeOperation|Uso de cobrança para execuções de operação nativa|Contagem|Total|Número de execuções de operações nativas sendo cobradas.|Sem dimensões|
|BillingUsageStandardConnector|Uso de cobrança para execuções de conector padrão|Contagem|Total|Número de execuções de conector padrão sendo cobradas.|Sem dimensões|
|BillingUsageStorageConsumption|Uso de cobrança para execuções de consumo de armazenamento|Contagem|Total|Número de execuções de consumo de armazenamento sendo cobradas.|Sem dimensões|
|BillingUsageNativeOperation|Uso de cobrança para execuções de operação nativa|Contagem|Total|Número de execuções de operações nativas sendo cobradas.|Sem dimensões|
|BillingUsageStandardConnector|Uso de cobrança para execuções de conector padrão|Contagem|Total|Número de execuções de conector padrão sendo cobradas.|Sem dimensões|
|BillingUsageStorageConsumption|Uso de cobrança para execuções de consumo de armazenamento|Contagem|Total|Número de execuções de consumo de armazenamento sendo cobradas.|Sem dimensões|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/integrationServiceEnvironments

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RunsStarted|Execuções iniciadas|Contagem|Total|Número de execuções de fluxo de trabalho iniciadas.|Sem dimensões|
|RunsCompleted|Execuções concluídas|Contagem|Total|Número de execuções de fluxo de trabalho concluídas.|Sem dimensões|
|RunsSucceeded|Execuções com êxito|Contagem|Total|Número de execuções de fluxo de trabalho bem-sucedidas.|Sem dimensões|
|RunsFailed|Execuções com falha|Contagem|Total|Número de execuções de fluxo de trabalho com falha.|Sem dimensões|
|RunsCancelled|Execuções canceladas|Contagem|Total|Número de execuções de fluxo de trabalho canceladas.|Sem dimensões|
|RunLatency|Latência de execução|Segundos|Média|Latência de execuções de fluxo de trabalho concluídas.|Sem dimensões|
|RunSuccessLatency|Latência de execução bem-sucedida|Segundos|Média|Latência de execuções de fluxo de trabalho bem-sucedidas.|Sem dimensões|
|RunThrottledEvents|Executar eventos limitados|Contagem|Total|Número de ações de fluxo de trabalho ou eventos restringidos de gatilho.|Sem dimensões|
|RunStartThrottledEvents|Executar eventos restritos de início|Contagem|Total|Número de eventos restritos de início de fluxo de trabalho.|Sem dimensões|
|RunFailurePercentage|Percentual de falha de execução|Percentagem|Total|Falha na porcentagem de execuções de fluxo de trabalho.|Sem dimensões|
|ActionsStarted|Ações iniciadas |Contagem|Total|Número de ações de fluxo de trabalho iniciadas.|Sem dimensões|
|ActionsCompleted|Ações concluídas |Contagem|Total|Número de ações de fluxo de trabalho concluídas.|Sem dimensões|
|ActionsSucceeded|Ações com êxito |Contagem|Total|Número de ações de fluxo de trabalho bem-sucedidas.|Sem dimensões|
|ActionsFailed|Ações com falha |Contagem|Total|Número de ações de fluxo de trabalho com falha.|Sem dimensões|
|ActionsSkipped|Ações ignoradas |Contagem|Total|Número de ações de fluxo de trabalho ignoradas.|Sem dimensões|
|ActionLatency|Latência da ação |Segundos|Média|Latência de ações de fluxo de trabalho concluídas.|Sem dimensões|
|ActionSuccessLatency|Latência de êxito da ação |Segundos|Média|Latência de ações de fluxo de trabalho bem-sucedidas.|Sem dimensões|
|ActionThrottledEvents|Eventos com restrição de ação|Contagem|Total|Número de eventos restritos da ação de fluxo de trabalho.|Sem dimensões|
|TriggersStarted|Gatilhos iniciados |Contagem|Total|Número de gatilhos de fluxo de trabalho iniciados.|Sem dimensões|
|TriggersCompleted|Gatilhos concluídos |Contagem|Total|Número de gatilhos de fluxo de trabalho concluídos.|Sem dimensões|
|TriggersSucceeded|Gatilhos com êxito |Contagem|Total|Número de gatilhos de fluxo de trabalho bem-sucedidos.|Sem dimensões|
|TriggersFailed|Gatilhos com falha |Contagem|Total|Número de gatilhos de fluxo de trabalho com falha.|Sem dimensões|
|TriggersSkipped|Gatilhos ignorados|Contagem|Total|Número de gatilhos de fluxo de trabalho ignorados.|Sem dimensões|
|TriggersFired|Gatilhos acionados |Contagem|Total|Número de gatilhos de fluxo de trabalho acionados.|Sem dimensões|
|TriggerLatency|Latência de gatilho |Segundos|Média|Latência de gatilhos de fluxo de trabalho concluídos.|Sem dimensões|
|TriggerFireLatency|Latência de disparo de gatilho |Segundos|Média|Latência de gatilhos de fluxo de trabalho acionados.|Sem dimensões|
|TriggerSuccessLatency|Latência de êxito do gatilho |Segundos|Média|Latência de gatilhos de fluxo de trabalho bem-sucedidos.|Sem dimensões|
|TriggerThrottledEvents|Disparar eventos restringidos|Contagem|Total|Número de eventos restringidos do gatilho de fluxo de trabalho.|Sem dimensões|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Uso do processador de fluxo de trabalho para Ambiente de Serviço de Integração|Percentagem|Média|Uso do processador do fluxo de trabalho para o ambiente do serviço de integração.|Sem dimensões|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Uso de memória do fluxo de trabalho para Ambiente de Serviço de Integração|Percentagem|Média|Uso de memória do fluxo de trabalho para o ambiente do serviço de integração.|Sem dimensões|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Uso do processador do conector para Ambiente de Serviço de Integração|Percentagem|Média|Uso do processador do conector para o ambiente do serviço de integração.|Sem dimensões|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Uso de memória do conector para Ambiente de Serviço de Integração|Percentagem|Média|Uso de memória do conector para o ambiente do serviço de integração.|Sem dimensões|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/Workspaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Execuções concluídas|Execuções concluídas|Contagem|Total|Número de execuções concluídas com êxito para este espaço de trabalho|Cenário|
|Execuções iniciadas|Execuções iniciadas|Contagem|Total|Número de execuções iniciadas para este espaço de trabalho|Cenário|
|Execuções com falha|Execuções com falha|Contagem|Total|Número de execuções com falha para este espaço de trabalho|Cenário|

## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/contas

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Utilização|Utilização|Contagem|Contagem|Contagem de chamadas de API|ApiCategory, ApiName, ResultType, ResponseCode|
|Disponibilidade|Disponibilidade|Percentagem|Média|Disponibilidade das APIs|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/volumes

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AverageOtherLatency|Latência média de outras|MS/op|Média|Média de outra latência (que não é leitura ou gravação) em milissegundos por operação|Sem dimensões|
|AverageReadLatency|Latência média de leitura|MS/op|Média|Média de latência de leitura em milissegundos por operação|Sem dimensões|
|AverageTotalLatency|Latência total média|MS/op|Média|Latência total média em milissegundos por operação|Sem dimensões|
|AverageWriteLatency|Latência média de gravação|MS/op|Média|Latência média de gravação em milissegundos por operação|Sem dimensões|
|FilesystemOtherOps|Outras operações do sistema de arquivos|Ops|Média|Número de outras operações do sistema de arquivos (que não são de leitura ou gravação)|Sem dimensões|
|FilesystemReadOps|Operações de leitura do sistema de arquivos|Ops|Média|Número de operações de leitura do sistema de arquivos|Sem dimensões|
|FilesystemTotalOps|Total de operações do sistema de arquivos|Ops|Média|Soma de todas as operações do sistema de arquivos|Sem dimensões|
|FilesystemWriteOps|Operações de gravação do sistema de arquivos|Ops|Média|Número de operações de gravação do sistema de arquivos|Sem dimensões|
|IoBytesPerOtherOps|Bytes de e/s por outra Ops|bytes/op|Média|Número de bytes de entrada/saída por outras operações (que não são de leitura ou gravação)|Sem dimensões|
|IoBytesPerReadOps|Bytes de e/s por operações de leitura|bytes/op|Média|Número de bytes de entrada/saída por operação de leitura|Sem dimensões|
|IoBytesPerTotalOps|Bytes de e/s por operação em todas as operações|bytes/op|Média|Soma de todas as operações de bytes in/out|Sem dimensões|
|IoBytesPerWriteOps|Bytes de e/s por operações de gravação|bytes/op|Média|Número de bytes de entrada/saída por operação de gravação|Sem dimensões|
|OtherIops|Outro IOPS|operações/segundo|Média|Outra operação de entrada/saída por segundo|Sem dimensões|
|OtherThroughput|Outra taxa de transferência|MBps|Média|Outra taxa de transferência (que não é de leitura ou gravação) em megabytes por segundo|Sem dimensões|
|ReadIops|IOPS de leitura|operações/segundo|Média|Operações de leitura/saída por segundo|Sem dimensões|
|ReadThroughput|Taxa de transferência de leitura|MBps|Média|Taxa de transferência de leitura em megabytes por segundo|Sem dimensões|
|TotalIops|IOPS total|operações/segundo|Média|Soma de todas as operações de entrada/saída por segundo|Sem dimensões|
|TotalThroughput|Taxa de transferência total|MBps|Média|Soma de toda a taxa de transferência em megabytes por segundo|Sem dimensões|
|VolumeAllocatedSize|Tamanho alocado do volume|Bytes|Média|Tamanho alocado do volume (não os bytes reais usados)|Sem dimensões|
|VolumeLogicalSize|Tamanho lógico do volume|Bytes|Média|Tamanho lógico do volume (bytes usados)|Sem dimensões|
|VolumeSnapshotSize|Tamanho do instantâneo de volume|Bytes|Média|Tamanho de todos os instantâneos no volume|Sem dimensões|
|WriteIops|IOPS de gravação|operações/segundo|Média|Operações de gravação/saída por segundo|Sem dimensões|
|WriteThroughput|Taxa de transferência de gravação|MBps|Média|Taxa de transferência de gravação em megabytes por segundo|Sem dimensões|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft. NetApp/netAppAccounts/capacityPools

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Tamanho alocado do pool de volumes|Bytes|Média|Tamanho alocado do pool (não os bytes reais usados)|Sem dimensões|
|VolumePoolAllocatedUsed|Pool de volumes alocado usado|Bytes|Média|Tamanho usado para o pool alocado|Sem dimensões|
|VolumePoolTotalLogicalSize|Tamanho lógico total do pool de volumes|Bytes|Média|Soma do tamanho lógico de todos os volumes pertencentes ao pool|Sem dimensões|
|VolumePoolTotalSnapshotSize|Tamanho do instantâneo total do pool de volumes|Bytes|Média|Soma de todos os instantâneos no pool|Sem dimensões|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft. Network/networkInterfaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BytesSentRate|Bytes enviados|Contagem|Total|Número de bytes enviados pela interface de rede|Sem dimensões|
|BytesReceivedRate|Bytes recebidos|Contagem|Total|Número de bytes recebidos pela interface de rede|Sem dimensões|
|PacketsSentRate|Pacotes enviados|Contagem|Total|Número de pacotes enviados pela interface de rede|Sem dimensões|
|PacketsReceivedRate|Pacotes recebidos|Contagem|Total|Número de pacotes recebidos pela interface de rede|Sem dimensões|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|VipAvailability|Disponibilidade do caminho de dados|Contagem|Média|Média de disponibilidade de caminho de dados de Load Balancer por duração de tempo|FrontendIPAddress, FrontendPort|
|DipAvailability|Status da investigação de integridade|Contagem|Média|Média Load Balancer status de investigação de integridade por duração de tempo|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Contagem de bytes|Contagem|Total|Número total de bytes transmitidos no período de tempo|FrontendIPAddress, FrontendPort, direção|
|PacketCount|Contagem de pacotes|Contagem|Total|Número total de pacotes transmitidos no período de tempo|FrontendIPAddress, FrontendPort, direção|
|SYNCount|Contagem de SYN|Contagem|Total|Número total de pacotes SYN transmitidos no período de tempo|FrontendIPAddress, FrontendPort, direção|
|SnatConnectionCount|Contagem de conexões SNAT|Contagem|Total|Número total de novas conexões SNAT criadas dentro do período de tempo|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Portas SNAT alocadas (visualização)|Contagem|Total|Número total de portas SNAT alocadas no período de tempo|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Portas SNAT usadas (visualização)|Contagem|Total|Número total de portas SNAT usadas no período de tempo|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft. Network/dnszones

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueryVolume|Volume de consulta|Contagem|Total|Número de consultas servidas para uma zona DNS|Sem dimensões|
|RecordSetCount|Contagem de conjuntos de registros|Contagem|Máximo|Número de conjuntos de registros em uma zona DNS|Sem dimensões|
|RecordSetCapacityUtilization|Utilização da capacidade do conjunto de registros|Percentagem|Máximo|Porcentagem da capacidade do conjunto de registros utilizada por uma zona DNS|Sem dimensões|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PacketsInDDoS|DDoS de pacotes de entrada|CountPerSecond|Máximo|DDoS de pacotes de entrada|Sem dimensões|
|PacketsDroppedDDoS|DDoS de pacotes de entrada removidos|CountPerSecond|Máximo|DDoS de pacotes de entrada removidos|Sem dimensões|
|PacketsForwardedDDoS|DDoS de pacotes de entrada encaminhados|CountPerSecond|Máximo|DDoS de pacotes de entrada encaminhados|Sem dimensões|
|TCPPacketsInDDoS|DDoS de pacotes TCP de entrada|CountPerSecond|Máximo|DDoS de pacotes TCP de entrada|Sem dimensões|
|TCPPacketsDroppedDDoS|DDoS de pacotes TCP de entrada removidos|CountPerSecond|Máximo|DDoS de pacotes TCP de entrada removidos|Sem dimensões|
|TCPPacketsForwardedDDoS|DDoS de pacotes TCP de entrada encaminhados|CountPerSecond|Máximo|DDoS de pacotes TCP de entrada encaminhados|Sem dimensões|
|UDPPacketsInDDoS|DDoS de pacotes UDP de entrada|CountPerSecond|Máximo|DDoS de pacotes UDP de entrada|Sem dimensões|
|UDPPacketsDroppedDDoS|DDoS de pacotes UDP de entrada removidos|CountPerSecond|Máximo|DDoS de pacotes UDP de entrada removidos|Sem dimensões|
|UDPPacketsForwardedDDoS|DDoS de pacotes UDP de entrada encaminhados|CountPerSecond|Máximo|DDoS de pacotes UDP de entrada encaminhados|Sem dimensões|
|BytesInDDoS|DDoS de bytes de entrada|BytesPerSecond|Máximo|DDoS de bytes de entrada|Sem dimensões|
|BytesDroppedDDoS|DDoS de bytes de entrada eliminados|BytesPerSecond|Máximo|DDoS de bytes de entrada eliminados|Sem dimensões|
|BytesForwardedDDoS|DDoS de bytes de entrada encaminhados|BytesPerSecond|Máximo|DDoS de bytes de entrada encaminhados|Sem dimensões|
|TCPBytesInDDoS|DDoS de bytes de TCP de entrada|BytesPerSecond|Máximo|DDoS de bytes de TCP de entrada|Sem dimensões|
|TCPBytesDroppedDDoS|DDoS de bytes de TCP de entrada eliminados|BytesPerSecond|Máximo|DDoS de bytes de TCP de entrada eliminados|Sem dimensões|
|TCPBytesForwardedDDoS|DDoS de bytes TCP de entrada encaminhados|BytesPerSecond|Máximo|DDoS de bytes TCP de entrada encaminhados|Sem dimensões|
|UDPBytesInDDoS|DDoS de bytes de UDP de entrada|BytesPerSecond|Máximo|DDoS de bytes de UDP de entrada|Sem dimensões|
|UDPBytesDroppedDDoS|DDoS de bytes UDP de entrada eliminados|BytesPerSecond|Máximo|DDoS de bytes UDP de entrada eliminados|Sem dimensões|
|UDPBytesForwardedDDoS|DDoS de bytes de UDP de entrada encaminhados|BytesPerSecond|Máximo|DDoS de bytes de UDP de entrada encaminhados|Sem dimensões|
|IfUnderDDoSAttack|Sob ataque de DDoS ou não|Contagem|Máximo|Sob ataque de DDoS ou não|Sem dimensões|
|DDoSTriggerTCPPackets|Pacotes TCP de entrada para disparar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes TCP de entrada para disparar a mitigação de DDoS|Sem dimensões|
|DDoSTriggerUDPPackets|Pacotes UDP de entrada para disparar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes UDP de entrada para disparar a mitigação de DDoS|Sem dimensões|
|DDoSTriggerSYNPackets|Pacotes de entrada SYN para disparar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes de entrada SYN para disparar a mitigação de DDoS|Sem dimensões|
|VipAvailability|Disponibilidade do caminho de dados|Contagem|Média|Média de disponibilidade de endereço IP por duração de tempo|Porta|
|ByteCount|Contagem de bytes|Contagem|Total|Número total de bytes transmitidos no período de tempo|Porta, direção|
|PacketCount|Contagem de pacotes|Contagem|Total|Número total de pacotes transmitidos no período de tempo|Porta, direção|
|SynCount|Contagem de SYN|Contagem|Total|Número total de pacotes SYN transmitidos no período de tempo|Porta, direção|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ApplicationRuleHit|Contagem de impacto de regras de aplicativo|Contagem|Total|Número de vezes que as regras de aplicativo foram atingidas|Status, motivo, protocolo|
|NetworkRuleHit|Contagem de impacto das regras de rede|Contagem|Total|Número de vezes que as regras de rede foram atingidas|Status, motivo, protocolo|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Débito|Débito|BytesPerSecond|Total|Número de bytes por segundo atendidos pelo gateway de aplicativo|Sem dimensões|
|UnhealthyHostCount|Contagem de hosts não íntegros|Contagem|Média|Número de hosts de back-end não íntegros|BackendSettingsPool|
|healthyHostCount|Contagem de hosts íntegros|Contagem|Média|Número de hosts de back-end íntegros|BackendSettingsPool|
|TotalRequests|Total de Pedidos|Contagem|Total|Contagem de solicitações bem-sucedidas que o gateway de aplicativo serviu|BackendSettingsPool|
|FailedRequests|Pedidos com Falhas|Contagem|Total|Contagem de solicitações com falha que o gateway de aplicativo serviu|BackendSettingsPool|
|ResponseStatus|Status da resposta|Contagem|Total|Status de resposta http retornado pelo gateway de aplicativo|HttpStatusGroup|
|CurrentConnections|Conexões atuais|Contagem|Total|Contagem de conexões atuais estabelecidas com o gateway de aplicativo|Sem dimensões|
|CapacityUnits|Unidades de capacidade atuais|Contagem|Média|Unidades de capacidade consumidas|Sem dimensões|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AverageBandwidth|Largura de banda S2S do gateway|BytesPerSecond|Média|Largura de banda de site a site média de um gateway em bytes por segundo|Sem dimensões|
|P2SBandwidth|Largura de banda P2S do gateway|BytesPerSecond|Média|Largura de banda de ponto a site média de um gateway em bytes por segundo|Sem dimensões|
|P2SConnectionCount|Contagem de conexões P2S|Contagem|Máximo|Contagem de conexões ponto a site de um gateway|Protocolo|
|TunnelAverageBandwidth|Largura de banda do túnel|BytesPerSecond|Média|Largura de banda média de um túnel em bytes por segundo|ConnectionName, RemoteIP|
|TunnelEgressBytes|Bytes de saída do túnel|Bytes|Total|Bytes de saída de um túnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Bytes de entrada de túnel|Bytes|Total|Bytes de entrada de um túnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Pacotes de saída de túnel|Contagem|Total|Contagem de pacotes de saída de um túnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Pacotes de entrada de túnel|Contagem|Total|Contagem de pacotes de entrada de um túnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Descarte de pacotes incompatíveis TS de saída de túnel|Contagem|Total|Contagem de descarte de pacotes de saída da incompatibilidade de seletor de tráfego de um túnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Descarte de pacotes incompatíveis TS de entrada de túnel|Contagem|Total|Contagem de eliminação de pacotes de entrada da incompatibilidade de seletor de tráfego de um túnel|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits de entrada do Azure por segundo|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits de saída do Azure por segundo|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/emparelhamentos

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits de entrada do Azure por segundo|Sem dimensões|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits de saída do Azure por segundo|Sem dimensões|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits de entrada do Azure por segundo|Sem dimensões|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits de saída do Azure por segundo|Sem dimensões|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QpsByEndpoint|Consultas por ponto de extremidade retornadas|Contagem|Total|Número de vezes que um ponto de extremidade do Gerenciador de tráfego foi retornado no período de tempo fornecido|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Status do ponto de extremidade por ponto de extremidade|Contagem|Máximo|1 se o status de investigação de um ponto de extremidade for "habilitado", caso contrário, 0.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft. Network/networkWatchers/connectionMonitors

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ProbesFailedPercent|% De investigações com falha|Percentagem|Média|% de investigações de monitoramento de conectividade com falha|Sem dimensões|
|AverageRoundtripMs|Média de tempo de ida e volta (MS)|Milissegundos|Média|Tempo de ida e volta médio da rede (MS) para investigações de monitoramento de conectividade enviadas entre a origem e o destino|Sem dimensões|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|requestCount|Número de Pedidos|Contagem|Total|O número de solicitações de cliente atendidas pelo proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|Solicitações|Tamanho da solicitação|Bytes|Total|O número de bytes enviados como solicitações de clientes para o proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|Respostas|Tamanho da resposta|Bytes|Total|O número de bytes enviados como respostas do proxy HTTP/S para clientes|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Contagem de solicitações de back-end|Contagem|Total|O número de solicitações enviadas do proxy HTTP/S para back-ends|HttpStatus, HttpStatusGroup, back-end|
|BackendRequestLatency|Latência de solicitação de back-end|Milissegundos|Média|O tempo calculado a partir de quando a solicitação foi enviada pelo proxy HTTP/S para o back-end até que o proxy HTTP/S receba o último byte de resposta do back-end|End|
|TotalLatency|Latência total|Milissegundos|Média|O tempo calculado a partir de quando a solicitação do cliente foi recebida pelo proxy HTTP/S até que o cliente tenha reconhecido o último byte de resposta do proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Percentual de integridade de back-end|Percentagem|Média|A porcentagem de investigações de integridade bem-sucedidas do proxy HTTP/S para back-ends|Back-end, Httpsettings|
|WebApplicationFirewallRequestCount|Contagem de solicitações de firewall do aplicativo Web|Contagem|Total|O número de solicitações de cliente processadas pelo firewall do aplicativo Web|PolicyName, RuleName, ação|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. NotificationHubs/namespaces/NotificationHubs

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|registro. todos|Operações de registro|Contagem|Total|A contagem de todas as operações de registro bem-sucedidas (cria consultas e exclusões de atualizações). |Sem dimensões|
|registro. criar|Operações de criação de registro|Contagem|Total|A contagem de todas as criações de registro bem-sucedidas.|Sem dimensões|
|registro. atualização|Operações de atualização de registro|Contagem|Total|A contagem de todas as atualizações de registro bem-sucedidas.|Sem dimensões|
|registro. Get|Operações de leitura de registro|Contagem|Total|A contagem de todas as consultas de registro bem-sucedidas.|Sem dimensões|
|registro. excluir|Operações de exclusão de registro|Contagem|Total|A contagem de todas as exclusões de registro bem-sucedidas.|Sem dimensões|
|Recebidos|Mensagens de entrada|Contagem|Total|A contagem de todas as chamadas de API de envio bem-sucedidas. |Sem dimensões|
|de entrada. agendado|Notificações por push agendadas enviadas|Contagem|Total|Notificações por push agendadas canceladas|Sem dimensões|
|de entrada. agendado. cancelar|Notificações por push agendadas canceladas|Contagem|Total|Notificações por push agendadas canceladas|Sem dimensões|
|agendado. pendente|Notificações agendadas pendentes|Contagem|Total|Notificações agendadas pendentes|Sem dimensões|
|instalação. todos|Operações de gerenciamento de instalação|Contagem|Total|Operações de gerenciamento de instalação|Sem dimensões|
|instalação. Get|Obter operações de instalação|Contagem|Total|Obter operações de instalação|Sem dimensões|
|instalação. Upsert|Criar ou atualizar operações de instalação|Contagem|Total|Criar ou atualizar operações de instalação|Sem dimensões|
|instalação. patch|Operações de instalação de patch|Contagem|Total|Operações de instalação de patch|Sem dimensões|
|instalação. exclusão|Excluir operações de instalação|Contagem|Total|Excluir operações de instalação|Sem dimensões|
|saída. allpns. Success|Notificações bem-sucedidas|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Sem dimensões|
|saída. allpns. invalidpayload|Erros de carga|Contagem|Total|A contagem de Pushes que falharam porque o PNS retornou um erro de carga inadequada.|Sem dimensões|
|saída. allpns. pnserror|Erros do sistema de notificação externa|Contagem|Total|A contagem de Pushes que falharam porque houve um problema de comunicação com o PNS (exclui problemas de autenticação).|Sem dimensões|
|saída. allpns. channelerror|Erros de canal|Contagem|Total|A contagem de Pushes que falharam porque o canal era inválido não associado ao aplicativo correto limitado ou expirou.|Sem dimensões|
|saída. allpns. badorexpiredchannel|Erros de canal insatisfatórios ou expirados|Contagem|Total|A contagem de Pushes que falharam porque o canal/token/RegistrationId no registro expirou ou é inválido.|Sem dimensões|
|saída. WNS. Success|Notificações com êxito do WNS|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Sem dimensões|
|saída. WNS. invalidcredentials|Erros de autorização do WNS (credenciais inválidas)|Contagem|Total|A contagem de Pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas. (O Windows Live não reconhece as credenciais).|Sem dimensões|
|saída. WNS. badchannel|Erro de canal insatisfatório do WNS|Contagem|Total|A contagem de Pushes que falharam porque o ChannelURI no registro não foi reconhecido (status do WNS: 404 não encontrado).|Sem dimensões|
|saída. WNS. expiredchannel|Erro de canal expirado do WNS|Contagem|Total|A contagem de Pushes que falharam porque o ChannelURI está expirado (status do WNS: 410 out).|Sem dimensões|
|saída. WNS. limitado|Notificações limitadas do WNS|Contagem|Total|A contagem de Pushes que falharam porque o WNS está limitando este aplicativo (status do WNS: 406 não aceitável).|Sem dimensões|
|saída. WNS. tokenproviderunreachable|Erros de autorização do WNS (inacessível)|Contagem|Total|O Windows Live não está acessível.|Sem dimensões|
|saída. WNS. InvalidToken|Erros de autorização do WNS (token inválido)|Contagem|Total|O token fornecido ao WNS não é válido (status do WNS: 401 não autorizado).|Sem dimensões|
|saída. WNS. wrongtoken|Erros de autorização do WNS (token incorreto)|Contagem|Total|O token fornecido ao WNS é válido, mas para outro aplicativo (status do WNS: 403 Proibido). Isso pode acontecer se o ChannelURI no registro estiver associado a outro aplicativo. Verifique se o aplicativo cliente está associado ao mesmo aplicativo cujas credenciais estão no Hub de notificação.|Sem dimensões|
|saída. WNS. invalidnotificationformat|Formato de notificação inválido do WNS|Contagem|Total|O formato da notificação é inválido (status do WNS: 400). Observe que o WNS não rejeita todas as cargas inválidas.|Sem dimensões|
|saída. WNS. invalidnotificationsize|Erro de tamanho de notificação inválido do WNS|Contagem|Total|A carga de notificação é muito grande (status do WNS: 413).|Sem dimensões|
|saída. WNS. channelthrottled|Canal do WNS limitado|Contagem|Total|A notificação foi descartada porque o ChannelURI no registro está limitado (cabeçalho de resposta WNS: X-WNS-NotificationStatus: channelThrottled).|Sem dimensões|
|saída. WNS. channeldisconnected|Canal do WNS desconectado|Contagem|Total|A notificação foi descartada porque o ChannelURI no registro está limitado (cabeçalho de resposta do WNS: X-WNS-DeviceConnectionStatus: desconectado).|Sem dimensões|
|saída. WNS. Descartado|Notificações descartadas do WNS|Contagem|Total|A notificação foi descartada porque o ChannelURI no registro está limitado (X-WNS-NotificationStatus: descartado, mas não X-WNS-DeviceConnectionStatus: desconectado).|Sem dimensões|
|saída. WNS. pnserror|Erros de WNS|Contagem|Total|Notificação não entregue devido a erros de comunicação com o WNS.|Sem dimensões|
|saída. WNS. authenticationerror|Erros de autenticação WNS|Contagem|Total|Notificação não entregue devido a erros de comunicação com credenciais inválidas do Windows Live ou token incorreto.|Sem dimensões|
|saída. APNs. Success|Notificações bem-sucedidas do APNS|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Sem dimensões|
|saída. APNs. invalidcredentials|Erros de autorização de APNS|Contagem|Total|A contagem de Pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Sem dimensões|
|saída. APNs. badchannel|Erro de canal insatisfatório de APNS|Contagem|Total|A contagem de Pushes que falharam porque o token é inválido (código de status do protocolo binário APNS: 8. Código de status do protocolo HTTP APNS: 400 com "BadDeviceToken").|Sem dimensões|
|saída. APNs. expiredchannel|Erro de canal expirado APNS|Contagem|Total|A contagem de tokens que foram invalidados pelo canal de comentários do APNS.|Sem dimensões|
|saída. APNs. invalidnotificationsize|Erro de tamanho de notificação inválido do APNS|Contagem|Total|A contagem de Pushes que falharam porque o conteúdo era muito grande (código de status do protocolo binário APNS: 7).|Sem dimensões|
|saída. APNs. pnserror|Erros de APNS|Contagem|Total|A contagem de Pushes que falharam devido a erros de comunicação com o APNS.|Sem dimensões|
|saída. gcm. Success|Notificações de êxito do GCM|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Sem dimensões|
|saída. gcm. invalidcredentials|Erros de autorização do GCM (credenciais inválidas)|Contagem|Total|A contagem de Pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Sem dimensões|
|saída. gcm. badchannel|Erro de canal insatisfatório do GCM|Contagem|Total|A contagem de Pushes que falharam porque o RegistrationId no registro não foi reconhecido (resultado do GCM: registro inválido).|Sem dimensões|
|saída. gcm. expiredchannel|Erro de canal expirado do GCM|Contagem|Total|A contagem de Pushes que falharam porque o RegistrationId no registro expirou (resultado do GCM: não registrado).|Sem dimensões|
|saída. gcm. Throttle|Notificações limitadas do GCM|Contagem|Total|A contagem de Pushes que falharam porque o GCM restringiu este aplicativo (código de status do GCM: 501-599 ou resultado: não disponível).|Sem dimensões|
|saída. gcm. invalidnotificationformat|Formato de notificação inválido do GCM|Contagem|Total|A contagem de Pushes que falharam porque a carga não foi formatada corretamente (resultado do GCM: InvalidDataKey ou InvalidTtl).|Sem dimensões|
|saída. gcm. invalidnotificationsize|Erro de tamanho de notificação inválido do GCM|Contagem|Total|A contagem de Pushes que falharam porque o conteúdo era muito grande (resultado do GCM: MessageTooBig).|Sem dimensões|
|saída. gcm. wrongchannel|Erro de canal incorreto do GCM|Contagem|Total|A contagem de Pushes que falharam porque o RegistrationId no registro não está associado ao aplicativo atual (resultado do GCM: InvalidPackageName).|Sem dimensões|
|saída. gcm. pnserror|Erros do GCM|Contagem|Total|A contagem de Pushes que falharam devido a erros de comunicação com o GCM.|Sem dimensões|
|saída. gcm. authenticationerror|Erros de autenticação do GCM|Contagem|Total|A contagem de Pushes que falharam porque o PNS não aceitou as credenciais fornecidas as credenciais estão bloqueadas ou a SenderId não está configurada corretamente no aplicativo (resultado do GCM: MismatchedSenderId).|Sem dimensões|
|saída. MPNs. Success|Notificações bem-sucedidas do MPNS|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Sem dimensões|
|saída. MPNs. invalidcredentials|Credenciais inválidas do MPNS|Contagem|Total|A contagem de Pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Sem dimensões|
|saída. MPNs. badchannel|Erro de canal insatisfatório do MPNS|Contagem|Total|A contagem de Pushes que falharam porque o ChannelURI no registro não foi reconhecido (status do MPNS: 404 não encontrado).|Sem dimensões|
|saída. MPNs. Throttle|Notificações limitadas do MPNS|Contagem|Total|A contagem de Pushes que falharam porque o MPNS está limitando este aplicativo (WNS MPNS: 406 não aceitável).|Sem dimensões|
|saída. MPNs. invalidnotificationformat|Formato de notificação inválido do MPNS|Contagem|Total|A contagem de Pushes que falharam porque a carga da notificação era muito grande.|Sem dimensões|
|saída. MPNs. channeldisconnected|Canal do MPNS desconectado|Contagem|Total|A contagem de Pushes que falharam porque o ChannelURI no registro foi desconectado (status do MPNS: 412 não encontrado).|Sem dimensões|
|saída. MPNs. Descartado|Notificações ignoradas do MPNS|Contagem|Total|A contagem de Pushes que foram descartados pelo MPNS (cabeçalho de resposta MPNS: X-NotificationStatus: QueueFull ou suprimido).|Sem dimensões|
|saída. MPNs. pnserror|Erros do MPNS|Contagem|Total|A contagem de Pushes que falharam devido a erros de comunicação com o MPNS.|Sem dimensões|
|saída. MPNs. authenticationerror|Erros de autenticação do MPNS|Contagem|Total|A contagem de Pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Sem dimensões|
|notificationhub. Pushes|Todas as notificações de saída|Contagem|Total|Todas as notificações de saída do hub de notificação|Sem dimensões|
|entrada. All. Requests|Todas as solicitações de entrada|Contagem|Total|Total de solicitações de entrada para um hub de notificação|Sem dimensões|
|entrada. All. failedrequests|Todas as solicitações com falha de entrada|Contagem|Total|Total de solicitações de entrada com falha para um hub de notificação|Sem dimensões|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/Workspaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Average_% de inodes livres|% De inodes livres|Contagem|Média|Average_% de inodes livres|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de espaço livre|% De espaço livre|Contagem|Média|Average_% de espaço livre|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Inodes de Average_% usados|% De inodes usados|Contagem|Média|Inodes de Average_% usados|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de espaço usado|% De espaço usado|Contagem|Média|Average_% de espaço usado|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Bytes de leitura de Average_Disk/s|Bytes de leitura de disco/s|Contagem|Média|Bytes de leitura de Average_Disk/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Leituras de Average_Disk/s|Leituras de disco/s|Contagem|Média|Leituras de Average_Disk/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Disk transferências de transferência/s|Transferências de disco/s|Contagem|Média|Average_Disk transferências de transferência/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Bytes de gravação de Average_Disk/s|Bytes de gravação no disco/s|Contagem|Média|Bytes de gravação de Average_Disk/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Gravações de Average_Disk/s|Gravações de disco/s|Contagem|Média|Gravações de Average_Disk/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Free megabytes|Megabytes livres|Contagem|Média|Average_Free megabytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Logical bytes de disco/s|Bytes de disco lógico/s|Contagem|Média|Average_Logical bytes de disco/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de memória disponível|% De memória disponível|Contagem|Média|Average_% de memória disponível|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de espaço de permuta disponível|% De espaço de permuta disponível|Contagem|Média|Average_% de espaço de permuta disponível|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% used Memory|% De memória usada|Contagem|Média|Average_% used Memory|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de espaço de permuta utilizado|% De espaço de permuta utilizado|Contagem|Média|Average_% de espaço de permuta utilizado|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Memória de Mbytes de Average_Available|Memória de MBytes disponíveis|Contagem|Média|Memória de Mbytes de Average_Available|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Permuta de Mbytes de Average_Available|Troca de MBytes disponíveis|Contagem|Média|Permuta de Mbytes de Average_Available|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Leituras de Average_Page/s|Leituras de página/s|Contagem|Média|Leituras de Average_Page/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Gravações de Average_Page/s|Gravações de página/s|Contagem|Média|Gravações de Average_Page/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Pages/s|Páginas/s|Contagem|Média|Average_Pages/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Espaço de permuta de Mbytes Average_Used|Espaço de permuta usado em Mbytes|Contagem|Média|Espaço de permuta de Mbytes Average_Used|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Mbytes de memória Average_Used|Mbytes de memória usados|Contagem|Média|Mbytes de memória Average_Used|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total bytes transmitidos|Total de bytes transmitidos|Contagem|Média|Average_Total bytes transmitidos|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total bytes recebidos|Total de bytes recebidos|Contagem|Média|Average_Total bytes recebidos|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total bytes|Total de bytes|Contagem|Média|Average_Total bytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Pacotes Average_Total transmitidos|Total de pacotes transmitidos|Contagem|Média|Pacotes Average_Total transmitidos|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Pacotes Average_Total recebidos|Total de pacotes recebidos|Contagem|Média|Pacotes Average_Total recebidos|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Erros de RX Average_Total|Total de erros de RX|Contagem|Média|Erros de RX Average_Total|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Erros de TX Average_Total|Total de erros TX|Contagem|Média|Erros de TX Average_Total|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Colisões de Average_Total|Total de colisões|Contagem|Média|Colisões de Average_Total|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Disco s/leitura|Média de disco s/leitura|Contagem|Média|Average_Avg. Disco s/leitura|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Disco s/transferência|Média de disco s/transferência|Contagem|Média|Average_Avg. Disco s/transferência|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Disco s/gravação|Média de disco s/gravação|Contagem|Média|Average_Avg. Disco s/gravação|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Physical bytes de disco/s|Bytes de disco físico/s|Contagem|Média|Average_Physical bytes de disco/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Pct tempo privilegiado|Percentual de tempo privilegiado|Contagem|Média|Average_Pct tempo privilegiado|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Pct tempo de usuário|Tempo de usuário do PCT|Contagem|Média|Average_Pct tempo de usuário|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|KBytes de memória Average_Used|KBytes de memória usada|Contagem|Média|KBytes de memória Average_Used|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Memória compartilhada Average_Virtual|Memória compartilhada virtual|Contagem|Média|Memória compartilhada Average_Virtual|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% tempo de DPC|% De tempo de DPC|Contagem|Média|Average_% tempo de DPC|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de tempo ocioso|% De tempo ocioso|Contagem|Média|Average_% de tempo ocioso|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% tempo de interrupção|% De tempo de interrupção|Contagem|Média|Average_% tempo de interrupção|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% IO tempo de espera|% De tempo de espera de e/s|Contagem|Média|Average_% IO tempo de espera|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de tempo bom|% De tempo adequado|Contagem|Média|Average_% de tempo bom|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de tempo privilegiado|% De tempo privilegiado|Contagem|Média|Average_% de tempo privilegiado|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% tempo do processador|% De tempo do processador|Contagem|Média|Average_% tempo do processador|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de tempo de usuário|% De tempo do usuário|Contagem|Média|Average_% de tempo de usuário|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Memória física Average_Free|Memória física livre|Contagem|Média|Memória física Average_Free|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Free espaço em arquivos de paginação|Espaço livre em arquivos de paginação|Contagem|Média|Average_Free espaço em arquivos de paginação|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Memória virtual Average_Free|Memória virtual livre|Contagem|Média|Memória virtual Average_Free|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Processes|Processos|Contagem|Média|Average_Processes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Size armazenados em arquivos de paginação|Tamanho armazenado em arquivos de paginação|Contagem|Média|Average_Size armazenados em arquivos de paginação|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Uptime|Atividade|Contagem|Média|Average_Uptime|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Users|Utilizadores|Contagem|Média|Average_Users|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Disco s/leitura|Média de disco s/leitura|Contagem|Média|Average_Avg. Disco s/leitura|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Disco s/gravação|Média de disco s/gravação|Contagem|Média|Average_Avg. Disco s/gravação|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Comprimento da fila de disco Average_Current|Comprimento da fila de disco atual|Contagem|Média|Comprimento da fila de disco Average_Current|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Leituras de Average_Disk/s|Leituras de disco/s|Contagem|Média|Leituras de Average_Disk/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Disk transferências de transferência/s|Transferências de disco/s|Contagem|Média|Average_Disk transferências de transferência/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Gravações de Average_Disk/s|Gravações de disco/s|Contagem|Média|Gravações de Average_Disk/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Free megabytes|Megabytes livres|Contagem|Média|Average_Free megabytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% de espaço livre|% De espaço livre|Contagem|Média|Average_% de espaço livre|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Available Mbytes|MBytes disponíveis|Contagem|Média|Average_Available Mbytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% bytes confirmados em uso|% Bytes confirmados em uso|Contagem|Média|Average_% bytes confirmados em uso|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Bytes recebidos/s|Bytes recebidos/s|Contagem|Média|Average_Bytes recebidos/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Bytes enviados/s|Bytes enviados/s|Contagem|Média|Average_Bytes enviados/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Total de Average_Bytes/s|Total de bytes/s|Contagem|Média|Total de Average_Bytes/s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% tempo do processador|% De tempo do processador|Contagem|Média|Average_% tempo do processador|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Comprimento da fila de Average_Processor|Comprimento da fila do processador|Contagem|Média|Comprimento da fila de Average_Processor|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Batida|Batida|Contagem|Total|Batida|Computador, OSType, versão, SourceComputerId|
|Atualizar|Atualizar|Contagem|Média|Atualizar|Computador, produto, classificação, UpdateState, opcional, aprovado|
|Evento|Evento|Contagem|Média|Evento|Origem, EventLog, computador, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/capacidades

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueryDuration|Duração da consulta|Milissegundos|Média|Duração da consulta DAX no último intervalo|Sem dimensões|
|QueryPoolJobQueueLength|Threads: comprimento da fila de trabalhos do pool de consultas|Contagem|Média|Número de trabalhos na fila do pool de threads de consulta.|Sem dimensões|
|qpu_high_utilization_metric|Alta utilização de QPU|Contagem|Total|QPU alta utilização no último minuto, 1 para alta utilização de QPU, caso contrário 0|Sem dimensões|
|memory_metric|Memória|Bytes|Média|Memória. Intervalo de 0-3 GB para a1, 0-5 GB para a2, 0-10 GB para a3, 0-25 GB para A4, 0-50 GB para a5 e 0-100 GB para a6|Sem dimensões|
|memory_thrashing_metric|Ultrapaginação de memória|Percentagem|Média|Média de ultrapaginação de memória.|Sem dimensões|

## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/namespaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ListenerConnections-êxito|ListenerConnections-êxito|Contagem|Total|ListenerConnections com êxito para Microsoft. Relay.|entityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Contagem|Total|ClientError em ListenerConnections para Microsoft. Relay.|entityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Contagem|Total|ServerError em ListenerConnections para Microsoft. Relay.|entityName|
|SenderConnections-êxito|SenderConnections-êxito|Contagem|Total|SenderConnections com êxito para Microsoft. Relay.|entityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Contagem|Total|ClientError em SenderConnections para Microsoft. Relay.|entityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Contagem|Total|ServerError em SenderConnections para Microsoft. Relay.|entityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Contagem|Total|Total de ListenerConnections para Microsoft. Relay.|entityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Contagem|Total|Total de solicitações SenderConnections para Microsoft. Relay.|entityName|
|ActiveConnections|ActiveConnections|Contagem|Total|Total de ActiveConnections para Microsoft. Relay.|entityName|
|ActiveListeners|ActiveListeners|Contagem|Total|Total de ActiveListeners para Microsoft. Relay.|entityName|
|BytesTransferred|BytesTransferred|Contagem|Total|Total de BytesTransferred para Microsoft. Relay.|entityName|
|ListenerDisconnects|ListenerDisconnects|Contagem|Total|Total de ListenerDisconnects para Microsoft. Relay.|entityName|
|SenderDisconnects|SenderDisconnects|Contagem|Total|Total de SenderDisconnects para Microsoft. Relay.|entityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/SearchServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SearchLatency|Latência de pesquisa|Segundos|Média|Latência de pesquisa média para o serviço de pesquisa|Sem dimensões|
|SearchQueriesPerSecond|Pesquisar consultas por segundo|CountPerSecond|Média|Pesquisar consultas por segundo para o serviço de pesquisa|Sem dimensões|
|ThrottledSearchQueriesPercentage|Percentual de consultas de pesquisa limitadas|Percentagem|Média|Porcentagem de consultas de pesquisa que foram limitadas para o serviço de pesquisa|Sem dimensões|

## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/namespaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfulRequests|Solicitações com êxito (versão prévia)|Contagem|Total|Total de solicitações bem-sucedidas para um namespace (versão prévia)|entityName|
|ServerErrors|Erros do servidor. (Pré-visualização)|Contagem|Total|Erros de servidor para Microsoft. ServiceBus. (Pré-visualização)|entityName|
|UserErrors|Erros do usuário. (Pré-visualização)|Contagem|Total|Erros de usuário para Microsoft. ServiceBus. (Pré-visualização)|entityName|
|ThrottledRequests|Solicitações limitadas. (Pré-visualização)|Contagem|Total|Solicitações limitadas para Microsoft. ServiceBus. (Pré-visualização)|entityName|
|IncomingRequests|Solicitações de entrada (versão prévia)|Contagem|Total|Solicitações de entrada para Microsoft. ServiceBus. (Pré-visualização)|entityName|
|IncomingMessages|Mensagens de entrada (versão prévia)|Contagem|Total|Mensagens de entrada para Microsoft. ServiceBus. (Pré-visualização)|entityName|
|OutgoingMessages|Mensagens de saída (versão prévia)|Contagem|Total|Mensagens de saída para Microsoft. ServiceBus. (Pré-visualização)|entityName|
|ActiveConnections|ActiveConnections (visualização)|Contagem|Total|Total de conexões ativas para Microsoft. ServiceBus. (Pré-visualização)|Sem dimensões|
|Tamanho|Tamanho (versão prévia)|Bytes|Média|Tamanho de uma fila/tópico em bytes. (Pré-visualização)|entityName|
|Mensagens|Contagem de mensagens em uma fila/tópico. (Pré-visualização)|Contagem|Média|Contagem de mensagens em uma fila/tópico. (Pré-visualização)|entityName|
|ActiveMessages|Contagem de mensagens ativas em uma fila/tópico. (Pré-visualização)|Contagem|Média|Contagem de mensagens ativas em uma fila/tópico. (Pré-visualização)|entityName|
|DeadletteredMessages|Contagem de mensagens mortas em uma fila/tópico. (Pré-visualização)|Contagem|Média|Contagem de mensagens mortas em uma fila/tópico. (Pré-visualização)|entityName|
|ScheduledMessages|Contagem de mensagens agendadas em uma fila/tópico. (Pré-visualização)|Contagem|Média|Contagem de mensagens agendadas em uma fila/tópico. (Pré-visualização)|entityName|
|CPUXNS|Uso da CPU por namespace|Percentagem|Máximo|Métrica de uso de CPU do namespace Premium do barramento de serviço|Sem dimensões|
|WSXNS|Uso do tamanho da memória por namespace|Percentagem|Máximo|Métrica de uso de memória do namespace Premium do barramento de serviço|Sem dimensões|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. ServiceFabricMesh/aplicativos

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Contagem|Média|CPU alocada para este contêiner em milicores|ApplicationName, ServiceName, CodePackageName, ServiceId|
|AllocatedMemory|AllocatedMemory|Bytes|Média|Memória alocada para este contêiner em MB|ApplicationName, ServiceName, CodePackageName, ServiceId|
|ActualCpu|ActualCpu|Contagem|Média|Uso real da CPU em milicores|ApplicationName, ServiceName, CodePackageName, ServiceId|
|ActualMemory|ActualMemory|Bytes|Média|Uso real da memória em MB|ApplicationName, ServiceName, CodePackageName, ServiceId|
|CpuUtilization|CpuUtilization|Percentagem|Média|Utilização da CPU para este contêiner como porcentagem de AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceId|
|MemoryUtilization|MemoryUtilization|Percentagem|Média|Utilização da CPU para este contêiner como porcentagem de AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceId|
|ApplicationStatus|ApplicationStatus|Contagem|Média|Status do aplicativo de malha Service Fabric|ApplicationName, status|
|Perstatus|Perstatus|Contagem|Média|Status de integridade de um serviço no aplicativo de malha Service Fabric|ApplicationName, status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Contagem|Média|Status de integridade de uma réplica de serviço no aplicativo de malha Service Fabric|ApplicationName, status, ServiceName, nome de réplica|
|ContainerStatus|ContainerStatus|Contagem|Média|Status do contêiner no aplicativo de malha Service Fabric|ApplicationName, ServiceName, CodePackageName, ServiceId, status|
|RestartCount|RestartCount|Contagem|Média|Reiniciar a contagem de um contêiner no aplicativo de malha Service Fabric|ApplicationName, status, ServiceName, nome de réplica, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/Signalr

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ConnectionCount|Contagem de conexões|Contagem|Máximo|A quantidade de conexão do usuário.|Ponto Final|
|MessageCount|Contagem de mensagens|Contagem|Total|A quantidade total de mensagens.|Sem dimensões|
|InboundTraffic|Tráfego de Entrada|Bytes|Total|O tráfego de entrada do serviço|Sem dimensões|
|OutboundTraffic|Tráfego de Saída|Bytes|Total|O tráfego de saída do serviço|Sem dimensões|
|UserErrors|Erros do usuário|Percentagem|Máximo|A porcentagem de erros do usuário|Sem dimensões|
|SystemErrors|Erros do sistema|Percentagem|Máximo|A porcentagem de erros do sistema|Sem dimensões|

## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/Servers/bancos de dados

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Sem dimensões|
|physical_data_read_percent|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|Sem dimensões|
|log_write_percent|Percentual de e/s de log|Percentagem|Média|Percentual de e/s de log. Não aplicável a data warehouses.|Sem dimensões|
|dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|Porcentagem de DTU. Aplica-se a bancos de dados baseados em DTU.|Sem dimensões|
|armazenamento|Espaço de dados usado|Bytes|Máximo|Tamanho total do banco de dados. Não aplicável a data warehouses.|Sem dimensões|
|connection_successful|Conexões com êxito|Contagem|Total|Conexões com êxito|Sem dimensões|
|connection_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Sem dimensões|
|blocked_by_firewall|Bloqueado pelo firewall|Contagem|Total|Bloqueado pelo firewall|Sem dimensões|
|Bloqueado|Deadlocks|Contagem|Total|Deadlocks. Não aplicável a data warehouses.|Sem dimensões|
|storage_percent|Porcentagem de espaço de dados usada|Percentagem|Máximo|Porcentagem de tamanho do banco de dados. Não aplicável a data warehouses ou a bancos de dados de hiperescala.|Sem dimensões|
|xtp_storage_percent|Porcentagem de armazenamento OLTP na memória|Percentagem|Média|Porcentagem de armazenamento OLTP na memória. Não aplicável a data warehouses.|Sem dimensões|
|workers_percent|Porcentagem de trabalhadores|Percentagem|Média|Porcentagem de trabalhadores. Não aplicável a data warehouses.|Sem dimensões|
|sessions_percent|Porcentagem de sessões|Percentagem|Média|Porcentagem de sessões. Não aplicável a data warehouses.|Sem dimensões|
|dtu_limit|Limite de DTU|Contagem|Média|Limite de DTU. Aplica-se a bancos de dados baseados em DTU.|Sem dimensões|
|dtu_used|DTU usado|Contagem|Média|DTU usado. Aplica-se a bancos de dados baseados em DTU.|Sem dimensões|
|cpu_limit|Limite de CPU|Contagem|Média|Limite de CPU. Aplica-se a bancos de dados baseados em vCore.|Sem dimensões|
|cpu_used|CPU usada|Contagem|Média|CPU usada. Aplica-se a bancos de dados baseados em vCore.|Sem dimensões|
|dwu_limit|Limite de DWU|Contagem|Máximo|Limite de DWU. Aplica-se somente a data warehouses.|Sem dimensões|
|dwu_consumption_percent|Porcentagem de DWU|Percentagem|Máximo|Porcentagem de DWU. Aplica-se somente a data warehouses.|Sem dimensões|
|dwu_used|DWU usado|Contagem|Máximo|DWU usado. Aplica-se somente a data warehouses.|Sem dimensões|
|dw_cpu_percent|Percentual de CPU no nível de nó DW|Percentagem|Média|Percentual de CPU no nível de nó DW|DwLogicalNodeId|
|dw_physical_data_read_percent|Porcentagem de e/s de dados de nível de nó DW|Percentagem|Média|Porcentagem de e/s de dados de nível de nó DW|DwLogicalNodeId|
|cache_hit_percent|Porcentagem de acesso ao cache|Percentagem|Máximo|Porcentagem de acesso ao cache. Aplica-se somente a data warehouses.|Sem dimensões|
|cache_used_percent|Percentual de cache usado|Percentagem|Máximo|Percentual de cache usado. Aplica-se somente a data warehouses.|Sem dimensões|
|local_tempdb_usage_percent|Porcentagem de tempdb local|Percentagem|Média|Porcentagem de tempdb local. Aplica-se somente a data warehouses.|Sem dimensões|
|app_cpu_billed|CPU do aplicativo cobrada|Contagem|Total|CPU do aplicativo cobrada. Aplica-se a bancos de dados sem servidor.|Sem dimensões|
|app_cpu_percent|Porcentagem de CPU do aplicativo|Percentagem|Média|Porcentagem de CPU do aplicativo. Aplica-se a bancos de dados sem servidor.|Sem dimensões|
|app_memory_percent|Porcentagem de memória usada do aplicativo|Percentagem|Média|Porcentagem de memória usada do aplicativo. Aplica-se a bancos de dados sem servidor.|Sem dimensões|
|allocated_data_storage|Espaço de dados alocado|Bytes|Média|Espaço de dados alocado. Não aplicável a data warehouses.|Sem dimensões|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft. SQL/Servers/elasticPools

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Sem dimensões|
|physical_data_read_percent|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|Sem dimensões|
|log_write_percent|Percentual de e/s de log|Percentagem|Média|Percentual de e/s de log|Sem dimensões|
|dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|Porcentagem de DTU. Aplica-se a pools elásticos baseados em DTU.|Sem dimensões|
|storage_percent|Porcentagem de espaço de dados usada||Percentagem|Média|Porcentagem de armazenamento|Sem dimensões|
|workers_percent|Porcentagem de trabalhadores|Percentagem|Média|Porcentagem de trabalhadores|Sem dimensões|
|sessions_percent|Porcentagem de sessões|Percentagem|Média|Porcentagem de sessões|Sem dimensões|
|eDTU_limit|limite de eDTU|Contagem|Média|limite de eDTU. Aplica-se a pools elásticos baseados em DTU.|Sem dimensões|
|storage_limit|Tamanho máximo de dados|Bytes|Média|Limite de armazenamento|Sem dimensões|
|eDTU_used|eDTU usado|Contagem|Média|eDTU usado. Aplica-se a pools elásticos baseados em DTU.|Sem dimensões|
|storage_used|Espaço de dados usado|Bytes|Média|Armazenamento utilizado|Sem dimensões|
|xtp_storage_percent|Porcentagem de armazenamento OLTP na memória|Percentagem|Média|Porcentagem de armazenamento OLTP na memória|Sem dimensões|
|cpu_limit|Limite de CPU|Contagem|Média|Limite de CPU. Aplica-se a pools elásticos baseados em vCore.|Sem dimensões|
|cpu_used|CPU usada|Contagem|Média|CPU usada. Aplica-se a pools elásticos baseados em vCore.|Sem dimensões|
|allocated_data_storage|Espaço de dados alocado|Bytes|Média|Espaço de dados alocado|Sem dimensões|
|allocated_data_storage_percent|Porcentagem alocada de espaço de dados|Percentagem|Máximo|Porcentagem alocada de espaço de dados|Sem dimensões|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|virtual_core_count|Contagem de núcleos virtuais|Contagem|Média|Contagem de núcleos virtuais|Sem dimensões|
|avg_cpu_percent|Percentual médio de CPU|Percentagem|Média|Percentual médio de CPU|Sem dimensões|
|reserved_storage_mb|Espaço de armazenamento reservado|Contagem|Média|Espaço de armazenamento reservado|Sem dimensões|
|storage_space_used_mb|Espaço de armazenamento usado|Contagem|Média|Espaço de armazenamento usado|Sem dimensões|
|io_requests|Contagem de solicitações de e/s|Contagem|Média|Contagem de solicitações de e/s|Sem dimensões|
|io_bytes_read|Bytes de e/s lidos|Bytes|Média|Bytes de e/s lidos|Sem dimensões|
|io_bytes_written|Bytes de e/s gravados|Bytes|Média|Bytes de e/s gravados|Sem dimensões|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|UsedCapacity|Capacidade usada|Bytes|Média|Capacidade usada da conta|Sem dimensões|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão ResponseType para o número de tipos diferentes de resposta.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de egresso, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência do servidor com êxito|Milissegundos|Média|A latência média usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E de sucesso|Milissegundos|Média|A latência média de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A porcentagem de disponibilidade para o serviço de armazenamento ou a operação da API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobservices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Capacidade|Capacidade do blob|Bytes|Média|A quantidade de armazenamento usada pelo serviço BLOB da conta de armazenamento em bytes.|BlobType, camada|
|BlobCount|Contagem de BLOBs|Contagem|Total|O número de blob no serviço BLOB da conta de armazenamento.|BlobType|       |BlobCount|Contagem de BLOBs|Contagem|Média|O número de blob no serviço BLOB da conta de armazenamento.|BlobType, camada|
|ContainerCount|Contagem de contêineres de BLOB|Contagem|Média|O número de contêineres no serviço BLOB da conta de armazenamento.|Sem dimensões|
|IndexCapacity|Capacidade do índice|Bytes|Média|A quantidade de armazenamento usada pelo índice ADLS Gen2 (hierárquico) em bytes.|Sem dimensões|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão ResponseType para o número de tipos diferentes de resposta.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de egresso, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência do servidor com êxito|Milissegundos|Média|A latência média usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E de sucesso|Milissegundos|Média|A latência média de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A porcentagem de disponibilidade para o serviço de armazenamento ou a operação da API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileservices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Capacidade de|Capacidade do arquivo|Bytes|Média|A quantidade de armazenamento usada pelo serviço de arquivo da conta de armazenamento em bytes.|Sem dimensões|
|FileCount|Contagem de arquivos|Contagem|Média|O número de arquivos no serviço de arquivo da conta de armazenamento.|Sem dimensões|
|FileShareCount|Contagem de compartilhamento de arquivos|Contagem|Média|O número de compartilhamentos de arquivos no serviço de arquivo da conta de armazenamento.|Sem dimensões|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão ResponseType para o número de tipos diferentes de resposta.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de egresso, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência do servidor com êxito|Milissegundos|Média|A latência média usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E de sucesso|Milissegundos|Média|A latência média de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A porcentagem de disponibilidade para o serviço de armazenamento ou a operação da API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueservices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueueCapacity|Capacidade da fila|Bytes|Média|A quantidade de armazenamento usada pela serviço Fila da conta de armazenamento em bytes.|Sem dimensões|
|QueueCount|Contagem de filas|Contagem|Média|O número de filas na serviço Fila da conta de armazenamento.|Sem dimensões|
|QueueMessageCount|Contagem de mensagens da fila|Contagem|Média|O número aproximado de mensagens de fila na serviço Fila da conta de armazenamento.|Sem dimensões|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão ResponseType para o número de tipos diferentes de resposta.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de egresso, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência do servidor com êxito|Milissegundos|Média|A latência média usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E de sucesso|Milissegundos|Média|A latência média de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A porcentagem de disponibilidade para o serviço de armazenamento ou a operação da API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableservices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TableCapacity|Capacidade da tabela|Bytes|Média|A quantidade de armazenamento usada pelo serviço tabela da conta de armazenamento em bytes.|Sem dimensões|
|TableCount|Contagem de tabelas|Contagem|Média|O número de tabelas no serviço tabela da conta de armazenamento.|Sem dimensões|
|TableEntityCount|Contagem de entidades de tabela|Contagem|Média|O número de entidades de tabela no serviço tabela da conta de armazenamento.|Sem dimensões|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão ResponseType para o número de tipos diferentes de resposta.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de egresso, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência do servidor com êxito|Milissegundos|Média|A latência média usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E de sucesso|Milissegundos|Média|A latência média de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A porcentagem de disponibilidade para o serviço de armazenamento ou a operação da API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftstoragesyncstoragesyncservices"></a>Microsoft. storagesync/storageSyncServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Resultado da sessão de sincronização|Contagem|Média|Métrica que registra um valor de 1 cada vez que o ponto de extremidade do servidor conclui com êxito uma sessão de sincronização com o ponto de extremidade de nuvem|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Arquivos sincronizados|Contagem|Total|Contagem de arquivos sincronizados|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Arquivos não sincronizando|Contagem|Total|Falha na sincronização da contagem de arquivos|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Bytes sincronizados|Bytes|Total|Tamanho total do arquivo transferido para sessões de sincronização|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Status online do servidor|Contagem|Máximo|Métrica que registra um valor de 1 cada vez que o servidor registrado registra com êxito uma pulsação com o ponto de extremidade de nuvem|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Recall de camadas de nuvem|Bytes|Total|Tamanho total dos dados recuperados pelo servidor|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ResourceUtilization|% De utilização de SU|Percentagem|Máximo|% De utilização de SU|LogicalName, PartitionID|
|InputEvents|Eventos de entrada|Contagem|Total|Eventos de entrada|LogicalName, PartitionID|
|InputEventBytes|Bytes de evento de entrada|Bytes|Total|Bytes de evento de entrada|LogicalName, PartitionID|
|LateInputEvents|Eventos de entrada tardia|Contagem|Total|Eventos de entrada tardia|LogicalName, PartitionID|
|OutputEvents|Eventos de saída|Contagem|Total|Eventos de saída|LogicalName, PartitionID|
|ConversionErrors|Erros de conversão de dados|Contagem|Total|Erros de conversão de dados|LogicalName, PartitionID|
|Erros|Erros de tempo de execução|Contagem|Total|Erros de tempo de execução|LogicalName, PartitionID|
|DroppedOrAdjustedEvents|Eventos fora de ordem|Contagem|Total|Eventos fora de ordem|LogicalName, PartitionID|
|AMLCalloutRequests|Solicitações de função|Contagem|Total|Solicitações de função|LogicalName, PartitionID|
|AMLCalloutFailedRequests|Solicitações de função com falha|Contagem|Total|Solicitações de função com falha|LogicalName, PartitionID|
|AMLCalloutInputEvents|Eventos de função|Contagem|Total|Eventos de função|LogicalName, PartitionID|
|DeserializationError|Erros de desserialização de entrada|Contagem|Total|Erros de desserialização de entrada|LogicalName, PartitionID|
|EarlyInputEvents|Eventos de entrada antecipada|Contagem|Total|Eventos de entrada antecipada|LogicalName, PartitionID|
|OutputWatermarkDelaySeconds|Atraso da marca d' água|Segundos|Máximo|Atraso da marca d' água|LogicalName, PartitionID|
|InputEventsSourcesBacklogged|Eventos de entrada de registro posterior|Contagem|Máximo|Eventos de entrada de registro posterior|LogicalName, PartitionID|
|InputEventsSourcesPerSecond|Fontes de entrada recebidas|Contagem|Total|Fontes de entrada recebidas|LogicalName, PartitionID|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/ambientes

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|IngressReceivedMessages|Mensagens de entrada recebidas|Contagem|Total|Contagem de mensagens lidas de todas as fontes de eventos do hub de eventos ou do Hub IoT|Sem dimensões|
|IngressReceivedInvalidMessages|A entrada recebeu mensagens inválidas|Contagem|Total|Contagem de mensagens inválidas lidas de todas as fontes de eventos do hub de eventos ou do Hub IoT|Sem dimensões|
|IngressReceivedBytes|Bytes de entrada recebidos|Bytes|Total|Contagem de bytes lidos de todas as origens do evento|Sem dimensões|
|IngressStoredBytes|Bytes de entrada armazenados|Bytes|Total|Tamanho total de eventos processados e disponíveis com êxito para consulta|Sem dimensões|
|IngressStoredEvents|Eventos de entrada armazenados|Contagem|Total|Contagem de eventos mesclados processados e disponíveis com êxito para consulta|Sem dimensões|
|IngressReceivedMessagesTimeLag|Atraso de tempo de mensagens recebidas de entrada|Segundos|Máximo|Diferença entre a hora em que a mensagem é enfileirada na origem do evento e a hora em que ela é processada na entrada|Sem dimensões|
|IngressReceivedMessagesCountLag|Atraso na contagem de mensagens recebidas de entrada|Contagem|Média|Diferença entre o número de sequência da última mensagem enfileirada na partição de origem do evento e o número de sequência da mensagem sendo processada na entrada|Sem dimensões|
|WarmStorageMaxProperties|Propriedades máximas de armazenamento quente|Contagem|Máximo|Número máximo de propriedades usadas permitidas pelo ambiente para SKU S1/S2 e número máximo de propriedades permitidas pelo armazenamento quente para o SKU PAYG|Sem dimensões|
|WarmStorageUsedProperties|Propriedades usadas de armazenamento quente |Contagem|Máximo|Número de propriedades usadas pelo ambiente para SKU S1/S2 e número de propriedades usadas pelo armazenamento quente para SKU PAYG|Sem dimensões|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/ambientes/EventSources

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|IngressReceivedMessages|Mensagens de entrada recebidas|Contagem|Total|Contagem de mensagens lidas da origem do evento|Sem dimensões|
|IngressReceivedInvalidMessages|A entrada recebeu mensagens inválidas|Contagem|Total|Contagem de mensagens inválidas lidas da origem do evento|Sem dimensões|
|IngressReceivedBytes|Bytes de entrada recebidos|Bytes|Total|Contagem de bytes lidos da origem do evento|Sem dimensões|
|IngressStoredBytes|Bytes de entrada armazenados|Bytes|Total|Tamanho total de eventos processados e disponíveis com êxito para consulta|Sem dimensões|
|IngressStoredEvents|Eventos de entrada armazenados|Contagem|Total|Contagem de eventos mesclados processados e disponíveis com êxito para consulta|Sem dimensões|
|IngressReceivedMessagesTimeLag|Atraso de tempo de mensagens recebidas de entrada|Segundos|Máximo|Diferença entre a hora em que a mensagem é enfileirada na origem do evento e a hora em que ela é processada na entrada|Sem dimensões|
|IngressReceivedMessagesCountLag|Atraso na contagem de mensagens recebidas de entrada|Contagem|Média|Diferença entre o número de sequência da última mensagem enfileirada na partição de origem do evento e o número de sequência da mensagem sendo processada na entrada|Sem dimensões|
|WarmStorageMaxProperties|Propriedades máximas de armazenamento quente|Contagem|Máximo|Número máximo de propriedades usadas permitidas pelo ambiente para SKU S1/S2 e número máximo de propriedades permitidas pelo armazenamento quente para o SKU PAYG|Sem dimensões|
|WarmStorageUsedProperties|Propriedades usadas de armazenamento quente |Contagem|Máximo|Número de propriedades usadas pelo ambiente para SKU S1/S2 e número de propriedades usadas pelo armazenamento quente para SKU PAYG|Sem dimensões|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Bytes de leitura de disco/s|BytesPerSecond|Média|Taxa de transferência média do disco devido a operações de leitura no período de exemplo.|Sem dimensões|
|DiskWriteBytesPerSecond|Bytes de gravação no disco/s|BytesPerSecond|Média|Taxa de transferência média do disco devido a operações de gravação no período de exemplo.|Sem dimensões|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Taxa de transferência total do disco devido a operações de leitura no período de exemplo.|Sem dimensões|
|Bytes de gravação no disco|Bytes de gravação no disco|Bytes|Total|Taxa de transferência total do disco devido a operações de gravação no período de exemplo.|Sem dimensões|
|DiskReadOperations|Operações de leitura de disco|Contagem|Total|O número de operações de leitura de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Sem dimensões|
|DiskWriteOperations|Operações de gravação de disco|Contagem|Total|O número de operações de gravação de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Sem dimensões|
|Operações de leitura de disco/s|Operações de leitura de disco/s|CountPerSecond|Média|O número médio de operações de leitura de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Sem dimensões|
|Operações de gravação de disco/s|Operações de gravação de disco/s|CountPerSecond|Média|O número médio de operações de gravação de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Sem dimensões|
|DiskReadLatency|Latência de leitura de disco|Milissegundos|Média|Latência de leitura total. A soma das latências de leitura do dispositivo e do kernel.|Sem dimensões|
|DiskWriteLatency|Latência de gravação de disco|Milissegundos|Média|Latência de gravação total. A soma das latências de gravação do kernel e do dispositivo.|Sem dimensões|
|NetworkInBytesPerSecond|Rede em bytes/s|BytesPerSecond|Média|Taxa de transferência média de rede para o tráfego recebido.|Sem dimensões|
|NetworkOutBytesPerSecond|Bytes de saída de rede/s|BytesPerSecond|Média|Taxa de transferência média de rede para o tráfego transmitido.|Sem dimensões|
|Entrada na Rede|Entrada na Rede|Bytes|Total|Taxa de transferência total de rede para o tráfego recebido.|Sem dimensões|
|Saída da Rede|Saída da Rede|Bytes|Total|Taxa de transferência total de rede para o tráfego transmitido.|Sem dimensões|
|MemoryUsed|Memória usada|Bytes|Média|A quantidade de memória da máquina que está sendo usada pela VM.|Sem dimensões|
|MemoryGranted|Memória concedida|Bytes|Média|A quantidade de memória que foi concedida à VM pelo host. A memória não é concedida ao host até que ele seja tocado uma vez e a memória concedida possa ser trocada ou bloqueada se o VMkernel precisar da memória.|Sem dimensões|
|MemoryActive|Memória ativa|Bytes|Média|A quantidade de memória usada pela VM na última pequena janela de tempo. Esse é o número "verdadeiro" da quantidade de memória necessária para a VM no momento. A memória adicional não utilizada pode ser trocada ou por balão sem impacto no desempenho do convidado.|Sem dimensões|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A utilização da CPU. Esse valor é relatado com 100% que representa todos os núcleos de processador no sistema. Por exemplo, uma VM de 2 vias usando 50% de um sistema de quatro núcleos está usando completamente dois núcleos.|Sem dimensões|
|PercentageCpuReady|Percentual de CPU pronta|Milissegundos|Total|O tempo de preparação é o tempo gasto aguardando que as CPUs fiquem disponíveis no intervalo de atualização anterior.|Sem dimensões|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuPercentage|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Instância|
|MemoryPercentage|Porcentagem de memória|Percentagem|Média|Porcentagem de memória|Instância|
|DiskQueueLength|Comprimento da fila de disco|Contagem|Média|Comprimento da fila de disco|Instância|
|HttpQueueLength|Comprimento da fila http|Contagem|Média|Comprimento da fila http|Instância|
|BytesReceived|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Saída de dados|Bytes|Total|Saída de dados|Instância|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/sites (exceto funções)

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
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

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
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

## <a name="microsoftwebsitesslots"></a>Microsoft. Web/sites/Slots

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
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

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft. Web/hostingEnvironments/multiRolePools

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
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
|TotalFrontEnds|Total de front-ends|Contagem|Média|Total de front-ends|Sem dimensões|
|SmallAppServicePlanInstances|Trabalhos do plano do serviço de aplicativo pequeno|Contagem|Média|Trabalhos do plano do serviço de aplicativo pequeno|Sem dimensões|
|MediumAppServicePlanInstances|Trabalhos do plano do serviço de aplicativo médio|Contagem|Média|Trabalhos do plano do serviço de aplicativo médio|Sem dimensões|
|LargeAppServicePlanInstances|Trabalhos grandes do plano do serviço de aplicativo|Contagem|Média|Trabalhos grandes do plano do serviço de aplicativo|Sem dimensões|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/pools

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|WorkersTotal|Total de trabalhadores|Contagem|Média|Total de trabalhadores|Sem dimensões|
|WorkersAvailable|Trabalhos disponíveis|Contagem|Média|Trabalhos disponíveis|Sem dimensões|
|WorkersUsed|Trabalhadores usados|Contagem|Média|Trabalhadores usados|Sem dimensões|
|CpuPercentage|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Instância|
|MemoryPercentage|Porcentagem de memória|Percentagem|Média|Porcentagem de memória|Instância|

## <a name="next-steps"></a>Passos seguintes
* [Leia sobre métricas no Azure Monitor](data-platform.md)
* [Criar alertas em métricas](alerts-overview.md)
* [Exportar métricas para armazenamento, Hub de eventos ou Log Analytics](resource-logs-overview.md)
