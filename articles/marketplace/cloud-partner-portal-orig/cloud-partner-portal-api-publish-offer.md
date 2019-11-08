---
title: Publicar uma oferta | Azure Marketplace
description: API para publicar a oferta especificada.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: b7ad8086c417cf1f14d9116fa4abcb0a88030922
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819646"
---
<a name="publish-an-offer"></a>Publicar uma oferta
================

Inicia o processo de publicação para a oferta especificada. Essa chamada é uma operação de longa execução.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>Parâmetros de URI
--------------

|  **Nome**      |    **Descrição**                               |  **Tipo de dados** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Identificador de editor, por exemplo `contoso`      |   String       |
|  OfferId       | Identificador da oferta                                 |   String       |
|  versão da API   | Versão mais recente da API                        |   Date         |
|  |  |


<a name="header"></a>Cabeçalho
------

|  **Nome**        |    **Valor**          |
|  --------        |    ---------          |
|  Tipo de conteúdo    | `application/json`    |
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

### <a name="request-body-properties"></a>Propriedades do corpo da solicitação

|  **Nome**               |   **Descrição**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  notificação-emails    | Lista separada por vírgulas de endereços de email a serem notificados do progresso da operação de publicação. |
|  |  |


### <a name="response"></a>Resposta

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Cabeçalho de Resposta

|  **Nome**             |    **Valor**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Operação-localização    | URL que pode ser consultada para determinar o status atual da operação.    |
|  |  |


### <a name="response-status-codes"></a>Códigos de status de resposta

| **Auto-completar** |  **Descrição**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`-a solicitação foi aceita com êxito. A resposta contém um local que pode ser usado para acompanhar a operação que é iniciada. |
| 400   | `Bad/Malformed request`-o corpo da resposta de erro pode fornecer mais informações.                                                               |
| 422   | `Un-processable entity`-indica que a entidade a ser publicada falhou na validação.                                                        |
| 404   | `Not found`-a entidade especificada pelo cliente não existe.                                                                              |
|  |  |
