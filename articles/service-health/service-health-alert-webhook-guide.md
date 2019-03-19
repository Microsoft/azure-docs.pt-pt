---
title: Configurar notificações de estado de funcionamento para sistemas de gestão existentes do problema através de um webhook | Documentos da Microsoft
description: Obtenha notificações personalizadas sobre eventos de estado de funcionamento do serviço para o seu sistema de gerenciamento existente do problema.
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.workload: Supportability
ms.date: 3/27/2018
ms.openlocfilehash: 69b142cd46c006e562218c949fb450864589a661
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57838089"
---
# <a name="configure-health-notifications-for-existing-problem-management-systems-using-a-webhook"></a>Configurar notificações de estado de funcionamento para sistemas de gestão existentes do problema através de um webhook

Este artigo mostra-lhe como configurar os alertas de estado de funcionamento do serviço para enviar dados através de Webhooks para seu sistema de notificação existente.

Hoje em dia, pode configurar alertas de estado de funcionamento do serviço para que quando um incidente de serviço do Azure ocorrer, seja notificado por mensagem de texto ou e-mail.
No entanto, já terá de sistema de notificação externa existente no local que pretende utilizar.
Este documento mostra-lhe as partes mais importantes do payload do webook, e como pode criar alertas personalizados para ser notificado quando é afetado por problemas de serviço.

Se pretender utilizar uma integração pré-configuradas, consulte como:
* [Configurar alertas com ServiceNow](service-health-alert-webhook-servicenow.md)
* [Configurar alertas com PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Configurar alertas com OpsGenie](service-health-alert-webhook-opsgenie.md)

## <a name="configuring-a-custom-notification-using-the-service-health-webhook-payload"></a>Configurar uma notificação com o payload de webhook de estado de funcionamento do serviço
Se quiser configurar a sua própria integração de webhook personalizado, deve analisar o payload JSON que é enviado durante as notificações de estado de funcionamento do serviço.

Ter um aspeto [aqui para ver um exemplo](../azure-monitor/platform/activity-log-alerts-webhook.md) do que o `ServiceHealth` payload do webook é semelhante.

Pode detectar este é um alerta de estado de funcionamento de serviço observando `context.eventSource == "ServiceHealth"`. A partir daí, as propriedades que são mais relevantes para ingerir são:
 * `data.context.activityLog.status`
 * `data.context.activityLog.level`
 * `data.context.activityLog.subscriptionId`
 * `data.context.activityLog.properties.title`
 * `data.context.activityLog.properties.impactStartTime`
 * `data.context.activityLog.properties.communication`
 * `data.context.activityLog.properties.impactedServices`
 * `data.context.activityLog.properties.trackingId`

## <a name="creating-a-direct-link-to-the-service-health-dashboard-for-an-incident"></a>Criar uma ligação direta para o dashboard de estado de funcionamento do serviço de um incidente
Pode criar uma ligação direta ao seu dashboard de estado de funcionamento do serviço no ambiente de trabalho ou móveis através da geração de um URL especializado. Utilize o `trackingId`, bem como os primeiros e últimos três dígitos de sua `subscriptionId`, para formar:
```
https://app.azure.com/h/<trackingId>/<first and last three digits of subscriptionId>
```

Por exemplo, se sua `subscriptionId` é `bba14129-e895-429b-8809-278e836ecdb3` e a sua `trackingId` é `0DET-URB`, então é o URL de estado de funcionamento do serviço:

```
https://app.azure.com/h/0DET-URB/bbadb3
```

## <a name="using-the-level-to-detect-the-severity-of-the-issue"></a>Utilizar o nível para detetar a gravidade do problema
De gravidade mais baixa de gravidade mais elevada, o `level` propriedade no payload pode ser qualquer um dos `Informational`, `Warning`, `Error`, e `Critical`.

## <a name="parsing-the-impacted-services-to-understand-the-full-scope-of-the-incident"></a>Analisar os serviços afetados para compreender todo o escopo do incidente
Alertas de estado de funcionamento do serviço podem informar sobre problemas em várias regiões e serviços. Para obter os detalhes completos, terá de analisar o valor de `impactedServices`.
É o conteúdo dentro de um [JSON escrito](https://json.org/) string, quando invalidada, contém outro objeto JSON que pode ser analisado regularmente.

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

Torna-se:

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

Isso mostra que existem problemas com "Alertas de métricas de &" no Leste da Austrália e Sudeste da Ásia, bem como problemas com o "Serviço de aplicações" no Sudeste da Austrália.


## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Teste a sua integração de webhook através de um pedido POST de HTTP
1. Crie o payload de estado de funcionamento do serviço que pretende enviar. Pode encontrar um payload de webhook do Estado de funcionamento do serviço do exemplo na [alertas de registo de Webhooks para atividades do Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

2. Crie um pedido POST de HTTP da seguinte forma:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. Deverá receber um `2XX - Successful` resposta.

4. Aceda a [PagerDuty](https://www.pagerduty.com/) para confirmar a sua integração foi configurada com êxito.

## <a name="next-steps"></a>Passos Seguintes
- Reveja os [esquema de webhook de alerta de registo de atividades](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Saiba mais sobre [notificações de estado de funcionamento de serviço](../azure-monitor/platform/service-notifications.md).
- Saiba mais sobre [grupos de ação](../azure-monitor/platform/action-groups.md).