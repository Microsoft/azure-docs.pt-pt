---
title: Amostras de modelo do gestor de recursos para regras de recolha de dados
description: Experimente modelos de Gestor de Recursos Azure para criar associações entre regras de recolha de dados e máquinas virtuais no Azure Monitor.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: bff13c24e0b5818b352e76df1d8ecd8251a5f12c
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430230"
---
# <a name="resource-manager-template-samples-for-data-collection-rules-in-azure-monitor"></a>Amostras de modelo do gestor de recursos para regras de recolha de dados no Azure Monitor
Este artigo inclui [modelos de gestor de recursos Azure](../../azure-resource-manager/templates/template-syntax.md) para implantar e configurar o [agente Log Analytics](../platform/log-analytics-agent.md) e a [extensão de diagnóstico](../platform/diagnostics-extension-overview.md) para máquinas virtuais no Azure Monitor. Cada amostra inclui um ficheiro de modelo e um ficheiro de parâmetros com valores de amostra para fornecer ao modelo.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-association-with-azure-vm"></a>Criar associação com a Azure VM

A amostra a seguir cria uma associação entre uma máquina virtual Azure e uma regra de recolha de dados.

### <a name="template-file"></a>Arquivo de modelo

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('vmName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this virtual machine.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Arquivo de parâmetros

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```

## <a name="create-association-with-azure-arc"></a>Criar associação com a Azure Arc

A amostra que se segue instala o agente Azure Monitor numa máquina virtual Windows Azure. Uma associação é criada entre uma máquina de servidor ativada pelo Arco Azure e uma regra de recolha de dados.

### <a name="template-file"></a>Arquivo de modelo

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.HybridCompute/machines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('machineName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this Arc server.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Arquivo de parâmetros

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```


## <a name="next-steps"></a>Passos seguintes

* [Obtenha outros modelos de amostra para Azure Monitor](resource-manager-samples.md).
* [Saiba mais sobre o agente Log Analytics.](../platform/log-analytics-agent.md)
* [Saiba mais sobre a extensão de diagnóstico.](../platform/diagnostics-extension-overview.md)
