---
title: Estado de oferta de recuperação - Azure Marketplace
description: API para recuperar o estado atual da oferta.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 88b07a3456bd2589d6ebefcc54903564e78f4bf2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85516310"
---
# <a name="retrieve-offer-status"></a>Obter estado da oferta

> [!NOTE]
> As APIs do Portal do Parceiro cloud estão integradas no Partner Center e continuarão a funcionar após as suas ofertas serem migradas para o Partner Center. A integração introduz pequenas mudanças. Reveja as alterações listadas no [Cloud Partner Portal API Reference](./cloud-partner-portal-api-overview.md) para garantir que o seu código continua a funcionar após a migração para o Partner Center.

Recupera o estado atual da oferta.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parâmetros URI

|  **Nome**       |   **Descrição**                            |  **Tipo de dados** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | Identificador de editor, por exemplo`Contoso`  |     String     |
|  offerId        | GUID que identifica exclusivamente a oferta      |     String     |
|  api-version    | Última versão da API                        |     Data       |
|  |  |


## <a name="header"></a>Cabeçalho


|  Name           |  Valor               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  Autorização  | `Bearer YOUR_TOKEN`  |
|  |  |

## <a name="body-example"></a>Exemplo corporal


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
  }
```


### <a name="response-body-properties"></a>Propriedades do corpo de resposta

|  **Nome**             |    **Descrição**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | O estado da oferta. Para obter a lista de valores possíveis, consulte [o estado da Oferta](#offer-status) abaixo. |
|  mensagens             | Conjunto de mensagens associadas à oferta                                                    |
|  passos                | Matriz dos passos que a oferta passa durante uma publicação de oferta                      |
|  Quadrotime estimado   | Estimativa do tempo que levaria para completar este passo, em formato amigável                       |
|  ID                   | Identificador do degrau                                                                         |
|  stepName             | Nome do degrau                                                                               |
|  descrição          | Descrição do passo                                                                        |
|  status               | Estado do passo. Para obter a lista de valores possíveis, consulte [o estado do passo](#step-status) abaixo.    |
|  mensagens             | Conjunto de mensagens relacionadas com o passo                                                          |
|  análise de processos    | Conclusão percentual do passo                                                              |
|  previewLinks         | *Não atualmente implementado*                                                                    |
|  liveLinks            | *Não atualmente implementado*                                                                    |
|  notificaçõesEmails   | Precedida por ofertas migradas para o Partner Center. Os e-mails de notificação para ofertas migradas serão enviados para o e-mail especificado nas definições de conta do Vendedor.<br><br>Para ofertas não migradas, lista separada por vírgula de endereços de e-mail a notificar do progresso da operação        |
|  |  |

### <a name="response-status-codes"></a>Códigos de estado de resposta

| **Código** |   **Descrição**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK`- O pedido foi processado com sucesso e o estado atual da oferta foi devolvido. |
|  400     | `Bad/Malformed request`- O corpo de resposta a erros pode conter mais informações.                 |
|  404     | `Not found`- A entidade especificada não existe.                                                |
|  |  |

### <a name="offer-status"></a>Estado da oferta

|  **Nome**                    |    **Descrição**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  Nunca publicado              | A oferta nunca foi publicada.                          |
|  Não Começou                  | A oferta é nova e não começou.                            |
|  WaitingForPublisherReview   | A oferta está à espera da aprovação dos editores.                 |
|  A executar                     | A submissão da oferta está a ser processada.                     |
|  Bem-sucedido                   | A submissão da oferta completou o processamento.               |
|  Cancelado                    | A submissão da oferta foi cancelada.                           |
|  Falhou                      | A submissão da oferta falhou.                                 |
|  |  |

### <a name="step-status"></a>Estatuto do Passo

|  **Nome**                    |    **Descrição**                           |
|  -------------------------   |  ------------------------------------------  |
|  Não Começou                  | O passo ainda não começou.                        |
|  InProgress                  | O passo está a correr.                             |
|  WaitingForPublisherReview   | O Step está à espera da aprovação dos editores.      |
|  WaitingForApproval          | O Passo está à espera da aprovação do processo.        |
|  Bloqueado                     | O degrau está bloqueado.                             |
|  Rejeitado                    | Passo é rejeitado.                            |
|  Concluído                    | O passo está completo.                            |
|  Cancelado                    | O passo foi cancelado.                           |
|  |  |
