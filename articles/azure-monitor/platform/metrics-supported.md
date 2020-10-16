---
title: Azure Monitor suportado métricas por tipo de recurso
description: Lista de métricas disponíveis para cada tipo de recurso com monitor Azure.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 07/16/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 59df49d320b23686a3d053335ea2b95e98125b28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88135560"
---
# <a name="supported-metrics-with-azure-monitor"></a>Métricas suportadas com monitor Azure

> [!NOTE]
> Esta lista é gerada em grande parte automaticamente a partir da Azure Monitor Metrics REST API. Qualquer modificação feita a esta lista via GitHub pode ser escrita sem aviso prévio. Contacte o autor deste artigo para obter mais detalhes sobre como fazer atualizações permanentes.

O Azure Monitor fornece várias formas de interagir com as métricas, incluindo cartografá-las no portal, acessá-las através da API REST, ou questioná-las usando PowerShell ou CLI. 

Este artigo é uma lista completa de todas as métricas da plataforma (isto é, automaticamente recolhidas) atualmente disponíveis com o pipeline métrico consolidado do Azure Monitor. A lista foi atualizada pela última vez a 27 de março de 2020. As métricas alteradas ou adicionadas após esta data podem não aparecer abaixo. Para consultar e aceder à lista de métricas programáticamente, utilize a [versão api 2018-01-01](/rest/api/monitor/metricdefinitions). Outras métricas que não constam desta lista podem estar disponíveis no portal ou usando APIs legados.

As métricas são organizadas por fornecedores de recursos e tipo de recursos. Para obter uma lista de serviços e os fornecedores de recursos que lhes pertencem, consulte [os fornecedores de recursos para os serviços Azure.](../../azure-resource-manager/management/azure-services-resource-providers.md) 

## <a name="exporting-platform-metrics-to-other-locations"></a>Métricas da plataforma de exportação para outros locais

Pode exportar as métricas da plataforma do gasoduto Azure monitor para outros locais de duas maneiras.
1. Use as [métricas REST API](/rest/api/monitor/metrics/list)
2. Use [definições de diagnóstico](diagnostic-settings.md) para encaminhar as métricas da plataforma para 
    - Storage do Azure
    - Registos do Monitor Azure (e, portanto, Log Analytics)
    - Centros de eventos, que é como osbtê-los para sistemas não-Microsoft 

Usar definições de diagnóstico é a maneira mais fácil de encaminhar as métricas, mas existem algumas limitações: 

- **Algumas não exportáveis** - Todas as métricas são exportáveis utilizando a API REST, mas algumas não podem ser exportadas usando Configurações de Diagnóstico devido a complexidades no backend do Monitor Azure. A coluna *exportável através de Definições de Diagnóstico* nas tabelas abaixo lista quais as métricas que podem ser exportadas desta forma.  

- **Métricas multidimensionais** - O envio de métricas multidimensionais para outros locais através de configurações de diagnóstico não é suportado atualmente. As métricas com dimensões são exportadas como métricas dimensionais simples e agregadas em valores de dimensões. *Por exemplo*: a métrica “Mensagens Recebidas” num hub do Hub de Eventos pode ser explorada e representada ao nível da linha. No entanto, se for exportada através das definições de diagnóstico, a métrica será representada como todas as mensagens recebidas em todas as filas do hub do Hub de Eventos.

## <a name="guest-os-and-host-os-metrics"></a>Métricas de OS e Anfitrião

> [!WARNING]
> As métricas para o sistema operativo convidado (OS convidados) que funciona em Azure Virtual Machines, Service Fabric e Cloud Services **NÃO** estão listadas aqui. As métricas de SO do hóspede devem ser recolhidas através de um ou mais agentes que funcionam no ou como parte do sistema operativo do hóspede.  As métricas de SO do hóspede incluem contadores de desempenho que acompanham a percentagem de CPU ou o uso da memória, ambos frequentemente utilizados para o dimensionamento automático ou alerta. 
>
> **As métricas de host OS estão disponíveis e listadas abaixo.** Não são os mesmos. As métricas do Host OS dizem respeito à sessão De Hiper-V que acolhe a sessão de SO do seu convidado. 

> [!TIP]
> A melhor prática é utilizar e configurar a [extensão Azure Diagnostics](diagnostics-extension-overview.md) para enviar métricas de desempenho do SO para a mesma base de dados métrica do Azure Monitor onde as métricas da plataforma são armazenadas. A extensão encaminha as métricas de SO do hóspede através das [métricas personalizadas](metrics-custom-overview.md) API. Em seguida, você pode cartografar, alertar e de outra forma usar métricas de SO convidados como métricas de plataforma. Em alternativa ou além disso, pode utilizar o agente Log Analytics para enviar métricas de SO para registos de monitores Azure / Log Analytics. Lá pode consultar essas métricas em combinação com dados não métricos. 

Para obter informações adicionais importantes, consulte [a visão geral dos agentes de monitorização.](agents-overview.md)    

## <a name="table-formatting"></a>Formatação de mesa

> [!IMPORTANT] 
> Esta última atualização adiciona uma nova coluna e reordenou as métricas para serem alfabéticas. As informações de adição significam que as tabelas abaixo podem ter uma barra de deslocação horizontal na parte inferior, dependendo da largura da janela do seu navegador. Se acredita que está a perder informação, use a barra de pergaminho para ver toda a mesa.


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servidores

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|Sim|Memória: Preço corrente mais limpo|Contagem|Média|Preço atual da memória, $/byte/time, normalizado para 1000.|ServerResourceType|
|CleanerMemoryNonshrinkable|Sim|Memória: Memória limpa não é insuportável|Bytes|Média|Quantidade de memória, em bytes, não sujeita a purga pelo limpador de fundos.|ServerResourceType|
|LimpadorMesshrinkable|Sim|Memória: Memória mais limpa encolhível|Bytes|Média|Quantidade de memória, em bytes, sujeito a purga pelo limpador de fundos.|ServerResourceType|
|CommandPoolBusyThreads|Sim|Threads: Linhas movimentadas da piscina de comando|Contagem|Média|Número de fios ocupados na piscina de fio de comando.|ServerResourceType|
|CommandPoolIdleThreads|Sim|Fios: Fios inativos da piscina de comando|Contagem|Média|Número de fios inativos na piscina do fio de comando.|ServerResourceType|
|ComandoPoolJobQueueLength|Sim|Comprimento da fila de trabalho da piscina de comando|Contagem|Média|Número de empregos na fila da piscina de fios de comando.|ServerResourceType|
|Correntes Deconhecões|Sim|Ligação: Ligações atuais|Contagem|Média|Número atual de ligações ao cliente estabelecidas.|ServerResourceType|
|Atuais Sesessãos de 2009|Sim|Sessões de Utilizador atuais|Contagem|Média|Número atual de sessões de utilizador estabelecidas.|ServerResourceType|
|LongParsingBusyThreads|Sim|Threads: Longas análises de fios ocupados|Contagem|Média|Número de fios ocupados na piscina de rosca de análise longa.|ServerResourceType|
|LongParsingIdleThreads|Sim|Threads: Fios longos de análise ocioso|Contagem|Média|Número de fios ociosos na piscina de rosca de análise longa.|ServerResourceType|
|LongParsingJobQueueLength|Sim|Threads: Comprimento da fila de trabalho de análise longa|Contagem|Média|Número de empregos na fila da piscina de rosca de longas análises.|ServerResourceType|
|mashup_engine_memory_metric|Sim|Memória do motor M|Bytes|Média|Utilização da memória por processos do motor de mashup|ServerResourceType|
|mashup_engine_private_bytes_metric|Sim|M Motor Private Bytes|Bytes|Média|Bytes privados utiliza-se através de processos de motor de mashup.|ServerResourceType|
|mashup_engine_qpu_metric|Sim|QPU do motor M|Contagem|Média|Utilização do QPU por processos de motor de mashup|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Sim|M Motor Virtual Bytes|Bytes|Média|Uso de bytes virtuais por processos de motor de mashup.|ServerResourceType|
|memory_metric|Sim|Memória|Bytes|Média|Memória. Gama 0-25 GB para S1, 0-50 GB para S2 e 0-100 GB para S4|ServerResourceType|
|memory_thrashing_metric|Sim|Degradação de Memória|Percentagem|Média|Memória média a bater.|ServerResourceType|
|MemoryLimitHard|Sim|Memória: Limite de memória difícil|Bytes|Média|Limite de memória rígido, a partir do ficheiro de configuração.|ServerResourceType|
|MemóriaLimithigh|Sim|Memória: Limite de memória Alto|Bytes|Média|Limite de memória elevado, a partir do ficheiro de configuração.|ServerResourceType|
|MemoryLimitLow|Sim|Memória: Limite de memória baixo|Bytes|Média|Limite de memória baixo, a partir do ficheiro de configuração.|ServerResourceType|
|MemoryLimitVertiPaq|Sim|Memória: Limite de memória VertiPaq|Bytes|Média|Limite de memória, a partir do ficheiro de configuração.|ServerResourceType|
|MemóriaSage|Sim|Memória: Utilização da memória|Bytes|Média|Utilização da memória do processo do servidor como usado no cálculo do preço de memória mais limpo. Igual a contrariar o Processo\PrivateBytes mais o tamanho dos dados mapeados pela memória, ignorando qualquer memória que tenha sido mapeada ou atribuída pelo motor de análise em memória xVelocity (VertiPaq) em excesso do limite de memória do motor xVelocity.|ServerResourceType|
|private_bytes_metric|Sim|Bytes privados|Bytes|Média|Bytes privados.|ServerResourceType|
|ProcessamentoPoolBusyIOJobThreads|Sim|Threads: Processamento de piscina ocupada linhas de trabalho de I/O|Contagem|Média|Número de fios a executar trabalhos de E/S na piscina de fios de processamento.|ServerResourceType|
|ProcessamentoPoolBusyNonIOThreads|Sim|Fios: Piscina de processamento ocupada fios não-I/O|Contagem|Média|Número de fios que executam trabalhos não-I/S na piscina de rosca de processamento.|ServerResourceType|
|ProcessamentoPoolIdleIOJobThreads|Sim|Fios: Processamento de linhas de trabalho I/O inativas da piscina|Contagem|Média|Número de fios inativos para trabalhos de E/S na piscina de rosca de processamento.|ServerResourceType|
|ProcessamentoPoolIdleNonIOThreads|Sim|Fios: Processamento de piscinas ociosas fios não-I/O|Contagem|Média|Número de fios inativos na piscina de fios de processamento dedicada a trabalhos não-I/S.|ServerResourceType|
|ProcessamentoPoolIOJobQueueLength|Sim|Threads: Processamento de piscina I/O comprimento da fila de trabalho|Contagem|Média|Número de trabalhos de E/S na fila da piscina de rosca de processamento.|ServerResourceType|
|ProcessamentoPoolJobQueueLength|Sim|Processamento de comprimento da fila de trabalho da piscina|Contagem|Média|Número de trabalhos não-I/O na fila da piscina de rosca de processamento.|ServerResourceType|
|qpu_metric|Sim|QPU|Contagem|Média|QPU. Intervalo 0-100 para S1, 0-200 para S2 e 0-400 para S4|ServerResourceType|
|QueryPoolBusyThreads|Sim|Linhas ocupadas da piscina de consulta|Contagem|Média|Número de fios ocupados na piscina de rosca de consulta.|ServerResourceType|
|QueryPoolIdleThreads|Sim|Fios: Linhas ociosas da piscina de consulta|Contagem|Média|Número de fios inativos para trabalhos de E/S na piscina de rosca de processamento.|ServerResourceType|
|QueryPoolJobQueueLength|Sim|Threads: Comprimento da fila do trabalho da piscina de consulta|Contagem|Média|Número de empregos na fila da piscina de fios de consulta.|ServerResourceType|
|Quota|Sim|Memória: Quota|Bytes|Média|Quota de memória atual, em bytes. A quota de memória também é conhecida como um subsídio de memória ou reserva de memória.|ServerResourceType|
|Quota Bloqueada|Sim|Memória: Quota Bloqueada|Contagem|Média|Número atual de pedidos de quotas que são bloqueados até que outras quotas de memória sejam libertadas.|ServerResourceType|
|RowsConvertedPerSec|Sim|Processamento: Linhas convertidas por seg|CondePerSecond|Média|Taxa de linhas convertidas durante o processamento.|ServerResourceType|
|RowsReadPerSec|Sim|Processamento: Linhas lidas por seg|CondePerSecond|Média|Taxa de linhas lidas de todas as bases de dados relacionais.|ServerResourceType|
|RowsWrittenPerSec|Sim|Processamento: Linhas escritas por seg|CondePerSecond|Média|Taxa de linhas escritas durante o processamento.|ServerResourceType|
|ShortParsingBusyThreads|Sim|Threads: Threads de análise curta|Contagem|Média|Número de fios ocupados na piscina de rosca de parsing curta.|ServerResourceType|
|ShortParsingIdleThreads|Sim|Fios: Fios curtos de parsing ocioso|Contagem|Média|Número de fios ociosos na piscina de rosca de parsing curta.|ServerResourceType|
|ShortParsingJobQueueLength|Sim|Threads: Comprimento da fila do trabalho de análise curta|Contagem|Média|Número de empregos na fila da piscina de rosca de pequena análise.|ServerResourceType|
|SuccessfullConnectionsPerSec|Sim|Conexões de sucesso por seg|CondePerSecond|Média|Taxa de conclusão de conexão bem sucedida.|ServerResourceType|
|TotalConnectionFailures|Sim|Falhas totais de ligação|Contagem|Média|Tentativas de ligação falhadas totais.|ServerResourceType|
|TotalConnectionRequests|Sim|Total de pedidos de conexão|Contagem|Média|Pedidos de ligação total. Estas são as chegadas.|ServerResourceType|
|VertiPaqNonpaged|Sim|Memória: VertiPaq Não Pageed|Bytes|Média|Bytes de memória bloqueados no conjunto de trabalho para utilização pelo motor de memória.|ServerResourceType|
|VertiPaqPaged|Sim|Memória: VertiPaq Paged|Bytes|Média|Bytes de memória paged em uso para dados de memória.|ServerResourceType|
|virtual_bytes_metric|Sim|Bytes Virtuais|Bytes|Média|Bytes virtuais.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|BackendDuration|Sim|Duração dos Pedidos de Backend|Milissegundos|Média|Duração dos Pedidos de Backend em milissegundos|Localização, Nome anfitrião|
|Capacidade|Sim|Capacidade|Percentagem|Média|Métrica de utilização para o serviço ApiManagement|Localização|
|Duração|Sim|Duração geral dos pedidos de gateway|Milissegundos|Média|Duração global dos pedidos gateway em milissegundos|Localização, Nome anfitrião|
|EventHubDroppedEvents|Sim|Eventos do EventHub abandonados|Contagem|Total|Número de eventos ignorados devido ao limite de tamanho da fila atingido|Localização|
|EventosHubRejectedEvents|Sim|Eventos de EventHub rejeitados|Contagem|Total|Número de eventos rejeitados do EventHub (configuração errada ou não autorizado)|Localização|
|EventoSHubSuccessfulEvents|Sim|Eventos de sucesso EventHub|Contagem|Total|Número de eventos de sucesso do EventHub|Localização|
|EventHubThrottledEvents|Sim|Eventos Throttled EventHub|Contagem|Total|Número de eventos acelerados eventHub|Localização|
|EventHubTimedoutEvents|Sim|Eventos Timed out EventHub|Contagem|Total|Número de eventos do EventHub cronometrado|Localização|
|EventHubTotalBytesSent|Sim|Tamanho dos eventos Do EventHub|Bytes|Total|Tamanho total dos eventos EventHub em bytes|Localização|
|EventHubTotalEvents|Sim|Total de eventos EventHub|Contagem|Total|Número de eventos enviados para o EventHub|Localização|
|EventHubTotalFailedEvents|Sim|Eventos falhados do EventHub|Contagem|Total|Número de eventos falhados do EventHub|Localização|
|Requessos Falhados|Sim|Pedidos de Gateway falhados (Deprecados)|Contagem|Total|Número de falhas nos pedidos de gateway - Use a métrica de pedido de várias dimensões com a dimensão GatewayResponseCodeCategoria|Localização, Nome anfitrião|
|RedeContividade|Sim|Estado da conectividade da rede dos recursos (pré-visualização)|Contagem|Média|Estado de conectividade da rede dos tipos de recursos dependentes do serviço de Gestão API|Localização,Tip de Recursos|
|Outros Requests|Sim|Outros pedidos de gateway (precotados)|Contagem|Total|Número de outros pedidos de gateway - Use a métrica de pedido de várias dimensões com a dimensão GatewayResponseCodeCategoria|Localização, Nome anfitrião|
|Pedidos|Sim|Pedidos|Contagem|Total|Métricas de pedido de gateway com múltiplas dimensões|Localização, Nome anfitrião, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory, GatewayResponseCodeCategory|
|Requess de sucesso|Sim|Pedidos de Gateway bem sucedidos (Deprecados)|Contagem|Total|Número de pedidos de gateway bem sucedidos - Use a métrica de pedido de várias dimensões com a dimensão GatewayResponseCodeCategoria|Localização, Nome anfitrião|
|TotalRequests|Sim|Total de pedidos de gateway (precotado)|Contagem|Total|Número de pedidos de gateway - Use métrica de pedido de várias dimensões com dimensão GatewayResponseCodeCategoria|Localização, Nome anfitrião|
|Requessionais não autorizados|Sim|Pedidos de Gateway não autorizados (Deprecados)|Contagem|Total|Número de pedidos de gateway não autorizados - Use a métrica de pedido de várias dimensões com a dimensão GatewayResponseCodeCategoria|Localização, Nome anfitrião|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|Sim|HttpIncomingRequestCount|Contagem|Contagem|Número total de pedidos de http recebidas.|Código de Estado, Autenticação|
|HttpIncomingRequestDuration|Sim|HttpIncomingRequestDuration|Contagem|Média|Latência num pedido http.|Código de Estado, Autenticação|
|ThrottledHttpRequestCount|Sim|ThrottledHttpRequestCount|Contagem|Contagem|Solicitações http throttled.|Sem Dimensões|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/primavera

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|jvm.gc.live.data.size|Sim|jvm.gc.live.data.size|Bytes|Média|Tamanho da piscina de memória de geração velha depois de um GC completo|Implantação, AppName, Pod|
|jvm.gc.max.data.size|Sim|jvm.gc.max.data.size|Bytes|Média|Tamanho máximo da piscina de memória de geração antiga|Implantação, AppName, Pod|
|jvm.gc.memory.alocado|Sim|jvm.gc.memory.alocado|Bytes|Máximo|Incrementado para um aumento no tamanho do pool de memória de geração jovem após um GC para antes do próximo|Implantação, AppName, Pod|
|jvm.gc.memory.promovido|Sim|jvm.gc.memory.promovido|Bytes|Máximo|Contagem de aumentos positivos no tamanho do pool de memória de geração antiga antes da GC para depois de GC|Implantação, AppName, Pod|
|jvm.gc.pause.total.count|Sim|jvm.gc.pause.total.count|Contagem|Total|Contagem de pausas GC|Implantação, AppName, Pod|
|jvm.gc.pause.total.time|Sim|jvm.gc.pause.total.time|Milissegundos|Total|GC Pausa Tempo Total|Implantação, AppName, Pod|
|jvm.memory.committed|Sim|jvm.memory.committed|Bytes|Média|Memória atribuída a JVM em bytes|Implantação, AppName, Pod|
|jvm.memory.max|Sim|jvm.memory.max|Bytes|Máximo|A quantidade máxima de memória em bytes que pode ser usada para a gestão da memória|Implantação, AppName, Pod|
|jvm.memory.usado|Sim|jvm.memory.usado|Bytes|Média|Memória de aplicativos utilizada em bytes|Implantação, AppName, Pod|
|process.cpu.usage|Sim|process.cpu.usage|Percentagem|Média|App JVM CPU Percentagem de Utilização|Implantação, AppName, Pod|
|system.cpu.usage|Sim|system.cpu.usage|Percentagem|Média|O recente uso do CPU para todo o sistema|Implantação, AppName, Pod|
|tomcat.global.erro|Sim|tomcat.global.erro|Contagem|Total|Erro Global de Tomcat|Implantação, AppName, Pod|
|tomcat.global.recebeu|Sim|tomcat.global.recebeu|Bytes|Total|Tomcat Total Recebido Bytes|Implantação, AppName, Pod|
|tomcat.global.request.avg.time|Sim|tomcat.global.request.avg.time|Milissegundos|Média|Tomcat Pede tempo médio|Implantação, AppName, Pod|
|tomcat.global.request.max|Sim|tomcat.global.request.max|Milissegundos|Máximo|Tomcat Request Max Time|Implantação, AppName, Pod|
|tomcat.global.request.total.count|Sim|tomcat.global.request.total.count|Contagem|Total|Tomcat Request Contagem Total|Implantação, AppName, Pod|
|tomcat.global.request.total.time|Sim|tomcat.global.request.total.time|Milissegundos|Total|Pedido tomcat tempo total|Implantação, AppName, Pod|
|tomcat.global.enviado|Sim|tomcat.global.enviado|Bytes|Total|Tomcat Total Enviado Bytes|Implantação, AppName, Pod|
|tomcat.sessions.ative.current|Sim|tomcat.sessions.ative.current|Contagem|Total|Contagem ativa da sessão de Tomcat|Implantação, AppName, Pod|
|tomcat.sessions.ative.max|Sim|tomcat.sessions.ative.max|Contagem|Total|Tomcat Session Max Ative Count|Implantação, AppName, Pod|
|tomcat.sessions.alive.max|Sim|tomcat.sessions.alive.max|Milissegundos|Máximo|Tomcat Session Max Alive Tempo|Implantação, AppName, Pod|
|tomcat.sessions.created|Sim|tomcat.sessions.created|Contagem|Total|Tomcat Session Criou Contagem|Implantação, AppName, Pod|
|tomcat.sessions.expirou|Sim|tomcat.sessions.expirou|Contagem|Total|Contagem expirada da sessão de Tomcat|Implantação, AppName, Pod|
|tomcat.sessions.rejeitado|Sim|tomcat.sessions.rejeitado|Contagem|Total|Contagem rejeitada da sessão de Tomcat|Implantação, AppName, Pod|
|tomcat.threads.config.max|Sim|tomcat.threads.config.max|Contagem|Total|Tomcat Config Max Thread Count|Implantação, AppName, Pod|
|tomcat.threads.current|Sim|tomcat.threads.current|Contagem|Total|Contagem de fios de corrente tomcat|Implantação, AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automation

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|TotalJob|Sim|Total de empregos|Contagem|Total|O número total de empregos|Runbook, Status|
|TotalUpdateDeploymentMachineRuns|Sim|Funciona a máquina de implementação total de atualizações|Contagem|Total|A máquina de implementação total de atualização de software é executada numa implementação de atualização de software|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|Sim|Execuções de implementação de atualização total|Contagem|Total|Total de execuções de atualização de software executa|SoftwareUpdateConfigurationName, Estado|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batcontas ch/batch

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|CoreCount|Não|Contagem de núcleos dedicada|Contagem|Total|Número total de núcleos dedicados na conta do lote|Sem Dimensões|
|Criação NodeCount|Não|Criação de Contagem de Nó|Contagem|Total|Número de nós a ser criado|Sem Dimensões|
|IdleNodeCount|Não|Conde nó inativo|Contagem|Total|Número de nós ociosos|Sem Dimensões|
|JobDeleteCompleteEvent|Sim|Trabalho Eliminar Eventos Completos|Contagem|Total|Número total de postos de trabalho que foram eliminados com êxito.|jobId|
|JobDeleteStartEvent|Sim|Trabalho eliminar eventos iniciá-lo|Contagem|Total|Número total de postos de trabalho que foram solicitados para serem suprimidos.|jobId|
|JobDisableCompleteEvent|Sim|Eventos completos para desativação de emprego|Contagem|Total|Número total de postos de trabalho que foram incapacitados com sucesso.|jobId|
|JobDisableStartEvent|Sim|Eventos de início de desativação de emprego|Contagem|Total|Número total de postos de trabalho que foram solicitados para serem incapacitados.|jobId|
|JobStartEvent|Sim|Eventos de início de emprego|Contagem|Total|Número total de postos de trabalho que foram iniciados com sucesso.|jobId|
|JobTerminateCompleteEvent|Sim|Emprego termina eventos completos|Contagem|Total|Número total de postos de trabalho que foram encerrados com sucesso.|jobId|
|JobTerminateStartEvent|Sim|Eventos de início de emprego terminam|Contagem|Total|Número total de postos de trabalho que foram solicitados para serem encerrados.|jobId|
|LeavingPoolNodeCount|Não|Deixando a contagem do nó de piscina|Contagem|Total|Número de nós que saem da Piscina|Sem Dimensões|
|LowPriorityCoreCount|Não|Contagem do Núcleo de BaixaPrioridade|Contagem|Total|Número total de núcleos de baixa prioridade na conta do lote|Sem Dimensões|
|OfflineNodeCount|Não|Contagem de nóles offline|Contagem|Total|Número de nós offline|Sem Dimensões|
|Evento PoolCreate|Sim|Criar Eventos de Criação de Piscinas|Contagem|Total|Número total de piscinas que foram criadas|poolId|
|PoolDeleteCompleteEvent|Sim|Piscina Eliminar Eventos Completos|Contagem|Total|Número total de eliminações de piscinas que tenham concluído|poolId|
|PoolDeleteStartEvent|Sim|Pool Delete Eventos iniciá-lo|Contagem|Total|Número total de eliminações de piscinas que começaram|poolId|
|PoolResizeCompleteEvent|Sim|Piscina Redimensione Eventos Completos|Contagem|Total|Número total de redimensionações de piscina que completaram|poolId|
|PoolResizeStartEvent|Sim|Eventos de início de piscina redimensionam|Contagem|Total|Número total de redimensionações de piscina que começaram|poolId|
|PreemptedNodeCount|Não|Contagem de nódoaista preventiva|Contagem|Total|Número de nós pré-emitidos|Sem Dimensões|
|RebootingNodeCount|Não|Contagem de nó de reinicialização|Contagem|Total|Número de nós reiniciantes|Sem Dimensões|
|ReimagingNodeCount|Não|Contagem de nó de reimaging|Contagem|Total|Número de nós de reimaging|Sem Dimensões|
|RunningNodeCount|Não|Contagem de nó de corrida|Contagem|Total|Número de nós em execução|Sem Dimensões|
|InícioNodeCount|Não|Contagem inicial do nó|Contagem|Total|Número de nós a partir|Sem Dimensões|
|StartTaskFailedNodeCount|Não|Início da contagem de nó falhado da tarefa|Contagem|Total|Número de nós onde a Tarefa inicial falhou|Sem Dimensões|
|TaskCompleteEvent|Sim|Eventos completos de tarefas|Contagem|Total|Número total de tarefas que completaram|poolId, jobId|
|TaskFailEvent|Sim|Eventos de Falha de Tarefa|Contagem|Total|Número total de tarefas que foram concluídas num estado falhado|poolId, jobId|
|TaskStartEvent|Sim|Eventos de início de tarefa|Contagem|Total|Número total de tarefas que começaram|poolId, jobId|
|TotalLowPriorityNodeCount|Não|Conde Low-Priority nó|Contagem|Total|Número total de nós de baixa prioridade na conta do lote|Sem Dimensões|
|TotalNodeCount|Não|Conde de Nó dedicado|Contagem|Total|Número total de nós dedicados na conta do lote|Sem Dimensões|
|Não utilizávelNodeCount|Não|Contagem de nó inutilizáveis|Contagem|Total|Número de nós inutilizáveis|Sem Dimensões|
|WaitingForStartTaskNodeCount|Não|À espera da contagem de nó de tarefa inicial|Contagem|Total|Número de nós à espera que a Tarefa inicial esteja concluída|Sem Dimensões|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/espaços de trabalho

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Núcleos Ativos|Sim|Núcleos Ativos|Contagem|Média|Número de núcleos ativos|Cenário, ClusterName|
|Nóns ativos|Sim|Nóns ativos|Contagem|Média|Número de nós em execução|Cenário, ClusterName|
|Núcleos Ociosos|Sim|Núcleos Ociosos|Contagem|Média|Número de núcleos ociosos|Cenário, ClusterName|
|Nódoas Ociosas|Sim|Nódoas Ociosas|Contagem|Média|Número de nós ociosos|Cenário, ClusterName|
|Trabalho Concluído|Sim|Trabalho Concluído|Contagem|Total|Número de postos de trabalho concluídos|Cenário, ClusterName, ResultadoType|
|Trabalho submetido|Sim|Trabalho submetido|Contagem|Total|Número de postos de trabalho submetidos|Cenário, ClusterName|
|Deixando os Núcleos|Sim|Deixando os Núcleos|Contagem|Média|Número de núcleos de saída|Cenário, ClusterName|
|Deixando os nóns|Sim|Deixando os nóns|Contagem|Média|Número de nós de saída|Cenário, ClusterName|
|Núcleos Preempted|Sim|Núcleos Preempted|Contagem|Média|Número de núcleos pré-apropriados|Cenário, ClusterName|
|Nómadas Preempted|Sim|Nómadas Preempted|Contagem|Média|Número de nós pré-emitidos|Cenário, ClusterName|
|Percentagem de Utilização de Cots|Sim|Percentagem de Utilização de Cots|Contagem|Média|Por cento das quotas utilizadas|Cenário, ClusterName, VmFamilyName, VmPriority|
|Núcleos Totais|Sim|Núcleos Totais|Contagem|Média|Número de núcleos totais|Cenário, ClusterName|
|Nómada Total|Sim|Nómada Total|Contagem|Média|Número de nós totais|Cenário, ClusterName|
|Núcleos inutilizáveis|Sim|Núcleos inutilizáveis|Contagem|Média|Número de núcleos inutilizáveis|Cenário, ClusterName|
|Nódes Inutilizáveis|Sim|Nódes Inutilizáveis|Contagem|Média|Número de nós inutilizáveis|Cenário, ClusterName|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|Sim|Contagem processada de transmissão|Contagem|Média|O número de transações processadas|Nó, canal, tipo, estado|
|LigaçãoAccepted|Sim|Conexões Aceites|Contagem|Total|Conexões Aceites|Nó|
|ConexãoActiva|Sim|Ligações Ativas|Contagem|Média|Ligações Ativas|Nó|
|Conexão Tratada|Sim|Conexões manuseidas|Contagem|Total|Conexões manuseidas|Nó|
|ConsensoEtcdraftCommittedBlockNumber|Sim|Número de bloco comprometido de Etcdraft de consenso|Contagem|Média|O número do bloco do último bloco comprometido|Nó, canal|
|CpuUsagePercentageInDouble|Sim|Percentagem de Utilização cpu|Percentagem|Máximo|Percentagem de Utilização cpu|Nó|
|EndorserEndorsementFailures|Sim|Falhas de endosso do apoio do apoiante|Contagem|Média|O número de apoios falhados.|Nó, canal, código de corrente, codificador de correntes|
|Líder eleitoral da GossipLeader|Sim|Líder eleitoral do Líder da Gossip|Contagem|Total|Peer é líder (1) ou seguidor (0)|Nó, canal|
|GossipMembershipTotalPeersKnown|Sim|Membro gossip Total Pares Conhecidos|Contagem|Média|Total de pares conhecidos|Nó, canal|
|GossipStateHeight|Sim|Altura do Estado dos Mexericos|Contagem|Média|Altura do livro atual|Nó, canal|
|IOReadBytes|Sim|IO Ler Bytes|Bytes|Total|IO Ler Bytes|Nó|
|IOWriteBytes|Sim|IO Escrever Bytes|Bytes|Total|IO Escrever Bytes|Nó|
|Contagem de Transações de Livros|Sim|Contagem de transações de livros|Contagem|Média|Número de transações processadas|Nó, canal, transaction_type, código de corrente, validation_code|
|MemóriaLimite|Sim|Limite de memória|Bytes|Média|Limite de memória|Nó|
|MemóriaSage|Sim|Utilização de Memória|Bytes|Média|Utilização de Memória|Nó|
|MemoryUsagePercentageInDouble|Sim|Percentagem de Utilização de Memória|Percentagem|Média|Percentagem de Utilização de Memória|Nó|
|Processos pendentes|Sim|Transações Pendentes|Contagem|Média|Transações Pendentes|Nó|
|Blocos Processados|Sim|Blocos processados|Contagem|Total|Blocos processados|Nó|
|Transações processadas|Sim|Transações Processadas|Contagem|Total|Transações Processadas|Nó|
|QueuedTransacções|Sim|Transações em fila|Contagem|Média|Transações em fila|Nó|
|Pedido Tratado|Sim|Pedidos Tratados|Contagem|Total|Pedidos Tratados|Nó|
|ArmazenamentoUsage|Sim|Utilização de armazenamento|Bytes|Média|Utilização de armazenamento|Nó|


## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|cachehits|Sim|Acertos na Cache|Contagem|Total||ShardId|
|cachehits0|Sim|Cache Hits (Fragmento 0)|Contagem|Total||Sem Dimensões|
|cachehits1|Sim|Cache Hits (Fragmento 1)|Contagem|Total||Sem Dimensões|
|cachehits2|Sim|Cache Hits (Fragmento 2)|Contagem|Total||Sem Dimensões|
|cachehits3|Sim|Cache Hits (Fragmento 3)|Contagem|Total||Sem Dimensões|
|cachehits4|Sim|Cache Hits (Fragmento 4)|Contagem|Total||Sem Dimensões|
|cachehits5|Sim|Cache Hits (Fragmento 5)|Contagem|Total||Sem Dimensões|
|cachehits6|Sim|Cache Hits (Fragmento 6)|Contagem|Total||Sem Dimensões|
|cachehits7|Sim|Cache Hits (Fragmento 7)|Contagem|Total||Sem Dimensões|
|cachehits8|Sim|Cache Hits (Fragmento 8)|Contagem|Total||Sem Dimensões|
|cachehits9|Sim|Cache Hits (Fragmento 9)|Contagem|Total||Sem Dimensões|
|cacheLatency|Sim|Microsegundos de latência de cache (pré-visualização)|Contagem|Média||ShardId|
|cachemisses|Sim|Falhas de Acerto na Cache|Contagem|Total||ShardId|
|cachemisses0|Sim|Cache Misses (Fragmento 0)|Contagem|Total||Sem Dimensões|
|cachemisses1|Sim|Cache Misses (Fragmento 1)|Contagem|Total||Sem Dimensões|
|cachemisses2|Sim|Cache Misses (Fragmento 2)|Contagem|Total||Sem Dimensões|
|cachemisses3|Sim|Cache Misses (Fragmento 3)|Contagem|Total||Sem Dimensões|
|cachemisses4|Sim|Cache Misses (Fragmento 4)|Contagem|Total||Sem Dimensões|
|cachemisses5|Sim|Cache Misses (Fragmento 5)|Contagem|Total||Sem Dimensões|
|cachemisses6|Sim|Cache Misses (Fragmento 6)|Contagem|Total||Sem Dimensões|
|cachemisses7|Sim|Cache Misses (Fragmento 7)|Contagem|Total||Sem Dimensões|
|cachemisses8|Sim|Cache Misses (Fragmento 8)|Contagem|Total||Sem Dimensões|
|cachemisses9|Sim|Cache Misses (Fragmento 9)|Contagem|Total||Sem Dimensões|
|cachemissrate|Sim|Taxa de Falha cache|Percentagem|cachemissrate||ShardId|
|cacheRead|Sim|Leitura da Cache|BytesPerSecond|Máximo||ShardId|
|cacheRead0|Sim|Cache Read (Fragmento 0)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheRead1|Sim|Cache Read (Fragmento 1)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheRead2|Sim|Cache Read (Fragmento 2)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheRead3|Sim|Cache Read (Fragmento 3)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheRead4|Sim|Cache Read (Fragmento 4)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheRead5|Sim|Cache Read (Fragmento 5)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheRead6|Sim|Cache Read (Fragmento 6)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheRead7|Sim|Cache Read (Fragmento 7)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheRead8|Sim|Cache Read (Fragmento 8)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheRead9|Sim|Cache Read (Fragmento 9)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheDite|Sim|Escrita na Cache|BytesPerSecond|Máximo||ShardId|
|cacheWrite0|Sim|Cache Write (Fragmento 0)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheWrite1|Sim|Cache Write (Fragmento 1)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheWrite2|Sim|Cache Write (Fragmento 2)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheWrite3|Sim|Cache Write (Fragmento 3)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheWrite4|Sim|Cache Write (Fragmento 4)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheWrite5|Sim|Cache Write (Fragmento 5)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheWrite6|Sim|Cache Write (Fragmento 6)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheWrite7|Sim|Cache Write (Fragmento 7)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheWrite8|Sim|Cache Write (Fragmento 8)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheWrite9|Sim|Cache Write (Fragmento 9)|BytesPerSecond|Máximo||Sem Dimensões|
|connectedclients|Sim|Clientes Ligados|Contagem|Máximo||ShardId|
|connectedclients0|Sim|Clientes Conectados (Fragmento 0)|Contagem|Máximo||Sem Dimensões|
|connectedclients1|Sim|Clientes Conectados (Fragmento 1)|Contagem|Máximo||Sem Dimensões|
|connectedclients2|Sim|Clientes Conectados (Fragmento 2)|Contagem|Máximo||Sem Dimensões|
|connectedclients3|Sim|Clientes Conectados (Fragmento 3)|Contagem|Máximo||Sem Dimensões|
|connectedclients4|Sim|Clientes Conectados (Fragmento 4)|Contagem|Máximo||Sem Dimensões|
|connectedclients5|Sim|Clientes Conectados (Fragmento 5)|Contagem|Máximo||Sem Dimensões|
|connectedclients6|Sim|Clientes Conectados (Fragmento 6)|Contagem|Máximo||Sem Dimensões|
|connectedclients7|Sim|Clientes Conectados (Fragmento 7)|Contagem|Máximo||Sem Dimensões|
|connectedclients8|Sim|Clientes Conectados (Fragmento 8)|Contagem|Máximo||Sem Dimensões|
|connectedclients9|Sim|Clientes Conectados (Fragmento 9)|Contagem|Máximo||Sem Dimensões|
|erros|Sim|Erros|Contagem|Máximo||ShardId, ErrorType|
|chaves despejadas|Sim|Chaves Excluídas|Contagem|Total||ShardId|
|despejadokeys0|Sim|Chaves despejadas (Fragmento 0)|Contagem|Total||Sem Dimensões|
|chaves despejadas1|Sim|Chaves despejadas (Fragmento 1)|Contagem|Total||Sem Dimensões|
|despejadokeys2|Sim|Chaves despejadas (Fragmento 2)|Contagem|Total||Sem Dimensões|
|despejadokeys3|Sim|Chaves despejadas (Fragmento 3)|Contagem|Total||Sem Dimensões|
|despejadokeys4|Sim|Chaves despejadas (Fragmento 4)|Contagem|Total||Sem Dimensões|
|despejadokeys5|Sim|Chaves despejadas (Fragmento 5)|Contagem|Total||Sem Dimensões|
|despejadokeys6|Sim|Chaves despejadas (Fragmento 6)|Contagem|Total||Sem Dimensões|
|despejadokeys7|Sim|Chaves despejadas (Fragmento 7)|Contagem|Total||Sem Dimensões|
|despejadokeys8|Sim|Chaves despejadas (Fragmento 8)|Contagem|Total||Sem Dimensões|
|despejadokeys9|Sim|Chaves despejadas (Fragmento 9)|Contagem|Total||Sem Dimensões|
|chaves caducadas|Sim|Chaves Expiradas|Contagem|Total||ShardId|
|40keys0|Sim|Chaves expiradas (Fragmento 0)|Contagem|Total||Sem Dimensões|
|400keys validade1|Sim|Chaves expiradas (Fragmento 1)|Contagem|Total||Sem Dimensões|
|400keys expirados2|Sim|Chaves expiradas (Fragmento 2)|Contagem|Total||Sem Dimensões|
|400keys3|Sim|Chaves expiradas (Fragmento 3)|Contagem|Total||Sem Dimensões|
|4keys expirados|Sim|Chaves expiradas (Fragmento 4)|Contagem|Total||Sem Dimensões|
|400keys5|Sim|Chaves expiradas (Fragmento 5)|Contagem|Total||Sem Dimensões|
|400keys6|Sim|Chaves expiradas (Fragmento 6)|Contagem|Total||Sem Dimensões|
|400keys validade|Sim|Chaves expiradas (Fragmento 7)|Contagem|Total||Sem Dimensões|
|400keys8|Sim|Chaves expiradas (Fragmento 8)|Contagem|Total||Sem Dimensões|
|expirarkeys9|Sim|Chaves expiradas (Fragmento 9)|Contagem|Total||Sem Dimensões|
|obter commões|Sim|Obtenções|Contagem|Total||ShardId|
|obter commands0|Sim|Recebe (Fragmento 0)|Contagem|Total||Sem Dimensões|
|obter commands1|Sim|Recebe (Fragmento 1)|Contagem|Total||Sem Dimensões|
|obter commands2|Sim|Recebe (Fragmento 2)|Contagem|Total||Sem Dimensões|
|obter commands3|Sim|Recebe (Fragmento 3)|Contagem|Total||Sem Dimensões|
|obter commands4|Sim|Recebe (Fragmento 4)|Contagem|Total||Sem Dimensões|
|obter commands5|Sim|Recebe (Fragmento 5)|Contagem|Total||Sem Dimensões|
|obter commands6|Sim|Recebe (Fragmento 6)|Contagem|Total||Sem Dimensões|
|obter commands7|Sim|Recebe (Fragmento 7)|Contagem|Total||Sem Dimensões|
|obter commands8|Sim|Recebe (Fragmento 8)|Contagem|Total||Sem Dimensões|
|obter commands9|Sim|Recebe (Fragmento 9)|Contagem|Total||Sem Dimensões|
|operaçõesPerSecond|Sim|Operações por Segundo|Contagem|Máximo||ShardId|
|operaçõesPerSecond0|Sim|Operações por segundo (Fragmento 0)|Contagem|Máximo||Sem Dimensões|
|operaçõesPerSecond1|Sim|Operações por segundo (Fragmento 1)|Contagem|Máximo||Sem Dimensões|
|operaçõesPerSecond2|Sim|Operações por segundo (Fragmento 2)|Contagem|Máximo||Sem Dimensões|
|operaçõesPerSecond3|Sim|Operações por segundo (Fragmento 3)|Contagem|Máximo||Sem Dimensões|
|operaçõesPerSecond4|Sim|Operações por segundo (Fragmento 4)|Contagem|Máximo||Sem Dimensões|
|operaçõesPerSecond5|Sim|Operações por segundo (Fragmento 5)|Contagem|Máximo||Sem Dimensões|
|operaçõesPerSecond6|Sim|Operações por segundo (Fragmento 6)|Contagem|Máximo||Sem Dimensões|
|operaçõesPerSecond7|Sim|Operações por segundo (Fragmento 7)|Contagem|Máximo||Sem Dimensões|
|operaçõesPerSecond8|Sim|Operações por segundo (Fragmento 8)|Contagem|Máximo||Sem Dimensões|
|operaçõesPerSecond9|Sim|Operações por segundo (Fragmento 9)|Contagem|Máximo||Sem Dimensões|
|percentProcessorTime|Sim|CPU|Percentagem|Máximo||ShardId|
|percentProcessorTime0|Sim|CPU (Fragmento 0)|Percentagem|Máximo||Sem Dimensões|
|percentProcessorTime1|Sim|CPU (Fragmento 1)|Percentagem|Máximo||Sem Dimensões|
|percentProcessorTime2|Sim|CPU (Fragmento 2)|Percentagem|Máximo||Sem Dimensões|
|percentProcessorTime3|Sim|CPU (Fragmento 3)|Percentagem|Máximo||Sem Dimensões|
|percentProcessorTime4|Sim|CPU (Fragmento 4)|Percentagem|Máximo||Sem Dimensões|
|percentProcessorTime5|Sim|CPU (Fragmento 5)|Percentagem|Máximo||Sem Dimensões|
|percentProcessorTime6|Sim|CPU (Fragmento 6)|Percentagem|Máximo||Sem Dimensões|
|percentProcessorTime7|Sim|CPU (Fragmento 7)|Percentagem|Máximo||Sem Dimensões|
|percentProcessorTime8|Sim|CPU (Fragmento 8)|Percentagem|Máximo||Sem Dimensões|
|percentProcessorTime9|Sim|CPU (Fragmento 9)|Percentagem|Máximo||Sem Dimensões|
|servidorAcar|Sim|Carga do Servidor|Percentagem|Máximo||ShardId|
|servidorLoad0|Sim|Carga do servidor (Fragmento 0)|Percentagem|Máximo||Sem Dimensões|
|servidorLoad1|Sim|Carga do servidor (Fragmento 1)|Percentagem|Máximo||Sem Dimensões|
|servidorLoad2|Sim|Carga do servidor (Fragmento 2)|Percentagem|Máximo||Sem Dimensões|
|servidorLoad3|Sim|Carga do servidor (Fragmento 3)|Percentagem|Máximo||Sem Dimensões|
|servidorLoad4|Sim|Carga do servidor (Fragmento 4)|Percentagem|Máximo||Sem Dimensões|
|servidorLoad5|Sim|Carga do servidor (Fragmento 5)|Percentagem|Máximo||Sem Dimensões|
|servidorLoad6|Sim|Carga do servidor (Fragmento 6)|Percentagem|Máximo||Sem Dimensões|
|servidorLoad7|Sim|Carga do servidor (Fragmento 7)|Percentagem|Máximo||Sem Dimensões|
|servidorLoad8|Sim|Carga do servidor (Fragmento 8)|Percentagem|Máximo||Sem Dimensões|
|servidorLoad9|Sim|Carga do servidor (Fragmento 9)|Percentagem|Máximo||Sem Dimensões|
|setcommands|Sim|Definições|Contagem|Total||ShardId|
|setcommands0|Sim|Conjuntos (Fragmento 0)|Contagem|Total||Sem Dimensões|
|setcommands1|Sim|Conjuntos (Fragmento 1)|Contagem|Total||Sem Dimensões|
|setcommands2|Sim|Conjuntos (Fragmento 2)|Contagem|Total||Sem Dimensões|
|setcommands3|Sim|Conjuntos (Fragmento 3)|Contagem|Total||Sem Dimensões|
|setcommands4|Sim|Conjuntos (Fragmento 4)|Contagem|Total||Sem Dimensões|
|setcommands5|Sim|Conjuntos (Fragmento 5)|Contagem|Total||Sem Dimensões|
|setcommands6|Sim|Conjuntos (Fragmento 6)|Contagem|Total||Sem Dimensões|
|setcommands7|Sim|Conjuntos (Fragmento 7)|Contagem|Total||Sem Dimensões|
|setcommands8|Sim|Conjuntos (Fragmento 8)|Contagem|Total||Sem Dimensões|
|setcommands9|Sim|Conjuntos (Fragmento 9)|Contagem|Total||Sem Dimensões|
|totalcommands processado|Sim|Total de Operações|Contagem|Total||ShardId|
|totalcommands processado0|Sim|Total de operações (Fragmento 0)|Contagem|Total||Sem Dimensões|
|totalcommandsprocessado1|Sim|Total de operações (Fragmento 1)|Contagem|Total||Sem Dimensões|
|totalcommands processado2|Sim|Total de Operações (Fragmento 2)|Contagem|Total||Sem Dimensões|
|totalcommands processado3|Sim|Total de operações (Fragmento 3)|Contagem|Total||Sem Dimensões|
|totalcommandsprocessado4|Sim|Total de operações (Fragmento 4)|Contagem|Total||Sem Dimensões|
|totalcommands processado5|Sim|Total de operações (Fragmento 5)|Contagem|Total||Sem Dimensões|
|totalcommands processado6|Sim|Total de operações (Fragmento 6)|Contagem|Total||Sem Dimensões|
|totalcommands processado7|Sim|Total de operações (Fragmento 7)|Contagem|Total||Sem Dimensões|
|totalcommandsprocessado8|Sim|Total de operações (Fragmento 8)|Contagem|Total||Sem Dimensões|
|totalcommandsprocessado9|Sim|Total de operações (Fragmento 9)|Contagem|Total||Sem Dimensões|
|totalkeys|Sim|Chaves totais|Contagem|Máximo||ShardId|
|totalkeys0|Sim|Chaves totais (Fragmento 0)|Contagem|Máximo||Sem Dimensões|
|totalkeys1|Sim|Chaves totais (Fragmento 1)|Contagem|Máximo||Sem Dimensões|
|totalkeys2|Sim|Chaves totais (Fragmento 2)|Contagem|Máximo||Sem Dimensões|
|totalkeys3|Sim|Chaves totais (Fragmento 3)|Contagem|Máximo||Sem Dimensões|
|totalkeys4|Sim|Chaves totais (Fragmento 4)|Contagem|Máximo||Sem Dimensões|
|totalkeys5|Sim|Chaves totais (Fragmento 5)|Contagem|Máximo||Sem Dimensões|
|totalkeys6|Sim|Chaves totais (Fragmento 6)|Contagem|Máximo||Sem Dimensões|
|totalkeys7|Sim|Chaves totais (Fragmento 7)|Contagem|Máximo||Sem Dimensões|
|totalkeys8|Sim|Chaves totais (Fragmento 8)|Contagem|Máximo||Sem Dimensões|
|totalkeys9|Sim|Chaves totais (Fragmento 9)|Contagem|Máximo||Sem Dimensões|
|usedmemory|Sim|Memória Utilizada|Bytes|Máximo||ShardId|
|usadomemory0|Sim|Memória Usada (Fragmento 0)|Bytes|Máximo||Sem Dimensões|
|usedmemory1|Sim|Memória Usada (Fragmento 1)|Bytes|Máximo||Sem Dimensões|
|usedmemory2|Sim|Memória Usada (Fragmento 2)|Bytes|Máximo||Sem Dimensões|
|usadomemory3|Sim|Memória Usada (Fragmento 3)|Bytes|Máximo||Sem Dimensões|
|usadomemory4|Sim|Memória Usada (Fragmento 4)|Bytes|Máximo||Sem Dimensões|
|usadomemory5|Sim|Memória Usada (Fragmento 5)|Bytes|Máximo||Sem Dimensões|
|usadomemory6|Sim|Memória Usada (Fragmento 6)|Bytes|Máximo||Sem Dimensões|
|usadomemory7|Sim|Memória Usada (Fragmento 7)|Bytes|Máximo||Sem Dimensões|
|usadomemory8|Sim|Memória Usada (Fragmento 8)|Bytes|Máximo||Sem Dimensões|
|usedmemory9|Sim|Memória Usada (Fragmento 9)|Bytes|Máximo||Sem Dimensões|
|usuário|Sim|Percentagem de Memória Utilizada|Percentagem|Máximo||ShardId|
|usedmemoryRss|Sim|RSS de memória usada|Bytes|Máximo||ShardId|
|usedmemoryRss0|Sim|RSS de memória usado (fragmento 0)|Bytes|Máximo||Sem Dimensões|
|usedmemoryRss1|Sim|RSS de memória usado (fragmento 1)|Bytes|Máximo||Sem Dimensões|
|usedmemoryRss2|Sim|RSS de memória usado (fragmento 2)|Bytes|Máximo||Sem Dimensões|
|usedmemoryRss3|Sim|RSS de memória usado (fragmento 3)|Bytes|Máximo||Sem Dimensões|
|usedmemoryRss4|Sim|RSS de memória usado (fragmento 4)|Bytes|Máximo||Sem Dimensões|
|usedmemoryRss5|Sim|RSS de memória usado (fragmento 5)|Bytes|Máximo||Sem Dimensões|
|usedmemoryRss6|Sim|RSS de memória usado (fragmento 6)|Bytes|Máximo||Sem Dimensões|
|usedmemoryRss7|Sim|RSS de memória usado (fragmento 7)|Bytes|Máximo||Sem Dimensões|
|usedmemoryRss8|Sim|RSS de memória usado (fragmento 8)|Bytes|Máximo||Sem Dimensões|
|usedmemoryRss9|Sim|RSS de memória usado (fragmento 9)|Bytes|Máximo||Sem Dimensões|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Sim|Contagem de pedidos de firewall de aplicação web|Contagem|Total|O número de pedidos de cliente processados pela Firewall de Aplicação Web|Nome de Política, Nome de Regras, Ação|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Discos de leitura bytes/sec|Não|Leitura de Disco|BytesPerSecond|Média|Bytes médios lidos a partir do disco durante o período de monitorização.|RoleInstanceId|
|Operações de leitura de disco/sec|Sim|Operações de leitura de disco/sec|CondePerSecond|Média|O disco lê iops.|RoleInstanceId|
|Discos De Escrita Bytes/Sec|Não|Escrita em Disco|BytesPerSecond|Média|Bytes médios escritos para o disco durante o período de monitorização.|RoleInstanceId|
|Operações de escrita de discos/seg|Sim|Operações de escrita de discos/seg|CondePerSecond|Média|Disco escreve IOPS.|RoleInstanceId|
|Entrada na Rede|Sim|Entrada na Rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela(s) Máquina Virtual(s) (Tráfego de Entrada).|RoleInstanceId|
|Saída da Rede|Sim|Saída da Rede|Bytes|Total|O número de bytes em todas as interfaces de rede pela(s) Máquina Virtual(s) (Tráfego de Saída).|RoleInstanceId|
|Percentagem da CPU|Sim|Percentagem da CPU|Percentagem|Média|A percentagem de unidades de computação atribuídas que estão atualmente a ser utilizadas pela Máquina Virtual( s).|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Discos de leitura bytes/sec|Não|Leitura de Disco|BytesPerSecond|Média|Bytes médios lidos a partir do disco durante o período de monitorização.|Sem Dimensões|
|Operações de leitura de disco/sec|Sim|Operações de leitura de disco/sec|CondePerSecond|Média|O disco lê iops.|Sem Dimensões|
|Discos De Escrita Bytes/Sec|Não|Escrita em Disco|BytesPerSecond|Média|Bytes médios escritos para o disco durante o período de monitorização.|Sem Dimensões|
|Operações de escrita de discos/seg|Sim|Operações de escrita de discos/seg|CondePerSecond|Média|Disco escreve IOPS.|Sem Dimensões|
|Entrada na Rede|Sim|Entrada na Rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela(s) Máquina Virtual(s) (Tráfego de Entrada).|Sem Dimensões|
|Saída da Rede|Sim|Saída da Rede|Bytes|Total|O número de bytes em todas as interfaces de rede pela(s) Máquina Virtual(s) (Tráfego de Saída).|Sem Dimensões|
|Percentagem da CPU|Sim|Percentagem da CPU|Percentagem|Média|A percentagem de unidades de computação atribuídas que estão atualmente a ser utilizadas pela Máquina Virtual( s).|Sem Dimensões|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAcontas

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Autenticação|
|Saída|Sim|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Autenticação|
|Entrada|Sim|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Autenticação|
|SucessoE2ELatency|Sim|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponta a ponta de pedidos bem sucedidos feitas a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Autenticação|
|SucessoServerLatency|Sim|Latência do servidor de sucesso|Milissegundos|Média|A latência usada pela Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Autenticação|
|Transações|Sim|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType, GeoType, ApiName, Autenticação|
|Capacidade Usada|Não|Capacidade utilizada|Bytes|Média|Capacidade da conta utilizada|Sem Dimensões|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Autenticação|
|Capacidade blob|Não|Capacidade blob|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Blob da conta de armazenamento em bytes.|BlobType, Tier|
|BlobCount|Não|Contagem de blobs|Contagem|Média|O número de Blob no serviço Blob da conta de armazenamento.|BlobType, Tier|
|Contagem de contentores|Sim|Contagem de contentores blob|Contagem|Média|O número de contentores no serviço Blob da conta de armazenamento.|Sem Dimensões|
|Saída|Sim|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Autenticação|
|Capacidade de Indexação|Não|Capacidade de Índice|Bytes|Média|A quantidade de armazenamento utilizada pelo Índice ADLS Gen2 (Hierárquica) em bytes.|Sem Dimensões|
|Entrada|Sim|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Autenticação|
|SucessoE2ELatency|Sim|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponta a ponta de pedidos bem sucedidos feitas a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Autenticação|
|SucessoServerLatency|Sim|Latência do servidor de sucesso|Milissegundos|Média|A latência usada pela Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Autenticação|
|Transações|Sim|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType, GeoType, ApiName, Autenticação|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAcons/fileServices

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Autenticação, FileShare|
|Saída|Sim|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Autenticação, FileShare|
|Capacidade de Ficheiros|Não|Capacidade de Arquivo|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço de ficheiros da conta de armazenamento em bytes.|FileShare|
|Contagem de ficheiros|Não|Contagem de ficheiros|Contagem|Média|O número de ficheiros no serviço de ficheiros da conta de armazenamento.|FileShare|
|FileShareCount|Não|Contagem de partilha de ficheiros|Contagem|Média|O número de ações de ficheiros no serviço de ficheiros da conta de armazenamento.|Sem Dimensões|
|FileShareQuota|Não|Tamanho da quota de ações de arquivo|Bytes|Média|O limite superior da quantidade de armazenamento que pode ser usado pelo Azure Files Service em bytes.|FileShare|
|FileShareSnapshotCount|Não|Contagem de snapshot de partilha de ficheiros|Contagem|Média|O número de instantâneos presentes na parte do Serviço de Ficheiros da conta de armazenamento.|FileShare|
|FileShareSnapshotSize|Não|Tamanho do snapshot de partilha de arquivo|Bytes|Média|A quantidade de armazenamento utilizada pelas fotos no serviço de ficheiros da conta de armazenamento em bytes.|FileShare|
|Entrada|Sim|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Autenticação, FileShare|
|SucessoE2ELatency|Sim|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponta a ponta de pedidos bem sucedidos feitas a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Autenticação, FileShare|
|SucessoServerLatency|Sim|Latência do servidor de sucesso|Milissegundos|Média|A latência usada pela Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Autenticação, FileShare|
|Transações|Sim|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType, GeoType, ApiName, Autenticação, FileShare|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Autenticação|
|Saída|Sim|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Autenticação|
|Entrada|Sim|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Autenticação|
|Capacidade de Fila|Sim|Capacidade de fila|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço de fila da conta de armazenamento em bytes.|Sem Dimensões|
|Contagem de filas|Sim|Contagem de filas|Contagem|Média|O número de filas no serviço de fila da conta de armazenamento.|Sem Dimensões|
|QueueMessageCount|Sim|Contagem de mensagens de fila|Contagem|Média|O número aproximado de mensagens de fila no serviço de fila da conta de armazenamento.|Sem Dimensões|
|SucessoE2ELatency|Sim|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponta a ponta de pedidos bem sucedidos feitas a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Autenticação|
|SucessoServerLatency|Sim|Latência do servidor de sucesso|Milissegundos|Média|A latência usada pela Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Autenticação|
|Transações|Sim|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType, GeoType, ApiName, Autenticação|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Autenticação|
|Saída|Sim|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Autenticação|
|Entrada|Sim|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Autenticação|
|SucessoE2ELatency|Sim|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponta a ponta de pedidos bem sucedidos feitas a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Autenticação|
|SucessoServerLatency|Sim|Latência do servidor de sucesso|Milissegundos|Média|A latência usada pela Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Autenticação|
|Capacidade de Mesa|Sim|Capacidade de mesa|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço de mesa da conta de armazenamento em bytes.|Sem Dimensões|
|MesaCount|Sim|Contagem de tabelas|Contagem|Média|O número de mesa no serviço de mesa da conta de armazenamento.|Sem Dimensões|
|TabelaseconagemCount|Sim|Contagem de Entidades de Tabela|Contagem|Média|O número de entidades de mesa no serviço mesa da conta de armazenamento.|Sem Dimensões|
|Transações|Sim|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType, GeoType, ApiName, Autenticação|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/contas

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|BlockedCalls|Sim|Chamadas bloqueadas|Contagem|Total|Número de chamadas que excederam a taxa ou o limite de quota.|ApiName, OperaçãoName, Região|
|Personagens Treinados|Sim|Personagens treinados|Contagem|Total|Número total de caracteres treinados.|ApiName, OperaçãoName, Região|
|CaracteresTranslatados|Sim|Caracteres Traduzidos|Contagem|Total|Número total de caracteres no pedido de texto de entrada.|ApiName, OperaçãoName, Região|
|ClientErrors|Sim|Erros do Cliente|Contagem|Total|Número de chamadas com erro lateral do cliente (código de resposta HTTP 4xx).|ApiName, OperaçãoName, Região|
|DataIn|Sim|Dados em|Bytes|Total|Tamanho dos dados de entrada em bytes.|ApiName, OperaçãoName, Região|
|DataOut|Sim|Data out|Bytes|Total|Tamanho dos dados de saída em bytes.|ApiName, OperaçãoName, Região|
|Latência|Sim|Latência|MilliSeconds|Média|Latência em milissegundos.|ApiName, OperaçãoName, Região|
|Imagens Processadas|Sim|Imagens processadas|Contagem|Total| Número de Transações para processamento de imagem.|ApiName, FeatureName, UsageChannel, Região|
|ServerErrors|Sim|Erros do servidor|Contagem|Total|Número de chamadas com erro interno de serviço (código de resposta HTTP 5xx).|ApiName, OperaçãoName, Região|
|DiscursoSSessionDuration|Sim|Duração da sessão de discurso|Segundos|Total|Duração total da sessão de discurso em segundos.|ApiName, OperaçãoName, Região|
|Chamadas de sucesso|Sim|Chamadas bem sucedidas|Contagem|Total|Número de chamadas bem sucedidas.|ApiName, OperaçãoName, Região|
|Total de Chamadas|Sim|Total de chamadas|Contagem|Total|Número total de chamadas.|ApiName, OperaçãoName, Região|
|TotalErrors|Sim|Total de Erros|Contagem|Total|Número total de chamadas com resposta a erros (código de resposta HTTP 4xx ou 5xx).|ApiName, OperaçãoName, Região|
|TotalTokenCalls|Sim|Total de chamadas simbólicas|Contagem|Total|Número total de chamadas simbólicas.|ApiName, OperaçãoName, Região|
|Total de transações|Sim|Total de Transações|Contagem|Total|Número total de transações.|Sem Dimensões|


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Créditos CPU Consumidos|Sim|Créditos CPU Consumidos|Contagem|Média|Número total de créditos consumidos pela Máquina Virtual|Sem Dimensões|
|Créditos CPU Remanescentes|Sim|Créditos CPU Remanescentes|Contagem|Média|Número total de créditos disponíveis para rebentar|Sem Dimensões|
|Profundidade da Fila do Disco de Dados|Sim|Profundidade da fila do disco de dados (pré-visualização)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|RIO LUN|
|Data Disk Ler Bytes/seg|Sim|Data Disk Read Bytes/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|RIO LUN|
|Operações de leitura de discos de dados/seg|Sim|Operações de leitura de discos de dados/seg (pré-visualização)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização|RIO LUN|
|Bytes/seg de escrita de disco de dados|Sim|Data Disk Write Bytes/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização|RIO LUN|
|Operações de escrita de discos de dados/seg|Sim|Operações de escrita de discos de dados/seg (pré-visualização)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|RIO LUN|
|Bytes de leitura de disco|Sim|Bytes de leitura de disco|Bytes|Total|Bytes lidos a partir de disco durante o período de monitorização|Sem Dimensões|
|Operações de leitura de disco/sec|Sim|Operações de leitura de disco/sec|CondePerSecond|Média|IOPS de leitura de disco|Sem Dimensões|
|Bytes de escrita de disco|Sim|Bytes de escrita de disco|Bytes|Total|Bytes escritos para o disco durante o período de monitorização|Sem Dimensões|
|Operações de escrita de discos/seg|Sim|Operações de escrita de discos/seg|CondePerSecond|Média|IOPS de escrita de disco|Sem Dimensões|
|Fluxos de Entrada|Sim|Fluxos de Entrada|Contagem|Média|Fluxos de entrada são um número de fluxos correntes no sentido de entrada (tráfego que vai para o VM)|Sem Dimensões|
|Fluxos de entrada Taxa máxima de criação|Sim|Fluxos de entrada Taxa máxima de criação|CondePerSecond|Média|A taxa máxima de criação dos fluxos de entrada (tráfego que vai para o VM)|Sem Dimensões|
|Entrada na Rede|Sim|Rede em Faturação (Depreciada)|Bytes|Total|O número de bytes faturados recebidos em todas as interfaces de rede pela(s) Máquina Virtual(Tráfego De entrada) (Depreifed)|Sem Dimensões|
|Rede no total|Sim|Rede no total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela Máquina Virtual(s) (Tráfego de Entrada)|Sem Dimensões|
|Saída da Rede|Sim|Network out Billable (Precotado)|Bytes|Total|O número de bytes faturados em todas as interfaces de rede pela(s) Máquina Virtual(Tráfego De saída) (Depreifed)|Sem Dimensões|
|Rede Total|Sim|Rede Total|Bytes|Total|O número de bytes em todas as interfaces de rede pela(s) Máquina Virtual(s) (Tráfego de Saída)|Sem Dimensões|
|Profundidade da Fila do Disco do SO|Sim|Profundidade da fila do disco os (pré-visualização)|Contagem|Média|Profundidade da fila do disco os(ou comprimento da fila)|Sem Dimensões|
|Leitura de discos de OS Bytes/seg|Sim|Os Discos de Leitura de Bytes/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização para o disco de SO|Sem Dimensões|
|Operações de leitura de disco de OS/Sec|Sim|Operações de leitura de disco de OS/Seg (Pré-visualização)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização do disco de so|Sem Dimensões|
|Os Discos de Escrita bytes/seg|Sim|Os Discos de Escrita de Discos/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização do disco OS|Sem Dimensões|
|Operações de escrita de discos de OS/Sec|Sim|Operações de escrita de discos de OS/Seg (Pré-visualização)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco de so|Sem Dimensões|
|OS por Disco QD|Sim|CO Disk QD (precotado)|Contagem|Média|Profundidade da fila do disco os(ou comprimento da fila)|Sem Dimensões|
|OS por Disco Ler Bytes/seg|Sim|Os Discos De Leitura Bytes/Sec (Deprecado)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização para o disco de SO|Sem Dimensões|
|Operações de leitura por disco/seg|Sim|Operações de leitura de discos de OS/Sec (Depreciadas)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização do disco de so|Sem Dimensões|
|OS por Disco Escrever Bytes/seg|Sim|Os Discos De Discos/Sec (Deprecado)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização do disco OS|Sem Dimensões|
|Os por Operações de Escrita de Disco/Sec|Sim|Operações de escrita de discos de OS/Sec (Depreciadas)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco de so|Sem Dimensões|
|Fluxos de saída|Sim|Fluxos de saída|Contagem|Média|Fluxos de saída são um número de fluxos correntes no sentido de saída (tráfego que sai do VM)|Sem Dimensões|
|Taxa máxima de criação de fluxos de saída|Sim|Taxa máxima de criação de fluxos de saída|CondePerSecond|Média|A taxa máxima de criação dos fluxos de saída (tráfego que sai do VM)|Sem Dimensões|
|Por Disco QD|Sim|QD do disco de dados (precotado)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|SlotId|
|Per Disk Read Bytes/sec|Sim|Data Disk Read Bytes/Sec (Deprecado)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|SlotId|
|Por Operações de leitura de disco/sec|Sim|Operações de leitura de discos de dados/seg (depreciadas)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização|SlotId|
|Per Disk Write Bytes/sec|Sim|Data Disk Write Bytes/Sec (Deprecado)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização|SlotId|
|Por operações de escrita de disco/sec|Sim|Operações de escrita de discos de dados/seg (depreciadas)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|SlotId|
|Percentagem da CPU|Sim|Percentagem da CPU|Percentagem|Média|A percentagem de unidades de computação atribuídas que estão atualmente a ser utilizadas pela Máquina Virtual(s)|Sem Dimensões|
|Sucesso de leitura de cache de disco de dados premium|Sim|Sucesso de leitura de cache de disco de dados premium (pré-visualização)|Percentagem|Média|Sucesso de leitura de cache de disco de dados premium|RIO LUN|
|Cache de disco de dados premium Ler Miss|Sim|Cache de disco de dados premium Ler Miss (Pré-visualização)|Percentagem|Média|Cache de disco de dados premium Ler Miss|RIO LUN|
|Premium OS Disk Cache Ler Hit|Sim|Premium OS Disk Cache Read Hit (Pré-visualização)|Percentagem|Média|Premium OS Disk Cache Ler Hit|Sem Dimensões|
|Premium OS Cache De Disco Ler Miss|Sim|Cache de disco premium OS Ler Miss (Pré-visualização)|Percentagem|Média|Premium OS Cache De Disco Ler Miss|Sem Dimensões|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Créditos CPU Consumidos|Sim|Créditos CPU Consumidos|Contagem|Média|Número total de créditos consumidos pela Máquina Virtual|Sem Dimensões|
|Créditos CPU Remanescentes|Sim|Créditos CPU Remanescentes|Contagem|Média|Número total de créditos disponíveis para rebentar|Sem Dimensões|
|Profundidade da Fila do Disco de Dados|Sim|Profundidade da fila do disco de dados (pré-visualização)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|LUN, VMName|
|Data Disk Ler Bytes/seg|Sim|Data Disk Read Bytes/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|LUN, VMName|
|Operações de leitura de discos de dados/seg|Sim|Operações de leitura de discos de dados/seg (pré-visualização)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização|LUN, VMName|
|Bytes/seg de escrita de disco de dados|Sim|Data Disk Write Bytes/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização|LUN, VMName|
|Operações de escrita de discos de dados/seg|Sim|Operações de escrita de discos de dados/seg (pré-visualização)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|LUN, VMName|
|Bytes de leitura de disco|Sim|Bytes de leitura de disco|Bytes|Total|Bytes lidos a partir de disco durante o período de monitorização|VMName|
|Operações de leitura de disco/sec|Sim|Operações de leitura de disco/sec|CondePerSecond|Média|IOPS de leitura de disco|VMName|
|Bytes de escrita de disco|Sim|Bytes de escrita de disco|Bytes|Total|Bytes escritos para o disco durante o período de monitorização|VMName|
|Operações de escrita de discos/seg|Sim|Operações de escrita de discos/seg|CondePerSecond|Média|IOPS de escrita de disco|VMName|
|Fluxos de Entrada|Sim|Fluxos de Entrada|Contagem|Média|Fluxos de entrada são um número de fluxos correntes no sentido de entrada (tráfego que vai para o VM)|VMName|
|Fluxos de entrada Taxa máxima de criação|Sim|Fluxos de entrada Taxa máxima de criação|CondePerSecond|Média|A taxa máxima de criação dos fluxos de entrada (tráfego que vai para o VM)|VMName|
|Entrada na Rede|Sim|Rede em Faturação (Depreciada)|Bytes|Total|O número de bytes faturados recebidos em todas as interfaces de rede pela(s) Máquina Virtual(Tráfego De entrada) (Depreifed)|VMName|
|Rede no total|Sim|Rede no total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela Máquina Virtual(s) (Tráfego de Entrada)|VMName|
|Saída da Rede|Sim|Network out Billable (Precotado)|Bytes|Total|O número de bytes faturados em todas as interfaces de rede pela(s) Máquina Virtual(Tráfego De saída) (Depreifed)|VMName|
|Rede Total|Sim|Rede Total|Bytes|Total|O número de bytes em todas as interfaces de rede pela(s) Máquina Virtual(s) (Tráfego de Saída)|VMName|
|Profundidade da Fila do Disco do SO|Sim|Profundidade da fila do disco os (pré-visualização)|Contagem|Média|Profundidade da fila do disco os(ou comprimento da fila)|VMName|
|Leitura de discos de OS Bytes/seg|Sim|Os Discos de Leitura de Bytes/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização para o disco de SO|VMName|
|Operações de leitura de disco de OS/Sec|Sim|Operações de leitura de disco de OS/Seg (Pré-visualização)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização do disco de so|VMName|
|Os Discos de Escrita bytes/seg|Sim|Os Discos de Escrita de Discos/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização do disco OS|VMName|
|Operações de escrita de discos de OS/Sec|Sim|Operações de escrita de discos de OS/Seg (Pré-visualização)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco de so|VMName|
|OS por Disco QD|Sim|CO Disk QD (precotado)|Contagem|Média|Profundidade da fila do disco os(ou comprimento da fila)|Sem Dimensões|
|OS por Disco Ler Bytes/seg|Sim|Os Discos De Leitura Bytes/Sec (Deprecado)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização para o disco de SO|Sem Dimensões|
|Operações de leitura por disco/seg|Sim|Operações de leitura de discos de OS/Sec (Depreciadas)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização do disco de so|Sem Dimensões|
|OS por Disco Escrever Bytes/seg|Sim|Os Discos De Discos/Sec (Deprecado)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização do disco OS|Sem Dimensões|
|Os por Operações de Escrita de Disco/Sec|Sim|Operações de escrita de discos de OS/Sec (Depreciadas)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco de so|Sem Dimensões|
|Fluxos de saída|Sim|Fluxos de saída|Contagem|Média|Fluxos de saída são um número de fluxos correntes no sentido de saída (tráfego que sai do VM)|VMName|
|Taxa máxima de criação de fluxos de saída|Sim|Taxa máxima de criação de fluxos de saída|CondePerSecond|Média|A taxa máxima de criação dos fluxos de saída (tráfego que sai do VM)|VMName|
|Por Disco QD|Sim|QD do disco de dados (precotado)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|SlotId|
|Per Disk Read Bytes/sec|Sim|Data Disk Read Bytes/Sec (Deprecado)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|SlotId|
|Por Operações de leitura de disco/sec|Sim|Operações de leitura de discos de dados/seg (depreciadas)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização|SlotId|
|Per Disk Write Bytes/sec|Sim|Data Disk Write Bytes/Sec (Deprecado)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização|SlotId|
|Por operações de escrita de disco/sec|Sim|Operações de escrita de discos de dados/seg (depreciadas)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|SlotId|
|Percentagem da CPU|Sim|Percentagem da CPU|Percentagem|Média|A percentagem de unidades de computação atribuídas que estão atualmente a ser utilizadas pela Máquina Virtual(s)|VMName|
|Sucesso de leitura de cache de disco de dados premium|Sim|Sucesso de leitura de cache de disco de dados premium (pré-visualização)|Percentagem|Média|Sucesso de leitura de cache de disco de dados premium|LUN, VMName|
|Cache de disco de dados premium Ler Miss|Sim|Cache de disco de dados premium Ler Miss (Pré-visualização)|Percentagem|Média|Cache de disco de dados premium Ler Miss|LUN, VMName|
|Premium OS Disk Cache Ler Hit|Sim|Premium OS Disk Cache Read Hit (Pré-visualização)|Percentagem|Média|Premium OS Disk Cache Ler Hit|VMName|
|Premium OS Cache De Disco Ler Miss|Sim|Cache de disco premium OS Ler Miss (Pré-visualização)|Percentagem|Média|Premium OS Cache De Disco Ler Miss|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Créditos CPU Consumidos|Sim|Créditos CPU Consumidos|Contagem|Média|Número total de créditos consumidos pela Máquina Virtual|Sem Dimensões|
|Créditos CPU Remanescentes|Sim|Créditos CPU Remanescentes|Contagem|Média|Número total de créditos disponíveis para rebentar|Sem Dimensões|
|Profundidade da Fila do Disco de Dados|Sim|Profundidade da fila do disco de dados (pré-visualização)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|RIO LUN|
|Data Disk Ler Bytes/seg|Sim|Data Disk Read Bytes/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|RIO LUN|
|Operações de leitura de discos de dados/seg|Sim|Operações de leitura de discos de dados/seg (pré-visualização)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização|RIO LUN|
|Bytes/seg de escrita de disco de dados|Sim|Data Disk Write Bytes/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização|RIO LUN|
|Operações de escrita de discos de dados/seg|Sim|Operações de escrita de discos de dados/seg (pré-visualização)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|RIO LUN|
|Bytes de leitura de disco|Sim|Bytes de leitura de disco|Bytes|Total|Bytes lidos a partir de disco durante o período de monitorização|Sem Dimensões|
|Operações de leitura de disco/sec|Sim|Operações de leitura de disco/sec|CondePerSecond|Média|IOPS de leitura de disco|Sem Dimensões|
|Bytes de escrita de disco|Sim|Bytes de escrita de disco|Bytes|Total|Bytes escritos para o disco durante o período de monitorização|Sem Dimensões|
|Operações de escrita de discos/seg|Sim|Operações de escrita de discos/seg|CondePerSecond|Média|IOPS de escrita de disco|Sem Dimensões|
|Fluxos de Entrada|Sim|Fluxos de Entrada|Contagem|Média|Fluxos de entrada são um número de fluxos correntes no sentido de entrada (tráfego que vai para o VM)|Sem Dimensões|
|Fluxos de entrada Taxa máxima de criação|Sim|Fluxos de entrada Taxa máxima de criação|CondePerSecond|Média|A taxa máxima de criação dos fluxos de entrada (tráfego que vai para o VM)|Sem Dimensões|
|Entrada na Rede|Sim|Rede em Faturação (Depreciada)|Bytes|Total|O número de bytes faturados recebidos em todas as interfaces de rede pela(s) Máquina Virtual(Tráfego De entrada) (Depreifed)|Sem Dimensões|
|Rede no total|Sim|Rede no total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela Máquina Virtual(s) (Tráfego de Entrada)|Sem Dimensões|
|Saída da Rede|Sim|Network out Billable (Precotado)|Bytes|Total|O número de bytes faturados em todas as interfaces de rede pela(s) Máquina Virtual(Tráfego De saída) (Depreifed)|Sem Dimensões|
|Rede Total|Sim|Rede Total|Bytes|Total|O número de bytes em todas as interfaces de rede pela(s) Máquina Virtual(s) (Tráfego de Saída)|Sem Dimensões|
|Profundidade da Fila do Disco do SO|Sim|Profundidade da fila do disco os (pré-visualização)|Contagem|Média|Profundidade da fila do disco os(ou comprimento da fila)|Sem Dimensões|
|Leitura de discos de OS Bytes/seg|Sim|Os Discos de Leitura de Bytes/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização para o disco de SO|Sem Dimensões|
|Operações de leitura de disco de OS/Sec|Sim|Operações de leitura de disco de OS/Seg (Pré-visualização)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização do disco de so|Sem Dimensões|
|Os Discos de Escrita bytes/seg|Sim|Os Discos de Escrita de Discos/Sec (Pré-visualização)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização do disco OS|Sem Dimensões|
|Operações de escrita de discos de OS/Sec|Sim|Operações de escrita de discos de OS/Seg (Pré-visualização)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco de so|Sem Dimensões|
|OS por Disco QD|Sim|CO Disk QD (precotado)|Contagem|Média|Profundidade da fila do disco os(ou comprimento da fila)|Sem Dimensões|
|OS por Disco Ler Bytes/seg|Sim|Os Discos De Leitura Bytes/Sec (Deprecado)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização para o disco de SO|Sem Dimensões|
|Operações de leitura por disco/seg|Sim|Operações de leitura de discos de OS/Sec (Depreciadas)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização do disco de so|Sem Dimensões|
|OS por Disco Escrever Bytes/seg|Sim|Os Discos De Discos/Sec (Deprecado)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização do disco OS|Sem Dimensões|
|Os por Operações de Escrita de Disco/Sec|Sim|Operações de escrita de discos de OS/Sec (Depreciadas)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco de so|Sem Dimensões|
|Fluxos de saída|Sim|Fluxos de saída|Contagem|Média|Fluxos de saída são um número de fluxos correntes no sentido de saída (tráfego que sai do VM)|Sem Dimensões|
|Taxa máxima de criação de fluxos de saída|Sim|Taxa máxima de criação de fluxos de saída|CondePerSecond|Média|A taxa máxima de criação dos fluxos de saída (tráfego que sai do VM)|Sem Dimensões|
|Por Disco QD|Sim|QD do disco de dados (precotado)|Contagem|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|SlotId|
|Per Disk Read Bytes/sec|Sim|Data Disk Read Bytes/Sec (Deprecado)|CondePerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|SlotId|
|Por Operações de leitura de disco/sec|Sim|Operações de leitura de discos de dados/seg (depreciadas)|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização|SlotId|
|Per Disk Write Bytes/sec|Sim|Data Disk Write Bytes/Sec (Deprecado)|CondePerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização|SlotId|
|Por operações de escrita de disco/sec|Sim|Operações de escrita de discos de dados/seg (depreciadas)|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|SlotId|
|Percentagem da CPU|Sim|Percentagem da CPU|Percentagem|Média|A percentagem de unidades de computação atribuídas que estão atualmente a ser utilizadas pela Máquina Virtual(s)|Sem Dimensões|
|Sucesso de leitura de cache de disco de dados premium|Sim|Sucesso de leitura de cache de disco de dados premium (pré-visualização)|Percentagem|Média|Sucesso de leitura de cache de disco de dados premium|RIO LUN|
|Cache de disco de dados premium Ler Miss|Sim|Cache de disco de dados premium Ler Miss (Pré-visualização)|Percentagem|Média|Cache de disco de dados premium Ler Miss|RIO LUN|
|Premium OS Disk Cache Ler Hit|Sim|Premium OS Disk Cache Read Hit (Pré-visualização)|Percentagem|Média|Premium OS Disk Cache Ler Hit|Sem Dimensões|
|Premium OS Cache De Disco Ler Miss|Sim|Cache de disco premium OS Ler Miss (Pré-visualização)|Percentagem|Média|Premium OS Cache De Disco Ler Miss|Sem Dimensões|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|CpuUsage|Sim|Utilização da CPU|Contagem|Média|Utilização do CPU em todos os núcleos em millicores.|nome de contentor|
|MemóriaSage|Sim|Utilização de Memória|Bytes|Média|Uso total da memória em byte.|nome de contentor|
|NetworkBytesReceivedPerSecond|Sim|Bytes de rede recebidos por segundo|Bytes|Média|Os bytes de rede recebidos por segundo.|Sem Dimensões|
|NetworkBytesTransmittedPerSecond|Sim|Bytes de rede transmitidos por segundo|Bytes|Média|Os bytes de rede transmitidos por segundo.|Sem Dimensões|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|Sim|Hora do CPU do AgentPool|Segundos|Total|Tempo do AgentPool CPU em segundos|Sem Dimensões|
|RunDuration|Sim|Duração do funcional|Milissegundos|Total|Duração da execução em milissegundos|Sem Dimensões|
|SucessoPullCount|Sim|Contagem de pull com sucesso|Contagem|Média|Número de puxas de imagem bem-sucedidas|Sem Dimensões|
|SucessoPushCount|Sim|Contagem de impulsos bem sucedida|Contagem|Média|Número de impulsos de imagem bem sucedidos|Sem Dimensões|
|TotalPullCount|Sim|Contagem total de pull|Contagem|Média|Número de tiras de imagem no total|Sem Dimensões|
|TotalPushCount|Sim|Contagem total de push|Contagem|Média|Número de impulsos de imagem no total|Sem Dimensões|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Não|Número total de núcleos cpu disponíveis num cluster gerido|Contagem|Média|Número total de núcleos cpu disponíveis num cluster gerido|Sem Dimensões|
|kube_node_status_allocatable_memory_bytes|Não|Quantidade total de memória disponível num cluster gerido|Bytes|Média|Quantidade total de memória disponível num cluster gerido|Sem Dimensões|
|kube_node_status_condition|Não|Estados para várias condições de nó|Contagem|Média|Estados para várias condições de nó|condição, estado, estado2, nó|
|kube_pod_status_phase|Não|Número de cápsulas por fase|Contagem|Média|Número de cápsulas por fase|fase, espaço de nome, vagem|
|kube_pod_status_ready|Não|Número de cápsulas no estado de Ready|Contagem|Média|Número de cápsulas no estado de Ready|espaço de nome, pod|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Requessos Falhados|Sim|Pedidos com Falhas|Contagem|Total|Obtém os registos disponíveis para Fornecedores de Recursos Personalizados|HttpMethod, CallPath, StatusCode|
|Requestres com sucesso|Sim|Pedidos Com Êxito|Contagem|Total|Pedidos bem sucedidos feitos pelo fornecedor personalizado|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Capacidade disponível|Sim|Capacidade disponível|Bytes|Média|A capacidade disponível nos bytes durante o período de reporte.|Sem Dimensões|
|BytesUploadedToCloud|Sim|Cloud Bytes Uploaded (Dispositivo)|Bytes|Média|O número total de bytes que é enviado para Azure a partir de um dispositivo durante o período de reporte.|Sem Dimensões|
|BytesUploadedToCloudPerShare|Sim|Cloud Bytes Uploaded (Partilhar)|Bytes|Média|O número total de bytes que é enviado para Azure a partir de uma parte durante o período de reporte.|Partilhar|
|CloudReadThroughput|Sim|Download da Cloud|BytesPerSecond|Média|A produção de download da nuvem para Azure durante o período de reporte.|Sem Dimensões|
|CloudReadThroughputPerShare|Sim|Download cloud 'Produção' (Partilhar)|BytesPerSecond|Média|O download para Azure de uma parte durante o período de reporte.|Partilhar|
|CloudUploadThroughput|Sim|Produção de upload de nuvens|BytesPerSecond|Média|A nuvem envia para Azure durante o período de reporte.|Sem Dimensões|
|CloudUploadThroughputPerShare|Sim|Produção de upload de nuvem (partilhar)|BytesPerSecond|Média|O envio para a Azure de uma parte durante o período de reporte.|Partilhar|
|Hipervmemoryutilização|Sim|Edge Compute - Utilização da Memória|Percentagem|Média|Quantidade de RAM em uso|Nome de exemplo|
|HiperVVirtualProcessorutilização|Sim|Edge Compute - Percentagem CPU|Percentagem|Média|Por cento uso cpu|Nome de exemplo|
|NICReadThroughput|Sim|Ler produção (rede)|BytesPerSecond|Média|A produção de leitura da interface de rede no dispositivo no período de reporte para todos os volumes no gateway.|Nome de exemplo|
|NICWriteThroughput|Sim|Escrever Produção (Rede)|BytesPerSecond|Média|A produção de produção da interface de rede no dispositivo no período de reporte para todos os volumes no gateway.|Nome de exemplo|
|Capacidade Total|Sim|Capacidade Total|Bytes|Média|Capacidade Total|Sem Dimensões|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Corridas Falhadas|Sim|Corridas falhadas|Contagem|Total||pipelineName, atividadeName|
|Sucesso DeRuns|Sim|Corridas de sucesso|Contagem|Total||pipelineName, atividadeName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/fábricas

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AtividadeCancelledRuns|Sim|Atividade cancelada executa métricas|Contagem|Total||ActivityType, PipelineName, FailureType, Name|
|AtividadeFailedRuns|Sim|Atividade falhada executa métricas|Contagem|Total||ActivityType, PipelineName, FailureType, Name|
|AtividadeSSucceedEdRuns|Sim|A atividade bem sucedida executa métricas|Contagem|Total||ActivityType, PipelineName, FailureType, Name|
|FactorySizeInGbUnits|Sim|Tamanho total da fábrica (unidade GB)|Contagem|Máximo||Sem Dimensões|
|IntegrationRuntimeAvailableMemory|Sim|Integração memória disponível|Bytes|Média||IntegraçãoRuntimeName, Nome node|
|IntegrationRuntimeAvailableNodeNumber|Sim|Contagem de nó disponível de integração|Contagem|Média||IntegraçãoRuntimeName|
|IntegraçãoRuntimeAverageTaskPickupDelay|Sim|Duração da fila de tempo de execução de integração|Segundos|Média||IntegraçãoRuntimeName|
|IntegraçãoRuntimeCpuPercentage|Sim|Utilização do CPU em tempo de integração|Percentagem|Média||IntegraçãoRuntimeName, Nome node|
|IntegraçãoRuntimeQueueLength|Sim|Comprimento da fila de tempo de execução de integração|Contagem|Média||IntegraçãoRuntimeName|
|MaxAllowedFactorySizeInGbUnits|Sim|Tamanho máximo permitido da fábrica (unidade GB)|Contagem|Máximo||Sem Dimensões|
|MaxAllowedResourceCount|Sim|Contagem máxima de entidades permitidas|Contagem|Máximo||Sem Dimensões|
|PipelineCancelledRuns|Sim|Gasoduto cancelado executa métricas|Contagem|Total||Falha, Nome|
|PipelineFailedRuns|Sim|Gasoduto falhado executa métricas|Contagem|Total||Falha, Nome|
|PipelineSucceededRuns|Sim|Pipeline bem sucedido executa métricas|Contagem|Total||Falha, Nome|
|Contagem de Recursos|Sim|Total de entidades contam|Contagem|Máximo||Sem Dimensões|
|TriggerCancelledRuns|Sim|Gatilho cancelado executa métricas|Contagem|Total||Nome, FalhaType|
|TriggerFailedRuns|Sim|O gatilho falhado executa métricas|Contagem|Total||Nome, FalhaType|
|TriggerSucceeddRuns|Sim|O gatilho bem sucedido executa métricas|Contagem|Total||Nome, FalhaType|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/contas

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|DataRead|Sim|Leitura de Dados|Bytes|Total|Quantidade total de dados lidos a partir da conta.|Sem Dimensões|
|DataWritten|Sim|Dados Escritos|Bytes|Total|Quantidade total de dados escritos na conta.|Sem Dimensões|
|ReadRequests|Sim|Ler Pedidos|Contagem|Total|Contagem de dados leia pedidos para a conta.|Sem Dimensões|
|TotalStorage|Sim|Armazenamento Total|Bytes|Máximo|Quantidade total de dados armazenados na conta.|Sem Dimensões|
|WriteRequests|Sim|Escrever Pedidos|Contagem|Total|A contagem de dados escreve pedidos para a conta.|Sem Dimensões|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servidores

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|active_connections|Sim|Ligações Ativas|Contagem|Média|Ligações Ativas|Sem Dimensões|
|backup_storage_used|Sim|Armazenamento de backup utilizado|Bytes|Média|Armazenamento de backup utilizado|Sem Dimensões|
|connections_failed|Sim|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Sem Dimensões|
|cpu_percent|Sim|CPU por cento|Percentagem|Média|CPU por cento|Sem Dimensões|
|io_consumption_percent|Sim|IO por cento|Percentagem|Média|IO por cento|Sem Dimensões|
|memory_percent|Sim|Por cento da memória|Percentagem|Média|Por cento da memória|Sem Dimensões|
|network_bytes_egress|Sim|Saída da Rede|Bytes|Total|Rede Para fora através de ligações ativas|Sem Dimensões|
|network_bytes_ingress|Sim|Entrada na Rede|Bytes|Total|Rede Em através de ligações ativas|Sem Dimensões|
|seconds_behind_master|Sim|Atraso de replicação em segundos|Contagem|Máximo|Atraso de replicação em segundos|Sem Dimensões|
|serverlog_storage_limit|Sim|Limite de armazenamento de registo de servidor|Bytes|Média|Limite de armazenamento de registo de servidor|Sem Dimensões|
|serverlog_storage_percent|Sim|Por cento de armazenamento de registo de servidor|Percentagem|Média|Por cento de armazenamento de registo de servidor|Sem Dimensões|
|serverlog_storage_usage|Sim|Armazenamento de registo de servidor utilizado|Bytes|Média|Armazenamento de registo de servidor utilizado|Sem Dimensões|
|storage_limit|Sim|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Sem Dimensões|
|storage_percent|Sim|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Sem Dimensões|
|storage_used|Sim|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Sem Dimensões|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servidores

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|active_connections|Sim|Ligações Ativas|Contagem|Média|Ligações Ativas|Sem Dimensões|
|backup_storage_used|Sim|Armazenamento de backup utilizado|Bytes|Média|Armazenamento de backup utilizado|Sem Dimensões|
|connections_failed|Sim|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Sem Dimensões|
|cpu_percent|Sim|CPU por cento|Percentagem|Média|CPU por cento|Sem Dimensões|
|io_consumption_percent|Sim|IO por cento|Percentagem|Média|IO por cento|Sem Dimensões|
|memory_percent|Sim|Por cento da memória|Percentagem|Média|Por cento da memória|Sem Dimensões|
|network_bytes_egress|Sim|Saída da Rede|Bytes|Total|Rede Para fora através de ligações ativas|Sem Dimensões|
|network_bytes_ingress|Sim|Entrada na Rede|Bytes|Total|Rede Em através de ligações ativas|Sem Dimensões|
|seconds_behind_master|Sim|Atraso de replicação em segundos|Contagem|Máximo|Atraso de replicação em segundos|Sem Dimensões|
|serverlog_storage_limit|Sim|Limite de armazenamento de registo de servidor|Bytes|Máximo|Limite de armazenamento de registo de servidor|Sem Dimensões|
|serverlog_storage_percent|Sim|Por cento de armazenamento de registo de servidor|Percentagem|Média|Por cento de armazenamento de registo de servidor|Sem Dimensões|
|serverlog_storage_usage|Sim|Armazenamento de registo de servidor utilizado|Bytes|Média|Armazenamento de registo de servidor utilizado|Sem Dimensões|
|storage_limit|Sim|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Sem Dimensões|
|storage_percent|Sim|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Sem Dimensões|
|storage_used|Sim|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Sem Dimensões|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servidores

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|active_connections|Sim|Ligações Ativas|Contagem|Média|Ligações Ativas|Sem Dimensões|
|backup_storage_used|Sim|Armazenamento de backup utilizado|Bytes|Média|Armazenamento de backup utilizado|Sem Dimensões|
|connections_failed|Sim|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Sem Dimensões|
|cpu_percent|Sim|CPU por cento|Percentagem|Média|CPU por cento|Sem Dimensões|
|io_consumption_percent|Sim|IO por cento|Percentagem|Média|IO por cento|Sem Dimensões|
|memory_percent|Sim|Por cento da memória|Percentagem|Média|Por cento da memória|Sem Dimensões|
|network_bytes_egress|Sim|Saída da Rede|Bytes|Total|Rede Para fora através de ligações ativas|Sem Dimensões|
|network_bytes_ingress|Sim|Entrada na Rede|Bytes|Total|Rede Em através de ligações ativas|Sem Dimensões|
|pg_replica_log_delay_in_bytes|Sim|Max Lag através de réplicas|Bytes|Máximo|Lag in bytes da réplica mais atrasada|Sem Dimensões|
|pg_replica_log_delay_in_seconds|Sim|Lag de réplica|Segundos|Máximo|Réplica lag em segundos|Sem Dimensões|
|serverlog_storage_limit|Sim|Limite de armazenamento de registo de servidor|Bytes|Máximo|Limite de armazenamento de registo de servidor|Sem Dimensões|
|serverlog_storage_percent|Sim|Por cento de armazenamento de registo de servidor|Percentagem|Média|Por cento de armazenamento de registo de servidor|Sem Dimensões|
|serverlog_storage_usage|Sim|Armazenamento de registo de servidor utilizado|Bytes|Média|Armazenamento de registo de servidor utilizado|Sem Dimensões|
|storage_limit|Sim|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Sem Dimensões|
|storage_percent|Sim|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Sem Dimensões|
|storage_used|Sim|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Sem Dimensões|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|active_connections|Sim|Ligações Ativas|Contagem|Média|Ligações Ativas|Sem Dimensões|
|cpu_percent|Sim|CPU por cento|Percentagem|Média|CPU por cento|Sem Dimensões|
|iops|Sim|IOPS|Contagem|Média|Operações IO por segundo|Sem Dimensões|
|memory_percent|Sim|Por cento da memória|Percentagem|Média|Por cento da memória|Sem Dimensões|
|network_bytes_egress|Sim|Saída da Rede|Bytes|Total|Rede Para fora através de ligações ativas|Sem Dimensões|
|network_bytes_ingress|Sim|Entrada na Rede|Bytes|Total|Rede Em através de ligações ativas|Sem Dimensões|
|storage_percent|Sim|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Sem Dimensões|
|storage_used|Sim|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Sem Dimensões|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft.DBforPostgreSQL/singleservers

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|active_connections|Sim|Ligações Ativas|Contagem|Média|Ligações Ativas|Sem Dimensões|
|connections_failed|Sim|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Sem Dimensões|
|connections_succeeded|Sim|Conexões bem sucedidas|Contagem|Total|Conexões bem sucedidas|Sem Dimensões|
|cpu_percent|Sim|CPU por cento|Percentagem|Média|CPU por cento|Sem Dimensões|
|iops|Sim|IOPS|Contagem|Média|Operações IO por segundo|Sem Dimensões|
|maximum_used_transactionIDs|Sim|IDs máximos de transações usadas|Contagem|Média|IDs máximos de transações usadas|Sem Dimensões|
|memory_percent|Sim|Por cento da memória|Percentagem|Média|Por cento da memória|Sem Dimensões|
|network_bytes_egress|Sim|Saída da Rede|Bytes|Total|Rede Para fora através de ligações ativas|Sem Dimensões|
|network_bytes_ingress|Sim|Entrada na Rede|Bytes|Total|Rede Em através de ligações ativas|Sem Dimensões|
|storage_percent|Sim|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Sem Dimensões|
|storage_used|Sim|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Sem Dimensões|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|Sim|Mensagens C2D abandonadas|Contagem|Total|Número de mensagens cloud-to-device abandonadas pelo dispositivo|Sem Dimensões|
|c2d.commands.egress.complete.success|Sim|Entregas de mensagens C2D concluídas|Contagem|Total|Número de entregas de mensagens nuvem-para-dispositivo concluídas com sucesso pelo dispositivo|Sem Dimensões|
|c2d.commands.egress.reject.success|Sim|Mensagens C2D rejeitadas|Contagem|Total|Número de mensagens nuvem-dispositivo rejeitadas pelo dispositivo|Sem Dimensões|
|c2d.methods.falha|Sim|Invocações de método direto falhadas|Contagem|Total|A contagem de todas as chamadas de métodos diretos falhadas.|Sem Dimensões|
|c2d.methods.requestSize|Sim|Tamanho do pedido de invocações de método direto|Bytes|Média|A média, min e máxima de todos os pedidos de método direto bem sucedidos.|Sem Dimensões|
|c2d.methods.responseSize|Sim|Tamanho da resposta das invocações do método direto|Bytes|Média|A média, min e máxima de todas as respostas de métodos diretos bem sucedidas.|Sem Dimensões|
|c2d.methods.success|Sim|Invocações de métodos diretos bem-sucedidas|Contagem|Total|A contagem de todas as chamadas de métodos diretos bem sucedidos.|Sem Dimensões|
|c2d.twin.read.failure|Sim|Gémeo falhado lê do fundo|Contagem|Total|A contagem de todas as leituras gémeas falhadas.|Sem Dimensões|
|c2d.twin.read.size|Sim|Tamanho de resposta de leituras gémeas da parte de trás|Bytes|Média|A média, min, e máx.|Sem Dimensões|
|c2d.twin.read.success|Sim|Gémea bem sucedida lê a partir da parte de trás|Contagem|Total|A contagem de todas as leituras gémeas iniciadas com sucesso.|Sem Dimensões|
|c2d.twin.update.failure|Sim|Falhas em duas atualizações a partir do final|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas pela parte de trás.|Sem Dimensões|
|c2d.twin.update.size|Sim|Tamanho das atualizações gémeas a partir da parte de trás|Bytes|Média|O tamanho médio, min e máximo de todas as atualizações gémeas iniciadas com sucesso.|Sem Dimensões|
|c2d.twin.update.success|Sim|Atualizações gémeas bem sucedidas a partir do final|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas com sucesso.|Sem Dimensões|
|C2DMessagesExpired|Sim|Mensagens C2D Expiradas (pré-visualização)|Contagem|Total|Número de mensagens de nuvem-para-dispositivo expiradas|Sem Dimensões|
|configurações|Sim|Métricas de configuração|Contagem|Total|Métricas para Operações de Configuração|Sem Dimensões|
|connectedDeviceCount|Não|Dispositivos conectados (pré-visualização)|Contagem|Média|Número de dispositivos ligados ao seu hub IoT|Sem Dimensões|
|d2c.endpoints.egress.builtIn.events|Sim|Encaminhamento: mensagens entregues a mensagens/eventos|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso ao ponto final incorporado (mensagens/eventos).|Sem Dimensões|
|d2c.endpoints.egress.eventHubs|Sim|Encaminhamento: mensagens entregues no Centro de Eventos|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso aos pontos finais do Event Hub.|Sem Dimensões|
|d2c.endpoints.egress.serviceBusQueues|Sim|Encaminhamento: mensagens entregues na Fila de Autocarros de Serviço|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso aos pontos finais da fila do Service Bus.|Sem Dimensões|
|d2c.endpoints.egress.serviceBusTopics|Sim|Encaminhamento: mensagens entregues no Service Bus Topic|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso aos pontos finais do tópico do Service Bus.|Sem Dimensões|
|d2c.endpoints.egress.storage|Sim|Encaminhamento: mensagens entregues no armazenamento|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso aos pontos finais de armazenamento.|Sem Dimensões|
|d2c.endpoints.egress.storage.blobs|Sim|Encaminhamento: bolhas entregues ao armazenamento|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou bolhas nos pontos finais de armazenamento.|Sem Dimensões|
|d2c.endpoints.egress.storage.bytes|Sim|Encaminhamento: dados entregues ao armazenamento|Bytes|Total|A quantidade de dados (bytes) IoT Hub de encaminhamento entregue nos pontos finais de armazenamento.|Sem Dimensões|
|d2c.endpoints.latncy.builtIn.events|Sim|Encaminhamento: latência da mensagem para mensagens/eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada da mensagem de telemetria no ponto final incorporado (mensagens/eventos).|Sem Dimensões|
|d2c.endpoints.latncy.eventHubs|Sim|Encaminhamento: latência da mensagem para o Centro de Eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para ioT Hub e a entrada de mensagens num ponto final do Event Hub.|Sem Dimensões|
|d2c.endpoints.latncy.serviceBusQueues|Sim|Encaminhamento: latência da mensagem para a fila do autocarro de serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada de mensagens de telemetria num ponto final de fila de autocarros de serviço.|Sem Dimensões|
|d2c.endpoints.latncy.serviceBusTopics|Sim|Encaminhamento: latência de mensagem para o tópico do autocarro de serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada de mensagens de telemetria num ponto final de ônibus de serviço.|Sem Dimensões|
|d2c.endpoints.latncy.storage|Sim|Encaminhamento: latência da mensagem para armazenamento|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada da mensagem de telemetria num ponto final de armazenamento.|Sem Dimensões|
|d2c.telemetria.egress.drop|Sim|Encaminhamento: mensagens de telemetria caídas |Contagem|Total|O número de vezes que as mensagens foram deixadas pelo itinerário do IoT Hub devido a pontos sem saída. Este valor não conta as mensagens entregues para a rota de retorno, uma vez que as mensagens deixadas não são entregues lá.|Sem Dimensões|
|d2c.telemetria.egress.fallback|Sim|Encaminhamento: mensagens entregues para recuo|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens ao ponto final associado à rota de retorno.|Sem Dimensões|
|d2c.telemetria.egress.inválido|Sim|Encaminhamento: mensagens de telemetria incompatíveis|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub não conseguiu entregar mensagens devido a uma incompatibilidade com o ponto final. Este valor não inclui retrações.|Sem Dimensões|
|d2c.telemetria.egress.órfão|Sim|Encaminhamento: mensagens de telemetria órfãs |Contagem|Total|O número de vezes que as mensagens foram órfãs pelo encaminhamento do IoT Hub porque não correspondem a nenhuma regra de encaminhamento (incluindo a regra do recuo). |Sem Dimensões|
|d2c.telemetria.egress.sucesso|Sim|Encaminhamento: mensagens de telemetria entregues|Contagem|Total|O número de vezes que as mensagens foram entregues com sucesso em todos os pontos finais utilizando o encaminhamento IoT Hub. Se uma mensagem for encaminhada para vários pontos finais, este valor aumenta um para cada entrega bem sucedida. Se uma mensagem for entregue no mesmo ponto final várias vezes, este valor aumenta um para cada entrega bem sucedida.|Sem Dimensões|
|d2c.telemetria.ingress.allProtocol|Sim|Mensagem de telemetria envia tentativas|Contagem|Total|Número de mensagens de telemetria dispositivo-nuvem tentadas de ser enviadas para o seu hub IoT|Sem Dimensões|
|d2c.telemetria.ingress.sendThrottle|Sim|Número de erros de estrangulamento|Contagem|Total|Número de erros de estrangulamento devido aos aceleradores de produção do dispositivo|Sem Dimensões|
|d2c.telemetria.ingress.success|Sim|Mensagens de telemetria enviadas|Contagem|Total|Número de mensagens de telemetria dispositivo-nuvem enviadas com sucesso para o seu hub IoT|Sem Dimensões|
|d2c.twin.read.failure|Sim|Leituras gémeas falhadas dos dispositivos|Contagem|Total|A contagem de todas as leituras gémeas iniciadas pelo dispositivo falhados.|Sem Dimensões|
|d2c.twin.read.size|Sim|Tamanho de resposta de leituras gémeas de dispositivos|Bytes|Média|A média, min e max de todas as leituras gémeas iniciadas pelo dispositivo com sucesso.|Sem Dimensões|
|d2c.twin.read.success|Sim|Leituras gémeas bem sucedidas de dispositivos|Contagem|Total|A contagem de todas as leituras gémeas iniciadas pelo dispositivo.|Sem Dimensões|
|d2c.twin.update.failure|Sim|Falhas em duas atualizações a partir de dispositivos|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas pelo dispositivo.|Sem Dimensões|
|d2c.twin.update.size|Sim|Tamanho das atualizações gémeas dos dispositivos|Bytes|Média|O tamanho médio, min e máximo de todas as atualizações gémeas iniciadas pelo dispositivo com sucesso.|Sem Dimensões|
|d2c.twin.update.success|Sim|Atualizações gémeas bem sucedidas dos dispositivos|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas pelo dispositivo com sucesso.|Sem Dimensões|
|dailyMessageQuotaUsed|Sim|Número total de mensagens utilizadas|Contagem|Máximo|Número de mensagens totais usadas hoje|Sem Dimensões|
|dispositivoDataUsage|Sim|Total da utilização de dados do dispositivo|Bytes|Total|Bytes transferidos de e para qualquer dispositivo ligado ao IotHub|Sem Dimensões|
|dispositivoDataUsageV2|Sim|Total da utilização dos dados do dispositivo (pré-visualização)|Bytes|Total|Bytes transferidos de e para qualquer dispositivo ligado ao IotHub|Sem Dimensões|
|dispositivos.connectedDevices.allProtocol|Sim|Dispositivos ligados (precotados) |Contagem|Total|Número de dispositivos ligados ao seu hub IoT|Sem Dimensões|
|dispositivos.totalDevices|Sim|Total de dispositivos (precotados)|Contagem|Total|Número de dispositivos registados no seu hub IoT|Sem Dimensões|
|EventGridDeliveries|Sim|Entregas de Grelha de Eventos (pré-visualização)|Contagem|Total|O número de eventos IoT Hub publicados para a Event Grid. Utilize a dimensão Resultado para o número de pedidos bem sucedidos e falhados. A dimensão EventType mostra o tipo de evento https://aka.ms/ioteventgrid) (.|Resultado, EventType|
|EventGridLatency|Sim|Latência da grelha de eventos (pré-visualização)|Milissegundos|Média|A latência média (milissegundos) de quando o evento Iot Hub foi gerado para quando o evento foi publicado para a Event Grid. Este número é uma média entre todos os tipos de eventos. Utilize a dimensão EventType para ver a latência de um tipo específico de evento.|EventType|
|jobs.cancelJob.failure|Sim|Cancelamentos de empregos falhados|Contagem|Total|A contagem de todas as chamadas falhadas para cancelar um emprego.|Sem Dimensões|
|jobs.cancelJob.sucesso|Sim|Cancelamentos de emprego bem-sucedidos|Contagem|Total|A contagem de todas as chamadas bem sucedidas para cancelar um emprego.|Sem Dimensões|
|empregos.concluídos|Sim|Trabalhos concluídos|Contagem|Total|A contagem de todos os trabalhos concluídos.|Sem Dimensões|
|jobs.createDirectMethodJob.fail|Sim|Criações falhadas de trabalhos de invocação de métodos|Contagem|Total|A contagem de todos os trabalhos falhados de invocação de métodos diretos.|Sem Dimensões|
|jobs.createDirectMethodJob.success|Sim|Criações bem sucedidas de trabalhos de invocação de métodos|Contagem|Total|A contagem de todos os trabalhos bem sucedidos de criação de trabalhos de invocação de métodos diretos.|Sem Dimensões|
|jobs.createTwinUpdateJob.failure|Sim|Criações falhadas de trabalhos de atualização dupla|Contagem|Total|A contagem de todos os trabalhos falhados de atualização dupla.|Sem Dimensões|
|jobs.createTwinUpdateJob.success|Sim|Criações bem sucedidas de trabalhos de atualização dupla|Contagem|Total|A contagem de todos os trabalhos bem sucedidos de criação de trabalhos de atualização dupla.|Sem Dimensões|
|empregos.falhou|Sim|Empregos falhados|Contagem|Total|A contagem de todos os trabalhos falhados.|Sem Dimensões|
|empregos.listJobs.fracasso|Sim|Chamadas falhadas para listar empregos|Contagem|Total|A contagem de todas as chamadas falhadas para listar empregos.|Sem Dimensões|
|jobs.listJobs.sucesso|Sim|Chamadas bem-sucedidas para listar empregos|Contagem|Total|A contagem de todas as chamadas bem sucedidas para listar empregos.|Sem Dimensões|
|jobs.consultaJobs.falha|Sim|Consultas de trabalho falhadas|Contagem|Total|A contagem de todas as chamadas falhadas para consultas.|Sem Dimensões|
|jobs.queryJobs.sucesso|Sim|Consultas de trabalho bem sucedidas|Contagem|Total|A contagem de todas as chamadas bem sucedidas para trabalhos de consulta.|Sem Dimensões|
|EncaminhamentoDataSizeInBytesDelivered|Sim|Tamanho da mensagem de entrega de encaminhamento em bytes (pré-visualização)|Bytes|Total|O tamanho total em bytes de mensagens entregues pelo hub IoT para um ponto final. Pode utilizar as dimensões EndpointName e EndpointType para visualizar o tamanho das mensagens nos bytes entregues nos seus diferentes pontos finais. O valor métrico aumenta para cada mensagem entregue, incluindo se a mensagem é entregue em vários pontos finais ou se a mensagem é entregue no mesmo ponto final várias vezes.|EndpointType, EndpointName, RoutingSource|
|RoteamentoDelivas|Sim|Entregas de encaminhamento (pré-visualização)|Contagem|Total|O número de vezes que o IoT Hub tentou entregar mensagens a todos os pontos finais utilizando o encaminhamento. Para ver o número de tentativas bem sucedidas ou falhadas, utilize a dimensão Resultado. Para ver a razão da falha, como inválido, caído ou órfão, use a dimensão FailReasonCategory. Também pode utilizar as dimensões EndpointName e EndpointType para entender quantas mensagens foram entregues nos seus diferentes pontos finais. O valor métrico aumenta por um para cada tentativa de entrega, incluindo se a mensagem é entregue em vários pontos finais ou se a mensagem é entregue no mesmo ponto final várias vezes.|EndpointType, EndpointName, FailureReasonCategory, Resultado, RoutingSource|
|EncaminhamentoDeliveryLatency|Sim|Data de entrega de encaminhamento (pré-visualização)|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada da mensagem de telemetria num ponto final. Pode utilizar as dimensões EndpointName e EndpointType para entender a latência dos seus diferentes pontos finais.|EndpointType, EndpointName, RoutingSource|
|totalDeviceCount|Não|Total de dispositivos (pré-visualização)|Contagem|Média|Número de dispositivos registados no seu hub IoT|Sem Dimensões|
|twinQueries.falha|Sim|Consultas gémeas falhadas|Contagem|Total|A contagem de todas as consultas gémeas falhadas.|Sem Dimensões|
|twinQueries.resultSize|Sim|Tamanho do resultado de consultas gémeas|Bytes|Média|A média, min e máximo do tamanho do resultado de todas as consultas gémeas bem sucedidas.|Sem Dimensões|
|twinQueries.sucesso|Sim|Consultas gémeas bem sucedidas|Contagem|Total|A contagem de todas as consultas gémeas bem sucedidas.|Sem Dimensões|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AttestationAttempts|Sim|Tentativas de atestado|Contagem|Total|Número de atestesações de dispositivos tentados|ProvisioningServiceName, Status, Protocolo|
|Configurações de Dispositivos|Sim|Dispositivos atribuídos|Contagem|Total|Número de dispositivos atribuídos a um hub IoT|ProvisioningServiceName, IotHubName|
|InscriçõesAptas|Sim|Tentativas de registo|Contagem|Total|Número de registos de dispositivos tentados|ProvisioningServiceName, IotHubName, Status|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/base de dadosAcontas

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AddRegion|Sim|Região Adicionada|Contagem|Contagem|Região Adicionada|Region|
|AutoscaleMaxThroughput|Não|Potência Max autoscale|Contagem|Máximo|Potência Max autoscale|Nome de dados, Nome de Coleção|
|DisponíveisToragem|Não|(precotado) Armazenamento disponível|Bytes|Total|O "Armazenamento Disponível" será removido do Azure Monitor no final de setembro de 2020. O tamanho do armazenamento da coleta Da Cosmos DB é agora ilimitado. A única restrição é que o tamanho de armazenamento de cada chave de partição lógica é de 20GB. Pode ativar a PartitionKeyStatistics no Registo de Diagnóstico para saber o consumo de armazenamento para as teclas de partição superior. Para mais informações sobre a quota de armazenamento Da Cosmos DB, consulte este doc https://docs.microsoft.com/azure/cosmos-db/concepts-limits . Após a depreciação, as restantes regras de alerta ainda definidas na métrica depreciada serão automaticamente desativadas após a data de depreciação.|Nome de coleção, nome de base de dados, região|
|CassandraConnectionClosures|Não|Encerramentos de conexão cassandra|Contagem|Total|Número de ligações cassandra que foram fechadas, reportadas a uma granularidade de 1 minuto|Região, EncerramentoReason|
|CassandraConnectorAvgReplicationLatency|Não|Replicação média do conector Cassandra|MilliSeconds|Média|Replicação média do conector Cassandra|Sem Dimensões|
|CassandraConnectorReplicationHealthStatus|Não|Estado de Saúde da Replicação do Conector Cassandra|Contagem|Contagem|Estado de Saúde da Replicação do Conector Cassandra|NotStarted, ReplicationInProgress, Error|
|CassandraKeyspaceCreate|Não|Cassandra Keyspace Criado|Contagem|Contagem|Cassandra Keyspace Criado|Nome de recursos, |
|CassandraKeyspaceDelete|Não|Cassandra Keyspace eliminada|Contagem|Contagem|Cassandra Keyspace eliminada|Nome de recursos, |
|CassandraKeyspaceThroughputUpdate|Não|Cassandra Keyspace Throughput Atualizado|Contagem|Contagem|Cassandra Keyspace Throughput Atualizado|Nome de recursos, |
|CassandraKeyspaceUpdate|Não|Cassandra Keyspace Atualizado|Contagem|Contagem|Cassandra Keyspace Atualizado|Nome de recursos, |
|CassandraRequestCharges|Não|Cassandra Request Charges|Contagem|Total|RUs consumidos por pedidos de Cassandra feitos|Nome de dados, Nome de Recolha, Região, OperaçãoType,Tip de Recursos|
|CassandraRequests|Não|Pedidos de Cassandra|Contagem|Contagem|Número de pedidos de Cassandra feitos|Nome de dados, Nome de Recolha, Região, OperaçãoType,Tip de Recursos, Código de Erro|
|CassandraTableCreate|Não|Tabela Cassandra Criada|Contagem|Contagem|Tabela Cassandra Criada|Nome de recurso, ChildResourceName, |
|CassandraTableDelete|Não|Tabela Cassandra Eliminada|Contagem|Contagem|Tabela Cassandra Eliminada|Nome de recurso, ChildResourceName, |
|CassandraTableThroughputUpdate|Não|Produção de tabela Cassandra atualizada|Contagem|Contagem|Produção de tabela Cassandra atualizada|Nome de recurso, ChildResourceName, |
|CassandraTableUpdate|Não|Tabela Cassandra Atualizada|Contagem|Contagem|Tabela Cassandra Atualizada|Nome de recurso, ChildResourceName, |
|Criar Contas|Sim|Conta Criada|Contagem|Contagem|Conta Criada|Sem Dimensões|
|DataUsage|Não|Utilização de Dados|Bytes|Total|Total de utilização de dados reportados a 5 minutos de granularidade|Nome de coleção, nome de base de dados, região|
|ExcluirAconta|Sim|Conta Eliminada|Contagem|Contagem|Conta Eliminada|Sem Dimensões|
|Contagem de documentos|Não|Contagem de documentos|Contagem|Total|Contagem total de documentos reportada em 5 minutos granularidade|Nome de coleção, nome de base de dados, região|
|DocumentQuota|Não|Quota documental|Bytes|Total|Quota total de armazenamento reportada a 5 minutos de granularidade|Nome de coleção, nome de base de dados, região|
|GremlinDatabaseCreate|Não|Gremlin Base de Dados Criada|Contagem|Contagem|Gremlin Base de Dados Criada|Nome de recursos, |
|GremlinDatabaseDelete|Não|Gremlin Base de Dados Eliminada|Contagem|Contagem|Gremlin Base de Dados Eliminada|Nome de recursos, |
|GremlinDatabaseThroughputUpdate|Não|Produção de base de dados gremlin atualizada|Contagem|Contagem|Produção de base de dados gremlin atualizada|Nome de recursos, |
|GremlinDatabaseUpdate|Não|Base de Dados Gremlin Atualizada|Contagem|Contagem|Base de Dados Gremlin Atualizada|Nome de recursos, |
|GremlinGraphCreate|Não|Gremlin Graph Criado|Contagem|Contagem|Gremlin Graph Criado|Nome de recurso, ChildResourceName, |
|GremlinGraphDelete|Não|Gremlin Graph Eliminado|Contagem|Contagem|Gremlin Graph Eliminado|Nome de recurso, ChildResourceName, |
|GremlinGraphThroughputUpdate|Não|Throughputo de gráfico gremlin atualizado|Contagem|Contagem|Throughputo de gráfico gremlin atualizado|Nome de recurso, ChildResourceName, |
|GremlinGraphUpdate|Não|Gráfico de Gremlin Atualizado|Contagem|Contagem|Gráfico de Gremlin Atualizado|Nome de recurso, ChildResourceName, |
|IndexUsage|Não|Utilização do Índice|Bytes|Total|Utilização total do índice reportada a 5 minutos de granularidade|Nome de coleção, nome de base de dados, região|
|MetadataReques|Não|Pedidos de Metadados|Contagem|Contagem|Contagem de pedidos de metadados. Cosmos DB mantém recolha de metadados do sistema para cada conta, que lhe permite enumerar coleções, bases de dados, etc, e suas configurações, gratuitamente.|Data de dados Nome, Nome de Recolha, Região, StatusCode, |
|MongoCollectionCreate|Não|Coleção Mongo Criada|Contagem|Contagem|Coleção Mongo Criada|Nome de recurso, ChildResourceName, |
|MongoCollectionDelete|Não|Coleção mongo eliminada|Contagem|Contagem|Coleção mongo eliminada|Nome de recurso, ChildResourceName, |
|MongoCollectionThroughputUpdate|Não|Produção de coleção de Mongo atualizada|Contagem|Contagem|Produção de coleção de Mongo atualizada|Nome de recurso, ChildResourceName, |
|MongoCollectionUpdate|Não|Coleção Mongo Atualizada|Contagem|Contagem|Coleção Mongo Atualizada|Nome de recurso, ChildResourceName, |
|MongoDatabaseDelete|Não|Base de Dados de Mongo eliminada|Contagem|Contagem|Base de Dados de Mongo eliminada|Nome de recursos, |
|MongoDatabaseThroughputUpdate|Não|Produção de base de dados de Mongo atualizada|Contagem|Contagem|Produção de base de dados de Mongo atualizada|Nome de recursos, |
|MongodbDatabaseCreate|Não|Base de Dados de Mongo Criada|Contagem|Contagem|Base de Dados de Mongo Criada|Nome de recursos, |
|MongoDBDatabaseUpdate|Não|Base de Dados de Mongo Atualizada|Contagem|Contagem|Base de Dados de Mongo Atualizada|Nome de recursos, |
|MongoRequestCharge|Sim|Taxa de pedido de Mongo|Contagem|Total|Unidades de pedido da Mongo Consumidas|Data de Dados Nome, Nome de Recolha, Região, Nome de Comando, ErrorCode, Status|
|MongoRequests|Sim|Pedidos de Mongo|Contagem|Contagem|Número de pedidos da Mongo Feitos|Data de Dados Nome, Nome de Recolha, Região, Nome de Comando, ErrorCode, Status|
|MongoRequestsCount|Não|Taxa de pedido de Mongo|CondePerSecond|Média|Mongo pede Conde por segundo|Nome de dados, Nome de Recolha, Região, Código de Erro|
|MongoRequestsDelete|Não|Tarifa de pedido de eliminação de Mongo|CondePerSecond|Média|Mongo Delete request por segundo|Nome de dados, Nome de Recolha, Região, Código de Erro|
|MongoRequestsInsert|Não|Taxa de pedido de inserção de Mongo|CondePerSecond|Média|Contagem de Inserção de Mongo por segundo|Nome de dados, Nome de Recolha, Região, Código de Erro|
|MongoRequestsQuery|Não|Taxa de pedido de consulta de Mongo|CondePerSecond|Média|Pedido de consulta de Mongo por segundo|Nome de dados, Nome de Recolha, Região, Código de Erro|
|MongoRequestsUpdate|Não|Taxa de pedido de atualização de Mongo|CondePerSecond|Média|Pedido de atualização de Mongo por segundo|Nome de dados, Nome de Recolha, Região, Código de Erro|
|Ruconsumption normalizado|Não|Consumo de RU normalizado|Percentagem|Máximo|Percentagem de consumo max RU por minuto|Nome de coleção, Nome de Base de Dados, Região, PartitionKeyRangeId|
|ProvisionedThroughput|Não|Débito Aprovisionado|Contagem|Máximo|Débito Aprovisionado|Nome de dados, Nome de Coleção|
|RegionFailover|Sim|Região falhou|Contagem|Contagem|Região falhou|Sem Dimensões|
|Remover a Região|Sim|Região removida|Contagem|Contagem|Região removida|Region|
|ReplicationLatency|Sim|Latência de replicação P99|MilliSeconds|Média|P99 Replicação Latência em todas as regiões de origem e alvo para conta geo-habilitada|SourceRegion, TargetRegion|
|ServerSideLatency|Não|Latência do lado do servidor|MilliSeconds|Média|Latência do lado do servidor|Nome de dados, Nome de Recolha, Região, ConnectionMode, OperationType, PublicAPIType|
|ServiçoSAdisponibilidade|Não|Disponibilidade de serviço|Percentagem|Média|Conta solicita disponibilidade a uma hora, dia ou mês de granularidade|Sem Dimensões|
|SqlContainerCreate|Não|Recipiente sql criado|Contagem|Contagem|Recipiente sql criado|Nome de recurso, ChildResourceName, |
|SqlContainerDelete|Não|Recipiente sql eliminado|Contagem|Contagem|Recipiente sql eliminado|Nome de recurso, ChildResourceName, |
|SqlContainerThroughputUpdate|Não|Produção de recipiente sql atualizado|Contagem|Contagem|Produção de recipiente sql atualizado|Nome de recurso, ChildResourceName, |
|SqlContainerUpdate|Não|Recipiente Sql Atualizado|Contagem|Contagem|Recipiente Sql Atualizado|Nome de recurso, ChildResourceName, |
|SqlDatabaseCreate|Não|Base de Dados Sql Criada|Contagem|Contagem|Base de Dados Sql Criada|Nome de recursos, |
|SqlDatabaseDelete|Não|Base de Dados Sql Eliminada|Contagem|Contagem|Base de Dados Sql Eliminada|Nome de recursos, |
|SqlDatabaseThroughputUpdate|Não|Produção de base de dados sql atualizada|Contagem|Contagem|Produção de base de dados sql atualizada|Nome de recursos, |
|SqlDatabaseUpdate|Não|Base de Dados Sql Atualizada|Contagem|Contagem|Base de Dados Sql Atualizada|Nome de recursos, |
|TableTableCreate|Não|Tabela AzureTable Criada|Contagem|Contagem|Tabela AzureTable Criada|Nome de recursos, |
|TableTableDelete|Não|Tabela AzureTable Eliminada|Contagem|Contagem|Tabela AzureTable Eliminada|Nome de recursos, |
|TableTableThroughputUpdate|Não|Produção de tabela azuretable atualizado|Contagem|Contagem|Produção de tabela azuretable atualizado|Nome de recursos, |
|TableTableUpdate|Não|Tabela AzureTable atualizada|Contagem|Contagem|Tabela AzureTable atualizada|Nome de recursos, |
|TotalRequests|Sim|Total de Pedidos|Contagem|Contagem|Número de pedidos feitos|Data de DadosName, Nome de Recolha, Região, Código de Estado, OperaçãoType, Estado|
|TotalRequestUnits|Sim|Total de Unidades de Pedido|Contagem|Total|Unidades de pedido consumidas|Data de DadosName, Nome de Recolha, Região, Código de Estado, OperaçãoType, Estado|
|UpdateAccountKeys|Sim|Chaves de conta atualizadas|Contagem|Contagem|Chaves de conta atualizadas|Teclas|
|UpdateAccountNetworkSettings|Sim|Definições de rede de conta atualizadas|Contagem|Contagem|Definições de rede de conta atualizadas|Sem Dimensões|
|UpdateAccountReplicationSettings|Sim|Definições de replicação de conta atualizadas|Contagem|Contagem|Definições de replicação de conta atualizadas|Sem Dimensões|
|AtualizaçãoDiagnosticsSettings|Não|Definições de diagnóstico de conta atualizadas|Contagem|Contagem|Definições de diagnóstico de conta atualizadas|DiagnósticoSettingsName, Nome do Grupo de Recursos|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domínios

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Sim|Eventos lettered mortos|Contagem|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|Tema, EventosUbscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|EntregaAttemptFailCount|Não|Eventos falhados de entrega|Contagem|Total|Total de eventos não entregaram a esta subscrição do evento|Tema, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|EntregaSSuccessCount|Sim|Eventos Entregues|Contagem|Total|Total de eventos entregues a esta subscrição do evento|Tema, Nome de Subscrição de Eventos, Nome de Subsubscrição de Eventos|
|DestinationProcessingDurationInMs|Não|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Tema, Nome de Subscrição de Eventos, Nome de Subsubscrição de Eventos|
|DropEventCount|Sim|Eventos abandonados|Contagem|Total|Total de eventos perdidos correspondentes a esta subscrição do evento|Tema, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Sim|Eventos Combinados|Contagem|Total|Total de eventos combinados com esta subscrição de evento|Tema, Nome de Subscrição de Eventos, Nome de Subsubscrição de Eventos|
|PublicarFailCount|Sim|Publicar Eventos Falhados|Contagem|Total|Total de eventos não publicou para este tópico|Tópico, ErrorType, ErrorType|
|PublicarSuccessCount|Sim|Eventos Publicados|Contagem|Total|Total de eventos publicados neste tópico|Tópico|
|Publicar Inimitrodências|Sim|Publicar Latência de Sucesso|Milissegundos|Total|Publique latência de sucesso em milissegundos|Sem Dimensões|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Sim|Eventos lettered mortos|Contagem|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason|
|EntregaAttemptFailCount|Não|Eventos falhados de entrega|Contagem|Total|Total de eventos não entregaram a esta subscrição do evento|Erro, ErrorType|
|EntregaSSuccessCount|Sim|Eventos Entregues|Contagem|Total|Total de eventos entregues a esta subscrição do evento|Sem Dimensões|
|DestinationProcessingDurationInMs|Não|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Sem Dimensões|
|DropEventCount|Sim|Eventos abandonados|Contagem|Total|Total de eventos perdidos correspondentes a esta subscrição do evento|GotaReason|
|MatchedEventCount|Sim|Eventos Combinados|Contagem|Total|Total de eventos combinados com esta subscrição de evento|Sem Dimensões|


## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|PublicarFailCount|Sim|Publicar Eventos Falhados|Contagem|Total|Total de eventos não publicou para este tópico|ErrorType, Error|
|PublicarSuccessCount|Sim|Eventos Publicados|Contagem|Total|Total de eventos publicados neste tópico|Sem Dimensões|
|Publicar Inimitrodências|Sim|Publicar Latência de Sucesso|Milissegundos|Total|Publique latência de sucesso em milissegundos|Sem Dimensões|
|InigualávelEventCount|Sim|Eventos incomparáveis|Contagem|Total|Total de eventos que não correspondem a nenhuma das subscrições do evento para este tópico|Sem Dimensões|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Sim|Eventos lettered mortos|Contagem|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason, Nome de EventosSubscrição|
|EntregaAttemptFailCount|Não|Eventos falhados de entrega|Contagem|Total|Total de eventos não entregaram a esta subscrição do evento|Erro, ErrorType, EventSubscriptionName|
|EntregaSSuccessCount|Sim|Eventos Entregues|Contagem|Total|Total de eventos entregues a esta subscrição do evento|Nome de subscrição de eventos|
|DestinationProcessingDurationInMs|Não|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Nome de subscrição de eventos|
|DropEventCount|Sim|Eventos abandonados|Contagem|Total|Total de eventos perdidos correspondentes a esta subscrição do evento|DropReason, Nome de EventosSubscription|
|MatchedEventCount|Sim|Eventos Combinados|Contagem|Total|Total de eventos combinados com esta subscrição de evento|Nome de subscrição de eventos|
|PublicarFailCount|Sim|Publicar Eventos Falhados|Contagem|Total|Total de eventos não publicou para este tópico|ErrorType, Error|
|PublicarSuccessCount|Sim|Eventos Publicados|Contagem|Total|Total de eventos publicados neste tópico|Sem Dimensões|
|Publicar Inimitrodências|Sim|Publicar Latência de Sucesso|Milissegundos|Total|Publique latência de sucesso em milissegundos|Sem Dimensões|
|InigualávelEventCount|Sim|Eventos incomparáveis|Contagem|Total|Total de eventos que não correspondem a nenhuma das subscrições do evento para este tópico|Sem Dimensões|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/tópicos

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Sim|Eventos lettered mortos|Contagem|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason, Nome de EventosSubscrição|
|EntregaAttemptFailCount|Não|Eventos falhados de entrega|Contagem|Total|Total de eventos não entregaram a esta subscrição do evento|Erro, ErrorType, EventSubscriptionName|
|EntregaSSuccessCount|Sim|Eventos Entregues|Contagem|Total|Total de eventos entregues a esta subscrição do evento|Nome de subscrição de eventos|
|DestinationProcessingDurationInMs|Não|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Nome de subscrição de eventos|
|DropEventCount|Sim|Eventos abandonados|Contagem|Total|Total de eventos perdidos correspondentes a esta subscrição do evento|DropReason, Nome de EventosSubscription|
|MatchedEventCount|Sim|Eventos Combinados|Contagem|Total|Total de eventos combinados com esta subscrição de evento|Nome de subscrição de eventos|
|PublicarFailCount|Sim|Publicar Eventos Falhados|Contagem|Total|Total de eventos não publicou para este tópico|ErrorType, Error|
|PublicarSuccessCount|Sim|Eventos Publicados|Contagem|Total|Total de eventos publicados neste tópico|Sem Dimensões|
|Publicar Inimitrodências|Sim|Publicar Latência de Sucesso|Milissegundos|Total|Publique latência de sucesso em milissegundos|Sem Dimensões|
|InigualávelEventCount|Sim|Eventos incomparáveis|Contagem|Total|Total de eventos que não correspondem a nenhuma das subscrições do evento para este tópico|Sem Dimensões|


## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ActiveConnections|Não|ActiveConnections|Contagem|Média|Total de conexões ativas para Microsoft.EventHub.|Sem Dimensões|
|DisponívelMemory|Não|Memória Disponível|Percentagem|Máximo|Memória disponível para o Cluster De Eventos hub em percentagem da memória total.|Função|
|CaptureBacklog|Não|Capture Backlog.|Contagem|Total|Capture Backlog for Microsoft.EventHub.|Sem Dimensões|
|CapturouBytes|Não|Bytes capturados.|Bytes|Total|Bytes capturados para Microsoft.EventHub.|Sem Dimensões|
|Caixas de Captura|Não|Mensagens capturadas.|Contagem|Total|Mensagens capturadas para Microsoft.EventHub.|Sem Dimensões|
|LigaçõesClosed|Não|Ligações Fechadas.|Contagem|Média|Ligações fechadas para Microsoft.EventHub.|Sem Dimensões|
|LigaçõesAsa|Não|Ligações Abertas.|Contagem|Média|Ligações abertas para Microsoft.EventHub.|Sem Dimensões|
|CPU|Não|CPU|Percentagem|Máximo|Utilização do CPU para o Cluster de Hub de Eventos em percentagem|Função|
|IncomingBytes|Sim|Bytes Recebidos.|Bytes|Total|Incoming Bytes para Microsoft.EventHub.|Sem Dimensões|
|Entradas|Sim|Mensagens Recebidas|Contagem|Total|Mensagens de entrada para Microsoft.EventHub.|Sem Dimensões|
|IncomingRequests|Sim|Pedidos Recebidos|Contagem|Total|Pedidos de entrada para Microsoft.EventHub.|Sem Dimensões|
|OutgoingBytes|Sim|Bytes de saída.|Bytes|Total|Bytes de saída para Microsoft.EventHub.|Sem Dimensões|
|Despesas de Saída|Sim|Mensagens Enviadas|Contagem|Total|Mensagens de saída para o Microsoft.EventHub.|Sem Dimensões|
|QuotaExceededErrors|Não|Erros de Quota Excedida.|Contagem|Total|Quota Excedeu erros para Microsoft.EventHub.|Sem Dimensões|
|ServerErrors|Não|Erros do Servidor.|Contagem|Total|Erros de servidor para Microsoft.EventHub.|Sem Dimensões|
|Tamanho|Não|Tamanho|Bytes|Média|Tamanho de um EventHub em Bytes.|Função|
|Requess de sucesso|Não|Pedidos Com Êxito|Contagem|Total|Pedidos de sucesso para Microsoft.EventHub.|Sem Dimensões|
|ThrottledRequests|Não|Pedidos Limitados.|Contagem|Total|Pedidos de aceleração para o Microsoft.EventHub.|Sem Dimensões|
|UserErrors|Não|Erros do Utilizador.|Contagem|Total|Erros do utilizador para o Microsoft.EventHub.|Sem Dimensões|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/espaços de nome

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ActiveConnections|Não|ActiveConnections|Contagem|Média|Total de conexões ativas para Microsoft.EventHub.|Sem Dimensões|
|CaptureBacklog|Não|Capture Backlog.|Contagem|Total|Capture Backlog for Microsoft.EventHub.|Nome de Entidade|
|CapturouBytes|Não|Bytes capturados.|Bytes|Total|Bytes capturados para Microsoft.EventHub.|Nome de Entidade|
|Caixas de Captura|Não|Mensagens capturadas.|Contagem|Total|Mensagens capturadas para Microsoft.EventHub.|Nome de Entidade|
|LigaçõesClosed|Não|Ligações Fechadas.|Contagem|Média|Ligações fechadas para Microsoft.EventHub.|Nome de Entidade|
|LigaçõesAsa|Não|Ligações Abertas.|Contagem|Média|Ligações abertas para Microsoft.EventHub.|Nome de Entidade|
|EHABL|Sim|Mensagens de atraso de arquivo (Deprecadas)|Contagem|Total|Mensagens de arquivo do Event Hub em atraso para um espaço de nome (Depreciado)|Sem Dimensões|
|EHAMBS|Sim|Produção de mensagem de arquivo (Precotada)|Bytes|Total|Event Hub arquivou produção de mensagem num espaço de nome (Depreciado)|Sem Dimensões|
|EHAMSGS|Sim|Mensagens de arquivo (Depreciadas)|Contagem|Total|Event Hub arquivou mensagens num espaço de nome (Depreciado)|Sem Dimensões|
|EHINBYTES|Sim|Bytes de entrada (Deprecados)|Bytes|Total|Envio de mensagem do Event Hub para um espaço de nome (Depreciado)|Sem Dimensões|
|EHINMBS|Sim|Bytes de entrada (obsoletos) (Deprecados)|Bytes|Total|Mensagem de entrada do Event Hub para um espaço de nome. Esta métrica está prevadida. Utilize em vez disso a métrica de entrada de bytes (Deprecada)|Sem Dimensões|
|EFINMSGS|Sim|Mensagens recebidas (depreciadas)|Contagem|Total|Total de mensagens recebidas para um espaço de nome (Deprecado)|Sem Dimensões|
|EHOUTBYTES|Sim|Bytes de saída (Deprecados)|Bytes|Total|Mensagem de saída do Event Hub para um espaço de nome (Depreciado)|Sem Dimensões|
|EHOUTMBS|Sim|Bytes de saída (obsoletos) (Preprecados)|Bytes|Total|Mensagem de saída do Event Hub para um espaço de nome. Esta métrica está prevadida. Utilize em vez disso bytes outgoing (Deprecado)|Sem Dimensões|
|EHOUTMSGS|Sim|Mensagens de saída (depreciadas)|Contagem|Total|Total de mensagens de saída para um espaço de nome (Deprecado)|Sem Dimensões|
|FAILREQ|Sim|Pedidos Falhados (Deprecados)|Contagem|Total|Total de pedidos falhados para um espaço de nome (Deprecado)|Sem Dimensões|
|IncomingBytes|Sim|Bytes Recebidos.|Bytes|Total|Incoming Bytes para Microsoft.EventHub.|Nome de Entidade|
|Entradas|Sim|Mensagens Recebidas|Contagem|Total|Mensagens de entrada para Microsoft.EventHub.|Nome de Entidade|
|IncomingRequests|Sim|Pedidos Recebidos|Contagem|Total|Pedidos de entrada para Microsoft.EventHub.|Nome de Entidade|
|INMSGS|Sim|Mensagens recebidas (obsoletas) (Depreciadas)|Contagem|Total|Total de mensagens recebidas para um espaço de nome. Esta métrica está prevadida. Utilize a métrica de Mensagens recebidas (Deprecadas)|Sem Dimensões|
|INREQS|Sim|Pedidos de Entrada (Deprecados)|Contagem|Total|Total de pedidos de envio para um espaço de nome (Deprecado)|Sem Dimensões|
|INTERR|Sim|Erros internos do servidor (preprecados)|Contagem|Total|Erros internos totais do servidor para um espaço de nome (Deprecado)|Sem Dimensões|
|MISCERR|Sim|Outros Erros (Precotados)|Contagem|Total|Total de pedidos falhados para um espaço de nome (Deprecado)|Sem Dimensões|
|OutgoingBytes|Sim|Bytes de saída.|Bytes|Total|Bytes de saída para Microsoft.EventHub.|Nome de Entidade|
|Despesas de Saída|Sim|Mensagens Enviadas|Contagem|Total|Mensagens de saída para o Microsoft.EventHub.|Nome de Entidade|
|OUTMSGS|Sim|Mensagens de saída (obsoletas) (Depreciadas)|Contagem|Total|Mensagens de saída total para um espaço de nome. Esta métrica está prevadida. Utilize em vez disso a métrica mensagens de saída (deprecadas)|Sem Dimensões|
|QuotaExceededErrors|Não|Erros de Quota Excedida.|Contagem|Total|Quota Excedeu erros para Microsoft.EventHub.|Nome da Entidade, |
|ServerErrors|Não|Erros do Servidor.|Contagem|Total|Erros de servidor para Microsoft.EventHub.|Nome da Entidade, |
|Tamanho|Não|Tamanho|Bytes|Média|Tamanho de um EventHub em Bytes.|Nome de Entidade|
|Requess de sucesso|Não|Pedidos Com Êxito|Contagem|Total|Pedidos de sucesso para Microsoft.EventHub.|Nome da Entidade, |
|SUCCREQ|Sim|Pedidos de sucesso (Deprecados)|Contagem|Total|Total de pedidos bem sucedidos para um espaço de nome (Deprecado)|Sem Dimensões|
|SVRBSY|Sim|Erros ocupados do servidor (preprecados)|Contagem|Total|Erros de ocupado total do servidor para um espaço de nome (Deprecado)|Sem Dimensões|
|ThrottledRequests|Não|Pedidos Limitados.|Contagem|Total|Pedidos de aceleração para o Microsoft.EventHub.|Nome da Entidade, |
|UserErrors|Não|Erros do Utilizador.|Contagem|Total|Erros do utilizador para o Microsoft.EventHub.|Nome da Entidade, |


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|CategorizadosGatewayRequests|Sim|Pedidos de Gateway categorizados|Contagem|Total|Número de pedidos de gateway por categorias (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|GatewayRequests|Sim|Pedidos de Gateway|Contagem|Total|Número de pedidos de gateway|HttpStatus|
|NumActiveWorkers|Sim|Número de trabalhadores ativos|Contagem|Máximo|Número de trabalhadores ativos|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|MétricaThreshold|Sim|Limiar métrico|Contagem|Média|O limiar de autoescala configurado quando a autoescalada foi executada.|MétricaTriggerrule|
|Capacidade Observada|Sim|Capacidade observada|Contagem|Média|A capacidade reportada para autoescalar quando executou.|Sem Dimensões|
|ValorMétrico Observado|Sim|Valor métrico observado|Contagem|Média|O valor calculado por autoescala quando executado|MetricTriggerSource|
|ScaleActionsInitiado|Sim|Ações de escala iniciadas|Contagem|Total|A direção da operação de escala.|Direção de Escala|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Componentes

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|disponibilidadeResults/disponibilidadePercentage|Sim|Disponibilidade|Percentagem|Média|Percentagem de testes de disponibilidade concluídos com sucesso|disponibilidadeResult/nome, disponibilidadeResult/localização|
|disponibilidadeResults/contagem|Não|Testes de disponibilidade|Contagem|Contagem|Contagem de testes de disponibilidade|disponibilidadeResult/nome, disponibilidadeResult/localização, disponibilidadeResult/sucesso|
|disponibilidadeResults/duração|Sim|Duração do teste de disponibilidade|MilliSeconds|Média|Duração do teste de disponibilidade|disponibilidadeResult/nome, disponibilidadeResult/localização, disponibilidadeResult/sucesso|
|browserEs/networkDuration|Sim|Tempo de ligação da rede de carga de página|MilliSeconds|Média|Tempo entre o pedido do utilizador e a ligação à rede. Inclui a procura de DNS e a ligação de transporte.|Sem Dimensões|
|browserEs/processamentoDuração|Sim|Tempo de processamento do cliente|MilliSeconds|Média|Tempo entre receber o último byte de um documento até que o DOM seja carregado. Os pedidos de async ainda podem estar a ser processados.|Sem Dimensões|
|browserEs/receberDuration|Sim|Tempo de resposta de receção|MilliSeconds|Média|Tempo entre o primeiro e o último bytes, ou até a desconexão.|Sem Dimensões|
|browserEs/sendDuration|Sim|Enviar tempo de pedido|MilliSeconds|Média|Tempo entre a ligação de rede e receber o primeiro byte.|Sem Dimensões|
|browserEs/totalDuration|Sim|Tempo de carregamento da página do navegador|MilliSeconds|Média|Tempo do pedido do utilizador até dom, folhas de estilo, scripts e imagens são carregados.|Sem Dimensões|
|dependências/contagem|Não|Chamadas de dependência|Contagem|Contagem|Contagem de chamadas feitas pela aplicação a recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/sucesso, dependência/alvo, dependência/resultadoSCódigo, operação/sintético, nuvem/roleInstance, cloud/roleName|
|dependências/duração|Sim|Duração da dependência|MilliSeconds|Média|Duração das chamadas efetuadas pela aplicação a recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/sucesso, dependência/alvo, dependência/resultadoSCódigo, operação/sintético, nuvem/roleInstance, cloud/roleName|
|dependências/falhados|Não|Falhas nas chamadas de dependência|Contagem|Contagem|Contagem de chamadas de dependência falhadas feitas pela aplicação a recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/alvo, dependência/resultadoSCódigo, operação/sintético, nuvem/roleInstance, cloud/roleName|
|exceções/navegador|Não|Exceções de browser|Contagem|Contagem|Contagem de exceções não realizadas no navegador.|nuvem/funçãoName|
|exceções/contagem|Sim|Exceções|Contagem|Contagem|Contagem combinada de todas as exceções não conseguidas.|cloud/roleName, cloud/roleInstance, cliente/tipo|
|exceções/servidor|Não|Exceções do servidor|Contagem|Contagem|Contagem de exceções não realizadas lançadas na aplicação do servidor.|cloud/roleName, cloud/roleInstance|
|pageVers/contagem|Sim|Vistas da página|Contagem|Contagem|Contagem de visualizações de página.|operação/sintético, nuvem/funName|
|páginaVerses/duração|Sim|Tempo de carga da visualização da página|MilliSeconds|Média|Tempo de carga da visualização da página|operação/sintético, nuvem/funName|
|performanceCounters/excepçõesPerSecond|Sim|Taxa de exceção|CondePerSecond|Média|Contagem de exceções manuseadas e não manipuladas reportadas às janelas, incluindo exceções .NET e exceções não geridas que são convertidas em exceções .NET.|nuvem/papelInstância|
|performanceCounters/memoryAvailableBytes|Sim|Memória disponível|Bytes|Média|Memória física imediatamente disponível para alocação a um processo ou para uso do sistema.|nuvem/papelInstância|
|performanceCounters/processCpuPercentage|Sim|Processo CPU|Percentagem|Média|A percentagem de tempo decorrido que todos os fios de processo utilizaram o processador para executar instruções. Isto pode variar entre 0 a 100. Esta métrica indica apenas o desempenho do processo w3wp.|nuvem/papelInstância|
|performanceCounters/processIOBytesPerSecond|Sim|Taxa IO do processo|BytesPerSecond|Média|Total de bytes por segundo lido e escrito para ficheiros, rede e dispositivos.|nuvem/papelInstância|
|performanceCounters/processadorEsPercentage|Sim|Tempo do processador|Percentagem|Média|A percentagem de tempo que o processador gasta em fios não ociosos.|nuvem/papelInstância|
|performanceCounters/processPrivateBytes|Sim|Processar bytes privados|Bytes|Média|Memória exclusivamente atribuída aos processos da aplicação monitorizada.|nuvem/papelInstância|
|performanceCounters/requestExecutionTime|Sim|HTTP solicitar tempo de execução|MilliSeconds|Média|Tempo de execução do mais recente pedido.|nuvem/papelInstância|
|performanceCounters/requestsInQueue|Sim|Pedidos HTTP na fila de candidaturas|Contagem|Média|Duração da fila de pedidos.|nuvem/papelInstância|
|performanceCounters/requestsPerSecond|Sim|Taxa de pedido HTTP|CondePerSecond|Média|Taxa de todos os pedidos para o pedido por segundo a partir de ASP.NET.|nuvem/papelInstância|
|pedidos/contagem|Não|Pedidos de servidor|Contagem|Contagem|Contagem de pedidos HTTP concluídos.|pedido/performanceBucket, pedido/resultadoSCode, operação/sintético, nuvem/funInstance, pedido/sucesso, nuvem/funName|
|pedidos/duração|Sim|Tempo de resposta do servidor|MilliSeconds|Média|Tempo entre receber um pedido HTTP e terminar o envio da resposta.|pedido/performanceBucket, pedido/resultadoSCode, operação/sintético, nuvem/funInstance, pedido/sucesso, nuvem/funName|
|pedidos/falhados|Não|Pedidos com falhas|Contagem|Contagem|Contagem de pedidos HTTP marcados como falhados. Na maioria dos casos, trata-se de pedidos com um código de resposta >= 400 e não igual a 401.|pedido/performanceBucket, pedido/resultadoSCode, operação/sintético, nuvem/roleInstance, cloud/roleName|
|pedidos/tarifa|Não|Taxa de pedido do servidor|CondePerSecond|Média|Taxa de pedidos de servidor por segundo|pedido/performanceBucket, pedido/resultadoSCode, operação/sintético, nuvem/funInstance, pedido/sucesso, nuvem/funName|
|vestígios/contagem|Sim|Rastreios|Contagem|Contagem|Contagem de documentos de rastreio|traço/severidadeLevel, operação/sintético, nuvem/funName, nuvem/roleInstance|

## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|c2d.property.read.failure|Sim|Falha na propriedade do dispositivo lê-se da IoT Central|Contagem|Total|A contagem de todas as leituras de propriedade falhada iniciadas a partir da IoT Central|Sem Dimensões|
|c2d.property.read.success|Sim|Propriedade de dispositivo de sucesso lê da IoT Central|Contagem|Total|A contagem de todas as leituras bem sucedidas iniciadas da IoT Central|Sem Dimensões|
|c2d.property.update.failure|Sim|Atualizações de propriedade de dispositivos falhados da IoT Central|Contagem|Total|A contagem de todas as atualizações de propriedade falhadas iniciadas a partir da IoT Central|Sem Dimensões|
|c2d.property.update.success|Sim|Atualizações de propriedade de dispositivos bem-sucedidos da IoT Central|Contagem|Total|A contagem de todas as atualizações de propriedade bem sucedidas iniciadas a partir da IoT Central|Sem Dimensões|
|connectedDeviceCount|Não|Total de dispositivos conectados|Contagem|Média|Número de dispositivos ligados à IoT Central|Sem Dimensões|
|d2c.property.read.failure|Sim|Falha na propriedade do dispositivo lê-se a partir de dispositivos|Contagem|Total|A contagem de todas as leituras de propriedade falhada iniciadas a partir de dispositivos|Sem Dimensões|
|d2c.property.read.success|Sim|Bem sucedida propriedade do dispositivo lê de dispositivos|Contagem|Total|A contagem de todas as leituras bem sucedidas iniciadas a partir de dispositivos|Sem Dimensões|
|d2c.property.update.failure|Sim|Falhas nas atualizações de propriedade do dispositivo a partir de dispositivos|Contagem|Total|A contagem de todas as atualizações de propriedade falhadas iniciadas a partir de dispositivos|Sem Dimensões|
|d2c.property.update.success|Sim|Atualizações de propriedade de dispositivos bem-sucedidos a partir de dispositivos|Contagem|Total|A contagem de todas as atualizações de propriedade bem sucedidas iniciadas a partir de dispositivos|Sem Dimensões|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/cofres

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade geral do cofre|Percentagem|Média|Abóbada solicita disponibilidade|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturaçãoShoebox|Não|Saturação geral do cofre|Percentagem|Média|Capacidade do cofre utilizada|ActivityType, ActivityName, TransactionType|
|ServiceApiHit|Sim|Acessos Api de serviço total|Contagem|Contagem|Número de acessos totais de serviço api|ActivityType, ActivityName|
|ServiçoApilatency|Sim|Latência geral do serviço Api|Milissegundos|Média|Latência geral dos pedidos de serviço api|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Sim|Resultados totais do serviço Api|Contagem|Contagem|Número de resultados totais da API de serviço|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|LotBlobCount|Sim|Contagem de Blob de Lote|Contagem|Média|Número de fontes de dados num lote agregado para ingestão.|Base de Dados|
|BatchDuration|Sim|Duração do lote|Segundos|Média|A duração da fase de agregação no fluxo de ingestão.|Base de Dados|
|Lotes Processados|Sim|Lotes processados|Contagem|Média|Número de lotes agregados para ingestão. Razão de conclusão do lote: Se o lote atingiu o tempo de loteamento, o tamanho dos dados ou o número de ficheiros limite definidos pela política de loteamento|Base de dados, SealReason|
|LotSize|Sim|Tamanho do lote|Bytes|Média|Tamanho de dados não comprimido esperado num lote agregado para ingestão.|Base de Dados|
|CacheUtilização|Sim|Utilização de cache|Percentagem|Média|Nível de utilização no âmbito do cluster|Sem Dimensões|
|ContinuaExportMaxLatenessMinutes|Sim|Exportação Contínua Max Lateness|Contagem|Máximo|O atraso (em minutos) reportado pelos postos de trabalho contínuos de exportação no cluster|Sem Dimensões|
|ContinuousExportNumOfRecordsExported|Sim|Exportação contínua - num de registos exportados|Contagem|Total|Número de registos exportados, disparados para cada artefacto de armazenamento escrito durante a operação de exportação|Nome, Base de Dados ContínuaExport|
|ContínuaExportPendingCount|Sim|Contagem pendente de exportação contínua|Contagem|Máximo|O número de postos de trabalho de exportação permanentes pendentes prontos para a execução|Sem Dimensões|
|ContínuaExportResult|Sim|Resultado contínuo da exportação|Contagem|Contagem|Indica se a Exportação Contínua foi bem sucedida ou falhou|OnlineExport Nome, Resultado, Base de Dados|
|CPU|Sim|CPU|Percentagem|Média|Nível de utilização do CPU|Sem Dimensões|
|EventosProcessadoForEventHubs|Sim|Eventos processados (para Centros de Eventos/IoT)|Contagem|Total|Número de eventos processados pelo cluster ao ingerir do Event/IoT Hub|EventStatus|
|ExportaçãoUtilização|Sim|Utilização da Exportação|Percentagem|Máximo|Utilização das exportações|Sem Dimensões|
|IngestionLatencyInSeconds|Sim|Latência da ingestão (em segundos)|Segundos|Média|Tempo de ingestão da fonte (por exemplo, a mensagem está no EventHub) para o cluster em segundos|Sem Dimensões|
|IngestionResult|Sim|Resultado da ingestão|Contagem|Contagem|Número de operações de ingestão|IngestionResultDetails|
|Ingestionutilização|Sim|Utilização da ingestão|Percentagem|Média|Relação das ranhuras de ingestão usadas no cluster|Sem Dimensões|
|IngestionVolumeInMB|Sim|Volume de ingestão (em MB)|Contagem|Total|Volume global de dados ingeridos para o cluster (em MB)|Sem Dimensões|
|InstânciaCount|Sim|Contagem de Instâncias|Contagem|Média|Contagem total de casos|Sem Dimensões|
|KeepAlive|Sim|Mantenha-se vivo|Contagem|Média|Verificação de sanidade indica que o cluster responde a consultas|Sem Dimensões|
|Queriaduração|Sim|Duração da consulta|Milissegundos|Média|Duração das consultas em segundos|QueriaStatus|
|SteamingIngestRequestRate|Sim|Velocidade dos Pedidos na Ingestão de Transmissão em Fluxo|Contagem|RateRequestsPerSecond|Taxa de pedido de ingestão de streaming (pedidos por segundo)|Sem Dimensões|
|StreamingIngestDataRate|Sim|Velocidade dos Dados na Ingestão de Transmissão em Fluxo|Contagem|Média|Taxa de dados de ingestão de streaming (MB por segundo)|Sem Dimensões|
|StreamingIngestDuration|Sim|Duração da Ingestão de Transmissão em Fluxo|Milissegundos|Média|Duração da ingestão de streaming em milissegundos|Sem Dimensões|
|StreamingIngestResults|Sim|Resultado da Ingestão de Transmissão em Fluxo|Contagem|Média|Resultado do streaming de ingerir|Resultado|
|TotalNumberOfConcurrentQueries|Sim|Número total de consultas simultâneas|Contagem|Total|Número total de consultas simultâneas|Sem Dimensões|
|TotalNumberOfExtents|Sim|Número total de extensões|Contagem|Total|Número total de extensões de dados|Sem Dimensões|
|TotalNumberOfThrottledCommands|Sim|Número total de comandos acelerados|Contagem|Total|Número total de comandos acelerados|Tipo de Comando|
|TotalNumberOfThrottledQueries|Sim|Número total de consultas estranguladas|Contagem|Total|Número total de consultas estranguladas|Sem Dimensões|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Ação|Sim|Latência de Ação |Segundos|Média|Latência de ações de fluxo de trabalho concluídas.|Sem Dimensões|
|Ações Computação|Sim|Ações Concluídas |Contagem|Total|Número de ações de fluxo de trabalho concluídas.|Sem Dimensões|
|Ações Destruídas|Sim|Ações falhadas |Contagem|Total|O número de ações de fluxo de trabalho falhou.|Sem Dimensões|
|AçõesSkipped|Sim|Ações ignoradas |Contagem|Total|Número de ações de fluxo de trabalho ignoradas.|Sem Dimensões|
|AçõesStarted|Sim|Ações Iniciadas |Contagem|Total|Número de ações de fluxo de trabalho iniciadas.|Sem Dimensões|
|AçõesDuzidas|Sim|Ações Bem Sucedidas |Contagem|Total|Número de ações de fluxo de trabalho conseguiu.|Sem Dimensões|
|ActionSuccessLatency|Sim|Latência do sucesso da ação |Segundos|Média|Latência de ações de fluxo de trabalho bem sucedidas.|Sem Dimensões|
|ActionThrottledEvents|Sim|Eventos throttled de ação|Contagem|Total|Número de eventos de fluxo de trabalho acelerado.|Sem Dimensões|
|Integração ServiçoEnvironmentConnectorMemoryUsage|Sim|Utilização da memória do conector para ambiente de serviço de integração|Percentagem|Média|Utilização da memória do conector para ambiente de serviço de integração.|Sem Dimensões|
|Integração ServiçoEnvironmentConnectorProcessorUsage|Sim|Utilização do processador do conector para ambiente de serviço de integração|Percentagem|Média|Utilização do processador de conector para ambiente de serviço de integração.|Sem Dimensões|
|Integração ServiçoEnvironmentOdestrabalhomor|Sim|Utilização da memória do fluxo de trabalho para ambiente de serviço de integração|Percentagem|Média|Utilização da memória do fluxo de trabalho para ambiente de serviço de integração.|Sem Dimensões|
|Integração ServiçoEnvironmentWorkflowProcessorUsage|Sim|Utilização do processador de fluxo de trabalho para ambiente de serviço de integração|Percentagem|Média|Utilização do processador de fluxo de trabalho para ambiente de serviço de integração.|Sem Dimensões|
|RunFailurePercentage|Sim|Percentagem de falha de execução|Percentagem|Total|A percentagem de fluxos de trabalho falhou.|Sem Dimensões|
|Estação Runlatency|Sim|Executar Latência|Segundos|Média|A latência do fluxo de trabalho concluído funciona.|Sem Dimensões|
|RunsCancelled|Sim|Execuções canceladas|Contagem|Total|Número de fluxos de trabalho cancelados.|Sem Dimensões|
|RunsCompleted|Sim|Execuções Concluídas|Contagem|Total|Número de fluxos de trabalho concluídos.|Sem Dimensões|
|RunsFailed|Sim|Runs Falhado|Contagem|Total|O número de fluxos de trabalho falhou.|Sem Dimensões|
|RunsStarted|Sim|Execuções iniciadas|Contagem|Total|O número de fluxos de trabalho iniciados.|Sem Dimensões|
|RunsSucceed|Sim|Runs Succeeded|Contagem|Total|Número de fluxos de trabalho conseguiu.|Sem Dimensões|
|RunStartThrottledEvents|Sim|Executar eventos throttled do início|Contagem|Total|Número de fluxos de trabalho iniciam eventos acelerados.|Sem Dimensões|
|RunSuccessLatency|Sim|Executar Latência de sucesso|Segundos|Média|A latência do fluxo de trabalho bem sucedido corre.|Sem Dimensões|
|RunThrottledEvents|Sim|Executar Eventos Throttled|Contagem|Total|Número de ação de fluxo de trabalho ou eventos acelerados.|Sem Dimensões|
|TriggerFireLatency|Sim|Desencadear a latência do fogo |Segundos|Média|Latência de gatilhos de fluxo de trabalho disparados.|Sem Dimensões|
|TriggerLatency|Sim|Desencadear a latência |Segundos|Média|Latência de gatilhos de fluxo de trabalho concluídos.|Sem Dimensões|
|GatilhosCompleted|Sim|Gatilhos Concluídos |Contagem|Total|Número de gatilhos de fluxo de trabalho concluídos.|Sem Dimensões|
|GatilhosFailed|Sim|Gatilhos falhados |Contagem|Total|O número de gatilhos de fluxo de trabalho falhou.|Sem Dimensões|
|Gatilhos Disparados|Sim|Gatilhos disparados |Contagem|Total|Número de gatilhos de fluxo de trabalho disparados.|Sem Dimensões|
|GatilhosSkipped|Sim|Gatilhos ignorados|Contagem|Total|Número de gatilhos de fluxo de trabalho ignorados.|Sem Dimensões|
|GatilhosStarted|Sim|Gatilhos Iniciados |Contagem|Total|O número de gatilhos de fluxo de trabalho começou.|Sem Dimensões|
|GatilhosSsueded|Sim|Gatilhos Bem Sucedidos |Contagem|Total|Número de gatilhos de fluxo de trabalho conseguiu.|Sem Dimensões|
|TriggerSuccessLatency|Sim|Desencadear a latência do sucesso |Segundos|Média|Latência de desencadeamentos de fluxo de trabalho bem sucedidos.|Sem Dimensões|
|TriggerThrottledEvents|Sim|Eventos aceleradores do gatilho|Contagem|Total|Número de eventos aceleradores de fluxo de trabalho.|Sem Dimensões|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Ação|Sim|Latência de Ação |Segundos|Média|Latência de ações de fluxo de trabalho concluídas.|Sem Dimensões|
|Ações Computação|Sim|Ações Concluídas |Contagem|Total|Número de ações de fluxo de trabalho concluídas.|Sem Dimensões|
|Ações Destruídas|Sim|Ações falhadas |Contagem|Total|O número de ações de fluxo de trabalho falhou.|Sem Dimensões|
|AçõesSkipped|Sim|Ações ignoradas |Contagem|Total|Número de ações de fluxo de trabalho ignoradas.|Sem Dimensões|
|AçõesStarted|Sim|Ações Iniciadas |Contagem|Total|Número de ações de fluxo de trabalho iniciadas.|Sem Dimensões|
|AçõesDuzidas|Sim|Ações Bem Sucedidas |Contagem|Total|Número de ações de fluxo de trabalho conseguiu.|Sem Dimensões|
|ActionSuccessLatency|Sim|Latência do sucesso da ação |Segundos|Média|Latência de ações de fluxo de trabalho bem sucedidas.|Sem Dimensões|
|ActionThrottledEvents|Sim|Eventos throttled de ação|Contagem|Total|Número de eventos de fluxo de trabalho acelerado.|Sem Dimensões|
|BillableActionExecutions|Sim|Execuções de ação executáveis|Contagem|Total|Número de execuções de fluxo de trabalho a ser cobrado.|Sem Dimensões|
|BillableTriggerExecutions|Sim|Execuções de gatilhos faturantes|Contagem|Total|Número de execuções de fluxo de trabalho a serem cobradas.|Sem Dimensões|
|Faturação UsageNativeOperação|Sim|Utilização de faturação para execuções de operações nativas|Contagem|Total|Número de execuções de operações nativas a ser cobrado.|Sem Dimensões|
|BillingUsageStandardConnector|Sim|Utilização de faturação para execuções padrão do conector|Contagem|Total|Número de execuções padrão de conector a ser faturado.|Sem Dimensões|
|BillingUsageStorageConsumption|Sim|Faturação de utilização para execuções de consumo de armazenamento|Contagem|Total|Número de execuções de consumo de armazenamento a ser cobrado.|Sem Dimensões|
|RunFailurePercentage|Sim|Percentagem de falha de execução|Percentagem|Total|A percentagem de fluxos de trabalho falhou.|Sem Dimensões|
|Estação Runlatency|Sim|Executar Latência|Segundos|Média|A latência do fluxo de trabalho concluído funciona.|Sem Dimensões|
|RunsCancelled|Sim|Execuções canceladas|Contagem|Total|Número de fluxos de trabalho cancelados.|Sem Dimensões|
|RunsCompleted|Sim|Execuções Concluídas|Contagem|Total|Número de fluxos de trabalho concluídos.|Sem Dimensões|
|RunsFailed|Sim|Runs Falhado|Contagem|Total|O número de fluxos de trabalho falhou.|Sem Dimensões|
|RunsStarted|Sim|Execuções iniciadas|Contagem|Total|O número de fluxos de trabalho iniciados.|Sem Dimensões|
|RunsSucceed|Sim|Runs Succeeded|Contagem|Total|Número de fluxos de trabalho conseguiu.|Sem Dimensões|
|RunStartThrottledEvents|Sim|Executar eventos throttled do início|Contagem|Total|Número de fluxos de trabalho iniciam eventos acelerados.|Sem Dimensões|
|RunSuccessLatency|Sim|Executar Latência de sucesso|Segundos|Média|A latência do fluxo de trabalho bem sucedido corre.|Sem Dimensões|
|RunThrottledEvents|Sim|Executar Eventos Throttled|Contagem|Total|Número de ação de fluxo de trabalho ou eventos acelerados.|Sem Dimensões|
|TotalBillableExecutions|Sim|Execuções totais faturantes|Contagem|Total|Número de execuções de fluxos de trabalho a ser cobrado.|Sem Dimensões|
|TriggerFireLatency|Sim|Desencadear a latência do fogo |Segundos|Média|Latência de gatilhos de fluxo de trabalho disparados.|Sem Dimensões|
|TriggerLatency|Sim|Desencadear a latência |Segundos|Média|Latência de gatilhos de fluxo de trabalho concluídos.|Sem Dimensões|
|GatilhosCompleted|Sim|Gatilhos Concluídos |Contagem|Total|Número de gatilhos de fluxo de trabalho concluídos.|Sem Dimensões|
|GatilhosFailed|Sim|Gatilhos falhados |Contagem|Total|O número de gatilhos de fluxo de trabalho falhou.|Sem Dimensões|
|Gatilhos Disparados|Sim|Gatilhos disparados |Contagem|Total|Número de gatilhos de fluxo de trabalho disparados.|Sem Dimensões|
|GatilhosSkipped|Sim|Gatilhos ignorados|Contagem|Total|Número de gatilhos de fluxo de trabalho ignorados.|Sem Dimensões|
|GatilhosStarted|Sim|Gatilhos Iniciados |Contagem|Total|O número de gatilhos de fluxo de trabalho começou.|Sem Dimensões|
|GatilhosSsueded|Sim|Gatilhos Bem Sucedidos |Contagem|Total|Número de gatilhos de fluxo de trabalho conseguiu.|Sem Dimensões|
|TriggerSuccessLatency|Sim|Desencadear a latência do sucesso |Segundos|Média|Latência de desencadeamentos de fluxo de trabalho bem sucedidos.|Sem Dimensões|
|TriggerThrottledEvents|Sim|Eventos aceleradores do gatilho|Contagem|Total|Número de eventos aceleradores de fluxo de trabalho.|Sem Dimensões|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Núcleos Ativos|Sim|Núcleos Ativos|Contagem|Média|Número de núcleos ativos|Cenário, ClusterName|
|Nóns ativos|Sim|Nóns ativos|Contagem|Média|Número de nós Acitve. Estes são os nós que estão ativamente a gerir um trabalho.|Cenário, ClusterName|
|Cancelar execuções solicitadas|Sim|Cancelar execuções solicitadas|Contagem|Total|Número de execuções onde foi solicitado o cancelamento para este espaço de trabalho. A contagem é atualizada quando o pedido de cancelamento foi recebido para uma execução.|Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Corridas Canceladas|Sim|Corridas Canceladas|Contagem|Total|Número de corridas canceladas para este espaço de trabalho. O conde é atualizado quando uma execução é cancelada com sucesso.|Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Execuções Concluídas|Sim|Execuções Concluídas|Contagem|Total|Número de runs concluídas com sucesso para este espaço de trabalho. A contagem é atualizada quando uma execução tiver sido concluída e a saída tiver sido recolhida.|Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|CpuUtilização|Sim|CpuUtilização|Contagem|Média|Percentagem de utilização da memória num nó CPU. A utilização é reportada em intervalos de um minuto.|Cenário, runId, NodeId, ClusterName|
|Erros|Sim|Erros|Contagem|Total|Número de erros de execução neste espaço de trabalho. A contagem é atualizada sempre que o run encontra um erro.|Cenário|
|Corridas falhadas|Sim|Corridas falhadas|Contagem|Total|Número de corridas falhou neste espaço de trabalho. O conde é atualizado quando uma corrida falha.|Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Finalização de Corridas|Sim|Finalização de Corridas|Contagem|Total|Número de corridas inseridas no estado finalizado para este espaço de trabalho. A contagem é atualizada quando uma execução tiver terminado, mas a recolha de saída ainda em andamento.|Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|GpuUtilização|Sim|GpuUtilização|Contagem|Média|Percentagem de utilização da memória num nó GPU. A utilização é reportada em intervalos de um minuto.|Cenário, runId, NodeId, DeviceId, ClusterName|
|Núcleos Ociosos|Sim|Núcleos Ociosos|Contagem|Média|Número de núcleos ociosos|Cenário, ClusterName|
|Nódoas Ociosas|Sim|Nódoas Ociosas|Contagem|Média|Número de nós ociosos. Os nós inativos são os nós que não estão a gerir nenhum emprego, mas que podem aceitar novos empregos se disponíveis.|Cenário, ClusterName|
|Deixando os Núcleos|Sim|Deixando os Núcleos|Contagem|Média|Número de núcleos de saída|Cenário, ClusterName|
|Deixando os nóns|Sim|Deixando os nóns|Contagem|Média|Número de nós de saída. Deixando nós são os nós que acabaram de processar um trabalho e irão para o estado de Idle.|Cenário, ClusterName|
|Implementação de modelo falhou|Sim|Implementação de modelo falhou|Contagem|Total|Número de implementações de modelos que falharam neste espaço de trabalho|Cenário, Código de Estado|
|Implementação de modelos iniciado|Sim|Implementação de modelos iniciado|Contagem|Total|Número de implementações de modelos iniciadas neste espaço de trabalho|Cenário|
|Implementação de modelos conseguiu|Sim|Implementação de modelos conseguiu|Contagem|Total|Número de implementações de modelos que foram bem sucedidas neste espaço de trabalho|Cenário|
|Registo modelo falhado|Sim|Registo modelo falhado|Contagem|Total|Número de registos de modelos que falharam neste espaço de trabalho|Cenário, Código de Estado|
|Registo modelo Bem sucedido|Sim|Registo modelo Bem sucedido|Contagem|Total|Número de registos de modelos que sucederam neste espaço de trabalho|Cenário|
|Não respondendo corre|Sim|Não respondendo corre|Contagem|Total|Número de corridas que não respondem a este espaço de trabalho. A contagem é atualizada quando uma execução entra no estado de Não Responder.|Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Não começou corre|Sim|Não começou corre|Contagem|Total|Número de runs em Estado Não Iniciado para este espaço de trabalho. A contagem é atualizada quando um pedido é recebido para criar uma execução, mas a informação de execução ainda não foi povoada. |Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Núcleos Preempted|Sim|Núcleos Preempted|Contagem|Média|Número de núcleos pré-apropriados|Cenário, ClusterName|
|Nómadas Preempted|Sim|Nómadas Preempted|Contagem|Média|Número de nós preempted. Estes nós são os nós de baixa prioridade que são retirados da piscina de nós disponíveis.|Cenário, ClusterName|
|Preparação de Corridas|Sim|Preparação de Corridas|Contagem|Total|Número de corridas que se preparam para este espaço de trabalho. A contagem é atualizada quando uma execução entra em estado de preparação enquanto o ambiente de funcionação está a ser preparado.|Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Corridas de Provisionamento|Sim|Corridas de Provisionamento|Contagem|Total|Número de execuções que estão a provisão para este espaço de trabalho. A contagem é atualizada quando uma corrida está à espera da criação ou provisão do alvo do cálculo.|Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Corridas em Fila|Sim|Corridas em Fila|Contagem|Total|Número de corridas que estão na fila para este espaço de trabalho. A contagem é atualizada quando uma corrida é em fila no alvo do cálculo. Pode ocorrer quando se espera que os nós computatados necessários estejam prontos.|Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Percentagem de Utilização de Cots|Sim|Percentagem de Utilização de Cots|Contagem|Média|Por cento das quotas utilizadas|Cenário, ClusterName, VmFamilyName, VmPriority|
|Corridas iniciadas|Sim|Corridas iniciadas|Contagem|Total|Número de corridas a correr para este espaço de trabalho. A contagem é atualizada quando a execução começa a funcionar com os recursos necessários.|Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Execuções ini 'Starting'|Sim|Execuções ini 'Starting'|Contagem|Total|Número de corridas iniciadas para este espaço de trabalho. O Conde é atualizado após pedido para criar informações de execução e execução, como o Run Id, foi povoado|Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Núcleos Totais|Sim|Núcleos Totais|Contagem|Média|Número de núcleos totais|Cenário, ClusterName|
|Nómada Total|Sim|Nómada Total|Contagem|Média|Número de nós totais. Este total inclui alguns dos nóns ativos, nóns ociosos, nóns inutilizáveis, nóns pré-identificados, deixando os nóns|Cenário, ClusterName|
|Núcleos inutilizáveis|Sim|Núcleos inutilizáveis|Contagem|Média|Número de núcleos inutilizáveis|Cenário, ClusterName|
|Nódes Inutilizáveis|Sim|Nódes Inutilizáveis|Contagem|Média|Número de nós inutilizáveis. Os nós inutilizáveis não estão funcionais devido a algum problema irresolúvel. Azure vai reciclar estes nós.|Cenário, ClusterName|
|Avisos|Sim|Avisos|Contagem|Total|Número de avisos de execução neste espaço de trabalho. A contagem é atualizada sempre que uma corrida encontra um aviso.|Cenário|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/contas

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade|Percentagem|Média|Disponibilidade das APIs|ApiCategory|
|Utilização|Não|Utilização|Contagem|Contagem|Contagem de chamadas da API|ApiCategoria, ApiName, ResultadoType, ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Conta de Ativos|Sim|Contagem de ativos|Contagem|Média|Quantos ativos já são criados na conta de serviço de mídia corrente|Sem Dimensões|
|AssetQuota|Sim|Quota de ativos|Contagem|Média|Quantos ativos são permitidos para a conta de serviço de mídia atual|Sem Dimensões|
|AssetQuotaUsedPercentage|Sim|Percentagem de quota de ativos utilizada|Percentagem|Média|Percentagem de ativos usados na conta de serviço de mídia corrente|Sem Dimensões|
|ContentKeyPolicyCount|Sim|Contagem de políticas de chave de conteúdo|Contagem|Média|Quantas políticas-chave de conteúdo já estão criadas na conta de serviço de mídia atual|Sem Dimensões|
|ContentKeyPolicyQuota|Sim|Quota de política chave de conteúdo|Contagem|Média|Quantos polícias chave de conteúdo são permitidos para a conta de serviço de mídia atual|Sem Dimensões|
|ContentKeyPolicyQuotaUsedPercentage|Sim|Quota de política de conteúdo utilizada|Percentagem|Média|Política chave de conteúdo usada percentagem na conta de serviço de mídia atual|Sem Dimensões|
|StreamingPolicyCount|Sim|Contagem de políticas de streaming|Contagem|Média|Quantas políticas de streaming já estão criadas na conta de serviço de mídia atual|Sem Dimensões|
|StreamingPolicyQuota|Sim|Quota de política de streaming|Contagem|Média|Quantas políticas de streaming são permitidas para a conta atual do serviço de mídia|Sem Dimensões|
|StreamingPolicyQuotaUsedPercentage|Sim|Percentagem utilizada pela quota de política de streaming|Percentagem|Média|Política de Streaming usada percentualmente na conta de serviço de mídia corrente|Sem Dimensões|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Saída|Sim|Saída|Bytes|Total|A quantidade de dados da Egress, em bytes.|OutputFormat|
|Pedidos|Sim|Pedidos|Contagem|Total|Pedidos para um Ponto Final de Streaming.|OutputFormat, HttpStatusCode, ErrorCode|
|SucessoE2ELatency|Sim|Fim do sucesso para acabar com a latência|Milissegundos|Média|A latência média para pedidos bem sucedidos em milissegundos.|OutputFormat|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedsize|Sim|Tamanho atribuído à piscina|Bytes|Média|Tamanho a provisionado desta piscina|Sem Dimensões|
|VolumePoolAllocatedUsed|Sim|Piscina atribuída ao tamanho do volume|Bytes|Média|Tamanho utilizado atribuído da piscina|Sem Dimensões|
|VolumePoolTotalLogicalsize|Sim|Tamanho consumido da piscina|Bytes|Média|Soma do tamanho lógico de todos os volumes pertencentes à piscina|Sem Dimensões|
|VolumePoolTotalSnapshotSize|Sim|Tamanho total do snapshot para a piscina|Bytes|Média|Soma de tamanho instantâneo de todos os volumes nesta piscina|Sem Dimensões|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Média DeOncência|Sim|Latência média de leitura|MilliSeconds|Média|Latência média de leitura em milissegundos por operação|Sem Dimensões|
|Média Desaquimia|Sim|Latência média de escrita|MilliSeconds|Média|Latência escrita média em milissegundos por operação|Sem Dimensões|
|CbsVolumeBackupActive|Sim|Estado ativo de backup de volume|Contagem|Média|Está atualmente suspenso pelo volume.|Sem Dimensões|
|CbsVolumeLogicalBackupBytes|Sim|Bytes lógicos apoiados|Bytes|Média|Toatl bytes não comprimidos/não encriptados apoiados para este Volume.|Sem Dimensões|
|CbsVolumeOperationComplete|Sim|Estado de operação|Contagem|Média|É a última operação de backup/restauro bem sucedida.|Sem Dimensões|
|CbsVolumeOperationTransferredBytes|Sim|Bytes transferidos para operação|Bytes|Média|Bytes totais transferidos para a última operação de backup/restauro.|Sem Dimensões|
|CbsVolumeProtector|Sim|Estado protegido do volume|Contagem|Média|É o volume protegido pelo serviço de backup em nuvem.|Sem Dimensões|
|ReadIops|Sim|Ler iops|CondePerSecond|Média|Ler operações de in/out por segundo|Sem Dimensões|
|VolumeAllocatedsize|Sim|Volume de tamanho atribuído|Bytes|Média|O tamanho previsto de um volume|Sem Dimensões|
|VolumeLogicalsize|Sim|Tamanho consumido do volume|Bytes|Média|Tamanho lógico do volume (bytes usados)|Sem Dimensões|
|VolumeSnapshotSize|Sim|Tamanho do instantâneo de volume|Bytes|Média|Tamanho de todos os instantâneos em volume|Sem Dimensões|
|WriteIops|Sim|Escrever iops|CondePerSecond|Média|Escrever operações de in/out por segundo|Sem Dimensões|
|XregionReplicationHealthy|Sim|É o estado de replicação do volume saudável|Contagem|Média|Condição da relação, 1 ou 0.|Sem Dimensões|
|XregionReplicationLagTime|Sim|Tempo de desfasamento da replicação do volume|Segundos|Média|A quantidade de tempo em segundos através da qual os dados no espelho ficam atrás da fonte.|Sem Dimensões|
|XregionReplicationLastTransferDuration|Sim|Duração da transferência de replicação de volume|Segundos|Média|O tempo em segundos que levou para a última transferência ser concluída.|Sem Dimensões|
|XregionReplicationLastTransfersize|Sim|Tamanho da última transferência de replicação de volume|Bytes|Média|O número total de bytes transferidos como parte da última transferência.|Sem Dimensões|
|XregionReplicationRelationshipProgress|Sim|Progresso da replicação do volume|Bytes|Média|Quantidade total de dados transferidos para a operação de transferência em curso.|Sem Dimensões|
|XregionReplicationRelationshipTransferring|Sim|É a transferência de replicação de volume|Contagem|Média|Se o estado da replicação do volume é 'transferido'.|Sem Dimensões|
|XregionReplicationTotalTransferBytes|Sim|Transferência total de replicação de volume|Bytes|Média|Bytes cumulativos transferidos para a relação.|Sem Dimensões|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|Não|Tempo total do Gateway de Aplicação|MilliSeconds|Média|O tempo médio que leva para que um pedido seja processado e a sua resposta seja enviada. Isto é calculado como média do intervalo a partir do momento em que o Application Gateway recebe o primeiro byte de um pedido HTTP ao momento em que a operação de envio de resposta termina. É importante notar que isto geralmente inclui o tempo de processamento do Gateway de Aplicação, tempo que os pacotes de pedido e resposta estão viajando através da rede e o tempo que o servidor backend demorou a responder.|Serviço de Escuta|
|AvgRequestCountPerHealthyHost|Não|Pedidos por minuto por Anfitrião Saudável|Contagem|Média|Contagem média de pedidos por minuto por hospedeiro saudável de backend em uma piscina|BackendSettingsPool|
|BackendConnectTime|Não|Tempo de ligação de backend|MilliSeconds|Média|Tempo gasto a estabelecer uma ligação com um servidor de backend|Ouvinte, BackendServer, BackendPool, BackendHttpSetting|
|BackendFirstByteResponseTime|Não|Tempo de resposta de backend First Byte|MilliSeconds|Média|Intervalo de tempo entre o início do estabelecimento de uma ligação ao servidor backend e a receção do primeiro byte do cabeçalho de resposta, aproximando o tempo de processamento do servidor backend|Ouvinte, BackendServer, BackendPool, BackendHttpSetting|
|BackendLastByteResponseTime|Não|Tempo de resposta de Backend Last Byte|MilliSeconds|Média|Intervalo de tempo entre o início do estabelecimento de uma ligação ao servidor backend e a receção do último byte do corpo de resposta|Ouvinte, BackendServer, BackendPool, BackendHttpSetting|
|BackendResponseStatus|Sim|Estado de resposta de backend|Contagem|Total|O número de códigos de resposta HTTP gerados pelos membros backend. Isto não inclui quaisquer códigos de resposta gerados pelo Gateway de Aplicação.|BackendServer, BackendPool, BackendHttpSetting, HttpStatusGroup|
|Contagem bloqueada|Sim|Firewall de aplicação web bloqueou distribuição de regras de pedidos|Contagem|Total|Firewall de aplicação web bloqueou distribuição de regras de pedidos|RuleGroup, RuleId|
|BlockedReqCount|Sim|Contagem de pedidos bloqueados de firewall de aplicação web|Contagem|Total|Firewall de aplicação web bloqueou contagem de pedidos|Sem Dimensões|
|BytesReceived|Sim|Bytes Recebidos|Bytes|Total|O número total de bytes recebidos pela Gateway de Aplicação dos clientes|Serviço de Escuta|
|BytesSent|Sim|Bytes Enviados|Bytes|Total|O número total de bytes enviados pela Porta de Aplicação aos clientes|Serviço de Escuta|
|Unidades de capacidade|Não|Unidades de Capacidade Corrente|Contagem|Média|Unidades de capacidade consumidas|Sem Dimensões|
|ClientRtt|Não|RTT cliente|MilliSeconds|Média|Tempo médio de ida e volta entre clientes e Application Gateway. Esta métrica indica quanto tempo demora a estabelecer ligações e reconhecimentos de retorno|Serviço de Escuta|
|Unidades computeuções|Não|Unidades de Computação Atuais|Contagem|Média|Unidades de computação consumidas|Sem Dimensões|
|CpuUtilização|Não|Utilização da CPU|Percentagem|Média|Utilização atual do CPU do Gateway de Aplicações|Sem Dimensões|
|Correntes Deconhecões|Sim|Conexões atuais|Contagem|Total|Contagem de ligações atuais estabelecidas com Gateway de Aplicação|Sem Dimensões|
|Vulnerabilidades estimadas da capacidade de pagamento|Não|Unidades de Capacidade Faturadas Estimadas|Contagem|Média|Unidades de capacidade estimadas que serão carregadas|Sem Dimensões|
|Requessos Falhados|Sim|Pedidos com Falhas|Contagem|Total|Contagem de pedidos falhados que a Application Gateway serviu|BackendSettingsPool|
|Unidades de Capacidade DeBilizáveis Fixas|Não|Unidades de Capacidade Faturadas Fixas|Contagem|Média|Unidades de capacidade mínima que serão carregadas|Sem Dimensões|
|HealthyHostCount|Sim|Contagem saudável do anfitrião|Contagem|Média|Número de hospedeiros saudáveis|BackendSettingsPool|
|MatchedCount|Sim|Distribuição total de regras de firewall de aplicação web|Contagem|Total|Distribuição total de regras da Web Application Firewall para o tráfego de entrada|RuleGroup, RuleId|
|NewConnectionsPerSecond|Não|Novas ligações por segundo|CondePerSecond|Média|Novas ligações por segundo estabelecidas com Application Gateway|Sem Dimensões|
|Estatísticas de Resposta|Sim|Estado de resposta|Contagem|Total|Estado de resposta http devolvido pelo Application Gateway|Grupo HttpStatus|
|Débito|Não|Débito|BytesPerSecond|Média|Número de bytes por segundo que o Gateway de Aplicação serviu|Sem Dimensões|
|TlsProtocol|Sim|Protocolo TLS do Cliente|Contagem|Total|O número de pedidos TLS e não-TLS iniciados pelo cliente que estabeleceu a ligação com o Gateway de Aplicação. Para visualizar a distribuição do protocolo TLS, filtre pela dimensão do Protocolo TLS.|Ouvinte, TlsProtocol|
|TotalRequests|Sim|Total de Pedidos|Contagem|Total|Contagem de pedidos bem sucedidos que a Application Gateway serviu|BackendSettingsPool|
|Não-saudávelHostCount|Sim|Contagem de anfitriões pouco saudável|Contagem|Média|Número de anfitriões insalubres|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AplicaçãoRuleHit|Sim|As regras de aplicação atingem a contagem|Contagem|Total|Número de vezes que as regras de aplicação foram atingidas|Estado, Razão, Protocolo|
|Dados Processados|Sim|Dados processados|Bytes|Total|Quantidade total de dados processados por esta firewall|Sem Dimensões|
|FirewallHealth|Sim|Estado de saúde de firewall|Percentagem|Média|Indica a saúde geral desta firewall|Estado, Razão|
|NetworkRuleHit|Sim|Regras de rede atingem a contagem|Contagem|Total|Número de vezes que as regras da rede foram atingidas|Estado, Razão, Protocolo|
|SNATPortutilization|Sim|Utilização do porto de SNAT|Percentagem|Média|Percentagem de portas SNAT de saída atualmente em uso|Protocolo|
|Débito|Não|Débito|BitsPerSecond|Média|Produção processada por esta firewall|Sem Dimensões|


## <a name="microsoftnetworkconnections"></a>Microsoft.Network/conexões

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Sim|BitsInPerSecond|CondePerSecond|Média|Bits que entram no Azure por segundo|Sem Dimensões|
|BitsOutPerSecond|Sim|BitsOutPerSecond|CondePerSecond|Média|Bits que saem do Azure por segundo|Sem Dimensões|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|QueryVolume|Sim|Volume de consulta|Contagem|Total|Número de consultas servidas para uma zona de DNS|Sem Dimensões|
|RecordSetCapacityUtilização|Não|Record Fixo Utilização da Capacidade|Percentagem|Máximo|Por cento da capacidade do Conjunto recorde utilizada por uma zona de DNS|Sem Dimensões|
|RecordSetCount|Sim|Contagem de recordes|Contagem|Máximo|Número de conjuntos de recordes numa zona de DNS|Sem Dimensões|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ArpAilability|Sim|Disponibilidade ARP|Percentagem|Média|Disponibilidade de ARP da MSEE para todos os pares.|PeeringType, Peer|
|BgpAilability|Sim|Disponibilidade BGP|Percentagem|Média|Disponibilidade de BGP da MSEE para todos os pares.|PeeringType, Peer|
|BitsInPerSecond|Não|BitsInPerSecond|CondePerSecond|Média|Bits que entram no Azure por segundo|Type de peering|
|BitsOutPerSecond|Não|BitsOutPerSecond|CondePerSecond|Média|Bits que saem do Azure por segundo|Type de peering|
|GlobalReachBitsInPerSecond|Não|GlobalReachBitsInPerSecond|CondePerSecond|Média|Bits que entram no Azure por segundo|PeeredCircuitsKey|
|GlobalReachBitsOutPerSecond|Não|GlobalReachBitsOutPerSecond|CondePerSecond|Média|Bits que saem do Azure por segundo|PeeredCircuitsKey|
|QosDropBitsInPerSecond|Não|DropInBitsPerSecond|CondePerSecond|Média|Ingress bits de dados caiu por segundo|Sem Dimensões|
|QosDropBitsOutPerSecond|Não|DropOutBitsPerSecond|CondePerSecond|Média|Pedaços de dados de Egress caíram por segundo|Sem Dimensões|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Sim|BitsInPerSecond|CondePerSecond|Média|Bits que entram no Azure por segundo|Sem Dimensões|
|BitsOutPerSecond|Sim|BitsOutPerSecond|CondePerSecond|Média|Bits que saem do Azure por segundo|Sem Dimensões|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|Não|BitsInPerSecond|CondePerSecond|Média|Bits que entram no Azure por segundo|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|Não|BitsOutPerSecond|CondePerSecond|Média|Bits que saem do Azure por segundo|ConnectionName|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Estado Administrativo|Sim|Estado Administrativo|Contagem|Média|Estado administrativo do porto|Ligação|
|LineProtocol|Sim|LineProtocol|Contagem|Média|Estado do protocolo de linha da porta|Ligação|
|PortBitsInPerSecond|Sim|BitsInPerSecond|CondePerSecond|Média|Bits que entram no Azure por segundo|Ligação|
|PortBitsOutPerSecond|Sim|BitsOutPerSecond|CondePerSecond|Média|Bits que saem do Azure por segundo|Ligação|
|RxLightLevel|Sim|RxLightLevel|Contagem|Média|Nível de luz Rx em dBm|Link|
|TxLightLevel|Sim|TxLightLevel|Contagem|Média|Nível de luz Tx em dBm|Link|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|Sim|Percentagem de Saúde backend|Percentagem|Média|A percentagem de sondas de saúde bem sucedidas do representante http/s para backends|Backend|
|BackendRequestCount|Sim|Contagem de pedidos de backend|Contagem|Total|O número de pedidos enviados do representante http/S para backends|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestatency|Sim|Pedido de backend Latência|MilliSeconds|Média|O tempo calculado a partir do momento em que o pedido foi enviado pelo representante HTTP/S para o backend até que o representante HTTP/S recebeu o último byte de resposta a partir do backend|Back-end|
|BillableResponseSize|Sim|Tamanho da resposta faturada|Bytes|Total|O número de bytes faturantes (mínimo 2KB por pedido) enviados como respostas do proxy HTTP/S aos clientes.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|PedidoCount|Sim|Número de Pedidos|Contagem|Total|O número de pedidos de clientes servidos pelo representante http/s|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|Solicitação|Sim|Tamanho do pedido|Bytes|Total|O número de bytes enviados como pedidos de clientes para o proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|Tamanho das respostas|Sim|Tamanho da resposta|Bytes|Total|O número de bytes enviados como respostas de http/S proxy aos clientes|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency|Sim|Latência total|MilliSeconds|Média|O tempo calculado a partir do momento em que o pedido do cliente foi recebido pelo representante HTTP/S até que o cliente reconhecesse o último byte de resposta do representante HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|Sim|Contagem de pedidos de firewall de aplicação web|Contagem|Total|O número de pedidos de cliente processados pela Firewall de Aplicação Web|Nome de Política, Nome de Regras, Ação|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AlocadosSnatPorts|Não|Portos SNAT atribuídos|Contagem|Média|Número total de portas SNAT atribuídas dentro do período de tempo|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|ByteCount|Sim|Byte Count|Contagem|Total|Número total de Bytes transmitidos dentro do período de tempo|FrontendIPAddress, FrontendPort, Direção|
|DipDUilability|Sim|Estado da Sonda de Estado de Funcionamento|Contagem|Média|Estado médio da sonda de saúde do balanceador de carga por duração do tempo|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|PacoteCount|Sim|Contagem de Pacotes|Contagem|Total|Número total de Pacotes transmitidos dentro do período de tempo|FrontendIPAddress, FrontendPort, Direção|
|SnatConnectionCount|Sim|Contagem de ligação SNAT|Contagem|Total|Número total de novas ligações SNAT criadas dentro do período de tempo|FrontendIPAddress, BackendIPAddress, ConnectionState|
|SYNCount|Sim|Contagem de SINA|Contagem|Total|Número total de pacotes SYN transmitidos dentro do período de tempo|FrontendIPAddress, FrontendPort, Direção|
|UssnatPorts|Não|Portas SNAT usadas|Contagem|Média|Número total de portas SNAT utilizadas dentro do período de tempo|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|VipSAdability|Sim|Disponibilidade do Caminho de Dados|Contagem|Média|Disponibilidade média de trajetória do balanceador de carga por duração do tempo|FrontendIPAddress, FrontendPort|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|BytesReceivedRate|Sim|Bytes Recebidos|Bytes|Total|Número de bytes que a Interface de Rede recebeu|Sem Dimensões|
|BytesSentRate|Sim|Bytes Enviados|Bytes|Total|Número de bytes enviados pela Interface de Rede|Sem Dimensões|
|PacketsReceivedRate|Sim|Pacotes Recebidos|Contagem|Total|Número de pacotes que a Interface de Rede recebeu|Sem Dimensões|
|PacotesSEntRate|Sim|Pacotes Enviados|Contagem|Total|Número de pacotes enviados pela Interface de Rede|Sem Dimensões|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Médias Desvelundos|Sim|Avg. Tempo de ida e volta (ms)|MilliSeconds|Média|Tempo médio de ida e volta (ms) para sondas de monitorização da conectividade enviadas entre a fonte e o destino|Sem Dimensões|
|ChequesFailedPercent|Sim|Cheques Falhados Por cento (Pré-visualização)|Percentagem|Média|% dos controlos de monitorização da conectividade falharam|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|
|ProbesFailedPercent|Sim|% Sondas Falhadas|Percentagem|Média|% das sondas de monitorização da conectividade falharam|Sem Dimensões|
|RoundTripTimeMs|Sim|Round-Trip Tempo (ms) (Visualização)|MilliSeconds|Média|Tempo de ida e volta em milissegundos para os controlos de conectividade|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ByteCount|Sim|Byte Count|Contagem|Total|Número total de Bytes transmitidos dentro do período de tempo|Porto, Direção|
|BytesDroppedDDoS|Sim|Bytes de entrada deixaram cair DDoS|BytesPerSecond|Máximo|Bytes de entrada deixaram cair DDoS|Sem Dimensões|
|BytesForwardedDDoS|Sim|Bytes de entrada reencaminhado DDoS|BytesPerSecond|Máximo|Bytes de entrada reencaminhado DDoS|Sem Dimensões|
|BytesInDDoS|Sim|Bytes de entrada DDoS|BytesPerSecond|Máximo|Bytes de entrada DDoS|Sem Dimensões|
|DDoSTriggerSYNPackets|Sim|Pacotes SYN de entrada para desencadear a mitigação do DDoS|CondePerSecond|Máximo|Pacotes SYN de entrada para desencadear a mitigação do DDoS|Sem Dimensões|
|Pacotes DDoSTriggerTCP|Sim|Pacotes TCP de entrada para desencadear mitigação do DDoS|CondePerSecond|Máximo|Pacotes TCP de entrada para desencadear mitigação do DDoS|Sem Dimensões|
|DDoSTriggerUDPPackets|Sim|Pacotes UDP de entrada para desencadear mitigação do DDoS|CondePerSecond|Máximo|Pacotes UDP de entrada para desencadear mitigação do DDoS|Sem Dimensões|
|IfUnderDDosAttack|Sim|Sob o ataque do DDoS ou não|Contagem|Máximo|Sob o ataque do DDoS ou não|Sem Dimensões|
|PacoteCount|Sim|Contagem de Pacotes|Contagem|Total|Número total de Pacotes transmitidos dentro do período de tempo|Porto, Direção|
|PacotesDroppedDDoS|Sim|Pacotes de entrada deixaram cair DDoS|CondePerSecond|Máximo|Pacotes de entrada deixaram cair DDoS|Sem Dimensões|
|PacotesForwardedDDoS|Sim|Pacotes de entrada reencaminhados DDoS|CondePerSecond|Máximo|Pacotes de entrada reencaminhados DDoS|Sem Dimensões|
|PacotesInDDoS|Sim|Pacotes de entrada DDoS|CondePerSecond|Máximo|Pacotes de entrada DDoS|Sem Dimensões|
|SynCount|Sim|Contagem de SINA|Contagem|Total|Número total de pacotes SYN transmitidos dentro do período de tempo|Porto, Direção|
|TCPBytesDroppedDDoS|Sim|Bytes TCP de entrada deixaram cair DDoS|BytesPerSecond|Máximo|Bytes TCP de entrada deixaram cair DDoS|Sem Dimensões|
|TCPBytesForwardedDDoS|Sim|Inbound TCP bytes reencaminhado DDoS|BytesPerSecond|Máximo|Inbound TCP bytes reencaminhado DDoS|Sem Dimensões|
|TCPBytesInDDoS|Sim|Entrada TCP bytes DDoS|BytesPerSecond|Máximo|Entrada TCP bytes DDoS|Sem Dimensões|
|TCPPacketsDroppedDDoS|Sim|Pacotes TCP de entrada deixaram cair DDoS|CondePerSecond|Máximo|Pacotes TCP de entrada deixaram cair DDoS|Sem Dimensões|
|TCPPacketsForwardedDDoS|Sim|Pacotes TCP de entrada reencaminhados DDoS|CondePerSecond|Máximo|Pacotes TCP de entrada reencaminhados DDoS|Sem Dimensões|
|TCPPacketsInDDoS|Sim|Pacotes TCP de entrada DDoS|CondePerSecond|Máximo|Pacotes TCP de entrada DDoS|Sem Dimensões|
|UDPBytesDroppedDDoS|Sim|Bytes udp de entrada deixaram cair DDoS|BytesPerSecond|Máximo|Bytes udp de entrada deixaram cair DDoS|Sem Dimensões|
|UDPBytesForwardedDDoS|Sim|Bytes de UDP de entrada reencaminhados DDoS|BytesPerSecond|Máximo|Bytes de UDP de entrada reencaminhados DDoS|Sem Dimensões|
|UDPBytesInDDoS|Sim|UDP bytes DDoS de entrada|BytesPerSecond|Máximo|UDP bytes DDoS de entrada|Sem Dimensões|
|UDPPacketsDroppedDDoS|Sim|Pacotes UDP de entrada deixaram cair DDoS|CondePerSecond|Máximo|Pacotes UDP de entrada deixaram cair DDoS|Sem Dimensões|
|UDPPacketsForwardedDDoS|Sim|Pacotes UDP de entrada reencaminhados DDoS|CondePerSecond|Máximo|Pacotes UDP de entrada reencaminhados DDoS|Sem Dimensões|
|UDPPacketsInDDoS|Sim|Pacotes de UDP de entrada DDoS|CondePerSecond|Máximo|Pacotes de UDP de entrada DDoS|Sem Dimensões|
|VipSAdability|Sim|Disponibilidade do Caminho de Dados|Contagem|Média|Disponibilidade média de endereço IP por duração de tempo|Porta|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Sim|Estado do ponto final por Endpoint|Contagem|Máximo|1 se o estado da sonda de um ponto final for "Ativado", 0 caso contrário.|Nome de ponto final|
|QpsByEndpoint|Sim|Consultas por Endpoint Devolvido|Contagem|Total|Número de vezes que um ponto final do Gestor de Tráfego foi devolvido no prazo dado|Nome de ponto final|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Largura média da largura|Sim|Largura de banda Gateway S2S|BytesPerSecond|Média|Largura de banda média local-local de um gateway in bytes por segundo|Sem Dimensões|
|P2SBandwidth|Sim|Largura de banda Gateway P2S|BytesPerSecond|Média|Largura de banda média ponto-a-local de uma porta de entrada em bytes por segundo|Sem Dimensões|
|P2SConnectionCount|Sim|Contagem de Ligações P2S|Contagem|Máximo|Contagem de ligação ponto a site de um gateway|Protocolo|
|TunnelAverageBandwidth|Sim|Largura de Banda do Túnel|BytesPerSecond|Média|Largura de banda média de um túnel em bytes por segundo|Nome de conexão, RemoteIP|
|TunnelEgressBytes|Sim|Bytes de Saída do Túnel|Bytes|Total|Bytes de saída de um túnel|Nome de conexão, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Sim|Remoção do Pacote de Erro de Correspondência de TS de Saída do Túnel|Contagem|Total|Contagem de remoção do pacote de saída a partir do erro de correspondência do seletor de tráfego de um túnel|Nome de conexão, RemoteIP|
|TunnelEgressPackets|Sim|Pacotes de Saída do Túnel|Contagem|Total|Contagem de pacotes de saída de um túnel|Nome de conexão, RemoteIP|
|TúnelIngsBytes|Sim|Bytes de Entrada do Túnel|Bytes|Total|Bytes de entrada de um túnel|Nome de conexão, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Sim|Remoção do Pacote de Erro de Correspondência de TS de Entrada do Túnel|Contagem|Total|Contagem de remoção do pacote de entrada a partir do erro de correspondência do seletor de tráfego de um túnel|Nome de conexão, RemoteIP|
|Pacotes de escavação|Sim|Pacotes de Entrada de Túnel|Contagem|Total|Contagem de pacotes de entrada de um túnel|Nome de conexão, RemoteIP|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Sim|Hora de ida e volta para Pings a um VM|MilliSeconds|Média|Tempo de ida e volta para Pings enviado para um VM de destino|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Sim|Pings falhados para um VM|Percentagem|Média|Por cento do número de pings falhados para o total enviado Pings de um VM destino|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|entrada|Sim|Mensagens Recebidas|Contagem|Total|A contagem de todos os bem sucedidos enviar chamadas da API. |Sem Dimensões|
|incoming.all.failedrequests|Sim|Todos os pedidos falhados de entrada|Contagem|Total|Total de pedidos falhados de entrada falhada para um centro de notificação|Sem Dimensões|
|incoming.all.requests|Sim|Todos os Pedidos de Entrada|Contagem|Total|Total de pedidos de entrada para um centro de notificação|Sem Dimensões|
|entrada.programado|Sim|Notificações de push agendadas enviadas|Contagem|Total|Notificações de push programadas canceladas|Sem Dimensões|
|incoming.scheduled.cancel|Sim|Notificações de push programadas canceladas|Contagem|Total|Notificações de push programadas canceladas|Sem Dimensões|
|instalação.todos|Sim|Operações de Gestão de Instalações|Contagem|Total|Operações de Gestão de Instalações|Sem Dimensões|
|instalação.excluir|Sim|Excluir operações de instalação|Contagem|Total|Excluir operações de instalação|Sem Dimensões|
|instalação.get|Sim|Obter Operações de Instalação|Contagem|Total|Obter Operações de Instalação|Sem Dimensões|
|instalação.patch|Sim|Operações de instalação de remendos|Contagem|Total|Operações de instalação de remendos|Sem Dimensões|
|instalação.upsert|Sim|Criar ou atualizar operações de instalação|Contagem|Total|Criar ou atualizar operações de instalação|Sem Dimensões|
|notificationhub.pushes|Sim|Todas as notificações de saída|Contagem|Total|Todas as notificações de saída do centro de notificação|Sem Dimensões|
|outgoing.allpns.badorexpiredchannel|Sim|Erros do Canal Mau ou Expirados|Contagem|Total|A contagem de impulsos que falharam porque o canal/token/registrationId no registo expirou ou inválido.|Sem Dimensões|
|outgoing.allpns.channelerror|Sim|Erros do Canal|Contagem|Total|A contagem de impulsos que falharam porque o canal era inválido não estava associado à aplicação correta estrangulada ou expirada.|Sem Dimensões|
|outgoing.allpns.invalidpayload|Sim|Erros de carga útil|Contagem|Total|A contagem de impulsos que falharam porque o PNS devolveu um erro de carga útil.|Sem Dimensões|
|outgoing.allpns.pnserror|Sim|Erros do sistema de notificação externa|Contagem|Total|A contagem de impulsos que falharam porque havia um problema de comunicação com o PNS (exclui problemas de autenticação).|Sem Dimensões|
|outgoing.allpns.sucesso|Sim|Notificações bem sucedidas|Contagem|Total|A contagem de todas as notificações bem sucedidas.|Sem Dimensões|
|outgoing.apns.badchannel|Sim|Erro do Canal Mau da APNS|Contagem|Total|A contagem de impulsos que falharam porque o token é inválido (código de estado DA APNS: 8).|Sem Dimensões|
|outgoing.apns.expiredchannel|Sim|Erro do Canal caducado da APNS|Contagem|Total|A contagem de fichas que foram invalidadas pelo canal de feedback da APNS.|Sem Dimensões|
|outgoing.apns.invalidcredentis|Sim|Erros de Autorização da APNS|Contagem|Total|A contagem de impulsos que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Sem Dimensões|
|outgoing.apns.invalidnotificationsize|Sim|Erro de tamanho de notificação inválido da APNS|Contagem|Total|A contagem de impulsos que falharam porque a carga era demasiado grande (código de estado DA APNS: 7).|Sem Dimensões|
|outgoing.apns.pnserror|Sim|Erros da APNS|Contagem|Total|A contagem de impulsos que falhou devido a erros de comunicação com a APNS.|Sem Dimensões|
|outgoing.apns.sucesso|Sim|Notificações bem sucedidas da APNS|Contagem|Total|A contagem de todas as notificações bem sucedidas.|Sem Dimensões|
|outgoing.gcm.authenticationeror|Sim|Erros de autenticação GCM|Contagem|Total|A contagem de impulsos que falharam porque o PNS não aceitou as credenciais fornecidas as credenciais estão bloqueadas ou o SenderId não está corretamente configurado na app (resultado GCM: MismatchedSenderId).|Sem Dimensões|
|outgoing.gcm.badchannel|Sim|Erro do Canal Mau gCM|Contagem|Total|A contagem de impulsos que falhou porque o registoId no registo não foi reconhecido (resultado GCM: Registo Inválido).|Sem Dimensões|
|outgoing.gcm.expiredchannel|Sim|Erro do canal expirado GCM|Contagem|Total|A contagem de impulsos que falharam porque o registoId no registo expirou (resultado GCM: Não Registado).|Sem Dimensões|
|outgoing.gcm.invalidcredentis|Sim|Erros de autorização GCM (Credenciais Inválidas)|Contagem|Total|A contagem de impulsos que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Sem Dimensões|
|outgoing.gcm.invalidnotificationformat|Sim|Formato de notificação inválida GCM|Contagem|Total|A contagem de impulsos que falharam porque a carga não foi formatada corretamente (resultado GCM: InvalidDataKey ou InvalidTtl).|Sem Dimensões|
|outgoing.gcm.invalidnotificationsize|Sim|Erro de tamanho de notificação inválido GCM|Contagem|Total|A contagem de impulsos que falharam porque a carga era demasiado grande (resultado GCM: MessageTooBig).|Sem Dimensões|
|outgoing.gcm.pnserror|Sim|Erros do GCM|Contagem|Total|A contagem de impulsos que falhou devido a erros de comunicação com gCM.|Sem Dimensões|
|outgoing.gcm.sucesso|Sim|Notificações bem sucedidas da GCM|Contagem|Total|A contagem de todas as notificações bem sucedidas.|Sem Dimensões|
|outgoing.gcm.throttled|Sim|Notificações throttled GCM|Contagem|Total|A contagem de impulsos que falharam porque a GCM estrangulou esta aplicação (código de estado GCM: 501-599 ou resultado:Indisponível).|Sem Dimensões|
|outgoing.gcm.canalil errado|Sim|Erro do canal errado gCM|Contagem|Total|A contagem de impulsos que falharam porque o registroId no registo não está associado à aplicação atual (resultado GCM: InvalidPackageName).|Sem Dimensões|
|outgoing.mpns.authenticationerror|Sim|Erros de Autenticação MPNS|Contagem|Total|A contagem de impulsos que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Sem Dimensões|
|outgoing.mpns.badchannel|Sim|Erro do Canal Mau da MPNS|Contagem|Total|A contagem de impulsos que falhou porque o ChannelURI no registo não foi reconhecido (estatuto MPNS: 404 não encontrado).|Sem Dimensões|
|outgoing.mpns.channeldisconnected|Sim|Canal MPNS desligado|Contagem|Total|A contagem de impulsos que falhou porque o ChannelURI no registo foi desligado (estado MPNS: 412 não encontrado).|Sem Dimensões|
|outgoing.mpns.drop|Sim|Notificações retiradas do MPNS|Contagem|Total|A contagem de impulsos que foram deixados por MPNS (cabeçalho de resposta MPNS: X-NotificationStatus: QueueFull ou Suprimido).|Sem Dimensões|
|outgoing.mpns.invalidcredentis|Sim|Credenciais inválidas da MPNS|Contagem|Total|A contagem de impulsos que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Sem Dimensões|
|outgoing.mpns.invalidnotificationformat|Sim|Formato de Notificação Inválida mpns|Contagem|Total|A contagem de impulsos que falharam porque a carga útil da notificação era demasiado grande.|Sem Dimensões|
|outgoing.mpns.pnserror|Sim|Erros do MPNS|Contagem|Total|A contagem de impulsos que falhou devido a erros de comunicação com MPNS.|Sem Dimensões|
|outgoing.mpns.sucesso|Sim|Notificações bem sucedidas da MPNS|Contagem|Total|A contagem de todas as notificações bem sucedidas.|Sem Dimensões|
|outgoing.mpns.throttled|Sim|Notificações throttled MPNS|Contagem|Total|A contagem de impulsos que falhou porque a MPNS está a estrangular esta aplicação (WNS MPNS: 406 Não Aceitável).|Sem Dimensões|
|outgoing.wns.authenticationerror|Sim|Erros de autenticação WNS|Contagem|Total|Notificação não entregue devido a erros de comunicação com credenciais inválidas do Windows Live ou token errado.|Sem Dimensões|
|outgoing.wns.badchannel|Sim|Erro do canal WNS|Contagem|Total|A contagem de impulsos que falharam porque o ChannelURI no registo não foi reconhecido (estatuto WNS: 404 não encontrado).|Sem Dimensões|
|outgoing.wns.channeldisconnected|Sim|Canal WNS desligado|Contagem|Total|A notificação foi retirada porque o ChannelURI no registo é acelerado (cabeçalho de resposta WNS: X-WNS-DeviceConnectionStatus: desligado).|Sem Dimensões|
|outgoing.wns.channelthrottled|Sim|Canal WNS Acelerado|Contagem|Total|A notificação foi retirada porque o ChannelURI no registo é acelerado (cabeçalho de resposta WNS: X-WNS-NotificationStatus:channelThrottled).|Sem Dimensões|
|outgoing.wns.drop|Sim|Notificações retiradas da WNS|Contagem|Total|A notificação foi retirada porque o ChannelURI no registo é acelerado (X-WNS-NotificationStatus: caiu, mas não X-WNS-DeviceConnectionStatus: desligado).|Sem Dimensões|
|outgoing.wns.expiredchannel|Sim|Erro do canal expirado da WNS|Contagem|Total|A contagem de impulsos que falharam porque o ChannelURI expirou (estado WNS: 410 Gone).|Sem Dimensões|
|outgoing.wns.invalidcredentis|Sim|Erros de autorização WNS (Credenciais Inválidas)|Contagem|Total|A contagem de impulsos que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas. (O Windows Live não reconhece as credenciais).|Sem Dimensões|
|outgoing.wns.invalidnotificationformat|Sim|Formato de notificação inválida da WNS|Contagem|Total|O formato da notificação é inválido (estado WNS: 400). Note que a WNS não rejeita todas as cargas inválidas.|Sem Dimensões|
|outgoing.wns.invalidnotificationsize|Sim|Erro de tamanho de notificação inválido da WNS|Contagem|Total|A carga útil da notificação é demasiado grande (estado WNS: 413).|Sem Dimensões|
|outgoing.wns.invalidtoken|Sim|Erros de autorização da WNS (token inválido)|Contagem|Total|O token fornecido à WNS não é válido (estado WNS: 401 Não Autorizado).|Sem Dimensões|
|outgoing.wns.pnserror|Sim|Erros do WNS|Contagem|Total|Notificação não entregue devido a erros de comunicação com a WNS.|Sem Dimensões|
|outgoing.wns.sucesso|Sim|Notificações bem sucedidas da WNS|Contagem|Total|A contagem de todas as notificações bem sucedidas.|Sem Dimensões|
|outgoing.wns.throttled|Sim|Notificações throttled WNS|Contagem|Total|A contagem de impulsos que falharam porque a WNS está a estrangular esta aplicação (estado WNS: 406 Não Aceitável).|Sem Dimensões|
|outgoing.wns.tokenproviderunreachable|Sim|Erros de autorização WNS (inacessíveis)|Contagem|Total|O Windows Live não é acessível.|Sem Dimensões|
|outgoing.wns.wrongtoken|Sim|Erros de autorização da WNS (Ficha Errada)|Contagem|Total|O token fornecido à WNS é válido, mas para outra aplicação (estatuto WNS: 403 Proibido). Isto pode acontecer se o ChannelURI no registo estiver associado a outra aplicação. Verifique se a aplicação do cliente está associada à mesma app cujas credenciais estão no centro de notificação.|Sem Dimensões|
|registro.todos|Sim|Operações de Registo|Contagem|Total|A contagem de todas as operações de registo bem sucedidas (criações atualiza consultas e supressões). |Sem Dimensões|
|registration.create|Sim|Criar Operações de Criação de Registos|Contagem|Total|A contagem de todas as criações de registo bem sucedidas.|Sem Dimensões|
|registration.delete|Sim|Operações de Eliminação de Registos|Contagem|Total|A contagem de todas as supressões de registo bem sucedidas.|Sem Dimensões|
|registration.get|Sim|Operações de Leitura de Registo|Contagem|Total|A contagem de todas as consultas de registo bem sucedidas.|Sem Dimensões|
|registration.update|Sim|Operações de Atualização de Registo|Contagem|Total|A contagem de todas as atualizações de registo bem sucedidas.|Sem Dimensões|
|agendado.pendente|Sim|Notificações Agendadas Pendentes|Contagem|Total|Notificações Agendadas Pendentes|Sem Dimensões|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|memória Average_% disponível|Sim|% Memória Disponível|Contagem|Média|memória Average_% disponível|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|espaço de troca disponível Average_%|Sim|% Espaço de troca disponível|Contagem|Média|espaço de troca disponível Average_%|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% bytes comprometidos em uso|Sim|% Bytes comprometidos em uso|Contagem|Média|Average_% bytes comprometidos em uso|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% tempo do DPC|Sim|% tempo DPC|Contagem|Média|Average_% tempo do DPC|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Inodes Average_% Grátis|Sim|% Inodes grátis|Contagem|Média|Inodes Average_% Grátis|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% Espaço Livre|Sim|% Espaço Livre|Contagem|Média|Average_% Espaço Livre|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% tempo inativo|Sim|% de Tempo Inativo|Contagem|Média|Average_% tempo inativo|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% Tempo de interrupção|Sim|% Tempo de interrupção|Contagem|Média|Average_% Tempo de interrupção|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% IO Tempo de espera|Sim|% IO Tempo de Espera|Contagem|Média|Average_% IO Tempo de espera|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% tempo agradável|Sim|% Tempo agradável|Contagem|Média|Average_% tempo agradável|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% Tempo Privilegiado|Sim|% Tempo Privilegiado|Contagem|Média|Average_% Tempo Privilegiado|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|tempo de processador Average_%|Sim|% de Tempo do Processador|Contagem|Média|tempo de processador Average_%|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|inodes Average_% usados|Sim|% Inodes usados|Contagem|Média|inodes Average_% usados|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% Memória Usada|Sim|% Memória Usada|Contagem|Média|Average_% Memória Usada|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% espaço usado|Sim|% Espaço Usado|Contagem|Média|Average_% espaço usado|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% espaço de troca usado|Sim|% Espaço de troca usado|Contagem|Média|Average_% espaço de troca usado|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% tempo de utilização|Sim|% Tempo de Utilização|Contagem|Média|Average_% tempo de utilização|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Available MBytes|Sim|MBytes Disponíveis|Contagem|Média|Average_Available MBytes|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|memória Average_Available MBytes|Sim|Memória MBytes disponível|Contagem|Média|memória Average_Available MBytes|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|troca de MBytes Average_Available|Sim|Troca de MBytes disponível|Contagem|Média|troca de MBytes Average_Available|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Avg. de Disco seg/Leitura|Sim|Avg. Disco seg/Ler|Contagem|Média|Average_Avg. de Disco seg/Leitura|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Avg. Disco seg/Transferência|Sim|Avg. Disco seg/Transferência|Contagem|Média|Average_Avg. Disco seg/Transferência|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Avg. de Disco seg/Escrita|Sim|Avg. Disco seg/Write|Contagem|Média|Average_Avg. de Disco seg/Escrita|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Bytes Recebido/seg|Sim|Bytes Recebidos/seg|Contagem|Média|Average_Bytes Recebido/seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Bytes Enviado/seg|Sim|Bytes Enviados/seg|Contagem|Média|Average_Bytes Enviado/seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Bytes Total/seg|Sim|Bytes Total/seg|Contagem|Média|Average_Bytes Total/seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|comprimento da fila do disco Average_Current|Sim|Comprimento atual da fila do disco|Contagem|Média|comprimento da fila do disco Average_Current|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Disk Ler Bytes/seg|Sim|Discos ler Bytes/seg|Contagem|Média|Average_Disk Ler Bytes/seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Disk Leituras/seg|Sim|Leituras/seg de disco|Contagem|Média|Average_Disk Leituras/seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Transferências Average_Disk/seg|Sim|Transferências de discos/seg|Contagem|Média|Transferências Average_Disk/seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Disk Escrever Bytes/seg|Sim|Discos De Escrita Bytes/seg|Contagem|Média|Average_Disk Escrever Bytes/seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Disk Escreve/Seg|Sim|Escritas/seg de disco|Contagem|Média|Average_Disk Escreve/Seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Free Megabytes|Sim|Megabytes grátis|Contagem|Média|Average_Free Megabytes|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|memória física Average_Free|Sim|Memória Física Gratuita|Contagem|Média|memória física Average_Free|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|espaço Average_Free em arquivos de paging|Sim|Espaço gratuito em ficheiros de paging|Contagem|Média|espaço Average_Free em arquivos de paging|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|memória virtual Average_Free|Sim|Memória Virtual Gratuita|Contagem|Média|memória virtual Average_Free|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Logical Discos Bytes/seg|Sim|Bytes/seg de disco lógico|Contagem|Média|Average_Logical Discos Bytes/seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Page Leituras/seg|Sim|Leituras de página/seg|Contagem|Média|Average_Page Leituras/seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Page Escreve/Seg|Sim|Escritas/seg de página|Contagem|Média|Average_Page Escreve/Seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Pages/seg|Sim|Páginas/seg|Contagem|Média|Average_Pages/seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Pct Tempo Privilegiado|Sim|Pct Tempo Privilegiado|Contagem|Média|Average_Pct Tempo Privilegiado|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Pct tempo de utilizador|Sim|Tempo de utilizador do Pct|Contagem|Média|Average_Pct tempo de utilizador|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Physical Discos Bytes/seg|Sim|Bytes/seg de disco físico|Contagem|Média|Average_Physical Discos Bytes/seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Processes|Sim|Processos|Contagem|Média|Average_Processes|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|comprimento da fila Average_Processor|Sim|Comprimento da fila do processador|Contagem|Média|comprimento da fila Average_Processor|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Size armazenado em ficheiros de paging|Sim|Tamanho armazenado em ficheiros de paging|Contagem|Média|Average_Size armazenado em ficheiros de paging|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Total Bytes|Sim|Total Bytes|Contagem|Média|Average_Total Bytes|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Total Bytes Recebidos|Sim|Total de bytes recebidos|Contagem|Média|Average_Total Bytes Recebidos|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|bytes de Average_Total transmitidos|Sim|Total de bytes transmitidos|Contagem|Média|bytes de Average_Total transmitidos|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Colisões Average_Total|Sim|Colisões totais|Contagem|Média|Colisões Average_Total|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|pacotes de Average_Total recebidos|Sim|Total de pacotes recebidos|Contagem|Média|pacotes de Average_Total recebidos|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|pacotes de Average_Total transmitidos|Sim|Total de pacotes transmitidos|Contagem|Média|pacotes de Average_Total transmitidos|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Total Erros Rx|Sim|Erros Rx totais|Contagem|Média|Average_Total Erros Rx|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Total Tx Erros|Sim|Erros Tx totais|Contagem|Média|Average_Total Tx Erros|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Uptime|Sim|Tempo de atividade|Contagem|Média|Average_Uptime|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|Sim|Espaço de troca de MBytes usado|Contagem|Média|Average_Used MBytes Swap Space|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|KBytes de Memória Average_Used|Sim|KBytes de Memória Usados|Contagem|Média|KBytes de Memória Average_Used|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|MBytes de Memória Average_Used|Sim|MBytes de memória usados|Contagem|Média|MBytes de Memória Average_Used|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Users|Sim|Utilizadores|Contagem|Média|Average_Users|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Virtual Memória Partilhada|Sim|Memória partilhada virtual|Contagem|Média|Average_Virtual Memória Partilhada|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Evento|Sim|Evento|Contagem|Média|Evento|Fonte, EventLog, Computador, EventCategory, EventLevel, EventLevelName, EventID|
|Heartbeat|Sim|Heartbeat|Contagem|Total|Heartbeat|Computador, OSType, Versão, SourceComputerId|
|Atualizar|Sim|Atualizar|Contagem|Média|Atualizar|Computador, Produto, Classificação, UpdateState, Opcional, Aprovado|


## <a name="microsoftpeeringpeerings"></a>Microsoft.Peering/peerings

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|EgressTrafficRate|Sim|Taxa de tráfego de Egress|BitsPerSecond|Média|Taxa de tráfego de egress em bits por segundo|ConnectionId|
|IngressTrafficRate|Sim|Taxa de tráfego de entrada|BitsPerSecond|Média|Taxa de tráfego ingress em bits por segundo|ConnectionId|
|SessãoSAlabilidadeV4|Sim|Disponibilidade de sessão V4|Percentagem|Média|Disponibilidade da sessão V4|ConnectionId|
|SessãoSSAlabilidadeV6|Sim|Disponibilidade de Sessão V6|Percentagem|Média|Disponibilidade da sessão V6|ConnectionId|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft.Peering/peeringServices

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Prefixlatency|Sim|Latência prefixo|Milissegundos|Média|Latência prefixo mediano|PrefixName|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|memory_metric|Sim|Memória|Bytes|Média|Memória. Intervalo 0-3 GB para A1, 0-5 GB para A2, 0-10 GB para A3, 0-25 GB para A4, 0-50 GB para A5 e 0-100 GB para A6|Sem Dimensões|
|memory_thrashing_metric|Sim|Escoamento de memória (conjuntos de dados)|Percentagem|Média|Memória média a bater.|Sem Dimensões|
|qpu_high_utilization_metric|Sim|Maior Utilização de QPU|Contagem|Total|QPU Alta Utilização em Último Minuto, 1 para alta utilização QPU, caso contrário 0|Sem Dimensões|
|Queriaduração|Sim|Duração da consulta (conjuntos de dados)|Milissegundos|Média|Duração da consulta DAX no último intervalo|Sem Dimensões|
|QueryPoolJobQueueLength|Sim|Comprimento da fila de trabalho da piscina de consulta (conjuntos de dados)|Contagem|Média|Número de empregos na fila da piscina de fios de consulta.|Sem Dimensões|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ActiveConnections|Não|ActiveConnections|Contagem|Total|Total de ActiveConnections para Microsoft.Relay.|Nome de Entidade|
|ActiveListeners|Não|ActiveListeners|Contagem|Total|Total de ActiveListeners para Microsoft.Relay.|Nome de Entidade|
|BytesTransferido|Sim|BytesTransferido|Contagem|Total|Total bytesTransferred para Microsoft.Relay.|Nome de Entidade|
|ListenerConnections-ClientError|Não|ListenerConnections-ClientError|Contagem|Total|ClientError em ListenerConnections para Microsoft.Relay.|Nome da Entidade, |
|ListenerConnections-ServerError|Não|ListenerConnections-ServerError|Contagem|Total|ServerError em ListenerConnections para Microsoft.Relay.|Nome da Entidade, |
|ListenerConnections-Success|Não|ListenerConnections-Success|Contagem|Total|Falhas de escuta bem sucedidas para Microsoft.Relay.|Nome da Entidade, |
|ListenerConnections-TotalRequests|Não|ListenerConnections-TotalRequests|Contagem|Total|Total de EscutasConnections para Microsoft.Relay.|Nome de Entidade|
|OuvinteSEsa ligações|Não|OuvinteSEsa ligações|Contagem|Total|Total ListenerDis ligações para Microsoft.Relay.|Nome de Entidade|
|SenderConnections-ClientError|Não|SenderConnections-ClientError|Contagem|Total|ClientError em SenderConnections para Microsoft.Relay.|Nome da Entidade, |
|SenderConnections-ServerError|Não|SenderConnections-ServerError|Contagem|Total|ServerError em SenderConnections para Microsoft.Relay.|Nome da Entidade, |
|SenderConnections-Success|Não|SenderConnections-Success|Contagem|Total|SenderConnections de sucesso para Microsoft.Relay.|Nome da Entidade, |
|SenderConnections-TotalRequests|Não|SenderConnections-TotalRequests|Contagem|Total|Total de pedidos de SenderConnections para Microsoft.Relay.|Nome de Entidade|
|SenderDis ligações|Não|SenderDis ligações|Contagem|Total|Total de SenderDis ligações para Microsoft.Relay.|Nome de Entidade|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|PesquisaLatency|Sim|Procurar Latência|Segundos|Média|Latência média de pesquisa para o serviço de pesquisa|Sem Dimensões|
|SearchQueriesPerSecond|Sim|Consultas de pesquisa por segundo|CondePerSecond|Média|Consultas de pesquisa por segundo para o serviço de pesquisa|Sem Dimensões|
|ThrottledSearchQueriesPercentage|Sim|Percentagem de consultas de pesquisa aceleradas|Percentagem|Média|Percentagem de consultas de pesquisa que foram estrangulados para o serviço de pesquisa|Sem Dimensões|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ActiveConnections|Não|ActiveConnections|Contagem|Total|Total de conexões ativas para Microsoft.ServiceBus.|Sem Dimensões|
|ActiveMessages|Não|Contagem de mensagens ativas numa fila/tópico.|Contagem|Média|Contagem de mensagens ativas numa fila/tópico.|Nome de Entidade|
|LigaçõesClosed|Não|Ligações Fechadas.|Contagem|Média|Ligações fechadas para Microsoft.ServiceBus.|Nome de Entidade|
|LigaçõesAsa|Não|Ligações Abertas.|Contagem|Média|Ligações abertas para Microsoft.ServiceBus.|Nome de Entidade|
|CPUXNS|Não|CPU (precedido)|Percentagem|Máximo|Métrica de utilização do espaço de identificação do espaço de serviço do serviço de serviço. Esta métrica está despricada. Utilize a métrica cpu (NamespaceCpuUsage) em vez disso.|Sem Dimensões|
|DeadletteredMesages|Não|Contagem de mensagens com letras mortas numa fila/tópico.|Contagem|Média|Contagem de mensagens com letras mortas numa fila/tópico.|Nome de Entidade|
|Entradas|Sim|Mensagens Recebidas|Contagem|Total|Mensagens de entrada para Microsoft.ServiceBus.|Nome de Entidade|
|IncomingRequests|Sim|Pedidos Recebidos|Contagem|Total|Pedidos de entrada para Microsoft.ServiceBus.|Nome de Entidade|
|Mensagens|Não|Contagem de mensagens numa Fila/Tópico.|Contagem|Média|Contagem de mensagens numa Fila/Tópico.|Nome de Entidade|
|NamespaceCpuUsage|Não|CPU|Percentagem|Máximo|Métrica de utilização do espaço de identificação do espaço de serviço do serviço de serviço.|Sem Dimensões|
|NomespaceMemoryUsage|Não|Utilização de Memória|Percentagem|Máximo|Métrica de utilização de memória de espaço de nome premium de serviço de serviço de serviço.|Sem Dimensões|
|Despesas de Saída|Sim|Mensagens Enviadas|Contagem|Total|Mensagens de saída para Microsoft.ServiceBus.|Nome de Entidade|
|Horários|Não|Contagem de mensagens programadas numa fila/tópico.|Contagem|Média|Contagem de mensagens programadas numa fila/tópico.|Nome de Entidade|
|ServerErrors|Não|Erros do Servidor.|Contagem|Total|Erros de servidor para Microsoft.ServiceBus.|Nome da Entidade, |
|Tamanho|Não|Tamanho|Bytes|Média|Tamanho de uma fila/tópico em Bytes.|Nome de Entidade|
|Requess de sucesso|Não|Pedidos Com Êxito|Contagem|Total|Total de pedidos bem sucedidos para um espaço de nome|Nome da Entidade, |
|ThrottledRequests|Não|Pedidos Limitados.|Contagem|Total|Pedidos de aceleração para Microsoft.ServiceBus.|Nome da Entidade, |
|UserErrors|Não|Erros do Utilizador.|Contagem|Total|Erros do utilizador para Microsoft.ServiceBus.|Nome da Entidade, |
|WSXNS|Não|Utilização da memória (Preprecada)|Percentagem|Máximo|Métrica de utilização de memória de espaço de nome premium de serviço de serviço de serviço. Esta métrica está prevadida. Utilize a métrica de Utilização da Memória (NamespaceMemoryUsage).|Sem Dimensões|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/aplicações

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ActualCpu|Não|ActualCpu|Contagem|Média|Uso real do CPU em núcleos milli|Nome de aplicação, nome de serviço, nome codepackage, nome de serviçoReplicaName|
|ActualMemory|Não|ActualMemory|Bytes|Média|Utilização real da memória em MB|Nome de aplicação, nome de serviço, nome codepackage, nome de serviçoReplicaName|
|Cpu atribuído|Não|Cpu atribuído|Contagem|Média|CPU alocado a este contentor em núcleos milli|Nome de aplicação, nome de serviço, nome codepackage, nome de serviçoReplicaName|
|Roteiro Atribuído|Não|Roteiro Atribuído|Bytes|Média|Memória alocada a este recipiente em MB|Nome de aplicação, nome de serviço, nome codepackage, nome de serviçoReplicaName|
|AplicaçãoStatus|Não|AplicaçãoStatus|Contagem|Média|Estado da aplicação de malha de tecido de serviço|Nome de aplicação, estado|
|Estatística do Contentor|Não|Estatística do Contentor|Contagem|Média|Estado do contentor na aplicação de malha de tecido de serviço|Nome de aplicação, nome de serviço, nome de codepackagename, ServiceReplicaName, Status|
|CpuUtilização|Não|CpuUtilização|Percentagem|Média|Utilização da CPU para este contentor em percentagem doCpu atribuído|Nome de aplicação, nome de serviço, nome codepackage, nome de serviçoReplicaName|
|Utilização da memória|Não|Utilização da memória|Percentagem|Média|Utilização da CPU para este contentor em percentagem doCpu atribuído|Nome de aplicação, nome de serviço, nome codepackage, nome de serviçoReplicaName|
|RestartCount|Não|RestartCount|Contagem|Média|Reiniciar a contagem de um recipiente na aplicação de malha de tecido de serviço|Nome de aplicação, estado, nome de serviço, nome de serviçoReplicaname, CodePackageName|
|ServiceReplicaStatus|Não|ServiceReplicaStatus|Contagem|Média|Estado de Saúde de uma réplica de serviço na aplicação de malha de tecido de serviço|Nome de aplicação, estado, nome de serviço, Nome de ServiçoReplicaName|
|ServiceStatus|Não|ServiceStatus|Contagem|Média|Estado de Saúde de um serviço na aplicação de malha de tecido de serviço|Nome de aplicação, estado, nome de serviço|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalrService/Signalr

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Contagem de Ligações|Sim|Contagem de Ligação|Contagem|Máximo|A quantidade de ligação do utilizador.|Ponto final|
|InboundTraffic|Sim|Tráfego de Entrada|Bytes|Total|O tráfego de entrada de serviço|Sem Dimensões|
|Contagem de mensagens|Sim|Contagem de mensagens|Contagem|Total|A quantidade total de mensagens.|Sem Dimensões|
|OutboundTraffic|Sim|Tráfego de Saída|Bytes|Total|O tráfego de serviço de saída|Sem Dimensões|
|SystemErrors|Sim|Erros do Sistema|Percentagem|Máximo|A percentagem de erros do sistema|Sem Dimensões|
|UserErrors|Sim|Erros do Utilizador|Percentagem|Máximo|A percentagem de erros do utilizador|Sem Dimensões|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|avg_cpu_percent|Sim|Percentagem média de CPU|Percentagem|Média|Percentagem média de CPU|Sem Dimensões|
|io_bytes_read|Sim|IO bytes ler|Bytes|Média|IO bytes ler|Sem Dimensões|
|io_bytes_written|Sim|IO bytes escritos|Bytes|Média|IO bytes escritos|Sem Dimensões|
|io_requests|Sim|Contagem de pedidos de IO|Contagem|Média|Contagem de pedidos de IO|Sem Dimensões|
|reserved_storage_mb|Sim|Espaço de armazenamento reservado|Contagem|Média|Espaço de armazenamento reservado|Sem Dimensões|
|storage_space_used_mb|Sim|Espaço de armazenamento utilizado|Contagem|Média|Espaço de armazenamento utilizado|Sem Dimensões|
|virtual_core_count|Sim|Contagem de núcleos virtuais|Contagem|Média|Contagem de núcleos virtuais|Sem Dimensões|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servidores/bases de dados

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|active_queries|Sim|Consultas ativas|Contagem|Total|Consultas ativas em todos os grupos de carga de trabalho. Aplica-se apenas a armazéns de dados.|Sem Dimensões|
|allocated_data_storage|Sim|Espaço de dados atribuído|Bytes|Média|Armazenamento de dados alocado. Não aplicável aos armazéns de dados.|Sem Dimensões|
|app_cpu_billed|Sim|App CPU faturada|Contagem|Total|App CPU faturado. Aplica-se a bases de dados sem servidor.|Sem Dimensões|
|app_cpu_percent|Sim|Percentagem de CPU de aplicações|Percentagem|Média|Percentagem de CPU de aplicação. Aplica-se a bases de dados sem servidor.|Sem Dimensões|
|app_memory_percent|Sim|Percentagem de memória de aplicativo|Percentagem|Média|Percentagem de memória de aplicativo. Aplica-se a bases de dados sem servidor.|Sem Dimensões|
|base_blob_size_bytes|Sim|Tamanho de armazenamento de bolha de base|Bytes|Máximo|Tamanho de armazenamento de bolha de base. Aplica-se a bases de dados de hiperescala.|Sem Dimensões|
|blocked_by_firewall|Sim|Bloqueado por Firewall|Contagem|Total|Bloqueado por Firewall|Sem Dimensões|
|cache_hit_percent|Sim|Percentagem de impacto de cache|Percentagem|Máximo|Cache atinge percentagem. Aplica-se apenas a armazéns de dados.|Sem Dimensões|
|cache_used_percent|Sim|Percentagem utilizada em cache|Percentagem|Máximo|Percentagem usada em cache. Aplica-se apenas a armazéns de dados.|Sem Dimensões|
|connection_failed|Sim|Ligações com Falhas|Contagem|Total|Ligações com Falhas|Sem Dimensões|
|connection_successful|Sim|Conexões bem sucedidas|Contagem|Total|Conexões bem sucedidas|Sem Dimensões|
|cpu_limit|Sim|Limite do CPU|Contagem|Média|Limite da CPU. Aplica-se a bases de dados baseadas em vCore.|Sem Dimensões|
|cpu_percent|Sim|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Sem Dimensões|
|cpu_used|Sim|CPU usado|Contagem|Média|CPU usado. Aplica-se a bases de dados baseadas em vCore.|Sem Dimensões|
|impasse|Sim|Impasses|Contagem|Total|Impasses. Não aplicável aos armazéns de dados.|Sem Dimensões|
|diff_backup_size_bytes|Sim|Tamanho de armazenamento de backup diferencial|Bytes|Máximo|Tamanho de armazenamento de backup diferencial cumulativo. Aplica-se a bases de dados baseadas em vCore. Não aplicável às bases de dados de hiperescala.|Sem Dimensões|
|dtu_consumption_percent|Sim|Percentagem de DTU|Percentagem|Média|Percentagem de DTU. Aplica-se a bases de dados baseadas em DTU.|Sem Dimensões|
|dtu_limit|Sim|Limite DTU|Contagem|Média|Limite dTU. Aplica-se a bases de dados baseadas em DTU.|Sem Dimensões|
|dtu_used|Sim|DTU usado|Contagem|Média|DTU usado. Aplica-se a bases de dados baseadas em DTU.|Sem Dimensões|
|dw_backup_size_gb|Sim|Tamanho do armazenamento de dados (GB)|Contagem|Total|O tamanho do armazenamento de dados é composto pelo tamanho dos seus dados e pelo registo de transações. A métrica é contada para a parte 'Armazenamento' da sua conta. Aplica-se apenas a armazéns de dados.|Sem Dimensões|
|dw_geosnapshot_size_gb|Sim|Tamanho do armazenamento de recuperação de desastres (GB)|Contagem|Total|O tamanho do armazenamento de recuperação de desastres é refletido como "Armazenamento de Recuperação de Desastres" na sua conta. Aplica-se apenas a armazéns de dados.|Sem Dimensões|
|dw_snapshot_size_gb|Sim|Tamanho do armazenamento de instantâneo (GB)|Contagem|Total|O tamanho do armazenamento instantâneo é o tamanho das alterações incrementais capturadas por instantâneos para criar pontos de restauro definidos pelo utilizador e automáticos. A métrica é contada para a parte 'Armazenamento' da sua conta. Aplica-se apenas a armazéns de dados.|Sem Dimensões|
|dwu_consumption_percent|Sim|Percentagem de DWU|Percentagem|Máximo|Percentagem de DWU. Aplica-se apenas a armazéns de dados.|Sem Dimensões|
|dwu_limit|Sim|Limite dwu|Contagem|Máximo|Limite da DWU. Aplica-se apenas a armazéns de dados.|Sem Dimensões|
|dwu_used|Sim|DWU usado|Contagem|Máximo|DWU usado. Aplica-se apenas a armazéns de dados.|Sem Dimensões|
|full_backup_size_bytes|Sim|Tamanho de armazenamento de backup completo|Bytes|Máximo|Tamanho acumulado de armazenamento de backup completo. Aplica-se a bases de dados baseadas em vCore. Não aplicável às bases de dados de hiperescala.|Sem Dimensões|
|local_tempdb_usage_percent|Sim|Percentagem de temporários locais|Percentagem|Média|Percentagem de temperatura local. Aplica-se apenas a armazéns de dados.|Sem Dimensões|
|log_backup_size_bytes|Sim|Registar tamanho de armazenamento de backup|Bytes|Máximo|Tamanho de armazenamento de backup de registo cumulativo. Aplica-se às bases de dados baseadas em vCore e hyperscale.|Sem Dimensões|
|log_write_percent|Sim|Percentagem de IO de registo|Percentagem|Média|Registar percentagem de IO. Não aplicável aos armazéns de dados.|Sem Dimensões|
|memory_usage_percent|Sim|Percentagem de memória|Percentagem|Máximo|Percentagem de memória. Aplica-se apenas a armazéns de dados.|Sem Dimensões|
|physical_data_read_percent|Sim|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|Sem Dimensões|
|queued_queries|Sim|Consultas em fila|Contagem|Total|Consultas em fila em todos os grupos de carga de trabalho. Aplica-se apenas a armazéns de dados.|Sem Dimensões|
|sessions_percent|Sim|Percentagem de sessões|Percentagem|Média|Percentagem de sessões. Não aplicável aos armazéns de dados.|Sem Dimensões|
|snapshot_backup_size_bytes|Sim|Tamanho de armazenamento de backup snapshot|Bytes|Máximo|Tamanho de armazenamento de backup de instantâneo cumulativo. Aplica-se a bases de dados de hiperescala.|Sem Dimensões|
|sqlserver_process_core_percent|Sim|Núcleo de núcleo de processo do SQL Server|Percentagem|Máximo|Utilização da CPU em percentagem do processo DB SQL. Não aplicável aos armazéns de dados.|Sem Dimensões|
|sqlserver_process_memory_percent|Sim|SQL Servidor processa memória por cento|Percentagem|Máximo|Utilização da memória em percentagem do processo DB SQL. Não aplicável aos armazéns de dados.|Sem Dimensões|
|storage|Sim|Espaço de dados utilizado|Bytes|Máximo|Espaço de dados usado. Não aplicável aos armazéns de dados.|Sem Dimensões|
|storage_percent|Sim|Espaço de dados usado por cento|Percentagem|Máximo|Espaço de dados usado por cento. Não aplicável a armazéns de dados ou bases de dados de hiperescala.|Sem Dimensões|
|tempdb_data_size|Sim|Kilobytes do tamanho do ficheiro de dados temporários|Contagem|Máximo|Kilobytes tamanho de ficheiro de dados temporários. Não aplicável aos armazéns de dados.|Sem Dimensões|
|tempdb_log_size|Sim|Kilobytes do tamanho do ficheiro de registo temporário|Contagem|Máximo|Kilobytes tamanho do ficheiro de registo temporário. Não aplicável aos armazéns de dados.|Sem Dimensões|
|tempdb_log_used_percent|Sim|Registo de percentagem de temperatura usado|Percentagem|Máximo|Tempdb% % registo usado. Não aplicável aos armazéns de dados.|Sem Dimensões|
|wlg_active_queries|Sim|Consultas ativas do grupo de carga de trabalho|Contagem|Total|Consultas ativas dentro do grupo de carga de trabalho. Aplica-se apenas a armazéns de dados.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|Sim|Tempo limites de consulta do grupo de carga de trabalho|Contagem|Total|Consultas que têm cronometrado para o grupo de carga de trabalho. Aplica-se apenas a armazéns de dados.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|Sim|Alocação de grupo de carga de trabalho por parte do sistema|Percentagem|Máximo|Percentagem de recursos atribuídos em relação a todo o sistema por grupo de carga de trabalho. Aplica-se apenas a armazéns de dados.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Sim|Alocação de grupo de carga de trabalho por percentagem de recursos da TAMPA|Percentagem|Máximo|Percentagem de recursos atribuídos em relação aos recursos de bonificação especificados por grupo de carga de trabalho. Aplica-se apenas a armazéns de dados.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|Sim|Por cento efetivo do recurso cap|Percentagem|Máximo|Um limite rígido para a percentagem de recursos permitidos para o grupo de carga de trabalho, tendo em conta a percentagem efetiva de recursos min atribuídos a outros grupos de carga de trabalho. Aplica-se apenas a armazéns de dados.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|Sim|Por cento de recursos min eficazes|Percentagem|Máximo|Percentagem mínima de recursos reservados e isolados para o grupo de trabalho, tendo em conta o nível mínimo de serviço. Aplica-se apenas a armazéns de dados.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|Sim|Consultas em fila de grupos de carga|Contagem|Total|Consultas em fila dentro do grupo de carga de trabalho. Aplica-se apenas a armazéns de dados.|WorkloadGroupName, IsUserDefined|
|workers_percent|Sim|Percentagem de trabalhadores|Percentagem|Média|Percentagem de trabalhadores. Não aplicável aos armazéns de dados.|Sem Dimensões|
|xtp_storage_percent|Sim|In-Memory por cento de armazenamento OLTP|Percentagem|Média|In-Memory por cento de armazenamento OLTP. Não aplicável aos armazéns de dados.|Sem Dimensões|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servidores/elásticos

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|allocated_data_storage|Sim|Espaço de dados atribuído|Bytes|Média|Espaço de dados atribuído|Sem Dimensões|
|allocated_data_storage_percent|Sim|Espaço de dados alocado por cento|Percentagem|Máximo|Espaço de dados alocado por cento|Sem Dimensões|
|cpu_limit|Sim|Limite do CPU|Contagem|Média|Limite da CPU. Aplica-se a piscinas elásticas à base de vCore.|Sem Dimensões|
|cpu_percent|Sim|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Sem Dimensões|
|cpu_used|Sim|CPU usado|Contagem|Média|CPU usado. Aplica-se a piscinas elásticas à base de vCore.|Sem Dimensões|
|database_allocated_data_storage|Não|Espaço de dados atribuído|Bytes|Média|Espaço de dados atribuído|Base de dadosResourceId|
|database_cpu_limit|Não|Limite do CPU|Contagem|Média|Limite do CPU|Base de dadosResourceId|
|database_cpu_percent|Não|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Base de dadosResourceId|
|database_cpu_used|Não|CPU usado|Contagem|Média|CPU usado|Base de dadosResourceId|
|database_dtu_consumption_percent|Não|Percentagem de DTU|Percentagem|Média|Percentagem de DTU|Base de dadosResourceId|
|database_eDTU_used|Não|eDTU usado|Contagem|Média|eDTU usado|Base de dadosResourceId|
|database_log_write_percent|Não|Percentagem de IO de registo|Percentagem|Média|Percentagem de IO de registo|Base de dadosResourceId|
|database_physical_data_read_percent|Não|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|Base de dadosResourceId|
|database_sessions_percent|Não|Percentagem de sessões|Percentagem|Média|Percentagem de sessões|Base de dadosResourceId|
|database_storage_used|Não|Espaço de dados utilizado|Bytes|Média|Espaço de dados utilizado|Base de dadosResourceId|
|database_workers_percent|Não|Percentagem de trabalhadores|Percentagem|Média|Percentagem de trabalhadores|Base de dadosResourceId|
|dtu_consumption_percent|Sim|Percentagem de DTU|Percentagem|Média|Percentagem de DTU. Aplica-se a piscinas elásticas à base de DTU.|Sem Dimensões|
|eDTU_limit|Sim|limite eDTU|Contagem|Média|limite eDTU. Aplica-se a piscinas elásticas à base de DTU.|Sem Dimensões|
|eDTU_used|Sim|eDTU usado|Contagem|Média|eDTU usado. Aplica-se a piscinas elásticas à base de DTU.|Sem Dimensões|
|log_write_percent|Sim|Percentagem de IO de registo|Percentagem|Média|Percentagem de IO de registo|Sem Dimensões|
|physical_data_read_percent|Sim|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|Sem Dimensões|
|sessions_percent|Sim|Percentagem de sessões|Percentagem|Média|Percentagem de sessões|Sem Dimensões|
|sqlserver_process_core_percent|Sim|Núcleo de núcleo de processo do SQL Server|Percentagem|Máximo|Utilização da CPU em percentagem do processo DB SQL. Aplica-se a piscinas elásticas.|Sem Dimensões|
|sqlserver_process_memory_percent|Sim|SQL Servidor processa memória por cento|Percentagem|Máximo|Utilização da memória em percentagem do processo DB SQL. Aplica-se a piscinas elásticas.|Sem Dimensões|
|storage_limit|Sim|Tamanho máximo dos dados|Bytes|Média|Tamanho máximo dos dados|Sem Dimensões|
|storage_percent|Sim|Espaço de dados usado por cento|Percentagem|Média|Espaço de dados usado por cento|Sem Dimensões|
|storage_used|Sim|Espaço de dados utilizado|Bytes|Média|Espaço de dados utilizado|Sem Dimensões|
|tempdb_data_size|Sim|Kilobytes do tamanho do ficheiro de dados temporários|Contagem|Máximo|Kilobytes do tamanho do ficheiro de dados temporários|Sem Dimensões|
|tempdb_log_size|Sim|Kilobytes do tamanho do ficheiro de registo temporário|Contagem|Máximo|Kilobytes do tamanho do ficheiro de registo temporário|Sem Dimensões|
|tempdb_log_used_percent|Sim|Registo de percentagem de temperatura usado|Percentagem|Máximo|Registo de percentagem de temperatura usado|Sem Dimensões|
|workers_percent|Sim|Percentagem de trabalhadores|Percentagem|Média|Percentagem de trabalhadores|Sem Dimensões|
|xtp_storage_percent|Sim|In-Memory por cento de armazenamento OLTP|Percentagem|Média|In-Memory por cento de armazenamento OLTP|Sem Dimensões|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAcontas

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Autenticação|
|Saída|Sim|Saída|Bytes|Total|A quantidade de dados de saída. Este número inclui saída para cliente externo da Azure Storage, bem como saída dentro do Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Autenticação|
|Entrada|Sim|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Autenticação|
|SucessoE2ELatency|Sim|Latência de E2E Com Êxito|Milissegundos|Média|A latência média de ponta a ponta de pedidos bem sucedidos feita a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Autenticação|
|SucessoServerLatency|Sim|Latência do servidor de sucesso|Milissegundos|Média|O tempo médio utilizado para processar um pedido com êxito pelo Armazenamento do Azure. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Autenticação|
|Transações|Sim|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType, GeoType, ApiName, Autenticação|
|Capacidade Usada|Não|Capacidade utilizada|Bytes|Média|A quantidade de armazenamento utilizada pela conta de armazenamento. Para as contas de armazenamento standard, é a soma da capacidade utilizada pelos blobs, tabelas, ficheiros e filas. Para contas de armazenamento premium e contas de armazenamento Blob, é o mesmo que BlobCapacity ou FileCapacity.|Sem Dimensões|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAcontas/blobServices

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Autenticação|
|Capacidade blob|Não|Capacidade blob|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Blob da conta de armazenamento em bytes.|BlobType, Tier|
|BlobCount|Não|Contagem de blobs|Contagem|Média|O número de objetos blob armazenados na conta de armazenamento.|BlobType, Tier|
|BlobProvisionedSize|Não|Tamanho provisto blob|Bytes|Média|A quantidade de armazenamento abastado no serviço Blob da conta de armazenamento em bytes.|BlobType, Tier|
|Contagem de contentores|Sim|Contagem de contentores blob|Contagem|Média|O número de contentores na conta de armazenamento.|Sem Dimensões|
|Saída|Sim|Saída|Bytes|Total|A quantidade de dados de saída. Este número inclui saída para cliente externo da Azure Storage, bem como saída dentro do Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Autenticação|
|Capacidade de Indexação|Não|Capacidade de Índice|Bytes|Média|A quantidade de armazenamento utilizada pelo índice hierárquico Azure Data Lake Storage Gen2.|Sem Dimensões|
|Entrada|Sim|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Autenticação|
|SucessoE2ELatency|Sim|Latência de E2E Com Êxito|Milissegundos|Média|A latência média de ponta a ponta de pedidos bem sucedidos feita a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Autenticação|
|SucessoServerLatency|Sim|Latência do servidor de sucesso|Milissegundos|Média|O tempo médio utilizado para processar um pedido com êxito pelo Armazenamento do Azure. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Autenticação|
|Transações|Sim|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType, GeoType, ApiName, Autenticação|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAcontas/fileServices

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Autenticação, FileShare|
|Saída|Sim|Saída|Bytes|Total|A quantidade de dados de saída. Este número inclui saída para cliente externo da Azure Storage, bem como saída dentro do Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Autenticação, FileShare|
|Capacidade de Ficheiros|Não|Capacidade de Arquivo|Bytes|Média|A quantidade de armazenamento de ficheiros utilizado pela conta de armazenamento.|FileShare|
|Contagem de ficheiros|Não|Contagem de ficheiros|Contagem|Média|O número de ficheiros na conta de armazenamento.|FileShare|
|FileShareCapacityQuota|Não|Quota de capacidade de partilha de ficheiros|Bytes|Média|O limite superior da quantidade de armazenamento que pode ser usado pelo Azure Files Service em bytes.|FileShare|
|FileShareCount|Não|Contagem de partilha de ficheiros|Contagem|Média|O número de ações de ficheiros na conta de armazenamento.|Sem Dimensões|
|FileShareProvisionedIOPS|Não|IOPS provisos de ações de ficheiros|Bytes|Média|O número de base de IOPS provisido para a parte de ficheiro premium na conta de armazenamento de ficheiros premium. Este número é calculado com base na dimensão (quota) prevista da capacidade de participação.|FileShare|
|FileShareSnapshotCount|Não|Contagem de snapshot de partilha de ficheiros|Contagem|Média|O número de instantâneos presentes na parte do Serviço de Ficheiros da conta de armazenamento.|FileShare|
|FileShareSnapshotSize|Não|Tamanho do snapshot de partilha de arquivo|Bytes|Média|A quantidade de armazenamento utilizada pelas fotos no serviço de ficheiros da conta de armazenamento em bytes.|FileShare|
|Entrada|Sim|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Autenticação, FileShare|
|SucessoE2ELatency|Sim|Latência de E2E Com Êxito|Milissegundos|Média|A latência média de ponta a ponta de pedidos bem sucedidos feita a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Autenticação, FileShare|
|SucessoServerLatency|Sim|Latência do servidor de sucesso|Milissegundos|Média|O tempo médio utilizado para processar um pedido com êxito pelo Armazenamento do Azure. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Autenticação, FileShare|
|Transações|Sim|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType, GeoType, ApiName, Autenticação, FileShare|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAcontas/filas Serviços

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Autenticação|
|Saída|Sim|Saída|Bytes|Total|A quantidade de dados de saída. Este número inclui saída para cliente externo da Azure Storage, bem como saída dentro do Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Autenticação|
|Entrada|Sim|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Autenticação|
|Capacidade de Fila|Sim|Capacidade de fila|Bytes|Média|A quantidade de armazenamento de fila utilizada pela conta de armazenamento.|Sem Dimensões|
|Contagem de filas|Sim|Contagem de filas|Contagem|Média|O número de filas na conta de armazenamento.|Sem Dimensões|
|QueueMessageCount|Sim|Contagem de mensagens de fila|Contagem|Média|O número de mensagens de fila não piradas na conta de armazenamento.|Sem Dimensões|
|SucessoE2ELatency|Sim|Latência de E2E Com Êxito|Milissegundos|Média|A latência média de ponta a ponta de pedidos bem sucedidos feita a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Autenticação|
|SucessoServerLatency|Sim|Latência do servidor de sucesso|Milissegundos|Média|O tempo médio utilizado para processar um pedido com êxito pelo Armazenamento do Azure. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Autenticação|
|Transações|Sim|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType, GeoType, ApiName, Autenticação|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAcontas/tableServices

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Autenticação|
|Saída|Sim|Saída|Bytes|Total|A quantidade de dados de saída. Este número inclui saída para cliente externo da Azure Storage, bem como saída dentro do Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Autenticação|
|Entrada|Sim|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Autenticação|
|SucessoE2ELatency|Sim|Latência de E2E Com Êxito|Milissegundos|Média|A latência média de ponta a ponta de pedidos bem sucedidos feita a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Autenticação|
|SucessoServerLatency|Sim|Latência do servidor de sucesso|Milissegundos|Média|O tempo médio utilizado para processar um pedido com êxito pelo Armazenamento do Azure. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Autenticação|
|Capacidade de Mesa|Sim|Capacidade de mesa|Bytes|Média|A quantidade de armazenamento de mesa utilizada pela conta de armazenamento.|Sem Dimensões|
|MesaCount|Sim|Contagem de tabelas|Contagem|Média|O número de mesas na conta de armazenamento.|Sem Dimensões|
|TabelaseconagemCount|Sim|Contagem de Entidades de Tabela|Contagem|Média|O número de entidades de mesa na conta de armazenamento.|Sem Dimensões|
|Transações|Sim|Transações|Contagem|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType, GeoType, ApiName, Autenticação|


## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ServidorSyncSessionResult|Sim|Resultado da sessão de sincronização|Contagem|Média|Métrica que regista um valor de 1 cada vez que o Ponto Final do Servidor completa com sucesso uma Sessão de Sincronização com o Ponto final da Cloud|SyncGroupName, ServerEndpointName, SyncDirection|
|ArmazenamentoSybatchTransferredFileBytes|Sim|Bytes sincronizados|Bytes|Total|Tamanho total do ficheiro transferido para Sincronização|SyncGroupName, ServerEndpointName, SyncDirection|
|ArmazenamentoSyncRecalledNetworkBytesByApplication|Sim|Tamanho de recolha de camadas de nuvem por aplicação|Bytes|Total|Tamanho dos dados recolhidos pela aplicação|SyncGroupName, ServerName, ApplicationName|
|ArmazenamentoSyncRecalledTotalNetworkBytes|Sim|Tamanho de recuperação de nível de nuvem|Bytes|Total|Tamanho dos dados recordados|SyncGroupName, Nome do Servidor|
|ArmazenamentoSyncRecallIOTotalSizeBytes|Sim|Recuperação de camadas de nuvem|Bytes|Total|Tamanho total dos dados recolhidos pelo servidor|ServerName|
|ArmazenamentoSyncRecallThroughputBytesPerSecond|Sim|Produção de recolha de camadas de nuvem|BytesPerSecond|Média|Tamanho do rendimento da recolha de dados|SyncGroupName, Nome do Servidor|
|StorageSyncServerHeartbeat|Sim|Estado online do servidor|Contagem|Máximo|Métrica que regista um valor de 1 cada vez que o servidor resigtered regista com sucesso um batimento cardíaco com o Cloud Endpoint|ServerName|
|ArmazenamentoSyncSyncSessionAppliedFilesCount|Sim|Ficheiros Sincronizados|Contagem|Total|Contagem de Ficheiros sincronizado|SyncGroupName, ServerEndpointName, SyncDirection|
|ArmazenamentoSyncSyncSessionPerItemErrorsCount|Sim|Ficheiros não sincronizados|Contagem|Total|Contagem de ficheiros não sincronizado|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registradosServers

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ServerHeartbeat|Sim|Estado online do servidor|Contagem|Máximo|Métrica que regista um valor de 1 cada vez que o servidor resigtered regista com sucesso um batimento cardíaco com o Cloud Endpoint|ServerResourceId, Nome do Servidor|
|ServerRecallIOTotalSizeBytes|Sim|Recuperação de camadas de nuvem|Bytes|Total|Tamanho total dos dados recolhidos pelo servidor|ServerResourceId, Nome do Servidor|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|Sim|Bytes sincronizados|Bytes|Total|Tamanho total do ficheiro transferido para Sincronização|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionAppliedFilesCount|Sim|Ficheiros Sincronizados|Contagem|Total|Contagem de Ficheiros sincronizado|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Sim|Ficheiros não sincronizados|Contagem|Total|Contagem de ficheiros não sincronizado|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|Sim|Bytes sincronizados|Bytes|Total|Tamanho total do ficheiro transferido para Sincronização|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|Sim|Ficheiros Sincronizados|Contagem|Total|Contagem de Ficheiros sincronizado|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Sim|Ficheiros não sincronizados|Contagem|Total|Contagem de ficheiros não sincronizado|ServerEndpointName, SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|Sim|Pedidos de função falhadas|Contagem|Total|Pedidos de função falhadas|Nome Lógico, PartitionId|
|AMLCalloutInputEvents|Sim|Eventos de Função|Contagem|Total|Eventos de Função|Nome Lógico, PartitionId|
|AMLCalloutRequests|Sim|Pedidos de Função|Contagem|Total|Pedidos de Função|Nome Lógico, PartitionId|
|ConversoresErrors|Sim|Erros de Conversão de Dados|Contagem|Total|Erros de Conversão de Dados|Nome Lógico, PartitionId|
|DeserializaçãoError|Sim|Erros de deserialização de entrada|Contagem|Total|Erros de deserialização de entrada|Nome Lógico, PartitionId|
|DropOrAdjustedEvents|Sim|Eventos fora de ordem|Contagem|Total|Eventos fora de ordem|Nome Lógico, PartitionId|
|Início dos Eventos|Sim|Eventos de entrada antecipada|Contagem|Total|Eventos de entrada antecipada|Nome Lógico, PartitionId|
|Erros|Sim|Erros de tempo de execução|Contagem|Total|Erros de tempo de execução|Nome Lógico, PartitionId|
|InputEventBytes|Sim|Insutos de Eventos|Bytes|Total|Insutos de Eventos|Nome Lógico, PartitionId|
|InputEvents|Sim|Eventos de Entrada|Contagem|Total|Eventos de Entrada|Nome Lógico, PartitionId|
|InputEventsSourcesBacklogged|Sim|Eventos de entrada recuados|Contagem|Máximo|Eventos de entrada recuados|Nome Lógico, PartitionId|
|InputEventsSourcesPerSecond|Sim|Fontes de entrada recebidas|Contagem|Total|Fontes de entrada recebidas|Nome Lógico, PartitionId|
|LateInputEvents|Sim|Eventos de entrada tardia|Contagem|Total|Eventos de entrada tardia|Nome Lógico, PartitionId|
|OutputEvents|Sim|Eventos de saída|Contagem|Total|Eventos de saída|Nome Lógico, PartitionId|
|OutputWatermarkDelaySeconds|Sim|Atraso da marca de água|Segundos|Máximo|Atraso da marca de água|Nome Lógico, PartitionId|
|Utilização de Recursos|Sim|SU % Utilização|Percentagem|Máximo|SU % Utilização|Nome Lógico, PartitionId|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|OrquestraçãoActivityRunsEnded|Não|A atividade corre terminada|Contagem|Total|Contagem de atividades de orquestração que sucederam, falharam ou foram canceladas|Resultado, Falha DeType, Atividade, ActivityType, Pipeline|
|OrquestrationPipelineRunsEnded|Não|As corridas de gasodutos terminaram|Contagem|Total|Contagem de corridas de oleoduto de orquestração que conseguiu, falhou, ou foram cancelados|Resultado, FalhaType, Pipeline|
|OrquestraçãoStroggersDessemotado|Não|Os gatilhos terminaram|Contagem|Total|Contagem de gatilhos de orquestração que conseguiu, falhou, ou foram cancelados|Resultado, FalhaType, Gatilho|
|SQLOnDemandLoginAttempts|Não|Tentativas de login|Contagem|Total|Contagem de tentativas de login que succedou ou falhou|Resultado|
|SQLOnDemandQueriesEnded|Não|Consultas terminadas|Contagem|Total|Contagem de consultas que sucederam, falharam, ou foram cancelados|Resultado|
|SQLOnDemandQueryProcessedBytes|Não|Dados processados|Bytes|Total|Quantidade de dados processados por consultas|Sem Dimensões|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Capacidade de Cores|Não|Capacidade de núcleos|Contagem|Máximo|Capacidade de núcleos|Sem Dimensões|
|Capacidade de MemóriaGB|Não|Capacidade de memória (GB)|Contagem|Máximo|Capacidade de memória (GB)|Sem Dimensões|
|SparkJobsEnded|Sim|Candidaturas terminadas|Contagem|Total|Contagem de candidaturas terminadas|JobType, JobResult|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AdaptiveCacheHitPercent|Não|Percentagem de impacto de cache adaptativo|Percentagem|Máximo|Mede a forma como as cargas de trabalho estão a utilizar a cache adaptativa. Utilize esta métrica com a métrica percentual de impacto da cache para determinar se deve escalar para capacidade adicional ou refazer cargas de trabalho para hidratar a cache|Sem Dimensões|
|AdaptiveCacheUsedPercent|Não|Percentagem de cache adaptativa utilizada|Percentagem|Máximo|Mede a forma como as cargas de trabalho estão a utilizar a cache adaptativa. Utilize esta métrica com a cache utilizada métrica percentual para determinar se deve escalar para capacidade adicional ou refazer cargas de trabalho para hidratar a cache|Sem Dimensões|
|Ligações|Sim|Ligações|Contagem|Total|Contagem de entradas totais para a piscina SQL|Resultado|
|ConexõesBlockedByFirewall|Não|Ligações bloqueadas por firewall|Contagem|Total|Contagem de ligações bloqueadas pelas regras de firewall. Reveja as políticas de controlo de acesso para a sua piscina SQL e monitorize estas ligações se a contagem for elevada|Sem Dimensões|
|DWULimit|Não|Limite dwu|Contagem|Máximo|Objetivo de nível de serviço da piscina SQL|Sem Dimensões|
|DWUUSED|Não|DWU usado|Contagem|Máximo|Representa uma representação de alto nível de uso em toda a piscina SQL. Medido por limite DE DWU * percentagem de DWU|Sem Dimensões|
|DWUUsedPercent|Não|Percentagem utilizada pela DWU|Percentagem|Máximo|Representa uma representação de alto nível de uso em toda a piscina SQL. Medido pela tomada do máximo entre a percentagem de CPU e a percentagem de IO de dados|Sem Dimensões|
|LocalTempDBUsedPercent|Não|Percentagem de temporários locais utilizada|Percentagem|Máximo|Utilização temporária local em todos os nós de computação - valores são emitidos a cada cinco minutos|Sem Dimensões|
|MemoryUsedPercent|Não|Percentagem de memória usada|Percentagem|Máximo|Utilização da memória em todos os nós na piscina SQL|Sem Dimensões|
|wlg_effective_min_resource_percent|Sim|Por cento de recursos min eficazes|Percentagem|Mínimo|A definição eficaz da percentagem de recursos min permitiu considerar o nível de serviço e as definições do grupo de carga de trabalho. O min_percentage_resource eficaz pode ser ajustado mais alto em níveis de serviço mais baixos|IsUserDefined, WorkloadGroup|
|WLGActiveQueries|Não|Consultas ativas do grupo de carga de trabalho|Contagem|Total|As consultas ativas dentro do grupo de carga de trabalho. Utilizando esta métrica não filtrada e não filtrada exibe todas as consultas ativas em execução no sistema|IsUserDefined, WorkloadGroup|
|WLGActiveQueriesTimeouts|Não|Tempo limites de consulta do grupo de carga de trabalho|Contagem|Total|Consultas para o grupo de trabalho que tem cronometrado. Os intervalos de consulta reportados por esta métrica são apenas uma vez que a consulta tenha começado a executar (não inclui o tempo de espera devido ao bloqueio ou espera de recursos)|IsUserDefined, WorkloadGroup|
|WLGAllocationByMaxResourcePercent|Não|Alocação de grupo de carga de trabalho por percentagem máxima de recursos|Percentagem|Máximo|Apresenta a percentagem de alocação de recursos em relação à percentagem de recursos de limite efetivo por grupo de carga de trabalho. Esta métrica fornece a utilização eficaz do grupo de carga de trabalho|IsUserDefined, WorkloadGroup|
|WLGAllocationBySystemPercent|Não|Alocação de grupo de carga de trabalho por parte do sistema|Percentagem|Máximo|A percentagem de alocação de recursos relativos a todo o sistema|IsUserDefined, WorkloadGroup|
|WLGEffectiveCapResourcePercent|Sim|Por cento efetivo do recurso cap|Percentagem|Máximo|A percentagem efetiva de recursos de capital para o grupo de trabalho. Se houver outros grupos de carga de trabalho com min_percentage_resource > 0, o effective_cap_percentage_resource é reduzido proporcionalmente|IsUserDefined, WorkloadGroup|
|WLGQueuedQueries|Não|Consultas em fila de grupos de carga|Contagem|Total|Contagem cumulativa de pedidos em fila após o limite máximo de concuência ter sido atingido|IsUserDefined, WorkloadGroup|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/ambientes

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Sim|Ingress Recebido Bytes|Bytes|Total|Contagem de bytes lidos de todas as fontes do evento|Sem Dimensões|
|IngressReceivedInvalidMesages|Sim|Ingress recebeu mensagens inválidas|Contagem|Total|Contagem de mensagens inválidas lidas de todas as fontes de eventos do centro de eventos do Evento ou IoT|Sem Dimensões|
|IngressReceivedMessages|Sim|Mensagens Recebidas Ingress|Contagem|Total|Contagem de mensagens lidas de todas as fontes de eventos do centro de eventos do Evento ou IoT|Sem Dimensões|
|IngressReceivedMessagesCountLag|Sim|Ingress recebeu recado de mensagens|Contagem|Média|Diferença entre o número de sequência da última mensagem encosa na partição de origem do evento e o número de sequência de mensagens que estão a ser processadas em Ingress|Sem Dimensões|
|IngressReceivedMesstimeLag|Sim|Ingress recebeu recado de tempo de mensagens|Segundos|Máximo|Diferença entre o tempo em que a mensagem é encosa na fonte do evento e o tempo que é processado em Ingress|Sem Dimensões|
|IngressStoredBytes|Sim|Ingress Stored Bytes|Bytes|Total|Tamanho total dos eventos processados com sucesso e disponíveis para consulta|Sem Dimensões|
|IngresssStoredEvents|Sim|Eventos Armazenados ingress|Contagem|Total|Contagem de eventos achatados processados com sucesso e disponíveis para consulta|Sem Dimensões|
|WarmStorageMaxProperties|Sim|Propriedades Max de Armazenamento Quente|Contagem|Máximo|Número máximo de propriedades utilizadas pelo ambiente para S1/S2 SKU e número máximo de propriedades permitidas pela Warm Store para PAYG SKU|Sem Dimensões|
|WarmStorageUsedProperties|Sim|Propriedades usadas de armazenamento quente |Contagem|Máximo|Número de propriedades utilizadas pelo ambiente para S1/S2 SKU e número de propriedades utilizadas pela Warm Store para PAYG SKU|Sem Dimensões|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/ambientes/eventsources

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Sim|Ingress Recebido Bytes|Bytes|Total|Contagem de bytes lidos a partir da fonte do evento|Sem Dimensões|
|IngressReceivedInvalidMesages|Sim|Ingress recebeu mensagens inválidas|Contagem|Total|Contagem de mensagens inválidas lidas a partir da fonte do evento|Sem Dimensões|
|IngressReceivedMessages|Sim|Mensagens Recebidas Ingress|Contagem|Total|Contagem de mensagens lidas a partir da fonte do evento|Sem Dimensões|
|IngressReceivedMessagesCountLag|Sim|Ingress recebeu recado de mensagens|Contagem|Média|Diferença entre o número de sequência da última mensagem encosa na partição de origem do evento e o número de sequência de mensagens que estão a ser processadas em Ingress|Sem Dimensões|
|IngressReceivedMesstimeLag|Sim|Ingress recebeu recado de tempo de mensagens|Segundos|Máximo|Diferença entre o tempo em que a mensagem é encosa na fonte do evento e o tempo que é processado em Ingress|Sem Dimensões|
|IngressStoredBytes|Sim|Ingress Stored Bytes|Bytes|Total|Tamanho total dos eventos processados com sucesso e disponíveis para consulta|Sem Dimensões|
|IngresssStoredEvents|Sim|Eventos Armazenados ingress|Contagem|Total|Contagem de eventos achatados processados com sucesso e disponíveis para consulta|Sem Dimensões|
|WarmStorageMaxProperties|Sim|Propriedades Max de Armazenamento Quente|Contagem|Máximo|Número máximo de propriedades utilizadas pelo ambiente para S1/S2 SKU e número máximo de propriedades permitidas pela Warm Store para PAYG SKU|Sem Dimensões|
|WarmStorageUsedProperties|Sim|Propriedades usadas de armazenamento quente |Contagem|Máximo|Número de propriedades utilizadas pelo ambiente para S1/S2 SKU e número de propriedades utilizadas pela Warm Store para PAYG SKU|Sem Dimensões|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Bytes de leitura de disco|Sim|Bytes de leitura de disco|Bytes|Total|Produção total de disco devido a operações de leitura durante o período de amostragem.|Sem Dimensões|
|Operações de leitura de disco/sec|Sim|Operações de leitura de disco/sec|CondePerSecond|Média|O número médio de operações de leitura de IO no período de amostragem anterior. Note que estas operações podem ser de tamanho variável.|Sem Dimensões|
|Bytes de escrita de disco|Sim|Bytes de escrita de disco|Bytes|Total|Produção total de disco devido a operações de escrita durante o período de amostragem.|Sem Dimensões|
|Operações de escrita de discos/seg|Sim|Operações de escrita de discos/seg|CondePerSecond|Média|O número médio de operações de escrita de IO no período de amostragem anterior. Note que estas operações podem ser de tamanho variável.|Sem Dimensões|
|DiskReadBytesPerSecond|Sim|Discos de leitura bytes/sec|BytesPerSecond|Média|Produção média de disco devido a operações de leitura durante o período de amostragem.|Sem Dimensões|
|DiskReadLatency|Sim|Leitura de disco Latência|Milissegundos|Média|Latência de leitura total. A soma do dispositivo e o núcleo lêem latências.|Sem Dimensões|
|Operações de Leitura de Discos|Sim|Operações de leitura de discos|Contagem|Total|O número de operações de leitura de IO no período de amostragem anterior. Note que estas operações podem ser de tamanho variável.|Sem Dimensões|
|DiskWriteBytesPerSecond|Sim|Discos De Escrita Bytes/Sec|BytesPerSecond|Média|Produção média de disco devido a operações de escrita durante o período de amostragem.|Sem Dimensões|
|DiskWriteLatency|Sim|Latência de escrita de disco|Milissegundos|Média|Latência total de escrita. A soma do dispositivo e o núcleo escrevem latências.|Sem Dimensões|
|DiskWriteOperations|Sim|Operações de escrita de discos|Contagem|Total|O número de operações de escrita de IO no período de amostragem anterior. Note que estas operações podem ser de tamanho variável.|Sem Dimensões|
|MemoryActive|Sim|Memória Ativa|Bytes|Média|A quantidade de memória usada pelo VM no passado pequena janela de tempo. Este é o número "verdadeiro" de quanta memória o VM atualmente precisa. Memória adicional e não uusada pode ser trocada ou balonizada sem impacto no desempenho do hóspede.|Sem Dimensões|
|Memorygranted|Sim|Memória Concedida|Bytes|Média|A quantidade de memória que foi concedida ao VM pelo hospedeiro. A memória não é concedida ao hospedeiro até que seja tocada uma vez e a memória concedida pode ser trocada ou balonizada se o VMkernel precisar da memória.|Sem Dimensões|
|MemóriaSUsed|Sim|Memória utilizada|Bytes|Média|A quantidade de memória da máquina que está a ser utilizada pelo VM.|Sem Dimensões|
|Entrada na Rede|Sim|Entrada na Rede|Bytes|Total|Produção total de rede para tráfego recebido.|Sem Dimensões|
|Saída da Rede|Sim|Saída da Rede|Bytes|Total|Produção total de rede para tráfego transmitido.|Sem Dimensões|
|NetworkInBytesPerSecond|Sim|Rede em Bytes/Sec|BytesPerSecond|Média|Produção média de rede para tráfego recebido.|Sem Dimensões|
|NetworkOutBytesPerSecond|Sim|Network out Bytes/Sec|BytesPerSecond|Média|Produção média de rede para tráfego transmitido.|Sem Dimensões|
|Percentagem da CPU|Sim|Percentagem da CPU|Percentagem|Média|A utilização do CPU. Este valor é reportado com 100% representando todos os núcleos de processadores no sistema. Como exemplo, um VM de duas vias que usa 50% de um sistema de quatro núcleos está a usar completamente dois núcleos.|Sem Dimensões|
|PercentagemCpuReady|Sim|Percentagem CPU Pronta|Milissegundos|Total|Tempo pronto é o tempo gasto à espera que os CPU(s) fiquem disponíveis no intervalo de atualização anterior.|Sem Dimensões|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Ativarequests|Sim|Pedidos Ativos|Contagem|Total|Pedidos Ativos|Instância|
|AverageResponseTime|Sim|Tempo médio de resposta|Segundos|Média|Tempo médio de resposta|Instância|
|BytesReceived|Sim|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Sim|Data out|Bytes|Total|Data out|Instância|
|CpuPercentage|Sim|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Instância|
|DiskQueueLength|Sim|Comprimento da fila do disco|Contagem|Média|Comprimento da fila do disco|Instância|
|Http101|Sim|Http 101|Contagem|Total|Http 101|Instância|
|Http2xx|Sim|Http 2xx|Contagem|Total|Http 2xx|Instância|
|Http3xx|Sim|Http 3xx|Contagem|Total|Http 3xx|Instância|
|Http401|Sim|Http 401|Contagem|Total|Http 401|Instância|
|Http403|Sim|Http 403|Contagem|Total|Http 403|Instância|
|Http404|Sim|Http 404|Contagem|Total|Http 404|Instância|
|Http406|Sim|Http 406|Contagem|Total|Http 406|Instância|
|Http4xx|Sim|Http 4xx|Contagem|Total|Http 4xx|Instância|
|Http5xx|Sim|http Erros do servidor|Contagem|Total|http Erros do servidor|Instância|
|HttpQueueLength|Sim|Comprimento da fila http|Contagem|Média|Comprimento da fila http|Instância|
|Grandes Aplicações de Planos de Serviço|Sim|Trabalhadores do plano de aplicação grande|Contagem|Média|Trabalhadores do plano de aplicação grande|Sem Dimensões|
|MediumAppServicePlanInstances|Sim|Trabalhadores do Plano de Serviço de Aplicações Médias|Contagem|Média|Trabalhadores do Plano de Serviço de Aplicações Médias|Sem Dimensões|
|MemóriaPercentage|Sim|Percentagem de Memória|Percentagem|Média|Percentagem de Memória|Instância|
|Pedidos|Sim|Pedidos|Contagem|Total|Pedidos|Instância|
|Pequenas Aplicações de Planos doAppService|Sim|Trabalhadores do plano de aplicação de pequenas aplicações|Contagem|Média|Trabalhadores do plano de aplicação de pequenas aplicações|Sem Dimensões|
|TotalFrontEnds|Sim|Total de extremidades frontais|Contagem|Média|Total de extremidades frontais|Sem Dimensões|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|CpuPercentage|Sim|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Instância|
|MemóriaPercentage|Sim|Percentagem de Memória|Percentagem|Média|Percentagem de Memória|Instância|
|Trabalhadores disponíveis|Sim|Trabalhadores disponíveis|Contagem|Média|Trabalhadores disponíveis|Sem Dimensões|
|TrabalhadoresTotal|Sim|Total de Trabalhadores|Contagem|Média|Total de Trabalhadores|Sem Dimensões|
|TrabalhadoresUsed|Sim|Trabalhadores Usados|Contagem|Média|Trabalhadores Usados|Sem Dimensões|


## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|BytesReceived|Sim|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Sim|Data out|Bytes|Total|Data out|Instância|
|CpuPercentage|Sim|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Instância|
|DiskQueueLength|Sim|Comprimento da fila do disco|Contagem|Média|Comprimento da fila do disco|Instância|
|HttpQueueLength|Sim|Comprimento da fila http|Contagem|Média|Comprimento da fila http|Instância|
|MemóriaPercentage|Sim|Percentagem de Memória|Percentagem|Média|Percentagem de Memória|Instância|
|SocketInboundAll|Sim|SocketInboundAll|Contagem|Média|SocketInboundAll|Instância|
|SocketLoopback|Sim|SocketLoopback|Contagem|Média|SocketLoopback|Instância|
|SocketOutboundAll|Sim|SocketOutboundAll|Contagem|Média|SocketOutboundAll|Instância|
|SocketOutboundEstablished|Sim|SocketOutboundEstablished|Contagem|Média|SocketOutboundEstablished|Instância|
|SocketOutboundTimeWait|Sim|SocketOutboundTimeWait|Contagem|Média|SocketOutboundTimeWait|Instância|
|TcpCloseWait|Sim|Espera de fecho de TCP|Contagem|Média|Espera de fecho de TCP|Instância|
|TcpClosing|Sim|Fecho da TCP|Contagem|Média|Fecho da TCP|Instância|
|TcpEstablished|Sim|TCP Estabelecido|Contagem|Média|TCP Estabelecido|Instância|
|TcpFinWait1|Sim|TCP Fin Wait 1|Contagem|Média|TCP Fin Wait 1|Instância|
|TcpFinWait2|Sim|TCP Fin Wait 2|Contagem|Média|TCP Fin Wait 2|Instância|
|TcpLastAck|Sim|TCP Last Ack|Contagem|Média|TCP Last Ack|Instância|
|TcpSynReceived|Sim|Sina TCP recebida|Contagem|Média|Sina TCP recebida|Instância|
|TcpSynsent|Sim|TCP Syn enviado|Contagem|Média|TCP Syn enviado|Instância|
|TcpTimeWait|Sim|Espera de tempo da TCP|Contagem|Média|Espera de tempo da TCP|Instância|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (excluindo funções) 

> [!NOTE]
> **O Uso do Sistema de Ficheiros** é uma nova métrica que está a ser lançada globalmente, não são esperados dados a menos que tenha sido whitelisted para pré-visualização privada.

> [!IMPORTANT]
> **O tempo médio de resposta** será depreciado para evitar confusões com agregações métricas. Utilize o **Tempo de Resposta** como substituto.

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AppConnections|Sim|Ligações|Contagem|Média|Ligações|Instância|
|Média MemoryWorkingSet|Sim|Conjunto de trabalho de memória média|Bytes|Média|Conjunto de trabalho de memória média|Instância|
|AverageResponseTime|Sim|Tempo médio de resposta **(precotado)**|Segundos|Média|Tempo médio de resposta|Instância|
|BytesReceived|Sim|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Sim|Data out|Bytes|Total|Data out|Instância|
|Horário do Cpu|Sim|Hora do CPU|Segundos|Total|Hora do CPU|Instância|
|Antiguidades Atuais|Sim|Atuais Assembleias|Contagem|Média|Atuais Assembleias|Instância|
|FileSystemUsage|Sim|Utilização do sistema de ficheiros|Bytes|Média|Utilização do sistema de ficheiros|Sem Dimensões|
|Gen0Collections|Sim|Coleções de lixo gen 0|Contagem|Total|Coleções de lixo gen 0|Instância|
|Gen1Collections|Sim|Coleções de lixo da Gen 1|Contagem|Total|Coleções de lixo da Gen 1|Instância|
|Gen2Collections|Sim|Coleções de lixo gen 2|Contagem|Total|Coleções de lixo gen 2|Instância|
|Pegas|Sim|N.º de Identificadores|Contagem|Média|N.º de Identificadores|Instância|
|HealthCheckStatus|Sim|Estado do controlo de saúde|Contagem|Média|Estado do controlo de saúde|Instância|
|Http101|Sim|Http 101|Contagem|Total|Http 101|Instância|
|Http2xx|Sim|Http 2xx|Contagem|Total|Http 2xx|Instância|
|Http3xx|Http 3xx|Contagem|Total|Http 3xx|Instância|
|Http401|Sim|Http 401|Contagem|Total|Http 401|Instância|
|Http403|Sim|Http 403|Contagem|Total|Http 403|Instância|
|Http404|Sim|Http 404|Contagem|Total|Http 404|Instância|
|Http406|Sim|Http 406|Contagem|Total|Http 406|Instância|
|Http4xx|Sim|Http 4xx|Contagem|Total|Http 4xx|Instância|
|Http5xx|Sim|http Erros do servidor|Contagem|Total|http Erros do servidor|Instância|
|HttpResponseTime|Sim|Tempo de resposta|Segundos|Média|Tempo de resposta|Instância|
|IoOtherBytesPerSecond|Sim|IO Outros Bytes por Segundo|BytesPerSecond|Total|IO Outros Bytes por Segundo|Instância|
|IoOtherOperationsPerSecond|Sim|IO Outras Operações por Segundo|BytesPerSecond|Total|IO Outras Operações por Segundo|Instância|
|IoReadBytesPerSecond|Sim|IO Ler Bytes por segundo|BytesPerSecond|Total|IO Ler Bytes por segundo|Instância|
|IoReadOperationsPerSecond|Sim|IO Ler Operações por segundo|BytesPerSecond|Total|IO Ler Operações por segundo|Instância|
|IoWriteBytesPerSecond|Sim|IO Escrever Bytes por segundo|BytesPerSecond|Total|IO Escrever Bytes por segundo|Instância|
|IoWriteOperationsPerSecond|Sim|IO Escrever Operações por segundo|BytesPerSecond|Total|IO Escrever Operações por segundo|Instância|
|MemoryWorkingSet|Sim|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|PrivateBytes|Sim|Bytes privados|Bytes|Média|Bytes privados|Instância|
|Pedidos|Sim|Pedidos|Contagem|Total|Pedidos|Instância|
|PedidosInApplicationQueue|Sim|Pedidos na fila de aplicações|Contagem|Média|Pedidos na fila de aplicações|Instância|
|Fios|Sim|Contagem de fios|Contagem|Média|Contagem de fios|Instância|
|Total DeDomínios|Sim|Domínios totais de aplicativos|Contagem|Média|Domínios totais de aplicativos|Instância|
|TotalAppDomainsUnloaded|Sim|Total de domínios de aplicações descarregados|Contagem|Média|Total de domínios de aplicações descarregados|Instância|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funções)

