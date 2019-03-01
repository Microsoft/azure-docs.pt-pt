---
title: Esquemas de controlo de AS2 para mensagens B2B - Azure Logic Apps | Documentos da Microsoft
description: Criar esquemas de controlo de AS2 monitorizar mensagens B2B no contas de integração para o Azure Logic Apps com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.date: 01/27/2017
ms.openlocfilehash: 180d90450497b38f107f3601944385a003f50282
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193522"
---
# <a name="create-schemas-for-tracking-as2-messages-and-mdns-in-integration-accounts-for-azure-logic-apps"></a>Crie esquemas de controlo nas mensagens AS2 e MDNs nas contas de integração para o Azure Logic Apps

Para ajudar a monitorizar o êxito, erros e as propriedades da mensagem para transações empresa-empresa (B2B), pode usar esses esquemas de controlo de AS2 na sua conta de integração:

* Esquema de controlo de mensagem AS2
* Esquema de controlo AS2 MDN

## <a name="as2-message-tracking-schema"></a>Esquema de controlo de mensagem AS2

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
      "incomingHeaders": {
       },
      "outgoingHeaders": {
       },
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| senderPartnerName | String | Nome do parceiro do remetente da mensagem AS2. (Opcional) |
| receiverPartnerName | String | Nome do parceiro do destinatário de mensagem AS2. (Opcional) |
| as2To | String | Nome do destinatário de mensagem AS2, dos cabeçalhos da mensagem AS2. (Obrigatório) |
| as2From | String | Nome do remetente da mensagem AS2, dos cabeçalhos da mensagem AS2. (Obrigatório) |
| agreementName | String | Nome do contrato de AS2 ao qual as mensagens são resolvidas. (Opcional) |
| direção | String | Direção do fluxo da mensagem, receber ou enviar. (Obrigatório) |
| messageId | String | ID da mensagem AS2, dos cabeçalhos da mensagem AS2 (opcional) |
| dispositionType |String | Valor de tipo de disposição de notificação de disposição (MDN) de mensagem. (Opcional) |
| fileName | String | Nome de ficheiro, do cabeçalho da mensagem AS2. (Opcional) |
| isMessageFailed |Booleano | Se a mensagem AS2 falha. (Obrigatório) |
| isMessageSigned | Booleano | Se estava assinada a mensagem AS2. (Obrigatório) |
| isMessageEncrypted | Booleano | Se a mensagem AS2 foi encriptada. (Obrigatório) |
| isMessageCompressed |Booleano | Se a mensagem AS2 tenha sido compactada. (Obrigatório) |
| correlationMessageId | String | ID da mensagem AS2, para correlacionar mensagens com MDNs. (Opcional) |
| incomingHeaders |Dicionário de JToken | Detalhes de cabeçalho de mensagem de entrada AS2. (Opcional) |
| outgoingHeaders |Dicionário de JToken | Detalhes de cabeçalho de mensagem de saída AS2. (Opcional) |
| isNrrEnabled | Booleano | Utilize o valor predefinido se o valor não é conhecido. (Obrigatório) |
| isMdnExpected | Booleano | Utilize o valor predefinido se o valor não é conhecido. (Obrigatório) |
| mdnType | Enum | Valores permitidos são **NotConfigured**, **sincronização**, e **Async**. (Obrigatório) |
||||

## <a name="as2-mdn-tracking-schema"></a>Esquema de controlo AS2 MDN

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": "g"
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

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| senderPartnerName | String | Nome do parceiro do remetente da mensagem AS2. (Opcional) |
| receiverPartnerName | String | Nome do parceiro do destinatário de mensagem AS2. (Opcional) |
| as2To | String | Nome do parceiro que recebe a mensagem AS2. (Obrigatório) |
| as2From | String | Nome do parceiro que envia a mensagem AS2. (Obrigatório) |
| agreementName | String | Nome do contrato de AS2 ao qual as mensagens são resolvidas. (Opcional) |
| direção |String | Direção do fluxo da mensagem, receber ou enviar. (Obrigatório) |
| messageId | String | ID da mensagem AS2. (Opcional) |
| originalMessageId |String | AS2 original ID da mensagem. (Opcional) |
| dispositionType | String | Valor de tipo de disposição de MDN. (Opcional) |
| isMessageFailed |Booleano | Se a mensagem AS2 falha. (Obrigatório) |
| isMessageSigned |Booleano | Se estava assinada a mensagem AS2. (Obrigatório) |
| isNrrEnabled | Booleano | Utilize o valor predefinido se o valor não é conhecido. (Obrigatório) |
| statusCode | Enum | Valores permitidos são **aceite**, **rejeitado**, e **AcceptedWithErrors**. (Obrigatório) |
| micVerificationStatus | Enum | Valores permitidos são **não aplicável**, **Succeeded**, e **falha**. (Obrigatório) |
| correlationMessageId | String | ID de correlação. O original messaged ID (o ID da mensagem da mensagem para o qual o MDN está configurado). (Opcional) |
| incomingHeaders | Dicionário de JToken | Indica os detalhes de cabeçalho da mensagem recebida. (Opcional) |
| outgoingHeaders |Dicionário de JToken | Indica a saída detalhes de cabeçalho da mensagem. (Opcional) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Esquemas de controlo de protocolo de B2B

Para obter informações sobre esquemas de controlo de protocolo de B2B, consulte:

* [Esquemas de controlo de X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [B2B personalizadas de esquemas de controlo](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [monitorizar mensagens B2B](logic-apps-monitor-b2b-message.md)
* Saiba mais sobre [mensagens B2B no Azure Monitor registos de controlo](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)