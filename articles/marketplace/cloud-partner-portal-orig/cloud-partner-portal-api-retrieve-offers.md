---
title: Recuperar oferece API / Mercado Azure
description: A API recupera uma lista resumida de ofertas sob um espaço de nome de editor.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 0413bc71c113bf1eef9f761dbeed4c0d0afe735c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255962"
---
<a name="retrieve-offers"></a>Obter ofertas
===============

> [!NOTE]
> As APIs do Portal do Parceiro cloud estão integradas no Partner Center e continuarão a trabalhar após as suas ofertas serem migradas para o Partner Center. A integração introduz pequenas alterações. Reveja as alterações listadas no [Portal do Parceiro cloud Referência API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) para garantir que o seu código continua a funcionar após a migração para partner Center.

Recupera uma lista resumida de ofertas sob um espaço de nome de editor.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`

<a name="uri-parameters"></a>Parâmetros URI
--------------

| **Nome**         |  **Descrição**                         |  **Tipo de dados** |
| -------------    |  ------------------------------------    |  -----------   |
|  editorId     | Identificador de editor, por exemplo`contoso` |   String    |
|  api-version     | Versão mais recente da API                    |    Date        |
|  |  |


<a name="header"></a>Cabeçalho
------

|  **Nome**        |         **Valor**       |
|  --------------- |       ----------------  |
|  Content-Type    | `application/json`      |
|  Autorização   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>Exemplo do corpo
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
|  ofertaTypeId    | Identifica o tipo de oferta                                                                                           |
|  editorId    | Identificador que identifica exclusivamente a editora                                                                      |
|  status         | Estado da oferta. Para a lista de valores possíveis, consulte o [estado da Oferta](#offer-status) abaixo.                         |
|  ID             | GUID que identifica exclusivamente a oferta no espaço de nome da editora.                                                    |
|  versão        | Versão atual da oferta. A propriedade da versão não pode ser modificada pelo cliente. É incrementado após cada publicação. |
|  definição     | Contém uma visão resumida da definição real da carga de trabalho. Para obter uma definição detalhada, use a [Oferta API específica do Recuperar.](./cloud-partner-portal-api-retrieve-specific-offer.md) |
|  mudouTempo    | Utc tempo quando a oferta foi modificada pela última vez                                                                              |
|  |  |


### <a name="response-status-codes"></a>Códigos de estado de resposta

| **Código**  |  **Descrição**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK`- O pedido foi processado com sucesso e todas as ofertas sob a editora foram devolvidas ao cliente.  |
|  400      | `Bad/Malformed request`- O corpo de resposta a erros pode conter mais informações.                                    |
|  403      | `Forbidden`- O cliente não tem acesso ao espaço de nome especificado.                                          |
|  404      | `Not found`- A entidade especificada não existe.                                                                 |
|  |  |


### <a name="offer-status"></a>Estado da Oferta

|  **Nome**                    | **Descrição**                                  |
|  ------------------------    | -----------------------------------------------  |
|  Nunca Publicado              | A oferta nunca foi publicada.                  |
|  Não Iniciado                  | A oferta é nova, mas não começou.                 |
|  WaitingForPublisherReview   | A oferta está à espera da aprovação do editor.         |
|  A executar                     | A submissão da oferta está a ser processada.             |
|  Bem-sucedido                   | A submissão da oferta concluiu o processamento.       |
|  Cancelado                    | A submissão da oferta foi cancelada.                   |
|  Falhou                      | A submissão da oferta falhou.                         |
|  |  |
