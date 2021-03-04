---
title: Azure Monitor Registos de Recursos suportados serviços e categorias
description: Referência do Azure Monitor Compreenda os serviços suportados e esquema de eventos para registos de recursos Azure.
ms.topic: reference
ms.date: 01/29/2021
ms.openlocfilehash: 9a04d0f470522dd4689d604756ffd25e70c5d456
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033151"
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
|Logia de Conta|Logia de Conta|Não|
|Gestão de Contas|Gestão de Contas|Não|
|DetalheTracking|DetalheTracking|Não|
|DirectórioServiceAccess|DirectórioServiceAccess|Não|
|LogonLogoff|LogonLogoff|Não|
|ObjectAccess|ObjectAccess|Não|
|Troca de Políticas|Troca de Políticas|Não|
|PrivilegeUse|PrivilegeUse|Não|
|Segurança do Sistema|Segurança do Sistema|Não|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servidores

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Motor|Motor|Não|
|Serviço|Serviço|Não|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|GatewayLogs|Registos relacionados com gateway de ApiManagement|Não|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|HttpRequest|Pedidos HTTP|Sim|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/primavera

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AplicaçãoConsola|Consola de aplicação|Não|
|SystemLogs|Registos do sistema|Não|


## <a name="microsoftattestationattestationproviders"></a>Microsoft.Attestation/attestationProviders

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AuditEvent|AuditEvent categoria de registo de mensagens.|Não|
|ERR|Categoria de registo de mensagem de erro.|Não|
|INF|Categoria de registo de mensagens informacionais.|Não|
|WRN|Categoria de registo de mensagem de aviso.|Não|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automation

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|DscNodeStatus|Estado do nó Dsc|Não|
|JobLogs|Registos de Emprego|Não|
|JobStreams|Fluxos de Emprego|Não|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batcontas ch/batch

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|ServiceLog|Registos de serviço|Não|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/espaços de trabalho

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|BaiClusterEvent|BaiClusterEvent|Não|
|BaiClusterNodeEvent|BaiClusterNodeEvent|Não|
|BaiJobEvent|BaiJobEvent|Não|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|BlockchainApplicação|Aplicação Blockchain|Não|
|FabricaÇão|Ordemdor de Tecidos|Não|
|FabricPeer|Par de tecido|Não|
|Proxy|Proxy|Não|


## <a name="microsoftblockchaincordamembers"></a>Microsoft.Blockchain/cordaMembers

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|BlockchainApplicação|Aplicação Blockchain|Não|


## <a name="microsoftbotservicebotservices"></a>microsoft.botservice/botservices

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|BotRequest|Pedidos dos canais para o bot|Não|
|Requestração de Dependência|Pedidos de dependências|Não|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|WebApplicationFirewallLogs|Registos de firewall de aplicação web|Não|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/perfis

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AzureCdnAccessLog|Log de acesso Azure Cdn|Não|
|FrontDoorAccessLog|Registo de acesso frontdoor|Sim|
|FrontDoorHealthProbeLog|Registo da sonda de saúde frontdoor|Sim|
|FrontDoorWebApplicationFirewallLog|FrontDoor WebApplicationFirewall Log|Sim|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft.Cdn/perfis/pontos finais

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|CoreAnalytics|Obtém as métricas do ponto final, por exemplo, largura de banda, saída, etc.|Não|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft.ClassicNetwork/grupos de segurança de rede

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Evento de fluxo de regras do grupo de segurança de rede|Evento de fluxo de regras do grupo de segurança de rede|Não|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/contas

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Auditoria|Registos de Auditoria|Não|
|SolicitaçãoResponse|Registos de Pedidos e Respostas|Não|
|Rastreio|Registos de vestígios|Não|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft.Communication/CommunicationServices

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|ChatOperational|Registos de chat operacionais|Não|
|SMSOperational|Registos SMS operacionais|Não|
|Utilização|Registos de Utilização|Não|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|ContainerRegistryLoginEvents|Eventos de Login|Não|
|ContainerRegistryRepositoryEvents|RepositórioRs de registos|Não|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|cluster-autoscaler|Kubernetes Cluster Autoscaler|Não|
|guarda|guarda|Não|
|kube-apiserver|Servidor API de Kubernetes|Não|
|kube-auditoria|Auditoria de Kubernetes|Não|
|kube-audit-admin|Registos de Admin de Auditoria de Kubernetes|Não|
|kube-controlador-manager|Gestor do Controlador Kubernetes|Não|
|kube-scheduler|Programador Kubernetes|Não|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Auditorias|Registos de auditoria para chamadas miniRP|Não|


