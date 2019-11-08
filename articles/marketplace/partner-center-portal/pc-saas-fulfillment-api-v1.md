---
title: APIs de cumprimento de SaaS v1 | Azure Marketplace
description: Explica como criar e gerenciar uma oferta de SaaS no Azure Marketplace usando as APIs de cumprimento v1 associadas.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ROBOTS: NOINDEX
ms.openlocfilehash: 99dd6db7003e0358ddde2438f6897cd767932227
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816572"
---
# <a name="saas-fulfillment-apis-version-1-deprecated"></a>APIs de preenchimento de SaaS versão 1 (preterido)

Este artigo explica como criar uma oferta de SaaS com APIs. As APIs, compostas de métodos REST e pontos de extremidade, são necessárias para permitir assinaturas para sua oferta de SaaS se você tiver vendido por meio do Azure selecionado.  

> [!WARNING]
> Esta versão inicial da API de preenchimento de SaaS foi preterida; em vez disso, use a [API de preenchimento de SaaS v2](./pc-saas-fulfillment-api-v2.md).  Esta versão inicial da API está sendo mantida no momento apenas para atender aos Publicadores existentes. 

As APIs a seguir são fornecidas para ajudá-lo a integrar seu serviço SaaS com o Azure:

-   Resolver
-   Subscrever
-   Converter
-   cancelar a assinatura


## <a name="api-methods-and-endpoints"></a>Métodos de API e pontos de extremidade

As seções a seguir descrevem os métodos de API e os pontos de extremidade disponíveis para habilitar assinaturas para uma oferta de SaaS.


### <a name="marketplace-api-endpoint-and-api-version"></a>Ponto de extremidade da API do Marketplace e versão da API

O ponto de extremidade para a API do Azure Marketplace é `https://marketplaceapi.microsoft.com`.

