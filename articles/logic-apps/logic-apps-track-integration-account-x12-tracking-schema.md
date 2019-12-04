---
title: Esquemas de acompanhamento de X12 para mensagens B2B
description: Criar esquemas de acompanhamento de X12 que monitorem mensagens B2B em contas de integração para aplicativos lógicos do Azure com Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 6f2356600f5b6a637da731c650b26d968092e2f6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791725"
---
# <a name="create-schemas-for-tracking-x12-messages-in-integration-accounts-for-azure-logic-apps"></a>Criar esquemas para rastrear mensagens X12 em contas de integração para aplicativos lógicos do Azure

Para ajudá-lo a monitorar o sucesso, os erros e as propriedades das mensagens para transações B2B (entre empresas), você pode usar esses esquemas de acompanhamento de X12 em sua conta de integração:

* Esquema de acompanhamento do conjunto de transações X12
* Esquema de acompanhamento de confirmação do conjunto de transações X12
* Esquema de controle de intercâmbio X12
* Esquema de acompanhamento de confirmação do X12 Interchange
* Esquema de acompanhamento de grupo funcional X12
* Esquema de acompanhamento de confirmação de grupo funcional X12

## <a name="x12-transaction-set-tracking-schema"></a>Esquema de acompanhamento do conjunto de transações X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "transactionSetControlNumber": "",
      "CorrelationMessageId": "",
      "messageType": "",
      "isMessageFailed": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "needAk2LoopForValidMessages":  "",
      "segmentsCount": ""
   }
}
```

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| senderPartnerName | String | Nome do parceiro do remetente da mensagem do X12. Adicional |
| receiverPartnerName | String | Nome do parceiro do destinatário da mensagem do X12. Adicional |
| senderQualifier | String | Qualificador de parceiro de envio. Obrigatório |
| senderIdentifier | String | Identificador de parceiro de envio. Obrigatório |
| receiverQualifier | String | Qualificador de parceiro de recebimento. Obrigatório |
| receiverIdentifier | String | Identificador de parceiro de recebimento. Obrigatório |
| agreementName | String | Nome do contrato X12 para o qual as mensagens são resolvidas. Adicional |
| direção | Enum | Direção do fluxo de mensagens, receber ou enviar. Obrigatório |
| interchangeControlNumber | String | Número de controle de intercâmbio. Adicional |
| functionalGroupControlNumber | String | Número de controle funcional. Adicional |
| transactionSetControlNumber | String | Número de controle do conjunto de transações. Adicional |
| CorrelationMessageId | String | ID da mensagem de correlação. Uma combinação de {Agreementname} {*GroupControlNumber*} {TransactionSetControlNumber}. Adicional |
| messageType | String | Conjunto de transações ou tipo de documento. Adicional |
| isMessageFailed | Booleano | Se a mensagem X12 falhou. Obrigatório |
| isTechnicalAcknowledgmentExpected | Booleano | Se a confirmação técnica está configurada no contrato X12. Obrigatório |
| isFunctionalAcknowledgmentExpected | Booleano | Se a confirmação funcional está configurada no contrato X12. Obrigatório |
| needAk2LoopForValidMessages | Booleano | Se o loop AK2 é necessário para uma mensagem válida. Obrigatório |
| segmentsCount | Número inteiro | Número de segmentos no conjunto de transações X12. Adicional |
||||

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>Esquema de acompanhamento de confirmação do conjunto de transações X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "respondingtransactionSetControlNumber": "",
      "respondingTransactionSetId": "",
      "statusCode": "",
      "processingStatus": "",
      "CorrelationMessageId": "",
      "isMessageFailed": "",
      "ak2Segment": "",
      "ak3Segment": "",
      "ak5Segment": ""
   }
}
```

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| senderPartnerName | String | Nome do parceiro do remetente da mensagem do X12. Adicional |
| receiverPartnerName | String | Nome do parceiro do destinatário da mensagem do X12. Adicional |
| senderQualifier | String | Qualificador de parceiro de envio. Obrigatório |
| senderIdentifier | String | Identificador de parceiro de envio. Obrigatório |
| receiverQualifier | String | Qualificador de parceiro de recebimento. Obrigatório |
| receiverIdentifier | String | Identificador de parceiro de recebimento. Obrigatório |
| agreementName | String | Nome do contrato X12 para o qual as mensagens são resolvidas. Adicional |
| direção | Enum | Direção do fluxo de mensagens, receber ou enviar. Obrigatório |
| interchangeControlNumber | String | O número de controle de intercâmbio da confirmação funcional. O valor é preenchido apenas para o lado de envio onde a confirmação funcional é recebida para as mensagens enviadas ao parceiro. Adicional |
| functionalGroupControlNumber | String | Número de controle de grupo funcional da confirmação funcional. O valor é preenchido apenas para o lado de envio onde a confirmação funcional é recebida para as mensagens enviadas ao parceiro. Adicional |
| isaSegment | String | Segmento ISA da mensagem. O valor é preenchido apenas para o lado de envio onde a confirmação funcional é recebida para as mensagens enviadas ao parceiro. Adicional |
| gsSegment | String | Segmento GS da mensagem. O valor é preenchido apenas para o lado de envio onde a confirmação funcional é recebida para as mensagens enviadas ao parceiro. Adicional |
| respondingfunctionalGroupControlNumber | String | Respondendo o número de controle de intercâmbio. Adicional |
| respondingFunctionalGroupId | String | ID do grupo funcional de resposta, que é mapeado para AK101 na confirmação. Adicional |
| respondingtransactionSetControlNumber | String | Número de controle do conjunto de transações de resposta. Adicional |
| respondingTransactionSetId | String | ID do conjunto de transações de resposta, que mapeia para AK201 na confirmação. Adicional |
| statusCode | Booleano | Código de status de confirmação do conjunto de transações. Obrigatório |
| segmentsCount | Enum | Código de status de confirmação. Os valores permitidos são **accepted**, **Rejected**e **AcceptedWithErrors**. Obrigatório |
| processingStatus | Enum | Status de processamento da confirmação. Os valores permitidos são **recebidos**, **gerados**e **enviados**. Obrigatório |
| CorrelationMessageId | String | ID da mensagem de correlação. Uma combinação de {Agreementname} {*GroupControlNumber*} {TransactionSetControlNumber}. Adicional |
| isMessageFailed | Booleano | Se a mensagem X12 falhou. Obrigatório |
| ak2Segment | String | Confirmação de um conjunto de transações dentro do grupo funcional recebido. Adicional |
| ak3Segment | String | Relata erros em um segmento de dados. Adicional |
| ak5Segment | String | Relata se o conjunto de transações identificado no segmento AK2 é aceito ou rejeitado, e por quê. Adicional |
||||

