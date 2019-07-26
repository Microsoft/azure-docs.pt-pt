---
title: Cenários de automação para cobrança e gerenciamento de custos do Azure | Microsoft Docs
description: Saiba como os cenários comuns de cobrança e gerenciamento de custos são mapeados para APIs diferentes.
services: billing
documentationcenter: ''
author: bandersmsft
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
ms.author: banders
ms.openlocfilehash: 3d06df9b9a90f26b39afc17b8fcd02c85da567a1
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443244"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Cenários de automação para cobrança e gerenciamento de custos

Este artigo lista cenários comuns de cobrança e gerenciamento de custos do Azure. Ele mapeia esses cenários para APIs que você pode usar. Em cada mapeamento de cenário para API, você pode encontrar um resumo das APIs e da funcionalidade que elas oferecem.

## <a name="common-scenarios"></a>Cenários comuns

Você pode usar as APIs de cobrança e gerenciamento de custos em vários cenários para responder a perguntas relacionadas ao custo e ao uso. Aqui está uma descrição dos cenários comuns:

- **Reconciliação de nota fiscal**: A Microsoft encarregadou o valor certo?  Qual é minha fatura e posso calculá-la sozinho?

- Encargos cruzados: Agora que sei quanto estou sendo cobrado, quem em minha organização precisa pagar?

- **Otimização de custo**: Eu sei quanto fui cobrado. Como posso aproveitar ao máximo o dinheiro que estou gastando no Azure?

- **Controle de custos**: Quero ver quanto estou gastando e usando o Azure com o passar do tempo. Quais são as tendências? Como posso fazer melhor?

- **Gastos com o Azure durante o mês**: Quanto meus gastos do meu mês atual têm até hoje? É necessário fazer alterações nos meus gastos e/ou no uso do Azure? Quando durante o mês eu estou consumindo o Azure mais?

- **Alertas**: Como posso configurar o consumo baseado em recursos ou os alertas baseados em monetários?

## <a name="scenario-to-api-mapping"></a>Mapeamento de cenário para API

|         API        | Reconciliação de fatura    | Encargos cruzados    | Otimização de custos    | Controle de custos    | Gasto de Midmonth    | Alertas    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Orçamentos                     |                           |                  |           X          |                  |                    |     X     |
| Encargos do Marketplace                |             X             |         X        |           X          |         X        |          X         |     X     |
| Folha de Preços                 |             X             |         X        |           X          |         X        |          X         |           |
| Recomendações da Reserva |                           |                  |           X          |                  |                    |           |
| Detalhes da Reserva         |                           |                  |           X          |         X        |                    |           |
| Resumos da Reserva       |                           |                  |           X          |         X        |                    |           |
| Detalhes de Utilização               |             X             |         X        |           X          |         X        |          X         |     X     |
| Períodos de cobrança             |             X             |         X        |           X          |         X        |                    |           |
| Faturas                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Uso sem classificação               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> O mapeamento cenário a API não inclui as APIs de consumo Enterprise. Sempre que possível, use as APIs de consumo geral para novos cenários de desenvolvimento.

## <a name="api-summaries"></a>Resumos de API

### <a name="consumption"></a>Consumo
Os clientes Web Direct e Enterprise podem usar todas as seguintes APIs, exceto quando indicado:

