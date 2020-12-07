---
title: Migrar dos Relatórios do Enterprise para as APIs do Azure Resource Manager
description: Este artigo ajuda-o a compreender as diferenças entre as APIs de Relatórios e as APIs do Azure Resource Manager, o que esperar da migração para as APIs do Azure Resource Manager e as novas capacidades que estão disponíveis com estas.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 11/19/2020
ms.author: banders
ms.openlocfilehash: 93dda4fc3a152b0a07a95ff327c9ea619f25787c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355825"
---
# <a name="migrate-from-enterprise-reporting-to-azure-resource-manager-apis"></a>Migrar dos Relatórios do Enterprise para as APIs do Azure Resource Manager

Este artigo ajuda os programadores que compilaram soluções personalizadas com as [APIs de Relatórios do Enterprise para Clientes Enterprise](../manage/enterprise-api.md) a migrar para as APIs do Azure Resource Manager para o Cost Management. O suporte de Principal de Serviço para as mais recentes APIs do Azure Resource Manager está agora em disponibilidade geral. As APIs do Azure Resource Manager estão em desenvolvimento ativo. Considere migrar para as mesmas em vez de utilizar as APIs de Relatórios do Azure mais antigas para os clientes Enterprise. As APIs mais antigas estão a ser descontinuadas. Este artigo ajuda-o a compreender as diferenças entre as APIs de Relatórios e as APIs do Azure Resource Manager, o que esperar da migração para as APIs do Azure Resource Manager e as novas capacidades que estão disponíveis com estas.

## <a name="api-differences"></a>Diferenças entre as APIs

As informações seguintes descrevem as diferenças entre as APIs de Relatórios mais antigas para os Clientes Enterprise e as APIs mais recentes do Azure Resource Manager.

| **Utilizar** | **APIs do Contrato Enterprise** | **APIs do Azure Resource Manager** |
| --- | --- | --- |
| Autenticação | Chave de API aprovisionada no portal do Contrato Enterprise (EA) | Autenticação do Azure Active Directory (Azure AD) mediante a utilização de tokens de Utilizador ou Principais de Serviço. Os Principais de Serviço substituem as Chaves de API. |
| Âmbitos e Permissões | Todos os pedidos têm o âmbito Inscrição. As atribuições de permissão da Chave de API determinarão se são devolvidos os dados de toda a Inscrição, de um Departamento ou de uma Conta específica. Sem autenticação do utilizador. | É atribuído acesso aos Utilizadores ou Principais de Serviços ao âmbito Inscrição, Departamento ou Conta. |
| Ponto final de URI | https://consumption.azure.com | https://management.azure.com |
| Estado do Desenvolvimento | Em modo de manutenção. A caminho da descontinuação. | A ser desenvolvidas ativamente |
| APIs disponíveis | Limitadas às que estão atualmente disponíveis | Estão disponíveis APIs equivalentes para substituir cada API do EA. <p> Também tem à sua disposição [APIs do Cost Management](/rest/api/cost-management/) adicionais, incluindo: <p> <ul><li>Orçamentos</li><li>Alertas<li>Exportações</li></ul> |

## <a name="migration-checklist"></a>Lista de verificação da migração

