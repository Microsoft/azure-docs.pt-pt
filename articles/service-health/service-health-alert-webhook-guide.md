---
title: Enviar notificações de Saúde do Serviço Azure através de webhooks
description: Envie notificações personalizadas sobre eventos de saúde de serviço para o seu sistema de gestão de problemas existente.
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: a70c4fcd01a1a95c598d980004ee60292a6cf24b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594616"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>Utilize um webhook para configurar notificações de saúde para sistemas de gestão de problemas

Este artigo mostra-lhe como configurar alertas de Saúde do Serviço Azure para enviar dados através de webhooks para o seu sistema de notificação existente.

Pode configurar alertas de saúde do serviço para notificá-lo por mensagem de texto ou e-mail quando um incidente de serviço Azure o afetar.

Mas pode já ter um sistema de notificação externo existente que prefere utilizar. Este artigo identifica as partes mais importantes da carga útil do webhook. E descreve como criar alertas personalizados para o notificar quando ocorrem problemas de serviço relevantes.

Se quiser utilizar uma integração pré-configurada, consulte:
* [Configure alertas com o ServiceNow](service-health-alert-webhook-servicenow.md)
* [Configure alertas com PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Configure alertas com OpsGenie](service-health-alert-webhook-opsgenie.md)

**Assista a um vídeo introdutório:**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>Configure uma notificação personalizada utilizando a carga útil do Webhook service Health
Para configurar a sua própria integração personalizada do webhook, você precisa analisar a carga útil JSON que é enviada através da notificação de Saúde do Serviço.

Veja [um exemplo](../azure-monitor/alerts/activity-log-alerts-webhook.md) de carga útil `ServiceHealth` webhook.

Pode confirmar que é um alerta de saúde de serviço olhando para `context.eventSource == "ServiceHealth"` . As seguintes propriedades são as mais relevantes:
- **data.context.activityLog.status**
- **data.context.activityLog.level**
- **data.context.activityLog.subscriptionId**
- **data.context.activityLog.properties.title**
- **data.context.activityLog.properties.impactStartTime**
- **data.context.activityLog.properties.communication**
- **data.context.activityLog.properties.impactedServices**
- **data.context.activityLog.properties.trackingId**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>Criar uma ligação com o painel de saúde do serviço para um incidente
Pode criar uma ligação direta ao seu painel de saúde de serviço num ambiente de trabalho ou dispositivo móvel, gerando um URL especializado. Utilize o *trackingId* e os primeiros três e últimos três dígitos da sua *subscriçãoId* neste formato:

https <i></i> ://app.azure.com/h/*&lt; trackingId &gt;* / *&lt; primeiros três e últimos &gt; três dígitos de subscriçãoId*

Por exemplo, se a sua *subscriçãoId* for bba14129-e895-429b-8809-278e836ecdb3 e o seu *trackingId* é 0DET-URB, o seu URL de Saúde de Serviço é:

https <i></i> ://app.azure.com/h/0DET-URB/bbadb3

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>Use o nível para detetar a gravidade da questão
Da severidade mais baixa à máxima, a propriedade **de nível** na carga útil pode ser *Informacional,* *Aviso,* *Erro,* Ou *Crítico*.

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>Parse os serviços impactados para determinar o âmbito do incidente
Os alertas de saúde do serviço podem informá-lo sobre problemas em várias regiões e serviços. Para obter detalhes completos, você precisa analisar o valor de `impactedServices` .

O conteúdo que está dentro é uma cadeia [JSON](https://json.org/) em fuga que, quando não escapada, contém outro objeto JSON que pode ser analisado regularmente. Por exemplo:

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

Siga estes passos:

1. Crie a carga útil de saúde de serviço que pretende enviar. Consulte um serviço de exemplo webhook payload na [Webhooks para alertas de registo de atividades Azure](../azure-monitor/alerts/activity-log-alerts-webhook.md).

1. Crie um pedido HTTP POST da seguinte forma:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   Deve receber uma resposta "2XX - Sucesso".

1. Vá ao [PagerDuty](https://www.pagerduty.com/) confirmar que a sua integração foi configurada com sucesso.

## <a name="next-steps"></a>Passos seguintes
- Reveja o [esquema de alerta de registo de atividade webhook](../azure-monitor/alerts/activity-log-alerts-webhook.md). 
- Saiba mais sobre [as notificações de saúde do serviço.](./service-notifications.md)
- Saiba mais sobre [grupos de ação.](../azure-monitor/alerts/action-groups.md)
