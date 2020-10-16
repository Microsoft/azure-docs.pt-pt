---
title: Tipos de recursos extensão
description: Listas os tipos de recursos Azure são usados para aumentar as capacidades de outros tipos de recursos.
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 8b80c63d361f3ad8199fd669178f7bf88dabe02e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90969749"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Tipos de recursos que ampliam capacidades de outros recursos

Um recurso de extensão é um recurso que adiciona às capacidades de outro recurso. Por exemplo, o bloqueio de recursos é um recurso de extensão. Aplica um bloqueio de recursos a outro recurso para evitar que seja eliminado ou modificado. Não faz sentido criar uma fechadura de recursos por si só. Um recurso de extensão é sempre aplicado a outro recurso.

## <a name="extension-resource-types"></a>Tipos de recursos extensão

- Microsoft.Advisor/configurações
- Microsoft.Advisor/recomendações
- Microsoft.Advisor/supressões
- Microsoft.AlertsManagement/alertas
- Microsoft.AlertsManagement/alertsSummary
- Microsoft.Authorization/accessReviewScheduleDefinitions
- Microsoft.Authorization/accessReviewScheduleSettings
- Microsoft.Autorização/checkAccess
- Microsoft.Autorização/negam Assignments
- Microsoft.Authorization/findOrphanRoleAssignments
- Microsoft.Autorização/bloqueios
- Microsoft.Autorização/permissões
- Microsoft.Autorização/políticaAssins de assinaturas
- Microsoft.Autorização/políticaDefinitions
- Microsoft.Autorização/políticaExemptions
- Microsoft.Authorization/policySetDefinitions
- Microsoft.Authorization/privateLinkAssociations
- Microsoft.Autorização/funAssignments
- Microsoft.Autorização/funAssignmentsUsageMetrics
- Microsoft.Autorização/funDefinitions
- Microsoft.Automanage/configurationProfileAssignments
- Microsoft.Billing/billingPeriods
- Microsoft.Billing/billingPermissions
- Microsoft.Billing/billingRoleAssignments
- Microsoft.Billing/billingRoleDefinitions
- Microsoft.Billing/createBillingRoleAssignment
- Microsoft.Blueprint/blueprintAssignments
- Microsoft.Blueprint/blueprints
- Microsoft.ChangeAnalysis/recursosChanges
- Microsoft.Consumption/AggregatedCost
- Microsoft.Consumption/Saldos
- Microsoft.Consumo/Orçamentos
- Microsoft.Consumption/Charges
- Microsoft.Consumption/CostTags
- Microsoft.Consumo/créditos
- Microsoft.Consumo/eventos
- Microsoft.Consumption/Previsões
- Microsoft.Consumo/lotes
- Microsoft.Consumption/Marketplaces
- Microsoft.Consumo/OperaçãoResults
- Microsoft.Consumption/OperationStatus
- Microsoft.Consumption/Pricesheets
- Microsoft.Consumo/produtos
- Microsoft.Consumption/ReservationDetails
- Microsoft.Consumption/ReservationRecommendationDetails
- Microsoft.Consumption/ReservasRecommendations
- Microsoft.Consumption/ReservasSummaries
- Microsoft.Consumption/ReservasTransacções
- Microsoft.Consumption/Tags
- Microsoft.Consumo/inquilinos
- Microsoft.Consumption/Termos
- Microsoft.Consumption/UsageDetails
- Microsoft.ContainerInstance/serviceAssociationLinks
- Microsoft.CostManagement/Alerts
- Microsoft.CostManagement/Orçamentos
- Microsoft.CostManagement/costAllocationRules
- Microsoft.CostManagement/Dimensions
- Microsoft.CostManagement/Exports
- Microsoft.CostManagement/ExternalSubscriptions
- Microsoft.CostManagement/Forecast
- Microsoft.CostManagement/Insights
- Microsoft.CostManagement/Consulta
- Microsoft.CostManagement/Reportconfigs
- Microsoft.CostManagement/Reports
- Microsoft.CostManagement/showbackRules
- Microsoft.CostManagement/Views
- Microsoft.CustomProviders/associações
- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTopics
- Microsoft.GuestConfiguration/configurationProfileAsssignments
- Microsoft.GuestConfiguration/guestConfigurationAssignments
- Microsoft.GuestConfiguration/software
- Microsoft.GuestConfiguration/softwareUpdateProfile
- Microsoft.GuestConfiguration/softwareUpdates
- microsoft.insights/linha de base
- microsoft.insights/calcularbaseline
- microsoft.insights/dataCollectionRuleAssociations
- microsoft.insights/diagnosticSettings
- microsoft.insights/diagnosticSettingsCategories
- microsoft.insights/eventtypes
- microsoft.insights/ExtendedDiagnosticSettings
- microsoft.insights/generateLiveToken
- microsoft.insights/guestDiagnosticSettingsAssociation
- microsoft.insights/logDefinitions
- microsoft.insights/logs
- microsoft.insights/metricbaselines
- microsoft.insights/metricDefinitions
- microsoft.insights/metricNamespaces
- microsoft.insights/métricas
- microsoft.insights/myWorkbooks
- microsoft.insights/topologia
- microsoft.insights/transações
- microsoft.insights/vmInsightsOnboardingStatuses
- Microsoft.KubernetesConfiguration/extensions
- Microsoft.KubernetesConfiguration/sourceControlConfigurations
- Microsoft.Manutenção/aplicades
- Microsoft.Manutenção/configuraçãoAssignments
- Microsoft.Manutenção/atualizações
- Microsoft.ManagedIdentity/Identities
- Microsoft.ManagedServices/registrationAssignments
- Microsoft.ManagedServices/registrationDefinitions
- Microsoft.OperationalInsights/storageInsightConfigs
- Microsoft.OperationsManagement/managementassociations
- Microsoft.PolicyInsights/atestados
- Microsoft.PolicyInsights/policyEvents
- Microsoft.PolicyInsights/policyStates
- Microsoft.PolicyInsights/policyTrackedResources
- Microsoft.PolicyInsights/remediações
- Microsoft.RecoveryServices/backupProtectedItems
- Microsoft.RecoveryServices/replicationEtsesibilidade
- Microsoft.ResourceHealth/availabilityStatuses
- Microsoft.ResourceHealth/childSetilabilityStatuses
- Microsoft.ResourceHealth/childResources
- Microsoft.ResourceHealth/eventos
- Microsoft.ResourceHealth/impactedResources
- Microsoft.ResourceHealth/notificações
- Microsoft.Resources/links
- Microsoft.Resources/tags
- Microsoft.Security/adaptiveNetworkHardenings
- Microsoft.Security/advancedThreatProtectionSettings
- Microsoft.Security/assessmentMetadata
- Microsoft.Security/assessments
- Microsoft.Security/complianceResults
- Microsoft.Security/Compliances
- Microsoft.Security/dataCollectionAgents
- Microsoft.Security/deviceSecurityGroups
- Microsoft.Security/InformationProtectionPolicies
- Microsoft.Security/iotSensors
- Microsoft.Security/jitPolicies
- Microsoft.Security/serverVulnerabilityAssesments
- Microsoft.Security/sqlVulnerabilityAssessments
- Microsoft.SecurityInsights/agregações
- Microsoft.SecurityInsights/alertRules
- Microsoft.SecurityInsights/alertRuleTemplates
- Microsoft.SecurityInsights/automationRules
- Microsoft.SecurityInsights/bookmarks
- Microsoft.SecurityInsights/cases
- Microsoft.SecurityInsights/dataConnectors
- Microsoft.SecurityInsights/dataConnectorsCheckRequirements
- Microsoft.SecurityInsights/entidades
- Microsoft.SecurityInsights/entityQueries
- Microsoft.SecurityInsights/incidentes
- Microsoft.SecurityInsights/officeConsents
- Microsoft.SecurityInsights/configurações
- Microsoft.SecurityInsights/threatIntelligence
- Microsoft.SecurityInsights/watchlists
- Microsoft.SoftwarePlan/hybridUseBenefits
- Microsoft.Subscrição/Cria subscrição
- microsoft.support/supporttickets
- Microsoft.WorkloadMonitor/componentes
- Microsoft.WorkloadMonitor/monitorInstances
- Microsoft.WorkloadMonitor/monitores
- Microsoft.WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>Passos seguintes

- Para obter o ID de recurso para um recurso de extensão num modelo de Gestor de Recursos Azure, utilize a [extensãoResourceId](../templates/template-functions-resource.md#extensionresourceid).
- Para um exemplo de criação de um recurso de extensão num modelo, consulte [subscrições de eventos de grelha de eventos](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions).
