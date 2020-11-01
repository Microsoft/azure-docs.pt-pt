---
title: 'Rede de ligação no local à rede virtual Azure: VPN site-to-site: Portal'
description: Crie uma ligação IPsec site-to-site VPN Gateway da sua rede no local para uma rede virtual Azure através da internet pública utilizando o portal.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/22/2020
ms.author: cherylmc
ms.openlocfilehash: b8c12048283cb8cc4220cb325762b272cde4a68f
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2020
ms.locfileid: "92541320"
---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Criar uma ligação Site a Site no portal do Azure

Este artigo mostra-lhe como utilizar o portal do Azure para criar uma ligação de gateway de VPN de Site para Site a partir da sua rede no local para a VNet. Os passos deste artigo aplicam-se ao modelo de implementação Resource Manager. Também pode criar esta configuração ao utilizar uma ferramenta de implementação diferente ou modelo de implementação ao selecionar uma opção diferente da lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-site-to-site-classic-portal.md)
>

Uma ligação de gateway de VPN de Site para Site é utilizada para ligar a sua rede no local a uma rede virtual do Azure através de um túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Este tipo de ligação requer um dispositivo VPN localizado no local que tenha um endereço IP público com acesso exterior atribuído ao mesmo. Para obter mais informações sobre o gateways de VPN, veja [About VPN gateway (Acerca do gateway de VPN)](vpn-gateway-about-vpngateways.md).

:::image type="content" source="./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png" alt-text="Diagrama da ligação de Gateway de Rede de VPNs em vários sites":::

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar a configuração, verifique se cumpre os seguintes critérios:

