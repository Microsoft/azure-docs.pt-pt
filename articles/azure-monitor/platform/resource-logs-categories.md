---
title: Azure Monitor Registos de Recursos suportados serviços e categorias
description: Referência do Azure Monitor Compreenda os serviços suportados e esquema de eventos para registos de recursos Azure.
ms.subservice: logs
ms.topic: reference
ms.date: 06/03/2020
ms.openlocfilehash: 1fd6f07151c93b64c150f01e5c0b5c7f4cffed85
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593013"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Categorias suportadas para Registos de Recursos Azure

> [!NOTE]
> Os registos de recursos eram anteriormente conhecidos como registos de diagnóstico. O nome foi alterado em outubro de 2019, uma vez que os tipos de registos recolhidos pelo Azure Monitor mudaram para incluir mais do que apenas o recurso Azure.

[Os registos de recursos do Azure Monitor](./platform-logs-overview.md) são registos emitidos pelos serviços da Azure que descrevem o funcionamento desses serviços ou recursos. Todos os registos de recursos disponíveis através do Azure Monitor partilham um esquema comum de alto nível, com flexibilidade para cada serviço emitir propriedades únicas para os seus próprios eventos.

Uma combinação do tipo de recurso (disponível na `resourceId` propriedade) e o `category` esquema de identificação única. Existe um esquema comum para todos os registos de recursos com campos específicos de serviço e adicionados para diferentes categorias de registo. Para obter mais informações, consulte [esquemas específicos de common e serviço para registos de recursos Azure]()

## <a name="supported-log-categories-per-resource-type"></a>Categorias de registo suportado por tipo de recurso

Segue-se uma lista dos tipos de registos disponíveis para cada tipo de recurso. 

Algumas categorias só podem ser apoiadas para tipos específicos de recursos. Consulte a documentação específica do recurso se sentir que lhe falta um recurso. Por exemplo, as categorias Microsoft.Sql/servidores/bases de dados não estão disponíveis para todos os tipos de bases de dados. Para obter mais informações, consulte [informações sobre a sessão de registo de diagnóstico da Base de Dados SQL.](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) 

Se ainda falta alguma coisa, pode abrir um comentário do GitHub na parte inferior deste artigo.

## <a name="microsoftaadiamtenants"></a>microsoft.aadiam/inquilinos

|Categoria|Nome de exibição de categoria|
|---|---|
|Signin|Signin|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servidores

|Categoria|Nome de exibição de categoria|
|---|---|
|Motor|Motor|
|Serviço|Serviço|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Categoria|Nome de exibição de categoria|
|---|---|
|GatewayLogs|Registos relacionados com gateway de ApiManagement|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/primavera

|Categoria|Nome de exibição de categoria|
|---|---|
|AplicaçãoConsola|Consola de aplicação|
|SystemLogs|Registos do sistema|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automation

|Categoria|Nome de exibição de categoria|
|---|---|
|JobLogs|Registos de Emprego|
|JobStreams|Fluxos de Emprego|
|DscNodeStatus|Estado do nó Dsc|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batcontas ch/batch

|Categoria|Nome de exibição de categoria|
|---|---|
|ServiceLog|Registos de serviço|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/espaços de trabalho

|Categoria|Nome de exibição de categoria|
|---|---|
|BaiClusterEvent|BaiClusterEvent|
|BaiClusterNodeEvent|BaiClusterNodeEvent|
|BaiJobEvent|BaiJobEvent|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Categoria|Nome de exibição de categoria|
|---|---|
|BlockchainApplicação|Aplicação Blockchain|
|Proxy|Proxy|


## <a name="microsoftblockchaincordamembers"></a>Microsoft.Blockchain/cordaMembers

|Categoria|Nome de exibição de categoria|
|---|---|
|BlockchainApplicação|Aplicação Blockchain|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|Categoria|Nome de exibição de categoria|
|---|---|
|WebApplicationFirewallLogs|Registos de firewall de aplicação web|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/perfis

|Categoria|Nome de exibição de categoria|
|---|---|
|AzureCdnAccessLog|Log de acesso Azure Cdn|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft.Cdn/perfis/pontos finais

|Categoria|Nome de exibição de categoria|
|---|---|
|CoreAnalytics|Obtém as métricas do ponto final, por exemplo, largura de banda, saída, etc.|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft.ClassicNetwork/grupos de segurança de rede

|Categoria|Nome de exibição de categoria|
|---|---|
|Evento de fluxo de regras do grupo de segurança de rede|Evento de fluxo de regras do grupo de segurança de rede|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/contas

