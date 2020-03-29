---
title: Esquemas de rastreio personalizados para mensagens B2B
description: Criar esquemas de rastreio personalizados para monitorizar mensagens B2B em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76903062"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>Crie esquemas de rastreio personalizados que monitorizem fluxos de trabalho de ponta a ponta na Lógica Azure A

As Aplicações Lógicas Azure têm um rastreio incorporado que pode permitir partes do seu fluxo de trabalho. No entanto, pode configurar rastreios personalizados que registam eventos desde o início até ao fim dos fluxos de trabalho, por exemplo, fluxos de trabalho que incluem uma aplicação lógica, BizTalk Server, SQL Server ou qualquer outra camada. Este artigo fornece código personalizado que pode usar nas camadas fora da sua aplicação lógica.

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

| Propriedade | Necessário | Tipo | Descrição |
|----------|----------|------|-------------|
| fonteTipo | Sim | Cadeia | Tipo de fonte de execução `Microsoft.Logic/workflows`com estes valores permitidos:`custom` |
| source | Sim | Corda ou JToken | Se o tipo `Microsoft.Logic/workflows`de origem for, a informação de origem deve seguir este esquema. Se o tipo `custom`de origem for, o esquema é um JToken. |
| systemId | Sim | Cadeia | ID do sistema de aplicações lógicas |
| runId | Sim | Cadeia | Id de execução de aplicativológico |
| operationName | Sim | Cadeia | Nome da operação, por exemplo, ação ou gatilho |
| repeatItemScopeName | Sim | Cadeia | Repita o nome do item `foreach` `until` se a ação estiver dentro de um ou loop |
| repeatItemIndex | Sim | Número inteiro | Indica que a ação `foreach` `until` está dentro de um ou loop e é o número de índice de item repetido. |
| trackingId | Não | Cadeia | Rastreio de ID para correlacionar as mensagens |
| correlationId | Não | Cadeia | Id de correlação para correlacionar as mensagens |
| clientRequestId | Não | Cadeia | O cliente pode povoar esta propriedade para correlacionar mensagens |
| eventoN | Sim | Cadeia | Nível do evento |
| eventTime | Sim | DateTime | Hora do evento em formato UTC: *YYYY-MM-DDTHH:MM:SS.00000Z* |
| recordType | Sim | Cadeia | Tipo de registo com este valor permitido apenas:`custom` |
| gravar | Sim | Rio JToken | Tipo de gravação personalizado apenas com formato JToken |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B protocolo de rastreio de schemas

Para obter informações sobre os esquemas de rastreio do protocolo B2B, consulte:

* [Esquemas de controlo de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de controlo de X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre monitorização de [mensagens B2B com registos do Monitor Azure](../logic-apps/monitor-b2b-messages-log-analytics.md)