---
title: Enviar alertas de integridade do serviço do Azure com OpsGenie usando WebHooks
description: Obtenha notificações personalizadas sobre eventos de integridade do serviço para sua instância do OpsGenie.
ms.service: service-health
ms.topic: article
ms.date: 06/10/2019
ms.openlocfilehash: d1f0ddb506b915697335ec71eab341a7cdb73dd7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465227"
---
# <a name="send-azure-service-health-alerts-with-opsgenie-using-webhooks"></a>Enviar alertas de integridade do serviço do Azure com OpsGenie usando WebHooks

Este artigo mostra como configurar alertas de integridade do serviço do Azure com o OpsGenie usando um webhook. Usando a integração de integridade do serviço do Azure do [OpsGenie](https://www.opsgenie.com/), você pode encaminhar alertas de integridade do serviço do Azure para o OpsGenie. O OpsGenie pode determinar as pessoas certas para notificar com base em agendas de chamada, usando email, mensagens de texto (SMS), chamadas telefônicas, iOS & notificações por push do Android e encaminhando alertas até que o alerta seja confirmado ou fechado.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Criando uma URL de integração de integridade do serviço no OpsGenie
1.  Verifique se você se inscreveu e entrou em sua conta do [OpsGenie](https://www.opsgenie.com/) .

1.  Navegue até a seção **integrações** em OpsGenie.

    ![A seção "integrações" em OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  Selecione o botão integração de **integridade do serviço do Azure** .

    ![O "botão de integridade do serviço do Azure" em OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  **Nomeie** o alerta e especifique o campo **atribuído ao grupo** .

1.  Preencha os outros campos, como **destinatários**, **habilitados**e **suprimir notificações**.

1.  Copie e salve a **URL de integração**, que já deve conter seu `apiKey` acrescentado ao final.

    ![A "URL de integração" no OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

1.  Selecione **salvar integração**

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Criar um alerta usando o OpsGenie no portal do Azure
### <a name="for-a-new-action-group"></a>Para um novo grupo de ação:
1. Siga as etapas 1 a 8 em [criar um alerta em uma notificação de integridade do serviço para um novo grupo de ação usando o portal do Azure](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

1. Defina na lista de **ações**:

    a. **Tipo de ação:** *webhook*

    b. **Detalhes:** A **URL de integração** do OpsGenie que você salvou anteriormente.

    c. **Nome:** Nome, alias ou identificador de webhook.

1. Selecione **salvar** quando terminar para criar o alerta.

### <a name="for-an-existing-action-group"></a>Para um grupo de ação existente:
1. Na [portal do Azure](https://portal.azure.com/), selecione **Monitor**.

1. Na seção **configurações** , selecione **grupos de ações**.

1. Localize e selecione o grupo de ações que deseja editar.

1. Adicione à lista de **ações**:

    a. **Tipo de ação:** *webhook*

    b. **Detalhes:** A **URL de integração** do OpsGenie que você salvou anteriormente.

    c. **Nome:** Nome, alias ou identificador de webhook.

1. Selecione **salvar** quando terminar de atualizar o grupo de ações.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testando a integração de webhook por meio de uma solicitação HTTP POST
1. Crie a carga de integridade do serviço que você deseja enviar. Você pode encontrar um conteúdo de webhook de integridade do serviço de exemplo em [WebHooks para alertas do log de atividades do Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Crie uma solicitação HTTP POST da seguinte maneira:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Você deve receber uma resposta de `200 OK` com a mensagem de status "êxito".

1. Vá para [OpsGenie](https://www.opsgenie.com/) para confirmar que sua integração foi configurada com êxito.

## <a name="next-steps"></a>Passos seguintes
- Saiba como [configurar notificações de webhook para sistemas de gerenciamento de problemas existentes](service-health-alert-webhook-guide.md).
- Examine o [esquema de webhook de alerta do log de atividades](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Saiba mais sobre as [notificações de integridade do serviço](../azure-monitor/platform/service-notifications.md).
- Saiba mais sobre [grupos de ações](../azure-monitor/platform/action-groups.md).