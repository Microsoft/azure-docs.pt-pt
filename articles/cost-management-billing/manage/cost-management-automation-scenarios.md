---
title: Cenários de automatização para faturação e gestão de custos do Azure
description: Saiba como os cenários comuns de faturação e gestão de custos são mapeados para diferentes APIs.
author: bandersmsft
ms.reviewer: adwise
tags: billing
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 01/26/2021
ms.author: banders
ms.openlocfilehash: 12c13b8a65296fb0ee74e0ee0449b604facf2f48
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051266"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Cenários de automatização para faturação e gestão de custos

Este artigo lista cenários comuns de automatização para faturação e gestão de custos do Azure. Mapeia estes cenários para APIs que pode utilizar. Em cada mapeamento de cenário para API, pode encontrar um resumo das APIs e da funcionalidade oferecida pelas mesmas.

## <a name="common-scenarios"></a>Cenários comuns

Pode utilizar as APIs de faturação e gestão de custos em vários cenários para responder a questões relativas aos custos e à utilização. Eis uma descrição de cenários comuns:

- **Reconciliação de fatura**: A Microsoft faturou-me o montante correto?  De quanto é minha fatura e posso calculá-la sozinho?

- **Encargos cruzados**: Agora que sei quanto está a ser faturado, quem dentro da minha organização terá de pagar?

- **Otimização de custos**: Eu sei o montante que me foi faturado. Como posso aproveitar ao máximo o dinheiro que estou a gastar no Azure?

- **Controlo de custos**: Quero ver quanto estou a gastar e a utilizar o Azure ao longo do tempo. Quais são as tendências? Como posso melhorar?

- **Gastos de Azure durante o mês**: Quanto é que os meus gastos do mês atual são até à data? É necessário fazer alterações nos meus gastos e/ou na utilização do Azure? Em que alturas do mês é maior o consumo do Azure?

- **Alertas**: Como posso configurar os alertas de consumo baseado em recursos ou os alertas baseados em monetários?

## <a name="scenario-to-api-mapping"></a>Mapeamento de cenário para API

|         API        | Reconciliação de fatura    | Encargos cruzados    | Otimização de custos    | Controlo de custos    | Gastos no meio do mês    | Alertas    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Orçamentos                     |                           |                  |           X          |                  |                    |     X     |
| Encargos do Marketplace                |             X             |         X        |           X          |         X        |          X         |     X     |
| Folha de Preços                 |             X             |         X        |           X          |         X        |          X         |           |
| Recomendações da Reserva |                           |                  |           X          |                  |                    |           |
| Detalhes da Reserva         |                           |                  |           X          |         X        |                    |           |
| Resumos da Reserva       |                           |                  |           X          |         X        |                    |           |
| Detalhes de Utilização               |             X             |         X        |           X          |         X        |          X         |     X     |
| Períodos de Faturação             |             X             |         X        |           X          |         X        |                    |           |
| Faturas                    |             X             |         X        |           X          |         X        |                    |           |
| Preços de retalho Azure                    |             X             |                  |           X          |         X        |                    |           |


> [!NOTE]
> O mapeamento de cenário para API não inclui as APIs de Consumo do Enterprise. Sempre que possível, utilize as APIs de Consumo geral nos novos cenários de desenvolvimento.

## <a name="api-summaries"></a>Resumos das APIs

### <a name="consumption"></a>Consumo
Os clientes Web Direct e Enterprise podem utilizar todas as APIs que se seguem, exceto quando for indicado:

-    [API de Orçamentos](/rest/api/consumption/budgets) (*Apenas Clientes Enterprise*): Crie orçamentos de custo ou de utilização para recursos, grupos de recursos ou medidores de faturação. Quando criar orçamentos, poderá configurar alertas para notificá-lo de quando excedeu os limiares de orçamento definidos. Também pode configurar ações para que ocorram quando os valores do orçamento tiverem sido atingidos.