## <a name="microsoftd365customerinsightsinstances"></a>Microsoft.D365CustomerInsights/instâncias

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Auditoria|Eventos de auditoria|Não|
|Operacional|Eventos operacionais|Não|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft.Databricks/workspaces

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|accounts|Contas databricks|Não|
|aglomerados|Databricks Clusters|Não|
|dbfs|Sistema de Ficheiros do Databricks|Não|
|instâncias|Piscinas de Exemplo|Não|
|empregos|Databricks Jobs|Não|
|bloco de notas|Databricks Notebook|Não|
|segredos|Segredos databricks|Não|
|sqlPermissions|Databricks SQLPermissions|Não|
|ssh|Databricks SSH|Não|
|área de trabalho|Databricks Workspace|Não|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft.DataCollaboration/workspaces

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|ColaboraçãoAudit|Auditoria de Colaboração|Sim|
|DataAssets|Ativos de Dados|Não|
|Pipelines|Pipelines|Não|
|Propostas|Propostas|Não|
|Scripts|Scripts|Não|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/fábricas

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Corridas de Atividades|A atividade do gasoduto executa o log|Não|
|PipelineRuns|Pipeline executa log|Não|
|SSISIntegrationRuntimeLogs|Registos de tempo de execução de integração SSIS|Não|
|SSISPackageEventMessageContext|SSIS pacote contexto de mensagem de evento|Não|
|SSISPackageEventMes|Mensagens de evento pacote SSIS|Não|
|SSISPackageExecutableStatistics|Estatísticas executáveis do pacote SSIS|Não|
|SSISPackageExecutionComponentPhases|Fases componentes de execução de pacote SSIS|Não|
|SSISPackageExecutionDataStatistics|Estatísticas de dados de exuções ao pacote SSIS|Não|
|TriggerRuns|O gatilho executa o registo|Não|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/contas

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Auditoria|Registos de Auditoria|Não|
|Pedidos|Solicitar Registos|Não|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/contas

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Auditoria|Registos de Auditoria|Não|
|Pedidos|Solicitar Registos|Não|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/contas

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|RecebeuPartilhaSnapshots|Fotos de partilha recebidas|Não|
|SentShareSnapshots|Instantâneos de partilha enviados|Não|
|Partilhas|Partilhas|Não|
|Subscrições de Partes|Assinaturas de partilha|Não|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servidores

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|MySqlAuditLogs|Registos de Auditoria MariaDB|Não|
|MySqlSlowLogs|Registos de servidores MariaDB|Não|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|MySqlAuditLogs|Registos de auditoria mySQL|Não|
|MySqlSlowLogs|Registos Lentos MySQL|Não|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servidores

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|MySqlAuditLogs|Registos de auditoria mySQL|Não|
|MySqlSlowLogs|Registos de servidor mySQL|Não|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|PostgreSQLLogs|Registos de servidor pós-SQL|Não|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servidores

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|PostgreSQLLogs|Registos de servidor pós-SQL|Não|
|QueryStoreRuntimeStatistics|Estatísticas de tempo de runtime da loja de consultas pós-ql|Não|
|QueryStoreWaitStatistics|Estatísticas de espera da loja de consultas postgresql|Não|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|PostgreSQLLogs|Registos de servidor pós-SQL|Não|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft.DesktopVirtualization/applicationgroups

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Check Point|Check Point|Não|
|Erro|Erro|Não|
|Gestão|Gestão|Não|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft.DesktopVirtualization/hostpools

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AgenteHealthStatus|AgenteHealthStatus|Não|
|Check Point|Check Point|Não|
|Ligação|Ligação|Não|
|Erro|Erro|Não|
|HostRegistration|HostRegistration|Não|
|Gestão|Gestão|Não|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft.DesktopVirtualization/workspaces

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Check Point|Check Point|Não|
|Erro|Erro|Não|
|Feed|Feed|Não|
|Gestão|Gestão|Não|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|C2DCommands|Comandos C2D|Não|
|C2DTwinOperações|Operações Gémeas C2D|Não|
|Configurações|Configurações|Não|
|Ligações|Ligações|Não|
|D2CTwinOperações|D2CTwinOperações|Não|
|Operações DeviceIdentity|Operações de Identidade de Dispositivo|Não|
|DispositivosStreams|Streams de dispositivos (pré-visualização)|Não|
|DeviceTelemetry|Telemetria do Dispositivo|Não|
|DirectMethods|Métodos Diretos|Não|
|Tracagem distribuída|Rastreio distribuído (pré-visualização)|Não|
|Operações de carga de ficheiros|Operações de upload de ficheiros|Não|
|JobsOperations|Operações de Emprego|Não|
|Rotas|Rotas|Não|
|TwinQueries|Consultas Gémeas|Não|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|C2DCommands|Comandos C2D|Não|
|C2DTwinOperações|Operações Gémeas C2D|Não|
|Configurações|Configurações|Não|
|Ligações|Ligações|Não|
|D2CTwinOperações|D2CTwinOperações|Não|
|Operações DeviceIdentity|Operações de Identidade de Dispositivo|Não|
|DispositivosStreams|Streams de dispositivos (pré-visualização)|Não|
|DeviceTelemetry|Telemetria do Dispositivo|Não|
|DirectMethods|Métodos Diretos|Não|
|Tracagem distribuída|Rastreio distribuído (pré-visualização)|Não|
|Operações de carga de ficheiros|Operações de upload de ficheiros|Não|
|JobsOperations|Operações de Emprego|Não|
|Rotas|Rotas|Não|
|TwinQueries|Consultas Gémeas|Não|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Operações de Dispositivos|Operações de Dispositivos|Não|
|Operações de Serviço|Operações de Serviço|Não|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft.DigitalTwins/digitalTwinsInstances

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|DigitalTwinsOperação|DigitalTwinsOperação|Não|
|EventRoutesOperação|EventRoutesOperação|Não|
|ModelosOperação|ModelosOperação|Não|
|Consulta|Consulta|Não|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/base de dadosAcontas

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|CassandraRequests|CassandraRequests|Não|
|ControlPlaneRequests|ControlPlaneRequests|Não|
|DataPlaneRequests|DataPlaneRequests|Não|
|GremlinRequests|GremlinRequests|Não|
|MongoRequests|MongoRequests|Não|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|Não|
|PartitionKeyStatistics|PartitionKeyStatistics|Não|
|QueryRuntimeStatistics|QueryRuntimeStatistics|Não|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domínios

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|EntregasFailures|Registos de falha de entrega|Não|
|PublicarFilures|Publicar Registos de Falhas|Não|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft.EventGrid/partnerNamespaces

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|EntregasFailures|Registos de falha de entrega|Não|
|PublicarFilures|Publicar Registos de Falhas|Não|


