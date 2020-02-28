---
title: Envie alertas de saúde de serviço Azure com PagerDuty
description: Obtenha notificações personalizadas sobre eventos de saúde de serviço para a sua instância PagerDuty.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: bb449a5279f3cea55e6aec2f72edfd11fb26227a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77654074"
---
# <a name="send-azure-service-health-alerts-with-pagerduty-using-webhooks"></a>Envie alertas de saúde de serviço Azure com PagerDuty usando webhooks

Este artigo mostra-lhe como configurar notificações de saúde do serviço Azure através do PagerDuty usando um webhook. Ao utilizar o tipo de integração personalizado do Microsoft Azure do [PagerDuty,](https://www.pagerduty.com/)pode adicionar sem esforço alertas de Saúde de Serviço aos seus novos ou já existentes serviços PagerDuty.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Criação de um URL de integração de saúde de serviço em PagerDuty
1.  Certifique-se de que se inscreveu e está inscrito na sua conta [PagerDuty.](https://www.pagerduty.com/)

1.  Navegue para a secção **de Serviços** em PagerDuty.

    ![A secção "Serviços" em PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

1.  Selecione **Adicionar Novo Serviço** ou abra um serviço existente que tenha configurado.

1.  Nas **Definições de Integração,** selecione o seguinte:

    a. **Tipo de integração**: Microsoft Azure

    b. **Nome de Integração**: \<Nome\>

    ![As "Definições de Integração" no PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

1.  Preencha quaisquer outros campos necessários e selecione **Adicionar**.

1.  Abra esta nova integração e copie e guarde o **URL de Integração.**

    ![O "URL de Integração" em PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Criar um alerta usando pagerDuty no portal Azure
### <a name="for-a-new-action-group"></a>Para um novo grupo de ação:
1. Siga os passos 1 a 8 em Criar um alerta sobre uma notificação de [saúde de serviço para um novo grupo de ação utilizando o portal Azure](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

1. Definir na lista de **Ações:**

    a. **Tipo de ação:** *Webhook*

    b. **Detalhes:** O URL **de Integração** PagerDuty que guardou anteriormente.

    c. **Nome:** O nome do Webhook, pseudónimo, ou identificador.

1. Selecione **Guardar** quando estiver feito para criar o alerta.

### <a name="for-an-existing-action-group"></a>Para um grupo de ação existente:
1. No [portal Azure,](https://portal.azure.com/)selecione **Monitor**.

1. Na secção **Definições,** selecione **grupos de ação**.

1. Encontre e selecione o grupo de ação que pretende editar.

1. Adicione à lista de **Ações:**

    a. **Tipo de ação:** *Webhook*

    b. **Detalhes:** O URL **de Integração** PagerDuty que guardou anteriormente.

    c. **Nome:** O nome do Webhook, pseudónimo, ou identificador.

1. Selecione **Guardar** quando for feito para atualizar o grupo de ação.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testar a sua integração webhook através de um pedido HTTP POST
1. Crie a carga de saúde de serviço que pretende enviar. Pode encontrar uma carga útil de webhook de serviço de exemplo em Webhooks para alertas de registo de [atividade sinuosos do Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Crie um pedido HTTP POST da seguinte forma:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Deve receber um `202 Accepted` com uma mensagem que contenha o seu "ID do evento".

1. Vá ao [PagerDuty](https://www.pagerduty.com/) para confirmar que a sua integração foi criada com sucesso.

## <a name="next-steps"></a>Passos seguintes
- Saiba como [configurar notificações de webhook para sistemas de gestão de problemas existentes.](service-health-alert-webhook-guide.md)
- Reveja o esquema de alerta de registo de [atividade](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Conheça as notificações de saúde de [serviço.](../azure-monitor/platform/service-notifications.md)
- Saiba mais sobre [grupos de ação.](../azure-monitor/platform/action-groups.md)