---
title: Azure Monitor suportado métricas por tipo de recurso
description: Lista de métricas disponíveis para cada tipo de recurso com monitor Azure.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 02/06/2021
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 51cd3bf3349e1880d4dc9f5d98d2d9b5dc5bb228
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100616681"
---
# <a name="supported-metrics-with-azure-monitor"></a>Métricas suportadas com monitor Azure

> [!NOTE]
> Esta lista é gerada em grande parte por si. Qualquer modificação feita a esta lista via GitHub pode ser escrita sem aviso prévio. Contacte o autor deste artigo para obter mais detalhes sobre como fazer atualizações permanentes.

O Azure Monitor fornece várias formas de interagir com as métricas, incluindo cartografá-las no portal, acessá-las através da API REST, ou questioná-las usando PowerShell ou CLI. 

Este artigo é uma lista completa de todas as métricas da plataforma (isto é, automaticamente recolhidas) atualmente disponíveis com o pipeline métrico consolidado do Azure Monitor. As métricas alteradas ou adicionadas após a data no topo deste artigo podem ainda não aparecer abaixo. Para consultar e aceder à lista de métricas programáticamente, utilize a [versão api 2018-01-01](/rest/api/monitor/metricdefinitions). Outras métricas que não constam desta lista podem estar disponíveis no portal ou usando APIs legados.

As métricas são organizadas por fornecedores de recursos e tipo de recursos. Para obter uma lista de serviços e os fornecedores e tipos de recursos que lhes pertencem, consulte [os fornecedores de recursos para os serviços Azure.](../../azure-resource-manager/management/azure-services-resource-providers.md)  

## <a name="exporting-platform-metrics-to-other-locations"></a>Métricas da plataforma de exportação para outros locais

Pode exportar as métricas da plataforma do gasoduto Azure monitor para outros locais de duas maneiras.
1. Use as [métricas REST API](/rest/api/monitor/metrics/list)
2. Use [definições de diagnóstico](../essentials/diagnostic-settings.md) para encaminhar as métricas da plataforma para 
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
> A melhor prática é utilizar e configurar a [extensão Azure Diagnostics](../agents/diagnostics-extension-overview.md) para enviar métricas de desempenho do SO para a mesma base de dados métrica do Azure Monitor onde as métricas da plataforma são armazenadas. A extensão encaminha as métricas de SO do hóspede através das [métricas personalizadas](../essentials/metrics-custom-overview.md) API. Em seguida, você pode cartografar, alertar e de outra forma usar métricas de SO convidados como métricas de plataforma. Em alternativa ou além disso, pode utilizar o agente Log Analytics para enviar métricas de SO para registos de monitores Azure / Log Analytics. Lá pode consultar essas métricas em combinação com dados não métricos. 

Para obter informações adicionais importantes, consulte [a visão geral dos agentes de monitorização.](../agents/agents-overview.md)

## <a name="table-formatting"></a>Formatação de mesa

> [!IMPORTANT] 
> Esta última atualização adiciona uma nova coluna e reordenou as métricas para serem alfabéticas. As informações de adição significam que as tabelas abaixo podem ter uma barra de deslocação horizontal na parte inferior, dependendo da largura da janela do seu navegador. Se acredita que está a perder informação, use a barra de pergaminho para ver toda a mesa.

## <a name="microsoftaadiamazureadmetrics"></a>microsoft.aadiam/azureADMetrics

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ThrottledRequests|No|ThrottledRequests|de palavras|Média|azureADMetrics tipo métrica|Sem Dimensões|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servidores

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|Yes|Memória: Preço corrente mais limpo|de palavras|Média|Preço atual da memória, $/byte/time, normalizado para 1000.|ServerResourceType|
|CleanerMemoryNonshrinkable|Yes|Memória: Memória limpa não é insuportável|Bytes|Média|Quantidade de memória, em bytes, não sujeita a purga pelo limpador de fundos.|ServerResourceType|
|LimpadorMesshrinkable|Yes|Memória: Memória mais limpa encolhível|Bytes|Média|Quantidade de memória, em bytes, sujeito a purga pelo limpador de fundos.|ServerResourceType|
|CommandPoolBusyThreads|Yes|Threads: Linhas movimentadas da piscina de comando|de palavras|Média|Número de fios ocupados na piscina de fio de comando.|ServerResourceType|
|CommandPoolIdleThreads|Yes|Fios: Fios inativos da piscina de comando|de palavras|Média|Número de fios inativos na piscina do fio de comando.|ServerResourceType|
|ComandoPoolJobQueueLength|Yes|Comprimento da fila de trabalho da piscina de comando|de palavras|Média|Número de empregos na fila da piscina de fios de comando.|ServerResourceType|
|Correntes Deconhecões|Yes|Ligação: Ligações atuais|de palavras|Média|Número atual de ligações ao cliente estabelecidas.|ServerResourceType|
|Atuais Sesessãos de 2009|Yes|Sessões de Utilizador atuais|de palavras|Média|Número atual de sessões de utilizador estabelecidas.|ServerResourceType|
|LongParsingBusyThreads|Yes|Threads: Longas análises de fios ocupados|de palavras|Média|Número de fios ocupados na piscina de rosca de análise longa.|ServerResourceType|
|LongParsingIdleThreads|Yes|Threads: Fios longos de análise ocioso|de palavras|Média|Número de fios ociosos na piscina de rosca de análise longa.|ServerResourceType|
|LongParsingJobQueueLength|Yes|Threads: Comprimento da fila de trabalho de análise longa|de palavras|Média|Número de empregos na fila da piscina de rosca de longas análises.|ServerResourceType|
|mashup_engine_memory_metric|Yes|Memória do motor M|Bytes|Média|Utilização da memória por processos do motor de mashup|ServerResourceType|
|mashup_engine_private_bytes_metric|Yes|M Motor Private Bytes|Bytes|Média|Bytes privados utiliza-se através de processos de motor de mashup.|ServerResourceType|
|mashup_engine_qpu_metric|Yes|QPU do motor M|de palavras|Média|Utilização do QPU por processos de motor de mashup|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Yes|M Motor Virtual Bytes|Bytes|Média|Uso de bytes virtuais por processos de motor de mashup.|ServerResourceType|
|memory_metric|Yes|Memória|Bytes|Média|Memória. Gama 0-25 GB para S1, 0-50 GB para S2 e 0-100 GB para S4|ServerResourceType|
|memory_thrashing_metric|Yes|Degradação de Memória|Percentagem|Média|Memória média a bater.|ServerResourceType|
|MemoryLimitHard|Yes|Memória: Limite de memória difícil|Bytes|Média|Limite de memória rígido, a partir do ficheiro de configuração.|ServerResourceType|
|MemóriaLimithigh|Yes|Memória: Limite de memória Alto|Bytes|Média|Limite de memória elevado, a partir do ficheiro de configuração.|ServerResourceType|
|MemoryLimitLow|Yes|Memória: Limite de memória baixo|Bytes|Média|Limite de memória baixo, a partir do ficheiro de configuração.|ServerResourceType|
|MemoryLimitVertiPaq|Yes|Memória: Limite de memória VertiPaq|Bytes|Média|Limite de memória, a partir do ficheiro de configuração.|ServerResourceType|
|MemóriaSage|Yes|Memória: Utilização da memória|Bytes|Média|Utilização da memória do processo do servidor como usado no cálculo do preço de memória mais limpo. Igual a contrariar o Processo\PrivateBytes mais o tamanho dos dados mapeados pela memória, ignorando qualquer memória que tenha sido mapeada ou atribuída pelo motor de análise em memória xVelocity (VertiPaq) em excesso do limite de memória do motor xVelocity.|ServerResourceType|
|private_bytes_metric|Yes|Bytes privados|Bytes|Média|Bytes privados.|ServerResourceType|
|ProcessamentoPoolBusyIOJobThreads|Yes|Threads: Processamento de piscina ocupada linhas de trabalho de I/O|de palavras|Média|Número de fios a executar trabalhos de E/S na piscina de fios de processamento.|ServerResourceType|
|ProcessamentoPoolBusyNonIOThreads|Yes|Fios: Piscina de processamento ocupada fios não-I/O|de palavras|Média|Número de fios que executam trabalhos não-I/S na piscina de rosca de processamento.|ServerResourceType|
|ProcessamentoPoolIdleIOJobThreads|Yes|Fios: Processamento de linhas de trabalho I/O inativas da piscina|de palavras|Média|Número de fios inativos para trabalhos de E/S na piscina de rosca de processamento.|ServerResourceType|
|ProcessamentoPoolIdleNonIOThreads|Yes|Fios: Processamento de piscinas ociosas fios não-I/O|de palavras|Média|Número de fios inativos na piscina de fios de processamento dedicada a trabalhos não-I/S.|ServerResourceType|
|ProcessamentoPoolIOJobQueueLength|Yes|Threads: Processamento de piscina I/O comprimento da fila de trabalho|de palavras|Média|Número de trabalhos de E/S na fila da piscina de rosca de processamento.|ServerResourceType|
|ProcessamentoPoolJobQueueLength|Yes|Processamento de comprimento da fila de trabalho da piscina|de palavras|Média|Número de trabalhos não-I/O na fila da piscina de rosca de processamento.|ServerResourceType|
|qpu_metric|Yes|QPU|de palavras|Média|QPU. Intervalo 0-100 para S1, 0-200 para S2 e 0-400 para S4|ServerResourceType|
|QueryPoolBusyThreads|Yes|Linhas ocupadas da piscina de consulta|de palavras|Média|Número de fios ocupados na piscina de rosca de consulta.|ServerResourceType|
|QueryPoolIdleThreads|Yes|Fios: Linhas ociosas da piscina de consulta|de palavras|Média|Número de fios inativos para trabalhos de E/S na piscina de rosca de processamento.|ServerResourceType|
|QueryPoolJobQueueLength|Yes|Threads: Consulta de trabalho na piscina fila lengt|de palavras|Média|Número de empregos na fila da piscina de fios de consulta.|ServerResourceType|
|Quota|Yes|Memória: Quota|Bytes|Média|Quota de memória atual, em bytes. A quota de memória também é conhecida como um subsídio de memória ou reserva de memória.|ServerResourceType|
|Quota Bloqueada|Yes|Memória: Quota Bloqueada|de palavras|Média|Número atual de pedidos de quotas que são bloqueados até que outras quotas de memória sejam libertadas.|ServerResourceType|
|RowsConvertedPerSec|Yes|Processamento: Linhas convertidas por seg|CondePerSecond|Média|Taxa de linhas convertidas durante o processamento.|ServerResourceType|
|RowsReadPerSec|Yes|Processamento: Linhas lidas por seg|CondePerSecond|Média|Taxa de linhas lidas de todas as bases de dados relacionais.|ServerResourceType|
|RowsWrittenPerSec|Yes|Processamento: Linhas escritas por seg|CondePerSecond|Média|Taxa de linhas escritas durante o processamento.|ServerResourceType|
|ShortParsingBusyThreads|Yes|Threads: Threads de análise curta|de palavras|Média|Número de fios ocupados na piscina de rosca de parsing curta.|ServerResourceType|
|ShortParsingIdleThreads|Yes|Fios: Fios curtos de parsing ocioso|de palavras|Média|Número de fios ociosos na piscina de rosca de parsing curta.|ServerResourceType|
|ShortParsingJobQueueLength|Yes|Threads: Comprimento da fila do trabalho de análise curta|de palavras|Média|Número de empregos na fila da piscina de rosca de pequena análise.|ServerResourceType|
|SuccessfullConnectionsPerSec|Yes|Conexões de sucesso por seg|CondePerSecond|Média|Taxa de conclusão de conexão bem sucedida.|ServerResourceType|
|TotalConnectionFailures|Yes|Falhas totais de ligação|de palavras|Média|Tentativas de ligação falhadas totais.|ServerResourceType|
|TotalConnectionRequests|Yes|Total de pedidos de conexão|de palavras|Média|Pedidos de ligação total. Estas são as chegadas.|ServerResourceType|
|VertiPaqNonpaged|Yes|Memória: VertiPaq Não Pageed|Bytes|Média|Bytes de memória bloqueados no conjunto de trabalho para utilização pelo motor de memória.|ServerResourceType|
|VertiPaqPaged|Yes|Memória: VertiPaq Paged|Bytes|Média|Bytes de memória paged em uso para dados de memória.|ServerResourceType|
|virtual_bytes_metric|Yes|Bytes Virtuais|Bytes|Média|Bytes virtuais.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|BackendDuration|Yes|Duração dos Pedidos de Backend|Milissegundos|Média|Duração dos Pedidos de Backend em milissegundos|Localização, Nome anfitrião|
|Capacidade|Yes|Capacidade|Percentagem|Média|Métrica de utilização para o serviço ApiManagement|Localização|
|Duração|Yes|Duração geral dos pedidos de gateway|Milissegundos|Média|Duração global dos pedidos gateway em milissegundos|Localização, Nome anfitrião|
|EventHubDroppedEvents|Yes|Eventos do EventHub abandonados|de palavras|Total|Número de eventos ignorados devido ao limite de tamanho da fila atingido|Localização|
|EventosHubRejectedEvents|Yes|Eventos de EventHub rejeitados|de palavras|Total|Número de eventos rejeitados do EventHub (configuração errada ou não autorizado)|Localização|
|EventoSHubSuccessfulEvents|Yes|Eventos de sucesso EventHub|de palavras|Total|Número de eventos de sucesso do EventHub|Localização|
|EventHubThrottledEvents|Yes|Eventos Throttled EventHub|de palavras|Total|Número de eventos acelerados eventHub|Localização|
|EventHubTimedoutEvents|Yes|Eventos Timed out EventHub|de palavras|Total|Número de eventos do EventHub cronometrado|Localização|
|EventHubTotalBytesSent|Yes|Tamanho dos eventos Do EventHub|Bytes|Total|Tamanho total dos eventos EventHub em bytes|Localização|
|EventHubTotalEvents|Yes|Total de eventos EventHub|de palavras|Total|Número de eventos enviados para o EventHub|Localização|
|EventHubTotalFailedEvents|Yes|Eventos falhados do EventHub|de palavras|Total|Número de eventos falhados do EventHub|Localização|
|Requessos Falhados|Yes|Pedidos de Gateway falhados (Deprecados)|de palavras|Total|Número de falhas nos pedidos de gateway - Use a métrica de pedido de várias dimensões com a dimensão GatewayResponseCodeCategoria|Localização, Nome anfitrião|
|RedeContividade|Yes|Estado da conectividade da rede dos recursos (pré-visualização)|de palavras|Média|Estado de conectividade da rede dos tipos de recursos dependentes do serviço de Gestão API|Localização,Tip de Recursos|
|Outros Requests|Yes|Outros pedidos de gateway (precotados)|de palavras|Total|Número de outros pedidos de gateway - Use a métrica de pedido de várias dimensões com a dimensão GatewayResponseCodeCategoria|Localização, Nome anfitrião|
|Pedidos|Yes|Pedidos|de palavras|Total|Métricas de pedido de gateway com múltiplas dimensões|Localização, Nome anfitrião, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory, GatewayResponseCodeCategory|
|Requess de sucesso|Yes|Pedidos de Gateway bem sucedidos (Deprecados)|de palavras|Total|Número de pedidos de gateway bem sucedidos - Use a métrica de pedido de várias dimensões com a dimensão GatewayResponseCodeCategoria|Localização, Nome anfitrião|
|TotalRequests|Yes|Total de pedidos de gateway (precotado)|de palavras|Total|Número de pedidos de gateway - Use métrica de pedido de várias dimensões com dimensão GatewayResponseCodeCategoria|Localização, Nome anfitrião|
|Requessionais não autorizados|Yes|Pedidos de Gateway não autorizados (Deprecados)|de palavras|Total|Número de pedidos de gateway não autorizados - Use a métrica de pedido de várias dimensões com a dimensão GatewayResponseCodeCategoria|Localização, Nome anfitrião|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|Yes|HttpIncomingRequestCount|de palavras|de palavras|Número total de pedidos de http recebidas.|Código de Estado, Autenticação|
|HttpIncomingRequestDuration|Yes|HttpIncomingRequestDuration|de palavras|Média|Latência num pedido http.|Código de Estado, Autenticação|
|ThrottledHttpRequestCount|Yes|ThrottledHttpRequestCount|de palavras|de palavras|Solicitações http throttled.|Sem Dimensões|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/primavera

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|contagem de temporizador ativo|Yes|System.Runtime|contagem de temporizador ativo|de palavras|Média|Número de temporizadores que estão atualmente ativos|Implantação, AppName, Pod|
|taxa alloc|Yes|System.Runtime|taxa alloc|Bytes|Média|Número de bytes atribuídos na pilha gerida|Implantação, AppName, Pod|
|AppCpuUsage|Yes|Utilização do CPU da aplicação (pré-visualização)|Percentagem|Média|O recente uso do CPU para a app|Implantação, AppName, Pod|
|montagem contagem|Yes|System.Runtime|montagem contagem|de palavras|Média|Número de Conjuntos Carregados|Implantação, AppName, Pod|
|cpu-uso|Yes|System.Runtime|cpu-uso|Percentagem|Média|% do tempo que o processo utilizou o CPU|Implantação, AppName, Pod|
|pedidos correntes|Yes|Microsoft.AspNetCore.Hosting|pedidos correntes|de palavras|Média|Número total de pedidos no processamento durante a vida útil do processo|Implantação, AppName, Pod|
|exceção-contagem|Yes|System.Runtime|exceção-contagem|de palavras|Total|Número de Exceções|Implantação, AppName, Pod|
|pedidos falhados|Yes|Microsoft.AspNetCore.Hosting|pedidos falhados|de palavras|Média|Número total de pedidos falhados durante a vida útil do processo|Implantação, AppName, Pod|
|gc-tamanho-heap|Yes|System.Runtime|gc-tamanho-heap|de palavras|Média|Tamanho total da pilha reportado pelo GC (MB)|Implantação, AppName, Pod|
|gen-0-gc-contagem|Yes|System.Runtime|gen-0-gc-contagem|de palavras|Média|Número de Gen 0 GCs|Implantação, AppName, Pod|
|género-0 tamanho|Yes|System.Runtime|género-0 tamanho|Bytes|Média|Tamanho do monte Gen 0|Implantação, AppName, Pod|
|gen-1-gc-contagem|Yes|System.Runtime|gen-1-gc-contagem|de palavras|Média|System.Runtime|Número de Gen 1 GCs|Implantação, AppName, Pod|
|tamanho gen-1|Yes|System.Runtime|tamanho gen-1|Bytes|Média|Tamanho da pilha gen 1|Implantação, AppName, Pod|
|gen-2-gc-contagem|Yes|System.Runtime|gen-2-gc-contagem|de palavras|Média|Número de GCs da Gen 2|Implantação, AppName, Pod|
|tamanho gen-2|Yes|System.Runtime|tamanho gen-2|Bytes|Média|Tamanho do monte Gen 2|Implantação, AppName, Pod|
|jvm.gc.live.data.size|Yes|jvm.gc.live.data.size|Bytes|Média|Tamanho da piscina de memória de geração velha depois de um GC completo|Implantação, AppName, Pod|
|jvm.gc.max.data.size|Yes|jvm.gc.max.data.size|Bytes|Média|Tamanho máximo da piscina de memória de geração antiga|Implantação, AppName, Pod|
|jvm.gc.memory.alocado|Yes|jvm.gc.memory.alocado|Bytes|Máximo|Incrementado para um aumento no tamanho do pool de memória de geração jovem após um GC para antes do próximo|Implantação, AppName, Pod|
|jvm.gc.memory.promovido|Yes|jvm.gc.memory.promovido|Bytes|Máximo|Contagem de aumentos positivos no tamanho do pool de memória de geração antiga antes da GC para depois de GC|Implantação, AppName, Pod|
|jvm.gc.pause.total.count|Yes|jvm.gc.pause.total.count|de palavras|Total|Contagem de pausas GC|Implantação, AppName, Pod|
|jvm.gc.pause.total.time|Yes|jvm.gc.pause.total.time|Milissegundos|Total|GC Pausa Tempo Total|Implantação, AppName, Pod|
|jvm.memory.committed|Yes|jvm.memory.committed|Bytes|Média|Memória atribuída a JVM em bytes|Implantação, AppName, Pod|
|jvm.memória.max|Yes|jvm.memória.max|Bytes|Máximo|A quantidade máxima de memória em bytes que pode ser usada para a gestão da memória|Implantação, AppName, Pod|
|jvm.memory.usado|Yes|jvm.memory.usado|Bytes|Média|Memória de aplicativos utilizada em bytes|Implantação, AppName, Pod|
|loh tamanho|Yes|System.Runtime|loh tamanho|Bytes|Média|Tamanho do monte LOH|Implantação, AppName, Pod|
|monitor-lock-contention-count|Yes|System.Runtime|monitor-lock-contention-count|de palavras|Média|Número de vezes houve contenção ao tentar pegar o bloqueio do monitor|Implantação, AppName, Pod|
|process.cpu.usage|Yes|process.cpu.usage|Percentagem|Média|O recente uso do CPU para o processo JVM|Implantação, AppName, Pod|
|pedidos por segundo|Yes|Microsoft.AspNetCore.Hosting|taxa de pedidos|de palavras|Média|Taxa de pedido|Implantação, AppName, Pod|
|system.cpu.usage|Yes|system.cpu.usage|Percentagem|Média|O recente uso do CPU para todo o sistema|Implantação, AppName, Pod|
|threadpool-complete-items-contagem|Yes|System.Runtime|threadpool-complete-items-contagem|de palavras|Média|Contagem de itens de trabalho concluídos ThreadPool|Implantação, AppName, Pod|
|threadpool-fila-comprimento|Yes|System.Runtime|threadpool-fila-comprimento|de palavras|Média|Comprimento da fila de itens de trabalho threadpool|Implantação, AppName, Pod|
|threadpool-thread-contagem|Yes|System.Runtime|threadpool-thread-contagem|de palavras|Média|Número de threadpool threads|Implantação, AppName, Pod|
|time-in-gc|Yes|System.Runtime|time-in-gc|Percentagem|Média|% tempo em GC desde o último GC|Implantação, AppName, Pod|
|tomcat.global.erro|Yes|tomcat.global.erro|de palavras|Total|Erro Global de Tomcat|Implantação, AppName, Pod|
|tomcat.global.recebeu|Yes|tomcat.global.recebeu|Bytes|Total|Tomcat Total Recebido Bytes|Implantação, AppName, Pod|
|tomcat.global.request.avg.time|Yes|tomcat.global.request.avg.time|Milissegundos|Média|Tomcat Pede tempo médio|Implantação, AppName, Pod|
|tomcat.global.request.max|Yes|tomcat.global.request.max|Milissegundos|Máximo|Tomcat Request Max Time|Implantação, AppName, Pod|
|tomcat.global.request.total.count|Yes|tomcat.global.request.total.count|de palavras|Total|Tomcat Request Contagem Total|Implantação, AppName, Pod|
|tomcat.global.request.total.time|Yes|tomcat.global.request.total.time|Milissegundos|Total|Pedido tomcat tempo total|Implantação, AppName, Pod|
|tomcat.global.enviado|Yes|tomcat.global.enviado|Bytes|Total|Tomcat Total Enviado Bytes|Implantação, AppName, Pod|
|tomcat.sessions.ative.current|Yes|tomcat.sessions.ative.current|de palavras|Total|Contagem ativa da sessão de Tomcat|Implantação, AppName, Pod|
|tomcat.sessions.ative.max|Yes|tomcat.sessions.ative.max|de palavras|Total|Tomcat Session Max Ative Count|Implantação, AppName, Pod|
|tomcat.sessions.alive.max|Yes|tomcat.sessions.alive.max|Milissegundos|Máximo|Tomcat Session Max Alive Tempo|Implantação, AppName, Pod|
|tomcat.sessions.created|Yes|tomcat.sessions.created|de palavras|Total|Tomcat Session Criou Contagem|Implantação, AppName, Pod|
|tomcat.sessions.expirou|Yes|tomcat.sessions.expirou|de palavras|Total|Contagem expirada da sessão de Tomcat|Implantação, AppName, Pod|
|tomcat.sessions.rejeitado|Yes|tomcat.sessions.rejeitado|de palavras|Total|Contagem rejeitada da sessão de Tomcat|Implantação, AppName, Pod|
|tomcat.threads.config.max|Yes|tomcat.threads.config.max|de palavras|Total|Tomcat Config Max Thread Count|Implantação, AppName, Pod|
|tomcat.threads.current|Yes|tomcat.threads.current|de palavras|Total|Contagem de fios de corrente tomcat|Implantação, AppName, Pod|
|pedidos totais|Yes|Microsoft.AspNetCore.Hosting|pedidos totais|de palavras|Média|Número total de pedidos durante a vida útil do processo|Implantação, AppName, Pod|
|conjunto de trabalho|Yes|System.Runtime|conjunto de trabalho|de palavras|Média|Quantidade de conjunto de trabalho utilizado pelo processo (MB)|Implantação, AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automation

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|TotalJob|Yes|Total de empregos|de palavras|Total|O número total de empregos|Runbook, Status|
|TotalUpdateDeploymentMachineRuns|Yes|Funciona a máquina de implementação total de atualizações|de palavras|Total|A máquina de implementação total de atualização de software é executada numa implementação de atualização de software|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|Yes|Execuções de implementação de atualização total|de palavras|Total|Total de execuções de atualização de software executa|SoftwareUpdateConfigurationName, Estado|


## <a name="microsoftavsprivateclouds"></a>Microsoft.AVS/privateClouds

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|CapacidadeLateste|Yes|Capacidade total do disco de datastore|Bytes|Média|A capacidade total do disco na datastore|nome dsname|
|DiskUsedPercentage|Yes| Disco percentual de datastore utilizado|Percentagem|Média|Por cento do disco disponível usado na Datastore|nome dsname|
|EficáciaCpuAverage|Yes|Percentagem da CPU|Percentagem|Média|Percentagem de recursos de CPU usados no Cluster|nome de cluster|
|EficazMemAverage|Yes|Memória média eficaz|Bytes|Média|Quantidade total disponível de memória da máquina em cluster|nome de cluster|
|OverheadAverage|Yes|Despesa média de memória|Bytes|Média|Memória física hospedeira consumida pela infraestrutura de virtualização|nome de cluster|
|TotalMbAverage|Yes|Memória total média|Bytes|Média|Memória total em cluster|nome de cluster|
|UsageAverage|Yes|Uso médio da memória|Percentagem|Média|Utilização da memória em percentagem do total de memória configurada ou disponível|nome de cluster|
|UsadoLatest|Yes|Disco de datastore utilizado|Bytes|Média|A quantidade total de disco utilizado na datastore|nome dsname|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batcontas ch/batch

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|CoreCount|No|Contagem de núcleos dedicada|de palavras|Total|Número total de núcleos dedicados na conta do lote|Sem Dimensões|
|Criação NodeCount|No|Criação de Contagem de Nó|de palavras|Total|Número de nós a ser criado|Sem Dimensões|
|IdleNodeCount|No|Conde nó inativo|de palavras|Total|Número de nós ociosos|Sem Dimensões|
|JobDeleteCompleteEvent|Yes|Trabalho Eliminar Eventos Completos|de palavras|Total|Número total de postos de trabalho que foram eliminados com êxito.|jobId|
|JobDeleteStartEvent|Yes|Trabalho eliminar eventos iniciá-lo|de palavras|Total|Número total de postos de trabalho que foram solicitados para serem suprimidos.|jobId|
|JobDisableCompleteEvent|Yes|Eventos completos para desativação de emprego|de palavras|Total|Número total de postos de trabalho que foram incapacitados com sucesso.|jobId|
|JobDisableStartEvent|Yes|Eventos de início de desativação de emprego|de palavras|Total|Número total de postos de trabalho que foram solicitados para serem incapacitados.|jobId|
|JobStartEvent|Yes|Eventos de início de emprego|de palavras|Total|Número total de postos de trabalho que foram iniciados com sucesso.|jobId|
|JobTerminateCompleteEvent|Yes|Emprego termina eventos completos|de palavras|Total|Número total de postos de trabalho que foram encerrados com sucesso.|jobId|
|JobTerminateStartEvent|Yes|Eventos de início de emprego terminam|de palavras|Total|Número total de postos de trabalho que foram solicitados para serem encerrados.|jobId|
|LeavingPoolNodeCount|No|Deixando a contagem do nó de piscina|de palavras|Total|Número de nós que saem da Piscina|Sem Dimensões|
|LowPriorityCoreCount|No|Contagem do Núcleo de BaixaPrioridade|de palavras|Total|Número total de núcleos de baixa prioridade na conta do lote|Sem Dimensões|
|OfflineNodeCount|No|Contagem de nóles offline|de palavras|Total|Número de nós offline|Sem Dimensões|
|Evento PoolCreate|Yes|Criar Eventos de Criação de Piscinas|de palavras|Total|Número total de piscinas que foram criadas|poolId|
|PoolDeleteCompleteEvent|Yes|Piscina Eliminar Eventos Completos|de palavras|Total|Número total de eliminações de piscinas que tenham concluído|poolId|
|PoolDeleteStartEvent|Yes|Pool Delete Eventos iniciá-lo|de palavras|Total|Número total de eliminações de piscinas que começaram|poolId|
|PoolResizeCompleteEvent|Yes|Piscina Redimensione Eventos Completos|de palavras|Total|Número total de redimensionações de piscina que completaram|poolId|
|PoolResizeStartEvent|Yes|Eventos de início de piscina redimensionam|de palavras|Total|Número total de redimensionações de piscina que começaram|poolId|
|PreemptedNodeCount|No|Contagem de nódoaista preventiva|de palavras|Total|Número de nós pré-emitidos|Sem Dimensões|
|RebootingNodeCount|No|Contagem de nó de reinicialização|de palavras|Total|Número de nós reiniciantes|Sem Dimensões|
|ReimagingNodeCount|No|Contagem de nó de reimaging|de palavras|Total|Número de nós de reimaging|Sem Dimensões|
|RunningNodeCount|No|Contagem de nó de corrida|de palavras|Total|Número de nós em execução|Sem Dimensões|
|InícioNodeCount|No|Contagem inicial do nó|de palavras|Total|Número de nós a partir|Sem Dimensões|
|StartTaskFailedNodeCount|No|Início da contagem de nó falhado da tarefa|de palavras|Total|Número de nós onde a Tarefa inicial falhou|Sem Dimensões|
|TaskCompleteEvent|Yes|Eventos completos de tarefas|de palavras|Total|Número total de tarefas que completaram|poolId, jobId|
|TaskFailEvent|Yes|Eventos de Falha de Tarefa|de palavras|Total|Número total de tarefas que foram concluídas num estado falhado|poolId, jobId|
|TaskStartEvent|Yes|Eventos de início de tarefa|de palavras|Total|Número total de tarefas que começaram|poolId, jobId|
|TotalLowPriorityNodeCount|No|Conde Low-Priority nó|de palavras|Total|Número total de nós de baixa prioridade na conta do lote|Sem Dimensões|
|TotalNodeCount|No|Conde de Nó dedicado|de palavras|Total|Número total de nós dedicados na conta do lote|Sem Dimensões|
|Não utilizávelNodeCount|No|Contagem de nó inutilizáveis|de palavras|Total|Número de nós inutilizáveis|Sem Dimensões|
|WaitingForStartTaskNodeCount|No|À espera da contagem de nó de tarefa inicial|de palavras|Total|Número de nós à espera que a Tarefa inicial esteja concluída|Sem Dimensões|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/espaços de trabalho

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Núcleos Ativos|Yes|Núcleos Ativos|de palavras|Média|Número de núcleos ativos|Cenário, ClusterName|
|Nóns ativos|Yes|Nóns ativos|de palavras|Média|Número de nós em execução|Cenário, ClusterName|
|Núcleos Ociosos|Yes|Núcleos Ociosos|de palavras|Média|Número de núcleos ociosos|Cenário, ClusterName|
|Nódoas Ociosas|Yes|Nódoas Ociosas|de palavras|Média|Número de nós ociosos|Cenário, ClusterName|
|Trabalho Concluído|Yes|Trabalho Concluído|de palavras|Total|Número de postos de trabalho concluídos|Cenário, ClusterName, ResultadoType|
|Trabalho submetido|Yes|Trabalho submetido|de palavras|Total|Número de postos de trabalho submetidos|Cenário, ClusterName|
|Deixando os Núcleos|Yes|Deixando os Núcleos|de palavras|Média|Número de núcleos de saída|Cenário, ClusterName|
|Deixando os nóns|Yes|Deixando os nóns|de palavras|Média|Número de nós de saída|Cenário, ClusterName|
|Núcleos Preempted|Yes|Núcleos Preempted|de palavras|Média|Número de núcleos pré-apropriados|Cenário, ClusterName|
|Nómadas Preempted|Yes|Nómadas Preempted|de palavras|Média|Número de nós pré-emitidos|Cenário, ClusterName|
|Percentagem de Utilização de Cots|Yes|Percentagem de Utilização de Cots|de palavras|Média|Por cento das quotas utilizadas|Cenário, ClusterName, VmFamilyName, VmPriority|
|Núcleos Totais|Yes|Núcleos Totais|de palavras|Média|Número de núcleos totais|Cenário, ClusterName|
|Nómada Total|Yes|Nómada Total|de palavras|Média|Número de nós totais|Cenário, ClusterName|
|Núcleos inutilizáveis|Yes|Núcleos inutilizáveis|de palavras|Média|Número de núcleos inutilizáveis|Cenário, ClusterName|
|Nódes Inutilizáveis|Yes|Nódes Inutilizáveis|de palavras|Média|Número de nós inutilizáveis|Cenário, ClusterName|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|Yes|BroadcastProcessedCountDisplayName|de palavras|Média|O número de transações processadas.|Nó, canal, tipo, estado|
|CadeiacodeExecuteTimeouts|Yes|ChaincodeExecuteTimeoutsDisplayName|de palavras|Média|O número de execuções de código de corrente (Init ou Invoke) que têm esgotado o tempo.|Nó, código de corrente|
|CadeiacodeLaunchFailures|Yes|CadeiacodeLaunchFailuresDisplayName|de palavras|Média|O número de lançamentos de código de corrente que falharam.|Nó, código de corrente|
|CadeiacodeLaunchTimeouts|Yes|ChaincodeLaunchTimeoutsDisplayName|de palavras|Média|O número de lançamentos de código de corrente que têm esgotado o tempo.|Nó, código de corrente|
|ChaincodeShimRequestsCompleted|Yes|ChaincodeShimRequestsCompletedDisplayName|de palavras|Média|O número de pedidos de calço de código de corrente preenchidos.|Nó, tipo, canal, código de corrente, sucesso|
|Código de correnteShimRequestsReceived|Yes|ChaincodeShimRequestsReceivedDisplayName|de palavras|Média|O número de pedidos de calços de código de corrente recebidos.|Nó, tipo, canal, código de corrente|
|ClusterCommEgressQueueCapy|Yes|ClusterCommEgressQueueCapyDisplayName|de palavras|Média|Capacidade da fila da saída.|Nó, anfitrião, msg_type, canal|
|ClusterCommEgressQueueLength|Yes|ClusterCommEgressQueueLengthDisplayName|de palavras|Média|Comprimento da fila da saída.|Nó, anfitrião, msg_type, canal|
|ClusterCommEgressQueueWorkers|Yes|ClusterCommEgressQueueWorkersDisplayName|de palavras|Média|Conde de trabalhadores da fila de saídas.|Nó, canal|
|ClusterCommEgressStreamCount|Yes|ClusterCommEgressStreamCountDisplayName|de palavras|Média|Contagem de riachos para outros nós.|Nó, canal|
|ClusterCommEgressTlsConnectionCount|Yes|ClusterCommEgressTlsConnectionCountDisplayName|de palavras|Média|Contagem de ligações TLS a outros nós.|Nó|
|ClusterCommIngressStreamCount|Yes|ClusterCommIngressStreamCountDisplayName|de palavras|Média|Contagem de riachos de outros nós.|Nó|
|ClusterCommMsgDroppedCount|Yes|ClusterCommMsgDroppedCountDisplayName|de palavras|Média|Contagem de mensagens caídas.|Nó, anfitrião, canal|
|LigaçãoAccepted|Yes|Conexões Aceites|de palavras|Total|Conexões Aceites|Nó|
|ConexãoActiva|Yes|Ligações Ativas|de palavras|Média|Ligações Ativas|Nó|
|Conexão Tratada|Yes|Conexões manuseidas|de palavras|Total|Conexões manuseidas|Nó|
|ConsensusEtcdraftActiveNodes|Yes|ConsensoEtcdraftActiveNodesDisplayName|de palavras|Média|Número de nós ativos neste canal.|Nó, canal|
|ConsensoSecdraftClusterSize|Yes|ConsensusEtcdraftClusterSizeDisplayName|de palavras|Média|Número de nós neste canal.|Nó, canal|
|ConsensoEtcdraftCommittedBlockNumber|Yes|ConsensusEtcdraftCommittedBlockNumberDisplayName|de palavras|Média|O número do quarteirão do último quarteirão cometido.|Nó, canal|
|ConsensoEtcdraftConfigProposalsRebid|Yes|ConsensoEtcdraftConfigProposalsReceivedDisplayName|de palavras|Média|O número total de propostas recebidas para transações tipo config.|Nó, canal|
|ConsensusEtcdraftIsLeader|Yes|ConsensusEtcdraftIsLeaderDisplayName|de palavras|Média|O estatuto de liderança do atual nó: 1 se for o líder mais 0.|Nó, canal|
|ConsensoSEtcdraftLeaderChanges|Yes|ConsensusEtcdraftLeaderChangesDisplayName|de palavras|Média|O número de líderes muda desde o início do processo.|Nó, canal|
|ConsensoSEtcdraftNormalProposalsRebid|Yes|ConsensoSEtcdraftNormalProposalsReceivedDisplayName|de palavras|Média|O número total de propostas recebidas para transações normais de tipo.|Nó, canal|
|ConsensoEtcdraftProposalFailures|Yes|ConsensoEtcdraftProposalFailuresDisplayName|de palavras|Média|O número de falhas de propostas.|Nó, canal|
|ConsensusEtcdraftSnapshotBlockNumber|Yes|ConsensusEtcdraftSnapshotBlockNumberDisplayName|de palavras|Média|O número do bloco da última foto.|Nó, canal|
|ConsensoKafkaBatchSize|Yes|ConsensusKafkaBatchSizeDisplayName|de palavras|Média|O tamanho médio do lote em bytes enviados para tópicos.|Nó, tópico|
|ConsensoKafkaCompressionRatio|Yes|ConsensusKafkaCompressionRatioDisplayName|de palavras|Média|A relação média de compressão (em percentagem) para tópicos.|Nó, tópico|
|ConsensoKafkaIncomingByteRate|Yes|ConsensusKafkaIncomingByteRateDisplayName|de palavras|Média|Bytes/segundo ler fora corretores.|Nó, broker_id|
|ConsensoKafkaLastOffsetPersisted|Yes|ConsensusKafkaLastOffsetPersistedDisplayName|de palavras|Média|A compensação especificada nos metadados de bloco do bloco mais recentemente comprometido.|Nó, canal|
|ConsensoKafkaOutgoingByteRate|Yes|ConsensusKafkaOutgoingByteRateDisplayName|de palavras|Média|Bytes/segundo escrito para corretores.|Nó, broker_id|
|ConsensoKafkaRecordSendRate|Yes|ConsensusKafkaRecordSendRateDisplayName|de palavras|Média|O número de registos por segundo enviados para tópicos.|Nó, tópico|
|ConsensoKafkaRecordsPerRequest|Yes|ConsensusKafkaRecordsPerRequestDisplayName|de palavras|Média|O número médio de registos enviados por pedido para tópicos.|Nó, tópico|
|ConsensusKafkaRequestLatency|Yes|ConsensusKafkaRequestLatencyDisplayName|de palavras|Média|O pedido médio de latência na Sra. para corretores.|Nó, broker_id|
|ConsensoKafkaRequestRate|Yes|ConsensusKafkaRequestRateDisplayName|de palavras|Média|Pedidos/segundo enviados para corretores.|Nó, broker_id|
|ConsensoKafkaRequestSize|Yes|ConsensusKafkaRequestSizeDisplayName|de palavras|Média|O tamanho médio do pedido em bytes para corretores.|Nó, broker_id|
|ConsensoKafkaResponseRate|Yes|ConsensusKafkaResponseRateDisplayName|de palavras|Média|Pedidos/segundo enviados para corretores.|Nó, broker_id|
|ConsensoKafkaResponseSize|Yes|ConsensusKafkaResponseSizeDisplayName|de palavras|Média|O tamanho médio da resposta em bytes de corretores.|Nó, broker_id|
|CpuUsagePercentageInDouble|Yes|Percentagem de Utilização cpu|Percentagem|Máximo|Percentagem de Utilização cpu|Nó|
|DeliverBlocksSent|Yes|Entregar Nome de DeliverBlocksSentDisplay|de palavras|Média|O número de blocos enviados pelo serviço de entrega.|Nó, canal, filtrado, data_type|
|DeliverRequestsCompleted|Yes|Entregar RequestsCompletedDisplayName|de palavras|Média|O número de pedidos de entrega que foram preenchidos.|Nó, canal, filtrado, data_type, sucesso|
|Entregar RequestsReceived|Yes|Entregar RequestsReceivedDisplayName|de palavras|Média|O número de pedidos de entrega que foram recebidos.|Nó, canal, filtrado, data_type|
|DeliverStreamsClosed|Yes|EntregaStreamsClosedDisplayName|de palavras|Média|O número de fluxos GRPC que foram fechados para o serviço de entrega.|Nó|
|DeliverStreamsOpened|Yes|DeliverStreamsOdisplayName|de palavras|Média|O número de fluxos GRPC que foram abertos para o serviço de entrega.|Nó|
|EndorserChaincodeInstantiationFailures|Yes|EndorserChaincodeInstantiationFailuresDisplayName|de palavras|Média|O número de instantâneos de código de corrente ou de atualização que falharam.|Nó, canal, código de corrente|
|EndorserDuplicateTransacçõesFailures|Yes|EndorserDuplicateTransactionFailuresDisplayName|de palavras|Média|O número de propostas falhadas devido à duplicação de identificação de transações.|Nó, canal, código de corrente|
|EndorserEndorsementFailures|Yes|EndorserEndorsementFailuresDisplayName|de palavras|Média|O número de apoios falhados.|Nó, canal, código de corrente, codificador de correntes|
|EndorserProposalAclFailures|Yes|EndorserProposalAclFailuresDisplayName|de palavras|Média|O número de propostas que falharam nos controlos da ACL.|Nó, canal, código de corrente|
|EndorserProposalSimulationsFailures|Yes|EndorserProposalSimulationFailuresDisplayName|de palavras|Média|O número de simulações de propostas falhadas.|Nó, canal, código de corrente|
|ApoianteProposalsRebid|Yes|EndorserProposalsReceivedDisplayName|de palavras|Média|O número de propostas recebidas.|Nó|
|EndorserProposalValidationFailures|Yes|EndorserProposalValidationFailuresDisplayName|de palavras|Média|O número de propostas que falharam na validação inicial.|Nó|
|EndorserSuccessfulProposals|Yes|EndorserSuccessfulProposalsDisplayName|de palavras|Média|O número de propostas bem sucedidas.|Nó|
|FabricVersion|Yes|FabricVersionDisplayName|de palavras|Média|A versão ativa de Fabric.|Nó, versão|
|GossipCommMEssagesReceived|Yes|GossipCommMessAgesReceivedDisplayName|de palavras|Média|Número de mensagens recebidas.|Nó|
|GossipCommMessagesSent|Yes|GossipCommMessagesSentDisplayName|de palavras|Média|Número de mensagens enviadas.|Nó|
|GossipCommOverflowCount|Yes|GossipCommOverflowCountDisplayName|de palavras|Média|Número de fila de saída transborda.|Nó|
|Líder eleitoral da GossipLeader|Yes|GossipLeaderElectionLeaderDisplayName|de palavras|Média|Peer é líder (1) ou seguidor (0).|Nó, canal|
|GossipMembershipTotalPeersKnown|Yes|GossipMembershipTotalPeersKnownDisplayName|de palavras|Média|Pares conhecidos.|Nó, canal|
|GossipPaypaybuffersize|Yes|GossipPaypaybuffersizedisplayName|de palavras|Média|Tamanho do tampão de carga.|Nó, canal|
|GossipStateHeight|Yes|GossipStateHeightDisplayName|de palavras|Média|Altura atual do livro de contabilidade.|Nó, canal|
|GrpcCommConnClosed|Yes|GrpcCommConnClosedDisplayName|de palavras|Média|ligações gRPC fechadas. Aberto menos fechado é o número ativo de ligações.|Nó|
|GrpcCommConnO aparas|Yes|GrpcCommConnO adpoudisplayName|de palavras|Média|ligações gRPC abertas. Aberto menos fechado é o número ativo de ligações.|Nó|
|GrpcServerStreamMessagesReceived|Yes|GrpcServerStreamMessagesReceivedDisplayName|de palavras|Média|O número de mensagens de streaming recebidas.|Nó, serviço, método|
|GrpcServerStreamMessagesSent|Yes|GrpcServerStreamMessagesSentDisplayName|de palavras|Média|O número de mensagens de streaming enviadas.|Nó, serviço, método|
|GrpcServerStreamRequestsCompleted|Yes|GrpcServerStreamRequestsCompletedDisplayName|de palavras|Média|O número de pedidos de streaming concluído.|Nó, serviço, método, código|
|GrpcServerUnaryRequestsRecebido|Yes|GrpcServerUnaryRequestsReceivedDisplayName|de palavras|Média|O número de pedidos não-oficiais recebidos.|Nó, serviço, método|
|IOReadBytes|Yes|IO Ler Bytes|Bytes|Total|IO Ler Bytes|Nó|
|IOWriteBytes|Yes|IO Escrever Bytes|Bytes|Total|IO Escrever Bytes|Nó|
|LedgerBlockchainHeight|Yes|Nome do Nome ledgerBlockchainHeightDisplay|de palavras|Média|Altura da corrente em blocos.|Nó, canal|
|Contagem de Transações de Livros|Yes|LedgerTransactionCountDisplayName|de palavras|Média|Número de transações processadas.|Nó, canal, transaction_type, código de corrente, validation_code|
|Entradas de registo Verificadas|Yes|Entradas de RegistoCheckdisplayName|de palavras|Média|Número de entradas de registo verificadas com o nível de registo ativo.|Nó, nível|
|Entradas de RegistoSCrito|Yes|RegistosDisplaydisplayName|de palavras|Média|Número de entradas de registo que estão escritas.|Nó, nível|
|MemóriaLimite|Yes|Limite de memória|Bytes|Média|Limite de memória|Nó|
|MemóriaSage|Yes|Utilização de Memória|Bytes|Média|Utilização de Memória|Nó|
|MemoryUsagePercentageInDouble|Yes|Percentagem de Utilização de Memória|Percentagem|Média|Percentagem de Utilização de Memória|Nó|
|Processos pendentes|Yes|Transações Pendentes|de palavras|Média|Transações Pendentes|Nó|
|Blocos Processados|Yes|Blocos processados|de palavras|Total|Blocos processados|Nó|
|Transações processadas|Yes|Transações Processadas|de palavras|Total|Transações Processadas|Nó|
|QueuedTransacções|Yes|Transações em fila|de palavras|Média|Transações em fila|Nó|
|Pedido Tratado|Yes|Pedidos Tratados|de palavras|Total|Pedidos Tratados|Nó|
|ArmazenamentoUsage|Yes|Utilização de armazenamento|Bytes|Média|Utilização de armazenamento|Nó|


## <a name="microsoftbotservicebotservices"></a>microsoft.botservice/botservices

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|PedidoLatency|Yes|Solicitar Latência|Milissegundos|Total|Tempo tomado pelo servidor para processar o pedido|Operação, Autenticação, Protocolo|
|PedidosTrafínico|Yes|Tráfego de Pedidos|Percentagem|de palavras|Número de Pedidos Feitos|Operação, Autenticação, Protocolo, Código de Estado, StatusCodeClass|


## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|allcachehits|Yes|Cache Hits (Baseado em Instância)|de palavras|Total||ShardId, Porto, Primário|
|allcachemisses|Yes|Cache Misses (Baseado em Instância)|de palavras|Total||ShardId, Porto, Primário|
|allcacheRead|Yes|Cache Read (Baseado em Instância)|BytesPerSecond|Máximo||ShardId, Porto, Primário|
|allcacheWrite|Yes|Cache Write (Baseado em Instância)|BytesPerSecond|Máximo||ShardId, Porto, Primário|
|todos osclientes ligados|Yes|Clientes conectados (baseado em casos)|de palavras|Máximo||ShardId, Porto, Primário|
|allevicedkeys|Yes|Chaves despejadas (Baseada em Exemplo)|de palavras|Total||ShardId, Porto, Primário|
|allexpiredkeys|Yes|Chaves expiradas (Baseado em Instância)|de palavras|Total||ShardId, Porto, Primário|
|allgetcommands|Yes|Obtém (Baseado em Instância)|de palavras|Total||ShardId, Porto, Primário|
|alloperationsPerSecond|Yes|Operações por segundo (caso baseado)|de palavras|Máximo||ShardId, Porto, Primário|
|allserverLoad|Yes|Carga do servidor (Baseada em Instância)|Percentagem|Máximo||ShardId, Porto, Primário|
|allsetcommands|Yes|Conjuntos (Baseado em Instância)|de palavras|Total||ShardId, Porto, Primário|
|alltotalcommands processado|Yes|Total de operações (baseado em instâncias)|de palavras|Total||ShardId, Porto, Primário|
|alltotalkeys|Yes|Chaves totais (baseada em instâncias)|de palavras|Máximo||ShardId, Porto, Primário|
|allusedmemory|Yes|Memória usada (baseado em exemplo)|Bytes|Máximo||ShardId, Porto, Primário|
|allusedmemorypercentage|Yes|Percentagem de memória utilizada (baseado em exemplo)|Percentagem|Máximo||ShardId, Porto, Primário|
|alusedmemoryRss|Yes|RSS de memória usado (baseado em exemplo)|Bytes|Máximo||ShardId, Porto, Primário|
|cachehits|Yes|Acertos na Cache|de palavras|Total||ShardId|
|cachehits0|Yes|Cache Hits (Fragmento 0)|de palavras|Total||Sem Dimensões|
|cachehits1|Yes|Cache Hits (Fragmento 1)|de palavras|Total||Sem Dimensões|
|cachehits2|Yes|Cache Hits (Fragmento 2)|de palavras|Total||Sem Dimensões|
|cachehits3|Yes|Cache Hits (Fragmento 3)|de palavras|Total||Sem Dimensões|
|cachehits4|Yes|Cache Hits (Fragmento 4)|de palavras|Total||Sem Dimensões|
|cachehits5|Yes|Cache Hits (Fragmento 5)|de palavras|Total||Sem Dimensões|
|cachehits6|Yes|Cache Hits (Fragmento 6)|de palavras|Total||Sem Dimensões|
|cachehits7|Yes|Cache Hits (Fragmento 7)|de palavras|Total||Sem Dimensões|
|cachehits8|Yes|Cache Hits (Fragmento 8)|de palavras|Total||Sem Dimensões|
|cachehits9|Yes|Cache Hits (Fragmento 9)|de palavras|Total||Sem Dimensões|
|cacheLatency|Yes|Microsegundos de latência de cache (pré-visualização)|de palavras|Média||ShardId|
|cachemisses|Yes|Falhas de Acerto na Cache|de palavras|Total||ShardId|
|cachemisses0|Yes|Cache Misses (Fragmento 0)|de palavras|Total||Sem Dimensões|
|cachemisses1|Yes|Cache Misses (Fragmento 1)|de palavras|Total||Sem Dimensões|
|cachemisses2|Yes|Cache Misses (Fragmento 2)|de palavras|Total||Sem Dimensões|
|cachemisses3|Yes|Cache Misses (Fragmento 3)|de palavras|Total||Sem Dimensões|
|cachemisses4|Yes|Cache Misses (Fragmento 4)|de palavras|Total||Sem Dimensões|
|cachemisses5|Yes|Cache Misses (Fragmento 5)|de palavras|Total||Sem Dimensões|
|cachemisses6|Yes|Cache Misses (Fragmento 6)|de palavras|Total||Sem Dimensões|
|cachemisses7|Yes|Cache Misses (Fragmento 7)|de palavras|Total||Sem Dimensões|
|cachemisses8|Yes|Cache Misses (Fragmento 8)|de palavras|Total||Sem Dimensões|
|cachemisses9|Yes|Cache Misses (Fragmento 9)|de palavras|Total||Sem Dimensões|
|cachemissrate|Yes|Taxa de Falha cache|Percentagem|cachemissrate||ShardId|
|cacheRead|Yes|Leitura da Cache|BytesPerSecond|Máximo||ShardId|
|cacheRead0|Yes|Cache Read (Fragmento 0)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheRead1|Yes|Cache Read (Fragmento 1)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheRead2|Yes|Cache Read (Fragmento 2)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheRead3|Yes|Cache Read (Fragmento 3)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheRead4|Yes|Cache Read (Fragmento 4)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheRead5|Yes|Cache Read (Fragmento 5)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheRead6|Yes|Cache Read (Fragmento 6)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheRead7|Yes|Cache Read (Fragmento 7)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheRead8|Yes|Cache Read (Fragmento 8)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheRead9|Yes|Cache Read (Fragmento 9)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheDite|Yes|Escrita na Cache|BytesPerSecond|Máximo||ShardId|
|cacheWrite0|Yes|Cache Write (Fragmento 0)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheWrite1|Yes|Cache Write (Fragmento 1)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheWrite2|Yes|Cache Write (Fragmento 2)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheWrite3|Yes|Cache Write (Fragmento 3)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheWrite4|Yes|Cache Write (Fragmento 4)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheWrite5|Yes|Cache Write (Fragmento 5)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheWrite6|Yes|Cache Write (Fragmento 6)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheWrite7|Yes|Cache Write (Fragmento 7)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheWrite8|Yes|Cache Write (Fragmento 8)|BytesPerSecond|Máximo||Sem Dimensões|
|cacheWrite9|Yes|Cache Write (Fragmento 9)|BytesPerSecond|Máximo||Sem Dimensões|
|connectedclients|Yes|Clientes Ligados|de palavras|Máximo||ShardId|
|connectedclients0|Yes|Clientes Conectados (Fragmento 0)|de palavras|Máximo||Sem Dimensões|
|connectedclients1|Yes|Clientes Conectados (Fragmento 1)|de palavras|Máximo||Sem Dimensões|
|connectedclients2|Yes|Clientes Conectados (Fragmento 2)|de palavras|Máximo||Sem Dimensões|
|connectedclients3|Yes|Clientes Conectados (Fragmento 3)|de palavras|Máximo||Sem Dimensões|
|connectedclients4|Yes|Clientes Conectados (Fragmento 4)|de palavras|Máximo||Sem Dimensões|
|connectedclients5|Yes|Clientes Conectados (Fragmento 5)|de palavras|Máximo||Sem Dimensões|
|connectedclients6|Yes|Clientes Conectados (Fragmento 6)|de palavras|Máximo||Sem Dimensões|
|connectedclients7|Yes|Clientes Conectados (Fragmento 7)|de palavras|Máximo||Sem Dimensões|
|connectedclients8|Yes|Clientes Conectados (Fragmento 8)|de palavras|Máximo||Sem Dimensões|
|connectedclients9|Yes|Clientes Conectados (Fragmento 9)|de palavras|Máximo||Sem Dimensões|
|erros|Yes|Erros|de palavras|Máximo||ShardId, ErrorType|
|chaves despejadas|Yes|Chaves Excluídas|de palavras|Total||ShardId|
|despejadokeys0|Yes|Chaves despejadas (Fragmento 0)|de palavras|Total||Sem Dimensões|
|chaves despejadas1|Yes|Chaves despejadas (Fragmento 1)|de palavras|Total||Sem Dimensões|
|despejadokeys2|Yes|Chaves despejadas (Fragmento 2)|de palavras|Total||Sem Dimensões|
|despejadokeys3|Yes|Chaves despejadas (Fragmento 3)|de palavras|Total||Sem Dimensões|
|despejadokeys4|Yes|Chaves despejadas (Fragmento 4)|de palavras|Total||Sem Dimensões|
|despejadokeys5|Yes|Chaves despejadas (Fragmento 5)|de palavras|Total||Sem Dimensões|
|despejadokeys6|Yes|Chaves despejadas (Fragmento 6)|de palavras|Total||Sem Dimensões|
|despejadokeys7|Yes|Chaves despejadas (Fragmento 7)|de palavras|Total||Sem Dimensões|
|despejadokeys8|Yes|Chaves despejadas (Fragmento 8)|de palavras|Total||Sem Dimensões|
|despejadokeys9|Yes|Chaves despejadas (Fragmento 9)|de palavras|Total||Sem Dimensões|
|chaves caducadas|Yes|Chaves Expiradas|de palavras|Total||ShardId|
|40keys0|Yes|Chaves expiradas (Fragmento 0)|de palavras|Total||Sem Dimensões|
|400keys validade1|Yes|Chaves expiradas (Fragmento 1)|de palavras|Total||Sem Dimensões|
|400keys expirados2|Yes|Chaves expiradas (Fragmento 2)|de palavras|Total||Sem Dimensões|
|400keys3|Yes|Chaves expiradas (Fragmento 3)|de palavras|Total||Sem Dimensões|
|4keys expirados|Yes|Chaves expiradas (Fragmento 4)|de palavras|Total||Sem Dimensões|
|400keys5|Yes|Chaves expiradas (Fragmento 5)|de palavras|Total||Sem Dimensões|
|400keys6|Yes|Chaves expiradas (Fragmento 6)|de palavras|Total||Sem Dimensões|
|400keys validade|Yes|Chaves expiradas (Fragmento 7)|de palavras|Total||Sem Dimensões|
|400keys8|Yes|Chaves expiradas (Fragmento 8)|de palavras|Total||Sem Dimensões|
|expirarkeys9|Yes|Chaves expiradas (Fragmento 9)|de palavras|Total||Sem Dimensões|
|obter commões|Yes|Obtenções|de palavras|Total||ShardId|
|obter commands0|Yes|Recebe (Fragmento 0)|de palavras|Total||Sem Dimensões|
|obter commands1|Yes|Recebe (Fragmento 1)|de palavras|Total||Sem Dimensões|
|obter commands2|Yes|Recebe (Fragmento 2)|de palavras|Total||Sem Dimensões|
|obter commands3|Yes|Recebe (Fragmento 3)|de palavras|Total||Sem Dimensões|
|obter commands4|Yes|Recebe (Fragmento 4)|de palavras|Total||Sem Dimensões|
|obter commands5|Yes|Recebe (Fragmento 5)|de palavras|Total||Sem Dimensões|
|obter commands6|Yes|Recebe (Fragmento 6)|de palavras|Total||Sem Dimensões|
|obter commands7|Yes|Recebe (Fragmento 7)|de palavras|Total||Sem Dimensões|
|obter commands8|Yes|Recebe (Fragmento 8)|de palavras|Total||Sem Dimensões|
|obter commands9|Yes|Recebe (Fragmento 9)|de palavras|Total||Sem Dimensões|
|operaçõesPerSecond|Yes|Operações por Segundo|de palavras|Máximo||ShardId|
|operaçõesPerSecond0|Yes|Operações por segundo (Fragmento 0)|de palavras|Máximo||Sem Dimensões|
|operaçõesPerSecond1|Yes|Operações por segundo (Fragmento 1)|de palavras|Máximo||Sem Dimensões|
|operaçõesPerSecond2|Yes|Operações por segundo (Fragmento 2)|de palavras|Máximo||Sem Dimensões|
|operaçõesPerSecond3|Yes|Operações por segundo (Fragmento 3)|de palavras|Máximo||Sem Dimensões|
|operaçõesPerSecond4|Yes|Operações por segundo (Fragmento 4)|de palavras|Máximo||Sem Dimensões|
|operaçõesPerSecond5|Yes|Operações por segundo (Fragmento 5)|de palavras|Máximo||Sem Dimensões|
|operaçõesPerSecond6|Yes|Operações por segundo (Fragmento 6)|de palavras|Máximo||Sem Dimensões|
|operaçõesPerSecond7|Yes|Operações por segundo (Fragmento 7)|de palavras|Máximo||Sem Dimensões|
|operaçõesPerSecond8|Yes|Operações por segundo (Fragmento 8)|de palavras|Máximo||Sem Dimensões|
|operaçõesPerSecond9|Yes|Operações por segundo (Fragmento 9)|de palavras|Máximo||Sem Dimensões|
|percentProcessorTime|Yes|CPU|Percentagem|Máximo||ShardId|
|percentProcessorTime0|Yes|CPU (Fragmento 0)|Percentagem|Máximo||Sem Dimensões|
|percentProcessorTime1|Yes|CPU (Fragmento 1)|Percentagem|Máximo||Sem Dimensões|
|percentProcessorTime2|Yes|CPU (Fragmento 2)|Percentagem|Máximo||Sem Dimensões|
|percentProcessorTime3|Yes|CPU (Fragmento 3)|Percentagem|Máximo||Sem Dimensões|
|percentProcessorTime4|Yes|CPU (Fragmento 4)|Percentagem|Máximo||Sem Dimensões|
|percentProcessorTime5|Yes|CPU (Fragmento 5)|Percentagem|Máximo||Sem Dimensões|
|percentProcessorTime6|Yes|CPU (Fragmento 6)|Percentagem|Máximo||Sem Dimensões|
|percentProcessorTime7|Yes|CPU (Fragmento 7)|Percentagem|Máximo||Sem Dimensões|
|percentProcessorTime8|Yes|CPU (Fragmento 8)|Percentagem|Máximo||Sem Dimensões|
|percentProcessorTime9|Yes|CPU (Fragmento 9)|Percentagem|Máximo||Sem Dimensões|
|servidorAcar|Yes|Carga do Servidor|Percentagem|Máximo||ShardId|
|servidorLoad0|Yes|Carga do servidor (Fragmento 0)|Percentagem|Máximo||Sem Dimensões|
|servidorLoad1|Yes|Carga do servidor (Fragmento 1)|Percentagem|Máximo||Sem Dimensões|
|servidorLoad2|Yes|Carga do servidor (Fragmento 2)|Percentagem|Máximo||Sem Dimensões|
|servidorLoad3|Yes|Carga do servidor (Fragmento 3)|Percentagem|Máximo||Sem Dimensões|
|servidorLoad4|Yes|Carga do servidor (Fragmento 4)|Percentagem|Máximo||Sem Dimensões|
|servidorLoad5|Yes|Carga do servidor (Fragmento 5)|Percentagem|Máximo||Sem Dimensões|
|servidorLoad6|Yes|Carga do servidor (Fragmento 6)|Percentagem|Máximo||Sem Dimensões|
|servidorLoad7|Yes|Carga do servidor (Fragmento 7)|Percentagem|Máximo||Sem Dimensões|
|servidorLoad8|Yes|Carga do servidor (Fragmento 8)|Percentagem|Máximo||Sem Dimensões|
|servidorLoad9|Yes|Carga do servidor (Fragmento 9)|Percentagem|Máximo||Sem Dimensões|
|setcommands|Yes|Conjuntos|de palavras|Total||ShardId|
|setcommands0|Yes|Conjuntos (Fragmento 0)|de palavras|Total||Sem Dimensões|
|setcommands1|Yes|Conjuntos (Fragmento 1)|de palavras|Total||Sem Dimensões|
|setcommands2|Yes|Conjuntos (Fragmento 2)|de palavras|Total||Sem Dimensões|
|setcommands3|Yes|Conjuntos (Fragmento 3)|de palavras|Total||Sem Dimensões|
|setcommands4|Yes|Conjuntos (Fragmento 4)|de palavras|Total||Sem Dimensões|
|setcommands5|Yes|Conjuntos (Fragmento 5)|de palavras|Total||Sem Dimensões|
|setcommands6|Yes|Conjuntos (Fragmento 6)|de palavras|Total||Sem Dimensões|
|setcommands7|Yes|Conjuntos (Fragmento 7)|de palavras|Total||Sem Dimensões|
|setcommands8|Yes|Conjuntos (Fragmento 8)|de palavras|Total||Sem Dimensões|
|setcommands9|Yes|Conjuntos (Fragmento 9)|de palavras|Total||Sem Dimensões|
|totalcommands processado|Yes|Total de Operações|de palavras|Total||ShardId|
|totalcommands processado0|Yes|Total de operações (Fragmento 0)|de palavras|Total||Sem Dimensões|
|totalcommandsprocessado1|Yes|Total de operações (Fragmento 1)|de palavras|Total||Sem Dimensões|
|totalcommands processado2|Yes|Total de Operações (Fragmento 2)|de palavras|Total||Sem Dimensões|
|totalcommands processado3|Yes|Total de operações (Fragmento 3)|de palavras|Total||Sem Dimensões|
|totalcommandsprocessado4|Yes|Total de operações (Fragmento 4)|de palavras|Total||Sem Dimensões|
|totalcommands processado5|Yes|Total de operações (Fragmento 5)|de palavras|Total||Sem Dimensões|
|totalcommands processado6|Yes|Total de operações (Fragmento 6)|de palavras|Total||Sem Dimensões|
|totalcommands processado7|Yes|Total de operações (Fragmento 7)|de palavras|Total||Sem Dimensões|
|totalcommandsprocessado8|Yes|Total de operações (Fragmento 8)|de palavras|Total||Sem Dimensões|
|totalcommandsprocessado9|Yes|Total de operações (Fragmento 9)|de palavras|Total||Sem Dimensões|
|totalkeys|Yes|Chaves totais|de palavras|Máximo||ShardId|
|totalkeys0|Yes|Chaves totais (Fragmento 0)|de palavras|Máximo||Sem Dimensões|
|totalkeys1|Yes|Chaves totais (Fragmento 1)|de palavras|Máximo||Sem Dimensões|
|totalkeys2|Yes|Chaves totais (Fragmento 2)|de palavras|Máximo||Sem Dimensões|
|totalkeys3|Yes|Chaves totais (Fragmento 3)|de palavras|Máximo||Sem Dimensões|
|totalkeys4|Yes|Chaves totais (Fragmento 4)|de palavras|Máximo||Sem Dimensões|
|totalkeys5|Yes|Chaves totais (Fragmento 5)|de palavras|Máximo||Sem Dimensões|
|totalkeys6|Yes|Chaves totais (Fragmento 6)|de palavras|Máximo||Sem Dimensões|
|totalkeys7|Yes|Chaves totais (Fragmento 7)|de palavras|Máximo||Sem Dimensões|
|totalkeys8|Yes|Chaves totais (Fragmento 8)|de palavras|Máximo||Sem Dimensões|
|totalkeys9|Yes|Chaves totais (Fragmento 9)|de palavras|Máximo||Sem Dimensões|
|usedmemory|Yes|Memória Utilizada|Bytes|Máximo||ShardId|
|usadomemory0|Yes|Memória Usada (Fragmento 0)|Bytes|Máximo||Sem Dimensões|
|usedmemory1|Yes|Memória Usada (Fragmento 1)|Bytes|Máximo||Sem Dimensões|
|usedmemory2|Yes|Memória Usada (Fragmento 2)|Bytes|Máximo||Sem Dimensões|
|usadomemory3|Yes|Memória Usada (Fragmento 3)|Bytes|Máximo||Sem Dimensões|
|usadomemory4|Yes|Memória Usada (Fragmento 4)|Bytes|Máximo||Sem Dimensões|
|usadomemory5|Yes|Memória Usada (Fragmento 5)|Bytes|Máximo||Sem Dimensões|
|usadomemory6|Yes|Memória Usada (Fragmento 6)|Bytes|Máximo||Sem Dimensões|
|usadomemory7|Yes|Memória Usada (Fragmento 7)|Bytes|Máximo||Sem Dimensões|
|usadomemory8|Yes|Memória Usada (Fragmento 8)|Bytes|Máximo||Sem Dimensões|
|usedmemory9|Yes|Memória Usada (Fragmento 9)|Bytes|Máximo||Sem Dimensões|
|usuário|Yes|Percentagem de Memória Utilizada|Percentagem|Máximo||ShardId|
|usedmemoryRss|Yes|RSS de memória usada|Bytes|Máximo||ShardId|
|usedmemoryRss0|Yes|RSS de memória usado (fragmento 0)|Bytes|Máximo||Sem Dimensões|
|usedmemoryRss1|Yes|RSS de memória usado (fragmento 1)|Bytes|Máximo||Sem Dimensões|
|usedmemoryRss2|Yes|RSS de memória usado (fragmento 2)|Bytes|Máximo||Sem Dimensões|
|usedmemoryRss3|Yes|RSS de memória usado (fragmento 3)|Bytes|Máximo||Sem Dimensões|
|usedmemoryRss4|Yes|RSS de memória usado (fragmento 4)|Bytes|Máximo||Sem Dimensões|
|usedmemoryRss5|Yes|RSS de memória usado (fragmento 5)|Bytes|Máximo||Sem Dimensões|
|usedmemoryRss6|Yes|RSS de memória usado (fragmento 6)|Bytes|Máximo||Sem Dimensões|
|usedmemoryRss7|Yes|RSS de memória usado (fragmento 7)|Bytes|Máximo||Sem Dimensões|
|usedmemoryRss8|Yes|RSS de memória usado (fragmento 8)|Bytes|Máximo||Sem Dimensões|
|usedmemoryRss9|Yes|RSS de memória usado (fragmento 9)|Bytes|Máximo||Sem Dimensões|


## <a name="microsoftcacheredisenterprise"></a>Microsoft.Cache/redisEnterprise

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|cachehits|Yes|Acertos na Cache|de palavras|Total||Sem Dimensões|
|cacheLatency|Yes|Microsegundos de latência de cache (pré-visualização)|de palavras|Média||InstanceId|
|cachemisses|Yes|Falhas de Acerto na Cache|de palavras|Total||InstanceId|
|cacheRead|Yes|Leitura da Cache|BytesPerSecond|Máximo||InstanceId|
|cacheDite|Yes|Escrita na Cache|BytesPerSecond|Máximo||InstanceId|
|connectedclients|Yes|Clientes Ligados|de palavras|Máximo||InstanceId|
|erros|Yes|Erros|de palavras|Máximo||InstanceId, ErrorType|
|chaves despejadas|Yes|Chaves Excluídas|de palavras|Total||Sem Dimensões|
|chaves caducadas|Yes|Chaves Expiradas|de palavras|Total||Sem Dimensões|
|obter commões|Yes|Obtenções|de palavras|Total||Sem Dimensões|
|operaçõesPerSecond|Yes|Operações por Segundo|de palavras|Máximo||Sem Dimensões|
|percentProcessorTime|Yes|CPU|Percentagem|Máximo||InstanceId|
|servidorAcar|Yes|Carga do Servidor|Percentagem|Máximo||Sem Dimensões|
|setcommands|Yes|Conjuntos|de palavras|Total||Sem Dimensões|
|totalcommands processado|Yes|Total de Operações|de palavras|Total||Sem Dimensões|
|totalkeys|Yes|Chaves totais|de palavras|Máximo||Sem Dimensões|
|usedmemory|Yes|Memória Utilizada|Bytes|Máximo||Sem Dimensões|
|usuário|Yes|Percentagem de Memória Utilizada|Percentagem|Máximo||InstanceId|
|usedmemoryRss|Yes|RSS de memória usada|Bytes|Máximo||InstanceId|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Yes|Contagem de pedidos de firewall de aplicação web|de palavras|Total|O número de pedidos de cliente processados pela Firewall de Aplicação Web|Nome de Política, Nome de Regras, Ação|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/perfis

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ByteHitRatio|Yes|Rácio byte hit|Percentagem|Média|Esta é a razão do total de bytes servidos a partir da cache em comparação com os bytes de resposta total|Ponto final|
|OriginHealthPercentage|Yes|Percentagem de Saúde de Origem|Percentagem|Média|A percentagem de sondas de saúde bem sucedidas da AFDX para backends.|Origin, OriginPool|
|OriginLatency|Yes|Latência de origem|MilliSeconds|Média|O tempo calculado a partir do momento em que o pedido foi enviado pela borda AFDX para o backend até que a AFDX recebeu a última resposta byte do backend.|Origem, Ponto final|
|OriginRequestCount|Yes|Contagem de pedidos de origem|de palavras|Total|O número de pedidos enviados da AFDX para a origem.|HttpStatus, HttpStatusGroup, Origin, Endpoint|
|Percentagem4XX|Yes|Percentagem de 4XX|Percentagem|Média|A percentagem de todos os pedidos de cliente para os quais o código de estado de resposta é 4XX|Endpoint, ClientRegion, ClientCountry|
|Percentagem5XX|Yes|Percentagem de 5XX|Percentagem|Média|A percentagem de todos os pedidos de cliente para os quais o código de estado de resposta é 5XX|Endpoint, ClientRegion, ClientCountry|
|PedidoCount|Yes|Número de Pedidos|de palavras|Total|O número de pedidos de clientes servidos pelo representante http/s|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|Solicitação|Yes|Tamanho do pedido|Bytes|Total|O número de bytes enviados como pedidos de clientes para a AFDX.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|Tamanho das respostas|Yes|Tamanho da resposta|Bytes|Total|O número de bytes enviados como respostas de http/S proxy aos clientes|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|TotalLatency|Yes|Latência total|MilliSeconds|Média|O tempo calculado a partir do momento em que o pedido do cliente foi recebido pelo representante HTTP/S até que o cliente reconhecesse o último byte de resposta do representante HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|WebApplicationFirewallRequestCount|Yes|Contagem de pedidos de firewall de aplicação web|de palavras|Total|O número de pedidos de cliente processados pela Firewall de Aplicação Web|Nome de Política, Nome de Regras, Ação|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Discos de leitura bytes/sec|No|Leitura de Disco|BytesPerSecond|Média|Bytes médios lidos a partir do disco durante o período de monitorização.|RoleInstanceId|
|Operações de leitura de disco/sec|Yes|Operações de leitura de disco/sec|CondePerSecond|Média|O disco lê iops.|RoleInstanceId|
|Discos De Escrita Bytes/Sec|No|Escrita em Disco|BytesPerSecond|Média|Bytes médios escritos para o disco durante o período de monitorização.|RoleInstanceId|
|Operações de escrita de discos/seg|Yes|Operações de escrita de discos/seg|CondePerSecond|Média|Disco escreve IOPS.|RoleInstanceId|
|Entrada na Rede|Yes|Entrada na Rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela(s) Máquina Virtual(s) (Tráfego de Entrada).|RoleInstanceId|
|Saída da Rede|Yes|Saída da Rede|Bytes|Total|O número de bytes em todas as interfaces de rede pela(s) Máquina Virtual(s) (Tráfego de Saída).|RoleInstanceId|
|Percentagem da CPU|Yes|Percentagem da CPU|Percentagem|Média|A percentagem de unidades de computação atribuídas que estão atualmente a ser utilizadas pela Máquina Virtual( s).|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Discos de leitura bytes/sec|No|Leitura de Disco|BytesPerSecond|Média|Bytes médios lidos a partir do disco durante o período de monitorização.|Sem Dimensões|
|Operações de leitura de disco/sec|Yes|Operações de leitura de disco/sec|CondePerSecond|Média|O disco lê iops.|Sem Dimensões|
|Discos De Escrita Bytes/Sec|No|Escrita em Disco|BytesPerSecond|Média|Bytes médios escritos para o disco durante o período de monitorização.|Sem Dimensões|
|Operações de escrita de discos/seg|Yes|Operações de escrita de discos/seg|CondePerSecond|Média|Disco escreve IOPS.|Sem Dimensões|
|Entrada na Rede|Yes|Entrada na Rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela(s) Máquina Virtual(s) (Tráfego de Entrada).|Sem Dimensões|
|Saída da Rede|Yes|Saída da Rede|Bytes|Total|O número de bytes em todas as interfaces de rede pela(s) Máquina Virtual(s) (Tráfego de Saída).|Sem Dimensões|
|Percentagem da CPU|Yes|Percentagem da CPU|Percentagem|Média|A percentagem de unidades de computação atribuídas que estão atualmente a ser utilizadas pela Máquina Virtual( s).|Sem Dimensões|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAcontas

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Autenticação|
|Saída|Yes|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Autenticação|
|Entrada|Yes|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Autenticação|
|SucessoE2ELatency|Yes|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponta a ponta de pedidos bem sucedidos feitas a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Autenticação|
|SucessoServerLatency|Yes|Latência do servidor de sucesso|Milissegundos|Média|A latência usada pela Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Autenticação|
|Transações|Yes|Transações|de palavras|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType, GeoType, ApiName, Autenticação|
|Capacidade Usada|No|Capacidade utilizada|Bytes|Média|Capacidade da conta utilizada|Sem Dimensões|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Autenticação|
|Capacidade blob|No|Capacidade blob|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Blob da conta de armazenamento em bytes.|BlobType, Tier|
|BlobCount|No|Contagem de blobs|de palavras|Média|O número de Blob no serviço Blob da conta de armazenamento.|BlobType, Tier|
|Contagem de contentores|Yes|Contagem de contentores blob|de palavras|Média|O número de contentores no serviço Blob da conta de armazenamento.|Sem Dimensões|
|Saída|Yes|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Autenticação|
|Capacidade de Indexação|No|Capacidade de Índice|Bytes|Média|A quantidade de armazenamento utilizada pelo Índice ADLS Gen2 (Hierárquica) em bytes.|Sem Dimensões|
|Entrada|Yes|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Autenticação|
|SucessoE2ELatency|Yes|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponta a ponta de pedidos bem sucedidos feitas a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Autenticação|
|SucessoServerLatency|Yes|Latência do servidor de sucesso|Milissegundos|Média|A latência usada pela Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Autenticação|
|Transações|Yes|Transações|de palavras|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType, GeoType, ApiName, Autenticação|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAcons/fileServices

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Autenticação, FileShare|
|Saída|Yes|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Autenticação, FileShare|
|Capacidade de Ficheiros|No|Capacidade de Arquivo|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço de ficheiros da conta de armazenamento em bytes.|FileShare|
|Contagem de ficheiros|No|Contagem de ficheiros|de palavras|Média|O número de ficheiros no serviço de ficheiros da conta de armazenamento.|FileShare|
|FileShareCount|No|Contagem de partilha de ficheiros|de palavras|Média|O número de ações de ficheiros no serviço de ficheiros da conta de armazenamento.|Sem Dimensões|
|FileShareQuota|No|Tamanho da quota de ações de arquivo|Bytes|Média|O limite superior da quantidade de armazenamento que pode ser usado pelo Azure Files Service em bytes.|FileShare|
|FileShareSnapshotCount|No|Contagem de snapshot de partilha de ficheiros|de palavras|Média|O número de instantâneos presentes na parte do Serviço de Ficheiros da conta de armazenamento.|FileShare|
|FileShareSnapshotSize|No|Tamanho do snapshot de partilha de arquivo|Bytes|Média|A quantidade de armazenamento utilizada pelas fotos no serviço de ficheiros da conta de armazenamento em bytes.|FileShare|
|Entrada|Yes|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Autenticação, FileShare|
|SucessoE2ELatency|Yes|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponta a ponta de pedidos bem sucedidos feitas a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Autenticação, FileShare|
|SucessoServerLatency|Yes|Latência do servidor de sucesso|Milissegundos|Média|A latência usada pela Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Autenticação, FileShare|
|Transações|Yes|Transações|de palavras|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType, GeoType, ApiName, Autenticação, FileShare|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Autenticação|
|Saída|Yes|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Autenticação|
|Entrada|Yes|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Autenticação|
|Capacidade de Fila|Yes|Capacidade de fila|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço de fila da conta de armazenamento em bytes.|Sem Dimensões|
|Contagem de filas|Yes|Contagem de filas|de palavras|Média|O número de filas no serviço de fila da conta de armazenamento.|Sem Dimensões|
|QueueMessageCount|Yes|Contagem de mensagens de fila|de palavras|Média|O número aproximado de mensagens de fila no serviço de fila da conta de armazenamento.|Sem Dimensões|
|SucessoE2ELatency|Yes|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponta a ponta de pedidos bem sucedidos feitas a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Autenticação|
|SucessoServerLatency|Yes|Latência do servidor de sucesso|Milissegundos|Média|A latência usada pela Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Autenticação|
|Transações|Yes|Transações|de palavras|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType, GeoType, ApiName, Autenticação|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Autenticação|
|Saída|Yes|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Autenticação|
|Entrada|Yes|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Autenticação|
|SucessoE2ELatency|Yes|Latência de E2E Com Êxito|Milissegundos|Média|A latência de ponta a ponta de pedidos bem sucedidos feitas a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Autenticação|
|SucessoServerLatency|Yes|Latência do servidor de sucesso|Milissegundos|Média|A latência usada pela Azure Storage para processar um pedido bem sucedido, em milissegundos. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Autenticação|
|Capacidade de Mesa|Yes|Capacidade de mesa|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço de mesa da conta de armazenamento em bytes.|Sem Dimensões|
|MesaCount|Yes|Contagem de tabelas|de palavras|Média|O número de mesa no serviço de mesa da conta de armazenamento.|Sem Dimensões|
|TabelaseconagemCount|Yes|Contagem de Entidades de Tabela|de palavras|Média|O número de entidades de mesa no serviço mesa da conta de armazenamento.|Sem Dimensões|
|Transações|Yes|Transações|de palavras|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType, GeoType, ApiName, Autenticação|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/contas

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|BlockedCalls|Yes|Chamadas bloqueadas|de palavras|Total|Número de chamadas que excederam a taxa ou o limite de quota.|ApiName, OperaçãoName, Região|
|Personagens Treinados|Yes|Personagens treinados|de palavras|Total|Número total de caracteres treinados.|ApiName, OperaçãoName, Região|
|CaracteresTranslatados|Yes|Caracteres Traduzidos|de palavras|Total|Número total de caracteres no pedido de texto de entrada.|ApiName, OperaçãoName, Região|
|ClientErrors|Yes|Erros do Cliente|de palavras|Total|Número de chamadas com erro lateral do cliente (código de resposta HTTP 4xx).|ApiName, OperaçãoName, Região|
|DataIn|Yes|Dados em|Bytes|Total|Tamanho dos dados de entrada em bytes.|ApiName, OperaçãoName, Região|
|DataOut|Yes|Data out|Bytes|Total|Tamanho dos dados de saída em bytes.|ApiName, OperaçãoName, Região|
|Latência|Yes|Latência|MilliSeconds|Média|Latência em milissegundos.|ApiName, OperaçãoName, Região|
|Eventos LearnedEvents|Yes|Eventos Aprendidos|de palavras|Total|Número de eventos aprendidos.|IsMatchBaseline, Modo, RunId|
|MatchedRewards|Yes|Recompensas combinadas|de palavras|Total| Número de recompensas combinadas.|IsMatchBaseline, Modo, RunId|
|Ressoemiado|Yes|Recompensas Observadas|de palavras|Total|Número de recompensas observadas.|IsMatchBaseline, Modo, RunId|
|ProcessadosCharacters|Yes|Caracteres processados|de palavras|Total|Número de caracteres.|ApiName, FeatureName, UsageChannel, Região|
|ProcessostextRecords|Yes|Registos de Texto Processados|de palavras|Total|Contagem de Registos de Texto.|ApiName, FeatureName, UsageChannel, Região|
|ServerErrors|Yes|Erros do servidor|de palavras|Total|Número de chamadas com erro interno de serviço (código de resposta HTTP 5xx).|ApiName, OperaçãoName, Região|
|DiscursoSSessionDuration|Yes|Duração da sessão de discurso|Segundos|Total|Duração total da sessão de discurso em segundos.|ApiName, OperaçãoName, Região|
|Chamadas de sucesso|Yes|Chamadas bem sucedidas|de palavras|Total|Número de chamadas bem sucedidas.|ApiName, OperaçãoName, Região|
|Total de Chamadas|Yes|Total de chamadas|de palavras|Total|Número total de chamadas.|ApiName, OperaçãoName, Região|
|TotalErrors|Yes|Total de Erros|de palavras|Total|Número total de chamadas com resposta a erros (código de resposta HTTP 4xx ou 5xx).|ApiName, OperaçãoName, Região|
|TotalTokenCalls|Yes|Total de chamadas simbólicas|de palavras|Total|Número total de chamadas simbólicas.|ApiName, OperaçãoName, Região|
|Total de transações|Yes|Total de Transações|de palavras|Total|Número total de transações.|Sem Dimensões|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft.Communication/CommunicationServices

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|APIRequestAuthentication|No|Pedidos de autenticação da API|de palavras|de palavras|Contagem de todos os pedidos contra o ponto final de autenticação dos serviços de comunicação.|Operação, StatusCode, StatusCodeClass|
|APIRequestChat|Yes|Pedidos de Chat API|de palavras|de palavras|Contagem de todos os pedidos contra o ponto final do Chat dos Serviços de Comunicação.|Operação, StatusCode, StatusCodeClass|
|APIRequestsms|Yes|Pedidos de API por SMS|de palavras|de palavras|Contagem de todos os pedidos contra o ponto final dos Serviços de Comunicação SMS.|Operação, StatusCode, StatusCodeClass|


## <a name="microsoftcomputecloudservices"></a>Microsoft.Compute/cloudServices

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Bytes de leitura de disco|Yes|Bytes de leitura de disco|Bytes|Total|Bytes lidos a partir de disco durante o período de monitorização|RoleInstanceId, RoleId|
|Operações de leitura de disco/sec|Yes|Operações de leitura de disco/sec|CondePerSecond|Média|IOPS de leitura de disco|RoleInstanceId, RoleId|
|Bytes de escrita de disco|Yes|Bytes de escrita de disco|Bytes|Total|Bytes escritos para o disco durante o período de monitorização|RoleInstanceId, RoleId|
|Operações de escrita de discos/seg|Yes|Operações de escrita de discos/seg|CondePerSecond|Média|IOPS de escrita de disco|RoleInstanceId, RoleId|
|Percentagem da CPU|Yes|Percentagem da CPU|Percentagem|Média|A percentagem de unidades de computação atribuídas que estão atualmente a ser utilizadas pela Máquina Virtual(s)|RoleInstanceId, RoleId|


## <a name="microsoftcomputecloudservicesroles"></a>Microsoft.Compute/cloudServices/roles

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Bytes de leitura de disco|Yes|Bytes de leitura de disco|Bytes|Total|Bytes lidos a partir de disco durante o período de monitorização|RoleInstanceId, RoleId|
|Operações de leitura de disco/sec|Yes|Operações de leitura de disco/sec|CondePerSecond|Média|IOPS de leitura de disco|RoleInstanceId, RoleId|
|Bytes de escrita de disco|Yes|Bytes de escrita de disco|Bytes|Total|Bytes escritos para o disco durante o período de monitorização|RoleInstanceId, RoleId|
|Operações de escrita de discos/seg|Yes|Operações de escrita de discos/seg|CondePerSecond|Média|IOPS de escrita de disco|RoleInstanceId, RoleId|
|Percentagem da CPU|Yes|Percentagem da CPU|Percentagem|Média|A percentagem de unidades de computação atribuídas que estão atualmente a ser utilizadas pela Máquina Virtual(s)|RoleInstanceId, RoleId|


## <a name="microsoftcomputedisks"></a>microsoft.compute/disks

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Discos compostos ler bytes/seg|No|Disk Read Bytes/seg (Pré-visualização)|Bytes|Média|Bytes/seg lido a partir do disco durante o período de monitorização, por favor note que esta métrica está em pré-visualização e está sujeita a alterações antes de se tornar geralmente disponível||
|Operações de leitura de disco compósito/seg|No|Operações de leitura de disco/seg (Pré-visualização)|Bytes|Média|Número de IOs de leitura realizado em disco durante o período de monitorização, por favor note que esta métrica está em pré-visualização e está sujeita a alterações antes de se tornar geralmente disponível||
|Bytes de escrita de disco composto/seg|No|Discos de escrita bytes/seg (Pré-visualização)|Bytes|Média|Bytes/seg escritos para o disco durante o período de monitorização, por favor note que esta métrica está em pré-visualização e está sujeita a alterações antes de se tornar geralmente disponível||
|Operações de escrita de discos compósitos/seg|No|Operações de escrita de discos/seg(Pré-visualização)|Bytes|Média|Número de IOs de escrita realizados num disco durante o período de monitorização, por favor note que esta métrica está em pré-visualização e está sujeita a alterações antes de se tornar geralmente disponível||


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Créditos CPU Consumidos|Yes|Créditos CPU Consumidos|de palavras|Média|Número total de créditos consumidos pela Máquina Virtual|Sem Dimensões|
|Créditos CPU Remanescentes|Yes|Créditos CPU Remanescentes|de palavras|Média|Número total de créditos disponíveis para rebentar|Sem Dimensões|
|Percentagem consumida da largura de banda do disco de dados|Yes|Percentagem consumida da largura de banda do disco de dados|Percentagem|Média|Percentagem de largura de banda de disco de dados consumida por minuto|RIO LUN|
|Percentagem consumida em IOPS do disco de dados|Yes|Percentagem consumida em IOPS do disco de dados|Percentagem|Média|Percentagem de disco de dados I/Os consumido por minuto|RIO LUN|
|Largura de banda Max Burst Do Disco de Dados|Yes|Largura de banda Max Burst Do Disco de Dados|de palavras|Média|Bytes máximos por segundo de produção O disco de dados pode alcançar com a explosão|RIO LUN|
|IOPS de explosão de disco de dados Max|Yes|IOPS de explosão de disco de dados Max|de palavras|Média|O disco de dados máximo do IOPS pode alcançar com a explosão|RIO LUN|
|Profundidade da Fila do Disco de Dados|Yes|Profundidade da Fila do Disco de Dados|de palavras|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|RIO LUN|
|Data Disk Ler Bytes/seg|Yes|Data Disk Read Bytes/Sec|BytesPerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|RIO LUN|
|Operações de leitura de discos de dados/seg|Yes|Operações de leitura de discos de dados/seg|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização|RIO LUN|
|Largura de banda do alvo do disco de dados|Yes|Largura de banda do alvo do disco de dados|de palavras|Média|Bytes de base por segundo de produção O disco de dados pode alcançar sem rebentar|RIO LUN|
|IOPS alvo de disco de dados|Yes|IOPS alvo de disco de dados|de palavras|Média|O disco de dados do IOPS de base pode alcançar sem rebentar|RIO LUN|
|Disco de dados usado burst BPS Credits Percentagem|Yes|Disco de dados usado burst BPS Credits Percentagem|Percentagem|Média|Percentagem de créditos de largura de banda rebentados em disco de dados utilizados até agora|RIO LUN|
|Disco de dados usado burst io credits percentagem|Yes|Disco de dados usado burst io credits percentagem|Percentagem|Média|Percentagem de créditos de E/S de explosão de disco de dados utilizados até agora|RIO LUN|
|Bytes/seg de escrita de disco de dados|Yes|Bytes de escrita de discos de dados/seg|BytesPerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização|RIO LUN|
|Operações de escrita de discos de dados/seg|Yes|Operações de escrita de discos de dados/seg|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|RIO LUN|
|Bytes de leitura de disco|Yes|Bytes de leitura de disco|Bytes|Total|Bytes lidos a partir de disco durante o período de monitorização|Sem Dimensões|
|Operações de leitura de disco/sec|Yes|Operações de leitura de disco/sec|CondePerSecond|Média|IOPS de leitura de disco|Sem Dimensões|
|Bytes de escrita de disco|Yes|Bytes de escrita de disco|Bytes|Total|Bytes escritos para o disco durante o período de monitorização|Sem Dimensões|
|Operações de escrita de discos/seg|Yes|Operações de escrita de discos/seg|CondePerSecond|Média|IOPS de escrita de disco|Sem Dimensões|
|Fluxos de Entrada|Yes|Fluxos de Entrada|de palavras|Média|Fluxos de entrada são um número de fluxos correntes no sentido de entrada (tráfego que vai para o VM)|Sem Dimensões|
|Fluxos de entrada Taxa máxima de criação|Yes|Fluxos de entrada Taxa máxima de criação|CondePerSecond|Média|A taxa máxima de criação dos fluxos de entrada (tráfego que vai para o VM)|Sem Dimensões|
|Entrada na Rede|Yes|Rede em Faturação (Depreciada)|Bytes|Total|O número de bytes faturados recebidos em todas as interfaces de rede pela(s) Máquina Virtual(Tráfego De entrada) (Depreifed)|Sem Dimensões|
|Rede no total|Yes|Rede no total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela Máquina Virtual(s) (Tráfego de Entrada)|Sem Dimensões|
|Saída da Rede|Yes|Network out Billable (Precotado)|Bytes|Total|O número de bytes faturados em todas as interfaces de rede pela(s) Máquina Virtual(Tráfego De saída) (Depreifed)|Sem Dimensões|
|Rede Total|Yes|Rede Total|Bytes|Total|O número de bytes em todas as interfaces de rede pela(s) Máquina Virtual(s) (Tráfego de Saída)|Sem Dimensões|
|Largura de banda do disco ossauma percentagem|Yes|Largura de banda do disco ossauma percentagem|Percentagem|Média|Percentagem de largura de banda do sistema operativo consumida por minuto|RIO LUN|
|Percentagem consumida em disco de OS|Yes|Percentagem consumida em disco de OS|Percentagem|Média|Percentagem do disco do sistema operativo I/Os consumido por minuto|RIO LUN|
|Largura de banda de max explosão de disco do DISCO Max|Yes|Largura de banda de max explosão de disco do DISCO Max|de palavras|Média|Bytes máximos por segundo de produção os discos OS podem alcançar com explosão|RIO LUN|
|IOPS de explosão de disco de OS Max|Yes|IOPS de explosão de disco de OS Max|de palavras|Média|Máximo IOPS OS Disco pode alcançar com explosão|RIO LUN|
|Profundidade da Fila do Disco do SO|Yes|Profundidade da Fila do Disco do SO|de palavras|Média|Profundidade da fila do disco os(ou comprimento da fila)|Sem Dimensões|
|Leitura de discos de OS Bytes/seg|Yes|Os Discos de Leitura de Bytes/Sec|BytesPerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização para o disco de SO|Sem Dimensões|
|Operações de leitura de disco de OS/Sec|Yes|Operações de leitura de disco de OS/Sec|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização do disco de so|Sem Dimensões|
|Largura de banda alvo do disco de OS|Yes|Largura de banda alvo do disco de OS|de palavras|Média|Bytes de base por segundo de produção os discos OS podem alcançar sem rebentar|RIO LUN|
|IOPS alvo do disco de OS|Yes|IOPS alvo do disco de OS|de palavras|Média|Disco de IOPS OS de base pode alcançar sem rebentar|RIO LUN|
|Disco de OS usado burst BPS Percentagem de créditos|Yes|Disco de OS usado burst BPS Percentagem de créditos|Percentagem|Média|Percentagem de créditos de largura de banda rebentados com os discos de SO usados até agora|RIO LUN|
|Disco de SO usado Burst IO Credits Percentagem|Yes|Disco de SO usado Burst IO Credits Percentagem|Percentagem|Média|Percentagem de créditos de I/O de explosão de disco de SO utilizados até agora|RIO LUN|
|Os Discos de Escrita bytes/seg|Yes|Os Discos de Escrita bytes/Seg|BytesPerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização do disco OS|Sem Dimensões|
|Operações de escrita de discos de OS/Sec|Yes|Operações de escrita de discos de OS/Sec|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco de so|Sem Dimensões|
|Fluxos de saída|Yes|Fluxos de saída|de palavras|Média|Fluxos de saída são um número de fluxos correntes no sentido de saída (tráfego que sai do VM)|Sem Dimensões|
|Taxa máxima de criação de fluxos de saída|Yes|Taxa máxima de criação de fluxos de saída|CondePerSecond|Média|A taxa máxima de criação dos fluxos de saída (tráfego que sai do VM)|Sem Dimensões|
|Percentagem da CPU|Yes|Percentagem da CPU|Percentagem|Média|A percentagem de unidades de computação atribuídas que estão atualmente a ser utilizadas pela Máquina Virtual(s)|Sem Dimensões|
|Sucesso de leitura de cache de disco de dados premium|Yes|Sucesso de leitura de cache de disco de dados premium|Percentagem|Média|Sucesso de leitura de cache de disco de dados premium|RIO LUN|
|Cache de disco de dados premium Ler Miss|Yes|Cache de disco de dados premium Ler Miss|Percentagem|Média|Cache de disco de dados premium Ler Miss|RIO LUN|
|Premium OS Disk Cache Ler Hit|Yes|Premium OS Disk Cache Ler Hit|Percentagem|Média|Premium OS Disk Cache Ler Hit|Sem Dimensões|
|Premium OS Cache De Disco Ler Miss|Yes|Premium OS Cache De Disco Ler Miss|Percentagem|Média|Premium OS Cache De Disco Ler Miss|Sem Dimensões|
|VM Cached Bandwidth Consumido Percentagem|Yes|VM Cached Bandwidth Consumido Percentagem|Percentagem|Média|Percentagem de largura de banda em cache consumida pelo VM|Sem Dimensões|
|VM Cached IOPS Percentagem consumida|Yes|VM Cached IOPS Percentagem consumida|Percentagem|Média|Percentagem de IOPS de disco em cache consumido pelo VM|Sem Dimensões|
|VM Largura de Banda Não Colada Consumida Percentagem|Yes|VM Largura de Banda Não Colada Consumida Percentagem|Percentagem|Média|Percentagem de largura de banda de disco não colada consumida pelo VM|Sem Dimensões|
|VM IOPS EdvolvidoS Percentagem Consumida|Yes|VM IOPS EdvolvidoS Percentagem Consumida|Percentagem|Média|Percentagem de IOPS de disco não colado consumido pelo VM|Sem Dimensões|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Créditos CPU Consumidos|Yes|Créditos CPU Consumidos|de palavras|Média|Número total de créditos consumidos pela Máquina Virtual|Sem Dimensões|
|Créditos CPU Remanescentes|Yes|Créditos CPU Remanescentes|de palavras|Média|Número total de créditos disponíveis para rebentar|Sem Dimensões|
|Percentagem consumida da largura de banda do disco de dados|Yes|Percentagem consumida da largura de banda do disco de dados|Percentagem|Média|Percentagem de largura de banda de disco de dados consumida por minuto|LUN, VMName|
|Percentagem consumida em IOPS do disco de dados|Yes|Percentagem consumida em IOPS do disco de dados|Percentagem|Média|Percentagem de disco de dados I/Os consumido por minuto|LUN, VMName|
|Largura de banda Max Burst Do Disco de Dados|Yes|Largura de banda Max Burst Do Disco de Dados|de palavras|Média|Bytes máximos por segundo de produção O disco de dados pode alcançar com a explosão|LUN, VMName|
|IOPS de explosão de disco de dados Max|Yes|IOPS de explosão de disco de dados Max|de palavras|Média|O disco de dados máximo do IOPS pode alcançar com a explosão|LUN, VMName|
|Profundidade da Fila do Disco de Dados|Yes|Profundidade da Fila do Disco de Dados|de palavras|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|LUN, VMName|
|Data Disk Ler Bytes/seg|Yes|Data Disk Read Bytes/Sec|BytesPerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|LUN, VMName|
|Operações de leitura de discos de dados/seg|Yes|Operações de leitura de discos de dados/seg|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização|LUN, VMName|
|Largura de banda do alvo do disco de dados|Yes|Largura de banda do alvo do disco de dados|de palavras|Média|Bytes de base por segundo de produção O disco de dados pode alcançar sem rebentar|LUN, VMName|
|IOPS alvo de disco de dados|Yes|IOPS alvo de disco de dados|de palavras|Média|O disco de dados do IOPS de base pode alcançar sem rebentar|LUN, VMName|
|Disco de dados usado burst BPS Credits Percentagem|Yes|Disco de dados usado burst BPS Credits Percentagem|Percentagem|Média|Percentagem de créditos de largura de banda rebentados em disco de dados utilizados até agora|LUN, VMName|
|Disco de dados usado burst io credits percentagem|Yes|Disco de dados usado burst io credits percentagem|Percentagem|Média|Percentagem de créditos de E/S de explosão de disco de dados utilizados até agora|LUN, VMName|
|Bytes/seg de escrita de disco de dados|Yes|Bytes de escrita de discos de dados/seg|BytesPerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização|LUN, VMName|
|Operações de escrita de discos de dados/seg|Yes|Operações de escrita de discos de dados/seg|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|LUN, VMName|
|Bytes de leitura de disco|Yes|Bytes de leitura de disco|Bytes|Total|Bytes lidos a partir de disco durante o período de monitorização|VMName|
|Operações de leitura de disco/sec|Yes|Operações de leitura de disco/sec|CondePerSecond|Média|IOPS de leitura de disco|VMName|
|Bytes de escrita de disco|Yes|Bytes de escrita de disco|Bytes|Total|Bytes escritos para o disco durante o período de monitorização|VMName|
|Operações de escrita de discos/seg|Yes|Operações de escrita de discos/seg|CondePerSecond|Média|IOPS de escrita de disco|VMName|
|Fluxos de Entrada|Yes|Fluxos de Entrada|de palavras|Média|Fluxos de entrada são um número de fluxos correntes no sentido de entrada (tráfego que vai para o VM)|VMName|
|Fluxos de entrada Taxa máxima de criação|Yes|Fluxos de entrada Taxa máxima de criação|CondePerSecond|Média|A taxa máxima de criação dos fluxos de entrada (tráfego que vai para o VM)|VMName|
|Entrada na Rede|Yes|Rede em Faturação (Depreciada)|Bytes|Total|O número de bytes faturados recebidos em todas as interfaces de rede pela(s) Máquina Virtual(Tráfego De entrada) (Depreifed)|VMName|
|Rede no total|Yes|Rede no total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela Máquina Virtual(s) (Tráfego de Entrada)|VMName|
|Saída da Rede|Yes|Network out Billable (Precotado)|Bytes|Total|O número de bytes faturados em todas as interfaces de rede pela(s) Máquina Virtual(Tráfego De saída) (Depreifed)|VMName|
|Rede Total|Yes|Rede Total|Bytes|Total|O número de bytes em todas as interfaces de rede pela(s) Máquina Virtual(s) (Tráfego de Saída)|VMName|
|Largura de banda do disco ossauma percentagem|Yes|Largura de banda do disco ossauma percentagem|Percentagem|Média|Percentagem de largura de banda do sistema operativo consumida por minuto|LUN, VMName|
|Percentagem consumida em disco de OS|Yes|Percentagem consumida em disco de OS|Percentagem|Média|Percentagem do disco do sistema operativo I/Os consumido por minuto|LUN, VMName|
|Largura de banda de max explosão de disco do DISCO Max|Yes|Largura de banda de max explosão de disco do DISCO Max|de palavras|Média|Bytes máximos por segundo de produção os discos OS podem alcançar com explosão|LUN, VMName|
|IOPS de explosão de disco de OS Max|Yes|IOPS de explosão de disco de OS Max|de palavras|Média|Máximo IOPS OS Disco pode alcançar com explosão|LUN, VMName|
|Profundidade da Fila do Disco do SO|Yes|Profundidade da Fila do Disco do SO|de palavras|Média|Profundidade da fila do disco os(ou comprimento da fila)|VMName|
|Leitura de discos de OS Bytes/seg|Yes|Os Discos de Leitura de Bytes/Sec|BytesPerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização para o disco de SO|VMName|
|Operações de leitura de disco de OS/Sec|Yes|Operações de leitura de disco de OS/Sec|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização do disco de so|VMName|
|Largura de banda alvo do disco de OS|Yes|Largura de banda alvo do disco de OS|de palavras|Média|Bytes de base por segundo de produção os discos OS podem alcançar sem rebentar|LUN, VMName|
|IOPS alvo do disco de OS|Yes|IOPS alvo do disco de OS|de palavras|Média|Disco de IOPS OS de base pode alcançar sem rebentar|LUN, VMName|
|Disco de OS usado burst BPS Percentagem de créditos|Yes|Disco de OS usado burst BPS Percentagem de créditos|Percentagem|Média|Percentagem de créditos de largura de banda rebentados com os discos de SO usados até agora|LUN, VMName|
|Disco de SO usado Burst IO Credits Percentagem|Yes|Disco de SO usado Burst IO Credits Percentagem|Percentagem|Média|Percentagem de créditos de I/O de explosão de disco de SO utilizados até agora|LUN, VMName|
|Os Discos de Escrita bytes/seg|Yes|Os Discos de Escrita bytes/Seg|BytesPerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização do disco OS|VMName|
|Operações de escrita de discos de OS/Sec|Yes|Operações de escrita de discos de OS/Sec|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco de so|VMName|
|Fluxos de saída|Yes|Fluxos de saída|de palavras|Média|Fluxos de saída são um número de fluxos correntes no sentido de saída (tráfego que sai do VM)|VMName|
|Taxa máxima de criação de fluxos de saída|Yes|Taxa máxima de criação de fluxos de saída|CondePerSecond|Média|A taxa máxima de criação dos fluxos de saída (tráfego que sai do VM)|VMName|
|Percentagem da CPU|Yes|Percentagem da CPU|Percentagem|Média|A percentagem de unidades de computação atribuídas que estão atualmente a ser utilizadas pela Máquina Virtual(s)|VMName|
|Sucesso de leitura de cache de disco de dados premium|Yes|Sucesso de leitura de cache de disco de dados premium|Percentagem|Média|Sucesso de leitura de cache de disco de dados premium|LUN, VMName|
|Cache de disco de dados premium Ler Miss|Yes|Cache de disco de dados premium Ler Miss|Percentagem|Média|Cache de disco de dados premium Ler Miss|LUN, VMName|
|Premium OS Disk Cache Ler Hit|Yes|Premium OS Disk Cache Ler Hit|Percentagem|Média|Premium OS Disk Cache Ler Hit|VMName|
|Premium OS Cache De Disco Ler Miss|Yes|Premium OS Cache De Disco Ler Miss|Percentagem|Média|Premium OS Cache De Disco Ler Miss|VMName|
|VM Cached Bandwidth Consumido Percentagem|Yes|VM Cached Bandwidth Consumido Percentagem|Percentagem|Média|Percentagem de largura de banda em cache consumida pelo VM|VMName|
|VM Cached IOPS Percentagem consumida|Yes|VM Cached IOPS Percentagem consumida|Percentagem|Média|Percentagem de IOPS de disco em cache consumido pelo VM|VMName|
|VM Largura de Banda Não Colada Consumida Percentagem|Yes|VM Largura de Banda Não Colada Consumida Percentagem|Percentagem|Média|Percentagem de largura de banda de disco não colada consumida pelo VM|VMName|
|VM IOPS EdvolvidoS Percentagem Consumida|Yes|VM IOPS EdvolvidoS Percentagem Consumida|Percentagem|Média|Percentagem de IOPS de disco não colado consumido pelo VM|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Créditos CPU Consumidos|Yes|Créditos CPU Consumidos|de palavras|Média|Número total de créditos consumidos pela Máquina Virtual|Sem Dimensões|
|Créditos CPU Remanescentes|Yes|Créditos CPU Remanescentes|de palavras|Média|Número total de créditos disponíveis para rebentar|Sem Dimensões|
|Percentagem consumida da largura de banda do disco de dados|Yes|Percentagem consumida da largura de banda do disco de dados|Percentagem|Média|Percentagem de largura de banda de disco de dados consumida por minuto|RIO LUN|
|Percentagem consumida em IOPS do disco de dados|Yes|Percentagem consumida em IOPS do disco de dados|Percentagem|Média|Percentagem de disco de dados I/Os consumido por minuto|RIO LUN|
|Largura de banda Max Burst Do Disco de Dados|Yes|Largura de banda Max Burst Do Disco de Dados|de palavras|Média|Bytes máximos por segundo de produção O disco de dados pode alcançar com a explosão|RIO LUN|
|IOPS de explosão de disco de dados Max|Yes|IOPS de explosão de disco de dados Max|de palavras|Média|O disco de dados máximo do IOPS pode alcançar com a explosão|RIO LUN|
|Profundidade da Fila do Disco de Dados|Yes|Profundidade da Fila do Disco de Dados|de palavras|Média|Profundidade da fila do disco de dados (ou comprimento da fila)|RIO LUN|
|Data Disk Ler Bytes/seg|Yes|Data Disk Read Bytes/Sec|BytesPerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização|RIO LUN|
|Operações de leitura de discos de dados/seg|Yes|Operações de leitura de discos de dados/seg|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização|RIO LUN|
|Largura de banda do alvo do disco de dados|Yes|Largura de banda do alvo do disco de dados|de palavras|Média|Bytes de base por segundo de produção O disco de dados pode alcançar sem rebentar|RIO LUN|
|IOPS alvo de disco de dados|Yes|IOPS alvo de disco de dados|de palavras|Média|O disco de dados do IOPS de base pode alcançar sem rebentar|RIO LUN|
|Disco de dados usado burst BPS Credits Percentagem|Yes|Disco de dados usado burst BPS Credits Percentagem|Percentagem|Média|Percentagem de créditos de largura de banda rebentados em disco de dados utilizados até agora|RIO LUN|
|Disco de dados usado burst io credits percentagem|Yes|Disco de dados usado burst io credits percentagem|Percentagem|Média|Percentagem de créditos de E/S de explosão de disco de dados utilizados até agora|RIO LUN|
|Bytes/seg de escrita de disco de dados|Yes|Bytes de escrita de discos de dados/seg|BytesPerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização|RIO LUN|
|Operações de escrita de discos de dados/seg|Yes|Operações de escrita de discos de dados/seg|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização|RIO LUN|
|Bytes de leitura de disco|Yes|Bytes de leitura de disco|Bytes|Total|Bytes lidos a partir de disco durante o período de monitorização|Sem Dimensões|
|Operações de leitura de disco/sec|Yes|Operações de leitura de disco/sec|CondePerSecond|Média|IOPS de leitura de disco|Sem Dimensões|
|Bytes de escrita de disco|Yes|Bytes de escrita de disco|Bytes|Total|Bytes escritos para o disco durante o período de monitorização|Sem Dimensões|
|Operações de escrita de discos/seg|Yes|Operações de escrita de discos/seg|CondePerSecond|Média|IOPS de escrita de disco|Sem Dimensões|
|Fluxos de Entrada|Yes|Fluxos de Entrada|de palavras|Média|Fluxos de entrada são um número de fluxos correntes no sentido de entrada (tráfego que vai para o VM)|Sem Dimensões|
|Fluxos de entrada Taxa máxima de criação|Yes|Fluxos de entrada Taxa máxima de criação|CondePerSecond|Média|A taxa máxima de criação dos fluxos de entrada (tráfego que vai para o VM)|Sem Dimensões|
|Entrada na Rede|Yes|Rede em Faturação (Depreciada)|Bytes|Total|O número de bytes faturados recebidos em todas as interfaces de rede pela(s) Máquina Virtual(Tráfego De entrada) (Depreifed)|Sem Dimensões|
|Rede no total|Yes|Rede no total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela Máquina Virtual(s) (Tráfego de Entrada)|Sem Dimensões|
|Saída da Rede|Yes|Network out Billable (Precotado)|Bytes|Total|O número de bytes faturados em todas as interfaces de rede pela(s) Máquina Virtual(Tráfego De saída) (Depreifed)|Sem Dimensões|
|Rede Total|Yes|Rede Total|Bytes|Total|O número de bytes em todas as interfaces de rede pela(s) Máquina Virtual(s) (Tráfego de Saída)|Sem Dimensões|
|Largura de banda do disco ossauma percentagem|Yes|Largura de banda do disco ossauma percentagem|Percentagem|Média|Percentagem de largura de banda do sistema operativo consumida por minuto|RIO LUN|
|Percentagem consumida em disco de OS|Yes|Percentagem consumida em disco de OS|Percentagem|Média|Percentagem do disco do sistema operativo I/Os consumido por minuto|RIO LUN|
|Largura de banda de max explosão de disco do DISCO Max|Yes|Largura de banda de max explosão de disco do DISCO Max|de palavras|Média|Bytes máximos por segundo de produção os discos OS podem alcançar com explosão|RIO LUN|
|IOPS de explosão de disco de OS Max|Yes|IOPS de explosão de disco de OS Max|de palavras|Média|Máximo IOPS OS Disco pode alcançar com explosão|RIO LUN|
|Profundidade da Fila do Disco do SO|Yes|Profundidade da Fila do Disco do SO|de palavras|Média|Profundidade da fila do disco os(ou comprimento da fila)|Sem Dimensões|
|Leitura de discos de OS Bytes/seg|Yes|Os Discos de Leitura de Bytes/Sec|BytesPerSecond|Média|Bytes/Sec lidos a partir de um único disco durante o período de monitorização para o disco de SO|Sem Dimensões|
|Operações de leitura de disco de OS/Sec|Yes|Operações de leitura de disco de OS/Sec|CondePerSecond|Média|Leia IOPS a partir de um único disco durante o período de monitorização do disco de so|Sem Dimensões|
|Largura de banda alvo do disco de OS|Yes|Largura de banda alvo do disco de OS|de palavras|Média|Bytes de base por segundo de produção os discos OS podem alcançar sem rebentar|RIO LUN|
|IOPS alvo do disco de OS|Yes|IOPS alvo do disco de OS|de palavras|Média|Disco de IOPS OS de base pode alcançar sem rebentar|RIO LUN|
|Disco de OS usado burst BPS Percentagem de créditos|Yes|Disco de OS usado burst BPS Percentagem de créditos|Percentagem|Média|Percentagem de créditos de largura de banda rebentados com os discos de SO usados até agora|RIO LUN|
|Disco de SO usado Burst IO Credits Percentagem|Yes|Disco de SO usado Burst IO Credits Percentagem|Percentagem|Média|Percentagem de créditos de I/O de explosão de disco de SO utilizados até agora|RIO LUN|
|Os Discos de Escrita bytes/seg|Yes|Os Discos de Escrita bytes/Seg|BytesPerSecond|Média|Bytes/Sec escritos num único disco durante o período de monitorização do disco OS|Sem Dimensões|
|Operações de escrita de discos de OS/Sec|Yes|Operações de escrita de discos de OS/Sec|CondePerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitorização do disco de so|Sem Dimensões|
|Fluxos de saída|Yes|Fluxos de saída|de palavras|Média|Fluxos de saída são um número de fluxos correntes no sentido de saída (tráfego que sai do VM)|Sem Dimensões|
|Taxa máxima de criação de fluxos de saída|Yes|Taxa máxima de criação de fluxos de saída|CondePerSecond|Média|A taxa máxima de criação dos fluxos de saída (tráfego que sai do VM)|Sem Dimensões|
|Percentagem da CPU|Yes|Percentagem da CPU|Percentagem|Média|A percentagem de unidades de computação atribuídas que estão atualmente a ser utilizadas pela Máquina Virtual(s)|Sem Dimensões|
|Sucesso de leitura de cache de disco de dados premium|Yes|Sucesso de leitura de cache de disco de dados premium|Percentagem|Média|Sucesso de leitura de cache de disco de dados premium|RIO LUN|
|Cache de disco de dados premium Ler Miss|Yes|Cache de disco de dados premium Ler Miss|Percentagem|Média|Cache de disco de dados premium Ler Miss|RIO LUN|
|Premium OS Disk Cache Ler Hit|Yes|Premium OS Disk Cache Ler Hit|Percentagem|Média|Premium OS Disk Cache Ler Hit|Sem Dimensões|
|Premium OS Cache De Disco Ler Miss|Yes|Premium OS Cache De Disco Ler Miss|Percentagem|Média|Premium OS Cache De Disco Ler Miss|Sem Dimensões|
|VM Cached Bandwidth Consumido Percentagem|Yes|VM Cached Bandwidth Consumido Percentagem|Percentagem|Média|Percentagem de largura de banda em cache consumida pelo VM|Sem Dimensões|
|VM Cached IOPS Percentagem consumida|Yes|VM Cached IOPS Percentagem consumida|Percentagem|Média|Percentagem de IOPS de disco em cache consumido pelo VM|Sem Dimensões|
|VM Largura de Banda Não Colada Consumida Percentagem|Yes|VM Largura de Banda Não Colada Consumida Percentagem|Percentagem|Média|Percentagem de largura de banda de disco não colada consumida pelo VM|Sem Dimensões|
|VM IOPS EdvolvidoS Percentagem Consumida|Yes|VM IOPS EdvolvidoS Percentagem Consumida|Percentagem|Média|Percentagem de IOPS de disco não colado consumido pelo VM|Sem Dimensões|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|CpuUsage|Yes|Utilização da CPU|de palavras|Média|Utilização do CPU em todos os núcleos em millicores.|nome de contentor|
|MemóriaSage|Yes|Utilização de Memória|Bytes|Média|Uso total da memória em byte.|nome de contentor|
|NetworkBytesReceivedPerSecond|Yes|Bytes de rede recebidos por segundo|Bytes|Média|Os bytes de rede recebidos por segundo.|Sem Dimensões|
|NetworkBytesTransmittedPerSecond|Yes|Bytes de rede transmitidos por segundo|Bytes|Média|Os bytes de rede transmitidos por segundo.|Sem Dimensões|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|Yes|Hora do CPU do AgentPool|Segundos|Total|Tempo do AgentPool CPU em segundos|Sem Dimensões|
|RunDuration|Yes|Duração do funcional|Milissegundos|Total|Duração da execução em milissegundos|Sem Dimensões|
|SucessoPullCount|Yes|Contagem de pull com sucesso|de palavras|Média|Número de puxas de imagem bem-sucedidas|Sem Dimensões|
|SucessoPushCount|Yes|Contagem de impulsos bem sucedida|de palavras|Média|Número de impulsos de imagem bem sucedidos|Sem Dimensões|
|TotalPullCount|Yes|Contagem total de pull|de palavras|Média|Número de tiras de imagem no total|Sem Dimensões|
|TotalPushCount|Yes|Contagem total de push|de palavras|Média|Número de impulsos de imagem no total|Sem Dimensões|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|apiserver_current_inflight_requests|No|Pedidos de voo|de palavras|Média|Número máximo de pedidos de voo atualmente utilizados no apiserver por pedido tipo no último segundo|requestKind|
|kube_node_status_allocatable_cpu_cores|No|Número total de núcleos cpu disponíveis num cluster gerido|de palavras|Média|Número total de núcleos cpu disponíveis num cluster gerido||
|kube_node_status_allocatable_memory_bytes|No|Quantidade total de memória disponível num cluster gerido|Bytes|Média|Quantidade total de memória disponível num cluster gerido||
|kube_node_status_condition|No|Estados para várias condições de nó|de palavras|Média|Estados para várias condições de nó|condição, estado, estado2, nó|
|kube_pod_status_phase|No|Número de cápsulas por fase|de palavras|Média|Número de cápsulas por fase|fase, espaço de nome, vagem|
|kube_pod_status_ready|No|Número de cápsulas no estado de Ready|de palavras|Média|Número de cápsulas no estado de Ready|espaço de nome, vagem, condição|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Requessos Falhados|Yes|Pedidos com Falhas|de palavras|Total|Obtém os registos disponíveis para Fornecedores de Recursos Personalizados|HttpMethod, CallPath, StatusCode|
|Requestres com sucesso|Yes|Pedidos Com Êxito|de palavras|Total|Pedidos bem sucedidos feitos pelo fornecedor personalizado|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Capacidade disponível|Yes|Capacidade disponível|Bytes|Média|A capacidade disponível nos bytes durante o período de reporte.|Sem Dimensões|
|BytesUploadedToCloud|Yes|Cloud Bytes Uploaded (Dispositivo)|Bytes|Média|O número total de bytes que é enviado para Azure a partir de um dispositivo durante o período de reporte.|Sem Dimensões|
|BytesUploadedToCloudPerShare|Yes|Cloud Bytes Uploaded (Partilhar)|Bytes|Média|O número total de bytes que é enviado para Azure a partir de uma parte durante o período de reporte.|Partilhar|
|CloudReadThroughput|Yes|Download da Cloud|BytesPerSecond|Média|A produção de download da nuvem para Azure durante o período de reporte.|Sem Dimensões|
|CloudReadThroughputPerShare|Yes|Download cloud 'Produção' (Partilhar)|BytesPerSecond|Média|O download para Azure de uma parte durante o período de reporte.|Partilhar|
|CloudUploadThroughput|Yes|Produção de upload de nuvens|BytesPerSecond|Média|A nuvem envia para Azure durante o período de reporte.|Sem Dimensões|
|CloudUploadThroughputPerShare|Yes|Produção de upload de nuvem (partilhar)|BytesPerSecond|Média|O envio para a Azure de uma parte durante o período de reporte.|Partilhar|
|Hipervmemoryutilização|Yes|Edge Compute - Utilização da Memória|Percentagem|Média|Quantidade de RAM em uso|InstanceName|
|HiperVVirtualProcessorutilização|Yes|Edge Compute - Percentagem CPU|Percentagem|Média|Por cento uso cpu|InstanceName|
|NICReadThroughput|Yes|Ler produção (rede)|BytesPerSecond|Média|A produção de leitura da interface de rede no dispositivo no período de reporte para todos os volumes no gateway.|InstanceName|
|NICWriteThroughput|Yes|Escrever Produção (Rede)|BytesPerSecond|Média|A produção de produção da interface de rede no dispositivo no período de reporte para todos os volumes no gateway.|InstanceName|
|Capacidade Total|Yes|Capacidade Total|Bytes|Média|Capacidade Total|Sem Dimensões|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft.DataCollaboration/workspaces

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|DataAssetCount|Yes|Ativos de Dados Criados|de palavras|Máximo|Número de ativos de dados criados|DataAssetName|
|PipelineCount|Yes|Oleodutos Criados|de palavras|Máximo|Número de oleodutos criados|PipelineName|
|PropostaCount|Yes|Propostas Criadas|de palavras|Máximo|Número de propostas criadas|Nome de Proposta|
|ScriptCount|Yes|Scripts criados|de palavras|Máximo|Número de scripts criados|Nome do Guião|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Corridas Falhadas|Yes|Corridas falhadas|de palavras|Total||pipelineName, atividadeName|
|Sucesso DeRuns|Yes|Corridas de sucesso|de palavras|Total||pipelineName, atividadeName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/fábricas

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AtividadeCancelledRuns|Yes|Atividade cancelada executa métricas|de palavras|Total||ActivityType, PipelineName, FailureType, Name|
|AtividadeFailedRuns|Yes|Atividade falhada executa métricas|de palavras|Total||ActivityType, PipelineName, FailureType, Name|
|AtividadeSSucceedEdRuns|Yes|A atividade bem sucedida executa métricas|de palavras|Total||ActivityType, PipelineName, FailureType, Name|
|FactorySizeInGbUnits|Yes|Tamanho total da fábrica (unidade GB)|de palavras|Máximo||Sem Dimensões|
|IntegrationRuntimeAvailableMemory|Yes|Integração memória disponível|Bytes|Média||IntegraçãoRuntimeName, Nome node|
|IntegrationRuntimeAvailableNodeNumber|Yes|Contagem de nó disponível de integração|de palavras|Média||IntegraçãoRuntimeName|
|IntegraçãoRuntimeAverageTaskPickupDelay|Yes|Duração da fila de tempo de execução de integração|Segundos|Média||IntegraçãoRuntimeName|
|IntegraçãoRuntimeCpuPercentage|Yes|Utilização do CPU em tempo de integração|Percentagem|Média||IntegraçãoRuntimeName, Nome node|
|IntegraçãoRuntimeQueueLength|Yes|Comprimento da fila de tempo de execução de integração|de palavras|Média||IntegraçãoRuntimeName|
|MaxAllowedFactorySizeInGbUnits|Yes|Tamanho máximo permitido da fábrica (unidade GB)|de palavras|Máximo||Sem Dimensões|
|MaxAllowedResourceCount|Yes|Contagem máxima de entidades permitidas|de palavras|Máximo||Sem Dimensões|
|PipelineCancelledRuns|Yes|Gasoduto cancelado executa métricas|de palavras|Total||Falha, Nome|
|PipelineElapsedTimeRuns|Yes|Pipeline de tempo decorrido executa métricas|de palavras|Total||RunId, Nome|
|PipelineFailedRuns|Yes|Gasoduto falhado executa métricas|de palavras|Total||Falha, Nome|
|PipelineSucceededRuns|Yes|Pipeline bem sucedido executa métricas|de palavras|Total||Falha, Nome|
|Contagem de Recursos|Yes|Total de entidades contam|de palavras|Máximo||Sem Dimensões|
|SSISIntegrationRuntimeStartCancel|Yes|Métricas de início de tempo de integração da SSIS canceladas|de palavras|Total||IntegraçãoRuntimeName|
|SSISIntegrationRuntimeStartFailed|Yes|Métricas falhadas de início de tempo de integração do SSIS|de palavras|Total||IntegraçãoRuntimeName|
|SSISIntegrationRuntimeStartSucceed|Yes|Métricas de início de tempo de integração SSIS bem sucedidas|de palavras|Total||IntegraçãoRuntimeName|
|SSISIntegrationRuntimestopStuck|Yes|Métricas de paragem de tempo de integração SSIS stuck|de palavras|Total||IntegraçãoRuntimeName|
|SSISIntegrationRuntimestopsucceed|Yes|Métricas de paragem de tempo de integração SSIS bem sucedidas|de palavras|Total||IntegraçãoRuntimeName|
|SSISPackageExecutionCancel|Yes|Métricas de execução de pacotes SSIS canceladas|de palavras|Total||IntegraçãoRuntimeName|
|SSISPackageExecutionFailed|Yes|Métricas de execução de pacote ssis falhados|de palavras|Total||IntegraçãoRuntimeName|
|SSISPackageExecutionSucceed|Yes|Métricas de execução de pacote ssis bem sucedidas|de palavras|Total||IntegraçãoRuntimeName|
|TriggerCancelledRuns|Yes|Gatilho cancelado executa métricas|de palavras|Total||Nome, FalhaType|
|TriggerFailedRuns|Yes|O gatilho falhado executa métricas|de palavras|Total||Nome, FalhaType|
|TriggerSucceeddRuns|Yes|O gatilho bem sucedido executa métricas|de palavras|Total||Nome, FalhaType|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/contas

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|JobAUEndedCancelled|Yes|Tempo cancelado da AU|Segundos|Total|Tempo total da AU para trabalhos cancelados.|Sem Dimensões|
|JobAUEndedFailure|Yes|Tempo AU falhado|Segundos|Total|Tempo total da AU para trabalhos falhados.|Sem Dimensões|
|JobAUEndedSuccess|Yes|Tempo de AU bem sucedido|Segundos|Total|Tempo total da AU para empregos bem sucedidos.|Sem Dimensões|
|JobEndedCancelled|Yes|Empregos Cancelados|de palavras|Total|Contagem de trabalhos cancelados.|Sem Dimensões|
|JobEndedFailure|Yes|Empregos Falhados|de palavras|Total|Contagem de trabalhos falhados.|Sem Dimensões|
|JobEndedSuccess|Yes|Empregos bem sucedidos|de palavras|Total|Contagem de trabalhos bem sucedidos.|Sem Dimensões|
|Palco jobs|Yes|Empregos em Palco|de palavras|Total|Número de empregos em cada etapa.|Sem Dimensões|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/contas

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|DataRead|Yes|Leitura de Dados|Bytes|Total|Quantidade total de dados lidos a partir da conta.|Sem Dimensões|
|DataWritten|Yes|Dados Escritos|Bytes|Total|Quantidade total de dados escritos na conta.|Sem Dimensões|
|ReadRequests|Yes|Ler Pedidos|de palavras|Total|Contagem de dados leia pedidos para a conta.|Sem Dimensões|
|TotalStorage|Yes|Armazenamento Total|Bytes|Máximo|Quantidade total de dados armazenados na conta.|Sem Dimensões|
|WriteRequests|Yes|Escrever Pedidos|de palavras|Total|A contagem de dados escreve pedidos para a conta.|Sem Dimensões|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/contas

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|FalhashareSubscriçãoSynchronizations|Yes|Fotos falhadas da partilha recebidas|de palavras|de palavras|Número de snapshots falhados da ação recebida na conta|Sem Dimensões|
|Falhas nas Sincronizações de Compartilhadas|Yes|Envio de imagens falhadas de partilha|de palavras|de palavras|Número de snapshots falhados da ação enviada na conta|Sem Dimensões|
|ShareCount|Yes|Ações enviadas|de palavras|Máximo|Número de ações enviadas na conta|Nome de partilha|
|ShareSubscriptionCount|Yes|Ações Recebidas|de palavras|Máximo|Número de ações recebidas na conta|Nome de Subscrição de Partilha|
|SucessoShareSubscriptionSynchronizations|Yes|Fotos bem sucedidas de partilha|de palavras|de palavras|Número de ações recebidas sucedeu instantâneos na conta|Sem Dimensões|
|Sucessos PartilharSynchronizations|Yes|Envio de ações instantâneas bem sucedidas|de palavras|de palavras|Número de ações enviadas instantâneas na conta|Sem Dimensões|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servidores

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|active_connections|Yes|Ligações Ativas|de palavras|Média|Ligações Ativas|Sem Dimensões|
|backup_storage_used|Yes|Armazenamento de backup utilizado|Bytes|Média|Armazenamento de backup utilizado|Sem Dimensões|
|connections_failed|Yes|Ligações com Falhas|de palavras|Total|Ligações com Falhas|Sem Dimensões|
|cpu_percent|Yes|CPU por cento|Percentagem|Média|CPU por cento|Sem Dimensões|
|io_consumption_percent|Yes|IO por cento|Percentagem|Média|IO por cento|Sem Dimensões|
|memory_percent|Yes|Por cento da memória|Percentagem|Média|Por cento da memória|Sem Dimensões|
|network_bytes_egress|Yes|Saída da Rede|Bytes|Total|Rede Para fora através de ligações ativas|Sem Dimensões|
|network_bytes_ingress|Yes|Entrada na Rede|Bytes|Total|Rede Em através de ligações ativas|Sem Dimensões|
|seconds_behind_master|Yes|Atraso de replicação em segundos|de palavras|Máximo|Atraso de replicação em segundos|Sem Dimensões|
|serverlog_storage_limit|Yes|Limite de armazenamento de registo de servidor|Bytes|Máximo|Limite de armazenamento de registo de servidor|Sem Dimensões|
|serverlog_storage_percent|Yes|Por cento de armazenamento de registo de servidor|Percentagem|Média|Por cento de armazenamento de registo de servidor|Sem Dimensões|
|serverlog_storage_usage|Yes|Armazenamento de registo de servidor utilizado|Bytes|Média|Armazenamento de registo de servidor utilizado|Sem Dimensões|
|storage_limit|Yes|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Sem Dimensões|
|storage_percent|Yes|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Sem Dimensões|
|storage_used|Yes|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Sem Dimensões|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|aborted_connections|Yes|Ligações Abortadas|de palavras|Total|Ligações Abortadas|Sem Dimensões|
|active_connections|Yes|Ligações Ativas|de palavras|Máximo|Ligações Ativas|Sem Dimensões|
|backup_storage_used|Yes|Armazenamento de backup usado|Bytes|Máximo|Armazenamento de backup usado|Sem Dimensões|
|cpu_percent|Yes|CpU anfitrião Por cento|Percentagem|Máximo|CpU anfitrião Por cento|Sem Dimensões|
|io_consumption_percent|Yes|IO Por cento|Percentagem|Máximo|IO Por cento|Sem Dimensões|
|memory_percent|Yes|Por cento da Memória do Anfitrião|Percentagem|Máximo|Por cento da Memória do Anfitrião|Sem Dimensões|
|network_bytes_egress|Yes|Rede de anfitriões fora|Bytes|Total|Saída da Rede de Anfitriões em bytes|Sem Dimensões|
|network_bytes_ingress|Yes|Rede de anfitriões em|Bytes|Total|Entrada da Rede de Anfitriões em bytes|Sem Dimensões|
|Consultas|Yes|Consultas|de palavras|Total|Consultas|Sem Dimensões|
|replication_lag|Yes|Lag de replicação em segundos|Segundos|Máximo|Atraso de replicação em segundos|Sem Dimensões|
|storage_limit|Yes|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Sem Dimensões|
|storage_percent|Yes|Por cento de Armazenamento|Percentagem|Máximo|Por cento de Armazenamento|Sem Dimensões|
|storage_used|Yes|Armazenamento utilizado|Bytes|Máximo|Armazenamento utilizado|Sem Dimensões|
|total_connections|Yes|Total de Ligações|de palavras|Total|Total de Ligações|Sem Dimensões|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servidores

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|active_connections|Yes|Ligações Ativas|de palavras|Média|Ligações Ativas|Sem Dimensões|
|backup_storage_used|Yes|Armazenamento de backup utilizado|Bytes|Média|Armazenamento de backup utilizado|Sem Dimensões|
|connections_failed|Yes|Ligações com Falhas|de palavras|Total|Ligações com Falhas|Sem Dimensões|
|cpu_percent|Yes|CPU por cento|Percentagem|Média|CPU por cento|Sem Dimensões|
|io_consumption_percent|Yes|IO por cento|Percentagem|Média|IO por cento|Sem Dimensões|
|memory_percent|Yes|Por cento da memória|Percentagem|Média|Por cento da memória|Sem Dimensões|
|network_bytes_egress|Yes|Saída da Rede|Bytes|Total|Rede Para fora através de ligações ativas|Sem Dimensões|
|network_bytes_ingress|Yes|Entrada na Rede|Bytes|Total|Rede Em através de ligações ativas|Sem Dimensões|
|seconds_behind_master|Yes|Atraso de replicação em segundos|de palavras|Máximo|Atraso de replicação em segundos|Sem Dimensões|
|serverlog_storage_limit|Yes|Limite de armazenamento de registo de servidor|Bytes|Máximo|Limite de armazenamento de registo de servidor|Sem Dimensões|
|serverlog_storage_percent|Yes|Por cento de armazenamento de registo de servidor|Percentagem|Média|Por cento de armazenamento de registo de servidor|Sem Dimensões|
|serverlog_storage_usage|Yes|Armazenamento de registo de servidor utilizado|Bytes|Média|Armazenamento de registo de servidor utilizado|Sem Dimensões|
|storage_limit|Yes|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Sem Dimensões|
|storage_percent|Yes|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Sem Dimensões|
|storage_used|Yes|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Sem Dimensões|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|active_connections|Yes|Ligações Ativas|de palavras|Média|Ligações Ativas|Sem Dimensões|
|backup_storage_used|Yes|Armazenamento de backup usado|Bytes|Média|Armazenamento de backup usado|Sem Dimensões|
|connections_failed|Yes|Ligações com Falhas|de palavras|Total|Ligações com Falhas|Sem Dimensões|
|connections_succeeded|Yes|Conexões bem sucedidas|de palavras|Total|Conexões bem sucedidas|Sem Dimensões|
|cpu_credits_consumed|Yes|Créditos CPU Consumidos|de palavras|Média|Número total de créditos consumidos pelo servidor de base de dados|Sem Dimensões|
|cpu_credits_remaining|Yes|Créditos CPU Remanescentes|de palavras|Média|Número total de créditos disponíveis para rebentar|Sem Dimensões|
|cpu_percent|Yes|CPU por cento|Percentagem|Média|CPU por cento|Sem Dimensões|
|disk_queue_depth|Yes|Profundidade da fila do disco|de palavras|Média|Número de operações de E/S pendentes no disco de dados|Sem Dimensões|
|iops|Yes|IOPS|de palavras|Média|Operações IO por segundo|Sem Dimensões|
|maximum_used_transactionIDs|Yes|IDs máximos de transações usadas|de palavras|Média|IDs máximos de transações usadas|Sem Dimensões|
|memory_percent|Yes|Por cento da memória|Percentagem|Média|Por cento da memória|Sem Dimensões|
|network_bytes_egress|Yes|Saída da Rede|Bytes|Total|Rede Para fora através de ligações ativas|Sem Dimensões|
|network_bytes_ingress|Yes|Entrada na Rede|Bytes|Total|Rede Em através de ligações ativas|Sem Dimensões|
|read_iops|Yes|Ler IOPS|de palavras|Média|Número de operações de leitura de disco de dados por segundo|Sem Dimensões|
|read_throughput|Yes|Ler Bytes de produção/Seg|de palavras|Média|Bytes lidos por segundo a partir do disco de dados durante o período de monitorização|Sem Dimensões|
|storage_free|Yes|Armazenamento gratuito|Bytes|Média|Armazenamento gratuito|Sem Dimensões|
|storage_percent|Yes|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Sem Dimensões|
|storage_used|Yes|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Sem Dimensões|
|txlogs_storage_used|Yes|Armazenamento de registo de transações utilizado|Bytes|Média|Armazenamento de registo de transações utilizado|Sem Dimensões|
|write_iops|Yes|Escrever iops|de palavras|Média|Número de operações de escrita de disco de dados por segundo|Sem Dimensões|
|write_throughput|Yes|Escrever Bytes de produção/Sec|de palavras|Média|Bytes escritos por segundo no disco de dados durante o período de monitorização|Sem Dimensões|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servidores

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|active_connections|Yes|Ligações Ativas|de palavras|Média|Ligações Ativas|Sem Dimensões|
|backup_storage_used|Yes|Armazenamento de backup usado|Bytes|Média|Armazenamento de backup usado|Sem Dimensões|
|connections_failed|Yes|Ligações com Falhas|de palavras|Total|Ligações com Falhas|Sem Dimensões|
|cpu_percent|Yes|CPU por cento|Percentagem|Média|CPU por cento|Sem Dimensões|
|io_consumption_percent|Yes|IO por cento|Percentagem|Média|IO por cento|Sem Dimensões|
|memory_percent|Yes|Por cento da memória|Percentagem|Média|Por cento da memória|Sem Dimensões|
|network_bytes_egress|Yes|Saída da Rede|Bytes|Total|Rede Para fora através de ligações ativas|Sem Dimensões|
|network_bytes_ingress|Yes|Entrada na Rede|Bytes|Total|Rede Em através de ligações ativas|Sem Dimensões|
|pg_replica_log_delay_in_bytes|Yes|Max Lag através de réplicas|Bytes|Máximo|Lag in bytes da réplica mais atrasada|Sem Dimensões|
|pg_replica_log_delay_in_seconds|Yes|Lag de réplica|Segundos|Máximo|Réplica lag em segundos|Sem Dimensões|
|serverlog_storage_limit|Yes|Limite de armazenamento de registo de servidor|Bytes|Máximo|Limite de armazenamento de registo de servidor|Sem Dimensões|
|serverlog_storage_percent|Yes|Por cento de armazenamento de registo de servidor|Percentagem|Média|Por cento de armazenamento de registo de servidor|Sem Dimensões|
|serverlog_storage_usage|Yes|Armazenamento de registo de servidor utilizado|Bytes|Média|Armazenamento de registo de servidor utilizado|Sem Dimensões|
|storage_limit|Yes|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Sem Dimensões|
|storage_percent|Yes|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Sem Dimensões|
|storage_used|Yes|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Sem Dimensões|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|active_connections|Yes|Ligações Ativas|de palavras|Média|Ligações Ativas|Sem Dimensões|
|cpu_percent|Yes|CPU por cento|Percentagem|Média|CPU por cento|Sem Dimensões|
|iops|Yes|IOPS|de palavras|Média|Operações IO por segundo|Sem Dimensões|
|memory_percent|Yes|Por cento da memória|Percentagem|Média|Por cento da memória|Sem Dimensões|
|network_bytes_egress|Yes|Saída da Rede|Bytes|Total|Rede Para fora através de ligações ativas|Sem Dimensões|
|network_bytes_ingress|Yes|Entrada na Rede|Bytes|Total|Rede Em através de ligações ativas|Sem Dimensões|
|storage_percent|Yes|Por cento de armazenamento|Percentagem|Média|Por cento de armazenamento|Sem Dimensões|
|storage_used|Yes|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|Sem Dimensões|


## <a name="microsoftdeviceselasticpools"></a>Microsoft.Devices/ElasticPools

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|elasticPool.requestedUsageRate|Yes|taxa de utilização solicitada|Percentagem|Média|taxa de utilização solicitada|Sem Dimensões|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|Yes|Mensagens C2D abandonadas|de palavras|Total|Número de mensagens cloud-to-device abandonadas pelo dispositivo|Sem Dimensões|
|c2d.commands.egress.complete.success|Yes|Entregas de mensagens C2D concluídas|de palavras|Total|Número de entregas de mensagens nuvem-para-dispositivo concluídas com sucesso pelo dispositivo|Sem Dimensões|
|c2d.commands.egress.reject.success|Yes|Mensagens C2D rejeitadas|de palavras|Total|Número de mensagens nuvem-dispositivo rejeitadas pelo dispositivo|Sem Dimensões|
|c2d.methods.falha|Yes|Invocações de método direto falhadas|de palavras|Total|A contagem de todas as chamadas de métodos diretos falhadas.|Sem Dimensões|
|c2d.methods.requestSize|Yes|Tamanho do pedido de invocações de método direto|Bytes|Média|A média, min e máxima de todos os pedidos de método direto bem sucedidos.|Sem Dimensões|
|c2d.methods.responseSize|Yes|Tamanho da resposta das invocações do método direto|Bytes|Média|A média, min e máxima de todas as respostas de métodos diretos bem sucedidas.|Sem Dimensões|
|c2d.methods.success|Yes|Invocações de métodos diretos bem-sucedidas|de palavras|Total|A contagem de todas as chamadas de métodos diretos bem sucedidos.|Sem Dimensões|
|c2d.twin.read.failure|Yes|Gémeo falhado lê do fundo|de palavras|Total|A contagem de todas as leituras gémeas falhadas.|Sem Dimensões|
|c2d.twin.read.size|Yes|Tamanho de resposta de leituras gémeas da parte de trás|Bytes|Média|A média, min, e máx.|Sem Dimensões|
|c2d.twin.read.success|Yes|Gémea bem sucedida lê a partir da parte de trás|de palavras|Total|A contagem de todas as leituras gémeas iniciadas com sucesso.|Sem Dimensões|
|c2d.twin.update.failure|Yes|Falhas em duas atualizações a partir do final|de palavras|Total|A contagem de todas as atualizações gémeas iniciadas pela parte de trás.|Sem Dimensões|
|c2d.twin.update.size|Yes|Tamanho das atualizações gémeas a partir da parte de trás|Bytes|Média|O tamanho médio, min e máximo de todas as atualizações gémeas iniciadas com sucesso.|Sem Dimensões|
|c2d.twin.update.success|Yes|Atualizações gémeas bem sucedidas a partir do final|de palavras|Total|A contagem de todas as atualizações gémeas iniciadas com sucesso.|Sem Dimensões|
|C2DMessagesExpired|Yes|Mensagens C2D expiradas|de palavras|Total|Número de mensagens de nuvem-para-dispositivo expiradas|Sem Dimensões|
|configurações|Yes|Métricas de configuração|de palavras|Total|Métricas para Operações de Configuração|Sem Dimensões|
|connectedDeviceCount|Yes|Dispositivos ligados|de palavras|Média|Número de dispositivos ligados ao seu hub IoT|Sem Dimensões|
|d2c.endpoints.egress.builtIn.events|Yes|Encaminhamento: mensagens entregues a mensagens/eventos|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso ao ponto final incorporado (mensagens/eventos).|Sem Dimensões|
|d2c.endpoints.egress.eventHubs|Yes|Encaminhamento: mensagens entregues no Centro de Eventos|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso aos pontos finais do Event Hub.|Sem Dimensões|
|d2c.endpoints.egress.serviceBusQueues|Yes|Encaminhamento: mensagens entregues na Fila de Autocarros de Serviço|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso aos pontos finais da fila do Service Bus.|Sem Dimensões|
|d2c.endpoints.egress.serviceBusTopics|Yes|Encaminhamento: mensagens entregues no Service Bus Topic|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso aos pontos finais do tópico do Service Bus.|Sem Dimensões|
|d2c.endpoints.egress.storage|Yes|Encaminhamento: mensagens entregues no armazenamento|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso aos pontos finais de armazenamento.|Sem Dimensões|
|d2c.endpoints.egress.storage.blobs|Yes|Encaminhamento: bolhas entregues ao armazenamento|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub entregou bolhas nos pontos finais de armazenamento.|Sem Dimensões|
|d2c.endpoints.egress.storage.bytes|Yes|Encaminhamento: dados entregues ao armazenamento|Bytes|Total|A quantidade de dados (bytes) IoT Hub de encaminhamento entregue nos pontos finais de armazenamento.|Sem Dimensões|
|d2c.endpoints.latncy.builtIn.events|Yes|Encaminhamento: latência da mensagem para mensagens/eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada da mensagem de telemetria no ponto final incorporado (mensagens/eventos).|Sem Dimensões|
|d2c.endpoints.latncy.eventHubs|Yes|Encaminhamento: latência da mensagem para o Centro de Eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para ioT Hub e a entrada de mensagens num ponto final do Event Hub.|Sem Dimensões|
|d2c.endpoints.latncy.serviceBusQueues|Yes|Encaminhamento: latência da mensagem para a fila do autocarro de serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada de mensagens de telemetria num ponto final de fila de autocarros de serviço.|Sem Dimensões|
|d2c.endpoints.latncy.serviceBusTopics|Yes|Encaminhamento: latência de mensagem para o tópico do autocarro de serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada de mensagens de telemetria num ponto final de ônibus de serviço.|Sem Dimensões|
|d2c.endpoints.latncy.storage|Yes|Encaminhamento: latência da mensagem para armazenamento|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada da mensagem de telemetria num ponto final de armazenamento.|Sem Dimensões|
|d2c.telemetria.egress.drop|Yes|Encaminhamento: mensagens de telemetria caídas |de palavras|Total|O número de vezes que as mensagens foram deixadas pelo itinerário do IoT Hub devido a pontos sem saída. Este valor não conta as mensagens entregues para a rota de retorno, uma vez que as mensagens deixadas não são entregues lá.|Sem Dimensões|
|d2c.telemetria.egress.fallback|Yes|Encaminhamento: mensagens entregues para recuo|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens ao ponto final associado à rota de retorno.|Sem Dimensões|
|d2c.telemetria.egress.inválido|Yes|Encaminhamento: mensagens de telemetria incompatíveis|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub não conseguiu entregar mensagens devido a uma incompatibilidade com o ponto final. Este valor não inclui retrações.|Sem Dimensões|
|d2c.telemetria.egress.órfão|Yes|Encaminhamento: mensagens de telemetria órfãs |de palavras|Total|O número de vezes que as mensagens foram órfãs pelo encaminhamento do IoT Hub porque não correspondem a nenhuma regra de encaminhamento (incluindo a regra do recuo). |Sem Dimensões|
|d2c.telemetria.egress.sucesso|Yes|Encaminhamento: mensagens de telemetria entregues|de palavras|Total|O número de vezes que as mensagens foram entregues com sucesso em todos os pontos finais utilizando o encaminhamento IoT Hub. Se uma mensagem for encaminhada para vários pontos finais, este valor aumenta um para cada entrega bem sucedida. Se uma mensagem for entregue no mesmo ponto final várias vezes, este valor aumenta um para cada entrega bem sucedida.|Sem Dimensões|
|d2c.telemetria.ingress.allProtocol|Yes|Mensagem de telemetria envia tentativas|de palavras|Total|Número de mensagens de telemetria dispositivo-nuvem tentadas de ser enviadas para o seu hub IoT|Sem Dimensões|
|d2c.telemetria.ingress.sendThrottle|Yes|Número de erros de estrangulamento|de palavras|Total|Número de erros de estrangulamento devido aos aceleradores de produção do dispositivo|Sem Dimensões|
|d2c.telemetria.ingress.success|Yes|Mensagens de telemetria enviadas|de palavras|Total|Número de mensagens de telemetria dispositivo-nuvem enviadas com sucesso para o seu hub IoT|Sem Dimensões|
|d2c.twin.read.failure|Yes|Leituras gémeas falhadas dos dispositivos|de palavras|Total|A contagem de todas as leituras gémeas iniciadas pelo dispositivo falhados.|Sem Dimensões|
|d2c.twin.read.size|Yes|Tamanho de resposta de leituras gémeas de dispositivos|Bytes|Média|A média, min e max de todas as leituras gémeas iniciadas pelo dispositivo com sucesso.|Sem Dimensões|
|d2c.twin.read.success|Yes|Leituras gémeas bem sucedidas de dispositivos|de palavras|Total|A contagem de todas as leituras gémeas iniciadas pelo dispositivo.|Sem Dimensões|
|d2c.twin.update.failure|Yes|Falhas em duas atualizações a partir de dispositivos|de palavras|Total|A contagem de todas as atualizações gémeas iniciadas pelo dispositivo.|Sem Dimensões|
|d2c.twin.update.size|Yes|Tamanho das atualizações gémeas dos dispositivos|Bytes|Média|O tamanho médio, min e máximo de todas as atualizações gémeas iniciadas pelo dispositivo com sucesso.|Sem Dimensões|
|d2c.twin.update.success|Yes|Atualizações gémeas bem sucedidas dos dispositivos|de palavras|Total|A contagem de todas as atualizações gémeas iniciadas pelo dispositivo com sucesso.|Sem Dimensões|
|dailyMessageQuotaUsed|Yes|Número total de mensagens utilizadas|de palavras|Máximo|Número de mensagens totais usadas hoje|Sem Dimensões|
|dispositivoDataUsage|Yes|Total da utilização de dados do dispositivo|Bytes|Total|Bytes transferidos de e para qualquer dispositivo ligado ao IotHub|Sem Dimensões|
|dispositivoDataUsageV2|Yes|Total da utilização dos dados do dispositivo (pré-visualização)|Bytes|Total|Bytes transferidos de e para qualquer dispositivo ligado ao IotHub|Sem Dimensões|
|dispositivos.connectedDevices.allProtocol|Yes|Dispositivos ligados (precotados) |de palavras|Total|Número de dispositivos ligados ao seu hub IoT|Sem Dimensões|
|dispositivos.totalDevices|Yes|Total de dispositivos (precotados)|de palavras|Total|Número de dispositivos registados no seu hub IoT|Sem Dimensões|
|EventGridDeliveries|Yes|Entregas de Grelha de Eventos|de palavras|Total|O número de eventos IoT Hub publicados para a Event Grid. Utilize a dimensão Resultado para o número de pedidos bem sucedidos e falhados. A dimensão EventType mostra o tipo de evento https://aka.ms/ioteventgrid) (.|Resultado, EventType|
|EventGridLatency|Yes|Latência da grelha de eventos|Milissegundos|Média|A latência média (milissegundos) de quando o evento Iot Hub foi gerado para quando o evento foi publicado para a Event Grid. Este número é uma média entre todos os tipos de eventos. Utilize a dimensão EventType para ver a latência de um tipo específico de evento.|EventType|
|jobs.cancelJob.failure|Yes|Cancelamentos de empregos falhados|de palavras|Total|A contagem de todas as chamadas falhadas para cancelar um emprego.|Sem Dimensões|
|jobs.cancelJob.sucesso|Yes|Cancelamentos de emprego bem-sucedidos|de palavras|Total|A contagem de todas as chamadas bem sucedidas para cancelar um emprego.|Sem Dimensões|
|empregos.concluídos|Yes|Trabalhos concluídos|de palavras|Total|A contagem de todos os trabalhos concluídos.|Sem Dimensões|
|jobs.createDirectMethodJob.fail|Yes|Criações falhadas de trabalhos de invocação de métodos|de palavras|Total|A contagem de todos os trabalhos falhados de invocação de métodos diretos.|Sem Dimensões|
|jobs.createDirectMethodJob.success|Yes|Criações bem sucedidas de trabalhos de invocação de métodos|de palavras|Total|A contagem de todos os trabalhos bem sucedidos de criação de trabalhos de invocação de métodos diretos.|Sem Dimensões|
|jobs.createTwinUpdateJob.failure|Yes|Criações falhadas de trabalhos de atualização dupla|de palavras|Total|A contagem de todos os trabalhos falhados de atualização dupla.|Sem Dimensões|
|jobs.createTwinUpdateJob.success|Yes|Criações bem sucedidas de trabalhos de atualização dupla|de palavras|Total|A contagem de todos os trabalhos bem sucedidos de criação de trabalhos de atualização dupla.|Sem Dimensões|
|empregos.falhou|Yes|Empregos falhados|de palavras|Total|A contagem de todos os trabalhos falhados.|Sem Dimensões|
|empregos.listJobs.fracasso|Yes|Chamadas falhadas para listar empregos|de palavras|Total|A contagem de todas as chamadas falhadas para listar empregos.|Sem Dimensões|
|jobs.listJobs.sucesso|Yes|Chamadas bem-sucedidas para listar empregos|de palavras|Total|A contagem de todas as chamadas bem sucedidas para listar empregos.|Sem Dimensões|
|jobs.consultaJobs.falha|Yes|Consultas de trabalho falhadas|de palavras|Total|A contagem de todas as chamadas falhadas para consultas.|Sem Dimensões|
|jobs.queryJobs.sucesso|Yes|Consultas de trabalho bem sucedidas|de palavras|Total|A contagem de todas as chamadas bem sucedidas para trabalhos de consulta.|Sem Dimensões|
|EncaminhamentoDataSizeInBytesDelivered|Yes|Tamanho da mensagem de entrega de encaminhamento em bytes (pré-visualização)|Bytes|Total|O tamanho total em bytes de mensagens entregues pelo hub IoT para um ponto final. Pode utilizar as dimensões EndpointName e EndpointType para visualizar o tamanho das mensagens nos bytes entregues nos seus diferentes pontos finais. O valor métrico aumenta para cada mensagem entregue, incluindo se a mensagem é entregue em vários pontos finais ou se a mensagem é entregue no mesmo ponto final várias vezes.|EndpointType, EndpointName, RoutingSource|
|RoteamentoDelivas|Yes|Entregas de encaminhamento (pré-visualização)|de palavras|Total|O número de vezes que o IoT Hub tentou entregar mensagens a todos os pontos finais utilizando o encaminhamento. Para ver o número de tentativas bem sucedidas ou falhadas, utilize a dimensão Resultado. Para ver a razão da falha, como inválido, caído ou órfão, use a dimensão FailReasonCategory. Também pode utilizar as dimensões EndpointName e EndpointType para entender quantas mensagens foram entregues nos seus diferentes pontos finais. O valor métrico aumenta por um para cada tentativa de entrega, incluindo se a mensagem é entregue em vários pontos finais ou se a mensagem é entregue no mesmo ponto final várias vezes.|EndpointType, EndpointName, FailureReasonCategory, Resultado, RoutingSource|
|EncaminhamentoDeliveryLatency|Yes|Data de entrega de encaminhamento (pré-visualização)|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada da mensagem de telemetria num ponto final. Pode utilizar as dimensões EndpointName e EndpointType para entender a latência dos seus diferentes pontos finais.|EndpointType, EndpointName, RoutingSource|
|tenantHub.requestedUsageRate|Yes|taxa de utilização solicitada|Percentagem|Média|taxa de utilização solicitada|Sem Dimensões|
|totalDeviceCount|Yes|Total de dispositivos|de palavras|Média|Número de dispositivos registados no seu hub IoT|Sem Dimensões|
|twinQueries.falha|Yes|Consultas gémeas falhadas|de palavras|Total|A contagem de todas as consultas gémeas falhadas.|Sem Dimensões|
|twinQueries.resultSize|Yes|Tamanho do resultado de consultas gémeas|Bytes|Média|A média, min e máximo do tamanho do resultado de todas as consultas gémeas bem sucedidas.|Sem Dimensões|
|twinQueries.sucesso|Yes|Consultas gémeas bem sucedidas|de palavras|Total|A contagem de todas as consultas gémeas bem sucedidas.|Sem Dimensões|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|Yes|Mensagens C2D abandonadas|de palavras|Total|Número de mensagens cloud-to-device abandonadas pelo dispositivo|Sem Dimensões|
|c2d.commands.egress.complete.success|Yes|Entregas de mensagens C2D concluídas|de palavras|Total|Número de entregas de mensagens nuvem-para-dispositivo concluídas com sucesso pelo dispositivo|Sem Dimensões|
|c2d.commands.egress.reject.success|Yes|Mensagens C2D rejeitadas|de palavras|Total|Número de mensagens nuvem-dispositivo rejeitadas pelo dispositivo|Sem Dimensões|
|c2d.methods.falha|Yes|Invocações de método direto falhadas|de palavras|Total|A contagem de todas as chamadas de métodos diretos falhadas.|Sem Dimensões|
|c2d.methods.requestSize|Yes|Tamanho do pedido de invocações de método direto|Bytes|Média|A média, min e máxima de todos os pedidos de método direto bem sucedidos.|Sem Dimensões|
|c2d.methods.responseSize|Yes|Tamanho da resposta das invocações do método direto|Bytes|Média|A média, min e máxima de todas as respostas de métodos diretos bem sucedidas.|Sem Dimensões|
|c2d.methods.success|Yes|Invocações de métodos diretos bem-sucedidas|de palavras|Total|A contagem de todas as chamadas de métodos diretos bem sucedidos.|Sem Dimensões|
|c2d.twin.read.failure|Yes|Gémeo falhado lê do fundo|de palavras|Total|A contagem de todas as leituras gémeas falhadas.|Sem Dimensões|
|c2d.twin.read.size|Yes|Tamanho de resposta de leituras gémeas da parte de trás|Bytes|Média|A média, min, e máx.|Sem Dimensões|
|c2d.twin.read.success|Yes|Gémea bem sucedida lê a partir da parte de trás|de palavras|Total|A contagem de todas as leituras gémeas iniciadas com sucesso.|Sem Dimensões|
|c2d.twin.update.failure|Yes|Falhas em duas atualizações a partir do final|de palavras|Total|A contagem de todas as atualizações gémeas iniciadas pela parte de trás.|Sem Dimensões|
|c2d.twin.update.size|Yes|Tamanho das atualizações gémeas a partir da parte de trás|Bytes|Média|O tamanho médio, min e máximo de todas as atualizações gémeas iniciadas com sucesso.|Sem Dimensões|
|c2d.twin.update.success|Yes|Atualizações gémeas bem sucedidas a partir do final|de palavras|Total|A contagem de todas as atualizações gémeas iniciadas com sucesso.|Sem Dimensões|
|C2DMessagesExpired|Yes|Mensagens C2D expiradas|de palavras|Total|Número de mensagens de nuvem-para-dispositivo expiradas|Sem Dimensões|
|configurações|Yes|Métricas de configuração|de palavras|Total|Métricas para Operações de Configuração|Sem Dimensões|
|connectedDeviceCount|No|Dispositivos ligados|de palavras|Média|Número de dispositivos ligados ao seu hub IoT|Sem Dimensões|
|d2c.endpoints.egress.builtIn.events|Yes|Encaminhamento: mensagens entregues a mensagens/eventos|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso ao ponto final incorporado (mensagens/eventos).|Sem Dimensões|
|d2c.endpoints.egress.eventHubs|Yes|Encaminhamento: mensagens entregues no Centro de Eventos|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso aos pontos finais do Event Hub.|Sem Dimensões|
|d2c.endpoints.egress.serviceBusQueues|Yes|Encaminhamento: mensagens entregues na Fila de Autocarros de Serviço|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso aos pontos finais da fila do Service Bus.|Sem Dimensões|
|d2c.endpoints.egress.serviceBusTopics|Yes|Encaminhamento: mensagens entregues no Service Bus Topic|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso aos pontos finais do tópico do Service Bus.|Sem Dimensões|
|d2c.endpoints.egress.storage|Yes|Encaminhamento: mensagens entregues no armazenamento|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso aos pontos finais de armazenamento.|Sem Dimensões|
|d2c.endpoints.egress.storage.blobs|Yes|Encaminhamento: bolhas entregues ao armazenamento|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub entregou bolhas nos pontos finais de armazenamento.|Sem Dimensões|
|d2c.endpoints.egress.storage.bytes|Yes|Encaminhamento: dados entregues ao armazenamento|Bytes|Total|A quantidade de dados (bytes) IoT Hub de encaminhamento entregue nos pontos finais de armazenamento.|Sem Dimensões|
|d2c.endpoints.latncy.builtIn.events|Yes|Encaminhamento: latência da mensagem para mensagens/eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada da mensagem de telemetria no ponto final incorporado (mensagens/eventos).|Sem Dimensões|
|d2c.endpoints.latncy.eventHubs|Yes|Encaminhamento: latência da mensagem para o Centro de Eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para ioT Hub e a entrada de mensagens num ponto final do Event Hub.|Sem Dimensões|
|d2c.endpoints.latncy.serviceBusQueues|Yes|Encaminhamento: latência da mensagem para a fila do autocarro de serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada de mensagens de telemetria num ponto final de fila de autocarros de serviço.|Sem Dimensões|
|d2c.endpoints.latncy.serviceBusTopics|Yes|Encaminhamento: latência de mensagem para o tópico do autocarro de serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada de mensagens de telemetria num ponto final de ônibus de serviço.|Sem Dimensões|
|d2c.endpoints.latncy.storage|Yes|Encaminhamento: latência da mensagem para armazenamento|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada da mensagem de telemetria num ponto final de armazenamento.|Sem Dimensões|
|d2c.telemetria.egress.drop|Yes|Encaminhamento: mensagens de telemetria caídas |de palavras|Total|O número de vezes que as mensagens foram deixadas pelo itinerário do IoT Hub devido a pontos sem saída. Este valor não conta as mensagens entregues para a rota de retorno, uma vez que as mensagens deixadas não são entregues lá.|Sem Dimensões|
|d2c.telemetria.egress.fallback|Yes|Encaminhamento: mensagens entregues para recuo|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens ao ponto final associado à rota de retorno.|Sem Dimensões|
|d2c.telemetria.egress.inválido|Yes|Encaminhamento: mensagens de telemetria incompatíveis|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub não conseguiu entregar mensagens devido a uma incompatibilidade com o ponto final. Este valor não inclui retrações.|Sem Dimensões|
|d2c.telemetria.egress.órfão|Yes|Encaminhamento: mensagens de telemetria órfãs |de palavras|Total|O número de vezes que as mensagens foram órfãs pelo encaminhamento do IoT Hub porque não correspondem a nenhuma regra de encaminhamento (incluindo a regra do recuo). |Sem Dimensões|
|d2c.telemetria.egress.sucesso|Yes|Encaminhamento: mensagens de telemetria entregues|de palavras|Total|O número de vezes que as mensagens foram entregues com sucesso em todos os pontos finais utilizando o encaminhamento IoT Hub. Se uma mensagem for encaminhada para vários pontos finais, este valor aumenta um para cada entrega bem sucedida. Se uma mensagem for entregue no mesmo ponto final várias vezes, este valor aumenta um para cada entrega bem sucedida.|Sem Dimensões|
|d2c.telemetria.ingress.allProtocol|Yes|Mensagem de telemetria envia tentativas|de palavras|Total|Número de mensagens de telemetria dispositivo-nuvem tentadas de ser enviadas para o seu hub IoT|Sem Dimensões|
|d2c.telemetria.ingress.sendThrottle|Yes|Número de erros de estrangulamento|de palavras|Total|Número de erros de estrangulamento devido aos aceleradores de produção do dispositivo|Sem Dimensões|
|d2c.telemetria.ingress.success|Yes|Mensagens de telemetria enviadas|de palavras|Total|Número de mensagens de telemetria dispositivo-nuvem enviadas com sucesso para o seu hub IoT|Sem Dimensões|
|d2c.twin.read.failure|Yes|Leituras gémeas falhadas dos dispositivos|de palavras|Total|A contagem de todas as leituras gémeas iniciadas pelo dispositivo falhados.|Sem Dimensões|
|d2c.twin.read.size|Yes|Tamanho de resposta de leituras gémeas de dispositivos|Bytes|Média|A média, min e max de todas as leituras gémeas iniciadas pelo dispositivo com sucesso.|Sem Dimensões|
|d2c.twin.read.success|Yes|Leituras gémeas bem sucedidas de dispositivos|de palavras|Total|A contagem de todas as leituras gémeas iniciadas pelo dispositivo.|Sem Dimensões|
|d2c.twin.update.failure|Yes|Falhas em duas atualizações a partir de dispositivos|de palavras|Total|A contagem de todas as atualizações gémeas iniciadas pelo dispositivo.|Sem Dimensões|
|d2c.twin.update.size|Yes|Tamanho das atualizações gémeas dos dispositivos|Bytes|Média|O tamanho médio, min e máximo de todas as atualizações gémeas iniciadas pelo dispositivo com sucesso.|Sem Dimensões|
|d2c.twin.update.success|Yes|Atualizações gémeas bem sucedidas dos dispositivos|de palavras|Total|A contagem de todas as atualizações gémeas iniciadas pelo dispositivo com sucesso.|Sem Dimensões|
|dailyMessageQuotaUsed|Yes|Número total de mensagens utilizadas|de palavras|Máximo|Número de mensagens totais usadas hoje|Sem Dimensões|
|dispositivoDataUsage|Yes|Total da utilização de dados do dispositivo|Bytes|Total|Bytes transferidos de e para qualquer dispositivo ligado ao IotHub|Sem Dimensões|
|dispositivoDataUsageV2|Yes|Total da utilização dos dados do dispositivo (pré-visualização)|Bytes|Total|Bytes transferidos de e para qualquer dispositivo ligado ao IotHub|Sem Dimensões|
|dispositivos.connectedDevices.allProtocol|Yes|Dispositivos ligados (precotados) |de palavras|Total|Número de dispositivos ligados ao seu hub IoT|Sem Dimensões|
|dispositivos.totalDevices|Yes|Total de dispositivos (precotados)|de palavras|Total|Número de dispositivos registados no seu hub IoT|Sem Dimensões|
|EventGridDeliveries|Yes|Entregas de Grelha de Eventos|de palavras|Total|O número de eventos IoT Hub publicados para a Event Grid. Utilize a dimensão Resultado para o número de pedidos bem sucedidos e falhados. A dimensão EventType mostra o tipo de evento https://aka.ms/ioteventgrid) (.|Resultado, EventType|
|EventGridLatency|Yes|Latência da grelha de eventos|Milissegundos|Média|A latência média (milissegundos) de quando o evento Iot Hub foi gerado para quando o evento foi publicado para a Event Grid. Este número é uma média entre todos os tipos de eventos. Utilize a dimensão EventType para ver a latência de um tipo específico de evento.|EventType|
|jobs.cancelJob.failure|Yes|Cancelamentos de empregos falhados|de palavras|Total|A contagem de todas as chamadas falhadas para cancelar um emprego.|Sem Dimensões|
|jobs.cancelJob.sucesso|Yes|Cancelamentos de emprego bem-sucedidos|de palavras|Total|A contagem de todas as chamadas bem sucedidas para cancelar um emprego.|Sem Dimensões|
|empregos.concluídos|Yes|Trabalhos concluídos|de palavras|Total|A contagem de todos os trabalhos concluídos.|Sem Dimensões|
|jobs.createDirectMethodJob.fail|Yes|Criações falhadas de trabalhos de invocação de métodos|de palavras|Total|A contagem de todos os trabalhos falhados de invocação de métodos diretos.|Sem Dimensões|
|jobs.createDirectMethodJob.success|Yes|Criações bem sucedidas de trabalhos de invocação de métodos|de palavras|Total|A contagem de todos os trabalhos bem sucedidos de criação de trabalhos de invocação de métodos diretos.|Sem Dimensões|
|jobs.createTwinUpdateJob.failure|Yes|Criações falhadas de trabalhos de atualização dupla|de palavras|Total|A contagem de todos os trabalhos falhados de atualização dupla.|Sem Dimensões|
|jobs.createTwinUpdateJob.success|Yes|Criações bem sucedidas de trabalhos de atualização dupla|de palavras|Total|A contagem de todos os trabalhos bem sucedidos de criação de trabalhos de atualização dupla.|Sem Dimensões|
|empregos.falhou|Yes|Empregos falhados|de palavras|Total|A contagem de todos os trabalhos falhados.|Sem Dimensões|
|empregos.listJobs.fracasso|Yes|Chamadas falhadas para listar empregos|de palavras|Total|A contagem de todas as chamadas falhadas para listar empregos.|Sem Dimensões|
|jobs.listJobs.sucesso|Yes|Chamadas bem-sucedidas para listar empregos|de palavras|Total|A contagem de todas as chamadas bem sucedidas para listar empregos.|Sem Dimensões|
|jobs.consultaJobs.falha|Yes|Consultas de trabalho falhadas|de palavras|Total|A contagem de todas as chamadas falhadas para consultas.|Sem Dimensões|
|jobs.queryJobs.sucesso|Yes|Consultas de trabalho bem sucedidas|de palavras|Total|A contagem de todas as chamadas bem sucedidas para trabalhos de consulta.|Sem Dimensões|
|EncaminhamentoDataSizeInBytesDelivered|Yes|Tamanho da mensagem de entrega de encaminhamento em bytes (pré-visualização)|Bytes|Total|O tamanho total em bytes de mensagens entregues pelo hub IoT para um ponto final. Pode utilizar as dimensões EndpointName e EndpointType para visualizar o tamanho das mensagens nos bytes entregues nos seus diferentes pontos finais. O valor métrico aumenta para cada mensagem entregue, incluindo se a mensagem é entregue em vários pontos finais ou se a mensagem é entregue no mesmo ponto final várias vezes.|EndpointType, EndpointName, RoutingSource|
|RoteamentoDelivas|Yes|Entregas de encaminhamento (pré-visualização)|de palavras|Total|O número de vezes que o IoT Hub tentou entregar mensagens a todos os pontos finais utilizando o encaminhamento. Para ver o número de tentativas bem sucedidas ou falhadas, utilize a dimensão Resultado. Para ver a razão da falha, como inválido, caído ou órfão, use a dimensão FailReasonCategory. Também pode utilizar as dimensões EndpointName e EndpointType para entender quantas mensagens foram entregues nos seus diferentes pontos finais. O valor métrico aumenta por um para cada tentativa de entrega, incluindo se a mensagem é entregue em vários pontos finais ou se a mensagem é entregue no mesmo ponto final várias vezes.|EndpointType, EndpointName, FailureReasonCategory, Resultado, RoutingSource|
|EncaminhamentoDeliveryLatency|Yes|Data de entrega de encaminhamento (pré-visualização)|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada da mensagem de telemetria num ponto final. Pode utilizar as dimensões EndpointName e EndpointType para entender a latência dos seus diferentes pontos finais.|EndpointType, EndpointName, RoutingSource|
|totalDeviceCount|No|Total de dispositivos|de palavras|Média|Número de dispositivos registados no seu hub IoT|Sem Dimensões|
|twinQueries.falha|Yes|Consultas gémeas falhadas|de palavras|Total|A contagem de todas as consultas gémeas falhadas.|Sem Dimensões|
|twinQueries.resultSize|Yes|Tamanho do resultado de consultas gémeas|Bytes|Média|A média, min e máximo do tamanho do resultado de todas as consultas gémeas bem sucedidas.|Sem Dimensões|
|twinQueries.sucesso|Yes|Consultas gémeas bem sucedidas|de palavras|Total|A contagem de todas as consultas gémeas bem sucedidas.|Sem Dimensões|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AttestationAttempts|Yes|Tentativas de atestado|de palavras|Total|Número de atestesações de dispositivos tentados|ProvisioningServiceName, Status, Protocolo|
|Configurações de Dispositivos|Yes|Dispositivos atribuídos|de palavras|Total|Número de dispositivos atribuídos a um hub IoT|ProvisioningServiceName, IotHubName|
|InscriçõesAptas|Yes|Tentativas de registo|de palavras|Total|Número de registos de dispositivos tentados|ProvisioningServiceName, IotHubName, Status|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft.DigitalTwins/digitalTwinsInstances

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ApiRequests|Yes|Pedidos da API|de palavras|Total|O número de pedidos de API feitos para as Gémeas Digitais lêem, escrevem, apagam e consultam as operações.|Operação, Autenticação, Protocolo, Código de Estado, StatusCodeClass, StatusText|
|ApiRequestsFailureRate|Yes|Taxa de falha de pedidos da API|Percentagem|Média|A percentagem de pedidos da API que o serviço recebe, por exemplo, que devolva um código de resposta de erro interno (500) para as Gémeas Digitais ler, escrever, eliminar e consultar operações.|Operação, Autenticação, Protocolo|
|ApiRequestsLatency|Yes|API solicita latência|Milissegundos|Média|O tempo de resposta para pedidos de API, ou seja, a partir de quando o pedido é recebido pela Azure Digital Twins até que o serviço envie um resultado de sucesso/falha para as operações de leitura, escrita, exclusão e consulta das Gémeas Digitais.|Operação, Autenticação, Protocolo, Código de Estado, StatusCodeClass, StatusText|
|BillingApiOperações|Yes|Operações de Faturação da API|de palavras|Total|Métrica de faturação para a contagem de todos os pedidos da API feitos contra o serviço Azure Digital Twins.|MeterId|
|BillingMessagesProcessado|Yes|Mensagens de faturação processadas|de palavras|Total|Métrica de faturação para o número de mensagens enviadas da Azure Digital Twins para pontos finais externos.|MeterId|
|BillingQueryUnits|Yes|Unidades de consulta de faturação|de palavras|Total|O número de Unidades de Consulta, uma medida internamente calculada de utilização de recursos de serviço, consumido para executar consultas.|MeterId|
|IngresssEvents|Yes|Eventos ingressos|de palavras|Total|O número de eventos de telemetria a entrar em Azure Digital Twins.|Resultado|
|IngressEventsFailureRate|Yes|Taxa de falha de eventos ingress|Percentagem|Média|A percentagem de eventos de telemetria de entrada para os quais o serviço devolve um código de resposta de erro interno (500).|Sem Dimensões|
|IngresssEventsLatency|Yes|Ingresss Eventos Latência|Milissegundos|Média|O momento em que um evento chega até quando está pronto para ser evacuado pela Azure Digital Twins, altura em que o serviço envia um resultado de sucesso/falha.|Resultado|
|ModelCount|Yes|Contagem de Modelos|de palavras|Total|Número total de modelos na instância Azure Digital Twins. Utilize esta métrica para determinar se está a aproximar-se do limite de serviço para o número máximo de modelos permitidos por exemplo.|Sem Dimensões|
|Encaminhamento|Yes|Mensagens encaminhada|de palavras|Total|O número de mensagens encaminhada para um serviço Azure de ponto final, como Event Hub, Service Bus ou Event Grid.|EndpointType, Resultado|
|EncaminhamentoFailureRate|Yes|Taxa de falha de encaminhamento|Percentagem|Média|A percentagem de eventos que resultam num erro, uma vez que são encaminhados da Azure Digital Twins para um serviço Azure de ponta, como o Event Hub, o Service Bus ou o Event Grid.|Tipo de ponto final|
|Encaminhamento|Yes|Latência de encaminhamento|Milissegundos|Média|O tempo decorreu entre um evento que foi encaminhado da Azure Digital Twins para quando é colocado no serviço endpoint Azure, como Event Hub, Service Bus ou Event Grid.|EndpointType, Resultado|
|TwinCount|Yes|Contagem de Gémeos|de palavras|Total|Número total de gémeos no caso Azure Digital Twins. Utilize esta métrica para determinar se está a aproximar-se do limite de serviço para o número máximo de gémeos permitido por exemplo.|Sem Dimensões|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/base de dadosAcontas

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AddRegion|Yes|Região Adicionada|de palavras|de palavras|Região Adicionada|Region|
|AutoscaleMaxThroughput|No|Potência Max autoscale|de palavras|Máximo|Potência Max autoscale|Nome de dados, Nome de Coleção|
|DisponíveisToragem|No|(precotado) Armazenamento disponível|Bytes|Total|O "Armazenamento Disponível" será removido do Azure Monitor no final de setembro de 2023. O tamanho do armazenamento da coleta Da Cosmos DB é agora ilimitado. A única restrição é que o tamanho de armazenamento de cada chave de partição lógica é de 20GB. Pode ativar a PartitionKeyStatistics no Registo de Diagnóstico para saber o consumo de armazenamento para as teclas de partição superior. Para mais informações sobre a quota de armazenamento Da Cosmos DB, consulte este doc https://docs.microsoft.com/azure/cosmos-db/concepts-limits . Após a depreciação, as restantes regras de alerta ainda definidas na métrica depreciada serão automaticamente desativadas após a data de depreciação.|Nome de coleção, nome de base de dados, região|
|CassandraConnectionClosures|No|Encerramentos de conexão cassandra|de palavras|Total|Número de ligações cassandra que foram fechadas, reportadas a uma granularidade de 1 minuto|Região, EncerramentoReason|
|CassandraConnectorAvgReplicationLatency|No|Replicação média do conector Cassandra|MilliSeconds|Média|Replicação média do conector Cassandra|Sem Dimensões|
|CassandraConnectorReplicationHealthStatus|No|Estado de Saúde da Replicação do Conector Cassandra|de palavras|de palavras|Estado de Saúde da Replicação do Conector Cassandra|NotStarted, ReplicationInProgress, Error|
|CassandraKeyspaceCreate|No|Cassandra Keyspace Criado|de palavras|de palavras|Cassandra Keyspace Criado|Nome de recursos, |
|CassandraKeyspaceDelete|No|Cassandra Keyspace eliminada|de palavras|de palavras|Cassandra Keyspace eliminada|Nome de recursos, |
|CassandraKeyspaceThroughputUpdate|No|Cassandra Keyspace Throughput Atualizado|de palavras|de palavras|Cassandra Keyspace Throughput Atualizado|Nome de recursos, |
|CassandraKeyspaceUpdate|No|Cassandra Keyspace Atualizado|de palavras|de palavras|Cassandra Keyspace Atualizado|Nome de recursos, |
|CassandraRequestCharges|No|Cassandra Request Charges|de palavras|Total|RUs consumidos por pedidos de Cassandra feitos|Nome de dados, Nome de Recolha, Região, OperaçãoType,Tip de Recursos|
|CassandraRequests|No|Pedidos de Cassandra|de palavras|de palavras|Número de pedidos de Cassandra feitos|Nome de dados, Nome de Recolha, Região, OperaçãoType,Tip de Recursos, Código de Erro|
|CassandraTableCreate|No|Tabela Cassandra Criada|de palavras|de palavras|Tabela Cassandra Criada|Nome de recurso, ChildResourceName, |
|CassandraTableDelete|No|Tabela Cassandra Eliminada|de palavras|de palavras|Tabela Cassandra Eliminada|Nome de recurso, ChildResourceName, |
|CassandraTableThroughputUpdate|No|Produção de tabela Cassandra atualizada|de palavras|de palavras|Produção de tabela Cassandra atualizada|Nome de recurso, ChildResourceName, |
|CassandraTableUpdate|No|Tabela Cassandra Atualizada|de palavras|de palavras|Tabela Cassandra Atualizada|Nome de recurso, ChildResourceName, |
|Criar Contas|Yes|Conta Criada|de palavras|de palavras|Conta Criada|Sem Dimensões|
|DataUsage|No|Utilização de Dados|Bytes|Total|Total de utilização de dados reportados a 5 minutos de granularidade|Nome de coleção, nome de base de dados, região|
|ExcluirAconta|Yes|Conta Eliminada|de palavras|de palavras|Conta Eliminada|Sem Dimensões|
|Contagem de documentos|No|Contagem de documentos|de palavras|Total|Contagem total de documentos reportada em 5 minutos granularidade|Nome de coleção, nome de base de dados, região|
|DocumentQuota|No|Quota documental|Bytes|Total|Quota total de armazenamento reportada a 5 minutos de granularidade|Nome de coleção, nome de base de dados, região|
|GremlinDatabaseCreate|No|Gremlin Base de Dados Criada|de palavras|de palavras|Gremlin Base de Dados Criada|Nome de recursos, |
|GremlinDatabaseDelete|No|Gremlin Base de Dados Eliminada|de palavras|de palavras|Gremlin Base de Dados Eliminada|Nome de recursos, |
|GremlinDatabaseThroughputUpdate|No|Produção de base de dados gremlin atualizada|de palavras|de palavras|Produção de base de dados gremlin atualizada|Nome de recursos, |
|GremlinDatabaseUpdate|No|Base de Dados Gremlin Atualizada|de palavras|de palavras|Base de Dados Gremlin Atualizada|Nome de recursos, |
|GremlinGraphCreate|No|Gremlin Graph Criado|de palavras|de palavras|Gremlin Graph Criado|Nome de recurso, ChildResourceName, |
|GremlinGraphDelete|No|Gremlin Graph Eliminado|de palavras|de palavras|Gremlin Graph Eliminado|Nome de recurso, ChildResourceName, |
|GremlinGraphThroughputUpdate|No|Throughputo de gráfico gremlin atualizado|de palavras|de palavras|Throughputo de gráfico gremlin atualizado|Nome de recurso, ChildResourceName, |
|GremlinGraphUpdate|No|Gráfico de Gremlin Atualizado|de palavras|de palavras|Gráfico de Gremlin Atualizado|Nome de recurso, ChildResourceName, |
|IndexUsage|No|Utilização do Índice|Bytes|Total|Utilização total do índice reportada a 5 minutos de granularidade|Nome de coleção, nome de base de dados, região|
|Entradas Integradas DeSprovidos de Apoio|No|Entradas Integradas DeSprovidos de Apoio|Bytes|Média|Tamanho das entradas despejadas da cache integrada|CacheType, Região|
|IntegradoCacheHitRate|No|IntegradoCacheHitRate|Percentagem|Média|Taxa de certe de cache para caches integrados|CacheType, Região|
|IntegradoSaquesize|No|IntegradoSaquesize|Bytes|Média|Tamanho das caches integradas para pedidos de gateway dedicados|CacheType, Região|
|IntegradoCacheTTLExpirationCount|No|IntegradoCacheTTLExpirationCount|de palavras|Média|Número de entradas removidas da cache integrada devido à expiração da TTL|CacheType, Região|
|MetadataReques|No|Pedidos de Metadados|de palavras|de palavras|Contagem de pedidos de metadados. Cosmos DB mantém recolha de metadados do sistema para cada conta, que lhe permite enumerar coleções, bases de dados, etc, e suas configurações, gratuitamente.|Data de dados Nome, Nome de Recolha, Região, StatusCode, |
|MongoCollectionCreate|No|Coleção Mongo Criada|de palavras|de palavras|Coleção Mongo Criada|Nome de recurso, ChildResourceName, |
|MongoCollectionDelete|No|Coleção mongo eliminada|de palavras|de palavras|Coleção mongo eliminada|Nome de recurso, ChildResourceName, |
|MongoCollectionThroughputUpdate|No|Produção de coleção de Mongo atualizada|de palavras|de palavras|Produção de coleção de Mongo atualizada|Nome de recurso, ChildResourceName, |
|MongoCollectionUpdate|No|Coleção Mongo Atualizada|de palavras|de palavras|Coleção Mongo Atualizada|Nome de recurso, ChildResourceName, |
|MongoDatabaseDelete|No|Base de Dados de Mongo eliminada|de palavras|de palavras|Base de Dados de Mongo eliminada|Nome de recursos, |
|MongoDatabaseThroughputUpdate|No|Produção de base de dados de Mongo atualizada|de palavras|de palavras|Produção de base de dados de Mongo atualizada|Nome de recursos, |
|MongodbDatabaseCreate|No|Base de Dados de Mongo Criada|de palavras|de palavras|Base de Dados de Mongo Criada|Nome de recursos, |
|MongoDBDatabaseUpdate|No|Base de Dados de Mongo Atualizada|de palavras|de palavras|Base de Dados de Mongo Atualizada|Nome de recursos, |
|MongoRequestCharge|Yes|Taxa de pedido de Mongo|de palavras|Total|Unidades de pedido da Mongo Consumidas|Data de Dados Nome, Nome de Recolha, Região, Nome de Comando, ErrorCode, Status|
|MongoRequests|Yes|Pedidos de Mongo|de palavras|de palavras|Número de pedidos da Mongo Feitos|Data de Dados Nome, Nome de Recolha, Região, Nome de Comando, ErrorCode, Status|
|MongoRequestsCount|No|(precotado) Taxa de pedido de Mongo|CondePerSecond|Média|Mongo pede Conde por segundo|Nome de dados, Nome de Recolha, Região, Código de Erro|
|MongoRequestsDelete|No|(precotado) Tarifa de pedido de eliminação de Mongo|CondePerSecond|Média|Mongo Delete request por segundo|Nome de dados, Nome de Recolha, Região, Código de Erro|
|MongoRequestsInsert|No|(precotado) Taxa de pedido de inserção de Mongo|CondePerSecond|Média|Contagem de Inserção de Mongo por segundo|Nome de dados, Nome de Recolha, Região, Código de Erro|
|MongoRequestsQuery|No|(precotado) Taxa de pedido de consulta de Mongo|CondePerSecond|Média|Pedido de consulta de Mongo por segundo|Nome de dados, Nome de Recolha, Região, Código de Erro|
|MongoRequestsUpdate|No|(precotado) Taxa de pedido de atualização de Mongo|CondePerSecond|Média|Pedido de atualização de Mongo por segundo|Nome de dados, Nome de Recolha, Região, Código de Erro|
|Ruconsumption normalizado|No|Consumo de RU normalizado|Percentagem|Máximo|Percentagem de consumo max RU por minuto|Nome de coleção, Nome de Base de Dados, Região, PartitionKeyRangeId|
|ProvisionedThroughput|No|Débito Aprovisionado|de palavras|Máximo|Débito Aprovisionado|Nome de dados, Nome de Coleção|
|RegionFailover|Yes|Região falhou|de palavras|de palavras|Região falhou|Sem Dimensões|
|Remover a Região|Yes|Região removida|de palavras|de palavras|Região removida|Region|
|ReplicationLatency|Yes|Latência de replicação P99|MilliSeconds|Média|P99 Replicação Latência em todas as regiões de origem e alvo para conta geo-habilitada|SourceRegion, TargetRegion|
|ServerSideLatency|No|Latência do lado do servidor|MilliSeconds|Média|Latência do lado do servidor|Nome de dados, Nome de Recolha, Região, ConnectionMode, OperationType, PublicAPIType|
|ServiçoSAdisponibilidade|No|Disponibilidade de serviço|Percentagem|Média|Conta solicita disponibilidade a uma hora, dia ou mês de granularidade|Sem Dimensões|
|SqlContainerCreate|No|Recipiente sql criado|de palavras|de palavras|Recipiente sql criado|Nome de recurso, ChildResourceName, |
|SqlContainerDelete|No|Recipiente sql eliminado|de palavras|de palavras|Recipiente sql eliminado|Nome de recurso, ChildResourceName, |
|SqlContainerThroughputUpdate|No|Produção de recipiente sql atualizado|de palavras|de palavras|Produção de recipiente sql atualizado|Nome de recurso, ChildResourceName, |
|SqlContainerUpdate|No|Recipiente Sql Atualizado|de palavras|de palavras|Recipiente Sql Atualizado|Nome de recurso, ChildResourceName, |
|SqlDatabaseCreate|No|Base de Dados Sql Criada|de palavras|de palavras|Base de Dados Sql Criada|Nome de recursos, |
|SqlDatabaseDelete|No|Base de Dados Sql Eliminada|de palavras|de palavras|Base de Dados Sql Eliminada|Nome de recursos, |
|SqlDatabaseThroughputUpdate|No|Produção de base de dados sql atualizada|de palavras|de palavras|Produção de base de dados sql atualizada|Nome de recursos, |
|SqlDatabaseUpdate|No|Base de Dados Sql Atualizada|de palavras|de palavras|Base de Dados Sql Atualizada|Nome de recursos, |
|TableTableCreate|No|Tabela AzureTable Criada|de palavras|de palavras|Tabela AzureTable Criada|Nome de recursos, |
|TableTableDelete|No|Tabela AzureTable Eliminada|de palavras|de palavras|Tabela AzureTable Eliminada|Nome de recursos, |
|TableTableThroughputUpdate|No|Produção de tabela azuretable atualizado|de palavras|de palavras|Produção de tabela azuretable atualizado|Nome de recursos, |
|TableTableUpdate|No|Tabela AzureTable atualizada|de palavras|de palavras|Tabela AzureTable atualizada|Nome de recursos, |
|TotalRequests|Yes|Total de Pedidos|de palavras|de palavras|Número de pedidos feitos|Data de DadosName, Nome de Recolha, Região, Código de Estado, OperaçãoType, Estado|
|TotalRequestUnits|Yes|Total de Unidades de Pedido|de palavras|Total|Unidades de pedido consumidas|Data de DadosName, Nome de Recolha, Região, Código de Estado, OperaçãoType, Estado|
|UpdateAccountKeys|Yes|Chaves de conta atualizadas|de palavras|de palavras|Chaves de conta atualizadas|Teclas|
|UpdateAccountNetworkSettings|Yes|Definições de rede de conta atualizadas|de palavras|de palavras|Definições de rede de conta atualizadas|Sem Dimensões|
|UpdateAccountReplicationSettings|Yes|Definições de replicação de conta atualizadas|de palavras|de palavras|Definições de replicação de conta atualizadas|Sem Dimensões|
|AtualizaçãoDiagnosticsSettings|No|Definições de diagnóstico de conta atualizadas|de palavras|de palavras|Definições de diagnóstico de conta atualizadas|DiagnósticoSettingsName, Nome do Grupo de Recursos|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domínios

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Extra-Endação DeFilter Avançado|Yes|Avaliações avançadas de filtros|de palavras|Total|Total de filtros avançados avaliados em todas as subscrições de eventos para este tópico.|Tema, Nome de Subscrição de Eventos, Nome de Subsubscrição de Eventos|
|DeadLetteredCount|Yes|Eventos lettered mortos|de palavras|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|Tema, EventosUbscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|EntregaAttemptFailCount|No|Eventos falhados de entrega|de palavras|Total|Total de eventos não entregaram a esta subscrição do evento|Tema, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|EntregaSSuccessCount|Yes|Eventos Entregues|de palavras|Total|Total de eventos entregues a esta subscrição do evento|Tema, Nome de Subscrição de Eventos, Nome de Subsubscrição de Eventos|
|DestinationProcessingDurationInMs|No|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Tema, Nome de Subscrição de Eventos, Nome de Subsubscrição de Eventos|
|DropEventCount|Yes|Eventos abandonados|de palavras|Total|Total de eventos perdidos correspondentes a esta subscrição do evento|Tema, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Yes|Eventos Combinados|de palavras|Total|Total de eventos combinados com esta subscrição de evento|Tema, Nome de Subscrição de Eventos, Nome de Subsubscrição de Eventos|
|PublicarFailCount|Yes|Publicar Eventos Falhados|de palavras|Total|Total de eventos não publicou para este tópico|Tópico, ErrorType, ErrorType|
|PublicarSuccessCount|Yes|Eventos Publicados|de palavras|Total|Total de eventos publicados neste tópico|Tópico|
|Publicar Inimitrodências|Yes|Publicar Latência de Sucesso|Milissegundos|Total|Publique latência de sucesso em milissegundos|Sem Dimensões|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Eventos lettered mortos|de palavras|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason|
|EntregaAttemptFailCount|No|Eventos falhados de entrega|de palavras|Total|Total de eventos não entregaram a esta subscrição do evento|Erro, ErrorType|
|EntregaSSuccessCount|Yes|Eventos Entregues|de palavras|Total|Total de eventos entregues a esta subscrição do evento|Sem Dimensões|
|DestinationProcessingDurationInMs|No|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Sem Dimensões|
|DropEventCount|Yes|Eventos abandonados|de palavras|Total|Total de eventos perdidos correspondentes a esta subscrição do evento|GotaReason|
|MatchedEventCount|Yes|Eventos Combinados|de palavras|Total|Total de eventos combinados com esta subscrição de evento|Sem Dimensões|


## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|PublicarFailCount|Yes|Publicar Eventos Falhados|de palavras|Total|Total de eventos não publicou para este tópico|ErrorType, Error|
|PublicarSuccessCount|Yes|Eventos Publicados|de palavras|Total|Total de eventos publicados neste tópico|Sem Dimensões|
|Publicar Inimitrodências|Yes|Publicar Latência de Sucesso|Milissegundos|Total|Publique latência de sucesso em milissegundos|Sem Dimensões|
|InigualávelEventCount|Yes|Eventos incomparáveis|de palavras|Total|Total de eventos que não correspondem a nenhuma das subscrições do evento para este tópico|Sem Dimensões|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft.EventGrid/partnerNamespaces

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Eventos lettered mortos|de palavras|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason, Nome de EventosSubscrição|
|EntregaAttemptFailCount|No|Eventos falhados de entrega|de palavras|Total|Total de eventos não entregaram a esta subscrição do evento|Erro, ErrorType, EventSubscriptionName|
|EntregaSSuccessCount|Yes|Eventos Entregues|de palavras|Total|Total de eventos entregues a esta subscrição do evento|Nome de subscrição de eventos|
|DestinationProcessingDurationInMs|No|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Nome de subscrição de eventos|
|DropEventCount|Yes|Eventos abandonados|de palavras|Total|Total de eventos perdidos correspondentes a esta subscrição do evento|DropReason, Nome de EventosSubscription|
|MatchedEventCount|Yes|Eventos Combinados|de palavras|Total|Total de eventos combinados com esta subscrição de evento|Nome de subscrição de eventos|
|PublicarFailCount|Yes|Publicar Eventos Falhados|de palavras|Total|Total de eventos não publicou para este tópico|ErrorType, Error|
|PublicarSuccessCount|Yes|Eventos Publicados|de palavras|Total|Total de eventos publicados neste tópico|Sem Dimensões|
|Publicar Inimitrodências|Yes|Publicar Latência de Sucesso|Milissegundos|Total|Publique latência de sucesso em milissegundos|Sem Dimensões|
|InigualávelEventCount|Yes|Eventos incomparáveis|de palavras|Total|Total de eventos que não correspondem a nenhuma das subscrições do evento para este tópico|Sem Dimensões|


## <a name="microsofteventgridpartnertopics"></a>Microsoft.EventGrid/partnerTopics

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Extra-Endação DeFilter Avançado|Yes|Avaliações avançadas de filtros|de palavras|Total|Total de filtros avançados avaliados em todas as subscrições de eventos para este tópico.|Nome de subscrição de eventos|
|DeadLetteredCount|Yes|Eventos lettered mortos|de palavras|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason, Nome de EventosSubscrição|
|EntregaAttemptFailCount|No|Eventos falhados de entrega|de palavras|Total|Total de eventos não entregaram a esta subscrição do evento|Erro, ErrorType, EventSubscriptionName|
|EntregaSSuccessCount|Yes|Eventos Entregues|de palavras|Total|Total de eventos entregues a esta subscrição do evento|Nome de subscrição de eventos|
|DestinationProcessingDurationInMs|No|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Nome de subscrição de eventos|
|DropEventCount|Yes|Eventos abandonados|de palavras|Total|Total de eventos perdidos correspondentes a esta subscrição do evento|DropReason, Nome de EventosSubscription|
|MatchedEventCount|Yes|Eventos Combinados|de palavras|Total|Total de eventos combinados com esta subscrição de evento|Nome de subscrição de eventos|
|PublicarFailCount|Yes|Publicar Eventos Falhados|de palavras|Total|Total de eventos não publicou para este tópico|ErrorType, Error|
|PublicarSuccessCount|Yes|Eventos Publicados|de palavras|Total|Total de eventos publicados neste tópico|Sem Dimensões|
|InigualávelEventCount|Yes|Eventos incomparáveis|de palavras|Total|Total de eventos que não correspondem a nenhuma das subscrições do evento para este tópico|Sem Dimensões|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Extra-Endação DeFilter Avançado|Yes|Avaliações avançadas de filtros|de palavras|Total|Total de filtros avançados avaliados em todas as subscrições de eventos para este tópico.|Nome de subscrição de eventos|
|DeadLetteredCount|Yes|Eventos lettered mortos|de palavras|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason, Nome de EventosSubscrição|
|EntregaAttemptFailCount|No|Eventos falhados de entrega|de palavras|Total|Total de eventos não entregaram a esta subscrição do evento|Erro, ErrorType, EventSubscriptionName|
|EntregaSSuccessCount|Yes|Eventos Entregues|de palavras|Total|Total de eventos entregues a esta subscrição do evento|Nome de subscrição de eventos|
|DestinationProcessingDurationInMs|No|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Nome de subscrição de eventos|
|DropEventCount|Yes|Eventos abandonados|de palavras|Total|Total de eventos perdidos correspondentes a esta subscrição do evento|DropReason, Nome de EventosSubscription|
|MatchedEventCount|Yes|Eventos Combinados|de palavras|Total|Total de eventos combinados com esta subscrição de evento|Nome de subscrição de eventos|
|PublicarFailCount|Yes|Publicar Eventos Falhados|de palavras|Total|Total de eventos não publicou para este tópico|ErrorType, Error|
|PublicarSuccessCount|Yes|Eventos Publicados|de palavras|Total|Total de eventos publicados neste tópico|Sem Dimensões|
|Publicar Inimitrodências|Yes|Publicar Latência de Sucesso|Milissegundos|Total|Publique latência de sucesso em milissegundos|Sem Dimensões|
|InigualávelEventCount|Yes|Eventos incomparáveis|de palavras|Total|Total de eventos que não correspondem a nenhuma das subscrições do evento para este tópico|Sem Dimensões|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/tópicos

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Extra-Endação DeFilter Avançado|Yes|Avaliações avançadas de filtros|de palavras|Total|Total de filtros avançados avaliados em todas as subscrições de eventos para este tópico.|Nome de subscrição de eventos|
|DeadLetteredCount|Yes|Eventos lettered mortos|de palavras|Total|Total de eventos com letras mortas correspondentes a esta subscrição do evento|DeadLetterReason, Nome de EventosSubscrição|
|EntregaAttemptFailCount|No|Eventos falhados de entrega|de palavras|Total|Total de eventos não entregaram a esta subscrição do evento|Erro, ErrorType, EventSubscriptionName|
|EntregaSSuccessCount|Yes|Eventos Entregues|de palavras|Total|Total de eventos entregues a esta subscrição do evento|Nome de subscrição de eventos|
|DestinationProcessingDurationInMs|No|Duração do processamento do destino|Milissegundos|Média|Duração do processamento do destino em milissegundos|Nome de subscrição de eventos|
|DropEventCount|Yes|Eventos abandonados|de palavras|Total|Total de eventos perdidos correspondentes a esta subscrição do evento|DropReason, Nome de EventosSubscription|
|MatchedEventCount|Yes|Eventos Combinados|de palavras|Total|Total de eventos combinados com esta subscrição de evento|Nome de subscrição de eventos|
|PublicarFailCount|Yes|Publicar Eventos Falhados|de palavras|Total|Total de eventos não publicou para este tópico|ErrorType, Error|
|PublicarSuccessCount|Yes|Eventos Publicados|de palavras|Total|Total de eventos publicados neste tópico|Sem Dimensões|
|Publicar Inimitrodências|Yes|Publicar Latência de Sucesso|Milissegundos|Total|Publique latência de sucesso em milissegundos|Sem Dimensões|
|InigualávelEventCount|Yes|Eventos incomparáveis|de palavras|Total|Total de eventos que não correspondem a nenhuma das subscrições do evento para este tópico|Sem Dimensões|


## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|de palavras|Média|Total de conexões ativas para Microsoft.EventHub.||
|DisponívelMemory|No|Memória Disponível|Percentagem|Máximo|Memória disponível para o Cluster De Eventos hub em percentagem da memória total.|Função|
|CaptureBacklog|No|Capture Backlog.|de palavras|Total|Capture Backlog for Microsoft.EventHub.||
|CapturouBytes|No|Bytes capturados.|Bytes|Total|Bytes capturados para Microsoft.EventHub.||
|Caixas de Captura|No|Mensagens capturadas.|de palavras|Total|Mensagens capturadas para Microsoft.EventHub.||
|LigaçõesClosed|No|Ligações Fechadas.|de palavras|Média|Ligações fechadas para Microsoft.EventHub.||
|LigaçõesAsa|No|Ligações Abertas.|de palavras|Média|Ligações abertas para Microsoft.EventHub.||
|CPU|No|CPU|Percentagem|Máximo|Utilização do CPU para o Cluster de Hub de Eventos em percentagem|Função|
|IncomingBytes|Yes|Bytes Recebidos.|Bytes|Total|Incoming Bytes para Microsoft.EventHub.||
|Entradas|Yes|Mensagens Recebidas|de palavras|Total|Mensagens de entrada para Microsoft.EventHub.||
|IncomingRequests|Yes|Pedidos Recebidos|de palavras|Total|Pedidos de entrada para Microsoft.EventHub.||
|OutgoingBytes|Yes|Bytes de saída.|Bytes|Total|Bytes de saída para Microsoft.EventHub.||
|Despesas de Saída|Yes|Mensagens Enviadas|de palavras|Total|Mensagens de saída para o Microsoft.EventHub.||
|QuotaExceededErrors|No|Erros de Quota Excedida.|de palavras|Total|Quota Excedeu erros para Microsoft.EventHub.|OperaçãoResult|
|ServerErrors|No|Erros do Servidor.|de palavras|Total|Erros de servidor para Microsoft.EventHub.|OperaçãoResult|
|Tamanho|No|Tamanho|Bytes|Média|Tamanho de um EventHub em Bytes.|Função|
|Requess de sucesso|No|Pedidos Com Êxito|de palavras|Total|Pedidos de sucesso para Microsoft.EventHub.|OperaçãoResult|
|ThrottledRequests|No|Pedidos Limitados.|de palavras|Total|Pedidos de aceleração para o Microsoft.EventHub.|OperaçãoResult|
|UserErrors|No|Erros do Utilizador.|de palavras|Total|Erros do utilizador para o Microsoft.EventHub.|OperaçãoResult|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/espaços de nome

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|de palavras|Média|Total de conexões ativas para Microsoft.EventHub.||
|CaptureBacklog|No|Capture Backlog.|de palavras|Total|Capture Backlog for Microsoft.EventHub.|Nome de Entidade|
|CapturouBytes|No|Bytes capturados.|Bytes|Total|Bytes capturados para Microsoft.EventHub.|Nome de Entidade|
|Caixas de Captura|No|Mensagens capturadas.|de palavras|Total|Mensagens capturadas para Microsoft.EventHub.|Nome de Entidade|
|LigaçõesClosed|No|Ligações Fechadas.|de palavras|Média|Ligações fechadas para Microsoft.EventHub.|Nome de Entidade|
|LigaçõesAsa|No|Ligações Abertas.|de palavras|Média|Ligações abertas para Microsoft.EventHub.|Nome de Entidade|
|EHABL|Yes|Mensagens de atraso de arquivo (Deprecadas)|de palavras|Total|Mensagens de arquivo do Event Hub em atraso para um espaço de nome (Depreciado)||
|EHAMBS|Yes|Produção de mensagem de arquivo (Precotada)|Bytes|Total|Event Hub arquivou produção de mensagem num espaço de nome (Depreciado)||
|EHAMSGS|Yes|Mensagens de arquivo (Depreciadas)|de palavras|Total|Event Hub arquivou mensagens num espaço de nome (Depreciado)||
|EHINBYTES|Yes|Bytes de entrada (Deprecados)|Bytes|Total|Envio de mensagem do Event Hub para um espaço de nome (Depreciado)||
|EHINMBS|Yes|Bytes de entrada (obsoletos) (Deprecados)|Bytes|Total|Mensagem de entrada do Event Hub para um espaço de nome. Esta métrica está prevadida. Utilize em vez disso a métrica de entrada de bytes (Deprecada)||
|EFINMSGS|Yes|Mensagens recebidas (depreciadas)|de palavras|Total|Total de mensagens recebidas para um espaço de nome (Deprecado)||
|EHOUTBYTES|Yes|Bytes de saída (Deprecados)|Bytes|Total|Mensagem de saída do Event Hub para um espaço de nome (Depreciado)||
|EHOUTMBS|Yes|Bytes de saída (obsoletos) (Preprecados)|Bytes|Total|Mensagem de saída do Event Hub para um espaço de nome. Esta métrica está prevadida. Utilize em vez disso bytes outgoing (Deprecado)||
|EHOUTMSGS|Yes|Mensagens de saída (depreciadas)|de palavras|Total|Total de mensagens de saída para um espaço de nome (Deprecado)||
|FAILREQ|Yes|Pedidos Falhados (Deprecados)|de palavras|Total|Total de pedidos falhados para um espaço de nome (Deprecado)||
|IncomingBytes|Yes|Bytes Recebidos.|Bytes|Total|Incoming Bytes para Microsoft.EventHub.|Nome de Entidade|
|Entradas|Yes|Mensagens Recebidas|de palavras|Total|Mensagens de entrada para Microsoft.EventHub.|Nome de Entidade|
|IncomingRequests|Yes|Pedidos Recebidos|de palavras|Total|Pedidos de entrada para Microsoft.EventHub.|Nome de Entidade|
|INMSGS|Yes|Mensagens recebidas (obsoletas) (Depreciadas)|de palavras|Total|Total de mensagens recebidas para um espaço de nome. Esta métrica está prevadida. Utilize a métrica de Mensagens recebidas (Deprecadas)||
|INREQS|Yes|Pedidos de Entrada (Deprecados)|de palavras|Total|Total de pedidos de envio para um espaço de nome (Deprecado)||
|INTERR|Yes|Erros internos do servidor (preprecados)|de palavras|Total|Erros internos totais do servidor para um espaço de nome (Deprecado)||
|MISCERR|Yes|Outros Erros (Precotados)|de palavras|Total|Total de pedidos falhados para um espaço de nome (Deprecado)||
|OutgoingBytes|Yes|Bytes de saída.|Bytes|Total|Bytes de saída para Microsoft.EventHub.|Nome de Entidade|
|Despesas de Saída|Yes|Mensagens Enviadas|de palavras|Total|Mensagens de saída para o Microsoft.EventHub.|Nome de Entidade|
|OUTMSGS|Yes|Mensagens de saída (obsoletas) (Depreciadas)|de palavras|Total|Mensagens de saída total para um espaço de nome. Esta métrica está prevadida. Utilize em vez disso a métrica mensagens de saída (deprecadas)||
|QuotaExceededErrors|No|Erros de Quota Excedida.|de palavras|Total|Quota Excedeu erros para Microsoft.EventHub.|Nome de Entidade, OperaçãoResult|
|ServerErrors|No|Erros do Servidor.|de palavras|Total|Erros de servidor para Microsoft.EventHub.|Nome de Entidade, OperaçãoResult|
|Tamanho|No|Tamanho|Bytes|Média|Tamanho de um EventHub em Bytes.|Nome de Entidade|
|Requess de sucesso|No|Pedidos Com Êxito|de palavras|Total|Pedidos de sucesso para Microsoft.EventHub.|Nome de Entidade, OperaçãoResult|
|SUCCREQ|Yes|Pedidos de sucesso (Deprecados)|de palavras|Total|Total de pedidos bem sucedidos para um espaço de nome (Deprecado)||
|SVRBSY|Yes|Erros ocupados do servidor (preprecados)|de palavras|Total|Erros de ocupado total do servidor para um espaço de nome (Deprecado)||
|ThrottledRequests|No|Pedidos Limitados.|de palavras|Total|Pedidos de aceleração para o Microsoft.EventHub.|Nome de Entidade, OperaçãoResult|
|UserErrors|No|Erros do Utilizador.|de palavras|Total|Erros do utilizador para o Microsoft.EventHub.|Nome de Entidade, OperaçãoResult|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|CategorizadosGatewayRequests|Yes|Pedidos de Gateway categorizados|de palavras|Total|Número de pedidos de gateway por categorias (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|GatewayRequests|Yes|Pedidos de Gateway|de palavras|Total|Número de pedidos de gateway|HttpStatus|
|KafkaRestProxy.ConsumerRequest.m1_delta|Yes|REST proxy Consumer RequestThroughput|CondePerSecond|Total|Número de pedidos de consumidores à procuração de Kafka REST|Máquina, Tópico|
|KafkaRestProxy.ConsumerRequestTime.p95|Yes|REST proxy Consumer RequestLatency|Milissegundos|Média|Mensagem Latência em um pedido de consumo através de procuração kafka REST|Máquina, Tópico|
|KafkaRestProxy.MessagesIn.m1_delta|Yes|REST Proxy Produtor MessageThroughput|CondePerSecond|Total|Número de mensagens de produtor através do representante kafka REST|Máquina, Tópico|
|KafkaRestProxy.MessagesOut.m1_delta|Yes|REST proxy Consumer MessageThroughput|CondePerSecond|Total|Número de mensagens de consumo através do representante kafka REST|Máquina, Tópico|
|KafkaRestProxy.OpenConnections|Yes|REST proxy ConcurrentConnections|de palavras|Total|Número de ligações simultâneas através do representante kafka REST|Máquina, Tópico|
|KafkaRestProxy.ProducerRequest.m1_delta|Yes|REST Proxy Produtor RequestThroughput|CondePerSecond|Total|Número de pedidos de produtor à procuração de Kafka REST|Máquina, Tópico|
|KafkaRestProxy.ProducerRequestTime.p95|Yes|Ressacamante do Produtor DeSAquirência|Milissegundos|Média|Mensagem Latência em um pedido de produtor através de procuração kafka REST|Máquina, Tópico|
|NumActiveWorkers|Yes|Número de trabalhadores ativos|de palavras|Máximo|Número de trabalhadores ativos|MetricName|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/serviços

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Percentagem|Média|A taxa de disponibilidade do serviço.|Sem Dimensões|
|CosmosDbCollectionSize|Yes|Tamanho da coleção Cosmos DB|Bytes|Total|O tamanho da coleção de apoio cosmos DB, em bytes.|Sem Dimensões|
|CosmosDbIndexize|Yes|Tamanho do índice de DB do cosmos|Bytes|Total|O tamanho do índice da coleção de cosmínios do Cosmos, em bytes.|Sem Dimensões|
|CosmosDbRequestCharge|Yes|Utilização do Cosmos DB RU|de palavras|Total|O uso da RU de pedidos para o apoio do serviço Cosmos DB.|Operação,Tip de Recursos|
|CosmosDbRequests|Yes|Pedidos de Serviço Cosmos DB|de palavras|Soma|O número total de pedidos feitos ao serviço de apoio cosmos DB.|Operação,Tip de Recursos|
|CosmosDbThrottleRate|Yes|Taxa de aceleração DB do Cosmos de serviço|de palavras|Soma|O número total de 429 respostas do apoio de um serviço cosmos DB.|Operação,Tip de Recursos|
|IoTConnectorDeviceEvent|Yes|Número de Mensagens recebidas|de palavras|Soma|O número total de mensagens recebidas pelo Conector Azure IoT para FHIR antes de qualquer normalização.|Operação, Nome do Conector|
|IoTConnectorDeviceEventProcessingLatencyMs|Yes|Latência média do estágio de normalização|Milissegundos|Média|O tempo médio entre o tempo de ingestão de um evento e o tempo de fase de eventos é processado para normalização.|Operação, Nome do Conector|
|IoTConnectorMeasurement|Yes|Número de medições|de palavras|Soma|O número de leituras de valor normalizadas recebidas pela fase de conversão FHIR do Conector Azure IoT para FHIR.|Operação, Nome do Conector|
|IoTConnectorMeasurementGroup|Yes|Número de grupos de mensagens|de palavras|Soma|O número total de agrupamentos únicos de medições em todo o tipo, dispositivo, paciente e período de tempo configurado gerado pela fase de conversão do FHIR.|Operação, Nome do Conector|
|IoTConnectorMeasurementIngestionLatencyMs|Yes|Latência média da fase de grupo|Milissegundos|Média|O período de tempo entre quando o Conector IoT recebeu os dados do dispositivo e quando os dados são tratados pela fase de conversão do FHIR.|Operação, Nome do Conector|
|IoTConnectorNormalizedEvent|Yes|Número de mensagens normalizadas|de palavras|Soma|O número total de valores normalizados mapeados a partir da fase de normalização do Conector Azure IoT para FHIR.|Operação, Nome do Conector|
|IoTConnectorTotalErrors|Yes|Contagem total de erros|de palavras|Soma|O número total de erros registados pelo Conector Azure IoT para FHIR|Nome, Operação, ErrorType, ErrorSeverity, ConnectorName|
|ServiceApiErrors|Yes|Erros de Serviço|de palavras|Soma|O número total de erros internos do servidor gerados pelo serviço.|Protocolo, Autenticação, Operação, Operação, Tipo de Recursos, Código de Estado, StatusCodeClass, StatusCodeText|
|ServiçoApilatency|Yes|Latência do Serviço|Milissegundos|Média|A latência de resposta do serviço.|Protocolo, Autenticação, Operação, Operação, Tipo de Recursos, Código de Estado, StatusCodeClass, StatusCodeText|
|ServiceApiRequests|Yes|Service Requests|de palavras|Soma|O número total de pedidos recebidos pelo serviço.|Protocolo, Autenticação, Operação, Operação, Tipo de Recursos, Código de Estado, StatusCodeClass, StatusCodeText|
|TotalErrors|Yes|Total de Erros|de palavras|Soma|O número total de erros internos do servidor encontrados pelo serviço.|Protocolo, Código de Estado, StatusCodeClass, StatusCodeText|
|TotalLatency|Yes|Latência total|Milissegundos|Média|A latência de resposta do serviço.|Protocolo|
|TotalRequests|Yes|Total de Pedidos|de palavras|Soma|O número total de pedidos recebidos pelo serviço.|Protocolo|


## <a name="microsofthybridnetworknetworkfunctions"></a>microsoft.hybridnetwork/networkfuntions

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|HiperVVirtualProcessorutilização|Yes|Utilização média do CPU|Percentagem|Média|Percentagem média total da utilização virtual do CPU num minuto de intervalo. O número total de CPU virtual baseia-se no valor configurado pelo utilizador na definição SKU. O filtro adicional pode ser aplicado com base no RoleName definido no SKU.|InstanceName|


## <a name="microsofthybridnetworkvirtualnetworkfunctions"></a>microsoft.hybridnetwork/virtualnetworkfunctions

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|HiperVVirtualProcessorutilização|Yes|Utilização média do CPU|Percentagem|Média|Percentagem média total da utilização virtual do CPU num minuto de intervalo. O número total de CPU virtual baseia-se no valor configurado pelo utilizador na definição SKU. O filtro adicional pode ser aplicado com base no RoleName definido no SKU.|InstanceName|


## <a name="microsoftinsightsautoscalesettings"></a>microsoft.insights/autoescalações

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|MétricaThreshold|Yes|Limiar métrico|de palavras|Média|O limiar de autoescala configurado quando a autoescalada foi executada.|MétricaTriggerrule|
|Capacidade Observada|Yes|Capacidade observada|de palavras|Média|A capacidade reportada para autoescalar quando executou.||
|ValorMétrico Observado|Yes|Valor métrico observado|de palavras|Média|O valor calculado por autoescala quando executado|MetricTriggerSource|
|ScaleActionsInitiado|Yes|Ações de escala iniciadas|de palavras|Total|A direção da operação de escala.|Direção de Escala|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Componentes

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|disponibilidadeResults/disponibilidadePercentage|Yes|Disponibilidade|Percentagem|Média|Percentagem de testes de disponibilidade concluídos com sucesso|disponibilidadeResult/nome, disponibilidadeResult/localização|
|disponibilidadeResults/contagem|No|Testes de disponibilidade|de palavras|de palavras|Contagem de testes de disponibilidade|disponibilidadeResult/nome, disponibilidadeResult/localização, disponibilidadeResult/sucesso|
|disponibilidadeResults/duração|Yes|Duração do teste de disponibilidade|MilliSeconds|Média|Duração do teste de disponibilidade|disponibilidadeResult/nome, disponibilidadeResult/localização, disponibilidadeResult/sucesso|
|browserEs/networkDuration|Yes|Tempo de ligação da rede de carga de página|MilliSeconds|Média|Tempo entre o pedido do utilizador e a ligação à rede. Inclui a procura de DNS e a ligação de transporte.|Sem Dimensões|
|browserEs/processamentoDuração|Yes|Tempo de processamento do cliente|MilliSeconds|Média|Tempo entre receber o último byte de um documento até que o DOM seja carregado. Os pedidos de async ainda podem estar a ser processados.|Sem Dimensões|
|browserEs/receberDuration|Yes|Tempo de resposta de receção|MilliSeconds|Média|Tempo entre o primeiro e o último bytes, ou até a desconexão.|Sem Dimensões|
|browserEs/sendDuration|Yes|Enviar tempo de pedido|MilliSeconds|Média|Tempo entre a ligação de rede e receber o primeiro byte.|Sem Dimensões|
|browserEs/totalDuration|Yes|Tempo de carregamento da página do navegador|MilliSeconds|Média|Tempo do pedido do utilizador até dom, folhas de estilo, scripts e imagens são carregados.|Sem Dimensões|
|dependências/contagem|No|Chamadas de dependência|de palavras|de palavras|Contagem de chamadas feitas pela aplicação a recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/sucesso, dependência/alvo, dependência/resultadoSCódigo, operação/sintético, nuvem/roleInstance, cloud/roleName|
|dependências/duração|Yes|Duração da dependência|MilliSeconds|Média|Duração das chamadas efetuadas pela aplicação a recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/sucesso, dependência/alvo, dependência/resultadoSCódigo, operação/sintético, nuvem/roleInstance, cloud/roleName|
|dependências/falhados|No|Falhas nas chamadas de dependência|de palavras|de palavras|Contagem de chamadas de dependência falhadas feitas pela aplicação a recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/alvo, dependência/resultadoSCódigo, operação/sintético, nuvem/roleInstance, cloud/roleName|
|exceções/navegador|No|Exceções de browser|de palavras|de palavras|Contagem de exceções não realizadas no navegador.|nuvem/funçãoName|
|exceções/contagem|Yes|Exceções|de palavras|de palavras|Contagem combinada de todas as exceções não conseguidas.|cloud/roleName, cloud/roleInstance, cliente/tipo|
|exceções/servidor|No|Exceções do servidor|de palavras|de palavras|Contagem de exceções não realizadas lançadas na aplicação do servidor.|cloud/roleName, cloud/roleInstance|
|pageVers/contagem|Yes|Vistas da página|de palavras|de palavras|Contagem de visualizações de página.|operação/sintético, nuvem/funName|
|páginaVerses/duração|Yes|Tempo de carga da visualização da página|MilliSeconds|Média|Tempo de carga da visualização da página|operação/sintético, nuvem/funName|
|performanceCounters/excepçõesPerSecond|Yes|Taxa de exceção|CondePerSecond|Média|Contagem de exceções manuseadas e não manipuladas reportadas às janelas, incluindo exceções .NET e exceções não geridas que são convertidas em exceções .NET.|nuvem/papelInstância|
|performanceCounters/memoryAvailableBytes|Yes|Memória disponível|Bytes|Média|Memória física imediatamente disponível para alocação a um processo ou para uso do sistema.|nuvem/papelInstância|
|performanceCounters/processCpuPercentage|Yes|Processo CPU|Percentagem|Média|A percentagem de tempo decorrido que todos os fios de processo utilizaram o processador para executar instruções. Isto pode variar entre 0 a 100. Esta métrica indica apenas o desempenho do processo w3wp.|nuvem/papelInstância|
|performanceCounters/processIOBytesPerSecond|Yes|Taxa IO do processo|BytesPerSecond|Média|Total de bytes por segundo lido e escrito para ficheiros, rede e dispositivos.|nuvem/papelInstância|
|performanceCounters/processadorEsPercentage|Yes|Tempo do processador|Percentagem|Média|A percentagem de tempo que o processador gasta em fios não ociosos.|nuvem/papelInstância|
|performanceCounters/processPrivateBytes|Yes|Processar bytes privados|Bytes|Média|Memória exclusivamente atribuída aos processos da aplicação monitorizada.|nuvem/papelInstância|
|performanceCounters/requestExecutionTime|Yes|HTTP solicitar tempo de execução|MilliSeconds|Média|Tempo de execução do mais recente pedido.|nuvem/papelInstância|
|performanceCounters/requestsInQueue|Yes|Pedidos HTTP na fila de candidaturas|de palavras|Média|Duração da fila de pedidos.|nuvem/papelInstância|
|performanceCounters/requestsPerSecond|Yes|Taxa de pedido HTTP|CondePerSecond|Média|Taxa de todos os pedidos para o pedido por segundo a partir de ASP.NET.|nuvem/papelInstância|
|pedidos/contagem|No|Pedidos de servidor|de palavras|de palavras|Contagem de pedidos HTTP concluídos.|pedido/performanceBucket, pedido/resultadoSCode, operação/sintético, nuvem/funInstance, pedido/sucesso, nuvem/funName|
|pedidos/duração|Yes|Tempo de resposta do servidor|MilliSeconds|Média|Tempo entre receber um pedido HTTP e terminar o envio da resposta.|pedido/performanceBucket, pedido/resultadoSCode, operação/sintético, nuvem/funInstance, pedido/sucesso, nuvem/funName|
|pedidos/falhados|No|Pedidos com falhas|de palavras|de palavras|Contagem de pedidos HTTP marcados como falhados. Na maioria dos casos, trata-se de pedidos com um código de resposta >= 400 e não igual a 401.|pedido/performanceBucket, pedido/resultadoSCode, operação/sintético, nuvem/roleInstance, cloud/roleName|
|pedidos/tarifa|No|Taxa de pedido do servidor|CondePerSecond|Média|Taxa de pedidos de servidor por segundo|pedido/performanceBucket, pedido/resultadoSCode, operação/sintético, nuvem/funInstance, pedido/sucesso, nuvem/funName|
|vestígios/contagem|Yes|Rastreios|de palavras|de palavras|Contagem de documentos de rastreio|traço/severidadeLevel, operação/sintético, nuvem/funName, nuvem/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|c2d.property.read.failure|Yes|Falha na propriedade do dispositivo lê-se da IoT Central|de palavras|Total|A contagem de todas as leituras de propriedade falhada iniciadas a partir da IoT Central|Sem Dimensões|
|c2d.property.read.success|Yes|Propriedade de dispositivo de sucesso lê da IoT Central|de palavras|Total|A contagem de todas as leituras bem sucedidas iniciadas da IoT Central|Sem Dimensões|
|c2d.property.update.failure|Yes|Atualizações de propriedade de dispositivos falhados da IoT Central|de palavras|Total|A contagem de todas as atualizações de propriedade falhadas iniciadas a partir da IoT Central|Sem Dimensões|
|c2d.property.update.success|Yes|Atualizações de propriedade de dispositivos bem-sucedidos da IoT Central|de palavras|Total|A contagem de todas as atualizações de propriedade bem sucedidas iniciadas a partir da IoT Central|Sem Dimensões|
|connectedDeviceCount|No|Total de dispositivos conectados|de palavras|Média|Número de dispositivos ligados à IoT Central|Sem Dimensões|
|d2c.property.read.failure|Yes|Falha na propriedade do dispositivo lê-se a partir de dispositivos|de palavras|Total|A contagem de todas as leituras de propriedade falhada iniciadas a partir de dispositivos|Sem Dimensões|
|d2c.property.read.success|Yes|Bem sucedida propriedade do dispositivo lê de dispositivos|de palavras|Total|A contagem de todas as leituras bem sucedidas iniciadas a partir de dispositivos|Sem Dimensões|
|d2c.property.update.failure|Yes|Falhas nas atualizações de propriedade do dispositivo a partir de dispositivos|de palavras|Total|A contagem de todas as atualizações de propriedade falhadas iniciadas a partir de dispositivos|Sem Dimensões|
|d2c.property.update.success|Yes|Atualizações de propriedade de dispositivos bem-sucedidos a partir de dispositivos|de palavras|Total|A contagem de todas as atualizações de propriedade bem sucedidas iniciadas a partir de dispositivos|Sem Dimensões|
|dataExport.error|Yes|Erros de Exportação de Dados|de palavras|Total|Número de erros encontrados para exportação de dados|exportid, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.messages.filtered|Yes|Mensagens de exportação de dados filtradas|de palavras|Total|Número de mensagens que passaram através de filtros na exportação de dados|exportid, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.messages.received|Yes|Mensagens de exportação de dados recebidas|de palavras|Total|Número de mensagens a chegar à exportação de dados, antes de filtrar e enriquecer o processamento|exportid, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.messages.written|Yes|Mensagens de exportação de dados escritas|de palavras|Total|Número de mensagens escritas para um destino|exportid, exportDisplayName, destinationId, destinationDisplayName|


## <a name="microsoftiotspacesgraph"></a>Microsoft.IoTSpaces/Gráfico

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Apilatency|No|Apilatency|6|0|Medidas de latência dos pedidos de API feitos à Microsoft.IoTSpaces em Milliseconds|Sem Dimensões|
|FunExecutionLatency|No|FunExecutionLatency|6|0|Medidas de latência da execução da função definida pelo utilizador em Milliseconds para Microsoft.IoTSpaces|Sem Dimensões|
|MensagemEgressFailure|No|MensagemEgressFailure|2|3|Procura uma cadeia localizada semelhante a measures Failed count event in Count for Microsoft.IoTSpaces|Sem Dimensões|
|MensagensSEgressLatency|No|MensagensSEgressLatency|6|0|Mede a latência do despachante para outros pontos finais em Milliseconds para Microsoft.IoTSpaces|Sem Dimensões|
|MensagemEgressuccess|No|MensagemEgressuccess|2|3|Procura uma cadeia localizada semelhante a medidas concluídas evento de contagem em Count for Microsoft.IoTSpaces|Sem Dimensões|
|Processamento de Restaurantes|No|Processamento de Restaurantes|6|0|Medidas de latência de mensagens ingeridas para evento despachado em Milliseconds para Microsoft.IoTSpaces|Sem Dimensões|


## <a name="microsoftkeyvaultmanagedhsms"></a>microsoft.keyvault/managedhsms

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|No|Disponibilidade geral do serviço|Percentagem|Média|Disponibilidade de pedidos de serviço|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiHit|Yes|Acessos Api de serviço total|de palavras|de palavras|Número de acessos totais de serviço api|ActivityType, ActivityName|
|ServiçoApilatency|No|Latência geral do serviço Api|Milissegundos|Média|Latência geral dos pedidos de serviço api|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Yes|Resultados totais do serviço Api|de palavras|de palavras|Número de resultados totais da API de serviço|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/cofres

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade geral do cofre|Percentagem|Média|Abóbada solicita disponibilidade|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturaçãoShoebox|No|Saturação geral do cofre|Percentagem|Média|Capacidade do cofre utilizada|ActivityType, ActivityName, TransactionType|
|ServiceApiHit|Yes|Acessos Api de serviço total|de palavras|de palavras|Número de acessos totais de serviço api|ActivityType, ActivityName|
|ServiçoApilatency|Yes|Latência geral do serviço Api|Milissegundos|Média|Latência geral dos pedidos de serviço api|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Yes|Resultados totais do serviço Api|de palavras|de palavras|Número de resultados totais da API de serviço|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkubernetesconnectedclusters"></a>microsoft.kubernetes/connectedClusters

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|capacity_cpu_cores|Yes|Número total de núcleos de cpu num cluster conectado|de palavras|Total|Número total de núcleos de cpu num cluster conectado||


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|LotBlobCount|Yes|Contagem de Blob de Lote|de palavras|Média|Número de fontes de dados num lote agregado para ingestão.|Base de Dados|
|BatchDuration|Yes|Duração do lote|Segundos|Média|A duração da fase de agregação no fluxo de ingestão.|Base de Dados|
|Lotes Processados|Yes|Lotes processados|de palavras|Total|Número de lotes agregados para ingestão. Tipo de lote: se o lote atingiu o tempo de loteamento, o tamanho dos dados ou o número de ficheiros limite definido pela política de loteamento|Base de dados, SealReason|
|LotSize|Yes|Tamanho do lote|Bytes|Média|Tamanho de dados não comprimido esperado num lote agregado para ingestão.|Base de Dados|
|BlobsDropped|Yes|Bolhas caídas|de palavras|Total|Número de bolhas permanentemente rejeitadas por um componente.|Base de dados, ComponentType, Nome de Componentes|
|Blobs Processado|Yes|Blobs Processados|de palavras|Total|Número de bolhas processadas por um componente.|Base de dados, ComponentType, Nome de Componentes|
|BlobsReceived|Yes|Blobs recebidos|de palavras|Total|Número de bolhas recebidas do fluxo de entrada por um componente.|Base de dados, ComponentType, Nome de Componentes|
|CacheUtilização|Yes|Utilização de cache|Percentagem|Média|Nível de utilização no âmbito do cluster|Sem Dimensões|
|ContinuaExportMaxLatenessMinutes|Yes|Exportação Contínua Max Lateness|de palavras|Máximo|O atraso (em minutos) reportado pelos postos de trabalho contínuos de exportação no cluster|Sem Dimensões|
|ContinuousExportNumOfRecordsExported|Yes|Exportação contínua - num de registos exportados|de palavras|Total|Número de registos exportados, disparados para cada artefacto de armazenamento escrito durante a operação de exportação|Nome, Base de Dados ContínuaExport|
|ContínuaExportPendingCount|Yes|Contagem pendente de exportação contínua|de palavras|Máximo|O número de postos de trabalho de exportação permanentes pendentes prontos para a execução|Sem Dimensões|
|ContínuaExportResult|Yes|Resultado contínuo da exportação|de palavras|de palavras|Indica se a Exportação Contínua foi bem sucedida ou falhou|OnlineExport Nome, Resultado, Base de Dados|
|CPU|Yes|CPU|Percentagem|Média|Nível de utilização do CPU|Sem Dimensões|
|DiscoveryLatency|Yes|Latência da Descoberta|Segundos|Média|Reportado por ligações de dados (se existir). Tempo em segundos a partir de quando uma mensagem é encoberta ou o evento é criado até que seja descoberto pela ligação de dados. Este tempo não está incluído na duração total de ingestão do Azure Data Explorer.|ComponenteType, Nome componente|
|EventosDropped|Yes|Eventos caídos|de palavras|Total|Número de eventos diminuído permanentemente por ligação de dados. Será enviada uma métrica de resultado de Ingestão com uma razão de falha.|ComponenteType, Nome componente|
|Eventos Processados|Yes|Eventos Processados|de palavras|Total|Número de eventos processados pelo cluster|ComponenteType, Nome componente|
|EventosProcessadoForEventHubs|Yes|Eventos Processados (para Centros de Eventos/IoT)|de palavras|Total|Número de eventos processados pelo cluster ao ingerir do Event/IoT Hub|EventStatus|
|EventosVivido|Yes|Eventos Recebidos|de palavras|Total|Número de eventos recebidos por ligação de dados.|ComponenteType, Nome componente|
|ExportaçãoUtilização|Yes|Utilização da Exportação|Percentagem|Máximo|Utilização das exportações|Sem Dimensões|
|IngestionLatencyInSeconds|Yes|Latência da Ingestão|Segundos|Média|Latência de dados ingeridos, desde o momento em que os dados foram recebidos no cluster até que estejam preparados para consulta. O período de latência de ingestão depende do cenário de ingestão.|Sem Dimensões|
|IngestionResult|Yes|Resultado da ingestão|de palavras|Total|Número de operações de ingestão|IngestionResultDetails|
|Ingestionutilização|Yes|Utilização da ingestão|Percentagem|Média|Relação das ranhuras de ingestão usadas no cluster|Sem Dimensões|
|IngestionVolumeInMB|Yes|Volume da Ingestão|Bytes|Total|Volume global de dados ingeridos para o cluster|Base de Dados|
|InstânciaCount|Yes|Contagem de Instâncias|de palavras|Média|Contagem total de casos|Sem Dimensões|
|KeepAlive|Yes|Mantenha-se vivo|de palavras|Média|Verificação de sanidade indica que o cluster responde a consultas|Sem Dimensões|
|Visualização Materializada|Yes|Idade de vista materializada|de palavras|Média|A idade da vista materializada em minutos|Base de dados, Nome Visualizado Materializado|
|Visualização Materializada VerDataS|Yes|Perda de dados de visualização materializada|de palavras|Máximo|Indica perda de dados potenciais na vista materializada|Base de dados, MaterializedViewName, Kind|
|MaterializadoViewExtentsRebuild|Yes|Extensões de visualização materializada reconstroem|de palavras|Média|Número de extensões reconstruídas|Base de dados, Nome Visualizado Materializado|
|MaterializadoViewHealth|Yes|Saúde de vista materializada|de palavras|Média|A saúde da vista materializada (1 para saudável, 0 para não-saudável)|Base de dados, Nome Visualizado Materializado|
|MaterializadoViewRecordsInDelta|Yes|Registos de vista materializados em Delta|de palavras|Média|O número de registos na parte não materializada da vista|Base de dados, Nome Visualizado Materializado|
|MaterializadoViewResult|Yes|Resultado da visualização materializada|de palavras|Média|O resultado do processo de materialização|Base de dados, Nome Materializado da Visão, Resultado|
|Queriaduração|Yes|Duração da consulta|Milissegundos|Média|Duração das consultas em segundos|QueriaStatus|
|ConsultaResult|No|Resultado da Consulta|de palavras|de palavras|Número total de consultas.|QueriaStatus|
|QueueLength|Yes|Comprimento da Fila|de palavras|Média|Número de mensagens pendentes na fila de um componente.|ComponenteType|
|FilaOldestMessage|Yes|Mensagem mais antiga da fila|de palavras|Média|Tempo em segundos a partir de quando a mensagem mais antiga na fila foi inserida.|ComponenteType|
|RecebeuDataSizeBytes|Yes|Bytes de tamanho de dados recebidos|Bytes|Média|Tamanho dos dados recebidos por ligação de dados. Este é o tamanho do fluxo de dados, ou do tamanho dos dados brutos, se fornecido.|ComponenteType, Nome componente|
|Estágio|Yes|Latência do estágio|Segundos|Média|Tempo cumulativo a partir do momento em que uma mensagem é descoberta até ser recebida pelo componente de reporte para processamento (o tempo de descoberta é definido quando a mensagem é enquiada para a fila de ingestão, ou quando descoberta pela ligação de dados).|Base de dados, ComponentType|
|SteamingIngestRequestRate|Yes|Velocidade dos Pedidos na Ingestão de Transmissão em Fluxo|de palavras|RateRequestsPerSecond|Taxa de pedido de ingestão de streaming (pedidos por segundo)|Sem Dimensões|
|StreamingIngestDataRate|Yes|Velocidade dos Dados na Ingestão de Transmissão em Fluxo|de palavras|Média|Taxa de dados de ingestão de streaming (MB por segundo)|Sem Dimensões|
|StreamingIngestDuration|Yes|Duração da Ingestão de Transmissão em Fluxo|Milissegundos|Média|Duração da ingestão de streaming em milissegundos|Sem Dimensões|
|StreamingIngestResults|Yes|Resultado da Ingestão de Transmissão em Fluxo|de palavras|Média|Resultado do streaming de ingerir|Resultado|
|TotalNumberOfConcurrentQueries|Yes|Número total de consultas simultâneas|de palavras|Máximo|Número total de consultas simultâneas|Sem Dimensões|
|TotalNumberOfExtents|Yes|Número total de extensões|de palavras|Total|Número total de extensões de dados|Sem Dimensões|
|TotalNumberOfThrottledCommands|Yes|Número total de comandos acelerados|de palavras|Total|Número total de comandos acelerados|Tipo de Comando|
|TotalNumberOfThrottledQueries|Yes|Número total de consultas estranguladas|de palavras|Máximo|Número total de consultas estranguladas|Sem Dimensões|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Ação|Yes|Latência de Ação |Segundos|Média|Latência de ações de fluxo de trabalho concluídas.|Sem Dimensões|
|Ações Computação|Yes|Ações Concluídas |de palavras|Total|Número de ações de fluxo de trabalho concluídas.|Sem Dimensões|
|Ações Destruídas|Yes|Ações falhadas |de palavras|Total|O número de ações de fluxo de trabalho falhou.|Sem Dimensões|
|AçõesSkipped|Yes|Ações ignoradas |de palavras|Total|Número de ações de fluxo de trabalho ignoradas.|Sem Dimensões|
|AçõesStarted|Yes|Ações Iniciadas |de palavras|Total|Número de ações de fluxo de trabalho iniciadas.|Sem Dimensões|
|AçõesDuzidas|Yes|Ações Bem Sucedidas |de palavras|Total|Número de ações de fluxo de trabalho conseguiu.|Sem Dimensões|
|ActionSuccessLatency|Yes|Latência do sucesso da ação |Segundos|Média|Latência de ações de fluxo de trabalho bem sucedidas.|Sem Dimensões|
|ActionThrottledEvents|Yes|Eventos throttled de ação|de palavras|Total|Número de eventos de fluxo de trabalho acelerado.|Sem Dimensões|
|Integração ServiçoEnvironmentConnectorMemoryUsage|Yes|Utilização da memória do conector para ambiente de serviço de integração|Percentagem|Média|Utilização da memória do conector para ambiente de serviço de integração.|Sem Dimensões|
|Integração ServiçoEnvironmentConnectorProcessorUsage|Yes|Utilização do processador do conector para ambiente de serviço de integração|Percentagem|Média|Utilização do processador de conector para ambiente de serviço de integração.|Sem Dimensões|
|Integração ServiçoEnvironmentOdestrabalhomor|Yes|Utilização da memória do fluxo de trabalho para ambiente de serviço de integração|Percentagem|Média|Utilização da memória do fluxo de trabalho para ambiente de serviço de integração.|Sem Dimensões|
|Integração ServiçoEnvironmentWorkflowProcessorUsage|Yes|Utilização do processador de fluxo de trabalho para ambiente de serviço de integração|Percentagem|Média|Utilização do processador de fluxo de trabalho para ambiente de serviço de integração.|Sem Dimensões|
|RunFailurePercentage|Yes|Percentagem de falha de execução|Percentagem|Total|A percentagem de fluxos de trabalho falhou.|Sem Dimensões|
|Estação Runlatency|Yes|Executar Latência|Segundos|Média|A latência do fluxo de trabalho concluído funciona.|Sem Dimensões|
|RunsCancelled|Yes|Execuções canceladas|de palavras|Total|Número de fluxos de trabalho cancelados.|Sem Dimensões|
|RunsCompleted|Yes|Execuções Concluídas|de palavras|Total|Número de fluxos de trabalho concluídos.|Sem Dimensões|
|RunsFailed|Yes|Runs Falhado|de palavras|Total|O número de fluxos de trabalho falhou.|Sem Dimensões|
|RunsStarted|Yes|Execuções iniciadas|de palavras|Total|O número de fluxos de trabalho iniciados.|Sem Dimensões|
|RunsSucceed|Yes|Runs Succeeded|de palavras|Total|Número de fluxos de trabalho conseguiu.|Sem Dimensões|
|RunStartThrottledEvents|Yes|Executar eventos throttled do início|de palavras|Total|Número de fluxos de trabalho iniciam eventos acelerados.|Sem Dimensões|
|RunSuccessLatency|Yes|Executar Latência de sucesso|Segundos|Média|A latência do fluxo de trabalho bem sucedido corre.|Sem Dimensões|
|RunThrottledEvents|Yes|Executar Eventos Throttled|de palavras|Total|Número de ação de fluxo de trabalho ou eventos acelerados.|Sem Dimensões|
|TriggerFireLatency|Yes|Desencadear a latência do fogo |Segundos|Média|Latência de gatilhos de fluxo de trabalho disparados.|Sem Dimensões|
|TriggerLatency|Yes|Desencadear a latência |Segundos|Média|Latência de gatilhos de fluxo de trabalho concluídos.|Sem Dimensões|
|GatilhosCompleted|Yes|Gatilhos Concluídos |de palavras|Total|Número de gatilhos de fluxo de trabalho concluídos.|Sem Dimensões|
|GatilhosFailed|Yes|Gatilhos falhados |de palavras|Total|O número de gatilhos de fluxo de trabalho falhou.|Sem Dimensões|
|Gatilhos Disparados|Yes|Gatilhos disparados |de palavras|Total|Número de gatilhos de fluxo de trabalho disparados.|Sem Dimensões|
|GatilhosSkipped|Yes|Gatilhos ignorados|de palavras|Total|Número de gatilhos de fluxo de trabalho ignorados.|Sem Dimensões|
|GatilhosStarted|Yes|Gatilhos Iniciados |de palavras|Total|O número de gatilhos de fluxo de trabalho começou.|Sem Dimensões|
|GatilhosSsueded|Yes|Gatilhos Bem Sucedidos |de palavras|Total|Número de gatilhos de fluxo de trabalho conseguiu.|Sem Dimensões|
|TriggerSuccessLatency|Yes|Desencadear a latência do sucesso |Segundos|Média|Latência de desencadeamentos de fluxo de trabalho bem sucedidos.|Sem Dimensões|
|TriggerThrottledEvents|Yes|Eventos aceleradores do gatilho|de palavras|Total|Número de eventos aceleradores de fluxo de trabalho.|Sem Dimensões|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Ação|Yes|Latência de Ação |Segundos|Média|Latência de ações de fluxo de trabalho concluídas.|Sem Dimensões|
|Ações Computação|Yes|Ações Concluídas |de palavras|Total|Número de ações de fluxo de trabalho concluídas.|Sem Dimensões|
|Ações Destruídas|Yes|Ações falhadas |de palavras|Total|O número de ações de fluxo de trabalho falhou.|Sem Dimensões|
|AçõesSkipped|Yes|Ações ignoradas |de palavras|Total|Número de ações de fluxo de trabalho ignoradas.|Sem Dimensões|
|AçõesStarted|Yes|Ações Iniciadas |de palavras|Total|Número de ações de fluxo de trabalho iniciadas.|Sem Dimensões|
|AçõesDuzidas|Yes|Ações Bem Sucedidas |de palavras|Total|Número de ações de fluxo de trabalho conseguiu.|Sem Dimensões|
|ActionSuccessLatency|Yes|Latência do sucesso da ação |Segundos|Média|Latência de ações de fluxo de trabalho bem sucedidas.|Sem Dimensões|
|ActionThrottledEvents|Yes|Eventos throttled de ação|de palavras|Total|Número de eventos de fluxo de trabalho acelerado.|Sem Dimensões|
|BillableActionExecutions|Yes|Execuções de ação executáveis|de palavras|Total|Número de execuções de fluxo de trabalho a ser cobrado.|Sem Dimensões|
|BillableTriggerExecutions|Yes|Execuções de gatilhos faturantes|de palavras|Total|Número de execuções de fluxo de trabalho a serem cobradas.|Sem Dimensões|
|Faturação UsageNativeOperação|Yes|Utilização de faturação para execuções de operações nativas|de palavras|Total|Número de execuções de operações nativas a ser cobrado.|Sem Dimensões|
|BillingUsageStandardConnector|Yes|Utilização de faturação para execuções padrão do conector|de palavras|Total|Número de execuções padrão de conector a ser faturado.|Sem Dimensões|
|BillingUsageStorageConsumption|Yes|Faturação de utilização para execuções de consumo de armazenamento|de palavras|Total|Número de execuções de consumo de armazenamento a ser cobrado.|Sem Dimensões|
|RunFailurePercentage|Yes|Percentagem de falha de execução|Percentagem|Total|A percentagem de fluxos de trabalho falhou.|Sem Dimensões|
|Estação Runlatency|Yes|Executar Latência|Segundos|Média|A latência do fluxo de trabalho concluído funciona.|Sem Dimensões|
|RunsCancelled|Yes|Execuções canceladas|de palavras|Total|Número de fluxos de trabalho cancelados.|Sem Dimensões|
|RunsCompleted|Yes|Execuções Concluídas|de palavras|Total|Número de fluxos de trabalho concluídos.|Sem Dimensões|
|RunsFailed|Yes|Runs Falhado|de palavras|Total|O número de fluxos de trabalho falhou.|Sem Dimensões|
|RunsStarted|Yes|Execuções iniciadas|de palavras|Total|O número de fluxos de trabalho iniciados.|Sem Dimensões|
|RunsSucceed|Yes|Runs Succeeded|de palavras|Total|Número de fluxos de trabalho conseguiu.|Sem Dimensões|
|RunStartThrottledEvents|Yes|Executar eventos throttled do início|de palavras|Total|Número de fluxos de trabalho iniciam eventos acelerados.|Sem Dimensões|
|RunSuccessLatency|Yes|Executar Latência de sucesso|Segundos|Média|A latência do fluxo de trabalho bem sucedido corre.|Sem Dimensões|
|RunThrottledEvents|Yes|Executar Eventos Throttled|de palavras|Total|Número de ação de fluxo de trabalho ou eventos acelerados.|Sem Dimensões|
|TotalBillableExecutions|Yes|Execuções totais faturantes|de palavras|Total|Número de execuções de fluxos de trabalho a ser cobrado.|Sem Dimensões|
|TriggerFireLatency|Yes|Desencadear a latência do fogo |Segundos|Média|Latência de gatilhos de fluxo de trabalho disparados.|Sem Dimensões|
|TriggerLatency|Yes|Desencadear a latência |Segundos|Média|Latência de gatilhos de fluxo de trabalho concluídos.|Sem Dimensões|
|GatilhosCompleted|Yes|Gatilhos Concluídos |de palavras|Total|Número de gatilhos de fluxo de trabalho concluídos.|Sem Dimensões|
|GatilhosFailed|Yes|Gatilhos falhados |de palavras|Total|O número de gatilhos de fluxo de trabalho falhou.|Sem Dimensões|
|Gatilhos Disparados|Yes|Gatilhos disparados |de palavras|Total|Número de gatilhos de fluxo de trabalho disparados.|Sem Dimensões|
|GatilhosSkipped|Yes|Gatilhos ignorados|de palavras|Total|Número de gatilhos de fluxo de trabalho ignorados.|Sem Dimensões|
|GatilhosStarted|Yes|Gatilhos Iniciados |de palavras|Total|O número de gatilhos de fluxo de trabalho começou.|Sem Dimensões|
|GatilhosSsueded|Yes|Gatilhos Bem Sucedidos |de palavras|Total|Número de gatilhos de fluxo de trabalho conseguiu.|Sem Dimensões|
|TriggerSuccessLatency|Yes|Desencadear a latência do sucesso |Segundos|Média|Latência de desencadeamentos de fluxo de trabalho bem sucedidos.|Sem Dimensões|
|TriggerThrottledEvents|Yes|Eventos aceleradores do gatilho|de palavras|Total|Número de eventos aceleradores de fluxo de trabalho.|Sem Dimensões|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Núcleos Ativos|Yes|Núcleos Ativos|de palavras|Média|Número de núcleos ativos|Cenário, ClusterName|
|Nóns ativos|Yes|Nóns ativos|de palavras|Média|Número de nós Acitve. Estes são os nós que estão ativamente a gerir um trabalho.|Cenário, ClusterName|
|Cancelar execuções solicitadas|Yes|Cancelar execuções solicitadas|de palavras|Total|Número de execuções onde foi solicitado o cancelamento para este espaço de trabalho. A contagem é atualizada quando o pedido de cancelamento foi recebido para uma execução.|Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Corridas Canceladas|Yes|Corridas Canceladas|de palavras|Total|Número de corridas canceladas para este espaço de trabalho. O conde é atualizado quando uma execução é cancelada com sucesso.|Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Execuções Concluídas|Yes|Execuções Concluídas|de palavras|Total|Número de runs concluídas com sucesso para este espaço de trabalho. A contagem é atualizada quando uma execução tiver sido concluída e a saída tiver sido recolhida.|Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|CpuUtilização|Yes|CpuUtilização|de palavras|Média|Percentagem de utilização num nó CPU. A utilização é reportada em intervalos de um minuto.|Cenário, runId, NodeId, ClusterName|
|Erros|Yes|Erros|de palavras|Total|Número de erros de execução neste espaço de trabalho. A contagem é atualizada sempre que o run encontra um erro.|Scenario|
|Corridas falhadas|Yes|Corridas falhadas|de palavras|Total|Número de corridas falhou neste espaço de trabalho. O conde é atualizado quando uma corrida falha.|Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Finalização de Corridas|Yes|Finalização de Corridas|de palavras|Total|Número de corridas inseridas no estado finalizado para este espaço de trabalho. A contagem é atualizada quando uma execução tiver terminado, mas a recolha de saída ainda em andamento.|Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|GpuMemoryUtilization|Yes|GpuMemoryUtilization|de palavras|Média|Percentagem de utilização da memória num nó GPU. A utilização é reportada em intervalos de um minuto.|Cenário, runId, NodeId, DeviceId, ClusterName|
|GpuUtilização|Yes|GpuUtilização|de palavras|Média|Percentagem de utilização num nó GPU. A utilização é reportada em intervalos de um minuto.|Cenário, runId, NodeId, DeviceId, ClusterName|
|Núcleos Ociosos|Yes|Núcleos Ociosos|de palavras|Média|Número de núcleos ociosos|Cenário, ClusterName|
|Nódoas Ociosas|Yes|Nódoas Ociosas|de palavras|Média|Número de nós ociosos. Os nós inativos são os nós que não estão a gerir nenhum emprego, mas que podem aceitar novos empregos se disponíveis.|Cenário, ClusterName|
|Deixando os Núcleos|Yes|Deixando os Núcleos|de palavras|Média|Número de núcleos de saída|Cenário, ClusterName|
|Deixando os nóns|Yes|Deixando os nóns|de palavras|Média|Número de nós de saída. Deixando nós são os nós que acabaram de processar um trabalho e irão para o estado de Idle.|Cenário, ClusterName|
|Implementação de modelo falhou|Yes|Implementação de modelo falhou|de palavras|Total|Número de implementações de modelos que falharam neste espaço de trabalho|Cenário, Código de Estado|
|Implementação de modelos iniciado|Yes|Implementação de modelos iniciado|de palavras|Total|Número de implementações de modelos iniciadas neste espaço de trabalho|Scenario|
|Implementação de modelos conseguiu|Yes|Implementação de modelos conseguiu|de palavras|Total|Número de implementações de modelos que foram bem sucedidas neste espaço de trabalho|Scenario|
|Registo modelo falhado|Yes|Registo modelo falhado|de palavras|Total|Número de registos de modelos que falharam neste espaço de trabalho|Cenário, Código de Estado|
|Registo modelo Bem sucedido|Yes|Registo modelo Bem sucedido|de palavras|Total|Número de registos de modelos que sucederam neste espaço de trabalho|Scenario|
|Não respondendo corre|Yes|Não respondendo corre|de palavras|Total|Número de corridas que não respondem a este espaço de trabalho. A contagem é atualizada quando uma execução entra no estado de Não Responder.|Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Não começou corre|Yes|Não começou corre|de palavras|Total|Número de runs em Estado Não Iniciado para este espaço de trabalho. A contagem é atualizada quando um pedido é recebido para criar uma execução, mas a informação de execução ainda não foi povoada. |Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Núcleos Preempted|Yes|Núcleos Preempted|de palavras|Média|Número de núcleos pré-apropriados|Cenário, ClusterName|
|Nómadas Preempted|Yes|Nómadas Preempted|de palavras|Média|Número de nós preempted. Estes nós são os nós de baixa prioridade que são retirados da piscina de nós disponíveis.|Cenário, ClusterName|
|Preparação de Corridas|Yes|Preparação de Corridas|de palavras|Total|Número de corridas que se preparam para este espaço de trabalho. A contagem é atualizada quando uma execução entra em estado de preparação enquanto o ambiente de funcionação está a ser preparado.|Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Corridas de Provisionamento|Yes|Corridas de Provisionamento|de palavras|Total|Número de execuções que estão a provisão para este espaço de trabalho. A contagem é atualizada quando uma corrida está à espera da criação ou provisão do alvo do cálculo.|Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Corridas em Fila|Yes|Corridas em Fila|de palavras|Total|Número de corridas que estão na fila para este espaço de trabalho. A contagem é atualizada quando uma corrida é em fila no alvo do cálculo. Pode ocorrer quando se espera que os nós computatados necessários estejam prontos.|Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Percentagem de Utilização de Cots|Yes|Percentagem de Utilização de Cots|de palavras|Média|Por cento das quotas utilizadas|Cenário, ClusterName, VmFamilyName, VmPriority|
|Corridas iniciadas|Yes|Corridas iniciadas|de palavras|Total|Número de corridas a correr para este espaço de trabalho. A contagem é atualizada quando a execução começa a funcionar com os recursos necessários.|Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Execuções ini 'Starting'|Yes|Execuções ini 'Starting'|de palavras|Total|Número de corridas iniciadas para este espaço de trabalho. O Conde é atualizado após pedido para criar informações de execução e execução, como o Run Id, foi povoado|Cenário, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Núcleos Totais|Yes|Núcleos Totais|de palavras|Média|Número de núcleos totais|Cenário, ClusterName|
|Nómada Total|Yes|Nómada Total|de palavras|Média|Número de nós totais. Este total inclui alguns dos nóns ativos, nóns ociosos, nóns inutilizáveis, nóns pré-identificados, deixando os nóns|Cenário, ClusterName|
|Núcleos inutilizáveis|Yes|Núcleos inutilizáveis|de palavras|Média|Número de núcleos inutilizáveis|Cenário, ClusterName|
|Nódes Inutilizáveis|Yes|Nódes Inutilizáveis|de palavras|Média|Número de nós inutilizáveis. Os nós inutilizáveis não estão funcionais devido a algum problema irresolúvel. Azure vai reciclar estes nós.|Cenário, ClusterName|
|Avisos|Yes|Avisos|de palavras|Total|Número de avisos de execução neste espaço de trabalho. A contagem é atualizada sempre que uma corrida encontra um aviso.|Scenario|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/contas

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Percentagem|Média|Disponibilidade das APIs|ApiCategory|
|Utilização|No|Utilização|de palavras|de palavras|Contagem de chamadas da API|ApiCategoria, ApiName, ResultadoType, ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Conta de Ativos|Yes|Contagem de ativos|de palavras|Média|Quantos ativos já são criados na conta de serviço de mídia corrente|Sem Dimensões|
|AssetQuota|Yes|Quota de ativos|de palavras|Média|Quantos ativos são permitidos para a conta de serviço de mídia atual|Sem Dimensões|
|AssetQuotaUsedPercentage|Yes|Percentagem de quota de ativos utilizada|Percentagem|Média|Percentagem de ativos usados na conta de serviço de mídia corrente|Sem Dimensões|
|CanaisAndLiveEventsCount|Yes|Contagem de eventos ao vivo|de palavras|Média|O número total de eventos ao vivo na conta corrente dos serviços de comunicação social|Sem Dimensões|
|ContentKeyPolicyCount|Yes|Contagem de políticas de chave de conteúdo|de palavras|Média|Quantas políticas-chave de conteúdo já estão criadas na conta de serviço de mídia atual|Sem Dimensões|
|ContentKeyPolicyQuota|Yes|Quota de política chave de conteúdo|de palavras|Média|Quantos polícias chave de conteúdo são permitidos para a conta de serviço de mídia atual|Sem Dimensões|
|ContentKeyPolicyQuotaUsedPercentage|Yes|Quota de política de conteúdo utilizada|Percentagem|Média|Política chave de conteúdo usada percentagem na conta de serviço de mídia atual|Sem Dimensões|
|RunningChannelsAndLiveEventsCount|Yes|Contagem de eventos ao vivo|de palavras|Média|O número total de eventos em execução ao vivo na conta corrente dos serviços de comunicação social|Sem Dimensões|
|StreamingPolicyCount|Yes|Contagem de políticas de streaming|de palavras|Média|Quantas políticas de streaming já estão criadas na conta de serviço de mídia atual|Sem Dimensões|
|StreamingPolicyQuota|Yes|Quota de política de streaming|de palavras|Média|Quantas políticas de streaming são permitidas para a conta atual do serviço de mídia|Sem Dimensões|
|StreamingPolicyQuotaUsedPercentage|Yes|Percentagem utilizada pela quota de política de streaming|Percentagem|Média|Política de Streaming usada percentualmente na conta de serviço de mídia corrente|Sem Dimensões|


## <a name="microsoftmediamediaservicesliveevents"></a>Microsoft.Media/mediaservices/liveEvents

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Ingestear|Yes|Evento ao vivo ingere bitrate|BitsPerSecond|Média|O bitrate de entrada ingeriu para um evento ao vivo, em pedaços por segundo.|Nome de track|
|IngestDriftValue|Yes|Evento ao vivo ingere valor de deriva|Segundos|Máximo|Deriva entre o tempo de marcação do conteúdo ingerido e o relógio do sistema, medido em segundos por minuto. Um valor não zero indica que o conteúdo ingerido está a chegar mais lento do que a hora do relógio do sistema.|Nome de track|
|IngestLastTimestamp|Yes|Live Event ingeri última hora de tempo|Milissegundos|Máximo|Última vez que o timetamp ingeriu para um evento ao vivo.|Nome de track|
|LiveOutputLastTimestamp|Yes|Última hora de saída|Milissegundos|Máximo|Timetamp do último fragmento enviado para armazenamento para uma saída de evento ao vivo.|Nome de track|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|CPU|Yes|Utilização da CPU|Percentagem|Média|Utilização do CPU para pontos finais de streaming premium. Estes dados não estão disponíveis para os pontos finais de streaming padrão.|VmId|
|Saída|Yes|Saída|Bytes|Total|A quantidade de dados da Egress, em bytes.|OutputFormat|
|EgressBandwidth|No|Largura de banda da Egress|BitsPerSecond|Média|Largura de banda de Egress em pedaços por segundo.|VmId|
|Pedidos|Yes|Pedidos|de palavras|Total|Pedidos para um Ponto Final de Streaming.|OutputFormat, HttpStatusCode, ErrorCode|
|SucessoE2ELatency|Yes|Fim do sucesso para acabar com a latência|Milissegundos|Média|A latência média para pedidos bem sucedidos em milissegundos.|OutputFormat|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft.MixedReality/remoteRenderIngS

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Ativaressessões de Esessão|Yes|Sessões de renderização ativa|de palavras|Média|Número total de sessões de renderização ativas|SessionType, SDKVersion|
|ActivosConvertidos|Yes|Ativos Convertidos|de palavras|Total|Número total de ativos convertidos|SDKVersion|


## <a name="microsoftmixedrealityspatialanchorsaccounts"></a>Microsoft.MixedReality/spatialAnchorsAccounts

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AncoradourosCretado|Yes|Âncoras Criadas|de palavras|Total|Número de Âncoras criadas|DeviceFamily, SDKVersion|
|ÂncorasDeletada|Yes|Âncoras apagadas|de palavras|Total|Número de âncoras apagadas|DeviceFamily, SDKVersion|
|ÂncorasQueried|Yes|Âncoras Questionadas|de palavras|Total|Número de âncoras espaciais consultadas|DeviceFamily, SDKVersion|
|ÂncorasUpdadas|Yes|Âncoras Atualizadas|de palavras|Total|Número de âncoras atualizadas|DeviceFamily, SDKVersion|
|PosesFound|Yes|Poses Encontradas|de palavras|Total|Número de Poses devolvidas|DeviceFamily, SDKVersion|
|Total Desamericanos|Yes|Âncoras Diárias Totais|de palavras|Média|Número total de Âncoras - Diariamente|DeviceFamily, SDKVersion|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedsize|Yes|Tamanho atribuído à piscina|Bytes|Média|Tamanho a provisionado desta piscina|Sem Dimensões|
|VolumePoolAllocatedToVolumeThroughput|Yes|Produção de piscina atribuída|BytesPerSecond|Média|Soma da produção de todos os volumes pertencentes à piscina|Sem Dimensões|
|VolumePoolAllocatedUsed|Yes|Piscina atribuída ao tamanho do volume|Bytes|Média|Tamanho utilizado atribuído da piscina|Sem Dimensões|
|VolumePoolProvisionedThroughput|Yes|Produção provisida para a piscina|BytesPerSecond|Média|Produção a provisionada desta piscina|Sem Dimensões|
|VolumePoolTotalLogicalsize|Yes|Tamanho consumido da piscina|Bytes|Média|Soma do tamanho lógico de todos os volumes pertencentes à piscina|Sem Dimensões|
|VolumePoolTotalSnapshotSize|Yes|Tamanho total do snapshot para a piscina|Bytes|Média|Soma de tamanho instantâneo de todos os volumes nesta piscina|Sem Dimensões|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Média DeOncência|Yes|Latência média de leitura|MilliSeconds|Média|Latência média de leitura em milissegundos por operação|Sem Dimensões|
|Média Desaquimia|Yes|Latência média de escrita|MilliSeconds|Média|Latência escrita média em milissegundos por operação|Sem Dimensões|
|CbsVolumeBackupActive|Yes|É cópia de segurança de volume suspensa|de palavras|Média|A política de reserva está suspensa pelo volume? 1 se sim, 0 se não.|Sem Dimensões|
|CbsVolumeLogicalBackupBytes|Yes|Volume Backup Bytes|Bytes|Média|Bytes totais apoiados para este Volume.|Sem Dimensões|
|CbsVolumeOperationComplete|Yes|É a operação de backup de volume completa|de palavras|Média|A última cópia de segurança de volume ou a operação restaurada completa com sucesso? 1 se sim, 0 se não.|Sem Dimensões|
|CbsVolumeOperationTransferredBytes|Yes|Cópia de segurança de volume Últimos Bytes transferidos|Bytes|Média|Bytes totais transferidos para a última operação de backup ou restauro.|Sem Dimensões|
|CbsVolumeProtector|Yes|É backup de volume ativado|de palavras|Média|A cópia de segurança está ativada para o volume? 1 se sim, 0 se não.|Sem Dimensões|
|Outros|Yes|Outra produção|BytesPerSecond|Média|Outra produção (que não é lida ou escrita) em bytes por segundo|Sem Dimensões|
|ReadIops|Yes|Ler iops|CondePerSecond|Média|Ler operações de in/out por segundo|Sem Dimensões|
|ReadThroughput|Yes|Ler a produção|BytesPerSecond|Média|Ler a produção em bytes por segundo|Sem Dimensões|
|TotalThroughput|Yes|Produção total|BytesPerSecond|Média|Soma de todos os bytes por segundo|Sem Dimensões|
|VolumeAllocatedsize|Yes|Volume de tamanho atribuído|Bytes|Média|O tamanho previsto de um volume|Sem Dimensões|
|VolumeConsumedSizePercentage|Yes|Volume percentual Tamanho Consumido|Percentagem|Média|A percentagem do volume consumido, incluindo instantâneos.|Sem Dimensões|
|VolumeLogicalsize|Yes|Tamanho consumido do volume|Bytes|Média|Tamanho lógico do volume (bytes usados)|Sem Dimensões|
|VolumeSnapshotSize|Yes|Tamanho do instantâneo de volume|Bytes|Média|Tamanho de todos os instantâneos em volume|Sem Dimensões|
|WriteIops|Yes|Escrever iops|CondePerSecond|Média|Escrever operações de in/out por segundo|Sem Dimensões|
|WriteThroughput|Yes|Escrever produção|BytesPerSecond|Média|Escrever produção em bytes por segundo|Sem Dimensões|
|XregionReplicationHealthy|Yes|É o estado de replicação do volume saudável|de palavras|Média|Condição da relação, 1 ou 0.|Sem Dimensões|
|XregionReplicationLagTime|Yes|Tempo de desfasamento da replicação do volume|Segundos|Média|A quantidade de tempo em segundos através da qual os dados no espelho ficam atrás da fonte.|Sem Dimensões|
|XregionReplicationLastTransferDuration|Yes|Duração da transferência de replicação de volume|Segundos|Média|O tempo em segundos que levou para a última transferência ser concluída.|Sem Dimensões|
|XregionReplicationLastTransfersize|Yes|Tamanho da última transferência de replicação de volume|Bytes|Média|O número total de bytes transferidos como parte da última transferência.|Sem Dimensões|
|XregionReplicationRelationshipProgress|Yes|Progresso da replicação do volume|Bytes|Média|Quantidade total de dados transferidos para a operação de transferência em curso.|Sem Dimensões|
|XregionReplicationRelationshipTransferring|Yes|É a transferência de replicação de volume|de palavras|Média|Se o estado da replicação do volume é 'transferido'.|Sem Dimensões|
|XregionReplicationTotalTransferBytes|Yes|Transferência total de replicação de volume|Bytes|Média|Bytes cumulativos transferidos para a relação.|Sem Dimensões|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|No|Tempo total do Gateway de Aplicação|MilliSeconds|Média|O tempo médio que leva para que um pedido seja processado e a sua resposta seja enviada. Isto é calculado como média do intervalo a partir do momento em que o Application Gateway recebe o primeiro byte de um pedido HTTP ao momento em que a operação de envio de resposta termina. É importante notar que isto geralmente inclui o tempo de processamento do Gateway de Aplicação, tempo que os pacotes de pedido e resposta estão viajando através da rede e o tempo que o servidor backend demorou a responder.|Serviço de Escuta|
|AvgRequestCountPerHealthyHost|No|Pedidos por minuto por Anfitrião Saudável|de palavras|Média|Contagem média de pedidos por minuto por hospedeiro saudável de backend em uma piscina|BackendSettingsPool|
|BackendConnectTime|No|Tempo de ligação de backend|MilliSeconds|Média|Tempo gasto a estabelecer uma ligação com um servidor de backend|Ouvinte, BackendServer, BackendPool, BackendHttpSetting|
|BackendFirstByteResponseTime|No|Tempo de resposta de backend First Byte|MilliSeconds|Média|Intervalo de tempo entre o início do estabelecimento de uma ligação ao servidor backend e a receção do primeiro byte do cabeçalho de resposta, aproximando o tempo de processamento do servidor backend|Ouvinte, BackendServer, BackendPool, BackendHttpSetting|
|BackendLastByteResponseTime|No|Tempo de resposta de Backend Last Byte|MilliSeconds|Média|Intervalo de tempo entre o início do estabelecimento de uma ligação ao servidor backend e a receção do último byte do corpo de resposta|Ouvinte, BackendServer, BackendPool, BackendHttpSetting|
|BackendResponseStatus|Yes|Estado de resposta de backend|de palavras|Total|O número de códigos de resposta HTTP gerados pelos membros backend. Isto não inclui quaisquer códigos de resposta gerados pelo Gateway de Aplicação.|BackendServer, BackendPool, BackendHttpSetting, HttpStatusGroup|
|Contagem bloqueada|Yes|Firewall de aplicação web bloqueou distribuição de regras de pedidos|de palavras|Total|Firewall de aplicação web bloqueou distribuição de regras de pedidos|RuleGroup, RuleId|
|BlockedReqCount|Yes|Contagem de pedidos bloqueados de firewall de aplicação web|de palavras|Total|Firewall de aplicação web bloqueou contagem de pedidos|Sem Dimensões|
|BytesReceived|Yes|Bytes Recebidos|Bytes|Total|O número total de bytes recebidos pela Gateway de Aplicação dos clientes|Serviço de Escuta|
|BytesSent|Yes|Bytes Enviados|Bytes|Total|O número total de bytes enviados pela Porta de Aplicação aos clientes|Serviço de Escuta|
|Unidades de capacidade|No|Unidades de Capacidade Corrente|de palavras|Média|Unidades de capacidade consumidas|Sem Dimensões|
|ClientRtt|No|RTT cliente|MilliSeconds|Média|Tempo médio de ida e volta entre clientes e Application Gateway. Esta métrica indica quanto tempo demora a estabelecer ligações e reconhecimentos de retorno|Serviço de Escuta|
|Unidades computeuções|No|Unidades de Computação Atuais|de palavras|Média|Unidades de computação consumidas|Sem Dimensões|
|CpuUtilização|No|Utilização da CPU|Percentagem|Média|Utilização atual do CPU do Gateway de Aplicações|Sem Dimensões|
|Correntes Deconhecões|Yes|Conexões atuais|de palavras|Total|Contagem de ligações atuais estabelecidas com Gateway de Aplicação|Sem Dimensões|
|Vulnerabilidades estimadas da capacidade de pagamento|No|Unidades de Capacidade Faturadas Estimadas|de palavras|Média|Unidades de capacidade estimadas que serão carregadas|Sem Dimensões|
|Requessos Falhados|Yes|Pedidos com Falhas|de palavras|Total|Contagem de pedidos falhados que a Application Gateway serviu|BackendSettingsPool|
|Unidades de Capacidade DeBilizáveis Fixas|No|Unidades de Capacidade Faturadas Fixas|de palavras|Média|Unidades de capacidade mínima que serão carregadas|Sem Dimensões|
|HealthyHostCount|Yes|Contagem saudável do anfitrião|de palavras|Média|Número de hospedeiros saudáveis|BackendSettingsPool|
|MatchedCount|Yes|Distribuição total de regras de firewall de aplicação web|de palavras|Total|Distribuição total de regras da Web Application Firewall para o tráfego de entrada|RuleGroup, RuleId|
|NewConnectionsPerSecond|No|Novas ligações por segundo|CondePerSecond|Média|Novas ligações por segundo estabelecidas com Application Gateway|Sem Dimensões|
|Estatísticas de Resposta|Yes|Estado de resposta|de palavras|Total|Estado de resposta http devolvido pelo Application Gateway|Grupo HttpStatus|
|Débito|No|Débito|BytesPerSecond|Média|Número de bytes por segundo que o Gateway de Aplicação serviu|Sem Dimensões|
|TlsProtocol|Yes|Protocolo TLS do Cliente|de palavras|Total|O número de pedidos TLS e não-TLS iniciados pelo cliente que estabeleceu a ligação com o Gateway de Aplicação. Para visualizar a distribuição do protocolo TLS, filtre pela dimensão do Protocolo TLS.|Ouvinte, TlsProtocol|
|TotalRequests|Yes|Total de Pedidos|de palavras|Total|Contagem de pedidos bem sucedidos que a Application Gateway serviu|BackendSettingsPool|
|Não-saudávelHostCount|Yes|Contagem de anfitriões pouco saudável|de palavras|Média|Número de anfitriões insalubres|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AplicaçãoRuleHit|Yes|As regras de aplicação atingem a contagem|de palavras|Total|Número de vezes que as regras de aplicação foram atingidas|Estado, Razão, Protocolo|
|Dados Processados|Yes|Dados processados|Bytes|Total|Quantidade total de dados processados por esta firewall|Sem Dimensões|
|FirewallHealth|Yes|Estado de saúde de firewall|Percentagem|Média|Indica a saúde geral desta firewall|Estado, Razão|
|NetworkRuleHit|Yes|Regras de rede atingem a contagem|de palavras|Total|Número de vezes que as regras da rede foram atingidas|Estado, Razão, Protocolo|
|SNATPortutilization|Yes|Utilização do porto de SNAT|Percentagem|Média|Percentagem de portas SNAT de saída atualmente em uso|Protocolo|
|Débito|No|Débito|BitsPerSecond|Média|Produção processada por esta firewall|Sem Dimensões|


## <a name="microsoftnetworkconnections"></a>Microsoft.Network/conexões

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Média|Bits que entram no Azure por segundo|Sem Dimensões|
|BitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Média|Bits que saem do Azure por segundo|Sem Dimensões|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|QueryVolume|Yes|Volume de consulta|de palavras|Total|Número de consultas servidas para uma zona de DNS|Sem Dimensões|
|RecordSetCapacityUtilização|No|Record Fixo Utilização da Capacidade|Percentagem|Máximo|Por cento da capacidade do Conjunto recorde utilizada por uma zona de DNS|Sem Dimensões|
|RecordSetCount|Yes|Contagem de recordes|de palavras|Máximo|Número de conjuntos de recordes numa zona de DNS|Sem Dimensões|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ArpAilability|Yes|Disponibilidade ARP|Percentagem|Média|Disponibilidade de ARP da MSEE para todos os pares.|PeeringType, Peer|
|BgpAilability|Yes|Disponibilidade BGP|Percentagem|Média|Disponibilidade de BGP da MSEE para todos os pares.|PeeringType, Peer|
|BitsInPerSecond|No|BitsInPerSecond|BitsPerSecond|Média|Bits que entram no Azure por segundo|PeeringType, DeviceRole|
|BitsOutPerSecond|No|BitsOutPerSecond|BitsPerSecond|Média|Bits que saem do Azure por segundo|PeeringType, DeviceRole|
|GlobalReachBitsInPerSecond|No|GlobalReachBitsInPerSecond|BitsPerSecond|Média|Bits que entram no Azure por segundo|PeeredCircuitsKey|
|GlobalReachBitsOutPerSecond|No|GlobalReachBitsOutPerSecond|BitsPerSecond|Média|Bits que saem do Azure por segundo|PeeredCircuitsKey|
|QosDropBitsInPerSecond|Yes|DropInBitsPerSecond|BitsPerSecond|Média|Ingress bits de dados caiu por segundo|Sem Dimensões|
|QosDropBitsOutPerSecond|Yes|DropOutBitsPerSecond|BitsPerSecond|Média|Pedaços de dados de Egress caíram por segundo|Sem Dimensões|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Média|Bits que entram no Azure por segundo|Sem Dimensões|
|BitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Média|Bits que saem do Azure por segundo|Sem Dimensões|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|No|BitsInPerSecond|BitsPerSecond|Média|Bits que entram no Azure por segundo|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|No|BitsOutPerSecond|BitsPerSecond|Média|Bits que saem do Azure por segundo|ConnectionName|
|ExpressRouteGatewayCountOfRoutesAdvertisedToPeer|Yes|Contagem de rotas anunciadas a peer (pré-visualização)|de palavras|Máximo|Contagem de rotas anunciadas para peer by ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Yes|Contagem de rotas aprendidas a par (pré-visualização)|de palavras|Máximo|Contagem de rotas aprendidas a partir de pares por ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCpuUtilization|Yes|Utilização do CPU (Pré-visualização)|de palavras|Média|Utilização do CPU do Portal ExpressRoute|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|No|Variação da frequência das rotas (pré-visualização)|de palavras|Total|Variação de frequência de rotas no Portal ExpressRoute|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|No|Número de VMs na Rede Virtual (Pré-visualização)|de palavras|Máximo|Número de VMs na Rede Virtual|Sem Dimensões|
|ExpressRouteGatewayPacketsPerSecond|No|Pacotes por segundo (Pré-visualização)|CondePerSecond|Média|Contagem de pacotes de ExpressRoute Gateway|roleInstance|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Estado Administrativo|Yes|Estado Administrativo|de palavras|Média|Estado administrativo do porto|Ligação|
|LineProtocol|Yes|LineProtocol|de palavras|Média|Estado do protocolo de linha da porta|Ligação|
|PortBitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Média|Bits que entram no Azure por segundo|Ligação|
|PortBitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Média|Bits que saem do Azure por segundo|Ligação|
|RxLightLevel|Yes|RxLightLevel|de palavras|Média|Nível de luz Rx em dBm|Link|
|TxLightLevel|Yes|TxLightLevel|de palavras|Média|Nível de luz Tx em dBm|Link|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|Yes|Percentagem de Saúde backend|Percentagem|Média|A percentagem de sondas de saúde bem sucedidas do representante http/s para backends|Backend|
|BackendRequestCount|Yes|Contagem de pedidos de backend|de palavras|Total|O número de pedidos enviados do representante http/S para backends|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestatency|Yes|Pedido de backend Latência|MilliSeconds|Média|O tempo calculado a partir do momento em que o pedido foi enviado pelo representante HTTP/S para o backend até que o representante HTTP/S recebeu o último byte de resposta a partir do backend|Back-end|
|BillableResponseSize|Yes|Tamanho da resposta faturada|Bytes|Total|O número de bytes faturantes (mínimo 2KB por pedido) enviados como respostas do proxy HTTP/S aos clientes.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|PedidoCount|Yes|Número de Pedidos|de palavras|Total|O número de pedidos de clientes servidos pelo representante http/s|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|Solicitação|Yes|Tamanho do pedido|Bytes|Total|O número de bytes enviados como pedidos de clientes para o proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|Tamanho das respostas|Yes|Tamanho da resposta|Bytes|Total|O número de bytes enviados como respostas de http/S proxy aos clientes|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency|Yes|Latência total|MilliSeconds|Média|O tempo calculado a partir do momento em que o pedido do cliente foi recebido pelo representante HTTP/S até que o cliente reconhecesse o último byte de resposta do representante HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|Yes|Contagem de pedidos de firewall de aplicação web|de palavras|Total|O número de pedidos de cliente processados pela Firewall de Aplicação Web|Nome de Política, Nome de Regras, Ação|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AlocadosSnatPorts|No|Portos SNAT atribuídos|de palavras|Média|Número total de portas SNAT atribuídas dentro do período de tempo|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|ByteCount|Yes|Byte Count|Bytes|Total|Número total de Bytes transmitidos dentro do período de tempo|FrontendIPAddress, FrontendPort, Direção|
|DipDUilability|Yes|Estado da Sonda de Estado de Funcionamento|de palavras|Média|Estado médio da sonda de saúde do balanceador de carga por duração do tempo|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|PacoteCount|Yes|Contagem de Pacotes|de palavras|Total|Número total de Pacotes transmitidos dentro do período de tempo|FrontendIPAddress, FrontendPort, Direção|
|SnatConnectionCount|Yes|Contagem de ligação SNAT|de palavras|Total|Número total de novas ligações SNAT criadas dentro do período de tempo|FrontendIPAddress, BackendIPAddress, ConnectionState|
|SYNCount|Yes|Contagem de SINA|de palavras|Total|Número total de pacotes SYN transmitidos dentro do período de tempo|FrontendIPAddress, FrontendPort, Direção|
|UssnatPorts|No|Portas SNAT usadas|de palavras|Média|Número total de portas SNAT utilizadas dentro do período de tempo|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|VipSAdability|Yes|Disponibilidade do Caminho de Dados|de palavras|Média|Disponibilidade média de trajetória do balanceador de carga por duração do tempo|FrontendIPAddress, FrontendPort|


## <a name="microsoftnetworknatgateways"></a>Microsoft.Network/natGateways

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ByteCount|Yes|Bytes|Bytes|Total|Número total de Bytes transmitidos dentro do período de tempo|Protocolo, Direção|
|DatapathDabilidade|Yes|Disponibilidade de datapath (pré-visualização)|de palavras|Média|Disponibilidade de datapath nat Gateway|Sem Dimensões|
|PacoteCount|Yes|Pacotes|de palavras|Total|Número total de Pacotes transmitidos dentro do período de tempo|Protocolo, Direção|
|PacketDropCount|Yes|Pacotes derrubados|de palavras|Total|Contagem de pacotes abandonados|Sem Dimensões|
|SNATConnectionCount|Yes|Contagem de ligação SNAT|de palavras|Total|Total de conexões ativas simultâneas|Protocolo, ConnectionState|
|TotalConnectionCount|Yes|Contagem total de ligação SNAT|de palavras|Total|Número total de ligações SNAT ativas|Protocolo|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|BytesReceivedRate|Yes|Bytes Recebidos|Bytes|Total|Número de bytes que a Interface de Rede recebeu|Sem Dimensões|
|BytesSentRate|Yes|Bytes Enviados|Bytes|Total|Número de bytes enviados pela Interface de Rede|Sem Dimensões|
|PacketsReceivedRate|Yes|Pacotes Recebidos|de palavras|Total|Número de pacotes que a Interface de Rede recebeu|Sem Dimensões|
|PacotesSEntRate|Yes|Pacotes Enviados|de palavras|Total|Número de pacotes enviados pela Interface de Rede|Sem Dimensões|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Médias Desvelundos|Yes|Avg. Tempo de ida e volta (ms) (clássico)|MilliSeconds|Média|Tempo médio de ida e volta (ms) para sondas de monitorização da conectividade enviadas entre a fonte e o destino|Sem Dimensões|
|ChequesFailedPercent|Yes|Cheques Falhados Por cento|Percentagem|Média|% dos controlos de monitorização da conectividade falharam|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|ProbesFailedPercent|Yes|% Sondas Falhadas (clássica)|Percentagem|Média|% das sondas de monitorização da conectividade falharam|Sem Dimensões|
|RoundTripTimeMs|Yes|tempo Round-Trip (ms)|MilliSeconds|Média|Tempo de ida e volta em milissegundos para os controlos de conectividade|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|TesteResult|Yes|Resultado do teste|de palavras|Média|Resultado do teste do monitor de ligação|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, TestResultCriterion, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft.Network/p2sVpnGateways

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|P2SBandwidth|Yes|Largura de banda Gateway P2S|BytesPerSecond|Média|Largura de banda média ponto-a-local de uma porta de entrada em bytes por segundo|Sem Dimensões|
|P2SConnectionCount|Yes|Contagem de Ligações P2S|de palavras|Total|Contagem de ligação ponto a site de um gateway|Protocolo|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft.Network/privateDnsZones

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|QueryVolume|Yes|Volume de consulta|de palavras|Total|Número de consultas servidas para uma zona privada de DNS|Sem Dimensões|
|RecordSetCapacityUtilização|No|Record Fixo Utilização da Capacidade|Percentagem|Máximo|Por cento da capacidade do Conjunto recorde utilizada por uma zona privada de DNS|Sem Dimensões|
|RecordSetCount|Yes|Contagem de recordes|de palavras|Máximo|Número de conjuntos de recordes numa zona privada de DNS|Sem Dimensões|
|VirtualNetworkLinkLinkCapacityUtilization|No|Utilização da capacidade de ligação da rede virtual|Percentagem|Máximo|Por cento da capacidade de Ligação de Rede Virtual utilizada por uma zona privada de DNS|Sem Dimensões|
|VirtualNetworkLinkCount|Yes|Contagem de link de rede virtual|de palavras|Máximo|Número de Redes Virtuais ligadas a uma zona privada de DNS|Sem Dimensões|
|VirtualNetworkWithRegistrationCapacityUtilization|No|Utilização da capacidade de utilização da capacidade de registo de rede virtual|Percentagem|Máximo|Por cento da Ligação de Rede Virtual com capacidade de registo automático utilizada por uma zona privada de DNS|Sem Dimensões|
|VirtualNetworkWithRegistrationLinkCount|Yes|Contagem de link de registo de rede virtual|de palavras|Máximo|Número de Redes Virtuais ligadas a uma zona privada de DNS com registo automático|Sem Dimensões|


## <a name="microsoftnetworkprivateendpoints"></a>Microsoft.Network/privateEndpoints

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|PEBytesIn|Yes|Bytes In|de palavras|Total|Número total de Bytes out|PrivateEndpointId|
|PEBytesOut|Yes|Bytes out|de palavras|Total|Número total de Bytes out|PrivateEndpointId|


## <a name="microsoftnetworkprivatelinkservices"></a>Microsoft.Network/privateLinkServices

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|PLSBytesIn|Yes|Bytes In|de palavras|Total|Número total de Bytes out|PrivateLinkServiceId|
|PlSBytesOut|Yes|Bytes out|de palavras|Total|Número total de Bytes out|PrivateLinkServiceId|
|PLSNatPortsUsage|Yes|Utilização de Portos Nat|Percentagem|Média|Utilização de Portos Nat|PrivateLinkServiceId, PrivateLinkServiceIPAddress|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ByteCount|Yes|Byte Count|Bytes|Total|Número total de Bytes transmitidos dentro do período de tempo|Porto, Direção|
|BytesDroppedDDoS|Yes|Bytes de entrada deixaram cair DDoS|BytesPerSecond|Máximo|Bytes de entrada deixaram cair DDoS|Sem Dimensões|
|BytesForwardedDDoS|Yes|Bytes de entrada reencaminhado DDoS|BytesPerSecond|Máximo|Bytes de entrada reencaminhado DDoS|Sem Dimensões|
|BytesInDDoS|Yes|Bytes de entrada DDoS|BytesPerSecond|Máximo|Bytes de entrada DDoS|Sem Dimensões|
|DDoSTriggerSYNPackets|Yes|Pacotes SYN de entrada para desencadear a mitigação do DDoS|CondePerSecond|Máximo|Pacotes SYN de entrada para desencadear a mitigação do DDoS|Sem Dimensões|
|Pacotes DDoSTriggerTCP|Yes|Pacotes TCP de entrada para desencadear mitigação do DDoS|CondePerSecond|Máximo|Pacotes TCP de entrada para desencadear mitigação do DDoS|Sem Dimensões|
|DDoSTriggerUDPPackets|Yes|Pacotes UDP de entrada para desencadear mitigação do DDoS|CondePerSecond|Máximo|Pacotes UDP de entrada para desencadear mitigação do DDoS|Sem Dimensões|
|IfUnderDDosAttack|Yes|Sob o ataque do DDoS ou não|de palavras|Máximo|Sob o ataque do DDoS ou não|Sem Dimensões|
|PacoteCount|Yes|Contagem de Pacotes|de palavras|Total|Número total de Pacotes transmitidos dentro do período de tempo|Porto, Direção|
|PacotesDroppedDDoS|Yes|Pacotes de entrada deixaram cair DDoS|CondePerSecond|Máximo|Pacotes de entrada deixaram cair DDoS|Sem Dimensões|
|PacotesForwardedDDoS|Yes|Pacotes de entrada reencaminhados DDoS|CondePerSecond|Máximo|Pacotes de entrada reencaminhados DDoS|Sem Dimensões|
|PacotesInDDoS|Yes|Pacotes de entrada DDoS|CondePerSecond|Máximo|Pacotes de entrada DDoS|Sem Dimensões|
|SynCount|Yes|Contagem de SINA|de palavras|Total|Número total de pacotes SYN transmitidos dentro do período de tempo|Porto, Direção|
|TCPBytesDroppedDDoS|Yes|Bytes TCP de entrada deixaram cair DDoS|BytesPerSecond|Máximo|Bytes TCP de entrada deixaram cair DDoS|Sem Dimensões|
|TCPBytesForwardedDDoS|Yes|Inbound TCP bytes reencaminhado DDoS|BytesPerSecond|Máximo|Inbound TCP bytes reencaminhado DDoS|Sem Dimensões|
|TCPBytesInDDoS|Yes|Entrada TCP bytes DDoS|BytesPerSecond|Máximo|Entrada TCP bytes DDoS|Sem Dimensões|
|TCPPacketsDroppedDDoS|Yes|Pacotes TCP de entrada deixaram cair DDoS|CondePerSecond|Máximo|Pacotes TCP de entrada deixaram cair DDoS|Sem Dimensões|
|TCPPacketsForwardedDDoS|Yes|Pacotes TCP de entrada reencaminhados DDoS|CondePerSecond|Máximo|Pacotes TCP de entrada reencaminhados DDoS|Sem Dimensões|
|TCPPacketsInDDoS|Yes|Pacotes TCP de entrada DDoS|CondePerSecond|Máximo|Pacotes TCP de entrada DDoS|Sem Dimensões|
|UDPBytesDroppedDDoS|Yes|Bytes udp de entrada deixaram cair DDoS|BytesPerSecond|Máximo|Bytes udp de entrada deixaram cair DDoS|Sem Dimensões|
|UDPBytesForwardedDDoS|Yes|Bytes de UDP de entrada reencaminhados DDoS|BytesPerSecond|Máximo|Bytes de UDP de entrada reencaminhados DDoS|Sem Dimensões|
|UDPBytesInDDoS|Yes|UDP bytes DDoS de entrada|BytesPerSecond|Máximo|UDP bytes DDoS de entrada|Sem Dimensões|
|UDPPacketsDroppedDDoS|Yes|Pacotes UDP de entrada deixaram cair DDoS|CondePerSecond|Máximo|Pacotes UDP de entrada deixaram cair DDoS|Sem Dimensões|
|UDPPacketsForwardedDDoS|Yes|Pacotes UDP de entrada reencaminhados DDoS|CondePerSecond|Máximo|Pacotes UDP de entrada reencaminhados DDoS|Sem Dimensões|
|UDPPacketsInDDoS|Yes|Pacotes de UDP de entrada DDoS|CondePerSecond|Máximo|Pacotes de UDP de entrada DDoS|Sem Dimensões|
|VipSAdability|Yes|Disponibilidade do Caminho de Dados|de palavras|Média|Disponibilidade média de endereço IP por duração de tempo|Porta|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Yes|Estado do ponto final por Endpoint|de palavras|Máximo|1 se o estado da sonda de um ponto final for "Ativado", 0 caso contrário.|Nome de ponto final|
|QpsByEndpoint|Yes|Consultas por Endpoint Devolvido|de palavras|Total|Número de vezes que um ponto final do Gestor de Tráfego foi devolvido no prazo dado|Nome de ponto final|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Largura média da largura|Yes|Largura de banda Gateway S2S|BytesPerSecond|Média|Largura de banda média local-local de um gateway in bytes por segundo|Sem Dimensões|
|ExpressRouteGatewayCountOfRoutesAdvertisedToPeer|Yes|Contagem de rotas anunciadas a peer (pré-visualização)|de palavras|Máximo|Contagem de rotas anunciadas para peer by ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Yes|Contagem de rotas aprendidas a par (pré-visualização)|de palavras|Máximo|Contagem de rotas aprendidas a partir de pares por ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCpuUtilization|Yes|Utilização do CPU (Pré-visualização)|de palavras|Média|Utilização do CPU do Portal ExpressRoute|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|No|Variação da frequência das rotas (pré-visualização)|de palavras|Total|Variação de frequência de rotas no Portal ExpressRoute|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|No|Número de VMs na Rede Virtual (Pré-visualização)|de palavras|Máximo|Número de VMs na Rede Virtual|Sem Dimensões|
|ExpressRouteGatewayPacketsPerSecond|No|Pacotes por segundo (Pré-visualização)|CondePerSecond|Média|Contagem de pacotes de ExpressRoute Gateway|roleInstance|
|P2SBandwidth|Yes|Largura de banda Gateway P2S|BytesPerSecond|Média|Largura de banda média ponto-a-local de uma porta de entrada em bytes por segundo|Sem Dimensões|
|P2SConnectionCount|Yes|Contagem de Ligações P2S|de palavras|Máximo|Contagem de ligação ponto a site de um gateway|Protocolo|
|TunnelAverageBandwidth|Yes|Largura de Banda do Túnel|BytesPerSecond|Média|Largura de banda média de um túnel em bytes por segundo|Nome de conexão, RemoteIP|
|TunnelEgressBytes|Yes|Bytes de Saída do Túnel|Bytes|Total|Bytes de saída de um túnel|Nome de conexão, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Yes|Remoção do Pacote de Erro de Correspondência de TS de Saída do Túnel|de palavras|Total|Contagem de remoção do pacote de saída a partir do erro de correspondência do seletor de tráfego de um túnel|Nome de conexão, RemoteIP|
|TunnelEgressPackets|Yes|Pacotes de Saída do Túnel|de palavras|Total|Contagem de pacotes de saída de um túnel|Nome de conexão, RemoteIP|
|TúnelIngsBytes|Yes|Bytes de Entrada do Túnel|Bytes|Total|Bytes de entrada de um túnel|Nome de conexão, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Yes|Remoção do Pacote de Erro de Correspondência de TS de Entrada do Túnel|de palavras|Total|Contagem de remoção do pacote de entrada a partir do erro de correspondência do seletor de tráfego de um túnel|Nome de conexão, RemoteIP|
|Pacotes de escavação|Yes|Pacotes de Entrada de Túnel|de palavras|Total|Contagem de pacotes de entrada de um túnel|Nome de conexão, RemoteIP|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Yes|Hora de ida e volta para Pings a um VM|MilliSeconds|Média|Tempo de ida e volta para Pings enviado para um VM de destino|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Yes|Pings falhados para um VM|Percentagem|Média|Por cento do número de pings falhados para o total enviado Pings de um VM destino|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnetworkvirtualrouters"></a>Microsoft.Network/virtualRouters

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|PeeringAilability|Yes|Disponibilidade BGP|Percentagem|Média|Disponibilidade de BGP entre pares virtualrouter e remoto|Elemento da rede|


## <a name="microsoftnetworkvpngateways"></a>Microsoft.Network/vpnGateways

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Largura média da largura|Yes|Largura de banda Gateway S2S|BytesPerSecond|Média|Largura de banda média local-local de um gateway in bytes por segundo|Sem Dimensões|
|TunnelAverageBandwidth|Yes|Largura de Banda do Túnel|BytesPerSecond|Média|Largura de banda média de um túnel em bytes por segundo|Nome de conexão, RemoteIP|
|TunnelEgressBytes|Yes|Bytes de Saída do Túnel|Bytes|Total|Bytes de saída de um túnel|Nome de conexão, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Yes|Remoção do Pacote de Erro de Correspondência de TS de Saída do Túnel|de palavras|Total|Contagem de remoção do pacote de saída a partir do erro de correspondência do seletor de tráfego de um túnel|Nome de conexão, RemoteIP|
|TunnelEgressPackets|Yes|Pacotes de Saída do Túnel|de palavras|Total|Contagem de pacotes de saída de um túnel|Nome de conexão, RemoteIP|
|TúnelIngsBytes|Yes|Bytes de Entrada do Túnel|Bytes|Total|Bytes de entrada de um túnel|Nome de conexão, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Yes|Remoção do Pacote de Erro de Correspondência de TS de Entrada do Túnel|de palavras|Total|Contagem de remoção do pacote de entrada a partir do erro de correspondência do seletor de tráfego de um túnel|Nome de conexão, RemoteIP|
|Pacotes de escavação|Yes|Pacotes de Entrada de Túnel|de palavras|Total|Contagem de pacotes de entrada de um túnel|Nome de conexão, RemoteIP|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|entrada|Yes|Mensagens Recebidas|de palavras|Total|A contagem de todos os bem sucedidos enviar chamadas da API. |Sem Dimensões|
|incoming.all.failedrequests|Yes|Todos os pedidos falhados de entrada|de palavras|Total|Total de pedidos falhados de entrada falhada para um centro de notificação|Sem Dimensões|
|incoming.all.requests|Yes|Todos os Pedidos de Entrada|de palavras|Total|Total de pedidos de entrada para um centro de notificação|Sem Dimensões|
|entrada.programado|Yes|Notificações de push agendadas enviadas|de palavras|Total|Notificações de push programadas canceladas|Sem Dimensões|
|incoming.scheduled.cancel|Yes|Notificações de push programadas canceladas|de palavras|Total|Notificações de push programadas canceladas|Sem Dimensões|
|instalação.todos|Yes|Operações de Gestão de Instalações|de palavras|Total|Operações de Gestão de Instalações|Sem Dimensões|
|instalação.excluir|Yes|Excluir operações de instalação|de palavras|Total|Excluir operações de instalação|Sem Dimensões|
|instalação.get|Yes|Obter Operações de Instalação|de palavras|Total|Obter Operações de Instalação|Sem Dimensões|
|instalação.patch|Yes|Operações de instalação de remendos|de palavras|Total|Operações de instalação de remendos|Sem Dimensões|
|instalação.upsert|Yes|Criar ou atualizar operações de instalação|de palavras|Total|Criar ou atualizar operações de instalação|Sem Dimensões|
|notificationhub.pushes|Yes|Todas as notificações de saída|de palavras|Total|Todas as notificações de saída do centro de notificação|Sem Dimensões|
|outgoing.allpns.badorexpiredchannel|Yes|Erros do Canal Mau ou Expirados|de palavras|Total|A contagem de impulsos que falharam porque o canal/token/registrationId no registo expirou ou inválido.|Sem Dimensões|
|outgoing.allpns.channelerror|Yes|Erros do Canal|de palavras|Total|A contagem de impulsos que falharam porque o canal era inválido não estava associado à aplicação correta estrangulada ou expirada.|Sem Dimensões|
|outgoing.allpns.invalidpayload|Yes|Erros de carga útil|de palavras|Total|A contagem de impulsos que falharam porque o PNS devolveu um erro de carga útil.|Sem Dimensões|
|outgoing.allpns.pnserror|Yes|Erros do sistema de notificação externa|de palavras|Total|A contagem de impulsos que falharam porque havia um problema de comunicação com o PNS (exclui problemas de autenticação).|Sem Dimensões|
|outgoing.allpns.sucesso|Yes|Notificações bem sucedidas|de palavras|Total|A contagem de todas as notificações bem sucedidas.|Sem Dimensões|
|outgoing.apns.badchannel|Yes|Erro do Canal Mau da APNS|de palavras|Total|A contagem de impulsos que falharam porque o token é inválido (código de estado DA APNS: 8).|Sem Dimensões|
|outgoing.apns.expiredchannel|Yes|Erro do Canal caducado da APNS|de palavras|Total|A contagem de fichas que foram invalidadas pelo canal de feedback da APNS.|Sem Dimensões|
|outgoing.apns.invalidcredentis|Yes|Erros de Autorização da APNS|de palavras|Total|A contagem de impulsos que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Sem Dimensões|
|outgoing.apns.invalidnotificationsize|Yes|Erro de tamanho de notificação inválido da APNS|de palavras|Total|A contagem de impulsos que falharam porque a carga era demasiado grande (código de estado DA APNS: 7).|Sem Dimensões|
|outgoing.apns.pnserror|Yes|Erros da APNS|de palavras|Total|A contagem de impulsos que falhou devido a erros de comunicação com a APNS.|Sem Dimensões|
|outgoing.apns.sucesso|Yes|Notificações bem sucedidas da APNS|de palavras|Total|A contagem de todas as notificações bem sucedidas.|Sem Dimensões|
|outgoing.gcm.authenticationeror|Yes|Erros de autenticação GCM|de palavras|Total|A contagem de impulsos que falharam porque o PNS não aceitou as credenciais fornecidas as credenciais estão bloqueadas ou o SenderId não está corretamente configurado na app (resultado GCM: MismatchedSenderId).|Sem Dimensões|
|outgoing.gcm.badchannel|Yes|Erro do Canal Mau gCM|de palavras|Total|A contagem de impulsos que falhou porque o registoId no registo não foi reconhecido (resultado GCM: Registo Inválido).|Sem Dimensões|
|outgoing.gcm.expiredchannel|Yes|Erro do canal expirado GCM|de palavras|Total|A contagem de impulsos que falharam porque o registoId no registo expirou (resultado GCM: Não Registado).|Sem Dimensões|
|outgoing.gcm.invalidcredentis|Yes|Erros de autorização GCM (Credenciais Inválidas)|de palavras|Total|A contagem de impulsos que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Sem Dimensões|
|outgoing.gcm.invalidnotificationformat|Yes|Formato de notificação inválida GCM|de palavras|Total|A contagem de impulsos que falharam porque a carga não foi formatada corretamente (resultado GCM: InvalidDataKey ou InvalidTtl).|Sem Dimensões|
|outgoing.gcm.invalidnotificationsize|Yes|Erro de tamanho de notificação inválido GCM|de palavras|Total|A contagem de impulsos que falharam porque a carga era demasiado grande (resultado GCM: MessageTooBig).|Sem Dimensões|
|outgoing.gcm.pnserror|Yes|Erros do GCM|de palavras|Total|A contagem de impulsos que falhou devido a erros de comunicação com gCM.|Sem Dimensões|
|outgoing.gcm.sucesso|Yes|Notificações bem sucedidas da GCM|de palavras|Total|A contagem de todas as notificações bem sucedidas.|Sem Dimensões|
|outgoing.gcm.throttled|Yes|Notificações throttled GCM|de palavras|Total|A contagem de impulsos que falharam porque a GCM estrangulou esta aplicação (código de estado GCM: 501-599 ou resultado:Indisponível).|Sem Dimensões|
|outgoing.gcm.canalil errado|Yes|Erro do canal errado gCM|de palavras|Total|A contagem de impulsos que falharam porque o registroId no registo não está associado à aplicação atual (resultado GCM: InvalidPackageName).|Sem Dimensões|
|outgoing.mpns.authenticationerror|Yes|Erros de Autenticação MPNS|de palavras|Total|A contagem de impulsos que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Sem Dimensões|
|outgoing.mpns.badchannel|Yes|Erro do Canal Mau da MPNS|de palavras|Total|A contagem de impulsos que falhou porque o ChannelURI no registo não foi reconhecido (estatuto MPNS: 404 não encontrado).|Sem Dimensões|
|outgoing.mpns.channeldisconnected|Yes|Canal MPNS desligado|de palavras|Total|A contagem de impulsos que falhou porque o ChannelURI no registo foi desligado (estado MPNS: 412 não encontrado).|Sem Dimensões|
|outgoing.mpns.drop|Yes|Notificações retiradas do MPNS|de palavras|Total|A contagem de impulsos que foram deixados por MPNS (cabeçalho de resposta MPNS: X-NotificationStatus: QueueFull ou Suprimido).|Sem Dimensões|
|outgoing.mpns.invalidcredentis|Yes|Credenciais inválidas da MPNS|de palavras|Total|A contagem de impulsos que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Sem Dimensões|
|outgoing.mpns.invalidnotificationformat|Yes|Formato de Notificação Inválida mpns|de palavras|Total|A contagem de impulsos que falharam porque a carga útil da notificação era demasiado grande.|Sem Dimensões|
|outgoing.mpns.pnserror|Yes|Erros do MPNS|de palavras|Total|A contagem de impulsos que falhou devido a erros de comunicação com MPNS.|Sem Dimensões|
|outgoing.mpns.sucesso|Yes|Notificações bem sucedidas da MPNS|de palavras|Total|A contagem de todas as notificações bem sucedidas.|Sem Dimensões|
|outgoing.mpns.throttled|Yes|Notificações throttled MPNS|de palavras|Total|A contagem de impulsos que falhou porque a MPNS está a estrangular esta aplicação (WNS MPNS: 406 Não Aceitável).|Sem Dimensões|
|outgoing.wns.authenticationerror|Yes|Erros de autenticação WNS|de palavras|Total|Notificação não entregue devido a erros de comunicação com credenciais inválidas do Windows Live ou token errado.|Sem Dimensões|
|outgoing.wns.badchannel|Yes|Erro do canal WNS|de palavras|Total|A contagem de impulsos que falharam porque o ChannelURI no registo não foi reconhecido (estatuto WNS: 404 não encontrado).|Sem Dimensões|
|outgoing.wns.channeldisconnected|Yes|Canal WNS desligado|de palavras|Total|A notificação foi retirada porque o ChannelURI no registo é acelerado (cabeçalho de resposta WNS: X-WNS-DeviceConnectionStatus: desligado).|Sem Dimensões|
|outgoing.wns.channelthrottled|Yes|Canal WNS Acelerado|de palavras|Total|A notificação foi retirada porque o ChannelURI no registo é acelerado (cabeçalho de resposta WNS: X-WNS-NotificationStatus:channelThrottled).|Sem Dimensões|
|outgoing.wns.drop|Yes|Notificações retiradas da WNS|de palavras|Total|A notificação foi retirada porque o ChannelURI no registo é acelerado (X-WNS-NotificationStatus: caiu, mas não X-WNS-DeviceConnectionStatus: desligado).|Sem Dimensões|
|outgoing.wns.expiredchannel|Yes|Erro do canal expirado da WNS|de palavras|Total|A contagem de impulsos que falharam porque o ChannelURI expirou (estado WNS: 410 Gone).|Sem Dimensões|
|outgoing.wns.invalidcredentis|Yes|Erros de autorização WNS (Credenciais Inválidas)|de palavras|Total|A contagem de impulsos que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas. (O Windows Live não reconhece as credenciais).|Sem Dimensões|
|outgoing.wns.invalidnotificationformat|Yes|Formato de notificação inválida da WNS|de palavras|Total|O formato da notificação é inválido (estado WNS: 400). Note que a WNS não rejeita todas as cargas inválidas.|Sem Dimensões|
|outgoing.wns.invalidnotificationsize|Yes|Erro de tamanho de notificação inválido da WNS|de palavras|Total|A carga útil da notificação é demasiado grande (estado WNS: 413).|Sem Dimensões|
|outgoing.wns.invalidtoken|Yes|Erros de autorização da WNS (token inválido)|de palavras|Total|O token fornecido à WNS não é válido (estado WNS: 401 Não Autorizado).|Sem Dimensões|
|outgoing.wns.pnserror|Yes|Erros do WNS|de palavras|Total|Notificação não entregue devido a erros de comunicação com a WNS.|Sem Dimensões|
|outgoing.wns.sucesso|Yes|Notificações bem sucedidas da WNS|de palavras|Total|A contagem de todas as notificações bem sucedidas.|Sem Dimensões|
|outgoing.wns.throttled|Yes|Notificações throttled WNS|de palavras|Total|A contagem de impulsos que falharam porque a WNS está a estrangular esta aplicação (estado WNS: 406 Não Aceitável).|Sem Dimensões|
|outgoing.wns.tokenproviderunreachable|Yes|Erros de autorização WNS (inacessíveis)|de palavras|Total|O Windows Live não é acessível.|Sem Dimensões|
|outgoing.wns.wrongtoken|Yes|Erros de autorização da WNS (Ficha Errada)|de palavras|Total|O token fornecido à WNS é válido, mas para outra aplicação (estatuto WNS: 403 Proibido). Isto pode acontecer se o ChannelURI no registo estiver associado a outra aplicação. Verifique se a aplicação do cliente está associada à mesma app cujas credenciais estão no centro de notificação.|Sem Dimensões|
|registro.todos|Yes|Operações de Registo|de palavras|Total|A contagem de todas as operações de registo bem sucedidas (criações atualiza consultas e supressões). |Sem Dimensões|
|registration.create|Yes|Criar Operações de Criação de Registos|de palavras|Total|A contagem de todas as criações de registo bem sucedidas.|Sem Dimensões|
|registration.delete|Yes|Operações de Eliminação de Registos|de palavras|Total|A contagem de todas as supressões de registo bem sucedidas.|Sem Dimensões|
|registration.get|Yes|Operações de Leitura de Registo|de palavras|Total|A contagem de todas as consultas de registo bem sucedidas.|Sem Dimensões|
|registration.update|Yes|Operações de Atualização de Registo|de palavras|Total|A contagem de todas as atualizações de registo bem sucedidas.|Sem Dimensões|
|agendado.pendente|Yes|Notificações Agendadas Pendentes|de palavras|Total|Notificações Agendadas Pendentes|Sem Dimensões|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|memória Average_% disponível|Yes|% Memória Disponível|de palavras|Média|memória Average_% disponível|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|espaço de troca disponível Average_%|Yes|% Espaço de troca disponível|de palavras|Média|espaço de troca disponível Average_%|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% bytes comprometidos em uso|Yes|% Bytes comprometidos em uso|de palavras|Média|Average_% bytes comprometidos em uso|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% tempo do DPC|Yes|% tempo DPC|de palavras|Média|Average_% tempo do DPC|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Inodes Average_% Grátis|Yes|% Inodes grátis|de palavras|Média|Inodes Average_% Grátis|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% Espaço Livre|Yes|% Espaço Livre|de palavras|Média|Average_% Espaço Livre|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% tempo inativo|Yes|% de Tempo Inativo|de palavras|Média|Average_% tempo inativo|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% Tempo de interrupção|Yes|% Tempo de interrupção|de palavras|Média|Average_% Tempo de interrupção|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% IO Tempo de espera|Yes|% IO Tempo de Espera|de palavras|Média|Average_% IO Tempo de espera|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% tempo agradável|Yes|% Tempo agradável|de palavras|Média|Average_% tempo agradável|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% Tempo Privilegiado|Yes|% Tempo Privilegiado|de palavras|Média|Average_% Tempo Privilegiado|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|tempo de processador Average_%|Yes|% de Tempo do Processador|de palavras|Média|tempo de processador Average_%|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|inodes Average_% usados|Yes|% Inodes usados|de palavras|Média|inodes Average_% usados|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% Memória Usada|Yes|% Memória Usada|de palavras|Média|Average_% Memória Usada|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% espaço usado|Yes|% Espaço Usado|de palavras|Média|Average_% espaço usado|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% espaço de troca usado|Yes|% Espaço de troca usado|de palavras|Média|Average_% espaço de troca usado|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_% tempo de utilização|Yes|% Tempo de Utilização|de palavras|Média|Average_% tempo de utilização|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Available MBytes|Yes|MBytes Disponíveis|de palavras|Média|Average_Available MBytes|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|memória Average_Available MBytes|Yes|Memória MBytes disponível|de palavras|Média|memória Average_Available MBytes|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|troca de MBytes Average_Available|Yes|Troca de MBytes disponível|de palavras|Média|troca de MBytes Average_Available|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Avg. de Disco seg/Leitura|Yes|Avg. Disco seg/Ler|de palavras|Média|Average_Avg. de Disco seg/Leitura|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Avg. Disco seg/Transferência|Yes|Avg. Disco seg/Transferência|de palavras|Média|Average_Avg. Disco seg/Transferência|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Avg. de Disco seg/Escrita|Yes|Avg. Disco seg/Write|de palavras|Média|Average_Avg. de Disco seg/Escrita|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Bytes Recebido/seg|Yes|Bytes Recebidos/seg|de palavras|Média|Average_Bytes Recebido/seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Bytes Enviado/seg|Yes|Bytes Enviados/seg|de palavras|Média|Average_Bytes Enviado/seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Bytes Total/seg|Yes|Bytes Total/seg|de palavras|Média|Average_Bytes Total/seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|comprimento da fila do disco Average_Current|Yes|Comprimento atual da fila do disco|de palavras|Média|comprimento da fila do disco Average_Current|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Disk Ler Bytes/seg|Yes|Discos ler Bytes/seg|de palavras|Média|Average_Disk Ler Bytes/seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Disk Leituras/seg|Yes|Leituras/seg de disco|de palavras|Média|Average_Disk Leituras/seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Transferências Average_Disk/seg|Yes|Transferências de discos/seg|de palavras|Média|Transferências Average_Disk/seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Disk Escrever Bytes/seg|Yes|Discos De Escrita Bytes/seg|de palavras|Média|Average_Disk Escrever Bytes/seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Disk Escreve/Seg|Yes|Escritas/seg de disco|de palavras|Média|Average_Disk Escreve/Seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Free Megabytes|Yes|Megabytes grátis|de palavras|Média|Average_Free Megabytes|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|memória física Average_Free|Yes|Memória Física Gratuita|de palavras|Média|memória física Average_Free|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|espaço Average_Free em arquivos de paging|Yes|Espaço gratuito em ficheiros de paging|de palavras|Média|espaço Average_Free em arquivos de paging|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|memória virtual Average_Free|Yes|Memória Virtual Gratuita|de palavras|Média|memória virtual Average_Free|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Logical Discos Bytes/seg|Yes|Bytes/seg de disco lógico|de palavras|Média|Average_Logical Discos Bytes/seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Page Leituras/seg|Yes|Leituras de página/seg|de palavras|Média|Average_Page Leituras/seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Page Escreve/Seg|Yes|Escritas/seg de página|de palavras|Média|Average_Page Escreve/Seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Pages/seg|Yes|Páginas/seg|de palavras|Média|Average_Pages/seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Pct Tempo Privilegiado|Yes|Pct Tempo Privilegiado|de palavras|Média|Average_Pct Tempo Privilegiado|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Pct tempo de utilizador|Yes|Tempo de utilizador do Pct|de palavras|Média|Average_Pct tempo de utilizador|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Physical Discos Bytes/seg|Yes|Bytes/seg de disco físico|de palavras|Média|Average_Physical Discos Bytes/seg|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Processes|Yes|Processos|de palavras|Média|Average_Processes|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|comprimento da fila Average_Processor|Yes|Comprimento da fila do processador|de palavras|Média|comprimento da fila Average_Processor|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Size armazenado em ficheiros de paging|Yes|Tamanho armazenado em ficheiros de paging|de palavras|Média|Average_Size armazenado em ficheiros de paging|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Total Bytes|Yes|Total Bytes|de palavras|Média|Average_Total Bytes|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Total Bytes Recebidos|Yes|Total de bytes recebidos|de palavras|Média|Average_Total Bytes Recebidos|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|bytes de Average_Total transmitidos|Yes|Total de bytes transmitidos|de palavras|Média|bytes de Average_Total transmitidos|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Colisões Average_Total|Yes|Colisões totais|de palavras|Média|Colisões Average_Total|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|pacotes de Average_Total recebidos|Yes|Total de pacotes recebidos|de palavras|Média|pacotes de Average_Total recebidos|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|pacotes de Average_Total transmitidos|Yes|Total de pacotes transmitidos|de palavras|Média|pacotes de Average_Total transmitidos|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Total Erros Rx|Yes|Erros Rx totais|de palavras|Média|Average_Total Erros Rx|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Total Tx Erros|Yes|Erros Tx totais|de palavras|Média|Average_Total Tx Erros|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Uptime|Yes|Tempo de atividade|de palavras|Média|Average_Uptime|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|Yes|Espaço de troca de MBytes usado|de palavras|Média|Average_Used MBytes Swap Space|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|KBytes de Memória Average_Used|Yes|KBytes de Memória Usados|de palavras|Média|KBytes de Memória Average_Used|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|MBytes de Memória Average_Used|Yes|MBytes de memória usados|de palavras|Média|MBytes de Memória Average_Used|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Users|Yes|Utilizadores|de palavras|Média|Average_Users|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Average_Virtual Memória Partilhada|Yes|Memória partilhada virtual|de palavras|Média|Average_Virtual Memória Partilhada|Computador, Nome de Objeto, Nome de Instância, CounterPath, SourceSystem|
|Evento|Yes|Evento|de palavras|Média|Evento|Fonte, EventLog, Computador, EventCategory, EventLevel, EventLevelName, EventID|
|Heartbeat|Yes|Heartbeat|de palavras|Total|Heartbeat|Computador, OSType, Versão, SourceComputerId|
|Atualizar|Yes|Atualizar|de palavras|Média|Atualizar|Computador, Produto, Classificação, UpdateState, Opcional, Aprovado|


## <a name="microsoftpeeringpeerings"></a>Microsoft.Peering/peerings

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|EgressTrafficRate|Yes|Taxa de tráfego de Egress|BitsPerSecond|Média|Taxa de tráfego de egress em bits por segundo|ConnectionId, SessionIp, TrafficClass|
|IngressTrafficRate|Yes|Taxa de tráfego de entrada|BitsPerSecond|Média|Taxa de tráfego ingress em bits por segundo|ConnectionId, SessionIp, TrafficClass|
|SessãoDisponibilidade|Yes|Disponibilidade de Sessão|de palavras|Média|Disponibilidade da sessão de observação|ConnectionId, SessionIp|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft.Peering/peeringServices

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Prefixlatency|Yes|Latência prefixo|Milissegundos|Média|Latência prefixo mediano|PrefixName|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|memory_metric|Yes|Memória (Gen1)|Bytes|Média|Memória. Alcance 0-3 GB para A1, 0-5 GB para A2, 0-10 GB para A3, 0-25 GB para A4, 0-50 GB para A5 e 0-100 GB para A6. Suportado apenas para recursos Power BI Embedded Geração 1.|Sem Dimensões|
|memory_thrashing_metric|Yes|Thrashing de memória (Conjuntos de dados) (Gen1)|Percentagem|Média|Memória média a bater. Suportado apenas para recursos Power BI Embedded Geração 1.|Sem Dimensões|
|qpu_high_utilization_metric|Yes|Utilização elevada do QPU (Gen1)|de palavras|Total|QPU Alta Utilização em último minuto, 1 para alta utilização QPU, caso contrário 0. Suportado apenas para recursos Power BI Embedded Geração 1.|Sem Dimensões|
|Queriaduração|Yes|Duração da consulta (Conjuntos de dados) (Gen1)|Milissegundos|Média|Duração da consulta DAX no último intervalo. Suportado apenas para recursos Power BI Embedded Geração 1.|Sem Dimensões|
|QueryPoolJobQueueLength|Yes|Comprimento da fila de trabalho da piscina de consulta (conjuntos de dados) (Gen1)|de palavras|Média|Número de empregos na fila da piscina de fios de consulta. Suportado apenas para recursos Power BI Embedded Geração 1.|Sem Dimensões|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft.ProjectBabylon/contas

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ScanCancelled|Yes|Scan cancelado|de palavras|Total|Indica o número de exames cancelados.|ResourceId|
|ScanCompleted|Yes|Digitalização Concluída|de palavras|Total|Indica o número de exames concluídos com sucesso.|ResourceId|
|ScanFailed|Yes|Scan falhou|de palavras|Total|Indica que o número de exames falhou.|ResourceId|
|ScanTimeTaken|Yes|Tempo de digitalização tomado|Segundos|Total|Indica o tempo total de digitalização em segundos.|ResourceId|


## <a name="microsoftpurviewaccounts"></a>microsoft.purview/accounts

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ScanCancelled|Yes|Scan cancelado|de palavras|Total|Indica o número de exames cancelados.||
|ScanCompleted|Yes|Digitalização Concluída|de palavras|Total|Indica o número de exames concluídos com sucesso.||
|ScanFailed|Yes|Scan falhou|de palavras|Total|Indica que o número de exames falhou.||
|ScanTimeTaken|Yes|Tempo de digitalização tomado|Segundos|Total|Indica o tempo total de digitalização em segundos.||


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|de palavras|Total|Total de ActiveConnections para Microsoft.Relay.|Nome de Entidade|
|ActiveListeners|No|ActiveListeners|de palavras|Total|Total de ActiveListeners para Microsoft.Relay.|Nome de Entidade|
|BytesTransferido|Yes|BytesTransferido|Bytes|Total|Total bytesTransferred para Microsoft.Relay.|Nome de Entidade|
|ListenerConnections-ClientError|No|ListenerConnections-ClientError|de palavras|Total|ClientError em ListenerConnections para Microsoft.Relay.|Nome de Entidade, OperaçãoResult|
|ListenerConnections-ServerError|No|ListenerConnections-ServerError|de palavras|Total|ServerError em ListenerConnections para Microsoft.Relay.|Nome de Entidade, OperaçãoResult|
|ListenerConnections-Success|No|ListenerConnections-Success|de palavras|Total|Falhas de escuta bem sucedidas para Microsoft.Relay.|Nome de Entidade, OperaçãoResult|
|ListenerConnections-TotalRequests|No|ListenerConnections-TotalRequests|de palavras|Total|Total de EscutasConnections para Microsoft.Relay.|Nome de Entidade|
|OuvinteSEsa ligações|No|OuvinteSEsa ligações|de palavras|Total|Total ListenerDis ligações para Microsoft.Relay.|Nome de Entidade|
|SenderConnections-ClientError|No|SenderConnections-ClientError|de palavras|Total|ClientError em SenderConnections para Microsoft.Relay.|Nome de Entidade, OperaçãoResult|
|SenderConnections-ServerError|No|SenderConnections-ServerError|de palavras|Total|ServerError em SenderConnections para Microsoft.Relay.|Nome de Entidade, OperaçãoResult|
|SenderConnections-Success|No|SenderConnections-Success|de palavras|Total|SenderConnections de sucesso para Microsoft.Relay.|Nome de Entidade, OperaçãoResult|
|SenderConnections-TotalRequests|No|SenderConnections-TotalRequests|de palavras|Total|Total de pedidos de SenderConnections para Microsoft.Relay.|Nome de Entidade|
|SenderDis ligações|No|SenderDis ligações|de palavras|Total|Total de SenderDis ligações para Microsoft.Relay.|Nome de Entidade|


## <a name="microsoftresourcessubscriptions"></a>microsoft.resources/subscrições

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Latência|Yes|Http Incoming Solicita dados de latência|de palavras|Média|Http Incoming Solicita dados de latência|Método, Espaço de Nome, Região de Pedidos,Tip de Recursos, Microsoft.SubscriptionId|
|Trânsito|Yes|Trânsito|de palavras|Média|Tráfego de http|RequestRegion, StatusCode, StatusCodeClass, ResourceType, Namespace, Microsoft.SubscriptionId|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|PesquisaLatency|Yes|Procurar Latência|Segundos|Média|Latência média de pesquisa para o serviço de pesquisa|Sem Dimensões|
|SearchQueriesPerSecond|Yes|Consultas de pesquisa por segundo|CondePerSecond|Média|Consultas de pesquisa por segundo para o serviço de pesquisa|Sem Dimensões|
|ThrottledSearchQueriesPercentage|Yes|Percentagem de consultas de pesquisa aceleradas|Percentagem|Média|Percentagem de consultas de pesquisa que foram estrangulados para o serviço de pesquisa|Sem Dimensões|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|de palavras|Total|Total de conexões ativas para Microsoft.ServiceBus.||
|ActiveMessages|No|Contagem de mensagens ativas numa fila/tópico.|de palavras|Média|Contagem de mensagens ativas numa fila/tópico.|Nome de Entidade|
|LigaçõesClosed|No|Ligações Fechadas.|de palavras|Média|Ligações fechadas para Microsoft.ServiceBus.|Nome de Entidade|
|LigaçõesAsa|No|Ligações Abertas.|de palavras|Média|Ligações abertas para Microsoft.ServiceBus.|Nome de Entidade|
|CPUXNS|No|CPU (precedido)|Percentagem|Máximo|Métrica de utilização do espaço de identificação do espaço de serviço do serviço de serviço. Esta métrica está despricada. Utilize a métrica cpu (NamespaceCpuUsage) em vez disso.|Réplica|
|DeadletteredMesages|No|Contagem de mensagens com letras mortas numa fila/tópico.|de palavras|Média|Contagem de mensagens com letras mortas numa fila/tópico.|Nome de Entidade|
|Entradas|Yes|Mensagens Recebidas|de palavras|Total|Mensagens de entrada para Microsoft.ServiceBus.|Nome de Entidade|
|IncomingRequests|Yes|Pedidos Recebidos|de palavras|Total|Pedidos de entrada para Microsoft.ServiceBus.|Nome de Entidade|
|Mensagens|No|Contagem de mensagens numa Fila/Tópico.|de palavras|Média|Contagem de mensagens numa Fila/Tópico.|Nome de Entidade|
|NamespaceCpuUsage|No|CPU|Percentagem|Máximo|Métrica de utilização do espaço de identificação do espaço de serviço do serviço de serviço.|Réplica|
|NomespaceMemoryUsage|No|Utilização de Memória|Percentagem|Máximo|Métrica de utilização de memória de espaço de nome premium de serviço de serviço de serviço.|Réplica|
|Despesas de Saída|Yes|Mensagens Enviadas|de palavras|Total|Mensagens de saída para Microsoft.ServiceBus.|Nome de Entidade|
|Horários|No|Contagem de mensagens programadas numa fila/tópico.|de palavras|Média|Contagem de mensagens programadas numa fila/tópico.|Nome de Entidade|
|ServerErrors|No|Erros do Servidor.|de palavras|Total|Erros de servidor para Microsoft.ServiceBus.|Nome de Entidade, OperaçãoResult|
|Tamanho|No|Tamanho|Bytes|Média|Tamanho de uma fila/tópico em Bytes.|Nome de Entidade|
|Requess de sucesso|No|Pedidos Com Êxito|de palavras|Total|Total de pedidos bem sucedidos para um espaço de nome|Nome de Entidade, OperaçãoResult|
|ThrottledRequests|No|Pedidos Limitados.|de palavras|Total|Pedidos de aceleração para Microsoft.ServiceBus.|Nome de Entidade, OperaçãoResult|
|UserErrors|No|Erros do Utilizador.|de palavras|Total|Erros do utilizador para Microsoft.ServiceBus.|Nome de Entidade, OperaçãoResult|
|WSXNS|No|Utilização da memória (Preprecada)|Percentagem|Máximo|Métrica de utilização de memória de espaço de nome premium de serviço de serviço de serviço. Esta métrica está prevadida. Utilize a métrica de Utilização da Memória (NamespaceMemoryUsage).|Réplica|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/aplicações

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ActualCpu|No|ActualCpu|de palavras|Média|Uso real do CPU em núcleos milli|Nome de aplicação, nome de serviço, nome codepackage, nome de serviçoReplicaName|
|ActualMemory|No|ActualMemory|Bytes|Média|Utilização real da memória em MB|Nome de aplicação, nome de serviço, nome codepackage, nome de serviçoReplicaName|
|Cpu atribuído|No|Cpu atribuído|de palavras|Média|CPU alocado a este contentor em núcleos milli|Nome de aplicação, nome de serviço, nome codepackage, nome de serviçoReplicaName|
|Roteiro Atribuído|No|Roteiro Atribuído|Bytes|Média|Memória alocada a este recipiente em MB|Nome de aplicação, nome de serviço, nome codepackage, nome de serviçoReplicaName|
|AplicaçãoStatus|No|AplicaçãoStatus|de palavras|Média|Estado da aplicação de malha de tecido de serviço|Nome de aplicação, estado|
|Estatística do Contentor|No|Estatística do Contentor|de palavras|Média|Estado do contentor na aplicação de malha de tecido de serviço|Nome de aplicação, nome de serviço, nome de codepackagename, ServiceReplicaName, Status|
|CpuUtilização|No|CpuUtilização|Percentagem|Média|Utilização da CPU para este contentor em percentagem doCpu atribuído|Nome de aplicação, nome de serviço, nome codepackage, nome de serviçoReplicaName|
|Utilização da memória|No|Utilização da memória|Percentagem|Média|Utilização da CPU para este contentor em percentagem doCpu atribuído|Nome de aplicação, nome de serviço, nome codepackage, nome de serviçoReplicaName|
|RestartCount|No|RestartCount|de palavras|Média|Reiniciar a contagem de um recipiente na aplicação de malha de tecido de serviço|Nome de aplicação, estado, nome de serviço, nome de serviçoReplicaname, CodePackageName|
|ServiceReplicaStatus|No|ServiceReplicaStatus|de palavras|Média|Estado de Saúde de uma réplica de serviço na aplicação de malha de tecido de serviço|Nome de aplicação, estado, nome de serviço, Nome de ServiçoReplicaName|
|ServiceStatus|No|ServiceStatus|de palavras|Média|Estado de Saúde de um serviço na aplicação de malha de tecido de serviço|Nome de aplicação, estado, nome de serviço|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalrService/Signalr

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Contagem de Ligações|Yes|Contagem de Ligação|de palavras|Máximo|A quantidade de ligação do utilizador.|Ponto final|
|InboundTraffic|Yes|Tráfego de Entrada|Bytes|Total|O tráfego de entrada de serviço|Sem Dimensões|
|Contagem de mensagens|Yes|Contagem de mensagens|de palavras|Total|A quantidade total de mensagens.|Sem Dimensões|
|OutboundTraffic|Yes|Tráfego de Saída|Bytes|Total|O tráfego de serviço de saída|Sem Dimensões|
|SystemErrors|Yes|Erros do Sistema|Percentagem|Máximo|A percentagem de erros do sistema|Sem Dimensões|
|UserErrors|Yes|Erros do Utilizador|Percentagem|Máximo|A percentagem de erros do utilizador|Sem Dimensões|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|avg_cpu_percent|Yes|Percentagem média de CPU|Percentagem|Média|Percentagem média de CPU|Sem Dimensões|
|io_bytes_read|Yes|IO bytes ler|Bytes|Média|IO bytes ler|Sem Dimensões|
|io_bytes_written|Yes|IO bytes escritos|Bytes|Média|IO bytes escritos|Sem Dimensões|
|io_requests|Yes|Contagem de pedidos de IO|de palavras|Média|Contagem de pedidos de IO|Sem Dimensões|
|reserved_storage_mb|Yes|Espaço de armazenamento reservado|de palavras|Média|Espaço de armazenamento reservado|Sem Dimensões|
|storage_space_used_mb|Yes|Espaço de armazenamento utilizado|de palavras|Média|Espaço de armazenamento utilizado|Sem Dimensões|
|virtual_core_count|Yes|Contagem de núcleos virtuais|de palavras|Média|Contagem de núcleos virtuais|Sem Dimensões|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servidores/bases de dados

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|active_queries|Yes|Consultas ativas|de palavras|Total|Consultas ativas em todos os grupos de carga de trabalho. Aplica-se apenas a armazéns de dados.|Sem Dimensões|
|allocated_data_storage|Yes|Espaço de dados atribuído|Bytes|Média|Armazenamento de dados alocado. Não aplicável aos armazéns de dados.|Sem Dimensões|
|app_cpu_billed|Yes|App CPU faturada|de palavras|Total|App CPU faturado. Aplica-se a bases de dados sem servidor.|Sem Dimensões|
|app_cpu_percent|Yes|Percentagem de CPU de aplicações|Percentagem|Média|Percentagem de CPU de aplicação. Aplica-se a bases de dados sem servidor.|Sem Dimensões|
|app_memory_percent|Yes|Percentagem de memória de aplicativo|Percentagem|Média|Percentagem de memória de aplicativo. Aplica-se a bases de dados sem servidor.|Sem Dimensões|
|base_blob_size_bytes|Yes|Tamanho de armazenamento de bolha de base|Bytes|Máximo|Tamanho de armazenamento de bolha de base. Aplica-se a bases de dados de hiperescala.|Sem Dimensões|
|blocked_by_firewall|Yes|Bloqueado pela Firewall|de palavras|Total|Bloqueado pela Firewall|Sem Dimensões|
|cache_hit_percent|Yes|Percentagem de impacto de cache|Percentagem|Máximo|Cache atinge percentagem. Aplica-se apenas a armazéns de dados.|Sem Dimensões|
|cache_used_percent|Yes|Percentagem utilizada em cache|Percentagem|Máximo|Percentagem usada em cache. Aplica-se apenas a armazéns de dados.|Sem Dimensões|
|connection_failed|Yes|Ligações com Falhas|de palavras|Total|Ligações com Falhas|Sem Dimensões|
|connection_successful|Yes|Conexões bem sucedidas|de palavras|Total|Conexões bem sucedidas|Sem Dimensões|
|cpu_limit|Yes|Limite do CPU|de palavras|Média|Limite da CPU. Aplica-se a bases de dados baseadas em vCore.|Sem Dimensões|
|cpu_percent|Yes|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Sem Dimensões|
|cpu_used|Yes|CPU usado|de palavras|Média|CPU usado. Aplica-se a bases de dados baseadas em vCore.|Sem Dimensões|
|impasse|Yes|Impasses|de palavras|Total|Impasses. Não aplicável aos armazéns de dados.|Sem Dimensões|
|diff_backup_size_bytes|Yes|Tamanho de armazenamento de backup diferencial|Bytes|Máximo|Tamanho de armazenamento de backup diferencial cumulativo. Aplica-se a bases de dados baseadas em vCore. Não aplicável às bases de dados de hiperescala.|Sem Dimensões|
|dtu_consumption_percent|Yes|Percentagem de DTU|Percentagem|Média|Percentagem de DTU. Aplica-se a bases de dados baseadas em DTU.|Sem Dimensões|
|dtu_limit|Yes|Limite DTU|de palavras|Média|Limite dTU. Aplica-se a bases de dados baseadas em DTU.|Sem Dimensões|
|dtu_used|Yes|DTU usado|de palavras|Média|DTU usado. Aplica-se a bases de dados baseadas em DTU.|Sem Dimensões|
|dwu_consumption_percent|Yes|Percentagem de DWU|Percentagem|Máximo|Percentagem de DWU. Aplica-se apenas a armazéns de dados.|Sem Dimensões|
|dwu_limit|Yes|Limite dwu|de palavras|Máximo|Limite da DWU. Aplica-se apenas a armazéns de dados.|Sem Dimensões|
|dwu_used|Yes|DWU usado|de palavras|Máximo|DWU usado. Aplica-se apenas a armazéns de dados.|Sem Dimensões|
|full_backup_size_bytes|Yes|Tamanho de armazenamento de backup completo|Bytes|Máximo|Tamanho acumulado de armazenamento de backup completo. Aplica-se a bases de dados baseadas em vCore. Não aplicável às bases de dados de hiperescala.|Sem Dimensões|
|local_tempdb_usage_percent|Yes|Percentagem de temporários locais|Percentagem|Média|Percentagem de temperatura local. Aplica-se apenas a armazéns de dados.|Sem Dimensões|
|log_backup_size_bytes|Yes|Registar tamanho de armazenamento de backup|Bytes|Máximo|Tamanho de armazenamento de backup de registo cumulativo. Aplica-se às bases de dados baseadas em vCore e hyperscale.|Sem Dimensões|
|log_write_percent|Yes|Percentagem de IO de registo|Percentagem|Média|Registar percentagem de IO. Não aplicável aos armazéns de dados.|Sem Dimensões|
|memory_usage_percent|Yes|Percentagem de memória|Percentagem|Máximo|Percentagem de memória. Aplica-se apenas a armazéns de dados.|Sem Dimensões|
|physical_data_read_percent|Yes|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|Sem Dimensões|
|queued_queries|Yes|Consultas em fila|de palavras|Total|Consultas em fila em todos os grupos de carga de trabalho. Aplica-se apenas a armazéns de dados.|Sem Dimensões|
|sessions_percent|Yes|Percentagem de sessões|Percentagem|Média|Percentagem de sessões. Não aplicável aos armazéns de dados.|Sem Dimensões|
|snapshot_backup_size_bytes|Yes|Tamanho de armazenamento de backup snapshot|Bytes|Máximo|Tamanho de armazenamento de backup de instantâneo cumulativo. Aplica-se a bases de dados de hiperescala.|Sem Dimensões|
|sqlserver_process_core_percent|Yes|Núcleo de núcleo de processo do SQL Server|Percentagem|Máximo|Utilização da CPU em percentagem do processo DB SQL. Não aplicável aos armazéns de dados.|Sem Dimensões|
|sqlserver_process_memory_percent|Yes|SQL Servidor processa memória por cento|Percentagem|Máximo|Utilização da memória em percentagem do processo DB SQL. Não aplicável aos armazéns de dados.|Sem Dimensões|
|storage|Yes|Espaço de dados utilizado|Bytes|Máximo|Espaço de dados usado. Não aplicável aos armazéns de dados.|Sem Dimensões|
|storage_percent|Yes|Espaço de dados usado por cento|Percentagem|Máximo|Espaço de dados usado por cento. Não aplicável a armazéns de dados ou bases de dados de hiperescala.|Sem Dimensões|
|tempdb_data_size|Yes|Kilobytes do tamanho do ficheiro de dados temporários|de palavras|Máximo|Espaço usado em ficheiros de dados temporários em quilobytes. Não aplicável aos armazéns de dados.|Sem Dimensões|
|tempdb_log_size|Yes|Kilobytes do tamanho do ficheiro de registo temporário|de palavras|Máximo|Espaço usado no ficheiro de registo de transações temporárias em kilobytes. Não aplicável aos armazéns de dados.|Sem Dimensões|
|tempdb_log_used_percent|Yes|Registo de percentagem de temperatura usado|Percentagem|Máximo|Percentagem de espaço usado no ficheiro de registo de transações temporárias. Não aplicável aos armazéns de dados.|Sem Dimensões|
|wlg_active_queries|Yes|Consultas ativas do grupo de carga de trabalho|de palavras|Total|Consultas ativas dentro do grupo de carga de trabalho. Aplica-se apenas a armazéns de dados.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|Yes|Tempo limites de consulta do grupo de carga de trabalho|de palavras|Total|Consultas que têm cronometrado para o grupo de carga de trabalho. Aplica-se apenas a armazéns de dados.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|Yes|Alocação de grupo de carga de trabalho por parte do sistema|Percentagem|Máximo|Percentagem de recursos atribuídos em relação a todo o sistema por grupo de carga de trabalho. Aplica-se apenas a armazéns de dados.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Yes|Alocação de grupo de carga de trabalho por percentagem de recursos da TAMPA|Percentagem|Máximo|Percentagem de recursos atribuídos em relação aos recursos de bonificação especificados por grupo de carga de trabalho. Aplica-se apenas a armazéns de dados.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|Yes|Por cento efetivo do recurso cap|Percentagem|Máximo|Um limite rígido para a percentagem de recursos permitidos para o grupo de carga de trabalho, tendo em conta a percentagem efetiva de recursos min atribuídos a outros grupos de carga de trabalho. Aplica-se apenas a armazéns de dados.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|Yes|Por cento de recursos min eficazes|Percentagem|Máximo|Percentagem mínima de recursos reservados e isolados para o grupo de trabalho, tendo em conta o nível mínimo de serviço. Aplica-se apenas a armazéns de dados.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|Yes|Consultas em fila de grupos de carga|de palavras|Total|Consultas em fila dentro do grupo de carga de trabalho. Aplica-se apenas a armazéns de dados.|WorkloadGroupName, IsUserDefined|
|workers_percent|Yes|Percentagem de trabalhadores|Percentagem|Média|Percentagem de trabalhadores. Não aplicável aos armazéns de dados.|Sem Dimensões|
|xtp_storage_percent|Yes|In-Memory por cento de armazenamento OLTP|Percentagem|Média|In-Memory por cento de armazenamento OLTP. Não aplicável aos armazéns de dados.|Sem Dimensões|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servidores/elásticos

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|allocated_data_storage|Yes|Espaço de dados atribuído|Bytes|Média|Espaço de dados atribuído|Sem Dimensões|
|allocated_data_storage_percent|Yes|Espaço de dados alocado por cento|Percentagem|Máximo|Espaço de dados alocado por cento|Sem Dimensões|
|cpu_limit|Yes|Limite do CPU|de palavras|Média|Limite da CPU. Aplica-se a piscinas elásticas à base de vCore.|Sem Dimensões|
|cpu_percent|Yes|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Sem Dimensões|
|cpu_used|Yes|CPU usado|de palavras|Média|CPU usado. Aplica-se a piscinas elásticas à base de vCore.|Sem Dimensões|
|database_allocated_data_storage|No|Espaço de dados atribuído|Bytes|Média|Espaço de dados atribuído|Base de dadosResourceId|
|database_cpu_limit|No|Limite do CPU|de palavras|Média|Limite do CPU|Base de dadosResourceId|
|database_cpu_percent|No|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|Base de dadosResourceId|
|database_cpu_used|No|CPU usado|de palavras|Média|CPU usado|Base de dadosResourceId|
|database_dtu_consumption_percent|No|Percentagem de DTU|Percentagem|Média|Percentagem de DTU|Base de dadosResourceId|
|database_eDTU_used|No|eDTU usado|de palavras|Média|eDTU usado|Base de dadosResourceId|
|database_log_write_percent|No|Percentagem de IO de registo|Percentagem|Média|Percentagem de IO de registo|Base de dadosResourceId|
|database_physical_data_read_percent|No|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|Base de dadosResourceId|
|database_sessions_percent|No|Percentagem de sessões|Percentagem|Média|Percentagem de sessões|Base de dadosResourceId|
|database_storage_used|No|Espaço de dados utilizado|Bytes|Média|Espaço de dados utilizado|Base de dadosResourceId|
|database_workers_percent|No|Percentagem de trabalhadores|Percentagem|Média|Percentagem de trabalhadores|Base de dadosResourceId|
|dtu_consumption_percent|Yes|Percentagem de DTU|Percentagem|Média|Percentagem de DTU. Aplica-se a piscinas elásticas à base de DTU.|Sem Dimensões|
|eDTU_limit|Yes|limite eDTU|de palavras|Média|limite eDTU. Aplica-se a piscinas elásticas à base de DTU.|Sem Dimensões|
|eDTU_used|Yes|eDTU usado|de palavras|Média|eDTU usado. Aplica-se a piscinas elásticas à base de DTU.|Sem Dimensões|
|log_write_percent|Yes|Percentagem de IO de registo|Percentagem|Média|Percentagem de IO de registo|Sem Dimensões|
|physical_data_read_percent|Yes|Percentagem de ES de Dados|Percentagem|Média|Percentagem de ES de Dados|Sem Dimensões|
|sessions_percent|Yes|Percentagem de sessões|Percentagem|Média|Percentagem de sessões|Sem Dimensões|
|sqlserver_process_core_percent|Yes|Núcleo de núcleo de processo do SQL Server|Percentagem|Máximo|Utilização da CPU em percentagem do processo DB SQL. Aplica-se a piscinas elásticas.|Sem Dimensões|
|sqlserver_process_memory_percent|Yes|SQL Servidor processa memória por cento|Percentagem|Máximo|Utilização da memória em percentagem do processo DB SQL. Aplica-se a piscinas elásticas.|Sem Dimensões|
|storage_limit|Yes|Tamanho máximo dos dados|Bytes|Média|Tamanho máximo dos dados|Sem Dimensões|
|storage_percent|Yes|Espaço de dados usado por cento|Percentagem|Média|Espaço de dados usado por cento|Sem Dimensões|
|storage_used|Yes|Espaço de dados utilizado|Bytes|Média|Espaço de dados utilizado|Sem Dimensões|
|tempdb_data_size|Yes|Kilobytes do tamanho do ficheiro de dados temporários|de palavras|Máximo|Espaço usado em ficheiros de dados temporários em quilobytes.|Sem Dimensões|
|tempdb_log_size|Yes|Kilobytes do tamanho do ficheiro de registo temporário|de palavras|Máximo|Espaço usado no ficheiro de registo de transações temporárias em kilobytes.|Sem Dimensões|
|tempdb_log_used_percent|Yes|Registo de percentagem de temperatura usado|Percentagem|Máximo|Percentagem de espaço usado no ficheiro de registo de transações temporárias|Sem Dimensões|
|workers_percent|Yes|Percentagem de trabalhadores|Percentagem|Média|Percentagem de trabalhadores|Sem Dimensões|
|xtp_storage_percent|Yes|In-Memory por cento de armazenamento OLTP|Percentagem|Média|In-Memory por cento de armazenamento OLTP|Sem Dimensões|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAcontas

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Autenticação|
|Saída|Yes|Saída|Bytes|Total|A quantidade de dados de saída. Este número inclui saída para cliente externo da Azure Storage, bem como saída dentro do Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Autenticação|
|Entrada|Yes|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Autenticação|
|SucessoE2ELatency|Yes|Latência de E2E Com Êxito|Milissegundos|Média|A latência média de ponta a ponta de pedidos bem sucedidos feita a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Autenticação|
|SucessoServerLatency|Yes|Latência do servidor de sucesso|Milissegundos|Média|O tempo médio utilizado para processar um pedido com êxito pelo Armazenamento do Azure. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Autenticação|
|Transações|Yes|Transações|de palavras|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType, GeoType, ApiName, Autenticação|
|Capacidade Usada|No|Capacidade utilizada|Bytes|Média|A quantidade de armazenamento utilizada pela conta de armazenamento. Para as contas de armazenamento standard, é a soma da capacidade utilizada pelos blobs, tabelas, ficheiros e filas. Para contas de armazenamento premium e contas de armazenamento Blob, é o mesmo que BlobCapacity ou FileCapacity.|Sem Dimensões|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAcontas/blobServices

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Autenticação|
|Capacidade blob|No|Capacidade blob|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Blob da conta de armazenamento em bytes.|BlobType, Tier|
|BlobCount|No|Contagem de blobs|de palavras|Média|O número de objetos blob armazenados na conta de armazenamento.|BlobType, Tier|
|BlobProvisionedSize|No|Tamanho provisto blob|Bytes|Média|A quantidade de armazenamento abastado no serviço Blob da conta de armazenamento em bytes.|BlobType, Tier|
|Contagem de contentores|Yes|Contagem de contentores blob|de palavras|Média|O número de contentores na conta de armazenamento.|Sem Dimensões|
|Saída|Yes|Saída|Bytes|Total|A quantidade de dados de saída. Este número inclui saída para cliente externo da Azure Storage, bem como saída dentro do Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Autenticação|
|Capacidade de Indexação|No|Capacidade de Índice|Bytes|Média|A quantidade de armazenamento utilizada pelo índice hierárquico Azure Data Lake Storage Gen2.|Sem Dimensões|
|Entrada|Yes|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Autenticação|
|SucessoE2ELatency|Yes|Latência de E2E Com Êxito|Milissegundos|Média|A latência média de ponta a ponta de pedidos bem sucedidos feita a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Autenticação|
|SucessoServerLatency|Yes|Latência do servidor de sucesso|Milissegundos|Média|O tempo médio utilizado para processar um pedido com êxito pelo Armazenamento do Azure. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Autenticação|
|Transações|Yes|Transações|de palavras|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType, GeoType, ApiName, Autenticação|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAcontas/fileServices

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Autenticação, FileShare|
|Saída|Yes|Saída|Bytes|Total|A quantidade de dados de saída. Este número inclui saída para cliente externo da Azure Storage, bem como saída dentro do Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Autenticação, FileShare|
|Capacidade de Ficheiros|No|Capacidade de Arquivo|Bytes|Média|A quantidade de armazenamento de ficheiros utilizado pela conta de armazenamento.|FileShare|
|Contagem de ficheiros|No|Contagem de ficheiros|de palavras|Média|O número de ficheiros na conta de armazenamento.|FileShare|
|FileShareCapacityQuota|No|Quota de capacidade de partilha de ficheiros|Bytes|Média|O limite superior da quantidade de armazenamento que pode ser usado pelo Azure Files Service em bytes.|FileShare|
|FileShareCount|No|Contagem de partilha de ficheiros|de palavras|Média|O número de ações de ficheiros na conta de armazenamento.|Sem Dimensões|
|FileShareProvisionedIOPS|No|IOPS provisos de ações de ficheiros|Bytes|Média|O número de base de IOPS provisido para a parte de ficheiro premium na conta de armazenamento de ficheiros premium. Este número é calculado com base na dimensão (quota) prevista da capacidade de participação.|FileShare|
|FileShareSnapshotCount|No|Contagem de snapshot de partilha de ficheiros|de palavras|Média|O número de instantâneos presentes na parte do Serviço de Ficheiros da conta de armazenamento.|FileShare|
|FileShareSnapshotSize|No|Tamanho do snapshot de partilha de arquivo|Bytes|Média|A quantidade de armazenamento utilizada pelas fotos no serviço de ficheiros da conta de armazenamento em bytes.|FileShare|
|Entrada|Yes|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Autenticação, FileShare|
|SucessoE2ELatency|Yes|Latência de E2E Com Êxito|Milissegundos|Média|A latência média de ponta a ponta de pedidos bem sucedidos feita a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Autenticação, FileShare|
|SucessoServerLatency|Yes|Latência do servidor de sucesso|Milissegundos|Média|O tempo médio utilizado para processar um pedido com êxito pelo Armazenamento do Azure. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Autenticação, FileShare|
|Transações|Yes|Transações|de palavras|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType, GeoType, ApiName, Autenticação, FileShare|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAcontas/filas Serviços

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Autenticação|
|Saída|Yes|Saída|Bytes|Total|A quantidade de dados de saída. Este número inclui saída para cliente externo da Azure Storage, bem como saída dentro do Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Autenticação|
|Entrada|Yes|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Autenticação|
|Capacidade de Fila|Yes|Capacidade de fila|Bytes|Média|A quantidade de armazenamento de fila utilizada pela conta de armazenamento.|Sem Dimensões|
|Contagem de filas|Yes|Contagem de filas|de palavras|Média|O número de filas na conta de armazenamento.|Sem Dimensões|
|QueueMessageCount|Yes|Contagem de mensagens de fila|de palavras|Média|O número de mensagens de fila não piradas na conta de armazenamento.|Sem Dimensões|
|SucessoE2ELatency|Yes|Latência de E2E Com Êxito|Milissegundos|Média|A latência média de ponta a ponta de pedidos bem sucedidos feita a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Autenticação|
|SucessoServerLatency|Yes|Latência do servidor de sucesso|Milissegundos|Média|O tempo médio utilizado para processar um pedido com êxito pelo Armazenamento do Azure. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Autenticação|
|Transações|Yes|Transações|de palavras|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType, GeoType, ApiName, Autenticação|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAcontas/tableServices

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Percentagem|Média|A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao ter em conta o valor TotalBillableRequests e dividi-lo pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada.|GeoType, ApiName, Autenticação|
|Saída|Yes|Saída|Bytes|Total|A quantidade de dados de saída. Este número inclui saída para cliente externo da Azure Storage, bem como saída dentro do Azure. Como resultado, este número não reflete a saída faturável.|GeoType, ApiName, Autenticação|
|Entrada|Yes|Entrada|Bytes|Total|A quantidade de dados ingresss, em bytes. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure.|GeoType, ApiName, Autenticação|
|SucessoE2ELatency|Yes|Latência de E2E Com Êxito|Milissegundos|Média|A latência média de ponta a ponta de pedidos bem sucedidos feita a um serviço de armazenamento ou à operação API especificada, em milissegundos. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta.|GeoType, ApiName, Autenticação|
|SucessoServerLatency|Yes|Latência do servidor de sucesso|Milissegundos|Média|O tempo médio utilizado para processar um pedido com êxito pelo Armazenamento do Azure. Este valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Autenticação|
|Capacidade de Mesa|Yes|Capacidade de mesa|Bytes|Média|A quantidade de armazenamento de mesa utilizada pela conta de armazenamento.|Sem Dimensões|
|MesaCount|Yes|Contagem de tabelas|de palavras|Média|O número de mesas na conta de armazenamento.|Sem Dimensões|
|TabelaseconagemCount|Yes|Contagem de Entidades de Tabela|de palavras|Média|O número de entidades de mesa na conta de armazenamento.|Sem Dimensões|
|Transações|Yes|Transações|de palavras|Total|O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. Use a dimensão do Tipo de Resposta para o número de diferentes tipos de resposta.|ResponseType, GeoType, ApiName, Autenticação|


## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/caches

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ClientIOPS|Yes|IOPS totais do cliente|de palavras|Média|A taxa de operações de ficheiros de clientes processadas pela Cache.|Sem Dimensões|
|ClientLatency|Yes|Latência média do cliente|Milissegundos|Média|Latência média das operações de ficheiros de clientes para a Cache.|Sem Dimensões|
|ClientLockIOPS|Yes|IOPS de bloqueio de cliente|CondePerSecond|Média|Operações de bloqueio de ficheiros de clientes por segundo.|Sem Dimensões|
|ClientMetadataReadIOPS|Yes|Metadados de Clientes lêem IOPS|CondePerSecond|Média|A taxa de operações de ficheiros de clientes enviadas para a Cache, excluindo as leituras de dados, que não modificam o estado persistente.|Sem Dimensões|
|ClientMetadataWriteIOPS|Yes|Metadados de clientes escrevem IOPS|CondePerSecond|Média|A taxa de operações de ficheiros de clientes enviadas para a Cache, excluindo os dados, que modificam o estado persistente.|Sem Dimensões|
|ClientReadIOPS|Yes|Cliente ler IOPS|CondePerSecond|Média|Cliente lê operações por segundo.|Sem Dimensões|
|ClientReadThroughput|Yes|Produção média de leitura de cache|BytesPerSecond|Média|Cliente leia taxa de transferência de dados.|Sem Dimensões|
|ClientWriteIOPS|Yes|Cliente escrever iOPS|CondePerSecond|Média|Cliente escreve operações por segundo.|Sem Dimensões|
|ClientWriteThroughput|Yes|Produção média de escrita de cache|BytesPerSecond|Média|Cliente escreve taxa de transferência de dados.|Sem Dimensões|
|StorageTargetAsyncWriteThroughput|Yes|Produção de escrita assíncronea de armazenamento|BytesPerSecond|Média|A taxa que o Cache assíncronea escreve dados para um determinado StorageTarget. São escritos oportunistas que não fazem com que os clientes bloqueiem.|StorageTarget|
|StorageTargetFillThroughput|Yes|Produção de preenchimento de armazenamentor|BytesPerSecond|Média|A taxa que o Cache lê os dados do StorageTarget para lidar com uma falha de cache.|StorageTarget|
|StorageTargetHealth|Yes|Saúde alvo de armazenamento|de palavras|Média|Boolean resultados do teste de conectividade entre os Alvos de Cache e Armazenamento.|Sem Dimensões|
|StorageTargetIOPS|Yes|Total de armazenamentoTarget IOPS|de palavras|Média|A taxa de todas as operações de ficheiro que o Cache envia para um determinado StorageTarget.|StorageTarget|
|ArmazenamentoTargetLatency|Yes|Latência do StorageTarget|Milissegundos|Média|A latência média de ida e volta de todas as operações de arquivo que o Cache envia para um StorageTarget partricular.|StorageTarget|
|ArmazenamentoTargetMetadataReadIOPS|Yes|ArmazenamentoTarget Metadados Ler IOPS|CondePerSecond|Média|A taxa de operações de ficheiro que não modificam o estado persistente, e excluindo a operação de leitura, que a Cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetMetadataWriteIOPS|Yes|ArmazenamentoTarget Metadados Escrever IOPS|CondePerSecond|Média|A taxa de operações de ficheiro que modificam o estado persistente e excluindo a operação de escrita, que a Cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetReadAheadThroughput|Yes|ArmazenamentoTarget Ler Antes Produção|BytesPerSecond|Média|A taxa que o Cache oportunisticamente lê dados do StorageTarget.|StorageTarget|
|ArmazenamentoTargetReadIOPS|Yes|ArmazenamentoTarget Ler IOPS|CondePerSecond|Média|A taxa de operações de leitura de ficheiros que a Cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetSyncWriteThroughput|Yes|Produção de escrita sincronizada StorageTarget|BytesPerSecond|Média|A taxa que o Cache escreve sincronizadamente os dados para um determinado StorageTarget. Estas são escritas que fazem com que os clientes bloqueiem.|StorageTarget|
|StorageTargetTotalReadThroughput|Yes|Produção total de leitura do StorageTarget|BytesPerSecond|Média|A taxa total que a Cache lê dados de um determinado StorageTarget.|StorageTarget|
|StorageTargetTotalWriteThroughput|Yes|Produção total de escrita do StorageTarget|BytesPerSecond|Média|A taxa total que o Cache escreve dados para um determinado StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|Yes|ArmazenamentoTarget Escrever IOPS|de palavras|Média|A taxa das operações de escrita de ficheiros que o Cache envia para um determinado StorageTarget.|StorageTarget|
|Tempo de atividade|Yes|Tempo de atividade|de palavras|Média|Boolean resultados do teste de conectividade entre a Cache e o sistema de monitorização.|Sem Dimensões|


## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ServidorSyncSessionResult|Yes|Resultado da sessão de sincronização|de palavras|Média|Métrica que regista um valor de 1 cada vez que o Ponto Final do Servidor completa com sucesso uma Sessão de Sincronização com o Ponto final da Cloud|SyncGroupName, ServerEndpointName, SyncDirection|
|ArmazenamentoSybatchTransferredFileBytes|Yes|Bytes sincronizados|Bytes|Total|Tamanho total do ficheiro transferido para Sincronização|SyncGroupName, ServerEndpointName, SyncDirection|
|ArmazenamentoSyncRecalledNetworkBytesByApplication|Yes|Tamanho de recolha de camadas de nuvem por aplicação|Bytes|Total|Tamanho dos dados recolhidos pela aplicação|SyncGroupName, ServerName, ApplicationName|
|ArmazenamentoSyncRecalledTotalNetworkBytes|Yes|Tamanho de recuperação de nível de nuvem|Bytes|Total|Tamanho dos dados recordados|SyncGroupName, Nome do Servidor|
|ArmazenamentoSyncRecallIOTotalSizeBytes|Yes|Recuperação de camadas de nuvem|Bytes|Total|Tamanho total dos dados recolhidos pelo servidor|ServerName|
|ArmazenamentoSyncRecallThroughputBytesPerSecond|Yes|Produção de recolha de camadas de nuvem|BytesPerSecond|Média|Tamanho do rendimento da recolha de dados|SyncGroupName, Nome do Servidor|
|StorageSyncServerHeartbeat|Yes|Estado online do servidor|de palavras|Máximo|Métrica que regista um valor de 1 cada vez que o servidor resigtered regista com sucesso um batimento cardíaco com o Cloud Endpoint|ServerName|
|ArmazenamentoSyncSyncSessionAppliedFilesCount|Yes|Ficheiros Sincronizados|de palavras|Total|Contagem de Ficheiros sincronizado|SyncGroupName, ServerEndpointName, SyncDirection|
|ArmazenamentoSyncSyncSessionPerItemErrorsCount|Yes|Ficheiros não sincronizados|de palavras|Total|Contagem de ficheiros não sincronizado|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registradosServers

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ServerHeartbeat|Yes|Estado online do servidor|de palavras|Máximo|Métrica que regista um valor de 1 cada vez que o servidor resigtered regista com sucesso um batimento cardíaco com o Cloud Endpoint|ServerResourceId, Nome do Servidor|
|ServerRecallIOTotalSizeBytes|Yes|Recuperação de camadas de nuvem|Bytes|Total|Tamanho total dos dados recolhidos pelo servidor|ServerResourceId, Nome do Servidor|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|Yes|Bytes sincronizados|Bytes|Total|Tamanho total do ficheiro transferido para Sincronização|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionAppliedFilesCount|Yes|Ficheiros Sincronizados|de palavras|Total|Contagem de Ficheiros sincronizado|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Yes|Ficheiros não sincronizados|de palavras|Total|Contagem de ficheiros não sincronizado|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|Yes|Bytes sincronizados|Bytes|Total|Tamanho total do ficheiro transferido para Sincronização|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|Yes|Ficheiros Sincronizados|de palavras|Total|Contagem de Ficheiros sincronizado|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Yes|Ficheiros não sincronizados|de palavras|Total|Contagem de ficheiros não sincronizado|ServerEndpointName, SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|Yes|Pedidos de função falhadas|de palavras|Total|Pedidos de função falhadas|Nome Lógico, PartitionId|
|AMLCalloutInputEvents|Yes|Eventos de Função|de palavras|Total|Eventos de Função|Nome Lógico, PartitionId|
|AMLCalloutRequests|Yes|Pedidos de Função|de palavras|Total|Pedidos de Função|Nome Lógico, PartitionId|
|ConversoresErrors|Yes|Erros de Conversão de Dados|de palavras|Total|Erros de Conversão de Dados|Nome Lógico, PartitionId|
|DeserializaçãoError|Yes|Erros de deserialização de entrada|de palavras|Total|Erros de deserialização de entrada|Nome Lógico, PartitionId|
|DropOrAdjustedEvents|Yes|Eventos fora de ordem|de palavras|Total|Eventos fora de ordem|Nome Lógico, PartitionId|
|Início dos Eventos|Yes|Eventos de entrada antecipada|de palavras|Total|Eventos de entrada antecipada|Nome Lógico, PartitionId|
|Erros|Yes|Erros de tempo de execução|de palavras|Total|Erros de tempo de execução|Nome Lógico, PartitionId|
|InputEventBytes|Yes|Insutos de Eventos|Bytes|Total|Insutos de Eventos|Nome Lógico, PartitionId|
|InputEvents|Yes|Eventos de Entrada|de palavras|Total|Eventos de Entrada|Nome Lógico, PartitionId|
|InputEventsSourcesBacklogged|Yes|Eventos de entrada recuados|de palavras|Máximo|Eventos de entrada recuados|Nome Lógico, PartitionId|
|InputEventsSourcesPerSecond|Yes|Fontes de entrada recebidas|de palavras|Total|Fontes de entrada recebidas|Nome Lógico, PartitionId|
|LateInputEvents|Yes|Eventos de entrada tardia|de palavras|Total|Eventos de entrada tardia|Nome Lógico, PartitionId|
|OutputEvents|Yes|Eventos de saída|de palavras|Total|Eventos de saída|Nome Lógico, PartitionId|
|OutputWatermarkDelaySeconds|Yes|Atraso da marca de água|Segundos|Máximo|Atraso da marca de água|Nome Lógico, PartitionId|
|Utilização de Recursos|Yes|SU % Utilização|Percentagem|Máximo|SU % Utilização|Nome Lógico, PartitionId|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|BuiltinSqlPoolDataProcessedBytes|No|Dados processados (bytes)|Bytes|Total|Quantidade de dados processados por consultas|Sem Dimensões|
|BuiltinSqlPoolLoginAttempts|No|Tentativas de login|de palavras|Total|Contagem de tentativas de login que succedou ou falhou|Resultado|
|BuiltinSqlPoolRequestsEnded|No|Pedidos terminados|de palavras|Total|Contagem de Pedidos que sucederam, falharam ou foram cancelados|Resultado|
|IntegraçãoActivityRunsEndidas|No|A atividade corre terminada|de palavras|Total|Contagem de atividades de integração que sucederam, falharam ou foram canceladas|Resultado, Falha DeType, Atividade, ActivityType, Pipeline|
|IntegraçãoPipelineRunsEnded|No|As corridas de gasodutos terminaram|de palavras|Total|Contagem de gasodutos de integração que conseguiram, falharam ou foram cancelados|Resultado, FalhaType, Pipeline|
|IntegraçãoTriggerRunsEnded|No|Trigger Runs terminou|de palavras|Total|Contagem de gatilhos de integração que conseguiram, falharam ou foram cancelados|Resultado, FalhaType, Gatilho|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|BigDataPoolAllocatedCores|No|vCores atribuídos|de palavras|Máximo|VCores atribuídos para uma Piscina de Faíscas Apache|SubmitterId|
|BigDataPoolAllocatedMemory|No|Memória alocada (GB)|de palavras|Máximo|Memória atribuída para a Piscina de Faíscas de Apach (GB)|SubmitterId|
|BigDataPoolApplicaçõesAtivas|No|Aplicações Apache Spark ativas|de palavras|Máximo|Total ativa apache spark pool applications|Estado do Trabalho|
|BigDataPoolApplicaçõesEndidas|No|Candidaturas de Apache Spark terminadas|de palavras|Total|As candidaturas à piscina de Apache Spark terminaram|JobType, JobResult|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ActiveQueries|No|Consultas ativas|de palavras|Total|As consultas ativas. Utilizando esta métrica não filtrada e não filtrada exibe todas as consultas ativas em execução no sistema|IsUserDefined|
|AdaptiveCacheHitPercent|No|Percentagem de impacto de cache adaptativo|Percentagem|Máximo|Mede a forma como as cargas de trabalho estão a utilizar a cache adaptativa. Utilize esta métrica com a métrica percentual de impacto da cache para determinar se deve escalar para capacidade adicional ou refazer cargas de trabalho para hidratar a cache|Sem Dimensões|
|AdaptiveCacheUsedPercent|No|Percentagem de cache adaptativa utilizada|Percentagem|Máximo|Mede a forma como as cargas de trabalho estão a utilizar a cache adaptativa. Utilize esta métrica com a cache utilizada métrica percentual para determinar se deve escalar para capacidade adicional ou refazer cargas de trabalho para hidratar a cache|Sem Dimensões|
|Ligações|Yes|Ligações|de palavras|Total|Contagem de entradas totais para a piscina SQL|Resultado|
|ConexõesBlockedByFirewall|No|Ligações bloqueadas por firewall|de palavras|Total|Contagem de ligações bloqueadas pelas regras de firewall. Reveja as políticas de controlo de acesso para a sua piscina SQL e monitorize estas ligações se a contagem for elevada|Sem Dimensões|
|CPUPercent|No|Percentagem utilizada pelo CPU|Percentagem|Máximo|Utilização do CPU em todos os nós na piscina SQL|Sem Dimensões|
|DWULimit|No|Limite dwu|de palavras|Máximo|Objetivo de nível de serviço da piscina SQL|Sem Dimensões|
|DWUUSED|No|DWU usado|de palavras|Máximo|Representa uma representação de alto nível de uso em toda a piscina SQL. Medido por limite DE DWU * percentagem de DWU|Sem Dimensões|
|DWUUsedPercent|No|Percentagem utilizada pela DWU|Percentagem|Máximo|Representa uma representação de alto nível de uso em toda a piscina SQL. Medido pela tomada do máximo entre a percentagem de CPU e a percentagem de IO de dados|Sem Dimensões|
|LocalTempDBUsedPercent|No|Percentagem de temporários locais utilizada|Percentagem|Máximo|Utilização temporária local em todos os nós de computação - valores são emitidos a cada cinco minutos|Sem Dimensões|
|MemoryUsedPercent|No|Percentagem de memória usada|Percentagem|Máximo|Utilização da memória em todos os nós na piscina SQL|Sem Dimensões|
|QueuedQueries|No|Consultas em fila|de palavras|Total|Contagem cumulativa de pedidos em fila após o limite máximo de concuência ter sido atingido|IsUserDefined|
|WLGActiveQueries|No|Consultas ativas do grupo de carga de trabalho|de palavras|Total|As consultas ativas dentro do grupo de carga de trabalho. Utilizando esta métrica não filtrada e não filtrada exibe todas as consultas ativas em execução no sistema|IsUserDefined, WorkloadGroup|
|WLGActiveQueriesTimeouts|No|Tempo limites de consulta do grupo de carga de trabalho|de palavras|Total|Consultas para o grupo de trabalho que tem cronometrado. Os intervalos de consulta reportados por esta métrica são apenas uma vez que a consulta tenha começado a executar (não inclui o tempo de espera devido ao bloqueio ou espera de recursos)|IsUserDefined, WorkloadGroup|
|WLGAllocationByEffectiveCapResourcePercent|No|Alocação de grupo de carga de trabalho por percentagem máxima de recursos|Percentagem|Máximo|Apresenta a percentagem de alocação de recursos em relação à percentagem de recursos de limite efetivo por grupo de carga de trabalho. Esta métrica fornece a utilização eficaz do grupo de carga de trabalho|IsUserDefined, WorkloadGroup|
|WLGAllocationBySystemPercent|No|Alocação de grupo de carga de trabalho por parte do sistema|Percentagem|Máximo|A percentagem de alocação de recursos relativos a todo o sistema|IsUserDefined, WorkloadGroup|
|WLGEffectiveCapResourcePercent|No|Por cento efetivo do recurso cap|Percentagem|Máximo|A percentagem efetiva de recursos de capital para o grupo de trabalho. Se houver outros grupos de carga de trabalho com min_percentage_resource > 0, o effective_cap_percentage_resource é reduzido proporcionalmente|IsUserDefined, WorkloadGroup|
|WLGEffectiveMinResourcePercent|No|Por cento de recursos min eficazes|Percentagem|Máximo|A definição eficaz da percentagem de recursos min permitiu considerar o nível de serviço e as definições do grupo de carga de trabalho. O min_percentage_resource eficaz pode ser ajustado mais alto em níveis de serviço mais baixos|IsUserDefined, WorkloadGroup|
|WLGQueuedQueries|No|Consultas em fila de grupos de carga|de palavras|Total|Contagem cumulativa de pedidos em fila após o limite máximo de concuência ter sido atingido|IsUserDefined, WorkloadGroup|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/ambientes

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Yes|Ingress Recebido Bytes|Bytes|Total|Contagem de bytes lidos de todas as fontes do evento|Sem Dimensões|
|IngressReceivedInvalidMesages|Yes|Ingress recebeu mensagens inválidas|de palavras|Total|Contagem de mensagens inválidas lidas de todas as fontes de eventos do centro de eventos do Evento ou IoT|Sem Dimensões|
|IngressReceivedMessages|Yes|Mensagens Recebidas Ingress|de palavras|Total|Contagem de mensagens lidas de todas as fontes de eventos do centro de eventos do Evento ou IoT|Sem Dimensões|
|IngressReceivedMessagesCountLag|Yes|Ingress recebeu recado de mensagens|de palavras|Média|Diferença entre o número de sequência da última mensagem encosa na partição de origem do evento e o número de sequência de mensagens que estão a ser processadas em Ingress|Sem Dimensões|
|IngressReceivedMesstimeLag|Yes|Ingress recebeu recado de tempo de mensagens|Segundos|Máximo|Diferença entre o tempo em que a mensagem é encosa na fonte do evento e o tempo que é processado em Ingress|Sem Dimensões|
|IngressStoredBytes|Yes|Ingress Stored Bytes|Bytes|Total|Tamanho total dos eventos processados com sucesso e disponíveis para consulta|Sem Dimensões|
|IngresssStoredEvents|Yes|Eventos Armazenados ingress|de palavras|Total|Contagem de eventos achatados processados com sucesso e disponíveis para consulta|Sem Dimensões|
|WarmStorageMaxProperties|Yes|Propriedades Max de Armazenamento Quente|de palavras|Máximo|Número máximo de propriedades utilizadas pelo ambiente para S1/S2 SKU e número máximo de propriedades permitidas pela Warm Store para PAYG SKU|Sem Dimensões|
|WarmStorageUsedProperties|Yes|Propriedades usadas de armazenamento quente |de palavras|Máximo|Número de propriedades utilizadas pelo ambiente para S1/S2 SKU e número de propriedades utilizadas pela Warm Store para PAYG SKU|Sem Dimensões|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/ambientes/eventsources

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Yes|Ingress Recebido Bytes|Bytes|Total|Contagem de bytes lidos a partir da fonte do evento|Sem Dimensões|
|IngressReceivedInvalidMesages|Yes|Ingress recebeu mensagens inválidas|de palavras|Total|Contagem de mensagens inválidas lidas a partir da fonte do evento|Sem Dimensões|
|IngressReceivedMessages|Yes|Mensagens Recebidas Ingress|de palavras|Total|Contagem de mensagens lidas a partir da fonte do evento|Sem Dimensões|
|IngressReceivedMessagesCountLag|Yes|Ingress recebeu recado de mensagens|de palavras|Média|Diferença entre o número de sequência da última mensagem encosa na partição de origem do evento e o número de sequência de mensagens que estão a ser processadas em Ingress|Sem Dimensões|
|IngressReceivedMesstimeLag|Yes|Ingress recebeu recado de tempo de mensagens|Segundos|Máximo|Diferença entre o tempo em que a mensagem é encosa na fonte do evento e o tempo que é processado em Ingress|Sem Dimensões|
|IngressStoredBytes|Yes|Ingress Stored Bytes|Bytes|Total|Tamanho total dos eventos processados com sucesso e disponíveis para consulta|Sem Dimensões|
|IngresssStoredEvents|Yes|Eventos Armazenados ingress|de palavras|Total|Contagem de eventos achatados processados com sucesso e disponíveis para consulta|Sem Dimensões|
|WarmStorageMaxProperties|Yes|Propriedades Max de Armazenamento Quente|de palavras|Máximo|Número máximo de propriedades utilizadas pelo ambiente para S1/S2 SKU e número máximo de propriedades permitidas pela Warm Store para PAYG SKU|Sem Dimensões|
|WarmStorageUsedProperties|Yes|Propriedades usadas de armazenamento quente |de palavras|Máximo|Número de propriedades utilizadas pelo ambiente para S1/S2 SKU e número de propriedades utilizadas pela Warm Store para PAYG SKU|Sem Dimensões|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Bytes de leitura de disco|Yes|Bytes de leitura de disco|Bytes|Total|Produção total de disco devido a operações de leitura durante o período de amostragem.|Sem Dimensões|
|Operações de leitura de disco/sec|Yes|Operações de leitura de disco/sec|CondePerSecond|Média|O número médio de operações de leitura de IO no período de amostragem anterior. Note que estas operações podem ser de tamanho variável.|Sem Dimensões|
|Bytes de escrita de disco|Yes|Bytes de escrita de disco|Bytes|Total|Produção total de disco devido a operações de escrita durante o período de amostragem.|Sem Dimensões|
|Operações de escrita de discos/seg|Yes|Operações de escrita de discos/seg|CondePerSecond|Média|O número médio de operações de escrita de IO no período de amostragem anterior. Note que estas operações podem ser de tamanho variável.|Sem Dimensões|
|DiskReadBytesPerSecond|Yes|Discos de leitura bytes/sec|BytesPerSecond|Média|Produção média de disco devido a operações de leitura durante o período de amostragem.|Sem Dimensões|
|DiskReadLatency|Yes|Leitura de disco Latência|Milissegundos|Média|Latência de leitura total. A soma do dispositivo e o núcleo lêem latências.|Sem Dimensões|
|Operações de Leitura de Discos|Yes|Operações de leitura de discos|de palavras|Total|O número de operações de leitura de IO no período de amostragem anterior. Note que estas operações podem ser de tamanho variável.|Sem Dimensões|
|DiskWriteBytesPerSecond|Yes|Discos De Escrita Bytes/Sec|BytesPerSecond|Média|Produção média de disco devido a operações de escrita durante o período de amostragem.|Sem Dimensões|
|DiskWriteLatency|Yes|Latência de escrita de disco|Milissegundos|Média|Latência total de escrita. A soma do dispositivo e o núcleo escrevem latências.|Sem Dimensões|
|DiskWriteOperations|Yes|Operações de escrita de discos|de palavras|Total|O número de operações de escrita de IO no período de amostragem anterior. Note que estas operações podem ser de tamanho variável.|Sem Dimensões|
|MemoryActive|Yes|Memória Ativa|Bytes|Média|A quantidade de memória usada pelo VM no passado pequena janela de tempo. Este é o número "verdadeiro" de quanta memória o VM atualmente precisa. Memória adicional e não uusada pode ser trocada ou balonizada sem impacto no desempenho do hóspede.|Sem Dimensões|
|Memorygranted|Yes|Memória Concedida|Bytes|Média|A quantidade de memória que foi concedida ao VM pelo hospedeiro. A memória não é concedida ao hospedeiro até que seja tocada uma vez e a memória concedida pode ser trocada ou balonizada se o VMkernel precisar da memória.|Sem Dimensões|
|MemóriaSUsed|Yes|Memória utilizada|Bytes|Média|A quantidade de memória da máquina que está a ser utilizada pelo VM.|Sem Dimensões|
|Entrada na Rede|Yes|Entrada na Rede|Bytes|Total|Produção total de rede para tráfego recebido.|Sem Dimensões|
|Saída da Rede|Yes|Saída da Rede|Bytes|Total|Produção total de rede para tráfego transmitido.|Sem Dimensões|
|NetworkInBytesPerSecond|Yes|Rede em Bytes/Sec|BytesPerSecond|Média|Produção média de rede para tráfego recebido.|Sem Dimensões|
|NetworkOutBytesPerSecond|Yes|Network out Bytes/Sec|BytesPerSecond|Média|Produção média de rede para tráfego transmitido.|Sem Dimensões|
|Percentagem da CPU|Yes|Percentagem da CPU|Percentagem|Média|A utilização do CPU. Este valor é reportado com 100% representando todos os núcleos de processadores no sistema. Como exemplo, um VM de duas vias que usa 50% de um sistema de quatro núcleos está a usar completamente dois núcleos.|Sem Dimensões|
|PercentagemCpuReady|Yes|Percentagem CPU Pronta|Milissegundos|Total|Tempo pronto é o tempo gasto à espera que os CPU(s) fiquem disponíveis no intervalo de atualização anterior.|Sem Dimensões|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Ativarequests|Yes|Pedidos Ativos (precotados)|de palavras|Total|Pedidos Ativos|Instância|
|AverageResponseTime|Yes|Tempo médio de resposta (precotado)|Segundos|Média|O tempo médio de tempo necessário para a frente servir pedidos, em segundos.|Instância|
|BytesReceived|Yes|Dados em|Bytes|Total|A largura de banda média usada em todas as extremidades dianteiras, em MiB.|Instância|
|BytesSent|Yes|Data out|Bytes|Total|A largura de banda média usada em toda a frente, em MiB.|Instância|
|CpuPercentage|Yes|Percentagem de CPU|Percentagem|Média|O CPU médio usado em todos os casos da frente.|Instância|
|DiskQueueLength|Yes|Comprimento da fila do disco|de palavras|Média|O número médio de pedidos de leitura e de escrita que estavam na fila no armazenamento. Um comprimento de fila de disco elevado é uma indicação de uma aplicação que pode estar a abrandar devido à i/O excessiva do disco.|Instância|
|Http101|Yes|Http 101|de palavras|Total|A contagem de pedidos resultando num código de estado HTTP 101.|Instância|
|Http2xx|Yes|Http 2xx|de palavras|Total|A contagem de pedidos resultando num código de estado HTTP = 200 mas < 300.|Instância|
|Http3xx|Yes|Http 3xx|de palavras|Total|A contagem de pedidos que resultam num código de estado HTTP = 300 mas < 400.|Instância|
|Http401|Yes|Http 401|de palavras|Total|A contagem de pedidos que resultam em código de estado HTTP 401.|Instância|
|Http403|Yes|Http 403|de palavras|Total|A contagem de pedidos que resultam em código de estado HTTP 403.|Instância|
|Http404|Yes|Http 404|de palavras|Total|A contagem de pedidos que resultam em código de estado HTTP 404.|Instância|
|Http406|Yes|Http 406|de palavras|Total|A contagem de pedidos que resultam em código de estado HTTP 406.|Instância|
|Http4xx|Yes|Http 4xx|de palavras|Total|A contagem de pedidos resultando num código de estado HTTP = 400 mas < 500.|Instância|
|Http5xx|Yes|http Erros do servidor|de palavras|Total|A contagem de pedidos que resultam num código de estado HTTP = 500 mas < 600.|Instância|
|HttpQueueLength|Yes|Comprimento da fila http|de palavras|Média|O número médio de pedidos HTTP que tiveram de sentar-se na fila antes de serem cumpridos. Um comprimento de fila HTTP elevado ou crescente é um sintoma de um plano sob carga pesada.|Instância|
|HttpResponseTime|Yes|Tempo de resposta|Segundos|Média|O tempo que levou para a frente servir pedidos, em segundos.|Instância|
|Grandes Aplicações de Planos de Serviço|Yes|Trabalhadores do plano de aplicação grande|de palavras|Média|Trabalhadores do plano de aplicação grande|Sem Dimensões|
|MediumAppServicePlanInstances|Yes|Trabalhadores do Plano de Serviço de Aplicações Médias|de palavras|Média|Trabalhadores do Plano de Serviço de Aplicações Médias|Sem Dimensões|
|MemóriaPercentage|Yes|Percentagem de Memória|Percentagem|Média|A memória média usada em todos os casos da frente.|Instância|
|Pedidos|Yes|Pedidos|de palavras|Total|O número total de pedidos, independentemente do código de estado HTTP resultante.|Instância|
|Pequenas Aplicações de Planos doAppService|Yes|Trabalhadores do plano de aplicação de pequenas aplicações|de palavras|Média|Trabalhadores do plano de aplicação de pequenas aplicações|Sem Dimensões|
|TotalFrontEnds|Yes|Total de extremidades frontais|de palavras|Média|Total de extremidades frontais|Sem Dimensões|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|CpuPercentage|Yes|Percentagem de CPU|Percentagem|Média|O CPU médio usado em todos os casos da piscina de trabalhadores.|Instância|
|MemóriaPercentage|Yes|Percentagem de Memória|Percentagem|Média|A memória média usada em todos os casos da piscina de trabalhadores.|Instância|
|Trabalhadores disponíveis|Yes|Trabalhadores disponíveis|de palavras|Média|Trabalhadores disponíveis|Sem Dimensões|
|TrabalhadoresTotal|Yes|Total de Trabalhadores|de palavras|Média|Total de Trabalhadores|Sem Dimensões|
|TrabalhadoresUsed|Yes|Trabalhadores Usados|de palavras|Média|Trabalhadores Usados|Sem Dimensões|


## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|BytesReceived|Yes|Dados em|Bytes|Total|A largura de banda média usada em todos os casos do plano.|Instância|
|BytesSent|Yes|Data out|Bytes|Total|A largura de banda de saída média usada em todos os casos do plano.|Instância|
|CpuPercentage|Yes|Percentagem de CPU|Percentagem|Média|O CPU médio usado em todas as instâncias do plano.|Instância|
|DiskQueueLength|Yes|Comprimento da fila do disco|de palavras|Média|O número médio de pedidos de leitura e de escrita que estavam na fila no armazenamento. Um comprimento de fila de disco elevado é uma indicação de uma aplicação que pode estar a abrandar devido à i/O excessiva do disco.|Instância|
|HttpQueueLength|Yes|Comprimento da fila http|de palavras|Média|O número médio de pedidos HTTP que tiveram de sentar-se na fila antes de serem cumpridos. Um comprimento de fila HTTP elevado ou crescente é um sintoma de um plano sob carga pesada.|Instância|
|MemóriaPercentage|Yes|Percentagem de Memória|Percentagem|Média|A memória média usada em todos os casos do plano.|Instância|
|SocketInboundAll|Yes|SocketInboundAll|de palavras|Média|SocketInboundAll|Instância|
|SocketLoopback|Yes|SocketLoopback|de palavras|Média|SocketLoopback|Instância|
|SocketOutboundAll|Yes|SocketOutboundAll|de palavras|Média|SocketOutboundAll|Instância|
|SocketOutboundEstablished|Yes|SocketOutboundEstablished|de palavras|Média|SocketOutboundEstablished|Instância|
|SocketOutboundTimeWait|Yes|SocketOutboundTimeWait|de palavras|Média|SocketOutboundTimeWait|Instância|
|TcpCloseWait|Yes|Espera de fecho de TCP|de palavras|Média|Espera de fecho de TCP|Instância|
|TcpClosing|Yes|Fecho da TCP|de palavras|Média|Fecho da TCP|Instância|
|TcpEstablished|Yes|TCP Estabelecido|de palavras|Média|TCP Estabelecido|Instância|
|TcpFinWait1|Yes|TCP Fin Wait 1|de palavras|Média|TCP Fin Wait 1|Instância|
|TcpFinWait2|Yes|TCP Fin Wait 2|de palavras|Média|TCP Fin Wait 2|Instância|
|TcpLastAck|Yes|TCP Last Ack|de palavras|Média|TCP Last Ack|Instância|
|TcpSynReceived|Yes|Sina TCP recebida|de palavras|Média|Sina TCP recebida|Instância|
|TcpSynsent|Yes|TCP Syn enviado|de palavras|Média|TCP Syn enviado|Instância|
|TcpTimeWait|Yes|Espera de tempo da TCP|de palavras|Média|Espera de tempo da TCP|Instância|


## <a name="microsoftwebsites"></a>Microsoft.Web/sites

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AppConnections|Yes|Ligações|de palavras|Média|O número de tomadas encadernadas existentes na caixa de areia (w3wp.exe e os seus processos infantis). Uma tomada de encaixe é criada chamando APIs()/connect() APIs e permanece até que a referida tomada seja fechada com CloseHandle()/closesocket().|Instância|
|Média MemoryWorkingSet|Yes|Conjunto de trabalho de memória média|Bytes|Média|A quantidade média de memória utilizada pela app, em megabytes (MiB).|Instância|
|AverageResponseTime|Yes|Tempo médio de resposta (precotado)|Segundos|Média|O tempo médio de tempo necessário para a app servir pedidos, em segundos.|Instância|
|BytesReceived|Yes|Dados em|Bytes|Total|A quantidade de largura de banda recebida consumida pela app, no MiB.|Instância|
|BytesSent|Yes|Data out|Bytes|Total|A quantidade de largura de banda de saída consumida pela app, no MiB.|Instância|
|Horário do Cpu|Yes|Hora do CPU|Segundos|Total|A quantidade de CPU consumida pela app, em segundos. Para mais informações sobre esta métrica. Consulte https://docs.microsoft.com/azure/app-service/web-sites-monitor#cpu-time-vs-cpu-percentage (tempo cpu vs percentagem de CPU).|Instância|
|Antiguidades Atuais|Yes|Atuais Assembleias|de palavras|Média|O número atual de Assembléias carregadas em todos os AppDomains nesta aplicação.|Instância|
|FileSystemUsage|Yes|Utilização do sistema de ficheiros|Bytes|Média|Percentagem de quota de sistema de ficheiros consumida pela app.|Sem Dimensões|
|FunExecutionCount|Yes|Contagem de execução de funções|de palavras|Total|Contagem de execução de funções|Instância|
|FunExecutionUnnits|Yes|Unidades de execução de funções|de palavras|Total|Unidades de execução de funções|Instância|
|Gen0Collections|Yes|Coleções de lixo gen 0|de palavras|Total|O número de vezes que a geração 0 objetos são lixo recolhido desde o início do processo da aplicação. Os GCs de maior geração incluem todos os GCs de menor geração.|Instância|
|Gen1Collections|Yes|Coleções de lixo da Gen 1|de palavras|Total|O número de vezes que os objetos de geração 1 são lixo recolhidos desde o início do processo de aplicação. Os GCs de maior geração incluem todos os GCs de menor geração.|Instância|
|Gen2Collections|Yes|Coleções de lixo gen 2|de palavras|Total|O número de vezes que a geração 2 objetos são lixo recolhidos desde o início do processo de aplicação.|Instância|
|Pegas|Yes|N.º de Identificadores|de palavras|Média|O número total de pegas atualmente abertas pelo processo da aplicação.|Instância|
|HealthCheckStatus|Yes|Estado do controlo de saúde|de palavras|Média|Estado do controlo de saúde|Instância|
|Http101|Yes|Http 101|de palavras|Total|A contagem de pedidos resultando num código de estado HTTP 101.|Instância|
|Http2xx|Yes|Http 2xx|de palavras|Total|A contagem de pedidos resultando num código de estado HTTP = 200 mas < 300.|Instância|
|Http3xx|Yes|Http 3xx|de palavras|Total|A contagem de pedidos que resultam num código de estado HTTP = 300 mas < 400.|Instância|
|Http401|Yes|Http 401|de palavras|Total|A contagem de pedidos que resultam em código de estado HTTP 401.|Instância|
|Http403|Yes|Http 403|de palavras|Total|A contagem de pedidos que resultam em código de estado HTTP 403.|Instância|
|Http404|Yes|Http 404|de palavras|Total|A contagem de pedidos que resultam em código de estado HTTP 404.|Instância|
|Http406|Yes|Http 406|de palavras|Total|A contagem de pedidos que resultam em código de estado HTTP 406.|Instância|
|Http4xx|Yes|Http 4xx|de palavras|Total|A contagem de pedidos resultando num código de estado HTTP = 400 mas < 500.|Instância|
|Http5xx|Yes|http Erros do servidor|de palavras|Total|A contagem de pedidos que resultam num código de estado HTTP = 500 mas < 600.|Instância|
|HttpResponseTime|Yes|Tempo de resposta|Segundos|Média|O tempo que a app demorou a servir pedidos, em segundos.|Instância|
|IoOtherBytesPerSecond|Yes|IO Outros Bytes por Segundo|BytesPerSecond|Total|A taxa a que o processo de aplicação está a emitir bytes para operações de I/S que não envolvem dados, como operações de controlo.|Instância|
|IoOtherOperationsPerSecond|Yes|IO Outras Operações por Segundo|BytesPerSecond|Total|A taxa a que o processo de aplicação está a emitir operações de E/S que não são lidas ou escrevem operações.|Instância|
|IoReadBytesPerSecond|Yes|IO Ler Bytes por segundo|BytesPerSecond|Total|A taxa a que o processo de aplicação está a ler bytes das operações de E/S.|Instância|
|IoReadOperationsPerSecond|Yes|IO Ler Operações por segundo|BytesPerSecond|Total|A taxa a que o processo de aplicação está a emitir operações de I/O.|Instância|
|IoWriteBytesPerSecond|Yes|IO Escrever Bytes por segundo|BytesPerSecond|Total|A taxa a que o processo de aplicação está a escrever bytes para operações de I/S.|Instância|
|IoWriteOperationsPerSecond|Yes|IO Escrever Operações por segundo|BytesPerSecond|Total|A taxa a que o processo de aplicação está a emitir operações de I/O de escrita.|Instância|
|MemoryWorkingSet|Yes|Conjunto de trabalho de memória|Bytes|Média|A quantidade atual de memória utilizada pela aplicação, em MiB.|Instância|
|PrivateBytes|Yes|Bytes privados|Bytes|Média|Private Bytes é o tamanho atual, em bytes, de memória que o processo de aplicação alocou que não pode ser partilhado com outros processos.|Instância|
|Pedidos|Yes|Pedidos|de palavras|Total|O número total de pedidos, independentemente do código de estado HTTP resultante.|Instância|
|PedidosInApplicationQueue|Yes|Pedidos na fila de aplicações|de palavras|Média|O número de pedidos na fila de pedidos.|Instância|
|Fios|Yes|Contagem de fios|de palavras|Média|O número de fios atualmente ativos no processo de aplicação.|Instância|
|Total DeDomínios|Yes|Domínios totais de aplicativos|de palavras|Média|O número atual de AppDomains carregado nesta aplicação.|Instância|
|TotalAppDomainsUnloaded|Yes|Total de domínios de aplicações descarregados|de palavras|Média|O número total de AppDomains descarregados desde o início da aplicação.|Instância|


## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AppConnections|Yes|Ligações|de palavras|Média|O número de tomadas encadernadas existentes na caixa de areia (w3wp.exe e os seus processos infantis). Uma tomada de encaixe é criada chamando APIs()/connect() APIs e permanece até que a referida tomada seja fechada com CloseHandle()/closesocket().|Instância|
|Média MemoryWorkingSet|Yes|Conjunto de trabalho de memória média|Bytes|Média|A quantidade média de memória utilizada pela app, em megabytes (MiB).|Instância|
|AverageResponseTime|Yes|Tempo médio de resposta (precotado)|Segundos|Média|O tempo médio de tempo necessário para a app servir pedidos, em segundos.|Instância|
|BytesReceived|Yes|Dados em|Bytes|Total|A quantidade de largura de banda recebida consumida pela app, no MiB.|Instância|
|BytesSent|Yes|Data out|Bytes|Total|A quantidade de largura de banda de saída consumida pela app, no MiB.|Instância|
|Horário do Cpu|Yes|Hora do CPU|Segundos|Total|A quantidade de CPU consumida pela app, em segundos. Para mais informações sobre esta métrica. Consulte https://docs.microsoft.com/azure/app-service/web-sites-monitor#cpu-time-vs-cpu-percentage (tempo cpu vs percentagem de CPU).|Instância|
|Antiguidades Atuais|Yes|Atuais Assembleias|de palavras|Média|O número atual de Assembléias carregadas em todos os AppDomains nesta aplicação.|Instância|
|FileSystemUsage|Yes|Utilização do sistema de ficheiros|Bytes|Média|Percentagem de quota de sistema de ficheiros consumida pela app.|Sem Dimensões|
|FunExecutionCount|Yes|Contagem de execução de funções|de palavras|Total|Contagem de execução de funções|Instância|
|FunExecutionUnnits|Yes|Unidades de execução de funções|de palavras|Total|Unidades de execução de funções|Instância|
|Gen0Collections|Yes|Coleções de lixo gen 0|de palavras|Total|O número de vezes que a geração 0 objetos são lixo recolhido desde o início do processo da aplicação. Os GCs de maior geração incluem todos os GCs de menor geração.|Instância|
|Gen1Collections|Yes|Coleções de lixo da Gen 1|de palavras|Total|O número de vezes que os objetos de geração 1 são lixo recolhidos desde o início do processo de aplicação. Os GCs de maior geração incluem todos os GCs de menor geração.|Instância|
|Gen2Collections|Yes|Coleções de lixo gen 2|de palavras|Total|O número de vezes que a geração 2 objetos são lixo recolhidos desde o início do processo de aplicação.|Instância|
|Pegas|Yes|N.º de Identificadores|de palavras|Média|O número total de pegas atualmente abertas pelo processo da aplicação.|Instância|
|HealthCheckStatus|Yes|Estado do controlo de saúde|de palavras|Média|Estado do controlo de saúde|Instância|
|Http101|Yes|Http 101|de palavras|Total|A contagem de pedidos resultando num código de estado HTTP 101.|Instância|
|Http2xx|Yes|Http 2xx|de palavras|Total|A contagem de pedidos resultando num código de estado HTTP = 200 mas < 300.|Instância|
|Http3xx|Yes|Http 3xx|de palavras|Total|A contagem de pedidos que resultam num código de estado HTTP = 300 mas < 400.|Instância|
|Http401|Yes|Http 401|de palavras|Total|A contagem de pedidos que resultam em código de estado HTTP 401.|Instância|
|Http403|Yes|Http 403|de palavras|Total|A contagem de pedidos que resultam em código de estado HTTP 403.|Instância|
|Http404|Yes|Http 404|de palavras|Total|A contagem de pedidos que resultam em código de estado HTTP 404.|Instância|
|Http406|Yes|Http 406|de palavras|Total|A contagem de pedidos que resultam em código de estado HTTP 406.|Instância|
|Http4xx|Yes|Http 4xx|de palavras|Total|A contagem de pedidos resultando num código de estado HTTP = 400 mas < 500.|Instância|
|Http5xx|Yes|http Erros do servidor|de palavras|Total|A contagem de pedidos que resultam num código de estado HTTP = 500 mas < 600.|Instância|
|HttpResponseTime|Yes|Tempo de resposta|Segundos|Média|O tempo que a app demorou a servir pedidos, em segundos.|Instância|
|IoOtherBytesPerSecond|Yes|IO Outros Bytes por Segundo|BytesPerSecond|Total|A taxa a que o processo de aplicação está a emitir bytes para operações de I/S que não envolvem dados, como operações de controlo.|Instância|
|IoOtherOperationsPerSecond|Yes|IO Outras Operações por Segundo|BytesPerSecond|Total|A taxa a que o processo de aplicação está a emitir operações de E/S que não são lidas ou escrevem operações.|Instância|
|IoReadBytesPerSecond|Yes|IO Ler Bytes por segundo|BytesPerSecond|Total|A taxa a que o processo de aplicação está a ler bytes das operações de E/S.|Instância|
|IoReadOperationsPerSecond|Yes|IO Ler Operações por segundo|BytesPerSecond|Total|A taxa a que o processo de aplicação está a emitir operações de I/O.|Instância|
|IoWriteBytesPerSecond|Yes|IO Escrever Bytes por segundo|BytesPerSecond|Total|A taxa a que o processo de aplicação está a escrever bytes para operações de I/S.|Instância|
|IoWriteOperationsPerSecond|Yes|IO Escrever Operações por segundo|BytesPerSecond|Total|A taxa a que o processo de aplicação está a emitir operações de I/O de escrita.|Instância|
|MemoryWorkingSet|Yes|Conjunto de trabalho de memória|Bytes|Média|A quantidade atual de memória utilizada pela aplicação, em MiB.|Instância|
|PrivateBytes|Yes|Bytes privados|Bytes|Média|Private Bytes é o tamanho atual, em bytes, de memória que o processo de aplicação alocou que não pode ser partilhado com outros processos.|Instância|
|Pedidos|Yes|Pedidos|de palavras|Total|O número total de pedidos, independentemente do código de estado HTTP resultante.|Instância|
|PedidosInApplicationQueue|Yes|Pedidos na fila de aplicações|de palavras|Média|O número de pedidos na fila de pedidos.|Instância|
|Fios|Yes|Contagem de fios|de palavras|Média|O número de fios atualmente ativos no processo de aplicação.|Instância|
|Total DeDomínios|Yes|Domínios totais de aplicativos|de palavras|Média|O número atual de AppDomains carregado nesta aplicação.|Instância|
|TotalAppDomainsUnloaded|Yes|Total de domínios de aplicações descarregados|de palavras|Média|O número total de AppDomains descarregados desde o início da aplicação.|Instância|


## <a name="microsoftwebstaticsites"></a>Microsoft.Web/staticSites

|Metric|Exportável através de Definições de Diagnóstico?|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|BytesSent|Yes|Data out|Bytes|Total|BytesSent|Instância|
|FunErrors|Yes|FunErrors|de palavras|Total|FunErrors|Instância|
|FunçõesHits|Yes|FunçõesHits|de palavras|Total|FunçõesHits|Instância|
|SiteErrors|Yes|SiteErrors|de palavras|Total|SiteErrors|Instância|
|SiteHits|Yes|SiteHits|de palavras|Total|SiteHits|Instância|


## <a name="next-steps"></a>Passos seguintes

- [Leia sobre métricas no Azure Monitor](../data-platform.md)
- [Criar alertas para as métricas](../alerts/alerts-overview.md)
- [Métricas de exportação para armazenamento, Centro de Eventos ou Log Analytics](../essentials/platform-logs-overview.md)
