---
title: Azure Monitor Registos de Recursos suportados serviços e categorias
description: Referência do Azure Monitor Compreenda os serviços suportados e esquema de eventos para registos de recursos Azure.
ms.subservice: logs
ms.topic: reference
ms.date: 01/29/2021
ms.openlocfilehash: 39ff78cd97682096fb284e137868c246dfdd7f14
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100616491"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Categorias suportadas para Registos de Recursos Azure

> [!NOTE]
> Os registos de recursos eram anteriormente conhecidos como registos de diagnóstico. O nome foi alterado em outubro de 2019, uma vez que os tipos de registos recolhidos pelo Azure Monitor mudaram para incluir mais do que apenas o recurso Azure.

[Os registos de recursos do Azure Monitor](../essentials/platform-logs-overview.md) são registos emitidos pelos serviços da Azure que descrevem o funcionamento desses serviços ou recursos. Todos os registos de recursos disponíveis através do Azure Monitor partilham um esquema comum de alto nível, com flexibilidade para cada serviço emitir propriedades únicas para os seus próprios eventos.

Uma combinação do tipo de recurso (disponível na `resourceId` propriedade) e o `category` esquema de identificação única. Existe um esquema comum para todos os registos de recursos com campos específicos de serviço e adicionados para diferentes categorias de registo. Para obter mais informações, consulte [esquemas comuns e específicos de serviço para registos de recursos Azure]()


## <a name="costs"></a>Custos

Existem custos associados ao envio e armazenamento de quaisquer dados no Log Analytics, Azure Storage e/ou Centro de Eventos. Você pode pagar o custo para obter os dados nestes locais e para mantê-los lá.  Os registos de recursos são um tipo de dados que pode enviar para estes locais. 

