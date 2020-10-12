---
title: ApIs de serviço de medição - Mercado comercial da Microsoft
description: O evento de utilização API permite-lhe emitir eventos de utilização para ofertas saaS no Microsoft AppSource e no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/26/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: ac48973653e89d43521979a5606a8a3a3c2e1346
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87319988"
---
# <a name="marketplace-metered-billing-apis"></a>APIs de faturação medido no mercado

As APIs de faturação medido devem ser utilizadas quando a editora cria dimensões de medição personalizadas para uma oferta a publicar no Partner Center. A integração com as APIs de faturação medido é necessária para qualquer oferta adquirida que tenha um ou mais planos com dimensões personalizadas para emitir eventos de uso.

Para obter mais informações sobre a criação de dimensões de medição personalizadas para o SaaS, consulte [a faturação com contador saaS.](saas-metered-billing.md)

Para obter mais informações sobre a criação de dimensões de medição personalizadas para uma oferta de aplicação Azure com um plano de aplicações gerido, consulte a [secção de configuração técnica da criação de uma nova oferta de Apps Azure).](create-new-azure-apps-offer.md#technical-configuration)

## <a name="enforcing-tls-12-note"></a>Aplicação TLS 1.2 Nota

A versão TLS versão 1.2 é aplicada como a versão mínima para comunicações HTTPS. Certifique-se de que utiliza esta versão TLS no seu código. As versões TLS 1.0 e 1.1 são depreciadas e as tentativas de ligação serão recusadas.

## <a name="metered-billing-single-usage-event"></a>Evento de utilização única de faturação medido

O evento de utilização API deve ser chamado pela editora para emitir eventos de utilização contra um recurso ativo (subscrito) para o plano adquirido pelo cliente específico. O evento de utilização é emitido separadamente para cada dimensão personalizada do plano definido pela editora ao publicar a oferta.

Apenas um evento de utilização pode ser emitido para cada hora de um dia de calendário. Por exemplo, às 8:15 da manhã de hoje, você pode emitir um evento de uso. Se este evento for aceite, o próximo evento de utilização será aceite a partir das 9:00 de hoje. Se enviar um evento adicional entre 8:15 e 8:59:59 hoje, será rejeitado como uma duplicata. Deve acumular todas as unidades consumidas numa hora e depois emitê-las num único evento.

Apenas um evento de utilização pode ser emitido por cada hora de um dia de calendário por recurso. Se mais de uma unidade for consumida numa hora, então acumule todas as unidades consumidas durante a hora e emita-as num único evento. Os eventos de utilização só podem ser emitidos nas últimas 24 horas. Se emitir um evento de utilização a qualquer momento entre as 8:00 e as 8:59:59 (e é aceite) e enviar um evento adicional para o mesmo dia entre as 8:00 e as 8:59:59, será rejeitado como uma duplicação.

**POSTO:**`https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Parâmetros de consulta:*

| Paramter | Recomendação          |
| ---------- | ---------------------- |
| `ApiVersion` | Use 2018-08-31. |
| | |

*Pedido de cabeçalhos:*

| Tipo de conteúdo       | Utilizar o comando `application/json`  |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Valor de cadeia único para acompanhar o pedido do cliente, de preferência um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| `x-ms-correlationid` | Valor único de corda para operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| `authorization`   | Um token de acesso único que identifica o ISV que está a fazer esta chamada da API. O formato é `"Bearer <access_token>"` quando o valor simbólico é recuperado pela editora, como explicado para <br> <ul> <li> SaaS in [Get the Token with a HTTP POST](./pc-saas-registration.md#get-the-token-with-an-http-post). </li> <li> Aplicação gerida em [estratégias de autenticação.](./marketplace-metering-service-authentication.md) </li> </ul> |
| | |

*Solicitar exemplo do corpo:*

```json
{
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. 
  "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, from now and until 24 hours back
  "planId": "plan1", // id of the plan purchased for the offer
}
```

>[!NOTE]
>`resourceId` tem um significado diferente para a app SaaS e para app Managed emitindo medidor personalizado. 

Para os planos de aplicações geridas por aplicações Azure, `resourceId` o encontra-se `resourceUsageId` sob o objeto de `billingDetails` metadados da Aplicação Gerida. Um roteiro de exemplo para buscá-lo pode ser encontrado [na utilização do símbolo de identidades geridas pelo Azure](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token). 

Para ofertas saaS, `resourceId` o é o ID de assinatura SaaS. Para obter mais detalhes sobre as subscrições do SaaS, consulte [as subscrições da lista.](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions)

### <a name="responses"></a>Respostas

Código: 200<br>
OK. A emissão de utilização foi aceite e registada do lado da Microsoft para posterior processamento e faturação.

Exemplo de carga útil de resposta: 

```json
{
  "usageEventId": <guid>, // unique identifier associated with the usage event in Microsoft records
  "status": "Accepted" // this is the only value in case of single usage event
  "messageTime": "2020-01-12T13:19:35.3458658Z", // time in UTC this event was accepted
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. For SaaS it's the subscriptionId.
  "quantity": 5.0, // amount of emitted units as recorded by Microsoft
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, as sent by the ISV
  "planId": "plan1", // id of the plan purchased for the offer
}
```

Código: 400 <br>
Mau pedido.

* Dados de pedidos em falta ou inválidos fornecidos.
* `effectiveStartTime` é mais de 24 horas no passado. O evento expirou.
* A subscrição do SaaS não está no estado de Subscrição.

Exemplo de carga útil de resposta: 

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

Código: 403<br>

É proibido. O sinal de autorização não é fornecido, é inválido ou expirado.  Ou o pedido está a tentar aceder a uma subscrição para uma oferta que foi publicada com um ID de app AD AD diferente daquele usado para criar o token de autorização.

Código: 409<br>
Um conflito. Um evento de utilização já foi reportado com sucesso para o ID de recursos especificado, data de utilização eficaz e hora.

Exemplo de carga útil de resposta: 

```json
{
  "additionalInfo": {
    "acceptedMessage": {
      "usageEventId": "<guid>", //unique identifier associated with the usage event in Microsoft records
      "status": "Duplicate",
      "messageTime": "2020-01-12T13:19:35.3458658Z",
      "resourceId": "<guid>", //unique identifier of the resource against which usage is emitted.
      "quantity": 1.0,
      "dimension": "dim1",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "plan1"
    }
  },
  "message": "This usage event already exist.",
  "code": "Conflict"
}
```

## <a name="metered-billing-batch-usage-event"></a>Evento de utilização de lote de faturação medido

O evento de utilização do lote API permite-lhe emitir eventos de utilização para mais de um recurso adquirido ao mesmo tempo. Também permite que emita vários eventos de uso para o mesmo recurso, desde que sejam para diferentes horas de calendário. O número máximo de eventos num único lote é de 25.

**POST:**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Parâmetros de consulta:*

| Parâmetro  | Recomendação     |
| ---------- | -------------------- |
| `ApiVersion` | Use 2018-08-31. |

*Pedido de cabeçalhos:*

| Tipo de conteúdo       | Utilizar o comando `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Valor de cadeia único para acompanhar o pedido do cliente, de preferência um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| `x-ms-correlationid` | Valor único de corda para operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor. Se este valor não for fornecido, um será gerado, e fornecido nos cabeçalhos de resposta. |
| `authorization`      | Um token de acesso único que identifica o ISV que está a fazer esta chamada da API. O formato é `Bearer <access_token>` quando o valor simbólico é recuperado pela editora, como explicado para <br> <ul> <li> SaaS in [Get the Token with a HTTP POST](./pc-saas-registration.md#get-the-token-with-an-http-post). </li> <li> Aplicação gerida em [estratégias de autenticação.](./marketplace-metering-service-authentication.md) </li> </ul> |
| | |


*Solicitar exemplo do corpo:*

```json
{
  "request": [ // list of usage events for the same or different resources of the publisher
    { // first event
      "resourceId": "<guid1>", // Unique identifier of the resource against which usage is emitted. 
      "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
      "dimension": "dim1", //Custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",//Time in UTC when the usage event occurred, from now and until 24 hours back
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // next event
      "resourceId": "<guid2>", 
      "quantity": 39.0, 
      "dimension": "email", 
      "effectiveStartTime": "2018-11-01T23:33:10
      "planId": "gold", // id of the plan purchased for the offer
    }
  ]
}
```

>[!NOTE]
>`resourceId` tem um significado diferente para a app SaaS e para app Managed emitindo medidor personalizado. 

Para os planos de aplicações geridas por aplicações Azure, `resourceId` o encontra-se `resourceUsageId` sob o objeto de `billingDetails` metadados da Aplicação Gerida. Um roteiro de exemplo para buscá-lo pode ser encontrado [na utilização do símbolo de identidades geridas pelo Azure](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token). 

Para ofertas saaS, `resourceId` o é o ID de assinatura SaaS. Para obter mais detalhes sobre as subscrições do SaaS, consulte [as subscrições da lista.](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions)

### <a name="responses"></a>Respostas

Código: 200<br>
OK. A emissão de utilização do lote foi aceite e registada do lado da Microsoft para posterior processamento e faturação. A lista de resposta é devolvida com o estado de cada evento no lote. Deverá iterar através da carga útil de resposta para entender as respostas para cada evento de utilização individual enviado como parte do evento do lote.

Exemplo de carga útil de resposta: 

```json
{
  "count": 2, // number of records in the response
  "result": [
    { // first response
      "usageEventId": "<guid>", // unique identifier associated with the usage event in Microsoft records
      "status": "Accepted" // see list of possible statuses below,
      "messageTime": "2020-01-12T13:19:35.3458658Z", // Time in UTC this event was accepted by Microsoft,
      "resourceId": "<guid1>", // unique identifier of the resource against which usage is emitted.
      "quantity": 5.0, // amount of emitted units as recorded by Microsoft 
      "dimension": "dim1", // custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",// time in UTC when the usage event occurred, as sent by the ISV
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // second response
      "status": "Duplicate",
      "messageTime": "0001-01-01T00:00:00",
      "error": {
        "additionalInfo": {
          "acceptedMessage": {
            "usageEventId": "<guid>",
            "status": "Duplicate",
            "messageTime": "2020-01-12T13:19:35.3458658Z",
            "resourceId": "<guid2>",
            "quantity": 1.0,
            "dimension": "email",
            "effectiveStartTime": "2020-01-12T11:03:28.14Z",
            "planId": "gold"
          }
        },
        "message": "This usage event already exist.",
        "code": "Conflict"
      },
      "resourceId": "<guid2>",
      "quantity": 1.0,
      "dimension": "email",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "gold"
    }
  ]
}
```

Descrição do código de estado referenciado na `BatchUsageEvent` resposta da API:

| Código de estado  | Description |
| ---------- | -------------------- |
| `Accepted` | Aceite. |
| `Expired` | Uso expirado. |
| `Duplicate` | Duplicado uso fornecido. |
| `Error` | Código de erro. |
| `ResourceNotFound` | O recurso de utilização fornecido é inválido. |
| `ResourceNotAuthorized` | Não está autorizado a fornecer uso para este recurso. |
| `InvalidDimension` | A dimensão para a qual a utilização é passada é inválida para esta oferta/plano. |
| `InvalidQuantity` | A quantidade passada é inferior ou igual a 0. |
| `BadArgument` | A entrada está desaparecida ou mal formada. |

Código: 400<br>
Mau pedido. O lote continha mais de 25 eventos de utilização.

Código: 403<br>
É proibido. O sinal de autorização não é fornecido, é inválido ou expirado.  Ou o pedido está a tentar aceder a uma subscrição para uma oferta que foi publicada com um ID de app AD AD diferente daquele usado para criar o token de autorização.

## <a name="development-and-testing-best-practices"></a>Desenvolvimento e teste das melhores práticas

Para testar a emissão de contadores personalizados, implemente a integração com a API medidora, crie um plano para a sua oferta saaS publicada com dimensões personalizadas definidas nele com preço zero por unidade. E publique esta oferta como pré-visualização para que apenas os utilizadores limitados pudessem aceder e testar a integração.

Você também pode usar um plano privado para uma oferta ao vivo existente para limitar o acesso a este plano durante os testes a um público limitado.

## <a name="get-support"></a>Obter suporte

Siga a instrução de [Suporte ao programa de marketplace comercial no Partner Center](./support.md) para compreender as opções de suporte dos editores e abrir um bilhete de apoio com a Microsoft.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o serviço de medição APIs , consulte [o serviço de medição de mercado APIs FAQ](./marketplace-metering-service-apis-faq.md).
