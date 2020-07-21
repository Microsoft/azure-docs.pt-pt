---
title: Métricas da plataforma Azure Monitor exportáveis através de Definições de Diagnóstico
description: Lista de métricas disponíveis para cada tipo de recurso com monitor Azure.
services: azure-monitor
ms.topic: reference
ms.date: 03/30/2020
ms.subservice: metrics
ms.openlocfilehash: f0a8fd186862cf95ebdbb2d5bd92d8ff860b3ba1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515483"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Métricas da plataforma Azure Monitor exportáveis através de Definições de Diagnóstico

O Azure Monitor fornece [métricas da plataforma](data-platform-metrics.md) por padrão sem configuração. Fornece várias formas de interagir com as métricas da plataforma, incluindo cartografá-las no portal, acessá-las através da API REST, ou questioná-las usando PowerShell ou CLI. Consulte [as métricas suportadas](metrics-supported.md) para uma lista completa das métricas da plataforma atualmente disponíveis com o pipeline métrico consolidado do Azure Monitor. Para consultar e aceder a estas métricas, utilize a [versão api 2018-01-01](/rest/api/monitor/metricdefinitions). Outras métricas podem estar disponíveis no portal ou usando APIs legados.

Pode exportar as métricas da plataforma do gasoduto Azure monitor para outros locais de duas maneiras.
1. Utilização de [definições de diagnóstico](diagnostic-settings.md) para enviar para Log Analytics, Event Hubs ou Azure Storage.
2. Use as [métricas REST API](/rest/api/monitor/metrics/list)

Devido aos meandros no backend do Monitor Azure, nem todas as métricas são exportáveis usando configurações de diagnóstico. O quadro abaixo listas que podem e não podem ser exportadas com configurações de diagnóstico.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Alteração ao comportamento dos NULLs e dos valores Zero 
 
Para as métricas da plataforma que podem ser exportadas através de configurações de diagnóstico, existem algumas métricas para as quais o Azure Monitor interpreta '0s' como 'Nulos'. Isto causou alguma confusão entre os verdadeiros '0' (emitidos por recurso) e os '0' (Nulos). Em breve, ocorrerá uma alteração e as métricas da plataforma exportadas através de definições de diagnóstico deixarão de exportar '0s', a menos que tenham sido verdadeiramente emitidas pelo recurso subjacente. 

> [!CAUTION]
> A mudança de comportamento acima descrita está prevista para 1 de junho de 2020.

Atenção:

1.  Se eliminar um grupo de recursos ou um recurso específico, os dados métricos dos recursos efetuados deixarão de ser enviados para destinos de exportação de definição de diagnóstico. Ou seja, deixará de aparecer nos Centros de Eventos, Contas de Armazenamento e Log Analytics Workspaces.
2.  Esta melhoria estará disponível em todas as nuvens públicas e privadas.
3.  Esta alteração não terá impacto no comportamento de nenhuma das seguintes experiências: 
   - Registos de Recursos da Plataforma exportados através de Definições de Diagnóstico
   - Gráfico de métricas no Explorador de Métricas
   - Alertando nas métricas da plataforma
 
## <a name="metrics-exportable-table"></a>Tabela exportável de métricas 

A tabela contém as seguintes colunas. 
- Exportável através de Definições de Diagnóstico? 
- Efetuado por NU / 0 
- ResourceType 
- Métrica 
- Nome métrico do Jogo
- Unidade 
- AgregaçãoType


> [!NOTE]
> A tabela abaixo pode ter uma barra de deslocal horizontal na parte inferior. Se acredita que está a perder informação, verifique se a barra de pergaminho é todo o caminho para a esquerda.  


