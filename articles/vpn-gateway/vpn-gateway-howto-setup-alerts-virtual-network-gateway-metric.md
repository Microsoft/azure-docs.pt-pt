---
title: Configure alertas em métricas de Gateway de VPN do Azure
description: Passos para configurar alertas em métricas de Gateway de VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: e54dadbda0582095e8152ea30376d369177bfd86
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65509896"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Configure alertas em métricas de Gateway de VPN

Este artigo ajuda-o a configurar alertas em métricas de Gateway de VPN do Azure. O Azure Monitor proporciona a capacidade de configurar alertas para recursos do Azure. Pode configurar alertas para gateways de rede virtual do tipo "VPN".


|**Métricas**   | **Unit** | **Granularidade** | **Descrição** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Bytes/s  | 5 minutos| Média de utilização de largura de banda combinada de todas as ligações site a site no gateway.     |
|**P2SBandwidth**| Bytes/s  | 1 minuto  | Média de utilização de largura de banda combinada de todas as ligações ponto a site no gateway.    |
|**P2SConnectionCount**| Count  | 1 minuto  | Contagem de ligações de ponto a site no gateway.   |
|**TunnelAverageBandwidth** | Bytes/s    | 5 minutos  | Média de utilização de largura de banda de túneis criada no gateway. |
|**TunnelEgressBytes** | Bytes | 5 minutos | Tráfego de saída em túneis criada no gateway.   |
|**TunnelEgressPackets** | Count | 5 minutos | Contagem de pacotes de saída em túneis criadas no gateway.   |
|**TunnelEgressPacketDropTSMismatch** | Count | 5 minutos | Contagem de pacotes de saída ignorados em túneis causados por erro de correspondência de Seletor de tráfego. |
|**TunnelIngressBytes** | Bytes | 5 minutos | Tráfego de entrada em túneis criada no gateway.   |
|**TunnelIngressPackets** | Count | 5 minutos | Contagem de pacotes de entrada em túneis criadas no gateway.   |
|**TunnelIngressPacketDropTSMismatch** | Count | 5 minutos | Contagem de pacotes de entrada ignorados em túneis causados por erro de correspondência de Seletor de tráfego. |


## <a name="setup"></a>Configurar alertas do Azure Monitor com base nas métricas ao utilizar o portal do Azure

Os seguintes passos de exemplo irão criar um alerta num gateway para:

- **Métrica:** TunnelAverageBandwidth
- **Condição:** Largura de banda > 10 bytes / segundo
- **Janela:** 5 minutos
- **Ação do alerta:** Correio Electrónico



1. Vá para o recurso de gateway de rede virtual e selecione **alertas** partir do **monitorização** separador. Em seguida, criar uma nova regra de alerta ou editar uma regra de alerta existente.

   ![Seleções para a criação de uma regra de alerta](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Create")

2. Selecione o gateway de VPN que o recurso.

   ![O botão de seleção e o gateway VPN na lista de recursos](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "selecione")

3. Selecione uma métrica para o configurar para o alerta.

   ![Selecionar métrica na lista de métricas](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "selecione")
4. Configure a lógica de sinal. Existem três componentes à mesma:

    a. **Dimensões**: Se a métrica tem dimensões, pode selecionar os valores de dimensão específicos para que o alerta é avaliada apenas os dados daquela dimensão. Estes são opcionais.

    b. **Condição**: Esta é a operação para avaliar o valor de métrica.

    c. **Tempo**: Especifique a granularidade de dados métricos e o período de tempo para avaliar o alerta.

   ![Detalhes de configuração sinalizam logic](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "selecione")

5. Para ver as regras configuradas, selecione **gerir regras de alerta**.

   ![Botão para o gerenciamento de regras de alerta](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "selecione")

## <a name="next-steps"></a>Passos Seguintes

Para configurar alertas em registos de diagnóstico de túnel, veja [configurar alertas em registos de diagnóstico do Gateway de VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
