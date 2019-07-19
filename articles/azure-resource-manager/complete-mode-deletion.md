---
title: Azure Resource Manager a exclusão do modo completo por tipo de recurso
description: Mostra como os tipos de recurso lidam com a exclusão de modo completo em modelos de Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 04/24/2019
ms.author: tomfitz
ms.openlocfilehash: 5ac442c0ae1e397fd1e8b58fdbcf61eb8712046c
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302848"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Exclusão de recursos do Azure para implantações de modo completo
Este artigo descreve como os tipos de recursos tratam da exclusão quando não estão em um modelo implantado no modo completo.

Os tipos de recurso marcados `Yes` com são excluídos quando o tipo não está no modelo implantado com o modo completo. 

Os tipos de recurso marcados `No` com não são excluídos automaticamente quando não estão no modelo; no entanto, eles serão excluídos se o recurso pai for excluído. Para obter uma descrição completa do comportamento, consulte [Azure Resource Manager modos de implantação](deployment-modes.md).


## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | DomainServices | Sim | 
> | DomainServices/oucontainer | Não | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | diagnosticSettings | Não | 
> | diagnosticSettingsCategories | Não | 

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
> | logs | Não | 
> | relatórios | Não | 
> | services | Não | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Figura | Não | 
> | generateRecommendations | Não | 
> | Recommendations | Não | 
> | supressões | Não | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | actionRules | Não | 
> | alerts | Não | 
> | alertas | Não | 
> | alertsSummary | Não | 
> | alertsSummaryList | Não | 
> | smartDetectorAlertRules | Não | 
> | smartDetectorRuntimeEnvironments | Não | 
> | smartGroups | Não | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Servidores | Sim | 

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | reportFeedback | Não | 
> | serviço | Sim | 
> | validateServiceName | Não | 

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
> | billingAccounts/billingProfiles | Não | 
> | billingAccounts/billingProfiles/billingSubscriptions | Não | 
> | billingAccounts/billingProfiles/invoices | Não | 
> | billingAccounts/billingProfiles/invoices/pricesheet | Não | 
> | billingAccounts/billingProfiles/operationStatus | Não | 
> | billingAccounts/billingProfiles/paymentMethods | Não | 
> | billingAccounts/billingProfiles/políticas | Não | 
> | billingAccounts/billingProfiles/pricesheet | Não | 
> | billingAccounts/billingProfiles/products | Não | 
> | billingAccounts/billingProfiles/transactions | Não | 
> | billingAccounts/billingSubscriptions | Não | 
> | billingAccounts/departments | Não | 
> | billingAccounts/eligibleOffers | Não | 
> | billingAccounts/enrollmentAccounts | Não | 
> | billingAccounts/faturas | Não | 
> | billingAccounts/invoiceSections | Não | 
> | billingAccounts/invoiceSections/billingSubscriptions | Não | 
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Não | 
> | billingAccounts/invoiceSections/importRequests | Não | 
> | billingAccounts/invoiceSections/initiateImportRequest | Não | 
> | billingAccounts/invoiceSections/initiateTransfer | Não | 
> | billingAccounts/invoiceSections/operationStatus | Não | 
> | billingAccounts/invoiceSections/products | Não | 
> | billingAccounts/invoiceSections/transfers | Não | 
> | billingAccounts/produtos | Não | 
> | billingAccounts/projetos | Não | 
> | billingAccounts/projects/billingSubscriptions | Não | 
> | billingAccounts/projects/importRequests | Não | 
> | billingAccounts/projects/initiateImportRequest | Não | 
> | billingAccounts/projects/operationStatus | Não | 
> | billingAccounts/projetos/produtos | Não | 
> | billingAccounts/transactions | Não | 
> | billingPeriods | Não | 
> | BillingPermissions | Não | 
> | billproperty | Não | 
> | BillingRoleAssignments | Não | 
> | BillingRoleDefinitions | Não | 
> | CreateBillingRoleAssignment | Não | 
> | departamentos | Não | 
> | enrollmentAccounts | Não | 
> | importRequests | Não | 
> | importRequests/acceptImportRequest | Não | 
> | importRequests/declineImportRequest | Não | 
> | faturas | Não | 
> | transferir | Não | 
> | transfers/acceptTransfer | Não | 
> | transferências/declineTransfer | Não | 
> | transferências/operationStatus | Não | 
> | usagePlans | Não | 

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
> | calculatePrice | Não | 
> | catálogos | Não | 
> | commercialReservationOrders | Não | 
> | reservationOrders | Não | 
> | reservationOrders/calculateRefund | Não | 
> | reservationOrders/merge | Não | 
> | reservationOrders/reservations | Não | 
> | reservationOrders/reservations/revisions | Não | 
> | reservationOrders/return | Não | 
> | reservationOrders/split | Não | 
> | reservationOrders/swap | Não | 
> | Reservas | Não | 
> | Os | Não | 
> | validateReservationOrder | Não | 

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
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
> | Técnicas | Não | 
> | domainNames | Não | 
> | domainNames/capabilities | Não | 
> | domainNames/internalLoadBalancers | Não | 
> | domainNames/serviceCertificates | Não | 
> | domainNames/slots | Não | 
> | domainNames/slots/roles | Não | 
> | moveSubscriptionResources | Não | 
> | operatingSystemFamilies | Não | 
> | operatingSystems | Não | 
> | quotas | Não | 
> | resourceType | Não | 
> | validateSubscriptionMoveAvailability | Não | 
> | VirtualMachines | Não | 
> | virtualMachines/diagnosticSettings | Não | 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | classicInfrastructureResources | Não | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Técnicas | Não | 
> | expressRouteCrossConnections | Não | 
> | expressRouteCrossConnections/peerings | Não | 
> | gatewaySupportedDevices | Não | 
> | networkSecurityGroups | Não | 
> | quotas | Não | 
> | reservedIps | Não | 
> | virtualNetworks | Não | 
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Não | 
> | virtualNetworks/virtualNetworkPeerings | Não | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Técnicas | Não | 
> | Discos | Não | 
> | imagens | Não | 
> | osImages | Não | 
> | osPlatformImages | Não | 
> | publicImages | Não | 
> | quotas | Não | 
> | storageAccounts | Não | 
> | storageAccounts/services | Não | 
> | storageAccounts/services/diagnosticSettings | Não | 
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
> | Discos | Sim | 
> | imagens | Sim | 
> | restorePointCollections | Sim | 
> | restorePointCollections/restorePoints | Não | 
> | sharedVMImages | Sim | 
> | sharedVMImages/versions | Sim | 
> | instantâneos | Sim | 
> | VirtualMachines | Sim | 
> | virtualMachines/diagnosticSettings | Não | 
> | virtualMachines/extensões | Sim | 
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
> | Contabiliza | Não | 
> | CostTags | Não | 
> | crédito | Não | 
> | eventos | Não | 
> | Previsões | Não | 
> | muitas | Não | 
> | Marketplaces | Não | 
> | Pricesheets | Não | 
> | produto | Não | 
> | ReservationDetails | Não | 
> | ReservationRecommendations | Não | 
> | ReservationSummaries | Não | 
> | ReservationTransactions | Não | 
> | Tags | Não | 
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
> | accounts | Sim | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Alertas | Não | 
> | billingAccounts | Não | 
> | Conectores | Sim | 
> | departamentos | Não | 
> | Dimensões | Não | 
> | enrollmentAccounts | Não | 
> | Consulta | Não | 
> | Registr | Não | 
> | Reportconfigs | Não | 
> | Relatórios | Não | 

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
> | Espaços | Sim | 
> | workspaces/virtualNetworkPeerings | Não | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | catálogos | Sim | 

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
> | dataFactorySchema | Não | 
> | fábricas | Sim | 
> | factories/integrationRuntimes | Não | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim | 
> | accounts/dataLakeStoreAccounts | Não | 
> | accounts/storageAccounts | Não | 
> | accounts/storageAccounts/containers | Não | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim | 
> | accounts/eventGridFilters | Não | 
> | accounts/firewallRules | Não | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | services | Sim | 
> | services/projects | Sim | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Servidores | Sim | 
> | servers/recoverableServers | Não | 
> | servers/virtualNetworkRules | Não | 

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Servidores | Sim | 
> | servers/recoverableServers | Não | 
> | servers/virtualNetworkRules | Não | 

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Servidores | Sim | 
> | servidores/consultores | Não | 
> | servers/queryTexts | Não | 
> | servers/recoverableServers | Não | 
> | servers/topQueryStatistics | Não | 
> | servers/virtualNetworkRules | Não | 
> | servers/waitStatistics | Não | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | IotHubs | Sim | 
> | IotHubs/eventGridFilters | Não | 
> | ProvisioningServices | Sim | 
> | usos | Não | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Controladores | Sim | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | prático | Sim | 
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
> | Os | Não | 
> | retrieveresourcesbyid | Não | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | guestConfigurationAssignments | Não | 
> | Antivírus | Não | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | hanaInstances | Sim | 

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | clusters | Sim | 
> | clusters/aplicativos | Não | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | sejam | Sim | 

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | labelGroups | Não | 
> | labelGroups/labels | Não | 
> | labelGroups/labels/conditions | Não | 
> | labelGroups/rótulos/subrótulos | Não | 
> | labelGroups/labels/subLabels/conditions | Não | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | actiongroups | Sim | 
> | activityLogAlerts | Sim | 
> | alertrules | Sim | 
> | automatedExportSettings | Não | 
> | autoscalesettings | Sim | 
> | base | Não | 
> | calculatebaseline | Não | 
> | QC | Sim | 
> | componentes/eventos | Não | 
> | componentes/pricingPlans | Não | 
> | components/query | Não | 
> | diagnosticSettings | Não | 
> | diagnosticSettingsCategories | Não | 
> | eventCategories | Não | 
> | EventTypes | Não | 
> | extendedDiagnosticSettings | Não | 
> | logDefinitions | Não | 
> | logprofiles | Não | 
> | logs | Não | 
> | metricAlerts | Sim |
> | migrateToNewPricingModel | Não | 
> | minhas pastas de trabalho | Não | 
> | consultas de | Não | 
> | rollbackToLegacyPricingModel | Não | 
> | scheduledqueryrules | Sim | 
> | vmInsightsOnboardingStatuses | Não | 
> | webtests | Sim | 
> | Pastas | Sim | 

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
> | cofres | Sim | 
> | vaults/accessPolicies | Não | 
> | cofres/segredos | Não | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | clusters | Sim | 
> | clusters/bancos de dados | Não | 
> | clusters/bancos de dados/conexões DataConnections | Não | 
> | clusters/bancos de dados/eventhubconnections | Não | 

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | labaccounts | Sim | 
> | utilizadores | Não | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim | 

