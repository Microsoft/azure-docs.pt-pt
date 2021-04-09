---
title: Envie alertas de saúde do serviço Azure com a OpsGenie usando webhooks
description: Receba notificações personalizadas sobre eventos de saúde de serviço para a sua instância OpsGenie.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: d8867d442d0c7fe563f6429fc1ff4edb212737c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594605"
---
# <a name="send-azure-service-health-alerts-with-opsgenie-using-webhooks"></a>Envie alertas de saúde do serviço Azure com a OpsGenie usando webhooks

Este artigo mostra-lhe como configurar alertas de saúde do serviço Azure com a OpsGenie usando um webhook. Ao utilizar a Integração de Saúde do Serviço Azure da [OpsGenie,](https://www.opsgenie.com/)pode encaminhar os alertas de Saúde do Serviço Azure para a OpsGenie. A OpsGenie pode determinar as pessoas certas para notificar com base em horários de chamada, usando e-mails, mensagens de texto (SMS), chamadas telefónicas, iOS & notificações push Android e alertas de escalada até que o alerta seja reconhecido ou fechado.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Criação de um URL de integração de saúde de serviço em OpsGenie
1.  Certifique-se de que se inscreveu e está assinado na sua conta [OpsGenie.](https://www.opsgenie.com/)

1.  Navegue para a secção **integrações** em OpsGenie.

    ![A secção "Integrações" em OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  Selecione o botão de integração **da Saúde do Serviço Azure.**

    ![O "botão de saúde do serviço Azure" em OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  **Nomeie** o seu alerta e especifique o campo **Atribuído à Equipa.**

1.  Preencha outros campos como **destinatários,** **Ativados** e **Notificações Suprimidas**.

1.  Copie e guarde o **URL de Integração,** que já deve conter o seu `apiKey` apêndice até ao fim.

    ![O "URL de Integração" em OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

1.  Selecione **Save Integration**

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Crie um alerta usando OpsGenie no portal Azure
### <a name="for-a-new-action-group"></a>Para um novo grupo de ação:
1. Siga os passos 1 a 8 no [Criar um alerta sobre uma notificação de saúde de serviço para um novo grupo de ação através do portal Azure](./alerts-activity-log-service-notifications-portal.md).

1. Definir na lista de **ações:**

    a. **Tipo de ação:** *Webhook*

    b. **Detalhes:** O **URL de Integração** OpsGenie que guardou anteriormente.

    c. **Nome:** Nome de Webhook, pseudónimo ou identificador.

1. **Selecione Guardar** quando for feito para criar o alerta.

### <a name="for-an-existing-action-group"></a>Para um grupo de ação existente:
1. No [portal Azure,](https://portal.azure.com/)selecione **Monitor**.

1. Na secção **Definições,** selecione **Grupos de Ação**.

1. Encontre e selecione o grupo de ação que pretende editar.

1. Adicione à lista de **Ações:**

    a. **Tipo de ação:** *Webhook*

    b. **Detalhes:** O **URL de Integração** OpsGenie que guardou anteriormente.

    c. **Nome:** Nome de Webhook, pseudónimo ou identificador.

1. **Selecione Guardar** quando for feito para atualizar o grupo de ação.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testar a sua integração webhook através de um pedido HTTP POST
1. Crie a carga útil de saúde do serviço que pretende enviar. Você pode encontrar um serviço de exemplo webhook payload em [Webhooks para alertas de registo de atividade Azure](../azure-monitor/alerts/activity-log-alerts-webhook.md).

1. Crie um pedido HTTP POST da seguinte forma:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Deve receber uma `200 OK` resposta com a mensagem de estado "bem sucedida".

1. Vá à [OpsGenie](https://www.opsgenie.com/) confirmar que a sua integração foi criada com sucesso.

## <a name="next-steps"></a>Passos seguintes
- Saiba como [configurar notificações webhook para os sistemas de gestão de problemas existentes](service-health-alert-webhook-guide.md).
- Reveja o [esquema de alerta de registo de atividade webhook](../azure-monitor/alerts/activity-log-alerts-webhook.md). 
- Saiba mais sobre [as notificações de saúde do serviço.](./service-notifications.md)
- Saiba mais sobre [grupos de ação.](../azure-monitor/alerts/action-groups.md)
