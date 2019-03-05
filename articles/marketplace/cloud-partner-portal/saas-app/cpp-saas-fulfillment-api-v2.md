---
title: V2 de API de preenchimento do SaaS - o Azure Marketplace | Documentos da Microsoft
description: Explica como criar uma oferta SaaS no Azure Marketplace usando o preenchimento associado V2 APIs.
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
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: pbutlerm
ms.openlocfilehash: 7e1fa82e71738ac113bfe748a7117d0c3a10b14b
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57318762"
---
# <a name="saas-fulfillment-api-version-2"></a>Versão de API de preenchimento do SaaS 2

Este artigo fornece detalhes sobre a API que permite aos fornecedores de software independentes (ISVs) para integrar as suas aplicações SaaS com o Azure Marketplace. Esta API permite que os aplicativos de ISV participar em todos os canais de comércio ativado: direto, liderada por parceiros (revendedor) e orientado por campo.  Esta API é um requisito para listagem que transactable SaaS oferece no Azure Marketplace.


## <a name="managing-the-saas-subscription-lifecycle"></a>Gerir o ciclo de vida de subscrição de SaaS

Microsoft SaaS Service gerencia todo o ciclo de vida de uma compra de subscrição de SaaS e utiliza a API de preenchimento como um mecanismo para orientar o atendimento real, é alterado para planos e eliminação da subscrição com o ISV. O cliente será faturado com base no estado da subscrição de SaaS que a Microsoft mantém. O diagrama seguinte ilustra os Estados e as operações que orientam as alterações entre Estados.

