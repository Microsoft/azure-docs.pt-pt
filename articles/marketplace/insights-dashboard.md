---
title: Dashboard de Informações do Marketplace na análise de mercados comerciais
description: Aceda a um resumo da análise web do marketplace no Partner Center, que lhe permite medir o envolvimento do cliente no Microsoft AppSource e no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: a8a53a54d30df9a2f7079d705f754e554328c3f7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553940"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>Dashboard de Informações do Marketplace na análise de mercados comerciais

Este artigo fornece informações sobre o dashboard Marketplace Insights no Partner Center. Este dashboard apresenta um resumo da análise web do marketplace comercial que permite aos editores medir o envolvimento do cliente nas respetivas páginas de detalhes do produto listadas nas lojas online do mercado comercial: Microsoft AppSource e Azure Marketplace.

Para aceder ao dashboard **Marketplace Insights** no Partner Center, no Mercado Comercial, selecione **[Analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **Marketplace Insights**.

Para definições detalhadas de terminologia analítica, consulte [terminologia de análise de mercado comercial e questões comuns.](./analytics-faq.md)

## <a name="marketplace-insights-dashboard"></a>Dashboard de Informações do Marketplace

O painel Marketplace Insights apresenta uma visão geral do Azure Marketplace e o AppSource oferece o desempenho do negócio. Este painel de instrumentos fornece uma visão geral do seguinte:

- Tendência de visitas de página
- Tendência de chamada para ações
- Visitas de página e chamada a ações contra ofertas, domínios de referência e iDs de campanha
- Marketplace Insights por geografia
- Tabela de detalhes do Marketplace Insights

O dashboard Marketplace Insights fornece dados clickstream, que não devem ser correlacionados com os leads gerados no ponto final do destino principal.

> [!NOTE]
> A latência máxima entre os utilizadores que visitam ofertas no Azure Marketplace ou AppSource e reportando no Partner Center é de 48 horas.

## <a name="elements-of-the-marketplace-insights-dashboard"></a>Elementos do painel de insights do mercado

O dashboard Marketplace Insights exibe detalhes de telemetria web para Azure Marketplace e AppSource em dois separados. As secções seguintes descrevem como utilizar o dashboard Marketplace Insights e como ler os dados.

### <a name="month-range"></a>Intervalo mensal

Pode encontrar uma seleção de mês no canto superior direito de cada página. Personalize a saída dos gráficos da página **marketplace Insights** selecionando uma gama de meses com base nos últimos 6, ou 12 meses, ou selecionando uma gama de meses personalizada com uma duração máxima de 12 meses. A gama de mês predefinido (período de computação) é de seis meses.

:::image type="content" source="./media/insights-dashboard/month-filters.png" alt-text="Ilustra os filtros mensais no painel Marketplace Insights.":::

> [!NOTE]
> Todas as métricas nos widgets de visualização e relatórios de exportação honram o período de cálculo selecionado pelo utilizador.

### <a name="page-visits-trends"></a>Tendências de visitas de página

O gráfico de **Visitantes** do Marketplace Insights apresenta uma contagem de _visitas_ de página e _visitantes únicos_ para o período de cálculo selecionado.

**Visitas de página**: Este número representa a contagem de sessões de utilizador distintas na página de listagem de oferta (página de detalhes do produto) para um período de cálculo selecionado. Os indicadores de percentagem vermelha e verde representam a percentagem de crescimento das visitas de página. O gráfico de tendências representa a contagem mensal de visitas de página.

**Visitantes únicos**: Este número representa a contagem de visitantes distinta durante o período de cálculo selecionado para a(s) oferta(s) em Azure Marketplace e AppSource. Um visitante que tenha visitado uma ou mais páginas de detalhes do produto será contado como um visitante único.

[![Ilustra a tabela de Visitantes no painel De Marketplace Insights.](./media/insights-dashboard/visitors.png)](./media/insights-dashboard/visitors.png#lightbox)

### <a name="call-to-actions-trend"></a>Tendência de chamada para ações

Este número representa a contagem de cliques do botão **Call to Action** concluídos na página de listagem de oferta (página de detalhe do produto). _As chamadas para a ação_ são contadas quando os utilizadores selecionam os botões Get It **Now**, **Free Trial**, **Contact Me** ou **Test Drive.**

[![Ilustra o gráfico de chamada para ação no painel de insights do Mercado.](./media/insights-dashboard/call-to-actions-trend.png)](./media/insights-dashboard/call-to-actions-trend.png#lightbox)

### <a name="page-visits-and-call-to-actions-against-offers-referral-domains-and-campaign-ids"></a>Visitas de página e chamada a ações contra ofertas, domínios de referência e iDs de campanha

**Domínios de referência**: A seleção de um domínio de referência específico mostra a tendência mensal das visitas de página e apela à ação no gráfico à direita.

:::image type="content" source="./media/insights-dashboard/referral-domain.png" alt-text="Ilustra o gráfico de domínio de referência no painel De marketplace Insights.":::

**Ofertas**: Selecione uma oferta específica, para ver a tendência mensal das visitas de página e apela à ação na tabela à direita.

:::image type="content" source="./media/insights-dashboard/offer-alias.png" alt-text="Ilustra o gráfico de pseudónimos da oferta no painel marketplace Insights.":::

**IDs de campanha**: Ao selecionar um ID de campanha específico, você deve ser capaz de entender o sucesso da campanha. Para cada campanha, você deve ser capaz de ver a tendência mensal das visitas de página e chamadas para a ação na tabela para a direita.

:::image type="content" source="./media/insights-dashboard/campaign.png" alt-text="Ilustra o gráfico de campanha no painel marketplace Insights.":::

### <a name="marketplace-insights-by-geography"></a>Marketplace Insights por geografia

Para o período de cálculo selecionado, o mapa de calor apresenta a contagem de visitas de página, visitantes únicos e chamadas à ação (CTA). A cor clara para a luz no mapa representa o valor baixo a alto dos visitantes únicos. Selecione um registo na tabela para fazer zoom em um país/região.

:::image type="content" source="./media/insights-dashboard/geographical-spread.png" alt-text="Ilustra o gráfico de spread geográfico no painel Marketplace Insights.":::

Tenha em atenção o seguinte:

- Pode mover o mapa para ver a localização exata.
- Pode aproximar-se de um local específico.
- O mapa de calor tem uma grelha suplementar para ver os detalhes da contagem de clientes, contagem de encomendas e horas de utilização normalizadas no local específico.
- Pode pesquisar e selecionar um país/região na grelha para ampliar a localização no mapa. Reverta para a vista original selecionando o botão **Home** no mapa.

### <a name="marketplace-insights-details-table"></a>Tabela de detalhes do Marketplace Insights

Esta tabela fornece uma visão da lista das visitas de página e as chamadas para a ação das páginas das suas ofertas selecionadas ordenadas por data.

- Os dados podem ser extraídos para um . TSV ou . . Ficheiro CSV se a contagem de registos for inferior a 1.000.
- Se a contagem de registos for superior a 1.000, os dados exportados serão colocados de forma assíncronea numa página de downloads durante os próximos 30 dias.
- Filtrar os dados por Ofeia nomes e nomes de campanha para exibir os dados em que está interessado.

> [!TIP]
> Pode utilizar o ícone de descarregamento no canto superior direito de qualquer widget para descarregar os dados. Pode fornecer feedback sobre cada um dos widgets clicando no ícone "polegares para cima" ou "polegares para baixo".

| Nome da coluna em<br>interface de utilizador | Nome do atributo | Definição | Nome da coluna em programático<br>relatórios de acesso |
| ------------ | ------------- | ------------- | ------------- |
| Data | Data de Visita | A data da visita de página e/ou CTA clique na geração de eventos na página da oferta no Azure Marketplace e/ou AppSource. | Data |
| Nome da Oferta | Nome da Oferta | O nome da oferta do mercado comercial. | OfferName |
| Domínio de referência | Domínio de referência | O nome do domínio de referência de onde a visita da página aconteceu. Se não houver domínios de referência capturados para a visita de página, então a entrada correspondente é "O domínio de encaminhamento não está presente". |  ReferênciaDomínio |
| Nome do País | Nome do País | O nome do país de onde a visita de página aconteceu. | Nome do país |
| Visitas de Página | Visitas de Página | O número de visitas de página associadas ao Nome da Oferta para uma data específica. | PageVisits |
| Get It Now | Get It Now | O número de cliques para o CTA "Get It Now" na página da oferta para uma data específica. | GetItNow |
| Contacte-me | Contacte-me | O número de cliques para a CTA "Contacte-me" na página da oferta para uma data específica. | ContatoMe |
| Versão de Teste | Versão de Teste | O número de cliques para o CTA "Test Drive" na página da oferta para uma data específica. | TestDrive |
| Avaliação Gratuita | Avaliação Gratuita | O número de cliques para a CTA "Teste Livre" na página da oferta para uma data específica. | FreeTrial |
|||||

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral dos relatórios de análise disponíveis no mercado comercial, consulte os [relatórios analíticos do Access para o mercado comercial no Partner Center.](./partner-center-portal/analytics.md)
- Para obter informações sobre as suas encomendas num formato gráfico e transferível, consulte [o painel de encomendas em análise de marketplace comercial.](./orders-dashboard.md)
- Para máquinas virtuais (VM) oferece métricas de utilização e faturação medido, consulte [o painel de uso em análise de mercado comercial.](./usage-dashboard.md)
- Para obter informações detalhadas sobre os seus clientes, incluindo tendências de crescimento, consulte [o painel de clientes em análise de mercado comercial.](./customer-dashboard.md)
- Para obter uma lista dos seus pedidos de descarregamento nos últimos 30 dias, consulte [downloads dashboard em análise de mercado comercial.](./partner-center-portal/downloads-dashboard.md)
- Para ver uma visão consolidada do feedback do cliente para as ofertas no Azure Marketplace e appSource, consulte [o painel de avaliação de avaliações & ratings no Partner Center](./partner-center-portal/ratings-reviews.md).
- Para perguntas frequentes sobre análise de mercado comercial e para um dicionário abrangente de termos de dados, consulte a terminologia de análise de [mercado comercial e questões comuns.](./analytics-faq.md)