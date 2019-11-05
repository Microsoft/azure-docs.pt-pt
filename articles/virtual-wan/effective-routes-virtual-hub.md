---
title: 'Exibir rotas efetivas de um hub virtual: WAN virtual do Azure | Microsoft Docs'
description: Requerer rotas efetivas para um hub virtual na WAN virtual do Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 1173da81736661048d1e4e12d9919bc2aadf73ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515853"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>Exibir rotas efetivas de um hub virtual

Você pode exibir todas as rotas do seu hub de WAN virtual no portal do Azure. Para exibir as rotas, navegue até o Hub virtual e selecione **Roteamento-> Exibir rotas efetivas**.

## <a name="understand"></a>Noções básicas sobre rotas

O exemplo a seguir pode ajudá-lo a entender melhor como o roteamento de WAN virtual aparece.

Neste exemplo, temos uma WAN virtual com três hubs. O primeiro Hub está na região leste dos EUA, o segundo Hub está na região Europa Ocidental e o terceiro Hub está na região oeste dos EUA. Em uma WAN virtual, todos os hubs são interconectados. Neste exemplo, vamos pressupor que os hubs leste dos EUA e Europa Ocidental tenham conexões de branches locais (spokes) e redes virtuais do Azure (spokes).

Um Azure VNet spoke (10.4.0.0/16) com uma solução de virtualização de rede (10.4.0.6) é ainda mais emparelhado com uma VNet (10.5.0.0/16). Consulte [informações adicionais](#abouthubroute) mais adiante neste artigo para obter mais informações sobre a tabela de rotas do Hub.

Neste exemplo, também presumimos que o Europa Ocidental Branch 1 esteja conectado ao Hub leste dos EUA, bem como ao Hub de Europa Ocidental. Um circuito do ExpressRoute no leste dos EUA conecta a ramificação 2 ao Hub leste dos EUA.

![Organograma](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view"></a>Exibir rotas efetivas

Quando você seleciona ' Exibir rotas efetivas ' no portal, ele produz a saída mostrada na [tabela de rotas do Hub](#routetable) para o Hub leste dos EUA.

Para colocar isso em perspectiva, a primeira linha implica que o Hub leste dos EUA aprendeu a rota de 10.20.1.0/24 (ramificação 1) devido à conexão de *tipo de próximo salto* de VPN (' próximo salto ', gateway de VPN Instance0 IP 10.1.0.6, instance1 IP 10.1.0.7). A *origem da rota* aponta para a ID do recurso. As *path* indica o caminho as para a ramificação 1.

### <a name="routetable"></a>Tabela de rotas do Hub

Use a barra de rolagem na parte inferior da tabela para exibir o "caminho AS".

| **Prefixo** |  **Tipo de salto seguinte** | **Salto seguinte** |  **Origem da rota** |**Caminho AS** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|VPN |10.1.0.6, 10.1.0.7| /subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw| 20000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/expressRouteGateways/4444a6ac74e4d85555-eastus-gw|21000|
|10.23.1.0/24| VPN |10.1.0.6, 10.1.0.7|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw|23000|
|10.4.0.0/16|Conexão de rede virtual| No link |  |  |
|10.5.0.0/16| Endereço IP| 10.4.0.6|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|0.0.0.0/0| Endereço IP| `<Azure Firewall IP>` |/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|10.22.1.0/16| Hub remoto|10.8.0.6, 10.8.0.7|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_| 4848-22000 |
|10.9.0.0/16| Hub remoto|  No link |/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_1| |

>[!NOTE]
> Se o leste dos EUA e os hubs de Europa Ocidental não estivessem se comunicando entre si na topologia de exemplo, a rota aprendida (10.9.0.0/16) não existiria. Os hubs só anunciam redes que estão conectadas diretamente a elas.
>

## <a name="additional"></a>Informações adicionais

### <a name="abouthubroute"></a>Sobre a tabela de rotas do Hub

Você pode criar uma rota de Hub virtual e aplicar a rota à tabela de rotas do Hub virtual. Pode aplicar várias rotas à tabela de rotas do hub virtual. Isso permite que você defina uma rota para VNet de destino por meio de um endereço IP (normalmente a NVA (solução de virtualização de rede) em uma VNet de spoke). Para obter mais informações sobre NVAs, consulte [rotear o tráfego de um hub virtual para um NVA](virtual-wan-route-table-portal.md).

### <a name="aboutdefaultroute"></a>Sobre a rota padrão (0.0.0.0/0)

Um hub virtual tem a capacidade de propagar uma rota padrão aprendida para uma rede virtual, uma VPN site a site e uma conexão de ExpressRoute se o sinalizador for ' Enabled ' na conexão. Esse sinalizador fica visível quando você edita uma conexão de rede virtual, uma conexão VPN ou uma conexão de ExpressRoute. ' EnableInternetSecurity ' é sempre false por padrão nas conexões VNet, ExpressRoute e VPN do Hub.

A rota padrão não é originada no Hub WAN virtual. A rota padrão será propagada se já tiver sido aprendida pelo hub de WAN virtual como resultado da implantação de um firewall no Hub ou se outro site conectado tiver o túnel forçado habilitado.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a WAN Virtual, veja a [Descrição Geral da WAN Virtual](virtual-wan-about.md).