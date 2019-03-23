---
title: Ativar automaticamente as definições de diagnóstico com um modelo do Resource Manager
description: Saiba como utilizar um modelo do Resource Manager para criar definições de diagnóstico que irão permitir-lhe transmitir os registos de diagnóstico para os Hubs de eventos ou armazená-los numa conta de armazenamento.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 3/26/2018
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: 7edce5175a1dda66abf3316cb8f0eb33e9f64ef7
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371474"
---
# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Ativar automaticamente as definições de diagnóstico durante a criação de recursos através de um modelo do Resource Manager
Neste artigo vamos mostrar como pode usar uma [modelo Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) para configurar as definições de diagnóstico num recurso quando é criado. Isto permite-lhe iniciar automaticamente a transmissão em fluxo a registos de diagnóstico e métricas para os Hubs de eventos, arquivá-los numa conta de armazenamento ou enviá-los para uma área de trabalho do Log Analytics, quando um recurso é criado.

> [!WARNING]
> O formato dos dados de registo na conta de armazenamento vai ser alterado para Linhas de JSON a 1 de novembro de 2018. [Leia este artigo para obter uma descrição do impacto e saber como atualizar a sua ferramenta para trabalhar com o novo formato.](./../../azure-monitor/platform/diagnostic-logs-append-blobs.md) 
>
> 

O método para ativar os registos de diagnóstico com um modelo do Resource Manager depende do tipo de recurso.

* **Não-Compute** recursos (por exemplo, grupos de segurança de rede, Logic Apps, automatização) utilizam [definições de diagnóstico descrito neste artigo](../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings).
* **Computação** recursos (WAD/LAD baseado) utilizar o [ficheiro de configuração de WAD/LAD descrito neste artigo](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

Neste artigo, descrevemos como configurar o diagnóstico utilizando um dos métodos.

Os passos básicos são os seguintes:

1. Crie um modelo como um ficheiro JSON que descreve como criar o recurso e ativar o diagnóstico.
2. [Implementar o modelo com qualquer método de implementação](../../azure-resource-manager/resource-group-template-deploy.md).

Veja a seguir, podemos dar um exemplo do ficheiro JSON do modelo que tem de gerar para não-Compute e de recursos de computação.

## <a name="non-compute-resource-template"></a>Modelo de recurso de computação não
Não-recursos de computação, terá de fazer duas coisas:

1. Adicione parâmetros para o blob de parâmetros para o nome da conta de armazenamento, ID de regra de autorização de hub de eventos e/ou ID de área de trabalho do Log Analytics (ativar o arquivamento dos registos de diagnóstico numa conta de armazenamento, transmissão em fluxo de registos aos Hubs de eventos, e/ou envio de registos para o Azure Monitor).
   
    ```json
    "settingName": {
      "type": "string",
      "metadata": {
        "description": "Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
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
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Azure Resource ID of the Log Analytics workspace for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. A matriz de recursos do recurso para o qual pretende ativar registos de diagnóstico, adicionar um recurso do tipo `[resource namespace]/providers/diagnosticSettings`.
   
    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
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
              "category": "/* log category name */",
              "enabled": true,
              "retentionPolicy": {
                "days": 0,
                "enabled": false
              }
            }
          ],
          "metrics": [
            {
              "category": "AllMetrics",
              "enabled": true,
              "retentionPolicy": {
                "enabled": false,
                "days": 0
              }
            }
          ]
        }
      }
    ]
    ```

Segue-se o blob de propriedades para a definição de diagnóstico [o formato descrito neste artigo](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate). Adicionar a `metrics` propriedade permitirá que também enviar métricas de recurso para essas mesmas saídas, fornecidas que [o recursos suporta métricas do Azure Monitor](../../azure-monitor/platform/metrics-supported.md).

Eis um exemplo completo que cria uma aplicação lógica e ativa a transmissão em fluxo para os Hubs de eventos e de armazenamento numa conta de armazenamento.

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "logicAppName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Logic App that will be created."
      }
    },
    "testUri": {
      "type": "string",
      "defaultValue": "https://azure.microsoft.com/status/feed/"
    },
    "settingName": {
      "type": "string",
      "metadata": {
        "description": "Name of the setting. Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
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
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Logic/workflows",
      "name": "[parameters('logicAppName')]",
      "apiVersion": "2016-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "definition": {
          "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
          "dependsOn": [
            "[resourceId('Microsoft.Logic/workflows', parameters('logicAppName'))]"
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
                "category": "WorkflowRuntime",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ],
            "metrics": [
              {
                "timeGrain": "PT1M",
                "enabled": true,
                "retentionPolicy": {
                  "enabled": false,
                  "days": 0
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```

## <a name="compute-resource-template"></a>Modelo de recursos de computação
Para ativar os diagnósticos num recurso de computação, por exemplo um cluster de Máquina Virtual ou do Service Fabric, tem de:

1. Adicione a extensão de diagnóstico do Azure para a definição do recurso VM.
2. Especifica um hub de conta e/ou eventos de armazenamento como um parâmetro.
3. Adicione o conteúdo do ficheiro XML de WADCfg na propriedade XMLCfg, carateres de escape todos os caracteres XML corretamente.

> [!WARNING]
> Neste último passo pode ser difícil de solucionar. [Veja este artigo](../../virtual-machines/extensions/diagnostics-template.md#diagnostics-configuration-variables) para obter um exemplo que divide o esquema de configuração de diagnósticos em variáveis que são escritas e formatadas corretamente.
> 
> 

O processo inteiro, incluindo exemplos, é descrito [neste documento](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Passos Seguintes
* [Leia mais sobre os Registos de Diagnóstico do Azure](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Stream registos de diagnóstico do Azure para os Hubs de eventos](../../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md)


