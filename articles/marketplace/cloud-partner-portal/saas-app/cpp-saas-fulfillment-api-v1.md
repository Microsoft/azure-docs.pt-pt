---
title: V1 de API de preenchimento do SaaS - o Azure Marketplace | Documentos da Microsoft
description: Explica como criar uma oferta SaaS no Azure Marketplace usando o preenchimento associado V1 APIs.
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
ms.date: 03/28/2019
ms.author: pbutlerm
ROBOTS: NOINDEX
ms.openlocfilehash: 4908233280c69a37ea470eed2ef077cb220a7930
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101103"
---
# <a name="saas-fulfillment-apis-version-1--deprecated"></a>Versão de APIs de preenchimento do SaaS 1 (preterido)

Este artigo explica como criar uma oferta SaaS com as APIs. As APIs, compostas por métodos REST e pontos de extremidade, são necessárias para permitir que as assinaturas para sua oferta de SaaS tem vender seu através do Azure selecionado.  

> [!WARNING]
> Esta versão inicial da API de preenchimento do SaaS foi preterido; em alternativa, utilize [SaaS preenchimento API V2](./cpp-saas-fulfillment-api-v2.md).  Esta API está atualmente a ser mantida apenas para servir os publicadores existentes. 

As seguintes APIs são fornecidas para o ajudar a integrar o serviço SaaS com o Azure:

-   Resolver
-   Subscrever
-   Converter
-   Anular a subscrição


## <a name="api-methods-and-endpoints"></a>Métodos da API e os pontos finais

As secções seguintes descrevem os métodos de API e os pontos finais disponíveis para ativar as subscrições para uma oferta SaaS.


### <a name="marketplace-api-endpoint-and-api-version"></a>Ponto final de API do Marketplace e a versão de API

O ponto final para a API do Azure Marketplace está `https://marketplaceapi.microsoft.com`.

