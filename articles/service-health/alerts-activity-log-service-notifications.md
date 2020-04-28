---
title: Receba alertas de registo de atividades em notificações de serviço do Azure
description: Seja notificado via SMS, e-mail ou webhook quando o serviço Azure ocorrer.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: d318adc76959ac24f4be9946167965a83053f632
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75749316"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Criar alertas do registo de atividades nas notificações do serviço
## <a name="overview"></a>Descrição geral

Este artigo mostra-lhe como configurar alertas de registo de atividade para notificações de saúde de serviço através do portal Azure.  

As notificações de saúde de serviço são armazenadas no registo de atividade do [Azure](../azure-monitor/platform/platform-logs-overview.md) Dado o volume possivelmente grande de informação armazenada no registo de atividade, existe uma interface separada do utilizador para facilitar a visualização e configuração de alertas sobre notificações de saúde de serviço. 

Pode receber um alerta quando o Azure enviar notificações de saúde de serviço para a sua subscrição do Azure. Pode configurar o alerta com base em:

- A classe de notificação de saúde de serviço (questões de serviço, manutenção planeada, avisos de saúde).
- A subscrição afetou.
- Os serviços(s) afetados.
- A ou a região afetada.

> [!NOTE]
> As notificações de saúde de serviço não enviam um alerta sobre eventos de saúde de recursos.

Também pode configurar a quem o alerta deve ser enviado para:

- Selecione um grupo de ação existente.
- Criar um novo grupo de ação (que pode ser usado para alertas futuros).

Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ações](../azure-monitor/platform/action-groups.md).

Para obter informações sobre como configurar os alertas de notificação de saúde do serviço utilizando modelos do Gestor de Recursos Azure, consulte [os modelos do Gestor](../azure-monitor/platform/alerts-activity-log.md)de Recursos .

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Veja um vídeo sobre a configuração do seu primeiro alerta de Saúde de Serviço Azure

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="alert-and-new-action-group-using-azure-portal"></a>Grupo de alerta e nova ação usando portal Azure
1. No [portal,](https://portal.azure.com)selecione **Service Health**.

    ![O serviço "Service Health"](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. Na secção **Alertas,** selecione alertas de **saúde**.

    ![O separador "Alertas de saúde"](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Selecione Criar alerta de **saúde** de serviço e preencha os campos.

    ![O comando "Criar alerta de saúde de serviço"](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Selecione a **Subscrição,** **Serviços**e **Regiões** para as quais deseja ser alertado.

    ![A caixa de diálogo "Adicionar alerta de registo de atividade"](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

    > [!NOTE]
    > Esta subscrição é utilizada para guardar o alerta de registo de atividade. O recurso de alerta é implantado nesta subscrição e monitoriza os eventos no registo de atividade para o mesmo.

1. Escolha os tipos de **eventos** para os quais pretende ser alertado: problema de *serviço,* *manutenção planeada*e avisos de *saúde* 

1. Defina os seus dados de alerta inserindo um nome e **descrição**da regra do **Alerta** .

1. Selecione o **grupo Recursos** onde pretende que o alerta seja guardado.

1. Crie um novo grupo de ação selecionando **novo grupo**de ação . Introduza um nome na caixa de **nomes** do grupo Action e introduza um nome na caixa **de nomes curtos.** O nome curto é referenciado nas notificações enviadas quando este alerta dispara.

    ![Criar um novo grupo de ação](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. Defina uma lista de recetores fornecendo o recetor:

    a. **Nome**: Introduza o nome, pseudónimo ou identificador do recetor.

    b. **Tipo de ação**: Selecione SMS, e-mail, webhook, app Azure, e muito mais.

    c. **Detalhes**: Com base no tipo de ação escolhido, introduza um número de telefone, endereço de e-mail, webhook URI, etc.

1. Selecione **OK** para criar o grupo de ação e, em seguida, Criar a regra de **alerta** para completar o seu alerta.

Dentro de poucos minutos, o alerta está ativo e começa a disparar com base nas condições que especificou durante a criação.

Saiba como [configurar notificações de webhook para sistemas de gestão de problemas existentes.](service-health-alert-webhook-guide.md) Para obter informações sobre o esquema do webhook para alertas de registo de atividade, consulte [webhooks para alertas](../azure-monitor/platform/activity-log-alerts-webhook.md)de registo de atividade do Azure .

>[!NOTE]
>O grupo de ação definido nestes passos é reutilizável como um grupo de ação existente para todas as futuras definições de alerta.
>

## <a name="alert-with-existing-action-group-using-azure-portal"></a>Alerta com grupo de ação existente usando portal Azure

1. Siga os passos 1 a 6 na secção anterior para criar a notificação de saúde do seu serviço. 

1. Em **definir grupo de ação,** clique no botão Select action **group.** Selecione o grupo de ação apropriado.

1. Selecione **Adicionar** para adicionar o grupo de ação **e,** em seguida, criar a regra de alerta para completar o seu alerta.

Dentro de poucos minutos, o alerta está ativo e começa a disparar com base nas condições que especificou durante a criação.

## <a name="alert-and-new-action-group-using-the-azure-resource-manager-templates"></a>Grupo de alerta e novo grupo de ação usando os modelos do Gestor de Recursos Azure

Segue-se um exemplo que cria um grupo de ação com um alvo de e-mail e permite todas as notificações de saúde de serviço para a subscrição alvo.

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

Depois de criar um alerta, é visível na secção **alertas** do **Monitor**. Selecione o alerta que pretende obter:

* Edite-o.
* Apague.
* Desative ou ative-o, caso pretenda parar ou retomar temporariamente a receção de notificações para o alerta.

## <a name="next-steps"></a>Passos seguintes
- Conheça [as melhores práticas para a criação de alertas](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa)de Saúde de Serviço Azure .
- Saiba como [configurar notificações de push móvel para a Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Saiba como [configurar notificações de webhook para sistemas de gestão de problemas existentes.](service-health-alert-webhook-guide.md)
- Conheça as notificações de saúde de [serviço.](service-notifications.md)
- Saiba mais sobre o limite da taxa de [notificação.](../azure-monitor/platform/alerts-rate-limiting.md)
- Reveja o esquema de alerta de registo de [atividade](../azure-monitor/platform/activity-log-alerts-webhook.md).
- Obtenha uma [visão geral dos alertas de registo de atividadee](../azure-monitor/platform/alerts-overview.md)aprenda a receber alertas.
- Saiba mais sobre [grupos de ação.](../azure-monitor/platform/action-groups.md)
