---
title: Tipos de recursos extensão
description: Lista os tipos de recursos do Azure são usados para estender os recursos de outros tipos de recursos.
ms.topic: conceptual
ms.date: 10/24/2019
ms.openlocfilehash: af60bd648e41532cca364272fb0ded01c0c18239
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75484678"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Tipos de recursos que estendem funcionalidades de outros recursos

Um recurso de extensão é um recurso que o adiciona aos recursos de outro recurso. Por exemplo, o bloqueio de recurso é um recurso de extensão. Você aplica um bloqueio de recurso a outro recurso para impedir que ele seja excluído ou modificado. Não faz sentido criar um bloqueio de recurso por si só. Um recurso de extensão sempre é aplicado a outro recurso.

## <a name="extension-resource-types"></a>Tipos de recursos extensão

- Microsoft. Advisor/Configurations
- Microsoft. Advisor/Recommendations
- Microsoft. Advisor/supressões
- Microsoft. AlertsManagement/alertas
- Microsoft. AlertsManagement/alertsSummary
- Microsoft. Authorization/checkAccess
- Microsoft. Authorization/denyAssignments
- Microsoft. Authorization/Locks
- Microsoft. Authorization/Permissions
- Microsoft. Authorization/policyAssignments
- Microsoft. Authorization/policyDefinitions
- Microsoft. Authorization/policySetDefinitions
- Microsoft. Authorization/roleAssignments
- Microsoft. Authorization/roleDefinitions
- Microsoft. billing/billingPeriods
- Microsoft. billing/billingPermissions
- Microsoft. billing/billingRoleAssignments
- Microsoft. billing/billingRoleDefinitions
- Microsoft. billing/createBillingRoleAssignment
- Microsoft. Blueprint/blueprintAssignments
- Microsoft. Blueprint/plantas
- Microsoft. consumo/AggregatedCost
- Microsoft. consumo/saldos
- Microsoft. consumo/orçamentos
- Microsoft. consumo/encargos
- Microsoft. consumo/CostTags
- Microsoft. consumo/previsões
- Microsoft. consumo/Marketplaces
- Microsoft. consumo/OperationResults
- Microsoft. consumo/OperationStatus
- Microsoft. consumo/Pricesheets
- Microsoft. consumo/ReservationDetails
- Microsoft. consumo/ReservationRecommendations
- Microsoft. consumo/ReservationSummaries
- Microsoft. consumo/ReservationTransactions
- Microsoft. consumo/marcas
- Microsoft. consumo/termos
- Microsoft. consumo/UsageDetails
- Microsoft. consumo/créditos
- Microsoft. consumo/eventos
- Microsoft. consumo/muitos
- Microsoft. consumo/produtos
- Microsoft. consumo/locatários
- Microsoft. ContainerInstance/serviceAssociationLinks
- Microsoft. CostManagement/alertas
- Microsoft. CostManagement/orçamentos
- Microsoft. CostManagement/Dimensions
- Microsoft. CostManagement/Exports
- Microsoft. CostManagement/ExternalSubscriptions
- Microsoft. CostManagement/Forecast
- Microsoft. CostManagement/Query
- Microsoft. CostManagement/Reportconfigs
- Microsoft. CostManagement/Reports
- Microsoft. CostManagement/views
- Microsoft. CostManagement/showbackRules
- Microsoft. CustomProviders/Associations
- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTopics
- Microsoft. GuestConfiguration/configurationProfileAssignments
- Microsoft. GuestConfiguration/guestConfigurationAssignments
- Microsoft. GuestConfiguration/software
- Microsoft. GuestConfiguration/softwareUpdateProfile
- Microsoft. GuestConfiguration/softwareUpdates
- Microsoft. insights/automatedExportSettings
- Microsoft. insights/linha de base
- Microsoft. insights/calculatebaseline
- Microsoft. insights/diagnosticSettings
- Microsoft. insights/diagnosticSettingsCategories
- Microsoft. insights/EventTypes
- Microsoft. insights/extendedDiagnosticSettings
- Microsoft. insights/guestDiagnosticSettingsAssociation
- Microsoft. insights/logDefinitions
- Microsoft. insights/logs
- Microsoft. insights/metricDefinitions
- Microsoft. insights/metricNamespaces
- Microsoft. insights/metricbaselines
- Microsoft. insights/métricas
- Microsoft. insights/minhas pastas de trabalho
- Microsoft. insights/vmInsightsOnboardingStatuses
- Microsoft. KubernetesConfiguration/sourceControlConfigurations
- Microsoft. Maintenance/applyUpdates
- Microsoft. Maintenance/configurationAssignments
- Microsoft. Maintenance/updates
- Microsoft. ManagedIdentity/Identities
- Microsoft. Managedservices/registrationAssignments
- Microsoft. Managedservices/registrationDefinitions
- Microsoft. OperationalInsights/storageInsightConfigs
- Microsoft. OperationsManagement/managementassociations
- Microsoft. PolicyInsights/policyEvents
- Microsoft. PolicyInsights/policyStates
- Microsoft. PolicyInsights/policyTrackedResources
- Microsoft.PolicyInsights/remediations
- Microsoft. Recoveryservices/backupProtectedItems
- Microsoft. ResourceHealth/availabilityStatuses
- Microsoft. ResourceHealth/childAvailabilityStatuses
- Microsoft. ResourceHealth/childResources
- Microsoft. ResourceHealth/eventos
- Microsoft. ResourceHealth/impactedResources
- Microsoft. ResourceHealth/Notifications
- Microsoft. Resources/links
- Microsoft. Resources/Tags
- Segurança/conformidade da Microsoft
- Microsoft. Security/InformationProtectionPolicies
- Microsoft. Security/adaptiveNetworkHardenings
- Microsoft. Security/advancedThreatProtectionSettings
- Microsoft. Security/assessmentMetadata
- Microsoft. Security/avaliações
- Microsoft. Security/complianceResults
- Microsoft. Security/dataCollectionAgents
- Microsoft. Security/dataCollectionResults
- Microsoft. Security/deviceSecurityGroups
- Microsoft. Security/networkData
- Microsoft. Security/serverVulnerabilityAssessments
- Microsoft. SecurityInsights/agregações
- Microsoft. SecurityInsights/alertRuleTemplates
- Microsoft. SecurityInsights/alertRules
- Microsoft. SecurityInsights/bookmarks
- Microsoft. SecurityInsights/casos
- Microsoft. SecurityInsights/dataconnecters
- Microsoft. SecurityInsights/Entities
- Microsoft. SecurityInsights/entityQueries
- Microsoft. SecurityInsights/officeConsents
- Microsoft. SecurityInsights/Settings
- Microsoft. SoftwarePlan/hybridUseBenefits
- Microsoft. Subscription/CreateSubscription
- Microsoft. support/createsupportticket
- Microsoft. support/supporttickets
- Microsoft. WorkloadMonitor/Components
- Microsoft. WorkloadMonitor/monitorInstances
- Microsoft. WorkloadMonitor/monitores
- Microsoft. WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>Passos seguintes

- Para obter a ID de recurso para um recurso de extensão em um modelo de Azure Resource Manager, use o [extensionResourceId](template-functions-resource.md#extensionresourceid).
- Para obter um exemplo de como criar um recurso de extensão em um modelo, consulte [assinaturas de evento de grade de eventos](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions).