|Categoria|Nome de exibição de categoria|
|---|---|
|Auditoria|Registos de Auditoria|
|SolicitaçãoResponse|Registos de Pedidos e Respostas|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Categoria|Nome de exibição de categoria|
|---|---|
|ContainerRegistryLoginEvents|Eventos de Login|
|ContainerRegistryRepositoryEvents|RepositórioRs de registos|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Categoria|Nome de exibição de categoria|
|---|---|
|cluster-autoscaler|Kubernetes Cluster Autoscaler|
|kube-apiserver|Servidor API de Kubernetes|
|kube-auditoria|Auditoria de Kubernetes|
|kube-controlador-manager|Gestor do Controlador Kubernetes|
|kube-scheduler|Programador Kubernetes|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|Categoria|Nome de exibição de categoria|
|---|---|
|Auditorias|Registos de auditoria para chamadas miniRP|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft.Databricks/workspaces

|Categoria|Nome de exibição de categoria|
|---|---|
|accounts|Contas databricks|
|aglomerados|Databricks Clusters|
|dbfs|Sistema de Ficheiros do Databricks|
|instâncias|Piscinas de Exemplo|
|empregos|Databricks Jobs|
|bloco de notas|Databricks Notebook|
|segredos|Segredos databricks|
|sqlPermissions|Databricks SQLPermissions|
|ssh|Databricks SSH|
|área de trabalho|Databricks Workspace|


## <a name="microsoftdatacatalogdatacatalogs"></a>Microsoft.DataCatalog/datacatalogs

|Categoria|Nome de exibição de categoria|
|---|---|
|ScanStatusLogEvent|ScanStatus|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/fábricas

|Categoria|Nome de exibição de categoria|
|---|---|
|Corridas de Atividades|A atividade do gasoduto executa o log|
|PipelineRuns|Pipeline executa log|
|TriggerRuns|O gatilho executa o registo|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/contas

|Categoria|Nome de exibição de categoria|
|---|---|
|Auditoria|Registos de Auditoria|
|Pedidos|Solicitar Registos|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servidores

|Categoria|Nome de exibição de categoria|
|---|---|
|MySqlAuditLogs|Registos de Auditoria MariaDB|
|MySqlSlowLogs|Registos de servidores MariaDB|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servidores

|Categoria|Nome de exibição de categoria|
|---|---|
|MySqlAuditLogs|Registos de auditoria mySQL|
|MySqlSlowLogs|Registos de servidor mySQL|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servidores

|Categoria|Nome de exibição de categoria|
|---|---|
|PostgreSQLLogs|Registos de servidor pós-SQL|
|QueryStoreRuntimeStatistics|Estatísticas de tempo de runtime da loja de consultas pós-ql|
|QueryStoreWaitStatistics|Estatísticas de espera da loja de consultas postgresql|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Categoria|Nome de exibição de categoria|
|---|---|
|PostgreSQLLogs|Registos de servidor pós-SQL|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft.DBforPostgreSQL/singleservers

|Categoria|Nome de exibição de categoria|
|---|---|
|PostgreSQLLogs|Registos de servidor pós-SQL|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft.DesktopVirtualization/applicationgroups

|Categoria|Nome de exibição de categoria|
|---|---|
|Check Point|Check Point|
|Erro|Erro|
|Gestão|Gestão|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft.DesktopVirtualization/hostpools

|Categoria|Nome de exibição de categoria|
|---|---|
|Check Point|Check Point|
|Ligação|Ligação|
|Erro|Erro|
|HostRegistration|HostRegistration|
|Gestão|Gestão|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft.DesktopVirtualization/workspaces

|Categoria|Nome de exibição de categoria|
|---|---|
|Check Point|Check Point|
|Erro|Erro|
|Feed|Feed|
|Gestão|Gestão|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Categoria|Nome de exibição de categoria|
|---|---|
|C2DCommands|Comandos C2D|
|C2DTwinOperações|Operações Gémeas C2D|
|Configurações|Configurações|
|Ligações|Ligações|
|D2CTwinOperações|D2CTwinOperações|
|Operações DeviceIdentity|Operações de Identidade de Dispositivo|
|DispositivosStreams|Streams de dispositivos (pré-visualização)|
|DeviceTelemetry|Telemetria do Dispositivo|
|DirectMethods|Métodos Diretos|
|Tracagem distribuída|Rastreio distribuído (pré-visualização)|
|Operações de carga de ficheiros|Operações de upload de ficheiros|
|JobsOperations|Operações de Emprego|
|Rotas|Rotas|
|TwinQueries|Consultas Gémeas|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Categoria|Nome de exibição de categoria|
|---|---|
|Operações de Dispositivos|Operações de Dispositivos|
|Operações de Serviço|Operações de Serviço|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/base de dadosAcontas

