---
title: 'Ver rotas eficazes de um hub virtual: Azure Virtual WAN [ Microsoft Docs'
description: Vie rotas eficazes para um hub virtual em Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 1173da81736661048d1e4e12d9919bc2aadf73ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515853"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>Ver rotas eficazes de um hub virtual

Pode ver todas as rotas do seu hub Virtual WAN no portal Azure. Para ver as rotas, navegue até ao centro virtual e, em seguida, selecione **Rotas eficazes de Encaminhamento -> Ver Rotas Eficazes**.

## <a name="understanding-routes"></a><a name="understand"></a>Compreender rotas

O exemplo que se segue pode ajudá-lo a entender melhor como o encaminhamento virtual wan aparece.

Neste exemplo, temos um WAN virtual com três hubs. O primeiro centro é na região leste dos EUA, o segundo centro é na região da Europa Ocidental, e o terceiro centro é na região oeste dos EUA. Num WAN virtual, todos os centros estão interligados. Neste exemplo, assumiremos que os centros dos EUA Oriental e da Europa Ocidental têm ligações a partir de sucursais no local (porta-vozes) e redes virtuais Azure (porta-vozes).

Um azure VNet falado (10.4.0.0.0/16) com um Aparelho Virtual rede (10.4.0.6) é ainda mais espreitado para um VNet (10.5.0.0.0/16). Consulte [informações adicionais](#abouthubroute) mais tarde neste artigo para obter mais informações sobre a tabela de rotas do hub.

Neste exemplo, assumimos também que o Ramo 1 da Europa Ocidental está ligado ao centro dos EUA Oriental, bem como ao centro da Europa Ocidental. Um circuito ExpressRoute no Leste dos EUA liga o Branch 2 ao centro dos EUA Oriental.

![diagrama](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view-effective-routes"></a><a name="view"></a>Ver rotas eficazes

Quando seleciona "Ver Rotas Eficazes" no portal, produz a saída mostrada na tabela de [rotas hub](#routetable) para o Centro dos EUA Oriental.

Para pôr isto em perspetiva, a primeira linha implica que o centro dos EUA Oriental aprendeu a rota de 10.20.1.0/24 (Filial 1) devido à ligação do tipo VPN *Next hop* ('Next hop' VPN Gateway Instance0 IP 10.1.0.6, Instância1 IP 10.1.0.7). *A Route Origin* aponta para o ID do recurso. *O Caminho AS* indica o Caminho AS para o Ramo 1.

### <a name="hub-route-table"></a><a name="routetable"></a>Mesa de rota do hub

Utilize a barra de deslocação na parte inferior da tabela para ver o "CAMINHO AS".

| **Prefixo** |  **Tipo de salto seguinte** | **Salto seguinte** |  **Rota de Origem** |**Caminho AS** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|VPN |10.1.0.6, 10.1.0.7| /subscrições/`<sub>`/recursosGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw| 20 000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/subscrições/`<sub>`/recursosGroups/`<rg>`/providers/Microsoft.Network/expressRouteGateways/444a6ac74e4d85555-eastus-gw|21000|
|10.23.1.0/24| VPN |10.1.0.6, 10.1.0.7|/subscrições/`<sub>`/recursosGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw|23000|
|10.4.0.0/16|Conexão de Rede Virtual| On-link |  |  |
|10.5.0.0/16| Endereço IP| 10.4.0.6|/subscrições/`<sub>`/recursosGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|0.0.0.0/0| Endereço IP| `<Azure Firewall IP>` |/subscrições/`<sub>`/recursosGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|10.22.1.0/16| Hub remoto|10.8.0.6, 10.8.0.7|/subscrições/`<sub>`/recursosGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_| 4848-22000 |
|10.9.0.0/16| Hub remoto|  On-link |/subscrições/`<sub>`/recursosGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_1| |

>[!NOTE]
> Se os centros dos EUA Orientais e da Europa Ocidental não se comunicassem entre si no exemplo da topologia, a rota aprendida (10.9.0.0/16) não existiria. Os hubs apenas anunciam redes que estejam diretamente ligadas a eles.
>

## <a name="additional-information"></a><a name="additional"></a>Informações adicionais

### <a name="about-the-hub-route-table"></a><a name="abouthubroute"></a>Sobre a mesa de rota do hub

Você pode criar uma rota de hub virtual e aplicar a rota para a tabela de rota do hub virtual. Pode aplicar várias rotas à tabela de rotas do hub virtual. Isto permite definir uma rota para o destino VNet através de um endereço IP (tipicamente o Aparelho Virtual rede (NVA) num VNet falado). Para mais informações sobre nVAs, consulte [o tráfego da Rota de um hub virtual para um NVA](virtual-wan-route-table-portal.md).

### <a name="about-default-route-00000"></a><a name="aboutdefaultroute"></a>Sobre a rota padrão (0.0.0.0/0)

Um hub virtual tem a capacidade de propagar uma rota padrão aprendida para uma rede virtual, uma VPN site-to-site, e uma ligação ExpressRoute se a bandeira estiver 'Activada' na ligação. Esta bandeira é visível quando edita uma ligação de rede virtual, uma ligação VPN ou uma ligação ExpressRoute. 'EnableInternetSecurity' é sempre falso por padrão nas ligações Hub VNet, ExpressRoute e VPN.

A rota padrão não tem origem no centro virtual WAN. A rota padrão é propagada se já for aprendida pelo centro virtual WAN como resultado da implementação de uma firewall no centro, ou se outro local conectado forçou a escavação ativada.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a WAN Virtual, veja a [Descrição Geral da WAN Virtual](virtual-wan-about.md).