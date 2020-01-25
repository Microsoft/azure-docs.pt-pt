---
title: Eliminação completa de modo
description: Mostra como os tipos de recurso lidam com a exclusão de modo completo em modelos de Azure Resource Manager.
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: b24bf2d8076dbaa1975f5444d597647156a31f4f
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715589"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Exclusão de recursos do Azure para implantações de modo completo

Este artigo descreve como os tipos de recursos tratam da exclusão quando não estão em um modelo implantado no modo completo.

Os tipos de recurso marcados com **Sim** são excluídos quando o tipo não está no modelo implantado com o modo completo.

Os tipos de recurso marcados **sem não são excluídos** automaticamente quando não estão no modelo; no entanto, elas serão excluídas se o recurso pai for excluído. Para obter uma descrição completa do comportamento, consulte [Azure Resource Manager modos de implantação](deployment-modes.md).

Se você implantar em [mais de um grupo de recursos em um modelo](cross-resource-group-deployment.md), os recursos no grupo de recursos especificado na operação de implantação poderão ser excluídos. Os recursos nos grupos de recursos secundários não são excluídos.

Ir para um namespace do provedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
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
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
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
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft. OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. ProjectBabylon](#microsoftprojectbabylon)
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
> - [Microsoft. SQL](#microsoftsql)
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
> - [Microsoft.VnfManager](#microsoftvnfmanager)
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
> | representantes | Não |
> | anonymousapiusers | Não |
> | configuração | Não |
> | registos | Não |
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
> | recomendações | Não |
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

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

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
> | Aliases de dataalias | Não |
> | denyAssignments | Não |
> | elevateAccess | Não |
> | findOrphanRoleAssignments | Não |
> | bloquea | Não |
> | permissões | Não |
> | policyAssignments | Não |
> | policyDefinitions | Não |
> | policySetDefinitions | Não |
> | providerOperations | Não |
> | roleAssignments | Não |
> | roleAssignmentsUsageMetrics | Não |
> | roleDefinitions | Não |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | automationAccounts | Sim |
> | automationAccounts/configurações | Sim |
> | automationAccounts/trabalhos | Não |
> | automationAccounts/runbooks | Sim |
> | automationAccounts/softwareUpdateConfigurations | Não |
> | automationAccounts/WebHooks | Não |

## <a name="microsoftazconfig"></a>Microsoft. Azconfig

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | configurationStores | Sim |
> | configurationStores / eventGridFilters | Não |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | sistemas | Não |
> | ambientes/contas | Não |
> | ambientes/contas/namespaces | Não |
> | ambientes/contas/namespaces/configurações | Não |

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
> | hybridDataManagers | Sim |
> | postgresInstances | Sim |
> | sqlBigDataClusters | Sim |
> | SQLInstances | Sim |
> | sqlServerRegistrations | Sim |
> | sqlServerRegistrations/sqlservers | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | registros | Sim |
> | registros/customerSubscriptions | Não |
> | registros/produtos | Não |
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
> | BillingAccounts | Não |
> | billingAccounts/contratos | Não |
> | billingAccounts / billingPermissions | Não |
> | billingAccounts / billingProfiles | Não |
> | billingAccounts / billingProfiles / billingPermissions | Não |
> | billingAccounts / billingProfiles / billingRoleAssignments | Não |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Não |
> | billingAccounts / billingProfiles / billingSubscriptions | Não |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Não |
> | billingAccounts/billingProfiles/clientes | Não |
> | contas de faturação / faturaçãoPerfis / instruções | Não |
> | billingAccounts/billingProfiles/faturas | Não |
> | billingAccounts/billingProfiles/faturas/pricesheet | Não |
> | billingAccounts / billingProfiles / invoiceSections | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Não |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Não |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Não |
> | billingAccounts/billingProfiles/invoiceSections/produtos | Não |
> | billingAccounts/billingProfiles/invoiceSections/produtos/transferência | Não |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | Não |
> | billingAccounts/billingProfiles/invoiceSections/transações | Não |
> | billingAccounts/billingProfiles/invoiceSections/transferências | Não |
> | billingAccounts / BillingProfiles / patchOperations | Não |
> | billingAccounts / billingProfiles / paymentMethods | Não |
> | billingAccounts/billingProfiles/políticas | Não |
> | billingAccounts/billingProfiles/pricesheet | Não |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Não |
> | billingAccounts/billingProfiles/produtos | Não |
> | billingAccounts/billingProfiles/transações | Não |
> | billingAccounts / billingRoleAssignments | Não |
> | billingAccounts / billingRoleDefinitions | Não |
> | billingAccounts / billingSubscriptions | Não |
> | billingAccounts/billingSubscriptions/faturas | Não |
> | billingAccounts / createBillingRoleAssignment | Não |
> | billingAccounts / createInvoiceSectionOperations | Não |
> | billingAccounts/clientes | Não |
> | billingAccounts/clientes/billingPermissions | Não |
> | billingAccounts/clientes/billingSubscriptions | Não |
> | billingAccounts/clientes/initiateTransfer | Não |
> | billingAccounts/clientes/políticas | Não |
> | billingAccounts/clientes/produtos | Não |
> | billingAccounts/clientes/transações | Não |
> | billingAccounts/clientes/transferências | Não |
> | billingAccounts/departamentos | Não |
> | billingAccounts / enrollmentAccounts | Não |
> | billingAccounts/faturas | Não |
> | billingAccounts / invoiceSections | Não |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Não |
> | billingAccounts / invoiceSections / billingSubscriptions | Não |
> | billingAccounts/invoiceSections/billingSubscriptions/transferência | Não |
> | billingAccounts/invoiceSections/Elevate | Não |
> | billingAccounts / invoiceSections / initiateTransfer | Não |
> | billingAccounts / invoiceSections / patchOperations | Não |
> | billingAccounts / invoiceSections / productMoveOperations | Não |
> | billingAccounts/invoiceSections/produtos | Não |
> | billingAccounts/invoiceSections/produtos/transferência | Não |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | Não |
> | billingAccounts/invoiceSections/transações | Não |
> | billingAccounts/invoiceSections/transferências | Não |
> | billingAccounts / lineOfCredit | Não |
> | billingAccounts / patchOperations | Não |
> | billingAccounts / paymentMethods | Não |
> | billingAccounts/produtos | Não |
> | billingAccounts/transações | Não |
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
> | transferências/acceptTransfer | Não |
> | transferências/declineTransfer | Não |
> | transferências/operationStatus | Não |
> | transferências/validateTransfer | Não |
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
> | blueprintAssignments/operações | Não |
> | planos gráficos | Não |
> | plantas/artefatos | Não |
> | plantas/versões | Não |
> | plantas/versões/artefatos | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | botServices | Sim |
> | botServices/canais | Não |
> | botServices/conexões | Não |
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
> | autoCotIncrease | Não |
> | calculateExchange | Não |
> | calculatePrice | Não |
> | calculatePurchasePrice | Não |
> | catálogos | Não |
> | commercialReservationOrders | Não |
> | exchange | Não |
> | placePurchaseOrder | Não |
> | reservationOrders | Não |
> | reservationOrders / calculateRefund | Não |
> | reservationOrders/mesclagem | Não |
> | reservationOrders/reservas | Não |
> | reservationOrders/reservas/revisões | Não |
> | reservationOrders/retorno | Não |
> | reservationOrders/divisão | Não |
> | reservationOrders/permuta | Não |
> | Reservas | Não |
> | resourceProviders | Não |
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
> | certificateOrders/certificados | Não |
> | validateCertificateRegistrationInformation | Não |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | técnicas | Não |
> | domainNames | Sim |
> | nome_do_domínio/recursos | Não |
> | nome_do_domínio/internalLoadBalancers | Não |
> | DomainNames/certificados | Não |
> | nome_do_domínio/Slots | Não |
> | nome_do_domínio/Slots/funções | Não |
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
> | expressRouteCrossConnections/emparelhamentos | Não |
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
> | storageAccounts/blobservices | Não |
> | storageAccounts/fileservices | Não |
> | storageAccounts/metricDefinitions | Não |
> | storageAccounts/métricas | Não |
> | storageAccounts/queueservices | Não |
> | storageAccounts/serviços | Não |
> | storageAccounts/serviços/diagnosticSettings | Não |
> | storageAccounts/serviços/metricDefinitions | Não |
> | storageAccounts/serviços/métricas | Não |
> | storageAccounts/tabelaservices | Não |
> | storageAccounts/vmImages | Não |
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
> | discos | Sim |
> | galerias | Sim |
> | galerias/aplicativos | Não |
> | galerias/aplicativos/versões | Não |
> | galerias/imagens | Não |
> | galerias/imagens/versões | Não |
> | Hosts | Sim |
> | Hosts/hosts | Sim |
> | imagens | Sim |
> | proximityPlacementGroups | Sim |
> | restorePointCollections | Sim |
> | restorePointCollections / restorePoints | Não |
> | sharedVMImages | Sim |
> | sharedVMImages/versões | Não |
> | instantâneos | Sim |
> | virtualMachines | Sim |
> | virtualMachines/extensões | Sim |
> | virtualMachines/metricDefinitions | Não |
> | virtualMachineScaleSets | Sim |
> | virtualMachineScaleSets/extensões | Não |
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
> | Cobranças | Não |
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
> | Etiquetas | Não |
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
> | registros/Builds/getLogLink | Não |
> | registros/buildTasks | Sim |
> | registros/buildTasks/etapas | Não |
> | registros/eventGridFilters | Não |
> | registros/generateCredentials | Não |
> | registros/getBuildSourceUploadUrl | Não |
> | registros/GetCredentials | Não |
> | registros/importImage | Não |
> | registros/queueBuild | Não |
> | registros/regenerateCredential | Não |
> | registros/regenerateCredentials | Não |
> | registros/replicações | Sim |
> | registros/execuções | Não |
> | registros/execuções/cancelamento | Não |
> | registros/scheduleRun | Não |
> | registros/scopeMaps | Não |
> | registros/taskRuns | Sim |
> | registros/tarefas | Sim |
> | registros/tokens | Não |
> | registros/updatePolicies | Não |
> | registros/WebHooks | Sim |
> | registros/WebHooks/getCallbackConfig | Não |
> | registros/WebHooks/ping | Não |

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
> | departamentos | Não |
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
> | registar | Não |
> | Reportconfigs | Não |
> | Relatórios | Não |
> | Definições | Não |
> | showbackRules | Não |
> | Vistas | Não |

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
> | sejam | Sim |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Sim |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | espaços de trabalho | Sim |
> | espaços de trabalho / dbWorkspaces | Não |
> | espaços de trabalho / armazenamentoEncriptação | Não |
> | espaços de trabalho/virtualNetworkPeerings | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | catálogos | Sim |
> | catálogos de | Sim |
> | datacatalogs/fontes de fonte | Não |
> | datacatalogs/fontes de pesquisa/verificações | Não |
> | datacatalogs/fontes/exames/conjuntos de valores | Não |
> | datacatalogs/fontes/exames/gatilhos | Não |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | dataFactories | Sim |
> | datafactorings/diagnosticSettings | Não |
> | datafactorings/metricDefinitions | Não |
> | dataFactorySchema | Não |
> | fábricas | Sim |
> | fábricas/integrationRuntimes | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | contas/dataLakeStoreAccounts | Não |
> | contas/storageAccounts | Não |
> | contas/storageAccounts/contêineres | Não |
> | contas/transferAnalyticsUnits | Não |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | contas/eventGridFilters | Não |
> | contas/firewallRules | Não |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | services | Sim |
> | serviços/projetos | Sim |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
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
> | servidores/chaves | Não |
> | servidores/privateEndpointConnectionProxies | Não |
> | servidores/privateEndpointConnections | Não |
> | servidores/privateLinkResources | Não |
> | servidores/queryTexts | Não |
> | servidores/recoverableServers | Não |
> | servidores/topQueryStatistics | Não |
> | servidores/virtualNetworkRules | Não |
> | servidores/waitStatistics | Não |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | servidores | Sim |
> | servidores/consultores | Não |
> | servidores/chaves | Não |
> | servidores/privateEndpointConnectionProxies | Não |
> | servidores/privateEndpointConnections | Não |
> | servidores/privateLinkResources | Não |
> | servidores/queryTexts | Não |
> | servidores/recoverableServers | Não |
> | servidores/topQueryStatistics | Não |
> | servidores/virtualNetworkRules | Não |
> | servidores/waitStatistics | Não |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | serverGroups | Sim |
> | servidores | Sim |
> | servidores/consultores | Não |
> | servidores/chaves | Não |
> | servidores/privateEndpointConnectionProxies | Não |
> | servidores/privateEndpointConnections | Não |
> | servidores/privateLinkResources | Não |
> | servidores/queryTexts | Não |
> | servidores/recoverableServers | Não |
> | servidores/topQueryStatistics | Não |
> | servidores/virtualNetworkRules | Não |
> | servidores/waitStatistics | Não |
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
> | applicationgroups/desktops | Não |
> | applicationgroups / startmenuitems | Não |
> | hostpools | Sim |
> | hostpools / sessionhosts | Não |
> | hostpools/sessionhosts/usersessions | Não |
> | hostpools/usersessions | Não |
> | espaços de trabalho | Sim |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | ElasticPools | Sim |
> | ElasticPools / IotHubTenants | Sim |
> | ElasticPools / IotHubTenants / securitySettings | Não |
> | IotHubs | Sim |
> | IotHubs/eventGridFilters | Não |
> | IotHubs / definições de segurança | Não |
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
> | laboratórios/perrunners | Sim |
> | laboratórios/virtualMachines | Sim |
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
> | domínios/domainOwnershipIdentifiers | Não |
> | generateSsoRequest | Não |
> | topLevelDomains | Não |
> | validateDomainRegistrationInformation | Não |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | lcsprojects | Não |
> | lcsprojects / clouddeployments | Não |
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
> | partnerNamespaces | Sim |
> | partnerNamespaces / eventoChannels | Não |
> | parceiroSRegistrations | Sim |
> | parceiroTópicos | Sim |
> | sistemaTópicos | Sim |
> | sistemaTópicos / eventosSubscrições | Não |
> | tópicos | Sim |
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
> | namespaces/Eventhubs/authorizationrules | Não |
> | namespaces/Eventhubs/consumergroups | Não |
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
> | inscrever-se | Não |
> | galleryitems | Não |
> | generateartifactaccessuri | Não |
> | minhas áreas | Não |
> | myddds/áreas | Não |
> | myddds/áreas/áreas | Não |
> | myddds/áreas/áreas/galleryitems | Não |
> | myddds/áreas/galleryitems | Não |
> | myareas/galleryitems | Não |
> | registar | Não |
> | recursos | Não |
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
> | autoGeridosAccounts | Sim |
> | autoGeridoVmConfigurationPerfis | Sim |
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
> | maquina | Sim |
> | máquinas/extensões | Sim |

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
> | sejam | Sim |

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
> | cofres/accessPolicies | Não |
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
> | clusters / bases de dados / principais atribuições | Não |
> | clusters / principados | Não |
> | clusters/sharedidentities | Não |

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
> | espaços de trabalho | Sim |
> | espaços de trabalho/computações | Não |
> | espaços de trabalho/eventGridFilters | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Identities | Não |
> | userAssignedIdentities | Sim |

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
> | accounts | Sim |
> | contas/eventGridFilters | Não |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | ofereça | Não |
> | offerTypes | Não |
> | offerTypes/Publicadores | Não |
> | offerTypes/Publicadores/ofertas | Não |
> | offerTypes/Publicadores/ofertas/planos | Não |
> | offerTypes/Publicadores/ofertas/planos/contratos | Não |
> | offerTypes/Publicadores/ofertas/planos/configurações | Não |
> | offerTypes/Publicadores/ofertas/planos/configurações/importImage | Não |
> | privategalleryitems | Não |
> | privateStoreClient | Não |
> | produtos | Não |
> | publicado | Não |
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
> | mediaservices/ativos/assetFilters | Não |
> | mediaservices/contentKeyPolicies | Não |
> | mediaservices/eventGridFilters | Não |
> | mediaservices/liveEventOperations | Não |
> | mediaservices/liveEvents | Sim |
> | mediaservices/liveEvents/liveOutputs | Não |
> | mediaservices/liveOutputOperations | Não |
> | mediaservices/mediaGraphs | Não |
> | mediaservices/streamingEndpointOperations | Não |
> | mediaservices/streamingEndpoints | Sim |
> | mediaservices/streamingLocators | Não |
> | mediaservices/streamingPolicies | Não |
> | mediaservices/transformações | Não |
> | mídia/transformações/trabalhos | Não |

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
> | moveCollections | Sim |
> | projeto | Sim |

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
> | conexões | Sim |
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
> | dnszones/conjuntos de registros | Não |
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
> | networkWatchers / connectionMonitors | Sim |
> | networkWatchers/lentes | Sim |
> | networkWatchers / pingMeshes | Sim |
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
> | trafficmanagerprofiles/calor | Não |
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

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

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
> | espaços de trabalho | Sim |
> | espaços de trabalho / dadosExportações | Não |
> | espaços de trabalho/fontes de fonte | Não |
> | espaços de trabalho/linksservices | Não |
> | espaços de trabalho / privateEndpointConnectionProxies | Não |
> | espaços de trabalho / privateEndpointConnections | Não |
> | espaços de trabalho / privateLinkResources | Não |
> | espaços de trabalho/consulta | Não |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | managementassociations | Não |
> | managementconfigurations | Sim |
> | soluções | Sim |
> | Modos de exibição | Sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | legacyPeerings | Não |
> | peerAsns | Não |
> | emparelhamentos | Sim |
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
> | correções | Não |

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
> | unidades | Sim |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |

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
> | accounts | Não |
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
> | questões emergentes | Não |
> | eventos | Não |
> | impactedResources | Não |
> | do IdP | Não |
> | notificações | Não |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | implantações | Não |
> | implantações/operações | Não |
> | deploymentScripts | Sim |
> | deploymentScripts/logs | Não |
> | Vincule | Não |
> | notifyResourceJobs | Não |
> | Fornecedores | Não |
> | resourceGroups | Não |
> | assinaturas | Não |
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
> | avaliações | Não |
> | autoDismissAlertsRules | Não |
> | automações | Sim |
> | AutoProvisioningSettings | Não |
> | Conformidades | Não |
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
> | políticas | Não |
> | preços | Não |
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
> | subavaliações | Não |
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
> | alertRuleTemplates | Não |
> | marcadores | Não |
> | bolsas | Não |
> | dataconnecters | Não |
> | contabilidade | Não |
> | entityQueries | Não |
> | officeConsents | Não |
> | settings | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | namespaces | Sim |
> | namespaces/authorizationrules | Não |
> | namespaces/disasterrecoveryconfigs | Não |
> | namespaces/eventgridfilters | Não |
> | namespaces/networkrulesets | Não |
> | namespaces/filas | Não |
> | namespaces/filas/authorizationrules | Não |
> | namespaces/tópicos | Não |
> | namespaces/tópicos/authorizationrules | Não |
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
> | confidenciais | Sim |
> | volumes | Sim |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | providerRegistrations | Não |
> | providerRegistrations / resourceTypeRegistrations | Não |
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
> | managedInstances/bancos de dados | Sim |
> | managedInstances/bancos de dados/backupShortTermRetentionPolicies | Não |
> | managedInstances/bancos de dados/esquemas/tabelas/colunas/sensitivityLabels | Não |
> | managedInstances/bancos de dados/vulnerabilityAssessments | Não |
> | managedInstances/bancos de dados/vulnerabilityAssessments/regras/linhas de base | Não |
> | managedInstances / encryptionProtector | Não |
> | managedInstances/chaves | Não |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Não |
> | managedInstances / vulnerabilityAssessments | Não |
> | servidores | Sim |
> | servidores/administradores | Não |
> | servidores/communicationLinks | Não |
> | servidores/bancos de dados | Sim |
> | servidores/encryptionProtector | Não |
> | servidores/firewallRules | Não |
> | servidores/chaves | Não |
> | servidores/restorableDroppedDatabases | Não |
> | servidores/preobjetivos | Não |
> | servidores/tdeCertificates | Não |
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
> | storageAccounts/blobservices | Não |
> | storageAccounts/fileservices | Não |
> | storageAccounts/queueservices | Não |
> | storageAccounts/serviços | Não |
> | storageAccounts/serviços/metricDefinitions | Não |
> | storageAccounts/tabelaservices | Não |
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
> | storageSyncServices / registeredServers | Não |
> | storageSyncServices / syncGroups | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não |
> | storageSyncServices/fluxos de trabalho | Não |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | Sim |
> | storageSyncServices / registeredServers | Não |
> | storageSyncServices / syncGroups | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não |
> | storageSyncServices/fluxos de trabalho | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | Sim |
> | storageSyncServices / registeredServers | Não |
> | storageSyncServices / syncGroups | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não |
> | storageSyncServices/fluxos de trabalho | Não |

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
> | Cancelar | Não |
> | CreateSubscription | Não |
> | permitir | Não |
> | renome | Não |
> | SubscriptionDefinitions | Não |
> | SubscriptionOperations | Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | sistemas | Sim |
> | ambientes/accessPolicies | Não |
> | ambientes/EventSources | Sim |
> | ambientes/referenceDataSets | Sim |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Sim |
> | dedicatedCloudServices | Sim |
> | virtualMachines | Sim |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | dispositivos | Sim |
> | fornecedores | Não |
> | fornecedores / skus | Não |
> | vnfs | Sim |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | apiManagementAccounts | Não |
> | apiManagementAccounts/apiAcls | Não |
> | apiManagementAccounts/APIs | Não |
> | apiManagementAccounts/APIs/apiAcls | Não |
> | apiManagementAccounts/APIs/connectionAcls | Não |
> | apiManagementAccounts/APIs/conexões | Não |
> | apiManagementAccounts/APIs/conexões/connectionAcls | Não |
> | apiManagementAccounts/APIs/localizedDefinitions | Não |
> | apiManagementAccounts/connectionAcls | Não |
> | apiManagementAccounts/conexões | Não |
> | billingMeters | Não |
> | certificados | Sim |
> | connectionGateways | Sim |
> | conexões | Sim |
> | customApis | Sim |
> | deletedSites | Não |
> | hostingEnvironments | Sim |
> | hospedagemAmbientes / eventoSGridFilters | Não |
> | hostingEnvironments/multiRolePools | Não |
> | hostingEnvironments/pools | Não |
> | publishingUsers | Não |
> | recomendações | Não |
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
> | sites/Slots/hostNameBindings | Não |
> | sites/Slots/networkConfig | Não |
> | sourceControls | Não |
> | sites estáticas | Sim |
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

## <a name="next-steps"></a>Passos seguintes

Para obter os mesmos dados como um arquivo de valores separados por vírgula, baixe [Complete-Mode-deletion. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
