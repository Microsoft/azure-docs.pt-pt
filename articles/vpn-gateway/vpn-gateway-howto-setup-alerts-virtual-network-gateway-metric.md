---
title: Como configurar alertas em métricas de Gateway de VPN do Azure
description: Passos para configurar alertas em métricas de Gateway de VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 890b096acba601ec20efaac21155da84e77a1f31
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769470"
---
# <a name="setting-up-alerts-on-vpn-gateway-metrics"></a>Configuração de alertas em métricas de Gateway de VPN

Este artigo ajuda-o a configurar alertas para as métricas de Gateway de VPN. O Azure monitor proporciona a capacidade de configurar alertas para recursos do Azure. Alertas podem ser definidos para gateways de rede virtual do tipo de "VPN".


|**Métricas**   | **Unit** | **Granularidade** | **Descrição** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Bytes/s  | 5 minutos| Média de utilização de largura de banda combinada de todas as ligações site a site no gateway.     |
|**P2SBandwidth**| Bytes/s  | 1 minuto  | Média de utilização de largura de banda combinada de todas as ligações ponto a site no gateway.    |
|**P2SConnectionCount**| Contagem  | 1 minuto  | Contagem de P2S ligações no gateway.   |
|**TunnelAverageBandwidth** | Bytes/s    | 5 minutos  | Média de utilização de largura de banda de túneis criada no gateway. |
|**TunnelEgressBytes** | Bytes | 5 minutos | Tráfego de saída em túneis criada no gateway.   |
|**TunnelEgressPackets** | Contagem | 5 minutos | Contagem de pacotes de saída em túneis criadas no gateway.   |
|**TunnelEgressPacketDropTSMismatch** | Contagem | 5 minutos | Contagem de pacotes de saída ignorados em túneis causados por erro de correspondência de TS. |
|**TunnelIngressBytes** | Bytes | 5 minutos | Tráfego de entrada em túneis criada no gateway.   |
|**TunnelIngressPackets** | Contagem | 5 minutos | Contagem de pacotes de entrada em túneis criadas no gateway.   |
|**TunnelIngressPacketDropTSMismatch** | Contagem | 5 minutos | Contagem de pacotes de entrada ignorados em túneis causados por erro de correspondência de TS. |


## <a name="setup"></a>Configurar alertas de Monitor do Azure com base em métricas com o portal

Os passos de exemplo abaixo irão criar um alerta num gateway para: <br>

**Métrica:** Largura de banda média do túnel <br>
**Condição:** largura de banda > 10 Bytes / segundo <br>
**Janela:** 5 minutos <br>
**Ação do alerta:** Email <br>



1. Navegue para o recurso de gateway de rede virtual e selecione "Alertas" no separador monitorização, em seguida, criar uma nova regra de alerta ou editar uma regra de alerta existente.

![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Create")

2. Selecione o gateway de VPN que o recurso.

![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "selecione")

3. Selecione uma métrica para o configurar para o alerta ![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "selecione")
4. Configure a lógica de sinal. Existem três componentes de lógica de sinal:

    a. Dimensões: Se a métrica tem dimensões, os valores de dimensão específicos podem ser selecionados para que o alerta avalia apenas dados daquela dimensão. Estes são opcionais.<br>
    b. Condição: A operação para avaliar o valor de métrica.<br>
    c. Hora: Especifique a granularidade de dados métricos e o período de tempo para avaliar o alerta no.<br>

![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "selecione")

5. Para ver as regras configuradas, clique em "Gerir regras de alerta" ![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "selecione")

## <a name="next-steps"></a>Passos Seguintes

Para configurar alertas em registos de diagnóstico de túnel, veja [como configurar alertas em registos de diagnóstico do Gateway de VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
