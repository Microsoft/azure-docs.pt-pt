---
title: Azure Resource Manager a exclusão do modo completo por tipo de recurso
description: Mostra como os tipos de recurso lidam com a exclusão de modo completo em modelos de Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/04/2019
ms.author: tomfitz
ms.openlocfilehash: 7f804e61f77b745aa654852f4c1413a8f4bf53b4
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779954"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Exclusão de recursos do Azure para implantações de modo completo

Este artigo descreve como os tipos de recursos tratam da exclusão quando não estão em um modelo implantado no modo completo.

Os tipos de recurso marcados com **Sim** são excluídos quando o tipo não está no modelo implantado com o modo completo.

Os tipos de recurso marcados sem não são excluídos automaticamente quando não estão no modelo; no entanto, elas serão excluídas se o recurso pai for excluído. Para obter uma descrição completa do comportamento, consulte [Azure Resource Manager modos de implantação](deployment-modes.md).

Ir para um namespace do provedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft. AADDomainServices](#microsoftaaddomainservices)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. atestador](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft. Azconfig](#microsoftazconfig)
> - [Microsoft. Azure. Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. consumo](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft. Features](#microsoftfeatures)
> - [Microsoft. Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft. Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedLab](#microsoftmanagedlab)
> - [Microsoft. Managedservices](#microsoftmanagedservices)
> - [Microsoft. Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft. Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft. MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft. RemoteApp](#microsoftremoteapp)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | DomainServices | Sim |
> | DomainServices/oucontainer | Não |
> | DomainServices/ReplicaSets | Sim |

## <a name="microsoftaaddomainservices"></a>Microsoft. AADDomainServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | domínios | Não |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | supportProviders | Não |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aadsupportcases | Não |
> | adicionaservices | Não |
> | agentes | Não |
> | anonymousapiusers | Não |
> | configuração | Não |
> | logs | Não |
> | relatórios | Não |
> | servicehealthmetrics | Não |
> | services | Não |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Figura | Não |
> | generateRecommendations | Não |
> | do IdP | Não |
> | Recomendações | Não |
> | supressões | Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | actionRules | Sim |
> | alerts | Não |
> | alertas | Não |
> | alertsMetaData | Não |
> | alertsSummary | Não |
> | alertsSummaryList | Não |
> | receber | Não |
> | smartDetectorAlertRules | Não |
> | smartDetectorRuntimeEnvironments | Não |
> | smartGroups | Não |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | servidores | Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | reportFeedback | Não |
> | serviço | Sim |
> | validateServiceName | Não |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | configurationStores | Sim |
> | configurationStores/eventGridFilters | Não |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | attestationProviders | Não |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | classicAdministrators | Não |
> | Aliases de dataalias | Não |
> | denyAssignments | Não |
> | elevateAccess | Não |
> | bloquea | Não |
> | permissões | Não |
> | policyAssignments | Não |
> | policyDefinitions | Não |
> | policySetDefinitions | Não |
> | providerOperations | Não |
> | roleAssignments | Não |
> | roleDefinitions | Não |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | automationAccounts | Sim |
> | automationAccounts/configurations | Sim |
> | automationAccounts/jobs | Não |
> | automationAccounts/runbooks | Sim |
> | automationAccounts/softwareUpdateConfigurations | Não |
> | automationAccounts/webhooks | Não |

## <a name="microsoftazconfig"></a>Microsoft. Azconfig

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | configurationStores | Sim |
> | configurationStores/eventGridFilters | Não |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | sistemas | Não |
> | ambientes/contas | Não |
> | environments/accounts/namespaces | Não |
> | environments/accounts/namespaces/configurations | Não |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | b2cDirectories | Sim |
> | b2ctenants | Não |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | sqlServerRegistrations | Sim |
> | sqlServerRegistrations/sqlservers | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | registros | Sim |
> | registrations/customerSubscriptions | Não |
> | registros/produtos | Não |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | batchAccounts | Sim |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | billingAccounts | Não |
> | billingAccounts/contratos | Não |
> | billingAccounts/billingProfiles | Não |
> | billingAccounts/billingProfiles/billingSubscriptions | Não |
> | billingAccounts/billingProfiles/invoices | Não |
> | billingAccounts/billingProfiles/invoices/pricesheet | Não |
> | billingAccounts/billingProfiles/invoiceSections | Não |
> | billingAccounts/BillingProfiles/patchOperations | Não |
> | billingAccounts/billingProfiles/paymentMethods | Não |
> | billingAccounts/billingProfiles/políticas | Não |
> | billingAccounts/billingProfiles/pricesheet | Não |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | Não |
> | billingAccounts/billingProfiles/products | Não |
> | billingAccounts/billingProfiles/transactions | Não |
> | billingAccounts/billingSubscriptions | Não |
> | billingAccounts/createInvoiceSectionOperations | Não |
> | billingAccounts/clientes | Não |
> | billingAccounts/clientes/billingSubscriptions | Não |
> | billingAccounts/departments | Não |
> | billingAccounts/enrollmentAccounts | Não |
> | billingAccounts/faturas | Não |
> | billingAccounts/invoiceSections | Não |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | Não |
> | billingAccounts/invoiceSections/billingSubscriptions | Não |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Não |
> | billingAccounts/invoiceSections/Elevate | Não |
> | billingAccounts/invoiceSections/initiateTransfer | Não |
> | billingAccounts/invoiceSections/patchOperations | Não |
> | billingAccounts/invoiceSections/productMoveOperations | Não |
> | billingAccounts/invoiceSections/products | Não |
> | billingAccounts/invoiceSections/produtos/transferência | Não |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | Não |
> | billingAccounts/invoiceSections/transações | Não |
> | billingAccounts/invoiceSections/transfers | Não |
> | billingAccounts/lineOfCredit | Não |
> | billingAccounts/patchOperations | Não |
> | billingAccounts/paymentMethods | Não |
> | billingAccounts/produtos | Não |
> | billingAccounts/transactions | Não |
> | billingPeriods | Não |
> | billingPermissions | Não |
> | billproperty | Não |
> | billingRoleAssignments | Não |
> | billingRoleDefinitions | Não |
> | createBillingRoleAssignment | Não |
> | departamentos | Não |
> | enrollmentAccounts | Não |
> | faturas | Não |
> | transferir | Não |
> | transfers/acceptTransfer | Não |
> | transferências/declineTransfer | Não |
> | transferências/operationStatus | Não |
> | validateAddress | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | mapApis | Sim |
> | updateCommunicationPreference | Não |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | BizTalk | Sim |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | blockchainMembers | Sim |
> | observadores | Sim |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | blueprintAssignments | Não |
> | blueprintAssignments/assignmentOperations | Não |
> | blueprintAssignments/operations | Não |
> | planos gráficos | Não |
> | plantas/artefatos | Não |
> | plantas/versões | Não |
> | plantas/versões/artefatos | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | botServices | Sim |
> | botServices/channels | Não |
> | botServices/connections | Não |
> | Idiomas | Não |
> | modelos | Não |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Redis | Sim |
> | RedisConfigDefinition | Não |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | appliedReservations | Não |
> | calculateExchange | Não |
> | calculatePrice | Não |
> | calculatePurchasePrice | Não |
> | catálogos | Não |
> | commercialReservationOrders | Não |
> | exchange | Não |
> | placePurchaseOrder | Não |
> | reservationOrders | Não |
> | reservationOrders/calculateRefund | Não |
> | reservationOrders/merge | Não |
> | reservationOrders/reservations | Não |
> | reservationOrders/reservations/revisions | Não |
> | reservationOrders/return | Não |
> | reservationOrders/split | Não |
> | reservationOrders/swap | Não |
> | reservas | Não |
> | recursos | Não |
> | validateReservationOrder | Não |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Não |
> | CdnWebApplicationFirewallPolicies | Sim |
> | edgenodes | Não |
> | profiles | Sim |
> | perfis/pontos de extremidade | Sim |
> | perfis/pontos de extremidade/customdomains | Não |
> | perfis/pontos de extremidade/origens | Não |
> | validateProbe | Não |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | certificateOrders | Sim |
> | certificateOrders/certificates | Não |
> | validateCertificateRegistrationInformation | Não |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | técnicas | Não |
> | domainNames | Sim |
> | domainNames/capabilities | Não |
> | domainNames/internalLoadBalancers | Não |
> | domainNames/serviceCertificates | Não |
> | domainNames/slots | Não |
> | domainNames/slots/roles | Não |
> | nome_do_domínio/Slots/funções/metricDefinitions | Não |
> | DomainNames/Slots/funções/métricas | Não |
> | moveSubscriptionResources | Não |
> | operatingSystemFamilies | Não |
> | operatingSystems | Não |
> | quotas | Não |
> | resourceType | Não |
> | validateSubscriptionMoveAvailability | Não |
> | virtualMachines | Sim |
> | virtualMachines/diagnosticSettings | Não |
> | virtualMachines/metricDefinitions | Não |
> | virtualMachines/métricas | Não |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | classicInfrastructureResources | Não |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | técnicas | Não |
> | expressRouteCrossConnections | Não |
> | expressRouteCrossConnections/peerings | Não |
> | gatewaySupportedDevices | Não |
> | networkSecurityGroups | Sim |
> | quotas | Não |
> | reservedIps | Sim |
> | virtualNetworks | Sim |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Não |
> | virtualNetworks/virtualNetworkPeerings | Não |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | técnicas | Não |
> | discos | Não |
> | imagens | Não |
> | osImages | Não |
> | osPlatformImages | Não |
> | publicImages | Não |
> | quotas | Não |
> | storageAccounts | Sim |
> | storageAccounts/metricDefinitions | Não |
> | storageAccounts/métricas | Não |
> | storageAccounts/services | Não |
> | storageAccounts/services/diagnosticSettings | Não |
> | storageAccounts/serviços/metricDefinitions | Não |
> | storageAccounts/serviços/métricas | Não |
> | storageAccounts/vmImages | Não |
> | vmImages | Não |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | contas | Sim |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | RateCard | Não |
> | UsageAggregates | Não |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | availabilitySets | Sim |
> | diskEncryptionSets | Sim |
> | discos | Sim |
> | galerias | Sim |
> | galerias/aplicativos | Sim |
> | galerias/aplicativos/versões | Sim |
> | galerias/imagens | Sim |
> | galleries/images/versions | Sim |
> | Hosts | Sim |
> | Hosts/hosts | Sim |
> | imagens | Sim |
> | proximityPlacementGroups | Sim |
> | restorePointCollections | Sim |
> | restorePointCollections/restorePoints | Não |
> | sharedVMImages | Sim |
> | sharedVMImages/versions | Sim |
> | instantâneos | Sim |
> | virtualMachines | Sim |
> | virtualMachines/extensões | Sim |
> | virtualMachines/metricDefinitions | Não |
> | virtualMachines/scriptJobs | Não |
> | virtualMachines/softwareUpdateDeployments | Não |
> | virtualMachineScaleSets | Sim |
> | virtualMachineScaleSets/extensions | Não |
> | virtualMachineScaleSets/networkInterfaces | Não |
> | virtualMachineScaleSets/publicIPAddresses | Não |
> | virtualMachineScaleSets/virtualMachines | Não |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Não |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | AggregatedCost | Não |
> | Saldos | Não |
> | Orçamentos | Não |
> | Encargos | Não |
> | CostTags | Não |
> | crédito | Não |
> | eventos | Não |
> | Previsões | Não |
> | muitas | Não |
> | Marketplaces | Não |
> | Pricesheets | Não |
> | produtos | Não |
> | ReservationDetails | Não |
> | ReservationRecommendations | Não |
> | ReservationSummaries | Não |
> | ReservationTransactions | Não |
> | Tags | Não |
> | locatários | Não |
> | Termos | Não |
> | UsageDetails | Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | containerGroups | Sim |
> | serviceAssociationLinks | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | registros | Sim |
> | registros/compilações | Não |
> | registros/compilações/cancelar | Não |
> | registries/builds/getLogLink | Não |
> | registries/buildTasks | Sim |
> | registries/buildTasks/steps | Não |
> | registries/eventGridFilters | Não |
> | registries/getBuildSourceUploadUrl | Não |
> | registros/GetCredentials | Não |
> | registries/importImage | Não |
> | registries/queueBuild | Não |
> | registries/regenerateCredential | Não |
> | registries/regenerateCredentials | Não |
> | registros/replicações | Sim |
> | registros/execuções | Não |
> | registros/execuções/cancelamento | Não |
> | registries/scheduleRun | Não |
> | registros/tarefas | Sim |
> | registries/updatePolicies | Não |
> | registros/WebHooks | Sim |
> | registries/webhooks/getCallbackConfig | Não |
> | registros/WebHooks/ping | Não |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | containerServices | Sim |
> | managedClusters | Sim |
> | openShiftManagedClusters | Sim |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicações | Sim |
> | updateCommunicationPreference | Não |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | contas | Sim |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Alertas | Não |
> | BillingAccounts | Não |
> | Orçamentos | Não |
> | CloudConnectors | Não |
> | Conectores | Sim |
> | Departamentos | Não |
> | Dimensões | Não |
> | EnrollmentAccounts | Não |
> | Exportações | Não |
> | ExternalBillingAccounts | Não |
> | ExternalBillingAccounts/alertas | Não |
> | ExternalBillingAccounts/dimensões | Não |
> | ExternalBillingAccounts/previsão | Não |
> | ExternalBillingAccounts/consulta | Não |
> | ExternalSubscriptions | Não |
> | ExternalSubscriptions/alertas | Não |
> | ExternalSubscriptions/dimensões | Não |
> | ExternalSubscriptions/previsão | Não |
> | ExternalSubscriptions/consulta | Não |
> | Previsão | Não |
> | Consulta | Não |
> | Registr | Não |
> | Reportconfigs | Não |
> | Relatórios | Não |
> | Definições | Não |
> | showbackRules | Não |
> | Vistas | Não |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | hubs | Sim |
> | hubs/authorizationPolicies | Não |
> | hubs/conectores | Não |
> | hubs/conectores/mapeamentos | Não |
> | hubs/interações | Não |
> | hubs/kpi | Não |
> | hubs/links | Não |
> | hubs/perfis | Não |
> | hubs/roleAssignments | Não |
> | hubs/funções | Não |
> | hubs/suggestTypeSchema | Não |
> | hubs/exibições | Não |
> | hubs/widgetTypes | Não |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | pedidos | Não |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | SAS | Não |
> | resourceProviders | Sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | tarefas | Sim |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Sim |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | áreas de trabalho | Sim |
> | workspaces/virtualNetworkPeerings | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | catálogos | Sim |
> | catálogos de | Sim |
> | datacatalogs/scantargets | Não |
> | datacatalogs/scantargets/DataSets | Não |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | connectionManagers | Sim |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | dataFactories | Sim |
> | dataFactories/diagnosticSettings | Não |
> | datafactorings/metricDefinitions | Não |
> | dataFactorySchema | Não |
> | fábricas | Sim |
> | factories/integrationRuntimes | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | contas | Sim |
> | accounts/dataLakeStoreAccounts | Não |
> | accounts/storageAccounts | Não |
> | accounts/storageAccounts/containers | Não |
> | contas/transferAnalyticsUnits | Não |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | contas | Sim |
> | accounts/eventGridFilters | Não |
> | accounts/firewallRules | Não |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | services | Sim |
> | services/projects | Sim |
> | períodos | Sim |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | contas | Sim |
> | contas/compartilhamentos | Não |
> | contas/compartilhamentos/conjuntos de valores | Não |
> | contas/compartilhamentos/convites | Não |
> | contas/compartilhamentos/providersharesubscriptions | Não |
> | contas/compartilhamentos/synchronizationSettings | Não |
> | contas/sharesubscriptions | Não |
> | contas/sharesubscriptions/consumerSourceDataSets | Não |
> | contas/sharesubscriptions/datasetmappings | Não |
> | contas/sharesubscriptions/gatilhos | Não |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | servidores | Sim |
> | servidores/consultores | Não |
> | servers/queryTexts | Não |
> | servers/recoverableServers | Não |
> | servers/topQueryStatistics | Não |
> | servers/virtualNetworkRules | Não |
> | servers/waitStatistics | Não |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | servidores | Sim |
> | servidores/consultores | Não |
> | servers/queryTexts | Não |
> | servers/recoverableServers | Não |
> | servers/topQueryStatistics | Não |
> | servers/virtualNetworkRules | Não |
> | servers/waitStatistics | Não |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | serverGroups | Sim |
> | servidores | Sim |
> | servidores/consultores | Não |
> | servers/queryTexts | Não |
> | servers/recoverableServers | Não |
> | servers/topQueryStatistics | Não |
> | servers/virtualNetworkRules | Não |
> | servers/waitStatistics | Não |
> | serversv2 | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | artifactSources | Sim |
> | distribuições | Sim |
> | pertopologias | Sim |
> | serviços e pertopologias | Sim |
> | pertopologias/serviços/unidades de serviço | Sim |
> | passos | Sim |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | applicationgroups | Sim |
> | applicationgroups/aplicativos | Não |
> | applicationgroups/assignedusers | Não |
> | applicationgroups/startmenuitems | Não |
> | hostpools | Sim |
> | hostpools/sessionhosts | Não |
> | hostpools/sessionhosts/usersessions | Não |
> | hostpools/usersessions | Não |
> | áreas de trabalho | Sim |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | ElasticPools | Sim |
> | ElasticPools/IotHubTenants | Sim |
> | IotHubs | Sim |
> | IotHubs/eventGridFilters | Não |
> | ProvisioningServices | Sim |
> | usos | Não |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | pipelines | Sim |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | controladores | Sim |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | labcenters | Sim |
> | prático | Sim |
> | laboratórios/ambientes | Sim |
> | labs/serviceRunners | Sim |
> | labs/virtualMachines | Sim |
> | agendamento | Sim |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | databaseAccountNames | Não |
> | databaseAccounts | Sim |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | domínios | Sim |
> | domains/domainOwnershipIdentifiers | Não |
> | generateSsoRequest | Não |
> | topLevelDomains | Não |
> | validateDomainRegistrationInformation | Não |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | lcsprojects | Não |
> | lcsprojects/clouddeployments | Não |
> | lcsprojects/conectores | Não |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | services | Sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | domínios | Sim |
> | domínios/tópicos | Não |
> | eventSubscriptions | Não |
> | extensionTopics | Não |
> | Tópicos | Sim |
> | topicTypes | Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | clusters | Sim |
> | namespaces | Sim |
> | namespaces/authorizationrules | Não |
> | namespaces/disasterrecoveryconfigs | Não |
> | namespaces/Eventhubs | Não |
> | namespaces/eventhubs/authorizationrules | Não |
> | namespaces/eventhubs/consumergroups | Não |
> | namespaces/networkrulesets | Não |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | elástica | Não |
> | Fornecedores | Não |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Registr | Não |
> | galleryitems | Não |
> | generateartifactaccessuri | Não |
> | minhas áreas | Não |
> | myddds/áreas | Não |
> | myddds/áreas/áreas | Não |
> | myareas/areas/areas/galleryitems | Não |
> | myareas/areas/galleryitems | Não |
> | myareas/galleryitems | Não |
> | Registr | Não |
> | recursos | Não |
> | retrieveresourcesbyid | Não |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | contas | Sim |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | guestConfigurationAssignments | Não |
> | antivírus | Não |
> | softwareUpdateProfile | Não |
> | softwareUpdates | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | hanaInstances | Sim |
> | sapMonitors | Sim |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | dedicatedHSMs | Sim |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | clusters | Sim |
> | clusters/aplicativos | Não |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | services | Sim |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | computadores | Sim |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Gerenciadores de DataManager | Sim |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | QC | Sim |
> | networkScopes | Sim |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | tarefas | Sim |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | diagnosticSettings | Não |
> | diagnosticSettingsCategories | Não |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | appTemplates | Não |
> | IoTApps | Sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Graph | Sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | deletedVaults | Não |
> | hsmPools | Sim |
> | cofres | Sim |
> | vaults/accessPolicies | Não |
> | cofres/eventGridFilters | Não |
> | cofres/segredos | Não |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | clusters | Sim |
> | clusters/attacheddatabaseconfigurations | Não |
> | clusters/bancos de dados | Não |
> | clusters/bancos de dados/conexões DataConnections | Não |
> | clusters/bancos de dados/eventhubconnections | Não |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | labaccounts | Sim |
> | Utilizadores | Não |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | hostingEnvironments | Sim |
> | integrationAccounts | Sim |
> | integrationServiceEnvironments | Sim |
> | isolatedEnvironments | Sim |
> | fluxos | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | commitmentPlans | Sim |
> | webServices | Sim |
> | Áreas de trabalho | Sim |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | áreas de trabalho | Sim |
> | espaços de trabalho/computações | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Identidades | Não |
> | userAssignedIdentities | Sim |

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | labaccounts | Sim |

## <a name="microsoftmanagedservices"></a>Microsoft. Managedservices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | Não |
> | registrationAssignments | Não |
> | registrationDefinitions | Não |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | getEntities | Não |
> | managementGroups | Não |
> | recursos | Não |
> | startTenantBackfill | Não |
> | tenantBackfillStatus | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | contas | Sim |
> | accounts/eventGridFilters | Não |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | ofereça | Não |
> | offerTypes | Não |
> | offerTypes/Publicadores | Não |
> | offerTypes/publishers/offers | Não |
> | offerTypes/publishers/offers/plans | Não |
> | offerTypes/publishers/offers/plans/agreements | Não |
> | offerTypes/publishers/offers/plans/configs | Não |
> | offerTypes/publishers/offers/plans/configs/importImage | Não |
> | privategalleryitems | Não |
> | produtos | Não |
> | publicadores | Não |
> | editores/ofertas | Não |
> | editores/ofertas/emendas | Não |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | classicDevServices | Sim |
> | updateCommunicationPreference | Não |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | licenças | Não |
> | offertypes | Não |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | mediaservices | Sim |
> | mediaservices/accountFilters | Não |
> | mediaservices/ativos | Não |
> | mediaservices/assets/assetFilters | Não |
> | mediaservices/contentKeyPolicies | Não |
> | mediaservices/eventGridFilters | Não |
> | mediaservices/liveEventOperations | Não |
> | mediaservices/liveEvents | Sim |
> | mediaservices/liveEvents/liveOutputs | Não |
> | mediaservices/liveOutputOperations | Não |
> | mediaservices/streamingEndpointOperations | Não |
> | mediaservices/streamingEndpoints | Sim |
> | mediaservices/streamingLocators | Não |
> | mediaservices/streamingPolicies | Não |
> | mediaservices/transformações | Não |
> | mediaservices/transforms/jobs | Não |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | appClusters | Sim |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | assessmentProjects | Sim |
> | migrateprojects | Sim |
> | projeto | Sim |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | remoteRenderingAccounts | Sim |
> | spatialAnchorsAccounts | Sim |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | netAppAccounts | Sim |
> | netAppAccounts/capacityPools | Sim |
> | netAppAccounts/capacityPools/volumes | Sim |
> | netAppAccounts/capacityPools/volumes/mountTargets | Sim |
> | netAppAccounts/capacityPools/volumes/instantâneos | Sim |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | applicationGateways | Sim |
> | applicationGatewayWebApplicationFirewallPolicies | Sim |
> | applicationSecurityGroups | Sim |
> | azureFirewallFqdnTags | Não |
> | azureFirewalls | Sim |
> | bastionHosts | Sim |
> | bgpServiceCommunities | Não |
> | ligações | Sim |
> | ddosCustomPolicies | Sim |
> | ddosProtectionPlans | Sim |
> | dnsOperationStatuses | Não |
> | dnszones | Sim |
> | dnszones/A | Não |
> | dnszones/AAAA | Não |
> | dnszones/tudo | Não |
> | dnszones/CAA | Não |
> | dnszones/CNAME | Não |
> | dnszones/MX | Não |
> | dnszones/NS | Não |
> | dnszones/PTR | Não |
> | dnszones/recordsets | Não |
> | dnszones/SOA | Não |
> | dnszones/SRV | Não |
> | dnszones/TXT | Não |
> | expressRouteCircuits | Sim |
> | expressRouteCrossConnections | Sim |
> | expressRouteGateways | Sim |
> | expressRoutePorts | Sim |
> | expressRouteServiceProviders | Não |
> | firewallPolicies | Sim |
> | frontdoors | Sim |
> | frontdoorWebApplicationFirewallManagedRuleSets | Não |
> | frontdoorWebApplicationFirewallPolicies | Sim |
> | getDnsResourceReference | Não |
> | internalNotify | Não |
> | loadBalancers | Sim |
> | localNetworkGateways | Sim |
> | natGateways | Sim |
> | networkIntentPolicies | Sim |
> | networkInterfaces | Sim |
> | networkProfiles | Sim |
> | networkSecurityGroups | Sim |
> | networkWatchers | Sim |
> | networkWatchers/connectionMonitors | Sim |
> | networkWatchers/lenses | Sim |
> | networkWatchers/pingMeshes | Sim |
> | p2sVpnGateways | Sim |
> | privateDnsOperationStatuses | Não |
> | privateDnsZones | Sim |
> | privateDnsZones/A | Não |
> | privateDnsZones/AAAA | Não |
> | privateDnsZones/tudo | Não |
> | privateDnsZones/CNAME | Não |
> | privateDnsZones/MX | Não |
> | privateDnsZones/PTR | Não |
> | privateDnsZones/SOA | Não |
> | privateDnsZones/SRV | Não |
> | privateDnsZones/TXT | Não |
> | privateDnsZones/virtualNetworkLinks | Sim |
> | privateEndpoints | Sim |
> | privateLinkServices | Sim |
> | publicIPAddresses | Sim |
> | publicIPPrefixes | Sim |
> | routeFilters | Sim |
> | routeTables | Sim |
> | secureGateways | Sim |
> | serviceEndpointPolicies | Sim |
> | trafficManagerGeographicHierarchies | Não |
> | trafficmanagerprofiles | Sim |
> | trafficmanagerprofiles/heatMaps | Não |
> | trafficManagerUserMetricsKeys | Não |
> | virtualHubs | Sim |
> | virtualNetworkGateways | Sim |
> | virtualNetworks | Sim |
> | virtualNetworkTaps | Sim |
> | virtualWans | Sim |
> | vpnGateways | Sim |
> | vpnSites | Sim |
> | webApplicationFirewallPolicies | Sim |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | namespaces | Sim |
> | namespaces/notificationHubs | Sim |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | HyperVSites | Sim |
> | ImportSites | Sim |
> | ServerSites | Sim |
> | VMwareSites | Sim |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | dispositivos | Não |
> | linkTargets | Não |
> | storageInsightConfigs | Não |
> | áreas de trabalho | Sim |
> | workspaces/dataSources | Não |
> | workspaces/linkedServices | Não |
> | espaços de trabalho/consulta | Não |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | managementassociations | Não |
> | managementconfigurations | Sim |
> | soluções | Sim |
> | visualizações | Sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | legacyPeerings | Não |
> | peerAsns | Não |
> | peerings | Sim |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | policyEvents | Não |
> | policyStates | Não |
> | policyTrackedResources | Não |
> | correções | Não |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | consoles | Não |
> | painéis | Sim |
> | UserSettings | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | workspaceCollections | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | unidades | Sim |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | backupProtectedItems | Não |
> | cofres | Sim |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | namespaces | Sim |
> | namespaces/authorizationrules | Não |
> | namespaces/hybridconnections | Não |
> | namespaces/hybridconnections/authorizationrules | Não |
> | namespaces/wcfrelays | Não |
> | namespaces/wcfrelays/authorizationrules | Não |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | contas | Não |
> | colec | Sim |
> | coleções/aplicativos | Não |
> | coleções/SecurityPrincipals | Não |
> | templateImages | Não |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | consultas de | Sim |
> | resourceChangeDetails | Não |
> | resourceChanges | Não |
> | recursos | Não |
> | resourcesHistory | Não |
> | subscriptionsStatus | Não |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | availabilityStatuses | Não |
> | childAvailabilityStatuses | Não |
> | childResources | Não |
> | eventos | Não |
> | impactedResources | Não |
> | do IdP | Não |
> | notificações | Não |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | implementações | Não |
> | implantações/operações | Não |
> | Vincule | Não |
> | notifyResourceJobs | Não |
> | Fornecedores | Não |
> | resourceGroups | Não |
> | recursos | Não |
> | subscrições | Não |
> | assinaturas/provedores | Não |
> | subscriptions/resourceGroups | Não |
> | subscriptions/resourcegroups/resources | Não |
> | assinaturas/recursos | Não |
> | assinaturas/TagNames | Não |
> | subscriptions/tagNames/tagValues | Não |
> | tags | Não |
> | locatários | Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicações | Sim |
> | saasresources | Não |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | fluxos | Sim |
> | gratuitas | Sim |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | resourceHealthMetadata | Não |
> | searchServices | Sim |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | Não |
> | advancedThreatProtectionSettings | Não |
> | alerts | Não |
> | allowedConnections | Não |
> | applicationWhitelistings | Não |
> | assessmentMetadata | Não |
> | Avaliações | Não |
> | AutoProvisioningSettings | Não |
> | Conformidades | Não |
> | dataCollectionAgents | Não |
> | deviceSecurityGroups | Não |
> | discoveredSecuritySolutions | Não |
> | externalSecuritySolutions | Não |
> | InformationProtectionPolicies | Não |
> | iotSecuritySolutions | Sim |
> | iotSecuritySolutions/analyticsModels | Não |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | Não |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | Não |
> | jitNetworkAccessPolicies | Não |
> | playbookConfigurations | Sim |
> | políticas | Não |
> | preços | Não |
> | regulatoryComplianceStandards | Não |
> | regulatoryComplianceStandards/regulatoryComplianceControls | Não |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | Não |
> | securityContacts | Não |
> | securitySolutions | Não |
> | securitySolutionsReferenceData | Não |
> | securityStatuses | Não |
> | securityStatusesSummaries | Não |
> | serverVulnerabilityAssessments | Não |
> | definições | Não |
> | tarefas | Não |
> | topologias | Não |
> | workspaceSettings | Não |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | diagnosticSettings | Não |
> | diagnosticSettingsCategories | Não |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | agregações | Não |
> | alertRules | Não |
> | indicadores | Não |
> | bolsas | Não |
> | dataconnecters | Não |
> | entidades | Não |
> | entityQueries | Não |
> | officeConsents | Não |
> | definições | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | namespaces | Sim |
> | namespaces/authorizationrules | Não |
> | namespaces/disasterrecoveryconfigs | Não |
> | namespaces/eventgridfilters | Não |
> | namespaces/networkrulesets | Não |
> | namespaces/queues | Não |
> | namespaces/queues/authorizationrules | Não |
> | namespaces/topics | Não |
> | namespaces/topics/authorizationrules | Não |
> | namespaces/tópicos/assinaturas | Não |
> | namespaces/tópicos/assinaturas/regras | Não |
> | premiumMessagingRegions | Não |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicações | Sim |
> | clusters | Sim |
> | clusters/aplicativos | Não |
> | containerGroups | Sim |
> | containerGroupSets | Sim |
> | edgeclusters | Sim |
> | edgeclusters/aplicativos | Não |
> | às | Sim |
> | secretstores | Sim |
> | secretstores/certificados | Não |
> | secretstores/segredos | Não |
> | volumes | Sim |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicações | Sim |
> | containerGroups | Sim |
> | gateways | Sim |
> | às | Sim |
> | segredos | Sim |
> | volumes | Sim |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | providerRegistrations | Não |
> | providerRegistrations/resourceTypeRegistrations | Não |
> | distribuições | Sim |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | SignalR | Sim |
> | Sinalização/eventGridFilters | Não |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | SiteRecoveryVault | Sim |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | hybridUseBenefits | Não |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | applicationDefinitions | Sim |
> | aplicações | Sim |
> | jitRequests | Sim |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | managedInstances | Sim |
> | managedInstances/databases | Sim |
> | managedInstances/databases/backupShortTermRetentionPolicies | Não |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | Não |
> | managedInstances/databases/vulnerabilityAssessments | Não |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | Não |
> | managedInstances/encryptionProtector | Não |
> | managedInstances/keys | Não |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Não |
> | managedInstances/vulnerabilityAssessments | Não |
> | servidores | Sim |
> | servidores/administradores | Não |
> | servers/communicationLinks | Não |
> | servers/databases | Sim |
> | servers/encryptionProtector | Não |
> | servers/firewallRules | Não |
> | servers/keys | Não |
> | servers/restorableDroppedDatabases | Não |
> | servidores/preobjetivos | Não |
> | servers/tdeCertificates | Não |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Sim |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Não |
> | SqlVirtualMachines | Sim |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | storageAccounts | Sim |
> | storageAccounts/blobServices | Não |
> | storageAccounts/fileServices | Não |
> | storageAccounts/queueServices | Não |
> | storageAccounts/services | Não |
> | storageAccounts/serviços/metricDefinitions | Não |
> | storageAccounts/tableServices | Não |
> | usos | Não |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | caches | Sim |
> | caches/storageTargets | Não |
> | usageModels | Não |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | replicationGroups | Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | Sim |
> | storageSyncServices/registeredServers | Não |
> | storageSyncServices/syncGroups | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não |
> | storageSyncServices/syncGroups/serverEndpoints | Não |
> | storageSyncServices/workflows | Não |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | Sim |
> | storageSyncServices/registeredServers | Não |
> | storageSyncServices/syncGroups | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não |
> | storageSyncServices/syncGroups/serverEndpoints | Não |
> | storageSyncServices/workflows | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | Sim |
> | storageSyncServices/registeredServers | Não |
> | storageSyncServices/syncGroups | Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não |
> | storageSyncServices/syncGroups/serverEndpoints | Não |
> | storageSyncServices/workflows | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | esses | Sim |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | streamingjobs | Sim |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | cancelar | Não |
> | CreateSubscription | Não |
> | mudar o nome | Não |
> | SubscriptionDefinitions | Não |
> | SubscriptionOperations | Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | sistemas | Sim |
> | environments/accessPolicies | Não |
> | ambientes/EventSources | Sim |
> | environments/referenceDataSets | Sim |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Sim |
> | dedicatedCloudServices | Sim |
> | virtualMachines | Sim |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | apiManagementAccounts | Não |
> | apiManagementAccounts/apiAcls | Não |
> | apiManagementAccounts/apis | Não |
> | apiManagementAccounts/apis/apiAcls | Não |
> | apiManagementAccounts/apis/connectionAcls | Não |
> | apiManagementAccounts/apis/connections | Não |
> | apiManagementAccounts/apis/connections/connectionAcls | Não |
> | apiManagementAccounts/apis/localizedDefinitions | Não |
> | apiManagementAccounts/connectionAcls | Não |
> | apiManagementAccounts/connections | Não |
> | billingMeters | Não |
> | certificados | Sim |
> | connectionGateways | Sim |
> | ligações | Sim |
> | customApis | Sim |
> | deletedSites | Não |
> | funções | Não |
> | hostingEnvironments | Sim |
> | hostingEnvironments/multiRolePools | Não |
> | hostingEnvironments/workerPools | Não |
> | publishingUsers | Não |
> | Recomendações | Não |
> | resourceHealthMetadata | Não |
> | tempos | Não |
> | serverFarms | Sim |
> | serverFarms/eventGridFilters | Não |
> | sites | Sim |
> | sites/configuração  | Não |
> | sites/eventGridFilters | Não |
> | sites/hostNameBindings | Não |
> | sites/networkConfig | Não |
> | sites/premieraddons | Sim |
> | sites/Slots | Sim |
> | sites/Slots/eventGridFilters | Não |
> | sites/slots/hostNameBindings | Não |
> | sites/Slots/networkConfig | Não |
> | sourceControls | Não |
> | verifica | Não |
> | verifyHostingEnvironmentVnet | Não |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | diagnosticSettings | Não |
> | diagnosticSettingsCategories | Não |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Dispositivos | Sim |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | QC | Não |
> | componentsSummary | Não |
> | monitorInstances | Não |
> | monitorInstancesSummary | Não |
> | monitores | Não |
> | notificationSettings | Não |

## <a name="next-steps"></a>Passos Seguintes

Para obter os mesmos dados como um arquivo de valores separados por vírgula, baixe [Complete-Mode-deletion. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
