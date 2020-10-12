---
title: Registos e métricas
titleSuffix: Azure Virtual WAN
description: Saiba mais sobre os registos e métricas de WAN virtuais do Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/05/2020
ms.author: cherylmc
ms.openlocfilehash: 78165e9c14d4a83dbc20cbccd2f31dc8ac4c79ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91440869"
---
# <a name="azure-virtual-wan-logs-and-metrics"></a>Registos e métricas Azure Virtual WAN

Pode monitorizar a Azure Virtual WAN utilizando o Azure Monitor. Virtual WAN é um serviço de networking que reúne muitas funcionalidades de networking, segurança e encaminhamento para fornecer uma única interface operacional. Os gateways virtuais wan VPN, gateways ExpressRoute e Azure Firewall têm registo e métricas disponíveis através do Azure Monitor. Para obter informações sobre a Azure Firewall, consulte [os registos e métricas do Azure Firewall](../firewall/logs-and-metrics.md).

Este artigo discute métricas e diagnósticos que estão disponíveis através do portal. As métricas são leves e podem suportar cenários perto de tempo real, tornando-os úteis para alertar e detetar rapidamente problemas.

## <a name="metrics"></a>Métricas

As métricas no Monitor Azure são valores numéricos que descrevem algum aspeto de um sistema num determinado momento. As métricas são recolhidas a cada minuto, e são úteis para alertar porque podem ser amostradas com frequência. Um alerta pode ser disparado rapidamente com uma lógica relativamente simples.

### <a name="site-to-site-vpn-gateways"></a>Gateways VPN site-to-site

As seguintes métricas estão disponíveis para gateways VPN site-to-site Azure:

* **Gateway Bandwidth** – Largura de banda agregada local-a-local média de um gateway in bytes por segundo.
* **Largura de banda do túnel** – Largura de banda média de um túnel em bytes por segundo.
* **Túnel Egress Bytes** - Bytes de saída de um túnel. 
* **Pacotes de Egress tunnel** - Contagem de pacotes de saída de um túnel. 
* **Tunnel Egress TS Desajuste Packet Drop** – Contagem de entrega de pacote de saída do seletor de tráfego desfasamento de um túnel. 
* **Túnel Ingress Bytes** – Bytes de entrada de um túnel. 
* **Túnel Ingress Packet** - Contagem de pacotes de entrada de um túnel. 
* **Tunnel Ingress TS Desajuste Packet Drop** – Contagem de entrega de pacote de entrada do seletor de tráfego desfasamento de um túnel. 

### <a name="point-to-site-vpn-gateways"></a>Gateways VPN ponto a local

As seguintes métricas estão disponíveis para gateways VPN ponto-a-local Azure:

* **Gateway P2S Bandwidth** – Largura de banda agregada média ponto-a-local de um gateway in bytes por segundo.
* **Contagem de ligação P2S** – contagem de ligação ponto-a-local de um gateway.

### <a name="azure-expressroute-gateways"></a>Gateways Azure ExpressRoute

As seguintes métricas estão disponíveis para gateways Azure ExpressRoute:

* **BitsInPerSecond** – Bits ingressando Azure por segundo.
* **BitsOutPerSecond** – Bits escamosing Azure por segundo.

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>Ver métricas de gateway

Os seguintes passos ajudam-no a localizar e a visualizar métricas:

1. No portal, navegue para o centro virtual que tem o portal.

2. Selecione **VPN (Site para site)** para localizar um gateway site-to-site, **ExpressRoute** para localizar um gateway ExpressRoute, ou **VPN do utilizador (ponto a local)** para localizar um gateway ponto-a-local. Na página, pode ver a informação do portal. Copie esta informação. Irá usá-lo mais tarde para visualizar diagnósticos usando o Azure Monitor.

3. Selecione **Métricas**.

   :::image type="content" source="./media/logs-metrics/metrics.png" alt-text="A screenshot mostra um site para o painel V P N com vista no Monitor Azure selecionado.":::

4. Na página **Métricas,** pode ver as métricas em que está interessado.

   :::image type="content" source="./media/logs-metrics/metrics-page.png" alt-text="A screenshot mostra um site para o painel V P N com vista no Monitor Azure selecionado.":::

## <a name="diagnostic-logs"></a><a name="diagnostic"></a>Registos de diagnósticos

