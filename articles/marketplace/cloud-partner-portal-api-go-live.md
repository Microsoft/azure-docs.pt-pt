---
title: Go Live API - Azure Marketplace
description: A API Go Live inicia o processo de listagem ao vivo da oferta.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 5d550f576108447a88660321899f2f55ffeb3c77
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85516352"
---
# <a name="go-live"></a>Ir ao vivo

> [!NOTE]
> As APIs do Portal do Parceiro cloud estão integradas no Partner Center e continuarão a funcionar após as suas ofertas serem migradas para o Partner Center. A integração introduz pequenas mudanças. Reveja as alterações listadas no [Cloud Partner Portal API Reference](./cloud-partner-portal-api-overview.md) para garantir que o seu código continua a funcionar após a migração para o Partner Center.

Esta API inicia o processo de produção de uma app. Esta operação é geralmente longa. Esta chamada utiliza a lista de e-mails de notificação da operação [Publish](./cloud-partner-portal-api-publish-offer.md) API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>Parâmetros URI
--------------

|  **Nome**      |   **Descrição**                                                           | **Tipo de dados** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Identificador de editor para a oferta de recuperar, por exemplo`contoso`       |  String       |
| offerId        | Oferta identificador da oferta para recuperar                                   |  String       |
| api-version    | Última versão da API                                                   |  Data         |
|  |  |  |

## <a name="header"></a>Cabeçalho
------

|  **Nome**       |     **Valor**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Autorização   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>Exemplo corporal

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
|  202     | `Accepted`- O pedido foi aceite com sucesso. A resposta contém um local para rastrear o estado de funcionamento. |
|  400     | `Bad/Malformed request`- Informações adicionais de erro são encontradas dentro do corpo de resposta. |
|  404     |  `Not found`- A entidade especificada não existe.                                       |
|  |  |
