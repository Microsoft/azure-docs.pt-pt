---
title: Configurar alertas sobre as métricas do Gateway Azure VPN
description: Saiba como usar o portal Azure para configurar alertas do Azure Monitor com base em métricas para gateways VPN de rede virtual.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 05fbc5675d6ee3b6720d9db9e07e7010cf1d9172
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89435662"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Configurar alertas sobre métricas VPN Gateway

Este artigo ajuda-o a configurar alertas sobre as métricas do Gateway Azure VPN. O Azure Monitor fornece a capacidade de configurar alertas para os recursos do Azure. Pode configurar alertas para gateways de rede virtuais do tipo "VPN".


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


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Configurar alertas do Azure Monitor com base em métricas utilizando o portal Azure

Os seguintes passos de exemplo criarão um alerta num gateway para:

- **Métrica:** TunnelAverageBandwidth
- **Condição:** Largura de banda > 10 bytes/segundo
- **Janela:** 5 minutos
- **Ação de alerta:** Email



1. Aceda ao recurso de gateway de rede virtual e **selecione Alertas** a partir do **separador Monitor.** Em seguida, crie uma nova regra de alerta ou edite uma regra de alerta existente.

   ![Seleções para criar uma regra de alerta](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Criar")

2. Selecione o seu gateway VPN como o recurso.

   ![O botão Select e a porta de entrada VPN na lista de recursos](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Selecione")

3. Selecione uma métrica para configurar para o alerta.

   ![Métrica selecionada na lista de métricas](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Selecione")
4. Configure a lógica do sinal. Há três componentes:

    a. **Dimensões**: Se a métrica tiver dimensões, pode selecionar valores de dimensão específicas para que o alerta avalie apenas dados dessa dimensão. Estes são opcionais.

    b. **Condição**: Esta é a operação para avaliar o valor métrico.

    c. **Tempo**: Especificar a granularidade dos dados métricos e o período de tempo para avaliar o alerta.

   ![Detalhes para configurar a lógica do sinal](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Selecione")

5. Para ver as regras configuradas, **selecione Gerir as regras de alerta**.

   ![Botão para gerir regras de alerta](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Selecione")

## <a name="next-steps"></a>Passos seguintes

Para configurar alertas nos registos de recursos do túnel, consulte [Configurar alertas nos registos de recursos do Gateway VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
