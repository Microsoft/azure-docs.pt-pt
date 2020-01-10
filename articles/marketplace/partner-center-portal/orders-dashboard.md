---
title: Painel de pedidos do Partner Center na análise do Marketplace comercial
description: Saiba como acessar relatórios analíticos sobre seus pedidos de oferta do Marketplace em um formato gráfico e baixável.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: bf4b48fafa5b877053abe653b569cf27eb50d57b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75481064"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Painel de pedidos na análise do Marketplace comercial

Este artigo fornece informações sobre o **painel de pedidos** no Partner Center. Esse painel exibe informações sobre seus pedidos em um formato gráfico e baixável.

Para acessar o **painel pedidos** nas ferramentas de análise do Partner Center, abra o **[painel analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** em Marketplace comercial.

>[!NOTE]
> Para obter definições detalhadas de terminologia de análise, consulte [perguntas e terminologia frequentes para análise do Marketplace comercial](./faq-terminology.md).

## <a name="orders-dashboard"></a>Dashboard de pedidos

O **painel Orders** do menu **analisar** exibe os pedidos atuais para todas as suas ofertas de SaaS. Você pode exibir representações gráficas dos seguintes itens:

- [Resumo do pedido](#order-summary)
- [Pedidos por geografia](#orders-by-geography)
- [Pedidos por ofertas](#orders-by-offers)
- [Tendência de pedidos por site versus por estação](#orders-trend-per-site-versus-per-seat)
- [Pedidos por SKUs](#orders-by-skus)
- [Tendência de pedidos e estações](#orders-and-seats-trend)
- [Tabela de detalhes do pedido](#order-details-table)

> [!NOTE]
> Há diferenças entre o modo como os relatórios de análise são exibidos no Portal do Cloud Partner (CPP) e no novo programa do Marketplace comercial no Partner Center. Uma maneira específica é que o **vendedor insights** em cpp tem uma guia **Orders & Usage** , que exibe dados para ofertas baseadas em uso e ofertas não baseadas em uso. No Partner Center, a página **pedidos** tem uma guia separada para ofertas de SaaS.

## <a name="order-dashboard-details"></a>Detalhes do painel do pedido

Esta seção descreve os relatórios de análise em mais detalhes.

### <a name="order-summary"></a>Resumo do pedido

A seção Resumo do pedido exibe uma contagem de todos os pedidos comprados (excluindo pedidos cancelados), pedidos cancelados e estações.

O valor percentual ao lado de pedidos totais representa a quantidade de crescimento do intervalo de datas selecionado.

![Resumo do pedido de análise do Partner Center](./media/order-summary.png)

- Um triângulo verde apontando para cima indica uma tendência de crescimento positivo.
- Um triângulo vermelho apontando para baixo indica uma tendência de crescimento negativo relativa ao mês anterior.
- As tendências de crescimento são representadas por gráficos micro bar. Você pode exibir o valor de cada mês passando o mouse sobre as colunas dentro do gráfico.
- Pedidos cancelados são uma contagem de pedidos que foram comprados anteriormente e cancelados durante o intervalo de datas selecionado.
- Estações são uma contagem de estações criadas durante o intervalo de datas selecionado.

### <a name="orders-by-geography"></a>Pedidos por geografia

O calor dos **pedidos por geografia** exibe uma contagem de seus pedidos em um mapa mundial e mostra os assentos mapeados com base no país do cliente. Esse calor funciona da mesma forma que o **[cliente pela geografia calor](./customer-dashboard.md#customer-by-geography)** .

![Partner Center analisar pedidos por geografia](./media/orders-by-geography.png)

### <a name="orders-by-offers"></a>Pedidos por ofertas

Os **pedidos por oferece** gráficos de rosca organizam ordens (incluindo pedidos cancelados) de acordo com seus nomes de oferta.

- As principais ofertas são exibidas no grafo e o restante das ofertas é agrupado como ' Rest all'.
- Você pode selecionar ofertas específicas na legenda para exibir somente as ofertas no grafo.
- Passar o mouse sobre uma fatia no grafo exibirá o número de pedidos e a porcentagem dessa oferta, em comparação com o número total de pedidos em todas as ofertas.
- Os **pedidos por ofertas de tendência** exibem as tendências de crescimento mês a mês. A coluna mês representa o número de pedidos por nome de oferta. O gráfico de linhas exibe a tendência de percentual de crescimento plotada em um eixo z.
- Você pode usar o controle deslizante na parte superior do gráfico para rolar para a direita e para a esquerda ao longo do eixo x e se concentrar em pontos de dados específicos.
- Você pode exibir o gráfico de tendência selecionando um item específico na legenda.
- Você também pode optar por exibir tendências e dados para **pedidos cancelados**. O grafo funcionará da mesma maneira que o grafo **Orders by offers** .

### <a name="orders-trend-per-site-versus-per-seat"></a>Tendência de pedidos por site versus por estação

O gráfico de rosca **por site versus por estação** representa a divisão de SaaS por site e pedidos de SaaS por estação adquiridos pelos clientes (este gráfico inclui pedidos cancelados). O gráfico de colunas representa a tendência de SaaS por site e pedidos de SaaS por estação adquiridos pelos clientes (este gráfico inclui pedidos cancelados).

### <a name="orders-by-skus"></a>Pedidos por SKUs

O gráfico **pedidos por SKUs** representa a tendência de pedidos no nível de SKU (unidade de manutenção de estoque) para todas as suas ofertas (isso inclui pedidos cancelados). O gráfico de rosca representa a divisão das cinco principais ordens de SKU e o gráfico de colunas representa a tendência de pedidos para as cinco SKUs principais.

### <a name="orders-and-seats-trend"></a>Tendência de pedidos e estações

O gráfico de **tendência de pedidos e estações** apresenta as principais ofertas de 50 com o maior número de pedidos. Eles são exibidos em um tabuleiro de preenchimento e são classificados por contagem de pedidos e porcentagem de pedidos mais altos.

- **Pedidos por SKUs**: selecione uma oferta para exibir a divisão da contagem de pedidos das cinco principais SKUs no gráfico.
- **Estações por SKUs**: a tendência mensal de estações para as cinco SKUs principais. Se a oferta selecionada não for uma oferta por estação, você não verá nenhum dado nesse gráfico de área.

### <a name="canceled-orders-by-offers"></a>Pedidos cancelados por ofertas

As **ordens canceladas por ofertas** de gráfico de pizza organizam todas as suas ordens canceladas de acordo com seus nomes de oferta. As principais ofertas são exibidas no grafo e o restante das ofertas é agrupado como "Rest". Você pode selecionar ofertas específicas na legenda para exibir no grafo.

- Passar o mouse sobre uma fatia no grafo exibirá o número de pedidos e a porcentagem da oferta selecionada, em comparação com o número total de pedidos em todas as ofertas.
- O gráfico de colunas exibe as tendências mês a mês. As colunas representam o número de pedidos cancelados por nome de oferta. Você pode usar o controle deslizante na parte superior do gráfico para rolar para a direita e para a esquerda ao longo do eixo x e se concentrar em pontos de dados específicos. Você pode exibir o gráfico de tendência selecionando um item específico na legenda.

### <a name="order-details-table"></a>Tabela de detalhes do pedido

A tabela detalhes do pedido exibe uma lista numerada dos 1000 principais pedidos classificados por data de aquisição.

- Cada coluna na grade é classificável.
- Os dados podem ser extraídos para um arquivo TSV se a contagem dos registros for menor que 1000.
- Se o número de registros acima de 1000, os dados exportados serão colocados de forma assíncrona em uma página de downloads para os próximos 30 dias.
- Os filtros podem ser aplicados à **tabela detalhes do pedido** para exibir apenas os dados nos quais você está interessado. Os dados podem ser filtrados por país, tipo de licença do Azure, tipo de licença do Marketplace, tipo de oferta, status do pedido, trilhas gratuitas, ID da assinatura do Marketplace, ID do cliente e nome da empresa.

#### <a name="orders-page-filters"></a>Filtros de página de pedidos

Esses filtros são aplicados no nível da página.

Você pode selecionar vários filtros para renderizar o gráfico para os critérios escolhidos para exibição e os dados que deseja exibir na grade de **dados de ordem detalhada** /exportar. Os filtros são aplicados nos dados extraídos para o intervalo de dados selecionado no canto superior direito da página pedidos.

- Os tipos de oferta e os nomes de oferta são listados apenas para ofertas das quais você tem pedidos durante o intervalo de datas selecionado. Os nomes de oferta na lista são exibidos para os tipos de ofertas que você selecionou na lista.
- Filtros aplicados mostram as métricas totais dentro de cada seleção para cada filtro selecionado. Os filtros aplicados não são exibidos quando a seleção padrão é escolhida.
- Se **tudo** estiver selecionado para uma das listas suspensas, todas as métricas na página selecionada serão agregadas. Por exemplo: "todos" na opção de filtro tipos de oferta significa que todos os tipos de oferta foram selecionados. Essa é a seleção padrão para as listas suspensas. Filtros aplicados exibe não mostrará nada **quando for** selecionado.
- **Seleção de vários valores**: todas as métricas na página serão agregadas para todas as seleções feitas na lista suspensa. Se várias seleções forem feitas, o filtro aplicado mostrará a contagem de todas as seleções feitas. Consulte a imagem abaixo para obter referência.

    ![Ordem de análise do Partner Center com vários valores aplicados ao filtro](./media/filters-applied.png)

- **Seleção de valor único**: se um valor for selecionado, o filtro aplicado mostrará a contagem de um filtro selecionado. Consulte a imagem abaixo para obter referência.

     ![Ordem de análise do Partner Center com valor único aplicado ao filtro](./media/filters-applied-single.png)

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral dos relatórios de análise disponíveis no Marketplace comercial do Partner Center, consulte [análise do Marketplace comercial no Partner Center](./analytics.md).
- Para grafos, tendências e valores de dados agregados que resumem a atividade do Marketplace para sua oferta, consulte [painel de resumo na análise do Marketplace comercial](./summary-dashboard.md).
- Para VM (máquina virtual) oferece métricas de uso e cobrança limitada, consulte [painel de uso na análise do Marketplace comercial](./usage-dashboard.md).
- Para obter informações detalhadas sobre seus clientes, incluindo tendências de crescimento, consulte [painel do cliente na análise do Marketplace comercial](./customer-dashboard.md).
- Para obter uma lista de suas solicitações de download nos últimos 30 dias, consulte o [painel de downloads na análise do Marketplace comercial](./downloads-dashboard.md).
- Para ver uma exibição consolidada dos comentários dos clientes para ofertas no Azure Marketplace e no AppSource, consulte [painel classificações e revisões na análise do Marketplace comercial](./ratings-reviews.md).
- Para perguntas frequentes sobre a análise do Marketplace comercial e um dicionário abrangente de termos de dados, consulte [perguntas e terminologia frequentes para análise do Marketplace comercial](./faq-terminology.md).
