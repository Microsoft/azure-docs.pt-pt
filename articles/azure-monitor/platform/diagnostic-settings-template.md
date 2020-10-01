---
title: Crie uma definição de diagnóstico em Azure utilizando o modelo do Gestor de Recursos
description: Crie definições de diagnóstico utilizando um modelo de Gestor de Recursos para encaminhar os registos da plataforma Azure para registos do Monitor Azure, armazenamento de Azure ou Hubs de Eventos Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 73f2b3ea90cc94fa3411552c7b812fe53eb4dbbb
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91607416"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>Crie uma definição de diagnóstico em Azure usando um modelo de Gestor de Recursos
[As definições de diagnóstico](diagnostic-settings.md) no Azure Monitor especificam para onde enviar [registos de plataforma](platform-logs-overview.md) que são recolhidos pelos recursos Azure e pela plataforma Azure de que dependem. Este artigo fornece detalhes e exemplos para usar um [modelo de Gestor de Recursos Azure](../../azure-resource-manager/templates/template-syntax.md) para criar e configurar definições de diagnóstico para recolher registos de plataformas para diferentes destinos.

> [!NOTE]
> Uma vez que não é possível [criar uma definição de diagnóstico](diagnostic-settings.md) para o registo de atividade azure utilizando definições de diagnóstico powerShell ou CLI como configurações de diagnóstico para outros recursos Azure, crie um modelo de Gestor de Recursos para o registo de atividade usando as informações neste artigo e implemente o modelo utilizando PowerShell ou CLI.

## <a name="deployment-methods"></a>Métodos de implantação
Pode implementar modelos de Gestor de Recursos utilizando qualquer método válido, incluindo PowerShell e CLI. As definições de diagnóstico do registo de atividade devem ser implantada numa subscrição utilizando `az deployment create` o CLI ou `New-AzDeployment` para o PowerShell. As definições de diagnóstico dos registos de recursos devem ser implantada num grupo de recursos que utilize `az group deployment create` o CLI ou `New-AzResourceGroupDeployment` para o PowerShell.

Consulte [recursos de implementação com modelos de Gestor de Recursos e Recursos Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) e [Implemente recursos com modelos de Gestor de Recursos e CLI Azure](../../azure-resource-manager/templates/deploy-cli.md) para mais detalhes. 





## <a name="resource-logs"></a>Registos do recurso
Para registos de recursos, adicione um recurso de tipo `<resource namespace>/providers/diagnosticSettings` ao modelo. A secção de propriedades segue o formato descrito em [Definições de Diagnóstico - Criar ou Atualizar](/rest/api/monitor/diagnosticsettings/createorupdate). Forneça uma `category` na secção para cada uma das `logs` categorias válidas para o recurso que pretende recolher. Adicione a `metrics` propriedade para recolher métricas de recursos para os mesmos destinos se o [recurso suportar métricas.](metrics-supported.md)

Segue-se um modelo que recolhe uma categoria de registo de recursos para um determinado recurso para um espaço de trabalho log analytics, conta de armazenamento e centro de eventos.

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
Segue-se um exemplo que cria uma definição de diagnóstico para uma definição de escala automática que permite o streaming de registos de recursos para um centro de eventos, uma conta de armazenamento e um espaço de trabalho Log Analytics.

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
Para o registo de atividades Azure, adicione um recurso do tipo `Microsoft.Insights/diagnosticSettings` . As categorias disponíveis estão listadas em [Categorias no Registo de Atividades.](./activity-log.md#view-the-activity-log) Segue-se um modelo que recolhe todas as categorias de registo de atividade para um espaço de trabalho log analytics, conta de armazenamento e centro de eventos.


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
* Leia mais sobre [os registos da plataforma em Azure](platform-logs-overview.md).
* Saiba mais [sobre as definições de diagnóstico](diagnostic-settings.md).