-    [API de Encargos do Marketplace](/rest/api/consumption/marketplaces): Obtenha dados de utilização e de custos em todos os recursos do Azure Marketplace (ofertas de parceiros do Azure). Pode utilizar estes dados para somar os custos em todos os recursos do Marketplace ou para investigar os custos/utilização em recursos específicos.

-    [API de Folha de Preços](/rest/api/consumption/pricesheet) (*apenas clientes Enterprise)* : Obtenha preços personalizados para todos os medidores. As empresas podem utilizar estes dados em combinação com detalhes de utilização e informações de utilização do Marketplace para calcular os custos com os dados de utilização e do Marketplace.

-    [API de Recomendações da Reserva](/rest/api/consumption/reservationrecommendations): Obtenha recomendações para comprar Instâncias de VM Reservadas. As recomendações ajudam-no a analisar a poupança de custos e os montantes de compra esperados. Para obter mais informações, veja [APIs para automatização de reservas do Azure](../reservations/reservation-apis.md).

-    [API de Detalhes da Reserva](/rest/api/consumption/reservationsdetails): Veja as informações sobre as reservas de VM compradas anteriormente, como por exemplo, quanto do consumo é reservado por comparação com quanto é utilizado. Pode ver os dados de acordo com os detalhes ao nível da VM. Para obter mais informações, veja [APIs para automatização de reservas do Azure](../reservations/reservation-apis.md).

-    [API de Resumos da Reserva](/rest/api/consumption/reservationssummaries): Veja as informações agregadas sobre reservas de VM compradas pela sua organização, por exemplo, quanto do consumo é reservado por comparação com quanto é utilizado na agregação. Para obter mais informações, veja [APIs para automatização de reservas do Azure](../reservations/reservation-apis.md).

-    [API Detalhes de Utilização](/rest/api/consumption/usagedetails): Obtenha informações de custos e utilização em todos os recursos do Azure da Microsoft. As informações estão na forma de registos de detalhes de utilização, que atualmente são emitidos uma vez por dia para cada medidor. Pode utilizar as informações para somar os custos em todos os recursos ou para investigar os custos/utilização em recursos específicos.

-    [Preços de venda a retalho Azure](/rest/api/cost-management/retail-prices/azure-retail-prices): Obtenha preços de contador com preços de pagamento. Em seguida, pode utilizar as informações devolvidas com as informações de utilização dos recursos para calcular manualmente a fatura esperada.