## <a name="microsoftloganalytics"></a>Microsoft. LogAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | logs | Não | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | integrationAccounts | Sim | 
> | fluxos | Sim | 

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | commitmentPlans | Sim | 
> | webServices | Sim | 
> | Áreas de Trabalho | Sim | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim | 
> | contas/espaços de trabalho | Sim | 
> | accounts/workspaces/projects | Sim | 
> | teamAccounts | Sim | 
> | teamAccounts/workspaces | Sim | 
> | teamAccounts/workspaces/projects | Sim | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Espaços | Sim | 
> | espaços de trabalho/computações | Não | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Identities | Não | 
> | userAssignedIdentities | Sim | 

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | getEntities | Não | 
> | managementGroups | Não | 
> | Os | Não | 
> | startTenantBackfill | Não | 
> | tenantBackfillStatus | Não | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | accounts | Sim | 
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
> | produto | Não | 

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

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | projeto | Sim | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | applicationGateways | Sim | 
> | applicationSecurityGroups | Sim | 
> | azureFirewallFqdnTags | Não | 
> | azureFirewalls | Sim | 
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
> | dnszones/recordsets | Não | 
> | dnszones/SOA | Não | 
> | dnszones/SRV | Não | 
> | dnszones/TXT | Não | 
> | expressRouteCircuits | Sim | 
> | expressRouteServiceProviders | Não | 
> | frontdoors | Sim | 
> | frontdoorWebApplicationFirewallPolicies | Sim | 
> | getDnsResourceReference | Não | 
> | interfaceEndpoints | Sim | 
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
> | privateLinkServices | Sim | 
> | publicIPAddresses | Sim | 
> | publicIPPrefixes | Sim | 
> | routeFilters | Sim | 
> | routeTables | Sim | 
> | serviceEndpointPolicies | Sim | 
> | trafficManagerGeographicHierarchies | Não | 
> | trafficmanagerprofiles | Sim | 
> | trafficmanagerprofiles/heatMaps | Não | 
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

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | dispositivos | Não | 
> | linkTargets | Não | 
> | storageInsightConfigs | Não | 
> | Espaços | Sim | 
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
> | Modos de exibição | Sim | 

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
> | Painéis | Sim | 
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

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

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

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | Os | Não | 
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
> | Notificações | Não | 

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | implantações | Não | 
> | implantações/operações | Não | 
> | Vincule | Não | 
> | notifyResourceJobs | Não | 
> | Fornecedores | Não | 
> | resourceGroups | Não | 
> | Os | Não | 
> | assinaturas | Não | 
> | assinaturas/provedores | Não | 
> | subscriptions/resourceGroups | Não | 
> | subscriptions/resourcegroups/resources | Não | 
> | assinaturas/recursos | Não | 
> | assinaturas/TagNames | Não | 
> | subscriptions/tagNames/tagValues | Não | 
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
> | fluxo | Sim | 
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
> | advancedThreatProtectionSettings | Não | 
> | alerts | Não | 
> | allowedConnections | Não | 
> | dispositivos | Não | 
> | applicationWhitelistings | Não | 
> | AutoProvisioningSettings | Não | 
> | Conformidades | Não | 
> | dataCollectionAgents | Não | 
> | discoveredSecuritySolutions | Não | 
> | externalSecuritySolutions | Não | 
> | InformationProtectionPolicies | Não | 
> | jitNetworkAccessPolicies | Não | 
> | Monitoriza | Não | 
> | monitoramento/Antimalware | Não | 
> | monitoramento/linha de base | Não | 
> | monitoramento/patch | Não | 
> | Policie | Não | 
> | preços | Não | 
> | securityContacts | Não | 
> | securitySolutions | Não | 
> | securitySolutionsReferenceData | Não | 
> | securityStatus | Não | 
> | securityStatus/pontos de extremidade | Não | 
> | securityStatus/sub-redes | Não | 
> | securityStatus/virtualMachines | Não | 
> | securityStatuses | Não | 
> | securityStatusesSummaries | Não | 
> | settings | Não | 
> | tarefas | Não | 
> | topologias | Não | 
> | workspaceSettings | Não | 

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | diagnosticSettings | Não | 
> | diagnosticSettingsCategories | Não | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | namespaces | Sim | 
> | namespaces/authorizationrules | Não | 
> | namespaces/disasterrecoveryconfigs | Não | 
> | namespaces/eventgridfilters | Não | 
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
> | clusters | Sim | 
> | clusters/aplicativos | Não | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | aplicações | Sim | 
> | gateways | Sim | 
> | às | Sim | 
> | confidenciais | Sim | 
> | volumes | Sim | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | SignalR | Sim | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | applianceDefinitions | Sim | 
> | dispositivos | Sim | 
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
> | Servidores | Sim | 
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
> | storageAccounts/tableServices | Não | 
> | usos | Não | 

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
> | streamingjobs/diagnosticSettings | Não | 

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | CreateSubscription | Não | 
> | SubscriptionDefinitions | Não | 
> | SubscriptionOperations | Não | 

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | supporttickets | Não | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | providerRegistrations | Sim | 
> | Os | Sim | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | sistemas | Sim | 
> | environments/accessPolicies | Não | 
> | ambientes/EventSources | Sim | 
> | environments/referenceDataSets | Sim | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Eliminação completa de modo |
> | ------------- | ----------- |
> | conta | Sim | 
> | conta/extensão | Sim | 
> | conta/projeto | Sim | 

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
> | Certificado | Sim | 
> | connectionGateways | Sim | 
> | conexões | Sim | 
> | customApis | Sim | 
> | deletedSites | Não | 
> | functions | Não | 
> | hostingEnvironments | Sim | 
> | hostingEnvironments/multiRolePools | Não | 
> | hostingEnvironments/multiRolePools/instâncias | Não | 
> | hostingEnvironments/workerPools | Não | 
> | hostingEnvironments/workerPools/instances | Não | 
> | publishingUsers | Não | 
> | Recommendations | Não | 
> | resourceHealthMetadata | Não | 
> | tempos | Não | 
> | serverFarms | Sim | 
> | serverFarms/workers | Não | 
> | sites | Sim | 
> | sites/domainOwnershipIdentifiers | Não | 
> | sites/hostNameBindings | Não | 
> | sites/instâncias | Não | 
> | sites/instances/extensions | Não | 
> | sites/premieraddons | Sim | 
> | sites/recomendações | Não | 
> | sites/resourceHealthMetadata | Não | 
> | sites/Slots | Sim | 
> | sites/slots/hostNameBindings | Não | 
> | sites/slots/instances | Não | 
> | sites/slots/instances/extensions | Não | 
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