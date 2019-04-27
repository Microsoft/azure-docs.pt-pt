---
title: Cancelar a operação de API | Documentos da Microsoft
description: Cancele operações.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 18f00391beded0744c80eab73bb1efe1c6ab8dbc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60625045"
---
<a name="cancel-operation"></a>Operação de cancelamento 
=================

Esta API cancela uma operação atualmente em curso sobre a oferta. Utilize o [obter operações de API](./cloud-partner-portal-api-retrieve-operations.md) para obter um `operationId` para passar para esta API. O cancelamento é normalmente uma operação síncrona, no entanto, em alguns cenários complexos pode ser necessário uma operação de novo para cancelar um já existente. Neste caso, o corpo da resposta HTTP contém a localização da operação que deve ser usada para consultar o estado.

Pode fornecer uma lista separada por vírgulas de endereços de e-mail com a solicitação e a API notificará estes endereços sobre o progresso da operação.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>Parâmetros do URI
--------------

|  **Nome**    |      **Descrição**                                  |    **Tipo de dados**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Identificador de publicador, por exemplo, `contoso`         |   String          |
| IDoferta      |  Identificador da oferta                                     |   String          |
| versão de API  |  Versão atual da API                               |    Date           |
|  |  |  |


<a name="header"></a>Cabeçalho
------

|  **Nome**              |  **Valor**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Autorização         |  Portador seu TOKEN |
|  |  |


<a name="body-example"></a>Exemplo de corpo
------------

### <a name="request"></a>Pedir

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Propriedades do corpo do pedido

|  **Nome**                |  **Descrição**                                               |
|  --------                |  ---------------                                               |
|  e-mails de notificação     | Lista de Ids para ser notificado sobre o progresso da operação de publicação de e-mail separados por vírgulas. |
|  |  |


### <a name="response"></a>Resposta

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Cabeçalho de Resposta

|  **Nome**             |    **Valor**                       |
|  ---------            |    ----------                      |
| Operação de localização    | URL, que pode ser consultada para determinar o estado atual da operação. |
|  |  |


### <a name="response-status-codes"></a>Códigos de estado de resposta

| **Código**  |  **Descrição**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | OK. O pedido foi processado com êxito e a operação foi cancelada de forma síncrona. |
|  202      | Aceite. O pedido foi processado com êxito e a operação está a ser cancelada. Localização da operação de cancelamento é retornada no cabeçalho de resposta. |
|  400      | Pedido incorreto/Malformed. O corpo da resposta de erro pode fornecer mais informações.  |
|  403      | Acesso não autorizado. O cliente não tem acesso ao espaço de nomes especificado no pedido. |
|  404      | Não encontrado. A entidade especificada não existe. |
|  |  |
