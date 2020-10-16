---
title: A Retrieve oferece API - Azure Marketplace
description: API para recuperar uma lista resumida de ofertas sob um espaço de nome de editor.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 39d07751c708d5555799ecbb3b3bc66d3f44f43a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87271965"
---
# <a name="retrieve-offers"></a>Obter ofertas

> [!NOTE]
> As APIs do Portal do Parceiro Cloud estão integradas e continuarão a trabalhar no Partner Center. A transição introduz pequenas mudanças. Reveja as alterações listadas no [Cloud Partner Portal API Reference](./cloud-partner-portal-api-overview.md) para garantir que o seu código continua a funcionar após a transição para o Partner Center. As APIs de CPP só devem ser utilizadas para produtos já integrados antes da transição para o Partner Center; novos produtos devem utilizar APIs de submissão do Partner Center.

Recupera uma lista resumida de ofertas sob um espaço de nome de editor.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parâmetros URI

| **Nome**         |  **Descrição**                         |  **Tipo de dados** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Identificador de editor, por exemplo `contoso` |   Cadeia    |
|  api-version     | Última versão da API                    |    Date        |
|  |  |

## <a name="header"></a>Cabeçalho

|  **Nome**        |         **Valor**       |
|  --------------- |       ----------------  |
|  Content-Type    | `application/json`      |
|  Autorização   | `Bearer YOUR_TOKEN`     |
|  |  |

## <a name="body-example"></a>Exemplo corporal

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
|  publisherId    | Identificador que identifica exclusivamente a editora                                                                      |
|  status         | Estado da oferta. Para obter a lista de valores possíveis, consulte [o estado da Oferta](#offer-status) abaixo.                         |
|  ID             | GUID que identifica exclusivamente a oferta no espaço de nome da editora.                                                    |
|  versão        | Versão atual da oferta. A propriedade da versão não pode ser modificada pelo cliente. É incrementado após cada publicação. |
|  definição     | Contém uma visão resumida da definição real da carga de trabalho. Para obter uma definição detalhada, use a [oferta específica de API.](./cloud-partner-portal-api-retrieve-specific-offer.md) |
|  mudou de tempo    | Hora utc quando a oferta foi modificada pela última vez                                                                              |
|  |  |

### <a name="response-status-codes"></a>Códigos de estado de resposta

| **Código**  |  **Descrição**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` - O pedido foi processado com sucesso e todas as ofertas sob a editora foram devolvidas ao cliente.  |
|  400      | `Bad/Malformed request` - O corpo de resposta a erros pode conter mais informações.                                    |
|  403      | `Forbidden` - O cliente não tem acesso ao espaço de nome especificado.                                          |
|  404      | `Not found` - A entidade especificada não existe.                                                                 |
|  |  |

### <a name="offer-status"></a>Estado da Oferta

|  **Nome**                    | **Descrição**                                  |
|  ------------------------    | -----------------------------------------------  |
|  Nunca publicado              | A oferta nunca foi publicada.                  |
|  Não Começou                  | A oferta é nova, mas ainda não começou.                 |
|  WaitingForPublisherReview   | A oferta está à espera da aprovação dos editores.         |
|  Em Execução                     | A submissão da oferta está a ser processada.             |
|  Com êxito                   | A submissão da oferta completou o processamento.       |
|  Cancelado                    | A submissão da oferta foi cancelada.                   |
|  Com falhas                      | A submissão da oferta falhou.                         |
|  |  |
