---
title: Analisar os dados de faturação da subscrição do Azure com a API REST | Microsoft Docs
description: Saiba como utilizar as APIs REST do Azure para analisar os detalhes de faturação da subscrição.
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: cost-management-billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 4dc7771862bc922702c5cc321fb3c566212d799a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225913"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Analisar a faturação da subscrição com as APIs REST

As APIs de Relatórios do Azure ajudam-no a analisar e a gerir os custos do Azure.

Os filtros ajudam a personalizar os resultados à medida das suas necessidades.

Aqui, ficará a saber como utilizar uma API REST para devolver detalhes de faturação da subscrição relativos a um determinado intervalo de datas.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>Criar o pedido

O parâmetro `{subscriptionID}` é necessário e identifica a subscrição de destino.

O parâmetro `{billingPeriod}` é necessário e especifica um [período de faturação](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) a decorrer.

Os parâmetros `${startDate}` e `${endDate}` são necessários para este exemplo, mas são opcionais para o ponto final. Especificam o intervalo de datas como cadeias de carateres no formato AAAA-MM-DD (exemplos: `'20180501'` e `'20180615'`).

Os seguintes cabeçalhos são obrigatórios:

|Cabeçalho do pedido|Descrição|
|--------------------|-----------------|
|*Content-Type:*|Necessário. Definido como `application/json`.|
|*Authorization:*|Necessário. Definido com um `Bearer` [token de acesso](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) válido. |

## <a name="response"></a>Resposta

O código de estado 200 (OK) é devolvido para uma resposta bem-sucedida, que contém uma lista dos custos detalhados da sua conta.

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

Cada item no **valor** representa os detalhes sobre a utilização de um serviço:

|Propriedade de resposta|Descrição|
|----------------|----------|
|**subscriptionGuid** | ID exclusivo global da subscrição. |
|**startDate** | Data em que a utilização começou. |
|**endDate** | Data em que a utilização terminou. |
|**useageQuantity** | Quantidade utilizada. |
|**billableQuantity** | Quantidade realmente faturada. |
|**pretaxCost** | Custo faturado, antes dos impostos aplicáveis. |
|**meterDetails** | Informações detalhadas sobre a utilização. |
|**nextLink**| Quando definido, especifica um URL para a próxima “página” de detalhes. Em branco, quando for a última página. |

Este exemplo é abreviado; veja [Listar detalhes de utilização](https://docs.microsoft.com/rest/api/consumption/usagedetails/list#usagedetailslistforbillingperiod-legacy) para obter uma descrição completa de cada campo de resposta.

Outros códigos de estado indicam condições de erro. Nesses casos, o objeto de resposta explica o motivo pelo qual o pedido falhou.

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
- Veja [Descrição geral dos relatórios Enterprise](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Consulte [Enterprise Billing REST API](https://docs.microsoft.com/rest/api/billing/) (API REST de Faturação do Enterprise)
- [Introdução à API REST do Azure](https://docs.microsoft.com/rest/api/azure/)
