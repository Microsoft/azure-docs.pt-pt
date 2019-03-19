---
title: Inscrição de enterprise do Azure de revisão dados com a REST API de faturação | Documentos da Microsoft
description: Saiba como utilizar APIs REST do Azure para rever as informações de faturação de inscrição de enterprise.
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
ms.openlocfilehash: 9a0b536426ab024d5af7b257e44a2d5e20f14def
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57898708"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Reveja a faturação de inscrição de enterprise com as APIs REST

Ajuda de APIs de relatórios do Azure, reveja e gira os seus custos do Azure.

Neste artigo, vai aprender a obter as informações de faturas associadas com contas de faturas, departamento ou contas de inscrição do enterprise agreement (EA) com as APIs de REST do Azure. 

## <a name="individual-account-billing"></a>Faturação da conta individual

Para obter detalhes de utilização de contas num departamento:

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

O `{billingAccountId}` parâmetro é obrigatório e deve conter o ID da conta.

Os seguintes cabeçalhos são necessários: 

|Cabeçalho do pedido|Descrição|  
|--------------------|-----------------|  
|*Content-Type:*|Necessário. Definido como `application/json`.|  
|*Autorização:*|Necessário. Definido como válido `Bearer` [chave de API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Este exemplo mostra uma chamada síncrona que devolve detalhes para o ciclo de faturação atual. Por motivos de desempenho, as chamadas síncronas retornam informações para o mês passado.  Também é possível chamar o [API de forma assíncrona](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) para retornar dados de 36 meses.


## <a name="response"></a>Resposta  

Código de estado 200 (OK) é devolvido para uma resposta com êxito, que contém uma lista dos custos detalhadas para a conta.

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

Neste exemplo é abreviado; ver [obter detalhes de utilização para uma conta de cobrança](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslist) para obter uma descrição completa de cada campo de resposta e o tratamento de erros.

## <a name="department-billing"></a>Faturação do departamento 

Obtenha os detalhes de utilização agregados para todas as contas num departamento. 

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

O `{departmentId}` parâmetro é obrigatório e deve conter o ID para o departamento na conta de inscrição.

Os seguintes cabeçalhos são necessários: 

|Cabeçalho do pedido|Descrição|  
|--------------------|-----------------|  
|*Content-Type:*|Necessário. Definido como `application/json`.|  
|*Autorização:*|Necessário. Definido como válido `Bearer` [chave de API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Este exemplo mostra uma chamada síncrona que devolve detalhes para o ciclo de faturação atual. Por motivos de desempenho, as chamadas síncronas retornam informações para o mês passado.  Também é possível chamar o [API de forma assíncrona](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) para retornar dados de 36 meses.

### <a name="response"></a>Resposta  

Código de estado 200 (OK) é devolvido para uma resposta com êxito, que contém uma lista de informações de utilização detalhadas e os custos para uma faturação período e a nota fiscal ID fornecido para o departamento.


O exemplo seguinte mostra a saída da API REST para o departamento `1234`.

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

Neste exemplo é abreviado; ver [obter detalhes de utilização para um departamento](/rest/api/consumption/usagedetails/list#departmentusagedetailslist) para obter uma descrição completa de cada campo de resposta e o tratamento de erros.

## <a name="enrollment-account-billing"></a>Faturação da conta de inscrição

Obtenha os detalhes de utilização agregados para a conta de inscrição.

```http
GET GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

O `{enrollmentAccountId}` parâmetro é obrigatório e deve conter o ID da conta de inscrição.

Os seguintes cabeçalhos são necessários: 

|Cabeçalho do pedido|Descrição|  
|--------------------|-----------------|  
|*Content-Type:*|Necessário. Definido como `application/json`.|  
|*Autorização:*|Necessário. Definido como válido `Bearer` [chave de API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Este exemplo mostra uma chamada síncrona que devolve detalhes para o ciclo de faturação atual. Por motivos de desempenho, as chamadas síncronas retornam informações para o mês passado.  Também é possível chamar o [API de forma assíncrona](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) para retornar dados de 36 meses.

### <a name="response"></a>Resposta  

Código de estado 200 (OK) é devolvido para uma resposta com êxito, que contém uma lista de informações de utilização detalhadas e os custos para uma faturação período e a nota fiscal ID fornecido para o departamento.

O exemplo seguinte mostra a saída da API REST para a inscrição de enterprise `1234`.

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

Neste exemplo é abreviado; ver [obter detalhes de utilização de uma conta de inscrição](/rest/api/consumption/usagedetails/list#enrollmentaccountusagedetailslist) para obter uma descrição completa de cada campo de resposta e o tratamento de erros.

## <a name="next-steps"></a>Passos Seguintes 
- Revisão [Enterprise descrição geral dos relatórios](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Investigar [Enterprise REST API de faturação](https://docs.microsoft.com/rest/api/billing/)   
- [Introdução à API REST do Azure](https://docs.microsoft.com/rest/api/azure/)   
