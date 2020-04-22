---
title: Modelo para criar alertas de saúde de recursos
description: Crie alertas programáticamente que o notifiquem quando os seus recursos Azure ficam indisponíveis.
ms.topic: conceptual
ms.date: 9/4/2018
ms.openlocfilehash: 60ff5bdf2f4f0dab94c18fd7c751869c1893ad65
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759010"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Configure alertas de saúde de recursos usando modelos de Gestor de Recursos

Este artigo irá mostrar-lhe como criar alertas de log de atividade de saúde de recursos programáticamente usando modelos de Gestor de Recursos Azure e Azure PowerShell.

A Azure Resource Health mantém-no informado sobre o estado de saúde atual e histórico dos seus recursos Azure. Os alertas de Saúde de Recursos Azure podem notificá-lo em tempo real quando estes recursos têm uma mudança no seu estado de saúde. Criar alertas de Saúde de Recursos, permite que os utilizadores criem e personalizem alertas a granel.

> [!NOTE]
> Os alertas de Saúde de Recursos estão atualmente em pré-visualização.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para seguir as instruções desta página, terá de configurar algumas coisas com antecedência:

1. Precisa de instalar o [módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)
2. Você precisa [criar ou reutilizar um Grupo](../azure-monitor/platform/action-groups.md) de Ação configurado para notificá-lo

## <a name="instructions"></a>Instruções
1. Utilizando o PowerShell, inicie sessão no Azure utilizando a sua conta e selecione a subscrição com a quais pretende interagir

        Login-AzAccount
        Select-AzSubscription -Subscription <subscriptionId>

    > Pode utilizar `Get-AzSubscription` para listar as subscrições a que tem acesso.

