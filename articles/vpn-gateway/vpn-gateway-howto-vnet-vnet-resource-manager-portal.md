---
title: 'Configurar uma conexão de gateway de VPN de VNet para VNet: portal do Azure'
description: Crie uma ligação do Gateway de VPN entre VNets com o Gestor de Recursos e o Portal do Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: cherylmc
ms.openlocfilehash: c8ed01af9574ce10c95fb4af61f1da1c72c858ad
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75860508"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>Configurar uma ligação de gateway VPN de VNet a VNet com o portal do Azure

Este artigo ajuda-o a ligar redes virtuais (VNets) com o tipo de ligação de VNet a VNet. Redes virtuais podem estar em regiões diferentes e de subscrições diferentes. Ligar VNets de diferentes subscrições, as subscrições não têm de estar associado ao mesmo inquilino do Active Directory. 

![Diagrama v2v](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

Os passos neste artigo aplicam-se ao modelo de implementação Azure Resource Manager e utilizam o portal do Azure. Pode criar esta configuração com uma ferramenta de implementação diferente ou um modelo ao utilizar as opções que são descritas nos seguintes artigos:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [CLI do Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Ligue diferentes modelos de implementação - portal do Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Ligue diferentes modelos de implementação - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a>Informações sobre como ligar VNets

As secções seguintes descrevem as diferentes formas de ligar redes virtuais.

### <a name="vnet-to-vnet"></a>VNet a VNet

Configurar uma ligação VNet a VNet é uma forma simples de ligar VNets. Quando ligar uma rede virtual a outra rede virtual com um tipo de ligação de VNet a VNet (VNet2VNet), é semelhante a criar uma ligação IPsec Site a Site para uma localização no local. Ambos os tipos de ligação utilizam um gateway de VPN para fornecer um túnel seguro com IPsec/IKE e funcionam da mesma forma quando estão a comunicar. No entanto, eles são diferentes na forma como o gateway de rede local está configurado. 

Quando cria uma ligação VNet a VNet, o espaço de endereços de gateway de rede local é automaticamente criado e preenchido. Se atualizar o espaço de endereços de uma VNet, a outra VNet encaminha-se automaticamente para o espaço de endereços atualizado. Normalmente, é mais rápido e fácil criar uma ligação VNet a VNet que uma ligação Site a Site.

### <a name="site-to-site-ipsec"></a>Site a Site (IPsec)

Se estiver trabalhando com uma configuração de rede mais complicada, poderá preferir ligar as suas VNets com um [ligação Site a Site](vpn-gateway-howto-site-to-site-resource-manager-portal.md) em vez disso. Ao seguir os passos de IPsec Site a Site, pode criar e configurar manualmente os gateways de rede local. O gateway de rede local para cada VNet trata a outra VNet como um site local. Estes passos permitem-lhe especificar espaços de endereços adicional para o gateway de rede local para encaminhar o tráfego. Se o espaço de endereço para uma VNet for alterado, tem de atualizar manualmente o gateway de rede local correspondente.

### <a name="vnet-peering"></a>VNet Peering

Também pode ligar as suas VNets com o VNet peering. O VNet peering não utiliza um gateway de VPN e tem restrições de diferentes. Além disso, o [preço do VNet peering](https://azure.microsoft.com/pricing/details/virtual-network) é calculado de forma diferente que os [preços dos Gateways VPN de VNet a VNet](https://azure.microsoft.com/pricing/details/vpn-gateway). Para obter mais informações, veja [VNet peering](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a>Por que criar uma ligação de VNet para vnet?

Poderá pretender ligar redes virtuais com uma ligação VNet a VNet pelos seguintes motivos:

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>Geopresença e georredundância entre várias regiões

  * Pode configurar a sua própria georreplicação ou sincronização com uma conetividade segura sem passar por pontos finais de acesso à internet.
  * Com o Gestor de tráfego do Azure e o Balanceador de carga do Azure, pode configurar-se a carga de trabalho de elevada disponibilidade com georredundância em várias regiões do Azure. Por exemplo, pode configurar grupos de disponibilidade Always On do SQL Server em várias regiões do Azure.

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>Aplicações multicamadas regionais com isolamento ou limites administrativos

  * Dentro da mesma região, pode configurar aplicações de várias camadas com várias redes virtuais estiverem ligadas em conjunto devido ao isolamento ou requisitos administrativos.

A comunicação VNet a VNet pode ser combinada com configurações multilocal. Estas configurações vai permitir estabelecer topologias de rede que combinam em vários locais conectividade com a conectividade de rede intervirtual, conforme mostrado no diagrama seguinte:

![Sobre conexões](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Acerca das ligações")

Este artigo mostra-lhe como ligar VNets com o tipo de ligação de VNet a VNet. Ao seguir estes passos como um exercício, pode utilizar os seguintes valores de definições de exemplo. No exemplo, as redes virtuais estão na mesma subscrição, mas em grupos de recursos diferentes. Se a suas VNets estiverem em diferentes subscrições, não pode criar a ligação no portal. Uso [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) ou [CLI](vpn-gateway-howto-vnet-vnet-cli.md) em vez disso. Para obter mais informações sobre ligações de VNet a VNet, veja [FAQ de VNet a VNet](#vnet-to-vnet-faq).

### <a name="example-settings"></a>Definições de exemplo

**Valores da TestVNet1:**

- **Definições de rede virtual**
    - **Nome**: introduza *TestVNet1*.
    - **Espaço de endereços**: introduza *10.11.0.0/16*.
    - **Subscrição**: selecione a subscrição que pretende utilizar.
    - **Grupo de recursos**: introduza *TestRG1*.
    - **Localização**: selecione **E.U.A. Leste**.
    - **Sub-rede**
        - **Nome**: introduza *front-end*.
        - **Intervalo de endereços**: introduza *10.11.0.0/24*.
    - **A sub-rede de gateway**:
        - **Nome**: *GatewaySubnet* é autofilled.
        - **Intervalo de endereços**: introduza *10.11.255.0/27*.

- **Definições do gateway de rede virtual** 
    - **Nome**: introduza *TestVNet1GW*.
    - **Tipo de gateway**: selecione **VPN**.
    - **Tipo de VPN**: selecione **baseado na rota**.
    - **SKU**: selecione o gateway do SKU que pretende utilizar.
    - **Nome do endereço IP público**: introduza *TestVNet1GWIP*
    - **ligação** 
       - **Nome**: introduza *TestVNet1toTestVNet4*.
       - **Chave partilhada**: introduza *abc123*. É possível criar a chave partilhada por conta própria. Ao criar a ligação entre as VNets, os valores têm de corresponder.

**Valores da TestVNet4:**

- **Definições de rede virtual**
   - **Nome**: introduza *TestVNet4*.
   - **Espaço de endereços**: introduza *10.41.0.0/16*.
   - **Subscrição**: selecione a subscrição que pretende utilizar.
   - **Grupo de recursos**: introduza *TestRG4*.
   - **Localização**: selecione **E.U.A. oeste**.
   - **Sub-rede** 
      - **Nome**: introduza *front-end*.
      - **Intervalo de endereços**: introduza *10.41.0.0/24*.
   - **GatewaySubnet** 
      - **Nome**: *GatewaySubnet* é autofilled.
      - **Intervalo de endereços**: introduza *10.41.255.0/27*.

- **Definições do gateway de rede virtual** 
    - **Nome**: introduza *TestVNet4GW*.
    - **Tipo de gateway**: selecione **VPN**.
    - **Tipo de VPN**: selecione **baseado na rota**.
    - **SKU**: selecione o gateway do SKU que pretende utilizar.
    - **Nome do endereço IP público**: introduza *TestVNet4GWIP*.
    - **ligação** 
       - **Nome**: introduza *TestVNet4toTestVNet1*.
       - **Chave partilhada**: introduza *abc123*. É possível criar a chave partilhada por conta própria. Ao criar a ligação entre as VNets, os valores têm de corresponder.

## <a name="create-and-configure-testvnet1"></a>Criar e configurar a TestVNet1
Se já tiver uma VNet, certifique-se de que as definições são compatíveis com a conceção do seu gateway de VPN. Tenha em especial atenção as sub-redes que se possam sobrepor a outras redes. A ligação não funcionará corretamente se tiver sub-redes sobrepostas.

### <a name="to-create-a-virtual-network"></a>Para criar uma rede virtual
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="add-additional-address-space-and-create-subnets"></a>Adicionar espaços de endereços adicionais e criar sub-redes
Pode adicionar espaços de endereços adicionais e criar sub-redes assim que a VNet tiver sido criada.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="create-a-virtual-network-gateway"></a>Criar um gateway de rede virtual
Neste passo, vai criar o gateway de rede virtual da VNet. Criar um gateway, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway selecionado. Se estiver a criar esta configuração como um exercício, veja a [definições de exemplo](#example-settings).

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="to-create-a-virtual-network-gateway"></a>Para criar um gateway de rede virtual
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-and-configure-testvnet4"></a>Criar e configurar a TestVNet4
Após configurar a TestVNet1, crie o TestVNet4 ao repetir os passos anteriores e substituindo os valores pelos valores da TestVNet4. Não precisa esperar até que o gateway de rede virtual da TestVNet1 tenha terminado de criar antes de configurar a TestVNet4. Se estiver usando seus próprios valores, certifique-se de que os espaços de endereços não se sobrepõe a qualquer uma das VNets para o qual pretende ligar.

## <a name="configure-the-testvnet1-gateway-connection"></a>Configurar a ligação de gateway TestVNet1
Quando os gateways de rede virtual para o TestVNet1 e o TestVNet4 estiverem concluídos, pode criar as ligações de gateway de rede virtual. Nesta secção, vai criar uma ligação da VNet1 à VNet4. Estes passos só funcionam em VNets na mesma subscrição. Se as suas VNets estiverem em diferentes subscrições, tem de utilizar [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) para fazer a conexão. No entanto, se as suas VNets estiverem em grupos de recursos diferentes na mesma subscrição, pode ligá-las com o portal.

1. No portal do Azure, selecione **todos os recursos**, introduza *gateway de rede virtual* na pesquisa caixa e, em seguida, navegue para o gateway de rede virtual para a sua VNet. Por exemplo, **TestVNet1GW**. Selecione para abrir o **gateway de rede Virtual** página.

   ![Página conexões](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/1to4connect2.png "Página conexões")
2. Sob **configurações**, selecione **ligações**e, em seguida, selecione **Add** para abrir o **adicionar ligação** página.

   ![Adicionar conexão](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add.png "Adicionar uma ligação")
3. Sobre o **adicionar ligação** página, preencha os valores para a sua ligação:

   - **Nome**: introduza um nome para a sua ligação. Por exemplo, *TestVNet1toTestVNet4*.

   - **Tipo de ligação**: selecione **VNet-para-VNet** na lista suspensa.

   - **Primeiro gateway de rede virtual**: este valor de campo é preenchido automaticamente porque está a criar esta ligação do gateway de rede virtual especificada.

   - **Segundo gateway de rede virtual**: Este campo é o gateway de rede virtual da VNet que pretende criar uma ligação a. Selecione **escolher outro gateway de rede virtual** para abrir o **gateway de rede virtual de escolha** página.

     - Veja os gateways de rede virtual que se encontram listados nesta página. Tenha em atenção que estão listados apenas os gateways da rede virtual na sua subscrição. Se pretender ligar a um gateway de rede virtual que não esteja na sua subscrição, utilize o [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

     - Selecione o gateway de rede virtual à qual pretende ligar.

     - **Chave partilhada (PSK)** : neste campo, introduza uma chave partilhada para a sua ligação. Pode gerar ou criar esta chave de forma independente. Uma ligação site a site, a chave que utiliza é o mesmo para o dispositivo no local e a ligação de gateway de rede virtual. O conceito é semelhante aqui, exceto que em vez de se ligar a um dispositivo VPN, está a ligar a outro gateway de rede virtual.
    
4. Selecione **OK** para guardar as alterações.

## <a name="configure-the-testvnet4-gateway-connection"></a>Configurar a ligação de gateway TestVNet4
Em seguida, crie uma ligação da TestVNet4 à TestVNet1. No portal, localize o gateway de rede virtual associado a TestVNet4. Siga os passos da secção anterior, substituindo os valores para criar uma ligação de TestVNet4 a TestVNet1. Certifique-se de que utiliza a mesma chave partilhada.

## <a name="verify-your-connections"></a>Verifique as suas ligações

Localize o gateway de rede virtual no portal do Azure. Sobre o **gateway de rede Virtual** página, selecione **ligações** para ver o **ligações** página para o gateway de rede virtual. Após a ligação é estabelecida, verá a **Status** valores alterar para **Succeeded** e **ligado**. Selecione uma ligação para abrir o **Essentials** página e ver mais informações.

![Foi](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Bem-sucedido")

Quando o fluxo de dados começar, verá valores para **dados no** e **dados de saída**.

![Essenciais](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Essentials")

## <a name="add-additional-connections"></a>Adicionar mais ligações

Se pretender adicionar mais ligações, navegue para o gateway de rede virtual a partir do qual pretende criar a ligação, em seguida, selecione **ligações**. Pode criar outra ligação VNet a VNet ou criar uma ligação Site a Site IPsec para uma localização no local. Certifique-se de que ajusta o **Tipo de ligação** de modo a corresponder ao tipo de ligação que quer criar. Antes de criar ligações adicionais, certifique-se de que o espaço de endereços da rede virtual não se sobrepõe a qualquer um dos espaços de endereços que pretende ligar a. Para obter os passos para criar uma ligação Site a Site, consulte [Criar uma ligação Site a Site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="vnet-to-vnet-faq"></a>FAQ da ligação VNet a VNet
Veja os detalhes das FAQ para obter informações adicionais sobre ligações VNet a VNet.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como limitar o tráfego de rede para os recursos numa rede virtual, consulte [segurança de rede](../virtual-network/security-overview.md).

Para obter informações sobre a forma como o Azure encaminha o tráfego entre os recursos do Azure, do local e da Internet veja [Encaminhamento de tráfego da rede virtual](../virtual-network/virtual-networks-udr-overview.md).
