---
title: Monitorização, métricas e alertas - ExpressRoute do Azure | Documentos da Microsoft
description: Esta página fornece informações sobre a monitorização de ExpressRoute
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 92ec03e20fb6e681a0afd14048449ad004ebca0c
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991490"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitorização, métricas e alertas do ExpressRoute

Este artigo ajuda-o a compreender o ExpressRoute, monitorização, métricas e alertas com o Azure Monitor. Monitor do Azure é um meio único para todas as métricas, alertas, os registos de diagnóstico em todas as do Azure.
 
>[!NOTE]
>Usando **métricas clássico** não é recomendado.
>

## <a name="circuit-metrics"></a>Métricas de circuito

Para navegar até **métricas**, clique na página de ExpressRoute para o circuito que pretende monitorizar. Sob **monitorização**, pode ver a **métricas**. Selecione uma das métricas listadas abaixo. A agregação padrão será aplicada. Opcionalmente, você pode aplicar a divisão, que mostrará as métricas com dimensões diferentes.

### <a name="metrics-available"></a>Métricas disponíveis: 
* **Disponibilidade** 
    * Disponibilidade do ARP
      * Dimensões disponíveis:
        * Par (roteador de ExpressRoute primário/secundário)
        * Tipo de emparelhamento (privado/público/Microsoft)
    * Disponibilidade de BGP
      * Dimensões disponíveis:
        * Par (roteador de ExpressRoute primário/secundário)
        * Tipo de emparelhamento (privado/público/Microsoft)
* **Tráfico**
    * BitsInPerSecond
      * Dimensões disponíveis:
        * Tipo de emparelhamento (privado/público/Microsoft)
    * BitsOutPerSecond
      * Dimensões disponíveis:
        * Tipo de emparelhamento (privado/público/Microsoft)
    * GlobalReachBitsInPerSecond
      * Dimensões disponíveis:
        * Circuito emparelhado skey (chave de serviço)
    * GlobalReachBitsOutPerSecond
      * Dimensões disponíveis:
        * Circuito emparelhado skey (chave de serviço)

>[!NOTE]
>Usar *GlobalGlobalReachBitsInPerSecond* e *GlobalGlobalReachBitsOutPerSecond* só será visível se pelo menos uma conexão alcance global for estabelecida.
>

## <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bits de entrada e saída-métricas em todos os emparelhamentos

Você pode exibir as métricas em todos os emparelhamentos em um determinado circuito do ExpressRoute.

![Métricas de circuito](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

## <a name="bits-in-and-out---metrics-per-peering"></a>Métricas de entrada e saída de bits por emparelhamento

Pode ver as métricas para privado, público e peering em bits por segundo da Microsoft.

![Métricas por peering](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

## <a name="bgp-availability---split-by-peer"></a>Disponibilidade de BGP-divisão por par  

Você pode exibir quase a disponibilidade em tempo real do BGP entre emparelhamentos e pares (roteadores de ExpressRoute primários e secundários). Esse painel mostra a sessão BGP primária para emparelhamento privado e a segunda sessão BGP para emparelhamento privado. 

![Disponibilidade de BGP por ponto](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

## <a name="arp-availability---split-by-peering"></a>Disponibilidade ARP-divisão por emparelhamento  

Você pode exibir quase a disponibilidade em tempo real do [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) entre emparelhamentos e pares (roteadores de ExpressRoute primários e secundários). Este painel mostra a sessão ARP de emparelhamento privado em ambos os pares, mas é concluída para o emparelhamento da Microsoft entre emparelhamentos. A agregação padrão (média) foi utilizada em ambos os pares.  

![Disponibilidade ARP por ponto](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Ligações de gateway do ExpressRoute em bits/segundos

![ligações de gateway](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg ) 

## <a name="alerts-for-expressroute-gateway-connections"></a>Alertas para as ligações de gateway do ExpressRoute

1. Para configurar alertas, navegue até **do Azure Monitor**, em seguida, clique em **alertas**.

   ![alerts](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Clique em **+ selecionar destino** e selecione o recurso de ligação de gateway do ExpressRoute.

   ![destino]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Defina os detalhes do alerta.

   ![grupo de ação](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. Definir e adicionar o grupo de ação.

   ![Adicionar grupo de ação](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Alertas com base em cada peering

 ![o que](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Configurar alertas para os registos de atividades em circuitos

Na **critérios de alerta**, pode selecionar **registo de atividades** para o tipo de sinal e selecione o sinal.

  ![outro](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>Passos Seguintes

Configurar a ligação do ExpressRoute.
  
  * [Criar e modificar um circuito](expressroute-howto-circuit-arm.md)
  * [Criar e modificar a configuração de peering](expressroute-howto-routing-arm.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
