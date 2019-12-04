---
title: Esquemas de controle AS2 para mensagens B2B
description: Criar esquemas de controle AS2 que monitorem mensagens B2B em contas de integração para aplicativos lógicos do Azure com Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 515d7cfc985ee9929f70de2c862170ff79ae4d60
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792803"
---
# <a name="create-schemas-for-tracking-as2-messages-and-mdns-in-integration-accounts-for-azure-logic-apps"></a>Criar esquemas para acompanhar mensagens AS2 e MDNs em contas de integração para aplicativos lógicos do Azure

Para ajudá-lo a monitorar o sucesso, os erros e as propriedades das mensagens para transações B2B (entre empresas), você pode usar esses esquemas de controle do AS2 em sua conta de integração:

* Esquema de controle de mensagens AS2
* Esquema de acompanhamento de MDN AS2

## <a name="as2-message-tracking-schema"></a>Esquema de controle de mensagens AS2

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
| senderPartnerName | String | Nome do parceiro do remetente da mensagem AS2. Adicional |
| receiverPartnerName | String | Nome do parceiro do destinatário da mensagem AS2. Adicional |
| as2To | String | Nome do destinatário da mensagem AS2, dos cabeçalhos da mensagem AS2. Obrigatório |
| as2From | String | Nome do remetente da mensagem AS2, dos cabeçalhos da mensagem AS2. Obrigatório |
| agreementName | String | Nome do contrato AS2 para o qual as mensagens são resolvidas. Adicional |
| direção | String | Direção do fluxo de mensagens, receber ou enviar. Obrigatório |
| messageId | String | ID da mensagem AS2, dos cabeçalhos da mensagem AS2 (opcional) |
| desposicionatype |String | Valor do tipo de disposição de notificação de disposição de mensagem (MDN). Adicional |
| fileName | String | Nome do arquivo, a partir do cabeçalho da mensagem AS2. Adicional |
| isMessageFailed |Booleano | Se a mensagem AS2 falhou. Obrigatório |
| isMessageSigned | Booleano | Se a mensagem AS2 foi assinada. Obrigatório |
| isMessageEncrypted | Booleano | Se a mensagem AS2 foi criptografada. Obrigatório |
| isMessageCompressed |Booleano | Se a mensagem AS2 foi compactada. Obrigatório |
| CorrelationMessageId | String | ID da mensagem AS2, para correlacionar mensagens com MDNs. Adicional |
| incomingHeaders |Dicionário de JToken | Detalhes do cabeçalho da mensagem AS2 de entrada. Adicional |
| outgoingHeaders |Dicionário de JToken | Detalhes do cabeçalho da mensagem AS2 de saída. Adicional |
| isNrrEnabled | Booleano | Use o valor padrão se o valor não for conhecido. Obrigatório |
| isMdnExpected | Booleano | Use o valor padrão se o valor não for conhecido. Obrigatório |
| mdnType | Enum | Os valores permitidos são não **configurados**, **sincronizados**e **assíncronos**. Obrigatório |
||||

## <a name="as2-mdn-tracking-schema"></a>Esquema de acompanhamento de MDN AS2

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
| senderPartnerName | String | Nome do parceiro do remetente da mensagem AS2. Adicional |
| receiverPartnerName | String | Nome do parceiro do destinatário da mensagem AS2. Adicional |
| as2To | String | Nome do parceiro que recebe a mensagem AS2. Obrigatório |
| as2From | String | Nome do parceiro que envia a mensagem AS2. Obrigatório |
| agreementName | String | Nome do contrato AS2 para o qual as mensagens são resolvidas. Adicional |
| direção |String | Direção do fluxo de mensagens, receber ou enviar. Obrigatório |
| messageId | String | ID da mensagem AS2. Adicional |
| originalMessageId |String | ID da mensagem original AS2. Adicional |
| desposicionatype | String | Valor do tipo de disposição MDN. Adicional |
| isMessageFailed |Booleano | Se a mensagem AS2 falhou. Obrigatório |
| isMessageSigned |Booleano | Se a mensagem AS2 foi assinada. Obrigatório |
| isNrrEnabled | Booleano | Use o valor padrão se o valor não for conhecido. Obrigatório |
| statusCode | Enum | Os valores permitidos são **accepted**, **Rejected**e **AcceptedWithErrors**. Obrigatório |
| micVerificationStatus | Enum | Os valores permitidos são não **aplicáveis**, com **êxito**e **falharam**. Obrigatório |
| CorrelationMessageId | String | ID de correlação. A ID da mensagem original (a ID da mensagem para a qual MDN está configurado). Adicional |
| incomingHeaders | Dicionário de JToken | Indica detalhes do cabeçalho da mensagem de entrada. Adicional |
| outgoingHeaders |Dicionário de JToken | Indica detalhes do cabeçalho da mensagem de saída. Adicional |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Esquemas de acompanhamento do protocolo B2B

Para obter informações sobre esquemas de controle de protocolo B2B, consulte:

* [Esquemas de controlo de X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquemas de acompanhamento personalizados B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [monitoramento de mensagens B2B](logic-apps-monitor-b2b-message.md)
* Saiba mais sobre como [controlar mensagens B2B em logs de Azure monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)