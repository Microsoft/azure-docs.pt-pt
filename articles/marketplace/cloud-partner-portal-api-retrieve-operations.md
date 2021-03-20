---
title: Recuperar operações API - Azure Marketplace
description: API para recuperar todas as operações da oferta ou para obter uma operação específica para a operação EspecificadaId.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: cb44d977407a7e854603e6bbacf3591752b109c2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87271948"
---
# <a name="retrieve-operations"></a>Obter operações

> [!NOTE]
> As APIs do Portal do Parceiro Cloud estão integradas e continuarão a trabalhar no Partner Center. A transição introduz pequenas mudanças. Reveja as alterações listadas no [Cloud Partner Portal API Reference](./cloud-partner-portal-api-overview.md) para garantir que o seu código continua a funcionar após a transição para o Partner Center. As APIs de CPP só devem ser utilizadas para produtos já integrados antes da transição para o Partner Center; novos produtos devem utilizar APIs de submissão do Partner Center.

Recupera todas as operações da oferta ou para obter uma operação específica para a operaçãoId especificada. O cliente pode utilizar parâmetros de consulta para filtrar as operações de funcionamento.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```

## <a name="uri-parameters"></a>Parâmetros URI

|  **Nome**          |      **Descrição**                                                                                           | **Tipo de dados** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  Identificador de editor, por exemplo `Contoso`                                                                   |  String       |
|  offerId           |  Identificar oferta                                                                                              |  String       |
|  operationId       |  GUID que identifica exclusivamente a operação na oferta. A operaçãoId pode ser recuperada utilizando esta API, e também é devolvida no cabeçalho HTTP da resposta para qualquer operação de longa duração, como a [Oferta Editorial](./cloud-partner-portal-api-publish-offer.md) API.  |   GUID   |
|  api-version       | Última versão da API |    Data      |
|  |  |  |

## <a name="header"></a>Cabeçalho

|  **Nome**          |  **Valor**           |
|  ---------------   | -------------------- |
|  Content-Type      | `application/json`   |
|  Autorização     | `Bearer YOUR_TOKEN`  |
|  |  |

## <a name="body-example"></a>Exemplo corporal

### <a name="response"></a>Resposta

#### <a name="get-operations"></a>Operações GET

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
|  submissãoType              | Identifica o tipo de operação que está a ser reportada para a oferta, por exemplo `Publish/GoLive`      |
|  createdDateTime             | Data da UTC quando a operação foi criada                                                       |
|  últimaActionDateTime          | Data da UTC quando a última atualização foi feita sobre a operação                                       |
|  status                      | Estado da operação, também `not started` \| `running` \| `failed` \| `completed` . . Só uma operação pode ter estatuto `running` de cada vez. |
|  erro                       | Mensagem de erro para operações falhadas                                                               |
|  |  |

### <a name="response-step-properties"></a>Propriedades de passo de resposta

|  **Nome**                    |  **Descrição**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
| Quadrotime estimado | A duração estimada desta operação |
| ID | O identificador único para o processo de passo |
| descrição | Descrição do passo |
| stepName | O nome amigável para o passo |
| status | O estado do passo, ou `notStarted` \| `running` \| `failed` \|`completed` |
| mensagens | Quaisquer notificações ou avisos encontrados durante o passo. Matriz de cordas |
| progressPercentage | Um inteiro de 0 a 100 indicando a progressão do passo |
| | |

### <a name="response-status-codes"></a>Códigos de estado de resposta

| **Código**  |   **Descrição**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK` - O pedido foi processado com sucesso e as operações solicitadas foram devolvidas.        |
|  400      | `Bad/Malformed request` - O corpo de resposta a erros pode conter mais informações.                    |
|  403      | `Forbidden` - O cliente não tem acesso ao espaço de nome especificado.                          |
|  404      | `Not found` - A entidade especificada não existe.                                                 |
|  |  |
