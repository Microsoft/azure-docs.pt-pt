---
title: Métricas suportadas pelo Monitor Azure por tipo de recurso
description: Lista de métricas disponíveis para cada tipo de recurso com o Monitor Azure.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 04/06/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: f2e3c03ba599128cc4552f64637ebd63efcb4578
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82128453"
---
# <a name="supported-metrics-with-azure-monitor"></a>Métricas suportadas com monitor Azure

> [!NOTE]
> Esta lista é em grande parte gerada automaticamente a partir da API DE MÉTRICAS de Monitor Azure. Qualquer modificação feita nesta lista via Github pode ser redigida sem aviso prévio. Contacte o autor deste artigo para obter mais informações sobre como efazer atualizações permanentes.

O Azure Monitor fornece várias formas de interagir com métricas, incluindo mapeá-las no portal, acessá-las através da API REST, ou questioná-las usando PowerShell ou CLI. 

Este artigo é uma lista completa de todas as métricas da plataforma (isto é, recolhidas automaticamente) atualmente disponíveis com o pipeline métrico consolidado do Azure Monitor. A lista foi atualizada pela última vez a 27 de março de 2020. As métricas alteradas ou adicionadas após esta data podem não aparecer abaixo. Para consultar e aceder à lista de métricas programáticamente, por favor use a [versão api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

Outras métricas podem estar disponíveis no portal ou usando APIs legados. As métricas para o sistema operativo convidado (guest os) que funciona em Máquinas Virtuais Azure, Tecido de Serviço e Serviços cloud **não** estão listadas aqui. Estes devem ser recolhidos através de um ou mais agentes que funcionam ou como parte do sistema operativo. Pode enviar as métricas do agente para a base de dados de métricas da plataforma utilizando as [métricas personalizadas](metrics-custom-overview.md) API, que estão atualmente em pré-visualização pública. Depois pode traçar, alertar e usar métricas de os convidados como métricas de plataforma. Para mais informações, consulte [a visão geral dos agentes de monitorização](agents-overview.md).    

As métricas são organizadas pelo espaço de nome. Para obter uma lista de serviços e os espaços de nome que lhes pertencem, consulte os fornecedores de [recursos para os serviços Azure.](../../azure-resource-manager/management/azure-services-resource-providers.md) 

> [!NOTE]
> Atualmente, o envio de métricas multidimensionais através das definições de diagnóstico não é suportado. As métricas com dimensões são exportadas como métricas dimensionais simples e agregadas em valores de dimensões.
>
> *Por exemplo*: a métrica “Mensagens Recebidas” num hub do Hub de Eventos pode ser explorada e representada ao nível da linha. No entanto, se for exportada através das definições de diagnóstico, a métrica será representada como todas as mensagens recebidas em todas as filas do hub do Hub de Eventos.
>
> Para obter uma lista de métricas de plataforma exportáveis através de configurações de diagnóstico, consulte [este artigo](metrics-supported-export-diagnostic-settings.md).


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servidores

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|qpu_metric|QPU|Contagem|Média|O QPU. Intervalo 0-100 para S1, 0-200 para S2 e 0-400 para S4|Tipo de recursos de servidor|
|memory_metric|Memória|Bytes|Média|Memória. Intervalo 0-25 GB para S1, 0-50 GB para S2 e 0-100 GB para S4|Tipo de recursos de servidor|
|private_bytes_metric|Bytes Privados|Bytes|Média|Bytes privados.|Tipo de recursos de servidor|
|virtual_bytes_metric|Bytes Virtuais|Bytes|Média|Bytes virtuais.|Tipo de recursos de servidor|
|TotalDePedidos de Conexão|Total de pedidos de ligação|Contagem|Média|Pedidos de ligação total. Estas são chegadas.|Tipo de recursos de servidor|
|SucessoConexsPerSec|Conexões bem sucedidas por Sec|CondeEmSegundo|Média|Taxa de conclusão de ligação bem sucedida.|Tipo de recursos de servidor|
|TotalDefalhas de ligação|Falhas totais de ligação|Contagem|Média|Total tentativas de ligação falhadas.|Tipo de recursos de servidor|
|Sessões de Utilizadores Atuais|Sessões de utilizador atuais|Contagem|Média|Número atual de sessões de utilizador estabelecidas.|Tipo de recursos de servidor|
|ConsultaPoolBusyThreads|Filas movimentadas da piscina de consulta|Contagem|Média|Número de fios ocupados na piscina de fios de consulta.|Tipo de recursos de servidor|
|Comprimento de fila de trabalho de commandpool|Comprimento da fila do trabalho da piscina de comando|Contagem|Média|Número de empregos na fila da piscina de linhas de comando.|Tipo de recursos de servidor|
|ProcessingPoolJobQueueLength|Processamento de trabalho de piscina comprimento de fila|Contagem|Média|Número de empregos não-I/O na fila da piscina de fios de processamento.|Tipo de recursos de servidor|
|CurrentConnections|Ligação: Ligações atuais|Contagem|Média|Número atual de ligações ao cliente estabelecidas.|Tipo de recursos de servidor|
|Preço Corrente mais limpo|Memória: Preço Atual Mais Limpo|Contagem|Média|Preço atual da memória, $/byte/tempo, normalizado a 1000.|Tipo de recursos de servidor|
|Memória Mais LimpaEncolhe|Memória: Memória mais limpa encolhe|Bytes|Média|Quantidade de memória, em bytes, sujeito a purga pelo limpador de fundo.|Tipo de recursos de servidor|
|Memória Mais LimpaNão Encolhe|Memória: Memória mais limpa não encolhe|Bytes|Média|Quantidade de memória, em bytes, não sujeito a purga pelo limpador de fundo.|Tipo de recursos de servidor|
|Uso da Memória|Memória: Utilização da memória|Bytes|Média|Utilização da memória do processo do servidor como utilizado no cálculo do preço de memória mais limpo. Igual a contra processo\PrivateBytes mais o tamanho dos dados mapeados pela memória, ignorando qualquer memória que tenha sido mapeada ou atribuída pelo motor de análise de memória xVelocity (VertiPaq) em excesso do limite de memória do motor xVelocity.|Tipo de recursos de servidor|
|MemoryLimithard|Memória: Limite de memória difícil|Bytes|Média|Limite de memória dura, a partir do ficheiro de configuração.|Tipo de recursos de servidor|
|MemoryLimitHigh|Memória: Limite de memória alto|Bytes|Média|Limite de memória elevado, a partir do ficheiro de configuração.|Tipo de recursos de servidor|
|Limite de memória|Memória: Limite de memória baixo|Bytes|Média|Baixo limite de memória, a partir do ficheiro de configuração.|Tipo de recursos de servidor|
|MemoryLimitVertiPaq|Memória: Limite de memória VertiPaq|Bytes|Média|Limite de memória, a partir do ficheiro de configuração.|Tipo de recursos de servidor|
|Quota|Memória: Quota|Bytes|Média|Quota de memória atual, em bytes. A quota de memória também é conhecida como subsídio de memória ou reserva de memória.|Tipo de recursos de servidor|
|Bloco de Quotas|Memória: Quota bloqueada|Contagem|Média|Número atual de pedidos de quota que são bloqueados até que outras quotas de memória sejam libertadas.|Tipo de recursos de servidor|
|VertiPaqNonpaged|Memória: VertiPaq Nonpaged|Bytes|Média|Bytes de memória bloqueados no conjunto de trabalho para utilização pelo motor de memória.|Tipo de recursos de servidor|
|VertiPaqPaged|Memória: VertiPaq Paged|Bytes|Média|Bytes de memória páginada em uso para dados na memória.|Tipo de recursos de servidor|
|LinhasReadPerSec|Processamento: Linhas lidas por segundo|CondeEmSegundo|Média|Taxa de linhas lidas a partir de todas as bases de dados relacionais.|Tipo de recursos de servidor|
|RowsConvertedPerSec|Processamento: Linhas convertidas por seg|CondeEmSegundo|Média|Taxa de linhas convertidas durante o processamento.|Tipo de recursos de servidor|
|LinhasWrittenPerSec|Processamento: Linhas escritas por seg|CondeEmSegundo|Média|Taxa de linhas escritas durante o processamento.|Tipo de recursos de servidor|
|CommandPoolBusyThreads|Threads: Comando piscina fios ocupados|Contagem|Média|Número de fios ocupados na piscina de fios de comando.|Tipo de recursos de servidor|
|CommandPoolIdleThreads|Threads: Linhas ociosas da piscina de comando|Contagem|Média|Número de fios ociosos na piscina de fios de comando.|Tipo de recursos de servidor|
|LongParsingBusyThreads|Threads: Longo parsing fios ocupados|Contagem|Média|Número de fios ocupados na longa piscina de fios de análise.|Tipo de recursos de servidor|
|LongParsingIdleThreads|Threads: Longos fios ociosos de análise|Contagem|Média|Número de fios ociosos na longa piscina de fios de análise.|Tipo de recursos de servidor|
|LongParsingJobQueueLength|Threads: Longa análise do comprimento da fila do trabalho|Contagem|Média|Número de empregos na fila da longa piscina de fios de análise.|Tipo de recursos de servidor|
|ProcessingPoolBusyIOJobThreads|Threads: Processing pool busy I/O job threads|Contagem|Média|Número de fios em funcionamento de empregos em I/S na piscina de fios de processamento.|Tipo de recursos de servidor|
|ProcessingPoolBusyNonIOThreads|Threads: Piscina de processamento movimentada fios não-I/O|Contagem|Média|Número de fios que executam trabalhos não-I/O na piscina de fios de processamento.|Tipo de recursos de servidor|
|ProcessingPoolioJobQueueLength|Threads: Processamento de piscina I/O comprimento da fila de trabalho|Contagem|Média|Número de empregos em I/S na fila da piscina de fios de processamento.|Tipo de recursos de servidor|
|ProcessingPoolIdleIOJobThreads|Threads: Processing pool idle I/O job threads|Contagem|Média|Número de fios ociosos para trabalhos de I/S na piscina de fios de processamento.|Tipo de recursos de servidor|
|ProcessingPoolIdleNonIOThreads|Threads: Processamento de fios não-I/O da piscina|Contagem|Média|Número de fios ociosos na piscina de fios de processamento dedicados a empregos não-I/O.|Tipo de recursos de servidor|
|ConsultaPoolIdleThreads|Threads: Filas ociosas da piscina de consulta|Contagem|Média|Número de fios ociosos para trabalhos de I/S na piscina de fios de processamento.|Tipo de recursos de servidor|
|ConsultaPoolJobQueueLength|Threads: Fila de trabalho de piscina de consulta lengt|Contagem|Média|Número de empregos na fila da piscina de fios de consulta.|Tipo de recursos de servidor|
|ShortParsingBusyThreads|Threads: Curto parsing fios ocupados|Contagem|Média|Número de fios ocupados na piscina de fios de análise curta.|Tipo de recursos de servidor|
|ShortParsingIdleThreads|Threads: Fios ociosos de análise curta|Contagem|Média|Número de fios ociosos na piscina de fios de análise curta.|Tipo de recursos de servidor|
|ShortParsingJobQueueLength|Threads: Curto período de fila de trabalho de análise|Contagem|Média|Número de empregos na fila da piscina de fios de análise curta.|Tipo de recursos de servidor|
|memory_thrashing_metric|Degradação de Memória|Percentagem|Média|A memória média a bater.|Tipo de recursos de servidor|
|mashup_engine_qpu_metric|M Motor QPU|Contagem|Média|Utilização de QPU por processos de monomotor de mashup|Tipo de recursos de servidor|
|mashup_engine_memory_metric|Memória do Motor M|Bytes|Média|Utilização da memória por processos do motor mashup|Tipo de recursos de servidor|
|mashup_engine_private_bytes_metric|M Motor Private Bytes|Bytes|Média|Bytes privados de utilização por processos de monomotor mashup.|Tipo de recursos de servidor|
|mashup_engine_virtual_bytes_metric|Bytes virtuais do motor M|Bytes|Média|Bytes virtuais de utilização por processos de monomotor mashup.|Tipo de recursos de servidor|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalRequests|Total de pedidos de gateway (Preprecated)|Contagem|Total|Número de pedidos de gateway - Utilize métrica de pedido de várias dimensões com dimensão GatewayResponseCodeCategory|Localização,Nome de anfitrião|
|Pedidos bem sucedidos|Pedidos de gateway bem sucedidos (precatiados)|Contagem|Total|Número de pedidos de gateway bem sucedidos - Utilize métrica de pedido de várias dimensões com dimensão GatewayResponseCodeCategory|Localização,Nome de anfitrião|
|Pedidos Não Autorizados|Pedidos de gateway não autorizados (Preprecated)|Contagem|Total|Número de pedidos de gateway não autorizados - Utilize métrica de pedido de várias dimensões com dimensão GatewayResponseCodeCategory|Localização,Nome de anfitrião|
|Pedidos Falhados|Pedidos de gateway falhados (precatiados)|Contagem|Total|Número de falhas nos pedidos de gateway - Utilize métrica de pedido de várias dimensões com dimensão GatewayResponseCodeCategory|Localização,Nome de anfitrião|
|Outros Pedidos|Outros pedidos de gateway (Preprecated)|Contagem|Total|Número de outros pedidos de gateway - Utilize métrica de pedido de várias dimensões com dimensão GatewayResponseCodeCategory|Localização,Nome de anfitrião|
|Duração|Duração global dos pedidos de gateway|Milissegundos|Média|Duração global dos pedidos de gateway em milissegundos|Localização,Nome de anfitrião|
|BackendDura|Duração dos pedidos de backend|Milissegundos|Média|Duração dos pedidos de backend em milissegundos|Localização,Nome de anfitrião|
|Capacidade|Capacidade|Percentagem|Média|Métrica de utilização para o serviço ApiManagement|Localização|
|Eventos EventHubTotal|Eventos Totais eventhub|Contagem|Total|Número de eventos enviados para eventHub|Localização|
|Eventos de Sucesso eventhub|Eventos de Sucesso EventHub|Contagem|Total|Número de eventos de sucesso eventHub|Localização|
|EventoHubTotalFailedEvents|Eventos falhados do EventHub|Contagem|Total|Número de eventos falhados do EventHub|Localização|
|Eventos EventHubRejected|Eventos de EventHub rejeitados|Contagem|Total|Número de eventos rejeitados do EventHub (configuração errada ou não autorizada)|Localização|
|EventoSHubThrottledEvents|Eventos Throttled EventHub|Contagem|Total|Número de eventos do EventHub estrangulados|Localização|
|EventoHubTimedoutEvents|Eventos Timed out EventHub|Contagem|Total|Número de eventos cronometrados eventHub|Localização|
|Eventos EventHubDrop|Eventos de EventHub abandonados|Contagem|Total|Número de eventos ignorados devido ao limite de tamanho da fila atingido|Localização|
|EventHubTotalBytesSent|Tamanho dos eventos EventHub|Bytes|Total|Tamanho total dos eventos EventHub em bytes|Localização|
|Pedidos|Pedidos|Contagem|Total|Gateway solicitar métricas com múltiplas dimensões|Localização,Nome de anfitrião,LastErrorReason,BackendResponseCode,GatewayResponseCode,BackendResponseCodeCategory,GatewayResponseCodeCategory,GatewayResponseCodeCategory|
|Conectividade de Rede|Estado de Conectividade da Rede de Recursos (Pré-visualização)|Contagem|Total|Estado de conectividade de rede de tipos de recursos dependentes do serviço de gestão da API|Localização,Recurso|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configuraçãoStores

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Contagem|Contagem|Número total de pedidos de http de entrada.|Código de Estado|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Contagem|Média|Latência a pedido de http.|Código de Estado|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
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

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalJob|Empregototal|Contagem|Total|O número total de postos de trabalho|Livro de execução,Estado|
|TotalUpdateDeploymentruns|Execuções de implementação total de atualização|Contagem|Total|Executações totais de implementação de atualização de software|Nome de configuração de software,Status|
|Corridas total de implantação|Corridas de máquinas de implantação de atualização total|Contagem|Total|Máquina de implementação total de atualização de software executa em uma execução de implementação de atualização de software|Nome de configuração de software,Status,Targetcomputer,SoftwareUpdateGenRunid|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CoreCount|Contagem de núcleos dedicado|Contagem|Total|Número total de núcleos dedicados na conta do lote|Nenhuma|
|TotalNodeCount|Contagem de nódedicados dedicado|Contagem|Total|Número total de nós dedicados na conta do lote|Nenhuma|
|BaixaPrioridadeCoreCount|Contagem do núcleo de baixa prioridade|Contagem|Total|Número total de núcleos de baixa prioridade na conta do lote|Nenhuma|
|TotalLowPriorityNodeCount|Contagem de nódeadebaixas|Contagem|Total|Número total de nós de baixa prioridade na conta do lote|Nenhuma|
|Criando NodeCount|Criando a Contagem do Nó|Contagem|Total|Número de nós sendo criados|Nenhuma|
|InícioNodeCount|Contagem de nósinos insinuantes|Contagem|Total|Número de nós começando|Nenhuma|
|EsperaForStartTaskNodeCount|Esperando para começar contagem de nó de tarefa|Contagem|Total|Número de nós à espera que a Tarefa de Início esteja concluída|Nenhuma|
|StartTaskFailedNodeCount|Contagem de nófalhado de tarefa inicial|Contagem|Total|Número de nós onde a Tarefa de Início falhou|Nenhuma|
|IdleNodeCount|Contagem de nóocioso ocioso|Contagem|Total|Número de nós ociosos|Nenhuma|
|OffNodeCount|Contagem de nó offline|Contagem|Total|Número de nós offline|Nenhuma|
|RebootingNodeCount|Contagem de nóreinicialização|Contagem|Total|Número de nós de reinicialização|Nenhuma|
|ReimagingNodeCount|Contagem de nó de reimaging|Contagem|Total|Número de nós de reimagem|Nenhuma|
|RunningNodeCount|Contagem de nósomas de execução|Contagem|Total|Número de nós de corrida|Nenhuma|
|Deixando PoolNodeCount|Deixando a contagem do nó da piscina|Contagem|Total|Número de nós saindo da Piscina|Nenhuma|
|InutilizávelNodeCount|Contagem inutilizável de nó|Contagem|Total|Número de nós inutilizáveis|Nenhuma|
|Contagem de NodeS pré-empreitada|Contagem de nósinos preempted|Contagem|Total|Número de nós pré-empreitados|Nenhuma|
|Evento TaskStartEvent|Eventos de início de tarefa|Contagem|Total|Número total de tarefas que começaram|poolId,jobId|
|Evento TaskComplete|Tarefa Completa Eventos|Contagem|Total|Número total de tarefas que completaram|poolId,jobId|
|TaskFailEvent|Eventos de falha de tarefa|Contagem|Total|Número total de tarefas que completaram num estado falhado|poolId,jobId|
|Evento PoolCreate|Piscina Criar Eventos|Contagem|Total|Número total de piscinas que foram criadas|poolId|
|PoolResizeStartEvent|Eventos de início de redimensionar piscina|Contagem|Total|Número total de redimensionos de piscina que começaram|poolId|
|PoolResizeCompleteEvent|Piscina Redimensionar eventos completos|Contagem|Total|Número total de redimensionos de piscina que tenham concluído|poolId|
|PoolDeleteStartEvent|Pool Delete Eventos de Início|Contagem|Total|Número total de eliminações de piscinas que começaram|poolId|
|PoolDeleteCompleteEvent|Pool Eliminar Eventos Completos|Contagem|Total|Número total de eliminações de piscinas que tenham concluído|poolId|
|JobDeleteCompleteEvent|Job Delete Complete Events|Contagem|Total|Número total de postos de trabalho que foram eliminados com êxito.|jobId|
|JobDeleteStartEvent|Job Delete Start Events|Contagem|Total|Número total de postos de trabalho que foram solicitados para serem suprimidos.|jobId|
|Evento Completo jobDisable|Desativação de trabalho eventos completos|Contagem|Total|Número total de postos de trabalho com êxito incapacitados.|jobId|
|Evento JobDisableStart|Eventos de início de desativação de emprego|Contagem|Total|Número total de postos de trabalho que foram solicitados para serem desativados.|jobId|
|Evento JobStartEvent|Eventos de início de trabalho|Contagem|Total|Número total de postos de trabalho que foram iniciados com sucesso.|jobId|
|Evento De Terminação de Trabalho|Trabalho terminar eventos completos|Contagem|Total|Número total de postos de trabalho que foram encerrados com sucesso.|jobId|
|Evento JobTerminateStartEvent|Trabalho terminar eventos de início|Contagem|Total|Número total de postos de trabalho que foram solicitados para serem encerrados.|jobId|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/espaços de trabalho

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
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

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|Percentagem de utilização do CPU|Percentagem|Máximo|Percentagem de utilização do CPU|Nó|
|Uso da Memória|Utilização de Memória|Bytes|Média|Utilização de Memória|Nó|
|Limite de memória|Limite de memória|Bytes|Média|Limite de memória|Nó|
|MemoryUsagePercentageinDouble|Percentagem de utilização da memória|Percentagem|Média|Percentagem de utilização da memória|Nó|
|Armazenamento|Utilização de armazenamento|Bytes|Média|Utilização de armazenamento|Nó|
|IOReadBytes|IO Ler Bytes|Bytes|Total|IO Ler Bytes|Nó|
|IOWriteBytes|IO Write Bytes|Bytes|Total|IO Write Bytes|Nó|
|Conexão Aceite|Conexões Aceites|Contagem|Total|Conexões Aceites|Nó|
|Ligação Handled|Conexões manuseadas|Contagem|Total|Conexões manuseadas|Nó|
|ConexãoActive|Conexões Ativas|Contagem|Média|Conexões Ativas|Nó|
|RequestHandled|Pedidos tratados|Contagem|Total|Pedidos tratados|Nó|
|Blocos Processados|Blocos processados|Contagem|Total|Blocos processados|Nó|
|Transações Processadas|Transações Processadas|Contagem|Total|Transações Processadas|Nó|
|Transações Pendentes|Transações Pendentes|Contagem|Média|Transações Pendentes|Nó|
|Transações em fila|Transações em fila|Contagem|Média|Transações em fila|Nó|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|clientes conectados|Clientes Ligados|Contagem|Máximo||ShardId|
|total de comandos processados|Total de Operações|Contagem|Total||ShardId|
|cachehits|Cache Hits|Contagem|Total||ShardId|
|cachemisses|Cache Misses|Contagem|Total||ShardId|
|cachemissrate|Cache Miss Rate|Percentagem|cachemissrate||ShardId|
|obter comandos|Fica|Contagem|Total||ShardId|
|setcommands|Conjuntos|Contagem|Total||ShardId|
|operaçõesPerSecond|Operações por segundo|Contagem|Máximo||ShardId|
|chaves despejadas|Chaves despejadas|Contagem|Total||ShardId|
|totaldechaves|Chaves totais|Contagem|Máximo||ShardId|
|expiradokeys|Chaves expiradas|Contagem|Total||ShardId|
|memória usada|Memória Usada|Bytes|Máximo||ShardId|
|percentagem de memória usada|Percentagem de memória usada|Percentagem|Máximo||ShardId|
|memória usadaRss|RSS de memória usada|Bytes|Máximo||ShardId|
|servidorLoad|Carga do servidor|Percentagem|Máximo||ShardId|
|cacheEscrever|Cache Write|BytesPerSecond|Máximo||ShardId|
|cacheLer|Leitura de cache|BytesPerSecond|Máximo||ShardId|
|percentProcessorTime|CPU|Percentagem|Máximo||ShardId|
|cacheLatency|Microsegundos de Latência cache (Pré-visualização)|Contagem|Média||ShardId|
|erros|Erros|Contagem|Máximo||ShardId,ErrorType|
|clientes conectados0|Clientes Conectados (Fragmento 0)|Contagem|Máximo||Nenhuma|
|total de comandos processados0|Total de Operações (Fragmento 0)|Contagem|Total||Nenhuma|
|cachehits0|Cache Hits (Fragmento 0)|Contagem|Total||Nenhuma|
|cachemisses0|Cache Misses (Fragmento 0)|Contagem|Total||Nenhuma|
|getcommands0|Recebe (Fragmento 0)|Contagem|Total||Nenhuma|
|setcommands0|Conjuntos (Fragmento 0)|Contagem|Total||Nenhuma|
|operaçõesPerSecond0|Operações por Segundo (Fragmento 0)|Contagem|Máximo||Nenhuma|
|despejadokeys0|Chaves despejadas (Fragmento 0)|Contagem|Total||Nenhuma|
|totaldekeys0|Chaves totais (Fragmento 0)|Contagem|Máximo||Nenhuma|
|expiradokeys0|Chaves expiradas (Fragmento 0)|Contagem|Total||Nenhuma|
|memória usada0|Memória Usada (Fragmento 0)|Bytes|Máximo||Nenhuma|
|memória usadaRss0|Memória Usada RSS (Fragmento 0)|Bytes|Máximo||Nenhuma|
|servidorLoad0|Carga do servidor (Fragmento 0)|Percentagem|Máximo||Nenhuma|
|cacheWrite0|Cache Write (Fragmento 0)|BytesPerSecond|Máximo||Nenhuma|
|cacheRead0|Cache Read (Fragmento 0)|BytesPerSecond|Máximo||Nenhuma|
|percentProcessorTime0|CPU (Fragmento 0)|Percentagem|Máximo||Nenhuma|
|clientes conectados1|Clientes Conectados (Fragmento 1)|Contagem|Máximo||Nenhuma|
|total de comandos processados1|Total de Operações (Fragmento 1)|Contagem|Total||Nenhuma|
|cachehits1|Cache Hits (Fragmento 1)|Contagem|Total||Nenhuma|
|cachemisses1|Cache Misses (Fragmento 1)|Contagem|Total||Nenhuma|
|getcommands1|Recebe (Fragmento 1)|Contagem|Total||Nenhuma|
|setcommands1|Conjuntos (Fragmento 1)|Contagem|Total||Nenhuma|
|operaçõesPerSecond1|Operações por Segundo (Fragmento 1)|Contagem|Máximo||Nenhuma|
|despejadoskeys1|Chaves despejadas (Fragmento 1)|Contagem|Total||Nenhuma|
|totaldekeys1|Chaves totais (Fragmento 1)|Contagem|Máximo||Nenhuma|
|expiradokeys1|Chaves expiradas (Fragmento 1)|Contagem|Total||Nenhuma|
|memória usada1|Memória Usada (Fragmento 1)|Bytes|Máximo||Nenhuma|
|memória usadaRss1|Memória Usada RSS (Fragmento 1)|Bytes|Máximo||Nenhuma|
|servidorLoad1|Carga do servidor (Fragmento 1)|Percentagem|Máximo||Nenhuma|
|cacheWrite1|Cache Write (Fragmento 1)|BytesPerSecond|Máximo||Nenhuma|
|cacheRead1|Cache Read (Fragmento 1)|BytesPerSecond|Máximo||Nenhuma|
|percentProcessorTime1|CPU (Fragmento 1)|Percentagem|Máximo||Nenhuma|
|clientes conectados2|Clientes Conectados (Fragmento 2)|Contagem|Máximo||Nenhuma|
|total de comandos processados2|Total de Operações (Fragmento 2)|Contagem|Total||Nenhuma|
|cachehits2|Cache Hits (Fragmento 2)|Contagem|Total||Nenhuma|
|cachemisses2|Cache Misses (Fragmento 2)|Contagem|Total||Nenhuma|
|getcommands2|Recebe (Fragmento 2)|Contagem|Total||Nenhuma|
|setcommands2|Conjuntos (Fragmento 2)|Contagem|Total||Nenhuma|
|operaçõesPerSecond2|Operações por Segundo (Fragmento 2)|Contagem|Máximo||Nenhuma|
|despejadoskeys2|Chaves despejadas (Fragmento 2)|Contagem|Total||Nenhuma|
|totaldekeys2|Chaves totais (Fragmento 2)|Contagem|Máximo||Nenhuma|
|expiradokeys2|Chaves expiradas (Fragmento 2)|Contagem|Total||Nenhuma|
|memória usada2|Memória Usada (Fragmento 2)|Bytes|Máximo||Nenhuma|
|memória usadaRss2|Memória Usada RSS (Fragmento 2)|Bytes|Máximo||Nenhuma|
|servidorLoad2|Carga do servidor (Fragmento 2)|Percentagem|Máximo||Nenhuma|
|cacheWrite2|Cache Write (Fragmento 2)|BytesPerSecond|Máximo||Nenhuma|
|cacheRead2|Cache Read (Fragmento 2)|BytesPerSecond|Máximo||Nenhuma|
|percentProcessorTime2|CPU (Fragmento 2)|Percentagem|Máximo||Nenhuma|
|clientes conectados3|Clientes Conectados (Fragmento 3)|Contagem|Máximo||Nenhuma|
|total de comandos processados3|Total de Operações (Fragmento 3)|Contagem|Total||Nenhuma|
|cachehits3|Cache Hits (Fragmento 3)|Contagem|Total||Nenhuma|
|cachemisses3|Cache Misses (Fragmento 3)|Contagem|Total||Nenhuma|
|getcommands3|Recebe (Fragmento 3)|Contagem|Total||Nenhuma|
|setcommands3|Conjuntos (Fragmento 3)|Contagem|Total||Nenhuma|
|operaçõesPerSecond3|Operações por Segundo (Fragmento 3)|Contagem|Máximo||Nenhuma|
|despejadoschaves3|Chaves despejadas (Fragmento 3)|Contagem|Total||Nenhuma|
|totaldekeys3|Chaves totais (Fragmento 3)|Contagem|Máximo||Nenhuma|
|expiradokeys3|Chaves expiradas (Fragmento 3)|Contagem|Total||Nenhuma|
|memória usada3|Memória Usada (Fragmento 3)|Bytes|Máximo||Nenhuma|
|memória usadaRss3|Memória Usada RSS (Fragmento 3)|Bytes|Máximo||Nenhuma|
|servidorLoad3|Carga do servidor (Fragmento 3)|Percentagem|Máximo||Nenhuma|
|cacheWrite3|Cache Write (Fragmento 3)|BytesPerSecond|Máximo||Nenhuma|
|cacheRead3|Cache Read (Fragmento 3)|BytesPerSecond|Máximo||Nenhuma|
|percentProcessorTime3|CPU (Fragmento 3)|Percentagem|Máximo||Nenhuma|
|clientes conectados4|Clientes Conectados (Fragmento 4)|Contagem|Máximo||Nenhuma|
|total de comandos processados4|Total de Operações (Fragmento 4)|Contagem|Total||Nenhuma|
|cachehits4|Cache Hits (Fragmento 4)|Contagem|Total||Nenhuma|
|cachemisses4|Cache Misses (Fragmento 4)|Contagem|Total||Nenhuma|
|getcommands4|Recebe (Fragmento 4)|Contagem|Total||Nenhuma|
|setcommands4|Conjuntos (Fragmento 4)|Contagem|Total||Nenhuma|
|operaçõesPerSecond4|Operações por Segundo (Fragmento 4)|Contagem|Máximo||Nenhuma|
|despejadoschaves4|Chaves despejadas (Fragmento 4)|Contagem|Total||Nenhuma|
|totaldekeys4|Chaves totais (Fragmento 4)|Contagem|Máximo||Nenhuma|
|expiradokeys4|Chaves expiradas (Fragmento 4)|Contagem|Total||Nenhuma|
|memória usada4|Memória Usada (Fragmento 4)|Bytes|Máximo||Nenhuma|
|memória usadaRss4|Memória Usada RSS (Fragmento 4)|Bytes|Máximo||Nenhuma|
|servidorLoad4|Carga do servidor (Fragmento 4)|Percentagem|Máximo||Nenhuma|
|cacheWrite4|Cache Write (Fragmento 4)|BytesPerSecond|Máximo||Nenhuma|
|cacheRead4|Cache Read (Fragmento 4)|BytesPerSecond|Máximo||Nenhuma|
|percentProcessorTime4|CPU (Fragmento 4)|Percentagem|Máximo||Nenhuma|
|clientes conectados5|Clientes Conectados (Fragmento 5)|Contagem|Máximo||Nenhuma|
|total de comandos processados5|Total de Operações (Fragmento 5)|Contagem|Total||Nenhuma|
|cachehits5|Cache Hits (Fragmento 5)|Contagem|Total||Nenhuma|
|cachemisses5|Cache Misses (Fragmento 5)|Contagem|Total||Nenhuma|
|getcommands5|Recebe (Fragmento 5)|Contagem|Total||Nenhuma|
|setcommands5|Conjuntos (Fragmento 5)|Contagem|Total||Nenhuma|
|operaçõesPerSecond5|Operações por Segundo (Fragmento 5)|Contagem|Máximo||Nenhuma|
|despejadoskeys5|Chaves despejadas (Fragmento 5)|Contagem|Total||Nenhuma|
|totaldekeys5|Chaves totais (Fragmento 5)|Contagem|Máximo||Nenhuma|
|expiradokeys5|Chaves expiradas (Fragmento 5)|Contagem|Total||Nenhuma|
|memória usada5|Memória Usada (Fragmento 5)|Bytes|Máximo||Nenhuma|
|memória usadaRss5|Memória Usada RSS (Fragmento 5)|Bytes|Máximo||Nenhuma|
|servidorLoad5|Carga do servidor (Fragmento 5)|Percentagem|Máximo||Nenhuma|
|cacheWrite5|Cache Write (Fragmento 5)|BytesPerSecond|Máximo||Nenhuma|
|cacheRead5|Cache Read (Fragmento 5)|BytesPerSecond|Máximo||Nenhuma|
|percentProcessorTime5|CPU (Fragmento 5)|Percentagem|Máximo||Nenhuma|
|clientes conectados6|Clientes Conectados (Fragmento 6)|Contagem|Máximo||Nenhuma|
|total de comandos processados6|Total de Operações (Fragmento 6)|Contagem|Total||Nenhuma|
|cachehits6|Cache Hits (Fragmento 6)|Contagem|Total||Nenhuma|
|cachemisses6|Cache Misses (Fragmento 6)|Contagem|Total||Nenhuma|
|getcommands6|Recebe (Fragmento 6)|Contagem|Total||Nenhuma|
|setcommands6|Conjuntos (Fragmento 6)|Contagem|Total||Nenhuma|
|operaçõesPerSecond6|Operações por Segundo (Fragmento 6)|Contagem|Máximo||Nenhuma|
|despejados6|Chaves despejadas (Fragmento 6)|Contagem|Total||Nenhuma|
|totalde6|Chaves totais (Fragmento 6)|Contagem|Máximo||Nenhuma|
|expiradokeys6|Chaves expiradas (Fragmento 6)|Contagem|Total||Nenhuma|
|memória usada6|Memória Usada (Fragmento 6)|Bytes|Máximo||Nenhuma|
|memória usadaRss6|Memória Usada RSS (Fragmento 6)|Bytes|Máximo||Nenhuma|
|servidorLoad6|Carga do servidor (Fragmento 6)|Percentagem|Máximo||Nenhuma|
|cacheWrite6|Cache Write (Fragmento 6)|BytesPerSecond|Máximo||Nenhuma|
|cacheRead6|Cache Read (Fragmento 6)|BytesPerSecond|Máximo||Nenhuma|
|percentProcessorTime6|CPU (Fragmento 6)|Percentagem|Máximo||Nenhuma|
|clientes conectados7|Clientes Conectados (Shard 7)|Contagem|Máximo||Nenhuma|
|total de comandos processados7|Total de Operações (Fragmento 7)|Contagem|Total||Nenhuma|
|cachehits7|Cache Hits (Fragmento 7)|Contagem|Total||Nenhuma|
|cachemisses7|Cache Misses (Fragmento 7)|Contagem|Total||Nenhuma|
|getcommands7|Recebe (Fragmento 7)|Contagem|Total||Nenhuma|
|setcommands7|Conjuntos (Fragmento 7)|Contagem|Total||Nenhuma|
|operaçõesPerSecond7|Operações por Segundo (Fragmento 7)|Contagem|Máximo||Nenhuma|
|despejadoskeys7|Chaves despejadas (Fragmento 7)|Contagem|Total||Nenhuma|
|totaldekeys7|Chaves totais (Fragmento 7)|Contagem|Máximo||Nenhuma|
|expiradokeys7|Chaves expiradas (Fragmento 7)|Contagem|Total||Nenhuma|
|memória usada7|Memória Usada (Fragmento 7)|Bytes|Máximo||Nenhuma|
|memória usadaRss7|Memória Usada RSS (Fragmento 7)|Bytes|Máximo||Nenhuma|
|servidorLoad7|Carga do servidor (Fragmento 7)|Percentagem|Máximo||Nenhuma|
|cacheWrite7|Cache Write (Fragmento 7)|BytesPerSecond|Máximo||Nenhuma|
|cacheRead7|Cache Read (Fragmento 7)|BytesPerSecond|Máximo||Nenhuma|
|percentProcessorTime7|CPU (Fragmento 7)|Percentagem|Máximo||Nenhuma|
|clientes conectados8|Clientes Conectados (Fragmento 8)|Contagem|Máximo||Nenhuma|
|total de comandos processados8|Total de Operações (Fragmento 8)|Contagem|Total||Nenhuma|
|cachehits8|Cache Hits (Fragmento 8)|Contagem|Total||Nenhuma|
|cachemisses8|Cache Misses (Fragmento 8)|Contagem|Total||Nenhuma|
|getcommands8|Recebe (Fragmento 8)|Contagem|Total||Nenhuma|
|setcommands8|Conjuntos (Fragmento 8)|Contagem|Total||Nenhuma|
|operaçõesPerSecond8|Operações por Segundo (Fragmento 8)|Contagem|Máximo||Nenhuma|
|despejadoschaves8|Chaves despejadas (Fragmento 8)|Contagem|Total||Nenhuma|
|totaldekeys8|Chaves totais (Fragmento 8)|Contagem|Máximo||Nenhuma|
|expiradokeys8|Chaves expiradas (Fragmento 8)|Contagem|Total||Nenhuma|
|memória usada8|Memória Usada (Fragmento 8)|Bytes|Máximo||Nenhuma|
|memória usadaRss8|Memória Usada RSS (Fragmento 8)|Bytes|Máximo||Nenhuma|
|servidorLoad8|Carga do servidor (Fragmento 8)|Percentagem|Máximo||Nenhuma|
|cacheWrite8|Cache Write (Fragmento 8)|BytesPerSecond|Máximo||Nenhuma|
|cacheRead8|Cache Read (Fragmento 8)|BytesPerSecond|Máximo||Nenhuma|
|percentProcessorTime8|CPU (Fragmento 8)|Percentagem|Máximo||Nenhuma|
|clientes conectados9|Clientes Conectados (Shard 9)|Contagem|Máximo||Nenhuma|
|total de comandos processados9|Total de Operações (Fragmento 9)|Contagem|Total||Nenhuma|
|cachehits9|Cache Hits (Fragmento 9)|Contagem|Total||Nenhuma|
|cachemisses9|Cache Misses (Fragmento 9)|Contagem|Total||Nenhuma|
|getcommands9|Recebe (Fragmento 9)|Contagem|Total||Nenhuma|
|setcommands9|Conjuntos (Fragmento 9)|Contagem|Total||Nenhuma|
|operaçõesPerSecond9|Operações por Segundo (Fragmento 9)|Contagem|Máximo||Nenhuma|
|despejadokeys9|Chaves despejadas (Fragmento 9)|Contagem|Total||Nenhuma|
|totaldekeys9|Chaves totais (Fragmento 9)|Contagem|Máximo||Nenhuma|
|expiradokeys9|Chaves expiradas (Fragmento 9)|Contagem|Total||Nenhuma|
|memória usada9|Memória Usada (Fragmento 9)|Bytes|Máximo||Nenhuma|
|memória usadaRss9|Memória Usada RSS (Fragmento 9)|Bytes|Máximo||Nenhuma|
|servidorLoad9|Carga do servidor (Fragmento 9)|Percentagem|Máximo||Nenhuma|
|cacheWrite9|Cache Write (Fragmento 9)|BytesPerSecond|Máximo||Nenhuma|
|cacheRead9|Cache Read (Fragmento 9)|BytesPerSecond|Máximo||Nenhuma|
|percentProcessorTime9|CPU (Fragmento 9)|Percentagem|Máximo||Nenhuma|




## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationapplicationfirewallpolicies

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Contagem de pedidos de firewall da web|Contagem de pedidos de firewall de aplicação web|Contagem|Total|O número de pedidos de clientes processados pela Firewall de Aplicação Web|Nome de política,Nome de Regras,Ação|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades computacionais atribuídas que estão atualmente a ser utilizadas pela(s Máquinas Virtuais).|Nenhuma|
|Entrada na Rede|Entrada na Rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela(s) Virtual Machine(s) (Tráfego De Entrada).|Nenhuma|
|Saída da Rede|Saída da Rede|Bytes|Total|O número de bytes em todas as interfaces de rede pelas Máquinas Virtuais (Tráfego De saída).|Nenhuma|
|Discos Ler Bytes/Sec|Leitura do disco|BytesPerSecond|Média|Bytes médios lidos a partir de disco durante o período de monitorização.|Nenhuma|
|Discos Write Bytes/Sec|Escrita de Disco|BytesPerSecond|Média|Bytes médios escritos ao disco durante o período de monitorização.|Nenhuma|
|Operações de leitura do disco/sec|Operações de leitura do disco/sec|CondeEmSegundo|Média|Disco leia IOPS.|Nenhuma|
|Operações de escrita de disco/sec|Operações de escrita de disco/sec|CondeEmSegundo|Média|Disco escrever IOPS.|Nenhuma|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades computacionais atribuídas que estão atualmente a ser utilizadas pela(s Máquinas Virtuais).|Roleinstanceid|
|Entrada na Rede|Entrada na Rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela(s) Virtual Machine(s) (Tráfego De Entrada).|Roleinstanceid|
|Saída da Rede|Saída da Rede|Bytes|Total|O número de bytes em todas as interfaces de rede pelas Máquinas Virtuais (Tráfego De saída).|Roleinstanceid|
|Discos Ler Bytes/Sec|Leitura do disco|BytesPerSecond|Média|Bytes médios lidos a partir de disco durante o período de monitorização.|Roleinstanceid|
|Discos Write Bytes/Sec|Escrita de Disco|BytesPerSecond|Média|Bytes médios escritos ao disco durante o período de monitorização.|Roleinstanceid|
|Operações de leitura do disco/sec|Operações de leitura do disco/sec|CondeEmSegundo|Média|Disco leia IOPS.|Roleinstanceid|
|Operações de escrita de disco/sec|Operações de escrita de disco/sec|CondeEmSegundo|Média|Disco escrever IOPS.|Roleinstanceid|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Capacidade Utilizada|Capacidade usada|Bytes|Média|Capacidade utilizada pela conta|Nenhuma|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão Do Tipo resposta para o número de diferentes tipos de resposta.|Tipo de resposta,GeoType,Nome ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de ingresso, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência usada pelo Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência final dos pedidos bem sucedidos feitos a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BlobCapacity|Capacidade blob|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Blob da conta de armazenamento em bytes.|BlobType,Tier|
|BlobCount|Contagem de Bolhas|Contagem|Média|O número de Blob no serviço Blob da conta de armazenamento.|BlobType,Tier|
|Contagem de contentores|Contagem de contentores blob|Contagem|Média|O número de contentores no serviço Blob da conta de armazenamento.|Nenhuma|
|IndexCapacity|Capacidade indexada|Bytes|Média|A quantidade de armazenamento utilizada pelo Índice ADLS Gen2 (Hierárquico) em bytes.|Nenhuma|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão Do Tipo resposta para o número de diferentes tipos de resposta.|Tipo de resposta,GeoType,Nome ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de ingresso, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência usada pelo Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência final dos pedidos bem sucedidos feitos a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Capacidade de Mesa|Capacidade de Mesa|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço de mesa da conta de armazenamento em bytes.|Nenhuma|
|Contagem de tabelas|Contagem de mesas|Contagem|Média|O número de mesa no serviço de mesa da conta de armazenamento.|Nenhuma|
|Contagem de tabelas|Contagem de entidades de mesa|Contagem|Média|O número de entidades de mesa no serviço mesa da conta de armazenamento.|Nenhuma|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão Do Tipo resposta para o número de diferentes tipos de resposta.|Tipo de resposta,GeoType,Nome ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de ingresso, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência usada pelo Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência final dos pedidos bem sucedidos feitos a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Capacidade de Arquivo|Capacidade de arquivo|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço de arquivos da conta de armazenamento em bytes.|FileShare|
|Contagem de Ficheiros|Contagem de Ficheiros|Contagem|Média|O número de ficheiros no serviço de ficheiros da conta de armazenamento.|FileShare|
|FileShareCount|Contagem de partilha de ficheiros|Contagem|Média|O número de ações de ficheiros no serviço de ficheiros da conta de armazenamento.|Nenhuma|
|FileShareSnapshotCount|Contagem de instantâneos de partilha de ficheiros|Contagem|Média|O número de instantâneos presentes na parte no Serviço de Ficheiros da conta de armazenamento.|FileShare|
|FileShareSnapshotSize|Tamanho instantâneo de partilha de ficheiros|Bytes|Média|A quantidade de armazenamento utilizada pelos instantâneos no serviço de ficheiros da conta de armazenamento em bytes.|FileShare|
|FileShareQuota|Tamanho da quota de partilha de ficheiros|Bytes|Média|O limite superior da quantidade de armazenamento que pode ser usado pelo Serviço de Ficheiros Azure em bytes.|FileShare|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão Do Tipo resposta para o número de diferentes tipos de resposta.|Tipo de resposta,GeoType,ApiName,Autenticação,FileShare|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de ingresso, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação,FileShare|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação,FileShare|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência usada pelo Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType,ApiName,Autenticação,FileShare|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência final dos pedidos bem sucedidos feitos a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação,FileShare|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação,FileShare|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Capacidade de fila|Capacidade de fila|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço de fila da conta de armazenamento em bytes.|Nenhuma|
|Contagem de filas|Contagem de filas|Contagem|Média|O número de filas no serviço de fila da conta de armazenamento.|Nenhuma|
|Contagem de mensagens de fila|Contagem de mensagens de fila|Contagem|Média|O número aproximado de mensagens de fila no serviço de fila da conta de armazenamento.|Nenhuma|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão Do Tipo resposta para o número de diferentes tipos de resposta.|Tipo de resposta,GeoType,Nome ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de ingresso, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência usada pelo Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência final dos pedidos bem sucedidos feitos a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Totalcall|Total de Chamadas|Contagem|Total|Número total de chamadas.|Nome ApiName,OperaçãoNome,Região|
|Chamadas bem sucedidas|Chamadas bem sucedidas|Contagem|Total|Número de chamadas bem sucedidas.|Nome ApiName,OperaçãoNome,Região|
|Erros Totais|Erros Totais|Contagem|Total|Número total de chamadas com resposta a erros (código de resposta HTTP 4xx ou 5xx).|Nome ApiName,OperaçãoNome,Região|
|Chamadas Bloqueadas|Chamadas Bloqueadas|Contagem|Total|Número de chamadas que excederam a taxa ou o limite de quota.|Nome ApiName,OperaçãoNome,Região|
|Erros de servidor|Erros do servidor|Contagem|Total|Número de chamadas com erro interno de serviço (código de resposta HTTP 5xx).|Nome ApiName,OperaçãoNome,Região|
|Erros de cliente|Erros do Cliente|Contagem|Total|Número de chamadas com erro do lado do cliente (código de resposta HTTP 4xx).|Nome ApiName,OperaçãoNome,Região|
|DataIn|Dados em|Bytes|Total|Tamanho dos dados de entrada em bytes.|Nome ApiName,OperaçãoNome,Região|
|DataOut|Dados out|Bytes|Total|Tamanho dos dados de saída em bytes.|Nome ApiName,OperaçãoNome,Região|
|Latência|Latência|MilliSeconds|Média|Latência em milissegundos.|Nome ApiName,OperaçãoNome,Região|
|TotalTokenCalls|Total de chamadas simbólicas|Contagem|Total|Número total de chamadas simbólicas.|Nome ApiName,OperaçãoNome,Região|
|CaracteresTraduzidos|Caracteres traduzidos|Contagem|Total|Número total de caracteres no pedido de texto.|Nome ApiName,OperaçãoNome,Região|
|Caracteres Treinados|Personagens treinados|Contagem|Total|Número total de caracteres treinados.|Nome ApiName,OperaçãoNome,Região|
|Duração das sessões de discurso|Duração da sessão de discurso|Segundos|Total|Duração total da sessão de discurso em segundos.|Nome ApiName,OperaçãoNome,Região|
|Total de Transações|Total de Transações|Contagem|Total|Número total de transações.|Nenhuma|
|Imagens Processadas|Imagens Processadas|Contagem|Total| Número de Transações para processamento de imagem.|ApiName,Nome de Recurso,Canal,Região|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades computacionais atribuídas que estão atualmente a ser utilizadas pela ou as máquinas virtuais|Nenhuma|
|Entrada na Rede|Rede Em Billable (Depreciado)|Bytes|Total|O número de bytes faturados recebidos em todas as interfaces de rede pelas Máquinas Virtuais (Tráfego De entrada) (Deprecated)|Nenhuma|
|Saída da Rede|Network out Billable (Depreciado)|Bytes|Total|O número de bytes faturados em todas as interfaces de rede pelas Máquinas Virtuais (Tráfego Desaída) (Deprecated)|Nenhuma|
|Discos Ler Bytes|Discos Ler Bytes|Bytes|Total|Bytes lidos a partir de disco durante o período de monitorização|Nenhuma|
|Bytes de Escrita de Disco|Bytes de Escrita de Disco|Bytes|Total|Bytes escritos ao disco durante o período de monitorização|Nenhuma|
|Operações de leitura do disco/sec|Operações de leitura do disco/sec|CondeEmSegundo|Média|IOPS de leitura de disco|Nenhuma|
|Operações de escrita de disco/sec|Operações de escrita de disco/sec|CondeEmSegundo|Média|IOPS de escrita de disco|Nenhuma|
|Créditos cpu restantes|Créditos cpu restantes|Contagem|Média|Número total de créditos disponíveis para rebentar|Nenhuma|
|Créditos da CPU consumidos|Créditos da CPU consumidos|Contagem|Média|Número total de créditos consumidos pela Máquina Virtual|Nenhuma|
|Por Disco Ler Bytes/seg|Data Disk Read Bytes/Sec [(Depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|SlotId|
|Por Disco Escreva Bytes/seg|Data Disk Write Bytes/Sec [(Depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Bytes/Sec escrito sumo durante o período de monitorização|SlotId|
|Por disco ler operações/sec|Operações de leitura de disco de dados/Sec [(Depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Leia iOPS a partir de um único disco durante o período de monitorização|SlotId|
|Por Operações de Escrita de Disco/Sec|Operações de Escrita de Disco de Dados/Sec [(Depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|SlotId|
|Por Disco QD|QD do disco de dados [(preprecated)](portal-disk-metrics-deprecation.md)[portal-disk-metrics-deprecation.md)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|SlotId|
|OS Per Disk Ler Bytes/seg|Os Discos Ler Bytes/Sec [(Depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Bytes/Sec lido saem de um único disco durante o período de monitorização do disco OS|Nenhuma|
|OS Por Disco Escreva Bytes/seg|Os Disk Write Bytes/Sec [(Depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Bytes/Sec escrito a um único disco durante o período de monitorização do disco OS|Nenhuma|
|OS por disco ler operações/sec|Operações de leitura de disco osso/sec [(depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Leia iOPS a partir de um único disco durante o período de monitorização do disco OS|Nenhuma|
|As operações de escrita de OS por disco/sec|Operações de Escrita de Disco OS/Sec [(Depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco OS|Nenhuma|
|OS Por Disco QD|QD do disco [osso (depreciado)](portal-disk-metrics-deprecation.md)|Contagem|Média|Profundidade de fila de disco osso (ou comprimento da fila)|Nenhuma|
|Data Disk Ler Bytes/seg|Data Disk Read Bytes/Sec (Pré-visualização)|CondeEmSegundo|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|LUN|
|Data Disk Write Bytes/seg|Data Disk Write Bytes/Sec (Pré-visualização)|CondeEmSegundo|Média|Bytes/Sec escrito sumo durante o período de monitorização|LUN|
|Operações de leitura de disco de dados/sec|Data Disk Ler Operações/Sec (Pré-visualização)|CondeEmSegundo|Média|Leia iOPS a partir de um único disco durante o período de monitorização|LUN|
|Operações de escrita de disco de dados/sec|Operações de escrita de disco de dados/sec (pré-visualização)|CondeEmSegundo|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|LUN|
|Profundidade da Fila do Disco de Dados|Profundidade da fila do disco de dados (pré-visualização)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|LUN|
|Os Discos Ler Bytes/seg|Os Discos Ler Bytes/Sec (Pré-visualização)|CondeEmSegundo|Média|Bytes/Sec lido saem de um único disco durante o período de monitorização do disco OS|Nenhuma|
|Os Disk Write Bytes/seg|Os Disk Write Bytes/Sec (Pré-visualização)|CondeEmSegundo|Média|Bytes/Sec escrito a um único disco durante o período de monitorização do disco OS|Nenhuma|
|Operações de leitura de disco osso/sec|As operações/seg de leitura do disco OS (pré-visualização)|CondeEmSegundo|Média|Leia iOPS a partir de um único disco durante o período de monitorização do disco OS|Nenhuma|
|Operações de escrita de disco osso/sec|Operações de escrita de disco osso/sec (pré-visualização)|CondeEmSegundo|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco OS|Nenhuma|
|Profundidade da Fila do Disco do SO|Profundidade da fila do disco osso (pré-visualização)|Contagem|Média|Profundidade de fila de disco osso (ou comprimento da fila)|Nenhuma|
|Fluxos de Entrada|Fluxos de Entrada|Contagem|Média|Os fluxos de entrada são o número de fluxos correntes na direção de entrada (tráfego que vai para o VM)|Nenhuma|
|Fluxos de saída|Fluxos de saída|Contagem|Média|Os fluxos de saída são o número de fluxos correntes na direção de saída (tráfego a sair do VM)|Nenhuma|
|Taxa máxima de criação dos fluxos de entrada|Taxa máxima de criação dos fluxos de entrada|CondeEmSegundo|Média|A taxa máxima de criação dos fluxos de entrada (tráfego que vai para o VM)|Nenhuma|
|Taxa máxima de criação dos fluxos de saída|Taxa máxima de criação dos fluxos de saída|CondeEmSegundo|Média|A taxa máxima de criação dos fluxos de saída (tráfego saindo do VM)|Nenhuma|
|Premium Data Disk Cache Ler Sucesso|Vídeo de cache de disco premium (pré-visualização)|Percentagem|Média|Premium Data Disk Cache Ler Sucesso|LUN|
|Cache de disco de dados premium ler miss|Cache de disco de dados premium Leia Miss (Pré-visualização)|Percentagem|Média|Cache de disco de dados premium ler miss|LUN|
|Premium OS Disk Cache Ler Sucesso|Premium OS Cache Cache Read Hit (Pré-visualização)|Percentagem|Média|Premium OS Disk Cache Ler Sucesso|Nenhuma|
|Cache de disco premium DO|Cache de disco premium OS Leia Miss (Pré-visualização)|Percentagem|Média|Cache de disco premium DO|Nenhuma|
|Rede no Total|Rede no Total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela(s) Virtual Machine(s) (Tráfego De Entrada)|Nenhuma|
|Rede out Total|Rede out Total|Bytes|Total|O número de bytes em todas as interfaces de rede pelas Máquinas Virtuais (Tráfego De saída)|Nenhuma|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades computacionais atribuídas que estão atualmente a ser utilizadas pela ou as máquinas virtuais|VMName|
|Entrada na Rede|Rede Em Billable (Depreciado)|Bytes|Total|O número de bytes faturados recebidos em todas as interfaces de rede pelas Máquinas Virtuais (Tráfego De entrada) (Deprecated)|VMName|
|Saída da Rede|Network out Billable (Depreciado)|Bytes|Total|O número de bytes faturados em todas as interfaces de rede pelas Máquinas Virtuais (Tráfego Desaída) (Deprecated)|VMName|
|Discos Ler Bytes|Discos Ler Bytes|Bytes|Total|Bytes lidos a partir de disco durante o período de monitorização|VMName|
|Bytes de Escrita de Disco|Bytes de Escrita de Disco|Bytes|Total|Bytes escritos ao disco durante o período de monitorização|VMName|
|Operações de leitura do disco/sec|Operações de leitura do disco/sec|CondeEmSegundo|Média|IOPS de leitura de disco|VMName|
|Operações de escrita de disco/sec|Operações de escrita de disco/sec|CondeEmSegundo|Média|IOPS de escrita de disco|VMName|
|Créditos cpu restantes|Créditos cpu restantes|Contagem|Média|Número total de créditos disponíveis para rebentar|Nenhuma|
|Créditos da CPU consumidos|Créditos da CPU consumidos|Contagem|Média|Número total de créditos consumidos pela Máquina Virtual|Nenhuma|
|Por Disco Ler Bytes/seg|Data Disk Read Bytes/Sec [(Depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|SlotId|
|Por Disco Escreva Bytes/seg|Data Disk Write Bytes/Sec [(Depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Bytes/Sec escrito sumo durante o período de monitorização|SlotId|
|Por disco ler operações/sec|Operações de leitura de disco de dados/Sec [(Depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Leia iOPS a partir de um único disco durante o período de monitorização|SlotId|
|Por Operações de Escrita de Disco/Sec|Operações de Escrita de Disco de Dados/Sec [(Depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|SlotId|
|Por Disco QD|QD do disco de dados [(depreciado)](portal-disk-metrics-deprecation.md)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|SlotId|
|OS Per Disk Ler Bytes/seg|Os Discos Ler Bytes/Sec [(Depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Bytes/Sec lido saem de um único disco durante o período de monitorização do disco OS|Nenhuma|
|OS Por Disco Escreva Bytes/seg|Os Disk Write Bytes/Sec [(Depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Bytes/Sec escrito a um único disco durante o período de monitorização do disco OS|Nenhuma|
|OS por disco ler operações/sec|Operações de leitura de disco osso/sec [(depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Leia iOPS a partir de um único disco durante o período de monitorização do disco OS|Nenhuma|
|As operações de escrita de OS por disco/sec|Operações de Escrita de Disco OS/Sec [(Depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco OS|Nenhuma|
|OS Por Disco QD|QD do disco [osso (depreciado)](portal-disk-metrics-deprecation.md)|Contagem|Média|Profundidade de fila de disco osso (ou comprimento da fila)|Nenhuma|
|Data Disk Ler Bytes/seg|Data Disk Read Bytes/Sec (Pré-visualização)|CondeEmSegundo|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|LUN,VMName|
|Data Disk Write Bytes/seg|Data Disk Write Bytes/Sec (Pré-visualização)|CondeEmSegundo|Média|Bytes/Sec escrito sumo durante o período de monitorização|LUN,VMName|
|Operações de leitura de disco de dados/sec|Data Disk Ler Operações/Sec (Pré-visualização)|CondeEmSegundo|Média|Leia iOPS a partir de um único disco durante o período de monitorização|LUN,VMName|
|Operações de escrita de disco de dados/sec|Operações de escrita de disco de dados/sec (pré-visualização)|CondeEmSegundo|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|LUN,VMName|
|Profundidade da Fila do Disco de Dados|Profundidade da fila do disco de dados (pré-visualização)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|LUN,VMName|
|Os Discos Ler Bytes/seg|Os Discos Ler Bytes/Sec (Pré-visualização)|CondeEmSegundo|Média|Bytes/Sec lido saem de um único disco durante o período de monitorização do disco OS|VMName|
|Os Disk Write Bytes/seg|Os Disk Write Bytes/Sec (Pré-visualização)|CondeEmSegundo|Média|Bytes/Sec escrito a um único disco durante o período de monitorização do disco OS|VMName|
|Operações de leitura de disco osso/sec|As operações/seg de leitura do disco OS (pré-visualização)|CondeEmSegundo|Média|Leia iOPS a partir de um único disco durante o período de monitorização do disco OS|VMName|
|Operações de escrita de disco osso/sec|Operações de escrita de disco osso/sec (pré-visualização)|CondeEmSegundo|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco OS|VMName|
|Profundidade da Fila do Disco do SO|Profundidade da fila do disco osso (pré-visualização)|Contagem|Média|Profundidade de fila de disco osso (ou comprimento da fila)|VMName|
|Fluxos de Entrada|Fluxos de Entrada|Contagem|Média|Os fluxos de entrada são o número de fluxos correntes na direção de entrada (tráfego que vai para o VM)|VMName|
|Fluxos de saída|Fluxos de saída|Contagem|Média|Os fluxos de saída são o número de fluxos correntes na direção de saída (tráfego a sair do VM)|VMName|
|Taxa máxima de criação dos fluxos de entrada|Taxa máxima de criação dos fluxos de entrada|CondeEmSegundo|Média|A taxa máxima de criação dos fluxos de entrada (tráfego que vai para o VM)|VMName|
|Taxa máxima de criação dos fluxos de saída|Taxa máxima de criação dos fluxos de saída|CondeEmSegundo|Média|A taxa máxima de criação dos fluxos de saída (tráfego saindo do VM)|VMName|
|Premium Data Disk Cache Ler Sucesso|Vídeo de cache de disco premium (pré-visualização)|Percentagem|Média|Premium Data Disk Cache Ler Sucesso|LUN,VMName|
|Cache de disco de dados premium ler miss|Cache de disco de dados premium Leia Miss (Pré-visualização)|Percentagem|Média|Cache de disco de dados premium ler miss|LUN,VMName|
|Premium OS Disk Cache Ler Sucesso|Premium OS Cache Cache Read Hit (Pré-visualização)|Percentagem|Média|Premium OS Disk Cache Ler Sucesso|VMName|
|Cache de disco premium DO|Cache de disco premium OS Leia Miss (Pré-visualização)|Percentagem|Média|Cache de disco premium DO|VMName|
|Rede no Total|Rede no Total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela(s) Virtual Machine(s) (Tráfego De Entrada)|VMName|
|Rede out Total|Rede out Total|Bytes|Total|O número de bytes em todas as interfaces de rede pelas Máquinas Virtuais (Tráfego De saída)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades computacionais atribuídas que estão atualmente a ser utilizadas pela ou as máquinas virtuais|Nenhuma|
|Entrada na Rede|Rede Em Billable (Depreciado)|Bytes|Total|O número de bytes faturados recebidos em todas as interfaces de rede pelas Máquinas Virtuais (Tráfego De entrada) (Deprecated)|Nenhuma|
|Saída da Rede|Network out Billable (Depreciado)|Bytes|Total|O número de bytes faturados em todas as interfaces de rede pelas Máquinas Virtuais (Tráfego Desaída) (Deprecated)|Nenhuma|
|Discos Ler Bytes|Discos Ler Bytes|Bytes|Total|Bytes lidos a partir de disco durante o período de monitorização|Nenhuma|
|Bytes de Escrita de Disco|Bytes de Escrita de Disco|Bytes|Total|Bytes escritos ao disco durante o período de monitorização|Nenhuma|
|Operações de leitura do disco/sec|Operações de leitura do disco/sec|CondeEmSegundo|Média|IOPS de leitura de disco|Nenhuma|
|Operações de escrita de disco/sec|Operações de escrita de disco/sec|CondeEmSegundo|Média|IOPS de escrita de disco|Nenhuma|
|Créditos cpu restantes|Créditos cpu restantes|Contagem|Média|Número total de créditos disponíveis para rebentar|Nenhuma|
|Créditos da CPU consumidos|Créditos da CPU consumidos|Contagem|Média|Número total de créditos consumidos pela Máquina Virtual|Nenhuma|
|Por Disco Ler Bytes/seg|Data Disk Read Bytes/Sec [(Depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|SlotId|
|Por Disco Escreva Bytes/seg|Data Disk Write Bytes/Sec [(Depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Bytes/Sec escrito sumo durante o período de monitorização|SlotId|
|Por disco ler operações/sec|Operações de leitura de disco de dados/Sec [(Depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Leia iOPS a partir de um único disco durante o período de monitorização|SlotId|
|Por Operações de Escrita de Disco/Sec|Operações de Escrita de Disco de Dados/Sec [(Depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|SlotId|
|Por Disco QD|QD do disco de dados [(depreciado)](portal-disk-metrics-deprecation.md)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|SlotId|
|OS Per Disk Ler Bytes/seg|Os Discos Ler Bytes/Sec [(Depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Bytes/Sec lido saem de um único disco durante o período de monitorização do disco OS|Nenhuma|
|OS Por Disco Escreva Bytes/seg|Os Disk Write Bytes/Sec [(Depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Bytes/Sec escrito a um único disco durante o período de monitorização do disco OS|Nenhuma|
|OS por disco ler operações/sec|Operações de leitura de disco osso/sec [(depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Leia iOPS a partir de um único disco durante o período de monitorização do disco OS|Nenhuma|
|As operações de escrita de OS por disco/sec|Operações de Escrita de Disco OS/Sec [(Depreciado)](portal-disk-metrics-deprecation.md)|CondeEmSegundo|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco OS|Nenhuma|
|OS Por Disco QD|QD do disco [osso (depreciado)](portal-disk-metrics-deprecation.md)|Contagem|Média|Profundidade de fila de disco osso (ou comprimento da fila)|Nenhuma|
|Data Disk Ler Bytes/seg|Data Disk Read Bytes/Sec (Pré-visualização)|CondeEmSegundo|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|LUN|
|Data Disk Write Bytes/seg|Data Disk Write Bytes/Sec (Pré-visualização)|CondeEmSegundo|Média|Bytes/Sec escrito sumo durante o período de monitorização|LUN|
|Operações de leitura de disco de dados/sec|Data Disk Ler Operações/Sec (Pré-visualização)|CondeEmSegundo|Média|Leia iOPS a partir de um único disco durante o período de monitorização|LUN|
|Operações de escrita de disco de dados/sec|Operações de escrita de disco de dados/sec (pré-visualização)|CondeEmSegundo|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|LUN|
|Profundidade da Fila do Disco de Dados|Profundidade da fila do disco de dados (pré-visualização)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|LUN|
|Os Discos Ler Bytes/seg|Os Discos Ler Bytes/Sec (Pré-visualização)|CondeEmSegundo|Média|Bytes/Sec lido saem de um único disco durante o período de monitorização do disco OS|Nenhuma|
|Os Disk Write Bytes/seg|Os Disk Write Bytes/Sec (Pré-visualização)|CondeEmSegundo|Média|Bytes/Sec escrito a um único disco durante o período de monitorização do disco OS|Nenhuma|
|Operações de leitura de disco osso/sec|As operações/seg de leitura do disco OS (pré-visualização)|CondeEmSegundo|Média|Leia iOPS a partir de um único disco durante o período de monitorização do disco OS|Nenhuma|
|Operações de escrita de disco osso/sec|Operações de escrita de disco osso/sec (pré-visualização)|CondeEmSegundo|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco OS|Nenhuma|
|Profundidade da Fila do Disco do SO|Profundidade da fila do disco osso (pré-visualização)|Contagem|Média|Profundidade de fila de disco osso (ou comprimento da fila)|Nenhuma|
|Fluxos de Entrada|Fluxos de Entrada|Contagem|Média|Os fluxos de entrada são o número de fluxos correntes na direção de entrada (tráfego que vai para o VM)|Nenhuma|
|Fluxos de saída|Fluxos de saída|Contagem|Média|Os fluxos de saída são o número de fluxos correntes na direção de saída (tráfego a sair do VM)|Nenhuma|
|Taxa máxima de criação dos fluxos de entrada|Taxa máxima de criação dos fluxos de entrada|CondeEmSegundo|Média|A taxa máxima de criação dos fluxos de entrada (tráfego que vai para o VM)|Nenhuma|
|Taxa máxima de criação dos fluxos de saída|Taxa máxima de criação dos fluxos de saída|CondeEmSegundo|Média|A taxa máxima de criação dos fluxos de saída (tráfego saindo do VM)|Nenhuma|
|Premium Data Disk Cache Ler Sucesso|Vídeo de cache de disco premium (pré-visualização)|Percentagem|Média|Premium Data Disk Cache Ler Sucesso|LUN|
|Cache de disco de dados premium ler miss|Cache de disco de dados premium Leia Miss (Pré-visualização)|Percentagem|Média|Cache de disco de dados premium ler miss|LUN|
|Premium OS Disk Cache Ler Sucesso|Premium OS Cache Cache Read Hit (Pré-visualização)|Percentagem|Média|Premium OS Disk Cache Ler Sucesso|Nenhuma|
|Cache de disco premium DO|Cache de disco premium OS Leia Miss (Pré-visualização)|Percentagem|Média|Cache de disco premium DO|Nenhuma|
|Rede no Total|Rede no Total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela(s) Virtual Machine(s) (Tráfego De Entrada)|Nenhuma|
|Rede out Total|Rede out Total|Bytes|Total|O número de bytes em todas as interfaces de rede pelas Máquinas Virtuais (Tráfego De saída)|Nenhuma|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuUsage|Utilização da CPU|Contagem|Média|Utilização de CPU em todos os núcleos em milislcores.|nome do recipiente|
|Uso da Memória|Utilização de Memória|Bytes|Média|Uso total da memória em byte.|nome do recipiente|
|NetworkBytesReceivedPerSecond|Bytes de rede recebidos por segundo|Bytes|Média|Os bytes da rede recebidos por segundo.|Nenhuma|
|NetworkBytesTransmittedPerSecond|Bytes de rede transmitidos por segundo|Bytes|Média|Os bytes da rede transmitidos por segundo.|Nenhuma|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registos

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Contagem total de pullcount|Contagem total de puxar|Contagem|Média|Número de imagenpuxa no total|Nenhuma|
|Contagem de PullCount bem sucedida|Contagem de puxar bem sucedida|Contagem|Média|Número de puxões de imagem bem sucedidos|Nenhuma|
|Contagem total de empurras|Contagem total de empurrões|Contagem|Média|Número de impulsos de imagem no total|Nenhuma|
|Contagem de PushCount bem sucedida|Contagem de impulsos bem sucedida|Contagem|Média|Número de impulsos de imagem bem sucedidos|Nenhuma|
|Duração da execução|Duração da execução|Milissegundos|Total|Duração do percurso em milissegundos|Nenhuma|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Número total de núcleos de CPU disponíveis num cluster gerido|Contagem|Média|Número total de núcleos de CPU disponíveis num cluster gerido|Nenhuma|
|kube_node_status_allocatable_memory_bytes|Quantidade total de memória disponível num cluster gerido|Bytes|Média|Quantidade total de memória disponível num cluster gerido|Nenhuma|
|kube_pod_status_ready|Número de cápsulas em estado pronto|Contagem|Média|Número de cápsulas em estado pronto|namespace,pod|
|kube_node_status_condition|Estados para várias condições do nó|Contagem|Média|Estados para várias condições do nó|condição,estado,status2,nó|
|kube_pod_status_phase|Número de cápsulas por fase|Contagem|Média|Número de cápsulas por fase|fase,espaço de nome,pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/fornecedores de recursos

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Pedidos de sucesso|Pedidos bem sucedidos|Contagem|Total|Pedidos bem sucedidos feitos pelo fornecedor personalizado|HttpMethod,CallPath,StatusCode|
|Pedidos Falhados|Pedidos com Falhas|Contagem|Total|Obtém os registos disponíveis para fornecedores de recursos personalizados|HttpMethod,CallPath,StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|NICReadThroughput|Ler A Mais(Rede)|BytesPerSecond|Média|A entrada de leitura da interface de rede no dispositivo no período de reporte para todos os volumes do gateway.|Nome de instância|
|NICWriteThroughput|Write Throughput (Rede)|BytesPerSecond|Média|A entrada de escrita da interface de rede no dispositivo no período de reporte para todos os volumes do gateway.|Nome de instância|
|CloudReadThroughputPerShare|Cloud Download Throughput (Share)|BytesPerSecond|Média|O download para Azure a partir de uma parte durante o período de reporte.|Partilhado|
|CloudUploadThroughputPerShare|Cloud Upload (Partilha)|BytesPerSecond|Média|A entrada de upload para Azure a partir de uma parte durante o período de reporte.|Partilhado|
|BytesUploadedToCloudPerShare|Cloud Bytes carregado (Partilhar)|Bytes|Média|O número total de bytes que é enviado para OT a partir de uma parte durante o período de reporte.|Partilhado|
|Capacidade Total|Capacidade Total|Bytes|Média|Capacidade Total|Nenhuma|
|Capacidade Disponível|Capacidade Disponível|Bytes|Média|A capacidade disponível em bytes durante o período de reporte.|Nenhuma|
|CloudUploadThroughput|Cloud Upload Throughput|BytesPerSecond|Média|A entrada de cloud upload para Azure durante o período de reporte.|Nenhuma|
|CloudReadThroughput|Cloud Download Throughput|BytesPerSecond|Média|A entrada de cloud download para Azure durante o período de reporte.|Nenhuma|
|BytesUploadedToCloud|Cloud Bytes Carregado (Dispositivo)|Bytes|Média|O número total de bytes que é enviado para Azure a partir de um dispositivo durante o período de reporte.|Nenhuma|
|Utilização de HiperVVirtualProcessor|Edge Compute - CPU percentual|Percentagem|Média|Por cento de utilização de CPU|Nome de instância|
|Utilização hyperVMemory|Edge Compute - Uso da Memória|Percentagem|Média|Quantidade de RAM em Uso|Nome de instância|


## <a name="microsoftdatacatalogdatacatalogs"></a>Microsoft.DataCatalog/datacatalogs

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Distribuição de ActivosByClassification|Distribuição de ativos por classificação|Contagem|Total|Indica o número de ativos com uma determinada classificação atribuída, ou seja, são classificados com essa etiqueta.|Classificação,Fonte|
|Tipo de armazenamento de bens|Distribuição de ativos por tipo de armazenamento|Contagem|Total|Indica o número de ativos com um determinado tipo de armazenamento.|Tipo de armazenamento|
|NumberOfAssetsWithClassifications|Número de ativos com pelo menos uma classificação|Contagem|Média|Indica o número de ativos com pelo menos uma classificação de etiqueta.|Nenhuma|
|ScanCancelado|Scan Cancelado|Contagem|Total|Indica o número de exames cancelados.|Nenhuma|
|ScanCompleted|Digitalização Concluída|Contagem|Total|Indica o número de exames concluídos com sucesso.|Nenhuma|
|ScanFailed|Scan Failed|Contagem|Total|Indica que o número de exames falhou.|Nenhuma|
|ScanTimeTaken|Tempo de digitalização tomado|Segundos|Total|Indica o tempo total de digitalização em segundos.|Nenhuma|
|Utilizadores Catalogativos|Utilizadores Ativos Diários|Contagem|Total|Número de utilizadores ativos diariamente|Nenhuma|
|Utilização de catálogos|Distribuição de Utilização por Operação|Contagem|Total|Indique o número de operação que o utilizador faz para o catálogo, ou é, Acesso, Pesquisa, Glossário.|Operação|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactores

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Corridas falhadas|Corridas falhadas|Contagem|Total||pipelineName,activityName|
|Corridas bem sucedidas|Corridas bem sucedidas|Contagem|Total||pipelineName,activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/fábricas

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PipelineFailedRuns|Gasoduto falhado executa métricas|Contagem|Total||Tipo de falha,Nome|
|PipelineSucceededRuns|Gasoduto bem sucedido executa métricas|Contagem|Total||Tipo de falha,Nome|
|PipelineCancelruns|Gasoduto cancelado executa métricas|Contagem|Total||Tipo de falha,Nome|
|ActivityFailedRuns|A atividade falhada executa métricas|Contagem|Total||Tipo de atividade,PipelineName,Tipo de Falha,Nome|
|ActivitySucceededRuns|Atividade bem sucedida executa métricas|Contagem|Total||Tipo de atividade,PipelineName,Tipo de Falha,Nome|
|Atividades Canceladas Runs|Atividade cancelada executa métricas|Contagem|Total||Tipo de atividade,PipelineName,Tipo de Falha,Nome|
|TriggerFailedRuns|O gatilho falhado corre métricas|Contagem|Total||Nome,FalhaType|
|TriggerSucceedruns|O gatilho bem sucedido corre métricas|Contagem|Total||Nome,FalhaType|
|TriggerCancelRuns|O gatilho cancelado corre métricas|Contagem|Total||Nome,FalhaType|
|IntegraçãoRuntimeCpuPercentage|Utilização do CPU em tempo de integração|Percentagem|Média||IntegraçãoRuntimeName,NodeName|
|IntegraçãoRuntimeAvailableMemory|Memória disponível de tempo de integração|Bytes|Média||IntegraçãoRuntimeName,NodeName|
|IntegraçãoRuntimeAverageTaskPickupDelay|Duração da fila de tempo de integração|Segundos|Média||IntegraçãoNome tempo de corrida de integração|
|IntegraçãoRuntimeQueueLength|Comprimento da fila de tempo de integração|Contagem|Média||IntegraçãoNome tempo de corrida de integração|
|IntegraçãoRuntimeAvailableNodeNumber|Contagem de tempo de integração disponível no nó|Contagem|Média||IntegraçãoNome tempo de corrida de integração|
|Contagem máxima de recursos permitidos|Contagem máxima permitida de entidades|Contagem|Máximo||Nenhuma|
|Unidades MaxallowedFactorySizeingb|Tamanho máximo permitido da fábrica (unidade GB)|Contagem|Máximo||Nenhuma|
|Contagem de Recursos|Total de entidades contam|Contagem|Máximo||Nenhuma|
|Unidades de FábricaSizeInGb|Tamanho total da fábrica (unidade GB)|Contagem|Máximo||Nenhuma|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/contas

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|JobEndedSuccess|Empregos bem sucedidos|Contagem|Total|Contagem de trabalhos bem sucedidos.|Nenhuma|
|JobEndedFailure|Empregos falhados|Contagem|Total|Contagem de empregos falhados.|Nenhuma|
|JobEndedCancelado|Empregos Cancelados|Contagem|Total|Contagem de empregos cancelados.|Nenhuma|
|JobauendedSuccess|Tempo de U de sucesso|Segundos|Total|Tempo total da UA para empregos bem sucedidos.|Nenhuma|
|JobauendedFail|Tempo falhado da UA|Segundos|Total|Tempo total da UA para empregos falhados.|Nenhuma|
|JobauendedCancelado|Hora da U Cancelada|Segundos|Total|Total de tempo da UA para trabalhos cancelados.|Nenhuma|
|Palco de Emprego|Empregos em Palco|Contagem|Total|Número de empregos em cada fase.|Nenhuma|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/contas

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalStorage|Armazenamento Total|Bytes|Máximo|Quantidade total de dados armazenados na conta.|Nenhuma|
|DataWritten|Dados Escritos|Bytes|Total|Quantidade total de dados escritos na conta.|Nenhuma|
|Leitura de Dados|Leitura de Dados|Bytes|Total|Quantidade total de dados lidos a partir da conta.|Nenhuma|
|WriteRequests|Pedidos de Escrita|Contagem|Total|Contagem de dados escrevem pedidos para a conta.|Nenhuma|
|Pedidos de Leitura|Ler Pedidos|Contagem|Total|Contagem de dados lê pedidos para a conta.|Nenhuma|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/contas

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Contagem de partilhas|Ações Enviadas|Contagem|Máximo|Número de ações enviadas na conta|Nome de partilha|
|Contagem de Subscrições de Ações|Ações Recebidas|Contagem|Máximo|Número de ações recebidas na conta|Nome de Subscrição de Ações|
|SucessoShareSynchronizations|Envio de imagens bem sucedidas da partilha|Contagem|Contagem|Número de ações enviadas conseguiu instantâneos na conta|Nenhuma|
|Sincronizações falhadas do ShareSync|Snapshots falhados da partilha enviada|Contagem|Contagem|Número de imagens falhadas da conta|Nenhuma|
|Subscrição de Subscrições de Subscrição de Subscrições bem sucedidas|Fotografias bem sucedidas da partilha recebida|Contagem|Contagem|Número de ações recebidas conseguiu instantâneos na conta|Nenhuma|
|Subscrição falhadaDesubscrição de subscrições|Fotos falhadas de partilha recebidas|Contagem|Contagem|Número de imagens falhadas da conta|Nenhuma|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servidores

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Por cento do CPU|Percentagem|Média|Por cento do CPU|Nenhuma|
|memory_percent|Por cento da memória|Percentagem|Média|Por cento da memória|Nenhuma|
|io_consumption_percent|IO por cento|Percentagem|Média|IO por cento|Nenhuma|
|storage_percent|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Nenhuma|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhuma|
|storage_limit|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Nenhuma|
|serverlog_storage_percent|Percentagem de armazenamento de registo do servidor|Percentagem|Média|Percentagem de armazenamento de registo do servidor|Nenhuma|
|serverlog_storage_usage|Armazenamento de registo do servidor utilizado|Bytes|Média|Armazenamento de registo do servidor utilizado|Nenhuma|
|serverlog_storage_limit|Limite de armazenamento de registo do servidor|Bytes|Média|Limite de armazenamento de registo do servidor|Nenhuma|
|active_connections|Conexões Ativas|Contagem|Média|Conexões Ativas|Nenhuma|
|connections_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Nenhuma|
|seconds_behind_master|Rema de replicação em segundos|Contagem|Máximo|Rema de replicação em segundos|Nenhuma|
|backup_storage_used|Armazenamento de cópia de segurança utilizado|Bytes|Média|Armazenamento de cópia de segurança utilizado|Nenhuma|
|network_bytes_egress|Saída da Rede|Bytes|Total|Network out através de conexões ativas|Nenhuma|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Rede Em conexões ativas|Nenhuma|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servidores

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Por cento do CPU|Percentagem|Média|Por cento do CPU|Nenhuma|
|memory_percent|Por cento da memória|Percentagem|Média|Por cento da memória|Nenhuma|
|io_consumption_percent|IO por cento|Percentagem|Média|IO por cento|Nenhuma|
|storage_percent|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Nenhuma|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhuma|
|storage_limit|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Nenhuma|
|serverlog_storage_percent|Percentagem de armazenamento de registo do servidor|Percentagem|Média|Percentagem de armazenamento de registo do servidor|Nenhuma|
|serverlog_storage_usage|Armazenamento de registo do servidor utilizado|Bytes|Média|Armazenamento de registo do servidor utilizado|Nenhuma|
|serverlog_storage_limit|Limite de armazenamento de registo do servidor|Bytes|Máximo|Limite de armazenamento de registo do servidor|Nenhuma|
|active_connections|Conexões Ativas|Contagem|Média|Conexões Ativas|Nenhuma|
|connections_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Nenhuma|
|seconds_behind_master|Rema de replicação em segundos|Contagem|Máximo|Rema de replicação em segundos|Nenhuma|
|backup_storage_used|Armazenamento de cópia de segurança utilizado|Bytes|Média|Armazenamento de cópia de segurança utilizado|Nenhuma|
|network_bytes_egress|Saída da Rede|Bytes|Total|Network out através de conexões ativas|Nenhuma|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Rede Em conexões ativas|Nenhuma|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servidores

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Por cento do CPU|Percentagem|Média|Por cento do CPU|Nenhuma|
|memory_percent|Por cento da memória|Percentagem|Média|Por cento da memória|Nenhuma|
|io_consumption_percent|IO por cento|Percentagem|Média|IO por cento|Nenhuma|
|storage_percent|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Nenhuma|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhuma|
|storage_limit|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Nenhuma|
|serverlog_storage_percent|Percentagem de armazenamento de registo do servidor|Percentagem|Média|Percentagem de armazenamento de registo do servidor|Nenhuma|
|serverlog_storage_usage|Armazenamento de registo do servidor utilizado|Bytes|Média|Armazenamento de registo do servidor utilizado|Nenhuma|
|serverlog_storage_limit|Limite de armazenamento de registo do servidor|Bytes|Máximo|Limite de armazenamento de registo do servidor|Nenhuma|
|active_connections|Conexões Ativas|Contagem|Média|Conexões Ativas|Nenhuma|
|connections_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Nenhuma|
|backup_storage_used|Armazenamento de cópia de segurança utilizado|Bytes|Média|Armazenamento de cópia de segurança utilizado|Nenhuma|
|network_bytes_egress|Saída da Rede|Bytes|Total|Network out através de conexões ativas|Nenhuma|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Rede Em conexões ativas|Nenhuma|
|pg_replica_log_delay_in_seconds|Réplica Lag|Segundos|Máximo|Rérplica em segundos|Nenhuma|
|pg_replica_log_delay_in_bytes|Max Lag Através de Réplicas|Bytes|Máximo|Lag in bytes da réplica mais atrasada|Nenhuma|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Por cento do CPU|Percentagem|Média|Por cento do CPU|Nenhuma|
|memory_percent|Por cento da memória|Percentagem|Média|Por cento da memória|Nenhuma|
|iops|IOPS|Contagem|Média|IO Operações por segundo|Nenhuma|
|storage_percent|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Nenhuma|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhuma|
|active_connections|Conexões Ativas|Contagem|Média|Conexões Ativas|Nenhuma|
|network_bytes_egress|Saída da Rede|Bytes|Total|Network out através de conexões ativas|Nenhuma|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Rede Em conexões ativas|Nenhuma|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft.DBforPostgreSQL/singleservers

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Por cento do CPU|Percentagem|Média|Por cento do CPU|Nenhuma|
|memory_percent|Por cento da memória|Percentagem|Média|Por cento da memória|Nenhuma|
|iops|IOPS|Contagem|Média|IO Operações por segundo|Nenhuma|
|storage_percent|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Nenhuma|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhuma|
|active_connections|Conexões Ativas|Contagem|Média|Conexões Ativas|Nenhuma|
|network_bytes_egress|Saída da Rede|Bytes|Total|Network out através de conexões ativas|Nenhuma|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Rede Em conexões ativas|Nenhuma|
|connections_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Nenhuma|
|connections_succeeded|Conexões bem sucedidas|Contagem|Total|Conexões bem sucedidas|Nenhuma|
|maximum_used_transactionIDs|IDs máximos de transação usada|Contagem|Média|IDs máximos de transação usada|Nenhuma|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|d2c.telemetria.ingress.allProtocol|Mensagem de telemetria enviar tentativas|Contagem|Total|Número de mensagens de telemetria dispositivo-para-nuvem tentou ser enviada para o seu hub IoT|Nenhuma|
|d2c.telemetria.ingress.success.success|Mensagens de telemetria enviadas|Contagem|Total|Número de mensagens de telemetria dispositivo-nuvem enviadas com sucesso para o seu hub IoT|Nenhuma|
|c2d.comandos.egress.complete.success|Entregas de mensagens C2D concluídas|Contagem|Total|Número de entregas de mensagens cloud-to-device concluídas com sucesso pelo dispositivo|Nenhuma|
|c2d.comandos.egress.abandone.success|Mensagens C2D abandonadas|Contagem|Total|Número de mensagens cloud-to-device abandonadas pelo dispositivo|Nenhuma|
|c2d.commands.egress.reject.success.success|Mensagens C2D rejeitadas|Contagem|Total|Número de mensagens cloud-to-device rejeitadas pelo dispositivo|Nenhuma|
|C2DMessagesExpired|Mensagens C2D Expiradas (pré-visualização)|Contagem|Total|Número de mensagens cloud-to-device expiradas|Nenhuma|
|dispositivos.totalDispositivos|Total de dispositivos (depreciados)|Contagem|Total|Número de dispositivos registados no seu hub IoT|Nenhuma|
|dispositivos.connectedDevices.allProtocol|Dispositivos conectados (depreciados) |Contagem|Total|Número de dispositivos ligados ao seu hub IoT|Nenhuma|
|d2c.telemetria.egress.sucesso|Encaminhamento: mensagens de telemetria entregues|Contagem|Total|O número de vezes que as mensagens foram entregues com sucesso a todos os pontos finais usando o encaminhamento do IoT Hub. Se uma mensagem for encaminhada para vários pontos finais, este valor aumenta em um para cada entrega bem sucedida. Se uma mensagem for entregue ao mesmo ponto final várias vezes, este valor aumenta por um para cada entrega bem sucedida.|Nenhuma|
|d2c.telemetria.egress.caiu|Encaminhamento: mensagens de telemetria caídas |Contagem|Total|O número de vezes que as mensagens foram retiradas pelo itinerário do IoT Hub devido a pontos sem saída. Este valor não conta as mensagens entregues para a rota de recuo, uma vez que as mensagens deixadas não são entregues lá.|Nenhuma|
|d2c.telemetria.egress.órfão|Encaminhamento: mensagens de telemetria órfãs |Contagem|Total|O número de mensagens foi órfão pelo encaminhamento do IoT Hub porque não correspondia a nenhuma regra de encaminhamento (incluindo a regra do recuo). |Nenhuma|
|d2c.telemetria.egress.inválido|Encaminhamento: mensagens de telemetria incompatíveis|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub não entregou mensagens devido a uma incompatibilidade com o ponto final. Este valor não inclui repetições.|Nenhuma|
|d2c.telemetria.egress.fallback|Encaminhamento: mensagens entregues para recuo|Contagem|Total|O número de vezes que o ioT Hub encaminha-se entregou mensagens ao ponto final associado à rota de recuo.|Nenhuma|
|d2c.endpoints.egress.eventHubs|Encaminhamento: mensagens entregues ao Event Hub|Contagem|Total|O número de vezes que o IoT Hub envia mensagens com sucesso entregou mensagens aos pontos finais do Event Hub.|Nenhuma|
|d2c.endpoints.latency.eventHubs|Encaminhamento: latência de mensagens para O Hub de Eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens no IoT Hub e a entrada de mensagens num ponto final do Event Hub.|Nenhuma|
|d2c.endpoints.egress.serviceBusQueues|Encaminhamento: mensagens entregues à Fila de Autocarros de Serviço|Contagem|Total|O número de vezes que o ioT Hub encaminha-se com sucesso entregou mensagens aos pontos finais da fila do Service Bus.|Nenhuma|
|d2c.endpoints.latency.serviceBusQueues|Encaminhamento: latência de mensagens para fila de autocarros de serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens no IoT Hub e a mensagem de telemetria entrando num ponto final da fila do Autocarro de Serviço.|Nenhuma|
|d2c.endpoints.egress.serviceBusTopics|Encaminhamento: mensagens entregues ao Tópico do Autocarro de Serviço|Contagem|Total|O número de vezes que o IoT Hub encaminha com sucesso entregou mensagens aos pontos finais do tópico do Bus de Serviço.|Nenhuma|
|d2c.endpoints.latency.serviceBusTopics|Encaminhamento: latência de mensagens para tópico de ônibus de serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens no IoT Hub e a mensagem de telemetria entrando num ponto final do tópico do Bus de Serviço.|Nenhuma|
|d2c.endpoints.egress.builtIn.events|Encaminhamento: mensagens entregues a mensagens/eventos|Contagem|Total|O número de vezes que o ioT Hub envia mensagens com sucesso entregou mensagens ao ponto final incorporado (mensagens/eventos).|Nenhuma|
|d2c.endpoints.latency.builtIn.events|Encaminhamento: latência de mensagens/eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens no IoT Hub e a mensagem de telemetria entrando no ponto final incorporado (mensagens/eventos).|Nenhuma|
|d2c.endpoints.egress.storage|Encaminhamento: mensagens entregues no armazenamento|Contagem|Total|O número de vezes que o ioT Hub encaminha com sucesso entregou mensagens para pontos finais de armazenamento.|Nenhuma|
|d2c.endpoints.latência.storage.storage|Encaminhamento: latência de mensagem para armazenamento|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens no IoT Hub e a mensagem de telemetria entrando num ponto final de armazenamento.|Nenhuma|
|d2c.endpoints.egress.storage.bytes.bytes|Encaminhamento: dados entregues ao armazenamento|Bytes|Total|A quantidade de dados (bytes) IoT Hub encaminhamento entregue em pontos finais de armazenamento.|Nenhuma|
|d2c.endpoints.egress.storage.blobs|Encaminhamento: bolhas entregues ao armazenamento|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou bolhas aos pontos finais de armazenamento.|Nenhuma|
|Entregas eventgrid|Entregas da Grelha de Eventos (pré-visualização)|Contagem|Total|O número de eventos do IoT Hub publicados na Event Grid. Utilize a dimensão Resultado para o número de pedidos bem sucedidos e falhados. A dimensão do Tipohttps://aka.ms/ioteventgrid)de Evento mostra o tipo de evento .|Resourceid,Resultado,Tipo de Evento|
|EventGridLatency|Latência da Grelha de Eventos (pré-visualização)|Milissegundos|Média|A latência média (milissegundos) de quando o evento Iot Hub foi gerado até quando o evento foi publicado para Event Grid. Este número é uma média entre todos os tipos de eventos. Utilize a dimensão EventType para ver a latência de um tipo específico de evento.|ResourceId,EventType|
|Entregas de encaminhamento|Entregas de encaminhamento (pré-visualização)|Milissegundos|Total|O número de vezes que o IoT Hub tentou entregar mensagens a todos os pontos finais usando o encaminhamento. Para ver o número de tentativas bem sucedidas ou falhadas, utilize a dimensão Resultado. Para ver a razão do fracasso, como inválido, abandonado ou órfão, utilize a dimensão FailureReasonCategory. Também pode utilizar as dimensões EndpointName e EndpointType para entender quantas mensagens foram entregues nos seus diferentes pontos finais. O valor métrico aumenta em uma para cada tentativa de entrega, incluindo se a mensagem é entregue em vários pontos finais ou se a mensagem for entregue ao mesmo ponto final várias vezes.|ResourceId,EndpointType,EndpointName,FailureReasonCategory,Resultado,RoutingSource|
|EncaminhamentoDeliveryLatency|Latência de entrega de encaminhamento (pré-visualização)|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens no IoT Hub e a mensagem de telemetria entrando num ponto final. Pode utilizar as dimensões EndpointName e EndpointType para compreender a latência dos seus diferentes pontos finais.|ResourceId,EndpointType,EndpointName,RoutingSource|
|d2c.twin.read.success.success|Leituras gémeas bem sucedidas de dispositivos|Contagem|Total|A contagem de todas as leituras gémeas bem sucedidas iniciadas pelo dispositivo.|Nenhuma|
|d2c.twin.read.failure.failure|Leituras gémeas falhadas de dispositivos|Contagem|Total|A contagem de todas as leituras gémeas iniciadas pelo dispositivo falhado.|Nenhuma|
|d2c.twin.read.size|Tamanho da resposta das leituras gémeas dos dispositivos|Bytes|Média|A média, min e máximo de todas as leituras gémeas bem sucedidas.|Nenhuma|
|d2c.twin.update.success.success|Atualizações gémeas bem-sucedidas a partir de dispositivos|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas pelo dispositivo.|Nenhuma|
|d2c.twin.update.failure.failure|Atualizações duplas falhadas de dispositivos|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas pelo dispositivo.|Nenhuma|
|d2c.twin.update.size|Tamanho das atualizações gémeas dos dispositivos|Bytes|Média|O tamanho médio, min e máximo de todas as atualizações gémeas iniciadas pelo dispositivo.|Nenhuma|
|c2d.methods.success.success|Invocações de método direto bem-sucedidas|Contagem|Total|A contagem de todas as chamadas de métodos diretos bem sucedidas.|Nenhuma|
|c2d.methods.failure|Invocações de método direto falhado|Contagem|Total|A contagem de todas as chamadas de métodos diretos falhadas.|Nenhuma|
|c2d.methods.requestSize|Solicitar dimensão das invocações do método direto|Bytes|Média|A média, min e máximo de todos os pedidos de método direto bem sucedidos.|Nenhuma|
|c2d.methods.responseSize|Tamanho da resposta das invocações do método direto|Bytes|Média|A média, min e máximo de todas as respostas de método direto bem sucedidas.|Nenhuma|
|c2d.twin.read.success.success|Twin de sucesso lê a partir de trás|Contagem|Total|A contagem de todas as leituras gémeas bem sucedidas.|Nenhuma|
|c2d.twin.read.failure.|Gémeo falhado lê a partir de trás|Contagem|Total|A contagem de todas as leituras gémeas iniciadas no final.|Nenhuma|
|c2d.twin.read.size|Tamanho da resposta de leituras gémeas da parte de trás|Bytes|Média|A média, min, e o máximo de todas as leituras gémeas bem sucedidas.|Nenhuma|
|c2d.twin.update.success|Atualizações gémeas bem sucedidas a partir de trás|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas no back-end.|Nenhuma|
|c2d.twin.update.failure.failure|Atualizações gémeas falhadas a partir de trás|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas no back-end.|Nenhuma|
|c2d.twin.update.size|Tamanho das atualizações gémeas a partir de trás|Bytes|Média|O tamanho médio, min e máximo de todas as atualizações gémeas iniciadas no back-end.|Nenhuma|
|twinQueries.sucesso|Consultas gémeas bem sucedidas|Contagem|Total|A contagem de todas as consultas gémeas bem sucedidas.|Nenhuma|
|twinQueries.failure|Perguntas gémeas falhadas|Contagem|Total|A contagem de todas as perguntas gémeas falhadas.|Nenhuma|
|twinQueries.resultaTamanho|Tamanho do resultado das consultas gémeas|Bytes|Média|A média, min, e o máximo do tamanho do resultado de todas as consultas gémeas bem sucedidas.|Nenhuma|
|jobs.createTwinUpdateJob.success|Criações bem sucedidas de empregos de atualização dupla|Contagem|Total|A contagem de toda a criação bem sucedida de empregos de atualização dupla.|Nenhuma|
|jobs.createTwinUpdateJob.failure|Criações falhadas de empregos de atualização dupla|Contagem|Total|A contagem de todos os empregos falhados de atualização dupla.|Nenhuma|
|jobs.createDirectMethodJob.success|Criações bem sucedidas de empregos de invocação de métodos|Contagem|Total|A contagem de todas as criações bem sucedidas de empregos de invocação de métodos diretos.|Nenhuma|
|jobs.createDirectMethodJob.failure|Criações falhadas de empregos de invocação de métodos|Contagem|Total|A contagem de todos os empregos de invocação de métodos diretos.|Nenhuma|
|empregos.listJobs.success|Chamadas bem sucedidas para listar empregos|Contagem|Total|A contagem de todas as chamadas bem sucedidas para listar empregos.|Nenhuma|
|empregos.listJobs.failure|Chamadas falhadas para listar empregos|Contagem|Total|A contagem de todas as chamadas falhadas para listar empregos.|Nenhuma|
|jobs.cancelJob.success|Cancelamentos de emprego bem sucedidos|Contagem|Total|A contagem de todas as chamadas bem sucedidas para cancelar um trabalho.|Nenhuma|
|jobs.cancelJob.failure|Cancelamentos de emprego falhados|Contagem|Total|A contagem de todas as chamadas falhadas para cancelar um trabalho.|Nenhuma|
|jobs.queryJobs.success|Consultas de emprego bem sucedidas|Contagem|Total|A contagem de todas as chamadas bem sucedidas para trabalhos de consulta.|Nenhuma|
|jobs.queryJobs.failure|Consultas de trabalho falhadas|Contagem|Total|A contagem de todas as chamadas falhadas para trabalhos de consulta.|Nenhuma|
|empregos.concluído|Trabalhos concluídos|Contagem|Total|A contagem de todos os trabalhos concluídos.|Nenhuma|
|empregos.falhou|Empregos falhados|Contagem|Total|A contagem de todos os empregos falhados.|Nenhuma|
|d2c.telemetria.ingress.sendThrottle|Número de erros de estrangulamento|Contagem|Total|Número de erros de estrangulamento devido a aceleração de entrada do dispositivo|Nenhuma|
|dailyMessageQuotaUsed|Número total de mensagens utilizadas|Contagem|Média|Número de mensagens totais usadas hoje|Nenhuma|
|dispositivoDataUsage|Utilização total dos dados do dispositivo|Bytes|Total|Bytes transferidos de e para quaisquer dispositivos ligados ao IotHub|Nenhuma|
|dispositivoDataUsageV2|Utilização total de dados do dispositivo (pré-visualização)|Bytes|Total|Bytes transferidos de e para quaisquer dispositivos ligados ao IotHub|Nenhuma|
|totaldeDispositivoCount|Total de dispositivos (pré-visualização)|Contagem|Média|Número de dispositivos registados no seu hub IoT|Nenhuma|
|connectedDeviceCount|Dispositivos conectados (pré-visualização)|Contagem|Média|Número de dispositivos ligados ao seu hub IoT|Nenhuma|
|configurações|Métricas de Configuração|Contagem|Total|Métricas para Operações de Configuração|Nenhuma|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Dispositivos/serviços de provisionamento

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Tentativas de Inscrição|Tentativas de registo|Contagem|Total|Número de registos de dispositivos tentados|ProvisioningServiceName,IotHubName,Status|
|Atribuição de Dispositivos|Dispositivos atribuídos|Contagem|Total|Número de dispositivos atribuídos a um hub IoT|ProvisioningServiceName,IotHubName|
|Tentativas de Attestation|Tentativas de atestação|Contagem|Total|Número de atestados de dispositivos tentados|ProvisioningServiceName,Status,Protocol|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Região Add|Região Adicionada|Contagem|Contagem|Região Adicionada|Região|
|Armazenamento Disponível|Armazenamento Disponível|Bytes|Total|Total de armazenamento disponível reportado em 5 minutos granularidade|Nome de recolha,Nome da Base de Dados,Região|
|Encerramentos de CassandraConnection|Encerramentos de conexões Cassandra|Contagem|Total|Número de ligações cassandra que foram fechadas, reportadas a uma granularidade de 1 minuto|APIType,Região,ClosureReason|
|CassandraKeyspaceDelete|Cassandra Keyspace apagada|Contagem|Contagem|Cassandra Keyspace apagada|Nome de recursos,ApiKind,ApiKindResourceType,OperationType|
|CassandraKeyspaceThroughputUpdate|Cassandra Keyspace Entrada atualizada|Contagem|Contagem|Cassandra Keyspace Entrada atualizada|Nome de recursos,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraKeyspaceUpdate|Cassandra Keyspace Atualizado|Contagem|Contagem|Cassandra Keyspace Atualizado|Nome de recursos,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraRequestCharges|Cassandra Request Charges|Contagem|Total|RUs consumidos para pedidos de Cassandra feitos|APIType,Nome de base de dados,Nome de recolha,Região,Tipo de Funcionamento,Tipo de Recursos|
|Pedidos de Cassandra|Pedidos de Cassandra|Contagem|Contagem|Número de pedidos de Cassandra feitos|APIType,Nome de base de dados,Nome de recolha,Região,Tipo de Operação,Tipo de Recurso,Errorcode|
|CassandraTableDelete|Mesa Cassandra apagada|Contagem|Contagem|Mesa Cassandra apagada|Nome de recursos,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|CassandraTableThroughputUpdate|Entrada de tabela Cassandra atualizada|Contagem|Contagem|Entrada de tabela Cassandra atualizada|Nome de recursos,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraTableUpdate|Tabela Cassandra Atualizada|Contagem|Contagem|Tabela Cassandra Atualizada|Nome de recursos,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CreateAccount|Conta Criada|Contagem|Contagem|Conta Criada|Nenhuma|
|Utilização de dados|Utilização de Dados|Bytes|Total|Utilização total de dados reportado sintetizando a 5 minutos granularidade|Nome de recolha,Nome da Base de Dados,Região|
|DeleteAccount|Conta Eliminada|Contagem|Contagem|Conta Eliminada|Nenhuma|
|Contagem de documentos|Contagem de Documentos|Contagem|Total|Contagem total de documentos reportada a 5 minutos de granularidade|Nome de recolha,Nome da Base de Dados,Região|
|DocumentQuota|Quota de Documento|Bytes|Total|Quota total de armazenamento reportada em 5 minutos granularidade|Nome de recolha,Nome da Base de Dados,Região|
|GremlinDatabaseDelete|Base de Dados Gremlin eliminada|Contagem|Contagem|Base de Dados Gremlin eliminada|Nome de recursos,ApiKind,ApiKindResourceType,OperationType|
|GremlinDatabaseThroughputupdate|Entrada de base de dados Gremlin atualizada|Contagem|Contagem|Entrada de base de dados Gremlin atualizada|Nome de recursos,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinDatabaseUpdate|Base de Dados Gremlin Atualizada|Contagem|Contagem|Base de Dados Gremlin Atualizada|Nome de recursos,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinGraphDelete|Gráfico de Gremlin eliminado|Contagem|Contagem|Gráfico de Gremlin eliminado|Nome de recursos,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|GremlinGraphThroughputUpdate|Entrada de gráfico de gremlin atualizada|Contagem|Contagem|Entrada de gráfico de gremlin atualizada|Nome de recursos,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinGraphUpdate|Gráfico de Gremlin Atualizado|Contagem|Contagem|Gráfico de Gremlin Atualizado|Nome de recursos,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|Utilização de índices|Utilização do índice|Bytes|Total|Utilização total do índice reportada a 5 minutos de granularidade|Nome de recolha,Nome da Base de Dados,Região|
|MetadadosPedidos|Pedidos de Metadados|Contagem|Contagem|Contagem de pedidos de metadados. Cosmos DB mantém recolha de metadados do sistema para cada conta, que lhe permite enumerar coleções, bases de dados, etc, e suas configurações, gratuitamente.|Nome de base de dados,Nome de recolha,Região,StatusCode,Função|
|MongoCollectionDelete|Coleção Mongo apagada|Contagem|Contagem|Coleção Mongo apagada|Nome de recursos,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|MongoCollectionThroughputUpdate|Mongo Collection Throughput Atualizado|Contagem|Contagem|Mongo Collection Throughput Atualizado|Nome de recursos,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoCollectionUpdate|Coleção Mongo Atualizada|Contagem|Contagem|Coleção Mongo Atualizada|Nome de recursos,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoDBDatabaseUpdate|Base de Dados de Mongo Atualizada|Contagem|Contagem|Base de Dados de Mongo Atualizada|Nome de recursos,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoDatabaseDelete|Base de Dados mongo eliminada|Contagem|Contagem|Base de Dados mongo eliminada|Nome de recursos,ApiKind,ApiKindResourceType,OperationType|
|MongoDatabaseThroughputUpdate|Entrada de base de dados de Mongo atualizada|Contagem|Contagem|Entrada de base de dados de Mongo atualizada|Nome de recursos,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoRequestCharge|Taxa de pedido de Mongo|Contagem|Total|Unidades de Pedido de Mongo Consumidas|Nome de base de dados,Nome de recolha,Região,Nome de Comando,Errorcode,Status|
|Pedidos de Mongo|Pedidos de Mongo|Contagem|Contagem|Número de pedidos de Mongo feitos|Nome de base de dados,Nome de recolha,Região,Nome de Comando,Errorcode,Status|
|MongoRequestsCount|Taxa de pedido de Mongo|CondeEmSegundo|Média|Pedido de Mongo Conde por segundo|Nome de base de dados,Nome de recolha,Região,Nome de Comando,Errorcode|
|MongoRequestsDelete|Taxa de pedido de exclusão de Mongo|CondeEmSegundo|Média|Mongo Apagar pedido por segundo|Nome de base de dados,Nome de recolha,Região,Nome de Comando,Errorcode|
|MongoRequestsInsert|Taxa de pedido de inserção de Mongo|CondeEmSegundo|Média|Mongo Inserir contagem por segundo|Nome de base de dados,Nome de recolha,Região,Nome de Comando,Errorcode|
|MongoRequestsQuery|Taxa de pedido de consulta de Mongo|CondeEmSegundo|Média|Pedido de consulta de Mongo por segundo|Nome de base de dados,Nome de recolha,Região,Nome de Comando,Errorcode|
|MongoRequestsUpdate|Taxa de pedido de atualização de Mongo|CondeEmSegundo|Média|Pedido de Atualização de Mongo por segundo|Nome de base de dados,Nome de recolha,Região,Nome de Comando,Errorcode|
|Consumo normalizado RU|Consumo ru normalizado|Percentagem|Máximo|Percentagem de consumo max RU por minuto|Nome de recolha,Nome da Base de Dados,Região|
|ProvisionedThroughput|Débito Aprovisionado|Contagem|Máximo|Débito Aprovisionado|Nome base de dados,Nome de recolha|
|RegiãoFailover|Região falhou|Contagem|Contagem|Região falhou|Nenhuma|
|Região de Remoção|Região Removida|Contagem|Contagem|Região Removida|Região|
|ReplicaçãoTardia|Latência de Replicação P99|MilliSeconds|Média|Latência de replicação P99 entre fonte e regiões-alvo para conta geoactiva|SourceRegion,TargetRegion|
|ServerSideLatency|Latência lateral do servidor|MilliSeconds|Média|Latência lateral do servidor|nome da base de dados,collectionname,região,Modo de Ligação,Tipo de Operação,PublicaPIType|
|ServiçoDisponibilidade|Disponibilidade de Serviço|Percentagem|Média|Conta solicita disponibilidade de uma hora, dia ou mês|Nenhuma|
|SqlContainerDelete|Recipiente Sql eliminado|Contagem|Contagem|Recipiente Sql eliminado|Nome de recursos,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|SqlContainerThroughputUpdate|Entrada de recipiente sql atualizada|Contagem|Contagem|Entrada de recipiente sql atualizada|Nome de recursos,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|SqlContainerUpdate|Recipiente Sql Atualizado|Contagem|Contagem|Recipiente Sql Atualizado|Nome de recursos,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|SqlDatabaseDelete|Base de Dados Sql Eliminada|Contagem|Contagem|Base de Dados Sql Eliminada|Nome de recursos,ApiKind,ApiKindResourceType,OperationType|
|SqlDatabaseThroughputUpdate|Entrada de base de dados Sql atualizada|Contagem|Contagem|Entrada de base de dados Sql atualizada|Nome de recursos,ApiKind,ApiKindResourceType,IsThroughputRequest|
|SqlDatabaseUpdate|Base de Dados Sql Atualizada|Contagem|Contagem|Base de Dados Sql Atualizada|Nome de recursos,ApiKind,ApiKindResourceType,IsThroughputRequest|
|Tabela tabelaDelete|Tabela AzureTable eliminada|Contagem|Contagem|Tabela AzureTable eliminada|Nome de recursos,ApiKind,ApiKindResourceType,OperationType|
|TableTableThroughputUpdate|Entrada de tabela azuretable atualizada|Contagem|Contagem|Entrada de tabela azuretable atualizada|Nome de recursos,ApiKind,ApiKindResourceType,IsThroughputRequest|
|TableTableUpdate|Tabela AzureTable Atualizada|Contagem|Contagem|Tabela AzureTable Atualizada|Nome de recursos,ApiKind,ApiKindResourceType,IsThroughputRequest|
|TotaldeUnidades de Pedidos|Total de Unidades de Pedido|Contagem|Total|Unidades de Pedido consumidas|Nome de base de dados,Nome de recolha,Região,StatusCode,OperationType,Status|
|TotalRequests|Total de Pedidos|Contagem|Contagem|Número de pedidos feitos|Nome de base de dados,Nome de recolha,Região,StatusCode,OperationType,Status|
|UpdateAccountKeys|Chaves de conta atualizadas|Contagem|Contagem|Chaves de conta atualizadas|Tipo de chave|
|Definições de Rede de Atualizações|Definições de rede de conta atualizadas|Contagem|Contagem|Definições de rede de conta atualizadas|Nenhuma|
|Definições de replicação de 'Actualizações', Replicação de Contas|Definições de replicação de conta atualizadas|Contagem|Contagem|Definições de replicação de conta atualizadas|Nenhuma|
|AtualizaçãoDiagnosticsSettings|Definições de diagnóstico de conta atualizadas|Contagem|Contagem|Definições de diagnóstico de conta atualizadas|DiagnósticoDefiniçõesNome,Nome do Grupo de Recursos|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft.EnterpriseKnowledgeGraph/services

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TransacçõesContagem|Contagem de Transações|Contagem|Contagem|Contagem total de transações|TransacçõesContagem|
|Contagem de Sucessos|Contagens com Êxito|Contagem|Contagem|Contagem de Transações Bem sucedidas|Contagem de Sucessos|
|FalhaCount|Contagem de Falhas|Contagem|Contagem|Contagem de Transações Falhadas|FalhaCount|
|SucessoLatency|Latência de Sucesso|MilliSeconds|Média|Latência de Transações Bem Sucedidas|Contagem de Sucessos|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domínios

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Contagem de Sucessos|Eventos Publicados|Contagem|Total|Total de eventos publicados para este tópico|Tópico|
|PublicarFailCount|Publicar Eventos Falhados|Contagem|Total|Eventos totais não publicaram para este tópico|Tópico,ErrorType,Error|
|PublicarSuccessLatencyInMs|Publicar Latência de Sucesso|Milissegundos|Total|Publicar latência de sucesso em milissegundos|Nenhuma|
|MatchedEventCount|Eventos Combinados|Contagem|Total|Total de eventos combinados com esta subscrição do evento|Tópico,Nome de Subscrição de Eventos,DomínioEventSubscriptionName|
|Contagem de falhas de tentativa de entrega|Eventos falhados de entrega|Contagem|Total|Eventos totais não entregaram a esta subscrição do evento|Tópico,Nome de Subscrição de Eventos,DomínioEventSubscriptionName,Error,ErrorType|
|Contagem de Sucessos de Entrega|Eventos Entregues|Contagem|Total|Total de eventos entregues a esta subscrição do evento|Tópico,Nome de Subscrição de Eventos,DomínioEventSubscriptionName|
|DestinationProcessingDurationInMs|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Tópico,Nome de Subscrição de Eventos,DomínioEventSubscriptionName|
|Contagem de eventos abandonados|Eventos abandonados|Contagem|Total|Total deixou cair eventos correspondentes a esta subscrição de evento|Tópico,Nome de Subscrição de Eventos,DomínioNome de Subscrição de Domínio,DropReason|
|DeadLetteredCount|Eventos com Letras Mortas|Contagem|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|Tópico,Nome de Subscrição de Eventos,Nome de Subscrição de Domínio,DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/tópicos

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Contagem de Sucessos|Eventos Publicados|Contagem|Total|Total de eventos publicados para este tópico|Nenhuma|
|PublicarFailCount|Publicar Eventos Falhados|Contagem|Total|Eventos totais não publicaram para este tópico|ErrorType,Error|
|Contagem de eventos incomparáveis|Eventos Incomparáveis|Contagem|Total|Eventos totais que não correspondem a nenhuma das subscrições do evento para este tópico|Nenhuma|
|PublicarSuccessLatencyInMs|Publicar Latência de Sucesso|Milissegundos|Total|Publicar latência de sucesso em milissegundos|Nenhuma|
|MatchedEventCount|Eventos Combinados|Contagem|Total|Total de eventos combinados com esta subscrição do evento|Nome de subscrição de eventos|
|Contagem de falhas de tentativa de entrega|Eventos falhados de entrega|Contagem|Total|Eventos totais não entregaram a esta subscrição do evento|Error,ErrorType,EventSubscriptionName|
|Contagem de Sucessos de Entrega|Eventos Entregues|Contagem|Total|Total de eventos entregues a esta subscrição do evento|Nome de subscrição de eventos|
|DestinationProcessingDurationInMs|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Nome de subscrição de eventos|
|Contagem de eventos abandonados|Eventos abandonados|Contagem|Total|Total deixou cair eventos correspondentes a esta subscrição de evento|DropReason,EventSubscriptionName|
|DeadLetteredCount|Eventos com Letras Mortas|Contagem|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Contagem de Sucessos|Eventos Publicados|Contagem|Total|Total de eventos publicados para este tópico|Nenhuma|
|PublicarFailCount|Publicar Eventos Falhados|Contagem|Total|Eventos totais não publicaram para este tópico|ErrorType,Error|
|Contagem de eventos incomparáveis|Eventos Incomparáveis|Contagem|Total|Eventos totais que não correspondem a nenhuma das subscrições do evento para este tópico|Nenhuma|
|PublicarSuccessLatencyInMs|Publicar Latência de Sucesso|Milissegundos|Total|Publicar latência de sucesso em milissegundos|Nenhuma|
|MatchedEventCount|Eventos Combinados|Contagem|Total|Total de eventos combinados com esta subscrição do evento|Nome de subscrição de eventos|
|Contagem de falhas de tentativa de entrega|Eventos falhados de entrega|Contagem|Total|Eventos totais não entregaram a esta subscrição do evento|Error,ErrorType,EventSubscriptionName|
|Contagem de Sucessos de Entrega|Eventos Entregues|Contagem|Total|Total de eventos entregues a esta subscrição do evento|Nome de subscrição de eventos|
|DestinationProcessingDurationInMs|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Nome de subscrição de eventos|
|Contagem de eventos abandonados|Eventos abandonados|Contagem|Total|Total deixou cair eventos correspondentes a esta subscrição de evento|DropReason,EventSubscriptionName|
|DeadLetteredCount|Eventos com Letras Mortas|Contagem|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|MatchedEventCount|Eventos Combinados|Contagem|Total|Total de eventos combinados com esta subscrição do evento|Nenhuma|
|Contagem de falhas de tentativa de entrega|Eventos falhados de entrega|Contagem|Total|Eventos totais não entregaram a esta subscrição do evento|Error,ErrorType|
|Contagem de Sucessos de Entrega|Eventos Entregues|Contagem|Total|Total de eventos entregues a esta subscrição do evento|Nenhuma|
|DestinationProcessingDurationInMs|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Nenhuma|
|Contagem de eventos abandonados|Eventos abandonados|Contagem|Total|Total deixou cair eventos correspondentes a esta subscrição de evento|DropReason|
|DeadLetteredCount|Eventos com Letras Mortas|Contagem|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Contagem de Sucessos|Eventos Publicados|Contagem|Total|Total de eventos publicados para este tópico|Nenhuma|
|PublicarFailCount|Publicar Eventos Falhados|Contagem|Total|Eventos totais não publicaram para este tópico|ErrorType,Error|
|Contagem de eventos incomparáveis|Eventos Incomparáveis|Contagem|Total|Eventos totais que não correspondem a nenhuma das subscrições do evento para este tópico|Nenhuma|
|PublicarSuccessLatencyInMs|Publicar Latência de Sucesso|Milissegundos|Total|Publicar latência de sucesso em milissegundos|Nenhuma|




## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/espaços de nome

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Pedidos bem sucedidos|Pedidos bem sucedidos|Contagem|Total|Pedidos de sucesso para Microsoft.EventHub.|Nome de entidade,OperationResult|
|Erros de servidor|Erros do servidor.|Contagem|Total|Erros de servidor para Microsoft.EventHub.|Nome de entidade,OperationResult|
|Erros de utilizador|Erros do utilizador.|Contagem|Total|Erros de utilizador para Microsoft.EventHub.|Nome de entidade,OperationResult|
|Erros Contingentes|Erros ultrapassados da quota.|Contagem|Total|Quota Exceeded Errors for Microsoft.EventHub.|Nome de entidade,OperationResult|
|ThrottledRequests|Pedidos estrangulados.|Contagem|Total|Pedidos throttled para Microsoft.EventHub.|Nome de entidade,OperationResult|
|Pedidos de entrada|Pedidos de Entrada|Contagem|Total|Pedidos de entrada para Microsoft.EventHub.|Nome da entidade|
|Mensagens de entrada|Mensagens de entrada|Contagem|Total|Mensagens de entrada para Microsoft.EventHub.|Nome da entidade|
|Mensagens de saída|Mensagens de saída|Contagem|Total|Mensagens de saída para Microsoft.EventHub.|Nome da entidade|
|IncomingBytes|Bytes de entrada.|Bytes|Total|Incoming Bytes para Microsoft.EventHub.|Nome da entidade|
|OutgoingBytes|Bytes de saída.|Bytes|Total|Bytes de saída para Microsoft.EventHub.|Nome da entidade|
|ActiveConnections|ActiveConnections|Contagem|Média|Total de conexões ativas para Microsoft.EventHub.|Nenhuma|
|Conexões Abertas|Ligações abertas.|Contagem|Média|Ligações abertas para Microsoft.EventHub.|Nome da entidade|
|Conexões Fechadas|Ligações fechadas.|Contagem|Média|Ligações fechadas para Microsoft.EventHub.|Nome da entidade|
|CaptureBacklog|Capture Backlog.|Contagem|Total|Capture Backlog para Microsoft.EventHub.|Nome da entidade|
|Mensagens Capturadas|Mensagens capturadas.|Contagem|Total|Mensagens capturadas para Microsoft.EventHub.|Nome da entidade|
|CapturedBytes|Bytes capturados.|Bytes|Total|Bytes capturados para Microsoft.EventHub.|Nome da entidade|
|Tamanho|Tamanho|Bytes|Média|Tamanho de um EventHub em Bytes.|Nome da entidade|
|INREQS|Pedidos de entrada (Preprecated)|Contagem|Total|Total de pedidos de envio de entrada para um espaço de nome (Deprecated)|Nenhuma|
|SUCCREQ|Pedidos bem sucedidos (Depreciados)|Contagem|Total|Total de pedidos bem sucedidos para um espaço de nome (Deprecated)|Nenhuma|
|FAILREQ|Pedidos Falhados (Preprecated)|Contagem|Total|Total de pedidos falhados para um espaço de nome (Deprecated)|Nenhuma|
|SVRBSY|Erros ocupados do servidor (Deprecated)|Contagem|Total|Total de erros ocupados do servidor para um espaço de nome (Deprecated)|Nenhuma|
|INTERR|Erros internos do servidor (Deprecated)|Contagem|Total|Total de erros internos do servidor para um espaço de nome (Deprecated)|Nenhuma|
|MISCERR|Outros Erros (Deprecated)|Contagem|Total|Total de pedidos falhados para um espaço de nome (Deprecated)|Nenhuma|
|INMSGS|Mensagens de entrada (obsoletas) (Depreciadas)|Contagem|Total|Total de mensagens de entrada para um espaço de nome. Esta métrica está depreciada. Utilize a métrica de Mensagens Incoming (Depreciada)|Nenhuma|
|EHINMSGS|Mensagens de entrada (Depreciadas)|Contagem|Total|Total de mensagens de entrada para um espaço de nome (Deprecated)|Nenhuma|
|Outmsgs|Mensagens de saída (obsoletas) (Depreciadas)|Contagem|Total|Total de mensagens de saída para um espaço de nome. Esta métrica está depreciada. Utilize a métrica das Mensagens De saída (Depreciada)|Nenhuma|
|EHOUTMSGS|Mensagens de saída (Depreciadas)|Contagem|Total|Total de mensagens de saída para um espaço de nome (Deprecated)|Nenhuma|
|EHINMBS|Bytes de entrada (obsoletos) (Depreciados)|Bytes|Total|Entrada de mensagem do Event Hub para um espaço de nome. Esta métrica está depreciada. Utilize a métrica incoming bytes (Depreciada)|Nenhuma|
|EHINBYTES|Bytes de entrada (Deprecated)|Bytes|Total|Entrada de mensagem do Event Hub para um espaço de nome (Deprecated)|Nenhuma|
|EHOUTMBS|Bytes de saída (obsoletos) (Depreciados)|Bytes|Total|Entrada de mensagem de saída do Event Hub para um espaço de nome. Esta métrica está depreciada. Utilize a métrica de bytes de saída (Depreciada)|Nenhuma|
|EHOUTBYTES|Bytes de saída (Deprecated)|Bytes|Total|Entrada de mensagem de saída do Event Hub para um espaço de nome (Deprecated)|Nenhuma|
|EHABL|Mensagens de atraso de arquivo (Depreciadas)|Contagem|Total|Mensagens de arquivo do Event Hub em atraso para um espaço de nome (Deprecated)|Nenhuma|
|EHAMSGS|Mensagens de arquivo (Depreciadas)|Contagem|Total|O Event Hub arquivou mensagens num espaço de nome (Deprecated)|Nenhuma|
|EHAMBs|Entrada de mensagem de arquivo (Depreciada)|Bytes|Total|Web de mensagem arquivada do Event Hub num espaço de nome (Deprecated)|Nenhuma|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Pedidos bem sucedidos|Pedidos bem sucedidos|Contagem|Total|Pedidos de sucesso para Microsoft.EventHub.|OperaçãoResult|
|Erros de servidor|Erros do servidor.|Contagem|Total|Erros de servidor para Microsoft.EventHub.|OperaçãoResult|
|Erros de utilizador|Erros do utilizador.|Contagem|Total|Erros de utilizador para Microsoft.EventHub.|OperaçãoResult|
|Erros Contingentes|Erros ultrapassados da quota.|Contagem|Total|Quota Exceeded Errors for Microsoft.EventHub.|OperaçãoResult|
|ThrottledRequests|Pedidos estrangulados.|Contagem|Total|Pedidos throttled para Microsoft.EventHub.|OperaçãoResult|
|Pedidos de entrada|Pedidos de Entrada|Contagem|Total|Pedidos de entrada para Microsoft.EventHub.|Nenhuma|
|Mensagens de entrada|Mensagens de entrada|Contagem|Total|Mensagens de entrada para Microsoft.EventHub.|Nenhuma|
|Mensagens de saída|Mensagens de saída|Contagem|Total|Mensagens de saída para Microsoft.EventHub.|Nenhuma|
|IncomingBytes|Bytes de entrada.|Bytes|Total|Incoming Bytes para Microsoft.EventHub.|Nenhuma|
|OutgoingBytes|Bytes de saída.|Bytes|Total|Bytes de saída para Microsoft.EventHub.|Nenhuma|
|ActiveConnections|ActiveConnections|Contagem|Média|Total de conexões ativas para Microsoft.EventHub.|Nenhuma|
|Conexões Abertas|Ligações abertas.|Contagem|Média|Ligações abertas para Microsoft.EventHub.|Nenhuma|
|Conexões Fechadas|Ligações fechadas.|Contagem|Média|Ligações fechadas para Microsoft.EventHub.|Nenhuma|
|CaptureBacklog|Capture Backlog.|Contagem|Total|Capture Backlog para Microsoft.EventHub.|Nenhuma|
|Mensagens Capturadas|Mensagens capturadas.|Contagem|Total|Mensagens capturadas para Microsoft.EventHub.|Nenhuma|
|CapturedBytes|Bytes capturados.|Bytes|Total|Bytes capturados para Microsoft.EventHub.|Nenhuma|
|CPU|CPU|Percentagem|Máximo|Utilização do CPU para o Cluster do Hub de Eventos em percentagem|Função|
|Memória Disponível|Memória Disponível|Percentagem|Máximo|Memória disponível para o Cluster Do Hub de Eventos em percentagem da memória total.|Função|
|Tamanho|Tamanho de um EventHub em Bytes.|Bytes|Média|Tamanho de um EventHub em Bytes.|Função|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|GatewayRequests|Pedidos gateway|Contagem|Total|Número de pedidos de gateway|HttpStatus|
|Pedidos categorizados gateway|Pedidos categorizados de gateway|Contagem|Total|Número de pedidos de gateway por categorias (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|Trabalhadores NumActivos|Número de Trabalhadores Ativos|Contagem|Máximo|Número de Trabalhadores Ativos|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Valor Métrico Observado|Valor Métrico Observado|Contagem|Média|O valor calculado por escala automática quando executado|MetricTriggerSource|
|Limiar Métrico|Limiar Métrico|Contagem|Média|O limiar de escala automática configurado quando a escala automática funcionava.|Regra do gatilho métrico|
|Capacidade Observada|Capacidade Observada|Contagem|Média|A capacidade reportada à escala automática quando foi executada.|Nenhuma|
|ScaleActionsIniciado|Ações de escala iniciadas|Contagem|Total|A direção da operação de escala.|Direção de Escala|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Componentes

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|disponibilidadeResultados/disponibilidadePercentage|Disponibilidade|Percentagem|Média|Percentagem de testes de disponibilidade concluídos com sucesso|disponibilidadeResult/nome,disponibilidadeResult/localização|
|disponibilidadeResultados/contagem|Testes de disponibilidade|Contagem|Contagem|Contagem de testes de disponibilidade|disponibilidadeResult/nome,disponibilidadeResult/localização,disponibilidadeResult/sucesso|
|disponibilidadeResultados/duração|Duração do teste de disponibilidade|MilliSeconds|Média|Duração do teste de disponibilidade|disponibilidadeResult/nome,disponibilidadeResult/localização,disponibilidadeResult/sucesso|
|navegadorTimings/redeDura|Tempo de ligação da rede de carga de página|MilliSeconds|Média|Tempo entre o pedido do utilizador e a ligação à rede. Inclui a aparência DNS e a ligação de transporte.|Nenhuma|
|navegadorTimings/processamentoDuração|Tempo de processamento do cliente|MilliSeconds|Média|Tempo entre receber o último byte de um documento até que o DOM esteja carregado. Os pedidos de asincronização podem ainda estar a ser processados.|Nenhuma|
|navegadorTimings/recepçãoDura|Tempo de resposta de receção|MilliSeconds|Média|Tempo entre o primeiro e o último bytes, ou até a desconexão.|Nenhuma|
|navegadorTimings/sendDura|Enviar tempo de pedido|MilliSeconds|Média|Tempo entre a ligação da rede e receber o primeiro byte.|Nenhuma|
|browserTimings/totalDuração|Tempo de carga da página do navegador|MilliSeconds|Média|O tempo do pedido do utilizador até do M, folhas de estilo, scripts e imagens são carregados.|Nenhuma|
|dependências/contagem|Chamadas de dependência|Contagem|Contagem|Contagem de chamadas feitas pela aplicação a recursos externos.|dependência/tipo,dependência/desempenhoBucket,dependência/sucesso,dependência/target,dependência/resultadoCódigo,operação/sintético, nuvem/roleInstance,cloud/roleName|
|dependências/duração|Duração da dependência|MilliSeconds|Média|Duração das chamadas efetuadas pela aplicação a recursos externos.|dependência/tipo,dependência/desempenhoBucket,dependência/sucesso,dependência/target,dependência/resultadoCódigo,operação/sintético, nuvem/roleInstance,cloud/roleName|
|dependências/falhado|Falhas de chamada de dependência|Contagem|Contagem|Contagem de chamadas de dependência falhadas feitas pela aplicação a recursos externos.|dependência/tipo,dependência/desempenhoBucket,dependência/sucesso,dependência/target,dependência/resultadoCódigo,operação/sintético, nuvem/roleInstance,cloud/roleName|
|pageViews/count|Vistas da página|Contagem|Contagem|Contagem de visualizações de página.|operação/sintético,cloud/roleName|
|páginaVistas/duração|Tempo de carga da vista da página|MilliSeconds|Média|Tempo de carga da vista da página|operação/sintético,cloud/roleName|
|performanceCounters/requestExecutionTime|HTTP solicitar tempo de execução|MilliSeconds|Média|Tempo de execução do pedido mais recente.|nuvem/roleInstance|
|performanceCounters/requestsInQueue|PEDIDOS HTTP na fila de candidaturas|Contagem|Média|Duração da fila do pedido de pedido de inscrição.|nuvem/roleInstance|
|performanceCounters/requestsPerSecond|Taxa de pedido http|CondeEmSegundo|Média|Taxa de todos os pedidos ao pedido por segundo a partir de ASP.NET.|nuvem/roleInstance|
|desempenhoCounters/excepçõesPerSecond|Taxa de exceção|CondeEmSegundo|Média|Contagem de exceções manipuladas e não manipuladas reportadas às janelas, incluindo exceções .NET e exceções não geridas que são convertidas em exceções .NET.|nuvem/roleInstance|
|performanceCounters/processIOBytesPerSecond|Taxa IO do processo|BytesPerSecond|Média|Total de bytes por segundo lido e escrito para ficheiros, rede e dispositivos.|nuvem/roleInstance|
|performanceCounters/processCpuPercentage|CpU processo|Percentagem|Média|A percentagem de tempo decorrido que todos os fios de processo utilizaram o processador para executar instruções. Isto pode variar entre 0 a 100. Esta métrica indica o desempenho do processo w3wp sozinho.|nuvem/roleInstance|
|desempenhoCounters/processadorCpuPercentage|Hora do processador|Percentagem|Média|A percentagem de tempo que o processador passa em fios não ociosos.|nuvem/roleInstance|
|desempenhoCounters/memoryAvailableBytes|Memória disponível|Bytes|Média|Memória física imediatamente disponível para alocação a um processo ou utilização do sistema.|nuvem/roleInstance|
|performanceCounters/processPrivateBytes|Processo bytes privados|Bytes|Média|Memória exclusivamente atribuída aos processos da aplicação monitorizada.|nuvem/roleInstance|
|pedidos/duração|Tempo de resposta do servidor|MilliSeconds|Média|Tempo entre receber um pedido http e terminar o envio da resposta.|pedido/desempenhoBucket,request/resultCódigo,operação/sintético,cloud/roleInstance,request/success,cloud/roleName|
|pedidos/contagem|Pedidos de servidor|Contagem|Contagem|Contagem dos pedidos http concluídos.|pedido/desempenhoBucket,request/resultCódigo,operação/sintético,cloud/roleInstance,request/success,cloud/roleName|
|pedidos/falhado|Pedidos falhados|Contagem|Contagem|Contagem de pedidos HTTP marcados como falhados. Na maioria dos casos, trata-se de pedidos com um código de resposta >= 400 e não igual a 401.|pedido/desempenhoBucket,request/resultCódigo,pedido/sucesso,operação/sintético,cloud/roleInstance,cloud/roleName|
|pedidos/taxa|Taxa de pedido do servidor|CondeEmSegundo|Média|Taxa de pedidos de servidor por segundo|pedido/desempenhoBucket,request/resultCódigo,operação/sintético,cloud/roleInstance,request/success,cloud/roleName|
|exceções/contagem|Exceções|Contagem|Contagem|Contagem combinada de todas as exceções não apanhadas.|cloud/roleName,cloud/roleInstance,cliente/tipo|
|exceções/navegador|Exceções de browser|Contagem|Contagem|Contagem de exceções não apanhadas lançadas no navegador.|cliente/isServer,cloud/roleName|
|exceções/servidor|Exceções ao servidor|Contagem|Contagem|Contagem de exceções não apanhadas lançadas na aplicação do servidor.|cliente/isServer,cloud/roleName,cloud/roleInstance|
|vestígios/contagem|Traces|Contagem|Contagem|Contagem de documentos de rastreio|traço/gravidadeN,operação/sintético,cloud/roleName,cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|connectedDeviceCount|Total de dispositivos conectados|Contagem|Média|Número de dispositivos ligados à IoT Central|Nenhuma|
|c2d.property.read.success|Successful Device Property Reads from IoT Central|Contagem|Total|A contagem de todas as leituras de propriedade de sucesso iniciada a partir da IoT Central|Nenhuma|
|c2d.property.read.failure|Propriedade do dispositivo falhado lê da IoT Central|Contagem|Total|A contagem de todas as leituras de propriedade falhada iniciada a partir da IoT Central|Nenhuma|
|d2c.property.read.success|Imóvel de sucesso lê a partir de dispositivos|Contagem|Total|A contagem de todas as leituras de propriedades bem sucedidas iniciadaa a partir de dispositivos|Nenhuma|
|d2c.property.read.failure|Propriedade do dispositivo falhado lê a partir de dispositivos|Contagem|Total|A contagem de todas as leituras de propriedade falhada iniciada a partir de dispositivos|Nenhuma|
|c2d.property.update.success|Atualizações de propriedade de dispositivos bem-sucedidos da IoT Central|Contagem|Total|A contagem de todas as atualizações de propriedade bem sucedidas iniciadas a partir da IoT Central|Nenhuma|
|c2d.property.update.failure|Atualizações de propriedade de dispositivos falhados da IoT Central|Contagem|Total|A contagem de todas as atualizações de propriedade falhadas iniciadas a partir da IoT Central|Nenhuma|
|d2c.property.update.success.success|Atualizações de propriedade de dispositivos bem-sucedidos de dispositivos|Contagem|Total|A contagem de todas as atualizações de propriedade bem sucedidas iniciadas a partir de dispositivos|Nenhuma|
|d2c.property.update.failure.failure|Atualizações de propriedade de dispositivos falhados a partir de dispositivos|Contagem|Total|A contagem de todas as atualizações de propriedade falhadas iniciadas a partir de dispositivos|Nenhuma|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/cofres

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServiceApiHit|Total de acesso síldo de serviço Api Hits|Contagem|Contagem|Número de acessos totais de api de serviço|Tipo de Atividade,Nome de Atividade|
|AtendimentoApiLatency|Atncy api de serviço geral|Milissegundos|Média|Latência geral dos pedidos de api de serviço|Tipo de atividade,Nome de atividade,StatusCode,StatusCodeClass|
|ServiçoApiResult|Resultados totais da Api de serviço|Contagem|Contagem|Número de resultados totais da api de serviço|Tipo de atividade,Nome de atividade,StatusCode,StatusCodeClass|
|Caixa de SaturationShoebox|Saturação geral do cofre|Percentagem|Média|Capacidade do cofre utilizada|Tipo de atividade,Nome de atividade,Tipo de Transação|
|Disponibilidade|Disponibilidade geral do cofre|Percentagem|Média|Abóbada solicita disponibilidade de pedidos|Tipo de atividade,Nome de atividade,StatusCode,StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CacheUtilização|Utilização de cache|Percentagem|Média|Nível de utilização no âmbito do cluster|Nenhuma|
|ConsultaDuração|Duração da consulta|Milissegundos|Média|Duração das consultas em segundos|ConsultaStatus|
|IngestionUtilização|Utilização de ingestão|Percentagem|Média|Relação entre as ranhuras de ingestão utilizadas no cluster|Nenhuma|
|KeepAlive|Mantenha-se vivo|Contagem|Média|Verificação de sanidade indica que o cluster responde a consultas|Nenhuma|
|IngestionVolumeInMB|Volume de ingestão (em MB)|Contagem|Total|Volume global de dados ingeridos para o cluster (em MB)|Base de Dados|
|IngestionLatencyInSeconds|Latência de ingestão (em segundos)|Segundos|Média|Tempo de ingestão da fonte (por exemplo, mensagem está no EventHub) para o cluster em segundos|Nenhuma|
|EventosProcessadosForEventHubs|Eventos processados (para Hubs de Evento/IoT)|Contagem|Total|Número de eventos processados pelo cluster ao ingerir em Event/IoT Hub|EventStatus|
|IngestionResult|Resultado da ingestão|Contagem|Contagem|Número de operações de ingestão|IngestionResultDetails|
|CPU|CPU|Percentagem|Média|Nível de utilização do CPU|Nenhuma|
|Exportação ContínuaNumOfRecordsExported|Exportação contínua – numdos de registos exportados|Contagem|Total|Número de registos exportados, disparados por cada artefacto de armazenamento escrito durante a operação de exportação|Nome de exportação contínua,Base de Dados|
|ExportUtilização|Utilização das exportações|Percentagem|Máximo|Utilização das exportações|Nenhuma|
|Contagem contínua de gastos com exportações|Contagem pendente de exportação contínua|Contagem|Máximo|O número de postos de trabalho de exportação continuados pendentes prontos para a execução|Nenhuma|
|ContinuamenteExportMaxLatenessMinutes|Exportação Contínua Max Lateness|Contagem|Máximo|O atraso (em minutos) reportado pelos contínuos trabalhos de exportação no cluster|Nenhuma|
|Resultado contínuo da exportação|Resultado contínuo da exportação|Contagem|Contagem|Indica se a Exportação Contínua foi bem sucedida ou fracassada|Nome de exportação contínua,Resultado,Base de Dados|
|StreamingIngestDura|Duração do ingest de streaming|Milissegundos|Média|Streaming de duração da ingestão em milissegundos|Nenhuma|
|StreamingIngestDataRate|Taxa de dados de ingestão de streaming|Contagem|Média|Taxa de dados de ingerir streaming (MB por segundo)|Nenhuma|
|Taxa de solicitação a vapor|Taxa de pedido de ingestão de streaming|Contagem|RateRequestsPerSecond|Taxa de pedido de influxo de streaming (pedidos por segundo)|Nenhuma|
|StreamingIngestResults|Resultado da ingest de streaming|Contagem|Média|Resultado de streaming de ingerir|Resultado|
|TotalNumberOfConcurrentQueries|Número total de consultas simultâneas|Contagem|Total|Número total de consultas simultâneas|Nenhuma|
|TotalNumberOfThrottledQueries|Número total de consultas estranguladas|Contagem|Total|Número total de consultas estranguladas|Nenhuma|
|TotalNumberOfThrottledCommands|Número total de comandos estrangulados|Contagem|Total|Número total de comandos estrangulados|Tipo de comando|
|TotalNumberOfExtents|Número total de extensões|Contagem|Total|Número total de extensões de dados|Nenhuma|
|Contagem de casos|Contagem de Instâncias|Contagem|Média|Contagem total de instâncias|Nenhuma|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RunsStarted|Corridas Iniciadas|Contagem|Total|O número de corridas de fluxo de trabalho começou.|Nenhuma|
|Execuções Concluídas|Execuções Concluídas|Contagem|Total|Número de fluxos de trabalho concluídos.|Nenhuma|
|RunsSucceed|Runs Succeeded|Contagem|Total|O número de corridas de fluxo de trabalho foi bem sucedido.|Nenhuma|
|RunsFailed|Execuções falhadas|Contagem|Total|O número de corridas de fluxo de trabalho falhou.|Nenhuma|
|RunsCancelado|Execuções canceladas|Contagem|Total|Número de fluxos de trabalho cancelados.|Nenhuma|
|RunLatency|Executar Latência|Segundos|Média|Latência de corridas completas de fluxo de trabalho.|Nenhuma|
|RunSuccessLatency|Latência de Sucesso de Execução|Segundos|Média|Latência de corridas de fluxo de trabalho bem sucedidas.|Nenhuma|
|Eventos RunThrottled|Eventos Throttled Run|Contagem|Total|Número de ação de fluxo de trabalho ou de provocar eventos acelerados.|Nenhuma|
|Eventos RunStartThrottled|Executar eventos throttled|Contagem|Total|O número de fluxos de trabalho iniciam eventos acelerados.|Nenhuma|
|RunFailurePercentage|Percentagem de falhas de execução|Percentagem|Total|A percentagem de corridas de fluxo de trabalho falhou.|Nenhuma|
|Ações Iniciadas|Ações Iniciadas |Contagem|Total|O número de ações de fluxo de trabalho começou.|Nenhuma|
|AçõesConcluídas|Ações Concluídas |Contagem|Total|Número de ações de fluxo de trabalho concluídas.|Nenhuma|
|Ações Bem sucedidas|Ações bem sucedidas |Contagem|Total|O número de ações de fluxo de trabalho foi bem sucedido.|Nenhuma|
|Ações Falhadas|Ações falhadas |Contagem|Total|O número de ações de fluxo de trabalho falhou.|Nenhuma|
|Ações Ignoradas|Ações ignoradas |Contagem|Total|O número de ações de fluxo de trabalho ignoradas.|Nenhuma|
|AçãoLatency|Latência de Ação |Segundos|Média|Latência de ações completas de fluxo de trabalho.|Nenhuma|
|ActionSuccessLatency|Latência de Sucesso de Ação |Segundos|Média|Latência de ações de fluxo de trabalho bem sucedidas.|Nenhuma|
|Eventos ActionThrottled|Eventos throttled de ação|Contagem|Total|Número de ação de fluxo de trabalho acelerou eventos.|Nenhuma|
|GatilhosIniciado|Gatilhos Iniciados |Contagem|Total|O número de gatilhos de fluxo de trabalho começou.|Nenhuma|
|Gatilhos Concluídos|Gatilhos Concluídos |Contagem|Total|Número de gatilhos de fluxo de trabalho concluídos.|Nenhuma|
|Gatilhos Bem sucedidos|Gatilhos bem sucedidos |Contagem|Total|O número de gatilhos de fluxo de trabalho foi bem sucedido.|Nenhuma|
|Gatilhos Falhados|Gatilhos falhados |Contagem|Total|O número de gatilhos de fluxo de trabalho falhou.|Nenhuma|
|Gatilhos Skipped|Gatilhos ignorados|Contagem|Total|O número de gatilhos de fluxo de trabalho saltou.|Nenhuma|
|TriggersFired|Gatilhos disparados |Contagem|Total|Número de acionadores de fluxo de trabalho disparados.|Nenhuma|
|GatilhoLatency|Latência do gatilho |Segundos|Média|Latência dos gatilhos de fluxo de trabalho completos.|Nenhuma|
|TriggerFireLatency|Latência de disparo |Segundos|Média|Latência de disparados acionadores de fluxo de trabalho.|Nenhuma|
|TriggerSuccessLatency|Latência de Sucesso do Gatilho |Segundos|Média|Latência dos desencadeadores de fluxo de trabalho bem sucedidos.|Nenhuma|
|Eventos TriggerThrottled|Eventos throttled de gatilho|Contagem|Total|Número de eventos de fluxo de trabalho acelerados.|Nenhuma|
|Execuções billableAction|Execuções de Ação Billable|Contagem|Total|Número de execuções de ação de fluxo de trabalho a serem cobradas.|Nenhuma|
|Execuções BillableTrigger|Execuções de gatilho sinuosos|Contagem|Total|O número de execuções de fluxo de trabalho a ser cobrado.|Nenhuma|
|Execuções Totais Billáveis|Execuções Totais De Faturação|Contagem|Total|Número de execuções de fluxo de trabalho a ser faturadas.|Nenhuma|
|BillingUsageNativeOperation|Utilização de faturação para execuções de operações nativas|Contagem|Total|Número de execuções de operações nativas a serem cobradas.|Nenhuma|
|BillingUsageStandardConnector|Utilização de faturação para execuções de conector padrão|Contagem|Total|Número de execuções de conector padrão a serem cobradas.|Nenhuma|
|Consumo de armazenamento de armazenamento de dólares de faturação|Utilização de faturação para execuções de consumo de armazenamento|Contagem|Total|Número de execuções de consumo de armazenamento a serem cobradas.|Nenhuma|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Lógica/integraçãoServiceEnvironments

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RunsStarted|Corridas Iniciadas|Contagem|Total|O número de corridas de fluxo de trabalho começou.|Nenhuma|
|Execuções Concluídas|Execuções Concluídas|Contagem|Total|Número de fluxos de trabalho concluídos.|Nenhuma|
|RunsSucceed|Runs Succeeded|Contagem|Total|O número de corridas de fluxo de trabalho foi bem sucedido.|Nenhuma|
|RunsFailed|Execuções falhadas|Contagem|Total|O número de corridas de fluxo de trabalho falhou.|Nenhuma|
|RunsCancelado|Execuções canceladas|Contagem|Total|Número de fluxos de trabalho cancelados.|Nenhuma|
|RunLatency|Executar Latência|Segundos|Média|Latência de corridas completas de fluxo de trabalho.|Nenhuma|
|RunSuccessLatency|Latência de Sucesso de Execução|Segundos|Média|Latência de corridas de fluxo de trabalho bem sucedidas.|Nenhuma|
|Eventos RunThrottled|Eventos Throttled Run|Contagem|Total|Número de ação de fluxo de trabalho ou de provocar eventos acelerados.|Nenhuma|
|Eventos RunStartThrottled|Executar eventos throttled|Contagem|Total|O número de fluxos de trabalho iniciam eventos acelerados.|Nenhuma|
|RunFailurePercentage|Percentagem de falhas de execução|Percentagem|Total|A percentagem de corridas de fluxo de trabalho falhou.|Nenhuma|
|Ações Iniciadas|Ações Iniciadas |Contagem|Total|O número de ações de fluxo de trabalho começou.|Nenhuma|
|AçõesConcluídas|Ações Concluídas |Contagem|Total|Número de ações de fluxo de trabalho concluídas.|Nenhuma|
|Ações Bem sucedidas|Ações bem sucedidas |Contagem|Total|O número de ações de fluxo de trabalho foi bem sucedido.|Nenhuma|
|Ações Falhadas|Ações falhadas |Contagem|Total|O número de ações de fluxo de trabalho falhou.|Nenhuma|
|Ações Ignoradas|Ações ignoradas |Contagem|Total|O número de ações de fluxo de trabalho ignoradas.|Nenhuma|
|AçãoLatency|Latência de Ação |Segundos|Média|Latência de ações completas de fluxo de trabalho.|Nenhuma|
|ActionSuccessLatency|Latência de Sucesso de Ação |Segundos|Média|Latência de ações de fluxo de trabalho bem sucedidas.|Nenhuma|
|Eventos ActionThrottled|Eventos throttled de ação|Contagem|Total|Número de ação de fluxo de trabalho acelerou eventos.|Nenhuma|
|GatilhosIniciado|Gatilhos Iniciados |Contagem|Total|O número de gatilhos de fluxo de trabalho começou.|Nenhuma|
|Gatilhos Concluídos|Gatilhos Concluídos |Contagem|Total|Número de gatilhos de fluxo de trabalho concluídos.|Nenhuma|
|Gatilhos Bem sucedidos|Gatilhos bem sucedidos |Contagem|Total|O número de gatilhos de fluxo de trabalho foi bem sucedido.|Nenhuma|
|Gatilhos Falhados|Gatilhos falhados |Contagem|Total|O número de gatilhos de fluxo de trabalho falhou.|Nenhuma|
|Gatilhos Skipped|Gatilhos ignorados|Contagem|Total|O número de gatilhos de fluxo de trabalho saltou.|Nenhuma|
|TriggersFired|Gatilhos disparados |Contagem|Total|Número de acionadores de fluxo de trabalho disparados.|Nenhuma|
|GatilhoLatency|Latência do gatilho |Segundos|Média|Latência dos gatilhos de fluxo de trabalho completos.|Nenhuma|
|TriggerFireLatency|Latência de disparo |Segundos|Média|Latência de disparados acionadores de fluxo de trabalho.|Nenhuma|
|TriggerSuccessLatency|Latência de Sucesso do Gatilho |Segundos|Média|Latência dos desencadeadores de fluxo de trabalho bem sucedidos.|Nenhuma|
|Eventos TriggerThrottled|Eventos throttled de gatilho|Contagem|Total|Número de eventos de fluxo de trabalho acelerados.|Nenhuma|
|IntegraçãoServiçoServiçoAmbienteUso deprocessadorde fluxos de trabalho|Utilização do processador workflow para o ambiente de serviço de integração|Percentagem|Média|Utilização do processador workflow para ambiente de serviço de integração.|Nenhuma|
|IntegraçãoServiçoServiçoAmbienteUso defluxodefluxo|Utilização da memória do fluxo de trabalho para o ambiente do serviço de integração|Percentagem|Média|Utilização da memória do fluxo de trabalho para o ambiente de serviço de integração.|Nenhuma|
|IntegraçãoServiçoServiçoAmbienteUso do Processador|Utilização do processador de conector para o ambiente do serviço de integração|Percentagem|Média|Utilização do processador de conector para ambiente de serviço de integração.|Nenhuma|
|IntegraçãoServiçoServiçoAmbienteConnectorMemoryUsage|Uso da memória do conector para o ambiente do serviço de integração|Percentagem|Média|Utilização da memória do conector para o ambiente de serviço de integração.|Nenhuma|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/espaços de trabalho

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Corridas Canceladas|Corridas Canceladas|Contagem|Total|Número de corridas canceladas para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Cancelar Corridas Solicitadas|Cancelar Corridas Solicitadas|Contagem|Total|Número de corridas onde o cancelamento foi solicitado para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Corridas Concluídas|Corridas Concluídas|Contagem|Total|Número de execuções concluídas com sucesso para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Corridas falhadas|Corridas falhadas|Contagem|Total|Número de corridas falhou para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Finalizando Corridas|Finalizando Corridas|Contagem|Total|Número de corridas inseridas em estado finalizador para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Não responder corre|Não responder corre|Contagem|Total|Número de corridas que não respondem para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Não começou corridas|Não começou corridas|Contagem|Total|Número de corridas em estado não iniciado para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Preparação de Corridas|Preparação de Corridas|Contagem|Total|Número de corridas que se preparam para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Corridas de Provisionamento|Corridas de Provisionamento|Contagem|Total|Número de execuções que estão a provisionar para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Corridas em fila|Corridas em fila|Contagem|Total|Número de corridas que estão na fila para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Corridas Iniciadas|Corridas Iniciadas|Contagem|Total|Número de corridas iniciadas para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Iniciar corridas|Iniciar corridas|Contagem|Total|Número de corridas iniciadas para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Erros|Erros|Contagem|Total|Número de erros de execução neste espaço de trabalho|Cenário|
|Avisos|Avisos|Contagem|Total|Número de avisos de execução neste espaço de trabalho|Cenário|
|Registo de Modelos Bem Sucedido|Registo de Modelos Bem Sucedido|Contagem|Total|Número de registos de modelos que conseguiram neste espaço de trabalho|Cenário|
|Registo de Modelos Falhado|Registo de Modelos Falhado|Contagem|Total|Número de registos de modelos que falharam neste espaço de trabalho|Cenário,Código de Estado|
|Modelo Implantação Iniciada|Modelo Implantação Iniciada|Contagem|Total|Número de implementações de modelos iniciadas neste espaço de trabalho|Cenário|
|Model Deploy Succeeded|Model Deploy Succeeded|Contagem|Total|Número de implementações de modelos que conseguiram neste espaço de trabalho|Cenário|
|Implementação do modelo falhou|Implementação do modelo falhou|Contagem|Total|Número de implementações de modelos que falharam neste espaço de trabalho|Cenário,Código de Estado|
|Nósodes totais|Nósodes totais|Contagem|Média|Número de nós totais. Este total inclui alguns de Nós Ativos, Nós ociosos, nós inutilizáveis, nós pré-mepted, deixando nós|Cenário,ClusterName|
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


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/contas

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Utilização|Utilização|Contagem|Contagem|Contagem de chamadas da API|Categoria Api,ApiName,Tipo de Resultados,Código de Resposta|
|Disponibilidade|Disponibilidade|Percentagem|Média|Disponibilidade das APIs|Categoria Api,ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Saída|Saída|Bytes|Total|A quantidade de dados da Egress, em bytes.|Formato de saída|
|SuccessE2ELatency|Fim do sucesso para acabar com a Latência|Milissegundos|Média|A latência média para pedidos bem sucedidos em milissegundos.|Formato de saída|
|Pedidos|Pedidos|Contagem|Total|Pedidos para um Ponto Final de Streaming.|Formato de saída,HttpStatusCode,ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AssetQuota|Quota de ativos|Contagem|Média|Quantos ativos são permitidos para a conta de serviço de mídia atual|Nenhuma|
|Contaque de ativos|Contagem de ativos|Contagem|Média|Quantos ativos já estão criados na conta de serviço de mídia atual|Nenhuma|
|Percentagem de Quotas de Ativos|Percentagem de quota de ativos utilizada|Percentagem|Média|Percentagem de ativos utilizada na conta de serviço de mídia corrente|Nenhuma|
|ContentKeyPolicyQuota|Quota de Política chave de conteúdo|Contagem|Média|Quantos agentes chave de conteúdo são permitidos para a conta de serviço de mídia atual|Nenhuma|
|Contagem de políticas de conteúdo|Contagem de políticas de chave de conteúdo|Contagem|Média|Quantas políticas chave de conteúdo já estão criadas na conta de serviço de mídia atual|Nenhuma|
|ContentKeyPolicyQuotaUsedPercentage|Percentagem de política-chave de conteúdo utilizada|Percentagem|Média|Política chave de conteúdo utilizada percentagem na conta de serviço de mídia corrente|Nenhuma|
|StreamingPolicyQuota|Quota política de streaming|Contagem|Média|Quantas políticas de streaming são permitidas para a conta de serviço de mídia atual|Nenhuma|
|Contagem de Políticas de Streaming|Contagem de políticas de streaming|Contagem|Média|Quantas políticas de streaming já estão criadas na conta de serviço sinuoso atual|Nenhuma|
|StreamingPolicyQuotaUsedPercentage|Quota de política de streaming utilizada percentagem|Percentagem|Média|Política de Streaming utilizada percentagem na conta de serviço de mídia atual|Nenhuma|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft.MixedReality/remoteRenderingAccounts

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ActivosConvertidos|Ativos Convertidos|Contagem|Total|Número total de ativos convertidos|Appid,Resourceid,SDKversion|
|ActiveRenderingSessions|Sessões ativas de renderização|Contagem|Total|Número total de sessões de renderização ativa|Appid,Resourceid,SessionType,SDKversion|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|MédiaDeLeitura|Latência média de leitura|MilliSeconds|Média|Latência média de leitura em milissegundos por operação|Nenhuma|
|MédiaWriteLatency|Latência média de escrita|MilliSeconds|Média|Latência média de escrita em milissegundos por operação|Nenhuma|
|Volumelógico|Volume Consumido Tamanho|Bytes|Média|Tamanho lógico do volume (bytes usados)|Nenhuma|
|VolumeSnapshotSize|Tamanho do instantâneo de volume|Bytes|Média|Tamanho de todos os instantâneos em volume|Nenhuma|
|Leituras|Ler iops|CondeEmSegundo|Média|Ler operações de in/fora por segundo|Nenhuma|
|WriteIops|Escrever iops|CondeEmSegundo|Média|Escreva operações in/out por segundo|Nenhuma|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Piscina atribuída ao tamanho do volume|Bytes|Média|Tamanho usado atribuído da piscina|Nenhuma|
|VolumePoolTotalLogicalSize|Piscina Consumida Tamanho|Bytes|Média|Soma do tamanho lógico de todos os volumes pertencentes à piscina|Nenhuma|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BytesSentRate|Bytes Enviados|Bytes|Total|Número de bytes a Interface de Rede enviada|Nenhuma|
|BytesReceivedRate|Bytes Recebidos|Bytes|Total|Número de bytes que a Interface de Rede recebeu|Nenhuma|
|PacotesSentRate|Pacotes Enviados|Contagem|Total|Número de pacotes que a Interface de Rede enviou|Nenhuma|
|PacotesReceivedRate|Pacotes Recebidos|Contagem|Total|Número de pacotes que a Interface de Rede recebeu|Nenhuma|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Disponibilidade Vip|Disponibilidade do Caminho de Dados|Contagem|Média|Disponibilidade de dados de balanceadores de carga média por duração do tempo|FrontendIPAddress,FrontendPort|
|Disponibilidade de Mergulho|Estado da sonda de saúde|Contagem|Média|Estado médio da sonda de saúde balanceadores de carga por duração do tempo|ProtocolType,BackendPort,FrontendIPAddress,FrontendPort,BackendIPAddress|
|ByteCount|Conde byte|Contagem|Total|Número total de Bytes transmitidos dentro do período de tempo|FrontendIPAddress,FrontendPort,Direção|
|Contagem de pacotes|Contagem de pacotes|Contagem|Total|Número total de pacotes transmitidos dentro do período de tempo|FrontendIPAddress,FrontendPort,Direção|
|SYNCount|Contagem de SYN|Contagem|Total|Número total de pacotes syn transmitidos dentro do período de tempo|FrontendIPAddress,FrontendPort,Direção|
|SnatConnectionCount|Contagem de ligação sNAT|Contagem|Total|Número total de novas ligações SNAT criadas dentro do período de tempo|FrontendIPAddress,BackendIPAddress,ConnectionState|
|AlocadoSnatPorts|Portas SNAT atribuídas (Pré-visualização)|Contagem|Total|Número total de portas SNAT atribuídas dentro do prazo|FrontendIPAddress,BackendIPAddress,ProtocolType,IsAwaitingRemoval|
|SnatPorts Usados|Portas SNAT usadas (Pré-visualização)|Contagem|Total|Número total de portas SNAT utilizadas dentro do período de tempo|FrontendIPAddress,BackendIPAddress,ProtocolType,IsAwaitingRemoval|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Volume de Consultas|Volume de consulta|Contagem|Total|Número de consultas servidas para uma zona de DNS|Nenhuma|
|RecordSetCount|Contagem de conjuntos de discos|Contagem|Máximo|Número de conjuntos de recordes numa zona de DNS|Nenhuma|
|Utilização de RecordSetCapacity|Utilização da capacidade do conjunto de registos|Percentagem|Máximo|Por cento da capacidade do Record set utilizada por uma zona DNS|Nenhuma|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresss

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PacotesInDDoS|Pacotes de entrada DDoS|CondeEmSegundo|Máximo|Pacotes de entrada DDoS|Nenhuma|
|Pacotes DropDDoS|Pacotes de entrada caíram DDoS|CondeEmSegundo|Máximo|Pacotes de entrada caíram DDoS|Nenhuma|
|Pacotes ForwardedDDoS|Pacotes de entrada encaminhadas DDoS|CondeEmSegundo|Máximo|Pacotes de entrada encaminhadas DDoS|Nenhuma|
|TCPPacketsInDDoS|Pacotes TCP de entrada DDoS|CondeEmSegundo|Máximo|Pacotes TCP de entrada DDoS|Nenhuma|
|TCPPacketsDropDDoS|Pacotes tCP de entrada caíram DDoS|CondeEmSegundo|Máximo|Pacotes tCP de entrada caíram DDoS|Nenhuma|
|TCPPacketsForwardedDDoS|Pacotes TCP de entrada encaminharam DDoS|CondeEmSegundo|Máximo|Pacotes TCP de entrada encaminharam DDoS|Nenhuma|
|UDPPacketsInDDoS|Pacotes UDP de entrada DDoS|CondeEmSegundo|Máximo|Pacotes UDP de entrada DDoS|Nenhuma|
|UDPPacketsDropDDoS|Pacotes uDP de entrada caíram DDoS|CondeEmSegundo|Máximo|Pacotes uDP de entrada caíram DDoS|Nenhuma|
|UDPPacketsForwardedDDoS|Pacotes UDP de entrada encaminharam DDoS|CondeEmSegundo|Máximo|Pacotes UDP de entrada encaminharam DDoS|Nenhuma|
|BytesInDDoS|Bytes de entrada|BytesPerSecond|Máximo|Bytes de entrada|Nenhuma|
|BytesDropDDoS|Bytes de entrada caíram DDoS|BytesPerSecond|Máximo|Bytes de entrada caíram DDoS|Nenhuma|
|BytesForwardedDDoS|Bytes de entrada encaminhadas DDoS|BytesPerSecond|Máximo|Bytes de entrada encaminhadas DDoS|Nenhuma|
|TCPBytesInDDoS|TCP de entrada bytes DDoS|BytesPerSecond|Máximo|TCP de entrada bytes DDoS|Nenhuma|
|TCPBytesDropDDoS|Bytes de TCP de entrada caíram DDoS|BytesPerSecond|Máximo|Bytes de TCP de entrada caíram DDoS|Nenhuma|
|TCPBytesForwardedDDoS|Bytes de TCP de entrada encaminhadas DDoS|BytesPerSecond|Máximo|Bytes de TCP de entrada encaminhadas DDoS|Nenhuma|
|UDPBytesInDDoS|UDP de entrada bytes DDoS|BytesPerSecond|Máximo|UDP de entrada bytes DDoS|Nenhuma|
|UDPBytesDropDDoS|Bytes uDP de entrada caíram DDoS|BytesPerSecond|Máximo|Bytes uDP de entrada caíram DDoS|Nenhuma|
|UDPBytesForwardedDDoS|Bytes UDP de entrada|BytesPerSecond|Máximo|Bytes UDP de entrada|Nenhuma|
|Ataque ifunderddos|Sob ataque DDoS ou não|Contagem|Máximo|Sob ataque DDoS ou não|Nenhuma|
|DDoSTriggerTCPPackets|Pacotes TCP de entrada para desencadear mitigação do DDoS|CondeEmSegundo|Máximo|Pacotes TCP de entrada para desencadear mitigação do DDoS|Nenhuma|
|DDoSTriggerUDPPackets|Pacotes UDP de entrada para desencadear mitigação do DDoS|CondeEmSegundo|Máximo|Pacotes UDP de entrada para desencadear mitigação do DDoS|Nenhuma|
|DDoSTriggerSYNPackets|Pacotes SYN de entrada para desencadear mitigação do DDoS|CondeEmSegundo|Máximo|Pacotes SYN de entrada para desencadear mitigação do DDoS|Nenhuma|
|Disponibilidade Vip|Disponibilidade do Caminho de Dados|Contagem|Média|Disponibilidade média de endereço IP por duração do tempo|Porta|
|ByteCount|Conde byte|Contagem|Total|Número total de Bytes transmitidos dentro do período de tempo|Porto,Direção|
|Contagem de pacotes|Contagem de pacotes|Contagem|Total|Número total de pacotes transmitidos dentro do período de tempo|Porto,Direção|
|SynCount|Contagem de SYN|Contagem|Total|Número total de pacotes syn transmitidos dentro do período de tempo|Porto,Direção|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Tempo de ida e volta para Pings a um VM|MilliSeconds|Média|Tempo de ida e volta para Pings enviado para um destino VM|Endereço sourceCustomer,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Pings falhados a um VM|Percentagem|Média|Por cento do número de Pings falhados no total enviado pings de um destino VM|Endereço sourceCustomer,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ApplicationRuleHit|Regras de aplicação atingem a contagem|Contagem|Total|Número de vezes que as regras de aplicação foram atingidas|Estado,Razão,Protocolo|
|NetworkRuleHit|Regras da rede atingir a contagem|Contagem|Total|Número de vezes que as regras da rede foram atingidas|Estado,Razão,Protocolo|
|FirewallHealth|Estado de saúde firewall|Percentagem|Média|Estado de saúde firewall|Estado,Razão|
|DataProcessado|Dados tratados|Bytes|Total|Quantidade total de dados processados pela Firewall|Nenhuma|
|SNATPortUtilização|Utilização da porta SNAT|Percentagem|Média|Utilização da porta SNAT|Nenhuma|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Débito|Débito|BytesPerSecond|Média|Número de bytes por segundo o Gateway de Aplicação serviu|Nenhuma|
|Contagem de anfitriões pouco saudável|Contagem de hospedeiros pouco saudável|Contagem|Média|Número de anfitriões insalubres|BackendSettingsPool|
|Contagem de anfitriões saudáveis|Contagem de hospedeiros saudáveis|Contagem|Média|Número de anfitriões saudáveis de backend|BackendSettingsPool|
|TotalRequests|Total de Pedidos|Contagem|Total|Contagem de pedidos bem sucedidos que o Application Gateway serviu|BackendSettingsPool|
|AvgRequestCountPerHealthyHostHost|Pedidos por minuto por Anfitrião Saudável|Contagem|Média|Contagem média de pedidos por minuto por hospedeiro saudável em uma piscina|BackendSettingsPool|
|Pedidos Falhados|Pedidos com Falhas|Contagem|Total|Contagem de pedidos falhados que o Application Gateway serviu|BackendSettingsPool|
|Estatuto de Resposta|Estado de Resposta|Contagem|Total|Estado de resposta http devolvido por Application Gateway|HttpStatusGroup|
|CurrentConnections|Conexões Atuais|Contagem|Total|Contagem das ligações atuais estabelecidas com gateway de aplicação|Nenhuma|
|NewConnectionsPerSecond|Novas ligações por segundo|CondeEmSegundo|Média|Novas ligações por segundo estabelecidas com Application Gateway|Nenhuma|
|CpuUtilização|Utilização da CPU|Percentagem|Média|Utilização atual do CPU do Gateway de Aplicação|Nenhuma|
|Unidades de Capacidade|Unidades de Capacidade Atuais|Contagem|Média|Unidades de Capacidade consumidas|Nenhuma|
|FixedBillableCapacityUnits|Unidades de Capacidade Faturadas Fixas|Contagem|Média|Unidades de capacidade mínima que serão carregadas|Nenhuma|
|Unidades estimadas de Capacidade faturada|Unidades de Capacidade Faturadas Estimadas|Contagem|Média|Unidades de capacidade estimadas que serão carregadas|Nenhuma|
|ComputeUnits|Unidades Computadas Atuais|Contagem|Média|Unidades Computadas consumidas|Nenhuma|
|BackendResponseStatus|Estado de resposta de backend|Contagem|Total|O número de códigos de resposta HTTP gerados pelos membros do backend. Isto não inclui quaisquer códigos de resposta gerados pelo Gateway da Aplicação.|BackendServer,BackendPool,BackendHttpSetting,HttpStatusGroup|
|Protocolo TLS|Protocolo TLS cliente|Contagem|Total|O número de pedidos tLS e não-TLS iniciados pelo cliente que estabeleceu a ligação com o Gateway de Aplicação. Para ver a distribuição do protocolo TLS, filtre pela dimensão do Protocolo TLS.|Ouvinte,TlsProtocol|
|BytesSent|Bytes Enviados|Bytes|Total|O número total de bytes enviados pela Porta de Entrada de Aplicação aos clientes|Serviço de Escuta|
|BytesReceived|Bytes Recebidos|Bytes|Total|O número total de bytes recebidos pelo Gateway de Aplicação dos clientes|Serviço de Escuta|
|ClientRtt|Cliente RTT|MilliSeconds|Média|Tempo médio de ida e volta entre clientes e Gateway de aplicação. Esta métrica indica quanto tempo leva para estabelecer ligações e reconhecer|Serviço de Escuta|
|AplicaçãoGatewayTotalTime|Tempo total do Gateway de Aplicação|MilliSeconds|Média|O tempo médio que leva para que um pedido seja processado e a sua resposta seja enviada. Isto é calculado em média do intervalo a partir do momento em que o Application Gateway recebe o primeiro byte de um pedido HTTP para o momento em que a resposta envia a operação termina. É importante notar que isto geralmente inclui o tempo de processamento do Gateway de aplicação, o tempo que os pacotes de pedido e resposta estão viajando sobre a rede e o tempo que o servidor backend levou para responder.|Serviço de Escuta|
|BackendConnectTime|Tempo de ligação backend|MilliSeconds|Média|Tempo gasto estabelecendo uma ligação com um servidor de backend|Ouvinte,BackendServer,BackendPool,BackendHttpSetting|
|BackendFirstByteResponseTime|Apoiar o primeiro tempo de resposta do byte|MilliSeconds|Média|Intervalo de tempo entre o início do estabelecimento de uma ligação ao servidor de backend e receber o primeiro byte do cabeçalho de resposta, aproximando o tempo de processamento do servidor backend|Ouvinte,BackendServer,BackendPool,BackendHttpSetting|
|BackendLastByteResponseTime|Backend Last Byte Response Time|MilliSeconds|Média|Intervalo de tempo entre o início do estabelecimento de uma ligação ao servidor de backend e receber o último byte do corpo de resposta|Ouvinte,BackendServer,BackendPool,BackendHttpSetting|
|Contagem compatível|Distribuição total de regras de firewall v1|Contagem|Total|Web Application Firewall v1 Distribuição total de regras para o tráfego de entrada|RuleGroup,RuleId|
|Contagem bloqueada|Web Application Firewall v1 Blocked Requests Rule Distribution|Contagem|Total|Web Application Firewall v1 bloqueado solicita a distribuição de regras|RuleGroup,RuleId|
|BlockedReqCount|Contagem de pedidos bloqueados firewall v1 bloqueados|Contagem|Total|Contagem de pedidos bloqueados firewall v1|Nenhuma|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Largura média de banda|Gateway S2S Largura de banda|BytesPerSecond|Média|Largura de banda de site-a-local média de um gateway em bytes por segundo|Nenhuma|
|Largura de banda p2S|Largura de banda Gateway P2S|BytesPerSecond|Média|Largura de banda ponto-a-local média de um gateway em bytes por segundo|Nenhuma|
|Contagem de Conexões P2S|Contagem de ligação P2S|Contagem|Máximo|Contagem de ligação ponto-a-local de um portal|Protocolo|
|TunnelAverageBandwidth|Largura de banda do túnel|BytesPerSecond|Média|Largura de banda média de um túnel em bytes por segundo|Nome de ligação,RemoteIP|
|TunnelEgressBytes|Túnel Egress Bytes|Bytes|Total|Bytes de saída de um túnel|Nome de ligação,RemoteIP|
|TunnelIngressBytes|Bytes de Ingresso do Túnel|Bytes|Total|Bytes de entrada de um túnel|Nome de ligação,RemoteIP|
|TunnelEgressPackets|Pacotes de Egress do Túnel|Contagem|Total|Contagem de pacotes de saída de um túnel|Nome de ligação,RemoteIP|
|TunnelIngressPackets|Pacotes de ingressos de túnel|Contagem|Total|Contagem de pacotes de entrada de um túnel|Nome de ligação,RemoteIP|
|TunnelEgressPacketDropTSMismatch|Túnel Egress TS Mismatch Packet Drop|Contagem|Total|Contagem de gotas de pacote de saída de um desvio de seletor de tráfego de um túnel|Nome de ligação,RemoteIP|
|Tunnelingresspacketdroptsmismatch|Túnel Ingress TS Mismatch Packet Drop|Contagem|Total|Contagem de gotas de pacote de entrada de um túnel de seletor de tráfego|Nome de ligação,RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Nível RxLight|Nível RxLight|Contagem|Média|Nível de luz Rx em dBm|Link,Pista|
|TxLightLevel|TxLightLevel|Contagem|Média|Nível de luz Tx em dBm|Link,Pista|
|Estado-de-administração|Estado-de-administração|Contagem|Média|Estado administrativo do porto|Ligação|
|Protocolo de Linha|Protocolo de Linha|Contagem|Média|Estatuto do protocolo de linha da porta|Ligação|
|PortBitsInPerSecond|BitsInPerSecond|CondeEmSegundo|Média|Bits ingressando Azure por segundo|Ligação|
|PortBitsOutPerSecond|BitsOutPerSecond|CondeEmSegundo|Média|Bits a esganar Azure por segundo|Ligação|



## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CondeEmSegundo|Média|Bits ingressando Azure por segundo|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CondeEmSegundo|Média|Bits a esganar Azure por segundo|PeeringType|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|CondeEmSegundo|Média|Bits ingressando Azure por segundo|PeeredCircuitsKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|CondeEmSegundo|Média|Bits a esganar Azure por segundo|PeeredCircuitsKey|
|BGPDisponibilidade|Disponibilidade de BGP|Percentagem|Média|BGP Disponibilidade do MSEE para todos os pares.|PeeringType,Peer|
|Disponibilidade de Arp|Disponibilidade arp|Percentagem|Média|Disponibilidade arp do MSEE para todos os pares.|PeeringType,Peer|
|QosDropBitsInPerSecond|DropInBitsPerSecond|CondeEmSegundo|Média|Ingressos de dados caíram por segundo|Nenhuma|
|QosDropBitsOutPerSecond|DropOutBitsPerSecond|CondeEmSegundo|Média|Egress bits de dados caiu por segundo|Nenhuma|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CondeEmSegundo|Média|Bits ingressando Azure por segundo|Nenhuma|
|BitsOutPerSecond|BitsOutPerSecond|CondeEmSegundo|Média|Bits a esganar Azure por segundo|Nenhuma|

## <a name="microsoftnetworkconnections"></a>Microsoft.Rede/ligações

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CondeEmSegundo|Média|Bits ingressando Azure por segundo|Nenhuma|
|BitsOutPerSecond|BitsOutPerSecond|CondeEmSegundo|Média|Bits a esganar Azure por segundo|Nenhuma|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|CondeEmSegundo|Média|Bits ingressando Azure por segundo|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|CondeEmSegundo|Média|Bits a esganar Azure por segundo|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QpsByEndpoint|Consultas por Endpoint Devolvidos|Contagem|Total|Número de vezes que um ponto final do Gestor de Tráfego foi devolvido no prazo dado|Nome final|
|ProbeAgentCurrentCurrentEndpointStateByProfileResourceId|Estado do ponto final por ponto final|Contagem|Máximo|1 se o estado da sonda de um ponto final for "Ativado", 0 caso contrário.|Nome final|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SondasFailedPercent|% sondas falhadas|Percentagem|Média|% das sondas de monitorização da conectividade falharam|Nenhuma|
|AverageRoundtripMs|Avg. Tempo de ida e volta (ms)|MilliSeconds|Média|Tempo médio de ida e volta da rede (MS) para sondas de monitorização de conectividade enviadas entre fonte e destino|Nenhuma|
|ChequesFailedPercent|Cheques Por cento falhado (Pré-visualização)|Percentagem|Média|% dos controlos de controlo da conectividade falharam|Endereço fonte,Nome de origem,SourceResourceID,SourceType,Protocol,DestinationAddress,DestinationName,DestinationResourceid,DestinationType,Destinationport,TestGroupName,TestConfigurationName|
|RoundTripTimeMs|Tempo de ida e volta (ms) (Pré-visualização)|MilliSeconds|Média|Tempo de ida e volta em milissegundos para os controlos de monitorização da conectividade|Endereço fonte,Nome de origem,SourceResourceID,SourceType,Protocol,DestinationAddress,DestinationName,DestinationResourceid,DestinationType,Destinationport,TestGroupName,TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Contagem de Pedidos|Número de Pedidos|Contagem|Total|O número de pedidos de clientes servidos pelo representante http/S|httpstatus,httpstatusGroup,ClientRegion,ClientCountry|
|Tamanho de pedido|Tamanho do pedido|Bytes|Total|O número de bytes enviados como pedidos de clientes para o proxy HTTP/S|httpstatus,httpstatusGroup,ClientRegion,ClientCountry|
|Tamanho de resposta|Tamanho da resposta|Bytes|Total|O número de bytes enviados como respostas de http/S proxy para clientes|httpstatus,httpstatusGroup,ClientRegion,ClientCountry|
|BillableResponseSize|Tamanho da resposta facturavel|Bytes|Total|O número de bytes faturados (mínimo 2KB por pedido) enviados como respostas de procuração HTTP/S aos clientes.|httpstatus,httpstatusGroup,ClientRegion,ClientCountry|
|Contagem de pedidos de backend|Contagem de pedidos de backend|Contagem|Total|O número de pedidos enviados do representante http/S para backends|HttpStatus,HttpStatusGroup,Backend|
|AtrasodePedido|Latência de Pedido de Backend|MilliSeconds|Média|O tempo calculado a partir do momento em que o pedido foi enviado pelo representante http/S para o backend até que o representante http/s recebeu o último byte de resposta do backend|Back-end|
|TotalLatency|Latência Total|MilliSeconds|Média|O tempo calculado a partir do momento em que o pedido do cliente foi recebido pelo representante http/S até que o cliente reconheceu o último byte de resposta do representante HTTP/S|httpstatus,httpstatusGroup,ClientRegion,ClientCountry|
|BackendHealthPercentage|Percentagem de Saúde Endend|Percentagem|Média|A percentagem de sondas de saúde bem sucedidas do proxy HTTP/S para backends|Backend,BackendPool|
|Contagem de pedidos de firewall da web|Contagem de pedidos de firewall de aplicação web|Contagem|Total|O número de pedidos de clientes processados pela Firewall de Aplicação Web|Nome de política,Nome de Regras,Ação|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft.Network/privateDnsZones

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Volume de Consultas|Volume de consulta|Contagem|Total|Número de consultas servidas para uma zona privada de DNS|Nenhuma|
|RecordSetCount|Contagem de conjuntos de discos|Contagem|Máximo|Número de conjuntos de recordes numa zona privada de DNS|Nenhuma|
|Utilização de RecordSetCapacity|Utilização da capacidade do conjunto de registos|Percentagem|Máximo|Por cento da capacidade do Record set utilizada por uma zona Privada de DNS|Nenhuma|
|Contagem de ligações virtual|Contagem de ligações de rede virtual|Contagem|Máximo|Número de Redes Virtuais ligadas a uma zona Privada de DNS|Nenhuma|
|VirtualNetworkLinkCapacityUtilização|Utilização da capacidade de ligação de rede virtual|Percentagem|Máximo|Por cento da capacidade de Ligação de Rede Virtual utilizada por uma zona Privada de DNS|Nenhuma|
|VirtualNetworkWithRegistrationLinkCount|Contagem de ligações de registo de rede virtual|Contagem|Máximo|Número de Redes Virtuais ligadas a uma zona Privada de DNS com registo automático ativado|Nenhuma|
|VirtualNetworkWithRegistrationCapacityUtilização|Utilização da capacidade de ligação de registo de rede virtual|Percentagem|Máximo|Por cento da Ligação de Rede Virtual com capacidade de registo automático utilizada por uma zona Privada de DNS|Nenhuma|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|registo.todos|Operações de Registo|Contagem|Total|A contagem de todas as operações de registo bem sucedidas (criações atualiza consultas e supressões). |Nenhuma|
|registo.criar|Inscrições Criar Operações|Contagem|Total|A contagem de todas as criações de registo bem sucedidas.|Nenhuma|
|registration.update|Operações de Atualização de Registo|Contagem|Total|A contagem de todas as atualizações de registo bem sucedidas.|Nenhuma|
|registro.get|Operações de Leitura de Registo|Contagem|Total|A contagem de todas as consultas de registo bem sucedidas.|Nenhuma|
|registo.excluir|Inscrições Supressão de Operações|Contagem|Total|A contagem de todas as supressões de registo bem sucedidas.|Nenhuma|
|entrada|Mensagens de entrada|Contagem|Total|A contagem de todos os bem sucedidos enviar chamadas da API. |Nenhuma|
|entrada.agendado|Notificações push agendadas enviadas|Contagem|Total|Notificações de Push agendadas canceladas|Nenhuma|
|entrada.agendado.cancelar|Notificações de Push agendadas canceladas|Contagem|Total|Notificações de Push agendadas canceladas|Nenhuma|
|agendado.pendente|Notificações agendadas pendentes|Contagem|Total|Notificações agendadas pendentes|Nenhuma|
|instalação.todos|Operações de Gestão de Instalações|Contagem|Total|Operações de Gestão de Instalações|Nenhuma|
|instalação.get|Obter Operações de Instalação|Contagem|Total|Obter Operações de Instalação|Nenhuma|
|instalação.upsert|Criar ou atualizar operações de instalação|Contagem|Total|Criar ou atualizar operações de instalação|Nenhuma|
|instalação.patch|Operações de instalação de patch|Contagem|Total|Operações de instalação de patch|Nenhuma|
|instalação.excluir|Excluir operações de instalação|Contagem|Total|Excluir operações de instalação|Nenhuma|
|outgoing.allpns.success|Notificações bem sucedidas|Contagem|Total|A contagem de todas as notificações bem sucedidas.|Nenhuma|
|outgoing.allpns.invalidpayload|Erros de carga útil|Contagem|Total|A contagem de empurrões que falhou porque o PNS devolveu um erro de carga útil.|Nenhuma|
|outgoing.allpns.pnserror|Erros do sistema de notificação externa|Contagem|Total|A contagem de empurrões que falharam porque havia um problema de comunicação com o PNS (exclui problemas de autenticação).|Nenhuma|
|outgoing.allpns.channelerror|Erros do Canal|Contagem|Total|A contagem de empurrões que falharam porque o canal não estava inválido não estava associado à aplicação correta acelerada ou expirada.|Nenhuma|
|outgoing.allpns.badorexpiredchannel|Erros no canal mau ou expirado|Contagem|Total|A contagem de empurrões que falharam porque o canal/token/registrationId no registo foi expirado ou inválido.|Nenhuma|
|outgoing.wns.success|Notificações bem sucedidas da WNS|Contagem|Total|A contagem de todas as notificações bem sucedidas.|Nenhuma|
|outgoing.wns.invalidcredentials|Erros de autorização wns (credenciais inválidas)|Contagem|Total|A contagem de empurrões que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas. (O Windows Live não reconhece as credenciais).|Nenhuma|
|outgoing.wns.badchannel|Erro do canal errado wns|Contagem|Total|A contagem de empurrões que falharam porque o ChannelURI no registo não foi reconhecido (estatuto WNS: 404 não encontrados).|Nenhuma|
|outgoing.wns.expiredchannel|Erro do canal expirado wns|Contagem|Total|A contagem de empurrões que falharam porque o ChannelURI expirou (estatuto WNS: 410 Gone).|Nenhuma|
|outgoing.wns.throttled|Notificações wns throttled|Contagem|Total|A contagem de empurrões que falharam porque a WNS está a estrangular esta aplicação (estatuto WNS: 406 Not Acceptable).|Nenhuma|
|outgoing.wns.tokenproviderunreachable|Erros de autorização wns (incontactáveis)|Contagem|Total|O Windows Live não é acessível.|Nenhuma|
|outgoing.wns.invalidtoken|Erros de autorização wns (ficha inválida)|Contagem|Total|O símbolo fornecido à WNS não é válido (estatuto WNS: 401 Não autorizado).|Nenhuma|
|outgoing.wns.wrongtoken|Erros de autorização wns (ficha errada)|Contagem|Total|O símbolo fornecido à WNS é válido, mas para outro pedido (estatuto WNS: 403 Proibido). Isto pode acontecer se o ChannelURI no registo estiver associado a outra aplicação. Verifique se a aplicação do cliente está associada à mesma aplicação cujas credenciais estão no centro de notificação.|Nenhuma|
|outgoing.wns.invalidnotificationforma|Formato de notificação inválido wns|Contagem|Total|O formato da notificação é inválido (estatuto WNS: 400). Note que a WNS não rejeita todas as cargas inválidas.|Nenhuma|
|outgoing.wns.invalidnotificationsize|Erro de tamanho de notificação inválido wns|Contagem|Total|A carga útil da notificação é demasiado grande (estado WNS: 413).|Nenhuma|
|outgoing.wns.channelthrottled|Canal WNS Throttled|Contagem|Total|A notificação foi retirada porque o ChannelURI no registo é estrangulado (cabeçalho de resposta WNS: X-WNS-NotificationStatus:channelThrottled).|Nenhuma|
|outgoing.wns.channeldesligado|Canal WNS desligado|Contagem|Total|A notificação foi retirada porque o ChannelURI no registo está estrangulado (cabeçalho de resposta WNS: X-WNS-DeviceConnectionStatus: desligado).|Nenhuma|
|outgoing.wns.drop|WNS Deixou notificações|Contagem|Total|A notificação foi retirada porque o ChannelURI no registo está estrangulado (X-WNS-NotificationStatus: retirado, mas não X-WNS-DeviceConnectionStatus: desligado).|Nenhuma|
|outgoing.wns.pnserror|Erros wns|Contagem|Total|Notificação não entregue devido a erros de comunicação com a WNS.|Nenhuma|
|outgoing.wns.authenticationerror|Erros de autenticação wns|Contagem|Total|Notificação não entregue devido a erros de comunicação com credenciais inválidas do Windows Live ou ficha errada.|Nenhuma|
|outgoing.apns.success|Notificações bem sucedidas da APNS|Contagem|Total|A contagem de todas as notificações bem sucedidas.|Nenhuma|
|outgoing.apns.invalidcredentials|Erros de autorização da APNS|Contagem|Total|A contagem de empurrões que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhuma|
|outgoing.apns.badchannel|Erro do canal bad da APNS|Contagem|Total|A contagem de empurrões que falharam porque o token é inválido (código de estado APNS: 8).|Nenhuma|
|outgoing.apns.expiredchannel|Erro do canal expirado APNS|Contagem|Total|A contagem de fichas que foram invalidadas pelo canal de feedback da APNS.|Nenhuma|
|outgoing.apns.invalidnotificationsize|Erro de tamanho de notificação inválido da APNS|Contagem|Total|A contagem de empurrões que falharam porque a carga útil era demasiado grande (código de estado APNS: 7).|Nenhuma|
|outgoing.apns.pnserror|Erros APNS|Contagem|Total|A contagem de empurrões que falharam devido a erros de comunicação com APNS.|Nenhuma|
|outgoing.gcm.success|Notificações bem sucedidas da GCM|Contagem|Total|A contagem de todas as notificações bem sucedidas.|Nenhuma|
|outgoing.gcm.invalidcredentials|Erros de autorização GCM (Credenciais inválidas)|Contagem|Total|A contagem de empurrões que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhuma|
|outgoing.gcm.badchannel|Erro do canal errado da GCM|Contagem|Total|A contagem de empurrões que falharam porque o registoId no registo não foi reconhecido (resultado GCM: Registo Inválido).|Nenhuma|
|outgoing.gcm.expiredchannel|Erro do canal expirado GCM|Contagem|Total|A contagem de empurrões que falharam porque o registoId no registo expirou (resultado GCM: NotRegistered).|Nenhuma|
|outgoing.gcm.throttled|Notificações de aceleração GCM|Contagem|Total|A contagem de empurrões que falharam porque o GCM estrangulou esta aplicação (código de estado GCM: 501-599 ou resultado:Indisponível).|Nenhuma|
|formato outgoing.gcm.invalidnotification|Formato de notificação inválida GCM|Contagem|Total|A contagem de empurrões que falharam porque a carga não foi formatada corretamente (resultado GCM: InvalidDataKey ou InvalidTTl).|Nenhuma|
|outgoing.gcm.invalidnotificationsize|Erro de tamanho de notificação inválido gcm|Contagem|Total|A contagem de empurrões que falharam porque a carga útil era demasiado grande (resultado gcm: MessageTooBig).|Nenhuma|
|outgoing.gcm.wrongchannel|Erro do canal errado GCM|Contagem|Total|A contagem de empurrões que falharam porque o registoId no registo não está associado à aplicação atual (resultado GCM: InvalidPackageName).|Nenhuma|
|outgoing.gcm.pnserror|Erros GCM|Contagem|Total|A contagem de empurrões que falharam devido a erros de comunicação com a GCM.|Nenhuma|
|outgoing.gcm.authenticationerror|Erros de autenticação GCM|Contagem|Total|A contagem de empurrões que falharam porque o PNS não aceitou as credenciais fornecidas as credenciais são bloqueadas ou o SenderId não está corretamente configurado na aplicação (resultado GCM: MismatchedSenderId).|Nenhuma|
|outgoing.mpns.success|Notificações bem sucedidas do MPNS|Contagem|Total|A contagem de todas as notificações bem sucedidas.|Nenhuma|
|outgoing.mpns.invalidcredenciais|Credenciais de mpns inválidas|Contagem|Total|A contagem de empurrões que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhuma|
|outgoing.mpns.badchannel|Erro do Canal Mau mpns|Contagem|Total|A contagem de empurrões que falharam porque o ChannelURI no registo não foi reconhecido (estatuto DE MPNS: 404 não encontrados).|Nenhuma|
|outgoing.mpns.throttled|Notificações de aceleração do MPNS|Contagem|Total|A contagem de empurrões que falharam porque o MPNS está a estrangular esta aplicação (WNS MPNS: 406 Not Acceptable).|Nenhuma|
|outgoing.mpns.invalidnotificationforma|Formato de notificação inválido do MPNS|Contagem|Total|A contagem de empurrões que falhou porque a carga útil da notificação era demasiado grande.|Nenhuma|
|outgoing.mpns.channeldesligado|Canal MPNS desligado|Contagem|Total|A contagem de empurrões que falharam porque o ChannelURI no registo foi desligado (estatuto DE MPNS: 412 não encontrados).|Nenhuma|
|outgoing.mpns.drop|MpNS Notificações retiradas|Contagem|Total|A contagem de empurrões que foram retiradas por MPNS (cabeçalho de resposta MPNS: X-NotificationStatus: QueueFull ou Suprimido).|Nenhuma|
|outgoing.mpns.pnserror|Erros mpns|Contagem|Total|A contagem de empurrões que falharam devido a erros de comunicação com mpNS.|Nenhuma|
|outgoing.mpns.authenticationerror|Erros de autenticação do MPNS|Contagem|Total|A contagem de empurrões que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhuma|
|notificationhub.pushes|Todas as Notificações De Saída|Contagem|Total|Todas as notificações de saída do centro de notificação|Nenhuma|
|entrada.todos.pedidos|Todos os pedidos de entrada|Contagem|Total|Total de pedidos de entrada para um centro de notificação|Nenhuma|
|incoming.all.failedrequests|Todos os pedidos falhados de entrada|Contagem|Total|Total de pedidos falhados para um centro de notificação|Nenhuma|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/espaços de trabalho

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Average_% Inodes Grátis|% Inodes livres|Contagem|Média|Average_% Inodes Grátis|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% espaço livre|% Espaço Livre|Contagem|Média|Average_% espaço livre|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% Inodes usados|% Inodos Usados|Contagem|Média|Average_% Inodes usados|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% Espaço Usado|% Espaço Usado|Contagem|Média|Average_% Espaço Usado|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Disk Ler Bytes/seg|Discos Ler Bytes/seg|Contagem|Média|Average_Disk Ler Bytes/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|leituras Average_Disk/seg|Leituras/seg de disco|Contagem|Média|leituras Average_Disk/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|transferências Average_Disk/seg|Transferências de disco/seg|Contagem|Média|transferências Average_Disk/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Disk Escrever Bytes/seg|Discos Write Bytes/seg|Contagem|Média|Average_Disk Escrever Bytes/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Disk Escreve/seg|Escritas de Disco/seg|Contagem|Média|Average_Disk Escreve/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Free Megabytes|Megabytes grátis|Contagem|Média|Average_Free Megabytes|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Logical Bytes/seg de disco|Bytes/sede de disco lógico|Contagem|Média|Average_Logical Bytes/seg de disco|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|memória disponível Average_%|% Memória Disponível|Contagem|Média|memória disponível Average_%|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% espaço disponível de swap|% Espaço de Troca Disponível %|Contagem|Média|Average_% espaço disponível de swap|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% memória usada|% Memória Usada|Contagem|Média|Average_% memória usada|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% espaço de troca usado|% Espaço de troca usado %|Contagem|Média|Average_% espaço de troca usado|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|memória Average_Available MBytes|Memória MBytes disponível|Contagem|Média|memória Average_Available MBytes|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Available Troca de MBytes|Troca de MBytes disponíveis|Contagem|Média|Average_Available Troca de MBytes|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Page Leituras/seg|Leituras da página/seg|Contagem|Média|Average_Page Leituras/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Page Escreve/seg|Página Escritas/seg|Contagem|Média|Average_Page Escreve/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Pages/seg|Páginas/seg|Contagem|Média|Average_Pages/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Used MBytes Swap Space|Espaço de troca de MBytes usado|Contagem|Média|Average_Used MBytes Swap Space|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Used Memória MBytes|MBytes de memória usada|Contagem|Média|Average_Used Memória MBytes|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Total Bytes Transmitidos|Total bytes transmitidos|Contagem|Média|Average_Total Bytes Transmitidos|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Total Bytes Recebidos|Total de Bytes Recebidos|Contagem|Média|Average_Total Bytes Recebidos|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Total Bytes|Bytes Totais|Contagem|Média|Average_Total Bytes|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|pacotes de Average_Total transmitidos|Pacotes totais transmitidos|Contagem|Média|pacotes de Average_Total transmitidos|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|pacotes Average_Total recebidos|Total de pacotes recebidos|Contagem|Média|pacotes Average_Total recebidos|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Total Erros Rx|Total de Erros Rx|Contagem|Média|Average_Total Erros Rx|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|erros Average_Total Tx|Total de erros Tx|Contagem|Média|erros Average_Total Tx|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|colisões Average_Total|Colisões totais|Contagem|Média|colisões Average_Total|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Avg. de Disco seg/Leitura|Avg. Disk sec/Read|Contagem|Média|Average_Avg. de Disco seg/Leitura|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Avg. Sec/Transferência de disco|Avg. Disco sec/Transferência|Contagem|Média|Average_Avg. Sec/Transferência de disco|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Avg. de Disco seg/Escrita|Avg. Disk sec/Write|Contagem|Média|Average_Avg. de Disco seg/Escrita|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Physical Bytes/seg de disco|Bytes/sede de disco físico|Contagem|Média|Average_Physical Bytes/seg de disco|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Pct Tempo Privilegiado|Tempo Privilegiado do Pct|Contagem|Média|Average_Pct Tempo Privilegiado|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|tempo de utilizador Average_Pct|Tempo de utilizador do Pct|Contagem|Média|tempo de utilizador Average_Pct|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Used Memory kBytes|KBytes de memória usada|Contagem|Média|Average_Used Memory kBytes|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|memória partilhada Average_Virtual|Memória Partilhada Virtual|Contagem|Média|memória partilhada Average_Virtual|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% de tempo dPC|% Tempo dPC|Contagem|Média|Average_% de tempo dPC|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% tempo de inatividade|% de Tempo Inativo|Contagem|Média|Average_% tempo de inatividade|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% de tempo de interrupção|% tempo de interrupção|Contagem|Média|Average_% de tempo de interrupção|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% IO Tempo de espera|% Io Tempo de Espera|Contagem|Média|Average_% IO Tempo de espera|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% bom tempo|% Bom Tempo|Contagem|Média|Average_% bom tempo|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% tempo privilegiado|% Tempo Privilegiado|Contagem|Média|Average_% tempo privilegiado|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% tempo do processador|% de Tempo do Processador|Contagem|Média|Average_% tempo do processador|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% tempo de utilizador|% Tempo de utilizador|Contagem|Média|Average_% tempo de utilizador|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|memória física Average_Free|Memória Física Gratuita|Contagem|Média|memória física Average_Free|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|espaço Average_Free em arquivos de paging|Espaço gratuito em arquivos de paging|Contagem|Média|espaço Average_Free em arquivos de paging|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Free Memória Virtual|Memória Virtual Gratuita|Contagem|Média|Average_Free Memória Virtual|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Processes|Processos|Contagem|Média|Average_Processes|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Size armazenado em arquivos de paging|Tamanho armazenado em arquivos de paging|Contagem|Média|Average_Size armazenado em arquivos de paging|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Uptime|Tempo de subida|Contagem|Média|Average_Uptime|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Users|Utilizadores|Contagem|Média|Average_Users|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|comprimento da fila do disco de Average_Current|Comprimento da fila do disco atual|Contagem|Média|comprimento da fila do disco de Average_Current|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Available MBytes|MBytes disponíveis|Contagem|Média|Average_Available MBytes|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_% bytes comprometidos em uso|% Bytes Comprometidos em Uso|Contagem|Média|Average_% bytes comprometidos em uso|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Bytes recebido/seg|Bytes Recebidos/seg|Contagem|Média|Average_Bytes recebido/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Bytes Enviado/seg|Bytes Enviado/seg|Contagem|Média|Average_Bytes Enviado/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Average_Bytes Total/seg|Bytes Total/seg|Contagem|Média|Average_Bytes Total/seg|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|comprimento da fila Average_Processor|Comprimento da fila do processador|Contagem|Média|comprimento da fila Average_Processor|Computador,Nome de objeto,nome de caso,contrapata,Sourcesystem|
|Heartbeat|Heartbeat|Contagem|Total|Heartbeat|Computador,OSType,Versão,SourceComputerId|
|Atualizar|Atualizar|Contagem|Média|Atualizar|Computador,Produto,Classificação,Estado de Atualização,Opcional,Aprovado|
|Evento|Evento|Contagem|Média|Evento|Fonte,EventLog,Computador,Categoria de Eventos,EventLevel,EventLevelName,EventID|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft.Peering/peeringServices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PrefixoLatency|Prefixo Latência|Milissegundos|Média|Latência prefixo mediana|PrefixoNome|

## <a name="microsoftpeeringpeerings"></a>Microsoft.Peering/peerings

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SessõesDisponibilidadeV4|Disponibilidade da Sessão V4|Percentagem|Média|Disponibilidade da sessão V4|Ligação|
|SessõesDisponibilidadeV6|Disponibilidade da Sessão V6|Percentagem|Média|Disponibilidade da sessão V6|Ligação|
|Taxa de tráfego ingresso|Taxa de tráfego de ingresso|BitsPerSecond|Média|Taxa de tráfego ingresso em bits por segundo|Ligação|
|EgressTrafficRate|Taxa de tráfego de egress|BitsPerSecond|Média|Taxa de tráfego de egress em bits por segundo|Ligação|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ConsultaDuração|Duração da Consulta|Milissegundos|Média|Dax Consulta duração no último intervalo|Sem Dimensões|
|ConsultaPoolJobQueueLength|Threads: Consulta de trabalho na piscina comprimento da fila|Contagem|Média|Número de empregos na fila da piscina de fios de consulta.|Sem Dimensões|
|qpu_high_utilization_metric|Maior Utilização de QPU|Contagem|Total|Utilização alta qpu em última hora, 1 para alta utilização de QPU, caso contrário 0|Sem Dimensões|
|memory_metric|Memória|Bytes|Média|Memória. Intervalo 0-3 GB para A1, 0-5 GB para A2, 0-10 GB para A3, 0-25 GB para A4, 0-50 GB para A5 e 0-100 GB para A6|Sem Dimensões|
|memory_thrashing_metric|Degradação de Memória|Percentagem|Média|A memória média a bater.|Sem Dimensões|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft.ProjectBabylon/contas

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Distribuição de ActivosByClassification|Distribuição de ativos por classificação|Contagem|Total|Indica o número de ativos com uma determinada classificação atribuída, ou seja, são classificados com essa etiqueta.|Classificação,Fonte,ResourceId|
|Tipo de armazenamento de bens|Distribuição de ativos por tipo de armazenamento|Contagem|Total|Indica o número de ativos com um determinado tipo de armazenamento.|Tipo de armazenamento,ResourceId|
|Utilizadores Catalogativos|Utilizadores Ativos Diários|Contagem|Total|Número de utilizadores ativos diariamente|ResourceId|
|Utilização de catálogos|Distribuição de Utilização por Operação|Contagem|Total|Indique o número de operação que o utilizador faz para o catálogo, ou é, Acesso, Pesquisa, Glossário.|Operação,Resourceid|
|NumberOfAssetsWithClassifications|Número de ativos com pelo menos uma classificação|Contagem|Média|Indica o número de ativos com pelo menos uma classificação de etiqueta.|ResourceId|
|ScanCancelado|Scan Cancelado|Contagem|Total|Indica o número de exames cancelados.|ResourceId|
|ScanCompleted|Digitalização Concluída|Contagem|Total|Indica o número de exames concluídos com sucesso.|ResourceId|
|ScanFailed|Scan Failed|Contagem|Total|Indica que o número de exames falhou.|ResourceId|
|ScanTimeTaken|Tempo de digitalização tomado|Segundos|Total|Indica o tempo total de digitalização em segundos.|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Contagem|Total|Sucesso ListenerConnections para Microsoft.Relay.|Nome de entidade,OperationResult|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Contagem|Total|Error do cliente em ListenerConnections para Microsoft.Relay.|Nome de entidade,OperationResult|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Contagem|Total|Error do servidor em ListenerConnections para Microsoft.Relay.|Nome de entidade,OperationResult|
|SenderConnections-Success|SenderConnections-Success|Contagem|Total|SenderConnections bem sucedido para Microsoft.Relay.|Nome de entidade,OperationResult|
|SenderConnections-ClientError|SenderConnections-ClientError|Contagem|Total|Error do cliente em SenderConnections para Microsoft.Relay.|Nome de entidade,OperationResult|
|SenderConnections-ServerError|SenderConnections-ServerError|Contagem|Total|Error do servidor em SenderConnections para Microsoft.Relay.|Nome de entidade,OperationResult|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Contagem|Total|Total ListenerConnections para Microsoft.Relay.|Nome da entidade|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Contagem|Total|Total de pedidos de SenderConnections para Microsoft.Relay.|Nome da entidade|
|ActiveConnections|ActiveConnections|Contagem|Total|Total ActiveConnections para Microsoft.Relay.|Nome da entidade|
|Ouvintes Ativos|Ouvintes Ativos|Contagem|Total|Total ActiveListeners para Microsoft.Relay.|Nome da entidade|
|BytesTransferido|BytesTransferido|Contagem|Total|Total bytesTransferido para Microsoft.Relay.|Nome da entidade|
|ListenerDisconnects|ListenerDisconnects|Contagem|Total|Total ListenerDisconnects para Microsoft.Relay.|Nome da entidade|
|SenderDisconnects|SenderDisconnects|Contagem|Total|Total de SenderDisconnects para Microsoft.Relay.|Nome da entidade|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SearchLatency|Latência de pesquisa|Segundos|Média|Latência média de pesquisa para o serviço de pesquisa|Nenhuma|
|PesquisaqueriesPerSecond|Pesquisar consultas por segundo|CondeEmSegundo|Média|Pesquisar consultas por segundo para o serviço de pesquisa|Nenhuma|
|ThrottledSearchQueriesPercentage|Percentagem de consultas de pesquisa acelerada|Percentagem|Média|Percentagem de consultas de pesquisa que foram estranguladas para o serviço de pesquisa|Nenhuma|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/espaços de nome

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Pedidos bem sucedidos|Pedidos bem sucedidos|Contagem|Total|Total de pedidos bem sucedidos para um espaço de nome|Nome de entidade,OperationResult|
|Erros de servidor|Erros do servidor.|Contagem|Total|Erros de servidor para Microsoft.ServiceBus.|Nome de entidade,OperationResult|
|Erros de utilizador|Erros do utilizador.|Contagem|Total|Erros de utilizador para Microsoft.ServiceBus.|Nome de entidade,OperationResult|
|ThrottledRequests|Pedidos estrangulados.|Contagem|Total|Pedidos throttled para Microsoft.ServiceBus.|Nome de entidade,OperationResult|
|Pedidos de entrada|Pedidos de Entrada|Contagem|Total|Pedidos de entrada para Microsoft.ServiceBus.|Nome da entidade|
|Mensagens de entrada|Mensagens de entrada|Contagem|Total|Mensagens de entrada para Microsoft.ServiceBus.|Nome da entidade|
|Mensagens de saída|Mensagens de saída|Contagem|Total|Mensagens de saída para Microsoft.ServiceBus.|Nome da entidade|
|ActiveConnections|ActiveConnections|Contagem|Total|Total de conexões ativas para Microsoft.ServiceBus.|Nenhuma|
|Conexões Abertas|Ligações abertas.|Contagem|Média|Ligações abertas para Microsoft.ServiceBus.|Nome da entidade|
|Conexões Fechadas|Ligações fechadas.|Contagem|Média|Ligações fechadas para Microsoft.ServiceBus.|Nome da entidade|
|Tamanho|Tamanho|Bytes|Média|Tamanho de uma fila/tópico em Bytes.|Nome da entidade|
|Mensagens|Contagem de mensagens numa fila/tópico.|Contagem|Média|Contagem de mensagens numa fila/tópico.|Nome da entidade|
|Mensagens Ativas|Contagem de mensagens ativas numa fila/tópico.|Contagem|Média|Contagem de mensagens ativas numa fila/tópico.|Nome da entidade|
|Mensagens mortas|Conde de mensagens com letras mortas numa fila/tópico.|Contagem|Média|Conde de mensagens com letras mortas numa fila/tópico.|Nome da entidade|
|Mensagens Agendadas|Contagem de mensagens programadas numa fila/tópico.|Contagem|Média|Contagem de mensagens programadas numa fila/tópico.|Nome da entidade|
|NomespaceCpuUsage|CPU|Percentagem|Máximo|Métrica de utilização de CPU premium de ônibus de serviço.|Réplica|
|Uso do espaço de nomeMemory|Utilização de Memória|Percentagem|Máximo|Métrica de utilização de memória de espaço premium de ônibus de serviço.|Réplica|
|CPUXNS|CPU (Depreciado)|Percentagem|Máximo|Métrica de utilização de CPU premium de ônibus de serviço. Esta métrica está deserdada. Utilize a métrica CPU (NamespaceCpuUsage) em vez disso.|Réplica|
|WSXNS|Utilização da memória (Depreciada)|Percentagem|Máximo|Métrica de utilização de memória de espaço premium de ônibus de serviço. Esta métrica está depreciada. Em vez disso, utilize a métrica de utilização da memória (NamespaceMemoryUsage).|Réplica|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Cpu atribuído|Cpu atribuído|Contagem|Média|Cpu alocado a este contentor em núcleos milli|Nome de aplicação,Nome de serviço,CódigoPacoteNome,Nome de Replica-Serviço|
|Memória Atribuída|Memória Atribuída|Bytes|Média|Memória atribuída a este recipiente em MB|Nome de aplicação,Nome de serviço,CódigoPacoteNome,Nome de Replica-Serviço|
|ActualCpu|ActualCpu|Contagem|Média|Utilização real do CPU em núcleos de milimil|Nome de aplicação,Nome de serviço,CódigoPacoteNome,Nome de Replica-Serviço|
|Memória Real|Memória Real|Bytes|Média|Uso real da memória em MB|Nome de aplicação,Nome de serviço,CódigoPacoteNome,Nome de Replica-Serviço|
|CpuUtilização|CpuUtilização|Percentagem|Média|Utilização do CPU para este contentor em percentagem do AllocatedCpu|Nome de aplicação,Nome de serviço,CódigoPacoteNome,Nome de Replica-Serviço|
|Utilização da Memória|Utilização da Memória|Percentagem|Média|Utilização do CPU para este contentor em percentagem do AllocatedCpu|Nome de aplicação,Nome de serviço,CódigoPacoteNome,Nome de Replica-Serviço|
|Estatuto de Aplicação|Estatuto de Aplicação|Contagem|Média|Aplicação de malha de tecido de serviço|Nome de aplicação,Estado|
|Estatuto de Serviço|Estatuto de Serviço|Contagem|Média|Estado de Saúde de um serviço na aplicação de malha de tecido de serviço|Nome de aplicação,Status,Nome de serviço|
|Estatuto de Replicação de Serviço|Estatuto de Replicação de Serviço|Contagem|Média|Estado de Saúde de uma réplica de serviço na aplicação de malha de tecido de serviço|Nome de aplicação,Status,ServiceName,ServiceReplicaName|
|Estatuto de Contentores|Estatuto de Contentores|Contagem|Média|Estado do recipiente na aplicação de malha de tecido de serviço|Nome de aplicação,Nome de serviço,CódigoPacoteNome,Nome de Replicação de Serviço,Status|
|Reiniciar Contagem|Reiniciar Contagem|Contagem|Média|Reinício da contagem de um recipiente na aplicação de malha de tecido de serviço|Nome de aplicação,Status,ServiceName,ServiceReplicaName,CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/Signalr

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Contagem de Ligações|Contagem de Ligação|Contagem|Máximo|A quantidade de ligação do utilizador.|Ponto Final|
|Contagem de mensagens|Contagem de Mensagens|Contagem|Total|A quantidade total de mensagens.|Nenhuma|
|Tráfego de entrada|Tráfego de Entrada|Bytes|Total|O tráfego de entrada de serviço|Nenhuma|
|Tráfego de saída|Tráfego de Saída|Bytes|Total|O tráfego de saída do serviço|Nenhuma|
|Erros de utilizador|Erros do utilizador|Percentagem|Máximo|A percentagem de erros do utilizador|Nenhuma|
|Erros de sistema|Erros do sistema|Percentagem|Máximo|A percentagem de erros do sistema|Nenhuma|



## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servidores/bases de dados

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Nenhuma|
|physical_data_read_percent|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|Nenhuma|
|log_write_percent|Taxa de Log IO|Percentagem|Média|Taxa de iO de log. Não aplicável aos armazéns de dados.|Nenhuma|
|dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|Percentagem DTU. Aplica-se às bases de dados baseadas em DTU.|Nenhuma|
|storage|Espaço de dados utilizado|Bytes|Máximo|Espaço de dados usado. Não aplicável aos armazéns de dados.|Nenhuma|
|connection_successful|Conexões bem sucedidas|Contagem|Total|Conexões bem sucedidas|Nenhuma|
|connection_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Nenhuma|
|blocked_by_firewall|Bloqueado por Firewall|Contagem|Total|Bloqueado por Firewall|Nenhuma|
|impasse|Impasses|Contagem|Total|Impasses. Não aplicável aos armazéns de dados.|Nenhuma|
|storage_percent|Espaço de dados usado por cento|Percentagem|Máximo|Espaço de dados usado por cento. Não aplicável a armazéns de dados ou bases de dados de hiperescala.|Nenhuma|
|xtp_storage_percent|Por cento de armazenamento OLTP em Memória|Percentagem|Média|In-Memory OLTP storage por cento. Não aplicável aos armazéns de dados.|Nenhuma|
|workers_percent|Percentagem de trabalhadores|Percentagem|Média|Percentagem de trabalhadores. Não aplicável aos armazéns de dados.|Nenhuma|
|sessions_percent|Percentagem de sessões|Percentagem|Média|Percentagem de sessões. Não aplicável aos armazéns de dados.|Nenhuma|
|dtu_limit|Limite dTU|Contagem|Média|Limite DTU. Aplica-se às bases de dados baseadas em DTU.|Nenhuma|
|dtu_used|DTU usado|Contagem|Média|DTU usado. Aplica-se às bases de dados baseadas em DTU.|Nenhuma|
|cpu_limit|Limite de CPU|Contagem|Média|Limite de CPU. Aplica-se às bases de dados baseadas em vCore.|Nenhuma|
|cpu_used|CPU utilizado|Contagem|Média|CPU usado. Aplica-se às bases de dados baseadas em vCore.|Nenhuma|
|dwu_limit|Limite de DWU|Contagem|Máximo|Limite de DWU. Aplica-se apenas aos armazéns de dados.|Nenhuma|
|dwu_consumption_percent|Percentagem de DWU|Percentagem|Máximo|Percentagem de DWU. Aplica-se apenas aos armazéns de dados.|Nenhuma|
|dwu_used|DWU usado|Contagem|Máximo|DWU usado. Aplica-se apenas aos armazéns de dados.|Nenhuma|
|cache_hit_percent|Percentagem de impacto cache|Percentagem|Máximo|Cache atingiu a percentagem. Aplica-se apenas aos armazéns de dados.|Nenhuma|
|cache_used_percent|Cache usado percentagem|Percentagem|Máximo|Cache usado percentagem. Aplica-se apenas aos armazéns de dados.|Nenhuma|
|sqlserver_process_core_percent<sup>1</sup> |SQL Server núcleo de processo por cento|Percentagem|Máximo|Percentagem de utilização do CPU para o processo Do Servidor SQL, medida pelo sistema operativo.|Nenhuma|
|<sup>sqlserver_process_memory_percent 1</sup> |SQL Server processa por cento da memória|Percentagem|Máximo|Percentagem de utilização da memória para o processo Do Servidor SQL, medida pelo sistema operativo.|Nenhuma|
|tempdb_data_size<sup>2</sup> |Kilobytes de tamanho de ficheiro de dados tempdb|Contagem|Máximo|Kilobytes de tamanho de ficheiro de dados tempdb.|Nenhuma|
|tempdb_log_size<sup>2</sup> |Kilobytes de tamanho de ficheiro de log tempdb|Contagem|Máximo|Kilobytes de ficheiro de log tempdb.|Nenhuma|
|<sup>tempdb_log_used_percent 2</sup> |Log por cento temporário usado|Percentagem|Máximo|Registo de percentagens temporárias usado.|Nenhuma|
|local_tempdb_usage_percent|Percentagem de temperatura local|Percentagem|Média|Percentagem de temperatura local. Aplica-se apenas aos armazéns de dados.|Nenhuma|
|app_cpu_billed|App CPU faturada|Contagem|Total|App CPU faturado. Aplica-se a bases de dados sem servidores.|Nenhuma|
|app_cpu_percent|Percentagem de CPU da aplicação|Percentagem|Média|Percentagem de CPU da aplicação. Aplica-se a bases de dados sem servidores.|Nenhuma|
|app_memory_percent|Percentagem de memória de aplicativos|Percentagem|Média|Percentagem de memória de aplicativos. Aplica-se a bases de dados sem servidores.|Nenhuma|
|allocated_data_storage|Espaço de dados atribuído|Bytes|Média|Armazenamento de dados atribuídos. Não aplicável aos armazéns de dados.|Nenhuma|
|memory_usage_percent|Percentagem de memória|Percentagem|Máximo|Percentagem de memória. Aplica-se apenas aos armazéns de dados.|Nenhuma|
|dw_backup_size_gb|Tamanho do armazenamento de dados|Contagem|Total|O tamanho do armazenamento de dados é composto pelo tamanho dos seus dados e pelo registo de transações. A métrica é contada para a parte de 'Armazenamento' da sua conta. Aplica-se apenas aos armazéns de dados.|Nenhuma|
|dw_snapshot_size_gb|Tamanho do armazenamento instantâneo|Contagem|Total|Tamanho de armazenamento instantâneo é o tamanho das alterações incrementais capturadas por instantâneos para criar pontos de restauro definidos pelo utilizador e automáticos. A métrica é contada para a parte de 'Armazenamento' da sua conta. Aplica-se apenas aos armazéns de dados.|Nenhuma|
|dw_geosnapshot_size_gb|Tamanho do armazenamento de recuperação de desastres|Contagem|Total|O tamanho do armazenamento de recuperação de desastres reflete-se como "Armazenamento de Recuperação de Desastres" na sua conta. Aplica-se apenas aos armazéns de dados.|Nenhuma|
|wlg_allocation_relative_to_system_percent|Atribuição de grupo de carga de trabalho por cento do sistema|Percentagem|Máximo|Percentagem atribuída de recursos relativamente a todo o sistema por grupo de carga de trabalho. Aplica-se apenas aos armazéns de dados.|Nome de grupo de trabalho,IsuserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Atribuição do grupo de carga de trabalho por cento de recursos da tampa|Percentagem|Máximo|Percentagem atribuída de recursos relativamente aos recursos limite siespecificados por grupo de carga de trabalho. Aplica-se apenas aos armazéns de dados.|Nome de grupo de trabalho,IsuserDefined|
|wlg_active_queries|Consultas ativas do grupo de trabalho|Contagem|Total|Consultas ativas dentro do grupo de carga de trabalho. Aplica-se apenas aos armazéns de dados.|Nome de grupo de trabalho,IsuserDefined|
|wlg_queued_queries|Consultas de grupo de trabalho em fila|Contagem|Total|Consultas na fila dentro do grupo de carga de trabalho. Aplica-se apenas aos armazéns de dados.|Nome de grupo de trabalho,IsuserDefined|
|active_queries|Consultas ativas|Contagem|Total|Consultas ativas em todos os grupos de carga de trabalho. Aplica-se apenas aos armazéns de dados.|Nenhuma|
|queued_queries|Consultas em fila|Contagem|Total|Consultas em fila em todos os grupos de carga de trabalho. Aplica-se apenas aos armazéns de dados.|Nenhuma|
|wlg_active_queries_timeouts|Intervalos de consulta de grupo de trabalho|Contagem|Total|Consultas que têm cronometrado para o grupo de carga de trabalho. Aplica-se apenas aos armazéns de dados.|Nome de grupo de trabalho,IsuserDefined|
|wlg_effective_min_resource_percent|Por cento de recursos min eficazes|Percentagem|Máximo|Percentagem mínima de recursos reservados e isolados para o grupo de carga de trabalho, tendo em conta o nível mínimo de serviço. Aplica-se apenas aos armazéns de dados.|Nome de grupo de trabalho,IsuserDefined|
|wlg_effective_cap_resource_percent|Por cento de recursos de tampa eficazes|Percentagem|Máximo|Um limite rígido à percentagem de recursos permitidos para o grupo de carga de trabalho, tendo em conta a Percentagem efetiva de Recursos Min atribuída a outros grupos de carga de trabalho. Aplica-se apenas aos armazéns de dados.|Nome de grupo de trabalho,IsuserDefined|
|full_backup_size_bytes|Tamanho total do armazenamento de backup|Bytes|Máximo|Tamanho acumulado de armazenamento de reserva completa. Aplica-se às bases de dados baseadas em vCore. Não aplicável às bases de dados de Hiperescala.|Nenhuma|
|diff_backup_size_bytes|Tamanho diferencial de armazenamento de backup|Bytes|Máximo|Tamanho acumulado de armazenamento de backup diferencial. Aplica-se às bases de dados baseadas em vCore. Não aplicável às bases de dados de Hiperescala.|Nenhuma|
|log_backup_size_bytes|Registar tamanho de armazenamento de backup|Bytes|Máximo|Tamanho acumulado de armazenamento de backup de registo. Aplica-se às bases de dados baseadas em vCore e em hiperescala.|Nenhuma|
|snapshot_backup_size_bytes|Tamanho de armazenamento de backup instantâneo|Bytes|Máximo|Tamanho cumulativo de armazenamento de backup instantâneo. Aplica-se às bases de dados de Hiperescala.|Nenhuma|
|base_blob_size_bytes|Tamanho de armazenamento de bolha de base|Bytes|Máximo|Tamanho de armazenamento de bolhas base. Aplica-se às bases de dados de Hiperescala.|Nenhuma|

<sup>1</sup> Esta métrica está disponível para bases de dados utilizando o modelo de compra vCore com 2 vCores e mais alto, ou 200 DTU e superior para modelos de compra baseados em DTU. 

<sup>2</sup> Esta métrica está disponível para bases de dados utilizando o modelo de compra vCore com 2 vCores e mais alto, ou 200 DTU e superior para modelos de compra baseados em DTU. Esta métrica não está atualmente disponível para bases de dados de hiperescala ou armazéns de dados.

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servidores/elasticPools

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Nenhuma|
|database_cpu_percent|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Base de Dados Resourceid|
|physical_data_read_percent|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|Nenhuma|
|database_physical_data_read_percent|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|Base de Dados Resourceid|
|log_write_percent|Taxa de Log IO|Percentagem|Média|Taxa de Log IO|Nenhuma|
|database_log_write_percent|Taxa de Log IO|Percentagem|Média|Taxa de Log IO|Base de Dados Resourceid|
|dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|Percentagem DTU. Aplica-se a piscinas elásticas baseadas em DTU.|Nenhuma|
|database_dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|Percentagem de DTU|Base de Dados Resourceid|
|storage_percent|Espaço de dados usado por cento|Percentagem|Média|Espaço de dados usado por cento|Nenhuma|
|workers_percent|Percentagem de trabalhadores|Percentagem|Média|Percentagem de trabalhadores|Nenhuma|
|database_workers_percent|Percentagem de trabalhadores|Percentagem|Média|Percentagem de trabalhadores|Base de Dados Resourceid|
|sessions_percent|Percentagem de sessões|Percentagem|Média|Percentagem de sessões|Nenhuma|
|database_sessions_percent|Percentagem de sessões|Percentagem|Média|Percentagem de sessões|Base de Dados Resourceid|
|eDTU_limit|limite eDTU|Contagem|Média|limite eDTU. Aplica-se a piscinas elásticas baseadas em DTU.|Nenhuma|
|storage_limit|Tamanho máximo de dados|Bytes|Média|Tamanho máximo de dados|Nenhuma|
|eDTU_used|eDTU utilizado|Contagem|Média|eDTU usado. Aplica-se a piscinas elásticas baseadas em DTU.|Nenhuma|
|database_eDTU_used|eDTU utilizado|Contagem|Média|eDTU utilizado|Base de Dados Resourceid|
|storage_used|Espaço de dados utilizado|Bytes|Média|Espaço de dados utilizado|Nenhuma|
|database_storage_used|Espaço de dados utilizado|Bytes|Média|Espaço de dados utilizado|Base de Dados Resourceid|
|xtp_storage_percent|Por cento de armazenamento OLTP em Memória|Percentagem|Média|Por cento de armazenamento OLTP em Memória|Nenhuma|
|cpu_limit|Limite de CPU|Contagem|Média|Limite de CPU. Aplica-se a piscinas elásticas baseadas em vCore.|Nenhuma|
|database_cpu_limit|Limite de CPU|Contagem|Média|Limite de CPU|Base de Dados Resourceid|
|cpu_used|CPU utilizado|Contagem|Média|CPU usado. Aplica-se a piscinas elásticas baseadas em vCore.|Nenhuma|
|database_cpu_used|CPU utilizado|Contagem|Média|CPU utilizado|Base de Dados Resourceid|
|sqlserver_process_core_percent<sup>1</sup>|SQL Server núcleo de processo por cento|Percentagem|Máximo|Percentagem de utilização do CPU para o processo Do Servidor SQL, medida pelo sistema operativo. Aplica-se a piscinas elásticas. |Nenhuma|
|<sup>sqlserver_process_memory_percent 1</sup>|SQL Server processa por cento da memória|Percentagem|Máximo|Percentagem de utilização da memória para o processo Do Servidor SQL, medida pelo sistema operativo. Aplica-se a piscinas elásticas. |Nenhuma|
|tempdb_data_size<sup>2</sup>|Kilobytes de tamanho de ficheiro de dados tempdb|Contagem|Máximo|Kilobytes de tamanho de ficheiro de dados tempdb.|Nenhuma|
|tempdb_log_size<sup>2</sup>|Kilobytes de tamanho de ficheiro de log tempdb|Contagem|Máximo|Kilobytes de ficheiro de log tempdb. |Nenhuma|
|<sup>tempdb_log_used_percent 2</sup>|Log por cento temporário usado|Percentagem|Máximo|Registo de percentagens temporárias usado.|Nenhuma|
|allocated_data_storage|Espaço de dados atribuído|Bytes|Média|Espaço de dados atribuído|Nenhuma|
|database_allocated_data_storage|Espaço de dados atribuído|Bytes|Média|Espaço de dados atribuído|Base de Dados Resourceid|
|allocated_data_storage_percent|Espaço de dados atribuído por cento|Percentagem|Máximo|Espaço de dados atribuído por cento|Nenhuma|

<sup>1</sup> Esta métrica está disponível para bases de dados utilizando o modelo de compra vCore com 2 vCores e mais alto, ou 200 DTU e superior para modelos de compra baseados em DTU. 

<sup>2</sup> Esta métrica está disponível para bases de dados utilizando o modelo de compra vCore com 2 vCores e mais alto, ou 200 DTU e superior para modelos de compra baseados em DTU. Esta métrica não está atualmente disponível para bases de dados de hiperescala.


## <a name="microsoftsqlservers"></a>Microsoft.Sql/servidores

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|Percentagem de DTU|ElasticPoolResourceid|
|database_dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|Percentagem de DTU|Base de DadosResourceid,ElasticPoolResourceid|
|storage_used|Espaço de dados utilizado|Bytes|Média|Espaço de dados utilizado|ElasticPoolResourceid|
|database_storage_used|Espaço de dados utilizado|Bytes|Média|Espaço de dados utilizado|Base de DadosResourceid,ElasticPoolResourceid|
|dtu_used|DTU usado|Contagem|Média|DTU usado|Base de Dados Resourceid|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|virtual_core_count|Contagem de núcleos virtuais|Contagem|Média|Contagem de núcleos virtuais|Nenhuma|
|avg_cpu_percent|Percentagem média de CPU|Percentagem|Média|Percentagem média de CPU|Nenhuma|
|reserved_storage_mb|Espaço de armazenamento reservado|Contagem|Média|Espaço de armazenamento reservado|Nenhuma|
|storage_space_used_mb|Espaço de armazenamento usado|Contagem|Média|Espaço de armazenamento usado|Nenhuma|
|io_requests|IO pedidos contam|Contagem|Média|IO pedidos contam|Nenhuma|
|io_bytes_read|IO bytes lidos|Bytes|Média|IO bytes lidos|Nenhuma|
|io_bytes_written|IO bytes escritos|Bytes|Média|IO bytes escritos|Nenhuma|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Capacidade Utilizada|Capacidade usada|Bytes|Média|Capacidade utilizada pela conta|Nenhuma|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão Do Tipo resposta para o número de diferentes tipos de resposta.|Tipo de resposta,GeoType,Nome ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de ingresso, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média usada pelo Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência média de fim a ponta dos pedidos bem sucedidos feitos a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Armazenamento/armazenamentoContas/blobServices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BlobCapacity|Capacidade blob|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Blob da conta de armazenamento em bytes.|BlobType,Tier|
|BlobCount|Contagem de Bolhas|Contagem|Média|O número de Blob no serviço Blob da conta de armazenamento.|BlobType,Tier|
|Contagem de contentores|Contagem de contentores blob|Contagem|Média|O número de contentores no serviço Blob da conta de armazenamento.|Nenhuma|
|IndexCapacity|Capacidade indexada|Bytes|Média|A quantidade de armazenamento utilizada pelo Índice ADLS Gen2 (Hierárquico) em bytes.|Nenhuma|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão Do Tipo resposta para o número de diferentes tipos de resposta.|Tipo de resposta,GeoType,Nome ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de ingresso, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média usada pelo Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência média de fim a ponta dos pedidos bem sucedidos feitos a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Armazenamento/armazenamentoContas/tableServices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Capacidade de Mesa|Capacidade de Mesa|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço de mesa da conta de armazenamento em bytes.|Nenhuma|
|Contagem de tabelas|Contagem de mesas|Contagem|Média|O número de mesa no serviço de mesa da conta de armazenamento.|Nenhuma|
|Contagem de tabelas|Contagem de entidades de mesa|Contagem|Média|O número de entidades de mesa no serviço mesa da conta de armazenamento.|Nenhuma|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão Do Tipo resposta para o número de diferentes tipos de resposta.|Tipo de resposta,GeoType,Nome ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de ingresso, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média usada pelo Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência média de fim a ponta dos pedidos bem sucedidos feitos a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Armazenamento/armazenamentoContas/ficheirosServiços

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Capacidade de Arquivo|Capacidade de arquivo|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço de arquivos da conta de armazenamento em bytes.|FileShare|
|Contagem de Ficheiros|Contagem de Ficheiros|Contagem|Média|O número de ficheiros no serviço de ficheiros da conta de armazenamento.|FileShare|
|FileShareCount|Contagem de partilha de ficheiros|Contagem|Média|O número de ações de ficheiros no serviço de ficheiros da conta de armazenamento.|Nenhuma|
|FileShareSnapshotCount|Contagem de instantâneos de partilha de ficheiros|Contagem|Média|O número de instantâneos presentes na parte no Serviço de Ficheiros da conta de armazenamento.|FileShare|
|FileShareSnapshotSize|Tamanho instantâneo de partilha de ficheiros|Bytes|Média|A quantidade de armazenamento utilizada pelos instantâneos no serviço de ficheiros da conta de armazenamento em bytes.|FileShare|
|FileShareQuota|Tamanho da quota de partilha de ficheiros|Bytes|Média|O limite superior da quantidade de armazenamento que pode ser usado pelo Serviço de Ficheiros Azure em bytes.|FileShare|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão Do Tipo resposta para o número de diferentes tipos de resposta.|Tipo de resposta,GeoType,ApiName,Autenticação,FileShare|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de ingresso, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação,FileShare|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação,FileShare|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média usada pelo Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType,ApiName,Autenticação,FileShare|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência média de fim a ponta dos pedidos bem sucedidos feitos a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação,FileShare|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação,FileShare|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Armazenamento/armazenamentoContas/filaServiços

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Capacidade de fila|Capacidade de fila|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço de fila da conta de armazenamento em bytes.|Nenhuma|
|Contagem de filas|Contagem de filas|Contagem|Média|O número de filas no serviço de fila da conta de armazenamento.|Nenhuma|
|Contagem de mensagens de fila|Contagem de mensagens de fila|Contagem|Média|O número aproximado de mensagens de fila no serviço de fila da conta de armazenamento.|Nenhuma|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Utilize a dimensão Do Tipo resposta para o número de diferentes tipos de resposta.|Tipo de resposta,GeoType,Nome ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de ingresso, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média usada pelo Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência média de fim a ponta dos pedidos bem sucedidos feitos a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|





## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/caches

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ClientiOPS|IOPS total do cliente|Contagem|Média|A taxa de operações de ficheiros de clientes processadas pelo Cache.|Nenhuma|
|ClienteLatency|Latência média do cliente|Milissegundos|Média|Latência média das operações de ficheiros do cliente para o Cache de Armazenamento.|Nenhuma|
|ClientReadIOPS|Cliente ler IOPS|CondeEmSegundo|Média|O cliente lê operações por segundo.|Nenhuma|
|ClientReadThroughput|Média de leitura de cache|BytesPerSecond|Média|O cliente leu a taxa de transferência de dados.|Nenhuma|
|ClientWriteIOPS|IOPS de escrita de cliente|CondeEmSegundo|Média|Cliente escreve operações por segundo.|Nenhuma|
|ClientWriteThroughput|Média cache write por ção|BytesPerSecond|Média|Cliente escrever taxa de transferência de dados.|Nenhuma|
|Metadados do ClienteReadIOPS|Metadados do Cliente Ler IOPS|CondeEmSegundo|Média|A taxa de operações de ficheiros de clientes enviadas para o Cache, excluindo as leituras de dados, que não modificam o estado persistente.|Nenhuma|
|ClientesMetadosWriteIOPS|Metadados de clientes Escrevem IOPS|CondeEmSegundo|Média|A taxa de operações de ficheiros de clientes enviadas para o Cache, excluindo os dados escritos, que modificam o estado persistente.|Nenhuma|
|ClientLockiOPS|IOPS de bloqueio de cliente|CondeEmSegundo|Média|Operações de bloqueio de ficheiros de cliente por segundo.|Nenhuma|
|StorageTargetHealth|Saúde alvo de armazenamento|Contagem|Média|Resultados booleanos do teste de conectividade entre os Alvos de Cache e Armazenamento.|Nenhuma|
|Tempo de subida|Tempo de subida|Contagem|Média|Resultados booleanos do teste de conectividade entre o Cache e o sistema de monitorização.|Nenhuma|
|Alvos de armazenamento|IOPS total de armazenamento|Contagem|Média|A taxa de todas as operações de ficheiroque o Cache envia para um determinado StorageTarget.|ArmazenamentoTarget|
|StorageTargetWriteIOPS|ArmazenamentoTarget Escrever IOPS|Contagem|Média|A taxa das operações de escrita de ficheiros que o Cache envia para um determinado StorageTarget.|ArmazenamentoTarget|
|StorageTargetAsyncWriteThroughput|StorageTarget Asynchronous Write Throughput|BytesPerSecond|Média|A taxa que o Cache asincronizamente escreve dados para um determinado StorageTarget. São escritos oportunistas que não fazem com que os clientes bloqueiem.|ArmazenamentoTarget|
|StorageTargetSyncWriteThroughput|StorageTarget Synchronous Write Throughput|BytesPerSecond|Média|A taxa que o Cache escreve sincronizadamente os dados para um determinado StorageTarget. Estes são escritos que fazem com que os clientes bloqueiem.|ArmazenamentoTarget|
|StorageTargetTotalWriteThroughput|StorageTarget Total Write Throughput|BytesPerSecond|Média|A taxa total que o Cache escreve dados para um determinado StorageTarget.|ArmazenamentoTarget|
|ArmazenamentoTargetLatency|Latência StorageTarget|Milissegundos|Média|A latência média de ida e volta de todas as operações de arquivo que o Cache envia para um Armazenamento particularTarget.|ArmazenamentoTarget|
|ArmazenamentoTargetMetasLeiaIOPS|ArmazenamentoTarget Metadados Ler IOPS|CondeEmSegundo|Média|A taxa de operações de ficheiros que não modificam o estado persistente, excluindo a operação de leitura, que o Cache envia para um determinado StorageTarget.|ArmazenamentoTarget|
|ArmazenamentoTargetMetadataWriteIOPS|ArmazenamentoTarget Metadados Escrevam IOPS|CondeEmSegundo|Média|A taxa de operações de ficheiros que modificam o estado persistente e excluindo a operação de escrita, que o Cache envia para um determinado StorageTarget.|ArmazenamentoTarget|
|ArmazenamentoTargetReadIOPS|ArmazenamentoTarget Ler IOPS|CondeEmSegundo|Média|A taxa de operações de leitura de ficheiros que o Cache envia para um determinado StorageTarget.|ArmazenamentoTarget|
|StorageTargetReadAheadThroughput|ArmazenamentoTarget Ler à frente|BytesPerSecond|Média|A taxa que o Cache lê oportunisticamente os dados do StorageTarget.|ArmazenamentoTarget|
|StorageTargetFillThroughput|ArmazenamentoTarget Fill Ingput|BytesPerSecond|Média|A taxa que o Cache lê os dados do StorageTarget para lidar com uma falha de cache.|ArmazenamentoTarget|
|StorageTargetTotalReadThroughput|ArmazenamentoTarget Total Read Throughput|BytesPerSecond|Média|A taxa total que o Cache lê dados de um determinado StorageTarget.|ArmazenamentoTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Resultado da sessão do servidor|Resultado da sessão de sincronização|Contagem|Média|Métrica que regista um valor de 1 cada vez que o Ponto final do servidor completa com sucesso uma Sessão de Sincronização com o Cloud Endpoint|Nome do SyncGroup,ServerEndpointName,SyncDirection|
|Contagem de ficheiros aplicados de sincronização de armazenamento|Ficheiros Sincronizados|Contagem|Total|Contagem de Ficheiros sincronizados|Nome do SyncGroup,ServerEndpointName,SyncDirection|
|ArmazenamentoSyncSyncSessionPerItemErrorsCount|Ficheiros que não sincronizam|Contagem|Total|Contagem de ficheiros não conseguiu sincronizar|Nome do SyncGroup,ServerEndpointName,SyncDirection|
|Ficheiros Transferidos de Ficheiros transferidos de armazenamento|Bytes sincronizados|Bytes|Total|Tamanho total do ficheiro transferido para Sessões de Sincronização|Nome do SyncGroup,ServerEndpointName,SyncDirection|
|Heartbeat Do StorageSyncServer|Estado On-Line do Servidor|Contagem|Máximo|Métrica que regista um valor de 1 cada vez que o servidor ressitered registra com sucesso um batimento cardíaco com o Cloud Endpoint|ServerName|
|ArmazenamentoSyncRecallIOTotalSizeBytes|Recolha de tiering de nuvem|Bytes|Total|Tamanho total dos dados recordados pelo servidor|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Tamanho da recuperação do tiering de nuvem|Bytes|Total|Tamanho dos dados recordados|Nome do SyncGroup,Nome do Servidor|
|StorageSyncRecallThroughputBytesPerSecond|Entrada de recolha de tiering de cloud|BytesPerSecond|Média|Tamanho da recolha de dados|Nome do SyncGroup,Nome do Servidor|
|StorageSyncRecalledNetworkBytesByApplication|Tamanho de recolha de nível de nuvem por aplicação|Bytes|Total|Tamanho dos dados recordados por aplicação|Nome do SyncGroup,Nome do servidor,Nome de aplicação|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Contagem de ficheiros aplicados syncgroupsession|Ficheiros Sincronizados|Contagem|Total|Contagem de Ficheiros sincronizados|Nome do SyncGroup,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Ficheiros que não sincronizam|Contagem|Total|Contagem de ficheiros não conseguiu sincronizar|Nome do SyncGroup,ServerEndpointName,SyncDirection|
|SyncGroupBatchTransferFileBytes|Bytes sincronizados|Bytes|Total|Tamanho total do ficheiro transferido para Sessões de Sincronização|Nome do SyncGroup,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|Ficheiros Sincronizados|Contagem|Total|Contagem de Ficheiros sincronizados|ServerEndpointName,SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Ficheiros que não sincronizam|Contagem|Total|Contagem de ficheiros não conseguiu sincronizar|ServerEndpointName,SyncDirection|
|ServerEndpointBatchTransferFileBytes|Bytes sincronizados|Bytes|Total|Tamanho total do ficheiro transferido para Sessões de Sincronização|ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Batimentocardíaco de servidor|Estado On-Line do Servidor|Contagem|Máximo|Métrica que regista um valor de 1 cada vez que o servidor ressitered registra com sucesso um batimento cardíaco com o Cloud Endpoint|ServerResourceid,Nome do servidor|
|ServerRecallIOTotalSizeBytes|Recolha de tiering de nuvem|Bytes|Total|Tamanho total dos dados recordados pelo servidor|ServerResourceid,Nome do servidor|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Utilização de Recursos|SU % Utilização|Percentagem|Máximo|SU % Utilização|Nome lógico,Partitionid|
|InputEvents|Eventos de entrada|Contagem|Total|Eventos de entrada|Nome lógico,Partitionid|
|InputEventBytes|Bytes de Evento de Entrada|Bytes|Total|Bytes de Evento de Entrada|Nome lógico,Partitionid|
|Eventos LateInput|Eventos de entrada tardia|Contagem|Total|Eventos de entrada tardia|Nome lógico,Partitionid|
|Eventos de saída|Eventos de Saída|Contagem|Total|Eventos de Saída|Nome lógico,Partitionid|
|Erros de Conversão|Erros de Conversão de Dados|Contagem|Total|Erros de Conversão de Dados|Nome lógico,Partitionid|
|Erros|Erros de tempo de execução|Contagem|Total|Erros de tempo de execução|Nome lógico,Partitionid|
|DropOrAdjustedEvents|Fora de ordem Eventos|Contagem|Total|Fora de ordem Eventos|Nome lógico,Partitionid|
|AMLCalloutRequests|Pedidos de Função|Contagem|Total|Pedidos de Função|Nome lógico,Partitionid|
|AMLCalloutPedidos Falhados|Pedidos de função falhados|Contagem|Total|Pedidos de função falhados|Nome lógico,Partitionid|
|AMLCalloutInputevents|Eventos de Função|Contagem|Total|Eventos de Função|Nome lógico,Partitionid|
|Erro de desserialização|Erros de desserialização de entrada|Contagem|Total|Erros de desserialização de entrada|Nome lógico,Partitionid|
|Eventos EarlyInput|Eventos de entrada precoce|Contagem|Total|Eventos de entrada precoce|Nome lógico,Partitionid|
|OutputWatermarkDelaySeconds|Atraso na marca de água|Segundos|Máximo|Atraso na marca de água|Nome lógico,Partitionid|
|InputEventsSourcesBacklogged|Eventos de entrada atrasados|Contagem|Máximo|Eventos de entrada atrasados|Nome lógico,Partitionid|
|InputEventsSourcesPerSecond|Fontes de entrada recebidas|Contagem|Total|Fontes de entrada recebidas|Nome lógico,Partitionid|

## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/espaços de trabalho

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|OrquestraçãoPipelineRunsEnded|Gasodutos terminados|Contagem|Total|Contagem de corridas de gasoduto sinuoso que conseguiu, falhou ou foram cancelados|Resultado,FalhaType,Pipeline|
|OrquestraçãoActivityRunsEnded|As corridas de atividades terminaram|Contagem|Total|Contagem de atividades de orquestração que conseguiu, falhou ou foram cancelados|Resultado,FalhaType,Atividade,ActivityType,Pipeline|
|OrquestraçõesTriggersEnded|Os gatilhos terminaram|Contagem|Total|Contagem de gatilhos de orquestração que conseguiu, falhou ou foram cancelados|Resultado,FalhaType,Gatilho|
|Tentativas de Login SQLOnDemandLogin|Tentativas de login|Contagem|Total|Contagem de tentativas de login que succed ou falhou|Resultado|
|SQLOnDemandQueriesEnded|As consultas terminaram|Contagem|Total|Contagem de consultas que sucederam, falharam ou foram cancelados|Resultado|
|SQLOnDemandQueryProcessadobytes|Dados tratados|Bytes|Total|Quantidade de dados tratados por consultas|Nenhuma|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SparkJobsEnded|Aplicações terminadas|Contagem|Total|Contagem de candidaturas terminadas|JobType,JobResult|
|Capacidade de Núcleos|Capacidade de núcleos|Contagem|Máximo|Capacidade de núcleos|Nenhuma|
|Capacidade de memóriaGB|Capacidade de memória (GB)|Contagem|Máximo|Capacidade de memória (GB)|Nenhuma|

## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|DWULimit|Limite de DWU|Contagem|Máximo|Objetivo de nível de serviço da piscina SQL|Nenhuma|
|DWUUsed|DWU usado|Contagem|Máximo|Representa uma representação de alto nível de uso em toda a piscina SQL. Medido pelo limite de DWU * Percentagem de DWU|Nenhuma|
|DWUUsedPercent|DWU percentagem utilizada|Percentagem|Máximo|Representa uma representação de alto nível de uso em toda a piscina SQL. Medido tomando o máximo entre a percentagem de CPU e a percentagem de Dados IO|Nenhuma|
|ConexõesBlockedByFirewall|Ligações bloqueadas por firewall|Contagem|Total|Contagem de ligações bloqueadas por regras de firewall. Reveja as políticas de controlo de acesso para o seu pool SQL e monitorize estas ligações se a contagem for elevada|Nenhuma|
|CacheHitPercent adaptativo|Percentagem de impacto de cache adaptativa|Percentagem|Máximo|Mede a forma como as cargas de trabalho estão a utilizar a cache adaptativa. Utilize esta métrica com a métrica da percentagem de impacto da cache para determinar se deve escalar para capacidade adicional ou reexecutar cargas de trabalho para hidratar a cache|Nenhuma|
|CacheUsedPercent adaptativo|Cache adaptável utilizada percentagem|Percentagem|Máximo|Mede a forma como as cargas de trabalho estão a utilizar a cache adaptativa. Utilize esta métrica com a métrica de percentagem utilizada para determinar se deve escalar para capacidade adicional ou reexecutar cargas de trabalho para hidratar a cache|Nenhuma|
|LocalTempDBUsedPercent|A temperatura local usou a percentagem|Percentagem|Máximo|Utilização temporária local em todos os nós de computação - valores são emitidos a cada cinco minutos|Nenhuma|
|MemoryUsedPercent|Percentagem usada na memória|Percentagem|Máximo|Utilização da memória em todos os nós da piscina SQL|Nenhuma|
|Ligações|Ligações|Contagem|Total|Contagem de logins totais para a piscina SQL|Resultado|
|WLGActiveQueries|Consultas ativas do grupo de trabalho|Contagem|Total|As consultas ativas dentro do grupo de carga de trabalho. A utilização desta métrica não filtrada e não dividida apresenta todas as consultas ativas em execução no sistema|IsuserDefined,WorkloadGroup|
|WLGActiveQueriesTimeouts|Intervalos de consulta de grupo de trabalho|Contagem|Total|Consultas para o grupo de trabalho que tem cronometrado. Os intervalos de consulta reportados por esta métrica são apenas uma vez que a consulta tenha começado a ser executada (não inclui tempo de espera devido a bloqueio ou esperas de recursos)|IsuserDefined,WorkloadGroup|
|WLGAllocationBySystemPercent|Atribuição de grupo de carga de trabalho por cento do sistema|Percentagem|Máximo|A repartição percentual dos recursos em relação a todo o sistema|IsuserDefined,WorkloadGroup|
|WLGAllocationByMaxResourcePercent|Atribuição do grupo de carga de trabalho por cento de recursos máximos|Percentagem|Máximo|Apresenta a repartição percentual de recursos em relação ao recurso efetiva da tampa por grupo de carga de trabalho. Esta métrica proporciona a utilização eficaz do grupo de carga de trabalho|IsuserDefined,WorkloadGroup|
|WLGEffectiveCapResourcePercent|Por cento de recursos de tampa eficazes|Percentagem|Máximo|O custo efetivo do recurso da tampa para o grupo de carga de trabalho. Se houver outros grupos de carga de trabalho com min_percentage_resource > 0, o effective_cap_percentage_resource é reduzido proporcionalmente|IsuserDefined,WorkloadGroup|
|wlg_effective_min_resource_percent|Por cento de recursos min eficazes|Percentagem|Mínimo|A definição efetiva da percentagem de recursos min permitiu considerar o nível de serviço e as definições do grupo de carga de trabalho. O min_percentage_resource eficaz pode ser ajustado mais alto em níveis de serviço mais baixos|IsuserDefined,WorkloadGroup|
|WLGQueuedQueries|Consultas de grupo de trabalho em fila|Contagem|Total|Contagem acumulada de pedidos em fila após o limite máximo de conmoedação foi atingido|IsuserDefined,WorkloadGroup|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/ambientes

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Mensagens Recebidas|Mensagens Recebidas de Ingress|Contagem|Total|Contagem de mensagens lidas de todas as fontes do evento hub ou ioT hub|Nenhuma|
|Mensagens Inválidas|Ingress recebeu mensagens inválidas|Contagem|Total|Contagem de mensagens inválidas lidas de todas as fontes do evento hub ou ioT hub|Nenhuma|
|IngressReceivedBytes|Ingress recebido bytes|Bytes|Total|Contagem de bytes lidos de todas as fontes do evento|Nenhuma|
|IngressStoredBytes|Ingress Stored Bytes|Bytes|Total|Tamanho total dos eventos processados com sucesso e disponíveis para consulta|Nenhuma|
|Eventos IngressStored|Eventos Armazenados de Ingress|Contagem|Total|Contagem de eventos achatados processados com sucesso e disponíveis para consulta|Nenhuma|
|Mensagens RecebidasTimelag|Ingress Recebeu O Tempo de Atraso|Segundos|Máximo|Diferença entre o tempo em que a mensagem é enquecida na fonte do evento e o tempo que é processado em Ingress|Nenhuma|
|IngressReceivedMessagesCountlag|Ingress Recebeu Mensagens Count Lag|Contagem|Média|Diferença entre o número de sequência da última mensagem enqueuada na partição da fonte do evento e o número de sequência de mensagens que estão a ser processadas em Ingress|Nenhuma|
|WarmStorageMaxProperties|Propriedades Max de Armazenamento Quente|Contagem|Máximo|Número máximo de propriedades permitidas pelo ambiente para S1/S2 SKU e número máximo de propriedades permitidas pela Warm Store para PAYG SKU|Nenhuma|
|Propriedades De Armazenamento Quente|Propriedades usadas de armazenamento quente |Contagem|Máximo|Número de propriedades utilizadas pelo ambiente para S1/S2 SKU e número de propriedades utilizadas pela Warm Store para PAYG SKU|Nenhuma|



## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/ambientes/fontes de eventos

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Mensagens Recebidas|Mensagens Recebidas de Ingress|Contagem|Total|Contagem de mensagens lidas a partir da fonte do evento|Nenhuma|
|Mensagens Inválidas|Ingress recebeu mensagens inválidas|Contagem|Total|Contagem de mensagens inválidas lidas a partir da fonte do evento|Nenhuma|
|IngressReceivedBytes|Ingress recebido bytes|Bytes|Total|Contagem de bytes lidos a partir da fonte do evento|Nenhuma|
|IngressStoredBytes|Ingress Stored Bytes|Bytes|Total|Tamanho total dos eventos processados com sucesso e disponíveis para consulta|Nenhuma|
|Eventos IngressStored|Eventos Armazenados de Ingress|Contagem|Total|Contagem de eventos achatados processados com sucesso e disponíveis para consulta|Nenhuma|
|Mensagens RecebidasTimelag|Ingress Recebeu O Tempo de Atraso|Segundos|Máximo|Diferença entre o tempo em que a mensagem é enquecida na fonte do evento e o tempo que é processado em Ingress|Nenhuma|
|IngressReceivedMessagesCountlag|Ingress Recebeu Mensagens Count Lag|Contagem|Média|Diferença entre o número de sequência da última mensagem enqueuada na partição da fonte do evento e o número de sequência de mensagens que estão a ser processadas em Ingress|Nenhuma|
|WarmStorageMaxProperties|Propriedades Max de Armazenamento Quente|Contagem|Máximo|Número máximo de propriedades permitidas pelo ambiente para S1/S2 SKU e número máximo de propriedades permitidas pela Warm Store para PAYG SKU|Nenhuma|
|Propriedades De Armazenamento Quente|Propriedades usadas de armazenamento quente |Contagem|Máximo|Número de propriedades utilizadas pelo ambiente para S1/S2 SKU e número de propriedades utilizadas pela Warm Store para PAYG SKU|Nenhuma|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Discos Ler Bytes/Sec|BytesPerSecond|Média|Produção média de disco devido a operações de leitura durante o período da amostra.|Nenhuma|
|DiskWriteBytesPerSecond|Discos Write Bytes/Sec|BytesPerSecond|Média|Produção média de disco devido a operações de escrita durante o período da amostra.|Nenhuma|
|Discos Ler Bytes|Discos Ler Bytes|Bytes|Total|Produção total de disco devido a operações de leitura durante o período da amostra.|Nenhuma|
|Bytes de Escrita de Disco|Bytes de Escrita de Disco|Bytes|Total|Produção total de disco devido a operações de escrita durante o período da amostra.|Nenhuma|
|Operações de Leitura de Discos|Operações de leitura do disco|Contagem|Total|O número de operações de leitura da OI no período de amostras anteriores. Note que estas operações podem ser de tamanho variável.|Nenhuma|
|DiskWriteOperations|Operações de escrita de disco|Contagem|Total|O número de operações de escrita da OI no período de amostras anteriores. Note que estas operações podem ser de tamanho variável.|Nenhuma|
|Operações de leitura do disco/sec|Operações de leitura do disco/sec|CondeEmSegundo|Média|O número médio de operações de leitura de IO no período de amostras anteriores. Note que estas operações podem ser de tamanho variável.|Nenhuma|
|Operações de escrita de disco/sec|Operações de escrita de disco/sec|CondeEmSegundo|Média|O número médio de operações de escrita de IO no período de amostragem anterior. Note que estas operações podem ser de tamanho variável.|Nenhuma|
|DiskReadLatency|Latência de leitura do disco|Milissegundos|Média|Latência total de leitura. A soma do dispositivo e do núcleo lêem lálências.|Nenhuma|
|DiskWriteLatency|Latência de escrita de disco|Milissegundos|Média|Total latência de escrever. A soma do dispositivo e do núcleo escrevem látexções.|Nenhuma|
|NetworkInBytesPerSecond|Rede Em Bytes/Sec|BytesPerSecond|Média|Produção média de rede para tráfego recebido.|Nenhuma|
|NetworkOutBytesPerSecond|Rede out Bytes/Sec|BytesPerSecond|Média|Produção média de rede para tráfego transmitido.|Nenhuma|
|Entrada na Rede|Entrada na Rede|Bytes|Total|Total de entrada de rede para tráfego recebido.|Nenhuma|
|Saída da Rede|Saída da Rede|Bytes|Total|Produção total de rede para tráfego transmitido.|Nenhuma|
|MemóriaUsada|Memória Usada|Bytes|Média|A quantidade de memória da máquina que está a ser utilizada pelo VM.|Nenhuma|
|MemóriaConcedida|Memória Concedida|Bytes|Média|A quantidade de memória que foi concedida ao VM pelo anfitrião. A memória não é concedida ao hospedeiro até que seja tocada uma vez e a memória concedida pode ser trocada ou reembalizada se o VMkernel precisar da memória.|Nenhuma|
|MemoryActive|Memória Ativa|Bytes|Média|A quantidade de memória usada pelo VM no passado pequena janela de tempo. Este é o número "verdadeiro" de quanta memória o VM precisa atualmente. A memória adicional e não utilizada pode ser trocada ou balonizada sem impacto no desempenho do hóspede.|Nenhuma|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A utilização da CPU. Este valor é reportado com 100% representando todos os núcleos de processador escarnecidos do sistema. Como exemplo, um VM de 2 vias usando 50% de um sistema de quatro núcleos está completamente usando dois núcleos.|Nenhuma|
|PercentageCpuReady|CpU percentual pronto|Milissegundos|Total|O tempo de preparação é o tempo que espera que a CPU(s) fique disponível no intervalo de atualização anterior.|Nenhuma|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuPercentage|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Instância|
|Percentagem de Memória|Percentagem de Memória|Percentagem|Média|Percentagem de Memória|Instância|
|Comprimento da fila do disco|Comprimento da fila do disco|Contagem|Média|Comprimento da fila do disco|Instância|
|HttpqueueLength|Comprimento da fila http|Contagem|Média|Comprimento da fila http|Instância|
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

> [!NOTE]
> O Uso do Sistema de **Ficheiros** é uma nova métrica a ser lançada globalmente, não são esperados dados a não ser que tenha sido listado em branco para pré-visualização privada.

> [!IMPORTANT]
> **O tempo médio** de resposta será premeditado para evitar confusões com agregações métricas. Utilize o Tempo de **Resposta** como substituto.

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
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
|Conjunto de Funções de Memória|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|Conjunto de trabalho de memória média|Conjunto de trabalho médio da memória|Bytes|Média|Conjunto de trabalho médio da memória|Instância|
|HttpResponseTime|Tempo de Resposta|Segundos|Total|Tempo de Resposta|Instância|
|Tempo médio de resposta|Tempo médio de resposta (depreciado)|Segundos|Média|Tempo médio de resposta|Instância|
|AppConnections|Ligações|Contagem|Média|Ligações|Instância|
|Alças|N.º de Identificadores|Contagem|Média|N.º de Identificadores|Instância|
|Fios|Contagem de fios|Contagem|Média|Contagem de fios|Instância|
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
|TotalAppDomínios Descarregados|Total de domínios de aplicativos descarregados|Contagem|Média|Total de domínios de aplicativos descarregados|Instância|
|Gen0Collections|Coleções de lixo gen 0|Contagem|Total|Coleções de lixo gen 0|Instância|
|Gen1Collections|Coleções de lixo da Gen 1|Contagem|Total|Coleções de lixo da Gen 1|Instância|
|Gen2Collections|Coleções de lixo gen 2|Contagem|Total|Coleções de lixo gen 2|Instância|
|HealthCheckStatus|Estado do controlo de saúde|Contagem|Média|Estado do controlo de saúde|Instância|
|Utilização do Sistema de Ficheiros|Utilização do sistema de ficheiros|Bytes|Média|Utilização do sistema de ficheiros|Nenhuma|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funções)

> [!NOTE]
> O Uso do Sistema de **Ficheiros** é uma nova métrica a ser lançada globalmente, não são esperados dados a não ser que tenha sido listado em branco para pré-visualização privada.

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BytesReceived|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Dados out|Bytes|Total|Dados out|Instância|
|Http5xx|Erros do servidor http|Contagem|Total|Erros do servidor http|Instância|
|Conjunto de Funções de Memória|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|Conjunto de trabalho de memória média|Conjunto de trabalho médio da memória|Bytes|Média|Conjunto de trabalho médio da memória|Instância|
|Unidades de Execução de Funções|Unidades de Execução de Funções|MB / Milliseconds|Total|[Unidades de Execução de Funções](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instância|
|Contagem de execuções de funções|Contagem de execução de funções|Contagem|Total|Contagem de execução de funções|Instância|
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
|TotalAppDomínios Descarregados|Total de domínios de aplicativos descarregados|Contagem|Média|Total de domínios de aplicativos descarregados|Instância|
|Gen0Collections|Coleções de lixo gen 0|Contagem|Total|Coleções de lixo gen 0|Instância|
|Gen1Collections|Coleções de lixo da Gen 1|Contagem|Total|Coleções de lixo da Gen 1|Instância|
|Gen2Collections|Coleções de lixo gen 2|Contagem|Total|Coleções de lixo gen 2|Instância|
|HealthCheckStatus|Estado do controlo de saúde|Contagem|Média|Estado do controlo de saúde|Instância|
|Utilização do Sistema de Ficheiros|Utilização do sistema de ficheiros|Bytes|Média|Utilização do sistema de ficheiros|Nenhuma|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
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
|Conjunto de Funções de Memória|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|Conjunto de trabalho de memória média|Conjunto de trabalho médio da memória|Bytes|Média|Conjunto de trabalho médio da memória|Instância|
|Tempo médio de resposta|Tempo médio de resposta|Segundos|Média|Tempo médio de resposta|Instância|
|HttpResponseTime|Tempo de Resposta|Segundos|Média|Tempo de Resposta|Instância|
|Unidades de Execução de Funções|Unidades de Execução de Funções|Contagem|Total|Unidades de Execução de Funções|Instância|
|Contagem de execuções de funções|Contagem de execução de funções|Contagem|Total|Contagem de execução de funções|Instância|
|AppConnections|Ligações|Contagem|Média|Ligações|Instância|
|Alças|N.º de Identificadores|Contagem|Média|N.º de Identificadores|Instância|
|Fios|Contagem de fios|Contagem|Média|Contagem de fios|Instância|
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
|TotalAppDomínios Descarregados|Total de domínios de aplicativos descarregados|Contagem|Média|Total de domínios de aplicativos descarregados|Instância|
|Gen0Collections|Coleções de lixo gen 0|Contagem|Total|Coleções de lixo gen 0|Instância|
|Gen1Collections|Coleções de lixo da Gen 1|Contagem|Total|Coleções de lixo da Gen 1|Instância|
|Gen2Collections|Coleções de lixo gen 2|Contagem|Total|Coleções de lixo gen 2|Instância|
|HealthCheckStatus|Estado do controlo de saúde|Contagem|Média|Estado do controlo de saúde|Instância|
|Utilização do Sistema de Ficheiros|Utilização do sistema de ficheiros|Bytes|Média|Utilização do sistema de ficheiros|Nenhuma|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
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
|Tempo médio de resposta|Tempo médio de resposta|Segundos|Média|Tempo médio de resposta|Instância|
|CpuPercentage|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Instância|
|Percentagem de Memória|Percentagem de Memória|Percentagem|Média|Percentagem de Memória|Instância|
|Comprimento da fila do disco|Comprimento da fila do disco|Contagem|Média|Comprimento da fila do disco|Instância|
|HttpqueueLength|Comprimento da fila http|Contagem|Média|Comprimento da fila http|Instância|
|Pedidos Ativos|Pedidos Ativos|Contagem|Total|Pedidos Ativos|Instância|
|TotalFrontEnds|Extremidades frontais totais|Contagem|Média|Extremidades frontais totais|Nenhuma|
|Pequenas Instâncias planplana do SmallAppService|Trabalhadores do plano de serviço de aplicativos de pequena saúde|Contagem|Média|Trabalhadores do plano de serviço de aplicativos de pequena saúde|Nenhuma|
|MédiaAppServicePlanInstances|Trabalhadores do plano de serviço de aplicações médias|Contagem|Média|Trabalhadores do plano de serviço de aplicações médias|Nenhuma|
|GrandesAparições do Serviço de Serviços|Trabalhadores do grande plano de serviço de aplicações|Contagem|Média|Trabalhadores do grande plano de serviço de aplicações|Nenhuma|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TrabalhadoresTotal|Total de Trabalhadores|Contagem|Média|Total de Trabalhadores|Nenhuma|
|TrabalhadoresDisponíveis|Trabalhadores disponíveis|Contagem|Média|Trabalhadores disponíveis|Nenhuma|
|Trabalhadores utilizados|Trabalhadores usados|Contagem|Média|Trabalhadores usados|Nenhuma|
|CpuPercentage|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Instância|
|Percentagem de Memória|Percentagem de Memória|Percentagem|Média|Percentagem de Memória|Instância|
## <a name="next-steps"></a>Passos seguintes
* [Ler sobre métricas no Monitor Azure](data-platform.md)
* [Criar alertas para as métricas](alerts-overview.md)
* [Métricas de exportação para armazenamento, Hub de Eventos ou Log Analytics](platform-logs-overview.md)

