---
title: Mover suporte de operação por tipo de recurso
description: Lista os tipos de recursos Azure que podem ser transferidos para um novo grupo de recursos ou subscrição.
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 0b1304b273523d42ad62f4e392fb803e1c174b69
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937530"
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
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços de domínio | Não | Não |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | diagnósticos | Não | Não |
> | diagnósticoscategorias | Não | Não |
> | privatelinkforazuread | Sim | Sim |
> | inquilinos | Sim | Sim |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | supportproviders | Não | Não |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | Não | Não |
> | adds serviços | Não | Não |
> | agentes | Não | Não |
> | anónimos | Não | Não |
> | configuração | Não | Não |
> | registos | Não | Não |
> | relatórios | Não | Não |
> | serviços de saúde | Não | Não |
> | services | Não | Não |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | configurações | Não | Não |
> | gerar recommendações | Não | Não |
> | do IdP | Não | Não |
> | recomendações | Não | Não |
> | supressões | Não | Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | regras de ação | Sim | Sim |
> | alerts | Não | Não |
> | lista de alertas | Não | Não |
> | alertasmetadata | Não | Não |
> | alertasummary | Não | Não |
> | alertasummarylista | Não | Não |
> | smartdetectoralertrules | Sim | Sim |
> | grupos inteligentes | Não | Não |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | servidores | Sim | Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!IMPORTANT]
> Um serviço de Gestão API que está definido para o SKU de consumo não pode ser movido.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | reportfeedback | Não | Não |
> | serviço | Sim | Sim |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | lojas de configurações | Sim | Sim |
> | lojas de configurações / filtros de eventos | Não | Não |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | primavera | Sim | Sim |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!IMPORTANT]
> Consulte a [orientação do movimento do Serviço de Aplicações](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | apiapps | Não | Não |
> | appidentidades | Não | Não |
> | portais | Não | Não |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | atesstationproviders | Sim | Sim |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | classicadministradores | Não | Não |
> | dataaliases | Não | Não |
> | denyassignments | Não | Não |
> | elevateaccess | Não | Não |
> | findorphanroleassignments | Não | Não |
> | fechaduras | Não | Não |
> | permissões | Não | Não |
> | assinaturas políticas | Não | Não |
> | definições de políticas | Não | Não |
> | políticas dedefinições | Não | Não |
> | privatelinkassociations | Não | Não |
> | recursos gestagementprivatelinks | Não | Não |
> | roleassignments | Não | Não |
> | roleassignmentsusagemetrics | Não | Não |
> | papéis dedefinições | Não | Não |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!IMPORTANT]
> Os runbooks devem existir no mesmo grupo de recursos que a Conta De Automação.
>
> Para obter informações, consulte [mover a sua conta Azure Automation para outra subscrição](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas de automação | Sim | Sim |
> | contas/configurações de automação | Sim | Sim |
> | contas de automação / runbooks | Sim | Sim |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | nuvens privadas | Sim | Sim |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | b2direões | Sim | Sim |
> | b2ctenants | Não | Não |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | datacontrollers | Não | Não |
> | híbridos gerentes | Não | Não |
> | pós-insinstâncias | Não | Não |
> | sqlinstances | Não | Não |
> | sqlmanagedinstances | Não | Não |
> | sqlserverinstances | Não | Não |
> | sqlserverregistrations | Sim | Sim |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | Não | Não |
> | registos | Sim | Sim |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | Não | Não |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | lotaccounts | Sim | Sim |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas de faturação | Não | Não |
> | faturantes | Não | Não |
> | faturações | Não | Não |
> | billingproperty | Não | Não |
> | billingroleassignments | Não | Não |
> | billingroledefinitions | Não | Não |
> | departamentos | Não | Não |
> | matrículas | Não | Não |
> | faturas | Não | Não |
> | transferências | Não | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | mapapis | Não | Não |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | biztalk | Não | Não |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | membros blockchain | Não | Não |
> | cordamembers | Não | Não |
> | observadores | Não | Não |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços de token | Não | Não |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Não | Não |
> | plantas | Não | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | botservices | Sim | Sim |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!IMPORTANT]
> Se a cache Azure para a instância Redis estiver configurada com uma rede virtual, o caso não pode ser movido para uma subscrição diferente. Ver [limitações de movimento de rede](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | redis | Sim | Sim |
> | redisenterprise | Não | Não |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | reservações aplicadas | Não | Não |
> | calcular a troca | Não | Não |
> | calcular preço | Não | Não |
> | calcular o preço da compra | Não | Não |
> | catálogos | Não | Não |
> | ordenadores de reservação comercial | Não | Não |
> | troca | Não | Não |
> | encomendas de reservas | Não | Não |
> | reservas | Não | Não |
> | resources | Não | Não |
> | validação encomenda de reservas | Não | Não |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | cdnwebapplication firewallmanagedrulesets | Não | Não |
> | cdnwebapplicationfirewallpolicies | Sim | Sim |
> | edgenodes | Não | Não |
> | perfis | Sim | Sim |
> | perfis / pontos finais | Sim | Sim |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!IMPORTANT]
> Consulte a [orientação do movimento do Serviço de Aplicações](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | certificadores | Sim | Sim |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> Consulte a [orientação do movimento de implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | capacidades | Não | Não |
> | temas de domínio | Sim | Não |
> | quotas | Não | Não |
> | recursos | Não | Não |
> | valida a subscrição demovedabilidade | Não | Não |
> | virtualmachines | Sim | Sim |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!IMPORTANT]
> Consulte a [orientação do movimento de implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | Não | Não |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!IMPORTANT]
> Consulte a [orientação do movimento de implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | capacidades | Não | Não |
> | expressroute crossconnections | Não | Não |
> | expressroutecrossconnections / peerings | Não | Não |
> | gatewaysupporteddes | Não | Não |
> | networksecuritygroups | Não | Não |
> | quotas | Não | Não |
> | reservedips | Não | Não |
> | virtualnetworks | Não | Não |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!IMPORTANT]
> Consulte a [orientação do movimento de implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | discos | Não | Não |
> | imagens | Não | Não |
> | osimagems | Não | Não |
> | osplatformimages | Não | Não |
> | imagens públicas | Não | Não |
> | quotas | Não | Não |
> | contas de armazenamento | Sim | Não |
> | vmimages | Não | Não |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!IMPORTANT]
> Consulte a [orientação do movimento de implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | cartão de taxa | Não | Não |
> | usageaggregates | Não | Não |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> Veja [a orientação das Máquinas Virtuais.](./move-limitations/virtual-machines-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | conjuntos de disponibilidades | Sim | Sim |
> | diskaccesses | Não | Não |
> | conjuntos de diskencrypations | Não | Não |
> | discos | Sim | Sim |
> | galerias | Não | Não |
> | galerias / imagens | Não | Não |
> | galerias / imagens / versões | Não | Não |
> | grupos de anfitriões | Não | Não |
> | grupos de anfitriões / anfitriões | Não | Não |
> | imagens | Sim | Sim |
> | grupos de destacamentos de proximidade | Sim | Sim |
> | restaurar as recordações de pontos | Não | Não |
> | restaurar acolecções / pontos de restauro | Não | Não |
> | partilha de milhões de pessoas | Não | Não |
> | sharedvmimages | Não | Não |
> | sharedvmimages / versões | Não | Não |
> | instantâneos | Sim | Sim |
> | sshpublickeys | Não | Não |
> | virtualmachines | Sim | Sim |
> | virtualmachines /extensões | Sim | Sim |
> | virtualmachinescalesets | Sim | Sim |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | agregados de cussão | Não | Não |
> | saldos | Não | Não |
> | orçamentos | Não | Não |
> | encargos | Não | Não |
> | costtags | Não | Não |
> | créditos | Não | Não |
> | eventos | Não | Não |
> | previsões | Não | Não |
> | lotes | Não | Não |
> | mercados | Não | Não |
> | folhas de preços | Não | Não |
> | produtos | Não | Não |
> | detalhes de reservas | Não | Não |
> | reservas recommendaçõesdedetails | Não | Não |
> | reservas recommendações | Não | Não |
> | reservas | Não | Não |
> | transações de reservas | Não | Não |
> | etiquetas | Não | Não |
> | inquilinos | Não | Não |
> | termos | Não | Não |
> | usagedetails | Não | Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | grupos de contentores | Não | Não |
> | ligações de serviços de ações | Não | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | registos | Sim | Sim |
> | registos / agentpools | Sim | Sim |
> | registos / buildtasks | Sim | Sim |
> | registos / replicações | Sim | Sim |
> | registos / tarefas | Sim | Sim |
> | registos / webhooks | Sim | Sim |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços de contentores | Não | Não |
> | geremclusters | Não | Não |
> | aglomerados de gestão de turnos abertos | Não | Não |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações | Não | Não |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | alerts | Não | Não |
> | contas de faturação | Não | Não |
> | orçamentos | Não | Não |
> | cloudconnectors | Não | Não |
> | conectores | Sim | Sim |
> | departamentos | Não | Não |
> | dimensões | Não | Não |
> | matrículas | Não | Não |
> | exportações | Não | Não |
> | contas de falcão externos | Não | Não |
> | forecast | Não | Não |
> | consulta | Não | Não |
> | registar | Não | Não |
> | configs relatório | Não | Não |
> | relatórios | Não | Não |
> | definições | Não | Não |
> | showbackrules | Não | Não |
> | Modos de exibição | Não | Não |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | hubs | Não | Não |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | pedidos | Não | Não |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | associações | Não | Não |
> | recursosproviders | Sim | Sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | empregos | Não | Não |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | availableskus | Não | Não |
> | databoxedgedevices | Sim | Sim |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | áreas de trabalho | Não | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | catálogos | Sim | Sim |
> | datacatalogs | Não | Não |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | gerentes de conexão | Não | Não |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | pacotes | Não | Não |
> | planos | Não | Não |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | datafactories | Sim | Sim |
> | fábricas | Sim | Sim |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Não | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | services | Não | Não |
> | serviços / projetos | Não | Não |
> | slots | Não | Não |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | backupvaults | Não | Não |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | servidores | Sim | Sim |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | flexibleServers | Sim | Sim |
> | servidores | Sim | Sim |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DbforPostgresql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | flexibleServers | Sim | Sim |
> | grupos de servidores | Não | Não |
> | servidores | Sim | Sim |
> | servidorv2 | Sim | Sim |
> | solteiroservers | Sim | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | artefactos | Sim | Sim |
> | rollouts | Sim | Sim |
> | topologias de serviço | Sim | Sim |
> | topologias de serviços / serviços | Sim | Sim |
> | topologias de serviços / serviços / unidades de serviço | Sim | Sim |
> | passos | Sim | Sim |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | grupos de aplicações | Sim | Sim |
> | hostpools | Sim | Sim |
> | áreas de trabalho | Sim | Sim |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | elásticos | Não | Não |
> | elásticos / iothubtenants | Não | Não |
> | iothubs | Sim | Sim |
> | serviços de provisionamento | Sim | Sim |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | oleodutos | Sim | Sim |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | controladores | Sim | Sim |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | centros de laboratório | Não | Não |
> | laboratórios | Sim | Não |
> | laboratórios / ambientes | Sim | Sim |
> | laboratórios / corredores de serviço | Sim | Sim |
> | laboratórios / virtualmachines | Sim | Não |
> | horários | Sim | Sim |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Não | Não |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | números de dados | Não | Não |
> | contas de base de dados | Sim | Sim |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | domínios | Sim | Sim |
> | gerassorequest | Não | Não |
> | topleveldomains | Não | Não |
> | validação da informação sobreregistration | Não | Não |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | services | Sim | Sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | domínios | Sim | Sim |
> | subscrições de eventos | Não - não pode ser movido de forma independente, mas automaticamente movido com recurso subscrito. | Não - não pode ser movido de forma independente, mas automaticamente movido com recurso subscrito. |
> | extensõestópicas | Não | Não |
> | partnernamespaces | Sim | Sim |
> | registos de parceiros | Não | Não |
> | partnertopics | Sim | Sim |
> | sistematópicos | Sim | Sim |
> | tópicos | Sim | Sim |
> | tópicos | Não | Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | Sim | Sim |
> | espaços de nome | Sim | Sim |
> | sku | Não | Não |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | espaços de trabalho experimenta | Não | Não |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | espaços de nome | Sim | Sim |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | recursosproviders | Não | Não |
> | funcionalidades | Não | Não |
> | fornecedores | Não | Não |
> | subscrições de registos de assinaturas | Não | Não |

## <a name="microsoftgenomics"></a>Microsoft.Geómica

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | auto-administradores | Não | Não |
> | autogeridavmconfigurationprofils | Não | Não |
> | convidadoconfigurationasssignments | Não | Não |
> | software | Não | Não |
> | softwareupdateprofile | Não | Não |
> | datas de software | Não | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | hanainstances | Não | Não |
> | sapmonitors | Não | Não |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | dedicados | Não | Não |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!IMPORTANT]
> Pode mover os clusters HDInsight para um novo grupo de subscrição ou recursos. No entanto, não é possível mover-se através de subscrições os recursos de networking ligados ao cluster HDInsight (como a rede virtual, NIC ou equilibrador de carga). Além disso, não é possível mover-se para um novo grupo de recursos um NIC que esteja ligado a uma máquina virtual para o cluster.
>
> Ao mover um cluster HDInsight para uma nova subscrição, primeiro mova outros recursos (como a conta de armazenamento). Em seguida, mova o cluster HDInsight por si só.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | Sim | Sim |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | services | Sim | Sim |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | máquinas | Sim | Sim |
> | máquinas / extensões | Sim | Sim |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | datamanagers | Sim | Sim |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | dispositivos | Não | Não |
> | vnfs | Não | Não |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | componentes | Não | Não |
> | networkscopes | Não | Não |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | empregos | Sim | Sim |

## <a name="microsoftinsights"></a>microsoft.insights

> [!IMPORTANT]
> Certifique-se de que a mudança para uma nova subscrição não excede as [quotas de subscrição](azure-subscription-service-limits.md#azure-monitor-limits).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | grupos de ação | Sim | Sim |
> | activitylogalerts | Não | Não |
> | alertas | Sim | Sim |
> | autoescala | Sim | Sim |
> | linha de base | Não | Não |
> | componentes | Sim | Sim |
> | datacollectionrules | Não | Não |
> | diagnósticos | Não | Não |
> | diagnósticoscategorias | Não | Não |
> | eventcategorias | Não | Não |
> | tipos de eventos | Não | Não |
> | diagnósticosgnósticos estendidos | Não | Não |
> | guestdiagnosticsettings | Não | Não |
> | listmigrationdate | Não | Não |
> | início de ções | Não | Não |
> | logprofiles | Não | Não |
> | registos | Não | Não |
> | metricalerts | Não | Não |
> | bases métricas | Não | Não |
> | metricbatch | Não | Não |
> | métricas | Não | Não |
> | espaços de nomes métricos | Não | Não |
> | metrics | Não | Não |
> | migratealertrules | Não | Não |
> | migrar modelo dericing | Não | Não |
> | livros de trabalho | Não | Não |
> | grupos de notificações | Não | Não |
> | privatelinkscopes | Não | Não |
> | modelo de backbacktolegacypricing | Não | Não |
> | queryrules agendados | Sim | Sim |
> | topology | Não | Não |
> | transações | Não | Não |
> | vminsightsonboardingstatuses | Não | Não |
> | webtests | Sim | Sim |
> | webtests / obtertestesultfile | Não | Não |
> | livros | Sim | Sim |
> | estalagens de livros | Sim | Sim |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | apliques | Não | Não |
> | iotapps | Sim | Sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | gráfico | Sim | Sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!IMPORTANT]
> Os Cofres-Chave utilizados para encriptação de discos não podem ser transferidos para um grupo de recursos na mesma subscrição ou através de subscrições.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | deletedvaults | Não | Não |
> | hsmpools | Não | Não |
> | geredhsmos | Não | Não |
> | cofres | Sim | Sim |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados conectados | Sim | Sim |
> | assinaturas registadas | Não | Não |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | configurações de controlo de fontes | Não | Não |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | Sim | Sim |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas de laboratório | Não | Não |
> | utilizadores | Não | Não |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | hospedar ambientes | Não | Não |
> | contas de integração | Sim | Sim |
> | integração de ambientes | Sim | Não |
> | integração de ambientesvírus /managedapis | Sim | Não |
> | ambientes isolados | Não | Não |
> | fluxos de trabalho | Sim | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | planos de compromisso | Não | Não |
> | serviços web | Sim | Não |
> | áreas de trabalho | Sim | Sim |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | grupos de operacionalização | Não | Não |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |
> | contas de equipa | Não | Não |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | áreas de trabalho | Não | Não |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | configurações de assinaturas | Não | Não |
> | configurações de manutenção | Sim | Sim |
> | atualizações | Não | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | identidades | Não | Não |
> | usassignedidentidades | Não | Não |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | managednetworks | Não | Não |
> | gerednetworks / grupos de redes geridas | Não | Não |
> | managednetworks / managednetworkpeeringpolicies | Não | Não |
> | notificação | Não | Não |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | Não | Não |
> | inscrições | Não | Não |
> | definições de registos | Não | Não |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | getentities | Não | Não |
> | managementgroups | Não | Não |
> | grupos de gestão / configurações | Não | Não |
> | resources | Não | Não |
> | filtro de backback starttenant | Não | Não |
> | inquilinosfillstatus | Não | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |
> | contas / privateatlases | Sim | Sim |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | ofertas | Não | Não |
> | tipos de ofertas | Não | Não |
> | privadas | Não | Não |
> | privatestoreclient | Não | Não |
> | lojas privadas | Não | Não |
> | produtos | Não | Não |
> | editores | Não | Não |
> | registar | Não | Não |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Não | Não |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | acordos | Não | Não |
> | tipos de ofertas | Não | Não |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | mediaservices | Sim | Sim |
> | mediaservices / liveevents | Sim | Sim |
> | mediaservices / streamingendpoints | Sim | Sim |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | apclusters | Não | Não |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | projetos de avaliação | Não | Não |
> | migrarprojects | Não | Não |
> | câmaras de mudança | Não | Não |
> | projetos | Não | Não |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | holográficosbroscastaccounts | Não | Não |
> | contas objectunderstanding | Não | Não |
> | contas de reconeradores remotos | Sim | Sim |
> | spatialanchorsaccounts | Sim | Sim |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas netappac | Não | Não |
> | contas de netappacs /capacitypools | Não | Não |
> | contas de netappacs / capacitypools / volumes | Não | Não |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> Consulte [a orientação do movimento em rede](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | pistas de aplicação | Não | Não |
> | applicationgatewaywebapplicationfirewallpolicies | Não | Não |
> | grupos de aplicações | Sim | Sim |
> | azurefirewalls | Não | Não |
> | bastionhosts | Não | Não |
> | bgpservicecommunidades | Não | Não |
> | conexões | Sim | Sim |
> | ddoscustompolicias | Sim | Sim |
> | ddosprotectionplans | Não | Não |
> | dnszones | Sim | Sim |
> | expressroutecircuits | Não | Não |
> | vias expressas | Não | Não |
> | expressrouteserviceproviders | Não | Não |
> | firewallpolicies | Sim | Sim |
> | frontdoors | Não | Não |
> | ipallocations | Sim | Sim |
> | ipgrupos | Sim | Sim |
> | loadbalancers | Sim - SKU Básico<br> Sim - SKU Padrão | Sim - SKU Básico<br>Não - SKU Padrão |
> | vias de rede locais | Sim | Sim |
> | natgateways | Não | Não |
> | projetos de bioperiment de rede | Não | Não |
> | políticas de networkintent | Sim | Sim |
> | networkinterfaces | Sim | Sim |
> | redes de mentofils | Não | Não |
> | networksecuritygroups | Sim | Sim |
> | observadores de rede | Não | Não |
> | observadores de rede / suportes de conexão | Sim | Não |
> | networkwatchers / fluxologs | Sim | Não |
> | observadores de rede / pingmeshes | Sim | Não |
> | p2svpngateways | Não | Não |
> | zonas privadas | Sim | Sim |
> | privatednszones / virtualnetworklinks | Sim | Sim |
> | privatednszonesinternal | Não | Não |
> | privateendpointredirectmaps | Não | Não |
> | pontos privados | Não | Não |
> | serviços de privatelink | Não | Não |
> | endereços publicitários | Sim - SKU Básico<br>Sim - SKU Padrão | Sim - SKU Básico<br>Não - SKU Padrão |
> | publicipprefixos | Sim | Sim |
> | filtros de rota | Não | Não |
> | routetables | Sim | Sim |
> | segurançapartnerproviders | Sim | Sim |
> | serviceendpointpolicies | Sim | Sim |
> | trafficmanagergeographichierarchies | Não | Não |
> | trafficmanagerprofiles | Sim | Sim |
> | trafficmanagerprofiles / heatmaps | Não | Não |
> | trafficmanagerusermetricskeys | Não | Não |
> | virtualhubs | Não | Não |
> | vias virtualnetworkgateways | Sim | Sim |
> | virtualnetworks | Sim | Sim |
> | virtualnetworktaps | Não | Não |
> | virtualrouters | Sim | Sim |
> | virtualwans | Não | Não |
> | VPNgateways (Wan Virtual) | Não | Não |
> | vpnsnserverconfigurations | Não | Não |
> | vpnsites (WAN virtual) | Não | Não |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | espaços de nome | Sim | Sim |
> | espaços de nomes / notificações | Sim | Sim |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | osnamepas | Sim | Sim |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | hipervsites | Não | Não |
> | importações | Não | Não |
> | servidores | Não | Não |
> | vmwaresites | Não | Não |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!IMPORTANT]
> Certifique-se de que a mudança para uma nova subscrição não exceda as [quotas de subscrição](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Espaços de trabalho que têm uma conta de automação ligada não podem ser movidos. Antes de iniciar uma operação de movimento, certifique-se de desvincular quaisquer contas de automação.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | Não | Não |
> | espaços de trabalho apagados | Não | Não |
> | linktargets | Não | Não |
> | storageinsightconfigs | Não | Não |
> | áreas de trabalho | Sim | Sim |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | sociedades de gestão | Não | Não |
> | configurações de gestão | Sim | Sim |
> | soluções | Sim | Sim |
> | Modos de exibição | Sim | Sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | legacypeerings | Não | Não |
> | peerasns | Não | Não |
> | peeringlocações | Não | Não |
> | espreiteiros | Não | Não |
> | países de serviço de peering | Não | Não |
> | localização de serviços de espre éming | Não | Não |
> | peeringserviceproviders | Não | Não |
> | serviços de espreiteiros | Não | Não |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | eventos de política | Não | Não |
> | estados de política | Não | Não |
> | recursos de recursos de pista de política | Não | Não |
> | remediações | Não | Não |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Consolas do | Não | Não |
> | dashboards | Sim | Sim |
> | utilizadores | Não | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | trabalhopascoscollections | Sim | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | capacidades | Sim | Sim |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas disponíveis | Não | Não |
> | registos de fornecedores | Não | Não |
> | rollouts | Não | Não |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | áreas de trabalho | Não | Não |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!IMPORTANT]
> Consulte a [orientação dos Serviços de Recuperação](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | replicationeligibilityres | Não | Não |
> | cofres | Sim | Sim |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatopenShift

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados de turnos abertos | Não | Não |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | espaços de nome | Sim | Sim |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | consultas | Sim | Sim |
> | resourcechangedetails | Não | Não |
> | mudanças de recursos | Não | Não |
> | resources | Não | Não |
> | recursos história | Não | Não |
> | subscriçõesstatus | Não | Não |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | fontes infantis | Não | Não |
> | emergentes | Não | Não |
> | eventos | Não | Não |
> | do IdP | Não | Não |
> | notificações | Não | Não |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | implementações | Não | Não |
> | descrições de implementação | Não | Não |
> | implementações / registos | Não | Não |
> | ligações | Não | Não |
> | fornecedores | Não | Não |
> | grupos de recursos | Não | Não |
> | resources | Não | Não |
> | assinaturas | Não | Não |
> | etiquetas | Não | Não |
> | modelospecs | Não | Não |
> | modelos / versões | Não | Não |
> | inquilinos | Não | Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações | Sim | Não |
> | saasresources | Não | Não |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!IMPORTANT]
> Não se pode mover vários recursos de pesquisa em diferentes regiões numa só operação. Em vez disso, mova-os em operações separadas.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | resourcehealthmetadata | Não | Não |
> | serviços de pesquisa | Sim | Sim |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | adaptíostas de redes | Não | Não |
> | advancedthreatprotectionsettings | Não | Não |
> | alerts | Não | Não |
> | permitidas ligações | Não | Não |
> | listas de aplicaçõeswhitelistings | Não | Não |
> | avaliaçãometadata | Não | Não |
> | avaliações | Não | Não |
> | autodismissalertsrules | Não | Não |
> | automações | Sim | Sim |
> | autoprovisionings | Não | Não |
> | resultados de conformidade | Não | Não |
> | conformidades | Não | Não |
> | datacollectionagentes | Não | Não |
> | grupos de dispositivos de insegurança | Não | Não |
> | descobertas sões de inseguranças | Não | Não |
> | sões de segurança externa | Não | Não |
> | políticas de proteção de informação | Não | Não |
> | soluções de iotsecuritys | Sim | Sim |
> | iotsecuritysolutions / analyticsmodels | Não | Não |
> | iotsecuritysolutions / analyticsmodels / agregados | Não | Não |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Não | Não |
> | jitnetworkacsspolicies | Não | Não |
> | políticas | Não | Não |
> | preços | Não | Não |
> | padrões de regulação | Não | Não |
> | padrões de regulação /regulatórios | Não | Não |
> | padrões de regulação / regulatórios de controlo /regulatórios | Não | Não |
> | contactos de segurança | Não | Não |
> | garantias de segurança | Não | Não |
> | securitysolutionsreferencedata | Não | Não |
> | estados de segurança | Não | Não |
> | securitystatusessummaries | Não | Não |
> | servidorvulnerabilityaments | Não | Não |
> | definições | Não | Não |
> | subassessments | Não | Não |
> | tarefas | Não | Não |
> | topologias | Não | Não |
> | espaços de trabalho | Não | Não |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | agregações | Não | Não |
> | alertas | Não | Não |
> | alertaruletemplas | Não | Não |
> | regras automation | Não | Não |
> | marcadores | Não | Não |
> | casos | Não | Não |
> | dataconnectors | Não | Não |
> | entidades | Não | Não |
> | entidadequeries | Não | Não |
> | Incidentes | Não | Não |
> | escritórioconsents | Não | Não |
> | definições | Não | Não |
> | ameaçaintelligence | Não | Não |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços de consola | Não | Não |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | portais | Não | Não |
> | osdes | Não | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | espaços de nome | Sim | Sim |
> | premiummessaging regiões | Não | Não |
> | sku | Não | Não |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações | Não | Não |
> | aglomerados | Sim | Sim |
> | grupos de contentores | Não | Não |
> | conjuntos de contentores | Não | Não |
> | edgeclusters | Não | Não |
> | geremclusters | Não | Não |
> | redes | Não | Não |
> | lojas secretas | Não | Não |
> | volumes | Não | Não |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações | Sim | Sim |
> | grupos de contentores | Não | Não |
> | portais | Sim | Sim |
> | redes | Sim | Sim |
> | segredos | Sim | Sim |
> | volumes | Sim | Sim |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | rollouts | Não | Não |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | sinaleiro | Sim | Sim |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | híbridosbenefits | Não | Não |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações dedefinições | Não | Não |
> | aplicações | Não | Não |
> | jitrequests | Não | Não |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> Uma base de dados e um servidor devem estar no mesmo grupo de recursos. Quando se move um servidor SQL, todas as suas bases de dados também são movidas. Este comportamento aplica-se às bases de dados Azure SQL Database e Azure Synapse Analytics.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | instâncias | Não | Não |
> | Locais | Sim | Sim |
> | managedinstances | Não | Não |
> | servidores | Sim | Sim |
> | servidores / bases de dados | Sim | Sim |
> | servidores / bases de dados / backuplongtermretentionpolicies | Sim | Sim |
> | servidores / elásticos | Sim | Sim |
> | servidores / contas de emprego | Sim | Sim |
> | servidores / agentes de trabalho | Sim | Sim |
> | virtualclusters | Sim | Sim |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | grupos sqlvirtualmachineinegroups | Sim | Sim |
> | sqlvirtualmachines | Sim | Sim |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas de armazenamento | Sim | Sim |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | caches | Não | Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Sim | Sim |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Não | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Não | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | gestores | Não | Não |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!IMPORTANT]
> Os trabalhos da Stream Analytics não podem ser movidos quando estão em funcionamento.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | Não | Não |
> | streamingjobs | Sim | Sim |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | ambientes | Não | Não |
> | casos | Não | Não |

## <a name="microsoftsubscription"></a>Microsoft.Subscrição

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | assinaturas | Não | Não |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | services | Não | Não |
> | bilhetes de apoio | Não | Não |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | áreas de trabalho | Sim | Sim |
> | espaços de trabalho / bigdatapools | Sim | Sim |
> | espaços de trabalho / sqlpools | Sim | Sim |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | ambientes | Sim | Sim |
> | ambientes / fontes de eventos | Sim | Sim |
> | ambientes / conjuntos de dados de referência | Sim | Sim |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | lojas | Sim | Sim |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | imagens | Não | Não |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!IMPORTANT]
> Para alterar a subscrição de Azure DevOps, consulte [alterar a subscrição Azure utilizada para faturação](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | conta | Não | Não |
> | conta / extensão | Não | Não |
> | conta / projeto | Não | Não |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | arczones | Não | Não |
> | recursos | Não | Não |
> | vcenters | Não | Não |
> | virtualmachines | Não | Não |
> | virtualmachinetemplates | Não | Não |
> | virtualnetworks | Não | Não |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | não-100 dedicados | Não | Não |
> | serviços decloud dedicados | Não | Não |
> | virtualmachines | Não | Não |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | dispositivos | Não | Não |
> | vnfs | Não | Não |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |
> | planos | Não | Não |
> | assinaturas registadas | Não | Não |

## <a name="microsoftweb"></a>Microsoft.Web

> [!IMPORTANT]
> Consulte a [orientação do movimento do Serviço de Aplicações](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | disponíveis mochilas | Não | Não |
> | billingmeters | Não | Não |
> | certificados | Não | Sim |
> | vias de conexão | Sim | Sim |
> | conexões | Sim | Sim |
> | customapis | Sim | Sim |
> | deletedsites | Não | Não |
> | implantações | Não | Não |
> | geo-regiões | Não | Não |
> | hospedar ambientes | Não | Não |
> | kubeenvironments | Sim | Sim |
> | editores | Não | Não |
> | recomendações | Não | Não |
> | resourcehealthmetadata | Não | Não |
> | tempos de execução | Não | Não |
> | serverfarms | Sim | Sim |
> | serverfarms / eventgridfilters | Não | Não |
> | sites | Sim | Sim |
> | sites / premieraddons | Sim | Sim |
> | sites / slots | Sim | Sim |
> | controlos de fontes | Não | Não |
> | estáticasites | Não | Não |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | multipleactivationkeys | Não | Não |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços de dispositivos | Não | Não |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | cargas de trabalho | Não | Não |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | componentes | Não | Não |
> | consumada de componentes | Não | Não |
> | monitorinstâncias | Não | Não |
> | monitorinstancessummary | Não | Não |
> | monitores | Não | Não |

## <a name="third-party-services"></a>Serviços de terceiros

Os serviços de terceiros não suportam atualmente a operação de mudança.

## <a name="next-steps"></a>Passos seguintes

Para que os comandos movimentem recursos, consulte [mover recursos para novo grupo de recursos ou subscrição](move-resource-group-and-subscription.md).

Para obter os mesmos dados que um ficheiro de valores separados por vírgula, faça o download [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
