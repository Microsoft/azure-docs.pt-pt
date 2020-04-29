---
title: Recuperar operações API [ Mercado Azure
description: Recupera todas as operações da oferta ou para obter uma determinada operação para a operação especificadaId.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 93b2ca700a987b86aedfdae55d58540c8ffe84ed
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255878"
---
# <a name="retrieve-operations"></a>Obter operações

> [!NOTE]
> As APIs do Portal do Parceiro cloud estão integradas no Partner Center e continuarão a trabalhar após as suas ofertas serem migradas para o Partner Center. A integração introduz pequenas alterações. Reveja as alterações listadas no [Portal do Parceiro cloud Referência API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) para garantir que o seu código continua a funcionar após a migração para partner Center.

Recupera todas as operações da oferta ou para obter uma determinada operação para a operação especificadaId. O cliente pode utilizar parâmetros de consulta para filtrar as operações de funcionamento.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


## <a name="uri-parameters"></a>Parâmetros URI

|  **Nome**          |      **Descrição**                                                                                           | **Tipo de dados** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  editorId       |  Identificador de editor, por exemplo`Contoso`                                                                   |  String       |
|  offerId           |  Identificador de oferta                                                                                              |  String       |
|  operationId       |  GUID que identifica exclusivamente a operação na oferta. O operationId pode ser recuperado utilizando esta API, e também é devolvido no cabeçalho HTTP da resposta para qualquer operação de longo prazo, como a [oferta de Publicação](./cloud-partner-portal-api-publish-offer.md) API.  |   GUID   |
|  api-version       | Versão mais recente da API |    Date      |
|  |  |  |

## <a name="header"></a>Cabeçalho


|  **Nome**          |  **Valor**           |
|  ---------------   | -------------------- |
|  Content-Type      | `application/json`   |
|  Autorização     | `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Exemplo do corpo

### <a name="response"></a>Resposta

#### <a name="get-operations"></a>Operações get

``` json
    [
        {
            "id": "5a63deb5-925b-4ee0-938b-7c86fbf287c5",
            "offerId": "56615b67-2185-49fe-80d2-c4ddf77bb2e8",
            "offerVersion": 1,
            "offerTypeId": "microsoft-azure-virtualmachines",
            "publisherId": "contoso",
            "submissionType": "publish",
            "submissionState": "running",
            "publishingVersion": 2,
            "slot": "staging",
            "version": 636576975611768314,
            "definition": {
                "metadata": {
                    "emails": "jdoe@contoso.com"
                }
            },
            "changedTime": "2018-03-26T21:46:01.179948Z"
        }
    ]
```

#### <a name="get-operation"></a>Operação GET

``` json
    [
        {
            "status" : "running",
            "messages" : [],
            "publishingVersion" : 2,
            "offerVersion" : 1,
            "cancellationRequestState": "canCancel",
            "steps": [
                        {
                            "estimatedTimeFrame": "< 15 min",
                            "id": "displaydummycertify",
                            "stepName": "Validate Pre-Requisites",
                            "description": "Offer settings provided are validated",
                            "status": "complete",
                            "messages": 
                            [
                                {
                                    "messageHtml": "Step completed.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:36.500052Z"
                                }
                            ],
                            "progressPercentage": 100
                        },
                        {
                            "estimatedTimeFrame": "< 5 day",
                            "id": "displaycertify",
                            "stepName": "Certification",
                            "description": "Your offer is analyzed by our certification systems for issues.",
                            "status": "blocked",
                            "messages": 
                            [
                                {
                                    "messageHtml": "No virtual machine image was found for the plan contoso.",
                                    "level": "error",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                },
                                {
                                    "messageHtml": "This step has not started yet.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                }
                            ],
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
                            "description": "Your virtual machine is packaged for being shown to your customers. Additionally, we hookup our lead generation systems to send leads for your offer.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "id": "publisher-signoff",
                            "stepName": "Publisher signoff",
                            "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "~2-5 days",
                            "id": "live",
                            "stepName": "Live",
                            "description": "Offer is publicly visible and is available for purchase.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        }
                    ],
                "previewLinks": [],
                "liveLinks": [],
            }
        }
    ]
```

### <a name="response-body-properties"></a>Propriedades do corpo de resposta

|  **Nome**                    |  **Descrição**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  ID                          | GUID que identifica exclusivamente a operação                                                       |
|  submissãoTipo              | Identifica o tipo de operação que está a ser reportada para a oferta, por exemplo`Publish/GoLive`      |
|  criadoDateTime             | UTC data de data quando a operação foi criada                                                       |
|  lastActionDateTime          | UtC data de data em que a última atualização foi feita sobre a operação                                       |
|  status                      | Estado da operação, `not started` \| `running` \| `failed` \| `completed`também. Só uma operação `running` pode ter estatuto de cada vez. |
|  erro                       | Mensagem de erro para operações falhadas                                                               |
|  |  |

### <a name="response-step-properties"></a>Propriedades de passo de resposta

|  **Nome**                    |  **Descrição**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
| calculou timeframe | A duração estimada desta operação |
| ID | O identificador único para o processo de etapa |
| descrição | Descrição do passo |
| nome do passo | O nome amigável para o passo |
| status | O estado do passo, ou `notStarted` \| `running` \| `failed` \|`completed` |
| mensagens | Quaisquer notificações ou avisos encontrados durante o passo. Conjunto de cordas |
| progressPercentage | Um inteiro de 0 a 100 indicando a progressão do degrau |
| | |

### <a name="response-status-codes"></a>Códigos de estado de resposta

| **Código**  |   **Descrição**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK`- O pedido foi processado com êxito e a operação(s) solicitada foi devolvida.        |
|  400      | `Bad/Malformed request`- O corpo de resposta a erros pode conter mais informações.                    |
|  403      | `Forbidden`- O cliente não tem acesso ao espaço de nome especificado.                          |
|  404      | `Not found`- A entidade especificada não existe.                                                 |
|  |  |
