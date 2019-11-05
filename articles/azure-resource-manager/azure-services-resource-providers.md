---
title: Azure Resource Manager provedores de recursos pelos serviços do Azure
description: Lista todos os namespaces do provedor de recursos para Azure Resource Manager e mostra o serviço do Azure para esse namespace.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: tomfitz
ms.openlocfilehash: 3bbd52615962c2cd187fb16282a373688175cf59
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476692"
---
# <a name="resource-providers-for-azure-services"></a>Provedores de recursos para serviços do Azure

Este artigo mostra como os namespaces do provedor de recursos são mapeados para os serviços do Azure.

## <a name="match-resource-provider-to-service"></a>Corresponder provedor de recursos ao serviço

| Namespace do provedor de recursos | Serviço do Azure |
| --------------------------- | ------------- |
| Microsoft. AAD | [Azure Active Directory Domain Services](../active-directory-domain-services/index.yml) |
| Microsoft. aadiam | [Azure Active Directory](/azure/active-directory/) |
| Microsoft. addons | núcleo |
| Microsoft. ADHybridHealthService | [Azure Active Directory](/azure/active-directory/) |
| Microsoft. Advisor | [Assistente do Azure](../advisor/index.yml) |
| Microsoft. AlertsManagement | [Azure Monitor](../azure-monitor/index.yml) |
| Microsoft. AnalysisServices | [Azure Analysis Services](/azure/analysis-services/) |
| Microsoft. ApiManagement | [Gerenciamento de API](../api-management/index.yml) |
| Microsoft. AppConfiguration | núcleo |
| Microsoft. atestador | Serviço de atestado do Azure |
| Microsoft. Authorization | [Azure Resource Manager](index.yml) |
| Microsoft. Automation | [Automatização](../automation/index.yml) |
| Microsoft. AzureActiveDirectory | [Azure Active Directory B2C](../active-directory-b2c/index.yml) |
| Microsoft. AzureStack | núcleo |
| Microsoft.Batch | [Batch](../batch/index.yml) |
| Microsoft. billing | [Faturação](/azure/billing/) |
| Microsoft. BingMaps | [Bing Maps](https://docs.microsoft.com/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft. BizTalkservices | [Serviços BizTalk](../logic-apps/logic-apps-move-from-mabs.md) |
| Microsoft. Blockchain | [Serviço Blockchain do Azure](/azure/blockchain/workbench/) |
| Microsoft. Blueprint | [Plantas do Azure](/azure/governance/blueprints/) |
| Microsoft. BotService | [Serviço de bot do Azure](/azure/bot-service/) |
| Microsoft. cache | [Cache do Azure para Redis](/azure/azure-cache-for-redis/) |
| Microsoft. Capacity | núcleo |
| Microsoft. CDN | [Rede de distribuição de conteúdo](../cdn/index.yml) |
| Microsoft. CertificateRegistration | [Certificados do serviço de aplicativo](../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Microsoft. ChangeAnalysis | [Azure Monitor](../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | Máquina virtual do modelo de implantação clássico |
| Microsoft. ClassicInfrastructureMigrate | Migração do modelo de implantação clássico |
| Microsoft. ClassicNetwork | Rede virtual do modelo de implantação clássico |
| Microsoft. ClassicStorage | Armazenamento de modelo de implantação clássico |
| Microsoft. ClassicSubscription | Modelo de implementação clássica |
| Microsoft. Cognitivaservices | [Serviços cognitivas](/azure/cognitive-services/) |
| Microsoft. Commerce | núcleo |
| Microsoft.Compute | [Máquinas virtuais](/azure/virtual-machines/)<br />[Conjuntos de Dimensionamento de Máquinas Virtuais](/azure/virtual-machine-scale-sets/) |
| Microsoft. consumo | [Gerenciamento de custos](/azure/cost-management/) |
| Microsoft. ContainerInstance | [Instâncias de contêiner](/azure/container-instances/) |
| Microsoft. ContainerRegistry | [Registro de contêiner](/azure/container-registry/) |
| Microsoft. ContainerService | [Azure Kubernetes Service (AKS)](/azure/aks/) |
| Microsoft. ContentModerator | [Content Moderator do Azure](../cognitive-services/content-moderator/index.yml) |
| Microsoft. CostManagement | [Gerenciamento de custos](/azure/cost-management/) |
| Microsoft. CostManagementExports | [Gerenciamento de custos](/azure/cost-management/) |
| Microsoft. CustomerInsights | Customer Insights |
| Microsoft. CustomerLockbox | Sistema de Proteção de Dados do Cliente para Microsoft Azure |
| Microsoft. CustomProviders | [Provedores personalizados do Azure](../managed-applications/custom-providers-overview.md) |
| Microsoft. Data Box | [Azure Data Box](/azure/databox-family/) |
| Microsoft. DataBoxEdge | [Azure Data Box Edge](../databox-online/data-box-edge-overview.md) |
| Microsoft. databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft. datacatalog | [Catálogo de dados](/azure/data-catalog/) |
| Microsoft. datafactory | [Data Factory](/azure/data-factory/) |
| Microsoft. DataLakeAnalytics | [Data Lake Analytics](/azure/data-lake-analytics/) |
| Microsoft. DataLakeStore | [Azure Data Lake Store](../storage/blobs/data-lake-storage-introduction.md) |
| Microsoft. datamigration | [Serviço de migração de banco de dados do Azure](/azure/dms/) |
| Microsoft. DataShare | [Compartilhamento de dados do Azure](/azure/data-share/) |
| Microsoft. DBforMariaDB | [Banco de dados do Azure para MariaDB](/azure/mariadb/) |
| Microsoft. DBforMySQL | [Banco de dados do Azure para MySQL](/azure/mysql/) |
| Microsoft. DBforPostgreSQL | [Banco de dados do Azure para PostgreSQL](/azure/postgresql/) |
| Microsoft. DesktopVirtualization | [Área de trabalho virtual do Windows](/azure/virtual-desktop/) |
| Microsoft. DeploymentManager | [Deployment Manager do Azure](deployment-manager-overview.md) |
| Microsoft. Devices | [Hub IoT](/azure/iot-hub/)<br />[Serviço de Aprovisionamento de Dispositivos no Hub IoT](/azure/iot-dps/) |
| Microsoft. DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft. DevSpaces | [Azure Dev Spaces](/azure/dev-spaces/) |
| Microsoft. DevTestLab | [Azure Lab Services](../lab-services/index.yml) |
| Microsoft. DocumentDB | [Azure Cosmos DB](../cosmos-db/index.yml) |
| Microsoft. DomainRegistration | [Serviço de aplicativo](/azure/app-service/) |
| Microsoft. EnterpriseKnowledgeGraph | Grafo de conhecimento corporativo |
| Microsoft. EventGrid | [Grade de eventos](/azure/event-grid/) |
| Microsoft. EventHub | [Hubs de Eventos](../event-hubs/index.yml) |
| Microsoft. Features | [Azure Resource Manager](index.yml) |
| Microsoft. genomas | [Microsoft Genomics](/azure/genomics/) |
| Microsoft. GuestConfiguration | [Azure Policy](../governance/policy/index.yml) |
| Microsoft. HanaOnAzure | [SAP HANA no Azure](../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft. HardwareSecurityModules | [HSM dedicado do Azure](../dedicated-hsm/index.yml) |
| Microsoft. HDInsight | [HDInsight](../hdinsight/index.yml) |
| Microsoft. HealthcareApis | [API do Azure para FHIR](../healthcare-apis/index.yml) |
| Microsoft. HybridData | [StorSimple](/azure/storsimple/) |
| Microsoft. ImportExport | [Importação/exportação do Azure](../storage/common/storage-import-export-service.md) |
| Microsoft. insights | [Azure Monitor](../azure-monitor/index.yml) |
| Microsoft. Intune | [Intune](/intune/) |
| Microsoft. IoTCentral | [IoT Central](/azure/iot-central/) |
| Microsoft. IoTSpaces | [Gêmeos digital do Azure](../digital-twins/index.yml) |
| Microsoft. keyvault | [Cofre de Chaves](../key-vault/index.yml) |
| Microsoft.Kusto | [Data Explorer do Azure](../data-explorer/index.yml) |
| Microsoft. LabServices | [Azure Lab Services](../lab-services/index.yml) |
| Microsoft. LocationBasedServices | [Mapas do Azure](../azure-maps/index.yml) |
| Microsoft. Locationservices | núcleo |
| Microsoft. LogAnalytics | [Azure Monitor](../azure-monitor/index.yml) |
| Microsoft. Logic | [Aplicações Lógicas](../logic-apps/index.yml) |
| Microsoft. MachineLearning | [Machine Learning Studio](../machine-learning/studio/index.yml) |
| Microsoft. MachineLearningCompute | [Serviço de Machine Learning](../machine-learning/index.yml) |
| Microsoft. MachineLearningModelManagement | [Serviço de Machine Learning](../machine-learning/index.yml) |
| Microsoft.MachineLearningServices | [Serviço de Machine Learning](../machine-learning/index.yml) |
| Microsoft. ManagedIdentity | [Identidades geridas para os recursos do Azure](../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft. ManagedLab | [Azure Lab Services](../lab-services/index.yml) |
| Microsoft. Managedservices | [Azure Lighthouse](/azure/lighthouse/) |
| Microsoft. Management | [Grupos de Gerenciamento](/azure/governance/management-groups/) |
| Microsoft. Maps | [Mapas do Azure](../azure-maps/index.yml) |
| Microsoft. Marketplace | núcleo |
| Microsoft. MarketplaceApps | núcleo |
| Microsoft. MarketplaceOrdering | núcleo |
| Microsoft. Media | [Serviços de Multimédia](../media-services/index.yml) |
| Microsoft. Microservices4Spring | [Spring no Azure](/azure/java/spring-framework/) |
| Microsoft. migrar | [Migrações para Azure](../migrate/migrate-overview.md) |
| Microsoft. MixedReality | [Âncoras espaciais do Azure](/azure/spatial-anchors/) |
| Microsoft. NetApp | [Azure NetApp Files](../azure-netapp-files/index.yml) |
| Microsoft.Network | [Rede Virtual](../virtual-network/index.yml)<br />[Balanceador de Carga](../load-balancer/index.yml)<br />[Gateway da Aplicação](../application-gateway/index.yml)<br />[DNS do Azure](../dns/index.yml)<br />[ExpressRoute](../expressroute/index.yml)<br />[Gateway de VPN](../vpn-gateway/index.yml)<br />[Gestor de Tráfego](../traffic-manager/index.yml)<br />[Observador de rede](../network-watcher/index.yml)<br />[Azure Firewall](../firewall/index.yml)<br />[Azure Front Door Service](../frontdoor/index.yml)<br />[Bastiões do Azure](/azure/bastion/) |
| Microsoft. NotificationHubs | [Hubs de Notificação](../notification-hubs/index.yml) |
| Microsoft. OffAzure | [Migrações para Azure](../migrate/migrate-overview.md) |
| Microsoft. OperationalInsights | [Azure Monitor](../azure-monitor/index.yml) |
| Microsoft. OperationsManagement | [Azure Monitor](../azure-monitor/index.yml) |
| Microsoft. emparelhamento | Serviço de emparelhamento Microsoft Azure |
| Microsoft. PolicyInsights | [Azure Policy](../governance/policy/index.yml) |
| Microsoft. Portal | [Portal do Azure](/azure/azure-portal/) |
| Microsoft. PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft. PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft. Recoveryservices | [Site Recovery](../site-recovery/index.yml) |
| Microsoft. Relay | [Retransmissão do Azure](../service-bus-relay/relay-what-is-it.md) |
| Microsoft. ResourceGraph | [Grafo de recursos do Azure](/azure/governance/resource-graph/) |
| Microsoft. ResourceHealth | núcleo |
| Microsoft. Resources | [Azure Resource Manager](index.yml) |
| Microsoft. SaaS | núcleo |
| Microsoft. Scheduler | [Scheduler](/azure/scheduler/) |
| Microsoft. Search | [Azure Search](../search/index.yml) |
| Microsoft. Security | [Central de segurança](../security-center/index.yml) |
| Microsoft. SecurityInsights | [Sentinela do Azure](/azure/sentinel/) |
| Microsoft.ServiceBus | [Service Bus](/azure/service-bus/) |
| Microsoft. infabric | [Service Fabric](../service-fabric/index.yml) |
| Microsoft. ServiceFabricMesh | [Malha de Service Fabric](../service-fabric-mesh/index.yml) |
| Microsoft. SignalRService | [Serviço de Signaler do Azure](../azure-signalr/index.yml) |
| Microsoft. SiteRecovery | [Site Recovery](../site-recovery/index.yml) |
| Microsoft. Solutions | [Aplicativos gerenciados do Azure](../managed-applications/index.yml) |
| Microsoft.Sql | [Base de Dados SQL do Azure](../sql-database/index.yml)<br />[SQL Data Warehouse](/azure/sql-data-warehouse/) |
| Microsoft. SqlVirtualMachine | [SQL Server nas Máquinas Virtuais do Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) |
| Microsoft.Storage | [Armazenamento](../storage/index.yml) |
| Microsoft. StorageCache | [Cache do HPC do Azure](/azure/hpc-cache/) |
| Microsoft. StorageSync | [Armazenamento](../storage/index.yml) |
| Microsoft. StorSimple | [StorSimple](/azure/storsimple/) |
| Microsoft. StreamAnalytics | [Stream Analytics](../stream-analytics/index.yml) |
| Microsoft. Subscription | núcleo |
| Microsoft. support | núcleo |
| Microsoft. TimeSeriesInsights | [Time Series Insights](../time-series-insights/index.yml) |
| Microsoft. VisualStudio | [DevOps do Azure](/azure/devops/?view=azure-devops) |
| Microsoft. VMwareCloudSimple | [Solução do Azure VMware por CloudSimple](/azure/vmware-cloudsimple/) |
| Microsoft. Web | [Serviço de Aplicações](../app-service/index.yml)<br />[Funções](../azure-functions/index.yml) |
| Microsoft. WindowsDefenderATP | [Proteção Avançada Contra Ameaças do Windows Defender](/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection) |
| Microsoft. WindowsIoT | [Serviços do Windows 10 IoT Core](https://docs.microsoft.com/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft. WorkloadMonitor | [Azure Monitor](../azure-monitor/index.yml) |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre provedores de recursos, consulte [tipos e provedores de recursos do Azure](resource-manager-supported-services.md)
