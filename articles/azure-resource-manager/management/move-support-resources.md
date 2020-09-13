---
title: Mover suporte de operação por tipo de recurso
description: Lista os tipos de recursos Azure que podem ser transferidos para um novo grupo de recursos ou subscrição.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 69fcb271ac94df3faf8e9e37c1ef30a6c0681441
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566131"
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
> | serviços de domínio | No | No |
> | serviços de domínio / oucontainer | No | No |
> | Locais | No | No |
> | localizações / resultados de operações | No | No |
> | operações | No | No |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | diagnósticos | No | No |
> | diagnósticoscategorias | No | No |
> | operações | No | No |
> | privatelinkforazuread | Yes | Yes |
> | inquilinos | Yes | Yes |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | resultados de operações | No | No |
> | operações | No | No |
> | supportproviders | No | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | No | No |
> | adds serviços | No | No |
> | agentes | No | No |
> | anónimos | No | No |
> | configuração | No | No |
> | registos | No | No |
> | operações | No | No |
> | relatórios | No | No |
> | serviços de saúde | No | No |
> | services | No | No |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | configurações | No | No |
> | gerar recommendações | No | No |
> | do IdP | No | No |
> | operações | No | No |
> | recomendações | No | No |
> | supressões | No | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | regras de ação | Yes | Yes |
> | alerts | No | No |
> | lista de alertas | No | No |
> | alertasmetadata | No | No |
> | alertasummary | No | No |
> | alertasummarylista | No | No |
> | operações | No | No |
> | smartdetectoralertrules | Yes | Yes |
> | grupos inteligentes | No | No |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | No | No |
> | localizações / checknameavailability | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / operações | No | No |
> | operações | No | No |
> | servidores | Yes | Yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!IMPORTANT]
> Um serviço de Gestão API que está definido para o SKU de consumo não pode ser movido.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checkfeedrequired | No | No |
> | checknamedilability | No | No |
> | check-innamedilability | No | No |
> | operações | No | No |
> | reportfeedback | No | No |
> | serviço | Yes | Yes |
> | validar nome de serviço | No | No |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | No | No |
> | lojas de configurações | Yes | Yes |
> | lojas de configurações / filtros de eventos | No | No |
> | Locais | No | No |
> | localizações / operaçõesstatus | No | No |
> | operações | No | No |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | No | No |
> | localizações / checknameavailability | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / operaçõestatus | No | No |
> | operações | No | No |
> | primavera | Yes | Yes |
> | primavera / aplicativos | No | No |
> | mola / apps / implementações | No | No |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!IMPORTANT]
> Consulte a [orientação do movimento do Serviço de Aplicações](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | apiapps | No | No |
> | appidentidades | No | No |
> | portais | No | No |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | atesstationproviders | Yes | Yes |
> | operações | No | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checkacesss | No | No |
> | classicadministradores | No | No |
> | dataaliases | No | No |
> | denyassignments | No | No |
> | elevateaccess | No | No |
> | findorphanroleassignments | No | No |
> | fechaduras | No | No |
> | operações | No | No |
> | operaçõestatus | No | No |
> | permissões | No | No |
> | assinaturas políticas | No | No |
> | definições de políticas | No | No |
> | políticas dedefinições | No | No |
> | privatelinkassociations | No | No |
> | operações de fornecedores | No | No |
> | recursos gestagementprivatelinks | No | No |
> | roleassignments | No | No |
> | roleassignmentsusagemetrics | No | No |
> | papéis dedefinições | No | No |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!IMPORTANT]
> Os runbooks devem existir no mesmo grupo de recursos que a Conta De Automação.
>
> Para obter informações, consulte [mover a sua conta Azure Automation para outra subscrição](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas de automação | Yes | Yes |
> | contas/configurações de automação | Yes | Yes |
> | automação contas / empregos | No | No |
> | automatização contas / privateendpointconnectionproxies | No | No |
> | automação de contas / privateendpointconnections | No | No |
> | automatização de contas / recursos de privatelinkresources | No | No |
> | contas de automação / runbooks | Yes | Yes |
> | licenças de automação / softwareupdateconfigurations | No | No |
> | automação contas / webhooks | No | No |
> | operações | No | No |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | No | No |
> | localizações / checkquotaailability | No | No |
> | localizações / checktrialavailability | No | No |
> | operações | No | No |
> | nuvens privadas | Yes | Yes |
> | nuvens privadas / clusters | No | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | b2direões | Yes | Yes |
> | b2ctenants | No | No |
> | checknamedilability | No | No |
> | operações | No | No |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | datacontrollers | No | No |
> | híbridos gerentes | No | No |
> | operações | No | No |
> | pós-insinstâncias | No | No |
> | sqlinstances | No | No |
> | sqlmanagedinstances | No | No |
> | sqlserverinstances | No | No |
> | sqlserverregistrations | Yes | Yes |
> | sqlserverregistrations / sqlservers | No | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | No | No |
> | operações | No | No |
> | registos | Yes | Yes |
> | registos / assinaturas de clientes | No | No |
> | registos / produtos | No | No |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | No | No |
> | operações | No | No |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | lotaccounts | Yes | Yes |
> | Locais | No | No |
> | locais / resultados da operação de conta | No | No |
> | localizações / checknameavailability | No | No |
> | localizações / quotas | No | No |
> | operações | No | No |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas de faturação | No | No |
> | faturação/acordos | No | No |
> | contas de faturação / faturações | No | No |
> | contas de faturação / faturação | No | No |
> | faturação / faturação / equilíbrio disponível | No | No |
> | faturações / faturações / faturações | No | No |
> | faturação / faturação / billingroleassignments | No | No |
> | contas de faturação / faturação / faturação | No | No |
> | faturação / faturação / faturação | No | No |
> | faturação / faturação / createbillingroleassignment | No | No |
> | faturação / faturação / clientes | No | No |
> | faturação / faturação / instruções | No | No |
> | faturação / faturas | No | No |
> | faturação / faturas / faturas / folha de preços | No | No |
> | faturação / faturas / transações | No | No |
> | faturação / faturação / faturação | No | No |
> | faturação / faturação / faturações / faturações / faturações | No | No |
> | faturação / faturação / faturações / faturas / billingroleassignments | No | No |
> | faturação / faturação / faturações / faturações / faturantes | No | No |
> | faturação / faturação / faturação / faturações / faturasubsubscriptions | No | No |
> | faturação / faturação / faturação / faturas / createbillingroleasignment | No | No |
> | faturação / faturação / faturações / secções de faturação / iniciar transtransfer | No | No |
> | faturação / faturação / faturações / produtos | No | No |
> | faturação / faturação / faturações / produtos / transferência | No | No |
> | faturação / faturação / faturações / produtos / updateautorenew | No | No |
> | faturação / faturação / faturações / transações de faturas | No | No |
> | faturação / faturação / faturações / transferências | No | No |
> | faturação / faturação / patchoperações | No | No |
> | faturação / faturação / métodos de pagamento | No | No |
> | faturação / faturação / políticas | No | No |
> | faturação / faturação / folha de preços | No | No |
> | faturação / faturação / operações de carga de downdown de preços | No | No |
> | faturação / faturação / produtos | No | No |
> | faturação / faturação / transações | No | No |
> | billingaccounts / billingroleassignments | No | No |
> | contas de faturação / faturação | No | No |
> | contas de faturação / faturações | No | No |
> | faturações / faturas | No | No |
> | billingaccounts / createbillingroleassignment | No | No |
> | billingaccounts / criações de operaçãos | No | No |
> | contas de faturação / clientes | No | No |
> | contas de faturação / clientes / faturações | No | No |
> | contas de faturação / clientes / subscrições de faturação | No | No |
> | faturação / clientes / iniciar transtransfer | No | No |
> | faturação / clientes / políticas | No | No |
> | faturação / clientes / produtos | No | No |
> | contas de faturação / clientes / transações | No | No |
> | faturação / clientes / transfers | No | No |
> | contas de faturação /departamentos | No | No |
> | contas de faturação / matrículas | No | No |
> | faturas de faturas | No | No |
> | faturações / faturações | No | No |
> | faturações / faturações / faturasubscrições | No | No |
> | contas de faturação / faturações / faturasubsubscriptions | No | No |
> | faturações / faturações / faturasubsubscriptions / transfer | No | No |
> | faturações / faturas / elevação | No | No |
> | faturações / faturas / iniciar transtransferição | No | No |
> | faturações / faturações / patchoperações | No | No |
> | faturações / faturações / operações de produtos | No | No |
> | faturações / faturas / produtos | No | No |
> | faturação / faturas / produtos / transferência | No | No |
> | faturações / faturas / produtos / updateautorenew | No | No |
> | faturações / faturações / resultados de transferências de produtos | No | No |
> | faturações / faturas / transações | No | No |
> | faturações / faturas / transferências | No | No |
> | contas de faturação / linha de crédito | No | No |
> | billingaccounts / listinvoicesectionswithcreatesubscriptionpersion | No | No |
> | contas de faturação / resultados de operações | No | No |
> | contas de faturação / patchoperações | No | No |
> | contas de faturação / métodos de pagamento | No | No |
> | faturação / produtos | No | No |
> | contas de faturação / transações | No | No |
> | faturantes | No | No |
> | faturações | No | No |
> | billingproperty | No | No |
> | billingroleassignments | No | No |
> | billingroledefinitions | No | No |
> | createbillingroleassignment | No | No |
> | departamentos | No | No |
> | matrículas | No | No |
> | faturas | No | No |
> | resultados de operações | No | No |
> | operações | No | No |
> | operaçõestatus | No | No |
> | transferências | No | No |
> | transferências / aceitatransfer | No | No |
> | transferências / declíniotransfer | No | No |
> | transferências / operaçõestatus | No | No |
> | transferências / validar transfera | No | No |
> | validação de morada | No | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | listcommunicationpreference | No | No |
> | mapapis | No | No |
> | operações | No | No |
> | atualização da atribuição de prescrições | No | No |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | biztalk | No | No |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | membros blockchain | No | No |
> | cordamembers | No | No |
> | Locais | No | No |
> | localizações / blockchainmemberoperationresults | No | No |
> | localizações / checknameavailability | No | No |
> | localizações / listconsortiums | No | No |
> | locais / observadores resultados da operação | No | No |
> | operações | No | No |
> | observadores | No | No |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | No | No |
> | serviços de token | No | No |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | No | No |
> | blueprintassignments / operações de atribuição | No | No |
> | blueprintassignments / operações | No | No |
> | plantas | No | No |
> | plantas / artefactos | No | No |
> | plantas / versões | No | No |
> | plantas / versões / artefactos | No | No |
> | operações | No | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | botservices | Yes | Yes |
> | botservices / canais | No | No |
> | botservices / conexões | No | No |
> | checknamedilability | No | No |
> | listar de aprequantoers | No | No |
> | operações | No | No |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!IMPORTANT]
> Se a cache Azure para a instância Redis estiver configurada com uma rede virtual, o caso não pode ser movido para uma subscrição diferente. Ver [limitações de movimento de rede](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | No | No |
> | Locais | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / operaçõesstatus | No | No |
> | operações | No | No |
> | redis | Yes | Yes |
> | redis / eventgridfilters | No | No |
> | redis / privatelinkresources | No | No |
> | redisenterprise | No | No |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | reservações aplicadas | No | No |
> | calcular a troca | No | No |
> | calcular preço | No | No |
> | calcular o preço da compra | No | No |
> | catálogos | No | No |
> | checkoffers | No | No |
> | checkpurchasestatus | No | No |
> | checkscópios | No | No |
> | ordenadores de reservação comercial | No | No |
> | troca | No | No |
> | listbenefits | No | No |
> | operações | No | No |
> | placepurchaseorder | No | No |
> | encomendas de reservas | No | No |
> | pedidos de reservas / disponíveisscópios | No | No |
> | encomendas de reservas / calcular refinanciamento | No | No |
> | encomendas de reservas / fusão | No | No |
> | encomendas de reservas / reservas | No | No |
> | encomendas de reservas / reservas / disponíveisscópios | No | No |
> | encomendas de reservas / reservas / revisões | No | No |
> | pedidos de reservas / devolução | No | No |
> | pedidos de reservas / divisão | No | No |
> | pedidos de reservas / swap | No | No |
> | reservas | No | No |
> | resources | No | No |
> | validação encomenda de reservas | No | No |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | cdnwebapplication firewallmanagedrulesets | No | No |
> | cdnwebapplicationfirewallpolicies | Yes | Yes |
> | checknamedilability | No | No |
> | checkresourceusage | No | No |
> | edgenodes | No | No |
> | resultados de operações | No | No |
> | resultados de operações / resultados de perfis | No | No |
> | resultados de operações / perfis / resultados finais | No | No |
> | resultados de operações / perfis / resultados finais / resultados personalizados | No | No |
> | resultados de operações / perfis / resultados finais / resultados de grupos de origem | No | No |
> | resultados de operações / perfis / resultados de ponto final / resultados de origem | No | No |
> | operações | No | No |
> | perfis | Yes | Yes |
> | perfis / pontos finais | Yes | Yes |
> | perfis / pontos finais / personaldomains | No | No |
> | perfis / pontos finais / grupos de origem | No | No |
> | perfis / pontos finais / origens | No | No |
> | validarprobe | No | No |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!IMPORTANT]
> Consulte a [orientação do movimento do Serviço de Aplicações](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | certificadores | Yes | Yes |
> | certificadores /certificados | No | No |
> | operações | No | No |
> | validar a informação de registo certificado | No | No |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | No | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> Consulte a [orientação do movimento de implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | capacidades | No | No |
> | checkdomainmedilability | No | No |
> | temas de domínio | Yes | No |
> | domínios / capacidades | No | No |
> | domainnames / caixas de carga internas | No | No |
> | domínios / certificados de serviço | No | No |
> | temas de domínio / slots | No | No |
> | domínios / slots / funções | No | No |
> | domínios / slots / funções / métricas | No | No |
> | domínios / slots / funções / métricas | No | No |
> | movesubscriptionresources | No | No |
> | operaçãofamários de sistemas | No | No |
> | sistemas operativos | No | No |
> | operações | No | No |
> | operações | No | No |
> | quotas | No | No |
> | recursos | No | No |
> | valida a subscrição demovedabilidade | No | No |
> | virtualmachines | Yes | Yes |
> | virtualmachines / diagnósticos | No | No |
> | virtualmachines / metricdefinitions | No | No |
> | virtualmachines / métricas | No | No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!IMPORTANT]
> Consulte a [orientação do movimento de implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | No | No |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!IMPORTANT]
> Consulte a [orientação do movimento de implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | capacidades | No | No |
> | expressroute crossconnections | No | No |
> | expressroutecrossconnections / peerings | No | No |
> | gatewaysupporteddes | No | No |
> | networksecuritygroups | No | No |
> | operações | No | No |
> | quotas | No | No |
> | reservedips | No | No |
> | virtualnetworks | No | No |
> | virtualnetworks / remotevirtualnetworkpeeringproxies | No | No |
> | virtualnetworks / virtualnetworkpeerings | No | No |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!IMPORTANT]
> Consulte a [orientação do movimento de implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | capacidades | No | No |
> | verificação da contagemcountsailabilidade | No | No |
> | discos | No | No |
> | imagens | No | No |
> | operações | No | No |
> | osimagems | No | No |
> | osplatformimages | No | No |
> | imagens públicas | No | No |
> | quotas | No | No |
> | contas de armazenamento | Yes | No |
> | contas de armazenamento / blobservices | No | No |
> | contas de armazenamento / serviços de ficheiros | No | No |
> | contas de armazenamento / métricas | No | No |
> | contas de armazenamento / métricas | No | No |
> | contas de armazenamento / serviços de fila | No | No |
> | contas de armazenamento / serviços | No | No |
> | contas de armazenamento / serviços / diagnósticos | No | No |
> | contas de armazenamento / serviços / métricas | No | No |
> | contas de armazenamento / serviços / métricas | No | No |
> | contas de armazenamento / serviços de mesa | No | No |
> | contas de armazenamento / vmimages | No | No |
> | vmimages | No | No |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!IMPORTANT]
> Consulte a [orientação do movimento de implementação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos através de subscrições com uma operação específica para esse cenário.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | No | No |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Yes | Yes |
> | checkdomainailability | No | No |
> | Locais | No | No |
> | localizações / checkskuavailability | No | No |
> | localizações / deletevirtualnetworkorsubnets | No | No |
> | localizações / resultados de operações | No | No |
> | operações | No | No |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | No | No |
> | cartão de taxa | No | No |
> | usageaggregates | No | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> Veja [a orientação das Máquinas Virtuais.](./move-limitations/virtual-machines-move-limitations.md)

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | conjuntos de disponibilidades | Yes | Yes |
> | diskaccesses | No | No |
> | conjuntos de diskencrypations | No | No |
> | discos | Yes | Yes |
> | galerias | No | No |
> | galerias / imagens | No | No |
> | galerias / imagens / versões | No | No |
> | grupos de anfitriões | No | No |
> | grupos de anfitriões / anfitriões | No | No |
> | imagens | Yes | Yes |
> | Locais | No | No |
> | localizações / artefactospublishers | No | No |
> | localizações / capsoperações | No | No |
> | localizações / operações de discos | No | No |
> | localizações / loganalytics | No | No |
> | localizações / operações | No | No |
> | localizações / editores | No | No |
> | localizações / runcommands | No | No |
> | localizações / utilizações | No | No |
> | localizações / virtualmachines | No | No |
> | localizações / vmsizes | No | No |
> | localizações / vsmoperações | No | No |
> | operações | No | No |
> | grupos de destacamentos de proximidade | Yes | Yes |
> | restaurar as recordações de pontos | No | No |
> | restaurar acolecções / pontos de restauro | No | No |
> | partilha de milhões de pessoas | No | No |
> | sharedvmimages | No | No |
> | sharedvmimages / versões | No | No |
> | instantâneos | Yes | Yes |
> | sshpublickeys | No | No |
> | virtualmachines | Yes | Yes |
> | virtualmachines /extensões | Yes | Yes |
> | virtualmachines / metricdefinitions | No | No |
> | virtualmachines / runcommands | No | No |
> | virtualmachinescalesets | Yes | Yes |
> | virtualmachinescalesets /extensões | No | No |
> | virtualmachinescalesets / networkinterfaces | No | No |
> | virtualmachinescalesets / publicipaddresss | No | No |
> | virtualmachinescalesets / virtualmachines | No | No |
> | virtualmachinescalesets / virtualmachines / networkinterfaces | No | No |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | agregados de cussão | No | No |
> | saldos | No | No |
> | orçamentos | No | No |
> | encargos | No | No |
> | costtags | No | No |
> | créditos | No | No |
> | eventos | No | No |
> | previsões | No | No |
> | lotes | No | No |
> | mercados | No | No |
> | resultados de operações | No | No |
> | operações | No | No |
> | operaçõestatus | No | No |
> | folhas de preços | No | No |
> | produtos | No | No |
> | detalhes de reservas | No | No |
> | reservas recommendaçõesdedetails | No | No |
> | reservas recommendações | No | No |
> | reservas | No | No |
> | transações de reservas | No | No |
> | etiquetas | No | No |
> | inquilinos | No | No |
> | termos | No | No |
> | usagedetails | No | No |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | grupos de contentores | No | No |
> | Locais | No | No |
> | localizações / cachedimages | No | No |
> | localizações / capacidades | No | No |
> | localizações / deletevirtualnetworkorsubnets | No | No |
> | localizações / operações | No | No |
> | localizações / utilizações | No | No |
> | operações | No | No |
> | ligações de serviços de ações | No | No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | No | No |
> | Locais | No | No |
> | localizações / autorizar | No | No |
> | localizações / deletevirtualnetworkorsubnets | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / configuração de dois | No | No |
> | operações | No | No |
> | registos | Yes | Yes |
> | registos / agentpools | Yes | Yes |
> | registos / agentpools / listqueuestatus | No | No |
> | registos / construções | No | No |
> | registos / construções / cancelar | No | No |
> | registos / construções / getloglink | No | No |
> | registos / buildtasks | Yes | Yes |
> | registos / buildtasks / listsourcerepositoryproperties | No | No |
> | registos / buildtasks / passos | No | No |
> | registos / buildtasks / passos / listbuildarguments | No | No |
> | registos / filtros de eventgridfilters | No | No |
> | registos / exportpipelines | No | No |
> | registos / gerarcredentais | No | No |
> | registos / getbuildsourceuploadurl | No | No |
> | registos / obtercredentais | No | No |
> | registos / importimage | No | No |
> | registos / importações | No | No |
> | registos / listbuildsourceuploadurl | No | No |
> | registos / listcredentais | No | No |
> | registos / listpolidades | No | No |
> | registos / listusages | No | No |
> | registos / gasodutos | No | No |
> | registos / privateendpointconnectionproxies | No | No |
> | registos / privateendpointconnectionproxies / validar | No | No |
> | registos / privateendpointconnections | No | No |
> | registos / recursos de privatelinkresources | No | No |
> | registos / queuebuild | No | No |
> | registos / regeneração | No | No |
> | registos / regeneração decredentais | No | No |
> | registos / replicações | Yes | Yes |
> | registos / corre | No | No |
> | registos / corre / cancela | No | No |
> | registos / runs / listlogsasurl | No | No |
> | registos / schedulerun | No | No |
> | registos / scopemaps | No | No |
> | registos / taskruns | No | No |
> | registos / taskruns / listdetails | No | No |
> | registos / tarefas | Yes | Yes |
> | registos / tarefas / listdetails | No | No |
> | registos / fichas | No | No |
> | registos / atualização de políticas | No | No |
> | registos / webhooks | Yes | Yes |
> | registros / webhooks / getcallbackconfig | No | No |
> | registos / webhooks / listevents | No | No |
> | registos / webhooks / ping | No | No |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços de contentores | No | No |
> | Locais | No | No |
> | localizações / aglomerados de turno aberto | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / operações | No | No |
> | localizações / orquestradores | No | No |
> | geremclusters | No | No |
> | aglomerados de gestão de turnos abertos | No | No |
> | operações | No | No |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações | No | No |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | alerts | No | No |
> | contas de faturação | No | No |
> | orçamentos | No | No |
> | cloudconnectors | No | No |
> | conectores | Yes | Yes |
> | departamentos | No | No |
> | dimensões | No | No |
> | matrículas | No | No |
> | exportações | No | No |
> | contas de falcão externos | No | No |
> | contas /alertas de avaliação externa | No | No |
> | contas/dimensões de avaliação externa | No | No |
> | contas /previsão de avaliação externa | No | No |
> | contas /consultas de externalbillings | No | No |
> | subscrições externas | No | No |
> | subscrições /alertas externos | No | No |
> | subscrições/dimensões externas | No | No |
> | subscrições externas / previsão | No | No |
> | subscrições externas / consulta | No | No |
> | forecast | No | No |
> | operações | No | No |
> | consulta | No | No |
> | registar | No | No |
> | configs relatório | No | No |
> | relatórios | No | No |
> | definições | No | No |
> | showbackrules | No | No |
> | Modos de exibição | No | No |

## <a name="microsoftcostmanagementexports"></a>Microsoft.CostManagementExports

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | No | No |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | hubs | No | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | No | No |
> | pedidos | No | No |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | associações | No | No |
> | Locais | No | No |
> | localizações / operações | No | No |
> | operações | No | No |
> | recursosproviders | Yes | Yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | empregos | No | No |
> | Locais | No | No |
> | localizações / availableskus | No | No |
> | localizações / checknameavailability | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / configuração da região | No | No |
> | localizações / validação de morada | No | No |
> | localizações / validar inputções | No | No |
> | operações | No | No |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | availableskus | No | No |
> | databoxedgedevices | Yes | Yes |
> | databoxedgedevices / checknameavailability | No | No |
> | operações | No | No |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | No | No |
> | localizações / obter redes políticas | No | No |
> | localizações / operações | No | No |
> | operações | No | No |
> | áreas de trabalho | No | No |
> | espaços de trabalho / espaço de trabalho | No | No |
> | espaços de trabalho / virtualnetworkpeerings | No | No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | catálogos | Yes | Yes |
> | checknamedilability | No | No |
> | datacatalogs | No | No |
> | Locais | No | No |
> | localizações / empregos | No | No |
> | localizações / resultados de operações | No | No |
> | operações | No | No |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | gerentes de conexão | No | No |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | pacotes | No | No |
> | planos | No | No |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checkazuredatafactorynamedilability | No | No |
> | checkdatafactorynamedilability | No | No |
> | datafactories | Yes | Yes |
> | datafactories / diagnósticos | No | No |
> | datafactories / metricdefinitions | No | No |
> | datafactoryschema | No | No |
> | fábricas | Yes | Yes |
> | fábricas / integrações tempos | No | No |
> | Locais | No | No |
> | localizações / configurafactoryrepo | No | No |
> | localizações / getfeaturevalue | No | No |
> | operações | No | No |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | No | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Yes | Yes |
> | contas / datalakestoreaccounts | No | No |
> | contas / contas de armazenamento | No | No |
> | contas / contas de armazenamento / contentores | No | No |
> | contas / contas de armazenamento / contentores / listastokens | No | No |
> | Locais | No | No |
> | localizações / capacidade | No | No |
> | localizações / checknameavailability | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / utilizações | No | No |
> | operações | No | No |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Yes | Yes |
> | contas / filtros de eventgridfilters | No | No |
> | contas / firewallrules | No | No |
> | Locais | No | No |
> | localizações / capacidade | No | No |
> | localizações / checknameavailability | No | No |
> | localizações / deletevirtualnetworkorsubnets | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / utilizações | No | No |
> | operações | No | No |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | No | No |
> | localizações / checknameavailability | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / operações | No | No |
> | operações | No | No |
> | services | No | No |
> | serviços / projetos | No | No |
> | slots | No | No |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | backupvaults | No | No |
> | Locais | No | No |
> | operações | No | No |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Yes | Yes |
> | contas / ações | No | No |
> | contas / ações / conjuntos de dados | No | No |
> | contas / ações / convites | No | No |
> | contas / ações / subscrições de partilha de fornecedores | No | No |
> | contas / ações / sincronizações | No | No |
> | contas / subscrições de ações | No | No |
> | contas / subscrições de ações / conjuntos de dados de fontes de consumo | No | No |
> | contas / subscrições de ações / datasetmappings | No | No |
> | contas / subscrições de ações / gatilhos | No | No |
> | listinvitações | No | No |
> | Locais | No | No |
> | localizações / consumidores | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / rejeição | No | No |
> | operações | No | No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | No | No |
> | Locais | No | No |
> | localizações / azureasyncoperação | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / performancetiers | No | No |
> | localizações / privateendpointconnectionazureasyncoperation | No | No |
> | localizações / resultados da operação de operação de privateendpointconnection | No | No |
> | localizações / privateendpointconnectionproxyazureasyncoperation | No | No |
> | localizações / privateendpointconnectionproxyoperationresults | No | No |
> | localizações / recomendações de essãosazureasyncoperação | No | No |
> | localizações / recomendações de operaçãos de operações | No | No |
> | localizações / securityalertpoliciesazuresyncoperation | No | No |
> | localizações / securityalertpoliciesoperatórios | No | No |
> | localizações / serverkeyazureasyncoperação | No | No |
> | localizações / servidores de operações de operações | No | No |
> | operações | No | No |
> | servidores | Yes | Yes |
> | servidores / assessores | No | No |
> | servidores / privateendpointconnectionproxies | No | No |
> | servidores / privateendpointconnections | No | No |
> | servidores / recursos de privatelinkresources | No | No |
> | servidores / textos de consulta | No | No |
> | servidores / servidores recuperáveis | No | No |
> | servidores / topquerystatistics | No | No |
> | servidores / regras de rede virtual | No | No |
> | servidores / estatistas | No | No |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | No | No |
> | Locais | No | No |
> | localizações / administradorazureasyncoperação | No | No |
> | locais / administradores resultados operacionais | No | No |
> | localizações / azureasyncoperação | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / performancetiers | No | No |
> | localizações / privateendpointconnectionazureasyncoperation | No | No |
> | localizações / resultados da operação de operação de privateendpointconnection | No | No |
> | localizações / privateendpointconnectionproxyazureasyncoperation | No | No |
> | localizações / privateendpointconnectionproxyoperationresults | No | No |
> | localizações / recomendações de essãosazureasyncoperação | No | No |
> | localizações / recomendações de operaçãos de operações | No | No |
> | localizações / securityalertpoliciesazuresyncoperation | No | No |
> | localizações / securityalertpoliciesoperatórios | No | No |
> | localizações / serverkeyazureasyncoperação | No | No |
> | localizações / servidores de operações de operações | No | No |
> | operações | No | No |
> | servidores | Yes | Yes |
> | servidores / assessores | No | No |
> | servidores / chaves | No | No |
> | servidores / privateendpointconnectionproxies | No | No |
> | servidores / privateendpointconnections | No | No |
> | servidores / recursos de privatelinkresources | No | No |
> | servidores / textos de consulta | No | No |
> | servidores / servidores recuperáveis | No | No |
> | servidores / topquerystatistics | No | No |
> | servidores / regras de rede virtual | No | No |
> | servidores / estatistas | No | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DbforPostgresql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | No | No |
> | Locais | No | No |
> | localizações / administradorazureasyncoperação | No | No |
> | locais / administradores resultados operacionais | No | No |
> | localizações / azureasyncoperação | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / performancetiers | No | No |
> | localizações / privateendpointconnectionazureasyncoperation | No | No |
> | localizações / resultados da operação de operação de privateendpointconnection | No | No |
> | localizações / privateendpointconnectionproxyazureasyncoperation | No | No |
> | localizações / privateendpointconnectionproxyoperationresults | No | No |
> | localizações / recomendações de essãosazureasyncoperação | No | No |
> | localizações / recomendações de operaçãos de operações | No | No |
> | localizações / securityalertpoliciesazuresyncoperation | No | No |
> | localizações / securityalertpoliciesoperatórios | No | No |
> | localizações / serverkeyazureasyncoperação | No | No |
> | localizações / servidores de operações de operações | No | No |
> | operações | No | No |
> | grupos de servidores | No | No |
> | servidores | Yes | Yes |
> | servidores / assessores | No | No |
> | servidores / chaves | No | No |
> | servidores / privateendpointconnectionproxies | No | No |
> | servidores / privateendpointconnections | No | No |
> | servidores / recursos de privatelinkresources | No | No |
> | servidores / textos de consulta | No | No |
> | servidores / servidores recuperáveis | No | No |
> | servidores / topquerystatistics | No | No |
> | servidores / regras de rede virtual | No | No |
> | servidores / estatistas | No | No |
> | servidorv2 | Yes | Yes |
> | solteiroservers | Yes | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | artefactos | Yes | Yes |
> | resultados de operações | No | No |
> | operações | No | No |
> | rollouts | Yes | Yes |
> | topologias de serviço | Yes | Yes |
> | topologias de serviços / serviços | Yes | Yes |
> | topologias de serviços / serviços / unidades de serviço | Yes | Yes |
> | passos | Yes | Yes |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | grupos de aplicações | Yes | Yes |
> | grupos de aplicações / aplicações | No | No |
> | grupos de aplicações / desktops | No | No |
> | grupos de aplicações / startmenuitems | No | No |
> | hostpools | Yes | Yes |
> | hostpools / sessão | No | No |
> | hostpools / sessionhosts / utilizadores | No | No |
> | hostpools / utilizadores | No | No |
> | operações | No | No |
> | áreas de trabalho | Yes | Yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | No | No |
> | checkprovisioningservicenamedilability | No | No |
> | elásticos | No | No |
> | elásticos / iothubtenants | No | No |
> | iothubs | Yes | Yes |
> | iothubs / eventgridfilters | No | No |
> | iothubs / seguranças | No | No |
> | resultados de operações | No | No |
> | operações | No | No |
> | serviços de provisionamento | Yes | Yes |
> | usos | No | No |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | oleodutos | Yes | Yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | controladores | Yes | Yes |
> | controladores / listconnectiondetails | No | No |
> | Locais | No | No |
> | localizações / checkcontainerhostmapping | No | No |
> | localizações / resultados de operações | No | No |
> | operações | No | No |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | centros de laboratório | No | No |
> | laboratórios | Yes | No |
> | laboratórios / ambientes | Yes | Yes |
> | laboratórios / corredores de serviço | Yes | Yes |
> | laboratórios / virtualmachines | Yes | No |
> | Locais | No | No |
> | localizações / operações | No | No |
> | operações | No | No |
> | horários | Yes | Yes |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | No | No |
> | resultados digitais /operação | No | No |
> | Locais | No | No |
> | operações | No | No |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | números de dados | No | No |
> | contas de base de dados | Yes | Yes |
> | Locais | No | No |
> | localizações / deletevirtualnetworkorsubnets | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / operaçõesstatus | No | No |
> | resultados de operações | No | No |
> | operações | No | No |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checkdomainailability | No | No |
> | domínios | Yes | Yes |
> | domínios / domainownershipidentifiers | No | No |
> | gerassorequest | No | No |
> | listdomainrecommendations | No | No |
> | operações | No | No |
> | topleveldomains | No | No |
> | validação da informação sobreregistration | No | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | No | No |
> | localizações / resultados de operações | No | No |
> | operações | No | No |
> | services | Yes | Yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | domínios | Yes | Yes |
> | domínios / tópicos | No | No |
> | subscrições de eventos | Não - não pode ser movido de forma independente, mas automaticamente movido com recurso subscrito. | Não - não pode ser movido de forma independente, mas automaticamente movido com recurso subscrito. |
> | extensõestópicas | No | No |
> | Locais | No | No |
> | localizações / subscrições de eventos | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / operaçõesstatus | No | No |
> | localizações / tópicos | No | No |
> | resultados de operações | No | No |
> | operações | No | No |
> | operastatus | No | No |
> | partnernamespaces | Yes | Yes |
> | partnernamespaces / eventchannels | No | No |
> | registos de parceiros | No | No |
> | partnertopics | Yes | Yes |
> | partnertopics / eventsubscriptions | No | No |
> | sistematópicos | Yes | Yes |
> | sistematópicos / subscrições de eventos | No | No |
> | tópicos | Yes | Yes |
> | tópicos | No | No |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | regiões declusters disponíveis | No | No |
> | checknamedilability | No | No |
> | checknamespaceailability | No | No |
> | aglomerados | Yes | Yes |
> | Locais | No | No |
> | localizações / deletevirtualnetworkorsubnets | No | No |
> | espaços de nome | Yes | Yes |
> | espaços de nomes / regras de autorização | No | No |
> | espaços de nome / disasterrecoveryconfigs | No | No |
> | espaços de nome / disasterrecoveryconfigs / checknameavailability | No | No |
> | espaços de nomes / eventhubs | No | No |
> | espaços de nome / eventhubs / autorizações | No | No |
> | espaços de nomes / eventhubs / grupos de consumidores | No | No |
> | espaços de nome / redes de regras | No | No |
> | operações | No | No |
> | sku | No | No |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | espaços de trabalho experimenta | No | No |
> | Locais | No | No |
> | localizações / operações | No | No |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | espaços de nome | Yes | Yes |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | recursosproviders | No | No |
> | funcionalidades | No | No |
> | operações | No | No |
> | fornecedores | No | No |
> | subscrições de registos de assinaturas | No | No |

## <a name="microsoftgenomics"></a>Microsoft.Geómica

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | auto-administradores | No | No |
> | autogeridavmconfigurationprofils | No | No |
> | convidadoconfigurationasssignments | No | No |
> | operações | No | No |
> | software | No | No |
> | softwareupdateprofile | No | No |
> | datas de software | No | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | hanainstances | No | No |
> | Locais | No | No |
> | localizações / operações | No | No |
> | localizações / operaçõesstatus | No | No |
> | operações | No | No |
> | sapmonitors | Yes | Yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | dedicados | No | No |
> | Locais | No | No |
> | operações | No | No |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!IMPORTANT]
> Pode mover os clusters HDInsight para um novo grupo de subscrição ou recursos. No entanto, não é possível mover-se através de subscrições os recursos de networking ligados ao cluster HDInsight (como a rede virtual, NIC ou equilibrador de carga). Além disso, não é possível mover-se para um novo grupo de recursos um NIC que esteja ligado a uma máquina virtual para o cluster.
>
> Ao mover um cluster HDInsight para uma nova subscrição, primeiro mova outros recursos (como a conta de armazenamento). Em seguida, mova o cluster HDInsight por si só.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | Yes | Yes |
> | clusters / aplicações | No | No |
> | clusters / resultados de operações | No | No |
> | Locais | No | No |
> | localizações / azureasyncoperações | No | No |
> | localizações / billingspecs | No | No |
> | localizações / capacidades | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / utilizações | No | No |
> | localizações / validadoreques | No | No |
> | operações | No | No |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | No | No |
> | Locais | No | No |
> | localizações / resultados de operações | No | No |
> | operações | No | No |
> | services | Yes | Yes |
> | serviços / privateendpointconnections | No | No |
> | serviços / recursos privados | No | No |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / operaçõestatus | No | No |
> | máquinas | Yes | Yes |
> | máquinas / extensões | Yes | Yes |
> | operações | No | No |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | datamanagers | Yes | Yes |
> | operações | No | No |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | dispositivos | No | No |
> | Locais | No | No |
> | localizações / operações | No | No |
> | operações | No | No |
> | vnfs | No | No |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | componentes | No | No |
> | Locais | No | No |
> | networkscopes | No | No |
> | operações | No | No |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | empregos | Yes | Yes |
> | Locais | No | No |
> | localizações / resultados de operações | No | No |
> | operações | No | No |

## <a name="microsoftinsights"></a>microsoft.insights

> [!IMPORTANT]
> Certifique-se de que a mudança para uma nova subscrição não excede as [quotas de subscrição](azure-subscription-service-limits.md#azure-monitor-limits).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | grupos de ação | Yes | Yes |
> | activitylogalerts | No | No |
> | alertas | Yes | Yes |
> | autoescala | Yes | Yes |
> | linha de base | No | No |
> | calcularbaseline | No | No |
> | componentes | Yes | Yes |
> | componentes / eventos | No | No |
> | componentes / linkedstorageaccounts | No | No |
> | componentes / metadados | No | No |
> | componentes / métricas | No | No |
> | componentes / planos de preços | No | No |
> | componentes / consulta | No | No |
> | datacollectionrules | No | No |
> | diagnósticos | No | No |
> | diagnósticoscategorias | No | No |
> | eventcategorias | No | No |
> | tipos de eventos | No | No |
> | diagnósticosgnósticos estendidos | No | No |
> | guestdiagnosticsettings | No | No |
> | listmigrationdate | No | No |
> | Locais | No | No |
> | localizações / resultados de operações | No | No |
> | início de ções | No | No |
> | logprofiles | No | No |
> | registos | No | No |
> | metricalerts | No | No |
> | bases métricas | No | No |
> | metricbatch | No | No |
> | métricas | No | No |
> | espaços de nomes métricos | No | No |
> | metrics | No | No |
> | migratealertrules | No | No |
> | migrar modelo dericing | No | No |
> | livros de trabalho | No | No |
> | grupos de notificações | No | No |
> | operações | No | No |
> | privatelinkscope operastatus | No | No |
> | privatelinkscopes | No | No |
> | privatelinkscopes / privateendpointconnectionproxies | No | No |
> | privatelinkscopes / privateendpointconnections | No | No |
> | privatelinkscopes / scopedresources | No | No |
> | modelo de backbacktolegacypricing | No | No |
> | queryrules agendados | Yes | Yes |
> | topology | No | No |
> | transações | No | No |
> | vminsightsonboardingstatuses | No | No |
> | webtests | Yes | Yes |
> | webtests / obtertestesultfile | No | No |
> | livros | Yes | Yes |
> | estalagens de livros | Yes | Yes |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | apliques | No | No |
> | checknamedilability | No | No |
> | verificaçõesubdomaindilability | No | No |
> | iotapps | Yes | Yes |
> | operações | No | No |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | Yes | Yes |
> | gráfico | Yes | Yes |
> | operações | No | No |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!IMPORTANT]
> Os Cofres-Chave utilizados para encriptação de discos não podem ser transferidos para um grupo de recursos na mesma subscrição ou através de subscrições.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | No | No |
> | deletedvaults | No | No |
> | hsmpools | No | No |
> | Locais | No | No |
> | localizações / deletedvaults | No | No |
> | localizações / deletevirtualnetworkorsubnets | No | No |
> | localizações / resultados de operações | No | No |
> | geredhsmos | No | No |
> | operações | No | No |
> | cofres | Yes | Yes |
> | cofres / políticas de acesso | No | No |
> | cofres / filtros eventgrid | No | No |
> | cofres / segredos | No | No |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados conectados | Yes | Yes |
> | Locais | No | No |
> | localizações / operações | No | No |
> | operações | No | No |
> | assinaturas registadas | No | No |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | configurações de controlo de fontes | No | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | Yes | Yes |
> | clusters / confinações de base de dados | No | No |
> | clusters / bases de dados | No | No |
> | clusters / bases de dados / ligações de dados | No | No |
> | clusters / bases de dados / ligações eventhub | No | No |
> | clusters / bases de dados / principais assinaturas | No | No |
> | aglomerados / principais destacamentos | No | No |
> | Locais | No | No |
> | localizações / checknameavailability | No | No |
> | localizações / resultados de operações | No | No |
> | operações | No | No |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas de laboratório | No | No |
> | Locais | No | No |
> | localizações / operações | No | No |
> | operações | No | No |
> | utilizadores | No | No |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | hospedar ambientes | No | No |
> | contas de integração | Yes | Yes |
> | integração de ambientes | Yes | No |
> | integração de ambientesvírus /managedapis | Yes | No |
> | ambientes isolados | No | No |
> | Locais | No | No |
> | localizações / fluxos de trabalho | No | No |
> | operações | No | No |
> | fluxos de trabalho | Yes | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | planos de compromisso | No | No |
> | Locais | No | No |
> | localizações / operações | No | No |
> | localizações / operaçõesstatus | No | No |
> | operações | No | No |
> | serviços web | Yes | No |
> | áreas de trabalho | Yes | Yes |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | grupos de operacionalização | No | No |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |
> | contas / espaços de trabalho | No | No |
> | contas / espaços de trabalho / projetos | No | No |
> | contas de equipa | No | No |
> | contas de equipa / espaços de trabalho | No | No |
> | teamaccounts / workspaces / projetos | No | No |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | No | No |
> | localizações / computeoperationsstatus | No | No |
> | localizações / quotas | No | No |
> | localizações / atualizações | No | No |
> | localizações / utilizações | No | No |
> | localizações / vmsizes | No | No |
> | localizações / workspaceoperationsstatus | No | No |
> | operações | No | No |
> | áreas de trabalho | No | No |
> | espaços de trabalho / computas | No | No |
> | espaços de trabalho / filtros de eventos | No | No |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | candidaturas | No | No |
> | configurações de assinaturas | No | No |
> | configurações de manutenção | Yes | Yes |
> | atualizações | No | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | identidades | No | No |
> | operações | No | No |
> | usassignedidentidades | No | No |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | managednetworks | No | No |
> | gerednetworks / grupos de redes geridas | No | No |
> | managednetworks / managednetworkpeeringpolicies | No | No |
> | notificação | No | No |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | No | No |
> | operações | No | No |
> | operações | No | No |
> | inscrições | No | No |
> | definições de registos | No | No |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | No | No |
> | getentities | No | No |
> | managementgroups | No | No |
> | grupos de gestão / configurações | No | No |
> | resultados de operações | No | No |
> | resultados de operações / assincoperação | No | No |
> | operações | No | No |
> | resources | No | No |
> | filtro de backback starttenant | No | No |
> | inquilinosfillstatus | No | No |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | Yes | Yes |
> | contas / filtros de eventgridfilters | No | No |
> | contas / privateatlases | Yes | Yes |
> | operações | No | No |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | listas de ofertas disponíveis | No | No |
> | ofertas | No | No |
> | tipos de ofertas | No | No |
> | tipos de ofertas / editores | No | No |
> | tipos de ofertas / editoras / ofertas | No | No |
> | tipos de ofertas / editoras / ofertas / planos | No | No |
> | tipos de ofertas / editoras / ofertas / planos / acordos | No | No |
> | tipos de ofertas / editoras / ofertas / planos / configs | No | No |
> | tipos de ofertas / editoras / ofertas / planos / configs / importimage | No | No |
> | operações | No | No |
> | privadas | No | No |
> | privatestoreclient | No | No |
> | lojas privadas | No | No |
> | lojas privadas / ofertas | No | No |
> | produtos | No | No |
> | editores | No | No |
> | editores / ofertas | No | No |
> | editores / ofertas / alterações | No | No |
> | registar | No | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | classicdevservices | No | No |
> | listcommunicationpreference | No | No |
> | operações | No | No |
> | atualização da atribuição de prescrições | No | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | acordos | No | No |
> | tipos de ofertas | No | No |
> | operações | No | No |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | No | No |
> | Locais | No | No |
> | localizações / checknameavailability | No | No |
> | mediaservices | Yes | Yes |
> | mediaservices / filtros de conta | No | No |
> | mediaserviços / ativos | No | No |
> | mediaservices / ativos / filtros de ativos | No | No |
> | mediaservices / contentkeypolicies | No | No |
> | mediaservices / eventgridfilters | No | No |
> | mediaservices / liveeventoperações | No | No |
> | mediaservices / liveevents | Yes | Yes |
> | mediaservices / liveevents / liveoutputs | No | No |
> | mediaservices / liveoutputoperações | No | No |
> | mediaservices / streamingendpointoperações | No | No |
> | mediaservices / streamingendpoints | Yes | Yes |
> | mediaservices / streaminglocators | No | No |
> | mediaservices / streamingpolicies | No | No |
> | mediaservices / transforma | No | No |
> | mediaservices / transforma / empregos | No | No |
> | operações | No | No |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | apclusters | No | No |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | projetos de avaliação | No | No |
> | Locais | No | No |
> | localizações / opções de avaliação | No | No |
> | localizações / checknameavailability | No | No |
> | migrarprojects | No | No |
> | câmaras de mudança | No | No |
> | operações | No | No |
> | projetos | No | No |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | holográficosbroscastaccounts | No | No |
> | Locais | No | No |
> | localizações / checknameavailability | No | No |
> | contas objectunderstanding | No | No |
> | operações | No | No |
> | contas de reconeradores remotos | Yes | Yes |
> | spatialanchorsaccounts | Yes | Yes |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas netappac | No | No |
> | contas netappaccounts / backuppolicies | No | No |
> | contas de netappacs /capacitypools | No | No |
> | contas de netappacs / capacitypools / volumes | No | No |
> | contas netappaccounts / capacitypools / volumes / mounttargets | No | No |
> | contas de netappacs / capacitypools / volumes / snapshots | No | No |
> | operações | No | No |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> Consulte [a orientação do movimento em rede](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | applicationgateway disponívelrequestheaders | No | No |
> | applicationgateway disponívelreseheaders | No | No |
> | applicationgatewayservervariables | No | No |
> | applicationgateway disponível opções | No | No |
> | applicationgateway disponívelwafrulesets | No | No |
> | pistas de aplicação | No | No |
> | applicationgatewaywebapplicationfirewallpolicies | No | No |
> | grupos de aplicações | Yes | Yes |
> | azurefirewallfqdntags | No | No |
> | azurefirewalls | No | No |
> | bastionhosts | No | No |
> | bgpservicecommunidades | No | No |
> | checkdoornamedilability | No | No |
> | checktrafficmanagernamedilability | No | No |
> | conexões | Yes | Yes |
> | ddoscustompolicias | Yes | Yes |
> | ddosprotectionplans | No | No |
> | dnsoperationresults | No | No |
> | dnsoperationstatuses | No | No |
> | dnszones | Yes | Yes |
> | dnszones / a | No | No |
> | dnszones / aaaa | No | No |
> | dnszones / todos os | No | No |
> | dnszones / caa | No | No |
> | dnszones / cname | No | No |
> | dnszones / mx | No | No |
> | dnszones / ns | No | No |
> | dnszones / ptr | No | No |
> | dnszones / recordets | No | No |
> | dnszones / soa | No | No |
> | dnszones / srv | No | No |
> | dnszones / txt | No | No |
> | expressroutecircuits | No | No |
> | vias expressas | No | No |
> | expressrouteserviceproviders | No | No |
> | firewallpolicies | Yes | Yes |
> | frontdooroperationresults | No | No |
> | frontdoors | No | No |
> | frontdoors / frontendendpoints | No | No |
> | frontdoorwebapplicationfirewallmanagedrulesets | No | No |
> | frontdoorwebapplicationfirewallpolicies | No | No |
> | obter re-referência de recursos | No | No |
> | internalnotificar | No | No |
> | ipallocations | Yes | Yes |
> | ipgrupos | Yes | Yes |
> | loadbalancers | Sim - SKU Básico<br>Não - SKU Padrão | Sim - SKU Básico<br>Não - SKU Padrão |
> | vias de rede locais | Yes | Yes |
> | Locais | No | No |
> | localizações / serviços deprivatelinks auto-aprovados | No | No |
> | localizações / deslegações disponíveis | No | No |
> | localizações / disponíveis deponts de nomeação | No | No |
> | localizações / serviços disponíveis | No | No |
> | localizações / descalços | No | No |
> | localizações / batchnotifyprivateendpointsforresourcemove | No | No |
> | localizações / batchvalidateprivateendpointsforresourcemove | No | No |
> | localizações / checkacceleratednetworkingsupport | No | No |
> | localizações / checkdnsnamedilability | No | No |
> | localizações / checkprivatelinkservicevisibilidade | No | No |
> | localizações / cometer internalazurenetworksconfiguration | No | No |
> | localizações / efetivo descentralização | No | No |
> | localizações / resultados de nfvoperations | No | No |
> | localizações / nfvoperations | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / operações | No | No |
> | localizações / marcas de serviço | No | No |
> | localizações / setresourceeese | No | No |
> | localizações / suportadavirtualmachinesizes | No | No |
> | localizações / utilizações | No | No |
> | localizações / validação de fontes | No | No |
> | localizações / virtualnetworkavailableendpointservices | No | No |
> | natgateways | No | No |
> | projetos de bioperiment de rede | No | No |
> | políticas de networkintent | Yes | Yes |
> | networkinterfaces | Yes | Yes |
> | redes de mentofils | No | No |
> | networksecuritygroups | Yes | Yes |
> | observadores de rede | Yes | No |
> | observadores de rede / suportes de conexão | Yes | No |
> | networkwatchers / fluxologs | Yes | No |
> | observadores de rede / pingmeshes | Yes | No |
> | operações | No | No |
> | p2svpngateways | No | No |
> | privados resultados operacionais | No | No |
> | privatednsoperationstatus | No | No |
> | zonas privadas | Yes | Yes |
> | privatednszones / a | No | No |
> | privatednszones / aaaa | No | No |
> | privatednszones / todos | No | No |
> | privatednszones / cname | No | No |
> | privatednszones / mx | No | No |
> | privatednszones / ptr | No | No |
> | privatednszones / soa | No | No |
> | privatednszones / srv | No | No |
> | privatednszones / txt | No | No |
> | privatednszones / virtualnetworklinks | Yes | Yes |
> | privatednszonesinternal | No | No |
> | privateendpointredirectmaps | No | No |
> | pontos privados | Yes | Yes |
> | serviços de privatelink | No | No |
> | endereços publicitários | Sim - SKU Básico<br>Não - SKU Padrão | Sim - SKU Básico<br>Não - SKU Padrão |
> | publicipprefixos | Yes | Yes |
> | filtros de rota | No | No |
> | routetables | Yes | Yes |
> | segurançapartnerproviders | Yes | Yes |
> | serviceendpointpolicies | Yes | Yes |
> | trafficmanagergeographichierarchies | No | No |
> | trafficmanagerprofiles | Yes | Yes |
> | trafficmanagerprofiles / heatmaps | No | No |
> | trafficmanagerusermetricskeys | No | No |
> | virtualhubs | No | No |
> | vias virtualnetworkgateways | Yes | Yes |
> | virtualnetworks | Yes | Yes |
> | virtualnetworktaps | No | No |
> | virtualrouters | Yes | Yes |
> | virtualwans | No | No |
> | VPNgateways (Wan Virtual) | No | No |
> | vpnsnserverconfigurations | No | No |
> | vpnsites (WAN virtual) | No | No |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | No | No |
> | checknamespaceailability | No | No |
> | espaços de nome | Yes | Yes |
> | espaços de nomes / notificações | Yes | Yes |
> | resultados de operações | No | No |
> | operações | No | No |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | osnamepas | Yes | Yes |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | hipervsites | No | No |
> | importações | No | No |
> | operações | No | No |
> | servidores | No | No |
> | vmwaresites | No | No |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!IMPORTANT]
> Certifique-se de que a mudança para uma nova subscrição não exceda as [quotas de subscrição](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Espaços de trabalho que têm uma conta de automação ligada não podem ser movidos. Antes de iniciar uma operação de movimento, certifique-se de desvincular quaisquer contas de automação.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | No | No |
> | espaços de trabalho apagados | No | No |
> | linktargets | No | No |
> | Locais | No | No |
> | localizações / operações | No | No |
> | operações | No | No |
> | storageinsightconfigs | No | No |
> | áreas de trabalho | Yes | Yes |
> | espaços de trabalho / fontes de dados | No | No |
> | espaços de trabalho / serviços de linkedservices | No | No |
> | workspaces / linkedstorageaccounts | No | No |
> | espaços de trabalho / metadados | No | No |
> | espaços de trabalho / consulta | No | No |
> | espaços de trabalho / scopedprivatelinkproxies | No | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | sociedades de gestão | No | No |
> | configurações de gestão | Yes | Yes |
> | operações | No | No |
> | soluções | Yes | Yes |
> | Modos de exibição | Yes | Yes |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | check-serviceproviderdispondibilidade | No | No |
> | legacypeerings | No | No |
> | operações | No | No |
> | peerasns | No | No |
> | peeringlocações | No | No |
> | espreiteiros | No | No |
> | países de serviço de peering | No | No |
> | localização de serviços de espre éming | No | No |
> | peeringserviceproviders | No | No |
> | serviços de espreiteiros | No | No |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | resultados das operações de ayncoperação | No | No |
> | operações | No | No |
> | eventos de política | No | No |
> | estados de política | No | No |
> | recursos de recursos de pista de política | No | No |
> | remediações | No | No |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Consolas do | No | No |
> | dashboards | Yes | Yes |
> | Locais | No | No |
> | localizações / consolas | No | No |
> | localizações / utilizadores | No | No |
> | operações | No | No |
> | utilizadores | No | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | No | No |
> | localizações / checknameavailability | No | No |
> | trabalhopascoscollections | Yes | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | capacidades | Yes | Yes |
> | Locais | No | No |
> | localizações / checknameavailability | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / operações | No | No |
> | operações | No | No |

## <a name="microsoftpowerplatform"></a>Microsoft.PowerPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | No | No |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |
> | checknamedilability | No | No |
> | operações | No | No |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | contas disponíveis | No | No |
> | registos de fornecedores | No | No |
> | registos de fornecedores / registos de tipos de recursos | No | No |
> | rollouts | No | No |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | No | No |
> | localizações / operações | No | No |
> | operações | No | No |
> | áreas de trabalho | No | No |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!IMPORTANT]
> Consulte a [orientação dos Serviços de Recuperação](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | backupprotectesites | No | No |
> | Locais | No | No |
> | localizações / alocação de estamp | No | No |
> | localizações / atribuiçãoestamp | No | No |
> | localizações / backupaproperties | No | No |
> | localizações / backupcrossregionrestore | No | No |
> | localizações / backupcrrjob | No | No |
> | localizações / trabalhos de backupcrr | No | No |
> | localizações / resultados de retrocesso | No | No |
> | localizações / backupcrroperationsstatus | No | No |
> | localizações / backupprevalidateprotete | No | No |
> | localizações / backupstatus | No | No |
> | localizações / backupvalidatefeatures | No | No |
> | localizações / checknameavailability | No | No |
> | operações | No | No |
> | replicationeligibilityres | No | No |
> | cofres | Yes | Yes |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatopenShift

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / operaçõesstatus | No | No |
> | aglomerados de turnos abertos | No | No |
> | operações | No | No |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | No | No |
> | espaços de nome | Yes | Yes |
> | espaços de nomes / regras de autorização | No | No |
> | namespaces / ligações híbridas | No | No |
> | espaços de nome / ligações híbridas / regras de autorização | No | No |
> | espaços de nome / privateendpointconnections | No | No |
> | espaços de nomes / wcfrelays | No | No |
> | espaços de nome / wcfrelays / autorizações | No | No |
> | operações | No | No |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | operações | No | No |
> | consultas | Yes | Yes |
> | resourcechangedetails | No | No |
> | mudanças de recursos | No | No |
> | resources | No | No |
> | recursos história | No | No |
> | subscriçõesstatus | No | No |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | estatísticas de disponibilidade | No | No |
> | estatísticas de pensão infantil | No | No |
> | fontes infantis | No | No |
> | emergentes | No | No |
> | eventos | No | No |
> | do IdP | No | No |
> | notificações | No | No |
> | operações | No | No |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | calculartemplatehash | No | No |
> | verificar a política | No | No |
> | nome de checkresource | No | No |
> | implementações | No | No |
> | implantações / operações | No | No |
> | descrições de implementação | No | No |
> | implementações / registos | No | No |
> | ligações | No | No |
> | Locais | No | No |
> | localizações / destacamentos de operações de funcionamento | No | No |
> | notificar resourcejobs | No | No |
> | resultados de operações | No | No |
> | operações | No | No |
> | fornecedores | No | No |
> | grupos de recursos | No | No |
> | resources | No | No |
> | assinaturas | No | No |
> | assinaturas / localizações | No | No |
> | subscrições / resultados de operações | No | No |
> | assinaturas /fornecedores | No | No |
> | subscrições / grupos de recursos | No | No |
> | subscrições / grupos de recursos / recursos | No | No |
> | subscrições / recursos | No | No |
> | subscrições / tagnames | No | No |
> | subscrições / tagnames / tagvalues | No | No |
> | etiquetas | No | No |
> | modelospecs | No | No |
> | modelos / versões | No | No |
> | inquilinos | No | No |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações | Yes | No |
> | checkmoderneligibility | No | No |
> | checknamedilability | No | No |
> | resultados de operações | No | No |
> | operações | No | No |
> | saasresources | No | No |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!IMPORTANT]
> Não se pode mover vários recursos de pesquisa em diferentes regiões numa só operação. Em vez disso, mova-os em operações separadas.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | No | No |
> | check-innamedilability | No | No |
> | operações | No | No |
> | resourcehealthmetadata | No | No |
> | serviços de pesquisa | Yes | Yes |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | adaptíostas de redes | No | No |
> | advancedthreatprotectionsettings | No | No |
> | alerts | No | No |
> | permitidas ligações | No | No |
> | listas de aplicaçõeswhitelistings | No | No |
> | avaliaçãometadata | No | No |
> | avaliações | No | No |
> | autodismissalertsrules | No | No |
> | automações | Yes | Yes |
> | autoprovisionings | No | No |
> | resultados de conformidade | No | No |
> | conformidades | No | No |
> | datacollectionagentes | No | No |
> | grupos de dispositivos de insegurança | No | No |
> | descobertas sões de inseguranças | No | No |
> | sões de segurança externa | No | No |
> | políticas de proteção de informação | No | No |
> | soluções de iotsecuritys | Yes | Yes |
> | iotsecuritysolutions / analyticsmodels | No | No |
> | iotsecuritysolutions / analyticsmodels / agregados | No | No |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | No | No |
> | jitnetworkacsspolicies | No | No |
> | Locais | No | No |
> | localizações / alertas | No | No |
> | localizações / ligações permitidas | No | No |
> | localizações / listas de aplicações | No | No |
> | localizações / soluções de inseguranças descobertas | No | No |
> | localizações / soluções de segurança externa | No | No |
> | localizações / jitnetworkaccesspolicies | No | No |
> | localizações / soluções de segurança | No | No |
> | localizações / securitysolutionsreferencedata | No | No |
> | localizações / tarefas | No | No |
> | localizações / topologias | No | No |
> | operações | No | No |
> | políticas | No | No |
> | preços | No | No |
> | padrões de regulação | No | No |
> | padrões de regulação /regulatórios | No | No |
> | padrões de regulação / regulatórios de controlo /regulatórios | No | No |
> | contactos de segurança | No | No |
> | garantias de segurança | No | No |
> | securitysolutionsreferencedata | No | No |
> | estados de segurança | No | No |
> | securitystatusessummaries | No | No |
> | servidorvulnerabilityaments | No | No |
> | definições | No | No |
> | subassessments | No | No |
> | tarefas | No | No |
> | topologias | No | No |
> | espaços de trabalho | No | No |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | agregações | No | No |
> | alertas | No | No |
> | alertaruletemplas | No | No |
> | regras automation | No | No |
> | marcadores | No | No |
> | casos | No | No |
> | dataconnectors | No | No |
> | verificação de dataconnectors | No | No |
> | entidades | No | No |
> | entidadequeries | No | No |
> | Incidentes | No | No |
> | escritórioconsents | No | No |
> | operações | No | No |
> | definições | No | No |
> | ameaçaintelligence | No | No |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços de consola | No | No |
> | Locais | No | No |
> | localizações / serviços de consola | No | No |
> | operações | No | No |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | portais | No | No |
> | osdes | No | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | No | No |
> | checknamespaceailability | No | No |
> | Locais | No | No |
> | localizações / deletevirtualnetworkorsubnets | No | No |
> | espaços de nome | Yes | Yes |
> | espaços de nomes / regras de autorização | No | No |
> | espaços de nome / disasterrecoveryconfigs | No | No |
> | espaços de nome / disasterrecoveryconfigs / checknameavailability | No | No |
> | espaços de nomes / filtros de eventos | No | No |
> | espaços de nome / redes de regras | No | No |
> | espaços de nomes / filas | No | No |
> | espaços de nome / filas / autorizações | No | No |
> | espaços de nomes / tópicos | No | No |
> | espaços de nome / tópicos / autorizações | No | No |
> | espaços de nomes / tópicos / subscrições | No | No |
> | espaços de nomes / tópicos / subscrições / regras | No | No |
> | operações | No | No |
> | premiummessaging regiões | No | No |
> | sku | No | No |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações | No | No |
> | aglomerados | Yes | Yes |
> | clusters / aplicações | No | No |
> | grupos de contentores | No | No |
> | conjuntos de contentores | No | No |
> | edgeclusters | No | No |
> | Locais | No | No |
> | localizações / agrupações | No | No |
> | localizações / ambientes | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / operações | No | No |
> | geremclusters | No | No |
> | redes | No | No |
> | operações | No | No |
> | lojas secretas | No | No |
> | volumes | No | No |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações | Yes | Yes |
> | grupos de contentores | No | No |
> | portais | Yes | Yes |
> | Locais | No | No |
> | localizações / operações de aplicação | No | No |
> | localizações / operações de gateway | No | No |
> | localizações / operações em rede | No | No |
> | localizações / secretoperações | No | No |
> | localizações / volumeoperações | No | No |
> | redes | Yes | Yes |
> | operações | No | No |
> | segredos | Yes | Yes |
> | volumes | Yes | Yes |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | rollouts | No | No |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | No | No |
> | localizações / checknameavailability | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / operações | No | No |
> | localizações / utilizações | No | No |
> | operações | No | No |
> | sinaleiro | Yes | Yes |
> | signalr / eventgridfilters | No | No |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | híbridosbenefits | No | No |
> | operações | No | No |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aplicações dedefinições | No | No |
> | aplicações | No | No |
> | jitrequests | No | No |
> | Locais | No | No |
> | localizações / operações | No | No |
> | operações | No | No |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> Uma base de dados e um servidor devem estar no mesmo grupo de recursos. Quando se move um servidor SQL, todas as suas bases de dados também são movidas. Este comportamento aplica-se às bases de dados Azure SQL Database e Azure Synapse Analytics.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | No | No |
> | instâncias | No | No |
> | Locais | Yes | Yes |
> | localizações / administradorazureasyncoperação | No | No |
> | locais / administradores resultados operacionais | No | No |
> | localizações / auditingsazureasyncoperação | No | No |
> | localizações / auditoresettings Resultados operacionais | No | No |
> | localizações / capacidades | No | No |
> | localizações / databaseazureasyncoperation | No | No |
> | locais / resultados operacionais da base de dados | No | No |
> | localizações / databaserestoreazureasyncoperação | No | No |
> | localizações / deletevirtualnetworkorsubnets | No | No |
> | localizações / deletevirtualnetworkorsubnetsazureasyncoperation | No | No |
> | localizações / deletevirtualnetworkorsubnetsoperationress | No | No |
> | localizações / dnsaliasasyncoperação | No | No |
> | locais / dnsalias operatórios resultados | No | No |
> | localizações / elasticpoolazureasyncoperação | No | No |
> | localizações / resultados de operações de elasticpool | No | No |
> | localizações / encriptaçãoprotectorazureasyncoperação | No | No |
> | localizações / encriptaçãoprotectorres de operações | No | No |
> | localizações / alargamento saquetandosazureasyncoperação | No | No |
> | localizações / alargamento saquesettingsoperatórios | No | No |
> | localizações / failovergroupazureasyncoperation | No | No |
> | localizações / failovergroupoperationresults | No | No |
> | localizações / firewallrulesazureasyncoperation | No | No |
> | localizações / firewallrulesoperationresults | No | No |
> | localizações / instânciafailovergroupazureasyncoperation | No | No |
> | localizações / instânciafailovergroupoperatórios | No | No |
> | localizações / instânciasfailovergroups | No | No |
> | localizações / instancepoolazureasyncoperação | No | No |
> | localizações / resultados da operação de instânciapool | No | No |
> | localizações / jobagentazureasyncoperação | No | No |
> | localizações / resultados operacionais do jobagent | No | No |
> | localizações / longtermretentionbackupazureasyncoperation | No | No |
> | localizações / longtermretention backupoperationress | No | No |
> | localizações / backups de retenção de longo prazo | Yes | Yes |
> | localizações / longtermretentionmanagedinstancebackupazureasyncoperation | No | No |
> | localizações / longtermretentionmanagedinstance backupoperationresults | No | No |
> | localizações / longtermretentionmanagedinstanceups | No | No |
> | localizações / longtermretentionmanagedinstances | No | No |
> | localizações / longtermretentionpolicyazuresyncoperation | No | No |
> | localizações / longtermretentionpolioperationress | No | No |
> | localizações / observadores de retenção de longo prazo | No | No |
> | localizações / gestão dedatabaseazureasyncoperação | No | No |
> | localizações / gestão dedatabasecompleterestoreazureasyncoperação | No | No |
> | localizações / resultados de operações de bases de dados geridos | No | No |
> | localizações / resultados de operações de base de dados geridos | No | No |
> | localizações / gestão de databaserestoreazureasyncoperação | No | No |
> | localizações / resultados de operações de bases de dados geridos | No | No |
> | localizações / managedinanceazureasyncoperation | No | No |
> | localizações / managedinstanceencryptionprotectorazureasyncoperation | No | No |
> | locais / gerdinastanceencryptionprotectorsoperatórios | No | No |
> | localizações / managedinstancekeyazureasyncoperation | No | No |
> | localizações / gerdinstancekeyoperaresults | No | No |
> | localizações / managedinstancelongtermretentionpolicyazureasyncoperation | No | No |
> | localizações / managedinstancelongtermretentionpolioperationresressults | No | No |
> | locais / resultados da operação de gestão | No | No |
> | localizações / managedinstancetdecertazuresyncoperação | No | No |
> | localizações / gerdinstancetdecertoperationres | No | No |
> | localizações / manageersecurityalertpoliciesazuresyncoperation | No | No |
> | localizações / manageersecurityalertpoliciesoperações | No | No |
> | localizações / managedshorttermretentionpolicyazureasyncoperation | No | No |
> | localizações / manageshorttermretentionpolioperationress | No | No |
> | localizações / notificaçãoazuresyncoperação | No | No |
> | localizações / privateendpointconnectionazureasyncoperation | No | No |
> | localizações / resultados da operação de operação de privateendpointconnection | No | No |
> | localizações / privateendpointconnectionproxyazureasyncoperation | No | No |
> | localizações / privateendpointconnectionproxyoperationresults | No | No |
> | localizações / securityalertpoliciesazuresyncoperation | No | No |
> | localizações / securityalertpoliciesoperatórios | No | No |
> | localizações / serveradministratorazureasyncoperação | No | No |
> | localizações / servidoradministrator operatórios resultados | No | No |
> | localizações / serverazureasyncoperação | No | No |
> | localizações / serverkeyazureasyncoperação | No | No |
> | localizações / servidores de operações de operações | No | No |
> | locais / resultados da operação do servidor | No | No |
> | localizações / shorttermretentionpolicyazuresyncoperation | No | No |
> | localizações / shorttermretentionpolioperationress | No | No |
> | localizações / resultados de operações sincronizadas | No | No |
> | localizações / sincronizados | No | No |
> | localizações / resultados de operações de sincronização | No | No |
> | locais / resultados da operação syncmember | No | No |
> | localizações / tdecertazureasyncoperação | No | No |
> | locais / resultados da operação tdecert | No | No |
> | localizações / utilizações | No | No |
> | localizações / virtualclusterazureasyncoperação | No | No |
> | localizações / resultados de operações de operador virtual | No | No |
> | localizações / virtualnetworkrulesazureasyncoperation | No | No |
> | localizações / virtualnetworkrulesoperationresults | No | No |
> | localizações / vulnerabilidadesssmentscanazureasyncoperation | No | No |
> | localizações / vulnerabilidadessmentscanoperationresults | No | No |
> | managedinstances | No | No |
> | managedinstances /administradores | No | No |
> | managedinstances / bases de dados | No | No |
> | managedinstances / bases de dados / backuplongtermretentionpolicies | No | No |
> | managedinstances / bases de dados / avaliações de vulnerabilidades | No | No |
> | managedinstances / metricdefinitions | No | No |
> | managedinstances / métricas | No | No |
> | managedinstances / bases de dados recuperáveis | No | No |
> | managedstances / tdecertificates | No | No |
> | managedinstances / avaliações de vulnerabilidades | No | No |
> | operações | No | No |
> | servidores | Yes | Yes |
> | servidores / administradores resultados operacionais | No | No |
> | servidores / administradores | No | No |
> | servidores / assessores | No | No |
> | servidores /baseimétricas de dados agregados | No | No |
> | servidores / políticas de auditoria | No | No |
> | servidores / auditings | No | No |
> | servidores / sintonização automática | No | No |
> | servidores / ligações de comunicação | No | No |
> | servidores / políticas de conexão | No | No |
> | servidores / bases de dados | Yes | Yes |
> | servidores / bases de dados / conselheiros | No | No |
> | servidores / bases de dados / políticas de auditoria | No | No |
> | servidores / bases de dados / auditings | No | No |
> | servidores / bases de dados / registos de auditoria | No | No |
> | servidores / bases de dados / sintonização automática | No | No |
> | servidores / bases de dados / backuplongtermretentionpolicies | Yes | Yes |
> | servidores / bases de dados / backupshorttermretentionpolicies | No | No |
> | servidores / bases de dados / políticas de conexão | No | No |
> | servidores / bases de dados / datamaskingpolicies | No | No |
> | servidores / bases de dados / datamaskingpolicies / regras | No | No |
> | servidores / bases de dados / extensões | No | No |
> | servidores / bases de dados / geopolíticas | No | No |
> | servidores / bases de dados / métricas | No | No |
> | servidores / bases de dados / métricas | No | No |
> | servidores / bases de dados / rótulos desensitividade recomendados | No | No |
> | servidores / bases de dados / seguranças | No | No |
> | servidores / bases de dados / syncgrupos | No | No |
> | servidores / bases de dados / syncmgroups / syncmembers | No | No |
> | servidores / bases de dados / topqueries | No | No |
> | servidores / bases de dados / topqueries / querytext | No | No |
> | servidores / bases de dados / análise de dados transparentes | No | No |
> | servidores / bases de dados / avaliação de vulnerabilidades | No | No |
> | servidores / bases de dados / avaliações de vulnerabilidades | No | No |
> | servidores / bases de dados / vulnerabilidadesssscans | No | No |
> | servidores / bases de dados / vulnerabilidades de avaliações | No | No |
> | servidores / bases de dados / grupos de carga de trabalho | No | No |
> | servidores / políticas de segurança de bases de dados | No | No |
> | servidores / recuperação de desastresconfiguação | No | No |
> | servidores / dnsaliases | No | No |
> | servidores / elasticpoolestimates | No | No |
> | servidores / elásticos | Yes | Yes |
> | servidores / elásticos / conselheiros | No | No |
> | servidores / elásticos / métricas | No | No |
> | servidores / elásticos / métricas | No | No |
> | servidores / projeto de encriptação | No | No |
> | servidores / ampliações | No | No |
> | servidores / grupos de failover | No | No |
> | servidores / importação | No | No |
> | servidores / importexoperationresults | No | No |
> | servidores / contas de emprego | Yes | Yes |
> | servidores / agentes de trabalho | Yes | Yes |
> | servidores / agentes de trabalho / empregos | No | No |
> | servidores / agentes de trabalho / empregos / execuções | No | No |
> | servidores / agentes de trabalho / empregos / passos | No | No |
> | servidores / chaves | No | No |
> | servidores / resultados de operações | No | No |
> | servidores /elasticpools recomendados | No | No |
> | servidores / bases de dados recuperáveis | No | No |
> | servidores / bases de dados restauradoras | No | No |
> | servidores / seguranças | No | No |
> | servidores /objetivos de serviço | No | No |
> | servidores / sincronizadores | No | No |
> | servidores / tdecertificados | No | No |
> | servidores / utilizações | No | No |
> | servidores / regras de rede virtual | No | No |
> | servidores / avaliações de vulnerabilidades | No | No |
> | virtualclusters | Yes | Yes |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | No | No |
> | locais / disponibilidadegrouplisteneroperationresults | No | No |
> | localizações / tipos de operação | No | No |
> | localizações / sqlvirtualmachinegroupoperatórios | No | No |
> | localizações / sqlvirtualmachineoperationresults | No | No |
> | operações | No | No |
> | grupos sqlvirtualmachineinegroups | Yes | Yes |
> | sqlvirtualmachinegroups / availabilitygrouplisteners | No | No |
> | sqlvirtualmachines | Yes | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | No | No |
> | Locais | No | No |
> | localizações / assincoperações | No | No |
> | localizações / checknameavailability | No | No |
> | localizações / deletevirtualnetworkorsubnets | No | No |
> | localizações / utilizações | No | No |
> | operações | No | No |
> | contas de armazenamento | Yes | Yes |
> | contas de armazenamento / blobservices | No | No |
> | contas de armazenamento / serviços de ficheiros | No | No |
> | contas de armazenamento / listaccountsas | No | No |
> | contas de armazenamento / listservicesas | No | No |
> | contas de armazenamento / serviços de fila | No | No |
> | contas de armazenamento / serviços | No | No |
> | contas de armazenamento / serviços / métricas | No | No |
> | contas de armazenamento / serviços de mesa | No | No |
> | usos | No | No |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | caches | No | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | No | No |
> | localizações / checknameavailability | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / operações | No | No |
> | localizações / fluxos de trabalho | No | No |
> | operações | No | No |
> | storagesyncservices | Yes | Yes |
> | storagesyncservices /servers registados | No | No |
> | storagesyncservices / syncgroups | No | No |
> | storagesyncservices / syncgroups / cloudendpoints | No | No |
> | storagesyncservices / syncgroups / serverendpoints | No | No |
> | storagesyncservices / workflows | No | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | No | No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | No | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | gestores | No | No |
> | operações | No | No |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!IMPORTANT]
> Os trabalhos da Stream Analytics não podem ser movidos quando estão em funcionamento.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | aglomerados | No | No |
> | Locais | No | No |
> | localizações / quotas | No | No |
> | operações | No | No |
> | streamingjobs | Yes | Yes |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | ambientes | No | No |
> | ambientes / fontes de eventos | No | No |
> | casos | No | No |
> | instâncias / ambientes | No | No |
> | instâncias / ambientes / fontes de eventos | No | No |

