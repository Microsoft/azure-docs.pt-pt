---
title: Cenários de automação para a gestão de custos e de faturação do Azure | Documentos da Microsoft
description: Saiba como comuns faturação e são de cenários de gestão de custos APIs mapeadas para diferentes.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: 204b15b2-6667-4b6c-8ea4-f32c06f287fd
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/13/2018
ms.author: erikre
ms.openlocfilehash: 8b42361e7b94bf017dde4eaec882f9fed4d42ed6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57862000"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Cenários de automação para a gestão de faturação e de custo

Este artigo apresenta uma lista de cenários comuns para a gestão de custos e de faturação do Azure. Mapeia estes cenários para APIs que pode utilizar. Em cada mapeamento de API de cenário, pode encontrar um resumo das APIs e a funcionalidade que oferecem.

## <a name="common-scenarios"></a>Cenários comuns

Pode utilizar a faturação e APIs de gestão em vários cenários para responder a perguntas relacionadas com o custo e relacionadas com a utilização de custos. Aqui está uma descrição dos cenários comuns:

- **Reconciliação de nota fiscal**: Microsoft cobrar-me a quantidade certa?  O que é a minha fatura e pode eu calculo-lo eu mesmo?

- **Custos de várias**: Agora que sei quanto eu estou a ser cobrado, quem na minha organização tem de pagar?

- **Otimização de custos**: Eu sei quanto eu tenha sido cobrado. Como posso obter mais o dinheiro que estou a passar no Azure?

- **Controlo de custos**: Quero ver quanto eu estou gastos e a utilizar o Azure ao longo do tempo. Quais são as tendências? Como posso fazer melhor?

- **Gastos durante o mês do Azure**: Quanto o meu mês atual está gastando até à data? É necessário fazer qualquer alteração no meu gastos e/ou utilização do Azure? Quando durante o mês estou que consumindo Azure mais?

- **Alertas**: Como posso configurar consumo baseada em recursos ou monetário com base em alertas?

## <a name="scenario-to-api-mapping"></a>Mapeamento de API do cenário

|         API        | Reconciliação de nota fiscal    | Em vários custos    | Otimização de custos    | Controlo de custos    | Gastos midmonth    | Alertas    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Orçamentos                     |                           |                  |           X          |                  |                    |     X     |
| Encargos do Marketplace                |             X             |         X        |           X          |         X        |          X         |     X     |
| Folha de Preços                 |             X             |         X        |           X          |         X        |          X         |           |
| Recomendações da Reserva |                           |                  |           X          |                  |                    |           |
| Detalhes da Reserva         |                           |                  |           X          |         X        |                    |           |
| Resumos da Reserva       |                           |                  |           X          |         X        |                    |           |
| Detalhes de Utilização               |             X             |         X        |           X          |         X        |          X         |     X     |
| Períodos de faturação             |             X             |         X        |           X          |         X        |                    |           |
| Faturas                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Utilização não classificada               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> O mapeamento de API de cenário não inclui as APIs de consumo da empresa. Sempre que possível, use as APIs de consumo geral para novos cenários de desenvolvimento.

## <a name="api-summaries"></a>Resumos de API

### <a name="consumption"></a>Consumo
Os clientes Web Direct e Enterprise, podem utilizar todas as APIs seguintes, exceto onde indicado:

