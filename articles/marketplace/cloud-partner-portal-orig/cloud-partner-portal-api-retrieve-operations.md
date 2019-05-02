---
title: Obter operações de API | O Azure Marketplace
description: Obtém todas as operações na oferta de ou para obter uma determinada operação para o operationId especificado.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 1fbcc1d50dbc4488c4123be64e85de612233ccc3
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935783"
---
<a name="retrieve-operations"></a>Obter operações
===================

Obtém todas as operações na oferta de ou para obter uma determinada operação para o operationId especificado. O cliente pode usar parâmetros de consulta para filtrar sobre a execução de operações.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>Parâmetros do URI
--------------

|  **Nome**          |      **Descrição**                                                                                           | **Tipo de dados** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  Identificador do publicador, por exemplo `Contoso`                                                                   |  String       |
|  IDoferta           |  Identificador da oferta                                                                                              |  String       |
|  operationId       |  GUID que identifica exclusivamente a operação sobre a oferta. OperationId poderá ser recuperada por meio desta API e também é devolvido no cabeçalho de HTTP de resposta para qualquer operação de longa execução, tais como o [publicar oferta](./cloud-partner-portal-api-publish-offer.md) API.  |   Guid   |
|  filteredStatus    | Parâmetro de consulta opcional utilizado para filtrar por Estado (por exemplo `running`) na coleção devolvida por esta API.  |   String |
|  versão de API       | Versão mais recente da API                                                                                           |    Date      |
|  |  |  |


<a name="header"></a>Cabeçalho
------

|  **Nome**          |  **Valor**           |
|  ---------------   | -------------------- |
|  Content-Type      | `application/json`   |
|  Autorização     | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Exemplo de corpo
------------

### <a name="response"></a>Resposta

#### <a name="get-operations"></a>OBTER operações

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

#### <a name="get-operation"></a>Operação de obtenção

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
                "notificationEmails": "jondoe@contoso.com"
            } 
        }
    ]
```


### <a name="response-body-properties"></a>Propriedades do corpo de resposta

|  **Nome**                    |  **Descrição**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  ID                          | GUID que identifica exclusivamente a operação                                                       |
|  submissionType              | Identifica o tipo de operação a ser comunicado para a oferta, por exemplo `Publish/GGoLive`      |
|  createdDateTime             | Datetime UTC em que a operação foi criada                                                       |
|  lastActionDateTime          | Datetime UTC quando a última atualização foi realizada na operação                                       |
|  status                      | Estado da operação, qualquer um dos `not started` \| `running` \| `failed` \| `completed`. Apenas uma operação pode ter estado `running` cada vez. |
|  erro                       | Mensagem de erro para operações com falhas                                                               |
|  |  |


### <a name="response-status-codes"></a>Códigos de estado de resposta

| **Código**  |   **Descrição**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK` -O pedido foi processado com êxito e as operações pedidas foram devolvidas.        |
|  400      | `Bad/Malformed request` -O corpo da resposta de erro pode conter mais informações.                    |
|  403      | `Forbidden` -O cliente não tem acesso ao espaço de nomes especificado.                          |
|  404      | `Not found` -A entidade especificada não existe.                                                 |
|  |  |
