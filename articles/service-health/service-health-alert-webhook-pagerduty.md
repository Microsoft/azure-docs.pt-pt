---
title: Enviar alertas de saúde do serviço Azure com PagerDuty
description: Receba notificações personalizadas sobre eventos de saúde de serviço para o seu pagerDuty.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 7ae5141f33ebf4ec6e32029625af3a199a04d67b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594537"
---
# <a name="send-azure-service-health-alerts-with-pagerduty-using-webhooks"></a>Envie alertas de saúde do serviço Azure com pagerDuty usando webhooks

Este artigo mostra-lhe como configurar notificações de saúde do serviço Azure através do PagerDuty usando um webhook. Ao utilizar o tipo de integração personalizada do Microsoft Azure do [PagerDuty,](https://www.pagerduty.com/)pode adicionar sem esforço alertas de Saúde de Serviço aos seus serviços de PagerDuty novos ou existentes.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Criar um URL de integração de saúde de serviço em PagerDuty
1.  Certifique-se de que se inscreveu e está inscrito na sua conta [PagerDuty.](https://www.pagerduty.com/)

1.  Navegue para a secção **serviço em** PagerDuty.

    ![A secção "Serviços" em PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

1.  **Selecione Adicionar Novo Serviço** ou abra um serviço existente que tenha criado.

1.  Nas **Definições de Integração,** selecione o seguinte:

    a. **Tipo de Integração**: Microsoft Azure

    b. **Nome de integração**: \<Name\>

    ![As "Definições de Integração" no PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

1.  Preencha quaisquer outros campos necessários e **selecione Adicionar**.

1.  Abra esta nova integração e copie e guarde o **URL de Integração.**

    ![O "URL de Integração" em PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Criar um alerta usando PagerDuty no portal Azure
### <a name="for-a-new-action-group"></a>Para um novo grupo de ação:
1. Siga os passos 1 a 8 no [Criar um alerta sobre uma notificação de saúde de serviço para um novo grupo de ação através do portal Azure](./alerts-activity-log-service-notifications-portal.md).

1. Definir na lista de **ações:**

    a. **Tipo de ação:** *Webhook*

    b. **Detalhes:** O URL de **Integração** PagerDuty que guardou anteriormente.

    c. **Nome:** Nome de Webhook, pseudónimo ou identificador.

1. **Selecione Guardar** quando for feito para criar o alerta.

### <a name="for-an-existing-action-group"></a>Para um grupo de ação existente:
1. No [portal Azure,](https://portal.azure.com/)selecione **Monitor**.

1. Na secção **Definições,** selecione **Grupos de Ação**.

1. Encontre e selecione o grupo de ação que pretende editar.

1. Adicione à lista de **Ações:**

    a. **Tipo de ação:** *Webhook*

    b. **Detalhes:** O URL de **Integração** PagerDuty que guardou anteriormente.

    c. **Nome:** Nome de Webhook, pseudónimo ou identificador.

1. **Selecione Guardar** quando for feito para atualizar o grupo de ação.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testar a sua integração webhook através de um pedido HTTP POST
1. Crie a carga útil de saúde do serviço que pretende enviar. Você pode encontrar um serviço de exemplo webhook payload em [Webhooks para alertas de registo de atividade Azure](../azure-monitor/alerts/activity-log-alerts-webhook.md).

1. Crie um pedido HTTP POST da seguinte forma:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Deve receber uma `202 Accepted` mensagem contendo o seu "ID do evento".

1. Vá ao [PagerDuty](https://www.pagerduty.com/) confirmar que a sua integração foi configurada com sucesso.

## <a name="next-steps"></a>Passos seguintes
- Saiba como [configurar notificações webhook para os sistemas de gestão de problemas existentes](service-health-alert-webhook-guide.md).
- Reveja o [esquema de alerta de registo de atividade webhook](../azure-monitor/alerts/activity-log-alerts-webhook.md). 
- Saiba mais sobre [as notificações de saúde do serviço.](./service-notifications.md)
- Saiba mais sobre [grupos de ação.](../azure-monitor/alerts/action-groups.md)
