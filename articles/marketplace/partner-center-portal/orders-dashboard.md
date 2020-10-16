---
title: Partner Center Orders dashboard em analytics marketplace comercial, Microsoft AppSource e Azure Marketplace
description: Saiba como aceder a relatórios analíticos sobre o seu mercado comercial oferece encomendas num formato gráfico e transferível.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/22/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: d5adc1bfe19de48568d0e77bb488bea0e5a02818
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87327383"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Dashboard de encomendas na análise do mercado comercial

Este artigo fornece informações sobre o **dashboard encomendas** no Partner Center. Este dashboard exibe informações sobre as suas encomendas num formato gráfico e transferível.

Para aceder ao **dashboard encomendas** nas ferramentas de análise do Partner Center, abra o painel de controlo de **[análise no](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** Mercado Comercial.

>[!NOTE]
> Para definições detalhadas de terminologia analítica, consulte [perguntas frequentes e terminologia para análise de mercado comercial.](./faq-terminology.md)

## <a name="orders-dashboard"></a>Dashboard de pedidos

O **painel de encomendas** do menu **'Encomendas'** apresenta as encomendas atuais para todas as suas ofertas SaaS. Pode ver as representações gráficas dos seguintes itens:

- [Resumo da encomenda](#order-summary)
- [Encomendas por geografia](#orders-by-geography)
- [Encomendas por ofertas](#orders-by-offers)
- [Tendência de encomendas por site versus por assento](#orders-trend-per-site-versus-per-seat)
- [Encomendas por planos](#orders-by-plans)
- [Tendência de encomendas e assentos](#orders-and-seats-trend)
- [Tabela de detalhes do pedido](#order-details-table)

A latência máxima entre a criação e reportagem de encomendas no Partner Center é de 48 horas.

## <a name="order-dashboard-details"></a>Encomende detalhes do painel de instrumentos

Esta secção descreve os relatórios de análise com mais detalhes.

### <a name="order-summary"></a>Resumo da Encomenda

A secção Resumo da Ordem apresenta uma contagem de todas as encomendas adquiridas (excluindo encomendas canceladas), encomendas canceladas e lugares.

O valor percentual ao lado das Ordens Totais representa a quantidade de crescimento da gama de datas selecionadas.

![Resumo da ordem do Centro Parceiro](./media/order-summary.png)

- Um triângulo verde apontando para cima indica uma tendência de crescimento positiva.
- Um triângulo vermelho que aponta para baixo indica uma tendência de crescimento negativa em relação ao mês anterior.
- As tendências de crescimento são representadas por gráficos de micro barras. Pode mostrar o valor de cada mês pairando sobre as colunas dentro da tabela.
- As encomendas canceladas são uma contagem de encomendas que foram previamente compradas e depois canceladas durante o intervalo de datas selecionadas.
- Os lugares são uma contagem de lugares criados durante o intervalo de datas selecionados.

### <a name="orders-by-geography"></a>Encomendas por geografia

As Encomendas por mapa térmico **de geografia** exibem uma contagem das suas encomendas num mapa mundial e mostram lugares mapeados com base no País/Região do Cliente. Este mapa de calor funciona da mesma forma que o Cliente através do **[mapa de calor da geografia.](./customer-dashboard.md#customer-by-geography)**

![Parceiro Centro Analisar encomendas por geografia](./media/orders-by-geography.png)

### <a name="orders-by-offers"></a>Encomendas por ofertas

As **Encomendas por ofertas** de gráfico de donut organiza encomendas (incluindo encomendas canceladas) de acordo com os nomes da sua oferta.

- As ofertas de topo são apresentadas no gráfico e as restantes ofertas são agrupadas como 'Rest All'.
- Pode selecionar ofertas específicas na legenda para exibir apenas essas ofertas no gráfico.
- Pairar sobre uma fatia no gráfico mostra o número de encomendas e percentagem dessa oferta em comparação com o seu número total de encomendas em todas as ofertas.
- As **encomendas por ofertas de tendência** exibem tendências de crescimento mensais. A coluna mensal representa o número de encomendas pelo nome de oferta. O gráfico de linha apresenta a tendência de crescimento percentual traçada num eixo z.
- Pode utilizar o slider no topo da tabela para deslocar para a direita e para a esquerda ao longo do eixo X e concentrar-se em pontos de dados específicos.
- Pode apresentar o gráfico de tendências selecionando um item específico na legenda.
- Também pode optar por exibir tendências e dados para **encomendas canceladas.** O gráfico funcionará da mesma forma que as encomendas por gráfico **de ofertas.**

### <a name="orders-trend-per-site-versus-per-seat"></a>Tendência de encomendas por site versus por assento

O gráfico **de donuts por local versus por assento** representa a desagregação das encomendas SaaS por local e por assento SaaS compradas pelos clientes (este gráfico inclui encomendas canceladas). O gráfico de colunas representa a tendência de por site SaaS e por assento As encomendas SaaS compradas pelos clientes (este gráfico inclui encomendas canceladas).

### <a name="orders-by-plans"></a>Encomendas por planos

O gráfico **de Encomendas por planos** representa a tendência das encomendas ao nível do plano para todas as suas ofertas (isto inclui encomendas canceladas). O gráfico de donuts representa a desagregação das cinco principais encomendas de planos e o gráfico da coluna representa a tendência das encomendas para os cinco primeiros planos.

### <a name="orders-and-seats-trend"></a>Tendência encomendas e assentos

O gráfico **de tendências de encomendas e lugares** apresenta as 50 melhores ofertas com o maior número de encomendas. Estes são exibidos num quadro de líderes e classificados pela maior percentagem de pedidos e pedidos.

- **Encomendas por planos** – Selecione uma oferta para ver a contagem decrescente para os cinco melhores planos da tabela.
- **Lugares por planos** – A tendência mensal dos lugares para os cinco primeiros planos. Se a oferta selecionada não for uma oferta por assento, nenhum dado será apresentado aqui.

### <a name="canceled-orders-by-offers"></a>Encomendas canceladas por ofertas

As **encomendas canceladas por ofertas** de gráfico de tartes organizam todas as suas encomendas canceladas de acordo com os nomes da oferta. As ofertas de topo são apresentadas no gráfico e as restantes ofertas são agrupadas como "Rest All". Pode selecionar ofertas específicas na legenda para exibir no gráfico.

- Pairar sobre uma fatia no gráfico exibe o número de encomendas e percentagem da oferta selecionada em comparação com o número total de encomendas em todas as ofertas.
- O gráfico da coluna apresenta tendências mensais. As colunas representam o número de encomendas canceladas pelo nome de oferta. Pode utilizar o slider no topo da tabela para deslocar para a direita e para a esquerda ao longo do eixo X e concentrar-se em pontos de dados específicos. Pode apresentar o gráfico de tendências selecionando um item específico na legenda.

### <a name="order-details-table"></a>Tabela de detalhes do pedido

A tabela de detalhes da Ordem apresenta uma lista numerada das 1000 principais encomendas classificadas por data de aquisição.

- Cada coluna da grelha é ordenada.
- Os dados podem ser extraídos para um ficheiro TSV se a contagem dos registos for inferior a 1000.
- Se os registos forem mais de 1000, os dados exportados serão colocados de forma assíncronea numa página de downloads durante os próximos 30 dias.
- Aplique filtros na **tabela de detalhes** da Ordem para exibir apenas os dados que lhe interessam. Filtrar por País/Região, Tipo de licença Azure, tipo de licença de mercado comercial, tipo de oferta, estado de encomenda, trilhos gratuitos, ID de assinatura de mercado comercial, ID do cliente e nome da Empresa.
- Como o SaaS oferece adquirido através do Azure Marketplace ou appSource, não requer uma Subscrição Azure, o ID de subscrição do Marketplace aparecerá como 0000000-0000-0000-0000-000000000000000000000000 na secção **de dados de encomendas detalhadas.**

#### <a name="orders-page-filters"></a>Pedidos Filtros de página

Estes filtros são aplicados ao nível da página.

Pode selecionar vários filtros para tornar o gráfico para os critérios que escolhe visualizar e os dados que pretende exibidos na grelha de dados/exportação de dados de **encomenda detalhada.** Os filtros são aplicados nos dados extraídos para a gama de dados que selecionou no canto superior direito da página das encomendas.

- Os tipos de oferta e os nomes de oferta estão listados apenas para ofertas para as suas encomendas durante o intervalo de datas selecionados. Os nomes da lista são apresentados para os tipos de ofertas que selecionou na lista.
- Os filtros aplicados mostram as métricas totais dentro de cada seleção(s) para cada filtro selecionado. Os filtros aplicados não são apresentados quando a seleção predefinida é escolhida.
- Se **Todos forem** selecionados para uma das listas de dropdown, então todas as métricas na página selecionada serão agregadas. Por exemplo: "Todos" na opção de filtragem dos tipos de oferta significa que todos os tipos de oferta foram selecionados. Esta é a seleção padrão para as listas de abandono. Os filtros aplicados não mostram nada quando **todos são** selecionados.
- **Seleção de valor múltiplo**: Todas as métricas da página serão agregadas para todas as seleções feitas na lista de dropdown. Se forem feitas várias seleções, o filtro aplicado mostrará a contagem de todas as seleções feitas. Consulte a imagem abaixo para referência.

    ![Parceiro Centro Analisar ordem com vários valores aplicados para filtrar](./media/filters-applied.png)

- **Seleção de valor único**: Se um valor for selecionado, o filtro aplicado mostrará a contagem do filtro que foi selecionado. Veja a imagem abaixo para referência.

     ![Ordem de Análise do Centro parceiro com valor único aplicado ao filtro](./media/filters-applied-single.png)

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral dos relatórios de análise disponíveis no mercado comercial do Partner Center, consulte [Analytics para o mercado comercial no Partner Center](./analytics.md).
- Para gráficos, tendências e valores de dados agregados que resumem a atividade do mercado para a sua oferta, consulte [o painel Sumário na análise do mercado comercial.](./summary-dashboard.md)
- Para Máquina Virtual (VM) oferece métricas de utilização e faturação medido, consulte [o painel de uso em análise de mercado comercial.](./usage-dashboard.md)
- Para obter informações detalhadas sobre os seus clientes, incluindo tendências de crescimento, consulte [o painel de clientes em análise de mercado comercial.](./customer-dashboard.md)
- Para obter uma lista dos seus pedidos de descarregamento nos últimos 30 dias, consulte [downloads dashboard em análise de mercado comercial.](./downloads-dashboard.md)
- Para ver uma visão consolidada do feedback dos clientes para as ofertas no Azure Marketplace e appSource, consulte [ratings e comentários no painel de análise de mercado comercial.](./ratings-reviews.md)
- Para perguntas frequentes sobre análise de mercado comercial e para um dicionário abrangente de termos de dados, consulte [perguntas frequentes e terminologia para análise de mercado comercial.](./faq-terminology.md)
