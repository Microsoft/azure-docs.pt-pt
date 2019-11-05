---
title: Conectar um gateway de rede virtual a uma WAN virtual do Azure | Microsoft Docs
description: Este artigo ajuda você a conectar um gateway de rede virtual do Azure a um gateway de VPN de WAN virtual do Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 1f8e0db9921c305edd2ee34efad22cdcf568f8df
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514956"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>Conectar um gateway de VPN (gateway de rede virtual) à WAN virtual

Este artigo ajuda você a configurar a conectividade de um gateway de VPN do Azure (gateway de rede virtual) para uma WAN virtual do Azure (gateway de VPN). Criar uma conexão de um gateway de VPN (gateway de rede virtual) para uma WAN virtual (gateway de VPN) é semelhante a configurar a conectividade com uma WAN virtual a partir de sites de VPN de ramificação.

Para minimizar a possível confusão entre dois recursos, precederemos o gateway com o nome do recurso ao qual estamos nos referindo. Por exemplo, gateway de rede virtual de gateway de VPN e gateway de VPN de WAN virtual.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar, crie os seguintes recursos:

WAN Virtual do Azure

* [Crie uma WAN virtual](virtual-wan-site-to-site-portal.md#openvwan).
* [Crie um hub](virtual-wan-site-to-site-portal.md#hub). O Hub virtual contém o gateway de VPN de WAN virtual.

Rede Virtual do Azure

* Crie uma rede virtual sem nenhum gateway de rede virtual. Verifique se nenhuma das sub-redes de suas redes locais se sobrepõe às redes virtuais às quais você deseja se conectar. Para criar uma rede virtual no portal do Azure, consulte o guia de [início rápido](../virtual-network/quick-create-portal.md).

## <a name="vnetgw"></a>1. criar um gateway de rede virtual do Azure

Crie um gateway de rede virtual gateway de VPN para sua rede virtual no modo ativo-ativo para sua rede virtual. Ao criar o gateway, você pode usar endereços IP públicos existentes para as duas instâncias do gateway ou pode criar novos IPs públicos. Você usa esses IPs públicos ao configurar os sites de WAN virtuais. Para obter mais informações sobre o modo ativo-ativo, consulte [Configurar conexões ativas-ativas](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway).

### <a name="active-active"></a>Configuração de modo ativo-ativo

![ativo-ativo](./media/connect-virtual-network-gateway-vwan/active.png "ativa-ativa")

### <a name="BGP"></a>Configuração de BGP

O ASN do BGP não pode ser 65515. 66515 será usado pela WAN virtual do Azure.

![BGP](./media/connect-virtual-network-gateway-vwan/bgp.png "BGP")

### <a name="pip"></a>Endereços IP públicos

Quando o gateway for criado, navegue até a página **Propriedades** . As propriedades e as definições de configuração serão semelhantes ao exemplo a seguir. Observe os dois endereços IP públicos que são usados para o gateway.

![Properties](./media/connect-virtual-network-gateway-vwan/publicip.png "propriedades")

## <a name="vwansite"></a>2. criar sites VPN de WAN virtual

Para criar sites VPN de WAN virtual, navegue até sua WAN virtual e, em **conectividade**, selecione **sites VPN**. Nesta seção, você criará dois sites VPN de WAN virtual que correspondem aos gateways de rede virtual criados na seção anterior.

1. Selecione **+ criar site**.
2. Na página **criar sites VPN** , digite os seguintes valores:

   * **Região** -(a mesma região do gateway de rede virtual do gateway de VPN do Azure)
   * **Fornecedor do dispositivo** – Insira o fornecedor do dispositivo (qualquer nome)
   * **Espaço de endereço privado** – (Insira um valor ou deixe em branco quando o BGP estiver habilitado)
   * **Border Gateway Protocol** -(defina como **habilitar** se o gateway de rede virtual do gateway de VPN do Azure tiver o BGP habilitado)
   * **Conectar-se a hubs** (selecione o Hub que você criou nos pré-requisitos do menu suspenso)
3. Em **links**, insira os seguintes valores:

   * **Nome do provedor** – Insira um nome de link e um nome de provedor (qualquer nome)
   * **Velocidade-velocidade** (qualquer número)
   * **Endereço IP** -Insira o endereço IP (o mesmo que o primeiro endereço IP público mostrado nas propriedades do gateway de rede virtual (gateway de VPN))
   * **Endereço BGP** e o endereço **ASN** -BGP e ASN. Eles devem ser iguais a um dos endereços IP do par de BGP e o ASN do gateway de rede virtual do gateway de VPN que você configurou na [etapa 1](#vnetgw).
4. Examine e selecione **confirmar** para criar o site.
5. Repita as etapas anteriores para criar o segundo site para corresponder à segunda instância do gateway de rede virtual do gateway de VPN. Você manterá as mesmas configurações, exceto usando o segundo endereço IP público e o segundo endereço IP do par de BGP da configuração do gateway de VPN.
6. Agora você tem dois sites provisionados com êxito e pode prosseguir para a próxima seção para baixar os arquivos de configuração.

## <a name="downloadconfig"></a>3. baixar os arquivos de configuração de VPN

Nesta seção, você baixa o arquivo de configuração de VPN para cada um dos sites que você criou na seção anterior.

1. Na parte superior da página **sites VPN** da WAN virtual, selecione o **site**e selecione **baixar configuração de VPN site a site**. O Azure cria um arquivo de configuração com as configurações.

   ![baixar arquivo de configuração](./media/connect-virtual-network-gateway-vwan/download.png "download")
2. Baixe e abra o arquivo de configuração.
3. Repita essas etapas para o segundo site. Quando os dois arquivos de configuração estiverem abertos, você poderá prosseguir para a próxima seção.

## <a name="createlocalgateways"></a>4. criar os gateways de rede local

Nesta seção, você criará dois gateways de rede local do gateway de VPN do Azure. Os arquivos de configuração da etapa anterior contêm as definições de configuração do gateway. Use essas configurações para criar e configurar os gateways de rede local do gateway de VPN do Azure.

1. Crie o gateway de rede local usando essas configurações. Para obter informações sobre como criar um gateway de rede local de gateway de VPN, consulte o artigo gateway de VPN [criar um gateway de rede local](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway).

   * **Endereço IP** -use o endereço IP Instance0 mostrado para *gatewayconfiguration* do arquivo de configuração.
   * **BGP** -se a conexão for sobre BGP, selecione **definir configurações de BGP** e insira o ASN ' 65515 '. Insira o endereço IP do par de BGP. Use ' Instance0 BgpPeeringAddresses ' para *gatewayconfiguration* do arquivo de configuração.
   * A **assinatura, o grupo de recursos e o local** são os mesmos para o Hub Wan virtual.
2. Examine e crie o gateway de rede local. O gateway de rede local deve ser semelhante a este exemplo.

   ![baixar arquivo de configuração](./media/connect-virtual-network-gateway-vwan/lng1.png "instance0")
3. Repita essas etapas para criar outro gateway de rede local, mas desta vez, use os valores ' instance1 ' em vez dos valores ' Instance0 ' do arquivo de configuração.

   ![baixar arquivo de configuração](./media/connect-virtual-network-gateway-vwan/lng2.png "instance1")

## <a name="createlocalgateways"></a>5. criar conexões

Nesta seção, você cria uma conexão entre os gateways de rede local do gateway de VPN e o gateway de rede virtual. Para obter as etapas sobre como criar uma conexão de gateway de VPN, consulte [Configurar uma conexão](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#CreateConnection).

1. No portal, navegue até seu gateway de rede virtual e clique em **conexões**. No topo da página Ligações, clique em **+Adicionar** para abrir a página **Adicionar ligação**.
2. Na página **Adicionar conexão** , configure os seguintes valores para sua conexão:

   * **Nome:** Atribua um nome à sua ligação.
   * **Tipo de conexão:** Selecionar **site a site (IPSec)**
   * **Gateway de rede virtual:** O valor é fixo porque está a ligar a partir deste gateway.
   * **Gateway de rede local:** Essa conexão conectará o gateway de rede virtual ao gateway de rede local. Escolha um dos gateways de rede local que você criou anteriormente.
   * **Chave compartilhada:** Insira uma chave compartilhada.
   * **Protocolo IKE:** Escolha o protocolo IKE.
   * **BGP:** Escolha **habilitar BGP** se a conexão estiver sobre BGP.
3. Clique em **OK** para criar a ligação.
4. A ligação é apresentada na página **Ligações** do gateway de rede virtual.

   ![Conexão](./media/connect-virtual-network-gateway-vwan/connect.png "ligação")
5. Repita as etapas anteriores para criar uma segunda conexão. Para a segunda conexão, selecione o outro gateway de rede local que você criou.

## <a name="test"></a>6. testar conexões

Você pode testar a conectividade criando duas máquinas virtuais, uma no lado do gateway de rede virtual do gateway de VPN e outra em uma rede virtual para a WAN virtual e, em seguida, executar ping nas duas máquinas virtuais.

1. Crie uma máquina virtual na rede virtual (Test1-VNet) para o gateway de VPN do Azure (Test1-VNG). Não crie a máquina virtual no GatewaySubnet.
2. Crie outra rede virtual para se conectar à WAN virtual. Crie uma máquina virtual em uma sub-rede dessa rede virtual. Esta rede virtual não pode conter nenhum gateway de rede virtual. Você pode criar rapidamente uma rede virtual usando as etapas do PowerShell no artigo de [conexão site a site](virtual-wan-site-to-site-portal.md#vnet) . Certifique-se de alterar os valores antes de executar os cmdlets.
3. Conecte a VNet ao Hub de WAN virtual. Na página de sua WAN virtual, selecione **conexões de rede virtual**e **+ Adicionar conexão**. Na página **Add connection** (Adicionar ligação), preencha os seguintes campos:

    * **Connection name** (Nome da ligação) - dê um nome à ligação.
    * **Hubs** - selecione o hub que pretende associar a esta ligação.
    * **Subscription** (Subscrição) - verifique a subscrição.
    * **Virtual network** (Rede virtual) - selecione a rede virtual que pretende ligar a este hub. A rede virtual não pode ter um gateway de rede virtual já existente.
4. Clique em **OK** para criar a conexão de rede virtual.
5. A conectividade agora está definida entre as VMs. Você deve ser capaz de executar o ping de uma VM da outra, a menos que haja firewalls ou outras políticas que bloqueiem a comunicação.

## <a name="next-steps"></a>Passos seguintes

Para obter as etapas para configurar uma política IPsec personalizada, consulte [Configurar uma política IPSec personalizada para WAN virtual](virtual-wan-custom-ipsec-portal.md).
Para obter mais informações sobre WAN virtual, consulte [sobre a WAN virtual do Azure](virtual-wan-about.md) e as [perguntas frequentes sobre a WAN virtual do Azure](virtual-wan-faq.md).