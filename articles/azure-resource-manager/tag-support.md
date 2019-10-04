---
title: Suporte de marca de Azure Resource Manager para recursos
description: Mostra quais tipos de recursos do Azure dão suporte a marcas. Fornece detalhes para todos os serviços do Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: tomfitz
ms.openlocfilehash: 06028224379bd42e1e0ea21b684ce443abd5992a
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937007"
---
# <a name="tag-support-for-azure-resources"></a>Suporte a marcas para recursos do Azure
Este artigo descreve se um tipo de recurso dá suporte a [marcas](resource-group-using-tags.md). A coluna rotulada **dá suporte a marcas** indica se o tipo de recurso tem uma propriedade para a marca. A coluna rotulada como **marca no relatório de custo** indica se esse tipo de recurso passa a marca para o relatório de custo.

Para obter os mesmos dados como um arquivo de valores separados por vírgula, baixe [tag-support. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

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
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
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
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | DomainServices | Sim |Sim |
> | DomainServices/oucontainer | Não |Não |
> | DomainServices/ReplicaSets | Sim |Sim |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | supportProviders | Não |Não |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Não |Não |
> | adicionaservices | Não |Não |
> | representantes | Não |Não |
> | anonymousapiusers | Não |Não |
> | configuração | Não |Não |
> | logs | Não |Não |
> | relatórios | Não |Não |
> | servicehealthmetrics | Não |Não |
> | serviços | Não |Não |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Figura | Não |Não |
> | generateRecommendations | Não |Não |
> | do IdP | Não |Não |
> | Recomendações | Não |Não |
> | supressões | Não |Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | actionRules | Sim |Sim |
> | alerts | Não |Não |
> | alertas | Não |Não |
> | alertsMetaData | Não |Não |
> | alertsSummary | Não |Não |
> | alertsSummaryList | Não |Não |
> | Receber | Não |Não |
> | smartDetectorAlertRules | Sim |Sim |
> | smartDetectorRuntimeEnvironments | Não |Não |
> | smartGroups | Não |Não |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | servidores | Sim |Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Não |Não |
> | serviço | Sim |Sim |
> | validateServiceName | Não |Não |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | configurationStores | Sim |Sim |
> | configurationStores/eventGridFilters | Não |Não |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Primavera | Sim |Sim |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Não |Não |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | Não |Não |
> | Aliases de dataalias | Não |Não |
> | denyAssignments | Não |Não |
> | elevateAccess | Não |Não |
> | bloquea | Não |Não |
> | permissões | Não |Não |
> | policyAssignments | Não |Não |
> | policyDefinitions | Não |Não |
> | policySetDefinitions | Não |Não |
> | providerOperations | Não |Não |
> | roleAssignments | Não |Não |
> | roleDefinitions | Não |Não |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Sim |Sim |
> | automationAccounts/configurations | Sim |Sim |
> | automationAccounts/jobs | Não |Não |
> | automationAccounts/runbooks | Sim |Sim |
> | automationAccounts/softwareUpdateConfigurations | Não |Não |
> | automationAccounts/webhooks | Não |Não |

## <a name="microsoftazconfig"></a>Microsoft. Azconfig

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | configurationStores | Sim |Sim |
> | configurationStores/eventGridFilters | Não |Não |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | sistemas | Não |Não |
> | ambientes/contas | Não |Não |
> | environments/accounts/namespaces | Não |Não |
> | environments/accounts/namespaces/configurations | Não |Não |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Sim |Não |
> | b2ctenants | Não |Não |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | Sim |Sim |
> | postgresInstances | Sim |Sim |
> | sqlBigDataClusters | Sim |Sim |
> | SQLInstances | Sim |Sim |
> | sqlServerRegistrations | Sim |Sim |
> | sqlServerRegistrations/sqlservers | Não |Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | registros | Sim |Sim |
> | registrations/customerSubscriptions | Não |Não |
> | registros/produtos | Não |Não |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Sim |Sim |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Não |Não |
> | billingAccounts/contratos | Não |Não |
> | billingAccounts/billingPermissions | Não |Não |
> | billingAccounts/billingProfiles | Não |Não |
> | billingAccounts/billingProfiles/billingPermissions | Não |Não |
> | billingAccounts/billingProfiles/billingRoleAssignments | Não |Não |
> | billingAccounts/billingProfiles/billingRoleDefinitions | Não |Não |
> | billingAccounts/billingProfiles/billingSubscriptions | Não |Não |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | Não |Não |
> | billingAccounts/billingProfiles/clientes | Não |Não |
> | billingAccounts/billingProfiles/invoices | Não |Não |
> | billingAccounts/billingProfiles/invoices/pricesheet | Não |Não |
> | billingAccounts/billingProfiles/invoiceSections | Não |Não |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | Não |Não |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | Não |Não |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | Não |Não |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | Não |Não |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | Não |Não |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | Não |Não |
> | billingAccounts/billingProfiles/invoiceSections/produtos | Não |Não |
> | billingAccounts/billingProfiles/invoiceSections/produtos/transferência | Não |Não |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | Não |Não |
> | billingAccounts/billingProfiles/invoiceSections/transações | Não |Não |
> | billingAccounts/billingProfiles/invoiceSections/transferências | Não |Não |
> | billingAccounts/BillingProfiles/patchOperations | Não |Não |
> | billingAccounts/billingProfiles/paymentMethods | Não |Não |
> | billingAccounts/billingProfiles/políticas | Não |Não |
> | billingAccounts/billingProfiles/pricesheet | Não |Não |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | Não |Não |
> | billingAccounts/billingProfiles/products | Não |Não |
> | billingAccounts/billingProfiles/transactions | Não |Não |
> | billingAccounts/billingRoleAssignments | Não |Não |
> | billingAccounts/billingRoleDefinitions | Não |Não |
> | billingAccounts/billingSubscriptions | Não |Não |
> | billingAccounts/createBillingRoleAssignment | Não |Não |
> | billingAccounts/createInvoiceSectionOperations | Não |Não |
> | billingAccounts/clientes | Não |Não |
> | billingAccounts/clientes/billingSubscriptions | Não |Não |
> | billingAccounts/clientes/initiateTransfer | Não |Não |
> | billingAccounts/clientes/políticas | Não |Não |
> | billingAccounts/clientes/produtos | Não |Não |
> | billingAccounts/clientes/transações | Não |Não |
> | billingAccounts/clientes/transferências | Não |Não |
> | billingAccounts/departments | Não |Não |
> | billingAccounts/enrollmentAccounts | Não |Não |
> | billingAccounts/faturas | Não |Não |
> | billingAccounts/invoiceSections | Não |Não |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | Não |Não |
> | billingAccounts/invoiceSections/billingSubscriptions | Não |Não |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Não |Não |
> | billingAccounts/invoiceSections/Elevate | Não |Não |
> | billingAccounts/invoiceSections/initiateTransfer | Não |Não |
> | billingAccounts/invoiceSections/patchOperations | Não |Não |
> | billingAccounts/invoiceSections/productMoveOperations | Não |Não |
> | billingAccounts/invoiceSections/products | Não |Não |
> | billingAccounts/invoiceSections/produtos/transferência | Não |Não |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | Não |Não |
> | billingAccounts/invoiceSections/transações | Não |Não |
> | billingAccounts/invoiceSections/transfers | Não |Não |
> | billingAccounts/lineOfCredit | Não |Não |
> | billingAccounts/patchOperations | Não |Não |
> | billingAccounts/paymentMethods | Não |Não |
> | billingAccounts/produtos | Não |Não |
> | billingAccounts/transactions | Não |Não |
> | billingPeriods | Não |Não |
> | BillingPermissions | Não |Não |
> | billproperty | Não |Não |
> | BillingRoleAssignments | Não |Não |
> | BillingRoleDefinitions | Não |Não |
> | CreateBillingRoleAssignment | Não |Não |
> | departamentos | Não |Não |
> | enrollmentAccounts | Não |Não |
> | faturas | Não |Não |
> | transferir | Não |Não |
> | transfers/acceptTransfer | Não |Não |
> | transferências/declineTransfer | Não |Não |
> | transferências/operationStatus | Não |Não |
> | transferências/validateTransfer | Não |Não |
> | validateAddress | Não |Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | mapApis | Sim |Sim |
> | updateCommunicationPreference | Não |Não |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Sim |Sim |
> | observadores | Sim |Sim |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Não |Não |
> | blueprintAssignments/assignmentOperations | Não |Não |
> | blueprintAssignments/operations | Não |Não |
> | planos gráficos | Não |Não |
> | plantas/artefatos | Não |Não |
> | plantas/versões | Não |Não |
> | plantas/versões/artefatos | Não |Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | botServices | Sim |Sim |
> | botServices/channels | Não |Não |
> | botServices/connections | Não |Não |
> | Idiomas | Não |Não |
> | modelos | Não |Não |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Redis | Sim |Sim |
> | RedisConfigDefinition | Não |Não |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Não |Não |
> | calculateExchange | Não |Não |
> | calculatePrice | Não |Não |
> | calculatePurchasePrice | Não |Não |
> | catálogos | Não |Não |
> | commercialReservationOrders | Não |Não |
> | exchange | Não |Não |
> | placePurchaseOrder | Não |Não |
> | reservationOrders | Não |Não |
> | reservationOrders/calculateRefund | Não |Não |
> | reservationOrders/merge | Não |Não |
> | reservationOrders/reservations | Não |Não |
> | reservationOrders/reservations/revisions | Não |Não |
> | reservationOrders/return | Não |Não |
> | reservationOrders/split | Não |Não |
> | reservationOrders/swap | Não |Não |
> | reservas | Não |Não |
> | recursos | Não |Não |
> | validateReservationOrder | Não |Não |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Não |Não |
> | CdnWebApplicationFirewallPolicies | Sim |Sim |
> | edgenodes | Não |Não |
> | profiles | Sim |Sim |
> | perfis/pontos de extremidade | Sim |Sim |
> | perfis/pontos de extremidade/customdomains | Não |Não |
> | perfis/pontos de extremidade/origens | Não |Não |
> | validateProbe | Não |Não |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Sim |Sim |
> | certificateOrders/certificates | Não |Não |
> | validateCertificateRegistrationInformation | Não |Não |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Técnicas | Não |Não |
> | domainNames | Não |Não |
> | domainNames/capabilities | Não |Não |
> | domainNames/internalLoadBalancers | Não |Não |
> | domainNames/serviceCertificates | Não |Não |
> | domainNames/slots | Não |Não |
> | domainNames/slots/roles | Não |Não |
> | nome_do_domínio/Slots/funções/metricDefinitions | Não |Não |
> | DomainNames/Slots/funções/métricas | Não |Não |
> | moveSubscriptionResources | Não |Não |
> | operatingSystemFamilies | Não |Não |
> | operatingSystems | Não |Não |
> | quotas | Não |Não |
> | resourceType | Não |Não |
> | validateSubscriptionMoveAvailability | Não |Não |
> | VirtualMachines | Não |Não |
> | virtualMachines/diagnosticSettings | Não |Não |
> | virtualMachines/metricDefinitions | Não |Não |
> | virtualMachines/métricas | Não |Não |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Não |Não |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Técnicas | Não |Não |
> | expressRouteCrossConnections | Não |Não |
> | expressRouteCrossConnections/peerings | Não |Não |
> | gatewaySupportedDevices | Não |Não |
> | networkSecurityGroups | Não |Não |
> | quotas | Não |Não |
> | reservedIps | Não |Não |
> | virtualNetworks | Não |Não |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Não |Não |
> | virtualNetworks/virtualNetworkPeerings | Não |Não |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Técnicas | Não |Não |
> | discos | Não |Não |
> | imagens | Não |Não |
> | osImages | Não |Não |
> | osPlatformImages | Não |Não |
> | publicImages | Não |Não |
> | quotas | Não |Não |
> | storageAccounts | Não |Não |
> | storageAccounts/blobServices | Não |Não |
> | storageAccounts/fileServices | Não |Não |
> | storageAccounts/metricDefinitions | Não |Não |
> | storageAccounts/métricas | Não |Não |
> | storageAccounts/queueServices | Não |Não |
> | storageAccounts/services | Não |Não |
> | storageAccounts/services/diagnosticSettings | Não |Não |
> | storageAccounts/serviços/metricDefinitions | Não |Não |
> | storageAccounts/serviços/métricas | Não |Não |
> | storageAccounts/tableServices | Não |Não |
> | storageAccounts/vmImages | Não |Não |
> | vmImages | Não |Não |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | contas | Sim |Sim |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | RateCard | Não |Não |
> | UsageAggregates | Não |Não |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Sim |Sim |
> | diskEncryptionSets | Sim |Sim |
> | discos | Sim |Sim |
> | galerias | Sim |Sim |
> | galerias/aplicativos | Não |Não |
> | galerias/aplicativos/versões | Não |Não |
> | galerias/imagens | Não |Não |
> | galleries/images/versions | Não |Não |
> | Hosts | Sim |Sim |
> | Hosts/hosts | Sim |Sim |
> | imagens | Sim |Sim |
> | proximityPlacementGroups | Sim |Sim |
> | restorePointCollections | Sim |Sim |
> | restorePointCollections/restorePoints | Não |Não |
> | sharedVMImages | Sim |Sim |
> | sharedVMImages/versions | Não |Não |
> | instantâneos | Sim |Sim |
> | VirtualMachines | Sim |Sim |
> | virtualMachines/extensões | Sim |Sim |
> | virtualMachines/metricDefinitions | Não |Não |
> | virtualMachineScaleSets | Sim |Sim |
> | virtualMachineScaleSets/extensions | Não |Não |
> | virtualMachineScaleSets/networkInterfaces | Não |Não |
> | virtualMachineScaleSets/publicIPAddresses | Não |Não |
> | virtualMachineScaleSets/virtualMachines | Não |Não |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Não |Não |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Não |Não |
> | Saldos | Não |Não |
> | Orçamentos | Não |Não |
> | Encargos | Não |Não |
> | CostTags | Não |Não |
> | crédito | Não |Não |
> | eventos | Não |Não |
> | Previsões | Não |Não |
> | muitas | Não |Não |
> | Marketplaces | Não |Não |
> | Pricesheets | Não |Não |
> | produto | Não |Não |
> | ReservationDetails | Não |Não |
> | ReservationRecommendations | Não |Não |
> | ReservationSummaries | Não |Não |
> | ReservationTransactions | Não |Não |
> | Tags | Não |Não |
> | locatários | Não |Não |
> | Termos | Não |Não |
> | UsageDetails | Não |Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | containerGroups | Sim |Sim |
> | serviceAssociationLinks | Não |Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | registros | Sim |Sim |
> | registros/compilações | Não |Não |
> | registros/compilações/cancelar | Não |Não |
> | registries/builds/getLogLink | Não |Não |
> | registries/buildTasks | Sim |Sim |
> | registries/buildTasks/steps | Não |Não |
> | registries/eventGridFilters | Não |Não |
> | registries/getBuildSourceUploadUrl | Não |Não |
> | registros/GetCredentials | Não |Não |
> | registries/importImage | Não |Não |
> | registries/queueBuild | Não |Não |
> | registries/regenerateCredential | Não |Não |
> | registries/regenerateCredentials | Não |Não |
> | registros/replicações | Sim |Sim |
> | registros/execuções | Não |Não |
> | registros/execuções/cancelamento | Não |Não |
> | registries/scheduleRun | Não |Não |
> | registros/tarefas | Sim |Sim |
> | registries/updatePolicies | Não |Não |
> | registros/WebHooks | Sim |Sim |
> | registries/webhooks/getCallbackConfig | Não |Não |
> | registros/WebHooks/ping | Não |Não |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | containerServices | Sim |Sim |
> | managedClusters | Sim |Sim |
> | openShiftManagedClusters | Sim |Sim |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | aplicações | Sim |Sim |
> | updateCommunicationPreference | Não |Não |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | contas | Sim |Sim |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Alertas | Não |Não |
> | billingAccounts | Não |Não |
> | Orçamentos | Não |Não |
> | CloudConnectors | Não |Não |
> | Conectores | Sim |Sim |
> | Departamentos | Não |Não |
> | Dimensões | Não |Não |
> | enrollmentAccounts | Não |Não |
> | Exportações | Não |Não |
> | ExternalBillingAccounts | Não |Não |
> | ExternalBillingAccounts/alertas | Não |Não |
> | ExternalBillingAccounts/dimensões | Não |Não |
> | ExternalBillingAccounts/previsão | Não |Não |
> | ExternalBillingAccounts/consulta | Não |Não |
> | ExternalSubscriptions | Não |Não |
> | ExternalSubscriptions/alertas | Não |Não |
> | ExternalSubscriptions/dimensões | Não |Não |
> | ExternalSubscriptions/previsão | Não |Não |
> | ExternalSubscriptions/consulta | Não |Não |
> | Previsão | Não |Não |
> | Consulta | Não |Não |
> | Registr | Não |Não |
> | Reportconfigs | Não |Não |
> | Relatórios | Não |Não |
> | Definições | Não |Não |
> | showbackRules | Não |Não |
> | Vistas | Não |Não |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | pedidos | Não |Não |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | SAS | Não |Não |
> | resourceProviders | Sim |Sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | tarefas | Sim |Sim |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Sim |Sim |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | áreas de trabalho | Sim |Não |
> | workspaces/virtualNetworkPeerings | Não |Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | catálogos | Sim |Sim |
> | catálogos de | Sim |Sim |
> | datacatalogs/fontes de fonte | Não |Não |
> | datacatalogs/fontes de pesquisa/verificações | Não |Não |
> | datacatalogs/fontes/exames/conjuntos de valores | Não |Não |
> | datacatalogs/fontes/exames/gatilhos | Não |Não |
> | datacatalogs/scantargets | Não |Não |
> | datacatalogs/scantargets/DataSets | Não |Não |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | dataFactories | Sim |Não |
> | dataFactories/diagnosticSettings | Não |Não |
> | datafactorings/metricDefinitions | Não |Não |
> | dataFactorySchema | Não |Não |
> | fábricas | Sim |Não |
> | factories/integrationRuntimes | Não |Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | contas | Sim |Sim |
> | accounts/dataLakeStoreAccounts | Não |Não |
> | accounts/storageAccounts | Não |Não |
> | accounts/storageAccounts/containers | Não |Não |
> | contas/transferAnalyticsUnits | Não |Não |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | contas | Sim |Sim |
> | accounts/eventGridFilters | Não |Não |
> | accounts/firewallRules | Não |Não |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | serviços | Não |Não |
> | services/projects | Não |Não |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | contas | Sim |Sim |
> | contas/compartilhamentos | Não |Não |
> | contas/compartilhamentos/conjuntos de valores | Não |Não |
> | contas/compartilhamentos/convites | Não |Não |
> | contas/compartilhamentos/providersharesubscriptions | Não |Não |
> | contas/compartilhamentos/synchronizationSettings | Não |Não |
> | contas/sharesubscriptions | Não |Não |
> | contas/sharesubscriptions/consumerSourceDataSets | Não |Não |
> | contas/sharesubscriptions/datasetmappings | Não |Não |
> | contas/sharesubscriptions/gatilhos | Não |Não |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | servidores | Sim |Sim |
> | servidores/consultores | Não |Não |
> | servers/queryTexts | Não |Não |
> | servers/recoverableServers | Não |Não |
> | servers/topQueryStatistics | Não |Não |
> | servers/virtualNetworkRules | Não |Não |
> | servers/waitStatistics | Não |Não |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | servidores | Sim |Sim |
> | servidores/consultores | Não |Não |
> | servers/queryTexts | Não |Não |
> | servers/recoverableServers | Não |Não |
> | servers/topQueryStatistics | Não |Não |
> | servers/virtualNetworkRules | Não |Não |
> | servers/waitStatistics | Não |Não |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | serverGroups | Sim |Sim |
> | servidores | Sim |Sim |
> | servidores/consultores | Não |Não |
> | servidores/privateEndpointConnectionProxies | Não |Não |
> | servidores/privateEndpointConnections | Não |Não |
> | servidores/privateLinkResources | Não |Não |
> | servers/queryTexts | Não |Não |
> | servers/recoverableServers | Não |Não |
> | servers/topQueryStatistics | Não |Não |
> | servers/virtualNetworkRules | Não |Não |
> | servers/waitStatistics | Não |Não |
> | serversv2 | Sim |Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | artifactSources | Sim |Sim |
> | distribuições | Sim |Sim |
> | pertopologias | Sim |Sim |
> | serviços e pertopologias | Sim |Sim |
> | pertopologias/serviços/unidades de serviço | Sim |Sim |
> | passos | Sim |Sim |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Sim |Sim |
> | applicationgroups/aplicativos | Não |Não |
> | applicationgroups/startmenuitems | Não |Não |
> | hostpools | Sim |Sim |
> | hostpools/sessionhosts | Não |Não |
> | hostpools/sessionhosts/usersessions | Não |Não |
> | hostpools/usersessions | Não |Não |
> | áreas de trabalho | Sim |Sim |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | elasticPools | Sim |Sim |
> | ElasticPools/IotHubTenants | Sim |Sim |
> | IotHubs | Sim |Sim |
> | IotHubs/eventGridFilters | Não |Não |
> | ProvisioningServices | Sim |Sim |
> | usos | Não |Não |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | pipelines | Sim |Sim |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Controladores | Sim |Sim |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | labcenters | Sim |Sim |
> | prático | Sim |Sim |
> | laboratórios/ambientes | Sim |Sim |
> | labs/serviceRunners | Sim |Sim |
> | labs/virtualMachines | Sim |Sim |
> | agendamento | Sim |Sim |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Não |Não |
> | databaseAccounts | Sim |Sim |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | domínios | Sim |Sim |
> | domains/domainOwnershipIdentifiers | Não |Não |
> | generateSsoRequest | Não |Não |
> | topLevelDomains | Não |Não |
> | validateDomainRegistrationInformation | Não |Não |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Não |Não |
> | lcsprojects/clouddeployments | Não |Não |
> | lcsprojects/conectores | Não |Não |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | serviços | Sim |Sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | domínios | Sim |Sim |
> | domínios/tópicos | Não |Não |
> | eventSubscriptions | Não |Não |
> | extensionTopics | Não |Não |
> | Tópicos | Sim |Sim |
> | topicTypes | Não |Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | clusters | Sim |Sim |
> | espaços de nomes | Sim |Sim |
> | namespaces/authorizationrules | Não |Não |
> | namespaces/disasterrecoveryconfigs | Não |Não |
> | namespaces/Eventhubs | Não |Não |
> | namespaces/eventhubs/authorizationrules | Não |Não |
> | namespaces/eventhubs/consumergroups | Não |Não |
> | namespaces/networkrulesets | Não |Não |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | elástica | Não |Não |
> | Fornecedores | Não |Não |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Registr | Não |Não |
> | galleryitems | Não |Não |
> | generateartifactaccessuri | Não |Não |
> | minhas áreas | Não |Não |
> | myddds/áreas | Não |Não |
> | myddds/áreas/áreas | Não |Não |
> | myareas/areas/areas/galleryitems | Não |Não |
> | myareas/areas/galleryitems | Não |Não |
> | myareas/galleryitems | Não |Não |
> | Registr | Não |Não |
> | recursos | Não |Não |
> | retrieveresourcesbyid | Não |Não |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | contas | Sim |Sim |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | autoManagedVmConfigurationProfiles | Sim |Sim |
> | guestConfigurationAssignments | Não |Não |
> | Antivírus | Não |Não |
> | softwareUpdateProfile | Não |Não |
> | softwareUpdates | Não |Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Sim |Sim |
> | sapMonitors | Sim |Sim |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | Sim |Sim |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | clusters | Sim |Sim |
> | clusters/aplicativos | Não |Não |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | serviços | Sim |Sim |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | computadores | Sim |Sim |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Gerenciadores de DataManager | Sim |Sim |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | QC | Sim |Sim |
> | networkScopes | Sim |Sim |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | tarefas | Sim |Sim |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Não |Não |
> | diagnosticSettingsCategories | Não |Não |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | appTemplates | Não |Não |
> | IoTApps | Sim |Sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Graph | Sim |Sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Não |Não |
> | hsmPools | Sim |Sim |
> | cofres | Sim |Sim |
> | vaults/accessPolicies | Não |Não |
> | cofres/eventGridFilters | Não |Não |
> | cofres/segredos | Não |Não |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | clusters | Sim |Sim |
> | clusters/attacheddatabaseconfigurations | Não |Não |
> | clusters/bancos de dados | Não |Não |
> | clusters/bancos de dados/conexões DataConnections | Não |Não |
> | clusters/bancos de dados/eventhubconnections | Não |Não |
> | clusters/sharedidentities | Não |Não |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | labaccounts | Sim |Sim |
> | Utilizadores | Não |Não |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Sim |Sim |
> | integrationAccounts | Sim |Sim |
> | integrationServiceEnvironments | Sim |Sim |
> | integrationServiceEnvironments/managedApis | Sim |Sim |
> | isolatedEnvironments | Sim |Sim |
> | fluxos | Sim |Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Sim |Sim |
> | webServices | Sim |Sim |
> | Áreas de trabalho | Sim |Sim |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | áreas de trabalho | Sim |Sim |
> | espaços de trabalho/computações | Não |Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Identities | Não |Não |
> | userAssignedIdentities | Sim |Sim |

## <a name="microsoftmanagedservices"></a>Microsoft. Managedservices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Não |Não |
> | registrationAssignments | Não |Não |
> | registrationDefinitions | Não |Não |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | getEntities | Não |Não |
> | managementGroups | Não |Não |
> | recursos | Não |Não |
> | startTenantBackfill | Não |Não |
> | tenantBackfillStatus | Não |Não |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | contas | Sim |Sim |
> | accounts/eventGridFilters | Não |Não |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | ofertas | Não |Não |
> | offerTypes | Não |Não |
> | offerTypes/Publicadores | Não |Não |
> | offerTypes/publishers/offers | Não |Não |
> | offerTypes/publishers/offers/plans | Não |Não |
> | offerTypes/publishers/offers/plans/agreements | Não |Não |
> | offerTypes/publishers/offers/plans/configs | Não |Não |
> | offerTypes/publishers/offers/plans/configs/importImage | Não |Não |
> | privategalleryitems | Não |Não |
> | produto | Não |Não |
> | publicadores | Não |Não |
> | editores/ofertas | Não |Não |
> | editores/ofertas/emendas | Não |Não |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Sim |Sim |
> | updateCommunicationPreference | Não |Não |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | licenças | Não |Não |
> | offertypes | Não |Não |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | mediaservices | Sim |Sim |
> | mediaservices/accountFilters | Não |Não |
> | mediaservices/ativos | Não |Não |
> | mediaservices/assets/assetFilters | Não |Não |
> | mediaservices/contentKeyPolicies | Não |Não |
> | mediaservices/eventGridFilters | Não |Não |
> | mediaservices/liveEventOperations | Não |Não |
> | mediaservices/liveEvents | Sim |Sim |
> | mediaservices/liveEvents/liveOutputs | Não |Não |
> | mediaservices/liveOutputOperations | Não |Não |
> | mediaservices/streamingEndpointOperations | Não |Não |
> | mediaservices/streamingEndpoints | Sim |Sim |
> | mediaservices/streamingLocators | Não |Não |
> | mediaservices/streamingPolicies | Não |Não |
> | mediaservices/transformações | Não |Não |
> | mediaservices/transforms/jobs | Não |Não |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | appClusters | Sim |Sim |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Sim |Sim |
> | migrateprojects | Sim |Sim |
> | projeto | Sim |Sim |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Sim |Sim |
> | objectUnderstandingAccounts | Sim |Sim |
> | remoteRenderingAccounts | Sim |Sim |
> | spatialAnchorsAccounts | Sim |Sim |
> | surfaceReconstructionAccounts | Sim |Sim |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Sim |Sim |
> | netAppAccounts/backupPolicies | Sim |Sim |
> | netAppAccounts/capacityPools | Sim |Sim |
> | netAppAccounts/capacityPools/volumes | Sim |Sim |
> | netAppAccounts/capacityPools/volumes/backups | Não |Não |
> | netAppAccounts/capacityPools/volumes/mountTargets | Sim |Sim |
> | netAppAccounts/capacityPools/volumes/instantâneos | Sim |Sim |
> | netAppAccounts/cofres | Não |Não |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Sim |Sim |
> | applicationGatewayWebApplicationFirewallPolicies | Sim |Sim |
> | applicationSecurityGroups | Sim |Sim |
> | azureFirewallFqdnTags | Não |Não |
> | azureFirewalls | Sim |Sim |
> | bastionHosts | Sim |Sim |
> | bgpServiceCommunities | Não |Não |
> | ligações | Sim |Sim |
> | ddosCustomPolicies | Sim |Sim |
> | ddosProtectionPlans | Sim |Sim |
> | dnsOperationStatuses | Não |Não |
> | dnszones | Sim |Sim |
> | dnszones/A | Não |Não |
> | dnszones/AAAA | Não |Não |
> | dnszones/tudo | Não |Não |
> | dnszones/CAA | Não |Não |
> | dnszones/CNAME | Não |Não |
> | dnszones/MX | Não |Não |
> | dnszones/NS | Não |Não |
> | dnszones/PTR | Não |Não |
> | dnszones/recordsets | Não |Não |
> | dnszones/SOA | Não |Não |
> | dnszones/SRV | Não |Não |
> | dnszones/TXT | Não |Não |
> | expressRouteCircuits | Sim |Sim |
> | expressRouteCrossConnections | Sim |Sim |
> | expressRouteGateways | Sim |Sim |
> | expressRoutePorts | Sim |Sim |
> | expressRouteServiceProviders | Não |Não |
> | firewallPolicies | Sim |Sim |
> | frontdoors | Sim, mas limitado (veja a [Observação abaixo](#frontdoor)) |Sim |
> | frontdoorWebApplicationFirewallManagedRuleSets | Sim, mas limitado (veja a [Observação abaixo](#frontdoor)) |Não |
> | frontdoorWebApplicationFirewallPolicies | Sim, mas limitado (veja a [Observação abaixo](#frontdoor)) |Sim |
> | getDnsResourceReference | Não |Não |
> | internalNotify | Não |Não |
> | loadBalancers | Sim |Não |
> | localNetworkGateways | Sim |Sim |
> | natGateways | Sim |Sim |
> | networkIntentPolicies | Sim |Sim |
> | networkInterfaces | Sim |Sim |
> | networkProfiles | Sim |Sim |
> | networkSecurityGroups | Sim |Sim |
> | networkWatchers | Sim |Não |
> | networkWatchers/connectionMonitors | Sim |Não |
> | networkWatchers/lenses | Sim |Não |
> | networkWatchers/pingMeshes | Sim |Não |
> | p2sVpnGateways | Sim |Sim |
> | privateDnsOperationStatuses | Não |Não |
> | privateDnsZones | Sim |Sim |
> | privateDnsZones/A | Não |Não |
> | privateDnsZones/AAAA | Não |Não |
> | privateDnsZones/tudo | Não |Não |
> | privateDnsZones/CNAME | Não |Não |
> | privateDnsZones/MX | Não |Não |
> | privateDnsZones/PTR | Não |Não |
> | privateDnsZones/SOA | Não |Não |
> | privateDnsZones/SRV | Não |Não |
> | privateDnsZones/TXT | Não |Não |
> | privateDnsZones/virtualNetworkLinks | Sim |Sim |
> | privateEndpoints | Sim |Sim |
> | privateLinkServices | Sim |Sim |
> | publicIPAddresses | Sim |Sim |
> | publicIPPrefixes | Sim |Sim |
> | routeFilters | Sim |Sim |
> | routeTables | Sim |Sim |
> | secureGateways | Sim |Sim |
> | serviceEndpointPolicies | Sim |Sim |
> | trafficManagerGeographicHierarchies | Não |Não |
> | trafficmanagerprofiles | Sim |Sim |
> | trafficmanagerprofiles/heatMaps | Não |Não |
> | trafficManagerUserMetricsKeys | Não |Não |
> | virtualHubs | Sim |Sim |
> | virtualNetworkGateways | Sim |Sim |
> | virtualNetworks | Sim |Sim |
> | virtualNetworkTaps | Sim |Sim |
> | virtualWans | Sim |Sim |
> | vpnGateways | Sim |Não |
> | vpnSites | Sim |Sim |
> | webApplicationFirewallPolicies | Sim |Sim |

<a id="frontdoor" />

> [!NOTE]
> Para o serviço de porta frontal do Azure, você pode aplicar marcas ao criar o recurso, mas a atualização ou adição de marcas não tem suporte no momento.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | espaços de nomes | Sim |Não |
> | namespaces/notificationHubs | Sim |Não |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Sim |Sim |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Sim |Sim |
> | ImportSites | Sim |Sim |
> | ServerSites | Sim |Sim |
> | VMwareSites | Sim |Sim |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | clusters | Sim |Sim |
> | dispositivos | Não |Não |
> | linkTargets | Não |Não |
> | storageInsightConfigs | Não |Não |
> | áreas de trabalho | Sim |Sim |
> | workspaces/dataSources | Não |Não |
> | workspaces/linkedServices | Não |Não |
> | espaços de trabalho/consulta | Não |Não |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | managementassociations | Não |Não |
> | managementconfigurations | Sim |Sim |
> | soluções | Sim |Sim |
> | visualizações | Sim |Sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Não |Não |
> | peerAsns | Não |Não |
> | peerings | Sim |Sim |
> | peeringServiceProviders | Não |Não |
> | peeringServices | Sim |Sim |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | policyEvents | Não |Não |
> | policyStates | Não |Não |
> | policyTrackedResources | Não |Não |
> | correções | Não |Não |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | consoles | Não |Não |
> | Painéis | Sim |Sim |
> | UserSettings | Não |Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | Sim |Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | unidades | Sim |Sim |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Não |Não |
> | cofres | Sim |Sim |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | espaços de nomes | Sim |Sim |
> | namespaces/authorizationrules | Não |Não |
> | namespaces/hybridconnections | Não |Não |
> | namespaces/hybridconnections/authorizationrules | Não |Não |
> | namespaces/wcfrelays | Não |Não |
> | namespaces/wcfrelays/authorizationrules | Não |Não |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | contas | Não |Não |
> | Colec | Sim |Sim |
> | coleções/aplicativos | Não |Não |
> | coleções/SecurityPrincipals | Não |Não |
> | templateImages | Não |Não |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | consultas de | Sim |Sim |
> | resourceChangeDetails | Não |Não |
> | resourceChanges | Não |Não |
> | recursos | Não |Não |
> | resourcesHistory | Não |Não |
> | subscriptionsStatus | Não |Não |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Não |Não |
> | childAvailabilityStatuses | Não |Não |
> | childResources | Não |Não |
> | eventos | Não |Não |
> | impactedResources | Não |Não |
> | do IdP | Não |Não |
> | notificações | Não |Não |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | implementações | Sim |Não |
> | implantações/operações | Não |Não |
> | ligações | Não |Não |
> | notifyResourceJobs | Não |Não |
> | Fornecedores | Não |Não |
> | resourceGroups | Sim |Não |
> | recursos | Não |Não |
> | subscrições | Não |Não |
> | assinaturas/provedores | Não |Não |
> | subscriptions/resourceGroups | Não |Não |
> | subscriptions/resourcegroups/resources | Não |Não |
> | assinaturas/recursos | Não |Não |
> | assinaturas/TagNames | Não |Não |
> | subscriptions/tagNames/tagValues | Não |Não |
> | locatários | Não |Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | aplicações | Sim |Sim |
> | saasresources | Não |Não |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | gratuitas | Sim |Sim |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Não |Não |
> | searchServices | Sim |Sim |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Não |Não |
> | advancedThreatProtectionSettings | Não |Não |
> | alerts | Não |Não |
> | allowedConnections | Não |Não |
> | applicationWhitelistings | Não |Não |
> | assessmentMetadata | Não |Não |
> | Avaliações | Não |Não |
> | AutoProvisioningSettings | Não |Não |
> | Conformidades | Não |Não |
> | dataCollectionAgents | Não |Não |
> | deviceSecurityGroups | Não |Não |
> | discoveredSecuritySolutions | Não |Não |
> | externalSecuritySolutions | Não |Não |
> | InformationProtectionPolicies | Não |Não |
> | iotSecuritySolutions | Sim |Sim |
> | iotSecuritySolutions/analyticsModels | Não |Não |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | Não |Não |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | Não |Não |
> | jitNetworkAccessPolicies | Não |Não |
> | networkData | Não |Não |
> | playbookConfigurations | Sim |Sim |
> | políticas | Não |Não |
> | preços | Não |Não |
> | regulatoryComplianceStandards | Não |Não |
> | regulatoryComplianceStandards/regulatoryComplianceControls | Não |Não |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | Não |Não |
> | securityContacts | Não |Não |
> | securitySolutions | Não |Não |
> | securitySolutionsReferenceData | Não |Não |
> | securityStatuses | Não |Não |
> | securityStatusesSummaries | Não |Não |
> | serverVulnerabilityAssessments | Não |Não |
> | definições | Não |Não |
> | subavaliações | Não |Não |
> | tarefas | Não |Não |
> | topologias | Não |Não |
> | workspaceSettings | Não |Não |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Não |Não |
> | diagnosticSettingsCategories | Não |Não |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | agregações | Não |Não |
> | alertRules | Não |Não |
> | alertRuleTemplates | Não |Não |
> | Indicadores | Não |Não |
> | bolsas | Não |Não |
> | dataconnecters | Não |Não |
> | Contabilidade | Não |Não |
> | entityQueries | Não |Não |
> | officeConsents | Não |Não |
> | definições | Não |Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | espaços de nomes | Sim |Não |
> | namespaces/authorizationrules | Não |Não |
> | namespaces/disasterrecoveryconfigs | Não |Não |
> | namespaces/eventgridfilters | Não |Não |
> | namespaces/networkrulesets | Não |Não |
> | namespaces/queues | Não |Não |
> | namespaces/queues/authorizationrules | Não |Não |
> | namespaces/topics | Não |Não |
> | namespaces/topics/authorizationrules | Não |Não |
> | namespaces/tópicos/assinaturas | Não |Não |
> | namespaces/tópicos/assinaturas/regras | Não |Não |
> | premiumMessagingRegions | Não |Não |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | aplicações | Sim |Sim |
> | clusters | Sim |Sim |
> | clusters/aplicativos | Não |Não |
> | containerGroups | Sim |Sim |
> | containerGroupSets | Sim |Sim |
> | edgeclusters | Sim |Sim |
> | edgeclusters/aplicativos | Não |Não |
> | às | Sim |Sim |
> | secretstores | Sim |Sim |
> | secretstores/certificados | Não |Não |
> | secretstores/segredos | Não |Não |
> | volumes | Sim |Sim |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | aplicações | Sim |Sim |
> | containerGroups | Sim |Sim |
> | gateways | Sim |Sim |
> | às | Sim |Sim |
> | segredos | Sim |Sim |
> | volumes | Sim |Sim |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Não |Não |
> | providerRegistrations/resourceTypeRegistrations | Não |Não |
> | distribuições | Sim |Sim |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | SignalR | Sim |Sim |
> | Sinalização/eventGridFilters | Não |Não |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Sim |Sim |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Não |Não |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Sim |Sim |
> | aplicações | Sim |Sim |
> | jitRequests | Sim |Sim |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | managedInstances | Sim | Sim |
> | managedInstances/databases | Sim (veja a [Observação abaixo](#sqlnote)) | Sim |
> | managedInstances/databases/backupShortTermRetentionPolicies | Não | Não |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | Não | Não |
> | managedInstances/databases/vulnerabilityAssessments | Não | Não |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | Não | Não |
> | managedInstances/encryptionProtector | Não | Não |
> | managedInstances/keys | Não | Não |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Não | Não |
> | managedInstances/vulnerabilityAssessments | Não | Não |
> | servidores | Sim | Sim |
> | servidores/administradores | Não | Não |
> | servers/communicationLinks | Não | Não |
> | servers/databases | Sim (veja a [Observação abaixo](#sqlnote)) | Sim |
> | servers/encryptionProtector | Não | Não |
> | servers/firewallRules | Não | Não |
> | servers/keys | Não | Não |
> | servers/restorableDroppedDatabases | Não | Não |
> | servidores/preobjetivos | Não | Não |
> | servers/tdeCertificates | Não | Não |
> | virtualClusters | Não | Não |

<a id="sqlnote" />

> [!NOTE]
> O banco de dados mestre não dá suporte a marcas, mas outros bancos de dados, incluindo bancos de dados do Azure SQL Data Warehouse, marcas de suporte. Os bancos de dados do Azure SQL Data Warehouse devem estar no estado ativo (não pausado).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Sim |Sim |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Não |Não |
> | SqlVirtualMachines | Sim |Sim |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Sim |Sim |
> | storageAccounts/blobServices | Não |Não |
> | storageAccounts/fileServices | Não |Não |
> | storageAccounts/queueServices | Não |Não |
> | storageAccounts/services | Não |Não |
> | storageAccounts/serviços/metricDefinitions | Não |Não |
> | storageAccounts/tableServices | Não |Não |
> | usos | Não |Não |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | caches | Sim |Sim |
> | caches/storageTargets | Não |Não |
> | usageModels | Não |Não |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Não |Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sim |Sim |
> | storageSyncServices/registeredServers | Não |Não |
> | storageSyncServices/syncGroups | Não |Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não |Não |
> | storageSyncServices/syncGroups/serverEndpoints | Não |Não |
> | storageSyncServices/workflows | Não |Não |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sim |Sim |
> | storageSyncServices/registeredServers | Não |Não |
> | storageSyncServices/syncGroups | Não |Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não |Não |
> | storageSyncServices/syncGroups/serverEndpoints | Não |Não |
> | storageSyncServices/workflows | Não |Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sim |Sim |
> | storageSyncServices/registeredServers | Não |Não |
> | storageSyncServices/syncGroups | Não |Não |
> | storageSyncServices/syncGroups/cloudEndpoints | Não |Não |
> | storageSyncServices/syncGroups/serverEndpoints | Não |Não |
> | storageSyncServices/workflows | Não |Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | esses | Sim |Sim |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Sim (veja a observação abaixo) |Sim |

> [!NOTE]
> Você não pode adicionar uma marca quando streamingjobs está em execução. Interrompa o recurso para adicionar uma marca.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | cancelar | Não |Não |
> | CreateSubscription | Não |Não |
> | ativar | Não |Não |
> | mudar o nome | Não |Não |
> | SubscriptionDefinitions | Não |Não |
> | SubscriptionOperations | Não |Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | sistemas | Sim |Não |
> | environments/accessPolicies | Não |Não |
> | ambientes/EventSources | Sim |Não |
> | environments/referenceDataSets | Sim |Não |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Sim |Sim |
> | dedicatedCloudServices | Sim |Sim |
> | VirtualMachines | Sim |Sim |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Não |Não |
> | apiManagementAccounts/apiAcls | Não |Não |
> | apiManagementAccounts/apis | Não |Não |
> | apiManagementAccounts/apis/apiAcls | Não |Não |
> | apiManagementAccounts/apis/connectionAcls | Não |Não |
> | apiManagementAccounts/apis/connections | Não |Não |
> | apiManagementAccounts/apis/connections/connectionAcls | Não |Não |
> | apiManagementAccounts/apis/localizedDefinitions | Não |Não |
> | apiManagementAccounts/connectionAcls | Não |Não |
> | apiManagementAccounts/connections | Não |Não |
> | billingMeters | Não |Não |
> | certificados | Sim |Sim |
> | connectionGateways | Sim |Sim |
> | ligações | Sim |Sim |
> | customApis | Sim |Sim |
> | deletedSites | Não |Não |
> | functions | Não |Não |
> | hostingEnvironments | Sim |Sim |
> | hostingEnvironments/multiRolePools | Não |Não |
> | hostingEnvironments/workerPools | Não |Não |
> | publishingUsers | Não |Não |
> | Recomendações | Não |Não |
> | resourceHealthMetadata | Não |Não |
> | tempos | Não |Não |
> | serverFarms | Sim |Sim |
> | serverFarms/eventGridFilters | Não |Não |
> | sites | Sim |Sim |
> | sites/configuração  | Não | Não |
> | sites/eventGridFilters | Não |Não |
> | sites/hostNameBindings | Não |Não |
> | sites/networkConfig | Não |Não |
> | sites/premieraddons | Sim |Sim |
> | sites/Slots | Sim |Sim |
> | sites/Slots/eventGridFilters | Não |Não |
> | sites/slots/hostNameBindings | Não |Não |
> | sites/Slots/networkConfig | Não |Não |
> | sourceControls | Não |Não |
> | verifica | Não |Não |
> | verifyHostingEnvironmentVnet | Não |Não |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Não |Não |
> | diagnosticSettingsCategories | Não |Não |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Dispositivos | Sim |Sim |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | QC | Não |Não |
> | componentsSummary | Não |Não |
> | monitorInstances | Não |Não |
> | monitorInstancesSummary | Não |Não |
> | monitores | Não |Não |
> | notificationSettings | Não |Não |

## <a name="next-steps"></a>Passos seguintes

Para saber como aplicar marcas a recursos, consulte [usar marcas para organizar os recursos do Azure](resource-group-using-tags.md).