## <a name="x12-interchange-tracking-schema"></a>Esquema de controle de intercâmbio X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "isa09": "",
      "isa10": "",
      "isa11": "",
      "isa12": "",
      "isa14": "",
      "isa15": "",
      "isa16": ""
   }
}
```

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| senderPartnerName | String | Nome do parceiro do remetente da mensagem do X12. Adicional |
| receiverPartnerName | String | Nome do parceiro do destinatário da mensagem do X12. Adicional |
| senderQualifier | String | Qualificador de parceiro de envio. Obrigatório |
| senderIdentifier | String | Identificador de parceiro de envio. Obrigatório |
| receiverQualifier | String | Qualificador de parceiro de recebimento. Obrigatório |
| receiverIdentifier | String | Identificador de parceiro de recebimento. Obrigatório |
| agreementName | String | Nome do contrato X12 para o qual as mensagens são resolvidas. Adicional |
| direção | Enum | Direção do fluxo de mensagens, receber ou enviar. Obrigatório |
| interchangeControlNumber | String | Número de controle de intercâmbio. Adicional |
| isaSegment | String | Segmento ISA de mensagem. Adicional |
| isTechnicalAcknowledgmentExpected | Booleano | Se a confirmação técnica está configurada no contrato X12. Obrigatório |
| isMessageFailed | Booleano | Se a mensagem X12 falhou. Obrigatório |
| isa09 | String | Data de intercâmbio do documento X12. Adicional |
| isa10 | String | Tempo de intercâmbio do documento X12. Adicional |
| isa11 | String | Identificador de padrões de controle de intercâmbio X12. Adicional |
| isa12 | String | Número de versão do controle de intercâmbio X12. Adicional |
| isa14 | String | A confirmação do X12 é solicitada. Adicional |
| isa15 | String | Indicador para teste ou produção. Adicional |
| isa16 | String | Separador de elemento. Adicional |
||||

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>Esquema de acompanhamento de confirmação do X12 Interchange

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "respondingInterchangeControlNumber": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ta102": "",
      "ta103": "",
      "ta105": ""
   }
}
```

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| senderPartnerName | String | Nome do parceiro do remetente da mensagem do X12. Adicional |
| receiverPartnerName | String | Nome do parceiro do destinatário da mensagem do X12. Adicional |
| senderQualifier | String | Qualificador de parceiro de envio. Obrigatório |
| senderIdentifier | String | Identificador de parceiro de envio. Obrigatório |
| receiverQualifier | String | Qualificador de parceiro de recebimento. Obrigatório |
| receiverIdentifier | String | Identificador de parceiro de recebimento. Obrigatório |
| agreementName | String | Nome do contrato X12 para o qual as mensagens são resolvidas. Adicional |
| direção | Enum | Direção do fluxo de mensagens, receber ou enviar. Obrigatório |
| interchangeControlNumber | String | O número de controle de intercâmbio da confirmação técnica que é recebida dos parceiros. Adicional |
| isaSegment | String | O segmento ISA para a confirmação técnica que é recebida dos parceiros. Adicional |
| respondingInterchangeControlNumber |String | Número de controle de intercâmbio para a confirmação técnica recebida dos parceiros. Adicional |
| isMessageFailed | Booleano | Se a mensagem X12 falhou. Obrigatório |
| statusCode | Enum | Código de status de confirmação do intercâmbio. Os valores permitidos são **accepted**, **Rejected**e **AcceptedWithErrors**. Obrigatório |
| processingStatus | Enum | Status de confirmação. Os valores permitidos são **recebidos**, **gerados**e **enviados**. Obrigatório |
| ta102 | String | Data de intercâmbio. Adicional |
| ta103 | String | Tempo de intercâmbio. Adicional |
| ta105 | String | Código de nota de intercâmbio. Adicional |
||||

