---
title: Mover o suporte de operação por tipo de recurso do Azure
description: Lista os tipos de recursos do Azure que podem ser movidos para um novo grupo de recursos ou subscrição.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 7/9/2019
ms.author: tomfitz
ms.openlocfilehash: 093c20407cb6210125106189f36566f539de0dcc
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721112"
---
# <a name="move-operation-support-for-resources"></a>Mover o suporte de operação para recursos
Este artigo lista se um tipo de recurso do Azure suporta a operação de movimentação. Ele também fornece informações sobre as condições especiais a serem considerados ao mover um recurso.

Ir para um espaço de nomes do fornecedor de recursos:
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
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Backup](#microsoftbackup)
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
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [microsoft.dns](#microsoftdns)
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
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
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
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domainservices | Não | Não |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Inquilinos | Não | Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| actionrules | Sim | Sim |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Servidores | Sim | Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| serviço | Sim | Sim |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| configurationstores | Sim | Sim |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| apiapps | Não | Não |
| appidentities | Não | Não |
| gateways | Não | Não |

> [!IMPORTANT]
> Ver [serviço de aplicações mover orientações](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| policyassignments | Não | Não |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| automationaccounts | Sim | Sim |
| automationaccounts/configurations | Sim | Sim |
| automationaccounts/runbooks | Sim | Sim |

> [!IMPORTANT]
> Runbooks tem de existir no mesmo grupo de recursos como a conta de automatização.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| b2cdirectories | Sim | Sim |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| registos | Sim | Sim |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| backupvault | Não | Não |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| batchaccounts | Sim | Sim |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Clusters | Não | Não |
| fileservers | Não | Não |
| Tarefas | Não | Não |
| Áreas de trabalho | Não | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| mapapis | Não | Não |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| biztalk | Sim | Sim |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| blockchainmembers | Sim | Sim |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| blueprintassignments | Não | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| botservices | Sim | Sim |

## <a name="microsoftcache"></a>Microsoft.Cache
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| De redis | Sim | Sim |

> [!IMPORTANT]
> Se a Cache do Azure para a instância de Redis está configurada com uma rede virtual, a instância não é possível mover para uma subscrição diferente. Ver [redes virtuais mover limitações](./move-limitations/virtual-network-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| profiles | Sim | Sim |
| perfis/pontos finais | Sim | Sim |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| certificateorders | Sim | Sim |

> [!IMPORTANT]
> Ver [serviço de aplicações mover orientações](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domainnames | Sim | Não |
| virtualmachines | Sim | Não |

> [!IMPORTANT]
> Ver [documentação de orientação de mover de implementação clássica](./move-limitations/classic-model-move-limitations.md). Recursos de implementação clássica podem ser movidos em várias subscrições com uma operação específica para esse cenário.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| networksecuritygroups | Não | Não |
| reservedips | Não | Não |
| redes virtuais | Não | Não |

> [!IMPORTANT]
> Ver [documentação de orientação de mover de implementação clássica](./move-limitations/classic-model-move-limitations.md). Recursos de implementação clássica podem ser movidos em várias subscrições com uma operação específica para esse cenário.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storageaccounts | Sim | Não |

> [!IMPORTANT]
> Ver [documentação de orientação de mover de implementação clássica](./move-limitations/classic-model-move-limitations.md). Recursos de implementação clássica podem ser movidos em várias subscrições com uma operação específica para esse cenário.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Sim | Sim |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| availabilitysets | Sim | Sim |
| Discos | Sim | Sim |
| galerias | Não | Não |
| galleries/images | Não | Não |
| galleries/images/versions | Não | Não |
| hostgroups | Não | Não |
| hostgroups/hosts | Não | Não |
| imagens | Sim | Sim |
| proximityplacementgroups | Não | Não |
| restorepointcollections | Não | Não |
| sharedvmimages | Não | Não |
| sharedvmimages/versions | Não | Não |
| Instantâneos | Sim | Sim |
| virtualmachines | Sim | Sim |
| virtualmachines/extensions | Sim | Sim |
| virtualmachinescalesets | Sim | Sim |

> [!IMPORTANT]
> Ver [documentação de orientação de mover as máquinas virtuais](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftcontainer"></a>Microsoft.Container
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containergroups | Não | Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containergroups | Não | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| registos | Sim | Sim |
| registries/buildtasks | Sim | Sim |
| registries/replications | Sim | Sim |
| registos/tarefas | Sim | Sim |
| registries/webhooks | Sim | Sim |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containerservices | Não | Não |
| managedclusters | Não | Não |
| openshiftmanagedclusters | Não | Não |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| aplicações | Sim | Sim |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Não | Não |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| empresariais | Sim | Sim |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hubs | Sim | Sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Tarefas | Não | Não |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| databoxedgedevices | Não | Não |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Áreas de trabalho | Não | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| catálogos | Sim | Sim |
| datacatalogs | Não | Não |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| connectionmanagers | Não | Não |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Pacotes | Não | Não |
| Planos | Não | Não |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datafactories | Sim | Sim |
| fábricas | Sim | Sim |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datalakeaccounts | Não | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Sim | Sim |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Sim | Sim |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Não | Não |
| services/projects | Não | Não |
| ranhuras | Não | Não |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Servidores | Sim | Sim |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Servidores | Sim | Sim |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servergroups | Não | Não |
| Servidores | Sim | Sim |
| serversv2 | Sim | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| artifactsources | Sim | Sim |
| Implementações | Sim | Sim |
| servicetopologies | Sim | Sim |
| servicetopologies/services | Sim | Sim |
| servicetopologies/services/serviceunits | Sim | Sim |
| passos | Sim | Sim |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| elasticpools | Não | Não |
| elasticpools/iothubtenants | Não | Não |
| iothubs | Sim | Sim |
| provisioningservices | Sim | Sim |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Controladores | Não | Não |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| labcenters | Não | Não |
| Laboratórios | Sim | Não |
| laboratórios/ambientes | Sim | Sim |
| labs/servicerunners | Sim | Sim |
| labs/virtualmachines | Sim | Não |
| Agendas | Sim | Sim |

## <a name="microsoftdns"></a>microsoft.dns
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dnszones | Não | Não |
| dnszones/a | Não | Não |
| dnszones/aaaa | Não | Não |
| dnszones/cname | Não | Não |
| dnszones/mx | Não | Não |
| dnszones/ptr | Não | Não |
| dnszones/srv | Não | Não |
| dnszones/txt | Não | Não |
| trafficmanagerprofiles | Não | Não |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| databaseaccounts | Sim | Sim |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domínios | Sim | Sim |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Sim | Sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domínios | Sim | Sim |
| Tópicos | Sim | Sim |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Clusters | Sim | Sim |
| namespaces | Sim | Sim |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Não | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hanainstances | Sim | Sim |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Clusters | Sim | Sim |

> [!IMPORTANT]
> Pode mover clusters do HDInsight para uma nova subscrição ou grupo de recursos. No entanto, não é possível mover entre subscrições, os recursos de rede ligados ao cluster do HDInsight (por exemplo, a rede virtual, uma NIC ou um balanceador de carga). Além disso, não é possível mover a um novo grupo de recursos um NIC que está ligado a uma máquina virtual para o cluster.
>
> Ao migrar um cluster do HDInsight para uma nova subscrição, primeiro mova outros recursos (como a conta de armazenamento). Em seguida, mova o cluster do HDInsight por si só.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Sim | Sim |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| máquinas | Não | Não |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datamanagers | Sim | Sim |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Tarefas | Sim | Sim |

## <a name="microsoftinsights"></a>microsoft.insights
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Não | Não |
| actiongroups | Sim | Sim |
| activitylogalerts | Não | Não |
| alertrules | Sim | Sim |
| autoscalesettings | Sim | Sim |
| Componentes | Sim | Sim |
| guestdiagnosticsettings | Não | Não |
| metricalerts | Não | Não |
| notificationgroups | Não | Não |
| notificationrules | Não | Não |
| scheduledqueryrules | Sim | Sim |
| testes Web | Sim | Sim |
| pastas de trabalho | Sim | Sim |

> [!IMPORTANT]
> Certifique-se de que a mudança para a nova assinatura não excede [quotas da subscrição](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| iotapps | Sim | Sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| checknameavailability | Sim | Sim |
| graph | Sim | Sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hsmpools | Não | Não |
| cofres | Sim | Sim |

> [!IMPORTANT]
> Os cofres de chave utilizada para encriptação de disco não pode ser movidos para um grupo de recursos na mesma subscrição ou em várias subscrições.

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Clusters | Sim | Sim |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| labaccounts | Não | Não |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Sim | Sim |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Não | Não |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hostingenvironments | Não | Não |
| integrationaccounts | Sim | Sim |
| integrationserviceenvironments | Não | Não |
| isolatedenvironments | Não | Não |
| Fluxos de trabalho | Sim | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| commitmentplans | Sim | Sim |
| webservices | Sim | Não |
| Áreas de trabalho | Sim | Sim |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Sim | Sim |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Não | Não |
| accounts/workspaces | Não | Não |
| accounts/workspaces/projects | Não | Não |
| teamaccounts | Não | Não |
| teamaccounts/áreas de trabalho | Não | Não |
| teamaccounts/workspaces/projects | Não | Não |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Sim | Sim |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hostingaccounts | Não | Não |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Áreas de trabalho | Não | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| userassignedidentities | Não | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Sim | Sim |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| classicdevservices | Não | Não |

## <a name="microsoftmedia"></a>Microsoft.Media
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| mediaservices | Sim | Sim |
| mediaservices/liveevents | Sim | Sim |
| mediaservices/streamingendpoints | Sim | Sim |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| assessmentprojects | Não | Não |
| migrateprojects | Não | Não |
| projetos | Não | Não |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| netappaccounts | Não | Não |
| netappaccounts/capacitypools | Não | Não |
| netappaccounts/capacitypools/volumes | Não | Não |
| netappaccounts/capacitypools/volumes/mounttargets | Não | Não |
| netappaccounts/capacitypools/volumes/snapshots | Não | Não |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applicationgateways | Não | Não |
| applicationgatewaywebapplicationfirewallpolicies | Não | Não |
| applicationsecuritygroups | Sim | Sim |
| azurefirewalls | Sim | Sim |
| bastionhosts | Não | Não |
| Ligações | Sim | Sim |
| ddoscustompolicies | Sim | Sim |
| ddosprotectionplans | Não | Não |
| dnszones | Sim | Sim |
| expressroutecircuits | Não | Não |
| expressroutecrossconnections | Não | Não |
| expressroutegateways | Não | Não |
| expressrouteports | Não | Não |
| frontdoors | Não | Não |
| frontdoorwebapplicationfirewallpolicies | Não | Não |
| balanceadores de carga | Sim - SKU básico<br>Não - Standard SKU | Sim - SKU básico<br>Não - Standard SKU |
| localnetworkgateways | Sim | Sim |
| natgateways | Sim | Sim |
| networkintentpolicies | Sim | Sim |
| networkinterfaces | Sim | Sim |
| networkprofiles | Não | Não |
| networksecuritygroups | Sim | Sim |
| networkwatchers | Sim | Sim |
| networkwatchers/connectionmonitors | Sim | Sim |
| networkwatchers/lenses | Sim | Sim |
| networkwatchers/pingmeshes | Sim | Sim |
| p2svpngateways | Não | Não |
| privatednszones | Sim | Sim |
| privatednszones/virtualnetworklinks | Sim | Sim |
| privateendpoints | Não | Não |
| privatelinkservices | Não | Não |
| publicipaddresses | Sim - SKU básico<br>Não - Standard SKU | Sim - SKU básico<br>Não - Standard SKU |
| publicipprefixes | Sim | Sim |
| routefilters | Não | Não |
| routetables | Sim | Sim |
| securegateways | Sim | Sim |
| serviceendpointpolicies | Sim | Sim |
| trafficmanagerprofiles | Sim | Sim |
| virtualhubs | Não | Não |
| virtualnetworkgateways | Sim | Sim |
| redes virtuais | Sim | Sim |
| virtualnetworktaps | Não | Não |
| virtualwans | Não | Não |
| vpngateways | Não | Não |
| vpnsites | Não | Não |
| webapplicationfirewallpolicies | Sim | Sim |

> [!IMPORTANT]
> Ver [redes virtuais mover orientações](./move-limitations/virtual-network-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| namespaces | Sim | Sim |
| namespaces/notificationhubs | Sim | Sim |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Áreas de trabalho | Sim | Sim |

> [!IMPORTANT]
> Certifique-se de que a mudança para a nova assinatura não excede [quotas da subscrição](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| managementconfigurations | Sim | Sim |
| soluções | Sim | Sim |
| Modos de exibição | Sim | Sim |

## <a name="microsoftpeering"></a>Microsoft.Peering
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| peerings | Não | Não |

## <a name="microsoftportal"></a>Microsoft.Portal
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dashboards | Sim | Sim |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| rootresources | Não | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspacecollections | Sim | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Capacidades | Sim | Sim |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Não | Não |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| cofres | Sim | Sim |

> [!IMPORTANT]
> Ver [serviços de recuperação mover orientações](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| namespaces | Sim | Sim |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| aplicações | Sim | Não |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| fluxos | Sim | Sim |
| jobcollections | Sim | Sim |

## <a name="microsoftsearch"></a>Microsoft.Search
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| searchservices | Sim | Sim |

> [!IMPORTANT]
> Não é possível mover vários recursos de pesquisa em regiões diferentes numa única operação. Em vez disso, movê-los em operações separadas.

## <a name="microsoftsecurity"></a>Microsoft.Security
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| iotsecuritysolutions | Sim | Sim |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| gateways | Não | Não |
| nós | Não | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| namespaces | Sim | Sim |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| aplicações | Não | Não |
| Clusters | Sim | Sim |
| containergroups | Não | Não |
| containergroupsets | Não | Não |
| edgeclusters | Não | Não |
| Redes | Não | Não |
| secretstores | Não | Não |
| volumes | Não | Não |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| aplicações | Sim | Sim |
| containergroups | Não | Não |
| gateways | Sim | Sim |
| Redes | Sim | Sim |
| Segredos | Sim | Sim |
| volumes | Sim | Sim |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| signalr | Sim | Sim |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| siterecoveryvault | Não | Não |

> [!IMPORTANT]
> Ver [serviços de recuperação mover orientações](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| appliancedefinitions | Não | Não |
| aplicações | Não | Não |
| applicationdefinitions | Não | Não |
| aplicações | Não | Não |
| jitrequests | Não | Não |

## <a name="microsoftsql"></a>Microsoft.Sql
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| instancepools | Sim | Sim |
| managedinstances | Sim | Sim |
| managedinstances/databases | Sim | Sim |
| Servidores | Sim | Sim |
| servers/databases | Sim | Sim |
| servers/elasticpools | Sim | Sim |
| virtualclusters | Sim | Sim |

> [!IMPORTANT]
> Uma base de dados e o servidor tem de estar no mesmo grupo de recursos. Quando move um SQL server, todas as suas bases de dados também são movidas. Este comportamento aplica-se às bases de dados do Azure SQL Database e o Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Sim | Sim |
| sqlvirtualmachines | Sim | Sim |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dwvm | Não | Não |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storageaccounts | Sim | Sim |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Caches | Não | Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Sim | Sim |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Não | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Não | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| gerentes | Não | Não |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| streamingjobs | Sim | Sim |

> [!IMPORTANT]
> Estado de análise de Stream tarefas não podem ser movidas quando em execução.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Ambientes | Não | Não |
| ambientes/eventsources | Não | Não |
| instâncias | Não | Não |
| ambientes/instâncias | Não | Não |
| ambientes/instâncias/eventsources | Não | Não |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| providerregistrations | Não | Não |
| Recursos | Não | Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Ambientes | Sim | Sim |
| ambientes/eventsources | Sim | Sim |
| environments/referencedatasets | Sim | Sim |

## <a name="microsofttoken"></a>Microsoft.Token
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| armazena | Não | Não |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| imagetemplates | Não | Não |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| conta | Sim | Sim |
| extensão da conta / | Sim | Sim |
| conta/projeto | Sim | Sim |

> [!IMPORTANT]
> Para alterar a subscrição de Azure DevOps, consulte [alterar a subscrição do Azure utilizada para faturação](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dedicatedcloudnodes | Sim | Sim |
| dedicatedcloudservices | Sim | Sim |
| virtualmachines | Sim | Sim |

## <a name="microsoftweb"></a>Microsoft.Web
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Certificados | Não | Sim |
| connectiongateways | Sim | Sim |
| Ligações | Sim | Sim |
| customapis | Sim | Sim |
| hostingenvironments | Não | Não |
| serverfarms | Sim | Sim |
| sites | Sim | Sim |
| sites/premieraddons | Sim | Sim |
| sites/slots | Sim | Sim |

> [!IMPORTANT]
> Ver [serviço de aplicações mover orientações](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| deviceservices | Não | Não |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applicationgroups | Não | Não |
| hostpools | Não | Não |
| Áreas de trabalho | Não | Não |

## <a name="third-party-services"></a>Serviços de terceiros

Serviços de terceiros atualmente não é suportada a operação de movimentação.

## <a name="next-steps"></a>Passos Seguintes
Para obter comandos mover os recursos, veja [mover recursos para um novo grupo de recursos ou subscrição](resource-group-move-resources.md).

Para obter os mesmos dados como um ficheiro de valores separados por vírgulas, transfira [move-suporte-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