## <a name="microsofteventgridpartnertopics"></a>Microsoft.EventGrid/partnerTopics

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|EntregasFailures|Registos de falha de entrega|Não|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|EntregasFailures|Registos de falha de entrega|Não|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/tópicos

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|EntregasFailures|Registos de falha de entrega|Não|
|PublicarFilures|Publicar Registos de Falhas|Não|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/espaços de nome

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|ArchiveLogs|Registos de Arquivo|Não|
|AutoScaleLogs|Registos de escala automática|Não|
|CustomerManagedKeyUserLogs|Registos de chaves geridos pelo cliente|Não|
|EventHubVNetConnectionEvent|Registos de ligação de filtragem VNet/IP|Não|
|KafkaCoordinatorLogs|Registos do Coordenador kafka|Não|
|KafkaUserErrorLogs|Registos de erro do utilizador kafka|Não|
|Logística|Registos Operacionais|Não|


## <a name="microsoftexperimentationexperimentworkspaces"></a>microsoft.experimentation/experimentWorkspaces

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Pedir|Pedir|Não|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/serviços

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Auditorias|Registos de auditoria|Não|


## <a name="microsoftinsightsautoscalesettings"></a>microsoft.insights/autoescalações

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Reavaliações de autoescala|Avaliações de autoescala|Não|
|AutoscaleActions|Ações de escala automática|Não|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Componentes

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AppDilabilityResults|Resultados da disponibilidade|Não|
|AppBrowserTimings|Timings do navegador|Não|
|AppDependencies|Dependências|Não|
|Eventos AppEvents|Eventos|Não|
|AppExceptions|Exceções|Não|
|AppMetrics|Métricas|Não|
|AppPageViews|Vistas da página|Não|
|AppPerformanceCounters|Contadores de desempenho|Não|
|Recorrentes|Pedidos|Não|
|Eventos AppSystemEvents|Eventos do sistema|Não|
|AppTraces|Rastreios|Não|


