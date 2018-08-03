---
title: Configurar alertas de estado de funcionamento do serviço do Azure com PagerDuty | Documentos da Microsoft
description: Obtenha notificações personalizadas sobre eventos de estado de funcionamento de serviço à sua instância do PagerDuty.
author: shawntabrizi
manager: scotthit
editor: ''
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: 5f6f3f61b5f7a06ac4056499edfb811780838cdc
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441882"
---
# <a name="configure-service-health-alerts-with-pagerduty"></a>Configurar alertas de estado de funcionamento do serviço com PagerDuty

Este artigo mostra-lhe como configurar notificações de estado de funcionamento do serviço do Azure através do PagerDuty através de um webhook. Usando [PagerDuty](https://www.pagerduty.com/)do tipo de integração personalizado do Microsoft Azure, pode facilmente adicionar alertas de estado de funcionamento do serviço aos seus serviços de PagerDuty novos ou existentes.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Criar um serviço de integração do Estado de funcionamento URL no PagerDuty
1.  Certifique-se de que se inscreveram cópia de segurança e iniciado sessão na sua [PagerDuty](https://www.pagerduty.com/) conta.

1.  Navegue para o **serviços** secção PagerDuty.

    ![A seção "Serviços" no PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

1.  Selecione **adicionar novo serviço** ou abrir um serviço existente que configurou.

1.  Na **definições de integração**, selecione o seguinte:

    a. **Tipo de integração**: Microsoft Azure

    b. **Nome de integração**: \<nome\>

    ![As "definições de integração" PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

1.  Preencha quaisquer outros campos obrigatórios e selecione **adicionar**.

1.  Abrir esta nova integração e copie e guarde o **URL de integração**.

    ![O "URL de integração" no PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Criar um alerta com PagerDuty no portal do Azure
### <a name="for-a-new-action-group"></a>Para um novo grupo de ação:
1. Siga os passos 1 a 8 na [criar um alerta numa notificação de estado de funcionamento do serviço para um novo grupo de ação com o portal do Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

1. Definir na lista de **ações**:

    a. **Tipo de ação:** *Webhook*

    b. **Detalhes:** o PagerDuty **URL de integração** tinha guardado.

    c. **Nome:** do Webhook nome, o alias ou o identificador.

1. Selecione **guardar** quando terminar de criar o alerta.

### <a name="for-an-existing-action-group"></a>Para um grupo de ação existente:
1. Na [portal do Azure](https://portal.azure.com/), selecione **Monitor**.

1. Na **configurações** secção, selecione **grupos de ação**.

1. Localize e selecione o grupo de ação que pretende editar.

1. Adicionar à lista de **ações**:

    a. **Tipo de ação:** *Webhook*

    b. **Detalhes:** o PagerDuty **URL de integração** tinha guardado.

    c. **Nome:** do Webhook nome, o alias ou o identificador.

1. Selecione **guardar** quando terminar de atualizar o grupo de ação.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Teste a sua integração de webhook através de um pedido POST de HTTP
1. Crie o payload de estado de funcionamento do serviço que pretende enviar. Pode encontrar um payload de webhook do Estado de funcionamento do serviço do exemplo na [alertas de registo de Webhooks para atividades do Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

1. Crie um pedido POST de HTTP da seguinte forma:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Deverá receber um `202 Accepted` com uma mensagem que contém seu "ID de evento."

1. Aceda a [PagerDuty](https://www.pagerduty.com/) para confirmar a sua integração foi configurada com êxito.

## <a name="next-steps"></a>Passos Seguintes
- Saiba como [configurar notificações de webhook para sistemas de gestão existentes do problema](service-health-alert-webhook-guide.md).
- Reveja os [esquema de webhook de alerta de registo de atividades](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Saiba mais sobre [notificações de estado de funcionamento de serviço](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Saiba mais sobre [grupos de ação](../monitoring-and-diagnostics/monitoring-action-groups.md).