---
title: Obter oferece API | Documentos da Microsoft
description: API obtém uma lista resumida dos ofertas num espaço de nomes do publicador.
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
ms.openlocfilehash: 3429917fcee520ae932253f1fdfead4ffb6535e6
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810239"
---
<a name="retrieve-offers"></a>Obter oferece
===============

Obtém uma lista resumida dos ofertas num espaço de nomes do publicador.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`


<a name="uri-parameters"></a>Parâmetros do URI
--------------

| * * Nome * *        |  **Descrição**                         |  **Tipo de dados** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Identificador do publicador, por exemplo `contoso` |   Cadeia    |
|  versão de API     | Versão mais recente da API                    |    Date        |
|  |  |


<a name="header"></a>Cabeçalho
------

|  **Nome**        |         **Valor**       |
|  --------------- |       ----------------  |
|  Content-Type    | `application/json`      |
|  Autorização   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>Exemplo de corpo
------------

### <a name="response"></a>Resposta

``` json
  200 OK 
  [ 
      {  
          "offerTypeId": "microsoft-azure-virtualmachines",
          "publisherId": "contoso",
          "status": "published",
          "id": "059afc24-07de-4126-b004-4e42a51816fe",
          "version": 1,
          "definition": {
              "displayText": "Contoso Virtual Machine"
          },
          "changedTime":"2017-05-23T23:33:47.8802283Z"
      }
  ]
```

### <a name="response-body-properties"></a>Propriedades do corpo de resposta

|  **Nome**       |       **Descrição**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | Identifica o tipo de oferta                                                                                           |
|  publisherId    | Identificador que identifica exclusivamente o publicador                                                                      |
|  status         | Estado da oferta. Para obter a lista de valores possíveis, consulte [stav Nabídky](#offer-status) abaixo.                         |
|  ID             | GUID que identifica exclusivamente a oferta no espaço de nomes do publicador.                                                    |
|  version        | Versão atual da oferta. A propriedade de versão não pode ser modificada pelo cliente. Ele é incrementado após cada publicação. |
|  definição     | Contém uma exibição resumida da definição real da carga de trabalho. Para obter uma definição detalhada, utilize a [obter oferta específica] (. / cloud-partner-portal-api-retrieve-specific-offer.md) API. |
|  changedTime    | Hora UTC, que a oferta foi modificado pela última vez                                                                              |
|  |  |


### <a name="response-status-codes"></a>Códigos de estado de resposta

| **Código**  |  **Descrição**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` -O pedido foi processado com êxito e todas as ofertas sob o publicador foram devolvidas ao cliente.  |
|  400      | `Bad/Malformed request` -O corpo da resposta de erro pode conter mais informações.                                    |
|  403      | `Forbidden` -O cliente não tem acesso ao espaço de nomes especificado.                                          |
|  404      | `Not found` -A entidade especificada não existe.                                                                 |
|  |  |


### <a name="offer-status"></a>Stav nabídky

|  **Nome**                    | **Descrição**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | Oferta nunca foi publicada.                  |
|  NotStarted                  | Oferta é nova, mas não foi iniciada.                 |
|  WaitingForPublisherReview   | Oferta está à espera de aprovação do publicador.         |
|  A executar                     | Submissão de oferta está a ser processado.             |
|  Bem-sucedido                   | Submissão de oferta terminou de processar.       |
|  Cancelado                    | Submissão de oferta foi cancelada.                   |
|  Com Falhas                      | Falha na submissão de oferta.                         |
|  |  |
