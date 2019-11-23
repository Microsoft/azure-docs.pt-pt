---
title: Tag support for resources
description: Shows which Azure resource types support tags. Provides details for all Azure services.
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 5b3e26d914887496eedde609404eaf0b380dbcc0
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422134"
---
# <a name="tag-support-for-azure-resources"></a>Tag support for Azure resources
This article describes whether a resource type supports [tags](resource-group-using-tags.md). The column labeled **Supports tags** indicates whether the resource type has a property for the tag. The column labeled **Tag in cost report** indicates whether that resource type passes the tag to the cost report. You can view costs by tags in the [Cost Management cost analysis](../cost-management/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) and the [Azure billing invoice and daily usage data](../billing/billing-download-azure-invoice-daily-usage-date.md).

To get the same data as a file of comma-separated values, download [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Jump to a resource provider namespace:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.Azconfig](#microsoftazconfig)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
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
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DomainServices | Sim | Sim |
> | DomainServices / oucontainer | Não | Não |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | supportProviders | Não | Não |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Não | Não |
> | addsservices | Não | Não |
> | agents | Não | Não |
> | anonymousapiusers | Não | Não |
> | configuração | Não | Não |
> | registos | Não | Não |
> | reports | Não | Não |
> | servicehealthmetrics | Não | Não |
> | services | Não | Não |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurations | Não | Não |
> | generateRecommendations | Não | Não |
> | do IdP | Não | Não |
> | recommendations | Não | Não |
> | suppressions | Não | Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | actionRules | Sim | Sim |
> | alerts | Não | Não |
> | alertsList | Não | Não |
> | alertsMetaData | Não | Não |
> | alertsSummary | Não | Não |
> | alertsSummaryList | Não | Não |
> | feedback | Não | Não |
> | smartDetectorAlertRules | Sim | Sim |
> | smartDetectorRuntimeEnvironments | Não | Não |
> | smartGroups | Não | Não |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | servidores | Sim | Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Não | Não |
> | serviço | Sim | Sim |
> | validateServiceName | Não | Não |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationStores | Sim | Sim |
> | configurationStores / eventGridFilters | Não | Não |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Spring | Sim | Sim |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Não | Não |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | Não | Não |
> | dataAliases | Não | Não |
> | denyAssignments | Não | Não |
> | elevateAccess | Não | Não |
> | findOrphanRoleAssignments | Não | Não |
> | locks | Não | Não |
> | permissions | Não | Não |
> | policyAssignments | Não | Não |
> | policyDefinitions | Não | Não |
> | policySetDefinitions | Não | Não |
> | providerOperations | Não | Não |
> | roleAssignments | Não | Não |
> | roleDefinitions | Não | Não |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Sim | Sim |
> | automationAccounts / configurations | Sim | Sim |
> | automationAccounts / jobs | Não | Não |
> | automationAccounts / runbooks | Sim | Sim |
> | automationAccounts / softwareUpdateConfigurations | Não | Não |
> | automationAccounts / webhooks | Não | Não |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationStores | Sim | Sim |
> | configurationStores / eventGridFilters | Não | Não |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | environments | Não | Não |
> | environments / accounts | Não | Não |
> | environments / accounts / namespaces | Não | Não |
> | environments / accounts / namespaces / configurations | Não | Não |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Sim | Não |
> | b2ctenants | Não | Não |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | Sim | Sim |
> | postgresInstances | Sim | Sim |
> | sqlBigDataClusters | Sim | Sim |
> | sqlInstances | Sim | Sim |
> | sqlServerRegistrations | Sim | Sim |
> | sqlServerRegistrations / sqlServers | Não | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | registrations | Sim | Sim |
> | registrations / customerSubscriptions | Não | Não |
> | registrations / products | Não | Não |
> | verificationKeys | Não | Não |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Sim | Sim |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Não | Não |
> | billingAccounts / agreements | Não | Não |
> | billingAccounts / billingPermissions | Não | Não |
> | billingAccounts / billingProfiles | Não | Não |
> | billingAccounts / billingProfiles / billingPermissions | Não | Não |
> | billingAccounts / billingProfiles / billingRoleAssignments | Não | Não |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Não | Não |
> | billingAccounts / billingProfiles / billingSubscriptions | Não | Não |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Não | Não |
> | billingAccounts / billingProfiles / customers | Não | Não |
> | billingAccounts / billingProfiles / invoices | Não | Não |
> | billingAccounts / billingProfiles / invoices / pricesheet | Não | Não |
> | billingAccounts / billingProfiles / invoiceSections | Não | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Não | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Não | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Não | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Não | Não |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Não | Não |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Não | Não |
> | billingAccounts / billingProfiles / invoiceSections / products | Não | Não |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | Não | Não |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | Não | Não |
> | billingAccounts / billingProfiles / invoiceSections / transactions | Não | Não |
> | billingAccounts / billingProfiles / invoiceSections / transfers | Não | Não |
> | billingAccounts / BillingProfiles / patchOperations | Não | Não |
> | billingAccounts / billingProfiles / paymentMethods | Não | Não |
> | billingAccounts / billingProfiles / policies | Não | Não |
> | billingAccounts / billingProfiles / pricesheet | Não | Não |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Não | Não |
> | billingAccounts / billingProfiles / products | Não | Não |
> | billingAccounts / billingProfiles / transactions | Não | Não |
> | billingAccounts / billingRoleAssignments | Não | Não |
> | billingAccounts / billingRoleDefinitions | Não | Não |
> | billingAccounts / billingSubscriptions | Não | Não |
> | billingAccounts / billingSubscriptions / invoices | Não | Não |
> | billingAccounts / createBillingRoleAssignment | Não | Não |
> | billingAccounts / createInvoiceSectionOperations | Não | Não |
> | billingAccounts / customers | Não | Não |
> | billingAccounts / customers / billingPermissions | Não | Não |
> | billingAccounts / customers / billingSubscriptions | Não | Não |
> | billingAccounts / customers / initiateTransfer | Não | Não |
> | billingAccounts / customers / policies | Não | Não |
> | billingAccounts / customers / products | Não | Não |
> | billingAccounts / customers / transactions | Não | Não |
> | billingAccounts / customers / transfers | Não | Não |
> | billingAccounts / departments | Não | Não |
> | billingAccounts / enrollmentAccounts | Não | Não |
> | billingAccounts / invoices | Não | Não |
> | billingAccounts / invoiceSections | Não | Não |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Não | Não |
> | billingAccounts / invoiceSections / billingSubscriptions | Não | Não |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | Não | Não |
> | billingAccounts / invoiceSections / elevate | Não | Não |
> | billingAccounts / invoiceSections / initiateTransfer | Não | Não |
> | billingAccounts / invoiceSections / patchOperations | Não | Não |
> | billingAccounts / invoiceSections / productMoveOperations | Não | Não |
> | billingAccounts / invoiceSections / products | Não | Não |
> | billingAccounts / invoiceSections / products / transfer | Não | Não |
> | billingAccounts / invoiceSections / products / updateAutoRenew | Não | Não |
> | billingAccounts / invoiceSections / transactions | Não | Não |
> | billingAccounts / invoiceSections / transfers | Não | Não |
> | billingAccounts / lineOfCredit | Não | Não |
> | billingAccounts / patchOperations | Não | Não |
> | billingAccounts / paymentMethods | Não | Não |
> | billingAccounts / products | Não | Não |
> | billingAccounts / transactions | Não | Não |
> | billingPeriods | Não | Não |
> | billingPermissions | Não | Não |
> | billingProperty | Não | Não |
> | billingRoleAssignments | Não | Não |
> | billingRoleDefinitions | Não | Não |
> | createBillingRoleAssignment | Não | Não |
> | departments | Não | Não |
> | enrollmentAccounts | Não | Não |
> | invoices | Não | Não |
> | transfers | Não | Não |
> | transfers / acceptTransfer | Não | Não |
> | transfers / declineTransfer | Não | Não |
> | transfers / operationStatus | Não | Não |
> | transfers / validateTransfer | Não | Não |
> | validateAddress | Não | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | mapApis | Sim | Sim |
> | updateCommunicationPreference | Não | Não |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Sim | Sim |
> | cordaMembers | Sim | Sim |
> | observadores | Sim | Sim |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Não | Não |
> | blueprintAssignments / assignmentOperations | Não | Não |
> | blueprintAssignments / operations | Não | Não |
> | blueprints | Não | Não |
> | blueprints / artifacts | Não | Não |
> | blueprints / versions | Não | Não |
> | blueprints / versions / artifacts | Não | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | botServices | Sim | Sim |
> | botServices / channels | Não | Não |
> | botServices / connections | Não | Não |
> | languages | Não | Não |
> | modelos | Não | Não |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Redis | Sim | Sim |
> | RedisConfigDefinition | Não | Não |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Não | Não |
> | calculateExchange | Não | Não |
> | calculatePrice | Não | Não |
> | calculatePurchasePrice | Não | Não |
> | catalogs | Não | Não |
> | commercialReservationOrders | Não | Não |
> | exchange | Não | Não |
> | placePurchaseOrder | Não | Não |
> | reservationOrders | Não | Não |
> | reservationOrders / calculateRefund | Não | Não |
> | reservationOrders / merge | Não | Não |
> | reservationOrders / reservations | Não | Não |
> | reservationOrders / reservations / revisions | Não | Não |
> | reservationOrders / return | Não | Não |
> | reservationOrders / split | Não | Não |
> | reservationOrders / swap | Não | Não |
> | reservations | Não | Não |
> | resources | Não | Não |
> | validateReservationOrder | Não | Não |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Não | Não |
> | CdnWebApplicationFirewallPolicies | Sim | Sim |
> | edgenodes | Não | Não |
> | profiles | Sim | Sim |
> | profiles / endpoints | Sim | Sim |
> | profiles / endpoints / customdomains | Não | Não |
> | profiles / endpoints / origins | Não | Não |
> | validateProbe | Não | Não |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Sim | Sim |
> | certificateOrders / certificates | Não | Não |
> | validateCertificateRegistrationInformation | Não | Não |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | Não | Não |
> | domainNames | Não | Não |
> | domainNames / capabilities | Não | Não |
> | domainNames / internalLoadBalancers | Não | Não |
> | domainNames / serviceCertificates | Não | Não |
> | domainNames / slots | Não | Não |
> | domainNames / slots / roles | Não | Não |
> | domainNames / slots / roles / metricDefinitions | Não | Não |
> | domainNames / slots / roles / metrics | Não | Não |
> | moveSubscriptionResources | Não | Não |
> | operatingSystemFamilies | Não | Não |
> | operatingSystems | Não | Não |
> | quotas | Não | Não |
> | resourceTypes | Não | Não |
> | validateSubscriptionMoveAvailability | Não | Não |
> | virtualMachines | Não | Não |
> | virtualMachines / diagnosticSettings | Não | Não |
> | virtualMachines / metricDefinitions | Não | Não |
> | virtualMachines / metrics | Não | Não |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Não | Não |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | Não | Não |
> | expressRouteCrossConnections | Não | Não |
> | expressRouteCrossConnections / peerings | Não | Não |
> | gatewaySupportedDevices | Não | Não |
> | networkSecurityGroups | Não | Não |
> | quotas | Não | Não |
> | reservedIps | Não | Não |
> | virtualNetworks | Não | Não |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Não | Não |
> | virtualNetworks / virtualNetworkPeerings | Não | Não |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | Não | Não |
> | disks | Não | Não |
> | imagens | Não | Não |
> | osImages | Não | Não |
> | osPlatformImages | Não | Não |
> | publicImages | Não | Não |
> | quotas | Não | Não |
> | storageAccounts | Não | Não |
> | storageAccounts / blobServices | Não | Não |
> | storageAccounts / fileServices | Não | Não |
> | storageAccounts / metricDefinitions | Não | Não |
> | storageAccounts / metrics | Não | Não |
> | storageAccounts / queueServices | Não | Não |
> | storageAccounts / services | Não | Não |
> | storageAccounts / services / diagnosticSettings | Não | Não |
> | storageAccounts / services / metricDefinitions | Não | Não |
> | storageAccounts / services / metrics | Não | Não |
> | storageAccounts / tableServices | Não | Não |
> | storageAccounts / vmImages | Não | Não |
> | vmImages | Não | Não |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | RateCard | Não | Não |
> | UsageAggregates | Não | Não |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Sim | Sim |
> | diskEncryptionSets | Sim | Sim |
> | disks | Sim | Sim |
> | galleries | Sim | Sim |
> | galleries / applications | Não | Não |
> | galleries / applications / versions | Não | Não |
> | galleries / images | Não | Não |
> | galleries / images / versions | Não | Não |
> | hostGroups | Sim | Sim |
> | hostGroups / hosts | Sim | Sim |
> | imagens | Sim | Sim |
> | proximityPlacementGroups | Sim | Sim |
> | restorePointCollections | Sim | Sim |
> | restorePointCollections / restorePoints | Não | Não |
> | sharedVMImages | Sim | Sim |
> | sharedVMImages / versions | Não | Não |
> | instantâneos | Sim | Sim |
> | virtualMachines | Sim | Sim |
> | virtualMachines / extensions | Sim | Sim |
> | virtualMachines / metricDefinitions | Não | Não |
> | virtualMachineScaleSets | Sim | Sim |
> | virtualMachineScaleSets / extensions | Não | Não |
> | virtualMachineScaleSets / networkInterfaces | Não | Não |
> | virtualMachineScaleSets / publicIPAddresses | Não | Não |
> | virtualMachineScaleSets / virtualMachines | Não | Não |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Não | Não |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Não | Não |
> | Saldos | Não | Não |
> | Orçamentos | Não | Não |
> | Cobranças | Não | Não |
> | CostTags | Não | Não |
> | credits | Não | Não |
> | eventos | Não | Não |
> | Previsões | Não | Não |
> | lots | Não | Não |
> | Marketplaces | Não | Não |
> | Pricesheets | Não | Não |
> | products | Não | Não |
> | ReservationDetails | Não | Não |
> | ReservationRecommendations | Não | Não |
> | ReservationSummaries | Não | Não |
> | ReservationTransactions | Não | Não |
> | Etiquetas | Não | Não |
> | tenants | Não | Não |
> | Termos | Não | Não |
> | UsageDetails | Não | Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | containerGroups | Sim | Sim |
> | serviceAssociationLinks | Não | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | registries | Sim | Sim |
> | registries / builds | Não | Não |
> | registries / builds / cancel | Não | Não |
> | registries / builds / getLogLink | Não | Não |
> | registries / buildTasks | Sim | Sim |
> | registries / buildTasks / steps | Não | Não |
> | registries / eventGridFilters | Não | Não |
> | registries / generateCredentials | Não | Não |
> | registries / getBuildSourceUploadUrl | Não | Não |
> | registries / GetCredentials | Não | Não |
> | registries / importImage | Não | Não |
> | registries / queueBuild | Não | Não |
> | registries / regenerateCredential | Não | Não |
> | registries / regenerateCredentials | Não | Não |
> | registries / replications | Sim | Sim |
> | registries / runs | Não | Não |
> | registries / runs / cancel | Não | Não |
> | registries / scheduleRun | Não | Não |
> | registries / scopeMaps | Não | Não |
> | registries / taskRuns | Sim | Sim |
> | registries / tasks | Sim | Sim |
> | registries / tokens | Não | Não |
> | registries / updatePolicies | Não | Não |
> | registries / webhooks | Sim | Sim |
> | registries / webhooks / getCallbackConfig | Não | Não |
> | registries / webhooks / ping | Não | Não |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | containerServices | Sim | Sim |
> | managedClusters | Sim | Sim |
> | openShiftManagedClusters | Sim | Sim |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Alertas | Não | Não |
> | BillingAccounts | Não | Não |
> | Orçamentos | Não | Não |
> | CloudConnectors | Não | Não |
> | Conectores | Sim | Sim |
> | Departments | Não | Não |
> | Dimensões | Não | Não |
> | EnrollmentAccounts | Não | Não |
> | Exportações | Não | Não |
> | ExternalBillingAccounts | Não | Não |
> | ExternalBillingAccounts / Alerts | Não | Não |
> | ExternalBillingAccounts / Dimensions | Não | Não |
> | ExternalBillingAccounts / Forecast | Não | Não |
> | ExternalBillingAccounts / Query | Não | Não |
> | ExternalSubscriptions | Não | Não |
> | ExternalSubscriptions / Alerts | Não | Não |
> | ExternalSubscriptions / Dimensions | Não | Não |
> | ExternalSubscriptions / Forecast | Não | Não |
> | ExternalSubscriptions / Query | Não | Não |
> | Forecast | Não | Não |
> | Consulta | Não | Não |
> | register | Não | Não |
> | Reportconfigs | Não | Não |
> | Relatórios | Não | Não |
> | Definições | Não | Não |
> | showbackRules | Não | Não |
> | Vistas | Não | Não |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | requests | Não | Não |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | associations | Não | Não |
> | resourceProviders | Sim | Sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobs | Sim | Sim |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Sim | Sim |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaces | Sim | Não |
> | workspaces / virtualNetworkPeerings | Não | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | catalogs | Sim | Sim |
> | datacatalogs | Sim | Sim |
> | datacatalogs / datasources | Não | Não |
> | datacatalogs / datasources / scans | Não | Não |
> | datacatalogs / datasources / scans / datasets | Não | Não |
> | datacatalogs / datasources / scans / triggers | Não | Não |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dataFactories | Sim | Não |
> | dataFactories / diagnosticSettings | Não | Não |
> | dataFactories / metricDefinitions | Não | Não |
> | dataFactorySchema | Não | Não |
> | factories | Sim | Não |
> | factories / integrationRuntimes | Não | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | accounts / dataLakeStoreAccounts | Não | Não |
> | accounts / storageAccounts | Não | Não |
> | accounts / storageAccounts / containers | Não | Não |
> | accounts / transferAnalyticsUnits | Não | Não |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | accounts / eventGridFilters | Não | Não |
> | accounts / firewallRules | Não | Não |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | services | Não | Não |
> | services / projects | Não | Não |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | accounts / shares | Não | Não |
> | accounts / shares / datasets | Não | Não |
> | accounts / shares / invitations | Não | Não |
> | accounts / shares / providersharesubscriptions | Não | Não |
> | accounts / shares / synchronizationSettings | Não | Não |
> | accounts / sharesubscriptions | Não | Não |
> | accounts / sharesubscriptions / consumerSourceDataSets | Não | Não |
> | accounts / sharesubscriptions / datasetmappings | Não | Não |
> | accounts / sharesubscriptions / triggers | Não | Não |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | servidores | Sim | Sim |
> | servers / advisors | Não | Não |
> | servers / privateEndpointConnectionProxies | Não | Não |
> | servers / privateEndpointConnections | Não | Não |
> | servers / privateLinkResources | Não | Não |
> | servers / queryTexts | Não | Não |
> | servers / recoverableServers | Não | Não |
> | servers / topQueryStatistics | Não | Não |
> | servers / virtualNetworkRules | Não | Não |
> | servers / waitStatistics | Não | Não |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | servidores | Sim | Sim |
> | servers / advisors | Não | Não |
> | servers / privateEndpointConnectionProxies | Não | Não |
> | servers / privateEndpointConnections | Não | Não |
> | servers / privateLinkResources | Não | Não |
> | servers / queryTexts | Não | Não |
> | servers / recoverableServers | Não | Não |
> | servers / topQueryStatistics | Não | Não |
> | servers / virtualNetworkRules | Não | Não |
> | servers / waitStatistics | Não | Não |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | serverGroups | Sim | Sim |
> | servidores | Sim | Sim |
> | servers / advisors | Não | Não |
> | servers / keys | Não | Não |
> | servers / privateEndpointConnectionProxies | Não | Não |
> | servers / privateEndpointConnections | Não | Não |
> | servers / privateLinkResources | Não | Não |
> | servers / queryTexts | Não | Não |
> | servers / recoverableServers | Não | Não |
> | servers / topQueryStatistics | Não | Não |
> | servers / virtualNetworkRules | Não | Não |
> | servers / waitStatistics | Não | Não |
> | serversv2 | Sim | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | artifactSources | Sim | Sim |
> | rollouts | Sim | Sim |
> | serviceTopologies | Sim | Sim |
> | serviceTopologies / services | Sim | Sim |
> | serviceTopologies / services / serviceUnits | Sim | Sim |
> | passos | Sim | Sim |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Sim | Sim |
> | applicationgroups / applications | Não | Não |
> | applicationgroups / desktops | Não | Não |
> | applicationgroups / startmenuitems | Não | Não |
> | hostpools | Sim | Sim |
> | hostpools / sessionhosts | Não | Não |
> | hostpools / sessionhosts / usersessions | Não | Não |
> | hostpools / usersessions | Não | Não |
> | workspaces | Sim | Sim |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Sim | Sim |
> | ElasticPools / IotHubTenants | Sim | Sim |
> | IotHubs | Sim | Sim |
> | IotHubs / eventGridFilters | Não | Não |
> | ProvisioningServices | Sim | Sim |
> | usages | Não | Não |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | pipelines | Sim | Sim |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | controllers | Sim | Sim |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | labcenters | Sim | Sim |
> | labs | Sim | Sim |
> | labs / environments | Sim | Sim |
> | labs / serviceRunners | Sim | Sim |
> | labs / virtualMachines | Sim | Sim |
> | schedules | Sim | Sim |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Não | Não |
> | databaseAccounts | Sim | Sim |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | domains | Sim | Sim |
> | domains / domainOwnershipIdentifiers | Não | Não |
> | generateSsoRequest | Não | Não |
> | topLevelDomains | Não | Não |
> | validateDomainRegistrationInformation | Não | Não |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Não | Não |
> | lcsprojects / clouddeployments | Não | Não |
> | lcsprojects / connectors | Não | Não |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | services | Sim | Sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | domains | Sim | Sim |
> | domains / topics | Não | Não |
> | eventSubscriptions | Não | Não |
> | extensionTopics | Não | Não |
> | topics | Sim | Sim |
> | topicTypes | Não | Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Sim | Sim |
> | namespaces | Sim | Sim |
> | namespaces / authorizationrules | Não | Não |
> | namespaces / disasterrecoveryconfigs | Não | Não |
> | namespaces / eventhubs | Não | Não |
> | namespaces / eventhubs / authorizationrules | Não | Não |
> | namespaces / eventhubs / consumergroups | Não | Não |
> | namespaces / networkrulesets | Não | Não |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | elástica | Não | Não |
> | providers | Não | Não |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | enroll | Não | Não |
> | galleryitems | Não | Não |
> | generateartifactaccessuri | Não | Não |
> | myareas | Não | Não |
> | myareas / areas | Não | Não |
> | myareas / areas / areas | Não | Não |
> | myareas / areas / areas / galleryitems | Não | Não |
> | myareas / areas / galleryitems | Não | Não |
> | myareas / galleryitems | Não | Não |
> | register | Não | Não |
> | resources | Não | Não |
> | retrieveresourcesbyid | Não | Não |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationProfileAssignments | Não | Não |
> | guestConfigurationAssignments | Não | Não |
> | software | Não | Não |
> | softwareUpdateProfile | Não | Não |
> | softwareUpdates | Não | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Sim | Sim |
> | sapMonitors | Sim | Sim |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | Sim | Sim |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Sim | Sim |
> | clusters / applications | Não | Não |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | services | Sim | Sim |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | machines | Sim | Sim |
> | machines / extensions | Sim | Sim |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dataManagers | Sim | Sim |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | components | Sim | Sim |
> | networkScopes | Sim | Sim |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobs | Sim | Sim |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Não | Não |
> | diagnosticSettingsCategories | Não | Não |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appTemplates | Não | Não |
> | IoTApps | Sim | Sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Graph | Sim | Sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Não | Não |
> | hsmPools | Sim | Sim |
> | vaults | Sim | Sim |
> | vaults / accessPolicies | Não | Não |
> | vaults / eventGridFilters | Não | Não |
> | vaults / secrets | Não | Não |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Sim | Sim |
> | clusters / attacheddatabaseconfigurations | Não | Não |
> | clusters / databases | Não | Não |
> | clusters / databases / dataconnections | Não | Não |
> | clusters / databases / eventhubconnections | Não | Não |
> | clusters / sharedidentities | Não | Não |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | labaccounts | Sim | Sim |
> | utilizadores | Não | Não |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Sim | Sim |
> | integrationAccounts | Sim | Sim |
> | integrationServiceEnvironments | Sim | Sim |
> | integrationServiceEnvironments / managedApis | Sim | Sim |
> | isolatedEnvironments | Sim | Sim |
> | workflows | Sim | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Sim | Sim |
> | webServices | Sim | Sim |
> | Áreas de trabalho | Sim | Sim |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaces | Sim | Sim |
> | workspaces / computes | Não | Não |
> | workspaces / eventGridFilters | Não | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Identities | Não | Não |
> | userAssignedIdentities | Sim | Sim |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Não | Não |
> | registrationAssignments | Não | Não |
> | registrationDefinitions | Não | Não |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | getEntities | Não | Não |
> | managementGroups | Não | Não |
> | resources | Não | Não |
> | startTenantBackfill | Não | Não |
> | tenantBackfillStatus | Não | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | accounts / eventGridFilters | Não | Não |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | offers | Não | Não |
> | offerTypes | Não | Não |
> | offerTypes / publishers | Não | Não |
> | offerTypes / publishers / offers | Não | Não |
> | offerTypes / publishers / offers / plans | Não | Não |
> | offerTypes / publishers / offers / plans / agreements | Não | Não |
> | offerTypes / publishers / offers / plans / configs | Não | Não |
> | offerTypes / publishers / offers / plans / configs / importImage | Não | Não |
> | privategalleryitems | Não | Não |
> | products | Não | Não |
> | publishers | Não | Não |
> | publishers / offers | Não | Não |
> | publishers / offers / amendments | Não | Não |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Sim | Sim |
> | updateCommunicationPreference | Não | Não |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | agreements | Não | Não |
> | offertypes | Não | Não |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | mediaservices | Sim | Sim |
> | mediaservices / accountFilters | Não | Não |
> | mediaservices / assets | Não | Não |
> | mediaservices / assets / assetFilters | Não | Não |
> | mediaservices / contentKeyPolicies | Não | Não |
> | mediaservices / eventGridFilters | Não | Não |
> | mediaservices / liveEventOperations | Não | Não |
> | mediaservices / liveEvents | Sim | Sim |
> | mediaservices / liveEvents / liveOutputs | Não | Não |
> | mediaservices / liveOutputOperations | Não | Não |
> | mediaservices / mediaGraphs | Não | Não |
> | mediaservices / streamingEndpointOperations | Não | Não |
> | mediaservices / streamingEndpoints | Sim | Sim |
> | mediaservices / streamingLocators | Não | Não |
> | mediaservices / streamingPolicies | Não | Não |
> | mediaservices / transforms | Não | Não |
> | mediaservices / transforms / jobs | Não | Não |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appClusters | Sim | Sim |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Sim | Sim |
> | migrateprojects | Sim | Sim |
> | projects | Sim | Sim |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Sim | Sim |
> | objectUnderstandingAccounts | Sim | Sim |
> | remoteRenderingAccounts | Sim | Sim |
> | spatialAnchorsAccounts | Sim | Sim |
> | surfaceReconstructionAccounts | Sim | Sim |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Sim | Não |
> | netAppAccounts / capacityPools | Sim | Não |
> | netAppAccounts / capacityPools / volumes | Sim | Não |
> | netAppAccounts / capacityPools / volumes / mountTargets | Sim | Não |
> | netAppAccounts / capacityPools / volumes / snapshots | Sim | Não |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Sim | Sim |
> | applicationGatewayWebApplicationFirewallPolicies | Sim | Sim |
> | applicationSecurityGroups | Sim | Sim |
> | azureFirewallFqdnTags | Não | Não |
> | azureFirewalls | Sim | Não |
> | bastionHosts | Sim | Sim |
> | bgpServiceCommunities | Não | Não |
> | connections | Sim | Sim |
> | ddosCustomPolicies | Sim | Sim |
> | ddosProtectionPlans | Sim | Sim |
> | dnsOperationStatuses | Não | Não |
> | dnszones | Sim | Sim |
> | dnszones / A | Não | Não |
> | dnszones / AAAA | Não | Não |
> | dnszones / all | Não | Não |
> | dnszones / CAA | Não | Não |
> | dnszones / CNAME | Não | Não |
> | dnszones / MX | Não | Não |
> | dnszones / NS | Não | Não |
> | dnszones / PTR | Não | Não |
> | dnszones / recordsets | Não | Não |
> | dnszones / SOA | Não | Não |
> | dnszones / SRV | Não | Não |
> | dnszones / TXT | Não | Não |
> | expressRouteCircuits | Sim | Sim |
> | expressRouteCrossConnections | Sim | Sim |
> | expressRouteGateways | Sim | Sim |
> | expressRoutePorts | Sim | Sim |
> | expressRouteServiceProviders | Não | Não |
> | firewallPolicies | Sim | Sim |
> | frontdoors | Yes, but limited (see [note below](#frontdoor)) | Sim |
> | frontdoorWebApplicationFirewallManagedRuleSets | Yes, but limited (see [note below](#frontdoor)) | Não |
> | frontdoorWebApplicationFirewallPolicies | Yes, but limited (see [note below](#frontdoor)) | Sim |
> | getDnsResourceReference | Não | Não |
> | internalNotify | Não | Não |
> | loadBalancers | Sim | Não |
> | localNetworkGateways | Sim | Sim |
> | natGateways | Sim | Sim |
> | networkIntentPolicies | Sim | Sim |
> | networkInterfaces | Sim | Sim |
> | networkProfiles | Sim | Sim |
> | networkSecurityGroups | Sim | Sim |
> | networkWatchers | Sim | Não |
> | networkWatchers / connectionMonitors | Sim | Não |
> | networkWatchers / lenses | Sim | Não |
> | networkWatchers / pingMeshes | Sim | Não |
> | p2sVpnGateways | Sim | Sim |
> | privateDnsOperationStatuses | Não | Não |
> | privateDnsZones | Sim | Sim |
> | privateDnsZones / A | Não | Não |
> | privateDnsZones / AAAA | Não | Não |
> | privateDnsZones / all | Não | Não |
> | privateDnsZones / CNAME | Não | Não |
> | privateDnsZones / MX | Não | Não |
> | privateDnsZones / PTR | Não | Não |
> | privateDnsZones / SOA | Não | Não |
> | privateDnsZones / SRV | Não | Não |
> | privateDnsZones / TXT | Não | Não |
> | privateDnsZones / virtualNetworkLinks | Sim | Sim |
> | privateEndpoints | Sim | Sim |
> | privateLinkServices | Sim | Sim |
> | publicIPAddresses | Sim | Sim |
> | publicIPPrefixes | Sim | Sim |
> | routeFilters | Sim | Sim |
> | routeTables | Sim | Sim |
> | serviceEndpointPolicies | Sim | Sim |
> | trafficManagerGeographicHierarchies | Não | Não |
> | trafficmanagerprofiles | Sim | Sim |
> | trafficmanagerprofiles/heatMaps | Não | Não |
> | trafficManagerUserMetricsKeys | Não | Não |
> | virtualHubs | Sim | Sim |
> | virtualNetworkGateways | Sim | Sim |
> | virtualNetworks | Sim | Sim |
> | virtualNetworkTaps | Sim | Sim |
> | virtualWans | Sim | Sim |
> | vpnGateways | Sim | Não |
> | vpnSites | Sim | Sim |
> | webApplicationFirewallPolicies | Sim | Sim |

<a id="frontdoor" />

> [!NOTE]
> For Azure Front Door Service, you can apply tags when creating the resource, but updating or adding tags is not currently supported.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | Sim | Não |
> | namespaces / notificationHubs | Sim | Não |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Sim | Sim |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Sim | Sim |
> | ImportSites | Sim | Sim |
> | ServerSites | Sim | Sim |
> | VMwareSites | Sim | Sim |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Sim | Sim |
> | dispositivos | Não | Não |
> | linkTargets | Não | Não |
> | storageInsightConfigs | Não | Não |
> | workspaces | Sim | Sim |
> | workspaces / dataSources | Não | Não |
> | workspaces / linkedServices | Não | Não |
> | workspaces / query | Não | Não |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managementassociations | Não | Não |
> | managementconfigurations | Sim | Sim |
> | soluções | Sim | Sim |
> | views | Sim | Sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Não | Não |
> | peerAsns | Não | Não |
> | peerings | Sim | Sim |
> | peeringServiceProviders | Não | Não |
> | peeringServices | Sim | Sim |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | policyEvents | Não | Não |
> | policyMetadata | Não | Não |
> | policyStates | Não | Não |
> | policyTrackedResources | Não | Não |
> | remediations | Não | Não |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | consoles | Não | Não |
> | dashboards | Sim | Sim |
> | userSettings | Não | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | Sim | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capacities | Sim | Sim |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Não | Não |
> | vaults | Sim | Sim |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | Sim | Sim |
> | namespaces / authorizationrules | Não | Não |
> | namespaces / hybridconnections | Não | Não |
> | namespaces / hybridconnections / authorizationrules | Não | Não |
> | namespaces / wcfrelays | Não | Não |
> | namespaces / wcfrelays / authorizationrules | Não | Não |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Não | Não |
> | collections | Sim | Sim |
> | collections / applications | Não | Não |
> | collections / securityprincipals | Não | Não |
> | templateImages | Não | Não |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | consultas de | Sim | Sim |
> | resourceChangeDetails | Não | Não |
> | resourceChanges | Não | Não |
> | resources | Não | Não |
> | resourcesHistory | Não | Não |
> | subscriptionsStatus | Não | Não |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Não | Não |
> | childAvailabilityStatuses | Não | Não |
> | childResources | Não | Não |
> | eventos | Não | Não |
> | impactedResources | Não | Não |
> | do IdP | Não | Não |
> | notifications | Não | Não |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | deployments | Sim | Não |
> | deployments / operations | Não | Não |
> | deploymentScripts | Sim | Sim |
> | deploymentScripts / logs | Não | Não |
> | links | Não | Não |
> | notifyResourceJobs | Não | Não |
> | providers | Não | Não |
> | resourceGroups | Sim | Não |
> | subscriptions | Não | Não |
> | tenants | Não | Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aplicações | Sim | Sim |
> | saasresources | Não | Não |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobcollections | Sim | Sim |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Não | Não |
> | searchServices | Sim | Sim |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Não | Não |
> | advancedThreatProtectionSettings | Não | Não |
> | alerts | Não | Não |
> | allowedConnections | Não | Não |
> | applicationWhitelistings | Não | Não |
> | assessmentMetadata | Não | Não |
> | assessments | Não | Não |
> | autoDismissAlertsRules | Não | Não |
> | automations | Sim | Sim |
> | AutoProvisioningSettings | Não | Não |
> | Compliances | Não | Não |
> | dataCollectionAgents | Não | Não |
> | deviceSecurityGroups | Não | Não |
> | discoveredSecuritySolutions | Não | Não |
> | externalSecuritySolutions | Não | Não |
> | InformationProtectionPolicies | Não | Não |
> | iotSecuritySolutions | Sim | Sim |
> | iotSecuritySolutions / analyticsModels | Não | Não |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Não | Não |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Não | Não |
> | jitNetworkAccessPolicies | Não | Não |
> | networkData | Não | Não |
> | policies | Não | Não |
> | pricings | Não | Não |
> | regulatoryComplianceStandards | Não | Não |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Não | Não |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Não | Não |
> | securityContacts | Não | Não |
> | securitySolutions | Não | Não |
> | securitySolutionsReferenceData | Não | Não |
> | securityStatuses | Não | Não |
> | securityStatusesSummaries | Não | Não |
> | serverVulnerabilityAssessments | Não | Não |
> | settings | Não | Não |
> | subAssessments | Não | Não |
> | tarefas | Não | Não |
> | topologies | Não | Não |
> | workspaceSettings | Não | Não |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Não | Não |
> | diagnosticSettingsCategories | Não | Não |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aggregations | Não | Não |
> | alertRules | Não | Não |
> | alertRuleTemplates | Não | Não |
> | bookmarks | Não | Não |
> | cases | Não | Não |
> | dataConnectors | Não | Não |
> | entities | Não | Não |
> | entityQueries | Não | Não |
> | officeConsents | Não | Não |
> | settings | Não | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | Sim | Não |
> | namespaces / authorizationrules | Não | Não |
> | namespaces / disasterrecoveryconfigs | Não | Não |
> | namespaces / eventgridfilters | Não | Não |
> | namespaces / networkrulesets | Não | Não |
> | namespaces / queues | Não | Não |
> | namespaces / queues / authorizationrules | Não | Não |
> | namespaces / topics | Não | Não |
> | namespaces / topics / authorizationrules | Não | Não |
> | namespaces / topics / subscriptions | Não | Não |
> | namespaces / topics / subscriptions / rules | Não | Não |
> | premiumMessagingRegions | Não | Não |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aplicações | Sim | Sim |
> | clusters | Sim | Sim |
> | clusters / applications | Não | Não |
> | containerGroups | Sim | Sim |
> | containerGroupSets | Sim | Sim |
> | edgeclusters | Sim | Sim |
> | edgeclusters / applications | Não | Não |
> | networks | Sim | Sim |
> | secretstores | Sim | Sim |
> | secretstores / certificates | Não | Não |
> | secretstores / secrets | Não | Não |
> | volumes | Sim | Sim |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aplicações | Sim | Sim |
> | containerGroups | Sim | Sim |
> | gateways | Sim | Sim |
> | networks | Sim | Sim |
> | secrets | Sim | Sim |
> | volumes | Sim | Sim |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Não | Não |
> | providerRegistrations / resourceTypeRegistrations | Não | Não |
> | rollouts | Sim | Sim |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SignalR | Sim | Sim |
> | SignalR / eventGridFilters | Não | Não |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Sim | Sim |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Não | Não |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Sim | Sim |
> | aplicações | Sim | Sim |
> | jitRequests | Sim | Sim |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managedInstances | Sim | Sim |
> | managedInstances / databases | Não | Não |
> | managedInstances / databases / backupShortTermRetentionPolicies | Não | Não |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | Não | Não |
> | managedInstances / databases / vulnerabilityAssessments | Não | Não |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | Não | Não |
> | managedInstances / encryptionProtector | Não | Não |
> | managedInstances / keys | Não | Não |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Não | Não |
> | managedInstances / vulnerabilityAssessments | Não | Não |
> | servidores | Sim | Sim |
> | servers / administrators | Não | Não |
> | servers / communicationLinks | Não | Não |
> | servers / databases | Yes (see [note below](#sqlnote)) | Sim |
> | servers / encryptionProtector | Não | Não |
> | servers / firewallRules | Não | Não |
> | servers / keys | Não | Não |
> | servers / restorableDroppedDatabases | Não | Não |
> | servers / serviceobjectives | Não | Não |
> | servers / tdeCertificates | Não | Não |
> | virtualClusters | Não | Não |

<a id="sqlnote" />

> [!NOTE]
> The Master database doesn't support tags, but other databases, including Azure SQL Data Warehouse databases, support tags. Azure SQL Data Warehouse databases must be in Active (not Paused) state.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Sim | Sim |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Não | Não |
> | SqlVirtualMachines | Sim | Sim |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Sim | Sim |
> | storageAccounts / blobServices | Não | Não |
> | storageAccounts / fileServices | Não | Não |
> | storageAccounts / queueServices | Não | Não |
> | storageAccounts / services | Não | Não |
> | storageAccounts / services / metricDefinitions | Não | Não |
> | storageAccounts / tableServices | Não | Não |
> | usages | Não | Não |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | caches | Sim | Sim |
> | caches / storageTargets | Não | Não |
> | usageModels | Não | Não |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Não | Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sim | Sim |
> | storageSyncServices / registeredServers | Não | Não |
> | storageSyncServices / syncGroups | Não | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não | Não |
> | storageSyncServices / workflows | Não | Não |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sim | Sim |
> | storageSyncServices / registeredServers | Não | Não |
> | storageSyncServices / syncGroups | Não | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não | Não |
> | storageSyncServices / workflows | Não | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sim | Sim |
> | storageSyncServices / registeredServers | Não | Não |
> | storageSyncServices / syncGroups | Não | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não | Não |
> | storageSyncServices / workflows | Não | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managers | Sim | Sim |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Yes (see note below) | Sim |

> [!NOTE]
> You can't add a tag when streamingjobs is running. Stop the resource to add a tag.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | cancel | Não | Não |
> | CreateSubscription | Não | Não |
> | enable | Não | Não |
> | rename | Não | Não |
> | SubscriptionDefinitions | Não | Não |
> | SubscriptionOperations | Não | Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | environments | Sim | Não |
> | environments / accessPolicies | Não | Não |
> | environments / eventsources | Sim | Não |
> | environments / referenceDataSets | Sim | Não |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Sim | Sim |
> | dedicatedCloudServices | Sim | Sim |
> | virtualMachines | Sim | Sim |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Não | Não |
> | apiManagementAccounts / apiAcls | Não | Não |
> | apiManagementAccounts / apis | Não | Não |
> | apiManagementAccounts / apis / apiAcls | Não | Não |
> | apiManagementAccounts / apis / connectionAcls | Não | Não |
> | apiManagementAccounts / apis / connections | Não | Não |
> | apiManagementAccounts / apis / connections / connectionAcls | Não | Não |
> | apiManagementAccounts / apis / localizedDefinitions | Não | Não |
> | apiManagementAccounts / connectionAcls | Não | Não |
> | apiManagementAccounts / connections | Não | Não |
> | billingMeters | Não | Não |
> | certificates | Sim | Sim |
> | connectionGateways | Sim | Sim |
> | connections | Sim | Sim |
> | customApis | Sim | Sim |
> | deletedSites | Não | Não |
> | functions | Não | Não |
> | hostingEnvironments | Sim | Sim |
> | hostingEnvironments / multiRolePools | Não | Não |
> | hostingEnvironments / workerPools | Não | Não |
> | publishingUsers | Não | Não |
> | recommendations | Não | Não |
> | resourceHealthMetadata | Não | Não |
> | runtimes | Não | Não |
> | serverFarms | Sim | Sim |
> | serverFarms / eventGridFilters | Não | Não |
> | sites | Sim | Sim |
> | sites / config  | Não | Não |
> | sites / eventGridFilters | Não | Não |
> | sites / hostNameBindings | Não | Não |
> | sites / networkConfig | Não | Não |
> | sites / premieraddons | Sim | Sim |
> | sites / slots | Sim | Sim |
> | sites / slots / eventGridFilters | Não | Não |
> | sites / slots / hostNameBindings | Não | Não |
> | sites / slots / networkConfig | Não | Não |
> | sourceControls | Não | Não |
> | validate | Não | Não |
> | verifyHostingEnvironmentVnet | Não | Não |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Não | Não |
> | diagnosticSettingsCategories | Não | Não |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Sim | Sim |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | components | Não | Não |
> | componentsSummary | Não | Não |
> | monitorInstances | Não | Não |
> | monitorInstancesSummary | Não | Não |
> | monitors | Não | Não |
> | notificationSettings | Não | Não |

## <a name="next-steps"></a>Passos seguintes

To learn how to apply tags to resources, see [Use tags to organize your Azure resources](resource-group-using-tags.md).
