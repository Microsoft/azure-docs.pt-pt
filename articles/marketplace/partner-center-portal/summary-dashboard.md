---
title: Painel de resumo para análise do Partner Center no Marketplace comercial
description: Saiba como acessar grafos, tendências e valores de dados agregados que resumem a atividade do Marketplace no painel de resumo no Partner Center.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: e20f3d156df139cfaf1935bae7fc7babd3e35fc5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480362"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Painel de resumo na análise do Marketplace comercial

Este artigo fornece informações sobre o painel de resumo no Partner Center. Esse painel exibe grafos, tendências e valores de dados agregados que resumem a atividade do Marketplace para suas ofertas.

Para acessar o painel de resumo, abra o **[painel analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** em Marketplace comercial.

>[!NOTE]
> Para obter definições detalhadas de terminologia de análise, consulte [perguntas e terminologia frequentes para análise do Marketplace comercial](./faq-terminology.md).

## <a name="summary-dashboard"></a>Dashboard de resumo

O painel **Resumo** apresenta uma visão geral baseada em cada tipo de oferta. Os **insights** mostram informações críticas em um relance e fornecem uma visão ampla da atividade de vendas de suas ofertas. Você pode visualizar esses relatórios usando o painel de **Resumo** . Este artigo apresenta mais detalhes sobre cada um dos seguintes elementos:

- [Intervalo de datas](#date-range)
- [Seção de resumo](#summary-section)
- [Clientes por geografia](#customers-by-geography)
- [Gráficos de tendência de crescimento](#growth-trend-charts)
- [Placar do cliente](#customer-leaderboard)
- [Tendência de contagem de estações](#seat-count-trend)
- [Tendência de pedidos SaaS de avaliações gratuitas](#free-trials-saas-orders-trend)

## <a name="elements-of-the-summary-dashboard"></a>Elementos do painel de resumo

As seções a seguir descrevem como usar o painel de resumo e como ler os dados.

### <a name="date-range"></a>Intervalo de datas

Você pode encontrar uma seleção de intervalo de datas no canto superior direito de cada página. Personalize a saída dos gráficos de página de **Resumo** selecionando um intervalo de datas com base nos últimos 3, 6 ou 12 meses ou selecionando um intervalo de datas personalizado com uma duração máxima de 12 meses. O intervalo de datas padrão é de seis meses.

![Painel de análise do Partner Center](./media/analyze-dashboard.png)

### <a name="summary-section"></a>Seção de resumo

A seção Resumo exibe uma contagem de todos os pedidos criados, clientes adquiridos e uso relatados durante o intervalo de datas selecionado. O mês atual parcial será excluído da computação dessas métricas. Por exemplo: se você tiver selecionado o período de tempo 6 minutos, as horas de uso serão computadas para os seis meses anteriores ao mês atual. Se um intervalo de datas personalizado for selecionado, um valor parcial do mês atual será excluído do cálculo.

![Tendências de crescimento no painel de resumo](./media/summary-summary-section.png)

#### <a name="reading-the-summary-section"></a>Lendo a seção de resumo

- **Orders**: contagem de todos os pedidos comprados (exclui pedidos cancelados) para ofertas que você publicou até agora.
- **Clientes**: contagem de todos os clientes que compraram suas ofertas e têm pelo menos uma ordem não cancelada.
- **Horas de uso normalizadas**: definidas como as horas de uso normalizadas para considerar o número de núcleos de VM ([número de núcleos de VM] x [horas de uso bruto]). As VMs designadas como "SHAREDCORE" usam 1/6 (ou 0,1666) como o multiplicador [número de núcleos de VM].
- **Horas de uso brutos**: a quantidade de tempo que as VMs estão em execução em termos de horas. O valor percentual ao lado de **pedidos totais**, **clientes totais**, **horas de uso normalizadas**, **horas de uso bruto**, **visitas à página**e **chamadas para ações** representam a quantidade de crescimento de uso para o intervalo de datas selecionado ([uso do último mês – uso do primeiro mês])/primeiro mês de uso). Conforme descrito acima, uma quantidade parcial do mês atual será excluída dessa métrica.
- **Tendências de crescimento**: se você passar o mouse sobre as colunas do gráfico, os gráficos de barras exibirão o valor de cada mês.
- **Triângulo verde apontando para cima**: indica uma tendência de crescimento positivo.
- **Triângulo vermelho apontando para baixo**: indica uma tendência de crescimento negativo relativa ao mês anterior.

### <a name="customers-by-geography"></a>Clientes por geografia

Os **clientes por geography** calor exibem uma contagem de clientes em um mapa mundial.

![Clientes por geografia no painel de resumo](./media/summary-customers-by-geography.png)

- Você pode mover o mapa para exibir o local exato.
- Você pode aplicar zoom em um local específico.
- O calor tem uma grade suplementar para exibir os detalhes de contagem de clientes, contagem de pedidos, horas de uso normalizadas no local específico.
- Você pode pesquisar e selecionar um país na grade para aplicar zoom ao local no mapa. Reverta para a exibição original pressionando o botão **página inicial** no mapa.
- Um **novo** cliente comprou uma de suas ofertas pela primeira vez durante o mês dentro do intervalo de datas selecionado.

### <a name="growth-trend-charts"></a>Gráficos de tendência de crescimento

Você pode exibir as tendências com base no crescimento de seus **pedidos adquiridos** (inclui pedidos cancelados), **clientes adquiridos** (incluindo clientes perdidos) e **uso** relatado, que são exibidos mês a mês de acordo com o intervalo de datas selecionado. Você pode analisar ainda mais essas tendências selecionando links abaixo do gráfico, que navega até as páginas respectivas informações de **pedido**, **uso**, **cliente**ou **Marketplace** .

As visitas à página de oferta do Marketplace **e a chamada para** gráficos de tendência de ação são exibidas para o Azure Marketplace e AppSource em duas guias.

![Visitas a páginas e planos de tendência de ações no painel de resumo](./media/summary-page-visits-and-cta.png)

O gráfico **pedidos por ofertas** organiza seus pedidos de acordo com o nome da oferta.

O gráfico de pizza **pedidos por canal de vendas** organiza seus pedidos (incluindo pedidos que os clientes cancelaram) durante o intervalo de datas selecionado, por canal de vendas. Canal de vendas é o tipo de contrato de licenciamento usado pelos clientes para comprar o Azure, que são o CSP (provedor de soluções de nuvem), Enterprise, Enterprise por revendedor, GTM e pré-pago.

O **uso por ofertas** e **uso por gráficos de** pizza de canal de vendas apresenta a divisão do uso por principais ofertas e canais de vendas, respectivamente. O gráfico de pizza interno representa o uso bruto e o gráfico de pizza externo representa o uso normalizado.

Os gráficos de pizza **pedidos por tipo de licença do Marketplace** e **uso por tipo de licença do Marketplace** exibem uma divisão de pedidos e uso por seu respectivo tipo de licença. Os tipos de licença incluem:

- **Cobrado por meio do Azure**: a Microsoft cobra os clientes em seu nome quando você opta por vender sua oferta pela Microsoft com esse tipo de licença. Os tipos de pagamento incluem pré-pago por cartão de crédito ou faturamento empresarial.
- **Traga sua própria licença**: a Microsoft não cobra os clientes pelo seu uso com esse tipo de oferta do Marketplace. Esse uso é listado como **obter agora (gratuito)** no Marketplace.
- **Gratuito**: a Microsoft não cobra os clientes pelo seu uso com esse tipo de oferta do Marketplace. Esse uso é listado como **avaliação gratuita** no Marketplace.
- **Microsoft as revendedores**: representa as ofertas vendidas por revendedores da Microsoft como parte do **programa CSP (provedor de soluções na nuvem)** .

### <a name="customer-leaderboard"></a>Placar do cliente

Os principais clientes 50 com o maior número de pedidos são exibidos em um *tabuleiro de preenchimento*, classificados por contagem de pedidos e porcentagem de pedidos mais altos.

- Selecione um cliente para exibir seus detalhes de perfil, pedidos organizados por oferta ou pedidos organizados por tipo de licença do Azure e canal de preço.
- O gráfico de rosca **ofertas por pedidos** apresenta as quatro principais ofertas (por contagem de pedidos) e as ofertas restantes agrupadas como ' Rest todos '.
- O gráfico de rosca de **uso normalizado por oferta** apresenta as cinco principais ofertas por uso.

> [!NOTE]
> As informações pessoais do cliente serão apresentadas somente se o cliente tiver fornecido consentimento. Você pode exibir essas informações se tiver feito logon com um nível de permissões de função de **proprietário** . Os usuários com a função **colaborador** não poderão exibir essas informações. [Saiba mais sobre as funções e permissões de usuário](./manage-account.md#define-user-roles-and-permissions).

### <a name="seat-count-trend"></a>Tendência de contagem de estações

O gráfico **pedidos por estação/por local** apresenta a divisão de todos os pedidos comprados de acordo com o modelo de preços. O gráfico de **tendências de contagem** de estações apresenta assentos versus pedidos comprados para todas as ofertas de SaaS (software como serviço) por estação.

### <a name="free-trials-saas-orders-trend"></a>Tendência de pedidos SaaS de avaliações gratuitas

O gráfico de **tendência de pedidos SaaS de avaliação gratuita** apresenta a tendência de pedidos para ofertas de SaaS de avaliações gratuitas com um período de avaliação de 30 dias.

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral dos relatórios de análise disponíveis no Marketplace comercial do Partner Center, consulte [análise do Marketplace comercial no Partner Center](./analytics.md).
- Para obter informações sobre seus pedidos em um formato gráfico e baixável, consulte [painel de pedidos na análise do Marketplace comercial](./orders-dashboard.md).
- Para VM (máquina virtual) oferece métricas de uso e cobrança limitada, consulte [painel de uso na análise do Marketplace comercial](./usage-dashboard.md).
- Para obter informações detalhadas sobre seus clientes, incluindo tendências de crescimento, consulte [painel do cliente na análise do Marketplace comercial](./customer-dashboard.md).
- Para obter uma lista de suas solicitações de download nos últimos 30 dias, consulte o [painel de downloads na análise do Marketplace comercial](./downloads-dashboard.md).
- Para ver uma exibição consolidada dos comentários dos clientes para ofertas no Azure Marketplace e no AppSource, consulte [painel classificações e revisões na análise do Marketplace comercial](./ratings-reviews.md).
- Para perguntas frequentes sobre a análise do Marketplace comercial e um dicionário abrangente de termos de dados, consulte [perguntas e terminologia frequentes para análise do Marketplace comercial](./faq-terminology.md).
