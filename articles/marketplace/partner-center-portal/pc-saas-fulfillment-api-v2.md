---
title: Realização do SaaS API v2  Mercado Azure
description: Este artigo explica como criar e gerir uma oferta SaaS no AppSource e Azure Marketplace utilizando o cumprimento associado v2 APIs.
services: Azure, Marketplace, Cloud Partner Portal,
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/18/2019
ms.author: evansma
ms.openlocfilehash: c0d5deef609796501515fc9c7064a96ca1419b3a
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371533"
---
# <a name="saas-fulfillment-apis-version-2"></a>ApIs de cumprimento saaS, versão 2 

Este artigo detalha as APIs que permitem aos parceiros vender as suas aplicações SaaS no mercado appSource e no Azure Marketplace. Estas APIs são um requisito para ofertas transacionáveis do SaaS no AppSource e no Azure Marketplace.

## <a name="managing-the-saas-subscription-life-cycle"></a>Gestão do ciclo de vida da subscrição saaS

A Azure SaaS gere todo o ciclo de vida de uma compra por subscrição SaaS. Utiliza as APIs de realização como um mecanismo para impulsionar o cumprimento real, alterações nos planos e eliminação da subscrição com o parceiro. A conta do cliente baseia-se no estado da subscrição saaS que a Microsoft mantém. O diagrama seguinte retrata os Estados e as operações que impulsionam as mudanças entre estados.

