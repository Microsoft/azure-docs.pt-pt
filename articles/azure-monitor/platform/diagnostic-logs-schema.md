---
title: Registos de diagnóstico do Azure suportada serviços e esquemas suportados
description: Compreenda o esquema de serviços e eventos suportado para registos de diagnóstico do Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/11/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 6e67b049ca179b1e93bcf645afd89b4a2eb0048d
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436507"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Serviços suportados, os esquemas e categorias para os registos de diagnóstico do Azure

[Registos de diagnóstico do Azure Monitor](../../azure-monitor/platform/diagnostic-logs-overview.md) registos emitidos pelos serviços do Azure que descrevem a operação desses serviços ou recursos. Todos os registos de diagnóstico disponíveis através do Azure Monitor partilham um esquema comum de nível superior, com flexibilidade para cada serviço emitir propriedades exclusivas para seus próprios eventos.

Uma combinação do tipo de recurso (disponível na `resourceId` propriedade) e o `category` identificar exclusivamente um esquema. Este artigo descreve o esquema de nível superior para os registos de diagnóstico e links para as esquemas para cada serviço.

## <a name="top-level-diagnostic-logs-schema"></a>Esquema de registos de diagnóstico de nível superior

| Name | Obrigatório/opcional | Descrição |
|---|---|---|
| hora | Necessário | O carimbo de hora (UTC) do evento. |
| resourceId | Necessário | O ID de recurso do recurso que o evento de emitidos. Para os serviços de inquilino, esta é a de /tenants/tenant-id/providers/provider-name o formulário. |
| tenantId | Necessário para os registos de inquilino | O ID de inquilino do que este evento está associado ao inquilino do Active Directory. Esta propriedade só é utilizada para os registos de nível de inquilino, não é apresentado nos registos ao nível do recurso. |
| operationName | Necessário | O nome da operação representada por este evento. Se o evento representa uma operação de RBAC, este é o nome da operação de RBAC (ex. Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Modelada normalmente na forma de uma operação do Gestor de recursos, mesmo que não sejam operações de Gestor de recursos de documentado real (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Opcional | A api-version associada à operação, se o operationName foi executada com uma API (por exemplo. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Se não houver nenhuma API que corresponde a esta operação, a versão representa a versão dessa operação, caso as propriedades associadas a operação mudar no futuro. |
| categoria | Necessário | A categoria de registo do evento. Categoria é a granularidade na qual pode ativar ou desativar os registos num determinado recurso. As propriedades que são apresentados no blob de propriedades de um evento são os mesmos dentro de um tipo de categoria e recursos de log específico. Categorias de registo típicos são "Auditoria" "operacional" "Execução" e "Pedido." |
| resultType | Opcional | O estado do evento. Valores típicos incluem iniciado, em curso, com êxito, falha, Active Directory e resolvido. |
| resultSignature | Opcional | O estado de sub-rotina do evento. Se esta operação corresponde a uma chamada à REST API, este é o código de estado HTTP da chamada REST correspondente. |
| resultDescription | Opcional | A descrição de texto estático desta operação, por exemplo. "Obter o ficheiro de armazenamento." |
| durationMs | Opcional | A duração da operação em milissegundos. |
| callerIpAddress | Opcional | O endereço IP de chamador, se a operação corresponde a uma chamada à API que virá numa entidade com um endereço IP disponível ao público. |
| correlationId | Opcional | Um GUID usado para agrupar um conjunto de eventos relacionados. Normalmente, se dois eventos tem o mesmo operationName, mas de dois Estados diferentes (por exemplo. "Introdução" e "Com êxito"), eles compartilham o mesmo ID de correlação. Isso também pode representar outras relações entre os eventos. |
| identidade | Opcional | Um blob JSON que descreve a identidade do utilizador ou aplicação que executou a operação. Normalmente, isto incluirá a autorização e afirmações / token JWT do Active Directory. |
| Nível | Opcional | O nível de gravidade do evento. Tem de ser informativo, aviso, erro ou crítico. |
| localização | Opcional | A região do recurso emite o evento, por exemplo. "Este dos E.U.A." ou "Sul de França" |
| propriedades | Opcional | Quaisquer propriedades relacionadas com esta categoria específica de eventos de estendidas. Todas as propriedades personalizadas/exclusivo tiver de ser colocadas dentro deste "B de parte" do esquema. |

## <a name="service-specific-schemas-for-resource-diagnostic-logs"></a>Esquemas de serviços específicos para os registos de diagnóstico de recursos
O esquema para os registos de diagnóstico de recursos varia consoante a categoria de recursos e de registo. Esta lista mostra todos os serviços que tornam os registos de diagnóstico disponíveis e links para o serviço e o esquema de categorias específicas que estiverem disponíveis.

| Serviço | Esquema e documentos |
| --- | --- |
| Azure Active Directory | [Descrição geral](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [esquema de registo de auditoria](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) e [esquema de inícios de sessão](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| Gestão de API | [Registos de diagnóstico de gestão de API](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Gateways de Aplicação |[Registo de diagnósticos para o Gateway de aplicação](../../application-gateway/application-gateway-diagnostics.md) |
| Automatização do Azure |[Análise de registos para a automatização do Azure](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Registo de diagnóstico do Azure Batch](../../batch/batch-diagnostics.md) |
| Base de Dados do Azure para MySQL | [Base de dados do Azure para MySQL registos de diagnóstico](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Base de Dados do Azure para PostgreSQL | [Base de dados do Azure para PostgreSQL registos de diagnóstico](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Serviços Cognitivos | Esquema não está disponível. |
| Rede de Entrega de Conteúdos | [Registos de diagnóstico do Azure CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Registo do Azure Cosmos DB](../../cosmos-db/logging.md) |
| Data Factory | [Monitorizar fábricas de dados através do Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Accessing diagnostics logs for Azure Data Lake Analytics (Aceder aos registos de diagnóstico do Azure Data Lake Analytics)](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Aceder a registos de diagnóstico para o Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Hubs de Eventos |[Registos de diagnóstico dos Hubs de eventos do Azure](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Esquema não está disponível. |
| Azure Firewall | Esquema não está disponível. |
| IoT Hub | [Operações do IoT Hub](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Cofre de Chaves |[Registo do Cofre de Chaves do Azure](../../key-vault/key-vault-logging.md) |
| Load balancer |[Log Analytics para o Balanceador de Carga do Azure](../../load-balancer/load-balancer-monitor-log.md) |
| Aplicações Lógicas |[Esquema de controlo personalizado B2B de Aplicações Lógicas](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Grupos de Segurança de Rede |[Análise de registos para grupos de segurança de rede (NSGs) (Log analytics for network security groups (NSGs))](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Proteção contra DDOS | [Gerir a norma de proteção contra DDoS do Azure](../../virtual-network/manage-ddos-protection.md) |
| Power BI dedicado | [Registo de diagnósticos para o Power BI Embedded no Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Serviços de Recuperação | [Modelo de dados para cópia de segurança do Azure](../../backup/backup-azure-reports-data-model.md)|
| Pesquisa |[Ativar e utilizar a análise de tráfego de pesquisa](../../search/search-traffic-analytics.md) |
| Service Bus |[Registos de diagnóstico do Azure Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Registo de diagnóstico de base de dados SQL do Azure](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Registos de diagnóstico de tarefa](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Gestor de Tráfego | [Esquema de registo do Gestor de tráfego](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Redes Virtuais | Esquema não está disponível. |
| Gateways de Rede Virtual | Esquema não está disponível. |

## <a name="supported-log-categories-per-resource-type"></a>Suportado categorias de registo por tipo de recurso
|Tipo de Recurso|Categoria|Nome de exibição de categoria|
|---|---|---|
|Microsoft.AnalysisServices/servers|Motor|Motor|
|Microsoft.AnalysisServices/servers|Serviço|Serviço|
|Microsoft.ApiManagement/service|GatewayLogs|Registos relacionados com o Gateway de ApiManagement|
|Microsoft.Automation/automationAccounts|JobLogs|Registos da tarefa|
|Microsoft.Automation/automationAccounts|JobStreams|Fluxos de trabalho|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Estado do nó DSC|
|Microsoft.Batch/batchAccounts|ServiceLog|Registos do serviço|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Obtém as métricas do ponto final, por exemplo, largura de banda, saída, etc.|
|Microsoft.ClassicNetwork/networksecuritygroups|Evento de Fluxo de Regra do Grupo de Segurança de Rede|Evento de Fluxo de Regra do Grupo de Segurança de Rede|
|Microsoft.CognitiveServices/accounts|Auditoria|Registos de Auditoria|
|Microsoft.CognitiveServices/accounts|RequestResponse|Registos de Pedidos e Respostas|
|Microsoft.ContainerService/managedClusters|kube apiserver|Servidor de API Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-controller-manager|Gestor do Controlador Kubernetes|
|Microsoft.ContainerService/managedClusters|cluster-autoscaler|Dimensionador Automático de Cluster Kubernetes|
|Microsoft.ContainerService/managedClusters|Agendador de kube|Programador Kubernetes|
|Microsoft.ContainerService/managedClusters|Guard|Webhook de Autenticação|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataFactory/factories|ActivityRuns|Registo de execuções de atividades do pipeline|
|Microsoft.DataFactory/factories|PipelineRuns|Registo de execuções do pipeline|
|Microsoft.DataFactory/factories|TriggerRuns|Registo de execuções do acionador|
|Microsoft.DataLakeAnalytics/accounts|Auditoria|Registos de Auditoria|
|Microsoft.DataLakeAnalytics/accounts|Pedidos|Registos de pedidos|
|Microsoft.DataLakeStore/accounts|Auditoria|Registos de Auditoria|
|Microsoft.DataLakeStore/accounts|Pedidos|Registos de pedidos|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|Registos do servidor MySQL|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|Registos do servidor PostgreSQL|
|Microsoft.Devices/IotHubs|Ligações|Ligações|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Telemetria do dispositivo|
|Microsoft.Devices/IotHubs|C2DCommands|Comandos de C2D|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Operações de identidade de dispositivo|
|Microsoft.Devices/IotHubs|FileUploadOperations|Operações de carregamento do ficheiro|
|Microsoft.Devices/IotHubs|Rotas|Rotas|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|Operações de duplo C2D|
|Microsoft.Devices/IotHubs|TwinQueries|Consultas de gémeos|
|Microsoft.Devices/IotHubs|JobsOperations|Operações de tarefas|
|Microsoft.Devices/IotHubs|DirectMethods|Métodos diretos|
|Microsoft.Devices/IotHubs|E2EDiagnostics|Diagnóstico de E2E (pré-visualização)|
|Microsoft.Devices/IotHubs|Configurações|Configurações|
|Microsoft.Devices/provisioningServices|DeviceOperations|Operações de dispositivo|
|Microsoft.Devices/provisioningServices|ServiceOperations|Operações de serviço|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.EventHub/namespaces|ArchiveLogs|Registos de arquivo|
|Microsoft.EventHub/namespaces|OperationalLogs|Registos operacionais|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Registos de dimensionamento automático|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|Avaliações de Dimensionamento Automático|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|Ações de Dimensionamento Automático|
|Microsoft.IoTSpaces/Graph|Rastreio|Rastreio|
|Microsoft.IoTSpaces/Graph|Operacional|Operacional|
|Microsoft.IoTSpaces/Graph|Auditoria|Auditoria|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|Entrada|Entrada|
|Microsoft.IoTSpaces/Graph|Saída|Saída|
|Microsoft.KeyVault/vaults|AuditEvent|Registos de Auditoria|
|Microsoft.Logic/workflows|WorkflowRuntime|Eventos de diagnóstico de runtime de fluxo de trabalho|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Conta de Integração para controlar eventos|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Evento do grupo de segurança de rede|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Contador de regra do grupo de segurança de rede|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Eventos de alerta do Balanceador de carga|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Estado de funcionamento de sonda de Balanceador de carga|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Notificações de proteção DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|Registos de decisões de mitigação de DDoS de fluxo|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|Relatórios de atenuações de DDoS|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Alertas de proteção de VM|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Registo de acesso do Gateway de aplicação|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Registo de desempenho do Gateway de aplicação|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Registo de Firewall do Gateway de aplicação|
|Microsoft.Network/securegateways|AzureFirewallApplicationRule|Regra de aplicação de Firewall do Azure|
|Microsoft.Network/securegateways|AzureFirewallNetworkRule|Regra de rede de Firewall do Azure|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Regra de aplicação de Firewall do Azure|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Regra de rede de Firewall do Azure|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Registos de diagnóstico do gateway|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Registos de diagnóstico de túnel|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Registos de diagnóstico de rota|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|Registos de diagnóstico do IKE|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|Registos de diagnóstico de P2S|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Evento de resultados do Estado de funcionamento de sonda de Gestor de tráfego|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|Registos de tabela de rota peering|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|Registo de acesso de Frontdoor|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|Registo de Firewall de aplicações Web de Frontdoor|
|Microsoft.PowerBIDedicated/capacities|Motor|Motor|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Relatório de dados do Azure Backup|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Tarefas de recuperação de Site do Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Eventos de recuperação de Site do Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Itens replicados do Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Estatísticas de replicação de recuperação de Site do Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Pontos de recuperação do Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Taxa de carregamento de dados de replicação de recuperação de Site do Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Recuperação de sites do Azure protegidos alterações a dados do disco|
|Microsoft.Search/searchServices|OperationLogs|Registos de Operações|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Registos operacionais|
|Microsoft.Sql/servers/databases|SQLInsights|Informações de SQL|
|Microsoft.Sql/servers/databases|AutomaticTuning|Ajuste automático|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Estatísticas de tempo de execução de consulta Store|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Estatísticas de espera de Store de consulta|
|Microsoft.Sql/servers/databases|Erros|Erros|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Estatísticas de espera da base de dados|
|Microsoft.Sql/servers/databases|Tempos limite|Tempos limite|
|Microsoft.Sql/servers/databases|blocos|blocos|
|Microsoft.Sql/servers/databases|Impasses|Impasses|
|Microsoft.Sql/servers/databases|Auditoria|Registos de Auditoria|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|Evento de auditoria de segurança SQL|
|Microsoft.Sql/servers/databases|DmsWorkers|Operadores de DMS|
|Microsoft.Sql/servers/databases|ExecRequests|Exec pedidos|
|Microsoft.Sql/servers/databases|RequestSteps|Passos de pedido|
|Microsoft.Sql/servers/databases|SqlRequests|Pedidos de SQL|
|Microsoft.Sql/servers/databases|Tem de aguardar|Tem de aguardar|
|Microsoft.Sql/managedInstances|ResourceUsageStats|Estatísticas de utilização de recursos|
|Microsoft.Sql/managedInstances|SQLSecurityAuditEvents|Evento de auditoria de segurança SQL|
|Microsoft.Sql/managedInstances/databases|SQLInsights|Informações de SQL|
|Microsoft.Sql/managedInstances/databases|QueryStoreRuntimeStatistics|Estatísticas de tempo de execução de consulta Store|
|Microsoft.Sql/managedInstances/databases|QueryStoreWaitStatistics|Estatísticas de espera de Store de consulta|
|Microsoft.Sql/managedInstances/databases|Erros|Erros|
|Microsoft.StreamAnalytics/streamingjobs|Execução|Execução|
|Microsoft.StreamAnalytics/streamingjobs|Criação de conteúdos|Criação de conteúdos|
|microsoft.web/sites|FunctionExecutionLogs|Registos de execução de função|
|microsoft.web/sites/slots|FunctionExecutionLogs|Registos de execução de função|

## <a name="next-steps"></a>Próximos Passos

* [Saiba mais sobre os registos de diagnóstico](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Stream registos de diagnóstico de recursos ao **dos Hubs de eventos**](../../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md)
* [Alterar definições de diagnóstico de recursos com a API de REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Analisar registos do armazenamento do Azure com o Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)

