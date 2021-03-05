---
title: Fornecedores de recursos por serviços Azure
description: Lista todos os espaços de nome do fornecedor de recursos para O Gestor de Recursos Azure e mostra o serviço Azure para esse espaço de nome.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: cc9793bfc0ca6cc0afbede241534453209685d94
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198936"
---
# <a name="resource-providers-for-azure-services"></a>Resource providers for Azure services (Fornecedor de recursos para serviços do Azure)

Este artigo mostra como os espaços de nome do fornecedor de recursos mapeiam para os serviços Azure.

## <a name="match-resource-provider-to-service"></a>Combine o fornecedor de recursos ao serviço

Os fornecedores de recursos com os que estão marcados **- registados** são registados por padrão para a sua subscrição. Para mais informações, consulte [o Registo.](#registration)

| Espaço de nome do fornecedor de recursos | Serviço do Azure |
| --------------------------- | ------------- |
| Microsoft.AAD | [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) |
| Microsoft.Addons | núcleo |
| Microsoft.ADHybridHealthService - [registado](#registration) | [Azure Active Directory](../../active-directory/index.yml) |
| Microsoft.Advisor | [Assistente do Azure](../../advisor/index.yml) |
| Microsoft.AlertsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](../../analysis-services/index.yml) |
| Microsoft.ApiManagement | [Gestão de API](../../api-management/index.yml) |
| Microsoft.AppConfiguration | [Configuração da Aplicação Azure](../../azure-app-configuration/index.yml) |
| Microsoft.AppPlatform | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft.Attestation | Serviço Azure Attestation |
| Microsoft.Authorization - [registado](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft.Automation | [Automatização](../../automation/index.yml) |
| Microsoft.AutonomousSystems | [Sistemas Autónomos](https://www.microsoft.com/ai/autonomous-systems) |
| Microsoft.AVS | [Solução VMware no Azure](../../azure-vmware/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| Microsoft.AzureArcData | Serviços de dados preparados para Azure Arc |
| Microsoft.AzureData | Registo do Servidor SQL |
| Microsoft.AzureStack | núcleo |
| Microsoft.AzureStackHCI | [Azure Stack HCI](/azure-stack/hci/overview) |
| Microsoft.Batch | [Batch](../../batch/index.yml) |
| Microsoft.Billing - [registado](#registration) | [Cost Management e Faturação](/azure/billing/) |
| Microsoft.BingMaps | [Mapas Bing](/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft.Blockchain | [Azure Blockchain Service](../../blockchain/workbench/index.yml) |
| Microsoft.BlockchainTokens | [Azure Blockchain Tokens](https://azure.microsoft.com/services/blockchain-tokens/) |
| Microsoft.Blueprint | [Azure Blueprints](../../governance/blueprints/index.yml) |
| Microsoft.BotService | [Azure Bot Service](/azure/bot-service/) |
| Microsoft.Cache | [Cache do Azure para Redis](../../azure-cache-for-redis/index.yml) |
| Microsoft.Capacity | núcleo |
| Microsoft.Cdn | [Rede de Entrega de Conteúdos](../../cdn/index.yml) |
| Microsoft.CertificateRegistration | [Certificados de Serviço de Aplicações](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Microsoft.ChangeAnalysis | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | Máquina virtual modelo de implementação clássica |
| Microsoft.ClassicInfrastructureMigrate | Migração de modelo de implantação clássica |
| Microsoft.ClassicNetwork | Rede virtual de modelo de implementação clássica |
| Microsoft.ClassicStorage | Armazenamento clássico de modelo de implementação |
| Microsoft.ClassicSubscription - [registado](#registration) | Modelo de implementação clássica |
| Microsoft.CognitiveServices | [Serviços Cognitivos](../../cognitive-services/index.yml) |
| Microsoft.Commerce - [registado](#registration) | núcleo |
| Microsoft.Compute | [Máquinas Virtuais](../../virtual-machines/index.yml)<br />[Conjuntos de Dimensionamento de Máquinas Virtuais](../../virtual-machine-scale-sets/index.yml) |
| Microsoft.Consumption - [registado](#registration) | [Cost Management](/azure/cost-management/) |
| Microsoft.ContainerInstance | [Container Instances](../../container-instances/index.yml) |
| Microsoft.ContainerRegistry | [Container Registry](../../container-registry/index.yml) |
| Microsoft.ContainerService | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft.CostManagement - [registado](#registration) | [Cost Management](/azure/cost-management/) |
| Microsoft.CostManagementExports | [Cost Management](/azure/cost-management/) |
| Microsoft.CustomerLockbox | [Sistema de Proteção de Dados do Cliente para o Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md) |
| Microsoft.CustomProviders | [Fornecedores Personalizados do Azure](../custom-providers/overview.md) |
| Microsoft.DataBox | [Azure Data Box](../../databox/index.yml) |
| Microsoft.DataBoxEdge | [Azure Stack Edge](../../databox-online/azure-stack-edge-overview.md) |
| Microsoft.Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [Catálogo de Dados](../../data-catalog/index.yml) |
| Microsoft.DataFactory | [Data Factory](../../data-factory/index.yml) |
| Microsoft.DataLakeAnalytics | [Data Lake Analytics](../../data-lake-analytics/index.yml) |
| Microsoft.DataLakeStore | [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) (Armazenamento do Azure Data Lake Gen2) |
| Microsoft.DataMigration | [Azure Database Migration Service](../../dms/index.yml) |
| Microsoft.DataProtection | Proteção de Dados |
| Microsoft.DataShare | [Azure Data Share](../../data-share/index.yml) |
| Microsoft.DBforMariaDB | [Azure Database for MariaDB](../../mariadb/index.yml) |
| Microsoft.DBforMySQL | [Base de Dados do Azure para MySQL](../../mysql/index.yml) |
| Microsoft.DbforPostgresql | [Base de Dados do Azure para PostgreSQL](../../postgresql/index.yml) |
| Microsoft.DeploymentManager | [Gestor de Implementação Azure](../templates/deployment-manager-overview.md) |
| Microsoft.DesktopVirtualization | [Windows Virtual Desktop](../../virtual-desktop/index.yml) |
| Microsoft.Devices | [Hub IoT do Azure](../../iot-hub/index.yml)<br />[Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure](../../iot-dps/index.yml) |
| Microsoft.DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft.DevSpaces | [Azure Dev Spaces](../../dev-spaces/index.yml) |
| Microsoft.DevTestLab | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft.DigitalTwins | [Azure Digital Twins](../../digital-twins/overview.md) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [Serviço de Aplicações](../../app-service/index.yml) |
| Microsoft.DynamicsLcs | [Serviços de ciclo de vida](https://lcs.dynamics.com/Logon/Index ) |
| Microsoft.EnterpriseKnowledgeGraph | Gráfico de conhecimento da empresa |
| Microsoft.EventGrid | [Event Grid](../../event-grid/index.yml) |
| Microsoft.EventHub | [Hubs de Eventos](../../event-hubs/index.yml) |
| Microsoft.Features - [registado](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft.GuestConfiguration | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [SAP HANA nas Instâncias Grandes do Azure](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft.HardwareSecurityModules | [Azure Dedicated HSM](../../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Microsoft.HealthcareApis | [API do Azure para FHIR](../../healthcare-apis/index.yml) |
| Microsoft.HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft.HybridData | [StorSimple](../../storsimple/index.yml) |
| Microsoft.HybridNetwork  | [Zonas de Borda Privada](../../networking/edge-zones-overview.md) |
| Microsoft.ImportExport | [Importar/Exportar do Microsoft Azure](../../import-export/storage-import-export-service.md) |
| microsoft.insights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.IoTCentral | [Azure IoT Central](../../iot-central/index.yml) |
| Microsoft.IoTSpaces | [Azure Digital Twins](../../digital-twins/index.yml) |
| Microsoft.Intune | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.KeyVault | [Cofre de Chaves](../../key-vault/index.yml) |
| Microsoft.Kubernetes | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft.KubernetesConfiguration | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft.Kusto | [Azure Data Explorer](/azure/data-explorer/) |
| Microsoft.LabServices | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft.Logic | [Logic Apps](../../logic-apps/index.yml) |
| Microsoft.MachineLearning | [Machine Learning Studio](../../machine-learning/classic/index.yml) |
| Microsoft.MachineLearningServices | [Azure Machine Learning](../../machine-learning/index.yml) |
| Microsoft.Maintenance | [Manutenção Azure](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft.ManagedIdentity | [Identidades geridas para os recursos do Azure](../../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft.ManagedNetwork | Redes virtuais geridas pelos serviços PaaS |
| Microsoft.ManagedServices | [Azure Lighthouse](../../lighthouse/index.yml) |
| Microsoft.Management | [Grupos de Gestão](../../governance/management-groups/index.yml) |
| Microsoft.Maps | [Azure Maps](../../azure-maps/index.yml) |
| Microsoft.Marketplace | núcleo |
| Microsoft.MarketplaceApps | núcleo |
| Microsoft.MarketplaceOrdering - [registado](#registration) | núcleo |
| Microsoft.Media | [Serviços de Multimédia](../../media-services/index.yml) |
| Microsoft.Microservices4Spring | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft.Migrate | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Microsoft.MixedReality | [Azure Spatial Anchors](../../spatial-anchors/index.yml) |
| Microsoft.NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [Gateway de Aplicação](../../application-gateway/index.yml)<br />[Azure Bastion](../../bastion/index.yml)<br />[Azure DDoS Protection](../../ddos-protection/ddos-protection-overview.md)<br />[DNS do Azure](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Azure Firewall](../../firewall/index.yml)<br />[Azure Front Door Service](../../frontdoor/index.yml)<br />[Azure Private Link](../../private-link/index.yml)<br />[Balanceador de Carga](../../load-balancer/index.yml)<br />[Observador de Rede](../../network-watcher/index.yml)<br />[Gestor de Tráfego](../../traffic-manager/index.yml)<br />[Rede Virtual](../../virtual-network/index.yml)<br />[WAN Virtual](../../virtual-wan/index.yml)<br />[Gateway de VPN](../../vpn-gateway/index.yml)<br /> |
| Microsoft.Notebooks | [Azure Notebooks](https://notebooks.azure.com/help/introduction) |
| Microsoft.NotificationHubs | [Hubs de Notificação](../../notification-hubs/index.yml) |
| Microsoft.ObjectStore | Loja de Objetos |
| Microsoft.OffAzure | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Microsoft.OperationalInsights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.Peering | [Azure Peering Service](../../peering-service/index.yml) |
| Microsoft.PolicyInsights | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft.Portal - [registado](#registration) | [Portal do Azure](../../azure-portal/index.yml) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft.PowerPlatform | [Power Platform](/power-platform/) |
| Microsoft.ProjectBabylon | [Catálogo de Dados do Azure](../../data-catalog/overview.md) |
| Microsoft.Quantum | [Azure Quantum](https://azure.microsoft.com/services/quantum/) |
| Microsoft.RecoveryServices | [Azure Site Recovery](../../site-recovery/index.yml) |
| Microsoft.RedHatopenShift | [Azure Red Hat OpenShift](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft.Relay | [Reencaminhamento do Azure](../../azure-relay/relay-what-is-it.md) |
| Microsoft.ResourceGraph - [registado](#registration) | [Azure Resource Graph](../../governance/resource-graph/index.yml) |
| Microsoft.ResourceHealth | [Azure Service Health](../../service-health/index.yml) |
| Microsoft.Resources - [registado](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft.SaaS | núcleo |
| Microsoft.Scheduler | [Scheduler](../../scheduler/index.yml) |
| Microsoft.Search | [Azure Cognitive Search](../../search/index.yml) |
| Microsoft.Security | [Centro de Segurança](../../security-center/index.yml) |
| Microsoft.SecurityInsights | [Azure Sentinel](../../sentinel/index.yml) |
| Microsoft.SerialConsole - [registado](#registration) | [Consola em série Azure para Windows](../../virtual-machines/troubleshooting/serial-console-windows.md) |
| Microsoft.ServiceBus | [Service Bus](/azure/service-bus/) |
| Microsoft.ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft.ServiceFabricMesh | [Service Fabric Mesh](../../service-fabric-mesh/index.yml) |
| Microsoft.Services | núcleo |
| Microsoft.SignalRService | [Serviço SignalR do Azure](../../azure-signalr/index.yml) |
| Microsoft.SoftwarePlan | Licença |
| Microsoft.Solutions | [Aplicações Geridas do Azure](../managed-applications/index.yml) |
| Microsoft.Sql | [Base de Dados SQL do Azure](../../azure-sql/database/index.yml)<br /> [Instância Gerida do SQL no Azure](../../azure-sql/managed-instance/index.yml) <br />[Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.SqlVirtualMachine | [SQL Server nas Máquinas Virtuais do Azure](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) |
| Microsoft.Storage | [Armazenamento](../../storage/index.yml) |
| Microsoft.StorageCache | [Azure HPC Cache](../../hpc-cache/index.yml) |
| Microsoft.StorageSync | [Armazenamento](../../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](../../storsimple/index.yml) |
| Microsoft.StreamAnalytics | [Azure Stream Analytics](../../stream-analytics/index.yml) |
| Microsoft.Subscrição | núcleo |
| microsoft.support - [registrado](#registration) | núcleo |
| Microsoft.Synapse | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.TimeSeriesInsights | [Azure Time Series Insights](../../time-series-insights/index.yml) |
| Microsoft.Token | Token |
| Microsoft.VirtualMachineImages | [Azure Image Builder](../../virtual-machines/image-builder-overview.md) |
| microsoft.visualstudio | [Azure DevOps](/azure/devops/) |
| Microsoft.VMware | [Solução VMware no Azure](../../azure-vmware/index.yml) |
| Microsoft.VMwareCloudSimple | [Azure VMware Solution by CloudSimple](../../vmware-cloudsimple/index.md) |
| Microsoft.VSOnline | [Azure DevOps](/azure/devops/) |
| Microsoft.Web | [Serviço de Aplicações](../../app-service/index.yml)<br />[Funções do Azure](../../azure-functions/index.yml) |
| Microsoft.WindowsDefenderATP | [Proteção Avançada Contra Ameaças do Microsoft Defender](../../security-center/security-center-wdatp.md) |
| Microsoft.WindowsESU | Atualizações de segurança alargadas |
| Microsoft.WindowsIoT | [Windows 10 IoT Core Services](/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft.WorkloadMonitor | [Azure Monitor](../../azure-monitor/index.yml) |

## <a name="registration"></a>Registo

Os fornecedores de recursos acima com os que estão marcados **- registados** são registados por padrão para a sua subscrição. Para utilizar os outros fornecedores de recursos, tem de [os registar](resource-providers-and-types.md). No entanto, muitos fornecedores de recursos estão registados para si quando toma determinadas ações. Por exemplo, se criar um recurso através do portal, o portal regista automaticamente quaisquer fornecedores de recursos não registados que sejam necessários. Ao utilizar recursos através de um [modelo de Gestor de Recursos Azure,](../templates/overview.md)todos os fornecedores de recursos necessários também estão registados.

> [!IMPORTANT]
> Só registe um fornecedor de recursos quando estiver pronto para o utilizar. O passo de registo permite-lhe manter os privilégios mínimos dentro da sua subscrição. Um utilizador malicioso não pode usar fornecedores de recursos que não estejam registados.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os fornecedores de recursos, incluindo como registar um fornecedor de recursos, consulte [os fornecedores e tipos de recursos Azure](resource-providers-and-types.md).