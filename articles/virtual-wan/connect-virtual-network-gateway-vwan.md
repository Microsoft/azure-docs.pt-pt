---
title: Ligue uma porta de entrada de rede virtual a um Wan Virtual Azure
description: Este artigo ajuda-o a ligar uma porta de entrada de rede virtual Azure a um gateway Azure Virtual WAN VPN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: 688183bc07aa14d5e5df182d7de0897cec93f0b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066242"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>Ligue um Gateway VPN (gateway de rede virtual) ao Wan Virtual

Este artigo ajuda-o a configurar a conectividade de um Azure VPN Gateway (gateway de rede virtual) para um Azure Virtual WAN (gateway VPN). Criar uma ligação de um VPN Gateway (gateway de rede virtual) a um Wan Virtual (gateway VPN) é semelhante a estabelecer conectividade com um WAN virtual a partir de sites VPN de sucursal.

De forma a minimizar uma possível confusão entre duas funcionalidades, prefácios o portal com o nome da funcionalidade a que nos referimos. Por exemplo, gateway de rede virtual VPN Gateway e gateway VVP N.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar, crie os seguintes recursos:

WAN Virtual do Azure

* [Crie um WAN virtual.](virtual-wan-site-to-site-portal.md#openvwan)
* [Criar um hub.](virtual-wan-site-to-site-portal.md#hub) O centro virtual contém o gateway Virtual WAN VPN.

Rede Virtual do Azure

* Criar uma rede virtual sem quaisquer gateways de rede virtuais. Verifique se nenhuma das subredes das suas redes no local se sobrepõe às redes virtuais a que pretende ligar. Para criar uma rede virtual no portal Azure, consulte o [Quickstart](../virtual-network/quick-create-portal.md).

## <a name="1-create-an-azure-virtual-network-gateway"></a><a name="vnetgw"></a>1. Criar um portal de rede virtual Azure

Crie um portal de rede virtual VPN Gateway para a sua rede virtual em modo ativo para a sua rede virtual. Quando criar o portal, pode utilizar endereços IP públicos existentes para as duas instâncias do portal, ou pode criar novos IP públicos. Você usa estes IPs públicos ao configurar os sites Virtual WAN. Para obter mais informações sobre o modo active-active, consulte [configurar ligações ativas](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway).

### <a name="active-active-mode-setting"></a><a name="active-active"></a>Definição de modo ativo

![ativa-ativa](./media/connect-virtual-network-gateway-vwan/active.png "ativa-ativa")

### <a name="bgp-setting"></a><a name="BGP"></a>Definição de BGP

O BGP ASN não pode ser 65515. 66515 será utilizado pela Azure Virtual WAN.

![BGP](./media/connect-virtual-network-gateway-vwan/bgp.png "bgp")

### <a name="public-ip-addresses"></a><a name="pip"></a>Endereços IP públicos

Quando o portal for criado, navegue para a página **Propriedades.** As propriedades e configurações de configuração serão semelhantes ao seguinte exemplo. Repare nos dois endereços IP públicos que são usados para o portal.

![propriedades](./media/connect-virtual-network-gateway-vwan/publicip.png "propriedades")

## <a name="2-create-virtual-wan-vpn-sites"></a><a name="vwansite"></a>2. Criar sites virtuais de VPN WAN

Para criar sites VVpN Virtual WAN, navegue para o seu WAN virtual e, sob **conectividade, selecione** **sites VPN**. Nesta secção, irá criar dois sites VVpN Virtual WAN que correspondem aos gateways de rede virtuais que criou na secção anterior.

1. Selecione **+Criar site**.
2. Na página **de sites Create VPN,** escreva os seguintes valores:

   * **Região** - (A mesma região do portal de rede virtual Azure VPN Gateway)
   * **Fornecedor de dispositivos** - Introduza o fornecedor do dispositivo (qualquer nome)
   * **Espaço de endereço sinuoso** - (Insira um valor ou deixe em branco quando o BGP estiver ativado)
   * **Border Gateway Protocol** - (set to **Enable** if the Azure VPN Gateway virtual network gateway gateway activeed)
   * **Ligue-se a Hubs** (Selecione hub que criou nos pré-requisitos a partir da queda)
3. Em **Links,** introduza os seguintes valores:

   * **Nome do fornecedor** - Insira um nome de link e um nome de fornecedor (qualquer nome)
   * **Velocidade** - Velocidade (qualquer número)
   * **Endereço IP** - Introduza o endereço IP (o mesmo que o primeiro endereço IP público mostrado nas propriedades de gateway da rede virtual (VPN Gateway)
   * **Endereço BGP** e endereço **ASN** - BGP e ASN. Estes devem ser os mesmos que um dos endereços IP peer BGP, e ASN a partir do gateway de rede virtual VPN Gateway que configuraste no [Passo 1](#vnetgw).
4. Reveja e selecione **Confirmar** para criar o site.
5. Repita os passos anteriores para criar o segundo site para combinar com a segunda instância do gateway de rede virtual VPN Gateway. Manterá as mesmas definições, exceto utilizando o segundo endereço IP público e o segundo endereço IP do BGP da configuração VPN Gateway.
6. Tem agora dois sites aprovisionados com sucesso e pode passar para a secção seguinte para descarregar ficheiros de configuração.

## <a name="3-download-the-vpn-configuration-files"></a><a name="downloadconfig"></a>3. Descarregue os ficheiros de configuração VPN

Nesta secção, descarrega o ficheiro de configuração VPN para cada um dos sites que criou na secção anterior.

1. Na parte superior da página de sites Virtual WAN **VPN,** selecione o **Site**e, em seguida, selecione a **configuração VPN site-para-site**de download . O Azure cria um ficheiro de configuração com as definições.

   ![ficheiro de configuração de descarregamento](./media/connect-virtual-network-gateway-vwan/download.png "transferir")
2. Faça o download e abra o ficheiro de configuração.
3. Repita estes passos para o segundo local. Uma vez abertos os dois ficheiros de configuração, pode passar para a secção seguinte.

## <a name="4-create-the-local-network-gateways"></a><a name="createlocalgateways"></a>4. Criar os gateways da rede local

Nesta secção, cria dois gateways de rede local Azure VPN Gateway. Os ficheiros de configuração do passo anterior contêm as definições de configuração do gateway. Utilize estas definições para criar e configurar os gateways de rede locais Azure VPN Gateway.

1. Crie o portal de rede local utilizando estas definições. Para obter informações sobre como criar um gateway de rede local VPN Gateway, consulte o artigo VPN Gateway [Criar um gateway de rede local](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway).

   * **Endereço IP** - Utilize o endereço IP da Instância mostrado para *a configuração* do gateway a partir do ficheiro de configuração.
   * **BGP** - Se a ligação tiver terminado de BGP, **selecione configurações de BGP de Configuração** e introduza a ASN '65515'. Introduza o endereço IP do par BGP. Utilize 'Exemplo0 BgpPeeringAddresss' para *configurar* o gateway do ficheiro de configuração.
   * **Subscrição, Grupo de Recursos e Localização são** iguais ao centro virtual WAN.
2. Reveja e crie o portal da rede local. O gateway da rede local deve ser semelhante a este exemplo.

   ![ficheiro de configuração de descarregamento](./media/connect-virtual-network-gateway-vwan/lng1.png "instância0")
3. Repita estes passos para criar outro portal de rede local, mas desta vez, utilize os valores 'Instance1' em vez dos valores 'Instance0' do ficheiro de configuração.

   ![ficheiro de configuração de descarregamento](./media/connect-virtual-network-gateway-vwan/lng2.png "instância1")

## <a name="5-create-connections"></a><a name="createlocalgateways"></a>5. Criar ligações

Nesta secção, cria-se uma ligação entre os gateways de rede locais VPN Gateway e o gateway da rede virtual. Para obter passos sobre como criar uma ligação VPN Gateway, consulte [Configurar uma ligação](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#CreateConnection).

1. No portal, navegue para o seu portal de rede virtual e clique em **Ligações**. No topo da página Ligações, clique em **+Adicionar** para abrir a página **Adicionar ligação**.
2. Na página **de ligação Adicionar,** configure os seguintes valores para a sua ligação:

   * **Nome:** Atribua um nome à sua ligação.
   * **Tipo de ligação:** Selecione **Site-a-site (IPSec)**
   * **Gateway de rede virtual:** O valor é fixo porque está a ligar a partir deste gateway.
   * **Gateway de rede local:** Esta ligação ligará a porta de entrada da rede virtual ao portal da rede local. Escolha um dos gateways de rede local que criou anteriormente.
   * **Chave partilhada:** Introduza uma chave partilhada.
   * **Protocolo IKE:** Escolha o protocolo IKE.
   * **BGP:** Escolha **Ativar BGP** se a ligação for superior a BGP.
3. Clique em **OK** para criar a ligação.
4. A ligação é apresentada na página **Ligações** do gateway de rede virtual.

   ![Conexão](./media/connect-virtual-network-gateway-vwan/connect.png "ligação")
5. Repita os passos anteriores para criar uma segunda ligação. Para a segunda ligação, selecione o outro portal de rede local que criou.

## <a name="6-test-connections"></a><a name="test"></a>6. Ligações de ensaio

Pode testar a conectividade criando duas máquinas virtuais, uma na lateral do gateway de rede virtual VPN Gateway, e outra numa rede virtual para o WAN Virtual, e depois pingar as duas máquinas virtuais.

1. Crie uma máquina virtual na rede virtual (Test1-VNet) para o Azure VPN Gateway (Test1-VNG). Não crie a máquina virtual na GatewaySubnet.
2. Crie outra rede virtual para se ligar ao WAN virtual. Crie uma máquina virtual numa subnet desta rede virtual. Esta rede virtual não pode conter quaisquer gateways de rede virtuais. Pode criar rapidamente uma rede virtual utilizando os passos PowerShell no artigo [de ligação site-to-site.](virtual-wan-site-to-site-portal.md#vnet) Certifique-se de que altera os valores antes de executar os cmdlets.
3. Ligue o VNet ao centro virtual WAN. Na página para o seu WAN virtual, selecione **ligações de rede Virtual**e, em seguida, **+Adicionar ligação**. Na página **Add connection** (Adicionar ligação), preencha os seguintes campos:

    * **Connection name** (Nome da ligação) - dê um nome à ligação.
    * **Hubs** - selecione o hub que pretende associar a esta ligação.
    * **Subscription** (Subscrição) - verifique a subscrição.
    * **Virtual network** (Rede virtual) - selecione a rede virtual que pretende ligar a este hub. A rede virtual não pode ter um gateway de rede virtual já existente.
4. Clique em **OK** para criar a ligação de rede virtual.
5. A conectividade está agora definida entre os VMs. Você deve ser capaz de pingar um VM do outro, a menos que existam firewalls ou outras políticas bloqueando a comunicação.

## <a name="next-steps"></a>Passos seguintes

Para passos para configurar uma política personalizada de IPsec, consulte [Configurar uma política personalizada de IPsec para Virtual WAN](virtual-wan-custom-ipsec-portal.md).
Para mais informações sobre o Wan Virtual, consulte [o Azure Virtual WAN](virtual-wan-about.md) e o [Azure Virtual WAN FAQ](virtual-wan-faq.md).