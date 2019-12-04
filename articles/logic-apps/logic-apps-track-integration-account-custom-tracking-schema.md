---
title: Esquemas de acompanhamento personalizados para mensagens B2B
description: Criar esquemas de controle personalizados que monitorem mensagens B2B em contas de integração para aplicativos lógicos do Azure com Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 7d7c5ef9e9a86c8b061a56fe41c0c8bbfc5ddbb3
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792790"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-apps"></a>Criar esquemas de controle personalizados que monitoram fluxos de trabalho de ponta a ponta em aplicativos lógicos do Azure

Há um acompanhamento interno que você pode habilitar para diferentes partes do fluxo de trabalho entre empresas, como o acompanhamento de mensagens AS2 ou X12. Ao criar fluxos de trabalho que incluem um aplicativo lógico, BizTalk Server, SQL Server ou qualquer outra camada, você pode habilitar o acompanhamento personalizado que registra eventos desde o início até o fim do fluxo de trabalho. 

Este artigo fornece código personalizado que você pode usar nas camadas fora do seu aplicativo lógico. 

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
         "repeatItemIndex": "",
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
         "record": {                
         }
      }
   ]
}
```

| Propriedade | Obrigatório | Tipo | Descrição |
| --- | --- | --- | --- |
| sourceType | Sim |   | Tipo de fonte de execução. Os valores permitidos são **Microsoft. Logic/workflows** e **Custom**. |
| source | Sim |   | Se o tipo de origem for **Microsoft. Logic/workflows**, as informações de origem precisarão seguir este esquema. Se o tipo de origem for **personalizado**, o esquema será um JToken. |
| systemId | Sim | String | ID do sistema do aplicativo lógico. |
| RunId | Sim | String | ID de execução do aplicativo lógico. |
| operationName | Sim | String | Nome da operação (por exemplo, ação ou gatilho). |
| repeatItemScopeName | Sim | String | Repita o nome do item se a ação estiver dentro de um loop de `until` de /de `foreach`. |
| repeatItemIndex | Sim | Número inteiro | Se a ação está dentro de um loop de `until` de /de `foreach`. Indica o índice de item repetido. |
| trackingId | Não | String | ID de rastreamento, para correlacionar as mensagens. |
| correlationId | Não | String | ID de correlação, para correlacionar as mensagens. |
| clientRequestId | Não | String | O cliente pode preenchê-lo para correlacionar mensagens. |
| eventLevel | Sim |   | Nível do evento. |
| eventTime | Sim |   | Hora do evento, no formato UTC AAAA-MM-DDTHH: MM: SS. 00000Z. |
| RecordType | Sim |   | Tipo do registro de faixa. O valor permitido é **personalizado**. |
| gravável | Sim |   | Tipo de registro personalizado. O formato permitido é JToken. |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Esquemas de acompanhamento do protocolo B2B

Para obter informações sobre esquemas de controle de protocolo B2B, consulte:

* [Esquemas de controlo de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Esquemas de controlo de X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [monitoramento de mensagens B2B](logic-apps-monitor-b2b-message.md)
* Saiba mais sobre como [controlar mensagens B2B em logs de Azure monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)
