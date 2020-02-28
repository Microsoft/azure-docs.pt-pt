---
title: Criar definição de diagnóstico em Azure usando o modelo de Gestor de Recursos
description: Crie configurações de diagnóstico utilizando um modelo de Gestor de Recursos para reencaminhar os registos da plataforma Azure para registos de monitores Azure, armazenamento Azure ou Hubs de Eventos Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: a2569ca3f998030680bd7dbd872d71ccd372a25d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77672434"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>Criar definição de diagnóstico em Azure usando um modelo de Gestor de Recursos
[As definições](diagnostic-settings.md) de diagnóstico no Monitor Azure especificam onde enviar registos da Plataforma que são [recolhidos](platform-logs-overview.md) pelos recursos do Azure e da plataforma Azure de que dependem. Este artigo fornece detalhes e exemplos para usar um modelo de Gestor de [Recursos Azure](../../azure-resource-manager/templates/template-syntax.md) para criar e configurar configurações de diagnóstico para recolher registos de plataformas para diferentes destinos.

> [!NOTE]
> Uma vez que não pode [criar uma definição de diagnóstico](diagnostic-settings.md) para o registo da Atividade Azure utilizando o PowerShell ou o CLI como configurações de diagnóstico para outros recursos Do Azure, crie um modelo de Gestor de Recursos para o registo de Atividade utilizando a informação neste artigo e implemente o modelo utilizando powerShell ou CLI.

## <a name="deployment-methods"></a>Métodos de implantação
Pode implementar modelos de Gestor de Recursos utilizando qualquer método válido, incluindo PowerShell e CLI. As definições de diagnóstico do registo de atividade devem ser implementadas para uma subscrição utilizando `az deployment create` para CLI ou `New-AzDeployment` para powerShell. As definições de diagnóstico dos registos de recursos devem ser implantadas para um grupo de recursos que utilize `az group deployment create` para CLI ou `New-AzResourceGroupDeployment` para o PowerShell.

Consulte [os recursos de implantação com modelos de Gestor de Recursos e O PowerShell azure](../../azure-resource-manager/templates/deploy-powershell.md) e implemente recursos com modelos de Gestor de [Recursos e ClI Azure](../../azure-resource-manager/templates/deploy-cli.md) para mais detalhes. 





## <a name="resource-logs"></a>Registos do recurso
Para registos de recursos, adicione um recurso de tipo `<resource namespace>/providers/diagnosticSettings` ao modelo. A secção de propriedades segue o formato descrito nas [Definições](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate)de Diagnóstico - Criar ou Atualizar . Forneça um `category` na secção `logs` para cada uma das categorias válidas para o recurso que pretende recolher. Adicione a propriedade `metrics` para recolher métricas de recursos para os mesmos destinos se o [recurso suportar métricas](metrics-supported.md).

Segue-se um modelo que recolhe uma categoria de registo de recursos para um recurso específico para um espaço de trabalho, conta de armazenamento e centro de eventos de Log Analytics.

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
Segue-se um exemplo que cria uma definição de diagnóstico para uma definição de escala automática que permite o streaming de registos de recursos para um centro de eventos, uma conta de armazenamento e um espaço de trabalho log Analytics.

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
Para o registo da Atividade Azure, adicione um recurso de tipo `Microsoft.Insights/diagnosticSettings`. As categorias disponíveis estão listadas nas [categorias no Registo de Atividades.](activity-log-view.md#categories-in-the-activity-log) Segue-se um modelo que recolhe todas as categorias de registo de Atividade para um espaço de trabalho log Analytics, conta de armazenamento e centro de eventos.


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
* Leia mais sobre [os registos da plataforma no Azure.](platform-logs-overview.md)
* Saiba mais sobre [as definições de diagnóstico](diagnostic-settings.md).
