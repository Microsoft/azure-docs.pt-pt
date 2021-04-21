---
title: Crie alertas do Azure Advisor para novas recomendações utilizando o modelo de Gestor de Recursos
description: Saiba como configurar um alerta para novas recomendações do Azure Advisor utilizando um modelo de Gestor de Recursos Azure (modelo ARM).
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.date: 06/29/2020
ms.openlocfilehash: 716ab104ce6517aeb554b42522e5906829877259
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765672"
---
# <a name="quickstart-create-azure-advisor-alerts-on-new-recommendations-using-an-arm-template"></a>Quickstart: Crie alertas do Azure Advisor sobre novas recomendações usando um modelo ARM

Este artigo mostra-lhe como configurar um alerta para novas recomendações do Azure Advisor usando um modelo de Gestor de Recursos Azure (modelo ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Sempre que o Azure Advisor deteta uma nova recomendação para um dos seus recursos, um evento é armazenado no [diário de atividades Azure](../azure-monitor/essentials/platform-logs-overview.md). Pode configurar alertas para estes eventos do Azure Advisor utilizando uma experiência de criação de alertas específicos de recomendação. Pode selecionar uma subscrição e opcionalmente um grupo de recursos para especificar os recursos em que deseja receber alertas.

Também pode determinar os tipos de recomendações utilizando estas propriedades:

- Categoria
- Nível de impacto
- Tipo de recomendação

Também pode configurar a ação que ocorrerá quando um alerta é desencadeado por:

- Selecionando um grupo de ação existente
- Criação de um novo grupo de ação

Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ações](../azure-monitor/alerts/action-groups.md).

> [!NOTE]
> Atualmente, os alertas de aconselhamento só estão disponíveis para recomendações de Alta Disponibilidade, Desempenho e Custo. Recomendações de segurança não são apoiadas.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Para executar os comandos a partir do seu computador local, instale o Azure CLI ou os módulos Azure PowerShell. Para obter mais informações, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli) e [instalar a Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>Rever o modelo

O modelo a seguir cria um grupo de ação com um alvo de e-mail e permite todas as notificações de saúde do serviço para a subscrição do alvo. Guarde este modelo como *CreateAdvisorAlert.js.*

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "apiVersion": "2019-06-01",
      "name": "[parameters('actionGroups_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [],
      "tags": {},
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      }
    },
    {
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "scale": null,
      "tags": {},
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
```

O modelo define dois recursos:

- [Microsoft.Insights/ActionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft.Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>Implementar o modelo

Implemente o modelo utilizando qualquer método padrão para [a implementação de um modelo ARM,](../azure-resource-manager/templates/deploy-portal.md) como os seguintes exemplos utilizando CLI e PowerShell. Substitua os valores da amostra **do Grupo de Recursos** e envie um **e-mail Para o** seu ambiente. O nome do espaço de trabalho deve ser único entre todas as subscrições do Azure.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateAdvisorAlert --resource-group my-resource-group --template-file CreateAdvisorAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateAdvisorAlert -ResourceGroupName my-resource-group -TemplateFile CreateAdvisorAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>Validar a implementação

Verifique se o espaço de trabalho foi criado utilizando um dos seguintes comandos. Substitua os valores de amostra **do Grupo de Recursos** pelo valor acima utilizado.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name AdvisorAlertsTest
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name AdvisorAlertsTest
```

---

## <a name="clean-up-resources"></a>Limpar recursos

Se planeia continuar a trabalhar com os rápidos e tutoriais subsequentes, talvez queira deixar estes recursos no lugar. Quando já não for necessário, elimine o grupo de recursos, que elimina a regra de alerta e os recursos conexos. Para eliminar o grupo de recursos utilizando a Azure CLI ou a Azure PowerShell

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Remove-AzResourceGroup -Name my-resource-group
```

---

## <a name="next-steps"></a>Passos seguintes

- Obtenha uma [visão geral dos alertas de registo de atividades](../azure-monitor/alerts/alerts-overview.md)e aprenda a receber alertas.
- Saiba mais sobre [grupos de ação.](../azure-monitor/alerts/action-groups.md)
