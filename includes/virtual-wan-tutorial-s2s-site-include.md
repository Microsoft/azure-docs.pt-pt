---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4bcee1097010bb8746b11185a470ca2584485c3f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73488974"
---
1. Na página do portal para o seu wan virtual, na secção **Conectividade,** selecione **sites VPN** para abrir a página de sites VPN.
2. Na página **Sites de VPN**, clique em **+Criar site**.

   ![Noções básicas](./media/virtual-wan-tutorial-site-include/basics.png "Noções básicas")
3. Na página **Site Create VPN,** no separador **Basics,** preencha os seguintes campos:

    * **Região** - Anteriormente referida como localização. Este é o local onde pretende criar este recurso do site.
    * **Nome** - O nome pelo qual pretende consultar o seu site no local.
    * Fornecedor de **dispositivos** - O nome do fornecedor de dispositivos VPN (por exemplo: Citrix, Cisco, Barracuda). Fazê-lo pode ajudar a Equipa Azure a entender melhor o seu ambiente para adicionar possibilidades adicionais de otimização no futuro, ou para ajudá-lo a resolver problemas.
    * **Border Gateway Protocol** - Enable implica que todas as ligações do site serão ativadas por BGP. Acabará por configurar as informações do BGP para cada link a partir do Site VPN na secção Links. Configurar o BGP num WAN virtual equivale a configurar o BGP numa VPN de gateway de rede virtual Azure. O seu endereço de pares BGP no local não deve ser o mesmo que o endereço IP público da sua VPN para dispositivo ou o espaço de endereço VNet do site VPN. Utilize um endereço IP diferente no dispositivo VPN para o seu IP de pares BGP. Pode ser um endereço atribuído à interface de loopback no dispositivo. No entanto, não pode ser um endereço APIPA (169.254.x.x). Especifique este endereço no site VPN correspondente que representa a localização. Para pré-requisitos de BGP, consulte [sobre bGP com Azure VPN Gateway](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). Pode sempre editar uma ligação VPN para atualizar os seus parâmetros BGP (Peering IP no link e o AS #) uma vez ativada a definição vpn site BGP.
    * Espaço de **endereços privados** - O espaço de endereço IP que está localizado no seu local no local. O tráfego destinado a este espaço de endereços é encaminhado para o site local. Isto é necessário quando o BGP não está ativado para o site.
    * **Hubs** - O centro a que pretende que o seu Site se conecte. Um site só pode ser ligado aos centros que têm um Gateway VPN. Se não vir um hub, por favor crie um gateway VPN naquele centro primeiro.
4. Selecione **Links** para adicionar informações sobre os links físicos no ramo. Se tiver um dispositivo CPE parceiro virtual wan, consulte com eles para ver se estas informações são trocadas com o Azure como parte do upload de informação do ramo configurado a partir dos seus sistemas.

   ![ligações](./media/virtual-wan-tutorial-site-include/links.png "Ligações")

    * **Link Name** - Um nome que pretende fornecer para o link físico no Site VPN. Exemplo: mylink1.
    * **Nome** do fornecedor - O nome do link físico no Site vpN. Exemplo: ATT, Verizon.
    * **Velocidade** - Esta é a velocidade do dispositivo VPN no local do ramo. Exemplo: 50, o que significa 50 Mbps é a velocidade do dispositivo VPN no local do ramo.
    * **Endereço IP** - Endereço IP público do dispositivo on-prem utilizando este link. Opcionalmente, pode fornecer o endereço IP privado do seu dispositivo VPN no local que está por trás do ExpressRoute.
5. Pode utilizar a caixa de verificação para eliminar ou adicionar links adicionais. São suportados quatro links por VPN Site. Por exemplo, se tiver quatro ISP (fornecedor de serviços de Internet) na localização da sucursal, pode criar quatro links. um por cada ISP, e fornecer a informação para cada link.
6. Depois de ter terminado o preenchimento dos campos, selecione **Review + crie** para verificar e criar o site.
7. Consulte o estado na página dos sites vpN. O site irá para **a Conexão Necessária** porque o site ainda não foi ligado ao centro.