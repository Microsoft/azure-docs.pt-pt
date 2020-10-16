---
title: Painel de utilização em análise de mercado comercial da Microsoft, Azure Marketplace e Microsoft AppSource
description: Saiba como aceder a todos os VM oferece métricas de utilização e faturação medido. Vá ao painel de utilização no Partner Center no mercado comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/22/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 9b8432a54aa90b7d500898b2f6959d075ac89460
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88245337"
---
# <a name="usage-dashboard-in-microsoft-commercial-marketplace-analytics"></a>Painel de utilização na análise do mercado comercial da Microsoft

Este artigo fornece informações sobre o painel de uso no Partner Center. Este painel de instrumentos exibe todas as métricas de utilização e faturação medidos em dois separados: utilização de VM e utilização de faturação medido.

Para aceder ao painel de instrumentos de utilização, abra o painel **[de instrumentos De analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** em mercado **comercial.**

>[!NOTE]
> Para definições detalhadas de terminologia analítica, consulte [perguntas frequentes e terminologia para análise de mercado comercial.](./faq-terminology.md)

## <a name="usage-dashboard"></a>Dashboard de utilização

O painel de uso representa as métricas para toda a Máquina Virtual (VM) oferece uso e utilização de faturação medido. Estas métricas encontram-se em dois separadores: utilização de VM e utilização de faturação medido.

No separador de utilização VM, existem representações gráficas dos seguintes itens:

- [Resumo da utilização](#usage-summary)
- [Utilização por geografia](#usage-by-geography)
- [Utilização por ofertas](#usage-by-offers)
- [Tendência de utilização por ofertas e planos](#usage-trend-by-offers-and-plans)
- [Utilização por tipo de oferta](#usage-by-offer-type)
- [Utilização por tamanho VM](#usage-by-vm-size)
- [Utilização por canal de vendas](#usage-by-sales-channel)
- [Dados de utilização detalhados](#detailed-usage-data)

A latência máxima entre a geração de eventos de utilização e a reportagem no Partner Center é de 48 horas.

### <a name="usage-summary"></a>Resumo da utilização

A tabela de resumo de utilização exibe as horas de utilização do cliente para todas as ofertas que adquiriram.

- As horas de utilização normalizadas são definidas como as horas de utilização normalizadas para explicar o número de núcleos VM ([número de núcleos VM] x [horas de utilização bruta]). Os VMs designados como "SHAREDCORE" utilizam 1/6 (ou 0,1666) como multiplicador [número de núcleos VM].
- As horas de utilização brutas são definidas como o número de VMs de tempo que têm sido em execução em termos de horas.
- O valor percentual representa uma alteração do crescimento da utilização para a gama de datas selecionadas ([utilização do mês passado – utilização do primeiro mês])/ utilização do primeiro mês).
- Triângulos verdes apontando para cima indicam uma mudança de crescimento.
- O triângulo vermelho apontado para baixo indica uma mudança negativa de crescimento em relação ao mês anterior.
- Os gráficos de micro barra representam valores mensais. Pode mostrar o valor de cada mês pairando sobre as colunas dentro da tabela.

### <a name="usage-by-geography"></a>Utilização por geografia

O **uso normalizado por** mapa térmico de geografia exibe horas de utilização mapeadas de acordo com o país/região do cliente. A variação de cor país/região representa uma concentração normalizada de utilização. Reverta para a vista original premindo o botão **inicial** no mapa.

### <a name="usage-by-offers"></a>Utilização por ofertas

- O **uso normalizado por ofertas** de gráfico de tortas apresenta uma desagregação das horas de utilização normalizadas por ofertas de acordo com o intervalo de datas selecionado. As cinco melhores ofertas são apresentadas em gráfico, enquanto as restantes são agrupadas na **restante** categoria.
- O gráfico de barras retrata uma tendência de crescimento mensal para a gama de datas selecionadas. As colunas mensais representam horas de utilização das ofertas com as horas de utilização mais elevadas do respetivo mês. O gráfico de linha retrata a tendência de crescimento da percentagem traçada no eixo Y secundário.
- Utilize o slider na parte superior da tabela para deslocar da direita para a esquerda ao longo do eixo x e/ou concentrar-se em pontos de dados específicos.

### <a name="usage-trend-by-offers-and-plans"></a>Tendência de utilização por ofertas e planos

Este gráfico apresenta a tendência de utilização normalizada para os planos selecionados (anteriormente chamados SKUs) de uma oferta. O leaderboard de oferta exibe as 50 melhores ofertas com o uso mais elevado, ordenadas por horas de utilização. O painel de líderes do plano exibe os 50 melhores planos com o uso mais elevado para a oferta selecionada.

### <a name="usage-by-offer-type"></a>Utilização por tipo de oferta

- O **uso por gráfico de** tarte tipo oferta organiza o uso de acordo com o tipo de oferta.
- As ofertas de topo são apresentadas na tabela e as restantes ofertas são agrupadas como 'Rest All'.
- O gráfico de **tendências** apresenta tendências de crescimento mensais. A coluna mensal representa o uso por tipos de oferta superiores nesse mês.

### <a name="usage-by-vm-size"></a>Utilização por tamanho VM

Este gráfico representa a tendência de utilização para tamanhos VM selecionados (máximo cinco) de todas as suas ofertas/planos. O gráfico da coluna é empilhado com as horas de utilização dos tamanhos de VM selecionados.

O quadro de líderes exibe os tamanhos top 50 VM com maior utilização e ordenado por horas de utilização.

### <a name="usage-by-sales-channel"></a>Utilização por canal de vendas

- O uso por gráfico de tartes de canal de vendas organiza o uso de acordo com o canal de vendas
- O canal de vendas de topo com maior utilização é apresentado na tabela e o resto do canal de vendas são agrupados como 'Rest All'.
- A coluna mensal representa o uso pelo melhor canal de vendas nesse mês.
- As características deste gráfico são as mesmas que o gráfico 'Use by Offers'

### <a name="detailed-usage-data"></a>Dados de utilização detalhados

A **tabela de detalhes de utilização** apresenta uma lista numerada dos 1000 melhores registos de utilização classificados por utilização.

- Cada coluna da grelha é ordenada.
- Os dados podem ser extraídos para um ficheiro CSV se a contagem dos registos for inferior a 1000.
- Se a contagem de registos for superior a 1000, os dados de exportação serão colocados assíncronos numa página de downloads que estará disponível durante os próximos 30 dias.
- Aplicar filtros em **dados de utilização detalhados** para exibir apenas os dados em que está interessado. Filtrar dados por país/região, canal de vendas, tipo de licença de mercado, tipo de utilização, nome de oferta, tipo de oferta, testes gratuitos, ID de assinatura do Mercado, ID do cliente e nome da empresa.

> [!NOTE]
> Selecione o **tipo de utilização** no filtro de página para visualizar gráficos na página em "Visualização normalizada" ou "Vista bruta". A vista padrão para estes gráficos é "Vista Normalizada".

Os **filtros de página de utilização** são aplicados ao nível da página. Pode selecionar vários filtros para tornar o gráfico para os critérios que escolhe visualizar e os dados que pretende visualizar na grelha/exportação de "dados de utilização detalhadas". Os filtros são aplicados nos dados extraídos para a gama de dados que selecionou no canto superior direito da página das encomendas.

- **Os tipos de oferta** e **os nomes da Oferta** estão listados apenas para as ofertas que adquiriu durante o intervalo de datas selecionados. Os nomes da oferta na lista são apresentados para os tipos de oferta que são selecionados da lista.
- A seleção predefinida é "All" para cada uma das opções de filtro, com exceção **do tipo de utilização**. A seleção padrão para **o tipo de utilização** é normalizada. Para exibir o uso bruto nos gráficos, selecione "uso cru".
- Os filtros aplicados mostram a seleção da contagem para as seleções de filtros que foram feitas. Os filtros aplicados não são apresentados para as seleções predefinidas.

> [!NOTE]
> Uma definição detalhada de cada um dos campos na grelha de "dados de ordem detalhada", filtros de página e todas as seleções possíveis são definidas na secção de dicionário de dados das [FAQs e artigo de terminologia.](link needed)

O **separador de utilização da faturação medido** apresenta informações de utilização para tipos de oferta onde a utilização é medida por dimensão por metro. A oferta saaS é apresentada atualmente. O separador apresenta representações gráficas das tendências de overage para o uso da faturação medido saaS:

- **Tendência de sobreação por dimensão**do medidor : Apresenta a tendência mensal de sobreatenção para a dimensão do medidor selecionado de uma oferta. O Eixo X representa o mês e o Eixo Y representa a quantidade de utilização. A unidade de medição do medidor personalizado também é exibida no eixo Y.
- **Tendência de sobreação por plano**: Representa a tendência da quantidade de utilização da dimensão do contador selecionado por planos. Os planos apresentados representarão os cinco melhores planos com a maior quantidade de utilização para a oferta selecionada.
- **Tendência overage by Top 50 Clientes**: As 50 melhores ofertas com as horas de utilização mais altas são exibidas num ***quadro de líderes*** e são classificadas pela utilização mais alta do medidor personalizado. Selecione um cliente na tabela de líderes para ver a tendência de utilização de uma dimensão de contador selecionado.
- **Tendência de sobreatenção por parte dos principais clientes**: Apresenta percentil de cliente de topo que contribuem para a % do uso geral. O percentil superior do cliente é apresentado ao longo do eixo X e é determinado pela quantidade de utilização do cliente. O eixo Y exibe a quantidade de utilização. Pode apresentar detalhes pairando sobre pontos ao longo da tabela de linha.

Se tiver várias ofertas que usam medidores personalizados, o relatório de utilização da faturação medido mostra informações de utilização para todas as suas ofertas, de acordo com as suas dimensões personalizadas do medidor.

> [!NOTE]
> Os detalhes de utilização e todos os gráficos desta página são apresentados para qualquer dimensão do medidor selecionada para o filtro de página.

## <a name="next-steps"></a>Passos Seguintes

- Para uma visão geral dos relatórios de análise disponíveis no mercado comercial do Partner Center, consulte [Analytics para o mercado comercial no Partner Center](./analytics.md).
- Para gráficos, tendências e valores de dados agregados que resumem a atividade do mercado para a sua oferta, consulte [o Painel De Resumo em análise de mercado comercial.](./summary-dashboard.md)
- Para obter informações sobre as suas encomendas num formato gráfico e transferível, consulte [o Painel de Encomendas em análise de marketplace comercial.](./orders-dashboard.md)
- Para obter informações detalhadas sobre os seus clientes, incluindo tendências de crescimento, consulte [o Customer Dashboard em análise de mercado comercial.](./customer-dashboard.md)
- Para obter uma lista dos seus pedidos de descarregamento nos últimos 30 dias, consulte [Downloads Dashboard em análise de mercado comercial.](./downloads-dashboard.md)
- Para ver uma visão consolidada do feedback dos clientes para as ofertas no Microsoft AppSource e no Azure Marketplace, consulte [ratings e comentários no painel de análise de mercado comercial.](./ratings-reviews.md)
- Para perguntas frequentes sobre análise de mercado comercial e para um dicionário abrangente de termos de dados, consulte [perguntas frequentes e terminologia para análise de mercado comercial.](./faq-terminology.md)
