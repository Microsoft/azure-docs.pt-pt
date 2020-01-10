---
title: Painel do Marketplace insights na análise do Marketplace comercial no Partner Center
description: Acesse um resumo do Marketplace Web Analytics, que permite que os editores meçam o envolvimento do cliente nas vitrines do AppSource e do Azure Marketplace.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: edfdf7bbfaa5fa5abb00aa7efcb35bfbcd6e68a4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480544"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>Painel do Marketplace insights na análise do Marketplace comercial

Este artigo fornece informações sobre o painel do Marketplace insights no Partner Center. Este painel exibe um resumo do Marketplace Web Analytics, que permite que os editores meçam o envolvimento do cliente para suas respectivas páginas de detalhes do produto listadas nas vitrines do Marketplace: AppSource e Azure Marketplace.

## <a name="marketplace-insights-dashboard"></a>Dashboard de Informações do Marketplace

Para acessar o **painel do Marketplace insights** no Partner Center, abra a **[guia analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** em Marketplace comercial.

Você pode exibir representações gráficas dos seguintes itens:  

- [Resumo de informações do Marketplace](#marketplace-insights-summary)
- [Visitas à página por geografia](#page-visits-by-geography)  
- [Visitas à página versus a tendência de visitantes exclusivos](#page-visits-versus-unique-visitors-trend)
- [Plano de ação versus visitantes exclusivos com CTAs](#call-to-action-versus-unique-visitors-with-ctas)
- [Visitas à página e plano de ação por ofertas](#page-visits-and-calls-to-action-by-offers)
- [Plano de tendência de porcentagem de ação](#call-to-action-percentage-trend)
- [Visitas à página e chamadas para ação por domínios de referência](#page-visits-and-calls-to-action-by-referral-domains)
- [Tabela de detalhes do Marketplace insights](#marketplace-insights-details-table)

>[!NOTE]
> Para obter definições detalhadas de terminologia de análise, consulte [perguntas e terminologia frequentes para análise do Marketplace comercial](./faq-terminology.md).

### <a name="insights-dashboard-layout"></a>Layout do painel de informações

Você pode exibir as métricas do Marketplace de várias maneiras:

- Guias de vitrine
- Filtros de página
- Filtros de data

**Guias de vitrine**: você pode exibir as métricas de suas ofertas separadamente por meio das guias AppSource & Azure Marketplace. Selecione as ofertas na lista suspensa oferta à direita para ver uma visualização das métricas da (s) ofertas (ões) selecionadas. Por padrão, todas as ofertas são selecionadas.

![Lista suspensa oferta de painel do Partner Center insights](./media/insights-offer-dropdown.png)

**Filtros de página de informações**: esses filtros são aplicados no nível da oferta (página de detalhes do produto). Você pode selecionar vários filtros para os critérios que deseja exibir. Esse filtro se aplica a toda a seção do Marketplace insights, incluindo gráficos e detalhes.

![Filtros da página de painel do recepções do Partner Center](./media/insights-page-filter.png)

- Os nomes de oferta são listados apenas para as ofertas que têm visitas à página no intervalo de datas selecionado.  
- A seleção padrão é ' all' para cada uma das opções de filtro
- Filtros aplicados mostram a contagem de seleções para as escolhas feitas. Os filtros aplicados não serão exibidos para a seleção padrão ' todos '.

![Filtros de informações do Partner Center aplicados](./media/insights-page-filter-two.png)

**Filtros de data do insights**: Este filtro se aplica a toda a seção do Marketplace insights. Os filtros podem incluir intervalos de datas predeterminados ou um intervalo de datas personalizado.

![Filtros de data do recepções do Partner Center](./media/insights-date-range.png)

## <a name="marketplace-insights-summary"></a>Resumo de informações do Marketplace

A seção Resumo do Marketplace insights exibe uma contagem de **visitas à página**, **chamadas para ação**e **visitantes exclusivos** para o intervalo de datas selecionado.

### <a name="page-visits"></a>Visitas à página

Esse número representa a contagem de sessões de usuário distintas na página de oferta (página de detalhes do produto) para um intervalo de datas selecionado. O indicador de porcentagem vermelha/verde representa o percentual de crescimento de visitas à página. O gráfico de tendência representa a contagem de mês a mês de visitas à página.

### <a name="unique-visitors"></a>Visitantes exclusivos

Esse número representa a contagem de visitantes distinta durante o intervalo de datas selecionado para as ofertas selecionadas no filtro de página. Um visitante que visitou uma ou mais páginas de detalhes do produto será contado como um único visitante.

### <a name="call-to-action"></a>Plano de ação

Esse número representa a contagem de cliques de botão de **chamada para ação** concluídas na página de oferta (página de detalhes do produto). As **chamadas para ação** são contadas quando os botões **obter agora**, **avaliação gratuita**, **entrar em contato comigo**e **testar unidade** estão selecionados.

![Plano de informações do Partner Center para Resumo da ação](./media/insights-summary.png)

## <a name="page-visits-by-geography"></a>Visitas à página por geografia

O calor abaixo exibe a contagem de **visitas à página**, **chamadas para ação**e **visitantes exclusivos de acordo**com o país do cliente. As visitas a páginas superiores são representadas por cores de mapa mais escuras e as visitas a páginas inferiores são representadas por cores de mapa mais claras.

![Divisões geográficas do Partner Center](./media/insights-geography.png)

O calor inclui os seguintes recursos:

- O calor tem uma grade suplementar para exibir os detalhes de **visitas à página**, **chamadas para ações** e **visitantes exclusivos** em um local específico; Você pode aplicar zoom em um local específico, se preferir.  
- A **distribuição dos países** é a contagem de todos os países de onde seus clientes relataram visitas à página durante o intervalo de datas selecionado.
- Você pode pesquisar e selecionar um país na grade para ampliar o local no mapa. Reverta para a exibição original selecionando **página inicial** no mapa.

## <a name="page-visits-versus-unique-visitors-trend"></a>Visitas à página versus a tendência de visitantes exclusivos

As colunas abaixo representam a contagem de visitas de páginas mensais, que são exibidas no eixo Y (eixo no lado esquerdo do gráfico). A linha de tendência representa a tendência mensal de visitantes exclusivos, que é exibida no eixo Y secundário (eixo no lado direito do gráfico), para suas ofertas publicadas nas vitrines: Azure Marketplace e AppSource.

![Visitas à página de informações do Partner Center em comparação com a tendência de visitantes exclusivos](./media/insights-page-vists-unique-visitors.png)

## <a name="call-to-action-versus-unique-visitors-with-ctas"></a>Plano de ação versus visitantes exclusivos com CTAs

As colunas empilhadas representam chamadas mensais para ação (CTA), que são divididas por tipos CTA (**Obtenha agora**, **entre em contato comigo**e **avaliação gratuita**) e plotadas no eixo Y (eixo no lado esquerdo da página). A linha de tendência representa a tendência mensal de visitantes exclusivos com CTAs, que é exibida no eixo Y secundário (eixo no lado direito do gráfico), para suas ofertas publicadas no Azure Marketplace e AppSource.

![Plano de informações do Partner Center para ação versus visitantes exclusivos com CTAs](./media/insights-call-to-action-unique-visitors.png)

## <a name="page-visits-and-calls-to-action-by-offers"></a>Visitas a páginas e chamadas para ação por ofertas

O gráfico de pizza externo representa a divisão de **visitas à página** com base nas ofertas publicadas no Marketplace e selecionadas no filtro. O gráfico interno representa as **chamadas para** a divisão de ação das mesmas ofertas.

![Visitas à página de informações do Partner Center e chamadas para ação por ofertas](./media/insights-page-visits-and-cta-by-offer.png)

## <a name="call-to-action-percentage-trend"></a>Plano de tendência de porcentagem de ação

A **chamada para a tendência percentual de ação** apresenta o percentual de CTA para as ofertas publicadas no Marketplace. CTA% = (CTAs/página visitas) * 100.

![Plano de informações do Partner Center para tendência de porcentagem de ação](./media/insights-call-to-action-percentage-trend.png)

## <a name="page-visits-and-calls-to-action-by-referral-domains"></a>Visitas à página e chamadas para ação por domínios de referência

O gráfico a seguir apresenta os principais domínios de referência do 50. Selecionar um domínio de referência específico mostra a tendência mensal de visitas à página e chamadas para ação no gráfico à direita.

![Visitas da página de informações e chamadas para ações por domínios de referência e campanhas do Partner Center](./media/insights-page-visits-call-to-actions.png)

## <a name="marketplace-insights-details-table"></a>Tabela de detalhes do Marketplace insights

Esta tabela fornece uma exibição de lista das visitas de página e as chamadas para ação das ofertas selecionadas classificadas por data.

![Tabela de detalhes de informações do Partner Center](./media/insights-details-page.png)

- Os dados podem ser extraídos para um arquivo CSV se a contagem de registros for menor que 1000.
- Se a contagem de registros for superior a 1000, os dados exportados serão colocados de forma assíncrona em uma página de downloads para os próximos 30 dias.
- Os filtros podem ser aplicados para exibir os dados nos quais você está interessado. Os dados podem ser filtrados por nomes de oferta e de campanha.  

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral dos relatórios de análise disponíveis no Marketplace comercial do Partner Center, consulte [análise do Marketplace comercial no Partner Center](./analytics.md).
- Para grafos, tendências e valores de dados agregados que resumem a atividade do Marketplace para sua oferta, consulte [painel de resumo na análise do Marketplace comercial](./summary-dashboard.md).
- Para obter informações sobre seus pedidos em um formato gráfico e baixável, consulte [painel de pedidos na análise do Marketplace comercial](./orders-dashboard.md).
- Para VM (máquina virtual) oferece métricas de uso e cobrança limitada, consulte [painel de uso na análise do Marketplace comercial](./usage-dashboard.md).
- Para obter informações detalhadas sobre seus clientes, incluindo tendências de crescimento, consulte [painel do cliente na análise do Marketplace comercial](./customer-dashboard.md).
- Para obter uma lista de suas solicitações de download nos últimos 30 dias, consulte o [painel de downloads na análise do Marketplace comercial](./downloads-dashboard.md).
- Para ver uma exibição consolidada dos comentários dos clientes para ofertas no Azure Marketplace e no AppSource, consulte [painel classificações e revisões na análise do Marketplace comercial](./ratings-reviews.md).
- Para perguntas frequentes sobre a análise do Marketplace comercial e um dicionário abrangente de termos de dados, consulte [perguntas e terminologia frequentes para análise do Marketplace comercial](./faq-terminology.md).