|Categoria|Nome de exibição de categoria|
|---|---|
|CassandraRequests|CassandraRequests|
|ControlPlaneRequests|ControlPlaneRequests|
|DataPlaneRequests|DataPlaneRequests|
|MongoRequests|MongoRequests|
|PartiçãoKeyRUConsumption|PartiçãoKeyRUConsumption|
|PartitionKeyStatistics|PartitionKeyStatistics|
|QueryRuntimeStatistics|QueryRuntimeStatistics|


## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft.EnterpriseKnowledgeGraph/services

|Categoria|Nome de exibição de categoria|
|---|---|
|Evento de Auditoria|AuditEvent log|
|DataIssue|DataIssue log|
|Pedidos|Registo de configuração|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domínios

|Categoria|Nome de exibição de categoria|
|---|---|
|EntregasFailures|Registos de falha de entrega|
|PublicarFilures|Publicar Registos de Falhas|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Categoria|Nome de exibição de categoria|
|---|---|
|EntregasFailures|Registos de falha de entrega|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/tópicos

|Categoria|Nome de exibição de categoria|
|---|---|
|EntregasFailures|Registos de falha de entrega|
|PublicarFilures|Publicar Registos de Falhas|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/espaços de nome

|Categoria|Nome de exibição de categoria|
|---|---|
|ArchiveLogs|Registos de Arquivo|
|AutoScaleLogs|Registos de escala automática|
|CustomerManagedKeyUserLogs|Customer-Managed registos de chaves|
|EventHubVNetConnectionEvent|Registos de ligação de filtragem VNet/IP|
|KafkaCoordinatorLogs|Registos do Coordenador kafka|
|KafkaUserErrorLogs|Registos de erro do utilizador kafka|
|Logística|Registos Operacionais|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/serviços

|Categoria|Nome de exibição de categoria|
|---|---|
|Auditorias|Registos de auditoria|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Categoria|Nome de exibição de categoria|
|---|---|
|Reavaliações de autoescala|Avaliações de autoescala|
|AutoscaleActions|Ações de escala automática|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Componentes

|Categoria|Nome de exibição de categoria|
|---|---|
|AppDilabilityResults|Resultados da disponibilidade|
|AppBrowserTimings|Timings do navegador|
|AppDependencies|Dependências|
|Eventos AppEvents|Eventos|
|AppExceptions|Exceções|
|AppMetrics|Métricas|
|AppPageViews|Vistas da página|
|AppPerformanceCounters|Contadores de desempenho|
|Recorrentes|Pedidos|
|Eventos AppSystemEvents|Eventos do sistema|
|AppTraces|Rastreios|


## <a name="microsoftiotspacesgraph"></a>Microsoft.IoTSpaces/Gráfico

|Categoria|Nome de exibição de categoria|
|---|---|
|Auditoria|Auditoria|
|Saída|Saída|
|Entrada|Entrada|
|Operacional|Operacional|
|Rastreio|Rastreio|
|Utilização de UtilizadoresDefinedFunction|Utilização de UtilizadoresDefinedFunction|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/cofres

|Categoria|Nome de exibição de categoria|
|---|---|
|Evento de Auditoria|Registos de Auditoria|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Categoria|Nome de exibição de categoria|
|---|---|
|Ingestion Falhada|Operações de ingestão falhadas|
|Ingestion sucedeu|Operações de ingestão bem sucedidas|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft.Logic/integrationAcontas

|Categoria|Nome de exibição de categoria|
|---|---|
|IntegraçãoCocountTrackingEvents|Eventos de rastreio de conta de integração|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Categoria|Nome de exibição de categoria|
|---|---|
|WorkflowRuntime|Eventos de diagnóstico de tempo de trabalho|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Categoria|Nome de exibição de categoria|
|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|
|AmlComputeJobEvent|AmlComputeJobEvent|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Categoria|Nome de exibição de categoria|
|---|---|
|KeyDeliveryRequests|Pedidos de entrega chave|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Categoria|Nome de exibição de categoria|
|---|---|
|ApplicationGatewayAccessLog|Registo de acesso a gateway de aplicação|
|ApplicationGatewayFirewallLog|Registo de firewall gateway de aplicação|
|ApplicationGatewayPerformanceLog|Registo de desempenho do Gateway de Aplicação|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Categoria|Nome de exibição de categoria|
|---|---|
|AzureFirewallApplicationRule|Regra de aplicação da firewall Azure|
|AzureFirewallNetworkRule|Regra da rede de firewall Azure|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft.Network/bastionHosts

