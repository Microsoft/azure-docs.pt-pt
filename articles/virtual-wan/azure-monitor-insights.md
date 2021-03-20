---
title: Monitorização do WAN virtual usando insights do monitor Azure
description: Neste artigo, você aprenderá sobre a monitorização do Azure Virtual WAN usando Azure Monitor Insights.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 6ead00a0979d81ef11ac81fb13a1abe31317691d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100571313"
---
# <a name="azure-monitor-insights-for-virtual-wan"></a>Azure Monitor Insights para WAN Virtual

[O Azure Monitor Insights](../azure-monitor/insights/network-insights-overview.md) for Azure Virtual WAN dá aos utilizadores e operadores a capacidade de visualizar o estado e o estado de um WAN virtual, apresentado através de um mapa topológico autodiscover. O estado de recursos e sobreposições de estado no mapa dão-lhe uma visão instantânea da saúde geral do WAN virtual. Pode navegar recursos no mapa através de um clique de acesso às páginas de configuração de recursos do portal Virtual WAN.

As métricas de nível de recursos virtuais wan são recolhidas e apresentadas através de um livro de métricas virtuais wan pré-embalado. O livro mostra as métricas nos níveis virtuais de WAN, hub, gateway e conexão. Este artigo acompanha-o através dos passos para usar o Azure Monitor Insights para o VIRTUAL WAN para ver a sua topologia e métricas de WAN virtuais, tudo num só local.

> [!NOTE]
> A opção de menu **Insights** está no portal Virtual WAN em **Monitorização**. Também pode aceder ao Livro de Topologia e Métricas Virtual WAN utilizando o Azure Monitor for Networks. Para obter mais informações, consulte [o Azure Monitor for Networks](../azure-monitor/insights/network-insights-overview.md). 
>

## <a name="before-you-begin"></a>Antes de começar

Para completar os passos deste artigo, você precisa ter um WAN virtual com um ou mais hubs. Para criar um WAN virtual e um hub, siga os passos nestes artigos:

* [Criar uma WAN Virtual](virtual-wan-site-to-site-portal.md#openvwan)
* [Criar um hub](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>Ver topologia VWAN

Ir para **o portal Azure**  >  **Virtual WAN**. No menu **Monitor** no painel esquerdo, selecione **Insights (pré-visualização)**. A vista **insights** aparece. Mostra o mapa de dependência virtual wan e mini-livro **de métricas** de alto nível.

**Figura 1: Menu Monitor > Insights**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="Screenshot que mostra a visualização insights (pré-visualização)." lightbox="./media/azure-monitor-insights/monitor-menu.png":::

Na vista **Insights,** pode ver os recursos de WAN virtuais codificados automaticamente. Estes recursos incluem hubs, gateways, firewalls, conexões e redes virtuais falada, NVAs de terceiros e sucursais em um WAN virtual de ponta a ponta. Por exemplo, consulte **a Figura 2**.

O estado e o estado do recurso são codificados por cores e sobrepostos nos ícones de recursos no mapa. Métricas de WAN virtuais de alto nível, como capacidades de hub e utilização de gateways, aparecem no lado direito da janela num mini livro de trabalho.

**Figura 2: Vista de insights**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="Screenshot que mostra a vista Insights." lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>Vista de dependência

A visão **de Dependência** para O WAN Virtual ajuda-o a visualizar a visão interligada de todos os recursos De WAN Virtuais amplamente organizados numa arquitetura de hub-and-spoke.

**Figura 3: Vista de dependência do VWAN**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="Screenshot que mostra a vista de Dependência." lightbox="./media/azure-monitor-insights/dependency-map.png":::

O mapa de visualização **da Dependência** apresenta os seguintes recursos como um gráfico conectado:

* Centros virtuais wan em todas as várias regiões de Azure.
* Redes virtuais faladas que estão diretamente ligadas ao centro.
* Sites de filiais VPN e Azure ExpressRoute e utilizadores P2S que estão ligados a cada hub através das respetivas ligações ExpressRoute, S2S e P2S e gateways de rede virtuais.
* Firewalls Azure (incluindo proxies de firewall de terceiros) implantados num hub (hub seguro).
* NVAs de terceiros (aparelhos virtuais de rede) que são implantados em redes virtuais faladas.

O mapa de dependência também exibe redes virtuais indiretamente ligadas (redes virtuais que são espreitadas com redes virtuais de falei WAN).

O mapa de dependência permite uma fácil navegação às definições de configuração de cada recurso. Por exemplo, pode pairar sobre o recurso do hub para ver a configuração básica de recursos, como a região do hub e o prefixo do hub. Clique com o botão direito para aceder à página do portal Azure do recurso hub.

**Figura 4: Navegar para informações específicas de recursos**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="Screenshot que mostra como navegar para informações específicas de recursos.":::

A barra de pesquisa e filtro na vista **Dependência** proporciona uma maneira fácil de pesquisar através do gráfico. Vários filtros ajudam a reduzir a sua pesquisa para um caminho e estado específicos.

**Figura 5: Pesquisa e filtragem**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="Screenshot que mostra a barra de pesquisa e filtro." lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>Métricas detalhadas

Pode selecionar **Ver métricas detalhadas** para aceder à página **Métricas detalhadas.** A página **Métricas** é um dashboard pré-configurado com separadores separados. Estes separadores fornecem insights sobre a capacidade, desempenho e utilização de recursos WAN virtuais ao nível virtual-WAN e ao nível do hub, e ao nível das ligações individuais.

**Figura 6: Painel de métricas detalhadas**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="Screenshot que mostra o painel de métricas detalhado." lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>Passos seguintes

* Para saber mais, consulte [métricas no Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md).
* Para obter uma descrição completa de todas as métricas virtuais wan, consulte [registos e métricas de WAN virtuais.](logs-metrics.md)
