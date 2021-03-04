---
title: Métricas de exportação comportamento com valores NULOS e zero
description: Discussão de valores NU VS Zero ao exportar métricas e um ponteiro para uma lista de que métricas não são exportáveis.
services: azure-monitor
ms.topic: reference
ms.date: 07/22/2020
ms.openlocfilehash: 47b98fe46ac1f2a3e2f3f1a8078ad9ca6f867554
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048850"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Métricas da plataforma do Azure Monitor exportáveis através de Definições de Diagnóstico

O Azure Monitor fornece [métricas da plataforma](../essentials/data-platform-metrics.md) por padrão sem configuração. Fornece várias formas de interagir com as métricas da plataforma, incluindo cartografá-las no portal, acessá-las através da API REST, ou questioná-las usando PowerShell ou CLI. Consulte [as métricas suportadas](./metrics-supported.md) para uma lista completa das métricas da plataforma atualmente disponíveis com o pipeline métrico consolidado do Azure Monitor. Para consultar e aceder a estas métricas, utilize a [versão api 2018-01-01](/rest/api/monitor/metricdefinitions). Outras métricas podem estar disponíveis no portal ou usando APIs legados.

## <a name="metrics-not-exportable-via-diagnostic-settings"></a>Métricas não exportáveis através de configurações de diagnóstico

