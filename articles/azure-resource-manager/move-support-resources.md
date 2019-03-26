---
title: Mover o suporte de operação por tipo de recurso do Azure
description: Lista os tipos de recursos do Azure que podem ser movidos para um novo grupo de recursos ou subscrição.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 03/22/2019
ms.author: tomfitz
ms.openlocfilehash: d44b1bf778c7ec9551e2fd30f67083f8dded22d1
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438473"
---
# <a name="move-operation-support-for-resources"></a>Mover o suporte de operação para recursos
Este artigo lista se um tipo de recurso do Azure suporta a operação de movimentação. Embora um tipo de recurso suporta a operação de movimentação, poderá haver condições que impedem que o recurso a ser movido. Para obter detalhes sobre as condições que afetem as operações de movimentação, consulte [mover recursos para um novo grupo de recursos ou subscrição](resource-group-move-resources.md).

Para obter os mesmos dados como um ficheiro de valores separados por vírgulas, transfira [move-suporte-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| domainservices | Não | Não |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| Inquilinos | Não | Não |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| servidores | Sim | Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| serviço | Sim | Sim |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| apiapps | Não | Não |
| appidentities | Não | Não |
| gateways | Não | Não |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| policyassignments | Não | Não |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| automationaccounts | Sim | Sim |
| automationaccounts/configurations | Sim | Sim |
| automationaccounts/runbooks | Sim | Sim |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| b2cdirectories | Sim | Sim |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| registos | Sim | Sim |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| backupvault | Não | Não |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| batchaccounts | Sim | Sim |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| Clusters | Não | Não |
| fileservers | Não | Não |
| tarefas | Não | Não |
| áreas de trabalho | Não | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| mapapis | Não | Não |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| biztalk | Sim | Sim |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| blockchainmembers | Não | Não |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| blueprintassignments | Não | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| botservices | Sim | Sim |

## <a name="microsoftcache"></a>Microsoft.Cache
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| De redis | Sim | Sim |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| Perfis | Sim | Sim |
| perfis/pontos finais | Sim | Sim |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| certificateorders | Sim | Sim |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| domainnames | Sim | Não |
| virtualmachines | Sim | Não |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| networksecuritygroups | Não | Não |
| reservedips | Não | Não |
| redes virtuais | Não | Não |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| storageaccounts | Sim | Não |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| accounts | Sim | Sim |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| availabilitysets | Sim | Sim |
| discos | Sim | Sim |
| galerias | Não | Não |
| galleries/images | Não | Não |
| galleries/images/versions | Não | Não |
| imagens | Sim | Sim |
| proximityplacementgroups | Não | Não |
| restorepointcollections | Não | Não |
| sharedvmimages | Não | Não |
| sharedvmimages/versions | Não | Não |
| instantâneos | Sim | Sim |
| virtualmachines | Sim | Sim |
| virtualmachines/extensions | Sim | Sim |
| virtualmachinescalesets | Sim | Sim |

## <a name="microsoftcontainer"></a>Microsoft.Container
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| containergroups | Não | Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| containergroups | Não | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| registos | Sim | Sim |
| registries/buildtasks | Sim | Sim |
| registries/replications | Não | Não |
| registos/tarefas | Sim | Sim |
| registries/webhooks | Sim | Sim |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| containerservices | Não | Não |
| managedclusters | Não | Não |
| openshiftmanagedclusters | Não | Não |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| aplicações | Sim | Sim |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| accounts | Não | Não |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| empresariais | Sim | Sim |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| hubs | Sim | Sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| tarefas | Não | Não |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| databoxedgedevices | Não | Não |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| áreas de trabalho | Não | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| catálogos | Sim | Sim |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| connectionmanagers | Não | Não |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| pacotes | Não | Não |
| Planos | Não | Não |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| datafactories | Sim | Sim |
| fábricas | Sim | Sim |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| datalakeaccounts | Não | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| accounts | Sim | Sim |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| accounts | Sim | Sim |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| services | Não | Não |
| services/projects | Não | Não |
| ranhuras | Não | Não |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| servidores | Sim | Sim |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| servidores | Sim | Sim |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| servergroups | Não | Não |
| servidores | Sim | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| artifactsources | Não | Não |
| Implementações | Não | Não |
| servicetopologies | Não | Não |
| servicetopologies/services | Não | Não |
| servicetopologies/services/serviceunits | Não | Não |
| passos | Não | Não |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| elasticpools | Não | Não |
| elasticpools/iothubtenants | Não | Não |
| iothubs | Sim | Sim |
| provisioningservices | Sim | Sim |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| Controladores | Não | Não |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| labcenters | Não | Não |
| Laboratórios | Sim | Não |
| labs/servicerunners | Sim | Sim |
| labs/virtualmachines | Sim | Não |
| Agendas | Não | Não |

## <a name="microsoftdns"></a>microsoft.dns
| Tipo de recurso | Grupo de recursos | Subscrição |
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
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| databaseaccounts | Sim | Sim |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| domínios | Sim | Sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| domínios | Sim | Sim |
| Tópicos | Sim | Sim |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| Clusters | Sim | Sim |
| namespaces | Sim | Sim |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| accounts | Não | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| hanainstances | Sim | Sim |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| Clusters | Sim | Sim |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| datamanagers | Sim | Sim |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| tarefas | Sim | Sim |

## <a name="microsoftinsights"></a>microsoft.insights
| Tipo de recurso | Grupo de recursos | Subscrição |
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
| scheduledqueryrules | Não | Não |
| testes Web | Sim | Sim |
| pastas de trabalho | Sim | Sim |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| iotapps | Sim | Sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| checknameavailability | Sim | Sim |
| gráfico | Sim | Sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| hsmpools | Não | Não |
| cofres | Sim | Sim |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| Clusters | Sim | Sim |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| labaccounts | Não | Não |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| accounts | Sim | Sim |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| accounts | Sim | Sim |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| hostingenvironments | Não | Não |
| integrationaccounts | Sim | Sim |
| integrationserviceenvironments | Não | Não |
| isolatedenvironments | Não | Não |
| Fluxos de trabalho | Sim | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| commitmentplans | Sim | Sim |
| webservices | Sim | Não |
| áreas de trabalho | Sim | Sim |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Sim | Sim |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| accounts | Sim | Sim |
| accounts/workspaces | Sim | Sim |
| accounts/workspaces/projects | Sim | Sim |
| teamaccounts | Sim | Sim |
| teamaccounts/áreas de trabalho | Sim | Sim |
| teamaccounts/workspaces/projects | Sim | Sim |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| accounts | Sim | Sim |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| hostingaccounts | Não | Não |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| áreas de trabalho | Não | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| userassignedidentities | Sim | Sim |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| accounts | Sim | Sim |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| classicdevservices | Não | Não |

## <a name="microsoftmedia"></a>Microsoft.Media
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| mediaservices | Sim | Sim |
| mediaservices/liveevents | Sim | Sim |
| mediaservices/streamingendpoints | Sim | Sim |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| assessmentprojects | Não | Não |
| migrateprojects | Não | Não |
| projetos | Não | Não |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| netappaccounts | Não | Não |
| netappaccounts/capacitypools | Não | Não |
| netappaccounts/capacitypools/volumes | Não | Não |
| netappaccounts/capacitypools/volumes/mounttargets | Não | Não |
| netappaccounts/capacitypools/volumes/snapshots | Não | Não |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| applicationgateways | Não | Não |
| applicationsecuritygroups | Sim | Sim |
| azurefirewalls | Sim | Sim |
| bastionhosts | Não | Não |
| ligações | Sim | Sim |
| ddoscustompolicies | Sim | Sim |
| ddosprotectionplans | Não | Não |
| dnszones | Sim | Sim |
| expressroutecircuits | Não | Não |
| expressroutecrossconnections | Não | Não |
| expressroutegateways | Não | Não |
| expressrouteports | Não | Não |
| frontdoors | Sim | Sim |
| frontdoorwebapplicationfirewallpolicies | Sim | Sim |
| interfaceendpoints | Não | Não |
| balanceadores de carga | Sim | Sim |
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
| privatelinkservices | Não | Não |
| publicipaddresses | Sim | Sim |
| publicipprefixes | Sim | Sim |
| routefilters | Não | Não |
| routetables | Sim | Sim |
| securegateways | Não | Não |
| serviceendpointpolicies | Sim | Sim |
| trafficmanagerprofiles | Sim | Sim |
| virtualhubs | Não | Não |
| virtualnetworkgateways | Sim | Sim |
| redes virtuais | Sim | Sim |
| virtualnetworktaps | Não | Não |
| virtualwans | Não | Não |
| vpngateways | Não | Não |
| vpnsites | Sim | Sim |
| webapplicationfirewallpolicies | Sim | Sim |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| namespaces | Sim | Sim |
| namespaces/notificationhubs | Sim | Sim |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| áreas de trabalho | Sim | Sim |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| managementconfigurations | Sim | Sim |
| soluções | Sim | Sim |
| visualizações | Sim | Sim |

## <a name="microsoftportal"></a>Microsoft.Portal
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| dashboards | Sim | Sim |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| rootresources | Não | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| workspacecollections | Sim | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| Capacidades | Sim | Sim |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| accounts | Não | Não |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| cofres | Sim | Sim |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| namespaces | Sim | Sim |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| aplicações | Sim | Não |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| fluxos | Sim | Sim |
| jobcollections | Sim | Sim |

## <a name="microsoftsearch"></a>Microsoft.Search
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| searchservices | Sim | Sim |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| gateways | Não | Não |
| nós | Não | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| namespaces | Sim | Sim |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Tipo de recurso | Grupo de recursos | Subscrição |
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
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| aplicações | Sim | Sim |
| containergroups | Não | Não |
| gateways | Sim | Sim |
| Redes | Sim | Sim |
| segredos | Sim | Sim |
| volumes | Sim | Sim |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| signalr | Sim | Sim |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| siterecoveryvault | Não | Não |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| appliancedefinitions | Não | Não |
| aplicações | Não | Não |
| applicationdefinitions | Não | Não |
| aplicações | Não | Não |
| jitrequests | Não | Não |

## <a name="microsoftsql"></a>Microsoft.Sql
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| managedinstances | Sim | Sim |
| managedinstances/databases | Sim | Sim |
| servidores | Sim | Sim |
| servers/databases | Sim | Sim |
| servers/elasticpools | Sim | Sim |
| servers/jobaccounts | Não | Não |
| servers/jobagents | Não | Não |
| virtualclusters | Sim | Sim |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Sim | Sim |
| sqlvirtualmachines | Sim | Sim |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| dwvm | Não | Não |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| storageaccounts | Sim | Sim |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| storagesyncservices | Sim | Sim |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| storagesyncservices | Não | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| storagesyncservices | Não | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| gerentes | Não | Não |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| streamingjobs | Sim | Sim |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| Ambientes | Não | Não |
| ambientes/eventsources | Não | Não |
| instâncias | Não | Não |
| ambientes/instâncias | Não | Não |
| ambientes/instâncias/eventsources | Não | Não |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| providerregistrations | Não | Não |
| recursos | Não | Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| Ambientes | Sim | Sim |
| ambientes/eventsources | Sim | Sim |
| environments/referencedatasets | Sim | Sim |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| imagetemplates | Não | Não |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| conta | Sim | Sim |
| extensão da conta / | Sim | Sim |
| conta/projeto | Sim | Sim |

## <a name="microsoftweb"></a>Microsoft.Web
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| certificados | Não | Sim |
| connectiongateways | Sim | Sim |
| ligações | Sim | Sim |
| customapis | Sim | Sim |
| hostingenvironments | Não | Não |
| serverfarms | Sim | Sim |
| sites | Sim | Sim |
| sites/premieraddons | Sim | Sim |
| sites/slots | Sim | Sim |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Tipo de recurso | Grupo de recursos | Subscrição |
| ------------- | ----------- | ---------- |
| deviceservices | Sim | Sim |

## <a name="third-party-services"></a>Serviços de terceiros

Serviços de terceiros atualmente não é suportada a operação de movimentação.

## <a name="next-steps"></a>Passos Seguintes
Para obter comandos mover os recursos, veja [mover recursos para um novo grupo de recursos ou subscrição](resource-group-move-resources.md).
