---
title: Envie notificações de saúde de serviço azure via webhooks
description: Envie notificações personalizadas sobre eventos de saúde de serviço para o seu sistema de gestão de problemas existente.
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: 2609a267bd151354f83482ab16c4b9345aa88cc4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062845"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>Use um webhook para configurar notificações de saúde para sistemas de gestão de problemas

Este artigo mostra-lhe como configurar alertas de Saúde de Serviço Azure para enviar dados através de webhooks para o seu sistema de notificação existente.

Pode configurar alertas de Saúde de Serviço para notificá-lo por mensagem de texto ou e-mail quando um incidente de serviço Azure o afetar.

Mas pode já ter um sistema de notificação externo existente que prefere utilizar. Este artigo identifica as partes mais importantes da carga útil do webhook. E descreve como criar alertas personalizados para notificá-lo quando ocorrerem problemas de serviço relevantes.

Se quiser utilizar uma integração pré-configurada, consulte:
* [Configure alertas com ServiceNow](service-health-alert-webhook-servicenow.md)
* [Configure alertas com PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Configure alertas com OpsGenie](service-health-alert-webhook-opsgenie.md)

**Veja um vídeo introdutório:**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>Configure uma notificação personalizada utilizando a carga útil do webhook service Health
Para configurar a sua própria integração personalizada no webhook, precisa de analisar a carga útil JSON que é enviada através da notificação de Saúde de Serviço.

Consulte uma carga útil de webhook [exemplo.](../azure-monitor/platform/activity-log-alerts-webhook.md) `ServiceHealth`

Pode confirmar que é um alerta de `context.eventSource == "ServiceHealth"`saúde de serviço olhando para . As seguintes propriedades são as mais relevantes:
- **data.context.activityLog.status**
- **data.context.activityLog.level**
- **data.context.activityLog.subscriçãoId**
- **data.context.activityLog.properties.title.title**
- **data.context.activityLog.properties.impactStartTime**
- **data.context.activityLog.properties.communication**
- **data.context.activityLog.properties.impactedServices**
- **data.context.activityLog.properties.trackingId**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>Crie um link para o dashboard service Health para um incidente
Pode criar uma ligação direta ao seu dashboard Service Health num ambiente de trabalho ou dispositivo móvel gerando um URL especializado. Utilize o *trackingId* e os três primeiros e últimos três dígitos da sua *subscriçãoId* neste formato:

https<i></i>://app.azure.com/h/*&lt;&gt;trackingId*/*&lt;primeiro três e últimos três dígitos de&gt; subscriçãoId*

Por exemplo, se o seu *id de subscrição* for bba14129-e895-429b-8809-278e836ecdb3 e o seu *trackingId* for 0DET-URB, o seu URL de Saúde de Serviço é:

https://app.azure.com/h/0DET-URB/bbadb3<i></i>

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>Use o nível para detetar a gravidade da questão
Da menor para a mais alta severidade, a propriedade **de nível** na carga útil pode ser *Informacional,* *Aviso,* *Erro*ou *Crítico.*

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>Analisar os serviços impactados para determinar o âmbito do incidente
Os alertas de Saúde de Serviço podem informá-lo sobre questões em várias regiões e serviços. Para obter detalhes completos, precisa analisar `impactedServices`o valor de .

O conteúdo que está dentro é uma cadeia [JSON](https://json.org/) escapada que, quando não escapada, contém outro objeto JSON que pode ser analisado regularmente. Por exemplo:

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

torna-se:

```json
[
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia East"
         },
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"Alerts & Metrics"
   },
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"App Service"
   }
]
```

Este exemplo mostra problemas para:
- "Alertas & Métricas" na Austrália Leste e Austrália Sudeste.
- "App Service" na Austrália Sudeste.

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>Teste a sua integração webhook através de um pedido HTTP POST

Siga estes passos.

1. Crie a carga de saúde de serviço que pretende enviar. Consulte uma carga útil de webhook de serviço de exemplo nos [Webhooks para alertas](../azure-monitor/platform/activity-log-alerts-webhook.md)de registo de atividade do Azure .

1. Crie um pedido HTTP POST da seguinte forma:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   Deve receber uma resposta "2XX - Bem-sucedida".

1. Vá ao [PagerDuty](https://www.pagerduty.com/) para confirmar que a sua integração foi criada com sucesso.

## <a name="next-steps"></a>Passos seguintes
- Reveja o esquema de alerta de registo de [atividade](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Conheça as notificações de saúde de [serviço.](../azure-monitor/platform/service-notifications.md)
- Saiba mais sobre [grupos de ação.](../azure-monitor/platform/action-groups.md)