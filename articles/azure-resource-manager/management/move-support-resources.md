---
title: Mover suporte de operação por tipo de recurso
description: Lista os tipos de recursos Azure que podem ser transferidos para um novo grupo de recursos ou subscrição.
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 7b7f6f05c9cae80cffb64245a3fc8a6b0890d577
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539232"
---
# <a name="move-operation-support-for-resources"></a>Suporte da operação de movimentação para recursos

Este artigo indica se um tipo de recurso Azure suporta a operação de movimento. Também fornece informações sobre condições especiais a ter em conta ao mover um recurso.

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
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
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
> - [Microsoft.CostManagementExports](#microsoftcostmanagementexports)
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
> - [Microsoft.PowerPlatform](#microsoftpowerplatform)
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
> | serviços de domínio / oucontainer | Não | Não |
> | Locais | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | diagnósticos | Não | Não |
> | diagnósticoscategorias | Não | Não |
> | operações | Não | Não |
> | privatelinkforazuread | Sim | Sim |
> | inquilinos | Sim | Sim |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | resultados de operações | Não | Não |
> | operações | Não | Não |
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
> | operações | Não | Não |
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
> | operações | Não | Não |
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
> | operações | Não | Não |
> | smartdetectoralertrules | Sim | Sim |
> | grupos inteligentes | Não | Não |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | localizações / checknameavailability | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / operações | Não | Não |
> | operações | Não | Não |
> | servidores | Sim | Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checkfeedrequired | Não | Não |
> | checknamedilability | Não | Não |
> | check-innamedilability | Não | Não |
> | operações | Não | Não |
> | reportfeedback | Não | Não |
> | serviço | Sim | Sim |
> | validar nome de serviço | Não | Não |

> [!IMPORTANT]
> Um serviço de Gestão API que está definido para o SKU de consumo não pode ser movido.

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | Não | Não |
> | lojas de configurações | Sim | Sim |
> | lojas de configurações / filtros de eventos | Não | Não |
> | Locais | Não | Não |
> | localizações / operaçõesstatus | Não | Não |
> | operações | Não | Não |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | localizações / checknameavailability | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / operaçõestatus | Não | Não |
> | operações | Não | Não |
> | primavera | Sim | Sim |
> | primavera / aplicativos | Não | Não |
> | mola / apps / implementações | Não | Não |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | apiapps | Não | Não |
> | appidentidades | Não | Não |
> | portais | Não | Não |

> [!IMPORTANT]
> Consulte a [orientação do movimento do Serviço de Aplicações](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | atesstationproviders | Sim | Sim |
> | operações | Não | Não |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checkacesss | Não | Não |
> | classicadministradores | Não | Não |
> | dataaliases | Não | Não |
> | denyassignments | Não | Não |
> | elevateaccess | Não | Não |
> | findorphanroleassignments | Não | Não |
> | fechaduras | Não | Não |
> | operações | Não | Não |
> | operaçõestatus | Não | Não |
> | permissões | Não | Não |
> | assinaturas políticas | Não | Não |
> | definições de políticas | Não | Não |
> | políticas dedefinições | Não | Não |
> | privatelinkassociations | Não | Não |
> | operações de fornecedores | Não | Não |
> | recursos gestagementprivatelinks | Não | Não |
> | roleassignments | Não | Não |
> | roleassignmentsusagemetrics | Não | Não |
> | papéis dedefinições | Não | Não |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas de automação | Sim | Sim |
> | contas/configurações de automação | Sim | Sim |
> | automação contas / empregos | Não | Não |
> | automatização contas / privateendpointconnectionproxies | Não | Não |
> | automação de contas / privateendpointconnections | Não | Não |
> | automatização de contas / recursos de privatelinkresources | Não | Não |
> | contas de automação / runbooks | Sim | Sim |
> | licenças de automação / softwareupdateconfigurations | Não | Não |
> | automação contas / webhooks | Não | Não |
> | operações | Não | Não |

> [!IMPORTANT]
> Os runbooks devem existir no mesmo grupo de recursos que a Conta De Automação.
>
> Para obter informações, consulte [mover a sua conta Azure Automation para outra subscrição](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | localizações / checkquotaailability | Não | Não |
> | localizações / checktrialavailability | Não | Não |
> | operações | Não | Não |
> | nuvens privadas | Sim | Sim |
> | nuvens privadas / clusters | Não | Não |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | b2direões | Sim | Sim |
> | b2ctenants | Não | Não |
> | checknamedilability | Não | Não |
> | operações | Não | Não |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | datacontrollers | Não | Não |
> | híbridos gerentes | Não | Não |
> | operações | Não | Não |
> | pós-insinstâncias | Não | Não |
> | sqlinstances | Não | Não |
> | sqlmanagedinstances | Não | Não |
> | sqlserverinstances | Não | Não |
> | sqlserverregistrations | Sim | Sim |
> | sqlserverregistrations / sqlservers | Não | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | Não | Não |
> | operações | Não | Não |
> | registos | Sim | Sim |
> | registos / assinaturas de clientes | Não | Não |
> | registos / produtos | Não | Não |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | Não | Não |
> | operações | Não | Não |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | lotaccounts | Sim | Sim |
> | Locais | Não | Não |
> | locais / resultados da operação de conta | Não | Não |
> | localizações / checknameavailability | Não | Não |
> | localizações / quotas | Não | Não |
> | operações | Não | Não |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas de faturação | Não | Não |
> | faturação/acordos | Não | Não |
> | contas de faturação / faturações | Não | Não |
> | contas de faturação / faturação | Não | Não |
> | faturação / faturação / equilíbrio disponível | Não | Não |
> | faturações / faturações / faturações | Não | Não |
> | faturação / faturação / billingroleassignments | Não | Não |
> | contas de faturação / faturação / faturação | Não | Não |
> | faturação / faturação / faturação | Não | Não |
> | faturação / faturação / createbillingroleassignment | Não | Não |
> | faturação / faturação / clientes | Não | Não |
> | faturação / faturação / instruções | Não | Não |
> | faturação / faturas | Não | Não |
> | faturação / faturas / faturas / folha de preços | Não | Não |
> | faturação / faturas / transações | Não | Não |
> | faturação / faturação / faturação | Não | Não |
> | faturação / faturação / faturações / faturações / faturações | Não | Não |
> | faturação / faturação / faturações / faturas / billingroleassignments | Não | Não |
> | faturação / faturação / faturações / faturações / faturantes | Não | Não |
> | faturação / faturação / faturação / faturações / faturasubsubscriptions | Não | Não |
> | faturação / faturação / faturação / faturas / createbillingroleasignment | Não | Não |
> | faturação / faturação / faturações / secções de faturação / iniciar transtransfer | Não | Não |
> | faturação / faturação / faturações / produtos | Não | Não |
> | faturação / faturação / faturações / produtos / transferência | Não | Não |
> | faturação / faturação / faturações / produtos / updateautorenew | Não | Não |
> | faturação / faturação / faturações / transações de faturas | Não | Não |
> | faturação / faturação / faturações / transferências | Não | Não |
> | faturação / faturação / patchoperações | Não | Não |
> | faturação / faturação / métodos de pagamento | Não | Não |
> | faturação / faturação / políticas | Não | Não |
> | faturação / faturação / folha de preços | Não | Não |
> | faturação / faturação / operações de carga de downdown de preços | Não | Não |
> | faturação / faturação / produtos | Não | Não |
> | faturação / faturação / transações | Não | Não |
> | billingaccounts / billingroleassignments | Não | Não |
> | contas de faturação / faturação | Não | Não |
> | contas de faturação / faturações | Não | Não |
> | faturações / faturas | Não | Não |
> | billingaccounts / createbillingroleassignment | Não | Não |
> | billingaccounts / criações de operaçãos | Não | Não |
> | contas de faturação / clientes | Não | Não |
> | contas de faturação / clientes / faturações | Não | Não |
> | contas de faturação / clientes / subscrições de faturação | Não | Não |
> | faturação / clientes / iniciar transtransfer | Não | Não |
> | faturação / clientes / políticas | Não | Não |
> | faturação / clientes / produtos | Não | Não |
> | contas de faturação / clientes / transações | Não | Não |
> | faturação / clientes / transfers | Não | Não |
> | contas de faturação /departamentos | Não | Não |
> | contas de faturação / matrículas | Não | Não |
> | faturas de faturas | Não | Não |
> | faturações / faturações | Não | Não |
> | faturações / faturações / faturasubscrições | Não | Não |
> | contas de faturação / faturações / faturasubsubscriptions | Não | Não |
> | faturações / faturações / faturasubsubscriptions / transfer | Não | Não |
> | faturações / faturas / elevação | Não | Não |
> | faturações / faturas / iniciar transtransferição | Não | Não |
> | faturações / faturações / patchoperações | Não | Não |
> | faturações / faturações / operações de produtos | Não | Não |
> | faturações / faturas / produtos | Não | Não |
> | faturação / faturas / produtos / transferência | Não | Não |
> | faturações / faturas / produtos / updateautorenew | Não | Não |
> | faturações / faturações / resultados de transferências de produtos | Não | Não |
> | faturações / faturas / transações | Não | Não |
> | faturações / faturas / transferências | Não | Não |
> | contas de faturação / linha de crédito | Não | Não |
> | billingaccounts / listinvoicesectionswithcreatesubscriptionpersion | Não | Não |
> | contas de faturação / resultados de operações | Não | Não |
> | contas de faturação / patchoperações | Não | Não |
> | contas de faturação / métodos de pagamento | Não | Não |
> | faturação / produtos | Não | Não |
> | contas de faturação / transações | Não | Não |
> | faturantes | Não | Não |
> | faturações | Não | Não |
> | billingproperty | Não | Não |
> | billingroleassignments | Não | Não |
> | billingroledefinitions | Não | Não |
> | createbillingroleassignment | Não | Não |
> | departamentos | Não | Não |
> | matrículas | Não | Não |
> | faturas | Não | Não |
> | resultados de operações | Não | Não |
> | operações | Não | Não |
> | operaçõestatus | Não | Não |
> | transferências | Não | Não |
> | transferências / aceitatransfer | Não | Não |
> | transferências / declíniotransfer | Não | Não |
> | transferências / operaçõestatus | Não | Não |
> | transferências / validar transfera | Não | Não |
> | validação de morada | Não | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | listcommunicationpreference | Não | Não |
> | mapapis | Não | Não |
> | operações | Não | Não |
> | atualização da atribuição de prescrições | Não | Não |

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
> | Locais | Não | Não |
> | localizações / blockchainmemberoperationresults | Não | Não |
> | localizações / checknameavailability | Não | Não |
> | localizações / listconsortiums | Não | Não |
> | locais / observadores resultados da operação | Não | Não |
> | operações | Não | Não |
> | observadores | Não | Não |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |
> | serviços de token | Não | Não |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Não | Não |
> | blueprintassignments / operações de atribuição | Não | Não |
> | blueprintassignments / operações | Não | Não |
> | plantas | Não | Não |
> | plantas / artefactos | Não | Não |
> | plantas / versões | Não | Não |
> | plantas / versões / artefactos | Não | Não |
> | operações | Não | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | botservices | Sim | Sim |
> | botservices / canais | Não | Não |
> | botservices / conexões | Não | Não |
> | checknamedilability | Não | Não |
> | listar de aprequantoers | Não | Não |
> | operações | Não | Não |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | Não | Não |
> | Locais | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / operaçõesstatus | Não | Não |
> | operações | Não | Não |
> | redis | Sim | Sim |
> | redis / eventgridfilters | Não | Não |
> | redis / privatelinkresources | Não | Não |
> | redisenterprise | Não | Não |

> [!IMPORTANT]
> Se a cache Azure para a instância Redis estiver configurada com uma rede virtual, o caso não pode ser movido para uma subscrição diferente. Ver [limitações de movimento de rede](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | reservações aplicadas | Não | Não |
> | calcular a troca | Não | Não |
> | calcular preço | Não | Não |
> | calcular o preço da compra | Não | Não |
> | catálogos | Não | Não |
> | checkoffers | Não | Não |
> | checkpurchasestatus | Não | Não |
> | checkscópios | Não | Não |
> | ordenadores de reservação comercial | Não | Não |
> | troca | Não | Não |
> | listbenefits | Não | Não |
> | operações | Não | Não |
> | placepurchaseorder | Não | Não |
> | encomendas de reservas | Não | Não |
> | pedidos de reservas / disponíveisscópios | Não | Não |
> | encomendas de reservas / calcular refinanciamento | Não | Não |
> | encomendas de reservas / fusão | Não | Não |
> | encomendas de reservas / reservas | Não | Não |
> | encomendas de reservas / reservas / disponíveisscópios | Não | Não |
> | encomendas de reservas / reservas / revisões | Não | Não |
> | pedidos de reservas / devolução | Não | Não |
> | pedidos de reservas / divisão | Não | Não |
> | pedidos de reservas / swap | Não | Não |
> | reservas | Não | Não |
> | resources | Não | Não |
> | validação encomenda de reservas | Não | Não |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | cdnwebapplication firewallmanagedrulesets | Não | Não |
> | cdnwebapplicationfirewallpolicies | Sim | Sim |
> | checknamedilability | Não | Não |
> | checkresourceusage | Não | Não |
> | edgenodes | Não | Não |
> | resultados de operações | Não | Não |
> | resultados de operações / resultados de perfis | Não | Não |
> | resultados de operações / perfis / resultados finais | Não | Não |
> | resultados de operações / perfis / resultados finais / resultados personalizados | Não | Não |
> | resultados de operações / perfis / resultados finais / resultados de grupos de origem | Não | Não |
> | resultados de operações / perfis / resultados de ponto final / resultados de origem | Não | Não |
> | operações | Não | Não |
> | perfis | Sim | Sim |
> | perfis / pontos finais | Sim | Sim |
> | perfis / pontos finais / personaldomains | Não | Não |
> | perfis / pontos finais / grupos de origem | Não | Não |
> | perfis / pontos finais / origens | Não | Não |
> | validarprobe | Não | Não |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | certificadores | Sim | Sim |
> | certificadores /certificados | Não | Não |
> | operações | Não | Não |
> | validar a informação de registo certificado | Não | Não |

> [!IMPORTANT]
> Consulte a [orientação do movimento do Serviço de Aplicações](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | capacidades | Não | Não |
> | checkdomainmedilability | Não | Não |
> | temas de domínio | Sim | Não |
> | domínios / capacidades | Não | Não |
> | domainnames / caixas de carga internas | Não | Não |
> | domínios / certificados de serviço | Não | Não |
> | temas de domínio / slots | Não | Não |
> | domínios / slots / funções | Não | Não |
> | domínios / slots / funções / métricas | Não | Não |
> | domínios / slots / funções / métricas | Não | Não |
> | movesubscriptionresources | Não | Não |
> | operaçãofamários de sistemas | Não | Não |
> | sistemas operativos | Não | Não |
> | operações | Não | Não |
> | operações | Não | Não |
> | quotas | Não | Não |
> | recursos | Não | Não |
> | valida a subscrição demovedabilidade | Não | Não |
> | virtualmachines | Sim | Não |
> | virtualmachines / diagnósticos | Não | Não |
> | virtualmachines / metricdefinitions | Não | Não |
> | virtualmachines / métricas | Não | Não |

> [!IMPORTANT]
> Consulte a [orientação do movimento de implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | Não | Não |

> [!IMPORTANT]
> Consulte a [orientação do movimento de implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | capacidades | Não | Não |
> | expressroute crossconnections | Não | Não |
> | expressroutecrossconnections / peerings | Não | Não |
> | gatewaysupporteddes | Não | Não |
> | networksecuritygroups | Não | Não |
> | operações | Não | Não |
> | quotas | Não | Não |
> | reservedips | Não | Não |
> | virtualnetworks | Não | Não |
> | virtualnetworks / remotevirtualnetworkpeeringproxies | Não | Não |
> | virtualnetworks / virtualnetworkpeerings | Não | Não |

> [!IMPORTANT]
> Consulte a [orientação do movimento de implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | capacidades | Não | Não |
> | verificação da contagemcountsailabilidade | Não | Não |
> | discos | Não | Não |
> | imagens | Não | Não |
> | operações | Não | Não |
> | osimagems | Não | Não |
> | osplatformimages | Não | Não |
> | imagens públicas | Não | Não |
> | quotas | Não | Não |
> | contas de armazenamento | Sim | Não |
> | contas de armazenamento / blobservices | Não | Não |
> | contas de armazenamento / serviços de ficheiros | Não | Não |
> | contas de armazenamento / métricas | Não | Não |
> | contas de armazenamento / métricas | Não | Não |
> | contas de armazenamento / serviços de fila | Não | Não |
> | contas de armazenamento / serviços | Não | Não |
> | contas de armazenamento / serviços / diagnósticos | Não | Não |
> | contas de armazenamento / serviços / métricas | Não | Não |
> | contas de armazenamento / serviços / métricas | Não | Não |
> | contas de armazenamento / serviços de mesa | Não | Não |
> | contas de armazenamento / vmimages | Não | Não |
> | vmimages | Não | Não |

> [!IMPORTANT]
> Consulte a [orientação do movimento de implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |

> [!IMPORTANT]
> Consulte a [orientação do movimento de implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |
> | checkdomainailability | Não | Não |
> | Locais | Não | Não |
> | localizações / checkskuavailability | Não | Não |
> | localizações / deletevirtualnetworkorsubnets | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |
> | cartão de taxa | Não | Não |
> | usageaggregates | Não | Não |

## <a name="microsoftcompute"></a>Microsoft.Compute

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
> | Locais | Não | Não |
> | localizações / artefactospublishers | Não | Não |
> | localizações / capsoperações | Não | Não |
> | localizações / operações de discos | Não | Não |
> | localizações / loganalytics | Não | Não |
> | localizações / operações | Não | Não |
> | localizações / editores | Não | Não |
> | localizações / runcommands | Não | Não |
> | localizações / utilizações | Não | Não |
> | localizações / virtualmachines | Não | Não |
> | localizações / vmsizes | Não | Não |
> | localizações / vsmoperações | Não | Não |
> | operações | Não | Não |
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
> | virtualmachines / metricdefinitions | Não | Não |
> | virtualmachines / runcommands | Não | Não |
> | virtualmachinescalesets | Sim | Sim |
> | virtualmachinescalesets /extensões | Não | Não |
> | virtualmachinescalesets / networkinterfaces | Não | Não |
> | virtualmachinescalesets / publicipaddresss | Não | Não |
> | virtualmachinescalesets / virtualmachines | Não | Não |
> | virtualmachinescalesets / virtualmachines / networkinterfaces | Não | Não |

> [!IMPORTANT]
> Veja [a orientação das Máquinas Virtuais.](./move-limitations/virtual-machines-move-limitations.md)

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
> | resultados de operações | Não | Não |
> | operações | Não | Não |
> | operaçõestatus | Não | Não |
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
> | Locais | Não | Não |
> | localizações / cachedimages | Não | Não |
> | localizações / capacidades | Não | Não |
> | localizações / deletevirtualnetworkorsubnets | Não | Não |
> | localizações / operações | Não | Não |
> | localizações / utilizações | Não | Não |
> | operações | Não | Não |
> | ligações de serviços de ações | Não | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | Não | Não |
> | Locais | Não | Não |
> | localizações / autorizar | Não | Não |
> | localizações / deletevirtualnetworkorsubnets | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / configuração de dois | Não | Não |
> | operações | Não | Não |
> | registos | Sim | Sim |
> | registos / agentpools | Sim | Sim |
> | registos / agentpools / listqueuestatus | Não | Não |
> | registos / construções | Não | Não |
> | registos / construções / cancelar | Não | Não |
> | registos / construções / getloglink | Não | Não |
> | registos / buildtasks | Sim | Sim |
> | registos / buildtasks / listsourcerepositoryproperties | Não | Não |
> | registos / buildtasks / passos | Não | Não |
> | registos / buildtasks / passos / listbuildarguments | Não | Não |
> | registos / filtros de eventgridfilters | Não | Não |
> | registos / exportpipelines | Não | Não |
> | registos / gerarcredentais | Não | Não |
> | registos / getbuildsourceuploadurl | Não | Não |
> | registos / obtercredentais | Não | Não |
> | registos / importimage | Não | Não |
> | registos / importações | Não | Não |
> | registos / listbuildsourceuploadurl | Não | Não |
> | registos / listcredentais | Não | Não |
> | registos / listpolidades | Não | Não |
> | registos / listusages | Não | Não |
> | registos / gasodutos | Não | Não |
> | registos / privateendpointconnectionproxies | Não | Não |
> | registos / privateendpointconnectionproxies / validar | Não | Não |
> | registos / privateendpointconnections | Não | Não |
> | registos / recursos de privatelinkresources | Não | Não |
> | registos / queuebuild | Não | Não |
> | registos / regeneração | Não | Não |
> | registos / regeneração decredentais | Não | Não |
> | registos / replicações | Sim | Sim |
> | registos / corre | Não | Não |
> | registos / corre / cancela | Não | Não |
> | registos / runs / listlogsasurl | Não | Não |
> | registos / schedulerun | Não | Não |
> | registos / scopemaps | Não | Não |
> | registos / taskruns | Não | Não |
> | registos / taskruns / listdetails | Não | Não |
> | registos / tarefas | Sim | Sim |
> | registos / tarefas / listdetails | Não | Não |
> | registos / fichas | Não | Não |
> | registos / atualização de políticas | Não | Não |
> | registos / webhooks | Sim | Sim |
> | registros / webhooks / getcallbackconfig | Não | Não |
> | registos / webhooks / listevents | Não | Não |
> | registos / webhooks / ping | Não | Não |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços de contentores | Não | Não |
> | Locais | Não | Não |
> | localizações / aglomerados de turno aberto | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / operações | Não | Não |
> | localizações / orquestradores | Não | Não |
> | geremclusters | Não | Não |
> | aglomerados de gestão de turnos abertos | Não | Não |
> | operações | Não | Não |

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
> | contas /alertas de avaliação externa | Não | Não |
> | contas/dimensões de avaliação externa | Não | Não |
> | contas /previsão de avaliação externa | Não | Não |
> | contas /consultas de externalbillings | Não | Não |
> | subscrições externas | Não | Não |
> | subscrições /alertas externos | Não | Não |
> | subscrições/dimensões externas | Não | Não |
> | subscrições externas / previsão | Não | Não |
> | subscrições externas / consulta | Não | Não |
> | forecast | Não | Não |
> | operações | Não | Não |
> | consulta | Não | Não |
> | registar | Não | Não |
> | configs relatório | Não | Não |
> | relatórios | Não | Não |
> | definições | Não | Não |
> | showbackrules | Não | Não |
> | Modos de exibição | Não | Não |

## <a name="microsoftcostmanagementexports"></a>Microsoft.CostManagementExports

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | hubs | Não | Não |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |
> | pedidos | Não | Não |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | associações | Não | Não |
> | Locais | Não | Não |
> | localizações / operações | Não | Não |
> | operações | Não | Não |
> | recursosproviders | Sim | Sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | empregos | Não | Não |
> | Locais | Não | Não |
> | localizações / availableskus | Não | Não |
> | localizações / checknameavailability | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / configuração da região | Não | Não |
> | localizações / validação de morada | Não | Não |
> | localizações / validar inputções | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | availableskus | Não | Não |
> | databoxedgedevices | Sim | Sim |
> | databoxedgedevices / checknameavailability | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | localizações / obter redes políticas | Não | Não |
> | localizações / operações | Não | Não |
> | operações | Não | Não |
> | áreas de trabalho | Não | Não |
> | espaços de trabalho / espaço de trabalho | Não | Não |
> | espaços de trabalho / virtualnetworkpeerings | Não | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | catálogos | Sim | Sim |
> | checknamedilability | Não | Não |
> | datacatalogs | Não | Não |
> | Locais | Não | Não |
> | localizações / empregos | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | operações | Não | Não |

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
> | checkazuredatafactorynamedilability | Não | Não |
> | checkdatafactorynamedilability | Não | Não |
> | datafactories | Sim | Sim |
> | datafactories / diagnósticos | Não | Não |
> | datafactories / metricdefinitions | Não | Não |
> | datafactoryschema | Não | Não |
> | fábricas | Sim | Sim |
> | fábricas / integrações tempos | Não | Não |
> | Locais | Não | Não |
> | localizações / configurafactoryrepo | Não | Não |
> | localizações / getfeaturevalue | Não | Não |
> | operações | Não | Não |

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
> | contas / datalakestoreaccounts | Não | Não |
> | contas / contas de armazenamento | Não | Não |
> | contas / contas de armazenamento / contentores | Não | Não |
> | contas / contas de armazenamento / contentores / listastokens | Não | Não |
> | Locais | Não | Não |
> | localizações / capacidade | Não | Não |
> | localizações / checknameavailability | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / utilizações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |
> | contas / filtros de eventgridfilters | Não | Não |
> | contas / firewallrules | Não | Não |
> | Locais | Não | Não |
> | localizações / capacidade | Não | Não |
> | localizações / checknameavailability | Não | Não |
> | localizações / deletevirtualnetworkorsubnets | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / utilizações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | localizações / checknameavailability | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / operações | Não | Não |
> | operações | Não | Não |
> | services | Não | Não |
> | serviços / projetos | Não | Não |
> | slots | Não | Não |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | backupvaults | Não | Não |
> | Locais | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |
> | contas / ações | Não | Não |
> | contas / ações / conjuntos de dados | Não | Não |
> | contas / ações / convites | Não | Não |
> | contas / ações / subscrições de partilha de fornecedores | Não | Não |
> | contas / ações / sincronizações | Não | Não |
> | contas / subscrições de ações | Não | Não |
> | contas / subscrições de ações / conjuntos de dados de fontes de consumo | Não | Não |
> | contas / subscrições de ações / datasetmappings | Não | Não |
> | contas / subscrições de ações / gatilhos | Não | Não |
> | listinvitações | Não | Não |
> | Locais | Não | Não |
> | localizações / consumidores | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / rejeição | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | Não | Não |
> | Locais | Não | Não |
> | localizações / azureasyncoperação | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / performancetiers | Não | Não |
> | localizações / privateendpointconnectionazureasyncoperation | Não | Não |
> | localizações / resultados da operação de operação de privateendpointconnection | Não | Não |
> | localizações / privateendpointconnectionproxyazureasyncoperation | Não | Não |
> | localizações / privateendpointconnectionproxyoperationresults | Não | Não |
> | localizações / recomendações de essãosazureasyncoperação | Não | Não |
> | localizações / recomendações de operaçãos de operações | Não | Não |
> | localizações / securityalertpoliciesazuresyncoperation | Não | Não |
> | localizações / securityalertpoliciesoperatórios | Não | Não |
> | localizações / serverkeyazureasyncoperação | Não | Não |
> | localizações / servidores de operações de operações | Não | Não |
> | operações | Não | Não |
> | servidores | Sim | Sim |
> | servidores / assessores | Não | Não |
> | servidores / privateendpointconnectionproxies | Não | Não |
> | servidores / privateendpointconnections | Não | Não |
> | servidores / recursos de privatelinkresources | Não | Não |
> | servidores / textos de consulta | Não | Não |
> | servidores / servidores recuperáveis | Não | Não |
> | servidores / topquerystatistics | Não | Não |
> | servidores / regras de rede virtual | Não | Não |
> | servidores / estatistas | Não | Não |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | Não | Não |
> | Locais | Não | Não |
> | localizações / administradorazureasyncoperação | Não | Não |
> | locais / administradores resultados operacionais | Não | Não |
> | localizações / azureasyncoperação | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / performancetiers | Não | Não |
> | localizações / privateendpointconnectionazureasyncoperation | Não | Não |
> | localizações / resultados da operação de operação de privateendpointconnection | Não | Não |
> | localizações / privateendpointconnectionproxyazureasyncoperation | Não | Não |
> | localizações / privateendpointconnectionproxyoperationresults | Não | Não |
> | localizações / recomendações de essãosazureasyncoperação | Não | Não |
> | localizações / recomendações de operaçãos de operações | Não | Não |
> | localizações / securityalertpoliciesazuresyncoperation | Não | Não |
> | localizações / securityalertpoliciesoperatórios | Não | Não |
> | localizações / serverkeyazureasyncoperação | Não | Não |
> | localizações / servidores de operações de operações | Não | Não |
> | operações | Não | Não |
> | servidores | Sim | Sim |
> | servidores / assessores | Não | Não |
> | servidores / chaves | Não | Não |
> | servidores / privateendpointconnectionproxies | Não | Não |
> | servidores / privateendpointconnections | Não | Não |
> | servidores / recursos de privatelinkresources | Não | Não |
> | servidores / textos de consulta | Não | Não |
> | servidores / servidores recuperáveis | Não | Não |
> | servidores / topquerystatistics | Não | Não |
> | servidores / regras de rede virtual | Não | Não |
> | servidores / estatistas | Não | Não |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DbforPostgresql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | Não | Não |
> | Locais | Não | Não |
> | localizações / administradorazureasyncoperação | Não | Não |
> | locais / administradores resultados operacionais | Não | Não |
> | localizações / azureasyncoperação | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / performancetiers | Não | Não |
> | localizações / privateendpointconnectionazureasyncoperation | Não | Não |
> | localizações / resultados da operação de operação de privateendpointconnection | Não | Não |
> | localizações / privateendpointconnectionproxyazureasyncoperation | Não | Não |
> | localizações / privateendpointconnectionproxyoperationresults | Não | Não |
> | localizações / recomendações de essãosazureasyncoperação | Não | Não |
> | localizações / recomendações de operaçãos de operações | Não | Não |
> | localizações / securityalertpoliciesazuresyncoperation | Não | Não |
> | localizações / securityalertpoliciesoperatórios | Não | Não |
> | localizações / serverkeyazureasyncoperação | Não | Não |
> | localizações / servidores de operações de operações | Não | Não |
> | operações | Não | Não |
> | grupos de servidores | Não | Não |
> | servidores | Sim | Sim |
> | servidores / assessores | Não | Não |
> | servidores / chaves | Não | Não |
> | servidores / privateendpointconnectionproxies | Não | Não |
> | servidores / privateendpointconnections | Não | Não |
> | servidores / recursos de privatelinkresources | Não | Não |
> | servidores / textos de consulta | Não | Não |
> | servidores / servidores recuperáveis | Não | Não |
> | servidores / topquerystatistics | Não | Não |
> | servidores / regras de rede virtual | Não | Não |
> | servidores / estatistas | Não | Não |
> | servidorv2 | Sim | Sim |
> | solteiroservers | Sim | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | artefactos | Sim | Sim |
> | resultados de operações | Não | Não |
> | operações | Não | Não |
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
> | grupos de aplicações / aplicações | Não | Não |
> | grupos de aplicações / desktops | Não | Não |
> | grupos de aplicações / startmenuitems | Não | Não |
> | hostpools | Sim | Sim |
> | hostpools / sessão | Não | Não |
> | hostpools / sessionhosts / utilizadores | Não | Não |
> | hostpools / utilizadores | Não | Não |
> | operações | Não | Não |
> | áreas de trabalho | Sim | Sim |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | Não | Não |
> | checkprovisioningservicenamedilability | Não | Não |
> | elásticos | Não | Não |
> | elásticos / iothubtenants | Não | Não |
> | iothubs | Sim | Sim |
> | iothubs / eventgridfilters | Não | Não |
> | iothubs / seguranças | Não | Não |
> | resultados de operações | Não | Não |
> | operações | Não | Não |
> | serviços de provisionamento | Sim | Sim |
> | usos | Não | Não |

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
> | controladores / listconnectiondetails | Não | Não |
> | Locais | Não | Não |
> | localizações / checkcontainerhostmapping | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | centros de laboratório | Não | Não |
> | laboratórios | Sim | Não |
> | laboratórios / ambientes | Sim | Sim |
> | laboratórios / corredores de serviço | Sim | Sim |
> | laboratórios / virtualmachines | Sim | Não |
> | Locais | Não | Não |
> | localizações / operações | Não | Não |
> | operações | Não | Não |
> | horários | Sim | Sim |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Não | Não |
> | resultados digitais /operação | Não | Não |
> | Locais | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | números de dados | Não | Não |
> | contas de base de dados | Sim | Sim |
> | Locais | Não | Não |
> | localizações / deletevirtualnetworkorsubnets | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / operaçõesstatus | Não | Não |
> | resultados de operações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checkdomainailability | Não | Não |
> | domínios | Sim | Sim |
> | domínios / domainownershipidentifiers | Não | Não |
> | gerassorequest | Não | Não |
> | listdomainrecommendations | Não | Não |
> | operações | Não | Não |
> | topleveldomains | Não | Não |
> | validação da informação sobreregistration | Não | Não |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | operações | Não | Não |
> | services | Sim | Sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | domínios | Sim | Sim |
> | domínios / tópicos | Não | Não |
> | subscrições de eventos | Não - não pode ser movido de forma independente, mas automaticamente movido com recurso subscrito. | Não - não pode ser movido de forma independente, mas automaticamente movido com recurso subscrito. |
> | extensõestópicas | Não | Não |
> | Locais | Não | Não |
> | localizações / subscrições de eventos | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / operaçõesstatus | Não | Não |
> | localizações / tópicos | Não | Não |
> | resultados de operações | Não | Não |
> | operações | Não | Não |
> | operastatus | Não | Não |
> | partnernamespaces | Sim | Sim |
> | partnernamespaces / eventchannels | Não | Não |
> | registos de parceiros | Não | Não |
> | partnertopics | Sim | Sim |
> | partnertopics / eventsubscriptions | Não | Não |
> | sistematópicos | Sim | Sim |
> | sistematópicos / subscrições de eventos | Não | Não |
> | tópicos | Sim | Sim |
> | tópicos | Não | Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | regiões declusters disponíveis | Não | Não |
> | checknamedilability | Não | Não |
> | checknamespaceailability | Não | Não |
> | aglomerados | Sim | Sim |
> | Locais | Não | Não |
> | localizações / deletevirtualnetworkorsubnets | Não | Não |
> | espaços de nome | Sim | Sim |
> | espaços de nomes / regras de autorização | Não | Não |
> | espaços de nome / disasterrecoveryconfigs | Não | Não |
> | espaços de nome / disasterrecoveryconfigs / checknameavailability | Não | Não |
> | espaços de nomes / eventhubs | Não | Não |
> | espaços de nome / eventhubs / autorizações | Não | Não |
> | espaços de nomes / eventhubs / grupos de consumidores | Não | Não |
> | espaços de nome / redes de regras | Não | Não |
> | operações | Não | Não |
> | sku | Não | Não |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | espaços de trabalho experimenta | Não | Não |
> | Locais | Não | Não |
> | localizações / operações | Não | Não |

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
> | operações | Não | Não |
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
> | operações | Não | Não |
> | software | Não | Não |
> | softwareupdateprofile | Não | Não |
> | datas de software | Não | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | hanainstances | Não | Não |
> | Locais | Não | Não |
> | localizações / operações | Não | Não |
> | localizações / operaçõesstatus | Não | Não |
> | operações | Não | Não |
> | sapmonitors | Sim | Sim |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | dedicados | Não | Não |
> | Locais | Não | Não |
> | operações | Não | Não |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | Sim | Sim |
> | clusters / aplicações | Não | Não |
> | clusters / resultados de operações | Não | Não |
> | Locais | Não | Não |
> | localizações / azureasyncoperações | Não | Não |
> | localizações / billingspecs | Não | Não |
> | localizações / capacidades | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / utilizações | Não | Não |
> | localizações / validadoreques | Não | Não |
> | operações | Não | Não |

> [!IMPORTANT]
> Pode mover os clusters HDInsight para um novo grupo de subscrição ou recursos. No entanto, não é possível mover-se através de subscrições os recursos de networking ligados ao cluster HDInsight (como a rede virtual, NIC ou equilibrador de carga). Além disso, não é possível mover-se para um novo grupo de recursos um NIC que esteja ligado a uma máquina virtual para o cluster.
>
> Ao mover um cluster HDInsight para uma nova subscrição, primeiro mova outros recursos (como a conta de armazenamento). Em seguida, mova o cluster HDInsight por si só.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | Não | Não |
> | Locais | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | operações | Não | Não |
> | services | Sim | Sim |
> | serviços / privateendpointconnections | Não | Não |
> | serviços / recursos privados | Não | Não |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / operaçõestatus | Não | Não |
> | máquinas | Sim | Sim |
> | máquinas / extensões | Sim | Sim |
> | operações | Não | Não |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | datamanagers | Sim | Sim |
> | operações | Não | Não |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | dispositivos | Não | Não |
> | Locais | Não | Não |
> | localizações / operações | Não | Não |
> | operações | Não | Não |
> | vnfs | Não | Não |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | componentes | Não | Não |
> | Locais | Não | Não |
> | networkscopes | Não | Não |
> | operações | Não | Não |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | empregos | Sim | Sim |
> | Locais | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | grupos de ação | Sim | Sim |
> | activitylogalerts | Não | Não |
> | alertas | Sim | Sim |
> | autoescala | Sim | Sim |
> | linha de base | Não | Não |
> | calcularbaseline | Não | Não |
> | componentes | Sim | Sim |
> | componentes / eventos | Não | Não |
> | componentes / linkedstorageaccounts | Não | Não |
> | componentes / metadados | Não | Não |
> | componentes / métricas | Não | Não |
> | componentes / planos de preços | Não | Não |
> | componentes / consulta | Não | Não |
> | datacollectionrules | Não | Não |
> | diagnósticos | Não | Não |
> | diagnósticoscategorias | Não | Não |
> | eventcategorias | Não | Não |
> | tipos de eventos | Não | Não |
> | diagnósticosgnósticos estendidos | Não | Não |
> | guestdiagnosticsettings | Não | Não |
> | listmigrationdate | Não | Não |
> | Locais | Não | Não |
> | localizações / resultados de operações | Não | Não |
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
> | operações | Não | Não |
> | privatelinkscope operastatus | Não | Não |
> | privatelinkscopes | Não | Não |
> | privatelinkscopes / privateendpointconnectionproxies | Não | Não |
> | privatelinkscopes / privateendpointconnections | Não | Não |
> | privatelinkscopes / scopedresources | Não | Não |
> | modelo de backbacktolegacypricing | Não | Não |
> | queryrules agendados | Sim | Sim |
> | topology | Não | Não |
> | transações | Não | Não |
> | vminsightsonboardingstatuses | Não | Não |
> | webtests | Sim | Sim |
> | webtests / obtertestesultfile | Não | Não |
> | livros | Sim | Sim |
> | estalagens de livros | Sim | Sim |

> [!IMPORTANT]
> Certifique-se de que a mudança para uma nova subscrição não excede as [quotas de subscrição](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | apliques | Não | Não |
> | checknamedilability | Não | Não |
> | verificaçõesubdomaindilability | Não | Não |
> | iotapps | Sim | Sim |
> | operações | Não | Não |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | Sim | Sim |
> | gráfico | Sim | Sim |
> | operações | Não | Não |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | Não | Não |
> | deletedvaults | Não | Não |
> | hsmpools | Não | Não |
> | Locais | Não | Não |
> | localizações / deletedvaults | Não | Não |
> | localizações / deletevirtualnetworkorsubnets | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | geredhsmos | Não | Não |
> | operações | Não | Não |
> | cofres | Sim | Sim |
> | cofres / políticas de acesso | Não | Não |
> | cofres / filtros eventgrid | Não | Não |
> | cofres / segredos | Não | Não |

> [!IMPORTANT]
> Os Cofres-Chave utilizados para encriptação de discos não podem ser transferidos para um grupo de recursos na mesma subscrição ou através de subscrições.

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados conectados | Sim | Sim |
> | Locais | Não | Não |
> | localizações / operações | Não | Não |
> | operações | Não | Não |
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
> | clusters / confinações de base de dados | Não | Não |
> | clusters / bases de dados | Não | Não |
> | clusters / bases de dados / ligações de dados | Não | Não |
> | clusters / bases de dados / ligações eventhub | Não | Não |
> | clusters / bases de dados / principais assinaturas | Não | Não |
> | aglomerados / principais destacamentos | Não | Não |
> | Locais | Não | Não |
> | localizações / checknameavailability | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas de laboratório | Não | Não |
> | Locais | Não | Não |
> | localizações / operações | Não | Não |
> | operações | Não | Não |
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
> | Locais | Não | Não |
> | localizações / fluxos de trabalho | Não | Não |
> | operações | Não | Não |
> | fluxos de trabalho | Sim | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | planos de compromisso | Não | Não |
> | Locais | Não | Não |
> | localizações / operações | Não | Não |
> | localizações / operaçõesstatus | Não | Não |
> | operações | Não | Não |
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
> | contas / espaços de trabalho | Não | Não |
> | contas / espaços de trabalho / projetos | Não | Não |
> | contas de equipa | Não | Não |
> | contas de equipa / espaços de trabalho | Não | Não |
> | teamaccounts / workspaces / projetos | Não | Não |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | localizações / computeoperationsstatus | Não | Não |
> | localizações / quotas | Não | Não |
> | localizações / atualizações | Não | Não |
> | localizações / utilizações | Não | Não |
> | localizações / vmsizes | Não | Não |
> | localizações / workspaceoperationsstatus | Não | Não |
> | operações | Não | Não |
> | áreas de trabalho | Não | Não |
> | espaços de trabalho / computas | Não | Não |
> | espaços de trabalho / filtros de eventos | Não | Não |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | candidaturas | Não | Não |
> | configurações de assinaturas | Não | Não |
> | configurações de manutenção | Sim | Sim |
> | atualizações | Não | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | identidades | Não | Não |
> | operações | Não | Não |
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
> | operações | Não | Não |
> | operações | Não | Não |
> | inscrições | Não | Não |
> | definições de registos | Não | Não |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | Não | Não |
> | getentities | Não | Não |
> | managementgroups | Não | Não |
> | grupos de gestão / configurações | Não | Não |
> | resultados de operações | Não | Não |
> | resultados de operações / assincoperação | Não | Não |
> | operações | Não | Não |
> | resources | Não | Não |
> | filtro de backback starttenant | Não | Não |
> | inquilinosfillstatus | Não | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |
> | contas / filtros de eventgridfilters | Não | Não |
> | contas / privateatlases | Sim | Sim |
> | operações | Não | Não |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | listas de ofertas disponíveis | Não | Não |
> | ofertas | Não | Não |
> | tipos de ofertas | Não | Não |
> | tipos de ofertas / editores | Não | Não |
> | tipos de ofertas / editoras / ofertas | Não | Não |
> | tipos de ofertas / editoras / ofertas / planos | Não | Não |
> | tipos de ofertas / editoras / ofertas / planos / acordos | Não | Não |
> | tipos de ofertas / editoras / ofertas / planos / configs | Não | Não |
> | tipos de ofertas / editoras / ofertas / planos / configs / importimage | Não | Não |
> | operações | Não | Não |
> | privadas | Não | Não |
> | privatestoreclient | Não | Não |
> | lojas privadas | Não | Não |
> | lojas privadas / ofertas | Não | Não |
> | produtos | Não | Não |
> | editores | Não | Não |
> | editores / ofertas | Não | Não |
> | editores / ofertas / alterações | Não | Não |
> | registar | Não | Não |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Não | Não |
> | listcommunicationpreference | Não | Não |
> | operações | Não | Não |
> | atualização da atribuição de prescrições | Não | Não |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | acordos | Não | Não |
> | tipos de ofertas | Não | Não |
> | operações | Não | Não |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | Não | Não |
> | Locais | Não | Não |
> | localizações / checknameavailability | Não | Não |
> | mediaservices | Sim | Sim |
> | mediaservices / filtros de conta | Não | Não |
> | mediaserviços / ativos | Não | Não |
> | mediaservices / ativos / filtros de ativos | Não | Não |
> | mediaservices / contentkeypolicies | Não | Não |
> | mediaservices / eventgridfilters | Não | Não |
> | mediaservices / liveeventoperações | Não | Não |
> | mediaservices / liveevents | Sim | Sim |
> | mediaservices / liveevents / liveoutputs | Não | Não |
> | mediaservices / liveoutputoperações | Não | Não |
> | mediaservices / streamingendpointoperações | Não | Não |
> | mediaservices / streamingendpoints | Sim | Sim |
> | mediaservices / streaminglocators | Não | Não |
> | mediaservices / streamingpolicies | Não | Não |
> | mediaservices / transforma | Não | Não |
> | mediaservices / transforma / empregos | Não | Não |
> | operações | Não | Não |

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
> | Locais | Não | Não |
> | localizações / opções de avaliação | Não | Não |
> | localizações / checknameavailability | Não | Não |
> | migrarprojects | Não | Não |
> | câmaras de mudança | Não | Não |
> | operações | Não | Não |
> | projetos | Não | Não |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | holográficosbroscastaccounts | Não | Não |
> | Locais | Não | Não |
> | localizações / checknameavailability | Não | Não |
> | contas objectunderstanding | Não | Não |
> | operações | Não | Não |
> | contas de reconeradores remotos | Sim | Sim |
> | spatialanchorsaccounts | Sim | Sim |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas netappac | Não | Não |
> | contas netappaccounts / backuppolicies | Não | Não |
> | contas de netappacs /capacitypools | Não | Não |
> | contas de netappacs / capacitypools / volumes | Não | Não |
> | contas netappaccounts / capacitypools / volumes / mounttargets | Não | Não |
> | contas de netappacs / capacitypools / volumes / snapshots | Não | Não |
> | operações | Não | Não |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | applicationgateway disponívelrequestheaders | Não | Não |
> | applicationgateway disponívelreseheaders | Não | Não |
> | applicationgatewayservervariables | Não | Não |
> | applicationgateway disponível opções | Não | Não |
> | applicationgateway disponívelwafrulesets | Não | Não |
> | pistas de aplicação | Não | Não |
> | applicationgatewaywebapplicationfirewallpolicies | Não | Não |
> | grupos de aplicações | Sim | Sim |
> | azurefirewallfqdntags | Não | Não |
> | azurefirewalls | Não | Não |
> | bastionhosts | Não | Não |
> | bgpservicecommunidades | Não | Não |
> | checkdoornamedilability | Não | Não |
> | checktrafficmanagernamedilability | Não | Não |
> | conexões | Sim | Sim |
> | ddoscustompolicias | Sim | Sim |
> | ddosprotectionplans | Não | Não |
> | dnsoperationresults | Não | Não |
> | dnsoperationstatuses | Não | Não |
> | dnszones | Sim | Sim |
> | dnszones / a | Não | Não |
> | dnszones / aaaa | Não | Não |
> | dnszones / todos os | Não | Não |
> | dnszones / caa | Não | Não |
> | dnszones / cname | Não | Não |
> | dnszones / mx | Não | Não |
> | dnszones / ns | Não | Não |
> | dnszones / ptr | Não | Não |
> | dnszones / recordets | Não | Não |
> | dnszones / soa | Não | Não |
> | dnszones / srv | Não | Não |
> | dnszones / txt | Não | Não |
> | expressroutecircuits | Não | Não |
> | vias expressas | Não | Não |
> | expressrouteserviceproviders | Não | Não |
> | firewallpolicies | Sim | Sim |
> | frontdooroperationresults | Não | Não |
> | frontdoors | Não | Não |
> | frontdoors / frontendendpoints | Não | Não |
> | frontdoorwebapplicationfirewallmanagedrulesets | Não | Não |
> | frontdoorwebapplicationfirewallpolicies | Não | Não |
> | obter re-referência de recursos | Não | Não |
> | internalnotificar | Não | Não |
> | ipallocations | Sim | Sim |
> | ipgrupos | Sim | Sim |
> | loadbalancers | Sim - SKU Básico<br>Não - SKU Padrão | Sim - SKU Básico<br>Não - SKU Padrão |
> | vias de rede locais | Sim | Sim |
> | Locais | Não | Não |
> | localizações / serviços deprivatelinks auto-aprovados | Não | Não |
> | localizações / deslegações disponíveis | Não | Não |
> | localizações / disponíveis deponts de nomeação | Não | Não |
> | localizações / serviços disponíveis | Não | Não |
> | localizações / descalços | Não | Não |
> | localizações / batchnotifyprivateendpointsforresourcemove | Não | Não |
> | localizações / batchvalidateprivateendpointsforresourcemove | Não | Não |
> | localizações / checkacceleratednetworkingsupport | Não | Não |
> | localizações / checkdnsnamedilability | Não | Não |
> | localizações / checkprivatelinkservicevisibilidade | Não | Não |
> | localizações / cometer internalazurenetworksconfiguration | Não | Não |
> | localizações / efetivo descentralização | Não | Não |
> | localizações / resultados de nfvoperations | Não | Não |
> | localizações / nfvoperations | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / operações | Não | Não |
> | localizações / marcas de serviço | Não | Não |
> | localizações / setresourceeese | Não | Não |
> | localizações / suportadavirtualmachinesizes | Não | Não |
> | localizações / utilizações | Não | Não |
> | localizações / validação de fontes | Não | Não |
> | localizações / virtualnetworkavailableendpointservices | Não | Não |
> | natgateways | Não | Não |
> | projetos de bioperiment de rede | Não | Não |
> | políticas de networkintent | Sim | Sim |
> | networkinterfaces | Sim | Sim |
> | redes de mentofils | Não | Não |
> | networksecuritygroups | Sim | Sim |
> | observadores de rede | Sim | Não |
> | observadores de rede / suportes de conexão | Sim | Não |
> | networkwatchers / fluxologs | Sim | Não |
> | observadores de rede / pingmeshes | Sim | Não |
> | operações | Não | Não |
> | p2svpngateways | Não | Não |
> | privados resultados operacionais | Não | Não |
> | privatednsoperationstatus | Não | Não |
> | zonas privadas | Sim | Sim |
> | privatednszones / a | Não | Não |
> | privatednszones / aaaa | Não | Não |
> | privatednszones / todos | Não | Não |
> | privatednszones / cname | Não | Não |
> | privatednszones / mx | Não | Não |
> | privatednszones / ptr | Não | Não |
> | privatednszones / soa | Não | Não |
> | privatednszones / srv | Não | Não |
> | privatednszones / txt | Não | Não |
> | privatednszones / virtualnetworklinks | Sim | Sim |
> | privatednszonesinternal | Não | Não |
> | privateendpointredirectmaps | Não | Não |
> | pontos privados | Sim | Sim |
> | serviços de privatelink | Não | Não |
> | endereços publicitários | Sim - SKU Básico<br>Não - SKU Padrão | Sim - SKU Básico<br>Não - SKU Padrão |
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

> [!IMPORTANT]
> Consulte [a orientação do movimento em rede](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | Não | Não |
> | checknamespaceailability | Não | Não |
> | espaços de nome | Sim | Sim |
> | espaços de nomes / notificações | Sim | Sim |
> | resultados de operações | Não | Não |
> | operações | Não | Não |

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
> | operações | Não | Não |
> | servidores | Não | Não |
> | vmwaresites | Não | Não |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | Não | Não |
> | espaços de trabalho apagados | Não | Não |
> | linktargets | Não | Não |
> | Locais | Não | Não |
> | localizações / operações | Não | Não |
> | operações | Não | Não |
> | storageinsightconfigs | Não | Não |
> | áreas de trabalho | Sim | Sim |
> | espaços de trabalho / fontes de dados | Não | Não |
> | espaços de trabalho / serviços de linkedservices | Não | Não |
> | workspaces / linkedstorageaccounts | Não | Não |
> | espaços de trabalho / metadados | Não | Não |
> | espaços de trabalho / consulta | Não | Não |
> | espaços de trabalho / scopedprivatelinkproxies | Não | Não |

> [!IMPORTANT]
> Certifique-se de que a mudança para uma nova subscrição não exceda as [quotas de subscrição](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Espaços de trabalho que têm uma conta de automação ligada não podem ser movidos. Antes de iniciar uma operação de movimento, certifique-se de desvincular quaisquer contas de automação.

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | sociedades de gestão | Não | Não |
> | configurações de gestão | Sim | Sim |
> | operações | Não | Não |
> | soluções | Sim | Sim |
> | Modos de exibição | Sim | Sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | check-serviceproviderdispondibilidade | Não | Não |
> | legacypeerings | Não | Não |
> | operações | Não | Não |
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
> | resultados das operações de ayncoperação | Não | Não |
> | operações | Não | Não |
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
> | Locais | Não | Não |
> | localizações / consolas | Não | Não |
> | localizações / utilizadores | Não | Não |
> | operações | Não | Não |
> | utilizadores | Não | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | localizações / checknameavailability | Não | Não |
> | trabalhopascoscollections | Sim | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | capacidades | Sim | Sim |
> | Locais | Não | Não |
> | localizações / checknameavailability | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / operações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftpowerplatform"></a>Microsoft.PowerPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |
> | checknamedilability | Não | Não |
> | operações | Não | Não |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas disponíveis | Não | Não |
> | registos de fornecedores | Não | Não |
> | registos de fornecedores / registos de tipos de recursos | Não | Não |
> | rollouts | Não | Não |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | localizações / operações | Não | Não |
> | operações | Não | Não |
> | áreas de trabalho | Não | Não |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | backupprotectesites | Não | Não |
> | Locais | Não | Não |
> | localizações / alocação de estamp | Não | Não |
> | localizações / atribuiçãoestamp | Não | Não |
> | localizações / backupaproperties | Não | Não |
> | localizações / backupcrossregionrestore | Não | Não |
> | localizações / backupcrrjob | Não | Não |
> | localizações / trabalhos de backupcrr | Não | Não |
> | localizações / resultados de retrocesso | Não | Não |
> | localizações / backupcrroperationsstatus | Não | Não |
> | localizações / backupprevalidateprotete | Não | Não |
> | localizações / backupstatus | Não | Não |
> | localizações / backupvalidatefeatures | Não | Não |
> | localizações / checknameavailability | Não | Não |
> | operações | Não | Não |
> | replicationeligibilityres | Não | Não |
> | cofres | Sim | Sim |

> [!IMPORTANT]
> Consulte a [orientação dos Serviços de Recuperação](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatopenShift

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / operaçõesstatus | Não | Não |
> | aglomerados de turnos abertos | Não | Não |
> | operações | Não | Não |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | Não | Não |
> | espaços de nome | Sim | Sim |
> | espaços de nomes / regras de autorização | Não | Não |
> | namespaces / ligações híbridas | Não | Não |
> | espaços de nome / ligações híbridas / regras de autorização | Não | Não |
> | espaços de nome / privateendpointconnections | Não | Não |
> | espaços de nomes / wcfrelays | Não | Não |
> | espaços de nome / wcfrelays / autorizações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |
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
> | estatísticas de disponibilidade | Não | Não |
> | estatísticas de pensão infantil | Não | Não |
> | fontes infantis | Não | Não |
> | emergentes | Não | Não |
> | eventos | Não | Não |
> | do IdP | Não | Não |
> | notificações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | calculartemplatehash | Não | Não |
> | verificar a política | Não | Não |
> | nome de checkresource | Não | Não |
> | implementações | Não | Não |
> | implantações / operações | Não | Não |
> | descrições de implementação | Não | Não |
> | implementações / registos | Não | Não |
> | ligações | Não | Não |
> | Locais | Não | Não |
> | localizações / destacamentos de operações de funcionamento | Não | Não |
> | notificar resourcejobs | Não | Não |
> | resultados de operações | Não | Não |
> | operações | Não | Não |
> | fornecedores | Não | Não |
> | grupos de recursos | Não | Não |
> | resources | Não | Não |
> | assinaturas | Não | Não |
> | assinaturas / localizações | Não | Não |
> | subscrições / resultados de operações | Não | Não |
> | assinaturas /fornecedores | Não | Não |
> | subscrições / grupos de recursos | Não | Não |
> | subscrições / grupos de recursos / recursos | Não | Não |
> | subscrições / recursos | Não | Não |
> | subscrições / tagnames | Não | Não |
> | subscrições / tagnames / tagvalues | Não | Não |
> | etiquetas | Não | Não |
> | modelospecs | Não | Não |
> | modelos / versões | Não | Não |
> | inquilinos | Não | Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações | Sim | Não |
> | checkmoderneligibility | Não | Não |
> | checknamedilability | Não | Não |
> | resultados de operações | Não | Não |
> | operações | Não | Não |
> | saasresources | Não | Não |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | Não | Não |
> | check-innamedilability | Não | Não |
> | operações | Não | Não |
> | resourcehealthmetadata | Não | Não |
> | serviços de pesquisa | Sim | Sim |

> [!IMPORTANT]
> Não se pode mover vários recursos de pesquisa em diferentes regiões numa só operação. Em vez disso, mova-os em operações separadas.

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
> | Locais | Não | Não |
> | localizações / alertas | Não | Não |
> | localizações / ligações permitidas | Não | Não |
> | localizações / listas de aplicações | Não | Não |
> | localizações / soluções de inseguranças descobertas | Não | Não |
> | localizações / soluções de segurança externa | Não | Não |
> | localizações / jitnetworkaccesspolicies | Não | Não |
> | localizações / soluções de segurança | Não | Não |
> | localizações / securitysolutionsreferencedata | Não | Não |
> | localizações / tarefas | Não | Não |
> | localizações / topologias | Não | Não |
> | operações | Não | Não |
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
> | verificação de dataconnectors | Não | Não |
> | entidades | Não | Não |
> | entidadequeries | Não | Não |
> | Incidentes | Não | Não |
> | escritórioconsents | Não | Não |
> | operações | Não | Não |
> | definições | Não | Não |
> | ameaçaintelligence | Não | Não |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços de consola | Não | Não |
> | Locais | Não | Não |
> | localizações / serviços de consola | Não | Não |
> | operações | Não | Não |

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
> | checknamedilability | Não | Não |
> | checknamespaceailability | Não | Não |
> | Locais | Não | Não |
> | localizações / deletevirtualnetworkorsubnets | Não | Não |
> | espaços de nome | Sim | Sim |
> | espaços de nomes / regras de autorização | Não | Não |
> | espaços de nome / disasterrecoveryconfigs | Não | Não |
> | espaços de nome / disasterrecoveryconfigs / checknameavailability | Não | Não |
> | espaços de nomes / filtros de eventos | Não | Não |
> | espaços de nome / redes de regras | Não | Não |
> | espaços de nomes / filas | Não | Não |
> | espaços de nome / filas / autorizações | Não | Não |
> | espaços de nomes / tópicos | Não | Não |
> | espaços de nome / tópicos / autorizações | Não | Não |
> | espaços de nomes / tópicos / subscrições | Não | Não |
> | espaços de nomes / tópicos / subscrições / regras | Não | Não |
> | operações | Não | Não |
> | premiummessaging regiões | Não | Não |
> | sku | Não | Não |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações | Não | Não |
> | aglomerados | Sim | Sim |
> | clusters / aplicações | Não | Não |
> | grupos de contentores | Não | Não |
> | conjuntos de contentores | Não | Não |
> | edgeclusters | Não | Não |
> | Locais | Não | Não |
> | localizações / agrupações | Não | Não |
> | localizações / ambientes | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / operações | Não | Não |
> | geremclusters | Não | Não |
> | redes | Não | Não |
> | operações | Não | Não |
> | lojas secretas | Não | Não |
> | volumes | Não | Não |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações | Sim | Sim |
> | grupos de contentores | Não | Não |
> | portais | Sim | Sim |
> | Locais | Não | Não |
> | localizações / operações de aplicação | Não | Não |
> | localizações / operações de gateway | Não | Não |
> | localizações / operações em rede | Não | Não |
> | localizações / secretoperações | Não | Não |
> | localizações / volumeoperações | Não | Não |
> | redes | Sim | Sim |
> | operações | Não | Não |
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
> | Locais | Não | Não |
> | localizações / checknameavailability | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / operações | Não | Não |
> | localizações / utilizações | Não | Não |
> | operações | Não | Não |
> | sinaleiro | Sim | Sim |
> | signalr / eventgridfilters | Não | Não |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | híbridosbenefits | Não | Não |
> | operações | Não | Não |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações dedefinições | Não | Não |
> | aplicações | Não | Não |
> | jitrequests | Não | Não |
> | Locais | Não | Não |
> | localizações / operações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | Não | Não |
> | instâncias | Não | Não |
> | Locais | Sim | Sim |
> | localizações / administradorazureasyncoperação | Não | Não |
> | locais / administradores resultados operacionais | Não | Não |
> | localizações / auditingsazureasyncoperação | Não | Não |
> | localizações / auditoresettings Resultados operacionais | Não | Não |
> | localizações / capacidades | Não | Não |
> | localizações / databaseazureasyncoperation | Não | Não |
> | locais / resultados operacionais da base de dados | Não | Não |
> | localizações / databaserestoreazureasyncoperação | Não | Não |
> | localizações / deletevirtualnetworkorsubnets | Não | Não |
> | localizações / deletevirtualnetworkorsubnetsazureasyncoperation | Não | Não |
> | localizações / deletevirtualnetworkorsubnetsoperationress | Não | Não |
> | localizações / dnsaliasasyncoperação | Não | Não |
> | locais / dnsalias operatórios resultados | Não | Não |
> | localizações / elasticpoolazureasyncoperação | Não | Não |
> | localizações / resultados de operações de elasticpool | Não | Não |
> | localizações / encriptaçãoprotectorazureasyncoperação | Não | Não |
> | localizações / encriptaçãoprotectorres de operações | Não | Não |
> | localizações / alargamento saquetandosazureasyncoperação | Não | Não |
> | localizações / alargamento saquesettingsoperatórios | Não | Não |
> | localizações / failovergroupazureasyncoperation | Não | Não |
> | localizações / failovergroupoperationresults | Não | Não |
> | localizações / firewallrulesazureasyncoperation | Não | Não |
> | localizações / firewallrulesoperationresults | Não | Não |
> | localizações / instânciafailovergroupazureasyncoperation | Não | Não |
> | localizações / instânciafailovergroupoperatórios | Não | Não |
> | localizações / instânciasfailovergroups | Não | Não |
> | localizações / instancepoolazureasyncoperação | Não | Não |
> | localizações / resultados da operação de instânciapool | Não | Não |
> | localizações / jobagentazureasyncoperação | Não | Não |
> | localizações / resultados operacionais do jobagent | Não | Não |
> | localizações / longtermretentionbackupazureasyncoperation | Não | Não |
> | localizações / longtermretention backupoperationress | Não | Não |
> | localizações / backups de retenção de longo prazo | Não | Não |
> | localizações / longtermretentionmanagedinstancebackupazureasyncoperation | Não | Não |
> | localizações / longtermretentionmanagedinstance backupoperationresults | Não | Não |
> | localizações / longtermretentionmanagedinstanceups | Não | Não |
> | localizações / longtermretentionmanagedinstances | Não | Não |
> | localizações / longtermretentionpolicyazuresyncoperation | Não | Não |
> | localizações / longtermretentionpolioperationress | Não | Não |
> | localizações / observadores de retenção de longo prazo | Não | Não |
> | localizações / gestão dedatabaseazureasyncoperação | Não | Não |
> | localizações / gestão dedatabasecompleterestoreazureasyncoperação | Não | Não |
> | localizações / resultados de operações de bases de dados geridos | Não | Não |
> | localizações / resultados de operações de base de dados geridos | Não | Não |
> | localizações / gestão de databaserestoreazureasyncoperação | Não | Não |
> | localizações / resultados de operações de bases de dados geridos | Não | Não |
> | localizações / managedinanceazureasyncoperation | Não | Não |
> | localizações / managedinstanceencryptionprotectorazureasyncoperation | Não | Não |
> | locais / gerdinastanceencryptionprotectorsoperatórios | Não | Não |
> | localizações / managedinstancekeyazureasyncoperation | Não | Não |
> | localizações / gerdinstancekeyoperaresults | Não | Não |
> | localizações / managedinstancelongtermretentionpolicyazureasyncoperation | Não | Não |
> | localizações / managedinstancelongtermretentionpolioperationresressults | Não | Não |
> | locais / resultados da operação de gestão | Não | Não |
> | localizações / managedinstancetdecertazuresyncoperação | Não | Não |
> | localizações / gerdinstancetdecertoperationres | Não | Não |
> | localizações / manageersecurityalertpoliciesazuresyncoperation | Não | Não |
> | localizações / manageersecurityalertpoliciesoperações | Não | Não |
> | localizações / managedshorttermretentionpolicyazureasyncoperation | Não | Não |
> | localizações / manageshorttermretentionpolioperationress | Não | Não |
> | localizações / notificaçãoazuresyncoperação | Não | Não |
> | localizações / privateendpointconnectionazureasyncoperation | Não | Não |
> | localizações / resultados da operação de operação de privateendpointconnection | Não | Não |
> | localizações / privateendpointconnectionproxyazureasyncoperation | Não | Não |
> | localizações / privateendpointconnectionproxyoperationresults | Não | Não |
> | localizações / securityalertpoliciesazuresyncoperation | Não | Não |
> | localizações / securityalertpoliciesoperatórios | Não | Não |
> | localizações / serveradministratorazureasyncoperação | Não | Não |
> | localizações / servidoradministrator operatórios resultados | Não | Não |
> | localizações / serverazureasyncoperação | Não | Não |
> | localizações / serverkeyazureasyncoperação | Não | Não |
> | localizações / servidores de operações de operações | Não | Não |
> | locais / resultados da operação do servidor | Não | Não |
> | localizações / shorttermretentionpolicyazuresyncoperation | Não | Não |
> | localizações / shorttermretentionpolioperationress | Não | Não |
> | localizações / resultados de operações sincronizadas | Não | Não |
> | localizações / sincronizados | Não | Não |
> | localizações / resultados de operações de sincronização | Não | Não |
> | locais / resultados da operação syncmember | Não | Não |
> | localizações / tdecertazureasyncoperação | Não | Não |
> | locais / resultados da operação tdecert | Não | Não |
> | localizações / utilizações | Não | Não |
> | localizações / virtualclusterazureasyncoperação | Não | Não |
> | localizações / resultados de operações de operador virtual | Não | Não |
> | localizações / virtualnetworkrulesazureasyncoperation | Não | Não |
> | localizações / virtualnetworkrulesoperationresults | Não | Não |
> | localizações / vulnerabilidadesssmentscanazureasyncoperation | Não | Não |
> | localizações / vulnerabilidadessmentscanoperationresults | Não | Não |
> | managedinstances | Não | Não |
> | managedinstances /administradores | Não | Não |
> | managedinstances / bases de dados | Não | Não |
> | managedinstances / bases de dados / backuplongtermretentionpolicies | Não | Não |
> | managedinstances / bases de dados / avaliações de vulnerabilidades | Não | Não |
> | managedinstances / metricdefinitions | Não | Não |
> | managedinstances / métricas | Não | Não |
> | managedinstances / bases de dados recuperáveis | Não | Não |
> | managedstances / tdecertificates | Não | Não |
> | managedinstances / avaliações de vulnerabilidades | Não | Não |
> | operações | Não | Não |
> | servidores | Sim | Sim |
> | servidores / administradores resultados operacionais | Não | Não |
> | servidores / administradores | Não | Não |
> | servidores / assessores | Não | Não |
> | servidores /baseimétricas de dados agregados | Não | Não |
> | servidores / políticas de auditoria | Não | Não |
> | servidores / auditings | Não | Não |
> | servidores / sintonização automática | Não | Não |
> | servidores / ligações de comunicação | Não | Não |
> | servidores / políticas de conexão | Não | Não |
> | servidores / bases de dados | Sim | Sim |
> | servidores / bases de dados / conselheiros | Não | Não |
> | servidores / bases de dados / políticas de auditoria | Não | Não |
> | servidores / bases de dados / auditings | Não | Não |
> | servidores / bases de dados / registos de auditoria | Não | Não |
> | servidores / bases de dados / sintonização automática | Não | Não |
> | servidores / bases de dados / backuplongtermretentionpolicies | Não | Não |
> | servidores / bases de dados / backupshorttermretentionpolicies | Não | Não |
> | servidores / bases de dados / políticas de conexão | Não | Não |
> | servidores / bases de dados / datamaskingpolicies | Não | Não |
> | servidores / bases de dados / datamaskingpolicies / regras | Não | Não |
> | servidores / bases de dados / extensões | Não | Não |
> | servidores / bases de dados / geopolíticas | Não | Não |
> | servidores / bases de dados / métricas | Não | Não |
> | servidores / bases de dados / métricas | Não | Não |
> | servidores / bases de dados / rótulos desensitividade recomendados | Não | Não |
> | servidores / bases de dados / seguranças | Não | Não |
> | servidores / bases de dados / syncgrupos | Não | Não |
> | servidores / bases de dados / syncmgroups / syncmembers | Não | Não |
> | servidores / bases de dados / topqueries | Não | Não |
> | servidores / bases de dados / topqueries / querytext | Não | Não |
> | servidores / bases de dados / análise de dados transparentes | Não | Não |
> | servidores / bases de dados / avaliação de vulnerabilidades | Não | Não |
> | servidores / bases de dados / avaliações de vulnerabilidades | Não | Não |
> | servidores / bases de dados / vulnerabilidadesssscans | Não | Não |
> | servidores / bases de dados / vulnerabilidades de avaliações | Não | Não |
> | servidores / bases de dados / grupos de carga de trabalho | Não | Não |
> | servidores / políticas de segurança de bases de dados | Não | Não |
> | servidores / recuperação de desastresconfiguação | Não | Não |
> | servidores / dnsaliases | Não | Não |
> | servidores / elasticpoolestimates | Não | Não |
> | servidores / elásticos | Sim | Sim |
> | servidores / elásticos / conselheiros | Não | Não |
> | servidores / elásticos / métricas | Não | Não |
> | servidores / elásticos / métricas | Não | Não |
> | servidores / projeto de encriptação | Não | Não |
> | servidores / ampliações | Não | Não |
> | servidores / grupos de failover | Não | Não |
> | servidores / importação | Não | Não |
> | servidores / importexoperationresults | Não | Não |
> | servidores / contas de emprego | Sim | Sim |
> | servidores / agentes de trabalho | Sim | Sim |
> | servidores / agentes de trabalho / empregos | Não | Não |
> | servidores / agentes de trabalho / empregos / execuções | Não | Não |
> | servidores / agentes de trabalho / empregos / passos | Não | Não |
> | servidores / chaves | Não | Não |
> | servidores / resultados de operações | Não | Não |
> | servidores /elasticpools recomendados | Não | Não |
> | servidores / bases de dados recuperáveis | Não | Não |
> | servidores / bases de dados restauradoras | Não | Não |
> | servidores / seguranças | Não | Não |
> | servidores /objetivos de serviço | Não | Não |
> | servidores / sincronizadores | Não | Não |
> | servidores / tdecertificados | Não | Não |
> | servidores / utilizações | Não | Não |
> | servidores / regras de rede virtual | Não | Não |
> | servidores / avaliações de vulnerabilidades | Não | Não |
> | virtualclusters | Sim | Sim |

> [!IMPORTANT]
> Uma base de dados e um servidor devem estar no mesmo grupo de recursos. Quando se move um servidor SQL, todas as suas bases de dados também são movidas. Este comportamento aplica-se às bases de dados Azure SQL Database e Azure Synapse Analytics.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | locais / disponibilidadegrouplisteneroperationresults | Não | Não |
> | localizações / tipos de operação | Não | Não |
> | localizações / sqlvirtualmachinegroupoperatórios | Não | Não |
> | localizações / sqlvirtualmachineoperationresults | Não | Não |
> | operações | Não | Não |
> | grupos sqlvirtualmachineinegroups | Sim | Sim |
> | sqlvirtualmachinegroups / availabilitygrouplisteners | Não | Não |
> | sqlvirtualmachines | Sim | Sim |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | Não | Não |
> | Locais | Não | Não |
> | localizações / assincoperações | Não | Não |
> | localizações / checknameavailability | Não | Não |
> | localizações / deletevirtualnetworkorsubnets | Não | Não |
> | localizações / utilizações | Não | Não |
> | operações | Não | Não |
> | contas de armazenamento | Sim | Sim |
> | contas de armazenamento / blobservices | Não | Não |
> | contas de armazenamento / serviços de ficheiros | Não | Não |
> | contas de armazenamento / listaccountsas | Não | Não |
> | contas de armazenamento / listservicesas | Não | Não |
> | contas de armazenamento / serviços de fila | Não | Não |
> | contas de armazenamento / serviços | Não | Não |
> | contas de armazenamento / serviços / métricas | Não | Não |
> | contas de armazenamento / serviços de mesa | Não | Não |
> | usos | Não | Não |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | caches | Não | Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | localizações / checknameavailability | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / operações | Não | Não |
> | localizações / fluxos de trabalho | Não | Não |
> | operações | Não | Não |
> | storagesyncservices | Sim | Sim |
> | storagesyncservices /servers registados | Não | Não |
> | storagesyncservices / syncgroups | Não | Não |
> | storagesyncservices / syncgroups / cloudendpoints | Não | Não |
> | storagesyncservices / syncgroups / serverendpoints | Não | Não |
> | storagesyncservices / workflows | Não | Não |

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
> | operações | Não | Não |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | Não | Não |
> | Locais | Não | Não |
> | localizações / quotas | Não | Não |
> | operações | Não | Não |
> | streamingjobs | Sim | Sim |

> [!IMPORTANT]
> Os trabalhos da Stream Analytics não podem ser movidos quando estão em funcionamento.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | ambientes | Não | Não |
> | ambientes / fontes de eventos | Não | Não |
> | casos | Não | Não |
> | instâncias / ambientes | Não | Não |
> | instâncias / ambientes / fontes de eventos | Não | Não |

## <a name="microsoftsubscription"></a>Microsoft.Subscrição

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | cancelar | Não | Não |
> | criação de subscrição | Não | Não |
> | ativar | Não | Não |
> | resultados de operações | Não | Não |
> | operações | Não | Não |
> | mudar o nome | Não | Não |
> | subscrições | Não | Não |
> | operações de subscrição | Não | Não |
> | assinaturas | Não | Não |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | Não | Não |
> | resultados de operações | Não | Não |
> | operações | Não | Não |
> | operastatus | Não | Não |
> | services | Não | Não |
> | serviços / classificações de problemas | Não | Não |
> | bilhetes de apoio | Não | Não |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | Não | Não |
> | operações | Não | Não |
> | áreas de trabalho | Sim | Sim |
> | espaços de trabalho / bigdatapools | Sim | Sim |
> | espaços de trabalho / resultados de operações | Não | Não |
> | espaços de trabalho / operações | Não | Não |
> | espaços de trabalho / sqlpools | Sim | Sim |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | ambientes | Sim | Sim |
> | ambientes / políticas de acesso | Não | Não |
> | ambientes / fontes de eventos | Sim | Sim |
> | ambientes / conjuntos de dados de referência | Sim | Sim |
> | operações | Não | Não |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | lojas | Sim | Sim |
> | lojas / políticas de acesso | Não | Não |
> | lojas / serviços | Não | Não |
> | lojas / serviços / tokens | Não | Não |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | imagens | Não | Não |
> | linhas de imagens / runoutputs | Não | Não |
> | Locais | Não | Não |
> | localizações / operações | Não | Não |
> | operações | Não | Não |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | conta | Não | Não |
> | conta / extensão | Não | Não |
> | conta / projeto | Não | Não |
> | checknamedilability | Não | Não |
> | operações | Não | Não |

> [!IMPORTANT]
> Para alterar a subscrição de Azure DevOps, consulte [alterar a subscrição Azure utilizada para faturação](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | arczones | Não | Não |
> | Locais | Não | Não |
> | localizações / operações | Não | Não |
> | operações | Não | Não |
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
> | Locais | Não | Não |
> | localizações / disponibilidades | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / nuvens privadas | Não | Não |
> | localizações / nuvems privadas / recursos | Não | Não |
> | localizações / privateclouds / virtualmachinetemplates | Não | Não |
> | localizações / nuvems privadas / virtualnetworks | Não | Não |
> | localizações / utilizações | Não | Não |
> | operações | Não | Não |
> | virtualmachines | Não | Não |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | dispositivos | Não | Não |
> | Locais | Não | Não |
> | localizações / operações | Não | Não |
> | operações | Não | Não |
> | vnfs | Não | Não |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |
> | operações | Não | Não |
> | planos | Não | Não |
> | assinaturas registadas | Não | Não |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | disponíveis mochilas | Não | Não |
> | billingmeters | Não | Não |
> | certificados | Não | Sim |
> | checknamedilability | Não | Não |
> | vias de conexão | Sim | Sim |
> | conexões | Sim | Sim |
> | customapis | Sim | Sim |
> | deletedsites | Não | Não |
> | implantações | Não | Não |
> | geo-regiões | Não | Não |
> | hospedar ambientes | Não | Não |
> | hospedar ambientes / filtros de eventgrid | Não | Não |
> | hospedamento de ambientes / multirolepools | Não | Não |
> | hospedamento de ambientes /casas de trabalho | Não | Não |
> | nome ishostingenvironment disponível | Não | Não |
> | ishostname disponível | Não | Não |
> | isusername disponível | Não | Não |
> | kubeenvironments | Sim | Sim |
> | nome de nome de listasassignedtohostname | Não | Não |
> | Locais | Não | Não |
> | localizações / apiperações | Não | Não |
> | localizações / instalações de connectiongateway | Não | Não |
> | localizações / locais apagados | Não | Não |
> | localizações / deletevirtualnetworkorsubnets | Não | Não |
> | localizações / extractapidefinitionfromwsdl | Não | Não |
> | localizações / obter redes políticas | Não | Não |
> | localizações / listwsdlinterfaces | Não | Não |
> | localizações / managedapis | Não | Não |
> | localizações / resultados de operações | Não | Não |
> | localizações / operações | Não | Não |
> | localizações / tempos de execução | Não | Não |
> | operações | Não | Não |
> | editores | Não | Não |
> | recomendações | Não | Não |
> | resourcehealthmetadata | Não | Não |
> | tempos de execução | Não | Não |
> | serverfarms | Sim | Sim |
> | serverfarms / eventgridfilters | Não | Não |
> | sites | Sim | Sim |
> | sites / filtros de eventos | Não | Não |
> | sites / hostnamebindings | Não | Não |
> | sites /config de rede | Não | Não |
> | sites / premieraddons | Sim | Sim |
> | sites / slots | Sim | Sim |
> | sites / slots / eventgridfilters | Não | Não |
> | sites / slots / hostnamebindings | Não | Não |
> | sites / slots / networkconfig | Não | Não |
> | controlos de fontes | Não | Não |
> | estáticasites | Não | Não |
> | validar | Não | Não |
> | verificar o desvio de ambiente | Não | Não |

> [!IMPORTANT]
> Consulte a [orientação do movimento do Serviço de Aplicações](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | localizações / operações | Não | Não |
> | multipleactivationkeys | Não | Não |
> | operações | Não | Não |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços de dispositivos | Não | Não |
> | operações | Não | Não |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | Não | Não |
> | localizações / operações | Não | Não |
> | operações | Não | Não |
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
> | notificações | Não | Não |
> | operações | Não | Não |

## <a name="third-party-services"></a>Serviços de terceiros

Os serviços de terceiros não suportam atualmente a operação de mudança.

## <a name="next-steps"></a>Próximos passos
Para que os comandos movimentem recursos, consulte [mover recursos para novo grupo de recursos ou subscrição](move-resource-group-and-subscription.md).

Para obter os mesmos dados que um ficheiro de valores separados por vírgula, faça o download [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
