---
title: Notas de lançamento de insights do vendedor
description: Fornece informações sobre alterações à funcionalidade "Insights do Vendedor".
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: dsindona
ms.openlocfilehash: 1b88a8353c1cb9f97f18d720171f0827d7d85254
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80285406"
---
# <a name="seller-insights-release-notes"></a>Notas de lançamento do Seller Insights 

(Data de lançamento: 1 de março de 2019)

Este artigo fornece informações sobre alterações à funcionalidade "Insights do Vendedor" no Portal do [Parceiro da Nuvem](https://cloudpartner.azure.com/#insights).

## <a name="release-highlights-for-march-1-2019"></a>Destaques de lançamento para 1 de março de 2019

* *Tendência do Cliente* adicionada ao Resumo
* *Os cinco principais clientes* em resumo refletem todas as subscrições do Azure que um cliente tem
* *Tendência de utilização normalizada & tendência de encomendas ativas* em resumo movido sob ordens *mensais num glance*
* *Relatório de Reconciliação de Pagamentos* atualizado
* *Os cinco principais clientes* no Pagamento refletem todas as subscrições do Azure que um cliente tem
* *Relatório de Utilização* atualizado com ID do cliente
* *Posse de Cliente* em Encomendas & Utilização reflete todas as subscrições do Azure que um cliente tem


(Data de lançamento: 28 de julho de 2018)

## <a name="release-highlights-for-july-28-2018"></a>Destaques de lançamento para 28 de julho de 2018


-   *Os preços estimados* proporcionam uma visão dos encargos dos clientes com implicações de conversão cambial.
-   *Os pagamentos previstos* fornecem uma visão mais precoce sobre potenciais pagamentos.
-  *Os identificadores* de referência de utilização fornecem fidelidade de dados entre o uso do cliente e as encomendas com pagamentos
-   *O uso num grão diário* proporciona mais granularidade e melhores insights sobre o uso do cliente.


### <a name="changes-to-data-structure-and-taxonomy"></a>Alterações na estrutura de dados e taxonomia

A tabela seguinte enumera as métricas que foram adicionadas ou substancialmente alteradas com esta libertação. 

| **Novo Termo**                   |    **Definição**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| Preço (CC)                     | Preço de uma unidade de utilização para um dado SKU (na moeda do cliente).       |
| Encargos estimados alargados (CC) | Encargos estimados para a quantidade de unidades de utilização para um determinado SKU (na moeda do cliente). Este valor pode não ser exato devido a erros de arredondamento ou truncação.   |
| Moeda da Editora (PC)        | Moeda preferida pelo editor para pagamento.                               |
| Preço Estimado (PC)           | O preço estimado para uma unidade de utilização de um dado SKU com base na conversão cambial na data de utilização é calculado (na moeda do editor). Este valor pode não ser exato devido a erros de arredondamento ou truncação.   |
| Carga alargada estimada (PC) | Calculada a taxa alargada estimada para a quantidade de unidades de utilização de um dado SKU com base na conversão cambial da data de utilização (na moeda do editor). Este valor pode não ser exato devido a erros de arredondamento ou truncação. |
| Pagamento Estimado (PC)          | Pagamento estimado pela quantidade de unidades de utilização de um dado SKU com base na conversão cambial na data em que a utilização é calculada (na moeda do editor). Este valor pode não ser exato devido a erros de arredondamento ou truncação.   |
| Referência de utilização                | O identificador para um ou mais dias de utilização do cliente para um dado SKU associado a uma entrada no relatório de pagamento. |
|  |  |
