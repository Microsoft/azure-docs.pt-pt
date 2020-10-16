---
title: Ver métricas do Gateway Azure VPN
description: Passos para ver as métricas do Gateway VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 4b33af3e64726e124373f57920836bce145cd891
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89443182"
---
# <a name="view-vpn-gateway-metrics"></a>Ver métricas do Gateway de VPN

Pode monitorizar as portas Azure VPN utilizando o Monitor Azure. Este artigo discute métricas que estão disponíveis através do portal. As métricas são leves e podem suportar cenários perto de tempo real, tornando-os úteis para alertar e detetar rapidamente problemas.


|**Métrica**   | **Unidade** | **Granularidade** | **Descrição** | 
|---       | ---        | ---       | ---            | ---       |
|**Largura média da largura**| Bytes/s  | 5 minutos| Utilização média combinada da largura de banda de todas as ligações site-to-site no gateway.     |
|**P2SBandwidth**| Bytes/s  | 1 minuto  | Utilização média combinada da largura de banda de todas as ligações ponto-a-local no gateway.    |
|**P2SConnectionCount**| Contagem  | 1 minuto  | Contagem de ligações ponto-a-local no portal.   |
|**TunnelAverageBandwidth** | Bytes/s    | 5 minutos  | Utilização média da largura de banda dos túneis criados no portal. |
|**TunnelEgressBytes** | Bytes | 5 minutos | Tráfego de saída em túneis criados no portal.   |
|**TunnelEgressPackets** | Contagem | 5 minutos | Contagem de pacotes de saída em túneis criados no portal.   |
|**TunnelEgressPacketDropTSMismatch** | Contagem | 5 minutos | Contagem de pacotes de saída caídos em túneis causados por desfasamento do selecionador de trânsito. |
|**TúnelIngsBytes** | Bytes | 5 minutos | Tráfego de entrada em túneis criados no portal.   |
|**Pacotes de escavação** | Contagem | 5 minutos | Contagem de pacotes de entrada em túneis criados no portal.   |
|**TunnelIngressPacketDropTSMismatch** | Contagem | 5 minutos | Contagem de pacotes de entrada caídos em túneis causados por desfasamento do seletor de trânsito. |

## <a name="the-following-steps-help-you-locate-and-view-metrics"></a>Os seguintes passos ajudam-no a localizar e a visualizar métricas:

1. Navegue para o recurso de gateway de rede virtual no Portal
2. Selecione **a visão geral** para ver as métricas totais de entrada e saída do túnel.

   ![Seleções para criar uma regra de alerta](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/overview.png "Vista")

3. Para ver qualquer uma das outras métricas listadas acima. Clique na secção **Métricas** sob o seu recurso de gateway de rede virtual e selecione a métrica da lista de drop down.

   ![O botão Select e a porta de entrada VPN na lista de recursos](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/metrics.png "Selecione")

## <a name="next-steps"></a>Passos seguintes

Para configurar alertas sobre as métricas dos túneis, consulte [Configurar alertas nas métricas do Gateway VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).

Para configurar alertas nos registos de recursos do túnel, consulte [Configurar alertas nos registos de recursos do Gateway VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
