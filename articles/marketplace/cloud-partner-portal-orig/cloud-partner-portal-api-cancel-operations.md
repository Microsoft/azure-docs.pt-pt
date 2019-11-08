---
title: Cancelar API de operação | Azure Marketplace
description: Cancelar operações.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 374425dbd2abacb2114b5792d7476bc341fa353a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819792"
---
# <a name="cancel-operation"></a>Cancelar operação 

Essa API cancela uma operação atualmente em andamento na oferta. Use a [API de operações de recuperação](./cloud-partner-portal-api-retrieve-operations.md) para obter um `operationId` para passar para essa API. O cancelamento geralmente é uma operação síncrona. no entanto, em alguns cenários complexos, uma nova operação pode ser necessária para cancelar um existente. Nesse caso, o corpo da resposta HTTP contém o local da operação que deve ser usado para consultar o status.

Você pode fornecer uma lista separada por vírgulas de endereços de email com a solicitação e a API notificará esses endereços sobre o progresso da operação.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>Parâmetros de URI
--------------

|  **Nome**    |      **Descrição**                                  |    **Tipo de dados**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Identificador de editor, por exemplo, `contoso`         |   String          |
| OfferId      |  Identificador da oferta                                     |   String          |
| versão da API  |  Versão atual da API                               |    Date           |
|  |  |  |


<a name="header"></a>Cabeçalho
------

|  **Nome**              |  **Valor**         |
|  ---------             |  ----------        |
|  Tipo de conteúdo          |  application/json  |
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

### <a name="request-body-properties"></a>Propriedades do corpo da solicitação

|  **Nome**                |  **Descrição**                                               |
|  --------                |  ---------------                                               |
|  notificação-emails     | Lista separada por vírgulas de IDs de email a ser notificada do progresso da operação de publicação. |
|  |  |


### <a name="response"></a>Resposta

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Cabeçalho de Resposta

|  **Nome**             |    **Valor**                       |
|  ---------            |    ----------                      |
| Operação-localização    | URL, que pode ser consultada para determinar o status atual da operação. |
|  |  |


### <a name="response-status-codes"></a>Códigos de status de resposta

| **Auto-completar**  |  **Descrição**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | Okey. A solicitação foi processada com êxito e a operação foi cancelada de forma síncrona. |
|  202      | Aceitar. A solicitação foi processada com êxito e a operação está em processo de cancelamento. O local da operação de cancelamento é retornado no cabeçalho de resposta. |
|  400      | Solicitação incorreta/malformada. O corpo da resposta de erro pode fornecer mais informações.  |
|  403      | Acesso proibido. O cliente não tem acesso ao namespace especificado na solicitação. |
|  404      | Não encontrado. A entidade especificada não existe. |
|  |  |