-   [API de orçamentos](https://docs.microsoft.com/rest/api/consumption/budgets) (*apenas a clientes empresariais*): Crie os orçamentos de custos ou utilização de recursos, grupos de recursos ou medidores de faturação. Quando criou, orçamentos, pode configurar alertas para notificar quando tive excedido definidos limites orçamentais. Também pode configurar ações para ocorrer quando atingiu quantidades de orçamento.

-   [API de encargos do Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces): Obter dados de utilização e custos em todos os recursos do Azure Marketplace (ofertas de parceiros do Azure). Pode utilizar estes dados para aumentar os custos em todos os recursos do Marketplace ou para investigar os custos/utilização em recursos específicos.

-   [API da folha de preços](https://docs.microsoft.com/rest/api/consumption/pricesheet) (*apenas a clientes empresariais*): Obtenha personalizados para todos os medidores de preços. As empresas podem utilizar estes dados em combinação com detalhes de utilização e informações de utilização do marketplace para calcular os custos, utilizando dados de utilização e o marketplace. 

-   [API de recomendações de reserva](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations): Obtenha recomendações para a compra de instâncias de VM reservadas. Recomendações de ajudar a analisar as economias de custo previsto e quantidades de compra. Para obter mais informações, consulte [APIs para a automatização do Azure reserva](billing-reservation-apis.md).

-   [API de detalhes de reserva](https://docs.microsoft.com/rest/api/consumption/reservationsdetails): Ver informações sobre as reservas anteriormente adquiridas da VM, como a quantidade de consumo está reservado versus quantidade utilizada. Pode ver dados per-VM-nível de detalhe. Para obter mais informações, consulte [APIs para a automatização do Azure reserva](billing-reservation-apis.md).

-   [Resumos de reserva API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries): Consulte informações agregadas sobre as reservas de VM que comprou a sua organização, como a quantidade de consumo está reservado versus quantidade utilizada no agregado. Para obter mais informações, consulte [APIs para a automatização do Azure reserva](billing-reservation-apis.md).

-   [API de detalhes de utilização](https://docs.microsoft.com/rest/api/consumption/usagedetails): Obtenha informações de utilização e custos em todos os recursos do Azure da Microsoft. Informações estão sob a forma de registos de detalhes de utilização, que atualmente são emitidos uma vez por medidor por dia. Pode utilizar as informações para aumentar os custos em todos os recursos ou investigar os custos/utilização em recursos específicos.

-   [API de RateCard](https://msdn.microsoft.com/library/azure/mt219005.aspx): Obter as taxas de medidor se for um cliente do Web Direct. Em seguida, pode utilizar as informações retornadas com as suas informações de utilização de recursos para calcular manualmente a fatura. 

-   [Não classificados de API de utilização](https://msdn.microsoft.com/library/azure/mt219003.aspx): Obtenha informações de utilização não processados antes do Azure faz qualquer medição/cobrar.

### <a name="billing"></a>Faturação
-   [Períodos de faturação API](https://docs.microsoft.com/rest/api/billing/billingperiods): Determine um período de faturação para analisar, juntamente com a nota fiscal IDs durante esse período. Pode usar o IDs de fatura com a API de notas fiscais.

-   [Notas fiscais de API](https://docs.microsoft.com/rest/api/billing/invoices): Obtenha o URL de transferência para uma nota fiscal para um período de faturação no formato PDF.

### <a name="enterprise-consumption"></a>Consumo de Enterprise
As APIs seguintes destinam-se apenas ao Enterprise:

-   [Resumo da API de equilibrar](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): Obtenha um mensal resumo de informações sobre os saldos, novas compras, os encargos de serviços do Azure Marketplace, ajustes e custos de utilização excedida. Pode obter estas informações para o período de faturação atual ou qualquer período no passado. As empresas podem utilizar estes dados para comparar com custos de resumos manualmente calculados. Esta API não fornece informações específicas de recurso ou uma visualização agregada dos custos.

-   [API de detalhes de utilização](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): Obtenha informações sobre a utilização do Azure (ofertas da Microsoft) para o mês atual, de um determinado período de faturação ou de um período de datas personalizadas. As empresas podem utilizar estes dados para calcular manualmente com base numa taxa de e no consumo de faturas. As empresas também podem utilizar as informações de organização/departamento aos custos de atributo entre organizações. Os dados fornecem uma exibição de recursos específicos de utilização/custo.

-   [Cobrança de Marketplace Store API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): Obtenha informações sobre a utilização do Azure (de ofertas de parceiros) para o mês atual, de um determinado período de faturação ou de um período de datas personalizadas. As empresas podem utilizar estes dados para calcular manualmente com base numa taxa de e no consumo de faturas. As empresas também podem utilizar as informações de organização/departamento aos custos de atributo entre organizações. Esta API fornece uma exibição de recursos específicos de utilização/custo.

-   [API da folha de preços](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): Obter a taxa aplicável para cada contador para a inscrição de determinado e o período de faturação. Pode utilizar essas informações de taxa em combinação com detalhes de utilização e a informações de utilização do marketplace para calcular manualmente a fatura.

-   [Períodos de faturação API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Obter uma lista de períodos de faturação. A API fornece também uma propriedade que aponta para a rota de API para os quatro conjuntos de dados empresariais de API que dizem respeito ao período de faturação: BalanceSummary, UsageDetails, encargos do Marketplace e folha de preços.

-   [Reservado API de recomendações de instância](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): Examinar a 7 dias, 30 dias, ou de 60 dias de utilização da máquina virtual e obtenha recomendações de compra partilhados e únicas. Pode utilizar esta API para analisar a economia de custo previsto e recomendado quantidades de compra. Para obter mais informações, consulte [APIs para a automatização do Azure reserva](billing-reservation-apis.md).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>O que é a diferença entre as APIs de relatórios empresariais e as APIs de consumo? Quando devo utilizar cada?
Essas APIs têm um conjunto de funcionalidades semelhantes e podem responder o mesmo conjunto amplo de perguntas no espaço de gestão de faturação e de custo. Mas se destinam a públicos diferentes: 

- APIs de relatórios empresariais estão disponíveis para os clientes que iniciaram um Enterprise Agreement com a Microsoft que lhe concede acesso a preços personalizados e de compromissos monetários negociados. As APIs requerem uma chave que pode obter a partir da [Enterprise Portal](https://ea.azure.com). Para obter uma descrição destas APIs, veja [descrição geral de APIs de relatórios para os clientes empresariais](billing-enterprise-api.md).

- Consumo de APIs estão disponíveis para todos os clientes, com algumas exceções. Para obter mais informações, consulte [descrição geral da API de consumo do Azure](billing-consumption-api-overview.md) e o [referência da API de consumo do Azure](https://docs.microsoft.com/rest/api/consumption/). Recomendamos que as APIs fornecidas como a solução para os cenários de desenvolvimento mais recentes. 

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>O que é a diferença entre a API de detalhes de utilização e a API de utilização?
Estas APIs fornecem dados fundamentalmente diferentes:

- O [API de detalhes de utilização](https://docs.microsoft.com/rest/api/consumption/usagedetails) fornece Azure informações de utilização e custo por instância de medidor. Os dados fornecidos já passa o custo de medição de sistema no Azure e tinha Custo aplicado a ele, juntamente com outras possíveis alterações:

   - Alterações para levar em conta a utilização de compromissos monetários pré-paga
   - Alterações para levar em conta as discrepâncias de utilização detetadas pelo Azure

- O [API de utilização](https://msdn.microsoft.com/library/Mt219003.aspx) fornece informações de utilização do Azure não processados antes que passa pelo custo de medição de sistema no Azure. Estes dados podem não ter qualquer correlação com a quantidade de utilização ou custo que é vista depois do Azure cobra o sistema de medição.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>O que é a diferença entre a API de nota fiscal e a API de detalhes de utilização?
Essas APIs proporcionam uma exibição diferente dos mesmos dados:

- O [API de nota fiscal](https://docs.microsoft.com/rest/api/billing/invoices) destina-se somente a clientes Web Direct. Ele fornece um rollup mensal da fatura com base nos custos de agregação para cada tipo de medidor. 

- O [API de detalhes de utilização](https://docs.microsoft.com/rest/api/consumption/usagedetails) fornece uma vista granular dos registos de utilização/custo para cada dia. Os clientes empresariais e de Web Direct podem utilizá-lo.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>O que é a diferença entre a API da folha de preços e a API de RateCard?
Estas APIs fornecem semelhante conjuntos de dados, mas têm a públicos diferentes:

- O [API da folha de preços](https://docs.microsoft.com/rest/api/consumption/pricesheet) fornece o personalizado preços que foi negociado para um cliente empresarial.

- O [RateCard API](https://msdn.microsoft.com/library/mt219005.aspx) fornece o destinado ao público preços que se aplica aos clientes Web Direct.

## <a name="next-steps"></a>Passos Seguintes

- Para obter informações sobre como utilizar as APIs do Azure para obter informações sobre sua utilização do Azure programaticamente, consulte [descrição geral da API de consumo do Azure](billing-consumption-api-overview.md) e [descrição geral da API de faturação do Azure](billing-usage-rate-card-overview.md).

- Para comparar a fatura com o ficheiro de utilização diária detalhadas e os relatórios de gestão de custos no portal do Azure, veja [compreender a sua fatura do Microsoft Azure](billing-understand-your-bill.md).

- Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
