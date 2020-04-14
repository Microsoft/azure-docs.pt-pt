---
title: Cancelar operação API [ Operação API] Mercado Azure
description: Cancelar operações.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: f9e55ff2c581f9392a125f6dc3ec8d903e9876a4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256438"
---
# <a name="cancel-operation"></a>Cancelar operação

> [!NOTE]
> As APIs do Portal do Parceiro cloud estão integradas no Partner Center e continuarão a trabalhar após as suas ofertas serem migradas para o Partner Center. A integração introduz pequenas alterações. Reveja as alterações listadas no [Portal do Parceiro cloud Referência API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) para garantir que o seu código continua a funcionar após a migração para partner Center.

Esta API cancela uma operação em curso na oferta. Utilize as [operações de Recuperação API](./cloud-partner-portal-api-retrieve-operations.md) para obter um `operationId` passe para esta API. O cancelamento é geralmente uma operação sincronizada, no entanto em alguns cenários complexos uma nova operação pode ser necessária para cancelar uma existente. Neste caso, o organismo de resposta HTTP contém a localização da operação que deve ser usada para consulta do estado.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parâmetros URI

--------------

|  **Nome**    |      **Descrição**                                  |    **Tipo de dados**  |
| ------------ |     ----------------                                  |     -----------   |
| editorId  |  Identificador de editor, por exemplo,`contoso`         |   String          |
| offerId      |  Identificador de oferta                                     |   String          |
| api-version  |  Versão atual da API                               |    Date           |
|  |  |  |

## <a name="header"></a>Cabeçalho
------

|  **Nome**              |  **Valor**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Autorização         |  Portador do seu token |
|  |  |

## <a name="body-example"></a>Exemplo do corpo
------------

### <a name="request"></a>Pedir

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Solicitar propriedades corporais

|  **Nome**                |  **Descrição**                                               |
|  --------                |  ---------------                                               |
|  e-mails de notificação     | A Vírvia separou a lista de ids de e-mail para ser notificado do progresso da operação editorial. |
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
|  200      | Ok. O pedido foi processado com sucesso e a operação é cancelada sincronizadamente. |
|  202      | Aceite. O pedido foi processado com sucesso e a operação está em vias de ser cancelada. A localização da operação de cancelamento é devolvida no cabeçalho de resposta. |
|  400      | Pedido mau/mal formado. O corpo de resposta a erros pode fornecer mais informações.  |
|  403      | Acesso Proibido. O cliente não tem acesso ao espaço de nome especificado no pedido. |
|  404      | Não foi encontrado. A entidade especificada não existe. |
|  |  |