|Categoria|Nome de exibição de categoria|
|---|---|
|BastionAuditLogs|Registos de Auditoria de Bastião|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Categoria|Nome de exibição de categoria|
|---|---|
|PeeringRouteLog|Registos de tabela de rota de espreitar|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Categoria|Nome de exibição de categoria|
|---|---|
|FrontdoorAccessLog|Registo de acesso frontal|
|FrontdoorWebApplicationFirewallLog|Registo de firewall de aplicação web frontdoor|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Categoria|Nome de exibição de categoria|
|---|---|
|LoadBalancerAlertEvent|Eventos de alerta de balanceador de carga|
|LoadBalancerProbeHealthStatus|Estado de saúde da sonda de balanço de carga|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft.Network/networksecuritygroups

|Categoria|Nome de exibição de categoria|
|---|---|
|Evento networkSecurityGroupEvent|Evento do Grupo de Segurança de Rede|
|NetworkSecurityGroupRuleCounter|Contador de regras do grupo de segurança de rede|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Categoria|Nome de exibição de categoria|
|---|---|
|DDoSMitigationFlowLogs|Registos de fluxo de decisões de mitigação do DDoS|
|DDoSMitigationReports|Relatórios de mitigações do DDoS|
|DDoSProtectionNotificações|Notificações de proteção DDoS|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Categoria|Nome de exibição de categoria|
|---|---|
|ProbeHealthStatusEvents|Evento de resultados de saúde da sonda de tráfego|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Categoria|Nome de exibição de categoria|
|---|---|
|GatewayDiagnosticLog|Registos de diagnóstico gateway|
|IKEDiagnosticLog|Registos de diagnóstico IKE|
|P2SDiagnosticLog|Registos de diagnóstico P2S|
|RouteDiagnosticLog|Registos de diagnóstico de rotas|
|TúnelDiagnosticLog|Registos de diagnóstico do túnel|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Categoria|Nome de exibição de categoria|
|---|---|
|VMProtectionAlerts|Alertas de proteção VM|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Categoria|Nome de exibição de categoria|
|---|---|
|Motor|Motor|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

|Categoria|Nome de exibição de categoria|
|---|---|
|AddonAzureBackupAlerts|Dados de alerta de backup addon Azure|
|AddonAzureBackupJobs|Dados de trabalho de backup addon Azure|
|AddonAzureBackupPolicy|Dados da política de backup addon Azure|
|AddonAzureBackupProtectedInstance|Dados de instância protegidos de Addon Azure|
|AddonAzureBackupstorage|Dados de armazenamento de backup Addon Azure|
|AzureBackupReport|Dados de relatórios de backup da Azure|
|AzureSiteRecoveryEvents|Eventos de recuperação do local de Azure|
|AzureSiteRecoveryJobs|Empregos de recuperação do local de Azure|
|AzureSiteRecoveryProtectedDiskDataChurn|Recuperação do site Azure Dados protegidos do disco churn|
|AzureSiteRecoveryRecoveryPoints|Pontos de recuperação do local de Azure|
|AzureSiteRecoveryReplicatedItems|Itens replicados de recuperação do local de Azure|
|AzureSiteRecoveryReplicationDataUploadRate|Taxa de carregamento de dados de replicação do site Azure|
|AzureSiteRecoveryReplicationStats|Estatísticas de replicação da replicação do local de Azure|
|CoreAzureBackup|Dados de backup do Core Azure|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Categoria|Nome de exibição de categoria|
|---|---|
|HybridConnectionsEvent|Eventos HybridConnections|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Categoria|Nome de exibição de categoria|
|---|---|
|OperaçãoLogs|Registos de operação|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Categoria|Nome de exibição de categoria|
|---|---|
|Logística|Registos Operacionais|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalrService/Signalr

|Categoria|Nome de exibição de categoria|
|---|---|
|AllLogs|Registos de serviço Azure SignalR.|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Categoria|Nome de exibição de categoria|
|---|---|
|DevOpsOperationsAudit|Registos de auditoria de operações devops|
|ResourceUsageStats|Estatísticas de Utilização de Recursos|
|SQLSecurityAuditEvents|Evento de Auditoria de Segurança SQL|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.Sql/managedInstances/databases