2. Encontre e guarde o ID completo do Gestor de Recursos Azure para o seu Grupo de Ação

        (Get-AzActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id

3. Criar e guardar um modelo de Gestor `resourcehealthalert.json` de Recursos para alertas de Saúde de Recursos como[(ver detalhes abaixo)](#resource-manager-template-options-for-resource-health-alerts)

4. Crie uma nova implementação do Gestor de Recursos Azure usando este modelo

        New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>

5. Será solicitado a escrever o Nome de Alerta e identificação de recurso do Grupo de Ação que copiou anteriormente:

        Supply values for the following parameters:
        (Type !? for Help.)
        activityLogAlertName: <Alert Name>
        actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>

6. Se tudo funcionar com sucesso, terá uma confirmação no PowerShell

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

Note que se estiver a planear automatizar totalmente este processo, basta editar o modelo do Gestor de Recursos para não solicitar os valores no Passo 5.

## <a name="resource-manager-template-options-for-resource-health-alerts"></a>Opções de modelo de gestor de recursos para alertas de saúde de recursos

Pode usar este modelo de base como ponto de partida para criar alertas de Saúde de Recursos. Este modelo funcionará como escrito, e irá inscrevê-lo para receber alertas para todos os eventos de saúde de recursos recentemente ativados em todos os recursos numa subscrição.

> Na parte inferior deste artigo também incluímos um modelo de alerta mais complexo que deve aumentar a relação sinal/ruído para alertas de saúde de recursos em comparação com este modelo.

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

No entanto, um alerta geralmente deste não é recomendado. Saiba como podemos analisar este alerta para nos concentrarmos nos eventos com que nos preocupamos abaixo.

### <a name="adjusting-the-alert-scope"></a>Ajustar o âmbito do alerta

Os alertas de saúde de recursos podem ser configurados para monitorizar eventos em três âmbitos diferentes:

 * Nível de subscrição
 * Nível de Grupo de Recursos
 * Nível de Recursos

O modelo de alerta está configurado ao nível da subscrição, mas se quiser configurar o seu alerta para apenas `scopes` notificá-lo sobre certos recursos, ou recursos dentro de um determinado grupo de recursos, simplesmente precisa modificar a secção no modelo acima.

Para um âmbito de nível de grupo de recursos, a secção de âmbitos deve parecer:
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

E para um âmbito de nível de recursos, a secção de âmbito deve parecer:

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

Por exemplo: `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> Pode ir ao Portal Azure e ver o URL ao visualizar o seu recurso Azure para obter esta cadeia.

### <a name="adjusting-the-resource-types-which-alert-you"></a>Ajustar os tipos de recursos que o alertam

Os alertas a nível de subscrição ou grupo de recursos podem ter diferentes tipos de recursos. Se quiser limitar os alertas a apenas provenientes de um determinado `condition` subconjunto de tipos de recursos, pode defini-lo na secção do modelo como:

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

Aqui usamos `anyOf` o invólucro para permitir que o alerta de saúde do recurso corresponda a qualquer uma das condições que especificamos, permitindo alertas que visam tipos de recursos específicos.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>Ajustar os eventos de Saúde de Recursos que o alertam
Quando os recursos são submetidos a um evento de saúde, podem `Active`passar `In Progress` `Updated`por `Resolved`uma série de etapas que representam o estado do evento de saúde: , , e .

Só pode querer ser notificado quando um recurso se torna insalubre, caso em `status` `Active`que pretende configurar o seu alerta apenas para notificar quando o for . No entanto, se também quiser ser notificado nos outros palcos, pode adicionar esses detalhes como o seguinte:

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

Se quiser ser notificado para as quatro fases dos eventos de saúde, pode remover `status` esta condição todas juntas, e o alerta irá notificá-lo independentemente da propriedade.

> [!NOTE]
> Cada secção "anyOf" deve conter apenas um tipo de campo valores.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>Ajustar os alertas de Saúde de Recursos para evitar eventos "desconhecidos"

A Azure Resource Health pode informar-lhe a mais recente saúde dos seus recursos, monitorizando-os constantemente utilizando corredores de teste. Os estatutos de saúde relevantes são: "Disponível", "Indisponível" e "Degradado". No entanto, em situações em que o corredor e o recurso Azure não conseguem comunicar, é reportado um estado de saúde "desconhecido" para o recurso, que é considerado um evento de saúde "ativo".

No entanto, quando um recurso reporta "Desconhecido", é provável que o seu estado de saúde não tenha mudado desde o último relatório preciso. Se quiser eliminar alertas sobre eventos "desconhecidos", pode especificar essa lógica no modelo:

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

Neste exemplo, estamos apenas a notificar os acontecimentos em que o estado de saúde atual e anterior não tem "Desconhecido". Esta alteração pode ser uma adição útil se os seus alertas forem enviados diretamente para o seu telemóvel ou e-mail. 

Note que é possível que as propriedades atuais do HealthStatus e anteriores do HealthStatus sejam nulas em alguns eventos. Por exemplo, quando ocorre um evento atualizado é provável que o estado de saúde do recurso não tenha mudado desde o último relatório, apenas que a informação adicional do evento está disponível (por exemplo, causa). Portanto, a utilização da cláusula acima pode resultar em alguns alertas não serem desencadeados, porque as propriedades.actualHealthStatus e propriedades.os valores anteriores do HealthStatus serão definidos como nulos.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>Ajustar o alerta para evitar eventos iniciados pelo utilizador

Os eventos de Saúde de Recursos podem ser desencadeados pela plataforma iniciada e eventos iniciados pelo utilizador. Pode fazer sentido enviar apenas uma notificação quando o evento de saúde for causado pela plataforma Azure.

É fácil configurar o seu alerta para filtrar apenas para este tipo de eventos:

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
Note que é possível que o campo causa seja nulo em alguns eventos. Ou seja, realiza-se uma transição para a saúde (por exemplo, disponível para indisponíveis) e o evento é imediatamente registado para evitar atrasos na notificação. Portanto, a utilização da cláusula acima pode resultar em um alerta não ser desencadeado, porque o valor da propriedade.cláusula será definido como nulo.

## <a name="complete-resource-health-alert-template"></a>Modelo completo de alerta de saúde de recursos

Utilizando os diferentes ajustes descritos na secção anterior, aqui está um modelo de amostra que é configurado para maximizar a relação sinal/ruído. Tenha em mente as ressalvas acima referidas onde o atual HealthStatus, anteriorHealthStatus, e causar valores de propriedade podem ser nulos em alguns eventos.

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

No entanto, saberá melhor quais as configurações que são eficazes para si, por isso use as ferramentas que lhe são ensinadas nesta documentação para fazer a sua própria personalização.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a Saúde dos Recursos:
-  [Visão geral da Saúde dos Recursos Azure](Resource-health-overview.md)
-  [Os tipos de recursos e verificações do estado de funcionamento disponíveis através do Azure Resource Health](resource-health-checks-resource-types.md)


Criar Alertas de Saúde de Serviço:
-  [Configurar alertas para a saúde do serviço](../azure-monitor/platform/alerts-activity-log-service-notifications.md) 
-  [Esquema de evento de log de atividade azure](../azure-monitor/platform/activity-log-schema.md)
