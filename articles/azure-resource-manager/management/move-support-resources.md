---
title: Mover suporte de operação por tipo de recurso
description: Lista os tipos de recursos Azure que podem ser transferidos para um novo grupo de recursos, subscrição ou região.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: a56a9e6f04aa800e16bbab0190ce7b41d87da590
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740093"
---
# <a name="move-operation-support-for-resources"></a>Mover suporte de operação para recursos

Este artigo indica se um tipo de recurso Azure suporta a operação de movimento. Também fornece informações sobre condições especiais a ter em conta ao mover um recurso.

> [!IMPORTANT]
> Na maioria dos casos, um recurso infantil não pode ser movido independentemente do seu recurso principal. Os recursos infantis têm um tipo de recurso no formato de `<resource-provider-namespace>/<parent-resource>/<child-resource>` . Por exemplo, `Microsoft.ServiceBus/namespaces/queues` é um recurso infantil de `Microsoft.ServiceBus/namespaces` . Quando se move o recurso principal, o recurso da criança é automaticamente movido com ele. Se não vir um recurso infantil neste artigo, pode assumir que é movido com o recurso dos pais. Se o recurso dos pais não suportar o movimento, o recurso da criança não pode ser movido.

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
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | serviços de domínio | No | No |  No |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | diagnósticos | No | No | No |
> | diagnósticoscategorias | No | No | No |
> | privatelinkforazuread | Yes | Yes | No |
> | inquilinos | Yes | Yes | No |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | supportproviders | No | No | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | aadsupportcases | No | No | No |
> | adds serviços | No | No | No |
> | agentes | No | No | No |
> | anónimos | No | No | No |
> | configuração | No | No | No |
> | registos | No | No | No |
> | relatórios | No | No | No |
> | serviços de saúde | No | No | No |
> | services | No | No | No |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | configurações | No | No | No |
> | gerar recommendações | No | No | No |
> | do IdP | No | No | No |
> | recomendações | No | No | No |
> | supressões | No | No | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | regras de ação | Yes | Yes | No |
> | alerts | No | No | No |
> | lista de alertas | No | No | No |
> | alertasmetadata | No | No | No |
> | alertasummary | No | No | No |
> | alertasummarylista | No | No | No |
> | smartdetectoralertrules | Yes | Yes | No |
> | grupos inteligentes | No | No | No |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | servidores | Yes | Yes | No |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!IMPORTANT]
> Um serviço de Gestão API que está definido para o SKU de consumo não pode ser movido.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | reportfeedback | No | No | No |
> | serviço | Yes | Yes | Sim (usando o modelo) <br/><br/> [Mover a API Management através das regiões.](../../api-management/api-management-howto-migrate.md) |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | lojas de configurações | Yes | Yes | No |
> | lojas de configurações / filtros de eventos | No | No | No |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | primavera | Yes | Yes | No |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!IMPORTANT]
> Consulte a [orientação do movimento do Serviço de Aplicações](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | apiapps | No | No | Sim (usando o modelo)<br/><br/> [Mover uma app de Serviço de Aplicações para outra região](../../app-service/manage-move-across-regions.md) |
> | appidentidades | No | No | No |
> | portais | No | No | No |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | atesstationproviders | Yes | Yes | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | classicadministradores | No | No | No |
> | dataaliases | No | No | No |
> | denyassignments | No | No | No |
> | elevateaccess | No | No | No |
> | findorphanroleassignments | No | No | No |
> | fechaduras | No | No | No |
> | permissões | No | No | No |
> | assinaturas políticas | No | No | No |
> | definições de políticas | No | No | No |
> | políticas dedefinições | No | No | No |
> | privatelinkassociations | No | No | No |
> | recursos gestagementprivatelinks | No | No | No |
> | roleassignments | No | No | No |
> | roleassignmentsusagemetrics | No | No | No |
> | papéis dedefinições | No | No | No |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!IMPORTANT]
> Os runbooks devem existir no mesmo grupo de recursos que a Conta De Automação.
>
> Para obter informações, consulte [mover a sua conta Azure Automation para outra subscrição](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | contas de automação | Yes | Yes | Sim (usando o modelo) <br/><br/> [Utilização de geo-replicação](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |
> | contas/configurações de automação | Yes | Yes | No |
> | contas de automação / runbooks | Yes | Yes | No |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | nuvens privadas | Yes | Yes | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | b2direões | Yes | Yes | No |
> | b2ctenants | No | No | No |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | datacontrollers | No | No | No |
> | híbridos gerentes | No | No | No |
> | pós-insinstâncias | No | No | No |
> | sqlinstances | No | No | No |
> | sqlmanagedinstances | No | No | No |
> | sqlserverinstances | No | No | No |
> | sqlserverregistrations | Yes | Yes | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | cloudmanifestfiles | No | No | No |
> | registos | Yes | Yes | No |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | aglomerados | No | No | No |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | lotaccounts | Yes | Yes | As contas do lote não podem ser movidas diretamente de uma região para outra, mas você pode usar um modelo para exportar um modelo, modificá-lo e implementar o modelo para a nova região. <br/><br/> Saiba como [mover uma conta batch através de regiões](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | contas de faturação | No | No | No |
> | faturantes | No | No | No |
> | faturações | No | No | No |
> | billingproperty | No | No | No |
> | billingroleassignments | No | No | No |
> | billingroledefinitions | No | No | No |
> | departamentos | No | No | No |
> | matrículas | No | No | No |
> | faturas | No | No | No |
> | transferências | No | No | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | mapapis | No | No | No |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | biztalk | No | No | No |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | membros blockchain | No | No | No <br/><br/> A rede blockchain não pode ter nós em diferentes regiões. |
> | cordamembers | No | No | No |
> | observadores | No | No | No |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | serviços de token | No | No | No |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | blueprintassignments | No | No | No |
> | plantas | No | No | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | botservices | Yes | Yes | No |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!IMPORTANT]
> Se a cache Azure para a instância Redis estiver configurada com uma rede virtual, o caso não pode ser movido para uma subscrição diferente. Ver [limitações de movimento de rede](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | redis | Yes | Yes | No |
> | redisenterprise | No | No | No |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | reservações aplicadas | No | No | No |
> | calcular a troca | No | No | No |
> | calcular preço | No | No | No |
> | calcular o preço da compra | No | No | No |
> | catálogos | No | No | No |
> | ordenadores de reservação comercial | No | No | No |
> | troca | No | No | No |
> | encomendas de reservas | No | No | No |
> | reservas | No | No | No |
> | resources | No | No | No |
> | validação encomenda de reservas | No | No | No |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | cdnwebapplication firewallmanagedrulesets | No | No | No |
> | cdnwebapplicationfirewallpolicies | Yes | Yes | No |
> | edgenodes | No | No | No |
> | perfis | Yes | Yes | No |
> | perfis / pontos finais | Yes | Yes | No |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!IMPORTANT]
> Consulte a [orientação do movimento do Serviço de Aplicações](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | certificadores | Yes | Yes | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> Consulte a [orientação do movimento de implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | capacidades | No | No | No |
> | temas de domínio | Yes | No | No |
> | quotas | No | No | No |
> | recursos | No | No | No |
> | valida a subscrição demovedabilidade | No | No | No |
> | virtualmachines | Yes | Yes | No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!IMPORTANT]
> Consulte a [orientação do movimento de implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | classicinfrastructureresources | No | No | No |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!IMPORTANT]
> Consulte a [orientação do movimento de implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | capacidades | No | No | No |
> | expressroute crossconnections | No | No | No |
> | expressroutecrossconnections / peerings | No | No | No |
> | gatewaysupporteddes | No | No | No |
> | networksecuritygroups | No | No | No |
> | quotas | No | No | No |
> | reservedips | No | No | No |
> | virtualnetworks | No | No | No |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!IMPORTANT]
> Consulte a [orientação do movimento de implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | discos | No | No | No |
> | imagens | No | No | No |
> | osimagems | No | No | No |
> | osplatformimages | No | No | No |
> | imagens públicas | No | No | No |
> | quotas | No | No | No |
> | contas de armazenamento | Yes | No | Yes |
> | vmimages | No | No | No |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!IMPORTANT]
> Consulte a [orientação do movimento de implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | operações | No | No | No |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Yes | Yes | No |
> | Cognitive Search | **pendente** | **pendente** | Suportado com passos manuais.<br/><br/> Saiba como [mover o seu serviço de Pesquisa Cognitiva Azure para outra região](../../search/search-howto-move-across-regions.md) |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | cartão de taxa | No | No | No |
> | usageaggregates | No | No | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> Veja [a orientação das Máquinas Virtuais.](./move-limitations/virtual-machines-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | conjuntos de disponibilidades | Yes | Yes |  Yes <br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover conjuntos de disponibilidade. |
> | diskaccesses | No | No | No |
> | conjuntos de diskencrypations | No | No | No |
> | discos | Yes | Yes | Yes <br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover VMs Azure e discos relacionados. |
> | galerias | No | No | No |
> | galerias / imagens | No | No | No |
> | galerias / imagens / versões | No | No | No |
> | grupos de anfitriões | No | No | No |
> | grupos de anfitriões / anfitriões | No | No | No |
> | imagens | Yes | Yes | No |
> | grupos de destacamentos de proximidade | Yes | Yes | No |
> | restaurar as recordações de pontos | No | No | No |
> | restaurar acolecções / pontos de restauro | No | No | No |
> | partilha de milhões de pessoas | No | No | No |
> | sharedvmimages | No | No | No |
> | sharedvmimages / versões | No | No | No |
> | instantâneos | Yes | Yes | No |
> | sshpublickeys | No | No | No |
> | virtualmachines | Yes | Yes | Yes <br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover VMs Azure. |
> | virtualmachines /extensões | Yes | Yes | No |
> | virtualmachinescalesets | Yes | Yes | No |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | agregados de cussão | No | No | No |
> | saldos | No | No | No |
> | orçamentos | No | No | No |
> | encargos | No | No | No |
> | costtags | No | No | No |
> | créditos | No | No | No |
> | eventos | No | No | No |
> | previsões | No | No | No |
> | lotes | No | No | No |
> | mercados | No | No | No |
> | folhas de preços | No | No | No |
> | produtos | No | No | No |
> | detalhes de reservas | No | No | No |
> | reservas recommendaçõesdedetails | No | No | No |
> | reservas recommendações | No | No | No |
> | reservas | No | No | No |
> | transações de reservas | No | No | No |
> | etiquetas | No | No | No |
> | inquilinos | No | No | No |
> | termos | No | No | No |
> | usagedetails | No | No | No |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | grupos de contentores | No | No | No |
> | ligações de serviços de ações | No | No | No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | registos | Yes | Yes | No |
> | registos / agentpools | Yes | Yes | No |
> | registos / buildtasks | Yes | Yes | No |
> | registos / replicações | Yes | Yes | No |
> | registos / tarefas | Yes | Yes | No |
> | registos / webhooks | Yes | Yes | No |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | serviços de contentores | No | No | No |
> | geremclusters | No | No | No |
> | aglomerados de gestão de turnos abertos | No | No | No |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | aplicações | No | No | No |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | No | No | No |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | alerts | No | No | No |
> | contas de faturação | No | No | No |
> | orçamentos | No | No | No |
> | cloudconnectors | No | No | No |
> | conectores | Yes | Yes | No |
> | departamentos | No | No | No |
> | dimensões | No | No | No |
> | matrículas | No | No | No |
> | exportações | No | No | No |
> | contas de falcão externos | No | No | No |
> | forecast | No | No | No |
> | consulta | No | No | No |
> | registar | No | No | No |
> | configs relatório | No | No | No |
> | relatórios | No | No | No |
> | definições | No | No | No |
> | showbackrules | No | No | No |
> | Modos de exibição | No | No | No |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | hubs | No | No | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | pedidos | No | No | No |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | associações | No | No | No |
> | recursosproviders | Yes | Yes | No |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | empregos | No | No | No |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | availableskus | No | No | No |
> | databoxedgedevices | No | No | No |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | áreas de trabalho | No | No | No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | catálogos | Yes | Yes | No |
> | datacatalogs | No | No | No |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | gerentes de conexão | No | No | No |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | pacotes | No | No | No |
> | planos | No | No | No |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | datafactories | Yes | Yes | No |
> | fábricas | Yes | Yes | No |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | datalakeaccounts | No | No | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Yes | Yes | No |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Yes | Yes | No |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | services | No | No | No |
> | serviços / projetos | No | No | No |
> | slots | No | No | No |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ---------- |
> | backupvaults | No | No | No |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Yes | Yes | No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | servidores | Yes | Yes | Pode utilizar uma réplica de leitura transversal para mover um servidor existente. [Saiba mais](../../postgresql/howto-move-regions-portal.md).<br/><br/> Se o serviço for aloque-se com armazenamento de backup geo-redundante, pode utilizar o geo-restauro para restaurar noutras regiões. [Saiba mais](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage).

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | flexibleServers | No | No | No |
> | servidores | Yes | Yes | Pode utilizar uma réplica de leitura transversal para mover um servidor existente. [Saiba mais](../../mysql/howto-move-regions-portal.md).

## <a name="microsoftdbforpostgresql"></a>Microsoft.DbforPostgresql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | flexibleServers | No | No | No |
> | grupos de servidores | No | No | No |
> | servidores | Yes | Yes | Pode utilizar uma réplica de leitura transversal para mover um servidor existente. [Saiba mais](../../postgresql/howto-move-regions-portal.md).
> | servidorv2 | Yes | Yes | No |
> | solteiroservers | Yes | Yes | No |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | artefactos | Yes | Yes | No |
> | rollouts | Yes | Yes | No |
> | topologias de serviço | Yes | Yes | No |
> | topologias de serviços / serviços | Yes | Yes | No |
> | topologias de serviços / serviços / unidades de serviço | Yes | Yes | No |
> | passos | Yes | Yes | No |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | grupos de aplicações | Yes | Yes | No |
> | hostpools | Yes | Yes | No |
> | áreas de trabalho | Yes | Yes | No |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | elásticos | No | No | N.º O recurso não está exposto. |
> | elásticos / iothubtenants | No | No | N.º O recurso não está exposto. |
> | iothubs | Yes | Sim | Sim. [Saiba mais](../../iot-hub/iot-hub-how-to-clone.md) |
> | serviços de provisionamento | Yes | Yes | No |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | oleodutos | Yes | Yes | No |
> | controladores | **pendente** | **pendente** | No |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | controladores | Yes | Yes | No |
> | Aglomerado de AKS | **pendente** | **pendente** | No<br/><br/> [Saiba mais](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) sobre a mudança para outra região.

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | centros de laboratório | No | No | No |
> | laboratórios | Yes | No | No |
> | laboratórios / ambientes | Yes | Yes | No |
> | laboratórios / corredores de serviço | Yes | Yes | No |
> | laboratórios / virtualmachines | Yes | No | No |
> | horários | Yes | Yes | No |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | digitaltwinsinstances | No | No | Sim, recriando recursos em nova região. [Saiba mais](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | números de dados | No | No | No |
> | contas de base de dados | Yes | Yes | No |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | domínios | Yes | Yes | No |
> | gerassorequest | No | No | No |
> | topleveldomains | No | No | No |
> | validação da informação sobreregistration | No | No | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Yes | Yes | No |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | domínios | Yes | Yes | No |
> | subscrições de eventos | Não - não pode ser movido de forma independente, mas automaticamente movido com recurso subscrito. | Não - não pode ser movido de forma independente, mas automaticamente movido com recurso subscrito. | No |
> | extensõestópicas | No | No | No |
> | partnernamespaces | Yes | Yes | No |
> | registos de parceiros | No | No | No |
> | partnertopics | Yes | Yes | No |
> | sistematópicos | Yes | Yes | No |
> | tópicos | Yes | Yes | No |
> | tópicos | No | No | No |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | aglomerados | Yes | Yes | No |
> | espaços de nome | Yes | Yes | Sim (com modelo)<br/><br/> [Mover um espaço de nomes do Event Hub para outra região](../../event-hubs/move-across-regions.md) |
> | sku | No | No | No |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | espaços de trabalho experimenta | No | No | No |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | espaços de nome | Yes | Yes | No |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | recursosproviders | No | No | No |
> | funcionalidades | No | No | No |
> | fornecedores | No | No | No |
> | subscrições de registos de assinaturas | No | No | No |

## <a name="microsoftgenomics"></a>Microsoft.Geómica

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | No | No | No |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | auto-administradores | No | No | No |
> | autogeridavmconfigurationprofils | No | No | No |
> | convidadoconfigurationasssignments | No | No | No |
> | software | No | No | No |
> | softwareupdateprofile | No | No | No |
> | datas de software | No | No | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | hanainstances | No | No | No |
> | sapmonitors | No | No | No |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | dedicados | No | No | No |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!IMPORTANT]
> Pode mover os clusters HDInsight para um novo grupo de subscrição ou recursos. No entanto, não é possível mover-se através de subscrições os recursos de networking ligados ao cluster HDInsight (como a rede virtual, NIC ou equilibrador de carga). Além disso, não é possível mover-se para um novo grupo de recursos um NIC que esteja ligado a uma máquina virtual para o cluster.
>
> Ao mover um cluster HDInsight para uma nova subscrição, primeiro mova outros recursos (como a conta de armazenamento). Em seguida, mova o cluster HDInsight por si só.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | aglomerados | Yes | Yes | No |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Yes | Yes | No |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | máquinas | Yes | Yes | No |
> | máquinas / extensões | Yes | Yes | No |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | datamanagers | Yes | Yes | No |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | dispositivos | No | No | No |
> | vnfs | No | No | No |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | componentes | No | No | No |
> | networkscopes | No | No | No |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | empregos | Yes | Yes | No |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!IMPORTANT]
> Certifique-se de que a mudança para uma nova subscrição não excede as [quotas de subscrição](azure-subscription-service-limits.md#azure-monitor-limits).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | **pendente** | **pendente** | N.º [Saiba mais](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region). |
> | grupos de ação | Yes | Yes | No |
> | activitylogalerts | No | No | No |
> | alertas | Yes | Yes | No |
> | autoescala | Yes | Yes | No |
> | linha de base | No | No | No |
> | componentes | Yes | Yes | No |
> | datacollectionrules | No | No | No |
> | diagnósticos | No | No | No |
> | diagnósticoscategorias | No | No | No |
> | eventcategorias | No | No | No |
> | tipos de eventos | No | No | No |
> | diagnósticosgnósticos estendidos | No | No | No |
> | guestdiagnosticsettings | No | No | No |
> | listmigrationdate | No | No | No |
> | início de ções | No | No | No |
> | logprofiles | No | No | No |
> | registos | No | No | No |
> | metricalerts | No | No | No |
> | bases métricas | No | No | No |
> | metricbatch | No | No | No |
> | métricas | No | No | No |
> | espaços de nomes métricos | No | No | No |
> | metrics | No | No | No |
> | migratealertrules | No | No | No |
> | migrar modelo dericing | No | No | No |
> | livros de trabalho | No | No | No |
> | grupos de notificações | No | No | No |
> | privatelinkscopes | No | No | No |
> | modelo de backbacktolegacypricing | No | No | No |
> | queryrules agendados | Yes | Yes | No |
> | topology | No | No | No |
> | transações | No | No | No |
> | vminsightsonboardingstatuses | No | No | No |
> | webtests | Yes | Yes | No |
> | webtests / obtertestesultfile | No | No | No |
> | livros | Yes | Yes | No |
> | estalagens de livros | Yes | Yes | No |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | apliques | No | No | No |
> | iotapps | Yes | Yes | No |

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | Iothub | **pendente** | **pendente** | Sim (centro clone) <br/><br/> [Clone um hub IoT para outra região](../../iot-hub/iot-hub-how-to-clone.md) |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | gráfico | Yes | Yes | No |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!IMPORTANT]
> Os Cofres-Chave utilizados para encriptação de discos não podem ser transferidos para um grupo de recursos na mesma subscrição ou através de subscrições.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | deletedvaults | No | No | No |
> | hsmpools | No | No | No |
> | geredhsmos | No | No | No |
> | cofres | Yes | Yes | No |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | aglomerados conectados | Yes | Yes | No |
> | assinaturas registadas | No | No | No |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | configurações de controlo de fontes | No | No | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | aglomerados | Yes | Yes | No |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | contas de laboratório | No | No | No |
> | utilizadores | No | No | No |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | No | No | No |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | No | No | Não, é um serviço global. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | hospedar ambientes | No | No | No |
> | contas de integração | Yes | Yes | No |
> | integração de ambientes | Yes | No | No |
> | integração de ambientesvírus /managedapis | Yes | No | No |
> | ambientes isolados | No | No | No |
> | fluxos de trabalho | Yes | Yes | No |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | planos de compromisso | No | No | No |
> | serviços web | Yes | No | No |
> | áreas de trabalho | Yes | Yes | No |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | grupos de operacionalização | No | No | No |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | No | No | No |
> | contas de equipa | No | No | No |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | No | No | No |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | áreas de trabalho | No | No | No |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | configurações de assinaturas | No | Não | Yes. [Saiba mais](../../virtual-machines/move-region-maintenance-configuration.md) |
> | configurações de manutenção | Yes | Sim | Sim. [Saiba mais](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | atualizações | No | No | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | identidades | No | No | No |
> | usassignedidentidades | No | No | No |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | managednetworks | No | No | No |
> | gerednetworks / grupos de redes geridas | No | No | No |
> | managednetworks / managednetworkpeeringpolicies | No | No | No |
> | notificação | No | No | No |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | marketplaceregistrationdefinitions | No | No | No |
> | inscrições | No | No | No |
> | definições de registos | No | No | No |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | getentities | No | No | No |
> | managementgroups | No | No | No |
> | grupos de gestão / configurações | No | No | No |
> | resources | No | No | No |
> | filtro de backback starttenant | No | No | No |
> | inquilinosfillstatus | No | No | No |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Yes | Yes | Não, a Azure Maps é um serviço geoespacial. |
> | contas / privateatlases | Yes | Yes | No |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | ofertas | No | No | No |
> | tipos de ofertas | No | No | No |
> | privadas | No | No | No |
> | privatestoreclient | No | No | No |
> | lojas privadas | No | No | No |
> | produtos | No | No | No |
> | editores | No | No | No |
> | registar | No | No | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | classicdevservices | No | No | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | acordos | No | No | No |
> | tipos de ofertas | No | No | No |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | mediaservices | Yes | Yes | No |
> | mediaservices / liveevents | Yes | Yes | No |
> | mediaservices / streamingendpoints | Yes | Yes | No |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | apclusters | No | No | No |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | projetos de avaliação | No | No | No |
> | migrarprojects | No | No | No |
> | câmaras de mudança | No | No | No |
> | projetos | No | No | No |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ---------- |
> | holográficosbroscastaccounts | No | No | No |
> | contas objectunderstanding | No | No | No |
> | contas de reconeradores remotos | Yes | Yes | No |
> | spatialanchorsaccounts | Yes | Yes | No |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | contas netappac | No | No | No |
> | contas de netappacs /capacitypools | No | No | No |
> | contas de netappacs / capacitypools / volumes | No | No | No |
> | contas netappaccounts / capacitypools / volumes / mounttargets | No | No | No |
> | contas de netappacs / capacitypools / volumes / snapshots | No | No | No |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> Consulte [a orientação do movimento em rede](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | pistas de aplicação | No | No | No |
> | applicationgatewaywebapplicationfirewallpolicies | No | No | No |
> | grupos de aplicações | Yes | Yes | No |
> | azurefirewalls | No | No | No |
> | bastionhosts | No | No | No |
> | bgpservicecommunidades | No | No | No |
> | conexões | Yes | Yes | No |
> | ddoscustompolicias | Yes | Yes | No |
> | ddosprotectionplans | No | No | No |
> | dnszones | Yes | Yes | No |
> | expressroutecircuits | No | No | No |
> | vias expressas | No | No | No |
> | expressrouteserviceproviders | No | No | No |
> | firewallpolicies | Yes | Yes | No |
> | frontdoors | No | No | No |
> | ipallocations | Yes | Yes | No |
> | ipgrupos | Yes | Yes | No |
> | loadbalancers | Sim - SKU Básico<br> Sim - SKU Padrão | Sim - SKU Básico<br>Não - SKU Padrão | Yes <br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover os equilibradores de carga internos e externos. |
> | vias de rede locais | Yes | Yes | No |
> | natgateways | No | No | No |
> | projetos de bioperiment de rede | No | No | No |
> | políticas de networkintent | Yes | Yes | No |
> | networkinterfaces | Yes | Yes | Yes <br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover NICs. |
> | redes de mentofils | No | No | No |
> | networksecuritygroups | Yes | Yes | Yes <br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover grupos de segurança de rede (NGSs). |
> | observadores de rede | No | No | No |
> | observadores de rede / suportes de conexão | Yes | No | No |
> | networkwatchers / fluxologs | Yes | No | No |
> | observadores de rede / pingmeshes | Yes | No | No |
> | p2svpngateways | No | No | No |
> | zonas privadas | Yes | Yes | No |
> | privatednszones / virtualnetworklinks | Yes | Yes | No |
> | privatednszonesinternal | No | No | No |
> | privateendpointredirectmaps | No | No | No |
> | pontos privados | No | No | No |
> | serviços de privatelink | No | No | No |
> | endereços publicitários | Sim - SKU Básico<br>Sim - SKU Padrão | Sim - SKU Básico<br>Não - SKU Padrão | Yes<br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover endereços IP públicos. |
> | publicipprefixos | Yes | Yes | No |
> | filtros de rota | No | No | No |
> | routetables | Yes | Yes | No |
> | segurançapartnerproviders | Yes | Yes | No |
> | serviceendpointpolicies | Yes | Yes | No |
> | trafficmanagergeographichierarchies | No | No | No |
> | trafficmanagerprofiles | Yes | Yes | No |
> | trafficmanagerprofiles / heatmaps | No | No | No |
> | trafficmanagerusermetricskeys | No | No | No |
> | virtualhubs | No | No | No |
> | vias virtualnetworkgateways | Yes | Yes | No |
> | virtualnetworks | Yes | Yes | No |
> | virtualnetworktaps | No | No | No |
> | virtualrouters | Yes | Yes | No |
> | virtualwans | No | No |
> | VPNgateways (Wan Virtual) | No | No | No |
> | vpnsnserverconfigurations | No | No | No |
> | vpnsites (WAN virtual) | No | No | No |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | espaços de nome | Yes | Yes | No |
> | espaços de nomes / notificações | Yes | Yes | No |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | osnamepas | Yes | Yes | No |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | hipervsites | No | No | No |
> | importações | No | No | No |
> | servidores | No | No | No |
> | vmwaresites | No | No | No |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!IMPORTANT]
> Certifique-se de que a mudança para uma nova subscrição não exceda as [quotas de subscrição](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Espaços de trabalho que têm uma conta de automação ligada não podem ser movidos. Antes de iniciar uma operação de movimento, certifique-se de desvincular quaisquer contas de automação.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | aglomerados | No | No | No |
> | espaços de trabalho apagados | No | No | No |
> | linktargets | No | No | No |
> | storageinsightconfigs | No | No | No |
> | áreas de trabalho | Yes | Yes | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | sociedades de gestão | No | No | No |
> | configurações de gestão | Yes | Yes | No |
> | soluções | Yes | Yes | No |
> | Modos de exibição | Yes | Yes | No |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | legacypeerings | No | No | No |
> | peerasns | No | No | No |
> | peeringlocações | No | No | No |
> | espreiteiros | No | No | No |
> | países de serviço de peering | No | No | No |
> | localização de serviços de espre éming | No | No | No |
> | peeringserviceproviders | No | No | No |
> | serviços de espreiteiros | No | No | No |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | eventos de política | No | No | No |
> | estados de política | No | No | No |
> | recursos de recursos de pista de política | No | No | No |
> | remediações | No | No | No |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | Consolas do | No | No | No |
> | dashboards | Yes | Yes | No |
> | utilizadores | No | No | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | trabalhopascoscollections | Yes | Yes | No |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | capacidades | Yes | Yes | No |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ---------- |
> | accounts | No | No | No |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ---------- |
> | accounts | **pendente** | **pendente** | No |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | contas disponíveis | No | No | No |
> | registos de fornecedores | No | No | No |
> | rollouts | No | No | No |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | áreas de trabalho | No | No | No |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!IMPORTANT]
> Consulte a [orientação dos Serviços de Recuperação](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | replicationeligibilityres | No | No | No |
> | cofres | Yes | Sim | Não.<br/><br/> A movimentação de cofres dos Serviços de Recuperação para o Azure Backup em todas as regiões de Azure não é suportada.<br/><br/> Nos cofres dos Serviços de Recuperação para recuperação do local de Azure, pode [desativar e recriar o cofre](../../site-recovery/move-vaults-across-regions.md) na região alvo. |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatopenShift

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | aglomerados de turnos abertos | No | No | No |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | espaços de nome | Yes | Yes | No |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | consultas | Yes | Yes | No |
> | resourcechangedetails | No | No | No |
> | mudanças de recursos | No | No | No |
> | resources | No | No | No |
> | recursos história | No | No | No |
> | subscriçõesstatus | No | No | No |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | fontes infantis | No | No | No |
> | emergentes | No | No | No |
> | eventos | No | No | No |
> | do IdP | No | No | No |
> | notificações | No | No | No |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | implementações | No | No | No |
> | descrições de implementação | No | No | Yes<br/><br/>[Mover recursos da Microsoft.Resources para uma nova região](microsoft-resources-move-regions.md) |
> | implementações / registos | No | No | No |
> | ligações | No | No | No |
> | fornecedores | No | No | No |
> | grupos de recursos | No | No | No |
> | resources | No | No | No |
> | assinaturas | No | No | No |
> | etiquetas | No | No | No |
> | modelospecs | No | No | Yes<br/><br/>[Mover recursos da Microsoft.Resources para uma nova região](microsoft-resources-move-regions.md) |
> | modelos / versões | No | No | No |
> | inquilinos | No | No | No |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | aplicações | Yes | No | No |
> | saasresources | No | No | No |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!IMPORTANT]
> Não se pode mover vários recursos de pesquisa em diferentes regiões numa só operação. Em vez disso, mova-os em operações separadas.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | resourcehealthmetadata | No | No | No |
> | serviços de pesquisa | Yes | Yes | No |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | adaptíostas de redes | No | No | No |
> | advancedthreatprotectionsettings | No | No | No |
> | alerts | No | No | No |
> | permitidas ligações | No | No | No |
> | listas de aplicaçõeswhitelistings | No | No | No |
> | avaliaçãometadata | No | No | No |
> | avaliações | No | No | No |
> | autodismissalertsrules | No | No | No |
> | automações | Yes | Yes | No |
> | autoprovisionings | No | No | No |
> | resultados de conformidade | No | No | No |
> | conformidades | No | No | No |
> | datacollectionagentes | No | No | No |
> | grupos de dispositivos de insegurança | No | No | No |
> | descobertas sões de inseguranças | No | No | No |
> | sões de segurança externa | No | No | No |
> | políticas de proteção de informação | No | No | No |
> | soluções de iotsecuritys | Yes | Yes | No |
> | iotsecuritysolutions / analyticsmodels | No | No | No |
> | iotsecuritysolutions / analyticsmodels / agregados | No | No | No |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | No | No | No |
> | jitnetworkacsspolicies | No | No | No |
> | políticas | No | No | No |
> | preços | No | No | No |
> | padrões de regulação | No | No | No |
> | padrões de regulação /regulatórios | No | No | No |
> | padrões de regulação / regulatórios de controlo /regulatórios | No | No | No |
> | contactos de segurança | No | No | No |
> | garantias de segurança | No | No | No |
> | securitysolutionsreferencedata | No | No | No |
> | estados de segurança | No | No | No |
> | securitystatusessummaries | No | No | No |
> | servidorvulnerabilityaments | No | No | No |
> | definições | No | No | No |
> | subassessments | No | No | No |
> | tarefas | No | No | No |
> | topologias | No | No | No |
> | espaços de trabalho | No | No | No |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | agregações | No | No | No |
> | alertas | No | No | No |
> | alertaruletemplas | No | No | No |
> | regras automation | No | No | No |
> | marcadores | No | No | No |
> | casos | No | No | No |
> | dataconnectors | No | No | No |
> | entidades | No | No | No |
> | entidadequeries | No | No | No |
> | Incidentes | No | No | No |
> | escritórioconsents | No | No | No |
> | definições | No | No | No |
> | ameaçaintelligence | No | No | No |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | serviços de consola | No | No | No |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | portais | No | No | No |
> | osdes | No | No | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | espaços de nome | Yes | Yes | No |
> | premiummessaging regiões | No | No | No |
> | sku | No | No | No |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | aplicações | No | No | No |
> | aglomerados | Yes | Yes | No |
> | grupos de contentores | No | No | No |
> | conjuntos de contentores | No | No | No |
> | edgeclusters | No | No | No |
> | geremclusters | No | No | No |
> | redes | No | No | No |
> | lojas secretas | No | No | No |
> | volumes | No | No | No |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | aplicações | Yes | Yes | No |
> | grupos de contentores | No | No | No |
> | portais | Yes | Yes | No |
> | redes | Yes | Yes | No |
> | segredos | Yes | Yes | No |
> | volumes | Yes | Yes | No |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | rollouts | No | No | No |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | sinaleiro | Yes | Yes | No |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | híbridosbenefits | No | No | No |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | aplicações dedefinições | No | No | No |
> | aplicações | No | No | No |
> | jitrequests | No | No | No |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> Uma base de dados e um servidor devem estar no mesmo grupo de recursos. Quando se move um servidor SQL, todas as suas bases de dados também são movidas. Este comportamento aplica-se às bases de dados Azure SQL Database e Azure Synapse Analytics.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | instâncias | No | No | No |
> | Locais | Yes | Yes | No |
> | managedinstances | No | No | Yes <br/><br/> [Saiba mais](../../azure-sql/database/move-resources-across-regions.md) sobre a mudança de instâncias geridas em todas as regiões. |
> | managedinstances / bases de dados | No | No | Yes |
> | servidores | Yes | Yes |Yes |
> | servidores / bases de dados | Yes | Yes | Yes <br/><br/> [Saiba mais](../../azure-sql/database/move-resources-across-regions.md) sobre a mudança de bases de dados em regiões.<br/><br/> [Saiba mais](../../resource-mover/tutorial-move-region-sql.md) sobre a utilização do Azure Resource Mover para mover bases de dados Azure SQL.  |
> | servidores / bases de dados / backuplongtermretentionpolicies | Yes | Yes | No |
> | servidores / elásticos | Yes | Yes | Yes <br/><br/> [Saiba mais](../../azure-sql/database/move-resources-across-regions.md) sobre a movimentação de piscinas elásticas em regiões.<br/><br/> [Saiba mais](../../resource-mover/tutorial-move-region-sql.md) sobre a utilização do Azure Resource Mover para mover piscinas elásticas Azure SQL.  |
> | servidores / contas de emprego | Yes | Yes | No |
> | servidores / agentes de trabalho | Yes | Yes | No |
> | virtualclusters | Yes | Yes | Yes |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | grupos sqlvirtualmachineinegroups | Yes | Yes | No |
> | sqlvirtualmachines | Yes | Yes | No |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | contas de armazenamento | Yes | Yes | Yes<br/><br/> [Mover uma conta de Armazenamento Azure para outra região](../../storage/common/storage-account-move.md) |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | caches | No | No | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Yes | Yes | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | No | No | No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | No | No | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | gestores | No | No | No |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!IMPORTANT]
> Os trabalhos da Stream Analytics não podem ser movidos quando estão em funcionamento.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | aglomerados | No | No | No |
> | streamingjobs | Yes | Yes | No |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | ambientes | No | No | No |
> | casos | No | No | No |

## <a name="microsoftsubscription"></a>Microsoft.Subscrição

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | assinaturas | No | No | No |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | services | No | No | No |
> | bilhetes de apoio | No | No | No |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | áreas de trabalho | No | No | No |
> | espaços de trabalho / bigdatapools | No | No | No |
> | espaços de trabalho / sqlpools | No | No | No |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | ambientes | Yes | Yes | No |
> | ambientes / fontes de eventos | Yes | Yes | No |
> | ambientes / conjuntos de dados de referência | Yes | Yes | No |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | lojas | Yes | Yes | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | imagens | No | No | No |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!IMPORTANT]
> Para alterar a subscrição de Azure DevOps, consulte [alterar a subscrição Azure utilizada para faturação](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | conta | No | No | No |
> | conta / extensão | No | No | No |
> | conta / projeto | No | No | No |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | arczones | No | No | No |
> | recursos | No | No | No |
> | vcenters | No | No | No |
> | virtualmachines | No | No | No |
> | virtualmachinetemplates | No | No | No |
> | virtualnetworks | No | No | No |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | não-100 dedicados | No | No | No |
> | serviços decloud dedicados | No | No | No |
> | virtualmachines | No | No | No |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | dispositivos | No | No | No |
> | vnfs | No | No | No |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | No | No | No |
> | planos | No | No | No |
> | assinaturas registadas | No | No | No |

## <a name="microsoftweb"></a>Microsoft.Web

> [!IMPORTANT]
> Consulte a [orientação do movimento do Serviço de Aplicações](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | disponíveis mochilas | No | No | No |
> | billingmeters | No | No | No |
> | certificados | No | Yes | No |
> | vias de conexão | Yes | Yes | No |
> | conexões | Yes | Yes | No |
> | customapis | Yes | Yes | No |
> | deletedsites | No | No | No |
> | implantações | No | No | No |
> | geo-regiões | No | No | No |
> | hospedar ambientes | No | No | No |
> | kubeenvironments | Yes | Yes | No |
> | editores | No | No | No |
> | recomendações | No | No | No |
> | resourcehealthmetadata | No | No | No |
> | tempos de execução | No | No | No |
> | serverfarms | Yes | Yes | No |
> | serverfarms / eventgridfilters | No | No | No |
> | sites | Yes | Yes | No |
> | sites / premieraddons | Yes | Yes | No |
> | sites / slots | Yes | Yes | No |
> | controlos de fontes | No | No | No |
> | estáticasites | No | No | No |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | multipleactivationkeys | No | No | No |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | serviços de dispositivos | No | No | No |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | cargas de trabalho | No | No | No |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição | Movimento da região |
> | ------------- | ----------- | ---------- | ----------- |
> | componentes | No | No | No |
> | consumada de componentes | No | No | No |
> | monitorinstâncias | No | No | No |
> | monitorinstancessummary | No | No | No |
> | monitores | No | No | No |

## <a name="third-party-services"></a>Serviços de terceiros

Os serviços de terceiros não suportam atualmente a operação de mudança.

## <a name="next-steps"></a>Passos seguintes

- Para que os comandos movimentem recursos, consulte [mover recursos para novo grupo de recursos ou subscrição](move-resource-group-and-subscription.md).
- [Saiba mais](../../resource-mover/overview.md) sobre o serviço Demôr Recursos.
- Para obter os mesmos dados que um ficheiro de valores separados por vírgula, faça o download [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
