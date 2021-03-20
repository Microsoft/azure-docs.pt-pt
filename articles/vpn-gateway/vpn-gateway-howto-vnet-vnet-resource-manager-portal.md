---
title: 'Configure uma ligação VNet-to-VNet VPN Gateway: Portal Azure'
description: Crie uma ligação do Gateway de VPN entre VNets com o Gestor de Recursos e o Portal do Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/19/2020
ms.author: cherylmc
ms.openlocfilehash: 465d877da48e0d7027dbba6615302af32c6bb154
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98872405"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>Configure uma ligação de gateway VNet-to-VNet VPN utilizando o portal Azure

Este artigo ajuda-o a ligar redes virtuais (VNets) utilizando o tipo de ligação VNet-to-VNet. As redes virtuais podem estar em diferentes regiões e de diferentes subscrições. Quando conecta VNets de diferentes subscrições, as subscrições não precisam de ser associadas ao mesmo inquilino ative. 

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet-vnet-diagram.png" alt-text="Diagrama VNet para VNet":::

Os passos deste artigo aplicam-se ao modelo de implementação do Gestor de Recursos Azure e utilizam o portal Azure. Pode criar esta configuração com uma ferramenta ou modelo de implementação diferente utilizando opções descritas nos seguintes artigos:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [CLI do Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Ligue diferentes modelos de implementação - portal do Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Ligue diferentes modelos de implementação - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a>Sobre a ligação de VNets

As secções seguintes descrevem as diferentes formas de ligar redes virtuais.

### <a name="vnet-to-vnet"></a>VNet a VNet

Configurar uma ligação VNet-vNet é uma forma simples de ligar VNets. Quando liga uma rede virtual a outra rede virtual com um tipo de ligação VNet-to-VNet (VNet2VNet), é semelhante à criação de uma ligação IPsec site-to-site a uma localização no local. Ambos os tipos de ligação utilizam uma porta de entrada VPN para fornecer um túnel seguro com IPsec/IKE e funcionam da mesma forma ao comunicar. No entanto, diferem na forma como o portal de rede local está configurado. 

Quando cria uma ligação VNet-para-VNet, o espaço de endereço de gateway de rede local é automaticamente criado e povoado. Se atualizar o espaço de endereço para um VNet, o outro VNet liga automaticamente para o espaço de endereço atualizado. É tipicamente mais rápido e fácil criar uma ligação VNet-vNet do que uma ligação Site-to-Site.

### <a name="site-to-site-ipsec"></a>Site a Site (IPsec)

Se estiver a trabalhar com uma configuração de rede complicada, poderá preferir ligar os seus VNets utilizando uma [ligação Site-to-Site.](./tutorial-site-to-site-portal.md) Quando segue os passos IPsec site-to-site, cria e configura manualmente os gateways de rede locais. O gateway de rede local para cada VNet trata a outra VNet como um site local. Estes passos permitem especificar espaços de endereço adicionais para a porta de entrada da rede local para o tráfego de rotas. Se o espaço de endereço de um VNet mudar, deve atualizar manualmente o portal de rede local correspondente.

### <a name="vnet-peering"></a>VNet peering

Também pode ligar os seus VNets utilizando o peering VNet. O espremiá-lo vNet não usa um gateway VPN e tem constrangimentos diferentes. Além disso, o [preço do VNet peering](https://azure.microsoft.com/pricing/details/virtual-network) é calculado de forma diferente que os [preços dos Gateways VPN de VNet a VNet](https://azure.microsoft.com/pricing/details/vpn-gateway). Para obter mais informações, veja [VNet peering](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a>Porquê criar uma ligação VNet a VNet?

Pode querer ligar redes virtuais utilizando uma ligação VNet-vNet pelas seguintes razões:

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>Geopresença e georredundância entre várias regiões

* Pode configurar a sua própria geo-replicação ou sincronização com conectividade segura sem passar por pontos finais virados para a Internet.
* Com o Azure Traffic Manager e o Azure Load Balancer, pode configurar uma carga de trabalho altamente disponível com geodussobrendância em várias regiões do Azure. Por exemplo, pode configurar grupos de disponibilidade SQL Server Always On de várias regiões do Azure.

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>Aplicações regionais multi-camadas com limites de isolamento ou administrativo

* Dentro da mesma região, pode configurar aplicações multi-níveis com múltiplas redes virtuais que estão ligadas em conjunto devido a requisitos de isolamento ou administrativos.

A comunicação VNet a VNet pode ser combinada com configurações multilocal. Estas configurações permitem estabelecer topologias de rede que combinam conectividade entre premissas com conectividade de rede inter-virtual, como mostra o seguinte diagrama:

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections-diagram.png" alt-text="Diagrama de ligações VNet":::

Este artigo mostra-lhe como ligar VNets utilizando o tipo de ligação VNet-vNet. Quando segue estes passos como exercício, pode utilizar os seguintes valores de definição de exemplo. No exemplo, as redes virtuais estão na mesma subscrição, mas em grupos de recursos diferentes. Se a suas VNets estiverem em diferentes subscrições, não pode criar a ligação no portal. Utilize [o PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) ou [o CLI.](vpn-gateway-howto-vnet-vnet-cli.md) Para obter mais informações sobre as ligações VNet-to-VNet, consulte [as FAQ VNet-to-VNet](#vnet-to-vnet-faq).

### <a name="example-settings"></a>Definições de exemplo

**Valores para VNet1:**

* **Definições de rede virtuais**
  * **Nome**: VNet1
  * **Espaço de** endereço : 10.1.0.0/16
  * **Subscrição**: selecione a subscrição que quer utilizar.
  * **Grupo de recursos**: TestRG1
  * **Localização**: Leste dos EUA
  * **Sub-rede**
    * **Nome**: FrontEnd
    * **Intervalo de endereços**: 10.1.0.0/24

* **Definições de gateway de rede virtual**
  * **Nome**: VNet1GW
  * **Grupo de recursos**: Leste dos EUA
  * **Geração**: Geração 1
  * **Tipo de gateway**: selecione **VPN**.
  * **Tipo VPN**: Selecione **Route*based**.
  * **SKU**: VpnGw1
  * **Rede virtual**: VNet1
  * **Intervalo de endereço da sub-rede Gateway**: 10.1.255.0/27
  * **Endereço IP público**: Criar novo
  * **Nome do endereço IP público**: VNet1GWpip

* **Ligação**
  * **Nome**: VNet1toVNet4
  * **Chave partilhada**: Pode criar a chave partilhada por si mesmo. Quando se cria a ligação entre os VNets, os valores devem coincidir. Para este exercício, use o abc123.

**Valores para VNet4:**

* **Definições de rede virtuais**
  * **Nome**: VNet4
  * **Espaço de** endereço : 10.41.0.0/16
  * **Subscrição**: selecione a subscrição que quer utilizar.
  * **Grupo de recursos**: TestRG4
  * **Localização**: West US
  * **Sub-rede**
  * **Nome**: FrontEnd
  * **Intervalo de endereços**: 10.41.0.0/24

* **Definições de gateway de rede virtual**
  * **Nome**: VNet4GW
  * **Grupo de recursos**: West US
  * **Geração**: Geração 1
  * **Tipo de gateway**: selecione **VPN**.
  * **Tipo VPN**: Selecione **Route-based**.
  * **SKU**: VpnGw1
  * **Rede virtual**: VNet4
  * **Intervalo de endereço da sub-rede Gateway**: 10.41.255.0/27
  * **Endereço IP público**: Criar novo
  * **Nome do endereço IP público**: VNet4GWpip

* **Ligação**
  * **Nome**: VNet4toVNet1
  * **Chave partilhada**: Pode criar a chave partilhada por si mesmo. Quando se cria a ligação entre os VNets, os valores devem coincidir. Para este exercício, use o abc123.

## <a name="create-and-configure-vnet1"></a>Criar e configurar vNet1

Se já tiver uma VNet, certifique-se de que as definições são compatíveis com a conceção do seu gateway de VPN. Tenha em especial atenção as sub-redes que se possam sobrepor a outras redes. A sua ligação não funcionará corretamente se tiver sub-redes sobrepostas.

### <a name="to-create-a-virtual-network"></a>Para criar uma rede virtual

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-the-vnet1-gateway"></a>Criar o portal VNet1

Neste passo, vai criar o gateway de rede virtual da VNet. Criar um gateway, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway selecionado. Se estiver a criar esta configuração como exercício, consulte as [definições de Exemplo](#example-settings).

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="to-create-a-virtual-network-gateway"></a>Para criar um gateway de rede virtual

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-and-configure-vnet4"></a>Criar e configurar vNet4

Depois de configurar o VNet1, crie o VNet4 e o gateway VNet4 repetindo os passos anteriores e substituindo os valores por valores VNet4. Não é preciso esperar até que a porta de entrada de rede virtual para o VNet1 termine de criar antes de configurar o VNet4. Se estiver a utilizar os seus próprios valores, certifique-se de que os espaços de endereço não se sobrepõem a nenhum dos VNets aos quais pretende ligar.

## <a name="configure-the-vnet1-gateway-connection"></a>Configure a ligação de gateway VNet1

Quando os gateways de rede virtuais para VNet1 e VNet4 estiverem concluídos, pode criar as suas ligações de gateway de rede virtual. Nesta secção, vai criar uma ligação da VNet1 à VNet4. Estes passos só funcionam em VNets na mesma subscrição. Se os seus VNets estiverem em subscrições diferentes, deve utilizar [o PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) para escoar a ligação. No entanto, se os seus VNets estiverem em diferentes grupos de recursos na mesma subscrição, pode conectá-los utilizando o portal.

1. No portal Azure, selecione **Todos os recursos**, introduza o gateway de rede *virtual* na caixa de pesquisa e, em seguida, navegue para o portal de rede virtual para o seu VNet. Por exemplo, **VNet1GW**. Selecione o portal para abrir a página **de gateway de rede Virtual.**
1. Na página gateway, aceda a **Definições ->Conexões**. Em seguida, selecione **+Adicionar**.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections.png" alt-text="Página de ligações":::
1. A página **de ligação Add** abre.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet1-vnet4.png" alt-text="Adicionar ligação":::

   Na página **de ligação Adicionar,** preencha os valores para a sua ligação:

   * **Nome**: Introduza um nome para a sua ligação. Por exemplo, *VNet1toVNet4*.

   * **Tipo de ligação**: Selecione **VNet-para-VNet** a partir do drop-down.

   * **Primeiro gateway de rede virtual**: Este valor de campo é preenchido automaticamente porque está a criar esta ligação a partir do gateway de rede virtual especificado.

   * **Segundo gateway de rede virtual**: Este campo é o portal de rede virtual do VNet ao qual pretende criar uma ligação. **Selecione Escolha outra porta de entrada de rede virtual** para abrir a página de gateway de rede virtual **Escolha.**

      :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/choose.png" alt-text="Escolher um gateway":::

     * Veja os gateways de rede virtual que se encontram listados nesta página. Tenha em atenção que estão listados apenas os gateways da rede virtual na sua subscrição. Se pretender ligar-se a um gateway de rede virtual que não esteja na sua subscrição, utilize o [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

     * Selecione a porta de entrada de rede virtual à qual pretende ligar.

   * **Tecla partilhada (PSK)**: Neste campo, introduza uma chave partilhada para a sua ligação. Pode gerar ou criar esta chave de forma independente. Numa ligação site-to-site, a chave que utiliza é a mesma para o seu dispositivo no local e a sua ligação virtual gateway de rede. O conceito é semelhante aqui, exceto que em vez de se ligar a um dispositivo VPN, você está conectando-se a outro portal de rede virtual.
1. Selecione **OK** para guardar as alterações.

## <a name="configure-the-vnet4-gateway-connection"></a>Configure a ligação de gateway VNet4

Em seguida, crie uma ligação de VNet4 a VNet1. No portal, localize o portal de rede virtual associado ao VNet4. Siga os passos da secção anterior, substituindo os valores para criar uma ligação de VNet4 a VNet1. Certifique-se de que utiliza a mesma chave partilhada.

## <a name="verify-your-connections"></a>Verifique as suas ligações

1. Localize o portal de rede virtual no portal Azure. 
1. Na página **de gateway de rede Virtual,** selecione **Connections** para ver a página **Connections** para o gateway de rede virtual. Após a ligação ser estabelecida, verá os valores **de Estado** mudarem para **Connected**.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/view-connections.png" alt-text="Verificar ligações":::
1. Sob a coluna **Nome,** selecione uma das ligações para visualizar mais informações. Quando os dados começarem a fluir, verá os valores **de Data in** e Data **out**.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/status.png" alt-text="Screenshot mostra um grupo de recursos com valores para Data in e Data out":::

## <a name="add-additional-connections"></a>Adicionar ligações adicionais

Se pretender adicionar ligações adicionais, navegue para o gateway de rede virtual a partir do qual pretende criar a ligação e, em seguida, selecione **Connections**. Pode criar outra ligação VNet a VNet ou criar uma ligação Site a Site IPsec para uma localização no local. Certifique-se de que ajusta o **Tipo de ligação** de modo a corresponder ao tipo de ligação que quer criar. Antes de criar ligações adicionais, verifique se o espaço de endereço da sua rede virtual não se sobrepõe a nenhum dos espaços de endereço a que pretende ligar. Para obter os passos para criar uma ligação Site a Site, consulte [Criar uma ligação Site a Site](./tutorial-site-to-site-portal.md).

## <a name="vnet-to-vnet-faq"></a>FAQ da ligação VNet a VNet

Veja os detalhes das FAQ para obter informações adicionais sobre ligações VNet a VNet.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre como pode limitar o tráfego de rede a recursos numa rede virtual, consulte [a Segurança da Rede.](../virtual-network/network-security-groups-overview.md)

* Para obter informações sobre a forma como o Azure encaminha o tráfego entre os recursos do Azure, do local e da Internet veja [Encaminhamento de tráfego da rede virtual](../virtual-network/virtual-networks-udr-overview.md).