### <a name="site-to-site-vpn-gateways"></a>Gateways VPN site-to-site

Estão disponíveis os seguintes diagnósticos para gateways VPN site-to-site Azure:

* **Gateway Diagnostic Logs** - Diagnósticos específicos do Gateway, tais como saúde, configuração, atualizações de serviço, bem como diagnósticos adicionais.
* **Registos de diagnóstico de túneis** – Estes são registos relacionados com túneis IPsec, tais como eventos de ligação e desconexão para um túnel IPsec local, SAs negociado, razões de desconexão, bem como diagnósticos adicionais.
* **Registos de Diagnóstico de Rotas** – Estes são registos relacionados com eventos para rotas estáticas, BGP, atualizações de rotas, bem como diagnósticos adicionais.
* **Registos de diagnóstico IKE** – diagnósticos específicos do IKE para ligações IPsec.

### <a name="point-to-site-vpn-gateways"></a>Gateways VPN ponto a local

Estão disponíveis os seguintes diagnósticos para gateways VPN ponto-a-local Azure:

* **Gateway Diagnostic Logs** - Diagnósticos específicos do Gateway, tais como saúde, configuração, atualizações de serviço, bem como outros diagnósticos.
* **Registos de diagnóstico IKE** – diagnósticos específicos do IKE para ligações IPsec.
* **Registos de diagnóstico P2S** – Estes são a configuração P2S do Utilizador (Ponto a Local) e eventos de clientes. Incluem a ligação/desconexão do cliente, a atribuição de endereços de cliente VPN, bem como outros diagnósticos.

### <a name="view-diagnostic-logs"></a><a name="diagnostic-steps"></a>Ver registos de diagnóstico

Os seguintes passos ajudam-no a localizar e a visualizar diagnósticos:

1. No portal, navegue para o seu recurso Virtual WAN. Na secção **Visão Geral** da página Geral WAN no portal, selecione **Essentials** para expandir a vista e obter informações de grupo de recursos. Copie a informação do grupo de recursos.

   :::image type="content" source="./media/logs-metrics/3.png" alt-text="A screenshot mostra um site para o painel V P N com vista no Monitor Azure selecionado.":::

2. Na secção de Monitorização, navegue para o grupo de recursos. Selecione **definições de diagnóstico**e, em seguida, insira as informações de recursos. Esta é a informação de recursos que copiou no Passo 2 da secção [de métricas do Gateway View,](#metrics-steps) no início deste artigo.

   :::image type="content" source="./media/logs-metrics/4.png" alt-text="A screenshot mostra um site para o painel V P N com vista no Monitor Azure selecionado.":::

3. Na página de resultados, selecione **+Adicionar a definição de diagnóstico**e, em seguida, selecione uma opção. Pode optar por enviar para Log Analytics, transmitir para um centro de eventos ou simplesmente arquivar para uma conta de armazenamento.

   :::image type="content" source="./media/logs-metrics/5.png" alt-text="A screenshot mostra um site para o painel V P N com vista no Monitor Azure selecionado.":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Consulta de amostra de Log Analytics

Os registos estão localizados no **espaço de trabalho Azure Log Analytics**. Pode configurar uma consulta no Log Analytics. O exemplo a seguir contém uma consulta para obter diagnósticos de rota local-a-local.

```AzureDiagnostics | where Category == "RouteDiagnosticLog"```

Substitua os valores abaixo, após o **= =**, conforme necessário.

* "GatewayDiagnosticLog"
* "IKEDiagnosticLog"
* "P2SDiagnosticLog"
* "TúnelDiagnosticLog"
* "RouteDiagnosticLog"

## <a name="activity-logs"></a><a name="activity-logs"></a>Troncos de atividade

**As** entradas de registo de atividade são recolhidas por predefinição e podem ser vistas no portal Azure. Pode utilizar registos de atividades da Azure (anteriormente conhecidos como *registos operacionais* e *registos de auditoria)* para visualizar todas as operações submetidas à sua subscrição do Azure.

## <a name="next-steps"></a>Passos seguintes

* Para aprender a monitorizar registos e métricas do Azure Firewall, consulte [Tutorial: Monitor Azure Firewall .](../firewall/tutorial-diagnostics.md)
* Para saber mais sobre as métricas no Azure Monitor, consulte [métricas no Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).