| Exportável através de Definições de Diagnóstico?  | Já emitem NULLs |  ResourceType  |  Métrica  |  Nome métrico do Jogo  |  Unidade  |  AgregaçãoType | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| Sim****  | Não |  Microsoft.AnalysisServices/servidores  |  CleanerCurrentPrice  |  Memória: Preço corrente mais limpo  |  de palavras  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servidores  |  CleanerMemoryNonshrinkable  |  Memória: Memória limpa não é insuportável  |  Bytes  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servidores  |  LimpadorMesshrinkable  |  Memória: Memória mais limpa encolhível  |  Bytes  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servidores  |  CommandPoolBusyThreads  |  Threads: Linhas movimentadas da piscina de comando  |  de palavras  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servidores  |  CommandPoolIdleThreads  |  Fios: Fios inativos da piscina de comando  |  de palavras  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servidores  |  ComandoPoolJobQueueLength  |  Comprimento da fila de trabalho da piscina de comando  |  de palavras  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servidores  |  Correntes Deconhecões  |  Ligação: Ligações atuais  |  de palavras  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servidores  |  Atuais Sesessãos de 2009  |  Sessões de Utilizador atuais  |  de palavras  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servidores  |  LongParsingBusyThreads  |  Threads: Longas análises de fios ocupados  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  LongParsingIdleThreads  |  Threads: Fios longos de análise ocioso  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  LongParsingJobQueueLength  |  Threads: Comprimento da fila de trabalho de análise longa  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  mashup_engine_memory_metric  |  Memória do motor M  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  mashup_engine_private_bytes_metric  |  M Motor Private Bytes  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  mashup_engine_qpu_metric  |  QPU do motor M  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  mashup_engine_virtual_bytes_metric  |  M Motor Virtual Bytes  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  memory_metric  |  Memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  memory_thrashing_metric  |  Degradação de Memória  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  MemoryLimitHard  |  Memória: Limite de memória difícil  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  MemóriaLimithigh  |  Memória: Limite de memória Alto  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  MemoryLimitLow  |  Memória: Limite de memória baixo  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  MemoryLimitVertiPaq  |  Memória: Limite de memória VertiPaq  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  MemóriaSage  |  Memória: Utilização da memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  private_bytes_metric  |  Bytes privados  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  ProcessamentoPoolBusyIOJobThreads  |  Threads: Processamento de piscina ocupada linhas de trabalho de I/O  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  ProcessamentoPoolBusyNonIOThreads  |  Fios: Piscina de processamento ocupada fios não-I/O  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  ProcessamentoPoolIdleIOJobThreads  |  Fios: Processamento de linhas de trabalho I/O inativas da piscina  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  ProcessamentoPoolIdleNonIOThreads  |  Fios: Processamento de piscinas ociosas fios não-I/O  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  ProcessamentoPoolIOJobQueueLength  |  Threads: Processamento de piscina I/O comprimento da fila de trabalho  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  ProcessamentoPoolJobQueueLength  |  Processamento de comprimento da fila de trabalho da piscina  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  qpu_metric  |  QPU  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  QueryPoolBusyThreads  |  Linhas ocupadas da piscina de consulta  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  QueryPoolIdleThreads  |  Fios: Linhas ociosas da piscina de consulta  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  QueryPoolJobQueueLength  |  Threads: Comprimento da fila do trabalho da piscina de consulta  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  Quota  |  Memória: Quota  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  Quota Bloqueada  |  Memória: Quota Bloqueada  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  RowsConvertedPerSec  |  Processamento: Linhas convertidas por seg  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  RowsReadPerSec  |  Processamento: Linhas lidas por seg  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  RowsWrittenPerSec  |  Processamento: Linhas escritas por seg  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  ShortParsingBusyThreads  |  Threads: Threads de análise curta  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  ShortParsingIdleThreads  |  Fios: Fios curtos de parsing ocioso  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  ShortParsingJobQueueLength  |  Threads: Comprimento da fila do trabalho de análise curta  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  SuccessfullConnectionsPerSec  |  Conexões de sucesso por seg  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  TotalConnectionFailures  |  Falhas totais de ligação  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  TotalConnectionRequests  |  Total de pedidos de conexão  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  VertiPaqNonpaged  |  Memória: VertiPaq Não Pageed  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  VertiPaqPaged  |  Memória: VertiPaq Paged  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servidores  |  virtual_bytes_metric  |  Bytes Virtuais  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  BackendDuration  |  Duração dos Pedidos de Backend  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ApiManagement/service  |  Capacidade  |  Capacidade  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  Duração  |  Duração geral dos pedidos de gateway  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventHubDroppedEvents  |  Eventos do EventHub abandonados  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventosHubRejectedEvents  |  Eventos de EventHub rejeitados  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventoSHubSuccessfulEvents  |  Eventos de sucesso EventHub  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventHubThrottledEvents  |  Eventos Throttled EventHub  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventHubTimedoutEvents  |  Eventos Timed out EventHub  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventHubTotalBytesSent  |  Tamanho dos eventos Do EventHub  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventHubTotalEvents  |  Total de eventos EventHub  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventHubTotalFailedEvents  |  Eventos falhados do EventHub  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  Requessos Falhados  |  Pedidos de Gateway falhados (Deprecados)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  Outros Requests  |  Outros pedidos de gateway (precotados)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  Pedidos  |  Pedidos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  Requess de sucesso  |  Pedidos de Gateway bem sucedidos (Deprecados)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  TotalRequests  |  Total de pedidos de gateway (precotado)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  Requessionais não autorizados  |  Pedidos de Gateway não autorizados (Deprecados)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  AppCpuUsagePercentage  |  Percentagem de Utilização cpu de aplicação  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  AppMemoryCommitted  |  Memória de aplicativo atribuída  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  AppMemoryMax  |  App Memory Max  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  AppMemoryUsed  |  Memória de aplicativo usada  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  GCPauseTotalCount  |  Contagem de pausas GC  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  GCPauseTotalTime  |  GC Pausa Tempo Total  |  Milissegundos  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  MaxOldGenMemoryPoolBytes  |  Tamanho máximo de dados de geração antiga disponível  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  OldGenMemoryPoolBytes  |  Tamanho dos dados da geração velha  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  OldGenPromotedBytes  |  Promover para o tamanho dos dados da geração velha  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  SystemCpuUsagePercentage  |  Percentagem de Utilização cpu do sistema  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  TomcatErrorCount  |  Erro Global de Tomcat  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  TomcatReceivedBytes  |  Tomcat Total Recebido Bytes  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  TomcatRequestMaxTime  |  Tomcat Request Max Time  |  Milissegundos  |  Máximo | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  TomcatRequestTotalCount  |  Tomcat Request Contagem Total  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  TomcatRequestTotalTime  |  Pedido tomcat tempos totais  |  Milissegundos  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  TomcatResponseAvgTime  |  Tomcat Pede tempo médio  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  TomcatSentBytes  |  Tomcat Total Enviado Bytes  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  TomcatSessionActiveCurrentCount  |  Tomcat Session Alive Count  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  TomcatSessionActiveMaxCount  |  Tomcat Session Max Ative Count  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  TomcatSessionAliveMaxTime  |  Tomcat Session Max Alive Tempo  |  Milissegundos  |  Máximo | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  TomcatSessionCreatedCount  |  Tomcat Session Criou Contagem  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  TomcatSessionExpiredCount  |  Contagem expirada da sessão de Tomcat  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  TomcatSessionRejectedCount  |  Contagem rejeitada da sessão de Tomcat  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/primavera  |  YoungGenPromotedBytes  |  Promover para o tamanho de dados de geração jovem  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Automation/automation  |  TotalJob  |  Total de empregos  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Automation/automation  |  TotalUpdateDeploymentMachineRuns  |  Funciona a máquina de implementação total de atualizações  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Automation/automation  |  TotalUpdateDeploymentRuns  |  Execuções de implementação de atualização total  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Batcontas ch/batch  |  CoreCount  |  Contagem de núcleos dedicada  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Batcontas ch/batch  |  Criação NodeCount  |  Criação de Contagem de Nó  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Batcontas ch/batch  |  IdleNodeCount  |  Conde nó inativo  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Batcontas ch/batch  |  JobDeleteCompleteEvent  |  Trabalho Eliminar Eventos Completos  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Batcontas ch/batch  |  JobDeleteStartEvent  |  Trabalho eliminar eventos iniciá-lo  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Batcontas ch/batch  |  JobDisableCompleteEvent  |  Eventos completos para desativação de emprego  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Batcontas ch/batch  |  JobDisableStartEvent  |  Eventos de início de desativação de emprego  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Batcontas ch/batch  |  JobStartEvent  |  Eventos de início de emprego  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Batcontas ch/batch  |  JobTerminateCompleteEvent  |  Emprego termina eventos completos  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Batcontas ch/batch  |  JobTerminateStartEvent  |  Eventos de início de emprego terminam  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Batcontas ch/batch  |  LeavingPoolNodeCount  |  Deixando a contagem do nó de piscina  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Batcontas ch/batch  |  LowPriorityCoreCount  |  Contagem do Núcleo de BaixaPrioridade  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Batcontas ch/batch  |  OfflineNodeCount  |  Contagem de nóles offline  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Batcontas ch/batch  |  Evento PoolCreate  |  Criar Eventos de Criação de Piscinas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Batcontas ch/batch  |  PoolDeleteCompleteEvent  |  Piscina Eliminar Eventos Completos  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Batcontas ch/batch  |  PoolDeleteStartEvent  |  Pool Delete Eventos iniciá-lo  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Batcontas ch/batch  |  PoolResizeCompleteEvent  |  Piscina Redimensione Eventos Completos  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Batcontas ch/batch  |  PoolResizeStartEvent  |  Eventos de início de piscina redimensionam  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Batcontas ch/batch  |  PreemptedNodeCount  |  Contagem de nódoaista preventiva  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Batcontas ch/batch  |  RebootingNodeCount  |  Contagem de nó de reinicialização  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Batcontas ch/batch  |  ReimagingNodeCount  |  Contagem de nó de reimaging  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Batcontas ch/batch  |  RunningNodeCount  |  Contagem de nó de corrida  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Batcontas ch/batch  |  InícioNodeCount  |  Contagem inicial do nó  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Batcontas ch/batch  |  StartTaskFailedNodeCount  |  Início da contagem de nó falhado da tarefa  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Batcontas ch/batch  |  TaskCompleteEvent  |  Eventos completos de tarefas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Batcontas ch/batch  |  TaskFailEvent  |  Eventos de Falha de Tarefa  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Batcontas ch/batch  |  TaskStartEvent  |  Eventos de início de tarefa  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Batcontas ch/batch  |  TotalLowPriorityNodeCount  |  Contagem de nó de baixa prioridade  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Batcontas ch/batch  |  TotalNodeCount  |  Conde de Nó dedicado  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Batcontas ch/batch  |  Não utilizávelNodeCount  |  Contagem de nó inutilizáveis  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Batcontas ch/batch  |  WaitingForStartTaskNodeCount  |  À espera da contagem de nó de tarefa inicial  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Núcleos Ativos  |  Núcleos Ativos  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Nóns ativos  |  Nóns ativos  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Núcleos Ociosos  |  Núcleos Ociosos  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Nódoas Ociosas  |  Nódoas Ociosas  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Trabalho Concluído  |  Trabalho Concluído  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Trabalho submetido  |  Trabalho submetido  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Deixando os Núcleos  |  Deixando os Núcleos  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Deixando os nóns  |  Deixando os nóns  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Núcleos Preempted  |  Núcleos Preempted  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Nómadas Preempted  |  Nómadas Preempted  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Percentagem de Utilização de Cots  |  Percentagem de Utilização de Cots  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Núcleos Totais  |  Núcleos Totais  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Nómada Total  |  Nómada Total  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Núcleos inutilizáveis  |  Núcleos inutilizáveis  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Nódes Inutilizáveis  |  Nódes Inutilizáveis  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  LigaçãoAccepted  |  Conexões Aceites  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  ConexãoActiva  |  Ligações Ativas  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  Conexão Tratada  |  Conexões manuseidas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  CpuUsagePercentageInDouble  |  Percentagem de Utilização cpu  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  IOReadBytes  |  IO Ler Bytes  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  IOWriteBytes  |  IO Escrever Bytes  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  MemóriaLimite  |  Limite de memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  MemóriaSage  |  Utilização de Memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  MemoryUsagePercentageInDouble  |  Percentagem de Utilização de Memória  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  Processos pendentes  |  Transações Pendentes  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  Blocos Processados  |  Blocos processados  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  Transações processadas  |  Transações Processadas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  QueuedTransacções  |  Transações em fila  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  Pedido Tratado  |  Pedidos Tratados  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  ArmazenamentoUsage  |  Utilização de armazenamento  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits  |  Acertos na Cache  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits0  |  Cache Hits (Fragmento 0)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits1  |  Cache Hits (Fragmento 1)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits2  |  Cache Hits (Fragmento 2)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits3  |  Cache Hits (Fragmento 3)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits4  |  Cache Hits (Fragmento 4)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits5  |  Cache Hits (Fragmento 5)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits6  |  Cache Hits (Fragmento 6)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits7  |  Cache Hits (Fragmento 7)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits8  |  Cache Hits (Fragmento 8)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits9  |  Cache Hits (Fragmento 9)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheLatency  |  Microsegundos de latência de cache (pré-visualização)  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses  |  Falhas de Acerto na Cache  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses0  |  Cache Misses (Fragmento 0)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses1  |  Cache Misses (Fragmento 1)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses2  |  Cache Misses (Fragmento 2)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses3  |  Cache Misses (Fragmento 3)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses4  |  Cache Misses (Fragmento 4)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses5  |  Cache Misses (Fragmento 5)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses6  |  Cache Misses (Fragmento 6)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses7  |  Cache Misses (Fragmento 7)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses8  |  Cache Misses (Fragmento 8)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses9  |  Cache Misses (Fragmento 9)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheRead  |  Leitura da Cache  |  BytesPerSecond  |  Máximo | 
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
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheDite  |  Escrita na Cache  |  BytesPerSecond  |  Máximo | 
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
| **Sim**  | Não |  Microsoft.Cache/redis  |  connectedclients  |  Clientes Ligados  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  connectedclients0  |  Clientes Conectados (Fragmento 0)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  connectedclients1  |  Clientes Conectados (Fragmento 1)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  connectedclients2  |  Clientes Conectados (Fragmento 2)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  connectedclients3  |  Clientes Conectados (Fragmento 3)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  connectedclients4  |  Clientes Conectados (Fragmento 4)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  connectedclients5  |  Clientes Conectados (Fragmento 5)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  connectedclients6  |  Clientes Conectados (Fragmento 6)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  connectedclients7  |  Clientes Conectados (Fragmento 7)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  connectedclients8  |  Clientes Conectados (Fragmento 8)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  connectedclients9  |  Clientes Conectados (Fragmento 9)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  erros  |  Erros  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  chaves despejadas  |  Chaves Excluídas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  despejadokeys0  |  Chaves despejadas (Fragmento 0)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  chaves despejadas1  |  Chaves despejadas (Fragmento 1)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  despejadokeys2  |  Chaves despejadas (Fragmento 2)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  despejadokeys3  |  Chaves despejadas (Fragmento 3)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  despejadokeys4  |  Chaves despejadas (Fragmento 4)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  despejadokeys5  |  Chaves despejadas (Fragmento 5)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  despejadokeys6  |  Chaves despejadas (Fragmento 6)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  despejadokeys7  |  Chaves despejadas (Fragmento 7)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  despejadokeys8  |  Chaves despejadas (Fragmento 8)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  despejadokeys9  |  Chaves despejadas (Fragmento 9)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  chaves caducadas  |  Chaves Expiradas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  40keys0  |  Chaves expiradas (Fragmento 0)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  400keys validade1  |  Chaves expiradas (Fragmento 1)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  400keys expirados2  |  Chaves expiradas (Fragmento 2)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  400keys3  |  Chaves expiradas (Fragmento 3)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  4keys expirados  |  Chaves expiradas (Fragmento 4)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  400keys5  |  Chaves expiradas (Fragmento 5)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  400keys6  |  Chaves expiradas (Fragmento 6)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  400keys validade  |  Chaves expiradas (Fragmento 7)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  400keys8  |  Chaves expiradas (Fragmento 8)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  expirarkeys9  |  Chaves expiradas (Fragmento 9)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  obter commões  |  Obtenções  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  obter commands0  |  Recebe (Fragmento 0)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  obter commands1  |  Recebe (Fragmento 1)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  obter commands2  |  Recebe (Fragmento 2)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  obter commands3  |  Recebe (Fragmento 3)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  obter commands4  |  Recebe (Fragmento 4)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  obter commands5  |  Recebe (Fragmento 5)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  obter commands6  |  Recebe (Fragmento 6)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  obter commands7  |  Recebe (Fragmento 7)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  obter commands8  |  Recebe (Fragmento 8)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  obter commands9  |  Recebe (Fragmento 9)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operaçõesPerSecond  |  Operações por Segundo  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operaçõesPerSecond0  |  Operações por segundo (Fragmento 0)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operaçõesPerSecond1  |  Operações por segundo (Fragmento 1)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operaçõesPerSecond2  |  Operações por segundo (Fragmento 2)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operaçõesPerSecond3  |  Operações por segundo (Fragmento 3)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operaçõesPerSecond4  |  Operações por segundo (Fragmento 4)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operaçõesPerSecond5  |  Operações por segundo (Fragmento 5)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operaçõesPerSecond6  |  Operações por segundo (Fragmento 6)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operaçõesPerSecond7  |  Operações por segundo (Fragmento 7)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operaçõesPerSecond8  |  Operações por segundo (Fragmento 8)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operaçõesPerSecond9  |  Operações por segundo (Fragmento 9)  |  de palavras  |  Máximo | 
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
| **Sim**  | Não |  Microsoft.Cache/redis  |  servidorAcar  |  Carga do Servidor  |  Percentagem  |  Máximo | 
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
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands  |  Definições  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands0  |  Conjuntos (Fragmento 0)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands1  |  Conjuntos (Fragmento 1)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands2  |  Conjuntos (Fragmento 2)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands3  |  Conjuntos (Fragmento 3)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands4  |  Conjuntos (Fragmento 4)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands5  |  Conjuntos (Fragmento 5)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands6  |  Conjuntos (Fragmento 6)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands7  |  Conjuntos (Fragmento 7)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands8  |  Conjuntos (Fragmento 8)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands9  |  Conjuntos (Fragmento 9)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalcommands processado  |  Total de Operações  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalcommands processado0  |  Total de operações (Fragmento 0)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalcommandsprocessado1  |  Total de operações (Fragmento 1)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalcommands processado2  |  Total de Operações (Fragmento 2)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalcommands processado3  |  Total de operações (Fragmento 3)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalcommandsprocessado4  |  Total de operações (Fragmento 4)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalcommands processado5  |  Total de operações (Fragmento 5)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalcommands processado6  |  Total de operações (Fragmento 6)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalcommands processado7  |  Total de operações (Fragmento 7)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalcommandsprocessado8  |  Total de operações (Fragmento 8)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalcommandsprocessado9  |  Total de operações (Fragmento 9)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalkeys  |  Chaves totais  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalkeys0  |  Chaves totais (Fragmento 0)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalkeys1  |  Chaves totais (Fragmento 1)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalkeys2  |  Chaves totais (Fragmento 2)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalkeys3  |  Chaves totais (Fragmento 3)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalkeys4  |  Chaves totais (Fragmento 4)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalkeys5  |  Chaves totais (Fragmento 5)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalkeys6  |  Chaves totais (Fragmento 6)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalkeys7  |  Chaves totais (Fragmento 7)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalkeys8  |  Chaves totais (Fragmento 8)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalkeys9  |  Chaves totais (Fragmento 9)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemory  |  Memória Utilizada  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usadomemory0  |  Memória Usada (Fragmento 0)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemory1  |  Memória Usada (Fragmento 1)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemory2  |  Memória Usada (Fragmento 2)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usadomemory3  |  Memória Usada (Fragmento 3)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usadomemory4  |  Memória Usada (Fragmento 4)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usadomemory5  |  Memória Usada (Fragmento 5)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usadomemory6  |  Memória Usada (Fragmento 6)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usadomemory7  |  Memória Usada (Fragmento 7)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usadomemory8  |  Memória Usada (Fragmento 8)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemory9  |  Memória Usada (Fragmento 9)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usuário  |  Percentagem de Memória Utilizada  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemoryRss  |  RSS de memória usada  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemoryRss0  |  RSS de memória usado (fragmento 0)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemoryRss1  |  RSS de memória usado (fragmento 1)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemoryRss2  |  RSS de memória usado (fragmento 2)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemoryRss3  |  RSS de memória usado (fragmento 3)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemoryRss4  |  RSS de memória usado (fragmento 4)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemoryRss5  |  RSS de memória usado (fragmento 5)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemoryRss6  |  RSS de memória usado (fragmento 6)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemoryRss7  |  RSS de memória usado (fragmento 7)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemoryRss8  |  RSS de memória usado (fragmento 8)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemoryRss9  |  RSS de memória usado (fragmento 9)  |  Bytes  |  Máximo | 
| Não  | Não |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Discos de leitura bytes/sec  |  Leitura de Disco  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Operações de leitura de disco/sec  |  Operações de leitura de disco/sec  |  CondePerSecond  |  Média | 
| Não  | Não |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Discos De Escrita Bytes/Sec  |  Escrita em Disco  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Operações de escrita de discos/seg  |  Operações de escrita de discos/seg  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Entrada na Rede  |  Entrada na Rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Saída da Rede  |  Saída da Rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Percentagem da CPU  |  Percentagem da CPU  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.ClassicCompute/virtualMachines  |  Discos de leitura bytes/sec  |  Leitura de Disco  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicCompute/virtualMachines  |  Operações de leitura de disco/sec  |  Operações de leitura de disco/sec  |  CondePerSecond  |  Média | 
| Não  | Não |  Microsoft.ClassicCompute/virtualMachines  |  Discos De Escrita Bytes/Sec  |  Escrita em Disco  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicCompute/virtualMachines  |  Operações de escrita de discos/seg  |  Operações de escrita de discos/seg  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicCompute/virtualMachines  |  Entrada na Rede  |  Entrada na Rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicCompute/virtualMachines  |  Saída da Rede  |  Saída da Rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicCompute/virtualMachines  |  Percentagem da CPU  |  Percentagem da CPU  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAcontas  |  Disponibilidade  |  Disponibilidade  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAcontas  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAcontas  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAcontas  |  SucessoE2ELatency  |  Latência de E2E Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAcontas  |  SucessoServerLatency  |  Latência do servidor de sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAcontas  |  Transações  |  Transações  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.ClassicStorage/storageAcontas  |  Capacidade Usada  |  Capacidade utilizada  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Disponibilidade  |  Disponibilidade  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Capacidade blob  |  Capacidade blob  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  BlobCount  |  Contagem de blobs  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Contagem de contentores  |  Contagem de contentores blob  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Capacidade de Indexação  |  Capacidade de Índice  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  SucessoE2ELatency  |  Latência de E2E Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  SucessoServerLatency  |  Latência do servidor de sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Transações  |  Transações  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAcons/fileServices  |  Disponibilidade  |  Disponibilidade  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAcons/fileServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.ClassicStorage/storageAcons/fileServices  |  Capacidade de Ficheiros  |  Capacidade de Arquivo  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ClassicStorage/storageAcons/fileServices  |  Contagem de ficheiros  |  Contagem de ficheiros  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.ClassicStorage/storageAcons/fileServices  |  FileShareCount  |  Contagem de partilha de ficheiros  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.ClassicStorage/storageAcons/fileServices  |  FileShareQuota  |  Tamanho da quota de ações de arquivo  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ClassicStorage/storageAcons/fileServices  |  FileShareSnapshotCount  |  Contagem de snapshot de partilha de ficheiros  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.ClassicStorage/storageAcons/fileServices  |  FileShareSnapshotSize  |  Tamanho do snapshot de partilha de arquivo  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAcons/fileServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAcons/fileServices  |  SucessoE2ELatency  |  Latência de E2E Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAcons/fileServices  |  SucessoServerLatency  |  Latência do servidor de sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAcons/fileServices  |  Transações  |  Transações  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Disponibilidade  |  Disponibilidade  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Capacidade de Fila  |  Capacidade de fila  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Contagem de filas  |  Contagem de filas  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  QueueMessageCount  |  Contagem de mensagens de fila  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  SucessoE2ELatency  |  Latência de E2E Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  SucessoServerLatency  |  Latência do servidor de sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Transações  |  Transações  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Disponibilidade  |  Disponibilidade  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  SucessoE2ELatency  |  Latência de E2E Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  SucessoServerLatency  |  Latência do servidor de sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Capacidade de Mesa  |  Capacidade de mesa  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  MesaCount  |  Contagem de tabelas  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  TabelaseconagemCount  |  Contagem de Entidades de Tabela  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Transações  |  Transações  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/contas  |  BlockedCalls  |  Chamadas bloqueadas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/contas  |  Personagens Treinados  |  Personagens treinados  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/contas  |  CaracteresTranslatados  |  Caracteres Traduzidos  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/contas  |  ClientErrors  |  Erros do Cliente  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/contas  |  DataIn  |  Dados em  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/contas  |  DataOut  |  Data out  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/contas  |  Latência  |  Latência  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.CognitiveServices/contas  |  ServerErrors  |  Erros do servidor  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/contas  |  DiscursoSSessionDuration  |  Duração da sessão de discurso  |  Segundos  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/contas  |  Chamadas de sucesso  |  Chamadas bem sucedidas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/contas  |  Total de Chamadas  |  Total de chamadas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/contas  |  TotalErrors  |  Total de Erros  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/contas  |  TotalTokenCalls  |  Total de chamadas simbólicas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/contas  |  Total de transações  |  Total de Transações  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Créditos CPU Consumidos  |  Créditos CPU Consumidos  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Créditos CPU Remanescentes  |  Créditos CPU Remanescentes  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Profundidade da Fila do Disco de Dados  |  Profundidade da fila do disco de dados (pré-visualização)  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Data Disk Ler Bytes/seg  |  Data Disk Read Bytes/Sec (Pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de leitura de discos de dados/seg  |  Operações de leitura de discos de dados/seg (pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Bytes/seg de escrita de disco de dados  |  Data Disk Write Bytes/Sec (Pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de escrita de discos de dados/seg  |  Operações de escrita de discos de dados/seg (pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Bytes de leitura de disco  |  Bytes de leitura de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de leitura de disco/sec  |  Operações de leitura de disco/sec  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Bytes de escrita de disco  |  Bytes de escrita de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de escrita de discos/seg  |  Operações de escrita de discos/seg  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Fluxos de Entrada  |  Fluxos de Entrada  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Fluxos de entrada Taxa máxima de criação  |  Fluxos de entrada Taxa máxima de criação (pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Entrada na Rede  |  Rede em Faturação (Depreciada)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Rede no total  |  Rede no total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Saída da Rede  |  Network out Billable (Precotado)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Rede Total  |  Rede Total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Profundidade da Fila do Disco do SO  |  Profundidade da fila do disco os (pré-visualização)  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Leitura de discos de OS Bytes/seg  |  Os Discos de Leitura de Bytes/Sec (Pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de leitura de disco de OS/Sec  |  Operações de leitura de disco de OS/Seg (Pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Os Discos de Escrita bytes/seg  |  Os Discos de Escrita de Discos/Sec (Pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de escrita de discos de OS/Sec  |  Operações de escrita de discos de OS/Seg (Pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  OS por Disco QD  |  CO Disk QD (precotado)  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  OS por Disco Ler Bytes/seg  |  Os Discos De Leitura Bytes/Sec (Deprecado)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de leitura por disco/seg  |  Operações de leitura de discos de OS/Sec (Depreciadas)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  OS por Disco Escrever Bytes/seg  |  Os Discos De Discos/Sec (Deprecado)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Os por Operações de Escrita de Disco/Sec  |  Operações de escrita de discos de OS/Sec (Depreciadas)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Fluxos de saída  |  Fluxos de saída  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Taxa máxima de criação de fluxos de saída  |  Taxa máxima de criação de fluxos de saída (pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Por Disco QD  |  QD do disco de dados (precotado)  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Per Disk Read Bytes/sec  |  Data Disk Read Bytes/Sec (Deprecado)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Por Operações de leitura de disco/sec  |  Operações de leitura de discos de dados/seg (depreciadas)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Per Disk Write Bytes/sec  |  Data Disk Write Bytes/Sec (Deprecado)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Por operações de escrita de disco/sec  |  Operações de escrita de discos de dados/seg (depreciadas)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Percentagem da CPU  |  Percentagem da CPU  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Sucesso de leitura de cache de disco de dados premium  |  Sucesso de leitura de cache de disco de dados premium (pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Cache de disco de dados premium Ler Miss  |  Cache de disco de dados premium Ler Miss (Pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Premium OS Disk Cache Ler Hit  |  Premium OS Disk Cache Read Hit (Pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Premium OS Cache De Disco Ler Miss  |  Cache de disco premium OS Ler Miss (Pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Créditos CPU Consumidos  |  Créditos CPU Consumidos  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Créditos CPU Remanescentes  |  Créditos CPU Remanescentes  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Profundidade da Fila do Disco de Dados  |  Profundidade da fila do disco de dados (pré-visualização)  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Data Disk Ler Bytes/seg  |  Data Disk Read Bytes/Sec (Pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de leitura de discos de dados/seg  |  Operações de leitura de discos de dados/seg (pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Bytes/seg de escrita de disco de dados  |  Data Disk Write Bytes/Sec (Pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de escrita de discos de dados/seg  |  Operações de escrita de discos de dados/seg (pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Bytes de leitura de disco  |  Bytes de leitura de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de leitura de disco/sec  |  Operações de leitura de disco/sec  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Bytes de escrita de disco  |  Bytes de escrita de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de escrita de discos/seg  |  Operações de escrita de discos/seg  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Fluxos de Entrada  |  Fluxos de Entrada  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Fluxos de entrada Taxa máxima de criação  |  Fluxos de entrada Taxa máxima de criação (pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Entrada na Rede  |  Rede em Faturação (Depreciada)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Rede no total  |  Rede no total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Saída da Rede  |  Network out Billable (Precotado)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Rede Total  |  Rede Total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Profundidade da Fila do Disco do SO  |  Profundidade da fila do disco os (pré-visualização)  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Leitura de discos de OS Bytes/seg  |  Os Discos de Leitura de Bytes/Sec (Pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de leitura de disco de OS/Sec  |  Operações de leitura de disco de OS/Seg (Pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Os Discos de Escrita bytes/seg  |  Os Discos de Escrita de Discos/Sec (Pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de escrita de discos de OS/Sec  |  Operações de escrita de discos de OS/Seg (Pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  OS por Disco QD  |  CO Disk QD (precotado)  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  OS por Disco Ler Bytes/seg  |  Os Discos De Leitura Bytes/Sec (Deprecado)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de leitura por disco/seg  |  Operações de leitura de discos de OS/Sec (Depreciadas)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  OS por Disco Escrever Bytes/seg  |  Os Discos De Discos/Sec (Deprecado)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Os por Operações de Escrita de Disco/Sec  |  Operações de escrita de discos de OS/Sec (Depreciadas)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Fluxos de saída  |  Fluxos de saída  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Taxa máxima de criação de fluxos de saída  |  Taxa máxima de criação de fluxos de saída (pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Por Disco QD  |  QD do disco de dados (precotado)  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Per Disk Read Bytes/sec  |  Data Disk Read Bytes/Sec (Deprecado)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Por Operações de leitura de disco/sec  |  Operações de leitura de discos de dados/seg (depreciadas)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Per Disk Write Bytes/sec  |  Data Disk Write Bytes/Sec (Deprecado)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Por operações de escrita de disco/sec  |  Operações de escrita de discos de dados/seg (depreciadas)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Percentagem da CPU  |  Percentagem da CPU  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Sucesso de leitura de cache de disco de dados premium  |  Sucesso de leitura de cache de disco de dados premium (pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Cache de disco de dados premium Ler Miss  |  Cache de disco de dados premium Ler Miss (Pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Premium OS Disk Cache Ler Hit  |  Premium OS Disk Cache Read Hit (Pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Premium OS Cache De Disco Ler Miss  |  Cache de disco premium OS Ler Miss (Pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Créditos CPU Consumidos  |  Créditos CPU Consumidos  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Créditos CPU Remanescentes  |  Créditos CPU Remanescentes  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Profundidade da Fila do Disco de Dados  |  Profundidade da fila do disco de dados (pré-visualização)  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Data Disk Ler Bytes/seg  |  Data Disk Read Bytes/Sec (Pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de leitura de discos de dados/seg  |  Operações de leitura de discos de dados/seg (pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bytes/seg de escrita de disco de dados  |  Data Disk Write Bytes/Sec (Pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de escrita de discos de dados/seg  |  Operações de escrita de discos de dados/seg (pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bytes de leitura de disco  |  Bytes de leitura de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de leitura de disco/sec  |  Operações de leitura de disco/sec  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bytes de escrita de disco  |  Bytes de escrita de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de escrita de discos/seg  |  Operações de escrita de discos/seg  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Fluxos de Entrada  |  Fluxos de Entrada  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Fluxos de entrada Taxa máxima de criação  |  Fluxos de entrada Taxa máxima de criação (pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Entrada na Rede  |  Rede em Faturação (Depreciada)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Rede no total  |  Rede no total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Saída da Rede  |  Network out Billable (Precotado)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Rede Total  |  Rede Total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Profundidade da Fila do Disco do SO  |  Profundidade da fila do disco os (pré-visualização)  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Leitura de discos de OS Bytes/seg  |  Os Discos de Leitura de Bytes/Sec (Pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de leitura de disco de OS/Sec  |  Operações de leitura de disco de OS/Seg (Pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Os Discos de Escrita bytes/seg  |  Os Discos de Escrita de Discos/Sec (Pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de escrita de discos de OS/Sec  |  Operações de escrita de discos de OS/Seg (Pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS por Disco QD  |  CO Disk QD (precotado)  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS por Disco Ler Bytes/seg  |  Os Discos De Leitura Bytes/Sec (Deprecado)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de leitura por disco/seg  |  Operações de leitura de discos de OS/Sec (Depreciadas)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS por Disco Escrever Bytes/seg  |  Os Discos De Discos/Sec (Deprecado)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Os por Operações de Escrita de Disco/Sec  |  Operações de escrita de discos de OS/Sec (Depreciadas)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Fluxos de saída  |  Fluxos de saída  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Taxa máxima de criação de fluxos de saída  |  Taxa máxima de criação de fluxos de saída (pré-visualização)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Por Disco QD  |  QD do disco de dados (precotado)  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Per Disk Read Bytes/sec  |  Data Disk Read Bytes/Sec (Deprecado)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Por Operações de leitura de disco/sec  |  Operações de leitura de discos de dados/seg (depreciadas)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Per Disk Write Bytes/sec  |  Data Disk Write Bytes/Sec (Deprecado)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Por operações de escrita de disco/sec  |  Operações de escrita de discos de dados/seg (depreciadas)  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Percentagem da CPU  |  Percentagem da CPU  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Sucesso de leitura de cache de disco de dados premium  |  Sucesso de leitura de cache de disco de dados premium (pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Cache de disco de dados premium Ler Miss  |  Cache de disco de dados premium Ler Miss (Pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Premium OS Disk Cache Ler Hit  |  Premium OS Disk Cache Read Hit (Pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Premium OS Cache De Disco Ler Miss  |  Cache de disco premium OS Ler Miss (Pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerInstance/containerGroups  |  CpuUsage  |  Utilização da CPU  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerInstance/containerGroups  |  MemóriaSage  |  Utilização de Memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesReceivedPerSecond  |  Bytes de rede recebidos por segundo  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesTransmittedPerSecond  |  Bytes de rede transmitidos por segundo  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerRegistry/registries  |  RunDuration  |  Duração do funcional  |  Milissegundos  |  Total | 
| **Sim**  | Não |  Microsoft.ContainerRegistry/registries  |  SucessoPullCount  |  Contagem de pull com sucesso  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerRegistry/registries  |  SucessoPushCount  |  Contagem de impulsos bem sucedida  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerRegistry/registries  |  TotalPullCount  |  Contagem total de pull  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerRegistry/registries  |  TotalPushCount  |  Contagem total de push  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_cpu_cores  |  Número total de núcleos cpu disponíveis num cluster gerido  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_memory_bytes  |  Quantidade total de memória disponível num cluster gerido  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ContainerService/managedClusters  |  kube_node_status_condition  |  Estados para várias condições de nó  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_phase  |  Número de cápsulas por fase  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_ready  |  Número de cápsulas no estado de Ready  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  Capacidade disponível  |  Capacidade disponível  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloud  |  Cloud Bytes Uploaded (Dispositivo)  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloudPerShare  |  Cloud Bytes Uploaded (Partilhar)  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughput  |  Download da Cloud  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughputPerShare  |  Download cloud 'Produção' (Partilhar)  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughput  |  Produção de upload de nuvens  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughputPerShare  |  Produção de upload de nuvem (partilhar)  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  Hipervmemoryutilização  |  Edge Compute - Utilização da Memória  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  HiperVVirtualProcessorutilização  |  Edge Compute - Percentagem CPU  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NICReadThroughput  |  Ler produção (rede)  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NICWriteThroughput  |  Escrever Produção (Rede)  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  Capacidade Total  |  Capacidade Total  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DataFactory/datafactories  |  Corridas Falhadas  |  Corridas falhadas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/datafactories  |  Sucesso DeRuns  |  Corridas de sucesso  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  AtividadeCancelledRuns  |  Atividade cancelada executa métricas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  AtividadeFailedRuns  |  Atividade falhada executa métricas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  AtividadeSSucceedEdRuns  |  A atividade bem sucedida executa métricas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  FactorySizeInGbUnits  |  Tamanho total da fábrica (unidade GB)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  IntegrationRuntimeAvailableMemory  |  Integração memória disponível  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  IntegraçãoRuntimeAverageTaskPickupDelay  |  Duração da fila de tempo de execução de integração  |  Segundos  |  Média | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  IntegraçãoRuntimeCpuPercentage  |  Utilização do CPU em tempo de integração  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  IntegraçãoRuntimeQueueLength  |  Comprimento da fila de tempo de execução de integração  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  MaxAllowedFactorySizeInGbUnits  |  Tamanho máximo permitido da fábrica (unidade GB)  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  MaxAllowedResourceCount  |  Contagem máxima de entidades permitidas  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  PipelineCancelledRuns  |  Gasoduto cancelado executa métricas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  PipelineFailedRuns  |  Gasoduto falhado executa métricas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  PipelineSucceededRuns  |  Pipeline bem sucedido executa métricas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  Contagem de Recursos  |  Total de entidades contam  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  TriggerCancelledRuns  |  Gatilho cancelado executa métricas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  TriggerFailedRuns  |  O gatilho falhado executa métricas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/fábricas  |  TriggerSucceeddRuns  |  O gatilho bem sucedido executa métricas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeAnalytics/contas  |  JobAUEndedCancelled  |  Tempo cancelado da AU  |  Segundos  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeAnalytics/contas  |  JobAUEndedFailure  |  Tempo AU falhado  |  Segundos  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeAnalytics/contas  |  JobAUEndedSuccess  |  Tempo de AU bem sucedido  |  Segundos  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeAnalytics/contas  |  JobEndedCancelled  |  Empregos Cancelados  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeAnalytics/contas  |  JobEndedFailure  |  Empregos Falhados  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeAnalytics/contas  |  JobEndedSuccess  |  Empregos bem sucedidos  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeStore/contas  |  DataRead  |  Leitura de Dados  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeStore/contas  |  DataWritten  |  Dados Escritos  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeStore/contas  |  ReadRequests  |  Ler Pedidos  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeStore/contas  |  TotalStorage  |  Armazenamento Total  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.DataLakeStore/contas  |  WriteRequests  |  Escrever Pedidos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  active_connections  |  Ligações Ativas  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  backup_storage_used  |  Armazenamento de backup utilizado  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  connections_failed  |  Ligações com Falhas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  cpu_percent  |  CPU por cento  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  io_consumption_percent  |  IO por cento  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  memory_percent  |  Por cento da memória  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  network_bytes_egress  |  Saída da Rede  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  network_bytes_ingress  |  Entrada na Rede  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  seconds_behind_master  |  Atraso de replicação em segundos  |  de palavras  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  serverlog_storage_limit  |  Limite de armazenamento de registo de servidor  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  serverlog_storage_percent  |  Por cento de armazenamento de registo de servidor  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  serverlog_storage_usage  |  Armazenamento de registo de servidor utilizado  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  storage_limit  |  Limite de armazenamento  |  Bytes  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  storage_percent  |  Por cento de armazenamento  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servidores  |  storage_used  |  Armazenamento utilizado  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  active_connections  |  Ligações Ativas  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  backup_storage_used  |  Armazenamento de backup utilizado  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  connections_failed  |  Ligações com Falhas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  cpu_percent  |  CPU por cento  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  io_consumption_percent  |  IO por cento  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  memory_percent  |  Por cento da memória  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  network_bytes_egress  |  Saída da Rede  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  network_bytes_ingress  |  Entrada na Rede  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  seconds_behind_master  |  Atraso de replicação em segundos  |  de palavras  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  serverlog_storage_limit  |  Limite de armazenamento de registo de servidor  |  Bytes  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  serverlog_storage_percent  |  Por cento de armazenamento de registo de servidor  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  serverlog_storage_usage  |  Armazenamento de registo de servidor utilizado  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  storage_limit  |  Limite de armazenamento  |  Bytes  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  storage_percent  |  Por cento de armazenamento  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servidores  |  storage_used  |  Armazenamento utilizado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  active_connections  |  Ligações Ativas  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  backup_storage_used  |  Armazenamento de backup utilizado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  connections_failed  |  Ligações com Falhas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  cpu_percent  |  CPU por cento  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  io_consumption_percent  |  IO por cento  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  memory_percent  |  Por cento da memória  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  network_bytes_egress  |  Saída da Rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  network_bytes_ingress  |  Entrada na Rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  pg_replica_log_delay_in_bytes  |  Max Lag através de réplicas  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  pg_replica_log_delay_in_seconds  |  Lag de réplica  |  Segundos  |  Máximo | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  serverlog_storage_limit  |  Limite de armazenamento de registo de servidor  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  serverlog_storage_percent  |  Por cento de armazenamento de registo de servidor  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  serverlog_storage_usage  |  Armazenamento de registo de servidor utilizado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  storage_limit  |  Limite de armazenamento  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  storage_percent  |  Por cento de armazenamento  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servidores  |  storage_used  |  Armazenamento utilizado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  active_connections  |  Ligações Ativas  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  cpu_percent  |  CPU por cento  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  iops  |  IOPS  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  memory_percent  |  Por cento da memória  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_egress  |  Saída da Rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_ingress  |  Entrada na Rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  storage_percent  |  Por cento de armazenamento  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  storage_used  |  Armazenamento utilizado  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/Conta  |  digitaltwins.telemetria.nós  |  Espaço reservado para telemetria de gémeos digitais  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.abandon.success  |  Mensagens C2D abandonadas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.complete.success  |  Entregas de mensagens C2D concluídas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.reject.success  |  Mensagens C2D rejeitadas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.methods.falha  |  Invocações de método direto falhadas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.methods.requestSize  |  Tamanho do pedido de invocações de método direto  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.methods.responseSize  |  Tamanho da resposta das invocações do método direto  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.methods.success  |  Invocações de métodos diretos bem-sucedidas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.failure  |  Gémeo falhado lê do fundo  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.size  |  Tamanho de resposta de leituras gémeas da parte de trás  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.success  |  Gémea bem sucedida lê a partir da parte de trás  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.failure  |  Falhas em duas atualizações a partir do final  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.size  |  Tamanho das atualizações gémeas a partir da parte de trás  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.success  |  Atualizações gémeas bem sucedidas a partir do final  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  C2DMessagesExpired  |  Mensagens C2D Expiradas (pré-visualização)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  configurações  |  Métricas de configuração  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Devices/IotHubs  |  connectedDeviceCount  |  Dispositivos conectados (pré-visualização)  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.builtIn.events  |  Encaminhamento: mensagens entregues a mensagens/eventos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.eventHubs  |  Encaminhamento: mensagens entregues no Centro de Eventos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusQueues  |  Encaminhamento: mensagens entregues na Fila de Autocarros de Serviço  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusTopics  |  Encaminhamento: mensagens entregues no Service Bus Topic  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage  |  Encaminhamento: mensagens entregues no armazenamento  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.blobs  |  Encaminhamento: bolhas entregues ao armazenamento  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.bytes  |  Encaminhamento: dados entregues ao armazenamento  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latncy.builtIn.events  |  Encaminhamento: latência da mensagem para mensagens/eventos  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latncy.eventHubs  |  Encaminhamento: latência da mensagem para o Centro de Eventos  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latncy.serviceBusQueues  |  Encaminhamento: latência da mensagem para a fila do autocarro de serviço  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latncy.serviceBusTopics  |  Encaminhamento: latência de mensagem para o tópico do autocarro de serviço  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latncy.storage  |  Encaminhamento: latência da mensagem para armazenamento  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetria.egress.drop  |  Encaminhamento: mensagens de telemetria caídas   |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetria.egress.fallback  |  Encaminhamento: mensagens entregues para recuo  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetria.egress.inválido  |  Encaminhamento: mensagens de telemetria incompatíveis  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetria.egress.órfão  |  Encaminhamento: mensagens de telemetria órfãs   |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetria.egress.sucesso  |  Encaminhamento: mensagens de telemetria entregues  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetria.ingress.allProtocol  |  Mensagem de telemetria envia tentativas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetria.ingress.sendThrottle  |  Número de erros de estrangulamento  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetria.ingress.success  |  Mensagens de telemetria enviadas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.failure  |  Leituras gémeas falhadas dos dispositivos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.size  |  Tamanho de resposta de leituras gémeas de dispositivos  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.success  |  Leituras gémeas bem sucedidas de dispositivos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.failure  |  Falhas em duas atualizações a partir de dispositivos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.size  |  Tamanho das atualizações gémeas dos dispositivos  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.success  |  Atualizações gémeas bem sucedidas dos dispositivos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  dailyMessageQuotaUsed  |  Número total de mensagens utilizadas  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  dispositivoDataUsage  |  Total da utilização de dados do dispositivo  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  dispositivoDataUsageV2  |  Total da utilização dos dados do dispositivo (pré-visualização)  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  dispositivos.connectedDevices.allProtocol  |  Dispositivos ligados (precotados)   |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  dispositivos.totalDevices  |  Total de dispositivos (precotados)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  EventGridDeliveries  |  Entregas de Grelha de Eventos (pré-visualização)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  EventGridLatency  |  Latência da grelha de eventos (pré-visualização)  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.failure  |  Cancelamentos de empregos falhados  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.sucesso  |  Cancelamentos de emprego bem-sucedidos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  empregos.concluídos  |  Trabalhos concluídos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.fail  |  Criações falhadas de trabalhos de invocação de métodos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.success  |  Criações bem sucedidas de trabalhos de invocação de métodos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.failure  |  Criações falhadas de trabalhos de atualização dupla  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.success  |  Criações bem sucedidas de trabalhos de atualização dupla  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  empregos.falhou  |  Empregos falhados  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  empregos.listJobs.fracasso  |  Chamadas falhadas para listar empregos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.sucesso  |  Chamadas bem-sucedidas para listar empregos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.consultaJobs.falha  |  Consultas de trabalho falhadas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.sucesso  |  Consultas de trabalho bem sucedidas  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Devices/IotHubs  |  totalDeviceCount  |  Total de dispositivos (pré-visualização)  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  twinQueries.falha  |  Consultas gémeas falhadas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  twinQueries.resultSize  |  Tamanho do resultado de consultas gémeas  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  twinQueries.sucesso  |  Consultas gémeas bem sucedidas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/provisioningServices  |  AttestationAttempts  |  Tentativas de atestado  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/provisioningServices  |  Configurações de Dispositivos  |  Dispositivos atribuídos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/provisioningServices  |  InscriçõesAptas  |  Tentativas de registo  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/base de dadosAcontas  |  DisponíveisToragem  |  Armazenamento Disponível  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/base de dadosAcontas  |  CassandraConnectionClosures  |  Encerramentos de conexão cassandra  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/base de dadosAcontas  |  CassandraRequestCharges  |  Cassandra Request Charges  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/base de dadosAcontas  |  CassandraRequests  |  Pedidos de Cassandra  |  de palavras  |  de palavras | 
| Não  | Não |  Microsoft.DocumentDB/base de dadosAcontas  |  DataUsage  |  Utilização de Dados  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.DocumentDB/base de dadosAcontas  |  DeleteVirtualNetwork  |  DeleteVirtualNetwork  |  de palavras  |  de palavras | 
| Não  | Não |  Microsoft.DocumentDB/base de dadosAcontas  |  Contagem de documentos  |  Contagem de documentos  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/base de dadosAcontas  |  DocumentQuota  |  Quota documental  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/base de dadosAcontas  |  IndexUsage  |  Utilização do Índice  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/base de dadosAcontas  |  MetadataReques  |  Pedidos de Metadados  |  de palavras  |  de palavras | 
| **Sim**  | **Sim** |  Microsoft.DocumentDB/base de dadosAcontas  |  MongoRequestCharge  |  Taxa de pedido de Mongo  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DocumentDB/base de dadosAcontas  |  MongoRequests  |  Pedidos de Mongo  |  de palavras  |  de palavras | 
| Não  | Não |  Microsoft.DocumentDB/base de dadosAcontas  |  MongoRequestsCount  |  Taxa de pedido de Mongo  |  CondePerSecond  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/base de dadosAcontas  |  MongoRequestsDelete  |  Tarifa de pedido de eliminação de Mongo  |  CondePerSecond  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/base de dadosAcontas  |  MongoRequestsInsert  |  Taxa de pedido de inserção de Mongo  |  CondePerSecond  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/base de dadosAcontas  |  MongoRequestsQuery  |  Taxa de pedido de consulta de Mongo  |  CondePerSecond  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/base de dadosAcontas  |  MongoRequestsUpdate  |  Taxa de pedido de atualização de Mongo  |  CondePerSecond  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/base de dadosAcontas  |  ProvisionedThroughput  |  Débito Aprovisionado  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.DocumentDB/base de dadosAcontas  |  ReplicationLatency  |  Latência de replicação P99  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/base de dadosAcontas  |  ServiçoSAdisponibilidade  |  Disponibilidade de serviço  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DocumentDB/base de dadosAcontas  |  TotalRequests  |  Total de Pedidos  |  de palavras  |  de palavras | 
| **Sim**  | **Sim** |  Microsoft.DocumentDB/base de dadosAcontas  |  TotalRequestUnits  |  Total de Unidades de Pedido  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.EnterpriseKnowledgeGraph/services  |  Contagem de falhas  |  Contagem de Falhas  |  de palavras  |  de palavras | 
| Não  | Não |  Microsoft.EnterpriseKnowledgeGraph/services  |  SucessoCount  |  Contagem de Êxitos  |  de palavras  |  de palavras | 
| Não  | Não |  Microsoft.EnterpriseKnowledgeGraph/services  |  SucessoSA  |  Latência do sucesso  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.EnterpriseKnowledgeGraph/services  |  TransaçãoCount  |  Contagem de transações  |  de palavras  |  de palavras | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domínios  |  DeadLetteredCount  |  Eventos lettered mortos  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.EventGrid/domínios  |  EntregaAttemptFailCount  |  Eventos falhados de entrega  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domínios  |  EntregaSSuccessCount  |  Eventos Entregues  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.EventGrid/domínios  |  DestinationProcessingDurationInMs  |  Duração do processamento do destino  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domínios  |  DropEventCount  |  Eventos abandonados  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domínios  |  MatchedEventCount  |  Eventos Combinados  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domínios  |  PublicarFailCount  |  Publicar Eventos Falhados  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domínios  |  PublicarSuccessCount  |  Eventos Publicados  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domínios  |  Publicar Inimitrodências  |  Publicar Latência de Sucesso  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/eventSubscriptions  |  DeadLetteredCount  |  Eventos lettered mortos  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.EventGrid/eventSubscriptions  |  EntregaAttemptFailCount  |  Eventos falhados de entrega  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/eventSubscriptions  |  EntregaSSuccessCount  |  Eventos Entregues  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.EventGrid/eventSubscriptions  |  DestinationProcessingDurationInMs  |  Duração do processamento do destino  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/eventSubscriptions  |  DropEventCount  |  Eventos abandonados  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/eventSubscriptions  |  MatchedEventCount  |  Eventos Combinados  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/extensionTopics  |  PublicarFailCount  |  Publicar Eventos Falhados  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/extensionTopics  |  PublicarSuccessCount  |  Eventos Publicados  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/extensionTopics  |  Publicar Inimitrodências  |  Publicar Latência de Sucesso  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/extensionTopics  |  InigualávelEventCount  |  Eventos incomparáveis  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/tópicos  |  PublicarFailCount  |  Publicar Eventos Falhados  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/tópicos  |  PublicarSuccessCount  |  Eventos Publicados  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/tópicos  |  Publicar Inimitrodências  |  Publicar Latência de Sucesso  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/tópicos  |  InigualávelEventCount  |  Eventos incomparáveis  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  ActiveConnections  |  ActiveConnections  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.EventHub/clusters  |  DisponívelMemory  |  Memória Disponível  |  Percentagem  |  Máximo | 
| Não  | Não |  Microsoft.EventHub/clusters  |  CaptureBacklog  |  Capture Backlog.  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  CapturouBytes  |  Bytes capturados.  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  Caixas de Captura  |  Mensagens capturadas.  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  LigaçõesClosed  |  Ligações Fechadas.  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.EventHub/clusters  |  LigaçõesAsa  |  Ligações Abertas.  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.EventHub/clusters  |  CPU  |  CPU  |  Percentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.EventHub/clusters  |  IncomingBytes  |  Bytes Recebidos.  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/clusters  |  Entradas  |  Mensagens Recebidas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/clusters  |  IncomingRequests  |  Pedidos Recebidos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/clusters  |  OutgoingBytes  |  Bytes de saída.  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/clusters  |  Despesas de Saída  |  Mensagens Enviadas  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  QuotaExceededErrors  |  Erros de Quota Excedida.  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  ServerErrors  |  Erros do Servidor.  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  Requess de sucesso  |  Pedidos Com Êxito  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  ThrottledRequests  |  Pedidos Limitados.  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  UserErrors  |  Erros do Utilizador.  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  ActiveConnections  |  ActiveConnections  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  CaptureBacklog  |  Capture Backlog.  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  CapturouBytes  |  Bytes capturados.  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  Caixas de Captura  |  Mensagens capturadas.  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  LigaçõesClosed  |  Ligações Fechadas.  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  LigaçõesAsa  |  Ligações Abertas.  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  EHABL  |  Mensagens de atraso de arquivo (Deprecadas)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  EHAMBS  |  Produção de mensagem de arquivo (Precotada)  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  EHAMSGS  |  Mensagens de arquivo (Depreciadas)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  EHINBYTES  |  Bytes de entrada (Deprecados)  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  EHINMBS  |  Bytes de entrada (obsoletos) (Deprecados)  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  EFINMSGS  |  Mensagens recebidas (depreciadas)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  EHOUTBYTES  |  Bytes de saída (Deprecados)  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  EHOUTMBS  |  Bytes de saída (obsoletos) (Preprecados)  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  EHOUTMSGS  |  Mensagens de saída (depreciadas)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  FAILREQ  |  Pedidos Falhados (Deprecados)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  IncomingBytes  |  Bytes Recebidos.  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  Entradas  |  Mensagens Recebidas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  IncomingRequests  |  Pedidos Recebidos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  INMSGS  |  Mensagens recebidas (obsoletas) (Depreciadas)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  INREQS  |  Pedidos de Entrada (Deprecados)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  INTERR  |  Erros internos do servidor (preprecados)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  MISCERR  |  Outros Erros (Precotados)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  OutgoingBytes  |  Bytes de saída.  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  Despesas de Saída  |  Mensagens Enviadas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  OUTMSGS  |  Mensagens de saída (obsoletas) (Depreciadas)  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  QuotaExceededErrors  |  Erros de Quota Excedida.  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  ServerErrors  |  Erros do Servidor.  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  Tamanho  |  Tamanho  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  Requess de sucesso  |  Pedidos Com Êxito  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  SUCCREQ  |  Pedidos de sucesso (Deprecados)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/espaços de nome  |  SVRBSY  |  Erros ocupados do servidor (preprecados)  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  ThrottledRequests  |  Pedidos Limitados.  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.EventHub/espaços de nome  |  UserErrors  |  Erros do Utilizador.  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.HDInsight/clusters  |  CategorizadosGatewayRequests  |  Pedidos de Gateway categorizados  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.HDInsight/clusters  |  GatewayRequests  |  Pedidos de Gateway  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.HDInsight/clusters  |  NumActiveWorkers  |  Número de trabalhadores ativos  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.HDInsight/clusters  |  EscalandoReques  |  Pedidos de escala  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Insights/AutoscaleSettings  |  MétricaThreshold  |  Limiar métrico  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/AutoscaleSettings  |  Capacidade Observada  |  Capacidade observada  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/AutoscaleSettings  |  ValorMétrico Observado  |  Valor métrico observado  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Insights/AutoscaleSettings  |  ScaleActionsInitiado  |  Ações de escala iniciadas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  disponibilidadeResults/disponibilidadePercentage  |  Disponibilidade  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.Insights/Componentes  |  disponibilidadeResults/contagem  |  Testes de disponibilidade  |  de palavras  |  de palavras | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  disponibilidadeResults/duração  |  Duração do teste de disponibilidade  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  browserEs/networkDuration  |  Tempo de ligação da rede de carga de página  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  browserEs/processamentoDuração  |  Tempo de processamento do cliente  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  browserEs/receberDuration  |  Tempo de resposta de receção  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  browserEs/sendDuration  |  Enviar tempo de pedido  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  browserEs/totalDuration  |  Tempo de carregamento da página do navegador  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Insights/Componentes  |  dependências/contagem  |  Chamadas de dependência  |  de palavras  |  de palavras | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  dependências/duração  |  Duração da dependência  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Insights/Componentes  |  dependências/falhados  |  Falhas nas chamadas de dependência  |  de palavras  |  de palavras | 
| Não  | Não |  Microsoft.Insights/Componentes  |  exceções/navegador  |  Exceções de browser  |  de palavras  |  de palavras | 
| **Sim**  | **Sim** |  Microsoft.Insights/Componentes  |  exceções/contagem  |  Exceções  |  de palavras  |  de palavras | 
| Não  | Não |  Microsoft.Insights/Componentes  |  exceções/servidor  |  Exceções do servidor  |  de palavras  |  de palavras | 
| **Sim**  | **Sim** |  Microsoft.Insights/Componentes  |  pageVers/contagem  |  Vistas da página  |  de palavras  |  de palavras | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  páginaVerses/duração  |  Tempo de carga da visualização da página  |  MilliSeconds  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Insights/Componentes  |  performanceCounters/excepçõesPerSecond  |  Taxa de exceção  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  performanceCounters/memoryAvailableBytes  |  Memória disponível  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  performanceCounters/processCpuPercentage  |  Processo CPU  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  performanceCounters/processIOBytesPerSecond  |  Taxa IO do processo  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  performanceCounters/processadorEsPercentage  |  Tempo do processador  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  performanceCounters/processPrivateBytes  |  Processar bytes privados  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  performanceCounters/requestExecutionTime  |  HTTP solicitar tempo de execução  |  MilliSeconds  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Insights/Componentes  |  performanceCounters/requestsInQueue  |  Pedidos HTTP na fila de candidaturas  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Insights/Componentes  |  performanceCounters/requestsPerSecond  |  Taxa de pedido HTTP  |  CondePerSecond  |  Média | 
| Não  | Não |  Microsoft.Insights/Componentes  |  pedidos/contagem  |  Pedidos de servidor  |  de palavras  |  de palavras | 
| **Sim**  | Não |  Microsoft.Insights/Componentes  |  pedidos/duração  |  Tempo de resposta do servidor  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Insights/Componentes  |  pedidos/falhados  |  Pedidos com falhas  |  de palavras  |  de palavras | 
| Não  | Não |  Microsoft.Insights/Componentes  |  pedidos/tarifa  |  Taxa de pedido do servidor  |  CondePerSecond  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Insights/Componentes  |  vestígios/contagem  |  Rastreios  |  de palavras  |  de palavras | 
| **Sim**  | Não |  Microsoft.KeyVault/cofres  |  ServiceApiHit  |  Acessos Api de serviço total  |  de palavras  |  de palavras | 
| **Sim**  | Não |  Microsoft.KeyVault/cofres  |  ServiçoApilatency  |  Latência geral do serviço Api  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.KeyVault/cofres  |  ServiceApiResult  |  Resultados totais do serviço Api  |  de palavras  |  de palavras | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  CacheUtilização  |  Utilização de cache  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  ContinuaExportMaxLatenessMinutes  |  Minutos de atraso de exportação contínua  |  de palavras  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  ContinuousExportNumOfRecordsExported  |  Exportação contínua - num de registos exportados  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  ContínuaExportPendingCount  |  Contagem pendente de exportação contínua  |  de palavras  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  ContínuaExportResult  |  Resultado contínuo da exportação  |  de palavras  |  de palavras | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  CPU  |  CPU  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  EventosProcessadoForEventHubs  |  Eventos processados (para Centros de Eventos/IoT)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  ExportaçãoUtilização  |  Utilização da Exportação  |  Percentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  IngestionLatencyInSeconds  |  Latência da ingestão (em segundos)  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  IngestionResult  |  Resultado da ingestão  |  de palavras  |  de palavras | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  Ingestionutilização  |  Utilização da ingestão  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  IngestionVolumeInMB  |  Volume de ingestão (em MB)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  KeepAlive  |  Mantenha-se vivo  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  Queriaduração  |  Duração da consulta  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  SteamingIngestRequestRate  |  Taxa de pedido de ingestão de streaming  |  de palavras  |  RateRequestsPerSecond | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  StreamingIngestDataRate  |  Taxa de dados de ingestão de streaming  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  StreamingIngestDuration  |  Duração do Streaming Ingest  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  StreamingIngestResults  |  Resultado do streaming inger  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  Ação  |  Latência de Ação   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  Ações Computação  |  Ações Concluídas   |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  Ações Destruídas  |  Ações falhadas   |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  AçõesSkipped  |  Ações ignoradas   |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  AçõesStarted  |  Ações Iniciadas   |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  AçõesDuzidas  |  Ações Bem Sucedidas   |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  ActionSuccessLatency  |  Latência do sucesso da ação   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionThrottledEvents  |  Eventos throttled de ação  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  Integração ServiçoEnvironmentConnectorMemoryUsage  |  Utilização da memória do conector para ambiente de serviço de integração  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  Integração ServiçoEnvironmentConnectorProcessorUsage  |  Utilização do processador do conector para ambiente de serviço de integração  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  Integração ServiçoEnvironmentOdestrabalhomor  |  Utilização da memória do fluxo de trabalho para ambiente de serviço de integração  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  Integração ServiçoEnvironmentWorkflowProcessorUsage  |  Utilização do processador de fluxo de trabalho para ambiente de serviço de integração  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  RunFailurePercentage  |  Percentagem de falha de execução  |  Percentagem  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  Estação Runlatency  |  Executar Latência  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsCancelled  |  Execuções canceladas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsCompleted  |  Execuções Concluídas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsFailed  |  Runs Falhado  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsStarted  |  Execuções iniciadas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsSucceed  |  Runs Succeeded  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  RunStartThrottledEvents  |  Executar eventos throttled do início  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  RunSuccessLatency  |  Executar Latência de sucesso  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  RunThrottledEvents  |  Executar Eventos Throttled  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerFireLatency  |  Desencadear a latência do fogo   |  Segundos  |  Média | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerLatency  |  Desencadear a latência   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  GatilhosCompleted  |  Gatilhos Concluídos   |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  GatilhosFailed  |  Gatilhos falhados   |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  Gatilhos Disparados  |  Gatilhos disparados   |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  GatilhosSkipped  |  Gatilhos ignorados  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  GatilhosStarted  |  Gatilhos Iniciados   |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  GatilhosSsueded  |  Gatilhos Bem Sucedidos   |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerSuccessLatency  |  Desencadear a latência do sucesso   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerThrottledEvents  |  Eventos aceleradores do gatilho  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/workflows  |  Ação  |  Latência de Ação   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  Ações Computação  |  Ações Concluídas   |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  Ações Destruídas  |  Ações falhadas   |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  AçõesSkipped  |  Ações ignoradas   |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  AçõesStarted  |  Ações Iniciadas   |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  AçõesDuzidas  |  Ações Bem Sucedidas   |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/workflows  |  ActionSuccessLatency  |  Latência do sucesso da ação   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  ActionThrottledEvents  |  Eventos throttled de ação  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  BillableActionExecutions  |  Execuções de ação executáveis  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  BillableTriggerExecutions  |  Execuções de gatilhos faturantes  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  Faturação UsageNativeOperação  |  Utilização de faturação para execuções de operações nativas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  Faturação UsageNativeOperação  |  Utilização de faturação para execuções de operações nativas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  Utilização de faturação para execuções padrão do conector  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  Utilização de faturação para execuções padrão do conector  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  Faturação de utilização para execuções de consumo de armazenamento  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  Faturação de utilização para execuções de consumo de armazenamento  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunFailurePercentage  |  Percentagem de falha de execução  |  Percentagem  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/workflows  |  Estação Runlatency  |  Executar Latência  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunsCancelled  |  Execuções canceladas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunsCompleted  |  Execuções Concluídas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunsFailed  |  Runs Falhado  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunsStarted  |  Execuções iniciadas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunsSucceed  |  Runs Succeeded  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunStartThrottledEvents  |  Executar eventos throttled do início  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/workflows  |  RunSuccessLatency  |  Executar Latência de sucesso  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunThrottledEvents  |  Executar Eventos Throttled  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  TotalBillableExecutions  |  Execuções totais faturantes  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/workflows  |  TriggerFireLatency  |  Desencadear a latência do fogo   |  Segundos  |  Média | 
| **Sim**  | Não |  Microsoft.Logic/workflows  |  TriggerLatency  |  Desencadear a latência   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  GatilhosCompleted  |  Gatilhos Concluídos   |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  GatilhosFailed  |  Gatilhos falhados   |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  Gatilhos Disparados  |  Gatilhos disparados   |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  GatilhosSkipped  |  Gatilhos ignorados  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  GatilhosStarted  |  Gatilhos Iniciados   |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  GatilhosSsueded  |  Gatilhos Bem Sucedidos   |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/workflows  |  TriggerSuccessLatency  |  Desencadear a latência do sucesso   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  TriggerThrottledEvents  |  Eventos aceleradores do gatilho  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Núcleos Ativos  |  Núcleos Ativos  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Nóns ativos  |  Nóns ativos  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Execuções Concluídas  |  Execuções Concluídas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Corridas falhadas  |  Corridas falhadas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Núcleos Ociosos  |  Núcleos Ociosos  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Nódoas Ociosas  |  Nódoas Ociosas  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Deixando os Núcleos  |  Deixando os Núcleos  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Deixando os nóns  |  Deixando os nóns  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Implementação de modelo falhou  |  Implementação de modelo falhou  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Implementação de modelos iniciado  |  Implementação de modelos iniciado  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Implementação de modelos conseguiu  |  Implementação de modelos conseguiu  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Registo modelo falhado  |  Registo modelo falhado  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Registo modelo Bem sucedido  |  Registo modelo Bem sucedido  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Núcleos Preempted  |  Núcleos Preempted  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Nómadas Preempted  |  Nómadas Preempted  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Percentagem de Utilização de Cots  |  Percentagem de Utilização de Cots  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Corridas iniciadas  |  Corridas iniciadas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Núcleos Totais  |  Núcleos Totais  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Nómada Total  |  Nómada Total  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Núcleos inutilizáveis  |  Núcleos inutilizáveis  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Nódes Inutilizáveis  |  Nódes Inutilizáveis  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Maps/contas  |  Disponibilidade  |  Disponibilidade  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.Maps/contas  |  Utilização  |  Utilização  |  de palavras  |  de palavras | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  Conta de Ativos  |  Contagem de ativos  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  AssetQuota  |  Quota de ativos  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  AssetQuotaUsedPercentage  |  Percentagem de quota de ativos utilizada  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  ContentKeyPolicyCount  |  Contagem de políticas de chave de conteúdo  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  ContentKeyPolicyQuota  |  Quota de política chave de conteúdo  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  ContentKeyPolicyQuotaUsedPercentage  |  Quota de política de conteúdo utilizada  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  StreamingPolicyCount  |  Contagem de políticas de streaming  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  StreamingPolicyQuota  |  Quota de política de streaming  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  StreamingPolicyQuotaUsedPercentage  |  Percentagem utilizada pela quota de política de streaming  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices/streamingEndpoints  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Media/mediaservices/streamingEndpoints  |  Pedidos  |  Pedidos  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Media/mediaservices/streamingEndpoints  |  SucessoE2ELatency  |  Fim do sucesso para acabar com a latência  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  GCPauseTotalCount  |  Contagem de pausas GC  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  GCPauseTotalTime  |  GC Pausa Tempo Total  |  Milissegundos  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  MaxOldGenMemoryPoolBytes  |  Tamanho máximo de dados de geração antiga disponível  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  OldGenMemoryPoolBytes  |  Tamanho dos dados da geração velha  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  OldGenPromotedBytes  |  Promover para o tamanho dos dados da geração velha  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  ServiceCpuUsagePercentage  |  Percentagem de Utilização cpu de serviço  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  ServiçoMemoryCommittedido  |  Memória de serviço atribuída  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryMax  |  Memória de Serviço Max  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  ServiçoSororizado  |  Memória de serviço usada  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  SystemCpuUsagePercentage  |  Percentagem de Utilização cpu do sistema  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatErrorCount  |  Erro Global de Tomcat  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatReceivedBytes  |  Tomcat Total Recebido Bytes  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestMaxTime  |  Tomcat Request Max Time  |  Milissegundos  |  Máximo | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestTotalCount  |  Tomcat Request Contagem Total  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestTotalTime  |  Pedido tomcat tempos totais  |  Milissegundos  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatResponseAvgTime  |  Tomcat Pede tempo médio  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSentBytes  |  Tomcat Total Enviado Bytes  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionActiveCurrentCount  |  Tomcat Session Alive Count  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionActiveMaxCount  |  Tomcat Session Max Ative Count  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionAliveMaxTime  |  Tomcat Session Max Alive Tempo  |  Milissegundos  |  Máximo | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionCreatedCount  |  Tomcat Session Criou Contagem  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionExpiredCount  |  Contagem expirada da sessão de Tomcat  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionRejectedCount  |  Contagem rejeitada da sessão de Tomcat  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  YoungGenPromotedBytes  |  Promover para o tamanho de dados de geração jovem  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolAllocatedUsed  |  Volume de piscina atribuído utilizado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolTotalLogicalsize  |  Tamanho lógico total da piscina de volume  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  Média DeOncência  |  Latência média de leitura  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  Média Desaquimia  |  Latência média de escrita  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  ReadIops  |  Ler iops  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  VolumeLogicalsize  |  Tamanho lógico do volume  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  VolumeSnapshotSize  |  Tamanho do instantâneo de volume  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  WriteIops  |  Escrever iops  |  CondePerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  ApplicationGatewayTotalTime  |  Tempo total do Gateway de Aplicação  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  AvgRequestCountPerHealthyHost  |  Pedidos por minuto por Anfitrião Saudável  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  BackendConnectTime  |  Tempo de ligação de backend  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  BackendFirstByteResponseTime  |  Tempo de resposta de backend First Byte  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  BackendLastByteResponseTime  |  Tempo de resposta de Backend Last Byte  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  BackendResponseStatus  |  Estado de resposta de backend  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  Contagem bloqueada  |  Firewall de aplicação web bloqueou distribuição de regras de pedidos  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  BlockedReqCount  |  Contagem de pedidos bloqueados de firewall de aplicação web  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  BytesReceived  |  Bytes Recebidos  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  BytesSent  |  Bytes Enviados  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  Unidades de capacidade  |  Unidades de Capacidade Corrente  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  ClientRtt  |  RTT cliente  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  Unidades computeuções  |  Unidades de Computação Atuais  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  Correntes Deconhecões  |  Conexões atuais  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  Requessos Falhados  |  Pedidos com Falhas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  HealthyHostCount  |  Contagem saudável do anfitrião  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  MatchedCount  |  Distribuição total de regras de firewall de aplicação web  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  Estatísticas de Resposta  |  Estado de resposta  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  Débito  |  Débito  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  TlsProtocol  |  Protocolo TLS do Cliente  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  TotalRequests  |  Total de Pedidos  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  Não-saudávelHostCount  |  Contagem de anfitriões pouco saudável  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Network/azurefirewalls  |  AplicaçãoRuleHit  |  As regras de aplicação atingem a contagem  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/azurefirewalls  |  Dados Processados  |  Dados processados  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Network/azurefirewalls  |  FirewallHealth  |  Estado de saúde de firewall  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/azurefirewalls  |  NetworkRuleHit  |  Regras de rede atingem a contagem  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/azurefirewalls  |  SNATPortutilization  |  Utilização do porto de SNAT  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/conexões  |  BitsInPerSecond  |  BitsInPerSecond  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/conexões  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/dnszones  |  QueryVolume  |  Volume de consulta  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Network/dnszones  |  RecordSetCapacityUtilização  |  Record Fixo Utilização da Capacidade  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/dnszones  |  RecordSetCount  |  Contagem de recordes  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/expressRouteCircuits  |  ArpAilability  |  Disponibilidade ARP  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRouteCircuits  |  BgpAilability  |  Disponibilidade BGP  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  BitsInPerSecond  |  BitsInPerSecond  |  CondePerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CondePerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsInPerSecond  |  GlobalReachBitsInPerSecond  |  CondePerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsOutPerSecond  |  GlobalReachBitsOutPerSecond  |  CondePerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsInPerSecond  |  DropInBitsPerSecond  |  CondePerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsOutPerSecond  |  DropOutBitsPerSecond  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsInPerSecond  |  BitsInPerSecond  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CondePerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteGateways  |  ErGatewayConnectionBitsInPerSecond  |  BitsInPerSecond  |  CondePerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteGateways  |  ErGatewayConnectionBitsOutPerSecond  |  BitsOutPerSecond  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  Estado Administrativo  |  Estado Administrativo  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  LineProtocol  |  LineProtocol  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  PortBitsInPerSecond  |  BitsInPerSecond  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  PortBitsOutPerSecond  |  BitsOutPerSecond  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  RxLightLevel  |  RxLightLevel  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  TxLightLevel  |  TxLightLevel  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  BackendHealthPercentage  |  Percentagem de Saúde backend  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  BackendRequestCount  |  Contagem de pedidos de backend  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  BackendRequestatency  |  Pedido de backend Latência  |  MilliSeconds  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  BillableResponseSize  |  Tamanho da resposta faturada  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  PedidoCount  |  Número de Pedidos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  Solicitação  |  Tamanho do pedido  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  Tamanho das respostas  |  Tamanho da resposta  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  TotalLatency  |  Latência total  |  MilliSeconds  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  WebApplicationFirewallRequestCount  |  Contagem de pedidos de firewall de aplicação web  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Network/loadBalancers  |  AlocadosSnatPorts  |  Portas SNAT atribuídas (Pré-visualização)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/loadBalancers  |  ByteCount  |  Byte Count  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/loadBalancers  |  DipDUilability  |  Estado da Sonda de Estado de Funcionamento  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Network/loadBalancers  |  PacoteCount  |  Contagem de Pacotes  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/loadBalancers  |  SnatConnectionCount  |  Contagem de ligação SNAT  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/loadBalancers  |  SYNCount  |  Contagem de SINA  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Network/loadBalancers  |  UssnatPorts  |  Portas SNAT usadas (pré-visualização)  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/loadBalancers  |  VipSAdability  |  Disponibilidade do Caminho de Dados  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Network/networkInterfaces  |  BytesReceivedRate  |  Bytes Recebidos  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Network/networkInterfaces  |  BytesSentRate  |  Bytes Enviados  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Network/networkInterfaces  |  PacketsReceivedRate  |  Pacotes Recebidos  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/networkInterfaces  |  PacotesSEntRate  |  Pacotes Enviados  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/networkWatchers/connectionMonitors  |  Médias Desvelundos  |  Avg. Tempo de ida e volta (ms)  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Network/networkWatchers/connectionMonitors  |  ChequesFailedPercent  |  Cheques Falhados Por cento (Pré-visualização)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/networkWatchers/connectionMonitors  |  ProbesFailedPercent  |  % Sondas Falhadas  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/networkWatchers/connectionMonitors  |  RoundTripTimeMs  |  Tempo de viagem redonda (ms) (pré-visualização)  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  ByteCount  |  Byte Count  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  BytesDroppedDDoS  |  Bytes de entrada deixaram cair DDoS  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  BytesForwardedDDoS  |  Bytes de entrada reencaminhado DDoS  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  BytesInDDoS  |  Bytes de entrada DDoS  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerSYNPackets  |  Pacotes SYN de entrada para desencadear a mitigação do DDoS  |  CondePerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  Pacotes DDoSTriggerTCP  |  Pacotes TCP de entrada para desencadear mitigação do DDoS  |  CondePerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerUDPPackets  |  Pacotes UDP de entrada para desencadear mitigação do DDoS  |  CondePerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  IfUnderDDosAttack  |  Sob o ataque do DDoS ou não  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  PacoteCount  |  Contagem de Pacotes  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  PacotesDroppedDDoS  |  Pacotes de entrada deixaram cair DDoS  |  CondePerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  PacotesForwardedDDoS  |  Pacotes de entrada reencaminhados DDoS  |  CondePerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  PacotesInDDoS  |  Pacotes de entrada DDoS  |  CondePerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  SynCount  |  Contagem de SINA  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  TCPBytesDroppedDDoS  |  Bytes TCP de entrada deixaram cair DDoS  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  TCPBytesForwardedDDoS  |  Inbound TCP bytes reencaminhado DDoS  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  TCPBytesInDDoS  |  Entrada TCP bytes DDoS  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  TCPPacketsDroppedDDoS  |  Pacotes TCP de entrada deixaram cair DDoS  |  CondePerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  TCPPacketsForwardedDDoS  |  Pacotes TCP de entrada reencaminhados DDoS  |  CondePerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  TCPPacketsInDDoS  |  Pacotes TCP de entrada DDoS  |  CondePerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  UDPBytesDroppedDDoS  |  Bytes udp de entrada deixaram cair DDoS  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  UDPBytesForwardedDDoS  |  Bytes de UDP de entrada reencaminhados DDoS  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  UDPBytesInDDoS  |  UDP bytes DDoS de entrada  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  UDPPacketsDroppedDDoS  |  Pacotes UDP de entrada deixaram cair DDoS  |  CondePerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  UDPPacketsForwardedDDoS  |  Pacotes UDP de entrada reencaminhados DDoS  |  CondePerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  UDPPacketsInDDoS  |  Pacotes de UDP de entrada DDoS  |  CondePerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  VipSAdability  |  Disponibilidade do Caminho de Dados  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Network/trafficManagerProfiles  |  ProbeAgentCurrentEndpointStateByProfileResourceId  |  Estado do ponto final por Endpoint  |  de palavras  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Network/trafficManagerProfiles  |  QpsByEndpoint  |  Consultas por Endpoint Devolvido  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  Largura média da largura  |  Largura de banda Gateway S2S  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  P2SBandwidth  |  Largura de banda Gateway P2S  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  P2SConnectionCount  |  Contagem de Ligações P2S  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  TunnelAverageBandwidth  |  Largura de Banda do Túnel  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressBytes  |  Bytes de Saída do Túnel  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPacketDropTSMismatch  |  Remoção do Pacote de Erro de Correspondência de TS de Saída do Túnel  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPackets  |  Pacotes de Saída do Túnel  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  TúnelIngsBytes  |  Bytes de Entrada do Túnel  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPacketDropTSMismatch  |  Remoção do Pacote de Erro de Correspondência de TS de Entrada do Túnel  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  Pacotes de escavação  |  Pacotes de Entrada de Túnel  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworks  |  PingMeshAverageRoundtripMs  |  Hora de ida e volta para Pings a um VM  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworks  |  PingMeshProbesFailedPercent  |  Pings falhados para um VM  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  entrada  |  Mensagens Recebidas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.all.failedrequests  |  Todos os pedidos falhados de entrada  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.all.requests  |  Todos os Pedidos de Entrada  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  entrada.programado  |  Notificações de push agendadas enviadas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.scheduled.cancel  |  Notificações de push programadas canceladas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  instalação.todos  |  Operações de Gestão de Instalações  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  instalação.excluir  |  Excluir operações de instalação  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  instalação.get  |  Obter Operações de Instalação  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  instalação.patch  |  Operações de instalação de remendos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  instalação.upsert  |  Criar ou atualizar operações de instalação  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  notificationhub.pushes  |  Todas as notificações de saída  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.badorexpiredchannel  |  Erros do Canal Mau ou Expirados  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.channelerror  |  Erros do Canal  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.invalidpayload  |  Erros de carga útil  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.pnserror  |  Erros do sistema de notificação externa  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.sucesso  |  Notificações bem sucedidas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.badchannel  |  Erro do Canal Mau da APNS  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.expiredchannel  |  Erro do Canal caducado da APNS  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidcredentis  |  Erros de Autorização da APNS  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidnotificationsize  |  Erro de tamanho de notificação inválido da APNS  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.pnserror  |  Erros da APNS  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.sucesso  |  Notificações bem sucedidas da APNS  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.authenticationeror  |  Erros de autenticação GCM  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.badchannel  |  Erro do Canal Mau gCM  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.expiredchannel  |  Erro do canal expirado GCM  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidcredentis  |  Erros de autorização GCM (Credenciais Inválidas)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationformat  |  Formato de notificação inválida GCM  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationsize  |  Erro de tamanho de notificação inválido GCM  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.pnserror  |  Erros do GCM  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.sucesso  |  Notificações bem sucedidas da GCM  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.throttled  |  Notificações throttled GCM  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.canalil errado  |  Erro do canal errado gCM  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.authenticationerror  |  Erros de Autenticação MPNS  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.badchannel  |  Erro do Canal Mau da MPNS  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.channeldisconnected  |  Canal MPNS desligado  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.drop  |  Notificações retiradas do MPNS  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidcredentis  |  Credenciais inválidas da MPNS  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidnotificationformat  |  Formato de Notificação Inválida mpns  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.pnserror  |  Erros do MPNS  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.sucesso  |  Notificações bem sucedidas da MPNS  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.throttled  |  Notificações throttled MPNS  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.authenticationerror  |  Erros de autenticação WNS  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.badchannel  |  Erro do canal WNS  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channeldisconnected  |  Canal WNS desligado  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channelthrottled  |  Canal WNS Acelerado  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.drop  |  Notificações retiradas da WNS  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.expiredchannel  |  Erro do canal expirado da WNS  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidcredentis  |  Erros de autorização WNS (Credenciais Inválidas)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationformat  |  Formato de notificação inválida da WNS  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationsize  |  Erro de tamanho de notificação inválido da WNS  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidtoken  |  Erros de autorização da WNS (token inválido)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.pnserror  |  Erros do WNS  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.sucesso  |  Notificações bem sucedidas da WNS  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.throttled  |  Notificações throttled WNS  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.tokenproviderunreachable  |  Erros de autorização WNS (inacessíveis)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.wrongtoken  |  Erros de autorização da WNS (Ficha Errada)  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registro.todos  |  Operações de Registo  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.create  |  Criar Operações de Criação de Registos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.delete  |  Operações de Eliminação de Registos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.get  |  Operações de Leitura de Registo  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.update  |  Operações de Atualização de Registo  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  agendado.pendente  |  Notificações Agendadas Pendentes  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  memória Average_% disponível  |  % Memória Disponível  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  espaço de troca disponível Average_%  |  % Espaço de troca disponível  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% bytes comprometidos em uso  |  % Bytes comprometidos em uso  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% tempo do DPC  |  % tempo DPC  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Inodes Average_% Grátis  |  % Inodes grátis  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% Espaço Livre  |  % Espaço Livre  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% Espaço Livre  |  % Espaço Livre  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% tempo inativo  |  % de Tempo Inativo  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% Tempo de interrupção  |  % Tempo de interrupção  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% IO Tempo de espera  |  % IO Tempo de Espera  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% tempo agradável  |  % Tempo agradável  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% Tempo Privilegiado  |  % Tempo Privilegiado  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  tempo de processador Average_%  |  % de Tempo do Processador  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  tempo de processador Average_%  |  % de Tempo do Processador  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  inodes Average_% usados  |  % Inodes usados  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% Memória Usada  |  % Memória Usada  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% espaço usado  |  % Espaço Usado  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% espaço de troca usado  |  % Espaço de troca usado  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% tempo de utilização  |  % Tempo de Utilização  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes  |  MBytes disponíveis  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  memória Average_Available MBytes  |  Memória MBytes disponível  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  troca de MBytes Average_Available  |  Troca de MBytes disponível  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. de Disco seg/Leitura  |  Avg. Disco seg/Ler  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. de Disco seg/Leitura  |  Avg. Disco seg/Ler  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. Disco seg/Transferência  |  Avg. Disco seg/Transferência  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. de Disco seg/Escrita  |  Avg. Disco seg/Write  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. de Disco seg/Escrita  |  Avg. Disco seg/Write  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Recebido/seg  |  Bytes Recebidos/seg  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Enviado/seg  |  Bytes Enviados/seg  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Total/seg  |  Bytes Total/seg  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  comprimento da fila do disco Average_Current  |  Comprimento atual da fila do disco  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Ler Bytes/seg  |  Discos ler Bytes/seg  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Leituras/seg  |  Leituras/seg de disco  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Leituras/seg  |  Leituras/seg de disco  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Transferências Average_Disk/seg  |  Transferências de discos/seg  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Transferências Average_Disk/seg  |  Transferências de discos/seg  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Escrever Bytes/seg  |  Discos De Escrita Bytes/seg  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Escreve/Seg  |  Escritas/seg de disco  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Escreve/Seg  |  Escritas/seg de disco  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Free Megabytes  |  Megabytes grátis  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Free Megabytes  |  Megabytes grátis  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  memória física Average_Free  |  Memória Física Gratuita  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  espaço Average_Free em arquivos de paging  |  Espaço gratuito em ficheiros de paging  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  memória virtual Average_Free  |  Memória Virtual Gratuita  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Logical Discos Bytes/seg  |  Bytes/seg de disco lógico  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Page Leituras/seg  |  Leituras de página/seg  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Page Escreve/Seg  |  Escritas/seg de página  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Pages/seg  |  Páginas/seg  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Pct Tempo Privilegiado  |  Pct Tempo Privilegiado  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Pct tempo de utilizador  |  Tempo de utilizador do Pct  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Physical Discos Bytes/seg  |  Bytes/seg de disco físico  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Processes  |  Processos  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  comprimento da fila Average_Processor  |  Comprimento da fila do processador  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Size armazenado em ficheiros de paging  |  Tamanho armazenado em ficheiros de paging  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes  |  Total Bytes  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes Recebidos  |  Total de bytes recebidos  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  bytes de Average_Total transmitidos  |  Total de bytes transmitidos  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Colisões Average_Total  |  Colisões totais  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  pacotes de Average_Total recebidos  |  Total de pacotes recebidos  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  pacotes de Average_Total transmitidos  |  Total de pacotes transmitidos  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Total Erros Rx  |  Erros Rx totais  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Total Tx Erros  |  Erros Tx totais  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Uptime  |  Uptime  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Used MBytes Swap Space  |  Espaço de troca de MBytes usado  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  KBytes de Memória Average_Used  |  KBytes de Memória Usados  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  MBytes de Memória Average_Used  |  MBytes de memória usados  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Users  |  Utilizadores  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Virtual Memória Partilhada  |  Memória partilhada virtual  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Evento  |  Evento  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.OperationalInsights/workspaces  |  Heartbeat  |  Heartbeat  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Atualizar  |  Atualizar  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.PowerBIDedicated/capacities  |  memory_metric  |  Memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.PowerBIDedicated/capacities  |  memory_thrashing_metric  |  Escoamento de memória (conjuntos de dados)  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.PowerBIDedicated/capacities  |  qpu_high_utilization_metric  |  Maior Utilização de QPU  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.PowerBIDedicated/capacities  |  Queriaduração  |  Duração da consulta (conjuntos de dados)  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.PowerBIDedicated/capacities  |  QueryPoolJobQueueLength  |  Comprimento da fila de trabalho da piscina de consulta (conjuntos de dados)  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.Relay/namespaces  |  ActiveConnections  |  ActiveConnections  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  ActiveListeners  |  ActiveListeners  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Relay/namespaces  |  BytesTransferido  |  BytesTransferido  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  OuvinteConnections-ClientError  |  OuvinteConnections-ClientError  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  OuvinteConnections-ServerError  |  OuvinteConnections-ServerError  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  OuvinteConnections-Sucesso  |  OuvinteConnections-Sucesso  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  OuvinteConnections-TotalRequests  |  OuvinteConnections-TotalRequests  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  OuvinteSEsa ligações  |  OuvinteSEsa ligações  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  SenderConnections-ClientError  |  SenderConnections-ClientError  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  SenderConnections-ServerError  |  SenderConnections-ServerError  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  SenderConnections-Sucesso  |  SenderConnections-Sucesso  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  SenderConnections-TotalRequests  |  SenderConnections-TotalRequests  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  SenderDis ligações  |  SenderDis ligações  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Search/searchServices  |  PesquisaLatency  |  Procurar Latência  |  Segundos  |  Média | 
| **Sim**  | Não |  Microsoft.Search/searchServices  |  SearchQueriesPerSecond  |  Consultas de pesquisa por segundo  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Search/searchServices  |  ThrottledSearchQueriesPercentage  |  Percentagem de consultas de pesquisa aceleradas  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  ActiveConnections  |  ActiveConnections  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  ActiveMessages  |  Contagem de mensagens ativas numa fila/tópico.  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  LigaçõesClosed  |  Ligações Fechadas.  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  LigaçõesAsa  |  Ligações Abertas.  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  CPUXNS  |  CPU (precedido)  |  Percentagem  |  Máximo | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  DeadletteredMesages  |  Contagem de mensagens com letras mortas numa fila/tópico.  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.ServiceBus/namespaces  |  Entradas  |  Mensagens Recebidas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ServiceBus/namespaces  |  IncomingRequests  |  Pedidos Recebidos  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  Mensagens  |  Contagem de mensagens numa Fila/Tópico.  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  NamespaceCpuUsage  |  CPU  |  Percentagem  |  Máximo | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  NomespaceMemoryUsage  |  Utilização de Memória  |  Percentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.ServiceBus/namespaces  |  Despesas de Saída  |  Mensagens Enviadas  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  Horários  |  Contagem de mensagens programadas numa fila/tópico.  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  ServerErrors  |  Erros do Servidor.  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  Tamanho  |  Tamanho  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  Requess de sucesso  |  Pedidos Com Êxito  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  ThrottledRequests  |  Pedidos Limitados.  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  UserErrors  |  Erros do Utilizador.  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  WSXNS  |  Utilização da memória (Preprecada)  |  Percentagem  |  Máximo | 
| Não  | Não |  Microsoft.ServiceFabricMesh/aplicações  |  ActualCpu  |  ActualCpu  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/aplicações  |  ActualMemory  |  ActualMemory  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/aplicações  |  Cpu atribuído  |  Cpu atribuído  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/aplicações  |  Roteiro Atribuído  |  Roteiro Atribuído  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/aplicações  |  AplicaçãoStatus  |  AplicaçãoStatus  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/aplicações  |  Estatística do Contentor  |  Estatística do Contentor  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/aplicações  |  CpuUtilização  |  CpuUtilização  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/aplicações  |  Utilização da memória  |  Utilização da memória  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/aplicações  |  RestartCount  |  RestartCount  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/aplicações  |  ServiceReplicaStatus  |  ServiceReplicaStatus  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/aplicações  |  ServiceStatus  |  ServiceStatus  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.SignalrService/Signalr  |  Contagem de Ligações  |  Contagem de Ligação  |  de palavras  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.SignalrService/Signalr  |  InboundTraffic  |  Tráfego de Entrada  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.SignalrService/Signalr  |  Contagem de mensagens  |  Contagem de mensagens  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.SignalrService/Signalr  |  OutboundTraffic  |  Tráfego de Saída  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.SignalrService/Signalr  |  SystemErrors  |  Erros do Sistema  |  Percentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.SignalrService/Signalr  |  UserErrors  |  Erros do Utilizador  |  Percentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Sql/managedInstances  |  avg_cpu_percent  |  Percentagem média de CPU  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/managedInstances  |  io_bytes_read  |  IO bytes ler  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/managedInstances  |  io_bytes_written  |  IO bytes escritos  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/managedInstances  |  io_requests  |  Contagem de pedidos de IO  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/managedInstances  |  reserved_storage_mb  |  Espaço de armazenamento reservado  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/managedInstances  |  storage_space_used_mb  |  Espaço de armazenamento utilizado  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/managedInstances  |  virtual_core_count  |  Contagem de núcleos virtuais  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores  |  database_dtu_consumption_percent  |  Percentagem de DTU  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores  |  database_storage_used  |  Espaço de dados utilizado  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores  |  dtu_consumption_percent  |  Percentagem de DTU  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores  |  dtu_used  |  DTU usado  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores  |  storage_used  |  Espaço de dados utilizado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  allocated_data_storage  |  Espaço de dados atribuído  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  app_cpu_billed  |  App CPU faturada  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  app_cpu_percent  |  Percentagem de CPU de aplicações  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  app_memory_percent  |  Percentagem de memória de aplicativo  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  blocked_by_firewall  |  Bloqueado por Firewall  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  cache_hit_percent  |  Percentagem de impacto de cache  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  cache_used_percent  |  Percentagem utilizada em cache  |  Percentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  connection_failed  |  Ligações com Falhas  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  connection_successful  |  Conexões bem sucedidas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  cpu_limit  |  Limite do CPU  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  cpu_percent  |  Percentagem de CPU  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  cpu_used  |  CPU usado  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  impasse  |  Impasses  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  dtu_consumption_percent  |  Percentagem de DTU  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  dtu_limit  |  Limite DTU  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  dtu_used  |  DTU usado  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  dwu_consumption_percent  |  Percentagem de DWU  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  dwu_limit  |  Limite dwu  |  de palavras  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  dwu_used  |  DWU usado  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  local_tempdb_usage_percent  |  Percentagem de temporários locais  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  log_write_percent  |  Percentagem de IO de registo  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  memory_usage_percent  |  Percentagem de memória  |  Percentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  physical_data_read_percent  |  Percentagem de ES de Dados  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  sessions_percent  |  Percentagem de sessões  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  storage  |  Espaço de dados utilizado  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  storage_percent  |  Espaço de dados usado por cento  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  tempdb_data_size  |  Kilobytes do tamanho do ficheiro de dados temporários  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  tempdb_log_size  |  Kilobytes do tamanho do ficheiro de registo temporário  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servidores/bases de dados  |  tempdb_log_used_percent  |  Registo de percentagem de temperatura usado  |  Percentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  workers_percent  |  Percentagem de trabalhadores  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/bases de dados  |  xtp_storage_percent  |  Por cento de armazenamento em memória OLTP  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/elásticos  |  allocated_data_storage  |  Espaço de dados atribuído  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/elásticos  |  allocated_data_storage_percent  |  Espaço de dados alocado por cento  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servidores/elásticos  |  cpu_limit  |  Limite do CPU  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/elásticos  |  cpu_percent  |  Percentagem de CPU  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/elásticos  |  cpu_used  |  CPU usado  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores/elásticos  |  database_allocated_data_storage  |  Espaço de dados atribuído  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores/elásticos  |  database_cpu_limit  |  Limite do CPU  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores/elásticos  |  database_cpu_percent  |  Percentagem de CPU  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores/elásticos  |  database_cpu_used  |  CPU usado  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores/elásticos  |  database_dtu_consumption_percent  |  Percentagem de DTU  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores/elásticos  |  database_eDTU_used  |  eDTU usado  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores/elásticos  |  database_log_write_percent  |  Percentagem de IO de registo  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores/elásticos  |  database_physical_data_read_percent  |  Percentagem de ES de Dados  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores/elásticos  |  database_sessions_percent  |  Percentagem de sessões  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores/elásticos  |  database_storage_used  |  Espaço de dados utilizado  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.Sql/servidores/elásticos  |  database_workers_percent  |  Percentagem de trabalhadores  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/elásticos  |  dtu_consumption_percent  |  Percentagem de DTU  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/elásticos  |  eDTU_limit  |  limite eDTU  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/elásticos  |  eDTU_used  |  eDTU usado  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/elásticos  |  log_write_percent  |  Percentagem de IO de registo  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/elásticos  |  physical_data_read_percent  |  Percentagem de ES de Dados  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/elásticos  |  sessions_percent  |  Percentagem de sessões  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/elásticos  |  storage_limit  |  Tamanho máximo de dados  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/elásticos  |  storage_percent  |  Espaço de dados usado por cento  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/elásticos  |  storage_used  |  Espaço de dados utilizado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servidores/elásticos  |  tempdb_data_size  |  Kilobytes do tamanho do ficheiro de dados temporários  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servidores/elásticos  |  tempdb_log_size  |  Kilobytes do tamanho do ficheiro de registo temporário  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servidores/elásticos  |  tempdb_log_used_percent  |  Registo de percentagem de temperatura usado  |  Percentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/elásticos  |  workers_percent  |  Percentagem de trabalhadores  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servidores/elásticos  |  xtp_storage_percent  |  Por cento de armazenamento em memória OLTP  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas  |  Disponibilidade  |  Disponibilidade  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas  |  SucessoE2ELatency  |  Latência de E2E Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas  |  SucessoServerLatency  |  Latência do servidor de sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas  |  Transações  |  Transações  |  de palavras  |  Total | 
| Não  | Não |  Microsoft.Storage/storageAcontas  |  Capacidade Usada  |  Capacidade utilizada  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/blobServices  |  Disponibilidade  |  Disponibilidade  |  Percentagem  |  Média | 
| Não  | Não |  Microsoft.Storage/storageAcontas/blobServices  |  Capacidade blob  |  Capacidade blob  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.Storage/storageAcontas/blobServices  |  BlobCount  |  Contagem de blobs  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/blobServices  |  Contagem de contentores  |  Contagem de contentores blob  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/blobServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.Storage/storageAcontas/blobServices  |  Capacidade de Indexação  |  Capacidade de Índice  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/blobServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/blobServices  |  SucessoE2ELatency  |  Latência de E2E Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/blobServices  |  SucessoServerLatency  |  Latência do servidor de sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/blobServices  |  Transações  |  Transações  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/fileServices  |  Disponibilidade  |  Disponibilidade  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/fileServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.Storage/storageAcontas/fileServices  |  Capacidade de Ficheiros  |  Capacidade de Arquivo  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.Storage/storageAcontas/fileServices  |  Contagem de ficheiros  |  Contagem de ficheiros  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.Storage/storageAcontas/fileServices  |  FileShareCount  |  Contagem de partilha de ficheiros  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.Storage/storageAcontas/fileServices  |  FileShareQuota  |  Tamanho da quota de ações de arquivo  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.Storage/storageAcontas/fileServices  |  FileShareSnapshotCount  |  Contagem de snapshot de partilha de ficheiros  |  de palavras  |  Média | 
| Não  | Não |  Microsoft.Storage/storageAcontas/fileServices  |  FileShareSnapshotSize  |  Tamanho do instantâneo de partilha de arquivo  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/fileServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/fileServices  |  SucessoE2ELatency  |  Latência de E2E Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/fileServices  |  SucessoServerLatency  |  Latência do servidor de sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/fileServices  |  Transações  |  Transações  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/filas Serviços  |  Disponibilidade  |  Disponibilidade  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/filas Serviços  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/filas Serviços  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/filas Serviços  |  Capacidade de Fila  |  Capacidade de fila  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/filas Serviços  |  Contagem de filas  |  Contagem de filas  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/filas Serviços  |  QueueMessageCount  |  Contagem de mensagens de fila  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/filas Serviços  |  SucessoE2ELatency  |  Latência de E2E Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/filas Serviços  |  SucessoServerLatency  |  Latência do servidor de sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/filas Serviços  |  Transações  |  Transações  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/tableServices  |  Disponibilidade  |  Disponibilidade  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/tableServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/tableServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/tableServices  |  SucessoE2ELatency  |  Latência de E2E Com Êxito  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/tableServices  |  SucessoServerLatency  |  Latência do servidor de sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/tableServices  |  Capacidade de Mesa  |  Capacidade de mesa  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/tableServices  |  MesaCount  |  Contagem de tabelas  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/tableServices  |  TabelaseconagemCount  |  Contagem de Entidades de Tabela  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAcontas/tableServices  |  Transações  |  Transações  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientIOPS  |  IOPS totais do cliente  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientLatency  |  Latência média do cliente  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientLockIOPS  |  IOPS de bloqueio de cliente  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientMetadataReadIOPS  |  Metadados de Clientes lêem IOPS  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientMetadataWriteIOPS  |  Metadados de clientes escrevem IOPS  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientReadIOPS  |  Cliente ler IOPS  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientReadThroughput  |  Produção média de leitura de cache  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientWriteIOPS  |  Cliente escrever iOPS  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientWriteThroughput  |  Produção média de escrita de cache  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetAsyncWriteThroughput  |  Produção de escrita assíncronea de armazenamento  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetFillThroughput  |  Produção de preenchimento de armazenamentor  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetHealth  |  Saúde alvo de armazenamento  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetIOPS  |  Total de armazenamentoTarget IOPS  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ArmazenamentoTargetLatency  |  Latência do StorageTarget  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ArmazenamentoTargetMetadataReadIOPS  |  ArmazenamentoTarget Metadados Ler IOPS  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetMetadataWriteIOPS  |  ArmazenamentoTarget Metadados Escrever IOPS  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetReadAheadThroughput  |  ArmazenamentoTarget Ler Antes Produção  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ArmazenamentoTargetReadIOPS  |  ArmazenamentoTarget Ler IOPS  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetSyncWriteThroughput  |  Produção de escrita sincronizada StorageTarget  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetTotalReadThroughput  |  Produção total de leitura do StorageTarget  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetTotalWriteThroughput  |  Produção total de escrita do StorageTarget  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetWriteIOPS  |  ArmazenamentoTarget Escrever IOPS  |  de palavras  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  Uptime  |  Uptime  |  de palavras  |  Média | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  ServidorSyncSessionResult  |  Resultado da sessão de sincronização  |  de palavras  |  Média | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  ArmazenamentoSybatchTransferredFileBytes  |  Bytes sincronizados  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  ArmazenamentoSyncRecalledNetworkBytesByApplication  |  Tamanho de recolha de camadas de nuvem por aplicação  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  ArmazenamentoSyncRecalledTotalNetworkBytes  |  Tamanho de recuperação de nível de nuvem  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  ArmazenamentoSyncRecallIOTotalSizeBytes  |  Recuperação de camadas de nuvem  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  ArmazenamentoSyncRecallThroughputBytesPerSecond  |  Produção de recolha de camadas de nuvem  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  StorageSyncServerHeartbeat  |  Estado online do servidor  |  de palavras  |  Máximo | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  ArmazenamentoSyncSyncSessionAppliedFilesCount  |  Ficheiros Sincronizados  |  de palavras  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  ArmazenamentoSyncSyncSessionPerItemErrorsCount  |  Ficheiros não sincronizados  |  de palavras  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/registradosServers  |  ServerHeartbeat  |  Estado online do servidor  |  de palavras  |  Máximo | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/registradosServers  |  ServerRecallIOTotalSizeBytes  |  Recuperação de camadas de nuvem  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupBatchTransferredFileBytes  |  Bytes sincronizados  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionAppliedFilesCount  |  Ficheiros Sincronizados  |  de palavras  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionPerItemErrorsCount  |  Ficheiros não sincronizados  |  de palavras  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointBatchTransferredFileBytes  |  Bytes sincronizados  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionAppliedFilesCount  |  Ficheiros Sincronizados  |  de palavras  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionPerItemErrorsCount  |  Ficheiros não sincronizados  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutFailedRequests  |  Pedidos de função falhadas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutInputEvents  |  Eventos de Função  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutRequests  |  Pedidos de Função  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  ConversoresErrors  |  Erros de Conversão de Dados  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  DeserializaçãoError  |  Erros de deserialização de entrada  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  DropOrAdjustedEvents  |  Eventos fora de ordem  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  Início dos Eventos  |  Eventos de entrada antecipada  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  Erros  |  Erros de tempo de execução  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventBytes  |  Insutos de Eventos  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  InputEvents  |  Eventos de Entrada  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesBacklogged  |  Eventos de entrada recuados  |  de palavras  |  Máximo | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesPerSecond  |  Fontes de entrada recebidas  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  LateInputEvents  |  Eventos de entrada tardia  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  OutputEvents  |  Eventos de saída  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  OutputWatermarkDelaySeconds  |  Atraso da marca de água  |  Segundos  |  Máximo | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  Utilização de Recursos  |  SU % Utilização  |  Percentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Bytes de leitura de disco  |  Bytes de leitura de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Operações de leitura de disco/sec  |  Operações de leitura de disco/sec  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Bytes de escrita de disco  |  Bytes de escrita de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Operações de escrita de discos/seg  |  Operações de escrita de discos/seg  |  CondePerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadBytesPerSecond  |  Discos de leitura bytes/sec  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadLatency  |  Leitura de disco Latência  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Operações de Leitura de Discos  |  Operações de leitura de discos  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteBytesPerSecond  |  Discos De Escrita Bytes/Sec  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteLatency  |  Latência de escrita de disco  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteOperations  |  Operações de escrita de discos  |  de palavras  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryActive  |  Memória Ativa  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Memorygranted  |  Memória Concedida  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemóriaSUsed  |  Memória utilizada  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Entrada na Rede  |  Entrada na Rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Saída da Rede  |  Saída da Rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkInBytesPerSecond  |  Rede em Bytes/Sec  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkOutBytesPerSecond  |  Network out Bytes/Sec  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Percentagem da CPU  |  Percentagem da CPU  |  Percentagem  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  PercentagemCpuReady  |  Percentagem CPU Pronta  |  Milissegundos  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Ativarequests  |  Pedidos Ativos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Média De Respostas  |  Tempo médio de resposta  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesReceived  |  Dados em  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesSent  |  Data out  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  CpuPercentage  |  Percentagem de CPU  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  DiskQueueLength  |  Comprimento da fila do disco  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http101  |  Http 101  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http2xx  |  Http 2xx  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http3xx  |  Http 3xx  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http401  |  Http 401  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http403  |  Http 403  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http404  |  Http 404  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http406  |  Http 406  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http4xx  |  Http 4xx  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http5xx  |  http Erros do servidor  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  HttpQueueLength  |  Comprimento da fila http  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Grandes Aplicações de Planos de Serviço  |  Trabalhadores do plano de aplicação grande  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MediumAppServicePlanInstances  |  Trabalhadores do Plano de Serviço de Aplicações Médias  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MemóriaPercentage  |  Percentagem de Memória  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Pedidos  |  Pedidos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Pequenas Aplicações de Planos doAppService  |  Trabalhadores do plano de aplicação de pequenas aplicações  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  TotalFrontEnds  |  Total de extremidades frontais  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/workerPools  |  CpuPercentage  |  Percentagem de CPU  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/workerPools  |  MemóriaPercentage  |  Percentagem de Memória  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/workerPools  |  Trabalhadores disponíveis  |  Trabalhadores disponíveis  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/workerPools  |  TrabalhadoresTotal  |  Total de Trabalhadores  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/workerPools  |  TrabalhadoresUsed  |  Trabalhadores Usados  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  BytesReceived  |  Dados em  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  BytesSent  |  Data out  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  CpuPercentage  |  Percentagem de CPU  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  DiskQueueLength  |  Comprimento da fila do disco  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  HttpQueueLength  |  Comprimento da fila http  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  MemóriaPercentage  |  Percentagem de Memória  |  Percentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpCloseWait  |  Espera de fecho de TCP  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpClosing  |  Fecho da TCP  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpEstablished  |  TCP Estabelecido  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpFinWait1  |  TCP Fin Wait 1  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpFinWait2  |  TCP Fin Wait 2  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpLastAck  |  TCP Last Ack  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpSynReceived  |  Sina TCP recebida  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpSynsent  |  TCP Syn enviado  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpTimeWait  |  Espera de tempo da TCP  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  AppConnections  |  Ligações  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Média MemoryWorkingSet  |  Conjunto de trabalho de memória média  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Média De Respostas  |  Tempo médio de resposta  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  BytesReceived  |  Dados em  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  BytesSent  |  Data out  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Horário do Cpu  |  Hora do CPU  |  Segundos  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Antiguidades Atuais  |  Atuais Assembleias  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  FunExecutionCount  |  Contagem de execução de funções  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  FunExecutionUnnits  |  Unidades de execução de funções  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Gen0Collections  |  Coleções de lixo gen 0  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Gen1Collections  |  Coleções de lixo da Gen 1  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Gen2Collections  |  Coleções de lixo gen 2  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Pegas  |  N.º de Identificadores  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  HealthCheckStatus  |  Estado do controlo de saúde  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http101  |  Http 101  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http2xx  |  Http 2xx  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http3xx  |  Http 3xx  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http401  |  Http 401  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http403  |  Http 403  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http404  |  Http 404  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http406  |  Http 406  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http4xx  |  Http 4xx  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http5xx  |  http Erros do servidor  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  HttpResponseTime  |  Tempo de resposta  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  IoOtherBytesPerSecond  |  IO Outros Bytes por Segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  IoOtherOperationsPerSecond  |  IO Outras Operações por Segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  IoReadBytesPerSecond  |  IO Ler Bytes por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  IoReadOperationsPerSecond  |  IO Ler Operações por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  IoWriteBytesPerSecond  |  IO Escrever Bytes por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  IoWriteOperationsPerSecond  |  IO Escrever Operações por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  MemoryWorkingSet  |  Conjunto de trabalho de memória  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  PrivateBytes  |  Bytes privados  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Pedidos  |  Pedidos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  PedidosInApplicationQueue  |  Pedidos na fila de aplicações  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Fios  |  Contagem de fios  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Total DeDomínios  |  Domínios totais de aplicativos  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  TotalAppDomainsUnloaded  |  Total de domínios de aplicações descarregados  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  AppConnections  |  Ligações  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Média MemoryWorkingSet  |  Conjunto de trabalho de memória média  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Média De Respostas  |  Tempo médio de resposta  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  BytesReceived  |  Dados em  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  BytesSent  |  Data out  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Horário do Cpu  |  Hora do CPU  |  Segundos  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Antiguidades Atuais  |  Atuais Assembleias  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  FunExecutionCount  |  Contagem de execução de funções  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  FunExecutionUnnits  |  Unidades de execução de funções  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Gen0Collections  |  Coleções de lixo gen 0  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Gen1Collections  |  Coleções de lixo da Gen 1  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Gen2Collections  |  Coleções de lixo gen 2  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Pegas  |  N.º de Identificadores  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  HealthCheckStatus  |  Estado do controlo de saúde  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http101  |  Http 101  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http2xx  |  Http 2xx  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http3xx  |  Http 3xx  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http401  |  Http 401  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http403  |  Http 403  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http404  |  Http 404  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http406  |  Http 406  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http4xx  |  Http 4xx  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http5xx  |  http Erros do servidor  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  HttpResponseTime  |  Tempo de resposta  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  IoOtherBytesPerSecond  |  IO Outros Bytes por Segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  IoOtherOperationsPerSecond  |  IO Outras Operações por Segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  IoReadBytesPerSecond  |  IO Ler Bytes por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  IoReadOperationsPerSecond  |  IO Ler Operações por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  IoWriteBytesPerSecond  |  IO Escrever Bytes por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  IoWriteOperationsPerSecond  |  IO Escrever Operações por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  MemoryWorkingSet  |  Conjunto de trabalho de memória  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  PrivateBytes  |  Bytes privados  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Pedidos  |  Pedidos  |  de palavras  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  PedidosInApplicationQueue  |  Pedidos na fila de aplicações  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Fios  |  Contagem de fios  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Total DeDomínios  |  Domínios totais de aplicativos  |  de palavras  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  TotalAppDomainsUnloaded  |  Total de domínios de aplicações descarregados  |  de palavras  |  Média | 
