---
title: Receber alertas de registo de atividade nas notificações do serviço do Azure
description: Seja notificado por SMS, e-mail ou webhook quando ocorre o serviço do Azure.
author: shawntabrizi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/09/2018
ms.author: shtabriz
ms.openlocfilehash: b5c11517209b9dfea8a28ce09084abd76d789d78
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270948"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Criar alertas do registo de atividade nas notificações do serviço
## <a name="overview"></a>Descrição geral
Este artigo mostra-lhe como configurar alertas de registo de atividade para notificações de estado de funcionamento do serviço com o portal do Azure.  

Pode receber um alerta quando o Azure envia notificações de estado de funcionamento do serviço para a sua subscrição do Azure. Pode configurar o alerta com base em:

- A classe de notificação de estado de funcionamento do serviço (problemas de serviço, manutenção planeada, as consultorias de estado de funcionamento).
- A subscrição afetada.
- Os serviços afetados.
- As regiões afetadas.

> [!NOTE]
> Notificações de estado de funcionamento do serviço não envia um alerta sobre recursos eventos de estado de funcionamento.

Também pode configurar que o alerta deve ser enviado para:

- Selecione um grupo de ação existente.
- Crie um novo grupo de ação (o que pode ser utilizado para futuros alertas).

Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ações](../../azure-monitor/platform/action-groups.md).

Para obter informações sobre como configurar alertas de notificação de estado de funcionamento do serviço, utilizando modelos Azure Resource Manager, consulte [modelos do Resource Manager](alerts-activity-log.md).

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-portal"></a>Criar um alerta numa notificação de estado de funcionamento do serviço para um novo grupo de ação com o portal do Azure
1. Na [portal](https://portal.azure.com), selecione **estado de funcionamento do serviço**.

    ![O serviço de "Estado de funcionamento do serviço"](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. Na **alertas** secção, selecione **alertas de estado de funcionamento**.

    ![O separador "Alertas de estado de funcionamento"](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Selecione **alerta de estado de funcionamento do serviço de criar** e preencha os campos.

    ![O comando "Criar alerta de estado de funcionamento do serviço"](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Selecione o **subscrição**, **serviços**, e **regiões** pretende ser alertado para.

    ![A caixa de diálogo "Adicionar alerta de registo de atividade"](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

> [!NOTE]
> Esta subscrição é utilizada para guardar o alerta de registo de atividades. O recurso de alerta é implementado para esta subscrição e monitoriza os eventos no registo de atividades para o mesmo.

1. Escolha o **tipos de evento** pretende ser alertado para: *Problema de serviço*, *manutenção planeada*, e *aconselhamentos sobre o estado de funcionamento* 

1. Definir os detalhes do alerta ao introduzir uma **nome da regra de alerta** e **Descrição**.

1. Selecione o **grupo de recursos** onde pretende que o alerta seja salvo.

1. Criar um novo grupo de ação selecionando **novo grupo de ação**. Introduza um nome na **nome do grupo de ação** caixa e introduza um nome na **nome abreviado** caixa. O nome abreviado é referenciado nas notificações que são enviadas quando este alerta é acionado.

    ![Criar um novo grupo de ação](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. Defina uma lista de destinatários ao indicar o recetor:

    a. **Nome**: Introduza o nome, o alias ou o identificador do recetor.

    b. **Tipo de ação**: Selecione o SMS, e-mail, webhook, aplicações do Azure e muito mais.

    c. **Detalhes**: Com base no tipo de ação escolhido, introduza um número de telefone, endereço de e-mail, webhook URI, etc.

1. Selecione **OK** para criar o grupo de ação e, em seguida **criar regra de alerta** para concluir o seu alerta.

Em poucos minutos, o alerta está ativo e começa a acionar, com base nas condições que especificou durante a criação.

Saiba como [configurar notificações de webhook para sistemas de gestão existentes do problema](../../service-health/service-health-alert-webhook-guide.md). Para obter informações sobre o esquema de webhook para alertas de registo de atividade, consulte [alertas de registo de Webhooks para atividades do Azure](../../azure-monitor/platform/activity-log-alerts-webhook.md).

>[!NOTE]
>O grupo de ações definido nessas etapas é reutilizável que um grupo de ação existente para todas as definições de alerta futuras.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>Criar um alerta numa notificação de estado de funcionamento do serviço para um grupo de ação existente com o portal do Azure

1. Siga os passos 1 a 7 na secção anterior para criar a notificação de estado de funcionamento do serviço. 

1. Sob **grupo de ação de definir**, clique nas **grupo de ação de seleção** botão. Selecione o grupo de medidas adequadas.

1. Selecione **Add** para adicionar o grupo de ação e, em seguida **criar regra de alerta** para concluir o seu alerta.

Em poucos minutos, o alerta está ativo e começa a acionar, com base nas condições que especificou durante a criação.

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-resource-manager-templates"></a>Criar um alerta numa notificação de estado de funcionamento do serviço para um novo grupo de ação com modelos do Azure Resource Manager

Segue-se um exemplo que cria um grupo de ação com um destino de e-mail e permite que todas as notificações de estado de funcionamento de serviço para a subscrição de destino.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "actionGroups_name": {
            "defaultValue": "SubHealth",
            "type": "String"
        },
        "activityLogAlerts_name": {
            "defaultValue": "ServiceHealthActivityLogAlert",
            "type": "String"
        },
        "emailAddress":{
            "type":"string"
        }
    },
    "variables": {
        "alertScope":"[concat('/','subscriptions','/',subscription().subscriptionId)]"
    },
    "resources": [
        {
            "comments": "Action Group",
            "type": "microsoft.insights/actionGroups",
            "name": "[parameters('actionGroups_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "groupShortName": "[parameters('actionGroups_name')]",
                "enabled": true,
                "emailReceivers": [
                    {
                        "name": "[parameters('actionGroups_name')]",
                        "emailAddress": "[parameters('emailAddress')]"
                    }
                ],
                "smsReceivers": [],
                "webhookReceivers": []
            },
            "dependsOn": []
        },
        {
            "comments": "Service Health Activity Log Alert",
            "type": "microsoft.insights/activityLogAlerts",
            "name": "[parameters('activityLogAlerts_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "scopes": [
                    "[variables('alertScope')]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ServiceHealth"
                        },
                        {
                            "field": "properties.incidentType",
                            "equals": "Incident"
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
                            "webhookProperties": {}
                        }
                    ]
                },
                "enabled": true,
                "description": ""
            },
            "dependsOn": [
                "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
            ]
        }
    ]
}
```

## <a name="manage-your-alerts"></a>Gerir os alertas

Depois de criar um alerta, é visível na **alertas** secção **Monitor**. Selecione o alerta que pretende gerir para:

* Editá-lo.
* Elimine-o.
* Desativar ou ativá-lo, se quiser temporariamente parar ou retomar a receção de notificações para o alerta.

## <a name="next-steps"></a>Passos Seguintes
- Saiba como [configurar notificações de webhook para sistemas de gestão existentes do problema](../../service-health/service-health-alert-webhook-guide.md).
- Saiba mais sobre [notificações de estado de funcionamento de serviço](../../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Saiba mais sobre [limitação de velocidade de notificação](../../monitoring-and-diagnostics/monitoring-alerts-rate-limiting.md).
- Reveja os [esquema de webhook de alerta de registo de atividades](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Obter um [descrição geral dos alertas de registo de atividade](../../monitoring-and-diagnostics/monitoring-overview-alerts.md)e saiba como receber alertas. 
- Saiba mais sobre [grupos de ação](../../azure-monitor/platform/action-groups.md).
