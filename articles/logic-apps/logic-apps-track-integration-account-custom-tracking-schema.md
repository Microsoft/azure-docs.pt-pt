---
title: Esquemas de rastreio personalizados para mensagens B2B
description: Crie esquemas de rastreio personalizados para monitorizar mensagens B2B em Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "76903062"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>Crie esquemas de rastreio personalizados que monitorizem fluxos de trabalho de ponta a ponta na Lógica A do Azure

O Azure Logic Apps tem um rastreio incorporado que pode ativar para partes do seu fluxo de trabalho. No entanto, pode configurar o rastreio personalizado que regista eventos do início ao fim dos fluxos de trabalho, por exemplo, fluxos de trabalho que incluem uma aplicação lógica, BizTalk Server, SQL Server ou qualquer outra camada. Este artigo fornece código personalizado que pode usar nas camadas fora da sua aplicação lógica.

## <a name="custom-tracking-schema"></a>Personalizar esquema de controlo

```json
{
   "sourceType": "",
   "source": {
      "workflow": {
         "systemId": ""
      },
      "runInstance": {
         "runId": ""
      },
      "operation": {
         "operationName": "",
         "repeatItemScopeName": "",
         "repeatItemIndex": ,
         "trackingId": "",
         "correlationId": "",
         "clientRequestId": ""
      }
   },
   "events": [
      {
         "eventLevel": "",
         "eventTime": "",
         "recordType": "",
         "record": {}
      }
   ]
}
```

| Propriedade | Necessário | Tipo | Description |
|----------|----------|------|-------------|
| origemType | Sim | String | Tipo de fonte de execução com estes valores `Microsoft.Logic/workflows` permitidos: `custom` |
| source | Yes | String ou JToken | Se o tipo de origem `Microsoft.Logic/workflows` for, a informação de origem tem de seguir este esquema. Se o tipo de origem `custom` for, o esquema é um JToken. |
| systemId | Sim | String | ID do sistema de aplicativos lógico |
| runId | Sim | String | ID de execução de aplicativo lógico |
| operationName | Sim | String | Nome da operação, por exemplo, ação ou gatilho |
| nome do repetóstaIteoScope | Sim | String | Repita o nome do item se a ação estiver dentro de um `foreach` `until` ou loop |
| repeatItemIndex | Yes | Número inteiro | Indica que a ação está dentro de um `foreach` ou loop e é o número de índice de item `until` repetido. |
| trackingId | Não | String | ID de rastreio para correlacionar as mensagens |
| correlationId | Não | String | ID de correlação para correlacionar as mensagens |
| clienteRequestId | Não | String | O cliente pode povoar esta propriedade para correlacionar mensagens |
| eventoLevel | Sim | String | Nível do evento |
| eventTime | Yes | DateTime | Hora do evento em formato UTC: *YYYY-MM-DDTHH:MM:SS.0000Z* |
| gravarType | Sim | String | Tipo de registo de faixa com este valor permitido apenas: `custom` |
| gravar | Yes | JToken | Tipo de gravação personalizado apenas com formato JToken |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Esquemas de rastreio do protocolo B2B

Para obter informações sobre os esquemas de rastreio do protocolo B2B, consulte:

* [Esquemas de controlo de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de controlo de X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a monitorização de mensagens B2B com registos do Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)