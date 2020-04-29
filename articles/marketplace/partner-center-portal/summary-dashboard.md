---
title: Painel de instrumentos sumário para análise do Partner Center no Mercado Comercial
description: Saiba como aceder a gráficos, tendências e valores de dados agregados que resumem a atividade do mercado a partir do dashboard Sumário no Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 86bce85c6ec273b4ab5f9f00cbae68fc054f53f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81262405"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Painel de instrumentos sumário na análise do mercado comercial

Este artigo fornece informações sobre o dashboard Sumário no Partner Center. Este dashboard exibe gráficos, tendências e valores de dados agregados que resumem a atividade do mercado para as suas ofertas.

Para aceder ao dashboard Sumário, abra o **[dashboard Analyze](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** no âmbito do Mercado Comercial.

>[!NOTE]
> Para definições detalhadas de terminologia analítica, consulte [frequentemente perguntas e terminologia para análise](./faq-terminology.md)de mercado comercial.

## <a name="summary-dashboard"></a>Dashboard de resumo

O painel de instrumentos **sumário** apresenta uma visão geral baseada em cada tipo de oferta. **Os insights** mostram informações críticas num ápice e proporcionam uma visão ampla da atividade de vendas das suas ofertas. Pode visualizar estes relatórios utilizando o painel de instrumentos **sumário.** Este artigo entra em mais detalhes sobre cada um dos seguintes elementos:

- [Intervalo de datas](#date-range)
- [Secção sumária](#summary-section)
- [Clientes por geografia](#customers-by-geography)
- [Gráficos de tendências de crescimento](#growth-trend-charts)
- [Tabela de clientes](#customer-leaderboard)
- [Tendência de contagem de lugares](#seat-count-trend)
- [Testes gratuitos SaaS encomenda tendência](#free-trials-saas-orders-trend)

## <a name="elements-of-the-summary-dashboard"></a>Elementos do painel de instrumentos sumário

As seguintes secções descrevem como utilizar o painel de instrumentos de resumo e como ler os dados.

### <a name="date-range"></a>Intervalo de datas

Pode encontrar uma seleção de intervalo de data seletiva no canto superior direito de cada página. Personalize a saída dos gráficos da página **Sumária** selecionando uma gama de datas baseada nos últimos 3, 6 ou 12 meses, ou selecionando uma gama de datas personalizada com uma duração máxima de 12 meses. O intervalo de data padrão é de seis meses.

![Painel de instrumentos de análise do Partner Center Analyze](./media/analyze-dashboard.png)

### <a name="summary-section"></a>Secção sumária

A secção Resumo apresenta uma contagem de todas as encomendas criadas, clientes adquiridos e utilização reportada durante a gama de datas selecionadas. O mês em curso parcial será excluído da computação destas métricas. Por exemplo: Se tiver selecionado o prazo de 6M, as horas de utilização são calculadas durante os seis meses anteriores ao mês em curso. Se for selecionada uma gama de datas personalizadas, um montante parcial do mês em curso será excluído do cálculo.

![Tendências de crescimento no dashboard sumário](./media/summary-summary-section.png)

#### <a name="reading-the-summary-section"></a>Ler a secção de resumo

- **Encomendas**: Contagem de todas as encomendas adquiridas (exclui encomendas canceladas) para ofertas que publicou até agora.
- **Clientes**: Contagem de todos os clientes que compraram as suas ofertas e tenham pelo menos uma encomenda não cancelada.
- **Horários de utilização normalizados**: Definidos como as horas de utilização normalizadas para contabilizar o número de núcleos VM ([número de núcleos VM] x [horas de utilização bruta]). Os VMs designados como "SHAREDCORE" utilizam 1/6 (ou 0,1666) como multiplicador [número de núcleos VM].
- Horas de **utilização brutas**: A quantidade de tempo que os VMs têm funcionado em termos de horas. O valor percentual próximo do total de **encomendas**, **clientes totais,** horários de **utilização normalizados,** **horas de utilização bruta,** visitas de **página,** e **chamada para ações** representam a quantidade de crescimento de utilização para a gama de datas selecionada ([utilização do mês passado – utilização do primeiro mês])/utilização do primeiro mês). Como acima descrito, uma quantidade parcial do mês em curso será excluída desta métrica.
- **Tendências de crescimento**: Se pairar sobre as colunas do gráfico, os gráficos de barras mostram o valor para cada mês.
- **Triângulo verde apontando para cima:** Indica uma tendência de crescimento positiva.
- **Triângulo vermelho apontando para baixo**: Indica uma tendência de crescimento negativa em relação ao mês anterior.

### <a name="customers-by-geography"></a>Clientes por geografia

Os Clientes por mapa de **calor de geografia** exibem uma contagem de clientes num mapa mundial.

![Clientes por geografia no dashboard Sumário](./media/summary-customers-by-geography.png)

- Pode mover o mapa para ver a localização exata.
- Pode ampliar para um local específico.
- O mapa térmico tem uma grelha suplementar para ver os detalhes da contagem de clientes, contagem de encomendas, horas de utilização normalizadas no local específico.
- Pode pesquisar e selecionar um país da grelha para ampliar a localização do mapa. Volte à vista original premindo o botão **Home** no mapa.
- Um **novo** cliente adquiriu uma das suas ofertas pela primeira vez durante o mês dentro da gama de datas selecionadas.

### <a name="growth-trend-charts"></a>Gráficos de tendências de crescimento

Pode ver tendências com base no crescimento das suas **encomendas adquiridas** (inclui encomendas canceladas), **clientes adquiridos** (inclui clientes perdidos) e **utilização** reportada, que são exibidas mês a mês de acordo com a gama de datas selecionadas. Pode ainda analisar estas tendências selecionando links abaixo do gráfico, que navegam para as **páginas**respetivas , **de utilização,** **cliente**ou **Marketplace Insights.**

O Marketplace oferece **visitas de página e** gráficos de tendência de ação são exibidos para o mercado Azure e AppSource em dois separadores.

![Visitas de página e chamada para gráficos de tendências de ações em dashboard sumário](./media/summary-page-visits-and-cta.png)

A tabela **de encomendas por ofertas** organiza as suas encomendas de acordo com o nome Oferta.

As **encomendas por** gráfico de tarte de canal de vendas organizam as suas encomendas (incluindo encomendas que os clientes cancelaram) durante a gama de datas selecionadas, por canal de vendas. O canal de vendas é o tipo de contrato de licenciamento usado pelos clientes para comprar o Azure, que são o Cloud Solution Provider (CSP), Enterprise, Enterprise através do Reseller, GTM e Pay As You Go.

**O uso por ofertas** e utilização por gráficos de tartes de **canais** de venda apresentam a desagregação do uso por ofertas de topo e canais de vendas, respectivamente. O gráfico de tartes interior representa o uso bruto e o gráfico de tartes exteriores representa o uso normalizado.

As **encomendas por tipo** de licença de mercado e **utilização por** gráficos de tarte tipo licença de mercado apresentam uma repartição de encomendas e utilização pelo respetivo tipo de licença. Os tipos de licença incluem:

- **Faturado através do Azure**: A Microsoft fatura clientes em seu nome quando opta por vender a sua oferta através da Microsoft com este tipo de licença. Os tipos de pagamento incluem pay-as-you-go via cartão de crédito ou faturação da Empresa.
- **Traga a sua própria licença**: A Microsoft não fatura clientes pelo seu uso com este tipo de oferta de mercado. Este uso está listado como **Get it now (Free)** in the marketplace.
- **Grátis**: A Microsoft não fatura os clientes pelo seu uso com este tipo de oferta de mercado. Este uso está listado como **teste gratuito** no mercado.
- **Microsoft como revendedor**: Representa ofertas vendidas pelos revendedores da Microsoft como parte do **programa Cloud Solution Provider (CSP).**

### <a name="customer-leaderboard"></a>Tabela de clientes

Os 50 principais clientes com maior número de encomendas são apresentados num quadro de *líderes,* classificados pela maior contagem de encomendas e por percentagem de encomendas.

- Selecione um cliente para ver os seus dados de perfil, encomendas organizadas por oferta, ou encomendas organizadas pelo tipo de licença Azure e canal de preços.
- O gráfico **de ofertas por encomendas** apresenta as quatro melhores ofertas (por contagem de encomendas) e as restantes ofertas agruparadas como 'Rest All'.
- O **uso normalizado por oferta** donut chart apresenta as cinco principais ofertas por uso.

> [!NOTE]
> As informações pessoais do cliente só serão apresentadas se o cliente tiver fornecido o consentimento. Pode ver esta informação se tiver iniciado sessão com um nível de permissões de **proprietário.** Os utilizadores com a função **De Contribuinte** não poderão visualizar esta informação. [Saiba mais sobre as funções e permissões](./manage-account.md#define-user-roles-and-permissions)do utilizador.

### <a name="seat-count-trend"></a>Tendência de contagem de assentos

As **encomendas por assento/gráfico de site** apresentam a desagregação de todas as encomendas adquiridas de acordo com o modelo de preços. O gráfico de **tendência da contagem de lugares** apresenta lugares versus encomendas adquiridas para todos os seus software por assento como um Serviço (SaaS) oferece.

### <a name="free-trials-saas-orders-trend"></a>Testes gratuitos SaaS encomenda tendência

O gráfico de tendências de **encomendas free trial SaaS** apresenta a tendência das encomendas para testes gratuitos que a SaaS oferece com um período experimental de 30 dias.

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral dos relatórios de análise disponíveis no mercado comercial do Partner Center, consulte [o Analytics para o mercado comercial no Partner Center](./analytics.md).
- Para obter informações sobre as suas encomendas num formato gráfico e descarregável, consulte [o Painel de Encomendas na análise do mercado comercial](./orders-dashboard.md).
- Para máquina virtual (VM) oferece métricas de utilização e faturação medida, consulte [o Usage Dashboard na análise do mercado comercial](./usage-dashboard.md).
- Para obter informações detalhadas sobre os seus clientes, incluindo tendências de crescimento, consulte o [Customer Dashboard na análise do mercado comercial.](./customer-dashboard.md)
- Para obter uma lista dos seus pedidos de descarregamento ao longo dos últimos 30 dias, consulte [Downloads Dashboard na análise do mercado comercial](./downloads-dashboard.md).
- Para ver uma visão consolidada do feedback dos clientes para as ofertas no Azure Marketplace e appSource, consulte [ratings e avaliações de dashboard na análise do mercado comercial.](./ratings-reviews.md)
- Para perguntas frequentes sobre análise de mercado comercial e para um dicionário abrangente de termos de dados, consulte [frequentemente perguntas e terminologia para análise](./faq-terminology.md)de mercado comercial.
