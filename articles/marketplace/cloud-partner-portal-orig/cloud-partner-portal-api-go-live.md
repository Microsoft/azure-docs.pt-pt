---
title: Ir ao vivo Mercado Azure
description: A API Go Live inicia o processo de listagem ao vivo.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: ef22f7720a4af2239c55d1a01f9d3f11c878d66e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256319"
---
# <a name="go-live"></a>Ir ao vivo

> [!NOTE]
> As APIs do Portal do Parceiro cloud estão integradas no Partner Center e continuarão a trabalhar após as suas ofertas serem migradas para o Partner Center. A integração introduz pequenas alterações. Reveja as alterações listadas no [Portal do Parceiro cloud Referência API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) para garantir que o seu código continua a funcionar após a migração para partner Center.

Esta API inicia o processo de empurrar uma app para a produção. Esta operação é geralmente de longa duração. Esta chamada utiliza a lista de e-mails de notificação da operação [Publish](./cloud-partner-portal-api-publish-offer.md) API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>Parâmetros URI
--------------

|  **Nome**      |   **Descrição**                                                           | **Tipo de dados** |
|  --------      |   ---------------                                                           | ------------- |
| editorId    | Identificador de editor para a oferta de recuperação, por exemplo`contoso`       |  String       |
| offerId        | Ofereça identificador da oferta para recuperar                                   |  String       |
| api-version    | Versão mais recente da API                                                   |  Date         |
|  |  |  |

## <a name="header"></a>Cabeçalho
------

|  **Nome**       |     **Valor**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Autorização   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>Exemplo do corpo

### <a name="response"></a>Resposta

#### <a name="migrated-offers"></a>Ofertas migradas

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Ofertas não migradas

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Cabeçalho de Resposta

|  **Nome**             |      **Valor**                                                            |
|  --------             |      ----------                                                           |
| Localização    |  O caminho relativo para recuperar o estado desta operação            |
|  |  |

### <a name="response-status-codes"></a>Códigos de estado de resposta

| **Código** |  **Descrição**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`- O pedido foi aceite com sucesso. A resposta contém um local para rastrear o estado de operação. |
|  400     | `Bad/Malformed request`- Informações adicionais de erro são encontradas dentro do corpo de resposta. |
|  404     |  `Not found`- A entidade especificada não existe.                                       |
|  |  |
