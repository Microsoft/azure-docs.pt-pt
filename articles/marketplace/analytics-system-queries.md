---
title: Lista de consultas de sistema
description: Saiba mais sobre as consultas do sistema que pode utilizar para obter dados de análise programáticas sobre as suas ofertas no mercado comercial da Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: f2b5f7eb559e349947f88067a3d2ea53d99b7cbf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584007"
---
# <a name="list-of-system-queries"></a>Lista de consultas de sistema

As seguintes consultas de sistema podem ser utilizadas na [API do Relatório criar](analytics-programmatic-access.md#create-report-api) diretamente com um `QueryId` . As consultas do sistema são como os relatórios de exportação no Partner Center por um período de cálculo de seis meses (6M).

Para obter mais informações sobre os nomes, atributos e descrição das colunas, consulte estes artigos:

- [Painel de clientes na análise do mercado comercial](customer-dashboard.md#customer-details-table)
- [Dashboard de encomendas na análise do mercado comercial](orders-dashboard.md#orders-details-table)
- [Painel de utilização na análise do mercado comercial](usage-dashboard.md#usage-details-table)
- [Dashboard de Informações do Marketplace na análise de mercados comerciais](insights-dashboard.md#marketplace-insights-details-table)

As secções seguintes fornecem consultas de relatórios para vários relatórios.

## <a name="customers-report-query"></a>Clientes reportam consulta

**Descrição do relatório**: Relatório de clientes para os últimos 6M

**ConsultaID:**`b9df4929-073f-4795-b0cb-a2c81b11e28d`

**Consulta do relatório:**

`SELECT MarketplaceSubscriptionId,DateAcquired,DateLost,ProviderName,ProviderEmail,FirstName,LastName,Email,CustomerCompanyName,CustomerCity,CustomerPostalCode,CustomerCommunicationCulture,CustomerCountryRegion,AzureLicenseType,PromotionalCustomers,CustomerState,CommerceRootCustomer,CustomerId,BillingAccountId,ID  FROM ISVCustomer TIMESPAN LAST_6_MONTHS`

## <a name="orders-report-query"></a>Pedidos reportam consulta

**Descrição do relatório**: Relatório de encomendas para os últimos 6M

**ConsultaID:**`fd0f299c-5a1c-4929-9f48-bfc6cc44355d`

**Consulta do relatório:**

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,Sku,CustomerCountry,IsPreviewSKU,OrderId,OrderQuantity,CloudInstanceName,IsNewCustomer,OrderStatus,OrderCancelDate,CustomerCompanyName,CustomerName,OrderPurchaseDate,OfferName,TrialEndDate,CustomerId,BillingAccountId FROM ISVOrder TIMESPAN LAST_6_MONTHS`

## <a name="usage-report-queries"></a>Consultas de relatório de utilização

**Descrição do relatório**: VM Relatório de utilização normalizado para os últimos 6M

**ConsultaID:**`2c6f384b-ad52-4aed-965f-32bfa09b3778`

**Consulta do relatório:**

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,NormalizedUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**Descrição do relatório**: VM Raw use report for the last 6M

**ConsultaID:**`3f19fb95-5bc4-4ee0-872e-cedd22578512`

**Consulta do relatório:**

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,RawUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**Descrição do relatório**: Relatório de utilização medido para os últimos 6M

**ConsultaID:**`f0c4927f-1f23-4c99-be4a-1371a5a9a086`

**Consulta do relatório:**

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,MeteredUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('SaaS', 'Azure Applications') TIMESPAN LAST_6_MONTHS`

## <a name="marketplace-insights-report-query"></a>Consulta de relatório de insights de mercado

**Descrição do relatório**: Relatório marketplace insights para os últimos 6M

**ConsultaID:**`6fd7624b-aa9f-42df-a61d-67d42fd00e92`

**Consulta do relatório:**

`Date,OfferName,ReferralDomain,CountryName,PageVisits,GetItNow,ContactMe,TestDrive,FreeTrial FROM ISVMarketplaceInsights TIMESPAN LAST_6_MONTHS`

## <a name="next-steps"></a>Passos seguintes

- [APIs para aceder a dados de análise de marketplace comercial](analytics-available-apis.md)
- [Aplicação de amostra para aceder a dados de análise de mercado comercial](analytics-sample-application.md)