Há um custo adicional para exportar algumas categorias de registos de recursos para estes locais. Esses registos com custos de exportação estão listados na tabela abaixo. Para obter mais informações sobre estes preços, consulte a secção Registos de Plataforma na página de preços do [Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="supported-log-categories-per-resource-type"></a>Categorias de registo suportado por tipo de recurso

Segue-se uma lista dos tipos de registos disponíveis para cada tipo de recurso. 

Algumas categorias só podem ser apoiadas para tipos específicos de recursos. Consulte a documentação específica do recurso se sentir que lhe falta um recurso. Por exemplo, as categorias Microsoft.Sql/servidores/bases de dados não estão disponíveis para todos os tipos de bases de dados. Para obter mais informações, consulte [informações sobre a sessão de registo de diagnóstico da Base de Dados SQL.](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) 

Se acha que falta alguma coisa, pode abrir um comentário do GitHub na parte inferior deste artigo.
## <a name="microsoftaaddomainservices"></a>Microsoft.AAD/domainServices

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Logia de Conta|Logia de Conta|No|
|Gestão de Contas|Gestão de Contas|No|
|DetalheTracking|DetalheTracking|No|
|DirectórioServiceAccess|DirectórioServiceAccess|No|
|LogonLogoff|LogonLogoff|No|
|ObjectAccess|ObjectAccess|No|
|Troca de Políticas|Troca de Políticas|No|
|PrivilegeUse|PrivilegeUse|No|
|Segurança do Sistema|Segurança do Sistema|No|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servidores

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Motor|Motor|No|
|Serviço|Serviço|No|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|GatewayLogs|Registos relacionados com gateway de ApiManagement|No|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|HttpRequest|Pedidos HTTP|Yes|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/primavera

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AplicaçãoConsola|Consola de aplicação|No|
|SystemLogs|Registos do sistema|No|


## <a name="microsoftattestationattestationproviders"></a>Microsoft.Attestation/attestationProviders

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AuditEvent|AuditEvent categoria de registo de mensagens.|No|
|ERR|Categoria de registo de mensagem de erro.|No|
|INF|Categoria de registo de mensagens informacionais.|No|
|WRN|Categoria de registo de mensagem de aviso.|No|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automation

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|DscNodeStatus|Estado do nó Dsc|No|
|JobLogs|Registos de Emprego|No|
|JobStreams|Fluxos de Emprego|No|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batcontas ch/batch

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|ServiceLog|Registos de serviço|No|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/espaços de trabalho

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|BaiClusterEvent|BaiClusterEvent|No|
|BaiClusterNodeEvent|BaiClusterNodeEvent|No|
|BaiJobEvent|BaiJobEvent|No|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|BlockchainApplicação|Aplicação Blockchain|No|
|FabricaÇão|Ordemdor de Tecidos|No|
|FabricPeer|Par de tecido|No|
|Proxy|Proxy|No|


## <a name="microsoftblockchaincordamembers"></a>Microsoft.Blockchain/cordaMembers

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|BlockchainApplicação|Aplicação Blockchain|No|


## <a name="microsoftbotservicebotservices"></a>microsoft.botservice/botservices

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|BotRequest|Pedidos dos canais para o bot|No|
|Requestração de Dependência|Pedidos de dependências|No|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|WebApplicationFirewallLogs|Registos de firewall de aplicação web|No|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/perfis

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AzureCdnAccessLog|Log de acesso Azure Cdn|No|
|FrontDoorAccessLog|Registo de acesso frontdoor|Yes|
|FrontDoorHealthProbeLog|Registo da sonda de saúde frontdoor|Yes|
|FrontDoorWebApplicationFirewallLog|FrontDoor WebApplicationFirewall Log|Yes|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft.Cdn/perfis/pontos finais

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|CoreAnalytics|Obtém as métricas do ponto final, por exemplo, largura de banda, saída, etc.|No|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft.ClassicNetwork/grupos de segurança de rede

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Evento de fluxo de regras do grupo de segurança de rede|Evento de fluxo de regras do grupo de segurança de rede|No|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/contas

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Auditoria|Registos de Auditoria|No|
|SolicitaçãoResponse|Registos de Pedidos e Respostas|No|
|Rastreio|Registos de vestígios|No|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft.Communication/CommunicationServices

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|ChatOperational|Registos de chat operacionais|No|
|SMSOperational|Registos SMS operacionais|No|
|Utilização|Registos de Utilização|No|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|ContainerRegistryLoginEvents|Eventos de Login|No|
|ContainerRegistryRepositoryEvents|RepositórioRs de registos|No|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|cluster-autoscaler|Kubernetes Cluster Autoscaler|No|
|guarda|guarda|No|
|kube-apiserver|Servidor API de Kubernetes|No|
|kube-auditoria|Auditoria de Kubernetes|No|
|kube-audit-admin|Registos de Admin de Auditoria de Kubernetes|No|
|kube-controlador-manager|Gestor do Controlador Kubernetes|No|
|kube-scheduler|Programador Kubernetes|No|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Auditorias|Registos de auditoria para chamadas miniRP|No|


## <a name="microsoftd365customerinsightsinstances"></a>Microsoft.D365CustomerInsights/instâncias

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Auditoria|Eventos de auditoria|No|
|Operacional|Eventos operacionais|No|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft.Databricks/workspaces

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|accounts|Contas databricks|No|
|aglomerados|Databricks Clusters|No|
|dbfs|Sistema de Ficheiros do Databricks|No|
|instâncias|Piscinas de Exemplo|No|
|empregos|Databricks Jobs|No|
|bloco de notas|Databricks Notebook|No|
|segredos|Segredos databricks|No|
|sqlPermissions|Databricks SQLPermissions|No|
|ssh|Databricks SSH|No|
|área de trabalho|Databricks Workspace|No|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft.DataCollaboration/workspaces

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|ColaboraçãoAudit|Auditoria de Colaboração|Yes|
|DataAssets|Ativos de Dados|No|
|Pipelines|Pipelines|No|
|Propostas|Propostas|No|
|Scripts|Scripts|No|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/fábricas

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Corridas de Atividades|A atividade do gasoduto executa o log|No|
|PipelineRuns|Pipeline executa log|No|
|SSISIntegrationRuntimeLogs|Registos de tempo de execução de integração SSIS|No|
|SSISPackageEventMessageContext|SSIS pacote contexto de mensagem de evento|No|
|SSISPackageEventMes|Mensagens de evento pacote SSIS|No|
|SSISPackageExecutableStatistics|Estatísticas executáveis do pacote SSIS|No|
|SSISPackageExecutionComponentPhases|Fases componentes de execução de pacote SSIS|No|
|SSISPackageExecutionDataStatistics|Estatísticas de dados de exuções ao pacote SSIS|No|
|TriggerRuns|O gatilho executa o registo|No|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/contas

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Auditoria|Registos de Auditoria|No|
|Pedidos|Solicitar Registos|No|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/contas

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Auditoria|Registos de Auditoria|No|
|Pedidos|Solicitar Registos|No|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/contas

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|RecebeuPartilhaSnapshots|Fotos de partilha recebidas|No|
|SentShareSnapshots|Instantâneos de partilha enviados|No|
|Partilhas|Partilhas|No|
|Subscrições de Partes|Assinaturas de partilha|No|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servidores

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|MySqlAuditLogs|Registos de Auditoria MariaDB|No|
|MySqlSlowLogs|Registos de servidores MariaDB|No|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|MySqlAuditLogs|Registos de auditoria mySQL|No|
|MySqlSlowLogs|Registos Lentos MySQL|No|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servidores

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|MySqlAuditLogs|Registos de auditoria mySQL|No|
|MySqlSlowLogs|Registos de servidor mySQL|No|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|PostgreSQLLogs|Registos de servidor pós-SQL|No|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servidores

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|PostgreSQLLogs|Registos de servidor pós-SQL|No|
|QueryStoreRuntimeStatistics|Estatísticas de tempo de runtime da loja de consultas pós-ql|No|
|QueryStoreWaitStatistics|Estatísticas de espera da loja de consultas postgresql|No|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|PostgreSQLLogs|Registos de servidor pós-SQL|No|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft.DesktopVirtualization/applicationgroups

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Check Point|Check Point|No|
|Erro|Erro|No|
|Gestão|Gestão|No|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft.DesktopVirtualization/hostpools

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AgenteHealthStatus|AgenteHealthStatus|No|
|Check Point|Check Point|No|
|Ligação|Ligação|No|
|Erro|Erro|No|
|HostRegistration|HostRegistration|No|
|Gestão|Gestão|No|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft.DesktopVirtualization/workspaces

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Check Point|Check Point|No|
|Erro|Erro|No|
|Feed|Feed|No|
|Gestão|Gestão|No|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|C2DCommands|Comandos C2D|No|
|C2DTwinOperações|Operações Gémeas C2D|No|
|Configurações|Configurações|No|
|Ligações|Ligações|No|
|D2CTwinOperações|D2CTwinOperações|No|
|Operações DeviceIdentity|Operações de Identidade de Dispositivo|No|
|DispositivosStreams|Streams de dispositivos (pré-visualização)|No|
|DeviceTelemetry|Telemetria do Dispositivo|No|
|DirectMethods|Métodos Diretos|No|
|Tracagem distribuída|Rastreio distribuído (pré-visualização)|No|
|Operações de carga de ficheiros|Operações de upload de ficheiros|No|
|JobsOperations|Operações de Emprego|No|
|Rotas|Rotas|No|
|TwinQueries|Consultas Gémeas|No|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|C2DCommands|Comandos C2D|No|
|C2DTwinOperações|Operações Gémeas C2D|No|
|Configurações|Configurações|No|
|Ligações|Ligações|No|
|D2CTwinOperações|D2CTwinOperações|No|
|Operações DeviceIdentity|Operações de Identidade de Dispositivo|No|
|DispositivosStreams|Streams de dispositivos (pré-visualização)|No|
|DeviceTelemetry|Telemetria do Dispositivo|No|
|DirectMethods|Métodos Diretos|No|
|Tracagem distribuída|Rastreio distribuído (pré-visualização)|No|
|Operações de carga de ficheiros|Operações de upload de ficheiros|No|
|JobsOperations|Operações de Emprego|No|
|Rotas|Rotas|No|
|TwinQueries|Consultas Gémeas|No|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Operações de Dispositivos|Operações de Dispositivos|No|
|Operações de Serviço|Operações de Serviço|No|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft.DigitalTwins/digitalTwinsInstances

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|DigitalTwinsOperação|DigitalTwinsOperação|No|
|EventRoutesOperação|EventRoutesOperação|No|
|ModelosOperação|ModelosOperação|No|
|Consulta|Consulta|No|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/base de dadosAcontas

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|CassandraRequests|CassandraRequests|No|
|ControlPlaneRequests|ControlPlaneRequests|No|
|DataPlaneRequests|DataPlaneRequests|No|
|GremlinRequests|GremlinRequests|No|
|MongoRequests|MongoRequests|No|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|No|
|PartitionKeyStatistics|PartitionKeyStatistics|No|
|QueryRuntimeStatistics|QueryRuntimeStatistics|No|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domínios

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|EntregasFailures|Registos de falha de entrega|No|
|PublicarFilures|Publicar Registos de Falhas|No|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft.EventGrid/partnerNamespaces

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|EntregasFailures|Registos de falha de entrega|No|
|PublicarFilures|Publicar Registos de Falhas|No|


## <a name="microsofteventgridpartnertopics"></a>Microsoft.EventGrid/partnerTopics

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|EntregasFailures|Registos de falha de entrega|No|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|EntregasFailures|Registos de falha de entrega|No|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/tópicos

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|EntregasFailures|Registos de falha de entrega|No|
|PublicarFilures|Publicar Registos de Falhas|No|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/espaços de nome

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|ArchiveLogs|Registos de Arquivo|No|
|AutoScaleLogs|Registos de escala automática|No|
|CustomerManagedKeyUserLogs|Registos de chaves geridos pelo cliente|No|
|EventHubVNetConnectionEvent|Registos de ligação de filtragem VNet/IP|No|
|KafkaCoordinatorLogs|Registos do Coordenador kafka|No|
|KafkaUserErrorLogs|Registos de erro do utilizador kafka|No|
|Logística|Registos Operacionais|No|


## <a name="microsoftexperimentationexperimentworkspaces"></a>microsoft.experimentation/experimentWorkspaces

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Pedir|Pedir|No|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/serviços

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Auditorias|Registos de auditoria|No|


## <a name="microsoftinsightsautoscalesettings"></a>microsoft.insights/autoescalações

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Reavaliações de autoescala|Avaliações de autoescala|No|
|AutoscaleActions|Ações de escala automática|No|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Componentes

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AppDilabilityResults|Resultados da disponibilidade|No|
|AppBrowserTimings|Timings do navegador|No|
|AppDependencies|Dependências|No|
|Eventos AppEvents|Eventos|No|
|AppExceptions|Exceções|No|
|AppMetrics|Métricas|No|
|AppPageViews|Vistas da página|No|
|AppPerformanceCounters|Contadores de desempenho|No|
|Recorrentes|Pedidos|No|
|Eventos AppSystemEvents|Eventos do sistema|No|
|AppTraces|Rastreios|No|


## <a name="microsoftiotspacesgraph"></a>Microsoft.IoTSpaces/Gráfico

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Auditoria|Auditoria|No|
|Saída|Saída|No|
|Entrada|Entrada|No|
|Operacional|Operacional|No|
|Rastreio|Rastreio|No|
|Utilização de UtilizadoresDefinedFunction|Utilização de UtilizadoresDefinedFunction|No|


## <a name="microsoftkeyvaultmanagedhsms"></a>microsoft.keyvault/managedhsms

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AuditEvent|Evento de Auditoria|No|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/cofres

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AuditEvent|Registos de Auditoria|No|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Comando|Comando|No|
|Ingestion Falhada|Operações de ingestão falhadas|No|
|IngestionBatching|Criação de batches de ingestão|No|
|Consulta|Consulta|No|
|Ingestion sucedeu|Operações de ingestão bem sucedidas|No|
|TableDetails|Detalhes da tabela|No|
|TableUsageStatistics|Estatísticas de utilização de quadros|No|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft.Logic/integrationAcontas

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|IntegraçãoCocountTrackingEvents|Eventos de rastreio de conta de integração|No|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|WorkflowRuntime|Eventos de diagnóstico de tempo de trabalho|No|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|No|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|No|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|No|
|AmlComputeJobEvent|AmlComputeJobEvent|No|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|No|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|KeyDeliveryRequests|Pedidos de entrega chave|No|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|ApplicationGatewayAccessLog|Registo de acesso a gateway de aplicação|No|
|ApplicationGatewayFirewallLog|Registo de firewall gateway de aplicação|No|
|ApplicationGatewayPerformanceLog|Registo de desempenho do Gateway de Aplicação|No|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AzureFirewallApplicationRule|Regra de aplicação da firewall Azure|No|
|AzureFirewallDnsProxy|Azure Firewall DNS Proxy|No|
|AzureFirewallNetworkRule|Regra da rede de firewall Azure|No|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft.Network/bastionHosts

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|BastionAuditLogs|Registos de Auditoria de Bastião|No|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|PeeringRouteLog|Registos de tabela de rota de espreitar|No|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|FrontdoorAccessLog|Registo de acesso frontal|No|
|FrontdoorWebApplicationFirewallLog|Registo de firewall de aplicação web frontdoor|No|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|LoadBalancerAlertEvent|Eventos de alerta de balanceador de carga|No|
|LoadBalancerProbeHealthStatus|Estado de saúde da sonda de balanço de carga|No|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft.Network/networksecuritygroups

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Evento networkSecurityGroupEvent|Evento do Grupo de Segurança de Rede|No|
|NetworkSecurityGroupFlowEvent|Evento de fluxo de regras do grupo de segurança de rede|No|
|NetworkSecurityGroupRuleCounter|Contador de regras do grupo de segurança de rede|No|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft.Network/p2sVpnGateways

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|GatewayDiagnosticLog|Registos de diagnóstico gateway|No|
|IKEDiagnosticLog|Registos de diagnóstico IKE|No|
|P2SDiagnosticLog|Registos de diagnóstico P2S|No|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|DDoSMitigationFlowLogs|Registos de fluxo de decisões de mitigação do DDoS|No|
|DDoSMitigationReports|Relatórios de mitigações do DDoS|No|
|DDoSProtectionNotificações|Notificações de proteção DDoS|No|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|ProbeHealthStatusEvents|Evento de resultados de saúde da sonda de tráfego|No|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|GatewayDiagnosticLog|Registos de diagnóstico gateway|No|
|IKEDiagnosticLog|Registos de diagnóstico IKE|No|
|P2SDiagnosticLog|Registos de diagnóstico P2S|No|
|RouteDiagnosticLog|Registos de diagnóstico de rotas|No|
|TúnelDiagnosticLog|Registos de diagnóstico do túnel|No|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|VMProtectionAlerts|Alertas de proteção VM|No|


## <a name="microsoftnetworkvpngateways"></a>Microsoft.Network/vpnGateways

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|GatewayDiagnosticLog|Registos de diagnóstico gateway|No|
|IKEDiagnosticLog|Registos de diagnóstico IKE|No|
|RouteDiagnosticLog|Registos de diagnóstico de rotas|No|
|TúnelDiagnosticLog|Registos de diagnóstico do túnel|No|


## <a name="microsoftnotificationhubsnamespaces"></a>Microsoft.NotificationHubs/namespaces

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Logística|Registos Operacionais|No|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Auditoria|Registos de Auditoria|No|


## <a name="microsoftpowerbitenants"></a>Microsoft.PowerBI/inquilinos

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Motor|Motor|No|


## <a name="microsoftpowerbitenantsworkspaces"></a>Microsoft.PowerBI/inquilinos/espaços de trabalho

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Motor|Motor|No|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Motor|Motor|No|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft.ProjectBabylon/contas

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|No|


## <a name="microsoftpurviewaccounts"></a>microsoft.purview/accounts

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|No|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AddonAzureBackupAlerts|Dados de alerta de backup addon Azure|No|
|AddonAzureBackupJobs|Dados de trabalho de backup addon Azure|No|
|AddonAzureBackupPolicy|Dados da política de backup addon Azure|No|
|AddonAzureBackupProtectedInstance|Dados de instância protegidos de Addon Azure|No|
|AddonAzureBackupstorage|Dados de armazenamento de backup Addon Azure|No|
|AzureBackupReport|Dados de relatórios de backup da Azure|No|
|AzureSiteRecoveryEvents|Eventos de recuperação do local de Azure|No|
|AzureSiteRecoveryJobs|Empregos de recuperação do local de Azure|No|
|AzureSiteRecoveryProtectedDiskDataChurn|Recuperação do site Azure Dados protegidos do disco churn|No|
|AzureSiteRecoveryRecoveryPoints|Pontos de recuperação do local de Azure|No|
|AzureSiteRecoveryReplicatedItems|Itens replicados de recuperação do local de Azure|No|
|AzureSiteRecoveryReplicationDataUploadRate|Taxa de carregamento de dados de replicação do site Azure|No|
|AzureSiteRecoveryReplicationStats|Estatísticas de replicação da replicação do local de Azure|No|
|CoreAzureBackup|Dados de backup do Core Azure|No|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|HybridConnectionsEvent|Eventos HybridConnections|No|
|HybridConnectionsLogs|HybridConnectionsLogs|No|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|OperaçãoLogs|Registos de operação|No|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Logística|Registos Operacionais|No|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalrService/Signalr

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AllLogs|Registos de serviço Azure SignalR.|No|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|DevOpsOperationsAudit|Registos de auditoria de operações devops|No|
|ResourceUsageStats|Estatísticas de Utilização de Recursos|No|
|SQLSecurityAuditEvents|Evento de Auditoria de Segurança SQL|No|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.Sql/managedInstances/databases

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Erros|Erros|No|
|QueryStoreRuntimeStatistics|Estatísticas de runtime da loja de consultas|No|
|QueryStoreWaitStatistics|Estatísticas de espera da loja de consultas|No|
|SQLInsights|SQL Insights|No|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servidores/bases de dados

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Sintonização Automática|Ajuste automático|No|
|Blocos|Blocos|No|
|Base de dadosWaitStatistics|Estatísticas de espera de base de dados|No|
|Impasses|Impasses|No|
|DevOpsOperationsAudit|Registos de auditoria de operações devops|No|
|DmsWorkers|Trabalhadores da DMS|No|
|Erros|Erros|No|
|ExecRequests|Pedidos de Executivos|No|
|QueryStoreRuntimeStatistics|Estatísticas de runtime da loja de consultas|No|
|QueryStoreWaitStatistics|Estatísticas de espera da loja de consultas|No|
|PedidoSteps|Passos de Pedido|No|
|SQLInsights|SQL Insights|No|
|SqlRequests|Pedidos Sql|No|
|SQLSecurityAuditEvents|Evento de Auditoria de Segurança SQL|No|
|Tempos limite|Tempos limite|No|
|Espera|Espera|No|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAcontas/blobServices

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|ArmazenamentoRead|ArmazenamentoRead|Yes|
|ArmazenamentoPorite|ArmazenamentoPorite|Yes|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAcontas/fileServices

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|ArmazenamentoRead|ArmazenamentoRead|Yes|
|ArmazenamentoPorite|ArmazenamentoPorite|Yes|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAcontas/filas Serviços

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|ArmazenamentoRead|ArmazenamentoRead|Yes|
|ArmazenamentoPorite|ArmazenamentoPorite|Yes|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAcontas/tableServices

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|ArmazenamentoRead|ArmazenamentoRead|Yes|
|ArmazenamentoPorite|ArmazenamentoPorite|Yes|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Criação|Criação|No|
|Execução|Execução|No|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|BuiltinSqlReqsEnded|Pedidos de piscina de sql embutidos terminados|No|
|GatewayApiRequests|Pedidos de Api de Gateway De Sinaapse|No|
|SQLSecurityAuditEvents|Evento de Auditoria de Segurança SQL|No|
|Operações De SinapseRbac|Operações do RBAC de Sinapse|No|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|BigDataPoolAppsEnded|Aplicações de big data pool terminadas|No|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|DmsWorkers|Trabalhadores da DMS|No|
|ExecRequests|Pedidos de Executivos|No|
|PedidoSteps|Passos de Pedido|No|
|SqlRequests|Pedidos Sql|No|
|SQLSecurityAuditEvents|Evento de Auditoria de Segurança Sql|No|
|Espera|Espera|No|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/ambientes

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Entrada|Entrada|No|
|Gestão|Gestão|No|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/ambientes/eventsources

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Entrada|Entrada|No|
|Gestão|Gestão|No|


## <a name="microsoftwebhostingenvironments"></a>microsoft.web/hostingenvironments

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AppServiceEnvironmentPlatformLogs|Registos de plataformas de ambiente de serviço de aplicativos|No|


## <a name="microsoftwebsites"></a>microsoft.web/sites

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Reportar Registos de Auditoria Antivírus|No|
|AppServiceAppLogs|Registos de aplicações do serviço de aplicações de aplicações|No|
|AppServiceAuditLogs|Aceder a Registos de Auditoria|No|
|AppServiceConsoleLogs|Registos de consola de serviço de aplicativos|No|
|AppServiceFileAuditLogs|Registos de auditoria de alteração de conteúdo do site|No|
|AppServiceHTTPLogs|Registos HTTP|No|
|AppServiceIPSecAuditLogs|Registos de Auditoria ipsegurança|No|
|AppServicePlatformLogs|Registos da Plataforma de Serviço de Aplicações|No|
|FunÇõesAppLogs|Registos de aplicação de funções|No|


## <a name="microsoftwebsitesslots"></a>microsoft.web/sites/slots

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Reportar Registos de Auditoria Antivírus|No|
|AppServiceAppLogs|Registos de aplicações do serviço de aplicações de aplicações|No|
|AppServiceAuditLogs|Aceder a Registos de Auditoria|No|
|AppServiceConsoleLogs|Registos de consola de serviço de aplicativos|No|
|AppServiceFileAuditLogs|Registos de auditoria de alteração de conteúdo do site|No|
|AppServiceHTTPLogs|Registos HTTP|No|
|AppServiceIPSecAuditLogs|Registos de Auditoria ipSegurança|No|
|AppServicePlatformLogs|Registos da Plataforma de Serviço de Aplicações|No|
|FunÇõesAppLogs|Registos de aplicação de funções|No|



## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre registos de recursos](../essentials/platform-logs-overview.md)
* [Fluxo de registos de recursos para **Centros de Eventos**](./resource-logs.md#send-to-azure-event-hubs)
* [Alterar definições de diagnóstico de registo de recursos utilizando a API do Monitor Azure](/rest/api/monitor/diagnosticsettings)
* [Analise os registos do armazenamento do Azure com o Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

