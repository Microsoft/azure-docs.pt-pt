---
title: Recuperar estado de oferta [ Recuperar estatuto de oferta ] Mercado Azure
description: A API recupera o estado atual da oferta.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 2f5211716145d6c05bbfb0132c4a6ba2f9cceabe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280512"
---
<a name="retrieve-offer-status"></a>Obter estado da oferta 
=====================

Recupera o estado atual da oferta.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

<a name="uri-parameters"></a>Parâmetros URI
--------------

|  **Nome**       |   **Descrição**                            |  **Tipo de dados** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  editorId    | Identificador de editor, por exemplo`Contoso`  |     Cadeia     |
|  offerId        | GUID que identifica exclusivamente a oferta      |     Cadeia     |
|  api-version    | Versão mais recente da API                        |     Date       |
|  |  |


<a name="header"></a>Cabeçalho
------

|  Nome           |  Valor               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  Autorização  | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Exemplo do corpo
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
|  status               | O estado da oferta. Para a lista de valores possíveis, consulte o [estado da Oferta](#offer-status) abaixo. |
|  mensagens             | Conjunto de mensagens associadas à oferta                                                    |
|  passos                | Conjunto dos passos que a oferta passa durante uma publicação de oferta                      |
|  calculou timeframe   | Estimativa do tempo que levaria para completar este passo, em formato amigável                       |
|  ID                   | Identificador do degrau                                                                         |
|  nome do passo             | Nome do passo                                                                               |
|  descrição          | Descrição do passo                                                                        |
|  status               | Estado do passo. Para a lista de valores possíveis, consulte o [estado do Step](#step-status) abaixo.    |
|  mensagens             | Conjunto de mensagens relacionadas com o passo                                                          |
|  processoPercentage    | Conclusão percentual do passo                                                              |
|  pré-visualizaçãoLinks         | *Não atualmente implementado*                                                                    |
|  liveLinks            | *Não atualmente implementado*                                                                    |
|  notificaçõesEmails   | Lista separada da Comma de endereços de e-mail a ser notificado do progresso da operação        |
|  |  |


### <a name="response-status-codes"></a>Códigos de estado de resposta

| **Código** |   **Descrição**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK`- O pedido foi processado com êxito e o estado atual da oferta foi devolvido. |
|  400     | `Bad/Malformed request`- O corpo de resposta a erros pode conter mais informações.                 |
|  404     | `Not found`- A entidade especificada não existe.                                                |
|  |  |


### <a name="offer-status"></a>Estado da oferta

|  **Nome**                    |    **Descrição**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  Nunca Publicado              | A oferta nunca foi publicada.                          |
|  Não Iniciado                  | A oferta é nova e não começou.                            |
|  WaitingForPublisherReview   | A oferta está à espera da aprovação do editor.                 |
|  A executar                     | A submissão da oferta está a ser processada.                     |
|  Bem-sucedido                   | A submissão da oferta concluiu o processamento.               |
|  Cancelado                    | A submissão da oferta foi cancelada.                           |
|  Falhou                      | A submissão da oferta falhou.                                 |
|  |  |


### <a name="step-status"></a>Estado do Passo

|  **Nome**                    |    **Descrição**                           |
|  -------------------------   |  ------------------------------------------  |
|  Não Iniciado                  | O passo ainda não começou.                        |
|  Inprogress                  | O passo está a correr.                             |
|  WaitingForPublisherReview   | O Step está à espera da aprovação da editora.      |
|  Esperando Aprovação          | O passo está à espera da aprovação do processo.        |
|  Bloqueado                     | O passo está bloqueado.                             |
|  Rejected                    | O passo é rejeitado.                            |
|  Concluída                    | O passo está completo.                            |
|  Cancelado                    | O passo foi cancelado.                           |
|  |  |
