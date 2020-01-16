---
title: Cenários de automatização para faturação e gestão de custos do Azure | Microsoft Docs
description: Saiba como os cenários comuns de faturação e gestão de custos são mapeados para diferentes APIs.
services: billing
documentationcenter: ''
author: bandersmsft
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c9de7d5f7661e4083d3a2f5b53368616d0e6655a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75992819"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Cenários de automatização para faturação e gestão de custos

Este artigo lista cenários comuns de automatização para faturação e gestão de custos do Azure. Mapeia estes cenários para APIs que pode utilizar. Em cada mapeamento de cenário para API, pode encontrar um resumo das APIs e da funcionalidade oferecida pelas mesmas.

## <a name="common-scenarios"></a>Cenários comuns

Pode utilizar as APIs de faturação e gestão de custos em vários cenários para responder a questões relativas aos custos e à utilização. Eis uma descrição de cenários comuns:

- **Reconciliação de nota fiscal**: a Microsoft encarregadou o valor certo?  De quanto é minha fatura e posso calculá-la sozinho?

- **Encargos cruzados**: agora que sei quanto estou sendo cobrado, quem em minha organização precisa pagar?

- **Otimização de custos**: sei quanto eu fui cobrado. Como posso aproveitar ao máximo o dinheiro que estou a gastar no Azure?

- **Controle de custos**: desejo ver quanto estou gastando e usando o Azure com o passar do tempo. Quais são as tendências? Como posso melhorar?

- **Gastos com o Azure durante o mês**: quanto meu mês atual está gastando até hoje? É necessário fazer alterações nos meus gastos e/ou na utilização do Azure? Em que alturas do mês é maior o consumo do Azure?

- **Alertas**: como posso configurar o consumo baseado em recursos ou os alertas baseados em monetários?

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
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Utilização sem Classificação               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> O mapeamento de cenário para API não inclui as APIs de Consumo do Enterprise. Sempre que possível, utilize as APIs de Consumo geral nos novos cenários de desenvolvimento.

## <a name="api-summaries"></a>Resumos das APIs

### <a name="consumption"></a>Consumo
Os clientes Web Direct e Enterprise podem utilizar todas as APIs que se seguem, exceto quando for indicado:

