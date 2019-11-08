---
title: Recuperar API de ofertas | Azure Marketplace
description: A API recupera uma lista resumida de ofertas em um namespace do Publicador.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 5c94c03a63936be2b086085a1e52064dedf214b0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819634"
---
<a name="retrieve-offers"></a>Obter ofertas
===============

Recupera uma lista resumida de ofertas em um namespace do Publicador.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`


<a name="uri-parameters"></a>Parâmetros de URI
--------------

| **Nome**         |  **Descrição**                         |  **Tipo de dados** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Identificador de editor, por exemplo `contoso` |   String    |
|  versão da API     | Versão mais recente da API                    |    Date        |
|  |  |


<a name="header"></a>Cabeçalho
------

|  **Nome**        |         **Valor**       |
|  --------------- |       ----------------  |
|  Tipo de conteúdo    | `application/json`      |
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

### <a name="response-body-properties"></a>Propriedades do corpo da resposta

|  **Nome**       |       **Descrição**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | Identifica o tipo de oferta                                                                                           |
|  publisherId    | Identificador que identifica exclusivamente o Publicador                                                                      |
|  status         | Status da oferta. Para obter a lista de valores possíveis, consulte o [status da oferta](#offer-status) abaixo.                         |
|  ID             | GUID que identifica exclusivamente a oferta no namespace do Publicador.                                                    |
|  versão        | Versão atual da oferta. A Propriedade Version não pode ser modificada pelo cliente. Ele é incrementado após cada publicação. |
|  Defini     | Contém uma exibição resumida da definição real da carga de trabalho. Para obter uma definição detalhada, use a API da [oferta específica de recuperação](./cloud-partner-portal-api-retrieve-specific-offer.md) . |
|  alteradotime    | Hora UTC em que a oferta foi modificada pela última vez                                                                              |
|  |  |


### <a name="response-status-codes"></a>Códigos de status de resposta

| **Auto-completar**  |  **Descrição**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK`-a solicitação foi processada com êxito e todas as ofertas no Publicador foram retornadas ao cliente.  |
|  400      | `Bad/Malformed request`-o corpo da resposta de erro pode conter mais informações.                                    |
|  403      | `Forbidden`-o cliente não tem acesso ao namespace especificado.                                          |
|  404      | `Not found`-a entidade especificada não existe.                                                                 |
|  |  |


### <a name="offer-status"></a>Status da oferta

|  **Nome**                    | **Descrição**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | A oferta nunca foi publicada.                  |
|  NotStarted                  | A oferta é nova, mas não iniciada.                 |
|  WaitingForPublisherReview   | A oferta está aguardando a aprovação do Publicador.         |
|  A executar                     | O envio da oferta está sendo processado.             |
|  Bem-sucedido                   | O envio da oferta concluiu o processamento.       |
|  Cancelado                    | O envio da oferta foi cancelado.                   |
|  Com Falhas                      | Falha no envio da oferta.                         |
|  |  |
