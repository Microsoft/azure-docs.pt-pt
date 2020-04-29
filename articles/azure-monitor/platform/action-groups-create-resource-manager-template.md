---
title: Criar grupos de ação com modelos de Gestor de Recursos
description: Aprenda a criar um grupo de ação usando um modelo de Gestor de Recursos Azure.
author: dkamstra
services: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 50ad9d57b24fab9ee57c2f9caae8f4c39d2681f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77669085"
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>Criar um grupo de ação com um modelo de Gestor de Recursos
Este artigo mostra-lhe como usar um modelo de Gestor de [Recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) para configurar grupos de ação. Ao utilizar modelos, pode configurar automaticamente grupos de ação que podem ser reutilizados em certos tipos de alertas. Estes grupos de ação asseguram que todas as partes corretas sejam notificadas quando é desencadeado um alerta.

Os passos básicos são:

1. Crie um modelo como um ficheiro JSON que descreva como criar o grupo de ação.

2. Implante o modelo utilizando [qualquer método de implementação](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

Primeiro, descrevemos como criar um modelo de Gestor de Recursos para um grupo de ação onde as definições de ação são codificadas no modelo. Em segundo lugar, descrevemos como criar um modelo que toma a informação de configuração do webhook como parâmetros de entrada quando o modelo é implementado.

## <a name="resource-manager-templates-for-an-action-group"></a>Modelos de Gestor de Recursos para um grupo de ação

Para criar um grupo de ação usando um modelo `Microsoft.Insights/actionGroups`de Gestor de Recursos, você cria um recurso do tipo . Depois preenchetodas as propriedades relacionadas. Aqui estão dois modelos de amostra que criam um grupo de ação.

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
* Saiba mais sobre [grupos de ação.](../../azure-monitor/platform/action-groups.md)
* Saiba mais sobre [alertas.](alerts-overview.md)
* Aprenda a adicionar [alertas utilizando um modelo](../../azure-monitor/platform/alerts-activity-log.md)de Gestor de Recursos .

