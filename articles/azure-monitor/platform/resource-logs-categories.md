---
title: Azure Monitor Registos de Recursos suportados serviços e categorias
description: Referência do Azure Monitor Compreenda os serviços suportados e esquema de eventos para registos de recursos Azure.
ms.subservice: logs
ms.topic: reference
ms.date: 12/09/2020
ms.openlocfilehash: aeac069b4e9382867664a82af62e29e72da7585e
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232252"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Categorias suportadas para Registos de Recursos Azure

> [!NOTE]
> Os registos de recursos eram anteriormente conhecidos como registos de diagnóstico. O nome foi alterado em outubro de 2019, uma vez que os tipos de registos recolhidos pelo Azure Monitor mudaram para incluir mais do que apenas o recurso Azure.

[Os registos de recursos do Azure Monitor](./platform-logs-overview.md) são registos emitidos pelos serviços da Azure que descrevem o funcionamento desses serviços ou recursos. Todos os registos de recursos disponíveis através do Azure Monitor partilham um esquema comum de alto nível, com flexibilidade para cada serviço emitir propriedades únicas para os seus próprios eventos.

Uma combinação do tipo de recurso (disponível na `resourceId` propriedade) e o `category` esquema de identificação única. Existe um esquema comum para todos os registos de recursos com campos específicos de serviço e adicionados para diferentes categorias de registo. Para obter mais informações, consulte [esquemas comuns e específicos de serviço para registos de recursos Azure]()


## <a name="costs"></a>Custos

