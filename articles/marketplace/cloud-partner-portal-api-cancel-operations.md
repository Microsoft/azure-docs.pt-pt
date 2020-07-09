---
title: Cancelar operação API - Mercado comercial da Microsoft
description: A API para cancelar uma operação em curso na oferta
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: anbene
ms.author: mingshen
ms.date: 06/16/2020
ms.openlocfilehash: e65f0a8ee0a5dfafab681010006fe190cb5bad70
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86102757"
---
# <a name="cancel-operation"></a>Cancelar operação

> [!NOTE]
> As APIs do Portal do Parceiro cloud estão integradas no Partner Center e continuarão a funcionar após as suas ofertas serem migradas para o Partner Center. A integração introduz pequenas mudanças. Reveja as alterações listadas no [Cloud Partner Portal API Reference](./cloud-partner-portal-api-overview.md) para garantir que o seu código continua a funcionar após a migração para o Partner Center.

Esta API cancela uma operação em curso na oferta. Utilize a [API de operações de Recuperação](./cloud-partner-portal-api-retrieve-operations.md) para obter um `operationId` passe para esta API. O cancelamento é geralmente uma operação sincronizada, no entanto, em alguns cenários complexos, uma nova operação pode ser necessária para cancelar uma existente. Neste caso, o organismo de resposta HTTP contém a localização da operação que deve ser utilizada para o estado de consulta.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parâmetros URI

--------------

|  **Nome**    |      **Descrição**                                  |    **Tipo de dados**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Identificador de editor, por exemplo,`contoso`         |   String          |
| offerId      |  Identificar oferta                                     |   String          |
| api-version  |  Versão atual da API                               |    Data           |
|  |  |  |

## <a name="header"></a>Cabeçalho
------

|  **Nome**              |  **Valor**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Autorização         |  Portador DO SEU TOKEN |
|  |  |

## <a name="body-example"></a>Exemplo corporal
------------

### <a name="request"></a>Pedir

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Solicitar propriedades do corpo

|  **Nome**                |  **Descrição**                                               |
|  --------                |  ---------------                                               |
|  notificações-e-mails     | A Vírgula separou a lista de identificações de e-mails para ser notificada do progresso da operação editorial. |
|  |  |

### <a name="response"></a>Resposta

#### <a name="migrated-offers"></a>Ofertas migradas

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Ofertas não migradas

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Cabeçalho de Resposta

|  **Nome**             |    **Valor**                       |
|  ---------            |    ----------                      |
| Localização    | O caminho relativo para recuperar o estado desta operação. |
|  |  |

### <a name="response-status-codes"></a>Códigos de estado de resposta

| **Código**  |  **Descrição**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | Ok. O pedido foi processado com sucesso e a operação é cancelada de forma sincronizada. |
|  202      | Aceite. O pedido foi processado com sucesso e a operação está em vias de ser cancelada. A localização da operação de cancelamento é devolvida no cabeçalho de resposta. |
|  400      | Mau/pedido mal formado. O corpo de resposta a erros pode fornecer mais informações.  |
|  403      | Acesso proibido. O cliente não tem acesso ao espaço de nome especificado no pedido. |
|  404      | Não encontrado. A entidade especificada não existe. |
|  |  |
