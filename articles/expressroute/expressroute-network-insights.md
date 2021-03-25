---
title: Azure ExpressRoute Insights usando insights de rede
description: Saiba mais sobre a Azure ExpressRoute Insights utilizando a Network Insights.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/23/2021
ms.author: duau
ms.openlocfilehash: 7033ea6a1ba6d85f9aa15e14bb9577b2439c59a8
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105050568"
---
# <a name="azure-expressroute-insights-using-network-insights"></a>Azure ExpressRoute Insights usando insights de rede

Este artigo explica como o Network Insights pode ajudá-lo a ver as suas métricas e configurações ExpressRoute num só local. Através do Network Insights, pode ver mapas topológicos e dashboards de saúde que contenham informações importantes do ExpressRoute sem precisar de completar qualquer configuração extra.

:::image type="content" source="./media/expressroute-network-insights/monitor-landing-page.png" alt-text="Screenshot da página de aterragem do monitor ExpressRoute." lightbox="./media/expressroute-network-insights/monitor-landing-page-expanded.png":::

## <a name="visualize-functional-dependencies"></a>Visualizar dependências funcionais

Para ver esta solução, navegue na página *Azure Monitor,* selecione *Redes* e, em seguida, selecione o cartão *Circuitos ExpressRoute.* Em seguida, selecione o botão de topologia para o circuito que gostaria de ver.

A visão de dependência funcional fornece uma imagem clara da sua configuração ExpressRoute, delineando a relação entre diferentes componentes ExpressRoute (espreitamentos, ligações, gateways).

:::image type="content" source="./media/expressroute-network-insights/topology-view.png" alt-text="Screenshot da vista de topologia para insights de rede." lightbox="./media/expressroute-network-insights/topology-view-expanded.png":::

Passe sobre qualquer componente no mapa de topologia para ver informações de configuração. Por exemplo, paire sobre um componente de observação ExpressRoute para ver detalhes como largura de banda de circuito e ativação global.

:::image type="content" source="./media/expressroute-network-insights/topology-hovered.png" alt-text="Screenshot de pairar sobre os recursos de vista topologia." lightbox="./media/expressroute-network-insights/topology-hovered-expanded.png":::

## <a name="view-a-detailed-and-pre-loaded-metrics-dashboard"></a>Ver um painel de métricas detalhado e pré-carregado

Assim que rever a topologia da sua configuração ExpressRoute utilizando a vista de dependência funcional, selecione **Ver métricas detalhadas** para navegar para a visão detalhada das métricas para entender o desempenho do seu circuito. Esta vista oferece uma lista organizada de recursos ligados e um rico painel de instrumentos de métricas expressRoute importantes.

A secção **Recursos Ligados** lista os gateways e os pares configurados do ExpressRoute, que pode selecionar para navegar para a página de recursos correspondente.

:::image type="content" source="./media/expressroute-network-insights/linked-resources.png" alt-text="Screenshot do recurso ligado na página do monitor.":::


A secção **ExpressRoute Metrics** inclui gráficos de métricas de circuitos importantes nas categorias de **Disponibilidade**, **Produção,** **Gotas de Pacotes** e **Métricas gateway.**

### <a name="availability"></a>Disponibilidade

O separador *Disponibilidade* acompanha a disponibilidade de ARP e BGP, traçando os dados para todo o circuito e para a ligação individual (primário e secundário). 

:::image type="content" source="./media/expressroute-network-insights/arp-bgp-availability.png" alt-text="Screenshot de gráficos métricos de disponibilidade." lightbox="./media/expressroute-network-insights/arp-bgp-availability-expanded.png":::

### <a name="throughput"></a>Débito

Da mesma  forma, o separador Descodiá-lo traça a produção total de tráfego de entrada e saída para o circuito em bits/segundo. Também pode visualizar a produção para ligações individuais e cada tipo de observação configurada.

:::image type="content" source="./media/expressroute-network-insights/throughput.png" alt-text="Screenshot de gráficos métricos de produção." lightbox="./media/expressroute-network-insights/throughput-expanded.png":::

### <a name="packet-drops"></a>Remoção de Pacotes

O *separador Packet Drops* traça os bits/segundo caídos para entrar e entrar no tráfego através do circuito. Este separador fornece uma maneira fácil de monitorizar problemas de desempenho que podem ocorrer se precisar regularmente ou exceder a largura de banda do circuito.

:::image type="content" source="./media/expressroute-network-insights/dropped-packets.png" alt-text="Screenshot de gráficos de pacotes caídos." lightbox="./media/expressroute-network-insights/dropped-packets-expanded.png":::

### <a name="gateway-metrics"></a>Métricas do Gateway

Por último, o separador Gateway Metrics povoa-se com gráficos-chave para uma porta de entrada ExpressRoute selecionada (a partir da secção Recursos Ligados). Utilize este separador quando precisar de monitorizar a sua conectividade a redes virtuais específicas.

:::image type="content" source="./media/expressroute-network-insights/gateway-metrics.png" alt-text="Screenshot da produção de gateway e métricas de CPU." lightbox="./media/expressroute-network-insights/gateway-metrics-expanded.png":::

## <a name="next-steps"></a>Passos seguintes

Configurar a ligação do ExpressRoute.
  
* Saiba mais sobre [a Azure ExpressRoute](expressroute-introduction.md), [Network Insights](../azure-monitor/insights/network-insights-overview.md)e Network [Watcher](../network-watcher/network-watcher-monitoring-overview.md)
* [Criar e modificar um circuito](expressroute-howto-circuit-arm.md)
* [Criar e modificar a configuração de peering](expressroute-howto-routing-arm.md)
* [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
* [Personalize as suas métricas](expressroute-monitoring-metrics-alerts.md) e crie um Monitor de [Ligação](../network-watcher/connection-monitor-overview.md)
