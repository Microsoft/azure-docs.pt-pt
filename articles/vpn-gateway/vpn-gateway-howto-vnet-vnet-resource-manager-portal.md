---
title: 'Configure uma ligação VNet-to-VNet VPN Gateway: Portal Azure'
description: Crie uma ligação do Gateway de VPN entre VNets com o Gestor de Recursos e o Portal do Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: cherylmc
ms.openlocfilehash: 3d91203253c08acdaa159fc70f7a34fa7fca20c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78674164"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>Configure uma ligação de gateway VNet-to-VNet VPN utilizando o portal Azure

Este artigo ajuda-o a ligar redes virtuais (VNets) utilizando o tipo de ligação VNet-to-VNet. As redes virtuais podem ser em diferentes regiões e a partir de diferentes subscrições. Quando liga VNets a diferentes subscrições, as subscrições não precisam de ser associadas ao mesmo inquilino do Diretório Ativo. 

![Diagrama v2v](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

As etapas deste artigo aplicam-se ao modelo de implantação do Gestor de Recursos Azure e utilizam o portal Azure. Pode criar esta configuração com uma ferramenta ou modelo de implementação diferente utilizando opções descritas nos seguintes artigos:

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

As seguintes secções descrevem as diferentes formas de ligar redes virtuais.

### <a name="vnet-to-vnet"></a>VNet a VNet

Configurar uma ligação VNet-to-VNet é uma forma simples de ligar VNets. Quando liga uma rede virtual a outra rede virtual com um tipo de ligação VNet-to-VNet (VNet2VNet), é semelhante a criar uma ligação IPsec site-to-site a uma localização no local. Ambos os tipos de ligação utilizam um gateway VPN para fornecer um túnel seguro com IPsec/IKE e funcionam da mesma forma quando comunicam. No entanto, diferem na forma como o portal de rede local está configurado. 

Quando cria uma ligação VNet-to-VNet, o espaço de endereço de gateway da rede local é automaticamente criado e povoado. Se atualizar o espaço de endereço para um VNet, o outro VNet dirige-se automaticamente para o espaço de endereço atualizado. É tipicamente mais rápido e fácil criar uma ligação VNet-to-VNet do que uma ligação Site-to-Site.

### <a name="site-to-site-ipsec"></a>Site a Site (IPsec)

Se estiver a trabalhar com uma configuração de rede complicada, pode preferir ligar os seus VNets utilizando uma [ligação Site-to-Site.](vpn-gateway-howto-site-to-site-resource-manager-portal.md) Quando segue os passos iPsec do Site-para-Site, cria e configura manualmente os gateways de rede locais. O gateway de rede local para cada VNet trata a outra VNet como um site local. Estas etapas permitem especificar espaços de endereçoadicionais para a porta de entrada da rede local para o tráfego de rotas. Se o espaço de endereço para um VNet mudar, deve atualizar manualmente o gateway de rede local correspondente.

### <a name="vnet-peering"></a>VNet peering

Também pode ligar os seus VNets utilizando o peering VNet. O peering VNet não usa um gateway VPN e tem diferentes restrições. Além disso, o [preço do VNet peering](https://azure.microsoft.com/pricing/details/virtual-network) é calculado de forma diferente que os [preços dos Gateways VPN de VNet a VNet](https://azure.microsoft.com/pricing/details/vpn-gateway). Para obter mais informações, veja [VNet peering](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a>Porquê criar uma ligação VNet a VNet?

Pode querer ligar redes virtuais utilizando uma ligação VNet-to-VNet pelas seguintes razões:

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>Geopresença e georredundância entre várias regiões

  * Pode configurar a sua própria geo-replicação ou sincronização com conectividade segura sem passar por pontos finais virados para a Internet.
  * Com o Azure Traffic Manager e o Azure Load Balancer, pode configurar uma carga de trabalho altamente disponível com geo-redundância em várias regiões do Azure. Por exemplo, pode configurar o SQL Server Always On em grupos de disponibilidade em várias regiões do Azure.

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>Aplicações regionais a vários níveis com limites de isolamento ou administrativos

  * Dentro da mesma região, pode configurar aplicações multi-nível com múltiplas redes virtuais que estão ligadas em conjunto devido a requisitos de isolamento ou administrativos.

A comunicação VNet a VNet pode ser combinada com configurações multilocal. Estas configurações permitem estabelecer topologs de rede que combinam conectividade transversal com conectividade inter-virtual da rede, como mostra o seguinte diagrama:

![Acerca das ligações](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Acerca das ligações")

Este artigo mostra-lhe como ligar VNets utilizando o tipo de ligação VNet-to-VNet. Quando segue estes passos como exercício, pode utilizar os seguintes valores de definições de exemplo. No exemplo, as redes virtuais estão na mesma subscrição, mas em grupos de recursos diferentes. Se a suas VNets estiverem em diferentes subscrições, não pode criar a ligação no portal. Utilize [powerShell](vpn-gateway-vnet-vnet-rm-ps.md) ou [CLI](vpn-gateway-howto-vnet-vnet-cli.md) em vez disso. Para obter mais informações sobre ligações VNet-to-VNet, consulte [VNet-to-VNet FAQ](#vnet-to-vnet-faq).

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
       - **Chave partilhada:** Você pode criar a chave partilhada por si mesmo. Quando cria a ligação entre os VNets, os valores devem coincidir. Para este exercício, utilize o abc123.

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
       - **Chave partilhada:** Você pode criar a chave partilhada por si mesmo. Quando cria a ligação entre os VNets, os valores devem coincidir. Para este exercício, utilize o abc123.

## <a name="create-and-configure-vnet1"></a>Criar e configurar VNet1
Se já tiver uma VNet, certifique-se de que as definições são compatíveis com a conceção do seu gateway de VPN. Tenha em especial atenção as sub-redes que se possam sobrepor a outras redes. A sua ligação não funcionará corretamente se tiver subredes sobrepostas.

### <a name="to-create-a-virtual-network"></a>Para criar uma rede virtual
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-the-vnet1-gateway"></a>Criar o gateway VNet1
Neste passo, vai criar o gateway de rede virtual da VNet. Criar um gateway, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway selecionado. Se estiver a criar esta configuração como exercício, consulte as [definições](#example-settings)do Exemplo .

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="to-create-a-virtual-network-gateway"></a>Para criar um gateway de rede virtual
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-and-configure-vnet4"></a>Criar e configurar VNet4
Depois de configurar o VNet1, crie o VNet4 e o gateway VNet4 repetindo os passos anteriores e substituindo os valores por valores VNet4. Não precisa de esperar até que a porta de entrada de rede virtual para VNet1 termine de criar antes de configurar o VNet4. Se estiver a utilizar os seus próprios valores, certifique-se de que os espaços de endereço não se sobrepõem a nenhum dos VNets a que pretende ligar.

## <a name="configure-the-vnet1-gateway-connection"></a>Configure a ligação de gateway VNet1
Quando os gateways de rede virtuais tanto para VNet1 como VNet4 estiverem concluídos, pode criar as suas ligações de gateway de rede virtual. Nesta secção, vai criar uma ligação da VNet1 à VNet4. Estes passos só funcionam em VNets na mesma subscrição. Se os seus VNets estiverem em subscrições diferentes, tem de utilizar o [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) para efazer a ligação. No entanto, se os seus VNets estiverem em diferentes grupos de recursos na mesma subscrição, pode conectá-los utilizando o portal.

1. No portal Azure, selecione **Todos os recursos,** *introduza* o portal da rede virtual na caixa de pesquisa e, em seguida, navegue para o portal de rede virtual para o seu VNet. Por exemplo, **VNet1GW**. Selecione o portal para abrir a página de gateway da **rede Virtual.** Em **Definições,** selecione **Ligações**.

   ![Página de ligações](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections.png "Página de ligações")
2. Selecione **+Adicionar** para abrir a página de **ligação Adicionar.**

   ![Adicionar ligação](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet1-vnet4-connection.png "Adicionar uma ligação")
3. Na página de **ligação Adicionar,** preencha os valores para a sua ligação:

   - **Nome**: Insira um nome para a sua ligação. Por exemplo, *VNet1toVNet4*.

   - Tipo de **ligação:** Selecione **VNet-to-VNet** a partir da queda.

   - **Primeira porta de entrada**de rede virtual : Este valor de campo é automaticamente preenchido porque está a criar esta ligação a partir do gateway de rede virtual especificado.

   - **Segunda porta de entrada**de rede virtual : Este campo é o portal de rede virtual do VNet a que pretende criar uma ligação. Selecione **Escolha outro portal de rede virtual** para abrir a página de gateway da rede virtual **Choose.**

     - Veja os gateways de rede virtual que se encontram listados nesta página. Tenha em atenção que estão listados apenas os gateways da rede virtual na sua subscrição. Se quiser ligar-se a um portal de rede virtual que não esteja na sua subscrição, utilize o [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

     - Selecione o portal de rede virtual ao qual pretende ligar.

     - **Tecla partilhada (PSK)**: Neste campo, introduza uma chave partilhada para a sua ligação. Pode gerar ou criar esta chave de forma independente. Numa ligação site-a-site, a chave que utiliza é a mesma para o seu dispositivo no local e para a sua ligação de gateway de rede virtual. O conceito é semelhante aqui, exceto que em vez de se ligar a um dispositivo VPN, está a ligar-se a outro portal de rede virtual.
    
4. Selecione **OK** para guardar as alterações.

## <a name="configure-the-vnet4-gateway-connection"></a>Configure a ligação de gateway VNet4
Em seguida, crie uma ligação de VNet4 a VNet1. No portal, localize o portal de rede virtual associado ao VNet4. Siga os passos da secção anterior, substituindo os valores para criar uma ligação de VNet4 a VNet1. Certifique-se de que utiliza a mesma chave partilhada.

## <a name="verify-your-connections"></a>Verifique as suas ligações

1. Localize o portal da rede virtual no portal Azure. 
2. Na página de gateway da **rede Virtual,** selecione **Ligações** para visualizar a página **De Ligações** para o gateway da rede virtual. Após a ligação ser estabelecida, verá os valores do **Estado** alterados para **Connected**.

   ![Verificar ligações](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/view-connections.png "Verificar ligações")
3. Na coluna **Nome,** selecione uma das ligações para ver mais informações. Quando os dados começarem a fluir, verá os valores dos **Dados dentro** e dados **para fora**.

   ![Estado](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/status.png "Estado")

## <a name="add-additional-connections"></a>Adicionar ligações adicionais

Se pretender adicionar ligações adicionais, navegue para o portal de rede virtual a partir do qual pretende criar a ligação e, em seguida, selecione **Ligações**. Pode criar outra ligação VNet a VNet ou criar uma ligação Site a Site IPsec para uma localização no local. Certifique-se de que ajusta o **Tipo de ligação** de modo a corresponder ao tipo de ligação que quer criar. Antes de criar ligações adicionais, verifique se o espaço de endereço para a sua rede virtual não se sobrepõe a nenhum dos espaços de endereço a que pretende ligar. Para obter os passos para criar uma ligação Site a Site, consulte [Criar uma ligação Site a Site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="vnet-to-vnet-faq"></a>FAQ da ligação VNet a VNet
Veja os detalhes das FAQ para obter informações adicionais sobre ligações VNet a VNet.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como pode limitar o tráfego de rede a recursos numa rede virtual, consulte a [Network Security](../virtual-network/security-overview.md).

Para obter informações sobre a forma como o Azure encaminha o tráfego entre os recursos do Azure, do local e da Internet veja [Encaminhamento de tráfego da rede virtual](../virtual-network/virtual-networks-udr-overview.md).
