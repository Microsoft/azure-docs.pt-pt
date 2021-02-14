---
title: Apoio à deslocação de recursos do Azure pelas regiões
description: Lista os tipos de recursos Azure que podem ser movidos através das regiões de Azure
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 18d4d84462d528b718d784ff6a16ecf990ed0d20
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100094020"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Apoio à deslocação de recursos do Azure pelas regiões

Este artigo confirma se um tipo de recurso Azure é suportado para se deslocar para outra região de Azure. 

Salte para um espaço de nome de fornecedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DbforPostgresql](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Geómica](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.Purview](#microsoftpurview)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatopenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscrição](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | serviços de domínio | Não | 


## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | diagnósticos | Não |
> | diagnósticoscategorias | Não |
> | privatelinkforazuread | Não |
> | inquilinos |  Não |

## <a name="microsoftaddons"></a>microsoft. Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | supportproviders | Não |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | aadsupportcases | Não |
> | adds serviços | Não | 
> | agentes | Não | 
> | anónimos | Não |
> | configuração | Não | 
> | registos | Não | 
> | relatórios | Não | 
> | serviços de saúde | Não | 
> | services | Não | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | configurações | Não | 
> | gerar recommendações | Não |
> | do IdP | Não |
> | recomendações | Não |
> | supressões | Não | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | regras de ação | Não | 
> | alerts | Não | 
> | lista de alertas | Não | 
> | alertasmetadata | Não | 
> | alertasummary | Não | 
> | alertasummarylista | Não | 
> | smartdetectoralertrules | Não | 
> | grupos inteligentes | Não | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | servidores | Não |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | reportfeedback | Não |
> | serviço |  Sim (usando o modelo) <br/><br/> [Mover a API Management através das regiões.](../../api-management/api-management-howto-migrate.md) | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | lojas de configurações | Não | 
> | lojas de configurações / filtros de eventos | Não |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | primavera | Não | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | apiapps | Sim (usando o modelo)<br/><br/> [Mover uma app de Serviço de Aplicações para outra região](../../app-service/manage-move-across-regions.md) | 
> | appidentidades | Não | 
> | portais | Não | 

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | atesstationproviders | Não | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | classicadministradores | Não | 
> | dataaliases | Não | 
> | denyassignments | Não | 
> | elevateaccess | Não | 
> | findorphanroleassignments | Não | 
> | fechaduras | Não | 
> | permissões | Não | 
> | assinaturas políticas | Não | 
> | definições de políticas | Não | 
> | políticas dedefinições | Não | 
> | privatelinkassociations | Não | 
> | recursos gestagementprivatelinks | Não | 
> | roleassignments | Não | 
> | roleassignmentsusagemetrics | Não | 
> | papéis dedefinições | Não | 

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | contas de automação | Sim (usando o modelo) <br/><br/> [Utilização de geo-replicação](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | contas/configurações de automação | Não | 
> | contas de automação / runbooks | Não | 

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | Subscrição |
> | ------------- | ----------- | 
> | nuvens privadas | Não | 


## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | b2direões | Não | 
> | b2ctenants | Não | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | datacontrollers | Não | 
> | híbridos gerentes | Não | 
> | pós-insinstâncias | Não | 
> | sqlinstances | Não | 
> | sqlmanagedinstances | Não |
> | sqlserverinstances | Não | 
> | sqlserverregistrations | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | cloudmanifestfiles | Não |
> | registos | Não | 

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | aglomerados | Não | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | lotaccounts |  As contas do lote não podem ser movidas diretamente de uma região para outra, mas você pode usar um modelo para exportar um modelo, modificá-lo e implementar o modelo para a nova região. <br/><br/> Saiba como [mover uma conta batch através de regiões](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | contas de faturação | Não | 
> | faturantes | Não | 
> | faturações | Não | 
> | billingproperty | Não | 
> | billingroleassignments | Não | 
> | billingroledefinitions | Não | 
> | departamentos | Não | 
> | matrículas | Não | 
> | faturas | Não | 
> | transferências | Não | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | mapapis | Não | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | biztalk | Não | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | membros blockchain | Não <br/><br/> A rede blockchain não pode ter nós em diferentes regiões. 
> | cordamembers | Não |
> | observadores | Não | 

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | serviços de token | Não |


## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | blueprintassignments | Não | 
> | plantas | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | botservices | Não | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | redis | Não | 
> | redisenterprise | Não | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | reservações aplicadas | Não | 
> | calcular a troca | Não | 
> | calcular preço | Não | 
> | calcular o preço da compra | Não | 
> | catálogos | Não | 
> | ordenadores de reservação comercial | Não | 
> | troca | Não |
> | encomendas de reservas | Não | 
> | reservas | Não | 
> | resources | Não | 
> | validação encomenda de reservas | Não | 

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Não |
> | edgenodes | Não
> | perfis | Não | 
> | perfis / pontos finais | Não | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | certificadores | Não | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | capacidades | Não | 
> | temas de domínio | Sim | Não |
> | quotas | Não | 
> | recursos | Não |
> | valida a subscrição demovedabilidade | Não | 
> | virtualmachines | Não 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | classicinfrastructureresources | Não | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | capacidades | Não | 
> | expressroute crossconnections | Não | 
> | expressroutecrossconnections / peerings | Não | 
> | gatewaysupporteddes | Não | 
> | networksecuritygroups | Não |
> | quotas | Não |
> | reservedips | Não | 
> | virtualnetworks | Não | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | discos | Não | 
> | imagens | Não | 
> | osimagems | Não | 
> | osplatformimages | Não | 
> | imagens públicas | Não | 
> | quotas | Não | 
> | contas de armazenamento | Sim |  
> | vmimages | Não |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | operações | Não | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | Não | 
> | Cognitive Search | Suportado com passos manuais.<br/><br/> Saiba como [mover o seu serviço de Pesquisa Cognitiva Azure para outra região](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | cartão de taxa | Não | 
> | usageaggregates | Não | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | conjuntos de disponibilidades | Sim <br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover conjuntos de disponibilidade. | 
> | diskaccesses | Não |
> | conjuntos de diskencrypations | Não | 
> | discos | Sim <br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover VMs Azure e discos relacionados. | 
> | galerias | Não | 
> | galerias / imagens | Não | 
> | galerias / imagens / versões | Não | 
> | grupos de anfitriões | Não | 
> | grupos de anfitriões / anfitriões | Não | 
> | imagens | Não | 
> | grupos de destacamentos de proximidade | Não | 
> | restaurar as recordações de pontos | Não | 
> | sharedvmimages | Não | 
> | sharedvmimages / versões | Não | 
> | instantâneos | Não | 
> | sshpublickeys | Não |
> | virtualmachines | Sim <br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover VMs Azure. | 
> | virtualmachines /extensões | Não | 
> | virtualmachinescalesets | Não | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | agregados de cussão | Não | 
> | saldos | Não | 
> | orçamentos | Não | 
> | encargos | Não | 
> | costtags | Não | 
> | créditos | Não | 
> | eventos | Não | 
> | previsões | Não | 
> | lotes | Não | 
> | mercados | Não | 
> | folhas de preços | Não | 
> | produtos | Não | 
> | detalhes de reservas | Não | 
> | reservas recommendaçõesdedetails | Não | 
> | reservas recommendações | Não | 
> | reservas | Não | 
> | transações de reservas | Não | 
> | etiquetas | Não | 
> | inquilinos | Não | 
> | termos | Não | 
> | usagedetails | Não | 


## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | grupos de contentores | Não | 
> | ligações de serviços de ações | Não |


## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | registos | Não |  
> | registos / agentpools | Não | 
> | registos / buildtasks | Não |  
> | registos / replicações | Não | 
> | registos / tarefas | Não |  
> | registos / webhooks | Não | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | serviços de contentores | Não |
> | geremclusters | Não | 
> | aglomerados de gestão de turnos abertos | Não | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aplicações | Não | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | alerts | Não | 
> | contas de faturação | Não | 
> | orçamentos | Não | 
> | cloudconnectors | Não | 
> | conectores | Não | 
> | departamentos | Não | 
> | dimensões | Não | 
> | matrículas | Não | 
> | exportações | Não | 
> | contas de falcão externos | Não | 
> | forecast | Não | 
> | consulta | Não | 
> | registar | Não | 
> | configs relatório | Não | 
> | relatórios | Não | 
> | definições | Não | 
> | showbackrules | Não | 
> | Modos de exibição | Não | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | hubs | Não |  

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | pedidos | Não | 

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | associações | Não |
> | recursosproviders | Não | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | empregos | Não | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | availableskus | Não |
> | databoxedgedevices | Não | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | áreas de trabalho | Não | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | catálogos | Não | 
> | datacatalogs | Não | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | gerentes de conexão | Não | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | pacotes | Não | 
> | planos | Não | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | datafactories | Não | 
> | fábricas | Não |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | datalakeaccounts | Não | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | services | Não | 
> | serviços / projetos | Não | 
> | slots | Não | 

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | ---------- |
> | backupvaults | Não | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | servidores | Pode utilizar uma réplica de leitura transversal para mover um servidor existente. [Saiba mais](../../postgresql/howto-move-regions-portal.md).<br/><br/> Se o serviço for aloque-se com armazenamento de backup geo-redundante, pode utilizar o geo-restauro para restaurar noutras regiões. [Saiba mais](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage).

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | servidores | Pode utilizar uma réplica de leitura transversal para mover um servidor existente. [Saiba mais](../../mysql/howto-move-regions-portal.md).

## <a name="microsoftdbforpostgresql"></a>Microsoft.DbforPostgresql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | grupos de servidores | Não | 
> | servidores | Pode utilizar uma réplica de leitura transversal para mover um servidor existente. [Saiba mais.](../../postgresql/howto-move-regions-portal.md)
> | servidorv2 | Não | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | artefactos | Não | 
> | rollouts | Não |  
> | topologias de serviço | Não | 
> | topologias de serviços / serviços | Não |  
> | topologias de serviços / serviços / unidades de serviço | Não | 
> | passos | Não | 


## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | grupos de aplicações | Não | 
> | áreas de trabalho | Não | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | elásticos | N.º O recurso não está exposto.
> | elásticos / iothubtenants | N.º O recurso não está exposto.
> | iothubs | Sim. [Saiba mais](../../iot-hub/iot-hub-how-to-clone.md)
> | serviços de provisionamento | Não | 

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | controladores | Não | 


## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | controladores | Não | 
> | Aglomerado de AKS | Não<br/><br/> [Saiba mais](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) sobre a mudança para outra região.

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | centros de laboratório | Não | 
> | laboratórios | Não | 
> | laboratórios / ambientes | Não |  
> | laboratórios / corredores de serviço | Não | 
> | laboratórios / virtualmachines | Não |  
> | horários | Não |  

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | digitaltwinsinstances | Sim, recriando recursos em nova região. [Saiba mais](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | contas de base de dados | Não | 
> | contas de base de dados | Não | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | domínios | Não | 
> | gerassorequest | Não | 
> | topleveldomains | Não | 
> | validação da informação sobreregistration | Não |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | services | Não |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | domínios | Não | 
> | subscrições de eventos | Não |
> | extensõestópicas | Não | 
> | partnernamespaces | Não | 
> | registos de parceiros | Não | 
> | partnertopics | Não | 
> | sistematópicos | Não | 
> | tópicos | Não | 
> | tópicos | Não | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aglomerados | Não |  
> | espaços de nome | Sim (com modelo)<br/><br/> [Mover um espaço de nomes do Event Hub para outra região](../../event-hubs/move-across-regions.md) | 
> | sku | Não |  

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | espaços de trabalho experimenta | Não | 

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | espaços de nome | Não | 

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | recursosproviders | Não | 
> | funcionalidades | Não | 
> | fornecedores | Não | 
> | subscrições de registos de assinaturas | Não | 

## <a name="microsoftgenomics"></a>Microsoft.Geómica

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | auto-administradores | Não | 
> | autogeridavmconfigurationprofils | Não | 
> | convidadoconfigurationasssignments | Não | 
> | software | Não | 
> | softwareupdateprofile | Não | 
> | datas de software | Não | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | hanainstances | Não | 
> | sapmonitors | Não |  

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | dedicados | Não | 


## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aglomerados | Não | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | services | Não |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | máquinas | Não | 
> | máquinas / extensões | Não |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | datamanagers |  Não | 

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | dispositivos | Não | 
> | vnfs | Não | 

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | componentes | Não | 
> | networkscopes | Não | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | empregos |  Não | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | N.º [Saiba mais](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region).
> | grupos de ação |  Não | 
> | activitylogalerts | Não | 
> | alertas |  Não | 
> | autoescala |  Não | 
> | linha de base | Não |
> | componentes |  Não |  
> | datacollectionrules | Não | 
> | diagnósticos | Não | 
> | diagnósticoscategorias | Não | 
> | eventcategorias | Não | 
> | tipos de eventos | Não | 
> | diagnósticosgnósticos estendidos | Não | |
> | guestdiagnosticsettings | Não | 
> | listmigrationdate | Não | 
> | início de ções | Não | 
> | logprofiles | Não | 
> | registos | Não | Não |
> | metricalerts | Não | 
> | bases métricas | Não | 
> | metricbatch | Não | 
> | métricas | Não | 
> | espaços de nomes métricos | Não | 
> | metrics | Não | 
> | migratealertrules | Não |
> | migrar modelo dericing | Não | 
> | livros de trabalho | Não |
> | grupos de notificações | Não | 
> | privatelinkscopes | Não |
> | modelo de backbacktolegacypricing | Não |
> | queryrules agendados |  Não | 
> | topology | Não |
> | transações | Não |
> | vminsightsonboardingstatuses | Não |
> | webtests |  Não | 
> | webtests / obtertestesultfile | Não |
> | livros |  Não |  
> | estalagens de livros | Não |


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | apliques | Não | 
> | iotapps | Não | 



## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> |  Iothub |  Sim (centro clone) <br/><br/> [Clone um hub IoT para outra região](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da Região |
> | ------------- | ----------- | 
> | gráfico | Não | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | deletedvaults | Não |
> | hsmpools | Não | 
> | geredhsmos | Não |
> | cofres |  Não | 

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | aglomerados conectados | Não | 
> | assinaturas registadas | Não | 

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | configurações de controlo de fontes | Não | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aglomerados |  Não |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | contas de laboratório | Não | 
> | utilizadores | Não | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | Não, é um serviço global.

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | hospedar ambientes | Não | 
> | contas de integração |  Não |  
> | integração de ambientes | Não | 
> | integração de ambientesvírus /managedapis | Não |
> | ambientes isolados | Não | 
> | fluxos de trabalho |  Não |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | planos de compromisso |  Não | 
> | serviços web |  Não | 
> | áreas de trabalho |  Não | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | grupos de operacionalização |  Não | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | Não | 
> | contas de equipa | Não | 


## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | Não | 


## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | áreas de trabalho | Não | 

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | configurações de assinaturas | Sim. [Saiba mais](../../virtual-machines/move-region-maintenance-configuration.md) | 
> | configurações de manutenção | Sim. [Saiba mais](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | atualizações | Não | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | identidades | Não | 
> | usassignedidentidades | Não | 

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | managednetworks | Não | 
> | gerednetworks / grupos de redes geridas | Não |
> | managednetworks / managednetworkpeeringpolicies | Não | 
> | notificação | Não | 

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | marketplaceregistrationdefinitions | Não | 
> | inscrições | Não |
> | definições de registos | Não | 

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | getentities | Não | 
> | managementgroups | Não | 
> | grupos de gestão / configurações | Não | 
> | resources | Não | 
> | filtro de backback starttenant | Não | 
> | inquilinosfillstatus | Não | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts |  Não, a Azure Maps é um serviço geoespacial. 
> | contas / privateatlases | Não

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | ofertas | Não | 
> | tipos de ofertas | Não | 
> | privadas | Não | 
> | privatestoreclient | Não | 
> | lojas privadas | Não | 
> | produtos | Não | 
> | editores | Não | 
> | registar | Não | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | classicdevservices | Não | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | acordos | Não | 
> | tipos de ofertas | Não | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | mediaservices |  Não | 
> | mediaservices / liveevents |  Não | 
> | mediaservices / streamingendpoints |  Não | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | apclusters | Não | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | projetos de avaliação | Não | 
> | migrarprojects | Não | 
> | câmaras de mudança | Não
> | projetos | Não | 

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | ---------- |
> | holográficosbroscastaccounts | Não | 
> | contas objectunderstanding | Não | 
> | contas de reconeradores remotos | Não | 
> | spatialanchorsaccounts | Não | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | contas netappac | Não | 
> | contas de netappacs /capacitypools | Não | 
> | contas de netappacs / capacitypools / volumes | Não | 
> | contas netappaccounts / capacitypools / volumes / mounttargets | Não | 
> | contas de netappacs / capacitypools / volumes / snapshots | Não | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | pistas de aplicação | Não |
> | applicationgatewaywebapplicationfirewallpolicies | Não | 
> | grupos de aplicações |  Não |  
> | azurefirewalls |  Não |  
> | bastionhosts | Não | 
> | bgpservicecommunidades | Não |
> | conexões |  Não | 
> | ddoscustompolicias |  Não | 
> | ddosprotectionplans | Não | 
> | dnszones |  Não | 
> | expressroutecircuits | Não | 
> | vias expressas | Não | 
> | expressrouteserviceproviders | Não | 
> | firewallpolicies | Não |
> | frontdoors | Não | 
> | ipallocations | Não |
> | ipgrupos | Não |
> | loadbalancers | Sim <br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover os equilibradores de carga internos e externos. |
> | vias de rede locais |  Não | 
> | natgateways |  Não | 
> | projetos de bioperiment de rede | Não |
> | políticas de networkintent |  Não | 
> | networkinterfaces | Sim <br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover NICs. | 
> | redes de mentofils | Não | 
> | networksecuritygroups | Sim <br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover grupos de segurança de rede (NGSs). | 
> | observadores de rede |  Não |  
> | observadores de rede / suportes de conexão |  Não | 
> | networkwatchers / fluxologs |  Não | 
> | observadores de rede / pingmeshes |  Não | 
> | p2svpngateways | Não | 
> | zonas privadas |  Não |  
> | privatednszones / virtualnetworklinks | Não |> | privatednszonesinternal | Não |
> | privateendpointredirectmaps | Não |
> | pontos privados | Não | 
> | serviços de privatelink | Não | 
> | endereços publicitários | Sim<br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover endereços IP públicos. |
> | publicipprefixos | Não | 
> | filtros de rota | Não | 
> | routetables |  Não | 
> | segurançapartnerproviders | Não |
> | serviceendpointpolicies |  Não | 
> | trafficmanagergeographichierarchies | Não | 
> | trafficmanagerprofiles |  Não | 
> | trafficmanagerusermetricskeys | Não |
> | virtualhubs | Não | 
> | vias virtualnetworkgateways |  Não |  
> | virtualnetworks |  Não | 
> | virtualnetworktaps | Não | 
> | virtualwans | Não | 
> | VPNgateways (Wan Virtual) | Não | 
> | vpnsites (WAN virtual) | Não | 
> | vpnsites (WAN virtual) | Não |


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | espaços de nome |  Não | 
> | espaços de nomes / notificações |  Não |  

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | osnamepas | Não | 

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | hipervsites | Não | 
> | importações | Não | 
> | servidores | Não | 
> | vmwaresites | Não | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aglomerados | Não | 
> | espaços de trabalho apagados | Não | 
> | linktargets | Não | 
> | storageinsightconfigs | Não |
> | áreas de trabalho | Não |



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | sociedades de gestão | Não |
> | configurações de gestão |  Não | 
> | soluções | Não |
> | Modos de exibição |  Não | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | legacypeerings | Não | 
> | peerasns | Não | 
> | peeringlocações | Não | 
> | espreiteiros | Não | 
> | países de serviço de peering | Não | 
> | localização de serviços de espre éming | Não | 
> | peeringserviceproviders | Não | 
> | serviços de espreiteiros | Não | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | eventos de política | Não | 
> | estados de política | Não | 
> | recursos de recursos de pista de política | Não | 
> | remediações | Não | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | Consolas do | Não |
> | dashboards | Não | 
> | utilizadores | Não | 


## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | trabalhopascoscollections |  Não | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | capacidades |  Não | 

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | contas disponíveis | Não | 
> | registos de fornecedores | Não | 
> | rollouts | Não | 

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | áreas de trabalho | Não | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | replicationeligibilityres | Não |
> | cofres | N.º<br/><br/> A movimentação de cofres dos Serviços de Recuperação para o Azure Backup em todas as regiões de Azure não é suportada.<br/><br/> Nos cofres dos Serviços de Recuperação para recuperação do local de Azure, pode [desativar e recriar o cofre](../../site-recovery/move-vaults-across-regions.md) na região alvo. | 

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatopenShift

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | aglomerados de turnos abertos | Não | 

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | espaços de nome |  Não | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | consultas |  Não |  
> | resourcechangedetails | Não | 
> | mudanças de recursos | Não | 
> | resources | Não | 
> | recursos história | Não | 
> | subscriçõesstatus | Não | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | fontes infantis | Não | 
> | emergentes | Não | 
> | eventos | Não | 
> | do IdP | Não | 
> | notificações | Não | 

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região |
> | ------------- | ----------- |
> | implementaçõesScripts |  Sim<br/><br/>[Mover recursos da Microsoft.Resources para uma nova região](microsoft-resources-move-regions.md) |
> | modeloSpecs |  Sim<br/><br/>[Mover recursos da Microsoft.Resources para uma nova região](microsoft-resources-move-regions.md) |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aplicações |  Não | 
> | saasresources | Não | 


## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | resourcehealthmetadata | Não |
> | serviços de pesquisa |  Não | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | adaptíostas de redes | Não | 
> | advancedthreatprotectionsettings | Não | 
> | alerts | Não | 
> | permitidas ligações | Não | 
> | listas de aplicaçõeswhitelistings | Não | 
> | avaliaçãometadata | Não | 
> | avaliações | Não | 
> | autodismissalertsrules | Não | 
> | automações | Não | 
> | autoprovisionings | Não |
> | resultados de conformidade | Não | 
> | conformidades | Não | 
> | datacollectionagentes | Não | 
> | grupos de dispositivos de insegurança | Não | 
> | descobertas sões de inseguranças | Não | 
> | sões de segurança externa | Não | 
> | políticas de proteção de informação | Não | 
> | soluções de iotsecuritys | Não | 
> | iotsecuritysolutions / analyticsmodels | Não | 
> | iotsecuritysolutions / analyticsmodels / agregados | Não | 
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Não | 
> | jitnetworkacsspolicies | Não | 
> | políticas | Não | 
> | preços | Não | 
> | padrões de regulação | Não | 
> | padrões de regulação /regulatórios | Não | 
> | padrões de regulação / regulatórios de controlo /regulatórios | Não | 
> | contactos de segurança | Não | 
> | garantias de segurança | Não | 
> | securitysolutionsreferencedata | Não | 
> | estados de segurança | Não | 
> | securitystatusessummaries | Não | 
> | servidorvulnerabilityaments | Não | 
> | definições | Não | 
> | subassessments | Não |
> | tarefas | Não | 
> | topologias | Não | 
> | espaços de trabalho | Não | 

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | agregações | Não | 
> | alertas | Não | 
> | alertaruletemplas | Não | 
> | regras automation | Não |
> | casos | Não | 
> | dataconnectors | Não | 
> | entidades | Não | 
> | entidadequeries | Não |
> | Incidentes | Não | 
> | escritórioconsents | Não | 
> | definições | Não | 
> | ameaçaintelligence | Não | 

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | serviços de consola | Não | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | portais | Não | 
> | osdes | Não | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | espaços de nome |  Não | 
> | premiummessaging regiões | Não | 
> | sku | Não | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aplicações | Não | 
> | aglomerados |  Não |  
> | grupos de contentores | Não | 
> | conjuntos de contentores | Não | 
> | edgeclusters | Não | 
> | geremclusters | Não |
> | redes | Não | 
> | lojas secretas | Não | 
> | volumes | Não | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aplicações |  Não | 
> | grupos de contentores | Não | 
> | portais |  Não | 
> | redes |  Não | 
> | segredos |  Não | 
> | volumes |  Não |  

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | rollouts | Não | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | sinaleiro |  Não |  

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | híbridosbenefits | Não | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aparelhos dedefinições | Não | 
> | aparelhos | Não | 
> | jitrequests | Não | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | instâncias | Não | 
> | Locais | Não |
> | managedinstances | Sim <br/><br/> [Saiba mais](../../azure-sql/database/move-resources-across-regions.md) sobre a mudança de instâncias geridas em todas as regiões. | 
> | managedinstances / bases de dados | Sim | 
> | servidores | Sim | 
> | servidores / bases de dados | Sim <br/><br/> [Saiba mais](../../azure-sql/database/move-resources-across-regions.md) sobre a mudança de bases de dados em regiões.<br/><br/> [Saiba mais](../../resource-mover/tutorial-move-region-sql.md) sobre a utilização do Azure Resource Mover para mover bases de dados Azure SQL.  | 
> | servidores / elásticos | Sim <br/><br/> [Saiba mais](../../azure-sql/database/move-resources-across-regions.md) sobre a movimentação de piscinas elásticas em regiões.<br/><br/> [Saiba mais](../../resource-mover/tutorial-move-region-sql.md) sobre a utilização do Azure Resource Mover para mover piscinas elásticas Azure SQL.  | 
> | virtualclusters | Sim | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | grupos sqlvirtualmachineinegroups |  Não |  
> | sqlvirtualmachines |  Não |  


## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | contas de armazenamento | Sim<br/><br/> [Mover uma conta de Armazenamento Azure para outra região](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | caches | Não | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | storagesyncservices |  Não | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | storagesyncservices | Não | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | storagesyncservices | Não | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | gestores | Não | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aglomerados | Não |
> | streamingjobs |  Não |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | ambientes | Não | 
> | casos | Não | 

## <a name="microsoftsubscription"></a>Microsoft.Subscrição

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | assinaturas | Não | 

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | services | Não | 
> | bilhetes de apoio | Não | 

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | áreas de trabalho | Não | 
> | espaços de trabalho / bigdatapools | Não | 
> | espaços de trabalho / sqlpools | Não | 


## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | ambientes |  Não | 
> | ambientes / fontes de eventos |  Não |  
> | ambientes / conjuntos de dados de referência |  Não | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | lojas | Não | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | imagens | Não | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | conta |  Não | 
> | conta / extensão |  Não | 
> | conta / projeto |  Não | 

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | arczones | Não | 
> | recursos | Não | 
> | vcenters | Não | 
> | virtualmachines | Não | 
> | virtualmachinetemplates | Não | 
> | virtualnetworks | Não | 

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | não-100 dedicados | Não | 
> | serviços decloud dedicados | Não | 
> | virtualmachines | Não | 

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | dispositivos | Não | 
> | vnfs | Não | 

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | accounts | Não | 
> | planos | Não | 
> | assinaturas registadas | Não |


## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | disponíveis mochilas | Não | 
> | billingmeters | Não | 
> | certificados | Não | 
> | vias de conexão |  Não |  
> | conexões |  Não |  
> | customapis |  Não | 
> | deletedsites | Não | 
> | implantações | Não | 
> | geo-regiões | Não | 
> | hospedar ambientes | Não | 
> | kubeenvironments | Não | 
> | editores | Não |
> | recomendações | Não | 
> | resourcehealthmetadata | Não | 
> | tempos de execução | Não | 
> | serverfarms | Não |  
> | serverfarms / eventgridfilters | N
> | sites |  Não | 
> | sites / premieraddons |  Não |  
> | sites / slots |  Não |  
> | controlos de fontes | Não |
> | estáticasites | Não | 

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | multipleactivationkeys | Não |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | serviços de dispositivos | Não | 

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | cargas de trabalho | Não | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | componentes | Não |
> | consumada de componentes | Não | 
> | monitorinstâncias | Não | 
> | monitorinstancessummary | Não | 
> | monitores | Não | 
## <a name="third-party-services"></a>Serviços de terceiros

Os serviços de terceiros não suportam atualmente a operação de mudança.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](../../resource-mover/overview.md) sobre o serviço Demôr Recursos.

