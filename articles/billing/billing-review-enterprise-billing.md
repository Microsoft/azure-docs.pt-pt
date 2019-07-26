---
title: Examinar dados de cobrança de registro do Azure Enterprise com a API REST | Microsoft Docs
description: Saiba como usar as APIs REST do Azure para examinar as informações de cobrança de registro corporativo.
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
ms.openlocfilehash: 25d9b48696dc2a83ea0ba77c1be2c7aad7627fff
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443153"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Examinar a cobrança de registro empresarial usando APIs REST

As APIs de relatórios do Azure ajudam você a revisar e gerenciar seus custos do Azure.

Neste artigo, você aprende a recuperar as informações de cobrança associadas às contas de cobrança, departamento ou conta de registro do EA (Enterprise Agreement) usando as APIs REST do Azure. 

## <a name="individual-account-billing"></a>Cobrança de conta individual

Para obter detalhes de uso de contas em um departamento:

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

O `{billingAccountId}` parâmetro é obrigatório e deve conter a ID da conta.

Os seguintes cabeçalhos são necessários: 

|Cabeçalho da solicitação|Descrição|  
|--------------------|-----------------|  
|*Tipo de conteúdo:*|Necessário. Defina como `application/json`.|  
|*Authorization:*|Necessário. Defina como uma chave `Bearer` de [API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)válida. |  

Este exemplo mostra uma chamada síncrona que retorna detalhes para o ciclo de cobrança atual. Por motivos de desempenho, as chamadas síncronas retornam informações para o último mês.  Você também pode chamar a [API de forma assíncrona](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) para retornar dados por 36 meses.


## <a name="response"></a>Resposta  

O código de status 200 (OK) é retornado para uma resposta bem-sucedida, que contém uma lista de custos detalhados para a conta.

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

Este exemplo é abreviado; consulte [obter detalhes de uso de uma conta de cobrança](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslist) para obter uma descrição completa de cada campo de resposta e tratamento de erros.

## <a name="department-billing"></a>Cobrança do departamento 

Obter detalhes de uso agregados para todas as contas em um departamento. 

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

O `{departmentId}` parâmetro é obrigatório e deve conter a ID do departamento na conta de registro.

Os seguintes cabeçalhos são necessários: 

|Cabeçalho da solicitação|Descrição|  
|--------------------|-----------------|  
|*Tipo de conteúdo:*|Necessário. Defina como `application/json`.|  
|*Authorization:*|Necessário. Defina como uma chave `Bearer` de [API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)válida. |  

Este exemplo mostra uma chamada síncrona que retorna detalhes para o ciclo de cobrança atual. Por motivos de desempenho, as chamadas síncronas retornam informações para o último mês.  Você também pode chamar a [API de forma assíncrona](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) para retornar dados por 36 meses.

### <a name="response"></a>Resposta  

O código de status 200 (OK) é retornado para uma resposta bem-sucedida, que contém uma lista de informações de uso e custos detalhados para um determinado período de cobrança e ID de fatura para o departamento.


O exemplo a seguir mostra a saída da API REST para o `1234`departamento.

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

Este exemplo é abreviado; consulte [obter detalhes de uso de um departamento](/rest/api/consumption/usagedetails/list#departmentusagedetailslist) para obter uma descrição completa de cada campo de resposta e tratamento de erros.

## <a name="enrollment-account-billing"></a>Cobrança da conta de registro

Obter detalhes de uso agregados para a conta de registro.

```http
GET GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

O `{enrollmentAccountId}` parâmetro é obrigatório e deve conter a ID da conta de registro.

Os seguintes cabeçalhos são necessários: 

|Cabeçalho da solicitação|Descrição|  
|--------------------|-----------------|  
|*Tipo de conteúdo:*|Necessário. Defina como `application/json`.|  
|*Authorization:*|Necessário. Defina como uma chave `Bearer` de [API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)válida. |  

Este exemplo mostra uma chamada síncrona que retorna detalhes para o ciclo de cobrança atual. Por motivos de desempenho, as chamadas síncronas retornam informações para o último mês.  Você também pode chamar a [API de forma assíncrona](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) para retornar dados por 36 meses.

### <a name="response"></a>Resposta  

O código de status 200 (OK) é retornado para uma resposta bem-sucedida, que contém uma lista de informações de uso e custos detalhados para um determinado período de cobrança e ID de fatura para o departamento.

O exemplo a seguir mostra a saída da API REST para registro `1234`Enterprise.

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

Este exemplo é abreviado; consulte [obter detalhes de uso de uma conta de registro](/rest/api/consumption/usagedetails/list#enrollmentaccountusagedetailslist) para obter uma descrição completa de cada campo de resposta e tratamento de erros.

## <a name="next-steps"></a>Passos Seguintes 
- Examinar [visão geral de relatórios empresariais](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Investigar [API REST de cobrança Enterprise](https://docs.microsoft.com/rest/api/billing/)   
- [Introdução à API REST do Azure](https://docs.microsoft.com/rest/api/azure/)   
