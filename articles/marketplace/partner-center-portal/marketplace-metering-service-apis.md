---
title: Serviço de medição APIs - Mercado comercial da Microsoft
description: O evento de utilização AAPI permite-lhe emitir eventos de utilização para ofertas SaaS no Microsoft AppSource e Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 95eba648219413923ce27d433a5236877c4953f3
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725470"
---
# <a name="marketplace-metering-service-apis"></a>APIs do serviço de medição do Marketplace

O evento de utilização A API permite-lhe emitir eventos de uso para uma entidade adquirida específica. O pedido de utilização refere a dimensão dos serviços de medição definida pela editora na publicação da oferta.

## <a name="usage-event"></a>Evento de uso

**POST**:`https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | A versão da operação a utilizar para este pedido. A mais recente versão da API é 2018-08-31. |

*Cabeçalhos de pedido:*

| Tipo de conteúdo       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Valor de cadeia único para acompanhar o pedido do cliente, de preferência um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| `x-ms-correlationid` | Valor de cadeia único para operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| `authorization`   | [Obtenha o token do portador da Web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Nota: Ao epreito o `Bearer` símbolo obtido a partir do link referenciado. |

>[!Note]
>Para os planos de Apps Geridas por Aplicações geridas pela Aplicação Azure, `resourceId` encontra-se no âmbito do objeto de `resourceUsageId` `billingDetails` metadados da App Gerida.  Um roteiro de exemplo para a sua busca pode ser encontrado na [utilização do símbolo de identidades geridas pelo Azure](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token).  Para ofertas SaaS, o `resourceId` é o ID de subscrição SaaS.  Para mais detalhes sobre as subscrições do SaaS, consulte [as subscrições da lista.](./pc-saas-fulfillment-api-v2.md#list-subscriptions)

*Pedido:*

```json
{
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

### <a name="responses"></a>Respostas

Código: 200<br>
OK 

```json
{
  "usageEventId": "Unique identifier associated with the usage event",
  "status": "Accepted",
  "messageTime": "Time this message was created in UTC",
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

Código: 400 <br>
Mau pedido, dados em falta ou inválidos fornecidos ou expirados

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
Mau pedido, dados em falta ou inválidos fornecidos ou expirados

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

Código: 409<br>
Conflito, quando recebemos a chamada de utilização para o ID do recurso de uso, e uso eficaz, que já existe. A resposta conterá `additionalInfo` um campo que contém informações sobre a mensagem aceite.

```json
{
  "code": "Conflict",
  "additionalInfo": {
    "usageEventId": "Unique identifier associated with the usage event",
    "status": "Accepted|NotProcessed|Expired",
    "messageTime": "Time this message was created in UTC",
    "resourceId": "Identifier of the resource against which usage is emitted",
    "quantity": 5.0,
    "dimension": "Dimension identifier",
    "effectiveStartTime": "Time in UTC when the usage event occurred",
    "planId": "Plan associated with the purchased offer"
  }
}
```

## <a name="batch-usage-event"></a>Evento de utilização de lotes

O evento de utilização do lote API permite-lhe emitir eventos de utilização para mais de uma entidade adquirida ao mesmo tempo. O pedido de utilização do lote refere a dimensão dos serviços de medição definida pela editora ao publicar a oferta.

>[!Note]
>Pode registar várias ofertas saaS no mercado comercial da Microsoft. Cada oferta saaS registada tem um pedido único da AD Azure que está registado para fins de autenticação e autorização. Os eventos emitidos em lote devem pertencer a ofertas com a mesma aplicação Azure AD no momento do registo da oferta.

**POST:**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | A versão da operação a utilizar para este pedido. A mais recente versão da API é 2018-08-31. |

*Cabeçalhos de pedido:*

| Tipo de conteúdo       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Valor de cadeia único para acompanhar o pedido do cliente, de preferência um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| `x-ms-correlationid` | Valor de cadeia único para operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se este valor não for fornecido, um será gerado, e fornecido nos cabeçalhos de resposta. |
| `authorization`      | [Obtenha o token do portador da Web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Nota: Ao epreito o `Bearer` símbolo obtido a partir do link referenciado.  |

*Pedido:*
```json
{
  "request": [
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    },
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    }
  ]
}
```
### <a name="responses"></a>Respostas

Código: 200<br>
OK

```json
{
  "count": 2,
  "result": [
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    },
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    }
  ]
}
```

Descrição do código de estado referenciado na `BatchUsageEvent` resposta da API:

| Código de estado  | Description |
| ---------- | -------------------- |
| `Accepted` | Código aceite. |
| `Expired` | Uso expirado. |
| `Duplicate` | Utilização duplicada fornecida. |
| `Error` | Código de erro. |
| `ResourceNotFound` | O recurso de utilização fornecido é inválido. |
| `ResourceNotAuthorized` | Não está autorizado a fornecer uso para este recurso. |
| `InvalidDimension` | A dimensão para a qual o uso é passado é inválida para esta oferta/plano. |
| `InvalidQuantity` | A quantidade passada é < 0. |
| `BadArgument` | A entrada está em falta ou mal formada. |

Código: 400<br>
Mau pedido, dados em falta ou inválidos fornecidos ou expirados

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "Invalid data format.",
      "target": "usageEventRequest",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```
Código: 403<br>
O utilizador não está autorizado a fazer esta chamada

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>Próximos passos

Para mais informações, consulte a [faturação de medidor SaaS.](./saas-metered-billing.md)
