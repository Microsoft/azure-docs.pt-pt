---
title: Apoio à movimentação de recursos azure em regiões
description: Lista os tipos de recursos Azure que podem ser movidos através das regiões de Azure
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/20/2020
ms.author: raynew
ms.openlocfilehash: 9bc7dc66ccf3049ac878f7871c816e5ade1afde5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760713"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Apoio à movimentação de recursos azure em regiões

Este artigo confirma se um tipo de recurso Azure é suportado para se mudar para outra região do Azure. 

Salte para um espaço de nome do fornecedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automação](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Batchai](#microsoftbatchai)
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
> - [Microsoft.Serviços Cognitivos](#microsoftcognitiveservices)
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
> - [Microsoft.dataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Dispositivos](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genómica](#microsoftgenomics)
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
> - [Microsoft.Soluções](#microsoftsolutions)
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
> | serviços de domínio / replicações | Não | 

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
> | serviço |  Sim | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | lojas de configuração | Não | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | apiapps | Não | 
> | appidentities | Não | 
> | gateways | Não | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | atribuições políticas | Não |

## <a name="microsoftautomation"></a>Microsoft.Automação

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | contas de automação | Não | 
> | contas/configurações de automação | Não | 
> | contas de automação / livros de execução | Não | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | b2cdirecties | Não | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | registos sqlserver | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | registos | Não | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | contas de lote | Não |

## <a name="microsoftbatchai"></a>Microsoft.Batchai

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aglomerados | Não | 
> | servidores de ficheiros | Não | 
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
> | blockchainmembers | Não |
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
> | serviços bots | Não | 

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
> | certificados | Não | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | nomes de domínio | Não |  
> | máquinas virtuais | Não | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | networksecuritygroups | Não |
> | reservas | Não | 
> | redes virtuais | Não | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | contas de armazenamento | Sim |  


## <a name="microsoftcognitiveservices"></a>Microsoft.Serviços Cognitivos

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | conjuntos de disponibilidade | Não | 
> | conjuntos de encriptação de discos | Não | 
> | discos | Não | 
> | galerias | Não | 
> | galerias / imagens | Não | 
> | galerias / imagens / versões | Não | 
> | grupos de anfitriões | Não | 
> | grupos de anfitriões / anfitriões | Não | 
> | imagens | Não | 
> | grupos de proximidade | Não | 
> | restaurospointcoles | Não | 
> | sharedvmimages | Não | 
> | sharedvmimages / versões | Não | 
> | instantâneos | Não | 
> | máquinas virtuais | Sim | 
> | virtualmachines / extensões | Não | 
> | conjuntos de escala seleções virtuais | Não | 

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
> | registos / tarefas de construção | Não |  
> | registos / replicações | Não | 
> | registos / tarefas | Não |  
> | registos / webhooks | Não | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | serviços de contentores | Não | 
> | clusters geridos | Não | 
> | openshiftmanagedclusters | Não | 

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
> | fornecedores de recursos | Não | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | empregos | Não | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | dispositivos de databoxedge | Não | 

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
> | catálogos de dados | Não | 

## <a name="microsoftdataconnect"></a>Microsoft.dataConnect

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | gestores de conexão | Não | 

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
> | fábricas de dados | Não | 
> | fábricas | Não |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | datalakecontas | Não | 

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
> | servidores | Não |  

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | servidores | Não |  

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | grupos de servidores | Não | 
> | servidores | Não |  
> | servidorsv2 | Não | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | fontes de artefactos | Não | 
> | lançamentos | Não |  
> | servicetopologies | Não | 
> | serviços /serviços | Não |  
> | serviços / serviços / unidades de serviço | Não | 
> | passos | Não | 

## <a name="microsoftdevices"></a>Microsoft.Dispositivos

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | elásticos | Não | 
> | elasticpools / iothubtenants | Não | 
> | iothubs | Sim | 
> | serviços de prestação | Não | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | controladores | Não | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | centros de laboratório | Não | 
> | laboratórios | Não | 
> | laboratórios / ambientes | Não |  
> | laboratórios / corredores de serviço | Não | 
> | laboratórios / máquinas virtuais | Não |  
> | horários | Não |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | contas de bases de dados | Não | 

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
> | espaços de nome | Não | 

## <a name="microsoftgenomics"></a>Microsoft.Genómica

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | hanainstâncias | Não | 
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
> | gestores de dados |  Não | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | empregos |  Não | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | Não | 
> | grupos de ação |  Não | 
> | alertas de atividade | Não | 
> | regras de alerta |  Não | 
> | definições de escala automática |  Não | 
> | componentes |  Não |  
> | definições de diagnóstico de convidados | Não | 
> | alertas métricos | Não | 
> | grupos de notificações | Não | 
> | regras de notificação | Não | 
> | regras de agendamento |  Não | 
> | webtestes |  Não | 
> | livros |  Não |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | iotapps |  Não |  

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | checknamedisponibilidade |  Não |  
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
> | accounts | Não | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | ambientes de hospedagem | Não | 
> | contas de integração |  Não |  
> | integraçõesambientes de serviços | Não | 
> | ambientes isolados | Não | 
> | fluxos de trabalho |  Não |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | planos de compromisso |  Não | 
> | webservices |  Não | 
> | áreas de trabalho |  Não | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | clusters de operacionalização |  Não | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts | Não | 
> | contas / espaços de trabalho | Não | 
> | contas / espaços de trabalho / projetos | Não | 
> | contas de equipa | Não | 
> | contas de equipas / espaços de trabalho | Não | 
> | teamaccounts / espaços de trabalho / projetos | Não | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

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
> | userassignedidentidades | Não | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | accounts |  Não |  

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | serviços clássicos | Não | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | serviços de media |  Não | 
> | mediaservices / liveevents |  Não | 
> | mediaservices / streamingendpoints |  Não | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | appclusters | Não | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | projetos de avaliação | Não | 
> | projetos migratórios | Não | 
> | projetos | Não | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | netappaccounts | Não | 
> | netappaccounts / pools de capacidade | Não | 
> | netappaccounts / capacitypools / volumes | Não | 
> | netappaccounts / capacitypools / volumes / mounttargets | Não | 
> | netappaccounts / capacitypools / volumes / instantâneos | Não | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | applicationgateways | Não | 
> | aplicaçõesgatewaywebapplicationfirewallpolicies | Não | 
> | grupos de segurança de aplicações |  Não |  
> | azurefirewalls |  Não |  
> | bastionários | Não | 
> | conexões |  Não | 
> | ddoscustompolíticas |  Não | 
> | ddosprotectionplans | Não | 
> | zonas de dnszone |  Não | 
> | circuitos de rotação expresso | Não | 
> | conexões de vias cruzadas expressroute | Não | 
> | expressroutegateways | Não | 
> | autorrotações ports | Não | 
> | portas da frente | Não | 
> | frontdoorwebapplicationapplicationfirewallpolicies | Não | 
> | loadbalancers | Sim - SKU Básico<br>Não - Standard SKU | Sim - SKU Básico<br> -Sim Standard SKU |
> | locaisdenetworkgateways |  Não | 
> | natgateways |  Não | 
> | políticas de intenções de rede |  Não | 
> | interfaces de rede | Sim | 
> | perfis de rede | Não | 
> | networksecuritygroups | Sim | 
> | observadores de rede |  Não |  
> | networkwatchers / monitores de ligação |  Não | 
> | networkwatchers / lentes |  Não | 
> | networkwatchers / pingmeshes |  Não | 
> | p2svpngateways | Não | 
> | zonas privadas |  Não |  
> | privatednszones / virtualnetworklinks |  Não |  
> | pontos de privateend | Não | 
> | serviços privatelink | Não | 
> | publicipaddresss | Sim - SKU Básico<br>Não - Standard SKU | Sim - SKU Básico<br>Não - Standard SKU |
> | publicipprefixes | Não | 
> | filtros de rotas | Não | 
> | rotações |  Não | 
> | políticas de pontos de serviço |  Não | 
> | perfis de gestor de tráfego |  Não | 
> | virtualhubs | Não | 
> | virtualnetworkgateways |  Não |  
> | redes virtuais |  Não | 
> | virtualnetworktaps | Não | 
> | virtualwans | Não | 
> | vpngateways (Virtual WAN) | Não | 
> | vpnsites (Virtual WAN) | Não | 
> | políticas webapplicationfirewall |  Não | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | espaços de nome |  Não | 
> | espaços de nomes / centros de notificações |  Não |  

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
> | os pares | Não | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | dashboards | Não | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | recursos radiculares | Não | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | workspacecollections |  Não | 

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
> | cofres | Não. [Desativar o cofre e recriar](https://docs.microsoft.com/azure/site-recovery/move-vaults-across-regions) para recuperação do site  | 


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
> | recolhas de emprego |  Não | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | serviços de pesquisa |  Não | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | soluções iotsecurity |  Não | 
> | configurações de playbook | Não | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | gateways | Não | 
> | nódoas | Não | 

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
> | conjuntos de grupos de contentores | Não | 
> | aglomerados de bordas | Não | 
> | redes | Não | 
> | lojas secretas | Não | 
> | volumes | Não | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | aplicações |  Não | 
> | grupos de contentores | Não | 
> | gateways |  Não | 
> | redes |  Não | 
> | segredos |  Não | 
> | volumes |  Não |  

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | signalr |  Não |  

## <a name="microsoftsolutions"></a>Microsoft.Soluções

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | definições de eletrodomésticos | Não | 
> | aparelhos | Não | 
> | definições de aplicações | Não | 
> | aplicações | Não | 
> | jitrequests | Não | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | instâncias | Não | 
> | casos geridos | Sim | 
> | casos geridos / bases de dados | Sim | 
> | servidores | Sim | 
> | servidores / bases de dados | Sim | 
> | servidores / elásticos | Sim | 
> | aglomerados virtuais | Sim | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | grupos de máquinas sqlvirtual |  Não |  
> | máquinas sqlvirtual |  Não |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | dwvm | Não | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | contas de armazenamento | Sim | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | caches | Não | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | serviços de armazenamentos |  Não | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | serviços de armazenamentos | Não | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | serviços de armazenamentos | Não | 

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
> | instâncias | Não | 
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
> | modelos de imagem | Não | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | conta |  Não | 
> | conta /extensão |  Não | 
> | conta / projeto |  Não | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | dedicados cloudnodes | Não | 
> | serviços dedicados em nuvem | Não | 
> | máquinas virtuais | Não | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimento da região | 
> | ------------- | ----------- |
> | certificados | Não | 
> | connectiongateways |  Não |  
> | conexões |  Não |  
> | prismapa |  Não | 
> | ambientes de hospedagem | Não | 
> | servidores fazendas |  Não |  
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
> | piscinas de acolhimento | Não | 
> | áreas de trabalho | Não | 

## <a name="third-party-services"></a>Serviços de terceiros

Atualmente, os serviços de terceiros não apoiam a operação de mudança.
