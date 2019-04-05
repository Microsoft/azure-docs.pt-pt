---
title: Configurar alertas de estado de funcionamento de recursos do Azure com modelos do Resource Manager | Documentos da Microsoft
description: Crie alertas por meio de programação que o notificam quando os recursos do Azure fiquem indisponíveis.
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 9/4/2018
ms.openlocfilehash: afa89fc90552c7ccba1fcea0945ee223d0096be4
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047522"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Configurar alertas de estado de funcionamento de recursos com modelos do Resource Manager

Este artigo irá mostrar como criar programaticamente usando modelos Azure Resource Manager e o Azure PowerShell dos alertas de registo de atividade para Estado de funcionamento do recurso.

O Azure mantém de estado de funcionamento do recurso informado sobre o estado de funcionamento atuais e históricas de recursos do Azure. Alertas de estado de funcionamento de recursos do Azure podem notificá-lo em quase em tempo real quando esses recursos têm uma alteração no respetivo estado de funcionamento. Estado de funcionamento do recurso a criar alertas por meio de programação permitem aos utilizadores criar e personalizar alertas em massa.

> [!NOTE]
> Alertas de estado de funcionamento do recurso estão atualmente em pré-visualização.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para seguir as instruções nesta página, terá de configurar algumas coisas antecipadamente:

1. Tem de instalar o [módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)
2. Precisa [criar ou reutilizar um grupo de ação](../azure-monitor/platform/action-groups.md) configurado para notificá-lo

## <a name="instructions"></a>Instruções
1. Com o PowerShell, inicie sessão no Azure com a sua conta e selecione a subscrição que pretende interagir com

        Login-AzAccount
        Select-AzSubscription -Subscription <subscriptionId>

    > Pode usar `Get-AzSubscription` para listar as subscrições, terá acesso ao.

