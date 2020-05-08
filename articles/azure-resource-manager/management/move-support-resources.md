---
title: Mover suporte de operação por tipo de recurso
description: Lista os tipos de recursos Azure que podem ser transferidos para um novo grupo de recursos ou subscrição.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 45450b21b1cd3236712043629f433c2c5fe20f80
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900983"
---
# <a name="move-operation-support-for-resources"></a>Suporte da operação de movimentação para recursos
Este artigo enumera se um tipo de recurso Azure suporta a operação de movimento. Também fornece informações sobre condições especiais a considerar ao mover um recurso.

Salte para um espaço de nome do fornecedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automação](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Batchai](#microsoftbatchai)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.Cognição](#microsoftcognition)
> - [Microsoft.Serviços Cognitivos](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumo](#microsoftconsumption)
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
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Dispositivos](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentação](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Genómica](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
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
> - [Microsoft.Manutenção](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Recursos](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
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
> - [Microsoft.Subscrição](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
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
> | inquilinos | Não | Não |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | configurações | Não | Não |
> | recomendações | Não | Não |
> | supressões | Não | Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | regras de ação | Sim | Sim |
> | alerts | Não | Não |
> | alerta | Não | Não |
> | smartdetectoralertrules | Sim | Sim |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | servidores | Sim | Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviço | Sim | Sim |

> [!IMPORTANT]
> Um serviço de Gestão API que está definido para o SKU de Consumo não pode ser movido.

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | lojas de configuração | Sim | Sim |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | primavera | Sim | Sim |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | apiapps | Não | Não |
> | appidentities | Não | Não |
> | gateways | Não | Não |

> [!IMPORTANT]
> Consulte [a orientação de movimento do Serviço de Aplicações](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | check-access | Não | Não |
> | negar atribuições | Não | Não |
> | encontrar tarefas de rolemóórfão | Não | Não |
> | fechaduras | Não | Não |
> | permissões | Não | Não |
> | atribuições políticas | Não | Não |
> | definições políticas | Não | Não |
> | definições políticas | Não | Não |
> | roleassignments | Não | Não |
> | roleassignmentsusagemetrics | Não | Não |
> | definições de roledefinitions | Não | Não |

## <a name="microsoftautomation"></a>Microsoft.Automação

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas de automação | Sim | Sim |
> | contas/configurações de automação | Sim | Sim |
> | contas de automação / livros de execução | Sim | Sim |

> [!IMPORTANT]
> Os livros de execução devem existir no mesmo grupo de recursos que a Conta de Automação.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | b2cdirecties | Sim | Sim |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | gestores de dados híbridos | Não | Não |
> | postgresinstances | Não | Não |
> | sqlinstâncias | Não | Não |
> | registos sqlserver | Sim | Sim |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | registos | Sim | Sim |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas de lote | Sim | Sim |

## <a name="microsoftbatchai"></a>Microsoft.Batchai

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | Não | Não |
> | servidores de ficheiros | Não | Não |
> | empregos | Não | Não |
> | áreas de trabalho | Não | Não |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | períodos de faturação | Não | Não |
> | permissões de faturação | Não | Não |
> | atribuição de faturações | Não | Não |
> | definições de billingrole | Não | Não |
> | criação de roleassignment de faturação | Não | Não |

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
> | blockchainmembers | Não | Não |
> | cordamembers | Não | Não |
> | observadores | Não | Não |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | tokenservices | Não | Não |

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
> | serviços bots | Sim | Sim |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | redis | Sim | Sim |

> [!IMPORTANT]
> Se o caso Azure Cache for Redis estiver configurado com uma rede virtual, a instância não pode ser transferida para uma subscrição diferente. Ver [limitações](./move-limitations/networking-move-limitations.md)de movimento em rede .

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Sim | Sim |
> | perfis | Sim | Sim |
> | perfis / pontos finais | Sim | Sim |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | certificados | Sim | Sim |

> [!IMPORTANT]
> Consulte [a orientação de movimento do Serviço de Aplicações](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | nomes de domínio | Sim | No |
> | máquinas virtuais | Sim | No |

> [!IMPORTANT]
> Consulte a orientação de movimento de movimento de [implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Não | Não |
> | reservas | Não | Não |
> | redes virtuais | Não | Não |

> [!IMPORTANT]
> Consulte a orientação de movimento de movimento de [implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas de armazenamento | Sim | No |

> [!IMPORTANT]
> Consulte a orientação de movimento de movimento de [implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

## <a name="microsoftcognition"></a>Microsoft.Cognição

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas sintéticas | Não | Não |

## <a name="microsoftcognitiveservices"></a>Microsoft.Serviços Cognitivos

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | conjuntos de disponibilidade | Sim | Sim |
> | conjuntos de encriptação de discos | Não | Não |
> | discos | Sim | Sim |
> | galerias | Não | Não |
> | galerias / imagens | Não | Não |
> | galerias / imagens / versões | Não | Não |
> | grupos de anfitriões | Não | Não |
> | grupos de anfitriões / anfitriões | Não | Não |
> | imagens | Sim | Sim |
> | grupos de proximidade | Sim | Sim |
> | restaurospointcoles | Não | Não |
> | extensões partilhadas | Não | Não |
> | sharedvmimages | Não | Não |
> | sharedvmimages / versões | Não | Não |
> | instantâneos | Sim | Sim |
> | sshpublickeys | Não | Não |
> | máquinas virtuais | Sim | Sim |
> | virtualmachines / extensões | Sim | Sim |
> | conjuntos de escala seleções virtuais | Sim | Sim |

> [!IMPORTANT]
> Ver [Máquinas Virtuais mover orientação](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Microsoft.Consumo

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | custo agregado | Não | Não |
> | equilíbrios | Não | Não |
> | orçamentos | Não | Não |
> | encargos | Não | Não |
> | costtags | Não | Não |
> | créditos | Não | Não |
> | eventos | Não | Não |
> | previsões | Não | Não |
> | lotes | Não | Não |
> | mercados | Não | Não |
> | resultados de operação | Não | Não |
> | estado de funcionamento | Não | Não |
> | folhas de preços | Não | Não |
> | produtos | Não | Não |
> | detalhes da reserva | Não | Não |
> | recomendações de reservas | Não | Não |
> | resumos de reservas | Não | Não |
> | transações de reservas | Não | Não |
> | etiquetas | Não | Não |
> | inquilinos | Não | Não |
> | termos | Não | Não |
> | detalhes de uso | Não | Não |

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | grupos de contentores | Não | Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | grupos de contentores | Não | Não |
> | ligações de associação de serviços | Não | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | registos | Sim | Sim |
> | registos / grupos de agentes | Não | Não |
> | registos / tarefas de construção | Sim | Sim |
> | registos / replicações | Sim | Sim |
> | registos / taskruns | Sim | Sim |
> | registos / tarefas | Sim | Sim |
> | registos / webhooks | Sim | Sim |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços de contentores | Não | Não |
> | clusters geridos | Não | Não |
> | openshiftmanagedclusters | Não | Não |

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
> | orçamentos | Não | Não |
> | conectores | Sim | Sim |
> | dimensões | Não | Não |
> | exportações | Não | Não |
> | subscrições externas | Não | Não |
> | forecast | Não | Não |
> | consulta | Não | Não |
> | reportconfigs | Não | Não |
> | relatórios | Não | Não |
> | regras de showback | Não | Não |
> | Modos de exibição | Não | Não |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | hubs | Não | Não |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | associações | Não | Não |
> | fornecedores de recursos | Sim | Sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | empregos | Não | Não |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | dispositivos de databoxedge | Não | Não |

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
> | catálogos de dados | Não | Não |

## <a name="microsoftdataconnect"></a>Microsoft.dataConnect

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | gestores de conexão | Não | Não |

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
> | fábricas de dados | Sim | Sim |
> | fábricas | Sim | Sim |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | datalakecontas | Não | Não |

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
> | servidores | Sim | Sim |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | grupos de servidores | Não | Não |
> | servidores | Sim | Sim |
> | servidorsv2 | Sim | Sim |
> | servidores únicos | Sim | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | fontes de artefactos | Sim | Sim |
> | lançamentos | Sim | Sim |
> | servicetopologies | Sim | Sim |
> | serviços /serviços | Sim | Sim |
> | serviços / serviços / unidades de serviço | Sim | Sim |
> | passos | Sim | Sim |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | grupos de aplicações | Não | Não |
> | piscinas de acolhimento | Não | Não |
> | áreas de trabalho | Não | Não |

## <a name="microsoftdevices"></a>Microsoft.Dispositivos

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | elásticos | Não | Não |
> | elasticpools / iothubtenants | Não | Não |
> | iothubs | Sim | Sim |
> | serviços de prestação | Sim | Sim |

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
> | laboratórios | Sim | No |
> | laboratórios / ambientes | Sim | Sim |
> | laboratórios / corredores de serviço | Sim | Sim |
> | laboratórios / máquinas virtuais | Sim | No |
> | horários | Sim | Sim |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | instâncias digitaltwins | Não | Não |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas de bases de dados | Sim | Sim |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | domínios | Sim | Sim |

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
> | eventoSSubscrições | Não - não pode ser movido independentemente, mas automaticamente movido com recurso subscrito. | Não - não pode ser movido independentemente, mas automaticamente movido com recurso subscrito. |
> | assinaturas de eventos | Não - não pode ser movido independentemente, mas automaticamente movido com recurso subscrito. | Não - não pode ser movido independentemente, mas automaticamente movido com recurso subscrito. |
> | tópicos de extensão | Não | Não |
> | espaços de nome de parceiro | Sim | Sim |
> | registos de parceiros | Não | Não |
> | tópicos parceiro | Sim | Sim |
> | tópicos sistematópicas | Sim | Sim |
> | tópicos | Sim | Sim |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | Sim | Sim |
> | espaços de nome | Sim | Sim |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentação

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | experimentosespaços | Não | Não |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | espaços de nome | Sim | Sim |

## <a name="microsoftgenomics"></a>Microsoft.Genómica

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas autogeridas | Não | Não |
> | perfis de reconfiguração autogeredm | Não | Não |
> | atribuições de guestconfigurações | Não | Não |
> | software | Não | Não |
> | softwareupdateperfil | Não | Não |
> | atualizações de software | Não | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | hanainstâncias | Não | Não |
> | sapmonitors | Sim | Sim |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | Sim | Sim |

> [!IMPORTANT]
> Pode mover os clusters HDInsight para uma nova subscrição ou grupo de recursos. No entanto, não é possível mover-se através de subscrições dos recursos de rede ligados ao cluster HDInsight (como a rede virtual, NIC ou balanceador de carga). Além disso, não é possível mover-se para um novo grupo de recursos um NIC que está ligado a uma máquina virtual para o cluster.
>
> Ao mover um cluster HDInsight para uma nova subscrição, primeiro mova outros recursos (como a conta de armazenamento). Em seguida, mova o cluster HDInsight por si só.

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
> | máquinas / extensões | Não | Não |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | gestores de dados | Sim | Sim |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | componentes | Não | Não |
> | cóscopos de rede | Não | Não |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | empregos | Sim | Sim |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | grupos de ação | Sim | Sim |
> | alertas de atividade | Não | Não |
> | regras de alerta | Sim | Sim |
> | definições de escala automática | Sim | Sim |
> | linha de base | Não | Não |
> | calcular base | Não | Não |
> | componentes | Sim | Sim |
> | regras de recolha de dados | Não | Não |
> | definições de diagnóstico | Não | Não |
> | categorias de definições de diagnóstico | Não | Não |
> | tipos de eventos | Não | Não |
> | definições de diagnóstico alargadas | Não | Não |
> | definições de diagnóstico de convidados | Não | Não |
> | definições de log | Não | Não |
> | registos | Não | Não |
> | alertas métricos | Não | Não |
> | linhas de base métricas | Não | Não |
> | definições métricas | Não | Não |
> | espaços de nomemétrico | Não | Não |
> | metrics | Não | Não |
> | meus livros | Não | Não |
> | grupos de notificações | Não | Não |
> | privatelinkscopes | Sim | Sim |
> | regras de agendamento | Sim | Sim |
> | topology | Não | Não |
> | transações | Não | Não |
> | vminsightsboardingstatuses | Não | Não |
> | webtestes | Sim | Sim |
> | livros | Sim | Sim |
> | modelos de livro | Sim | Sim |

> [!IMPORTANT]
> Certifique-se de que a mudança para uma nova subscrição não excede [as quotas de subscrição.](azure-subscription-service-limits.md#azure-monitor-limits)

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | iotapps | Sim | Sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedisponibilidade | Sim | Sim |
> | gráfico | Sim | Sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | hsmpools | Não | Não |
> | cofres | Sim | Sim |

> [!IMPORTANT]
> Os Cofres-Chave utilizados para encriptação de discos não podem ser transferidos para um grupo de recursos na mesma subscrição ou através de subscrições.

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | clusters conectados | Não | Não |

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
> | ambientes de hospedagem | Não | Não |
> | contas de integração | Sim | Sim |
> | integraçõesambientes de serviços | Sim | No |
> | integraçõesambientes de serviços / managedapis | Sim | No |
> | ambientes isolados | Não | Não |
> | fluxos de trabalho | Sim | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | planos de compromisso | Não | Não |
> | webservices | Sim | No |
> | áreas de trabalho | Sim | Sim |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | clusters de operacionalização | Não | Não |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |
> | contas / espaços de trabalho | Não | Não |
> | contas / espaços de trabalho / projetos | Não | Não |
> | contas de equipa | Não | Não |
> | contas de equipas / espaços de trabalho | Não | Não |
> | teamaccounts / espaços de trabalho / projetos | Não | Não |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperacionalização

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas de hospedagem | Não | Não |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | áreas de trabalho | Não | Não |
> | espaços de trabalho / computação | Não | Não |

## <a name="microsoftmaintenance"></a>Microsoft.Manutenção

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicar atualizações | Não | Não |
> | atribuições de configuração | Não | Não |
> | configurações de manutenção | Sim | Sim |
> | atualizações | Não | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | identidades | Não | Não |
> | userassignedidentidades | Não | Não |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | redes geridas | Não | Não |
> | redes geridas /grupos de redes geridas | Não | Não |
> | redes geridas / políticas geridas de networkpeering | Não | Não |
> | notificação | Não | Não |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | atribuição de inscrições | Não | Não |
> | definições de registo | Não | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |
> | contas / privateatlases | Sim | Sim |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços clássicos | Não | Não |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços de media | Sim | Sim |
> | mediaservices / liveevents | Sim | Sim |
> | mediaservices / streamingendpoints | Sim | Sim |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | appclusters | Não | Não |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | projetos de avaliação | Sim | Sim |
> | projetos migratórios | Sim | Sim |
> | move-secoles | Não | Não |
> | projetos | Não | Não |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Não | Não |
> | netappaccounts / backuppolicies | Não | Não |
> | netappaccounts / pools de capacidade | Não | Não |
> | netappaccounts / capacitypools / volumes | Não | Não |
> | netappaccounts / capacitypools / volumes / mounttargets | Não | Não |
> | netappaccounts / capacitypools / volumes / instantâneos | Não | Não |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Não | Não |
> | aplicaçõesgatewaywebapplicationfirewallpolicies | Não | Não |
> | grupos de segurança de aplicações | Sim | Sim |
> | azurefirewalls | Sim | Sim |
> | bastionários | Não | Não |
> | conexões | Sim | Sim |
> | ddoscustompolíticas | Sim | Sim |
> | ddosprotectionplans | Não | Não |
> | zonas de dnszone | Sim | Sim |
> | circuitos de rotação expresso | Não | Não |
> | expressroutegateways | Não | Não |
> | firewallpolíticas | Sim | Sim |
> | portas da frente | Não | Não |
> | frontdoorwebapplicationapplicationfirewallpolicies | Não | Não |
> | ipgroups | Sim | Sim |
> | loadbalancers | Sim - SKU Básico<br>Não - Standard SKU | Sim - SKU Básico<br>Não - Standard SKU |
> | locaisdenetworkgateways | Sim | Sim |
> | natgateways | Sim | Sim |
> | perfis de experiência de rede | Sim | Sim |
> | políticas de intenções de rede | Sim | Sim |
> | interfaces de rede | Sim | Sim |
> | perfis de rede | Não | Não |
> | networksecuritygroups | Sim | Sim |
> | observadores de rede | Sim | No |
> | networkwatchers / monitores de ligação | Sim | No |
> | networkwatchers / flowlogs | Sim | No |
> | networkwatchers / pingmeshes | Sim | No |
> | p2svpngateways | Não | Não |
> | zonas privadas | Sim | Sim |
> | privatednszones / virtualnetworklinks | Sim | Sim |
> | mapas de privateendpointredirectmaps | Não | Não |
> | pontos de privateend | Sim | Sim |
> | serviços privatelink | Não | Não |
> | publicipaddresss | Sim - SKU Básico<br>Não - Standard SKU | Sim - SKU Básico<br>Não - Standard SKU |
> | publicipprefixes | Sim | Sim |
> | filtros de rotas | Não | Não |
> | rotações | Sim | Sim |
> | políticas de pontos de serviço | Sim | Sim |
> | perfis de gestor de tráfego | Sim | Sim |
> | virtualhubs | Não | Não |
> | virtualnetworkgateways | Sim | Sim |
> | redes virtuais | Sim | Sim |
> | virtualnetworktaps | Não | Não |
> | routers virtuais | Sim | Sim |
> | virtualwans | Não | Não |
> | vpngateways (Virtual WAN) | Não | Não |
> | vpnserverconfiguras | Não | Não |
> | vpnsites (Virtual WAN) | Não | Não |
> | políticas webapplicationfirewall | Sim | Sim |

> [!IMPORTANT]
> Consulte [a orientação](./move-limitations/networking-move-limitations.md)do movimento em rede .

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | espaços de nome | Sim | Sim |
> | espaços de nomes / centros de notificações | Sim | Sim |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | espaços de osnome | Sim | Sim |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | Não | Não |
> | armazenamentoinsightconfigs | Não | Não |
> | áreas de trabalho | Sim | Sim |

> [!IMPORTANT]
> Certifique-se de que a mudança para uma nova subscrição não excede as quotas de [subscrição.](azure-subscription-service-limits.md#azure-monitor-limits)

> [!IMPORTANT]
> Espaços de trabalho que têm uma conta de automação ligada não podem ser movidos. Antes de iniciar uma operação de movimento, certifique-se de desligar quaisquer contas de automação.   

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | associações de gestão | Não | Não |
> | configurações de gestão | Sim | Sim |
> | soluções | Sim | Sim |
> | Modos de exibição | Sim | Sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | os pares | Sim | Sim |
> | serviços de peering | Não | Não |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | eventos políticos | Não | Não |
> | estados políticos | Não | Não |
> | recursos de política | Não | Não |
> | reparações | Não | Não |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | dashboards | Sim | Sim |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | recursos radiculares | Não | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Sim | Sim |

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

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | lançamentos | Não | Não |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | áreas de trabalho | Não | Não |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | Não | Não |
> | resultados de elegibilidade de replicação | Não | Não |
> | cofres | Sim | Sim |

> [!IMPORTANT]
> Consulte [os Serviços de Recuperação a mover orientação.](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | openshiftclusters | Não | Não |

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

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | estados de disponibilidade | Não | Não |
> | estados de disponibilidade de crianças | Não | Não |
> | recursos infantis | Não | Não |
> | eventos | Não | Não |
> | notificações | Não | Não |

## <a name="microsoftresources"></a>Microsoft.Recursos

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | implantscripts | Não | Não |
> | ligações | Não | Não |
> | etiquetas | Não | Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações | Sim | No |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços de pesquisa | Sim | Sim |

> [!IMPORTANT]
> Não se pode mover vários recursos de busca em diferentes regiões numa só operação. Em vez disso, movê-los em operações separadas.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Não | Não |
> | definições avançadas de proteção contra ameaças | Não | Não |
> | metadados de avaliação | Não | Não |
> | avaliações | Não | Não |
> | automações | Sim | Sim |
> | resultados de conformidade | Não | Não |
> | cumprimentos | Não | Não |
> | datacollectionagents | Não | Não |
> | grupos de segurança de dispositivos | Não | Não |
> | políticas de proteção de informação | Não | Não |
> | soluções iotsecurity | Sim | Sim |
> | avaliações de vulnerabilidade de servidores | Não | Não |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | agregações | Não | Não |
> | regras de alerta | Não | Não |
> | modelos de regras de alerta | Não | Não |
> | marcadores | Não | Não |
> | casos | Não | Não |
> | ligação de dados | Não | Não |
> | requisitos de verificação de conectores de dados | Não | Não |
> | entidades | Não | Não |
> | entidades | Não | Não |
> | incidentes | Não | Não |
> | consentimentos de escritório | Não | Não |
> | settings | Não | Não |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | gateways | Não | Não |
> | nódoas | Não | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | espaços de nome | Sim | Sim |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações | Não | Não |
> | aglomerados | Sim | Sim |
> | clusters / aplicações | Não | Não |
> | grupos de contentores | Não | Não |
> | conjuntos de grupos de contentores | Não | Não |
> | aglomerados de bordas | Não | Não |
> | clusters geridos | Não | Não |
> | redes | Não | Não |
> | lojas secretas | Não | Não |
> | volumes | Não | Não |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações | Sim | Sim |
> | grupos de contentores | Não | Não |
> | gateways | Sim | Sim |
> | redes | Sim | Sim |
> | segredos | Sim | Sim |
> | volumes | Sim | Sim |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | lançamentos | Não | Não |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | signalr | Sim | Sim |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | benefícios híbridos | Não | Não |

## <a name="microsoftsolutions"></a>Microsoft.Soluções

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | definições de aplicações | Não | Não |
> | aplicações | Não | Não |
> | jitrequests | Não | Não |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | instâncias | Não | Não |
> | locais | Sim | Sim |
> | casos geridos | Não | Não |
> | casos geridos / bases de dados | Não | Não |
> | servidores | Sim | Sim |
> | servidores / bases de dados | Sim | Sim |
> | servidores / elásticos | Sim | Sim |
> | servidores / contas de emprego | Sim | Sim |
> | servidores / agentes de trabalho | Sim | Sim |
> | aglomerados virtuais | Sim | Sim |

> [!IMPORTANT]
> Uma base de dados e um servidor devem estar no mesmo grupo de recursos. Quando se move um servidor SQL, todas as suas bases de dados também são movidas. Este comportamento aplica-se às bases de dados azure SQL e Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | grupos de máquinas sqlvirtual | Sim | Sim |
> | máquinas sqlvirtual | Sim | Sim |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | dwvm | Não | Não |

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
> | serviços de armazenamentos | Sim | Sim |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços de armazenamentos | Não | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços de armazenamentos | Não | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | gestores | Não | Não |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | streamingjobs | Sim | Sim |

> [!IMPORTANT]
> Os trabalhos da Stream Analytics não podem ser movidos quando estão em estado de gestão.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | ambientes | Não | Não |
> | ambientes / fontes de eventos | Não | Não |
> | instâncias | Não | Não |
> | instâncias / ambientes | Não | Não |
> | instâncias / ambientes / fontes de eventos | Não | Não |

## <a name="microsoftsubscription"></a>Microsoft.Subscrição

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | criar subscrição | Não | Não |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | bilhetes de apoio | Não | Não |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | áreas de trabalho | Não | Não |
> | espaços de trabalho / bigdatapools | Não | Não |
> | espaços de trabalho / sqlpools | Não | Não |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | registos de fornecedores | Não | Não |
> | resources | Não | Não |

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
> | modelos de imagem | Não | Não |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | conta | Não | Não |
> | conta /extensão | Sim | Sim |
> | conta / projeto | Sim | Sim |

> [!IMPORTANT]
> Para alterar a subscrição do Azure DevOps, consulte [a alteração da subscrição Azure utilizada para a faturação](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | dedicados cloudnodes | Não | Não |
> | serviços dedicados em nuvem | Não | Não |
> | máquinas virtuais | Não | Não |

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
> | accounts | Sim | Sim |
> | planos | Sim | Sim |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | certificados | No | Sim |
> | connectiongateways | Sim | Sim |
> | conexões | Sim | Sim |
> | prismapa | Sim | Sim |
> | ambientes de hospedagem | Não | Não |
> | kubeenvironments | Sim | Sim |
> | servidores fazendas | Sim | Sim |
> | sites | Sim | Sim |
> | sites / premieraddons | Sim | Sim |
> | sites / slots | Sim | Sim |
> | estaticos | Não | Não |

> [!IMPORTANT]
> Consulte [a orientação de movimento do Serviço de Aplicações](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | múltiplas teclas de ativação | Não | Não |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços de dispositivos | Não | Não |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | componentes | Não | Não |
> | monitorinstâncias | Não | Não |
> | monitores | Não | Não |
> | definições de notificações | Não | Não |

## <a name="third-party-services"></a>Serviços de terceiros

Atualmente, os serviços de terceiros não apoiam a operação de mudança.

## <a name="next-steps"></a>Passos seguintes
Para que os comandos movam recursos, consulte [mover recursos para um novo grupo de recursos ou subscrição](move-resource-group-and-subscription.md).

Para obter os mesmos dados que um ficheiro de valores separados de vírem, descarregue [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
