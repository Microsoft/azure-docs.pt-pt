---
title: SaaS Realização APIs v1 [ Mercado Azure
description: Explica como criar e gerir uma oferta SaaS no Mercado Azure utilizando as APIs v1 de Realização associadas.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 05/23/2019
ms.author: dsindona
ROBOTS: NOINDEX
ms.openlocfilehash: 3ec8373288a2ea5809ee5d349c52c57051586035
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80288347"
---
# <a name="saas-fulfillment-apis-version-1-deprecated"></a>SaaS Fulfillment APIs versão 1 (depreciada)

Este artigo explica como criar uma oferta SaaS com APIs. As APIs, compostas por métodos REST e pontos finais, são necessárias para permitir subscrições da sua oferta SaaS se tiver sell através do Azure selecionado.  

> [!WARNING]
> Esta versão inicial da API de Realização do SaaS é depreciada; em vez disso, utilize [saas Fulfillment API V2](./pc-saas-fulfillment-api-v2.md).  Esta versão inicial da API está atualmente a ser mantida apenas para servir os editores existentes. 

São fornecidas as seguintes APIs para ajudá-lo a integrar o seu serviço SaaS com o Azure:

-   Resolver
-   Subscrever
-   Converter
-   Anular a subscrição


## <a name="api-methods-and-endpoints"></a>Métodos e pontos finais da API

As seguintes secções descrevem os métodos e pontos finais da API disponíveis para permitir subscrições para uma oferta SaaS.


### <a name="marketplace-api-endpoint-and-api-version"></a>Versão final do Marketplace API e DaPI

O ponto final da API `https://marketplaceapi.microsoft.com`azure marketplace é .

A versão Atual `api-version=2017-04-15`da API é .


### <a name="resolve-subscription"></a>Resolver a subscrição

A ação post on resolve endpoint permite que os utilizadores resolvam um símbolo de mercado para um ID de recursos persistente.  O Id de recurso é o identificador único para a subscrição sAAS. 

Quando um utilizador é redirecionado para o website de um ISV, o URL contém um símbolo nos parâmetros de consulta. Espera-se que o ISV utilize este símbolo e faça um pedido para resolvê-lo. A resposta contém o ID de subscrição exclusivo da SAAS, nome, id de oferta e plano para o recurso. Este token é válido por apenas uma hora.

*Pedir*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Nome do parâmetro** |     **Descrição**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  api-version        |  A versão da operação a utilizar para este pedido.   |
|  |  |


*Cabeçalhos*

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Não           | Um valor de cadeia único para acompanhar o pedido do cliente, de preferência um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
| x-ms-correlação | Não           | Um valor de cadeia único para a operação no cliente. Este campo correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| Tipo de conteúdo       | Sim          | `application/json`                                        |
| autorização      | Sim          | O símbolo do portador da web JSON (JWT).                    |
| x-ms-marketplace-token| Sim| O parâmetro de consulta simbólica no URL quando o utilizador é redirecionado para o website do SaaS ISV a partir do Azure. **Nota:** Este token só é válido por 1 hora. Além disso, o URL descodifica o valor simbólico do navegador antes de o utilizar.|
|  |  |  |
  

*Corpo de Resposta*

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
| ID                 | String        | Identificação da assinatura SaaS.          |
| subscriptionName| String| Nome da subscrição SaaS definida pelo utilizador em Azure enquanto subscreve o serviço SaaS.|
| Offerid            | String        | Ofereça ID que o utilizador subscreveu. |
| planId             | String        | Plano ID que o utilizador subscreveu.  |
|  |  |  |


*Códigos de Resposta*

| **Código de Estado HTTP** | **Código de Erro**     | **Descrição**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | Token resolveu com sucesso.                                                            |
| 400                  | `BadRequest`         | Ou faltam cabeçalhos necessários ou especificauma versão api inválida. Não conseguiu resolver o símbolo porque ou o símbolo está mal formado ou expirado (o token só é válido por 1 hora uma vez gerado). |
| 403                  | `Forbidden`          | O chamador não está autorizado a realizar esta operação.                                 |
| 429                  | `RequestThrottleId`  | O serviço está ocupado a processar pedidos, a tentar novamente mais tarde.                                |
| 503                  | `ServiceUnavailable` | O serviço está temporariamente em baixo, tente mais tarde.                                        |
|  |  |  |


*Cabeçalhos de Resposta*

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | Pedido de identificação recebido do cliente.                                                                   |
| x-ms-correlação | Sim          | Id de correlação se passado pelo cliente, caso contrário este valor é o ID de correlação do servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia único para acompanhar o pedido do serviço. Esta identificação é usada para qualquer reconciliação. |
| Retry-After        | Não           | Este valor é definido apenas para uma resposta 429.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Subscrever

O ponto final de subscrição permite que os utilizadores iniciem uma subscrição de um serviço SaaS para um determinado plano e permitam faturação no sistema de comércio.

**COLOQUE**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriçãoId}*?api-version=2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Id exclusivo da subscrição SaaS que é obtido após a resolução do token via Resolve API.                              |
| api-version         | A versão da operação a utilizar para este pedido. |
|  |  |

