---
title: Envie alertas de saúde de serviço Azure com OpsGenie usando webhooks
description: Obtenha notificações personalizadas sobre eventos de saúde de serviço para a sua instância OpsGenie.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: def12d5e7b1b93b8370cd7be61538fca53531ae1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77654142"
---
# <a name="send-azure-service-health-alerts-with-opsgenie-using-webhooks"></a>Envie alertas de saúde de serviço Azure com OpsGenie usando webhooks

Este artigo mostra-lhe como configurar alertas de saúde de serviço Azure com opsGenie usando um webhook. Ao utilizar a Integração de Saúde do Serviço Azure da [OpsGenie,](https://www.opsgenie.com/)pode retransmitir os alertas de Saúde do Serviço Azure para a OpsGenie. A OpsGenie pode determinar as pessoas certas para notificar com base em horários de chamada, usando e-mail, mensagens de texto (SMS), chamadas telefónicas, notificações push iOS & Android e alertas de escalada até que o alerta seja reconhecido ou fechado.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Criação de um URL de integração de saúde de serviço em OpsGenie
1.  Certifique-se de que se inscreveu e está inscrito na sua conta [OpsGenie.](https://www.opsgenie.com/)

1.  Navegue na secção **integrações** em OpsGenie.

    ![A secção "Integrações" em OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  Selecione o botão de integração da **Saúde do Serviço Azure.**

    ![O "botão de saúde de serviço Azure" em OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  **Nomeie** o seu alerta e especifique o campo **atribuído à Equipa.**

1.  Preencha os outros campos como **destinatários,** **habilitados**e **suprimir notificações.**

1.  Copie e guarde o URL `apiKey` de **Integração,** que já deve conter o seu anexo até ao fim.

    ![O "URL de Integração" em OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

1.  Selecione **Salvar a Integração**

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Criar um alerta usando opsGenie no portal Azure
### <a name="for-a-new-action-group"></a>Para um novo grupo de ação:
1. Siga os passos 1 a 8 em Criar um alerta sobre uma notificação de [saúde de serviço para um novo grupo de ação utilizando o portal Azure](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

1. Definir na lista de **Ações:**

    a. **Tipo de ação:** *Webhook*

    b. **Detalhes:** O URL de **Integração** OpsGenie que guardou anteriormente.

    c. **Nome:** O nome do Webhook, pseudónimo, ou identificador.

1. Selecione **Guardar** quando estiver feito para criar o alerta.

### <a name="for-an-existing-action-group"></a>Para um grupo de ação existente:
1. No [portal Azure,](https://portal.azure.com/)selecione **Monitor**.

1. Na secção **Definições,** selecione **grupos de ação**.

1. Encontre e selecione o grupo de ação que pretende editar.

1. Adicione à lista de **Ações:**

    a. **Tipo de ação:** *Webhook*

    b. **Detalhes:** O URL de **Integração** OpsGenie que guardou anteriormente.

    c. **Nome:** O nome do Webhook, pseudónimo, ou identificador.

1. Selecione **Guardar** quando for feito para atualizar o grupo de ação.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testar a sua integração webhook através de um pedido HTTP POST
1. Crie a carga de saúde de serviço que pretende enviar. Pode encontrar uma carga útil de webhook de serviço de exemplo em Webhooks para alertas de registo de [atividade sinuosos do Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Crie um pedido HTTP POST da seguinte forma:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Deve receber `200 OK` uma resposta com a mensagem de "sucesso".

1. Vá à [OpsGenie](https://www.opsgenie.com/) para confirmar que a sua integração foi criada com sucesso.

## <a name="next-steps"></a>Passos seguintes
- Saiba como [configurar notificações de webhook para sistemas de gestão de problemas existentes.](service-health-alert-webhook-guide.md)
- Reveja o esquema de alerta de registo de [atividade](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Conheça as notificações de saúde de [serviço.](../azure-monitor/platform/service-notifications.md)
- Saiba mais sobre [grupos de ação.](../azure-monitor/platform/action-groups.md)