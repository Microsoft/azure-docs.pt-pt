---
title: Serviço de medição APIs - Mercado comercial da Microsoft
description: O evento de utilização AAPI permite-lhe emitir eventos de utilização para ofertas SaaS no Microsoft AppSource e Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 159d2c60fc1fc5ad1f21f2b948208eaae0d06208
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857863"
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
| `authorization`   | [Obtenha o token do portador da Web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Nota: Ao epreito `Bearer` o símbolo obtido a partir do link referenciado. |

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
Conflito, quando recebemos a chamada de utilização para o ID do recurso de uso, e uso eficaz, que já existe. A resposta `additionalInfo` conterá um campo que contém informações sobre a mensagem aceite.

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
| `authorization`      | [Obtenha o token do portador da Web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Nota: Ao epreito `Bearer` o símbolo obtido a partir do link referenciado.  |

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

Descrição do código `BatchUsageEvent` de estado referenciado na resposta da API:

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

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte a [faturação de medidor SaaS.](./saas-metered-billing.md)
