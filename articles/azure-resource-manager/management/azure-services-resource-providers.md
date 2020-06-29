---
title: Fornecedores de recursos por serviços Azure
description: Lista todos os espaços de nome do fornecedor de recursos para O Gestor de Recursos Azure e mostra o serviço Azure para esse espaço de nome.
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: d4dea1dc5ee46d9be7bbc92166535aa93db46275
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2020
ms.locfileid: "85506808"
---
# <a name="resource-providers-for-azure-services"></a>Fornecedores de recursos para serviços Azure

Este artigo mostra como os espaços de nome do fornecedor de recursos mapeiam para os serviços Azure.

## <a name="match-resource-provider-to-service"></a>Combine o fornecedor de recursos ao serviço

| Espaço de nome do fornecedor de recursos | Serviço do Azure |
| --------------------------- | ------------- |
| Microsoft.AAD | [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) |
| Microsoft.Addons | núcleo |
| Microsoft.ADHybridHealthService<sup>1</sup> | [Azure Active Directory](/azure/active-directory/) |
| Microsoft.Advisor | [Azure Advisor](../../advisor/index.yml) |
| Microsoft.AlertsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](/azure/analysis-services/) |
| Microsoft.ApiManagement | [Gestão de API](../../api-management/index.yml) |
| Microsoft.AppConfiguration | [Configuração da Aplicação Azure](../../azure-app-configuration/index.yml) |
| Microsoft.AppPlatform | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft.Attestation | Serviço Azure Attestation |
| Microsoft.Autorização<sup>1</sup> | [Azure Resource Manager](../index.yml) |
| Microsoft.Automation | [Automatização](../../automation/index.yml) |
| Microsoft.AutonomousSystems | [Sistemas Autónomos](https://www.microsoft.com/ai/autonomous-systems) |
| Microsoft.AVS | [Solução VMware no Azure](../../azure-vmware/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| Microsoft.AzureData | Registo do Servidor SQL |
| Microsoft.AzureStack | núcleo |
| Microsoft.AzureStackHCI | [Azure Stack HCI](/azure-stack/hci/overview) |
| Microsoft.Batch | [Batch](../../batch/index.yml) |
| Microsoft.Billing<sup>1</sup> | [Cost Management e Faturação](/azure/billing/) |
| Microsoft.BingMaps | [Mapas Bing](https://docs.microsoft.com/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft.Blockchain | [Azure Blockchain Service](/azure/blockchain/workbench/) |
| Microsoft.BlockchainTokens | [Azure Blockchain Tokens](https://azure.microsoft.com/services/blockchain-tokens/) |
| Microsoft.Blueprint | [Azure Blueprints](/azure/governance/blueprints/) |
| Microsoft.BotService | [Azure Bot Service](/azure/bot-service/) |
| Microsoft.Cache | [Cache do Azure para Redis](/azure/azure-cache-for-redis/) |
| Microsoft.Capacity | núcleo |
| Microsoft.Cdn | [Rede de Entrega de Conteúdos](../../cdn/index.yml) |
| Microsoft.CertificateRegistration | [Certificados de Serviço de Aplicações](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Microsoft.ChangeAnalysis | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | Máquina virtual modelo de implementação clássica |
| Microsoft.ClassicInfrastructureMigrate | Migração de modelo de implantação clássica |
| Microsoft.ClassicNetwork | Rede virtual de modelo de implementação clássica |
| Microsoft.ClassicStorage | Armazenamento clássico de modelo de implementação |
| Microsoft.ClassicSubscription<sup>1</sup> | Modelo de implementação clássica |
| Microsoft.CognitiveServices | [Serviços Cognitivos](/azure/cognitive-services/) |
| Microsoft.Commerce<sup>1</sup> | núcleo |
| Microsoft.Compute | [Virtual Machines](/azure/virtual-machines/)<br />[Conjuntos de Dimensionamento de Máquinas Virtuais](/azure/virtual-machine-scale-sets/) |
| Microsoft.Consumption<sup>1</sup> | [Cost Management](/azure/cost-management/) |
| Microsoft.ContainerInstance | [Container Instances](/azure/container-instances/) |
| Microsoft.ContainerRegistry | [Registo de Contentor](/azure/container-registry/) |
| Microsoft.ContainerService | [Serviço de Kubernetes do Azure (AKS)](/azure/aks/) |
| Microsoft.CostManagement<sup>1</sup> | [Cost Management](/azure/cost-management/) |
| Microsoft.CostManagementExports | [Cost Management](/azure/cost-management/) |
| Microsoft.CustomerLockbox | [Lockbox do cliente para Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md) |
| Microsoft.CustomProviders | [Fornecedores Personalizados do Azure](../custom-providers/overview.md) |
| Microsoft.DataBox | [Azure Data Box](/azure/databox/) |
| Microsoft.DataBoxEdge | [Azure Stack Edge](../../databox-online/azure-stack-edge-overview.md) |
| Microsoft.Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [Catálogo de Dados](/azure/data-catalog/) |
| Microsoft.DataFactory | [Data Factory](/azure/data-factory/) |
| Microsoft.DataLakeAnalytics | [Data Lake Analytics](/azure/data-lake-analytics/) |
| Microsoft.DataLakeStore | [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) (Armazenamento do Azure Data Lake Gen2) |
| Microsoft.DataMigration | [Azure Database Migration Service](/azure/dms/) |
| Microsoft.DataProtection | Proteção de Dados |
| Microsoft.DataShare | [Azure Data Share](/azure/data-share/) |
| Microsoft.DBforMariaDB | [Azure Database for MariaDB](/azure/mariadb/) |
| Microsoft.DBforMySQL | [Base de Dados do Azure para MySQL](/azure/mysql/) |
| Microsoft.DbforPostgresql | [Base de Dados do Azure para PostgreSQL](/azure/postgresql/) |
| Microsoft.DeploymentManager | [Gestor de Implementação Azure](../templates/deployment-manager-overview.md) |
| Microsoft.DesktopVirtualization | [Windows Virtual Desktop](/azure/virtual-desktop/) |
| Microsoft.Devices | [Azure IoT Hub](/azure/iot-hub/)<br />[Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure](/azure/iot-dps/) |
| Microsoft.DevOps | [DevOps do Azure](/azure/devops/) |
| Microsoft.DevSpaces | [Espaços de Programador do Azure](/azure/dev-spaces/) |
| Microsoft.DevTestLab | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft.DigitalTwins | [Azure Digital Twins](../../digital-twins/about-digital-twins.md) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [Serviço de Aplicações](/azure/app-service/) |
| Microsoft.EnterpriseKnowledgeGraph | Gráfico de conhecimento da empresa |
| Microsoft.EventGrid | [Event Grid](/azure/event-grid/) |
| Microsoft.EventHub | [Hubs de Eventos](../../event-hubs/index.yml) |
| Microsoft.Features<sup>1</sup> | [Azure Resource Manager](../index.yml) |
| Microsoft.GuestConfiguration | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [SAP HANA em Instâncias Grandes do Azure](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft.HardwareSecurityModules | [HSM Dedicado do Azure](../../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Microsoft.HealthcareApis | [API do Azure para FHIR](../../healthcare-apis/index.yml) |
| Microsoft.HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft.HybridData | [StorSimple](/azure/storsimple/) |
| Microsoft.ImportExport | [Importar/Exportar do Microsoft Azure](../../storage/common/storage-import-export-service.md) |
| microsoft.insights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.IoTCentral | [Azure IoT Central](/azure/iot-central/) |
| Microsoft.IoTSpaces | [Azure Digital Twins](../../digital-twins/index.yml) |
| Microsoft.KeyVault | [Cofre de Chaves](../../key-vault/index.yml) |
| Microsoft.Kubernetes | [Serviço de Kubernetes do Azure (AKS)](/azure/aks/) |
| Microsoft.KubernetesConfiguration | [Serviço de Kubernetes do Azure (AKS)](/azure/aks/) |
| Microsoft.Kusto | [Azure Data Explorer](/azure/data-explorer/) |
| Microsoft.LabServices | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft.Logic | [Aplicações Lógicas](../../logic-apps/index.yml) |
| Microsoft.MachineLearning | [Machine Learning Studio](../../machine-learning/studio/index.yml) |
| Microsoft.MachineLearningServices | [Azure Machine Learning](../../machine-learning/index.yml) |
| Microsoft.Maintenance | [Manutenção Azure](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft.ManagedIdentity | [Identidades geridas para os recursos do Azure](../../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft.ManagedServices | [Azure Lighthouse](/azure/lighthouse/) |
| Microsoft.Management | [Grupos de Gestão](/azure/governance/management-groups/) |
| Microsoft.Maps | [Azure Maps](../../azure-maps/index.yml) |
| Microsoft.Marketplace | núcleo |
| Microsoft.MarketplaceApps | núcleo |
| Microsoft.MarketplaceOrdering<sup>1</sup> | núcleo |
| Microsoft.Media | [Serviços de Multimédia](../../media-services/index.yml) |
| Microsoft.Migrate | [Azure Migrate](../../migrate/migrate-overview.md) |
| Microsoft.MixedReality | [Azure Spatial Anchors](/azure/spatial-anchors/) |
| Microsoft.NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [Gateway de Aplicação](../../application-gateway/index.yml)<br />[Azure Bastion](/azure/bastion/)<br />[Proteção contra DDoS do Azure](../../virtual-network/ddos-protection-overview.md)<br />[DNS do Azure](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Azure Firewall](../../firewall/index.yml)<br />[Azure Front Door Service](../../frontdoor/index.yml)<br />[Azure Private Link](../../private-link/index.yml)<br />[Load balancer](../../load-balancer/index.yml)<br />[Observador de Rede](../../network-watcher/index.yml)<br />[Gestor de Tráfego](../../traffic-manager/index.yml)<br />[Rede Virtual](../../virtual-network/index.yml)<br />[WAN Virtual](../../virtual-wan/index.yml)<br />[Gateway de VPN](../../vpn-gateway/index.yml)<br /> |
| Microsoft.NotificationHubs | [Hubs de Notificação](../../notification-hubs/index.yml) |
| Microsoft.ObjectStore | Loja de Objetos |
| Microsoft.OffAzure | [Azure Migrate](../../migrate/migrate-overview.md) |
| Microsoft.OperationalInsights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.Peering | [Azure Peering Service](../../peering-service/index.yml) |
| Microsoft.PolicyInsights | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft.Portal<sup>1</sup> | [Portal do Azure](/azure/azure-portal/) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft.PowerPlatform | [Power Platform](/power-platform/) |
| Microsoft.Quantum | [Azure Quantum](https://azure.microsoft.com/services/quantum/) |
| Microsoft.RecoveryServices | [Azure Site Recovery](../../site-recovery/index.yml) |
| Microsoft.RedHatopenShift | [Azure Red Hat OpenShift](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft.Relay | [Reencaminhamento do Azure](../../service-bus-relay/relay-what-is-it.md) |
| Microsoft.ResourceGraph<sup>1</sup> | [Azure Resource Graph](/azure/governance/resource-graph/) |
| Microsoft.ResourceHealth | [Azure Service Health](../../service-health/index.yml) |
| Microsoft.Resources<sup>1</sup> | [Azure Resource Manager](../index.yml) |
| Microsoft.SaaS | núcleo |
| Microsoft.Scheduler | [Scheduler](/azure/scheduler/) |
| Microsoft.Search | [Azure Cognitive Search](../../search/index.yml) |
| Microsoft.Security | [Centro de Segurança](../../security-center/index.yml) |
| Microsoft.SecurityInsights | [Azure Sentinel](/azure/sentinel/) |
| Microsoft.SerialConsole<sup>1</sup> | [Consola em série Azure para Windows](../../virtual-machines/troubleshooting/serial-console-windows.md) |
| Microsoft.ServiceBus | [Service Bus](/azure/service-bus/) |
| Microsoft.ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft.ServiceFabricMesh | [Service Fabric Mesh](../../service-fabric-mesh/index.yml) |
| Microsoft.Services | núcleo |
| Microsoft.SignalRService | [Serviço Azure SignalR](../../azure-signalr/index.yml) |
| Microsoft.SoftwarePlan | Licença |
| Microsoft.Solutions | [Aplicações Geridas do Azure](../managed-applications/index.yml) |
| Microsoft.Sql | [Base de Dados SQL do Azure](../../azure-sql/database/index.yml)<br /> [Instância Gerida do SQL no Azure](../../azure-sql/managed-instance/index.yml) <br />[Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.SqlVirtualMachine | [SQL Server nas Máquinas Virtuais do Azure](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) |
| Microsoft.Storage | [Armazenamento](../../storage/index.yml) |
| Microsoft.StorageSync | [Armazenamento](../../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](/azure/storsimple/) |
| Microsoft.StreamAnalytics | [Azure Stream Analytics](../../stream-analytics/index.yml) |
| Microsoft.Subscrição | núcleo |
| microsoft.support<sup>1</sup> | núcleo |
| Microsoft.Synapse | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.TimeSeriesInsights | [Azure Time Series Insights](../../time-series-insights/index.yml) |
| Microsoft.Token | Token |
| Microsoft.VirtualMachineImages | [Azure Image Builder](../../virtual-machines/linux/image-builder-overview.md) |
| microsoft.visualstudio | [DevOps do Azure](/azure/devops/?view=azure-devops) |
| Microsoft.VMware | [Solução VMware no Azure](../../azure-vmware/index.yml) |
| Microsoft.VMwareCloudSimple | [Azure VMware Solution by CloudSimple](/azure/vmware-cloudsimple/) |
| Microsoft.VSOnline | [DevOps do Azure](/azure/devops/?view=azure-devops) |
| Microsoft.Web | [Serviço de Aplicações](../../app-service/index.yml)<br />[Funções do Azure](../../azure-functions/index.yml) |
| Microsoft.WindowsESU | Atualizações de segurança alargadas |
| Microsoft.WindowsIoT | [Windows 10 IoT Core Services](https://docs.microsoft.com/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft.WorkloadMonitor<sup>1</sup> | [Azure Monitor](../../azure-monitor/index.yml) |

<sup>1</sup> Registado por predefinição

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os fornecedores de recursos, incluindo como registar um fornecedor de recursos, consulte [os fornecedores e tipos de recursos da Azure](resource-providers-and-types.md)