## <a name="x12-functional-group-tracking-schema"></a>Esquema de acompanhamento de grupo funcional X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "gsSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "gs01": "",
      "gs02": "",
      "gs03": "",
      "gs04": "",
      "gs05": "",
      "gs07": "",
      "gs08": ""
   }
}
```

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| senderPartnerName | String | Nome do parceiro do remetente da mensagem do X12. Adicional |
| receiverPartnerName | String | Nome do parceiro do destinatário da mensagem do X12. Adicional |
| senderQualifier | String | Qualificador de parceiro de envio. Obrigatório |
| senderIdentifier | String | Identificador de parceiro de envio. Obrigatório |
| receiverQualifier | String | Qualificador de parceiro de recebimento. Obrigatório |
| receiverIdentifier | String | Identificador de parceiro de recebimento. Obrigatório |
| agreementName | String | Nome do contrato X12 para o qual as mensagens são resolvidas. Adicional |
| direção | Enum | Direção do fluxo de mensagens, receber ou enviar. Obrigatório |
| interchangeControlNumber | String | Número de controle de intercâmbio. Adicional |
| functionalGroupControlNumber | String | Número de controle funcional. Adicional |
| gsSegment | String | Segmento GS da mensagem. Adicional |
| isTechnicalAcknowledgmentExpected | Booleano | Se a confirmação técnica está configurada no contrato X12. Obrigatório |
| isFunctionalAcknowledgmentExpected | Booleano | Se a confirmação funcional está configurada no contrato X12. Obrigatório |
| isMessageFailed | Booleano | Se a mensagem X12 falhou. Obrigatório|
| gs01 | String | Código de identificador funcional. Adicional |
| gs02 | String | Código do remetente do aplicativo. Adicional |
| gs03 | String | Código do receptor do aplicativo. Adicional |
| gs04 | String | Data do grupo funcional. Adicional |
| gs05 | String | Hora do grupo funcional. Adicional |
| gs07 | String | Código da agência responsável. Adicional |
| gs08 | String | Código da versão/versão/identificador do setor. Adicional |
||||

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>Esquema de acompanhamento de confirmação de grupo funcional X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ak903": "",
      "ak904": "",
      "ak9Segment": ""
   }
}
```

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| senderPartnerName | String | Nome do parceiro do remetente da mensagem do X12. Adicional |
| receiverPartnerName | String | Nome do parceiro do destinatário da mensagem do X12. Adicional |
| senderQualifier | String | Qualificador de parceiro de envio. Obrigatório |
| senderIdentifier | String | Identificador de parceiro de envio. Obrigatório |
| receiverQualifier | String | Qualificador de parceiro de recebimento. Obrigatório |
| receiverIdentifier | String | Identificador de parceiro de recebimento. Obrigatório |
| agreementName | String | Nome do contrato X12 para o qual as mensagens são resolvidas. Adicional |
| direção | Enum | Direção do fluxo de mensagens, receber ou enviar. Obrigatório |
| interchangeControlNumber | String | Número de controle de intercâmbio, que preenche o lado de envio quando uma confirmação técnica é recebida de parceiros. Adicional |
| functionalGroupControlNumber | String | Número de controle de grupo funcional da confirmação técnica, que preenche o lado de envio quando uma confirmação técnica é recebida dos parceiros. Adicional |
| isaSegment | String | O mesmo que o número de controle de intercâmbio, mas preenchido apenas em casos específicos. Adicional |
| gsSegment | String | O mesmo que o número de controle de grupo funcional, mas preenchido somente em casos específicos. Adicional |
| respondingfunctionalGroupControlNumber | String | Número de controle do grupo funcional original. Adicional |
| respondingFunctionalGroupId | String | Mapeia para AK101 na ID do grupo funcional de confirmação. Adicional |
| isMessageFailed | Booleano | Se a mensagem X12 falhou. Obrigatório |
| statusCode | Enum | Código de status de confirmação. Os valores permitidos são **accepted**, **Rejected**e **AcceptedWithErrors**. Obrigatório |
| processingStatus | Enum | Status de processamento da confirmação. Os valores permitidos são **recebidos**, **gerados**e **enviados**. Obrigatório |
| ak903 | String | Número de conjuntos de transações recebidos. Adicional |
| ak904 | String | Número de conjuntos de transações aceitos no grupo funcional identificado. Adicional |
| ak9Segment | String | Se o grupo funcional identificado no segmento AK1 é aceito ou rejeitado, e por quê. Adicional |
|||| 

## <a name="b2b-protocol-tracking-schemas"></a>Esquemas de acompanhamento do protocolo B2B

Para obter informações sobre esquemas de controle de protocolo B2B, consulte:

* [Esquemas de controlo de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Esquemas de acompanhamento personalizados B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como [monitorar mensagens B2B](logic-apps-monitor-b2b-message.md).
* Saiba mais sobre como [controlar mensagens B2B em logs de Azure monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
