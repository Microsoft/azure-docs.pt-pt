---
title: Receba alertas de registo de atividade nas notificações do serviço Azure usando o modelo do Gestor de Recursos
description: Ser notificado via SMS, e-mail ou webhook quando o serviço Azure ocorrer.
ms.date: 06/29/2020
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 730c023de61275d95fe594642149770af42b34b9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535755"
---
# <a name="quickstart-create-activity-log-alerts-on-service-notifications-using-an-arm-template"></a>Quickstart: Criar alertas de registo de atividade nas notificações de serviço usando um modelo ARM

Este artigo mostra-lhe como configurar alertas de registo de atividade para notificações de saúde de serviço usando um modelo de Gestor de Recursos Azure (modelo ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

As notificações de saúde do serviço são armazenadas no registo de [atividades do Azure](../azure-monitor/essentials/platform-logs-overview.md). Dado o eventual grande volume de informação armazenada no registo de atividades, existe uma interface de utilizador separada para facilitar a visualização e a configuração de alertas nas notificações de saúde do serviço.

Pode receber um alerta quando o Azure enviar notificações de saúde de serviço para a sua assinatura Azure. Pode configurar o alerta com base em:

- A classe de notificação de saúde de serviço (problemas de serviço, manutenção planeada, avisos de saúde).
- A assinatura foi afetada.
- O(s) serviço(s) afetado.
- A(s) região(s) afetada.

> [!NOTE]
> As notificações de saúde do serviço não enviam um alerta sobre eventos de saúde de recursos.

Também pode configurar a quem o alerta deve ser enviado:

- Selecione um grupo de ação existente.
- Crie um novo grupo de ação (que pode ser usado para futuros alertas).

Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ações](../azure-monitor/alerts/action-groups.md).

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Para executar os comandos a partir do seu computador local, instale o Azure CLI ou os módulos Azure PowerShell. Para obter mais informações, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli) e [instalar a Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>Rever o modelo

O modelo a seguir cria um grupo de ação com um alvo de e-mail e permite todas as notificações de saúde do serviço para a subscrição do alvo. Guarde este modelo como *CreateServiceHealthAlert.js.*

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "type": "String",
      "defaultValue": "SubHealth"
    },
    "activityLogAlerts_name": {
      "type": "String",
      "defaultValue": "ServiceHealthActivityLogAlert"
    },
    "emailAddress": {
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
      "comments": "Service Health Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ],
      "tags": {},
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ServiceHealth"
            },
            {
              "field": "properties.incidentType",
              "equals": "Incident"
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
      }
    }
  ]
}
```

O modelo define dois recursos:

- [Microsoft.Insights/ActionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft.Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>Implementar o modelo

Implemente o modelo utilizando qualquer método padrão para [a implementação de um modelo ARM,](../azure-resource-manager/templates/deploy-portal.md) como os seguintes exemplos utilizando CLI e PowerShell. Substitua os valores da amostra **do Grupo de Recursos** e envie um email **Para** o seu ambiente.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateServiceHealthAlert --resource-group my-resource-group --template-file CreateServiceHealthAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateServiceHealthAlert -ResourceGroupName my-resource-group -TemplateFile CreateServiceHealthAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>Validar a implementação

Verifique se o espaço de trabalho foi criado utilizando um dos seguintes comandos. Substitua os valores de amostra **do Grupo de Recursos** pelo valor acima utilizado.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name ServiceHealthActivityLogAlert
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name ServiceHealthActivityLogAlert
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

- Conheça as [melhores práticas para a criação de alertas de Saúde do Serviço Azure.](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa)
- Saiba como [configurar notificações de push móvel para a Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Saiba como [configurar notificações webhook para os sistemas de gestão de problemas existentes](service-health-alert-webhook-guide.md).
- Saiba mais sobre [as notificações de saúde do serviço.](service-notifications.md)
- Saiba mais sobre [a limitação da taxa de notificação.](../azure-monitor/alerts/alerts-rate-limiting.md)
- Reveja o [esquema de alerta de registo de atividade webhook](../azure-monitor/alerts/activity-log-alerts-webhook.md).
- Obtenha uma [visão geral dos alertas de registo de atividades](../azure-monitor/alerts/alerts-overview.md)e aprenda a receber alertas.
- Saiba mais sobre [grupos de ação.](../azure-monitor/alerts/action-groups.md)
