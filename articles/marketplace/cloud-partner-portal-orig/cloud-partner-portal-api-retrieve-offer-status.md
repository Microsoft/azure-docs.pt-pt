---
title: Obter o estado da oferta | Documentos da Microsoft
description: API obtém o estado atual da oferta.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 9233a5919ad86adcbb7947cd095945654ed015a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61093986"
---
<a name="retrieve-offer-status"></a>Obter estado da oferta 
=====================

Obtém o estado atual da oferta.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

<a name="uri-parameters"></a>Parâmetros do URI
--------------

|  **Nome**       |   **Descrição**                            |  **Tipo de dados** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | Identificador do publicador, por exemplo `Contoso`  |     String     |
|  IDoferta        | GUID que identifica exclusivamente a oferta      |     String     |
|  versão de API    | Versão mais recente da API                        |     Date       |
|  |  |


<a name="header"></a>Cabeçalho
------

|  Name           |  Value               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  Autorização  | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Exemplo de corpo
------------

### <a name="response"></a>Resposta

``` json
  {
      "status": "succeeded",
      "messages": [],
      "steps": [
      {
          "estimatedTimeFrame": "< 15 min",
          "id": "displaydummycertify",
          "stepName": "Validate Pre-Requisites",
          "description": "Offer settings provided are validated.",
          "status": "complete",
          "messages": [
              {
                  "messageHtml": "Step completed.",
                  "level": "information",
                  "timestamp": "2018-03-16T17:50:45.7215661Z"
              }
          ],       
          "progressPercentage": 100
      },
      {
          "estimatedTimeFrame": "~2-3 days",
          "id": "displaycertify",
          "stepName": "Certification",
          "description": "Your offer is analyzed by our certification systems for issues.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 day",
          "id": "displayprovision",
          "stepName": "Provisioning",
          "description": "Your virtual machine is being replicated in our production systems.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "displaypackage",
          "stepName": "Packaging and Lead Generation Registration",
          "description": "Your virtual machine is being packaged for customers. Additionally, lead systems are being configured and set up.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "publisher-signoff",
          "stepName": "Publisher signoff",
          "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "~2-5 days",
          "id": "live",
          "stepName": "Live",
          "description": "Offer is publicly visible and is available for purchase.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      }
      ],
      "previewLinks": [],
      liveLinks": [],
      "notificationEmails": "jdoe@contoso.com"
  } 
```


### <a name="response-body-properties"></a>Propriedades do corpo de resposta

|  **Nome**             |    **Descrição**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | O estado da oferta. Para obter a lista de valores possíveis, consulte [stav Nabídky](#offer-status) abaixo. |
|  mensagens             | Matriz de mensagens associadas à oferta                                                    |
|  passos                | Matriz dos passos que a oferta atravessa o limite de tempo durante a publicação de uma oferta                      |
|  estimatedTimeFrame   | Estimativa de tempo que é necessário para concluir este passo, em formato amigável                       |
|  ID                   | Identificador do passo                                                                         |
|  stepName             | Nome do passo                                                                               |
|  description          | Descrição do passo                                                                        |
|  status               | Estado do passo. Para obter a lista de valores possíveis, consulte [passo estado](#step-status) abaixo.    |
|  mensagens             | Matriz de mensagens relacionadas para o passo                                                          |
|  processPercentage    | Porcentagem de conclusão do passo                                                              |
|  previewLinks         | *Atualmente não implementado*                                                                    |
|  liveLinks            | *Atualmente não implementado*                                                                    |
|  notificationEmails   | Lista separada por vírgulas de endereços de e-mail para ser notificado sobre o progresso da operação        |
|  |  |


### <a name="response-status-codes"></a>Códigos de estado de resposta

| **Código** |   **Descrição**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK` -O pedido foi processado com êxito e o estado atual da oferta foi devolvido. |
|  400     | `Bad/Malformed request` -O corpo da resposta de erro pode conter mais informações.                 |
|  404     | `Not found` -A entidade especificada não existe.                                                |
|  |  |


### <a name="offer-status"></a>Stav nabídky

|  **Nome**                    |    **Descrição**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | Oferta nunca foi publicada.                          |
|  NotStarted                  | A oferta é novo e não iniciado.                            |
|  WaitingForPublisherReview   | Oferta está à espera de aprovação do publicador.                 |
|  A executar                     | Submissão de oferta está a ser processado.                     |
|  Bem-sucedido                   | Submissão de oferta terminou de processar.               |
|  Cancelado                    | Submissão de oferta foi cancelada.                           |
|  Com Falhas                      | Falha na submissão de oferta.                                 |
|  |  |


### <a name="step-status"></a>Estado de passo

|  **Nome**                    |    **Descrição**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | Passo não foi iniciado.                        |
|  Em Curso                  | Passo está em execução.                             |
|  WaitingForPublisherReview   | Passo está à espera de aprovação do publicador.      |
|  WaitingForApproval          | Passo está à espera de aprovação de processo.        |
|  Bloqueado                     | Passo está bloqueado.                             |
|  Rejeitado                    | Passo é rejeitado.                            |
|  Concluir                    | Passo está concluído.                            |
|  Cancelado                    | Passo foi cancelado.                           |
|  |  |