## <a name="microsoftsubscription"></a>Microsoft.Subscrição

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | cancelar | No | No |
> | criação de subscrição | No | No |
> | ativar | No | No |
> | resultados de operações | No | No |
> | operações | No | No |
> | mudar o nome | No | No |
> | subscrições | No | No |
> | operações de subscrição | No | No |
> | assinaturas | No | No |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | No | No |
> | resultados de operações | No | No |
> | operações | No | No |
> | operastatus | No | No |
> | services | No | No |
> | serviços / classificações de problemas | No | No |
> | bilhetes de apoio | No | No |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | checknamedilability | No | No |
> | operações | No | No |
> | áreas de trabalho | Yes | Yes |
> | espaços de trabalho / bigdatapools | Yes | Yes |
> | espaços de trabalho / resultados de operações | No | No |
> | espaços de trabalho / operações | No | No |
> | espaços de trabalho / sqlpools | Yes | Yes |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | ambientes | Yes | Yes |
> | ambientes / políticas de acesso | No | No |
> | ambientes / fontes de eventos | Yes | Yes |
> | ambientes / conjuntos de dados de referência | Yes | Yes |
> | operações | No | No |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | lojas | Yes | Yes |
> | lojas / políticas de acesso | No | No |
> | lojas / serviços | No | No |
> | lojas / serviços / tokens | No | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | imagens | No | No |
> | linhas de imagens / runoutputs | No | No |
> | Locais | No | No |
> | localizações / operações | No | No |
> | operações | No | No |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!IMPORTANT]
> Para alterar a subscrição de Azure DevOps, consulte [alterar a subscrição Azure utilizada para faturação](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | conta | No | No |
> | conta / extensão | No | No |
> | conta / projeto | No | No |
> | checknamedilability | No | No |
> | operações | No | No |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | arczones | No | No |
> | Locais | No | No |
> | localizações / operações | No | No |
> | operações | No | No |
> | recursos | No | No |
> | vcenters | No | No |
> | virtualmachines | No | No |
> | virtualmachinetemplates | No | No |
> | virtualnetworks | No | No |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | não-100 dedicados | No | No |
> | serviços decloud dedicados | No | No |
> | Locais | No | No |
> | localizações / disponibilidades | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / nuvens privadas | No | No |
> | localizações / nuvems privadas / recursos | No | No |
> | localizações / privateclouds / virtualmachinetemplates | No | No |
> | localizações / nuvems privadas / virtualnetworks | No | No |
> | localizações / utilizações | No | No |
> | operações | No | No |
> | virtualmachines | No | No |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | dispositivos | No | No |
> | Locais | No | No |
> | localizações / operações | No | No |
> | operações | No | No |
> | vnfs | No | No |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |
> | operações | No | No |
> | planos | No | No |
> | assinaturas registadas | No | No |

## <a name="microsoftweb"></a>Microsoft.Web

> [!IMPORTANT]
> Consulte a [orientação do movimento do Serviço de Aplicações](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | disponíveis mochilas | No | No |
> | billingmeters | No | No |
> | certificados | No | Yes |
> | checknamedilability | No | No |
> | vias de conexão | Yes | Yes |
> | conexões | Yes | Yes |
> | customapis | Yes | Yes |
> | deletedsites | No | No |
> | implantações | No | No |
> | geo-regiões | No | No |
> | hospedar ambientes | No | No |
> | hospedar ambientes / filtros de eventgrid | No | No |
> | hospedamento de ambientes / multirolepools | No | No |
> | hospedamento de ambientes /casas de trabalho | No | No |
> | nome ishostingenvironment disponível | No | No |
> | ishostname disponível | No | No |
> | isusername disponível | No | No |
> | kubeenvironments | Yes | Yes |
> | nome de nome de listasassignedtohostname | No | No |
> | Locais | No | No |
> | localizações / apiperações | No | No |
> | localizações / instalações de connectiongateway | No | No |
> | localizações / locais apagados | No | No |
> | localizações / deletevirtualnetworkorsubnets | No | No |
> | localizações / extractapidefinitionfromwsdl | No | No |
> | localizações / obter redes políticas | No | No |
> | localizações / listwsdlinterfaces | No | No |
> | localizações / managedapis | No | No |
> | localizações / resultados de operações | No | No |
> | localizações / operações | No | No |
> | localizações / tempos de execução | No | No |
> | operações | No | No |
> | editores | No | No |
> | recomendações | No | No |
> | resourcehealthmetadata | No | No |
> | tempos de execução | No | No |
> | serverfarms | Yes | Yes |
> | serverfarms / eventgridfilters | No | No |
> | sites | Yes | Yes |
> | sites / filtros de eventos | No | No |
> | sites / hostnamebindings | No | No |
> | sites /config de rede | No | No |
> | sites / premieraddons | Yes | Yes |
> | sites / slots | Yes | Yes |
> | sites / slots / eventgridfilters | No | No |
> | sites / slots / hostnamebindings | No | No |
> | sites / slots / networkconfig | No | No |
> | controlos de fontes | No | No |
> | estáticasites | No | No |
> | validar | No | No |
> | verificar o desvio de ambiente | No | No |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | No | No |
> | localizações / operações | No | No |
> | multipleactivationkeys | No | No |
> | operações | No | No |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | serviços de dispositivos | No | No |
> | operações | No | No |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | Locais | No | No |
> | localizações / operações | No | No |
> | operações | No | No |
> | cargas de trabalho | No | No |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Grupo de recursos | Subscrição |
> | ------------- | ----------- | ---------- |
> | componentes | No | No |
> | consumada de componentes | No | No |
> | monitorinstâncias | No | No |
> | monitorinstancessummary | No | No |
> | monitores | No | No |
> | notificações | No | No |
> | operações | No | No |

## <a name="third-party-services"></a>Serviços de terceiros

Os serviços de terceiros não suportam atualmente a operação de mudança.

## <a name="next-steps"></a>Próximos passos
Para que os comandos movimentem recursos, consulte [mover recursos para novo grupo de recursos ou subscrição](move-resource-group-and-subscription.md).

Para obter os mesmos dados que um ficheiro de valores separados por vírgula, faça o download [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
