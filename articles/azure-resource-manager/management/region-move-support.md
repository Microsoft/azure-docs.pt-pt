---
title: Apoio à deslocação de recursos do Azure pelas regiões
description: Lista os tipos de recursos Azure que podem ser movidos através das regiões de Azure
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 49c5828e02bf96a536ff14f6b84e81f7adbe3090
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806893"
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
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
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
> | serviços de domínio | No | 


## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | diagnósticos | No |
> | diagnósticoscategorias | No |
> | privatelinkforazuread | No |
> | inquilinos |  No |

## <a name="microsoftaddons"></a>microsoft. Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | supportproviders | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | aadsupportcases | No |
> | adds serviços | No | 
> | agentes | No | 
> | anónimos | No |
> | configuração | No | 
> | registos | No | 
> | relatórios | No | 
> | serviços de saúde | No | 
> | services | No | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | configurações | No | 
> | gerar recommendações | No |
> | do IdP | No |
> | recomendações | No |
> | supressões | No | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | regras de ação | No | 
> | alerts | No | 
> | lista de alertas | No | 
> | alertasmetadata | No | 
> | alertasummary | No | 
> | alertasummarylista | No | 
> | smartdetectoralertrules | No | 
> | grupos inteligentes | No | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | servidores | No |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | reportfeedback | No |
> | serviço |  Sim (usando o modelo) <br/><br/> [Mover a API Management através das regiões.](../../api-management/api-management-howto-migrate.md) | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | lojas de configurações | No | 
> | lojas de configurações / filtros de eventos | No |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | primavera | No | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | apiapps | Sim (usando o modelo)<br/><br/> [Mover uma app de Serviço de Aplicações para outra região](../../app-service/manage-move-across-regions.md) | 
> | appidentidades | No | 
> | portais | No | 

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | atesstationproviders | No | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | classicadministradores | No | 
> | dataaliases | No | 
> | denyassignments | No | 
> | elevateaccess | No | 
> | findorphanroleassignments | No | 
> | fechaduras | No | 
> | permissões | No | 
> | assinaturas políticas | No | 
> | definições de políticas | No | 
> | políticas dedefinições | No | 
> | privatelinkassociations | No | 
> | recursos gestagementprivatelinks | No | 
> | roleassignments | No | 
> | roleassignmentsusagemetrics | No | 
> | papéis dedefinições | No | 

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | contas de automação | Sim (usando o modelo) <br/><br/> [Utilização de geo-replicação](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | contas/configurações de automação | No | 
> | contas de automação / runbooks | No | 

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | Subscrição |
> | ------------- | ----------- | 
> | nuvens privadas | No | 


## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | b2direões | No | 
> | b2ctenants | No | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | datacontrollers | No | 
> | híbridos gerentes | No | 
> | pós-insinstâncias | No | 
> | sqlinstances | No | 
> | sqlmanagedinstances | No |
> | sqlserverinstances | No | 
> | sqlserverregistrations | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | cloudmanifestfiles | No |
> | registos | No | 

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | aglomerados | No | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | lotaccounts |  As contas do lote não podem ser movidas diretamente de uma região para outra, mas você pode usar um modelo para exportar um modelo, modificá-lo e implementar o modelo para a nova região. <br/><br/> Saiba como [mover uma conta batch através de regiões](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | contas de faturação | No | 
> | faturantes | No | 
> | faturações | No | 
> | billingproperty | No | 
> | billingroleassignments | No | 
> | billingroledefinitions | No | 
> | departamentos | No | 
> | matrículas | No | 
> | faturas | No | 
> | transferências | No | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | mapapis | No | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | biztalk | No | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | membros blockchain | No <br/><br/> A rede blockchain não pode ter nós em diferentes regiões. 
> | cordamembers | No |
> | observadores | No | 

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | serviços de token | No |


## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | blueprintassignments | No | 
> | plantas | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | botservices | No | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | redis | No | 
> | redisenterprise | No | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | reservações aplicadas | No | 
> | calcular a troca | No | 
> | calcular preço | No | 
> | calcular o preço da compra | No | 
> | catálogos | No | 
> | ordenadores de reservação comercial | No | 
> | troca | No |
> | encomendas de reservas | No | 
> | reservas | No | 
> | resources | No | 
> | validação encomenda de reservas | No | 

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | No |
> | edgenodes | No
> | perfis | No | 
> | perfis / pontos finais | No | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | certificadores | No | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | capacidades | No | 
> | temas de domínio | Yes | No |
> | quotas | No | 
> | recursos | No |
> | valida a subscrição demovedabilidade | No | 
> | virtualmachines | No 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | classicinfrastructureresources | No | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | capacidades | No | 
> | expressroute crossconnections | No | 
> | expressroutecrossconnections / peerings | No | 
> | gatewaysupporteddes | No | 
> | networksecuritygroups | No |
> | quotas | No |
> | reservedips | No | 
> | virtualnetworks | No | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | discos | No | 
> | imagens | No | 
> | osimagems | No | 
> | osplatformimages | No | 
> | imagens públicas | No | 
> | quotas | No | 
> | contas de armazenamento | Yes |  
> | vmimages | No |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | operações | No | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | No | 
> | Cognitive Search | Suportado com passos manuais.<br/><br/> Saiba como [mover o seu serviço de Pesquisa Cognitiva Azure para outra região](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | cartão de taxa | No | 
> | usageaggregates | No | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | conjuntos de disponibilidades | Yes <br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover conjuntos de disponibilidade. | 
> | diskaccesses | No |
> | conjuntos de diskencrypations | No | 
> | discos | Yes <br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover VMs Azure e discos relacionados. | 
> | galerias | No | 
> | galerias / imagens | No | 
> | galerias / imagens / versões | No | 
> | grupos de anfitriões | No | 
> | grupos de anfitriões / anfitriões | No | 
> | imagens | No | 
> | grupos de destacamentos de proximidade | No | 
> | restaurar as recordações de pontos | No | 
> | sharedvmimages | No | 
> | sharedvmimages / versões | No | 
> | instantâneos | No | 
> | sshpublickeys | No |
> | virtualmachines | Yes <br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover VMs Azure. | 
> | virtualmachines /extensões | No | 
> | virtualmachinescalesets | No | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | agregados de cussão | No | 
> | saldos | No | 
> | orçamentos | No | 
> | encargos | No | 
> | costtags | No | 
> | créditos | No | 
> | eventos | No | 
> | previsões | No | 
> | lotes | No | 
> | mercados | No | 
> | folhas de preços | No | 
> | produtos | No | 
> | detalhes de reservas | No | 
> | reservas recommendaçõesdedetails | No | 
> | reservas recommendações | No | 
> | reservas | No | 
> | transações de reservas | No | 
> | etiquetas | No | 
> | inquilinos | No | 
> | termos | No | 
> | usagedetails | No | 


## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | grupos de contentores | No | 
> | ligações de serviços de ações | No |


## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | registos | No |  
> | registos / agentpools | No | 
> | registos / buildtasks | No |  
> | registos / replicações | No | 
> | registos / tarefas | No |  
> | registos / webhooks | No | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | serviços de contentores | No |
> | geremclusters | No | 
> | aglomerados de gestão de turnos abertos | No | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aplicações | No | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | alerts | No | 
> | contas de faturação | No | 
> | orçamentos | No | 
> | cloudconnectors | No | 
> | conectores | No | 
> | departamentos | No | 
> | dimensões | No | 
> | matrículas | No | 
> | exportações | No | 
> | contas de falcão externos | No | 
> | forecast | No | 
> | consulta | No | 
> | registar | No | 
> | configs relatório | No | 
> | relatórios | No | 
> | definições | No | 
> | showbackrules | No | 
> | Modos de exibição | No | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | hubs | No |  

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | pedidos | No | 

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | associações | No |
> | recursosproviders | No | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | empregos | No | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | availableskus | No |
> | databoxedgedevices | No | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | áreas de trabalho | No | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | catálogos | No | 
> | datacatalogs | No | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | gerentes de conexão | No | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | pacotes | No | 
> | planos | No | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | datafactories | No | 
> | fábricas | No |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | datalakeaccounts | No | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | services | No | 
> | serviços / projetos | No | 
> | slots | No | 

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | ---------- |
> | backupvaults | No | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | No | 

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
> | grupos de servidores | No | 
> | servidores | Pode utilizar uma réplica de leitura transversal para mover um servidor existente. [Saiba mais.](../../postgresql/howto-move-regions-portal.md)
> | servidorv2 | No | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | artefactos | No | 
> | rollouts | No |  
> | topologias de serviço | No | 
> | topologias de serviços / serviços | No |  
> | topologias de serviços / serviços / unidades de serviço | No | 
> | passos | No | 


## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | grupos de aplicações | No | 
> | áreas de trabalho | No | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | elásticos | Não. O recurso não está exposto.
> | elásticos / iothubtenants | Não. O recurso não está exposto.
> | iothubs | Yes. [Saiba mais](../../iot-hub/iot-hub-how-to-clone.md)
> | serviços de provisionamento | No | 

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | controladores | No | 


## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | controladores | No | 
> | Aglomerado de AKS | No<br/><br/> [Saiba mais](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) sobre a mudança para outra região.

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | centros de laboratório | No | 
> | laboratórios | No | 
> | laboratórios / ambientes | No |  
> | laboratórios / corredores de serviço | No | 
> | laboratórios / virtualmachines | No |  
> | horários | No |  

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | digitaltwinsinstances | Sim, recriando recursos em nova região. [Saiba mais](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | contas de base de dados | No | 
> | contas de base de dados | No | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | domínios | No | 
> | gerassorequest | No | 
> | topleveldomains | No | 
> | validação da informação sobreregistration | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | services | No |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | domínios | No | 
> | subscrições de eventos | No |
> | extensõestópicas | No | 
> | partnernamespaces | No | 
> | registos de parceiros | No | 
> | partnertopics | No | 
> | sistematópicos | No | 
> | tópicos | No | 
> | tópicos | No | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aglomerados | No |  
> | espaços de nome | Sim (com modelo)<br/><br/> [Mover um espaço de nomes do Event Hub para outra região](../../event-hubs/move-across-regions.md) | 
> | sku | No |  

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | espaços de trabalho experimenta | No | 

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | espaços de nome | No | 

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | recursosproviders | No | 
> | funcionalidades | No | 
> | fornecedores | No | 
> | subscrições de registos de assinaturas | No | 

## <a name="microsoftgenomics"></a>Microsoft.Geómica

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | auto-administradores | No | 
> | autogeridavmconfigurationprofils | No | 
> | convidadoconfigurationasssignments | No | 
> | software | No | 
> | softwareupdateprofile | No | 
> | datas de software | No | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | hanainstances | No | 
> | sapmonitors | No |  

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | dedicados | No | 


## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aglomerados | No | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | services | No |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | máquinas | No | 
> | máquinas / extensões | No |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | datamanagers |  No | 

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | dispositivos | No | 
> | vnfs | No | 

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | componentes | No | 
> | networkscopes | No | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | empregos |  No | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | Não. [Saiba mais](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region).
> | grupos de ação |  No | 
> | activitylogalerts | No | 
> | alertas |  No | 
> | autoescala |  No | 
> | linha de base | No |
> | componentes |  No |  
> | datacollectionrules | No | 
> | diagnósticos | No | 
> | diagnósticoscategorias | No | 
> | eventcategorias | No | 
> | tipos de eventos | No | 
> | diagnósticosgnósticos estendidos | No | |
> | guestdiagnosticsettings | No | 
> | listmigrationdate | No | 
> | início de ções | No | 
> | logprofiles | No | 
> | registos | No | No |
> | metricalerts | No | 
> | bases métricas | No | 
> | metricbatch | No | 
> | métricas | No | 
> | espaços de nomes métricos | No | 
> | metrics | No | 
> | migratealertrules | No |
> | migrar modelo dericing | No | 
> | livros de trabalho | No |
> | grupos de notificações | No | 
> | privatelinkscopes | No |
> | modelo de backbacktolegacypricing | No |
> | queryrules agendados |  No | 
> | topology | No |
> | transações | No |
> | vminsightsonboardingstatuses | No |
> | webtests |  No | 
> | webtests / obtertestesultfile | No |
> | livros |  No |  
> | estalagens de livros | No |


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | apliques | No | 
> | iotapps | No | 



## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> |  Iothub |  Sim (centro clone) <br/><br/> [Clone um hub IoT para outra região](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da Região |
> | ------------- | ----------- | 
> | gráfico | No | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | deletedvaults | No |
> | hsmpools | No | 
> | geredhsmos | No |
> | cofres |  No | 

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | aglomerados conectados | No | 
> | assinaturas registadas | No | 

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | configurações de controlo de fontes | No | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aglomerados |  No |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | contas de laboratório | No | 
> | utilizadores | No | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | Não, é um serviço global.

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | hospedar ambientes | No | 
> | contas de integração |  No |  
> | integração de ambientes | No | 
> | integração de ambientesvírus /managedapis | No |
> | ambientes isolados | No | 
> | fluxos de trabalho |  No |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | planos de compromisso |  No | 
> | serviços web |  No | 
> | áreas de trabalho |  No | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | grupos de operacionalização |  No | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | No | 
> | contas de equipa | No | 


## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | No | 


## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | áreas de trabalho | No | 

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | configurações de assinaturas | Yes. [Saiba mais](../../virtual-machines/move-region-maintenance-configuration.md) | 
> | configurações de manutenção | Yes. [Saiba mais](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | atualizações | No | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | identidades | No | 
> | usassignedidentidades | No | 

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | managednetworks | No | 
> | gerednetworks / grupos de redes geridas | No |
> | managednetworks / managednetworkpeeringpolicies | No | 
> | notificação | No | 

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | marketplaceregistrationdefinitions | No | 
> | inscrições | No |
> | definições de registos | No | 

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | getentities | No | 
> | managementgroups | No | 
> | grupos de gestão / configurações | No | 
> | resources | No | 
> | filtro de backback starttenant | No | 
> | inquilinosfillstatus | No | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts |  Não, a Azure Maps é um serviço geoespacial. 
> | contas / privateatlases | No

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | ofertas | No | 
> | tipos de ofertas | No | 
> | privadas | No | 
> | privatestoreclient | No | 
> | lojas privadas | No | 
> | produtos | No | 
> | editores | No | 
> | registar | No | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | classicdevservices | No | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | acordos | No | 
> | tipos de ofertas | No | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | mediaservices |  No | 
> | mediaservices / liveevents |  No | 
> | mediaservices / streamingendpoints |  No | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | apclusters | No | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | projetos de avaliação | No | 
> | migrarprojects | No | 
> | câmaras de mudança | No
> | projetos | No | 

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | ---------- |
> | holográficosbroscastaccounts | No | 
> | contas objectunderstanding | No | 
> | contas de reconeradores remotos | No | 
> | spatialanchorsaccounts | No | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | contas netappac | No | 
> | contas de netappacs /capacitypools | No | 
> | contas de netappacs / capacitypools / volumes | No | 
> | contas netappaccounts / capacitypools / volumes / mounttargets | No | 
> | contas de netappacs / capacitypools / volumes / snapshots | No | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | pistas de aplicação | No |
> | applicationgatewaywebapplicationfirewallpolicies | No | 
> | grupos de aplicações |  No |  
> | azurefirewalls |  No |  
> | bastionhosts | No | 
> | bgpservicecommunidades | No |
> | conexões |  No | 
> | ddoscustompolicias |  No | 
> | ddosprotectionplans | No | 
> | dnszones |  No | 
> | expressroutecircuits | No | 
> | vias expressas | No | 
> | expressrouteserviceproviders | No | 
> | firewallpolicies | No |
> | frontdoors | No | 
> | ipallocations | No |
> | ipgrupos | No |
> | loadbalancers | Yes <br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover os equilibradores de carga internos e externos. |
> | vias de rede locais |  No | 
> | natgateways |  No | 
> | projetos de bioperiment de rede | No |
> | políticas de networkintent |  No | 
> | networkinterfaces | Yes <br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover NICs. | 
> | redes de mentofils | No | 
> | networksecuritygroups | Yes <br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover grupos de segurança de rede (NGSs). | 
> | observadores de rede |  No |  
> | observadores de rede / suportes de conexão |  No | 
> | networkwatchers / fluxologs |  No | 
> | observadores de rede / pingmeshes |  No | 
> | p2svpngateways | No | 
> | zonas privadas |  No |  
> | privatednszones / virtualnetworklinks | No |> | privatednszonesinternal | No |
> | privateendpointredirectmaps | No |
> | pontos privados | No | 
> | serviços de privatelink | No | 
> | endereços publicitários | Yes<br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover endereços IP públicos. |
> | publicipprefixos | No | 
> | filtros de rota | No | 
> | routetables |  No | 
> | segurançapartnerproviders | No |
> | serviceendpointpolicies |  No | 
> | trafficmanagergeographichierarchies | No | 
> | trafficmanagerprofiles |  No | 
> | trafficmanagerusermetricskeys | No |
> | virtualhubs | No | 
> | vias virtualnetworkgateways |  No |  
> | virtualnetworks |  No | 
> | virtualnetworktaps | No | 
> | virtualwans | No | 
> | VPNgateways (Wan Virtual) | No | 
> | vpnsites (WAN virtual) | No | 
> | vpnsites (WAN virtual) | No |


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | espaços de nome |  No | 
> | espaços de nomes / notificações |  No |  

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | osnamepas | No | 

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | hipervsites | No | 
> | importações | No | 
> | servidores | No | 
> | vmwaresites | No | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aglomerados | No | 
> | espaços de trabalho apagados | No | 
> | linktargets | No | 
> | storageinsightconfigs | No |
> | áreas de trabalho | No |



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | sociedades de gestão | No |
> | configurações de gestão |  No | 
> | soluções | No |
> | Modos de exibição |  No | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | legacypeerings | No | 
> | peerasns | No | 
> | peeringlocações | No | 
> | espreiteiros | No | 
> | países de serviço de peering | No | 
> | localização de serviços de espre éming | No | 
> | peeringserviceproviders | No | 
> | serviços de espreiteiros | No | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | eventos de política | No | 
> | estados de política | No | 
> | recursos de recursos de pista de política | No | 
> | remediações | No | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | Consolas do | No |
> | dashboards | No | 
> | utilizadores | No | 


## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | trabalhopascoscollections |  No | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | capacidades |  No | 

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | contas disponíveis | No | 
> | registos de fornecedores | No | 
> | rollouts | No | 

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | áreas de trabalho | No | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | replicationeligibilityres | No |
> | cofres | Não.<br/><br/> A movimentação de cofres dos Serviços de Recuperação para o Azure Backup em todas as regiões de Azure não é suportada.<br/><br/> Nos cofres dos Serviços de Recuperação para recuperação do local de Azure, pode [desativar e recriar o cofre](../../site-recovery/move-vaults-across-regions.md) na região alvo. | 

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatopenShift

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | aglomerados de turnos abertos | No | 

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | espaços de nome |  No | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | consultas |  No |  
> | resourcechangedetails | No | 
> | mudanças de recursos | No | 
> | resources | No | 
> | recursos história | No | 
> | subscriçõesstatus | No | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | fontes infantis | No | 
> | emergentes | No | 
> | eventos | No | 
> | do IdP | No | 
> | notificações | No | 

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região |
> | ------------- | ----------- |
> | implementaçõesScripts |  Yes<br/><br/>[Mover recursos da Microsoft.Resources para uma nova região](microsoft-resources-move-regions.md) |
> | modeloSpecs |  Yes<br/><br/>[Mover recursos da Microsoft.Resources para uma nova região](microsoft-resources-move-regions.md) |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aplicações |  No | 
> | saasresources | No | 


## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | resourcehealthmetadata | No |
> | serviços de pesquisa |  No | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | adaptíostas de redes | No | 
> | advancedthreatprotectionsettings | No | 
> | alerts | No | 
> | permitidas ligações | No | 
> | listas de aplicaçõeswhitelistings | No | 
> | avaliaçãometadata | No | 
> | avaliações | No | 
> | autodismissalertsrules | No | 
> | automações | No | 
> | autoprovisionings | No |
> | resultados de conformidade | No | 
> | conformidades | No | 
> | datacollectionagentes | No | 
> | grupos de dispositivos de insegurança | No | 
> | descobertas sões de inseguranças | No | 
> | sões de segurança externa | No | 
> | políticas de proteção de informação | No | 
> | soluções de iotsecuritys | No | 
> | iotsecuritysolutions / analyticsmodels | No | 
> | iotsecuritysolutions / analyticsmodels / agregados | No | 
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | No | 
> | jitnetworkacsspolicies | No | 
> | políticas | No | 
> | preços | No | 
> | padrões de regulação | No | 
> | padrões de regulação /regulatórios | No | 
> | padrões de regulação / regulatórios de controlo /regulatórios | No | 
> | contactos de segurança | No | 
> | garantias de segurança | No | 
> | securitysolutionsreferencedata | No | 
> | estados de segurança | No | 
> | securitystatusessummaries | No | 
> | servidorvulnerabilityaments | No | 
> | definições | No | 
> | subassessments | No |
> | tarefas | No | 
> | topologias | No | 
> | espaços de trabalho | No | 

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | agregações | No | 
> | alertas | No | 
> | alertaruletemplas | No | 
> | regras automation | No |
> | casos | No | 
> | dataconnectors | No | 
> | entidades | No | 
> | entidadequeries | No |
> | Incidentes | No | 
> | escritórioconsents | No | 
> | definições | No | 
> | ameaçaintelligence | No | 

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | serviços de consola | No | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | portais | No | 
> | osdes | No | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | espaços de nome |  No | 
> | premiummessaging regiões | No | 
> | sku | No | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aplicações | No | 
> | aglomerados |  No |  
> | grupos de contentores | No | 
> | conjuntos de contentores | No | 
> | edgeclusters | No | 
> | geremclusters | No |
> | redes | No | 
> | lojas secretas | No | 
> | volumes | No | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aplicações |  No | 
> | grupos de contentores | No | 
> | portais |  No | 
> | redes |  No | 
> | segredos |  No | 
> | volumes |  No |  

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | rollouts | No | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | sinaleiro |  No |  

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | híbridosbenefits | No | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aparelhos dedefinições | No | 
> | aparelhos | No | 
> | jitrequests | No | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | instâncias | No | 
> | Locais | No |
> | managedinstances | Yes <br/><br/> [Saiba mais](../../azure-sql/database/move-resources-across-regions.md) sobre a mudança de instâncias geridas em todas as regiões. | 
> | managedinstances / bases de dados | Yes | 
> | servidores | Yes | 
> | servidores / bases de dados | Yes <br/><br/> [Saiba mais](../../azure-sql/database/move-resources-across-regions.md) sobre a mudança de bases de dados em regiões.<br/><br/> [Saiba mais](../../resource-mover/tutorial-move-region-sql.md) sobre a utilização do Azure Resource Mover para mover bases de dados Azure SQL.  | 
> | servidores / elásticos | Yes <br/><br/> [Saiba mais](../../azure-sql/database/move-resources-across-regions.md) sobre a movimentação de piscinas elásticas em regiões.<br/><br/> [Saiba mais](../../resource-mover/tutorial-move-region-sql.md) sobre a utilização do Azure Resource Mover para mover piscinas elásticas Azure SQL.  | 
> | virtualclusters | Yes | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | grupos sqlvirtualmachineinegroups |  No |  
> | sqlvirtualmachines |  No |  


## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | contas de armazenamento | Yes<br/><br/> [Mover uma conta de Armazenamento Azure para outra região](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | caches | No | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | storagesyncservices |  No | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | storagesyncservices | No | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | storagesyncservices | No | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | gestores | No | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aglomerados | No |
> | streamingjobs |  No |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | ambientes | No | 
> | casos | No | 

## <a name="microsoftsubscription"></a>Microsoft.Subscrição

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | assinaturas | No | 

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | services | No | 
> | bilhetes de apoio | No | 

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | áreas de trabalho | No | 
> | espaços de trabalho / bigdatapools | No | 
> | espaços de trabalho / sqlpools | No | 


## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | ambientes |  No | 
> | ambientes / fontes de eventos |  No |  
> | ambientes / conjuntos de dados de referência |  No | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | lojas | No | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | imagens | No | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | conta |  No | 
> | conta / extensão |  No | 
> | conta / projeto |  No | 

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | arczones | No | 
> | recursos | No | 
> | vcenters | No | 
> | virtualmachines | No | 
> | virtualmachinetemplates | No | 
> | virtualnetworks | No | 

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | não-100 dedicados | No | 
> | serviços decloud dedicados | No | 
> | virtualmachines | No | 

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | dispositivos | No | 
> | vnfs | No | 

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | accounts | No | 
> | planos | No | 
> | assinaturas registadas | No |


## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | disponíveis mochilas | No | 
> | billingmeters | No | 
> | certificados | No | 
> | vias de conexão |  No |  
> | conexões |  No |  
> | customapis |  No | 
> | deletedsites | No | 
> | implantações | No | 
> | geo-regiões | No | 
> | hospedar ambientes | No | 
> | kubeenvironments | No | 
> | editores | No |
> | recomendações | No | 
> | resourcehealthmetadata | No | 
> | tempos de execução | No | 
> | serverfarms | No |  
> | serverfarms / eventgridfilters | N
> | sites |  No | 
> | sites / premieraddons |  No |  
> | sites / slots |  No |  
> | controlos de fontes | No |
> | estáticasites | No | 

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | multipleactivationkeys | No |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | serviços de dispositivos | No | 

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | cargas de trabalho | No | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | componentes | No |
> | consumada de componentes | No | 
> | monitorinstâncias | No | 
> | monitorinstancessummary | No | 
> | monitores | No | 
## <a name="third-party-services"></a>Serviços de terceiros

Os serviços de terceiros não suportam atualmente a operação de mudança.

## <a name="next-steps"></a>Próximos passos

[Saiba mais](../../resource-mover/overview.md) sobre o serviço Demôr Recursos.

