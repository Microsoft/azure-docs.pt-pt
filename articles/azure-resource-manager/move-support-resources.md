---
title: Mover o suporte de operação pelo tipo de recurso do Azure
description: Lista os tipos de recursos do Azure que podem ser movidos para um novo grupo de recursos ou assinatura.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 10/24/2019
ms.author: tomfitz
ms.openlocfilehash: ba594a2bc0f0cb50eb515a24255a3f9ad56a10a3
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931880"
---
# <a name="move-operation-support-for-resources"></a>Mover o suporte de operação para recursos
Este artigo lista se um tipo de recurso do Azure dá suporte à operação de movimentação. Ele também fornece informações sobre condições especiais a serem consideradas ao mover um recurso.

Ir para um namespace do provedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. aadiam](#microsoftaadiam)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. batch](#microsoftbatch)
> - [Microsoft. BatchAI](#microsoftbatchai)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. BizTalkservices](#microsoftbiztalkservices)
> - [Microsoft. Blockchain](#microsoftblockchain)
> - [Microsoft. Blueprint](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft. ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. Cognitivaservices](#microsoftcognitiveservices)
> - [Microsoft. Compute](#microsoftcompute)
> - [Microsoft. Container](#microsoftcontainer)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. ContainerService](#microsoftcontainerservice)
> - [Microsoft. ContentModerator](#microsoftcontentmoderator)
> - [Microsoft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft. Data Box](#microsoftdatabox)
> - [Microsoft. DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. databricks](#microsoftdatabricks)
> - [Microsoft. datacatalog](#microsoftdatacatalog)
> - [Microsoft. DataConnect](#microsoftdataconnect)
> - [Microsoft. DataExchange](#microsoftdataexchange)
> - [Microsoft. datafactory](#microsoftdatafactory)
> - [Microsoft. datalake](#microsoftdatalake)
> - [Microsoft. DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft. DataLakeStore](#microsoftdatalakestore)
> - [Microsoft. datamigration](#microsoftdatamigration)
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft. DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft. DBforMySQL](#microsoftdbformysql)
> - [Microsoft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft. DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. Devices](#microsoftdevices)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. DevTestLab](#microsoftdevtestlab)
> - [Microsoft. DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. genomas](#microsoftgenomics)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. insights](#microsoftinsights)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft. keyvault](#microsoftkeyvault)
> - [Microsoft. Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft. Locationservices](#microsoftlocationservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft. MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft. MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft. MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. migrar](#microsoftmigrate)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft. Network](#microsoftnetwork)
> - [Microsoft. NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. emparelhamento](#microsoftpeering)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PortalSdk](#microsoftportalsdk)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. ProjectOxford](#microsoftprojectoxford)
> - [Microsoft. Recoveryservices](#microsoftrecoveryservices)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. Scheduler](#microsoftscheduler)
> - [Microsoft. Search](#microsoftsearch)
> - [Microsoft. Security](#microsoftsecurity)
> - [Microsoft. ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. infabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft. SqlVM](#microsoftsqlvm)
> - [Microsoft. Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft. TerraformOSS](#microsoftterraformoss)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | DomainServices | Não | Não |
> | DomainServices/replicasets | Não | Não |

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locatários | Não | Não |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | actionrules | Sim | Sim |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | servidores | Sim | Sim |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviço | Sim | Sim |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | configurationstores | Sim | Sim |

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | apiapps | Não | Não |
> | appidentities | Não | Não |
> | gateways | Não | Não |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação do serviço de aplicativo](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft. Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | policyassignments | Não | Não |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Sim | Sim |
> | automationaccounts/configurações | Sim | Sim |
> | automationaccounts/runbooks | Sim | Sim |

> [!IMPORTANT]
> Os Runbooks devem existir no mesmo grupo de recursos que a conta de automação.

## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Sim | Sim |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | sqlserverregistrations | Não | Não |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | registros | Sim | Sim |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Sim | Sim |

## <a name="microsoftbatchai"></a>Microsoft. BatchAI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | clusters | Não | Não |
> | servidores | Não | Não |
> | Sejam | Não | Não |
> | espaços | Não | Não |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | mapapis | Não | Não |

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkservices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | biztalk | Sim | Sim |

## <a name="microsoftblockchain"></a>Microsoft. Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Sim | Sim |
> | observadores | Não | Não |

## <a name="microsoftblueprint"></a>Microsoft. Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Não | Não |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | botservices | Sim | Sim |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Redis | Sim | Sim |

> [!IMPORTANT]
> Se o cache do Azure para instância Redis estiver configurado com uma rede virtual, a instância não poderá ser movida para uma assinatura diferente. Consulte [limitações de movimentação de rede](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Não | Não |
> | perfis | Sim | Sim |
> | perfis/pontos de extremidade | Sim | Sim |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | certificateorders | Sim | Sim |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação do serviço de aplicativo](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | DomainNames | Sim | Não |
> | VirtualMachines | Sim | Não |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação da implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos entre assinaturas com uma operação específica para esse cenário.

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Não | Não |
> | reservedips | Não | Não |
> | virtualnetworks | Não | Não |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação da implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos entre assinaturas com uma operação específica para esse cenário.

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Sim | Não |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação da implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos entre assinaturas com uma operação específica para esse cenário.

## <a name="microsoftcognitiveservices"></a>Microsoft. Cognitivaservices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Sim | Sim |
> | diskencryptionsets | Não | Não |
> | discos | Sim | Sim |
> | galerias | Não | Não |
> | galerias/imagens | Não | Não |
> | galerias/imagens/versões | Não | Não |
> | hosts | Não | Não |
> | hosts/hosts | Não | Não |
> | imagens | Sim | Sim |
> | proximityplacementgroups | Não | Não |
> | restorepointcollections | Não | Não |
> | sharedvmimages | Não | Não |
> | sharedvmimages/versões | Não | Não |
> | instantâneos | Sim | Sim |
> | VirtualMachines | Sim | Sim |
> | VirtualMachines/extensões | Sim | Sim |
> | virtualmachinescalesets | Sim | Sim |

> [!IMPORTANT]
> Consulte as [diretrizes de movimentação de máquinas virtuais](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftcontainer"></a>Microsoft. Container

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | containergroups | Não | Não |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | containergroups | Não | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | registros | Sim | Sim |
> | registros/BuildTasks | Sim | Sim |
> | registros/replicações | Sim | Sim |
> | registros/tarefas | Sim | Sim |
> | registros/WebHooks | Sim | Sim |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contêinerservices | Não | Não |
> | managedclusters | Não | Não |
> | openshiftmanagedclusters | Não | Não |

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações | Sim | Sim |

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | conectores | Sim | Sim |

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | hubs | Sim | Sim |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | resourceproviders | Sim | Sim |

## <a name="microsoftdatabox"></a>Microsoft. Data Box

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Sejam | Não | Não |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Não | Não |

## <a name="microsoftdatabricks"></a>Microsoft. databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | espaços | Não | Não |

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | catálogos | Sim | Sim |
> | catálogos de | Não | Não |

## <a name="microsoftdataconnect"></a>Microsoft. DataConnect

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Não | Não |

## <a name="microsoftdataexchange"></a>Microsoft. DataExchange

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | compacta | Não | Não |
> | Plan | Não | Não |

## <a name="microsoftdatafactory"></a>Microsoft. datafactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | datafactories | Sim | Sim |
> | fábricas | Sim | Sim |

## <a name="microsoftdatalake"></a>Microsoft. datalake

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Não | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |

## <a name="microsoftdatamigration"></a>Microsoft. datamigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | services | Não | Não |
> | serviços/projetos | Não | Não |
> | períodos | Não | Não |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | servidores | Sim | Sim |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | servidores | Sim | Sim |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | servergroups | Não | Não |
> | servidores | Sim | Sim |
> | serversv2 | Sim | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | artifactsources | Sim | Sim |
> | distribuições | Sim | Sim |
> | pertopologias | Sim | Sim |
> | serviços e pertopologias | Sim | Sim |
> | pertopologias/serviços/unidades de serviço | Sim | Sim |
> | passos | Sim | Sim |

## <a name="microsoftdevices"></a>Microsoft. Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | elasticpools | Não | Não |
> | elasticpools / iothubtenants | Não | Não |
> | iothubs | Sim | Sim |
> | provisioningservices | Sim | Sim |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Controladores | Sim | Sim |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | labcenters | Não | Não |
> | prático | Sim | Não |
> | laboratórios/ambientes | Sim | Sim |
> | laboratórios/perrunners | Sim | Sim |
> | laboratórios/VirtualMachines | Sim | Não |
> | agendamento | Sim | Sim |

## <a name="microsoftdocumentdb"></a>Microsoft. DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | Sim | Sim |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | domínios | Sim | Sim |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | services | Sim | Sim |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | domínios | Sim | Sim |
> | Tópicos | Sim | Sim |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | clusters | Sim | Sim |
> | Namespaces | Sim | Sim |

## <a name="microsoftgenomics"></a>Microsoft. genomas

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | hanainstances | Não | Não |
> | sapmonitors | Sim | Sim |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | clusters | Sim | Sim |

> [!IMPORTANT]
> Você pode mover os clusters do HDInsight para uma nova assinatura ou grupo de recursos. No entanto, você não pode mover entre assinaturas os recursos de rede vinculados ao cluster HDInsight (como a rede virtual, NIC ou balanceador de carga). Além disso, você não pode mover para um novo grupo de recursos uma NIC que está anexada a uma máquina virtual para o cluster.
>
> Ao mover um cluster HDInsight para uma nova assinatura, primeiro mova outros recursos (como a conta de armazenamento). Em seguida, mova o cluster HDInsight por si só.

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | services | Sim | Sim |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | maquina | Não | Não |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | gerenciadores de DataManager | Sim | Sim |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Sejam | Sim | Sim |

## <a name="microsoftinsights"></a>Microsoft. insights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |
> | actiongroups | Sim | Sim |
> | activitylogalerts | Não | Não |
> | alertrules | Sim | Sim |
> | autoscalesettings | Sim | Sim |
> | QC | Sim | Sim |
> | guestdiagnosticsettings | Não | Não |
> | metricalerts | Não | Não |
> | notificationgroups | Não | Não |
> | notificationrules | Não | Não |
> | scheduledqueryrules | Sim | Sim |
> | webtests | Sim | Sim |
> | pastas | Sim | Sim |

> [!IMPORTANT]
> Certifique-se de que a mudança para a nova assinatura não exceda as [cotas de assinatura](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | iotapps | Sim | Sim |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Sim | Sim |
> | Graph | Sim | Sim |

## <a name="microsoftkeyvault"></a>Microsoft. keyvault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | hsmpools | Não | Não |
> | cofres | Sim | Sim |

> [!IMPORTANT]
> Os cofres de chaves usados para criptografia de disco não podem ser movidos para um grupo de recursos na mesma assinatura ou entre assinaturas.

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | clusters | Sim | Sim |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | labaccounts | Não | Não |

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftlocationservices"></a>Microsoft. Locationservices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Não | Não |
> | integrationaccounts | Sim | Sim |
> | integrationserviceenvironments | Não | Não |
> | isolatedenvironments | Não | Não |
> | fluxos | Sim | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Sim | Sim |
> | WebServices | Sim | Não |
> | espaços | Sim | Sim |

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Sim | Sim |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |
> | contas/espaços de trabalho | Não | Não |
> | contas/espaços de trabalho/projetos | Não | Não |
> | teamaccounts | Não | Não |
> | teamaccounts/espaços de trabalho | Não | Não |
> | teamaccounts/espaços de trabalho/projetos | Não | Não |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft. MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | Não | Não |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | espaços | Não | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | userassignedidentities | Não | Não |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Não | Não |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | mediaservices | Sim | Sim |
> | mediaservices/liveevents | Sim | Sim |
> | mediaservices/streamingendpoints | Sim | Sim |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | appclusters | Não | Não |

## <a name="microsoftmigrate"></a>Microsoft. migrar

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Não | Não |
> | migrateprojects | Não | Não |
> | projeto | Não | Não |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Não | Não |
> | netappaccounts / capacitypools | Não | Não |
> | netappaccounts/capacitypools/volumes | Não | Não |
> | netappaccounts/capacitypools/volumes/mounttargets | Não | Não |
> | netappaccounts/capacitypools/volumes/instantâneos | Não | Não |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Não | Não |
> | applicationgatewaywebapplicationfirewallpolicies | Não | Não |
> | applicationsecuritygroups | Sim | Sim |
> | azurefirewalls | Sim | Sim |
> | bastionhosts | Não | Não |
> | Conexões | Sim | Sim |
> | ddoscustompolicies | Sim | Sim |
> | ddosprotectionplans | Não | Não |
> | dnszones | Sim | Sim |
> | expressroutecircuits | Não | Não |
> | expressroutecrossconnections | Não | Não |
> | expressroutegateways | Não | Não |
> | expressrouteports | Não | Não |
> | frontdoors | Não | Não |
> | frontdoorwebapplicationfirewallpolicies | Não | Não |
> | loadbalancers | Sim-SKU básico<br>Não-SKU padrão | Sim-SKU básico<br>Não-SKU padrão |
> | localnetworkgateways | Sim | Sim |
> | natgateways | Sim | Sim |
> | networkintentpolicies | Sim | Sim |
> | NetworkInterfaces | Sim | Sim |
> | networkprofiles | Não | Não |
> | networksecuritygroups | Sim | Sim |
> | networkwatchers | Sim | Sim |
> | networkwatchers / connectionmonitors | Sim | Sim |
> | networkwatchers/lentes | Sim | Sim |
> | networkwatchers / pingmeshes | Sim | Sim |
> | p2svpngateways | Não | Não |
> | privatednszones | Sim | Sim |
> | privatednszones / virtualnetworklinks | Sim | Sim |
> | privateendpoints | Não | Não |
> | privatelinkservices | Não | Não |
> | publicipaddresses | Sim-SKU básico<br>Não-SKU padrão | Sim-SKU básico<br>Não-SKU padrão |
> | publicipprefixes | Sim | Sim |
> | routefilters | Não | Não |
> | routetables | Sim | Sim |
> | securegateways | Sim | Sim |
> | serviceendpointpolicies | Sim | Sim |
> | trafficmanagerprofiles | Sim | Sim |
> | virtualhubs | Não | Não |
> | virtualnetworkgateways | Sim | Sim |
> | virtualnetworks | Sim | Sim |
> | virtualnetworktaps | Não | Não |
> | virtualwans | Não | Não |
> | vpngateways (WAN virtual) | Não | Não |
> | vpnsites (WAN virtual) | Não | Não |
> | webapplicationfirewallpolicies | Sim | Sim |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação de rede](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Namespaces | Sim | Sim |
> | namespaces/notificationhubs | Sim | Sim |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | espaços | Sim | Sim |

> [!IMPORTANT]
> Certifique-se de que a mudança para a nova assinatura não exceda as [cotas de assinatura](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | managementconfigurations | Sim | Sim |
> | soluções | Sim | Sim |
> | Modos de exibição | Sim | Sim |

## <a name="microsoftpeering"></a>Microsoft. emparelhamento

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | emparelhamentos | Não | Não |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Painéis | Sim | Sim |

## <a name="microsoftportalsdk"></a>Microsoft. PortalSdk

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | rootresources | Não | Não |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Sim | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | unidades | Sim | Sim |

## <a name="microsoftprojectoxford"></a>Microsoft. ProjectOxford

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftrecoveryservices"></a>Microsoft. Recoveryservices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | cofres | Sim | Sim |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação dos serviços de recuperação](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Namespaces | Sim | Sim |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | consultas de | Sim | Sim |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações | Sim | Não |

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Fluxo | Sim | Sim |
> | gratuitas | Sim | Sim |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | pesquisarservices | Sim | Sim |

> [!IMPORTANT]
> Você não pode mover vários recursos de pesquisa em regiões diferentes em uma única operação. Em vez disso, mova-os em operações separadas.

## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | iotsecuritysolutions | Sim | Sim |
> | playbookconfigurations | Não | Não |

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | gateways | Não | Não |
> | nós | Não | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Namespaces | Sim | Sim |

## <a name="microsoftservicefabric"></a>Microsoft. infabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações | Não | Não |
> | clusters | Sim | Sim |
> | clusters/aplicativos | Não | Não |
> | containergroups | Não | Não |
> | containergroupsets | Não | Não |
> | edgeclusters | Não | Não |
> | às | Não | Não |
> | secretstores | Não | Não |
> | volumes | Não | Não |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações | Sim | Sim |
> | containergroups | Não | Não |
> | gateways | Sim | Sim |
> | às | Sim | Sim |
> | confidenciais | Sim | Sim |
> | volumes | Sim | Sim |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | signalr | Sim | Sim |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | appliancedefinitions | Não | Não |
> | dispositivos | Não | Não |
> | applicationdefinitions | Não | Não |
> | aplicações | Não | Não |
> | jitrequests | Não | Não |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | instancepools | Não | Não |
> | managedinstances | Não | Não |
> | ManagedInstances/bancos de dados | Não | Não |
> | servidores | Sim | Sim |
> | servidores/bancos de dados | Sim | Sim |
> | servidores/elasticpools | Sim | Sim |
> | virtualclusters | Sim | Sim |

> [!IMPORTANT]
> Um banco de dados e um servidor devem estar no mesmo grupo de recursos. Quando você move um SQL Server, todos os seus bancos de dados também são movidos. Esse comportamento se aplica ao banco de dados SQL do Azure e ao Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Sim | Sim |
> | sqlvirtualmachines | Sim | Sim |

## <a name="microsoftsqlvm"></a>Microsoft. SqlVM

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | dwvm | Não | Não |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Sim | Sim |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | caches | Não | Não |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Sim | Sim |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Não | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Não | Não |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | esses | Não | Não |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | streamingjobs | Sim | Sim |

> [!IMPORTANT]
> Stream Analytics trabalhos não podem ser movidos quando estiver em estado de execução.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | sistemas | Não | Não |
> | ambientes/EventSources | Não | Não |
> | instâncias | Não | Não |
> | instâncias/ambientes | Não | Não |
> | instâncias/ambientes/EventSources | Não | Não |

## <a name="microsoftterraformoss"></a>Microsoft. TerraformOSS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Não | Não |
> | Os | Não | Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | sistemas | Sim | Sim |
> | ambientes/EventSources | Sim | Sim |
> | ambientes/referencedatasets | Sim | Sim |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | mercado | Não | Não |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Não | Não |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | conta | Sim | Sim |
> | conta/extensão | Sim | Sim |
> | conta/projeto | Sim | Sim |

> [!IMPORTANT]
> Para alterar a assinatura do Azure DevOps, consulte [alterar a assinatura do Azure usada para cobrança](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Não | Não |
> | dedicatedcloudservices | Não | Não |
> | VirtualMachines | Não | Não |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Certificado | Não | Sim |
> | connectiongateways | Sim | Sim |
> | Conexões | Sim | Sim |
> | customapis | Sim | Sim |
> | hostingenvironments | Não | Não |
> | serverfarms | Sim | Sim |
> | sites | Sim | Sim |
> | sites/premieraddons | Sim | Sim |
> | sites/Slots | Sim | Sim |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação do serviço de aplicativo](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | dispositivos | Não | Não |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft. WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Não | Não |
> | hostpools | Não | Não |
> | espaços | Não | Não |

## <a name="third-party-services"></a>Serviços de terceiros

Os serviços de terceiros atualmente não dão suporte à operação de movimentação.

## <a name="next-steps"></a>Passos seguintes
Para obter comandos para mover recursos, consulte [mover recursos para um novo grupo de recursos ou assinatura](resource-group-move-resources.md).

Para obter os mesmos dados como um arquivo de valores separados por vírgula, baixe [move-support-Resources. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