## <a name="microsoftiotspacesgraph"></a>Microsoft.IoTSpaces/Gráfico

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Auditoria|Auditoria|Não|
|Saída|Saída|Não|
|Entrada|Entrada|Não|
|Operacional|Operacional|Não|
|Rastreio|Rastreio|Não|
|Utilização de UtilizadoresDefinedFunction|Utilização de UtilizadoresDefinedFunction|Não|


## <a name="microsoftkeyvaultmanagedhsms"></a>microsoft.keyvault/managedhsms

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AuditEvent|Evento de Auditoria|Não|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/cofres

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AuditEvent|Registos de Auditoria|Não|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Comando|Comando|Não|
|Ingestion Falhada|Operações de ingestão falhadas|Não|
|IngestionBatching|Criação de batches de ingestão|Não|
|Consulta|Consulta|Não|
|Ingestion sucedeu|Operações de ingestão bem sucedidas|Não|
|TableDetails|Detalhes da tabela|Não|
|TableUsageStatistics|Estatísticas de utilização de quadros|Não|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft.Logic/integrationAcontas

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|IntegraçãoCocountTrackingEvents|Eventos de rastreio de conta de integração|Não|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|WorkflowRuntime|Eventos de diagnóstico de tempo de trabalho|Não|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|Não|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|Não|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|Não|
|AmlComputeJobEvent|AmlComputeJobEvent|Não|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|Não|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|KeyDeliveryRequests|Pedidos de entrega chave|Não|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|ApplicationGatewayAccessLog|Registo de acesso a gateway de aplicação|Não|
|ApplicationGatewayFirewallLog|Registo de firewall gateway de aplicação|Não|
|ApplicationGatewayPerformanceLog|Registo de desempenho do Gateway de Aplicação|Não|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AzureFirewallApplicationRule|Regra de aplicação da firewall Azure|Não|
|AzureFirewallDnsProxy|Azure Firewall DNS Proxy|Não|
|AzureFirewallNetworkRule|Regra da rede de firewall Azure|Não|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft.Network/bastionHosts

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|BastionAuditLogs|Registos de Auditoria de Bastião|Não|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|PeeringRouteLog|Registos de tabela de rota de espreitar|Não|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|FrontdoorAccessLog|Registo de acesso frontal|Não|
|FrontdoorWebApplicationFirewallLog|Registo de firewall de aplicação web frontdoor|Não|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|LoadBalancerAlertEvent|Eventos de alerta de balanceador de carga|Não|
|LoadBalancerProbeHealthStatus|Estado de saúde da sonda de balanço de carga|Não|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft.Network/networksecuritygroups

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Evento networkSecurityGroupEvent|Evento do Grupo de Segurança de Rede|Não|
|NetworkSecurityGroupFlowEvent|Evento de fluxo de regras do grupo de segurança de rede|Não|
|NetworkSecurityGroupRuleCounter|Contador de regras do grupo de segurança de rede|Não|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft.Network/p2sVpnGateways

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|GatewayDiagnosticLog|Registos de diagnóstico gateway|Não|
|IKEDiagnosticLog|Registos de diagnóstico IKE|Não|
|P2SDiagnosticLog|Registos de diagnóstico P2S|Não|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|DDoSMitigationFlowLogs|Registos de fluxo de decisões de mitigação do DDoS|Não|
|DDoSMitigationReports|Relatórios de mitigações do DDoS|Não|
|DDoSProtectionNotificações|Notificações de proteção DDoS|Não|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|ProbeHealthStatusEvents|Evento de resultados de saúde da sonda de tráfego|Não|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|GatewayDiagnosticLog|Registos de diagnóstico gateway|Não|
|IKEDiagnosticLog|Registos de diagnóstico IKE|Não|
|P2SDiagnosticLog|Registos de diagnóstico P2S|Não|
|RouteDiagnosticLog|Registos de diagnóstico de rotas|Não|
|TúnelDiagnosticLog|Registos de diagnóstico do túnel|Não|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|VMProtectionAlerts|Alertas de proteção VM|Não|