*Cabeçalhos*

|  **Chave do cabeçalho**        | **Necessário** |  **Descrição**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-ms-requestid         |   Não         | Um valor de cadeia único para acompanhar o pedido do cliente, de preferência um GUID. Se isto não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| x-ms-correlação     |   Não         | Um valor de cadeia único para a operação no cliente. Este valor é para correlacionar todos os eventos da operação do cliente com eventos do lado do servidor. Se isto não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| Se-Match/If-None-Match |   Não         |   Valor eTag validator forte.                                                          |
| tipo de conteúdo           |   Sim        |    `application/json`                                                                   |
|  autorização         |   Sim        |    O símbolo do portador da web JSON (JWT).                                               |
| x-ms-marketplace-modo sessão| Não | Sinalizar para permitir o modo de funcionamento a seco ao subscrever uma oferta SaaS. Se definido, a subscrição não será cobrada. Isto é útil para cenários de teste ISV. Por favor, coloque-o em **'dryrun'**|
|  |  |  |

*Corpo*

``` json
{
    "lanId": "",
}
```

| **Nome do elemento** | **Tipo de dados** | **Descrição**                      |
|------------------|---------------|--------------------------------------|
| planId           | (Obrigatório) Corda        | O plano de identificação do utilizador do serviço SaaS está a subscrever.  |
|  |  |  |

*Códigos de Resposta*

| **Código de Estado HTTP** | **Código de Erro**     | **Descrição**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Ativação de subscrição SaaS recebida para um determinado plano.                   |
| 400                  | `BadRequest`         | Ou faltam cabeçalhos necessários ou o corpo do JSON está mal formado. |
| 403                  | `Forbidden`          | O chamador não está autorizado a realizar esta operação.                   |
| 404                  | `NotFound`           | Subscrição não encontrada com o ID dado                                  |
| 409                  | `Conflict`           | Outra operação está em curso na subscrição.                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado a processar pedidos, a tentar novamente mais tarde.                  |
| 503                  | `ServiceUnavailable` | O serviço está temporariamente em baixo, tente mais tarde.                          |
|  |  |  |

Para uma resposta de 202, acompanhe o estado da operação de pedido no cabeçalho 'Operação-localização'. A autenticação é a mesma que outras APIs do Marketplace.

*Cabeçalhos de Resposta*

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | Pedido de identificação recebido do cliente.                                                                   |
| x-ms-correlação | Sim          | Id de correlação se passado pelo cliente, caso contrário este valor é o ID de correlação do servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia único para acompanhar o pedido do serviço. Este valor é usado para quaisquer reconciliações. |
| Retry-After        | Sim          | Intervalo com o qual o cliente pode verificar o estado.                                                       |
| Localização da Operação | Sim          | Ligue-se a um recurso para obter o estado de operação.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Ponto final do plano de mudança

O ponto final de alteração permite ao utilizador converter o seu plano atualmente subscrito para um novo plano.

**PATCH**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriçãoId}*?api-version=2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID da assinatura SaaS.                              |
| api-version         | A versão da operação a utilizar para este pedido. |
|  |  |

*Cabeçalhos*

| **Chave do cabeçalho**          | **Necessário** | **Descrição**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid          | Não           | Um valor de cadeia único para acompanhar o pedido do cliente. Recomende um GUID. Se isto não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.   |
| x-ms-correlação      | Não           | Um valor de cadeia único para a operação no cliente. Este valor é para correlacionar todos os eventos da operação do cliente com eventos do lado do servidor. Se isto não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| Se-Match /If-None-Match | Não           | Valor eTag validator forte.                              |
| tipo de conteúdo            | Sim          | `application/json`                                        |
| autorização           | Sim          | O símbolo do portador da web JSON (JWT).                    |
|  |  |  |

*Corpo*

```json
{
    "planId": ""
}
```