A versão de API atual é `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Resolver a subscrição

Ação de publicação em resolver o ponto final permite aos utilizadores resolver um token do marketplace para uma ID de recurso persistente.  O ID de recurso é o identificador exclusivo para a subscrição de SAAS. 

Quando um utilizador é redirecionado para o site de um ISV, o URL contém um token nos parâmetros de consulta. O ISV deve utilizar este token e fazer um pedido para resolvê-lo. A resposta contém o exclusivo ID de subscrição de SAAS, nome, ID de oferta e plano para o recurso. Este token é válido apenas uma hora.

*Pedido*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Nome do parâmetro** |     **Descrição**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  versão de API        |  A versão da operação para utilizar para este pedido.   |
|  |  |


*Headers* (Cabeçalhos)

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Não           | Um valor de cadeia de caracteres exclusivo para o pedido de controlo do cliente, preferencialmente, um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
| x-ms-correlationid | Não           | Um valor de cadeia de caracteres exclusivo para a operação no cliente. Isso correlaciona todos os eventos da operação de cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| tipo de conteúdo       | Sim          | `application/json`                                        |
| Autorização      | Sim          | O JSON web token (JWT) token de portador.                    |
| x-ms-marketplace-token| Sim| O parâmetro de consulta token no URL quando o utilizador é redirecionado para o Web site de ISVS SaaS do Azure. **Nota:** Este token só é válido durante uma hora. Além disso, o URL descodificar o valor do token do navegador antes de o utilizar.|
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
| ID                 | String        | ID da subscrição SaaS.          |
| subscriptionName| String| Nome da subscrição SaaS definida pelo utilizador no Azure ao subscrever o serviço SaaS.|
| OfferId            | String        | ID de oferta que o utilizador inscrito. |
| planId             | String        | ID do plano nos quais o utilizador inscrito.  |
|  |  |  |


*Códigos de resposta*

| **Código de estado de HTTP** | **Código de erro**     | **Descrição**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | Token está a ser resolvido com êxito.                                                            |
| 400                  | `BadRequest`         | É necessário um cabeçalhos estão em falta ou um api-version inválido especificado. Falha ao resolver o token, porque o token de qualquer um dos está incorretamente formulados ou expirados (o token só é válido durante uma hora depois de gerada). |
| 403                  | `Forbidden`          | O chamador não está autorizado a efetuar esta operação.                                 |
| 429                  | `RequestThrottleId`  | O serviço está ocupado a processar pedidos, tente novamente mais tarde.                                |
| 503                  | `ServiceUnavailable` | Serviço para baixo temporariamente, tente novamente mais tarde.                                        |
|  |  |  |


*Cabeçalhos de resposta*

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | ID recebida do cliente do pedido.                                                                   |
| x-ms-correlationid | Sim          | ID de correlação se transmitido pelo cliente, caso contrário, este valor é o ID de correlação de servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia de caracteres exclusivo para o pedido de controlo do serviço. Este ID é utilizado para qualquer reconciliations. |
| Retry-After        | Não           | Este valor é definido apenas para uma resposta 429.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Subscrever

O ponto de extremidade subscribe permite aos utilizadores iniciar uma subscrição para um serviço de SaaS para um determinado plano e ativar a faturação no sistema de comércio.

**PUT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Subscrição de ID de SaaS exclusiva que é obtida depois de resolver o token através da API de resolver.                              |
| versão de API         | A versão da operação para utilizar para este pedido. |
|  |  |

*Headers* (Cabeçalhos)

|  **Chave do cabeçalho**        | **Necessário** |  **Descrição**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-ms-requestid         |   Não         | Um valor de cadeia de caracteres exclusivo para o pedido de controlo do cliente, preferencialmente, um GUID. Se não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| x-ms-correlationid     |   Não         | Um valor de cadeia de caracteres exclusivo para a operação no cliente. Este valor é para correlacionar a todos os eventos da operação de cliente com eventos do lado do servidor. Se não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| If-Match/If-None-Match |   Não         |   Validador de valor de ETag forte.                                                          |
| content-type           |   Sim        |    `application/json`                                                                   |
|  Autorização         |   Sim        |    O JSON web token (JWT) token de portador.                                               |
| x-ms-marketplace-session-mode| Não | Sinalizador para ativar o modo de execução ao subscrever uma oferta SaaS. Se definido, a subscrição não será cobrada. Isto é útil para cenários de teste de ISV. Defina-o como **'dryrun'**|
|  |  |  |

*Corpo*

``` json
{
    "lanId": "",
}
```

| **Nome do elemento** | **Tipo de dados** | **Descrição**                      |
|------------------|---------------|--------------------------------------|
| planId           | (Obrigatório) Cadeia de caracteres        | Id do plano de utilizador de serviço SaaS está a subscrever.  |
|  |  |  |

*Códigos de resposta*

| **Código de estado de HTTP** | **Código de erro**     | **Descrição**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Ativação de assinatura de SaaS recebida para um determinado plano.                   |
| 400                  | `BadRequest`         | É necessário um cabeçalhos estão em falta ou o corpo do JSON é um formato incorreto. |
| 403                  | `Forbidden`          | O chamador não está autorizado a efetuar esta operação.                   |
| 404                  | `NotFound`           | Subscrição não foi encontrada com o ID fornecido                                  |
| 409                  | `Conflict`           | Outra operação está em curso na subscrição.                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado a processar pedidos, tente novamente mais tarde.                  |
| 503                  | `ServiceUnavailable` | Serviço para baixo temporariamente, tente novamente mais tarde.                          |
|  |  |  |

Para uma resposta 202, dar seguimento estado da operação de pedido no cabeçalho "Location de operação". A autenticação é o mesmo que outras APIs do Marketplace.

*Cabeçalhos de resposta*

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | ID recebida do cliente do pedido.                                                                   |
| x-ms-correlationid | Sim          | ID de correlação se transmitido pelo cliente, caso contrário, este valor é o ID de correlação de servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia de caracteres exclusivo para o pedido de controlo do serviço. Este valor é utilizado para qualquer reconciliations. |
| Retry-After        | Sim          | Intervalo com o cliente pode verificar o estado.                                                       |
| Operação de localização | Sim          | Ligar a um recurso para obter o estado da operação.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Ponto final do plano de alteração

O ponto de extremidade de alteração permite ao utilizador converter o seu plano de atualmente subscrito para um novo plano.

**PATCH**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Subscrição de ID de SaaS.                              |
| versão de API         | A versão da operação para utilizar para este pedido. |
|  |  |

*Headers* (Cabeçalhos)

| **Chave do cabeçalho**          | **Necessário** | **Descrição**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid          | Não           | Um valor de cadeia de caracteres exclusivo para o pedido de controlo do cliente. Recomendamos um GUID. Se não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.   |
| x-ms-correlationid      | Não           | Um valor de cadeia de caracteres exclusivo para a operação no cliente. Este valor é para correlacionar a todos os eventos da operação de cliente com eventos do lado do servidor. Se não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| If-Match /If-None-Match | Não           | Validador de valor de ETag forte.                              |
| content-type            | Sim          | `application/json`                                        |
| Autorização           | Sim          | O JSON web token (JWT) token de portador.                    |
|  |  |  |

*Corpo*

```json
{
    "planId": ""
}
```

|  **Nome do elemento** |  **Tipo de dados**  | **Descrição**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId           |  (Obrigatório) Cadeia de caracteres         | Id do plano de utilizador de serviço SaaS está a subscrever.          |
|  |  |  |

*Códigos de resposta*

| **Código de estado de HTTP** | **Código de erro**     | **Descrição**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Ativação de assinatura de SaaS recebida para um determinado plano.                   |
| 400                  | `BadRequest`         | É necessário um cabeçalhos estão em falta ou o corpo do JSON é um formato incorreto. |
| 403                  | `Forbidden`          | O chamador não está autorizado a efetuar esta operação.                   |
| 404                  | `NotFound`           | Subscrição não foi encontrada com o ID fornecido                                  |
| 409                  | `Conflict`           | Outra operação está em curso na subscrição.                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado a processar pedidos, tente novamente mais tarde.                  |
| 503                  | `ServiceUnavailable` | Serviço para baixo temporariamente, tente novamente mais tarde.                          |
|  |  |  |

*Cabeçalhos de resposta*

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | ID recebida do cliente do pedido.                                                                   |
| x-ms-correlationid | Sim          | ID de correlação se transmitido pelo cliente, caso contrário, este valor é o ID de correlação de servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia de caracteres exclusivo para o pedido de controlo do serviço. Este valor é utilizado para qualquer reconciliations. |
| Retry-After        | Sim          | Intervalo com o cliente pode verificar o estado.                                                       |
| Operação de localização | Sim          | Ligar a um recurso para obter o estado da operação.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Eliminar subscrição

A ação de eliminação no ponto de extremidade subscribe permite que um utilizador eliminar uma subscrição com uma ID especificada.

*Pedido*

**DELETE**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Subscrição de ID de SaaS.                              |
| versão de API         | A versão da operação para utilizar para este pedido. |
|  |  |

*Headers* (Cabeçalhos)

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-ms-requestid     | Não           | Um valor de cadeia de caracteres exclusivo para o pedido de controlo do cliente. Recomendamos um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.                                                           |
| x-ms-correlationid | Não           | Um valor de cadeia de caracteres exclusivo para a operação no cliente. Este valor é para correlacionar a todos os eventos da operação de cliente com eventos do lado do servidor. Se não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| Autorização      | Sim          | O JSON web token (JWT) token de portador.                    |
|  |  |  |

*Códigos de resposta*

| **Código de estado de HTTP** | **Código de erro**     | **Descrição**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Ativação de assinatura de SaaS recebida para um determinado plano.                   |
| 400                  | `BadRequest`         | É necessário um cabeçalhos estão em falta ou o corpo do JSON é um formato incorreto. |
| 403                  | `Forbidden`          | O chamador não está autorizado a efetuar esta operação.                   |
| 404                  | `NotFound`           | Subscrição não foi encontrada com o ID fornecido                                  |
| 429                  | `RequestThrottleId`  | O serviço está ocupado a processar pedidos, tente novamente mais tarde.                  |
| 503                  | `ServiceUnavailable` | Serviço está temporariamente indisponível. Tente novamente mais tarde.                          |
|  |  |  |

Para uma resposta 202, dar seguimento estado da operação de pedido no cabeçalho "Location de operação". A autenticação é o mesmo que outras APIs do Marketplace.

*Cabeçalhos de resposta*

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | ID recebida do cliente do pedido.                                                                   |
| x-ms-correlationid | Sim          | ID de correlação se transmitido pelo cliente, caso contrário, isso é o ID de correlação de servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia de caracteres exclusivo para o pedido de controlo do serviço. Isto é utilizado para qualquer reconciliations. |
| Retry-After        | Sim          | Intervalo com o cliente pode verificar o estado.                                                       |
| Operação de localização | Sim          | Ligar a um recurso para obter o estado da operação.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Obter estado da operação

Este ponto final permite ao utilizador controlar o estado de uma operação de async acionadas (subscrever/anular a subscrição/alterar plano).

*Pedido*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/operations/*{operationId}*?api-version=2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | ID exclusivo para a operação acionada.                |
| versão de API         | A versão da operação para utilizar para este pedido. |
|  |  |

*Headers* (Cabeçalhos)

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Não           | Um valor de cadeia de caracteres exclusivo para o pedido de controlo do cliente. Recomendamos um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.   |
| x-ms-correlationid | Não           | Um valor de cadeia de caracteres exclusivo para a operação no cliente. Este valor é para correlacionar a todos os eventos da operação de cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
| Autorização      | Sim          | O JSON web token (JWT) token de portador.                    |
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
| status             | Enum          | Estado da operação, um dos seguintes: `In Progress`, `Succeeded`, ou `Failed`.          |
| resourceLocation   | String        | Ligação para a subscrição que foi criada ou modificada. Isto ajuda o cliente para obter a operação de mensagem de estado atualizado. Este valor não está definido para `Unsubscribe` operações. |
| criado            | DateTime      | Hora de criação de operação em UTC.                                                           |
| lastModified       | DateTime      | Última atualização na operação em UTC.                                                      |
|  |  |  |

*Códigos de resposta*

| **Código de estado de HTTP** | **Código de erro**     | **Descrição**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Resolvido o pedido get com êxito e o corpo contém a resposta.    |
| 400                  | `BadRequest`         | É necessário um cabeçalhos estão em falta ou um api-version inválido foi especificado. |
| 403                  | `Forbidden`          | O chamador não está autorizado a efetuar esta operação.                      |
| 404                  | `NotFound`           | Subscrição não foi encontrada com o ID fornecido                                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado a processar pedidos, tente novamente mais tarde.                     |
| 503                  | `ServiceUnavailable` | Serviço para baixo temporariamente, tente novamente mais tarde.                             |
|  |  |  |

*Cabeçalhos de resposta*

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | ID recebida do cliente do pedido.                                                                   |
| x-ms-correlationid | Sim          | ID de correlação se transmitido pelo cliente, caso contrário, isso é o ID de correlação de servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia de caracteres exclusivo para o pedido de controlo do serviço. Isto é utilizado para qualquer reconciliations. |
| Retry-After        | Sim          | Intervalo com o cliente pode verificar o estado.                                                       |
|  |  |  |

### <a name="get-subscription"></a>Obter a subscrição

Assine a ação de Get no ponto final permite que um utilizador a obter uma subscrição com um identificador de recurso específico.

*Pedido*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Subscrição de ID de SaaS.                              |
| versão de API         | A versão da operação para utilizar para este pedido. |
|  |  |

*Headers* (Cabeçalhos)

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Não           | Um valor de cadeia de caracteres exclusivo para o pedido de controlo do cliente, preferencialmente, um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.                                                           |
| x-ms-correlationid | Não           | Um valor de cadeia de caracteres exclusivo para a operação no cliente. Este valor é para correlacionar a todos os eventos da operação de cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| Autorização      | Sim          | O JSON web token (JWT) token de portador.                                                                    |
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
| ID                     | String        | Recursos de subscrição de ID de SaaS no Azure.    |
| offerId                | String        | ID de oferta que o utilizador inscrito.         |
| planId                 | String        | ID do plano nos quais o utilizador inscrito.          |
| saasSubscriptionName   | String        | Nome da subscrição SaaS.                |
| saasSubscriptionStatus | Enum          | Estado da operação.  Um dos seguintes:  <br/> - `Subscribed`: A subscrição está ativa.  <br/> - `Pending`: Utilizador criar o recurso, mas ele não está ativado por de ISV.   <br/> - `Unsubscribed`: Utilizador anulou a subscrição.   <br/> - `Suspended`: Utilizador tem de suspender a subscrição.   <br/> - `Deactivated`:  Subscrição do Azure está suspensa.  |
| criado                | DateTime      | Valor de timestamp de criação de subscrição em UTC. |
| lastModified           | DateTime      | Subscrição alterar timestamp valor em UTC. |
|  |  |  |

*Códigos de resposta*

| **Código de estado de HTTP** | **Código de erro**     | **Descrição**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Resolvido o pedido get com êxito e o corpo contém a resposta.    |
| 400                  | `BadRequest`         | É necessário um cabeçalhos estão em falta ou um api-version inválido foi especificado. |
| 403                  | `Forbidden`          | O chamador não está autorizado a efetuar esta operação.                      |
| 404                  | `NotFound`           | Subscrição não foi encontrada com o ID fornecido                                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado a processar pedidos, tente novamente mais tarde.                     |
| 503                  | `ServiceUnavailable` | Serviço para baixo temporariamente, tente novamente mais tarde.                             |
|  |  |  |

*Cabeçalhos de resposta*

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | ID recebida do cliente do pedido.                                                                   |
| x-ms-correlationid | Sim          | ID de correlação se transmitido pelo cliente, caso contrário, isso é o ID de correlação de servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia de caracteres exclusivo para o pedido de controlo do serviço. Isto é utilizado para qualquer reconciliations. |
| Retry-After        | Não           | Intervalo com o cliente pode verificar o estado.                                                       |
| eTag               | Sim          | Ligar a um recurso para obter o estado da operação.                                                        |
|  |  |  |

### <a name="get-subscriptions"></a>Obter Subscrições

A ação de Get no ponto final de subscrições permite que um usuário recuperar todas as subscrições para todas as ofertas do ISV.

*Pedido*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| versão de API         | A versão da operação para utilizar para este pedido. |
|  |  |

*Headers* (Cabeçalhos)

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Não           | Um valor de cadeia de caracteres exclusivo para o pedido de controlo do cliente. Recomendamos um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.             |
| x-ms-correlationid | Não           | Um valor de cadeia de caracteres exclusivo para a operação no cliente. Este valor é para correlacionar a todos os eventos da operação de cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| Autorização      | Sim          | O JSON web token (JWT) token de portador.                    |
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
| ID                     | String        | Recursos de subscrição de ID de SaaS no Azure.    |
| offerId                | String        | ID de oferta que o utilizador inscrito.         |
| planId                 | String        | ID do plano nos quais o utilizador inscrito.          |
| saasSubscriptionName   | String        | Nome da subscrição SaaS.                |
| saasSubscriptionStatus | Enum          | Estado da operação.  Um dos seguintes:  <br/> - `Subscribed`: A subscrição está ativa.  <br/> - `Pending`: Utilizador criar o recurso, mas ele não está ativado por de ISV.   <br/> - `Unsubscribed`: Utilizador anulou a subscrição.   <br/> - `Suspended`: Utilizador tem de suspender a subscrição.   <br/> - `Deactivated`:  Subscrição do Azure está suspensa.  |
| criado                | DateTime      | Valor de timestamp de criação de subscrição em UTC. |
| lastModified           | DateTime      | Subscrição alterar timestamp valor em UTC. |
|  |  |  |

*Códigos de resposta*

| **Código de estado de HTTP** | **Código de erro**     | **Descrição**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Resolvido o pedido get com êxito e o corpo contém a resposta.    |
| 400                  | `BadRequest`         | É necessário um cabeçalhos estão em falta ou um api-version inválido foi especificado. |
| 403                  | `Forbidden`          | O chamador não está autorizado a efetuar esta operação.                      |
| 404                  | `NotFound`           | Subscrição não foi encontrada com o ID fornecido                                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado a processar pedidos, tente novamente mais tarde.                     |
| 503                  | `ServiceUnavailable` | Serviço está temporariamente indisponível. Tente novamente mais tarde.                             |
|  |  |  |

*Cabeçalhos de resposta*

| **Chave do cabeçalho**     | **Necessário** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | ID recebida do cliente do pedido.                                                                   |
| x-ms-correlationid | Sim          | ID de correlação se transmitido pelo cliente, caso contrário, isso é o ID de correlação de servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia de caracteres exclusivo para o pedido de controlo do serviço. Isto é utilizado para qualquer reconciliations. |
| Retry-After        | Não           | Intervalo com o cliente pode verificar o estado.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>SaaS Webhook

Um webhook de SaaS é utilizado para a notificação de alterações de forma pró-ativa para o serviço SaaS. Esta API de publicação é esperado que seja não autenticadas e será chamada pelo serviço da Microsoft. O serviço de SaaS é esperado para chamar as operações de API para validar e autorizar antes de efetuar a ação de notificação de webhook. 

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
| ID  | String       | ID exclusivo para a operação acionada.                |
| activityId   | String        | Um valor de cadeia de caracteres exclusivo para o pedido de controlo do serviço. Isto é utilizado para qualquer reconciliations.               |
| subscriptionId                     | String        | Recursos de subscrição de ID de SaaS no Azure.    |
| offerId                | String        | ID de oferta que o utilizador inscrito. É fornecido apenas com a ação "Update".        |
| publisherId                | String        | ID de publicador da oferta SaaS         |
| planId                 | String        | ID do plano nos quais o utilizador inscrito. É fornecido apenas com a ação "Update".          |
| action                 | String        | A ação que está disparando esta notificação. Valores possíveis - ativar, eliminar, atualização de suspensão, o restabelecimento,          |
| timeStamp                 | String        | Valor de TImestamp em UTC em que esta notificação foi acionada.          |
|  |  |  |


## <a name="next-steps"></a>Passos Seguintes

Os desenvolvedores podem também obter automaticamente a e manipulação de cargas de trabalho, ofertas e publicador perfis utilizando o [APIs de REST do Cloud Partner Portal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