### <a name="billing"></a>Faturação
-    [API de Períodos de Faturação](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Determine um período de faturação a analisar, juntamente com as IDs de fatura para esse período. Pode utilizar IDs de fatura com a API de Faturas.

-    [API de Faturas](/rest/api/billing/2019-10-01-preview/invoices): Obtenha a URL de transferência de uma fatura para um período de faturação em formato PDF.

### <a name="enterprise-consumption"></a>Consumo do Enterprise
As seguintes APIs são apenas para o Enterprise:

-    [API de Resumo de Saldo](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): Obtenha um resumo mensal das informações sobre os saldos, as novas compras, os encargos dos serviços do Azure Marketplace, os ajustes e os encargos de utilização excedida. Pode obter estas informações para o período de faturação atual ou para qualquer período no passado. As empresas podem utilizar estes dados para comparar com os encargos de resumo calculados manualmente. Esta API não fornece informações específicas dos recursos ou uma vista agregada dos custos.

-    [API Detalhes de Utilização](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): Obtenha informações sobre a utilização do Azure (das ofertas da Microsoft) para o mês atual, um período de faturação específico ou um período de data personalizado. As empresas podem utilizar estes dados para calcular manualmente as faturas com base na tarifa e no consumo. As empresas também podem utilizar informações de departamento/organização para atribuir custos de atributos entre organizações. Os dados fornecem uma vista com utilização/custo específica para o recurso.

-    [API de Encargo de Loja do Marketplace](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): Obtenha informações sobre a utilização do Azure (das ofertas do parceiro) para o mês atual, um período de faturação específico ou um período de data personalizado. As empresas podem utilizar estes dados para calcular manualmente as faturas com base na tarifa e no consumo. As empresas também podem utilizar informações de departamento/organização para atribuir custos de atributos entre organizações. Esta API fornece uma vista específica para o recurso com utilização/custo.

-    [API de Folha de Preços](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): Obtenha a tarifa aplicável de cada medidor para a inscrição e o período de faturação indicados. Pode utilizar estas informações de tarifa em combinação com os detalhes de utilização e as informações de utilização do Marketplace para calcular manualmente a fatura esperada.

-    [API de Períodos de Faturação](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Obtenha uma lista de períodos de faturação. A API também fornece uma propriedade que aponta para a rota da API para os quatro conjuntos de dados da API Enterprise que pertencem ao período de faturação: BalanceSummary, UsageDetails, Encargos do Marketplace e PriceSheet.

-    [API de Recomendações de Instância Reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): Examine 7 dias, 30 dias ou 60 dias de utilização da máquina virtual e obtenha recomendações de Compra Únicas e Partilhadas. Pode utilizar esta API para analisar a poupança de custos esperada e os valores de compra recomendados. Para obter mais informações, veja [APIs para automatização de reserva do Azure](../reservations/reservation-apis.md).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Qual é a diferença entre as APIs de Relatórios do Enterprise e as APIs de Consumo? Quando devo utilizar cada uma delas?
Estas APIs têm um conjunto de funcionalidades semelhante e podem responder ao mesmo conjunto amplo de perguntas no espaço da faturação e gestão de custos. No entanto, visam públicos diferentes:

- As APIs de Relatórios Enterprise estão disponíveis para os clientes que assinaram um Contrato Enterprise com a Microsoft, que concede acesso a pagamentos negociados do Pré-pagamento do Azure (que antes se chamava alocação monetária) e a preços personalizados. As APIs exigem uma chave que pode obter no [Enterprise Portal.](https://ea.azure.com) Para uma descrição destas APIs, veja [Descrição geral das APIs de Relatórios para clientes Enterprise](enterprise-api.md).

- As APIs de Consumo estão disponíveis para todos os clientes, com algumas exceções. Para obter mais informações, veja [Descrição geral da API de consumo do Azure](consumption-api-overview.md) e a [ referência da API de Consumo do Azure](/rest/api/consumption/). Recomendamos as APIs fornecidas como a solução para os cenários de desenvolvimento mais recentes.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Qual é a diferença entre a API de Fatura e a API de Detalhes de Utilização?
Estas APIs fornecem uma vista diferente dos mesmos dados:

- A [API de Fatura](/rest/api/billing/2019-10-01-preview/invoices) é apenas para clientes do Web Direct. Fornece uma acumulação mensal da sua fatura com base nos encargos agregados para cada tipo de medidor.

- A [API de Detalhes de Utilização](/rest/api/consumption/usagedetails) fornece uma vista granular dos registos de utilização/custo para cada dia. Tanto os Clientes Enterprise como os clientes Web Direct podem utilizá-la.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Qual é a diferença entre a API da Folha de Preços e a API do RateCard?
Estas APIs fornecem conjuntos de dados semelhantes, mas têm públicos diferentes:

- A [API da Folha de Preços](/rest/api/consumption/pricesheet) fornece os preços personalizados que foram negociados para um cliente Enterprise.

- A [Azure Retail Prices API](/rest/api/cost-management/retail-prices/azure-retail-prices) fornece preços de pagamento virados para o público que se aplica aos clientes Web Direct.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre a utilização de APIs REST para todos os serviços Azure, consulte [a visão geral dos preços de venda a retalho do Azure](/rest/api/cost-management/retail-prices/azure-retail-prices).

- Para comparar a sua fatura com o ficheiro de utilização diária detalhada e os relatórios de gestão de custos no portal do Azure, veja [Compreender a sua fatura para o Microsoft Azure](../understand/review-individual-bill.md).

- Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).