Existem custos associados ao envio e armazenamento de quaisquer dados no Log Analytics, Azure Storage e/ou Centro de Eventos. Você pode pagar o custo para obter os dados nestes locais e para mantê-los lá.  Os registos de recursos são um tipo de dados que pode enviar para estes locais. Existe um custo adicional [para a exportação de algumas categorias de registos de recursos](https://azure.microsoft.com/pricing/details/monitor/) para estes locais, enquanto outros estão isentos de custos de exportação. As especificidades dos custos de exportação constam do quadro a seguir.

## <a name="supported-log-categories-per-resource-type"></a>Categorias de registo suportado por tipo de recurso

Segue-se uma lista dos tipos de registos disponíveis para cada tipo de recurso. 

Algumas categorias só podem ser apoiadas para tipos específicos de recursos. Consulte a documentação específica do recurso se sentir que lhe falta um recurso. Por exemplo, as categorias Microsoft.Sql/servidores/bases de dados não estão disponíveis para todos os tipos de bases de dados. Para obter mais informações, consulte [informações sobre a sessão de registo de diagnóstico da Base de Dados SQL.](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) 

Se ainda falta alguma coisa, pode abrir um comentário do GitHub na parte inferior deste artigo.
## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servidores

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|Motor|Motor|
|Serviço|Serviço|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|GatewayLogs|Registos relacionados com gateway de ApiManagement|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/primavera

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|AplicaçãoConsola|Consola de aplicação|
|SystemLogs|Registos do sistema|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automation

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|DscNodeStatus|Estado do nó Dsc|
|JobLogs|Registos de Emprego|
|JobStreams|Fluxos de Emprego|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batcontas ch/batch

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|ServiceLog|Registos de serviço|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/espaços de trabalho

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|BaiClusterEvent|BaiClusterEvent|
|BaiClusterNodeEvent|BaiClusterNodeEvent|
|BaiJobEvent|BaiJobEvent|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|BlockchainApplicação|Aplicação Blockchain|
|FabricaÇão|Ordemdor de Tecidos|
|FabricPeer|Par de tecido|
|Proxy|Proxy|


## <a name="microsoftblockchaincordamembers"></a>Microsoft.Blockchain/cordaMembers

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|BlockchainApplicação|Aplicação Blockchain|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|WebApplicationFirewallLogs|Registos de firewall de aplicação web|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/perfis

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|AzureCdnAccessLog|Log de acesso Azure Cdn|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft.Cdn/perfis/pontos finais

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|CoreAnalytics|Obtém as métricas do ponto final, por exemplo, largura de banda, saída, etc.|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft.ClassicNetwork/grupos de segurança de rede

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|Evento de fluxo de regras do grupo de segurança de rede|Evento de fluxo de regras do grupo de segurança de rede|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/contas

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|Auditoria|Registos de Auditoria|
|SolicitaçãoResponse|Registos de Pedidos e Respostas|
|Rastreio|Registos de vestígios|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|ContainerRegistryLoginEvents|Eventos de Login|
|ContainerRegistryRepositoryEvents|RepositórioRs de registos|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|cluster-autoscaler|Kubernetes Cluster Autoscaler|
|kube-apiserver|Servidor API de Kubernetes|
|kube-auditoria|Auditoria de Kubernetes|
|kube-controlador-manager|Gestor do Controlador Kubernetes|
|kube-scheduler|Programador Kubernetes|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|Auditorias|Registos de auditoria para chamadas miniRP|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft.Databricks/workspaces

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
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


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/fábricas

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|Corridas de Atividades|A atividade do gasoduto executa o log|
|PipelineRuns|Pipeline executa log|
|TriggerRuns|O gatilho executa o registo|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/contas

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|Auditoria|Registos de Auditoria|
|Pedidos|Solicitar Registos|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/contas

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|RecebeuPartilhaSnapshots|Fotos de partilha recebidas|
|SentShareSnapshots|Instantâneos de partilha enviados|
|Partilhas|Partilhas|
|Subscrições de Partes|Assinaturas de partilha|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servidores

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|MySqlAuditLogs|Registos de Auditoria MariaDB|
|MySqlSlowLogs|Registos de servidores MariaDB|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|MySqlAuditLogs|Registos de auditoria mySQL|
|MySqlSlowLogs|Registos Lentos MySQL|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servidores

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|MySqlAuditLogs|Registos de auditoria mySQL|
|MySqlSlowLogs|Registos de servidor mySQL|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|PostgreSQLLogs|Registos de servidor pós-SQL|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servidores

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|PostgreSQLLogs|Registos de servidor pós-SQL|
|QueryStoreRuntimeStatistics|Estatísticas de tempo de runtime da loja de consultas pós-ql|
|QueryStoreWaitStatistics|Estatísticas de espera da loja de consultas postgresql|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|PostgreSQLLogs|Registos de servidor pós-SQL|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft.DesktopVirtualization/applicationgroups

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|Check Point|Check Point|
|Erro|Erro|
|Gestão|Gestão|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft.DesktopVirtualization/hostpools

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|Check Point|Check Point|
|Ligação|Ligação|
|Error|Error|
|HostRegistration|HostRegistration|
|Gestão|Gestão|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft.DesktopVirtualization/workspaces

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|Check Point|Check Point|
|Erro|Error|
|Feed|Feed|
|Gestão|Gestão|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
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

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|Operações de Dispositivos|Operações de Dispositivos|
|Operações de Serviço|Operações de Serviço|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/base de dadosAcontas

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|CassandraRequests|CassandraRequests|
|ControlPlaneRequests|ControlPlaneRequests|
|DataPlaneRequests|DataPlaneRequests|
|GremlinRequests|GremlinRequests|
|MongoRequests|MongoRequests|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|
|PartitionKeyStatistics|PartitionKeyStatistics|
|QueryRuntimeStatistics|QueryRuntimeStatistics|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domínios

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|EntregasFailures|Registos de falha de entrega|
|PublicarFilures|Publicar Registos de Falhas|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|EntregasFailures|Registos de falha de entrega|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/tópicos

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|EntregasFailures|Registos de falha de entrega|
|PublicarFilures|Publicar Registos de Falhas|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/espaços de nome

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|ArchiveLogs|Registos de Arquivo|
|AutoScaleLogs|Registos de escala automática|
|CustomerManagedKeyUserLogs|Registos de chaves geridos pelo cliente|
|EventHubVNetConnectionEvent|Registos de ligação de filtragem VNet/IP|
|KafkaCoordinatorLogs|Registos do Coordenador kafka|
|KafkaUserErrorLogs|Registos de erro do utilizador kafka|
|Logística|Registos Operacionais|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/serviços

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|Auditorias|Registos de auditoria|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|Reavaliações de autoescala|Avaliações de autoescala|
|AutoscaleActions|Ações de escala automática|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Componentes

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
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


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/cofres

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|AuditEvent|Registos de Auditoria|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|Comando|Comando|
|Ingestion Falhada|Operações de ingestão falhadas|
|IngestionBatching|Criação de batches de ingestão|
|Consulta|Consulta|
|Ingestion sucedeu|Operações de ingestão bem sucedidas|
|TableDetails|Detalhes da tabela|
|TableUsageStatistics|Estatísticas de utilização de quadros|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft.Logic/integrationAcontas

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|IntegraçãoCocountTrackingEvents|Eventos de rastreio de conta de integração|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|WorkflowRuntime|Eventos de diagnóstico de tempo de trabalho|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|
|AmlComputeJobEvent|AmlComputeJobEvent|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|KeyDeliveryRequests|Pedidos de entrega chave|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|ApplicationGatewayAccessLog|Registo de acesso a gateway de aplicação|
|ApplicationGatewayFirewallLog|Registo de firewall gateway de aplicação|
|ApplicationGatewayPerformanceLog|Registo de desempenho do Gateway de Aplicação|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|AzureFirewallApplicationRule|Regra de aplicação da firewall Azure|
|AzureFirewallNetworkRule|Regra da rede de firewall Azure|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft.Network/bastionHosts

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|BastionAuditLogs|Registos de Auditoria de Bastião|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|PeeringRouteLog|Registos de tabela de rota de espreitar|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|FrontdoorAccessLog|Registo de acesso frontal|
|FrontdoorWebApplicationFirewallLog|Registo de firewall de aplicação web frontdoor|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|LoadBalancerAlertEvent|Eventos de alerta de balanceador de carga|
|LoadBalancerProbeHealthStatus|Estado de saúde da sonda de balanço de carga|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft.Network/networksecuritygroups

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|Evento networkSecurityGroupEvent|Evento do Grupo de Segurança de Rede|
|NetworkSecurityGroupFlowEvent|Evento de fluxo de regras do grupo de segurança de rede|
|NetworkSecurityGroupRuleCounter|Contador de regras do grupo de segurança de rede|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|DDoSMitigationFlowLogs|Registos de fluxo de decisões de mitigação do DDoS|
|DDoSMitigationReports|Relatórios de mitigações do DDoS|
|DDoSProtectionNotificações|Notificações de proteção DDoS|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|ProbeHealthStatusEvents|Evento de resultados de saúde da sonda de tráfego|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|GatewayDiagnosticLog|Registos de diagnóstico gateway|
|IKEDiagnosticLog|Registos de diagnóstico IKE|
|P2SDiagnosticLog|Registos de diagnóstico P2S|
|RouteDiagnosticLog|Registos de diagnóstico de rotas|
|TúnelDiagnosticLog|Registos de diagnóstico do túnel|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|VMProtectionAlerts|Alertas de proteção VM|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|Motor|Motor|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
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

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|HybridConnectionsEvent|Eventos HybridConnections|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|OperaçãoLogs|Registos de operação|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|Logística|Registos Operacionais|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalrService/Signalr

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|AllLogs|Registos de serviço Azure SignalR.|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|DevOpsOperationsAudit|Registos de auditoria de operações devops|
|ResourceUsageStats|Estatísticas de Utilização de Recursos|
|SQLSecurityAuditEvents|Evento de Auditoria de Segurança SQL|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.Sql/managedInstances/databases

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|Erros|Erros|
|QueryStoreRuntimeStatistics|Estatísticas de runtime da loja de consultas|
|QueryStoreWaitStatistics|Estatísticas de espera da loja de consultas|
|SQLInsights|SQL Insights|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servidores/bases de dados

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
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

Custo para exportação: Pago como delineado na secção de Registos de Plataforma da [página de preços do Monitor Azure.](https://azure.microsoft.com/pricing/details/monitor/) 

|Categoria |Nome de exibição de categoria|
|---|---|
|StorageDelete|StorageDelete|
|ArmazenamentoRead|ArmazenamentoRead|
|ArmazenamentoPorite|ArmazenamentoPorite|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAcontas/fileServices

Custo para exportação: Pago como delineado na secção de Registos de Plataforma da [página de preços do Monitor Azure.](https://azure.microsoft.com/pricing/details/monitor/) 

|Categoria |Nome de exibição de categoria|
|---|---|
|StorageDelete|StorageDelete|
|ArmazenamentoRead|ArmazenamentoRead|
|ArmazenamentoPorite|ArmazenamentoPorite|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAcontas/filas Serviços

Custo para exportação: Pago como delineado na secção de Registos de Plataforma da [página de preços do Monitor Azure.](https://azure.microsoft.com/pricing/details/monitor/) 
 
|Categoria |Nome de exibição de categoria|
|---|---|
|StorageDelete|StorageDelete|
|ArmazenamentoRead|ArmazenamentoRead|
|ArmazenamentoPorite|ArmazenamentoPorite|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAcontas/tableServices

Custo para exportação: Pago como delineado na secção de Registos de Plataforma da [página de preços do Monitor Azure.](https://azure.microsoft.com/pricing/details/monitor/) 
 
|Categoria |Nome de exibição de categoria|
|---|---|
|StorageDelete|StorageDelete|
|ArmazenamentoRead|ArmazenamentoRead|
|ArmazenamentoPorite|ArmazenamentoPorite|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|Criação|Criação|
|Execução|Execução|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|BuiltinSqlReqsEnded|Pedidos de piscina de sql embutidos terminados|
|GatewayApiRequests|Pedidos de Api de Gateway De Sinaapse|
|SQLSecurityAuditEvents|Evento de Auditoria de Segurança SQL|
|Operações De SinapseRbac|Operações do RBAC de Sinapse|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|BigDataPoolAppsEnded|Aplicações de big data pool terminadas|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|DmsWorkers|Trabalhadores da DMS|
|ExecRequests|Pedidos de Executivos|
|PedidoSteps|Passos de Pedido|
|SqlRequests|Pedidos Sql|
|SQLSecurityAuditEvents|Evento de Auditoria de Segurança Sql|
|Espera|Espera|


## <a name="microsoftwebhostingenvironments"></a>microsoft.web/hostingenvironments

Custo para exportação: Grátis 

|Categoria |Nome de exibição de categoria|
|---|---|
|AppServiceEnvironmentPlatformLogs|Registos de plataformas de ambiente de serviço de aplicativos|


## <a name="microsoftwebsites"></a>microsoft.web/sites

Custo para exportação: Grátis 


|Categoria |Nome de exibição de categoria|
|---|---|
|AppServiceAppLogs|Registos de aplicações do serviço de aplicações de aplicações|
|AppServiceAuditLogs|Aceder a Registos de Auditoria|
|AppServiceConsoleLogs|Registos de consola de serviço de aplicativos|
|AppServiceFileAuditLogs|Registos de auditoria de alteração de conteúdo do site|
|AppServiceHTTPLogs|Registos HTTP|
|FunÇõesAppLogs|Registos de aplicação de funções|


## <a name="microsoftwebsitesslots"></a>microsoft.web/sites/slots

Custo para exportação: Grátis 


|Categoria |Nome de exibição de categoria|
|---|---|
|AppServiceAppLogs|Registos de aplicações do serviço de aplicações de aplicações|
|AppServiceAuditLogs|Aceder a Registos de Auditoria|
|AppServiceConsoleLogs|Registos de consola de serviço de aplicativos|
|AppServiceFileAuditLogs|Registos de auditoria de alteração de conteúdo do site|
|AppServiceHTTPLogs|Registos HTTP|
|FunÇõesAppLogs|Registos de aplicação de funções|


## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre registos de recursos](./platform-logs-overview.md)
* [Fluxo de registos de recursos para **Centros de Eventos**](./resource-logs.md#send-to-azure-event-hubs)
* [Alterar definições de diagnóstico de registo de recursos utilizando a API do Monitor Azure](/rest/api/monitor/diagnosticsettings)
* [Analise os registos do armazenamento do Azure com o Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

