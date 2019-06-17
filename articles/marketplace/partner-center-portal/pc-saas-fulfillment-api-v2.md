---
title: SaaS preenchimento API v2 | O Azure Marketplace
description: Explica como criar e gerir uma oferta de SaaS no AppSource e com o atendimento associado do Azure Marketplace v2 APIs.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: da23b90e44869dcbd21acf9b2c4e04f30153ae09
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66751771"
---
# <a name="saas-fulfillment-apis-version-2"></a>APIs de preenchimento de SaaS versão 2 

Este artigo fornece detalhes sobre a API que permite que fornecedores independentes de software (ISVs) para vender as suas aplicações SaaS no Azure Marketplace e AppSource. Esta API é que um requisito para transactable SaaS oferece no Azure Marketplace e AppSource.

## <a name="managing-the-saas-subscription-lifecycle"></a>Gerir o ciclo de vida de subscrição de SaaS

Microsoft SaaS Service gerencia todo o ciclo de vida de uma compra de subscrição de SaaS e utiliza a API de preenchimento como um mecanismo para orientar o atendimento real, é alterado para planos e eliminação da subscrição com o ISV. O cliente será faturado com base no estado da subscrição de SaaS que a Microsoft mantém. O diagrama seguinte ilustra os Estados e as operações que orientam as alterações entre Estados.

