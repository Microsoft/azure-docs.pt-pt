---
title: Reveja os dados de faturação de subscrição do Azure com a REST API | Documentos da Microsoft
description: Saiba como utilizar APIs REST do Azure para rever os detalhes de faturação de subscrição.
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
ms.author: erikre
ms.openlocfilehash: 15725989ef786f94421eddf647f101e3e73633fb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57890768"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Reveja a faturação de subscrição com REST APIs

Ajuda de APIs de relatórios do Azure, reveja e gira os seus custos do Azure.

Filtros de ajudar a personalizar os resultados para atender às suas necessidades.

Aqui, vai aprender a utilizar uma API REST para devolver os detalhes de faturação de subscrição para um determinado intervalo de datas.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>Criar o pedido

O `{subscriptionID}` parâmetro é necessário e identifica a subscrição de destino.

O `{billingPeriod}` parâmetro é necessário e especifica um atual [período de faturação](https://docs.microsoft.com/rest/api/billing/billingperiods/get#billingperiod).

O `${startDate}` e `${endDate}` parâmetros são necessários para este exemplo, mas opcional para o ponto final. Especifica o intervalo de datas como cadeias de caracteres no formato dd-MM-AAAA (exemplos: `'20180501'` e `'20180615'`).

Os seguintes cabeçalhos são necessários:

|Cabeçalho do pedido|Descrição|
|--------------------|-----------------|
|*Content-Type:*|Necessário. Definido como `application/json`.|
|*Autorização:*|Necessário. Definido como válido `Bearer` [token de acesso](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

## <a name="response"></a>Resposta

Código de estado 200 (OK) é devolvido para uma resposta com êxito, que contém uma lista dos custos detalhadas para a sua conta.

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

Cada item na **valor** representa um detalhes sobre a utilização de um serviço:

|Propriedade de resposta|Descrição|
|----------------|----------|
|**subscriptionGuid** | ID exclusivo global para a subscrição. |
|**startDate** | Data a utilização iniciada. |
|**endDate** | A utilização terminada de datas. |
|**useageQuantity** | Quantidade usada. |
|**billableQuantity** | Quantidade, na verdade, é cobrada. |
|**pretaxCost** | Custo faturado, antes dos impostos aplicáveis. |
|**meterDetails** | Obter informações detalhadas sobre a utilização. |
|**nextLink**| Quando definida, especifica um URL para o próximo "page" dos detalhes. Em branco quando a página é o último. |

Neste exemplo é abreviado; ver [lista os detalhes de utilização](https://docs.microsoft.com/rest/api/consumption/usagedetails/list#usagedetailslistforbillingperiod) para obter uma descrição completa de cada campo de resposta.

Outros códigos de estado indicam condições de erro. Nestes casos, o objeto de resposta explica por que o pedido falhou.

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

## <a name="next-steps"></a>Passos Seguintes
- Revisão [Enterprise descrição geral dos relatórios](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Investigar [Enterprise REST API de faturação](https://docs.microsoft.com/rest/api/billing/)
- [Introdução à API REST do Azure](https://docs.microsoft.com/rest/api/azure/)
