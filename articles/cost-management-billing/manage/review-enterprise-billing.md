---
title: Analisar dados de faturação da inscrição do Azure Enterprise com a API REST | Microsoft Docs
description: Saiba como utilizar as APIs REST do Azure para analisar as informações de faturação da inscrição do Enterprise.
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
ms.openlocfilehash: 222b0358f027e0a6687ca0710e3cf5b80f292c4e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75993469"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Analisar a faturação da inscrição do Enterprise com as APIs REST

As APIs de Relatórios do Azure ajudam-no a analisar e a gerir os custos do Azure.

Neste artigo, ficará a saber como obter as informações de faturação associadas às contas de faturação, ao departamento ou às contas de inscrição do Contrato Enterprise (EA) através das APIs REST do Azure. 

## <a name="individual-account-billing"></a>Faturação de contas individuais

Para obter os detalhes de utilização das contas num departamento:

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

O parâmetro `{billingAccountId}` é obrigatório e deve conter o ID da conta.

Os seguintes cabeçalhos são obrigatórios: 

|Cabeçalho do pedido|Descrição|  
|--------------------|-----------------|  
|*Content-Type:*|Necessário. Definido como `application/json`.|  
|*Authorization:*|Necessário. Defina como uma chave de [API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)de `Bearer` válida. |  

Este exemplo mostra uma chamada síncrona que devolve detalhes relativos ao atual ciclo de faturação. Por motivos de desempenho, as chamadas síncronas devolvem informações relativas ao último mês.  Também pode chamar a [API de forma assíncrona](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) para devolver dados durante 36 meses.


## <a name="response"></a>Resposta  

O código de estado 200 (OK) é devolvido para uma resposta bem-sucedida, que contém uma lista dos custos detalhados da conta.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/BillingAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        ...
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        ...
      }
    }
  ]
}
```  

Este exemplo é abreviado; veja [Get usage detail for a billing account](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslist-legacy) (Obter detalhes de utilização de uma conta) para obter uma descrição completa de cada campo de resposta e como resolver erros.

## <a name="department-billing"></a>Faturação do departamento 

Obtenha detalhes de utilização agregados de todas as contas num departamento. 

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

O parâmetro `{departmentId}` é obrigatório e deve conter o ID do departamento na conta de inscrição.

Os seguintes cabeçalhos são obrigatórios: 

|Cabeçalho do pedido|Descrição|  
|--------------------|-----------------|  
|*Content-Type:*|Necessário. Definido como `application/json`.|  
|*Authorization:*|Necessário. Defina como uma chave de [API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)de `Bearer` válida. |  

Este exemplo mostra uma chamada síncrona que devolve detalhes relativos ao atual ciclo de faturação. Por motivos de desempenho, as chamadas síncronas devolvem informações relativas ao último mês.  Também pode chamar a [API de forma assíncrona](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) para devolver dados durante 36 meses.

### <a name="response"></a>Resposta  

O código de estado 200 (OK) é devolvido para uma resposta bem-sucedida, que contém uma lista de informações de utilização detalhadas e dos custos para um determinado período de faturação e ID de fatura do departamento.


O exemplo a seguir mostra a saída da API REST para o departamento `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```  

Este exemplo é abreviado; veja [Get usage detail for a department](/rest/api/consumption/usagedetails/list#departmentusagedetailslist-legacy) (Obter detalhes de utilização de um departamento) para obter uma descrição completa de cada campo de resposta e como resolver erros.

## <a name="enrollment-account-billing"></a>Faturação da conta de inscrição

Obtenha os detalhes de utilização agregados da conta de inscrição.

```http
GET GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

O parâmetro `{enrollmentAccountId}` é obrigatório e deve conter o ID da conta de inscrição.

Os seguintes cabeçalhos são obrigatórios: 

|Cabeçalho do pedido|Descrição|  
|--------------------|-----------------|  
|*Content-Type:*|Necessário. Definido como `application/json`.|  
|*Authorization:*|Necessário. Defina como uma chave de [API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)de `Bearer` válida. |  

Este exemplo mostra uma chamada síncrona que devolve detalhes relativos ao atual ciclo de faturação. Por motivos de desempenho, as chamadas síncronas devolvem informações relativas ao último mês.  Também pode chamar a [API de forma assíncrona](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) para devolver dados durante 36 meses.

### <a name="response"></a>Resposta  

O código de estado 200 (OK) é devolvido para uma resposta bem-sucedida, que contém uma lista de informações de utilização detalhadas e dos custos para um determinado período de faturação e ID de fatura do departamento.

O exemplo a seguir mostra a saída da API REST para a inscrição Enterprise `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        ....
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
``` 

Este exemplo é abreviado; veja [Get usage detail for an enrollment account](/rest/api/consumption/usagedetails/list#enrollmentaccountusagedetailslist-legacy) (Obter detalhes de utilização de uma conta de inscrição) para obter uma descrição completa de cada campo de resposta e como resolver erros.

## <a name="next-steps"></a>Passos seguintes 
- Veja [Descrição geral dos relatórios Enterprise](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Consulte [Enterprise Billing REST API](https://docs.microsoft.com/rest/api/billing/) (API REST de Faturação do Enterprise)   
- [Introdução à API REST do Azure](https://docs.microsoft.com/rest/api/azure/)   
