---
title: Esquemas de rastreio X12 para mensagens B2B
description: Criar esquemas de rastreio para monitorizar mensagens X12 para Apps Azure Logic
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76905297"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>Criar esquemas para rastrear mensagens X12 em Azure Logic Apps

Para ajudá-lo a monitorizar propriedades de sucesso, erros e mensagens para transações business-to-business (B2B), pode utilizar estes esquemas de rastreio X12 na sua conta de integração:

* Esquema de rastreamento de conjunto de transação X12
* X12 conjunto de transação reconhecimento de localização esquema
* Esquema de rastreio de permuta X12
* X12 esquema de rastreio de reconhecimento de intercâmbio
* Esquema de rastreio funcional do grupo X12
* X12 sistema de reconhecimento funcional de grupo de rastreamento

## <a name="x12-transaction-set-tracking-schema"></a>Esquema de rastreamento de conjunto de transação X12

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
      "needAk2LoopForValidMessages": "",
      "segmentsCount": ""
   }
}
```

| Propriedade | Necessário | Tipo | Descrição |
|----------|----------|------|-------------|
| nome senderPartner | Não | String | Nome do parceiro do remetente de mensagens X12 |
| receptorPartnerName | Não | String | Nome do parceiro do recetor de mensagens X12 |
| remetenteQualifier | Sim | String | Enviar qualificação de parceiro |
| espetador | Sim | String | Enviar identificador de parceiro |
| receptorQualifier | Sim | String | Receber qualificação de parceiro |
| receptorIdentifier | Sim | String | Receber identificador de parceiro |
| agreementName | Não | String | Nome do acordo X12 para o qual as mensagens são resolvidas |
| direção | Sim | Enumeração | Direção do fluxo de mensagem, que é `receive` ou `send` |
| interchangeControlNumber | Não | String | Número de controlo de intercâmbio |
| funcionalGroupControlNumber | Não | String | Número de controlo funcional |
| transactionSetControlNumber | Não | String | Número de controlo definido por transação |
| CorrelationMessageId | Não | String | ID de mensagem de correlação, que é uma combinação de {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber} |
| mensagemType | Não | String | Conjunto de transações ou tipo de documento |
| isMessageFailed | Sim | Booleano | Se a mensagem X12 falhou |
| isTechnicalAcknowledgmentExpected | Sim | Booleano | Se o reconhecimento técnico está configurado no acordo X12 |
| isFunctionalAcknowledgmentExpected | Sim | Booleano | Se o reconhecimento funcional está configurado no acordo X12 |
| needAk2LoopForValidMesages | Sim | Booleano | Se o loop AK2 é necessário para uma mensagem válida |
| segmentosConse | Não | Número inteiro | Número de segmentos no conjunto de transações X12 |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>X12 conjunto de transação reconhecimento de localização esquema

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

| Propriedade | Necessário | Tipo | Descrição |
|----------|----------|------|-------------|
| nome senderPartner | Não | String | Nome do parceiro do remetente de mensagens X12 |
| receptorPartnerName | Não | String | Nome do parceiro do recetor de mensagens X12 |
| remetenteQualifier | Sim | String | Enviar qualificação de parceiro |
| espetador | Sim | String | Enviar identificador de parceiro |
| receptorQualifier | Sim | String | Receber qualificação de parceiro |
| receptorIdentifier | Sim | String | Receber identificador de parceiro |
| agreementName | Não | String | Nome do acordo X12 para o qual as mensagens são resolvidas |
| direção | Sim | Enumeração | Direção do fluxo de mensagem, que é `receive` ou `send` |
| interchangeControlNumber | Não | String | Número de controlo de intercâmbio do reconhecimento funcional. O valor só se encontra para o lado do envio onde é recebido reconhecimento funcional para as mensagens enviadas ao parceiro. |
| funcionalGroupControlNumber | Não | String | Número funcional de controlo do grupo do reconhecimento funcional. O valor povoa apenas para o lado de envio onde o reconhecimento funcional é recebido para as mensagens enviadas ao parceiro |
| isaSegment | Não | String | Segmento ISA da mensagem. O valor povoa apenas para o lado de envio onde o reconhecimento funcional é recebido para as mensagens enviadas ao parceiro |
| gsSegment | Não | String | Segmento GS da mensagem. O valor povoa apenas para o lado de envio onde o reconhecimento funcional é recebido para as mensagens enviadas ao parceiro |
| resposta functionalGroupControlNumber | Não | String | O número de controlo de intercâmbio de resposta |
| respondendoFunctionalGroupId | Não | String | O ID funcional respondendo, que mapeia para AK101 no reconhecimento |
| resposta do combate ao controlo de controlo de transportes | Não | String | O número de controlo do conjunto de transações de resposta |
| respondendoTransactionSetId | Não | String | O ID do conjunto de transações respondendo, que mapeia para AK201 no reconhecimento |
| statusCode | Sim | Booleano | Código de estado de reconhecimento definido por transação |
| segmentosConse | Sim | Enumeração | Código de estado de reconhecimento com estes valores permitidos: `Accepted` `Rejected` , e `AcceptedWithErrors` |
| processamentoStatus | Sim | Enumeração | Estado de processamento do reconhecimento com estes valores permitidos: `Received` `Generated` , e `Sent` |
| CorrelationMessageId | Não | String | ID de mensagem de correlação, que é uma combinação de {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber} |
| isMessageFailed | Sim | Booleano | Se a mensagem X12 falhou |
| ak2Segment | Não | String | Reconhecimento de uma transação definida dentro do grupo funcional recebido |
| ak3Segment | Não | String | Reporta erros num segmento de dados |
| ak5Segment | Não | String | Relatórios se o conjunto de transações identificado no segmento AK2 é aceite ou rejeitado, e porquê |
|||||

## <a name="x12-interchange-tracking-schema"></a>Esquema de rastreio de permuta X12

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

| Propriedade | Necessário | Tipo | Descrição |
|----------|----------|------|-------------|
| nome senderPartner | Não | String | Nome do parceiro do remetente de mensagens X12 |
| receptorPartnerName | Não | String | Nome do parceiro do recetor de mensagens X12 |
| remetenteQualifier | Sim | String | Enviar qualificação de parceiro |
| espetador | Sim | String | Enviar identificador de parceiro |
| receptorQualifier | Sim | String | Receber qualificação de parceiro |
| receptorIdentifier | Sim | String | Receber identificador de parceiro |
| agreementName | Não | String | Nome do acordo X12 para o qual as mensagens são resolvidas |
| direção | Sim | Enumeração | Direção do fluxo de mensagem, que é `receive` ou `send` |
| interchangeControlNumber | Não | String | Número de controlo de intercâmbio |
| isaSegment | Não | String | Segmento isa de mensagem |
| isTechnicalAcknowledgmentExpected | Booleano | Se o reconhecimento técnico está configurado no acordo X12  |
| isMessageFailed | Sim | Booleano | Se a mensagem X12 falhou |
| isa09 | Não | String | Data de troca de documentos X12 |
| isa10 | Não | String | Tempo de troca de documentos X12 |
| isa11 | Não | String | Identificador de normas de controlo de intercâmbio X12 |
| isa12 | Não | String | Número da versão do controlo de intercâmbio X12 |
| isa14 | Não | String | X12 reconhecimento é solicitado |
| isa15 | Não | String | Indicador de ensaio ou produção |
| isa16 | Não | String | Separador de elementos |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>X12 esquema de rastreio de reconhecimento de intercâmbio

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

| Propriedade | Necessário | Tipo | Descrição |
|----------|----------|------|-------------|
| nome senderPartner | Não | String | Nome do parceiro do remetente de mensagens X12 |
| receptorPartnerName | Não | String | Nome do parceiro do recetor de mensagens X12 |
| remetenteQualifier | Sim | String | Enviar qualificação de parceiro |
| espetador | Sim | String | Enviar identificador de parceiro |
| receptorQualifier | Sim | String | Receber qualificação de parceiro |
| receptorIdentifier | Sim | String | Receber identificador de parceiro |
| agreementName | Não | String | Nome do acordo X12 para o qual as mensagens são resolvidas |
| direção | Sim | Enumeração | Direção do fluxo de mensagem, que é `receive` ou `send` |
| interchangeControlNumber | Não | String | Número de controlo de intercâmbio do reconhecimento técnico recebido dos parceiros |
| isaSegment | Não | String | Segmento ISA para o reconhecimento técnico que é recebido dos parceiros |
| respondendo InterchangeControlNumber | Não | String | Número de controlo de intercâmbio para o reconhecimento técnico que é recebido dos parceiros |
| isMessageFailed | Sim | Booleano | Se a mensagem X12 falhou |
| statusCode | Sim | Enumeração | Código de estado de reconhecimento de intercâmbio com estes valores permitidos: `Accepted` `Rejected` , e `AcceptedWithErrors` |
| processamentoStatus | Sim | Enumeração | Estatuto de reconhecimento com estes valores permitidos: `Received` `Generated` , e `Sent` |
| ta102 | Não | String | Data de intercâmbio |
| ta103 | Não | String | Tempo de troca |
| ta105 | Não | String | Código de nota de intercâmbio |
|||||

## <a name="x12-functional-group-tracking-schema"></a>Esquema de rastreio funcional do grupo X12

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

| Propriedade | Necessário | Tipo | Descrição |
|----------|----------|------|-------------|
| nome senderPartner | Não | String | Nome do parceiro do remetente de mensagens X12 |
| receptorPartnerName | Não | String | Nome do parceiro do recetor de mensagens X12 |
| remetenteQualifier | Sim | String | Enviar qualificação de parceiro |
| espetador | Sim | String | Enviar identificador de parceiro |
| receptorQualifier | Sim | String | Receber qualificação de parceiro |
| receptorIdentifier | Sim | String | Receber identificador de parceiro |
| agreementName | Não | String | O nome do acordo X12 para o qual as mensagens são resolvidas |
| direção | Sim | Enumeração | Direção do fluxo de mensagem, ou receber ou enviar |
| interchangeControlNumber | Não | String | Número de controlo de intercâmbio |
| funcionalGroupControlNumber | Não | String | Número de controlo funcional |
| gsSegment | Não | String | Segmento GS de mensagem |
| isTechnicalAcknowledgmentExpected | Sim | Booleano | Se o reconhecimento técnico está configurado no acordo X12 |
| isFunctionalAcknowledgmentExpected | Sim | Booleano | Se o reconhecimento funcional está configurado no acordo X12 |
| isMessageFailed | Sim | Booleano | Se a mensagem X12 falhou |
| gs01 | Não | String | Código de identificador funcional |
| gs02 | Não | String | Código do remetente de aplicações |
| gs03 | Não | String | Código do recetor de aplicações |
| gs04 | Não | String | Data funcional do grupo |
| gs05 | Não | String | Tempo de grupo funcional |
| gs07 | Não | String | Código de agência responsável |
| gs08 | Não | String | Código identificador para a versão, lançamento ou indústria |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>X12 sistema de reconhecimento funcional de grupo de rastreamento

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

| Propriedade | Necessário | Tipo | Descrição |
|----------|----------|------|-------------|
| nome senderPartner | Não | String | Nome do parceiro do remetente de mensagens X12 |
| receptorPartnerName | Não | String | Nome do parceiro do recetor de mensagens X12 |
| remetenteQualifier | Sim | String | Enviar qualificação de parceiro |
| espetador | Sim | String | Enviar identificador de parceiro |
| receptorQualifier | Sim | String | Receber qualificação de parceiro |
| receptorIdentifier | Sim | String | Receber identificador de parceiro |
| agreementName | Não | String | Nome do acordo X12 para o qual as mensagens são resolvidas |
| direção | Sim | Enumeração | Direção do fluxo de mensagem, que é `receive` ou `send` |
| interchangeControlNumber | Não | String | Número de controlo de intercâmbio, que povoa para o lado de envio quando um reconhecimento técnico é recebido de parceiros |
| funcionalGroupControlNumber | Não | String | Número funcional de controlo do grupo do reconhecimento técnico, que povoa para o lado de envio quando um reconhecimento técnico é recebido de parceiros |
| isaSegment | Não | String | O mesmo que o número de controlo de intercâmbio, mas povoado apenas em casos específicos |
| gsSegment | Não | String | O mesmo que o número funcional de controlo do grupo, mas povoado apenas em casos específicos |
| resposta functionalGroupControlNumber | Não | String | Número de controlo do grupo funcional original |
| respondendoFunctionalGroupId | Não | String | Mapas para AK101 no ID funcional de reconhecimento |
| isMessageFailed | Booleano | Se a mensagem X12 falhou |
| statusCode | Sim | Enumeração | Código de estado de reconhecimento com estes valores permitidos: `Accepted` `Rejected` , e `AcceptedWithErrors` |
| processamentoStatus | Sim | Enumeração | Estado de processamento do reconhecimento com estes valores permitidos: `Received` `Generated` , e `Sent` |
| ak903 | Não | String | Número de conjuntos de transações recebidos |
| ak904 | Não | String | Número de conjuntos de transações aceites no grupo funcional identificado |
| ak9Segment | Não | String | Se o grupo funcional identificado no segmento AK1 é aceite ou rejeitado, e porquê |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Esquemas de rastreio do protocolo B2B

Para obter informações sobre os esquemas de rastreio do protocolo B2B, consulte:

* [Esquemas de controlo de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de rastreio personalizados B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Passos seguintes

* [Monitorizar mensagens B2B com os registos do Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)