## <a name="microsoftnetworkvpngateways"></a>Microsoft.Network/vpnGateways

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|GatewayDiagnosticLog|Registos de diagnóstico gateway|Não|
|IKEDiagnosticLog|Registos de diagnóstico IKE|Não|
|RouteDiagnosticLog|Registos de diagnóstico de rotas|Não|
|TúnelDiagnosticLog|Registos de diagnóstico do túnel|Não|


## <a name="microsoftnotificationhubsnamespaces"></a>Microsoft.NotificationHubs/namespaces

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Logística|Registos Operacionais|Não|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Auditoria|Registos de Auditoria|Não|


## <a name="microsoftpowerbitenants"></a>Microsoft.PowerBI/inquilinos

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Motor|Motor|Não|


## <a name="microsoftpowerbitenantsworkspaces"></a>Microsoft.PowerBI/inquilinos/espaços de trabalho

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Motor|Motor|Não|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Motor|Motor|Não|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft.ProjectBabylon/contas

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|Não|


## <a name="microsoftpurviewaccounts"></a>microsoft.purview/accounts

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|Não|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AddonAzureBackupAlerts|Dados de alerta de backup addon Azure|Não|
|AddonAzureBackupJobs|Dados de trabalho de backup addon Azure|Não|
|AddonAzureBackupPolicy|Dados da política de backup addon Azure|Não|
|AddonAzureBackupProtectedInstance|Dados de instância protegidos de Addon Azure|Não|
|AddonAzureBackupstorage|Dados de armazenamento de backup Addon Azure|Não|
|AzureBackupReport|Dados de relatórios de backup da Azure|Não|
|AzureSiteRecoveryEvents|Eventos de recuperação do local de Azure|Não|
|AzureSiteRecoveryJobs|Empregos de recuperação do local de Azure|Não|
|AzureSiteRecoveryProtectedDiskDataChurn|Recuperação do site Azure Dados protegidos do disco churn|Não|
|AzureSiteRecoveryRecoveryPoints|Pontos de recuperação do local de Azure|Não|
|AzureSiteRecoveryReplicatedItems|Itens replicados de recuperação do local de Azure|Não|
|AzureSiteRecoveryReplicationDataUploadRate|Taxa de carregamento de dados de replicação do site Azure|Não|
|AzureSiteRecoveryReplicationStats|Estatísticas de replicação da replicação do local de Azure|Não|
|CoreAzureBackup|Dados de backup do Core Azure|Não|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|HybridConnectionsEvent|Eventos HybridConnections|Não|
|HybridConnectionsLogs|HybridConnectionsLogs|Não|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|OperaçãoLogs|Registos de operação|Não|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Logística|Registos Operacionais|Não|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalrService/Signalr

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AllLogs|Registos de serviço Azure SignalR.|Não|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|DevOpsOperationsAudit|Registos de auditoria de operações devops|Não|
|ResourceUsageStats|Estatísticas de Utilização de Recursos|Não|
|SQLSecurityAuditEvents|Evento de Auditoria de Segurança SQL|Não|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.Sql/managedInstances/databases

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Erros|Erros|Não|
|QueryStoreRuntimeStatistics|Estatísticas de runtime da loja de consultas|Não|
|QueryStoreWaitStatistics|Estatísticas de espera da loja de consultas|Não|
|SQLInsights|SQL Insights|Não|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servidores/bases de dados

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Sintonização Automática|Ajuste automático|Não|
|Blocos|Blocos|Não|
|Base de dadosWaitStatistics|Estatísticas de espera de base de dados|Não|
|Impasses|Impasses|Não|
|DevOpsOperationsAudit|Registos de auditoria de operações devops|Não|
|DmsWorkers|Trabalhadores da DMS|Não|
|Erros|Erros|Não|
|ExecRequests|Pedidos de Executivos|Não|
|QueryStoreRuntimeStatistics|Estatísticas de runtime da loja de consultas|Não|
|QueryStoreWaitStatistics|Estatísticas de espera da loja de consultas|Não|
|PedidoSteps|Passos de Pedido|Não|
|SQLInsights|SQL Insights|Não|
|SqlRequests|Pedidos Sql|Não|
|SQLSecurityAuditEvents|Evento de Auditoria de Segurança SQL|Não|
|Tempos limite|Tempos limite|Não|
|Espera|Espera|Não|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAcontas/blobServices

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|StorageDelete|StorageDelete|Sim|
|ArmazenamentoRead|ArmazenamentoRead|Sim|
|ArmazenamentoPorite|ArmazenamentoPorite|Sim|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAcontas/fileServices

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|StorageDelete|StorageDelete|Sim|
|ArmazenamentoRead|ArmazenamentoRead|Sim|
|ArmazenamentoPorite|ArmazenamentoPorite|Sim|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAcontas/filas Serviços

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|StorageDelete|StorageDelete|Sim|
|ArmazenamentoRead|ArmazenamentoRead|Sim|
|ArmazenamentoPorite|ArmazenamentoPorite|Sim|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAcontas/tableServices

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|StorageDelete|StorageDelete|Sim|
|ArmazenamentoRead|ArmazenamentoRead|Sim|
|ArmazenamentoPorite|ArmazenamentoPorite|Sim|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Criação|Criação|Não|
|Execução|Execução|Não|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|BuiltinSqlReqsEnded|Pedidos de piscina de sql embutidos terminados|Não|
|GatewayApiRequests|Pedidos de Api de Gateway De Sinaapse|Não|
|SQLSecurityAuditEvents|Evento de Auditoria de Segurança SQL|Não|
|Operações De SinapseRbac|Operações do RBAC de Sinapse|Não|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|BigDataPoolAppsEnded|Aplicações de big data pool terminadas|Não|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|DmsWorkers|Trabalhadores da DMS|Não|
|ExecRequests|Pedidos de Executivos|Não|
|PedidoSteps|Passos de Pedido|Não|
|SqlRequests|Pedidos Sql|Não|
|SQLSecurityAuditEvents|Evento de Auditoria de Segurança Sql|Não|
|Espera|Espera|Não|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/ambientes

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Entrada|Entrada|Não|
|Gestão|Gestão|Não|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/ambientes/eventsources

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|Entrada|Entrada|Não|
|Gestão|Gestão|Não|


