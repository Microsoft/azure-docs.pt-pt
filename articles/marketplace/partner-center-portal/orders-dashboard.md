---
title: Partner Center Orders dashboard in Commercial Marketplace analytics
description: Saiba como aceder a relatórios analíticos sobre o seu marketplace, oferecem encomendas num formato gráfico e descarregável.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: d0db6553a774a69bb8a55538cbd2b4a333be9316
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281413"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Painel de encomendas na análise do Mercado Comercial

Este artigo fornece informações sobre o **dashboard encomendas** no Partner Center. Este dashboard exibe informações sobre as suas encomendas num formato gráfico e descarregável.

Para aceder ao **dashboard De Encomendas** nas ferramentas de análise do Partner Center, abra o dashboard **[Analyze](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** no âmbito do Mercado Comercial.

>[!NOTE]
> Para definições detalhadas de terminologia analítica, consulte [perguntas frequentes e terminologia para análise](./faq-terminology.md)do Mercado Comercial.

## <a name="orders-dashboard"></a>Dashboard de pedidos

O **dashboard Encomendas** do menu **Analyze** exibe as encomendas atuais para todas as suas ofertas SaaS. Pode ver representações gráficas dos seguintes itens:

- [Resumo da ordem](#order-summary)
- [Encomendas por geografia](#orders-by-geography)
- [Encomendas por ofertas](#orders-by-offers)
- [Tendência de encomendas por site versus por assento](#orders-trend-per-site-versus-per-seat)
- [Encomendas por SKUs](#orders-by-skus)
- [Tendência de encomendas e lugares](#orders-and-seats-trend)
- [Tabela de detalhes de encomenda](#order-details-table)

> [!NOTE]
> Existem diferenças entre a forma como os relatórios de análise apresentam no Portal dos Parceiros da Nuvem (CPP) e o novo programa de Marketplace Comercial no Partner Center. Uma forma específica é que o **Seller Insights** em CPP tem um separador **de utilização & Encomendas,** que apresenta dados para ofertas baseadas na utilização e ofertas não baseadas em uso. No Partner Center, a página **Encomendas** tem um separador para Ofertas SaaS.

## <a name="order-dashboard-details"></a>Encomende detalhes do dashboard

Esta secção descreve os relatórios de análise mais detalhadamente.

### <a name="order-summary"></a>Resumo da encomenda

A secção Resumo da Encomenda apresenta uma contagem de todas as encomendas adquiridas (excluindo encomendas canceladas), encomendas canceladas e lugares.

O valor percentual próximo do Total de Encomendas representa a quantidade de crescimento da gama de datas selecionada.

![Resumo da ordem do Partner Center Analise](./media/order-summary.png)

- Um triângulo verde apontando para cima indica uma tendência de crescimento positiva.
- Um triângulo vermelho que aponta para baixo indica uma tendência de crescimento negativa em relação ao mês anterior.
- As tendências de crescimento são representadas por gráficos de micro barras. Pode mostrar o valor por cada mês pairando sobre as colunas dentro da tabela.
- As encomendas canceladas são uma contagem de encomendas que foram previamente adquiridas e depois canceladas durante o intervalo de datas selecionadas.
- Os assentos são uma contagem de lugares criados durante a gama de datas selecionadas.

### <a name="orders-by-geography"></a>Encomendas por geografia

As Encomendas por mapa de **calor de geografia** exibem uma contagem das suas encomendas num mapa mundial e mostram lugares mapeados com base no País do Cliente. Este mapa de calor funciona da mesma forma que o **[Cliente por mapa de calor de geografia.](./customer-dashboard.md#customer-by-geography)**

![Partner Center Analisar encomendas por geografia](./media/orders-by-geography.png)

### <a name="orders-by-offers"></a>Encomendas por ofertas

As **Encomendas por ofertas** do nut graph organizam encomendas (incluindo encomendas canceladas) de acordo com os seus nomes de oferta.

- As ofertas superiores são apresentadas no gráfico e as restantes ofertas são agruparadas como 'Rest All'.
- Pode selecionar ofertas específicas na legenda para exibir apenas as ofertas no gráfico.
- Pairar sobre uma fatia no gráfico mostrará o número de encomendas e percentagem dessa oferta em comparação com o seu número total de encomendas em todas as ofertas.
- As **encomendas por ofertas de tendência** mostram tendências de crescimento mensais. A coluna mensal representa o número de encomendas por nome de oferta. O gráfico de linha mostra a tendência percentual de crescimento traçada num eixo z.
- Pode utilizar o slider na parte superior da tabela para rolar para a direita e para a esquerda ao longo do eixo x e concentrar-se em pontos de dados específicos.
- Pode exibir o gráfico de tendências selecionando um item específico na legenda.
- Também pode optar por apresentar tendências e dados para **encomendas canceladas.** O gráfico funcionará da mesma forma que as encomendas por gráfico de **ofertas.**

### <a name="orders-trend-per-site-versus-per-seat"></a>Encomendas Tendência por site versus por assento

O gráfico de donut **por site versus assento** representa a desagregação de por site SaaS e por assento As encomendas SaaS compradas pelos clientes (este gráfico inclui encomendas canceladas). O gráfico de colunas representa a tendência de por site SaaS e por assento as encomendas SaaS compradas pelos clientes (este gráfico inclui encomendas canceladas).

### <a name="orders-by-skus"></a>Encomendas por SKUs

O gráfico de **Encomendas por SKUs** representa a tendência das encomendas ao nível da Unidade de Armazenamento de Stock (SKU) para todas as suas ofertas (isto inclui encomendas canceladas). O gráfico de donuts representa a desagregação das cinco principais encomendas SKU e o gráfico de colunas representa a tendência das encomendas para os cinco principais SKUs.

### <a name="orders-and-seats-trend"></a>Tendência encomendas e assentos

O gráfico de tendências de **encomendas e assentos** apresenta as 50 melhores ofertas com o maior número de encomendas. Estes são exibidos num quadro de líderes e são classificados pela maior contagem de encomendas e percentagem de encomendas.

- **Encomendas por SKUs**: Selecione uma oferta para visualizar a contagem de encomendas para os cinco melhores SKUs da tabela.
- **Assentos por SKUs**: A tendência mensal de lugares para os cinco melhores SKUs. Se a oferta que selecionar não for uma oferta por assento, não verá quaisquer dados nesta tabela de área.

### <a name="canceled-orders-by-offers"></a>Pedidos cancelados por ofertas

As **encomendas canceladas por ofertas** de gráfico de tarteorganizatodas as suas encomendas canceladas de acordo com os nomes das suas ofertas. As principais ofertas são exibidas no gráfico e as restantes ofertas são agruparadas como "Rest All". Pode selecionar ofertas específicas na legenda para exibir no gráfico.

- Pairar sobre uma fatia no gráfico mostrará o número de encomendas e a percentagem da oferta selecionada em comparação com o número total de encomendas em todas as ofertas.
- O gráfico da coluna apresenta tendências mensais. As colunas representam o número de encomendas canceladas pelo nome da oferta. Pode utilizar o slider em cima da tabela para rolar para a direita e para a esquerda ao longo do eixo x e concentrar-se em pontos de dados específicos. Pode exibir o gráfico de tendências selecionando um item específico na legenda.

### <a name="order-details-table"></a>Tabela de detalhes de encomenda

A tabela de detalhes da Ordem apresenta uma lista numerada das 1000 encomendas de topo ordenadas por data de aquisição.

- Cada coluna na grelha é ordenada.
- Os dados podem ser extraídos para um ficheiro TSV se a contagem dos registos for inferior a 1000.
- Se os registos forem superiores a 1000, os dados exportados serão colocados assincronicamente numa página de downloads durante os próximos 30 dias.
- Os filtros podem ser aplicados na **tabela de detalhes** da Encomenda para mostrar apenas os dados que lhe interessam. Os dados podem ser filtrados pelo Country, do tipo de licença Azure, do tipo de licença marketplace, do tipo de oferta, do estado da Encomenda, dos trilhos gratuitos, do ID de subscrição do Marketplace, do ID do cliente e do nome da Empresa.

#### <a name="orders-page-filters"></a>Encomendas Filtros de página

Estes filtros são aplicados ao nível da página.

Pode selecionar vários filtros para tornar o gráfico pelos critérios que escolher visualizar e os dados que pretende visualizados na grelha/exportação de Dados de **Encomendas Detalhadas.** Os filtros são aplicados nos dados extraídos para a gama de dados selecionada no canto superior direito da página de encomendas.

- Os tipos de oferta e os nomes de oferta estão listados apenas para ofertas que você tem encomendas durante o intervalo de datas selecionados. Os nomes de oferta na lista são apresentados para ofertas que selecionou na lista.
- Os filtros aplicados mostram as métricas totais dentro de cada s seleção para cada filtro selecionado. Os filtros aplicados não são apresentados quando a seleção predefinida é escolhida.
- Se **Tudo** estiver selecionado para uma das listas de abandono, todas as métricas da página selecionada serão agregadas. Por exemplo: "All" na opção de filtro de tipos de oferta significa que todos os tipos de oferta foram selecionados. Esta é a seleção padrão para as listas de abandono. Os ecrãs de filtros aplicados não mostram nada quando **tudo** é selecionado.
- **Seleção de vários valores**: Todas as métricas da página serão agregadas para todas as seleções feitas no âmbito da lista de abandono. Se forem feitas várias seleções, o filtro aplicado mostrará a contagem de todas as seleções feitas. Veja a imagem abaixo para referência.

    ![Partner Center Analisar ordem com vários valores aplicados ao filtro](./media/filters-applied.png)

- **Seleção**de valor único : Se for selecionado um valor, o filtro aplicado mostrará a contagem do filtro que foi selecionado. Veja abaixo a imagem para referência.

     ![Partner Center Analisar a ordem com um valor único aplicado ao filtro](./media/filters-applied-single.png)

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral dos relatórios de análise disponíveis no Partner Center Commercial Marketplace, consulte [o Analytics para o Mercado Comercial no Partner Center](./analytics.md).
- Para gráficos, tendências e valores de dados agregados que resumam a atividade do mercado para a sua oferta, consulte o [dashboard Sumário na análise do Mercado Comercial.](./summary-dashboard.md)
- Para máquina virtual (VM) oferece métricas de utilização e faturação medida, consulte [o dashboard de utilização na análise do Mercado Comercial](./usage-dashboard.md).
- Para obter informações detalhadas sobre os seus clientes, incluindo tendências de crescimento, consulte o [dashboard do Cliente na análise do Mercado Comercial.](./customer-dashboard.md)
- Para obter uma lista dos seus pedidos de descarregamento ao longo dos últimos 30 dias, consulte [downloads dashboard na análise do Mercado Comercial](./downloads-dashboard.md).
- Para ver uma visão consolidada do feedback dos clientes para as ofertas no Azure Marketplace e appSource, consulte [ratings e análises dashboard na análise](./ratings-reviews.md)do Mercado Comercial.
- Para perguntas frequentes sobre análise do Mercado Comercial e para um dicionário abrangente de termos de dados, consulte [frequentemente perguntas e terminologia para análise](./faq-terminology.md)do Mercado Comercial.
