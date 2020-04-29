---
title: Dashboard de uso na análise do mercado comercial no Partner Center
description: Saiba como aceder a todos os VM oferece métricas de utilização e faturação medição.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 33762540d14ea51e8325abe9a466007cd7cca748
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81262184"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>Dashboard de utilização na análise do mercado comercial

Este artigo fornece informações sobre o dashboard de utilização no Partner Center. Este dashboard exibe todos os VM que oferecem métricas de faturação de utilização e medimétricas em dois separadores: utilização de VM e utilização de faturação medida.

Para aceder ao dashboard de utilização, abra o dashboard **[Analyze](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** no Mercado Comercial.

>[!NOTE]
> Para definições detalhadas de terminologia analítica, consulte [frequentemente perguntas e terminologia para análise](./faq-terminology.md)de mercado comercial.

## <a name="usage-dashboard"></a>Dashboard de utilização

O Dashboard de Utilização representa as métricas para todas as máquinas virtuais (VM) que oferece utilização e utilização de faturação medida. Estes encontram-se em dois separadores distintos: utilização de VM e utilização de faturação medição.

No separador de utilização VM, existem representações gráficas dos seguintes itens:

- [Resumo da utilização](#usage-summary)
- [Utilização por geografia](#usage-by-geography)
- [Utilização por ofertas](#usage-by-offers)
- [Tendência de utilização por ofertas e SKUs](#usage-trend-by-offers-and-skus)
- [Utilização por tipo de oferta](#usage-by-offer-type)
- [Utilização pelo tamanho VM](#usage-by-vm-size)
- [Utilização por canal de vendas](#usage-by-sales-channel)
- [Dados de utilização detalhados](#detailed-usage-data)

> [!NOTE]
> Os relatórios de análise exibem de forma diferente no Cloud Partner Portal (CPP) e no Partner Center. **Os conhecimentos dos vendedores** em CPP têm um separador de encomendas e de utilização, que apresenta dados tanto para ofertas baseadas em uso como para ofertas não baseadas em uso. No Partner Center, as métricas de utilização são apresentadas numa página separada.

### <a name="usage-summary"></a>Resumo da utilização

A tabela de resumo de utilização mostra as horas de utilização do cliente para todas as ofertas que adquiriu.

- As horas de utilização normalizadas são definidas como as horas de utilização normalizadas para contabilizar o número de núcleos VM ([número de núcleos VM] x [horas de utilização bruta]). Os VMs designados como "SHAREDCORE" utilizam 1/6 (ou 0,1666) como multiplicador [número de núcleos VM].
- As horas de utilização brutas são definidas como a quantidade de tempo que os VMs têm funcionado em termos de horas.
- O valor percentual representa uma variação de crescimento do uso para a gama de datas selecionada ([no mês passado – utilização do primeiro mês])/utilização do primeiro mês).
- Triângulos verdes apontando para cima indicam uma mudança de crescimento.
- O triângulo vermelho apontando para baixo indica uma mudança de crescimento negativa em relação ao mês anterior.
- Os gráficos de micro barras representam valores mensais. Pode mostrar o valor por cada mês pairando sobre as colunas dentro da tabela.

### <a name="usage-by-geography"></a>Utilização por geografia

O **uso normalizado pelo** mapa de calor da geografia mostra horas de utilização mapeadas de acordo com o país do cliente. A variação da cor do país representa uma concentração de utilização normalizada. Volte à vista original premindo o botão **inicial** no mapa.

### <a name="usage-by-offers"></a>Utilização por ofertas

- O **uso normalizado por ofertas** de gráfico de tarte saem de uma repartição das horas de utilização normalizadas por ofertas de acordo com a gama de datas selecionada. As 5 melhores ofertas são exibidas em gráfico, enquanto as restantes estão agruparadas na categoria "descanse todos".
- O gráfico de barras retrata uma tendência de crescimento mensal para a gama de datas selecionadas. As colunas mensais representam horas de utilização das ofertas com as horas de utilização mais elevadas para o mês respetivo. O gráfico de linha mostra a tendência percentual de crescimento traçada no eixo Y secundário.
- Utilize o slider na parte superior da tabela para rolar para a esquerda ao longo do eixo x e/ou concentrar-se em pontos de dados específicos.

### <a name="usage-trend-by-offers-and-skus"></a>Tendência de utilização por ofertas e SKUs

Este gráfico apresenta a tendência de utilização normalizada para as SKUs selecionadas de uma oferta. O leaderboard de oferta exibe as 50 melhores ofertas com o maior uso e são classificados por horas de utilização. O leaderboard SKU exibe os 50 Melhores SKUs com a maior utilização para a oferta selecionada.

### <a name="usage-by-offer-type"></a>Utilização por tipo de oferta

- O gráfico de tarte tipo **oferta** organiza o uso de acordo com o tipo de oferta.
- As principais ofertas são exibidas na tabela e as restantes ofertas são agruparadas como 'Rest All'.
- O gráfico de **tendências** apresenta tendências de crescimento mensais. A coluna do mês representa o uso por tipos de oferta de topo nesse mês.

### <a name="usage-by-vm-size"></a>Utilização pelo tamanho VM

Este gráfico representa a tendência de utilização para tamanhos VM selecionados (máx. 5) de todas as suas ofertas/SKUs. O gráfico da coluna é empilhado com as horas de utilização dos tamanhos vm selecionados.

A tabela exibe os 50 melhores tamanhos vm com maior utilização e ordenado por horas de utilização.

### <a name="usage-by-sales-channel"></a>Utilização por canal de vendas

- O uso por gráfico de tarte de canal de vendas organiza o uso de acordo com o canal de vendas
- O canal de vendas de topo com maior utilização é exibido na tabela e o resto do canal de vendas são agrupados como 'Rest All'.
- A coluna do mês representa o uso pelo canal de vendas de topo nesse mês.
- As características deste gráfico são as mesmas que o gráfico 'Usage by Offers'

### <a name="detailed-usage-data"></a>Dados de utilização detalhados

A tabela de detalhes de **utilização** apresenta uma lista numerada dos 1000 melhores registos de utilização classificados pela utilização.

- Cada coluna na grelha é ordenada.
- Os dados podem ser extraídos para um ficheiro CSV se a contagem dos registos for inferior a 1000.
- Se a contagem de registos for superior a 1000, os dados de exportação serão colocados assincronicamente numa página de downloads que estará disponível nos próximos 30 dias.
- Os filtros podem ser aplicados aos **dados de utilização detalhados** para mostrar apenas os dados que lhe interessam. Os dados podem ser filtrados por país, canal de vendas, tipo de licença marketplace, tipo de utilização, nome da oferta, tipo de oferta, testes gratuitos, ID de subscrição do Marketplace, ID do cliente e nome da empresa.

> [!NOTE]
> Selecione o **tipo de utilização** no filtro da página para visualizar gráficos na página em "Vista Normalizada" ou "Vista crua". A vista padrão para estes gráficos é "vista normalizada".

Os filtros da **página de utilização** são aplicados ao nível da página. Pode selecionar vários filtros para tornar o gráfico para os critérios que escolher visualizar, e os dados que pretende visualizados na grelha/exportação de "dados de utilização detalhados". Os filtros são aplicados nos dados extraídos para a gama de dados selecionado no canto superior direito da página de encomendas.

- **Os tipos** de oferta e **os nomes de Oferta** estão listados apenas para as ofertas que adquiriu durante a gama de datas selecionadas. Os nomes de oferta na lista são apresentados para tipos de oferta selecionados da lista.
- A seleção predefinida é "All" para cada uma das opções do filtro, com exceção do **tipo de utilização**. A seleção padrão para **o tipo de utilização** é uma utilização normalizada. Para visualizar o uso bruto nas tabelas, selecione "uso bruto".
- Os filtros aplicados mostram as seleções de contagem para as seleções de filtro s que foram feitas. Os filtros aplicados não são apresentados para as seleções predefinidas.

> [!NOTE]
> Uma definição detalhada de cada um dos campos na grelha de "dados de ordem detalhada", filtros de página e todas as seleções possíveis são definidas na secção do dicionário de dados das PERGUNTAS FAQs e artigo de [terminologia.](link needed)

O separador de utilização de **faturação medido** apresenta informações de utilização para tipos de oferta onde a utilização é medida pela dimensão por metro. Atualmente, é apresentada uma sobretaxa de oferta SaaS. O separador apresenta representações gráficas de tendências de sobretaxa para o uso de faturação medido saaS:

- **Tendência de sobreposição por dimensão do contador**: Exibe a tendência mensal de sobreagem para a dimensão do contador selecionado de uma oferta. O Eixo X representa o mês e o Eixo Y representa a quantidade de utilização. A unidade de medição do medidor personalizado também é exibida no Eixo Y.
- **Tendência overage by SKU**: Representa a tendência da quantidade de utilização da dimensão do contador selecionada pelas SKUs. As SKUs apresentadas representarão as 5 Melhores SKUs com a maior quantidade de utilização para a oferta selecionada.
- **Tendência overage by Top 50 Customers**: As 50 melhores ofertas com as horas de utilização mais altas são exibidas em um quadro de ***líderes*** e são classificadas pelo uso mais alto do medidor personalizado. Selecione um cliente na tabela de líderes para ver a tendência de utilização de uma dimensão de contador selecionada.
- **Tendência de sobretaxa por parte**dos clientes de topo : Apresenta percentil de clientes de topo que contribuem para a % do uso global. O percentil superior do cliente é exibido ao longo do eixo X e é determinado pela quantidade de utilização do cliente. O eixo Y mostra a quantidade de utilização. Pode exibir detalhes pairando sobre pontos ao longo da tabela de linhas.

> [!NOTE]
> Os detalhes de utilização e todos os gráficos desta página são apresentados para qualquer dimensão do contador selecionada para o filtro da página.

## <a name="next-steps"></a>Passos Seguintes

- Para uma visão geral dos relatórios de análise disponíveis no mercado comercial do Partner Center, consulte [o Analytics para o mercado comercial no Partner Center](./analytics.md).
- Para gráficos, tendências e valores de dados agregados que resumam a atividade do mercado para a sua oferta, consulte [Summary Dashboard na análise do mercado comercial.](./summary-dashboard.md)
- Para obter informações sobre as suas encomendas num formato gráfico e descarregável, consulte [o Painel de Encomendas na análise do mercado comercial](./orders-dashboard.md).
- Para obter informações detalhadas sobre os seus clientes, incluindo tendências de crescimento, consulte o [Customer Dashboard na análise do mercado comercial.](./customer-dashboard.md)
- Para obter uma lista dos seus pedidos de descarregamento ao longo dos últimos 30 dias, consulte [Downloads Dashboard na análise do mercado comercial](./downloads-dashboard.md).
- Para ver uma visão consolidada do feedback dos clientes para as ofertas no Azure Marketplace e appSource, consulte [ratings e avaliações de dashboard na análise do mercado comercial.](./ratings-reviews.md)
- Para perguntas frequentes sobre análise de mercado comercial e para um dicionário abrangente de termos de dados, consulte [frequentemente perguntas e terminologia para análise](./faq-terminology.md)de mercado comercial.
