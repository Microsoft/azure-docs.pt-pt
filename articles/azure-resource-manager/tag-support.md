---
title: Suporte de marca de Azure Resource Manager para recursos
description: Mostra quais tipos de recursos do Azure dão suporte a marcas. Fornece detalhes para todos os serviços do Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 06/07/2019
ms.author: tomfitz
ms.openlocfilehash: 72bb11cd064c90c2bbe1e9e6452dcbf07fe37817
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304873"
---
# <a name="tag-support-for-azure-resources"></a>Suporte a marcas para recursos do Azure
Este artigo descreve se um tipo de recurso dá suporte a [marcas](resource-group-using-tags.md). A coluna rotulada **dá suporte a marcas** indica se o tipo de recurso tem uma propriedade para a marca. A coluna rotulada como **marca no relatório de custo** indica se esse tipo de recurso passa a marca para o relatório de custo.

Para obter os mesmos dados como um arquivo de valores separados por vírgula, baixe [tag-support. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| DomainServices | Sim | Sim |
| DomainServices/oucontainer | Não | Não |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Não |  Não |
| diagnosticSettingsCategories | Não |  Não |

## <a name="microsoftaddons"></a>Microsoft.Addons
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| supportProviders | Não |  Não |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| aadsupportcases | Não |  Não |
| adicionaservices | Não |  Não |
| representantes | Não |  Não |
| anonymousapiusers | Não |  Não |
| configuração | Não |  Não |
| logs | Não |  Não |
| relatórios | Não |  Não |
| services | Não |  Não |

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Figura | Não |  Não |
| generateRecommendations | Não |  Não |
| Recommendations | Não |  Não |
| supressões | Não |  Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| actionRules | Não |  Não |
| alerts | Não |  Não |
| alertas | Não |  Não |
| alertsSummary | Não |  Não |
| alertsSummaryList | Não |  Não |
| smartDetectorAlertRules | Não |  Não |
| smartDetectorRuntimeEnvironments | Não |  Não |
| smartGroups | Não |  Não |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Servidores | Sim | Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| reportFeedback | Não |  Não |
| serviço | Sim | Sim |
| validateServiceName | Não |  Não |

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| attestationProviders | Não |  Não |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| classicAdministrators | Não |  Não |
| denyAssignments | Não |  Não |
| elevateAccess | Não |  Não |
| bloquea | Não |  Não |
| permissões | Não |  Não |
| policyAssignments | Não |  Não |
| policyDefinitions | Não |  Não |
| policySetDefinitions | Não |  Não |
| providerOperations | Não |  Não |
| roleAssignments | Não |  Não |
| roleDefinitions | Não |  Não |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| automationAccounts | Sim | Sim |
| automationAccounts/configurations | Sim | Sim |
| automationAccounts/jobs | Não |  Não |
| automationAccounts/runbooks | Sim | Sim |
| automationAccounts/softwareUpdateConfigurations | Não | Não |
| automationAccounts/webhooks | Não |  Não |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| sistemas | Não |  Não |
| ambientes/contas | Não |  Não |
| environments/accounts/namespaces | Não |  Não |
| environments/accounts/namespaces/configurations | Não |  Não |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| b2cDirectories | Sim | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| registros | Sim | Sim |
| registrations/customerSubscriptions | Não |  Não |
| registros/produtos | Não |  Não |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| batchAccounts | Sim | Sim |

## <a name="microsoftbilling"></a>Microsoft.Billing
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| billingAccounts | Não |  Não |
| billingAccounts/billingProfiles | Não |  Não |
| billingAccounts/billingProfiles/billingSubscriptions | Não |  Não |
| billingAccounts/billingProfiles/invoices | Não |  Não |
| billingAccounts/billingProfiles/invoices/pricesheet | Não |  Não |
| billingAccounts/billingProfiles/operationStatus | Não |  Não |
| billingAccounts/billingProfiles/paymentMethods | Não |  Não |
| billingAccounts/billingProfiles/políticas | Não |  Não |
| billingAccounts/billingProfiles/pricesheet | Não |  Não |
| billingAccounts/billingProfiles/products | Não |  Não |
| billingAccounts/billingProfiles/transactions | Não |  Não |
| billingAccounts/billingSubscriptions | Não |  Não |
| billingAccounts/departments | Não |  Não |
| billingAccounts/eligibleOffers | Não |  Não |
| billingAccounts/enrollmentAccounts | Não |  Não |
| billingAccounts/faturas | Não |  Não |
| billingAccounts/invoiceSections | Não |  Não |
| billingAccounts/invoiceSections/billingSubscriptions | Não |  Não |
| billingAccounts/invoiceSections/billingSubscriptions/transfer | Não |  Não |
| billingAccounts/invoiceSections/importRequests | Não |  Não |
| billingAccounts/invoiceSections/initiateImportRequest | Não |  Não |
| billingAccounts/invoiceSections/initiateTransfer | Não |  Não |
| billingAccounts/invoiceSections/operationStatus | Não |  Não |
| billingAccounts/invoiceSections/products | Não |  Não |
| billingAccounts/invoiceSections/transfers | Não |  Não |
| billingAccounts/produtos | Não |  Não |
| billingAccounts/projetos | Não |  Não |
| billingAccounts/projects/billingSubscriptions | Não |  Não |
| billingAccounts/projects/importRequests | Não |  Não |
| billingAccounts/projects/initiateImportRequest | Não |  Não |
| billingAccounts/projects/operationStatus | Não |  Não |
| billingAccounts/projetos/produtos | Não |  Não |
| billingAccounts/transactions | Não |  Não |
| billingPeriods | Não |  Não |
| BillingPermissions | Não |  Não |
| billproperty | Não |  Não |
| BillingRoleAssignments | Não |  Não |
| BillingRoleDefinitions | Não |  Não |
| CreateBillingRoleAssignment | Não |  Não |
| departamentos | Não |  Não |
| enrollmentAccounts | Não |  Não |
| importRequests | Não |  Não |
| importRequests/acceptImportRequest | Não |  Não |
| importRequests/declineImportRequest | Não |  Não |
| faturas | Não |  Não |
| transferir | Não |  Não |
| transfers/acceptTransfer | Não |  Não |
| transferências/declineTransfer | Não |  Não |
| transferências/operationStatus | Não |  Não |
| usagePlans | Não |  Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| mapApis | Sim | Sim |
| updateCommunicationPreference | Não |  Não |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| BizTalk | Sim | Sim |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| blueprintAssignments | Não |  Não |
| blueprintAssignments/assignmentOperations | Não |  Não |
| blueprintAssignments/operations | Não |  Não |
| planos gráficos | Não |  Não |
| plantas/artefatos | Não |  Não |
| plantas/versões | Não |  Não |
| plantas/versões/artefatos | Não |  Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| botServices | Sim | Sim |
| botServices/channels | Não |  Não |
| botServices/connections | Não |  Não |

## <a name="microsoftcache"></a>Microsoft.Cache
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Redis | Sim | Sim |
| RedisConfigDefinition | Não |  Não |

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| appliedReservations | Não |  Não |
| calculatePrice | Não |  Não |
| catálogos | Não |  Não |
| commercialReservationOrders | Não |  Não |
| reservationOrders | Não |  Não |
| reservationOrders/calculateRefund | Não |  Não |
| reservationOrders/merge | Não |  Não |
| reservationOrders/reservations | Não |  Não |
| reservationOrders/reservations/revisions | Não |  Não |
| reservationOrders/return | Não |  Não |
| reservationOrders/split | Não |  Não |
| reservationOrders/swap | Não |  Não |
| Reservas | Não |  Não |
| Os | Não |  Não |
| validateReservationOrder | Não |  Não |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| edgenodes | Não |  Não |
| profiles | Sim | Sim |
| perfis/pontos de extremidade | Sim | Sim |
| perfis/pontos de extremidade/customdomains | Não |  Não |
| perfis/pontos de extremidade/origens | Não |  Não |
| validateProbe | Não |  Não |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| certificateOrders | Sim | Sim |
| certificateOrders/certificates | Não |  Não |
| validateCertificateRegistrationInformation | Não |  Não |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Técnicas | Não |  Não |
| domainNames | Não |  Não |
| domainNames/capabilities | Não |  Não |
| domainNames/internalLoadBalancers | Não |  Não |
| domainNames/serviceCertificates | Não |  Não |
| domainNames/slots | Não |  Não |
| domainNames/slots/roles | Não |  Não |
| moveSubscriptionResources | Não |  Não |
| operatingSystemFamilies | Não |  Não |
| operatingSystems | Não |  Não |
| quotas | Não |  Não |
| resourceType | Não |  Não |
| validateSubscriptionMoveAvailability | Não |  Não |
| VirtualMachines | Não |  Não |
| virtualMachines/diagnosticSettings | Não |  Não |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| classicInfrastructureResources | Não |  Não |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Técnicas | Não |  Não |
| expressRouteCrossConnections | Não |  Não |
| expressRouteCrossConnections/peerings | Não |  Não |
| gatewaySupportedDevices | Não |  Não |
| networkSecurityGroups | Não |  Não |
| quotas | Não |  Não |
| reservedIps | Não |  Não |
| virtualNetworks | Não |  Não |
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Não |  Não |
| virtualNetworks/virtualNetworkPeerings | Não |  Não |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Técnicas | Não |  Não |
| Discos | Não |  Não |
| imagens | Não |  Não |
| osImages | Não |  Não |
| osPlatformImages | Não |  Não |
| publicImages | Não |  Não |
| quotas | Não |  Não |
| storageAccounts | Não |  Não |
| storageAccounts/services | Não |  Não |
| storageAccounts/services/diagnosticSettings | Não |  Não |
| storageAccounts/vmImages | Não |  Não |
| vmImages | Não |  Não |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| RateCard | Não |  Não |
| UsageAggregates | Não |  Não |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| availabilitySets | Sim | Sim |
| Discos | Sim | Sim |
| imagens | Sim | Sim |
| restorePointCollections | Sim | Sim |
| restorePointCollections/restorePoints | Não |  Não |
| sharedVMImages | Sim | Sim |
| sharedVMImages/versions | Sim | Sim |
| instantâneos | Sim | Sim |
| VirtualMachines | Sim | Sim |
| virtualMachines/diagnosticSettings | Não |  Não |
| virtualMachines/extensões | Sim | Sim |
| virtualMachineScaleSets | Sim | Sim |
| virtualMachineScaleSets/extensions | Não |  Não |
| virtualMachineScaleSets/networkInterfaces | Não |  Não |
| virtualMachineScaleSets/publicIPAddresses | Não |  Não |
| virtualMachineScaleSets/virtualMachines | Não |  Não |
| virtualMachineScaleSets/virtualMachines/networkInterfaces | Não |  Não |

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| AggregatedCost | Não |  Não |
| Saldos | Não |  Não |
| Orçamentos | Não |  Não |
| Contabiliza | Não |  Não |
| CostTags | Não |  Não |
| crédito | Não |  Não |
| eventos | Não |  Não |
| Previsões | Não |  Não |
| muitas | Não |  Não |
| Marketplaces | Não |  Não |
| Pricesheets | Não |  Não |
| produto | Não |  Não |
| ReservationDetails | Não |  Não |
| ReservationRecommendations | Não |  Não |
| ReservationSummaries | Não |  Não |
| ReservationTransactions | Não |  Não |
| Tags | Não |  Não |
| Termos | Não |  Não |
| UsageDetails | Não |  Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| containerGroups | Sim | Sim |
| serviceAssociationLinks | Não |  Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| registros | Sim | Sim |
| registros/compilações | Não |  Não |
| registros/compilações/cancelar | Não |  Não |
| registries/builds/getLogLink | Não |  Não |
| registries/buildTasks | Sim | Sim |
| registries/buildTasks/steps | Não |  Não |
| registries/eventGridFilters | Não |  Não |
| registries/getBuildSourceUploadUrl | Não |  Não |
| registros/GetCredentials | Não |  Não |
| registries/importImage | Não |  Não |
| registries/queueBuild | Não |  Não |
| registries/regenerateCredential | Não |  Não |
| registries/regenerateCredentials | Não |  Não |
| registros/replicações | Sim | Sim |
| registros/execuções | Não |  Não |
| registros/execuções/cancelamento | Não |  Não |
| registries/scheduleRun | Não |  Não |
| registros/tarefas | Sim | Sim |
| registries/updatePolicies | Não |  Não |
| registros/WebHooks | Sim | Sim |
| registries/webhooks/getCallbackConfig | Não |  Não |
| registros/WebHooks/ping | Não |  Não |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| containerServices | Sim | Sim |
| managedClusters | Sim | Sim |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| aplicações | Sim | Sim |
| updateCommunicationPreference | Não |  Não |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Alertas | Não |  Não |
| billingAccounts | Não |  Não |
| Conectores | Sim | Sim |
| departamentos | Não |  Não |
| Dimensões | Não |  Não |
| enrollmentAccounts | Não |  Não |
| Consulta | Não |  Não |
| Registr | Não |  Não |
| Reportconfigs | Não |  Não |
| Relatórios | Não |  Não |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| hubs | Sim | Sim |
| hubs/authorizationPolicies | Não |  Não |
| hubs/conectores | Não |  Não |
| hubs/conectores/mapeamentos | Não |  Não |
| hubs/interações | Não |  Não |
| hubs/kpi | Não |  Não |
| hubs/links | Não |  Não |
| hubs/perfis | Não |  Não |
| hubs/roleAssignments | Não |  Não |
| hubs/funções | Não |  Não |
| hubs/suggestTypeSchema | Não |  Não |
| hubs/exibições | Não |  Não |
| hubs/widgetTypes | Não |  Não |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| sejam | Sim | Sim |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| DataBoxEdgeDevices | Sim | Sim |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Espaços | Sim | Não |
| workspaces/virtualNetworkPeerings | Não |  Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| catálogos | Sim | Sim |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| connectionManagers | Sim | Sim |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| dataFactories | Sim | Não |
| dataFactories/diagnosticSettings | Não |  Não |
| dataFactorySchema | Não |  Não |
| fábricas | Sim | Não |
| factories/integrationRuntimes | Não |  Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |
| accounts/dataLakeStoreAccounts | Não |  Não |
| accounts/storageAccounts | Não |  Não |
| accounts/storageAccounts/containers | Não |  Não |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |
| accounts/eventGridFilters | Não |  Não |
| accounts/firewallRules | Não |  Não |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| services | Sim | Sim |
| services/projects | Sim | Sim |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Servidores | Sim | Sim |
| servers/recoverableServers | Não |  Não |
| servers/virtualNetworkRules | Não |  Não |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Servidores | Sim | Sim |
| servers/recoverableServers | Não |  Não |
| servers/virtualNetworkRules | Não |  Não |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Servidores | Sim | Sim |
| servidores/consultores | Não |  Não |
| servers/queryTexts | Não |  Não |
| servers/recoverableServers | Não |  Não |
| servers/topQueryStatistics | Não |  Não |
| servers/virtualNetworkRules | Não |  Não |
| servers/waitStatistics | Não |  Não |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| IotHubs | Sim | Sim |
| IotHubs/eventGridFilters | Não |  Não |
| ProvisioningServices | Sim | Sim |
| usos | Não |  Não |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Controladores | Sim | Sim |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| prático | Sim | Sim |
| labs/serviceRunners | Sim | Sim |
| labs/virtualMachines | Sim | Sim |
| agendamento | Sim | Sim |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| databaseAccountNames | Não |  Não |
| databaseAccounts | Sim | Sim |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| domínios | Sim | Sim |
| domains/domainOwnershipIdentifiers | Não |  Não |
| generateSsoRequest | Não |  Não |
| topLevelDomains | Não |  Não |
| validateDomainRegistrationInformation | Não |  Não |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| lcsprojects | Não |  Não |
| lcsprojects/clouddeployments | Não |  Não |
| lcsprojects/conectores | Não |  Não |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| domínios | Sim | Não |
| domínios/tópicos | Não |  Não |
| eventSubscriptions | Não |  Não |
| extensionTopics | Não |  Não |
| Tópicos | Sim | Não |
| topicTypes | Não |  Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| clusters | Sim | Não |
| namespaces | Sim | Não |
| namespaces/authorizationrules | Não |  Não |
| namespaces/disasterrecoveryconfigs | Não |  Não |
| namespaces/Eventhubs | Não |  Não |
| namespaces/eventhubs/authorizationrules | Não |  Não |
| namespaces/eventhubs/consumergroups | Não |  Não |

## <a name="microsoftfeatures"></a>Microsoft.Features
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| elástica | Não |  Não |
| Fornecedores | Não |  Não |

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Registr | Não |  Não |
| galleryitems | Não |  Não |
| generateartifactaccessuri | Não |  Não |
| minhas áreas | Não |  Não |
| myddds/áreas | Não |  Não |
| myddds/áreas/áreas | Não |  Não |
| myareas/areas/areas/galleryitems | Não |  Não |
| myareas/areas/galleryitems | Não |  Não |
| myareas/galleryitems | Não |  Não |
| Registr | Não |  Não |
| Os | Não |  Não |
| retrieveresourcesbyid | Não |  Não |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| guestConfigurationAssignments | Não |  Não |
| Antivírus | Não |  Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| hanaInstances | Sim |  Sim |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| clusters | Sim | Sim |
| clusters/aplicativos | Não |  Não |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| sejam | Sim | Sim |

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| labelGroups | Não |  Não |
| labelGroups/labels | Não |  Não |
| labelGroups/labels/conditions | Não |  Não |
| labelGroups/rótulos/subrótulos | Não |  Não |
| labelGroups/labels/subLabels/conditions | Não |  Não |

## <a name="microsoftinsights"></a>microsoft.insights
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| actiongroups | Sim | Sim |
| activityLogAlerts | Sim | Sim |
| alertrules | Sim | Sim |
| automatedExportSettings | Não |  Não |
| autoscalesettings | Sim | Sim |
| base | Não |  Não |
| calculatebaseline | Não |  Não |
| QC | Sim | Sim |
| componentes/eventos | Não |  Não |
| componentes/pricingPlans | Não |  Não |
| components/query | Não |  Não |
| diagnosticSettings | Não |  Não |
| diagnosticSettingsCategories | Não |  Não |
| eventCategories | Não |  Não |
| EventTypes | Não |  Não |
| extendedDiagnosticSettings | Não |  Não |
| logDefinitions | Não |  Não |
| logprofiles | Não |  Não |
| logs | Não |  Não |
| metricAlerts | Sim | Sim |
| migrateToNewPricingModel | Não |  Não |
| minhas pastas de trabalho | Não |  Não |
| consultas de | Não |  Não |
| rollbackToLegacyPricingModel | Não |  Não |
| scheduledqueryrules | Sim | Sim |
| vmInsightsOnboardingStatuses | Não |  Não |
| webtests | Sim | Sim |
| Pastas | Sim | Sim |

## <a name="microsoftintune"></a>Microsoft.Intune
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Não |  Não |
| diagnosticSettingsCategories | Não |  Não |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| IoTApps | Sim | Sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Graph | Sim | Sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| deletedVaults | Não |  Não |
| cofres | Sim | Sim |
| vaults/accessPolicies | Não |  Não |
| cofres/segredos | Não |  Não |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| clusters | Sim | Sim |
| clusters/bancos de dados | Não |  Não |
| clusters/bancos de dados/conexões DataConnections | Não |  Não |
| clusters/bancos de dados/eventhubconnections | Não |  Não |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| labaccounts | Sim | Sim |
| utilizadores | Não |  Não |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |

## <a name="microsoftloganalytics"></a>Microsoft. LogAnalytics
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| logs | Não |  Não |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| integrationAccounts | Sim | Sim |
| fluxos | Sim | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| commitmentPlans | Sim | Sim |
| webServices | Sim | Sim |
| Áreas de Trabalho | Sim | Sim |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |
| contas/espaços de trabalho | Sim | Sim |
| accounts/workspaces/projects | Sim | Sim |
| teamAccounts | Sim | Sim |
| teamAccounts/workspaces | Sim | Sim |
| teamAccounts/workspaces/projects | Sim | Sim |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Espaços | Sim | Sim |
| espaços de trabalho/computações | Não |  Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Identities | Não |  Não |
| userAssignedIdentities | Sim | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| getEntities | Não |  Não |
| managementGroups | Não |  Não |
| Os | Não |  Não |
| startTenantBackfill | Não |  Não |
| tenantBackfillStatus | Não |  Não |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |
| accounts/eventGridFilters | Não |  Não |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| ofereça | Não |  Não |
| offerTypes | Não |  Não |
| offerTypes/Publicadores | Não |  Não |
| offerTypes/publishers/offers | Não |  Não |
| offerTypes/publishers/offers/plans | Não |  Não |
| offerTypes/publishers/offers/plans/agreements | Não |  Não |
| offerTypes/publishers/offers/plans/configs | Não |  Não |
| offerTypes/publishers/offers/plans/configs/importImage | Não |  Não |
| privategalleryitems | Não |  Não |
| produto | Não |  Não |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| classicDevServices | Sim | Sim |
| updateCommunicationPreference | Não |  Não |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| licenças | Não |  Não |
| offertypes | Não |  Não |

## <a name="microsoftmedia"></a>Microsoft.Media
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| mediaservices | Sim | Sim |
| mediaservices/accountFilters | Não |  Não |
| mediaservices/ativos | Não |  Não |
| mediaservices/assets/assetFilters | Não |  Não |
| mediaservices/contentKeyPolicies | Não |  Não |
| mediaservices/eventGridFilters | Não |  Não |
| mediaservices/liveEventOperations | Não |  Não |
| mediaservices/liveEvents | Sim | Sim |
| mediaservices/liveEvents/liveOutputs | Não |  Não |
| mediaservices/liveOutputOperations | Não |  Não |
| mediaservices/streamingEndpointOperations | Não |  Não |
| mediaservices/streamingEndpoints | Sim | Sim |
| mediaservices/streamingLocators | Não |  Não |
| mediaservices/streamingPolicies | Não |  Não |
| mediaservices/transformações | Não |  Não |
| mediaservices/transforms/jobs | Não |  Não |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| projeto | Sim | Sim |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| applicationGateways | Sim | Não |
| applicationSecurityGroups | Sim | Sim |
| azureFirewallFqdnTags | Não |  Não |
| azureFirewalls | Sim | Não |
| bgpServiceCommunities | Não |  Não |
| conexões | Sim | Sim |
| ddosCustomPolicies | Sim | Sim |
| ddosProtectionPlans | Sim | Sim |
| dnsOperationStatuses | Não |  Não |
| dnszones | Sim | Sim |
| dnszones/A | Não |  Não |
| dnszones/AAAA | Não |  Não |
| dnszones/tudo | Não |  Não |
| dnszones/CAA | Não |  Não |
| dnszones/CNAME | Não |  Não |
| dnszones/MX | Não |  Não |
| dnszones/NS | Não |  Não |
| dnszones/PTR | Não |  Não |
| dnszones/recordsets | Não |  Não |
| dnszones/SOA | Não |  Não |
| dnszones/SRV | Não |  Não |
| dnszones/TXT | Não |  Não |
| expressRouteCircuits | Sim  | Não |
| expressRouteServiceProviders | Não |  Não |
| frontdoors | Sim, mas limitado (veja a [Observação abaixo](#frontdoor)) | Sim |
| frontdoorWebApplicationFirewallPolicies | Sim, mas limitado (veja a [Observação abaixo](#frontdoor)) | Sim |
| getDnsResourceReference | Não |  Não |
| interfaceEndpoints | Sim | Sim |
| internalNotify | Não |  Não |
| loadBalancers | Sim | Não |
| localNetworkGateways | Sim | Sim |
| natGateways | Sim | Sim |
| networkIntentPolicies | Sim | Sim |
| networkInterfaces | Sim | Sim |
| networkProfiles | Sim | Sim |
| networkSecurityGroups | Sim | Sim |
| networkWatchers | Sim | Não |
| networkWatchers/connectionMonitors | Sim | Não |
| networkWatchers/lenses | Sim | Não |
| networkWatchers/pingMeshes | Sim | Não |
| privateLinkServices | Sim | Sim |
| publicIPAddresses | Sim | Sim |
| publicIPPrefixes | Sim | Sim |
| routeFilters | Sim | Sim |
| routeTables | Sim | Sim |
| serviceEndpointPolicies | Sim | Sim |
| trafficManagerGeographicHierarchies | Não |  Não |
| trafficmanagerprofiles | Sim | Sim |
| trafficmanagerprofiles/heatMaps | Não |  Não |
| virtualHubs | Sim | Sim |
| virtualNetworkGateways | Sim | Sim |
| virtualNetworks | Sim | Sim |
| virtualNetworks/subnets | Não |  Não |
| virtualNetworkTaps | Sim | Sim |
| virtualWans | Sim | Sim |
| vpnGateways | Sim | Não |
| vpnSites | Sim | Sim |
| webApplicationFirewallPolicies | Sim | Sim |

<a id="frontdoor" />

Para o serviço de porta frontal do Azure, você pode aplicar marcas ao criar o recurso, mas a atualização ou adição de marcas não tem suporte no momento.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| namespaces | Sim | Não |
| namespaces/notificationHubs | Sim | Não |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| dispositivos | Não |  Não |
| linkTargets | Não |  Não |
| storageInsightConfigs | Não |  Não |
| Espaços | Sim | Sim |
| workspaces/dataSources | Não |  Não |
| workspaces/linkedServices | Não |  Não |
| espaços de trabalho/consulta | Não |  Não |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| managementassociations | Não |  Não |
| managementconfigurations | Sim | Sim |
| soluções | Sim | Sim |
| Modos de exibição | Sim | Sim |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| policyEvents | Não |  Não |
| policyStates | Não |  Não |
| policyTrackedResources | Não |  Não |
| correções | Não |  Não |

## <a name="microsoftportal"></a>Microsoft.Portal
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| consoles | Não |  Não |
| Painéis | Sim | Sim |
| UserSettings | Não |  Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| workspaceCollections | Sim | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| unidades | Sim | Sim |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| backupProtectedItems | Não |  Não |
| cofres | Sim | Sim |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| namespaces | Sim | Sim |
| namespaces/authorizationrules | Não |  Não |
| namespaces/hybridconnections | Não |  Não |
| namespaces/hybridconnections/authorizationrules | Não |  Não |
| namespaces/wcfrelays | Não |  Não |
| namespaces/wcfrelays/authorizationrules | Não |  Não |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Os | Não |  Não |
| subscriptionsStatus | Não |  Não |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| availabilityStatuses | Não |  Não |
| childAvailabilityStatuses | Não |  Não |
| childResources | Não |  Não |
| eventos | Não |  Não |
| impactedResources | Não |  Não |
| Notificações | Não |  Não |

## <a name="microsoftresources"></a>Microsoft.Resources
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| implantações | Não |  Não |
| implantações/operações | Não |  Não |
| Vincule | Não |  Não |
| notifyResourceJobs | Não |  Não |
| Fornecedores | Não |  Não |
| resourceGroups | Não |  Não |
| Os | Não |  Não |
| assinaturas | Não |  Não |
| assinaturas/provedores | Não |  Não |
| subscriptions/resourceGroups | Não |  Não |
| subscriptions/resourcegroups/resources | Não |  Não |
| assinaturas/recursos | Não |  Não |
| assinaturas/TagNames | Não |  Não |
| subscriptions/tagNames/tagValues | Não |  Não |
| locatários | Não |  Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| aplicações | Sim | Sim |
| saasresources | Não |  Não |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| fluxo | Sim | Sim |
| gratuitas | Sim | Sim |

## <a name="microsoftsearch"></a>Microsoft.Search
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| resourceHealthMetadata | Não |  Não |
| searchServices | Sim | Sim |

## <a name="microsoftsecurity"></a>Microsoft.Security
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| advancedThreatProtectionSettings | Não |  Não |
| alerts | Não |  Não |
| allowedConnections | Não |  Não |
| dispositivos | Não |  Não |
| applicationWhitelistings | Não |  Não |
| AutoProvisioningSettings | Não |  Não |
| Conformidades | Não |  Não |
| dataCollectionAgents | Não |  Não |
| discoveredSecuritySolutions | Não |  Não |
| externalSecuritySolutions | Não |  Não |
| InformationProtectionPolicies | Não |  Não |
| jitNetworkAccessPolicies | Não |  Não |
| Monitoriza | Não |  Não |
| monitoramento/Antimalware | Não |  Não |
| monitoramento/linha de base | Não |  Não |
| monitoramento/patch | Não |  Não |
| Policie | Não |  Não |
| preços | Não |  Não |
| securityContacts | Não |  Não |
| securitySolutions | Não |  Não |
| securitySolutionsReferenceData | Não |  Não |
| securityStatus | Não |  Não |
| securityStatus/pontos de extremidade | Não |  Não |
| securityStatus/sub-redes | Não |  Não |
| securityStatus/virtualMachines | Não |  Não |
| securityStatuses | Não |  Não |
| securityStatusesSummaries | Não |  Não |
| settings | Não |  Não |
| tarefas | Não |  Não |
| topologias | Não |  Não |
| workspaceSettings | Não |  Não |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Não |  Não |
| diagnosticSettingsCategories | Não |  Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| namespaces | Sim | Não |
| namespaces/authorizationrules | Não |  Não |
| namespaces/disasterrecoveryconfigs | Não |  Não |
| namespaces/eventgridfilters | Não |  Não |
| namespaces/queues | Não |  Não |
| namespaces/queues/authorizationrules | Não |  Não |
| namespaces/topics | Não |  Não |
| namespaces/topics/authorizationrules | Não |  Não |
| namespaces/tópicos/assinaturas | Não |  Não |
| namespaces/tópicos/assinaturas/regras | Não |  Não |
| premiumMessagingRegions | Não |  Não |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| clusters | Sim | Sim |
| clusters/aplicativos | Não |  Não |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| aplicações | Sim | Sim |
| gateways | Sim | Sim |
| às | Sim | Sim |
| confidenciais | Sim | Sim |
| volumes | Sim | Sim |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| SignalR | Sim | Sim |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| applianceDefinitions | Sim | Sim |
| dispositivos | Sim | Sim |
| applicationDefinitions | Sim | Sim |
| aplicações | Sim | Sim |
| jitRequests | Sim | Sim |

## <a name="microsoftsql"></a>Microsoft.SQL
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| managedInstances | Sim | Sim |
| managedInstances/databases | Sim (veja a observação abaixo) | Sim |
| managedInstances/databases/backupShortTermRetentionPolicies | Não | Não |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | Não | Não |
| managedInstances/databases/vulnerabilityAssessments | Não | Não |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | Não | Não |
| managedInstances/encryptionProtector | Não | Não |
| managedInstances/keys | Não | Não |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Não | Não |
| managedInstances/vulnerabilityAssessments | Não | Não |
| Servidores | Sim | Sim |
| servidores/administradores | Não |  Não |
| servers/communicationLinks | Não |  Não |
| servers/databases | Sim (veja a observação abaixo) | Sim |
| servers/encryptionProtector | Não |  Não |
| servers/firewallRules | Não |  Não |
| servers/keys | Não |  Não |
| servers/restorableDroppedDatabases | Não |  Não |
| servidores/preobjetivos | Não |  Não |
| servers/tdeCertificates | Não |  Não |

> [!NOTE]
> O banco de dados mestre não dá suporte a marcas, mas outros bancos de dados, incluindo bancos de dados do Azure SQL Data Warehouse, marcas de suporte. Os bancos de dados do Azure SQL Data Warehouse devem estar no estado ativo (não pausado).


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| SqlVirtualMachineGroups | Sim | Sim |
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Não |  Não |
| SqlVirtualMachines | Sim | Sim |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| storageAccounts | Sim | Sim |
| storageAccounts/blobServices | Não |  Não |
| storageAccounts/fileServices | Não |  Não |
| storageAccounts/queueServices | Não |  Não |
| storageAccounts/services | Não |  Não |
| storageAccounts/tableServices | Não |  Não |
| usos | Não |  Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| storageSyncServices | Sim | Sim |
| storageSyncServices/registeredServers | Não |  Não |
| storageSyncServices/syncGroups | Não |  Não |
| storageSyncServices/syncGroups/cloudEndpoints | Não |  Não |
| storageSyncServices/syncGroups/serverEndpoints | Não |  Não |
| storageSyncServices/workflows | Não |  Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| esses | Sim | Sim |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| streamingjobs | Sim (veja a observação abaixo) | Sim |
| streamingjobs/diagnosticSettings | Não |  Não |

> [!NOTE]
> Você não pode adicionar uma marca quando streamingjobs está em execução. Interrompa o recurso para adicionar uma marca.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| CreateSubscription | Não |  Não |
| SubscriptionDefinitions | Não |  Não |
| SubscriptionOperations | Não |  Não |

## <a name="microsoftsupport"></a>Microsoft. support
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| supporttickets | Não |  Não |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| providerRegistrations | Sim | Sim |
| Os | Sim | Sim |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| sistemas | Sim | Não |
| environments/accessPolicies | Não |  Não |
| ambientes/EventSources | Sim | Não |
| environments/referenceDataSets | Sim | Não |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| conta | Sim | Sim |
| conta/extensão | Sim | Sim |
| conta/projeto | Sim | Sim |

## <a name="microsoftweb"></a>Microsoft.Web
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| apiManagementAccounts | Não |  Não |
| apiManagementAccounts/apiAcls | Não |  Não |
| apiManagementAccounts/apis | Não |  Não |
| apiManagementAccounts/apis/apiAcls | Não |  Não |
| apiManagementAccounts/apis/connectionAcls | Não |  Não |
| apiManagementAccounts/apis/connections | Não |  Não |
| apiManagementAccounts/apis/connections/connectionAcls | Não |  Não |
| apiManagementAccounts/apis/localizedDefinitions | Não |  Não |
| apiManagementAccounts/connectionAcls | Não |  Não |
| apiManagementAccounts/connections | Não |  Não |
| billingMeters | Não |  Não |
| Certificado | Sim | Sim |
| connectionGateways | Sim | Sim |
| conexões | Sim | Sim |
| customApis | Sim | Sim |
| deletedSites | Não |  Não |
| functions | Não |  Não |
| hostingEnvironments | Sim | Sim |
| hostingEnvironments/multiRolePools | Não |  Não |
| hostingEnvironments/multiRolePools/instâncias | Não |  Não |
| hostingEnvironments/workerPools | Não |  Não |
| hostingEnvironments/workerPools/instances | Não |  Não |
| publishingUsers | Não |  Não |
| Recommendations | Não |  Não |
| resourceHealthMetadata | Não |  Não |
| tempos | Não |  Não |
| serverFarms | Sim | Sim |
| serverFarms/workers | Não |  Não |
| sites | Sim | Sim |
| sites/domainOwnershipIdentifiers | Não |  Não |
| sites/hostNameBindings | Não |  Não |
| sites/instâncias | Não |  Não |
| sites/instances/extensions | Não |  Não |
| sites/premieraddons | Sim | Sim |
| sites/recomendações | Não |  Não |
| sites/resourceHealthMetadata | Não |  Não |
| sites/Slots | Sim | Sim |
| sites/slots/hostNameBindings | Não |  Não |
| sites/slots/instances | Não |  Não |
| sites/slots/instances/extensions | Não |  Não |
| sourceControls | Não |  Não |
| verifica | Não |  Não |
| verifyHostingEnvironmentVnet | Não |  Não |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Não |  Não |
| diagnosticSettingsCategories | Não |  Não |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Dispositivos | Sim | Sim |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Tipo de recurso | Dá suporte a marcas | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| QC | Não |  Não |
| componentsSummary | Não |  Não |
| monitorInstances | Não |  Não |
| monitorInstancesSummary | Não |  Não |
| monitores | Não |  Não |
| notificationSettings | Não |  Não |

## <a name="next-steps"></a>Passos Seguintes
Para saber como aplicar marcas a recursos, consulte [usar marcas para organizar os recursos do Azure](resource-group-using-tags.md).
