---
title: Esquemas de rastreio AS2 para mensagens B2B
description: Criar esquemas de rastreio para monitorizar mensagens AS2 em Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: bccf69362279afd9e8148b20b61ff3ea9b472a03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "76906967"
---
# <a name="create-schemas-for-tracking-as2-messages-in-azure-logic-apps"></a>Criar esquemas para rastrear mensagens AS2 em Azure Logic Apps

Para ajudá-lo a monitorizar propriedades de sucesso, erros e mensagens para transações business-to-business (B2B), pode utilizar estes esquemas de rastreio AS2 na sua conta de integração:

* Esquema de rastreio de mensagens AS2
* Sistema de rastreio de notificação de disposição de mensagens AS2 (MDN)

## <a name="as2-message-tracking-schema"></a>Esquema de rastreio de mensagens AS2

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "dispositionType": "",
      "fileName": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isMessageEncrypted": "",
      "isMessageCompressed": "",
      "correlationMessageId": "",
      "incomingHeaders": {},
      "outgoingHeaders": {},
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Propriedade | Necessário | Tipo | Description |
|----------|----------|------|-------------|
| nome senderPartner | Não | String | Nome do parceiro do remetente de mensagens AS2 |
| receptorPartnerName | Não | String | Nome do parceiro do recetor de mensagens AS2 |
| as2To | Sim | String | O nome do recetor de mensagens AS2 dos cabeçalhos da mensagem AS2 |
| as2From | Sim | String | Nome do remetente de mensagens AS2 dos cabeçalhos da mensagem AS2 |
| agreementName | Não | String | Nome do acordo AS2 para o qual as mensagens são resolvidas |
| direção | Sim | String | Direção do fluxo de mensagem, que é `receive` ou `send` |
| messageId | Não | String | ID de mensagem AS2 dos cabeçalhos da mensagem AS2 |
| disposiçãoType | Não | String | Valor do tipo de disposição de disposição de mensagem (MDN) |
| fileName | Não | String | Nome do ficheiro do cabeçalho da mensagem AS2 |
| isMessageFailed | Yes | Booleano | Se a mensagem AS2 falhou |
| isMessageSigned | Yes | Booleano | Se a mensagem AS2 foi assinada |
| isMessageEncrypted | Yes | Booleano | Se a mensagem AS2 foi encriptada |
| isMessageComprido | Yes | Booleano | Se a mensagem AS2 foi comprimida |
| correlationMessageId | Não | String | ID de mensagem AS2, para correlacionar mensagens com MDNs |
| entradasHeaders | No | Dicionário de JToken | Detalhes do cabeçalho de mensagem AS2 |
| outgoingHeaders | No | Dicionário de JToken | Detalhes do cabeçalho de mensagens AS2 de saída |
| isNrrEnabled | Yes | Booleano | Se usar o valor padrão se o valor não for conhecido |
| isMdnExpected | Yes | Booleano | Se usar o valor padrão se o valor não for conhecido |
| mdnType | Yes | Enumeração | Valores permitidos: `NotConfigured` `Sync` , e `Async` |
|||||

## <a name="as2-mdn-tracking-schema"></a>Esquema de rastreio AS2 MDN

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "originalMessageId": "",
      "dispositionType": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isNrrEnabled": "",
      "statusCode": "",
      "micVerificationStatus": "",
      "correlationMessageId": "",
      "incomingHeaders": {
      },
      "outgoingHeaders": {
      }
   }
}
```

| Propriedade | Necessário | Tipo | Description |
|----------|----------|------|-------------|
| nome senderPartner | Não | String | Nome do parceiro do remetente de mensagens AS2 |
| receptorPartnerName | Não | String | Nome do parceiro do recetor de mensagens AS2 |
| as2To | Sim | String | Nome do parceiro que recebe a mensagem AS2 |
| as2From | Sim | String | Nome do parceiro que envia a mensagem AS2 |
| agreementName | Não | String | Nome do acordo AS2 para o qual as mensagens são resolvidas |
| direção | Sim | String | Direção do fluxo de mensagem, que é `receive` ou `send` |
| messageId | Não | String | ID de mensagem AS2 |
| originalMessageId | Não | String | ID de mensagem original AS2 |
| disposiçãoType | Não | String | Valor do tipo de disposição MDN |
| isMessageFailed | Yes | Booleano | Se a mensagem AS2 falhou |
| isMessageSigned | Yes | Booleano | Se a mensagem AS2 foi assinada |
| isNrrEnabled | Yes | Booleano | Se usar o valor padrão se o valor não for conhecido |
| statusCode | Yes | Enumeração | Valores permitidos: `Accepted` `Rejected` , e `AcceptedWithErrors` |
| micVerificationStatus | Yes | Enumeração | Valores permitidos: `NotApplicable` `Succeeded` , e `Failed` |
| correlationMessageId | Não | String | ID de correlação, que é o ID para a mensagem original que tem o MDN configurado |
| entradasHeaders | No | Dicionário de JToken | Detalhes do cabeçalho da mensagem de entrada |
| outgoingHeaders | No | Dicionário de JToken | Detalhes do cabeçalho de mensagem de saída |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Esquemas de rastreio do protocolo B2B

Para obter informações sobre os esquemas de rastreio do protocolo B2B, consulte:

* [Esquemas de controlo de X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquemas de rastreio personalizados B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Passos seguintes

* [Monitorizar mensagens B2B com os registos do Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)