> [!NOTE]
> **O Uso do Sistema de Ficheiros** é uma nova métrica que está a ser lançada globalmente, não são esperados dados a menos que tenha sido whitelisted para pré-visualização privada.

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Média MemoryWorkingSet|Sim|Conjunto de trabalho de memória média|Bytes|Média|Conjunto de trabalho de memória média|Instância|
|BytesReceived|Sim|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Sim|Data out|Bytes|Total|Data out|Instância|
|Antiguidades Atuais|Sim|Atuais Assembleias|Contagem|Média|Atuais Assembleias|Instância|
|FileSystemUsage|Sim|Utilização do sistema de ficheiros|Bytes|Média|Utilização do sistema de ficheiros|Sem Dimensões|
|FunExecutionCount|Sim|Contagem de execução de funções|Contagem|Total|Contagem de execução de funções|Instância|
|FunExecutionUnnits|Sim|Unidades de execução de funções|Contagem|Total|[Unidades de execução de funções](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instância|
|Gen0Collections|Sim|Coleções de lixo gen 0|Contagem|Total|Coleções de lixo gen 0|Instância|
|Gen1Collections|Sim|Coleções de lixo da Gen 1|Contagem|Total|Coleções de lixo da Gen 1|Instância|
|Gen2Collections|Sim|Coleções de lixo gen 2|Contagem|Total|Coleções de lixo gen 2|Instância|
|HealthCheckStatus|Sim|Estado do controlo de saúde|Contagem|Média|Estado do controlo de saúde|Instância|
|Http5xx|Sim|http Erros do servidor|Contagem|Total|http Erros do servidor|Instância|
|IoOtherBytesPerSecond|Sim|IO Outros Bytes por Segundo|BytesPerSecond|Total|IO Outros Bytes por Segundo|Instância|
|IoOtherOperationsPerSecond|Sim|IO Outras Operações por Segundo|BytesPerSecond|Total|IO Outras Operações por Segundo|Instância|
|IoReadBytesPerSecond|Sim|IO Ler Bytes por segundo|BytesPerSecond|Total|IO Ler Bytes por segundo|Instância|
|IoReadOperationsPerSecond|Sim|IO Ler Operações por segundo|BytesPerSecond|Total|IO Ler Operações por segundo|Instância|
|IoWriteBytesPerSecond|Sim|IO Escrever Bytes por segundo|BytesPerSecond|Total|IO Escrever Bytes por segundo|Instância|
|IoWriteOperationsPerSecond|Sim|IO Escrever Operações por segundo|BytesPerSecond|Total|IO Escrever Operações por segundo|Instância|
|MemoryWorkingSet|Sim|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|PrivateBytes|Sim|Bytes privados|Bytes|Média|Bytes privados|Instância|
|PedidosInApplicationQueue|Sim|Pedidos na fila de aplicações|Contagem|Média|Pedidos na fila de aplicações|Instância|
|Total DeDomínios|Sim|Domínios totais de aplicativos|Contagem|Média|Domínios totais de aplicativos|Instância|
|TotalAppDomainsUnloaded|Sim|Total de domínios de aplicações descarregados|Contagem|Média|Total de domínios de aplicações descarregados|Instância|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Métrica|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AppConnections|Sim|Ligações|Contagem|Média|Ligações|Instância|
|Média MemoryWorkingSet|Sim|Conjunto de trabalho de memória média|Bytes|Média|Conjunto de trabalho de memória média|Instância|
|AverageResponseTime|Sim|Tempo médio de resposta|Segundos|Média|Tempo médio de resposta|Instância|
|BytesReceived|Sim|Dados em|Bytes|Total|Dados em|Instância|
|BytesSent|Sim|Data out|Bytes|Total|Data out|Instância|
|Horário do Cpu|Sim|Hora do CPU|Segundos|Total|Hora do CPU|Instância|
|Antiguidades Atuais|Sim|Atuais Assembleias|Contagem|Média|Atuais Assembleias|Instância|
|FileSystemUsage|Sim|Utilização do sistema de ficheiros|Bytes|Média|Utilização do sistema de ficheiros|Sem Dimensões|
|FunExecutionCount|Sim|Contagem de execução de funções|Contagem|Total|Contagem de execução de funções|Instância|
|FunExecutionUnnits|Sim|Unidades de execução de funções|Contagem|Total|Unidades de execução de funções|Instância|
|Gen0Collections|Sim|Coleções de lixo gen 0|Contagem|Total|Coleções de lixo gen 0|Instância|
|Gen1Collections|Sim|Coleções de lixo da Gen 1|Contagem|Total|Coleções de lixo da Gen 1|Instância|
|Gen2Collections|Sim|Coleções de lixo gen 2|Contagem|Total|Coleções de lixo gen 2|Instância|
|Pegas|Sim|N.º de Identificadores|Contagem|Média|N.º de Identificadores|Instância|
|HealthCheckStatus|Sim|Estado do controlo de saúde|Contagem|Média|Estado do controlo de saúde|Instância|
|Http101|Sim|Http 101|Contagem|Total|Http 101|Instância|
|Http2xx|Sim|Http 2xx|Contagem|Total|Http 2xx|Instância|
|Http3xx|Sim|Http 3xx|Contagem|Total|Http 3xx|Instância|
|Http401|Sim|Http 401|Contagem|Total|Http 401|Instância|
|Http403|Sim|Http 403|Contagem|Total|Http 403|Instância|
|Http404|Sim|Http 404|Contagem|Total|Http 404|Instância|
|Http406|Sim|Http 406|Contagem|Total|Http 406|Instância|
|Http4xx|Sim|Http 4xx|Contagem|Total|Http 4xx|Instância|
|Http5xx|Sim|http Erros do servidor|Contagem|Total|http Erros do servidor|Instância|
|HttpResponseTime|Sim|Tempo de resposta|Segundos|Média|Tempo de resposta|Instância|
|IoOtherBytesPerSecond|Sim|IO Outros Bytes por Segundo|BytesPerSecond|Total|IO Outros Bytes por Segundo|Instância|
|IoOtherOperationsPerSecond|Sim|IO Outras Operações por Segundo|BytesPerSecond|Total|IO Outras Operações por Segundo|Instância|
|IoReadBytesPerSecond|Sim|IO Ler Bytes por segundo|BytesPerSecond|Total|IO Ler Bytes por segundo|Instância|
|IoReadOperationsPerSecond|Sim|IO Ler Operações por segundo|BytesPerSecond|Total|IO Ler Operações por segundo|Instância|
|IoWriteBytesPerSecond|Sim|IO Escrever Bytes por segundo|BytesPerSecond|Total|IO Escrever Bytes por segundo|Instância|
|IoWriteOperationsPerSecond|Sim|IO Escrever Operações por segundo|BytesPerSecond|Total|IO Escrever Operações por segundo|Instância|
|MemoryWorkingSet|Sim|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|PrivateBytes|Sim|Bytes privados|Bytes|Média|Bytes privados|Instância|
|Pedidos|Sim|Pedidos|Contagem|Total|Pedidos|Instância|
|PedidosInApplicationQueue|Sim|Pedidos na fila de aplicações|Contagem|Média|Pedidos na fila de aplicações|Instância|
|Fios|Sim|Contagem de fios|Contagem|Média|Contagem de fios|Instância|
|Total DeDomínios|Sim|Domínios totais de aplicativos|Contagem|Média|Domínios totais de aplicativos|Instância|
|TotalAppDomainsUnloaded|Sim|Total de domínios de aplicações descarregados|Contagem|Média|Total de domínios de aplicações descarregados|Instância|


## <a name="next-steps"></a>Passos seguintes
* [Leia sobre métricas no Azure Monitor](data-platform.md)
* [Criar alertas para as métricas](alerts-overview.md)
* [Métricas de exportação para armazenamento, Centro de Eventos ou Log Analytics](platform-logs-overview.md)
