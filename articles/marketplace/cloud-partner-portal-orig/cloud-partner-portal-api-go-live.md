---
title: Entrar em tempo real | Azure Marketplace
description: A API Go Live inicia o processo de listagem dinâmica da oferta.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 30500e9dfae9411563fc727290d0569998ba3550
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819676"
---
<a name="go-live"></a>Entrar em tempo real
=======

Essa API inicia o processo de envio por push de um aplicativo para produção. Essa operação geralmente é de execução longa. Essa chamada usa a lista email de notificação da operação [publicar](./cloud-partner-portal-api-publish-offer.md) API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>Parâmetros de URI
--------------

|  **Nome**      |   **Descrição**                                                           | **Tipo de dados** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Identificador de editor da oferta a ser recuperada, por exemplo `contoso`       |  String       |
| OfferId        | Identificador da oferta para recuperar                                   |  String       |
| versão da API    | Versão mais recente da API                                                   |  Date         |
|  |  |  |


<a name="header"></a>Cabeçalho
------

|  **Nome**       |     **Valor**       |
|  ---------      |     ----------      |
| Tipo de conteúdo    | `application/json`  |
| Autorização   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>Exemplo de corpo
------------

### <a name="response"></a>Resposta

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Cabeçalho de Resposta

|  **Nome**             |      **Valor**                                                            |
|  --------             |      ----------                                                           |
| Operação-localização    |  URL para consulta para determinar o status atual da operação            |
|  |  |


### <a name="response-status-codes"></a>Códigos de status de resposta

| **Auto-completar** |  **Descrição**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`-a solicitação foi aceita com êxito. A resposta contém um local para acompanhar o status da operação. |
|  400     | `Bad/Malformed request`-informações de erro adicionais são encontradas no corpo da resposta. |
|  404     |  `Not found`-a entidade especificada não existe.                                       |
|  |  |
