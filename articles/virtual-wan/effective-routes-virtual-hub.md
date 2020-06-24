---
title: 'Ver rotas eficazes de um hub virtual: Azure Virtual WAN / Microsoft Docs'
description: Vie rotas eficazes para um hub virtual em Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/02/2020
ms.author: cherylmc
ms.openlocfilehash: c7d436f2aecb021a7848ef0455a3f1c834cc38c1
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84750554"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>Ver rotas eficazes de um centro virtual

Pode ver todas as rotas do seu hub VIRTUAL WAN no portal Azure. Para ver as rotas, navegue até ao centro virtual e, em seguida, **selecione Rotas de encaminhamento -> Ver Rotas Eficazes**.

## <a name="understanding-routes"></a><a name="understand"></a>Compreender rotas

O exemplo a seguir pode ajudá-lo a entender melhor como o roteamento virtual WAN aparece.

Neste exemplo, temos um WAN virtual com três hubs. O primeiro hub é na região leste dos EUA, o segundo centro é na região da Europa Ocidental, e o terceiro centro é na região oeste dos EUA. Num WAN virtual, todos os centros estão interligados. Neste exemplo, partiremos do princípio de que os centros dos EUA e da Europa Ocidental têm ligações a partir de agências (porta-vozes) e redes virtuais Azure (porta-vozes).

Um Azure VNet (10.4.0.0/16) com um Aparelho Virtual de Rede (10.4.0.6) é ainda mais espreitado para um VNet (10.5.0.0/16). Consulte [informações adicionais](#abouthubroute) mais tarde neste artigo para obter mais informações sobre a tabela de rotas do hub.

Neste exemplo, assumimos também que o Ramo 1 da Europa Ocidental está ligado ao centro dos EUA oriental, bem como ao centro da Europa Ocidental. Um circuito ExpressRoute no Leste dos EUA liga o Ramo 2 ao centro leste dos EUA.

![diagrama](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view-effective-routes"></a><a name="view"></a>Ver rotas eficazes

Quando seleciona 'Ver Rotas Eficazes' no portal, produz a saída mostrada na tabela de [rotas hub](#routetable) para o Centro Leste dos EUA.

Para pôr isto em perspetiva, a primeira linha implica que o hub dos EUA oriental aprendeu a rota de 10.20.1.0/24 (Ramo 1) devido à ligação tipo de *lÚPD Next ('Next* hop' VPN Gateway Instance0 IP 10.1.0.6, Instância1 IP 10.1.0.7). *Route Origin* aponta para o ID do recurso. *O Caminho as* indica o caminho AS para o Ramo 1.

### <a name="hub-route-table"></a><a name="routetable"></a>Tabela de rota do hub

Utilize a barra de deslocamento na parte inferior da tabela para ver o "Caminho AS".

| **Prefixo** |  **Tipo de salto seguinte** | **Salto seguinte** |  **Origem da Rota** |**AS Path** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|VPN |10.1.0.6, 10.1.0.7| /subscrições/ `<sub>` /resourceGroups/ `<rg>` /providers/Microsoft.Network/vpnGateways/343a19aaaaa6ac74e4d81f05ccf1536cf-eastus-gw| 20 000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/subscrições/ `<sub>` /resourceGroups/ `<rg>` /providers/Microsoft.Network/expressRouteGateways/4444a6ac74e4d8555-eastus-gw|21000|
|10.23.1.0/24| VPN |10.1.0.6, 10.1.0.7|/subscrições/ `<sub>` /resourceGroups/ `<rg>` /providers/Microsoft.Network/vpnGateways/343a19aaaaa6ac74e4d81f05ccf1536cf-eastus-gw|23000|
|10.4.0.0/16|Conexão de rede virtual| Ligação on-link |  |  |
|10.5.0.0/16| Endereço IP| 10.4.0.6|/subscrições/ `<sub>` /resourceGroups/ `<rg>` /providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|0.0.0.0/0| Endereço IP| `<Azure Firewall IP>` |/subscrições/ `<sub>` /resourceGroups/ `<rg>` /providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|10.22.1.0/16| Hub Remoto|10.8.0.6, 10.8.0.7|/subscrições/ `<sub>` /resourceGroups/ `<rg>` /providers/Microsoft.Network/virtualHubs/westhub_| 4848-22000 |
|10.9.0.0/16| Hub Remoto|  Ligação on-link |/subscrições/ `<sub>` /resourceGroups/ `<rg>` /providers/Microsoft.Network/virtualHubs/westhub_1| |

>[!NOTE]
> Se os centros dos EUA e da Europa Ocidental não se comunicassem entre si na topologia exemplo, a rota aprendida (10.9.0.0/16) não existiria. Os hubs apenas anunciam redes que estão diretamente ligadas a eles.
>

## <a name="additional-information"></a><a name="additional"></a>Informação adicional

### <a name="about-the-hub-route-table"></a><a name="abouthubroute"></a>Sobre a mesa de rota do hub

Você pode criar uma rota de hub virtual e aplicar a rota para a tabela de rotas do hub virtual. Pode aplicar várias rotas à tabela de rotas do hub virtual. Isto permite-lhe definir uma rota para o destino VNet através de um endereço IP (normalmente o Aparelho Virtual de Rede (NVA) num VNet falado). Para obter mais informações sobre NVAs, consulte [o tráfego da Rota de um centro virtual para um NVA](virtual-wan-route-table-portal.md). Por favor, note que estas rotas não aparecerão na tabela de rotas eficaz. A tabela de rotas eficaz contém apenas os prefixos para centros locais e remotos, além do espaço de endereço de Rede Virtual conectado e rotas aprendidas via BGP.

### <a name="about-default-route-00000"></a><a name="aboutdefaultroute"></a>Sobre a rota predefinido (0.0.0.0/0)

Um hub virtual tem a capacidade de propagar uma rota padrão aprendida para uma rede virtual, uma VPN site-to-site e uma ligação ExpressRoute se a bandeira estiver 'Activada' na ligação. Esta bandeira é visível quando edita uma ligação de rede virtual, uma ligação VPN ou uma ligação ExpressRoute. 'EnableInternetSecurity' é sempre falso por padrão nas ligações Hub VNet, ExpressRoute e VPN.

A rota padrão não se origina no hub WAN virtual. A rota padrão é propagada se já for aprendida pelo hub virtual WAN como resultado da implantação de uma firewall no centro, ou se outro site conectado tiver feito um túnel forçado.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a WAN Virtual, veja a [Descrição Geral da WAN Virtual](virtual-wan-about.md).
