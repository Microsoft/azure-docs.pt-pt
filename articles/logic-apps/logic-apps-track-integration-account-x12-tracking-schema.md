---
title: X12 rastreio de schemas para mensagens B2B
description: Criar esquemas de rastreio para monitorizar mensagens X12 para aplicações lógicas do Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76905297"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>Crie esquemas para rastrear mensagens X12 em Aplicações Lógicas Azure

Para ajudá-lo a monitorizar o sucesso, erros e propriedades de mensagens para transações negócio-a-negócio (B2B), você pode usar estes esquemas de rastreio X12 na sua conta de integração:

* Esquema de rastreio de conjunto de transações X12
* X12 conjunto de reconhecimento esquema de rastreamento
* Esquema de rastreio de intercâmbio X12
* X12 troca ndo o esquema de rastreio
* Esquema de rastreio de grupo funcional X12
* X12 grupo funcional reconhecimento de esquema de rastreamento

## <a name="x12-transaction-set-tracking-schema"></a>Esquema de rastreio de conjunto de transações X12

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
| senderPartnerName | Não | String | Nome do parceiro do remetente de mensagens X12 |
| receptorPartnerName | Não | String | Nome do parceiro do recetor de mensagens X12 |
| remetenteQualifier | Sim | String | Enviar qualificação de parceiro |
| remetente Identificador | Sim | String | Enviar identificador parceiro |
| receptorQualifier | Sim | String | Receber qualificação de parceiro |
| receptorIdentificador | Sim | String | Receba identificador parceiro |
| acordoNome | Não | String | Nome do acordo X12 ao qual as mensagens são resolvidas |
| direção | Sim | Enum | Direção do fluxo de `receive` mensagem, que é ou ou`send` |
| intercâmbioControlNumber | Não | String | Número de controlo de intercâmbio |
| funcionalGroupControlNumber | Não | String | Número de controlo funcional |
| transacçãoSetControlNumber | Não | String | Número de controlo definido de transação |
| Mensagem de correlação | Não | String | Id de mensagem de correlação, que é uma combinação de {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber} |
| mensagemTipo | Não | String | Conjunto de transações ou tipo de documento |
| isMessageFailed | Sim | Booleano | Se a mensagem X12 falhou |
| isReconhecimento Técnico Esperado | Sim | Booleano | Se o reconhecimento técnico está configurado no acordo X12 |
| isFunctionalReconhecementExpected | Sim | Booleano | Se o reconhecimento funcional está configurado no acordo X12 |
| needAk2LoopForValidMessages | Sim | Booleano | Se o loop AK2 é necessário para uma mensagem válida |
| segmentosCount | Não | Número inteiro | Número de segmentos no conjunto de transações X12 |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>X12 conjunto de reconhecimento esquema de rastreamento

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
| senderPartnerName | Não | String | Nome do parceiro do remetente de mensagens X12 |
| receptorPartnerName | Não | String | Nome do parceiro do recetor de mensagens X12 |
| remetenteQualifier | Sim | String | Enviar qualificação de parceiro |
| remetente Identificador | Sim | String | Enviar identificador parceiro |
| receptorQualifier | Sim | String | Receber qualificação de parceiro |
| receptorIdentificador | Sim | String | Receba identificador parceiro |
| acordoNome | Não | String | Nome do acordo X12 ao qual as mensagens são resolvidas |
| direção | Sim | Enum | Direção do fluxo de `receive` mensagem, que é ou ou`send` |
| intercâmbioControlNumber | Não | String | Número de controlo de intercâmbio do reconhecimento funcional. O valor povoa apenas para o lado do envio onde o reconhecimento funcional é recebido para as mensagens enviadas ao parceiro. |
| funcionalGroupControlNumber | Não | String | Número funcional de controlo de grupo do reconhecimento funcional. O valor povoa apenas para o lado de envio onde o reconhecimento funcional é recebido para as mensagens enviadas ao parceiro |
| isaSegment | Não | String | Segmento ISA da mensagem. O valor povoa apenas para o lado de envio onde o reconhecimento funcional é recebido para as mensagens enviadas ao parceiro |
| gsSegment | Não | String | Segmento GS da mensagem. O valor povoa apenas para o lado de envio onde o reconhecimento funcional é recebido para as mensagens enviadas ao parceiro |
| respondendoFuncionalGroupControlNumber | Não | String | O número de controlo de intercâmbio de resposta |
| respondendoFunctionalGroupId | Não | String | O id do grupo funcional de resposta, que mapeia para AK101 no reconhecimento |
| responder transacçõesSetControlNumber | Não | String | O número de controlo de conjunto de transações de resposta |
| respondendoTransactionSetId | Não | String | O conjunto de transações respondendo ID, que mapeia para AK201 no reconhecimento |
| estadoCódigo | Sim | Booleano | Código de estado de reconhecimento definido de transação |
| segmentosCount | Sim | Enum | Código de estatuto de reconhecimento `Accepted`com `Rejected`estes valores permitidos: , e`AcceptedWithErrors` |
| processamentoEstatuto | Sim | Enum | Estatuto de processamento do reconhecimento com `Received`estes `Generated`valores permitidos: , e`Sent` |
| Mensagem de correlação | Não | String | Id de mensagem de correlação, que é uma combinação de {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber} |
| isMessageFailed | Sim | Booleano | Se a mensagem X12 falhou |
| ak2Segment | Não | String | Reconhecimento de uma transação definida dentro do grupo funcional recebido |
| ak3Segment | Não | String | Reporta erros num segmento de dados |
| ak5Segment | Não | String | Relatórios se o conjunto de transações identificado no segmento AK2 é aceite ou rejeitado, e por que |
|||||

