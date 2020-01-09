---
title: Suporte para mover recursos do Azure entre regiões
description: Lista os tipos de recursos do Azure que podem ser movidos entre regiões do Azure
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 60b8708458e081d66514e092edc9ef9af7c33494
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75485250"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Suporte para mover recursos do Azure entre regiões

Este artigo confirma se há suporte para um tipo de recurso do Azure para a mudança para outra região do Azure. 

Ir para um namespace do provedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
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
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | DomainServices | Não | 
> | DomainServices/replicasets | Não | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | locatários | Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | actionrules | Não | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | servidores | Não |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | serviço | Não |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | configurationstores | Não | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | apiapps | Não | 
> | appidentities | Não | 
> | gateways | Não | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | policyassignments | Não |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | automationaccounts | Não | 
> | automationaccounts/configurações | Não | 
> | automationaccounts/runbooks | Não | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | b2cdirectories | Não | 

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | sqlserverregistrations | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | registros | Não | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | batchaccounts | Não |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | clusters | Não | 
> | servidores | Não | 
> | sejam | Não | 
> | áreas de trabalho | Não | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | mapapis | Não | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | biztalk | Não | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | blockchainmembers | Não |
> | observadores | Não | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | blueprintassignments | Não | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | botservices | Não | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | Redis | Não | 


## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Não |
> | profiles | Não | 
> | perfis/pontos de extremidade | Não | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | certificateorders | Não | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | DomainNames | Não |  
> | VirtualMachines | Não | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | networksecuritygroups | Não |
> | reservedips | Não | 
> | virtualnetworks | Não | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | storageaccounts | Sim |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | availabilitysets | Não | 
> | diskencryptionsets | Não | 
> | discos | Não | 
> | galerias | Não | 
> | galerias/imagens | Não | 
> | galerias/imagens/versões | Não | 
> | hosts | Não | 
> | hosts/hosts | Não | 
> | imagens | Não | 
> | proximityplacementgroups | Não | 
> | restorepointcollections | Não | 
> | sharedvmimages | Não | 
> | sharedvmimages/versões | Não | 
> | instantâneos | Não | 
> | VirtualMachines | Sim | 
> | VirtualMachines/extensões | Não | 
> | virtualmachinescalesets | Não | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | containergroups | Não | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | containergroups | Não | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | registros | Não |  
> | registros/BuildTasks | Não |  
> | registros/replicações | Não | 
> | registros/tarefas | Não |  
> | registros/WebHooks | Não | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | contêinerservices | Não | 
> | managedclusters | Não | 
> | openshiftmanagedclusters | Não | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | aplicações | Não | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | conectores | Não |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | hubs | Não |  

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | resourceproviders | Não | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | sejam | Não | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | databoxedgedevices | Não | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | áreas de trabalho | Não | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | catálogos | Não | 
> | catálogos de | Não | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | connectionmanagers | Não | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | compacta | Não | 
> | Plan | Não | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | datafactories | Não | 
> | fábricas | Não |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | datalakeaccounts | Não | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | services | Não | 
> | serviços/projetos | Não | 
> | períodos | Não | 

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | servidores | Não |  

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | servidores | Não |  

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | servergroups | Não | 
> | servidores | Não |  
> | serversv2 | Não | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | artifactsources | Não | 
> | distribuições | Não |  
> | pertopologias | Não | 
> | serviços e pertopologias | Não |  
> | pertopologias/serviços/unidades de serviço | Não | 
> | passos | Não | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | elasticpools | Não | 
> | elasticpools / iothubtenants | Não | 
> | iothubs | Sim | 
> | provisioningservices | Não | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | controladores | Não | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | labcenters | Não | 
> | prático | Não | 
> | laboratórios/ambientes | Não |  
> | laboratórios/perrunners | Não | 
> | laboratórios/VirtualMachines | Não |  
> | agendamento | Não |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | databaseaccounts | Não | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | domínios | Não | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | services | Não |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | domínios | Não |  
> | Tópicos do | Não | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | clusters | Não |  
> | namespaces | Não | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | hanainstances | Não | 
> | sapmonitors | Não |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | clusters | Não | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | services | Não |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | maquina | Não | 

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | gerenciadores de DataManager |  Não | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | sejam |  Não | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 
> | actiongroups |  Não | 
> | activitylogalerts | Não | 
> | alertrules |  Não | 
> | autoscalesettings |  Não | 
> | QC |  Não |  
> | guestdiagnosticsettings | Não | 
> | metricalerts | Não | 
> | notificationgroups | Não | 
> | notificationrules | Não | 
> | scheduledqueryrules |  Não | 
> | webtests |  Não | 
> | livros |  Não |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | iotapps |  Não |  

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | checknameavailability |  Não |  
> | graph |  Não | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | hsmpools | Não | 
> | cofres |  Não | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | clusters |  Não |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | labaccounts | Não | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | hostingenvironments | Não | 
> | integrationaccounts |  Não |  
> | integrationserviceenvironments | Não | 
> | isolatedenvironments | Não | 
> | fluxos |  Não |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | commitmentplans |  Não | 
> | WebServices |  Não | 
> | áreas de trabalho |  Não | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | operationalizationclusters |  Não | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 
> | contas/espaços de trabalho | Não | 
> | contas/espaços de trabalho/projetos | Não | 
> | teamaccounts | Não | 
> | teamaccounts/espaços de trabalho | Não | 
> | teamaccounts/espaços de trabalho/projetos | Não | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | hostingaccounts | Não | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | áreas de trabalho | Não | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | userassignedidentities | Não | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts |  Não |  

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | classicdevservices | Não | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | mediaservices |  Não | 
> | mediaservices/liveevents |  Não | 
> | mediaservices/streamingendpoints |  Não | 

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | appclusters | Não | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | assessmentprojects | Não | 
> | migrateprojects | Não | 
> | projeto | Não | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | netappaccounts | Não | 
> | netappaccounts / capacitypools | Não | 
> | netappaccounts/capacitypools/volumes | Não | 
> | netappaccounts/capacitypools/volumes/mounttargets | Não | 
> | netappaccounts/capacitypools/volumes/instantâneos | Não | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | applicationgateways | Não | 
> | applicationgatewaywebapplicationfirewallpolicies | Não | 
> | applicationsecuritygroups |  Não |  
> | azurefirewalls |  Não |  
> | bastionhosts | Não | 
> | conexões |  Não | 
> | ddoscustompolicies |  Não | 
> | ddosprotectionplans | Não | 
> | dnszones |  Não | 
> | expressroutecircuits | Não | 
> | expressroutecrossconnections | Não | 
> | expressroutegateways | Não | 
> | expressrouteports | Não | 
> | frontdoors | Não | 
> | frontdoorwebapplicationfirewallpolicies | Não | 
> | loadbalancers | Sim-SKU básico<br>Não-SKU padrão | Sim-SKU básico<br> -SKU padrão Sim |
> | localnetworkgateways |  Não | 
> | natgateways |  Não | 
> | networkintentpolicies |  Não | 
> | networkinterfaces | Sim | 
> | networkprofiles | Não | 
> | networksecuritygroups | Sim | 
> | networkwatchers |  Não |  
> | networkwatchers / connectionmonitors |  Não | 
> | networkwatchers/lentes |  Não | 
> | networkwatchers / pingmeshes |  Não | 
> | p2svpngateways | Não | 
> | privatednszones |  Não |  
> | privatednszones / virtualnetworklinks |  Não |  
> | privateendpoints | Não | 
> | privatelinkservices | Não | 
> | publicipaddresses | Sim-SKU básico<br>Não-SKU padrão | Sim-SKU básico<br>Não-SKU padrão |
> | publicipprefixes | Não | 
> | routefilters | Não | 
> | routetables |  Não | 
> | serviceendpointpolicies |  Não | 
> | trafficmanagerprofiles |  Não | 
> | virtualhubs | Não | 
> | virtualnetworkgateways |  Não |  
> | virtualnetworks |  Não | 
> | virtualnetworktaps | Não | 
> | virtualwans | Não | 
> | vpngateways (WAN virtual) | Não | 
> | vpnsites (WAN virtual) | Não | 
> | webapplicationfirewallpolicies |  Não | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | namespaces |  Não | 
> | namespaces/notificationhubs |  Não |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | áreas de trabalho |  Não | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | managementconfigurations |  Não | 
> | Modos de exibição |  Não | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | emparelhamentos | Não | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | dashboards | Não | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | rootresources | Não | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | workspacecollections |  Não | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | unidades |  Não | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | cofres | Sim (para cofres de backup, acho que? | 


## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | namespaces |  Não | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | consultas de |  Não |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | aplicações |  Não | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | fluxos |  Não |  
> | gratuitas |  Não | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | pesquisarservices |  Não | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  Não | 
> | playbookconfigurations | Não | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | gateways | Não | 
> | nós | Não | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | namespaces |  Não | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | aplicações | Não | 
> | clusters |  Não | 
> | clusters/aplicativos | Não | 
> | containergroups | Não | 
> | containergroupsets | Não | 
> | edgeclusters | Não | 
> | às | Não | 
> | secretstores | Não | 
> | volumes | Não | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | aplicações |  Não | 
> | containergroups | Não | 
> | gateways |  Não | 
> | às |  Não | 
> | confidenciais |  Não | 
> | volumes |  Não |  

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | signalr |  Não |  

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | appliancedefinitions | Não | 
> | dispositivos | Não | 
> | applicationdefinitions | Não | 
> | aplicações | Não | 
> | jitrequests | Não | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | instancepools | Não | 
> | managedinstances | Sim | 
> | ManagedInstances/bancos de dados | Sim | 
> | servidores | Sim | 
> | servidores/bancos de dados | Sim | 
> | servidores/elasticpools | Sim | 
> | virtualclusters | Sim | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  Não |  
> | sqlvirtualmachines |  Não |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | dwvm | Não | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | storageaccounts | Sim?? | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | Caches | Não | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | storagesyncservices |  Não | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | storagesyncservices | Não | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | storagesyncservices | Não | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | esses | Não | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | streamingjobs |  Não |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | sistemas | Não | 
> | ambientes/EventSources | Não | 
> | instâncias | Não | 
> | instâncias/ambientes | Não | 
> | instâncias/ambientes/EventSources | Não | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | providerregistrations | Não | 
> | recursos | Não | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | sistemas |  Não | 
> | ambientes/EventSources |  Não |  
> | ambientes/referencedatasets |  Não | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | mercado | Não | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | imagetemplates | Não | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | conta |  Não | 
> | conta/extensão |  Não | 
> | conta/projeto |  Não | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | Não | 
> | dedicatedcloudservices | Não | 
> | VirtualMachines | Não | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | certificados | Não | 
> | connectiongateways |  Não |  
> | conexões |  Não |  
> | customapis |  Não | 
> | hostingenvironments | Não | 
> | serverfarms |  Não |  
> | sites |  Não | 
> | sites/premieraddons |  Não |  
> | sites/Slots |  Não |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | dispositivos | Não | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | applicationgroups | Não | 
> | hostpools | Não | 
> | áreas de trabalho | Não | 

## <a name="third-party-services"></a>Serviços de terceiros

Os serviços de terceiros atualmente não dão suporte à operação de movimentação.
