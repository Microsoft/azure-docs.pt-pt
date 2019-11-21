---
title: Eliminação completa de modo
description: Shows how resource types handle complete mode deletion in Azure Resource Manager templates.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 075dd5071d6c59c6cc73fff8d51a495b012665dd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232695"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Deletion of Azure resources for complete mode deployments

This article describes how resource types handle deletion when not in a template that is deployed in complete mode.

The resource types marked with **Yes** are deleted when the type isn't in the template deployed with complete mode.

The resource types marked with **No** aren't automatically deleted when not in the template; however, they're deleted if the parent resource is deleted. For a full description of the behavior, see [Azure Resource Manager deployment modes](deployment-modes.md).

If you deploy to [more than one resource group in a template](resource-manager-cross-resource-group-deployment.md), resources in the resource group specified in the deployment operation are eligible to be deleted. Resources in the secondary resource groups aren't deleted.

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
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | DomainServices | Sim |
> | DomainServices / oucontainer | Não |

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
> | addsservices | Não |
> | agents | Não |
> | anonymousapiusers | Não |
> | configuração | Não |
> | registos | Não |
> | reports | Não |
> | servicehealthmetrics | Não |
> | services | Não |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | configurations | Não |
> | generateRecommendations | Não |
> | do IdP | Não |
> | recommendations | Não |
> | suppressions | Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | actionRules | Sim |
> | alerts | Não |
> | alertsList | Não |
> | alertsMetaData | Não |
> | alertsSummary | Não |
> | alertsSummaryList | Não |
> | feedback | Não |
> | smartDetectorAlertRules | Sim |
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
> | configurationStores / eventGridFilters | Não |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Spring | Sim |

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
> | dataAliases | Não |
> | denyAssignments | Não |
> | elevateAccess | Não |
> | findOrphanRoleAssignments | Não |
> | locks | Não |
> | permissions | Não |
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
> | automationAccounts / configurations | Sim |
> | automationAccounts / jobs | Não |
> | automationAccounts / runbooks | Sim |
> | automationAccounts / softwareUpdateConfigurations | Não |
> | automationAccounts / webhooks | Não |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | configurationStores | Sim |
> | configurationStores / eventGridFilters | Não |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | environments | Não |
> | environments / accounts | Não |
> | environments / accounts / namespaces | Não |
> | environments / accounts / namespaces / configurations | Não |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | b2cDirectories | Sim |
> | b2ctenants | Não |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | hybridDataManagers | Sim |
> | postgresInstances | Sim |
> | sqlBigDataClusters | Sim |
> | sqlInstances | Sim |
> | sqlServerRegistrations | Sim |
> | sqlServerRegistrations / sqlServers | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | registrations | Sim |
> | registrations / customerSubscriptions | Não |
> | registrations / products | Não |
> | verificationKeys | Não |

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
> | billingAccounts / agreements | Não |
> | billingAccounts / billingPermissions | Não |
> | billingAccounts / billingProfiles | Não |
> | billingAccounts / billingProfiles / billingPermissions | Não |
> | billingAccounts / billingProfiles / billingRoleAssignments | Não |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Não |
> | billingAccounts / billingProfiles / billingSubscriptions | Não |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Não |
> | billingAccounts / billingProfiles / customers | Não |
> | billingAccounts / billingProfiles / invoices | Não |
> | billingAccounts / billingProfiles / invoices / pricesheet | Não |
> | billingAccounts / billingProfiles / invoiceSections | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Não |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Não |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Não |
> | billingAccounts / billingProfiles / invoiceSections / products | Não |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | Não |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | Não |
> | billingAccounts / billingProfiles / invoiceSections / transactions | Não |
> | billingAccounts / billingProfiles / invoiceSections / transfers | Não |
> | billingAccounts / BillingProfiles / patchOperations | Não |
> | billingAccounts / billingProfiles / paymentMethods | Não |
> | billingAccounts / billingProfiles / policies | Não |
> | billingAccounts / billingProfiles / pricesheet | Não |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Não |
> | billingAccounts / billingProfiles / products | Não |
> | billingAccounts / billingProfiles / transactions | Não |
> | billingAccounts / billingRoleAssignments | Não |
> | billingAccounts / billingRoleDefinitions | Não |
> | billingAccounts / billingSubscriptions | Não |
> | billingAccounts / billingSubscriptions / invoices | Não |
> | billingAccounts / createBillingRoleAssignment | Não |
> | billingAccounts / createInvoiceSectionOperations | Não |
> | billingAccounts / customers | Não |
> | billingAccounts / customers / billingPermissions | Não |
> | billingAccounts / customers / billingSubscriptions | Não |
> | billingAccounts / customers / initiateTransfer | Não |
> | billingAccounts / customers / policies | Não |
> | billingAccounts / customers / products | Não |
> | billingAccounts / customers / transactions | Não |
> | billingAccounts / customers / transfers | Não |
> | billingAccounts / departments | Não |
> | billingAccounts / enrollmentAccounts | Não |
> | billingAccounts / invoices | Não |
> | billingAccounts / invoiceSections | Não |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Não |
> | billingAccounts / invoiceSections / billingSubscriptions | Não |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | Não |
> | billingAccounts / invoiceSections / elevate | Não |
> | billingAccounts / invoiceSections / initiateTransfer | Não |
> | billingAccounts / invoiceSections / patchOperations | Não |
> | billingAccounts / invoiceSections / productMoveOperations | Não |
> | billingAccounts / invoiceSections / products | Não |
> | billingAccounts / invoiceSections / products / transfer | Não |
> | billingAccounts / invoiceSections / products / updateAutoRenew | Não |
> | billingAccounts / invoiceSections / transactions | Não |
> | billingAccounts / invoiceSections / transfers | Não |
> | billingAccounts / lineOfCredit | Não |
> | billingAccounts / patchOperations | Não |
> | billingAccounts / paymentMethods | Não |
> | billingAccounts / products | Não |
> | billingAccounts / transactions | Não |
> | billingPeriods | Não |
> | billingPermissions | Não |
> | billingProperty | Não |
> | billingRoleAssignments | Não |
> | billingRoleDefinitions | Não |
> | createBillingRoleAssignment | Não |
> | departments | Não |
> | enrollmentAccounts | Não |
> | invoices | Não |
> | transfers | Não |
> | transfers / acceptTransfer | Não |
> | transfers / declineTransfer | Não |
> | transfers / operationStatus | Não |
> | transfers / validateTransfer | Não |
> | validateAddress | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | mapApis | Sim |
> | updateCommunicationPreference | Não |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | blockchainMembers | Sim |
> | cordaMembers | Sim |
> | observadores | Sim |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | blueprintAssignments | Não |
> | blueprintAssignments / assignmentOperations | Não |
> | blueprintAssignments / operations | Não |
> | blueprints | Não |
> | blueprints / artifacts | Não |
> | blueprints / versions | Não |
> | blueprints / versions / artifacts | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | botServices | Sim |
> | botServices / channels | Não |
> | botServices / connections | Não |
> | languages | Não |
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
> | catalogs | Não |
> | commercialReservationOrders | Não |
> | exchange | Não |
> | placePurchaseOrder | Não |
> | reservationOrders | Não |
> | reservationOrders / calculateRefund | Não |
> | reservationOrders / merge | Não |
> | reservationOrders / reservations | Não |
> | reservationOrders / reservations / revisions | Não |
> | reservationOrders / return | Não |
> | reservationOrders / split | Não |
> | reservationOrders / swap | Não |
> | reservations | Não |
> | resources | Não |
> | validateReservationOrder | Não |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Não |
> | CdnWebApplicationFirewallPolicies | Sim |
> | edgenodes | Não |
> | profiles | Sim |
> | profiles / endpoints | Sim |
> | profiles / endpoints / customdomains | Não |
> | profiles / endpoints / origins | Não |
> | validateProbe | Não |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | certificateOrders | Sim |
> | certificateOrders / certificates | Não |
> | validateCertificateRegistrationInformation | Não |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | capabilities | Não |
> | domainNames | Sim |
> | domainNames / capabilities | Não |
> | domainNames / internalLoadBalancers | Não |
> | domainNames / serviceCertificates | Não |
> | domainNames / slots | Não |
> | domainNames / slots / roles | Não |
> | domainNames / slots / roles / metricDefinitions | Não |
> | domainNames / slots / roles / metrics | Não |
> | moveSubscriptionResources | Não |
> | operatingSystemFamilies | Não |
> | operatingSystems | Não |
> | quotas | Não |
> | resourceTypes | Não |
> | validateSubscriptionMoveAvailability | Não |
> | virtualMachines | Sim |
> | virtualMachines / diagnosticSettings | Não |
> | virtualMachines / metricDefinitions | Não |
> | virtualMachines / metrics | Não |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | classicInfrastructureResources | Não |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | capabilities | Não |
> | expressRouteCrossConnections | Não |
> | expressRouteCrossConnections / peerings | Não |
> | gatewaySupportedDevices | Não |
> | networkSecurityGroups | Sim |
> | quotas | Não |
> | reservedIps | Sim |
> | virtualNetworks | Sim |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Não |
> | virtualNetworks / virtualNetworkPeerings | Não |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | capabilities | Não |
> | disks | Não |
> | imagens | Não |
> | osImages | Não |
> | osPlatformImages | Não |
> | publicImages | Não |
> | quotas | Não |
> | storageAccounts | Sim |
> | storageAccounts / blobServices | Não |
> | storageAccounts / fileServices | Não |
> | storageAccounts / metricDefinitions | Não |
> | storageAccounts / metrics | Não |
> | storageAccounts / queueServices | Não |
> | storageAccounts / services | Não |
> | storageAccounts / services / diagnosticSettings | Não |
> | storageAccounts / services / metricDefinitions | Não |
> | storageAccounts / services / metrics | Não |
> | storageAccounts / tableServices | Não |
> | storageAccounts / vmImages | Não |
> | vmImages | Não |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |

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
> | disks | Sim |
> | galleries | Sim |
> | galleries / applications | Não |
> | galleries / applications / versions | Não |
> | galleries / images | Não |
> | galleries / images / versions | Não |
> | hostGroups | Sim |
> | hostGroups / hosts | Sim |
> | imagens | Sim |
> | proximityPlacementGroups | Sim |
> | restorePointCollections | Sim |
> | restorePointCollections / restorePoints | Não |
> | sharedVMImages | Sim |
> | sharedVMImages / versions | Não |
> | instantâneos | Sim |
> | virtualMachines | Sim |
> | virtualMachines / extensions | Sim |
> | virtualMachines / metricDefinitions | Não |
> | virtualMachineScaleSets | Sim |
> | virtualMachineScaleSets / extensions | Não |
> | virtualMachineScaleSets / networkInterfaces | Não |
> | virtualMachineScaleSets / publicIPAddresses | Não |
> | virtualMachineScaleSets / virtualMachines | Não |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Não |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | AggregatedCost | Não |
> | Saldos | Não |
> | Orçamentos | Não |
> | Cobranças | Não |
> | CostTags | Não |
> | credits | Não |
> | eventos | Não |
> | Previsões | Não |
> | lots | Não |
> | Marketplaces | Não |
> | Pricesheets | Não |
> | products | Não |
> | ReservationDetails | Não |
> | ReservationRecommendations | Não |
> | ReservationSummaries | Não |
> | ReservationTransactions | Não |
> | Etiquetas | Não |
> | tenants | Não |
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
> | registries | Sim |
> | registries / builds | Não |
> | registries / builds / cancel | Não |
> | registries / builds / getLogLink | Não |
> | registries / buildTasks | Sim |
> | registries / buildTasks / steps | Não |
> | registries / eventGridFilters | Não |
> | registries / generateCredentials | Não |
> | registries / getBuildSourceUploadUrl | Não |
> | registries / GetCredentials | Não |
> | registries / importImage | Não |
> | registries / queueBuild | Não |
> | registries / regenerateCredential | Não |
> | registries / regenerateCredentials | Não |
> | registries / replications | Sim |
> | registries / runs | Não |
> | registries / runs / cancel | Não |
> | registries / scheduleRun | Não |
> | registries / scopeMaps | Não |
> | registries / taskRuns | Sim |
> | registries / tasks | Sim |
> | registries / tokens | Não |
> | registries / updatePolicies | Não |
> | registries / webhooks | Sim |
> | registries / webhooks / getCallbackConfig | Não |
> | registries / webhooks / ping | Não |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | containerServices | Sim |
> | managedClusters | Sim |
> | openShiftManagedClusters | Sim |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Alertas | Não |
> | BillingAccounts | Não |
> | Orçamentos | Não |
> | CloudConnectors | Não |
> | Conectores | Sim |
> | Departments | Não |
> | Dimensões | Não |
> | EnrollmentAccounts | Não |
> | Exportações | Não |
> | ExternalBillingAccounts | Não |
> | ExternalBillingAccounts / Alerts | Não |
> | ExternalBillingAccounts / Dimensions | Não |
> | ExternalBillingAccounts / Forecast | Não |
> | ExternalBillingAccounts / Query | Não |
> | ExternalSubscriptions | Não |
> | ExternalSubscriptions / Alerts | Não |
> | ExternalSubscriptions / Dimensions | Não |
> | ExternalSubscriptions / Forecast | Não |
> | ExternalSubscriptions / Query | Não |
> | Forecast | Não |
> | Consulta | Não |
> | register | Não |
> | Reportconfigs | Não |
> | Relatórios | Não |
> | Definições | Não |
> | showbackRules | Não |
> | Vistas | Não |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | requests | Não |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | associations | Não |
> | resourceProviders | Sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | jobs | Sim |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Sim |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | workspaces | Sim |
> | workspaces / virtualNetworkPeerings | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | catalogs | Sim |
> | datacatalogs | Sim |
> | datacatalogs / datasources | Não |
> | datacatalogs / datasources / scans | Não |
> | datacatalogs / datasources / scans / datasets | Não |
> | datacatalogs / datasources / scans / triggers | Não |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | dataFactories | Sim |
> | dataFactories / diagnosticSettings | Não |
> | dataFactories / metricDefinitions | Não |
> | dataFactorySchema | Não |
> | factories | Sim |
> | factories / integrationRuntimes | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | accounts / dataLakeStoreAccounts | Não |
> | accounts / storageAccounts | Não |
> | accounts / storageAccounts / containers | Não |
> | accounts / transferAnalyticsUnits | Não |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | accounts / eventGridFilters | Não |
> | accounts / firewallRules | Não |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | services | Sim |
> | services / projects | Sim |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | accounts / shares | Não |
> | accounts / shares / datasets | Não |
> | accounts / shares / invitations | Não |
> | accounts / shares / providersharesubscriptions | Não |
> | accounts / shares / synchronizationSettings | Não |
> | accounts / sharesubscriptions | Não |
> | accounts / sharesubscriptions / consumerSourceDataSets | Não |
> | accounts / sharesubscriptions / datasetmappings | Não |
> | accounts / sharesubscriptions / triggers | Não |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | servidores | Sim |
> | servers / advisors | Não |
> | servers / privateEndpointConnectionProxies | Não |
> | servers / privateEndpointConnections | Não |
> | servers / privateLinkResources | Não |
> | servers / queryTexts | Não |
> | servers / recoverableServers | Não |
> | servers / topQueryStatistics | Não |
> | servers / virtualNetworkRules | Não |
> | servers / waitStatistics | Não |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | servidores | Sim |
> | servers / advisors | Não |
> | servers / privateEndpointConnectionProxies | Não |
> | servers / privateEndpointConnections | Não |
> | servers / privateLinkResources | Não |
> | servers / queryTexts | Não |
> | servers / recoverableServers | Não |
> | servers / topQueryStatistics | Não |
> | servers / virtualNetworkRules | Não |
> | servers / waitStatistics | Não |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | serverGroups | Sim |
> | servidores | Sim |
> | servers / advisors | Não |
> | servers / keys | Não |
> | servers / privateEndpointConnectionProxies | Não |
> | servers / privateEndpointConnections | Não |
> | servers / privateLinkResources | Não |
> | servers / queryTexts | Não |
> | servers / recoverableServers | Não |
> | servers / topQueryStatistics | Não |
> | servers / virtualNetworkRules | Não |
> | servers / waitStatistics | Não |
> | serversv2 | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | artifactSources | Sim |
> | rollouts | Sim |
> | serviceTopologies | Sim |
> | serviceTopologies / services | Sim |
> | serviceTopologies / services / serviceUnits | Sim |
> | passos | Sim |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | applicationgroups | Sim |
> | applicationgroups / applications | Não |
> | applicationgroups / desktops | Não |
> | applicationgroups / startmenuitems | Não |
> | hostpools | Sim |
> | hostpools / sessionhosts | Não |
> | hostpools / sessionhosts / usersessions | Não |
> | hostpools / usersessions | Não |
> | workspaces | Sim |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | ElasticPools | Sim |
> | ElasticPools / IotHubTenants | Sim |
> | IotHubs | Sim |
> | IotHubs / eventGridFilters | Não |
> | ProvisioningServices | Sim |
> | usages | Não |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | pipelines | Sim |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | controllers | Sim |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | labcenters | Sim |
> | labs | Sim |
> | labs / environments | Sim |
> | labs / serviceRunners | Sim |
> | labs / virtualMachines | Sim |
> | schedules | Sim |

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
> | domains | Sim |
> | domains / domainOwnershipIdentifiers | Não |
> | generateSsoRequest | Não |
> | topLevelDomains | Não |
> | validateDomainRegistrationInformation | Não |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | lcsprojects | Não |
> | lcsprojects / clouddeployments | Não |
> | lcsprojects / connectors | Não |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | services | Sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | domains | Sim |
> | domains / topics | Não |
> | eventSubscriptions | Não |
> | extensionTopics | Não |
> | topics | Sim |
> | topicTypes | Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | clusters | Sim |
> | namespaces | Sim |
> | namespaces / authorizationrules | Não |
> | namespaces / disasterrecoveryconfigs | Não |
> | namespaces / eventhubs | Não |
> | namespaces / eventhubs / authorizationrules | Não |
> | namespaces / eventhubs / consumergroups | Não |
> | namespaces / networkrulesets | Não |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | elástica | Não |
> | providers | Não |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | enroll | Não |
> | galleryitems | Não |
> | generateartifactaccessuri | Não |
> | myareas | Não |
> | myareas / areas | Não |
> | myareas / areas / areas | Não |
> | myareas / areas / areas / galleryitems | Não |
> | myareas / areas / galleryitems | Não |
> | myareas / galleryitems | Não |
> | register | Não |
> | resources | Não |
> | retrieveresourcesbyid | Não |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | configurationProfileAssignments | Não |
> | guestConfigurationAssignments | Não |
> | software | Não |
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
> | clusters / applications | Não |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | services | Sim |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | machines | Sim |
> | machines / extensions | Sim |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | dataManagers | Sim |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | components | Sim |
> | networkScopes | Sim |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | jobs | Sim |

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
> | vaults | Sim |
> | vaults / accessPolicies | Não |
> | vaults / eventGridFilters | Não |
> | vaults / secrets | Não |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | clusters | Sim |
> | clusters / attacheddatabaseconfigurations | Não |
> | clusters / databases | Não |
> | clusters / databases / dataconnections | Não |
> | clusters / databases / eventhubconnections | Não |
> | clusters / sharedidentities | Não |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | labaccounts | Sim |
> | utilizadores | Não |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | hostingEnvironments | Sim |
> | integrationAccounts | Sim |
> | integrationServiceEnvironments | Sim |
> | integrationServiceEnvironments / managedApis | Sim |
> | isolatedEnvironments | Sim |
> | workflows | Sim |

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
> | workspaces | Sim |
> | workspaces / computes | Não |
> | workspaces / eventGridFilters | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Identities | Não |
> | userAssignedIdentities | Sim |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

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
> | resources | Não |
> | startTenantBackfill | Não |
> | tenantBackfillStatus | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | accounts / eventGridFilters | Não |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | offers | Não |
> | offerTypes | Não |
> | offerTypes / publishers | Não |
> | offerTypes / publishers / offers | Não |
> | offerTypes / publishers / offers / plans | Não |
> | offerTypes / publishers / offers / plans / agreements | Não |
> | offerTypes / publishers / offers / plans / configs | Não |
> | offerTypes / publishers / offers / plans / configs / importImage | Não |
> | privategalleryitems | Não |
> | products | Não |
> | publishers | Não |
> | publishers / offers | Não |
> | publishers / offers / amendments | Não |

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
> | agreements | Não |
> | offertypes | Não |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | mediaservices | Sim |
> | mediaservices / accountFilters | Não |
> | mediaservices / assets | Não |
> | mediaservices / assets / assetFilters | Não |
> | mediaservices / contentKeyPolicies | Não |
> | mediaservices / eventGridFilters | Não |
> | mediaservices / liveEventOperations | Não |
> | mediaservices / liveEvents | Sim |
> | mediaservices / liveEvents / liveOutputs | Não |
> | mediaservices / liveOutputOperations | Não |
> | mediaservices / mediaGraphs | Não |
> | mediaservices / streamingEndpointOperations | Não |
> | mediaservices / streamingEndpoints | Sim |
> | mediaservices / streamingLocators | Não |
> | mediaservices / streamingPolicies | Não |
> | mediaservices / transforms | Não |
> | mediaservices / transforms / jobs | Não |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

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
> | projects | Sim |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Sim |
> | objectUnderstandingAccounts | Sim |
> | remoteRenderingAccounts | Sim |
> | spatialAnchorsAccounts | Sim |
> | surfaceReconstructionAccounts | Sim |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | netAppAccounts | Sim |
> | netAppAccounts / capacityPools | Sim |
> | netAppAccounts / capacityPools / volumes | Sim |
> | netAppAccounts / capacityPools / volumes / mountTargets | Sim |
> | netAppAccounts / capacityPools / volumes / snapshots | Sim |
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
> | connections | Sim |
> | ddosCustomPolicies | Sim |
> | ddosProtectionPlans | Sim |
> | dnsOperationStatuses | Não |
> | dnszones | Sim |
> | dnszones / A | Não |
> | dnszones / AAAA | Não |
> | dnszones / all | Não |
> | dnszones / CAA | Não |
> | dnszones / CNAME | Não |
> | dnszones / MX | Não |
> | dnszones / NS | Não |
> | dnszones / PTR | Não |
> | dnszones / recordsets | Não |
> | dnszones / SOA | Não |
> | dnszones / SRV | Não |
> | dnszones / TXT | Não |
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
> | networkWatchers / connectionMonitors | Sim |
> | networkWatchers / lenses | Sim |
> | networkWatchers / pingMeshes | Sim |
> | p2sVpnGateways | Sim |
> | privateDnsOperationStatuses | Não |
> | privateDnsZones | Sim |
> | privateDnsZones / A | Não |
> | privateDnsZones / AAAA | Não |
> | privateDnsZones / all | Não |
> | privateDnsZones / CNAME | Não |
> | privateDnsZones / MX | Não |
> | privateDnsZones / PTR | Não |
> | privateDnsZones / SOA | Não |
> | privateDnsZones / SRV | Não |
> | privateDnsZones / TXT | Não |
> | privateDnsZones / virtualNetworkLinks | Sim |
> | privateEndpoints | Sim |
> | privateLinkServices | Sim |
> | publicIPAddresses | Sim |
> | publicIPPrefixes | Sim |
> | routeFilters | Sim |
> | routeTables | Sim |
> | serviceEndpointPolicies | Sim |
> | trafficManagerGeographicHierarchies | Não |
> | trafficmanagerprofiles | Sim |
> | trafficmanagerprofiles / heatMaps | Não |
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
> | namespaces / notificationHubs | Sim |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | osNamespaces | Sim |

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
> | clusters | Sim |
> | dispositivos | Não |
> | linkTargets | Não |
> | storageInsightConfigs | Não |
> | workspaces | Sim |
> | workspaces / dataSources | Não |
> | workspaces / linkedServices | Não |
> | workspaces / query | Não |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | managementassociations | Não |
> | managementconfigurations | Sim |
> | soluções | Sim |
> | views | Sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | legacyPeerings | Não |
> | peerAsns | Não |
> | peerings | Sim |
> | peeringServiceProviders | Não |
> | peeringServices | Sim |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | policyEvents | Não |
> | policyMetadata | Não |
> | policyStates | Não |
> | policyTrackedResources | Não |
> | remediations | Não |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | consoles | Não |
> | dashboards | Sim |
> | userSettings | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | workspaceCollections | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | capacities | Sim |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | backupProtectedItems | Não |
> | vaults | Sim |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | namespaces | Sim |
> | namespaces / authorizationrules | Não |
> | namespaces / hybridconnections | Não |
> | namespaces / hybridconnections / authorizationrules | Não |
> | namespaces / wcfrelays | Não |
> | namespaces / wcfrelays / authorizationrules | Não |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Não |
> | collections | Sim |
> | collections / applications | Não |
> | collections / securityprincipals | Não |
> | templateImages | Não |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | consultas de | Sim |
> | resourceChangeDetails | Não |
> | resourceChanges | Não |
> | resources | Não |
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
> | notifications | Não |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | deployments | Não |
> | deployments / operations | Não |
> | deploymentScripts | Sim |
> | deploymentScripts / logs | Não |
> | links | Não |
> | notifyResourceJobs | Não |
> | providers | Não |
> | resourceGroups | Não |
> | resources | Não |
> | subscriptions | Não |
> | subscriptions / providers | Não |
> | subscriptions / resources | Não |
> | subscriptions / tagnames | Não |
> | subscriptions / tagNames / tagValues | Não |
> | tenants | Não |

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
> | jobcollections | Sim |

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
> | assessments | Não |
> | autoDismissAlertsRules | Não |
> | automations | Sim |
> | AutoProvisioningSettings | Não |
> | Compliances | Não |
> | dataCollectionAgents | Não |
> | deviceSecurityGroups | Não |
> | discoveredSecuritySolutions | Não |
> | externalSecuritySolutions | Não |
> | InformationProtectionPolicies | Não |
> | iotSecuritySolutions | Sim |
> | iotSecuritySolutions / analyticsModels | Não |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Não |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Não |
> | jitNetworkAccessPolicies | Não |
> | networkData | Não |
> | policies | Não |
> | pricings | Não |
> | regulatoryComplianceStandards | Não |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Não |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Não |
> | securityContacts | Não |
> | securitySolutions | Não |
> | securitySolutionsReferenceData | Não |
> | securityStatuses | Não |
> | securityStatusesSummaries | Não |
> | serverVulnerabilityAssessments | Não |
> | settings | Não |
> | subAssessments | Não |
> | tarefas | Não |
> | topologies | Não |
> | workspaceSettings | Não |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | diagnosticSettings | Não |
> | diagnosticSettingsCategories | Não |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aggregations | Não |
> | alertRules | Não |
> | alertRuleTemplates | Não |
> | bookmarks | Não |
> | cases | Não |
> | dataConnectors | Não |
> | entities | Não |
> | entityQueries | Não |
> | officeConsents | Não |
> | settings | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | namespaces | Sim |
> | namespaces / authorizationrules | Não |
> | namespaces / disasterrecoveryconfigs | Não |
> | namespaces / eventgridfilters | Não |
> | namespaces / networkrulesets | Não |
> | namespaces / queues | Não |
> | namespaces / queues / authorizationrules | Não |
> | namespaces / topics | Não |
> | namespaces / topics / authorizationrules | Não |
> | namespaces / topics / subscriptions | Não |
> | namespaces / topics / subscriptions / rules | Não |
> | premiumMessagingRegions | Não |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicações | Sim |
> | clusters | Sim |
> | clusters / applications | Não |
> | containerGroups | Sim |
> | containerGroupSets | Sim |
> | edgeclusters | Sim |
> | edgeclusters / applications | Não |
> | networks | Sim |
> | secretstores | Sim |
> | secretstores / certificates | Não |
> | secretstores / secrets | Não |
> | volumes | Sim |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicações | Sim |
> | containerGroups | Sim |
> | gateways | Sim |
> | networks | Sim |
> | secrets | Sim |
> | volumes | Sim |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | providerRegistrations | Não |
> | providerRegistrations / resourceTypeRegistrations | Não |
> | rollouts | Sim |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | SignalR | Sim |
> | SignalR / eventGridFilters | Não |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | SiteRecoveryVault | Sim |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

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
> | managedInstances / databases | Sim |
> | managedInstances / databases / backupShortTermRetentionPolicies | Não |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | Não |
> | managedInstances / databases / vulnerabilityAssessments | Não |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | Não |
> | managedInstances / encryptionProtector | Não |
> | managedInstances / keys | Não |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Não |
> | managedInstances / vulnerabilityAssessments | Não |
> | servidores | Sim |
> | servers / administrators | Não |
> | servers / communicationLinks | Não |
> | servers / databases | Sim |
> | servers / encryptionProtector | Não |
> | servers / firewallRules | Não |
> | servers / keys | Não |
> | servers / restorableDroppedDatabases | Não |
> | servers / serviceobjectives | Não |
> | servers / tdeCertificates | Não |
> | virtualClusters | Não |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Sim |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Não |
> | SqlVirtualMachines | Sim |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | storageAccounts | Sim |
> | storageAccounts / blobServices | Não |
> | storageAccounts / fileServices | Não |
> | storageAccounts / queueServices | Não |
> | storageAccounts / services | Não |
> | storageAccounts / services / metricDefinitions | Não |
> | storageAccounts / tableServices | Não |
> | usages | Não |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | caches | Sim |
> | caches / storageTargets | Não |
> | usageModels | Não |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | replicationGroups | Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | Sim |
> | storageSyncServices / registeredServers | Não |
> | storageSyncServices / syncGroups | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não |
> | storageSyncServices / workflows | Não |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | Sim |
> | storageSyncServices / registeredServers | Não |
> | storageSyncServices / syncGroups | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não |
> | storageSyncServices / workflows | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | Sim |
> | storageSyncServices / registeredServers | Não |
> | storageSyncServices / syncGroups | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não |
> | storageSyncServices / workflows | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | managers | Sim |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | streamingjobs | Sim |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | cancel | Não |
> | CreateSubscription | Não |
> | enable | Não |
> | rename | Não |
> | SubscriptionDefinitions | Não |
> | SubscriptionOperations | Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | environments | Sim |
> | environments / accessPolicies | Não |
> | environments / eventsources | Sim |
> | environments / referenceDataSets | Sim |

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
> | apiManagementAccounts / apiAcls | Não |
> | apiManagementAccounts / apis | Não |
> | apiManagementAccounts / apis / apiAcls | Não |
> | apiManagementAccounts / apis / connectionAcls | Não |
> | apiManagementAccounts / apis / connections | Não |
> | apiManagementAccounts / apis / connections / connectionAcls | Não |
> | apiManagementAccounts / apis / localizedDefinitions | Não |
> | apiManagementAccounts / connectionAcls | Não |
> | apiManagementAccounts / connections | Não |
> | billingMeters | Não |
> | certificates | Sim |
> | connectionGateways | Sim |
> | connections | Sim |
> | customApis | Sim |
> | deletedSites | Não |
> | functions | Não |
> | hostingEnvironments | Sim |
> | hostingEnvironments / multiRolePools | Não |
> | hostingEnvironments / workerPools | Não |
> | publishingUsers | Não |
> | recommendations | Não |
> | resourceHealthMetadata | Não |
> | runtimes | Não |
> | serverFarms | Sim |
> | serverFarms / eventGridFilters | Não |
> | sites | Sim |
> | sites/config  | Não |
> | sites / eventGridFilters | Não |
> | sites / hostNameBindings | Não |
> | sites / networkConfig | Não |
> | sites / premieraddons | Sim |
> | sites / slots | Sim |
> | sites / slots / eventGridFilters | Não |
> | sites / slots / hostNameBindings | Não |
> | sites / slots / networkConfig | Não |
> | sourceControls | Não |
> | validate | Não |
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
> | DeviceServices | Sim |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | components | Não |
> | componentsSummary | Não |
> | monitorInstances | Não |
> | monitorInstancesSummary | Não |
> | monitors | Não |
> | notificationSettings | Não |

## <a name="next-steps"></a>Passos seguintes

To get the same data as a file of comma-separated values, download [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
