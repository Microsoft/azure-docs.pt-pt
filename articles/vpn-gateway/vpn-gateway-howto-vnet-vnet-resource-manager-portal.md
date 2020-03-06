---
title: 'Configure uma ligação VNet-to-VNet VPN Gateway: Portal Azure'
description: Crie uma ligação do Gateway de VPN entre VNets com o Gestor de Recursos e o Portal do Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: cherylmc
ms.openlocfilehash: dbdc13b8c861c620bfdbaaf53c0901a51bb9ce08
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399208"
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

Se estiver a trabalhar com uma configuração de rede complicada, pode preferir ligar os seus VNets utilizando uma [ligação Site-to-Site.](vpn-gateway-howto-site-to-site-resource-manager-portal.md) Ao seguir os passos de IPsec Site a Site, pode criar e configurar manualmente os gateways de rede local. O gateway de rede local para cada VNet trata a outra VNet como um site local. Estes passos permitem-lhe especificar espaços de endereços adicional para o gateway de rede local para encaminhar o tráfego. Se o espaço de endereço para uma VNet for alterado, tem de atualizar manualmente o gateway de rede local correspondente.

### <a name="vnet-peering"></a>VNet peering

Também pode ligar as suas VNets com o VNet peering. O VNet peering não utiliza um gateway de VPN e tem restrições de diferentes. Além disso, o [preço do VNet peering](https://azure.microsoft.com/pricing/details/virtual-network) é calculado de forma diferente que os [preços dos Gateways VPN de VNet a VNet](https://azure.microsoft.com/pricing/details/vpn-gateway). Para obter mais informações, veja [VNet peering](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a>Por que criar uma ligação de VNet para vnet?

Poderá pretender ligar redes virtuais com uma ligação VNet a VNet pelos seguintes motivos:

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>Geopresença e georredundância entre várias regiões

  * Pode configurar a sua própria georreplicação ou sincronização com uma conetividade segura sem passar por pontos finais de acesso à internet.
  * Com o Gestor de tráfego do Azure e o Balanceador de carga do Azure, pode configurar-se a carga de trabalho de elevada disponibilidade com georredundância em várias regiões do Azure. Por exemplo, pode configurar grupos de disponibilidade Always On do SQL Server em várias regiões do Azure.

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>Aplicações multicamadas regionais com isolamento ou limites administrativos

  * Dentro da mesma região, pode configurar aplicações de várias camadas com várias redes virtuais estiverem ligadas em conjunto devido ao isolamento ou requisitos administrativos.

A comunicação VNet a VNet pode ser combinada com configurações multilocal. Estas configurações vai permitir estabelecer topologias de rede que combinam em vários locais conectividade com a conectividade de rede intervirtual, conforme mostrado no diagrama seguinte:

![Sobre ligações](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Acerca das ligações")

Este artigo mostra-lhe como ligar VNets com o tipo de ligação de VNet a VNet. Ao seguir estes passos como um exercício, pode utilizar os seguintes valores de definições de exemplo. No exemplo, as redes virtuais estão na mesma subscrição, mas em grupos de recursos diferentes. Se a suas VNets estiverem em diferentes subscrições, não pode criar a ligação no portal. Utilize [powerShell](vpn-gateway-vnet-vnet-rm-ps.md) ou [CLI](vpn-gateway-howto-vnet-vnet-cli.md) em vez disso. Para obter mais informações sobre ligações VNet-to-VNet, consulte [VNet-to-VNet FAQ](#vnet-to-vnet-faq).

### <a name="example-settings"></a>Definições de exemplo

**Valores para VNet1:**

- **Definições de rede virtual**
    - **Nome**: VNet1
    - **Espaço**de endereço : 10.1.0.0/16
    - **Subscrição**: Selecione a subscrição que pretende utilizar.
    - **Grupo de recursos**: TestRG1
    - **Localização**: Leste dos EUA
    - **Sub-rede**
        - **Nome**: FrontEnd
        - Intervalo de **endereços**: 10.1.0.0/24
    - **Sub-rede gateway:**
        - **Nome**: *GatewaySubnet* é preenchido automaticamente
        - Intervalo de **endereços**: 10.1.255.0/27

- **Definições de gateway de rede virtual**
    - **Nome**: VNet1GW
    - **Tipo de gateway**: selecione **VPN**.
    - **Tipo VPN**: Selecione **baseado na rota**.
    - **SKU:** Selecione o portal SKU que pretende utilizar.
    - **Nome de endereço IP público**: VNet1GWpip
    - **Conexão**
       - **Nome**: VNet1toVNet4
       - **Chave partilhada:** Você pode criar a chave partilhada por si mesmo. Ao criar a ligação entre as VNets, os valores têm de corresponder. Para este exercício, utilize o abc123.

**Valores para VNet4:**

- **Definições de rede virtual**
   - **Nome**: VNet4
   - **Espaço**de endereço : 10.41.0.0/16
   - **Subscrição**: Selecione a subscrição que pretende utilizar.
   - **Grupo de recursos**: TestRG4
   - **Localização**: Oeste DOS EUA
   - **Sub-rede** 
      - **Nome**: FrontEnd
      - Intervalo de **endereços**: 10.41.0.0/24
   - **GatewaySubnet** 
      - **Nome**: *GatewaySubnet* é preenchido automaticamente
      - **Intervalo**de endereços: 10.41.255.0/27

- **Definições de gateway de rede virtual** 
    - **Nome**: VNet4GW
    - **Tipo de gateway**: selecione **VPN**.
    - **Tipo VPN**: Selecione **baseado na rota**.
    - **SKU:** Selecione o portal SKU que pretende utilizar.
    - **Nome de endereço IP público**: VNet4GWpip
    - **Conexão** 
       - **Nome**: VNet4toVNet1
       - **Chave partilhada:** Você pode criar a chave partilhada por si mesmo. Ao criar a ligação entre as VNets, os valores têm de corresponder. Para este exercício, utilize o abc123.

## <a name="create-and-configure-vnet1"></a>Criar e configurar VNet1
Se já tiver uma VNet, certifique-se de que as definições são compatíveis com a conceção do seu gateway de VPN. Tenha em especial atenção as sub-redes que se possam sobrepor a outras redes. A ligação não funcionará corretamente se tiver sub-redes sobrepostas.

### <a name="to-create-a-virtual-network"></a>Para criar uma rede virtual
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-virtual-network-gateway"></a>Criar um gateway de rede virtual
Neste passo, vai criar o gateway de rede virtual da VNet. Criar um gateway, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway selecionado. Se estiver a criar esta configuração como exercício, consulte as [definições](#example-settings)do Exemplo .

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="to-create-a-virtual-network-gateway"></a>Para criar um gateway de rede virtual
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-and-configure-vnet4"></a>Criar e configurar VNet4
Depois de configurar o VNet1, crie o VNet4 repetindo os passos anteriores e substituindo os valores por valores VNet4. Não precisa de esperar até que a porta de entrada de rede virtual para VNet1 termine de criar antes de configurar o VNet4. Se estiver usando seus próprios valores, certifique-se de que os espaços de endereços não se sobrepõe a qualquer uma das VNets para o qual pretende ligar.

## <a name="configure-the-vnet1-gateway-connection"></a>Configure a ligação de gateway VNet1
Quando os gateways de rede virtuais tanto para VNet1 como VNet4 estiverem concluídos, pode criar as suas ligações de gateway de rede virtual. Nesta secção, vai criar uma ligação da VNet1 à VNet4. Estes passos só funcionam em VNets na mesma subscrição. Se os seus VNets estiverem em subscrições diferentes, tem de utilizar o [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) para efazer a ligação. No entanto, se as suas VNets estiverem em grupos de recursos diferentes na mesma subscrição, pode ligá-las com o portal.

1. No portal Azure, selecione **Todos os recursos,** *introduza* o portal da rede virtual na caixa de pesquisa e, em seguida, navegue para o portal de rede virtual para o seu VNet. Por exemplo, **VNet1GW**. Selecione o portal para abrir a página de gateway da **rede Virtual.** Em **Definições,** selecione **Ligações**.

   ![Página de ligações](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections.png "Página de ligações")
2. Selecione **+Adicionar** para abrir a página de **ligação Adicionar.**

   ![Adicionar ligação](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet1-to-vnet4.png "adicionar uma ligação")
3. Na página de **ligação Adicionar,** preencha os valores para a sua ligação:

   - **Nome**: Insira um nome para a sua ligação. Por exemplo, *VNet1toVNet4*.

   - Tipo de **ligação:** Selecione **VNet-to-VNet** a partir da queda.

   - **Primeira porta de entrada**de rede virtual : Este valor de campo é automaticamente preenchido porque está a criar esta ligação a partir do gateway de rede virtual especificado.

   - **Segunda porta de entrada**de rede virtual : Este campo é o portal de rede virtual do VNet a que pretende criar uma ligação. Selecione **Escolha outro portal de rede virtual** para abrir a página de gateway da rede virtual **Choose.**

     - Veja os gateways de rede virtual que se encontram listados nesta página. Tenha em atenção que estão listados apenas os gateways da rede virtual na sua subscrição. Se quiser ligar-se a um portal de rede virtual que não esteja na sua subscrição, utilize o [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

     - Selecione o gateway de rede virtual à qual pretende ligar.

     - **Tecla partilhada (PSK)** : Neste campo, introduza uma chave partilhada para a sua ligação. Pode gerar ou criar esta chave de forma independente. Uma ligação site a site, a chave que utiliza é o mesmo para o dispositivo no local e a ligação de gateway de rede virtual. O conceito é semelhante aqui, exceto que em vez de se ligar a um dispositivo VPN, está a ligar a outro gateway de rede virtual.
    
4. Selecione **OK** para guardar as suas alterações.

## <a name="configure-the-vnet4-gateway-connection"></a>Configure a ligação de gateway VNet4
Em seguida, crie uma ligação de VNet4 a VNet1. No portal, localize o portal de rede virtual associado ao VNet4. Siga os passos da secção anterior, substituindo os valores para criar uma ligação de VNet4 a VNet1. Certifique-se de que utiliza a mesma chave partilhada.

## <a name="verify-your-connections"></a>Verifique as suas ligações

1. Localize o gateway de rede virtual no portal do Azure. 
2. Na página de gateway da **rede Virtual,** selecione **Ligações** para visualizar a página **De Ligações** para o gateway da rede virtual. Após a ligação ser estabelecida, verá os valores do **Estado** alterados para **Connected**.

   ![Verificar ligações](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/view-connections.png "Verificar ligações")
3. Na coluna **Nome,** selecione uma das ligações para ver mais informações. Quando os dados começarem a fluir, verá os valores dos **Dados dentro** e dados **para fora**.

   ![Estado](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/status.png "Estado")

## <a name="add-additional-connections"></a>Adicionar mais ligações

Se pretender adicionar ligações adicionais, navegue para o portal de rede virtual a partir do qual pretende criar a ligação e, em seguida, selecione **Ligações**. Pode criar outra ligação VNet a VNet ou criar uma ligação Site a Site IPsec para uma localização no local. Certifique-se de que ajusta o **Tipo de ligação** de modo a corresponder ao tipo de ligação que quer criar. Antes de criar ligações adicionais, certifique-se de que o espaço de endereços da rede virtual não se sobrepõe a qualquer um dos espaços de endereços que pretende ligar a. Para obter os passos para criar uma ligação Site a Site, consulte [Criar uma ligação Site a Site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="vnet-to-vnet-faq"></a>FAQ da ligação VNet a VNet
Veja os detalhes das FAQ para obter informações adicionais sobre ligações VNet a VNet.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como pode limitar o tráfego de rede a recursos numa rede virtual, consulte a [Network Security](../virtual-network/security-overview.md).

Para obter informações sobre a forma como o Azure encaminha o tráfego entre os recursos do Azure, do local e da Internet veja [Encaminhamento de tráfego da rede virtual](../virtual-network/virtual-networks-udr-overview.md).
