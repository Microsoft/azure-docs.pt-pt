---
title: Painel de resumo para análise do Partner Center no Mercado Comercial
description: Saiba como aceder a gráficos, tendências e valores de dados agregados que resumem a atividade do mercado a partir do painel Sumário no Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 8e58de7975bc7f5de1fe2ad71f97c02c901c0c21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87304127"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Dashboard de resumo na análise do mercado comercial

Este artigo fornece informações sobre o dashboard Sumário no Centro de Parceiros. Este dashboard exibe gráficos, tendências e valores de dados agregados que resumem a atividade do mercado para as suas ofertas.

Para aceder ao painel Sumário, abra o **[painel de controlo de análise no](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** Mercado Comercial.

>[!NOTE]
> Para definições detalhadas de terminologia analítica, consulte [perguntas frequentes e terminologia para análise de mercado comercial.](./faq-terminology.md)

## <a name="summary-dashboard"></a>Dashboard de resumo

O **painel Resumo** apresenta uma visão geral baseada em cada tipo de oferta. **Os insights** mostram informações críticas num ápice e fornecem uma visão ampla da atividade de vendas das suas ofertas. Pode visualizar estes relatórios utilizando o painel **Sumário.** Este artigo entra em mais detalhes sobre cada um dos seguintes elementos:

- [Intervalo de datas](#date-range)
- [Secção de resumo](#summary-section)
- [Clientes por geografia](#customers-by-geography)
- [Gráficos de tendência de crescimento](#growth-trend-charts)
- [Leaderboard do cliente](#customer-leaderboard)
- [Tendência de contagem de assentos](#seat-count-trend)
- [Testes gratuitos SaaS ordena tendência](#free-trials-saas-orders-trend)

## <a name="elements-of-the-summary-dashboard"></a>Elementos do painel de resumo

As secções seguintes descrevem como utilizar o painel de resumo e como ler os dados.

### <a name="date-range"></a>Intervalo de datas

Pode encontrar uma seleção de intervalo de datas no canto superior direito de cada página. Personalize a saída dos gráficos da página **Resumo** selecionando uma gama de datas com base nos últimos 3, 6 ou 12 meses, ou selecionando uma gama de datas personalizada com uma duração máxima de 12 meses. O intervalo de data por defeito é de seis meses.

![Painel de análise do Centro Parceiro](./media/analyze-dashboard.png)

### <a name="summary-section"></a>Secção de resumo

A secção Resumo apresenta uma contagem de todas as encomendas criadas, clientes adquiridos e utilização reportadas durante o intervalo de datas selecionadas. O mês parcial de corrente será excluído do cálculo destas métricas. Por exemplo: Se tiver selecionado o prazo de 6M, as horas de utilização são calculadas para os seis meses anteriores ao mês em curso. Se for selecionado um intervalo de datas personalizadas, um valor parcial do mês em curso será excluído do cálculo.

![Tendências de crescimento no painel de resumo](./media/summary-summary-section.png)

#### <a name="reading-the-summary-section"></a>Ler a secção de resumo

- **Encomendas**: Contar todas as encomendas adquiridas (exclui encomendas canceladas) para ofertas que publicou até agora.
- **Clientes**: Conte de todos os clientes que compraram as suas ofertas e tenham pelo menos uma encomenda não cancelada.
- **Horas de utilização normalizadas**: Definidas como as horas de utilização normalizadas para explicar o número de núcleos VM ([número de núcleos VM] x [horas de utilização bruta]). Os VMs designados como "SHAREDCORE" utilizam 1/6 (ou 0,1666) como multiplicador [número de núcleos VM].
- **Horas de utilização brutas**: A quantidade de tempo que os VM têm sido em execução em termos de horas. O valor percentual próximo do **total das encomendas**, **clientes totais,** **horários de utilização normalizados,** **horas de utilização bruta,** visitas de **página,** e **chamadas a ações** representam a quantidade de crescimento de utilização para a gama de datas selecionadas ([utilização do mês passado – utilização do primeiro mês])/ utilização do primeiro mês). Como descrito acima, uma quantidade parcial do mês em curso será excluída desta métrica.
- **Tendências de crescimento**: Se pairar sobre as colunas do gráfico, os gráficos de barras exibem o valor de cada mês.
- **Triângulo verde apontando para cima**: Indica uma tendência de crescimento positiva.
- **Triângulo vermelho apontando para baixo**: Indica uma tendência de crescimento negativa em relação ao mês anterior.

### <a name="customers-by-geography"></a>Clientes por geografia

O mapa de calor **dos Clientes por geografia** exibe uma contagem de clientes num mapa mundial.

![Clientes por geografia no painel sumário](./media/summary-customers-by-geography.png)

- Pode mover o mapa para ver a localização exata.
- Pode aproximar-se de um local específico.
- O mapa de calor tem uma grelha suplementar para ver os detalhes da contagem do cliente, contagem de encomendas, horas de utilização normalizadas no local específico.
- Pode pesquisar e selecionar um país/região na grelha para ampliar a localização no mapa. Reverta para a vista original premindo o botão **Home** no mapa.
- Um **novo** cliente adquiriu uma das suas ofertas pela primeira vez durante o mês dentro do intervalo de datas selecionadas.

### <a name="growth-trend-charts"></a>Gráficos de tendência de crescimento

Pode ver tendências com base no crescimento das suas **encomendas adquiridas** (inclui encomendas **canceladas), clientes adquiridos** (inclui clientes perdidos) e **utilização** reportada, que são exibidas mês a mês de acordo com a gama de datas selecionadas. Pode analisar ainda mais estas tendências selecionando links abaixo do gráfico, que navegam para as **respetivas páginas**de encomenda, **utilização,** **cliente**ou **Marketplace Insights.**

As visitas de página de oferta do Marketplace e a chamada para as tendências **de ação** são apresentadas para o mercado Azure e AppSource em dois separadores.

![Visitas de página e chamada para gráficos de tendência de ações no painel de instrumentos sumário](./media/summary-page-visits-and-cta.png)

O gráfico **de encomendas por ofertas** organiza as suas encomendas de acordo com o nome Oferta.

As encomendas por gráfico de tartes **de canais de vendas** organizam as suas encomendas (incluindo encomendas que os clientes cancelaram) durante a gama de datas selecionadas, pelo canal Sales. O canal de vendas é o tipo de acordo de licenciamento usado pelos clientes para comprar a Azure, que são Cloud Solution Provider (CSP), Enterprise, Enterprise através de Revseller, GTM e Pay As You Go.

**O uso por ofertas** e utilização por gráficos de tartes **de canais** de vendas apresenta a desagregação do uso por ofertas de topo e canais de vendas, respectivamente. O gráfico de tartes interiores representa o uso bruto e o gráfico de tartes exteriores representa uma utilização normalizada.

As **encomendas por tipo de licença de mercado** e **utilização por gráficos de** tarte tipo de licença de mercado exibem uma repartição de encomendas e uso pelo respetivo tipo de licença. Os tipos de licença incluem:

- **Faturado através do Azure**: A Microsoft fatura os clientes em seu nome quando optar por vender a sua oferta através da Microsoft com este tipo de licença. Os tipos de pagamento incluem pay-as-you-go via cartão de crédito ou faturação da Enterprise.
- **Traga a sua própria licença**: A Microsoft não cobra aos clientes pelo seu uso com este tipo de oferta de mercado. Este uso está listado como **Get it now (Free)** in the marketplace.
- **Grátis**: A Microsoft não cobra aos clientes o seu uso com este tipo de oferta de mercado. Este uso está listado como **teste gratuito** no mercado.
- **Microsoft como revendedor**: Representa ofertas vendidas pelos revendedores da Microsoft como parte do **programa Cloud Solution Provider (CSP).**

### <a name="customer-leaderboard"></a>Leaderboard do cliente

Os 50 clientes com maior número de encomendas são apresentados num quadro de *líderes*, classificado pela maior percentagem de pedidos e de encomendas.

- Selecione um cliente para ver os seus detalhes de perfil, encomendas organizadas por oferta, ou encomendas organizadas pelo tipo de licença Azure e canal de preços.
- O gráfico **de donuts ofertas por encomendas** apresenta as quatro principais ofertas (por contagem de encomendas) e as restantes ofertas agrupadas em 'Rest All'.
- O **uso normalizado por oferta** de donut chart apresenta as cinco principais ofertas por uso.

> [!NOTE]
> As informações pessoais do cliente só serão apresentadas se o cliente tiver dado o seu consentimento. Pode ver estas informações se tiver iniciado sessão com um nível de permissões de funções **do Proprietário.** Os utilizadores com a função **Contribuinte** não poderão visualizar esta informação. [Saiba mais sobre as funções e permissões do utilizador.](./manage-account.md#define-user-roles-and-permissions)

### <a name="seat-count-trend"></a>Tendência da Contagem de Assentos

As **encomendas por assento/por** gráfico do site apresentam a repartição de todas as encomendas adquiridas de acordo com o modelo de preços. O gráfico **de tendência da contagem de assentos** apresenta lugares versus encomendas compradas para todos os seus por assento O Software como um Serviço (SaaS) oferece.

### <a name="free-trials-saas-orders-trend"></a>Testes gratuitos SaaS ordena tendência

O **gráfico de tendências de encomendas saas de teste gratuito** apresenta a tendência das encomendas para testes gratuitos que o SaaS oferece com um período experimental de 30 dias.

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral dos relatórios de análise disponíveis no mercado comercial do Partner Center, consulte [Analytics para o mercado comercial no Partner Center](./analytics.md).
- Para obter informações sobre as suas encomendas num formato gráfico e transferível, consulte [o Painel de Encomendas em análise de marketplace comercial.](./orders-dashboard.md)
- Para Máquina Virtual (VM) oferece métricas de utilização e faturação medido, consulte [o Painel de Utilização em análise de mercado comercial.](./usage-dashboard.md)
- Para obter informações detalhadas sobre os seus clientes, incluindo tendências de crescimento, consulte [o Customer Dashboard em análise de mercado comercial.](./customer-dashboard.md)
- Para obter uma lista dos seus pedidos de descarregamento nos últimos 30 dias, consulte [Downloads Dashboard em análise de mercado comercial.](./downloads-dashboard.md)
- Para ver uma visão consolidada do feedback dos clientes para as ofertas no Azure Marketplace e appSource, consulte [ratings e comentários no painel de análise de mercado comercial.](./ratings-reviews.md)
- Para perguntas frequentes sobre análise de mercado comercial e para um dicionário abrangente de termos de dados, consulte [perguntas frequentes e terminologia para análise de mercado comercial.](./faq-terminology.md)