|Categoria|Nome de exibição de categoria|
|---|---|
|Erros|Erros|
|QueryStoreRuntimeStatistics|Estatísticas de runtime da loja de consultas|
|QueryStoreWaitStatistics|Estatísticas de espera da loja de consultas|
|SQLInsights|SQL Insights|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servidores/bases de dados

|Categoria|Nome de exibição de categoria|
|---|---|
|Sintonização Automática|Ajuste automático|
|Blocos|Blocos|
|Base de dadosWaitStatistics|Estatísticas de espera de base de dados|
|Impasses|Impasses|
|DevOpsOperationsAudit|Registos de auditoria de operações devops|
|DmsWorkers|Trabalhadores da DMS|
|Erros|Erros|
|ExecRequests|Pedidos de Executivos|
|QueryStoreRuntimeStatistics|Estatísticas de runtime da loja de consultas|
|QueryStoreWaitStatistics|Estatísticas de espera da loja de consultas|
|PedidoSteps|Passos de Pedido|
|SQLInsights|SQL Insights|
|SqlRequests|Pedidos Sql|
|SQLSecurityAuditEvents|Evento de Auditoria de Segurança SQL|
|Tempos limite|Tempos limite|
|Espera|Espera|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAcontas/blobServices

|Categoria|Nome de exibição de categoria|
|---|---|
|StorageDelete|StorageDelete|
|ArmazenamentoRead|ArmazenamentoRead|
|ArmazenamentoPorite|ArmazenamentoPorite|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAcontas/fileServices

|Categoria|Nome de exibição de categoria|
|---|---|
|StorageDelete|StorageDelete|
|ArmazenamentoRead|ArmazenamentoRead|
|ArmazenamentoPorite|ArmazenamentoPorite|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAcontas/filas Serviços

|Categoria|Nome de exibição de categoria|
|---|---|
|StorageDelete|StorageDelete|
|ArmazenamentoRead|ArmazenamentoRead|
|ArmazenamentoPorite|ArmazenamentoPorite|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAcontas/tableServices

|Categoria|Nome de exibição de categoria|
|---|---|
|StorageDelete|StorageDelete|
|ArmazenamentoRead|ArmazenamentoRead|
|ArmazenamentoPorite|ArmazenamentoPorite|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Categoria|Nome de exibição de categoria|
|---|---|
|Criação|Criação|
|Execução|Execução|


## <a name="microsoftwebhostingenvironments"></a>microsoft.web/hostingenvironments

|Categoria|Nome de exibição de categoria|
|---|---|
|AppServiceEnvironmentPlatformLogs|Registos de plataformas de ambiente de serviço de aplicativos|


## <a name="microsoftwebsites"></a>microsoft.web/sites

|Categoria|Nome de exibição de categoria|
|---|---|
|AppServiceAppLogs|Registos de aplicações do serviço de aplicações de aplicações|
|AppServiceAuditLogs|Aceder a Registos de Auditoria|
|AppServiceConsoleLogs|Registos de consola de serviço de aplicativos|
|AppServiceFileAuditLogs|Registos de auditoria de alteração de conteúdo do site|
|AppServiceHTTPLogs|Registos HTTP|
|FunÇõesAppLogs|Registos de aplicação de funções|
|ScanLogs|Troncos de varredura antivírus|


## <a name="microsoftwebsitesslots"></a>microsoft.web/sites/slots


|Categoria|Nome de exibição de categoria|
|---|---|
|AppServiceAppLogs|Registos de aplicações do serviço de aplicações de aplicações|
|AppServiceAuditLogs|Aceder a Registos de Auditoria|
|AppServiceConsoleLogs|Registos de consola de serviço de aplicativos|
|AppServiceFileAuditLogs|Registos de auditoria de alteração de conteúdo do site|
|AppServiceHTTPLogs|Registos HTTP|
|FunÇõesAppLogs|Registos de aplicação de funções|
|ScanLogs|Troncos de varredura antivírus|


## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre registos de recursos](./platform-logs-overview.md)
* [Fluxo de registos de recursos para **Centros de Eventos**](./resource-logs.md#send-to-azure-event-hubs)
* [Alterar definições de diagnóstico de registo de recursos utilizando a API do Monitor Azure](/rest/api/monitor/diagnosticsettings)
* [Analise os registos do armazenamento do Azure com o Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

