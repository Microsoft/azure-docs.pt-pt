---
title: Painel de resumo para análise do Partner Center no Mercado Comercial
description: Saiba como aceder a gráficos, tendências e valores de dados agregados que resumem a atividade do mercado a partir do painel Sumário no Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: c02ba12c790d745904d241f121e269aac3ed12f6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462986"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Dashboard de resumo na análise do mercado comercial

Este artigo fornece informações sobre o dashboard Sumário no Centro de Parceiros. Este dashboard exibe gráficos, tendências e valores de dados agregados que resumem a atividade do mercado para as suas ofertas.

Para aceder ao painel de resumo no Partner Center, no **Mercado Comercial** selecione **[Analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **Resumo**.

>[!NOTE]
> Para definições detalhadas de terminologia analítica, consulte [terminologia de análise de mercado comercial e questões comuns.](./analytics-faq.md)

## <a name="summary-dashboard"></a>Dashboard de resumo

O painel De resumo apresenta uma visão geral do Azure Marketplace e o Microsoft AppSource oferece o desempenho do negócio. O painel de instrumentos fornece uma visão geral do seguinte:

- Encomendas dos clientes
- Clientes
- Utilização dos clientes das ofertas
- Visitas de página de clientes no Azure Marketplace e AppSource

## <a name="elements-of-the-summary-dashboard"></a>Elementos do painel de resumo

As secções seguintes descrevem como utilizar o painel de resumo e como ler os dados.

### <a name="month-range"></a>Intervalo mensal

Pode encontrar uma seleção de mês no canto superior direito de cada página. Personalize a saída dos gráficos da página **Resumo** selecionando uma gama de meses com base nos últimos 3, 6 ou 12 meses, ou selecionando uma gama de meses personalizada com uma duração máxima de 12 meses. A gama de mês predefinido (período de computação) é de seis meses.

:::image type="content" source="./media/summary-dashboard/summary-dashboard.png" alt-text="Ilustra as opções de gama mensal no painel de resumo.":::

> [!NOTE]
> Todas as métricas nos widgets de visualização e relatórios de exportação honram o período de cálculo selecionado pelo utilizador.

### <a name="orders-widget"></a>Widget de encomendas

O widget Orders no painel Resumo** apresenta as encomendas atuais para todas as suas ofertas baseadas em transações. O widget Orders apresenta uma contagem e tendência de todas as encomendas adquiridas (excluindo encomendas canceladas) para o período de cálculo selecionado. O valor percentual **As encomendas** representam a quantidade de crescimento durante o período de cálculo selecionado.

[![Ilustra o widget Encomendas no painel de resumo.](./media/summary-dashboard/orders-widget.png)](./media/summary-dashboard/orders-widget.png#lightbox)


Também pode ir ao relatório Encomendas selecionando o link **'Dashboard' de encomendas** no canto inferior esquerdo do widget.

### <a name="customers-widget"></a>Widget de clientes

O widget **cliente** do painel **sumário** exibe o número total de clientes que adquiriram as suas ofertas para o período de cálculo selecionado. O widget cliente apresenta uma contagem e tendência do número total de clientes ativos (incluindo novos e existentes) (excluindo clientes agitados) para o período de cálculo selecionado. O valor percentual em **Clientes** representa a quantidade de crescimento durante o período de cálculo selecionado.

[![Ilustra o widget dos clientes no painel de resumo.](./media/summary-dashboard/customers-widget.png)](./media/summary-dashboard/customers-widget.png#lightbox)

Também pode ir ao relatório detalhado dos Clientes selecionando o link do **dashboard clientes** no canto inferior esquerdo do widget.

### <a name="usage-widget"></a>Widget de utilização

O widget de **utilização** do painel **de instrumentos Sumário** representa as horas de utilização total normalizadas e cruas para todas as ofertas da Azure Virtual Machine (VM). O widget Usage apresenta uma contagem e tendência de horas de utilização totais para o período de cálculo selecionado.

A tabela de resumo de utilização exibe as horas de utilização do cliente para todas as ofertas que adquiriram.

- As horas de utilização normalizadas são definidas como as horas de utilização normalizadas para explicar o número de núcleos VM ([número de núcleos VM] x [horas de utilização bruta]). Os VMs designados como "SHAREDCORE" utilizam 1/6 (ou 0,1666) como multiplicador [número de núcleos VM].
- As horas de utilização brutas são definidas como a quantidade de tempo que os VMs têm sido em execução em termos de horas.

O valor percentual abaixo do total das horas de utilização representa a quantidade de crescimento das horas de utilização durante o período de cálculo selecionado.

[![Ilustra o widget de utilização no painel de instrumentos de resumo.](./media/summary-dashboard/usage-widget.png)](./media/summary-dashboard/usage-widget.png#lightbox)

Também pode ir ao relatório De utilização selecionando o link **do painel de utilização** no canto inferior esquerdo do widget.

### <a name="marketplace-insights"></a>Insights do mercado

O Marketplace Insights mostra o número de utilizadores que visitaram as páginas das suas ofertas no Azure Marketplace e no AppSource. **A contagem** de visitas de página mostra um resumo da análise web do marketplace comercial que permite aos editores medir o envolvimento do cliente nas respetivas páginas de detalhes do produto listadas nas lojas online do mercado comercial: Microsoft AppSource e Azure Marketplace. Este widget apresenta uma contagem e tendência de visitas totais de página durante o período de cálculo selecionado.

[![Ilustra o widget de contagem de visitas da página no painel de instrumentos de resumo.](./media/summary-dashboard/page-visit-count.png)](./media/summary-dashboard/page-visit-count.png#lightbox)

Também pode ir ao relatório Marketplace Insights selecionando o link do **painel de insights do Marketplace** no canto inferior esquerdo do widget.

### <a name="geographical-spread"></a>Propagação geográfica

Para o período de cálculo selecionado, o mapa de calor apresenta o número total de clientes, encomendas e horas de utilização normalizadas contra a dimensão geográfica.

:::image type="content" source="./media/summary-dashboard/geo-spread.png" alt-text="Ilustra o widget países espalhados no painel de resumo.":::

Tenha em atenção o seguinte:

- Pode mover o mapa para ver a localização exata.
- Pode aproximar-se de um local específico.
- O mapa de calor tem uma grelha suplementar para ver os detalhes da contagem de clientes, contagem de encomendas e horas de utilização normalizadas para a localização específica.
- Pode pesquisar e selecionar um país/região na grelha para ampliar a localização no mapa. Reverta para a vista original selecionando o botão **Home** no mapa.

> [!TIP]
> Pode utilizar o ícone de descarregamento no canto superior direito de qualquer widget para descarregar os dados. Pode fornecer feedback sobre cada um dos widgets selecionando o ícone "polegares para cima" ou "polegares para baixo".

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral dos relatórios de análise disponíveis no mercado comercial, consulte os [relatórios analíticos do Access para o mercado comercial no Partner Center.](./partner-center-portal/analytics.md)
- Para obter informações sobre as suas encomendas num formato gráfico e transferível, consulte [o Painel de Encomendas em análise de marketplace comercial.](orders-dashboard.md)
- Para Máquina Virtual (VM) oferece métricas de utilização e faturação medido, consulte [o Painel de Utilização em análise de mercado comercial.](usage-dashboard.md)
- Para obter informações detalhadas sobre os seus clientes, incluindo tendências de crescimento, consulte [o Customer Dashboard em análise de mercado comercial.](customer-dashboard.md)
- Para obter uma lista dos seus pedidos de descarregamento nos últimos 30 dias, consulte [Downloads Dashboard em análise de mercado comercial.](./partner-center-portal/downloads-dashboard.md)
- Para ver uma visão consolidada do feedback do cliente para as ofertas no Azure Marketplace e appSource, consulte [o painel de avaliação de avaliações & ratings no Partner Center](./partner-center-portal/ratings-reviews.md).
- Para perguntas frequentes sobre análise de mercado comercial e para um dicionário abrangente de termos de dados, consulte a terminologia de análise de [mercado comercial e questões comuns.](./analytics-faq.md)