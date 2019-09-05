---
title: Receber alertas do log de atividades nas notificações de serviço do Azure
description: Seja notificado por SMS, email ou webhook quando ocorrer o serviço do Azure.
author: stephbaron
ms.author: stbaron
services: monitoring
ms.service: service-health
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 40ffe0b377a5cbb21f07c479097958d7c15a2879
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383148"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Criar alertas do log de atividades em notificações de serviço
## <a name="overview"></a>Descrição geral

Este artigo mostra como configurar alertas do log de atividades para notificações de integridade do serviço usando o portal do Azure.  

As notificações de integridade do serviço são armazenadas no [log de atividades do Azure](../azure-monitor/platform/activity-logs-overview.md) , considerando o volume possivelmente grande de informações armazenadas no log de atividades, há uma interface do usuário separada para facilitar a exibição e a configuração de alertas sobre notificações de integridade do serviço. 

Você pode receber um alerta quando o Azure envia notificações de integridade do serviço para sua assinatura do Azure. Você pode configurar o alerta com base em:

- A classe de notificação de integridade do serviço (problemas de serviço, manutenção planejada, consultorias de integridade).
- A assinatura afetou.
- Os serviços foram afetados.
- As regiões afetadas.

> [!NOTE]
> As notificações de integridade do serviço não enviam um alerta sobre eventos do Resource Health.

Você também pode configurar para quem o alerta deve ser enviado:

- Selecione um grupo de ação existente.
- Crie um novo grupo de ação (que pode ser usado para futuros alertas).

Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ações](../azure-monitor/platform/action-groups.md).

Para obter informações sobre como configurar alertas de notificação de integridade do serviço usando modelos de Azure Resource Manager, consulte [modelos do Resource Manager](../azure-monitor/platform/alerts-activity-log.md).

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Assista a um vídeo sobre como configurar seu primeiro alerta de integridade do serviço do Azure

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="alert-and-new-action-group-using-azure-portal"></a>Alerta e novo grupo de ação usando portal do Azure
1. No [portal](https://portal.azure.com), selecione **integridade do serviço**.

    ![O serviço de "integridade do serviço"](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. Na seção **alertas** , selecione **alertas de integridade**.

    ![A guia "alertas de integridade"](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Selecione **criar alerta de integridade do serviço** e preencha os campos.

    ![O comando "criar alerta de integridade do serviço"](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Selecione a **assinatura**, os **Serviços**e as **regiões** para as quais você deseja ser alertado.

    ![A caixa de diálogo "adicionar alerta do log de atividades"](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

    > [!NOTE]
    > Essa assinatura é usada para salvar o alerta do log de atividades. O recurso de alerta é implantado nesta assinatura e monitora eventos no log de atividades para ele.

1. Escolha os **tipos de eventos** para os quais você deseja ser alertado: *Problema de serviço*, *manutenção planejada*e *consultorias de integridade* 

1. Defina os detalhes do alerta inserindo um nome e uma **Descrição**da **regra de alerta** .

1. Selecione o **grupo de recursos** onde você deseja que o alerta seja salvo.

1. Crie um novo grupo de ação selecionando **novo grupo de ação**. Insira um nome na caixa **nome do grupo de ações** e insira um nome na caixa **nome curto** . O nome curto é referenciado nas notificações que são enviadas quando esse alerta é disparado.

    ![Criar um novo grupo de ação](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. Defina uma lista de destinatários fornecendo o destinatário:

    a. **Nome**: Insira o nome, o alias ou o identificador do destinatário.

    b. **Tipo de ação**: Selecione SMS, email, webhook, aplicativo do Azure e muito mais.

    c. **Detalhes**: Com base no tipo de ação escolhido, insira um número de telefone, endereço de email, URI de webhook, etc.

1. Selecione **OK** para criar o grupo de ações e, em seguida, **criar regra de alerta** para concluir o alerta.

Em alguns minutos, o alerta está ativo e começa a disparar com base nas condições especificadas durante a criação.

Saiba como [configurar notificações de webhook para sistemas de gerenciamento de problemas existentes](service-health-alert-webhook-guide.md). Para obter informações sobre o esquema de webhook para alertas do log de atividades, consulte [WebHooks para alertas do log de atividades do Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

>[!NOTE]
>O grupo de ações definido nessas etapas é reutilizável como um grupo de ação existente para todas as definições de alerta futuras.
>

## <a name="alert-with-existing-action-group-using-azure-portal"></a>Alerta com grupo de ação existente usando portal do Azure

1. Siga as etapas 1 a 6 na seção anterior para criar sua notificação de integridade do serviço. 

1. Em **definir grupo de ações**, clique no botão **selecionar grupo de ações** . Selecione o grupo de ações apropriado.

1. Selecione **Adicionar** para adicionar o grupo de ações e, em seguida, **criar regra de alerta** para concluir o alerta.

Em alguns minutos, o alerta está ativo e começa a disparar com base nas condições especificadas durante a criação.

## <a name="alert-and-new-action-group-using-the-azure-resource-manager-templates"></a>Alerta e novo grupo de ação usando os modelos de Azure Resource Manager

Veja a seguir um exemplo que cria um grupo de ações com um destino de email e habilita todas as notificações de integridade do serviço para a assinatura de destino.

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

Depois de criar um alerta, ele fica visível na seção **alertas** do **Monitor**. Selecione o alerta que você deseja gerenciar para:

* Edite-o.
* Exclua-o.
* Desabilite ou habilite-o, se você quiser parar temporariamente ou continuar recebendo notificações para o alerta.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [as práticas recomendadas para configurar alertas de integridade do serviço do Azure](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Saiba como [configurar notificações por push móvel para a integridade do serviço do Azure](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Saiba como [configurar notificações de webhook para sistemas de gerenciamento de problemas existentes](service-health-alert-webhook-guide.md).
- Saiba mais sobre as [notificações de integridade do serviço](service-notifications.md).
- Saiba mais sobre a [limitação da taxa de notificação](../azure-monitor/platform/alerts-rate-limiting.md).
- Examine o [esquema de webhook de alerta do log de atividades](../azure-monitor/platform/activity-log-alerts-webhook.md).
- Obtenha uma [visão geral dos alertas do log de atividades](../azure-monitor/platform/alerts-overview.md)e saiba como receber alertas.
- Saiba mais sobre [grupos de ações](../azure-monitor/platform/action-groups.md).
