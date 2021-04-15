---
title: Tipos de recursos extensão
description: Listas os tipos de recursos Azure são usados para aumentar as capacidades de outros tipos de recursos.
ms.topic: conceptual
ms.date: 04/12/2021
ms.openlocfilehash: 7085c0894fbf3bd56b56e4187d56f9af54916962
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363993"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Tipos de recursos que ampliam capacidades de outros recursos

Um recurso de extensão é um recurso que adiciona às capacidades de outro recurso. Por exemplo, o bloqueio de recursos é um recurso de extensão. Aplica um bloqueio de recursos a outro recurso para evitar que seja eliminado ou modificado. Não faz sentido criar uma fechadura de recursos por si só. Um recurso de extensão é sempre aplicado a outro recurso.

## <a name="microsoftadvisor"></a>Microsoft.Advisor

- Microsoft.Advisor/configurações
- Microsoft.Advisor/recomendações
- Microsoft.Advisor/supressões

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

- Microsoft.AlertsManagement/alertas

## <a name="microsoftauthorization"></a>Microsoft.Authorization

- Microsoft.Autorização/negam Assignments
- Microsoft.Autorização/bloqueios
- Microsoft.Autorização/políticaAssins de assinaturas
- Microsoft.Autorização/políticaDefinitions
- Microsoft.Autorização/políticaExemptions
- Microsoft.Authorization/policySetDefinitions
- Microsoft.Authorization/privateLinkAssociations
- Microsoft.Authorization/roleAssignmentApprovals
- Microsoft.Autorização/funAssignments
- Microsoft.Autorização/funAssignmentScheduleInstances
- Microsoft.Autorização/funAssignmentScheduleRequests
- Microsoft.Autorização/funAssignmentS
- Microsoft.Autorização/funDefinitions
- Microsoft.Authorization/roleEligibilityScheduleInstances
- Microsoft.Authorization/roleEligibilityScheduleRequests
- Microsoft.Autorização/roleEligibilitySchedules
- Microsoft.Authorization/roleManagementPolicies
- Microsoft.Authorization/roleManagementPolicyAssignments

## <a name="microsoftautomanage"></a>Microsoft.Automanage

- Microsoft.Automanage/configurationProfileAssignments

## <a name="microsoftbilling"></a>Microsoft.Billing

- Microsoft.Billing/billingPeriods
- Microsoft.Billing/billingPermissions
- Microsoft.Billing/billingRoleAssignments
- Microsoft.Billing/billingRoleDefinitions
- Microsoft.Billing/createBillingRoleAssignment

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

- Microsoft.Blueprint/blueprintAssignments
- Microsoft.Blueprint/blueprints

## <a name="microsoftcapacity"></a>Microsoft.Capacity

- Microsoft.Capacity/listSkus

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

- Microsoft.ChangeAnalysis/alterações

## <a name="microsoftconsumption"></a>Microsoft.Consumption

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
- Microsoft.Consumption/Pricesheets
- Microsoft.Consumo/produtos
- Microsoft.Consumption/ReservationDetails
- Microsoft.Consumption/ReservationRecommendationDetails
- Microsoft.Consumption/ReservasRecommendations
- Microsoft.Consumption/ReservasSummaries
- Microsoft.Consumption/ReservasTransacções

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

- Microsoft.ContainerInstance/serviceAssociationLinks

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

- Microsoft.CostManagement/Alerts
- Microsoft.CostManagement/Orçamentos
- Microsoft.CostManagement/CheckNameAvailability
- Microsoft.CostManagement/Dimensions
- Microsoft.CostManagement/Exports
- Microsoft.CostManagement/ExternalSubscriptions
- Microsoft.CostManagement/Forecast
- Microsoft.CostManagement/GenerateDetailedCostReport
- Microsoft.CostManagement/Insights
- Microsoft.CostManagement/OperationResults
- Microsoft.CostManagement/OperationStatus
- Microsoft.CostManagement/Consulta
- Microsoft.CostManagement/Reportconfigs
- Microsoft.CostManagement/Reports
- Microsoft.CostManagement/AgendadosActions
- Microsoft.CostManagement/Views

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

- Microsoft.CustomProviders/associações

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

- Microsoft.DataMigration/DatabaseMigrations

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTopics

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

- Microsoft.GuestConfiguration/configurationProfileAsssignments
- Microsoft.GuestConfiguration/guestConfigurationAssignments
- Microsoft.GuestConfiguration/software

