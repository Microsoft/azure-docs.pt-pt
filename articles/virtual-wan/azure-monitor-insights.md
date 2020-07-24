---
title: Monitorização Azure Virtual WAN usando Insights do Monitor Azure
description: Saiba mais sobre a monitorização do WAN virtual utilizando insights do Monitor Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: cherylmc
ms.openlocfilehash: 8553a809173d955a21e6730de35c70de5b69e81b
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136262"
---
# <a name="azure-monitor-insights-for-virtual-wan-preview"></a>Azure Monitor Insights for Virtual WAN (Pré-visualização)

[O Azure Monitor Insights](../azure-monitor/insights/network-insights-overview.md) for Virtual WAN dá aos utilizadores e operadores a capacidade de visualizar o estado e o estado do WAN Virtual, apresentado através de um mapa topológico autodiscover. O estado e o estado dos recursos estão sobrepostos no mapa para lhe dar uma visão instantânea da saúde geral do WAN virtual. A navegação de recursos é ativada no mapa através de um clique de acesso às páginas de configuração de recursos do portal Virtual WAN.

As métricas de nível de recursos WAN virtuais são recolhidas e apresentadas através de um manual de métricas virtuais de WAN pré-embalado que mostra as métricas em um WAN virtual, hub, gateway e níveis de conexão. Este artigo acompanha-o através dos passos para usar o Azure Monitor Insights para o VIRTUAL WAN para ver a sua topologia e métricas de WAN virtuais, tudo num só local.

> [!NOTE]
> O menu Insights está em fase de lançamento para esta pré-visualização.
>

## <a name="before-you-begin"></a>Before you begin

Os passos deste artigo assumem que já implementou um WAN virtual com um ou mais hubs. Para criar um novo WAN virtual e um novo hub, utilize os passos nos seguintes artigos:

* [Criar uma WAN Virtual](virtual-wan-site-to-site-portal.md#openvwan)
* [Criar um hub](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>Ver topologia VWAN

No **portal Azure ->Virtual WAN,** a partir do menu **Monitor** à esquerda, selecione **Insights (pré-visualização)**. Isto traz a **vista Insights**, que exibe o mapa de dependência virtual wan e mini-livro de métricas de alto nível.

**Figura 1: Monitor - Menu insights**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="figura" lightbox="./media/azure-monitor-insights/monitor-menu.png":::

Na visão **insights,** você pode ver os recursos de WAN virtuais autodiscovers como hubs, gateways, firewalls, conexões e VNets falados, NVAs de 3ª parte, e ramos em um WAN virtual de ponta a ponta, como mostrado na **Figura 2**.

O estado e **o estado do** **recurso** são codificados por cores e sobrepostos nos ícones de recursos no mapa. As métricas de alto nível da WAN virtuais, tais como as capacidades do hub e a utilização de gateways, são exibidas à direita através de um mini livro de trabalho.

**Figura 2: Vista de insights**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="figura" lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>Vista de dependência

A visão **de Dependência** para O WAN Virtual ajuda a visualizar a visão interligada de todos os recursos De WAN Virtuais amplamente organizados numa arquitetura de hub-and-spoke.

**Figura 3: Vista de dependência do VWAN**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="Mapa de dependência" lightbox="./media/azure-monitor-insights/dependency-map.png":::

O mapa de visualização da Dependência apresenta os seguintes recursos como um gráfico conectado:

* Centros virtuais wan em todas as várias regiões de Azure.
* VNets de spoke que estão diretamente ligados ao centro.
* Sites de sucursais VPN e ExpressRoute e utilizadores P2S que estão ligados a cada hub através das respetivas ligações ExpressRoute, S2S e P2S, e gateways de rede virtuais.
* Firewalls Azure (incluindo proxies de firewall de terceiros) implantados num hub (Secured Hub).
* 3º NVA (Aparelhos Virtuais de Rede) que são implantados em VNets falados.

O mapa de Dependência também apresenta VNets indiretamente ligados (VNet que são espreitados com um VNets de raios VIRTUAIS).

O mapa De dependência permite uma fácil navegação às definições de configuração de cada recurso. Por exemplo, pode pairar sobre o recurso do hub para ver a configuração básica de recursos, como a região do hub, e o prefixo do hub. Clique com o botão direito para aceder à página do portal Azure do recurso hub.

**Figura 4: Navegar para informações específicas de recursos**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="informação sobre recursos":::

A barra de pesquisa e filtro na vista Dependência fornece uma forma sem esforço de pesquisar através do gráfico. Filtros diferentes ajudam a reduzir a sua pesquisa para um caminho e estado específicos.

**Figura 5: Pesquisa e filtragem**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="barra de pesquisa e filtro" lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>Métricas detalhadas

Pode selecionar **Ver métricas detalhadas** para aceder à página **Métricas detalhadas.** A página Métricas é um dashboard que está pré-configurado com separadores que fornecem informações úteis sobre a capacidade, desempenho e utilização de recursos wan virtuais ao nível, nível de hub e conexões individuais.

**Figura 6: Painel de métricas detalhadas**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="métricas detalhadas" lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre as métricas no Azure Monitor, consulte [métricas no Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).
* Para obter uma descrição completa de todas as métricas virtuais wan, consulte [registos e métricas de WAN virtuais.](logs-metrics.md)
