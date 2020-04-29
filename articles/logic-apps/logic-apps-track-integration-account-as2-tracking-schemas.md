---
title: Squemas de rastreio AS2 para mensagens B2B
description: Criar esquemas de rastreio para monitorizar mensagens AS2 em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: bccf69362279afd9e8148b20b61ff3ea9b472a03
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76906967"
---
# <a name="create-schemas-for-tracking-as2-messages-in-azure-logic-apps"></a>Criar esquemas para rastrear mensagens AS2 em Aplicações Lógicas Azure

Para ajudá-lo a monitorizar o sucesso, erros e propriedades de mensagens para transações negócio-a-negócio (B2B), você pode usar estes esquemas de rastreamento AS2 na sua conta de integração:

* Esquema de rastreio de mensagens AS2
* As2 Message Disposition Notification (MDN) tracking schema

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

| Propriedade | Necessário | Tipo | Descrição |
|----------|----------|------|-------------|
| senderPartnerName | Não | String | Nome do parceiro do remetente de mensagens AS2 |
| receptorPartnerName | Não | String | Nome do parceiro do recetor de mensagens AS2 |
| as2To | Sim | String | Nome do recetor de mensagens AS2 a partir dos cabeçalhos da mensagem AS2 |
| as2From | Sim | String | Nome do remetente de mensagens AS2 a partir dos cabeçalhos da mensagem AS2 |
| acordoNome | Não | String | Nome do acordo AS2 ao qual as mensagens são resolvidas |
| direção | Sim | String | Direção do fluxo de `receive` mensagem, que é ou ou`send` |
| mensagemId | Não | String | ID de mensagem AS2 dos cabeçalhos da mensagem AS2 |
| disposiçãoType | Não | String | Valor do tipo de disposição da disposição da disposição da disposição da mensagem (MDN) |
| fileName | Não | String | Nome de arquivo do cabeçalho da mensagem AS2 |
| isMessageFailed | Sim | Booleano | Se a mensagem AS2 falhou |
| isMessageSigned | Sim | Booleano | Se a mensagem AS2 foi assinada |
| isMessageEncrypted | Sim | Booleano | Se a mensagem AS2 foi encriptada |
| isMessageComprim | Sim | Booleano | Se a mensagem AS2 foi comprimido |
| correlaçãoMessageId | Não | String | ID de mensagem AS2, para correlacionar mensagens com MDNs |
| incomingHeaders | Não | Dicionário de JToken | Detalhes do cabeçalho da mensagem AS2 |
| cabeçalhos cessantes | Não | Dicionário de JToken | Detalhes do cabeçalho da mensagem as2 cessantes |
| isNrrEnabled | Sim | Booleano | Se usar o valor predefinido se o valor não for conhecido |
| isMdnExpected | Sim | Booleano | Se usar o valor predefinido se o valor não for conhecido |
| mdnType | Sim | Enum | Valores `NotConfigured`permitidos: , `Sync`e`Async` |
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

| Propriedade | Necessário | Tipo | Descrição |
|----------|----------|------|-------------|
| senderPartnerName | Não | String | Nome do parceiro do remetente de mensagens AS2 |
| receptorPartnerName | Não | String | Nome do parceiro do recetor de mensagens AS2 |
| as2To | Sim | String | Nome do parceiro que recebe a mensagem AS2 |
| as2From | Sim | String | Nome do parceiro que envia a mensagem AS2 |
| acordoNome | Não | String | Nome do acordo AS2 ao qual as mensagens são resolvidas |
| direção | Sim | String | Direção do fluxo de `receive` mensagem, que é ou ou`send` |
| mensagemId | Não | String | ID de mensagem AS2 |
| originalMessageId | Não | String | ID de mensagem original AS2 |
| disposiçãoType | Não | String | Valor do tipo de disposição MDN |
| isMessageFailed | Sim | Booleano | Se a mensagem AS2 falhou |
| isMessageSigned | Sim | Booleano | Se a mensagem AS2 foi assinada |
| isNrrEnabled | Sim | Booleano | Se usar o valor predefinido se o valor não for conhecido |
| estadoCódigo | Sim | Enum | Valores `Accepted`permitidos: , `Rejected`e`AcceptedWithErrors` |
| micVerificationStatus | Sim | Enum | Valores`NotApplicable`permitidos: , `Succeeded`e`Failed` |
| correlaçãoMessageId | Não | String | Id de correlação, que é o ID para a mensagem original que tem o MDN configurado |
| incomingHeaders | Não | Dicionário de JToken | Detalhes do cabeçalho da mensagem |
| cabeçalhos cessantes | Não | Dicionário de JToken | Detalhes do cabeçalho da mensagem de saída |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B protocolo de rastreio de schemas

Para obter informações sobre os esquemas de rastreio do protocolo B2B, consulte:

* [Esquemas de controlo de X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [B2B rastreio personalizado schemas](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Passos seguintes

* [Monitorizar mensagens B2B com os registos do Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)