## <a name="microsoftinsights"></a>microsoft.insights

- microsoft.insights/linha de base
- microsoft.insights/dataCollectionRuleAssociations
- microsoft.insights/diagnosticSettings
- microsoft.insights/diagnosticSettingsCategories
- microsoft.insights/eventtypes
- microsoft.insights/ExtendedDiagnosticSettings
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

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

- Microsoft.KubernetesConfiguration/extensions
- Microsoft.KubernetesConfiguration/sourceControlConfigurations

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

- Microsoft.Manutenção/aplicades
- Microsoft.Manutenção/configuraçãoAssignments
- Microsoft.Manutenção/atualizações

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

- Microsoft.ManagedIdentity/Identities

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

- Microsoft.ManagedServices/registrationAssignments
- Microsoft.ManagedServices/registrationDefinitions

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

- Microsoft.OperationalInsights/storageInsightConfigs

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

- Microsoft.OperationsManagement/managementassociations

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

- Microsoft.PolicyInsights/atestados
- Microsoft.PolicyInsights/eventGridFilters
- Microsoft.PolicyInsights/policyEvents
- Microsoft.PolicyInsights/policyStates
- Microsoft.PolicyInsights/policyTrackedResources
- Microsoft.PolicyInsights/remediações

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

- Microsoft.RecoveryServices/backupProtectedItems
- Microsoft.RecoveryServices/replicationEtsesibilidade

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

- Microsoft.ResourceHealth/childResources
- Microsoft.ResourceHealth/eventos
- Microsoft.ResourceHealth/impactedResources

## <a name="microsoftresources"></a>Microsoft.Resources

- Microsoft.Resources/links
- Microsoft.Resources/tags

## <a name="microsoftsecurity"></a>Microsoft.Security

- Microsoft.Security/adaptiveNetworkHardenings
- Microsoft.Security/advancedThreatProtectionSettings
- Microsoft.Security/assessmentMetadata
- Microsoft.Security/assessments
- Microsoft.Security/Compliances
- Microsoft.Security/dataCollectionAgents
- Microsoft.Security/dispositivos
- Microsoft.Security/deviceSecurityGroups
- Microsoft.Security/InformationProtectionPolicies
- Microsoft.Security/insights
- Microsoft.Security/iotAlerts
- Microsoft.Security/iotRecommendations
- Microsoft.Security/iotSensors
- Microsoft.Security/iotSites
- Microsoft.Security/jitPolicies
- Microsoft.Security/serverVulnerabilityAssesments
- Microsoft.Security/sqlVulnerabilityAssessments

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

- Microsoft.SecurityInsights/agregações
- Microsoft.SecurityInsights/alertRules
- Microsoft.SecurityInsights/alertRuleTemplates
- Microsoft.SecurityInsights/automationRules
- Microsoft.SecurityInsights/bookmarks
- Microsoft.SecurityInsights/cases
- Microsoft.SecurityInsights/dataConnectors
- Microsoft.SecurityInsights/dataConnectorsCheckRequirements
- Microsoft.SecurityInsights/enriquecimento
- Microsoft.SecurityInsights/entidades
- Microsoft.SecurityInsights/entityQueryTemplates
- Microsoft.SecurityInsights/incidentes
- Microsoft.SecurityInsights/configurações
- Microsoft.SecurityInsights/threatIntelligence
- Microsoft.SecurityInsights/watchlists

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

- Microsoft.SerialConsole/serialPorts

## <a name="microsoftservicelinker"></a>Microsoft.ServiceLinker

- Microsoft.ServiceLinker/linkers

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

- Microsoft.SoftwarePlan/hybridUseBenefits

## <a name="microsoftsubscription"></a>Microsoft.Subscrição

- Microsoft.Subscrição/políticas

## <a name="microsoftsupport"></a>microsoft.support

- microsoft.support/supporttickets

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

- Microsoft.WorkloadMonitor/monitores

## <a name="next-steps"></a>Passos seguintes

- Para obter o ID de recurso para um recurso de extensão num modelo de Gestor de Recursos Azure, utilize a [extensãoResourceId](../templates/template-functions-resource.md#extensionresourceid).
- Para um exemplo de criação de um recurso de extensão num modelo, consulte [subscrições de eventos de grelha de eventos](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions).