O conteúdo que costumava estar neste local foi transferido para a [lista suportada de Métricas do Monitor Azure.](./metrics-supported.md#exporting-platform-metrics-to-other-locations)

Existem limitações na exportação de métricas através de configurações de diagnóstico. Todas as métricas são exportáveis utilizando a API REST. 

## <a name="exported-zero-vs-null-values"></a>Valores zero vs NUS exportados 

As métricas têm um comportamento diferente quando se lida com valores 0 ou NUOS.  Algumas métricas relatam 0 quando não são obtidos dados, por exemplo, métricas em falhas http. Outras métricas armazenam NULLs quando não são obtidos dados porque podem indicar que o recurso está offline. Pode ver a diferença ao cartografar estas métricas com valores NULOS aparecendo como [linhas tracejadas](metrics-troubleshoot.md#chart-shows-dashed-line). 

Quando as métricas da plataforma podem ser exportadas através de configurações de diagnóstico, correspondem ao comportamento da métrica. Ou seja, exportam NULLs quando o recurso não envia dados.  Só exportam 0's quando foram verdadeiramente emitidos pelo recurso subjacente. 

Se eliminar um grupo de recursos ou um recurso específico, os dados métricos dos recursos afetados deixaram de ser enviados para a definição de diagnóstico de destinos de exportação. Ou seja, já não aparece em Centros de Eventos, contas de Armazenamento Azure e espaços de trabalho log Analytics.

### <a name="metrics-that-used-to-export-zero-for-null"></a>Métricas que costumavam exportar zero para NU NU

Antes de 1 de junho de 2020, as métricas abaixo **costumavam** emitir '0's quando não havia dados. Esses zeros poderiam então ser exportados para sistemas a jusante como o Log Analytics e o armazenamento Azure.  Este comportamento causou alguma confusão entre os '0' reais (emitidos por recurso) e os '0' (NULs), pelo que o comportamento foi alterado para corresponder ao da métrica subjacente, como mencionado na secção anterior. 

A mudança ocorreu em todas as nuvens públicas e privadas.

A alteração não teve impacto no comportamento de nenhuma das seguintes experiências: 
   - Registos de recursos da plataforma exportados através de Definições de Diagnóstico
   - Gráfico de métricas no Explorador de Métricas
   - Alertando nas métricas da plataforma
 
Segue-se uma lista das métricas cujo comportamento mudou. 

| ResourceType                    | Metric               |  Nome métrico do Jogo  | 
|---------------------------------|----------------------|---------------------|
| Microsoft.ApiManagement/service | Requessionais não autorizados |  Pedidos de Gateway não autorizados (Deprecados)  | 
| Microsoft.ApiManagement/service | TotalRequests |  Total de pedidos de gateway (precotado)  | 
| Microsoft.ApiManagement/service | Requess de sucesso |  Pedidos de Gateway bem sucedidos (Deprecados)  | 
| Microsoft.ApiManagement/service | Pedidos |  Pedidos  | 
| Microsoft.ApiManagement/service | Outros Requests |  Outros pedidos de gateway (precotados)  | 
| Microsoft.ApiManagement/service | Requessos Falhados |  Pedidos de Gateway falhados (Deprecados)  | 
| Microsoft.ApiManagement/service | EventHubTotalFailedEvents |  Eventos falhados do EventHub  | 
| Microsoft.ApiManagement/service | EventHubTotalEvents |  Total de eventos EventHub  | 
| Microsoft.ApiManagement/service | EventHubTotalBytesSent |  Tamanho dos eventos Do EventHub  | 
| Microsoft.ApiManagement/service | EventHubTimedoutEvents |  Eventos Timed out EventHub  | 
| Microsoft.ApiManagement/service | EventHubThrottledEvents |  Eventos Throttled EventHub  | 
| Microsoft.ApiManagement/service | EventoSHubSuccessfulEvents |  Eventos de sucesso EventHub  | 
| Microsoft.ApiManagement/service | EventosHubRejectedEvents |  Eventos de EventHub rejeitados  | 
| Microsoft.ApiManagement/service | EventHubDroppedEvents |  Eventos do EventHub abandonados  | 
| Microsoft.ApiManagement/service | Duração |  Duração geral dos pedidos de gateway  | 
| Microsoft.ApiManagement/service | BackendDuration |  Duração dos Pedidos de Backend  | 
| Microsoft.DBforMariaDB/servidores | storage_used |  Armazenamento utilizado  | 
| Microsoft.DBforMariaDB/servidores | storage_percent |  Por cento de armazenamento  | 
| Microsoft.DBforMariaDB/servidores | storage_limit |  Limite de armazenamento  | 
| Microsoft.DBforMariaDB/servidores | serverlog_storage_usage |  Armazenamento de registo de servidor utilizado  | 
| Microsoft.DBforMariaDB/servidores | serverlog_storage_percent |  Por cento de armazenamento de registo de servidor  | 
| Microsoft.DBforMariaDB/servidores | serverlog_storage_limit |  Limite de armazenamento de registo de servidor  | 
| Microsoft.DBforMariaDB/servidores | seconds_behind_master |  Atraso de replicação em segundos  | 
| Microsoft.DBforMariaDB/servidores | network_bytes_ingress |  Entrada na Rede  | 
| Microsoft.DBforMariaDB/servidores | network_bytes_egress |  Saída da Rede  | 
| Microsoft.DBforMariaDB/servidores | memory_percent |  Por cento da memória  | 
| Microsoft.DBforMariaDB/servidores | io_consumption_percent |  IO por cento  | 
| Microsoft.DBforMariaDB/servidores | cpu_percent |  CPU por cento  | 
| Microsoft.DBforMariaDB/servidores | connections_failed |  Ligações com Falhas  | 
| Microsoft.DBforMariaDB/servidores | backup_storage_used |  Armazenamento de backup utilizado  | 
| Microsoft.DBforMariaDB/servidores | active_connections |  Ligações Ativas  | 
| Microsoft.DBforMySQL/servidores | storage_used |  Armazenamento utilizado  | 
| Microsoft.DBforMySQL/servidores | storage_percent |  Por cento de armazenamento  | 
| Microsoft.DBforMySQL/servidores | storage_limit |  Limite de armazenamento  | 
| Microsoft.DBforMySQL/servidores | serverlog_storage_usage |  Armazenamento de registo de servidor utilizado  | 
| Microsoft.DBforMySQL/servidores | serverlog_storage_percent |  Por cento de armazenamento de registo de servidor  | 
| Microsoft.DBforMySQL/servidores | serverlog_storage_limit |  Limite de armazenamento de registo de servidor  | 
| Microsoft.DBforMySQL/servidores | seconds_behind_master |  Atraso de replicação em segundos  | 
| Microsoft.DBforMySQL/servidores | network_bytes_ingress |  Entrada na Rede  | 
| Microsoft.DBforMySQL/servidores | network_bytes_egress |  Saída da Rede  | 
| Microsoft.DBforMySQL/servidores | memory_percent |  Por cento da memória  | 
| Microsoft.DBforMySQL/servidores | io_consumption_percent |  IO por cento  | 
| Microsoft.DBforMySQL/servidores | cpu_percent |  CPU por cento  | 
| Microsoft.DBforMySQL/servidores | connections_failed |  Ligações com Falhas  | 
| Microsoft.DBforMySQL/servidores | backup_storage_used |  Armazenamento de backup utilizado  | 
| Microsoft.DBforMySQL/servidores | active_connections |  Ligações Ativas  | 
| Microsoft.Devices/Conta | digitaltwins.telemetria.nós |  Espaço reservado para telemetria de gémeos digitais  | 
| Microsoft.Devices/IotHubs | twinQueries.sucesso |  Consultas gémeas bem sucedidas  | 
| Microsoft.Devices/IotHubs | twinQueries.resultSize |  Tamanho do resultado de consultas gémeas  | 
| Microsoft.Devices/IotHubs | twinQueries.falha |  Consultas gémeas falhadas  | 
| Microsoft.Devices/IotHubs | jobs.queryJobs.sucesso |  Consultas de trabalho bem sucedidas  | 
| Microsoft.Devices/IotHubs | jobs.consultaJobs.falha |  Consultas de trabalho falhadas  | 
| Microsoft.Devices/IotHubs | jobs.listJobs.sucesso |  Chamadas bem-sucedidas para listar empregos  | 
| Microsoft.Devices/IotHubs | empregos.listJobs.fracasso |  Chamadas falhadas para listar empregos  | 
| Microsoft.Devices/IotHubs | empregos.falhou |  Empregos falhados  | 
| Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.success |  Criações bem sucedidas de trabalhos de atualização dupla  | 
| Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.failure |  Criações falhadas de trabalhos de atualização dupla  | 
| Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.success |  Criações bem sucedidas de trabalhos de invocação de métodos  | 
| Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.fail |  Criações falhadas de trabalhos de invocação de métodos  | 
| Microsoft.Devices/IotHubs | empregos.concluídos |  Trabalhos concluídos  | 
| Microsoft.Devices/IotHubs | jobs.cancelJob.sucesso |  Cancelamentos de emprego bem-sucedidos  | 
| Microsoft.Devices/IotHubs | jobs.cancelJob.failure |  Cancelamentos de empregos falhados  | 
| Microsoft.Devices/IotHubs | EventGridLatency |  Latência da grelha de eventos (pré-visualização)  | 
| Microsoft.Devices/IotHubs | EventGridDeliveries |  Entregas de Grelha de Eventos (pré-visualização)  | 
| Microsoft.Devices/IotHubs | dispositivos.totalDevices |  Total de dispositivos (precotados)  | 
| Microsoft.Devices/IotHubs | dispositivos.connectedDevices.allProtocol |  Dispositivos ligados (precotados)   | 
| Microsoft.Devices/IotHubs | dispositivoDataUsageV2 |  Total da utilização dos dados do dispositivo (pré-visualização)  | 
| Microsoft.Devices/IotHubs | dispositivoDataUsage |  Total da utilização de dados do dispositivo  | 
| Microsoft.Devices/IotHubs | dailyMessageQuotaUsed |  Número total de mensagens utilizadas  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.success |  Atualizações gémeas bem sucedidas dos dispositivos  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.size |  Tamanho das atualizações gémeas dos dispositivos  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.failure |  Falhas em duas atualizações a partir de dispositivos  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.success |  Leituras gémeas bem sucedidas de dispositivos  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.size |  Tamanho de resposta de leituras gémeas de dispositivos  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.failure |  Leituras gémeas falhadas dos dispositivos  | 
| Microsoft.Devices/IotHubs | d2c.telemetria.ingress.success |  Mensagens de telemetria enviadas  | 
| Microsoft.Devices/IotHubs | d2c.telemetria.ingress.sendThrottle |  Número de erros de estrangulamento  | 
| Microsoft.Devices/IotHubs | d2c.telemetria.ingress.allProtocol |  Mensagem de telemetria envia tentativas  | 
| Microsoft.Devices/IotHubs | d2c.telemetria.egress.sucesso |  Encaminhamento: mensagens de telemetria entregues  | 
| Microsoft.Devices/IotHubs | d2c.telemetria.egress.órfão |  Encaminhamento: mensagens de telemetria órfãs   | 
| Microsoft.Devices/IotHubs | d2c.telemetria.egress.inválido |  Encaminhamento: mensagens de telemetria incompatíveis  | 
| Microsoft.Devices/IotHubs | d2c.telemetria.egress.fallback |  Encaminhamento: mensagens entregues para recuo  | 
| Microsoft.Devices/IotHubs | d2c.telemetria.egress.drop |  Encaminhamento: mensagens de telemetria caídas   | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latncy.storage |  Encaminhamento: latência da mensagem para armazenamento  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latncy.serviceBusTopics |  Encaminhamento: latência de mensagem para o tópico do autocarro de serviço  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latncy.serviceBusQueues |  Encaminhamento: latência da mensagem para a fila do autocarro de serviço  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latncy.eventHubs |  Encaminhamento: latência da mensagem para o Centro de Eventos  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latncy.builtIn.events |  Encaminhamento: latência da mensagem para mensagens/eventos  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.bytes |  Encaminhamento: dados entregues ao armazenamento  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.blobs |  Encaminhamento: bolhas entregues ao armazenamento  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage |  Encaminhamento: mensagens entregues no armazenamento  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusTopics |  Encaminhamento: mensagens entregues no Service Bus Topic  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusQueues |  Encaminhamento: mensagens entregues na Fila de Autocarros de Serviço  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.eventHubs |  Encaminhamento: mensagens entregues no Centro de Eventos  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.builtIn.events |  Encaminhamento: mensagens entregues a mensagens/eventos  | 
| Microsoft.Devices/IotHubs | configurações |  Métricas de configuração  | 
| Microsoft.Devices/IotHubs | C2DMessagesExpired |  Mensagens C2D Expiradas (pré-visualização)  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.success |  Atualizações gémeas bem sucedidas a partir do final  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.size |  Tamanho das atualizações gémeas a partir da parte de trás  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.failure |  Falhas em duas atualizações a partir do final  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.success |  Gémea bem sucedida lê a partir da parte de trás  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.size |  Tamanho de resposta de leituras gémeas da parte de trás  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.failure |  Gémeo falhado lê do fundo  | 
| Microsoft.Devices/IotHubs | c2d.methods.success |  Invocações de métodos diretos bem-sucedidas  | 
| Microsoft.Devices/IotHubs | c2d.methods.responseSize |  Tamanho da resposta das invocações do método direto  | 
| Microsoft.Devices/IotHubs | c2d.methods.requestSize |  Tamanho do pedido de invocações de método direto  | 
| Microsoft.Devices/IotHubs | c2d.methods.falha |  Invocações de método direto falhadas  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.reject.success |  Mensagens C2D rejeitadas  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.complete.success |  Entregas de mensagens C2D concluídas  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.abandon.success |  Mensagens C2D abandonadas  | 
| Microsoft.Devices/provisioningServices | InscriçõesAptas |  Tentativas de registo  | 
| Microsoft.Devices/provisioningServices | Configurações de Dispositivos |  Dispositivos atribuídos  | 
| Microsoft.Devices/provisioningServices | AttestationAttempts |  Tentativas de atestado  | 
| Microsoft.DocumentDB/base de dadosAcontas | TotalRequestUnits |  Total de Unidades de Pedido  | 
| Microsoft.DocumentDB/base de dadosAcontas | TotalRequests |  Total de Pedidos  | 
| Microsoft.DocumentDB/base de dadosAcontas | MongoRequests |  Pedidos de Mongo  | 
| Microsoft.DocumentDB/base de dadosAcontas | MongoRequestCharge |  Taxa de pedido de Mongo  | 
| Microsoft.EventGrid/domínios | Publicar Inimitrodências |  Publicar Latência de Sucesso  | 
| Microsoft.EventGrid/domínios | PublicarSuccessCount |  Eventos Publicados  | 
| Microsoft.EventGrid/domínios | PublicarFailCount |  Publicar Eventos Falhados  | 
| Microsoft.EventGrid/domínios | MatchedEventCount |  Eventos Combinados  | 
| Microsoft.EventGrid/domínios | DropEventCount |  Eventos abandonados  | 
| Microsoft.EventGrid/domínios | EntregaSSuccessCount |  Eventos Entregues  | 
| Microsoft.EventGrid/domínios | DeadLetteredCount |  Eventos lettered mortos  | 
| Microsoft.EventGrid/eventSubscriptions | MatchedEventCount |  Eventos Combinados  | 
| Microsoft.EventGrid/eventSubscriptions | DropEventCount |  Eventos abandonados  | 
| Microsoft.EventGrid/eventSubscriptions | EntregaSSuccessCount |  Eventos Entregues  | 
| Microsoft.EventGrid/eventSubscriptions | DeadLetteredCount |  Eventos lettered mortos  | 
| Microsoft.EventGrid/extensionTopics | InigualávelEventCount |  Eventos incomparáveis  | 
| Microsoft.EventGrid/extensionTopics | Publicar Inimitrodências |  Publicar Latência de Sucesso  | 
| Microsoft.EventGrid/extensionTopics | PublicarSuccessCount |  Eventos Publicados  | 
| Microsoft.EventGrid/extensionTopics | PublicarFailCount |  Publicar Eventos Falhados  | 
| Microsoft.EventGrid/tópicos | InigualávelEventCount |  Eventos incomparáveis  | 
| Microsoft.EventGrid/tópicos | Publicar Inimitrodências |  Publicar Latência de Sucesso  | 
| Microsoft.EventGrid/tópicos | PublicarSuccessCount |  Eventos Publicados  | 
| Microsoft.EventGrid/tópicos | PublicarFailCount |  Publicar Eventos Falhados  | 
| Microsoft.EventHub/clusters | Despesas de Saída |  Mensagens Enviadas  | 
| Microsoft.EventHub/clusters | OutgoingBytes |  Bytes de saída.  | 
| Microsoft.EventHub/clusters | IncomingRequests |  Pedidos Recebidos  | 
| Microsoft.EventHub/clusters | Entradas |  Mensagens Recebidas  | 
| Microsoft.EventHub/clusters | IncomingBytes |  Bytes Recebidos.  | 
| Microsoft.EventHub/espaços de nome | SVRBSY |  Erros ocupados do servidor (preprecados)  | 
| Microsoft.EventHub/espaços de nome | SUCCREQ |  Pedidos de sucesso (Deprecados)  | 
| Microsoft.EventHub/espaços de nome | OUTMSGS |  Mensagens de saída (obsoletas) (Depreciadas)  | 
| Microsoft.EventHub/espaços de nome | Despesas de Saída |  Mensagens Enviadas  | 
| Microsoft.EventHub/espaços de nome | OutgoingBytes |  Bytes de saída.  | 
| Microsoft.EventHub/espaços de nome | MISCERR |  Outros Erros (Precotados)  | 
| Microsoft.EventHub/espaços de nome | INTERR |  Erros internos do servidor (preprecados)  | 
| Microsoft.EventHub/espaços de nome | INREQS |  Pedidos de Entrada (Deprecados)  | 
| Microsoft.EventHub/espaços de nome | INMSGS |  Mensagens recebidas (obsoletas) (Depreciadas)  | 
| Microsoft.EventHub/espaços de nome | IncomingRequests |  Pedidos Recebidos  | 
| Microsoft.EventHub/espaços de nome | Entradas |  Mensagens Recebidas  | 
| Microsoft.EventHub/espaços de nome | IncomingBytes |  Bytes Recebidos.  | 
| Microsoft.EventHub/espaços de nome | FAILREQ |  Pedidos Falhados (Deprecados)  | 
| Microsoft.EventHub/espaços de nome | EHOUTMSGS |  Mensagens de saída (depreciadas)  | 
| Microsoft.EventHub/espaços de nome | EHOUTMBS |  Bytes de saída (obsoletos) (Preprecados)  | 
| Microsoft.EventHub/espaços de nome | EHOUTBYTES |  Bytes de saída (Deprecados)  | 
| Microsoft.EventHub/espaços de nome | EFINMSGS |  Mensagens recebidas (depreciadas)  | 
| Microsoft.EventHub/espaços de nome | EHINMBS |  Bytes de entrada (obsoletos) (Deprecados)  | 
| Microsoft.EventHub/espaços de nome | EHINBYTES |  Bytes de entrada (Deprecados)  | 
| Microsoft.EventHub/espaços de nome | EHAMSGS |  Mensagens de arquivo (Depreciadas)  | 
| Microsoft.EventHub/espaços de nome | EHAMBS |  Produção de mensagem de arquivo (Precotada)  | 
| Microsoft.EventHub/espaços de nome | EHABL |  Mensagens de atraso de arquivo (Deprecadas)  | 
| Microsoft.HDInsight/clusters | NumActiveWorkers |  Número de trabalhadores ativos  | 
| Microsoft.HDInsight/clusters | GatewayRequests |  Pedidos de Gateway  | 
| Microsoft.HDInsight/clusters | CategorizadosGatewayRequests |  Pedidos de Gateway categorizados  | 
| Microsoft.Insights/AutoscaleSettings | ScaleActionsInitiado |  Ações de escala iniciadas  | 
| Microsoft.Insights/Componentes | vestígios/contagem |  Rastreios  | 
| Microsoft.Insights/Componentes | performanceCounters/requestsPerSecond |  Taxa de pedido HTTP  | 
| Microsoft.Insights/Componentes | performanceCounters/requestsInQueue |  Pedidos HTTP na fila de candidaturas  | 
| Microsoft.Insights/Componentes | performanceCounters/excepçõesPerSecond |  Taxa de exceção  | 
| Microsoft.Insights/Componentes | pageVers/contagem |  Vistas da página  | 
| Microsoft.Insights/Componentes | exceções/contagem |  Exceções  | 
| Microsoft.Kusto/Clusters | StreamingIngestResults |  Resultado da Ingestão de Transmissão em Fluxo  | 
| Microsoft.Kusto/Clusters | StreamingIngestDuration |  Duração da Ingestão de Transmissão em Fluxo  | 
| Microsoft.Kusto/Clusters | StreamingIngestDataRate |  Velocidade dos Dados na Ingestão de Transmissão em Fluxo  | 
| Microsoft.Kusto/Clusters | SteamingIngestRequestRate |  Velocidade dos Pedidos na Ingestão de Transmissão em Fluxo  | 
| Microsoft.Kusto/Clusters | Queriaduração |  Duração da consulta  | 
| Microsoft.Kusto/Clusters | KeepAlive |  Mantenha-se vivo  | 
| Microsoft.Kusto/Clusters | IngestionVolumeInMB |  Volume de ingestão (em MB)  | 
| Microsoft.Kusto/Clusters | Ingestionutilização |  Utilização da ingestão  | 
| Microsoft.Kusto/Clusters | IngestionResult |  Resultado da ingestão  | 
| Microsoft.Kusto/Clusters | IngestionLatencyInSeconds |  Latência da ingestão (em segundos)  | 
| Microsoft.Kusto/Clusters | ExportaçãoUtilização |  Utilização da Exportação  | 
| Microsoft.Kusto/Clusters | EventosProcessadoForEventHubs |  Eventos processados (para Centros de Eventos/IoT)  | 
| Microsoft.Kusto/Clusters | CPU |  CPU  | 
| Microsoft.Kusto/Clusters | ContínuaExportResult |  Resultado contínuo da exportação  | 
| Microsoft.Kusto/Clusters | ContínuaExportPendingCount |  Contagem pendente de exportação contínua  | 
| Microsoft.Kusto/Clusters | ContinuousExportNumOfRecordsExported |  Exportação contínua - num de registos exportados  | 
| Microsoft.Kusto/Clusters | ContinuaExportMaxLatenessMinutes |  Minutos de atraso de exportação contínua  | 
| Microsoft.Kusto/Clusters | CacheUtilização |  Utilização de cache  | 
| Microsoft.Logic/integrationServiceEnvironments | TriggerThrottledEvents |  Eventos aceleradores do gatilho  | 
| Microsoft.Logic/integrationServiceEnvironments | GatilhosSsueded |  Gatilhos Bem Sucedidos   | 
| Microsoft.Logic/integrationServiceEnvironments | GatilhosStarted |  Gatilhos Iniciados   | 
| Microsoft.Logic/integrationServiceEnvironments | GatilhosSkipped |  Gatilhos ignorados  | 
| Microsoft.Logic/integrationServiceEnvironments | Gatilhos Disparados |  Gatilhos disparados   | 
| Microsoft.Logic/integrationServiceEnvironments | GatilhosFailed |  Gatilhos falhados   | 
| Microsoft.Logic/integrationServiceEnvironments | GatilhosCompleted |  Gatilhos Concluídos   | 
| Microsoft.Logic/integrationServiceEnvironments | RunThrottledEvents |  Executar Eventos Throttled  | 
| Microsoft.Logic/integrationServiceEnvironments | RunStartThrottledEvents |  Executar eventos throttled do início  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsSucceed |  Runs Succeeded  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsStarted |  Execuções iniciadas  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsFailed |  Runs Falhado  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsCompleted |  Execuções Concluídas  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsCancelled |  Execuções canceladas  | 
| Microsoft.Logic/integrationServiceEnvironments | RunFailurePercentage |  Percentagem de falha de execução  | 
| Microsoft.Logic/integrationServiceEnvironments | ActionThrottledEvents |  Eventos throttled de ação  | 
| Microsoft.Logic/integrationServiceEnvironments | AçõesDuzidas |  Ações Bem Sucedidas   | 
| Microsoft.Logic/integrationServiceEnvironments | AçõesStarted |  Ações Iniciadas   | 
| Microsoft.Logic/integrationServiceEnvironments | AçõesSkipped |  Ações ignoradas   | 
| Microsoft.Logic/integrationServiceEnvironments | Ações Destruídas |  Ações falhadas   | 
| Microsoft.Logic/integrationServiceEnvironments | Ações Computação |  Ações Concluídas   | 
| Microsoft.Logic/workflows | TriggerThrottledEvents |  Eventos aceleradores do gatilho  | 
| Microsoft.Logic/workflows | GatilhosSsueded |  Gatilhos Bem Sucedidos   | 
| Microsoft.Logic/workflows | GatilhosStarted |  Gatilhos Iniciados   | 
| Microsoft.Logic/workflows | GatilhosSkipped |  Gatilhos ignorados  | 
| Microsoft.Logic/workflows | Gatilhos Disparados |  Gatilhos disparados   | 
| Microsoft.Logic/workflows | GatilhosFailed |  Gatilhos falhados   | 
| Microsoft.Logic/workflows | GatilhosCompleted |  Gatilhos Concluídos   | 
| Microsoft.Logic/workflows | TotalBillableExecutions |  Execuções totais faturantes  | 
| Microsoft.Logic/workflows | RunThrottledEvents |  Executar Eventos Throttled  | 
| Microsoft.Logic/workflows | RunStartThrottledEvents |  Executar eventos throttled do início  | 
| Microsoft.Logic/workflows | RunsSucceed |  Runs Succeeded  | 
| Microsoft.Logic/workflows | RunsStarted |  Execuções iniciadas  | 
| Microsoft.Logic/workflows | RunsFailed |  Runs Falhado  | 
| Microsoft.Logic/workflows | RunsCompleted |  Execuções Concluídas  | 
| Microsoft.Logic/workflows | RunsCancelled |  Execuções canceladas  | 
| Microsoft.Logic/workflows | RunFailurePercentage |  Percentagem de falha de execução  | 
| Microsoft.Logic/workflows | BillingUsageStorageConsumption |  Faturação de utilização para execuções de consumo de armazenamento  | 
| Microsoft.Logic/workflows | BillingUsageStorageConsumption |  Faturação de utilização para execuções de consumo de armazenamento  | 
| Microsoft.Logic/workflows | BillingUsageStandardConnector |  Utilização de faturação para execuções padrão do conector  | 
| Microsoft.Logic/workflows | BillingUsageStandardConnector |  Utilização de faturação para execuções padrão do conector  | 
| Microsoft.Logic/workflows | Faturação UsageNativeOperação |  Utilização de faturação para execuções de operações nativas  | 
| Microsoft.Logic/workflows | Faturação UsageNativeOperação |  Utilização de faturação para execuções de operações nativas  | 
| Microsoft.Logic/workflows | BillableTriggerExecutions |  Execuções de gatilhos faturantes  | 
| Microsoft.Logic/workflows | BillableActionExecutions |  Execuções de ação executáveis  | 
| Microsoft.Logic/workflows | ActionThrottledEvents |  Eventos throttled de ação  | 
| Microsoft.Logic/workflows | AçõesDuzidas |  Ações Bem Sucedidas   | 
| Microsoft.Logic/workflows | AçõesStarted |  Ações Iniciadas   | 
| Microsoft.Logic/workflows | AçõesSkipped |  Ações ignoradas   | 
| Microsoft.Logic/workflows | Ações Destruídas |  Ações falhadas   | 
| Microsoft.Logic/workflows | Ações Computação |  Ações Concluídas   | 
| Microsoft.Network/frontdoors | WebApplicationFirewallRequestCount |  Contagem de pedidos de firewall de aplicação web  | 
| Microsoft.Network/frontdoors | TotalLatency |  Latência total  | 
| Microsoft.Network/frontdoors | Tamanho das respostas |  Tamanho da resposta  | 
| Microsoft.Network/frontdoors | Solicitação |  Tamanho do pedido  | 
| Microsoft.Network/frontdoors | PedidoCount |  Número de Pedidos  | 
| Microsoft.Network/frontdoors | BillableResponseSize |  Tamanho da resposta faturada  | 
| Microsoft.Network/frontdoors | BackendRequestatency |  Pedido de backend Latência  | 
| Microsoft.Network/frontdoors | BackendRequestCount |  Contagem de pedidos de backend  | 
| Microsoft.Network/frontdoors | BackendHealthPercentage |  Percentagem de Saúde backend  | 
| Microsoft.Network/trafficManagerProfiles | QpsByEndpoint |  Consultas por Endpoint Devolvido  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | agendado.pendente |  Notificações Agendadas Pendentes  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.update |  Operações de Atualização de Registo  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.get |  Operações de Leitura de Registo  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.delete |  Operações de Eliminação de Registos  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.create |  Criar Operações de Criação de Registos  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registro.todos |  Operações de Registo  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.wrongtoken |  Erros de autorização da WNS (Ficha Errada)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.tokenproviderunreachable |  Erros de autorização WNS (inacessíveis)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.throttled |  Notificações throttled WNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.sucesso |  Notificações bem sucedidas da WNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.pnserror |  Erros do WNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidtoken |  Erros de autorização da WNS (token inválido)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationsize |  Erro de tamanho de notificação inválido da WNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationformat |  Formato de notificação inválida da WNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidcredentis |  Erros de autorização WNS (Credenciais Inválidas)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.expiredchannel |  Erro do canal expirado da WNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.drop |  Notificações retiradas da WNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channelthrottled |  Canal WNS Acelerado  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channeldisconnected |  Canal WNS desligado  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.badchannel |  Erro do canal WNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.authenticationerror |  Erros de autenticação WNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.throttled |  Notificações throttled MPNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.sucesso |  Notificações bem sucedidas da MPNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.pnserror |  Erros do MPNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidnotificationformat |  Formato de Notificação Inválida mpns  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidcredentis |  Credenciais inválidas da MPNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.drop |  Notificações retiradas do MPNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.channeldisconnected |  Canal MPNS desligado  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.badchannel |  Erro do Canal Mau da MPNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.authenticationerror |  Erros de Autenticação MPNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.canalil errado |  Erro do canal errado gCM  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.throttled |  Notificações throttled GCM  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.sucesso |  Notificações bem sucedidas da GCM  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.pnserror |  Erros do GCM  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationsize |  Erro de tamanho de notificação inválido GCM  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationformat |  Formato de notificação inválida GCM  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidcredentis |  Erros de autorização GCM (Credenciais Inválidas)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.expiredchannel |  Erro do canal expirado GCM  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.badchannel |  Erro do Canal Mau gCM  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.authenticationeror |  Erros de autenticação GCM  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.sucesso |  Notificações bem sucedidas da APNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.pnserror |  Erros da APNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidnotificationsize |  Erro de tamanho de notificação inválido da APNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidcredentis |  Erros de Autorização da APNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.expiredchannel |  Erro do Canal caducado da APNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.badchannel |  Erro do Canal Mau da APNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.sucesso |  Notificações bem sucedidas  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.pnserror |  Erros do sistema de notificação externa  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.invalidpayload |  Erros de carga útil  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.channelerror |  Erros do Canal  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | notificationhub.pushes |  Todas as notificações de saída  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | instalação.upsert |  Criar ou atualizar operações de instalação  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | instalação.patch |  Operações de instalação de remendos  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | instalação.get |  Obter Operações de Instalação  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | instalação.excluir |  Excluir operações de instalação  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | instalação.todos |  Operações de Gestão de Instalações  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled.cancel |  Notificações de push programadas canceladas  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | entrada.programado |  Notificações de push agendadas enviadas  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.requests |  Todos os Pedidos de Entrada  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.failedrequests |  Todos os pedidos falhados de entrada  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | entrada |  Mensagens Recebidas  | 
| Microsoft.OperationalInsights/workspaces | Heartbeat |  Heartbeat  | 
| Microsoft.Relay/namespaces | BytesTransferido |  BytesTransferido  | 
| Microsoft.ServiceBus/namespaces | Despesas de Saída |  Mensagens Enviadas  | 
| Microsoft.ServiceBus/namespaces | IncomingRequests |  Pedidos Recebidos  | 
| Microsoft.ServiceBus/namespaces | Entradas |  Mensagens Recebidas  | 
| Microsoft.SignalrService/Signalr | UserErrors |  Erros do Utilizador  | 
| Microsoft.SignalrService/Signalr | SystemErrors |  Erros do Sistema  | 
| Microsoft.SignalrService/Signalr | OutboundTraffic |  Tráfego de Saída  | 
| Microsoft.SignalrService/Signalr | Contagem de mensagens |  Contagem de mensagens  | 
| Microsoft.SignalrService/Signalr | InboundTraffic |  Tráfego de Entrada  | 
| Microsoft.SignalrService/Signalr | Contagem de Ligações |  Contagem de Ligação  | 
| Microsoft.Sql/managedInstances | avg_cpu_percent |  Percentagem média de CPU  | 
| Microsoft.Sql/servidores | dtu_used |  DTU usado  | 
| Microsoft.Sql/servidores | dtu_consumption_percent |  Percentagem de DTU  | 
| Microsoft.Sql/servidores/bases de dados | xtp_storage_percent |  In-Memory por cento de armazenamento OLTP  | 
| Microsoft.Sql/servidores/bases de dados | workers_percent |  Percentagem de trabalhadores  | 
| Microsoft.Sql/servidores/bases de dados | sessions_percent |  Percentagem de sessões  | 
| Microsoft.Sql/servidores/bases de dados | physical_data_read_percent |  Percentagem de ES de Dados  | 
| Microsoft.Sql/servidores/bases de dados | log_write_percent |  Percentagem de IO de registo  | 
| Microsoft.Sql/servidores/bases de dados | dwu_used |  DWU usado  | 
| Microsoft.Sql/servidores/bases de dados | dwu_consumption_percent |  Percentagem de DWU  | 
| Microsoft.Sql/servidores/bases de dados | dtu_used |  DTU usado  | 
| Microsoft.Sql/servidores/bases de dados | dtu_consumption_percent |  Percentagem de DTU  | 
| Microsoft.Sql/servidores/bases de dados | impasse |  Impasses  | 
| Microsoft.Sql/servidores/bases de dados | cpu_used |  CPU usado  | 
| Microsoft.Sql/servidores/bases de dados | cpu_percent |  Percentagem de CPU  | 
| Microsoft.Sql/servidores/bases de dados | connection_successful |  Conexões bem sucedidas  | 
| Microsoft.Sql/servidores/bases de dados | connection_failed |  Ligações com Falhas  | 
| Microsoft.Sql/servidores/bases de dados | cache_hit_percent |  Percentagem de impacto de cache  | 
| Microsoft.Sql/servidores/bases de dados | blocked_by_firewall |  Bloqueado pela Firewall  | 
| Microsoft.Sql/servidores/elásticos | xtp_storage_percent |  In-Memory por cento de armazenamento OLTP  | 
| Microsoft.Sql/servidores/elásticos | workers_percent |  Percentagem de trabalhadores  | 
| Microsoft.Sql/servidores/elásticos | sessions_percent |  Percentagem de sessões  | 
| Microsoft.Sql/servidores/elásticos | physical_data_read_percent |  Percentagem de ES de Dados  | 
| Microsoft.Sql/servidores/elásticos | log_write_percent |  Percentagem de IO de registo  | 
| Microsoft.Sql/servidores/elásticos | eDTU_used |  eDTU usado  | 
| Microsoft.Sql/servidores/elásticos | dtu_consumption_percent |  Percentagem de DTU  | 
| Microsoft.Sql/servidores/elásticos | cpu_percent |  Percentagem de CPU  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | TotalFrontEnds |  Total de extremidades frontais  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Pequenas Aplicações de Planos doAppService |  Trabalhadores do plano de aplicação de pequenas aplicações  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Pedidos |  Pedidos  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | MemóriaPercentage |  Percentagem de Memória  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances |  Trabalhadores do Plano de Serviço de Aplicações Médias  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Grandes Aplicações de Planos de Serviço |  Trabalhadores do plano de aplicação grande  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | HttpQueueLength |  Comprimento da fila http  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http5xx |  http Erros do servidor  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http4xx |  Http 4xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http406 |  Http 406  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http404 |  Http 404  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http403 |  Http 403  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http401 |  Http 401  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http3xx |  Http 3xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http2xx |  Http 2xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http101 |  Http 101  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | DiskQueueLength |  Comprimento da fila do disco  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | CpuPercentage |  Percentagem de CPU  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | BytesSent |  Data out  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | BytesReceived |  Dados em  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | AverageResponseTime |  Tempo médio de resposta  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Ativarequests |  Pedidos Ativos  | 
| Microsoft.Web/hostingEnvironments/workerPools | TrabalhadoresUsed |  Trabalhadores Usados  | 
| Microsoft.Web/hostingEnvironments/workerPools | TrabalhadoresTotal |  Total de Trabalhadores  | 
| Microsoft.Web/hostingEnvironments/workerPools | Trabalhadores disponíveis |  Trabalhadores disponíveis  | 
| Microsoft.Web/hostingEnvironments/workerPools | MemóriaPercentage |  Percentagem de Memória  | 
| Microsoft.Web/hostingEnvironments/workerPools | CpuPercentage |  Percentagem de CPU  | 
| Microsoft.Web/serverfarms | TcpTimeWait |  Espera de tempo da TCP  | 
| Microsoft.Web/serverfarms | TcpSynsent |  TCP Syn enviado  | 
| Microsoft.Web/serverfarms | TcpSynReceived |  Sina TCP recebida  | 
| Microsoft.Web/serverfarms | TcpLastAck |  TCP Last Ack  | 
| Microsoft.Web/serverfarms | TcpFinWait2 |  TCP Fin Wait 2  | 
| Microsoft.Web/serverfarms | TcpFinWait1 |  TCP Fin Wait 1  | 
| Microsoft.Web/serverfarms | TcpEstablished |  TCP Estabelecido  | 
| Microsoft.Web/serverfarms | TcpClosing |  Fecho da TCP  | 
| Microsoft.Web/serverfarms | TcpCloseWait |  Espera de fecho de TCP  | 
| Microsoft.Web/serverfarms | MemóriaPercentage |  Percentagem de Memória  | 
| Microsoft.Web/serverfarms | HttpQueueLength |  Comprimento da fila http  | 
| Microsoft.Web/serverfarms | DiskQueueLength |  Comprimento da fila do disco  | 
| Microsoft.Web/serverfarms | CpuPercentage |  Percentagem de CPU  | 
| Microsoft.Web/serverfarms | BytesSent |  Data out  | 
| Microsoft.Web/serverfarms | BytesReceived |  Dados em  | 
| Microsoft.Web/sites | TotalAppDomainsUnloaded |  Total de domínios de aplicações descarregados  | 
| Microsoft.Web/sites | Total DeDomínios |  Domínios totais de aplicativos  | 
| Microsoft.Web/sites | Fios |  Contagem de fios  | 
| Microsoft.Web/sites | PedidosInApplicationQueue |  Pedidos na fila de aplicações  | 
| Microsoft.Web/sites | Pedidos |  Pedidos  | 
| Microsoft.Web/sites | PrivateBytes |  Bytes privados  | 
| Microsoft.Web/sites | MemoryWorkingSet |  Conjunto de trabalho de memória  | 
| Microsoft.Web/sites | IoWriteOperationsPerSecond |  IO Escrever Operações por segundo  | 
| Microsoft.Web/sites | IoWriteBytesPerSecond |  IO Escrever Bytes por segundo  | 
| Microsoft.Web/sites | IoReadOperationsPerSecond |  IO Ler Operações por segundo  | 
| Microsoft.Web/sites | IoReadBytesPerSecond |  IO Ler Bytes por segundo  | 
| Microsoft.Web/sites | IoOtherOperationsPerSecond |  IO Outras Operações por Segundo  | 
| Microsoft.Web/sites | IoOtherBytesPerSecond |  IO Outros Bytes por Segundo  | 
| Microsoft.Web/sites | HttpResponseTime |  Tempo de resposta  | 
| Microsoft.Web/sites | Http5xx |  http Erros do servidor  | 
| Microsoft.Web/sites | Http4xx |  Http 4xx  | 
| Microsoft.Web/sites | Http406 |  Http 406  | 
| Microsoft.Web/sites | Http404 |  Http 404  | 
| Microsoft.Web/sites | Http403 |  Http 403  | 
| Microsoft.Web/sites | Http401 |  Http 401  | 
| Microsoft.Web/sites | Http3xx |  Http 3xx  | 
| Microsoft.Web/sites | Http2xx |  Http 2xx  | 
| Microsoft.Web/sites | Http101 |  Http 101  | 
| Microsoft.Web/sites | HealthCheckStatus |  Estado do controlo de saúde  | 
| Microsoft.Web/sites | Pegas |  N.º de Identificadores  | 
| Microsoft.Web/sites | Gen2Collections |  Coleções de lixo gen 2  | 
| Microsoft.Web/sites | Gen1Collections |  Coleções de lixo da Gen 1  | 
| Microsoft.Web/sites | Gen0Collections |  Coleções de lixo gen 0  | 
| Microsoft.Web/sites | FunExecutionUnnits |  Unidades de execução de funções  | 
| Microsoft.Web/sites | FunExecutionCount |  Contagem de execução de funções  | 
| Microsoft.Web/sites | Antiguidades Atuais |  Atuais Assembleias  | 
| Microsoft.Web/sites | Horário do Cpu |  Hora do CPU  | 
| Microsoft.Web/sites | BytesSent |  Data out  | 
| Microsoft.Web/sites | BytesReceived |  Dados em  | 
| Microsoft.Web/sites | AverageResponseTime |  Tempo médio de resposta  | 
| Microsoft.Web/sites | Média MemoryWorkingSet |  Conjunto de trabalho de memória média  | 
| Microsoft.Web/sites | AppConnections |  Ligações  | 
| Microsoft.Web/sites/slots | TotalAppDomainsUnloaded |  Total de domínios de aplicações descarregados  | 
| Microsoft.Web/sites/slots | Total DeDomínios |  Domínios totais de aplicativos  | 
| Microsoft.Web/sites/slots | Fios |  Contagem de fios  | 
| Microsoft.Web/sites/slots | PedidosInApplicationQueue |  Pedidos na fila de aplicações  | 
| Microsoft.Web/sites/slots | Pedidos |  Pedidos  | 
| Microsoft.Web/sites/slots | PrivateBytes |  Bytes privados  | 
| Microsoft.Web/sites/slots | MemoryWorkingSet |  Conjunto de trabalho de memória  | 
| Microsoft.Web/sites/slots | IoWriteOperationsPerSecond |  IO Escrever Operações por segundo  | 
| Microsoft.Web/sites/slots | IoWriteBytesPerSecond |  IO Escrever Bytes por segundo  | 
| Microsoft.Web/sites/slots | IoReadOperationsPerSecond |  IO Ler Operações por segundo  | 
| Microsoft.Web/sites/slots | IoReadBytesPerSecond |  IO Ler Bytes por segundo  | 
| Microsoft.Web/sites/slots | IoOtherOperationsPerSecond |  IO Outras Operações por Segundo  | 
| Microsoft.Web/sites/slots | IoOtherBytesPerSecond |  IO Outros Bytes por Segundo  | 
| Microsoft.Web/sites/slots | HttpResponseTime |  Tempo de resposta  | 
| Microsoft.Web/sites/slots | Http5xx |  http Erros do servidor  | 
| Microsoft.Web/sites/slots | Http4xx |  Http 4xx  | 
| Microsoft.Web/sites/slots | Http406 |  Http 406  | 
| Microsoft.Web/sites/slots | Http404 |  Http 404  | 
| Microsoft.Web/sites/slots | Http403 |  Http 403  | 
| Microsoft.Web/sites/slots | Http401 |  Http 401  | 
| Microsoft.Web/sites/slots | Http3xx |  Http 3xx  | 
| Microsoft.Web/sites/slots | Http2xx |  Http 2xx  | 
| Microsoft.Web/sites/slots | Http101 |  Http 101  | 
| Microsoft.Web/sites/slots | HealthCheckStatus |  Estado do controlo de saúde  | 
| Microsoft.Web/sites/slots | Pegas |  N.º de Identificadores  | 
| Microsoft.Web/sites/slots | Gen2Collections |  Coleções de lixo gen 2  | 
| Microsoft.Web/sites/slots | Gen1Collections |  Coleções de lixo da Gen 1  | 
| Microsoft.Web/sites/slots | Gen0Collections |  Coleções de lixo gen 0  | 
| Microsoft.Web/sites/slots | FunExecutionUnnits |  Unidades de execução de funções  | 
| Microsoft.Web/sites/slots | FunExecutionCount |  Contagem de execução de funções  | 
| Microsoft.Web/sites/slots | Antiguidades Atuais |  Atuais Assembleias  | 
| Microsoft.Web/sites/slots | Horário do Cpu |  Hora do CPU  | 
| Microsoft.Web/sites/slots | BytesSent |  Data out  | 
| Microsoft.Web/sites/slots | BytesReceived |  Dados em  | 
| Microsoft.Web/sites/slots | AverageResponseTime |  Tempo médio de resposta  | 
| Microsoft.Web/sites/slots | Média MemoryWorkingSet |  Conjunto de trabalho de memória média  | 
| Microsoft.Web/sites/slots | AppConnections |  Ligações  | 
| Microsoft.Sql/servidores/bases de dados | cpu_percent | Percentagem de CPU | 
| Microsoft.Sql/servidores/bases de dados | physical_data_read_percent | Percentagem de ES de Dados | 
| Microsoft.Sql/servidores/bases de dados | log_write_percent | Percentagem de IO de registo | 
| Microsoft.Sql/servidores/bases de dados | dtu_consumption_percent | Percentagem de DTU | 
| Microsoft.Sql/servidores/bases de dados | connection_successful | Conexões bem sucedidas | 
| Microsoft.Sql/servidores/bases de dados | connection_failed | Ligações com Falhas | 
| Microsoft.Sql/servidores/bases de dados | blocked_by_firewall | Bloqueado pela Firewall | 
| Microsoft.Sql/servidores/bases de dados | impasse | Impasses | 
| Microsoft.Sql/servidores/bases de dados | xtp_storage_percent | In-Memory por cento de armazenamento OLTP | 
| Microsoft.Sql/servidores/bases de dados | workers_percent | Percentagem de trabalhadores | 
| Microsoft.Sql/servidores/bases de dados | sessions_percent | Percentagem de sessões | 
| Microsoft.Sql/servidores/bases de dados | dtu_used | DTU usado | 
| Microsoft.Sql/servidores/bases de dados | cpu_used | CPU usado | 
| Microsoft.Sql/servidores/bases de dados | dwu_consumption_percent | Percentagem de DWU | 
| Microsoft.Sql/servidores/bases de dados | dwu_used | DWU usado | 
| Microsoft.Sql/servidores/bases de dados | cache_hit_percent | Percentagem de impacto de cache | 
| Microsoft.Sql/servidores/bases de dados | wlg_allocation_relative_to_system_percent | Alocação de grupo de carga de trabalho por parte do sistema | 
| Microsoft.Sql/servidores/bases de dados | wlg_allocation_relative_to_wlg_effective_cap_percent | Alocação de grupo de carga de trabalho por percentagem máxima de recursos | 
| Microsoft.Sql/servidores/bases de dados | wlg_active_queries | Consultas ativas do grupo de carga de trabalho | 
| Microsoft.Sql/servidores/bases de dados | wlg_queued_queries | Consultas em fila de grupos de carga | 
| Microsoft.Sql/servidores/bases de dados | active_queries | Consultas ativas | 
| Microsoft.Sql/servidores/bases de dados | queued_queries | Consultas em fila | 
| Microsoft.Sql/servidores/bases de dados | wlg_active_queries_timeouts | Tempo limites de consulta do grupo de carga de trabalho | 
| Microsoft.Sql/servidores/bases de dados | wlg_queued_queries_timeouts | Tempos de tempo de consulta do grupo de trabalho | 
| Microsoft.Sql/servidores/bases de dados | wlg_effective_min_resource_percent | Por cento de recursos min eficazes | 
| Microsoft.Sql/servidores/bases de dados | wlg_effective_cap_resource_percent | Por cento efetivo do recurso cap | 
| Microsoft.Sql/servidores/elásticos | cpu_percent | Percentagem de CPU | 
| Microsoft.Sql/servidores/elásticos | physical_data_read_percent | Percentagem de ES de Dados | 
| Microsoft.Sql/servidores/elásticos | log_write_percent | Percentagem de IO de registo | 
| Microsoft.Sql/servidores/elásticos | dtu_consumption_percent | Percentagem de DTU | 
| Microsoft.Sql/servidores/elásticos | workers_percent | Percentagem de trabalhadores | 
| Microsoft.Sql/servidores/elásticos | sessions_percent | Percentagem de sessões | 
| Microsoft.Sql/servidores/elásticos | eDTU_used | eDTU usado | 
| Microsoft.Sql/servidores/elásticos | xtp_storage_percent | In-Memory por cento de armazenamento OLTP | 
| Microsoft.Sql/servidores | dtu_consumption_percent | Percentagem de DTU | 
| Microsoft.Sql/servidores | dtu_used | DTU usado | 
| Microsoft.Sql/managedInstances | avg_cpu_percent | Percentagem média de CPU |