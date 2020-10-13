---
title: Publicar uma oferta - Azure Marketplace
description: API para publicar a oferta especificada.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 60e75aff79913896bdf1dcdc8754b6ecf5620b06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87272050"
---
# <a name="publish-an-offer"></a>Publicar uma oferta

> [!NOTE]
> As APIs do Portal do Parceiro Cloud estão integradas e continuarão a trabalhar no Partner Center. A transição introduz pequenas mudanças. Reveja as alterações listadas no [Cloud Partner Portal API Reference](./cloud-partner-portal-api-overview.md) para garantir que o seu código continua a funcionar após a transição para o Partner Center. As APIs de CPP só devem ser utilizadas para produtos já integrados antes da transição para o Partner Center; novos produtos devem utilizar APIs de submissão do Partner Center.

Inicia o processo de publicação da oferta especificada. Esta chamada é uma operação de longa duração.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parâmetros URI
--------------

|  **Nome**      |    **Descrição**                               |  **Tipo de dados** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Identificador de editor, por exemplo `contoso`      |   Cadeia       |
|  offerId       | Identificar oferta                                 |   Cadeia       |
|  api-version   | Última versão da API                        |   Date         |
|  |  |

## <a name="header"></a>Cabeçalho
------

|  **Nome**        |    **Valor**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Autorização   |  `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Exemplo corporal
------------

### <a name="request"></a>Pedir

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>Solicitar propriedades do corpo

|  **Nome**               |   **Descrição**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  notificações-e-mails    | Lista separada por vírgula de endereços de e-mail a ser notificada do progresso da operação editorial. |
|  |  |

### <a name="response"></a>Resposta

#### <a name="migrated-offers"></a>Ofertas migradas

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Ofertas não migradas

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Cabeçalho de Resposta

|  **Nome**             |    **Valor**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Localização    | O caminho relativo para recuperar o estado desta operação     |
|  |  |

### <a name="response-status-codes"></a>Códigos de estado de resposta

| **Código** |  **Descrição**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` - O pedido foi aceite com sucesso. A resposta contém um local que pode ser usado para rastrear a operação que é lançada. |
| 400   | `Bad/Malformed request` - O organismo de resposta a erros pode fornecer mais informações.                                                               |
| 422   | `Un-processable entity` - Indica que a entidade a publicar falhou na validação.                                                        |
| 404   | `Not found` - A entidade especificada pelo cliente não existe.                                                                              |
|  |  |
