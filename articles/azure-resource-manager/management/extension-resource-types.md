---
title: Tipos de recursos extensão
description: Listas os tipos de recursos Azure são usados para aumentar as capacidades de outros tipos de recursos.
ms.topic: conceptual
ms.date: 11/14/2020
ms.openlocfilehash: 5561c480dd5a2849588ed2288eb5bcc35fc1446c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94658456"
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
- Microsoft.Autorização/funAssignments
- Microsoft.Autorização/funDefinitions

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
- Microsoft.CostManagement/Dimensions
- Microsoft.CostManagement/Exports
- Microsoft.CostManagement/ExternalSubscriptions
- Microsoft.CostManagement/Forecast
- Microsoft.CostManagement/Insights
- Microsoft.CostManagement/Consulta
- Microsoft.CostManagement/Reportconfigs
- Microsoft.CostManagement/Reports
- Microsoft.CostManagement/Views

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

- Microsoft.CustomProviders/associações

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
- Microsoft.ResourceHealth/notificações

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
- Microsoft.Security/iotSensors
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
- Microsoft.SecurityInsights/entidades
- Microsoft.SecurityInsights/incidentes
- Microsoft.SecurityInsights/configurações
- Microsoft.SecurityInsights/threatIntelligence
- Microsoft.SecurityInsights/watchlists

## <a name="microsoftserialconsoleppe"></a>Microsoft.SerialConsole.PPE

- Microsoft.SerialConsole.PPE/serialPorts

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

- Microsoft.SoftwarePlan/hybridUseBenefits

## <a name="microsoftsupport"></a>microsoft.support

- microsoft.support/supporttickets

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

- Microsoft.WorkloadMonitor/componentes
- Microsoft.WorkloadMonitor/monitorInstances
- Microsoft.WorkloadMonitor/monitores
- Microsoft.WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>Passos seguintes

- Para obter o ID de recurso para um recurso de extensão num modelo de Gestor de Recursos Azure, utilize a [extensãoResourceId](../templates/template-functions-resource.md#extensionresourceid).
- Para um exemplo de criação de um recurso de extensão num modelo, consulte [subscrições de eventos de grelha de eventos](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions).
