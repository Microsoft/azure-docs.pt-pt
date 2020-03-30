---
title: Ir ao vivo Mercado Azure
description: A API Go Live inicia o processo de listagem ao vivo.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: bf7bebf6e72e373811879a311d70255c29988ed6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288585"
---
<a name="go-live"></a>Ir ao vivo
=======

Esta API inicia o processo de empurrar uma app para a produção. Esta operação é geralmente de longa duração. Esta chamada utiliza a lista de e-mails de notificação da operação [Publish](./cloud-partner-portal-api-publish-offer.md) API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>Parâmetros URI
--------------

|  **Nome**      |   **Descrição**                                                           | **Tipo de dados** |
|  --------      |   ---------------                                                           | ------------- |
| editorId    | Identificador de editor para a oferta de recuperação, por exemplo`contoso`       |  Cadeia       |
| offerId        | Ofereça identificador da oferta para recuperar                                   |  Cadeia       |
| api-version    | Versão mais recente da API                                                   |  Date         |
|  |  |  |


<a name="header"></a>Cabeçalho
------

|  **Nome**       |     **Valor**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Autorização   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>Exemplo do corpo
------------

### <a name="response"></a>Resposta

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Cabeçalho de Resposta

|  **Nome**             |      **Valor**                                                            |
|  --------             |      ----------                                                           |
| Localização da Operação    |  URL para consulta para determinar o estado atual da operação            |
|  |  |


### <a name="response-status-codes"></a>Códigos de estado de resposta

| **Código** |  **Descrição**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`- O pedido foi aceite com sucesso. A resposta contém um local para rastrear o estado de operação. |
|  400     | `Bad/Malformed request`- Informações adicionais de erro são encontradas dentro do corpo de resposta. |
|  404     |  `Not found`- A entidade especificada não existe.                                       |
|  |  |
