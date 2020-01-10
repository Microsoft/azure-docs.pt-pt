---
title: Painel de uso na análise do Marketplace comercial no Partner Center
description: Saiba como acessar toda a VM oferece métricas de uso e cobrança limitada.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: fb0e0e8f1a514ab7d21e97e1fb2aaed3ea39b088
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75481077"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>Painel de uso na análise do Marketplace comercial

Este artigo fornece informações sobre o painel de uso no Partner Center. Este painel exibe todas as métricas de uso e cobrança limitada em duas guias separadas: uso de VM e uso de cobrança limitada.

Para acessar o painel de uso, abra o painel **[analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** no Marketplace comercial.

>[!NOTE]
> Para obter definições detalhadas de terminologia de análise, consulte [perguntas e terminologia frequentes para análise do Marketplace comercial](./faq-terminology.md).

## <a name="usage-dashboard"></a>Dashboard de utilização

O painel uso representa as métricas para todas as máquinas virtuais (VM) que oferecem uso e uso de cobrança medido. Elas são encontradas em duas guias separadas: uso da VM e uso de cobrança limitada.

Na guia uso da VM, há representações gráficas dos seguintes itens:

- [Resumo de uso](#usage-summary)
- [Uso por geografia](#usage-by-geography)
- [Uso por ofertas](#usage-by-offers)
- [Tendência de uso por ofertas e SKUs](#usage-trend-by-offers-and-skus)
- [Uso por tipo de oferta](#usage-by-offer-type)
- [Uso por tamanho da VM](#usage-by-vm-size)
- [Uso por canal de vendas](#usage-by-sales-channel)
- [Dados de uso detalhados](#detailed-usage-data)

> [!NOTE]
> Os relatórios de análise são exibidos de forma diferente em Portal do Cloud Partner (CPP) e no Partner Center. O **vendedor insights** em cpp tem uma guia Orders e Usage, que exibe dados para ofertas baseadas em uso e não baseadas em uso. No Partner Center, as métricas de uso são exibidas em uma página separada.

### <a name="usage-summary"></a>Resumo da utilização

A tabela de Resumo de uso exibe as horas de uso do cliente para todas as ofertas que foram adquiridas.

- As horas de uso normalizadas são definidas como as horas de uso normalizadas para considerar o número de núcleos de VM ([número de núcleos de VM] x [horas de uso bruto]). As VMs designadas como "SHAREDCORE" usam 1/6 (ou 0,1666) como o multiplicador [número de núcleos de VM].
- Horas de uso brutos são definidas como a quantidade de tempo que as VMs estão em execução em termos de horas.
- O valor percentual representa a alteração de crescimento de uso para o intervalo de datas selecionado ([uso do último mês – uso do primeiro mês])/uso do primeiro mês).
- Triângulos verdes apontando para cima indicam uma mudança de crescimento.
- O triângulo vermelho apontando para baixo indica uma alteração de crescimento negativa em relação ao mês anterior.
- Os gráficos de micro bar representam valores mensais. Você pode exibir o valor de cada mês passando o mouse sobre as colunas dentro do gráfico.

### <a name="usage-by-geography"></a>Uso por geografia

O **uso normalizado por geography** calor exibe horas de uso mapeadas de acordo com o país do cliente. A variação de cor do país representa a concentração de uso normalizado. Reverta para a exibição original pressionando o botão **página inicial** no mapa.

### <a name="usage-by-offers"></a>Uso por ofertas

- O gráfico de pizza **uso normalizado por ofertas** exibe uma divisão de horas de uso normalizadas por ofertas de acordo com o intervalo de datas selecionado. As 5 principais ofertas são exibidas no grafo, enquanto o restante é agrupado na categoria "Rest All".
- O gráfico de barras representa uma tendência de crescimento mês a mês para o intervalo de datas selecionado. As colunas mês representam horas de uso das ofertas com as horas de uso mais altas para o respectivo mês. O gráfico de linhas mostra a tendência de percentual de crescimento plotada no eixo Y secundário.
- Use o controle deslizante na parte superior do gráfico para rolar da direita para a esquerda ao longo do eixo x e/ou se concentrar em pontos de dados específicos.

### <a name="usage-trend-by-offers-and-skus"></a>Tendência de uso por ofertas e SKUs

Este gráfico exibe a tendência de uso normalizado para os SKUs selecionados de uma oferta. A oferta placar exibe as principais ofertas de 50 com o uso mais alto e são classificadas por horas de uso. O SKU placar exibe os principais SKUs 50 com o uso mais alto para a oferta selecionada.

### <a name="usage-by-offer-type"></a>Uso por tipo de oferta

- O gráfico de pizza **uso por tipo de oferta** organiza o uso de acordo com o tipo de oferta.
- As ofertas principais são exibidas no gráfico e o restante das ofertas é agrupado como ' Rest tudo '.
- O gráfico de **tendência** exibe as tendências de crescimento mês a mês. A coluna mês representa o uso dos principais tipos de oferta naquele mês.

### <a name="usage-by-vm-size"></a>Uso por tamanho da VM

Este gráfico representa a tendência de uso para tamanhos de VM selecionados (máx. 5) de todas as suas ofertas/SKUs. O gráfico de colunas é empilhado com as horas de uso dos tamanhos de VM selecionados.

O placar exibe os principais tamanhos de VM 50 com maior uso e classificados por horas de uso.

### <a name="usage-by-sales-channel"></a>Uso por canal de vendas

- O gráfico de pizza uso por canal de vendas organiza o uso de acordo com o canal de vendas
- O canal de vendas superior com uso mais alto é exibido no gráfico e o restante do canal de vendas é agrupado como ' Rest todos '.
- A coluna mês representa o uso pelo canal de vendas superior nesse mês.
- Os recursos deste gráfico são os mesmos do gráfico "uso por ofertas"

### <a name="detailed-usage-data"></a>Dados de uso detalhados

A **tabela detalhes de uso** exibe uma lista numerada dos principais registros de uso 1000 classificados por uso.

- Cada coluna na grade é classificável.
- Os dados podem ser extraídos para um arquivo CSV se a contagem dos registros for menor que 1000.
- Se a contagem de registros for superior a 1000, os dados de exportação serão colocados de forma assíncrona em uma página de downloads que estará disponível para os próximos 30 dias.
- Os filtros podem ser aplicados aos **dados de uso detalhados** para exibir somente os dados nos quais você está interessado. Os dados podem ser filtrados por país, canal de vendas, tipo de licença do Marketplace, tipo de uso, nome da oferta, tipo de oferta, avaliações gratuitas, ID de assinatura do Marketplace, ID do cliente e nome da empresa.

> [!NOTE]
> Selecione o **tipo de uso** no filtro de página para exibir os gráficos na página em "exibição normalizada" ou "exibição bruta". A exibição padrão para esses gráficos é "exibição normalizada".

Os **filtros da página de uso** são aplicados no nível da página. Você pode selecionar vários filtros para renderizar o gráfico para os critérios que você escolher e os dados que deseja exibir na grade/exportação de "dados de uso detalhados". Os filtros são aplicados nos dados extraídos para o intervalo de dados selecionado no canto superior direito da página pedidos.

- Os **tipos de oferta** e os nomes de **oferta** são listados apenas para as ofertas que você adquiriu durante o intervalo de datas selecionado. Os nomes de oferta na lista são exibidos para os tipos de oferta selecionados na lista.
- A seleção padrão é "todos" para cada uma das opções de filtro, exceto para o **tipo de uso**. A seleção padrão para o **tipo de uso** é o uso normalizado. Para exibir o uso bruto nos gráficos, selecione "uso bruto".
- Filtros aplicados mostram as seleções de contagem para as seleções de filtro feitas. Os filtros aplicados não são exibidos para as seleções padrão.

> [!NOTE]
> Uma definição detalhada de cada um dos campos na grade "dados da ordem detalhada", filtros de página e todas as seleções possíveis são definidas na seção dicionário de dados do artigo [perguntas frequentes e terminologia](link needed) .

A guia **uso de cobrança limitada** apresenta informações de uso para os tipos de oferta em que o uso é medido por dimensão de medidor. O tipo de oferta de SaaS excedente é apresentado no momento. A guia apresenta representações gráficas de tendências excedentes para o uso de cobrança limitada por SaaS:

- **Tendência de excedente por dimensão de medidor**: exibe a tendência de excedente mensal para a dimensão de medidor selecionada de uma oferta. O eixo X representa o mês e o eixo Y representa a quantidade de uso. A unidade de medida do medidor personalizado também é exibida no eixo Y.
- **Tendência de excedente por SKU**: representa a tendência de quantidade de uso da dimensão de medidor selecionada por SKUs. Os SKUs exibidos representarão as 5 principais SKUs com a maior quantidade de uso para a oferta selecionada.
- **Tendência de excedente dos principais clientes 50**: as principais ofertas de 50 com as horas de uso mais altas são exibidas em um ***tabuleiro de preenchimento*** e são classificadas pelo uso mais alto do medidor personalizado. Selecione um cliente no placar para exibir a tendência de uso de uma dimensão de medidor selecionada.
- **Tendência de excedente pelos principais clientes**: apresenta os principais percentuais do cliente que contribuem para o% do uso geral. O principal percentual do cliente é exibido ao longo do eixo X e é determinado pela quantidade de uso do cliente. O eixo Y exibe a quantidade de uso. Você pode exibir detalhes passando o mouse sobre pontos ao longo do gráfico de linhas.

> [!NOTE]
> Os detalhes de uso e todos os gráficos nessa página são exibidos para qualquer dimensão de medição selecionada para o filtro de página.

## <a name="next-steps"></a>Próximos Passos

- Para obter uma visão geral dos relatórios de análise disponíveis no Marketplace comercial do Partner Center, consulte [análise do Marketplace comercial no Partner Center](./analytics.md).
- Para grafos, tendências e valores de dados agregados que resumem a atividade do Marketplace para sua oferta, consulte [painel de resumo na análise do Marketplace comercial](./summary-dashboard.md).
- Para obter informações sobre seus pedidos em um formato gráfico e baixável, consulte [painel de pedidos na análise do Marketplace comercial](./orders-dashboard.md).
- Para obter informações detalhadas sobre seus clientes, incluindo tendências de crescimento, consulte [painel do cliente na análise do Marketplace comercial](./customer-dashboard.md).
- Para obter uma lista de suas solicitações de download nos últimos 30 dias, consulte o [painel de downloads na análise do Marketplace comercial](./downloads-dashboard.md).
- Para ver uma exibição consolidada dos comentários dos clientes para ofertas no Azure Marketplace e no AppSource, consulte [painel classificações e revisões na análise do Marketplace comercial](./ratings-reviews.md).
- Para perguntas frequentes sobre a análise do Marketplace comercial e um dicionário abrangente de termos de dados, consulte [perguntas e terminologia frequentes para análise do Marketplace comercial](./faq-terminology.md).
