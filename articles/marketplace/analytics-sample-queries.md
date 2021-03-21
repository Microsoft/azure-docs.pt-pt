---
title: Consultas de amostra para análise programática
description: Utilize estas consultas de amostra para aceder programáticamente aos dados de análise de marketplace comercial da Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 7d788448fb3f8a849f79e43fcb0737898f4c9e15
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584014"
---
# <a name="sample-queries-for-programmatic-analytics"></a>Consultas de amostra para análise programática

Este artigo fornece consultas de amostra para os relatórios do mercado comercial da Microsoft, Encomendas, Utilização e Cliente. Pode utilizar estas consultas ligando para o ponto final da [API de consulta](analytics-programmatic-access.md#create-report-query-api) de relatório create. Se necessário, a chamada de API [de Consulta de Relatório de Criação](analytics-programmatic-access.md#create-report-query-api) pode ser modificada para adicionar mais colunas, ajustar o período de cálculo e adicionar condições de filtro. Os períodos de tempo suportados são de seis meses (6M), 12 meses (12M) e período de tempo personalizado.

Para obter detalhes sobre os nomes, atributos e descrições das colunas, consulte as seguintes tabelas:

- [Tabela de detalhes do cliente](customer-dashboard.md#customer-details-table)
- [Tabela de detalhes de encomendas](orders-dashboard.md#orders-details-table)
- [Tabela de detalhes de utilização](usage-dashboard.md#usage-details-table)

## <a name="customers-report-queries"></a>Clientes reportam consultas

Estas consultas de amostra aplicam-se ao relatório dos Clientes.

| **Descrição de consulta** | **Consulta de amostras** |
| --- | --- |
| Clientes ativos do parceiro até à data escolhida | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 1` |
| Clientes agitados do parceiro até à data escolhida | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 0` |
| Lista de novos clientes de uma geografia específica nos últimos seis meses | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE DateAcquired <= ‘2020-06-30’ AND CustomerCountryRegion = ‘United States’` |
|||

## <a name="usage-report-queries"></a>Consultas de relatório de utilização

Estas consultas de amostra aplicam-se ao relatório de utilização.

| **Descrição de consulta** | **Consulta de amostras** |
| --- | --- |
| Máquina Virtual (VM) normalizou o uso para o tipo de licença de mercado "Billed through Azure" para os últimos 6M | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| VM Raw uso para tipo de licença de mercado "Billed through Azure" para os últimos 12M | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_1_YEAR` |
| VM Uso normalizado para tipo de licença de marketplace "Bring Your Own License" para os últimos 6M | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| VM Uso bruto para tipo de licença de mercado "Bring Your Own License" para os últimos 6M | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Com base na Data de Utilização, o uso total normalizado diário e "Encargos Alargados Estimados (PC/CC)" para planos pagos para o último mês | `SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST_MONTH` |
| Com base na Data de Utilização, uso total diário de brutos e "Encargos Alargados Estimados (PC/CC)" para planos pagos para o último mês | `SELECT UsageDate, RawUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST\_MONTH` |
| Para um nome de oferta específico, vm normalizado uso para "Billed through Azure" Marketplace License type para os últimos 6M | `SELECT OfferName, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| Para um Nome de Oferta específico, o uso medido para os últimos 6M | `SELECT OfferName, MeteredUsage FROM ISVUsage WHERE OfferName = ‘Example Offer Name’ AND OfferType IN (‘SaaS’, ‘Azure Applications’) TIMESPAN LAST_6_MONTHS` |
|||

## <a name="orders-report-queries"></a>Encomendas reportam consultas

Estas consultas de amostra aplicam-se ao relatório encomendas.

| **Descrição de consulta** | **Consulta de amostras** |
| --- | --- |
| Relatório de encomendas para Azure License Type como "Enterprise" para os últimos 6M | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Enterprise’ TIMESPAN LAST\_6\_MONTHS` |
| Relatório de encomendas para Azure License Type como "Pay as You Go" para os últimos 6M | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Pay as You Go’ TIMESPAN LAST_6_MONTHS` |
| Relatório de encomendas para nome de oferta específico para o último 6M | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| Relatório de encomendas para encomendas ativas para os últimos 6M | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Active’ TIMESPAN LAST_6_MONTHS` |
| Relatório de encomendas para encomendas canceladas para os últimos 6M | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Cancelled’ TIMESPAN LAST_6_MONTHS` |
|||

## <a name="next-steps"></a>Passos seguintes

- [APIs para aceder a dados de análise de marketplace comercial](analytics-available-apis.md)