## <a name="microsoftwebhostingenvironments"></a>microsoft.web/hostingenvironments

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AppServiceEnvironmentPlatformLogs|Registos de plataformas de ambiente de serviço de aplicativos|Não|


## <a name="microsoftwebsites"></a>microsoft.web/sites

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Reportar Registos de Auditoria Antivírus|Não|
|AppServiceAppLogs|Registos de aplicações do serviço de aplicações de aplicações|Não|
|AppServiceAuditLogs|Aceder a Registos de Auditoria|Não|
|AppServiceConsoleLogs|Registos de consola de serviço de aplicativos|Não|
|AppServiceFileAuditLogs|Registos de auditoria de alteração de conteúdo do site|Não|
|AppServiceHTTPLogs|Registos HTTP|Não|
|AppServiceIPSecAuditLogs|Registos de Auditoria ipsegurança|Não|
|AppServicePlatformLogs|Registos da Plataforma de Serviço de Aplicações|Não|
|FunÇõesAppLogs|Registos de aplicação de funções|Não|


## <a name="microsoftwebsitesslots"></a>microsoft.web/sites/slots

|Categoria|Nome de exibição de categoria|Custos para exportação|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Reportar Registos de Auditoria Antivírus|Não|
|AppServiceAppLogs|Registos de aplicações do serviço de aplicações de aplicações|Não|
|AppServiceAuditLogs|Aceder a Registos de Auditoria|Não|
|AppServiceConsoleLogs|Registos de consola de serviço de aplicativos|Não|
|AppServiceFileAuditLogs|Registos de auditoria de alteração de conteúdo do site|Não|
|AppServiceHTTPLogs|Registos HTTP|Não|
|AppServiceIPSecAuditLogs|Registos de Auditoria ipSegurança|Não|
|AppServicePlatformLogs|Registos da Plataforma de Serviço de Aplicações|Não|
|FunÇõesAppLogs|Registos de aplicação de funções|Não|



## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre registos de recursos](../essentials/platform-logs-overview.md)
* [Fluxo de registos de recursos para **Centros de Eventos**](./resource-logs.md#send-to-azure-event-hubs)
* [Alterar definições de diagnóstico de registo de recursos utilizando a API do Monitor Azure](/rest/api/monitor/diagnosticsettings)
* [Analise os registos do armazenamento do Azure com o Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