![Estados de ciclo de vida por subscrição saaS](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Estados de uma subscrição SaaS

A tabela seguinte lista os estados de provisionamento para uma subscrição SaaS, incluindo um diagrama de descrição e sequência para cada um (se aplicável). 

#### <a name="provisioning"></a>Aprovisionamento

Quando um cliente inicia uma compra, o parceiro recebe esta informação num código de autorização numa página web interativa do cliente que utiliza um parâmetro URL. Um exemplo é `https://contoso.com/signup?token=..`, enquanto o URL da página de aterragem no Partner Center é `https://contoso.com/signup`. O código de autorização pode ser validado e trocado pelos detalhes do serviço de provisionamento, ligando para a API resolve.  Quando um serviço SaaS termina o fornecimento, envia uma chamada ativa para sinalizar que o cumprimento está completo e que o cliente pode ser cobrado. 

O diagrama seguinte mostra a sequência de Chamadas API para um cenário de provisionamento.  

![API apela ao fornecimento de um serviço SaaS](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Provisionado

Este estado é o estado estável de um serviço prestado.

##### <a name="provisioning-for-update"></a>Provisionamento para atualização 

Este estado significa que está pendente uma atualização para um serviço existente. Esta atualização pode ser iniciada pelo cliente, quer a partir do mercado quer no serviço SaaS (apenas para transações diretas ao cliente).

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>Provisionamento para atualização (quando é iniciado a partir do mercado)

O diagrama seguinte mostra a sequência de ações quando uma atualização é iniciada a partir do mercado.

![API chama quando a atualização é iniciada a partir do mercado](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>Provisionamento para atualização (quando é iniciado a partir do serviço SaaS)

O diagrama seguinte mostra as ações quando uma atualização é iniciada a partir do serviço SaaS. (A chamada webhook é substituída por uma atualização da subscrição iniciada pelo serviço SaaS.) 

![API liga quando a atualização é iniciada a partir do serviço SaaS](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Suspenso

Este estado indica que o pagamento de um cliente não foi recebido. Por política, forneceremos ao cliente um período de carência antes de cancelar a subscrição. Quando uma subscrição está neste estado: 

- Como parceiro, pode optar por degradar ou bloquear o acesso do utilizador ao serviço.
- A subscrição deve ser mantida num estado recuperável que possa restaurar a funcionalidade completa sem qualquer perda de dados ou configurações. 
- Espere obter um pedido de reintegração para esta subscrição através das APIs de cumprimento ou de um pedido de desprovisionamento no final do período de carência. 

#### <a name="unsubscribed"></a>Sem subscrição 

As subscrições chegam a este estado em resposta a um pedido explícito do cliente ou ao não pagamento de dívidas. A expectativa do parceiro é que os dados do cliente sejam retidos para recuperação a pedido de um determinado número de dias e depois eliminados. 


## <a name="api-reference"></a>Referência da API

Esta secção documenta a API de *Subscrição* SaaS e API de *Operações.*  O valor do parâmetro `api-version` para as APIs da versão 2 é `2018-08-31`.  


### <a name="parameter-and-entity-definitions"></a>Definições de parâmetros e entidades

A tabela seguinte enumera as definições para parâmetros e entidades comuns utilizadas por APIs de cumprimento.

|     Entidade/Parâmetro     |     Definição                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | O identificador GUID para um recurso SaaS.  |
| `name`                   | Um nome amigável fornecido para este recurso pelo cliente. |
| `publisherId`            | Um identificador de cordas único para cada editor (por exemplo: "contoso"). |
| `offerId`                | Um identificador de cordas único para cada oferta (por exemplo: "oferta1").  |
| `planId`                 | Um identificador de cordas único para cada plano/SKU (por exemplo: "prata"). |
| `operationId`            | O identificador GUID para uma determinada operação.  |
|  `action`                | A ação que está a ser realizada com recurso, quer `Unsubscribe`, `Suspend`, `Reinstate`, ou `ChangePlan`, `ChangeQuantity`, `Transfer`. |
|   |   |

Identificadores globalmente únicos[(GUIDs)](https://en.wikipedia.org/wiki/Universally_unique_identifier)são números de 128 bits (32-hexadecimal) que são tipicamente gerados automaticamente. 

#### <a name="resolve-a-subscription"></a>Resolver uma subscrição 

O ponto final de resolução permite à editora resolver um símbolo de mercado para um ID de recursos persistente. O ID de recurso é o identificador único para uma subscrição SaaS. Quando um utilizador é redirecionado para o website de um parceiro, o URL contém um símbolo nos parâmetros de consulta. Espera-se que o parceiro utilize este símbolo e faça um pedido para resolvê-lo. A resposta contém o ID de subscrição exclusivo do SaaS, nome, id de oferta e plano para o recurso. Este token é válido por apenas uma hora. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Publicar<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação a utilizar para este pedido.  |

*Cabeçalhos de pedido:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo de conteúdo      | `application/json` |
|  x-ms-requestid    |  Um valor de cadeia único para acompanhar o pedido do cliente, de preferência um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  x-ms-correlationid |  Um valor de cadeia único para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  authorization     |  Obtenha ficha do portador da [JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Por exemplo: "`Bearer <access_token>`". |
|  x-ms-marketplace-token  |  O parâmetro de consulta simbólica no URL quando o utilizador é redirecionado para o website do parceiro SaaS a partir do Azure (por exemplo: `https://contoso.com/signup?token=..`). *Nota:* O URL descodifica o valor simbólico do navegador antes de o utilizar.  |

*Códigos de resposta:*

Código: 200<br>
Resolve o token opaco para uma assinatura SaaS. Corpo de resposta:
 

```json
{
    "id": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

Código: 400<br>
Mau pedido. x-ms-marketplace-token está desaparecido, mal formado ou expirado.

Código: 403<br>
Não autorizado. O símbolo de autenticação não foi fornecido ou é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence à editora atual.

Código: 404<br>
Não foi encontrado.

Código: 500<br>
Erro interno do servidor.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>API de subscrição

A API de subscrição suporta as seguintes operações HTTPS: **Get,** **Post,** **Patch**, and **Delete**.


#### <a name="list-subscriptions"></a>Lista de assinaturas

Lista todas as subscrições do SaaS para um editor.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Get<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  A versão da operação a utilizar para este pedido.  |

*Cabeçalhos de pedido:*

|                    |                   |
|  ---------------   |  ---------------  |
| Tipo de conteúdo       |  `application/json`  |
| x-ms-requestid     |  Um valor de cadeia único para acompanhar o pedido do cliente, de preferência um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| x-ms-correlationid |  Um valor de cadeia único para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
| authorization      |  Obtenha ficha do portador da [JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Por exemplo: "`Bearer <access_token>`".  |

*Códigos de resposta:*

Código: 200 <br/>
Obtém a editora e subscrições correspondentes para todas as ofertas da editora, com base no símbolo de autenticação.

>[!Note]
>[As APIs falsas](#mock-apis) são usadas quando desenvolve si pela primeira vez a oferta, enquanto as APIs reais precisam de ser usadas na publicação da oferta.  APIs reais e APIs falsos diferem pela primeira linha do código.  Na verdadeira API existe a secção `subscription`, enquanto esta secção não existe para simular a API.

Carga útil de resposta para a API falsa:<br>

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
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": "true", // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```
E para a API real: <br>

```json
{
  "subscriptions": [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant, object id and email address for which SaaS subscription is purchased.
              "emailId": "<email>",
              "objectId": "<guid>",                     
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant, object id and email address that purchased the SaaS subscription. These could be different for reseller scenario
              "emailId": "<email>",
              "objectId": "<guid>",                      
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.(optional field - default false)
          "isTest": false, //indicating whether the current subscription is a test asset
          "sandboxType": "None", // Possible Values: None, Csp (Csp sandbox purchase)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "@nextLink": ""
}
```
O símbolo de continuação só estará presente se houver "páginas" adicionais de planos para recuperar. 

Código: 403 <br>
Não autorizado. O símbolo de autenticação não foi fornecido ou é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence à editora atual. 

Código: 500<br>
Erro interno do servidor.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>Obtenha subscrição

Obtém a assinatura SaaS especificada. Use esta chamada para obter informações de licença e planejar informações.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Um identificador único da subscrição SaaS que é obtido após a resolução do símbolo via Resolve API.   |
|  ApiVersion        |   A versão da operação a utilizar para este pedido.   |

*Cabeçalhos de pedido:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo de conteúdo      |  `application/json`  |
|  x-ms-requestid    |  Um valor de cadeia único para acompanhar o pedido do cliente, de preferência um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  x-ms-correlationid |  Um valor de cadeia único para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  authorization     |  Obtenha ficha do portador da [JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Por exemplo: "`Bearer <access_token>`".  |

*Códigos de resposta:*

Código: 200<br>
Obtém a assinatura SaaS do identificador. Carga útil de resposta:<br>

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
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "isFreeTrial": "true", // true - customer subscription is currently in free trial, false - customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M" //where P1M: Monthly, P1Y: Yearly 
        },
}
```

Código: 403<br>
Não autorizado. O símbolo de autenticação não foi fornecido ou é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence à editora atual.

Código: 404<br>
Não foi encontrado.<br> 

Código: 500<br>
Erro interno do servidor.<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Lista de planos disponíveis

Use esta chamada para saber se existem ofertas privadas ou públicas para o editor atual.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   A versão da operação a utilizar para este pedido.  |

*Cabeçalhos de pedido:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Tipo de conteúdo     |  `application/json` |
|   x-ms-requestid   |   Um valor de cadeia único para acompanhar o pedido do cliente, de preferência um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  x-ms-correlationid  | Um valor de cadeia único para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  authorization     |  Obtenha ficha do portador da [JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Por exemplo: "`Bearer <access_token>`". |

*Códigos de resposta:*

Código: 200<br>
Obtém uma lista dos planos disponíveis para um cliente. Corpo de resposta:

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
Não foi encontrado.<br> 

Código: 403<br>
Não autorizado. O símbolo de autenticação não foi fornecido ou é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence à editora atual. <br> 

Código: 500<br>
Erro interno do servidor.<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>Ativar uma subscrição

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Publicar<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação a utilizar para este pedido.  |
| subscriptionId     | Um identificador único da subscrição SaaS que é obtido após a resolução do símbolo usando a API Resolve.  |

*Cabeçalhos de pedido:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo de conteúdo      | `application/json`  |
|  x-ms-requestid    | Um valor de cadeia único para acompanhar o pedido do cliente, de preferência um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid  | Um valor de cadeia único para a operação no cliente. Esta cadeia correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  authorization     |  Obtenha ficha do portador da [JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Por exemplo: "`Bearer <access_token>`". |

*Solicitar carga útil:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Códigos de resposta:*

Código: 200<br>
Ativa a subscrição.<br>

Código: 400<br>
Mau pedido: falhas de validação.

Código: 403<br>
Não autorizado. O símbolo de autenticação não foi fornecido ou é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence à editora atual.

Código: 404<br>
Não foi encontrado.

Código: 500<br>
Erro interno do servidor.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>Alterar o plano sobre a subscrição

Atualize o plano sobre a subscrição.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação a utilizar para este pedido.  |
| subscriptionId     | Um identificador único da subscrição SaaS que é obtido após a resolução do símbolo usando a API Resolve.  |

*Cabeçalhos de pedido:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo de conteúdo      | `application/json` |
|  x-ms-requestid    |   Um valor de cadeia único para acompanhar o pedido do cliente, de preferência um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid  |  Um valor de cadeia único para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.    |
| authorization      |  Obtenha ficha do portador da [JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Por exemplo: "`Bearer <access_token>`".  |

*Solicitar carga útil:*

```json
Request Body:
{
    "planId": "gold"
}
```

*Cabeçalhos de pedido:*

|                    |                   |
|  ---------------   |  ---------------  |
| Localização da Operação | A ligação a um recurso para obter o estado da operação.   |

*Códigos de resposta:*

Código: 202<br>
O pedido de alteração do plano foi aceite. Espera-se que o parceiro investigue a Operação-Localização para determinar um sucesso ou falha. <br>

Código: 400<br>
Mau pedido: falhas de validação.

Código: 403<br>
Não autorizado. O símbolo de autenticação não foi fornecido ou é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence à editora atual.

Código: 404<br>
Não foi encontrado.

Código: 500<br>
Erro interno do servidor.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Apenas um plano ou quantidade pode ser remendado de uma só vez, não ambos. As edas numa subscrição com **Update** não estão em `allowedCustomerOperations`.

#### <a name="change-the-quantity-on-the-subscription"></a>Alterar a quantidade na subscrição

Atualize a quantidade na subscrição.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação a utilizar para este pedido.  |
| subscriptionId     | Um identificador único da subscrição SaaS que é obtido após a resolução do símbolo usando a API Resolve.  |

*Cabeçalhos de pedido:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo de conteúdo      | `application/json` |
|  x-ms-requestid    |   Um valor de cadeia único para acompanhar o pedido do cliente, de preferência um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid  |  Um valor de cadeia único para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.    |
| authorization      |  Obtenha ficha do portador da [JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Por exemplo: "`Bearer <access_token>`".  |

*Solicitar carga útil:*

```json
Request Body:
{
    "quantity": 5
}
```

*Cabeçalhos de pedido:*

|                    |                   |
|  ---------------   |  ---------------  |
| Localização da Operação | Ligue-se a um recurso para obter o estado da operação.   |

*Códigos de resposta:*

Código: 202<br>
O pedido de alteração da quantidade foi aceite. Espera-se que o parceiro investigue a Operação-Localização para determinar um sucesso ou falha. <br>

Código: 400<br>
Mau pedido: falhas de validação.


Código: 403<br>
Não autorizado. O símbolo de autenticação não foi fornecido ou é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence à editora atual.

Código: 404<br>
Não foi encontrado.

Código: 500<br>
Erro interno do servidor.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Apenas um plano ou quantidade pode ser remendado de uma só vez, não ambos. As edas numa subscrição com **Update** não estão em `allowedCustomerOperations`.

#### <a name="delete-a-subscription"></a>Eliminar uma subscrição

Cancelar a subscrição e apagar a subscrição especificada.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Eliminar<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação a utilizar para este pedido.  |
| subscriptionId     | Um identificador único da subscrição SaaS que é obtido após a resolução do símbolo usando a API Resolve.  |

*Cabeçalhos de pedido:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Tipo de conteúdo     |  `application/json` |
|  x-ms-requestid    |   Um valor de cadeia único para acompanhar o pedido do cliente, de preferência um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.   |
|  x-ms-correlationid  |  Um valor de cadeia único para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.   |
|  authorization     |  Obtenha ficha do portador da [JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Por exemplo: "`Bearer <access_token>`".  |

*Códigos de resposta:*

Código: 202<br>
O parceiro iniciou uma chamada para cancelar a subscrição do SaaS.<br>

Código: 400<br>
Apagar numa subscrição com **delete** não em `allowedCustomerOperations`.

Código: 403<br>
Não autorizado. O símbolo de autenticação não foi fornecido ou é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence à editora atual.

Código: 404<br>
Não foi encontrado.

Código: 500<br>
Erro interno do servidor.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>Operações API

As operações a API suportam as seguintes operações patch e Get.

#### <a name="list-outstanding-operations"></a>Lista de operações pendentes 

Lista as operações pendentes para o atual editor. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   A versão da operação a utilizar para este pedido.                |
| subscriptionId     | Um identificador único da subscrição SaaS que é obtido após a resolução do símbolo usando a API Resolve.  |

*Cabeçalhos de pedido:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Tipo de conteúdo     |  `application/json` |
|  x-ms-requestid    |  Um valor de cadeia único para acompanhar o pedido do cliente, de preferência um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid |  Um valor de cadeia único para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  authorization     |  Obtenha ficha do portador da [JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Por exemplo: "`Bearer <access_token>`".  |

*Códigos de resposta:*

Código: 200<br> Obtém a lista de operações pendentes numa subscrição. Carga útil de resposta:

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


Código: 400<br>
Mau pedido: falhas de validação.

Código: 403<br>
Não autorizado. O símbolo de autenticação não foi fornecido ou é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence à editora atual.

Código: 404<br>
Não foi encontrado.

Código: 500<br>
Erro interno do servidor.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>Obtenha o estado de operação

Permite ao editor acompanhar o estado da operação de asincronização desencadeada especificada (como `Subscribe`, `Unsubscribe`, `ChangePlan`ou `ChangeQuantity`).

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação a utilizar para este pedido.  |

*Cabeçalhos de pedido:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo de conteúdo      |  `application/json`   |
|  x-ms-requestid    |   Um valor de cadeia único para acompanhar o pedido do cliente, de preferência um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid |  Um valor de cadeia único para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  authorization     |  Obtenha ficha do portador da [JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Por exemplo: "`Bearer <access_token>`".  |

*Códigos de resposta:*<br>

Código: 200<br> Obtém a operação saaS especificada pendente. Carga útil de resposta:

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

Código: 400<br>
Mau pedido: falhas de validação.

Código: 403<br>
Não autorizado. O símbolo de autenticação não foi fornecido ou é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence à editora atual.
 
Código: 404<br>
Não foi encontrado.

Código: 500<br> Erro interno do servidor.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>Atualizar o estado de uma operação

Atualize o estado de uma operação para indicar sucesso ou falha com os valores fornecidos.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  A versão da operação a utilizar para este pedido.  |
| subscriptionId     | Um identificador único da subscrição SaaS que é obtido após a resolução do símbolo usando a API Resolve.  |
|  operationId       | A operação que está a ser concluída. |

*Cabeçalhos de pedido:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Tipo de conteúdo     | `application/json`   |
|   x-ms-requestid   |   Um valor de cadeia único para acompanhar o pedido do cliente, de preferência um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  x-ms-correlationid |  Um valor de cadeia único para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  authorization     |  Obtenha ficha do portador da [JSON (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Por exemplo: "`Bearer <access_token>`".  |

*Solicitar carga útil:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Códigos de resposta:*

Código: 200<br> Uma chamada para informar da conclusão de uma operação do lado do parceiro. Por exemplo, esta resposta pode sinalizar a mudança de lugares ou planos.

Código: 400<br>
Mau pedido: falhas de validação.

Código: 403<br>
Não autorizado. O símbolo de autenticação não foi fornecido ou é inválido, ou o pedido está a tentar aceder a uma aquisição que não pertence à editora atual.

Código: 404<br>
Não foi encontrado.

Código: 409<br>
Um conflito. Por exemplo, uma transação mais recente já está preenchida.

Código: 500<br> Erro interno do servidor.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implementação de um webhook no serviço SaaS

A editora deve implementar um webhook neste serviço SaaS para notificar proativamente os utilizadores das alterações no seu serviço. Espera-se que o serviço SaaS ligue para as operações API para validar e autorizar antes de tomar uma ação na notificação do webhook.


```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher's name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
Sempre que a ação possa ser uma das seguintes: 
- `Unsubscribe` (quando o recurso tiver sido eliminado)
- `ChangePlan` (quando a operação do plano de mudança estiver concluída)
- `ChangeQuantity` (quando a operação de alteração da quantidade estiver concluída)
- `Suspend` (quando o recurso tiver sido suspenso)
- `Reinstate` (quando o recurso tiver sido reintegrado após a suspensão)

Sempre que o estatuto possa ser um dos seguintes: 
- **Não Iniciado** <br>
 - **Inprogress** <br>
- **Sucedido** <br>
- **Falhou** <br>
- **Conflito** <br>

Numa notificação webhook, os status acionáveis são **bem sucedidos** e **falhados**. O ciclo de vida de uma operação é de **NotStarted** para um estado terminal como **Succeeded**, **Failed**, ou **Conflict**. Se receber **O NãoIniciado** ou **inProgress,** continue a solicitar o estatuto através da API GET até que a operação chegue a um estado terminal antes de tomar medidas. 

## <a name="mock-apis"></a>Mock APIs

Você pode usar as nossas APIs falsas para ajudá-lo a começar com o desenvolvimento, particularmente prototipagem, bem como projetos de teste. 

Ponto final do hospedeiro: `https://marketplaceapi.microsoft.com/api` (não é necessária autenticação)<br/>
Versão API: `2018-09-15`<br/>
Amostra URI: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

Os caminhos finais da API são os mesmos tanto em simulação como em APIs reais, mas as versões API são diferentes. A versão está `2018-09-15` para a versão mock e `2018-08-31` para a versão de produção. 

Qualquer uma das chamadas da API neste artigo pode ser feita para o ponto final do anfitrião simulado. Em geral, espere obter dados falsos de volta como resposta. As chamadas para os métodos de subscrição de atualização na API falsa saem sempre 500. 

## <a name="next-steps"></a>Passos seguintes

Os desenvolvedores também podem recuperar e manipular programáticamente cargas de trabalho, ofertas e perfis de editor, utilizando o Portal de Parceiros [cloud REST APIs](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
