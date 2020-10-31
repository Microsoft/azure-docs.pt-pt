---
title: Criar grupos de ação com modelos de Gestor de Recursos
description: Aprenda a criar um grupo de ação utilizando um modelo de Gestor de Recursos Azure.
author: dkamstra
services: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 01fb93a4b74a35501d0684b822ea83fc7b20770a
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130583"
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>Crie um grupo de ação com um modelo de Gestor de Recursos
Este artigo mostra-lhe como usar um [modelo de Gestor de Recursos Azure](../../azure-resource-manager/templates/template-syntax.md) para configurar grupos de ação. Ao utilizar modelos, pode configurar automaticamente grupos de ação que podem ser reutilizados em certos tipos de alertas. Estes grupos de ação asseguram que todas as partes corretas sejam notificadas quando um alerta é desencadeado.

Os passos básicos são:

1. Crie um modelo como um ficheiro JSON que descreva como criar o grupo de ação.

2. Implemente o modelo utilizando [qualquer método de implantação](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="resource-manager-templates-for-an-action-group"></a>Modelos de gestor de recursos para um grupo de ação

Para criar um grupo de ação utilizando um modelo de Gestor de Recursos, cria-se um recurso do tipo `Microsoft.Insights/actionGroups` . Depois preenche todas as propriedades relacionadas. Aqui estão dois modelos de amostra que criam um grupo de ação.

Primeiro modelo, descreve como criar um modelo de Gestor de Recursos para um grupo de ação onde as definições de ação são codificadas no modelo. Segundo modelo, descreve como criar um modelo que toma a informação de configuração webhook como parâmetros de entrada quando o modelo é implementado.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com"
          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com"
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1"
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    },
    "webhookReceiverName": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service Name."
      }
    },    
    "webhookServiceUri": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    }    
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
        ],
        "emailReceivers": [
        ],
        "webhookReceivers": [
          {
            "name": "[parameters('webhookReceiverName')]",
            "serviceUri": "[parameters('webhookServiceUri')]"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupResourceId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [grupos de ação.](./action-groups.md)
* Saiba mais sobre [alertas.](alerts-overview.md)
* Aprenda a adicionar [alertas utilizando um modelo de Gestor de Recursos.](./alerts-activity-log.md)

