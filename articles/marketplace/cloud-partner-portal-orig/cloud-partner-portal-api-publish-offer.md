---
title: Publicar uma oferta | O Azure Marketplace
description: API para publicar a oferta especificada.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 117a4e5e238e754524ff813ce25ebc1105e2153c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64934981"
---
<a name="publish-an-offer"></a>Publicar uma oferta
================

Inicia o processo de publicação para a oferta especificado. Esta chamada é uma operação de longa execução.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>Parâmetros do URI
--------------

|  **Nome**      |    **Descrição**                               |  **Tipo de dados** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Identificador do publicador, por exemplo `contoso`      |   String       |
|  IDoferta       | Identificador da oferta                                 |   String       |
|  versão de API   | Versão mais recente da API                        |   Date         |
|  |  |


<a name="header"></a>Cabeçalho
------

|  **Nome**        |    **Valor**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Autorização   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Exemplo de corpo
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

### <a name="request-body-properties"></a>Propriedades do corpo do pedido

|  **Nome**               |   **Descrição**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  e-mails de notificação    | Lista separada por vírgulas de endereços de e-mail para ser notificado sobre o progresso da operação de publicação. |
|  |  |


### <a name="response"></a>Resposta

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Cabeçalho de Resposta

|  **Nome**             |    **Valor**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Operação de localização    | URL que pode ser consultado para determinar o estado atual da operação.    |
|  |  |


### <a name="response-status-codes"></a>Códigos de estado de resposta

| **Código** |  **Descrição**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` -O pedido foi aceite a com êxito. A resposta contém uma localização que pode ser utilizada para controlar a operação que é iniciada. |
| 400   | `Bad/Malformed request` -O corpo da resposta de erro pode fornecer mais informações.                                                               |
| 422   | `Un-processable entity` -Indica que a entidade a ser publicado falha na validação.                                                        |
| 404   | `Not found` -A entidade especificada pelo cliente não existe.                                                                              |
|  |  |
