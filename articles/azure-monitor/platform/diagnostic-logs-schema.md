---
title: Os registos de recursos da Azure suportam serviços e schemas
description: Compreenda os serviços suportados e o esquema de eventos para os registos de recursos do Azure.
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
ms.openlocfilehash: 352310a6e489a96c38e85b16e9504d8eb9be38b1
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457251"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Serviços suportados, schemas e categorias para Registos de Recursos Azure

> [!NOTE]
> Os registos de recursos eram anteriormente conhecidos como registos de diagnóstico.

Os registos de [recursos do Azure Monitor](../../azure-monitor/platform/platform-logs-overview.md) são registos emitidos pelos serviços azure que descrevem o funcionamento desses serviços ou recursos. Todos os registos de recursos disponíveis através do Azure Monitor partilham um esquema comum de alto nível, com flexibilidade para cada serviço emitir propriedades únicas para os seus próprios eventos.

Uma combinação do tipo de `resourceId` recurso (disponível na propriedade) e o `category` único identificar um esquema. Este artigo descreve o esquema de alto nível para registos de recursos e ligações aos schemata schemata para cada serviço.

## <a name="top-level-resource-logs-schema"></a>Esquema de registos de recursos de alto nível

| Nome | Obrigatório/Opcional | Descrição |
|---|---|---|
| hora | Necessário | A marca de tempo (UTC) do evento. |
| resourceId | Necessário | A identificação de recursos do recurso que emitia o evento. Para os serviços de inquilinos, este é do formulário /inquilinos/inquilino-id/fornecedores/nome do fornecedor. |
| inquilinoId | Obrigatório para registos de inquilinos | A identificação do inquilino do Diretório Ativo a que este evento está ligado. Esta propriedade é usada apenas para registos ao nível do inquilino, não aparece em registos ao nível de recursos. |
| operationName | Necessário | O nome da operação representada por este evento. Se o evento representar uma operação RBAC, este é o nome de operação RBAC (por exemplo. Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Normalmente modelados sob a forma de uma operação do Gestor de`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`Recursos, mesmo que não sejam operações de Gestão de Recursos documentadas () |
| operationVersion | Opcional | A versão api associada à operação, se o nome da operação foi realizado com uma API (por exemplo. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Se não houver API que corresponda a esta operação, a versão representa a versão dessa operação no caso de as propriedades associadas à operação mudarem no futuro. |
| categoria | Necessário | A categoria de registo do evento. Categoria é a granularidade na qual pode ativar ou desativar os registos num determinado recurso. As propriedades que aparecem dentro das propriedades blob de um evento são as mesmas dentro de uma determinada categoria de log e tipo de recursos. As categorias típicas de registo são "Auditoria" "Operacional" "Execução" e "Pedido". |
| resultType | Opcional | O estado do evento. Os valores típicos incluem Iniciado, Em Progresso, Bem Sucedido, Falhado, Ativo e Resolvido. |
| resultSignature | Opcional | O sub-estatuto do evento. Se esta operação corresponder a uma chamada REST API, este é o código de estado HTTP da chamada REPOUSA correspondente. |
| resultDescription | Opcional | A descrição do texto estático desta operação, por exemplo. "Obter arquivo de armazenamento." |
| durationMs | Opcional | A duração da operação em milissegundos. |
| callerIpAddress | Opcional | O endereço IP do chamador, se a operação corresponder a uma chamada da API que provém de uma entidade com um endereço IP disponível ao público. |
| correlationId | Opcional | Um GUID costumava agrupar um conjunto de eventos relacionados. Normalmente, se dois eventos tiverem o mesmo nome de operação, mas dois estados diferentes (por exemplo. "Iniciado" e "bem sucedido"), partilham a mesma identificação de correlação. Isto também pode representar outras relações entre eventos. |
| identidade | Opcional | Uma bolha JSON que descreve a identidade do utilizador ou aplicação que realizou a operação. Normalmente isto incluirá a autorização e reclamações / símbolo JWT do diretório ativo. |
| Nível | Opcional | O nível de gravidade do evento. Deve ser de Informação, Aviso, Erro ou Crítico. |
| localização | Opcional | A região do recurso que emite o evento, por exemplo. "East US" ou "France South" |
| propriedades | Opcional | Quaisquer propriedades estendidas relacionadas com esta categoria particular de eventos. Todas as propriedades personalizadas/únicas devem ser colocadas dentro desta "Parte B" do esquema. |

## <a name="service-specific-schemas-for-resource-logs"></a>Esquemos específicos do serviço para registos de recursos
O esquema para os registos de diagnóstico de recursos varia consoante o recurso e a categoria de registo. Esta lista mostra todos os serviços que disponibilizam registos de recursos e ligações ao serviço e esquemaespecífico de categoria, sempre que disponível.

| Serviço | Schema & Docs |
| --- | --- |
| Azure Active Directory | [Visão geral](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), Esquema de [registo de auditoria](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) e esquema de [sign-ins](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| Gestão de API | [Registos de Recursos de Gestão API](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Gateways de Aplicação |[Login para Gateway de Aplicação](../../application-gateway/application-gateway-diagnostics.md) |
| Automatização do Azure |[Análise de log para automação azure](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Exploração madeireira de lote azure](../../batch/batch-diagnostics.md) |
| Base de Dados do Azure para MySQL | [Base de Dados Azure para registos de diagnóstico MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Base de Dados do Azure para PostgreSQL | [Base de Dados Azure para registos PostgreSQL](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Azure Data Explorer | [Registos do Explorador de Dados Azure](/azure/data-explorer/using-diagnostic-logs) |
| Serviços Cognitivos | [Exploração madeireira para serviços cognitivos azure](../../cognitive-services/diagnostic-logging.md) |
| Registo de Contentor | [Exploração madeireira para registo de contentores azure](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Rede de Entrega de Conteúdos | [Registos Azure para CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Extração de DB Azure Cosmos](../../cosmos-db/logging.md) |
| Data Factory | [Monitor fábricas de dados usando o Monitor Azure](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Acesso a registos para Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Acesso a registos para Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Hubs de Eventos |[Registos de Hubs de Eventos Azure](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Schema não está disponível. |
| Azure Firewall | Schema não está disponível. |
| IoT Hub | [Operações do Hub IoT](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Cofre de Chaves |[Exploração do cofre de chaves azure](../../key-vault/general/logging.md) |
| Serviço Kubernetes |[Exploração Madeireira Azure Kubernetes](../../aks/view-master-logs.md#log-event-schema) |
| Load balancer |[Análise de registos para o Balanceador de Carga do Azure](../../load-balancer/load-balancer-monitor-log.md) |
| Aplicações Lógicas |[Esquema de controlo personalizado B2B de Aplicações Lógicas](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Grupos de Segurança de Rede |[Análise de registos para grupos de segurança de rede (NSGs) (Log analytics for network security groups (NSGs))](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Proteção contra DDOS | [Gerir a Norma de Proteção DDoS Azure](../../virtual-network/manage-ddos-protection.md) |
| Power BI Dedicado | [Exploração madeireira para Power BI incorporada em Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Serviços de Recuperação | [Modelo de dados para backup azure](../../backup/backup-azure-reports-data-model.md)|
| Pesquisa |[Habilitar e utilizar a análise do tráfego de pesquisa](../../search/search-traffic-analytics.md) |
| Service Bus |[Registos de ônibus de serviço azure](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Exploração da Base de Dados Azure SQL](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Registos de trabalhos](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Gestor de Tráfego | [Esquema de log do gestor de tráfego](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Redes Virtuais | Schema não está disponível. |
| Gateways de Rede Virtual | Schema não está disponível. |

## <a name="supported-log-categories-per-resource-type"></a>Categorias de registo suportadas por tipo de recurso

Algumas categorias só podem ser apoiadas para tipos específicos de recursos. Esta é a lista de todos os que estão disponíveis de alguma forma.  Por exemplo, as categorias microsoft.Sql/servidores/bases de dados não estão disponíveis para todos os tipos de bases de dados. Para mais informações, consulte a informação sobre o registo de diagnóstico da Base de [Dados SQL](../../sql-database/sql-database-metrics-diag-logging.md). 

|Tipo de Recurso|Categoria|Nome de exibição de categoria|
|---|---|---|
|Microsoft.AAD/domainServices|Segurança do Sistema|Segurança do Sistema|
|Microsoft.AAD/domainServices|Gestão de Contas|Gestão de Contas|
|Microsoft.AAD/domainServices|LogonLogoff|LogonLogoff|
|Microsoft.AAD/domainServices|ObjectAccess|ObjectAccess|
|Microsoft.AAD/domainServices|Mudança de Política|Mudança de Política|
|Microsoft.AAD/domainServices|PrivilégioS|PrivilégioS|
|Microsoft.AAD/domainServices|Rastreio de Detalhes|Rastreio de Detalhes|
|Microsoft.AAD/domainServices|DirectórioServiceAccess|DirectórioServiceAccess|
|Microsoft.AAD/domainServices|AccountLogon|AccountLogon|
|microsoft.aadiam/inquilinos|Signin|Signin|
|Microsoft.AnalysisServices/servidores|Motor|Motor|
|Microsoft.AnalysisServices/servidores|Serviço|Serviço|
|Microsoft.ApiManagement/service|GatewayLogs|Registos relacionados com Gateway apiManagement|
|Microsoft.AppPlatform/Spring|ApplicationConsole|Consola de aplicação|
|Microsoft.Automation/automationAccounts|JobLogs|Registos de Emprego|
|Microsoft.Automation/automationAccounts|JobStreams|Fluxos de Emprego|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Estado do Nó Dsc|
|Microsoft.Batch/batchAccounts|ServiceLog|Registos de Serviço|
|Microsoft.BatchAI/espaços de trabalho|Evento BaiCluster|Evento BaiCluster|
|Microsoft.BatchAI/espaços de trabalho|BaiClusterNodeEvent|BaiClusterNodeEvent|
|Microsoft.BatchAI/espaços de trabalho|Evento BaiJob|Evento BaiJob|
|Microsoft.Blockchain/blockchainMembers|BlockchainApplication|Aplicação Blockchain|
|Microsoft.Blockchain/blockchainMembers|Proxy|Proxy|
|Microsoft.Cdn/perfis/pontos finais|CoreAnalytics|Obtém as métricas do ponto final, por exemplo, largura de banda, saída, etc.|
|Microsoft.ClassicNetwork/networksecuritygroups|Evento de fluxo de regras do grupo de segurança da rede|Evento de fluxo de regras do grupo de segurança da rede|
|Microsoft.CognitiveServices/accounts|Auditoria|Registos de Auditoria|
|Microsoft.CognitiveServices/accounts|Resposta de pedido|Registos de Pedido e Resposta|
|Microsoft.ContainerRegistry/registos|Eventos De Registo de ContentoresRepositórios|Registos repositóriosEvento (Pré-visualização)|
|Microsoft.ContainerRegistry/registos|Eventos De Registo de ContentoresLoginEvents|Eventos de Login (Pré-visualização)|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Servidor Kubernetes API|
|Microsoft.ContainerService/managedClusters|kube-controlador-manager|Gestor de Controladores Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-scheduler|Programador Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-auditoria|Auditoria Kubernetes|
|Microsoft.ContainerService/managedClusters|cluster-autoscaler|Kubernetes Cluster Autoscaler|
|Microsoft.Databricks/espaços de trabalho|dbfs|Sistema de Ficheiros do Databricks|
|Microsoft.Databricks/espaços de trabalho|aglomerados|Clusters de tijolos de dados|
|Microsoft.Databricks/espaços de trabalho|accounts|Contas databricks|
|Microsoft.Databricks/espaços de trabalho|empregos|Empregos de Databricks|
|Microsoft.Databricks/espaços de trabalho|caderno|Databricks Notebook|
|Microsoft.Databricks/espaços de trabalho|ssh|Databricks SSH|
|Microsoft.Databricks/espaços de trabalho|área de trabalho|Espaço de trabalho de Databricks|
|Microsoft.Databricks/espaços de trabalho|segredos|Segredos de Databricks|
|Microsoft.Databricks/espaços de trabalho|sqlPermissions|Databricks SQLPermissions|
|Microsoft.Databricks/espaços de trabalho|casosPools|Piscinas de Instância|
|Microsoft.DataCatalog/datacatalogs|Evento ScanStatusLogEvent|ScanStatus|
|Microsoft.DataFactory/fábricas|Corridas de Atividades|A atividade do gasoduto executa o log|
|Microsoft.DataFactory/fábricas|PipelineRuns|Pipeline executa log|
|Microsoft.DataFactory/fábricas|TriggerRuns|Gatilho executa log|
|Microsoft.DataLakeAnalytics/contas|Auditoria|Registos de Auditoria|
|Microsoft.DataLakeAnalytics/contas|Pedidos|Registos de Pedidos|
|Microsoft.DataLakeStore/contas|Auditoria|Registos de Auditoria|
|Microsoft.DataLakeStore/contas|Pedidos|Registos de Pedidos|
|Microsoft.DataShare/contas|Partilhas|Partilhas|
|Microsoft.DataShare/contas|Subscrições de Partilha|Subscrições de partilha|
|Microsoft.DataShare/contas|SentShareSnapshots|Snapshots de partilha enviados|
|Microsoft.DataShare/contas|ReceivedShareSnapshots|Fotos de partilha recebidas|
|Microsoft.DBforMySQL/servidores|MySqlSlowLogs|Registos do servidor MySQL|
|Microsoft.DBforMySQL/servidores|MySqlAuditLogs|Registos de Auditoria MySQL|
|Microsoft.DBforPostgreSQL/servidores|PostgreSQLLogs|Registos do servidor PostgreSQL|
|Microsoft.DBforPostgreSQL/servidores|ConsultasStoreRuntimeStatistics|Estatísticas de prazo de execução da loja de consulta postgresQL|
|Microsoft.DBforPostgreSQL/servidores|ConsultasStoreWaitStatistics|Estatísticas de espera da loja de consulta postgresQL|
|Microsoft.DBforPostgreSQL/serversv2|PostgreSQLLogs|Registos do servidor PostgreSQL|
|Microsoft.DBforPostgreSQL/serversv2|ConsultasStoreRuntimeStatistics|Estatísticas de prazo de execução da loja de consulta postgresQL|
|Microsoft.DBforPostgreSQL/serversv2|ConsultasStoreWaitStatistics|Estatísticas de espera da loja de consulta postgresQL|
|Microsoft.DesktopVirtualization/workspaces|Check Point|Check Point|
|Microsoft.DesktopVirtualization/workspaces|Erro|Erro|
|Microsoft.DesktopVirtualization/workspaces|Gestão|Gestão|
|Microsoft.DesktopVirtualization/workspaces|Feed|Feed|
|Microsoft.DesktopVirtualization/applicationGroups|Check Point|Check Point|
|Microsoft.DesktopVirtualization/applicationGroups|Erro|Erro|
|Microsoft.DesktopVirtualization/applicationGroups|Gestão|Gestão|
|Microsoft.DesktopVirtualization/hostPools|Check Point|Check Point|
|Microsoft.DesktopVirtualization/hostPools|Erro|Erro|
|Microsoft.DesktopVirtualization/hostPools|Gestão|Gestão|
|Microsoft.DesktopVirtualization/hostPools|Ligação|Ligação|
|Microsoft.DesktopVirtualization/hostPools|Registo de anfitriões|Registo de anfitriões|
|Microsoft.Devices/IotHubs|Ligações|Ligações|
|Microsoft.Devices/IotHubs|Telemetria|Telemetria do Dispositivo|
|Microsoft.Devices/IotHubs|C2DCommands|Comandos C2D|
|Microsoft.Devices/IotHubs|Operações de Identidade de Dispositivos|Operações de Identidade do Dispositivo|
|Microsoft.Devices/IotHubs|Operações de Upload de Ficheiros|Operações de upload de ficheiros|
|Microsoft.Devices/IotHubs|Rotas|Rotas|
|Microsoft.Devices/IotHubs|Operações D2CTwin|Operações D2CTwin|
|Microsoft.Devices/IotHubs|Operações C2DTwin|Operações Gémeas C2D|
|Microsoft.Devices/IotHubs|TwinQueries|Consultas Gémeas|
|Microsoft.Devices/IotHubs|Operações de Emprego|Operações de Emprego|
|Microsoft.Devices/IotHubs|Métodos Diretos|Métodos Diretos|
|Microsoft.Devices/IotHubs|DistributedTracing|Rastreio distribuído (Pré-visualização)|
|Microsoft.Devices/IotHubs|Configurações|Configurações|
|Microsoft.Devices/IotHubs|DeviceStreams|Fluxos de dispositivos (Pré-visualização)|
|Microsoft.Dispositivos/serviços de provisionamento|Operações de Dispositivos|Operações de Dispositivos|
|Microsoft.Dispositivos/serviços de provisionamento|Operações de Serviço|Operações de Serviço|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|Pedidos de Mongo|Pedidos de Mongo|
|Microsoft.DocumentDB/databaseAccounts|ConsultasEstatísticas de Tempo de Corrido|ConsultasEstatísticas de Tempo de Corrido|
|Microsoft.DocumentDB/databaseAccounts|Estatísticas de Divisóriakey|Estatísticas de Divisóriakey|
|Microsoft.DocumentDB/databaseAccounts|Pedidos de ControlPlane|Pedidos de ControlPlane|
|Microsoft.EnterpriseKnowledgeGraph/services|Evento de Auditoria|Registo de eventos de auditoria|
|Microsoft.EnterpriseKnowledgeGraph/services|DataIssue|Registo dataE|
|Microsoft.EnterpriseKnowledgeGraph/services|Pedidos|Registo de configuração|
|Microsoft.EventHub/espaços de nome|ArquivoS|Registos de Arquivo|
|Microsoft.EventHub/espaços de nome|Registos Operacionais|Registos Operacionais|
|Microsoft.EventHub/espaços de nome|AutoScaleLogs|Registos de escala automática|
|Microsoft.EventHub/espaços de nome|KafkaCoordenadorLogs|Registos coordenadores de Kafka|
|Microsoft.EventHub/espaços de nome|KafkaUserErrorLogs|Registos de erros do utilizador de Kafka|
|Microsoft.EventHub/espaços de nome|EventoHubVNetConnectionEvent|Registos de ligação de filtragem VNet/IP|
|Microsoft.EventHub/espaços de nome|CustomerManagedKeyUserLogs|Registos de chaves geridos pelo cliente|
|Microsoft.HealthcareApis/serviços|Registos de Auditoria|Registos de auditoria|
|Microsoft.Insights/AutoscaleSettings|Avaliações de escala automática|Avaliações de escala automática|
|Microsoft.Insights/AutoscaleSettings|Ações de Escala Automática|Ações de escala automática|
|Microsoft.IoTSpaces/Graph|Rastreio|Rastreio|
|Microsoft.IoTSpaces/Graph|Operacional|Operacional|
|Microsoft.IoTSpaces/Graph|Auditoria|Auditoria|
|Microsoft.IoTSpaces/Graph|Função Definida do Utilizador|Função Definida do Utilizador|
|Microsoft.IoTSpaces/Graph|Entrada|Entrada|
|Microsoft.IoTSpaces/Graph|Saída|Saída|
|Microsoft.KeyVault/cofres|Evento de Auditoria|Registos de Auditoria|
|Microsoft.Kusto/Clusters|SucessoIngestion|Operações de ingestão bem sucedidas|
|Microsoft.Kusto/Clusters|Ingestion falhado|Operações falhadas de ingerir|
|Microsoft.Logic/workflows|WorkflowTempo|Eventos de diagnóstico de tempo de funcionamento do workflow|
|Microsoft.Logic/integrationAccounts|IntegraçãoAccountTrackingEvents|Conta de Integração acompanha eventos|
|Microsoft.MachineLearningServices/espaços de trabalho|AmlComputeClusterEvent|AmlComputeClusterEvent|
|Microsoft.MachineLearningServices/espaços de trabalho|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|Microsoft.MachineLearningServices/espaços de trabalho|AmlComputeJobEvent|AmlComputeJobEvent|
|Microsoft.Media/mediaservices|Pedidos de entrega de chaves|Pedidos de entrega de chaves|
|Microsoft.Network/networksecuritygroups|Evento NetworkSecurityGroupEvent|Evento do Grupo de Segurança da Rede|
|Microsoft.Network/networksecuritygroups|Contador de regras de networksecurityGroup|Contador de regras do grupo de segurança da rede|
|Microsoft.Network/networksecuritygroups|Evento de Fluxos de NetworkSecurityGroup|Evento de fluxo de regras do grupo de segurança da rede|
|Microsoft.Network/publicIPAddresss|DDoSProtectionNotificas|Notificações de proteção DDoS|
|Microsoft.Network/publicIPAddresss|DDoSMitigationFlowLogs|Registos de fluxo de decisões de mitigação do DDoS|
|Microsoft.Network/publicIPAddresss|Relatórios de DdosMitigation|Relatórios de mitigações do DDoS|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Alertas de proteção VM|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Registo de acesso ao gateway da aplicação|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Registo de desempenho do gateway da aplicação|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Registo de firewall de gateway de aplicação|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Regra de aplicação da firewall azure|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Regra da rede de firewall azure|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Registos de Diagnóstico gateway|
|Microsoft.Network/virtualNetworkGateways|TúnelDiagnosticLog|Registos de diagnóstico do túnel|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Registos de Diagnóstico de Rota|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|Logs de Diagnóstico IKE|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticlog|Registos de Diagnóstico P2S|
|Microsoft.Network/trafficManagerProfiles|Eventos Saúde da Sonda|Evento de Resultados da Saúde do Gestor de Tráfego|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|Registos de tabelas de rotas de observação|
|Microsoft.Network/vpnGateways|GatewayDiagnosticLog|Registos de Diagnóstico gateway|
|Microsoft.Network/vpnGateways|TúnelDiagnosticLog|Registos de diagnóstico do túnel|
|Microsoft.Network/vpnGateways|RouteDiagnosticLog|Registos de Diagnóstico de Rota|
|Microsoft.Network/vpnGateways|IKEDiagnosticLog|Logs de Diagnóstico IKE|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|Log de acesso frontdoor|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|Log de firewall de aplicação web frontdoor|
|Microsoft.Network/p2sVpnGateways|GatewayDiagnosticLog|Registos de Diagnóstico gateway|
|Microsoft.Network/p2sVpnGateways|IKEDiagnosticLog|Logs de Diagnóstico IKE|
|Microsoft.Network/p2sVpnGateways|P2SDiagnosticlog|Registos de Diagnóstico P2S|
|Microsoft.Network/bastionHosts|BastionAuditLogs|Registos de Auditoria bastião|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Eventos de alerta de balanceadores de carga|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Estado de saúde da sonda do balanço de carga|
|Microsoft.PowerBIDedicated/capacities|Motor|Motor|
|Microsoft.RecoveryServices/Vaults|Relatório AzureBackup|Dados de Relatórios de Backup Azure|
|Microsoft.RecoveryServices/Vaults|CoreAzureBackup|Dados de backup core Azure|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupJobs|Dados do trabalho de backup addon Azure|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupAlerts|Dados de alerta de backup Addon Azure|
|Microsoft.RecoveryServices/Vaults|Política addonAzureBackup|Dados da política de backup Addon Azure|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupStorage|Dados de armazenamento de backup Addon Azure|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupProtectedInstance|Dados de instância protegidos de backup addon Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Empregos de recuperação de sítios de Azure|
|Microsoft.RecoveryServices/Vaults|Eventos azuresiterecovery|Eventos de recuperação do site azure|
|Microsoft.RecoveryServices/Vaults|Itens replicados azuresiteRecoveryreplicadItems|Itens replicados de recuperação do site Azure|
|Microsoft.RecoveryServices/Vaults|Estatísticas de Replicação de Recuperação de AzureSite|Estatísticas de replicação de recuperação do site azure|
|Microsoft.RecoveryServices/Vaults|Pontos de recuperação do AzureSiteRecoveryPoints|Pontos de recuperação do site Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Taxa de carregamento de dados de replicação do site Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Recuperação de site azure dados protegidos do disco churn|
|Microsoft.Search/searchServices|OperationLogs|Registos de Operação|
|Microsoft.ServiceBus/espaços de nome|Registos Operacionais|Registos Operacionais|
|Microsoft.Sql/servidores/bases de dados|SQLInsights|SQL Insights|
|Microsoft.Sql/servidores/bases de dados|Afinação Automática|Ajuste automático|
|Microsoft.Sql/servidores/bases de dados|ConsultasStoreRuntimeStatistics|Estatísticas de tempo de execução da loja de consulta|
|Microsoft.Sql/servidores/bases de dados|ConsultasStoreWaitStatistics|Estatísticas de espera da Loja de Consulta|
|Microsoft.Sql/servidores/bases de dados|Erros|Erros|
|Microsoft.Sql/servidores/bases de dados|Bases de DadosEstatísticas Wait|Estatísticas de espera de base de dados|
|Microsoft.Sql/servidores/bases de dados|Tempos limite|Tempos limite|
|Microsoft.Sql/servidores/bases de dados|Blocos|Blocos|
|Microsoft.Sql/servidores/bases de dados|Impasses|Impasses|
|Microsoft.Sql/servidores/bases de dados|Auditoria|Registos de Auditoria|
|Microsoft.Sql/servidores/bases de dados|Eventos de Auditoria sqlSecurity|Evento de Auditoria de Segurança SQL|
|Microsoft.Sql/servidores/bases de dados|DmsWorkers|Trabalhadores da DMS|
|Microsoft.Sql/servidores/bases de dados|ExecRequests|Pedidos executivos|
|Microsoft.Sql/servidores/bases de dados|RequestSteps|Passos de pedido|
|Microsoft.Sql/servidores/bases de dados|Pedidos de Sql|Pedidos Sql|
|Microsoft.Sql/servidores/bases de dados|Espera|Espera|
|Microsoft.Sql/managedInstances|Estatísticas de Utilização de Recursos|Estatísticas de Utilização de Recursos|
|Microsoft.Sql/managedInstances|Eventos de Auditoria sqlSecurity|Evento de Auditoria de Segurança SQL|
|Microsoft.Sql/managedInstances/bases de dados|SQLInsights|SQL Insights|
|Microsoft.Sql/managedInstances/bases de dados|ConsultasStoreRuntimeStatistics|Estatísticas de tempo de execução da loja de consulta|
|Microsoft.Sql/managedInstances/bases de dados|ConsultasStoreWaitStatistics|Estatísticas de espera da Loja de Consulta|
|Microsoft.Sql/managedInstances/bases de dados|Erros|Erros|
|Microsoft.Armazenamento/armazenamentoContas/tableServices|Leitura de armazenamento|Leitura de armazenamento|
|Microsoft.Armazenamento/armazenamentoContas/tableServices|StorageWrite|StorageWrite|
|Microsoft.Armazenamento/armazenamentoContas/tableServices|ArmazenamentoDelete|ArmazenamentoDelete|
|Microsoft.Armazenamento/armazenamentoContas/blobServices|Leitura de armazenamento|Leitura de armazenamento|
|Microsoft.Armazenamento/armazenamentoContas/blobServices|StorageWrite|StorageWrite|
|Microsoft.Armazenamento/armazenamentoContas/blobServices|ArmazenamentoDelete|ArmazenamentoDelete|
|Microsoft.Armazenamento/armazenamentoContas/ficheirosServiços|Leitura de armazenamento|Leitura de armazenamento|
|Microsoft.Armazenamento/armazenamentoContas/ficheirosServiços|StorageWrite|StorageWrite|
|Microsoft.Armazenamento/armazenamentoContas/ficheirosServiços|ArmazenamentoDelete|ArmazenamentoDelete|
|Microsoft.Armazenamento/armazenamentoContas/filaServiços|Leitura de armazenamento|Leitura de armazenamento|
|Microsoft.Armazenamento/armazenamentoContas/filaServiços|StorageWrite|StorageWrite|
|Microsoft.Armazenamento/armazenamentoContas/filaServiços|ArmazenamentoDelete|ArmazenamentoDelete|
|Microsoft.StreamAnalytics/streamingjobs|Execução|Execução|
|Microsoft.StreamAnalytics/streamingjobs|Criação|Criação|
|ambientes microsoft.web/hosting|AppServiceEnvironmentPlatformLogs|Registos da plataforma ambiente do serviço de aplicações|
|microsoft.web/sites|AppLogs de funções|Registos de aplicação de função|
|microsoft.web/sites|AppServiceHTTPLogs|Registos HTTP|
|microsoft.web/sites|AppServiceConsoleLogs|Registos de consolas de serviço de aplicativo|
|microsoft.web/sites|AppServiceAppLogs|Registos de aplicações de serviço de aplicações|
|microsoft.web/sites|AppServiceFileAuditLogs|Registos de auditoria de alteração de conteúdo do site|
|microsoft.web/sites|AppServiceAuditLogs|Acesso registos de auditoria|
|microsoft.web/sites/slots|AppLogs de funções|Registos de aplicação de função|
|microsoft.web/sites/slots|AppServiceHTTPLogs|Registos HTTP|
|microsoft.web/sites/slots|AppServiceConsoleLogs|Registos de Consolas|
|microsoft.web/sites/slots|AppServiceAppLogs|Registos de Aplicações|
|microsoft.web/sites/slots|AppServiceFileAuditLogs|Registos de auditoria de alteração de conteúdo do site|
|microsoft.web/sites/slots|AppServiceAuditLogs|Acesso registos de auditoria|

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre os registos de recursos](../../azure-monitor/platform/platform-logs-overview.md)
* [Fluxo de registos de recursos para Centros de **Eventos**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Alterar as definições de diagnóstico de registo de recursos utilizando a API REST do Monitor Azure](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Analise os registos do armazenamento do Azure com o Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
