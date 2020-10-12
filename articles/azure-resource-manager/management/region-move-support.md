---
title: Apoio à deslocação de recursos do Azure pelas regiões
description: Lista os tipos de recursos Azure que podem ser movidos através das regiões de Azure
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: dc931b910981578a3257c9131bea93cd836d1def
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945168"
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
> - [Microsoft.Resources](#microsoftresources)
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
> | serviços de domínio | Não | 
> | serviços de domínio / conjuntos de réplicas | Não | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | inquilinos | Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | regras de ação | Não | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | servidores | Não |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | serviço |  Sim (usando o modelo) <br/><br/> [Mover a API Management através das regiões.](../../api-management/api-management-howto-migrate.md) | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | lojas de configurações | Não | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | apiapps | Sim (usando o modelo)<br/><br/> [Mover uma app de Serviço de Aplicações para outra região](../../app-service/manage-move-across-regions.md) | 
> | appidentidades | Não | 
> | portais | Não | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | assinaturas políticas | Não |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | contas de automação | Sim (usando o modelo) <br/><br/> [Utilização de geo-replicação](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | contas/configurações de automação | Não | 
> | contas de automação / runbooks | Não | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | b2direões | Não | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | sqlserverregistrations | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | registos | Não | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | lotaccounts |  As contas do lote não podem ser movidas diretamente de uma região para outra, mas você pode usar um modelo para exportar um modelo, modificá-lo e implementar o modelo para a nova região. <br/><br/> Saiba como [mover uma conta batch através de regiões](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aglomerados | Não <br/><br/> O serviço Azure Batch AI está [reformado.](/previous-versions/azure/batch-ai/overview-what-happened-batch-ai)
> | arqui-gravadores | Não | 
> | empregos | Não | 
> | áreas de trabalho | Não | 

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
> | observadores | Não | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | blueprintassignments | Não | 

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


## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Não |
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
> | temas de domínio | Não está previsto nenhum trabalho para serviços clássicos.
> | virtualmachines | Não | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | networksecuritygroups | Não está previsto nenhum trabalho para serviços clássicos.
> | reservedips | Não | 
> | virtualnetworks | Não | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | contas de armazenamento | Sim |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | Não | 
> | Cognitive Search | Suportado com passos manuais.<br/><br/> Saiba como [mover o seu serviço de Pesquisa Cognitiva Azure para outra região](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | conjuntos de disponibilidades | Sim <br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover conjuntos de disponibilidade. | 
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
> | virtualmachines | Sim <br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover VMs Azure. | 
> | virtualmachines /extensões | Não | 
> | virtualmachinescalesets | Não | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | grupos de contentores | Não | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | grupos de contentores | Não | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | registos | Não |  
> | registos / buildtasks | Não |  
> | registos / replicações | Não | 
> | registos / tarefas | Não |  
> | registos / webhooks | Não | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | serviços de contentores | Não.<br/><br/> O serviço [está reformado.](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/)
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
> | conectores | Não |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | hubs | Não |  

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
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

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | elásticos | Não. O recurso não está exposto.
> | elásticos / iothubtenants | Não. O recurso não está exposto.
> | iothubs | Yes. [Saiba mais](../../iot-hub/iot-hub-how-to-clone.md)
> | serviços de provisionamento | Não | 

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

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | contas de base de dados | Não | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | domínios | Não | 

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
> | tópicos | Não | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aglomerados | Não |  
> | espaços de nome | Sim (com modelo)<br/><br/> [Mover um espaço de nomes do Event Hub para outra região](../../event-hubs/move-across-regions.md) | 

## <a name="microsoftgenomics"></a>Microsoft.Geómica

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | hanainstances | Não | 
> | sapmonitors | Não |  

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

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | datamanagers |  Não | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | empregos |  Não | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | Não. [Saiba mais](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region).
> | grupos de ação |  Não | 
> | activitylogalerts | Não | 
> | alertas |  Não | 
> | autoescala |  Não | 
> | componentes |  Não |  
> | guestdiagnosticsettings | Não | 
> | metricalerts | Não | 
> | grupos de notificações | Não | 
> | regras de notificação | Não | 
> | queryrules agendados |  Não | 
> | webtests |  Não | 
> | livros |  Não |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | checknamedilability |  Não.<br/><br/> A IoT Central trabalha com geografias, e não com regiões.
> | gráfico | Não

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> |  Iothub |  Sim (centro clone) <br/><br/> [Clone um hub IoT para outra região](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | checknamedilability |  Não |  
> | gráfico |  Não | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | hsmpools | Não | 
> | cofres |  Não | 


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
> | contas / espaços de trabalho | Não | 
> | contas / espaços de trabalho / projetos | Não | 
> | contas de equipa | Não | 
> | contas de equipa / espaços de trabalho | Não | 
> | teamaccounts / workspaces / projetos | Não | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperacionalização

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | contas de hospedagem | Não | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | áreas de trabalho | Não | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | usassignedidentidades | Não | 

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
> | projetos | Não | 

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
> | conexões |  Não | 
> | ddoscustompolicias |  Não | 
> | ddosprotectionplans | Não | 
> | dnszones |  Não | 
> | expressroutecircuits | Não | 
> | expressroute crossconnections | Não | 
> | vias expressas | Não | 
> | expressrouteports | Não | 
> | frontdoors | Não | 
> | frontdoorwebapplicationfirewallpolicies | Não | 
> | loadbalancers | Sim <br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover os equilibradores de carga internos e externos. |
> | vias de rede locais |  Não | 
> | natgateways |  Não | 
> | políticas de networkintent |  Não | 
> | networkinterfaces | Sim <br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover NICs. | 
> | redes de mentofils | Não | 
> | networksecuritygroups | Sim <br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover grupos de segurança de rede (NGSs). | 
> | observadores de rede |  Não |  
> | observadores de rede / suportes de conexão |  Não | 
> | observadores de rede / lentes |  Não | 
> | observadores de rede / pingmeshes |  Não | 
> | p2svpngateways | Não | 
> | zonas privadas |  Não |  
> | privatednszones / virtualnetworklinks |  Não |  
> | pontos privados | Não | 
> | serviços de privatelink | Não | 
> | endereços publicitários | Sim<br/><br/> Utilize [o Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover endereços IP públicos. |
> | publicipprefixos | Não | 
> | filtros de rota | Não | 
> | routetables |  Não | 
> | serviceendpointpolicies |  Não | 
> | trafficmanagerprofiles |  Não | 
> | virtualhubs | Não | 
> | vias virtualnetworkgateways |  Não |  
> | virtualnetworks |  Não | 
> | virtualnetworktaps | Não | 
> | virtualwans | Não | 
> | VPNgateways (Wan Virtual) | Não | 
> | vpnsites (WAN virtual) | Não | 
> | webapplicationfirewallpolicies |  Não | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | espaços de nome |  Não | 
> | espaços de nomes / notificações |  Não |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | áreas de trabalho |  Não | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | configurações de gestão |  Não | 
> | Modos de exibição |  Não | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | espreiteiros | Não | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | dashboards | Não | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | rootresources | Não | 

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

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | cofres | Não.<br/><br/> A movimentação de cofres dos Serviços de Recuperação para o Azure Backup em todas as regiões de Azure não é suportada.<br/><br/> Nos cofres dos Serviços de Recuperação para recuperação do local de Azure, pode [desativar e recriar o cofre](../../site-recovery/move-vaults-across-regions.md) na região alvo. | 


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

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | flows |  Não |  
> | trabalhocollectões |  Não | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | serviços de pesquisa |  Não | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | soluções de iotsecuritys |  Não | 
> | configurações de playbook | Não | 

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

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aplicações | Não | 
> | aglomerados |  Não | 
> | clusters / aplicações | Não | 
> | grupos de contentores | Não | 
> | conjuntos de contentores | Não | 
> | edgeclusters | Não | 
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

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | sinaleiro |  Não |  

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aparelhos dedefinições | Não | 
> | aparelhos | Não | 
> | aplicações dedefinições | Não | 
> | aplicações | Não | 
> | jitrequests | Não | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | instâncias | Não | 
> | managedinstances | Sim <br/><br/> [Saiba mais](/azure/azure-sql/database/move-resources-across-regions) sobre a mudança de instâncias geridas em todas as regiões. | 
> | managedinstances / bases de dados | Sim | 
> | servidores | Sim | 
> | servidores / bases de dados | Sim <br/><br/> [Saiba mais](/azure/azure-sql/database/move-resources-across-regions) sobre a mudança de bases de dados em regiões.<br/><br/> [Saiba mais](../../resource-mover/tutorial-move-region-sql.md) sobre a utilização do Azure Resource Mover para mover bases de dados Azure SQL.  | 
> | servidores / elásticos | Sim <br/><br/> [Saiba mais](/azure/azure-sql/database/move-resources-across-regions) sobre a movimentação de piscinas elásticas em regiões.<br/><br/> [Saiba mais](../../resource-mover/tutorial-move-region-sql.md) sobre a utilização do Azure Resource Mover para mover piscinas elásticas Azure SQL.  | 
> | virtualclusters | Sim | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | grupos sqlvirtualmachineinegroups |  Não |  
> | sqlvirtualmachines |  Não |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | dwvm | Não | 

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
> | streamingjobs |  Não |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | ambientes | Não | 
> | ambientes / fontes de eventos | Não | 
> | casos | Não | 
> | instâncias / ambientes | Não | 
> | instâncias / ambientes / fontes de eventos | Não | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | registos de fornecedores | Não | 
> | resources | Não | 

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



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | não-100 dedicados | Não | 
> | serviços decloud dedicados | Não | 
> | virtualmachines | Não | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | certificados | Não | 
> | vias de conexão |  Não |  
> | conexões |  Não |  
> | customapis |  Não | 
> | hospedar ambientes | Não | 
> | serverfarms |  Não |  
> | sites |  Não | 
> | sites / premieraddons |  Não |  
> | sites / slots |  Não |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | serviços de dispositivos | Não | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | grupos de aplicações | Não | 
> | hostpools | Não | 
> | áreas de trabalho | Não | 

## <a name="third-party-services"></a>Serviços de terceiros

Os serviços de terceiros não suportam atualmente a operação de mudança.
