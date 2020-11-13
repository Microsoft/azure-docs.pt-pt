---
title: Painel de utilização em análise de mercado comercial / Mercado Azure
description: Saiba como aceder a todas as métricas de faturação de uso e medido para ofertas publicadas no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 5e2bdec8b3c628f1d470acb3e27bb18eabbc71ac
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565543"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>Dashboard de utilização na análise do mercado comercial

Este artigo fornece informações sobre o dashboard de utilização no Partner Center. Este painel de instrumentos exibe todas as máquinas virtuais (VM) oferece métricas de utilização normalizada, uso em bruto e métricas de faturação medidos em três separados: utilização normalizada VM, utilização de VM Raw e utilização de faturação medido.

Para aceder ao dashboard de utilização no Partner Center, no **Mercado Comercial,** selecione **[Analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **o Uso**.

>[!NOTE]
> Para definições detalhadas de terminologia analítica, consulte [terminologia de análise de mercado comercial e questões comuns.](./partner-center-portal/faq-terminology.md)

## <a name="usage-dashboard"></a>Dashboard de utilização

O painel **de instrumentos de utilização** no menu **Analisar** exibe as encomendas atuais para todo o seu software como um serviço (SaaS) oferece. Pode ver as representações gráficas dos seguintes itens:

- Tendência de utilização
- Utilização normalizada por ofertas
- Utilização normalizada por outras dimensões: Tamanho VM, Canais de Vendas e Tipos de Oferta
- Utilização por geografia
- Tabela de utilização detalhada
- Encomendas filtros de página

> [!NOTE]
> A latência máxima entre a geração de eventos de utilização e a reportagem no Partner Center é de 48 horas.

## <a name="elements-of-the-usage-dashboard"></a>Elementos do painel de utilização

As secções seguintes descrevem como utilizar o painel de instrumentos de utilização e como ler os dados.

### <a name="month-range"></a>Intervalo mensal

Pode encontrar uma seleção de mês no canto superior direito de cada página. Personalize a saída dos gráficos da página **de Utilização** selecionando uma gama de meses com base nos últimos 6 ou 12 meses, ou selecionando uma gama de meses personalizada com uma duração máxima de 12 meses. A gama de mês predefinido (período de computação) é de seis meses.

:::image type="content" source="./media/usage-dashboard/month-filter.png" alt-text="Ilustra os filtros do Mês no painel de instrumentos de utilização.":::

### <a name="usage-trend"></a>Tendência de utilização

Nesta secção, encontrará horas de utilização total e tendência para todas as suas ofertas que são consumidas pelos seus clientes durante o período de cálculo selecionado. As métricas e as tendências de crescimento são representadas por um gráfico de linha. Mostre o valor de cada mês pairando sobre a linha na tabela. O valor percentual abaixo das métricas de utilização no widget representa a quantidade de crescimento ou declínio durante o período de cálculo selecionado.

Existem duas representações das horas de utilização: utilização normalizada em VM e utilização em bruto em VM.

- As horas de utilização normalizadas são definidas como as horas de utilização normalizadas para explicar o número de núcleos VM ([número de núcleos VM] x [horas de utilização bruta]). Os VMs designados como "SHAREDCORE" utilizam 1/6 (ou 0,1666) como multiplicador [número de núcleos VM].
- As horas de utilização brutas são definidas como a quantidade de tempo que os VMs têm sido em execução em termos de horas.

[![Ilustra os dados de utilização normalizados e de utilização bruta no painel de instrumentos de utilização.](./media/usage-dashboard/normalized-usage.png)](./media/usage-dashboard/normalized-usage.png#lightbox)

### <a name="normalized-usage-by-offers"></a>Utilização normalizada por ofertas

Esta secção fornece o total de horas de utilização e tendência para as suas ofertas baseadas em uso no Azure Marketplace. O gráfico normalizado por ofertas é descrito abaixo.

- A **utilização normalizada por ofertas** empilhadas de gráfico de colunas apresenta uma desagregação das horas de utilização normalizadas para as 5 melhores ofertas de acordo com o período de cálculo selecionado. As cinco melhores ofertas são apresentadas num gráfico, enquanto as restantes estão agrupadas na categoria **Rest All.**
- O gráfico de colunas empilhados retrata uma tendência de crescimento mensal para a gama de datas selecionada. As colunas mensais representam horas de utilização das ofertas com as horas de utilização mais elevadas do respetivo mês. O gráfico de linha retrata a tendência de crescimento da percentagem traçada no eixo Y secundário.
- Pode selecionar ofertas específicas na legenda para exibir apenas essas ofertas no gráfico.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers.png" alt-text="Ilustra que a utilização normalizada oferece dados no painel de instrumentos de utilização.":::

Você pode selecionar qualquer oferta e um máximo de três SKUs dessa oferta para ver a tendência de utilização mensal para a oferta e os SKUs selecionados.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers-sku.png" alt-text="Ilustra as ofertas de utilização normalizadas e os dados sku no painel de instrumentos de utilização.":::

### <a name="orders-by-offers-and-skus"></a>Encomendas por ofertas e SKUs

O **gráfico De Ofertas e SKU** mostra as medidas e tendências de todas as ofertas. Tenha em atenção o seguinte:

- As ofertas de topo são apresentadas no gráfico e as restantes ofertas são agrupadas como **Rest All**.
- Pode selecionar ofertas específicas na legenda para exibir apenas essas ofertas no gráfico.
- Pairar sobre uma fatia no gráfico mostra o número de encomendas e percentagem dessa oferta em comparação com o seu número total de encomendas em todas as ofertas.
- As **encomendas por ofertas de tendência** exibem tendências de crescimento mensais. A coluna mensal representa o número de encomendas pelo nome de oferta. O gráfico de linha apresenta a tendência de crescimento percentual traçada no eixo z.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus.png" alt-text="Ilustra as Encomendas por ofertas e gráfico SKUs no painel de instrumentos de utilização.":::

Você pode selecionar qualquer oferta e um máximo de três SKUs dessa oferta para ver a tendência mensal para a oferta, SKUs, e assentos.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus-2.png" alt-text="Ilustra as Encomendas por ofertas e gráfico SKUs no painel de instrumentos de utilização. A tendência Oferta, a tendência SKUs e a tendência seats são mostradas.":::

#### <a name="normalized-usage-by-other-dimensions-vm-size-sales-channels-and-offer-type"></a>Utilização normalizada por outras dimensões: tamanho VM, canais de vendas e tipo de oferta

Existem três separadores para as dimensões: tamanho VM, canais de vendas e tipo Oferta. Pode ver as métricas de utilização e a tendência mensal em relação a cada uma destas dimensões.

:::image type="content" source="./media/usage-dashboard/normalized-usage-other-dimensions.png" alt-text="Ilustra o gráfico de outras dimensões de utilização normalizada no painel de instrumentos de utilização.":::

### <a name="usage-by-geography"></a>Utilização por geografia

Para o período de cálculo selecionado, o mapa de calor apresenta a utilização total contra a dimensão geográfica. A cor clara para a luz no mapa representa o valor baixo a alto da contagem do cliente. Selecione um registo na tabela para fazer zoom em um país/região.

:::image type="content" source="./media/usage-dashboard/normalized-usage-country.png" alt-text="Ilustra a tabela do país de utilização normalizada no painel de instrumentos de utilização.":::

Tenha em atenção o seguinte:

- Pode mover o mapa para ver a localização exata.
- Pode aproximar-se de um local específico.
- O mapa de calor tem uma grelha suplementar para ver os detalhes da contagem de clientes, contagem de encomendas e horas de utilização normalizadas no local específico.
- Pode pesquisar e selecionar um país/região na grelha para ampliar a localização no mapa. Reverta para a vista original selecionando o botão **Home** no mapa.

### <a name="usage-details-table"></a>Tabela de detalhes de utilização

A tabela de detalhes de **utilização** apresenta uma lista numerada dos 1.000 registos de utilização mais bem classificados por utilização. Tenha em atenção o seguinte:

- Cada coluna da grelha é ordenada.
- Os dados podem ser extraídos para um . TSV ou . . Ficheiro CSV se a contagem dos registos for inferior a 1.000.
- Se a contagem de registos for superior a 1.000, os dados de exportação serão colocados de forma assíncronea numa página de downloads que estará disponível durante os próximos 30 dias.
- Aplicar filtros em **dados de utilização detalhados** para exibir apenas os dados em que está interessado. Filtrar dados por país/região, canal de vendas, tipo de licença de mercado, tipo de utilização, nome de oferta, tipo de oferta, testes gratuitos, ID de assinatura do Mercado, ID do cliente e nome da empresa.

_**Quadro 1: Dicionário de termos de dados**_

| Nome da coluna | Nome do atributo | Definição |
| ------------ | ------------- | ------------- |
| Id de assinatura de mercado | ID de assinatura de mercado | O identificador único associado à subscrição Azure que o cliente usou para adquirir a sua oferta de mercado comercial. ID foi anteriormente a Azure Subscription GUID. |
| MêsStartDate | Data de Início do Mês | A Data de Início do Mês representa o mês de Compra. |
| Tipo de Oferta | Tipo de Oferta | O tipo de oferta de mercado comercial. |
| Tipo de Licença Azure | Tipo de Licença Azure | O tipo de contrato de licenciamento usado pelos clientes para comprar a Azure. Também conhecido como o Canal. Os valores possíveis são:<ui><li>Provedor de solução cloud</li><li>Enterprise</li><li>Empresa através de Revendedor</li><li>Pague como você vai</li></ul> |
| Tipo de Licença de Mercado | Tipo de Licença de Mercado | O método de faturação da oferta de mercado comercial. Os valores possíveis são:<ul><li>Faturado através de Azure</li><li>Traga a sua própria licença</li><li>Gratuito</li><li>Microsoft como Revendedor</li></ul> |
| SKU | SKU | O plano associado à oferta. |
| País do Cliente | País/Região do Cliente | O nome país/região fornecido pelo cliente. O país/região poderia ser diferente do país/região na subscrição Azure de um cliente. |
| É pré-visualização SKU | É pré-visualização SKU | O valor mostra se marcou o SKU como "pré-visualização". O valor será "Sim" se o SKU tiver sido marcado em conformidade, e apenas as subscrições Azure autorizadas por você pode implementar e usar esta imagem. O valor será "Não" se o SKU não tiver sido identificado como "pré-visualização". |
| Tipo de faturação SKU | Tipo de faturação SKU | O tipo de Faturação associado a cada SKU na oferta. Os valores possíveis são:<ul><li>Gratuito</li><li>Pago</li></ul> |
| IsInternal | Preterido | Preterido |
| Tamanho da VM | Tamanho da máquina virtual | Para os tipos de oferta baseada em VM, esta entidade significa o tamanho do VM associado ao SKU da oferta. |
| Nome da instância da nuvem | Nome da instância da nuvem | A Cloud Microsoft em que ocorreu uma implementação em VM. |
| Nome do Plano de Serviço | Preterido | Preprecada (mesma definição que SKU) |
| Nome da Oferta | Nome da Oferta | O nome da oferta do mercado comercial. |
| ImplantaçãoMethod | Preterido | Precatado (mesma definição do tipo oferta)
 |
| Nome da empresa do cliente | Nome da empresa do cliente | O nome da empresa fornecido pelo cliente. O nome pode ser diferente da cidade na assinatura Azure de um cliente. |
| Data de Utilização | Data de Utilização | A data de geração de eventos de utilização para ativos baseados no uso. |
| IsMultisolution | É Multissolução | Significa se a oferta é um tipo de oferta multissolução. |
| É novo cliente | Preterido | Preterido |
| Tamanho do núcleo | Tamanho do núcleo | Número de núcleos associados à oferta baseada em VM. |
| Tipo de Utilização | Tipo de Utilização | Significa se o evento de utilização associado à oferta é um dos seguintes:<ul><li>Utilização normalizada</li><li>Utilização bruta</li><li>Utilização medido</li></ul> |
| Data de Fim do Julgamento | Data de Fim do Julgamento | A data do período experimental para esta ordem terminará ou terminou. |
| Moeda do Cliente (CC) | Moeda do Cliente | A moeda utilizada pelo cliente para a transação comercial do mercado. |
| Preço (CC) | Preço | Preço unitário do SKU mostrado em moeda de cliente. |
| Moeda de Pagamento (PC) | Moeda de pagamento | O editor é pago pelos eventos de utilização associados ao ativo na moeda configurada pela editora. |
| Preço Estimado (PC) | Preço Estimado | Preço unitário do SKU na moeda configurada pelo editor. |
| Referência de utilização | Referência de utilização | Um GUID concatenado que é utilizado para ligar o Relatório de Utilização (em análise de mercado comercial) com o relatório de transações do Payout. A Referência de Utilização está ligada ao OrderId e ao LineItemId no relatório de transações do Payout. |
| Unidade de Utilização | Unidade de Utilização | Unidade de consumo associada ao SKU. |
| ID do cliente | ID de Cliente | O identificador único atribuído a um cliente. Um cliente pode ter zero ou mais subscrições do Azure Marketplace. |
| ID de conta de faturação | ID de conta de faturação | O identificador da conta em que a faturação é gerada. **Mapear iD de conta** de faturação ao **clienteID** para ligar o seu Relatório de Transações de Pagamento com os Relatórios de Cliente, Ordem e Utilização. |
| Quantidade de utilização | Quantidade de utilização | As unidades de utilização totais consumidas pelo ativo que é implantado pelo cliente.<br>Isto baseia-se no item do tipo de utilização. Por exemplo, se o Tipo de Utilização for normalizado, então a quantidade de utilização destina-se a uma utilização normalizada. |
| NormalizadoUsage | Utilização normalizada | As unidades de utilização total normalizadas consumidas pelo ativo que é implantado pelo cliente.<br>As horas de utilização normalizadas são definidas como as horas de utilização normalizadas para explicar o número de núcleos VM ([número de núcleos VM] x [horas de utilização bruta]). Os VMs designados como "SHAREDCORE" utilizam 1/6 (ou 0,1666) como multiplicador [número de núcleos VM]. |
| Medidusage | Utilização medido | As unidades de utilização totais consumidas pelos contadores configurados com a oferta que é implementada pelo cliente. |
| RawUsage | Utilização bruta | As unidades de utilização bruta totais consumidas pelo ativo que é implantado pelo cliente.<br>As horas de utilização brutas são definidas como a quantidade de tempo que os VMs têm vindo a ser em execução em termos de unidades de utilização. |
| Encargo alargado estimado (CC) | Encargo alargado estimado na moeda do cliente | Significa as acusações associadas à utilização. A coluna é o produto da Quantidade de Preço (CC) e de Utilização. |
| Carga Estendida Estimada (PC) | Encargo estimado alargado na moeda de pagamento | Significa as acusações associadas à utilização. A coluna é o produto do Preço Estimado (PC) e da Quantidade de Utilização. |
||||

### <a name="usage-page-filters"></a>Filtros de página de uso

Os filtros da página **de utilização** são aplicados ao nível da página 'Encomendas'. Pode selecionar um ou vários filtros para tornar o gráfico para os critérios que escolhe visualizar e os dados que pretende ver na grelha/exportação de dados de pedidos de utilização. Os filtros são aplicados nos dados extraídos para a gama de mês que selecionou no canto superior direito da página De utilização.

Os widgets e o relatório de exportação para a utilização de VM Raw são semelhantes ao uso normalizado da VM com as seguintes distinções:

- As horas de utilização normalizadas são definidas como as horas de utilização normalizadas para explicar o número de núcleos VM ([número de núcleos VM] x [horas de utilização bruta]). Os VMs designados como "SHAREDCORE" utilizam 1/6 (ou 0,1666) como multiplicador [número de núcleos VM].
- As horas de utilização brutas são definidas como a quantidade de tempo que os VMs têm vindo a ser em execução em termos de unidades de utilização.

### <a name="metered-billing-usage"></a>Uso de faturação medido

O **separador De Utilização Medido** mostra informações de utilização para tipos de oferta em que a utilização é medida pela dimensão por metro. A oferta saaS é apresentada atualmente. O separador apresenta representações gráficas das tendências de overage para o uso da faturação medido saaS:

- **Tendência de sobreação por dimensão** do medidor : Apresenta a tendência mensal de sobreatenção para a dimensão do medidor selecionado de uma oferta. O Eixo X representa o mês e o Eixo Y representa a quantidade de utilização excessiva. A unidade de medição do medidor personalizado também é exibida no eixo Y.

    :::image type="content" source="./media/usage-dashboard/metered-usage.png" alt-text="Ilustra a tabela de utilização normalizada VM no painel de instrumentos de utilização.":::

- **Tendência de sobreatenção por SKU** : Representa a tendência da quantidade de utilização da dimensão do medidor selecionada pela SKU/planos. Os cinco primeiros planos com a maior quantidade de utilização para a oferta selecionada são apresentados.

- **Tendência de sobreavalagem por clientes** : O conselho de administração do cliente representa uma lista empilhada de clientes com as horas de utilização mais altas e são apresentados num _quadro de líderes,_ classificado pela maior utilização do contador personalizado. Selecione um cliente no quadro de líderes para ver a tendência de utilização excessiva de uma dimensão de contador selecionado.

    :::image type="content" source="./media/usage-dashboard/metered-usage-by-customers.png" alt-text="Ilustra a tabela de Utilização Medido pelos Clientes no painel de instrumentos de utilização.":::

Se tiver várias ofertas que usam medidores personalizados, o relatório de utilização da faturação medido mostra informações de utilização para todas as suas ofertas, de acordo com as suas dimensões personalizadas do medidor.

> [!TIP]
> Pode utilizar o ícone de descarregamento no canto superior direito de qualquer widget para descarregar os dados. Pode fornecer feedback sobre cada um dos widgets clicando no ícone "polegares para cima" ou "polegares para baixo".

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral dos relatórios de análise disponíveis no mercado comercial, consulte os [relatórios analíticos do Access para o mercado comercial no Partner Center.](./partner-center-portal/analytics.md)
- Para gráficos, tendências e valores de dados agregados que resumem a atividade do mercado para a sua oferta, consulte [o Painel De Resumo em análise de mercado comercial.](./summary-dashboard.md)
- Para obter informações sobre as suas encomendas num formato gráfico e transferível, consulte [o Painel de Encomendas em análise de marketplace comercial](./orders-dashboard.md)
- Para máquinas virtuais (VM) oferece métricas de utilização e faturação medido, consulte [o Use Dashboard em análise de mercado comercial.](usage-dashboard.md)
- Para obter uma lista dos seus pedidos de descarregamento nos últimos 30 dias, consulte [downloads dashboard em análise de mercado comercial.](./partner-center-portal/downloads-dashboard.md)
- Para ver uma visão consolidada do feedback do cliente para as ofertas no Azure Marketplace e no Microsoft AppSource, consulte [o painel de análise de Avaliações & de Avaliações no Partner Center](./partner-center-portal/ratings-reviews.md).
- Para perguntas frequentes sobre análise de mercado comercial e para um dicionário abrangente de termos de dados, consulte a terminologia de análise de [mercado comercial e questões comuns.](./partner-center-portal/faq-terminology.md)
