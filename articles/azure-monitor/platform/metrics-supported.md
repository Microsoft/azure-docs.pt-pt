---
title: Métricas suportadas pelo Monitor Azure por tipo de recurso
description: Lista de métricas disponíveis para cada tipo de recurso com o Monitor Azure.
author: anirudhcavale
services: azure-monitor
ms.topic: reference
ms.date: 12/18/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 3e43d2baf4337e7a986d59c47f805183a920c7a1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275389"
---
# <a name="supported-metrics-with-azure-monitor"></a>Métricas suportadas com monitor Azure

O Azure Monitor fornece várias formas de interagir com métricas, incluindo mapeá-las no portal, acessá-las através da API REST, ou questioná-las usando PowerShell ou CLI. Abaixo está uma lista completa de todas as métricas atualmente disponíveis com o pipeline métrico do Azure Monitor. Outras métricas podem estar disponíveis no portal ou usando APIs legados. Esta lista abaixo inclui apenas métricas disponíveis utilizando o gasoduto métrico Azure Monitor consolidado. As métricas são organizadas pelo espaço de nome. Para obter uma lista de serviços e os espaços de nome que lhes pertencem, consulte os fornecedores de [recursos para os serviços Azure.](../../azure-resource-manager/management/azure-services-resource-providers.md) Para consultar e aceder a estas métricas programáticamente, por favor use a [versão api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Atualmente, o envio de métricas multidimensionais através das definições de diagnóstico não é suportado. As métricas com dimensões são exportadas como métricas dimensionais simples e agregadas em valores de dimensões.
>
> *Por exemplo*: a métrica “Mensagens Recebidas” num hub do Hub de Eventos pode ser explorada e representada ao nível da linha. No entanto, se for exportada através das definições de diagnóstico, a métrica será representada como todas as mensagens recebidas em todas as filas do hub do Hub de Eventos.
>
> Para obter uma lista de métricas de plataforma exportáveis através de configurações de diagnóstico, consulte [este artigo](metrics-supported-export-diagnostic-settings.md).



## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|qpu_metric|QPU|Contagem|Média|O QPU. Intervalo 0-100 para S1, 0-200 para S2 e 0-400 para S4|ServerResourceType|
|memory_metric|Memória|Bytes|Média|A memória. Intervalo 0-25 GB para S1, 0-50 GB para S2 e 0-100 GB para S4|ServerResourceType|
|private_bytes_metric|Bytes Privados|Bytes|Média|Bytes privados.|ServerResourceType|
|virtual_bytes_metric|Bytes Virtuais|Bytes|Média|Bytes virtuais.|ServerResourceType|
|TotalConnectionRequests|Total de pedidos de ligação|Contagem|Média|Pedidos de ligação total. Estas são chegadas.|ServerResourceType|
|SuccessfullConnectionsPerSec|Conexões bem sucedidas por Sec|CountPerSecond|Média|Taxa de conclusão de ligação bem sucedida.|ServerResourceType|
|TotalConnectionFailures|Falhas totais de ligação|Contagem|Média|Total tentativas de ligação falhadas.|ServerResourceType|
|CurrentUserSessions|Sessões de utilizador atuais|Contagem|Média|Número atual de sessões de utilizador estabelecidas.|ServerResourceType|
|QueryPoolBusyThreads|Filas movimentadas da piscina de consulta|Contagem|Média|Número de fios ocupados na piscina de fios de consulta.|ServerResourceType|
|CommandPoolJobQueueLength|Comprimento da fila do trabalho da piscina de comando|Contagem|Média|Número de empregos na fila da piscina de linhas de comando.|ServerResourceType|
|ProcessingPoolJobQueueLength|Processamento de trabalho de piscina comprimento de fila|Contagem|Média|Número de empregos não-I/O na fila da piscina de fios de processamento.|ServerResourceType|
|CurrentConnections|Ligação: Ligações atuais|Contagem|Média|Número atual de ligações ao cliente estabelecidas.|ServerResourceType|
|Preço Corrente mais limpo|Memória: Preço Atual Mais Limpo|Contagem|Média|Preço atual da memória, $/byte/tempo, normalizado a 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Memória: Memória mais limpa encolhe|Bytes|Média|Quantidade de memória, em bytes, sujeito a purga pelo limpador de fundo.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memória: Memória mais limpa não encolhe|Bytes|Média|Quantidade de memória, em bytes, não sujeito a purga pelo limpador de fundo.|ServerResourceType|
|MemoryUsage|Memória: Utilização da memória|Bytes|Média|Utilização da memória do processo do servidor como utilizado no cálculo do preço de memória mais limpo. Igual a contra processo\PrivateBytes mais o tamanho dos dados mapeados pela memória, ignorando qualquer memória que tenha sido mapeada ou atribuída pelo motor de análise de memória xVelocity (VertiPaq) em excesso do limite de memória do motor xVelocity.|ServerResourceType|
|MemoryLimitHard|Memória: Limite de memória difícil|Bytes|Média|Limite de memória dura, a partir do ficheiro de configuração.|ServerResourceType|
|MemoryLimitHigh|Memória: Limite de memória alto|Bytes|Média|Limite de memória elevado, a partir do ficheiro de configuração.|ServerResourceType|
|MemoryLimitLow|Memória: Limite de memória baixo|Bytes|Média|Baixo limite de memória, a partir do ficheiro de configuração.|ServerResourceType|
|MemoryLimitVertiPaq|Memória: Limite de memória VertiPaq|Bytes|Média|Limite de memória, a partir do ficheiro de configuração.|ServerResourceType|
|Quota|Memória: Quota|Bytes|Média|Quota de memória atual, em bytes. A quota de memória também é conhecida como subsídio de memória ou reserva de memória.|ServerResourceType|
|Bloco de Quotas|Memória: Quota bloqueada|Contagem|Média|Número atual de pedidos de quota que são bloqueados até que outras quotas de memória sejam libertadas.|ServerResourceType|
|VertiPaqNonpaged|Memória: VertiPaq Nonpaged|Bytes|Média|Bytes de memória bloqueados no conjunto de trabalho para utilização pelo motor de memória.|ServerResourceType|
|VertiPaqPaged|Memória: VertiPaq Paged|Bytes|Média|Bytes de memória páginada em uso para dados na memória.|ServerResourceType|
|RowsReadPerSec|Processamento: Linhas lidas por segundo|CountPerSecond|Média|Taxa de linhas lidas a partir de todas as bases de dados relacionais.|ServerResourceType|
|RowsConvertedPerSec|Processamento: Linhas convertidas por seg|CountPerSecond|Média|Taxa de linhas convertidas durante o processamento.|ServerResourceType|
|RowsWrittenPerSec|Processamento: Linhas escritas por seg|CountPerSecond|Média|Taxa de linhas escritas durante o processamento.|ServerResourceType|
|CommandPoolBusyThreads|Threads: Comando piscina fios ocupados|Contagem|Média|Número de fios ocupados na piscina de fios de comando.|ServerResourceType|
|CommandPoolIdleThreads|Threads: Linhas ociosas da piscina de comando|Contagem|Média|Número de fios ociosos na piscina de fios de comando.|ServerResourceType|
|LongParsingBusyThreads|Threads: Longo parsing fios ocupados|Contagem|Média|Número de fios ocupados na longa piscina de fios de análise.|ServerResourceType|
|LongParsingIdleThreads|Threads: Longos fios ociosos de análise|Contagem|Média|Número de fios ociosos na longa piscina de fios de análise.|ServerResourceType|
|LongParsingJobQueueLength|Threads: Longa análise do comprimento da fila do trabalho|Contagem|Média|Número de empregos na fila da longa piscina de fios de análise.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Threads: Processing pool busy I/O job threads|Contagem|Média|Número de fios em funcionamento de empregos em I/S na piscina de fios de processamento.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Threads: Piscina de processamento movimentada fios não-I/O|Contagem|Média|Número de fios que executam trabalhos não-I/O na piscina de fios de processamento.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Threads: Processamento de piscina I/O comprimento da fila de trabalho|Contagem|Média|Número de empregos em I/S na fila da piscina de fios de processamento.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Threads: Processing pool idle I/O job threads|Contagem|Média|Número de fios ociosos para trabalhos de I/S na piscina de fios de processamento.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Threads: Processamento de fios não-I/O da piscina|Contagem|Média|Número de fios ociosos na piscina de fios de processamento dedicados a empregos não-I/O.|ServerResourceType|
|QueryPoolIdleThreads|Threads: Filas ociosas da piscina de consulta|Contagem|Média|Número de fios ociosos para trabalhos de I/S na piscina de fios de processamento.|ServerResourceType|
|QueryPoolJobQueueLength|Threads: Fila de trabalho de piscina de consulta lengt|Contagem|Média|Número de empregos na fila da piscina de fios de consulta.|ServerResourceType|
|ShortParsingBusyThreads|Threads: Curto parsing fios ocupados|Contagem|Média|Número de fios ocupados na piscina de fios de análise curta.|ServerResourceType|
|ShortParsingIdleThreads|Threads: Fios ociosos de análise curta|Contagem|Média|Número de fios ociosos na piscina de fios de análise curta.|ServerResourceType|
|ShortParsingJobQueueLength|Threads: Curto período de fila de trabalho de análise|Contagem|Média|Número de empregos na fila da piscina de fios de análise curta.|ServerResourceType|
|memory_thrashing_metric|Goleada de memória|Percentagem|Média|A memória média a bater.|ServerResourceType|
|mashup_engine_qpu_metric|M Motor QPU|Contagem|Média|Utilização de QPU por processos de monomotor de mashup|ServerResourceType|
|mashup_engine_memory_metric|Memória do Motor M|Bytes|Média|Utilização da memória por processos do motor mashup|ServerResourceType|
|mashup_engine_private_bytes_metric|M Motor Private Bytes|Bytes|Média|Bytes privados de utilização por processos de monomotor mashup.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Bytes virtuais do motor M|Bytes|Média|Bytes virtuais de utilização por processos de monomotor mashup.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Total De Pedidos|Total de pedidos de gateway (Preprecated)|Contagem|Total|Número de pedidos de gateway - Utilize métrica de pedido de várias dimensões com dimensão GatewayResponseCodeCategory|Localização,Nome de anfitrião|
|Pedidos bem sucedidos|Pedidos de gateway bem sucedidos (precatiados)|Contagem|Total|Número de pedidos de gateway bem sucedidos - Utilize métrica de pedido de várias dimensões com dimensão GatewayResponseCodeCategory|Localização,Nome de anfitrião|
|UnauthorizedRequests|Pedidos de gateway não autorizados (Preprecated)|Contagem|Total|Número de pedidos de gateway não autorizados - Utilize métrica de pedido de várias dimensões com dimensão GatewayResponseCodeCategory|Localização,Nome de anfitrião|
|FailedRequests|Pedidos de gateway falhados (precatiados)|Contagem|Total|Número de falhas nos pedidos de gateway - Utilize métrica de pedido de várias dimensões com dimensão GatewayResponseCodeCategory|Localização,Nome de anfitrião|
|Outros Pedidos|Outros pedidos de gateway (Preprecated)|Contagem|Total|Número de outros pedidos de gateway - Utilize métrica de pedido de várias dimensões com dimensão GatewayResponseCodeCategory|Localização,Nome de anfitrião|
|Duração|Duração global dos pedidos de gateway|Milissegundos|Média|Duração global dos pedidos de gateway em milissegundos|Localização,Nome de anfitrião|
|BackendDura|Duração dos pedidos de backend|Milissegundos|Média|Duração dos pedidos de backend em milissegundos|Localização,Nome de anfitrião|
|Capacidade|Capacidade|Percentagem|Média|Métrica de utilização para o serviço ApiManagement|Localização|
|EventHubTotalEvents|Eventos Totais eventhub|Contagem|Total|Número de eventos enviados para eventHub|Localização|
|EventHubSuccessfulEvents|Eventos de Sucesso EventHub|Contagem|Total|Número de eventos de sucesso eventHub|Localização|
|EventHubTotalFailedEvents|Eventos falhados do EventHub|Contagem|Total|Número de eventos falhados do EventHub|Localização|
|EventHubRejectedEvents|Eventos de EventHub rejeitados|Contagem|Total|Número de eventos rejeitados do EventHub (configuração errada ou não autorizada)|Localização|
|EventHubThrottledEvents|Eventos Throttled EventHub|Contagem|Total|Número de eventos do EventHub estrangulados|Localização|
|EventHubTimedoutEvents|Eventos Timed out EventHub|Contagem|Total|Número de eventos cronometrados eventHub|Localização|
|EventHubDroppedEvents|Eventos de EventHub abandonados|Contagem|Total|Número de eventos ignorados devido ao limite de tamanho da fila atingido|Localização|
|EventHubTotalBytesSent|Tamanho dos eventos EventHub|Bytes|Total|Tamanho total dos eventos EventHub em bytes|Localização|
|Pedidos|Pedidos|Contagem|Total|Gateway solicitar métricas com múltiplas dimensões|Localização,Nome de anfitrião,LastErrorReason,BackendResponseCode,GatewayResponseCode,BackendResponseCodeCategory,GatewayResponseCodeCategory,GatewayResponseCodeCategory|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Contagem|Contagem|Número total de pedidos de http de entrada.|Nenhum|
|Contagem de pedidos falhado|Contagem de pedidos falhado|Contagem|Contagem|Pedidos de http falhados.|Nenhum|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Contagem|Média|Latência a pedido de http.|Nenhum|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage|Percentagem de utilização do CPU do sistema|Percentagem|Média|O recente uso do CPU para todo o sistema|Nome de aplicativo,Pod|
|AppCpuUsagePercentage|Percentagem de utilização do CPU da aplicação|Percentagem|Média|Percentagem de utilização do CPU da aplicação JVM|Nome de aplicativo,Pod|
|AppMemoryCommitted|Memória de aplicativo atribuída|Bytes|Média|Memória atribuída à JVM em bytes|Nome de aplicativo,Pod|
|AppMemoryUsed|Memória de aplicativo usada|Bytes|Média|Memória de aplicativo usada em bytes|Nome de aplicativo,Pod|
|AppMemoryMax|Memória de aplicativo Max|Bytes|Máximo|A quantidade máxima de memória em bytes que podem ser usados para a gestão da memória|Nome de aplicativo,Pod|
|MaxOldGenMemoryPoolBytes|Tamanho de dados de geração antiga disponível Max|Bytes|Média|Tamanho máximo da piscina de memória de geração antiga|Nome de aplicativo,Pod|
|OldGenMemoryPoolBytes|Tamanho dos dados da geração antiga|Bytes|Média|Tamanho da piscina de memória de geração antiga após um GC completo|Nome de aplicativo,Pod|
|OldGenPromotedBytes|Promover o tamanho dos dados da geração antiga|Bytes|Máximo|Contagem de aumentos positivos no tamanho do pool de memória de geração antiga antes de GC para depois de GC|Nome de aplicativo,Pod|
|YoungGenPromotedBytes|Promover para o tamanho de dados de geração jovem|Bytes|Máximo|Incrementado para um aumento do tamanho do pool de memória de geração jovem após um GC para antes do próximo|Nome de aplicativo,Pod|
|GCPauseTotalCount|Contagem de pausas GC|Contagem|Total|Contagem de pausas GC|Nome de aplicativo,Pod|
|GCPauseTotalTime|GC Pausa Tempo Total|Milissegundos|Total|GC Pausa Tempo Total|Nome de aplicativo,Pod|
|TomcatSentBytes|Tomcat Total Bytes Enviados|Bytes|Total|Tomcat Total Bytes Enviados|Nome de aplicativo,Pod|
|TomcatReceivedBytes|Tomcat Total Recebido Bytes|Bytes|Total|Tomcat Total Recebido Bytes|Nome de aplicativo,Pod|
|TomcatRequestTotalTime|Tomcat Request Total Times|Milissegundos|Total|Tomcat Request Total Times|Nome de aplicativo,Pod|
|TomcatRequestTotalCount|Tomcat Request Contagem Total|Contagem|Total|Tomcat Request Contagem Total|Nome de aplicativo,Pod|
|TomcatResponseAvgTime|Tomcat Request Tempo Médio|Milissegundos|Média|Tomcat Request Tempo Médio|Nome de aplicativo,Pod|
|TomcatRequestMaxTime|Tomcat Request Max Time|Milissegundos|Máximo|Tomcat Request Max Time|Nome de aplicativo,Pod|
|TomcatErrorCount|Erro Global tomcat|Contagem|Total|Erro Global tomcat|Nome de aplicativo,Pod|
|TomcatSessionActiveMaxCount|Tomcat Session Max Ative Count|Contagem|Total|Tomcat Session Max Ative Count|Nome de aplicativo,Pod|
|TomcatSessionAliveMaxTime|Tomcat Session Max Alive Time|Milissegundos|Máximo|Tomcat Session Max Alive Time|Nome de aplicativo,Pod|
|TomcatSessionActiveCurrentCurrentCount|Tomcat Session Alive Count|Contagem|Total|Tomcat Session Alive Count|Nome de aplicativo,Pod|
|TomcatSessionCreatedCount|Tomcat Session Criou a Contagem|Contagem|Total|Tomcat Session Criou a Contagem|Nome de aplicativo,Pod|
|TomcatSessionExpiredCount|Contagem expirada da sessão de Tomcat|Contagem|Total|Contagem expirada da sessão de Tomcat|Nome de aplicativo,Pod|
|TomcatSessionRejectedCount|Tomcat Session Rejeitou contagem|Contagem|Total|Tomcat Session Rejeitou contagem|Nome de aplicativo,Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalJob|Empregototal|Contagem|Total|O número total de postos de trabalho|Livro de execução,Estado|
|TotalUpdateDeploymentRuns|Execuções de implementação total de atualização|Contagem|Total|Executações totais de implementação de atualização de software|Nome de configuração de software,Status|
|TotalUpdateDeploymentMachineRuns|Corridas de máquinas de implantação de atualização total|Contagem|Total|Máquina de implementação total de atualização de software executa em uma execução de implementação de atualização de software|Nome de configuração de software,Status,Targetcomputer,SoftwareUpdateGenRunid|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CoreCount|Contagem de núcleos dedicado|Contagem|Total|Número total de núcleos dedicados na conta do lote|Nenhum|
|TotalNodeCount|Contagem de nódedicados dedicado|Contagem|Total|Número total de nós dedicados na conta do lote|Nenhum|
|LowPriorityCoreCount|Contagem do núcleo de baixa prioridade|Contagem|Total|Número total de núcleos de baixa prioridade na conta do lote|Nenhum|
|TotalLowPriorityNodeCount|Contagem de nódeadebaixas|Contagem|Total|Número total de nós de baixa prioridade na conta do lote|Nenhum|
|CreatingNodeCount|Criando a Contagem do Nó|Contagem|Total|Número de nós sendo criados|Nenhum|
|StartingNodeCount|Contagem de nósinos insinuantes|Contagem|Total|Número de nós começando|Nenhum|
|WaitingForStartTaskNodeCount|Esperando para começar contagem de nó de tarefa|Contagem|Total|Número de nós à espera que a Tarefa de Início esteja concluída|Nenhum|
|StartTaskFailedNodeCount|Contagem de nófalhado de tarefa inicial|Contagem|Total|Número de nós onde a Tarefa de Início falhou|Nenhum|
|IdleNodeCount|Contagem de nóocioso ocioso|Contagem|Total|Número de nós ociosos|Nenhum|
|OfflineNodeCount|Contagem de nó offline|Contagem|Total|Número de nós offline|Nenhum|
|RebootingNodeCount|Contagem de nóreinicialização|Contagem|Total|Número de nós de reinicialização|Nenhum|
|ReimagingNodeCount|Contagem de nó de reimaging|Contagem|Total|Número de nós de reimagem|Nenhum|
|RunningNodeCount|Contagem de nósomas de execução|Contagem|Total|Número de nós de corrida|Nenhum|
|LeavingPoolNodeCount|Deixando a contagem do nó da piscina|Contagem|Total|Número de nós saindo da Piscina|Nenhum|
|UnusableNodeCount|Contagem inutilizável de nó|Contagem|Total|Número de nós inutilizáveis|Nenhum|
|PreemptedNodeCount|Contagem de nósinos preempted|Contagem|Total|Número de nós pré-empreitados|Nenhum|
|TaskStartEvent|Eventos de início de tarefa|Contagem|Total|Número total de tarefas que começaram|Nenhum|
|TaskCompleteEvent|Tarefa Completa Eventos|Contagem|Total|Número total de tarefas que completaram|Nenhum|
|TaskFailEvent|Eventos de falha de tarefa|Contagem|Total|Número total de tarefas que completaram num estado falhado|Nenhum|
|PoolCreateEvent|Piscina Criar Eventos|Contagem|Total|Número total de piscinas que foram criadas|Nenhum|
|PoolResizeStartEvent|Eventos de início de redimensionar piscina|Contagem|Total|Número total de redimensionos de piscina que começaram|Nenhum|
|PoolResizeCompleteEvent|Piscina Redimensionar eventos completos|Contagem|Total|Número total de redimensionos de piscina que tenham concluído|Nenhum|
|PoolDeleteStartEvent|Pool Delete Eventos de Início|Contagem|Total|Número total de eliminações de piscinas que começaram|Nenhum|
|PoolDeleteCompleteEvent|Pool Eliminar Eventos Completos|Contagem|Total|Número total de eliminações de piscinas que tenham concluído|Nenhum|
|JobDeleteCompleteEvent|Job Delete Complete Events|Contagem|Total|Número total de postos de trabalho que foram eliminados com êxito.|Nenhum|
|JobDeleteStartEvent|Job Delete Start Events|Contagem|Total|Número total de postos de trabalho que foram solicitados para serem suprimidos.|Nenhum|
|JobDisableCompleteEvent|Desativação de trabalho eventos completos|Contagem|Total|Número total de postos de trabalho com êxito incapacitados.|Nenhum|
|JobDisableStartEvent|Eventos de início de desativação de emprego|Contagem|Total|Número total de postos de trabalho que foram solicitados para serem desativados.|Nenhum|
|JobStartEvent|Eventos de início de trabalho|Contagem|Total|Número total de postos de trabalho que foram iniciados com sucesso.|Nenhum|
|JobTerminateCompleteEvent|Trabalho terminar eventos completos|Contagem|Total|Número total de postos de trabalho que foram encerrados com sucesso.|Nenhum|
|JobTerminateStartEvent|Trabalho terminar eventos de início|Contagem|Total|Número total de postos de trabalho que foram solicitados para serem encerrados.|Nenhum|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/espaços de trabalho

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Trabalho Submetido|Trabalho Submetido|Contagem|Total|Número de postos de trabalho apresentados|Cenário,ClusterName|
|Trabalho Concluído|Trabalho Concluído|Contagem|Total|Número de postos de trabalho concluídos|Cenário,ClusterName,Tipo de Resultados|
|Nósodes totais|Nósodes totais|Contagem|Média|Número de nós totais|Cenário,ClusterName|
|Nódosativos Ativos|Nódosativos Ativos|Contagem|Média|Número de nós de corrida|Cenário,ClusterName|
|Nódoas ociosas|Nódoas ociosas|Contagem|Média|Número de nós ociosos|Cenário,ClusterName|
|Nós Inutilizáveis|Nós Inutilizáveis|Contagem|Média|Número de nós inutilizáveis|Cenário,ClusterName|
|Nódosos pré-empreitados|Nódosos pré-empreitados|Contagem|Média|Número de nós pré-empreitados|Cenário,ClusterName|
|Deixando nódosos|Deixando nódosos|Contagem|Média|Número de nós de saída|Cenário,ClusterName|
|Núcleos Totais|Núcleos Totais|Contagem|Média|Número de núcleos totais|Cenário,ClusterName|
|Núcleos Ativos|Núcleos Ativos|Contagem|Média|Número de núcleos ativos|Cenário,ClusterName|
|Núcleos ociosos|Núcleos ociosos|Contagem|Média|Número de núcleos ociosos|Cenário,ClusterName|
|Núcleos Inutilizáveis|Núcleos Inutilizáveis|Contagem|Média|Número de núcleos inutilizáveis|Cenário,ClusterName|
|Núcleos Preempted|Núcleos Preempted|Contagem|Média|Número de núcleos pré-optados|Cenário,ClusterName|
|Deixando núcleos|Deixando núcleos|Contagem|Média|Número de núcleos de saída|Cenário,ClusterName|
|Percentagem de Utilização de Quotas|Percentagem de Utilização de Quotas|Contagem|Média|Por cento da quota utilizada|Cenário,ClusterName,VmFamilyName,VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|Percentagem de utilização do CPU|Percentagem|Máximo|Percentagem de utilização do CPU|Nó|
|MemoryUsage|Utilização de Memória|Bytes|Média|Utilização de Memória|Nó|
|MemoryLimit|Limite de memória|Bytes|Média|Limite de memória|Nó|
|MemoryUsagePercentageinDouble|Porcentagem de uso da memória|Percentagem|Média|Porcentagem de uso da memória|Nó|
|Armazenamento|Uso do armazenamento|Bytes|Média|Uso do armazenamento|Nó|
|IOReadBytes|Bytes de leitura de e/s|Bytes|Total|Bytes de leitura de e/s|Nó|
|IOWriteBytes|Bytes de gravação de e/s|Bytes|Total|Bytes de gravação de e/s|Nó|
|Conexão Aceite|Conexões aceitas|Contagem|Total|Conexões aceitas|Nó|
|Ligação Handled|Conexões manipuladas|Contagem|Total|Conexões manipuladas|Nó|
|ConexãoActive|Conexões ativas|Contagem|Média|Conexões ativas|Nó|
|RequestHandled|Solicitações manipuladas|Contagem|Total|Solicitações manipuladas|Nó|
|Blocos Processados|Blocos processados|Contagem|Total|Blocos processados|Nó|
|Transações Processadas|Transações processadas|Contagem|Total|Transações processadas|Nó|
|Transações Pendentes|Transações pendentes|Contagem|Média|Transações pendentes|Nó|
|Transações em fila|Transações em fila|Contagem|Média|Transações em fila|Nó|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|clientes conectados|Clientes Ligados|Contagem|Máximo||ShardId|
|total de comandos processados|Total de Operações|Contagem|Total||ShardId|
|cachehits|Cache Hits|Contagem|Total||ShardId|
|cachemisses|Cache Misses|Contagem|Total||ShardId|
|cachemissrate|Cache Miss Rate|Percentagem|cachemissrate||ShardId|
|obter comandos|Fica|Contagem|Total||ShardId|
|setcommands|Conjuntos|Contagem|Total||ShardId|
|operationsPerSecond|Operações por segundo|Contagem|Máximo||ShardId|
|chaves despejadas|Chaves despejadas|Contagem|Total||ShardId|
|totaldechaves|Chaves totais|Contagem|Máximo||ShardId|
|expiredkeys|Chaves expiradas|Contagem|Total||ShardId|
|memória usada|Memória Usada|Bytes|Máximo||ShardId|
|percentagem de memória usada|Percentagem de memória usada|Percentagem|Máximo||ShardId|
|usedmemoryRss|RSS de memória usada|Bytes|Máximo||ShardId|
|serverLoad|Carga do servidor|Percentagem|Máximo||ShardId|
|cacheWrite|Cache Write|BytesPerSecond|Máximo||ShardId|
|cacheRead|Leitura de cache|BytesPerSecond|Máximo||ShardId|
|percentProcessorTime|CPU|Percentagem|Máximo||ShardId|
|cacheLatency|Microsegundos de Latência cache (Pré-visualização)|Contagem|Média||ShardId|
|erros|Erros|Contagem|Máximo||ShardId,ErrorType|
|connectedclients0|Clientes Conectados (Fragmento 0)|Contagem|Máximo||Nenhum|
|total de comandos processados0|Total de Operações (Fragmento 0)|Contagem|Total||Nenhum|
|cachehits0|Cache Hits (Fragmento 0)|Contagem|Total||Nenhum|
|cachemisses0|Cache Misses (Fragmento 0)|Contagem|Total||Nenhum|
|getcommands0|Recebe (Fragmento 0)|Contagem|Total||Nenhum|
|setcommands0|Conjuntos (Fragmento 0)|Contagem|Total||Nenhum|
|operationsPerSecond0|Operações por Segundo (Fragmento 0)|Contagem|Máximo||Nenhum|
|evictedkeys0|Chaves despejadas (Fragmento 0)|Contagem|Total||Nenhum|
|totalkeys0|Chaves totais (Fragmento 0)|Contagem|Máximo||Nenhum|
|expiredkeys0|Chaves expiradas (Fragmento 0)|Contagem|Total||Nenhum|
|usedmemory0|Memória Usada (Fragmento 0)|Bytes|Máximo||Nenhum|
|usedmemoryRss0|Memória Usada RSS (Fragmento 0)|Bytes|Máximo||Nenhum|
|serverLoad0|Carga do servidor (Fragmento 0)|Percentagem|Máximo||Nenhum|
|cacheWrite0|Cache Write (Fragmento 0)|BytesPerSecond|Máximo||Nenhum|
|cacheRead0|Cache Read (Fragmento 0)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime0|CPU (Fragmento 0)|Percentagem|Máximo||Nenhum|
|connectedclients1|Clientes Conectados (Fragmento 1)|Contagem|Máximo||Nenhum|
|total de comandos processados1|Total de Operações (Fragmento 1)|Contagem|Total||Nenhum|
|cachehits1|Cache Hits (Fragmento 1)|Contagem|Total||Nenhum|
|cachemisses1|Cache Misses (Fragmento 1)|Contagem|Total||Nenhum|
|getcommands1|Recebe (Fragmento 1)|Contagem|Total||Nenhum|
|setcommands1|Conjuntos (Fragmento 1)|Contagem|Total||Nenhum|
|operationsPerSecond1|Operações por Segundo (Fragmento 1)|Contagem|Máximo||Nenhum|
|despejadoskeys1|Chaves despejadas (Fragmento 1)|Contagem|Total||Nenhum|
|totaldekeys1|Chaves totais (Fragmento 1)|Contagem|Máximo||Nenhum|
|expiredkeys1|Chaves expiradas (Fragmento 1)|Contagem|Total||Nenhum|
|memória usada1|Memória Usada (Fragmento 1)|Bytes|Máximo||Nenhum|
|usedmemoryRss1|Memória Usada RSS (Fragmento 1)|Bytes|Máximo||Nenhum|
|serverLoad1|Carga do servidor (Fragmento 1)|Percentagem|Máximo||Nenhum|
|cacheWrite1|Cache Write (Fragmento 1)|BytesPerSecond|Máximo||Nenhum|
|cacheRead1|Cache Read (Fragmento 1)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime1|CPU (Fragmento 1)|Percentagem|Máximo||Nenhum|
|clientes conectados2|Clientes Conectados (Fragmento 2)|Contagem|Máximo||Nenhum|
|total de comandos processados2|Total de Operações (Fragmento 2)|Contagem|Total||Nenhum|
|cachehits2|Cache Hits (Fragmento 2)|Contagem|Total||Nenhum|
|cachemisses2|Cache Misses (Fragmento 2)|Contagem|Total||Nenhum|
|getcommands2|Recebe (Fragmento 2)|Contagem|Total||Nenhum|
|setcommands2|Conjuntos (Fragmento 2)|Contagem|Total||Nenhum|
|operationsPerSecond2|Operações por Segundo (Fragmento 2)|Contagem|Máximo||Nenhum|
|despejadoskeys2|Chaves despejadas (Fragmento 2)|Contagem|Total||Nenhum|
|totalkeys2|Chaves totais (Fragmento 2)|Contagem|Máximo||Nenhum|
|expiredkeys2|Chaves expiradas (Fragmento 2)|Contagem|Total||Nenhum|
|memória usada2|Memória Usada (Fragmento 2)|Bytes|Máximo||Nenhum|
|usedmemoryRss2|Memória Usada RSS (Fragmento 2)|Bytes|Máximo||Nenhum|
|serverLoad2|Carga do servidor (Fragmento 2)|Percentagem|Máximo||Nenhum|
|cacheWrite2|Cache Write (Fragmento 2)|BytesPerSecond|Máximo||Nenhum|
|cacheRead2|Cache Read (Fragmento 2)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime2|CPU (Fragmento 2)|Percentagem|Máximo||Nenhum|
|connectedclients3|Clientes Conectados (Fragmento 3)|Contagem|Máximo||Nenhum|
|total de comandos processados3|Total de Operações (Fragmento 3)|Contagem|Total||Nenhum|
|cachehits3|Cache Hits (Fragmento 3)|Contagem|Total||Nenhum|
|cachemisses3|Cache Misses (Fragmento 3)|Contagem|Total||Nenhum|
|getcommands3|Recebe (Fragmento 3)|Contagem|Total||Nenhum|
|setcommands3|Conjuntos (Fragmento 3)|Contagem|Total||Nenhum|
|operationsPerSecond3|Operações por Segundo (Fragmento 3)|Contagem|Máximo||Nenhum|
|evictedkeys3|Chaves despejadas (Fragmento 3)|Contagem|Total||Nenhum|
|totalkeys3|Chaves totais (Fragmento 3)|Contagem|Máximo||Nenhum|
|expiredkeys3|Chaves expiradas (Fragmento 3)|Contagem|Total||Nenhum|
|usedmemory3|Memória Usada (Fragmento 3)|Bytes|Máximo||Nenhum|
|usedmemoryRss3|Memória Usada RSS (Fragmento 3)|Bytes|Máximo||Nenhum|
|serverLoad3|Carga do servidor (Fragmento 3)|Percentagem|Máximo||Nenhum|
|cacheWrite3|Cache Write (Fragmento 3)|BytesPerSecond|Máximo||Nenhum|
|cacheRead3|Cache Read (Fragmento 3)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime3|CPU (Fragmento 3)|Percentagem|Máximo||Nenhum|
|connectedclients4|Clientes Conectados (Fragmento 4)|Contagem|Máximo||Nenhum|
|total de comandos processados4|Total de Operações (Fragmento 4)|Contagem|Total||Nenhum|
|cachehits4|Cache Hits (Fragmento 4)|Contagem|Total||Nenhum|
|cachemisses4|Cache Misses (Fragmento 4)|Contagem|Total||Nenhum|
|getcommands4|Recebe (Fragmento 4)|Contagem|Total||Nenhum|
|setcommands4|Conjuntos (Fragmento 4)|Contagem|Total||Nenhum|
|operationsPerSecond4|Operações por Segundo (Fragmento 4)|Contagem|Máximo||Nenhum|
|evictedkeys4|Chaves despejadas (Fragmento 4)|Contagem|Total||Nenhum|
|totalkeys4|Chaves totais (Fragmento 4)|Contagem|Máximo||Nenhum|
|expiredkeys4|Chaves expiradas (Fragmento 4)|Contagem|Total||Nenhum|
|usedmemory4|Memória Usada (Fragmento 4)|Bytes|Máximo||Nenhum|
|usedmemoryRss4|Memória Usada RSS (Fragmento 4)|Bytes|Máximo||Nenhum|
|serverLoad4|Carga do servidor (Fragmento 4)|Percentagem|Máximo||Nenhum|
|cacheWrite4|Cache Write (Fragmento 4)|BytesPerSecond|Máximo||Nenhum|
|cacheRead4|Cache Read (Fragmento 4)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime4|CPU (Fragmento 4)|Percentagem|Máximo||Nenhum|
|connectedclients5|Clientes Conectados (Fragmento 5)|Contagem|Máximo||Nenhum|
|total de comandos processados5|Total de Operações (Fragmento 5)|Contagem|Total||Nenhum|
|cachehits5|Cache Hits (Fragmento 5)|Contagem|Total||Nenhum|
|cachemisses5|Cache Misses (Fragmento 5)|Contagem|Total||Nenhum|
|getcommands5|Recebe (Fragmento 5)|Contagem|Total||Nenhum|
|setcommands5|Conjuntos (Fragmento 5)|Contagem|Total||Nenhum|
|operationsPerSecond5|Operações por Segundo (Fragmento 5)|Contagem|Máximo||Nenhum|
|evictedkeys5|Chaves despejadas (Fragmento 5)|Contagem|Total||Nenhum|
|totalkeys5|Chaves totais (Fragmento 5)|Contagem|Máximo||Nenhum|
|expiredkeys5|Chaves expiradas (Fragmento 5)|Contagem|Total||Nenhum|
|memória usada5|Memória Usada (Fragmento 5)|Bytes|Máximo||Nenhum|
|usedmemoryRss5|Memória Usada RSS (Fragmento 5)|Bytes|Máximo||Nenhum|
|serverLoad5|Carga do servidor (Fragmento 5)|Percentagem|Máximo||Nenhum|
|cacheWrite5|Cache Write (Fragmento 5)|BytesPerSecond|Máximo||Nenhum|
|cacheRead5|Cache Read (Fragmento 5)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime5|CPU (Fragmento 5)|Percentagem|Máximo||Nenhum|
|connectedclients6|Clientes Conectados (Fragmento 6)|Contagem|Máximo||Nenhum|
|total de comandos processados6|Total de Operações (Fragmento 6)|Contagem|Total||Nenhum|
|cachehits6|Cache Hits (Fragmento 6)|Contagem|Total||Nenhum|
|cachemisses6|Cache Misses (Fragmento 6)|Contagem|Total||Nenhum|
|getcommands6|Recebe (Fragmento 6)|Contagem|Total||Nenhum|
|setcommands6|Conjuntos (Fragmento 6)|Contagem|Total||Nenhum|
|operationsPerSecond6|Operações por Segundo (Fragmento 6)|Contagem|Máximo||Nenhum|
|evictedkeys6|Chaves despejadas (Fragmento 6)|Contagem|Total||Nenhum|
|totalkeys6|Chaves totais (Fragmento 6)|Contagem|Máximo||Nenhum|
|expiredkeys6|Chaves expiradas (Fragmento 6)|Contagem|Total||Nenhum|
|usedmemory6|Memória Usada (Fragmento 6)|Bytes|Máximo||Nenhum|
|usedmemoryRss6|Memória Usada RSS (Fragmento 6)|Bytes|Máximo||Nenhum|
|serverLoad6|Carga do servidor (Fragmento 6)|Percentagem|Máximo||Nenhum|
|cacheWrite6|Cache Write (Fragmento 6)|BytesPerSecond|Máximo||Nenhum|
|cacheRead6|Cache Read (Fragmento 6)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime6|CPU (Fragmento 6)|Percentagem|Máximo||Nenhum|
|clientes conectados7|Clientes Conectados (Shard 7)|Contagem|Máximo||Nenhum|
|total de comandos processados7|Total de Operações (Fragmento 7)|Contagem|Total||Nenhum|
|cachehits7|Cache Hits (Fragmento 7)|Contagem|Total||Nenhum|
|cachemisses7|Cache Misses (Fragmento 7)|Contagem|Total||Nenhum|
|getcommands7|Recebe (Fragmento 7)|Contagem|Total||Nenhum|
|setcommands7|Conjuntos (Fragmento 7)|Contagem|Total||Nenhum|
|operationsPerSecond7|Operações por Segundo (Fragmento 7)|Contagem|Máximo||Nenhum|
|evictedkeys7|Chaves despejadas (Fragmento 7)|Contagem|Total||Nenhum|
|totalkeys7|Chaves totais (Fragmento 7)|Contagem|Máximo||Nenhum|
|expiredkeys7|Chaves expiradas (Fragmento 7)|Contagem|Total||Nenhum|
|usedmemory7|Memória Usada (Fragmento 7)|Bytes|Máximo||Nenhum|
|usedmemoryRss7|Memória Usada RSS (Fragmento 7)|Bytes|Máximo||Nenhum|
|serverLoad7|Carga do servidor (Fragmento 7)|Percentagem|Máximo||Nenhum|
|cacheWrite7|Cache Write (Fragmento 7)|BytesPerSecond|Máximo||Nenhum|
|cacheRead7|Cache Read (Fragmento 7)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime7|CPU (Fragmento 7)|Percentagem|Máximo||Nenhum|
|connectedclients8|Clientes Conectados (Fragmento 8)|Contagem|Máximo||Nenhum|
|total de comandos processados8|Total de Operações (Fragmento 8)|Contagem|Total||Nenhum|
|cachehits8|Cache Hits (Fragmento 8)|Contagem|Total||Nenhum|
|cachemisses8|Cache Misses (Fragmento 8)|Contagem|Total||Nenhum|
|getcommands8|Recebe (Fragmento 8)|Contagem|Total||Nenhum|
|setcommands8|Conjuntos (Fragmento 8)|Contagem|Total||Nenhum|
|operationsPerSecond8|Operações por Segundo (Fragmento 8)|Contagem|Máximo||Nenhum|
|evictedkeys8|Chaves despejadas (Fragmento 8)|Contagem|Total||Nenhum|
|totalkeys8|Chaves totais (Fragmento 8)|Contagem|Máximo||Nenhum|
|expiredkeys8|Chaves expiradas (Fragmento 8)|Contagem|Total||Nenhum|
|usedmemory8|Memória Usada (Fragmento 8)|Bytes|Máximo||Nenhum|
|usedmemoryRss8|Memória Usada RSS (Fragmento 8)|Bytes|Máximo||Nenhum|
|serverLoad8|Carga do servidor (Fragmento 8)|Percentagem|Máximo||Nenhum|
|cacheWrite8|Cache Write (Fragmento 8)|BytesPerSecond|Máximo||Nenhum|
|cacheRead8|Cache Read (Fragmento 8)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime8|CPU (Fragmento 8)|Percentagem|Máximo||Nenhum|
|clientes conectados9|Clientes Conectados (Shard 9)|Contagem|Máximo||Nenhum|
|total de comandos processados9|Total de Operações (Fragmento 9)|Contagem|Total||Nenhum|
|cachehits9|Cache Hits (Fragmento 9)|Contagem|Total||Nenhum|
|cachemisses9|Cache Misses (Fragmento 9)|Contagem|Total||Nenhum|
|getcommands9|Recebe (Fragmento 9)|Contagem|Total||Nenhum|
|setcommands9|Conjuntos (Fragmento 9)|Contagem|Total||Nenhum|
|operationsPerSecond9|Operações por Segundo (Fragmento 9)|Contagem|Máximo||Nenhum|
|despejadokeys9|Chaves despejadas (Fragmento 9)|Contagem|Total||Nenhum|
|totalkeys9|Chaves totais (Fragmento 9)|Contagem|Máximo||Nenhum|
|expiredkeys9|Chaves expiradas (Fragmento 9)|Contagem|Total||Nenhum|
|memória usada9|Memória Usada (Fragmento 9)|Bytes|Máximo||Nenhum|
|usedmemoryRss9|Memória Usada RSS (Fragmento 9)|Bytes|Máximo||Nenhum|
|serverLoad9|Carga do servidor (Fragmento 9)|Percentagem|Máximo||Nenhum|
|cacheWrite9|Cache Write (Fragmento 9)|BytesPerSecond|Máximo||Nenhum|
|cacheRead9|Cache Read (Fragmento 9)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime9|CPU (Fragmento 9)|Percentagem|Máximo||Nenhum|



## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationapplicationfirewallpolicies

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Contagem de pedidos de firewall de aplicação web|Contagem|Total|O número de pedidos de clientes processados pela Firewall de Aplicação Web|Nome de política,Nome de Regras,Ação|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades computacionais atribuídas que estão atualmente a ser utilizadas pela(s Máquinas Virtuais).|Nenhum|
|Entrada na Rede|Entrada na Rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela(s) Virtual Machine(s) (Tráfego De Entrada).|Nenhum|
|Saída da Rede|Saída da Rede|Bytes|Total|O número de bytes em todas as interfaces de rede pelas Máquinas Virtuais (Tráfego De saída).|Nenhum|
|Discos Ler Bytes/Sec|Leitura do disco|BytesPerSecond|Média|Bytes médios lidos a partir de disco durante o período de monitorização.|Nenhum|
|Discos Write Bytes/Sec|Escrita de Disco|BytesPerSecond|Média|Bytes médios escritos ao disco durante o período de monitorização.|Nenhum|
|Operações de leitura do disco/sec|Operações de leitura do disco/sec|CountPerSecond|Média|Disco leia IOPS.|Nenhum|
|Operações de escrita de disco/sec|Operações de escrita de disco/sec|CountPerSecond|Média|Disco escrever IOPS.|Nenhum|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades computacionais atribuídas que estão atualmente a ser utilizadas pela(s Máquinas Virtuais).|RoleInstanceId|
|Entrada na Rede|Entrada na Rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela(s) Virtual Machine(s) (Tráfego De Entrada).|RoleInstanceId|
|Saída da Rede|Saída da Rede|Bytes|Total|O número de bytes em todas as interfaces de rede pelas Máquinas Virtuais (Tráfego De saída).|RoleInstanceId|
|Discos Ler Bytes/Sec|Leitura do disco|BytesPerSecond|Média|Bytes médios lidos a partir de disco durante o período de monitorização.|RoleInstanceId|
|Discos Write Bytes/Sec|Escrita de Disco|BytesPerSecond|Média|Bytes médios escritos ao disco durante o período de monitorização.|RoleInstanceId|
|Operações de leitura do disco/sec|Operações de leitura do disco/sec|CountPerSecond|Média|Disco leia IOPS.|RoleInstanceId|
|Operações de escrita de disco/sec|Operações de escrita de disco/sec|CountPerSecond|Média|Disco escrever IOPS.|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|UsedCapacity|Capacidade utilizada|Bytes|Média|Capacidade da conta utilizada|Nenhum|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|Tipo de resposta,GeoType,Nome ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência usada pelo Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência final dos pedidos bem sucedidos feitos a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BlobCapacity|Capacidade do Blob|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Blob a conta de armazenamento, em bytes.|BlobType,Tier|
|BlobCount|Contagem de Blobs|Contagem|Média|O número de blobs no serviço Blob da conta de armazenamento.|BlobType,Tier|
|ContainerCount|Contagem do Contentor de Blobs|Contagem|Média|O número de contentores no serviço Blob da conta de armazenamento.|Nenhum|
|IndexCapacity|Capacidade indexada|Bytes|Média|A quantidade de armazenamento utilizada pelo Índice ADLS Gen2 (Hierárquico) em bytes.|Nenhum|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|Tipo de resposta,GeoType,Nome ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência usada pelo Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência final dos pedidos bem sucedidos feitos a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TableCapacity|Capacidade de Tabelas|Bytes|Média|A quantidade de armazenamento utilizado pelo serviço Tabela da conta de armazenamento, em bytes.|Nenhum|
|TableCount|Contagem de Tabelas|Contagem|Média|O número de tabelas no serviço Tabela da conta de armazenamento.|Nenhum|
|TableEntityCount|Contagem de Entidade de Tabelas|Contagem|Média|O número de entidades de tabelas no serviço Tabela da conta de armazenamento.|Nenhum|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|Tipo de resposta,GeoType,Nome ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência usada pelo Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência final dos pedidos bem sucedidos feitos a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Capacidade de Arquivo|Capacidade de arquivo|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço de arquivos da conta de armazenamento em bytes.|FileShare|
|FileCount|Contagem de Ficheiros|Contagem|Média|O número de ficheiros no serviço de ficheiros da conta de armazenamento.|FileShare|
|FileShareCount|Contagem de partilha de ficheiros|Contagem|Média|O número de ações de ficheiros no serviço de ficheiros da conta de armazenamento.|Nenhum|
|FileShareSnapshotCount|Contagem de instantâneos de partilha de ficheiros|Contagem|Média|O número de instantâneos presentes na parte no Serviço de Ficheiros da conta de armazenamento.|FileShare|
|FileShareSnapshotSize|Tamanho instantâneo de partilha de ficheiros|Bytes|Média|A quantidade de armazenamento utilizada pelos instantâneos no serviço de ficheiros da conta de armazenamento em bytes.|FileShare|
|FileShareQuota|Tamanho da quota de partilha de ficheiros|Bytes|Média|O limite superior da quantidade de armazenamento que pode ser usado pelo Serviço de Ficheiros Azure em bytes.|FileShare|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|Tipo de resposta,GeoType,ApiName,Autenticação,FileShare|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação,FileShare|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação,FileShare|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência usada pelo Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType,ApiName,Autenticação,FileShare|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência final dos pedidos bem sucedidos feitos a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação,FileShare|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação,FileShare|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueueCapacity|Capacidade de Fila|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Fila a conta de armazenamento, em bytes.|Nenhum|
|QueueCount|Contagem de Filas|Contagem|Média|O número de filas no serviço Fila da conta de armazenamento.|Nenhum|
|QueueMessageCount|Contagem de Mensagens em Fila|Contagem|Média|O número aproximado de mensagens em fila no serviço Fila da conta de armazenamento.|Nenhum|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|Tipo de resposta,GeoType,Nome ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência usada pelo Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência final dos pedidos bem sucedidos feitos a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Totalcall|Total de Chamadas|Contagem|Total|Número total de chamadas.|Nome ApiName,OperaçãoNome,Região|
|Chamadas bem sucedidas|Chamadas bem sucedidas|Contagem|Total|Número de chamadas bem sucedidas.|Nome ApiName,OperaçãoNome,Região|
|TotalErrors|Total de Erros|Contagem|Total|Número total de chamadas com resposta a erros (código de resposta HTTP 4xx ou 5xx).|Nome ApiName,OperaçãoNome,Região|
|BlockedCalls|Chamadas Bloqueadas|Contagem|Total|Número de chamadas que excederam a taxa ou o limite de quota.|Nome ApiName,OperaçãoNome,Região|
|Erros de servidor|Erros do servidor|Contagem|Total|Número de chamadas com erro interno de serviço (código de resposta HTTP 5xx).|Nome ApiName,OperaçãoNome,Região|
|Erros de cliente|Erros do Cliente|Contagem|Total|Número de chamadas com erro do lado do cliente (código de resposta HTTP 4xx).|Nome ApiName,OperaçãoNome,Região|
|DataIn|Dados em|Bytes|Total|Tamanho dos dados de entrada em bytes.|Nome ApiName,OperaçãoNome,Região|
|DataOut|Dados out|Bytes|Total|Tamanho dos dados de saída em bytes.|Nome ApiName,OperaçãoNome,Região|
|Latência|Latência|MilliSeconds|Média|Latência em milissegundos.|Nome ApiName,OperaçãoNome,Região|
|CharactersTranslated|Caracteres traduzidos|Contagem|Total|Número total de caracteres no pedido de texto.|Nome ApiName,OperaçãoNome,Região|
|Caracteres Treinados|Personagens treinados|Contagem|Total|Número total de caracteres treinados.|Nome ApiName,OperaçãoNome,Região|
|SpeechSessionDuration|Duração da sessão de discurso|Segundos|Total|Duração total da sessão de discurso em segundos.|Nome ApiName,OperaçãoNome,Região|
|Total de Transações|Total de Transações|Contagem|Total|Número total de transações.|Nenhum|
|TotalTokenCalls|Total de chamadas simbólicas|Contagem|Total|Número total de chamadas simbólicas.|Nome ApiName,OperaçãoNome,Região|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades do computador alocadas que estão atualmente em utilização pela Máquina Virtual ou Máquinas Virtuais|Nenhum|
|Entrada na Rede|Rede Em Billable (Depreciado)|Bytes|Total|O número de bytes faturados recebidos em todas as interfaces de rede pelas Máquinas Virtuais (Tráfego De entrada) (Deprecated)|Nenhum|
|Saída da Rede|Network out Billable (Depreciado)|Bytes|Total|O número de bytes faturados em todas as interfaces de rede pelas Máquinas Virtuais (Tráfego Desaída) (Deprecated)|Nenhum|
|Discos Ler Bytes|Discos Ler Bytes|Bytes|Total|Bytes lidos a partir de disco durante o período de monitorização|Nenhum|
|Bytes de Escrita de Disco|Bytes de Escrita de Disco|Bytes|Total|Bytes escritos ao disco durante o período de monitorização|Nenhum|
|Operações de leitura do disco/sec|Operações de leitura do disco/sec|CountPerSecond|Média|IOPS de leitura de disco|Nenhum|
|Operações de escrita de disco/sec|Operações de escrita de disco/sec|CountPerSecond|Média|IOPS de escrita de disco|Nenhum|
|Créditos cpu restantes|Créditos cpu restantes|Contagem|Média|Número total de créditos disponíveis para rebentar|Nenhum|
|Créditos da CPU consumidos|Créditos da CPU consumidos|Contagem|Média|Número total de créditos consumidos pela Máquina Virtual|Nenhum|
|Por Disco Ler Bytes/seg|Data Disk Read Bytes/Sec (Depreciado)|CountPerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|SlotId|
|Por Disco Escreva Bytes/seg|Data Disk Write Bytes/Sec (Depreciado)|CountPerSecond|Média|Bytes/Sec escrito sumo durante o período de monitorização|SlotId|
|Por disco ler operações/sec|Operações de leitura de disco de dados/Sec (Depreciado)|CountPerSecond|Média|Leia iOPS a partir de um único disco durante o período de monitorização|SlotId|
|Por Operações de Escrita de Disco/Sec|Operações de Escrita de Disco de Dados/Sec (Depreciado)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|SlotId|
|Por Disco QD|QD do disco de dados (depreciado)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|SlotId|
|OS Per Disk Ler Bytes/seg|Os Discos Ler Bytes/Sec (Depreciado)|CountPerSecond|Média|Bytes/Sec lido saem de um único disco durante o período de monitorização do disco OS|Nenhum|
|OS Por Disco Escreva Bytes/seg|Os Disk Write Bytes/Sec (Depreciado)|CountPerSecond|Média|Bytes/Sec escrito a um único disco durante o período de monitorização do disco OS|Nenhum|
|OS por disco ler operações/sec|Operações de leitura de disco osso/sec (depreciado)|CountPerSecond|Média|Leia iOPS a partir de um único disco durante o período de monitorização do disco OS|Nenhum|
|As operações de escrita de OS por disco/sec|Operações de Escrita de Disco OS/Sec (Depreciado)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco OS|Nenhum|
|OS Por Disco QD|QD do disco osso (depreciado)|Contagem|Média|Profundidade de fila de disco osso (ou comprimento da fila)|Nenhum|
|Data Disk Ler Bytes/seg|Data Disk Read Bytes/Sec (Pré-visualização)|CountPerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|LUN|
|Data Disk Write Bytes/seg|Data Disk Write Bytes/Sec (Pré-visualização)|CountPerSecond|Média|Bytes/Sec escrito sumo durante o período de monitorização|LUN|
|Operações de leitura de disco de dados/sec|Data Disk Ler Operações/Sec (Pré-visualização)|CountPerSecond|Média|Leia iOPS a partir de um único disco durante o período de monitorização|LUN|
|Operações de escrita de disco de dados/sec|Operações de escrita de disco de dados/sec (pré-visualização)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|LUN|
|Profundidade da fila do disco de dados|Profundidade da fila do disco de dados (pré-visualização)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|LUN|
|Os Discos Ler Bytes/seg|Os Discos Ler Bytes/Sec (Pré-visualização)|CountPerSecond|Média|Bytes/Sec lido saem de um único disco durante o período de monitorização do disco OS|Nenhum|
|Os Disk Write Bytes/seg|Os Disk Write Bytes/Sec (Pré-visualização)|CountPerSecond|Média|Bytes/Sec escrito a um único disco durante o período de monitorização do disco OS|Nenhum|
|Operações de leitura de disco osso/sec|As operações/seg de leitura do disco OS (pré-visualização)|CountPerSecond|Média|Leia iOPS a partir de um único disco durante o período de monitorização do disco OS|Nenhum|
|Operações de escrita de disco osso/sec|Operações de escrita de disco osso/sec (pré-visualização)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco OS|Nenhum|
|Profundidade da fila do disco osso|Profundidade da fila do disco osso (pré-visualização)|Contagem|Média|Profundidade de fila de disco osso (ou comprimento da fila)|Nenhum|
|Fluxos de entrada|Fluxos de entrada|Contagem|Média|Os fluxos de entrada são o número de fluxos correntes na direção de entrada (tráfego que vai para o VM)|Nenhum|
|Fluxos de saída|Fluxos de saída|Contagem|Média|Os fluxos de saída são o número de fluxos correntes na direção de saída (tráfego a sair do VM)|Nenhum|
|Taxa máxima de criação dos fluxos de entrada|Taxa máxima de criação dos fluxos de entrada (pré-visualização)|CountPerSecond|Média|A taxa máxima de criação dos fluxos de entrada (tráfego que vai para o VM)|Nenhum|
|Taxa máxima de criação dos fluxos de saída|Taxa máxima de criação dos fluxos de saída (pré-visualização)|CountPerSecond|Média|A taxa máxima de criação dos fluxos de saída (tráfego saindo do VM)|Nenhum|
|Premium Data Disk Cache Ler Sucesso|Vídeo de cache de disco premium (pré-visualização)|Percentagem|Média|Premium Data Disk Cache Ler Sucesso|LUN|
|Cache de disco de dados premium ler miss|Cache de disco de dados premium Leia Miss (Pré-visualização)|Percentagem|Média|Cache de disco de dados premium ler miss|LUN|
|Premium OS Disk Cache Ler Sucesso|Premium OS Cache Cache Read Hit (Pré-visualização)|Percentagem|Média|Premium OS Disk Cache Ler Sucesso|Nenhum|
|Cache de disco premium DO|Cache de disco premium OS Leia Miss (Pré-visualização)|Percentagem|Média|Cache de disco premium DO|Nenhum|
|Rede no Total|Rede no Total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela(s) Virtual Machine(s) (Tráfego De Entrada)|Nenhum|
|Rede out Total|Rede out Total|Bytes|Total|O número de bytes em todas as interfaces de rede pelas Máquinas Virtuais (Tráfego De saída)|Nenhum|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades do computador alocadas que estão atualmente em utilização pela Máquina Virtual ou Máquinas Virtuais|VMName|
|Entrada na Rede|Rede Em Billable (Depreciado)|Bytes|Total|O número de bytes faturados recebidos em todas as interfaces de rede pelas Máquinas Virtuais (Tráfego De entrada) (Deprecated)|VMName|
|Saída da Rede|Network out Billable (Depreciado)|Bytes|Total|O número de bytes faturados em todas as interfaces de rede pelas Máquinas Virtuais (Tráfego Desaída) (Deprecated)|VMName|
|Discos Ler Bytes|Discos Ler Bytes|Bytes|Total|Bytes lidos a partir de disco durante o período de monitorização|VMName|
|Bytes de Escrita de Disco|Bytes de Escrita de Disco|Bytes|Total|Bytes escritos ao disco durante o período de monitorização|VMName|
|Operações de leitura do disco/sec|Operações de leitura do disco/sec|CountPerSecond|Média|IOPS de leitura de disco|VMName|
|Operações de escrita de disco/sec|Operações de escrita de disco/sec|CountPerSecond|Média|IOPS de escrita de disco|VMName|
|Créditos cpu restantes|Créditos cpu restantes|Contagem|Média|Número total de créditos disponíveis para rebentar|Nenhum|
|Créditos da CPU consumidos|Créditos da CPU consumidos|Contagem|Média|Número total de créditos consumidos pela Máquina Virtual|Nenhum|
|Por Disco Ler Bytes/seg|Data Disk Read Bytes/Sec (Depreciado)|CountPerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|SlotId|
|Por Disco Escreva Bytes/seg|Data Disk Write Bytes/Sec (Depreciado)|CountPerSecond|Média|Bytes/Sec escrito sumo durante o período de monitorização|SlotId|
|Por disco ler operações/sec|Operações de leitura de disco de dados/Sec (Depreciado)|CountPerSecond|Média|Leia iOPS a partir de um único disco durante o período de monitorização|SlotId|
|Por Operações de Escrita de Disco/Sec|Operações de Escrita de Disco de Dados/Sec (Depreciado)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|SlotId|
|Por Disco QD|QD do disco de dados (depreciado)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|SlotId|
|OS Per Disk Ler Bytes/seg|Os Discos Ler Bytes/Sec (Depreciado)|CountPerSecond|Média|Bytes/Sec lido saem de um único disco durante o período de monitorização do disco OS|Nenhum|
|OS Por Disco Escreva Bytes/seg|Os Disk Write Bytes/Sec (Depreciado)|CountPerSecond|Média|Bytes/Sec escrito a um único disco durante o período de monitorização do disco OS|Nenhum|
|OS por disco ler operações/sec|Operações de leitura de disco osso/sec (depreciado)|CountPerSecond|Média|Leia iOPS a partir de um único disco durante o período de monitorização do disco OS|Nenhum|
|As operações de escrita de OS por disco/sec|Operações de Escrita de Disco OS/Sec (Depreciado)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco OS|Nenhum|
|OS Por Disco QD|QD do disco osso (depreciado)|Contagem|Média|Profundidade de fila de disco osso (ou comprimento da fila)|Nenhum|
|Data Disk Ler Bytes/seg|Data Disk Read Bytes/Sec (Pré-visualização)|CountPerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|LUN,VMName|
|Data Disk Write Bytes/seg|Data Disk Write Bytes/Sec (Pré-visualização)|CountPerSecond|Média|Bytes/Sec escrito sumo durante o período de monitorização|LUN,VMName|
|Operações de leitura de disco de dados/sec|Data Disk Ler Operações/Sec (Pré-visualização)|CountPerSecond|Média|Leia iOPS a partir de um único disco durante o período de monitorização|LUN,VMName|
|Operações de escrita de disco de dados/sec|Operações de escrita de disco de dados/sec (pré-visualização)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|LUN,VMName|
|Profundidade da fila do disco de dados|Profundidade da fila do disco de dados (pré-visualização)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|LUN,VMName|
|Os Discos Ler Bytes/seg|Os Discos Ler Bytes/Sec (Pré-visualização)|CountPerSecond|Média|Bytes/Sec lido saem de um único disco durante o período de monitorização do disco OS|VMName|
|Os Disk Write Bytes/seg|Os Disk Write Bytes/Sec (Pré-visualização)|CountPerSecond|Média|Bytes/Sec escrito a um único disco durante o período de monitorização do disco OS|VMName|
|Operações de leitura de disco osso/sec|As operações/seg de leitura do disco OS (pré-visualização)|CountPerSecond|Média|Leia iOPS a partir de um único disco durante o período de monitorização do disco OS|VMName|
|Operações de escrita de disco osso/sec|Operações de escrita de disco osso/sec (pré-visualização)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco OS|VMName|
|Profundidade da fila do disco osso|Profundidade da fila do disco osso (pré-visualização)|Contagem|Média|Profundidade de fila de disco osso (ou comprimento da fila)|VMName|
|Fluxos de entrada|Fluxos de entrada|Contagem|Média|Os fluxos de entrada são o número de fluxos correntes na direção de entrada (tráfego que vai para o VM)|VMName|
|Fluxos de saída|Fluxos de saída|Contagem|Média|Os fluxos de saída são o número de fluxos correntes na direção de saída (tráfego a sair do VM)|VMName|
|Taxa máxima de criação dos fluxos de entrada|Taxa máxima de criação dos fluxos de entrada (pré-visualização)|CountPerSecond|Média|A taxa máxima de criação dos fluxos de entrada (tráfego que vai para o VM)|VMName|
|Taxa máxima de criação dos fluxos de saída|Taxa máxima de criação dos fluxos de saída (pré-visualização)|CountPerSecond|Média|A taxa máxima de criação dos fluxos de saída (tráfego saindo do VM)|VMName|
|Premium Data Disk Cache Ler Sucesso|Vídeo de cache de disco premium (pré-visualização)|Percentagem|Média|Premium Data Disk Cache Ler Sucesso|LUN,VMName|
|Cache de disco de dados premium ler miss|Cache de disco de dados premium Leia Miss (Pré-visualização)|Percentagem|Média|Cache de disco de dados premium ler miss|LUN,VMName|
|Premium OS Disk Cache Ler Sucesso|Premium OS Cache Cache Read Hit (Pré-visualização)|Percentagem|Média|Premium OS Disk Cache Ler Sucesso|VMName|
|Cache de disco premium DO|Cache de disco premium OS Leia Miss (Pré-visualização)|Percentagem|Média|Cache de disco premium DO|VMName|
|Rede no Total|Rede no Total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela(s) Virtual Machine(s) (Tráfego De Entrada)|VMName|
|Rede out Total|Rede out Total|Bytes|Total|O número de bytes em todas as interfaces de rede pelas Máquinas Virtuais (Tráfego De saída)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades do computador alocadas que estão atualmente em utilização pela Máquina Virtual ou Máquinas Virtuais|Nenhum|
|Entrada na Rede|Rede Em Billable (Depreciado)|Bytes|Total|O número de bytes faturados recebidos em todas as interfaces de rede pelas Máquinas Virtuais (Tráfego De entrada) (Deprecated)|Nenhum|
|Saída da Rede|Network out Billable (Depreciado)|Bytes|Total|O número de bytes faturados em todas as interfaces de rede pelas Máquinas Virtuais (Tráfego Desaída) (Deprecated)|Nenhum|
|Discos Ler Bytes|Discos Ler Bytes|Bytes|Total|Bytes lidos a partir de disco durante o período de monitorização|Nenhum|
|Bytes de Escrita de Disco|Bytes de Escrita de Disco|Bytes|Total|Bytes escritos ao disco durante o período de monitorização|Nenhum|
|Operações de leitura do disco/sec|Operações de leitura do disco/sec|CountPerSecond|Média|IOPS de leitura de disco|Nenhum|
|Operações de escrita de disco/sec|Operações de escrita de disco/sec|CountPerSecond|Média|IOPS de escrita de disco|Nenhum|
|Créditos cpu restantes|Créditos cpu restantes|Contagem|Média|Número total de créditos disponíveis para rebentar|Nenhum|
|Créditos da CPU consumidos|Créditos da CPU consumidos|Contagem|Média|Número total de créditos consumidos pela Máquina Virtual|Nenhum|
|Por Disco Ler Bytes/seg|Data Disk Read Bytes/Sec (Depreciado)|CountPerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|SlotId|
|Por Disco Escreva Bytes/seg|Data Disk Write Bytes/Sec (Depreciado)|CountPerSecond|Média|Bytes/Sec escrito sumo durante o período de monitorização|SlotId|
|Por disco ler operações/sec|Operações de leitura de disco de dados/Sec (Depreciado)|CountPerSecond|Média|Leia iOPS a partir de um único disco durante o período de monitorização|SlotId|
|Por Operações de Escrita de Disco/Sec|Operações de Escrita de Disco de Dados/Sec (Depreciado)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|SlotId|
|Por Disco QD|QD do disco de dados (depreciado)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|SlotId|
|OS Per Disk Ler Bytes/seg|Os Discos Ler Bytes/Sec (Depreciado)|CountPerSecond|Média|Bytes/Sec lido saem de um único disco durante o período de monitorização do disco OS|Nenhum|
|OS Por Disco Escreva Bytes/seg|Os Disk Write Bytes/Sec (Depreciado)|CountPerSecond|Média|Bytes/Sec escrito a um único disco durante o período de monitorização do disco OS|Nenhum|
|OS por disco ler operações/sec|Operações de leitura de disco osso/sec (depreciado)|CountPerSecond|Média|Leia iOPS a partir de um único disco durante o período de monitorização do disco OS|Nenhum|
|As operações de escrita de OS por disco/sec|Operações de Escrita de Disco OS/Sec (Depreciado)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco OS|Nenhum|
|OS Por Disco QD|QD do disco osso (depreciado)|Contagem|Média|Profundidade de fila de disco osso (ou comprimento da fila)|Nenhum|
|Data Disk Ler Bytes/seg|Data Disk Read Bytes/Sec (Pré-visualização)|CountPerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|LUN|
|Data Disk Write Bytes/seg|Data Disk Write Bytes/Sec (Pré-visualização)|CountPerSecond|Média|Bytes/Sec escrito sumo durante o período de monitorização|LUN|
|Operações de leitura de disco de dados/sec|Data Disk Ler Operações/Sec (Pré-visualização)|CountPerSecond|Média|Leia iOPS a partir de um único disco durante o período de monitorização|LUN|
|Operações de escrita de disco de dados/sec|Operações de escrita de disco de dados/sec (pré-visualização)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|LUN|
|Profundidade da fila do disco de dados|Profundidade da fila do disco de dados (pré-visualização)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|LUN|
|Os Discos Ler Bytes/seg|Os Discos Ler Bytes/Sec (Pré-visualização)|CountPerSecond|Média|Bytes/Sec lido saem de um único disco durante o período de monitorização do disco OS|Nenhum|
|Os Disk Write Bytes/seg|Os Disk Write Bytes/Sec (Pré-visualização)|CountPerSecond|Média|Bytes/Sec escrito a um único disco durante o período de monitorização do disco OS|Nenhum|
|Operações de leitura de disco osso/sec|As operações/seg de leitura do disco OS (pré-visualização)|CountPerSecond|Média|Leia iOPS a partir de um único disco durante o período de monitorização do disco OS|Nenhum|
|Operações de escrita de disco osso/sec|Operações de escrita de disco osso/sec (pré-visualização)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco OS|Nenhum|
|Profundidade da fila do disco osso|Profundidade da fila do disco osso (pré-visualização)|Contagem|Média|Profundidade de fila de disco osso (ou comprimento da fila)|Nenhum|
|Fluxos de entrada|Fluxos de entrada|Contagem|Média|Os fluxos de entrada são o número de fluxos correntes na direção de entrada (tráfego que vai para o VM)|Nenhum|
|Fluxos de saída|Fluxos de saída|Contagem|Média|Os fluxos de saída são o número de fluxos correntes na direção de saída (tráfego a sair do VM)|Nenhum|
|Taxa máxima de criação dos fluxos de entrada|Taxa máxima de criação dos fluxos de entrada (pré-visualização)|CountPerSecond|Média|A taxa máxima de criação dos fluxos de entrada (tráfego que vai para o VM)|Nenhum|
|Taxa máxima de criação dos fluxos de saída|Taxa máxima de criação dos fluxos de saída (pré-visualização)|CountPerSecond|Média|A taxa máxima de criação dos fluxos de saída (tráfego saindo do VM)|Nenhum|
|Premium Data Disk Cache Ler Sucesso|Vídeo de cache de disco premium (pré-visualização)|Percentagem|Média|Premium Data Disk Cache Ler Sucesso|LUN|
|Cache de disco de dados premium ler miss|Cache de disco de dados premium Leia Miss (Pré-visualização)|Percentagem|Média|Cache de disco de dados premium ler miss|LUN|
|Premium OS Disk Cache Ler Sucesso|Premium OS Cache Cache Read Hit (Pré-visualização)|Percentagem|Média|Premium OS Disk Cache Ler Sucesso|Nenhum|
|Cache de disco premium DO|Cache de disco premium OS Leia Miss (Pré-visualização)|Percentagem|Média|Cache de disco premium DO|Nenhum|
|Rede no Total|Rede no Total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela(s) Virtual Machine(s) (Tráfego De Entrada)|Nenhum|
|Rede out Total|Rede out Total|Bytes|Total|O número de bytes em todas as interfaces de rede pelas Máquinas Virtuais (Tráfego De saída)|Nenhum|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuUsage|Utilização do CPU|Contagem|Média|Utilização de CPU em todos os núcleos em milislcores.|containerName|
|MemoryUsage|Utilização de Memória|Bytes|Média|Uso total da memória em byte.|containerName|
|NetworkBytesReceivedPerSecond|Bytes de rede recebidos por segundo|Bytes|Média|Os bytes da rede recebidos por segundo.|Nenhum|
|NetworkBytesTransmittedPerSecond|Bytes de rede transmitidos por segundo|Bytes|Média|Os bytes da rede transmitidos por segundo.|Nenhum|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Contagem total de pullcount|Contagem total de puxar|Contagem|Média|Número de imagenpuxa no total|Nenhum|
|Contagem de PullCount bem sucedida|Contagem de puxar bem sucedida|Contagem|Média|Número de puxões de imagem bem sucedidos|Nenhum|
|Contagem total de empurras|Contagem total de empurrões|Contagem|Média|Número de impulsos de imagem no total|Nenhum|
|Contagem de PushCount bem sucedida|Contagem de impulsos bem sucedida|Contagem|Média|Número de impulsos de imagem bem sucedidos|Nenhum|
|Duração da execução|Duração da execução|Milissegundos|Total|Duração do percurso em milissegundos|Nenhum|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Número total de núcleos de CPU disponíveis num cluster gerido|Contagem|Média|Número total de núcleos de CPU disponíveis num cluster gerido|Nenhum|
|kube_node_status_allocatable_memory_bytes|Quantidade total de memória disponível num cluster gerido|Bytes|Média|Quantidade total de memória disponível num cluster gerido|Nenhum|
|kube_pod_status_ready|Número de cápsulas em estado pronto|Contagem|Média|Número de cápsulas em estado pronto|namespace,pod|
|kube_node_status_condition|Estados para várias condições do nó|Contagem|Média|Estados para várias condições do nó|condição,estado,status2,nó|
|kube_pod_status_phase|Número de cápsulas por fase|Contagem|Média|Número de cápsulas por fase|fase,espaço de nome,pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/fornecedores de recursos

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Pedidos de sucesso|Pedidos bem sucedidos|Contagem|Total|Pedidos bem sucedidos feitos pelo fornecedor personalizado|HttpMethod,CallPath,StatusCode|
|FailedRequests|Pedidos com Falhas|Contagem|Total|Obtém os registos disponíveis para fornecedores de recursos personalizados|HttpMethod,CallPath,StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|NICReadThroughput|Ler A Mais(Rede)|BytesPerSecond|Média|A entrada de leitura da interface de rede no dispositivo no período de reporte para todos os volumes do gateway.|InstanceName|
|NICWriteThroughput|Write Throughput (Rede)|BytesPerSecond|Média|A entrada de escrita da interface de rede no dispositivo no período de reporte para todos os volumes do gateway.|InstanceName|
|CloudReadThroughputPerShare|Cloud Download Throughput (Share)|BytesPerSecond|Média|O download para Azure a partir de uma parte durante o período de reporte.|Partilha|
|CloudUploadThroughputPerShare|Cloud Upload (Partilha)|BytesPerSecond|Média|A entrada de upload para Azure a partir de uma parte durante o período de reporte.|Partilha|
|BytesUploadedToCloudPerShare|Cloud Bytes carregado (Partilhar)|Bytes|Média|O número total de bytes que é enviado para OT a partir de uma parte durante o período de reporte.|Partilha|
|Capacidade Total|Capacidade Total|Bytes|Média|Capacidade Total|Nenhum|
|Capacidade Disponível|Capacidade Disponível|Bytes|Média|A capacidade disponível em bytes durante o período de reporte.|Nenhum|
|CloudUploadThroughput|Cloud Upload Throughput|BytesPerSecond|Média|A entrada de cloud upload para Azure durante o período de reporte.|Nenhum|
|CloudReadThroughput|Cloud Download Throughput|BytesPerSecond|Média|A entrada de cloud download para Azure durante o período de reporte.|Nenhum|
|BytesUploadedToCloud|Cloud Bytes Carregado (Dispositivo)|Bytes|Média|O número total de bytes que é enviado para Azure a partir de um dispositivo durante o período de reporte.|Nenhum|
|HyperVVirtualProcessorUtilization|Edge Compute - CPU percentual|Percentagem|Média|Por cento de utilização de CPU|InstanceName|
|Utilização hyperVMemory|Edge Compute - Uso da Memória|Percentagem|Média|Quantidade de RAM em Uso|InstanceName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|FailedRuns|Corridas falhadas|Contagem|Total||pipelineName,activityName|
|SuccessfulRuns|Corridas bem sucedidas|Contagem|Total||pipelineName,activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PipelineFailedRuns|Gasoduto falhado executa métricas|Contagem|Total||Tipo de falha,Nome|
|PipelineSucceededRuns|Gasoduto bem sucedido executa métricas|Contagem|Total||Tipo de falha,Nome|
|PipelineCancelruns|Gasoduto cancelado executa métricas|Contagem|Total||Tipo de falha,Nome|
|ActivityFailedRuns|A atividade falhada executa métricas|Contagem|Total||Tipo de atividade,PipelineName,Tipo de Falha,Nome|
|ActivitySucceededRuns|Atividade bem sucedida executa métricas|Contagem|Total||Tipo de atividade,PipelineName,Tipo de Falha,Nome|
|Atividades Canceladas Runs|Atividade cancelada executa métricas|Contagem|Total||Tipo de atividade,PipelineName,Tipo de Falha,Nome|
|TriggerFailedRuns|O gatilho falhado corre métricas|Contagem|Total||Nome,FalhaType|
|TriggerSucceededRuns|O gatilho bem sucedido corre métricas|Contagem|Total||Nome,FalhaType|
|TriggerCancelRuns|O gatilho cancelado corre métricas|Contagem|Total||Nome,FalhaType|
|IntegrationRuntimeCpuPercentage|Utilização do CPU em tempo de integração|Percentagem|Média||IntegraçãoRuntimeName,NodeName|
|IntegrationRuntimeAvailableMemory|Memória disponível de tempo de integração|Bytes|Média||IntegraçãoRuntimeName,NodeName|
|IntegraçãoRuntimeAverageTaskPickupDelay|Duração da fila de tempo de integração|Segundos|Média||IntegraçãoNome tempo de corrida de integração|
|IntegraçãoRuntimeQueueLength|Comprimento da fila de tempo de integração|Contagem|Média||IntegraçãoNome tempo de corrida de integração|
|IntegraçãoRuntimeAvailableNodeNumber|Contagem de tempo de integração disponível no nó|Contagem|Média||IntegraçãoNome tempo de corrida de integração|
|MaxAllowedResourceCount|Contagem máxima permitida de entidades|Contagem|Máximo||Nenhum|
|MaxAllowedFactorySizeInGbUnits|Tamanho máximo permitido da fábrica (unidade GB)|Contagem|Máximo||Nenhum|
|Contagem de Recursos|Total de entidades contam|Contagem|Máximo||Nenhum|
|FactorySizeInGbUnits|Tamanho total da fábrica (unidade GB)|Contagem|Máximo||Nenhum|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|JobEndedSuccess|Empregos bem sucedidos|Contagem|Total|Contagem de trabalhos bem sucedidos.|Nenhum|
|JobEndedFailure|Empregos falhados|Contagem|Total|Contagem de empregos falhados.|Nenhum|
|JobEndedCancelled|Empregos Cancelados|Contagem|Total|Contagem de empregos cancelados.|Nenhum|
|JobAUEndedSuccess|Tempo de U de sucesso|Segundos|Total|Tempo total da UA para empregos bem sucedidos.|Nenhum|
|JobAUEndedFailure|Tempo falhado da UA|Segundos|Total|Tempo total da UA para empregos falhados.|Nenhum|
|JobauendedCancelado|Hora da U Cancelada|Segundos|Total|Total de tempo da UA para trabalhos cancelados.|Nenhum|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalStorage|Armazenamento Total|Bytes|Máximo|Quantidade total de dados armazenados na conta.|Nenhum|
|DataWritten|Dados Escritos|Bytes|Total|Quantidade total de dados escritos na conta.|Nenhum|
|DataRead|Leitura de Dados|Bytes|Total|Quantidade total de dados lidos a partir da conta.|Nenhum|
|WriteRequests|Pedidos de Escrita|Contagem|Total|Contagem de dados escrevem pedidos para a conta.|Nenhum|
|ReadRequests|Ler Pedidos|Contagem|Total|Contagem de dados lê pedidos para a conta.|Nenhum|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Por cento do CPU|Percentagem|Média|Por cento do CPU|Nenhum|
|memory_percent|Por cento da memória|Percentagem|Média|Por cento da memória|Nenhum|
|io_consumption_percent|IO por cento|Percentagem|Média|IO por cento|Nenhum|
|storage_percent|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Nenhum|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhum|
|storage_limit|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Nenhum|
|serverlog_storage_percent|Percentagem de armazenamento de registo do servidor|Percentagem|Média|Percentagem de armazenamento de registo do servidor|Nenhum|
|serverlog_storage_usage|Armazenamento de registo do servidor utilizado|Bytes|Média|Armazenamento de registo do servidor utilizado|Nenhum|
|serverlog_storage_limit|Limite de armazenamento de registo do servidor|Bytes|Média|Limite de armazenamento de registo do servidor|Nenhum|
|active_connections|Conexões ativas|Contagem|Média|Conexões ativas|Nenhum|
|connections_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Nenhum|
|seconds_behind_master|Rema de replicação em segundos|Contagem|Máximo|Rema de replicação em segundos|Nenhum|
|backup_storage_used|Armazenamento de cópia de segurança utilizado|Bytes|Média|Armazenamento de cópia de segurança utilizado|Nenhum|
|network_bytes_egress|Saída da Rede|Bytes|Total|Network out através de conexões ativas|Nenhum|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Rede Em conexões ativas|Nenhum|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Por cento do CPU|Percentagem|Média|Por cento do CPU|Nenhum|
|memory_percent|Por cento da memória|Percentagem|Média|Por cento da memória|Nenhum|
|io_consumption_percent|IO por cento|Percentagem|Média|IO por cento|Nenhum|
|storage_percent|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Nenhum|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhum|
|storage_limit|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Nenhum|
|serverlog_storage_percent|Percentagem de armazenamento de registo do servidor|Percentagem|Média|Percentagem de armazenamento de registo do servidor|Nenhum|
|serverlog_storage_usage|Armazenamento de registo do servidor utilizado|Bytes|Média|Armazenamento de registo do servidor utilizado|Nenhum|
|serverlog_storage_limit|Limite de armazenamento de registo do servidor|Bytes|Máximo|Limite de armazenamento de registo do servidor|Nenhum|
|active_connections|Conexões ativas|Contagem|Média|Conexões ativas|Nenhum|
|connections_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Nenhum|
|seconds_behind_master|Rema de replicação em segundos|Contagem|Máximo|Rema de replicação em segundos|Nenhum|
|backup_storage_used|Armazenamento de cópia de segurança utilizado|Bytes|Média|Armazenamento de cópia de segurança utilizado|Nenhum|
|network_bytes_egress|Saída da Rede|Bytes|Total|Network out através de conexões ativas|Nenhum|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Rede Em conexões ativas|Nenhum|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Por cento do CPU|Percentagem|Média|Por cento do CPU|Nenhum|
|memory_percent|Por cento da memória|Percentagem|Média|Por cento da memória|Nenhum|
|io_consumption_percent|IO por cento|Percentagem|Média|IO por cento|Nenhum|
|storage_percent|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Nenhum|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhum|
|storage_limit|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Nenhum|
|serverlog_storage_percent|Percentagem de armazenamento de registo do servidor|Percentagem|Média|Percentagem de armazenamento de registo do servidor|Nenhum|
|serverlog_storage_usage|Armazenamento de registo do servidor utilizado|Bytes|Média|Armazenamento de registo do servidor utilizado|Nenhum|
|serverlog_storage_limit|Limite de armazenamento de registo do servidor|Bytes|Máximo|Limite de armazenamento de registo do servidor|Nenhum|
|active_connections|Conexões ativas|Contagem|Média|Conexões ativas|Nenhum|
|connections_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Nenhum|
|backup_storage_used|Armazenamento de cópia de segurança utilizado|Bytes|Média|Armazenamento de cópia de segurança utilizado|Nenhum|
|network_bytes_egress|Saída da Rede|Bytes|Total|Network out através de conexões ativas|Nenhum|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Rede Em conexões ativas|Nenhum|
|pg_replica_log_delay_in_seconds|Réplica Lag|Segundos|Máximo|Rérplica em segundos|Nenhum|
|pg_replica_log_delay_in_bytes|Max Lag Através de Réplicas|Bytes|Máximo|Lag in bytes da réplica mais atrasada|Nenhum|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Por cento do CPU|Percentagem|Média|Por cento do CPU|Nenhum|
|memory_percent|Por cento da memória|Percentagem|Média|Por cento da memória|Nenhum|
|iops|IOPS|Contagem|Média|IO Operações por segundo|Nenhum|
|storage_percent|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Nenhum|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhum|
|active_connections|Conexões ativas|Contagem|Média|Conexões ativas|Nenhum|
|network_bytes_egress|Saída da Rede|Bytes|Total|Network out através de conexões ativas|Nenhum|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Rede Em conexões ativas|Nenhum|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Mensagem de telemetria enviar tentativas|Contagem|Total|Número de mensagens de telemetria dispositivo-para-nuvem tentou ser enviada para o seu hub IoT|Nenhum|
|d2c.telemetry.ingress.success|Mensagens de telemetria enviadas|Contagem|Total|Número de mensagens de telemetria dispositivo-nuvem enviadas com sucesso para o seu hub IoT|Nenhum|
|c2d.commands.egress.complete.success|Entregas de mensagens C2D concluídas|Contagem|Total|Número de entregas de mensagens cloud-to-device concluídas com sucesso pelo dispositivo|Nenhum|
|c2d.commands.egress.abandon.success|Mensagens C2D abandonadas|Contagem|Total|Número de mensagens cloud-to-device abandonadas pelo dispositivo|Nenhum|
|c2d.commands.egress.reject.success|Mensagens C2D rejeitadas|Contagem|Total|Número de mensagens cloud-to-device rejeitadas pelo dispositivo|Nenhum|
|C2DMessagesExpired|Mensagens C2D Expiradas (pré-visualização)|Contagem|Total|Número de mensagens cloud-to-device expiradas|Nenhum|
|devices.totalDevices|Total de dispositivos (depreciados)|Contagem|Total|Número de dispositivos registados no seu hub IoT|Nenhum|
|devices.connectedDevices.allProtocol|Dispositivos conectados (depreciados) |Contagem|Total|Número de dispositivos ligados ao seu hub IoT|Nenhum|
|d2c.telemetry.egress.success|Encaminhamento: mensagens de telemetria entregues|Contagem|Total|O número de vezes que as mensagens foram entregues com sucesso a todos os pontos finais usando o encaminhamento do IoT Hub. Se uma mensagem for encaminhada para vários pontos finais, este valor aumenta em um para cada entrega bem sucedida. Se uma mensagem for entregue ao mesmo ponto final várias vezes, este valor aumenta por um para cada entrega bem sucedida.|Nenhum|
|d2c.telemetry.egress.dropped|Encaminhamento: mensagens de telemetria caídas |Contagem|Total|O número de vezes que as mensagens foram retiradas pelo itinerário do IoT Hub devido a pontos sem saída. Este valor não conta as mensagens entregues para a rota de recuo, uma vez que as mensagens deixadas não são entregues lá.|Nenhum|
|d2c.telemetry.egress.orphaned|Encaminhamento: mensagens de telemetria órfãs |Contagem|Total|O número de mensagens foi órfão pelo encaminhamento do IoT Hub porque não correspondia a nenhuma regra de encaminhamento (incluindo a regra do recuo). |Nenhum|
|d2c.telemetry.egress.invalid|Encaminhamento: mensagens de telemetria incompatíveis|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub não entregou mensagens devido a uma incompatibilidade com o ponto final. Este valor não inclui repetições.|Nenhum|
|d2c.telemetry.egress.fallback|Encaminhamento: mensagens entregues para recuo|Contagem|Total|O número de vezes que o ioT Hub encaminha-se entregou mensagens ao ponto final associado à rota de recuo.|Nenhum|
|d2c.endpoints.egress.eventHubs|Encaminhamento: mensagens entregues ao Event Hub|Contagem|Total|O número de vezes que o IoT Hub envia mensagens com sucesso entregou mensagens aos pontos finais do Event Hub.|Nenhum|
|d2c.endpoints.latency.eventHubs|Encaminhamento: latência de mensagens para O Hub de Eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens no IoT Hub e a entrada de mensagens num ponto final do Event Hub.|Nenhum|
|d2c.endpoints.egress.serviceBusQueues|Encaminhamento: mensagens entregues à Fila de Autocarros de Serviço|Contagem|Total|O número de vezes que o ioT Hub encaminha-se com sucesso entregou mensagens aos pontos finais da fila do Service Bus.|Nenhum|
|d2c.endpoints.latency.serviceBusQueues|Encaminhamento: latência de mensagens para fila de autocarros de serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens no IoT Hub e a mensagem de telemetria entrando num ponto final da fila do Autocarro de Serviço.|Nenhum|
|d2c.endpoints.egress.serviceBusTopics|Encaminhamento: mensagens entregues ao Tópico do Autocarro de Serviço|Contagem|Total|O número de vezes que o IoT Hub encaminha com sucesso entregou mensagens aos pontos finais do tópico do Bus de Serviço.|Nenhum|
|d2c.endpoints.latency.serviceBusTopics|Encaminhamento: latência de mensagens para tópico de ônibus de serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens no IoT Hub e a mensagem de telemetria entrando num ponto final do tópico do Bus de Serviço.|Nenhum|
|d2c.endpoints.egress.builtIn.events|Encaminhamento: mensagens entregues a mensagens/eventos|Contagem|Total|O número de vezes que o ioT Hub envia mensagens com sucesso entregou mensagens ao ponto final incorporado (mensagens/eventos).|Nenhum|
|d2c.endpoints.latency.builtIn.events|Encaminhamento: latência de mensagens/eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens no IoT Hub e a mensagem de telemetria entrando no ponto final incorporado (mensagens/eventos).|Nenhum|
|d2c.endpoints.egress.storage|Encaminhamento: mensagens entregues no armazenamento|Contagem|Total|O número de vezes que o ioT Hub encaminha com sucesso entregou mensagens para pontos finais de armazenamento.|Nenhum|
|d2c.endpoints.latency.storage|Encaminhamento: latência de mensagem para armazenamento|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens no IoT Hub e a mensagem de telemetria entrando num ponto final de armazenamento.|Nenhum|
|d2c.endpoints.egress.storage.bytes|Encaminhamento: dados entregues ao armazenamento|Bytes|Total|A quantidade de dados (bytes) IoT Hub encaminhamento entregue em pontos finais de armazenamento.|Nenhum|
|d2c.endpoints.egress.storage.blobs|Encaminhamento: bolhas entregues ao armazenamento|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou bolhas aos pontos finais de armazenamento.|Nenhum|
|Entregas eventgrid|Entregas da Grelha de Eventos (pré-visualização)|Contagem|Total|O número de eventos do IoT Hub publicados na Event Grid. Utilize a dimensão Resultado para o número de pedidos bem sucedidos e falhados. A dimensão do Tipo de Evento mostra o tipo de evento (https://aka.ms/ioteventgrid).|Resourceid,Resultado,Tipo de Evento|
|EventGridLatency|Latência da Grelha de Eventos (pré-visualização)|Milissegundos|Média|A latência média (milissegundos) de quando o evento Iot Hub foi gerado até quando o evento foi publicado para Event Grid. Este número é uma média entre todos os tipos de eventos. Utilize a dimensão EventType para ver a latência de um tipo específico de evento.|ResourceId,EventType|
|d2c.twin.read.success|Leituras gémeas bem sucedidas de dispositivos|Contagem|Total|A contagem de todas as leituras gémeas bem sucedidas iniciadas pelo dispositivo.|Nenhum|
|d2c.twin.read.failure|Leituras gémeas falhadas de dispositivos|Contagem|Total|A contagem de todas as leituras gémeas iniciadas pelo dispositivo falhado.|Nenhum|
|d2c.twin.read.size|Tamanho da resposta das leituras gémeas dos dispositivos|Bytes|Média|A média, min e máximo de todas as leituras gémeas bem sucedidas.|Nenhum|
|d2c.twin.update.success|Atualizações gémeas bem-sucedidas a partir de dispositivos|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas pelo dispositivo.|Nenhum|
|d2c.twin.update.failure|Atualizações duplas falhadas de dispositivos|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas pelo dispositivo.|Nenhum|
|d2c.twin.update.size|Tamanho das atualizações gémeas dos dispositivos|Bytes|Média|O tamanho médio, min e máximo de todas as atualizações gémeas iniciadas pelo dispositivo.|Nenhum|
|c2d.methods.success.success|Invocações de método direto bem-sucedidas|Contagem|Total|A contagem de todas as chamadas de métodos diretos bem sucedidas.|Nenhum|
|c2d.methods.failure|Invocações de método direto falhado|Contagem|Total|A contagem de todas as chamadas de métodos diretos falhadas.|Nenhum|
|c2d.methods.requestSize|Solicitar dimensão das invocações do método direto|Bytes|Média|A média, min e máximo de todos os pedidos de método direto bem sucedidos.|Nenhum|
|c2d.methods.responseSize|Tamanho da resposta das invocações do método direto|Bytes|Média|A média, min e máximo de todas as respostas de método direto bem sucedidas.|Nenhum|
|c2d.twin.read.success|Twin de sucesso lê a partir de trás|Contagem|Total|A contagem de todas as leituras gémeas bem sucedidas.|Nenhum|
|c2d.twin.read.failure|Gémeo falhado lê a partir de trás|Contagem|Total|A contagem de todas as leituras gémeas iniciadas no final.|Nenhum|
|c2d.twin.read.size|Tamanho da resposta de leituras gémeas da parte de trás|Bytes|Média|A média, min, e o máximo de todas as leituras gémeas bem sucedidas.|Nenhum|
|c2d.twin.update.success|Atualizações gémeas bem sucedidas a partir de trás|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas no back-end.|Nenhum|
|c2d.twin.update.failure|Atualizações gémeas falhadas a partir de trás|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas no back-end.|Nenhum|
|c2d.twin.update.size|Tamanho das atualizações gémeas a partir de trás|Bytes|Média|O tamanho médio, min e máximo de todas as atualizações gémeas iniciadas no back-end.|Nenhum|
|twinQueries.success|Consultas gémeas bem sucedidas|Contagem|Total|A contagem de todas as consultas gémeas bem sucedidas.|Nenhum|
|twinQueries.failure|Perguntas gémeas falhadas|Contagem|Total|A contagem de todas as perguntas gémeas falhadas.|Nenhum|
|twinQueries.resultSize|Tamanho do resultado das consultas gémeas|Bytes|Média|A média, min, e o máximo do tamanho do resultado de todas as consultas gémeas bem sucedidas.|Nenhum|
|jobs.createTwinUpdateJob.success|Criações bem sucedidas de empregos de atualização dupla|Contagem|Total|A contagem de toda a criação bem sucedida de empregos de atualização dupla.|Nenhum|
|jobs.createTwinUpdateJob.failure|Criações falhadas de empregos de atualização dupla|Contagem|Total|A contagem de todos os empregos falhados de atualização dupla.|Nenhum|
|jobs.createDirectMethodJob.success|Criações bem sucedidas de empregos de invocação de métodos|Contagem|Total|A contagem de todas as criações bem sucedidas de empregos de invocação de métodos diretos.|Nenhum|
|jobs.createDirectMethodJob.failure|Criações falhadas de empregos de invocação de métodos|Contagem|Total|A contagem de todos os empregos de invocação de métodos diretos.|Nenhum|
|jobs.listJobs.success|Chamadas bem sucedidas para listar empregos|Contagem|Total|A contagem de todas as chamadas bem sucedidas para listar empregos.|Nenhum|
|jobs.listJobs.failure|Chamadas falhadas para listar empregos|Contagem|Total|A contagem de todas as chamadas falhadas para listar empregos.|Nenhum|
|jobs.cancelJob.success|Cancelamentos de emprego bem sucedidos|Contagem|Total|A contagem de todas as chamadas bem sucedidas para cancelar um trabalho.|Nenhum|
|jobs.cancelJob.failure|Cancelamentos de emprego falhados|Contagem|Total|A contagem de todas as chamadas falhadas para cancelar um trabalho.|Nenhum|
|jobs.queryJobs.success|Consultas de emprego bem sucedidas|Contagem|Total|A contagem de todas as chamadas bem sucedidas para trabalhos de consulta.|Nenhum|
|jobs.queryJobs.failure|Consultas de trabalho falhadas|Contagem|Total|A contagem de todas as chamadas falhadas para trabalhos de consulta.|Nenhum|
|jobs.completed|Trabalhos concluídos|Contagem|Total|A contagem de todos os trabalhos concluídos.|Nenhum|
|jobs.failed|Empregos falhados|Contagem|Total|A contagem de todos os empregos falhados.|Nenhum|
|d2c.telemetry.ingress.sendThrottle|Número de erros de estrangulamento|Contagem|Total|Número de erros de estrangulamento devido a aceleração de entrada do dispositivo|Nenhum|
|dailyMessageQuotaUsed|Número total de mensagens utilizadas|Contagem|Média|Número de mensagens totais usadas hoje|Nenhum|
|deviceDataUsage|Utilização total dos dados do dispositivo|Bytes|Total|Bytes transferidos de e para quaisquer dispositivos ligados ao IotHub|Nenhum|
|deviceDataUsageV2|Utilização total de dados do dispositivo (pré-visualização)|Bytes|Total|Bytes transferidos de e para quaisquer dispositivos ligados ao IotHub|Nenhum|
|totaldeDispositivoCount|Total de dispositivos (pré-visualização)|Contagem|Média|Número de dispositivos registados no seu hub IoT|Nenhum|
|connectedDeviceCount|Dispositivos conectados (pré-visualização)|Contagem|Média|Número de dispositivos ligados ao seu hub IoT|Nenhum|
|configurações|Métricas de Configuração|Contagem|Total|Métricas para Operações de Configuração|Nenhum|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Tentativas de Inscrição|Tentativas de registo|Contagem|Total|Número de registos de dispositivos tentados|ProvisioningServiceName,IotHubName,Status|
|Atribuição de Dispositivos|Dispositivos atribuídos|Contagem|Total|Número de dispositivos atribuídos a um hub IoT|ProvisioningServiceName,IotHubName|
|Tentativas de Attestation|Tentativas de atestação|Contagem|Total|Número de atestados de dispositivos tentados|ProvisioningServiceName,Status,Protocol|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Região Add|Região Adicionada|Contagem|Contagem|Região Adicionada|Região|
|Armazenamento Disponível|Armazenamento disponível|Bytes|Total|Total de armazenamento disponível reportado em 5 minutos granularidade|Nome de recolha,Nome da Base de Dados,Região|
|CassandraConnectionClosures|Encerramentos de conexões Cassandra|Contagem|Total|Número de ligações cassandra que foram fechadas, reportadas a uma granularidade de 1 minuto|APIType,Região,ClosureReason|
|CassandraRequestCharges|Cassandra Request Charges|Contagem|Total|RUs consumidos para pedidos de Cassandra feitos|APIType,Nome de base de dados,Nome de recolha,Região,Tipo de Funcionamento,Tipo de Recursos|
|Pedidos de Cassandra|Pedidos de Cassandra|Contagem|Contagem|Número de pedidos de Cassandra feitos|APIType,Nome de base de dados,Nome de recolha,Região,Tipo de Operação,Tipo de Recurso,Errorcode|
|CreateAccount|Conta Criada|Contagem|Contagem|Conta Criada|Nenhum|
|DataUsage|Utilização de Dados|Bytes|Total|Utilização total de dados reportado sintetizando a 5 minutos granularidade|Nome de recolha,Nome da Base de Dados,Região|
|DeleteAccount|Conta Eliminada|Contagem|Contagem|Conta Eliminada|Nenhum|
|DocumentCount|Contagem de documentos|Contagem|Total|Contagem total de documentos reportada a 5 minutos de granularidade|Nome de recolha,Nome da Base de Dados,Região|
|DocumentQuota|Quota de Documento|Bytes|Total|Quota total de armazenamento reportada em 5 minutos granularidade|Nome de recolha,Nome da Base de Dados,Região|
|Utilização de índices|Utilização do índice|Bytes|Total|Utilização total do índice reportada a 5 minutos de granularidade|Nome de recolha,Nome da Base de Dados,Região|
|MetadataRequests|Pedidos de Metadados|Contagem|Contagem|Contagem de pedidos de metadados. Cosmos DB mantém recolha de metadados do sistema para cada conta, que lhe permite enumerar coleções, bases de dados, etc, e suas configurações, gratuitamente.|Nome de base de dados,Nome de recolha,Região,StatusCode,Função|
|MongoRequestCharge|Taxa de pedido de Mongo|Contagem|Total|Unidades de Pedido de Mongo Consumidas|Nome de base de dados,Nome de recolha,Região,Nome de Comando,Errorcode|
|Pedidos de Mongo|Pedidos de Mongo|Contagem|Contagem|Número de pedidos de Mongo feitos|Nome de base de dados,Nome de recolha,Região,Nome de Comando,Errorcode|
|MongoRequestsCount|Taxa de pedido de Mongo|CountPerSecond|Média|Pedido de Mongo Conde por segundo|Nome de base de dados,Nome de recolha,Região,Nome de Comando,Errorcode|
|MongoRequestsDelete|Taxa de pedido de exclusão de Mongo|CountPerSecond|Média|Mongo Apagar pedido por segundo|Nome de base de dados,Nome de recolha,Região,Nome de Comando,Errorcode|
|MongoRequestsInsert|Taxa de pedido de inserção de Mongo|CountPerSecond|Média|Mongo Inserir contagem por segundo|Nome de base de dados,Nome de recolha,Região,Nome de Comando,Errorcode|
|MongoRequestsQuery|Taxa de pedido de consulta de Mongo|CountPerSecond|Média|Pedido de consulta de Mongo por segundo|Nome de base de dados,Nome de recolha,Região,Nome de Comando,Errorcode|
|MongoRequestsUpdate|Taxa de pedido de atualização de Mongo|CountPerSecond|Média|Pedido de Atualização de Mongo por segundo|Nome de base de dados,Nome de recolha,Região,Nome de Comando,Errorcode|
|ProvisionedThroughput|Débito Aprovisionado|Contagem|Máximo|Débito Aprovisionado|Nome base de dados,Nome de recolha|
|RegiãoFailover|Região falhou|Contagem|Contagem|Região falhou|Nenhum|
|Região de Remoção|Região Removida|Contagem|Contagem|Região Removida|Região|
|ReplicaçãoTardia|Latência de Replicação P99|MilliSeconds|Média|Latência de replicação P99 entre fonte e regiões-alvo para conta geoactiva|SourceRegion,TargetRegion|
|ServiçoDisponibilidade|Disponibilidade de Serviço|Percentagem|Média|Conta solicita disponibilidade de uma hora, dia ou mês|Nenhum|
|TotalRequestUnits|Total de Unidades de Pedido|Contagem|Total|Unidades de Pedido consumidas|Nome de base de dados,Nome de recolha,Região,StatusCode,OperationType|
|Total De Pedidos|Total de Pedidos|Contagem|Contagem|Número de pedidos feitos|Nome de base de dados,Nome de recolha,Região,StatusCode,OperationType|
|UpdateAccountKeys|Chaves de conta atualizadas|Contagem|Contagem|Chaves de conta atualizadas|chaveTipo|
|Definições de Rede de Atualizações|Definições de rede de conta atualizadas|Contagem|Contagem|Definições de rede de conta atualizadas|Nenhum|
|Definições de replicação de 'Actualizações', Replicação de Contas|Definições de replicação de conta atualizadas|Contagem|Contagem|Definições de replicação de conta atualizadas|Nenhum|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft.EnterpriseKnowledgeGraph/services

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TransacçõesContagem|Contagem de Transações|Contagem|Contagem|Contagem total de transações|TransacçõesContagem|
|Contagem de Sucessos|Contagem de Sucesso|Contagem|Contagem|Contagem de Transações Bem sucedidas|Contagem de Sucessos|
|FalhaCount|Contagem de falhas|Contagem|Contagem|Contagem de Transações Falhadas|FalhaCount|
|SucessoLatency|Latência de Sucesso|MilliSeconds|Média|Latência de Transações Bem Sucedidas|Contagem de Sucessos|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos Publicados|Contagem|Total|Total de eventos publicados para este tópico|Tópico|
|PublicarFailCount|Publicar Eventos Falhados|Contagem|Total|Eventos totais não publicaram para este tópico|Tópico,ErrorType,Error|
|PublishSuccessLatencyInMs|Publicar Latência de Sucesso|Milissegundos|Total|Publicar latência de sucesso em milissegundos|Nenhum|
|MatchedEventCount|Eventos Combinados|Contagem|Total|Total de eventos combinados com esta subscrição do evento|Tópico,Nome de Subscrição de Eventos,DomínioEventSubscriptionName|
|DeliveryAttemptFailCount|Eventos falhados de entrega|Contagem|Total|Eventos totais não entregaram a esta subscrição do evento|Tópico,Nome de Subscrição de Eventos,DomínioEventSubscriptionName,Error,ErrorType|
|DeliverySuccessCount|Eventos Entregues|Contagem|Total|Total de eventos entregues a esta subscrição do evento|Tópico,Nome de Subscrição de Eventos,DomínioEventSubscriptionName|
|DestinationProcessingDurationInMs|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Tópico,Nome de Subscrição de Eventos,DomínioEventSubscriptionName|
|Contagem de eventos abandonados|Eventos abandonados|Contagem|Total|Total deixou cair eventos correspondentes a esta subscrição de evento|Tópico,Nome de Subscrição de Eventos,DomínioNome de Subscrição de Domínio,DropReason|
|DeadLetteredCount|Eventos com Letras Mortas|Contagem|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|Tópico,Nome de Subscrição de Eventos,Nome de Subscrição de Domínio,DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos Publicados|Contagem|Total|Total de eventos publicados para este tópico|Nenhum|
|PublicarFailCount|Publicar Eventos Falhados|Contagem|Total|Eventos totais não publicaram para este tópico|ErrorType,Error|
|Contagem de eventos incomparáveis|Eventos Incomparáveis|Contagem|Total|Eventos totais que não correspondem a nenhuma das subscrições do evento para este tópico|Nenhum|
|PublishSuccessLatencyInMs|Publicar Latência de Sucesso|Milissegundos|Total|Publicar latência de sucesso em milissegundos|Nenhum|
|MatchedEventCount|Eventos Combinados|Contagem|Total|Total de eventos combinados com esta subscrição do evento|Nome de subscrição de eventos|
|DeliveryAttemptFailCount|Eventos falhados de entrega|Contagem|Total|Eventos totais não entregaram a esta subscrição do evento|Error,ErrorType,EventSubscriptionName|
|DeliverySuccessCount|Eventos Entregues|Contagem|Total|Total de eventos entregues a esta subscrição do evento|Nome de subscrição de eventos|
|DestinationProcessingDurationInMs|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Nome de subscrição de eventos|
|Contagem de eventos abandonados|Eventos abandonados|Contagem|Total|Total deixou cair eventos correspondentes a esta subscrição de evento|DropReason,EventSubscriptionName|
|DeadLetteredCount|Eventos com Letras Mortas|Contagem|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|MatchedEventCount|Eventos Combinados|Contagem|Total|Total de eventos combinados com esta subscrição do evento|Nenhum|
|DeliveryAttemptFailCount|Eventos falhados de entrega|Contagem|Total|Eventos totais não entregaram a esta subscrição do evento|Error,ErrorType|
|DeliverySuccessCount|Eventos Entregues|Contagem|Total|Total de eventos entregues a esta subscrição do evento|Nenhum|
|DestinationProcessingDurationInMs|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Nenhum|
|Contagem de eventos abandonados|Eventos abandonados|Contagem|Total|Total deixou cair eventos correspondentes a esta subscrição de evento|DropReason|
|DeadLetteredCount|Eventos com Letras Mortas|Contagem|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos Publicados|Contagem|Total|Total de eventos publicados para este tópico|Nenhum|
|PublicarFailCount|Publicar Eventos Falhados|Contagem|Total|Eventos totais não publicaram para este tópico|ErrorType,Error|
|Contagem de eventos incomparáveis|Eventos Incomparáveis|Contagem|Total|Eventos totais que não correspondem a nenhuma das subscrições do evento para este tópico|Nenhum|
|PublishSuccessLatencyInMs|Publicar Latência de Sucesso|Milissegundos|Total|Publicar latência de sucesso em milissegundos|Nenhum|



## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Pedidos bem sucedidos|Pedidos bem sucedidos|Contagem|Total|Pedidos de sucesso para Microsoft.EventHub.|Nome de entidade,OperationResult|
|Erros de servidor|Erros do servidor.|Contagem|Total|Erros de servidor para Microsoft.EventHub.|Nome de entidade,OperationResult|
|Erros de utilizador|Erros do utilizador.|Contagem|Total|Erros de utilizador para Microsoft.EventHub.|Nome de entidade,OperationResult|
|QuotaExceededErrors|Erros ultrapassados da quota.|Contagem|Total|Quota Exceeded Errors for Microsoft.EventHub.|Nome de entidade,OperationResult|
|ThrottledRequests|Pedidos estrangulados.|Contagem|Total|Pedidos throttled para Microsoft.EventHub.|Nome de entidade,OperationResult|
|IncomingRequests|Pedidos de Entrada|Contagem|Total|Pedidos de entrada para Microsoft.EventHub.|EntityName|
|IncomingMessages|Mensagens de entrada|Contagem|Total|Mensagens de entrada para Microsoft.EventHub.|EntityName|
|OutgoingMessages|Mensagens de saída|Contagem|Total|Mensagens de saída para Microsoft.EventHub.|EntityName|
|IncomingBytes|Bytes de entrada.|Bytes|Total|Incoming Bytes para Microsoft.EventHub.|EntityName|
|OutgoingBytes|Bytes de saída.|Bytes|Total|Bytes de saída para Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Contagem|Média|Total de conexões ativas para Microsoft.EventHub.|Nenhum|
|Conexões Abertas|Ligações abertas.|Contagem|Média|Ligações abertas para Microsoft.EventHub.|EntityName|
|Conexões Fechadas|Ligações fechadas.|Contagem|Média|Ligações fechadas para Microsoft.EventHub.|EntityName|
|CaptureBacklog|Capture Backlog.|Contagem|Total|Capture Backlog para Microsoft.EventHub.|EntityName|
|CapturedMessages|Mensagens capturadas.|Contagem|Total|Mensagens capturadas para Microsoft.EventHub.|EntityName|
|CapturedBytes|Bytes capturados.|Bytes|Total|Bytes capturados para Microsoft.EventHub.|EntityName|
|Tamanho|Tamanho|Bytes|Média|Tamanho de um EventHub em Bytes.|EntityName|
|INREQS|Pedidos de entrada (Preprecated)|Contagem|Total|Total de pedidos de envio de entrada para um espaço de nome (Deprecated)|Nenhum|
|SUCCREQ|Pedidos bem sucedidos (Depreciados)|Contagem|Total|Total de pedidos bem sucedidos para um espaço de nome (Deprecated)|Nenhum|
|FAILREQ|Pedidos Falhados (Preprecated)|Contagem|Total|Total de pedidos falhados para um espaço de nome (Deprecated)|Nenhum|
|SVRBSY|Erros ocupados do servidor (Deprecated)|Contagem|Total|Total de erros ocupados do servidor para um espaço de nome (Deprecated)|Nenhum|
|INTERR|Erros internos do servidor (Deprecated)|Contagem|Total|Total de erros internos do servidor para um espaço de nome (Deprecated)|Nenhum|
|MISCERR|Outros Erros (Deprecated)|Contagem|Total|Total de pedidos falhados para um espaço de nome (Deprecated)|Nenhum|
|INMSGS|Mensagens de entrada (obsoletas) (Depreciadas)|Contagem|Total|Total de mensagens de entrada para um espaço de nome. Esta métrica está depreciada. Utilize a métrica de Mensagens Incoming (Depreciada)|Nenhum|
|EHINMSGS|Mensagens de entrada (Depreciadas)|Contagem|Total|Total de mensagens de entrada para um espaço de nome (Deprecated)|Nenhum|
|Outmsgs|Mensagens de saída (obsoletas) (Depreciadas)|Contagem|Total|Total de mensagens de saída para um espaço de nome. Esta métrica está depreciada. Utilize a métrica das Mensagens De saída (Depreciada)|Nenhum|
|EHOUTMSGS|Mensagens de saída (Depreciadas)|Contagem|Total|Total de mensagens de saída para um espaço de nome (Deprecated)|Nenhum|
|EHINMBS|Bytes de entrada (obsoletos) (Depreciados)|Bytes|Total|Entrada de mensagem do Event Hub para um espaço de nome. Esta métrica está depreciada. Utilize a métrica incoming bytes (Depreciada)|Nenhum|
|EHINBYTES|Bytes de entrada (Deprecated)|Bytes|Total|Entrada de mensagem do Event Hub para um espaço de nome (Deprecated)|Nenhum|
|EHOUTMBS|Bytes de saída (obsoletos) (Depreciados)|Bytes|Total|Entrada de mensagem de saída do Event Hub para um espaço de nome. Esta métrica está depreciada. Utilize a métrica de bytes de saída (Depreciada)|Nenhum|
|EHOUTBYTES|Bytes de saída (Deprecated)|Bytes|Total|Entrada de mensagem de saída do Event Hub para um espaço de nome (Deprecated)|Nenhum|
|EHABL|Mensagens de atraso de arquivo (Depreciadas)|Contagem|Total|Mensagens de arquivo do Event Hub em atraso para um espaço de nome (Deprecated)|Nenhum|
|EHAMSGS|Mensagens de arquivo (Depreciadas)|Contagem|Total|O Event Hub arquivou mensagens num espaço de nome (Deprecated)|Nenhum|
|EHAMBS|Entrada de mensagem de arquivo (Depreciada)|Bytes|Total|Web de mensagem arquivada do Event Hub num espaço de nome (Deprecated)|Nenhum|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Pedidos bem sucedidos|Pedidos bem sucedidos|Contagem|Total|Pedidos de sucesso para Microsoft.EventHub.|OperaçãoResult|
|Erros de servidor|Erros do servidor.|Contagem|Total|Erros de servidor para Microsoft.EventHub.|OperaçãoResult|
|Erros de utilizador|Erros do utilizador.|Contagem|Total|Erros de utilizador para Microsoft.EventHub.|OperaçãoResult|
|QuotaExceededErrors|Erros ultrapassados da quota.|Contagem|Total|Quota Exceeded Errors for Microsoft.EventHub.|OperaçãoResult|
|ThrottledRequests|Pedidos estrangulados.|Contagem|Total|Pedidos throttled para Microsoft.EventHub.|OperaçãoResult|
|IncomingRequests|Pedidos de Entrada|Contagem|Total|Pedidos de entrada para Microsoft.EventHub.|Nenhum|
|IncomingMessages|Mensagens de entrada|Contagem|Total|Mensagens de entrada para Microsoft.EventHub.|Nenhum|
|OutgoingMessages|Mensagens de saída|Contagem|Total|Mensagens de saída para Microsoft.EventHub.|Nenhum|
|IncomingBytes|Bytes de entrada.|Bytes|Total|Incoming Bytes para Microsoft.EventHub.|Nenhum|
|OutgoingBytes|Bytes de saída.|Bytes|Total|Bytes de saída para Microsoft.EventHub.|Nenhum|
|ActiveConnections|ActiveConnections|Contagem|Média|Total de conexões ativas para Microsoft.EventHub.|Nenhum|
|Conexões Abertas|Ligações abertas.|Contagem|Média|Ligações abertas para Microsoft.EventHub.|Nenhum|
|Conexões Fechadas|Ligações fechadas.|Contagem|Média|Ligações fechadas para Microsoft.EventHub.|Nenhum|
|CaptureBacklog|Capture Backlog.|Contagem|Total|Capture Backlog para Microsoft.EventHub.|Nenhum|
|CapturedMessages|Mensagens capturadas.|Contagem|Total|Mensagens capturadas para Microsoft.EventHub.|Nenhum|
|CapturedBytes|Bytes capturados.|Bytes|Total|Bytes capturados para Microsoft.EventHub.|Nenhum|
|CPU|CPU|Percentagem|Máximo|Utilização do CPU para o Cluster do Hub de Eventos em percentagem|Função|
|Memória Disponível|Memória Disponível|Percentagem|Máximo|Memória disponível para o Cluster Do Hub de Eventos em percentagem da memória total.|Função|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|GatewayRequests|Pedidos gateway|Contagem|Total|Número de pedidos de gateway|HttpStatus|
|CategorizedGatewayRequests|Pedidos categorizados de gateway|Contagem|Total|Número de pedidos de gateway por categorias (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|Trabalhadores NumActivos|Número de Trabalhadores Ativos|Contagem|Máximo|Número de Trabalhadores Ativos|Nome métrico|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ObservedMetricValue|Valor Métrico Observado|Contagem|Média|O valor calculado por escala automática quando executado|MetricTriggerSource|
|MetricThreshold|Limiar Métrico|Contagem|Média|O limiar de escala automática configurado quando a escala automática funcionava.|MetricTriggerRule|
|Capacidade Observada|Capacidade Observada|Contagem|Média|A capacidade reportada à escala automática quando foi executada.|Nenhum|
|ScaleActionsInitiated|Ações de escala iniciadas|Contagem|Total|A direção da operação de escala.|ScaleDirection|




## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Disponibilidade|Percentagem|Média|Percentagem de testes de disponibilidade concluídos com sucesso|disponibilidadeResult/nome,disponibilidadeResult/localização|
|disponibilidadeResultados/contagem|Testes de disponibilidade|Contagem|Contagem|Contagem de testes de disponibilidade|disponibilidadeResult/nome,disponibilidadeResult/localização,disponibilidadeResult/sucesso|
|availabilityResults/duration|Duração do teste de disponibilidade|MilliSeconds|Média|Duração do teste de disponibilidade|disponibilidadeResult/nome,disponibilidadeResult/localização,disponibilidadeResult/sucesso|
|browserTimings/networkDuration|Tempo de ligação da rede de carga de página|MilliSeconds|Média|Tempo entre o pedido do utilizador e a ligação à rede. Inclui a aparência DNS e a ligação de transporte.|Nenhum|
|browserTimings/processingDuration|Tempo de processamento do cliente|MilliSeconds|Média|Tempo entre receber o último byte de um documento até que o DOM esteja carregado. Os pedidos de asincronização podem ainda estar a ser processados.|Nenhum|
|browserTimings/receiveDuration|Tempo de resposta de receção|MilliSeconds|Média|Tempo entre o primeiro e o último bytes, ou até a desconexão.|Nenhum|
|browserTimings/sendDuration|Enviar tempo de pedido|MilliSeconds|Média|Tempo entre a ligação da rede e receber o primeiro byte.|Nenhum|
|browserTimings/totalDuration|Tempo de carga da página do navegador|MilliSeconds|Média|O tempo do pedido do utilizador até do M, folhas de estilo, scripts e imagens são carregados.|Nenhum|
|dependências/contagem|Chamadas de dependência|Contagem|Contagem|Contagem de chamadas feitas pela aplicação a recursos externos.|dependência/tipo,dependência/desempenhoBucket,dependência/sucesso,dependência/target,operação/sintético,cloud/roleInstance,cloud/roleName/roleName|
|dependências/duração|Duração da dependência|MilliSeconds|Média|Duração das chamadas efetuadas pela aplicação a recursos externos.|dependência/tipo,dependência/desempenhoBucket,dependência/sucesso,dependência/target,operação/sintético,cloud/roleInstance,cloud/roleName/roleName|
|dependências/falhado|Falhas de chamada de dependência|Contagem|Contagem|Contagem de chamadas de dependência falhadas feitas pela aplicação a recursos externos.|dependência/tipo,dependência/desempenhoBucket,dependência/sucesso,dependência/target,operação/sintético,cloud/roleInstance,cloud/roleName/roleName|
|pageViews/count|Vistas da página|Contagem|Contagem|Contagem de visualizações de página.|operação/sintético,cloud/roleName|
|páginaVistas/duração|Tempo de carga da vista da página|MilliSeconds|Média|Tempo de carga da vista da página|operação/sintético,cloud/roleName|
|performanceCounters/requestExecutionTime|HTTP solicitar tempo de execução|MilliSeconds|Média|Tempo de execução do pedido mais recente.|cloud/roleInstance|
|performanceCounters/requestsInQueue|PEDIDOS HTTP na fila de candidaturas|Contagem|Média|Duração da fila do pedido de pedido de inscrição.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Taxa de pedido http|CountPerSecond|Média|Taxa de todos os pedidos ao pedido por segundo a partir de ASP.NET.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Taxa de exceção|CountPerSecond|Média|Contagem de exceções manipuladas e não manipuladas reportadas às janelas, incluindo exceções .NET e exceções não geridas que são convertidas em exceções .NET.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Taxa IO do processo|BytesPerSecond|Média|Total de bytes por segundo lido e escrito para ficheiros, rede e dispositivos.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|CpU processo|Percentagem|Média|A percentagem de tempo decorrido que todos os fios de processo utilizaram o processador para executar instruções. Isto pode variar entre 0 a 100. Esta métrica indica o desempenho do processo w3wp sozinho.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Hora do processador|Percentagem|Média|A percentagem de tempo que o processador passa em fios não ociosos.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Memória disponível|Bytes|Média|Memória física imediatamente disponível para alocação a um processo ou utilização do sistema.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Processo bytes privados|Bytes|Média|Memória exclusivamente atribuída aos processos da aplicação monitorizada.|cloud/roleInstance|
|pedidos/duração|Tempo de resposta do servidor|MilliSeconds|Média|Tempo entre receber um pedido http e terminar o envio da resposta.|pedido/desempenhoBucket,request/resultCódigo,operação/sintético,cloud/roleInstance,request/success,cloud/roleName|
|pedidos/contagem|Pedidos de servidor|Contagem|Contagem|Contagem dos pedidos http concluídos.|pedido/desempenhoBucket,request/resultCódigo,operação/sintético,cloud/roleInstance,request/success,cloud/roleName|
|pedidos/falhado|Pedidos falhados|Contagem|Contagem|Contagem de pedidos HTTP marcados como falhados. Na maioria dos casos, estes são pedidos com um código de resposta >= 400 e não igual a 401.|pedido/desempenhoBucket,request/resultCódigo,pedido/sucesso,operação/sintético,cloud/roleInstance,cloud/roleName|
|pedidos/taxa|Taxa de pedido do servidor|CountPerSecond|Média|Taxa de pedidos de servidor por segundo|pedido/desempenhoBucket,request/resultCódigo,operação/sintético,cloud/roleInstance,request/success,cloud/roleName|
|exceções/contagem|Exceções|Contagem|Contagem|Contagem combinada de todas as exceções não apanhadas.|cloud/roleName,cloud/roleInstance,cliente/tipo|
|exceções/navegador|Exceções de browser|Contagem|Contagem|Contagem de exceções não apanhadas lançadas no navegador.|cliente/isServer,cloud/roleName|
|exceções/servidor|Exceções ao servidor|Contagem|Contagem|Contagem de exceções não apanhadas lançadas na aplicação do servidor.|cliente/isServer,cloud/roleName,cloud/roleInstance|
|vestígios/contagem|Vestígios|Contagem|Contagem|Contagem de documentos de rastreio|traço/gravidadeN,operação/sintético,cloud/roleName,cloud/roleInstance|



## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServiceApiHit|Total de acesso síldo de serviço Api Hits|Contagem|Contagem|Número de acessos totais de api de serviço|Tipo de Atividade,Nome de Atividade|
|ServiceApiLatency|Atncy api de serviço geral|Milissegundos|Média|Latência geral dos pedidos de api de serviço|Tipo de atividade,Nome de atividade,StatusCode,StatusCodeClass|
|ServiceApiResult|Resultados totais da Api de serviço|Contagem|Contagem|Número de resultados totais da api de serviço|Tipo de atividade,Nome de atividade,StatusCode,StatusCodeClass|
|Caixa de SaturationShoebox|Saturação geral do cofre|Percentagem|Média|Capacidade do cofre utilizada|Tipo de atividade,Nome de atividade,Tipo de Transação|
|Disponibilidade|Disponibilidade geral do cofre|Percentagem|Média|Abóbada solicita disponibilidade de pedidos|Tipo de atividade,Nome de atividade,StatusCode,StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CacheUtilization|Utilização de cache|Percentagem|Média|Nível de utilização no âmbito do cluster|Nenhum|
|QueryDuration|Duração da consulta|Milissegundos|Média|Duração das consultas em segundos|ConsultaStatus|
|IngestionUtilização|Utilização de ingestão|Percentagem|Média|Relação entre as ranhuras de ingestão utilizadas no cluster|Nenhum|
|KeepAlive|Mantenha-se vivo|Contagem|Média|Verificação de sanidade indica que o cluster responde a consultas|Nenhum|
|IngestionVolumeInMB|Volume de ingestão (em MB)|Contagem|Total|Volume global de dados ingeridos para o cluster (em MB)|Base de Dados|
|IngestionLatencyInSeconds|Latência de ingestão (em segundos)|Segundos|Média|Tempo de ingestão da fonte (por exemplo, mensagem está no EventHub) para o cluster em segundos|Nenhum|
|EventosProcessadosForEventHubs|Eventos processados (para Hubs de Evento/IoT)|Contagem|Total|Número de eventos processados pelo cluster ao ingerir em Event/IoT Hub|EventStatus|
|IngestionResult|Resultado da ingestão|Contagem|Contagem|Número de operações de ingestão|IngestionResultDetails|
|CPU|CPU|Percentagem|Média|Nível de utilização do CPU|Nenhum|
|ContinuousExportNumOfRecordsExported|Exportação contínua – numdos de registos exportados|Contagem|Total|Número de registos exportados, disparados por cada artefacto de armazenamento escrito durante a operação de exportação|Nenhum|
|ExportUtilização|Utilização das exportações|Percentagem|Máximo|Utilização das exportações|Nenhum|
|ContinuousExportPendingCount|Contagem pendente de exportação contínua|Contagem|Máximo|O número de postos de trabalho de exportação continuados pendentes prontos para a execução|Nenhum|
|ContinuousExportMaxLatenessMinutes|Minutos contínuos de atraso de exportação max|Contagem|Máximo|O atraso máximo em minutos de todas as exportações contínuas pendentes e prontas para a execução|Nenhum|
|Resultado contínuo da exportação|Resultado contínuo da exportação|Contagem|Contagem|Indica se a Exportação Contínua foi bem sucedida ou fracassada|Nome de exportação contínua,Resultado,Base de Dados|
|StreamingIngestDura|Duração do ingest de streaming|Milissegundos|Média|Streaming de duração da ingestão em milissegundos|Nenhum|
|StreamingIngestDataRate|Taxa de dados de ingestão de streaming|Contagem|Média|Taxa de dados de ingerir streaming (MB por segundo)|Nenhum|
|Taxa de solicitação a vapor|Taxa de pedido de ingestão de streaming|Contagem|RateRequestsPerSecond|Taxa de pedido de influxo de streaming (pedidos por segundo)|Nenhum|
|StreamingIngestResults|Resultado da ingest de streaming|Contagem|Média|Resultado de streaming de ingerir|Resultado|
|TotalNumberOfConcurrentQueries|Número total de consultas simultâneas|Contagem|Total|Número total de consultas simultâneas|Nenhum|
|TotalNumberOfThrottledQueries|Número total de consultas estranguladas|Contagem|Total|Número total de consultas estranguladas|Nenhum|
|TotalNumberOfThrottledCommands|Número total de comandos estrangulados|Contagem|Total|Número total de comandos estrangulados|Tipo de comando|
|TotalNumberOfExtents|Número total de extensões|Contagem|Total|Número total de extensões de dados|Nenhum|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RunsStarted|Corridas Iniciadas|Contagem|Total|O número de corridas de fluxo de trabalho começou.|Nenhum|
|RunsCompleted|Execuções Concluídas|Contagem|Total|Número de fluxos de trabalho concluídos.|Nenhum|
|RunsSucceeded|Runs Succeeded|Contagem|Total|O número de corridas de fluxo de trabalho foi bem sucedido.|Nenhum|
|RunsFailed|Execuções falhadas|Contagem|Total|O número de corridas de fluxo de trabalho falhou.|Nenhum|
|RunsCancelled|Execuções canceladas|Contagem|Total|Número de fluxos de trabalho cancelados.|Nenhum|
|RunLatency|Executar Latência|Segundos|Média|Latência de corridas completas de fluxo de trabalho.|Nenhum|
|RunSuccessLatency|Latência de Sucesso de Execução|Segundos|Média|Latência de corridas de fluxo de trabalho bem sucedidas.|Nenhum|
|RunThrottledEvents|Eventos Throttled Run|Contagem|Total|Número de ação de fluxo de trabalho ou de provocar eventos acelerados.|Nenhum|
|RunStartThrottledEvents|Executar eventos throttled|Contagem|Total|O número de fluxos de trabalho iniciam eventos acelerados.|Nenhum|
|RunFailurePercentage|Percentagem de falhas de execução|Percentagem|Total|A percentagem de corridas de fluxo de trabalho falhou.|Nenhum|
|Ações Iniciadas|Ações Iniciadas |Contagem|Total|O número de ações de fluxo de trabalho começou.|Nenhum|
|AçõesConcluídas|Ações Concluídas |Contagem|Total|Número de ações de fluxo de trabalho concluídas.|Nenhum|
|ActionsSucceeded|Ações bem sucedidas |Contagem|Total|O número de ações de fluxo de trabalho foi bem sucedido.|Nenhum|
|ActionsFailed|Ações falhadas |Contagem|Total|O número de ações de fluxo de trabalho falhou.|Nenhum|
|Ações Ignoradas|Ações ignoradas |Contagem|Total|O número de ações de fluxo de trabalho ignoradas.|Nenhum|
|AçãoLatency|Latência de Ação |Segundos|Média|Latência de ações completas de fluxo de trabalho.|Nenhum|
|ActionSuccessLatency|Latência de Sucesso de Ação |Segundos|Média|Latência de ações de fluxo de trabalho bem sucedidas.|Nenhum|
|ActionThrottledEvents|Eventos throttled de ação|Contagem|Total|Número de ação de fluxo de trabalho acelerou eventos.|Nenhum|
|GatilhosIniciado|Gatilhos Iniciados |Contagem|Total|O número de gatilhos de fluxo de trabalho começou.|Nenhum|
|TriggersCompleted|Gatilhos Concluídos |Contagem|Total|Número de gatilhos de fluxo de trabalho concluídos.|Nenhum|
|TriggersSucceeded|Gatilhos bem sucedidos |Contagem|Total|O número de gatilhos de fluxo de trabalho foi bem sucedido.|Nenhum|
|TriggersFailed|Gatilhos falhados |Contagem|Total|O número de gatilhos de fluxo de trabalho falhou.|Nenhum|
|Gatilhos Skipped|Gatilhos ignorados|Contagem|Total|O número de gatilhos de fluxo de trabalho saltou.|Nenhum|
|TriggersFired|Gatilhos disparados |Contagem|Total|Número de acionadores de fluxo de trabalho disparados.|Nenhum|
|GatilhoLatency|Latência do gatilho |Segundos|Média|Latência dos gatilhos de fluxo de trabalho completos.|Nenhum|
|TriggerFireLatency|Latência de disparo |Segundos|Média|Latência de disparados acionadores de fluxo de trabalho.|Nenhum|
|TriggerSuccessLatency|Latência de Sucesso do Gatilho |Segundos|Média|Latência dos desencadeadores de fluxo de trabalho bem sucedidos.|Nenhum|
|TriggerThrottledEvents|Eventos throttled de gatilho|Contagem|Total|Número de eventos de fluxo de trabalho acelerados.|Nenhum|
|BillableActionExecutions|Execuções de Ação Billable|Contagem|Total|Número de execuções de ação de fluxo de trabalho a serem cobradas.|Nenhum|
|BillableTriggerExecutions|Execuções de gatilho sinuosos|Contagem|Total|O número de execuções de fluxo de trabalho a ser cobrado.|Nenhum|
|TotalBillableExecutions|Execuções Totais De Faturação|Contagem|Total|Número de execuções de fluxo de trabalho a ser faturadas.|Nenhum|
|BillingUsageNativeOperation|Utilização de faturação para execuções de operações nativas|Contagem|Total|Número de execuções de operações nativas a serem cobradas.|Nenhum|
|BillingUsageStandardConnector|Utilização de faturação para execuções de conector padrão|Contagem|Total|Número de execuções de conector padrão a serem cobradas.|Nenhum|
|BillingUsageStorageConsumption|Utilização de faturação para execuções de consumo de armazenamento|Contagem|Total|Número de execuções de consumo de armazenamento a serem cobradas.|Nenhum|
|BillingUsageNativeOperation|Utilização de faturação para execuções de operações nativas|Contagem|Total|Número de execuções de operações nativas a serem cobradas.|Nenhum|
|BillingUsageStandardConnector|Utilização de faturação para execuções de conector padrão|Contagem|Total|Número de execuções de conector padrão a serem cobradas.|Nenhum|
|BillingUsageStorageConsumption|Utilização de faturação para execuções de consumo de armazenamento|Contagem|Total|Número de execuções de consumo de armazenamento a serem cobradas.|Nenhum|



## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RunsStarted|Corridas Iniciadas|Contagem|Total|O número de corridas de fluxo de trabalho começou.|Nenhum|
|RunsCompleted|Execuções Concluídas|Contagem|Total|Número de fluxos de trabalho concluídos.|Nenhum|
|RunsSucceeded|Runs Succeeded|Contagem|Total|O número de corridas de fluxo de trabalho foi bem sucedido.|Nenhum|
|RunsFailed|Execuções falhadas|Contagem|Total|O número de corridas de fluxo de trabalho falhou.|Nenhum|
|RunsCancelled|Execuções canceladas|Contagem|Total|Número de fluxos de trabalho cancelados.|Nenhum|
|RunLatency|Executar Latência|Segundos|Média|Latência de corridas completas de fluxo de trabalho.|Nenhum|
|RunSuccessLatency|Latência de Sucesso de Execução|Segundos|Média|Latência de corridas de fluxo de trabalho bem sucedidas.|Nenhum|
|RunThrottledEvents|Eventos Throttled Run|Contagem|Total|Número de ação de fluxo de trabalho ou de provocar eventos acelerados.|Nenhum|
|RunStartThrottledEvents|Executar eventos throttled|Contagem|Total|O número de fluxos de trabalho iniciam eventos acelerados.|Nenhum|
|RunFailurePercentage|Percentagem de falhas de execução|Percentagem|Total|A percentagem de corridas de fluxo de trabalho falhou.|Nenhum|
|Ações Iniciadas|Ações Iniciadas |Contagem|Total|O número de ações de fluxo de trabalho começou.|Nenhum|
|AçõesConcluídas|Ações Concluídas |Contagem|Total|Número de ações de fluxo de trabalho concluídas.|Nenhum|
|ActionsSucceeded|Ações bem sucedidas |Contagem|Total|O número de ações de fluxo de trabalho foi bem sucedido.|Nenhum|
|ActionsFailed|Ações falhadas |Contagem|Total|O número de ações de fluxo de trabalho falhou.|Nenhum|
|Ações Ignoradas|Ações ignoradas |Contagem|Total|O número de ações de fluxo de trabalho ignoradas.|Nenhum|
|AçãoLatency|Latência de Ação |Segundos|Média|Latência de ações completas de fluxo de trabalho.|Nenhum|
|ActionSuccessLatency|Latência de Sucesso de Ação |Segundos|Média|Latência de ações de fluxo de trabalho bem sucedidas.|Nenhum|
|ActionThrottledEvents|Eventos throttled de ação|Contagem|Total|Número de ação de fluxo de trabalho acelerou eventos.|Nenhum|
|GatilhosIniciado|Gatilhos Iniciados |Contagem|Total|O número de gatilhos de fluxo de trabalho começou.|Nenhum|
|TriggersCompleted|Gatilhos Concluídos |Contagem|Total|Número de gatilhos de fluxo de trabalho concluídos.|Nenhum|
|TriggersSucceeded|Gatilhos bem sucedidos |Contagem|Total|O número de gatilhos de fluxo de trabalho foi bem sucedido.|Nenhum|
|TriggersFailed|Gatilhos falhados |Contagem|Total|O número de gatilhos de fluxo de trabalho falhou.|Nenhum|
|Gatilhos Skipped|Gatilhos ignorados|Contagem|Total|O número de gatilhos de fluxo de trabalho saltou.|Nenhum|
|TriggersFired|Gatilhos disparados |Contagem|Total|Número de acionadores de fluxo de trabalho disparados.|Nenhum|
|GatilhoLatency|Latência do gatilho |Segundos|Média|Latência dos gatilhos de fluxo de trabalho completos.|Nenhum|
|TriggerFireLatency|Latência de disparo |Segundos|Média|Latência de disparados acionadores de fluxo de trabalho.|Nenhum|
|TriggerSuccessLatency|Latência de Sucesso do Gatilho |Segundos|Média|Latência dos desencadeadores de fluxo de trabalho bem sucedidos.|Nenhum|
|TriggerThrottledEvents|Eventos throttled de gatilho|Contagem|Total|Número de eventos de fluxo de trabalho acelerados.|Nenhum|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Utilização do processador workflow para o ambiente de serviço de integração|Percentagem|Média|Utilização do processador workflow para ambiente de serviço de integração.|Nenhum|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Utilização da memória do fluxo de trabalho para o ambiente do serviço de integração|Percentagem|Média|Utilização da memória do fluxo de trabalho para o ambiente de serviço de integração.|Nenhum|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Utilização do processador de conector para o ambiente do serviço de integração|Percentagem|Média|Utilização do processador de conector para ambiente de serviço de integração.|Nenhum|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Uso da memória do conector para o ambiente do serviço de integração|Percentagem|Média|Utilização da memória do conector para o ambiente de serviço de integração.|Nenhum|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Corridas Concluídas|Corridas Concluídas|Contagem|Total|Número de execuções concluídas com sucesso para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Corridas Iniciadas|Corridas Iniciadas|Contagem|Total|Número de corridas iniciadas para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Corridas falhadas|Corridas falhadas|Contagem|Total|Número de corridas falhou para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Registo de Modelos Bem Sucedido|Registo de Modelos Bem Sucedido|Contagem|Total|Número de registos de modelos que conseguiram neste espaço de trabalho|Cenário|
|Registo de Modelos Falhado|Registo de Modelos Falhado|Contagem|Total|Número de registos de modelos que falharam neste espaço de trabalho|Cenário,Código de Estado|
|Modelo Implantação Iniciada|Modelo Implantação Iniciada|Contagem|Total|Número de implementações de modelos iniciadas neste espaço de trabalho|Cenário|
|Model Deploy Succeeded|Model Deploy Succeeded|Contagem|Total|Número de implementações de modelos que conseguiram neste espaço de trabalho|Cenário|
|Implementação do modelo falhou|Implementação do modelo falhou|Contagem|Total|Número de implementações de modelos que falharam neste espaço de trabalho|Cenário,Código de Estado|
|Nósodes totais|Nósodes totais|Contagem|Média|Número de nós totais. Este total inclui alguns de Nós Ativos, Nós Ociosos, Nós Inutilizáveis, Nós Preempted, Deixando nós|Cenário,ClusterName|
|Nódosativos Ativos|Nódosativos Ativos|Contagem|Média|Número de nós de Aitve. Estes são os nódosos que estão ativamente a gerir um trabalho.|Cenário,ClusterName|
|Nódoas ociosas|Nódoas ociosas|Contagem|Média|Número de nós ociosos. Os nódoas ociosas são os nódosos que não estão a gerir nenhum emprego, mas podem aceitar novos empregos se disponíveis.|Cenário,ClusterName|
|Nós Inutilizáveis|Nós Inutilizáveis|Contagem|Média|Número de nós inutilizáveis. Nós inutilizáveis não são funcionais devido a algum problema irresolúvel. O Azure vai reciclar estes nódosos.|Cenário,ClusterName|
|Nódosos pré-empreitados|Nódosos pré-empreitados|Contagem|Média|Número de nós pré-empreitados. Estes nódosos são os nódosos de baixa prioridade que são retirados da piscina disponível.|Cenário,ClusterName|
|Deixando nódosos|Deixando nódosos|Contagem|Média|Número de nós deixando. Deixando os nódosos são os nódosos que acabaram de processar um trabalho e irão para o estado de Idle.|Cenário,ClusterName|
|Núcleos Totais|Núcleos Totais|Contagem|Média|Número de núcleos totais|Cenário,ClusterName|
|Núcleos Ativos|Núcleos Ativos|Contagem|Média|Número de núcleos ativos|Cenário,ClusterName|
|Núcleos ociosos|Núcleos ociosos|Contagem|Média|Número de núcleos ociosos|Cenário,ClusterName|
|Núcleos Inutilizáveis|Núcleos Inutilizáveis|Contagem|Média|Número de núcleos inutilizáveis|Cenário,ClusterName|
|Núcleos Preempted|Núcleos Preempted|Contagem|Média|Número de núcleos pré-optados|Cenário,ClusterName|
|Deixando núcleos|Deixando núcleos|Contagem|Média|Número de núcleos de saída|Cenário,ClusterName|
|Percentagem de Utilização de Quotas|Percentagem de Utilização de Quotas|Contagem|Média|Por cento da quota utilizada|Cenário,ClusterName,VmFamilyName,VmPriority|
|CpuUtilização|CpuUtilização|Contagem|Média|CPU (Pré-visualização)|Cenário,runId,NodeId,CreatedTime|
|GpuUtilização|GpuUtilização|Contagem|Média|GPU (Pré-visualização)|Cenário,runId,NodeId,CreatedTime,DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Utilização|Utilização|Contagem|Contagem|Contagem de chamadas da API|Categoria Api,ApiName,Tipo de Resultados,Código de Resposta|
|Disponibilidade|Disponibilidade|Percentagem|Média|Disponibilidade das APIs|Categoria Api,ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Saída|Saída|Bytes|Total|A quantidade de dados da Egress, em bytes.|Formato de saída|
|SuccessE2ELatency|Fim do sucesso para acabar com a Latência|Milissegundos|Média|A latência média para pedidos bem sucedidos em milissegundos.|Formato de saída|
|Pedidos|Pedidos|Contagem|Total|Pedidos para um Ponto Final de Streaming.|Formato de saída,HttpStatusCode,ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AssetQuota|Quota de ativos|Contagem|Média|Quantos ativos são permitidos para a conta de serviço de mídia atual|Nenhum|
|AssetCount|Contagem de ativos|Contagem|Média|Quantos ativos já estão criados na conta de serviço de mídia atual|Nenhum|
|AssetQuotaUsedPercentage|Percentagem de quota de ativos utilizada|Percentagem|Média|Percentagem de ativos utilizada na conta de serviço de mídia corrente|Nenhum|
|ContentKeyPolicyQuota|Quota de Política chave de conteúdo|Contagem|Média|Quantos agentes chave de conteúdo são permitidos para a conta de serviço de mídia atual|Nenhum|
|ContentKeyPolicyCount|Contagem de políticas de chave de conteúdo|Contagem|Média|Quantas políticas chave de conteúdo já estão criadas na conta de serviço de mídia atual|Nenhum|
|ContentKeyPolicyQuotaUsedPercentage|Percentagem de política-chave de conteúdo utilizada|Percentagem|Média|Política chave de conteúdo utilizada percentagem na conta de serviço de mídia corrente|Nenhum|
|StreamingPolicyQuota|Quota política de streaming|Contagem|Média|Quantas políticas de streaming são permitidas para a conta de serviço de mídia atual|Nenhum|
|StreamingPolicyCount|Contagem de políticas de streaming|Contagem|Média|Quantas políticas de streaming já estão criadas na conta de serviço sinuoso atual|Nenhum|
|StreamingPolicyQuotaUsedPercentage|Quota de política de streaming utilizada percentagem|Percentagem|Média|Política de Streaming utilizada percentagem na conta de serviço de mídia atual|Nenhum|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|MédiaDeLeitura|Latência média de leitura|MilliSeconds|Média|Latência média de leitura em milissegundos por operação|Nenhum|
|AverageWriteLatency|Latência média de escrita|MilliSeconds|Média|Latência média de escrita em milissegundos por operação|Nenhum|
|Volumelógico|Tamanho lógico do volume|Bytes|Média|Tamanho lógico do volume (bytes usados)|Nenhum|
|VolumeSnapshotSize|Tamanho do instantâneo de volume|Bytes|Média|Tamanho de todos os instantâneos em volume|Nenhum|
|ReadIops|Ler iops|CountPerSecond|Média|Ler operações de in/fora por segundo|Nenhum|
|WriteIops|Escrever iops|CountPerSecond|Média|Escreva operações in/out por segundo|Nenhum|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Piscina de volume atribuída|Bytes|Média|Tamanho usado atribuído da piscina|Nenhum|
|VolumePoolTotalLogicalSize|Volume pool tamanho lógico total|Bytes|Média|Soma do tamanho lógico de todos os volumes pertencentes à piscina|Nenhum|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BytesSentRate|Bytes Enviados|Bytes|Total|Número de bytes a Interface de Rede enviada|Nenhum|
|BytesReceivedRate|Bytes Recebidos|Bytes|Total|Número de bytes que a Interface de Rede recebeu|Nenhum|
|PacketsSentRate|Pacotes Enviados|Contagem|Total|Número de pacotes que a Interface de Rede enviou|Nenhum|
|PacotesReceivedRate|Pacotes Recebidos|Contagem|Total|Número de pacotes que a Interface de Rede recebeu|Nenhum|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Disponibilidade Vip|Disponibilidade do Caminho de Dados|Contagem|Média|Disponibilidade de dados de balanceadores de carga média por duração do tempo|FrontendIPAddress,FrontendPort|
|Disponibilidade de Mergulho|Estado da sonda de saúde|Contagem|Média|Estado médio da sonda de saúde balanceadores de carga por duração do tempo|ProtocolType,BackendPort,FrontendIPAddress,FrontendPort,BackendIPAddress|
|ByteCount|Conde byte|Contagem|Total|Número total de Bytes transmitidos dentro do período de tempo|FrontendIPAddress,FrontendPort,Direção|
|Contagem de pacotes|Contagem de pacotes|Contagem|Total|Número total de pacotes transmitidos dentro do período de tempo|FrontendIPAddress,FrontendPort,Direção|
|SYNCount|Contagem de SYN|Contagem|Total|Número total de pacotes syn transmitidos dentro do período de tempo|FrontendIPAddress,FrontendPort,Direção|
|SnatConnectionCount|Contagem de ligação sNAT|Contagem|Total|Número total de novas ligações SNAT criadas dentro do período de tempo|FrontendIPAddress,BackendIPAddress,ConnectionState|
|AllocatedSnatPorts|Portas SNAT atribuídas (Pré-visualização)|Contagem|Total|Número total de portas SNAT atribuídas dentro do prazo|FrontendIPAddress,BackendIPAddress,ProtocolType,IsAwaitingRemoval|
|UsedSnatPorts|Portas SNAT usadas (Pré-visualização)|Contagem|Total|Número total de portas SNAT utilizadas dentro do período de tempo|FrontendIPAddress,BackendIPAddress,ProtocolType,IsAwaitingRemoval|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueryVolume|Volume de consulta|Contagem|Total|Número de consultas servidas para uma zona de DNS|Nenhum|
|RecordSetCount|Contagem de conjuntos de discos|Contagem|Máximo|Número de conjuntos de recordes numa zona de DNS|Nenhum|
|RecordSetCapacityUtilization|Utilização da capacidade do conjunto de registos|Percentagem|Máximo|Por cento da capacidade do Record set utilizada por uma zona DNS|Nenhum|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PacketsInDDoS|Pacotes de entrada DDoS|CountPerSecond|Máximo|Pacotes de entrada DDoS|Nenhum|
|PacketsDroppedDDoS|Pacotes de entrada caíram DDoS|CountPerSecond|Máximo|Pacotes de entrada caíram DDoS|Nenhum|
|PacketsForwardedDDoS|Pacotes de entrada encaminhadas DDoS|CountPerSecond|Máximo|Pacotes de entrada encaminhadas DDoS|Nenhum|
|TCPPacketsInDDoS|Pacotes TCP de entrada DDoS|CountPerSecond|Máximo|Pacotes TCP de entrada DDoS|Nenhum|
|TCPPacketsDroppedDDoS|Pacotes tCP de entrada caíram DDoS|CountPerSecond|Máximo|Pacotes tCP de entrada caíram DDoS|Nenhum|
|TCPPacketsForwardedDDoS|Pacotes TCP de entrada encaminharam DDoS|CountPerSecond|Máximo|Pacotes TCP de entrada encaminharam DDoS|Nenhum|
|UDPPacketsInDDoS|Pacotes UDP de entrada DDoS|CountPerSecond|Máximo|Pacotes UDP de entrada DDoS|Nenhum|
|UDPPacketsDroppedDDoS|Pacotes uDP de entrada caíram DDoS|CountPerSecond|Máximo|Pacotes uDP de entrada caíram DDoS|Nenhum|
|UDPPacketsForwardedDDoS|Pacotes UDP de entrada encaminharam DDoS|CountPerSecond|Máximo|Pacotes UDP de entrada encaminharam DDoS|Nenhum|
|BytesInDDoS|Bytes de entrada|BytesPerSecond|Máximo|Bytes de entrada|Nenhum|
|BytesDroppedDDoS|Bytes de entrada caíram DDoS|BytesPerSecond|Máximo|Bytes de entrada caíram DDoS|Nenhum|
|BytesForwardedDDoS|Bytes de entrada encaminhadas DDoS|BytesPerSecond|Máximo|Bytes de entrada encaminhadas DDoS|Nenhum|
|TCPBytesInDDoS|TCP de entrada bytes DDoS|BytesPerSecond|Máximo|TCP de entrada bytes DDoS|Nenhum|
|TCPBytesDroppedDDoS|Bytes de TCP de entrada caíram DDoS|BytesPerSecond|Máximo|Bytes de TCP de entrada caíram DDoS|Nenhum|
|TCPBytesForwardedDDoS|Bytes de TCP de entrada encaminhadas DDoS|BytesPerSecond|Máximo|Bytes de TCP de entrada encaminhadas DDoS|Nenhum|
|UDPBytesInDDoS|UDP de entrada bytes DDoS|BytesPerSecond|Máximo|UDP de entrada bytes DDoS|Nenhum|
|UDPBytesDroppedDDoS|Bytes uDP de entrada caíram DDoS|BytesPerSecond|Máximo|Bytes uDP de entrada caíram DDoS|Nenhum|
|UDPBytesForwardedDDoS|Bytes UDP de entrada|BytesPerSecond|Máximo|Bytes UDP de entrada|Nenhum|
|IfUnderDDoSAttack|Sob ataque DDoS ou não|Contagem|Máximo|Sob ataque DDoS ou não|Nenhum|
|DDoSTriggerTCPPackets|Pacotes TCP de entrada para desencadear mitigação do DDoS|CountPerSecond|Máximo|Pacotes TCP de entrada para desencadear mitigação do DDoS|Nenhum|
|DDoSTriggerUDPPackets|Pacotes UDP de entrada para desencadear mitigação do DDoS|CountPerSecond|Máximo|Pacotes UDP de entrada para desencadear mitigação do DDoS|Nenhum|
|DDoSTriggerSYNPackets|Pacotes SYN de entrada para desencadear mitigação do DDoS|CountPerSecond|Máximo|Pacotes SYN de entrada para desencadear mitigação do DDoS|Nenhum|
|Disponibilidade Vip|Disponibilidade do Caminho de Dados|Contagem|Média|Disponibilidade média de endereço IP por duração do tempo|Porta|
|ByteCount|Conde byte|Contagem|Total|Número total de Bytes transmitidos dentro do período de tempo|Porto,Direção|
|Contagem de pacotes|Contagem de pacotes|Contagem|Total|Número total de pacotes transmitidos dentro do período de tempo|Porto,Direção|
|SynCount|Contagem de SYN|Contagem|Total|Número total de pacotes syn transmitidos dentro do período de tempo|Porto,Direção|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Tempo de ida e volta para Pings a um VM|MilliSeconds|Média|Tempo de ida e volta para Pings enviado para um destino VM|Endereço sourceCustomer,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Pings falhados a um VM|Percentagem|Média|Por cento do número de Pings falhados no total enviado pings de um destino VM|Endereço sourceCustomer,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ApplicationRuleHit|Regras de aplicação atingem a contagem|Contagem|Total|Número de vezes que as regras de aplicação foram atingidas|Estado,Razão,Protocolo|
|NetworkRuleHit|Regras da rede atingir a contagem|Contagem|Total|Número de vezes que as regras da rede foram atingidas|Estado,Razão,Protocolo|
|FirewallHealth|Estado de saúde firewall|Percentagem|Média|Estado de saúde firewall|Estado,Razão|
|DataProcessado|Dados tratados|Bytes|Total|Quantidade total de dados processados pela Firewall|Nenhum|
|SNATPortUtilização|Utilização da porta SNAT|Percentagem|Média|Utilização da porta SNAT|Nenhum|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Débito|Débito|BytesPerSecond|Média|Número de bytes por segundo o Gateway de Aplicação serviu|Nenhum|
|UnhealthyHostCount|Contagem de hospedeiros pouco saudável|Contagem|Média|Número de anfitriões insalubres|BackendSettingsPool|
|HealthyHostCount|Contagem de hospedeiros saudáveis|Contagem|Média|Número de anfitriões saudáveis de backend|BackendSettingsPool|
|Total De Pedidos|Total de Pedidos|Contagem|Total|Contagem de pedidos bem sucedidos que o Application Gateway serviu|BackendSettingsPool|
|AvgRequestCountPerHealthyHostHost|Pedidos por minuto por Anfitrião Saudável|Contagem|Média|Contagem média de pedidos por minuto por hospedeiro saudável em uma piscina|BackendSettingsPool|
|FailedRequests|Pedidos com Falhas|Contagem|Total|Contagem de pedidos falhados que o Application Gateway serviu|BackendSettingsPool|
|Estatuto de Resposta|Estado de Resposta|Contagem|Total|Estado de resposta http devolvido por Application Gateway|HttpStatusGroup|
|CurrentConnections|Conexões Atuais|Contagem|Total|Contagem das ligações atuais estabelecidas com gateway de aplicação|Nenhum|
|CpuUtilização|Utilização do CPU|Percentagem|Média|Utilização atual do CPU do Gateway de Aplicação|Nenhum|
|Unidades de Capacidade|Unidades de Capacidade Atuais|Contagem|Média|Unidades de Capacidade consumidas|Nenhum|
|ComputeUnits|Unidades Computadas Atuais|Contagem|Média|Unidades Computadas consumidas|Nenhum|
|BackendResponseStatus|Estado de resposta de backend|Contagem|Total|O número de códigos de resposta HTTP gerados pelos membros do backend. Isto não inclui quaisquer códigos de resposta gerados pelo Gateway da Aplicação.|BackendServer,BackendPool,BackendHttpSetting,HttpStatusGroup|
|Protocolo TLS|Protocolo TLS cliente|Contagem|Total|O número de pedidos tLS e não-TLS iniciados pelo cliente que estabeleceu a ligação com o Gateway de Aplicação. Para ver a distribuição do protocolo TLS, filtre pela dimensão do Protocolo TLS.|Ouvinte,TlsProtocol|
|BytesSent|Bytes Enviados|Bytes|Total|O número total de bytes enviados pela Porta de Entrada de Aplicação aos clientes|Ouvinte|
|BytesReceived|Bytes Recebidos|Bytes|Total|O número total de bytes recebidos pelo Gateway de Aplicação dos clientes|Ouvinte|
|ClientRtt|Cliente RTT|MilliSeconds|Média|Tempo médio de ida e volta entre clientes e Gateway de aplicação. Esta métrica indica quanto tempo leva para estabelecer ligações e reconhecer|Ouvinte|
|AplicaçãoGatewayTotalTime|Tempo total do Gateway de Aplicação|MilliSeconds|Média|O tempo médio que leva para que um pedido seja processado e a sua resposta seja enviada. Isto é calculado em média do intervalo a partir do momento em que o Application Gateway recebe o primeiro byte de um pedido HTTP para o momento em que a resposta envia a operação termina. É importante notar que isto geralmente inclui o tempo de processamento do Gateway de aplicação, o tempo que os pacotes de pedido e resposta estão viajando sobre a rede e o tempo que o servidor backend levou para responder.|Ouvinte|
|BackendConnectTime|Tempo de ligação backend|MilliSeconds|Média|Tempo gasto estabelecendo uma ligação com um servidor de backend|Ouvinte,BackendServer,BackendPool,BackendHttpSetting|
|BackendFirstByteResponseTime|Apoiar o primeiro tempo de resposta do byte|MilliSeconds|Média|Intervalo de tempo entre o início do estabelecimento de uma ligação ao servidor de backend e receber o primeiro byte do cabeçalho de resposta, aproximando o tempo de processamento do servidor backend|Ouvinte,BackendServer,BackendPool,BackendHttpSetting|
|BackendLastByteResponseTime|Backend Last Byte Response Time|MilliSeconds|Média|Intervalo de tempo entre o início do estabelecimento de uma ligação ao servidor de backend e receber o último byte do corpo de resposta|Ouvinte,BackendServer,BackendPool,BackendHttpSetting|
|Contagem compatível|Distribuição total de regras de firewall de aplicação web|Contagem|Total|Distribuição total de regras de firewall de aplicação web para o tráfego de entrada|RuleGroup,RuleId|
|Contagem bloqueada|Distribuição de regras de pedidos bloqueados por firewall de aplicação web|Contagem|Total|Firewall bloqueado de aplicações web solicita a distribuição de regras|RuleGroup,RuleId|
|BlockedReqCount|Contagem de pedidos bloqueados por firewall de aplicação web|Contagem|Total|Contagem de pedidos bloqueados por firewall de aplicação web|Nenhum|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Largura média de banda|Gateway S2S Largura de banda|BytesPerSecond|Média|Largura de banda de site-a-local média de um gateway em bytes por segundo|Nenhum|
|Largura de banda p2S|Largura de banda Gateway P2S|BytesPerSecond|Média|Largura de banda ponto-a-local média de um gateway em bytes por segundo|Nenhum|
|P2SConnectionCount|Contagem de ligação P2S|Contagem|Máximo|Contagem de ligação ponto-a-local de um portal|Protocolo|
|TunnelAverageBandwidth|Largura de banda do túnel|BytesPerSecond|Média|Largura de banda média de um túnel em bytes por segundo|Nome de ligação,RemoteIP|
|TunnelEgressBytes|Túnel Egress Bytes|Bytes|Total|Bytes de saída de um túnel|Nome de ligação,RemoteIP|
|TunnelIngressBytes|Bytes de Ingresso do Túnel|Bytes|Total|Bytes de entrada de um túnel|Nome de ligação,RemoteIP|
|TunnelEgressPackets|Pacotes de Egress do Túnel|Contagem|Total|Contagem de pacotes de saída de um túnel|Nome de ligação,RemoteIP|
|TunnelIngressPackets|Pacotes de ingressos de túnel|Contagem|Total|Contagem de pacotes de entrada de um túnel|Nome de ligação,RemoteIP|
|TunnelEgressPacketDropTSMismatch|Túnel Egress TS Mismatch Packet Drop|Contagem|Total|Contagem de gotas de pacote de saída de um desvio de seletor de tráfego de um túnel|Nome de ligação,RemoteIP|
|TunnelIngressPacketDropTSMismatch|Túnel Ingress TS Mismatch Packet Drop|Contagem|Total|Contagem de gotas de pacote de entrada de um túnel de seletor de tráfego|Nome de ligação,RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Nível RxLight|Nível RxLight|Contagem|Média|Nível de luz Rx em dBm|Link,Pista|
|TxLightLevel|TxLightLevel|Contagem|Média|Nível de luz Tx em dBm|Link,Pista|
|Estado-de-administração|Estado-de-administração|Contagem|Média|Estado administrativo do porto|Ligação|
|Protocolo de Linha|Protocolo de Linha|Contagem|Média|Estatuto do protocolo de linha da porta|Ligação|
|PortBitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits ingressando Azure por segundo|Ligação|
|PortBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits a esganar Azure por segundo|Ligação|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits ingressando Azure por segundo|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits a esganar Azure por segundo|PeeringType|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|CountPerSecond|Média|Bits ingressando Azure por segundo|PeeredCircuitsKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|CountPerSecond|Média|Bits a esganar Azure por segundo|PeeredCircuitsKey|
|BGPDisponibilidade|Disponibilidade de BGP|Percentagem|Média|BGP Disponibilidade do MSEE para todos os pares.|PeeringType,Peer|
|Disponibilidade de Arp|Disponibilidade arp|Percentagem|Média|Disponibilidade arp do MSEE para todos os pares.|PeeringType,Peer|
|QosDropBitsInPerSecond|DropInBitsPerSecond|CountPerSecond|Média|Ingressos de dados caíram por segundo|Nenhum|
|QosDropBitsOutPerSecond|DropOutBitsPerSecond|CountPerSecond|Média|Egress bits de dados caiu por segundo|Nenhum|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits ingressando Azure por segundo|Nenhum|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits a esganar Azure por segundo|Nenhum|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits ingressando Azure por segundo|Nenhum|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits a esganar Azure por segundo|Nenhum|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits ingressando Azure por segundo|Nome de ligação|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits a esganar Azure por segundo|Nome de ligação|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QpsByEndpoint|Consultas por Endpoint Devolvidos|Contagem|Total|Número de vezes que um ponto final do Gestor de Tráfego foi devolvido no prazo dado|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Estado do ponto final por ponto final|Contagem|Máximo|1 se o estado da sonda de um ponto final for "Ativado", 0 caso contrário.|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ProbesFailedPercent|% sondas falhadas|Percentagem|Média|% das sondas de monitorização da conectividade falharam|Nenhum|
|AverageRoundtripMs|Avg. Tempo de ida e volta (ms)|MilliSeconds|Média|Tempo médio de ida e volta da rede (MS) para sondas de monitorização de conectividade enviadas entre fonte e destino|Nenhum|
|ChequesFailedPercent|Cheques Por cento falhado (Pré-visualização)|Percentagem|Média|% dos controlos de controlo da conectividade falharam|Endereço fonte,Nome de origem,SourceResourceID,SourceType,Protocol,DestinationAddress,DestinationName,DestinationResourceid,DestinationType,Destinationport,TestGroupName,TestConfigurationName|
|RoundTripTimeMs|Tempo de ida e volta (ms) (Pré-visualização)|MilliSeconds|Média|Tempo de ida e volta em milissegundos para os controlos de monitorização da conectividade|Endereço fonte,Nome de origem,SourceResourceID,SourceType,Protocol,DestinationAddress,DestinationName,DestinationResourceid,DestinationType,Destinationport,TestGroupName,TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RequestCount|Número de Pedidos|Contagem|Total|O número de pedidos de clientes servidos pelo representante http/S|httpstatus,httpstatusGroup,ClientRegion,ClientCountry|
|Solicitações|Tamanho do pedido|Bytes|Total|O número de bytes enviados como pedidos de clientes para o proxy HTTP/S|httpstatus,httpstatusGroup,ClientRegion,ClientCountry|
|Tamanho de resposta|Tamanho da resposta|Bytes|Total|O número de bytes enviados como respostas de http/S proxy para clientes|httpstatus,httpstatusGroup,ClientRegion,ClientCountry|
|BillableResponseSize|Tamanho da resposta facturavel|Bytes|Total|O número de bytes faturados (mínimo 2KB por pedido) enviados como respostas de procuração HTTP/S aos clientes.|httpstatus,httpstatusGroup,ClientRegion,ClientCountry|
|BackendRequestCount|Contagem de pedidos de backend|Contagem|Total|O número de pedidos enviados do representante http/S para backends|HttpStatus,HttpStatusGroup,Backend|
|BackendRequestLatency|Latência de Pedido de Backend|MilliSeconds|Média|O tempo calculado a partir do momento em que o pedido foi enviado pelo representante http/S para o backend até que o representante http/s recebeu o último byte de resposta do backend|Backend|
|TotalLatency|Latência Total|MilliSeconds|Média|O tempo calculado a partir do momento em que o pedido do cliente foi recebido pelo representante http/S até que o cliente reconheceu o último byte de resposta do representante HTTP/S|httpstatus,httpstatusGroup,ClientRegion,ClientCountry|
|BackendHealthPercentage|Percentagem de Saúde Endend|Percentagem|Média|A percentagem de sondas de saúde bem sucedidas do proxy HTTP/S para backends|Backend,BackendPool|
|WebApplicationFirewallRequestCount|Contagem de pedidos de firewall de aplicação web|Contagem|Total|O número de pedidos de clientes processados pela Firewall de Aplicação Web|Nome de política,Nome de Regras,Ação|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|registration.all|Operações de Registo|Contagem|Total|A contagem de todas as operações de registo bem sucedidas (criações atualiza consultas e supressões). |Nenhum|
|registration.create|Inscrições Criar Operações|Contagem|Total|A contagem de todas as criações de registo bem sucedidas.|Nenhum|
|registration.update|Operações de Atualização de Registo|Contagem|Total|A contagem de todas as atualizações de registo bem sucedidas.|Nenhum|
|registration.get|Operações de Leitura de Registo|Contagem|Total|A contagem de todas as consultas de registo bem sucedidas.|Nenhum|
|registration.delete|Inscrições Supressão de Operações|Contagem|Total|A contagem de todas as supressões de registo bem sucedidas.|Nenhum|
|entrada|Mensagens de entrada|Contagem|Total|A contagem de todos os bem sucedidos enviar chamadas da API. |Nenhum|
|incoming.scheduled|Notificações push agendadas enviadas|Contagem|Total|Notificações de Push agendadas canceladas|Nenhum|
|incoming.scheduled.cancel|Notificações de Push agendadas canceladas|Contagem|Total|Notificações de Push agendadas canceladas|Nenhum|
|agendado.pendente|Notificações agendadas pendentes|Contagem|Total|Notificações agendadas pendentes|Nenhum|
|instalação.todos|Operações de Gestão de Instalações|Contagem|Total|Operações de Gestão de Instalações|Nenhum|
|installation.get|Obter Operações de Instalação|Contagem|Total|Obter Operações de Instalação|Nenhum|
|installation.upsert|Criar ou atualizar operações de instalação|Contagem|Total|Criar ou atualizar operações de instalação|Nenhum|
|installation.patch|Operações de instalação de patch|Contagem|Total|Operações de instalação de patch|Nenhum|
|installation.delete|Excluir operações de instalação|Contagem|Total|Excluir operações de instalação|Nenhum|
|outgoing.allpns.success|Notificações bem sucedidas|Contagem|Total|A contagem de todas as notificações bem sucedidas.|Nenhum|
|outgoing.allpns.invalidpayload|Erros de carga útil|Contagem|Total|A contagem de empurrões que falhou porque o PNS devolveu um erro de carga útil.|Nenhum|
|outgoing.allpns.pnserror|Erros do sistema de notificação externa|Contagem|Total|A contagem de empurrões que falharam porque havia um problema de comunicação com o PNS (exclui problemas de autenticação).|Nenhum|
|outgoing.allpns.channelerror|Erros do Canal|Contagem|Total|A contagem de empurrões que falharam porque o canal não estava inválido não estava associado à aplicação correta acelerada ou expirada.|Nenhum|
|outgoing.allpns.badorexpiredchannel|Erros no canal mau ou expirado|Contagem|Total|A contagem de empurrões que falharam porque o canal/token/registrationId no registo foi expirado ou inválido.|Nenhum|
|outgoing.wns.success|Notificações bem sucedidas da WNS|Contagem|Total|A contagem de todas as notificações bem sucedidas.|Nenhum|
|outgoing.wns.invalidcredentials|Erros de autorização wns (credenciais inválidas)|Contagem|Total|A contagem de empurrões que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas. (O Windows Live não reconhece as credenciais).|Nenhum|
|outgoing.wns.badchannel|Erro do canal errado wns|Contagem|Total|A contagem de empurrões que falharam porque o ChannelURI no registo não foi reconhecido (estatuto WNS: 404 não encontrados).|Nenhum|
|outgoing.wns.expiredchannel|Erro do canal expirado wns|Contagem|Total|A contagem de empurrões que falharam porque o ChannelURI expirou (estatuto WNS: 410 Gone).|Nenhum|
|outgoing.wns.throttled|Notificações wns throttled|Contagem|Total|A contagem de empurrões que falharam porque a WNS está a estrangular esta aplicação (estatuto WNS: 406 Not Acceptable).|Nenhum|
|outgoing.wns.tokenproviderunreachable|Erros de autorização wns (incontactáveis)|Contagem|Total|O Windows Live não é acessível.|Nenhum|
|outgoing.wns.invalidtoken|Erros de autorização wns (ficha inválida)|Contagem|Total|O símbolo fornecido à WNS não é válido (estatuto WNS: 401 Não autorizado).|Nenhum|
|outgoing.wns.wrongtoken|Erros de autorização wns (ficha errada)|Contagem|Total|O símbolo fornecido à WNS é válido, mas para outro pedido (estatuto WNS: 403 Proibido). Isto pode acontecer se o ChannelURI no registo estiver associado a outra aplicação. Verifique se a aplicação do cliente está associada à mesma aplicação cujas credenciais estão no centro de notificação.|Nenhum|
|outgoing.wns.invalidnotificationformat|Formato de notificação inválido wns|Contagem|Total|O formato da notificação é inválido (estatuto WNS: 400). Note que a WNS não rejeita todas as cargas inválidas.|Nenhum|
|outgoing.wns.invalidnotificationsize|Erro de tamanho de notificação inválido wns|Contagem|Total|A carga útil da notificação é demasiado grande (estado WNS: 413).|Nenhum|
|outgoing.wns.channelthrottled|Canal WNS Throttled|Contagem|Total|A notificação foi retirada porque o ChannelURI no registo é estrangulado (cabeçalho de resposta WNS: X-WNS-NotificationStatus:channelThrottled).|Nenhum|
|outgoing.wns.channeldisconnected|Canal WNS desligado|Contagem|Total|A notificação foi retirada porque o ChannelURI no registo está estrangulado (cabeçalho de resposta WNS: X-WNS-DeviceConnectionStatus: desligado).|Nenhum|
|outgoing.wns.dropped|WNS Deixou notificações|Contagem|Total|A notificação foi retirada porque o ChannelURI no registo está estrangulado (X-WNS-NotificationStatus: retirado, mas não X-WNS-DeviceConnectionStatus: desligado).|Nenhum|
|outgoing.wns.pnserror|Erros wns|Contagem|Total|Notificação não entregue devido a erros de comunicação com a WNS.|Nenhum|
|outgoing.wns.authenticationerror|Erros de autenticação wns|Contagem|Total|Notificação não entregue devido a erros de comunicação com credenciais inválidas do Windows Live ou ficha errada.|Nenhum|
|outgoing.apns.success|Notificações bem sucedidas da APNS|Contagem|Total|A contagem de todas as notificações bem sucedidas.|Nenhum|
|outgoing.apns.invalidcredentials|Erros de autorização da APNS|Contagem|Total|A contagem de empurrões que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhum|
|outgoing.apns.badchannel|Erro do canal bad da APNS|Contagem|Total|A contagem de empurrões que falharam porque o token é inválido (código de estado APNS: 8).|Nenhum|
|outgoing.apns.expiredchannel|Erro do canal expirado APNS|Contagem|Total|A contagem de fichas que foram invalidadas pelo canal de feedback da APNS.|Nenhum|
|outgoing.apns.invalidnotificationsize|Erro de tamanho de notificação inválido da APNS|Contagem|Total|A contagem de empurrões que falharam porque a carga útil era demasiado grande (código de estado APNS: 7).|Nenhum|
|outgoing.apns.pnserror|Erros APNS|Contagem|Total|A contagem de empurrões que falharam devido a erros de comunicação com APNS.|Nenhum|
|outgoing.gcm.success|Notificações bem sucedidas da GCM|Contagem|Total|A contagem de todas as notificações bem sucedidas.|Nenhum|
|outgoing.gcm.invalidcredentials|Erros de autorização GCM (Credenciais inválidas)|Contagem|Total|A contagem de empurrões que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhum|
|outgoing.gcm.badchannel|Erro do canal errado da GCM|Contagem|Total|A contagem de empurrões que falharam porque o registoId no registo não foi reconhecido (resultado GCM: Registo Inválido).|Nenhum|
|outgoing.gcm.expiredchannel|Erro do canal expirado GCM|Contagem|Total|A contagem de empurrões que falharam porque o registoId no registo expirou (resultado GCM: NotRegistered).|Nenhum|
|outgoing.gcm.throttled|Notificações de aceleração GCM|Contagem|Total|A contagem de empurrões que falharam porque o GCM estrangulou esta aplicação (código de estado GCM: 501-599 ou resultado:Indisponível).|Nenhum|
|outgoing.gcm.invalidnotificationformat|Formato de notificação inválida GCM|Contagem|Total|A contagem de empurrões que falharam porque a carga não foi formatada corretamente (resultado GCM: InvalidDataKey ou InvalidTTl).|Nenhum|
|outgoing.gcm.invalidnotificationsize|Erro de tamanho de notificação inválido gcm|Contagem|Total|A contagem de empurrões que falharam porque a carga útil era demasiado grande (resultado gcm: MessageTooBig).|Nenhum|
|outgoing.gcm.wrongchannel|Erro do canal errado GCM|Contagem|Total|A contagem de empurrões que falharam porque o registoId no registo não está associado à aplicação atual (resultado GCM: InvalidPackageName).|Nenhum|
|outgoing.gcm.pnserror|Erros GCM|Contagem|Total|A contagem de empurrões que falharam devido a erros de comunicação com a GCM.|Nenhum|
|outgoing.gcm.authenticationerror|Erros de autenticação GCM|Contagem|Total|A contagem de empurrões que falharam porque o PNS não aceitou as credenciais fornecidas as credenciais são bloqueadas ou o SenderId não está corretamente configurado na aplicação (resultado GCM: MismatchedSenderId).|Nenhum|
|outgoing.mpns.success|Notificações bem sucedidas do MPNS|Contagem|Total|A contagem de todas as notificações bem sucedidas.|Nenhum|
|outgoing.mpns.invalidcredentials|Credenciais de mpns inválidas|Contagem|Total|A contagem de empurrões que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhum|
|outgoing.mpns.badchannel|Erro do Canal Mau mpns|Contagem|Total|A contagem de empurrões que falharam porque o ChannelURI no registo não foi reconhecido (estatuto DE MPNS: 404 não encontrados).|Nenhum|
|outgoing.mpns.throttled|Notificações de aceleração do MPNS|Contagem|Total|A contagem de empurrões que falharam porque o MPNS está a estrangular esta aplicação (WNS MPNS: 406 Not Acceptable).|Nenhum|
|outgoing.mpns.invalidnotificationformat|Formato de notificação inválido do MPNS|Contagem|Total|A contagem de empurrões que falhou porque a carga útil da notificação era demasiado grande.|Nenhum|
|outgoing.mpns.channeldisconnected|Canal MPNS desligado|Contagem|Total|A contagem de empurrões que falharam porque o ChannelURI no registo foi desligado (estatuto DE MPNS: 412 não encontrados).|Nenhum|
|outgoing.mpns.dropped|MpNS Notificações retiradas|Contagem|Total|A contagem de empurrões que foram retiradas por MPNS (cabeçalho de resposta MPNS: X-NotificationStatus: QueueFull ou Suprimido).|Nenhum|
|outgoing.mpns.pnserror|Erros mpns|Contagem|Total|A contagem de empurrões que falharam devido a erros de comunicação com mpNS.|Nenhum|
|outgoing.mpns.authenticationerror|Erros de autenticação do MPNS|Contagem|Total|A contagem de empurrões que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhum|
|notificationhub.pushes|Todas as Notificações De Saída|Contagem|Total|Todas as notificações de saída do centro de notificação|Nenhum|
|incoming.all.requests|Todos os pedidos de entrada|Contagem|Total|Total de pedidos de entrada para um centro de notificação|Nenhum|
|incoming.all.failedrequests|Todos os pedidos falhados de entrada|Contagem|Total|Total de pedidos falhados para um centro de notificação|Nenhum|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Average_% Inodes Grátis|% Inodes livres|Contagem|Média|Average_% Inodes Grátis|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% espaço livre|% Espaço Livre|Contagem|Média|Average_% espaço livre|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% Inodes usados|% Inodos Usados|Contagem|Média|Average_% Inodes usados|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% Espaço Usado|% De espaço utilizado|Contagem|Média|Average_% Espaço Usado|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Disk Ler Bytes/seg|Bytes Lidos de Disco/seg|Contagem|Média|Average_Disk Ler Bytes/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|leituras Average_Disk/seg|Leituras de Disco/seg|Contagem|Média|leituras Average_Disk/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|transferências Average_Disk/seg|As transferências de disco/seg|Contagem|Média|transferências Average_Disk/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Disk Escrever Bytes/seg|Bytes Escritos em Disco/seg|Contagem|Média|Average_Disk Escrever Bytes/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Disk Escreve/seg|Escritas em disco/seg|Contagem|Média|Average_Disk Escreve/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Free Megabytes|Megabytes livres|Contagem|Média|Average_Free Megabytes|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Logical Bytes/seg de disco|Bytes de disco lógico/seg|Contagem|Média|Average_Logical Bytes/seg de disco|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|memória disponível Average_%|% Memória Disponível|Contagem|Média|memória disponível Average_%|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% espaço disponível de swap|% Espaço de Troca Disponível %|Contagem|Média|Average_% espaço disponível de swap|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% memória usada|% Memória Usada|Contagem|Média|Average_% memória usada|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% espaço de troca usado|% Espaço de troca usado %|Contagem|Média|Average_% espaço de troca usado|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|memória Average_Available MBytes|MBytes de memória disponíveis|Contagem|Média|memória Average_Available MBytes|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Available Troca de MBytes|Troca de MBytes disponíveis|Contagem|Média|Average_Available Troca de MBytes|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Page Leituras/seg|Leituras de Paginações/seg|Contagem|Média|Average_Page Leituras/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Page Escreve/seg|Escritas de Paginações/seg|Contagem|Média|Average_Page Escreve/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Pages/seg|Páginas/seg|Contagem|Média|Average_Pages/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Used MBytes Swap Space|Espaço de troca de MBytes usado|Contagem|Média|Average_Used MBytes Swap Space|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Used Memória MBytes|MBytes de memória usada|Contagem|Média|Average_Used Memória MBytes|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Total Bytes Transmitidos|Total de Bytes transmitidos|Contagem|Média|Average_Total Bytes Transmitidos|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Total Bytes Recebidos|Total de Bytes recebidos|Contagem|Média|Average_Total Bytes Recebidos|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Total Bytes|Bytes Totais|Contagem|Média|Average_Total Bytes|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|pacotes de Average_Total transmitidos|Pacotes totais transmitidos|Contagem|Média|pacotes de Average_Total transmitidos|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|pacotes Average_Total recebidos|Total de pacotes recebidos|Contagem|Média|pacotes Average_Total recebidos|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Total Erros Rx|Total de Erros Rx|Contagem|Média|Average_Total Erros Rx|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|erros Average_Total Tx|Total de erros Tx|Contagem|Média|erros Average_Total Tx|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|colisões Average_Total|Colisões totais|Contagem|Média|colisões Average_Total|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Avg. de Disco seg/Leitura|Avg. Disk sec/Read|Contagem|Média|Average_Avg. de Disco seg/Leitura|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Avg. de seg de Disco/Transferência|Avg. Disco sec/Transferência|Contagem|Média|Average_Avg. de seg de Disco/Transferência|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Avg. de Disco seg/Escrita|Avg. Disk sec/Write|Contagem|Média|Average_Avg. de Disco seg/Escrita|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Physical Bytes/seg de disco|Bytes/sede de disco físico|Contagem|Média|Average_Physical Bytes/seg de disco|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Pct Tempo Privilegiado|Tempo Privilegiado do Pct|Contagem|Média|Average_Pct Tempo Privilegiado|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|tempo de utilizador Average_Pct|Tempo de utilizador do Pct|Contagem|Média|tempo de utilizador Average_Pct|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Used Memory kBytes|KBytes de memória usada|Contagem|Média|Average_Used Memory kBytes|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|memória partilhada Average_Virtual|Memória Partilhada Virtual|Contagem|Média|memória partilhada Average_Virtual|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% de tempo dPC|% Tempo dPC|Contagem|Média|Average_% de tempo dPC|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% tempo de inatividade|% Tempo de inatividade|Contagem|Média|Average_% tempo de inatividade|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% de tempo de interrupção|% tempo de interrupção|Contagem|Média|Average_% de tempo de interrupção|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% IO Tempo de espera|% Io Tempo de Espera|Contagem|Média|Average_% IO Tempo de espera|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% bom tempo|% Bom Tempo|Contagem|Média|Average_% bom tempo|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% tempo privilegiado|% Tempo Privilegiado|Contagem|Média|Average_% tempo privilegiado|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% tempo do processador|% de tempo do processador|Contagem|Média|Average_% tempo do processador|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% tempo de utilizador|% Tempo de utilizador|Contagem|Média|Average_% tempo de utilizador|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|memória física Average_Free|Memória Física Gratuita|Contagem|Média|memória física Average_Free|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|espaço Average_Free em arquivos de paging|Espaço gratuito em arquivos de paging|Contagem|Média|espaço Average_Free em arquivos de paging|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Free Memória Virtual|Memória Virtual Gratuita|Contagem|Média|Average_Free Memória Virtual|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Processes|Processos|Contagem|Média|Average_Processes|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Size armazenado em arquivos de paging|Tamanho armazenado em arquivos de paging|Contagem|Média|Average_Size armazenado em arquivos de paging|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Uptime|Período de atividade|Contagem|Média|Average_Uptime|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Users|Utilizadores|Contagem|Média|Average_Users|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Avg. de Disco seg/Leitura|Avg. Disk sec/Read|Contagem|Média|Average_Avg. de Disco seg/Leitura|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Avg. de Disco seg/Escrita|Avg. Disk sec/Write|Contagem|Média|Average_Avg. de Disco seg/Escrita|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|comprimento da fila do disco de Average_Current|Comprimento da fila do disco atual|Contagem|Média|comprimento da fila do disco de Average_Current|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|leituras Average_Disk/seg|Leituras de Disco/seg|Contagem|Média|leituras Average_Disk/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|transferências Average_Disk/seg|As transferências de disco/seg|Contagem|Média|transferências Average_Disk/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Disk Escreve/seg|Escritas em disco/seg|Contagem|Média|Average_Disk Escreve/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Free Megabytes|Megabytes livres|Contagem|Média|Average_Free Megabytes|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% espaço livre|% Espaço Livre|Contagem|Média|Average_% espaço livre|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Available MBytes|MBytes disponíveis|Contagem|Média|Average_Available MBytes|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% bytes comprometidos em uso|% Bytes Comprometidos em Uso|Contagem|Média|Average_% bytes comprometidos em uso|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Bytes recebido/seg|Bytes recebidos/seg|Contagem|Média|Average_Bytes recebido/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Bytes Enviado/seg|Bytes enviados/seg|Contagem|Média|Average_Bytes Enviado/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Bytes Total/seg|Bytes Total/seg|Contagem|Média|Average_Bytes Total/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% tempo do processador|% de tempo do processador|Contagem|Média|Average_% tempo do processador|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|comprimento da fila Average_Processor|Comprimento da fila do processador|Contagem|Média|comprimento da fila Average_Processor|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Heartbeat|Heartbeat|Contagem|Total|Heartbeat|Computador,OSType,Versão,SourceComputerId|
|Atualizar|Atualizar|Contagem|Média|Atualizar|Computador,Produto,Classificação,Estado de Atualização,Opcional,Aprovado|
|Evento|Evento|Contagem|Média|Evento|Fonte,EventLog,Computador,Categoria de Eventos,EventLevel,EventLevelName,EventID|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft.Peering/peeringServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PrefixoLatency|Prefixo Latência|Milissegundos|Média|Latência prefixo mediana|PrefixoNome|

## <a name="microsoftpeeringpeerings"></a>Microsoft.Peering/peerings

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SessõesDisponibilidadeV4|Disponibilidade da Sessão V4|Percentagem|Média|Disponibilidade da sessão V4|Ligação|
|SessõesDisponibilidadeV6|Disponibilidade da Sessão V6|Percentagem|Média|Disponibilidade da sessão V6|Ligação|
|Taxa de tráfego ingresso|Taxa de tráfego de ingresso|BitsPerSecond|Média|Taxa de tráfego ingresso em bits por segundo|Ligação|
|EgressTrafficRate|Taxa de tráfego de egress|BitsPerSecond|Média|Taxa de tráfego de egress em bits por segundo|Ligação|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueryDuration|Duração da consulta|Milissegundos|Média|Dax Consulta duração no último intervalo|Nenhuma dimensão|
|QueryPoolJobQueueLength|Threads: Consulta de trabalho na piscina comprimento da fila|Contagem|Média|Número de empregos na fila da piscina de fios de consulta.|Nenhuma dimensão|
|qpu_high_utilization_metric|Alta Utilização qpu|Contagem|Total|Utilização alta qpu em última hora, 1 para alta utilização de QPU, caso contrário 0|Nenhuma dimensão|
|memory_metric|Memória|Bytes|Média|A memória. Intervalo 0-3 GB para A1, 0-5 GB para A2, 0-10 GB para A3, 0-25 GB para A4, 0-50 GB para A5 e 0-100 GB para A6|Nenhuma dimensão|
|memory_thrashing_metric|Goleada de memória|Percentagem|Média|A memória média a bater.|Nenhuma dimensão|



## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Contagem|Total|Sucesso ListenerConnections para Microsoft.Relay.|Nome de entidade,OperationResult|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Contagem|Total|Error do cliente em ListenerConnections para Microsoft.Relay.|Nome de entidade,OperationResult|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Contagem|Total|Error do servidor em ListenerConnections para Microsoft.Relay.|Nome de entidade,OperationResult|
|SenderConnections-Success|SenderConnections-Success|Contagem|Total|SenderConnections bem sucedido para Microsoft.Relay.|Nome de entidade,OperationResult|
|SenderConnections-ClientError|SenderConnections-ClientError|Contagem|Total|Error do cliente em SenderConnections para Microsoft.Relay.|Nome de entidade,OperationResult|
|SenderConnections-ServerError|SenderConnections-ServerError|Contagem|Total|Error do servidor em SenderConnections para Microsoft.Relay.|Nome de entidade,OperationResult|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Contagem|Total|Total ListenerConnections para Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Contagem|Total|Total de pedidos de SenderConnections para Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Contagem|Total|Total ActiveConnections para Microsoft.Relay.|EntityName|
|Ouvintes Ativos|Ouvintes Ativos|Contagem|Total|Total ActiveListeners para Microsoft.Relay.|EntityName|
|BytesTransferido|BytesTransferido|Contagem|Total|Total bytesTransferido para Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Contagem|Total|Total ListenerDisconnects para Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Contagem|Total|Total de SenderDisconnects para Microsoft.Relay.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SearchLatency|Latência de pesquisa|Segundos|Média|Latência média de pesquisa para o serviço de pesquisa|Nenhum|
|SearchQueriesPerSecond|Pesquisar consultas por segundo|CountPerSecond|Média|Pesquisar consultas por segundo para o serviço de pesquisa|Nenhum|
|ThrottledSearchQueriesPercentage|Percentagem de consultas de pesquisa acelerada|Percentagem|Média|Percentagem de consultas de pesquisa que foram estranguladas para o serviço de pesquisa|Nenhum|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Pedidos bem sucedidos|Pedidos bem sucedidos|Contagem|Total|Total de pedidos bem sucedidos para um espaço de nome|Nome de entidade,OperationResult|
|Erros de servidor|Erros do servidor.|Contagem|Total|Erros de servidor para Microsoft.ServiceBus.|Nome de entidade,OperationResult|
|Erros de utilizador|Erros do utilizador.|Contagem|Total|Erros de utilizador para Microsoft.ServiceBus.|Nome de entidade,OperationResult|
|ThrottledRequests|Pedidos estrangulados.|Contagem|Total|Pedidos throttled para Microsoft.ServiceBus.|Nome de entidade,OperationResult|
|IncomingRequests|Pedidos de Entrada|Contagem|Total|Pedidos de entrada para Microsoft.ServiceBus.|EntityName|
|IncomingMessages|Mensagens de entrada|Contagem|Total|Mensagens de entrada para Microsoft.ServiceBus.|EntityName|
|OutgoingMessages|Mensagens de saída|Contagem|Total|Mensagens de saída para Microsoft.ServiceBus.|EntityName|
|ActiveConnections|ActiveConnections|Contagem|Total|Total de conexões ativas para Microsoft.ServiceBus.|Nenhum|
|Conexões Abertas|Ligações abertas.|Contagem|Média|Ligações abertas para Microsoft.ServiceBus.|EntityName|
|Conexões Fechadas|Ligações fechadas.|Contagem|Média|Ligações fechadas para Microsoft.ServiceBus.|EntityName|
|Tamanho|Tamanho|Bytes|Média|Tamanho de uma fila/tópico em Bytes.|EntityName|
|Mensagens|Contagem de mensagens numa fila/tópico.|Contagem|Média|Contagem de mensagens numa fila/tópico.|EntityName|
|ActiveMessages|Contagem de mensagens ativas numa fila/tópico.|Contagem|Média|Contagem de mensagens ativas numa fila/tópico.|EntityName|
|DeadletteredMessages|Conde de mensagens com letras mortas numa fila/tópico.|Contagem|Média|Conde de mensagens com letras mortas numa fila/tópico.|EntityName|
|Mensagens Agendadas|Contagem de mensagens programadas numa fila/tópico.|Contagem|Média|Contagem de mensagens programadas numa fila/tópico.|EntityName|
|NomespaceCpuUsage|CPU|Percentagem|Máximo|Métrica de utilização de CPU premium de ônibus de serviço.|Réplica|
|Uso do espaço de nomeMemory|Utilização de Memória|Percentagem|Máximo|Métrica de utilização de memória de espaço premium de ônibus de serviço.|Réplica|
|CPUXNS|CPU (Depreciado)|Percentagem|Máximo|Métrica de utilização de CPU premium de ônibus de serviço. Esta métrica está deserdada. Utilize a métrica CPU (NamespaceCpuUsage) em vez disso.|Réplica|
|WSXNS|Utilização da memória (Depreciada)|Percentagem|Máximo|Métrica de utilização de memória de espaço premium de ônibus de serviço. Esta métrica está depreciada. Em vez disso, utilize a métrica de utilização da memória (NamespaceMemoryUsage).|Réplica|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Cpu atribuído|Cpu atribuído|Contagem|Média|Cpu alocado a este contentor em núcleos milli|Nome de aplicação,Nome de serviço,CódigoPacoteNome,Nome de Replica-Serviço|
|Memória Atribuída|Memória Atribuída|Bytes|Média|Memória atribuída a este recipiente em MB|Nome de aplicação,Nome de serviço,CódigoPacoteNome,Nome de Replica-Serviço|
|ActualCpu|ActualCpu|Contagem|Média|Utilização real do CPU em núcleos de milimil|Nome de aplicação,Nome de serviço,CódigoPacoteNome,Nome de Replica-Serviço|
|Memória Real|Memória Real|Bytes|Média|Uso real da memória em MB|Nome de aplicação,Nome de serviço,CódigoPacoteNome,Nome de Replica-Serviço|
|CpuUtilização|CpuUtilização|Percentagem|Média|Utilização do CPU para este contentor em percentagem do AllocatedCpu|Nome de aplicação,Nome de serviço,CódigoPacoteNome,Nome de Replica-Serviço|
|Utilização da Memória|Utilização da Memória|Percentagem|Média|Utilização do CPU para este contentor em percentagem do AllocatedCpu|Nome de aplicação,Nome de serviço,CódigoPacoteNome,Nome de Replica-Serviço|
|Estatuto de Aplicação|Estatuto de Aplicação|Contagem|Média|Aplicação de malha de tecido de serviço|Nome de aplicação,Estado|
|ServiceStatus|ServiceStatus|Contagem|Média|Estado de Saúde de um serviço na aplicação de malha de tecido de serviço|Nome de aplicação,Status,Nome de serviço|
|ServiceReplicaStatus|ServiceReplicaStatus|Contagem|Média|Estado de Saúde de uma réplica de serviço na aplicação de malha de tecido de serviço|Nome de aplicação,Status,ServiceName,ServiceReplicaName|
|Estatuto de Contentores|Estatuto de Contentores|Contagem|Média|Estado do recipiente na aplicação de malha de tecido de serviço|Nome de aplicação,Nome de serviço,CódigoPacoteNome,Nome de Replicação de Serviço,Status|
|Reiniciar Contagem|Reiniciar Contagem|Contagem|Média|Reinício da contagem de um recipiente na aplicação de malha de tecido de serviço|Nome de aplicação,Status,ServiceName,ServiceReplicaName,CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ConnectionCount|Contagem de Ligação|Contagem|Máximo|A quantidade de ligação do utilizador.|Ponto Final|
|MessageCount|Contagem de mensagens|Contagem|Total|A quantidade total de mensagens.|Nenhum|
|InboundTraffic|Tráfego de Entrada|Bytes|Total|O tráfego de entrada de serviço|Nenhum|
|OutboundTraffic|Tráfego de Saída|Bytes|Total|O tráfego de saída do serviço|Nenhum|
|Erros de utilizador|Erros do utilizador|Percentagem|Máximo|A percentagem de erros do utilizador|Nenhum|
|Erros de sistema|Erros do sistema|Percentagem|Máximo|A percentagem de erros do sistema|Nenhum|





## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Nenhum|
|physical_data_read_percent|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|Nenhum|
|log_write_percent|Taxa de Log IO|Percentagem|Média|Taxa de iO de log. Não aplicável aos armazéns de dados.|Nenhum|
|dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|Percentagem DTU. Aplica-se às bases de dados baseadas em DTU.|Nenhum|
|armazenamento|Espaço de dados utilizado|Bytes|Máximo|Espaço de dados usado. Não aplicável aos armazéns de dados.|Nenhum|
|connection_successful|Conexões bem sucedidas|Contagem|Total|Conexões bem sucedidas|Nenhum|
|connection_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Nenhum|
|blocked_by_firewall|Bloqueado por Firewall|Contagem|Total|Bloqueado por Firewall|Nenhum|
|impasse|Impasses|Contagem|Total|Impasses. Não aplicável aos armazéns de dados.|Nenhum|
|storage_percent|Espaço de dados usado por cento|Percentagem|Máximo|Espaço de dados usado por cento. Não aplicável a armazéns de dados ou bases de dados de hiperescala.|Nenhum|
|xtp_storage_percent|Por cento de armazenamento OLTP em Memória|Percentagem|Média|In-Memory OLTP storage por cento. Não aplicável aos armazéns de dados.|Nenhum|
|workers_percent|Percentagem de trabalhadores|Percentagem|Média|Percentagem de trabalhadores. Não aplicável aos armazéns de dados.|Nenhum|
|sessions_percent|Percentagem de sessões|Percentagem|Média|Percentagem de sessões. Não aplicável aos armazéns de dados.|Nenhum|
|dtu_limit|Limite dTU|Contagem|Média|Limite DTU. Aplica-se às bases de dados baseadas em DTU.|Nenhum|
|dtu_used|DTU usado|Contagem|Média|DTU usado. Aplica-se às bases de dados baseadas em DTU.|Nenhum|
|cpu_limit|Limite de CPU|Contagem|Média|Limite de CPU. Aplica-se às bases de dados baseadas em vCore.|Nenhum|
|cpu_used|CPU utilizado|Contagem|Média|CPU usado. Aplica-se às bases de dados baseadas em vCore.|Nenhum|
|dwu_limit|Limite de DWU|Contagem|Máximo|Limite de DWU. Aplica-se apenas aos armazéns de dados.|Nenhum|
|dwu_consumption_percent|Percentagem de DWU|Percentagem|Máximo|Percentagem de DWU. Aplica-se apenas aos armazéns de dados.|Nenhum|
|dwu_used|DWU usado|Contagem|Máximo|DWU usado. Aplica-se apenas aos armazéns de dados.|Nenhum|
|cache_hit_percent|Percentagem de impacto cache|Percentagem|Máximo|Cache atingiu a percentagem. Aplica-se apenas aos armazéns de dados.|Nenhum|
|cache_used_percent|Cache usado percentagem|Percentagem|Máximo|Cache usado percentagem. Aplica-se apenas aos armazéns de dados.|Nenhum|
|sqlserver_process_core_percent|SQL Server núcleo de processo por cento|Percentagem|Máximo|Percentagem de utilização do CPU para o processo Do Servidor SQL, medida pelo sistema operativo. Atualmente disponível apenas para bases de dados sem servidores.|Nenhum|
|sqlserver_process_memory_percent|SQL Server processa por cento da memória|Percentagem|Máximo|Percentagem de utilização da memória para o processo Do Servidor SQL, medida pelo sistema operativo. Atualmente disponível apenas para bases de dados sem servidores.|Nenhum|
|tempdb_data_size|Kilobytes de tamanho de ficheiro de dados tempdb|Contagem|Máximo|Kilobytes de tamanho de ficheiro de dados tempdb. Não aplicável aos armazéns de dados.|Nenhum|
|tempdb_log_size|Kilobytes de tamanho de ficheiro de log tempdb|Contagem|Máximo|Kilobytes de ficheiro de log tempdb. Não aplicável aos armazéns de dados.|Nenhum|
|tempdb_log_used_percent|Log por cento temporário usado|Percentagem|Máximo|Registo de percentagens temporárias usado. Não aplicável aos armazéns de dados.|Nenhum|
|local_tempdb_usage_percent|Percentagem de temperatura local|Percentagem|Média|Percentagem de temperatura local. Aplica-se apenas aos armazéns de dados.|Nenhum|
|app_cpu_billed|App CPU faturada|Contagem|Total|App CPU faturado. Aplica-se a bases de dados sem servidores.|Nenhum|
|app_cpu_percent|Percentagem de CPU da aplicação|Percentagem|Média|Percentagem de CPU da aplicação. Aplica-se a bases de dados sem servidores.|Nenhum|
|app_memory_percent|Percentagem de memória de aplicativos|Percentagem|Média|Percentagem de memória de aplicativos. Aplica-se a bases de dados sem servidores.|Nenhum|
|allocated_data_storage|Espaço de dados atribuído|Bytes|Média|Armazenamento de dados atribuídos. Não aplicável aos armazéns de dados.|Nenhum|
|memory_usage_percent|Percentagem de memória|Percentagem|Máximo|Percentagem de memória. Aplica-se apenas aos armazéns de dados.|Nenhum|
|full_backup_size_bytes|Tamanho total do armazenamento de backup|Bytes|Máximo|Tamanho acumulado de armazenamento de reserva completa. Aplica-se às bases de dados baseadas em vCore. Não aplicável às bases de dados de Hiperescala.|Nenhum|
|diff_backup_size_bytes|Tamanho diferencial de armazenamento de backup|Bytes|Máximo|Tamanho acumulado de armazenamento de backup diferencial. Aplica-se às bases de dados baseadas em vCore. Não aplicável às bases de dados de Hiperescala.|Nenhum|
|log_backup_size_bytes|Registar tamanho de armazenamento de backup|Bytes|Máximo|Tamanho acumulado de armazenamento de backup de registo. Aplica-se às bases de dados baseadas em vCore. Não aplicável às bases de dados de Hiperescala.|Nenhum|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Nenhum|
|database_cpu_percent|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Base de Dados Resourceid|
|physical_data_read_percent|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|Nenhum|
|database_physical_data_read_percent|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|Base de Dados Resourceid|
|log_write_percent|Taxa de Log IO|Percentagem|Média|Taxa de Log IO|Nenhum|
|database_log_write_percent|Taxa de Log IO|Percentagem|Média|Taxa de Log IO|Base de Dados Resourceid|
|dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|Percentagem DTU. Aplica-se a piscinas elásticas baseadas em DTU.|Nenhum|
|database_dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|Percentagem de DTU|Base de Dados Resourceid|
|storage_percent|Espaço de dados usado por cento|Percentagem|Média|Espaço de dados usado por cento|Nenhum|
|workers_percent|Percentagem de trabalhadores|Percentagem|Média|Percentagem de trabalhadores|Nenhum|
|database_workers_percent|Percentagem de trabalhadores|Percentagem|Média|Percentagem de trabalhadores|Base de Dados Resourceid|
|sessions_percent|Percentagem de sessões|Percentagem|Média|Percentagem de sessões|Nenhum|
|database_sessions_percent|Percentagem de sessões|Percentagem|Média|Percentagem de sessões|Base de Dados Resourceid|
|eDTU_limit|limite eDTU|Contagem|Média|limite eDTU. Aplica-se a piscinas elásticas baseadas em DTU.|Nenhum|
|storage_limit|Tamanho máximo de dados|Bytes|Média|Tamanho máximo de dados|Nenhum|
|eDTU_used|eDTU utilizado|Contagem|Média|eDTU usado. Aplica-se a piscinas elásticas baseadas em DTU.|Nenhum|
|database_eDTU_used|eDTU utilizado|Contagem|Média|eDTU utilizado|Base de Dados Resourceid|
|storage_used|Espaço de dados utilizado|Bytes|Média|Espaço de dados utilizado|Nenhum|
|database_storage_used|Espaço de dados utilizado|Bytes|Média|Espaço de dados utilizado|Base de Dados Resourceid|
|xtp_storage_percent|Por cento de armazenamento OLTP em Memória|Percentagem|Média|Por cento de armazenamento OLTP em Memória|Nenhum|
|cpu_limit|Limite de CPU|Contagem|Média|Limite de CPU. Aplica-se a piscinas elásticas baseadas em vCore.|Nenhum|
|database_cpu_limit|Limite de CPU|Contagem|Média|Limite de CPU|Base de Dados Resourceid|
|cpu_used|CPU utilizado|Contagem|Média|CPU usado. Aplica-se a piscinas elásticas baseadas em vCore.|Nenhum|
|database_cpu_used|CPU utilizado|Contagem|Média|CPU utilizado|Base de Dados Resourceid|
|sqlserver_process_core_percent|SQL Server núcleo de processo por cento|Percentagem|Máximo|Utilização de CPU em percentagem do processo SQL DB. Aplica-se a piscinas elásticas.|Nenhum|
|sqlserver_process_memory_percent|SQL Server processa por cento da memória|Percentagem|Máximo|Utilização da memória em percentagem do processo SQL DB. Aplica-se a piscinas elásticas.|Nenhum|
|tempdb_data_size|Kilobytes de tamanho de ficheiro de dados tempdb|Contagem|Máximo|Kilobytes de tamanho de ficheiro de dados tempdb|Nenhum|
|tempdb_log_size|Kilobytes de tamanho de ficheiro de log tempdb|Contagem|Máximo|Kilobytes de tamanho de ficheiro de log tempdb|Nenhum|
|tempdb_log_used_percent|Log por cento temporário usado|Percentagem|Máximo|Log por cento temporário usado|Nenhum|
|allocated_data_storage|Espaço de dados atribuído|Bytes|Média|Espaço de dados atribuído|Nenhum|
|database_allocated_data_storage|Espaço de dados atribuído|Bytes|Média|Espaço de dados atribuído|Base de Dados Resourceid|
|allocated_data_storage_percent|Espaço de dados atribuído por cento|Percentagem|Máximo|Espaço de dados atribuído por cento|Nenhum|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|Percentagem de DTU|ElasticPoolResourceId|
|database_dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|Percentagem de DTU|Base de DadosResourceid,ElasticPoolResourceid|
|storage_used|Espaço de dados utilizado|Bytes|Média|Espaço de dados utilizado|ElasticPoolResourceId|
|database_storage_used|Espaço de dados utilizado|Bytes|Média|Espaço de dados utilizado|Base de DadosResourceid,ElasticPoolResourceid|
|dtu_used|DTU usado|Contagem|Média|DTU usado|Base de Dados Resourceid|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|virtual_core_count|Contagem de núcleos virtuais|Contagem|Média|Contagem de núcleos virtuais|Nenhum|
|avg_cpu_percent|Percentagem média de CPU|Percentagem|Média|Percentagem média de CPU|Nenhum|
|reserved_storage_mb|Espaço de armazenamento reservado|Contagem|Média|Espaço de armazenamento reservado|Nenhum|
|storage_space_used_mb|Espaço de armazenamento usado|Contagem|Média|Espaço de armazenamento usado|Nenhum|
|io_requests|IO pedidos contam|Contagem|Média|IO pedidos contam|Nenhum|
|io_bytes_read|IO bytes lidos|Bytes|Média|IO bytes lidos|Nenhum|
|io_bytes_written|IO bytes escritos|Bytes|Média|IO bytes escritos|Nenhum|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|UsedCapacity|Capacidade utilizada|Bytes|Média|Capacidade da conta utilizada|Nenhum|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|Tipo de resposta,GeoType,Nome ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BlobCapacity|Capacidade do Blob|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Blob a conta de armazenamento, em bytes.|BlobType,Tier|
|BlobCount|Contagem de Blobs|Contagem|Média|O número de blobs no serviço Blob da conta de armazenamento.|BlobType,Tier|
|ContainerCount|Contagem do Contentor de Blobs|Contagem|Média|O número de contentores no serviço Blob da conta de armazenamento.|Nenhum|
|IndexCapacity|Capacidade indexada|Bytes|Média|A quantidade de armazenamento utilizada pelo Índice ADLS Gen2 (Hierárquico) em bytes.|Nenhum|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|Tipo de resposta,GeoType,Nome ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TableCapacity|Capacidade de Tabelas|Bytes|Média|A quantidade de armazenamento utilizado pelo serviço Tabela da conta de armazenamento, em bytes.|Nenhum|
|TableCount|Contagem de Tabelas|Contagem|Média|O número de tabelas no serviço Tabela da conta de armazenamento.|Nenhum|
|TableEntityCount|Contagem de Entidade de Tabelas|Contagem|Média|O número de entidades de tabelas no serviço Tabela da conta de armazenamento.|Nenhum|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|Tipo de resposta,GeoType,Nome ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Capacidade de Arquivo|Capacidade de arquivo|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço de arquivos da conta de armazenamento em bytes.|FileShare|
|FileCount|Contagem de Ficheiros|Contagem|Média|O número de ficheiros no serviço de ficheiros da conta de armazenamento.|FileShare|
|FileShareCount|Contagem de partilha de ficheiros|Contagem|Média|O número de ações de ficheiros no serviço de ficheiros da conta de armazenamento.|Nenhum|
|FileShareSnapshotCount|Contagem de instantâneos de partilha de ficheiros|Contagem|Média|O número de instantâneos presentes na parte no Serviço de Ficheiros da conta de armazenamento.|FileShare|
|FileShareSnapshotSize|Tamanho instantâneo de partilha de ficheiros|Bytes|Média|A quantidade de armazenamento utilizada pelos instantâneos no serviço de ficheiros da conta de armazenamento em bytes.|FileShare|
|FileShareQuota|Tamanho da quota de partilha de ficheiros|Bytes|Média|O limite superior da quantidade de armazenamento que pode ser usado pelo Serviço de Ficheiros Azure em bytes.|FileShare|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|Tipo de resposta,GeoType,ApiName,Autenticação,FileShare|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação,FileShare|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação,FileShare|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType,ApiName,Autenticação,FileShare|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação,FileShare|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação,FileShare|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueueCapacity|Capacidade de Fila|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Fila a conta de armazenamento, em bytes.|Nenhum|
|QueueCount|Contagem de Filas|Contagem|Média|O número de filas no serviço Fila da conta de armazenamento.|Nenhum|
|QueueMessageCount|Contagem de Mensagens em Fila|Contagem|Média|O número aproximado de mensagens em fila no serviço Fila da conta de armazenamento.|Nenhum|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão ResponseType para o número de tipos de resposta diferente.|Tipo de resposta,GeoType,Nome ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média utilizada pelo armazenamento do Azure para processar um pedido com êxito, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponto-a-ponto média de pedidos com êxito efetuados a um serviço de armazenamento ou a operação de API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|





## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/caches

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ClientiOPS|IOPS total do cliente|Contagem|Média|A taxa de operações de ficheiros de clientes processadas pelo Cache.|Nenhum|
|ClienteLatency|Latência média do cliente|Milissegundos|Média|Latência média das operações de ficheiros do cliente para o Cache de Armazenamento.|Nenhum|
|ClientReadIOPS|Cliente ler IOPS|CountPerSecond|Média|O cliente lê operações por segundo.|Nenhum|
|ClientReadThroughput|Média de leitura de cache|BytesPerSecond|Média|O cliente leu a taxa de transferência de dados.|Nenhum|
|ClientWriteIOPS|IOPS de escrita de cliente|CountPerSecond|Média|Cliente escreve operações por segundo.|Nenhum|
|ClientWriteThroughput|Média cache write por ção|BytesPerSecond|Média|Cliente escrever taxa de transferência de dados.|Nenhum|
|Metadados do ClienteReadIOPS|Metadados do Cliente Ler IOPS|CountPerSecond|Média|A taxa de operações de ficheiros de clientes enviadas para o Cache, excluindo as leituras de dados, que não modificam o estado persistente.|Nenhum|
|ClientesMetadosWriteIOPS|Metadados de clientes Escrevem IOPS|CountPerSecond|Média|A taxa de operações de ficheiros de clientes enviadas para o Cache, excluindo os dados escritos, que modificam o estado persistente.|Nenhum|
|ClientLockiOPS|IOPS de bloqueio de cliente|CountPerSecond|Média|Operações de bloqueio de ficheiros de cliente por segundo.|Nenhum|
|StorageTargetHealth|Saúde alvo de armazenamento|Contagem|Média|Resultados booleanos do teste de conectividade entre os Alvos de Cache e Armazenamento.|Nenhum|
|Período de atividade|Período de atividade|Contagem|Média|Resultados booleanos do teste de conectividade entre o Cache e o sistema de monitorização.|Nenhum|
|Alvos de armazenamento|IOPS total de armazenamento|Contagem|Média|A taxa de todas as operações de ficheiroque o Cache envia para um determinado StorageTarget.|ArmazenamentoTarget|
|StorageTargetWriteIOPS|ArmazenamentoTarget Escrever IOPS|Contagem|Média|A taxa das operações de escrita de ficheiros que o Cache envia para um determinado StorageTarget.|ArmazenamentoTarget|
|StorageTargetAsyncWriteThroughput|StorageTarget Asynchronous Write Throughput|BytesPerSecond|Média|A taxa que o Cache asincronizamente escreve dados para um determinado StorageTarget. São escritos oportunistas que não fazem com que os clientes bloqueiem.|ArmazenamentoTarget|
|StorageTargetSyncWriteThroughput|StorageTarget Synchronous Write Throughput|BytesPerSecond|Média|A taxa que o Cache escreve sincronizadamente os dados para um determinado StorageTarget. Estes são escritos que fazem com que os clientes bloqueiem.|ArmazenamentoTarget|
|StorageTargetTotalWriteThroughput|StorageTarget Total Write Throughput|BytesPerSecond|Média|A taxa total que o Cache escreve dados para um determinado StorageTarget.|ArmazenamentoTarget|
|ArmazenamentoTargetLatency|Latência StorageTarget|Milissegundos|Média|A latência média de ida e volta de todas as operações de arquivo que o Cache envia para um Armazenamento particularTarget.|ArmazenamentoTarget|
|ArmazenamentoTargetMetasLeiaIOPS|ArmazenamentoTarget Metadados Ler IOPS|CountPerSecond|Média|A taxa de operações de ficheiros que não modificam o estado persistente, excluindo a operação de leitura, que o Cache envia para um determinado StorageTarget.|ArmazenamentoTarget|
|ArmazenamentoTargetMetadataWriteIOPS|ArmazenamentoTarget Metadados Escrevam IOPS|CountPerSecond|Média|A taxa de operações de ficheiros que modificam o estado persistente e excluindo a operação de escrita, que o Cache envia para um determinado StorageTarget.|ArmazenamentoTarget|
|ArmazenamentoTargetReadIOPS|ArmazenamentoTarget Ler IOPS|CountPerSecond|Média|A taxa de operações de leitura de ficheiros que o Cache envia para um determinado StorageTarget.|ArmazenamentoTarget|
|StorageTargetReadAheadThroughput|ArmazenamentoTarget Ler à frente|BytesPerSecond|Média|A taxa que o Cache lê oportunisticamente os dados do StorageTarget.|ArmazenamentoTarget|
|StorageTargetFillThroughput|ArmazenamentoTarget Fill Ingput|BytesPerSecond|Média|A taxa que o Cache lê os dados do StorageTarget para lidar com uma falha de cache.|ArmazenamentoTarget|
|StorageTargetTotalReadThroughput|ArmazenamentoTarget Total Read Throughput|BytesPerSecond|Média|A taxa total que o Cache lê dados de um determinado StorageTarget.|ArmazenamentoTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Resultado da sessão de sincronização|Contagem|Média|Métrica que regista um valor de 1 cada vez que o Ponto final do servidor completa com sucesso uma Sessão de Sincronização com o Cloud Endpoint|Nome do SyncGroup,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Ficheiros Sincronizados|Contagem|Total|Contagem de Ficheiros sincronizados|Nome do SyncGroup,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Ficheiros que não sincronizam|Contagem|Total|Contagem de ficheiros não conseguiu sincronizar|Nome do SyncGroup,ServerEndpointName,SyncDirection|
|StorageSyncBatchTransferredFileBytes|Bytes sincronizados|Bytes|Total|Tamanho total do ficheiro transferido para Sessões de Sincronização|Nome do SyncGroup,ServerEndpointName,SyncDirection|
|StorageSyncServerHeartbeat|Estado On-Line do Servidor|Contagem|Máximo|Métrica que regista um valor de 1 cada vez que o servidor ressitered registra com sucesso um batimento cardíaco com o Cloud Endpoint|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Recolha de tiering de nuvem|Bytes|Total|Tamanho total dos dados recordados pelo servidor|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Tamanho da recuperação do tiering de nuvem|Bytes|Total|Tamanho dos dados recordados|Nome do SyncGroup,Nome do Servidor|
|StorageSyncRecallThroughputBytesPerSecond|Entrada de recolha de tiering de cloud|BytesPerSecond|Média|Tamanho da recolha de dados|Nome do SyncGroup,Nome do Servidor|
|StorageSyncRecalledNetworkBytesByApplication|Tamanho de recolha de nível de nuvem por aplicação|Bytes|Total|Tamanho dos dados recordados por aplicação|Nome do SyncGroup,Nome do servidor,Nome de aplicação|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Contagem de ficheiros aplicados syncgroupsession|Ficheiros Sincronizados|Contagem|Total|Contagem de Ficheiros sincronizados|Nome do SyncGroup,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Ficheiros que não sincronizam|Contagem|Total|Contagem de ficheiros não conseguiu sincronizar|Nome do SyncGroup,ServerEndpointName,SyncDirection|
|SyncGroupBatchTransferFileBytes|Bytes sincronizados|Bytes|Total|Tamanho total do ficheiro transferido para Sessões de Sincronização|Nome do SyncGroup,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|Ficheiros Sincronizados|Contagem|Total|Contagem de Ficheiros sincronizados|ServerEndpointName,SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Ficheiros que não sincronizam|Contagem|Total|Contagem de ficheiros não conseguiu sincronizar|ServerEndpointName,SyncDirection|
|ServerEndpointBatchTransferFileBytes|Bytes sincronizados|Bytes|Total|Tamanho total do ficheiro transferido para Sessões de Sincronização|ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Batimentocardíaco de servidor|Estado On-Line do Servidor|Contagem|Máximo|Métrica que regista um valor de 1 cada vez que o servidor ressitered registra com sucesso um batimento cardíaco com o Cloud Endpoint|ServerResourceid,Nome do servidor|
|ServerRecallIOTotalSizeBytes|Recolha de tiering de nuvem|Bytes|Total|Tamanho total dos dados recordados pelo servidor|ServerResourceid,Nome do servidor|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ResourceUtilization|% de utilização SU|Percentagem|Máximo|% de utilização SU|Nome lógico,Partitionid|
|InputEvents|Eventos de Entrada|Contagem|Total|Eventos de Entrada|Nome lógico,Partitionid|
|InputEventBytes|Bytes de Evento de Entrada|Bytes|Total|Bytes de Evento de Entrada|Nome lógico,Partitionid|
|Eventos LateInput|Eventos de Entrada atrasados|Contagem|Total|Eventos de Entrada atrasados|Nome lógico,Partitionid|
|Eventos de saída|Eventos de Saída|Contagem|Total|Eventos de Saída|Nome lógico,Partitionid|
|Erros de Conversão|Erros de Conversão de Dados|Contagem|Total|Erros de Conversão de Dados|Nome lógico,Partitionid|
|Erros|Erros de Tempo de Execução|Contagem|Total|Erros de Tempo de Execução|Nome lógico,Partitionid|
|DroppedOrAdjustedEvents|Fora de ordem Eventos|Contagem|Total|Fora de ordem Eventos|Nome lógico,Partitionid|
|AMLCalloutRequests|Pedidos de Função|Contagem|Total|Pedidos de Função|Nome lógico,Partitionid|
|AMLCalloutFailedRequests|Falha no pedido de funções|Contagem|Total|Falha no pedido de funções|Nome lógico,Partitionid|
|AMLCalloutInputEvents|Eventos de Função|Contagem|Total|Eventos de Função|Nome lógico,Partitionid|
|DeserializationError|Erros de Desserialização de entrada|Contagem|Total|Erros de Desserialização de entrada|Nome lógico,Partitionid|
|Eventos EarlyInput|Eventos de Entrada Antigos|Contagem|Total|Eventos de Entrada Antigos|Nome lógico,Partitionid|
|OutputWatermarkDelaySeconds|Atraso de Marca de Água|Segundos|Máximo|Atraso de Marca de Água|Nome lógico,Partitionid|
|InputEventsSourcesBacklogged|Eventos de Entrada Pendentes|Contagem|Máximo|Eventos de Entrada Pendentes|Nome lógico,Partitionid|
|InputEventsSourcesPerSecond|Origens de Entrada Recebidas|Contagem|Total|Origens de Entrada Recebidas|Nome lógico,Partitionid|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Mensagens Recebidas|Mensagens Recebidas de Ingress|Contagem|Total|Contagem de mensagens lidas de todas as fontes do evento hub ou ioT hub|Nenhum|
|IngressReceivedInvalidMessages|Ingress recebeu mensagens inválidas|Contagem|Total|Contagem de mensagens inválidas lidas de todas as fontes do evento hub ou ioT hub|Nenhum|
|IngressReceivedBytes|Ingress recebido bytes|Bytes|Total|Contagem de bytes lidos de todas as fontes do evento|Nenhum|
|IngressStoredBytes|Ingress Stored Bytes|Bytes|Total|Tamanho total dos eventos processados com sucesso e disponíveis para consulta|Nenhum|
|IngressStoredEvents|Eventos Armazenados de Ingress|Contagem|Total|Contagem de eventos achatados processados com sucesso e disponíveis para consulta|Nenhum|
|IngressReceivedMessagesTimeLag|Ingress Recebeu O Tempo de Atraso|Segundos|Máximo|Diferença entre o tempo em que a mensagem é enquecida na fonte do evento e o tempo que é processado em Ingress|Nenhum|
|IngressReceivedMessagesCountLag|Ingress Recebeu Mensagens Count Lag|Contagem|Média|Diferença entre o número de sequência da última mensagem enqueuada na partição da fonte do evento e o número de sequência de mensagens que estão a ser processadas em Ingress|Nenhum|
|WarmStorageMaxProperties|Propriedades Max de Armazenamento Quente|Contagem|Máximo|Número máximo de propriedades permitidas pelo ambiente para S1/S2 SKU e número máximo de propriedades permitidas pela Warm Store para PAYG SKU|Nenhum|
|WarmStorageUsedProperties|Propriedades usadas de armazenamento quente |Contagem|Máximo|Número de propriedades utilizadas pelo ambiente para S1/S2 SKU e número de propriedades utilizadas pela Warm Store para PAYG SKU|Nenhum|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Mensagens Recebidas|Mensagens Recebidas de Ingress|Contagem|Total|Contagem de mensagens lidas a partir da fonte do evento|Nenhum|
|IngressReceivedInvalidMessages|Ingress recebeu mensagens inválidas|Contagem|Total|Contagem de mensagens inválidas lidas a partir da fonte do evento|Nenhum|
|IngressReceivedBytes|Ingress recebido bytes|Bytes|Total|Contagem de bytes lidos a partir da fonte do evento|Nenhum|
|IngressStoredBytes|Ingress Stored Bytes|Bytes|Total|Tamanho total dos eventos processados com sucesso e disponíveis para consulta|Nenhum|
|IngressStoredEvents|Eventos Armazenados de Ingress|Contagem|Total|Contagem de eventos achatados processados com sucesso e disponíveis para consulta|Nenhum|
|IngressReceivedMessagesTimeLag|Ingress Recebeu O Tempo de Atraso|Segundos|Máximo|Diferença entre o tempo em que a mensagem é enquecida na fonte do evento e o tempo que é processado em Ingress|Nenhum|
|IngressReceivedMessagesCountLag|Ingress Recebeu Mensagens Count Lag|Contagem|Média|Diferença entre o número de sequência da última mensagem enqueuada na partição da fonte do evento e o número de sequência de mensagens que estão a ser processadas em Ingress|Nenhum|
|WarmStorageMaxProperties|Propriedades Max de Armazenamento Quente|Contagem|Máximo|Número máximo de propriedades permitidas pelo ambiente para S1/S2 SKU e número máximo de propriedades permitidas pela Warm Store para PAYG SKU|Nenhum|
|WarmStorageUsedProperties|Propriedades usadas de armazenamento quente |Contagem|Máximo|Número de propriedades utilizadas pelo ambiente para S1/S2 SKU e número de propriedades utilizadas pela Warm Store para PAYG SKU|Nenhum|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Discos Ler Bytes/Sec|BytesPerSecond|Média|Produção média de disco devido a operações de leitura durante o período da amostra.|Nenhum|
|DiskWriteBytesPerSecond|Discos Write Bytes/Sec|BytesPerSecond|Média|Produção média de disco devido a operações de escrita durante o período da amostra.|Nenhum|
|Discos Ler Bytes|Discos Ler Bytes|Bytes|Total|Produção total de disco devido a operações de leitura durante o período da amostra.|Nenhum|
|Bytes de Escrita de Disco|Bytes de Escrita de Disco|Bytes|Total|Produção total de disco devido a operações de escrita durante o período da amostra.|Nenhum|
|DiskReadOperations|Operações de leitura do disco|Contagem|Total|O número de operações de leitura da OI no período de amostras anteriores. Note que estas operações podem ser de tamanho variável.|Nenhum|
|DiskWriteOperations|Operações de escrita de disco|Contagem|Total|O número de operações de escrita da OI no período de amostras anteriores. Note que estas operações podem ser de tamanho variável.|Nenhum|
|Operações de leitura do disco/sec|Operações de leitura do disco/sec|CountPerSecond|Média|O número médio de operações de leitura de IO no período de amostras anteriores. Note que estas operações podem ser de tamanho variável.|Nenhum|
|Operações de escrita de disco/sec|Operações de escrita de disco/sec|CountPerSecond|Média|O número médio de operações de escrita de IO no período de amostragem anterior. Note que estas operações podem ser de tamanho variável.|Nenhum|
|DiskReadLatency|Latência de leitura do disco|Milissegundos|Média|Latência total de leitura. A soma do dispositivo e do núcleo lêem lálências.|Nenhum|
|DiskWriteLatency|Latência de escrita de disco|Milissegundos|Média|Total latência de escrever. A soma do dispositivo e do núcleo escrevem látexções.|Nenhum|
|NetworkInBytesPerSecond|Rede Em Bytes/Sec|BytesPerSecond|Média|Produção média de rede para tráfego recebido.|Nenhum|
|NetworkOutBytesPerSecond|Rede out Bytes/Sec|BytesPerSecond|Média|Produção média de rede para tráfego transmitido.|Nenhum|
|Entrada na Rede|Entrada na Rede|Bytes|Total|Total de entrada de rede para tráfego recebido.|Nenhum|
|Saída da Rede|Saída da Rede|Bytes|Total|Produção total de rede para tráfego transmitido.|Nenhum|
|MemoryUsed|Memória Usada|Bytes|Média|A quantidade de memória da máquina que está a ser utilizada pelo VM.|Nenhum|
|MemóriaConcedida|Memória Concedida|Bytes|Média|A quantidade de memória que foi concedida ao VM pelo anfitrião. A memória não é concedida ao hospedeiro até que seja tocada uma vez e a memória concedida pode ser trocada ou reembalizada se o VMkernel precisar da memória.|Nenhum|
|MemoryActive|Memória Ativa|Bytes|Média|A quantidade de memória usada pelo VM no passado pequena janela de tempo. Este é o número "verdadeiro" de quanta memória o VM precisa atualmente. A memória adicional e não utilizada pode ser trocada ou balonizada sem impacto no desempenho do hóspede.|Nenhum|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A utilização da CPU. Este valor é reportado com 100% representando todos os núcleos de processador escarnecidos do sistema. Como exemplo, um VM de 2 vias usando 50% de um sistema de quatro núcleos está completamente usando dois núcleos.|Nenhum|
|PercentageCpuReady|CpU percentual pronto|Milissegundos|Total|O tempo de preparação é o tempo que espera que a CPU(s) fique disponível no intervalo de atualização anterior.|Nenhum|










## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuPercentage|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Instância|
|Percentagem de Memória|Percentagem de Memória|Percentagem|Média|Percentagem de Memória|Instância|
|DiskQueueLength|Comprimento da fila do disco|Contagem|Média|Comprimento da fila do disco|Instância|
|HttpQueueLength|Comprimento da fila http|Contagem|Média|Comprimento da fila http|Instância|
|BytesReceived|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Dados out|Bytes|Total|Dados out|Instância|
|TcpSynSent|TCP Syn Enviado|Contagem|Média|TCP Syn Enviado|Instância|
|TcpSynReceived|TCP Syn recebido|Contagem|Média|TCP Syn recebido|Instância|
|TcpEstablished|TCP Estabelecido|Contagem|Média|TCP Estabelecido|Instância|
|TcpFinWait1|TCP Fin Esperar 1|Contagem|Média|TCP Fin Esperar 1|Instância|
|TcpFinWait2|TCP Fin Wait 2|Contagem|Média|TCP Fin Wait 2|Instância|
|TcpClosing|Fecho do TCP|Contagem|Média|Fecho do TCP|Instância|
|TcpCloseWait|TCP Close Wait|Contagem|Média|TCP Close Wait|Instância|
|TcpLastAck|TCP Last Ack|Contagem|Média|TCP Last Ack|Instância|
|TcpTimeWait|Tempo de Espera do TCP|Contagem|Média|Tempo de Espera do TCP|Instância|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (excluindo funções)

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuTime|Tempo cpu|Segundos|Total|Tempo cpu|Instância|
|Pedidos|Pedidos|Contagem|Total|Pedidos|Instância|
|BytesReceived|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Dados out|Bytes|Total|Dados out|Instância|
|Http101|Http 101|Contagem|Total|Http 101|Instância|
|Http2xx|Http 2xx|Contagem|Total|Http 2xx|Instância|
|Http3xx|Http 3xx|Contagem|Total|Http 3xx|Instância|
|Http401|Http 401|Contagem|Total|Http 401|Instância|
|Http403|Http 403|Contagem|Total|Http 403|Instância|
|Http404|Http 404|Contagem|Total|Http 404|Instância|
|Http406|Http 406|Contagem|Total|Http 406|Instância|
|Http4xx|Http 4xx|Contagem|Total|Http 4xx|Instância|
|Http5xx|Erros do servidor http|Contagem|Total|Erros do servidor http|Instância|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|AverageMemoryWorkingSet|Conjunto de trabalho médio da memória|Bytes|Média|Conjunto de trabalho médio da memória|Instância|
|AverageResponseTime|Tempo de Resposta Médio|Segundos|Média|Tempo de Resposta Médio|Instância|
|AppConnections|Ligações|Contagem|Média|Ligações|Instância|
|Alças|Contagem de Identificadores|Contagem|Média|Contagem de Identificadores|Instância|
|Fios|Número de Threads|Contagem|Média|Número de Threads|Instância|
|PrivateBytes|Bytes Privados|Bytes|Média|Bytes Privados|Instância|
|IoReadBytesPerSecond|IO Ler Bytes por Segundo|BytesPerSecond|Total|IO Ler Bytes por Segundo|Instância|
|IoWriteBytesPerSecond|IO Escrever Bytes por Segundo|BytesPerSecond|Total|IO Escrever Bytes por Segundo|Instância|
|IoOtherBytesPerSecond|IO Outros Bytes por Segundo|BytesPerSecond|Total|IO Outros Bytes por Segundo|Instância|
|IoReadOperationsPerSecond|IO Ler Operações por segundo|BytesPerSecond|Total|IO Ler Operações por segundo|Instância|
|IoWriteOperationsPerSecond|IO Write Operations por segundo|BytesPerSecond|Total|IO Write Operations por segundo|Instância|
|IoOtherOperationsPerSecond|IO outras operações por segundo|BytesPerSecond|Total|IO outras operações por segundo|Instância|
|RequestsInApplicationQueue|Pedidos na fila de candidaturas|Contagem|Média|Pedidos na fila de candidaturas|Instância|
|CurrentAssemblies|Assembleias atuais|Contagem|Média|Assembleias atuais|Instância|
|TotalAppDomains|Domínios totais de aplicativos|Contagem|Média|Domínios totais de aplicativos|Instância|
|TotalAppDomainsUnloaded|Total de domínios de aplicativos descarregados|Contagem|Média|Total de domínios de aplicativos descarregados|Instância|
|Gen0Collections|Coleções de lixo gen 0|Contagem|Total|Coleções de lixo gen 0|Instância|
|Gen1Collections|Coleções de lixo da Gen 1|Contagem|Total|Coleções de lixo da Gen 1|Instância|
|Gen2Collections|Coleções de lixo gen 2|Contagem|Total|Coleções de lixo gen 2|Instância|
|HealthCheckStatus|Estado do controlo de saúde|Contagem|Média|Estado do controlo de saúde|Instância|
|Utilização do Sistema de Ficheiros|Utilização do sistema de ficheiros|Bytes|Média|Utilização do sistema de ficheiros|Nenhum|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funções)

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BytesReceived|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Dados out|Bytes|Total|Dados out|Instância|
|Http5xx|Erros do servidor http|Contagem|Total|Erros do servidor http|Instância|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|AverageMemoryWorkingSet|Conjunto de trabalho médio da memória|Bytes|Média|Conjunto de trabalho médio da memória|Instância|
|Unidades de Execução de Funções|Unidades de Execução de Funções|MB / Milliseconds|Total|[Unidades de Execução de Funções](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instância|
|FunctionExecutionCount|Contagem de execução de funções|Contagem|Total|Contagem de execução de funções|Instância|
|PrivateBytes|Bytes Privados|Bytes|Média|Bytes Privados|Instância|
|IoReadBytesPerSecond|IO Ler Bytes por Segundo|BytesPerSecond|Total|IO Ler Bytes por Segundo|Instância|
|IoWriteBytesPerSecond|IO Escrever Bytes por Segundo|BytesPerSecond|Total|IO Escrever Bytes por Segundo|Instância|
|IoOtherBytesPerSecond|IO Outros Bytes por Segundo|BytesPerSecond|Total|IO Outros Bytes por Segundo|Instância|
|IoReadOperationsPerSecond|IO Ler Operações por segundo|BytesPerSecond|Total|IO Ler Operações por segundo|Instância|
|IoWriteOperationsPerSecond|IO Write Operations por segundo|BytesPerSecond|Total|IO Write Operations por segundo|Instância|
|IoOtherOperationsPerSecond|IO outras operações por segundo|BytesPerSecond|Total|IO outras operações por segundo|Instância|
|RequestsInApplicationQueue|Pedidos na fila de candidaturas|Contagem|Média|Pedidos na fila de candidaturas|Instância|
|CurrentAssemblies|Assembleias atuais|Contagem|Média|Assembleias atuais|Instância|
|TotalAppDomains|Domínios totais de aplicativos|Contagem|Média|Domínios totais de aplicativos|Instância|
|TotalAppDomainsUnloaded|Total de domínios de aplicativos descarregados|Contagem|Média|Total de domínios de aplicativos descarregados|Instância|
|Gen0Collections|Coleções de lixo gen 0|Contagem|Total|Coleções de lixo gen 0|Instância|
|Gen1Collections|Coleções de lixo da Gen 1|Contagem|Total|Coleções de lixo da Gen 1|Instância|
|Gen2Collections|Coleções de lixo gen 2|Contagem|Total|Coleções de lixo gen 2|Instância|
|HealthCheckStatus|Estado do controlo de saúde|Contagem|Média|Estado do controlo de saúde|Instância|
|Utilização do Sistema de Ficheiros|Utilização do sistema de ficheiros|Bytes|Média|Utilização do sistema de ficheiros|Nenhum|


## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuTime|Tempo cpu|Segundos|Total|Tempo cpu|Instância|
|Pedidos|Pedidos|Contagem|Total|Pedidos|Instância|
|BytesReceived|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Dados out|Bytes|Total|Dados out|Instância|
|Http101|Http 101|Contagem|Total|Http 101|Instância|
|Http2xx|Http 2xx|Contagem|Total|Http 2xx|Instância|
|Http3xx|Http 3xx|Contagem|Total|Http 3xx|Instância|
|Http401|Http 401|Contagem|Total|Http 401|Instância|
|Http403|Http 403|Contagem|Total|Http 403|Instância|
|Http404|Http 404|Contagem|Total|Http 404|Instância|
|Http406|Http 406|Contagem|Total|Http 406|Instância|
|Http4xx|Http 4xx|Contagem|Total|Http 4xx|Instância|
|Http5xx|Erros do servidor http|Contagem|Total|Erros do servidor http|Instância|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|AverageMemoryWorkingSet|Conjunto de trabalho médio da memória|Bytes|Média|Conjunto de trabalho médio da memória|Instância|
|AverageResponseTime|Tempo de Resposta Médio|Segundos|Média|Tempo de Resposta Médio|Instância|
|HttpResponseTime|Tempo de Resposta|Segundos|Média|Tempo de Resposta|Instância|
|Unidades de Execução de Funções|Unidades de Execução de Funções|Contagem|Total|Unidades de Execução de Funções|Instância|
|FunctionExecutionCount|Contagem de execução de funções|Contagem|Total|Contagem de execução de funções|Instância|
|AppConnections|Ligações|Contagem|Média|Ligações|Instância|
|Alças|Contagem de Identificadores|Contagem|Média|Contagem de Identificadores|Instância|
|Fios|Número de Threads|Contagem|Média|Número de Threads|Instância|
|PrivateBytes|Bytes Privados|Bytes|Média|Bytes Privados|Instância|
|IoReadBytesPerSecond|IO Ler Bytes por Segundo|BytesPerSecond|Total|IO Ler Bytes por Segundo|Instância|
|IoWriteBytesPerSecond|IO Escrever Bytes por Segundo|BytesPerSecond|Total|IO Escrever Bytes por Segundo|Instância|
|IoOtherBytesPerSecond|IO Outros Bytes por Segundo|BytesPerSecond|Total|IO Outros Bytes por Segundo|Instância|
|IoReadOperationsPerSecond|IO Ler Operações por segundo|BytesPerSecond|Total|IO Ler Operações por segundo|Instância|
|IoWriteOperationsPerSecond|IO Write Operations por segundo|BytesPerSecond|Total|IO Write Operations por segundo|Instância|
|IoOtherOperationsPerSecond|IO outras operações por segundo|BytesPerSecond|Total|IO outras operações por segundo|Instância|
|RequestsInApplicationQueue|Pedidos na fila de candidaturas|Contagem|Média|Pedidos na fila de candidaturas|Instância|
|CurrentAssemblies|Assembleias atuais|Contagem|Média|Assembleias atuais|Instância|
|TotalAppDomains|Domínios totais de aplicativos|Contagem|Média|Domínios totais de aplicativos|Instância|
|TotalAppDomainsUnloaded|Total de domínios de aplicativos descarregados|Contagem|Média|Total de domínios de aplicativos descarregados|Instância|
|Gen0Collections|Coleções de lixo gen 0|Contagem|Total|Coleções de lixo gen 0|Instância|
|Gen1Collections|Coleções de lixo da Gen 1|Contagem|Total|Coleções de lixo da Gen 1|Instância|
|Gen2Collections|Coleções de lixo gen 2|Contagem|Total|Coleções de lixo gen 2|Instância|
|HealthCheckStatus|Estado do controlo de saúde|Contagem|Média|Estado do controlo de saúde|Instância|
|Utilização do Sistema de Ficheiros|Utilização do sistema de ficheiros|Bytes|Média|Utilização do sistema de ficheiros|Nenhum|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Pedidos|Pedidos|Contagem|Total|Pedidos|Instância|
|BytesReceived|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Dados out|Bytes|Total|Dados out|Instância|
|Http101|Http 101|Contagem|Total|Http 101|Instância|
|Http2xx|Http 2xx|Contagem|Total|Http 2xx|Instância|
|Http3xx|Http 3xx|Contagem|Total|Http 3xx|Instância|
|Http401|Http 401|Contagem|Total|Http 401|Instância|
|Http403|Http 403|Contagem|Total|Http 403|Instância|
|Http404|Http 404|Contagem|Total|Http 404|Instância|
|Http406|Http 406|Contagem|Total|Http 406|Instância|
|Http4xx|Http 4xx|Contagem|Total|Http 4xx|Instância|
|Http5xx|Erros do servidor http|Contagem|Total|Erros do servidor http|Instância|
|AverageResponseTime|Tempo de Resposta Médio|Segundos|Média|Tempo de Resposta Médio|Instância|
|CpuPercentage|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Instância|
|Percentagem de Memória|Percentagem de Memória|Percentagem|Média|Percentagem de Memória|Instância|
|DiskQueueLength|Comprimento da fila do disco|Contagem|Média|Comprimento da fila do disco|Instância|
|HttpQueueLength|Comprimento da fila http|Contagem|Média|Comprimento da fila http|Instância|
|ActiveRequests|Pedidos Ativos|Contagem|Total|Pedidos Ativos|Instância|
|TotalFrontEnds|Extremidades frontais totais|Contagem|Média|Extremidades frontais totais|Nenhum|
|SmallAppServicePlanInstances|Trabalhadores do plano de serviço de aplicativos de pequena saúde|Contagem|Média|Trabalhadores do plano de serviço de aplicativos de pequena saúde|Nenhum|
|MediumAppServicePlanInstances|Trabalhadores do plano de serviço de aplicações médias|Contagem|Média|Trabalhadores do plano de serviço de aplicações médias|Nenhum|
|LargeAppServicePlanInstances|Trabalhadores do grande plano de serviço de aplicações|Contagem|Média|Trabalhadores do grande plano de serviço de aplicações|Nenhum|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|WorkersTotal|Total de Trabalhadores|Contagem|Média|Total de Trabalhadores|Nenhum|
|TrabalhadoresDisponíveis|Trabalhadores disponíveis|Contagem|Média|Trabalhadores disponíveis|Nenhum|
|Trabalhadores utilizados|Trabalhadores usados|Contagem|Média|Trabalhadores usados|Nenhum|
|CpuPercentage|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Instância|
|Percentagem de Memória|Percentagem de Memória|Percentagem|Média|Percentagem de Memória|Instância|
## <a name="next-steps"></a>Passos seguintes
* [Ler sobre métricas no Monitor Azure](data-platform.md)
* [Criar alertas sobre métricas](alerts-overview.md)
* [Métricas de exportação para armazenamento, Hub de Eventos ou Log Analytics](platform-logs-overview.md)