![Estados de ciclo de vida de subscrição de SaaS](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Estados de uma subscrição de SaaS

A tabela seguinte lista os Estados de aprovisionamento de uma subscrição de SaaS, incluindo um diagrama de descrição e a sequência para cada (se aplicável). 

#### <a name="provisioning"></a>Aprovisionamento

Quando um cliente inicia uma compra, o ISV recebe essas informações num código de autenticação numa página da web interativas ao cliente com um parâmetro de URL. Por exemplo: `https://contoso.com/signup?token=..`, em que é o fornecedor de URL de página de aterrissagem no Centro de parceiros `https://contoso.com/signup`. O código de autenticação pode ser validado e trocado para obter os detalhes sobre o que tem de ser aprovisionados ao chamar a API de resolver.  Quando o serviço SaaS concluir o aprovisionamento, envia uma chamada de ativar para sinalizar que o preenchimento estiver concluído e o cliente pode ser faturado.  O diagrama seguinte mostra a sequência de chamadas de API para um cenário de aprovisionamento.  

![Chamadas de API para o aprovisionamento de um serviço SaaS.](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Aprovisionada

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

#### <a name="unsubscribed"></a>Anulada 

Subscrições atingir esse Estado em resposta a um pedido de cliente explícita ou como uma resposta para o não pagamento das tributos que se. A expectativa do ISV é que os dados do cliente são mantidos para a recuperação no pedido para um mínimo de X dias e, em seguida, eliminados. 


## <a name="api-reference"></a>Referência da API

Esta secção documenta o SaaS *API da subscrição* e *operações API*.  O valor do `api-version` APIs de parâmetro para a versão 2 é `2018-08-31`.  


### <a name="parameter-and-entity-definitions"></a>Definições de parâmetros e entidade

A tabela seguinte lista as definições para entidades utilizadas pelas APIs de preenchimento e parâmetros comuns.

|     Entidade/parâmetro     |     Definição                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | Identificador GUID de um recurso de SaaS  |
| `name`                   | Nome amigável, fornecido para este recurso pelo cliente |
| `publisherId`            | Identificador de cadeia exclusiva para cada editor, por exemplo "contoso" |
| `offerId`                | Identificador de cadeia exclusiva para cada oferta, por exemplo "offer1"  |
| `planId`                 | Identificador de cadeia exclusiva para cada plano/sku, por exemplo "silver" |
| `operationId`            | Identificador de GUID para uma determinada operação  |
|  `action`                | A ação a ser executada num recurso, seja `subscribe`, `unsubscribe`, `suspend`, `reinstate`, ou `changePlan`, `changeQuantity`, `transfer`  |
|   |   |

Identificadores globalmente exclusivos ([GUIDs](https://en.wikipedia.org/wiki/Universally_unique_identifier)) são números de (hexadecimal de 32) de 128 bits que normalmente são gerados automaticamente. 

#### <a name="resolve-a-subscription"></a>Resolver uma subscrição 

O ponto de extremidade de resolver permite que o publicador para resolver um token do marketplace para uma ID de recurso persistente. O ID de recurso é o identificador exclusivo para a subscrição de SAAS.  Quando um utilizador é redirecionado para o site de um ISV, o URL contém um token nos parâmetros de consulta. O ISV deve utilizar este token e fazer um pedido para resolvê-lo. A resposta contém o exclusivo ID de subscrição de SAAS, nome, ID de oferta e plano para o recurso. Este token é válido apenas uma hora. 

**Mensagem:<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`**

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
|  authorization     |  [Obtenha o token de portador do JSON web token (JWT)](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) |
|  x-ms-marketplace-token  |  Parâmetro de consulta token no URL quando o utilizador é redirecionado para o website do ISV SaaS do Azure (por exemplo: `https://contoso.com/signup?token=..`). *Nota:* O URL descodifica o valor do token do navegador antes de o utilizar.  |

*Códigos de resposta:*

Código: 200<br>
Resolve o token opaco para uma subscrição de SaaS.<br>

```json
Response body:
{
    "subscriptionId": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

Código: 404<br>
Não foi encontrado

Código: 400<br>
Pedido incorreto. x-ms-marketplace-token está em falta, com formato incorreto ou expirado.

Código: 403<br>
Não autorizado. Não foi fornecido o token de autenticação, é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence ao publicador atual.

Código: 500<br>
Erro de servidor interno

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>API de subscrição

A API de subscrição suporta as seguintes operações de HTTPS: **Obtenha**, **Post**, **Patch**, e **eliminar**.


#### <a name="list-subscriptions"></a>Lista de subscrições

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
| authorization      |  [Obtenha o token de portador do JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Códigos de resposta:*

Código: 200 <br/>
Com base no token de autenticação, obtenha o publicador e subscrições correspondentes para as ofertas do publicador.<br> Payload de resposta:<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

O token de continuação apenas será apresentado se existem adicionais "páginas" dos planos para recuperar. 

Código: 403 <br>
Não autorizado. Não foi fornecido o token de autenticação, é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence ao publicador atual. 

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
|  authorization     |  [Obtenha o token de portador do JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Códigos de resposta:*

Código: 200<br>
Obtém a assinatura de SaaS de identificador<br> Payload de resposta:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "offer1",
        "planId": "silver",
        "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "status": "Subscribed", // Indicates the status of the operation.
}
```

Código: 404<br>
Não foi encontrado<br> 

Código: 403<br>
Não autorizado. Não foi fornecido o token de autenticação, é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence ao publicador atual.

Código: 500<br>
Erro de servidor interno<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Lista de planos disponíveis

Utilize esta chamada para saber se existem quaisquer ofertas de pública/privada para o publicador atual.

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
|  authorization     |  [Obtenha o token de portador do JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) |

*Códigos de resposta:*

Código: 200<br>
Obter uma lista dos planos disponíveis para um cliente.<br>

Corpo da resposta:

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

Código: 404<br>
Não foi encontrado<br> 

Código: 403<br>
Não autorizado. Não foi fornecido o token de autenticação, é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence ao publicador atual. <br> 

Código: 500<br>
Erro de servidor interno<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
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
|  x-ms-correlationid  | Valor de cadeia de caracteres exclusivo para a operação no cliente. Esta cadeia correlaciona todos os eventos da operação de cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  authorization     |  [Obtenha o token de portador do JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) |

*Pedido:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Códigos de resposta:*

Código: 200<br>
Ativa a subscrição.<br>

Código: 404<br>
Não foi encontrado

Código: 400<br>
Falhas de validação de pedido incorreto

Código: 403<br>
Não autorizado. Não foi fornecido o token de autenticação, é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence ao publicador atual.

Código: 500<br>
Erro de servidor interno

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>Alterar o plano da subscrição

Atualize o plano da subscrição.

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
| authorization      |  [Obtenha o token de portador do JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Payload de pedido:*

```json
Request Body:
{
    "planId": "gold"
}
```

*Cabeçalhos de pedido:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operação de localização | Ligar a um recurso ao obter o estado da operação.   |

*Códigos de resposta:*

Código: 202<br>
O pedido para alterar o plano foi aceite. O ISV é esperado para consultar a localização de operação para determinar uma êxito/falha. <br>

Código: 404<br>
Não foi encontrado

Código: 400<br>
Falhas de validação de pedido incorreto.

>[!Note]
>Apenas um plano ou a quantidade pode ser corrigida de uma só vez, não ambos. Edita uma subscrição com **atualização** não está no `allowedCustomerOperations`.

Código: 403<br>
Não autorizado. Não foi fornecido o token de autenticação, é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence ao publicador atual.

Código: 500<br>
Erro de servidor interno

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-quantity-on-the-subscription"></a>Alterar a quantidade da subscrição

Atualize a quantidade da subscrição.

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
| authorization      |  [Obtenha o token de portador do JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Payload de pedido:*

```json
Request Body:
{
    "quantity": 5
}
```

*Cabeçalhos de pedido:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operação de localização | Ligar a um recurso ao obter o estado da operação.   |

*Códigos de resposta:*

Código: 202<br>
Aceite. O pedido para alterar a quantidade de ter sido aceite. O ISV é esperado para consultar a localização de operação para determinar uma êxito/falha. <br>

Código: 404<br>
Não foi encontrado

Código: 400<br>
Falhas de validação de pedido incorreto.

>[!Note]
>Apenas um plano ou a quantidade pode ser corrigida de uma só vez, não ambos. Edita uma subscrição com **atualização** não está no `allowedCustomerOperations`.

Código: 403<br>
Não autorizado. Não foi fornecido o token de autenticação, é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence ao publicador atual.

Código: 500<br>
Erro de servidor interno

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
|  authorization     |  [Obtenha o token de portador do JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Códigos de resposta:*

Código: 202<br>
Anular a subscrição a chamada de ISV iniciada para indicar uma subscrição de SaaS.<br>

Código: 404<br>
Não foi encontrado

Código: 400<br>
Eliminar uma subscrição com **elimine** não está no `allowedCustomerOperations`.

Código: 403<br>
Não autorizado. Não foi fornecido o token de autenticação, é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence ao publicador atual.

Código: 500<br>
Erro de servidor interno

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

#### <a name="list-outstanding-operations"></a>Lista de operações pendentes 

Lista as operações pendentes para o publicador atual. 

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
|  authorization     |  [Obtenha o token de portador do JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Códigos de resposta:*

Código: 200<br> Obtém a lista de operações pendentes numa subscrição.<br>
Payload de resposta:

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```

Código: 404<br>
Não foi encontrado

Código: 400<br>
Falhas de validação de pedido incorreto

Código: 403<br>
Não autorizado. Não foi fornecido o token de autenticação, é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence ao publicador atual.

Código: 500<br>
Erro de servidor interno

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>Obter estado da operação

Permite que o publicador para controlar o estado da operação assíncrona acionadas especificado (subscrever / anular a subscrição / alterar plano / Alterar quantidade).

**Get:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação para utilizar para este pedido.  |

*Cabeçalhos de pedido:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   Um valor de cadeia de caracteres exclusivo para o pedido de controlo do cliente, preferencialmente, um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid |  Um valor de cadeia de caracteres exclusivo para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação de cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  authorization     |[Obtenha o token de portador do JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Códigos de resposta:* Código: 200<br> Obtém especificado SaaS operação pendente<br>
Payload de resposta:

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

Código: 404<br>
Não foi encontrado

Código: 400<br>
Falhas de validação de pedido incorreto

Código: 403<br>
Não autorizado. Não foi fornecido o token de autenticação, é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence ao publicador atual.
 
Código: 500<br> Erro de servidor interno

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>Atualizar o estado de uma operação

Atualize o estado de uma operação para indicar êxito/falha com os valores fornecidos.

**Patch:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

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
|  authorization     |  [Obtenha o token de portador do JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Payload de pedido:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Códigos de resposta:*

Código: 200<br> Chamada para o informar de conclusão de uma operação no lado do ISV. Por exemplo, esta resposta pode assinalar a alteração dos planos/postos de trabalho.

Código: 404<br>
Não foi encontrado

Código: 400<br>
Falhas de validação de pedido incorreto

Código: 403<br>
Não autorizado. Não foi fornecido o token de autenticação, é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence ao publicador atual.

Código: 409<br>
Conflito. Por exemplo, uma transação mais recente já foi cumprida

Código: 500<br> Erro de servidor interno

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
  "id": "<this is a Guid operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher’s name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
Onde ação pode ser um dos seguintes: 
- `Subscribe`, (Quando o recurso tiver sido ativado)
- `Unsubscribe`, (Quando o recurso foi eliminado)
- `ChangePlan`, (Quando a operação de plano de alteração for concluída)
- `ChangeQuantity`, (Quando a operação de quantidade de alteração foi concluída),
- `Suspend`, (Quando recursos foi suspensa)
- `Reinstate`, (Quando recursos tem sido restabelecidos após suspensão)

Em que estado pode ser um dos seguintes: <br>
        -NotStarted, <br>
        -InProgress, <br>
        -Foi concluída com êxito, <br>
        -Falha, <br>
        -Conflitos <br>

Estados passíveis de ação são alterado com êxito e falha numa notificação de webhook. Ciclo de vida de uma operação é de NotStarted para um Estado terminal como com êxito/falha/conflito. Se receber não foi iniciada ou está em curso, continue para pedir o estado por meio da operação de obtenção de API até a operação de atinge um Estado terminal antes de efetuar qualquer ação. 

## <a name="mock-api"></a>API de teste

Pode utilizar as nossas APIs fictícios para ajudar a começar com o desenvolvimento, particularmente criação de protótipos, e projetos de teste. 

Ponto final de anfitrião: `https://marketplaceapi.microsoft.com/api` <br/>
Versão de API: `2018-09-15` <br/>
Não é necessária autenticação <br/>
Uri de exemplo: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

Os caminhos de ponto final de API são os mesmos em simulação e Real APIs, mas as versões de API são diferentes. A versão é 2018-09-15 para simulação e 2018-08-31 para a versão de produção. 

Qualquer uma das chamadas de API neste artigo podem ser feitas para o ponto de extremidade do anfitrião de simulação. Pode esperar obter dados fictícios novamente como uma resposta. Em geral, pode esperar obter dados fictícios novamente como uma resposta. Chamadas para os métodos de subscrição de atualização na API fictício devolvem sempre 500. 

## <a name="next-steps"></a>Passos Seguintes

Os desenvolvedores podem também obter automaticamente a e manipulação de cargas de trabalho, ofertas e publicador perfis utilizando o [APIs de REST do Cloud Partner Portal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