-   [API](https://docs.microsoft.com/rest/api/consumption/budgets) de orçamentos (*Somente clientes empresariais*): Crie orçamentos de custo ou de uso para recursos, grupos de recursos ou medidores de cobrança. Quando você tiver criado orçamentos, poderá configurar alertas para notificá-lo quando excedeu os limites de orçamento definidos. Você também pode configurar ações para ocorrer quando você tiver atingido os valores de orçamento.

-   [API](https://docs.microsoft.com/rest/api/consumption/marketplaces)de encargos do Marketplace: Obtenha dados de uso e de custos em todos os recursos do Azure Marketplace (ofertas de parceiros do Azure). Você pode usar esses dados para somar os custos em todos os recursos do Marketplace ou para investigar os custos/uso em recursos específicos.

-   [API de folha de preços](https://docs.microsoft.com/rest/api/consumption/pricesheet) (*Somente clientes empresariais*): Obtenha preços personalizados para todos os medidores. As empresas podem usar esses dados em combinação com detalhes de uso e informações de uso do Marketplace para calcular os custos usando dados de uso e do Marketplace. 

-   [API de recomendações de reserva](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations): Obtenha recomendações para comprar instâncias de VM reservadas. As recomendações ajudam você a analisar a economia de custos e os valores de compra esperados. Para obter mais informações, consulte [APIs para automação de reserva do Azure](billing-reservation-apis.md).

-   [API de detalhes de reserva](https://docs.microsoft.com/rest/api/consumption/reservationsdetails): Veja informações sobre reservas de VM adquiridas anteriormente, como quanto consumo é reservado versus quanto é usado. Você pode ver os dados de acordo com os detalhes de nível de VM. Para obter mais informações, consulte [APIs para automação de reserva do Azure](billing-reservation-apis.md).

-   [API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries)de resumos de reserva: Confira informações agregadas sobre reservas de VM que sua organização comprou, como quanto consumo é reservado versus quanto é usado na agregação. Para obter mais informações, consulte [APIs para automação de reserva do Azure](billing-reservation-apis.md).

-   [API de detalhes de uso](https://docs.microsoft.com/rest/api/consumption/usagedetails): Obtenha informações de custos e uso em todos os recursos do Azure da Microsoft. As informações estão na forma de registros de detalhes de uso, que atualmente são emitidos uma vez por medidor por dia. Você pode usar as informações para somar os custos em todos os recursos ou investigar os custos/uso em recursos específicos.

-   [API do RateCard](/previous-versions/azure/reference/mt219005(v=azure.100)): Obtenha taxas de medição se você for um cliente direto da Web. Você pode usar as informações retornadas com as informações de uso de recursos para calcular manualmente a fatura esperada. 

-   [API de uso sem classificação](/previous-versions/azure/reference/mt219003(v=azure.100)): Obtenha informações brutas de uso antes que o Azure faça qualquer medição/cobrança.

### <a name="billing"></a>Faturação
-   [API de períodos de cobrança](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Determine um período de cobrança a ser analisado, juntamente com as IDs da fatura para esse período. Você pode usar IDs de fatura com a API de notas fiscais.

-   [API de notas fiscais](https://docs.microsoft.com/rest/api/billing/2018-11-01-preview/invoices): Obtenha a URL de download de uma fatura para um período de cobrança em formato PDF.

### <a name="enterprise-consumption"></a>Consumo empresarial
As seguintes APIs são apenas para empresas:

-   [API de Resumo de saldo](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): Obtenha um resumo mensal das informações sobre saldos, novas compras, encargos de serviço do Azure Marketplace, ajustes e encargos excedentes. Você pode obter essas informações para o período de cobrança atual ou para qualquer período no passado. As empresas podem usar esses dados para comparar com os encargos de resumo calculados manualmente. Essa API não fornece informações específicas de recursos ou uma exibição agregada de custos.

-   [API de detalhes de uso](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): Obtenha informações sobre o uso do Azure (das ofertas da Microsoft) para o mês atual, um período de cobrança específico ou um período de data personalizado. As empresas podem usar esses dados para calcular manualmente as faturas com base na taxa e no consumo. As empresas também podem usar informações de departamento/Organização para os custos de atributos entre as organizações. Os dados fornecem uma exibição específica do recurso de uso/custo.

-   [API de encargo da loja do Marketplace](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): Obtenha informações sobre o uso do Azure (de ofertas de parceiros) para o mês atual, um período de cobrança específico ou um período de data personalizado. As empresas podem usar esses dados para calcular manualmente as faturas com base na taxa e no consumo. As empresas também podem usar informações de departamento/Organização para os custos de atributos entre as organizações. Essa API fornece uma exibição específica do recurso de uso/custo.

-   [API da folha de preços](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): Obtenha a taxa aplicável para cada medidor para o registro e o período de cobrança fornecidos. Você pode usar essas informações de taxa em combinação com detalhes de uso e informações de uso do Marketplace para calcular manualmente a fatura esperada.

-   [API de períodos de cobrança](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Obter uma lista de períodos de cobrança. A API também fornece uma propriedade que aponta para a rota da API para os quatro conjuntos de dados da API empresarial que pertencem ao período de cobrança: : Balancesummary, UsageDetails, encargos do Marketplace e PriceSheet.

-   [API de recomendações de instância reservada](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): Examine os 7 dias, 30 dias ou 60 dias de uso da máquina virtual e Obtenha recomendações de compra únicas e compartilhadas. Você pode usar essa API para analisar a economia de custo esperada e os valores de compra recomendados. Para obter mais informações, consulte [APIs para automação de reserva do Azure](billing-reservation-apis.md).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Qual é a diferença entre as APIs de relatórios corporativos e as APIs de consumo? Quando devo usar cada um?
Essas APIs têm um conjunto de funcionalidades semelhante e podem responder ao mesmo conjunto amplo de perguntas no espaço de gerenciamento de custos e cobrança. Mas eles visam públicos diferentes: 

- As APIs de relatórios empresariais estão disponíveis para clientes que assinaram uma Enterprise Agreement com a Microsoft que concede acesso a compromissos monetários negociados e a preços personalizados. As APIs exigem uma chave que você pode obter do [Enterprise Portal](https://ea.azure.com). Para obter uma descrição dessas APIs, consulte [visão geral das APIs de relatórios para clientes empresariais](billing-enterprise-api.md).

- As APIs de consumo estão disponíveis para todos os clientes, com algumas exceções. Para obter mais informações, consulte [visão geral da API de consumo do Azure](billing-consumption-api-overview.md) e a [referência da API de consumo do Azure](https://docs.microsoft.com/rest/api/consumption/). Recomendamos as APIs fornecidas como a solução para os cenários de desenvolvimento mais recentes. 

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Qual é a diferença entre a API de detalhes de uso e a API de uso?
Essas APIs fornecem dados fundamentalmente diferentes:

- A [API de detalhes de uso](https://docs.microsoft.com/rest/api/consumption/usagedetails) fornece informações de uso e custo do Azure por instância de medidor. Os dados fornecidos já passaram pelo sistema de medição de custo no Azure e tiveram o custo aplicado a ele, juntamente com outras alterações possíveis:

   - Alterações à conta para o uso de compromissos monetários pré-pagos
   - Alterações à conta para discrepâncias de uso descobertas pelo Azure

- A [API de uso](/previous-versions/azure/reference/mt219003(v=azure.100)) fornece informações brutas de uso do Azure antes de passar pelo sistema de medição de custo no Azure. Esses dados podem não ter nenhuma correlação com o valor de uso ou cobrança que é visto após o sistema de medição de carga do Azure.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Qual é a diferença entre a API de fatura e a API de detalhes de uso?
Essas APIs fornecem uma exibição diferente dos mesmos dados:

- A [API de fatura](https://docs.microsoft.com/rest/api/billing/2018-11-01-preview/invoices) é apenas para clientes do Web Direct. Ele fornece um acúmulo mensal de sua fatura com base nos encargos agregados para cada tipo de medidor. 

- A [API de detalhes de uso](https://docs.microsoft.com/rest/api/consumption/usagedetails) fornece uma exibição granular dos registros de uso/custo para cada dia. Os clientes da empresa e do Direct Web podem usá-lo.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Qual é a diferença entre a API da folha de preços e a API do RateCard?
Essas APIs fornecem conjuntos de dados semelhantes, mas têm públicos diferentes:

- A [API da folha de preços](https://docs.microsoft.com/rest/api/consumption/pricesheet) fornece os preços personalizados que foram negociados para um cliente corporativo.

- A [API do RateCard](/previous-versions/azure/reference/mt219005(v=azure.100)) fornece os preços voltados ao público que se aplicam aos clientes diretos da Web.

## <a name="next-steps"></a>Passos Seguintes

- Para obter informações sobre como usar as APIs do Azure para obter insights sobre o uso do Azure, consulte [visão geral da API de consumo do Azure](billing-consumption-api-overview.md) e [visão geral da API de cobrança](billing-usage-rate-card-overview.md)

- Para comparar sua fatura com o arquivo de uso diário detalhado e os relatórios de gerenciamento de custos na portal do Azure, consulte [entender sua fatura de Microsoft Azure](billing-understand-your-bill.md).

- Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
