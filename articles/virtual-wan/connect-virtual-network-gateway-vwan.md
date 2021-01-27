---
title: Ligue uma porta de rede virtual a um Azure Virtual WAN
description: Este artigo ajuda-o a ligar uma porta de rede virtual Azure a uma porta de entrada Azure Virtual WAN VPN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 469d7ba9e86751312ebf6a6c82b35f065ee6cb50
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880377"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>Ligue um Gateway VPN (gateway de rede virtual) ao VIRTUAL WAN

Este artigo ajuda-o a configurar a conectividade de um Gateway Azure VPN (gateway de rede virtual) para um Azure Virtual WAN (gateway VPN). Criar uma ligação de um Gateway VPN (gateway de rede virtual) para um Gateway Virtual WAN (gateway VPN) é semelhante à configuração da conectividade a um WAN virtual a partir de sites VPN de ramo.

De forma a minimizar uma possível confusão entre duas funcionalidades, vamos prefaciar o gateway com o nome da funcionalidade a que nos referimos. Por exemplo, gateway de rede virtual VPN Gateway e gateway Virtual WAN VPN.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar, crie os seguintes recursos:

WAN Virtual do Azure

* [Crie um WAN virtual.](virtual-wan-site-to-site-portal.md#openvwan)
* [Criar um hub](virtual-wan-site-to-site-portal.md#hub). O hub virtual contém o gateway Virtual WAN VPN.

Rede Virtual do Azure

* Crie uma rede virtual sem quaisquer gateways de rede virtuais. Verifique se nenhuma das sub-redes das suas redes no local se sobrepõe às redes virtuais a que pretende ligar. Para criar uma rede virtual no portal Azure, consulte o [Quickstart](../virtual-network/quick-create-portal.md).

## <a name="1-create-a-vpn-gateway-virtual-network-gateway"></a><a name="vnetgw"></a>1. Criar uma porta de rede virtual VPN Gateway

Crie um gateway de rede virtual **VPN Gateway** em modo ativo para a sua rede virtual. Quando criar o gateway, pode utilizar os endereços IP públicos existentes para as duas instâncias do gateway, ou pode criar novos IPs públicos. Utilizará estes IPs públicos ao configurar os sites DE WAN Virtuais. Para obter mais informações sobre gateways VPN ativos e etapas de configuração, consulte as [portas VPN ativas ativas](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway).

### <a name="active-active-mode-setting"></a><a name="active-active"></a>Definição de modo ativo

Na página **de Configuração** de gateway de rede virtual, ative o modo ative.

![ativo ativo](./media/connect-virtual-network-gateway-vwan/active.png "ativa-ativa")

### <a name="bgp-setting"></a><a name="BGP"></a>Definição de BGP

Na página **configuração** do gateway de rede virtual, pode configurar o **BGP ASN**. Mude o BGP ASN. O BGP ASN não pode ser 65515. O 65515 será utilizado pela Azure Virtual WAN.

![O Screenshot mostra uma página de configuração de gateway de rede virtual com o Configure BGP ASN selecionado.](./media/connect-virtual-network-gateway-vwan/bgp.png "bgp")

### <a name="public-ip-addresses"></a><a name="pip"></a>Endereços IP públicos

Quando o gateway for criado, navegue para a página **Propriedades.** As configurações de propriedades e configurações serão semelhantes ao exemplo seguinte. Observe os dois endereços IP públicos que são utilizados para o gateway.

![propriedades](./media/connect-virtual-network-gateway-vwan/publicip.png "propriedades")

## <a name="2-create-virtual-wan-vpn-sites"></a><a name="vwansite"></a>2. Criar sites virtuais wan VPN

Para criar sites VPN de WAN virtual, navegue até ao seu WAN virtual e, em **conectividade,** selecione **sites VPN**. Nesta secção, irá criar dois sites De WAN VPN Virtuais que correspondem aos gateways de rede virtuais que criou na secção anterior.

1. Selecione **+Criar site.**
2. Na página de **sites Create VPN,** digite os seguintes valores:

   * **Região** - A mesma região que o gateway virtual Azure VPN Gateway.
   * **Fornecedor de dispositivos** - Introduza o fornecedor do dispositivo (qualquer nome).
   * **Espaço de endereço privado** - Introduza um valor ou deixe em branco quando o BGP estiver ativado.
   * **Protocolo border gateway** - Definido para **ativar** se o gateway de rede virtual Azure VPN Gateway tiver BGP habilitado.
   * **Conecte-se a Hubs** - Selecione o hub que criou nos pré-requisitos a partir do dropdown. Se não vir um hub, verifique se criou uma porta de entrada VPN site-to-site para o seu hub.
3. Em **Links**, insira os seguintes valores:

   * **Nome do fornecedor** - Introduza um nome link e um nome de fornecedor (qualquer nome).
   * **Velocidade** - Velocidade (qualquer número).
   * **Endereço IP** - Insira o endereço IP (o mesmo que o primeiro endereço IP público mostrado sob as propriedades de gateway de rede virtual (VPN Gateway).
   * **Endereço BGP** e **ASN** - Endereço BGP e ASN. Estes devem ser os mesmos que um dos endereços IP peer BGP, e ASN a partir do gateway de rede virtual VPN Gateway que você configurado no [passo 1](#vnetgw).
4. Reveja e **selecione Confirme** para criar o site.
5. Repita os passos anteriores para criar o segundo site para combinar com a segunda instância do gateway de rede virtual VPN Gateway. Você manterá as mesmas definições, exceto usando o segundo endereço IP público e segundo endereço IP peer BGP a partir da configuração VPN Gateway.
6. Tem agora dois sites com sucesso a provisionados e pode proceder à próxima secção para descarregar ficheiros de configuração.

## <a name="3-download-the-vpn-configuration-files"></a><a name="downloadconfig"></a>3. Descarregue os ficheiros de configuração VPN

Nesta secção, descarrega o ficheiro de configuração VPN para cada um dos sites que criou na secção anterior.

1. No topo da página virtual dos sites WAN **VPN,** selecione o **Site** e, em seguida, selecione **Descarregue a configuração VPN site-to-site**. O Azure cria um ficheiro de configuração com as definições.

   ![Screenshot que mostra a página "sites VPN" com a ação "Transferir configuração VPN site-to-site" selecionada.](./media/connect-virtual-network-gateway-vwan/download.png "transferir")
2. Faça o download e abra o ficheiro de configuração.
3. Repita estes passos para o segundo local. Uma vez abertos os dois ficheiros de configuração, pode proceder à secção seguinte.

## <a name="4-create-the-local-network-gateways"></a><a name="createlocalgateways"></a>4. Criar os gateways de rede locais

Nesta secção, cria-se duas portas de rede locais Azure VPN Gateway. Os ficheiros de configuração do passo anterior contêm as definições de configuração do gateway. Utilize estas definições para criar e configurar os gateways de rede locais Azure VPN Gateway.

1. Crie o portal de rede local utilizando estas definições. Para obter informações sobre como criar uma porta de rede local VPN Gateway, consulte o artigo VPN Gateway [Criar um gateway de rede local](../vpn-gateway/tutorial-site-to-site-portal.md#LocalNetworkGateway).

   * **Endereço IP** - Utilize o endereço IP Instance0 mostrado para *a configuração* de gateways a partir do ficheiro de configuração.
   * **BGP** - Se a ligação for sobre BGP, selecione **configurar as definições de BGP** e introduza a ASN '65515'. Insira o endereço IP peer BGP. Utilize 'Instance0 BgpPeeringAddresses' para *configurar gateways* a partir do ficheiro de configuração.
   * **Subscrição, Grupo de Recursos e Localização** são os mesmos que para o centro DE WAN Virtual.
2. Reveja e crie o portal de rede local. O seu portal de rede local deve ser semelhante a este exemplo.

   ![Screenshot que mostra a página "Configuração" com um endereço IP realçado e "Configurar definições de BGP" selecionadas.](./media/connect-virtual-network-gateway-vwan/lng1.png "instância0")
3. Repita estes passos para criar outro gateway de rede local, mas desta vez, use os valores 'Instance1' em vez dos valores de 'Instance0' a partir do ficheiro de configuração.

   ![ficheiro de configuração de descarregamento](./media/connect-virtual-network-gateway-vwan/lng2.png "instância1")

## <a name="5-create-connections"></a><a name="createlocalgateways"></a>5. Criar ligações

Nesta secção, cria-se uma ligação entre os gateways de rede locais VPN Gateway e o gateway de rede virtual. Para obter etapas sobre como criar uma ligação VPN Gateway, consulte [configurar uma ligação](../vpn-gateway/tutorial-site-to-site-portal.md#CreateConnection).

1. No portal, navegue para o seu gateway de rede virtual e clique em **Conexões**. No topo da página Ligações, clique em **+Adicionar** para abrir a página **Adicionar ligação**.
2. Na página **de ligação Adicionar,** configuure os seguintes valores para a sua ligação:

   * **Nome:** Atribua um nome à sua ligação.
   * **Tipo de ligação:** Selecione **Site-to-site (IPSec)**
   * **Gateway de rede virtual:** O valor é fixo porque está a ligar a partir deste gateway.
   * **Gateway de rede local:** Esta ligação ligará a porta de entrada de rede virtual à porta de entrada de rede local. Escolha um dos gateways de rede locais que criou anteriormente.
   * **Chave partilhada:** Introduza uma chave partilhada.
   * **Protocolo IKE:** Escolha o protocolo IKE.
3. Clique em **OK** para criar a ligação.
4. A ligação é apresentada na página **Ligações** do gateway de rede virtual.

   ![Ligação](./media/connect-virtual-network-gateway-vwan/connect.png "ligação")
5. Repita os passos anteriores para criar uma segunda ligação. Para a segunda ligação, selecione o outro gateway de rede local que criou.
6. Se as ligações estiverem sobre o BGP, depois de ter criado as suas ligações, navegue para uma ligação e selecione **Configuração**. Na página **Configuração,** para **BGP,** selecione **Ativado**. Em seguida, clique em **Guardar**. Repita para a segunda ligação.

## <a name="6-test-connections"></a><a name="test"></a>6. Ligações de teste

Pode testar a conectividade criando duas máquinas virtuais, uma na lateral do gateway de rede virtual VPN Gateway e outra numa rede virtual para o WAN Virtual e, em seguida, pingando as duas máquinas virtuais.

1. Crie uma máquina virtual na rede virtual (Test1-VNet) para Azure VPN Gateway (Test1-VNG). Não crie a máquina virtual na GatewaySubnet.
2. Crie outra rede virtual para ligar ao WAN virtual. Crie uma máquina virtual numa sub-rede desta rede virtual. Esta rede virtual não pode conter quaisquer gateways de rede virtuais. Pode criar rapidamente uma rede virtual utilizando os passos PowerShell no artigo [de ligação site-to-site.](virtual-wan-site-to-site-portal.md#vnet) Certifique-se de que altera os valores antes de executar os cmdlets.
3. Ligue o VNet ao centro VIRTUAL WAN. Na página para o seu WAN virtual, selecione **ligações de rede virtuais** e, em seguida, **+Adicionar a ligação**. Na página **Add connection** (Adicionar ligação), preencha os seguintes campos:

    * **Connection name** (Nome da ligação) - dê um nome à ligação.
    * **Hubs** - selecione o hub que pretende associar a esta ligação.
    * **Subscription** (Subscrição) - verifique a subscrição.
    * **Virtual network** (Rede virtual) - selecione a rede virtual que pretende ligar a este hub. A rede virtual não pode ter um gateway de rede virtual já existente.
4. Clique **em OK** para criar a ligação de rede virtual.
5. A conectividade está agora definida entre os VMs. Você deve ser capaz de ping um VM do outro, a menos que existam quaisquer firewalls ou outras políticas que bloqueiem a comunicação.

## <a name="next-steps"></a>Próximos passos

Para obter medidas para configurar uma política personalizada do IPsec, consulte [configurar uma política personalizada do IPsec para o VIRTUAL WAN](virtual-wan-custom-ipsec-portal.md).
Para obter mais informações sobre o WAN Virtual, consulte [About Azure Virtual WAN](virtual-wan-about.md) e o [Azure Virtual WAN FAQ](virtual-wan-faq.md).