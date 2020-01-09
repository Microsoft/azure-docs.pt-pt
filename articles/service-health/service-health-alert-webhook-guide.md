---
title: Enviar notificações de integridade do serviço do Azure com um webhook
description: Envie notificações personalizadas sobre eventos de integridade do serviço para o sistema de gerenciamento de problemas existente.
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: 95926185057d9fc1177b974fe76b2da18ebfc124
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551680"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>Usar um webhook para configurar notificações de integridade para sistemas de gerenciamento de problemas

Este artigo mostra como configurar alertas de integridade do serviço do Azure para enviar dados por meio de WebHooks para o sistema de notificação existente.

Você pode configurar alertas de integridade do serviço para notificá-lo por mensagem de texto ou email quando um incidente de serviço do Azure afetar você.

Mas talvez você já tenha um sistema de notificação externo existente no local que preferir usar. Este artigo identifica as partes mais importantes do conteúdo de webhook. E descreve como criar alertas personalizados para notificá-lo quando ocorrerem problemas de serviço relevantes.

Se você quiser usar uma integração pré-configurada, consulte:
* [Configurar alertas com o ServiceNow](service-health-alert-webhook-servicenow.md)
* [Configurar alertas com o PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Configurar alertas com o OpsGenie](service-health-alert-webhook-opsgenie.md)

**Assista a um vídeo introdutório:**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>Configurar uma notificação personalizada usando o conteúdo do webhook de integridade do serviço
Para configurar sua própria integração de webhook personalizada, você precisa analisar a carga JSON que é enviada por meio da notificação de integridade do serviço.

Consulte [um exemplo](../azure-monitor/platform/activity-log-alerts-webhook.md) `ServiceHealth` carga de webhook.

Você pode confirmar se é um alerta de integridade do serviço examinando `context.eventSource == "ServiceHealth"`. As propriedades a seguir são as mais relevantes:
- **Data. Context. activityLog. status**
- **Data. Context. activityLog. Level**
- **Data. Context. activityLog. SubscriptionId**
- **Data. Context. activityLog. Properties. title**
- **Data. Context. activityLog. Properties. impactStartTime**
- **Data. Context. activityLog. Properties. Communication**
- **Data. Context. activityLog. Properties. impactedServices**
- **Data. Context. activityLog. Properties. TrackingID**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>Criar um link para o painel de integridade do serviço para um incidente
Você pode criar um link direto para o painel de integridade do serviço em um desktop ou dispositivo móvel gerando uma URL especializada. Use *TrackingID* e os três primeiros e os três últimos dígitos de sua *SubscriptionId* neste formato:

https<i></i>://app.Azure.com/h/ *&lt;trackingid&gt;* / *&lt;primeiros três e últimos três dígitos de SubscriptionId&gt;*

Por exemplo, se sua *SubscriptionId* for bba14129-e895-429B-8809-278e836ecdb3 e o *TrackingID* for 0DET-URB, a URL de integridade do serviço será:

https<i></i>://app.Azure.com/h/0DET-URB/bbadb3

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>Use o nível para detectar a gravidade do problema
Da severidade mais baixa para a mais alta, a propriedade **Level** na carga pode ser *informativa*, *aviso*, *erro*ou *crítico*.

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>Analisar os serviços afetados para determinar o escopo do incidente
Os alertas de integridade do serviço podem informá-lo sobre problemas em várias regiões e serviços. Para obter detalhes completos, você precisa analisar o valor de `impactedServices`.

O conteúdo que está dentro é uma cadeia de caracteres [JSON](https://json.org/) de escape que, quando sem escape, contém outro objeto JSON que pode ser analisado regularmente. Por exemplo:

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

se torna:

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
- "Alertas & métricas" no leste da Austrália e no sudeste da Austrália.
- "Serviço de aplicativo" no sudeste da Austrália.

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>Testar sua integração de webhook por meio de uma solicitação HTTP POST

Siga estes passos.

1. Crie a carga de integridade do serviço que você deseja enviar. Consulte um exemplo de carga de webhook de integridade do serviço em [WebHooks para alertas do log de atividades do Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Crie uma solicitação HTTP POST da seguinte maneira:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   Você deve receber uma resposta "2XX-successful".

1. Vá para [PagerDuty](https://www.pagerduty.com/) para confirmar que sua integração foi configurada com êxito.

## <a name="next-steps"></a>Passos seguintes
- Examine o [esquema de webhook de alerta do log de atividades](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Saiba mais sobre as [notificações de integridade do serviço](../azure-monitor/platform/service-notifications.md).
- Saiba mais sobre [grupos de ações](../azure-monitor/platform/action-groups.md).