2. Localizar e guarde o ID de Gestor de recursos do Azure completa para o seu grupo de ação

        (Get-AzActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id

3. Criar e guardar um modelo do Resource Manager para alertas de estado de funcionamento do recurso, como `resourcehealthalert.json` ([veja os detalhes abaixo](#resource-manager-template-for-resource-health-alerts))

4. Criar uma nova implementação do Azure Resource Manager utilizando este modelo

        New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>

5. Será solicitado a digitar o nome do alerta e o ID de recurso do grupo de ação que copiou anteriormente:

        Supply values for the following parameters:
        (Type !? for Help.)
        activityLogAlertName: <Alert Name>
        actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>

6. Se está tudo a funcionar com êxito, receberá uma confirmação no PowerShell

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

Tenha em atenção que se estiver a planear sobre como automatizar completamente a esse processo, simplesmente precisa editar o modelo do Resource Manager, a não solicitar os valores no passo 5.

## <a name="resource-manager-template-for-resource-health-alerts"></a>Modelo do Resource Manager para alertas de estado de funcionamento do recurso

Pode utilizar este modelo de base como ponto de partida para a criação de alertas de estado de funcionamento do recurso. Este modelo irá funcionar como está escrito e iniciar a sua cópia de segurança para receber alertas para todos os eventos de estado de funcionamento do recurso recentemente ativada em todos os recursos numa subscrição.

> Na parte inferior deste artigo incluímos também um modelo de alerta mais complexo que deve aumentar o sinal a proporção de ruído de alertas de estado de funcionamento do recurso em comparação com este modelo.

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

No entanto, um alerta de amplo como esta, geralmente, não é recomendado. Saiba como estamos pode definir o âmbito para baixo deste alerta se concentre nos eventos que nos interessa abaixo.

### <a name="adjusting-the-alert-scope"></a>Ajustar o âmbito de alerta

Alertas de estado de funcionamento de recursos podem ser configuradas para monitorar eventos em três âmbitos diferentes:

 * Nível de Subscrição
 * Nível de grupo de recursos
 * Nível de recursos

O modelo de alerta é configurado ao nível da subscrição, mas se desejar configurar o alerta para notificar apenas determinados recursos ou recursos dentro de um determinado grupo de recursos, basta modificar o `scopes` secção acima modelo.

Para um recurso nível âmbito de grupo, a seção de âmbitos deve ser semelhante:
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

E para um âmbito de nível de recursos, a seção de âmbito deve ser semelhante:

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

Por exemplo: `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> Pode aceder ao Portal do Azure e ver o URL ao visualizar o seu recurso do Azure para obter essa cadeia de caracteres.

### <a name="adjusting-the-resource-types-which-alert-you"></a>Ajustar o recurso de tipos que alertá-lo

Os alertas da subscrição ou ao nível do grupo de recursos podem ter diferentes tipos de recursos. Se pretender limitar os alertas provenientes apenas de um determinado subconjunto de tipos de recursos, pode definir isso no `condition` secção do modelo da seguinte forma:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "resourceType",
                    "equals": "Microsoft.Compute/virtualMachines",
                    "containsAny": null
                },
                {
                    "field": "resourceType",
                    "equals": "Microsoft.Storage/storageAccounts",
                    "containsAny": null
                },
                ...
            ]
        }
    ]
},
```

Aqui, vamos utilizar o `anyOf` wrapper para permitir que o alerta de estado de funcionamento recurso corresponder a qualquer uma das condições que especificar, permitindo que para os alertas que visam tipos de recurso específico.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>Ajustar os eventos de estado de funcionamento do recurso que alertá-lo
Quando os recursos passam por um evento de estado de funcionamento, podem aceder através de uma série de fases que representa o estado do evento de estado de funcionamento: `Active`, `InProgress`, `Updated`, e `Resolved`.

Que apenas pretende ser notificado quando um recurso se torna mau estado de funcionamento, caso em que pretende configurar o alerta para notificar apenas quando os `status` é `Active`. Entretanto se pretender também ser notificado sobre as outras fases, pode adicionar os detalhes da seguinte forma:

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
                    "equals": "InProgress"
                },
                        {
                    "field": "status",
                    "equals": "Resolved"
                }
            ]
        }
    ]
}
```

Se pretender ser notificado para todas as quatro fases de eventos de estado de funcionamento, pode remover esta condição todas as ferramentas e o alerta irá notificá-lo irrespective do `status` propriedade.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>Ajustar os alertas de estado de funcionamento do recurso para evitar eventos "Desconhecidos"

Estado de funcionamento de recursos do Azure pode denunciar para o estado de funcionamento dos seus recursos mais recentes ao monitorizar constantemente com os concorrentes de teste. Os Estados de estado de funcionamento comunicadas relevantes são: "Disponível", "Indisponível" e "Degradado". No entanto, em situações em que o executor e o recurso do Azure são incapazes de comunicar, um Estado de funcionamento "Desconhecido" é reportado para o recurso e que é considerado um evento de estado de funcionamento "Ativos".

No entanto, quando um recurso de relatórios "Desconhecido", é provável que o respetivo estado de funcionamento não foi alterado desde o último relatório preciso. Se quiser eliminar alertas nos eventos "Desconhecidos", é possível especificar essa lógica no modelo:

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

Neste exemplo, podemos são notificação apenas nos eventos em que o estado de funcionamento atuais e anteriores não tem "Desconhecido". Esta alteração pode ser uma adição útil se os alertas são enviados diretamente para o telemóvel ou e-mail.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>Ajustar o alerta para evitar eventos iniciada pelo utilizador

Eventos de estado de funcionamento do recurso podem ser acionador pela plataforma iniciada e eventos iniciada pelo utilizador. Talvez faça sentido para enviar apenas uma notificação quando o evento de estado de funcionamento é causado pela plataforma do Azure.

É fácil configurar o alerta para filtrar para apenas esses tipos de eventos:

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

## <a name="recommended-resource-health-alert-template"></a>Modelo de alerta de estado de funcionamento de recursos recomendado

Usando os ajustes diferentes descritos na secção anterior, podemos criar um modelo abrangente de alerta que está configurado para maximizar o sinal a proporção de ruído.

Eis o que sugerimos que use:
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
                                    "equals": "InProgress",
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

No entanto, será melhor saber quais configurações são eficazes para, por isso, utilize as ferramentas ensinadas para nesta documentação para tornar a sua própria personalização.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o estado de funcionamento do recurso:
-  [Descrição geral do Estado de funcionamento de recursos do Azure](Resource-health-overview.md)
-  [Os tipos de recursos e verificações do estado de funcionamento disponíveis através do Azure Resource Health](resource-health-checks-resource-types.md)

Crie alertas de estado de funcionamento do serviço:
-  [Configurar alertas para o estado de funcionamento do serviço](../azure-monitor/platform/alerts-activity-log-service-notifications.md) 