## <a name="x12-interchange-tracking-schema"></a>Esquema de rastreio de intercâmbio X12

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
| senderPartnerName | Não | String | Nome do parceiro do remetente de mensagens X12 |
| receptorPartnerName | Não | String | Nome do parceiro do recetor de mensagens X12 |
| remetenteQualifier | Sim | String | Enviar qualificação de parceiro |
| remetente Identificador | Sim | String | Enviar identificador parceiro |
| receptorQualifier | Sim | String | Receber qualificação de parceiro |
| receptorIdentificador | Sim | String | Receba identificador parceiro |
| acordoNome | Não | String | Nome do acordo X12 ao qual as mensagens são resolvidas |
| direção | Sim | Enum | Direção do fluxo de `receive` mensagem, que é ou ou`send` |
| intercâmbioControlNumber | Não | String | Número de controlo de intercâmbio |
| isaSegment | Não | String | Segmento ISA de mensagem |
| isReconhecimento Técnico Esperado | Booleano | Se o reconhecimento técnico está configurado no acordo X12  |
| isMessageFailed | Sim | Booleano | Se a mensagem X12 falhou |
| isa09 | Não | String | Data de intercâmbio de documentos X12 |
| isa10 | Não | String | Tempo de intercâmbio de documentos X12 |
| isa11 | Não | String | Identificador de normas de controlo de intercâmbio X12 |
| isa12 | Não | String | Número da versão de controlo de intercâmbio X12 |
| isa14 | Não | String | Pedido de reconhecimento X12 |
| isa15 | Não | String | Indicador para ensaio ou produção |
| isa16 | Não | String | Separador de elementos |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>X12 troca ndo o esquema de rastreio

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
| senderPartnerName | Não | String | Nome do parceiro do remetente de mensagens X12 |
| receptorPartnerName | Não | String | Nome do parceiro do recetor de mensagens X12 |
| remetenteQualifier | Sim | String | Enviar qualificação de parceiro |
| remetente Identificador | Sim | String | Enviar identificador parceiro |
| receptorQualifier | Sim | String | Receber qualificação de parceiro |
| receptorIdentificador | Sim | String | Receba identificador parceiro |
| acordoNome | Não | String | Nome do acordo X12 ao qual as mensagens são resolvidas |
| direção | Sim | Enum | Direção do fluxo de `receive` mensagem, que é ou ou`send` |
| intercâmbioControlNumber | Não | String | Número de controlo de intercâmbio do reconhecimento técnico que é recebido por parceiros |
| isaSegment | Não | String | Segmento ISA para o reconhecimento técnico que é recebido de parceiros |
| respondendoInterchangeControlNumber | Não | String | Número de controlo de intercâmbio para o reconhecimento técnico que é recebido de parceiros |
| isMessageFailed | Sim | Booleano | Se a mensagem X12 falhou |
| estadoCódigo | Sim | Enum | Código de estado de reconhecimento de `Accepted` `Rejected`intercâmbio com estes valores permitidos: , e`AcceptedWithErrors` |
| processamentoEstatuto | Sim | Enum | Estatuto de reconhecimento com estes `Received` `Generated`valores permitidos: , e`Sent` |
| ta102 | Não | String | Data de intercâmbio |
| ta103 | Não | String | Tempo de intercâmbio |
| ta105 | Não | String | Código de nota de intercâmbio |
|||||

