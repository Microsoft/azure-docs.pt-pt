---
title: Configurar alertas do Azure Resource Health usando modelos do Resource Manager | Microsoft Docs
description: Crie alertas programaticamente que o notifiquem quando os recursos do Azure ficarem indisponíveis.
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 9/4/2018
ms.openlocfilehash: 10a940e43b2ab4dff1b7c90aa7d6d274ddef82d9
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73023903"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Configurar alertas do Resource Health usando modelos do Resource Manager

Este artigo mostrará como criar Resource Health alertas do log de atividades programaticamente usando Azure Resource Manager modelos e Azure PowerShell.

Azure Resource Health mantém você informado sobre o status de integridade atual e histórico dos recursos do Azure. Azure Resource Health alertas podem notificá-lo quase em tempo real quando esses recursos têm uma alteração no status de integridade. Criar Resource Health alertas programaticamente permitem que os usuários criem e personalizem alertas em massa.

> [!NOTE]
> Resource Health alertas estão atualmente em versão prévia.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para seguir as instruções nesta página, você precisará configurar algumas coisas com antecedência:

1. Você precisa instalar o [módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)
2. Você precisa [criar ou reutilizar um grupo de ações](../azure-monitor/platform/action-groups.md) configurado para notificá-lo

## <a name="instructions"></a>Instruções
1. Usando o PowerShell, faça logon no Azure usando sua conta e selecione a assinatura com a qual você deseja interagir

        Login-AzAccount
        Select-AzSubscription -Subscription <subscriptionId>

    > Você pode usar `Get-AzSubscription` para listar as assinaturas às quais você tem acesso.

2. Localizar e salvar a ID de Azure Resource Manager completa para o grupo de ação

        (Get-AzActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id

3. Crie e salve um modelo do Resource Manager para alertas de Resource Health como `resourcehealthalert.json` ([Veja os detalhes abaixo](#resource-manager-template-options-for-resource-health-alerts))

4. Criar uma nova implantação de Azure Resource Manager usando este modelo

        New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>

5. Você será solicitado a digitar o nome do alerta e a ID do recurso do grupo de ações copiado anteriormente:

        Supply values for the following parameters:
        (Type !? for Help.)
        activityLogAlertName: <Alert Name>
        actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>

6. Se tudo funcionou com êxito, você receberá uma confirmação no PowerShell

        DeploymentName          : ExampleDeployment
        ResourceGroupName       : <resourceGroup>
        ProvisioningState       : Succeeded
        Timestamp               : 11/8/2017 2:32:00 AM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                                Name                     Type       Value
                                ===============          =========  ==========
                                activityLogAlertName     String     <Alert Name>
                                activityLogAlertEnabled  Bool       True
                                actionGroupResourceId    String     /...
        
        Outputs                 :
        DeploymentDebugLogLevel :

Observe que, se você estiver planejando automatizar completamente esse processo, basta editar o modelo do Resource Manager para não solicitar os valores na etapa 5.

## <a name="resource-manager-template-options-for-resource-health-alerts"></a>Opções de modelo do Resource Manager para alertas de Resource Health

Você pode usar esse modelo base como um ponto de partida para criar Resource Health alertas. Este modelo funcionará como escrito e o inscreverá para receber alertas para todos os eventos de integridade de recursos ativados recentemente em todos os recursos em uma assinatura.

> Na parte inferior deste artigo, também incluímos um modelo de alerta mais complexo que deve aumentar o sinal para a taxa de ruído para alertas de Resource Health em comparação com esse modelo.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": true,
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ResourceHealth"
            },
            {
              "field": "status",
              "equals": "Active"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

No entanto, um alerta amplo como este geralmente não é recomendado. Saiba como é possível desfocar esse alerta para se concentrar nos eventos que nos preocupamos abaixo.

### <a name="adjusting-the-alert-scope"></a>Ajustando o escopo do alerta

Resource Health alertas podem ser configurados para monitorar eventos em três escopos diferentes:

 * Nível de Subscrição
 * Nível do grupo de recursos
 * Nível de recurso

O modelo de alerta é configurado no nível de assinatura, mas se você quiser configurar seu alerta para apenas notificar sobre determinados recursos ou recursos em um determinado grupo de recursos, bastará modificar a seção `scopes` no modelo acima.

Para um escopo de nível de grupo de recursos, a seção Escopos deve ser semelhante a:
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

E para um escopo de nível de recurso, a seção de escopo deve ser semelhante a:

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

Por exemplo: `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> Você pode acessar o portal do Azure e examinar a URL ao exibir o recurso do Azure para obter essa cadeia de caracteres.

### <a name="adjusting-the-resource-types-which-alert-you"></a>Ajustando os tipos de recursos que alertam você

Os alertas na assinatura ou no nível do grupo de recursos podem ter tipos diferentes de recursos. Se você quiser limitar os alertas a serem fornecidos apenas de um determinado subconjunto de tipos de recursos, poderá defini-los na seção `condition` do modelo, desta forma:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.COMPUTE/VIRTUALMACHINES",
                    "containsAny": null
                },
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.STORAGE/STORAGEACCOUNTS",
                    "containsAny": null
                },
                ...
            ]
        }
    ]
},
```

Aqui, usamos o wrapper `anyOf` para permitir que o alerta de integridade do recurso corresponda a qualquer uma das condições que especificamos, permitindo alertas que se destinam a tipos de recursos específicos.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>Ajustar os eventos de Resource Health que alertam você
Quando os recursos passam por um evento de integridade, eles podem percorrer uma série de estágios que representam o estado do evento de integridade: `Active`, `In Progress`, `Updated`e `Resolved`.

Você pode querer ser notificado apenas quando um recurso se tornar não íntegro; nesse caso, você deseja configurar o alerta para notificar apenas quando o `status` for `Active`. No entanto, se você também quiser ser notificado sobre os outros estágios, poderá adicionar esses detalhes da seguinte maneira:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "status",
                    "equals": "Active"
                },
                {
                    "field": "status",
                    "equals": "In Progress"
                },
                {
                    "field": "status",
                    "equals": "Resolved"
                },
                {
                    "field": "status",
                    "equals": "Updated"
                }
            ]
        }
    ]
}
```

Se você quiser ser notificado para todos os quatro estágios de eventos de integridade, poderá remover essa condição, e o alerta o notificará independentemente da propriedade `status`.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>Ajuste dos alertas de Resource Health para evitar eventos "desconhecidos"

Azure Resource Health pode relatar a integridade mais recente de seus recursos, monitorando-os constantemente usando executores de teste. Os status de integridade informados relevantes são: "disponível", "não disponível" e "degradado". No entanto, em situações em que o executor e o recurso do Azure não conseguem se comunicar, um status de integridade "desconhecido" é relatado para o recurso e isso é considerado um evento de integridade "ativo".

No entanto, quando um recurso relata "desconhecido", é provável que seu status de integridade não tenha sido alterado desde o último relatório preciso. Se você quiser eliminar alertas em eventos "desconhecidos", poderá especificar essa lógica no modelo:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
        {
            "anyOf": [
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
    ]
},
```

