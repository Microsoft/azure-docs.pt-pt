---
title: Marketplace Insights - Mercado comercial da Microsoft, Microsoft AppSource e Azure Marketplace
description: Aceda a um resumo da análise web do marketplace, que lhe permite medir o envolvimento do cliente no Microsoft AppSource e no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/22/2019
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 1a645a333db9b24005639f4adbb2913a2b887b66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89055673"
---
# <a name="marketplace-insights-dashboard-in-partner-center"></a>Painel de insights de mercado no Partner Center

Este artigo fornece informações sobre o dashboard Marketplace Insights no Partner Center. Este dashboard apresenta um resumo da análise web do marketplace que permite aos editores medir o envolvimento do cliente nas respetivas páginas de detalhes do produto listadas nas lojas online do mercado comercial: Microsoft AppSource e Azure Marketplace.

## <a name="marketplace-insights-dashboard"></a>Dashboard de Informações do Marketplace

Para aceder ao **dashboard Marketplace Insights** no Partner Center, abra o **[separador Analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** em Mercado Comercial.

Pode ver as representações gráficas dos seguintes itens:  

- [Resumo do Marketplace Insights](#marketplace-insights-summary)
- [Visitas de página por geografia](#page-visits-by-geography)  
- [Visitas de página versus tendência única de visitantes](#page-visits-versus-unique-visitors-trend)
- [Chamada à ação (CTA) contra visitantes únicos com CTAs](#call-to-action-versus-unique-visitors-with-ctas)
- [Visitas de página e chamada à ação por ofertas](#page-visits-and-calls-to-action-by-offers)
- [Tendência percentual de apelo à ação](#call-to-action-percentage-trend)
- [Visitas de página e chamadas à ação por domínios de referência](#page-visits-and-calls-to-action-by-referral-domains)
- [Tabela de detalhes do Marketplace Insights](#marketplace-insights-details-table)

A latência máxima entre os utilizadores que visitam ofertas no Azure Marketplace ou AppSource e reportando no Partner Center é de 48 horas.

>[!NOTE]
> Para definições detalhadas de terminologia analítica, consulte [perguntas frequentes e terminologia para análise de mercado comercial.](./faq-terminology.md)

### <a name="insights-dashboard-layout"></a>Insights dashboard layout

Ver métricas de mercado comercial de várias maneiras:

- Separadores de lojas online
- Filtros de página
- Filtros de data

**Separados de lojas online**: Pode ver as métricas das suas ofertas separadamente através dos separadores appSource & Azure Marketplace. Selecione a(s) oferta(s) da lista de abandono da oferta no direito de ver uma visualização das métricas para a(s) oferta(s) selecionadas. Por predefinição, todas as ofertas são selecionadas.

![Parceiro Center Insights dashboard oferta lista de dropdown](./media/insights-offer-dropdown.png)

**Filtros de página de**insights : Estes filtros são aplicados ao nível da oferta (página de detalhes do produto). Pode selecionar vários filtros para os critérios que pretende visualizar. Este filtro aplica-se a toda a secção Marketplace Insights, incluindo gráficos e detalhes.

![Filtros de página de dashboard do Centro Parceiro Insights](./media/insights-page-filter.png)

- Os Nomes da Oferta estão listados apenas para as ofertas que têm visitas de página no intervalo de datas selecionadas.  
- A seleção predefinida é 'All' para cada uma das opções de filtro
- Os filtros aplicados mostram a contagem de seleções para as escolhas feitas. Os filtros aplicados não serão apresentados para a seleção predefinida 'All'.

![Filtros Partner Center Insights aplicados](./media/insights-page-filter-two.png)

**Filtros de data insights**: Este filtro aplica-se a toda a secção De Insights do Mercado. Os filtros podem incluir intervalos de datas pré-determinados ou um intervalo de datas personalizado.

![Filtros de data do Partner Center Insights](./media/insights-date-range.png)

## <a name="marketplace-insights-summary"></a>Resumo do Marketplace Insights

A secção de resumo do Marketplace Insights apresenta uma contagem de visitas de **página, chamadas** **à ação**e **visitantes únicos** para a gama de datas selecionadas.

>[!NOTE]
>O dashboard Marketplace Insights fornece dados clickstream, que não devem ser correlacionados com os leads gerados no ponto final do destino principal.

### <a name="page-visits"></a>Visitas de página

Este número representa a contagem de sessões de utilizador distintas na página de oferta (página de detalhes do produto) para uma gama de datas selecionada. O indicador de percentagem vermelha/verde representa o crescimento % das visitas de página. O gráfico de tendências representa a contagem mensal de visitas de página.

### <a name="unique-visitors"></a>Visitantes únicos

Este número representa a contagem de visitantes distinta durante o intervalo de datas selecionado para a(s) oferta(s) selecionada no filtro de página. Um visitante que tenha visitado uma ou mais páginas de detalhes do produto será contado como um visitante único.

### <a name="call-to-action"></a>Chamada à ação

Este número representa a contagem de cliques do botão **Call to Action** concluídos na página de oferta (página de detalhes do produto). **As chamadas para a ação** são contadas quando os utilizadores selecionam o Get it **now**, **Free trial**, Contact **me**ou **Test drive** buttons.

![Partner Center Insights chamada para resumo de ação](./media/insights-summary.png)

## <a name="page-visits-by-geography"></a>Visitas de página por geografia

O mapa de calor abaixo exibe a contagem de visitas de **página,** **chamadas à ação,** e **visitantes únicos de acordo com o país do cliente.** As visitas de página mais altas são representadas por cores de mapas mais escuras e as visitas de página mais baixa são representadas por cores de mapas mais claras.

![Propagação geográfica do Centro Parceiro Insights](./media/insights-geography.png)

O mapa de calor inclui as seguintes capacidades:

- O mapa de calor tem uma grelha suplementar para ver os detalhes das visitas de **página,** **chamadas à ação** e **visitantes únicos** num local específico; pode fazer zoom para um local específico, se preferir.  
- **A disseminação de países/regiões** é a contagem de todos os países/regiões de onde os seus clientes reportaram visitas de página durante a gama de datas selecionadas.
- Pode pesquisar e selecionar um país na grelha para ampliar a localização no mapa. Reverta para a vista original selecionando **Home** no mapa.

## <a name="page-visits-versus-unique-visitors-trend"></a>Visitas de página versus tendência única de visitantes

As colunas abaixo representam a contagem de visitas mensais de páginas, que são exibidas no eixo Y (eixo no lado esquerdo da tabela). A linha de tendência representa a tendência mensal dos visitantes únicos, que é exibida no eixo Y secundário (eixo no lado direito da tabela), para as suas ofertas publicadas nas lojas online: Azure Marketplace e AppSource.

![Visitas de página do Partner Center Insights versus tendência única dos visitantes](./media/insights-page-vists-unique-visitors.png)

## <a name="call-to-action-versus-unique-visitors-with-ctas"></a>Chamada à ação contra visitantes únicos com CTAs

As colunas empilhadas representam chamadas mensais de ação (CTA), que são divididas por tipos CTA **(Get it now**, **Contact me**, and Free **Trial**) e traçadas no eixo Y (eixo no lado esquerdo da página). A linha de tendência representa a tendência mensal de visitantes únicos com CTAs, que é exibido no eixo Y secundário (eixo no lado direito da tabela), para as suas ofertas publicadas no Azure Marketplace e appSource.

![Partner Center Insights apela à ação contra visitantes únicos com CTAs](./media/insights-call-to-action-unique-visitors.png)

## <a name="page-visits-and-calls-to-action-by-offers"></a>Visitas de página e chamadas à ação através de ofertas

O gráfico de tartes exteriores representa a desagregação das visitas de **página** com base nas ofertas que publicou no mercado e selecionada no filtro. O gráfico interno representa as **Chamadas à ação** para a desagregação das mesmas ofertas.

![Visitas de página do Partner Center Insights e chamadas à ação por ofertas](./media/insights-page-visits-and-cta-by-offer.png)

## <a name="call-to-action-percentage-trend"></a>Tendência percentual de apelo à ação

A **tendência de percentagem de apelo à ação** apresenta a percentagem de CTA para as ofertas publicadas no mercado. CTA % = (CTAs/visitas de página) * 100.

![Partner Center Insights apela à tendência da percentagem de ação](./media/insights-call-to-action-percentage-trend.png)

## <a name="page-visits-and-calls-to-action-by-referral-domains"></a>Visitas de página e chamadas à ação por domínios de referência

O gráfico abaixo apresenta os 50 principais domínios de referência. A seleção de um domínio de referência específico mostra a tendência mensal das visitas de página e apela à ação no gráfico à direita.

![Visitas de página do Partner Center Insights e chamadas à ação por domínios e campanhas de referência](./media/insights-page-visits-call-to-actions.png)

## <a name="marketplace-insights-details-table"></a>Tabela de detalhes do Marketplace Insights

Esta tabela fornece uma visão de lista das visitas de página e as chamadas para a ação das suas ofertas selecionadas ordenadas por data.

![Tabela de detalhes do Partner Center Insights](./media/insights-details-page.png)

- Os dados podem ser extraídos para um ficheiro CSV se a contagem de registos for inferior a 1000.
- Se a contagem de registos for superior a 1000, os dados exportados serão colocados de forma assíncronea numa página de downloads durante os próximos 30 dias.
- Filtrar os dados por Ofeia nomes e nomes de campanha para exibir os dados em que está interessado.

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral dos relatórios de análise disponíveis no mercado comercial do Partner Center, consulte [Analytics para o mercado comercial no Partner Center](./analytics.md).
- Para gráficos, tendências e valores de dados agregados que resumem a atividade do mercado para a sua oferta, consulte [o Painel De Resumo em análise de mercado comercial.](./summary-dashboard.md)
- Para obter informações sobre as suas encomendas num formato gráfico e transferível, consulte [o Painel de Encomendas em análise de marketplace comercial.](./orders-dashboard.md)
- Para Máquina Virtual (VM) oferece métricas de utilização e faturação medido, consulte [o Painel de Utilização em análise de mercado comercial.](./usage-dashboard.md)
- Para obter informações detalhadas sobre os seus clientes, incluindo tendências de crescimento, consulte [o Customer Dashboard em análise de mercado comercial.](./customer-dashboard.md)
- Para obter uma lista dos seus pedidos de descarregamento nos últimos 30 dias, consulte [Downloads Dashboard em análise de mercado comercial.](./downloads-dashboard.md)
- Para ver uma visão consolidada do feedback dos clientes para as ofertas no Azure Marketplace e appSource, consulte [ratings e comentários no painel de análise de mercado comercial.](./ratings-reviews.md)
- Para perguntas frequentes sobre análise de mercado comercial e para um dicionário abrangente de termos de dados, consulte [perguntas frequentes e terminologia para análise de mercado comercial.](./faq-terminology.md)
