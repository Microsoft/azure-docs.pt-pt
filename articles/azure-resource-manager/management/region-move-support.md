---
title: Apoio à deslocação de recursos do Azure pelas regiões
description: Lista os tipos de recursos Azure que podem ser movidos através das regiões de Azure
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 07/21/2020
ms.author: raynew
ms.openlocfilehash: 70f981f2763dd36f0f417faec6c81e168e9856e7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87040965"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Apoio à deslocação de recursos do Azure pelas regiões

Este artigo confirma se um tipo de recurso Azure é suportado para se deslocar para outra região de Azure. 

Salte para um espaço de nome de fornecedor de recursos:
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
> - [Microsoft.AzureData](#microsoftazuredata)
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
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DbforPostgresql](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Geómica](#microsoftgenomics)
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
> - [Microsoft.MachineLearningModelagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperacionalização](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
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
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
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
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- | 
> | serviços de domínio | No | 
> | serviços de domínio / conjuntos de réplicas | No | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | inquilinos | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | regras de ação | No | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | servidores | No |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | serviço |  Sim (usando o modelo) <br/><br/> [Mover a API Management através das regiões.](../../api-management/api-management-howto-migrate.md) | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | lojas de configurações | No | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | apiapps | Sim (usando o modelo)<br/><br/> [Mover uma app de Serviço de Aplicações para outra região](../../app-service/manage-move-across-regions.md) | 
> | appidentidades | No | 
> | portais | No | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | assinaturas políticas | No |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | contas de automação | Sim (usando o modelo) <br/><br/> [Utilização de geo-replicação](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | contas/configurações de automação | No | 
> | contas de automação / runbooks | No | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | b2direões | No | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | sqlserverregistrations | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | registos | No | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | lotaccounts |  As contas do lote não podem ser movidas diretamente de uma região para outra, mas você pode usar um modelo para exportar um modelo, modificá-lo e implementar o modelo para a nova região. <br/><br/> Saiba como [mover uma conta batch através de regiões](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aglomerados | No <br/><br/> O serviço Azure Batch AI está [reformado.](/previous-versions/azure/batch-ai/overview-what-happened-batch-ai)
> | arqui-gravadores | No | 
> | empregos | No | 
> | áreas de trabalho | No | 

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
> | observadores | No | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | blueprintassignments | No | 

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


## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | No |
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
> | temas de domínio | Não está previsto nenhum trabalho para serviços clássicos.
> | virtualmachines | No | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | networksecuritygroups | Não está previsto nenhum trabalho para serviços clássicos.
> | reservedips | No | 
> | virtualnetworks | No | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | contas de armazenamento | Yes |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | No | 
> | Cognitive Search | Suportado com passos manuais.<br/><br/> Saiba como [mover o seu serviço de Pesquisa Cognitiva Azure para outra região](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | conjuntos de disponibilidades | No | 
> | conjuntos de diskencrypations | No | 
> | discos | No | 
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
> | virtualmachines | Yes | 
> | virtualmachines /extensões | No | 
> | virtualmachinescalesets | No | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | grupos de contentores | No | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | grupos de contentores | No | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | registos | No |  
> | registos / buildtasks | No |  
> | registos / replicações | No | 
> | registos / tarefas | No |  
> | registos / webhooks | No | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | serviços de contentores | Não.<br/><br/> O serviço [está reformado.](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/)
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
> | conectores | No |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | hubs | No |  

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
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

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | elásticos | Não. O recurso não está exposto.
> | elásticos / iothubtenants | Não. O recurso não está exposto.
> | iothubs | Yes. [Saiba mais](../../iot-hub/iot-hub-how-to-clone.md)
> | serviços de provisionamento | No | 

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

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | contas de base de dados | No | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | domínios | No | 

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
> | tópicos | No | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aglomerados | No |  
> | espaços de nome | Sim (com modelo)<br/><br/> [Mover um espaço de nomes do Event Hub para outra região](../../event-hubs/move-across-regions.md) | 

## <a name="microsoftgenomics"></a>Microsoft.Geómica

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | hanainstances | No | 
> | sapmonitors | No |  

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

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | datamanagers |  No | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | empregos |  No | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | No | 
> | grupos de ação |  No | 
> | activitylogalerts | No | 
> | alertas |  No | 
> | autoescala |  No | 
> | componentes |  No |  
> | guestdiagnosticsettings | No | 
> | metricalerts | No | 
> | grupos de notificações | No | 
> | regras de notificação | No | 
> | queryrules agendados |  No | 
> | webtests |  No | 
> | livros |  No |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | checknamedilability |  Não.<br/><br/> A IoT Central trabalha com geografias, e não com regiões.
> | gráfico | No

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> |  Iothub |  Sim (centro clone) <br/><br/> [Clone um hub IoT para outra região](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | checknamedilability |  No |  
> | gráfico |  No | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | hsmpools | No | 
> | cofres |  No | 


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
> | contas / espaços de trabalho | No | 
> | contas / espaços de trabalho / projetos | No | 
> | contas de equipa | No | 
> | contas de equipa / espaços de trabalho | No | 
> | teamaccounts / workspaces / projetos | No | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperacionalização

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | contas de hospedagem | No | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | áreas de trabalho | No | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | usassignedidentidades | No | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts |  Não, a Azure Maps é um serviço geoespacial. 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | classicdevservices | Não está prevista nenhuma obra para serviços clássicos 

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
> | projetos | No | 

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
> | conexões |  No | 
> | ddoscustompolicias |  No | 
> | ddosprotectionplans | No | 
> | dnszones |  No | 
> | expressroutecircuits | No | 
> | expressroute crossconnections | No | 
> | vias expressas | No | 
> | expressrouteports | No | 
> | frontdoors | No | 
> | frontdoorwebapplicationfirewallpolicies | No | 
> | loadbalancers | Yes <br/><br/> Pode exportar a configuração existente como modelo e implementar o modelo na nova região. Saiba como mover um equilibrador de carga [externo](../..//load-balancer/move-across-regions-external-load-balancer-portal.md) ou [interno.](../../load-balancer/move-across-regions-internal-load-balancer-portal.md) |
> | vias de rede locais |  No | 
> | natgateways |  No | 
> | políticas de networkintent |  No | 
> | networkinterfaces | Yes | 
> | redes de mentofils | No | 
> | networksecuritygroups | Yes | 
> | observadores de rede |  No |  
> | observadores de rede / suportes de conexão |  No | 
> | observadores de rede / lentes |  No | 
> | observadores de rede / pingmeshes |  No | 
> | p2svpngateways | No | 
> | zonas privadas |  No |  
> | privatednszones / virtualnetworklinks |  No |  
> | pontos privados | No | 
> | serviços de privatelink | No | 
> | endereços publicitários | Yes<br/><br/> Pode exportar a configuração de endereço IP público existente como modelo e implementar o modelo na nova região. [Saiba mais](../../virtual-network/move-across-regions-publicip-portal.md) sobre como mover um endereço IP público. |
> | publicipprefixos | No | 
> | filtros de rota | No | 
> | routetables |  No | 
> | serviceendpointpolicies |  No | 
> | trafficmanagerprofiles |  No | 
> | virtualhubs | No | 
> | vias virtualnetworkgateways |  No |  
> | virtualnetworks |  No | 
> | virtualnetworktaps | No | 
> | virtualwans | No | 
> | VPNgateways (Wan Virtual) | No | 
> | vpnsites (WAN virtual) | No | 
> | webapplicationfirewallpolicies |  No | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | espaços de nome |  No | 
> | espaços de nomes / notificações |  No |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | áreas de trabalho |  No | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | configurações de gestão |  No | 
> | Modos de exibição |  No | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | espreiteiros | No | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | dashboards | No | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | rootresources | No | 

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

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | cofres | Não.<br/><br/> A movimentação de cofres dos Serviços de Recuperação para o Azure Backup em todas as regiões de Azure não é suportada.<br/><br/> Nos cofres dos Serviços de Recuperação para recuperação do local de Azure, pode [desativar e recriar o cofre](../../site-recovery/move-vaults-across-regions.md) na região alvo. | 


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

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aplicações |  No | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | flows |  No |  
> | trabalhocollectões |  No | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | serviços de pesquisa |  No | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | soluções de iotsecuritys |  No | 
> | configurações de playbook | No | 

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

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aplicações | No | 
> | aglomerados |  No | 
> | clusters / aplicações | No | 
> | grupos de contentores | No | 
> | conjuntos de contentores | No | 
> | edgeclusters | No | 
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

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | sinaleiro |  No |  

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aparelhos dedefinições | No | 
> | aparelhos | No | 
> | aplicações dedefinições | No | 
> | aplicações | No | 
> | jitrequests | No | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | instâncias | No | 
> | managedinstances | Yes | 
> | managedinstances / bases de dados | Yes | 
> | servidores | Yes | 
> | servidores / bases de dados | Yes | 
> | servidores / elásticos | Yes | 
> | virtualclusters | Yes | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | grupos sqlvirtualmachineinegroups |  No |  
> | sqlvirtualmachines |  No |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | dwvm | No | 

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
> | streamingjobs |  No |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | ambientes | No | 
> | ambientes / fontes de eventos | No | 
> | casos | No | 
> | instâncias / ambientes | No | 
> | instâncias / ambientes / fontes de eventos | No | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | registos de fornecedores | No | 
> | resources | No | 

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



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | não-100 dedicados | No | 
> | serviços decloud dedicados | No | 
> | virtualmachines | No | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | certificados | No | 
> | vias de conexão |  No |  
> | conexões |  No |  
> | customapis |  No | 
> | hospedar ambientes | No | 
> | serverfarms |  No |  
> | sites |  No | 
> | sites / premieraddons |  No |  
> | sites / slots |  No |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | serviços de dispositivos | No | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | grupos de aplicações | No | 
> | hostpools | No | 
> | áreas de trabalho | No | 

## <a name="third-party-services"></a>Serviços de terceiros

Os serviços de terceiros não suportam atualmente a operação de mudança.