Neste exemplo, estamos apenas notificando eventos em que o status de integridade atual e anterior não tem "desconhecido". Essa alteração pode ser uma adição útil se os alertas forem enviados diretamente para seu telefone celular ou email. 

Observe que é possível que as propriedades currentHealthStatus e previousHealthStatus sejam nulas em alguns eventos. Por exemplo, quando um evento atualizado ocorre, é provável que o status de integridade do recurso não tenha sido alterado desde o último relatório, apenas que informações adicionais do evento estejam disponíveis (por exemplo, causa). Portanto, usar a cláusula acima pode resultar em alguns alertas não sendo disparados, pois os valores de Properties. currentHealthStatus e Properties. previousHealthStatus serão definidos como NULL.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>Ajustando o alerta para evitar eventos iniciados pelo usuário

Resource Health eventos podem ser disparados por eventos iniciados pela plataforma e pelo usuário. Pode fazer sentido enviar apenas uma notificação quando o evento de integridade é causado pela plataforma do Azure.

É fácil configurar seu alerta para filtrar apenas esses tipos de eventos:

```json
"condition": {
    "allOf": [
        ...,
        {
            "field": "properties.cause",
            "equals": "PlatformInitiated",
            "containsAny": null
        }
    ]
}
```
Observe que é possível que o campo de causa seja nulo em alguns eventos. Ou seja, ocorre uma transição de integridade (por exemplo, disponível para indisponível) e o evento é registrado imediatamente para evitar atrasos de notificação. Portanto, o uso da cláusula acima pode resultar em um alerta não ser disparado, pois o valor da Propriedade Properties. cláusula será definido como NULL.

## <a name="complete-resource-health-alert-template"></a>Concluir Resource Health modelo de alerta

Usando os diferentes ajustes descritos na seção anterior, aqui está um modelo de exemplo que é configurado para maximizar o sinal para a taxa de ruído. Tenha em mente as advertências indicadas acima em que os valores de propriedade currentHealthStatus, previousHealthStatus e causa podem ser nulos em alguns eventos.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "activityLogAlertName": {
            "type": "string",
            "metadata": {
                "description": "Unique name (within the Resource Group) for the Activity log alert."
            }
        },
        "actionGroupResourceId": {
            "type": "string",
            "metadata": {
                "description": "Resource Id for the Action group."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Insights/activityLogAlerts",
            "apiVersion": "2017-04-01",
            "name": "[parameters('activityLogAlertName')]",
            "location": "Global",
            "properties": {
                "enabled": true,
                "scopes": [
                    "[subscription().id]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ResourceHealth",
                            "containsAny": null
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.cause",
                                    "equals": "PlatformInitiated",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "status",
                                    "equals": "Active",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Resolved",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "In Progress",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Updated",
                                    "containsAny": null
                                }
                            ]
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[parameters('actionGroupResourceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

No entanto, você saberá melhor quais configurações são eficazes para você, portanto, use as ferramentas ensinadas nesta documentação para fazer sua própria personalização.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o Resource Health:
-  [Visão geral de Azure Resource Health](Resource-health-overview.md)
-  [Os tipos de recursos e verificações do estado de funcionamento disponíveis através do Azure Resource Health](resource-health-checks-resource-types.md)


Criar alertas de integridade do serviço:
-  [Configurar alertas para integridade do serviço](../azure-monitor/platform/alerts-activity-log-service-notifications.md) 
-  [Esquema de evento do log de atividades do Azure](../azure-monitor/platform/activity-log-schema.md)
