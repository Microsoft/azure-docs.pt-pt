---
title: Eliminação de modo completa do Gestor de recursos do Azure por tipo de recurso
description: Mostra como tipos de recurso lidar com a eliminação de modo completa em modelos Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 02/13/2019
ms.author: tomfitz
ms.openlocfilehash: fded37fee844a01f4d51518f2ca56dcf575704b2
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60150154"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Eliminação de recursos do Azure para implementações no modo concluída
Este artigo descreve como tipos de recurso lidar com a eliminação quando não estiver num modelo que é implementada no modo de conclusão.

Os tipos de recursos marcados com `Yes` são eliminados quando o tipo não está no modelo implementado com o modo de conclusão. 

Os tipos de recursos marcados com `No` não são automaticamente eliminados quando não estiver no modelo; no entanto, sendo excluído se o recurso principal é eliminado. Para obter uma descrição completa do comportamento, consulte [modos de implementação Azure Resource Manager](deployment-modes.md).

Para obter os mesmos dados como um ficheiro de valores separados por vírgulas, transfira [concluir-modo-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| DomainServices | Sim | 
| DomainServices/oucontainer | Não | 

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| diagnosticSettings | Não | 
| diagnosticSettingsCategories | Não | 

## <a name="microsoftaddons"></a>Microsoft.Addons
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| supportProviders | Não | 

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| aadsupportcases | Não | 
| addsservices | Não | 
| agentes | Não | 
| anonymousapiusers | Não | 
| configuração | Não | 
| registos | Não | 
| relatórios | Não | 
| services | Não | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| Configurações | Não | 
| generateRecommendations | Não | 
| Recomendações | Não | 
| supressão | Não | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| actionRules | Não | 
| alerts | Não | 
| alertsList | Não | 
| alertsSummary | Não | 
| alertsSummaryList | Não | 
| smartDetectorAlertRules | Não | 
| smartDetectorRuntimeEnvironments | Não | 
| smartGroups | Não | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| servidores | Sim | 

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| reportFeedback | Não | 
| serviço | Sim | 
| validateServiceName | Não | 

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| attestationProviders | Não | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| classicAdministrators | Não | 
| denyAssignments | Não | 
| elevateAccess | Não | 
| Bloqueios | Não | 
| permissões | Não | 
| policyAssignments | Não | 
| policyDefinitions | Não | 
| policySetDefinitions | Não | 
| providerOperations | Não | 
| roleAssignments | Não | 
| roleDefinitions | Não | 

## <a name="microsoftautomation"></a>Microsoft.Automation
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| automationAccounts | Sim | 
| automationAccounts/configurations | Sim | 
| automationAccounts/jobs | Não | 
| automationAccounts/runbooks | Sim | 
| automationAccounts/softwareUpdateConfigurations | Não | 
| automationAccounts/webhooks | Não | 

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| Ambientes | Não | 
| ambientes/contas | Não | 
| environments/accounts/namespaces | Não | 
| environments/accounts/namespaces/configurations | Não | 

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| b2cDirectories | Sim | 

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| registos | Sim | 
| registrations/customerSubscriptions | Não | 
| registos/produtos | Não | 

## <a name="microsoftbatch"></a>Microsoft.Batch
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| batchAccounts | Sim | 

## <a name="microsoftbilling"></a>Microsoft.Billing
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| billingAccounts | Não | 
| billingAccounts/billingProfiles | Não | 
| billingAccounts/billingProfiles/billingSubscriptions | Não | 
| billingAccounts/billingProfiles/invoices | Não | 
| billingAccounts/billingProfiles/invoices/pricesheet | Não | 
| billingAccounts/billingProfiles/operationStatus | Não | 
| billingAccounts/billingProfiles/paymentMethods | Não | 
| billingAccounts/billingProfiles/policies | Não | 
| billingAccounts/billingProfiles/pricesheet | Não | 
| billingAccounts/billingProfiles/products | Não | 
| billingAccounts/billingProfiles/transactions | Não | 
| billingAccounts/billingSubscriptions | Não | 
| billingAccounts/departments | Não | 
| billingAccounts/eligibleOffers | Não | 
| billingAccounts/enrollmentAccounts | Não | 
| billingAccounts/invoices | Não | 
| billingAccounts/invoiceSections | Não | 
| billingAccounts/invoiceSections/billingSubscriptions | Não | 
| billingAccounts/invoiceSections/billingSubscriptions/transfer | Não | 
| billingAccounts/invoiceSections/importRequests | Não | 
| billingAccounts/invoiceSections/initiateImportRequest | Não | 
| billingAccounts/invoiceSections/initiateTransfer | Não | 
| billingAccounts/invoiceSections/operationStatus | Não | 
| billingAccounts/invoiceSections/products | Não | 
| billingAccounts/invoiceSections/transfers | Não | 
| billingAccounts/products | Não | 
| billingAccounts/projects | Não | 
| billingAccounts/projects/billingSubscriptions | Não | 
| billingAccounts/projects/importRequests | Não | 
| billingAccounts/projects/initiateImportRequest | Não | 
| billingAccounts/projects/operationStatus | Não | 
| billingAccounts/projects/products | Não | 
| billingAccounts/transactions | Não | 
| billingPeriods | Não | 
| BillingPermissions | Não | 
| billingProperty | Não | 
| BillingRoleAssignments | Não | 
| BillingRoleDefinitions | Não | 
| CreateBillingRoleAssignment | Não | 
| Departamentos | Não | 
| enrollmentAccounts | Não | 
| importRequests | Não | 
| importRequests/acceptImportRequest | Não | 
| importRequests/declineImportRequest | Não | 
| Notas fiscais | Não | 
| Transferências | Não | 
| transfers/acceptTransfer | Não | 
| transfers/declineTransfer | Não | 
| transfers/operationStatus | Não | 
| usagePlans | Não | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| mapApis | Sim | 
| updateCommunicationPreference | Não | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| BizTalk | Sim | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| blueprintAssignments | Não | 
| blueprintAssignments/assignmentOperations | Não | 
| blueprintAssignments/operations | Não | 
| planos gráficos | Não | 
| blueprints/artifacts | Não | 
| blueprints/versions | Não | 
| blueprints/versions/artifacts | Não | 

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| botServices | Sim | 
| botServices/channels | Não | 
| botServices/connections | Não | 

## <a name="microsoftcache"></a>Microsoft.Cache
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| Redis | Sim | 
| RedisConfigDefinition | Não | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| appliedReservations | Não | 
| calculatePrice | Não | 
| catálogos | Não | 
| commercialReservationOrders | Não | 
| reservationOrders | Não | 
| reservationOrders/calculateRefund | Não | 
| reservationOrders/merge | Não | 
| reservationOrders/reservations | Não | 
| reservationOrders/reservations/revisions | Não | 
| reservationOrders/return | Não | 
| reservationOrders/split | Não | 
| reservationOrders/swap | Não | 
| reservas | Não | 
| recursos | Não | 
| validateReservationOrder | Não | 

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| edgenodes | Não | 
| Perfis | Sim | 
| perfis/pontos finais | Sim | 
| profiles/endpoints/customdomains | Não | 
| perfis/pontos finais/origens | Não | 
| validateProbe | Não | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| certificateOrders | Sim | 
| certificateOrders/certificates | Não | 
| validateCertificateRegistrationInformation | Não | 

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| Capacidades | Não | 
| domainNames | Não | 
| domainNames/capabilities | Não | 
| domainNames/internalLoadBalancers | Não | 
| domainNames/serviceCertificates | Não | 
| domainNames/slots | Não | 
| domainNames/slots/roles | Não | 
| moveSubscriptionResources | Não | 
| operatingSystemFamilies | Não | 
| operatingSystems | Não | 
| quotas | Não | 
| resourceTypes | Não | 
| validateSubscriptionMoveAvailability | Não | 
| virtualMachines | Não | 
| virtualMachines/diagnosticSettings | Não | 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| classicInfrastructureResources | Não | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| Capacidades | Não | 
| expressRouteCrossConnections | Não | 
| expressRouteCrossConnections/peerings | Não | 
| gatewaySupportedDevices | Não | 
| networkSecurityGroups | Não | 
| quotas | Não | 
| reservedIps | Não | 
| virtualNetworks | Não | 
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Não | 
| virtualNetworks/virtualNetworkPeerings | Não | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| Capacidades | Não | 
| discos | Não | 
| imagens | Não | 
| osImages | Não | 
| osPlatformImages | Não | 
| publicImages | Não | 
| quotas | Não | 
| storageAccounts | Não | 
| storageAccounts/services | Não | 
| storageAccounts/services/diagnosticSettings | Não | 
| storageAccounts/vmImages | Não | 
| vmImages | Não | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| accounts | Sim | 

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| RateCard | Não | 
| UsageAggregates | Não | 

## <a name="microsoftcompute"></a>Microsoft.Compute
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| availabilitySets | Sim | 
| discos | Sim | 
| imagens | Sim | 
| restorePointCollections | Sim | 
| restorePointCollections/restorePoints | Não | 
| sharedVMImages | Sim | 
| sharedVMImages/versions | Sim | 
| instantâneos | Sim | 
| virtualMachines | Sim | 
| virtualMachines/diagnosticSettings | Não | 
| virtualMachines/extensions | Sim | 
| virtualMachineScaleSets | Sim | 
| virtualMachineScaleSets/extensions | Não | 
| virtualMachineScaleSets/networkInterfaces | Não | 
| virtualMachineScaleSets/publicIPAddresses | Não | 
| virtualMachineScaleSets/virtualMachines | Não | 
| virtualMachineScaleSets/virtualMachines/networkInterfaces | Não | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| AggregatedCost | Não | 
| Saldos | Não | 
| Orçamentos | Não | 
| Encargos | Não | 
| CostTags | Não | 
| Créditos | Não | 
| eventos | Não | 
| Previsões | Não | 
| muitos | Não | 
| Marketplaces | Não | 
| Pricesheets | Não | 
| produtos | Não | 
| ReservationDetails | Não | 
| ReservationRecommendations | Não | 
| ReservationSummaries | Não | 
| ReservationTransactions | Não | 
| Etiquetas | Não | 
| Termos | Não | 
| UsageDetails | Não | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| containerGroups | Sim | 
| serviceAssociationLinks | Não | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| registos | Sim | 
| registos/compilações | Não | 
| registries/builds/cancel | Não | 
| registries/builds/getLogLink | Não | 
| registries/buildTasks | Sim | 
| registries/buildTasks/steps | Não | 
| registries/eventGridFilters | Não | 
| registries/getBuildSourceUploadUrl | Não | 
| registries/GetCredentials | Não | 
| registries/importImage | Não | 
| registries/queueBuild | Não | 
| registries/regenerateCredential | Não | 
| registries/regenerateCredentials | Não | 
| registries/replications | Sim | 
| registries/runs | Não | 
| registries/runs/cancel | Não | 
| registries/scheduleRun | Não | 
| registos/tarefas | Sim | 
| registries/updatePolicies | Não | 
| registries/webhooks | Sim | 
| registries/webhooks/getCallbackConfig | Não | 
| registries/webhooks/ping | Não | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| containerServices | Sim | 
| managedClusters | Sim | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| aplicações | Sim | 
| updateCommunicationPreference | Não | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| accounts | Sim | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| Alertas | Não | 
| BillingAccounts | Não | 
| Conectores | Sim | 
| Departamentos | Não | 
| Dimensões | Não | 
| EnrollmentAccounts | Não | 
| Consulta | Não | 
| Registre-se | Não | 
| Reportconfigs | Não | 
| Relatórios | Não | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| hubs | Sim | 
| hubs/authorizationPolicies | Não | 
| os hubs/conectores | Não | 
| hubs/connectors/mappings | Não | 
| hubs/interactions | Não | 
| hubs/kpi | Não | 
| hubs/links | Não | 
| hubs/profiles | Não | 
| hubs/roleAssignments | Não | 
| hubs/roles | Não | 
| hubs/suggestTypeSchema | Não | 
| hubs/views | Não | 
| hubs/widgetTypes | Não | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| tarefas | Sim | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| DataBoxEdgeDevices | Sim | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| áreas de trabalho | Sim | 
| workspaces/virtualNetworkPeerings | Não | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| catálogos | Sim | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| connectionManagers | Sim | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| dataFactories | Sim | 
| dataFactories/diagnosticSettings | Não | 
| dataFactorySchema | Não | 
| fábricas | Sim | 
| factories/integrationRuntimes | Não | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| accounts | Sim | 
| accounts/dataLakeStoreAccounts | Não | 
| accounts/storageAccounts | Não | 
| accounts/storageAccounts/containers | Não | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| accounts | Sim | 
| accounts/eventGridFilters | Não | 
| accounts/firewallRules | Não | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| services | Sim | 
| services/projects | Sim | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| servidores | Sim | 
| servers/recoverableServers | Não | 
| servers/virtualNetworkRules | Não | 

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| servidores | Sim | 
| servers/recoverableServers | Não | 
| servers/virtualNetworkRules | Não | 

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| servidores | Sim | 
| servers/advisors | Não | 
| servers/queryTexts | Não | 
| servers/recoverableServers | Não | 
| servers/topQueryStatistics | Não | 
| servers/virtualNetworkRules | Não | 
| servers/waitStatistics | Não | 

## <a name="microsoftdevices"></a>Microsoft.Devices
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| IotHubs | Sim | 
| IotHubs/eventGridFilters | Não | 
| ProvisioningServices | Sim | 
| Utilizações | Não | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| Controladores | Sim | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| Laboratórios | Sim | 
| labs/serviceRunners | Sim | 
| labs/virtualMachines | Sim | 
| Agendas | Sim | 

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| databaseAccountNames | Não | 
| databaseAccounts | Sim | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| domínios | Sim | 
| domains/domainOwnershipIdentifiers | Não | 
| generateSsoRequest | Não | 
| topLevelDomains | Não | 
| validateDomainRegistrationInformation | Não | 

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| lcsprojects | Não | 
| lcsprojects/clouddeployments | Não | 
| lcsprojects/conectores | Não | 

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| domínios | Sim | 
| domínios/tópicos | Não | 
| eventSubscriptions | Não | 
| extensionTopics | Não | 
| Tópicos | Sim | 
| topicTypes | Não | 

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| Clusters | Sim | 
| namespaces | Sim | 
| namespaces/authorizationrules | Não | 
| namespaces/disasterrecoveryconfigs | Não | 
| namespaces/eventhubs | Não | 
| namespaces/eventhubs/authorizationrules | Não | 
| namespaces/eventhubs/consumergroups | Não | 

## <a name="microsoftfeatures"></a>Microsoft.Features
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| elástica | Não | 
| Fornecedores | Não | 

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| Inscrever | Não | 
| galleryitems | Não | 
| generateartifactaccessuri | Não | 
| myareas | Não | 
| myareas/areas | Não | 
| myareas/areas/areas | Não | 
| myareas/areas/areas/galleryitems | Não | 
| myareas/areas/galleryitems | Não | 
| myareas/galleryitems | Não | 
| Registre-se | Não | 
| recursos | Não | 
| retrieveresourcesbyid | Não | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| guestConfigurationAssignments | Não | 
| software | Não | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| hanaInstances | Sim | 

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| Clusters | Não | 
| clusters/applications | Não | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| tarefas | Sim | 

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| labelGroups | Não | 
| labelGroups/labels | Não | 
| labelGroups/labels/conditions | Não | 
| labelGroups/labels/subLabels | Não | 
| labelGroups/labels/subLabels/conditions | Não | 

## <a name="microsoftinsights"></a>microsoft.insights
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| actiongroups | Sim | 
| activityLogAlerts | Sim | 
| alertrules | Sim | 
| automatedExportSettings | Não | 
| autoscalesettings | Sim | 
| linha de base | Não | 
| calculatebaseline | Não | 
| Componentes | Sim | 
| componentes/eventos | Não | 
| components/pricingPlans | Não | 
| components/query | Não | 
| diagnosticSettings | Não | 
| diagnosticSettingsCategories | Não | 
| eventCategories | Não | 
| eventtypes | Não | 
| extendedDiagnosticSettings | Não | 
| logDefinitions | Não | 
| logprofiles | Não | 
| registos | Não | 
| metricAlerts | Sim |
| migrateToNewPricingModel | Não | 
| myWorkbooks | Não | 
| consultas de | Não | 
| rollbackToLegacyPricingModel | Não | 
| scheduledqueryrules | Sim | 
| vmInsightsOnboardingStatuses | Não | 
| testes Web | Sim | 
| pastas de trabalho | Sim | 

## <a name="microsoftintune"></a>Microsoft.Intune
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| diagnosticSettings | Não | 
| diagnosticSettingsCategories | Não | 

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| IoTApps | Sim | 

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| Graph | Sim | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| deletedVaults | Não | 
| cofres | Sim | 
| vaults/accessPolicies | Não | 
| Cofres/segredos | Não | 

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| Clusters | Sim | 
| clusters/databases | Não | 
| clusters/databases/dataconnections | Não | 
| clusters/databases/eventhubconnections | Não | 

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| labaccounts | Sim | 
| utilizadores | Não | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| accounts | Sim | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| accounts | Sim | 

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| registos | Não | 

## <a name="microsoftlogic"></a>Microsoft.Logic
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| integrationAccounts | Sim | 
| Fluxos de trabalho | Sim | 

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| commitmentPlans | Sim | 
| webServices | Sim | 
| Áreas de Trabalho | Sim | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| accounts | Sim | 
| accounts/workspaces | Sim | 
| accounts/workspaces/projects | Sim | 
| teamAccounts | Sim | 
| teamAccounts/workspaces | Sim | 
| teamAccounts/workspaces/projects | Sim | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| accounts | Sim | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| áreas de trabalho | Sim | 
| workspaces/computes | Não | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| Identidades | Não | 
| userAssignedIdentities | Sim | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| getEntities | Não | 
| managementGroups | Não | 
| recursos | Não | 
| startTenantBackfill | Não | 
| tenantBackfillStatus | Não | 

## <a name="microsoftmaps"></a>Microsoft.Maps
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| accounts | Sim | 
| accounts/eventGridFilters | Não | 

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| Ofertas | Não | 
| offerTypes | Não | 
| offerTypes/publishers | Não | 
| offerTypes/publishers/offers | Não | 
| offerTypes/publishers/offers/plans | Não | 
| offerTypes/publishers/offers/plans/agreements | Não | 
| offerTypes/publishers/offers/plans/configs | Não | 
| offerTypes/publishers/offers/plans/configs/importImage | Não | 
| privategalleryitems | Não | 
| produtos | Não | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| classicDevServices | Sim | 
| updateCommunicationPreference | Não | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| Contratos | Não | 
| offertypes | Não | 

## <a name="microsoftmedia"></a>Microsoft.Media
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| mediaservices | Sim | 
| mediaservices/accountFilters | Não | 
| mediaservices/assets | Não | 
| mediaservices/assets/assetFilters | Não | 
| mediaservices/contentKeyPolicies | Não | 
| mediaservices/eventGridFilters | Não | 
| mediaservices/liveEventOperations | Não | 
| mediaservices/liveEvents | Sim | 
| mediaservices/liveEvents/liveOutputs | Não | 
| mediaservices/liveOutputOperations | Não | 
| mediaservices/streamingEndpointOperations | Não | 
| mediaservices/streamingEndpoints | Sim | 
| mediaservices/streamingLocators | Não | 
| mediaservices/streamingPolicies | Não | 
| mediaservices/transforms | Não | 
| mediaservices/transforms/jobs | Não | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| projetos | Sim | 

## <a name="microsoftnetwork"></a>Microsoft.Network
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| applicationGateways | Sim | 
| applicationSecurityGroups | Sim | 
| azureFirewallFqdnTags | Não | 
| azureFirewalls | Sim | 
| bgpServiceCommunities | Não | 
| ligações | Sim | 
| ddosCustomPolicies | Sim | 
| ddosProtectionPlans | Sim | 
| dnsOperationStatuses | Não | 
| dnszones | Sim | 
| dnszones/A | Não | 
| dnszones/AAAA | Não | 
| dnszones/all | Não | 
| dnszones/CAA | Não | 
| dnszones/CNAME | Não | 
| dnszones/MX | Não | 
| dnszones/NS | Não | 
| dnszones/PTR | Não | 
| dnszones/recordsets | Não | 
| dnszones/SOA | Não | 
| dnszones/SRV | Não | 
| dnszones/TXT | Não | 
| expressRouteCircuits | Sim | 
| expressRouteServiceProviders | Não | 
| frontdoors | Sim | 
| frontdoorWebApplicationFirewallPolicies | Sim | 
| getDnsResourceReference | Não | 
| interfaceEndpoints | Sim | 
| internalNotify | Não | 
| loadBalancers | Sim | 
| localNetworkGateways | Sim | 
| natGateways | Sim | 
| networkIntentPolicies | Sim | 
| networkInterfaces | Sim | 
| networkProfiles | Sim | 
| networkSecurityGroups | Sim | 
| networkWatchers | Sim | 
| networkWatchers/connectionMonitors | Sim | 
| networkWatchers/lenses | Sim | 
| networkWatchers/pingMeshes | Sim | 
| privateLinkServices | Sim | 
| publicIPAddresses | Sim | 
| publicIPPrefixes | Sim | 
| routeFilters | Sim | 
| routeTables | Sim | 
| serviceEndpointPolicies | Sim | 
| trafficManagerGeographicHierarchies | Não | 
| trafficmanagerprofiles | Sim | 
| trafficmanagerprofiles/heatMaps | Não | 
| virtualHubs | Sim | 
| virtualNetworkGateways | Sim | 
| virtualNetworks | Sim | 
| virtualNetworkTaps | Sim | 
| virtualWans | Sim | 
| vpnGateways | Sim | 
| vpnSites | Sim | 
| webApplicationFirewallPolicies | Sim | 

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| namespaces | Sim | 
| namespaces/notificationHubs | Sim | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| dispositivos | Não | 
| linkTargets | Não | 
| storageInsightConfigs | Não | 
| áreas de trabalho | Sim | 
| workspaces/dataSources | Não | 
| workspaces/linkedServices | Não | 
| workspaces/query | Não | 

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| managementassociations | Não | 
| managementconfigurations | Sim | 
| soluções | Sim | 
| visualizações | Sim | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| policyEvents | Não | 
| policyStates | Não | 
| policyTrackedResources | Não | 
| remediações | Não | 

## <a name="microsoftportal"></a>Microsoft.Portal
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| consolas | Não | 
| dashboards | Sim | 
| userSettings | Não | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| workspaceCollections | Sim | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| Capacidades | Sim | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| accounts | Sim | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| backupProtectedItems | Não | 
| cofres | Sim | 

## <a name="microsoftrelay"></a>Microsoft.Relay
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| namespaces | Sim | 
| namespaces/authorizationrules | Não | 
| namespaces/hybridconnections | Não | 
| namespaces/hybridconnections/authorizationrules | Não | 
| namespaces/wcfrelays | Não | 
| namespaces/wcfrelays/authorizationrules | Não | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| recursos | Não | 
| subscriptionsStatus | Não | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| availabilityStatuses | Não | 
| childAvailabilityStatuses | Não | 
| childResources | Não | 
| eventos | Não | 
| impactedResources | Não | 
| notificações | Não | 

## <a name="microsoftresources"></a>Microsoft.Resources
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| implementações | Não | 
| Implementações/operações | Não | 
| Ligações | Não | 
| notifyResourceJobs | Não | 
| Fornecedores | Não | 
| resourceGroups | Não | 
| recursos | Não | 
| subscrições | Não | 
| Subscrições/fornecedores | Não | 
| subscriptions/resourceGroups | Não | 
| subscriptions/resourcegroups/resources | Não | 
| subscriptions/resources | Não | 
| subscriptions/tagnames | Não | 
| subscriptions/tagNames/tagValues | Não | 
| Inquilinos | Não | 

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| aplicações | Sim | 
| saasresources | Não | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| fluxos | Sim | 
| jobcollections | Sim | 

## <a name="microsoftsearch"></a>Microsoft.Search
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| resourceHealthMetadata | Não | 
| searchServices | Sim | 

## <a name="microsoftsecurity"></a>Microsoft.Security
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| advancedThreatProtectionSettings | Não | 
| alerts | Não | 
| allowedConnections | Não | 
| aplicações | Não | 
| applicationWhitelistings | Não | 
| AutoProvisioningSettings | Não | 
| Regulamentares | Não | 
| dataCollectionAgents | Não | 
| discoveredSecuritySolutions | Não | 
| externalSecuritySolutions | Não | 
| InformationProtectionPolicies | Não | 
| jitNetworkAccessPolicies | Não | 
| monitorização | Não | 
| monitoring/antimalware | Não | 
| monitorização/linha de base | Não | 
| monitorização/correção | Não | 
| políticas | Não | 
| preços | Não | 
| securityContacts | Não | 
| securitySolutions | Não | 
| securitySolutionsReferenceData | Não | 
| securityStatus | Não | 
| securityStatus/pontos finais | Não | 
| securityStatus/sub-redes | Não | 
| securityStatus/virtualMachines | Não | 
| securityStatuses | Não | 
| securityStatusesSummaries | Não | 
| settings | Não | 
| tarefas | Não | 
| Topologias | Não | 
| workspaceSettings | Não | 

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| diagnosticSettings | Não | 
| diagnosticSettingsCategories | Não | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| namespaces | Sim | 
| namespaces/authorizationrules | Não | 
| namespaces/disasterrecoveryconfigs | Não | 
| namespaces/eventgridfilters | Não | 
| namespaces/queues | Não | 
| namespaces/queues/authorizationrules | Não | 
| namespaces/topics | Não | 
| namespaces/topics/authorizationrules | Não | 
| namespaces/topics/subscriptions | Não | 
| namespaces/topics/subscriptions/rules | Não | 
| premiumMessagingRegions | Não | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| Clusters | Sim | 
| clusters/applications | Não | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| aplicações | Sim | 
| gateways | Sim | 
| Redes | Sim | 
| segredos | Sim | 
| volumes | Sim | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| SignalR | Sim | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| applianceDefinitions | Sim | 
| aplicações | Sim | 
| applicationDefinitions | Sim | 
| aplicações | Sim | 
| jitRequests | Sim | 

## <a name="microsoftsql"></a>Microsoft.SQL
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| managedInstances | Sim |
| managedInstances/databases | Sim (ver nota abaixo) |
| managedInstances/databases/backupShortTermRetentionPolicies | Não |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | Não |
| managedInstances/databases/vulnerabilityAssessments | Não |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | Não |
| managedInstances/encryptionProtector | Não |
| managedInstances/keys | Não |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Não |
| managedInstances/vulnerabilityAssessments | Não |
| servidores | Sim | 
| servidores/administradores | Não | 
| servers/communicationLinks | Não | 
| servers/databases | Sim (ver nota abaixo) | 
| servers/encryptionProtector | Não | 
| servers/firewallRules | Não | 
| servers/keys | Não | 
| servers/restorableDroppedDatabases | Não | 
| servidores/serviceobjectives | Não | 
| servers/tdeCertificates | Não | 

> [!NOTE]
> O mestre de base de dados não suporta etiquetas, mas outras bases de dados, incluindo bases de dados do armazém de dados suportam etiquetas.


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| SqlVirtualMachineGroups | Sim | 
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Não | 
| SqlVirtualMachines | Sim | 

## <a name="microsoftstorage"></a>Microsoft.Storage
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| storageAccounts | Sim | 
| storageAccounts/blobServices | Não | 
| storageAccounts/fileServices | Não | 
| storageAccounts/queueServices | Não | 
| storageAccounts/services | Não | 
| storageAccounts/tableServices | Não | 
| Utilizações | Não | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| storageSyncServices | Sim | 
| storageSyncServices/registeredServers | Não | 
| storageSyncServices/syncGroups | Não | 
| storageSyncServices/syncGroups/cloudEndpoints | Não | 
| storageSyncServices/syncGroups/serverEndpoints | Não | 
| storageSyncServices/workflows | Não | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| gerentes | Sim | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| streamingjobs | Sim (ver nota abaixo) | 
| streamingjobs/diagnosticSettings | Não | 

> [!NOTE]
> Não é possível adicionar uma etiqueta quando streamingjobs está em execução. Pare o recurso para adicionar uma etiqueta.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| CreateSubscription | Não | 
| SubscriptionDefinitions | Não | 
| SubscriptionOperations | Não | 

## <a name="microsoftsupport"></a>microsoft.support
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| supporttickets | Não | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| providerRegistrations | Sim | 
| recursos | Sim | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| Ambientes | Sim | 
| environments/accessPolicies | Não | 
| ambientes/eventsources | Sim | 
| environments/referenceDataSets | Sim | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| conta | Sim | 
| extensão da conta / | Sim | 
| conta/projeto | Sim | 

## <a name="microsoftweb"></a>Microsoft.Web
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| apiManagementAccounts | Não | 
| apiManagementAccounts/apiAcls | Não | 
| apiManagementAccounts/apis | Não | 
| apiManagementAccounts/apis/apiAcls | Não | 
| apiManagementAccounts/apis/connectionAcls | Não | 
| apiManagementAccounts/apis/connections | Não | 
| apiManagementAccounts/apis/connections/connectionAcls | Não | 
| apiManagementAccounts/apis/localizedDefinitions | Não | 
| apiManagementAccounts/connectionAcls | Não | 
| apiManagementAccounts/connections | Não | 
| billingMeters | Não | 
| certificados | Sim | 
| connectionGateways | Sim | 
| ligações | Sim | 
| customApis | Sim | 
| deletedSites | Não | 
| functions | Não | 
| hostingEnvironments | Sim | 
| hostingEnvironments/multiRolePools | Não | 
| hostingEnvironments/multiRolePools/instances | Não | 
| hostingEnvironments/workerPools | Não | 
| hostingEnvironments/workerPools/instances | Não | 
| publishingUsers | Não | 
| Recomendações | Não | 
| resourceHealthMetadata | Não | 
| tempos de execução | Não | 
| serverFarms | Sim | 
| serverFarms/workers | Não | 
| sites | Sim | 
| sites/domainOwnershipIdentifiers | Não | 
| sites/hostNameBindings | Não | 
| sites/instances | Não | 
| sites/instances/extensions | Não | 
| sites/premieraddons | Sim | 
| sites/recomendações | Não | 
| sites/resourceHealthMetadata | Não | 
| sites/slots | Sim | 
| sites/slots/hostNameBindings | Não | 
| sites/slots/instances | Não | 
| sites/slots/instances/extensions | Não | 
| sourceControls | Não | 
| Validar | Não | 
| verifyHostingEnvironmentVnet | Não | 

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| diagnosticSettings | Não | 
| diagnosticSettingsCategories | Não | 

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| DeviceServices | Sim | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Tipo de recurso | Eliminação completa de modo |
| ------------- | ----------- |
| Componentes | Não | 
| componentsSummary | Não | 
| monitorInstances | Não | 
| monitorInstancesSummary | Não | 
| Monitores | Não | 
| notificationSettings | Não | 

## <a name="next-steps"></a>Passos Seguintes
Para saber como aplicar etiquetas a recursos, veja [utilizar etiquetas para organizar os recursos do Azure](resource-group-using-tags.md).
