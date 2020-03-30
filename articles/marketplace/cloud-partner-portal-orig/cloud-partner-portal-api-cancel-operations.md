---
title: Cancelar operação API [ Operação API] Mercado Azure
description: Cancelar operações.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6d4c1f52f0f3b1e05ec06f5a66a36323f346d4eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280546"
---
# <a name="cancel-operation"></a>Cancelar operação 

Esta API cancela uma operação em curso na oferta. Utilize as [operações de Recuperação API](./cloud-partner-portal-api-retrieve-operations.md) para obter um `operationId` passe para esta API. O cancelamento é geralmente uma operação sincronizada, no entanto em alguns cenários complexos uma nova operação pode ser necessária para cancelar uma existente. Neste caso, o organismo de resposta HTTP contém a localização da operação que deve ser usada para consulta do estado.

Pode fornecer uma lista separada de endereços de e-mail com o pedido, e a API notificará estes endereços sobre o progresso da operação.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>Parâmetros URI
--------------

|  **Nome**    |      **Descrição**                                  |    **Tipo de dados**  |
| ------------ |     ----------------                                  |     -----------   |
| editorId  |  Identificador de editor, por exemplo,`contoso`         |   Cadeia          |
| offerId      |  Identificador de oferta                                     |   Cadeia          |
| api-version  |  Versão atual da API                               |    Date           |
|  |  |  |


<a name="header"></a>Cabeçalho
------

|  **Nome**              |  **Valor**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Autorização         |  Portador do seu token |
|  |  |


<a name="body-example"></a>Exemplo do corpo
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

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Cabeçalho de Resposta

|  **Nome**             |    **Valor**                       |
|  ---------            |    ----------                      |
| Localização da Operação    | URL, que pode ser consultado para determinar o estado atual da operação. |
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