|  **Nome do elemento** |  **Tipo de dados**  | **Descrição**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId           |  (Obrigatório) Corda         | O plano de identificação do utilizador do serviço SaaS está a subscrever.          |
|  |  |  |

*Códigos de Resposta*

| **Código de Estado HTTP** | **Código de Erro**     | **Descrição**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Ativação de subscrição SaaS recebida para um determinado plano.                   |
| 400                  | `BadRequest`         | Ou faltam cabeçalhos necessários ou o corpo do JSON está mal formado. |
| 403                  | `Forbidden`          | O chamador não está autorizado a realizar esta operação.                   |
| 404                  | `NotFound`           | Subscrição não encontrada com o ID dado                                  |
| 409                  | `Conflict`           | Outra operação está em curso na subscrição.                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado a processar pedidos, a tentar novamente mais tarde.                  |
| 503                  | `ServiceUnavailable` | O serviço está temporariamente em baixo, tente mais tarde.                          |
|  |  |  |

*Cabeçalhos de Resposta*

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | Pedido de identificação recebido do cliente.                                                                   |
| x-ms-correlação | Sim          | Id de correlação se passado pelo cliente, caso contrário este valor é o ID de correlação do servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia único para acompanhar o pedido do serviço. Este valor é usado para quaisquer reconciliações. |
| Retry-After        | Sim          | Intervalo com o qual o cliente pode verificar o estado.                                                       |
| Localização da Operação | Sim          | Ligue-se a um recurso para obter o estado de operação.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Eliminar subscrição

A ação Delete no ponto final de subscrição permite que um utilizador elimine uma subscrição com um determinado ID.

*Pedir*

**ELIMINAR**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriçãoId}*?api-version=2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID da assinatura SaaS.                              |
| api-version         | A versão da operação a utilizar para este pedido. |
|  |  |

*Cabeçalhos*

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-ms-requestid     | Não           | Um valor de cadeia único para acompanhar o pedido do cliente. Recomende um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.                                                           |
| x-ms-correlação | Não           | Um valor de cadeia único para a operação no cliente. Este valor é para correlacionar todos os eventos da operação do cliente com eventos do lado do servidor. Se isto não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| autorização      | Sim          | O símbolo do portador da web JSON (JWT).                    |
|  |  |  |

*Códigos de Resposta*

| **Código de Estado HTTP** | **Código de Erro**     | **Descrição**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Ativação de subscrição SaaS recebida para um determinado plano.                   |
| 400                  | `BadRequest`         | Ou faltam cabeçalhos necessários ou o corpo do JSON está mal formado. |
| 403                  | `Forbidden`          | O chamador não está autorizado a realizar esta operação.                   |
| 404                  | `NotFound`           | Subscrição não encontrada com o ID dado                                  |
| 429                  | `RequestThrottleId`  | O serviço está ocupado a processar pedidos, por favor, tente mais tarde.                  |
| 503                  | `ServiceUnavailable` | O serviço está temporariamente em baixo. Por favor, tente mais tarde.                          |
|  |  |  |

Para uma resposta de 202, acompanhe o estado da operação de pedido no cabeçalho 'Operação-localização'. A autenticação é a mesma que outras APIs do Marketplace.

*Cabeçalhos de Resposta*

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | Pedido de identificação recebido do cliente.                                                                   |
| x-ms-correlação | Sim          | Id de correlação se passado pelo cliente, caso contrário este é o ID de correlação do servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia único para acompanhar o pedido do serviço. Isto é usado para qualquer reconciliação. |
| Retry-After        | Sim          | Intervalo com o qual o cliente pode verificar o estado.                                                       |
| Localização da Operação | Sim          | Ligue-se a um recurso para obter o estado de operação.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Obter Estado da Operação

Este ponto final permite ao utilizador rastrear o estado de uma operação de asincronização desencadeada (Plano subscrever/cancelar/alterar).

