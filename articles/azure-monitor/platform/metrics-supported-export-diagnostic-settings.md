---
title: Métricas da plataforma Azure Monitor exportáveis através de Definições de Diagnóstico
description: Lista de métricas disponíveis para cada tipo de recurso com o Monitor Azure.
services: azure-monitor
ms.topic: reference
ms.date: 02/10/2020
ms.subservice: metrics
ms.openlocfilehash: 7a75655d1707dd2491065974ed8addc4c2da1a6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661367"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Métricas da plataforma Azure Monitor exportáveis através de Definições de Diagnóstico

O Monitor Azure fornece [métricas](data-platform-metrics.md) da plataforma por padrão sem configuração. Fornece várias formas de interagir com métricas da plataforma, incluindo mapeá-las no portal, acessá-las através da API REST, ou questioná-las usando PowerShell ou CLI. Consulte as [métricas suportadas](metrics-supported.md) para obter uma lista completa de métricas de plataforma atualmente disponíveis com o pipeline métrico consolidado do Azure Monitor. Para consultar e aceder a estas métricas, utilize a [versão api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Outras métricas podem estar disponíveis no portal ou usando APIs legados.

Pode exportar as métricas da plataforma do oleoduto de monitor Azure para outros locais de uma de duas formas.
1. Utilizando [definições](diagnostic-settings.md) de diagnóstico para enviar para Log Analytics, Event Hubs ou Azure Storage.
2. Use as [métricas REST API](https://docs.microsoft.com/rest/api/monitor/metrics/list)

Devido a complexidades no backend do Monitor Azure, nem todas as métricas são exportáveis utilizando configurações de diagnóstico. O quadro abaixo lista que pode e não pode ser exportado com base em definições de diagnóstico.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Alteração do comportamento dos VALORES NULls e Zero 
 
Para as métricas da plataforma que podem ser exportadas através de configurações de diagnóstico, existem algumas métricas para as quais o Azure Monitor interpreta '0s' como 'Nulos'. Isto causou alguma confusão entre os verdadeiros '0' (emitidos por recurso) e interpretado '0s' (Nulos). A partir de **1 de abril de 2020,** as métricas da plataforma exportadas através de definições de diagnóstico deixarão de exportar '0s', a menos que tenham sido verdadeiramente emitidas pelo recurso subjacente. Atenção:

1.  Se eliminar um grupo de recursos ou um recurso específico, os dados métricos dos recursos efetuados deixarão de ser enviados para destinos de exportação de definição de diagnóstico. Ou seja, deixará de aparecer em Centros de Eventos, Contas de Armazenamento e espaços de trabalho de Log Analytics.
2.  Esta melhoria estará disponível em todas as nuvens públicas e privadas.
3.  Esta mudança não terá impacto no comportamento de nenhuma das seguintes experiências: 
   - Registos de recursos da plataforma exportados através de Definições de Diagnóstico
   - Gráfico de métricas no Explorador de Métricas
   - Alerta sobre métricas da plataforma
 
## <a name="metrics-exportable-table"></a>Tabela exportável de métricas 

A tabela contém as seguintes colunas. 
- Exportável através de Definições de Diagnóstico? 
- Efetuado por NULO / 0 
- ResourceType 
- Métrica 
- Nome de exibição métrica
- Unidade 
- AgregadoTipo


> [!NOTE]
> A tabela abaixo pode ter uma barra de pergaminho horizontal na parte inferior. Se acredita que está a perder informação, verifique se a barra de pergaminho é até à esquerda.  


| Exportável através de Definições de Diagnóstico?  | Emite NULLs |  ResourceType  |  Métrica  |  Nome de exibição métrica  |  Unidade  |  AgregadoTipo | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| Sim****  | Não |  Microsoft.AnalysisServices/servidores  |  Preço Corrente mais limpo  |  Memória: Preço Atual Mais Limpo  |  Contagem  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servidores  |  Memória Mais LimpaNão Encolhe  |  Memória: Memória mais limpa não encolhe  |  Bytes  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servidores  |  Memória Mais LimpaEncolhe  |  Memória: Memória mais limpa encolhe  |  Bytes  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servidores  |  CommandPoolBusyThreads  |  Threads: Comando piscina fios ocupados  |  Contagem  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servidores  |  CommandPoolIdleThreads  |  Threads: Linhas ociosas da piscina de comando  |  Contagem  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servidores  |  Comprimento de fila de trabalho de commandpool  |  Comprimento da fila do trabalho da piscina de comando  |  Contagem  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servidores  |  CurrentConnections  |  Ligação: Ligações atuais  |  Contagem  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servidores  |  Sessões de Utilizadores Atuais  |  Sessões de utilizador atuais  |  Contagem  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servidores  |  LongParsingBusyThreads  |  Threads: Longo parsing fios ocupados  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  LongParsingIdleThreads  |  Threads: Longos fios ociosos de análise  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  LongParsingJobQueueLength  |  Threads: Longa análise do comprimento da fila do trabalho  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  mashup_engine_memory_metric  |  Memória do Motor M  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  mashup_engine_private_bytes_metric  |  M Motor Private Bytes  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  mashup_engine_qpu_metric  |  M Motor QPU  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  mashup_engine_virtual_bytes_metric  |  Bytes virtuais do motor M  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  memory_metric  |  Memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  memory_thrashing_metric  |  Degradação de Memória  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  MemoryLimithard  |  Memória: Limite de memória difícil  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  MemoryLimitHigh  |  Memória: Limite de memória alto  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  Limite de memória  |  Memória: Limite de memória baixo  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  MemoryLimitVertiPaq  |  Memória: Limite de memória VertiPaq  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  Uso da Memória  |  Memória: Utilização da memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  private_bytes_metric  |  Bytes Privados  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  ProcessingPoolBusyIOJobThreads  |  Threads: Processing pool busy I/O job threads  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  ProcessingPoolBusyNonIOThreads  |  Threads: Piscina de processamento movimentada fios não-I/O  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  ProcessingPoolIdleIOJobThreads  |  Threads: Processing pool idle I/O job threads  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  ProcessingPoolIdleNonIOThreads  |  Threads: Processamento de fios não-I/O da piscina  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  ProcessingPoolioJobQueueLength  |  Threads: Processamento de piscina I/O comprimento da fila de trabalho  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  ProcessingPoolJobQueueLength  |  Processamento de trabalho de piscina comprimento de fila  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  qpu_metric  |  QPU  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  ConsultaPoolBusyThreads  |  Filas movimentadas da piscina de consulta  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  ConsultaPoolIdleThreads  |  Threads: Filas ociosas da piscina de consulta  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  ConsultaPoolJobQueueLength  |  Threads: Consulta de trabalho na piscina comprimento da fila  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  Quota  |  Memória: Quota  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  Bloco de Quotas  |  Memória: Quota bloqueada  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  RowsConvertedPerSec  |  Processamento: Linhas convertidas por seg  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  LinhasReadPerSec  |  Processamento: Linhas lidas por segundo  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  LinhasWrittenPerSec  |  Processamento: Linhas escritas por seg  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  ShortParsingBusyThreads  |  Threads: Curto parsing fios ocupados  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  ShortParsingIdleThreads  |  Threads: Fios ociosos de análise curta  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  ShortParsingJobQueueLength  |  Threads: Curto período de fila de trabalho de análise  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  SucessoConexsPerSec  |  Conexões bem sucedidas por Sec  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  TotalDefalhas de ligação  |  Falhas totais de ligação  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  TotalDePedidos de Conexão  |  Total de pedidos de ligação  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  VertiPaqNonpaged  |  Memória: VertiPaq Nonpaged  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  VertiPaqPaged  |  Memória: VertiPaq Paged  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  virtual_bytes_metric  |  Bytes Virtuais  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  BackendDura  |  Duração dos pedidos de backend  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ApiManagement/service  |  Capacidade  |  Capacidade  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  Duração  |  Duração global dos pedidos de gateway  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  Eventos EventHubDrop  |  Eventos de EventHub abandonados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  Eventos EventHubRejected  |  Eventos de EventHub rejeitados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  Eventos de Sucesso eventhub  |  Eventos de Sucesso EventHub  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventoSHubThrottledEvents  |  Eventos Throttled EventHub  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventoHubTimedoutEvents  |  Eventos Timed out EventHub  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventHubTotalBytesSent  |  Tamanho dos eventos EventHub  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  Eventos EventHubTotal  |  Eventos Totais eventhub  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventoHubTotalFailedEvents  |  Eventos falhados do EventHub  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  Pedidos Falhados  |  Pedidos de gateway falhados (precatiados)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  Outros Pedidos  |  Outros pedidos de gateway (Preprecated)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  Pedidos  |  Pedidos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  Pedidos bem sucedidos  |  Pedidos de gateway bem sucedidos (precatiados)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  TotalRequests  |  Total de pedidos de gateway (Preprecated)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  Pedidos Não Autorizados  |  Pedidos de gateway não autorizados (Preprecated)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  AppCpuUsagePercentage  |  Percentagem de utilização do CPU da aplicação  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  AppMemoryCommitted  |  Memória de aplicativo atribuída  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  AppMemoryMax  |  Memória de aplicativo Max  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  AppMemoryUsed  |  Memória de aplicativo usada  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  GCPauseTotalCount  |  Contagem de pausas GC  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  GCPauseTotalTime  |  GC Pausa Tempo Total  |  Milissegundos  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  MaxOldGenMemoryPoolBytes  |  Tamanho de dados de geração antiga disponível Max  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  OldGenMemoryPoolBytes  |  Tamanho dos dados da geração antiga  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  OldGenPromotedBytes  |  Promover o tamanho dos dados da geração antiga  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  SystemCpuUsagePercentage  |  Percentagem de utilização do CPU do sistema  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatErrorCount  |  Erro Global tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatReceivedBytes  |  Tomcat Total Recebido Bytes  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatRequestMaxTime  |  Tomcat Request Max Time  |  Milissegundos  |  Máximo | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatRequestTotalCount  |  Tomcat Request Contagem Total  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatRequestTotalTime  |  Tomcat Request Total Times  |  Milissegundos  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatResponseAvgTime  |  Tomcat Request Tempo Médio  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatSentBytes  |  Tomcat Total Bytes Enviados  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatSessionActiveCurrentCurrentCount  |  Tomcat Session Alive Count  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatSessionActiveMaxCount  |  Tomcat Session Max Ative Count  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatSessionAliveMaxTime  |  Tomcat Session Max Alive Time  |  Milissegundos  |  Máximo | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatSessionCreatedCount  |  Tomcat Session Criou a Contagem  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatSessionExpiredCount  |  Contagem expirada da sessão de Tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatSessionRejectedCount  |  Tomcat Session Rejeitou contagem  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  YoungGenPromotedBytes  |  Promover para o tamanho de dados de geração jovem  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Automation/automationAccounts  |  TotalJob  |  Empregototal  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Automation/automationAccounts  |  Corridas total de implantação  |  Corridas de máquinas de implantação de atualização total  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Automation/automationAccounts  |  TotalUpdateDeploymentruns  |  Execuções de implementação total de atualização  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  CoreCount  |  Contagem de núcleos dedicado  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  Criando NodeCount  |  Criando a Contagem do Nó  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  IdleNodeCount  |  Contagem de nóocioso ocioso  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  JobDeleteCompleteEvent  |  Job Delete Complete Events  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  JobDeleteStartEvent  |  Job Delete Start Events  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  Evento Completo jobDisable  |  Desativação de trabalho eventos completos  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  Evento JobDisableStart  |  Eventos de início de desativação de emprego  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  Evento JobStartEvent  |  Eventos de início de trabalho  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  Evento De Terminação de Trabalho  |  Trabalho terminar eventos completos  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  Evento JobTerminateStartEvent  |  Trabalho terminar eventos de início  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  Deixando PoolNodeCount  |  Deixando a contagem do nó da piscina  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  BaixaPrioridadeCoreCount  |  Contagem do núcleo de baixa prioridade  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  OffNodeCount  |  Contagem de nó offline  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  Evento PoolCreate  |  Piscina Criar Eventos  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  PoolDeleteCompleteEvent  |  Pool Eliminar Eventos Completos  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  PoolDeleteStartEvent  |  Pool Delete Eventos de Início  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  PoolResizeCompleteEvent  |  Piscina Redimensionar eventos completos  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  PoolResizeStartEvent  |  Eventos de início de redimensionar piscina  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  Contagem de NodeS pré-empreitada  |  Contagem de nósinos preempted  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  RebootingNodeCount  |  Contagem de nóreinicialização  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  ReimagingNodeCount  |  Contagem de nó de reimaging  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  RunningNodeCount  |  Contagem de nósomas de execução  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  InícioNodeCount  |  Contagem de nósinos insinuantes  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  StartTaskFailedNodeCount  |  Contagem de nófalhado de tarefa inicial  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  Evento TaskComplete  |  Tarefa Completa Eventos  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  TaskFailEvent  |  Eventos de falha de tarefa  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  Evento TaskStartEvent  |  Eventos de início de tarefa  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  TotalLowPriorityNodeCount  |  Contagem de nódeadebaixas  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  TotalNodeCount  |  Contagem de nódedicados dedicado  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  InutilizávelNodeCount  |  Contagem inutilizável de nó  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  EsperaForStartTaskNodeCount  |  Esperando para começar contagem de nó de tarefa  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Núcleos Ativos  |  Núcleos Ativos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Nódosativos Ativos  |  Nódosativos Ativos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Núcleos ociosos  |  Núcleos ociosos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Nódoas ociosas  |  Nódoas ociosas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Trabalho Concluído  |  Trabalho Concluído  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Trabalho Submetido  |  Trabalho Submetido  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Deixando núcleos  |  Deixando núcleos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Deixando nódosos  |  Deixando nódosos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Núcleos Preempted  |  Núcleos Preempted  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Nódosos pré-empreitados  |  Nódosos pré-empreitados  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Percentagem de Utilização de Quotas  |  Percentagem de Utilização de Quotas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Núcleos Totais  |  Núcleos Totais  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Nósodes totais  |  Nósodes totais  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Núcleos Inutilizáveis  |  Núcleos Inutilizáveis  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Nós Inutilizáveis  |  Nós Inutilizáveis  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  Conexão Aceite  |  Conexões Aceites  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  ConexãoActive  |  Conexões Ativas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  Ligação Handled  |  Conexões manuseadas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  CpuUsagePercentageInDouble  |  Percentagem de utilização do CPU  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  IOReadBytes  |  IO Ler Bytes  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  IOWriteBytes  |  IO Write Bytes  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  Limite de memória  |  Limite de memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  Uso da Memória  |  Utilização de Memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  MemoryUsagePercentageinDouble  |  Percentagem de utilização da memória  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  Transações Pendentes  |  Transações Pendentes  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  Blocos Processados  |  Blocos processados  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  Transações Processadas  |  Transações Processadas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  Transações em fila  |  Transações em fila  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  RequestHandled  |  Pedidos tratados  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  Armazenamento  |  Utilização de armazenamento  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits  |  Cache Hits  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits0  |  Cache Hits (Fragmento 0)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits1  |  Cache Hits (Fragmento 1)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits2  |  Cache Hits (Fragmento 2)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits3  |  Cache Hits (Fragmento 3)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits4  |  Cache Hits (Fragmento 4)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits5  |  Cache Hits (Fragmento 5)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits6  |  Cache Hits (Fragmento 6)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits7  |  Cache Hits (Fragmento 7)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits8  |  Cache Hits (Fragmento 8)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits9  |  Cache Hits (Fragmento 9)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheLatency  |  Microsegundos de Latência cache (Pré-visualização)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses  |  Cache Misses  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses0  |  Cache Misses (Fragmento 0)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses1  |  Cache Misses (Fragmento 1)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses2  |  Cache Misses (Fragmento 2)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses3  |  Cache Misses (Fragmento 3)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses4  |  Cache Misses (Fragmento 4)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses5  |  Cache Misses (Fragmento 5)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses6  |  Cache Misses (Fragmento 6)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses7  |  Cache Misses (Fragmento 7)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses8  |  Cache Misses (Fragmento 8)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses9  |  Cache Misses (Fragmento 9)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheLer  |  Leitura de cache  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheRead0  |  Cache Read (Fragmento 0)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheRead1  |  Cache Read (Fragmento 1)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheRead2  |  Cache Read (Fragmento 2)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheRead3  |  Cache Read (Fragmento 3)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheRead4  |  Cache Read (Fragmento 4)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheRead5  |  Cache Read (Fragmento 5)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheRead6  |  Cache Read (Fragmento 6)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheRead7  |  Cache Read (Fragmento 7)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheRead8  |  Cache Read (Fragmento 8)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheRead9  |  Cache Read (Fragmento 9)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheEscrever  |  Cache Write  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheWrite0  |  Cache Write (Fragmento 0)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheWrite1  |  Cache Write (Fragmento 1)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheWrite2  |  Cache Write (Fragmento 2)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheWrite3  |  Cache Write (Fragmento 3)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheWrite4  |  Cache Write (Fragmento 4)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheWrite5  |  Cache Write (Fragmento 5)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheWrite6  |  Cache Write (Fragmento 6)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheWrite7  |  Cache Write (Fragmento 7)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheWrite8  |  Cache Write (Fragmento 8)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheWrite9  |  Cache Write (Fragmento 9)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  clientes conectados  |  Clientes Ligados  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  clientes conectados0  |  Clientes Conectados (Fragmento 0)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  clientes conectados1  |  Clientes Conectados (Fragmento 1)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  clientes conectados2  |  Clientes Conectados (Fragmento 2)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  clientes conectados3  |  Clientes Conectados (Fragmento 3)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  clientes conectados4  |  Clientes Conectados (Fragmento 4)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  clientes conectados5  |  Clientes Conectados (Fragmento 5)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  clientes conectados6  |  Clientes Conectados (Fragmento 6)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  clientes conectados7  |  Clientes Conectados (Shard 7)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  clientes conectados8  |  Clientes Conectados (Fragmento 8)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  clientes conectados9  |  Clientes Conectados (Shard 9)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  erros  |  Erros  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  chaves despejadas  |  Chaves despejadas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  despejadokeys0  |  Chaves despejadas (Fragmento 0)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  despejadoskeys1  |  Chaves despejadas (Fragmento 1)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  despejadoskeys2  |  Chaves despejadas (Fragmento 2)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  despejadoschaves3  |  Chaves despejadas (Fragmento 3)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  despejadoschaves4  |  Chaves despejadas (Fragmento 4)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  despejadoskeys5  |  Chaves despejadas (Fragmento 5)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  despejados6  |  Chaves despejadas (Fragmento 6)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  despejadoskeys7  |  Chaves despejadas (Fragmento 7)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  despejadoschaves8  |  Chaves despejadas (Fragmento 8)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  despejadokeys9  |  Chaves despejadas (Fragmento 9)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  expiradokeys  |  Chaves expiradas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  expiradokeys0  |  Chaves expiradas (Fragmento 0)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  expiradokeys1  |  Chaves expiradas (Fragmento 1)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  expiradokeys2  |  Chaves expiradas (Fragmento 2)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  expiradokeys3  |  Chaves expiradas (Fragmento 3)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  expiradokeys4  |  Chaves expiradas (Fragmento 4)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  expiradokeys5  |  Chaves expiradas (Fragmento 5)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  expiradokeys6  |  Chaves expiradas (Fragmento 6)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  expiradokeys7  |  Chaves expiradas (Fragmento 7)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  expiradokeys8  |  Chaves expiradas (Fragmento 8)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  expiradokeys9  |  Chaves expiradas (Fragmento 9)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  obter comandos  |  Fica  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  getcommands0  |  Recebe (Fragmento 0)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  getcommands1  |  Recebe (Fragmento 1)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  getcommands2  |  Recebe (Fragmento 2)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  getcommands3  |  Recebe (Fragmento 3)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  getcommands4  |  Recebe (Fragmento 4)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  getcommands5  |  Recebe (Fragmento 5)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  getcommands6  |  Recebe (Fragmento 6)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  getcommands7  |  Recebe (Fragmento 7)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  getcommands8  |  Recebe (Fragmento 8)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  getcommands9  |  Recebe (Fragmento 9)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operaçõesPerSecond  |  Operações por segundo  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operaçõesPerSecond0  |  Operações por Segundo (Fragmento 0)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operaçõesPerSecond1  |  Operações por Segundo (Fragmento 1)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operaçõesPerSecond2  |  Operações por Segundo (Fragmento 2)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operaçõesPerSecond3  |  Operações por Segundo (Fragmento 3)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operaçõesPerSecond4  |  Operações por Segundo (Fragmento 4)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operaçõesPerSecond5  |  Operações por Segundo (Fragmento 5)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operaçõesPerSecond6  |  Operações por Segundo (Fragmento 6)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operaçõesPerSecond7  |  Operações por Segundo (Fragmento 7)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operaçõesPerSecond8  |  Operações por Segundo (Fragmento 8)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operaçõesPerSecond9  |  Operações por Segundo (Fragmento 9)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  percentProcessorTime  |  CPU  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  percentProcessorTime0  |  CPU (Fragmento 0)  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  percentProcessorTime1  |  CPU (Fragmento 1)  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  percentProcessorTime2  |  CPU (Fragmento 2)  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  percentProcessorTime3  |  CPU (Fragmento 3)  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  percentProcessorTime4  |  CPU (Fragmento 4)  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  percentProcessorTime5  |  CPU (Fragmento 5)  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  percentProcessorTime6  |  CPU (Fragmento 6)  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  percentProcessorTime7  |  CPU (Fragmento 7)  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  percentProcessorTime8  |  CPU (Fragmento 8)  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  percentProcessorTime9  |  CPU (Fragmento 9)  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  servidorLoad  |  Carga do servidor  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  servidorLoad0  |  Carga do servidor (Fragmento 0)  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  servidorLoad1  |  Carga do servidor (Fragmento 1)  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  servidorLoad2  |  Carga do servidor (Fragmento 2)  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  servidorLoad3  |  Carga do servidor (Fragmento 3)  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  servidorLoad4  |  Carga do servidor (Fragmento 4)  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  servidorLoad5  |  Carga do servidor (Fragmento 5)  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  servidorLoad6  |  Carga do servidor (Fragmento 6)  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  servidorLoad7  |  Carga do servidor (Fragmento 7)  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  servidorLoad8  |  Carga do servidor (Fragmento 8)  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  servidorLoad9  |  Carga do servidor (Fragmento 9)  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands  |  Conjuntos  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands0  |  Conjuntos (Fragmento 0)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands1  |  Conjuntos (Fragmento 1)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands2  |  Conjuntos (Fragmento 2)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands3  |  Conjuntos (Fragmento 3)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands4  |  Conjuntos (Fragmento 4)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands5  |  Conjuntos (Fragmento 5)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands6  |  Conjuntos (Fragmento 6)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands7  |  Conjuntos (Fragmento 7)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands8  |  Conjuntos (Fragmento 8)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands9  |  Conjuntos (Fragmento 9)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  total de comandos processados  |  Total de Operações  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  total de comandos processados0  |  Total de Operações (Fragmento 0)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  total de comandos processados1  |  Total de Operações (Fragmento 1)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  total de comandos processados2  |  Total de Operações (Fragmento 2)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  total de comandos processados3  |  Total de Operações (Fragmento 3)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  total de comandos processados4  |  Total de Operações (Fragmento 4)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  total de comandos processados5  |  Total de Operações (Fragmento 5)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  total de comandos processados6  |  Total de Operações (Fragmento 6)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  total de comandos processados7  |  Total de Operações (Fragmento 7)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  total de comandos processados8  |  Total de Operações (Fragmento 8)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  total de comandos processados9  |  Total de Operações (Fragmento 9)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totaldechaves  |  Chaves totais  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totaldekeys0  |  Chaves totais (Fragmento 0)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totaldekeys1  |  Chaves totais (Fragmento 1)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totaldekeys2  |  Chaves totais (Fragmento 2)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totaldekeys3  |  Chaves totais (Fragmento 3)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totaldekeys4  |  Chaves totais (Fragmento 4)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totaldekeys5  |  Chaves totais (Fragmento 5)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalde6  |  Chaves totais (Fragmento 6)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totaldekeys7  |  Chaves totais (Fragmento 7)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totaldekeys8  |  Chaves totais (Fragmento 8)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totaldekeys9  |  Chaves totais (Fragmento 9)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  memória usada  |  Memória Usada  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  memória usada0  |  Memória Usada (Fragmento 0)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  memória usada1  |  Memória Usada (Fragmento 1)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  memória usada2  |  Memória Usada (Fragmento 2)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  memória usada3  |  Memória Usada (Fragmento 3)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  memória usada4  |  Memória Usada (Fragmento 4)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  memória usada5  |  Memória Usada (Fragmento 5)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  memória usada6  |  Memória Usada (Fragmento 6)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  memória usada7  |  Memória Usada (Fragmento 7)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  memória usada8  |  Memória Usada (Fragmento 8)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  memória usada9  |  Memória Usada (Fragmento 9)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  percentagem de memória usada  |  Percentagem de memória usada  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  memória usadaRss  |  RSS de memória usada  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  memória usadaRss0  |  Memória Usada RSS (Fragmento 0)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  memória usadaRss1  |  Memória Usada RSS (Fragmento 1)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  memória usadaRss2  |  Memória Usada RSS (Fragmento 2)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  memória usadaRss3  |  Memória Usada RSS (Fragmento 3)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  memória usadaRss4  |  Memória Usada RSS (Fragmento 4)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  memória usadaRss5  |  Memória Usada RSS (Fragmento 5)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  memória usadaRss6  |  Memória Usada RSS (Fragmento 6)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  memória usadaRss7  |  Memória Usada RSS (Fragmento 7)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  memória usadaRss8  |  Memória Usada RSS (Fragmento 8)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  memória usadaRss9  |  Memória Usada RSS (Fragmento 9)  |  Bytes  |  Máximo | 
| Não  | Não |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Discos Ler Bytes/Sec  |  Leitura do disco  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Operações de leitura do disco/sec  |  Operações de leitura do disco/sec  |  CondeEmSegundo  |  Média | 
| Não  | Não |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Discos Write Bytes/Sec  |  Escrita de Disco  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Operações de escrita de disco/sec  |  Operações de escrita de disco/sec  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Entrada na Rede  |  Entrada na Rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Saída da Rede  |  Saída da Rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Percentagem da CPU  |  Percentagem da CPU  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.ClassicCompute/virtualMachines  |  Discos Ler Bytes/Sec  |  Leitura do disco  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicCompute/virtualMachines  |  Operações de leitura do disco/sec  |  Operações de leitura do disco/sec  |  CondeEmSegundo  |  Média | 
| Não  | Não |  Microsoft.ClassicCompute/virtualMachines  |  Discos Write Bytes/Sec  |  Escrita de Disco  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicCompute/virtualMachines  |  Operações de escrita de disco/sec  |  Operações de escrita de disco/sec  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicCompute/virtualMachines  |  Entrada na Rede  |  Entrada na Rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicCompute/virtualMachines  |  Saída da Rede  |  Saída da Rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicCompute/virtualMachines  |  Percentagem da CPU  |  Percentagem da CPU  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts  |  Disponibilidade  |  Disponibilidade  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts  |  SuccessE2ELatency  |  Latência de E2E Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts  |  SuccessServerLatency  |  Latência de Servidor Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts  |  Transações  |  Transações  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.ClassicStorage/storageAccounts  |  Capacidade Utilizada  |  Capacidade usada  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Disponibilidade  |  Disponibilidade  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  BlobCapacity  |  Capacidade blob  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  BlobCount  |  Contagem de Bolhas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Contagem de contentores  |  Contagem de contentores blob  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  IndexCapacity  |  Capacidade indexada  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Latência de E2E Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  SuccessServerLatency  |  Latência de Servidor Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Transações  |  Transações  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Disponibilidade  |  Disponibilidade  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Capacidade de Arquivo  |  Capacidade de arquivo  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Contagem de Ficheiros  |  Contagem de Ficheiros  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareCount  |  Contagem de partilha de ficheiros  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareQuota  |  Tamanho da quota de partilha de ficheiros  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Contagem de instantâneos de partilha de ficheiros  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  Tamanho instantâneo de partilha de ficheiros  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Latência de E2E Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  SuccessServerLatency  |  Latência de Servidor Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Transações  |  Transações  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Disponibilidade  |  Disponibilidade  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Capacidade de fila  |  Capacidade de fila  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Contagem de filas  |  Contagem de filas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Contagem de mensagens de fila  |  Contagem de mensagens de fila  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Latência de E2E Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  SuccessServerLatency  |  Latência de Servidor Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Transações  |  Transações  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Disponibilidade  |  Disponibilidade  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Latência de E2E Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  SuccessServerLatency  |  Latência de Servidor Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Capacidade de Mesa  |  Capacidade de Mesa  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Contagem de tabelas  |  Contagem de mesas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Contagem de tabelas  |  Contagem de entidades de mesa  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Transações  |  Transações  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  Chamadas Bloqueadas  |  Chamadas Bloqueadas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  Caracteres Treinados  |  Personagens treinados  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  CaracteresTraduzidos  |  Caracteres traduzidos  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  Erros de cliente  |  Erros do Cliente  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  DataIn  |  Dados em  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  DataOut  |  Dados out  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  Latência  |  Latência  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  Erros de servidor  |  Erros do servidor  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  Duração das sessões de discurso  |  Duração da sessão de discurso  |  Segundos  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  Chamadas bem sucedidas  |  Chamadas bem sucedidas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  Totalcall  |  Total de Chamadas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  Erros Totais  |  Erros Totais  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  TotalTokenCalls  |  Total de chamadas simbólicas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  Total de Transações  |  Total de Transações  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Créditos da CPU consumidos  |  Créditos da CPU consumidos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Créditos cpu restantes  |  Créditos cpu restantes  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Profundidade da Fila do Disco de Dados  |  Profundidade da fila do disco de dados (pré-visualização)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Data Disk Ler Bytes/seg  |  Data Disk Read Bytes/Sec (Pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de leitura de disco de dados/sec  |  Data Disk Ler Operações/Sec (Pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Data Disk Write Bytes/seg  |  Data Disk Write Bytes/Sec (Pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de escrita de disco de dados/sec  |  Operações de escrita de disco de dados/sec (pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Discos Ler Bytes  |  Discos Ler Bytes  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de leitura do disco/sec  |  Operações de leitura do disco/sec  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Bytes de Escrita de Disco  |  Bytes de Escrita de Disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de escrita de disco/sec  |  Operações de escrita de disco/sec  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Fluxos de Entrada  |  Fluxos de Entrada  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Taxa máxima de criação dos fluxos de entrada  |  Taxa máxima de criação dos fluxos de entrada (pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Entrada na Rede  |  Rede Em Billable (Depreciado)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Rede no Total  |  Rede no Total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Saída da Rede  |  Network out Billable (Depreciado)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Rede out Total  |  Rede out Total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Profundidade da Fila do Disco do SO  |  Profundidade da fila do disco osso (pré-visualização)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Os Discos Ler Bytes/seg  |  Os Discos Ler Bytes/Sec (Pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de leitura de disco osso/sec  |  As operações/seg de leitura do disco OS (pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Os Disk Write Bytes/seg  |  Os Disk Write Bytes/Sec (Pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de escrita de disco osso/sec  |  Operações de escrita de disco osso/sec (pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  OS Por Disco QD  |  QD do disco osso (depreciado)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  OS Per Disk Ler Bytes/seg  |  Os Discos Ler Bytes/Sec (Depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  OS por disco ler operações/sec  |  Operações de leitura de disco osso/sec (depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  OS Por Disco Escreva Bytes/seg  |  Os Disk Write Bytes/Sec (Depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  As operações de escrita de OS por disco/sec  |  Operações de Escrita de Disco OS/Sec (Depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Fluxos de saída  |  Fluxos de saída  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Taxa máxima de criação dos fluxos de saída  |  Taxa máxima de criação dos fluxos de saída (pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Por Disco QD  |  QD do disco de dados (depreciado)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Por Disco Ler Bytes/seg  |  Data Disk Read Bytes/Sec (Depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Por disco ler operações/sec  |  Operações de leitura de disco de dados/Sec (Depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Por Disco Escreva Bytes/seg  |  Data Disk Write Bytes/Sec (Depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Por Operações de Escrita de Disco/Sec  |  Operações de Escrita de Disco de Dados/Sec (Depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Percentagem da CPU  |  Percentagem da CPU  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Premium Data Disk Cache Ler Sucesso  |  Vídeo de cache de disco premium (pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Cache de disco de dados premium ler miss  |  Cache de disco de dados premium Leia Miss (Pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Premium OS Disk Cache Ler Sucesso  |  Premium OS Cache Cache Read Hit (Pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Cache de disco premium DO  |  Cache de disco premium OS Leia Miss (Pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Créditos da CPU consumidos  |  Créditos da CPU consumidos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Créditos cpu restantes  |  Créditos cpu restantes  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Profundidade da Fila do Disco de Dados  |  Profundidade da fila do disco de dados (pré-visualização)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Data Disk Ler Bytes/seg  |  Data Disk Read Bytes/Sec (Pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de leitura de disco de dados/sec  |  Data Disk Ler Operações/Sec (Pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Data Disk Write Bytes/seg  |  Data Disk Write Bytes/Sec (Pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de escrita de disco de dados/sec  |  Operações de escrita de disco de dados/sec (pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Discos Ler Bytes  |  Discos Ler Bytes  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de leitura do disco/sec  |  Operações de leitura do disco/sec  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Bytes de Escrita de Disco  |  Bytes de Escrita de Disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de escrita de disco/sec  |  Operações de escrita de disco/sec  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Fluxos de Entrada  |  Fluxos de Entrada  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Taxa máxima de criação dos fluxos de entrada  |  Taxa máxima de criação dos fluxos de entrada (pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Entrada na Rede  |  Rede Em Billable (Depreciado)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Rede no Total  |  Rede no Total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Saída da Rede  |  Network out Billable (Depreciado)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Rede out Total  |  Rede out Total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Profundidade da Fila do Disco do SO  |  Profundidade da fila do disco osso (pré-visualização)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Os Discos Ler Bytes/seg  |  Os Discos Ler Bytes/Sec (Pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de leitura de disco osso/sec  |  As operações/seg de leitura do disco OS (pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Os Disk Write Bytes/seg  |  Os Disk Write Bytes/Sec (Pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de escrita de disco osso/sec  |  Operações de escrita de disco osso/sec (pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  OS Por Disco QD  |  QD do disco osso (depreciado)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  OS Per Disk Ler Bytes/seg  |  Os Discos Ler Bytes/Sec (Depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  OS por disco ler operações/sec  |  Operações de leitura de disco osso/sec (depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  OS Por Disco Escreva Bytes/seg  |  Os Disk Write Bytes/Sec (Depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  As operações de escrita de OS por disco/sec  |  Operações de Escrita de Disco OS/Sec (Depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Fluxos de saída  |  Fluxos de saída  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Taxa máxima de criação dos fluxos de saída  |  Taxa máxima de criação dos fluxos de saída (pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Por Disco QD  |  QD do disco de dados (depreciado)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Por Disco Ler Bytes/seg  |  Data Disk Read Bytes/Sec (Depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Por disco ler operações/sec  |  Operações de leitura de disco de dados/Sec (Depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Por Disco Escreva Bytes/seg  |  Data Disk Write Bytes/Sec (Depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Por Operações de Escrita de Disco/Sec  |  Operações de Escrita de Disco de Dados/Sec (Depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Percentagem da CPU  |  Percentagem da CPU  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Premium Data Disk Cache Ler Sucesso  |  Vídeo de cache de disco premium (pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Cache de disco de dados premium ler miss  |  Cache de disco de dados premium Leia Miss (Pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Premium OS Disk Cache Ler Sucesso  |  Premium OS Cache Cache Read Hit (Pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Cache de disco premium DO  |  Cache de disco premium OS Leia Miss (Pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Créditos da CPU consumidos  |  Créditos da CPU consumidos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Créditos cpu restantes  |  Créditos cpu restantes  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Profundidade da Fila do Disco de Dados  |  Profundidade da fila do disco de dados (pré-visualização)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Data Disk Ler Bytes/seg  |  Data Disk Read Bytes/Sec (Pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de leitura de disco de dados/sec  |  Data Disk Ler Operações/Sec (Pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Data Disk Write Bytes/seg  |  Data Disk Write Bytes/Sec (Pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de escrita de disco de dados/sec  |  Operações de escrita de disco de dados/sec (pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Discos Ler Bytes  |  Discos Ler Bytes  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de leitura do disco/sec  |  Operações de leitura do disco/sec  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bytes de Escrita de Disco  |  Bytes de Escrita de Disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de escrita de disco/sec  |  Operações de escrita de disco/sec  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Fluxos de Entrada  |  Fluxos de Entrada  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Taxa máxima de criação dos fluxos de entrada  |  Taxa máxima de criação dos fluxos de entrada (pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Entrada na Rede  |  Rede Em Billable (Depreciado)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Rede no Total  |  Rede no Total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Saída da Rede  |  Network out Billable (Depreciado)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Rede out Total  |  Rede out Total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Profundidade da Fila do Disco do SO  |  Profundidade da fila do disco osso (pré-visualização)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Os Discos Ler Bytes/seg  |  Os Discos Ler Bytes/Sec (Pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de leitura de disco osso/sec  |  As operações/seg de leitura do disco OS (pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Os Disk Write Bytes/seg  |  Os Disk Write Bytes/Sec (Pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de escrita de disco osso/sec  |  Operações de escrita de disco osso/sec (pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS Por Disco QD  |  QD do disco osso (depreciado)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS Per Disk Ler Bytes/seg  |  Os Discos Ler Bytes/Sec (Depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS por disco ler operações/sec  |  Operações de leitura de disco osso/sec (depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS Por Disco Escreva Bytes/seg  |  Os Disk Write Bytes/Sec (Depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  As operações de escrita de OS por disco/sec  |  Operações de Escrita de Disco OS/Sec (Depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Fluxos de saída  |  Fluxos de saída  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Taxa máxima de criação dos fluxos de saída  |  Taxa máxima de criação dos fluxos de saída (pré-visualização)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Por Disco QD  |  QD do disco de dados (depreciado)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Por Disco Ler Bytes/seg  |  Data Disk Read Bytes/Sec (Depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Por disco ler operações/sec  |  Operações de leitura de disco de dados/Sec (Depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Por Disco Escreva Bytes/seg  |  Data Disk Write Bytes/Sec (Depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Por Operações de Escrita de Disco/Sec  |  Operações de Escrita de Disco de Dados/Sec (Depreciado)  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Percentagem da CPU  |  Percentagem da CPU  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Premium Data Disk Cache Ler Sucesso  |  Vídeo de cache de disco premium (pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Cache de disco de dados premium ler miss  |  Cache de disco de dados premium Leia Miss (Pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Premium OS Disk Cache Ler Sucesso  |  Premium OS Cache Cache Read Hit (Pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Cache de disco premium DO  |  Cache de disco premium OS Leia Miss (Pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerInstance/containerGroups  |  CpuUsage  |  Utilização da CPU  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerInstance/containerGroups  |  Uso da Memória  |  Utilização de Memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesReceivedPerSecond  |  Bytes de rede recebidos por segundo  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesTransmittedPerSecond  |  Bytes de rede transmitidos por segundo  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerRegistry/registos  |  Duração da execução  |  Duração da execução  |  Milissegundos  |  Total | 
| **Sim**  | Não |  Microsoft.ContainerRegistry/registos  |  Contagem de PullCount bem sucedida  |  Contagem de puxar bem sucedida  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerRegistry/registos  |  Contagem de PushCount bem sucedida  |  Contagem de impulsos bem sucedida  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerRegistry/registos  |  Contagem total de pullcount  |  Contagem total de puxar  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerRegistry/registos  |  Contagem total de empurras  |  Contagem total de empurrões  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_cpu_cores  |  Número total de núcleos de CPU disponíveis num cluster gerido  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_memory_bytes  |  Quantidade total de memória disponível num cluster gerido  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ContainerService/managedClusters  |  kube_node_status_condition  |  Estados para várias condições do nó  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_phase  |  Número de cápsulas por fase  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_ready  |  Número de cápsulas em estado pronto  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  Capacidade Disponível  |  Capacidade Disponível  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloud  |  Cloud Bytes Carregado (Dispositivo)  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloudPerShare  |  Cloud Bytes carregado (Partilhar)  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughput  |  Cloud Download Throughput  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughputPerShare  |  Cloud Download Throughput (Share)  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughput  |  Cloud Upload Throughput  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughputPerShare  |  Cloud Upload (Partilha)  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  Utilização hyperVMemory  |  Edge Compute - Uso da Memória  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  Utilização de HiperVVirtualProcessor  |  Edge Compute - CPU percentual  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NICReadThroughput  |  Ler A Mais(Rede)  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NICWriteThroughput  |  Write Throughput (Rede)  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  Capacidade Total  |  Capacidade Total  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DataFactory/datafactores  |  Corridas falhadas  |  Corridas falhadas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/datafactores  |  Corridas bem sucedidas  |  Corridas bem sucedidas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  Atividades Canceladas Runs  |  Atividade cancelada executa métricas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  ActivityFailedRuns  |  A atividade falhada executa métricas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  ActivitySucceededRuns  |  Atividade bem sucedida executa métricas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  Unidades de FábricaSizeInGb  |  Tamanho total da fábrica (unidade GB)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  IntegraçãoRuntimeAvailableMemory  |  Memória disponível de tempo de integração  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  IntegraçãoRuntimeAverageTaskPickupDelay  |  Duração da fila de tempo de integração  |  Segundos  |  Média | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  IntegraçãoRuntimeCpuPercentage  |  Utilização do CPU em tempo de integração  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  IntegraçãoRuntimeQueueLength  |  Comprimento da fila de tempo de integração  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  Unidades MaxallowedFactorySizeingb  |  Tamanho máximo permitido da fábrica (unidade GB)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  Contagem máxima de recursos permitidos  |  Contagem máxima permitida de entidades  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  PipelineCancelruns  |  Gasoduto cancelado executa métricas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  PipelineFailedRuns  |  Gasoduto falhado executa métricas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  PipelineSucceededRuns  |  Gasoduto bem sucedido executa métricas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  Contagem de Recursos  |  Total de entidades contam  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  TriggerCancelRuns  |  O gatilho cancelado corre métricas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  TriggerFailedRuns  |  O gatilho falhado corre métricas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  TriggerSucceedruns  |  O gatilho bem sucedido corre métricas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeAnalytics/contas  |  JobauendedCancelado  |  Hora da U Cancelada  |  Segundos  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeAnalytics/contas  |  JobauendedFail  |  Tempo falhado da UA  |  Segundos  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeAnalytics/contas  |  JobauendedSuccess  |  Tempo de U de sucesso  |  Segundos  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeAnalytics/contas  |  JobEndedCancelado  |  Empregos Cancelados  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeAnalytics/contas  |  JobEndedFailure  |  Empregos falhados  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeAnalytics/contas  |  JobEndedSuccess  |  Empregos bem sucedidos  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeStore/contas  |  Leitura de Dados  |  Leitura de Dados  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeStore/contas  |  DataWritten  |  Dados Escritos  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeStore/contas  |  Pedidos de Leitura  |  Ler Pedidos  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeStore/contas  |  TotalStorage  |  Armazenamento Total  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.DataLakeStore/contas  |  WriteRequests  |  Pedidos de Escrita  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  active_connections  |  Conexões Ativas  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  backup_storage_used  |  Armazenamento de cópia de segurança utilizado  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  connections_failed  |  Ligações com Falhas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  cpu_percent  |  Por cento do CPU  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  io_consumption_percent  |  IO por cento  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  memory_percent  |  Por cento da memória  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  network_bytes_egress  |  Saída da Rede  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  network_bytes_ingress  |  Entrada na Rede  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  seconds_behind_master  |  Rema de replicação em segundos  |  Contagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  serverlog_storage_limit  |  Limite de armazenamento de registo do servidor  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  serverlog_storage_percent  |  Percentagem de armazenamento de registo do servidor  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  serverlog_storage_usage  |  Armazenamento de registo do servidor utilizado  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  storage_limit  |  Limite de armazenamento  |  Bytes  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  storage_percent  |  Por cento de armazenamento  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  storage_used  |  Armazenamento utilizado  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  active_connections  |  Conexões Ativas  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  backup_storage_used  |  Armazenamento de cópia de segurança utilizado  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  connections_failed  |  Ligações com Falhas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  cpu_percent  |  Por cento do CPU  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  io_consumption_percent  |  IO por cento  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  memory_percent  |  Por cento da memória  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  network_bytes_egress  |  Saída da Rede  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  network_bytes_ingress  |  Entrada na Rede  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  seconds_behind_master  |  Rema de replicação em segundos  |  Contagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  serverlog_storage_limit  |  Limite de armazenamento de registo do servidor  |  Bytes  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  serverlog_storage_percent  |  Percentagem de armazenamento de registo do servidor  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  serverlog_storage_usage  |  Armazenamento de registo do servidor utilizado  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  storage_limit  |  Limite de armazenamento  |  Bytes  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  storage_percent  |  Por cento de armazenamento  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  storage_used  |  Armazenamento utilizado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  active_connections  |  Conexões Ativas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  backup_storage_used  |  Armazenamento de cópia de segurança utilizado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  connections_failed  |  Ligações com Falhas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  cpu_percent  |  Por cento do CPU  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  io_consumption_percent  |  IO por cento  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  memory_percent  |  Por cento da memória  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  network_bytes_egress  |  Saída da Rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  network_bytes_ingress  |  Entrada na Rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  pg_replica_log_delay_in_bytes  |  Max Lag Através de Réplicas  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  pg_replica_log_delay_in_seconds  |  Réplica Lag  |  Segundos  |  Máximo | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  serverlog_storage_limit  |  Limite de armazenamento de registo do servidor  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  serverlog_storage_percent  |  Percentagem de armazenamento de registo do servidor  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  serverlog_storage_usage  |  Armazenamento de registo do servidor utilizado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  storage_limit  |  Limite de armazenamento  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  storage_percent  |  Por cento de armazenamento  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  storage_used  |  Armazenamento utilizado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  active_connections  |  Conexões Ativas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  cpu_percent  |  Por cento do CPU  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  iops  |  IOPS  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  memory_percent  |  Por cento da memória  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_egress  |  Saída da Rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_ingress  |  Entrada na Rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  storage_percent  |  Por cento de armazenamento  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  storage_used  |  Armazenamento utilizado  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Dispositivos/Conta  |  digitaltwins.telemetria.nós  |  Digital Twins Node Telemettry Placeholder  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.comandos.egress.abandone.success  |  Mensagens C2D abandonadas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.comandos.egress.complete.success  |  Entregas de mensagens C2D concluídas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.reject.success.success  |  Mensagens C2D rejeitadas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.methods.failure  |  Invocações de método direto falhado  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.methods.requestSize  |  Solicitar dimensão das invocações do método direto  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.methods.responseSize  |  Tamanho da resposta das invocações do método direto  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.methods.success.success  |  Invocações de método direto bem-sucedidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.failure.  |  Gémeo falhado lê a partir de trás  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.size  |  Tamanho da resposta de leituras gémeas da parte de trás  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.success.success  |  Twin de sucesso lê a partir de trás  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.failure.failure  |  Atualizações gémeas falhadas a partir de trás  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.size  |  Tamanho das atualizações gémeas a partir de trás  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.success  |  Atualizações gémeas bem sucedidas a partir de trás  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  C2DMessagesExpired  |  Mensagens C2D Expiradas (pré-visualização)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  configurações  |  Métricas de Configuração  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Devices/IotHubs  |  connectedDeviceCount  |  Dispositivos conectados (pré-visualização)  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.builtIn.events  |  Encaminhamento: mensagens entregues a mensagens/eventos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.eventHubs  |  Encaminhamento: mensagens entregues ao Event Hub  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusQueues  |  Encaminhamento: mensagens entregues à Fila de Autocarros de Serviço  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusTopics  |  Encaminhamento: mensagens entregues ao Tópico do Autocarro de Serviço  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage  |  Encaminhamento: mensagens entregues no armazenamento  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.blobs  |  Encaminhamento: bolhas entregues ao armazenamento  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.bytes.bytes  |  Encaminhamento: dados entregues ao armazenamento  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.builtIn.events  |  Encaminhamento: latência de mensagens/eventos  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.eventHubs  |  Encaminhamento: latência de mensagens para O Hub de Eventos  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusQueues  |  Encaminhamento: latência de mensagens para fila de autocarros de serviço  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusTopics  |  Encaminhamento: latência de mensagens para tópico de ônibus de serviço  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latência.storage.storage  |  Encaminhamento: latência de mensagem para armazenamento  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetria.egress.caiu  |  Encaminhamento: mensagens de telemetria caídas   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetria.egress.fallback  |  Encaminhamento: mensagens entregues para recuo  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetria.egress.inválido  |  Encaminhamento: mensagens de telemetria incompatíveis  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetria.egress.órfão  |  Encaminhamento: mensagens de telemetria órfãs   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetria.egress.sucesso  |  Encaminhamento: mensagens de telemetria entregues  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetria.ingress.allProtocol  |  Mensagem de telemetria enviar tentativas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetria.ingress.sendThrottle  |  Número de erros de estrangulamento  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetria.ingress.success.success  |  Mensagens de telemetria enviadas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.failure.failure  |  Leituras gémeas falhadas de dispositivos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.size  |  Tamanho da resposta das leituras gémeas dos dispositivos  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.success.success  |  Leituras gémeas bem sucedidas de dispositivos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.failure.failure  |  Atualizações duplas falhadas de dispositivos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.size  |  Tamanho das atualizações gémeas dos dispositivos  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.success.success  |  Atualizações gémeas bem-sucedidas a partir de dispositivos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  dailyMessageQuotaUsed  |  Número total de mensagens utilizadas  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  dispositivoDataUsage  |  Utilização total dos dados do dispositivo  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  dispositivoDataUsageV2  |  Utilização total de dados do dispositivo (pré-visualização)  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  dispositivos.connectedDevices.allProtocol  |  Dispositivos conectados (depreciados)   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  dispositivos.totalDispositivos  |  Total de dispositivos (depreciados)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  Entregas eventgrid  |  Entregas da Grelha de Eventos (pré-visualização)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  EventGridLatency  |  Latência da Grelha de Eventos (pré-visualização)  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.failure  |  Cancelamentos de emprego falhados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.success  |  Cancelamentos de emprego bem sucedidos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  empregos.concluído  |  Trabalhos concluídos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.failure  |  Criações falhadas de empregos de invocação de métodos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.success  |  Criações bem sucedidas de empregos de invocação de métodos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.failure  |  Criações falhadas de empregos de atualização dupla  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.success  |  Criações bem sucedidas de empregos de atualização dupla  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  empregos.falhou  |  Empregos falhados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  empregos.listJobs.failure  |  Chamadas falhadas para listar empregos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  empregos.listJobs.success  |  Chamadas bem sucedidas para listar empregos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.failure  |  Consultas de trabalho falhadas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.success  |  Consultas de emprego bem sucedidas  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Devices/IotHubs  |  totaldeDispositivoCount  |  Total de dispositivos (pré-visualização)  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  twinQueries.failure  |  Perguntas gémeas falhadas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  twinQueries.resultaTamanho  |  Tamanho do resultado das consultas gémeas  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  twinQueries.sucesso  |  Consultas gémeas bem sucedidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Dispositivos/serviços de provisionamento  |  Tentativas de Attestation  |  Tentativas de atestação  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Dispositivos/serviços de provisionamento  |  Atribuição de Dispositivos  |  Dispositivos atribuídos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Dispositivos/serviços de provisionamento  |  Tentativas de Inscrição  |  Tentativas de registo  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  Armazenamento Disponível  |  Armazenamento Disponível  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  Encerramentos de CassandraConnection  |  Encerramentos de conexões Cassandra  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequestCharges  |  Cassandra Request Charges  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  Pedidos de Cassandra  |  Pedidos de Cassandra  |  Contagem  |  Contagem | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  Utilização de dados  |  Utilização de Dados  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.DocumentDB/databaseAccounts  |  DeleteVirtualNetwork  |  DeleteVirtualNetwork  |  Contagem  |  Contagem | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  Contagem de documentos  |  Contagem de Documentos  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  DocumentQuota  |  Quota de Documento  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  Utilização de índices  |  Utilização do índice  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  MetadadosPedidos  |  Pedidos de Metadados  |  Contagem  |  Contagem | 
| **Sim**  | **Sim** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestCharge  |  Taxa de pedido de Mongo  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DocumentDB/databaseAccounts  |  Pedidos de Mongo  |  Pedidos de Mongo  |  Contagem  |  Contagem | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsCount  |  Taxa de pedido de Mongo  |  CondeEmSegundo  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsDelete  |  Taxa de pedido de exclusão de Mongo  |  CondeEmSegundo  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsInsert  |  Taxa de pedido de inserção de Mongo  |  CondeEmSegundo  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsQuery  |  Taxa de pedido de consulta de Mongo  |  CondeEmSegundo  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsUpdate  |  Taxa de pedido de atualização de Mongo  |  CondeEmSegundo  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  ProvisionedThroughput  |  Débito Aprovisionado  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.DocumentDB/databaseAccounts  |  ReplicaçãoTardia  |  Latência de Replicação P99  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  ServiçoDisponibilidade  |  Disponibilidade de Serviço  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequests  |  Total de Pedidos  |  Contagem  |  Contagem | 
| **Sim**  | **Sim** |  Microsoft.DocumentDB/databaseAccounts  |  TotaldeUnidades de Pedidos  |  Total de Unidades de Pedido  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EnterpriseKnowledgeGraph/services  |  FalhaCount  |  Contagem de Falhas  |  Contagem  |  Contagem | 
| Não  | Não |  Microsoft.EnterpriseKnowledgeGraph/services  |  Contagem de Sucessos  |  Contagens com Êxito  |  Contagem  |  Contagem | 
| Não  | Não |  Microsoft.EnterpriseKnowledgeGraph/services  |  SucessoLatency  |  Latência de Sucesso  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.EnterpriseKnowledgeGraph/services  |  TransacçõesContagem  |  Contagem de Transações  |  Contagem  |  Contagem | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domínios  |  DeadLetteredCount  |  Eventos com Letras Mortas  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventGrid/domínios  |  Contagem de falhas de tentativa de entrega  |  Eventos falhados de entrega  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domínios  |  Contagem de Sucessos de Entrega  |  Eventos Entregues  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventGrid/domínios  |  DestinationProcessingDurationInMs  |  Duração do processamento do destino  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domínios  |  Contagem de eventos abandonados  |  Eventos abandonados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domínios  |  MatchedEventCount  |  Eventos Combinados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domínios  |  PublicarFailCount  |  Publicar Eventos Falhados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domínios  |  Contagem de Sucessos  |  Eventos Publicados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domínios  |  PublicarSuccessLatencyInMs  |  Publicar Latência de Sucesso  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/eventSubscriptions  |  DeadLetteredCount  |  Eventos com Letras Mortas  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventGrid/eventSubscriptions  |  Contagem de falhas de tentativa de entrega  |  Eventos falhados de entrega  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/eventSubscriptions  |  Contagem de Sucessos de Entrega  |  Eventos Entregues  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventGrid/eventSubscriptions  |  DestinationProcessingDurationInMs  |  Duração do processamento do destino  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/eventSubscriptions  |  Contagem de eventos abandonados  |  Eventos abandonados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/eventSubscriptions  |  MatchedEventCount  |  Eventos Combinados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/extensionTopics  |  PublicarFailCount  |  Publicar Eventos Falhados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/extensionTopics  |  Contagem de Sucessos  |  Eventos Publicados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/extensionTopics  |  PublicarSuccessLatencyInMs  |  Publicar Latência de Sucesso  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/extensionTopics  |  Contagem de eventos incomparáveis  |  Eventos Incomparáveis  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/tópicos  |  PublicarFailCount  |  Publicar Eventos Falhados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/tópicos  |  Contagem de Sucessos  |  Eventos Publicados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/tópicos  |  PublicarSuccessLatencyInMs  |  Publicar Latência de Sucesso  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/tópicos  |  Contagem de eventos incomparáveis  |  Eventos Incomparáveis  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  ActiveConnections  |  ActiveConnections  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.EventHub/clusters  |  Memória Disponível  |  Memória Disponível  |  Percentagem  |  Máximo | 
| Não  | Não |  Microsoft.EventHub/clusters  |  CaptureBacklog  |  Capture Backlog.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  CapturedBytes  |  Bytes capturados.  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  Mensagens Capturadas  |  Mensagens capturadas.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  Conexões Fechadas  |  Ligações fechadas.  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.EventHub/clusters  |  Conexões Abertas  |  Ligações abertas.  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.EventHub/clusters  |  CPU  |  CPU  |  Percentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.EventHub/clusters  |  IncomingBytes  |  Bytes de entrada.  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/clusters  |  Mensagens de entrada  |  Mensagens de entrada  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/clusters  |  Pedidos de entrada  |  Pedidos de Entrada  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/clusters  |  OutgoingBytes  |  Bytes de saída.  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/clusters  |  Mensagens de saída  |  Mensagens de saída  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  Erros Contingentes  |  Erros ultrapassados da quota.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  Erros de servidor  |  Erros do servidor.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  Pedidos bem sucedidos  |  Pedidos bem sucedidos  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  ThrottledRequests  |  Pedidos estrangulados.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  Erros de utilizador  |  Erros do utilizador.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  ActiveConnections  |  ActiveConnections  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  CaptureBacklog  |  Capture Backlog.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  CapturedBytes  |  Bytes capturados.  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  Mensagens Capturadas  |  Mensagens capturadas.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  Conexões Fechadas  |  Ligações fechadas.  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  Conexões Abertas  |  Ligações abertas.  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  EHABL  |  Mensagens de atraso de arquivo (Depreciadas)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  EHAMBs  |  Entrada de mensagem de arquivo (Depreciada)  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  EHAMSGS  |  Mensagens de arquivo (Depreciadas)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  EHINBYTES  |  Bytes de entrada (Deprecated)  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  EHINMBS  |  Bytes de entrada (obsoletos) (Depreciados)  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  EHINMSGS  |  Mensagens de entrada (Depreciadas)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  EHOUTBYTES  |  Bytes de saída (Deprecated)  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  EHOUTMBS  |  Bytes de saída (obsoletos) (Depreciados)  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  EHOUTMSGS  |  Mensagens de saída (Depreciadas)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  FAILREQ  |  Pedidos Falhados (Preprecated)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  IncomingBytes  |  Bytes de entrada.  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  Mensagens de entrada  |  Mensagens de entrada  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  Pedidos de entrada  |  Pedidos de Entrada  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  INMSGS  |  Mensagens de entrada (obsoletas) (Depreciadas)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  INREQS  |  Pedidos de entrada (Preprecated)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  INTERR  |  Erros internos do servidor (Deprecated)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  MISCERR  |  Outros Erros (Deprecated)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  OutgoingBytes  |  Bytes de saída.  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  Mensagens de saída  |  Mensagens de saída  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  Outmsgs  |  Mensagens de saída (obsoletas) (Depreciadas)  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  Erros Contingentes  |  Erros ultrapassados da quota.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  Erros de servidor  |  Erros do servidor.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  Tamanho  |  Tamanho  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  Pedidos bem sucedidos  |  Pedidos bem sucedidos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  SUCCREQ  |  Pedidos bem sucedidos (Depreciados)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  SVRBSY  |  Erros ocupados do servidor (Deprecated)  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  ThrottledRequests  |  Pedidos estrangulados.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  Erros de utilizador  |  Erros do utilizador.  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.HDInsight/clusters  |  Pedidos categorizados gateway  |  Pedidos categorizados de gateway  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.HDInsight/clusters  |  GatewayRequests  |  Pedidos gateway  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.HDInsight/clusters  |  Trabalhadores NumActivos  |  Número de Trabalhadores Ativos  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.HDInsight/clusters  |  Pedidos de Escala  |  Pedidos de escala  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Insights/AutoscaleSettings  |  Limiar Métrico  |  Limiar Métrico  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/AutoscaleSettings  |  Capacidade Observada  |  Capacidade Observada  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/AutoscaleSettings  |  Valor Métrico Observado  |  Valor Métrico Observado  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Insights/AutoscaleSettings  |  ScaleActionsIniciado  |  Ações de escala iniciadas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  disponibilidadeResultados/disponibilidadePercentage  |  Disponibilidade  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.Insights/Componentes  |  disponibilidadeResultados/contagem  |  Testes de disponibilidade  |  Contagem  |  Contagem | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  disponibilidadeResultados/duração  |  Duração do teste de disponibilidade  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  navegadorTimings/redeDura  |  Tempo de ligação da rede de carga de página  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  navegadorTimings/processamentoDuração  |  Tempo de processamento do cliente  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  navegadorTimings/recepçãoDura  |  Tempo de resposta de receção  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  navegadorTimings/sendDura  |  Enviar tempo de pedido  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  browserTimings/totalDuração  |  Tempo de carga da página do navegador  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Insights/Componentes  |  dependências/contagem  |  Chamadas de dependência  |  Contagem  |  Contagem | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  dependências/duração  |  Duração da dependência  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Insights/Componentes  |  dependências/falhado  |  Falhas de chamada de dependência  |  Contagem  |  Contagem | 
| Não  | Não |  Microsoft.Insights/Componentes  |  exceções/navegador  |  Exceções de browser  |  Contagem  |  Contagem | 
| **Sim**  | **Sim** |  Microsoft.Insights/Componentes  |  exceções/contagem  |  Exceções  |  Contagem  |  Contagem | 
| Não  | Não |  Microsoft.Insights/Componentes  |  exceções/servidor  |  Exceções ao servidor  |  Contagem  |  Contagem | 
| **Sim**  | **Sim** |  Microsoft.Insights/Componentes  |  pageViews/count  |  Vistas da página  |  Contagem  |  Contagem | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  páginaVistas/duração  |  Tempo de carga da vista da página  |  MilliSeconds  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Insights/Componentes  |  desempenhoCounters/excepçõesPerSecond  |  Taxa de exceção  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  desempenhoCounters/memoryAvailableBytes  |  Memória disponível  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  performanceCounters/processCpuPercentage  |  CpU processo  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  performanceCounters/processIOBytesPerSecond  |  Taxa IO do processo  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  desempenhoCounters/processadorCpuPercentage  |  Hora do processador  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  performanceCounters/processPrivateBytes  |  Processo bytes privados  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  performanceCounters/requestExecutionTime  |  HTTP solicitar tempo de execução  |  MilliSeconds  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Insights/Componentes  |  performanceCounters/requestsInQueue  |  PEDIDOS HTTP na fila de candidaturas  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Insights/Componentes  |  performanceCounters/requestsPerSecond  |  Taxa de pedido http  |  CondeEmSegundo  |  Média | 
| Não  | Não |  Microsoft.Insights/Componentes  |  pedidos/contagem  |  Pedidos de servidor  |  Contagem  |  Contagem | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  pedidos/duração  |  Tempo de resposta do servidor  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Insights/Componentes  |  pedidos/falhado  |  Pedidos falhados  |  Contagem  |  Contagem | 
| Não  | Não |  Microsoft.Insights/Componentes  |  pedidos/taxa  |  Taxa de pedido do servidor  |  CondeEmSegundo  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Insights/Componentes  |  vestígios/contagem  |  Traces  |  Contagem  |  Contagem | 
| **Sim**  | Não |  Microsoft.KeyVault/cofres  |  ServiceApiHit  |  Total de acesso síldo de serviço Api Hits  |  Contagem  |  Contagem | 
| **Sim**  | Não |  Microsoft.KeyVault/cofres  |  AtendimentoApiLatency  |  Atncy api de serviço geral  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.KeyVault/cofres  |  ServiçoApiResult  |  Resultados totais da Api de serviço  |  Contagem  |  Contagem | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  CacheUtilização  |  Utilização de cache  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  ContinuamenteExportMaxLatenessMinutes  |  Minutos contínuos de atraso de exportação max  |  Contagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  Exportação ContínuaNumOfRecordsExported  |  Exportação contínua - numdos de registos exportados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  Contagem contínua de gastos com exportações  |  Contagem pendente de exportação contínua  |  Contagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  Resultado contínuo da exportação  |  Resultado contínuo da exportação  |  Contagem  |  Contagem | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  CPU  |  CPU  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  EventosProcessadosForEventHubs  |  Eventos processados (para Hubs de Evento/IoT)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  ExportUtilização  |  Utilização das exportações  |  Percentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  IngestionLatencyInSeconds  |  Latência de ingestão (em segundos)  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  IngestionResult  |  Resultado da ingestão  |  Contagem  |  Contagem | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  IngestionUtilização  |  Utilização de ingestão  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  IngestionVolumeInMB  |  Volume de ingestão (em MB)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  KeepAlive  |  Mantenha-se vivo  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  ConsultaDuração  |  Duração da consulta  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  Taxa de solicitação a vapor  |  Taxa de pedido de ingestão de streaming  |  Contagem  |  RateRequestsPerSecond | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  StreamingIngestDataRate  |  Taxa de dados de ingestão de streaming  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  StreamingIngestDura  |  Duração do ingest de streaming  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  StreamingIngestResults  |  Resultado da ingest de streaming  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Lógica/integraçãoServiceEnvironments  |  AçãoLatency  |  Latência de Ação   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Lógica/integraçãoServiceEnvironments  |  AçõesConcluídas  |  Ações Concluídas   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Lógica/integraçãoServiceEnvironments  |  Ações Falhadas  |  Ações falhadas   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Lógica/integraçãoServiceEnvironments  |  Ações Ignoradas  |  Ações ignoradas   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Lógica/integraçãoServiceEnvironments  |  Ações Iniciadas  |  Ações Iniciadas   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Lógica/integraçãoServiceEnvironments  |  Ações Bem sucedidas  |  Ações bem sucedidas   |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Lógica/integraçãoServiceEnvironments  |  ActionSuccessLatency  |  Latência de Sucesso de Ação   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Lógica/integraçãoServiceEnvironments  |  Eventos ActionThrottled  |  Eventos throttled de ação  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Lógica/integraçãoServiceEnvironments  |  IntegraçãoServiçoServiçoAmbienteConnectorMemoryUsage  |  Uso da memória do conector para o ambiente do serviço de integração  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Lógica/integraçãoServiceEnvironments  |  IntegraçãoServiçoServiçoAmbienteUso do Processador  |  Utilização do processador de conector para o ambiente do serviço de integração  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Lógica/integraçãoServiceEnvironments  |  IntegraçãoServiçoServiçoAmbienteUso defluxodefluxo  |  Utilização da memória do fluxo de trabalho para o ambiente do serviço de integração  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Lógica/integraçãoServiceEnvironments  |  IntegraçãoServiçoServiçoAmbienteUso deprocessadorde fluxos de trabalho  |  Utilização do processador workflow para o ambiente de serviço de integração  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Lógica/integraçãoServiceEnvironments  |  RunFailurePercentage  |  Percentagem de falhas de execução  |  Percentagem  |  Total | 
| **Sim**  | Não |  Microsoft.Lógica/integraçãoServiceEnvironments  |  RunLatency  |  Executar Latência  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Lógica/integraçãoServiceEnvironments  |  RunsCancelado  |  Execuções canceladas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Lógica/integraçãoServiceEnvironments  |  Execuções Concluídas  |  Execuções Concluídas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Lógica/integraçãoServiceEnvironments  |  RunsFailed  |  Execuções falhadas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Lógica/integraçãoServiceEnvironments  |  RunsStarted  |  Corridas Iniciadas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Lógica/integraçãoServiceEnvironments  |  RunsSucceed  |  Runs Succeeded  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Lógica/integraçãoServiceEnvironments  |  Eventos RunStartThrottled  |  Executar eventos throttled  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Lógica/integraçãoServiceEnvironments  |  RunSuccessLatency  |  Latência de Sucesso de Execução  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Lógica/integraçãoServiceEnvironments  |  Eventos RunThrottled  |  Eventos Throttled Run  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Lógica/integraçãoServiceEnvironments  |  TriggerFireLatency  |  Latência de disparo   |  Segundos  |  Média | 
| **Sim**  | Não |  Microsoft.Lógica/integraçãoServiceEnvironments  |  GatilhoLatency  |  Latência do gatilho   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Lógica/integraçãoServiceEnvironments  |  Gatilhos Concluídos  |  Gatilhos Concluídos   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Lógica/integraçãoServiceEnvironments  |  Gatilhos Falhados  |  Gatilhos falhados   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Lógica/integraçãoServiceEnvironments  |  TriggersFired  |  Gatilhos disparados   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Lógica/integraçãoServiceEnvironments  |  Gatilhos Skipped  |  Gatilhos ignorados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Lógica/integraçãoServiceEnvironments  |  GatilhosIniciado  |  Gatilhos Iniciados   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Lógica/integraçãoServiceEnvironments  |  Gatilhos Bem sucedidos  |  Gatilhos bem sucedidos   |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Lógica/integraçãoServiceEnvironments  |  TriggerSuccessLatency  |  Latência de Sucesso do Gatilho   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Lógica/integraçãoServiceEnvironments  |  Eventos TriggerThrottled  |  Eventos throttled de gatilho  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/workflows  |  AçãoLatency  |  Latência de Ação   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  AçõesConcluídas  |  Ações Concluídas   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  Ações Falhadas  |  Ações falhadas   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  Ações Ignoradas  |  Ações ignoradas   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  Ações Iniciadas  |  Ações Iniciadas   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  Ações Bem sucedidas  |  Ações bem sucedidas   |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/workflows  |  ActionSuccessLatency  |  Latência de Sucesso de Ação   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  Eventos ActionThrottled  |  Eventos throttled de ação  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  Execuções billableAction  |  Execuções de Ação Billable  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  Execuções BillableTrigger  |  Execuções de gatilho sinuosos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  Utilização de faturação para execuções de operações nativas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  Utilização de faturação para execuções de operações nativas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  Utilização de faturação para execuções de conector padrão  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  Utilização de faturação para execuções de conector padrão  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  Consumo de armazenamento de armazenamento de dólares de faturação  |  Utilização de faturação para execuções de consumo de armazenamento  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  Consumo de armazenamento de armazenamento de dólares de faturação  |  Utilização de faturação para execuções de consumo de armazenamento  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunFailurePercentage  |  Percentagem de falhas de execução  |  Percentagem  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/workflows  |  RunLatency  |  Executar Latência  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunsCancelado  |  Execuções canceladas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  Execuções Concluídas  |  Execuções Concluídas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunsFailed  |  Execuções falhadas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunsStarted  |  Corridas Iniciadas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunsSucceed  |  Runs Succeeded  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  Eventos RunStartThrottled  |  Executar eventos throttled  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/workflows  |  RunSuccessLatency  |  Latência de Sucesso de Execução  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  Eventos RunThrottled  |  Eventos Throttled Run  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  Execuções Totais Billáveis  |  Execuções Totais De Faturação  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/workflows  |  TriggerFireLatency  |  Latência de disparo   |  Segundos  |  Média | 
| **Sim**  | Não |  Microsoft.Logic/workflows  |  GatilhoLatency  |  Latência do gatilho   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  Gatilhos Concluídos  |  Gatilhos Concluídos   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  Gatilhos Falhados  |  Gatilhos falhados   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  TriggersFired  |  Gatilhos disparados   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  Gatilhos Skipped  |  Gatilhos ignorados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  GatilhosIniciado  |  Gatilhos Iniciados   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  Gatilhos Bem sucedidos  |  Gatilhos bem sucedidos   |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/workflows  |  TriggerSuccessLatency  |  Latência de Sucesso do Gatilho   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  Eventos TriggerThrottled  |  Eventos throttled de gatilho  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/espaços de trabalho  |  Núcleos Ativos  |  Núcleos Ativos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/espaços de trabalho  |  Nódosativos Ativos  |  Nódosativos Ativos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/espaços de trabalho  |  Corridas Concluídas  |  Corridas Concluídas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/espaços de trabalho  |  Corridas falhadas  |  Corridas falhadas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/espaços de trabalho  |  Núcleos ociosos  |  Núcleos ociosos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/espaços de trabalho  |  Nódoas ociosas  |  Nódoas ociosas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/espaços de trabalho  |  Deixando núcleos  |  Deixando núcleos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/espaços de trabalho  |  Deixando nódosos  |  Deixando nódosos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/espaços de trabalho  |  Implementação do modelo falhou  |  Implementação do modelo falhou  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/espaços de trabalho  |  Modelo Implantação Iniciada  |  Modelo Implantação Iniciada  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/espaços de trabalho  |  Model Deploy Succeeded  |  Model Deploy Succeeded  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/espaços de trabalho  |  Registo de Modelos Falhado  |  Registo de Modelos Falhado  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/espaços de trabalho  |  Registo de Modelos Bem Sucedido  |  Registo de Modelos Bem Sucedido  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/espaços de trabalho  |  Núcleos Preempted  |  Núcleos Preempted  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/espaços de trabalho  |  Nódosos pré-empreitados  |  Nódosos pré-empreitados  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/espaços de trabalho  |  Percentagem de Utilização de Quotas  |  Percentagem de Utilização de Quotas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/espaços de trabalho  |  Corridas Iniciadas  |  Corridas Iniciadas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/espaços de trabalho  |  Núcleos Totais  |  Núcleos Totais  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/espaços de trabalho  |  Nósodes totais  |  Nósodes totais  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/espaços de trabalho  |  Núcleos Inutilizáveis  |  Núcleos Inutilizáveis  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/espaços de trabalho  |  Nós Inutilizáveis  |  Nós Inutilizáveis  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Maps/contas  |  Disponibilidade  |  Disponibilidade  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.Maps/contas  |  Utilização  |  Utilização  |  Contagem  |  Contagem | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  Contaque de ativos  |  Contagem de ativos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  AssetQuota  |  Quota de ativos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  Percentagem de Quotas de Ativos  |  Percentagem de quota de ativos utilizada  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  Contagem de políticas de conteúdo  |  Contagem de políticas de chave de conteúdo  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  ContentKeyPolicyQuota  |  Quota de Política chave de conteúdo  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  ContentKeyPolicyQuotaUsedPercentage  |  Percentagem de política-chave de conteúdo utilizada  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  Contagem de Políticas de Streaming  |  Contagem de políticas de streaming  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  StreamingPolicyQuota  |  Quota política de streaming  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  StreamingPolicyQuotaUsedPercentage  |  Quota de política de streaming utilizada percentagem  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices/streamingEndpoints  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Media/mediaservices/streamingEndpoints  |  Pedidos  |  Pedidos  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Media/mediaservices/streamingEndpoints  |  SuccessE2ELatency  |  Fim do sucesso para acabar com a Latência  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  GCPauseTotalCount  |  Contagem de pausas GC  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  GCPauseTotalTime  |  GC Pausa Tempo Total  |  Milissegundos  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  MaxOldGenMemoryPoolBytes  |  Tamanho de dados de geração antiga disponível Max  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  OldGenMemoryPoolBytes  |  Tamanho dos dados da geração antiga  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  OldGenPromotedBytes  |  Promover o tamanho dos dados da geração antiga  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  ServiçoCpuUsagePercentage  |  Percentagem de utilização do CPU de serviço  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  Memória de ServiçoComprometida  |  Memória de Serviço Atribuída  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  ServiçoMemoryMax  |  Memória de serviço Max  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  Memória de ServiçoUtilizada  |  Memória de serviço utilizada  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  SystemCpuUsagePercentage  |  Percentagem de utilização do CPU do sistema  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatErrorCount  |  Erro Global tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatReceivedBytes  |  Tomcat Total Recebido Bytes  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestMaxTime  |  Tomcat Request Max Time  |  Milissegundos  |  Máximo | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestTotalCount  |  Tomcat Request Contagem Total  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestTotalTime  |  Tomcat Request Total Times  |  Milissegundos  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatResponseAvgTime  |  Tomcat Request Tempo Médio  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSentBytes  |  Tomcat Total Bytes Enviados  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionActiveCurrentCurrentCount  |  Tomcat Session Alive Count  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionActiveMaxCount  |  Tomcat Session Max Ative Count  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionAliveMaxTime  |  Tomcat Session Max Alive Time  |  Milissegundos  |  Máximo | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionCreatedCount  |  Tomcat Session Criou a Contagem  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionExpiredCount  |  Contagem expirada da sessão de Tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionRejectedCount  |  Tomcat Session Rejeitou contagem  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  YoungGenPromotedBytes  |  Promover para o tamanho de dados de geração jovem  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolAllocatedUsed  |  Piscina de volume atribuída  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolTotalLogicalSize  |  Volume pool tamanho lógico total  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  MédiaDeLeitura  |  Latência média de leitura  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  MédiaWriteLatency  |  Latência média de escrita  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  Leituras  |  Ler iops  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  Volumelógico  |  Tamanho lógico do volume  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  VolumeSnapshotSize  |  Tamanho do instantâneo de volume  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  WriteIops  |  Escrever iops  |  CondeEmSegundo  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  AplicaçãoGatewayTotalTime  |  Tempo total do Gateway de Aplicação  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  AvgRequestCountPerHealthyHostHost  |  Pedidos por minuto por Anfitrião Saudável  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  BackendConnectTime  |  Tempo de ligação backend  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  BackendFirstByteResponseTime  |  Apoiar o primeiro tempo de resposta do byte  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  BackendLastByteResponseTime  |  Backend Last Byte Response Time  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  BackendResponseStatus  |  Estado de resposta de backend  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  Contagem bloqueada  |  Distribuição de regras de pedidos bloqueados por firewall de aplicação web  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  BlockedReqCount  |  Contagem de pedidos bloqueados por firewall de aplicação web  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  BytesReceived  |  Bytes Recebidos  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  BytesSent  |  Bytes Enviados  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  Unidades de Capacidade  |  Unidades de Capacidade Atuais  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  ClientRtt  |  Cliente RTT  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  ComputeUnits  |  Unidades Computadas Atuais  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  CurrentConnections  |  Conexões Atuais  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  Pedidos Falhados  |  Pedidos com Falhas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  Contagem de anfitriões saudáveis  |  Contagem de hospedeiros saudáveis  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  Contagem compatível  |  Distribuição total de regras de firewall de aplicação web  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  Estatuto de Resposta  |  Estado de Resposta  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  Débito  |  Débito  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  Protocolo TLS  |  Protocolo TLS cliente  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  TotalRequests  |  Total de Pedidos  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  Contagem de anfitriões pouco saudável  |  Contagem de hospedeiros pouco saudável  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/azurefirewalls  |  ApplicationRuleHit  |  Regras de aplicação atingem a contagem  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/azurefirewalls  |  DataProcessado  |  Dados tratados  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Network/azurefirewalls  |  FirewallHealth  |  Estado de saúde firewall  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/azurefirewalls  |  NetworkRuleHit  |  Regras da rede atingir a contagem  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/azurefirewalls  |  SNATPortUtilização  |  Utilização da porta SNAT  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Rede/ligações  |  BitsInPerSecond  |  BitsInPerSecond  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Rede/ligações  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Network/dnszones  |  Volume de Consultas  |  Volume de consulta  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Network/dnszones  |  Utilização de RecordSetCapacity  |  Utilização da capacidade do conjunto de registos  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/dnszones  |  RecordSetCount  |  Contagem de conjuntos de discos  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/expressRouteCircuits  |  Disponibilidade de Arp  |  Disponibilidade arp  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRouteCircuits  |  BGPDisponibilidade  |  Disponibilidade de BGP  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  BitsInPerSecond  |  BitsInPerSecond  |  CondeEmSegundo  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CondeEmSegundo  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsInPerSecond  |  GlobalReachBitsInPerSecond  |  CondeEmSegundo  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsOutPerSecond  |  GlobalReachBitsOutPerSecond  |  CondeEmSegundo  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsInPerSecond  |  DropInBitsPerSecond  |  CondeEmSegundo  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsOutPerSecond  |  DropOutBitsPerSecond  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsInPerSecond  |  BitsInPerSecond  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CondeEmSegundo  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteGateways  |  ErGatewayConnectionBitsInPerSecond  |  BitsInPerSecond  |  CondeEmSegundo  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteGateways  |  ErGatewayConnectionBitsOutPerSecond  |  BitsOutPerSecond  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  Estado-de-administração  |  Estado-de-administração  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  Protocolo de Linha  |  Protocolo de Linha  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  PortBitsInPerSecond  |  BitsInPerSecond  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  PortBitsOutPerSecond  |  BitsOutPerSecond  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  Nível RxLight  |  Nível RxLight  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  TxLightLevel  |  TxLightLevel  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  BackendHealthPercentage  |  Percentagem de Saúde Endend  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  Contagem de pedidos de backend  |  Contagem de pedidos de backend  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  AtrasodePedido  |  Latência de Pedido de Backend  |  MilliSeconds  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  BillableResponseSize  |  Tamanho da resposta facturavel  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  Contagem de Pedidos  |  Número de Pedidos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  Tamanho de pedido  |  Tamanho do pedido  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  Tamanho de resposta  |  Tamanho da resposta  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  TotalLatency  |  Latência Total  |  MilliSeconds  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  Contagem de pedidos de firewall da web  |  Contagem de pedidos de firewall de aplicação web  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Network/loadBalancers  |  AlocadoSnatPorts  |  Portas SNAT atribuídas (Pré-visualização)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/loadBalancers  |  ByteCount  |  Conde byte  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/loadBalancers  |  Disponibilidade de Mergulho  |  Estado da sonda de saúde  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/loadBalancers  |  Contagem de pacotes  |  Contagem de pacotes  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/loadBalancers  |  SnatConnectionCount  |  Contagem de ligação sNAT  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/loadBalancers  |  SYNCount  |  Contagem de SYN  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Network/loadBalancers  |  SnatPorts Usados  |  Portas SNAT usadas (Pré-visualização)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/loadBalancers  |  Disponibilidade Vip  |  Disponibilidade do Caminho de Dados  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/networkInterfaces  |  BytesReceivedRate  |  Bytes Recebidos  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Network/networkInterfaces  |  BytesSentRate  |  Bytes Enviados  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Network/networkInterfaces  |  PacotesReceivedRate  |  Pacotes Recebidos  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/networkInterfaces  |  PacotesSentRate  |  Pacotes Enviados  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/networkWatchers/connectionMonitors  |  AverageRoundtripMs  |  Avg. Tempo de ida e volta (ms)  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Network/networkWatchers/connectionMonitors  |  ChequesFailedPercent  |  Cheques Por cento falhado (Pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/networkWatchers/connectionMonitors  |  SondasFailedPercent  |  % sondas falhadas  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/networkWatchers/connectionMonitors  |  RoundTripTimeMs  |  Tempo de ida e volta (ms) (Pré-visualização)  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  ByteCount  |  Conde byte  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  BytesDropDDoS  |  Bytes de entrada caíram DDoS  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  BytesForwardedDDoS  |  Bytes de entrada encaminhadas DDoS  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  BytesInDDoS  |  Bytes de entrada  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  DDoSTriggerSYNPackets  |  Pacotes SYN de entrada para desencadear mitigação do DDoS  |  CondeEmSegundo  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  DDoSTriggerTCPPackets  |  Pacotes TCP de entrada para desencadear mitigação do DDoS  |  CondeEmSegundo  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  DDoSTriggerUDPPackets  |  Pacotes UDP de entrada para desencadear mitigação do DDoS  |  CondeEmSegundo  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  Ataque ifunderddos  |  Sob ataque DDoS ou não  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  Contagem de pacotes  |  Contagem de pacotes  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  Pacotes DropDDoS  |  Pacotes de entrada caíram DDoS  |  CondeEmSegundo  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  Pacotes ForwardedDDoS  |  Pacotes de entrada encaminhadas DDoS  |  CondeEmSegundo  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  PacotesInDDoS  |  Pacotes de entrada DDoS  |  CondeEmSegundo  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  SynCount  |  Contagem de SYN  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  TCPBytesDropDDoS  |  Bytes de TCP de entrada caíram DDoS  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  TCPBytesForwardedDDoS  |  Bytes de TCP de entrada encaminhadas DDoS  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  TCPBytesInDDoS  |  TCP de entrada bytes DDoS  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  TCPPacketsDropDDoS  |  Pacotes tCP de entrada caíram DDoS  |  CondeEmSegundo  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  TCPPacketsForwardedDDoS  |  Pacotes TCP de entrada encaminharam DDoS  |  CondeEmSegundo  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  TCPPacketsInDDoS  |  Pacotes TCP de entrada DDoS  |  CondeEmSegundo  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  UDPBytesDropDDoS  |  Bytes uDP de entrada caíram DDoS  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  UDPBytesForwardedDDoS  |  Bytes UDP de entrada  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  UDPBytesInDDoS  |  UDP de entrada bytes DDoS  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  UDPPacketsDropDDoS  |  Pacotes uDP de entrada caíram DDoS  |  CondeEmSegundo  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  UDPPacketsForwardedDDoS  |  Pacotes UDP de entrada encaminharam DDoS  |  CondeEmSegundo  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  UDPPacketsInDDoS  |  Pacotes UDP de entrada DDoS  |  CondeEmSegundo  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresss  |  Disponibilidade Vip  |  Disponibilidade do Caminho de Dados  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/trafficManagerProfiles  |  ProbeAgentCurrentCurrentEndpointStateByProfileResourceId  |  Estado do ponto final por ponto final  |  Contagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Network/trafficManagerProfiles  |  QpsByEndpoint  |  Consultas por Endpoint Devolvidos  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  Largura média de banda  |  Gateway S2S Largura de banda  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  Largura de banda p2S  |  Largura de banda Gateway P2S  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  Contagem de Conexões P2S  |  Contagem de ligação P2S  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  TunnelAverageBandwidth  |  Largura de banda do túnel  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressBytes  |  Túnel Egress Bytes  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPacketDropTSMismatch  |  Túnel Egress TS Mismatch Packet Drop  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPackets  |  Pacotes de Egress do Túnel  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressBytes  |  Bytes de Ingresso do Túnel  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  Tunnelingresspacketdroptsmismatch  |  Túnel Ingress TS Mismatch Packet Drop  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPackets  |  Pacotes de ingressos de túnel  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworks  |  PingMeshAverageRoundtripMs  |  Tempo de ida e volta para Pings a um VM  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworks  |  PingMeshProbesFailedPercent  |  Pings falhados a um VM  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  entrada  |  Mensagens de entrada  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.all.failedrequests  |  Todos os pedidos falhados de entrada  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  entrada.todos.pedidos  |  Todos os pedidos de entrada  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  entrada.agendado  |  Notificações push agendadas enviadas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  entrada.agendado.cancelar  |  Notificações de Push agendadas canceladas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  instalação.todos  |  Operações de Gestão de Instalações  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  instalação.excluir  |  Excluir operações de instalação  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  instalação.get  |  Obter Operações de Instalação  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  instalação.patch  |  Operações de instalação de patch  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  instalação.upsert  |  Criar ou atualizar operações de instalação  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  notificationhub.pushes  |  Todas as Notificações De Saída  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.badorexpiredchannel  |  Erros no canal mau ou expirado  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.channelerror  |  Erros do Canal  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.invalidpayload  |  Erros de carga útil  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.pnserror  |  Erros do sistema de notificação externa  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.success  |  Notificações bem sucedidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.badchannel  |  Erro do canal bad da APNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.expiredchannel  |  Erro do canal expirado APNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidcredentials  |  Erros de autorização da APNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidnotificationsize  |  Erro de tamanho de notificação inválido da APNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.pnserror  |  Erros APNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.success  |  Notificações bem sucedidas da APNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.authenticationerror  |  Erros de autenticação GCM  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.badchannel  |  Erro do canal errado da GCM  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.expiredchannel  |  Erro do canal expirado GCM  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidcredentials  |  Erros de autorização GCM (Credenciais inválidas)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  formato outgoing.gcm.invalidnotification  |  Formato de notificação inválida GCM  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationsize  |  Erro de tamanho de notificação inválido gcm  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.pnserror  |  Erros GCM  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.success  |  Notificações bem sucedidas da GCM  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.throttled  |  Notificações de aceleração GCM  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.wrongchannel  |  Erro do canal errado GCM  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.authenticationerror  |  Erros de autenticação do MPNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.badchannel  |  Erro do Canal Mau mpns  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.channeldesligado  |  Canal MPNS desligado  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.drop  |  MpNS Notificações retiradas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidcredenciais  |  Credenciais de mpns inválidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidnotificationforma  |  Formato de notificação inválido do MPNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.pnserror  |  Erros mpns  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.success  |  Notificações bem sucedidas do MPNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.throttled  |  Notificações de aceleração do MPNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.authenticationerror  |  Erros de autenticação wns  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.badchannel  |  Erro do canal errado wns  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channeldesligado  |  Canal WNS desligado  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channelthrottled  |  Canal WNS Throttled  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.drop  |  WNS Deixou notificações  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.expiredchannel  |  Erro do canal expirado wns  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidcredentials  |  Erros de autorização wns (credenciais inválidas)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationforma  |  Formato de notificação inválido wns  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationsize  |  Erro de tamanho de notificação inválido wns  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidtoken  |  Erros de autorização wns (ficha inválida)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.pnserror  |  Erros wns  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.success  |  Notificações bem sucedidas da WNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.throttled  |  Notificações wns throttled  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.tokenproviderunreachable  |  Erros de autorização wns (incontactáveis)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.wrongtoken  |  Erros de autorização wns (ficha errada)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registo.todos  |  Operações de Registo  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registo.criar  |  Inscrições Criar Operações  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registo.excluir  |  Inscrições Supressão de Operações  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registro.get  |  Operações de Leitura de Registo  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.update  |  Operações de Atualização de Registo  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  agendado.pendente  |  Notificações agendadas pendentes  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  memória disponível Average_%  |  % Memória Disponível  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_% espaço disponível de swap  |  % Espaço de Troca Disponível %  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_% bytes comprometidos em uso  |  % Bytes Comprometidos em Uso  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_% de tempo dPC  |  % Tempo dPC  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_% Inodes Grátis  |  % Inodes livres  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_% espaço livre  |  % Espaço Livre  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_% espaço livre  |  % Espaço Livre  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_% tempo de inatividade  |  % de Tempo Inativo  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_% de tempo de interrupção  |  % tempo de interrupção  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_% IO Tempo de espera  |  % Io Tempo de Espera  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_% bom tempo  |  % Bom Tempo  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_% tempo privilegiado  |  % Tempo Privilegiado  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_% tempo do processador  |  % de Tempo do Processador  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_% tempo do processador  |  % de Tempo do Processador  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_% Inodes usados  |  % Inodos Usados  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_% memória usada  |  % Memória Usada  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_% Espaço Usado  |  % Espaço Usado  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_% espaço de troca usado  |  % Espaço de troca usado %  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_% tempo de utilizador  |  % Tempo de utilizador  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Available MBytes  |  MBytes disponíveis  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  memória Average_Available MBytes  |  Memória MBytes disponível  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Available Troca de MBytes  |  Troca de MBytes disponíveis  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Avg. de Disco seg/Leitura  |  Avg. Disk sec/Read  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Avg. de Disco seg/Leitura  |  Avg. Disk sec/Read  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Avg. Sec/Transferência de disco  |  Avg. Disco sec/Transferência  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Avg. de Disco seg/Escrita  |  Avg. Disk sec/Write  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Avg. de Disco seg/Escrita  |  Avg. Disk sec/Write  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Bytes recebido/seg  |  Bytes Recebidos/seg  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Bytes Enviado/seg  |  Bytes Enviado/seg  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Bytes Total/seg  |  Bytes Total/seg  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  comprimento da fila do disco de Average_Current  |  Comprimento da fila do disco atual  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Disk Ler Bytes/seg  |  Discos Ler Bytes/seg  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  leituras Average_Disk/seg  |  Leituras/seg de disco  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  leituras Average_Disk/seg  |  Leituras/seg de disco  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  transferências Average_Disk/seg  |  Transferências de disco/seg  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  transferências Average_Disk/seg  |  Transferências de disco/seg  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Disk Escrever Bytes/seg  |  Discos Write Bytes/seg  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Disk Escreve/seg  |  Escritas de Disco/seg  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Disk Escreve/seg  |  Escritas de Disco/seg  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Free Megabytes  |  Megabytes grátis  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Free Megabytes  |  Megabytes grátis  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  memória física Average_Free  |  Memória Física Gratuita  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  espaço Average_Free em arquivos de paging  |  Espaço gratuito em arquivos de paging  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Free Memória Virtual  |  Memória Virtual Gratuita  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Logical Bytes/seg de disco  |  Bytes/sede de disco lógico  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Page Leituras/seg  |  Leituras da página/seg  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Page Escreve/seg  |  Página Escritas/seg  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Pages/seg  |  Páginas/seg  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Pct Tempo Privilegiado  |  Tempo Privilegiado do Pct  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  tempo de utilizador Average_Pct  |  Tempo de utilizador do Pct  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Physical Bytes/seg de disco  |  Bytes/sede de disco físico  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Processes  |  Processos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  comprimento da fila Average_Processor  |  Comprimento da fila do processador  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Size armazenado em arquivos de paging  |  Tamanho armazenado em arquivos de paging  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Total Bytes  |  Bytes Totais  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Total Bytes Recebidos  |  Total de Bytes Recebidos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Total Bytes Transmitidos  |  Total bytes transmitidos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  colisões Average_Total  |  Colisões totais  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  pacotes Average_Total recebidos  |  Total de pacotes recebidos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  pacotes de Average_Total transmitidos  |  Pacotes totais transmitidos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Total Erros Rx  |  Total de Erros Rx  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  erros Average_Total Tx  |  Total de erros Tx  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Uptime  |  Tempo de subida  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Used MBytes Swap Space  |  Espaço de troca de MBytes usado  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Used Memory kBytes  |  KBytes de memória usada  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Used Memória MBytes  |  MBytes de memória usada  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Average_Users  |  Utilizadores  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  memória partilhada Average_Virtual  |  Memória Partilhada Virtual  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Evento  |  Evento  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.OperationalInsights/espaços de trabalho  |  Heartbeat  |  Heartbeat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.OperationalInsights/espaços de trabalho  |  Atualizar  |  Atualizar  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.PowerBIDedicated/capacities  |  memory_metric  |  Memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.PowerBIDedicated/capacities  |  memory_thrashing_metric  |  Goleada de memória (Conjuntos de Dados)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.PowerBIDedicated/capacities  |  qpu_high_utilization_metric  |  Maior Utilização de QPU  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.PowerBIDedicated/capacities  |  ConsultaDuração  |  Duração da consulta (Conjuntos de Dados)  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.PowerBIDedicated/capacities  |  ConsultaPoolJobQueueLength  |  Comprimento da fila de trabalho da piscina de consulta (Conjuntos de dados)  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Relay/namespaces  |  ActiveConnections  |  ActiveConnections  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  Ouvintes Ativos  |  Ouvintes Ativos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Relay/namespaces  |  BytesTransferido  |  BytesTransferido  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  ListenerConnections-ClientError  |  ListenerConnections-ClientError  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  ListenerConnections-ServerError  |  ListenerConnections-ServerError  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  ListenerConnections-Success  |  ListenerConnections-Success  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  ListenerConnections-TotalRequests  |  ListenerConnections-TotalRequests  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  ListenerDisconnects  |  ListenerDisconnects  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  SenderConnections-ClientError  |  SenderConnections-ClientError  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  SenderConnections-ServerError  |  SenderConnections-ServerError  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  SenderConnections-Success  |  SenderConnections-Success  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  SenderConnections-TotalRequests  |  SenderConnections-TotalRequests  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  SenderDisconnects  |  SenderDisconnects  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Search/searchServices  |  SearchLatency  |  Latência de pesquisa  |  Segundos  |  Média | 
| **Sim**  | Não |  Microsoft.Search/searchServices  |  PesquisaqueriesPerSecond  |  Pesquisar consultas por segundo  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.Search/searchServices  |  ThrottledSearchQueriesPercentage  |  Percentagem de consultas de pesquisa acelerada  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/espaços de nome  |  ActiveConnections  |  ActiveConnections  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/espaços de nome  |  Mensagens Ativas  |  Contagem de mensagens ativas numa fila/tópico.  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/espaços de nome  |  Conexões Fechadas  |  Ligações fechadas.  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/espaços de nome  |  Conexões Abertas  |  Ligações abertas.  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/espaços de nome  |  CPUXNS  |  CPU (Depreciado)  |  Percentagem  |  Máximo | 
| Não  | Não |  Microsoft.ServiceBus/espaços de nome  |  Mensagens mortas  |  Conde de mensagens com letras mortas numa fila/tópico.  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.ServiceBus/espaços de nome  |  Mensagens de entrada  |  Mensagens de entrada  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ServiceBus/espaços de nome  |  Pedidos de entrada  |  Pedidos de Entrada  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/espaços de nome  |  Mensagens  |  Contagem de mensagens numa fila/tópico.  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/espaços de nome  |  NomespaceCpuUsage  |  CPU  |  Percentagem  |  Máximo | 
| Não  | Não |  Microsoft.ServiceBus/espaços de nome  |  Uso do espaço de nomeMemory  |  Utilização de Memória  |  Percentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.ServiceBus/espaços de nome  |  Mensagens de saída  |  Mensagens de saída  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/espaços de nome  |  Mensagens Agendadas  |  Contagem de mensagens programadas numa fila/tópico.  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/espaços de nome  |  Erros de servidor  |  Erros do servidor.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/espaços de nome  |  Tamanho  |  Tamanho  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/espaços de nome  |  Pedidos bem sucedidos  |  Pedidos bem sucedidos  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/espaços de nome  |  ThrottledRequests  |  Pedidos estrangulados.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/espaços de nome  |  Erros de utilizador  |  Erros do utilizador.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/espaços de nome  |  WSXNS  |  Utilização da memória (Depreciada)  |  Percentagem  |  Máximo | 
| Não  | Não |  Microsoft.ServiceFabricMesh/applications  |  ActualCpu  |  ActualCpu  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/applications  |  Memória Real  |  Memória Real  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/applications  |  Cpu atribuído  |  Cpu atribuído  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/applications  |  Memória Atribuída  |  Memória Atribuída  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/applications  |  Estatuto de Aplicação  |  Estatuto de Aplicação  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/applications  |  Estatuto de Contentores  |  Estatuto de Contentores  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/applications  |  CpuUtilização  |  CpuUtilização  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/applications  |  Utilização da Memória  |  Utilização da Memória  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/applications  |  Reiniciar Contagem  |  Reiniciar Contagem  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/applications  |  Estatuto de Replicação de Serviço  |  Estatuto de Replicação de Serviço  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/applications  |  Estatuto de Serviço  |  Estatuto de Serviço  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.SignalRService/Signalr  |  Contagem de Ligações  |  Contagem de Ligação  |  Contagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.SignalRService/Signalr  |  Tráfego de entrada  |  Tráfego de Entrada  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.SignalRService/Signalr  |  Contagem de mensagens  |  Contagem de Mensagens  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.SignalRService/Signalr  |  Tráfego de saída  |  Tráfego de Saída  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.SignalRService/Signalr  |  Erros de sistema  |  Erros do sistema  |  Percentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.SignalRService/Signalr  |  Erros de utilizador  |  Erros do utilizador  |  Percentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Sql/managedInstances  |  avg_cpu_percent  |  Percentagem média de CPU  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/managedInstances  |  io_bytes_read  |  IO bytes lidos  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/managedInstances  |  io_bytes_written  |  IO bytes escritos  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/managedInstances  |  io_requests  |  IO pedidos contam  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/managedInstances  |  reserved_storage_mb  |  Espaço de armazenamento reservado  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/managedInstances  |  storage_space_used_mb  |  Espaço de armazenamento usado  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/managedInstances  |  virtual_core_count  |  Contagem de núcleos virtuais  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores  |  database_dtu_consumption_percent  |  Percentagem de DTU  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores  |  database_storage_used  |  Espaço de dados utilizado  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores  |  dtu_consumption_percent  |  Percentagem de DTU  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores  |  dtu_used  |  DTU usado  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores  |  storage_used  |  Espaço de dados utilizado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  allocated_data_storage  |  Espaço de dados atribuído  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  app_cpu_billed  |  App CPU faturada  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  app_cpu_percent  |  Percentagem de CPU da aplicação  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  app_memory_percent  |  Percentagem de memória de aplicativos  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  blocked_by_firewall  |  Bloqueado por Firewall  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  cache_hit_percent  |  Percentagem de impacto cache  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  cache_used_percent  |  Cache usado percentagem  |  Percentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  connection_failed  |  Ligações com Falhas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  connection_successful  |  Conexões bem sucedidas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  cpu_limit  |  Limite de CPU  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  cpu_percent  |  Percentagem de CPU  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  cpu_used  |  CPU utilizado  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  impasse  |  Impasses  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  dtu_consumption_percent  |  Percentagem de DTU  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  dtu_limit  |  Limite dTU  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  dtu_used  |  DTU usado  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  dwu_consumption_percent  |  Percentagem de DWU  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  dwu_limit  |  Limite de DWU  |  Contagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  dwu_used  |  DWU usado  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  local_tempdb_usage_percent  |  Percentagem de temperatura local  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  log_write_percent  |  Taxa de Log IO  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  memory_usage_percent  |  Percentagem de memória  |  Percentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  physical_data_read_percent  |  Percentagem de ES de Dados  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  sessions_percent  |  Percentagem de sessões  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  storage  |  Espaço de dados utilizado  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  storage_percent  |  Espaço de dados usado por cento  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  tempdb_data_size  |  Kilobytes de tamanho de ficheiro de dados tempdb  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  tempdb_log_size  |  Kilobytes de tamanho de ficheiro de log tempdb  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  tempdb_log_used_percent  |  Log por cento temporário usado  |  Percentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  workers_percent  |  Percentagem de trabalhadores  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  xtp_storage_percent  |  Por cento de armazenamento OLTP em Memória  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/elasticPools  |  allocated_data_storage  |  Espaço de dados atribuído  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/elasticPools  |  allocated_data_storage_percent  |  Espaço de dados atribuído por cento  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servidores/elasticPools  |  cpu_limit  |  Limite de CPU  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/elasticPools  |  cpu_percent  |  Percentagem de CPU  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/elasticPools  |  cpu_used  |  CPU utilizado  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores/elasticPools  |  database_allocated_data_storage  |  Espaço de dados atribuído  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores/elasticPools  |  database_cpu_limit  |  Limite de CPU  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores/elasticPools  |  database_cpu_percent  |  Percentagem de CPU  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores/elasticPools  |  database_cpu_used  |  CPU utilizado  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores/elasticPools  |  database_dtu_consumption_percent  |  Percentagem de DTU  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores/elasticPools  |  database_eDTU_used  |  eDTU utilizado  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores/elasticPools  |  database_log_write_percent  |  Taxa de Log IO  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores/elasticPools  |  database_physical_data_read_percent  |  Percentagem de ES de Dados  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores/elasticPools  |  database_sessions_percent  |  Percentagem de sessões  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores/elasticPools  |  database_storage_used  |  Espaço de dados utilizado  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores/elasticPools  |  database_workers_percent  |  Percentagem de trabalhadores  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/elasticPools  |  dtu_consumption_percent  |  Percentagem de DTU  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/elasticPools  |  eDTU_limit  |  limite eDTU  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/elasticPools  |  eDTU_used  |  eDTU utilizado  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/elasticPools  |  log_write_percent  |  Taxa de Log IO  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/elasticPools  |  physical_data_read_percent  |  Percentagem de ES de Dados  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/elasticPools  |  sessions_percent  |  Percentagem de sessões  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/elasticPools  |  storage_limit  |  Tamanho máximo de dados  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/elasticPools  |  storage_percent  |  Espaço de dados usado por cento  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/elasticPools  |  storage_used  |  Espaço de dados utilizado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/elasticPools  |  tempdb_data_size  |  Kilobytes de tamanho de ficheiro de dados tempdb  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servidores/elasticPools  |  tempdb_log_size  |  Kilobytes de tamanho de ficheiro de log tempdb  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servidores/elasticPools  |  tempdb_log_used_percent  |  Log por cento temporário usado  |  Percentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/elasticPools  |  workers_percent  |  Percentagem de trabalhadores  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/elasticPools  |  xtp_storage_percent  |  Por cento de armazenamento OLTP em Memória  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts  |  Disponibilidade  |  Disponibilidade  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts  |  SuccessE2ELatency  |  Latência de E2E Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts  |  SuccessServerLatency  |  Latência de Servidor Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts  |  Transações  |  Transações  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Storage/storageAccounts  |  Capacidade Utilizada  |  Capacidade usada  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/blobServices  |  Disponibilidade  |  Disponibilidade  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.Armazenamento/armazenamentoContas/blobServices  |  BlobCapacity  |  Capacidade blob  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.Armazenamento/armazenamentoContas/blobServices  |  BlobCount  |  Contagem de Bolhas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/blobServices  |  Contagem de contentores  |  Contagem de contentores blob  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/blobServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.Armazenamento/armazenamentoContas/blobServices  |  IndexCapacity  |  Capacidade indexada  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/blobServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/blobServices  |  SuccessE2ELatency  |  Latência de E2E Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/blobServices  |  SuccessServerLatency  |  Latência de Servidor Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/blobServices  |  Transações  |  Transações  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/ficheirosServiços  |  Disponibilidade  |  Disponibilidade  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/ficheirosServiços  |  Saída  |  Saída  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.Armazenamento/armazenamentoContas/ficheirosServiços  |  Capacidade de Arquivo  |  Capacidade de arquivo  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.Armazenamento/armazenamentoContas/ficheirosServiços  |  Contagem de Ficheiros  |  Contagem de Ficheiros  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Armazenamento/armazenamentoContas/ficheirosServiços  |  FileShareCount  |  Contagem de partilha de ficheiros  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Armazenamento/armazenamentoContas/ficheirosServiços  |  FileShareQuota  |  Tamanho da quota de partilha de ficheiros  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.Armazenamento/armazenamentoContas/ficheirosServiços  |  FileShareSnapshotCount  |  Contagem de instantâneos de partilha de ficheiros  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Armazenamento/armazenamentoContas/ficheirosServiços  |  FileShareSnapshotSize  |  Tamanho instantâneo de partilha de ficheiros  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/ficheirosServiços  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/ficheirosServiços  |  SuccessE2ELatency  |  Latência de E2E Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/ficheirosServiços  |  SuccessServerLatency  |  Latência de Servidor Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/ficheirosServiços  |  Transações  |  Transações  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/filaServiços  |  Disponibilidade  |  Disponibilidade  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/filaServiços  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/filaServiços  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/filaServiços  |  Capacidade de fila  |  Capacidade de fila  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/filaServiços  |  Contagem de filas  |  Contagem de filas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/filaServiços  |  Contagem de mensagens de fila  |  Contagem de mensagens de fila  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/filaServiços  |  SuccessE2ELatency  |  Latência de E2E Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/filaServiços  |  SuccessServerLatency  |  Latência de Servidor Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/filaServiços  |  Transações  |  Transações  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/tableServices  |  Disponibilidade  |  Disponibilidade  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/tableServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/tableServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/tableServices  |  SuccessE2ELatency  |  Latência de E2E Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/tableServices  |  SuccessServerLatency  |  Latência de Servidor Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/tableServices  |  Capacidade de Mesa  |  Capacidade de Mesa  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/tableServices  |  Contagem de tabelas  |  Contagem de mesas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/tableServices  |  Contagem de tabelas  |  Contagem de entidades de mesa  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Armazenamento/armazenamentoContas/tableServices  |  Transações  |  Transações  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientiOPS  |  IOPS total do cliente  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClienteLatency  |  Latência média do cliente  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientLockiOPS  |  IOPS de bloqueio de cliente  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  Metadados do ClienteReadIOPS  |  Metadados do Cliente Ler IOPS  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientesMetadosWriteIOPS  |  Metadados de clientes Escrevem IOPS  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientReadIOPS  |  Cliente ler IOPS  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientReadThroughput  |  Média de leitura de cache  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientWriteIOPS  |  IOPS de escrita de cliente  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientWriteThroughput  |  Média cache write por ção  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetAsyncWriteThroughput  |  StorageTarget Asynchronous Write Throughput  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetFillThroughput  |  ArmazenamentoTarget Fill Ingput  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetHealth  |  Saúde alvo de armazenamento  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  Alvos de armazenamento  |  IOPS total de armazenamento  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ArmazenamentoTargetLatency  |  Latência StorageTarget  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ArmazenamentoTargetMetasLeiaIOPS  |  ArmazenamentoTarget Metadados Ler IOPS  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ArmazenamentoTargetMetadataWriteIOPS  |  ArmazenamentoTarget Metadados Escrevam IOPS  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetReadAheadThroughput  |  ArmazenamentoTarget Ler à frente  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ArmazenamentoTargetReadIOPS  |  ArmazenamentoTarget Ler IOPS  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetSyncWriteThroughput  |  StorageTarget Synchronous Write Throughput  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetTotalReadThroughput  |  ArmazenamentoTarget Total Read Throughput  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetTotalWriteThroughput  |  StorageTarget Total Write Throughput  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetWriteIOPS  |  ArmazenamentoTarget Escrever IOPS  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  Tempo de subida  |  Tempo de subida  |  Contagem  |  Média | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  Resultado da sessão do servidor  |  Resultado da sessão de sincronização  |  Contagem  |  Média | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  Ficheiros Transferidos de Ficheiros transferidos de armazenamento  |  Bytes sincronizados  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledNetworkBytesByApplication  |  Tamanho de recolha de nível de nuvem por aplicação  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledTotalNetworkBytes  |  Tamanho da recuperação do tiering de nuvem  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  ArmazenamentoSyncRecallIOTotalSizeBytes  |  Recolha de tiering de nuvem  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallThroughputBytesPerSecond  |  Entrada de recolha de tiering de cloud  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  Heartbeat Do StorageSyncServer  |  Estado On-Line do Servidor  |  Contagem  |  Máximo | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  Contagem de ficheiros aplicados de sincronização de armazenamento  |  Ficheiros Sincronizados  |  Contagem  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  ArmazenamentoSyncSyncSessionPerItemErrorsCount  |  Ficheiros que não sincronizam  |  Contagem  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/registeredServers  |  Batimentocardíaco de servidor  |  Estado On-Line do Servidor  |  Contagem  |  Máximo | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/registeredServers  |  ServerRecallIOTotalSizeBytes  |  Recolha de tiering de nuvem  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupBatchTransferFileBytes  |  Bytes sincronizados  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/syncGroups  |  Contagem de ficheiros aplicados syncgroupsession  |  Ficheiros Sincronizados  |  Contagem  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionPerItemErrorsCount  |  Ficheiros que não sincronizam  |  Contagem  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointBatchTransferFileBytes  |  Bytes sincronizados  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionAppliedFilesCount  |  Ficheiros Sincronizados  |  Contagem  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionPerItemErrorsCount  |  Ficheiros que não sincronizam  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutPedidos Falhados  |  Pedidos de função falhados  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutInputevents  |  Eventos de Função  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutRequests  |  Pedidos de Função  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  Erros de Conversão  |  Erros de Conversão de Dados  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  Erro de desserialização  |  Erros de desserialização de entrada  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  DropOrAdjustedEvents  |  Fora de ordem Eventos  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  Eventos EarlyInput  |  Eventos de entrada precoce  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  Erros  |  Erros de tempo de execução  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventBytes  |  Bytes de Evento de Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  InputEvents  |  Eventos de entrada  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesBacklogged  |  Eventos de entrada atrasados  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesPerSecond  |  Fontes de entrada recebidas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  Eventos LateInput  |  Eventos de entrada tardia  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  Eventos de saída  |  Eventos de Saída  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  OutputWatermarkDelaySeconds  |  Atraso na marca de água  |  Segundos  |  Máximo | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  Utilização de Recursos  |  SU % Utilização  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Discos Ler Bytes  |  Discos Ler Bytes  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Operações de leitura do disco/sec  |  Operações de leitura do disco/sec  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Bytes de Escrita de Disco  |  Bytes de Escrita de Disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Operações de escrita de disco/sec  |  Operações de escrita de disco/sec  |  CondeEmSegundo  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadBytesPerSecond  |  Discos Ler Bytes/Sec  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadLatency  |  Latência de leitura do disco  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Operações de Leitura de Discos  |  Operações de leitura do disco  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteBytesPerSecond  |  Discos Write Bytes/Sec  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteLatency  |  Latência de escrita de disco  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteOperations  |  Operações de escrita de disco  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryActive  |  Memória Ativa  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemóriaConcedida  |  Memória Concedida  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemóriaUsada  |  Memória Usada  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Entrada na Rede  |  Entrada na Rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Saída da Rede  |  Saída da Rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkInBytesPerSecond  |  Rede Em Bytes/Sec  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkOutBytesPerSecond  |  Rede out Bytes/Sec  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Percentagem da CPU  |  Percentagem da CPU  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  PercentageCpuReady  |  CpU percentual pronto  |  Milissegundos  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Pedidos Ativos  |  Pedidos Ativos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Tempo médio de resposta  |  Tempo médio de resposta  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesReceived  |  Dados em  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesSent  |  Dados out  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  CpuPercentage  |  Percentagem de CPU  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Comprimento da fila do disco  |  Comprimento da fila do disco  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http101  |  Http 101  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http2xx  |  Http 2xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http3xx  |  Http 3xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http401  |  Http 401  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http403  |  Http 403  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http404  |  Http 404  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http406  |  Http 406  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http4xx  |  Http 4xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http5xx  |  Erros do servidor http  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  HttpqueueLength  |  Comprimento da fila http  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  GrandesAparições do Serviço de Serviços  |  Trabalhadores do grande plano de serviço de aplicações  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MédiaAppServicePlanInstances  |  Trabalhadores do plano de serviço de aplicações médias  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Percentagem de Memória  |  Percentagem de Memória  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Pedidos  |  Pedidos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Pequenas Instâncias planplana do SmallAppService  |  Trabalhadores do plano de serviço de aplicativos de pequena saúde  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  TotalFrontEnds  |  Extremidades frontais totais  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/workerPools  |  CpuPercentage  |  Percentagem de CPU  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/workerPools  |  Percentagem de Memória  |  Percentagem de Memória  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/workerPools  |  TrabalhadoresDisponíveis  |  Trabalhadores disponíveis  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/workerPools  |  TrabalhadoresTotal  |  Total de Trabalhadores  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/workerPools  |  Trabalhadores utilizados  |  Trabalhadores usados  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  BytesReceived  |  Dados em  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  BytesSent  |  Dados out  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  CpuPercentage  |  Percentagem de CPU  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  Comprimento da fila do disco  |  Comprimento da fila do disco  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  HttpqueueLength  |  Comprimento da fila http  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  Percentagem de Memória  |  Percentagem de Memória  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpCloseWait  |  TCP Close Wait  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpClosing  |  Fecho do TCP  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpEstablished  |  TCP Estabelecido  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpFinWait1  |  TCP Fin Esperar 1  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpFinWait2  |  TCP Fin Wait 2  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpLastAck  |  TCP Last Ack  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpSynReceived  |  TCP Syn recebido  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpSynSent  |  TCP Syn Enviado  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpTimeWait  |  Tempo de Espera do TCP  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  AppConnections  |  Ligações  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Conjunto de trabalho de memória média  |  Conjunto de trabalho médio da memória  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Tempo médio de resposta  |  Tempo médio de resposta  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  BytesReceived  |  Dados em  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  BytesSent  |  Dados out  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  CpuTime  |  Tempo cpu  |  Segundos  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  CurrentAssemblies  |  Assembleias atuais  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Contagem de execuções de funções  |  Contagem de execução de funções  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Unidades de Execução de Funções  |  Unidades de Execução de Funções  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Gen0Collections  |  Coleções de lixo gen 0  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Gen1Collections  |  Coleções de lixo da Gen 1  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Gen2Collections  |  Coleções de lixo gen 2  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Alças  |  N.º de Identificadores  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  HealthCheckStatus  |  Estado do controlo de saúde  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http101  |  Http 101  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http2xx  |  Http 2xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http3xx  |  Http 3xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http401  |  Http 401  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http403  |  Http 403  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http404  |  Http 404  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http406  |  Http 406  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http4xx  |  Http 4xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http5xx  |  Erros do servidor http  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  HttpResponseTime  |  Tempo de Resposta  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  IoOtherBytesPerSecond  |  IO Outros Bytes por Segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  IoOtherOperationsPerSecond  |  IO outras operações por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  IoReadBytesPerSecond  |  IO Ler Bytes por Segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  IoReadOperationsPerSecond  |  IO Ler Operações por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  IoWriteBytesPerSecond  |  IO Escrever Bytes por Segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  IoWriteOperationsPerSecond  |  IO Write Operations por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Conjunto de Funções de Memória  |  Conjunto de trabalho de memória  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  PrivateBytes  |  Bytes Privados  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Pedidos  |  Pedidos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  RequestsInApplicationQueue  |  Pedidos na fila de candidaturas  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Fios  |  Contagem de fios  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  TotalAppDomains  |  Domínios totais de aplicativos  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  TotalAppDomínios Descarregados  |  Total de domínios de aplicativos descarregados  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  AppConnections  |  Ligações  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Conjunto de trabalho de memória média  |  Conjunto de trabalho médio da memória  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Tempo médio de resposta  |  Tempo médio de resposta  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  BytesReceived  |  Dados em  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  BytesSent  |  Dados out  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  CpuTime  |  Tempo cpu  |  Segundos  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  CurrentAssemblies  |  Assembleias atuais  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Contagem de execuções de funções  |  Contagem de execução de funções  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Unidades de Execução de Funções  |  Unidades de Execução de Funções  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Gen0Collections  |  Coleções de lixo gen 0  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Gen1Collections  |  Coleções de lixo da Gen 1  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Gen2Collections  |  Coleções de lixo gen 2  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Alças  |  N.º de Identificadores  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  HealthCheckStatus  |  Estado do controlo de saúde  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http101  |  Http 101  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http2xx  |  Http 2xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http3xx  |  Http 3xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http401  |  Http 401  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http403  |  Http 403  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http404  |  Http 404  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http406  |  Http 406  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http4xx  |  Http 4xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http5xx  |  Erros do servidor http  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  HttpResponseTime  |  Tempo de Resposta  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  IoOtherBytesPerSecond  |  IO Outros Bytes por Segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  IoOtherOperationsPerSecond  |  IO outras operações por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  IoReadBytesPerSecond  |  IO Ler Bytes por Segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  IoReadOperationsPerSecond  |  IO Ler Operações por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  IoWriteBytesPerSecond  |  IO Escrever Bytes por Segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  IoWriteOperationsPerSecond  |  IO Write Operations por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Conjunto de Funções de Memória  |  Conjunto de trabalho de memória  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  PrivateBytes  |  Bytes Privados  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Pedidos  |  Pedidos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  RequestsInApplicationQueue  |  Pedidos na fila de candidaturas  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Fios  |  Contagem de fios  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  TotalAppDomains  |  Domínios totais de aplicativos  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  TotalAppDomínios Descarregados  |  Total de domínios de aplicativos descarregados  |  Contagem  |  Média | 