## <a name="x12-functional-group-tracking-schema"></a>Esquema de rastreio de grupo funcional X12

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
| senderPartnerName | Não | String | Nome do parceiro do remetente de mensagens X12 |
| receptorPartnerName | Não | String | Nome do parceiro do recetor de mensagens X12 |
| remetenteQualifier | Sim | String | Enviar qualificação de parceiro |
| remetente Identificador | Sim | String | Enviar identificador parceiro |
| receptorQualifier | Sim | String | Receber qualificação de parceiro |
| receptorIdentificador | Sim | String | Receba identificador parceiro |
| acordoNome | Não | String | O nome do acordo X12 ao qual as mensagens são resolvidas |
| direção | Sim | Enum | Direção do fluxo de mensagem, receber ou enviar |
| intercâmbioControlNumber | Não | String | Número de controlo de intercâmbio |
| funcionalGroupControlNumber | Não | String | Número de controlo funcional |
| gsSegment | Não | String | Segmento GS da mensagem |
| isReconhecimento Técnico Esperado | Sim | Booleano | Se o reconhecimento técnico está configurado no acordo X12 |
| isFunctionalReconhecementExpected | Sim | Booleano | Se o reconhecimento funcional está configurado no acordo X12 |
| isMessageFailed | Sim | Booleano | Se a mensagem X12 falhou |
| gs01 | Não | String | Código de identificador funcional |
| gs02 | Não | String | Código do remetente de aplicação |
| gs03 | Não | String | Código do recetor de aplicações |
| gs04 | Não | String | Data funcional do grupo |
| gs05 | Não | String | Tempo funcional do grupo |
| gs07 | Não | String | Código de agência responsável |
| gs08 | Não | String | Código de identificação para a versão, lançamento ou indústria |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>X12 grupo funcional reconhecimento de esquema de rastreamento

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
| senderPartnerName | Não | String | Nome do parceiro do remetente de mensagens X12 |
| receptorPartnerName | Não | String | Nome do parceiro do recetor de mensagens X12 |
| remetenteQualifier | Sim | String | Enviar qualificação de parceiro |
| remetente Identificador | Sim | String | Enviar identificador parceiro |
| receptorQualifier | Sim | String | Receber qualificação de parceiro |
| receptorIdentificador | Sim | String | Receba identificador parceiro |
| acordoNome | Não | String | Nome do acordo X12 ao qual as mensagens são resolvidas |
| direção | Sim | Enum | Direção do fluxo de `receive` mensagem, que é ou ou`send` |
| intercâmbioControlNumber | Não | String | Número de controlo de intercâmbio, que povoa para o lado do envio quando um reconhecimento técnico é recebido de parceiros |
| funcionalGroupControlNumber | Não | String | Número funcional de controlo do grupo do reconhecimento técnico, que povoa para o lado do envio quando um reconhecimento técnico é recebido de parceiros |
| isaSegment | Não | String | O mesmo que o número de controlo de intercâmbio, mas povoado apenas em casos específicos |
| gsSegment | Não | String | O mesmo que o número funcional de controlo de grupo, mas povoado apenas em casos específicos |
| respondendoFuncionalGroupControlNumber | Não | String | Número de controlo do grupo funcional original |
| respondendoFunctionalGroupId | Não | String | Mapas para AK101 no grupo funcional de reconhecimento ID |
| isMessageFailed | Booleano | Se a mensagem X12 falhou |
| estadoCódigo | Sim | Enum | Código de estatuto de reconhecimento `Accepted`com `Rejected`estes valores permitidos: , e`AcceptedWithErrors` |
| processamentoEstatuto | Sim | Enum | Estatuto de processamento do reconhecimento com `Received`estes `Generated`valores permitidos: , e`Sent` |
| ak903 | Não | String | Número de conjuntos de transações recebidos |
| ak904 | Não | String | Número de conjuntos de transações aceites no grupo funcional identificado |
| ak9Segment | Não | String | Se o grupo funcional identificado no segmento AK1 é aceite ou rejeitado, e por que |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B protocolo de rastreio de schemas

Para obter informações sobre os esquemas de rastreio do protocolo B2B, consulte:

* [Esquemas de controlo de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [B2B rastreio personalizado schemas](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Passos seguintes

* [Monitorizar mensagens B2B com os registos do Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)