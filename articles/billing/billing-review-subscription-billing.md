---
title: Examinar os dados de cobrança da assinatura do Azure com a API REST | Microsoft Docs
description: Saiba como usar as APIs REST do Azure para examinar detalhes de cobrança da assinatura.
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: banders
ms.openlocfilehash: 8cfa429b18fb282f5c1f85d2fd1637704653b855
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443040"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Examinar a cobrança da assinatura usando as APIs REST

As APIs de relatórios do Azure ajudam você a revisar e gerenciar seus custos do Azure.

Os filtros ajudam a personalizar os resultados para atender às suas necessidades.

Aqui, você aprende a usar uma API REST para retornar detalhes de cobrança da assinatura para um determinado intervalo de datas.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>Criar o pedido

O `{subscriptionID}` parâmetro é necessário e identifica a assinatura de destino.

O `{billingPeriod}` parâmetro é obrigatório e especifica um [período de cobrança](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods)atual.

Os `${startDate}` parâmetros `${endDate}` e são necessários para este exemplo, mas opcional para o ponto de extremidade. Eles especificam o intervalo de datas como cadeias de caracteres no formato aaaa-mm-dd `'20180501'` ( `'20180615'`exemplos: e).

Os seguintes cabeçalhos são necessários:

|Cabeçalho da solicitação|Descrição|
|--------------------|-----------------|
|*Tipo de conteúdo:*|Necessário. Defina como `application/json`.|
|*Authorization:*|Necessário. Defina como um token `Bearer` de [acesso](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)válido. |

## <a name="response"></a>Resposta

O código de status 200 (OK) é retornado para uma resposta bem-sucedida, que contém uma lista de custos detalhados para sua conta.

``` json
{
  "value": [
    {
      "id": "/subscriptions/{$subscriptionID}/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/{$detailsID}",
      "name": "{$detailsID}",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}",
        "invoiceId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/invoices/${invoiceID}",
        "usageStart": "${startDate}}",
        "usageEnd": "${endDate}",
        "currency": "USD",
        "usageQuantity": "${usageQuantity}",
        "billableQuantity": "${billableQuantity}",
        "pretaxCost": "${cost}",
        "meterId": "${meterID}",
        "meterDetails": "${meterDetails}"
      }
    }
  ],
  "nextLink": "${nextLinkURL}"
}
```

Cada item em **valor** representa detalhes sobre o uso de um serviço:

|Propriedade de resposta|Descrição|
|----------------|----------|
|**subscriptionGuid** | ID global exclusivo para a assinatura. |
|**startDate** | Data em que o uso foi iniciado. |
|**Término** | Data em que o uso terminou. |
|**useageQuantity** | Quantidade usada. |
|**billableQuantity** | Quantidade realmente cobrada. |
|**pretaxCost** | Custo faturado, antes dos impostos aplicáveis. |
|**meterDetails** | Informações detalhadas sobre o uso. |
|**nextLink**| Quando definido, especifica uma URL para a próxima "página" de detalhes. Em branco quando a página for a última. |

Este exemplo é abreviado; consulte [listar detalhes de uso](https://docs.microsoft.com/rest/api/consumption/usagedetails/list#usagedetailslistforbillingperiod) para obter uma descrição completa de cada campo de resposta.

Outros códigos de status indicam condições de erro. Nesses casos, o objeto de resposta explica por que a solicitação falhou.

``` json
{
  "error": [
    {
      "code": "Error type.",
      "message": "Error response describing why the operation failed."
    }
  ]
}
```

## <a name="next-steps"></a>Passos seguintes
- Examinar [visão geral de relatórios empresariais](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Investigar [API REST de cobrança Enterprise](https://docs.microsoft.com/rest/api/billing/)
- [Introdução à API REST do Azure](https://docs.microsoft.com/rest/api/azure/)
