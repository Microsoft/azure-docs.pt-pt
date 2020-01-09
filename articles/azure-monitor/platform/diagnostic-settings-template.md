---
title: Criar configuração de diagnóstico no Azure usando o modelo do Resource Manager
description: Crie configurações de diagnóstico usando um modelo do Resource Manager para encaminhar logs da plataforma Azure para Azure Monitor logs, armazenamento do Azure ou hubs de eventos do Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: b549cc0e890a122a04984baa2348831fc51abe08
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531008"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>Criar configuração de diagnóstico no Azure usando um modelo do Resource Manager
[As configurações de diagnóstico](diagnostic-settings.md) no Azure monitor especificam onde enviar [os logs de plataforma](platform-logs-overview.md) que são coletados pelos recursos do Azure e a plataforma do Azure da qual dependem. Este artigo fornece detalhes e exemplos para usar um [modelo de Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) para criar e definir configurações de diagnóstico para coletar logs de plataforma para destinos diferentes. 

> [!NOTE]
> Como você não pode [criar uma configuração de diagnóstico](diagnostic-settings.md) para o log de atividades do Azure usando o PowerShell ou a CLI, como configurações de diagnóstico para outros recursos do Azure, crie um modelo do Resource Manager para o log de atividades usando as informações neste artigo e implante o modelo usando o PowerShell ou a CLI.

## <a name="deployment-methods"></a>Métodos de implantação
Você pode implantar modelos do Resource Manager usando qualquer método válido, incluindo PowerShell e CLI. As configurações de diagnóstico para o log de atividades devem ser implantadas em uma assinatura usando `az deployment create` para CLI ou `New-AzDeployment` para o PowerShell. As configurações de diagnóstico para logs de recursos devem ser implantadas em um grupo de recursos usando `az group deployment create` para CLI ou `New-AzResourceGroupDeployment` para o PowerShell. 

Consulte [implantar recursos com modelos do Resource Manager e Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md) e [implantar recursos com modelos do resource Manager e CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md) para obter detalhes. 





## <a name="resource-logs"></a>Registos do recurso
Para logs de recursos, adicione um recurso do tipo `<resource namespace>/providers/diagnosticSettings` ao modelo. A seção Propriedades segue o formato descrito em [configurações de diagnóstico – criar ou atualizar](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate). Forneça um `category` na seção `logs` para cada uma das categorias válidas para o recurso que você deseja coletar. Adicione a propriedade `metrics` para coletar métricas de recurso para os mesmos destinos se o [recurso der suporte a métricas](metrics-supported.md).

Veja a seguir um modelo que coleta uma categoria de log de recursos para um recurso específico para um Log Analytics espaço de trabalho, uma conta de armazenamento e um hub de eventos. 

```json
"resources": [
  {
    "type": "/<resource namespace>/providers/diagnosticSettings",
    "name": "[concat(parameters('resourceName'),'/microsoft.insights/', parameters('settingName'))]",
    "dependsOn": [
      "[<resource Id for which resource logs will be enabled>]"
    ],
    "apiVersion": "2017-05-01-preview",
    "properties": {
      "name": "[parameters('settingName')]",
      "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
      "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
      "eventHubName": "[parameters('eventHubName')]",
      "workspaceId": "[parameters('workspaceId')]",
      "logs": [ 
        {
          "category": "<category name>",
          "enabled": true
        }
      ],
      "metrics": [
        {
          "category": "AllMetrics",
          "enabled": true
        }
      ]
    }
  }
]
```



### <a name="example"></a>Exemplo
Veja a seguir um exemplo que cria uma configuração de diagnóstico para uma configuração de dimensionamento automático que habilita o streaming de logs de recursos para um hub de eventos, uma conta de armazenamento e um espaço de trabalho Log Analytics.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "autoscaleSettingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the autoscale setting"
            }
        },
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceIDl of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
    {
      "type": "microsoft.insights/autoscalesettings/providers/diagnosticSettings",
      "apiVersion": "2017-05-01-preview",
      "name": "[concat(parameters('autoscaleSettingName'),'/microsoft.insights/', parameters('settingName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/autoscalesettings', parameters('autoscaleSettingName'))]"
      ],
      "properties": {
        "workspaceId": "[parameters('workspaceId')]",
        "storageAccountId": "[parameters('storageAccountId')]",
        "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
        "eventHubName": "[parameters('eventHubName')]",
        "logs": [
          {
            "category": "AutoscaleScaleActions",
            "enabled": true
          },
          {
            "category": "AutoscaleEvaluations",
            "enabled": true
          }
        ]
      }
    }
  ]
}
```

## <a name="activity-log"></a>Registo de atividades
Para o log de atividades do Azure, adicione um recurso do tipo `Microsoft.Insights/diagnosticSettings`. As categorias disponíveis são listadas em [categorias no log de atividades](activity-log-view.md#categories-in-the-activity-log). Veja a seguir um modelo que coleta todas as categorias de log de atividades para um Log Analytics espaço de trabalho, uma conta de armazenamento e um hub de eventos.


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceID of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Insights/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[parameters('settingName')]",
            "location": "global",
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "logs": [
                    {
                        "category": "Administrative",
                        "enabled": true
                    },
                    {
                        "category": "Security",
                        "enabled": true
                    },
                    {
                        "category": "ServiceHealth",
                        "enabled": true
                    },
                    {
                        "category": "Alert",
                        "enabled": true
                    },
                    {
                        "category": "Recommendation",
                        "enabled": true
                    },
                    {
                        "category": "Policy",
                        "enabled": true
                    },
                    {
                        "category": "Autoscale",
                        "enabled": true
                    },
                    {
                        "category": "ResourceHealth",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```


## <a name="next-steps"></a>Passos seguintes
* Leia mais sobre [os logs de plataforma no Azure](platform-logs-overview.md).
* Saiba mais sobre [as configurações de diagnóstico](diagnostic-settings.md).