A versão atual da API é `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Resolver assinatura

Ação POST no ponto de extremidade de resolução permite que os usuários resolvam um token do Marketplace para uma ID de recurso persistente.  A ID de recurso é o identificador exclusivo para a assinatura de SAAS. 

Quando um usuário é redirecionado para o site de um ISV, a URL contém um token nos parâmetros de consulta. O ISV deve usar esse token e fazer uma solicitação para resolvê-lo. A resposta contém a ID de assinatura SAAS exclusiva, o nome, a ID da oferta e o plano para o recurso. Esse token é válido somente por uma hora.

*Pedido*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Nome do parâmetro** |     **Descrição**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  versão da API        |  A versão da operação a ser usada para esta solicitação.   |
|  |  |


*Headers* (Cabeçalhos)

| **Chave de cabeçalho**     | **Necessário** | **Descrição**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-MS-RequestId     | Não           | Um valor de cadeia de caracteres exclusivo para acompanhar a solicitação do cliente, preferivelmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
| x-MS-CorrelationId | Não           | Um valor de cadeia de caracteres exclusivo para a operação no cliente. Esse campo correlaciona todos os eventos da operação do cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| tipo de conteúdo       | Sim          | `application/json`                                        |
| nesse      | Sim          | O token de portador JWT (token Web JSON).                    |
| x-MS-Marketplace-token| Sim| O parâmetro de consulta de token na URL quando o usuário é redirecionado para o site de ISVs do SaaS do Azure. **Observação:** Esse token só é válido por 1 hora. Além disso, a URL decodifica o valor do token do navegador antes de usá-lo.|
|  |  |  |
  

*Corpo da resposta*

``` json
{
    "id": "",
    "subscriptionName": "",
    "offerId": "",
    "planId": "",
}
```

| **Parameter name** (Nome do parâmetro) | **Tipo de dados** | **Descrição**                       |
|--------------------|---------------|---------------------------------------|
| ID                 | String        | ID da assinatura de SaaS.          |
| subscriptionName| String| Nome da assinatura de SaaS definida pelo usuário no Azure ao assinar o serviço SaaS.|
| OfferId            | String        | ID da oferta que o usuário assinou. |
| planId             | String        | ID do plano que o usuário assinou.  |
|  |  |  |


*Códigos de resposta*

| **Código de status HTTP** | **Código de erro**     | **Descrição**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | Token resolvido com êxito.                                                            |
| 400                  | `BadRequest`         | Os cabeçalhos necessários estão ausentes ou uma versão de API inválida especificada. Falha ao resolver o token porque o token está malformado ou expirou (o token só é válido por 1 hora depois de gerado). |
| 403                  | `Forbidden`          | O chamador não está autorizado a executar esta operação.                                 |
| 429                  | `RequestThrottleId`  | O serviço está ocupado processando solicitações, tente novamente mais tarde.                                |
| 503                  | `ServiceUnavailable` | O serviço está inoperante temporariamente, tente novamente mais tarde.                                        |
|  |  |  |


*Cabeçalhos de resposta*

| **Chave de cabeçalho**     | **Necessário** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Sim          | ID da solicitação recebida do cliente.                                                                   |
| x-MS-CorrelationId | Sim          | ID de correlação se passada pelo cliente; caso contrário, esse valor será a ID de correlação do servidor.                   |
| x-MS-ActivityId    | Sim          | Um valor de cadeia de caracteres exclusivo para acompanhar a solicitação do serviço. Essa ID é usada para qualquer reconciliação. |
| Repetir-após        | Não           | Esse valor é definido somente para uma resposta 429.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Subscrever

O ponto de extremidade de assinatura permite que os usuários iniciem uma assinatura para um serviço SaaS para um determinado plano e habilitem a cobrança no sistema de comércio.

**PUT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{SubscriptionId}* ? API-versão = 2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID exclusiva da assinatura de SaaS que é obtida após a resolução do token por meio da API de resolução.                              |
| versão da API         | A versão da operação a ser usada para esta solicitação. |
|  |  |

*Headers* (Cabeçalhos)

|  **Chave de cabeçalho**        | **Necessário** |  **Descrição**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-MS-RequestId         |   Não         | Um valor de cadeia de caracteres exclusivo para acompanhar a solicitação do cliente, preferivelmente um GUID. Se isso não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| x-MS-CorrelationId     |   Não         | Um valor de cadeia de caracteres exclusivo para a operação no cliente. Esse valor é para correlacionar todos os eventos da operação do cliente com eventos no lado do servidor. Se isso não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| If-Match/If-None-Match |   Não         |   Valor de ETag de validador forte.                                                          |
| tipo de conteúdo           |   Sim        |    `application/json`                                                                   |
|  nesse         |   Sim        |    O token de portador JWT (token Web JSON).                                               |
| x-MS-Marketplace-modo de sessão| Não | Sinalizador para habilitar o modo de execução seca ao assinar uma oferta de SaaS. Se definido, a assinatura não será cobrada. Isso é útil para cenários de teste de ISV. Defina-o como **' simulação '**|
|  |  |  |

*Corpo*

``` json
{
    "lanId": "",
}
```

| **Nome do elemento** | **Tipo de dados** | **Descrição**                      |
|------------------|---------------|--------------------------------------|
| planId           | Necessária Strings        | A ID do plano do usuário do serviço SaaS está assinando.  |
|  |  |  |

*Códigos de resposta*

| **Código de status HTTP** | **Código de erro**     | **Descrição**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Ativação de assinatura SaaS recebida para um determinado plano.                   |
| 400                  | `BadRequest`         | Os cabeçalhos necessários estão ausentes ou o corpo do JSON está malformado. |
| 403                  | `Forbidden`          | O chamador não está autorizado a executar esta operação.                   |
| 404                  | `NotFound`           | Assinatura não encontrada com a ID fornecida                                  |
| 409                  | `Conflict`           | Outra operação está em andamento na assinatura.                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado processando solicitações, tente novamente mais tarde.                  |
| 503                  | `ServiceUnavailable` | O serviço está inoperante temporariamente, tente novamente mais tarde.                          |
|  |  |  |

Para uma resposta de 202, acompanhe o status da operação de solicitação no cabeçalho ' Operation-location '. A autenticação é a mesma que outras APIs do Marketplace.

*Cabeçalhos de resposta*

| **Chave de cabeçalho**     | **Necessário** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Sim          | ID da solicitação recebida do cliente.                                                                   |
| x-MS-CorrelationId | Sim          | ID de correlação se passada pelo cliente; caso contrário, esse valor será a ID de correlação do servidor.                   |
| x-MS-ActivityId    | Sim          | Um valor de cadeia de caracteres exclusivo para acompanhar a solicitação do serviço. Esse valor é usado para qualquer reconciliação. |
| Repetir-após        | Sim          | Intervalo com o qual o cliente pode verificar o status.                                                       |
| Operação-localização | Sim          | Link para um recurso para obter o status da operação.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Alterar ponto de extremidade do plano

O ponto de extremidade de alteração permite que o usuário Converta seu plano assinado no momento para um novo plano.

**DISTRIBUÍDO**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{SubscriptionId}* ? API-versão = 2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID da assinatura de SaaS.                              |
| versão da API         | A versão da operação a ser usada para esta solicitação. |
|  |  |

*Headers* (Cabeçalhos)

| **Chave de cabeçalho**          | **Necessário** | **Descrição**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-MS-RequestId          | Não           | Um valor de cadeia de caracteres exclusivo para acompanhar a solicitação do cliente. Recomendar um GUID. Se isso não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.   |
| x-MS-CorrelationId      | Não           | Um valor de cadeia de caracteres exclusivo para a operação no cliente. Esse valor é para correlacionar todos os eventos da operação do cliente com eventos no lado do servidor. Se isso não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| /If-None-Match If-Match | Não           | Valor de ETag de validador forte.                              |
| tipo de conteúdo            | Sim          | `application/json`                                        |
| nesse           | Sim          | O token de portador JWT (token Web JSON).                    |
|  |  |  |

*Corpo*

```json
{
    "planId": ""
}
```

|  **Nome do elemento** |  **Tipo de dados**  | **Descrição**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId           |  Necessária Strings         | A ID do plano do usuário do serviço SaaS está assinando.          |
|  |  |  |

*Códigos de resposta*

| **Código de status HTTP** | **Código de erro**     | **Descrição**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Ativação de assinatura SaaS recebida para um determinado plano.                   |
| 400                  | `BadRequest`         | Os cabeçalhos necessários estão ausentes ou o corpo do JSON está malformado. |
| 403                  | `Forbidden`          | O chamador não está autorizado a executar esta operação.                   |
| 404                  | `NotFound`           | Assinatura não encontrada com a ID fornecida                                  |
| 409                  | `Conflict`           | Outra operação está em andamento na assinatura.                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado processando solicitações, tente novamente mais tarde.                  |
| 503                  | `ServiceUnavailable` | O serviço está inoperante temporariamente, tente novamente mais tarde.                          |
|  |  |  |

*Cabeçalhos de resposta*

| **Chave de cabeçalho**     | **Necessário** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Sim          | ID da solicitação recebida do cliente.                                                                   |
| x-MS-CorrelationId | Sim          | ID de correlação se passada pelo cliente; caso contrário, esse valor será a ID de correlação do servidor.                   |
| x-MS-ActivityId    | Sim          | Um valor de cadeia de caracteres exclusivo para acompanhar a solicitação do serviço. Esse valor é usado para qualquer reconciliação. |
| Repetir-após        | Sim          | Intervalo com o qual o cliente pode verificar o status.                                                       |
| Operação-localização | Sim          | Link para um recurso para obter o status da operação.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Excluir assinatura

A ação Excluir no ponto de extremidade de assinatura permite que um usuário exclua uma assinatura com uma determinada ID.

*Pedido*

**DELETE**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{SubscriptionId}* ? API-versão = 2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID da assinatura de SaaS.                              |
| versão da API         | A versão da operação a ser usada para esta solicitação. |
|  |  |

*Headers* (Cabeçalhos)

| **Chave de cabeçalho**     | **Necessário** | **Descrição**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-MS-RequestId     | Não           | Um valor de cadeia de caracteres exclusivo para acompanhar a solicitação do cliente. Recomendar um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.                                                           |
| x-MS-CorrelationId | Não           | Um valor de cadeia de caracteres exclusivo para a operação no cliente. Esse valor é para correlacionar todos os eventos da operação do cliente com eventos no lado do servidor. Se isso não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| nesse      | Sim          | O token de portador JWT (token Web JSON).                    |
|  |  |  |

*Códigos de resposta*

| **Código de status HTTP** | **Código de erro**     | **Descrição**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Ativação de assinatura SaaS recebida para um determinado plano.                   |
| 400                  | `BadRequest`         | Os cabeçalhos necessários estão ausentes ou o corpo do JSON está malformado. |
| 403                  | `Forbidden`          | O chamador não está autorizado a executar esta operação.                   |
| 404                  | `NotFound`           | Assinatura não encontrada com a ID fornecida                                  |
| 429                  | `RequestThrottleId`  | O serviço está ocupado processando solicitações, tente novamente mais tarde.                  |
| 503                  | `ServiceUnavailable` | O serviço está inoperante temporariamente. Tente novamente mais tarde.                          |
|  |  |  |

Para uma resposta de 202, acompanhe o status da operação de solicitação no cabeçalho ' Operation-location '. A autenticação é a mesma que outras APIs do Marketplace.

*Cabeçalhos de resposta*

| **Chave de cabeçalho**     | **Necessário** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Sim          | ID da solicitação recebida do cliente.                                                                   |
| x-MS-CorrelationId | Sim          | ID de correlação se passada pelo cliente, caso contrário, essa é a ID de correlação do servidor.                   |
| x-MS-ActivityId    | Sim          | Um valor de cadeia de caracteres exclusivo para acompanhar a solicitação do serviço. Isso é usado para qualquer reconciliação. |
| Repetir-após        | Sim          | Intervalo com o qual o cliente pode verificar o status.                                                       |
| Operação-localização | Sim          | Link para um recurso para obter o status da operação.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Obter status da operação

Esse ponto de extremidade permite que o usuário acompanhe o status de uma operação assíncrona disparada (assinar/cancelar assinatura/Alterar plano).

*Pedido*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/operations/ *{operationId}* ? API-versão = 2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | ID exclusiva para a operação disparada.                |
| versão da API         | A versão da operação a ser usada para esta solicitação. |
|  |  |

*Headers* (Cabeçalhos)

| **Chave de cabeçalho**     | **Necessário** | **Descrição**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Não           | Um valor de cadeia de caracteres exclusivo para acompanhar a solicitação do cliente. Recomendar um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.   |
| x-MS-CorrelationId | Não           | Um valor de cadeia de caracteres exclusivo para a operação no cliente. Esse valor é para correlacionar todos os eventos da operação do cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
| nesse      | Sim          | O token de portador JWT (token Web JSON).                    |
|  |  |  | 

*Corpo da resposta*

```json
{
    "id": "",
    "status": "",
    "resourceLocation": "",
    "created": "",
    "lastModified": ""
}
```

| **Parameter name** (Nome do parâmetro) | **Tipo de dados** | **Descrição**                                                                                                                                               |
|--------------------|---------------|-------------------------------------------------------------------------------------------|
| ID                 | String        | ID da operação.                                                                      |
| status             | Enum          | Status da operação, um dos seguintes: `In Progress`, `Succeeded`ou `Failed`.          |
| resourceLocation   | String        | Link para a assinatura que foi criada ou modificada. Isso ajuda o cliente a obter a operação de postagem do estado atualizado. Este valor não está definido para operações de `Unsubscribe`. |
| criação            | DateTime      | Hora de criação da operação em UTC.                                                           |
| lastModified       | DateTime      | Última atualização na operação em UTC.                                                      |
|  |  |  |

*Códigos de resposta*

| **Código de status HTTP** | **Código de erro**     | **Descrição**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | A solicitação get foi resolvida com êxito e o corpo contém a resposta.    |
| 400                  | `BadRequest`         | Os cabeçalhos necessários estão ausentes ou uma versão de API inválida foi especificada. |
| 403                  | `Forbidden`          | O chamador não está autorizado a executar esta operação.                      |
| 404                  | `NotFound`           | Assinatura não encontrada com a ID fornecida.                                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado processando solicitações, tente novamente mais tarde.                     |
| 503                  | `ServiceUnavailable` | O serviço está inoperante temporariamente, tente novamente mais tarde.                             |
|  |  |  |

*Cabeçalhos de resposta*

| **Chave de cabeçalho**     | **Necessário** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Sim          | ID da solicitação recebida do cliente.                                                                   |
| x-MS-CorrelationId | Sim          | ID de correlação se passada pelo cliente, caso contrário, essa é a ID de correlação do servidor.                   |
| x-MS-ActivityId    | Sim          | Um valor de cadeia de caracteres exclusivo para acompanhar a solicitação do serviço. Isso é usado para qualquer reconciliação. |
| Repetir-após        | Sim          | Intervalo com o qual o cliente pode verificar o status.                                                       |
|  |  |  |

### <a name="get-subscription"></a>Obter assinatura

A ação Get no ponto de extremidade de assinatura permite que um usuário recupere uma assinatura com um determinado identificador de recurso.

*Pedido*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{SubscriptionId}* ? API-versão = 2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID da assinatura de SaaS.                              |
| versão da API         | A versão da operação a ser usada para esta solicitação. |
|  |  |

*Headers* (Cabeçalhos)

| **Chave de cabeçalho**     | **Necessário** | **Descrição**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Não           | Um valor de cadeia de caracteres exclusivo para acompanhar a solicitação do cliente, preferivelmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.                                                           |
| x-MS-CorrelationId | Não           | Um valor de cadeia de caracteres exclusivo para a operação no cliente. Esse valor é para correlacionar todos os eventos da operação do cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| nesse      | Sim          | O token de portador JWT (token Web JSON).                                                                    |
|  |  |  |

*Corpo da resposta*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Parameter name** (Nome do parâmetro)     | **Tipo de dados** | **Descrição**                               |
|------------------------|---------------|-----------------------------------------------|
| ID                     | String        | ID do recurso de assinatura SaaS no Azure.    |
| OfferId                | String        | ID da oferta que o usuário assinou.         |
| planId                 | String        | ID do plano que o usuário assinou.          |
| saasSubscriptionName   | String        | Nome da assinatura de SaaS.                |
| saasSubscriptionStatus | Enum          | Status da operação.  Um dos seguintes:  <br/> - `Subscribed`: a assinatura está ativa.  <br/> - `Pending`: o usuário cria o recurso, mas não é ativado pelo ISV.   <br/> - `Unsubscribed`: o usuário cancelou a assinatura.   <br/> - `Suspended`: o usuário suspendeu a assinatura.   <br/> - `Deactivated`: a assinatura do Azure está suspensa.  |
| criação                | DateTime      | Valor de carimbo de data/hora de criação da assinatura em UTC. |
| lastModified           | DateTime      | Valor de carimbo de data/hora modificado pela assinatura em UTC. |
|  |  |  |

*Códigos de resposta*

| **Código de status HTTP** | **Código de erro**     | **Descrição**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | A solicitação get foi resolvida com êxito e o corpo contém a resposta.    |
| 400                  | `BadRequest`         | Os cabeçalhos necessários estão ausentes ou uma versão de API inválida foi especificada. |
| 403                  | `Forbidden`          | O chamador não está autorizado a executar esta operação.                      |
| 404                  | `NotFound`           | Assinatura não encontrada com a ID fornecida                                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado processando solicitações, tente novamente mais tarde.                     |
| 503                  | `ServiceUnavailable` | O serviço está inoperante temporariamente, tente novamente mais tarde.                             |
|  |  |  |

*Cabeçalhos de resposta*

| **Chave de cabeçalho**     | **Necessário** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Sim          | ID da solicitação recebida do cliente.                                                                   |
| x-MS-CorrelationId | Sim          | ID de correlação se passada pelo cliente, caso contrário, essa é a ID de correlação do servidor.                   |
| x-MS-ActivityId    | Sim          | Um valor de cadeia de caracteres exclusivo para acompanhar a solicitação do serviço. Isso é usado para qualquer reconciliação. |
| Repetir-após        | Não           | Intervalo com o qual o cliente pode verificar o status.                                                       |
| eTag               | Sim          | Link para um recurso para obter o status da operação.                                                        |
|  |  |  |

### <a name="get-subscriptions"></a>Obter assinaturas

A ação Get no ponto de extremidade de assinaturas permite que um usuário recupere todas as assinaturas para todas as ofertas do ISV.

*Pedido*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| versão da API         | A versão da operação a ser usada para esta solicitação. |
|  |  |

*Headers* (Cabeçalhos)

| **Chave de cabeçalho**     | **Necessário** | **Descrição**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-MS-RequestId     | Não           | Um valor de cadeia de caracteres exclusivo para acompanhar a solicitação do cliente. Recomendar um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.             |
| x-MS-CorrelationId | Não           | Um valor de cadeia de caracteres exclusivo para a operação no cliente. Esse valor é para correlacionar todos os eventos da operação do cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| nesse      | Sim          | O token de portador JWT (token Web JSON).                    |
|  |  |  |

*Corpo da resposta*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Parameter name** (Nome do parâmetro)     | **Tipo de dados** | **Descrição**                               |
|------------------------|---------------|-----------------------------------------------|
| ID                     | String        | ID do recurso de assinatura SaaS no Azure    |
| OfferId                | String        | ID da oferta à qual o usuário se inscreveu         |
| planId                 | String        | ID do plano para o qual o usuário se inscreveu          |
| saasSubscriptionName   | String        | Nome da assinatura de SaaS                |
| saasSubscriptionStatus | Enum          | Status da operação.  Um dos seguintes:  <br/> - `Subscribed`: a assinatura está ativa.  <br/> - `Pending`: o usuário cria o recurso, mas não é ativado pelo ISV.   <br/> - `Unsubscribed`: o usuário cancelou a assinatura.   <br/> - `Suspended`: o usuário suspendeu a assinatura.   <br/> - `Deactivated`: a assinatura do Azure está suspensa.  |
| criação                | DateTime      | Valor timestamp de criação da assinatura em UTC |
| lastModified           | DateTime      | Valor de carimbo de data/hora modificado pela assinatura em UTC |
|  |  |  |

*Códigos de resposta*

| **Código de status HTTP** | **Código de erro**     | **Descrição**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | A solicitação get foi resolvida com êxito e o corpo contém a resposta.    |
| 400                  | `BadRequest`         | Os cabeçalhos necessários estão ausentes ou uma versão de API inválida foi especificada. |
| 403                  | `Forbidden`          | O chamador não está autorizado a executar esta operação.                      |
| 404                  | `NotFound`           | Assinatura não encontrada com a ID fornecida                                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado processando solicitações, tente novamente mais tarde.                     |
| 503                  | `ServiceUnavailable` | O serviço está inoperante temporariamente. Tente novamente mais tarde.                             |
|  |  |  |

*Cabeçalhos de resposta*

| **Chave de cabeçalho**     | **Necessário** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Sim          | ID da solicitação recebida do cliente.                                                                   |
| x-MS-CorrelationId | Sim          | ID de correlação se passada pelo cliente, caso contrário, essa é a ID de correlação do servidor.                   |
| x-MS-ActivityId    | Sim          | Um valor de cadeia de caracteres exclusivo para acompanhar a solicitação do serviço. Isso é usado para qualquer reconciliação. |
| Repetir-após        | Não           | Intervalo com o qual o cliente pode verificar o status.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>Webhook SaaS

Um webhook SaaS é usado para notificar as alterações proativamente para o serviço SaaS. Espera-se que essa API POST não seja autenticada e seja chamada pelo serviço da Microsoft. Espera-se que o serviço SaaS chame a API de operações para validar e autorizar antes de tomar medidas na notificação de webhook. 

*Corpo*

``` json
  {
    "id": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "action": "Subscribe", // Subscribe/Unsubscribe/ChangePlan
    "operationRequestSource":"Azure",
    "timeStamp":"2018-12-01T00:00:00"
  }
```

| **Parameter name** (Nome do parâmetro)     | **Tipo de dados** | **Descrição**                               |
|------------------------|---------------|-----------------------------------------------|
| ID  | String       | ID exclusiva para a operação disparada.                |
| ActivityId   | String        | Um valor de cadeia de caracteres exclusivo para acompanhar a solicitação do serviço. Isso é usado para qualquer reconciliação.               |
| subscriptionId                     | String        | ID do recurso de assinatura SaaS no Azure.    |
| OfferId                | String        | ID da oferta que o usuário assinou. Fornecido somente com a ação "atualizar".        |
| publisherId                | String        | ID do editor da oferta de SaaS         |
| planId                 | String        | ID do plano que o usuário assinou. Fornecido somente com a ação "atualizar".          |
| action                 | String        | A ação que está disparando esta notificação. Valores possíveis-ativar, excluir, suspender, reaplicar, atualizar          |
| Estampa                 | String        | Valor de carimbo de data/hora em UTC quando esta notificação foi disparada.          |
|  |  |  |


## <a name="next-steps"></a>Passos seguintes

Os desenvolvedores também podem recuperar e manipular programaticamente cargas de trabalho, ofertas e perfis de Publicador usando as [APIs REST do portal do Cloud Partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