![Estados de ciclo de vida de subscrição de SaaS](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Estados de uma subscrição de SaaS

A tabela seguinte lista os Estados de aprovisionamento de uma subscrição de SaaS, incluindo um diagrama de descrição e a sequência para cada (se aplicável). 

#### <a name="provisioning"></a>Aprovisionamento

Quando um cliente inicia uma compra, o ISV recebe essas informações num AuthCode numa página da web interativas ao cliente com um parâmetro de URL. O AuthCode pode ser validado e trocado para obter os detalhes sobre o que tem de ser aprovisionado.  Quando o serviço SaaS concluir o aprovisionamento, envia uma chamada de ativar para sinalizar que o preenchimento estiver concluído e o cliente pode ser faturado.  O diagrama seguinte mostra a sequência de chamadas de API para um cenário de aprovisionamento.  

![Chamadas de API para o aprovisionamento de um serviço SaaS.](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Aprovisionado

Este estado é o estado estável de um serviço aprovisionado.

#### <a name="provisioning-for-update"></a>Aprovisionamento para a atualização
(no marketplace) 

Este estado representa uma atualização de um existente já serviço está pendente. Uma atualização desse tipo pode ser iniciada pelo cliente no marketplace ou no serviço SaaS (apenas para o direct para transações do cliente). O diagrama seguinte mostra as ações quando é iniciada uma atualização a partir do marketplace.

![As chamadas de API quando a atualização é iniciada a partir do marketplace.](./media/saas-update-api-v2-calls-from-marketplace-a.png)

#### <a name="provisioning-for-update"></a>Aprovisionamento para a atualização  
(a partir do serviço SaaS) 

O diagrama seguinte mostra as ações quando uma atualização é iniciada pelo serviço SaaS. (A chamada de webhook é substituída por uma atualização para a subscrição iniciada pelo serviço SaaS. 

![As chamadas de API quando a atualização é iniciada pelo serviço SaaS.](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Suspenso

Este estado indica que o pagamento de um cliente ainda não foi recebido. Por política, forneceremos o cliente um período de tolerância antes unfulfilling a subscrição. Quando uma subscrição está neste Estado: 

- Enquanto ISV, pode optar por degradar ou bloquear o acesso do utilizador para o serviço. 
- A subscrição têm de ser mantida num Estado recuperável que pode restaurar todas as funcionalidades sem qualquer perda de dados ou configurações. 
- Pode esperar obter um pedido de restabelecimento para esta subscrição através do preenchimento de API ou um pedido de desprovisionamento no final do período de tolerância. 

#### <a name="unsubscribed"></a>Com subscrição anulada 

Subscrições atingir esse Estado em resposta a um pedido de cliente explícita ou como uma resposta para o não pagamento das tributos que se. A expectativa do ISV é que os dados do cliente são mantidos para a recuperação no pedido para um mínimo de X dias e, em seguida, eliminados. 

## <a name="api-reference"></a>Referência da API

Esta secção documenta o SaaS *API da subscrição* e *operações API*.


### <a name="subscription-api"></a>API de subscrição

A API de subscrição suporta as seguintes operações de HTTPS: **Obtenha**, **Post**, **Patch**, e **eliminar**.

#### <a name="list-subscriptions"></a>Listar subscrições

Apresenta uma lista de todas as subscrições de SaaS para um publicador.

**Get:<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`**

*Parâmetros de consulta:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  A versão da operação para utilizar para este pedido.  |

*Cabeçalhos de pedido:*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  Valor de cadeia de caracteres exclusivo para o pedido de controlo do cliente, preferencialmente, um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| x-ms-correlationid |  Valor de cadeia de caracteres exclusivo para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação de cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
| Autorização      |  O JSON web token (JWT) token de portador.  |

*Códigos de resposta:*

Código: 200<br>
Base do get de token de autenticação do publicador e subscrições correspondentes para as ofertas do publicador.<br> Payload de resposta:<br>

```json
{
  "subscriptions": [
      {
          "id": "",
          "name": "CloudEndure for Production use",
          "publisherId": "cloudendure",
          "offerId": "ce-dr-tier2",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "cc906b16-1991-4b6d-a5a4-34c66a5202d7"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "0396833b-87bf-4f31-b81c-c67f88973512"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "status": "Subscribed" // Indicates the status of the operation. [Provisioning, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

Código: 403 <br>
Não autorizado. Não foi fornecido o token de autenticação, é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence ao utilizador atual. 

Código: 500 Erro de Servidor Interno

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>Obter subscrição

Obtém a subscrição especificada de SaaS. Utilize esta chamada para obter informações de licença e informações do plano.

**Get:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`**

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Identificador exclusivo da subscrição de SaaS, que é obtida depois de resolver o token através da API de resolver   |
|  ApiVersion        |   Versão da operação para utilizar para este pedido   |

*Cabeçalhos de pedido:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  Valor de cadeia de caracteres exclusivo para o pedido de controlo do cliente, preferencialmente, um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  x-ms-correlationid |  Valor de cadeia de caracteres exclusivo para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação de cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  Autorização     |  JSON web token (JWT) portador tokens  |

*Códigos de resposta:*

Código: 200<br>
Obtém a assinatura de saas de identificador<br> Payload de resposta:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"CloudEndure for Production use",
        "publisherId": "cloudendure",
        "offerId": "ce-dr-tier2",
        "planId": "silver",
        "quantity": "10"",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "cc906b16-1991-4b6d-a5a4-34c66a5202d7"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "0396833b-87bf-4f31-b81c-c67f88973512"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "status": "Subscribed", // Indicates the status of the operation.
}
```

Código: 404<br>
Não Encontrado<br> 

Código: 403<br>
Não autorizado. Não foi fornecido o token de autenticação, é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence ao utilizador atual.

Código: 500<br>
Erro Interno do Servidor<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Lista de planos disponíveis

Utilize esta chamada para saber se existem quaisquer ofertas de pública/privada para o utilizador atual.

**Get:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`**

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   Versão da operação para utilizar para este pedido  |

*Cabeçalhos de pedido:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   Valor de cadeia de caracteres exclusivo para o pedido de controlo do cliente, preferencialmente, um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  x-ms-correlationid  | Valor de cadeia de caracteres exclusivo para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação de cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  Autorização     |  JSON web token (JWT) portador tokens |

*Códigos de resposta:*

Código: 200<br>
Obter uma lista dos planos disponíveis para um cliente.<br>

```json
Response Body:
[{
    "planId": "silver",
    "displayName": "Silver",
    "isPrivate": false
},
{
    "planId": "silver-private",
    "displayName": "Silver-private",
    "isPrivate": true
}]
```

Código: 404<br>
Não Encontrado<br> 

Código: 403<br>
Não autorizado. Não foi fornecido o token de autenticação, é inválido ou o pedido está a tentar aceder a uma aquisição que não pertence ao utilizador atual. <br> 

Código: 500<br>
Erro Interno do Servidor<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="resolve-a-subscription"></a>Resolver uma subscrição 

O ponto de extremidade de resolver permite aos utilizadores resolver um token do marketplace para uma ID de recurso persistente. O ID de recurso é o identificador exclusivo para a subscrição de SAAS.  Quando um utilizador é redirecionado para o site de um ISV, o URL contém um token nos parâmetros de consulta. O ISV deve utilizar este token e fazer um pedido para resolvê-lo. A resposta contém o exclusivo ID de subscrição de SAAS, nome, ID de oferta e plano para o recurso. Este token é válido apenas uma hora. 

**Mensagem:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`**

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Versão da operação para utilizar para este pedido  |

*Cabeçalhos de pedido:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  Valor de cadeia de caracteres exclusivo para o pedido de controlo do cliente, preferencialmente, um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  x-ms-correlationid |  Valor de cadeia de caracteres exclusivo para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação de cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  Autorização     |  JSON web token (JWT) portador tokens  |
|  x-ms-marketplace-token  |  Parâmetro de consulta de token no URL quando o utilizador é redirecionado para o website do ISV SaaS do Azure. *Nota:* O URL descodifica o valor do token do navegador antes de o utilizar. |

*Códigos de resposta:*

Código: 200<br>
Resolve o token opaco para uma subscrição de SaaS.<br>

```json
Response body:
{
    "subscriptionId": "cd9c6a3a-7576-49f2-b27e-1e5136e57f45",  
    "subscriptionName": "My Saas application",
    "offerId": "ce-dr-tier2",
    "planId": "silver",
    "quantity": "20",
    "operationId": " be750acb-00aa-4a02-86bc-476cbe66d7fa"  
}
```

Código: 404<br>
Não Encontrado

Código: 400<br>
Falhas de validação de pedido incorreto

Código: 403<br>
Não autorizado. Não foi fornecido o token de autenticação, é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence ao utilizador atual.

Código: 500<br>
Erro Interno do Servidor

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="activate-a-subscription"></a>Ative uma subscrição

**Mensagem:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`**

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Versão da operação para utilizar para este pedido  |
| subscriptionId     | Identificador exclusivo da subscrição do SaaS, que é obtida depois de resolver o token com a API de resolver  |

*Cabeçalhos de pedido:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | Valor de cadeia de caracteres exclusivo para o pedido de controlo do cliente, preferencialmente, um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid  | Valor de cadeia de caracteres exclusivo para a operação no cliente. Isso correlaciona todos os eventos da operação de cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  Autorização     |  JSON web token (JWT) portador tokens |

*Pedido:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Códigos de resposta:*

Código: 202<br>
Ativa a subscrição.<br>

Código: 404<br>
Não Encontrado

Código: 400<br>
Falhas de validação de pedido incorreto

Código: 403<br>
Não autorizado. Não foi fornecido o token de autenticação, é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence ao utilizador atual.

Código: 500<br>
Erro Interno do Servidor

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="update-a-subscription"></a>Atualizar uma subscrição

Atualizar ou alterar um plano de subscrição com os valores fornecidos.

**Patch:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação para utilizar para este pedido.  |
| subscriptionId     | Identificador exclusivo da subscrição do SaaS, que é obtida depois de resolver o token com a API de resolver.  |

*Cabeçalhos de pedido:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Um valor de cadeia de caracteres exclusivo para o pedido de controlo do cliente, preferencialmente, um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid  |  Um valor de cadeia de caracteres exclusivo para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação de cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.    |
| Autorização      |  O JSON web token (JWT) token de portador.  |

*Payload de pedido:*

```json
Request Body:
{
    "planId": "gold",
    "quantity": ""
}
```

*Cabeçalhos de pedido:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operação de localização | Ligar a um recurso ao obter o estado da operação.   |

*Códigos de resposta:*

Código: 202<br>
ISV inicia um plano de alteração ou uma quantidade de alterações. <br>

Código: 404<br>
Não Encontrado

Código: 400<br>
Falhas de validação de pedido incorreto.

>[!Note]
>Apenas um plano ou a quantidade pode ser corrigida de uma só vez, não ambos. Edita uma subscrição com **atualização** não está no `allowedCustomerOperations`.

Código: 403<br>
Não autorizado. Não foi fornecido o token de autenticação, é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence ao utilizador atual.

Código: 500<br>
Erro Interno do Servidor

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="delete-a-subscription"></a>Eliminar uma subscrição

Anular a subscrição e eliminar a subscrição especificada.

**Delete:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`**

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação para utilizar para este pedido.  |
| subscriptionId     | Identificador exclusivo da subscrição do SaaS, que é obtida depois de resolver o token com a API de resolver.  |

*Cabeçalhos de pedido:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   Um valor de cadeia de caracteres exclusivo para o pedido de controlo do cliente, preferencialmente, um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.   |
|  x-ms-correlationid  |  Um valor de cadeia de caracteres exclusivo para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação de cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.   |
|  Autorização     |  O JSON web token (JWT) token de portador.   |

*Códigos de resposta:*

Código: 200<br>
Anular a subscrição a chamada de ISV iniciada para indicar uma subscrição de SaaS.<br>

Código: 404<br>
Não Encontrado

Código: 400<br>
Eliminar uma subscrição com **elimine** não está no `allowedCustomerOperations`.

Código: 403<br>
Não autorizado. Não foi fornecido o token de autenticação, é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence ao utilizador atual.

Código: 500<br>
Erro Interno do Servidor

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>Operações de API

A API de operações suporta as seguintes operações Get e patches.


#### <a name="update-a-subscription"></a>Atualizar uma subscrição

Atualize uma subscrição com os valores fornecidos.

**Patch:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operation/<operationId>?api-version=<ApiVersion>`**

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  A versão da operação para utilizar para este pedido.  |
| subscriptionId     | Identificador exclusivo da subscrição do SaaS, que é obtida depois de resolver o token com a API de resolver.  |
|  operationId       | A operação que está a ser concluída. |

*Cabeçalhos de pedido:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   Um valor de cadeia de caracteres exclusivo para o pedido de controlo do cliente, preferencialmente, um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  x-ms-correlationid |  Um valor de cadeia de caracteres exclusivo para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação de cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  Autorização     |  O JSON web token (JWT) token de portador.  |

*Payload de pedido:*

```json
{
    "planId": "",
    "quantity": "",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}
```

*Códigos de resposta:*

Código: 200<br> Chamada para o informar de conclusão de uma operação no lado do ISV. Por exemplo, isto pode ser alteração dos planos/postos de trabalho.

Código: 404<br>
Não Encontrado

Código: 400<br>
Falhas de validação de pedido incorreto

Código: 403<br>
Não autorizado. Não foi fornecido o token de autenticação, é inválido ou o pedido está a tentar aceder a uma aquisição que não pertence ao utilizador atual.

Código: 409<br>
Conflito. Por exemplo, uma transação mais recente já foi cumprida

Código: 500<br> Erro Interno do Servidor

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="list-outstanding-operations"></a>Lista de operações pendentes 

Lista as operações pendentes para o utilizador atual. 

**Get:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`**

*Parâmetros de consulta:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   A versão da operação para utilizar para este pedido.                |
| subscriptionId     | Identificador exclusivo da subscrição do SaaS, que é obtida depois de resolver o token com a API de resolver.  |

*Cabeçalhos de pedido:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  Um valor de cadeia de caracteres exclusivo para o pedido de controlo do cliente, preferencialmente, um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid |  Um valor de cadeia de caracteres exclusivo para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação de cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  Autorização     |  O JSON web token (JWT) token de portador.  |

*Códigos de resposta:*

Código: 200<br> Obtém a lista de operações pendentes numa subscrição.<br>
Payload de resposta:

```json
[{
    "id": "be750acb-00aa-4a02-86bc-476cbe66d7fa",  
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId": "cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "offerId": "ce-dr-tier2",
    "publisherId": "cloudendure",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```

Código: 404<br>
Não Encontrado

Código: 400<br>
Falhas de validação de pedido incorreto

Código: 403<br>
Não autorizado. Não foi fornecido o token de autenticação, é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence ao utilizador atual.

Código: 500<br>
Erro Interno do Servidor

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>Obter estado da operação

Permite ao utilizador controlar o estado de uma operação de async acionadas (subscrever/anular a subscrição/alterar plano).

**Get:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação para utilizar para este pedido.  |

*Cabeçalhos de pedido:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  ` application/json`   |
|  x-ms-requestid    |   Um valor de cadeia de caracteres exclusivo para o pedido de controlo do cliente, preferencialmente, um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid |  Um valor de cadeia de caracteres exclusivo para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação de cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  Autorização     | O JSON web token (JWT) token de portador.  |

*Códigos de resposta:* Código: 200<br> Obtém a lista de todas as operações pendentes de SaaS<br>
Payload de resposta:

```json
Response body:
[{
    "id  ": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "offerId": "ce-dr-tier2",
    "publisherId": "cloudendure",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}]

```

Código: 404<br>
Não Encontrado

Código: 400<br>
Falhas de validação de pedido incorreto

Código: 403<br>
Não autorizado. Não foi fornecido o token de autenticação, é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence ao utilizador atual.
 
Código: 500<br> Erro Interno do Servidor

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="webhook-on-the-saas-service"></a>Webhook no serviço SaaS

O publicador tem de implementar um webhook neste serviço de SaaS para ser notificado proativamente usuários das alterações no seu serviço. A API deve ser não autenticadas e será chamada pelo serviço SaaS da Microsoft. O serviço de SaaS é esperado para chamar as operações de API para validar e autorizar antes de efetuar uma ação na notificação de webhook.

```json
{
    "operationId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "offerId": "ce-dr-tier2",
    "publisherId": "cloudendure",
    "planId": "silver",
    "quantity": "20"  ,
    "action": "Activate",   // Activate/Delete/Suspend/Reinstate/Change[new]  
    "timeStamp": "2018-12-01T00:00:00"
}

```

<!-- Review following, might not be needed when this publishes -->


## <a name="mock-api"></a>API de teste

Pode utilizar as nossas APIs fictícios para ajudar a começar com o desenvolvimento, particularmente, criação de protótipos e projetos de testes. 

Ponto final de anfitrião: https://marketplaceapi.microsoft.com/api Versão de API: 2018-09-15 sem autenticação necessário o Uri de exemplo: https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15

Qualquer uma das chamadas de API neste artigo podem ser feitas para o ponto de extremidade do anfitrião de simulação. Pode esperar obter dados fictícios novamente como uma resposta.


## <a name="next-steps"></a>Passos Seguintes

Os desenvolvedores podem também obter automaticamente a e manipulação de cargas de trabalho, ofertas e publicador perfis utilizando o [APIs de REST do Cloud Partner Portal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