*Pedir*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/operations/*{operationId}*?api-version=2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | Identificação única para a operação desencadeada.                |
| api-version         | A versão da operação a utilizar para este pedido. |
|  |  |

*Cabeçalhos*

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Não           | Um valor de cadeia único para acompanhar o pedido do cliente. Recomende um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.   |
| x-ms-correlação | Não           | Um valor de cadeia único para a operação no cliente. Este valor é para correlacionar todos os eventos da operação do cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
| autorização      | Sim          | O símbolo do portador da web JSON (JWT).                    |
|  |  |  | 

*Corpo de Resposta*

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
| ID                 | String        | Identificação da operação.                                                                      |
| status             | Enum          | Estado de funcionamento, `In Progress`um `Succeeded`dos `Failed`seguintes: , ou .          |
| resourceLocation   | String        | Ligação à subscrição que foi criada ou modificada. Isto ajuda o cliente a obter uma operação estatal atualizada. Este valor não `Unsubscribe` está definido para operações. |
| criado            | DateTime      | Tempo de criação de operação na UTC.                                                           |
| últimaModificada       | DateTime      | Última atualização da operação na UTC.                                                      |
|  |  |  |

*Códigos de Resposta*

| **Código de Estado HTTP** | **Código de Erro**     | **Descrição**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Resolveu o pedido de obter com sucesso e o corpo contém a resposta.    |
| 400                  | `BadRequest`         | Ou faltam cabeçalhos necessários ou foi especificada uma versão api inválida. |
| 403                  | `Forbidden`          | O chamador não está autorizado a realizar esta operação.                      |
| 404                  | `NotFound`           | Assinatura não encontrada com o ID dado.                                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado a processar pedidos, a tentar novamente mais tarde.                     |
| 503                  | `ServiceUnavailable` | O serviço está temporariamente em baixo, tente mais tarde.                             |
|  |  |  |

*Cabeçalhos de Resposta*

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | Pedido de identificação recebido do cliente.                                                                   |
| x-ms-correlação | Sim          | Id de correlação se passado pelo cliente, caso contrário este é o ID de correlação do servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia único para acompanhar o pedido do serviço. Isto é usado para qualquer reconciliação. |
| Retry-After        | Sim          | Intervalo com o qual o cliente pode verificar o estado.                                                       |
|  |  |  |

### <a name="get-subscription"></a>Obter Subscrição

A ação Get no ponto final de subscrição permite que um utilizador recupere uma subscrição com um determinado identificador de recursos.

*Pedir*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriçãoId}*?api-version=2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID da assinatura SaaS.                              |
| api-version         | A versão da operação a utilizar para este pedido. |
|  |  |

*Cabeçalhos*

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Não           | Um valor de cadeia único para acompanhar o pedido do cliente, de preferência um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.                                                           |
| x-ms-correlação | Não           | Um valor de cadeia único para a operação no cliente. Este valor é para correlacionar todos os eventos da operação do cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| autorização      | Sim          | O símbolo do portador da web JSON (JWT).                                                                    |
|  |  |  |

*Corpo de Resposta*

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
| ID                     | String        | ID do recurso de subscrição SaaS em Azure.    |
| offerId                | String        | Ofereça ID que o utilizador subscreveu.         |
| planId                 | String        | Plano ID que o utilizador subscreveu.          |
| nome saasSubscription   | String        | Nome da assinatura SaaS.                |
| SaasEstatuto de Subscrição | Enum          | Estado de operação.  Um dos seguintes:  <br/> - `Subscribed`: A subscrição está ativa.  <br/> - `Pending`: O utilizador cria o recurso mas não é ativado pelo ISV.   <br/> - `Unsubscribed`: O utilizador não tem subscrição.   <br/> - `Suspended`: O utilizador suspendeu a subscrição.   <br/> - `Deactivated`: A subscrição do Azure está suspensa.  |
| criado                | DateTime      | Valor de carimbo de tempo de criação de assinatura seleções na UTC. |
| últimaModificada           | DateTime      | Valor de carimbo de tempo modificado por subscrição na UTC. |
|  |  |  |

*Códigos de Resposta*

| **Código de Estado HTTP** | **Código de Erro**     | **Descrição**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Resolveu o pedido de obter com sucesso e o corpo contém a resposta.    |
| 400                  | `BadRequest`         | Ou faltam cabeçalhos necessários ou foi especificada uma versão api inválida. |
| 403                  | `Forbidden`          | O chamador não está autorizado a realizar esta operação.                      |
| 404                  | `NotFound`           | Subscrição não encontrada com o ID dado                                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado a processar pedidos, a tentar novamente mais tarde.                     |
| 503                  | `ServiceUnavailable` | O serviço está temporariamente em baixo, tente mais tarde.                             |
|  |  |  |

*Cabeçalhos de Resposta*

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | Pedido de identificação recebido do cliente.                                                                   |
| x-ms-correlação | Sim          | Id de correlação se passado pelo cliente, caso contrário este é o ID de correlação do servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia único para acompanhar o pedido do serviço. Isto é usado para qualquer reconciliação. |
| Retry-After        | Não           | Intervalo com o qual o cliente pode verificar o estado.                                                       |
| eTag               | Sim          | Ligue-se a um recurso para obter o estado de operação.                                                        |
|  |  |  |

### <a name="get-subscriptions"></a>Obter Subscrições

A ação Get no ponto final de subscrições permite que um utilizador recupere todas as subscrições para todas as ofertas do ISV.

*Pedir*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| api-version         | A versão da operação a utilizar para este pedido. |
|  |  |

*Cabeçalhos*

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Não           | Um valor de cadeia único para acompanhar o pedido do cliente. Recomende um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.             |
| x-ms-correlação | Não           | Um valor de cadeia único para a operação no cliente. Este valor é para correlacionar todos os eventos da operação do cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| autorização      | Sim          | O símbolo do portador da web JSON (JWT).                    |
|  |  |  |

*Corpo de Resposta*

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
| ID                     | String        | ID do recurso de subscrição SaaS em Azure    |
| offerId                | String        | DI oferta que o utilizador subscreveu         |
| planId                 | String        | Id do plano que o utilizador subscreveu          |
| nome saasSubscription   | String        | Nome da subscrição SaaS                |
| SaasEstatuto de Subscrição | Enum          | Estado de operação.  Um dos seguintes:  <br/> - `Subscribed`: A subscrição está ativa.  <br/> - `Pending`: O utilizador cria o recurso mas não é ativado pelo ISV.   <br/> - `Unsubscribed`: O utilizador não tem subscrição.   <br/> - `Suspended`: O utilizador suspendeu a subscrição.   <br/> - `Deactivated`: A subscrição do Azure está suspensa.  |
| criado                | DateTime      | Valor de carimbo de tempo de criação de assinatura seleções na UTC |
| últimaModificada           | DateTime      | Valor de carimbo de tempo modificado por subscrição na UTC |
|  |  |  |

*Códigos de Resposta*

| **Código de Estado HTTP** | **Código de Erro**     | **Descrição**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Resolveu o pedido de obter com sucesso e o corpo contém a resposta.    |
| 400                  | `BadRequest`         | Ou faltam cabeçalhos necessários ou foi especificada uma versão api inválida. |
| 403                  | `Forbidden`          | O chamador não está autorizado a realizar esta operação.                      |
| 404                  | `NotFound`           | Subscrição não encontrada com o ID dado                                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado a processar pedidos, por favor, tente mais tarde.                     |
| 503                  | `ServiceUnavailable` | O serviço está temporariamente em baixo. Por favor, tente mais tarde.                             |
|  |  |  |

*Cabeçalhos de Resposta*

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | Pedido de identificação recebido do cliente.                                                                   |
| x-ms-correlação | Sim          | Id de correlação se passado pelo cliente, caso contrário este é o ID de correlação do servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia único para acompanhar o pedido do serviço. Isto é usado para qualquer reconciliação. |
| Retry-After        | Não           | Intervalo com o qual o cliente pode verificar o estado.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>SaaS Webhook

Um webhook SaaS é usado para notificar alterações proactivamente ao serviço SaaS. Espera-se que esta API post não seja autenticada e será chamada pelo serviço da Microsoft. Espera-se que o serviço SaaS ligue para as operações API para validar e autorizar antes de tomar medidas sobre a notificação do webhook. 

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
| ID  | String       | Identificação única para a operação desencadeada.                |
| atividadeId   | String        | Um valor de cadeia único para acompanhar o pedido do serviço. Isto é usado para qualquer reconciliação.               |
| subscriptionId                     | String        | ID do recurso de subscrição SaaS em Azure.    |
| offerId                | String        | Ofereça ID que o utilizador subscreveu. Fornecido apenas com a ação "Atualizar".        |
| editorId                | String        | Identificação da editora da oferta SaaS         |
| planId                 | String        | Plano ID que o utilizador subscreveu. Fornecido apenas com a ação "Atualizar".          |
| action                 | String        | A ação que está a desencadear esta notificação. Possíveis valores - Ativar, Eliminar, Suspender, Restabelecer, Atualizar          |
| tempoStamp                 | String        | Valor de carimbo na UTC quando esta notificação foi desencadeada.          |
|  |  |  |


## <a name="next-steps"></a>Passos seguintes

Os desenvolvedores também podem recuperar e manipular programáticamente cargas de trabalho, ofertas e perfis de editor usando o Portal de [Parceiros cloud REST APIs](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
