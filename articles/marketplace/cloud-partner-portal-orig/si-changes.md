---
title: Notas de versão de informações do vendedor
description: Fornece informações sobre as alterações para a funcionalidade de informações de vendedor.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: pabutler
ms.openlocfilehash: c6e9e4fe672c7e171ed4b1cd60655f9e71a562e6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943113"
---
# <a name="seller-insights-release-notes"></a>Notas de versão de informações do vendedor 

(Data de lançamento: 1 de Março de 2019)

Este artigo fornece informações sobre as alterações à funcionalidade do vendedor Insights na [Cloud Partner Portal](https://cloudpartner.azure.com/#insights).

## <a name="release-highlights-for-march-1-2019"></a>Destaques da versão de 1 de Março de 2019

* *Tendência de cliente* adicionado ao resumo
* *Principais cinco clientes* no resumo refletir todas as subscrições do Azure tem um cliente
* *Normalizados tendência de utilização e o Active Directory de ordens de tendência* no resumo movido para o nó *ordens mensal rapidamente*
* *Relatório de reconciliação de dividendos* atualizado
* *Principais cinco clientes* sobre dividendos refletir todas as subscrições do Azure tem um cliente
* *Relatório de utilização* atualizado com o ID de cliente
* *No escritório de cliente* em ordens de & utilização reflete todas as subscrições do Azure tem um cliente


(Data de lançamento: 28 de Julho de 2018)

## <a name="release-highlights-for-july-28-2018"></a>Destaques de versão do dia 28 de Julho de 2018


-   *Os preços de estimado* fornecer uma vista de custos de cliente de moeda implicações de conversão.
-   *Previstos dividendos* fornecer uma vista anterior sobre dividendos potenciais.
-  *Identificadores de referência de utilização* proporcionar fidelidade de dados entre a utilização do cliente e os pedidos com dividendos
-   *Utilização num detalhamento diário* fornece mais granularidade e melhores informações sobre a utilização do cliente.


### <a name="changes-to-data-structure-and-taxonomy"></a>Alterações à estrutura de dados e a taxonomia

A tabela seguinte lista as métricas que foram adicionadas ou alteradas consideravelmente – com esta versão. 

| **Novo termo**                   |    **Definição**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| Preço (CC)                     | Preço de uma unidade de utilização para um determinado SKU (na moeda do cliente).       |
| Custo estimado de expandida (CC) | Estimado custos expandido para a quantidade de unidades de utilização para um determinado SKU (na moeda do cliente). Este valor pode não ser exato devido a erros de arredondamento ou o truncamento.   |
| Publicador de moeda (PC)        | Moeda preferida pelo fabricante para dividendos.                               |
| Preço estimado (PC)           | Preço estimado para uma unidade de utilização para um determinado SKU com base na conversão de câmbio sobre a utilização de data é calculado (na moeda do Editor). Este valor pode não ser exato devido a erros de arredondamento ou o truncamento.   |
| Custo estimado de expandida (PC) | Cobrança de expandida prevista para a quantidade de unidades de utilização para um determinado SKU com base na conversão de câmbio sobre a utilização de data é calculada (na moeda do Editor). Este valor pode não ser exato devido a erros de arredondamento ou o truncamento. |
| Dividendos estimado (PC)          | Pagamento estimado para a quantidade de unidades de utilização para um determinado SKU com base na conversão de câmbio na data que da utilização é calculada (na moeda do Editor). Este valor pode não ser exato devido a erros de arredondamento ou o truncamento.   |
| Referência de utilização                | O identificador para um ou mais dias de utilização do cliente para um determinado SKU associado com uma entrada no relatório dividendos. |
|  |  |
