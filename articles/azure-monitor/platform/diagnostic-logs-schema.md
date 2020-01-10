---
title: Serviços e esquemas com suporte dos logs de recursos do Azure
description: Entenda os serviços com suporte e o esquema de eventos para logs de recursos do Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
author: rboucher
ms.author: robb
ms.openlocfilehash: e744cdde298054de3631adb96b56bbc808f36a38
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750953"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Serviços, esquemas e categorias com suporte para logs de recursos do Azure

> [!NOTE]
> Os logs de recursos eram anteriormente conhecidos como logs de diagnóstico.

[Azure monitor logs de recursos](../../azure-monitor/platform/platform-logs-overview.md) são logs emitidos pelos serviços do Azure que descrevem a operação desses serviços ou recursos. Todos os logs de recursos disponíveis por meio de Azure Monitor compartilham um esquema comum de nível superior, com flexibilidade para cada serviço para emitir propriedades exclusivas para seus próprios eventos.

Uma combinação do tipo de recurso (disponível na propriedade `resourceId`) e o `category` identificar exclusivamente um esquema. Este artigo descreve o esquema de nível superior para logs de recursos e links para o Schemata para cada serviço.

## <a name="top-level-resource-logs-schema"></a>Esquema de logs de recursos de nível superior

| Nome | Obrigatório/Opcional | Descrição |
|---|---|---|
| hora | Obrigatório | O carimbo de data/hora (UTC) do evento. |
| resourceId | Obrigatório | A ID de recurso do recurso que emitiu o evento. Para serviços de locatário, esse é o formato/Tenants/Tenant-ID/Providers/Provider-Name. |
| tenantId | Necessário para logs de locatário | A ID de locatário do locatário de Active Directory ao qual esse evento está vinculado. Essa propriedade é usada somente para logs em nível de locatário, ela não aparece em logs de nível de recurso. |
| operationName | Obrigatório | O nome da operação representada por este evento. Se o evento representar uma operação RBAC, esse será o nome da operação RBAC (por exemplo, Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Normalmente modelados na forma de uma operação do Resource Manager, mesmo que não sejam operações reais documentadas do Resource Manager (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Opcional | A versão de API associada à operação, se a operationName tiver sido executada usando uma API (por exemplo, `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Se não houver nenhuma API que corresponda a essa operação, a versão representará a versão dessa operação, caso as propriedades associadas à operação sejam alteradas no futuro. |
| categoria | Obrigatório | A categoria de log do evento. Categoria é a granularidade na qual você pode habilitar ou desabilitar logs em um recurso específico. As propriedades que aparecem dentro do blob de propriedades de um evento são as mesmas dentro de uma categoria de log e tipo de recurso específicos. As categorias de log típicas são "auditoria" "operacional" "execução" e "solicitação". |
| resultType | Opcional | O status do evento. Os valores típicos incluem iniciado, em andamento, bem-sucedido, com falha, ativo e resolvido. |
| resultSignature | Opcional | O substatus do evento. Se essa operação corresponder a uma chamada à API REST, esse será o código de status HTTP da chamada REST correspondente. |
| resultDescription | Opcional | A descrição de texto estático desta operação, por exemplo, "Obter arquivo de armazenamento". |
| durationMs | Opcional | A duração da operação em milissegundos. |
| callerIpAddress | Opcional | O endereço IP do chamador, se a operação corresponder a uma chamada à API que venha de uma entidade com um endereço IP disponível publicamente. |
| correlationId | Opcional | Um GUID usado para agrupar um conjunto de eventos relacionados. Normalmente, se dois eventos tiverem a mesma operationName, mas dois status diferentes (por exemplo, "Iniciado" e "êxito"), eles compartilham a mesma ID de correlação. Isso também pode representar outras relações entre eventos. |
| identidade | Opcional | Um blob JSON que descreve a identidade do usuário ou aplicativo que realizou a operação. Normalmente, isso incluirá a autorização e o token de declarações/JWT do Active Directory. |
| Nível | Opcional | O nível de severidade do evento. Deve ser uma das informações, aviso, erro ou crítico. |
| localização | Opcional | A região do recurso que emite o evento, por exemplo, "Leste dos EUA" ou "sul da França" |
| propriedades | Opcional | Todas as propriedades estendidas relacionadas a esta determinada categoria de eventos. Todas as propriedades personalizadas/exclusivas devem ser colocadas dentro dessa "parte B" do esquema. |

## <a name="service-specific-schemas-for-resource-logs"></a>Esquemas específicos do serviço para logs de recursos
O esquema para logs de diagnóstico de recurso varia de acordo com a categoria de recurso e log. Esta lista mostra todos os serviços que disponibilizam logs de recursos e links para o serviço e o esquema específico de categoria, quando disponíveis.

| Serviço | Documentos de & de esquema |
| --- | --- |
| Azure Active Directory | [Visão geral](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), esquema de [log de auditoria](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) e [esquemas de entradas](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| Gestão de API | [Logs de recursos de gerenciamento de API](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Gateways da Aplicação |[Registro em log do gateway de aplicativo](../../application-gateway/application-gateway-diagnostics.md) |
| Automatização do Azure |[Log Analytics para automação do Azure](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Log do lote do Azure](../../batch/batch-diagnostics.md) |
| Base de Dados do Azure para MySQL | [Logs de diagnóstico do banco de dados do Azure para MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Base de Dados do Azure para PostgreSQL | [Logs do banco de dados do Azure para PostgreSQL](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Explorador de Dados do Azure | [Logs de Data Explorer do Azure](../../data-explorer/using-diagnostic-logs.md) |
| Serviços Cognitivos | [Registro em log para serviços cognitivas do Azure](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Registro em log do registro de contêiner do Azure](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Rede de Entrega de Conteúdos | [Logs do Azure para CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Log de Azure Cosmos DB](../../cosmos-db/logging.md) |
| Gestor de Dados | [Monitorar fábricas de dados usando Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Acessando logs para Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Arquivo do Data Lake |[Acessando logs para Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Logs de hubs de eventos do Azure](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express Route | Esquema não disponível. |
| Azure Firewall | Esquema não disponível. |
| Hub IoT | [Operações do Hub IoT](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Registo do Cofre de Chaves do Azure](../../key-vault/key-vault-logging.md) |
| Serviço Kubernetes |[Log de kubernetes do Azure](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Log Analytics para o Balanceador de Carga do Azure](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Esquema de controlo personalizado B2B de Aplicações Lógicas](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Grupos de Segurança de Rede |[Análise de registos para grupos de segurança de rede (NSGs) (Log analytics for network security groups (NSGs))](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Proteção contra DDOS | [Gerenciar a proteção contra DDoS do Azure Standard](../../virtual-network/manage-ddos-protection.md) |
| Power BI Dedicado | [Registro em log para Power BI Embedded no Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Serviços de Recuperação | [Modelo de dados para o backup do Azure](../../backup/backup-azure-reports-data-model.md)|
| Search |[Habilitando e usando o Análise de Tráfego de pesquisa](../../search/search-traffic-analytics.md) |
| Service Bus |[Logs do barramento de serviço do Azure](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Log do banco de dados SQL do Azure](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Logs de trabalho](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Gestor de Tráfego | [Esquema de log do Gerenciador de tráfego](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Redes Virtuais | Esquema não disponível. |
| Gateways de Rede Virtual | Esquema não disponível. |

## <a name="supported-log-categories-per-resource-type"></a>Categorias de log com suporte por tipo de recurso
|Tipo de Recurso|Categoria|Nome de exibição da categoria|
|---|---|---|
|Microsoft. AAD/DomainServices|SystemSecurity|SystemSecurity|
|Microsoft. AAD/DomainServices|AccountManagement|AccountManagement|
|Microsoft. AAD/DomainServices|LogonLogoff|LogonLogoff|
|Microsoft. AAD/DomainServices|Objectaccess|Objectaccess|
|Microsoft. AAD/DomainServices|PolicyChange|PolicyChange|
|Microsoft. AAD/DomainServices|PrivilegeUse|PrivilegeUse|
|Microsoft. AAD/DomainServices|DetailTracking|DetailTracking|
|Microsoft. AAD/DomainServices|DirectoryServiceAccess|DirectoryServiceAccess|
|Microsoft. AAD/DomainServices|AccountLogon|AccountLogon|
|Microsoft. aadiam/locatários|Entrada|Entrada|
|Microsoft.AnalysisServices/servers|Motor|Motor|
|Microsoft.AnalysisServices/servers|Serviço|Serviço|
|Microsoft.ApiManagement/service|GatewayLogs|Logs relacionados ao gateway ApiManagement|
|Microsoft. AppPlatform/Spring|ApplicationConsole|Console de aplicativo|
|Microsoft.Automation/automationAccounts|JobLogs|Logs de trabalho|
|Microsoft.Automation/automationAccounts|JobStreams|Fluxos de trabalho|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Status do nó DSC|
|Microsoft.Batch/batchAccounts|ServiceLog|Logs de serviço|
|Microsoft. BatchAI/Workspaces|BaiClusterEvent|BaiClusterEvent|
|Microsoft. BatchAI/Workspaces|BaiClusterNodeEvent|BaiClusterNodeEvent|
|Microsoft. BatchAI/Workspaces|BaiJobEvent|BaiJobEvent|
|Microsoft.Blockchain/blockchainMembers|BlockchainApplication|Aplicativo Blockchain|
|Microsoft.Blockchain/blockchainMembers|Proxy|Proxy|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Obtém as métricas do ponto de extremidade, por exemplo, largura de banda, saída etc.|
|Microsoft.ClassicNetwork/networksecuritygroups|Evento de fluxo de regra do grupo de segurança de rede|Evento de fluxo de regra do grupo de segurança de rede|
|Microsoft.CognitiveServices/accounts|Auditoria|Registos de Auditoria|
|Microsoft.CognitiveServices/accounts|RequestResponse|Logs de solicitação e resposta|
|Microsoft.ContainerRegistry/registries|ContainerRegistryRepositoryEvents|Logs do RepositoryEvent (visualização)|
|Microsoft.ContainerRegistry/registries|ContainerRegistryLoginEvents|Eventos de logon (versão prévia)|
|Microsoft.ContainerService/managedClusters|Kube-apiserver|Servidor de API kubernetes|
|Microsoft.ContainerService/managedClusters|Kube-Controller-Manager|Gerenciador do controlador kubernetes|
|Microsoft.ContainerService/managedClusters|Kube-Agendador|Agendador de kubernetes|
|Microsoft.ContainerService/managedClusters|Kube-auditoria|Kubernetes auditoria|
|Microsoft.ContainerService/managedClusters|cluster-autoescalar|Autoescalar do cluster kubernetes|
|Microsoft. databricks/espaços de trabalho|dBFS|Sistema de Ficheiros do Databricks|
|Microsoft. databricks/espaços de trabalho|clusters|Clusters do databricks|
|Microsoft. databricks/espaços de trabalho|accounts|Contas do databricks|
|Microsoft. databricks/espaços de trabalho|sejam|Tarefas do Databricks|
|Microsoft. databricks/espaços de trabalho|D430|Databricks Notebook|
|Microsoft. databricks/espaços de trabalho|SSH|SSH do databricks|
|Microsoft. databricks/espaços de trabalho|área de trabalho|Área de Trabalho do Databricks|
|Microsoft. databricks/espaços de trabalho|confidenciais|Segredos do databricks|
|Microsoft. databricks/espaços de trabalho|sqlpermissions|Sqlpermissões do databricks|
|Microsoft. databricks/espaços de trabalho|instancePools|Conjuntos de Instâncias|
|Microsoft. datacatalog/datacatalogs|ScanStatusLogEvent|ScanStatus|
|Microsoft.DataFactory/factories|ActivityRuns|Log de execuções de atividade de pipeline|
|Microsoft.DataFactory/factories|PipelineRuns|Log de execuções de pipeline|
|Microsoft.DataFactory/factories|TriggerRuns|Log de execuções de gatilho|
|Microsoft.DataLakeAnalytics/accounts|Auditoria|Registos de Auditoria|
|Microsoft.DataLakeAnalytics/accounts|Pedidos|Logs de solicitação|
|Microsoft.DataLakeStore/accounts|Auditoria|Registos de Auditoria|
|Microsoft.DataLakeStore/accounts|Pedidos|Logs de solicitação|
|Microsoft. DataShare/accounts|Partilhas|Partilhas|
|Microsoft. DataShare/accounts|ShareSubscriptions|Compartilhar assinaturas|
|Microsoft. DataShare/accounts|SentShareSnapshots|Instantâneos de compartilhamento enviados|
|Microsoft. DataShare/accounts|ReceivedShareSnapshots|Instantâneos de compartilhamento recebidos|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|Logs do servidor MySQL|
|Microsoft.DBforMySQL/servers|MySqlAuditLogs|Logs de auditoria do MySQL|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|Logs do servidor PostgreSQL|
|Microsoft.DBforPostgreSQL/servers|QueryStoreRuntimeStatistics|As estatísticas de tempo de execução Repositório de Consultas PostgreSQL|
|Microsoft.DBforPostgreSQL/servers|QueryStoreWaitStatistics|Estatísticas de espera Repositório de Consultas PostgreSQL|
|Microsoft.DBforPostgreSQL/serversv2|PostgreSQLLogs|Logs do servidor PostgreSQL|
|Microsoft.DBforPostgreSQL/serversv2|QueryStoreRuntimeStatistics|As estatísticas de tempo de execução Repositório de Consultas PostgreSQL|
|Microsoft.DBforPostgreSQL/serversv2|QueryStoreWaitStatistics|Estatísticas de espera Repositório de Consultas PostgreSQL|
|Microsoft. DesktopVirtualization/Workspaces|Checkpoint|Checkpoint|
|Microsoft. DesktopVirtualization/Workspaces|Erro|Erro|
|Microsoft. DesktopVirtualization/Workspaces|Gestão|Gestão|
|Microsoft. DesktopVirtualization/Workspaces|Feed|Feed|
|Microsoft. DesktopVirtualization/applicationGroups|Checkpoint|Checkpoint|
|Microsoft. DesktopVirtualization/applicationGroups|Erro|Erro|
|Microsoft. DesktopVirtualization/applicationGroups|Gestão|Gestão|
|Microsoft. DesktopVirtualization/hostPools|Checkpoint|Checkpoint|
|Microsoft. DesktopVirtualization/hostPools|Erro|Erro|
|Microsoft. DesktopVirtualization/hostPools|Gestão|Gestão|
|Microsoft. DesktopVirtualization/hostPools|Ligação|Ligação|
|Microsoft. DesktopVirtualization/hostPools|HostRegistration|HostRegistration|
|Microsoft.Devices/IotHubs|Ligações|Ligações|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Telemetria do Dispositivo|
|Microsoft.Devices/IotHubs|C2DCommands|Comandos C2D|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Operações de identidade do dispositivo|
|Microsoft.Devices/IotHubs|FileUploadOperations|Operações de upload de arquivo|
|Microsoft.Devices/IotHubs|Rotas|Rotas|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|Operações C2D|
|Microsoft.Devices/IotHubs|TwinQueries|Consultas de entrelaçamento|
|Microsoft.Devices/IotHubs|JobsOperations|Operações de trabalhos|
|Microsoft.Devices/IotHubs|DirectMethods|Métodos diretos|
|Microsoft.Devices/IotHubs|DistributedTracing|Rastreamento distribuído (visualização)|
|Microsoft.Devices/IotHubs|Configurações|Configurações|
|Microsoft.Devices/IotHubs|DeviceStreams|Fluxos de dispositivo (visualização)|
|Microsoft.Devices/provisioningServices|DeviceOperations|Operações do dispositivo|
|Microsoft.Devices/provisioningServices|ServiceOperations|Operações de serviço|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.DocumentDB/databaseAccounts|PartitionKeyStatistics|PartitionKeyStatistics|
|Microsoft.DocumentDB/databaseAccounts|ControlPlaneRequests|ControlPlaneRequests|
|Microsoft. EnterpriseKnowledgeGraph/serviços|AuditEvent|Log do AuditEvent|
|Microsoft. EnterpriseKnowledgeGraph/serviços|Emissão de problemas|Log de emissão de problemas|
|Microsoft. EnterpriseKnowledgeGraph/serviços|Pedidos|Log de configuração|
|Microsoft.EventHub/namespaces|ArchiveLogs|Logs de arquivo|
|Microsoft.EventHub/namespaces|OperationalLogs|Logs operacionais|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Logs de dimensionamento automático|
|Microsoft.EventHub/namespaces|KafkaCoordinatorLogs|Logs do coordenador de Kafka|
|Microsoft.EventHub/namespaces|KafkaUserErrorLogs|Logs de erros do usuário Kafka|
|Microsoft.EventHub/namespaces|EventHubVNetConnectionEvent|Logs de conexão de filtragem de VNet/IP|
|Microsoft.EventHub/namespaces|CustomerManagedKeyUserLogs|Logs de chave gerenciado pelo cliente|
|Microsoft. HealthcareApis/serviços|AuditLogs|Registos de auditoria|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|Avaliações de dimensionamento automático|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|Ações de escala de dimensionamento automático|
|Microsoft. IoTSpaces/Graph|Rastreio|Rastreio|
|Microsoft. IoTSpaces/Graph|Eficiência|Eficiência|
|Microsoft. IoTSpaces/Graph|Auditoria|Auditoria|
|Microsoft. IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft. IoTSpaces/Graph|Entrada|Entrada|
|Microsoft. IoTSpaces/Graph|Saída|Saída|
|Microsoft.KeyVault/vaults|AuditEvent|Registos de Auditoria|
|Microsoft. Kusto/clusters|SucceededIngestion|Operações de ingestão com êxito|
|Microsoft. Kusto/clusters|FailedIngestion|Operações de ingestão com falha|
|Microsoft.Logic/workflows|WorkflowRuntime|Eventos de diagnóstico do Workflow Runtime|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Eventos de acompanhamento da conta de integração|
|Microsoft.MachineLearningServices/workspaces|AmlComputeClusterEvent|AmlComputeClusterEvent|
|Microsoft.MachineLearningServices/workspaces|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|Microsoft.MachineLearningServices/workspaces|AmlComputeJobEvent|AmlComputeJobEvent|
|Microsoft. Media/mediaservices|KeyDeliveryRequests|Solicitações de entrega de chave|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Evento de grupo de segurança de rede|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Contador de regras do grupo de segurança de rede|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Evento de fluxo de regra do grupo de segurança de rede|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Notificações de proteção contra DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|Logs de fluxo de decisões de mitigação de DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|Relatórios de mitigações de DDoS|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Alertas de proteção da VM|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Log de acesso do gateway de aplicativo|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Log de desempenho do gateway de aplicativo|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Log do firewall do gateway de aplicativo|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Regra de aplicativo de firewall do Azure|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Regra de rede do firewall do Azure|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Logs de diagnóstico do gateway|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Logs de diagnóstico de túnel|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Rotear logs de diagnóstico|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|Logs de diagnóstico do IKE|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|Logs de diagnóstico do P2S|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Evento de resultados de integridade de investigação do Traffic Manager|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|Logs de tabela de rotas de emparelhamento|
|Microsoft. Network/vpnGateways|GatewayDiagnosticLog|Logs de diagnóstico do gateway|
|Microsoft. Network/vpnGateways|TunnelDiagnosticLog|Logs de diagnóstico de túnel|
|Microsoft. Network/vpnGateways|RouteDiagnosticLog|Rotear logs de diagnóstico|
|Microsoft. Network/vpnGateways|IKEDiagnosticLog|Logs de diagnóstico do IKE|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|Log de acesso do frontdoor|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|Log do firewall do aplicativo Web frontdoor|
|Microsoft. Network/p2sVpnGateways|GatewayDiagnosticLog|Logs de diagnóstico do gateway|
|Microsoft. Network/p2sVpnGateways|IKEDiagnosticLog|Logs de diagnóstico do IKE|
|Microsoft. Network/p2sVpnGateways|P2SDiagnosticLog|Logs de diagnóstico do P2S|
|Microsoft. Network/bastionHosts|BastionAuditLogs|Logs de auditoria de bastiões|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Load Balancer eventos de alerta|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Status de integridade da investigação de Load Balancer|
|Microsoft.PowerBIDedicated/capacities|Motor|Motor|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Dados de relatórios de backup do Azure|
|Microsoft.RecoveryServices/Vaults|CoreAzureBackup|Principais dados de backup do Azure|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupJobs|Dados do trabalho de backup do Azure addon|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupAlerts|Dados de alerta de backup do Azure addon|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupPolicy|Dados de política de backup do Azure addon|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupStorage|Dados de armazenamento de backup do Azure addon|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupProtectedInstance|Dados de instância protegida do backup do Azure addon|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Trabalhos de Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Eventos de Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Azure Site Recovery itens replicados|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Estatísticas de replicação Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Pontos de recuperação do Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Taxa de upload de dados de replicação Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery a rotatividade de dados de disco protegido|
|Microsoft.Search/searchServices|OperationLogs|Logs de operação|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Logs operacionais|
|Microsoft.Sql/servers/databases|SQLInsights|Insights do SQL|
|Microsoft.Sql/servers/databases|AutomaticTuning|Ajuste automático|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Estatísticas de tempo de execução Repositório de Consultas|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Estatísticas de espera Repositório de Consultas|
|Microsoft.Sql/servers/databases|Erros|Erros|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Estatísticas de espera do banco de dados|
|Microsoft.Sql/servers/databases|Tempos limite|Tempos limite|
|Microsoft.Sql/servers/databases|Blocos|Blocos|
|Microsoft.Sql/servers/databases|Deadlocks|Deadlocks|
|Microsoft.Sql/servers/databases|Auditoria|Registos de Auditoria|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|Evento de auditoria de segurança do SQL|
|Microsoft.Sql/servers/databases|DmsWorkers|Trabalhos DMS|
|Microsoft.Sql/servers/databases|ExecRequests|Solicitações de exec|
|Microsoft.Sql/servers/databases|RequestSteps|Etapas da solicitação|
|Microsoft.Sql/servers/databases|Requests|Solicitações SQL|
|Microsoft.Sql/servers/databases|Aguarda|Aguarda|
|Microsoft.Sql/managedInstances|ResourceUsageStats|Estatísticas de uso de recursos|
|Microsoft.Sql/managedInstances|SQLSecurityAuditEvents|Evento de auditoria de segurança do SQL|
|Microsoft.Sql/managedInstances/databases|SQLInsights|Insights do SQL|
|Microsoft.Sql/managedInstances/databases|QueryStoreRuntimeStatistics|Estatísticas de tempo de execução Repositório de Consultas|
|Microsoft.Sql/managedInstances/databases|QueryStoreWaitStatistics|Estatísticas de espera Repositório de Consultas|
|Microsoft.Sql/managedInstances/databases|Erros|Erros|
|Microsoft.Storage/storageAccounts/tableServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/tableServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/tableServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/blobServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/blobServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/blobServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/fileServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/fileServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/fileServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/queueServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/queueServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/queueServices|StorageDelete|StorageDelete|
|Microsoft.StreamAnalytics/streamingjobs|Execução|Execução|
|Microsoft.StreamAnalytics/streamingjobs|Criação|Criação|
|Microsoft. Web/hostingenvironments|AppServiceEnvironmentPlatformLogs|Ambiente do Serviço de Aplicativondo logs da plataforma|
|microsoft.web/sites|FunctionAppLogs|Logs do aplicativo de funções|
|microsoft.web/sites|AppServiceHTTPLogs|Logs de HTTP|
|microsoft.web/sites|AppServiceConsoleLogs|Logs do console do serviço de aplicativo|
|microsoft.web/sites|AppServiceAppLogs|Logs de aplicativo do serviço de aplicativo|
|microsoft.web/sites|AppServiceFileAuditLogs|Logs de auditoria de alteração de conteúdo do site|
|microsoft.web/sites|AppServiceAuditLogs|Acessar logs de auditoria|
|microsoft.web/sites/slots|FunctionAppLogs|Logs do aplicativo de funções|
|microsoft.web/sites/slots|AppServiceHTTPLogs|Logs de HTTP|
|microsoft.web/sites/slots|AppServiceConsoleLogs|Logs do console|
|microsoft.web/sites/slots|AppServiceAppLogs|Registos de Aplicações|
|microsoft.web/sites/slots|AppServiceFileAuditLogs|Logs de auditoria de alteração de conteúdo do site|
|microsoft.web/sites/slots|AppServiceAuditLogs|Acessar logs de auditoria|

## <a name="next-steps"></a>Próximos Passos

* [Saiba mais sobre os logs de recursos](../../azure-monitor/platform/platform-logs-overview.md)
* [Transmitir logs de recurso de recurso para **hubs de eventos**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Alterar as configurações de diagnóstico do log de recursos usando a API REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Analisar registos do armazenamento do Azure com o Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
