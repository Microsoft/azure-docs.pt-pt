---
title: Painel de insights do Marketplace na análise do Marketplace Comercial no Partner Center
description: Aceda a um resumo da análise web do marketplace, que permite aos editores medir o envolvimento dos clientes nas lojas AppSource e Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: a547ced9df98298361360ecab88036599cd86027
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275854"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>Painel de insights do marketplace na análise do Marketplace Comercial

Este artigo fornece informações sobre o dashboard Marketplace Insights no Partner Center. Este dashboard apresenta um resumo da análise web do marketplace, que permite aos editores medir o envolvimento dos clientes nas respetivas páginas de detalhes de produtos listadas nas montras do mercado: AppSource e Azure Marketplace.

## <a name="marketplace-insights-dashboard"></a>Dashboard de Informações do Marketplace

Para aceder ao **dashboard Marketplace Insights** no Partner Center, abra o **[separador Analyze](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** no Mercado Comercial.

Pode ver representações gráficas dos seguintes itens:  

- [Resumo de Insights do Mercado](#marketplace-insights-summary)
- [Visitas de página por geografia](#page-visits-by-geography)  
- [Visitas de página versus tendência única de visitantes](#page-visits-versus-unique-visitors-trend)
- [Chamada à ação contra visitantes únicos com CTAs](#call-to-action-versus-unique-visitors-with-ctas)
- [Visitas de página e chamada à ação por ofertas](#page-visits-and-calls-to-action-by-offers)
- [Chamada à tendência percentual de ação](#call-to-action-percentage-trend)
- [Visitas de página e chamadas à ação por domínios de referência](#page-visits-and-calls-to-action-by-referral-domains)
- [Tabela de detalhes do Marketplace Insights](#marketplace-insights-details-table)

>[!NOTE]
> Para definições detalhadas de terminologia analítica, consulte [perguntas frequentes e terminologia para análise](./faq-terminology.md)do Mercado Comercial.

### <a name="insights-dashboard-layout"></a>Layout do dashboard insights

Pode ver as métricas do Marketplace de várias maneiras:

- Separadores de montra
- Filtros de página
- Filtros de data

**Separadores de montras**: Pode visualizar as métricas das suas ofertas separadamente através dos separadores AppSource & Azure Marketplace. Selecione a oferta(s) da lista de dropdown da oferta no direito de ver uma visualização das métricas para a oferta(s) selecionada. Por padrão, todas as ofertas são selecionadas.

![Partner Center Insights dashboard oferecem lista de dropdown](./media/insights-offer-dropdown.png)

**Filtros**de página insights : Estes filtros são aplicados ao nível da oferta (página de detalhes do produto). Pode selecionar vários filtros para os critérios que deseja ver. Este filtro aplica-se a toda a secção Marketplace Insights, incluindo gráficos e detalhes.

![Filtros de página de painel de insights do partner Center Insights](./media/insights-page-filter.png)

- Os Nomes de Oferta estão listados apenas para as ofertas que têm visitas de página na gama de datas selecionadas.  
- A seleção padrão é 'All' para cada uma das opções de filtro
- Os filtros aplicados mostram a contagem de seleções(s) para as escolhas feitas. Os filtros aplicados não serão apresentados para a seleção 'All' predefinida.

![Filtros Partner Center Insights aplicados](./media/insights-page-filter-two.png)

**Filtros de data**de insights : Este filtro aplica-se a toda a secção Marketplace Insights. Os filtros podem incluir intervalos de data pré-determinados ou um intervalo de data seletiva.

![Filtros de data de data do Partner Center Insights](./media/insights-date-range.png)

## <a name="marketplace-insights-summary"></a>Resumo de Insights do Mercado

A secção resumo de insights do Marketplace exibe uma contagem de visitas de **página,** **chamadas à ação**e **visitantes únicos** para a gama de datas selecionadas.

### <a name="page-visits"></a>Visitas de página

Este número representa a contagem de sessões de utilizador distintas na página de oferta (página de detalhes do produto) para uma gama de datas selecionada. O indicador de percentagem vermelho/verde representa o crescimento % das visitas de página. O gráfico de tendências representa a contagem mensal de visitas de página.

### <a name="unique-visitors"></a>Visitantes únicos

Este número representa a contagem distinta de visitantes durante a gama de datas selecionada para a oferta(s) selecionada no filtro da página. Um visitante que tenha visitado uma ou mais páginas de detalhes do produto será contado como um visitante único.

### <a name="call-to-action"></a>Chamada à ação

Este número representa a contagem de cliques de botão **Call to Action** concluídos na página de oferta (página de detalhes do produto). **As chamadas para a ação** são contadas quando o Get it **now**, **Free trial**, **Contacte-me**, e os botões **de acionamento test** são selecionados.

![Partner Center Insights chama ao resumo de ação](./media/insights-summary.png)

## <a name="page-visits-by-geography"></a>Visitas de página por geografia

O mapa de calor abaixo exibe a contagem de visitas de **página,** **chamadas à ação,** e **visitantes únicos de acordo com o país do cliente.** As visitas de página mais altas são representadas por cores de mapas mais escuras e as visitas de página inferior são representadas por cores de mapas mais claras.

![Distribuição geográfica de Insights do Partner Center Insights](./media/insights-geography.png)

O mapa de calor inclui as seguintes capacidades:

- O mapa de calor tem uma grelha suplementar para ver os detalhes das visitas de **página,** **chamadas à ação** e **visitantes únicos** em um local específico; pode ampliar para um local específico, se preferir.  
- **Os países espalhados** são a contagem de todos os países de onde os seus clientes reportaram visitas de página durante a gama de datas selecionadas.
- Você pode pesquisar e selecionar um país na grelha para ampliar a localização no mapa. Volte à vista original selecionando **Home** no mapa.

## <a name="page-visits-versus-unique-visitors-trend"></a>Visitas de página versus tendência única de visitantes

As colunas abaixo representam a contagem de visitas mensais de páginas, que são exibidas no eixo Y (eixo no lado esquerdo do gráfico). A linha de tendência representa a tendência mensal de visitantes únicos, que é exibido no eixo Y secundário (eixo no lado direito da tabela), para as suas ofertas publicadas nas montras: Azure Marketplace e AppSource.

![Visitas de página do Partner Center Insights versus tendência única de visitantes](./media/insights-page-vists-unique-visitors.png)

## <a name="call-to-action-versus-unique-visitors-with-ctas"></a>Chamada à ação contra visitantes únicos com CTAs

As colunas empilhadas representam chamadas mensais de ação (CTA), que são desfeitas por tipos CTA **(Get it now**, **Contact e** **Free Trial)** e traçadas no eixo Y (eixo no lado esquerdo da página). A linha de tendência representa a tendência mensal de visitantes únicos com CTAs, que é exibido no eixo Y secundário (eixo no lado direito da tabela), para as suas ofertas publicadas no Azure Marketplace e appSource.

![Partner Center Insights chama à ação contra visitantes únicos com CTAs](./media/insights-call-to-action-unique-visitors.png)

## <a name="page-visits-and-calls-to-action-by-offers"></a>Visitas de página e chamadas à ação por ofertas

O gráfico de tartes exteriorrepresenta a desagregação das visitas da **Página** com base em ofertas que publicou no mercado e selecionadas no filtro. O gráfico interno representa as **Chamadas para a** desagregação de ação para as mesmas ofertas.

![Visita da página de Partner Center Insights e chamadas à ação por ofertas](./media/insights-page-visits-and-cta-by-offer.png)

## <a name="call-to-action-percentage-trend"></a>Chamada à tendência percentual de ação

A **tendência "Call to action percent"** apresenta a percentagem cta para as ofertas publicadas no mercado. CTA % = (visitas CTAs/página) * 100.

![Partner Center Insights chamam à tendência percentual de ação](./media/insights-call-to-action-percentage-trend.png)

## <a name="page-visits-and-calls-to-action-by-referral-domains"></a>Visitas de página e chamadas à ação por domínios de referência

O gráfico abaixo apresenta os 50 principais domínios de referência. A seleção de um domínio específico de referência mostra a tendência mensal de visitas de página e chamadas para a ação no gráfico à direita.

![Página de Insights do Partner Center visita e apela à ação por domínios e campanhas de referência](./media/insights-page-visits-call-to-actions.png)

## <a name="marketplace-insights-details-table"></a>Tabela de detalhes do Marketplace Insights

Esta tabela fornece uma visão da lista das visitas da página e as chamadas para a ação das suas ofertas selecionadas ordenadas por data.

![Tabela de detalhes do Partner Center Insights](./media/insights-details-page.png)

- Os dados podem ser extraídos para um ficheiro CSV se a contagem de registos for inferior a 1000.
- Se a contagem de registos for superior a 1000, os dados exportados serão colocados assincronicamente numa página de downloads durante os próximos 30 dias.
- Os filtros podem ser aplicados para mostrar os dados que lhe interessam. Os dados podem ser filtrados por nomes de oferta e nomes de campanha.  

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral dos relatórios de análise disponíveis no Partner Center Commercial Marketplace, consulte [o Analytics para o Mercado Comercial no Partner Center](./analytics.md).
- Para gráficos, tendências e valores de dados agregados que resumam a atividade do mercado para a sua oferta, consulte [Summary Dashboard na análise do Mercado Comercial.](./summary-dashboard.md)
- Para obter informações sobre as suas encomendas num formato gráfico e descarregável, consulte [o Painel de Encomendas na análise do Mercado Comercial](./orders-dashboard.md).
- Para máquina virtual (VM) oferece métricas de utilização e faturação medida, consulte [O Dashboard de Utilização na análise do Mercado Comercial](./usage-dashboard.md).
- Para obter informações detalhadas sobre os seus clientes, incluindo tendências de crescimento, consulte o [Customer Dashboard na análise do Mercado Comercial.](./customer-dashboard.md)
- Para obter uma lista dos seus pedidos de descarregamento ao longo dos últimos 30 dias, consulte [Downloads Dashboard na análise do Mercado Comercial](./downloads-dashboard.md).
- Para ver uma visão consolidada do feedback dos clientes para as ofertas no Azure Marketplace e appSource, consulte [ratings e análises dashboard na análise](./ratings-reviews.md)do Mercado Comercial.
- Para perguntas frequentes sobre análise do Mercado Comercial e para um dicionário abrangente de termos de dados, consulte [frequentemente perguntas e terminologia para análise](./faq-terminology.md)do Mercado Comercial.
