---
title: Configurar alertas sobre as métricas do Azure VPN Gateway
description: Passos para configurar alertas sobre métricas VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 01704030ee3142cac9a328e1a3edc024651919a0
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82127914"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Configurar alertas sobre as métricas vpN Gateway

Este artigo ajuda-o a configurar alertas sobre as métricas do Azure VPN Gateway. O Azure Monitor fornece a capacidade de criar alertas para os recursos do Azure. Pode configurar alertas para gateways de rede virtuais do tipo "VPN".


|**Métrica**   | **Unidade** | **Granularidade** | **Descrição** | 
|---       | ---        | ---       | ---            | ---       |
|**Largura média de banda**| Bytes/s  | 5 minutos| Utilização média combinada da largura de banda de todas as ligações site-to-site no gateway.     |
|**Largura de banda p2S**| Bytes/s  | 1 minuto  | Utilização média combinada da largura de banda de todas as ligações ponto-a-local no portal.    |
|**Contagem de Conexões P2S**| Contagem  | 1 minuto  | Contagem de ligações ponto-a-local na porta de entrada.   |
|**TunnelAverageBandwidth** | Bytes/s    | 5 minutos  | Utilização média da largura de banda dos túneis criados na porta de entrada. |
|**TunnelEgressBytes** | Bytes | 5 minutos | Tráfego de saída em túneis criados na porta de entrada.   |
|**TunnelEgressPackets** | Contagem | 5 minutos | Contagem de pacotes de saída em túneis criados na porta de entrada.   |
|**TunnelEgressPacketDropTSMismatch** | Contagem | 5 minutos | A contagem de pacotes de saída caiu nos túneis causados pela incompatibilidade do selecionador de tráfego. |
|**TunnelIngressBytes** | Bytes | 5 minutos | Tráfego de entrada em túneis criados na porta de entrada.   |
|**TunnelIngressPackets** | Contagem | 5 minutos | Contagem de pacotes de entrada em túneis criados na porta de entrada.   |
|**Tunnelingresspacketdroptsmismatch** | Contagem | 5 minutos | A contagem de pacotes de entrada caiu nos túneis causados pela incompatibilidade do selecionador de tráfego. |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Configurar alertas do Monitor Azure com base nas métricas utilizando o portal Azure

Os seguintes passos de exemplo criarão um alerta sobre uma porta de entrada para:

- **Métrica:** TunnelAverageBandwidth
- **Condição:** Largura de banda > 10 bytes/segundo
- **Janela:** 5 minutos
- **Ação de alerta:** E-mail



1. Vá ao recurso de gateway da rede virtual e selecione **Alertas** do separador **Monitor.** Em seguida, crie uma nova regra de alerta ou edite uma regra de alerta existente.

   ![Seleções para criar uma regra de alerta](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Criar")

2. Selecione o seu gateway VPN como recurso.

   ![O botão Select e o gateway VPN na lista de recursos](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Selecione")

3. Selecione uma métrica para configurar para o alerta.

   ![Métrica selecionada na lista de métricas](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Selecione")
4. Configure a lógica do sinal. Há três componentes:

    a. **Dimensões**: Se a métrica tiver dimensões, pode selecionar valores de dimensão específicos para que o alerta avalie apenas dados dessa dimensão. Estes são opcionais.

    b. **Condição**: Esta é a operação para avaliar o valor métrico.

    c. **Tempo**: Especificar a granularidade dos dados métricos e o período de tempo para avaliar o alerta.

   ![Detalhes para configurar lógica de sinal](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Selecione")

5. Para ver as regras configuradas, selecione **Gerir as regras**de alerta .

   ![Botão para gestão das regras de alerta](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Selecione")

## <a name="next-steps"></a>Passos seguintes

Para configurar alertas nos registos de recursos do túnel, consulte [a configuração de alertas nos registos](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)de recursos vpn Gateway .