* Uma conta Azure com uma subscrição ativa. Se não tiver um, [crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Certifique-se de que tem um dispositivo VPN compatível e alguém que o possa configurar. Para obter mais informações sobre os dispositivos VPN compatíveis e a configuração do dispositivo, consulte [About VPN Devices (Acerca dos Dispositivos VPN)](vpn-gateway-about-vpn-devices.md).
* Verifique se tem um endereço IP IPv4 público com acesso exterior para o seu dispositivo VPN.
* Se não estiver familiarizado com os intervalos de endereços IP localizados na configuração de rede no local, tem de se coordenar com alguém que consiga fornecer esses detalhes. Ao criar esta configuração, tem de especificar prefixos de intervalo de endereços IP que o Azure irá encaminhar para a sua localização no local. Nenhuma das sub-redes da rede local pode sobrepor as sub-redes de rede virtual a que pretende ligar.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Criar uma rede virtual

Criar uma rede virtual (VNet) utilizando os seguintes valores:

* **Grupo de recursos:** TestRG1
* **Nome:** VNet1
* **Região:** (EUA) Leste dos EUA
* **Espaço de endereço IPv4:** 10.1.0.0/16
* **Nome da sub-rede:** FrontEnd
* **Espaço de endereço da sub-rede:** 10.1.0.0/24

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>Criar um gateway de VPN

Neste passo, vai criar o gateway de rede virtual da VNet. Criar um gateway, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

### <a name="about-the-gateway-subnet"></a>Sobre a sub-rede do gateway

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="create-the-gateway"></a>Criar o portal

Criar um gateway VPN utilizando os seguintes valores:

* **Nome:** VNet1GW
* **Região:** Leste dos EUA
* **Tipo de gateway:** VPN
* **Tipo de VPN:** baseado na rota
* **SKU:** VpnGw1
* **Geração:** Geração1
* **Rede virtual:** VNet1
* **Intervalo de endereço da sub-rede Gateway:** 10.1.255.0/27
* **Endereço IP público:** Criar novo
* **Nome do endereço IP público:** VNet1GWpip
* **Ativar o modo ativo:** Deficientes
* **Configure BGP:** Deficientes

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>Criar um gateway de rede local

O gateway de rede local é um objeto específico que representa a sua localização no local (o site) para fins de encaminhamento. Dê um nome ao site pelo qual o Azure se possa referir ao mesmo e especifique o endereço IP do dispositivo VPN no local para o qual vai criar uma ligação. Também pode especificar os prefixos do endereço IP que vai ser encaminhado através do gateway de VPN para o dispositivo VPN. Os prefixos do endereço que especificar são os que estão localizados na sua rede no local. Se a sua rede no local for alterada ou se precisar de alterar o endereço IP público para o dispositivo VPN, pode atualizar facilmente os valores mais tarde.

Criar um gateway de rede local utilizando os seguintes valores:

* **Nome:** Site1
* **Grupo de Recursos:** TestRG1
* **Localização:** Leste dos EUA

[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="configure-your-vpn-device"></a><a name="VPNDevice"></a>Configurar o dispositivo VPN

As ligações de Site a Site para uma rede no local requerem um dispositivo VPN. Neste passo, configure o seu dispositivo VPN. Ao configurar o seu dispositivo VPN, necessita dos seguintes valores:

* Uma chave partilhada. Esta é a mesma chave partilhada que especifica ao criar a ligação VPN de Site a Site. Nos nossos exemplos, iremos utilizar uma chave partilhada básica. Deve gerar uma chave mais complexa para utilizar.
* O endereço IP Público do gateway de rede virtual. Pode ver o endereço IP público através do portal do Azure, do PowerShell ou da CLI. Para encontrar o endereço IP público do seu gateway VPN utilizando o portal Azure, navegue para **gateways de rede Virtual,** em seguida, selecione o nome do seu gateway.

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="create-a-vpn-connection"></a><a name="CreateConnection"></a>Criar uma ligação VPN

Crie a ligação de Rede de VPNs entre o gateway de rede virtual e o dispositivo VPN no local.

Criar uma ligação utilizando os seguintes valores:

* **Nome do gateway de rede local:** Site1
* **Nome de ligação:** VNet1toSite1
* **Chave partilhada:** neste exemplo, utilizaremos abc123. No entanto, pode utilizar uma chave qualquer, desde que seja compatível com o seu hardware de VPN. O mais importante é que os valores correspondam em ambos os lados da ligação.

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="verify-the-vpn-connection"></a><a name="VerifyConnection"></a>Verificar a ligação VPN

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="how-to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Como ligar-se a uma máquina virtual

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>Como repor um gateway de VPN

Repor o gateway de VPN do Azure é útil se perder a conectividade VPN em vários locais num ou mais túneis de rede de VPNs. Nesta situação, os dispositivos VPN no local estão todos a funcionar corretamente, mas não podem estabelecer túneis IPsec com os gateways de VPN do Azure. Para obter os passos, veja [Reset a VPN gateway](reset-gateway.md) (Repor um gateway de VPN).

## <a name="how-to-change-a-gateway-sku-resize-a-gateway"></a><a name="resize"></a>Como alterar um SKU de gateway (redimensionar um gateway)

Para obter os passos para alterar um SKU de gateway, veja [Gateway SKUs](vpn-gateway-about-vpn-gateway-settings.md#gwsku) (SKUs de gateway).

## <a name="how-to-add-an-additional-connection-to-a-vpn-gateway"></a><a name="addconnect"></a>Como adicionar outra ligação a um gateway de VPN

Pode adicionar mais ligações, desde que nenhum desses espaços de endereços se sobreponha entre as ligações.

1. Para adicionar uma ligação adicional, navegue no gateway VPN e, em seguida, selecione **Connections** para abrir a página 'Ligações'.
1. **Selecione +Adicionar** para adicionar a sua ligação. Ajuste o tipo de ligação para refletir se é VNet a VNet (se estiver a ligar a outro gateway de VPN) ou Site a Site.
1. Se estiver a ligar através de Site a Site e ainda não tiver criado um gateway de rede local para o site ao qual se quer ligar, pode criar um novo.
1. Especifique a chave partilhada que pretende utilizar e, em seguida, selecione **OK** para criar a ligação.

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre o BGP, veja a [Descrição Geral do BGP](vpn-gateway-bgp-overview.md) e [Como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Para obter informações sobre túneis forçados, consulte [sobre o túnel forçado.](vpn-gateway-forced-tunneling-rm.md)
* Para obter informações sobre ligações Altamente Disponíveis Ativo-Ativo, veja [Premissas cruzadas de disponibilidade elevada e ligação VNet para VNet](vpn-gateway-highlyavailable.md).
* Para obter informações sobre como limitar o tráfego de rede para os recursos numa rede virtual, veja [Segurança da rede](../virtual-network/security-overview.md).
* Para obter informações sobre a forma como o Azure encaminha o tráfego entre os recursos do Azure, do local e da Internet veja [Encaminhamento de tráfego da rede virtual](../virtual-network/virtual-networks-udr-overview.md).
* Para obter informações sobre a criação de uma ligação VPN site-to-site utilizando o modelo do Gestor de Recursos Azure, consulte [Criar uma Ligação VPN site-to-site](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/).
* Para obter informações sobre a criação de uma ligação VNet-to-VNet VPN utilizando o modelo do Gestor de Recursos Azure, consulte [a replicação geografca de implementação HBase](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).
