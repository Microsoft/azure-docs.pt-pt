---
title: Esquemas para mensagens B2B - Azure Logic Apps de controlo personalizado | Documentos da Microsoft
description: Criar esquemas de controle personalizados que monitorizar mensagens B2B no contas de integração para o Azure Logic Apps com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.date: 01/27/2017
ms.openlocfilehash: 76a9ece9e925543e856136a798a60038316caad9
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203047"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-apps"></a>Criar esquemas de controlo personalizado que monitorizam os fluxos de trabalho ponto-a-ponto no Azure Logic Apps

Há incorporadas que pode ativar para diferentes partes do seu fluxo de trabalho empresa-empresa, como o controlo de AS2 ou X12 mensagens de controlo. Quando criar fluxos de trabalho que inclui uma aplicação lógica, BizTalk Server, SQL Server ou qualquer outra camada, em seguida, pode ativar o controlo personalizado que registra em log eventos desde o início ao fim do fluxo de trabalho. 

Este artigo fornece código personalizado que podem ser usadas nas camadas de fora da sua aplicação lógica. 

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

| Propriedade | Necessário | Tipo | Descrição |
| --- | --- | --- | --- |
| sourceType | Sim |   | Tipo de origem de execução. Valores permitidos são **Microsoft.Logic/workflows** e **custom**. |
| source | Sim |   | Se o tipo de origem for **Microsoft.Logic/workflows**, as informações de origem tem de cumprir esse esquema. Se o tipo de origem for **personalizado**, o esquema é um JToken. |
| systemId | Sim | String | ID de sistema da aplicação lógica. |
| runId | Sim | String | ID de execução da aplicação lógica |
| operationName | Sim | String | Nome da operação (por exemplo, ação ou acionador). |
| repeatItemScopeName | Sim | String | Repetir o nome do item, se a ação está dentro de um `foreach` / `until` loop. |
| repeatItemIndex | Sim | Integer | Se a ação está dentro de um `foreach` / `until` loop. Indica o índice do item repetido. |
| trackingId | Não | String | ID de controlo, para correlacionar mensagens. |
| correlationId | Não | String | ID de correlação, ao correlacionar as mensagens. |
| clientRequestId | Não | String | Cliente pode preenchê-lo para correlacionar mensagens. |
| eventLevel | Sim |   | Nível do evento. |
| eventTime | Sim |   | Hora do evento, no formato AAAA-MM-DDTHH:MM:SS.00000Z da UTC. |
| recordType | Sim |   | Tipo de registro de rastreamento. Permitido é de valor **personalizado**. |
| record | Sim |   | Tipo de registo personalizado. O formato permitido é JToken. |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Esquemas de controlo de protocolo de B2B

Para obter informações sobre esquemas de controlo de protocolo de B2B, consulte:

* [Esquemas de controlo de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Esquemas de controlo de X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [monitorizar mensagens B2B](logic-apps-monitor-b2b-message.md)
* Saiba mais sobre [mensagens B2B no Azure Monitor registos de controlo](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)
