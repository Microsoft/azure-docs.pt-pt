---
title: Receba alertas de registo de atividades em notificações de serviço da Azure usando o portal Azure
description: Ser notificado via SMS, e-mail ou webhook quando o serviço Azure ocorrer.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: a8723698cddfb519687525820475517b93219a4a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85569005"
---
# <a name="create-activity-log-alerts-on-service-notifications-using-the-azure-portal"></a>Criar alertas de registo de atividades nas notificações de serviço utilizando o portal Azure
## <a name="overview"></a>Descrição geral

Este artigo mostra-lhe como usar o portal Azure para configurar alertas de registo de atividade para notificações de saúde de serviço, utilizando o portal Azure.  

As notificações de saúde do serviço são armazenadas no registo de [atividades do Azure](../azure-monitor/platform/platform-logs-overview.md) Dado o eventual grande volume de informação armazenada no registo de atividades, existe uma interface de utilizador separada para facilitar a visualização e configuração de alertas nas notificações de saúde do serviço. 

Pode receber um alerta quando o Azure enviar notificações de saúde de serviço para a sua assinatura Azure. Pode configurar o alerta com base em:

- A classe de notificação de saúde de serviço (problemas de serviço, manutenção planeada, avisos de saúde).
- A assinatura foi afetada.
- O(s) serviço(s) afetado.
- A(s) região(s) afetada.

> [!NOTE]
> As notificações de saúde do serviço não enviam um alerta sobre eventos de saúde de recursos.

Também pode configurar a quem o alerta deve ser enviado:

- Selecione um grupo de ação existente.
- Crie um novo grupo de ação (que pode ser usado para futuros alertas).

Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ações](../azure-monitor/platform/action-groups.md).

Para obter informações sobre como configurar alertas de notificação de saúde do serviço utilizando modelos do Gestor de Recursos Azure, consulte [os modelos do Gestor de Recursos](../azure-monitor/platform/alerts-activity-log.md).

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Veja um vídeo sobre a configuração do seu primeiro alerta de Saúde do Serviço Azure

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="alert-and-new-action-group-using-azure-portal"></a>Alerta e novo grupo de ação usando o portal Azure
1. No [portal](https://portal.azure.com), selecione **Service Health**.

    ![O serviço "Service Health"](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. Na secção **Alertas,** selecione **Alertas de Saúde**.

    ![O separador "Alertas de Saúde"](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Selecione **Criar alerta de saúde de serviço** e preencha os campos.

    ![O comando "Criar alerta de saúde de serviço"](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Selecione a **Subscrição,** **Serviços**e **Regiões** para as quais pretende ser alertado.

    ![A caixa de diálogo "Adicionar alerta de registo de atividade"](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

    > [!NOTE]
    > Esta subscrição é usada para salvar o alerta de registo de atividade. O recurso de alerta é implantado nesta subscrição e monitoriza eventos no registo de atividade para o mesmo.

1. Escolha os **tipos de Eventos** para os quais pretende ser alertado: *Emissão de serviço,* *manutenção planeada*e *avisos de saúde* 

1. Defina os seus dados de alerta introduzindo um **nome de regra de alerta** e **descrição**.

1. Selecione o **grupo de Recursos** onde deseja que o alerta seja guardado.

1. Criar um novo grupo de ação selecionando **novo grupo de ação**. Introduza um nome na caixa **de nome do grupo Action** e introduza um nome na caixa de **nomes Curto.** O nome curto é referenciado nas notificações enviadas quando este alerta dispara.

    ![Criar um novo grupo de ação](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. Definir uma lista de recetores fornecendo o do recetor:

    a. **Nome**: Introduza o nome, pseudónimo ou identificador do recetor.

    b. **Tipo de Ação**: Selecione SMS, e-mail, webhook, app Azure, entre outros.

    c. **Detalhes**: Com base no tipo de ação escolhido, insira um número de telefone, endereço de e-mail, webhook URI, etc.

1. Selecione **OK** para criar o grupo de ação e, em seguida, **crie a regra de alerta** para completar o seu alerta.

Dentro de poucos minutos, o alerta está ativo e começa a desencadear com base nas condições especificadas durante a criação.

Saiba como [configurar notificações webhook para os sistemas de gestão de problemas existentes](service-health-alert-webhook-guide.md). Para obter informações sobre o esquema webhook para alertas de registo de atividade, consulte [Webhooks para alertas de registo de atividades Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

>[!NOTE]
>O grupo de ação definido nestas etapas é reutilizável como um grupo de ação existente para todas as definições de alerta futuras.
>

## <a name="alert-with-existing-action-group-using-azure-portal"></a>Alerta com grupo de ação existente usando portal Azure

1. Siga os passos 1 a 6 na secção anterior para criar a notificação de saúde do seu serviço. 

1. No **grupo de ação Define,** clique no botão De grupo de ação **Select.** Selecione o grupo de ação apropriado.

1. **Selecione Adicionar** para adicionar o grupo de ação e, em seguida, **criar a regra de alerta** para completar o seu alerta.

Dentro de poucos minutos, o alerta está ativo e começa a desencadear com base nas condições especificadas durante a criação.


## <a name="next-steps"></a>Próximos passos
- Conheça as [melhores práticas para a criação de alertas de Saúde do Serviço Azure.](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa)
- Saiba como [configurar notificações de push móvel para a Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Saiba como [configurar notificações webhook para os sistemas de gestão de problemas existentes](service-health-alert-webhook-guide.md).
- Saiba mais sobre [as notificações de saúde do serviço.](service-notifications.md)
- Saiba mais sobre [a limitação da taxa de notificação.](../azure-monitor/platform/alerts-rate-limiting.md)
- Reveja o [esquema de alerta de registo de atividade webhook](../azure-monitor/platform/activity-log-alerts-webhook.md).
- Obtenha uma [visão geral dos alertas de registo de atividades](../azure-monitor/platform/alerts-overview.md)e aprenda a receber alertas.
- Saiba mais sobre [grupos de ação.](../azure-monitor/platform/action-groups.md)
