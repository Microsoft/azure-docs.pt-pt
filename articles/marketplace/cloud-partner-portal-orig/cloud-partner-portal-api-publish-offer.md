---
title: Publique uma oferta [ Mercado Azure
description: API para publicar a oferta especificada.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 960d5facb53f20719045c5fdbe2179f549aca3f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255945"
---
# <a name="publish-an-offer"></a>Publicar uma oferta

> [!NOTE]
> As APIs do Portal do Parceiro cloud estão integradas no Partner Center e continuarão a trabalhar após as suas ofertas serem migradas para o Partner Center. A integração introduz pequenas alterações. Reveja as alterações listadas no [Portal do Parceiro cloud Referência API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) para garantir que o seu código continua a funcionar após a migração para partner Center.

Inicia o processo de publicação para a oferta especificada. Esta chamada é uma operação de longa duração.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parâmetros URI
--------------

|  **Nome**      |    **Descrição**                               |  **Tipo de dados** |
|  ------------- |  ------------------------------------            |   -----------  |
|  editorId   | Identificador de editor, por exemplo`contoso`      |   String       |
|  offerId       | Identificador de oferta                                 |   String       |
|  api-version   | Versão mais recente da API                        |   Date         |
|  |  |

## <a name="header"></a>Cabeçalho
------

|  **Nome**        |    **Valor**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Autorização   |  `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Exemplo do corpo
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

### <a name="request-body-properties"></a>Solicitar propriedades corporais

|  **Nome**               |   **Descrição**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  e-mails de notificação    | Lista separada da Comma de endereços de e-mail a ser notificado do progresso da operação editorial. |
|  |  |


### <a name="response"></a>Resposta

#### <a name="migrated-offers"></a>Ofertas migradas

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Ofertas não migradas

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Cabeçalho de Resposta

|  **Nome**             |    **Valor**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Localização    | O caminho relativo para recuperar o estado desta operação     |
|  |  |


### <a name="response-status-codes"></a>Códigos de estado de resposta

| **Código** |  **Descrição**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`- O pedido foi aceite com sucesso. A resposta contém um local que pode ser usado para acompanhar a operação que é lançada. |
| 400   | `Bad/Malformed request`- O organismo de resposta a erros pode fornecer mais informações.                                                               |
| 422   | `Un-processable entity`- Indica que a entidade a publicar não foi validada.                                                        |
| 404   | `Not found`- A entidade especificada pelo cliente não existe.                                                                              |
|  |  |