-   [API de orçamentos](https://docs.microsoft.com/rest/api/consumption/budgets) (*somente clientes empresariais*): Crie orçamentos de uso ou de custo para recursos, grupos de recursos ou medidores de cobrança. Quando criar orçamentos, poderá configurar alertas para notificá-lo de quando excedeu os limiares de orçamento definidos. Também pode configurar ações para que ocorram quando os valores do orçamento tiverem sido atingidos.

-   [API de encargos do Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces): obter dados de uso e custos em todos os recursos do Azure Marketplace (ofertas de parceiros do Azure). Pode utilizar estes dados para somar os custos em todos os recursos do Marketplace ou para investigar os custos/utilização em recursos específicos.

-   [API de folha de preços](https://docs.microsoft.com/rest/api/consumption/pricesheet) (*somente clientes empresariais*): Obtenha preços personalizados para todos os medidores. As empresas podem utilizar estes dados em combinação com detalhes de utilização e informações de utilização do Marketplace para calcular os custos com os dados de utilização e do Marketplace.

-   [API de recomendações de reserva](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations): Obtenha recomendações para comprar instâncias de VM reservadas. As recomendações ajudam-no a analisar a poupança de custos e os montantes de compra esperados. Para obter mais informações, veja [APIs para automatização de reserva do Azure](../reservations/reservation-apis.md).

-   [API de detalhes de reserva](https://docs.microsoft.com/rest/api/consumption/reservationsdetails): consulte informações sobre reservas de VM adquiridas anteriormente, como quanto consumo é reservado versus quanto é usado. Pode ver os dados de acordo com os detalhes ao nível da VM. Para obter mais informações, veja [APIs para automatização de reserva do Azure](../reservations/reservation-apis.md).

-   [API de resumos de reserva](https://docs.microsoft.com/rest/api/consumption/reservationssummaries): consulte informações agregadas sobre reservas de VM que sua organização comprou, como quanto consumo é reservado versus quanto é usado na agregação. Para obter mais informações, veja [APIs para automatização de reserva do Azure](../reservations/reservation-apis.md).

-   [API de detalhes de uso](https://docs.microsoft.com/rest/api/consumption/usagedetails): Obtenha informações de custo e uso em todos os recursos do Azure da Microsoft. As informações estão na forma de registos de detalhes de utilização, que atualmente são emitidos uma vez por dia para cada medidor. Pode utilizar as informações para somar os custos em todos os recursos ou para investigar os custos/utilização em recursos específicos.

-   [API do RateCard](/previous-versions/azure/reference/mt219005(v=azure.100)): Obtenha taxas de medição se você for um cliente direto da Web. Em seguida, pode utilizar as informações devolvidas com as informações de utilização dos recursos para calcular manualmente a fatura esperada.

-   [API de uso sem classificação](/previous-versions/azure/reference/mt219003(v=azure.100)): Obtenha informações brutas de uso antes que o Azure faça qualquer medição/cobrança.

### <a name="billing"></a>Faturação
-   [API de períodos de cobrança](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Determine um período de cobrança para analisar, junto com as IDs de fatura para esse período. Pode utilizar IDs de fatura com a API de Faturas.

-   [API de notas fiscais](/rest/api/billing/2019-10-01-preview/invoices): Obtenha a URL de download para uma fatura de um período de cobrança em formato PDF.

### <a name="enterprise-consumption"></a>Consumo do Enterprise
As seguintes APIs são apenas para o Enterprise:

-   [API de Resumo de saldo](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): Obtenha um resumo mensal das informações sobre saldos, novas compras, encargos de serviço do Azure Marketplace, ajustes e encargos excedentes. Pode obter estas informações para o período de faturação atual ou para qualquer período no passado. As empresas podem utilizar estes dados para comparar com os encargos de resumo calculados manualmente. Esta API não fornece informações específicas dos recursos ou uma vista agregada dos custos.

-   [API de detalhes de uso](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): Obtenha informações sobre o uso do Azure (das ofertas da Microsoft) para o mês atual, um período de cobrança específico ou um período de data personalizado. As empresas podem utilizar estes dados para calcular manualmente as faturas com base na tarifa e no consumo. As empresas também podem utilizar informações de departamento/organização para atribuir custos de atributos entre organizações. Os dados fornecem uma vista com utilização/custo específica para o recurso.

-   [API de cobrança do Marketplace Store](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): Obtenha informações sobre o uso do Azure (de ofertas de parceiros) para o mês atual, um período de cobrança específico ou um período de data personalizado. As empresas podem utilizar estes dados para calcular manualmente as faturas com base na tarifa e no consumo. As empresas também podem utilizar informações de departamento/organização para atribuir custos de atributos entre organizações. Esta API fornece uma vista específica para o recurso com utilização/custo.

-   [API da folha de preços](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): Obtenha a taxa aplicável para cada medidor para o registro e o período de cobrança fornecidos. Pode utilizar estas informações de tarifa em combinação com os detalhes de utilização e as informações de utilização do Marketplace para calcular manualmente a fatura esperada.

-   [API de períodos de cobrança](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Obtenha uma lista de períodos de cobrança. A API também fornece uma propriedade que aponta para a rota da API para os quatro conjuntos de dados da API empresarial que pertencem ao período de cobrança:: Balancesummary, UsageDetails, encargos do Marketplace e PriceSheet.

-   [API de recomendações de instância reservada](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): examine 7 dias, 30 dias ou 60 dias de uso da máquina virtual e Obtenha recomendações de compra únicas e compartilhadas. Pode utilizar esta API para analisar a poupança de custos esperada e os valores de compra recomendados. Para obter mais informações, veja [APIs para automatização de reserva do Azure](../reservations/reservation-apis.md).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Qual é a diferença entre as APIs de Relatórios do Enterprise e as APIs de Consumo? Quando devo utilizar cada uma delas?
Estas APIs têm um conjunto de funcionalidades semelhante e podem responder ao mesmo conjunto amplo de perguntas no espaço da faturação e gestão de custos. No entanto, visam públicos diferentes:

- As APIs de Relatórios Enterprise estão disponíveis para os clientes que assinaram um Contrato Enterprise com a Microsoft, que concede acesso a alocações monetárias negociadas e a preços personalizados. As APIs exigem uma chave que pode obter no [Enterprise Portal.](https://ea.azure.com) Para uma descrição destas APIs, veja [Descrição geral das APIs de Relatórios para clientes Enterprise](enterprise-api.md).

- As APIs de Consumo estão disponíveis para todos os clientes, com algumas exceções. Para obter mais informações, veja [Descrição geral da API de consumo do Azure](consumption-api-overview.md) e a [ referência da API de Consumo do Azure](https://docs.microsoft.com/rest/api/consumption/). Recomendamos as APIs fornecidas como a solução para os cenários de desenvolvimento mais recentes.

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Qual é a diferença entre a API de Detalhes de Utilização e a API de Utilização?
Estas APIs fornecem dados fundamentalmente diferentes:

- A [API de Detalhes de Utilização](https://docs.microsoft.com/rest/api/consumption/usagedetails) fornece informações de utilização e custo do Azure por instância de medidor. Os dados fornecidos já passaram pelo sistema de medição de custos no Azure e foram-lhes aplicados os custos, juntamente com outras alterações possíveis:

   - Alterações à conta para a utilização de alocações monetárias pré-pagas
   - Alterações à conta para discrepâncias de utilização descobertas pelo Azure

- A [API de utilização](/previous-versions/azure/reference/mt219003(v=azure.100)) fornece informações brutas de utilização do Azure antes de passar pelo sistema de medição de custos no Azure. Estes dados podem não ter nenhuma correlação com a utilização ou o montante de faturação que é visto após o sistema de medição de carga do Azure.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Qual é a diferença entre a API de Fatura e a API de Detalhes de Utilização?
Estas APIs fornecem uma vista diferente dos mesmos dados:

- A [API de Fatura](/rest/api/billing/2019-10-01-preview/invoices) é apenas para clientes do Web Direct. Fornece uma acumulação mensal da sua fatura com base nos encargos agregados para cada tipo de medidor.

- A [API de Detalhes de Utilização](https://docs.microsoft.com/rest/api/consumption/usagedetails) fornece uma vista granular dos registos de utilização/custo para cada dia. Tanto os Clientes Enterprise como os clientes Web Direct podem utilizá-la.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Qual é a diferença entre a API da Folha de Preços e a API do RateCard?
Estas APIs fornecem conjuntos de dados semelhantes, mas têm públicos diferentes:

- A [API da Folha de Preços](https://docs.microsoft.com/rest/api/consumption/pricesheet) fornece os preços personalizados que foram negociados para um cliente Enterprise.

- A [API do RateCard ](/previous-versions/azure/reference/mt219005(v=azure.100)) fornece os preços destinados ao público que se aplicam aos clientes Web Direct.

## <a name="next-steps"></a>Passos seguintes

- Para informações sobre como utilizar as APIs do Azure para obter informações sobre a utilização do Azure, veja [Descrição geral da API de Consumo do Azure](consumption-api-overview.md) e [Descrição geral da API de Faturação do Azure](usage-rate-card-overview.md).

- Para comparar a sua fatura com o ficheiro de utilização diária detalhada e os relatórios de gestão de custos no portal do Azure, veja [Compreender a sua fatura para o Microsoft Azure](../understand/review-individual-bill.md).

- Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
