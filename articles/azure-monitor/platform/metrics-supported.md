---
title: Azure Monitor suportado métricas por tipo de recurso
description: Lista de métricas disponíveis para cada tipo de recurso com monitor Azure.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 06/16/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 7ed55200db72879f7ec7323b5c165998944688a5
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84906790"
---
# <a name="supported-metrics-with-azure-monitor"></a>Métricas suportadas com monitor Azure

> [!NOTE]
> Esta lista é gerada em grande parte automaticamente a partir da Azure Monitor Metrics REST API. Qualquer modificação feita a esta lista via GitHub pode ser escrita sem aviso prévio. Contacte o autor deste artigo para obter mais detalhes sobre como fazer atualizações permanentes.

O Azure Monitor fornece várias formas de interagir com as métricas, incluindo cartografá-las no portal, acessá-las através da API REST, ou questioná-las usando PowerShell ou CLI. 

Este artigo é uma lista completa de todas as métricas da plataforma (isto é, automaticamente recolhidas) atualmente disponíveis com o pipeline métrico consolidado do Azure Monitor. A lista foi atualizada pela última vez a 27 de março de 2020. As métricas alteradas ou adicionadas após esta data podem não aparecer abaixo. Para consultar e aceder à lista de métricas programáticamente, utilize a [versão api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Outras métricas que não constam desta lista podem estar disponíveis no portal ou usando APIs legados.

As métricas são organizadas por fornecedores de recursos e tipo de recursos. Para obter uma lista de serviços e os fornecedores de recursos que lhes pertencem, consulte [os fornecedores de recursos para os serviços Azure.](../../azure-resource-manager/management/azure-services-resource-providers.md) 


## <a name="guest-os-metrics"></a>Métricas de OS Convidados

As métricas para o sistema operativo convidado (guest os) que funciona em Azure Virtual Machines, Service Fabric e Cloud Services **NÃO** estão listadas aqui. Em vez disso, as métricas de desempenho do hóspede devem ser recolhidas através de um ou mais agentes que funcionam no ou como parte do sistema operativo convidado.  As métricas de os hóspedes incluem contadores de desempenho que acompanham a percentagem de CPU ou o uso da memória, ambos frequentemente utilizados para o dimensionamento automático ou alerta.  Utilizando a [extensão Azure Diagnostics,](diagnostics-extension-overview.md)pode enviar métricas de desempenho do guest os para a mesma base de dados onde as métricas da plataforma são armazenadas. Ele encaminha as métricas do hóspede através das [métricas personalizadas](metrics-custom-overview.md) API. Em seguida, você pode cartografar, alertar e de outra forma usar métricas de hóspedes como métricas de plataforma. Para obter mais informações, consulte [a visão geral dos agentes de monitorização.](agents-overview.md)    

## <a name="routing-platform-metrics-to-other-locations"></a>Métricas da plataforma de encaminhamento para outros locais

Pode utilizar [as definições de diagnóstico](diagnostic-settings.md) para encaminhar as métricas da plataforma para O Azure Storage, Azure Monitor Logs (e, portanto, Log Analytics) e centros de Eventos.  

Existem algumas limitações no que pode ser encaminhado e na forma em que são armazenados. 
- Nem todas as métricas são exportáveis para outros locais. Para obter uma lista de métricas de plataforma exportáveis através de definições de diagnóstico, consulte [este artigo](metrics-supported-export-diagnostic-settings.md).

- O envio de métricas multidimensionais para outros locais através de configurações de diagnóstico não é suportado atualmente. As métricas com dimensões são exportadas como métricas dimensionais simples e agregadas em valores de dimensões.
*Por exemplo*: a métrica “Mensagens Recebidas” num hub do Hub de Eventos pode ser explorada e representada ao nível da linha. No entanto, se for exportada através das definições de diagnóstico, a métrica será representada como todas as mensagens recebidas em todas as filas do hub do Hub de Eventos.


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servidores

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|qpu_metric|QPU|Contagem|Média|QPU. Intervalo 0-100 para S1, 0-200 para S2 e 0-400 para S4|ServerResourceType|
|memory_metric|Memória|Bytes|Média|Memória. Gama 0-25 GB para S1, 0-50 GB para S2 e 0-100 GB para S4|ServerResourceType|
|private_bytes_metric|Bytes privados|Bytes|Média|Bytes privados.|ServerResourceType|
|virtual_bytes_metric|Bytes Virtuais|Bytes|Média|Bytes virtuais.|ServerResourceType|
|TotalConnectionRequests|Total de pedidos de conexão|Contagem|Média|Pedidos de ligação total. Estas são as chegadas.|ServerResourceType|
|SuccessfullConnectionsPerSec|Conexões de sucesso por seg|CondePerSecond|Média|Taxa de conclusão de conexão bem sucedida.|ServerResourceType|
|TotalConnectionFailures|Falhas totais de ligação|Contagem|Média|Tentativas de ligação falhadas totais.|ServerResourceType|
|Atuais Sesessãos de 2009|Sessões de Utilizador atuais|Contagem|Média|Número atual de sessões de utilizador estabelecidas.|ServerResourceType|
|QueryPoolBusyThreads|Linhas ocupadas da piscina de consulta|Contagem|Média|Número de fios ocupados na piscina de rosca de consulta.|ServerResourceType|
|ComandoPoolJobQueueLength|Comprimento da fila de trabalho da piscina de comando|Contagem|Média|Número de empregos na fila da piscina de fios de comando.|ServerResourceType|
|ProcessamentoPoolJobQueueLength|Processamento de comprimento da fila de trabalho da piscina|Contagem|Média|Número de trabalhos não-I/O na fila da piscina de rosca de processamento.|ServerResourceType|
|Correntes Deconhecões|Ligação: Ligações atuais|Contagem|Média|Número atual de ligações ao cliente estabelecidas.|ServerResourceType|
|CleanerCurrentPrice|Memória: Preço corrente mais limpo|Contagem|Média|Preço atual da memória, $/byte/time, normalizado para 1000.|ServerResourceType|
|LimpadorMesshrinkable|Memória: Memória mais limpa encolhível|Bytes|Média|Quantidade de memória, em bytes, sujeito a purga pelo limpador de fundos.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memória: Memória limpa não é insuportável|Bytes|Média|Quantidade de memória, em bytes, não sujeita a purga pelo limpador de fundos.|ServerResourceType|
|MemóriaSage|Memória: Utilização da memória|Bytes|Média|Utilização da memória do processo do servidor como usado no cálculo do preço de memória mais limpo. Igual a contrariar o Processo\PrivateBytes mais o tamanho dos dados mapeados pela memória, ignorando qualquer memória que tenha sido mapeada ou atribuída pelo motor de análise em memória xVelocity (VertiPaq) em excesso do limite de memória do motor xVelocity.|ServerResourceType|
|MemoryLimitHard|Memória: Limite de memória difícil|Bytes|Média|Limite de memória rígido, a partir do ficheiro de configuração.|ServerResourceType|
|MemóriaLimithigh|Memória: Limite de memória Alto|Bytes|Média|Limite de memória elevado, a partir do ficheiro de configuração.|ServerResourceType|
|MemoryLimitLow|Memória: Limite de memória baixo|Bytes|Média|Limite de memória baixo, a partir do ficheiro de configuração.|ServerResourceType|
|MemoryLimitVertiPaq|Memória: Limite de memória VertiPaq|Bytes|Média|Limite de memória, a partir do ficheiro de configuração.|ServerResourceType|
|Quota|Memória: Quota|Bytes|Média|Quota de memória atual, em bytes. A quota de memória também é conhecida como um subsídio de memória ou reserva de memória.|ServerResourceType|
|Quota Bloqueada|Memória: Quota Bloqueada|Contagem|Média|Número atual de pedidos de quotas que são bloqueados até que outras quotas de memória sejam libertadas.|ServerResourceType|
|VertiPaqNonpaged|Memória: VertiPaq Não Pageed|Bytes|Média|Bytes de memória bloqueados no conjunto de trabalho para utilização pelo motor de memória.|ServerResourceType|
|VertiPaqPaged|Memória: VertiPaq Paged|Bytes|Média|Bytes de memória paged em uso para dados de memória.|ServerResourceType|
|RowsReadPerSec|Processamento: Linhas lidas por seg|CondePerSecond|Média|Taxa de linhas lidas de todas as bases de dados relacionais.|ServerResourceType|
|RowsConvertedPerSec|Processamento: Linhas convertidas por seg|CondePerSecond|Média|Taxa de linhas convertidas durante o processamento.|ServerResourceType|
|RowsWrittenPerSec|Processamento: Linhas escritas por seg|CondePerSecond|Média|Taxa de linhas escritas durante o processamento.|ServerResourceType|
|CommandPoolBusyThreads|Threads: Linhas movimentadas da piscina de comando|Contagem|Média|Número de fios ocupados na piscina de fio de comando.|ServerResourceType|
|CommandPoolIdleThreads|Fios: Fios inativos da piscina de comando|Contagem|Média|Número de fios inativos na piscina do fio de comando.|ServerResourceType|
|LongParsingBusyThreads|Threads: Longas análises de fios ocupados|Contagem|Média|Número de fios ocupados na piscina de rosca de análise longa.|ServerResourceType|
|LongParsingIdleThreads|Threads: Fios longos de análise ocioso|Contagem|Média|Número de fios ociosos na piscina de rosca de análise longa.|ServerResourceType|
|LongParsingJobQueueLength|Threads: Comprimento da fila de trabalho de análise longa|Contagem|Média|Número de empregos na fila da piscina de rosca de longas análises.|ServerResourceType|
|ProcessamentoPoolBusyIOJobThreads|Threads: Processamento de piscina ocupada linhas de trabalho de I/O|Contagem|Média|Número de fios a executar trabalhos de E/S na piscina de fios de processamento.|ServerResourceType|
|ProcessamentoPoolBusyNonIOThreads|Fios: Piscina de processamento ocupada fios não-I/O|Contagem|Média|Número de fios que executam trabalhos não-I/S na piscina de rosca de processamento.|ServerResourceType|
|ProcessamentoPoolIOJobQueueLength|Threads: Processamento de piscina I/O comprimento da fila de trabalho|Contagem|Média|Número de trabalhos de E/S na fila da piscina de rosca de processamento.|ServerResourceType|
|ProcessamentoPoolIdleIOJobThreads|Fios: Processamento de linhas de trabalho I/O inativas da piscina|Contagem|Média|Número de fios inativos para trabalhos de E/S na piscina de rosca de processamento.|ServerResourceType|
|ProcessamentoPoolIdleNonIOThreads|Fios: Processamento de piscinas ociosas fios não-I/O|Contagem|Média|Número de fios inativos na piscina de fios de processamento dedicada a trabalhos não-I/S.|ServerResourceType|
|QueryPoolIdleThreads|Fios: Linhas ociosas da piscina de consulta|Contagem|Média|Número de fios inativos para trabalhos de E/S na piscina de rosca de processamento.|ServerResourceType|
|QueryPoolJobQueueLength|Threads: Consulta de trabalho na piscina fila lengt|Contagem|Média|Número de empregos na fila da piscina de fios de consulta.|ServerResourceType|
|ShortParsingBusyThreads|Threads: Threads de análise curta|Contagem|Média|Número de fios ocupados na piscina de rosca de parsing curta.|ServerResourceType|
|ShortParsingIdleThreads|Fios: Fios curtos de parsing ocioso|Contagem|Média|Número de fios ociosos na piscina de rosca de parsing curta.|ServerResourceType|
|ShortParsingJobQueueLength|Threads: Comprimento da fila do trabalho de análise curta|Contagem|Média|Número de empregos na fila da piscina de rosca de pequena análise.|ServerResourceType|
|memory_thrashing_metric|Degradação de Memória|Percentagem|Média|Memória média a bater.|ServerResourceType|
|mashup_engine_qpu_metric|QPU do motor M|Contagem|Média|Utilização do QPU por processos de motor de mashup|ServerResourceType|
|mashup_engine_memory_metric|Memória do motor M|Bytes|Média|Utilização da memória por processos do motor de mashup|ServerResourceType|
|mashup_engine_private_bytes_metric|M Motor Private Bytes|Bytes|Média|Bytes privados utiliza-se através de processos de motor de mashup.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M Motor Virtual Bytes|Bytes|Média|Uso de bytes virtuais por processos de motor de mashup.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|TotalRequests|Total de pedidos de gateway (precotado)|Contagem|Total|Número de pedidos de gateway - Use métrica de pedido de várias dimensões com dimensão GatewayResponseCodeCategoria|Localização,Nome anfitrião|
|Requess de sucesso|Pedidos de Gateway bem sucedidos (Deprecados)|Contagem|Total|Número de pedidos de gateway bem sucedidos - Use a métrica de pedido de várias dimensões com a dimensão GatewayResponseCodeCategoria|Localização,Nome anfitrião|
|Requessionais não autorizados|Pedidos de Gateway não autorizados (Deprecados)|Contagem|Total|Número de pedidos de gateway não autorizados - Use a métrica de pedido de várias dimensões com a dimensão GatewayResponseCodeCategoria|Localização,Nome anfitrião|
|Requessos Falhados|Pedidos de Gateway falhados (Deprecados)|Contagem|Total|Número de falhas nos pedidos de gateway - Use a métrica de pedido de várias dimensões com a dimensão GatewayResponseCodeCategoria|Localização,Nome anfitrião|
|Outros Requests|Outros pedidos de gateway (precotados)|Contagem|Total|Número de outros pedidos de gateway - Use a métrica de pedido de várias dimensões com a dimensão GatewayResponseCodeCategoria|Localização,Nome anfitrião|
|Duração|Duração geral dos pedidos de gateway|Milissegundos|Média|Duração global dos pedidos gateway em milissegundos|Localização,Nome anfitrião|
|BackendDuration|Duração dos Pedidos de Backend|Milissegundos|Média|Duração dos Pedidos de Backend em milissegundos|Localização,Nome anfitrião|
|Capacidade|Capacidade|Percentagem|Média|Métrica de utilização para o serviço ApiManagement|Localização|
|EventHubTotalEvents|Total de eventos EventHub|Contagem|Total|Número de eventos enviados para o EventHub|Localização|
|EventoSHubSuccessfulEvents|Eventos de sucesso EventHub|Contagem|Total|Número de eventos de sucesso do EventHub|Localização|
|EventHubTotalFailedEvents|Eventos falhados do EventHub|Contagem|Total|Número de eventos falhados do EventHub|Localização|
|EventosHubRejectedEvents|Eventos de EventHub rejeitados|Contagem|Total|Número de eventos rejeitados do EventHub (configuração errada ou não autorizado)|Localização|
|EventHubThrottledEvents|Eventos Throttled EventHub|Contagem|Total|Número de eventos acelerados eventHub|Localização|
|EventHubTimedoutEvents|Eventos Timed out EventHub|Contagem|Total|Número de eventos do EventHub cronometrado|Localização|
|EventHubDroppedEvents|Eventos do EventHub abandonados|Contagem|Total|Número de eventos ignorados devido ao limite de tamanho da fila atingido|Localização|
|EventHubTotalBytesSent|Tamanho dos eventos Do EventHub|Bytes|Total|Tamanho total dos eventos EventHub em bytes|Localização|
|Pedidos|Pedidos|Contagem|Total|Métricas de pedido de gateway com múltiplas dimensões|Localização,Nome anfitrião,LastErrorReason,BackendResponseCode,GatewayResponseCode,BackendResponseCodeCategory,GatewayResponseCodeCategory,GatewayResponseCodeCategory|
|RedeContividade|Estado da conectividade da rede dos recursos (pré-visualização)|Contagem|Total|Estado de conectividade da rede dos tipos de recursos dependentes do serviço de Gestão API|Localização,Tip de Recursos|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Contagem|Contagem|Número total de pedidos de http recebidas.|Código de Estado|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Contagem|Média|Latência num pedido http.|Código de Estado|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/primavera

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage|Percentagem de Utilização cpu do sistema|Percentagem|Média|O recente uso do CPU para todo o sistema|AppName,Pod|
|AppCpuUsagePercentage|Percentagem de Utilização cpu de aplicação|Percentagem|Média|App JVM CPU Percentagem de Utilização|AppName,Pod|
|AppMemoryCommitted|Memória de aplicativo atribuída|Bytes|Média|Memória atribuída a JVM em bytes|AppName,Pod|
|AppMemoryUsed|Memória de aplicativo usada|Bytes|Média|Memória de aplicativos utilizada em bytes|AppName,Pod|
|AppMemoryMax|App Memory Max|Bytes|Máximo|A quantidade máxima de memória em bytes que pode ser usada para a gestão da memória|AppName,Pod|
|MaxOldGenMemoryPoolBytes|Tamanho máximo de dados de geração antiga disponível|Bytes|Média|Tamanho máximo da piscina de memória de geração antiga|AppName,Pod|
|OldGenMemoryPoolBytes|Tamanho dos dados da geração velha|Bytes|Média|Tamanho da piscina de memória de geração velha depois de um GC completo|AppName,Pod|
|OldGenPromotedBytes|Promover para o tamanho dos dados da geração velha|Bytes|Máximo|Contagem de aumentos positivos no tamanho do pool de memória de geração antiga antes da GC para depois de GC|AppName,Pod|
|YoungGenPromotedBytes|Promover para o tamanho de dados de geração jovem|Bytes|Máximo|Incrementado para um aumento no tamanho do pool de memória de geração jovem após um GC para antes do próximo|AppName,Pod|
|GCPauseTotalCount|Contagem de pausas GC|Contagem|Total|Contagem de pausas GC|AppName,Pod|
|GCPauseTotalTime|GC Pausa Tempo Total|Milissegundos|Total|GC Pausa Tempo Total|AppName,Pod|
|TomcatSentBytes|Tomcat Total Enviado Bytes|Bytes|Total|Tomcat Total Enviado Bytes|AppName,Pod|
|TomcatReceivedBytes|Tomcat Total Recebido Bytes|Bytes|Total|Tomcat Total Recebido Bytes|AppName,Pod|
|TomcatRequestTotalTime|Pedido tomcat tempos totais|Milissegundos|Total|Pedido tomcat tempos totais|AppName,Pod|
|TomcatRequestTotalCount|Tomcat Request Contagem Total|Contagem|Total|Tomcat Request Contagem Total|AppName,Pod|
|TomcatResponseAvgTime|Tomcat Pede tempo médio|Milissegundos|Média|Tomcat Pede tempo médio|AppName,Pod|
|TomcatRequestMaxTime|Tomcat Request Max Time|Milissegundos|Máximo|Tomcat Request Max Time|AppName,Pod|
|TomcatErrorCount|Erro Global de Tomcat|Contagem|Total|Erro Global de Tomcat|AppName,Pod|
|TomcatSessionActiveMaxCount|Tomcat Session Max Ative Count|Contagem|Total|Tomcat Session Max Ative Count|AppName,Pod|
|TomcatSessionAliveMaxTime|Tomcat Session Max Alive Tempo|Milissegundos|Máximo|Tomcat Session Max Alive Tempo|AppName,Pod|
|TomcatSessionActiveCurrentCount|Tomcat Session Alive Count|Contagem|Total|Tomcat Session Alive Count|AppName,Pod|
|TomcatSessionCreatedCount|Tomcat Session Criou Contagem|Contagem|Total|Tomcat Session Criou Contagem|AppName,Pod|
|TomcatSessionExpiredCount|Contagem expirada da sessão de Tomcat|Contagem|Total|Contagem expirada da sessão de Tomcat|AppName,Pod|
|TomcatSessionRejectedCount|Contagem rejeitada da sessão de Tomcat|Contagem|Total|Contagem rejeitada da sessão de Tomcat|AppName,Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automation

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|TotalJob|Total de empregos|Contagem|Total|O número total de empregos|Runbook,Status|
|TotalUpdateDeploymentRuns|Execuções de implementação de atualização total|Contagem|Total|Total de execuções de atualização de software executa|SoftwareUpdateConfigurationName,Status|
|TotalUpdateDeploymentMachineRuns|Funciona a máquina de implementação total de atualizações|Contagem|Total|A máquina de implementação total de atualização de software é executada numa implementação de atualização de software|SoftwareUpdateConfigurationName,Status,TargetComputer,SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batcontas ch/batch

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|CoreCount|Contagem de núcleos dedicada|Contagem|Total|Número total de núcleos dedicados na conta do lote|Nenhum|
|TotalNodeCount|Conde de Nó dedicado|Contagem|Total|Número total de nós dedicados na conta do lote|Nenhum|
|LowPriorityCoreCount|Contagem do Núcleo de BaixaPrioridade|Contagem|Total|Número total de núcleos de baixa prioridade na conta do lote|Nenhum|
|TotalLowPriorityNodeCount|Contagem de nó de baixa prioridade|Contagem|Total|Número total de nós de baixa prioridade na conta do lote|Nenhum|
|Criação NodeCount|Criação de Contagem de Nó|Contagem|Total|Número de nós a ser criado|Nenhum|
|InícioNodeCount|Contagem inicial do nó|Contagem|Total|Número de nós a partir|Nenhum|
|WaitingForStartTaskNodeCount|À espera da contagem de nó de tarefa inicial|Contagem|Total|Número de nós à espera que a Tarefa inicial esteja concluída|Nenhum|
|StartTaskFailedNodeCount|Início da contagem de nó falhado da tarefa|Contagem|Total|Número de nós onde a Tarefa inicial falhou|Nenhum|
|IdleNodeCount|Conde nó inativo|Contagem|Total|Número de nós ociosos|Nenhum|
|OfflineNodeCount|Contagem de nóles offline|Contagem|Total|Número de nós offline|Nenhum|
|RebootingNodeCount|Contagem de nó de reinicialização|Contagem|Total|Número de nós reiniciantes|Nenhum|
|ReimagingNodeCount|Contagem de nó de reimaging|Contagem|Total|Número de nós de reimaging|Nenhum|
|RunningNodeCount|Contagem de nó de corrida|Contagem|Total|Número de nós em execução|Nenhum|
|LeavingPoolNodeCount|Deixando a contagem do nó de piscina|Contagem|Total|Número de nós que saem da Piscina|Nenhum|
|Não utilizávelNodeCount|Contagem de nó inutilizáveis|Contagem|Total|Número de nós inutilizáveis|Nenhum|
|PreemptedNodeCount|Contagem de nódoaista preventiva|Contagem|Total|Número de nós pré-emitidos|Nenhum|
|TaskStartEvent|Eventos de início de tarefa|Contagem|Total|Número total de tarefas que começaram|poolId,jobId|
|TaskCompleteEvent|Eventos completos de tarefas|Contagem|Total|Número total de tarefas que completaram|poolId,jobId|
|TaskFailEvent|Eventos de Falha de Tarefa|Contagem|Total|Número total de tarefas que foram concluídas num estado falhado|poolId,jobId|
|Evento PoolCreate|Criar Eventos de Criação de Piscinas|Contagem|Total|Número total de piscinas que foram criadas|poolId|
|PoolResizeStartEvent|Eventos de início de piscina redimensionam|Contagem|Total|Número total de redimensionações de piscina que começaram|poolId|
|PoolResizeCompleteEvent|Piscina Redimensione Eventos Completos|Contagem|Total|Número total de redimensionações de piscina que completaram|poolId|
|PoolDeleteStartEvent|Pool Delete Eventos iniciá-lo|Contagem|Total|Número total de eliminações de piscinas que começaram|poolId|
|PoolDeleteCompleteEvent|Piscina Eliminar Eventos Completos|Contagem|Total|Número total de eliminações de piscinas que tenham concluído|poolId|
|JobDeleteCompleteEvent|Trabalho Eliminar Eventos Completos|Contagem|Total|Número total de postos de trabalho que foram eliminados com êxito.|jobId|
|JobDeleteStartEvent|Trabalho eliminar eventos iniciá-lo|Contagem|Total|Número total de postos de trabalho que foram solicitados para serem suprimidos.|jobId|
|JobDisableCompleteEvent|Eventos completos para desativação de emprego|Contagem|Total|Número total de postos de trabalho que foram incapacitados com sucesso.|jobId|
|JobDisableStartEvent|Eventos de início de desativação de emprego|Contagem|Total|Número total de postos de trabalho que foram solicitados para serem incapacitados.|jobId|
|JobStartEvent|Eventos de início de emprego|Contagem|Total|Número total de postos de trabalho que foram iniciados com sucesso.|jobId|
|JobTerminateCompleteEvent|Emprego termina eventos completos|Contagem|Total|Número total de postos de trabalho que foram encerrados com sucesso.|jobId|
|JobTerminateStartEvent|Eventos de início de emprego terminam|Contagem|Total|Número total de postos de trabalho que foram solicitados para serem encerrados.|jobId|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/espaços de trabalho

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Trabalho submetido|Trabalho submetido|Contagem|Total|Número de postos de trabalho submetidos|Cenário,ClusterName|
|Trabalho Concluído|Trabalho Concluído|Contagem|Total|Número de postos de trabalho concluídos|Cenário,ClusterName,ResultadoType|
|Nómada Total|Nómada Total|Contagem|Média|Número de nós totais|Cenário,ClusterName|
|Nóns ativos|Nóns ativos|Contagem|Média|Número de nós em execução|Cenário,ClusterName|
|Nódoas Ociosas|Nódoas Ociosas|Contagem|Média|Número de nós ociosos|Cenário,ClusterName|
|Nódes Inutilizáveis|Nódes Inutilizáveis|Contagem|Média|Número de nós inutilizáveis|Cenário,ClusterName|
|Nómadas Preempted|Nómadas Preempted|Contagem|Média|Número de nós pré-emitidos|Cenário,ClusterName|
|Deixando os nóns|Deixando os nóns|Contagem|Média|Número de nós de saída|Cenário,ClusterName|
|Núcleos Totais|Núcleos Totais|Contagem|Média|Número de núcleos totais|Cenário,ClusterName|
|Núcleos Ativos|Núcleos Ativos|Contagem|Média|Número de núcleos ativos|Cenário,ClusterName|
|Núcleos Ociosos|Núcleos Ociosos|Contagem|Média|Número de núcleos ociosos|Cenário,ClusterName|
|Núcleos inutilizáveis|Núcleos inutilizáveis|Contagem|Média|Número de núcleos inutilizáveis|Cenário,ClusterName|
|Núcleos Preempted|Núcleos Preempted|Contagem|Média|Número de núcleos pré-apropriados|Cenário,ClusterName|
|Deixando os Núcleos|Deixando os Núcleos|Contagem|Média|Número de núcleos de saída|Cenário,ClusterName|
|Percentagem de Utilização de Cots|Percentagem de Utilização de Cots|Contagem|Média|Por cento das quotas utilizadas|Cenário,ClusterName,VmFamilyName,VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|Percentagem de Utilização cpu|Percentagem|Máximo|Percentagem de Utilização cpu|Nó|
|MemóriaSage|Utilização de Memória|Bytes|Média|Utilização de Memória|Nó|
|MemóriaLimite|Limite de memória|Bytes|Média|Limite de memória|Nó|
|MemoryUsagePercentageInDouble|Percentagem de Utilização de Memória|Percentagem|Média|Percentagem de Utilização de Memória|Nó|
|ArmazenamentoUsage|Utilização de armazenamento|Bytes|Média|Utilização de armazenamento|Nó|
|IOReadBytes|IO Ler Bytes|Bytes|Total|IO Ler Bytes|Nó|
|IOWriteBytes|IO Escrever Bytes|Bytes|Total|IO Escrever Bytes|Nó|
|LigaçãoAccepted|Conexões Aceites|Contagem|Total|Conexões Aceites|Nó|
|Conexão Tratada|Conexões manuseidas|Contagem|Total|Conexões manuseidas|Nó|
|ConexãoActiva|Ligações Ativas|Contagem|Média|Ligações Ativas|Nó|
|Pedido Tratado|Pedidos Tratados|Contagem|Total|Pedidos Tratados|Nó|
|Blocos Processados|Blocos processados|Contagem|Total|Blocos processados|Nó|
|Transações processadas|Transações Processadas|Contagem|Total|Transações Processadas|Nó|
|Processos pendentes|Transações Pendentes|Contagem|Média|Transações Pendentes|Nó|
|QueuedTransacções|Transações em fila|Contagem|Média|Transações em fila|Nó|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|connectedclients|Clientes Ligados|Contagem|Máximo||ShardId|
|totalcommands processado|Total de Operações|Contagem|Total||ShardId|
|cachehits|Acertos na Cache|Contagem|Total||ShardId|
|cachemisses|Falhas de Acerto na Cache|Contagem|Total||ShardId|
|cachemissrate|Taxa de Falha cache|Percentagem|cachemissrate||ShardId|
|obter commões|Obtenções|Contagem|Total||ShardId|
|setcommands|Definições|Contagem|Total||ShardId|
|operaçõesPerSecond|Operações por Segundo|Contagem|Máximo||ShardId|
|chaves despejadas|Chaves Excluídas|Contagem|Total||ShardId|
|totalkeys|Chaves totais|Contagem|Máximo||ShardId|
|chaves caducadas|Chaves Expiradas|Contagem|Total||ShardId|
|usedmemory|Memória Utilizada|Bytes|Máximo||ShardId|
|usuário|Percentagem de Memória Utilizada|Percentagem|Máximo||ShardId|
|usedmemoryRss|RSS de memória usada|Bytes|Máximo||ShardId|
|servidorAcar|Carga do Servidor|Percentagem|Máximo||ShardId|
|cacheDite|Escrita na Cache|BytesPerSecond|Máximo||ShardId|
|cacheRead|Leitura da Cache|BytesPerSecond|Máximo||ShardId|
|percentProcessorTime|CPU|Percentagem|Máximo||ShardId|
|cacheLatency|Microsegundos de latência de cache (pré-visualização)|Contagem|Média||ShardId|
|erros|Erros|Contagem|Máximo||ShardId,ErrorType|
|connectedclients0|Clientes Conectados (Fragmento 0)|Contagem|Máximo||Nenhum|
|totalcommands processado0|Total de operações (Fragmento 0)|Contagem|Total||Nenhum|
|cachehits0|Cache Hits (Fragmento 0)|Contagem|Total||Nenhum|
|cachemisses0|Cache Misses (Fragmento 0)|Contagem|Total||Nenhum|
|obter commands0|Recebe (Fragmento 0)|Contagem|Total||Nenhum|
|setcommands0|Conjuntos (Fragmento 0)|Contagem|Total||Nenhum|
|operaçõesPerSecond0|Operações por segundo (Fragmento 0)|Contagem|Máximo||Nenhum|
|despejadokeys0|Chaves despejadas (Fragmento 0)|Contagem|Total||Nenhum|
|totalkeys0|Chaves totais (Fragmento 0)|Contagem|Máximo||Nenhum|
|40keys0|Chaves expiradas (Fragmento 0)|Contagem|Total||Nenhum|
|usadomemory0|Memória Usada (Fragmento 0)|Bytes|Máximo||Nenhum|
|usedmemoryRss0|RSS de memória usado (fragmento 0)|Bytes|Máximo||Nenhum|
|servidorLoad0|Carga do servidor (Fragmento 0)|Percentagem|Máximo||Nenhum|
|cacheWrite0|Cache Write (Fragmento 0)|BytesPerSecond|Máximo||Nenhum|
|cacheRead0|Cache Read (Fragmento 0)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime0|CPU (Fragmento 0)|Percentagem|Máximo||Nenhum|
|connectedclients1|Clientes Conectados (Fragmento 1)|Contagem|Máximo||Nenhum|
|totalcommandsprocessado1|Total de operações (Fragmento 1)|Contagem|Total||Nenhum|
|cachehits1|Cache Hits (Fragmento 1)|Contagem|Total||Nenhum|
|cachemisses1|Cache Misses (Fragmento 1)|Contagem|Total||Nenhum|
|obter commands1|Recebe (Fragmento 1)|Contagem|Total||Nenhum|
|setcommands1|Conjuntos (Fragmento 1)|Contagem|Total||Nenhum|
|operaçõesPerSecond1|Operações por segundo (Fragmento 1)|Contagem|Máximo||Nenhum|
|chaves despejadas1|Chaves despejadas (Fragmento 1)|Contagem|Total||Nenhum|
|totalkeys1|Chaves totais (Fragmento 1)|Contagem|Máximo||Nenhum|
|400keys validade1|Chaves expiradas (Fragmento 1)|Contagem|Total||Nenhum|
|usedmemory1|Memória Usada (Fragmento 1)|Bytes|Máximo||Nenhum|
|usedmemoryRss1|RSS de memória usado (fragmento 1)|Bytes|Máximo||Nenhum|
|servidorLoad1|Carga do servidor (Fragmento 1)|Percentagem|Máximo||Nenhum|
|cacheWrite1|Cache Write (Fragmento 1)|BytesPerSecond|Máximo||Nenhum|
|cacheRead1|Cache Read (Fragmento 1)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime1|CPU (Fragmento 1)|Percentagem|Máximo||Nenhum|
|connectedclients2|Clientes Conectados (Fragmento 2)|Contagem|Máximo||Nenhum|
|totalcommands processado2|Total de Operações (Fragmento 2)|Contagem|Total||Nenhum|
|cachehits2|Cache Hits (Fragmento 2)|Contagem|Total||Nenhum|
|cachemisses2|Cache Misses (Fragmento 2)|Contagem|Total||Nenhum|
|obter commands2|Recebe (Fragmento 2)|Contagem|Total||Nenhum|
|setcommands2|Conjuntos (Fragmento 2)|Contagem|Total||Nenhum|
|operaçõesPerSecond2|Operações por segundo (Fragmento 2)|Contagem|Máximo||Nenhum|
|despejadokeys2|Chaves despejadas (Fragmento 2)|Contagem|Total||Nenhum|
|totalkeys2|Chaves totais (Fragmento 2)|Contagem|Máximo||Nenhum|
|400keys expirados2|Chaves expiradas (Fragmento 2)|Contagem|Total||Nenhum|
|usedmemory2|Memória Usada (Fragmento 2)|Bytes|Máximo||Nenhum|
|usedmemoryRss2|RSS de memória usado (fragmento 2)|Bytes|Máximo||Nenhum|
|servidorLoad2|Carga do servidor (Fragmento 2)|Percentagem|Máximo||Nenhum|
|cacheWrite2|Cache Write (Fragmento 2)|BytesPerSecond|Máximo||Nenhum|
|cacheRead2|Cache Read (Fragmento 2)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime2|CPU (Fragmento 2)|Percentagem|Máximo||Nenhum|
|connectedclients3|Clientes Conectados (Fragmento 3)|Contagem|Máximo||Nenhum|
|totalcommands processado3|Total de operações (Fragmento 3)|Contagem|Total||Nenhum|
|cachehits3|Cache Hits (Fragmento 3)|Contagem|Total||Nenhum|
|cachemisses3|Cache Misses (Fragmento 3)|Contagem|Total||Nenhum|
|obter commands3|Recebe (Fragmento 3)|Contagem|Total||Nenhum|
|setcommands3|Conjuntos (Fragmento 3)|Contagem|Total||Nenhum|
|operaçõesPerSecond3|Operações por segundo (Fragmento 3)|Contagem|Máximo||Nenhum|
|despejadokeys3|Chaves despejadas (Fragmento 3)|Contagem|Total||Nenhum|
|totalkeys3|Chaves totais (Fragmento 3)|Contagem|Máximo||Nenhum|
|400keys3|Chaves expiradas (Fragmento 3)|Contagem|Total||Nenhum|
|usadomemory3|Memória Usada (Fragmento 3)|Bytes|Máximo||Nenhum|
|usedmemoryRss3|RSS de memória usado (fragmento 3)|Bytes|Máximo||Nenhum|
|servidorLoad3|Carga do servidor (Fragmento 3)|Percentagem|Máximo||Nenhum|
|cacheWrite3|Cache Write (Fragmento 3)|BytesPerSecond|Máximo||Nenhum|
|cacheRead3|Cache Read (Fragmento 3)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime3|CPU (Fragmento 3)|Percentagem|Máximo||Nenhum|
|connectedclients4|Clientes Conectados (Fragmento 4)|Contagem|Máximo||Nenhum|
|totalcommandsprocessado4|Total de operações (Fragmento 4)|Contagem|Total||Nenhum|
|cachehits4|Cache Hits (Fragmento 4)|Contagem|Total||Nenhum|
|cachemisses4|Cache Misses (Fragmento 4)|Contagem|Total||Nenhum|
|obter commands4|Recebe (Fragmento 4)|Contagem|Total||Nenhum|
|setcommands4|Conjuntos (Fragmento 4)|Contagem|Total||Nenhum|
|operaçõesPerSecond4|Operações por segundo (Fragmento 4)|Contagem|Máximo||Nenhum|
|despejadokeys4|Chaves despejadas (Fragmento 4)|Contagem|Total||Nenhum|
|totalkeys4|Chaves totais (Fragmento 4)|Contagem|Máximo||Nenhum|
|4keys expirados|Chaves expiradas (Fragmento 4)|Contagem|Total||Nenhum|
|usadomemory4|Memória Usada (Fragmento 4)|Bytes|Máximo||Nenhum|
|usedmemoryRss4|RSS de memória usado (fragmento 4)|Bytes|Máximo||Nenhum|
|servidorLoad4|Carga do servidor (Fragmento 4)|Percentagem|Máximo||Nenhum|
|cacheWrite4|Cache Write (Fragmento 4)|BytesPerSecond|Máximo||Nenhum|
|cacheRead4|Cache Read (Fragmento 4)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime4|CPU (Fragmento 4)|Percentagem|Máximo||Nenhum|
|connectedclients5|Clientes Conectados (Fragmento 5)|Contagem|Máximo||Nenhum|
|totalcommands processado5|Total de operações (Fragmento 5)|Contagem|Total||Nenhum|
|cachehits5|Cache Hits (Fragmento 5)|Contagem|Total||Nenhum|
|cachemisses5|Cache Misses (Fragmento 5)|Contagem|Total||Nenhum|
|obter commands5|Recebe (Fragmento 5)|Contagem|Total||Nenhum|
|setcommands5|Conjuntos (Fragmento 5)|Contagem|Total||Nenhum|
|operaçõesPerSecond5|Operações por segundo (Fragmento 5)|Contagem|Máximo||Nenhum|
|despejadokeys5|Chaves despejadas (Fragmento 5)|Contagem|Total||Nenhum|
|totalkeys5|Chaves totais (Fragmento 5)|Contagem|Máximo||Nenhum|
|400keys5|Chaves expiradas (Fragmento 5)|Contagem|Total||Nenhum|
|usadomemory5|Memória Usada (Fragmento 5)|Bytes|Máximo||Nenhum|
|usedmemoryRss5|RSS de memória usado (fragmento 5)|Bytes|Máximo||Nenhum|
|servidorLoad5|Carga do servidor (Fragmento 5)|Percentagem|Máximo||Nenhum|
|cacheWrite5|Cache Write (Fragmento 5)|BytesPerSecond|Máximo||Nenhum|
|cacheRead5|Cache Read (Fragmento 5)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime5|CPU (Fragmento 5)|Percentagem|Máximo||Nenhum|
|connectedclients6|Clientes Conectados (Fragmento 6)|Contagem|Máximo||Nenhum|
|totalcommands processado6|Total de operações (Fragmento 6)|Contagem|Total||Nenhum|
|cachehits6|Cache Hits (Fragmento 6)|Contagem|Total||Nenhum|
|cachemisses6|Cache Misses (Fragmento 6)|Contagem|Total||Nenhum|
|obter commands6|Recebe (Fragmento 6)|Contagem|Total||Nenhum|
|setcommands6|Conjuntos (Fragmento 6)|Contagem|Total||Nenhum|
|operaçõesPerSecond6|Operações por segundo (Fragmento 6)|Contagem|Máximo||Nenhum|
|despejadokeys6|Chaves despejadas (Fragmento 6)|Contagem|Total||Nenhum|
|totalkeys6|Chaves totais (Fragmento 6)|Contagem|Máximo||Nenhum|
|400keys6|Chaves expiradas (Fragmento 6)|Contagem|Total||Nenhum|
|usadomemory6|Memória Usada (Fragmento 6)|Bytes|Máximo||Nenhum|
|usedmemoryRss6|RSS de memória usado (fragmento 6)|Bytes|Máximo||Nenhum|
|servidorLoad6|Carga do servidor (Fragmento 6)|Percentagem|Máximo||Nenhum|
|cacheWrite6|Cache Write (Fragmento 6)|BytesPerSecond|Máximo||Nenhum|
|cacheRead6|Cache Read (Fragmento 6)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime6|CPU (Fragmento 6)|Percentagem|Máximo||Nenhum|
|connectedclients7|Clientes Conectados (Fragmento 7)|Contagem|Máximo||Nenhum|
|totalcommands processado7|Total de operações (Fragmento 7)|Contagem|Total||Nenhum|
|cachehits7|Cache Hits (Fragmento 7)|Contagem|Total||Nenhum|
|cachemisses7|Cache Misses (Fragmento 7)|Contagem|Total||Nenhum|
|obter commands7|Recebe (Fragmento 7)|Contagem|Total||Nenhum|
|setcommands7|Conjuntos (Fragmento 7)|Contagem|Total||Nenhum|
|operaçõesPerSecond7|Operações por segundo (Fragmento 7)|Contagem|Máximo||Nenhum|
|despejadokeys7|Chaves despejadas (Fragmento 7)|Contagem|Total||Nenhum|
|totalkeys7|Chaves totais (Fragmento 7)|Contagem|Máximo||Nenhum|
|400keys validade|Chaves expiradas (Fragmento 7)|Contagem|Total||Nenhum|
|usadomemory7|Memória Usada (Fragmento 7)|Bytes|Máximo||Nenhum|
|usedmemoryRss7|RSS de memória usado (fragmento 7)|Bytes|Máximo||Nenhum|
|servidorLoad7|Carga do servidor (Fragmento 7)|Percentagem|Máximo||Nenhum|
|cacheWrite7|Cache Write (Fragmento 7)|BytesPerSecond|Máximo||Nenhum|
|cacheRead7|Cache Read (Fragmento 7)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime7|CPU (Fragmento 7)|Percentagem|Máximo||Nenhum|
|connectedclients8|Clientes Conectados (Fragmento 8)|Contagem|Máximo||Nenhum|
|totalcommandsprocessado8|Total de operações (Fragmento 8)|Contagem|Total||Nenhum|
|cachehits8|Cache Hits (Fragmento 8)|Contagem|Total||Nenhum|
|cachemisses8|Cache Misses (Fragmento 8)|Contagem|Total||Nenhum|
|obter commands8|Recebe (Fragmento 8)|Contagem|Total||Nenhum|
|setcommands8|Conjuntos (Fragmento 8)|Contagem|Total||Nenhum|
|operaçõesPerSecond8|Operações por segundo (Fragmento 8)|Contagem|Máximo||Nenhum|
|despejadokeys8|Chaves despejadas (Fragmento 8)|Contagem|Total||Nenhum|
|totalkeys8|Chaves totais (Fragmento 8)|Contagem|Máximo||Nenhum|
|400keys8|Chaves expiradas (Fragmento 8)|Contagem|Total||Nenhum|
|usadomemory8|Memória Usada (Fragmento 8)|Bytes|Máximo||Nenhum|
|usedmemoryRss8|RSS de memória usado (fragmento 8)|Bytes|Máximo||Nenhum|
|servidorLoad8|Carga do servidor (Fragmento 8)|Percentagem|Máximo||Nenhum|
|cacheWrite8|Cache Write (Fragmento 8)|BytesPerSecond|Máximo||Nenhum|
|cacheRead8|Cache Read (Fragmento 8)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime8|CPU (Fragmento 8)|Percentagem|Máximo||Nenhum|
|connectedclients9|Clientes Conectados (Fragmento 9)|Contagem|Máximo||Nenhum|
|totalcommandsprocessado9|Total de operações (Fragmento 9)|Contagem|Total||Nenhum|
|cachehits9|Cache Hits (Fragmento 9)|Contagem|Total||Nenhum|
|cachemisses9|Cache Misses (Fragmento 9)|Contagem|Total||Nenhum|
|obter commands9|Recebe (Fragmento 9)|Contagem|Total||Nenhum|
|setcommands9|Conjuntos (Fragmento 9)|Contagem|Total||Nenhum|
|operaçõesPerSecond9|Operações por segundo (Fragmento 9)|Contagem|Máximo||Nenhum|
|despejadokeys9|Chaves despejadas (Fragmento 9)|Contagem|Total||Nenhum|
|totalkeys9|Chaves totais (Fragmento 9)|Contagem|Máximo||Nenhum|
|expirarkeys9|Chaves expiradas (Fragmento 9)|Contagem|Total||Nenhum|
|usedmemory9|Memória Usada (Fragmento 9)|Bytes|Máximo||Nenhum|
|usedmemoryRss9|RSS de memória usado (fragmento 9)|Bytes|Máximo||Nenhum|
|servidorLoad9|Carga do servidor (Fragmento 9)|Percentagem|Máximo||Nenhum|
|cacheWrite9|Cache Write (Fragmento 9)|BytesPerSecond|Máximo||Nenhum|
|cacheRead9|Cache Read (Fragmento 9)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime9|CPU (Fragmento 9)|Percentagem|Máximo||Nenhum|




## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Contagem de pedidos de firewall de aplicação web|Contagem|Total|O número de pedidos de cliente processados pela Firewall de Aplicação Web|Nome de Política,Nome de Regras,Ação|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades de computação atribuídas que estão atualmente a ser utilizadas pela Máquina Virtual( s).|Nenhum|
|Entrada na Rede|Entrada na Rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela(s) Máquina Virtual(s) (Tráfego de Entrada).|Nenhum|
|Saída da Rede|Saída da Rede|Bytes|Total|O número de bytes em todas as interfaces de rede pela(s) Máquina Virtual(s) (Tráfego de Saída).|Nenhum|
|Discos de leitura bytes/sec|Leitura de Disco|BytesPerSecond|Média|Bytes médios lidos a partir do disco durante o período de monitorização.|Nenhum|
|Discos De Escrita Bytes/Sec|Escrita em Disco|BytesPerSecond|Média|Bytes médios escritos para o disco durante o período de monitorização.|Nenhum|
|Operações de leitura de disco/sec|Operações de leitura de disco/sec|CondePerSecond|Média|O disco lê iops.|Nenhum|
|Operações de escrita de discos/seg|Operações de escrita de discos/seg|CondePerSecond|Média|Disco escreve IOPS.|Nenhum|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades de computação atribuídas que estão atualmente a ser utilizadas pela Máquina Virtual( s).|RoleInstanceId|
|Entrada na Rede|Entrada na Rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela(s) Máquina Virtual(s) (Tráfego de Entrada).|RoleInstanceId|
|Saída da Rede|Saída da Rede|Bytes|Total|O número de bytes em todas as interfaces de rede pela(s) Máquina Virtual(s) (Tráfego de Saída).|RoleInstanceId|
|Discos de leitura bytes/sec|Leitura de Disco|BytesPerSecond|Média|Bytes médios lidos a partir do disco durante o período de monitorização.|RoleInstanceId|
|Discos De Escrita Bytes/Sec|Escrita em Disco|BytesPerSecond|Média|Bytes médios escritos para o disco durante o período de monitorização.|RoleInstanceId|
|Operações de leitura de disco/sec|Operações de leitura de disco/sec|CondePerSecond|Média|O disco lê iops.|RoleInstanceId|
|Operações de escrita de discos/seg|Operações de escrita de discos/seg|CondePerSecond|Média|Disco escreve IOPS.|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAcontas

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Capacidade Usada|Capacidade utilizada|Bytes|Média|Capacidade da conta utilizada|Nenhum|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType,GeoType,ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SucessoServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência usada pela Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType,ApiName,Autenticação|
|SucessoE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponta a ponta de pedidos bem sucedidos feitas a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Capacidade blob|Capacidade blob|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Blob da conta de armazenamento em bytes.|BlobType,Tier|
|BlobCount|Contagem de blobs|Contagem|Média|O número de Blob no serviço Blob da conta de armazenamento.|BlobType,Tier|
|Contagem de contentores|Contagem de contentores blob|Contagem|Média|O número de contentores no serviço Blob da conta de armazenamento.|Nenhum|
|Capacidade de Indexação|Capacidade de Índice|Bytes|Média|A quantidade de armazenamento utilizada pelo Índice ADLS Gen2 (Hierárquica) em bytes.|Nenhum|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType,GeoType,ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SucessoServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência usada pela Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType,ApiName,Autenticação|
|SucessoE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponta a ponta de pedidos bem sucedidos feitas a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Capacidade de Mesa|Capacidade de mesa|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço de mesa da conta de armazenamento em bytes.|Nenhum|
|MesaCount|Contagem de tabelas|Contagem|Média|O número de mesa no serviço de mesa da conta de armazenamento.|Nenhum|
|TabelaseconagemCount|Contagem de Entidades de Tabela|Contagem|Média|O número de entidades de mesa no serviço mesa da conta de armazenamento.|Nenhum|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType,GeoType,ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SucessoServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência usada pela Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType,ApiName,Autenticação|
|SucessoE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponta a ponta de pedidos bem sucedidos feitas a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAcons/fileServices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Capacidade de Ficheiros|Capacidade de Arquivo|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço de ficheiros da conta de armazenamento em bytes.|Partilha de Ficheiros|
|Contagem de ficheiros|Contagem de ficheiros|Contagem|Média|O número de ficheiros no serviço de ficheiros da conta de armazenamento.|Partilha de Ficheiros|
|FileShareCount|Contagem de partilha de ficheiros|Contagem|Média|O número de ações de ficheiros no serviço de ficheiros da conta de armazenamento.|Nenhum|
|FileShareSnapshotCount|Contagem de snapshot de partilha de ficheiros|Contagem|Média|O número de instantâneos presentes na parte do Serviço de Ficheiros da conta de armazenamento.|Partilha de Ficheiros|
|FileShareSnapshotSize|Tamanho do snapshot de partilha de arquivo|Bytes|Média|A quantidade de armazenamento utilizada pelas fotos no serviço de ficheiros da conta de armazenamento em bytes.|Partilha de Ficheiros|
|FileShareQuota|Tamanho da quota de ações de arquivo|Bytes|Média|O limite superior da quantidade de armazenamento que pode ser usado pelo Azure Files Service em bytes.|Partilha de Ficheiros|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType,GeoType,ApiName,Authentication,FileShare|
|Entrada|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação,FileShare|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação,FileShare|
|SucessoServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência usada pela Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType,ApiName,Autenticação,FileShare|
|SucessoE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponta a ponta de pedidos bem sucedidos feitas a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação,FileShare|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação,FileShare|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Capacidade de Fila|Capacidade de fila|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço de fila da conta de armazenamento em bytes.|Nenhum|
|Contagem de filas|Contagem de filas|Contagem|Média|O número de filas no serviço de fila da conta de armazenamento.|Nenhum|
|QueueMessageCount|Contagem de mensagens de fila|Contagem|Média|O número aproximado de mensagens de fila no serviço de fila da conta de armazenamento.|Nenhum|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType,GeoType,ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SucessoServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência usada pela Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType,ApiName,Autenticação|
|SucessoE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponta a ponta de pedidos bem sucedidos feitas a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/contas

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Total de Chamadas|Total de chamadas|Contagem|Total|Número total de chamadas.|ApiName,OperaçãoName,Região|
|Chamadas de sucesso|Chamadas bem sucedidas|Contagem|Total|Número de chamadas bem sucedidas.|ApiName,OperaçãoName,Região|
|TotalErrors|Total de Erros|Contagem|Total|Número total de chamadas com resposta a erros (código de resposta HTTP 4xx ou 5xx).|ApiName,OperaçãoName,Região|
|BlockedCalls|Chamadas bloqueadas|Contagem|Total|Número de chamadas que excederam a taxa ou o limite de quota.|ApiName,OperaçãoName,Região|
|ServerErrors|Erros do servidor|Contagem|Total|Número de chamadas com erro interno de serviço (código de resposta HTTP 5xx).|ApiName,OperaçãoName,Região|
|ClientErrors|Erros do Cliente|Contagem|Total|Número de chamadas com erro lateral do cliente (código de resposta HTTP 4xx).|ApiName,OperaçãoName,Região|
|DataIn|Dados em|Bytes|Total|Tamanho dos dados de entrada em bytes.|ApiName,OperaçãoName,Região|
|DataOut|Data out|Bytes|Total|Tamanho dos dados de saída em bytes.|ApiName,OperaçãoName,Região|
|Latência|Latência|MilliSeconds|Média|Latência em milissegundos.|ApiName,OperaçãoName,Região|
|TotalTokenCalls|Total de chamadas simbólicas|Contagem|Total|Número total de chamadas simbólicas.|ApiName,OperaçãoName,Região|
|CaracteresTranslatados|Caracteres Traduzidos|Contagem|Total|Número total de caracteres no pedido de texto de entrada.|ApiName,OperaçãoName,Região|
|Personagens Treinados|Personagens treinados|Contagem|Total|Número total de caracteres treinados.|ApiName,OperaçãoName,Região|
|DiscursoSSessionDuration|Duração da sessão de discurso|Segundos|Total|Duração total da sessão de discurso em segundos.|ApiName,OperaçãoName,Região|
|Total de transações|Total de Transações|Contagem|Total|Número total de transações.|Nenhum|
|Imagens Processadas|Imagens processadas|Contagem|Total| Número de Transações para processamento de imagem.|ApiName,FeatureName,Channel,Região|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades de computação atribuídas que estão atualmente a ser utilizadas pela Máquina Virtual(s)|Nenhum|
|Entrada na Rede|Rede em Faturação (Depreciada)|Bytes|Total|O número de bytes faturados recebidos em todas as interfaces de rede pela(s) Máquina Virtual(Tráfego De entrada) (Depreifed)|Nenhum|
|Saída da Rede|Network out Billable (Precotado)|Bytes|Total|O número de bytes faturados em todas as interfaces de rede pela(s) Máquina Virtual(Tráfego De saída) (Depreifed)|Nenhum|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Bytes lidos a partir de disco durante o período de monitorização|Nenhum|
|Bytes de escrita de disco|Bytes de escrita de disco|Bytes|Total|Bytes escritos para o disco durante o período de monitorização|Nenhum|
|Operações de leitura de disco/sec|Operações de leitura de disco/sec|CondePerSecond|Média|IOPS de leitura de disco|Nenhum|
|Operações de escrita de discos/seg|Operações de escrita de discos/seg|CondePerSecond|Média|IOPS de escrita de disco|Nenhum|
|Créditos CPU Remanescentes|Créditos CPU Remanescentes|Contagem|Média|Número total de créditos disponíveis para rebentar|Nenhum|
|Créditos CPU Consumidos|Créditos CPU Consumidos|Contagem|Média|Número total de créditos consumidos pela Máquina Virtual|Nenhum|
|Per Disk Read Bytes/sec|Data Disk Read Bytes/Sec [(Deprecado)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|SlotId|
|Per Disk Write Bytes/sec|Data Disk Write Bytes/Sec [(Deprecado)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização|SlotId|
|Por Operações de leitura de disco/sec|Operações de leitura de discos de dados/seg [(depreciadas)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização|SlotId|
|Por operações de escrita de disco/sec|Operações de escrita de discos de dados/seg [(depreciadas)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|SlotId|
|Por Disco QD|QD do disco [de dados (preprecado)](portal-disk-metrics-deprecation.md)](portal-disk-metrics-deprecation.md)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|SlotId|
|OS por Disco Ler Bytes/seg|Os Discos De Leitura Bytes/Sec [(Deprecado)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização para o disco de SO|Nenhum|
|OS por Disco Escrever Bytes/seg|Os Discos De Discos/Sec [(Deprecado)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização do disco OS|Nenhum|
|Operações de leitura por disco/seg|Operações de leitura de discos de OS/Sec [(Depreciadas)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização do disco de so|Nenhum|
|Os por Operações de Escrita de Disco/Sec|Operações de escrita de discos de OS/Sec [(Depreciadas)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco de so|Nenhum|
|OS por Disco QD|CO Disk QD [(precotado)](portal-disk-metrics-deprecation.md)|Contagem|Média|Profundidade da fila do disco os(ou comprimento da fila)|Nenhum|
|Data Disk Ler Bytes/seg|Data Disk Read Bytes/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|RIO LUN|
|Bytes/seg de escrita de disco de dados|Data Disk Write Bytes/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização|RIO LUN|
|Operações de leitura de discos de dados/seg|Operações de leitura de discos de dados/seg (pré-visualização)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização|RIO LUN|
|Operações de escrita de discos de dados/seg|Operações de escrita de discos de dados/seg (pré-visualização)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|RIO LUN|
|Profundidade da Fila do Disco de Dados|Profundidade da fila do disco de dados (pré-visualização)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|RIO LUN|
|Leitura de discos de OS Bytes/seg|Os Discos de Leitura de Bytes/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização para o disco de SO|Nenhum|
|Os Discos de Escrita bytes/seg|Os Discos de Escrita de Discos/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização do disco OS|Nenhum|
|Operações de leitura de disco de OS/Sec|Operações de leitura de disco de OS/Seg (Pré-visualização)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização do disco de so|Nenhum|
|Operações de escrita de discos de OS/Sec|Operações de escrita de discos de OS/Seg (Pré-visualização)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco de so|Nenhum|
|Profundidade da Fila do Disco do SO|Profundidade da fila do disco os (pré-visualização)|Contagem|Média|Profundidade da fila do disco os(ou comprimento da fila)|Nenhum|
|Fluxos de Entrada|Fluxos de Entrada|Contagem|Média|Fluxos de entrada são um número de fluxos correntes no sentido de entrada (tráfego que vai para o VM)|Nenhum|
|Fluxos de saída|Fluxos de saída|Contagem|Média|Fluxos de saída são um número de fluxos correntes no sentido de saída (tráfego que sai do VM)|Nenhum|
|Fluxos de entrada Taxa máxima de criação|Fluxos de entrada Taxa máxima de criação|CondePerSecond|Média|A taxa máxima de criação dos fluxos de entrada (tráfego que vai para o VM)|Nenhum|
|Taxa máxima de criação de fluxos de saída|Taxa máxima de criação de fluxos de saída|CondePerSecond|Média|A taxa máxima de criação dos fluxos de saída (tráfego que sai do VM)|Nenhum|
|Sucesso de leitura de cache de disco de dados premium|Sucesso de leitura de cache de disco de dados premium (pré-visualização)|Percentagem|Média|Sucesso de leitura de cache de disco de dados premium|RIO LUN|
|Cache de disco de dados premium Ler Miss|Cache de disco de dados premium Ler Miss (Pré-visualização)|Percentagem|Média|Cache de disco de dados premium Ler Miss|RIO LUN|
|Premium OS Disk Cache Ler Hit|Premium OS Disk Cache Read Hit (Pré-visualização)|Percentagem|Média|Premium OS Disk Cache Ler Hit|Nenhum|
|Premium OS Cache De Disco Ler Miss|Cache de disco premium OS Ler Miss (Pré-visualização)|Percentagem|Média|Premium OS Cache De Disco Ler Miss|Nenhum|
|Rede no total|Rede no total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela Máquina Virtual(s) (Tráfego de Entrada)|Nenhum|
|Rede Total|Rede Total|Bytes|Total|O número de bytes em todas as interfaces de rede pela(s) Máquina Virtual(s) (Tráfego de Saída)|Nenhum|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades de computação atribuídas que estão atualmente a ser utilizadas pela Máquina Virtual(s)|VMName|
|Entrada na Rede|Rede em Faturação (Depreciada)|Bytes|Total|O número de bytes faturados recebidos em todas as interfaces de rede pela(s) Máquina Virtual(Tráfego De entrada) (Depreifed)|VMName|
|Saída da Rede|Network out Billable (Precotado)|Bytes|Total|O número de bytes faturados em todas as interfaces de rede pela(s) Máquina Virtual(Tráfego De saída) (Depreifed)|VMName|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Bytes lidos a partir de disco durante o período de monitorização|VMName|
|Bytes de escrita de disco|Bytes de escrita de disco|Bytes|Total|Bytes escritos para o disco durante o período de monitorização|VMName|
|Operações de leitura de disco/sec|Operações de leitura de disco/sec|CondePerSecond|Média|IOPS de leitura de disco|VMName|
|Operações de escrita de discos/seg|Operações de escrita de discos/seg|CondePerSecond|Média|IOPS de escrita de disco|VMName|
|Créditos CPU Remanescentes|Créditos CPU Remanescentes|Contagem|Média|Número total de créditos disponíveis para rebentar|Nenhum|
|Créditos CPU Consumidos|Créditos CPU Consumidos|Contagem|Média|Número total de créditos consumidos pela Máquina Virtual|Nenhum|
|Per Disk Read Bytes/sec|Data Disk Read Bytes/Sec [(Deprecado)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|SlotId|
|Per Disk Write Bytes/sec|Data Disk Write Bytes/Sec [(Deprecado)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização|SlotId|
|Por Operações de leitura de disco/sec|Operações de leitura de discos de dados/seg [(depreciadas)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização|SlotId|
|Por operações de escrita de disco/sec|Operações de escrita de discos de dados/seg [(depreciadas)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|SlotId|
|Por Disco QD|QD do disco de dados [(precotado)](portal-disk-metrics-deprecation.md)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|SlotId|
|OS por Disco Ler Bytes/seg|Os Discos De Leitura Bytes/Sec [(Deprecado)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização para o disco de SO|Nenhum|
|OS por Disco Escrever Bytes/seg|Os Discos De Discos/Sec [(Deprecado)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização do disco OS|Nenhum|
|Operações de leitura por disco/seg|Operações de leitura de discos de OS/Sec [(Depreciadas)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização do disco de so|Nenhum|
|Os por Operações de Escrita de Disco/Sec|Operações de escrita de discos de OS/Sec [(Depreciadas)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco de so|Nenhum|
|OS por Disco QD|CO Disk QD [(precotado)](portal-disk-metrics-deprecation.md)|Contagem|Média|Profundidade da fila do disco os(ou comprimento da fila)|Nenhum|
|Data Disk Ler Bytes/seg|Data Disk Read Bytes/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|LUN,VMName|
|Bytes/seg de escrita de disco de dados|Data Disk Write Bytes/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização|LUN,VMName|
|Operações de leitura de discos de dados/seg|Operações de leitura de discos de dados/seg (pré-visualização)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização|LUN,VMName|
|Operações de escrita de discos de dados/seg|Operações de escrita de discos de dados/seg (pré-visualização)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|LUN,VMName|
|Profundidade da Fila do Disco de Dados|Profundidade da fila do disco de dados (pré-visualização)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|LUN,VMName|
|Leitura de discos de OS Bytes/seg|Os Discos de Leitura de Bytes/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização para o disco de SO|VMName|
|Os Discos de Escrita bytes/seg|Os Discos de Escrita de Discos/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização do disco OS|VMName|
|Operações de leitura de disco de OS/Sec|Operações de leitura de disco de OS/Seg (Pré-visualização)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização do disco de so|VMName|
|Operações de escrita de discos de OS/Sec|Operações de escrita de discos de OS/Seg (Pré-visualização)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco de so|VMName|
|Profundidade da Fila do Disco do SO|Profundidade da fila do disco os (pré-visualização)|Contagem|Média|Profundidade da fila do disco os(ou comprimento da fila)|VMName|
|Fluxos de Entrada|Fluxos de Entrada|Contagem|Média|Fluxos de entrada são um número de fluxos correntes no sentido de entrada (tráfego que vai para o VM)|VMName|
|Fluxos de saída|Fluxos de saída|Contagem|Média|Fluxos de saída são um número de fluxos correntes no sentido de saída (tráfego que sai do VM)|VMName|
|Fluxos de entrada Taxa máxima de criação|Fluxos de entrada Taxa máxima de criação|CondePerSecond|Média|A taxa máxima de criação dos fluxos de entrada (tráfego que vai para o VM)|VMName|
|Taxa máxima de criação de fluxos de saída|Taxa máxima de criação de fluxos de saída|CondePerSecond|Média|A taxa máxima de criação dos fluxos de saída (tráfego que sai do VM)|VMName|
|Sucesso de leitura de cache de disco de dados premium|Sucesso de leitura de cache de disco de dados premium (pré-visualização)|Percentagem|Média|Sucesso de leitura de cache de disco de dados premium|LUN,VMName|
|Cache de disco de dados premium Ler Miss|Cache de disco de dados premium Ler Miss (Pré-visualização)|Percentagem|Média|Cache de disco de dados premium Ler Miss|LUN,VMName|
|Premium OS Disk Cache Ler Hit|Premium OS Disk Cache Read Hit (Pré-visualização)|Percentagem|Média|Premium OS Disk Cache Ler Hit|VMName|
|Premium OS Cache De Disco Ler Miss|Cache de disco premium OS Ler Miss (Pré-visualização)|Percentagem|Média|Premium OS Cache De Disco Ler Miss|VMName|
|Rede no total|Rede no total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela Máquina Virtual(s) (Tráfego de Entrada)|VMName|
|Rede Total|Rede Total|Bytes|Total|O número de bytes em todas as interfaces de rede pela(s) Máquina Virtual(s) (Tráfego de Saída)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A percentagem de unidades de computação atribuídas que estão atualmente a ser utilizadas pela Máquina Virtual(s)|Nenhum|
|Entrada na Rede|Rede em Faturação (Depreciada)|Bytes|Total|O número de bytes faturados recebidos em todas as interfaces de rede pela(s) Máquina Virtual(Tráfego De entrada) (Depreifed)|Nenhum|
|Saída da Rede|Network out Billable (Precotado)|Bytes|Total|O número de bytes faturados em todas as interfaces de rede pela(s) Máquina Virtual(Tráfego De saída) (Depreifed)|Nenhum|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Bytes lidos a partir de disco durante o período de monitorização|Nenhum|
|Bytes de escrita de disco|Bytes de escrita de disco|Bytes|Total|Bytes escritos para o disco durante o período de monitorização|Nenhum|
|Operações de leitura de disco/sec|Operações de leitura de disco/sec|CondePerSecond|Média|IOPS de leitura de disco|Nenhum|
|Operações de escrita de discos/seg|Operações de escrita de discos/seg|CondePerSecond|Média|IOPS de escrita de disco|Nenhum|
|Créditos CPU Remanescentes|Créditos CPU Remanescentes|Contagem|Média|Número total de créditos disponíveis para rebentar|Nenhum|
|Créditos CPU Consumidos|Créditos CPU Consumidos|Contagem|Média|Número total de créditos consumidos pela Máquina Virtual|Nenhum|
|Per Disk Read Bytes/sec|Data Disk Read Bytes/Sec [(Deprecado)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|SlotId|
|Per Disk Write Bytes/sec|Data Disk Write Bytes/Sec [(Deprecado)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização|SlotId|
|Por Operações de leitura de disco/sec|Operações de leitura de discos de dados/seg [(depreciadas)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização|SlotId|
|Por operações de escrita de disco/sec|Operações de escrita de discos de dados/seg [(depreciadas)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|SlotId|
|Por Disco QD|QD do disco de dados [(precotado)](portal-disk-metrics-deprecation.md)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|SlotId|
|OS por Disco Ler Bytes/seg|Os Discos De Leitura Bytes/Sec [(Deprecado)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização para o disco de SO|Nenhum|
|OS por Disco Escrever Bytes/seg|Os Discos De Discos/Sec [(Deprecado)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização do disco OS|Nenhum|
|Operações de leitura por disco/seg|Operações de leitura de discos de OS/Sec [(Depreciadas)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização do disco de so|Nenhum|
|Os por Operações de Escrita de Disco/Sec|Operações de escrita de discos de OS/Sec [(Depreciadas)](portal-disk-metrics-deprecation.md)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco de so|Nenhum|
|OS por Disco QD|CO Disk QD [(precotado)](portal-disk-metrics-deprecation.md)|Contagem|Média|Profundidade da fila do disco os(ou comprimento da fila)|Nenhum|
|Data Disk Ler Bytes/seg|Data Disk Read Bytes/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|RIO LUN|
|Bytes/seg de escrita de disco de dados|Data Disk Write Bytes/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização|RIO LUN|
|Operações de leitura de discos de dados/seg|Operações de leitura de discos de dados/seg (pré-visualização)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização|RIO LUN|
|Operações de escrita de discos de dados/seg|Operações de escrita de discos de dados/seg (pré-visualização)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|RIO LUN|
|Profundidade da Fila do Disco de Dados|Profundidade da fila do disco de dados (pré-visualização)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|RIO LUN|
|Leitura de discos de OS Bytes/seg|Os Discos de Leitura de Bytes/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização para o disco de SO|Nenhum|
|Os Discos de Escrita bytes/seg|Os Discos de Escrita de Discos/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização do disco OS|Nenhum|
|Operações de leitura de disco de OS/Sec|Operações de leitura de disco de OS/Seg (Pré-visualização)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização do disco de so|Nenhum|
|Operações de escrita de discos de OS/Sec|Operações de escrita de discos de OS/Seg (Pré-visualização)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco de so|Nenhum|
|Profundidade da Fila do Disco do SO|Profundidade da fila do disco os (pré-visualização)|Contagem|Média|Profundidade da fila do disco os(ou comprimento da fila)|Nenhum|
|Fluxos de Entrada|Fluxos de Entrada|Contagem|Média|Fluxos de entrada são um número de fluxos correntes no sentido de entrada (tráfego que vai para o VM)|Nenhum|
|Fluxos de saída|Fluxos de saída|Contagem|Média|Fluxos de saída são um número de fluxos correntes no sentido de saída (tráfego que sai do VM)|Nenhum|
|Fluxos de entrada Taxa máxima de criação|Fluxos de entrada Taxa máxima de criação|CondePerSecond|Média|A taxa máxima de criação dos fluxos de entrada (tráfego que vai para o VM)|Nenhum|
|Taxa máxima de criação de fluxos de saída|Taxa máxima de criação de fluxos de saída|CondePerSecond|Média|A taxa máxima de criação dos fluxos de saída (tráfego que sai do VM)|Nenhum|
|Sucesso de leitura de cache de disco de dados premium|Sucesso de leitura de cache de disco de dados premium (pré-visualização)|Percentagem|Média|Sucesso de leitura de cache de disco de dados premium|RIO LUN|
|Cache de disco de dados premium Ler Miss|Cache de disco de dados premium Ler Miss (Pré-visualização)|Percentagem|Média|Cache de disco de dados premium Ler Miss|RIO LUN|
|Premium OS Disk Cache Ler Hit|Premium OS Disk Cache Read Hit (Pré-visualização)|Percentagem|Média|Premium OS Disk Cache Ler Hit|Nenhum|
|Premium OS Cache De Disco Ler Miss|Cache de disco premium OS Ler Miss (Pré-visualização)|Percentagem|Média|Premium OS Cache De Disco Ler Miss|Nenhum|
|Rede no total|Rede no total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela Máquina Virtual(s) (Tráfego de Entrada)|Nenhum|
|Rede Total|Rede Total|Bytes|Total|O número de bytes em todas as interfaces de rede pela(s) Máquina Virtual(s) (Tráfego de Saída)|Nenhum|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|CpuUsage|Utilização da CPU|Contagem|Média|Utilização do CPU em todos os núcleos em millicores.|nome de contentor|
|MemóriaSage|Utilização de Memória|Bytes|Média|Uso total da memória em byte.|nome de contentor|
|NetworkBytesReceivedPerSecond|Bytes de rede recebidos por segundo|Bytes|Média|Os bytes de rede recebidos por segundo.|Nenhum|
|NetworkBytesTransmittedPerSecond|Bytes de rede transmitidos por segundo|Bytes|Média|Os bytes de rede transmitidos por segundo.|Nenhum|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|TotalPullCount|Contagem total de pull|Contagem|Média|Número de tiras de imagem no total|Nenhum|
|SucessoPullCount|Contagem de pull com sucesso|Contagem|Média|Número de puxas de imagem bem-sucedidas|Nenhum|
|TotalPushCount|Contagem total de push|Contagem|Média|Número de impulsos de imagem no total|Nenhum|
|SucessoPushCount|Contagem de impulsos bem sucedida|Contagem|Média|Número de impulsos de imagem bem sucedidos|Nenhum|
|RunDuration|Duração do funcional|Milissegundos|Total|Duração da execução em milissegundos|Nenhum|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Número total de núcleos cpu disponíveis num cluster gerido|Contagem|Média|Número total de núcleos cpu disponíveis num cluster gerido|Nenhum|
|kube_node_status_allocatable_memory_bytes|Quantidade total de memória disponível num cluster gerido|Bytes|Média|Quantidade total de memória disponível num cluster gerido|Nenhum|
|kube_pod_status_ready|Número de cápsulas no estado de Ready|Contagem|Média|Número de cápsulas no estado de Ready|espaço de nome,pod|
|kube_node_status_condition|Estados para várias condições de nó|Contagem|Média|Estados para várias condições de nó|condição,estado,status2,nó|
|kube_pod_status_phase|Número de cápsulas por fase|Contagem|Média|Número de cápsulas por fase|fase,espaço de nome,pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Requestres com sucesso|Pedidos de Sucesso|Contagem|Total|Pedidos bem sucedidos feitos pelo fornecedor personalizado|HttpMethod,CallPath,StatusCode|
|Requessos Falhados|Pedidos com Falhas|Contagem|Total|Obtém os registos disponíveis para Fornecedores de Recursos Personalizados|HttpMethod,CallPath,StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|NICReadThroughput|Ler produção (rede)|BytesPerSecond|Média|A produção de leitura da interface de rede no dispositivo no período de reporte para todos os volumes no gateway.|Nome de exemplo|
|NICWriteThroughput|Escrever Produção (Rede)|BytesPerSecond|Média|A produção de produção da interface de rede no dispositivo no período de reporte para todos os volumes no gateway.|Nome de exemplo|
|CloudReadThroughputPerShare|Download cloud 'Produção' (Partilhar)|BytesPerSecond|Média|O download para Azure de uma parte durante o período de reporte.|Partilhar|
|CloudUploadThroughputPerShare|Produção de upload de nuvem (partilhar)|BytesPerSecond|Média|O envio para a Azure de uma parte durante o período de reporte.|Partilhar|
|BytesUploadedToCloudPerShare|Cloud Bytes Uploaded (Partilhar)|Bytes|Média|O número total de bytes que é enviado para Azure a partir de uma parte durante o período de reporte.|Partilhar|
|Capacidade Total|Capacidade Total|Bytes|Média|Capacidade Total|Nenhum|
|Capacidade disponível|Capacidade disponível|Bytes|Média|A capacidade disponível nos bytes durante o período de reporte.|Nenhum|
|CloudUploadThroughput|Produção de upload de nuvens|BytesPerSecond|Média|A nuvem envia para Azure durante o período de reporte.|Nenhum|
|CloudReadThroughput|Download da Cloud|BytesPerSecond|Média|A produção de download da nuvem para Azure durante o período de reporte.|Nenhum|
|BytesUploadedToCloud|Cloud Bytes Uploaded (Dispositivo)|Bytes|Média|O número total de bytes que é enviado para Azure a partir de um dispositivo durante o período de reporte.|Nenhum|
|HiperVVirtualProcessorutilização|Edge Compute - Percentagem CPU|Percentagem|Média|Por cento uso cpu|Nome de exemplo|
|Hipervmemoryutilização|Edge Compute - Utilização da Memória|Percentagem|Média|Quantidade de RAM em uso|Nome de exemplo|


## <a name="microsoftdatacatalogdatacatalogs"></a>Microsoft.DataCatalog/datacatalogs

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|AssetDistributionByClassification|Distribuição de ativos por classificação|Contagem|Total|Indica o número de ativos com uma determinada classificação atribuída, ou seja, são classificados com essa etiqueta.|Classificação,Fonte|
|AssetDistributionByStorageType|Distribuição de ativos por tipo de armazenamento|Contagem|Total|Indica o número de ativos com um determinado tipo de armazenamento.|Dispositivo de armazenamento|
|NúmeroOfAssetsWithClassificações|Número de ativos com pelo menos uma classificação|Contagem|Média|Indica o número de ativos com pelo menos uma classificação de etiqueta.|Nenhum|
|ScanCancelled|Scan cancelado|Contagem|Total|Indica o número de exames cancelados.|Nenhum|
|ScanCompleted|Digitalização Concluída|Contagem|Total|Indica o número de exames concluídos com sucesso.|Nenhum|
|ScanFailed|Scan falhou|Contagem|Total|Indica que o número de exames falhou.|Nenhum|
|ScanTimeTaken|Tempo de digitalização tomado|Segundos|Total|Indica o tempo total de digitalização em segundos.|Nenhum|
|CatalogActiveUsers|Utilizadores Ativos Diários|Contagem|Total|Número de utilizadores ativos diariamente|Nenhum|
|CatalogUsage|Distribuição de utilização por Operação|Contagem|Total|Indicar o número de operação que o utilizador faz ao catálogo, ou seja, Acesso, Pesquisa, Glossário.|Operação|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Corridas Falhadas|Corridas falhadas|Contagem|Total||pipelineName,atividadeName|
|Sucesso DeRuns|Corridas de sucesso|Contagem|Total||pipelineName,atividadeName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/fábricas

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|PipelineFailedRuns|Gasoduto falhado executa métricas|Contagem|Total||FalhaType,Nome|
|PipelineSucceededRuns|Pipeline bem sucedido executa métricas|Contagem|Total||FalhaType,Nome|
|PipelineCancelledRuns|Gasoduto cancelado executa métricas|Contagem|Total||FalhaType,Nome|
|AtividadeFailedRuns|Atividade falhada executa métricas|Contagem|Total||ActivityType,PipelineName,FailureType,Name|
|AtividadeSSucceedEdRuns|A atividade bem sucedida executa métricas|Contagem|Total||ActivityType,PipelineName,FailureType,Name|
|AtividadeCancelledRuns|Atividade cancelada executa métricas|Contagem|Total||ActivityType,PipelineName,FailureType,Name|
|TriggerFailedRuns|O gatilho falhado executa métricas|Contagem|Total||Nome,FalhaType|
|TriggerSucceeddRuns|O gatilho bem sucedido executa métricas|Contagem|Total||Nome,FalhaType|
|TriggerCancelledRuns|Gatilho cancelado executa métricas|Contagem|Total||Nome,FalhaType|
|IntegraçãoRuntimeCpuPercentage|Utilização do CPU em tempo de integração|Percentagem|Média||IntegraçãoRuntimeName,Nome de Node|
|IntegrationRuntimeAvailableMemory|Integração memória disponível|Bytes|Média||IntegraçãoRuntimeName,Nome de Node|
|IntegraçãoRuntimeAverageTaskPickupDelay|Duração da fila de tempo de execução de integração|Segundos|Média||IntegraçãoRuntimeName|
|IntegraçãoRuntimeQueueLength|Comprimento da fila de tempo de execução de integração|Contagem|Média||IntegraçãoRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|Contagem de nó disponível de integração|Contagem|Média||IntegraçãoRuntimeName|
|MaxAllowedResourceCount|Contagem máxima de entidades permitidas|Contagem|Máximo||Nenhum|
|MaxAllowedFactorySizeInGbUnits|Tamanho máximo permitido da fábrica (unidade GB)|Contagem|Máximo||Nenhum|
|Contagem de Recursos|Total de entidades contam|Contagem|Máximo||Nenhum|
|FactorySizeInGbUnits|Tamanho total da fábrica (unidade GB)|Contagem|Máximo||Nenhum|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/contas

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|JobEndedSuccess|Empregos bem sucedidos|Contagem|Total|Contagem de trabalhos bem sucedidos.|Nenhum|
|JobEndedFailure|Empregos Falhados|Contagem|Total|Contagem de trabalhos falhados.|Nenhum|
|JobEndedCancelled|Empregos Cancelados|Contagem|Total|Contagem de trabalhos cancelados.|Nenhum|
|JobAUEndedSuccess|Tempo de AU bem sucedido|Segundos|Total|Tempo total da AU para empregos bem sucedidos.|Nenhum|
|JobAUEndedFailure|Tempo AU falhado|Segundos|Total|Tempo total da AU para trabalhos falhados.|Nenhum|
|JobAUEndedCancelled|Tempo cancelado da AU|Segundos|Total|Tempo total da AU para trabalhos cancelados.|Nenhum|
|Palco jobs|Empregos em Palco|Contagem|Total|Número de empregos em cada etapa.|Nenhum|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/contas

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|TotalStorage|Armazenamento Total|Bytes|Máximo|Quantidade total de dados armazenados na conta.|Nenhum|
|DataWritten|Dados Escritos|Bytes|Total|Quantidade total de dados escritos na conta.|Nenhum|
|DataRead|Leitura de Dados|Bytes|Total|Quantidade total de dados lidos a partir da conta.|Nenhum|
|WriteRequests|Escrever Pedidos|Contagem|Total|A contagem de dados escreve pedidos para a conta.|Nenhum|
|ReadRequests|Ler Pedidos|Contagem|Total|Contagem de dados leia pedidos para a conta.|Nenhum|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/contas

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|ShareCount|Ações enviadas|Contagem|Máximo|Número de ações enviadas na conta|Nome de partilha|
|ShareSubscriptionCount|Ações Recebidas|Contagem|Máximo|Número de ações recebidas na conta|Nome de Subscrição de Partilha|
|Sucessos PartilharSynchronizations|Envio de ações instantâneas bem sucedidas|Contagem|Contagem|Número de ações enviadas instantâneas na conta|Nenhum|
|Falhas nas Sincronizações de Compartilhadas|Envio de imagens falhadas de partilha|Contagem|Contagem|Número de snapshots falhados da ação enviada na conta|Nenhum|
|SucessoShareSubscriptionSynchronizations|Fotos bem sucedidas de partilha|Contagem|Contagem|Número de ações recebidas sucedeu instantâneos na conta|Nenhum|
|FalhashareSubscriçãoSynchronizations|Fotos falhadas da partilha recebidas|Contagem|Contagem|Número de snapshots falhados da ação recebida na conta|Nenhum|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servidores

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|CPU por cento|Percentagem|Média|CPU por cento|Nenhum|
|memory_percent|Por cento da memória|Percentagem|Média|Por cento da memória|Nenhum|
|io_consumption_percent|IO por cento|Percentagem|Média|IO por cento|Nenhum|
|storage_percent|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Nenhum|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhum|
|storage_limit|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Nenhum|
|serverlog_storage_percent|Por cento de armazenamento de registo de servidor|Percentagem|Média|Por cento de armazenamento de registo de servidor|Nenhum|
|serverlog_storage_usage|Armazenamento de registo de servidor utilizado|Bytes|Média|Armazenamento de registo de servidor utilizado|Nenhum|
|serverlog_storage_limit|Limite de armazenamento de registo de servidor|Bytes|Média|Limite de armazenamento de registo de servidor|Nenhum|
|active_connections|Ligações Ativas|Contagem|Média|Ligações Ativas|Nenhum|
|connections_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Nenhum|
|seconds_behind_master|Atraso de replicação em segundos|Contagem|Máximo|Atraso de replicação em segundos|Nenhum|
|backup_storage_used|Armazenamento de backup utilizado|Bytes|Média|Armazenamento de backup utilizado|Nenhum|
|network_bytes_egress|Saída da Rede|Bytes|Total|Rede Para fora através de ligações ativas|Nenhum|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Rede Em através de ligações ativas|Nenhum|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servidores

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|CPU por cento|Percentagem|Média|CPU por cento|Nenhum|
|memory_percent|Por cento da memória|Percentagem|Média|Por cento da memória|Nenhum|
|io_consumption_percent|IO por cento|Percentagem|Média|IO por cento|Nenhum|
|storage_percent|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Nenhum|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhum|
|storage_limit|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Nenhum|
|serverlog_storage_percent|Por cento de armazenamento de registo de servidor|Percentagem|Média|Por cento de armazenamento de registo de servidor|Nenhum|
|serverlog_storage_usage|Armazenamento de registo de servidor utilizado|Bytes|Média|Armazenamento de registo de servidor utilizado|Nenhum|
|serverlog_storage_limit|Limite de armazenamento de registo de servidor|Bytes|Máximo|Limite de armazenamento de registo de servidor|Nenhum|
|active_connections|Ligações Ativas|Contagem|Média|Ligações Ativas|Nenhum|
|connections_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Nenhum|
|seconds_behind_master|Atraso de replicação em segundos|Contagem|Máximo|Atraso de replicação em segundos|Nenhum|
|backup_storage_used|Armazenamento de backup utilizado|Bytes|Média|Armazenamento de backup utilizado|Nenhum|
|network_bytes_egress|Saída da Rede|Bytes|Total|Rede Para fora através de ligações ativas|Nenhum|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Rede Em através de ligações ativas|Nenhum|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servidores

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|CPU por cento|Percentagem|Média|CPU por cento|Nenhum|
|memory_percent|Por cento da memória|Percentagem|Média|Por cento da memória|Nenhum|
|io_consumption_percent|IO por cento|Percentagem|Média|IO por cento|Nenhum|
|storage_percent|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Nenhum|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhum|
|storage_limit|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Nenhum|
|serverlog_storage_percent|Por cento de armazenamento de registo de servidor|Percentagem|Média|Por cento de armazenamento de registo de servidor|Nenhum|
|serverlog_storage_usage|Armazenamento de registo de servidor utilizado|Bytes|Média|Armazenamento de registo de servidor utilizado|Nenhum|
|serverlog_storage_limit|Limite de armazenamento de registo de servidor|Bytes|Máximo|Limite de armazenamento de registo de servidor|Nenhum|
|active_connections|Ligações Ativas|Contagem|Média|Ligações Ativas|Nenhum|
|connections_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Nenhum|
|backup_storage_used|Armazenamento de backup utilizado|Bytes|Média|Armazenamento de backup utilizado|Nenhum|
|network_bytes_egress|Saída da Rede|Bytes|Total|Rede Para fora através de ligações ativas|Nenhum|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Rede Em através de ligações ativas|Nenhum|
|pg_replica_log_delay_in_seconds|Lag de réplica|Segundos|Máximo|Réplica lag em segundos|Nenhum|
|pg_replica_log_delay_in_bytes|Max Lag através de réplicas|Bytes|Máximo|Lag in bytes da réplica mais atrasada|Nenhum|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|CPU por cento|Percentagem|Média|CPU por cento|Nenhum|
|memory_percent|Por cento da memória|Percentagem|Média|Por cento da memória|Nenhum|
|iops|IOPS|Contagem|Média|Operações IO por segundo|Nenhum|
|storage_percent|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Nenhum|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhum|
|active_connections|Ligações Ativas|Contagem|Média|Ligações Ativas|Nenhum|
|network_bytes_egress|Saída da Rede|Bytes|Total|Rede Para fora através de ligações ativas|Nenhum|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Rede Em através de ligações ativas|Nenhum|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft.DBforPostgreSQL/singleservers

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|CPU por cento|Percentagem|Média|CPU por cento|Nenhum|
|memory_percent|Por cento da memória|Percentagem|Média|Por cento da memória|Nenhum|
|iops|IOPS|Contagem|Média|Operações IO por segundo|Nenhum|
|storage_percent|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Nenhum|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Nenhum|
|active_connections|Ligações Ativas|Contagem|Média|Ligações Ativas|Nenhum|
|network_bytes_egress|Saída da Rede|Bytes|Total|Rede Para fora através de ligações ativas|Nenhum|
|network_bytes_ingress|Entrada na Rede|Bytes|Total|Rede Em através de ligações ativas|Nenhum|
|connections_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Nenhum|
|connections_succeeded|Conexões bem sucedidas|Contagem|Total|Conexões bem sucedidas|Nenhum|
|maximum_used_transactionIDs|IDs máximos de transações usadas|Contagem|Média|IDs máximos de transações usadas|Nenhum|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|d2c.telemetria.ingress.allProtocol|Mensagem de telemetria envia tentativas|Contagem|Total|Número de mensagens de telemetria dispositivo-nuvem tentadas de ser enviadas para o seu hub IoT|Nenhum|
|d2c.telemetria.ingress.success|Mensagens de telemetria enviadas|Contagem|Total|Número de mensagens de telemetria dispositivo-nuvem enviadas com sucesso para o seu hub IoT|Nenhum|
|c2d.commands.egress.complete.success|Entregas de mensagens C2D concluídas|Contagem|Total|Número de entregas de mensagens nuvem-para-dispositivo concluídas com sucesso pelo dispositivo|Nenhum|
|c2d.commands.egress.abandon.success|Mensagens C2D abandonadas|Contagem|Total|Número de mensagens cloud-to-device abandonadas pelo dispositivo|Nenhum|
|c2d.commands.egress.reject.success|Mensagens C2D rejeitadas|Contagem|Total|Número de mensagens nuvem-dispositivo rejeitadas pelo dispositivo|Nenhum|
|C2DMessagesExpired|Mensagens C2D Expiradas (pré-visualização)|Contagem|Total|Número de mensagens de nuvem-para-dispositivo expiradas|Nenhum|
|dispositivos.totalDevices|Total de dispositivos (precotados)|Contagem|Total|Número de dispositivos registados no seu hub IoT|Nenhum|
|dispositivos.connectedDevices.allProtocol|Dispositivos ligados (precotados) |Contagem|Total|Número de dispositivos ligados ao seu hub IoT|Nenhum|
|d2c.telemetria.egress.sucesso|Encaminhamento: mensagens de telemetria entregues|Contagem|Total|O número de vezes que as mensagens foram entregues com sucesso em todos os pontos finais utilizando o encaminhamento IoT Hub. Se uma mensagem for encaminhada para vários pontos finais, este valor aumenta um para cada entrega bem sucedida. Se uma mensagem for entregue no mesmo ponto final várias vezes, este valor aumenta um para cada entrega bem sucedida.|Nenhum|
|d2c.telemetria.egress.drop|Encaminhamento: mensagens de telemetria caídas |Contagem|Total|O número de vezes que as mensagens foram deixadas pelo itinerário do IoT Hub devido a pontos sem saída. Este valor não conta as mensagens entregues para a rota de retorno, uma vez que as mensagens deixadas não são entregues lá.|Nenhum|
|d2c.telemetria.egress.órfão|Encaminhamento: mensagens de telemetria órfãs |Contagem|Total|O número de vezes que as mensagens foram órfãs pelo encaminhamento do IoT Hub porque não correspondem a nenhuma regra de encaminhamento (incluindo a regra do recuo). |Nenhum|
|d2c.telemetria.egress.inválido|Encaminhamento: mensagens de telemetria incompatíveis|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub não conseguiu entregar mensagens devido a uma incompatibilidade com o ponto final. Este valor não inclui retrações.|Nenhum|
|d2c.telemetria.egress.fallback|Encaminhamento: mensagens entregues para recuo|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens ao ponto final associado à rota de retorno.|Nenhum|
|d2c.endpoints.egress.eventHubs|Encaminhamento: mensagens entregues no Centro de Eventos|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso aos pontos finais do Event Hub.|Nenhum|
|d2c.endpoints.latncy.eventHubs|Encaminhamento: latência da mensagem para o Centro de Eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para ioT Hub e a entrada de mensagens num ponto final do Event Hub.|Nenhum|
|d2c.endpoints.egress.serviceBusQueues|Encaminhamento: mensagens entregues na Fila de Autocarros de Serviço|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso aos pontos finais da fila do Service Bus.|Nenhum|
|d2c.endpoints.latncy.serviceBusQueues|Encaminhamento: latência da mensagem para a fila do autocarro de serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada de mensagens de telemetria num ponto final de fila de autocarros de serviço.|Nenhum|
|d2c.endpoints.egress.serviceBusTopics|Encaminhamento: mensagens entregues no Service Bus Topic|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso aos pontos finais do tópico do Service Bus.|Nenhum|
|d2c.endpoints.latncy.serviceBusTopics|Encaminhamento: latência de mensagem para o tópico do autocarro de serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada de mensagens de telemetria num ponto final de ônibus de serviço.|Nenhum|
|d2c.endpoints.egress.builtIn.events|Encaminhamento: mensagens entregues a mensagens/eventos|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso ao ponto final incorporado (mensagens/eventos).|Nenhum|
|d2c.endpoints.latncy.builtIn.events|Encaminhamento: latência da mensagem para mensagens/eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada da mensagem de telemetria no ponto final incorporado (mensagens/eventos).|Nenhum|
|d2c.endpoints.egress.storage|Encaminhamento: mensagens entregues no armazenamento|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso aos pontos finais de armazenamento.|Nenhum|
|d2c.endpoints.latncy.storage|Encaminhamento: latência da mensagem para armazenamento|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada da mensagem de telemetria num ponto final de armazenamento.|Nenhum|
|d2c.endpoints.egress.storage.bytes|Encaminhamento: dados entregues ao armazenamento|Bytes|Total|A quantidade de dados (bytes) IoT Hub de encaminhamento entregue nos pontos finais de armazenamento.|Nenhum|
|d2c.endpoints.egress.storage.blobs|Encaminhamento: bolhas entregues ao armazenamento|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou bolhas nos pontos finais de armazenamento.|Nenhum|
|EventGridDeliveries|Entregas de Grelha de Eventos (pré-visualização)|Contagem|Total|O número de eventos IoT Hub publicados para a Event Grid. Utilize a dimensão Resultado para o número de pedidos bem sucedidos e falhados. A dimensão EventType mostra o tipo de evento https://aka.ms/ioteventgrid) (.|ResourceId,Resultado,EventType|
|EventGridLatency|Latência da grelha de eventos (pré-visualização)|Milissegundos|Média|A latência média (milissegundos) de quando o evento Iot Hub foi gerado para quando o evento foi publicado para a Event Grid. Este número é uma média entre todos os tipos de eventos. Utilize a dimensão EventType para ver a latência de um tipo específico de evento.|ResourceId,EventType|
|RoteamentoDelivas|Entregas de encaminhamento (pré-visualização)|Milissegundos|Total|O número de vezes que o IoT Hub tentou entregar mensagens a todos os pontos finais utilizando o encaminhamento. Para ver o número de tentativas bem sucedidas ou falhadas, utilize a dimensão Resultado. Para ver a razão da falha, como inválido, caído ou órfão, use a dimensão FailReasonCategory. Também pode utilizar as dimensões EndpointName e EndpointType para entender quantas mensagens foram entregues nos seus diferentes pontos finais. O valor métrico aumenta por um para cada tentativa de entrega, incluindo se a mensagem é entregue em vários pontos finais ou se a mensagem é entregue no mesmo ponto final várias vezes.|ResourceId,EndpointType,EndpointName,FailureReasonCategory,Result,RoutingSource|
|EncaminhamentoDeliveryLatency|Data de entrega de encaminhamento (pré-visualização)|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada da mensagem de telemetria num ponto final. Pode utilizar as dimensões EndpointName e EndpointType para entender a latência dos seus diferentes pontos finais.|ResourceId,EndpointType,EndpointName,RoutingSource|
|d2c.twin.read.success|Leituras gémeas bem sucedidas de dispositivos|Contagem|Total|A contagem de todas as leituras gémeas iniciadas pelo dispositivo.|Nenhum|
|d2c.twin.read.failure|Leituras gémeas falhadas dos dispositivos|Contagem|Total|A contagem de todas as leituras gémeas iniciadas pelo dispositivo falhados.|Nenhum|
|d2c.twin.read.size|Tamanho de resposta de leituras gémeas de dispositivos|Bytes|Média|A média, min e max de todas as leituras gémeas iniciadas pelo dispositivo com sucesso.|Nenhum|
|d2c.twin.update.success|Atualizações gémeas bem sucedidas dos dispositivos|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas pelo dispositivo com sucesso.|Nenhum|
|d2c.twin.update.failure|Falhas em duas atualizações a partir de dispositivos|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas pelo dispositivo.|Nenhum|
|d2c.twin.update.size|Tamanho das atualizações gémeas dos dispositivos|Bytes|Média|O tamanho médio, min e máximo de todas as atualizações gémeas iniciadas pelo dispositivo com sucesso.|Nenhum|
|c2d.methods.success|Invocações de métodos diretos bem-sucedidas|Contagem|Total|A contagem de todas as chamadas de métodos diretos bem sucedidos.|Nenhum|
|c2d.methods.falha|Invocações de método direto falhadas|Contagem|Total|A contagem de todas as chamadas de métodos diretos falhadas.|Nenhum|
|c2d.methods.requestSize|Tamanho do pedido de invocações de método direto|Bytes|Média|A média, min e máxima de todos os pedidos de método direto bem sucedidos.|Nenhum|
|c2d.methods.responseSize|Tamanho da resposta das invocações do método direto|Bytes|Média|A média, min e máxima de todas as respostas de métodos diretos bem sucedidas.|Nenhum|
|c2d.twin.read.success|Gémea bem sucedida lê a partir da parte de trás|Contagem|Total|A contagem de todas as leituras gémeas iniciadas com sucesso.|Nenhum|
|c2d.twin.read.failure|Gémeo falhado lê do fundo|Contagem|Total|A contagem de todas as leituras gémeas falhadas.|Nenhum|
|c2d.twin.read.size|Tamanho de resposta de leituras gémeas da parte de trás|Bytes|Média|A média, min, e máx.|Nenhum|
|c2d.twin.update.success|Atualizações gémeas bem sucedidas a partir do final|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas com sucesso.|Nenhum|
|c2d.twin.update.failure|Falhas em duas atualizações a partir do final|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas pela parte de trás.|Nenhum|
|c2d.twin.update.size|Tamanho das atualizações gémeas a partir da parte de trás|Bytes|Média|O tamanho médio, min e máximo de todas as atualizações gémeas iniciadas com sucesso.|Nenhum|
|twinQueries.sucesso|Consultas gémeas bem sucedidas|Contagem|Total|A contagem de todas as consultas gémeas bem sucedidas.|Nenhum|
|twinQueries.falha|Consultas gémeas falhadas|Contagem|Total|A contagem de todas as consultas gémeas falhadas.|Nenhum|
|twinQueries.resultSize|Tamanho do resultado de consultas gémeas|Bytes|Média|A média, min e máximo do tamanho do resultado de todas as consultas gémeas bem sucedidas.|Nenhum|
|jobs.createTwinUpdateJob.success|Criações bem sucedidas de trabalhos de atualização dupla|Contagem|Total|A contagem de todos os trabalhos bem sucedidos de criação de trabalhos de atualização dupla.|Nenhum|
|jobs.createTwinUpdateJob.failure|Criações falhadas de trabalhos de atualização dupla|Contagem|Total|A contagem de todos os trabalhos falhados de atualização dupla.|Nenhum|
|jobs.createDirectMethodJob.success|Criações bem sucedidas de trabalhos de invocação de métodos|Contagem|Total|A contagem de todos os trabalhos bem sucedidos de criação de trabalhos de invocação de métodos diretos.|Nenhum|
|jobs.createDirectMethodJob.fail|Criações falhadas de trabalhos de invocação de métodos|Contagem|Total|A contagem de todos os trabalhos falhados de invocação de métodos diretos.|Nenhum|
|jobs.listJobs.sucesso|Chamadas bem-sucedidas para listar empregos|Contagem|Total|A contagem de todas as chamadas bem sucedidas para listar empregos.|Nenhum|
|empregos.listJobs.fracasso|Chamadas falhadas para listar empregos|Contagem|Total|A contagem de todas as chamadas falhadas para listar empregos.|Nenhum|
|jobs.cancelJob.sucesso|Cancelamentos de emprego bem-sucedidos|Contagem|Total|A contagem de todas as chamadas bem sucedidas para cancelar um emprego.|Nenhum|
|jobs.cancelJob.failure|Cancelamentos de empregos falhados|Contagem|Total|A contagem de todas as chamadas falhadas para cancelar um emprego.|Nenhum|
|jobs.queryJobs.sucesso|Consultas de trabalho bem sucedidas|Contagem|Total|A contagem de todas as chamadas bem sucedidas para trabalhos de consulta.|Nenhum|
|jobs.consultaJobs.falha|Consultas de trabalho falhadas|Contagem|Total|A contagem de todas as chamadas falhadas para consultas.|Nenhum|
|empregos.concluídos|Trabalhos concluídos|Contagem|Total|A contagem de todos os trabalhos concluídos.|Nenhum|
|empregos.falhou|Empregos falhados|Contagem|Total|A contagem de todos os trabalhos falhados.|Nenhum|
|d2c.telemetria.ingress.sendThrottle|Número de erros de estrangulamento|Contagem|Total|Número de erros de estrangulamento devido aos aceleradores de produção do dispositivo|Nenhum|
|dailyMessageQuotaUsed|Número total de mensagens utilizadas|Contagem|Média|Número de mensagens totais usadas hoje|Nenhum|
|dispositivoDataUsage|Total da utilização de dados do dispositivo|Bytes|Total|Bytes transferidos de e para qualquer dispositivo ligado ao IotHub|Nenhum|
|dispositivoDataUsageV2|Total da utilização dos dados do dispositivo (pré-visualização)|Bytes|Total|Bytes transferidos de e para qualquer dispositivo ligado ao IotHub|Nenhum|
|totalDeviceCount|Total de dispositivos (pré-visualização)|Contagem|Média|Número de dispositivos registados no seu hub IoT|Nenhum|
|connectedDeviceCount|Dispositivos conectados (pré-visualização)|Contagem|Média|Número de dispositivos ligados ao seu hub IoT|Nenhum|
|configurações|Métricas de configuração|Contagem|Total|Métricas para Operações de Configuração|Nenhum|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|InscriçõesAptas|Tentativas de registo|Contagem|Total|Número de registos de dispositivos tentados|ProvisioningServiceName,IotHubName,Status|
|Configurações de Dispositivos|Dispositivos atribuídos|Contagem|Total|Número de dispositivos atribuídos a um hub IoT|ProvisioningServiceName,IotHubName|
|AttestationAttempts|Tentativas de atestado|Contagem|Total|Número de atestesações de dispositivos tentados|ProvisioningServiceName,Status,Protocol|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/base de dadosAcontas

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|AddRegion|Região Adicionada|Contagem|Contagem|Região Adicionada|Região|
|DisponíveisToragem|Armazenamento Disponível|Bytes|Total|Armazenamento total disponível reportado a 5 minutos de granularidade|Nome de coleção,Nome de Base de Dados,Região|
|CassandraConnectionClosures|Encerramentos de conexão cassandra|Contagem|Total|Número de ligações cassandra que foram fechadas, reportadas a uma granularidade de 1 minuto|APIType,Região,ClosureReason|
|CassandraKeyspaceDelete|Cassandra Keyspace eliminada|Contagem|Contagem|Cassandra Keyspace eliminada|Nome de recurso,ApiKind,ApiKindResourceType,OperationType|
|CassandraKeyspaceThroughputUpdate|Cassandra Keyspace Throughput Atualizado|Contagem|Contagem|Cassandra Keyspace Throughput Atualizado|Nome de recurso,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraKeyspaceUpdate|Cassandra Keyspace Atualizado|Contagem|Contagem|Cassandra Keyspace Atualizado|Nome de recurso,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraRequestCharges|Cassandra Request Charges|Contagem|Total|RUs consumidos por pedidos de Cassandra feitos|APIType,DatabaseName,CollectionName,Region,OperationType,ResourceType|
|CassandraRequests|Pedidos de Cassandra|Contagem|Contagem|Número de pedidos de Cassandra feitos|APIType,DatabaseName,CollectionName,Region,OperationType,ResourceType,ErrorCode|
|CassandraTableDelete|Tabela Cassandra Eliminada|Contagem|Contagem|Tabela Cassandra Eliminada|Nome de recurso,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|CassandraTableThroughputUpdate|Produção de tabela Cassandra atualizada|Contagem|Contagem|Produção de tabela Cassandra atualizada|Nome de recurso,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraTableUpdate|Tabela Cassandra Atualizada|Contagem|Contagem|Tabela Cassandra Atualizada|Nome de recurso,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|Criar Contas|Conta Criada|Contagem|Contagem|Conta Criada|Nenhum|
|DataUsage|Utilização de Dados|Bytes|Total|Total de utilização de dados reportados a 5 minutos de granularidade|Nome de coleção,Nome de Base de Dados,Região|
|ExcluirAconta|Conta Eliminada|Contagem|Contagem|Conta Eliminada|Nenhum|
|Contagem de documentos|Contagem de documentos|Contagem|Total|Contagem total de documentos reportada em 5 minutos granularidade|Nome de coleção,Nome de Base de Dados,Região|
|DocumentQuota|Quota documental|Bytes|Total|Quota total de armazenamento reportada a 5 minutos de granularidade|Nome de coleção,Nome de Base de Dados,Região|
|GremlinDatabaseDelete|Gremlin Base de Dados Eliminada|Contagem|Contagem|Gremlin Base de Dados Eliminada|Nome de recurso,ApiKind,ApiKindResourceType,OperationType|
|GremlinDatabaseThroughputUpdate|Produção de base de dados gremlin atualizada|Contagem|Contagem|Produção de base de dados gremlin atualizada|Nome de recurso,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinDatabaseUpdate|Base de Dados Gremlin Atualizada|Contagem|Contagem|Base de Dados Gremlin Atualizada|Nome de recurso,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinGraphDelete|Gremlin Graph Eliminado|Contagem|Contagem|Gremlin Graph Eliminado|Nome de recurso,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|GremlinGraphThroughputUpdate|Throughputo de gráfico gremlin atualizado|Contagem|Contagem|Throughputo de gráfico gremlin atualizado|Nome de recurso,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinGraphUpdate|Gráfico de Gremlin Atualizado|Contagem|Contagem|Gráfico de Gremlin Atualizado|Nome de recurso,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|IndexUsage|Utilização do Índice|Bytes|Total|Utilização total do índice reportada a 5 minutos de granularidade|Nome de coleção,Nome de Base de Dados,Região|
|MetadataReques|Pedidos de Metadados|Contagem|Contagem|Contagem de pedidos de metadados. Cosmos DB mantém recolha de metadados do sistema para cada conta, que lhe permite enumerar coleções, bases de dados, etc, e suas configurações, gratuitamente.|Data de dados Nome,Nome de Recolha,Região,Código de Estado,Função|
|MongoCollectionDelete|Coleção mongo eliminada|Contagem|Contagem|Coleção mongo eliminada|Nome de recurso,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|MongoCollectionThroughputUpdate|Produção de coleção de Mongo atualizada|Contagem|Contagem|Produção de coleção de Mongo atualizada|Nome de recurso,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoCollectionUpdate|Coleção Mongo Atualizada|Contagem|Contagem|Coleção Mongo Atualizada|Nome de recurso,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoDBDatabaseUpdate|Base de Dados de Mongo Atualizada|Contagem|Contagem|Base de Dados de Mongo Atualizada|Nome de recurso,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoDatabaseDelete|Base de Dados de Mongo eliminada|Contagem|Contagem|Base de Dados de Mongo eliminada|Nome de recurso,ApiKind,ApiKindResourceType,OperationType|
|MongoDatabaseThroughputUpdate|Produção de base de dados de Mongo atualizada|Contagem|Contagem|Produção de base de dados de Mongo atualizada|Nome de recurso,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoRequestCharge|Taxa de pedido de Mongo|Contagem|Total|Unidades de pedido da Mongo Consumidas|Data de dadosName,Nome de Recolha,Região,Nome de Comando,Código de Erro,Estado|
|MongoRequests|Pedidos de Mongo|Contagem|Contagem|Número de pedidos da Mongo Feitos|Data de dadosName,Nome de Recolha,Região,Nome de Comando,Código de Erro,Estado|
|MongoRequestsCount|Taxa de pedido de Mongo|CondePerSecond|Média|Mongo pede Conde por segundo|Nome de dados,Nome de recolha,Região,Nome de Comando,Código de Erro|
|MongoRequestsDelete|Tarifa de pedido de eliminação de Mongo|CondePerSecond|Média|Mongo Delete request por segundo|Nome de dados,Nome de recolha,Região,Nome de Comando,Código de Erro|
|MongoRequestsInsert|Taxa de pedido de inserção de Mongo|CondePerSecond|Média|Contagem de Inserção de Mongo por segundo|Nome de dados,Nome de recolha,Região,Nome de Comando,Código de Erro|
|MongoRequestsQuery|Taxa de pedido de consulta de Mongo|CondePerSecond|Média|Pedido de consulta de Mongo por segundo|Nome de dados,Nome de recolha,Região,Nome de Comando,Código de Erro|
|MongoRequestsUpdate|Taxa de pedido de atualização de Mongo|CondePerSecond|Média|Pedido de atualização de Mongo por segundo|Nome de dados,Nome de recolha,Região,Nome de Comando,Código de Erro|
|Ruconsumption normalizado|Consumo de RU normalizado|Percentagem|Máximo|Percentagem de consumo max RU por minuto|Nome de coleção,Nome de Base de Dados,Região|
|ProvisionedThroughput|Débito Aprovisionado|Contagem|Máximo|Débito Aprovisionado|Nome de dados,Nome de Coleção|
|RegionFailover|Região falhou|Contagem|Contagem|Região falhou|Nenhum|
|Remover a Região|Região removida|Contagem|Contagem|Região removida|Região|
|ReplicationLatency|Latência de replicação P99|MilliSeconds|Média|P99 Replicação Latência em todas as regiões de origem e alvo para conta geo-habilitada|OrigemRegion,TargetRegion|
|ServerSideLatency|Latência do lado do servidor|MilliSeconds|Média|Latência do lado do servidor|Nome de dados,Nome de Recolha,Região,ConnectionMode,OperationType,PublicAPIType|
|ServiçoSAdisponibilidade|Disponibilidade de serviço|Percentagem|Média|Conta solicita disponibilidade a uma hora, dia ou mês de granularidade|Nenhum|
|SqlContainerDelete|Recipiente sql eliminado|Contagem|Contagem|Recipiente sql eliminado|Nome de recurso,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|SqlContainerThroughputUpdate|Produção de recipiente sql atualizado|Contagem|Contagem|Produção de recipiente sql atualizado|Nome de recurso,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|SqlContainerUpdate|Recipiente Sql Atualizado|Contagem|Contagem|Recipiente Sql Atualizado|Nome de recurso,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|SqlDatabaseDelete|Base de Dados Sql Eliminada|Contagem|Contagem|Base de Dados Sql Eliminada|Nome de recurso,ApiKind,ApiKindResourceType,OperationType|
|SqlDatabaseThroughputUpdate|Produção de base de dados sql atualizada|Contagem|Contagem|Produção de base de dados sql atualizada|Nome de recurso,ApiKind,ApiKindResourceType,IsThroughputRequest|
|SqlDatabaseUpdate|Base de Dados Sql Atualizada|Contagem|Contagem|Base de Dados Sql Atualizada|Nome de recurso,ApiKind,ApiKindResourceType,IsThroughputRequest|
|TableTableDelete|Tabela AzureTable Eliminada|Contagem|Contagem|Tabela AzureTable Eliminada|Nome de recurso,ApiKind,ApiKindResourceType,OperationType|
|TableTableThroughputUpdate|Produção de tabela azuretable atualizado|Contagem|Contagem|Produção de tabela azuretable atualizado|Nome de recurso,ApiKind,ApiKindResourceType,IsThroughputRequest|
|TableTableUpdate|Tabela AzureTable atualizada|Contagem|Contagem|Tabela AzureTable atualizada|Nome de recurso,ApiKind,ApiKindResourceType,IsThroughputRequest|
|TotalRequestUnits|Total de Unidades de Pedido|Contagem|Total|Unidades de pedido consumidas|Data de dadosName,Nome de Recolha,Região,Código de Estado,OperaçãoType,Estado|
|TotalRequests|Total de Pedidos|Contagem|Contagem|Número de pedidos feitos|Data de dadosName,Nome de Recolha,Região,Código de Estado,OperaçãoType,Estado|
|UpdateAccountKeys|Chaves de conta atualizadas|Contagem|Contagem|Chaves de conta atualizadas|Teclas|
|UpdateAccountNetworkSettings|Definições de rede de conta atualizadas|Contagem|Contagem|Definições de rede de conta atualizadas|Nenhum|
|UpdateAccountReplicationSettings|Definições de replicação de conta atualizadas|Contagem|Contagem|Definições de replicação de conta atualizadas|Nenhum|
|AtualizaçãoDiagnosticsSettings|Definições de diagnóstico de conta atualizadas|Contagem|Contagem|Definições de diagnóstico de conta atualizadas|DiagnósticoSettingsName,Nome do Grupo de Recursos|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft.EnterpriseKnowledgeGraph/services

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|TransaçãoCount|Contagem de transações|Contagem|Contagem|Contagem total de transações|TransaçãoCount|
|SucessoCount|Contagem de Êxitos|Contagem|Contagem|Contagem de Transações Bem Sucedidas|SucessoCount|
|Contagem de falhas|Contagem de Falhas|Contagem|Contagem|Contagem de Transações Falhadas|Contagem de falhas|
|SucessoSA|Latência do sucesso|MilliSeconds|Média|Latência de Transações Bem Sucedidas|SucessoCount|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domínios

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|PublicarSuccessCount|Eventos Publicados|Contagem|Total|Total de eventos publicados neste tópico|Tópico|
|PublicarFailCount|Publicar Eventos Falhados|Contagem|Total|Total de eventos não publicou para este tópico|Tópico,ErrorType,Error|
|Publicar Inimitrodências|Publicar Latência de Sucesso|Milissegundos|Total|Publique latência de sucesso em milissegundos|Nenhum|
|MatchedEventCount|Eventos Combinados|Contagem|Total|Total de eventos combinados com esta subscrição de evento|Tema,EventSubscriptionName,DomainEventSubscriptionName|
|EntregaAttemptFailCount|Eventos falhados de entrega|Contagem|Total|Total de eventos não entregaram a esta subscrição do evento|Tópico,EventSubscriptionName,DomainEventSubscriptionName,Error,ErrorType|
|EntregaSSuccessCount|Eventos Entregues|Contagem|Total|Total de eventos entregues a esta subscrição do evento|Tema,EventSubscriptionName,DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Tema,EventSubscriptionName,DomainEventSubscriptionName|
|DropEventCount|Eventos abandonados|Contagem|Total|Total de eventos perdidos correspondentes a esta subscrição do evento|Tópico,EventSubscriptionName,DomainEventSubscriptionName,DropReason|
|DeadLetteredCount|Eventos lettered mortos|Contagem|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|Tópico,EventSubscriptionName,DomainEventSubscriptionName,DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/tópicos

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|PublicarSuccessCount|Eventos Publicados|Contagem|Total|Total de eventos publicados neste tópico|Nenhum|
|PublicarFailCount|Publicar Eventos Falhados|Contagem|Total|Total de eventos não publicou para este tópico|ErrorType,Error|
|InigualávelEventCount|Eventos incomparáveis|Contagem|Total|Total de eventos que não correspondem a nenhuma das subscrições do evento para este tópico|Nenhum|
|Publicar Inimitrodências|Publicar Latência de Sucesso|Milissegundos|Total|Publique latência de sucesso em milissegundos|Nenhum|
|MatchedEventCount|Eventos Combinados|Contagem|Total|Total de eventos combinados com esta subscrição de evento|Nome de subscrição de eventos|
|EntregaAttemptFailCount|Eventos falhados de entrega|Contagem|Total|Total de eventos não entregaram a esta subscrição do evento|Erro,ErrorType,EventSubscriptionName|
|EntregaSSuccessCount|Eventos Entregues|Contagem|Total|Total de eventos entregues a esta subscrição do evento|Nome de subscrição de eventos|
|DestinationProcessingDurationInMs|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Nome de subscrição de eventos|
|DropEventCount|Eventos abandonados|Contagem|Total|Total de eventos perdidos correspondentes a esta subscrição do evento|DropReason,EventSubscriptionName|
|DeadLetteredCount|Eventos lettered mortos|Contagem|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|PublicarSuccessCount|Eventos Publicados|Contagem|Total|Total de eventos publicados neste tópico|Nenhum|
|PublicarFailCount|Publicar Eventos Falhados|Contagem|Total|Total de eventos não publicou para este tópico|ErrorType,Error|
|InigualávelEventCount|Eventos incomparáveis|Contagem|Total|Total de eventos que não correspondem a nenhuma das subscrições do evento para este tópico|Nenhum|
|Publicar Inimitrodências|Publicar Latência de Sucesso|Milissegundos|Total|Publique latência de sucesso em milissegundos|Nenhum|
|MatchedEventCount|Eventos Combinados|Contagem|Total|Total de eventos combinados com esta subscrição de evento|Nome de subscrição de eventos|
|EntregaAttemptFailCount|Eventos falhados de entrega|Contagem|Total|Total de eventos não entregaram a esta subscrição do evento|Erro,ErrorType,EventSubscriptionName|
|EntregaSSuccessCount|Eventos Entregues|Contagem|Total|Total de eventos entregues a esta subscrição do evento|Nome de subscrição de eventos|
|DestinationProcessingDurationInMs|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Nome de subscrição de eventos|
|DropEventCount|Eventos abandonados|Contagem|Total|Total de eventos perdidos correspondentes a esta subscrição do evento|DropReason,EventSubscriptionName|
|DeadLetteredCount|Eventos lettered mortos|Contagem|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|MatchedEventCount|Eventos Combinados|Contagem|Total|Total de eventos combinados com esta subscrição de evento|Nenhum|
|EntregaAttemptFailCount|Eventos falhados de entrega|Contagem|Total|Total de eventos não entregaram a esta subscrição do evento|Erro,ErrorType|
|EntregaSSuccessCount|Eventos Entregues|Contagem|Total|Total de eventos entregues a esta subscrição do evento|Nenhum|
|DestinationProcessingDurationInMs|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Nenhum|
|DropEventCount|Eventos abandonados|Contagem|Total|Total de eventos perdidos correspondentes a esta subscrição do evento|GotaReason|
|DeadLetteredCount|Eventos lettered mortos|Contagem|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|PublicarSuccessCount|Eventos Publicados|Contagem|Total|Total de eventos publicados neste tópico|Nenhum|
|PublicarFailCount|Publicar Eventos Falhados|Contagem|Total|Total de eventos não publicou para este tópico|ErrorType,Error|
|InigualávelEventCount|Eventos incomparáveis|Contagem|Total|Total de eventos que não correspondem a nenhuma das subscrições do evento para este tópico|Nenhum|
|Publicar Inimitrodências|Publicar Latência de Sucesso|Milissegundos|Total|Publique latência de sucesso em milissegundos|Nenhum|




## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/espaços de nome

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Requess de sucesso|Pedidos de Sucesso|Contagem|Total|Pedidos de sucesso para Microsoft.EventHub.|Nome da Entidade,OperaçãoResult|
|ServerErrors|Erros do servidor.|Contagem|Total|Erros de servidor para Microsoft.EventHub.|Nome da Entidade,OperaçãoResult|
|UserErrors|Erros do utilizador.|Contagem|Total|Erros do utilizador para o Microsoft.EventHub.|Nome da Entidade,OperaçãoResult|
|QuotaExceededErrors|Quota excedeu erros.|Contagem|Total|Quota Excedeu erros para Microsoft.EventHub.|Nome da Entidade,OperaçãoResult|
|ThrottledRequests|Pedidos de aceleração.|Contagem|Total|Pedidos de aceleração para o Microsoft.EventHub.|Nome da Entidade,OperaçãoResult|
|IncomingRequests|Pedidos de Entrada|Contagem|Total|Pedidos de entrada para Microsoft.EventHub.|Nome de Entidade|
|Entradas|Mensagens recebidas|Contagem|Total|Mensagens de entrada para Microsoft.EventHub.|Nome de Entidade|
|Despesas de Saída|Mensagens de saída|Contagem|Total|Mensagens de saída para o Microsoft.EventHub.|Nome de Entidade|
|IncomingBytes|Entradas bytes.|Bytes|Total|Incoming Bytes para Microsoft.EventHub.|Nome de Entidade|
|OutgoingBytes|Bytes de saída.|Bytes|Total|Bytes de saída para Microsoft.EventHub.|Nome de Entidade|
|ActiveConnections|ActiveConnections|Contagem|Média|Total de conexões ativas para Microsoft.EventHub.|Nenhum|
|LigaçõesAsa|Ligações Abertas.|Contagem|Média|Ligações abertas para Microsoft.EventHub.|Nome de Entidade|
|LigaçõesClosed|Ligações fechadas.|Contagem|Média|Ligações fechadas para Microsoft.EventHub.|Nome de Entidade|
|CaptureBacklog|Capture Backlog.|Contagem|Total|Capture Backlog for Microsoft.EventHub.|Nome de Entidade|
|Caixas de Captura|Mensagens capturadas.|Contagem|Total|Mensagens capturadas para Microsoft.EventHub.|Nome de Entidade|
|CapturouBytes|Bytes capturados.|Bytes|Total|Bytes capturados para Microsoft.EventHub.|Nome de Entidade|
|Tamanho|Tamanho|Bytes|Média|Tamanho de um EventHub em Bytes.|Nome de Entidade|
|INREQS|Pedidos de Entrada (Deprecados)|Contagem|Total|Total de pedidos de envio para um espaço de nome (Deprecado)|Nenhum|
|SUCCREQ|Pedidos de sucesso (Deprecados)|Contagem|Total|Total de pedidos bem sucedidos para um espaço de nome (Deprecado)|Nenhum|
|FAILREQ|Pedidos Falhados (Deprecados)|Contagem|Total|Total de pedidos falhados para um espaço de nome (Deprecado)|Nenhum|
|SVRBSY|Erros ocupados do servidor (preprecados)|Contagem|Total|Erros de ocupado total do servidor para um espaço de nome (Deprecado)|Nenhum|
|INTERR|Erros internos do servidor (preprecados)|Contagem|Total|Erros internos totais do servidor para um espaço de nome (Deprecado)|Nenhum|
|MISCERR|Outros Erros (Precotados)|Contagem|Total|Total de pedidos falhados para um espaço de nome (Deprecado)|Nenhum|
|INMSGS|Mensagens recebidas (obsoletas) (Depreciadas)|Contagem|Total|Total de mensagens recebidas para um espaço de nome. Esta métrica está prevadida. Utilize a métrica de Mensagens recebidas (Deprecadas)|Nenhum|
|EFINMSGS|Mensagens recebidas (depreciadas)|Contagem|Total|Total de mensagens recebidas para um espaço de nome (Deprecado)|Nenhum|
|OUTMSGS|Mensagens de saída (obsoletas) (Depreciadas)|Contagem|Total|Mensagens de saída total para um espaço de nome. Esta métrica está prevadida. Utilize em vez disso a métrica mensagens de saída (deprecadas)|Nenhum|
|EHOUTMSGS|Mensagens de saída (depreciadas)|Contagem|Total|Total de mensagens de saída para um espaço de nome (Deprecado)|Nenhum|
|EHINMBS|Bytes de entrada (obsoletos) (Deprecados)|Bytes|Total|Mensagem de entrada do Event Hub para um espaço de nome. Esta métrica está prevadida. Utilize em vez disso a métrica de entrada de bytes (Deprecada)|Nenhum|
|EHINBYTES|Bytes de entrada (Deprecados)|Bytes|Total|Envio de mensagem do Event Hub para um espaço de nome (Depreciado)|Nenhum|
|EHOUTMBS|Bytes de saída (obsoletos) (Preprecados)|Bytes|Total|Mensagem de saída do Event Hub para um espaço de nome. Esta métrica está prevadida. Utilize em vez disso bytes outgoing (Deprecado)|Nenhum|
|EHOUTBYTES|Bytes de saída (Deprecados)|Bytes|Total|Mensagem de saída do Event Hub para um espaço de nome (Depreciado)|Nenhum|
|EHABL|Mensagens de atraso de arquivo (Deprecadas)|Contagem|Total|Mensagens de arquivo do Event Hub em atraso para um espaço de nome (Depreciado)|Nenhum|
|EHAMSGS|Mensagens de arquivo (Depreciadas)|Contagem|Total|Event Hub arquivou mensagens num espaço de nome (Depreciado)|Nenhum|
|EHAMBS|Produção de mensagem de arquivo (Precotada)|Bytes|Total|Event Hub arquivou produção de mensagem num espaço de nome (Depreciado)|Nenhum|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Requess de sucesso|Pedidos de Sucesso|Contagem|Total|Pedidos de sucesso para Microsoft.EventHub.|OperaçãoResult|
|ServerErrors|Erros do servidor.|Contagem|Total|Erros de servidor para Microsoft.EventHub.|OperaçãoResult|
|UserErrors|Erros do utilizador.|Contagem|Total|Erros do utilizador para o Microsoft.EventHub.|OperaçãoResult|
|QuotaExceededErrors|Quota excedeu erros.|Contagem|Total|Quota Excedeu erros para Microsoft.EventHub.|OperaçãoResult|
|ThrottledRequests|Pedidos de aceleração.|Contagem|Total|Pedidos de aceleração para o Microsoft.EventHub.|OperaçãoResult|
|IncomingRequests|Pedidos de Entrada|Contagem|Total|Pedidos de entrada para Microsoft.EventHub.|Nenhum|
|Entradas|Mensagens recebidas|Contagem|Total|Mensagens de entrada para Microsoft.EventHub.|Nenhum|
|Despesas de Saída|Mensagens de saída|Contagem|Total|Mensagens de saída para o Microsoft.EventHub.|Nenhum|
|IncomingBytes|Entradas bytes.|Bytes|Total|Incoming Bytes para Microsoft.EventHub.|Nenhum|
|OutgoingBytes|Bytes de saída.|Bytes|Total|Bytes de saída para Microsoft.EventHub.|Nenhum|
|ActiveConnections|ActiveConnections|Contagem|Média|Total de conexões ativas para Microsoft.EventHub.|Nenhum|
|LigaçõesAsa|Ligações Abertas.|Contagem|Média|Ligações abertas para Microsoft.EventHub.|Nenhum|
|LigaçõesClosed|Ligações fechadas.|Contagem|Média|Ligações fechadas para Microsoft.EventHub.|Nenhum|
|CaptureBacklog|Capture Backlog.|Contagem|Total|Capture Backlog for Microsoft.EventHub.|Nenhum|
|Caixas de Captura|Mensagens capturadas.|Contagem|Total|Mensagens capturadas para Microsoft.EventHub.|Nenhum|
|CapturouBytes|Bytes capturados.|Bytes|Total|Bytes capturados para Microsoft.EventHub.|Nenhum|
|CPU|CPU|Percentagem|Máximo|Utilização do CPU para o Cluster de Hub de Eventos em percentagem|Função|
|DisponívelMemory|Memória Disponível|Percentagem|Máximo|Memória disponível para o Cluster De Eventos hub em percentagem da memória total.|Função|
|Tamanho|Tamanho de um EventHub em Bytes.|Bytes|Média|Tamanho de um EventHub em Bytes.|Função|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|GatewayRequests|Pedidos de Gateway|Contagem|Total|Número de pedidos de gateway|HttpStatus|
|CategorizadosGatewayRequests|Pedidos de Gateway categorizados|Contagem|Total|Número de pedidos de gateway por categorias (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|NumActiveWorkers|Número de trabalhadores ativos|Contagem|Máximo|Número de trabalhadores ativos|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|ValorMétrico Observado|Valor métrico observado|Contagem|Média|O valor calculado por autoescala quando executado|MetricTriggerSource|
|MétricaThreshold|Limiar métrico|Contagem|Média|O limiar de autoescala configurado quando a autoescalada foi executada.|MétricaTriggerrule|
|Capacidade Observada|Capacidade observada|Contagem|Média|A capacidade reportada para autoescalar quando executou.|Nenhum|
|ScaleActionsInitiado|Ações de escala iniciadas|Contagem|Total|A direção da operação de escala.|Direção de Escala|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Componentes

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|disponibilidadeResults/disponibilidadePercentage|Disponibilidade|Percentagem|Média|Percentagem de testes de disponibilidade concluídos com sucesso|disponibilidadeResult/name,disponibilidadeResult/location|
|disponibilidadeResults/contagem|Testes de disponibilidade|Contagem|Contagem|Contagem de testes de disponibilidade|disponibilidadeResult/name,disponibilidadeResult/location,disponibilidadeResult/sucesso|
|disponibilidadeResults/duração|Duração do teste de disponibilidade|MilliSeconds|Média|Duração do teste de disponibilidade|disponibilidadeResult/name,disponibilidadeResult/location,disponibilidadeResult/sucesso|
|browserEs/networkDuration|Tempo de ligação da rede de carga de página|MilliSeconds|Média|Tempo entre o pedido do utilizador e a ligação à rede. Inclui a procura de DNS e a ligação de transporte.|Nenhum|
|browserEs/processamentoDuração|Tempo de processamento do cliente|MilliSeconds|Média|Tempo entre receber o último byte de um documento até que o DOM seja carregado. Os pedidos de async ainda podem estar a ser processados.|Nenhum|
|browserEs/receberDuration|Tempo de resposta de receção|MilliSeconds|Média|Tempo entre o primeiro e o último bytes, ou até a desconexão.|Nenhum|
|browserEs/sendDuration|Enviar tempo de pedido|MilliSeconds|Média|Tempo entre a ligação de rede e receber o primeiro byte.|Nenhum|
|browserEs/totalDuration|Tempo de carregamento da página do navegador|MilliSeconds|Média|Tempo do pedido do utilizador até dom, folhas de estilo, scripts e imagens são carregados.|Nenhum|
|dependências/contagem|Chamadas de dependência|Contagem|Contagem|Contagem de chamadas feitas pela aplicação a recursos externos.|dependência/tipo,dependência/performanceBucket,dependência/sucesso,dependência/destino,dependência/resultadoSCórne,operação/sintético,nuvem/roleInstance,cloud/roleName|
|dependências/duração|Duração da dependência|MilliSeconds|Média|Duração das chamadas efetuadas pela aplicação a recursos externos.|dependência/tipo,dependência/performanceBucket,dependência/sucesso,dependência/destino,dependência/resultadoSCórne,operação/sintético,nuvem/roleInstance,cloud/roleName|
|dependências/falhados|Falhas nas chamadas de dependência|Contagem|Contagem|Contagem de chamadas de dependência falhadas feitas pela aplicação a recursos externos.|dependência/tipo,dependência/performanceBucket,dependência/sucesso,dependência/destino,dependência/resultadoSCórne,operação/sintético,nuvem/roleInstance,cloud/roleName|
|pageVers/contagem|Vistas da página|Contagem|Contagem|Contagem de visualizações de página.|operação/sintético,nuvem/funName|
|páginaVerses/duração|Tempo de carga da visualização da página|MilliSeconds|Média|Tempo de carga da visualização da página|operação/sintético,nuvem/funName|
|performanceCounters/requestExecutionTime|HTTP solicitar tempo de execução|MilliSeconds|Média|Tempo de execução do mais recente pedido.|nuvem/papelInstância|
|performanceCounters/requestsInQueue|Pedidos HTTP na fila de candidaturas|Contagem|Média|Duração da fila de pedidos.|nuvem/papelInstância|
|performanceCounters/requestsPerSecond|Taxa de pedido HTTP|CondePerSecond|Média|Taxa de todos os pedidos para o pedido por segundo a partir de ASP.NET.|nuvem/papelInstância|
|performanceCounters/excepçõesPerSecond|Taxa de exceção|CondePerSecond|Média|Contagem de exceções manuseadas e não manipuladas reportadas às janelas, incluindo exceções .NET e exceções não geridas que são convertidas em exceções .NET.|nuvem/papelInstância|
|performanceCounters/processIOBytesPerSecond|Taxa IO do processo|BytesPerSecond|Média|Total de bytes por segundo lido e escrito para ficheiros, rede e dispositivos.|nuvem/papelInstância|
|performanceCounters/processCpuPercentage|Processo CPU|Percentagem|Média|A percentagem de tempo decorrido que todos os fios de processo utilizaram o processador para executar instruções. Isto pode variar entre 0 a 100. Esta métrica indica apenas o desempenho do processo w3wp.|nuvem/papelInstância|
|performanceCounters/processadorEsPercentage|Tempo do processador|Percentagem|Média|A percentagem de tempo que o processador gasta em fios não ociosos.|nuvem/papelInstância|
|performanceCounters/memoryAvailableBytes|Memória disponível|Bytes|Média|Memória física imediatamente disponível para alocação a um processo ou para uso do sistema.|nuvem/papelInstância|
|performanceCounters/processPrivateBytes|Processar bytes privados|Bytes|Média|Memória exclusivamente atribuída aos processos da aplicação monitorizada.|nuvem/papelInstância|
|pedidos/duração|Tempo de resposta do servidor|MilliSeconds|Média|Tempo entre receber um pedido HTTP e terminar o envio da resposta.|pedido/performanceBucket,pedido/resultadoSCode,operação/sintético,nuvem/roleInstance,request/success,cloud/roleName|
|pedidos/contagem|Pedidos de servidor|Contagem|Contagem|Contagem de pedidos HTTP concluídos.|pedido/performanceBucket,pedido/resultadoSCode,operação/sintético,nuvem/roleInstance,request/success,cloud/roleName|
|pedidos/falhados|Pedidos falhados|Contagem|Contagem|Contagem de pedidos HTTP marcados como falhados. Na maioria dos casos, trata-se de pedidos com um código de resposta >= 400 e não igual a 401.|pedido/performanceBucket,pedido/resultadoSDôr/resultadosDôr/tentar/fazer sucesso,operação/sintético,nuvem/roleInstance,cloud/roleName|
|pedidos/tarifa|Taxa de pedido do servidor|CondePerSecond|Média|Taxa de pedidos de servidor por segundo|pedido/performanceBucket,pedido/resultadoSCode,operação/sintético,nuvem/roleInstance,request/success,cloud/roleName|
|exceções/contagem|Exceções|Contagem|Contagem|Contagem combinada de todas as exceções não conseguidas.|cloud/roleName,cloud/roleInstance,cliente/tipo|
|exceções/navegador|Exceções de browser|Contagem|Contagem|Contagem de exceções não realizadas no navegador.|cliente/isServer,cloud/roleName|
|exceções/servidor|Exceções do servidor|Contagem|Contagem|Contagem de exceções não realizadas lançadas na aplicação do servidor.|cliente/isServer,cloud/roleName,cloud/roleInstance|
|vestígios/contagem|Traces|Contagem|Contagem|Contagem de documentos de rastreio|traço/severidadeLevel,operação/sintético,nuvem/funName,nuvem/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|connectedDeviceCount|Total de dispositivos conectados|Contagem|Média|Número de dispositivos ligados à IoT Central|Nenhum|
|c2d.property.read.success|Propriedade de dispositivo de sucesso lê da IoT Central|Contagem|Total|A contagem de todas as leituras bem sucedidas iniciadas da IoT Central|Nenhum|
|c2d.property.read.failure|Falha na propriedade do dispositivo lê-se da IoT Central|Contagem|Total|A contagem de todas as leituras de propriedade falhada iniciadas a partir da IoT Central|Nenhum|
|d2c.property.read.success|Bem sucedida propriedade do dispositivo lê de dispositivos|Contagem|Total|A contagem de todas as leituras bem sucedidas iniciadas a partir de dispositivos|Nenhum|
|d2c.property.read.failure|Falha na propriedade do dispositivo lê-se a partir de dispositivos|Contagem|Total|A contagem de todas as leituras de propriedade falhada iniciadas a partir de dispositivos|Nenhum|
|c2d.property.update.success|Atualizações de propriedade de dispositivos bem-sucedidos da IoT Central|Contagem|Total|A contagem de todas as atualizações de propriedade bem sucedidas iniciadas a partir da IoT Central|Nenhum|
|c2d.property.update.failure|Atualizações de propriedade de dispositivos falhados da IoT Central|Contagem|Total|A contagem de todas as atualizações de propriedade falhadas iniciadas a partir da IoT Central|Nenhum|
|d2c.property.update.success|Atualizações de propriedade de dispositivos bem-sucedidos a partir de dispositivos|Contagem|Total|A contagem de todas as atualizações de propriedade bem sucedidas iniciadas a partir de dispositivos|Nenhum|
|d2c.property.update.failure|Falhas nas atualizações de propriedade do dispositivo a partir de dispositivos|Contagem|Total|A contagem de todas as atualizações de propriedade falhadas iniciadas a partir de dispositivos|Nenhum|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/cofres

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|ServiceApiHit|Acessos Api de serviço total|Contagem|Contagem|Número de acessos totais de serviço api|ActivityType,ActivityName|
|ServiçoApilatency|Latência geral do serviço Api|Milissegundos|Média|Latência geral dos pedidos de serviço api|ActivityType,ActivityName,StatusCode,StatusCodeClass|
|ServiceApiResult|Resultados totais do serviço Api|Contagem|Contagem|Número de resultados totais da API de serviço|ActivityType,ActivityName,StatusCode,StatusCodeClass|
|SaturaçãoShoebox|Saturação geral do cofre|Percentagem|Média|Capacidade do cofre utilizada|ActivityType,ActivityName,TransactionType|
|Disponibilidade|Disponibilidade geral do cofre|Percentagem|Média|Abóbada solicita disponibilidade|ActivityType,ActivityName,StatusCode,StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|CacheUtilização|Utilização de cache|Percentagem|Média|Nível de utilização no âmbito do cluster|Nenhum|
|Queriaduração|Duração da consulta|Milissegundos|Média|Duração das consultas em segundos|QueriaStatus|
|Ingestionutilização|Utilização da ingestão|Percentagem|Média|Relação das ranhuras de ingestão usadas no cluster|Nenhum|
|KeepAlive|Mantenha-se vivo|Contagem|Média|Verificação de sanidade indica que o cluster responde a consultas|Nenhum|
|IngestionVolumeInMB|Volume de ingestão (em MB)|Contagem|Total|Volume global de dados ingeridos para o cluster (em MB)|Base de Dados|
|IngestionLatencyInSeconds|Latência da ingestão (em segundos)|Segundos|Média|Tempo de ingestão da fonte (por exemplo, a mensagem está no EventHub) para o cluster em segundos|Nenhum|
|EventosProcessadoForEventHubs|Eventos processados (para Centros de Eventos/IoT)|Contagem|Total|Número de eventos processados pelo cluster ao ingerir do Event/IoT Hub|EventStatus|
|IngestionResult|Resultado da ingestão|Contagem|Contagem|Número de operações de ingestão|IngestionResultDetails|
|CPU|CPU|Percentagem|Média|Nível de utilização do CPU|Nenhum|
|ContinuousExportNumOfRecordsExported|Exportação contínua - num de registos exportados|Contagem|Total|Número de registos exportados, disparados para cada artefacto de armazenamento escrito durante a operação de exportação|Nome ContínuoExportName,Base de Dados|
|ExportaçãoUtilização|Utilização da Exportação|Percentagem|Máximo|Utilização das exportações|Nenhum|
|ContínuaExportPendingCount|Contagem pendente de exportação contínua|Contagem|Máximo|O número de postos de trabalho de exportação permanentes pendentes prontos para a execução|Nenhum|
|ContinuaExportMaxLatenessMinutes|Exportação Contínua Max Lateness|Contagem|Máximo|O atraso (em minutos) reportado pelos postos de trabalho contínuos de exportação no cluster|Nenhum|
|ContínuaExportResult|Resultado contínuo da exportação|Contagem|Contagem|Indica se a Exportação Contínua foi bem sucedida ou falhou|Nome deExport Continuous,Resultado,Base de Dados|
|StreamingIngestDuration|Duração do Streaming Ingest|Milissegundos|Média|Duração da ingestão de streaming em milissegundos|Nenhum|
|StreamingIngestDataRate|Taxa de dados de ingestão de streaming|Contagem|Média|Taxa de dados de ingestão de streaming (MB por segundo)|Nenhum|
|SteamingIngestRequestRate|Taxa de pedido de ingestão de streaming|Contagem|RateRequestsPerSecond|Taxa de pedido de ingestão de streaming (pedidos por segundo)|Nenhum|
|StreamingIngestResults|Resultado do streaming inger|Contagem|Média|Resultado do streaming de ingerir|Resultado|
|TotalNumberOfConcurrentQueries|Número total de consultas simultâneas|Contagem|Total|Número total de consultas simultâneas|Nenhum|
|TotalNumberOfThrottledQueries|Número total de consultas estranguladas|Contagem|Total|Número total de consultas estranguladas|Nenhum|
|TotalNumberOfThrottledCommands|Número total de comandos acelerados|Contagem|Total|Número total de comandos acelerados|Tipo de Comando|
|TotalNumberOfExtents|Número total de extensões|Contagem|Total|Número total de extensões de dados|Nenhum|
|InstânciaCount|Contagem de Instâncias|Contagem|Média|Contagem total de casos|Nenhum|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|RunsStarted|Execuções iniciadas|Contagem|Total|O número de fluxos de trabalho iniciados.|Nenhum|
|RunsCompleted|Execuções Concluídas|Contagem|Total|Número de fluxos de trabalho concluídos.|Nenhum|
|RunsSucceed|Runs Succeeded|Contagem|Total|Número de fluxos de trabalho conseguiu.|Nenhum|
|RunsFailed|Runs Falhado|Contagem|Total|O número de fluxos de trabalho falhou.|Nenhum|
|RunsCancelled|Execuções canceladas|Contagem|Total|Número de fluxos de trabalho cancelados.|Nenhum|
|Estação Runlatency|Executar Latência|Segundos|Média|A latência do fluxo de trabalho concluído funciona.|Nenhum|
|RunSuccessLatency|Executar Latência de sucesso|Segundos|Média|A latência do fluxo de trabalho bem sucedido corre.|Nenhum|
|RunThrottledEvents|Executar Eventos Throttled|Contagem|Total|Número de ação de fluxo de trabalho ou eventos acelerados.|Nenhum|
|RunStartThrottledEvents|Executar eventos throttled do início|Contagem|Total|Número de fluxos de trabalho iniciam eventos acelerados.|Nenhum|
|RunFailurePercentage|Percentagem de falha de execução|Percentagem|Total|A percentagem de fluxos de trabalho falhou.|Nenhum|
|AçõesStarted|Ações Iniciadas |Contagem|Total|Número de ações de fluxo de trabalho iniciadas.|Nenhum|
|Ações Computação|Ações Concluídas |Contagem|Total|Número de ações de fluxo de trabalho concluídas.|Nenhum|
|AçõesDuzidas|Ações Bem Sucedidas |Contagem|Total|Número de ações de fluxo de trabalho conseguiu.|Nenhum|
|Ações Destruídas|Ações falhadas |Contagem|Total|O número de ações de fluxo de trabalho falhou.|Nenhum|
|AçõesSkipped|Ações ignoradas |Contagem|Total|Número de ações de fluxo de trabalho ignoradas.|Nenhum|
|Ação|Latência de Ação |Segundos|Média|Latência de ações de fluxo de trabalho concluídas.|Nenhum|
|ActionSuccessLatency|Latência do sucesso da ação |Segundos|Média|Latência de ações de fluxo de trabalho bem sucedidas.|Nenhum|
|ActionThrottledEvents|Eventos throttled de ação|Contagem|Total|Número de eventos de fluxo de trabalho acelerado.|Nenhum|
|GatilhosStarted|Gatilhos Iniciados |Contagem|Total|O número de gatilhos de fluxo de trabalho começou.|Nenhum|
|GatilhosCompleted|Gatilhos Concluídos |Contagem|Total|Número de gatilhos de fluxo de trabalho concluídos.|Nenhum|
|GatilhosSsueded|Gatilhos Bem Sucedidos |Contagem|Total|Número de gatilhos de fluxo de trabalho conseguiu.|Nenhum|
|GatilhosFailed|Gatilhos falhados |Contagem|Total|O número de gatilhos de fluxo de trabalho falhou.|Nenhum|
|GatilhosSkipped|Gatilhos ignorados|Contagem|Total|Número de gatilhos de fluxo de trabalho ignorados.|Nenhum|
|Gatilhos Disparados|Gatilhos disparados |Contagem|Total|Número de gatilhos de fluxo de trabalho disparados.|Nenhum|
|TriggerLatency|Desencadear a latência |Segundos|Média|Latência de gatilhos de fluxo de trabalho concluídos.|Nenhum|
|TriggerFireLatency|Desencadear a latência do fogo |Segundos|Média|Latência de gatilhos de fluxo de trabalho disparados.|Nenhum|
|TriggerSuccessLatency|Desencadear a latência do sucesso |Segundos|Média|Latência de desencadeamentos de fluxo de trabalho bem sucedidos.|Nenhum|
|TriggerThrottledEvents|Eventos aceleradores do gatilho|Contagem|Total|Número de eventos aceleradores de fluxo de trabalho.|Nenhum|
|BillableActionExecutions|Execuções de ação executáveis|Contagem|Total|Número de execuções de fluxo de trabalho a ser cobrado.|Nenhum|
|BillableTriggerExecutions|Execuções de gatilhos faturantes|Contagem|Total|Número de execuções de fluxo de trabalho a serem cobradas.|Nenhum|
|TotalBillableExecutions|Execuções totais faturantes|Contagem|Total|Número de execuções de fluxos de trabalho a ser cobrado.|Nenhum|
|Faturação UsageNativeOperação|Utilização de faturação para execuções de operações nativas|Contagem|Total|Número de execuções de operações nativas a ser cobrado.|Nenhum|
|BillingUsageStandardConnector|Utilização de faturação para execuções padrão do conector|Contagem|Total|Número de execuções padrão de conector a ser faturado.|Nenhum|
|BillingUsageStorageConsumption|Faturação de utilização para execuções de consumo de armazenamento|Contagem|Total|Número de execuções de consumo de armazenamento a ser cobrado.|Nenhum|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|RunsStarted|Execuções iniciadas|Contagem|Total|O número de fluxos de trabalho iniciados.|Nenhum|
|RunsCompleted|Execuções Concluídas|Contagem|Total|Número de fluxos de trabalho concluídos.|Nenhum|
|RunsSucceed|Runs Succeeded|Contagem|Total|Número de fluxos de trabalho conseguiu.|Nenhum|
|RunsFailed|Runs Falhado|Contagem|Total|O número de fluxos de trabalho falhou.|Nenhum|
|RunsCancelled|Execuções canceladas|Contagem|Total|Número de fluxos de trabalho cancelados.|Nenhum|
|Estação Runlatency|Executar Latência|Segundos|Média|A latência do fluxo de trabalho concluído funciona.|Nenhum|
|RunSuccessLatency|Executar Latência de sucesso|Segundos|Média|A latência do fluxo de trabalho bem sucedido corre.|Nenhum|
|RunThrottledEvents|Executar Eventos Throttled|Contagem|Total|Número de ação de fluxo de trabalho ou eventos acelerados.|Nenhum|
|RunStartThrottledEvents|Executar eventos throttled do início|Contagem|Total|Número de fluxos de trabalho iniciam eventos acelerados.|Nenhum|
|RunFailurePercentage|Percentagem de falha de execução|Percentagem|Total|A percentagem de fluxos de trabalho falhou.|Nenhum|
|AçõesStarted|Ações Iniciadas |Contagem|Total|Número de ações de fluxo de trabalho iniciadas.|Nenhum|
|Ações Computação|Ações Concluídas |Contagem|Total|Número de ações de fluxo de trabalho concluídas.|Nenhum|
|AçõesDuzidas|Ações Bem Sucedidas |Contagem|Total|Número de ações de fluxo de trabalho conseguiu.|Nenhum|
|Ações Destruídas|Ações falhadas |Contagem|Total|O número de ações de fluxo de trabalho falhou.|Nenhum|
|AçõesSkipped|Ações ignoradas |Contagem|Total|Número de ações de fluxo de trabalho ignoradas.|Nenhum|
|Ação|Latência de Ação |Segundos|Média|Latência de ações de fluxo de trabalho concluídas.|Nenhum|
|ActionSuccessLatency|Latência do sucesso da ação |Segundos|Média|Latência de ações de fluxo de trabalho bem sucedidas.|Nenhum|
|ActionThrottledEvents|Eventos throttled de ação|Contagem|Total|Número de eventos de fluxo de trabalho acelerado.|Nenhum|
|GatilhosStarted|Gatilhos Iniciados |Contagem|Total|O número de gatilhos de fluxo de trabalho começou.|Nenhum|
|GatilhosCompleted|Gatilhos Concluídos |Contagem|Total|Número de gatilhos de fluxo de trabalho concluídos.|Nenhum|
|GatilhosSsueded|Gatilhos Bem Sucedidos |Contagem|Total|Número de gatilhos de fluxo de trabalho conseguiu.|Nenhum|
|GatilhosFailed|Gatilhos falhados |Contagem|Total|O número de gatilhos de fluxo de trabalho falhou.|Nenhum|
|GatilhosSkipped|Gatilhos ignorados|Contagem|Total|Número de gatilhos de fluxo de trabalho ignorados.|Nenhum|
|Gatilhos Disparados|Gatilhos disparados |Contagem|Total|Número de gatilhos de fluxo de trabalho disparados.|Nenhum|
|TriggerLatency|Desencadear a latência |Segundos|Média|Latência de gatilhos de fluxo de trabalho concluídos.|Nenhum|
|TriggerFireLatency|Desencadear a latência do fogo |Segundos|Média|Latência de gatilhos de fluxo de trabalho disparados.|Nenhum|
|TriggerSuccessLatency|Desencadear a latência do sucesso |Segundos|Média|Latência de desencadeamentos de fluxo de trabalho bem sucedidos.|Nenhum|
|TriggerThrottledEvents|Eventos aceleradores do gatilho|Contagem|Total|Número de eventos aceleradores de fluxo de trabalho.|Nenhum|
|Integração ServiçoEnvironmentWorkflowProcessorUsage|Utilização do processador de fluxo de trabalho para ambiente de serviço de integração|Percentagem|Média|Utilização do processador de fluxo de trabalho para ambiente de serviço de integração.|Nenhum|
|Integração ServiçoEnvironmentOdestrabalhomor|Utilização da memória do fluxo de trabalho para ambiente de serviço de integração|Percentagem|Média|Utilização da memória do fluxo de trabalho para ambiente de serviço de integração.|Nenhum|
|Integração ServiçoEnvironmentConnectorProcessorUsage|Utilização do processador do conector para ambiente de serviço de integração|Percentagem|Média|Utilização do processador de conector para ambiente de serviço de integração.|Nenhum|
|Integração ServiçoEnvironmentConnectorMemoryUsage|Utilização da memória do conector para ambiente de serviço de integração|Percentagem|Média|Utilização da memória do conector para ambiente de serviço de integração.|Nenhum|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Corridas Canceladas|Corridas Canceladas|Contagem|Total|Número de corridas canceladas para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Cancelar execuções solicitadas|Cancelar execuções solicitadas|Contagem|Total|Número de corridas onde foi pedido cancelamento para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Execuções Concluídas|Execuções Concluídas|Contagem|Total|Número de corridas concluídas com sucesso para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Corridas falhadas|Corridas falhadas|Contagem|Total|Número de corridas falhou para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Finalização de Corridas|Finalização de Corridas|Contagem|Total|Número de corridas inscritas no estado finalizado para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Não respondendo corre|Não respondendo corre|Contagem|Total|Número de corridas que não respondem a este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Não começou corre|Não começou corre|Contagem|Total|Número de corridas em estado não iniciado para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Preparação de Corridas|Preparação de Corridas|Contagem|Total|Número de corridas que se preparam para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Corridas de Provisionamento|Corridas de Provisionamento|Contagem|Total|Número de corridas que estão a provisão para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Corridas em Fila|Corridas em Fila|Contagem|Total|Número de corridas que são filas para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Corridas iniciadas|Corridas iniciadas|Contagem|Total|Número de corridas iniciadas para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Execuções ini 'Starting'|Execuções ini 'Starting'|Contagem|Total|Número de corridas iniciadas para este espaço de trabalho|Cenário,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Erros|Erros|Contagem|Total|Número de erros de execução neste espaço de trabalho|Cenário|
|Avisos|Avisos|Contagem|Total|Número de advertências de execução neste espaço de trabalho|Cenário|
|Registo modelo Bem sucedido|Registo modelo Bem sucedido|Contagem|Total|Número de registos de modelos que sucederam neste espaço de trabalho|Cenário|
|Registo modelo falhado|Registo modelo falhado|Contagem|Total|Número de registos de modelos que falharam neste espaço de trabalho|Cenário,Código de Estado|
|Implementação de modelos iniciado|Implementação de modelos iniciado|Contagem|Total|Número de implementações de modelos iniciadas neste espaço de trabalho|Cenário|
|Implementação de modelos conseguiu|Implementação de modelos conseguiu|Contagem|Total|Número de implementações de modelos que foram bem sucedidas neste espaço de trabalho|Cenário|
|Implementação de modelo falhou|Implementação de modelo falhou|Contagem|Total|Número de implementações de modelos que falharam neste espaço de trabalho|Cenário,Código de Estado|
|Nómada Total|Nómada Total|Contagem|Média|Número de nós totais. Este total inclui alguns dos nóns ativos, nóns ociosos, nóns inutilizáveis, nóns pré-identificados, deixando os nóns|Cenário,ClusterName|
|Nóns ativos|Nóns ativos|Contagem|Média|Número de nós Acitve. Estes são os nós que estão ativamente a gerir um trabalho.|Cenário,ClusterName|
|Nódoas Ociosas|Nódoas Ociosas|Contagem|Média|Número de nós ociosos. Os nós inativos são os nós que não estão a gerir nenhum emprego, mas que podem aceitar novos empregos se disponíveis.|Cenário,ClusterName|
|Nódes Inutilizáveis|Nódes Inutilizáveis|Contagem|Média|Número de nós inutilizáveis. Os nós inutilizáveis não estão funcionais devido a algum problema irresolúvel. Azure vai reciclar estes nós.|Cenário,ClusterName|
|Nómadas Preempted|Nómadas Preempted|Contagem|Média|Número de nós preempted. Estes nós são os nós de baixa prioridade que são retirados da piscina de nós disponíveis.|Cenário,ClusterName|
|Deixando os nóns|Deixando os nóns|Contagem|Média|Número de nós de saída. Deixando nós são os nós que acabaram de processar um trabalho e irão para o estado de Idle.|Cenário,ClusterName|
|Núcleos Totais|Núcleos Totais|Contagem|Média|Número de núcleos totais|Cenário,ClusterName|
|Núcleos Ativos|Núcleos Ativos|Contagem|Média|Número de núcleos ativos|Cenário,ClusterName|
|Núcleos Ociosos|Núcleos Ociosos|Contagem|Média|Número de núcleos ociosos|Cenário,ClusterName|
|Núcleos inutilizáveis|Núcleos inutilizáveis|Contagem|Média|Número de núcleos inutilizáveis|Cenário,ClusterName|
|Núcleos Preempted|Núcleos Preempted|Contagem|Média|Número de núcleos pré-apropriados|Cenário,ClusterName|
|Deixando os Núcleos|Deixando os Núcleos|Contagem|Média|Número de núcleos de saída|Cenário,ClusterName|
|Percentagem de Utilização de Cots|Percentagem de Utilização de Cots|Contagem|Média|Por cento das quotas utilizadas|Cenário,ClusterName,VmFamilyName,VmPriority|
|CpuUtilização|CpuUtilização|Contagem|Média|CPU (Pré-visualização)|Cenário,runId,NodeId,CreatedTime|
|GpuUtilização|GpuUtilização|Contagem|Média|GPU (Pré-visualização)|Cenário,runId,NodeId,CreatedTime,DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/contas

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Utilização|Utilização|Contagem|Contagem|Contagem de chamadas da API|ApiCategory,ApiName,ResultadoType,ResponseCode|
|Disponibilidade|Disponibilidade|Percentagem|Média|Disponibilidade das APIs|ApiCategory,ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Saída|Saída|Bytes|Total|A quantidade de dados da Egress, em bytes.|OutputFormat|
|SucessoE2ELatency|Fim do sucesso para acabar com a latência|Milissegundos|Média|A latência média para pedidos bem sucedidos em milissegundos.|OutputFormat|
|Pedidos|Pedidos|Contagem|Total|Pedidos para um Ponto Final de Streaming.|OutputFormat,HttpStatusCode,ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|AssetQuota|Quota de ativos|Contagem|Média|Quantos ativos são permitidos para a conta de serviço de mídia atual|Nenhum|
|Conta de Ativos|Contagem de ativos|Contagem|Média|Quantos ativos já são criados na conta de serviço de mídia corrente|Nenhum|
|AssetQuotaUsedPercentage|Percentagem de quota de ativos utilizada|Percentagem|Média|Percentagem de ativos usados na conta de serviço de mídia corrente|Nenhum|
|ContentKeyPolicyQuota|Quota de política chave de conteúdo|Contagem|Média|Quantos polícias chave de conteúdo são permitidos para a conta de serviço de mídia atual|Nenhum|
|ContentKeyPolicyCount|Contagem de políticas de chave de conteúdo|Contagem|Média|Quantas políticas-chave de conteúdo já estão criadas na conta de serviço de mídia atual|Nenhum|
|ContentKeyPolicyQuotaUsedPercentage|Quota de política de conteúdo utilizada|Percentagem|Média|Política chave de conteúdo usada percentagem na conta de serviço de mídia atual|Nenhum|
|StreamingPolicyQuota|Quota de política de streaming|Contagem|Média|Quantas políticas de streaming são permitidas para a conta atual do serviço de mídia|Nenhum|
|StreamingPolicyCount|Contagem de políticas de streaming|Contagem|Média|Quantas políticas de streaming já estão criadas na conta de serviço de mídia atual|Nenhum|
|StreamingPolicyQuotaUsedPercentage|Percentagem utilizada pela quota de política de streaming|Percentagem|Média|Política de Streaming usada percentualmente na conta de serviço de mídia corrente|Nenhum|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft.MixedReality/remoteRenderIngS

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|ActivosConvertidos|Ativos Convertidos|Contagem|Total|Número total de ativos convertidos|AppId,ResourceId,SDKVersion|
|Ativaressessões de Esessão|Sessões de renderização ativa|Contagem|Total|Número total de sessões de renderização ativas|AppId,ResourceId,SessionType,SDKVersion|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Média DeOncência|Latência média de leitura|MilliSeconds|Média|Latência média de leitura em milissegundos por operação|Nenhum|
|Média Desaquimia|Latência média de escrita|MilliSeconds|Média|Latência escrita média em milissegundos por operação|Nenhum|
|VolumeLogicalsize|Tamanho consumido do volume|Bytes|Média|Tamanho lógico do volume (bytes usados)|Nenhum|
|VolumeSnapshotSize|Tamanho do instantâneo de volume|Bytes|Média|Tamanho de todos os instantâneos em volume|Nenhum|
|ReadIops|Ler iops|CondePerSecond|Média|Ler operações de in/out por segundo|Nenhum|
|WriteIops|Escrever iops|CondePerSecond|Média|Escrever operações de in/out por segundo|Nenhum|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Piscina atribuída ao tamanho do volume|Bytes|Média|Tamanho utilizado atribuído da piscina|Nenhum|
|VolumePoolTotalLogicalsize|Tamanho consumido da piscina|Bytes|Média|Soma do tamanho lógico de todos os volumes pertencentes à piscina|Nenhum|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|BytesSentRate|Bytes Enviados|Bytes|Total|Número de bytes enviados pela Interface de Rede|Nenhum|
|BytesReceivedRate|Bytes Recebidos|Bytes|Total|Número de bytes que a Interface de Rede recebeu|Nenhum|
|PacotesSEntRate|Pacotes Enviados|Contagem|Total|Número de pacotes enviados pela Interface de Rede|Nenhum|
|PacketsReceivedRate|Pacotes Recebidos|Contagem|Total|Número de pacotes que a Interface de Rede recebeu|Nenhum|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|VipSAdability|Disponibilidade do caminho dos dados|Contagem|Média|Disponibilidade média de trajetória do balanceador de carga por duração do tempo|FrontendIPAddress,FrontendPort|
|DipDUilability|Estado da sonda de saúde|Contagem|Média|Estado médio da sonda de saúde do balanceador de carga por duração do tempo|ProtocolType,BackendPort,FrontendIPAddress,FrontendPort,BackendIPAddress|
|ByteCount|Byte Count|Contagem|Total|Número total de Bytes transmitidos dentro do período de tempo|FrontendIPAddress,FrontendPort,Direction|
|PacoteCount|Contagem de pacotes|Contagem|Total|Número total de Pacotes transmitidos dentro do período de tempo|FrontendIPAddress,FrontendPort,Direction|
|SYNCount|Contagem de SINA|Contagem|Total|Número total de pacotes SYN transmitidos dentro do período de tempo|FrontendIPAddress,FrontendPort,Direction|
|SnatConnectionCount|Contagem de ligação SNAT|Contagem|Total|Número total de novas ligações SNAT criadas dentro do período de tempo|FrontendIPAddress,BackendIPAddress,ConnectionState|
|AlocadosSnatPorts|Portas SNAT atribuídas (Pré-visualização)|Contagem|Total|Número total de portas SNAT atribuídas dentro do período de tempo|FrontendIPAddress,BackendIPAddress,ProtocolType,IsAwaitingRemoval|
|UssnatPorts|Portas SNAT usadas (pré-visualização)|Contagem|Total|Número total de portas SNAT utilizadas dentro do período de tempo|FrontendIPAddress,BackendIPAddress,ProtocolType,IsAwaitingRemoval|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|QueryVolume|Volume de consulta|Contagem|Total|Número de consultas servidas para uma zona de DNS|Nenhum|
|RecordSetCount|Contagem de recordes|Contagem|Máximo|Número de conjuntos de recordes numa zona de DNS|Nenhum|
|RecordSetCapacityUtilização|Record Fixo Utilização da Capacidade|Percentagem|Máximo|Por cento da capacidade do Conjunto recorde utilizada por uma zona de DNS|Nenhum|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|PacotesInDDoS|Pacotes de entrada DDoS|CondePerSecond|Máximo|Pacotes de entrada DDoS|Nenhum|
|PacotesDroppedDDoS|Pacotes de entrada deixaram cair DDoS|CondePerSecond|Máximo|Pacotes de entrada deixaram cair DDoS|Nenhum|
|PacotesForwardedDDoS|Pacotes de entrada reencaminhados DDoS|CondePerSecond|Máximo|Pacotes de entrada reencaminhados DDoS|Nenhum|
|TCPPacketsInDDoS|Pacotes TCP de entrada DDoS|CondePerSecond|Máximo|Pacotes TCP de entrada DDoS|Nenhum|
|TCPPacketsDroppedDDoS|Pacotes TCP de entrada deixaram cair DDoS|CondePerSecond|Máximo|Pacotes TCP de entrada deixaram cair DDoS|Nenhum|
|TCPPacketsForwardedDDoS|Pacotes TCP de entrada reencaminhados DDoS|CondePerSecond|Máximo|Pacotes TCP de entrada reencaminhados DDoS|Nenhum|
|UDPPacketsInDDoS|Pacotes de UDP de entrada DDoS|CondePerSecond|Máximo|Pacotes de UDP de entrada DDoS|Nenhum|
|UDPPacketsDroppedDDoS|Pacotes UDP de entrada deixaram cair DDoS|CondePerSecond|Máximo|Pacotes UDP de entrada deixaram cair DDoS|Nenhum|
|UDPPacketsForwardedDDoS|Pacotes UDP de entrada reencaminhados DDoS|CondePerSecond|Máximo|Pacotes UDP de entrada reencaminhados DDoS|Nenhum|
|BytesInDDoS|Bytes de entrada DDoS|BytesPerSecond|Máximo|Bytes de entrada DDoS|Nenhum|
|BytesDroppedDDoS|Bytes de entrada deixaram cair DDoS|BytesPerSecond|Máximo|Bytes de entrada deixaram cair DDoS|Nenhum|
|BytesForwardedDDoS|Bytes de entrada reencaminhado DDoS|BytesPerSecond|Máximo|Bytes de entrada reencaminhado DDoS|Nenhum|
|TCPBytesInDDoS|Entrada TCP bytes DDoS|BytesPerSecond|Máximo|Entrada TCP bytes DDoS|Nenhum|
|TCPBytesDroppedDDoS|Bytes TCP de entrada deixaram cair DDoS|BytesPerSecond|Máximo|Bytes TCP de entrada deixaram cair DDoS|Nenhum|
|TCPBytesForwardedDDoS|Inbound TCP bytes reencaminhado DDoS|BytesPerSecond|Máximo|Inbound TCP bytes reencaminhado DDoS|Nenhum|
|UDPBytesInDDoS|UDP bytes DDoS de entrada|BytesPerSecond|Máximo|UDP bytes DDoS de entrada|Nenhum|
|UDPBytesDroppedDDoS|Bytes udp de entrada deixaram cair DDoS|BytesPerSecond|Máximo|Bytes udp de entrada deixaram cair DDoS|Nenhum|
|UDPBytesForwardedDDoS|Bytes de UDP de entrada reencaminhados DDoS|BytesPerSecond|Máximo|Bytes de UDP de entrada reencaminhados DDoS|Nenhum|
|IfUnderDDosAttack|Sob o ataque do DDoS ou não|Contagem|Máximo|Sob o ataque do DDoS ou não|Nenhum|
|Pacotes DDoSTriggerTCP|Pacotes TCP de entrada para desencadear mitigação do DDoS|CondePerSecond|Máximo|Pacotes TCP de entrada para desencadear mitigação do DDoS|Nenhum|
|DDoSTriggerUDPPackets|Pacotes UDP de entrada para desencadear mitigação do DDoS|CondePerSecond|Máximo|Pacotes UDP de entrada para desencadear mitigação do DDoS|Nenhum|
|DDoSTriggerSYNPackets|Pacotes SYN de entrada para desencadear a mitigação do DDoS|CondePerSecond|Máximo|Pacotes SYN de entrada para desencadear a mitigação do DDoS|Nenhum|
|VipSAdability|Disponibilidade do caminho dos dados|Contagem|Média|Disponibilidade média de endereço IP por duração de tempo|Porta|
|ByteCount|Byte Count|Contagem|Total|Número total de Bytes transmitidos dentro do período de tempo|Porto,Direção|
|PacoteCount|Contagem de pacotes|Contagem|Total|Número total de Pacotes transmitidos dentro do período de tempo|Porto,Direção|
|SynCount|Contagem de SINA|Contagem|Total|Número total de pacotes SYN transmitidos dentro do período de tempo|Porto,Direção|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Hora de ida e volta para Pings a um VM|MilliSeconds|Média|Tempo de ida e volta para Pings enviado para um VM de destino|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Pings falhados para um VM|Percentagem|Média|Por cento do número de pings falhados para o total enviado Pings de um VM destino|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|AplicaçãoRuleHit|As regras de aplicação atingem a contagem|Contagem|Total|Número de vezes que as regras de aplicação foram atingidas|Estado,Razão,Protocolo|
|NetworkRuleHit|Regras de rede atingem a contagem|Contagem|Total|Número de vezes que as regras da rede foram atingidas|Estado,Razão,Protocolo|
|FirewallHealth|Estado de saúde de firewall|Percentagem|Média|Estado de saúde de firewall|Estado,Razão|
|Dados Processados|Dados processados|Bytes|Total|Quantidade total de dados processados por Firewall|Nenhum|
|SNATPortutilization|Utilização do porto de SNAT|Percentagem|Média|Utilização do porto de SNAT|Nenhum|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Débito|Débito|BytesPerSecond|Média|Número de bytes por segundo que o Gateway de Aplicação serviu|Nenhum|
|Não-saudávelHostCount|Contagem de anfitriões pouco saudável|Contagem|Média|Número de anfitriões insalubres|BackendSettingsPool|
|HealthyHostCount|Contagem saudável do anfitrião|Contagem|Média|Número de hospedeiros saudáveis|BackendSettingsPool|
|TotalRequests|Total de Pedidos|Contagem|Total|Contagem de pedidos bem sucedidos que a Application Gateway serviu|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Pedidos por minuto por Anfitrião Saudável|Contagem|Média|Contagem média de pedidos por minuto por hospedeiro saudável de backend em uma piscina|BackendSettingsPool|
|Requessos Falhados|Pedidos com Falhas|Contagem|Total|Contagem de pedidos falhados que a Application Gateway serviu|BackendSettingsPool|
|Estatísticas de Resposta|Estado de resposta|Contagem|Total|Estado de resposta http devolvido pelo Application Gateway|Grupo HttpStatus|
|Correntes Deconhecões|Conexões atuais|Contagem|Total|Contagem de ligações atuais estabelecidas com Gateway de Aplicação|Nenhum|
|NewConnectionsPerSecond|Novas ligações por segundo|CondePerSecond|Média|Novas ligações por segundo estabelecidas com Application Gateway|Nenhum|
|CpuUtilização|Utilização da CPU|Percentagem|Média|Utilização atual do CPU do Gateway de Aplicações|Nenhum|
|Unidades de capacidade|Unidades de Capacidade Corrente|Contagem|Média|Unidades de capacidade consumidas|Nenhum|
|Unidades de Capacidade DeBilizáveis Fixas|Unidades de Capacidade Faturadas Fixas|Contagem|Média|Unidades de capacidade mínima que serão carregadas|Nenhum|
|Vulnerabilidades estimadas da capacidade de pagamento|Unidades de Capacidade Faturadas Estimadas|Contagem|Média|Unidades de capacidade estimadas que serão carregadas|Nenhum|
|Unidades computeuções|Unidades de Computação Atuais|Contagem|Média|Unidades de computação consumidas|Nenhum|
|BackendResponseStatus|Estado de resposta de backend|Contagem|Total|O número de códigos de resposta HTTP gerados pelos membros backend. Isto não inclui quaisquer códigos de resposta gerados pelo Gateway de Aplicação.|BackendServer,BackendPool,BackendHttpSetting,HttpStatusGroup|
|TlsProtocol|Protocolo TLS do Cliente|Contagem|Total|O número de pedidos TLS e não-TLS iniciados pelo cliente que estabeleceu a ligação com o Gateway de Aplicação. Para visualizar a distribuição do protocolo TLS, filtre pela dimensão do Protocolo TLS.|Ouvinte,TlsProtocol|
|BytesSent|Bytes Enviados|Bytes|Total|O número total de bytes enviados pela Porta de Aplicação aos clientes|Serviço de Escuta|
|BytesReceived|Bytes Recebidos|Bytes|Total|O número total de bytes recebidos pela Gateway de Aplicação dos clientes|Serviço de Escuta|
|ClientRtt|RTT cliente|MilliSeconds|Média|Tempo médio de ida e volta entre clientes e Application Gateway. Esta métrica indica quanto tempo demora a estabelecer ligações e reconhecimentos de retorno|Serviço de Escuta|
|ApplicationGatewayTotalTime|Tempo total do Gateway de Aplicação|MilliSeconds|Média|O tempo médio que leva para que um pedido seja processado e a sua resposta seja enviada. Isto é calculado como média do intervalo a partir do momento em que o Application Gateway recebe o primeiro byte de um pedido HTTP ao momento em que a operação de envio de resposta termina. É importante notar que isto geralmente inclui o tempo de processamento do Gateway de Aplicação, tempo que os pacotes de pedido e resposta estão viajando através da rede e o tempo que o servidor backend demorou a responder.|Serviço de Escuta|
|BackendConnectTime|Tempo de ligação de backend|MilliSeconds|Média|Tempo gasto a estabelecer uma ligação com um servidor de backend|Ouvinte,BackendServer,BackendPool,BackendHttpSetting|
|BackendFirstByteResponseTime|Tempo de resposta de backend First Byte|MilliSeconds|Média|Intervalo de tempo entre o início do estabelecimento de uma ligação ao servidor backend e a receção do primeiro byte do cabeçalho de resposta, aproximando o tempo de processamento do servidor backend|Ouvinte,BackendServer,BackendPool,BackendHttpSetting|
|BackendLastByteResponseTime|Tempo de resposta de Backend Last Byte|MilliSeconds|Média|Intervalo de tempo entre o início do estabelecimento de uma ligação ao servidor backend e a receção do último byte do corpo de resposta|Ouvinte,BackendServer,BackendPool,BackendHttpSetting|
|MatchedCount|Firewall de aplicação web v1 Distribuição total de regras|Contagem|Total|Firewall de aplicação web v1 Distribuição total de regras para o tráfego de entrada|RuleGroup,RuleId|
|Contagem bloqueada|Firewall de aplicação web v1 bloqueado pedidos de distribuição de regras|Contagem|Total|Web Application Firewall v1 bloqueado pedidos de distribuição de regras|RuleGroup,RuleId|
|BlockedReqCount|Firewall de aplicação web v1 Contagem de pedidos bloqueados|Contagem|Total|Web Application Firewall v1 contagem de pedidos bloqueados|Nenhum|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Largura média da largura|Largura de banda Gateway S2S|BytesPerSecond|Média|Largura de banda média local-local de um gateway in bytes por segundo|Nenhum|
|P2SBandwidth|Largura de banda Gateway P2S|BytesPerSecond|Média|Largura de banda média ponto-a-local de uma porta de entrada em bytes por segundo|Nenhum|
|P2SConnectionCount|Contagem de ligação P2S|Contagem|Máximo|Contagem de ligação ponto-a-local de um gateway|Protocolo|
|TunnelAverageBandwidth|Largura de banda do túnel|BytesPerSecond|Média|Largura de banda média de um túnel em bytes por segundo|Nome de conexão,RemoteIP|
|TunnelEgressBytes|Túnel Egress Bytes|Bytes|Total|Bytes de saída de um túnel|Nome de conexão,RemoteIP|
|TúnelIngsBytes|Túnel Ingress Bytes|Bytes|Total|Bytes de entrada de um túnel|Nome de conexão,RemoteIP|
|TunnelEgressPackets|Pacotes de Egress do Túnel|Contagem|Total|Contagem de pacotes de saída de um túnel|Nome de conexão,RemoteIP|
|Pacotes de escavação|Pacotes de Entrada de Túnel|Contagem|Total|Contagem de pacotes de entrada de um túnel|Nome de conexão,RemoteIP|
|TunnelEgressPacketDropTSMismatch|Queda de pacote de desajustada do túnel Egress TS|Contagem|Total|Contagem de entrega de pacote de saída do desfasamento do seletor de tráfego de um túnel|Nome de conexão,RemoteIP|
|TunnelIngressPacketDropTSMismatch|Túnel Ingress TS Desajuste Gota de Pacote|Contagem|Total|Contagem de gotas de pacote de entrada do seletor de tráfego desfasamento de um túnel|Nome de conexão,RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Contagem|Média|Nível de luz Rx em dBm|Link,Lane|
|TxLightLevel|TxLightLevel|Contagem|Média|Nível de luz Tx em dBm|Link,Lane|
|Estado Administrativo|Estado Administrativo|Contagem|Média|Estado administrativo do porto|Ligação|
|LineProtocol|LineProtocol|Contagem|Média|Estado do protocolo de linha da porta|Ligação|
|PortBitsInPerSecond|BitsInPerSecond|CondePerSecond|Média|Bits ingressando Azure por segundo|Ligação|
|PortBitsOutPerSecond|BitsOutPerSecond|CondePerSecond|Média|Bits eja Azure por segundo|Ligação|



## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CondePerSecond|Média|Bits ingressando Azure por segundo|Type de peering|
|BitsOutPerSecond|BitsOutPerSecond|CondePerSecond|Média|Bits eja Azure por segundo|Type de peering|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|CondePerSecond|Média|Bits ingressando Azure por segundo|PeeredCircuitsKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|CondePerSecond|Média|Bits eja Azure por segundo|PeeredCircuitsKey|
|BgpAilability|Disponibilidade BGP|Percentagem|Média|Disponibilidade de BGP da MSEE para todos os pares.|PeeringType,Peer|
|ArpAilability|Disponibilidade ARP|Percentagem|Média|Disponibilidade de ARP da MSEE para todos os pares.|PeeringType,Peer|
|QosDropBitsInPerSecond|DropInBitsPerSecond|CondePerSecond|Média|Ingress bits de dados caiu por segundo|Nenhum|
|QosDropBitsOutPerSecond|DropOutBitsPerSecond|CondePerSecond|Média|Pedaços de dados de Egress caíram por segundo|Nenhum|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CondePerSecond|Média|Bits ingressando Azure por segundo|Nenhum|
|BitsOutPerSecond|BitsOutPerSecond|CondePerSecond|Média|Bits eja Azure por segundo|Nenhum|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/conexões

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CondePerSecond|Média|Bits ingressando Azure por segundo|Nenhum|
|BitsOutPerSecond|BitsOutPerSecond|CondePerSecond|Média|Bits eja Azure por segundo|Nenhum|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|CondePerSecond|Média|Bits ingressando Azure por segundo|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|CondePerSecond|Média|Bits eja Azure por segundo|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|QpsByEndpoint|Consultas por Endpoint Devolvido|Contagem|Total|Número de vezes que um ponto final do Gestor de Tráfego foi devolvido no prazo dado|Nome de ponto final|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Estado do ponto final por Endpoint|Contagem|Máximo|1 se o estado da sonda de um ponto final for "Ativado", 0 caso contrário.|Nome de ponto final|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Sondas Falhadas|Percentagem|Média|% das sondas de monitorização da conectividade falharam|Nenhum|
|Médias Desvelundos|Avg. Tempo de ida e volta (ms)|MilliSeconds|Média|Tempo médio de ida e volta (ms) para sondas de monitorização da conectividade enviadas entre a fonte e o destino|Nenhum|
|ChequesFailedPercent|Cheques Falhados Por cento (Pré-visualização)|Percentagem|Média|% dos controlos de monitorização da conectividade falharam|SourceAddress,SourceName,SourceResourceId,SourceType,Protocol,DestinationAddress,DestinationName,DestinationResourceId,DestinationType,DestinationPort,TestGroupName,TestConfigurationName|
|RoundTripTimeMs|Tempo de viagem redonda (ms) (pré-visualização)|MilliSeconds|Média|Tempo de ida e volta em milissegundos para os controlos de conectividade|SourceAddress,SourceName,SourceResourceId,SourceType,Protocol,DestinationAddress,DestinationName,DestinationResourceId,DestinationType,DestinationPort,TestGroupName,TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|PedidoCount|Número de Pedidos|Contagem|Total|O número de pedidos de clientes servidos pelo representante http/s|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|Solicitação|Tamanho do pedido|Bytes|Total|O número de bytes enviados como pedidos de clientes para o proxy HTTP/S|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|Tamanho das respostas|Tamanho da resposta|Bytes|Total|O número de bytes enviados como respostas de http/S proxy aos clientes|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|BillableResponseSize|Tamanho da resposta faturada|Bytes|Total|O número de bytes faturantes (mínimo 2KB por pedido) enviados como respostas do proxy HTTP/S aos clientes.|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|BackendRequestCount|Contagem de pedidos de backend|Contagem|Total|O número de pedidos enviados do representante http/S para backends|HttpStatus,HttpStatusGroup,Backend|
|BackendRequestatency|Pedido de backend Latência|MilliSeconds|Média|O tempo calculado a partir do momento em que o pedido foi enviado pelo representante HTTP/S para o backend até que o representante HTTP/S recebeu o último byte de resposta a partir do backend|Back-end|
|TotalLatency|Latência total|MilliSeconds|Média|O tempo calculado a partir do momento em que o pedido do cliente foi recebido pelo representante HTTP/S até que o cliente reconhecesse o último byte de resposta do representante HTTP/S|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|BackendHealthPercentage|Percentagem de Saúde backend|Percentagem|Média|A percentagem de sondas de saúde bem sucedidas do representante http/s para backends|Backend,BackendPool|
|WebApplicationFirewallRequestCount|Contagem de pedidos de firewall de aplicação web|Contagem|Total|O número de pedidos de cliente processados pela Firewall de Aplicação Web|Nome de Política,Nome de Regras,Ação|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft.Network/privateDnsZones

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|QueryVolume|Volume de consulta|Contagem|Total|Número de consultas servidas para uma zona privada de DNS|Nenhum|
|RecordSetCount|Contagem de recordes|Contagem|Máximo|Número de conjuntos de recordes numa zona privada de DNS|Nenhum|
|RecordSetCapacityUtilização|Record Fixo Utilização da Capacidade|Percentagem|Máximo|Por cento da capacidade do Conjunto recorde utilizada por uma zona privada de DNS|Nenhum|
|VirtualNetworkLinkCount|Contagem de link de rede virtual|Contagem|Máximo|Número de Redes Virtuais ligadas a uma zona privada de DNS|Nenhum|
|VirtualNetworkLinkLinkCapacityUtilization|Utilização da capacidade de ligação da rede virtual|Percentagem|Máximo|Por cento da capacidade de Ligação de Rede Virtual utilizada por uma zona privada de DNS|Nenhum|
|VirtualNetworkWithRegistrationLinkCount|Contagem de link de registo de rede virtual|Contagem|Máximo|Número de Redes Virtuais ligadas a uma zona privada de DNS com registo automático|Nenhum|
|VirtualNetworkWithRegistrationCapacityUtilization|Utilização da capacidade de utilização da capacidade de registo de rede virtual|Percentagem|Máximo|Por cento da Ligação de Rede Virtual com capacidade de registo automático utilizada por uma zona privada de DNS|Nenhum|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|registro.todos|Operações de Registo|Contagem|Total|A contagem de todas as operações de registo bem sucedidas (criações atualiza consultas e supressões). |Nenhum|
|registration.create|Criar Operações de Criação de Registos|Contagem|Total|A contagem de todas as criações de registo bem sucedidas.|Nenhum|
|registration.update|Operações de Atualização de Registo|Contagem|Total|A contagem de todas as atualizações de registo bem sucedidas.|Nenhum|
|registration.get|Operações de Leitura de Registo|Contagem|Total|A contagem de todas as consultas de registo bem sucedidas.|Nenhum|
|registration.delete|Operações de Eliminação de Registos|Contagem|Total|A contagem de todas as supressões de registo bem sucedidas.|Nenhum|
|entrada|Mensagens recebidas|Contagem|Total|A contagem de todos os bem sucedidos enviar chamadas da API. |Nenhum|
|entrada.programado|Notificações de push agendadas enviadas|Contagem|Total|Notificações de push programadas canceladas|Nenhum|
|incoming.scheduled.cancel|Notificações de push programadas canceladas|Contagem|Total|Notificações de push programadas canceladas|Nenhum|
|agendado.pendente|Notificações Agendadas Pendentes|Contagem|Total|Notificações Agendadas Pendentes|Nenhum|
|instalação.todos|Operações de Gestão de Instalações|Contagem|Total|Operações de Gestão de Instalações|Nenhum|
|instalação.get|Obter Operações de Instalação|Contagem|Total|Obter Operações de Instalação|Nenhum|
|instalação.upsert|Criar ou atualizar operações de instalação|Contagem|Total|Criar ou atualizar operações de instalação|Nenhum|
|instalação.patch|Operações de instalação de remendos|Contagem|Total|Operações de instalação de remendos|Nenhum|
|instalação.excluir|Excluir operações de instalação|Contagem|Total|Excluir operações de instalação|Nenhum|
|outgoing.allpns.sucesso|Notificações bem sucedidas|Contagem|Total|A contagem de todas as notificações bem sucedidas.|Nenhum|
|outgoing.allpns.invalidpayload|Erros de carga útil|Contagem|Total|A contagem de impulsos que falharam porque o PNS devolveu um erro de carga útil.|Nenhum|
|outgoing.allpns.pnserror|Erros do sistema de notificação externa|Contagem|Total|A contagem de impulsos que falharam porque havia um problema de comunicação com o PNS (exclui problemas de autenticação).|Nenhum|
|outgoing.allpns.channelerror|Erros do Canal|Contagem|Total|A contagem de impulsos que falharam porque o canal era inválido não estava associado à aplicação correta estrangulada ou expirada.|Nenhum|
|outgoing.allpns.badorexpiredchannel|Erros do Canal Mau ou Expirados|Contagem|Total|A contagem de impulsos que falharam porque o canal/token/registrationId no registo expirou ou inválido.|Nenhum|
|outgoing.wns.sucesso|Notificações bem sucedidas da WNS|Contagem|Total|A contagem de todas as notificações bem sucedidas.|Nenhum|
|outgoing.wns.invalidcredentis|Erros de autorização WNS (Credenciais Inválidas)|Contagem|Total|A contagem de impulsos que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas. (O Windows Live não reconhece as credenciais).|Nenhum|
|outgoing.wns.badchannel|Erro do canal WNS|Contagem|Total|A contagem de impulsos que falharam porque o ChannelURI no registo não foi reconhecido (estatuto WNS: 404 não encontrado).|Nenhum|
|outgoing.wns.expiredchannel|Erro do canal expirado da WNS|Contagem|Total|A contagem de impulsos que falharam porque o ChannelURI expirou (estado WNS: 410 Gone).|Nenhum|
|outgoing.wns.throttled|Notificações throttled WNS|Contagem|Total|A contagem de impulsos que falharam porque a WNS está a estrangular esta aplicação (estado WNS: 406 Não Aceitável).|Nenhum|
|outgoing.wns.tokenproviderunreachable|Erros de autorização WNS (inacessíveis)|Contagem|Total|O Windows Live não é acessível.|Nenhum|
|outgoing.wns.invalidtoken|Erros de autorização da WNS (token inválido)|Contagem|Total|O token fornecido à WNS não é válido (estado WNS: 401 Não Autorizado).|Nenhum|
|outgoing.wns.wrongtoken|Erros de autorização da WNS (Ficha Errada)|Contagem|Total|O token fornecido à WNS é válido, mas para outra aplicação (estatuto WNS: 403 Proibido). Isto pode acontecer se o ChannelURI no registo estiver associado a outra aplicação. Verifique se a aplicação do cliente está associada à mesma app cujas credenciais estão no centro de notificação.|Nenhum|
|outgoing.wns.invalidnotificationformat|Formato de notificação inválida da WNS|Contagem|Total|O formato da notificação é inválido (estado WNS: 400). Note que a WNS não rejeita todas as cargas inválidas.|Nenhum|
|outgoing.wns.invalidnotificationsize|Erro de tamanho de notificação inválido da WNS|Contagem|Total|A carga útil da notificação é demasiado grande (estado WNS: 413).|Nenhum|
|outgoing.wns.channelthrottled|Canal WNS Acelerado|Contagem|Total|A notificação foi retirada porque o ChannelURI no registo é acelerado (cabeçalho de resposta WNS: X-WNS-NotificationStatus:channelThrottled).|Nenhum|
|outgoing.wns.channeldisconnected|Canal WNS desligado|Contagem|Total|A notificação foi retirada porque o ChannelURI no registo é acelerado (cabeçalho de resposta WNS: X-WNS-DeviceConnectionStatus: desligado).|Nenhum|
|outgoing.wns.drop|Notificações retiradas da WNS|Contagem|Total|A notificação foi retirada porque o ChannelURI no registo é acelerado (X-WNS-NotificationStatus: caiu, mas não X-WNS-DeviceConnectionStatus: desligado).|Nenhum|
|outgoing.wns.pnserror|Erros do WNS|Contagem|Total|Notificação não entregue devido a erros de comunicação com a WNS.|Nenhum|
|outgoing.wns.authenticationerror|Erros de autenticação WNS|Contagem|Total|Notificação não entregue devido a erros de comunicação com credenciais inválidas do Windows Live ou token errado.|Nenhum|
|outgoing.apns.sucesso|Notificações bem sucedidas da APNS|Contagem|Total|A contagem de todas as notificações bem sucedidas.|Nenhum|
|outgoing.apns.invalidcredentis|Erros de Autorização da APNS|Contagem|Total|A contagem de impulsos que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhum|
|outgoing.apns.badchannel|Erro do Canal Mau da APNS|Contagem|Total|A contagem de impulsos que falharam porque o token é inválido (código de estado DA APNS: 8).|Nenhum|
|outgoing.apns.expiredchannel|Erro do Canal caducado da APNS|Contagem|Total|A contagem de fichas que foram invalidadas pelo canal de feedback da APNS.|Nenhum|
|outgoing.apns.invalidnotificationsize|Erro de tamanho de notificação inválido da APNS|Contagem|Total|A contagem de impulsos que falharam porque a carga era demasiado grande (código de estado DA APNS: 7).|Nenhum|
|outgoing.apns.pnserror|Erros da APNS|Contagem|Total|A contagem de impulsos que falhou devido a erros de comunicação com a APNS.|Nenhum|
|outgoing.gcm.sucesso|Notificações bem sucedidas da GCM|Contagem|Total|A contagem de todas as notificações bem sucedidas.|Nenhum|
|outgoing.gcm.invalidcredentis|Erros de autorização GCM (Credenciais Inválidas)|Contagem|Total|A contagem de impulsos que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhum|
|outgoing.gcm.badchannel|Erro do Canal Mau gCM|Contagem|Total|A contagem de impulsos que falhou porque o registoId no registo não foi reconhecido (resultado GCM: Registo Inválido).|Nenhum|
|outgoing.gcm.expiredchannel|Erro do canal expirado GCM|Contagem|Total|A contagem de impulsos que falharam porque o registoId no registo expirou (resultado GCM: Não Registado).|Nenhum|
|outgoing.gcm.throttled|Notificações throttled GCM|Contagem|Total|A contagem de impulsos que falharam porque a GCM estrangulou esta aplicação (código de estado GCM: 501-599 ou resultado:Indisponível).|Nenhum|
|outgoing.gcm.invalidnotificationformat|Formato de notificação inválida GCM|Contagem|Total|A contagem de impulsos que falharam porque a carga não foi formatada corretamente (resultado GCM: InvalidDataKey ou InvalidTtl).|Nenhum|
|outgoing.gcm.invalidnotificationsize|Erro de tamanho de notificação inválido GCM|Contagem|Total|A contagem de impulsos que falharam porque a carga era demasiado grande (resultado GCM: MessageTooBig).|Nenhum|
|outgoing.gcm.canalil errado|Erro do canal errado gCM|Contagem|Total|A contagem de impulsos que falharam porque o registroId no registo não está associado à aplicação atual (resultado GCM: InvalidPackageName).|Nenhum|
|outgoing.gcm.pnserror|Erros do GCM|Contagem|Total|A contagem de impulsos que falhou devido a erros de comunicação com gCM.|Nenhum|
|outgoing.gcm.authenticationeror|Erros de autenticação GCM|Contagem|Total|A contagem de impulsos que falharam porque o PNS não aceitou as credenciais fornecidas as credenciais estão bloqueadas ou o SenderId não está corretamente configurado na app (resultado GCM: MismatchedSenderId).|Nenhum|
|outgoing.mpns.sucesso|Notificações bem sucedidas da MPNS|Contagem|Total|A contagem de todas as notificações bem sucedidas.|Nenhum|
|outgoing.mpns.invalidcredentis|Credenciais inválidas da MPNS|Contagem|Total|A contagem de impulsos que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhum|
|outgoing.mpns.badchannel|Erro do Canal Mau da MPNS|Contagem|Total|A contagem de impulsos que falhou porque o ChannelURI no registo não foi reconhecido (estatuto MPNS: 404 não encontrado).|Nenhum|
|outgoing.mpns.throttled|Notificações throttled MPNS|Contagem|Total|A contagem de impulsos que falhou porque a MPNS está a estrangular esta aplicação (WNS MPNS: 406 Não Aceitável).|Nenhum|
|outgoing.mpns.invalidnotificationformat|Formato de Notificação Inválida mpns|Contagem|Total|A contagem de impulsos que falharam porque a carga útil da notificação era demasiado grande.|Nenhum|
|outgoing.mpns.channeldisconnected|Canal MPNS desligado|Contagem|Total|A contagem de impulsos que falhou porque o ChannelURI no registo foi desligado (estado MPNS: 412 não encontrado).|Nenhum|
|outgoing.mpns.drop|Notificações retiradas do MPNS|Contagem|Total|A contagem de impulsos que foram deixados por MPNS (cabeçalho de resposta MPNS: X-NotificationStatus: QueueFull ou Suprimido).|Nenhum|
|outgoing.mpns.pnserror|Erros do MPNS|Contagem|Total|A contagem de impulsos que falhou devido a erros de comunicação com MPNS.|Nenhum|
|outgoing.mpns.authenticationerror|Erros de Autenticação MPNS|Contagem|Total|A contagem de impulsos que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhum|
|notificationhub.pushes|Todas as notificações de saída|Contagem|Total|Todas as notificações de saída do centro de notificação|Nenhum|
|incoming.all.requests|Todos os Pedidos de Entrada|Contagem|Total|Total de pedidos de entrada para um centro de notificação|Nenhum|
|incoming.all.failedrequests|Todos os pedidos falhados de entrada|Contagem|Total|Total de pedidos falhados de entrada falhada para um centro de notificação|Nenhum|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Inodes Average_% Grátis|% Inodes grátis|Contagem|Média|Inodes Average_% Grátis|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_% Espaço Livre|% Espaço Livre|Contagem|Média|Average_% Espaço Livre|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|inodes Average_% usados|% Inodes usados|Contagem|Média|inodes Average_% usados|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_% espaço usado|% Espaço Usado|Contagem|Média|Average_% espaço usado|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Disk Ler Bytes/seg|Discos ler Bytes/seg|Contagem|Média|Average_Disk Ler Bytes/seg|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Disk Leituras/seg|Leituras/seg de disco|Contagem|Média|Average_Disk Leituras/seg|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Transferências Average_Disk/seg|Transferências de discos/seg|Contagem|Média|Transferências Average_Disk/seg|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Disk Escrever Bytes/seg|Discos De Escrita Bytes/seg|Contagem|Média|Average_Disk Escrever Bytes/seg|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Disk Escreve/Seg|Escritas/seg de disco|Contagem|Média|Average_Disk Escreve/Seg|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Free Megabytes|Megabytes grátis|Contagem|Média|Average_Free Megabytes|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Logical Discos Bytes/seg|Bytes/seg de disco lógico|Contagem|Média|Average_Logical Discos Bytes/seg|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|memória Average_% disponível|% Memória Disponível|Contagem|Média|memória Average_% disponível|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|espaço de troca disponível Average_%|% Espaço de troca disponível|Contagem|Média|espaço de troca disponível Average_%|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_% Memória Usada|% Memória Usada|Contagem|Média|Average_% Memória Usada|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_% espaço de troca usado|% Espaço de troca usado|Contagem|Média|Average_% espaço de troca usado|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|memória Average_Available MBytes|Memória MBytes disponível|Contagem|Média|memória Average_Available MBytes|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|troca de MBytes Average_Available|Troca de MBytes disponível|Contagem|Média|troca de MBytes Average_Available|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Page Leituras/seg|Leituras de página/seg|Contagem|Média|Average_Page Leituras/seg|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Page Escreve/Seg|Escritas/seg de página|Contagem|Média|Average_Page Escreve/Seg|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Pages/seg|Páginas/seg|Contagem|Média|Average_Pages/seg|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Used MBytes Swap Space|Espaço de troca de MBytes usado|Contagem|Média|Average_Used MBytes Swap Space|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|MBytes de Memória Average_Used|MBytes de memória usados|Contagem|Média|MBytes de Memória Average_Used|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|bytes de Average_Total transmitidos|Total de bytes transmitidos|Contagem|Média|bytes de Average_Total transmitidos|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Total Bytes Recebidos|Total de bytes recebidos|Contagem|Média|Average_Total Bytes Recebidos|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Total Bytes|Total Bytes|Contagem|Média|Average_Total Bytes|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|pacotes de Average_Total transmitidos|Total de pacotes transmitidos|Contagem|Média|pacotes de Average_Total transmitidos|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|pacotes de Average_Total recebidos|Total de pacotes recebidos|Contagem|Média|pacotes de Average_Total recebidos|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Total Erros Rx|Erros Rx totais|Contagem|Média|Average_Total Erros Rx|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Total Tx Erros|Erros Tx totais|Contagem|Média|Average_Total Tx Erros|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Colisões Average_Total|Colisões totais|Contagem|Média|Colisões Average_Total|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Avg. de Disco seg/Leitura|Avg. Disco seg/Ler|Contagem|Média|Average_Avg. de Disco seg/Leitura|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Avg. Disco seg/Transferência|Avg. Disco seg/Transferência|Contagem|Média|Average_Avg. Disco seg/Transferência|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Avg. de Disco seg/Escrita|Avg. Disco seg/Write|Contagem|Média|Average_Avg. de Disco seg/Escrita|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Physical Discos Bytes/seg|Bytes/seg de disco físico|Contagem|Média|Average_Physical Discos Bytes/seg|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Pct Tempo Privilegiado|Pct Tempo Privilegiado|Contagem|Média|Average_Pct Tempo Privilegiado|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Pct tempo de utilizador|Tempo de utilizador do Pct|Contagem|Média|Average_Pct tempo de utilizador|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|KBytes de Memória Average_Used|KBytes de Memória Usados|Contagem|Média|KBytes de Memória Average_Used|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Virtual Memória Partilhada|Memória partilhada virtual|Contagem|Média|Average_Virtual Memória Partilhada|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_% tempo do DPC|% tempo DPC|Contagem|Média|Average_% tempo do DPC|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_% tempo inativo|% de Tempo Inativo|Contagem|Média|Average_% tempo inativo|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_% Tempo de interrupção|% Tempo de interrupção|Contagem|Média|Average_% Tempo de interrupção|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_% IO Tempo de espera|% IO Tempo de Espera|Contagem|Média|Average_% IO Tempo de espera|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_% tempo agradável|% Tempo agradável|Contagem|Média|Average_% tempo agradável|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_% Tempo Privilegiado|% Tempo Privilegiado|Contagem|Média|Average_% Tempo Privilegiado|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|tempo de processador Average_%|% de Tempo do Processador|Contagem|Média|tempo de processador Average_%|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_% tempo de utilização|% Tempo de Utilização|Contagem|Média|Average_% tempo de utilização|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|memória física Average_Free|Memória Física Gratuita|Contagem|Média|memória física Average_Free|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|espaço Average_Free em arquivos de paging|Espaço gratuito em ficheiros de paging|Contagem|Média|espaço Average_Free em arquivos de paging|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|memória virtual Average_Free|Memória Virtual Gratuita|Contagem|Média|memória virtual Average_Free|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Processes|Processos|Contagem|Média|Average_Processes|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Size armazenado em ficheiros de paging|Tamanho armazenado em ficheiros de paging|Contagem|Média|Average_Size armazenado em ficheiros de paging|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Uptime|Uptime|Contagem|Média|Average_Uptime|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Users|Utilizadores|Contagem|Média|Average_Users|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|comprimento da fila do disco Average_Current|Comprimento atual da fila do disco|Contagem|Média|comprimento da fila do disco Average_Current|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Available MBytes|MBytes disponíveis|Contagem|Média|Average_Available MBytes|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_% bytes comprometidos em uso|% Bytes comprometidos em uso|Contagem|Média|Average_% bytes comprometidos em uso|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Bytes Recebido/seg|Bytes Recebidos/seg|Contagem|Média|Average_Bytes Recebido/seg|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Bytes Enviado/seg|Bytes Enviados/seg|Contagem|Média|Average_Bytes Enviado/seg|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Average_Bytes Total/seg|Bytes Total/seg|Contagem|Média|Average_Bytes Total/seg|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|comprimento da fila Average_Processor|Comprimento da fila do processador|Contagem|Média|comprimento da fila Average_Processor|Computador,Nome de Objeto,Nome de Instância,CounterPath,SourceSystem|
|Heartbeat|Heartbeat|Contagem|Total|Heartbeat|Computador,OSType,Versão,SourceComputerId|
|Atualizar|Atualizar|Contagem|Média|Atualizar|Computador,Produto,Classificação,UpdateState,Opcional,Aprovado|
|Evento|Evento|Contagem|Média|Evento|Fonte,EventLog,Computer,EventCategoria,EventLevel,EventLevelName,EventLEVELName,EventID|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft.Peering/peeringServices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Prefixlatency|Latência prefixo|Milissegundos|Média|Latência prefixo mediano|PrefixName|

## <a name="microsoftpeeringpeerings"></a>Microsoft.Peering/peerings

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|SessãoSAlabilidadeV4|Disponibilidade de sessão V4|Percentagem|Média|Disponibilidade da sessão V4|ConnectionId|
|SessãoSSAlabilidadeV6|Disponibilidade de Sessão V6|Percentagem|Média|Disponibilidade da sessão V6|ConnectionId|
|IngressTrafficRate|Taxa de tráfego de entrada|BitsPerSecond|Média|Taxa de tráfego ingress em bits por segundo|ConnectionId|
|EgressTrafficRate|Taxa de tráfego de Egress|BitsPerSecond|Média|Taxa de tráfego de egress em bits por segundo|ConnectionId|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Queriaduração|Duração da Consulta|Milissegundos|Média|Duração da consulta DAX no último intervalo|Sem Dimensões|
|QueryPoolJobQueueLength|Threads: Comprimento da fila do trabalho da piscina de consulta|Contagem|Média|Número de empregos na fila da piscina de fios de consulta.|Sem Dimensões|
|qpu_high_utilization_metric|Maior Utilização de QPU|Contagem|Total|QPU Alta Utilização em Último Minuto, 1 para alta utilização QPU, caso contrário 0|Sem Dimensões|
|memory_metric|Memória|Bytes|Média|Memória. Intervalo 0-3 GB para A1, 0-5 GB para A2, 0-10 GB para A3, 0-25 GB para A4, 0-50 GB para A5 e 0-100 GB para A6|Sem Dimensões|
|memory_thrashing_metric|Degradação de Memória|Percentagem|Média|Memória média a bater.|Sem Dimensões|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft.ProjectBabylon/contas

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|AssetDistributionByClassification|Distribuição de ativos por classificação|Contagem|Total|Indica o número de ativos com uma determinada classificação atribuída, ou seja, são classificados com essa etiqueta.|Classificação,Fonte,Recursos|
|AssetDistributionByStorageType|Distribuição de ativos por tipo de armazenamento|Contagem|Total|Indica o número de ativos com um determinado tipo de armazenamento.|StorageType,ResourceId|
|CatalogActiveUsers|Utilizadores Ativos Diários|Contagem|Total|Número de utilizadores ativos diariamente|ResourceId|
|CatalogUsage|Distribuição de utilização por Operação|Contagem|Total|Indicar o número de operação que o utilizador faz ao catálogo, ou seja, Acesso, Pesquisa, Glossário.|Operação,ResourceId|
|NúmeroOfAssetsWithClassificações|Número de ativos com pelo menos uma classificação|Contagem|Média|Indica o número de ativos com pelo menos uma classificação de etiqueta.|ResourceId|
|ScanCancelled|Scan cancelado|Contagem|Total|Indica o número de exames cancelados.|ResourceId|
|ScanCompleted|Digitalização Concluída|Contagem|Total|Indica o número de exames concluídos com sucesso.|ResourceId|
|ScanFailed|Scan falhou|Contagem|Total|Indica que o número de exames falhou.|ResourceId|
|ScanTimeTaken|Tempo de digitalização tomado|Segundos|Total|Indica o tempo total de digitalização em segundos.|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|OuvinteConnections-Sucesso|OuvinteConnections-Sucesso|Contagem|Total|Falhas de escuta bem sucedidas para Microsoft.Relay.|Nome da Entidade,OperaçãoResult|
|OuvinteConnections-ClientError|OuvinteConnections-ClientError|Contagem|Total|ClientError em ListenerConnections para Microsoft.Relay.|Nome da Entidade,OperaçãoResult|
|OuvinteConnections-ServerError|OuvinteConnections-ServerError|Contagem|Total|ServerError em ListenerConnections para Microsoft.Relay.|Nome da Entidade,OperaçãoResult|
|SenderConnections-Sucesso|SenderConnections-Sucesso|Contagem|Total|SenderConnections de sucesso para Microsoft.Relay.|Nome da Entidade,OperaçãoResult|
|SenderConnections-ClientError|SenderConnections-ClientError|Contagem|Total|ClientError em SenderConnections para Microsoft.Relay.|Nome da Entidade,OperaçãoResult|
|SenderConnections-ServerError|SenderConnections-ServerError|Contagem|Total|ServerError em SenderConnections para Microsoft.Relay.|Nome da Entidade,OperaçãoResult|
|OuvinteConnections-TotalRequests|OuvinteConnections-TotalRequests|Contagem|Total|Total de EscutasConnections para Microsoft.Relay.|Nome de Entidade|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Contagem|Total|Total de pedidos de SenderConnections para Microsoft.Relay.|Nome de Entidade|
|ActiveConnections|ActiveConnections|Contagem|Total|Total de ActiveConnections para Microsoft.Relay.|Nome de Entidade|
|ActiveListeners|ActiveListeners|Contagem|Total|Total de ActiveListeners para Microsoft.Relay.|Nome de Entidade|
|BytesTransferido|BytesTransferido|Contagem|Total|Total bytesTransferred para Microsoft.Relay.|Nome de Entidade|
|OuvinteSEsa ligações|OuvinteSEsa ligações|Contagem|Total|Total ListenerDis ligações para Microsoft.Relay.|Nome de Entidade|
|SenderDis ligações|SenderDis ligações|Contagem|Total|Total de SenderDis ligações para Microsoft.Relay.|Nome de Entidade|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|PesquisaLatency|Procurar Latência|Segundos|Média|Latência média de pesquisa para o serviço de pesquisa|Nenhum|
|SearchQueriesPerSecond|Consultas de pesquisa por segundo|CondePerSecond|Média|Consultas de pesquisa por segundo para o serviço de pesquisa|Nenhum|
|ThrottledSearchQueriesPercentage|Percentagem de consultas de pesquisa aceleradas|Percentagem|Média|Percentagem de consultas de pesquisa que foram estrangulados para o serviço de pesquisa|Nenhum|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Requess de sucesso|Pedidos de Sucesso|Contagem|Total|Total de pedidos bem sucedidos para um espaço de nome|Nome da Entidade,OperaçãoResult|
|ServerErrors|Erros do servidor.|Contagem|Total|Erros de servidor para Microsoft.ServiceBus.|Nome da Entidade,OperaçãoResult|
|UserErrors|Erros do utilizador.|Contagem|Total|Erros do utilizador para Microsoft.ServiceBus.|Nome da Entidade,OperaçãoResult|
|ThrottledRequests|Pedidos de aceleração.|Contagem|Total|Pedidos de aceleração para Microsoft.ServiceBus.|Nome da Entidade,OperaçãoResult|
|IncomingRequests|Pedidos de Entrada|Contagem|Total|Pedidos de entrada para Microsoft.ServiceBus.|Nome de Entidade|
|Entradas|Mensagens recebidas|Contagem|Total|Mensagens de entrada para Microsoft.ServiceBus.|Nome de Entidade|
|Despesas de Saída|Mensagens de saída|Contagem|Total|Mensagens de saída para Microsoft.ServiceBus.|Nome de Entidade|
|ActiveConnections|ActiveConnections|Contagem|Total|Total de conexões ativas para Microsoft.ServiceBus.|Nenhum|
|LigaçõesAsa|Ligações Abertas.|Contagem|Média|Ligações abertas para Microsoft.ServiceBus.|Nome de Entidade|
|LigaçõesClosed|Ligações fechadas.|Contagem|Média|Ligações fechadas para Microsoft.ServiceBus.|Nome de Entidade|
|Tamanho|Tamanho|Bytes|Média|Tamanho de uma fila/tópico em Bytes.|Nome de Entidade|
|Mensagens|Contagem de mensagens numa fila/tópico.|Contagem|Média|Contagem de mensagens numa fila/tópico.|Nome de Entidade|
|ActiveMessages|Contagem de mensagens ativas numa fila/tópico.|Contagem|Média|Contagem de mensagens ativas numa fila/tópico.|Nome de Entidade|
|DeadletteredMesages|Contagem de mensagens com letras mortas numa fila/tópico.|Contagem|Média|Contagem de mensagens com letras mortas numa fila/tópico.|Nome de Entidade|
|Horários|Contagem de mensagens programadas numa fila/tópico.|Contagem|Média|Contagem de mensagens programadas numa fila/tópico.|Nome de Entidade|
|NamespaceCpuUsage|CPU|Percentagem|Máximo|Métrica de utilização do espaço de identificação do espaço de serviço do serviço de serviço.|Réplica|
|NomespaceMemoryUsage|Utilização de Memória|Percentagem|Máximo|Métrica de utilização de memória de espaço de nome premium de serviço de serviço de serviço.|Réplica|
|CPUXNS|CPU (precedido)|Percentagem|Máximo|Métrica de utilização do espaço de identificação do espaço de serviço do serviço de serviço. Esta métrica está despricada. Utilize a métrica cpu (NamespaceCpuUsage) em vez disso.|Réplica|
|WSXNS|Utilização da memória (Preprecada)|Percentagem|Máximo|Métrica de utilização de memória de espaço de nome premium de serviço de serviço de serviço. Esta métrica está prevadida. Utilize a métrica de Utilização da Memória (NamespaceMemoryUsage).|Réplica|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/aplicações

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Cpu atribuído|Cpu atribuído|Contagem|Média|CPU alocado a este contentor em núcleos milli|Nome de aplicação,Nome de serviço,CodePackageName,ServiceReplicaName|
|Roteiro Atribuído|Roteiro Atribuído|Bytes|Média|Memória alocada a este recipiente em MB|Nome de aplicação,Nome de serviço,CodePackageName,ServiceReplicaName|
|ActualCpu|ActualCpu|Contagem|Média|Uso real do CPU em núcleos milli|Nome de aplicação,Nome de serviço,CodePackageName,ServiceReplicaName|
|ActualMemory|ActualMemory|Bytes|Média|Utilização real da memória em MB|Nome de aplicação,Nome de serviço,CodePackageName,ServiceReplicaName|
|CpuUtilização|CpuUtilização|Percentagem|Média|Utilização da CPU para este contentor em percentagem doCpu atribuído|Nome de aplicação,Nome de serviço,CodePackageName,ServiceReplicaName|
|Utilização da memória|Utilização da memória|Percentagem|Média|Utilização da CPU para este contentor em percentagem doCpu atribuído|Nome de aplicação,Nome de serviço,CodePackageName,ServiceReplicaName|
|AplicaçãoStatus|AplicaçãoStatus|Contagem|Média|Estado da aplicação de malha de tecido de serviço|Nome de aplicação,Estado|
|ServiceStatus|ServiceStatus|Contagem|Média|Estado de Saúde de um serviço na aplicação de malha de tecido de serviço|Nome de aplicação,Estado,Nome de Serviço|
|ServiceReplicaStatus|ServiceReplicaStatus|Contagem|Média|Estado de Saúde de uma réplica de serviço na aplicação de malha de tecido de serviço|AplicaçãoName,Status,ServiceName,ServiceReplicaName|
|Estatística do Contentor|Estatística do Contentor|Contagem|Média|Estado do contentor na aplicação de malha de tecido de serviço|AplicativoName,ServiceName,CodePackageName,ServiceReplicaName,Status|
|RestartCount|RestartCount|Contagem|Média|Reiniciar a contagem de um recipiente na aplicação de malha de tecido de serviço|AplicativoName,Status,ServiceName,ServiceReplicaName,CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalrService/Signalr

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Contagem de Ligações|Contagem de Ligação|Contagem|Máximo|A quantidade de ligação do utilizador.|Ponto Final|
|Contagem de mensagens|Contagem de mensagens|Contagem|Total|A quantidade total de mensagens.|Nenhum|
|InboundTraffic|Tráfego de Entrada|Bytes|Total|O tráfego de entrada de serviço|Nenhum|
|OutboundTraffic|Tráfego de Saída|Bytes|Total|O tráfego de serviço de saída|Nenhum|
|UserErrors|Erros do Utilizador|Percentagem|Máximo|A percentagem de erros do utilizador|Nenhum|
|SystemErrors|Erros do Sistema|Percentagem|Máximo|A percentagem de erros do sistema|Nenhum|



## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servidores/bases de dados

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Nenhum|
|physical_data_read_percent|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|Nenhum|
|log_write_percent|Percentagem de IO de registo|Percentagem|Média|Registar percentagem de IO. Não aplicável aos armazéns de dados.|Nenhum|
|dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|Percentagem de DTU. Aplica-se a bases de dados baseadas em DTU.|Nenhum|
|storage|Espaço de dados utilizado|Bytes|Máximo|Espaço de dados usado. Não aplicável aos armazéns de dados.|Nenhum|
|connection_successful|Conexões bem sucedidas|Contagem|Total|Conexões bem sucedidas|Nenhum|
|connection_failed|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Nenhum|
|blocked_by_firewall|Bloqueado por Firewall|Contagem|Total|Bloqueado por Firewall|Nenhum|
|impasse|Impasses|Contagem|Total|Impasses. Não aplicável aos armazéns de dados.|Nenhum|
|storage_percent|Espaço de dados usado por cento|Percentagem|Máximo|Espaço de dados usado por cento. Não aplicável a armazéns de dados ou bases de dados de hiperescala.|Nenhum|
|xtp_storage_percent|Por cento de armazenamento em memória OLTP|Percentagem|Média|Por cento de armazenamento em memória OLTP. Não aplicável aos armazéns de dados.|Nenhum|
|workers_percent|Percentagem de trabalhadores|Percentagem|Média|Percentagem de trabalhadores. Não aplicável aos armazéns de dados.|Nenhum|
|sessions_percent|Percentagem de sessões|Percentagem|Média|Percentagem de sessões. Não aplicável aos armazéns de dados.|Nenhum|
|dtu_limit|Limite DTU|Contagem|Média|Limite dTU. Aplica-se a bases de dados baseadas em DTU.|Nenhum|
|dtu_used|DTU usado|Contagem|Média|DTU usado. Aplica-se a bases de dados baseadas em DTU.|Nenhum|
|cpu_limit|Limite do CPU|Contagem|Média|Limite da CPU. Aplica-se a bases de dados baseadas em vCore.|Nenhum|
|cpu_used|CPU usado|Contagem|Média|CPU usado. Aplica-se a bases de dados baseadas em vCore.|Nenhum|
|dwu_limit|Limite dwu|Contagem|Máximo|Limite da DWU. Aplica-se apenas a armazéns de dados.|Nenhum|
|dwu_consumption_percent|Percentagem de DWU|Percentagem|Máximo|Percentagem de DWU. Aplica-se apenas a armazéns de dados.|Nenhum|
|dwu_used|DWU usado|Contagem|Máximo|DWU usado. Aplica-se apenas a armazéns de dados.|Nenhum|
|cache_hit_percent|Percentagem de impacto de cache|Percentagem|Máximo|Cache atinge percentagem. Aplica-se apenas a armazéns de dados.|Nenhum|
|cache_used_percent|Percentagem utilizada em cache|Percentagem|Máximo|Percentagem usada em cache. Aplica-se apenas a armazéns de dados.|Nenhum|
|sqlserver_process_core_percent<sup>1</sup> |Núcleo de núcleo de processo do SQL Server|Percentagem|Máximo|Percentagem de utilização do CPU para o processo SQL Server, medido pelo sistema operativo.|Nenhum|
|sqlserver_process_memory_percent<sup>1</sup> |SQL Servidor processa memória por cento|Percentagem|Máximo|Percentagem de utilização da memória para o processo SQL Server, medido pelo sistema operativo.|Nenhum|
|tempdb_data_size<sup>2</sup> |Kilobytes do tamanho do ficheiro de dados temporários|Contagem|Máximo|Kilobytes tamanho de ficheiro de dados temporários.|Nenhum|
|tempdb_log_size<sup>2</sup> |Kilobytes do tamanho do ficheiro de registo temporário|Contagem|Máximo|Kilobytes tamanho do ficheiro de registo temporário.|Nenhum|
|tempdb_log_used_percent<sup>2</sup> |Registo de percentagem de temperatura usado|Percentagem|Máximo|Tempdb% % registo usado.|Nenhum|
|local_tempdb_usage_percent|Percentagem de temporários locais|Percentagem|Média|Percentagem de temperatura local. Aplica-se apenas a armazéns de dados.|Nenhum|
|app_cpu_billed|App CPU faturada|Contagem|Total|App CPU faturado. Aplica-se a bases de dados sem servidor.|Nenhum|
|app_cpu_percent|Percentagem de CPU de aplicações|Percentagem|Média|Percentagem de CPU de aplicação. Aplica-se a bases de dados sem servidor.|Nenhum|
|app_memory_percent|Percentagem de memória de aplicativo|Percentagem|Média|Percentagem de memória de aplicativo. Aplica-se a bases de dados sem servidor.|Nenhum|
|allocated_data_storage|Espaço de dados atribuído|Bytes|Média|Armazenamento de dados alocado. Não aplicável aos armazéns de dados.|Nenhum|
|memory_usage_percent|Percentagem de memória|Percentagem|Máximo|Percentagem de memória. Aplica-se apenas a armazéns de dados.|Nenhum|
|dw_backup_size_gb|Tamanho do armazenamento de dados|Contagem|Total|O tamanho do armazenamento de dados é composto pelo tamanho dos seus dados e pelo registo de transações. A métrica é contada para a parte 'Armazenamento' da sua conta. Aplica-se apenas a armazéns de dados.|Nenhum|
|dw_snapshot_size_gb|Tamanho do armazenamento instantâneo|Contagem|Total|O tamanho do armazenamento instantâneo é o tamanho das alterações incrementais capturadas por instantâneos para criar pontos de restauro definidos pelo utilizador e automáticos. A métrica é contada para a parte 'Armazenamento' da sua conta. Aplica-se apenas a armazéns de dados.|Nenhum|
|dw_geosnapshot_size_gb|Tamanho do armazenamento de recuperação de desastres|Contagem|Total|O tamanho do armazenamento de recuperação de desastres é refletido como "Armazenamento de Recuperação de Desastres" na sua conta. Aplica-se apenas a armazéns de dados.|Nenhum|
|wlg_allocation_relative_to_system_percent|Alocação de grupo de carga de trabalho por parte do sistema|Percentagem|Máximo|Percentagem de recursos atribuídos em relação a todo o sistema por grupo de carga de trabalho. Aplica-se apenas a armazéns de dados.|WorkloadGroupName,IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Alocação de grupo de carga de trabalho por percentagem de recursos da TAMPA|Percentagem|Máximo|Percentagem de recursos atribuídos em relação aos recursos de bonificação especificados por grupo de carga de trabalho. Aplica-se apenas a armazéns de dados.|WorkloadGroupName,IsUserDefined|
|wlg_active_queries|Consultas ativas do grupo de carga de trabalho|Contagem|Total|Consultas ativas dentro do grupo de carga de trabalho. Aplica-se apenas a armazéns de dados.|WorkloadGroupName,IsUserDefined|
|wlg_queued_queries|Consultas em fila de grupos de carga|Contagem|Total|Consultas em fila dentro do grupo de carga de trabalho. Aplica-se apenas a armazéns de dados.|WorkloadGroupName,IsUserDefined|
|active_queries|Consultas ativas|Contagem|Total|Consultas ativas em todos os grupos de carga de trabalho. Aplica-se apenas a armazéns de dados.|Nenhum|
|queued_queries|Consultas em fila|Contagem|Total|Consultas em fila em todos os grupos de carga de trabalho. Aplica-se apenas a armazéns de dados.|Nenhum|
|wlg_active_queries_timeouts|Tempo limites de consulta do grupo de carga de trabalho|Contagem|Total|Consultas que têm cronometrado para o grupo de carga de trabalho. Aplica-se apenas a armazéns de dados.|WorkloadGroupName,IsUserDefined|
|wlg_effective_min_resource_percent|Por cento de recursos min eficazes|Percentagem|Máximo|Percentagem mínima de recursos reservados e isolados para o grupo de trabalho, tendo em conta o nível mínimo de serviço. Aplica-se apenas a armazéns de dados.|WorkloadGroupName,IsUserDefined|
|wlg_effective_cap_resource_percent|Por cento efetivo do recurso cap|Percentagem|Máximo|Um limite rígido para a percentagem de recursos permitidos para o grupo de carga de trabalho, tendo em conta a percentagem efetiva de recursos min atribuídos a outros grupos de carga de trabalho. Aplica-se apenas a armazéns de dados.|WorkloadGroupName,IsUserDefined|
|full_backup_size_bytes|Tamanho de armazenamento de backup completo|Bytes|Máximo|Tamanho acumulado de armazenamento de backup completo. Aplica-se a bases de dados baseadas em vCore. Não aplicável às bases de dados de hiperescala.|Nenhum|
|diff_backup_size_bytes|Tamanho de armazenamento de backup diferencial|Bytes|Máximo|Tamanho de armazenamento de backup diferencial cumulativo. Aplica-se a bases de dados baseadas em vCore. Não aplicável às bases de dados de hiperescala.|Nenhum|
|log_backup_size_bytes|Registar tamanho de armazenamento de backup|Bytes|Máximo|Tamanho de armazenamento de backup de registo cumulativo. Aplica-se às bases de dados baseadas em vCore e hyperscale.|Nenhum|
|snapshot_backup_size_bytes|Tamanho de armazenamento de backup snapshot|Bytes|Máximo|Tamanho de armazenamento de backup de instantâneo cumulativo. Aplica-se a bases de dados de hiperescala.|Nenhum|
|base_blob_size_bytes|Tamanho de armazenamento de bolha de base|Bytes|Máximo|Tamanho de armazenamento de bolha de base. Aplica-se a bases de dados de hiperescala.|Nenhum|

<sup>1</sup> Esta métrica está disponível para bases de dados utilizando o modelo de compra vCore com 2 vCores e superior, ou 200 DTU e superior para modelos de compra baseados em DTU. 

<sup>2</sup> Esta métrica está disponível para bases de dados utilizando o modelo de compra vCore com 2 vCores e superior, ou 200 DTU e superior para modelos de compra baseados em DTU. Esta métrica não está atualmente disponível para bases de dados de hiperescala ou armazéns de dados.

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servidores/elásticos

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Nenhum|
|database_cpu_percent|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Base de dadosResourceId|
|physical_data_read_percent|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|Nenhum|
|database_physical_data_read_percent|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|Base de dadosResourceId|
|log_write_percent|Percentagem de IO de registo|Percentagem|Média|Percentagem de IO de registo|Nenhum|
|database_log_write_percent|Percentagem de IO de registo|Percentagem|Média|Percentagem de IO de registo|Base de dadosResourceId|
|dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|Percentagem de DTU. Aplica-se a piscinas elásticas à base de DTU.|Nenhum|
|database_dtu_consumption_percent|Percentagem de DTU|Percentagem|Média|Percentagem de DTU|Base de dadosResourceId|
|storage_percent|Espaço de dados usado por cento|Percentagem|Média|Espaço de dados usado por cento|Nenhum|
|workers_percent|Percentagem de trabalhadores|Percentagem|Média|Percentagem de trabalhadores|Nenhum|
|database_workers_percent|Percentagem de trabalhadores|Percentagem|Média|Percentagem de trabalhadores|Base de dadosResourceId|
|sessions_percent|Percentagem de sessões|Percentagem|Média|Percentagem de sessões|Nenhum|
|database_sessions_percent|Percentagem de sessões|Percentagem|Média|Percentagem de sessões|Base de dadosResourceId|
|eDTU_limit|limite eDTU|Contagem|Média|limite eDTU. Aplica-se a piscinas elásticas à base de DTU.|Nenhum|
|storage_limit|Tamanho máximo de dados|Bytes|Média|Tamanho máximo de dados|Nenhum|
|eDTU_used|eDTU usado|Contagem|Média|eDTU usado. Aplica-se a piscinas elásticas à base de DTU.|Nenhum|
|database_eDTU_used|eDTU usado|Contagem|Média|eDTU usado|Base de dadosResourceId|
|storage_used|Espaço de dados utilizado|Bytes|Média|Espaço de dados utilizado|Nenhum|
|database_storage_used|Espaço de dados utilizado|Bytes|Média|Espaço de dados utilizado|Base de dadosResourceId|
|xtp_storage_percent|Por cento de armazenamento em memória OLTP|Percentagem|Média|Por cento de armazenamento em memória OLTP|Nenhum|
|cpu_limit|Limite do CPU|Contagem|Média|Limite da CPU. Aplica-se a piscinas elásticas à base de vCore.|Nenhum|
|database_cpu_limit|Limite do CPU|Contagem|Média|Limite do CPU|Base de dadosResourceId|
|cpu_used|CPU usado|Contagem|Média|CPU usado. Aplica-se a piscinas elásticas à base de vCore.|Nenhum|
|database_cpu_used|CPU usado|Contagem|Média|CPU usado|Base de dadosResourceId|
|sqlserver_process_core_percent<sup>1</sup>|Núcleo de núcleo de processo do SQL Server|Percentagem|Máximo|Percentagem de utilização do CPU para o processo SQL Server, medido pelo sistema operativo. Aplica-se a piscinas elásticas. |Nenhum|
|sqlserver_process_memory_percent<sup>1</sup>|SQL Servidor processa memória por cento|Percentagem|Máximo|Percentagem de utilização da memória para o processo SQL Server, medido pelo sistema operativo. Aplica-se a piscinas elásticas. |Nenhum|
|tempdb_data_size<sup>2</sup>|Kilobytes do tamanho do ficheiro de dados temporários|Contagem|Máximo|Kilobytes tamanho de ficheiro de dados temporários.|Nenhum|
|tempdb_log_size<sup>2</sup>|Kilobytes do tamanho do ficheiro de registo temporário|Contagem|Máximo|Kilobytes tamanho do ficheiro de registo temporário. |Nenhum|
|tempdb_log_used_percent<sup>2</sup>|Registo de percentagem de temperatura usado|Percentagem|Máximo|Tempdb% % registo usado.|Nenhum|
|allocated_data_storage|Espaço de dados atribuído|Bytes|Média|Espaço de dados atribuído|Nenhum|
|database_allocated_data_storage|Espaço de dados atribuído|Bytes|Média|Espaço de dados atribuído|Base de dadosResourceId|
|allocated_data_storage_percent|Espaço de dados alocado por cento|Percentagem|Máximo|Espaço de dados alocado por cento|Nenhum|

<sup>1</sup> Esta métrica está disponível para bases de dados utilizando o modelo de compra vCore com 2 vCores e superior, ou 200 DTU e superior para modelos de compra baseados em DTU. 

<sup>2</sup> Esta métrica está disponível para bases de dados utilizando o modelo de compra vCore com 2 vCores e superior, ou 200 DTU e superior para modelos de compra baseados em DTU. Esta métrica não está atualmente disponível para bases de dados de Hiperescala.

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|virtual_core_count|Contagem de núcleos virtuais|Contagem|Média|Contagem de núcleos virtuais|Nenhum|
|avg_cpu_percent|Percentagem média de CPU|Percentagem|Média|Percentagem média de CPU|Nenhum|
|reserved_storage_mb|Espaço de armazenamento reservado|Contagem|Média|Espaço de armazenamento reservado|Nenhum|
|storage_space_used_mb|Espaço de armazenamento utilizado|Contagem|Média|Espaço de armazenamento utilizado|Nenhum|
|io_requests|Contagem de pedidos de IO|Contagem|Média|Contagem de pedidos de IO|Nenhum|
|io_bytes_read|IO bytes ler|Bytes|Média|IO bytes ler|Nenhum|
|io_bytes_written|IO bytes escritos|Bytes|Média|IO bytes escritos|Nenhum|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAcontas

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Capacidade Usada|Capacidade utilizada|Bytes|Média|Capacidade da conta utilizada|Nenhum|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType,GeoType,ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SucessoServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média usada pela Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType,ApiName,Autenticação|
|SucessoE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência média de ponta a ponta de pedidos bem sucedidos feita a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAcontas/blobServices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Capacidade blob|Capacidade blob|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Blob da conta de armazenamento em bytes.|BlobType,Tier|
|BlobCount|Contagem de blobs|Contagem|Média|O número de Blob no serviço Blob da conta de armazenamento.|BlobType,Tier|
|Contagem de contentores|Contagem de contentores blob|Contagem|Média|O número de contentores no serviço Blob da conta de armazenamento.|Nenhum|
|Capacidade de Indexação|Capacidade de Índice|Bytes|Média|A quantidade de armazenamento utilizada pelo Índice ADLS Gen2 (Hierárquica) em bytes.|Nenhum|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType,GeoType,ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SucessoServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média usada pela Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType,ApiName,Autenticação|
|SucessoE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência média de ponta a ponta de pedidos bem sucedidos feita a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAcontas/tableServices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Capacidade de Mesa|Capacidade de mesa|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço de mesa da conta de armazenamento em bytes.|Nenhum|
|MesaCount|Contagem de tabelas|Contagem|Média|O número de mesa no serviço de mesa da conta de armazenamento.|Nenhum|
|TabelaseconagemCount|Contagem de Entidades de Tabela|Contagem|Média|O número de entidades de mesa no serviço mesa da conta de armazenamento.|Nenhum|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType,GeoType,ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SucessoServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média usada pela Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType,ApiName,Autenticação|
|SucessoE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência média de ponta a ponta de pedidos bem sucedidos feita a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAcontas/fileServices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Capacidade de Ficheiros|Capacidade de Arquivo|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço de ficheiros da conta de armazenamento em bytes.|Partilha de Ficheiros|
|Contagem de ficheiros|Contagem de ficheiros|Contagem|Média|O número de ficheiros no serviço de ficheiros da conta de armazenamento.|Partilha de Ficheiros|
|FileShareCount|Contagem de partilha de ficheiros|Contagem|Média|O número de ações de ficheiros no serviço de ficheiros da conta de armazenamento.|Nenhum|
|FileShareSnapshotCount|Contagem de snapshot de partilha de ficheiros|Contagem|Média|O número de instantâneos presentes na parte do Serviço de Ficheiros da conta de armazenamento.|Partilha de Ficheiros|
|FileShareSnapshotSize|Tamanho do instantâneo de partilha de arquivo|Bytes|Média|A quantidade de armazenamento utilizada pelas fotos no serviço de ficheiros da conta de armazenamento em bytes.|Partilha de Ficheiros|
|FileShareQuota|Tamanho da quota de ações de arquivo|Bytes|Média|O limite superior da quantidade de armazenamento que pode ser usado pelo Azure Files Service em bytes.|Partilha de Ficheiros|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType,GeoType,ApiName,Authentication,FileShare|
|Entrada|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação,FileShare|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação,FileShare|
|SucessoServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média usada pela Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType,ApiName,Autenticação,FileShare|
|SucessoE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência média de ponta a ponta de pedidos bem sucedidos feita a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação,FileShare|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação,FileShare|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAcontas/filas Serviços

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Capacidade de Fila|Capacidade de fila|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço de fila da conta de armazenamento em bytes.|Nenhum|
|Contagem de filas|Contagem de filas|Contagem|Média|O número de filas no serviço de fila da conta de armazenamento.|Nenhum|
|QueueMessageCount|Contagem de mensagens de fila|Contagem|Média|O número aproximado de mensagens de fila no serviço de fila da conta de armazenamento.|Nenhum|
|Transações|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType,GeoType,ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SucessoServerLatency|Latência de Servidor Com Êxito|Milissegundos|Média|A latência média usada pela Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType,ApiName,Autenticação|
|SucessoE2ELatency|Latência de E2E Com Êxito|Milissegundos|Média|A latência média de ponta a ponta de pedidos bem sucedidos feita a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType,ApiName,Autenticação|





## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/caches

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|ClientIOPS|IOPS totais do cliente|Contagem|Média|A taxa de operações de ficheiros de clientes processadas pela Cache.|Nenhum|
|ClientLatency|Latência média do cliente|Milissegundos|Média|Latência média das operações de ficheiro de clientes para a Cache de Armazenamento.|Nenhum|
|ClientReadIOPS|Cliente ler IOPS|CondePerSecond|Média|Cliente lê operações por segundo.|Nenhum|
|ClientReadThroughput|Produção média de leitura de cache|BytesPerSecond|Média|Cliente leia taxa de transferência de dados.|Nenhum|
|ClientWriteIOPS|Cliente escrever iOPS|CondePerSecond|Média|Cliente escreve operações por segundo.|Nenhum|
|ClientWriteThroughput|Produção média de escrita de cache|BytesPerSecond|Média|Cliente escreve taxa de transferência de dados.|Nenhum|
|ClientMetadataReadIOPS|Metadados de Clientes lêem IOPS|CondePerSecond|Média|A taxa de operações de ficheiros de clientes enviadas para a Cache, excluindo as leituras de dados, que não modificam o estado persistente.|Nenhum|
|ClientMetadataWriteIOPS|Metadados de clientes escrevem IOPS|CondePerSecond|Média|A taxa de operações de ficheiros de clientes enviadas para a Cache, excluindo os dados, que modificam o estado persistente.|Nenhum|
|ClientLockIOPS|IOPS de bloqueio de cliente|CondePerSecond|Média|Operações de bloqueio de ficheiros de clientes por segundo.|Nenhum|
|StorageTargetHealth|Saúde alvo de armazenamento|Contagem|Média|Boolean resultados do teste de conectividade entre os Alvos de Cache e Armazenamento.|Nenhum|
|Uptime|Uptime|Contagem|Média|Boolean resultados do teste de conectividade entre a Cache e o sistema de monitorização.|Nenhum|
|StorageTargetIOPS|Total de armazenamentoTarget IOPS|Contagem|Média|A taxa de todas as operações de ficheiro que o Cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|ArmazenamentoTarget Escrever IOPS|Contagem|Média|A taxa das operações de escrita de ficheiros que o Cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetAsyncWriteThroughput|Produção de escrita assíncronea de armazenamento|BytesPerSecond|Média|A taxa que o Cache assíncronea escreve dados para um determinado StorageTarget. São escritos oportunistas que não fazem com que os clientes bloqueiem.|StorageTarget|
|StorageTargetSyncWriteThroughput|Produção de escrita sincronizada StorageTarget|BytesPerSecond|Média|A taxa que o Cache escreve sincronizadamente os dados para um determinado StorageTarget. Estas são escritas que fazem com que os clientes bloqueiem.|StorageTarget|
|StorageTargetTotalWriteThroughput|Produção total de escrita do StorageTarget|BytesPerSecond|Média|A taxa total que o Cache escreve dados para um determinado StorageTarget.|StorageTarget|
|ArmazenamentoTargetLatency|Latência do StorageTarget|Milissegundos|Média|A latência média de ida e volta de todas as operações de arquivo que o Cache envia para um StorageTarget partricular.|StorageTarget|
|ArmazenamentoTargetMetadataReadIOPS|ArmazenamentoTarget Metadados Ler IOPS|CondePerSecond|Média|A taxa de operações de ficheiro que não modificam o estado persistente, e excluindo a operação de leitura, que a Cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetMetadataWriteIOPS|ArmazenamentoTarget Metadados Escrever IOPS|CondePerSecond|Média|A taxa de operações de ficheiro que modificam o estado persistente e excluindo a operação de escrita, que a Cache envia para um determinado StorageTarget.|StorageTarget|
|ArmazenamentoTargetReadIOPS|ArmazenamentoTarget Ler IOPS|CondePerSecond|Média|A taxa de operações de leitura de ficheiros que a Cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetReadAheadThroughput|ArmazenamentoTarget Ler Antes Produção|BytesPerSecond|Média|A taxa que o Cache oportunisticamente lê dados do StorageTarget.|StorageTarget|
|StorageTargetFillThroughput|Produção de preenchimento de armazenamentor|BytesPerSecond|Média|A taxa que o Cache lê os dados do StorageTarget para lidar com uma falha de cache.|StorageTarget|
|StorageTargetTotalReadThroughput|Produção total de leitura do StorageTarget|BytesPerSecond|Média|A taxa total que a Cache lê dados de um determinado StorageTarget.|StorageTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|ServidorSyncSessionResult|Resultado da sessão de sincronização|Contagem|Média|Métrica que regista um valor de 1 cada vez que o Ponto Final do Servidor completa com sucesso uma Sessão de Sincronização com o Ponto final da Cloud|SyncGroupName,ServerEndpointName,SyncDirection|
|ArmazenamentoSyncSyncSessionAppliedFilesCount|Ficheiros Sincronizados|Contagem|Total|Contagem de Ficheiros sincronizado|SyncGroupName,ServerEndpointName,SyncDirection|
|ArmazenamentoSyncSyncSessionPerItemErrorsCount|Ficheiros não sincronizados|Contagem|Total|Contagem de ficheiros não sincronizado|SyncGroupName,ServerEndpointName,SyncDirection|
|ArmazenamentoSybatchTransferredFileBytes|Bytes sincronizados|Bytes|Total|Tamanho total do ficheiro transferido para Sincronização|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncServerHeartbeat|Estado online do servidor|Contagem|Máximo|Métrica que regista um valor de 1 cada vez que o servidor resigtered regista com sucesso um batimento cardíaco com o Cloud Endpoint|ServerName|
|ArmazenamentoSyncRecallIOTotalSizeBytes|Recuperação de camadas de nuvem|Bytes|Total|Tamanho total dos dados recolhidos pelo servidor|ServerName|
|ArmazenamentoSyncRecalledTotalNetworkBytes|Tamanho de recuperação de nível de nuvem|Bytes|Total|Tamanho dos dados recordados|SyncGroupName,ServerName|
|ArmazenamentoSyncRecallThroughputBytesPerSecond|Produção de recolha de camadas de nuvem|BytesPerSecond|Média|Tamanho do rendimento da recolha de dados|SyncGroupName,ServerName|
|ArmazenamentoSyncRecalledNetworkBytesByApplication|Tamanho de recolha de camadas de nuvem por aplicação|Bytes|Total|Tamanho dos dados recolhidos pela aplicação|SyncGroupName,ServerName,ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|Ficheiros Sincronizados|Contagem|Total|Contagem de Ficheiros sincronizado|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Ficheiros não sincronizados|Contagem|Total|Contagem de ficheiros não sincronizado|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupBatchTransferredFileBytes|Bytes sincronizados|Bytes|Total|Tamanho total do ficheiro transferido para Sincronização|SyncGroupName,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|Ficheiros Sincronizados|Contagem|Total|Contagem de Ficheiros sincronizado|Nome de ServerEndpoint,SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Ficheiros não sincronizados|Contagem|Total|Contagem de ficheiros não sincronizado|Nome de ServerEndpoint,SyncDirection|
|ServerEndpointBatchTransferredFileBytes|Bytes sincronizados|Bytes|Total|Tamanho total do ficheiro transferido para Sincronização|Nome de ServerEndpoint,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registradosServers

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|ServerHeartbeat|Estado online do servidor|Contagem|Máximo|Métrica que regista um valor de 1 cada vez que o servidor resigtered regista com sucesso um batimento cardíaco com o Cloud Endpoint|ServerResourceId,ServerName|
|ServerRecallIOTotalSizeBytes|Recuperação de camadas de nuvem|Bytes|Total|Tamanho total dos dados recolhidos pelo servidor|ServerResourceId,ServerName|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Utilização de Recursos|SU % Utilização|Percentagem|Máximo|SU % Utilização|Nome Lógico,PartitionId|
|InputEvents|Eventos de Entrada|Contagem|Total|Eventos de Entrada|Nome Lógico,PartitionId|
|InputEventBytes|Insutos de Eventos|Bytes|Total|Insutos de Eventos|Nome Lógico,PartitionId|
|LateInputEvents|Eventos de entrada tardia|Contagem|Total|Eventos de entrada tardia|Nome Lógico,PartitionId|
|OutputEvents|Eventos de saída|Contagem|Total|Eventos de saída|Nome Lógico,PartitionId|
|ConversoresErrors|Erros de Conversão de Dados|Contagem|Total|Erros de Conversão de Dados|Nome Lógico,PartitionId|
|Erros|Erros de tempo de execução|Contagem|Total|Erros de tempo de execução|Nome Lógico,PartitionId|
|DropOrAdjustedEvents|Eventos fora de ordem|Contagem|Total|Eventos fora de ordem|Nome Lógico,PartitionId|
|AMLCalloutRequests|Pedidos de Função|Contagem|Total|Pedidos de Função|Nome Lógico,PartitionId|
|AMLCalloutFailedRequests|Pedidos de função falhadas|Contagem|Total|Pedidos de função falhadas|Nome Lógico,PartitionId|
|AMLCalloutInputEvents|Eventos de Função|Contagem|Total|Eventos de Função|Nome Lógico,PartitionId|
|DeserializaçãoError|Erros de deserialização de entrada|Contagem|Total|Erros de deserialização de entrada|Nome Lógico,PartitionId|
|Início dos Eventos|Eventos de entrada antecipada|Contagem|Total|Eventos de entrada antecipada|Nome Lógico,PartitionId|
|OutputWatermarkDelaySeconds|Atraso da marca de água|Segundos|Máximo|Atraso da marca de água|Nome Lógico,PartitionId|
|InputEventsSourcesBacklogged|Eventos de entrada recuados|Contagem|Máximo|Eventos de entrada recuados|Nome Lógico,PartitionId|
|InputEventsSourcesPerSecond|Fontes de entrada recebidas|Contagem|Total|Fontes de entrada recebidas|Nome Lógico,PartitionId|

## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|OrquestrationPipelineRunsEnded|As corridas de gasodutos terminaram|Contagem|Total|Contagem de corridas de oleoduto de orquestração que conseguiu, falhou, ou foram cancelados|Resultado,FalhaType,Pipeline|
|OrquestraçãoActivityRunsEnded|A atividade corre terminada|Contagem|Total|Contagem de atividades de orquestração que sucederam, falharam ou foram canceladas|Resultado,FalhaType,Atividade,ActivityType,Pipeline|
|OrquestraçãoStroggersDessemotado|Os gatilhos terminaram|Contagem|Total|Contagem de gatilhos de orquestração que conseguiu, falhou, ou foram cancelados|Resultado,FalhaType,Gatilho|
|SQLOnDemandLoginAttempts|Tentativas de login|Contagem|Total|Contagem de tentativas de login que succedou ou falhou|Resultado|
|SQLOnDemandQueriesEnded|Consultas terminadas|Contagem|Total|Contagem de consultas que sucederam, falharam, ou foram cancelados|Resultado|
|SQLOnDemandQueryProcessedBytes|Dados processados|Bytes|Total|Quantidade de dados processados por consultas|Nenhum|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|SparkJobsEnded|Candidaturas terminadas|Contagem|Total|Contagem de candidaturas terminadas|JobType,JobResult|
|Capacidade de Cores|Capacidade de núcleos|Contagem|Máximo|Capacidade de núcleos|Nenhum|
|Capacidade de MemóriaGB|Capacidade de memória (GB)|Contagem|Máximo|Capacidade de memória (GB)|Nenhum|

## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|DWULimit|Limite dwu|Contagem|Máximo|Objetivo de nível de serviço da piscina SQL|Nenhum|
|DWUUSED|DWU usado|Contagem|Máximo|Representa uma representação de alto nível de uso em toda a piscina SQL. Medido por limite DE DWU * percentagem de DWU|Nenhum|
|DWUUsedPercent|Percentagem utilizada pela DWU|Percentagem|Máximo|Representa uma representação de alto nível de uso em toda a piscina SQL. Medido pela tomada do máximo entre a percentagem de CPU e a percentagem de IO de dados|Nenhum|
|ConexõesBlockedByFirewall|Ligações bloqueadas por firewall|Contagem|Total|Contagem de ligações bloqueadas pelas regras de firewall. Reveja as políticas de controlo de acesso para a sua piscina SQL e monitorize estas ligações se a contagem for elevada|Nenhum|
|AdaptiveCacheHitPercent|Percentagem de impacto de cache adaptativo|Percentagem|Máximo|Mede a forma como as cargas de trabalho estão a utilizar a cache adaptativa. Utilize esta métrica com a métrica percentual de impacto da cache para determinar se deve escalar para capacidade adicional ou refazer cargas de trabalho para hidratar a cache|Nenhum|
|AdaptiveCacheUsedPercent|Percentagem de cache adaptativa utilizada|Percentagem|Máximo|Mede a forma como as cargas de trabalho estão a utilizar a cache adaptativa. Utilize esta métrica com a cache utilizada métrica percentual para determinar se deve escalar para capacidade adicional ou refazer cargas de trabalho para hidratar a cache|Nenhum|
|LocalTempDBUsedPercent|Percentagem de temporários locais utilizada|Percentagem|Máximo|Utilização temporária local em todos os nós de computação - valores são emitidos a cada cinco minutos|Nenhum|
|MemoryUsedPercent|Percentagem de memória usada|Percentagem|Máximo|Utilização da memória em todos os nós na piscina SQL|Nenhum|
|Ligações|Ligações|Contagem|Total|Contagem de entradas totais para a piscina SQL|Resultado|
|WLGActiveQueries|Consultas ativas do grupo de carga de trabalho|Contagem|Total|As consultas ativas dentro do grupo de carga de trabalho. Utilizando esta métrica não filtrada e não filtrada exibe todas as consultas ativas em execução no sistema|IsUserDefined,WorkloadGroup|
|WLGActiveQueriesTimeouts|Tempo limites de consulta do grupo de carga de trabalho|Contagem|Total|Consultas para o grupo de trabalho que tem cronometrado. Os intervalos de consulta reportados por esta métrica são apenas uma vez que a consulta tenha começado a executar (não inclui o tempo de espera devido ao bloqueio ou espera de recursos)|IsUserDefined,WorkloadGroup|
|WLGAllocationBySystemPercent|Alocação de grupo de carga de trabalho por parte do sistema|Percentagem|Máximo|A percentagem de alocação de recursos relativos a todo o sistema|IsUserDefined,WorkloadGroup|
|WLGAllocationByMaxResourcePercent|Alocação de grupo de carga de trabalho por percentagem máxima de recursos|Percentagem|Máximo|Apresenta a percentagem de alocação de recursos em relação à percentagem de recursos de limite efetivo por grupo de carga de trabalho. Esta métrica fornece a utilização eficaz do grupo de carga de trabalho|IsUserDefined,WorkloadGroup|
|WLGEffectiveCapResourcePercent|Por cento efetivo do recurso cap|Percentagem|Máximo|A percentagem efetiva de recursos de capital para o grupo de trabalho. Se houver outros grupos de carga de trabalho com min_percentage_resource > 0, o effective_cap_percentage_resource é reduzido proporcionalmente|IsUserDefined,WorkloadGroup|
|wlg_effective_min_resource_percent|Por cento de recursos min eficazes|Percentagem|Mínimo|A definição eficaz da percentagem de recursos min permitiu considerar o nível de serviço e as definições do grupo de carga de trabalho. O min_percentage_resource eficaz pode ser ajustado mais alto em níveis de serviço mais baixos|IsUserDefined,WorkloadGroup|
|WLGQueuedQueries|Consultas em fila de grupos de carga|Contagem|Total|Contagem cumulativa de pedidos em fila após o limite máximo de concuência ter sido atingido|IsUserDefined,WorkloadGroup|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/ambientes

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|IngressReceivedMessages|Mensagens Recebidas Ingress|Contagem|Total|Contagem de mensagens lidas de todas as fontes de eventos do centro de eventos do Evento ou IoT|Nenhum|
|IngressReceivedInvalidMesages|Ingress recebeu mensagens inválidas|Contagem|Total|Contagem de mensagens inválidas lidas de todas as fontes de eventos do centro de eventos do Evento ou IoT|Nenhum|
|IngressReceivedBytes|Ingress Recebido Bytes|Bytes|Total|Contagem de bytes lidos de todas as fontes do evento|Nenhum|
|IngressStoredBytes|Ingress Stored Bytes|Bytes|Total|Tamanho total dos eventos processados com sucesso e disponíveis para consulta|Nenhum|
|IngresssStoredEvents|Eventos Armazenados ingress|Contagem|Total|Contagem de eventos achatados processados com sucesso e disponíveis para consulta|Nenhum|
|IngressReceivedMesstimeLag|Ingress recebeu recado de tempo de mensagens|Segundos|Máximo|Diferença entre o tempo em que a mensagem é encosa na fonte do evento e o tempo que é processado em Ingress|Nenhum|
|IngressReceivedMessagesCountLag|Ingress recebeu recado de mensagens|Contagem|Média|Diferença entre o número de sequência da última mensagem encosa na partição de origem do evento e o número de sequência de mensagens que estão a ser processadas em Ingress|Nenhum|
|WarmStorageMaxProperties|Propriedades Max de Armazenamento Quente|Contagem|Máximo|Número máximo de propriedades utilizadas pelo ambiente para S1/S2 SKU e número máximo de propriedades permitidas pela Warm Store para PAYG SKU|Nenhum|
|WarmStorageUsedProperties|Propriedades usadas de armazenamento quente |Contagem|Máximo|Número de propriedades utilizadas pelo ambiente para S1/S2 SKU e número de propriedades utilizadas pela Warm Store para PAYG SKU|Nenhum|



## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/ambientes/eventsources

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|IngressReceivedMessages|Mensagens Recebidas Ingress|Contagem|Total|Contagem de mensagens lidas a partir da fonte do evento|Nenhum|
|IngressReceivedInvalidMesages|Ingress recebeu mensagens inválidas|Contagem|Total|Contagem de mensagens inválidas lidas a partir da fonte do evento|Nenhum|
|IngressReceivedBytes|Ingress Recebido Bytes|Bytes|Total|Contagem de bytes lidos a partir da fonte do evento|Nenhum|
|IngressStoredBytes|Ingress Stored Bytes|Bytes|Total|Tamanho total dos eventos processados com sucesso e disponíveis para consulta|Nenhum|
|IngresssStoredEvents|Eventos Armazenados ingress|Contagem|Total|Contagem de eventos achatados processados com sucesso e disponíveis para consulta|Nenhum|
|IngressReceivedMesstimeLag|Ingress recebeu recado de tempo de mensagens|Segundos|Máximo|Diferença entre o tempo em que a mensagem é encosa na fonte do evento e o tempo que é processado em Ingress|Nenhum|
|IngressReceivedMessagesCountLag|Ingress recebeu recado de mensagens|Contagem|Média|Diferença entre o número de sequência da última mensagem encosa na partição de origem do evento e o número de sequência de mensagens que estão a ser processadas em Ingress|Nenhum|
|WarmStorageMaxProperties|Propriedades Max de Armazenamento Quente|Contagem|Máximo|Número máximo de propriedades utilizadas pelo ambiente para S1/S2 SKU e número máximo de propriedades permitidas pela Warm Store para PAYG SKU|Nenhum|
|WarmStorageUsedProperties|Propriedades usadas de armazenamento quente |Contagem|Máximo|Número de propriedades utilizadas pelo ambiente para S1/S2 SKU e número de propriedades utilizadas pela Warm Store para PAYG SKU|Nenhum|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Discos de leitura bytes/sec|BytesPerSecond|Média|Produção média de disco devido a operações de leitura durante o período de amostragem.|Nenhum|
|DiskWriteBytesPerSecond|Discos De Escrita Bytes/Sec|BytesPerSecond|Média|Produção média de disco devido a operações de escrita durante o período de amostragem.|Nenhum|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Produção total de disco devido a operações de leitura durante o período de amostragem.|Nenhum|
|Bytes de escrita de disco|Bytes de escrita de disco|Bytes|Total|Produção total de disco devido a operações de escrita durante o período de amostragem.|Nenhum|
|Operações de Leitura de Discos|Operações de leitura de discos|Contagem|Total|O número de operações de leitura de IO no período de amostragem anterior. Note que estas operações podem ser de tamanho variável.|Nenhum|
|DiskWriteOperations|Operações de escrita de discos|Contagem|Total|O número de operações de escrita de IO no período de amostragem anterior. Note que estas operações podem ser de tamanho variável.|Nenhum|
|Operações de leitura de disco/sec|Operações de leitura de disco/sec|CondePerSecond|Média|O número médio de operações de leitura de IO no período de amostragem anterior. Note que estas operações podem ser de tamanho variável.|Nenhum|
|Operações de escrita de discos/seg|Operações de escrita de discos/seg|CondePerSecond|Média|O número médio de operações de escrita de IO no período de amostragem anterior. Note que estas operações podem ser de tamanho variável.|Nenhum|
|DiskReadLatency|Leitura de disco Latência|Milissegundos|Média|Latência de leitura total. A soma do dispositivo e o núcleo lêem latências.|Nenhum|
|DiskWriteLatency|Latência de escrita de disco|Milissegundos|Média|Latência total de escrita. A soma do dispositivo e o núcleo escrevem latências.|Nenhum|
|NetworkInBytesPerSecond|Rede em Bytes/Sec|BytesPerSecond|Média|Produção média de rede para tráfego recebido.|Nenhum|
|NetworkOutBytesPerSecond|Network out Bytes/Sec|BytesPerSecond|Média|Produção média de rede para tráfego transmitido.|Nenhum|
|Entrada na Rede|Entrada na Rede|Bytes|Total|Produção total de rede para tráfego recebido.|Nenhum|
|Saída da Rede|Saída da Rede|Bytes|Total|Produção total de rede para tráfego transmitido.|Nenhum|
|MemóriaSUsed|Memória utilizada|Bytes|Média|A quantidade de memória da máquina que está a ser utilizada pelo VM.|Nenhum|
|Memorygranted|Memória Concedida|Bytes|Média|A quantidade de memória que foi concedida ao VM pelo hospedeiro. A memória não é concedida ao hospedeiro até que seja tocada uma vez e a memória concedida pode ser trocada ou balonizada se o VMkernel precisar da memória.|Nenhum|
|MemoryActive|Memória Ativa|Bytes|Média|A quantidade de memória usada pelo VM no passado pequena janela de tempo. Este é o número "verdadeiro" de quanta memória o VM atualmente precisa. Memória adicional e não uusada pode ser trocada ou balonizada sem impacto no desempenho do hóspede.|Nenhum|
|Percentagem da CPU|Percentagem da CPU|Percentagem|Média|A utilização do CPU. Este valor é reportado com 100% representando todos os núcleos de processadores no sistema. Como exemplo, um VM de duas vias que usa 50% de um sistema de quatro núcleos está a usar completamente dois núcleos.|Nenhum|
|PercentagemCpuReady|Percentagem CPU Pronta|Milissegundos|Total|Tempo pronto é o tempo gasto à espera que os CPU(s) fiquem disponíveis no intervalo de atualização anterior.|Nenhum|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|CpuPercentage|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Instância|
|MemóriaPercentage|Percentagem de Memória|Percentagem|Média|Percentagem de Memória|Instância|
|DiskQueueLength|Comprimento da fila do disco|Contagem|Média|Comprimento da fila do disco|Instância|
|HttpQueueLength|Comprimento da fila http|Contagem|Média|Comprimento da fila http|Instância|
|BytesReceived|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Data out|Bytes|Total|Data out|Instância|
|TcpSynsent|TCP Syn enviado|Contagem|Média|TCP Syn enviado|Instância|
|TcpSynReceived|Sina TCP recebida|Contagem|Média|Sina TCP recebida|Instância|
|TcpEstablished|TCP Estabelecido|Contagem|Média|TCP Estabelecido|Instância|
|TcpFinWait1|TCP Fin Wait 1|Contagem|Média|TCP Fin Wait 1|Instância|
|TcpFinWait2|TCP Fin Wait 2|Contagem|Média|TCP Fin Wait 2|Instância|
|TcpClosing|Fecho da TCP|Contagem|Média|Fecho da TCP|Instância|
|TcpCloseWait|Espera de fecho de TCP|Contagem|Média|Espera de fecho de TCP|Instância|
|TcpLastAck|TCP Last Ack|Contagem|Média|TCP Last Ack|Instância|
|TcpTimeWait|Espera de tempo da TCP|Contagem|Média|Espera de tempo da TCP|Instância|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (excluindo funções) 

> [!NOTE]
> **O Uso do Sistema de Ficheiros** é uma nova métrica que está a ser lançada globalmente, não são esperados dados a menos que tenha sido whitelisted para pré-visualização privada.

> [!IMPORTANT]
> **O tempo médio de resposta** será depreciado para evitar confusões com agregações métricas. Utilize o **Tempo de Resposta** como substituto.

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Horário do Cpu|Hora do CPU|Segundos|Total|Hora do CPU|Instância|
|Pedidos|Pedidos|Contagem|Total|Pedidos|Instância|
|BytesReceived|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Data out|Bytes|Total|Data out|Instância|
|Http101|Http 101|Contagem|Total|Http 101|Instância|
|Http2xx|Http 2xx|Contagem|Total|Http 2xx|Instância|
|Http3xx|Http 3xx|Contagem|Total|Http 3xx|Instância|
|Http401|Http 401|Contagem|Total|Http 401|Instância|
|Http403|Http 403|Contagem|Total|Http 403|Instância|
|Http404|Http 404|Contagem|Total|Http 404|Instância|
|Http406|Http 406|Contagem|Total|Http 406|Instância|
|Http4xx|Http 4xx|Contagem|Total|Http 4xx|Instância|
|Http5xx|http Erros do servidor|Contagem|Total|http Erros do servidor|Instância|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|Média MemoryWorkingSet|Conjunto de trabalho de memória média|Bytes|Média|Conjunto de trabalho de memória média|Instância|
|HttpResponseTime|Tempo de resposta|Segundos|Total|Tempo de resposta|Instância|
|Média De Respostas|Tempo médio de resposta (precotado)|Segundos|Média|Tempo médio de resposta|Instância|
|AppConnections|Ligações|Contagem|Média|Ligações|Instância|
|Pegas|N.º de Identificadores|Contagem|Média|N.º de Identificadores|Instância|
|Fios|Contagem de fios|Contagem|Média|Contagem de fios|Instância|
|PrivateBytes|Bytes privados|Bytes|Média|Bytes privados|Instância|
|IoReadBytesPerSecond|IO Ler Bytes por segundo|BytesPerSecond|Total|IO Ler Bytes por segundo|Instância|
|IoWriteBytesPerSecond|IO Escrever Bytes por segundo|BytesPerSecond|Total|IO Escrever Bytes por segundo|Instância|
|IoOtherBytesPerSecond|IO Outros Bytes por Segundo|BytesPerSecond|Total|IO Outros Bytes por Segundo|Instância|
|IoReadOperationsPerSecond|IO Ler Operações por segundo|BytesPerSecond|Total|IO Ler Operações por segundo|Instância|
|IoWriteOperationsPerSecond|IO Escrever Operações por segundo|BytesPerSecond|Total|IO Escrever Operações por segundo|Instância|
|IoOtherOperationsPerSecond|IO Outras Operações por Segundo|BytesPerSecond|Total|IO Outras Operações por Segundo|Instância|
|PedidosInApplicationQueue|Pedidos na fila de aplicações|Contagem|Média|Pedidos na fila de aplicações|Instância|
|Antiguidades Atuais|Atuais Assembleias|Contagem|Média|Atuais Assembleias|Instância|
|Total DeDomínios|Domínios totais de aplicativos|Contagem|Média|Domínios totais de aplicativos|Instância|
|TotalAppDomainsUnloaded|Total de domínios de aplicações descarregados|Contagem|Média|Total de domínios de aplicações descarregados|Instância|
|Gen0Collections|Coleções de lixo gen 0|Contagem|Total|Coleções de lixo gen 0|Instância|
|Gen1Collections|Coleções de lixo da Gen 1|Contagem|Total|Coleções de lixo da Gen 1|Instância|
|Gen2Collections|Coleções de lixo gen 2|Contagem|Total|Coleções de lixo gen 2|Instância|
|HealthCheckStatus|Estado do controlo de saúde|Contagem|Média|Estado do controlo de saúde|Instância|
|FileSystemUsage|Utilização do sistema de ficheiros|Bytes|Média|Utilização do sistema de ficheiros|Nenhum|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funções)

> [!NOTE]
> **O Uso do Sistema de Ficheiros** é uma nova métrica que está a ser lançada globalmente, não são esperados dados a menos que tenha sido whitelisted para pré-visualização privada.

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|BytesReceived|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Data out|Bytes|Total|Data out|Instância|
|Http5xx|http Erros do servidor|Contagem|Total|http Erros do servidor|Instância|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|Média MemoryWorkingSet|Conjunto de trabalho de memória média|Bytes|Média|Conjunto de trabalho de memória média|Instância|
|FunExecutionUnnits|Unidades de execução de funções|MB / Milissegundos|Total|[Unidades de execução de funções](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instância|
|FunExecutionCount|Contagem de execução de funções|Contagem|Total|Contagem de execução de funções|Instância|
|PrivateBytes|Bytes privados|Bytes|Média|Bytes privados|Instância|
|IoReadBytesPerSecond|IO Ler Bytes por segundo|BytesPerSecond|Total|IO Ler Bytes por segundo|Instância|
|IoWriteBytesPerSecond|IO Escrever Bytes por segundo|BytesPerSecond|Total|IO Escrever Bytes por segundo|Instância|
|IoOtherBytesPerSecond|IO Outros Bytes por Segundo|BytesPerSecond|Total|IO Outros Bytes por Segundo|Instância|
|IoReadOperationsPerSecond|IO Ler Operações por segundo|BytesPerSecond|Total|IO Ler Operações por segundo|Instância|
|IoWriteOperationsPerSecond|IO Escrever Operações por segundo|BytesPerSecond|Total|IO Escrever Operações por segundo|Instância|
|IoOtherOperationsPerSecond|IO Outras Operações por Segundo|BytesPerSecond|Total|IO Outras Operações por Segundo|Instância|
|PedidosInApplicationQueue|Pedidos na fila de aplicações|Contagem|Média|Pedidos na fila de aplicações|Instância|
|Antiguidades Atuais|Atuais Assembleias|Contagem|Média|Atuais Assembleias|Instância|
|Total DeDomínios|Domínios totais de aplicativos|Contagem|Média|Domínios totais de aplicativos|Instância|
|TotalAppDomainsUnloaded|Total de domínios de aplicações descarregados|Contagem|Média|Total de domínios de aplicações descarregados|Instância|
|Gen0Collections|Coleções de lixo gen 0|Contagem|Total|Coleções de lixo gen 0|Instância|
|Gen1Collections|Coleções de lixo da Gen 1|Contagem|Total|Coleções de lixo da Gen 1|Instância|
|Gen2Collections|Coleções de lixo gen 2|Contagem|Total|Coleções de lixo gen 2|Instância|
|HealthCheckStatus|Estado do controlo de saúde|Contagem|Média|Estado do controlo de saúde|Instância|
|FileSystemUsage|Utilização do sistema de ficheiros|Bytes|Média|Utilização do sistema de ficheiros|Nenhum|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Horário do Cpu|Hora do CPU|Segundos|Total|Hora do CPU|Instância|
|Pedidos|Pedidos|Contagem|Total|Pedidos|Instância|
|BytesReceived|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Data out|Bytes|Total|Data out|Instância|
|Http101|Http 101|Contagem|Total|Http 101|Instância|
|Http2xx|Http 2xx|Contagem|Total|Http 2xx|Instância|
|Http3xx|Http 3xx|Contagem|Total|Http 3xx|Instância|
|Http401|Http 401|Contagem|Total|Http 401|Instância|
|Http403|Http 403|Contagem|Total|Http 403|Instância|
|Http404|Http 404|Contagem|Total|Http 404|Instância|
|Http406|Http 406|Contagem|Total|Http 406|Instância|
|Http4xx|Http 4xx|Contagem|Total|Http 4xx|Instância|
|Http5xx|http Erros do servidor|Contagem|Total|http Erros do servidor|Instância|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|Média MemoryWorkingSet|Conjunto de trabalho de memória média|Bytes|Média|Conjunto de trabalho de memória média|Instância|
|Média De Respostas|Tempo médio de resposta|Segundos|Média|Tempo médio de resposta|Instância|
|HttpResponseTime|Tempo de resposta|Segundos|Média|Tempo de resposta|Instância|
|FunExecutionUnnits|Unidades de execução de funções|Contagem|Total|Unidades de execução de funções|Instância|
|FunExecutionCount|Contagem de execução de funções|Contagem|Total|Contagem de execução de funções|Instância|
|AppConnections|Ligações|Contagem|Média|Ligações|Instância|
|Pegas|N.º de Identificadores|Contagem|Média|N.º de Identificadores|Instância|
|Fios|Contagem de fios|Contagem|Média|Contagem de fios|Instância|
|PrivateBytes|Bytes privados|Bytes|Média|Bytes privados|Instância|
|IoReadBytesPerSecond|IO Ler Bytes por segundo|BytesPerSecond|Total|IO Ler Bytes por segundo|Instância|
|IoWriteBytesPerSecond|IO Escrever Bytes por segundo|BytesPerSecond|Total|IO Escrever Bytes por segundo|Instância|
|IoOtherBytesPerSecond|IO Outros Bytes por Segundo|BytesPerSecond|Total|IO Outros Bytes por Segundo|Instância|
|IoReadOperationsPerSecond|IO Ler Operações por segundo|BytesPerSecond|Total|IO Ler Operações por segundo|Instância|
|IoWriteOperationsPerSecond|IO Escrever Operações por segundo|BytesPerSecond|Total|IO Escrever Operações por segundo|Instância|
|IoOtherOperationsPerSecond|IO Outras Operações por Segundo|BytesPerSecond|Total|IO Outras Operações por Segundo|Instância|
|PedidosInApplicationQueue|Pedidos na fila de aplicações|Contagem|Média|Pedidos na fila de aplicações|Instância|
|Antiguidades Atuais|Atuais Assembleias|Contagem|Média|Atuais Assembleias|Instância|
|Total DeDomínios|Domínios totais de aplicativos|Contagem|Média|Domínios totais de aplicativos|Instância|
|TotalAppDomainsUnloaded|Total de domínios de aplicações descarregados|Contagem|Média|Total de domínios de aplicações descarregados|Instância|
|Gen0Collections|Coleções de lixo gen 0|Contagem|Total|Coleções de lixo gen 0|Instância|
|Gen1Collections|Coleções de lixo da Gen 1|Contagem|Total|Coleções de lixo da Gen 1|Instância|
|Gen2Collections|Coleções de lixo gen 2|Contagem|Total|Coleções de lixo gen 2|Instância|
|HealthCheckStatus|Estado do controlo de saúde|Contagem|Média|Estado do controlo de saúde|Instância|
|FileSystemUsage|Utilização do sistema de ficheiros|Bytes|Média|Utilização do sistema de ficheiros|Nenhum|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Pedidos|Pedidos|Contagem|Total|Pedidos|Instância|
|BytesReceived|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Data out|Bytes|Total|Data out|Instância|
|Http101|Http 101|Contagem|Total|Http 101|Instância|
|Http2xx|Http 2xx|Contagem|Total|Http 2xx|Instância|
|Http3xx|Http 3xx|Contagem|Total|Http 3xx|Instância|
|Http401|Http 401|Contagem|Total|Http 401|Instância|
|Http403|Http 403|Contagem|Total|Http 403|Instância|
|Http404|Http 404|Contagem|Total|Http 404|Instância|
|Http406|Http 406|Contagem|Total|Http 406|Instância|
|Http4xx|Http 4xx|Contagem|Total|Http 4xx|Instância|
|Http5xx|http Erros do servidor|Contagem|Total|http Erros do servidor|Instância|
|Média De Respostas|Tempo médio de resposta|Segundos|Média|Tempo médio de resposta|Instância|
|CpuPercentage|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Instância|
|MemóriaPercentage|Percentagem de Memória|Percentagem|Média|Percentagem de Memória|Instância|
|DiskQueueLength|Comprimento da fila do disco|Contagem|Média|Comprimento da fila do disco|Instância|
|HttpQueueLength|Comprimento da fila http|Contagem|Média|Comprimento da fila http|Instância|
|Ativarequests|Pedidos Ativos|Contagem|Total|Pedidos Ativos|Instância|
|TotalFrontEnds|Total de extremidades frontais|Contagem|Média|Total de extremidades frontais|Nenhum|
|Pequenas Aplicações de Planos doAppService|Trabalhadores do plano de aplicação de pequenas aplicações|Contagem|Média|Trabalhadores do plano de aplicação de pequenas aplicações|Nenhum|
|MediumAppServicePlanInstances|Trabalhadores do Plano de Serviço de Aplicações Médias|Contagem|Média|Trabalhadores do Plano de Serviço de Aplicações Médias|Nenhum|
|Grandes Aplicações de Planos de Serviço|Trabalhadores do plano de aplicação grande|Contagem|Média|Trabalhadores do plano de aplicação grande|Nenhum|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|TrabalhadoresTotal|Total de Trabalhadores|Contagem|Média|Total de Trabalhadores|Nenhum|
|Trabalhadores disponíveis|Trabalhadores disponíveis|Contagem|Média|Trabalhadores disponíveis|Nenhum|
|TrabalhadoresUsed|Trabalhadores Usados|Contagem|Média|Trabalhadores Usados|Nenhum|
|CpuPercentage|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Instância|
|MemóriaPercentage|Percentagem de Memória|Percentagem|Média|Percentagem de Memória|Instância|
## <a name="next-steps"></a>Passos seguintes
* [Leia sobre métricas no Azure Monitor](data-platform.md)
* [Criar alertas para as métricas](alerts-overview.md)
* [Métricas de exportação para armazenamento, Centro de Eventos ou Log Analytics](platform-logs-overview.md)

