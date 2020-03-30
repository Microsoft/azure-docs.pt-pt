---
title: Publique uma oferta [ Mercado Azure
description: API para publicar a oferta especificada.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 4163bf5727c327d559b81db42f99684aa0cc8d5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280529"
---
<a name="publish-an-offer"></a>Publicar uma oferta
================

Inicia o processo de publicação para a oferta especificada. Esta chamada é uma operação de longa duração.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>Parâmetros URI
--------------

|  **Nome**      |    **Descrição**                               |  **Tipo de dados** |
|  ------------- |  ------------------------------------            |   -----------  |
|  editorId   | Identificador de editor, por exemplo`contoso`      |   Cadeia       |
|  offerId       | Identificador de oferta                                 |   Cadeia       |
|  api-version   | Versão mais recente da API                        |   Date         |
|  |  |


<a name="header"></a>Cabeçalho
------

|  **Nome**        |    **Valor**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Autorização   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Exemplo do corpo
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

### <a name="request-body-properties"></a>Solicitar propriedades corporais

|  **Nome**               |   **Descrição**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  e-mails de notificação    | Lista separada da Comma de endereços de e-mail a ser notificado do progresso da operação editorial. |
|  |  |


### <a name="response"></a>Resposta

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Cabeçalho de Resposta

|  **Nome**             |    **Valor**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Localização da Operação    | URL que pode ser consultado para determinar o estado atual da operação.    |
|  |  |


### <a name="response-status-codes"></a>Códigos de estado de resposta

| **Código** |  **Descrição**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`- O pedido foi aceite com sucesso. A resposta contém um local que pode ser usado para acompanhar a operação que é lançada. |
| 400   | `Bad/Malformed request`- O organismo de resposta a erros pode fornecer mais informações.                                                               |
| 422   | `Un-processable entity`- Indica que a entidade a publicar não foi validada.                                                        |
| 404   | `Not found`- A entidade especificada pelo cliente não existe.                                                                              |
|  |  |
