---
title: Tipos de recursos extensão
description: Listas os tipos de recursos Azure são usados para alargar as capacidades de outros tipos de recursos.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3a3fbc531750bec4b16e38f1fe79f613c1b94f5e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80754864"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Tipos de recursos que alargam capacidades de outros recursos

Um recurso de extensão é um recurso que adiciona às capacidades de outro recurso. Por exemplo, o bloqueio de recursos é um recurso de extensão. Aplica um bloqueio de recursos a outro recurso para evitar que seja eliminado ou modificado. Não faz sentido criar um bloqueio de recursos por si só. Um recurso de extensão é sempre aplicado a outro recurso.

## <a name="extension-resource-types"></a>Tipos de recursos extensão

- Microsoft.Advisor/configurações
- Microsoft.Advisor/recomendações
- Microsoft.Advisor/supressões
- Microsoft.AlertesGe/alertas
- Microsoft.AlertsGe/alertasSSummary
- Microsoft.Authorization/checkAccess
- Microsoft.Autorizações/negações
- Microsoft.Authorization/findOrphanRoleAssignments
- Microsoft.Autorização/fechaduras
- Microsoft.Autorização/permissões
- Microsoft.Autorizações/políticas
- Microsoft.Autorizações/políticasDefinições
- Microsoft.Authorization/policySetDefinis
- Microsoft.Autorizações/tarefas
- Microsoft.Autorização/roleAssignmentsUsageMetrics
- Microsoft.Autorizações/definições de funções
- Microsoft.Billing/BillingPeriods
- Microsoft.Billing/billingPermissions
- Microsoft.Billing/billingRoleAssignments
- Microsoft.Billing/billingRoleDefinitions
- Microsoft.Billing/createBillingRoleAssignment
- Microsoft.Blueprint/blueprintAssignments
- Microsoft.Blueprint/blueprints
- Microsoft.Consumo/Custo agregado
- Microsoft.Consumo/Saldos
- Microsoft.Consumo/Orçamentos
- Microsoft.Consumo/Encargos
- Microsoft.Consumo/CostTags
- Microsoft.Consumo/Previsões
- Microsoft.Consumo/Marketplaces
- Microsoft.Consumo/FuncionamentoResultados
- Microsoft.Consumo/Estado de Funcionamento
- Microsoft.Consumo/Folhas de Preços
- Microsoft.Consumo/Reservas
- Microsoft.Recomendações de consumo/reservas
- Microsoft.Consumo/ReservasResumos
- Microsoft.Consumo/ReservasTransacções
- Microsoft.Consumo/Etiquetas
- Microsoft.Consumo/Termos
- Microsoft.Consumo/utilização
- Microsoft.Consumo/créditos
- Microsoft.Consumo/eventos
- Microsoft.Consumo/lotes
- Microsoft.Consumo/produtos
- Microsoft.Consumo/inquilinos
- Microsoft.ContainerInstance/serviceAssociationLinks
- Microsoft.CostManagement/Alerts
- Microsoft.CostManagement/Budgets
- Microsoft.CostManagement/Dimensions
- Microsoft.CostManagement/Exportações
- Microsoft.CostManagement/ExternalSubscriptions
- Microsoft.CostManagement/Forecast
- Microsoft.CostManagement/Consulta
- Microsoft.CostManagement/Reportconfigs
- Microsoft.CostManagement/Reports
- Microsoft.CostManagement/Views
- Microsoft.CostManagement/showbackRules
- Microsoft.CustomProviders/associações
- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTopics
- Microsoft.GuestConfigura/configuraçãoProfileAssignments
- Microsoft.GuestConfiguration/guestConfigurationAssignments
- Microsoft.GuestConfiguration/software
- Microsoft.GuestConfiguration/softwareUpdateProfile
- Microsoft.GuestConfiguration/softwareUpdates
- microsoft.insights/baseline
- microsoft.insights/calcular base
- microsoft.insights/dataCollectionRuleAssociations
- microsoft.insights/diagnósticoDefinições
- microsoft.insights/diagnósticoSettingsCategories
- microsoft.insights/eventtypes
- microsoft.insights/extendedDiagnosticSettings
- microsoft.insights/guestDiagnosticSettingsAssociation
- microsoft.insights/logDefinis
- microsoft.insights/logs
- microsoft.insights/metricDefinitions
- microsoft.insights/metricNamespaces
- microsoft.insights/metricbaselines
- microsoft.insights/métricas
- microsoft.insights/myWorkbooks
- microsoft.insights/topologia
- microsoft.insights/transações
- microsoft.insights/vmInsightsOnboardingStatuses
- Microsoft.KubernetesConfigurações/sourceControlConfiguras
- Microsoft.Manutenção/aplicaçãoUpdates
- Microsoft.Maintenance/configuraçãoAtribuis
- Microsoft.Manutenção/atualizações
- Microsoft.ManagedIdentity/Identities
- Microsoft.ManagedServices/registrationAssignments
- Microsoft.ManagedServices/registrationDefinições
- Microsoft.OperationalInsights/storageInsightsConfigs
- Microsoft.OperationsManagement/managementassociations
- Microsoft.PolicyInsights/policyEvents
- Microsoft.PolicyInsights/policyStates
- Microsoft.PolicyInsights/policyTrackedResources
- Microsoft.PolicyInsights/remediações
- Microsoft.RecoveryServices/backupProtectedItems
- Microsoft.RecoveryServices/replicationEligibilityResults    
- Microsoft.ResourceHealth/availabilityStatuses
- Microsoft.ResourceHealth/childAvailabilityStatuses
- Microsoft.RecursosHealth/childResources
- Microsoft.ResourceHealth/events
- Microsoft.RecursosHealth/Impactrecursos
- Microsoft.ResourceHealth/notificações
- Microsoft.Recursos/links
- Microsoft.Recursos/tags
- Microsoft.Security/Compliances
- Microsoft.Security/InformationProtectionPolicies
- Microsoft.Security/adaptiveNetworkHardenings
- Microsoft.Security/advancedThreatProtectionSettings
- Microsoft.Segurança/avaliaçãoMetas
- Microsoft.Segurança/avaliações
- Microsoft.Segurança/conformidadeResultados
- Microsoft.Security/dataCollectionAgents
- Microsoft.Security/deviceSecurityGroups
- Microsoft.Security/networkData
- Microsoft.Security/serverVulnerabilityAssessments
- Microsoft.SecurityInsights/agregações
- Microsoft.SecurityInsights/alertRuleTemplates
- Microsoft.SecurityInsights/alertRules
- Microsoft.SecurityInsights/bookmarks
- Microsoft.SecurityInsights/cases
- Microsoft.SecurityInsights/dataConnectors
- Microsoft.SecurityInsights/dataConnectorsCheckRequirements
- Microsoft.SecurityInsights/entidades
- Microsoft.SecurityInsights/entidadeQueries
- Microsoft.SecurityInsights/incidentes
- Microsoft.SecurityInsights/officeConsents
- Microsoft.SecurityInsights/definições
- Microsoft.SoftwarePlan/hybridUseBenefits
- Microsoft.Subscrição/CreateSubscription
- microsoft.support/supporttickets
- Microsoft.WorkloadMonitor/componentes
- Microsoft.WorkloadMonitor/monitorInstances
- Microsoft.WorkloadMonitor/monitores
- Microsoft.WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>Passos seguintes

- Para obter o ID de recurso para um recurso de extensão num modelo de Gestor de Recursos Azure, utilize a [extensãoResourceId](../templates/template-functions-resource.md#extensionresourceid).
- Para um exemplo de criação de um recurso de extensão num modelo, consulte [as assinaturas](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions)do Evento Grid Event .
