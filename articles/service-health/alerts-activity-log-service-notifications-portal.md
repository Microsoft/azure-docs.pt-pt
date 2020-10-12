---
title: Receba alertas de registo de atividades em notificações de serviço da Azure usando o portal Azure
description: Saiba como utilizar o portal Azure para configurar alertas de registo de atividade para notificações de saúde de serviço utilizando o portal Azure.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 88fbdeeb8bdcc239f1591e053aaf4fb1c36b9b19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289791"
---
# <a name="create-activity-log-alerts-on-service-notifications-using-the-azure-portal"></a>Criar alertas de registo de atividades nas notificações de serviço utilizando o portal Azure
## <a name="overview"></a>Descrição geral

Este artigo mostra-lhe como usar o portal Azure para configurar alertas de registo de atividade para notificações de saúde de serviço, utilizando o portal Azure.  

As notificações de saúde do serviço são armazenadas no registo de [atividades do Azure](../azure-monitor/platform/platform-logs-overview.md). Dado o grande volume de informação armazenada no registo de atividade, existe uma interface de utilizador separada para facilitar a visualização e configuração de alertas nas notificações de saúde do serviço. 

Pode receber um alerta quando o Azure enviar notificações de saúde de serviço para a sua assinatura Azure. Pode configurar o alerta com base em:

- A classe de notificação de saúde de serviço (questões de serviço, manutenção planeada, avisos de saúde, avisos de segurança).
- A assinatura foi afetada.
- O(s) serviço(s) afetado.
- A(s) região(s) afetada.

> [!NOTE]
> As notificações de saúde do serviço não enviam alertas para eventos de saúde de recursos.

Também pode configurar a quem o alerta deve ser enviado:

- Selecione um grupo de ação existente.
- Crie um novo grupo de ação (que pode ser usado para futuros alertas).

Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ações](../azure-monitor/platform/action-groups.md).

Para obter informações sobre como configurar alertas de notificação de saúde do serviço utilizando modelos do Gestor de Recursos Azure, consulte [os modelos do Gestor de Recursos](../azure-monitor/platform/alerts-activity-log.md).

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Veja um vídeo sobre a configuração do seu primeiro alerta de Saúde do Serviço Azure

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="create-service-health-alert-using-azure-portal"></a>Criar alerta de saúde de serviço usando o portal Azure
1. No [portal](https://portal.azure.com), selecione **Service Health**.

    ![O serviço "Service Health"](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. Na secção **Alertas,** selecione **Alertas de Saúde**.

    ![O separador "Alertas de Saúde"](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. **Selecione Adicionar alerta de saúde de serviço** e preencha os campos.

    ![O comando "Criar alerta de saúde de serviço"](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Selecione a **Subscrição,** **Serviços**e **Regiões** para as quais pretende ser alertado.

    [![A caixa de diálogo "Adicionar alerta de registo de atividade"](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png#lightbox)

> [!NOTE]
>Esta subscrição é usada para salvar o alerta de registo de atividade. O recurso de alerta é implantado nesta subscrição e monitoriza eventos no registo de atividade para o mesmo.

5. Escolha os **tipos de Eventos** para os quais pretende ser alertado para: *Emissão de serviço,* *manutenção planeada,* *avisos de saúde*e *aviso de Segurança.*

6. Clique **em Selecionar grupo de ação** para escolher um grupo de ação existente ou para criar um novo grupo de ação. Para obter mais informações sobre grupos de ação, consulte [criar e gerir grupos de ação no portal Azure.](../azure-monitor/platform/action-groups.md)


7. Defina os seus dados de alerta introduzindo um **nome de regra de alerta** e **descrição**.

8. Selecione o **grupo de Recursos** onde deseja que o alerta seja guardado.



Dentro de poucos minutos, o alerta está ativo e começa a desencadear com base nas condições especificadas durante a criação.

Saiba como [configurar notificações webhook para os sistemas de gestão de problemas existentes](service-health-alert-webhook-guide.md). Para obter informações sobre o esquema webhook para alertas de registo de atividade, consulte [Webhooks para alertas de registo de atividades Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).


## <a name="next-steps"></a>Passos seguintes
- Conheça as [melhores práticas para a criação de alertas de Saúde do Serviço Azure.](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa)
- Saiba como [configurar notificações de push móvel para a Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Saiba como [configurar notificações webhook para os sistemas de gestão de problemas existentes](service-health-alert-webhook-guide.md).
- Saiba mais sobre [as notificações de saúde do serviço.](service-notifications.md)
- Saiba mais sobre [a limitação da taxa de notificação.](../azure-monitor/platform/alerts-rate-limiting.md)
- Reveja o [esquema de alerta de registo de atividade webhook](../azure-monitor/platform/activity-log-alerts-webhook.md).
- Obtenha uma [visão geral dos alertas de registo de atividades](../azure-monitor/platform/alerts-overview.md)e aprenda a receber alertas.
- Saiba mais sobre [grupos de ação.](../azure-monitor/platform/action-groups.md)
