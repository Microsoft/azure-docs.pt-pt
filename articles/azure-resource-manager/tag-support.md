---
title: Suporte de etiqueta do Azure Resource Manager para recursos
description: Mostra os tipos de recursos do Azure suportam etiquetas. Fornece detalhes sobre todos os serviços do Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 06/07/2019
ms.author: tomfitz
ms.openlocfilehash: 4f1bc1415fbb875120d7b64128cae69e1e3f442c
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67339849"
---
# <a name="tag-support-for-azure-resources"></a>Suporte a marcas para recursos do Azure
Este artigo descreve se um tipo de recurso suporta [etiquetas](resource-group-using-tags.md). A coluna intitulada **suporta etiquetas** indica se o tipo de recurso tem uma propriedade para a marca. A coluna intitulada **marca no relatório de custo** indica se esse tipo de recurso passa a etiqueta para o relatório de custos.

Para obter os mesmos dados como um ficheiro de valores separados por vírgulas, transfira [marca support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| DomainServices | Sim | Sim |
| DomainServices/oucontainer | Não | Não |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Não |  Não |
| diagnosticSettingsCategories | Não |  Não |

## <a name="microsoftaddons"></a>Microsoft.Addons
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| supportProviders | Não |  Não |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| aadsupportcases | Não |  Não |
| addsservices | Não |  Não |
| agents | Não |  Não |
| anonymousapiusers | Não |  Não |
| configuração | Não |  Não |
| logs | Não |  Não |
| relatórios | Não |  Não |
| services | Não |  Não |

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Configurações | Não |  Não |
| generateRecommendations | Não |  Não |
| Recomendações | Não |  Não |
| supressão | Não |  Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| actionRules | Não |  Não |
| alerts | Não |  Não |
| alertsList | Não |  Não |
| alertsSummary | Não |  Não |
| alertsSummaryList | Não |  Não |
| smartDetectorAlertRules | Não |  Não |
| smartDetectorRuntimeEnvironments | Não |  Não |
| smartGroups | Não |  Não |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Servidores | Sim | Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| reportFeedback | Não |  Não |
| serviço | Sim | Sim |
| validateServiceName | Não |  Não |

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| attestationProviders | Não |  Não |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| classicAdministrators | Não |  Não |
| denyAssignments | Não |  Não |
| elevateAccess | Não |  Não |
| Bloqueios | Não |  Não |
| permissões | Não |  Não |
| policyAssignments | Não |  Não |
| policyDefinitions | Não |  Não |
| policySetDefinitions | Não |  Não |
| providerOperations | Não |  Não |
| roleAssignments | Não |  Não |
| roleDefinitions | Não |  Não |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| automationAccounts | Sim | Sim |
| automationAccounts/configurations | Sim | Sim |
| automationAccounts/jobs | Não |  Não |
| automationAccounts/runbooks | Sim | Sim |
| automationAccounts/softwareUpdateConfigurations | Não | Não |
| automationAccounts/webhooks | Não |  Não |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Ambientes | Não |  Não |
| ambientes/contas | Não |  Não |
| environments/accounts/namespaces | Não |  Não |
| environments/accounts/namespaces/configurations | Não |  Não |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| b2cDirectories | Sim | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| registos | Sim | Sim |
| registrations/customerSubscriptions | Não |  Não |
| registos/produtos | Não |  Não |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| batchAccounts | Sim | Sim |

## <a name="microsoftbilling"></a>Microsoft.Billing
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| billingAccounts | Não |  Não |
| billingAccounts/billingProfiles | Não |  Não |
| billingAccounts/billingProfiles/billingSubscriptions | Não |  Não |
| billingAccounts/billingProfiles/invoices | Não |  Não |
| billingAccounts/billingProfiles/invoices/pricesheet | Não |  Não |
| billingAccounts/billingProfiles/operationStatus | Não |  Não |
| billingAccounts/billingProfiles/paymentMethods | Não |  Não |
| billingAccounts/billingProfiles/policies | Não |  Não |
| billingAccounts/billingProfiles/pricesheet | Não |  Não |
| billingAccounts/billingProfiles/products | Não |  Não |
| billingAccounts/billingProfiles/transactions | Não |  Não |
| billingAccounts/billingSubscriptions | Não |  Não |
| billingAccounts/departments | Não |  Não |
| billingAccounts/eligibleOffers | Não |  Não |
| billingAccounts/enrollmentAccounts | Não |  Não |
| billingAccounts/invoices | Não |  Não |
| billingAccounts/invoiceSections | Não |  Não |
| billingAccounts/invoiceSections/billingSubscriptions | Não |  Não |
| billingAccounts/invoiceSections/billingSubscriptions/transfer | Não |  Não |
| billingAccounts/invoiceSections/importRequests | Não |  Não |
| billingAccounts/invoiceSections/initiateImportRequest | Não |  Não |
| billingAccounts/invoiceSections/initiateTransfer | Não |  Não |
| billingAccounts/invoiceSections/operationStatus | Não |  Não |
| billingAccounts/invoiceSections/products | Não |  Não |
| billingAccounts/invoiceSections/transfers | Não |  Não |
| billingAccounts/products | Não |  Não |
| billingAccounts/projects | Não |  Não |
| billingAccounts/projects/billingSubscriptions | Não |  Não |
| billingAccounts/projects/importRequests | Não |  Não |
| billingAccounts/projects/initiateImportRequest | Não |  Não |
| billingAccounts/projects/operationStatus | Não |  Não |
| billingAccounts/projects/products | Não |  Não |
| billingAccounts/transactions | Não |  Não |
| billingPeriods | Não |  Não |
| BillingPermissions | Não |  Não |
| billingProperty | Não |  Não |
| BillingRoleAssignments | Não |  Não |
| BillingRoleDefinitions | Não |  Não |
| CreateBillingRoleAssignment | Não |  Não |
| Departamentos | Não |  Não |
| enrollmentAccounts | Não |  Não |
| importRequests | Não |  Não |
| importRequests/acceptImportRequest | Não |  Não |
| importRequests/declineImportRequest | Não |  Não |
| Notas fiscais | Não |  Não |
| Transferências | Não |  Não |
| transfers/acceptTransfer | Não |  Não |
| transfers/declineTransfer | Não |  Não |
| transfers/operationStatus | Não |  Não |
| usagePlans | Não |  Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| mapApis | Sim | Sim |
| updateCommunicationPreference | Não |  Não |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| BizTalk | Sim | Sim |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| blueprintAssignments | Não |  Não |
| blueprintAssignments/assignmentOperations | Não |  Não |
| blueprintAssignments/operations | Não |  Não |
| planos gráficos | Não |  Não |
| blueprints/artifacts | Não |  Não |
| blueprints/versions | Não |  Não |
| blueprints/versions/artifacts | Não |  Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| botServices | Sim | Sim |
| botServices/channels | Não |  Não |
| botServices/connections | Não |  Não |

## <a name="microsoftcache"></a>Microsoft.Cache
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Redis | Sim | Sim |
| RedisConfigDefinition | Não |  Não |

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
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
| reservas | Não |  Não |
| Recursos | Não |  Não |
| validateReservationOrder | Não |  Não |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| edgenodes | Não |  Não |
| Perfis | Sim | Sim |
| perfis/pontos finais | Sim | Sim |
| profiles/endpoints/customdomains | Não |  Não |
| perfis/pontos finais/origens | Não |  Não |
| validateProbe | Não |  Não |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| certificateOrders | Sim | Sim |
| certificateOrders/certificates | Não |  Não |
| validateCertificateRegistrationInformation | Não |  Não |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Capacidades | Não |  Não |
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
| resourceTypes | Não |  Não |
| validateSubscriptionMoveAvailability | Não |  Não |
| virtualMachines | Não |  Não |
| virtualMachines/diagnosticSettings | Não |  Não |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| classicInfrastructureResources | Não |  Não |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Capacidades | Não |  Não |
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
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Capacidades | Não |  Não |
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
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| RateCard | Não |  Não |
| UsageAggregates | Não |  Não |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| availabilitySets | Sim | Sim |
| Discos | Sim | Sim |
| imagens | Sim | Sim |
| restorePointCollections | Sim | Sim |
| restorePointCollections/restorePoints | Não |  Não |
| sharedVMImages | Sim | Sim |
| sharedVMImages/versions | Sim | Sim |
| Instantâneos | Sim | Sim |
| virtualMachines | Sim | Sim |
| virtualMachines/diagnosticSettings | Não |  Não |
| virtualMachines/extensions | Sim | Sim |
| virtualMachineScaleSets | Sim | Sim |
| virtualMachineScaleSets/extensions | Não |  Não |
| virtualMachineScaleSets/networkInterfaces | Não |  Não |
| virtualMachineScaleSets/publicIPAddresses | Não |  Não |
| virtualMachineScaleSets/virtualMachines | Não |  Não |
| virtualMachineScaleSets/virtualMachines/networkInterfaces | Não |  Não |

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| AggregatedCost | Não |  Não |
| Saldos | Não |  Não |
| Orçamentos | Não |  Não |
| Custos | Não |  Não |
| CostTags | Não |  Não |
| Créditos | Não |  Não |
| eventos | Não |  Não |
| Previsões | Não |  Não |
| muitos | Não |  Não |
| Marketplaces | Não |  Não |
| Pricesheets | Não |  Não |
| Produtos | Não |  Não |
| ReservationDetails | Não |  Não |
| ReservationRecommendations | Não |  Não |
| ReservationSummaries | Não |  Não |
| ReservationTransactions | Não |  Não |
| Tags | Não |  Não |
| Termos | Não |  Não |
| UsageDetails | Não |  Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| containerGroups | Sim | Sim |
| serviceAssociationLinks | Não |  Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| registos | Sim | Sim |
| registos/compilações | Não |  Não |
| registries/builds/cancel | Não |  Não |
| registries/builds/getLogLink | Não |  Não |
| registries/buildTasks | Sim | Sim |
| registries/buildTasks/steps | Não |  Não |
| registries/eventGridFilters | Não |  Não |
| registries/getBuildSourceUploadUrl | Não |  Não |
| registries/GetCredentials | Não |  Não |
| registries/importImage | Não |  Não |
| registries/queueBuild | Não |  Não |
| registries/regenerateCredential | Não |  Não |
| registries/regenerateCredentials | Não |  Não |
| registries/replications | Sim | Sim |
| registries/runs | Não |  Não |
| registries/runs/cancel | Não |  Não |
| registries/scheduleRun | Não |  Não |
| registos/tarefas | Sim | Sim |
| registries/updatePolicies | Não |  Não |
| registries/webhooks | Sim | Sim |
| registries/webhooks/getCallbackConfig | Não |  Não |
| registries/webhooks/ping | Não |  Não |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| containerServices | Sim | Sim |
| managedClusters | Sim | Sim |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| aplicações | Sim | Sim |
| updateCommunicationPreference | Não |  Não |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Alertas | Não |  Não |
| BillingAccounts | Não |  Não |
| Conectores | Sim | Sim |
| Departamentos | Não |  Não |
| Dimensões | Não |  Não |
| EnrollmentAccounts | Não |  Não |
| Consulta | Não |  Não |
| Registre-se | Não |  Não |
| Reportconfigs | Não |  Não |
| Relatórios | Não |  Não |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| hubs | Sim | Sim |
| hubs/authorizationPolicies | Não |  Não |
| os hubs/conectores | Não |  Não |
| hubs/connectors/mappings | Não |  Não |
| hubs/interactions | Não |  Não |
| hubs/kpi | Não |  Não |
| hubs/links | Não |  Não |
| hubs/profiles | Não |  Não |
| hubs/roleAssignments | Não |  Não |
| hubs/roles | Não |  Não |
| hubs/suggestTypeSchema | Não |  Não |
| hubs/views | Não |  Não |
| hubs/widgetTypes | Não |  Não |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Tarefas | Sim | Sim |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| DataBoxEdgeDevices | Sim | Sim |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Áreas de trabalho | Sim | Não |
| workspaces/virtualNetworkPeerings | Não |  Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| catálogos | Sim | Sim |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| connectionManagers | Sim | Sim |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| dataFactories | Sim | Não |
| dataFactories/diagnosticSettings | Não |  Não |
| dataFactorySchema | Não |  Não |
| fábricas | Sim | Não |
| factories/integrationRuntimes | Não |  Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |
| accounts/dataLakeStoreAccounts | Não |  Não |
| accounts/storageAccounts | Não |  Não |
| accounts/storageAccounts/containers | Não |  Não |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |
| accounts/eventGridFilters | Não |  Não |
| accounts/firewallRules | Não |  Não |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| services | Sim | Sim |
| services/projects | Sim | Sim |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Servidores | Sim | Sim |
| servers/recoverableServers | Não |  Não |
| servers/virtualNetworkRules | Não |  Não |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Servidores | Sim | Sim |
| servers/recoverableServers | Não |  Não |
| servers/virtualNetworkRules | Não |  Não |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Servidores | Sim | Sim |
| servers/advisors | Não |  Não |
| servers/queryTexts | Não |  Não |
| servers/recoverableServers | Não |  Não |
| servers/topQueryStatistics | Não |  Não |
| servers/virtualNetworkRules | Não |  Não |
| servers/waitStatistics | Não |  Não |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| IotHubs | Sim | Sim |
| IotHubs/eventGridFilters | Não |  Não |
| ProvisioningServices | Sim | Sim |
| Utilizações | Não |  Não |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Controladores | Sim | Sim |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Laboratórios | Sim | Sim |
| labs/serviceRunners | Sim | Sim |
| labs/virtualMachines | Sim | Sim |
| Agendas | Sim | Sim |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| databaseAccountNames | Não |  Não |
| databaseAccounts | Sim | Sim |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| domínios | Sim | Sim |
| domains/domainOwnershipIdentifiers | Não |  Não |
| generateSsoRequest | Não |  Não |
| topLevelDomains | Não |  Não |
| validateDomainRegistrationInformation | Não |  Não |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| lcsprojects | Não |  Não |
| lcsprojects/clouddeployments | Não |  Não |
| lcsprojects/conectores | Não |  Não |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| domínios | Sim | Não |
| domínios/tópicos | Não |  Não |
| eventSubscriptions | Não |  Não |
| extensionTopics | Não |  Não |
| Tópicos | Sim | Não |
| topicTypes | Não |  Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Clusters | Sim | Não |
| namespaces | Sim | Não |
| namespaces/authorizationrules | Não |  Não |
| namespaces/disasterrecoveryconfigs | Não |  Não |
| namespaces/eventhubs | Não |  Não |
| namespaces/eventhubs/authorizationrules | Não |  Não |
| namespaces/eventhubs/consumergroups | Não |  Não |

## <a name="microsoftfeatures"></a>Microsoft.Features
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| elástica | Não |  Não |
| Fornecedores | Não |  Não |

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Inscrever | Não |  Não |
| galleryitems | Não |  Não |
| generateartifactaccessuri | Não |  Não |
| myareas | Não |  Não |
| myareas/areas | Não |  Não |
| myareas/areas/areas | Não |  Não |
| myareas/areas/areas/galleryitems | Não |  Não |
| myareas/areas/galleryitems | Não |  Não |
| myareas/galleryitems | Não |  Não |
| Registre-se | Não |  Não |
| Recursos | Não |  Não |
| retrieveresourcesbyid | Não |  Não |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| guestConfigurationAssignments | Não |  Não |
| software | Não |  Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| hanaInstances | Sim |  Sim |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Clusters | Sim | Sim |
| clusters/applications | Não |  Não |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Tarefas | Sim | Sim |

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| labelGroups | Não |  Não |
| labelGroups/labels | Não |  Não |
| labelGroups/labels/conditions | Não |  Não |
| labelGroups/labels/subLabels | Não |  Não |
| labelGroups/labels/subLabels/conditions | Não |  Não |

## <a name="microsoftinsights"></a>microsoft.insights
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| actiongroups | Sim | Sim |
| activityLogAlerts | Sim | Sim |
| alertrules | Sim | Sim |
| automatedExportSettings | Não |  Não |
| autoscalesettings | Sim | Sim |
| Linha de base | Não |  Não |
| calculatebaseline | Não |  Não |
| Componentes | Sim | Sim |
| componentes/eventos | Não |  Não |
| components/pricingPlans | Não |  Não |
| components/query | Não |  Não |
| diagnosticSettings | Não |  Não |
| diagnosticSettingsCategories | Não |  Não |
| eventCategories | Não |  Não |
| eventtypes | Não |  Não |
| extendedDiagnosticSettings | Não |  Não |
| logDefinitions | Não |  Não |
| logprofiles | Não |  Não |
| logs | Não |  Não |
| metricAlerts | Sim | Sim |
| migrateToNewPricingModel | Não |  Não |
| myWorkbooks | Não |  Não |
| consultas de | Não |  Não |
| rollbackToLegacyPricingModel | Não |  Não |
| scheduledqueryrules | Sim | Sim |
| vmInsightsOnboardingStatuses | Não |  Não |
| testes Web | Sim | Sim |
| pastas de trabalho | Sim | Sim |

## <a name="microsoftintune"></a>Microsoft.Intune
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Não |  Não |
| diagnosticSettingsCategories | Não |  Não |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| IoTApps | Sim | Sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Graph | Sim | Sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| deletedVaults | Não |  Não |
| cofres | Sim | Sim |
| vaults/accessPolicies | Não |  Não |
| Cofres/segredos | Não |  Não |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Clusters | Sim | Sim |
| clusters/databases | Não |  Não |
| clusters/databases/dataconnections | Não |  Não |
| clusters/databases/eventhubconnections | Não |  Não |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| labaccounts | Sim | Sim |
| utilizadores | Não |  Não |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| logs | Não |  Não |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| integrationAccounts | Sim | Sim |
| Fluxos de trabalho | Sim | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| commitmentPlans | Sim | Sim |
| webServices | Sim | Sim |
| Áreas de Trabalho | Sim | Sim |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |
| accounts/workspaces | Sim | Sim |
| accounts/workspaces/projects | Sim | Sim |
| teamAccounts | Sim | Sim |
| teamAccounts/workspaces | Sim | Sim |
| teamAccounts/workspaces/projects | Sim | Sim |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Áreas de trabalho | Sim | Sim |
| workspaces/computes | Não |  Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Identidades | Não |  Não |
| userAssignedIdentities | Sim | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| getEntities | Não |  Não |
| managementGroups | Não |  Não |
| Recursos | Não |  Não |
| startTenantBackfill | Não |  Não |
| tenantBackfillStatus | Não |  Não |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |
| accounts/eventGridFilters | Não |  Não |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Ofertas | Não |  Não |
| offerTypes | Não |  Não |
| offerTypes/publishers | Não |  Não |
| offerTypes/publishers/offers | Não |  Não |
| offerTypes/publishers/offers/plans | Não |  Não |
| offerTypes/publishers/offers/plans/agreements | Não |  Não |
| offerTypes/publishers/offers/plans/configs | Não |  Não |
| offerTypes/publishers/offers/plans/configs/importImage | Não |  Não |
| privategalleryitems | Não |  Não |
| Produtos | Não |  Não |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| classicDevServices | Sim | Sim |
| updateCommunicationPreference | Não |  Não |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Contratos | Não |  Não |
| offertypes | Não |  Não |

## <a name="microsoftmedia"></a>Microsoft.Media
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| mediaservices | Sim | Sim |
| mediaservices/accountFilters | Não |  Não |
| mediaservices/assets | Não |  Não |
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
| mediaservices/transforms | Não |  Não |
| mediaservices/transforms/jobs | Não |  Não |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| projetos | Sim | Sim |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| applicationGateways | Sim | Não |
| applicationSecurityGroups | Sim | Sim |
| azureFirewallFqdnTags | Não |  Não |
| azureFirewalls | Sim | Não |
| bgpServiceCommunities | Não |  Não |
| Ligações | Sim | Sim |
| ddosCustomPolicies | Sim | Sim |
| ddosProtectionPlans | Sim | Sim |
| dnsOperationStatuses | Não |  Não |
| dnszones | Sim | Sim |
| dnszones/A | Não |  Não |
| dnszones/AAAA | Não |  Não |
| dnszones/all | Não |  Não |
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
| frontdoors | Sim, mas limitado (consulte [nota abaixo](#frontdoor)) | Sim |
| frontdoorWebApplicationFirewallPolicies | Sim, mas limitado (consulte [nota abaixo](#frontdoor)) | Sim |
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
| virtualNetworkGateways | Sim | Não |
| virtualNetworks | Sim | Sim |
| virtualNetworks/subnets | Não |  Não |
| virtualNetworkTaps | Sim | Sim |
| virtualWans | Sim | Sim |
| vpnGateways | Sim | Não |
| vpnSites | Sim | Sim |
| webApplicationFirewallPolicies | Sim | Sim |

<a id="frontdoor" />

Porta de entrada do serviço do Azure, pode aplicar etiquetas ao criar o recurso, mas a atualização ou adição de etiquetas não é atualmente suportada.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| namespaces | Sim | Não |
| namespaces/notificationHubs | Sim | Não |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| dispositivos | Não |  Não |
| linkTargets | Não |  Não |
| storageInsightConfigs | Não |  Não |
| Áreas de trabalho | Sim | Sim |
| workspaces/dataSources | Não |  Não |
| workspaces/linkedServices | Não |  Não |
| workspaces/query | Não |  Não |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| managementassociations | Não |  Não |
| managementconfigurations | Sim | Sim |
| soluções | Sim | Sim |
| Modos de exibição | Sim | Sim |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| policyEvents | Não |  Não |
| policyStates | Não |  Não |
| policyTrackedResources | Não |  Não |
| remediações | Não |  Não |

## <a name="microsoftportal"></a>Microsoft.Portal
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| consolas | Não |  Não |
| dashboards | Sim | Sim |
| userSettings | Não |  Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| workspaceCollections | Sim | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Capacidades | Sim | Sim |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| backupProtectedItems | Não |  Não |
| cofres | Sim | Sim |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| namespaces | Sim | Sim |
| namespaces/authorizationrules | Não |  Não |
| namespaces/hybridconnections | Não |  Não |
| namespaces/hybridconnections/authorizationrules | Não |  Não |
| namespaces/wcfrelays | Não |  Não |
| namespaces/wcfrelays/authorizationrules | Não |  Não |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Recursos | Não |  Não |
| subscriptionsStatus | Não |  Não |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| availabilityStatuses | Não |  Não |
| childAvailabilityStatuses | Não |  Não |
| childResources | Não |  Não |
| eventos | Não |  Não |
| impactedResources | Não |  Não |
| Notificações | Não |  Não |

## <a name="microsoftresources"></a>Microsoft.Resources
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Implementações | Não |  Não |
| Implementações/operações | Não |  Não |
| Ligações | Não |  Não |
| notifyResourceJobs | Não |  Não |
| Fornecedores | Não |  Não |
| resourceGroups | Não |  Não |
| Recursos | Não |  Não |
| Subscrições | Não |  Não |
| Subscrições/fornecedores | Não |  Não |
| subscriptions/resourceGroups | Não |  Não |
| subscriptions/resourcegroups/resources | Não |  Não |
| subscriptions/resources | Não |  Não |
| subscriptions/tagnames | Não |  Não |
| subscriptions/tagNames/tagValues | Não |  Não |
| Inquilinos | Não |  Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| aplicações | Sim | Sim |
| saasresources | Não |  Não |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| fluxos | Sim | Sim |
| jobcollections | Sim | Sim |

## <a name="microsoftsearch"></a>Microsoft.Search
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| resourceHealthMetadata | Não |  Não |
| searchServices | Sim | Sim |

## <a name="microsoftsecurity"></a>Microsoft.Security
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| advancedThreatProtectionSettings | Não |  Não |
| alerts | Não |  Não |
| allowedConnections | Não |  Não |
| aplicações | Não |  Não |
| applicationWhitelistings | Não |  Não |
| AutoProvisioningSettings | Não |  Não |
| Regulamentares | Não |  Não |
| dataCollectionAgents | Não |  Não |
| discoveredSecuritySolutions | Não |  Não |
| externalSecuritySolutions | Não |  Não |
| InformationProtectionPolicies | Não |  Não |
| jitNetworkAccessPolicies | Não |  Não |
| Monitorização | Não |  Não |
| monitoring/antimalware | Não |  Não |
| monitorização/linha de base | Não |  Não |
| monitorização/correção | Não |  Não |
| Políticas | Não |  Não |
| preços | Não |  Não |
| securityContacts | Não |  Não |
| securitySolutions | Não |  Não |
| securitySolutionsReferenceData | Não |  Não |
| securityStatus | Não |  Não |
| securityStatus/pontos finais | Não |  Não |
| securityStatus/sub-redes | Não |  Não |
| securityStatus/virtualMachines | Não |  Não |
| securityStatuses | Não |  Não |
| securityStatusesSummaries | Não |  Não |
| settings | Não |  Não |
| tarefas | Não |  Não |
| Topologias | Não |  Não |
| workspaceSettings | Não |  Não |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Não |  Não |
| diagnosticSettingsCategories | Não |  Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| namespaces | Sim | Não |
| namespaces/authorizationrules | Não |  Não |
| namespaces/disasterrecoveryconfigs | Não |  Não |
| namespaces/eventgridfilters | Não |  Não |
| namespaces/queues | Não |  Não |
| namespaces/queues/authorizationrules | Não |  Não |
| namespaces/topics | Não |  Não |
| namespaces/topics/authorizationrules | Não |  Não |
| namespaces/topics/subscriptions | Não |  Não |
| namespaces/topics/subscriptions/rules | Não |  Não |
| premiumMessagingRegions | Não |  Não |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Clusters | Sim | Sim |
| clusters/applications | Não |  Não |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| aplicações | Sim | Sim |
| gateways | Sim | Sim |
| Redes | Sim | Sim |
| Segredos | Sim | Sim |
| volumes | Sim | Sim |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| SignalR | Sim | Sim |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| applianceDefinitions | Sim | Sim |
| aplicações | Sim | Sim |
| applicationDefinitions | Sim | Sim |
| aplicações | Sim | Sim |
| jitRequests | Sim | Sim |

## <a name="microsoftsql"></a>Microsoft.SQL
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| managedInstances | Sim | Sim |
| managedInstances/databases | Sim (ver nota abaixo) | Sim |
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
| servers/databases | Sim (ver nota abaixo) | Sim |
| servers/encryptionProtector | Não |  Não |
| servers/firewallRules | Não |  Não |
| servers/keys | Não |  Não |
| servers/restorableDroppedDatabases | Não |  Não |
| servidores/serviceobjectives | Não |  Não |
| servers/tdeCertificates | Não |  Não |

> [!NOTE]
> O mestre de base de dados não suporta etiquetas, mas outras bases de dados, incluindo bases de dados do Azure SQL Data Warehouse suportam etiquetas. Bases de dados de armazém de dados SQL do Azure tem de estar no Active Directory (não em pausa) Estado.


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| SqlVirtualMachineGroups | Sim | Sim |
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Não |  Não |
| SqlVirtualMachines | Sim | Sim |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| storageAccounts | Sim | Sim |
| storageAccounts/blobServices | Não |  Não |
| storageAccounts/fileServices | Não |  Não |
| storageAccounts/queueServices | Não |  Não |
| storageAccounts/services | Não |  Não |
| storageAccounts/tableServices | Não |  Não |
| Utilizações | Não |  Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| storageSyncServices | Sim | Sim |
| storageSyncServices/registeredServers | Não |  Não |
| storageSyncServices/syncGroups | Não |  Não |
| storageSyncServices/syncGroups/cloudEndpoints | Não |  Não |
| storageSyncServices/syncGroups/serverEndpoints | Não |  Não |
| storageSyncServices/workflows | Não |  Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| gerentes | Sim | Sim |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| streamingjobs | Sim (ver nota abaixo) | Sim |
| streamingjobs/diagnosticSettings | Não |  Não |

> [!NOTE]
> Não é possível adicionar uma etiqueta quando streamingjobs está em execução. Pare o recurso para adicionar uma etiqueta.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| CreateSubscription | Não |  Não |
| SubscriptionDefinitions | Não |  Não |
| SubscriptionOperations | Não |  Não |

## <a name="microsoftsupport"></a>microsoft.support
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| supporttickets | Não |  Não |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| providerRegistrations | Sim | Sim |
| Recursos | Sim | Sim |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Ambientes | Sim | Não |
| environments/accessPolicies | Não |  Não |
| ambientes/eventsources | Sim | Não |
| environments/referenceDataSets | Sim | Não |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| conta | Sim | Sim |
| extensão da conta / | Sim | Sim |
| conta/projeto | Sim | Sim |

## <a name="microsoftweb"></a>Microsoft.Web
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
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
| Certificados | Sim | Sim |
| connectionGateways | Sim | Sim |
| Ligações | Sim | Sim |
| customApis | Sim | Sim |
| deletedSites | Não |  Não |
| functions | Não |  Não |
| hostingEnvironments | Sim | Sim |
| hostingEnvironments/multiRolePools | Não |  Não |
| hostingEnvironments/multiRolePools/instances | Não |  Não |
| hostingEnvironments/workerPools | Não |  Não |
| hostingEnvironments/workerPools/instances | Não |  Não |
| publishingUsers | Não |  Não |
| Recomendações | Não |  Não |
| resourceHealthMetadata | Não |  Não |
| tempos de execução | Não |  Não |
| serverFarms | Sim | Sim |
| serverFarms/workers | Não |  Não |
| sites | Sim | Sim |
| sites/domainOwnershipIdentifiers | Não |  Não |
| sites/hostNameBindings | Não |  Não |
| sites/instances | Não |  Não |
| sites/instances/extensions | Não |  Não |
| sites/premieraddons | Sim | Sim |
| sites/recomendações | Não |  Não |
| sites/resourceHealthMetadata | Não |  Não |
| sites/slots | Sim | Sim |
| sites/slots/hostNameBindings | Não |  Não |
| sites/slots/instances | Não |  Não |
| sites/slots/instances/extensions | Não |  Não |
| sourceControls | Não |  Não |
| Validar | Não |  Não |
| verifyHostingEnvironmentVnet | Não |  Não |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Não |  Não |
| diagnosticSettingsCategories | Não |  Não |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| DeviceServices | Sim | Sim |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Tipo de recurso | Suporta etiquetas | Etiquetar no relatório de custos |
| ------------- | ----------- | ----------- |
| Componentes | Não |  Não |
| componentsSummary | Não |  Não |
| monitorInstances | Não |  Não |
| monitorInstancesSummary | Não |  Não |
| Monitores | Não |  Não |
| notificationSettings | Não |  Não |

## <a name="next-steps"></a>Passos Seguintes
Para saber como aplicar etiquetas a recursos, veja [utilizar etiquetas para organizar os recursos do Azure](resource-group-using-tags.md).