- Familiarizar-se com as [APIs REST do Azure Resource Manager](/rest/api/azure).
- Determinar que APIs do EA vai utilizar e saiba para que APIs do Azure Resource Manager mover em [Mapeamento de APIs do EA para as novas APIs do Azure Resource Manager](#ea-api-mapping-to-new-azure-resource-manager-apis).
- Configurar a autorização e a autenticação do serviço para as APIs do Azure Resource Manager
  - Se ainda não estiver a utilizar as APIs do Azure Resource Manager, veja [Register your client app with Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad) (Registar a aplicação de cliente no Azure Active Directory). O registo cria um principal de serviço a utilizar para chamar as APIs.
  - Atribua ao principal de serviço acesso aos âmbitos necessários, conforme descrito abaixo.
  - Atualize o código de programação para [utilizar a autenticação do Azure AD](/rest/api/azure/#create-the-request) com o Principal de Serviço.
- Testar as APIs e, em seguida, atualizar o código de programação para substituir as chamadas às APIs do EA pelas chamadas às APIs do Azure Resource Manager.
- Atualize o processamento de erros para utilizar os novos códigos de erro. Algumas considerações incluem:
  - As APIs do Azure Resource Manager têm um período de tempo limite de 60 segundos.
  - As APIs do Azure Resource Manager têm limitação de taxa em vigor. Se as taxas forem excedidas, essa limitação resulta no erro de limitação 429. Compile as suas soluções de modo a que não faça demasiadas chamadas à API num curto período de tempo.
- Reveja as outras APIs do Cost Management que estão disponíveis através do Azure Resource Manager e avalie-as para utilização mais tarde. Para obter mais informações, veja [Utilizar as APIs do Cost Management](#use-additional-cost-management-apis).

## <a name="assign-service-principal-access-to-azure-resource-manager-apis"></a>Atribuir acesso ao Principal de Serviço às APIs do Azure Resource Manager

Depois de criar um Principal de Serviço para chamar programaticamente as APIs do Azure Resource Manager, tem de lhe atribuir as permissões adequadas para autorizar e executar pedidos no Azure Resource Manager. Existem dois frameworks de permissões para diferentes cenários.

### <a name="azure-billing-hierarchy-access"></a>Acesso à Hierarquia da Faturação do Azure

Para atribuir permissões do Principal de Serviço aos seus âmbitos de Conta de Faturação do Enterprise, Departamentos ou Contas de Inscrição, utilize as APIs [Permissões de Faturação](/rest/api/billing/2019-10-01-preview/billingpermissions), [Definições da Função de Faturação](/rest/api/billing/2019-10-01-preview/billingroledefinitions) e [Atribuições de Função de Faturação](/rest/api/billing/2019-10-01-preview/billingroleassignments).

- Utilize as APIs de Permissões de Faturação para identificar as permissões que um Principal de Serviço já tem num determinado âmbito, como Conta de Faturação ou Departamento.
- Utilize as APIs de Definições de Função de Faturação para enumerar as funções disponíveis que podem ser atribuídas ao seu Principal de Serviço.
  - Neste momento, só podem ser atribuídas aos Principais de Serviço as funções Administrador do EA Só de Leitura e Administrador de Departamento Só de Leitura.
- Utilize as APIs de Atribuições de Função de Faturação para atribuir uma função ao seu Principal de Serviço.

O exemplo seguinte mostra como chamar a API Atribuições de Funções para conceder acesso a um Principal de Serviço à sua conta de faturação. Para fazer estas configurações de permissões únicas, recomendamos utilizar o [PostMan](https://postman.com).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/createBillingRoleAssignment?api-version=2019-10-01-preview
```

#### <a name="request-body"></a>Corpo do Pedido

```json
{
  "principalId": "00000000-0000-0000-0000-000000000000",
  "billingRoleDefinitionId": "/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.Billing/billingRoleDefinition/10000000-aaaa-bbbb-cccc-100000000000"
}

```

### <a name="azure-role-based-access-control"></a>Controlo de acesso baseado em funções do Azure

O novo suporte de Principal de Serviço estende-se a âmbitos específicos do Azure, como grupos de gestão, subscrições e grupos de recursos. Pode atribuir ao Principal de Serviço permissões para esses âmbitos diretamente no [portal do Azure](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) ou através do [Azure PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#assign-the-application-to-a-role).

## <a name="ea-api-mapping-to-new-azure-resource-manager-apis"></a>Mapeamento das APIs do EA para as novas APIs do Azure Resource Manager

Utilize a tabela abaixo para identificar as APIs do EA que utiliza atualmente e as APIs do Azure Resource Manager a utilizar como substitutas.

| **Cenário** | **APIs do EA** | **APIs do Azure Resource Manager** |
| --- | --- | --- |
| Resumo do Saldo | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) |[Microsoft.Consumption/balances](/rest/api/consumption/balances/getbybillingaccount) |
| Folha de Preços | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | [Microsoft.Consumption/pricesheets/default](/rest/api/consumption/pricesheet) – utilizar para preços negociados <p> [API de Preços para Retalho](/rest/api/cost-management/retail-prices/azure-retail-prices) – utilizar para preços para retalho |
| Detalhes da Instância Reservada | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.CostManagement/generateReservationDetailsReport](/rest/api/cost-management/generatereservationdetailsreport) |
| Resumo da Instância Reservada | [/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid) |
| Recomendações da Instância Reservada | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)<p>[/SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Custos da Instância Reservada | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | [Microsoft.Consumption/reservationTransactions](/rest/api/consumption/reservationtransactions/list) |

## <a name="migration-details-by-api"></a>Detalhes da migração por API

As secções seguintes mostram exemplos de pedidos da API antiga com exemplos da API de substituição nova.

### <a name="balance-summary-api"></a>API de Resumo de Saldo

Utilize os URIs de pedido seguintes ao chamar a nova API de Resumo de Saldo. O seu número de inscrição deverá ser utilizado como billingAccountId.

#### <a name="supported-requests"></a>Pedidos suportados

[Obter para Inscrição](/rest/api/consumption/balances/getbybillingaccount)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/balances?api-version=2019-10-01
```

### <a name="response-body-changes"></a>Alterações ao corpo da resposta

_Corpo da resposta antiga_:

```json
{
        "id": "enrollments/100/billingperiods/201507/balancesummaries",
          "billingPeriodId": 201507,
          "currencyCode": "USD",
          "beginningBalance": 0,
          "endingBalance": 1.1,
          "newPurchases": 1,
          "adjustments": 1.1,
          "utilized": 1.1,
          "serviceOverage": 1,
          "chargesBilledSeparately": 1,
          "totalOverage": 1,
          "totalUsage": 1.1,
          "azureMarketplaceServiceCharges": 1,
          "newPurchasesDetails": [
            {
              "name": "",
              "value": 1
            }
          ],
          "adjustmentDetails": [
            {
              "name": "Promo Credit",
              "value": 1.1
            },
            {
              "name": "SIE Credit",
              "value": 1.0
            }
          ]
    }

```

_Corpo da resposta nova_:

Os mesmos dados estão agora disponíveis no campo `properties` da nova resposta da API. Poderá haver alterações mínimas à ortografia dos nomes de alguns campos.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/balances/balanceId1",
  "name": "balanceId1",
  "type": "Microsoft.Consumption/balances",
  "properties": {
    "currency": "USD  ",
    "beginningBalance": 3396469.19,
    "endingBalance": 2922371.02,
    "newPurchases": 0,
    "adjustments": 0,
    "utilized": 474098.17,
    "serviceOverage": 0,
    "chargesBilledSeparately": 0,
    "totalOverage": 0,
    "totalUsage": 474098.17,
    "azureMarketplaceServiceCharges": 609.82,
    "billingFrequency": "Month",
    "priceHidden": false,
    "newPurchasesDetails": [
      {
        "name": "Promo Purchase",
        "value": 1
      }
    ],
    "adjustmentDetails": [
      {
        "name": "Promo Credit",
        "value": 1.1
      },
      {
        "name": "SIE Credit",
        "value": 1
      }
    ]
  }
}

```

### <a name="price-sheet"></a>Folha de Preços

Utilize os URIs de pedido seguintes ao chamar a nova API de Folha de Preços.

#### <a name="supported-requests"></a>Pedidos suportados

 Pode utilizar os seguintes âmbitos para chamar a API:

- Inscrição: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- Subscrição: `subscriptions/{subscriptionId}`

[_Obter para Período de Faturação atual_](/rest/api/consumption/pricesheet/get)


```json
https://management.azure.com/{scope}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

[_Obter para Período de Faturação especificado_](/rest/api/consumption/pricesheet/getbybillingperiod)

```json
https://management.azure.com/{scope}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Alterações ao corpo da resposta

_Resposta antiga_:

```json
      [
        {
              "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
              "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
              "meterName": "A1 VM",
              "unitOfMeasure": "100 Hours",
              "includedQuantity": 0,
              "partNumber": "N7H-00015",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        {
              "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
              "billingPeriodId": "201404",
            "meterId": "dc210ecb-97e8-4522-8134-5385494233c0",
              "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
              "unitOfMeasure": "100 GB",
              "includedQuantity": 0,
              "partNumber": "N9H-00402",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        ...
    ]

```

_Resposta nova_:

Os dados antigos estão agora disponíveis no campo `pricesheets` da nova resposta da API. Também são fornecidas informações dos detalhes dos medidores.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/pricesheets/default",
  "name": "default",
  "type": "Microsoft.Consumption/pricesheets",
  "properties": {
    "nextLink": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/microsoft.consumption/pricesheets/default?api-version=2018-01-31&$skiptoken=AQAAAA%3D%3D&$expand=properties/pricesheets/meterDetails",
    "pricesheets": [
      {
        "billingPeriodId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702",
        "meterId": "00000000-0000-0000-0000-000000000000",
        "unitOfMeasure": "100 Hours",
        "includedQuantity": 100,
        "partNumber": "XX-11110",
        "unitPrice": 0.00000,
        "currencyCode": "EUR",
        "offerId": "OfferId 1",
        "meterDetails": {
          "meterName": "Data Transfer Out (GB)",
          "meterCategory": "Networking",
          "unit": "GB",
          "meterLocation": "Zone 2",
          "totalIncludedQuantity": 0,
          "pretaxStandardRate": 0.000
        }
      }
    ]
  }
}

```

### <a name="reserved-instance-usage-details"></a>Detalhes da utilização da Instância Reservada

A Microsoft não está a trabalhar ativamente nas APIs de Detalhes de Reserva baseada em sincronização. Como parte da migração, recomendamos que mude para o novo padrão da chamada à API assíncrona suportada por SPN. Os pedidos assíncronos lidam melhor com grandes quantidades de dados e reduzirão os erros de tempo limite excedido.

#### <a name="supported-requests"></a>Pedidos suportados

Ao chamar a nova API de Detalhes de Reserva Assíncrona, utilize os seguintes URIs de pedido. O seu número de inscrição deverá ser utilizado como `billingAccountId`. Pode utilizar os seguintes âmbitos para chamar a API:

- Inscrição: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

#### <a name="sample-request-to-generate-a-reservation-details-report"></a>Exemplo de pedido para gerar um relatório de detalhes de reserva

```json
POST 
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/generateReservationDetailsReport?startDate={startDate}&endDate={endDate}&api-version=2019-11-01

```

#### <a name="sample-request-to-poll-report-generation-status"></a>Exemplo de pedido para sondar o estado da geração do relatório

```json
GET
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/reservationDetailsOperationResults/{operationId}?api-version=2019-11-01

```

#### <a name="sample-poll-response"></a>Exemplo de resposta da sondagem

```json
{
  "status": "Completed",
  "properties": {
    "reportUrl": "https://storage.blob.core.windows.net/details/20200911/00000000-0000-0000-0000-000000000000?sv=2016-05-31&sr=b&sig=jep8HT2aphfUkyERRZa5LRfd9RPzjXbzB%2F9TNiQ",
    "validUntil": "2020-09-12T02:56:55.5021869Z"
  }
}

```

#### <a name="response-body-changes"></a>Alterações ao corpo da resposta

A resposta da API de Detalhes de Reserva síncrona antiga é a seguinte.

_Resposta antiga_:

```json
{
    "reservationOrderId": "00000000-0000-0000-0000-000000000000",
    "reservationId": "00000000-0000-0000-0000-000000000000",
    "usageDate": "2018-02-01T00:00:00",
    "skuName": "Standard_F2s",
    "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/resourvegroup1/providers/microsoft.compute/virtualmachines/VM1",
    "totalReservedQuantity": 18.000000000000000,
    "reservedHours": 432.000000000000000,
    "usedHours": 400.000000000000000
}

```

_Resposta nova_:

A API nova cria um ficheiro CSV por si. Veja os campos seguintes do ficheiro.

| **Propriedade Antiga** | **Propriedade Nova** | **Notas** |
| --- | --- | --- |
|  | InstanceFlexibilityGroup | Propriedade nova para flexibilidade da instância. |
|  | InstanceFlexibilityRatio | Propriedade nova para flexibilidade da instância. |
| instanceId | InstanceName |  |
|  | Tipo | É uma propriedade nova. O valor é `None`, `Reservation` ou `IncludedQuantity`. |
| reservationId | ReservationId |  |
| reservationOrderId | ReservationOrderId |  |
| reservedHours | ReservedHours |  |
| skuName | SkuName |  |
| totalReservedQuantity | TotalReservedQuantity |  |
| usageDate | UsageDate |  |
| usedHours | UsedHours |  |

### <a name="reserved-instance-usage-summary"></a>Reserved Instance Usage Summary (Resumo da Utilização de Instâncias Reservadas)

Utilize os seguintes URIs de pedido para chamar a nova API de Resumos de Reserva.

#### <a name="supported-requests"></a>Pedidos suportados

 Chame a API com os seguintes âmbitos:

- Inscrição: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

[_Obter Resumo de Reserva Diariamente_](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

[_Obter Resumo de Reserva Mensalmente_](/rest/api/consumption/reservationssummaries/list#reservationsummariesmonthlywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Alterações ao corpo da resposta

_Resposta antiga_:

```json
[
     {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_F1s",
        "reservedHours": 24,
        "usageDate": "2018-05-01T00:00:00",
        "usedHours": 23,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 95.83,
        "maxUtilizationPercentage": 100
    }
]

```

_Resposta nova_:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/12345/providers/Microsoft.Consumption/reservationSummaries/reservationSummaries_Id1",
      "name": "reservationSummaries_Id1",
      "type": "Microsoft.Consumption/reservationSummaries",
      "tags": null,
      "properties": {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_B1s",
        "reservedHours": 720,
        "usageDate": "2018-09-01T00:00:00-07:00",
        "usedHours": 0,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 0,
        "maxUtilizationPercentage": 0
      }
    }
  ]
}

```

### <a name="reserved-instance-recommendations"></a>Recomendações da Instância Reservada

Utilize os seguintes URIs de pedido para chamar a nova API de Recomendações de Reserva.

#### <a name="supported-requests"></a>Pedidos suportados

 Chame a API com os seguintes âmbitos:

- Inscrição: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- Subscrição: `subscriptions/{subscriptionId}`
- Grupos de recursos: `subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`

[_Obter Recomendações_](/rest/api/consumption/reservationrecommendations/list)

Estão disponíveis através desta API as recomendações de âmbitos partilhados e únicos. Também pode filtrar pelo âmbito, como parâmetro opcional da API.

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Alterações ao corpo da resposta

As recomendações para os âmbitos partilhados e únicos estão combinadas numa API individual.

_Resposta antiga_:

```json
[{
    "subscriptionId": "1111111-1111-1111-1111-111111111111",
    "lookBackPeriod": "Last7Days",
    "meterId": "2e3c2132-1398-43d2-ad45-1d77f6574933",
    "skuName": "Standard_DS1_v2",
    "term": "P1Y",
    "region": "westus",
    "costWithNoRI": 186.27634908960002,
    "recommendedQuantity": 9,
    "totalCostWithRI": 143.12931642978083,
    "netSavings": 43.147032659819189,
    "firstUsageDate": "2018-02-19T00:00:00"
}
]

```

_Resposta nova_:

```json
{
  "value": [
    {
      "id": "billingAccount/123456/providers/Microsoft.Consumption/reservationRecommendations/00000000-0000-0000-0000-000000000000",
      "name": "00000000-0000-0000-0000-000000000000",
      "type": "Microsoft.Consumption/reservationRecommendations",
      "location": "westus",
      "sku": "Standard_DS1_v2",
      "kind": "legacy",
      "properties": {
        "meterId": "00000000-0000-0000-0000-000000000000",
        "term": "P1Y",
        "costWithNoReservedInstances": 12.0785105,
        "recommendedQuantity": 1,
        "totalCostWithReservedInstances": 11.4899644807748,
        "netSavings": 0.588546019225182,
        "firstUsageDate": "2019-07-07T00:00:00-07:00",
        "scope": "Shared",
        "lookBackPeriod": "Last7Days",
        "instanceFlexibilityRatio": 1,
        "instanceFlexibilityGroup": "DSv2 Series",
        "normalizedSize": "Standard_DS1_v2",
        "recommendedQuantityNormalized": 1,
        "skuProperties": [
          {
            "name": "Cores",
            "value": "1"
          },
          {
            "name": "Ram",
            "value": "1"
          }
        ]
      }
    },
   ]
}

```

### <a name="reserved-instance-charges"></a>Custos da Instância Reservada

Utilize os seguintes URIs de pedido para chamar a nova API de Custos de Instância Reservada.

#### <a name="supported-requests"></a>Pedidos suportados

[_Obter Custos de Reserva por Intervalo de Datas_](/rest/api/consumption/reservationtransactions/list)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/reservationTransactions?$filter=properties/eventDate+ge+2020-05-20+AND+properties/eventDate+le+2020-05-30&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Alterações ao corpo da resposta

_Resposta antiga_:

```json
[
    {
        "purchasingEnrollment": "string",
        "armSkuName": "Standard_F1s",
        "term": "P1Y",
        "region": "eastus",
        "PurchasingsubscriptionGuid": "00000000-0000-0000-0000-000000000000",
        "PurchasingsubscriptionName": "string",
        "accountName": "string",
        "accountOwnerEmail": "string",
        "departmentName": "string",
        "costCenter": "",
        "currentEnrollment": "string",
        "eventDate": "string",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_F1s eastus 1 Year",
        "eventType": "Purchase",
        "quantity": int,
        "amount": double,
        "currency": "string",
        "reservationOrderName": "string"
    }
]

```
_Resposta nova_:

```json
{
  "value": [
    {
      "id": "/billingAccounts/123456/providers/Microsoft.Consumption/reservationtransactions/201909091919",
      "name": "201909091919",
      "type": "Microsoft.Consumption/reservationTransactions",
      "tags": {},
      "properties": {
        "eventDate": "2019-09-09T19:19:04Z",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_DS1_v2 westus 1 Year",
        "eventType": "Cancel",
        "quantity": 1,
        "amount": -21,
        "currency": "USD",
        "reservationOrderName": "Transaction-DS1_v2",
        "purchasingEnrollment": "123456",
        "armSkuName": "Standard_DS1_v2",
        "term": "P1Y",
        "region": "westus",
        "purchasingSubscriptionGuid": "11111111-1111-1111-1111-11111111111",
        "purchasingSubscriptionName": "Infrastructure Subscription",
        "accountName": "Microsoft Infrastructure",
        "accountOwnerEmail": "admin@microsoft.com",
        "departmentName": "Unassigned",
        "costCenter": "",
        "currentEnrollment": "123456",
        "billingFrequency": "recurring"
      }
    },
  ]
}

```

## <a name="use-additional-cost-management-apis"></a>Utilizar as APIs adicionais do Cost Management

Depois de migrar para as APIs do Azure Resource Manager, no que diz respeito aos seus cenários de relatórios atuais, também pode utilizar muitas outras APIs. As APIs estão disponíveis através do Azure Resource Manager e podem ser automatizadas com a autenticação baseada em Principal de Serviço. Eis um resumo rápido das novas capacidades que pode utilizar.

- [Orçamentos](/rest/api/consumption/budgets/createorupdate) – utilizar para definir limiares para monitorizar proativamente os seus custos, alertar intervenientes relevantes e automatizar ações em resposta a violações aos limiares.

- [Alertas](/rest/api/cost-management/alerts) – utilizar para ver informações de alertas, incluindo, entre outros, alertas de orçamentos, alertas de faturas, alertas de crédito e alertas de quota.

- [Exportações](/rest/api/cost-management/exports) – utilizar para agendar a exportação recorrente dos seus custos para uma conta do Armazenamento do Azure à sua escolha. É a solução recomendada para os clientes com uma grande presença no Azure e que pretendam analisar os respetivos dados e utilizá-los nos seus próprios sistemas internos.

## <a name="next-steps"></a>Passos seguintes

- Familiarizar-se com as [APIs REST do Azure Resource Manager](/rest/api/azure).
- Se necessário, determine que APIs do EA vai utilizar e saiba para que APIs do Azure Resource Manager mover em [Mapeamento de APIs do EA para as novas APIs do Azure Resource Manager](#ea-api-mapping-to-new-azure-resource-manager-apis).
- Se ainda não estiver a utilizar as APIs do Azure Resource Manager, veja [Register your client app with Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad) (Registar a aplicação de cliente no Azure Active Directory).
- Se necessário, atualize o código de programação para [utilizar a autenticação do Azure AD](/rest/api/azure/#create-the-request) com o Principal de Serviço.