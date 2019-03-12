---
title: FAQ do Gateway de VPN do Azure | Microsoft Docs
description: FAQ do VPN Gateway. FAQ das ligações em vários locais da Rede Virtual do Microsoft Azure, das ligações de configuração híbrida e dos Gateways de VPN.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: yushwang
ms.openlocfilehash: 98252f7faf7a456edae71dda5fda44850eef74dc
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766993"
---
# <a name="vpn-gateway-faq"></a>FAQ do Gateway de VPN

## <a name="connecting"></a>Ligar às redes virtuais

### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>Posso ligar redes virtuais em diferentes regiões do Azure?

Sim. Na verdade, não existe qualquer restrição de região. Uma rede virtual pode ligar a outra rede virtual na mesma região ou numa região do Azure diferente. 

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>Posso ligar redes virtuais em diferentes subscrições?

Sim.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>Posso ligar a vários sites a partir de uma única rede virtual?

Pode ligar a vários sites com o Windows PowerShell e as APIs REST do Azure. Veja a secção das FAQ [Conetividade Multilocal e VNet a VNet](#V2VMulti).

### <a name="is-there-an-additional-cost-for-setting-up-a-vpn-gateway-as-active-active"></a>Existe um custo adicional para configurar um gateway de VPN como ativo-ativo?

Não. 

### <a name="what-are-my-cross-premises-connection-options"></a>Quais são as minhas opções de ligação em vários locais?

São suportadas as seguintes ligações em vários locais:

* Site a Site – ligação VPN através de IPsec (IKE v1 e IKE v2). Este tipo de ligação requer um dispositivo VPN ou RRAS. Para obter mais informações, consulte [Site-to-Site (Site a Site)](vpn-gateway-howto-site-to-site-resource-manager-portal.md).
* Ponto a Site – ligação VPN através de SSTP (Secure Socket Tunneling Protocol) ou IKE v2. Esta ligação não requer um dispositivo VPN. Para obter mais informações, consulte [Point-to-Site (Ponto a Site)](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* VNet a VNet – este tipo de ligação é igual a uma configuração Site a Site. A ligação VNet a VNet é uma ligação VPN através de IPsec (IKE v1 e IKE v2). Não requer um dispositivo VPN. Para obter mais informações, consulte [VNet-to-VNet (VNet a VNet)](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
* Multilocal – é uma variação de uma configuração Site a Site que lhe permite ligar vários sites no local a uma rede virtual. Para obter mais informações, consulte [Multi-Site (Vários Sites)](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).
* ExpressRoute – o ExpressRoute é uma ligação privada para o Azure da sua WAN e não uma ligação de VPN na Internet pública. Para obter mais informações, consulte [ExpressRoute Technical Overview (Descrição Geral Técnica do ExpressRoute)](../expressroute/expressroute-introduction.md) e as [ExpressRoute FAQ (FAQ do ExpressRoute)](../expressroute/expressroute-faqs.md).

Para obter mais informações sobre as ligações do gateway de VPN, veja [About VPN Gateway (Acerca do Gateway de VPN)](vpn-gateway-about-vpngateways.md).

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>Qual é a diferença entre uma ligação Site a Site e Ponto a Site?

As configurações de **Site a Site** (tunel IPsec/IKE VPN) são entre a localização no local e o Azure. O que significa que pode estabelecer uma ligação a partir de qualquer um dos computadores localizados no local para qualquer máquina virtual ou instância de função na sua rede virtual, dependendo de como escolher configurar o encaminhamento e as permissões. É uma excelente opção para uma ligação em vários locais sempre disponível e é adequada para configurações híbridas. Este tipo de ligação depende de uma aplicação VPN IPsec (dispositivo de hardware ou aplicação de software), que tem de ser implementada na periferia da sua rede. Para criar este tipo de ligação, tem de ter um endereço IPv4 com acesso exterior.

As configurações **Ponto a Site** (VPN sobre SSTP) permitem ligar a partir de um único computador em qualquer lugar a qualquer destino localizado na sua rede virtual. Utiliza o cliente VPN fornecido pelo Windows. Como parte da configuração Ponto a Site, instale um certificado e um pacote de configuração do cliente VPN, que contém as definições que permitem que o computador estabeleça ligação a qualquer máquina virtual ou instância de função na rede virtual. É ótimo quando pretende estabelecer ligação a uma rede virtual, mas não está no local. Também é uma boa opção quando não tiver acesso ao hardware VPN ou a um endereço IPv4 com acesso exterior, que são necessários para uma ligação Site a Site.

Pode configurar a rede virtual para utilizar o Site a Site e o Ponto a Site em simultâneo, desde que crie a ligação Site a Site através de um tipo de VPN baseado na rota do seu gateway. Os tipos de VPN com base na rota são denominados gateways dinâmicos no modelo de implementação clássica.

## <a name="gateways"></a>Gateways de rede virtual

### <a name="is-a-vpn-gateway-a-virtual-network-gateway"></a>Um gateway de VPN é um gateway de rede virtual?

Um gateway de VPN é um tipo de gateway de rede virtual. Um gateway de VPN envia o tráfego encriptado entre a rede virtual e a sua localização no local através de uma ligação pública. Também pode utilizar um gateway de VPN para enviar tráfego entre redes virtuais. Ao criar um gateway de VPN, pode utilizar o "Vpn" do valor -GatewayType. Para obter mais informações, veja [About VPN Gateway configuration settings (Acerca das definições de configuração dos gateway de VPN)](vpn-gateway-about-vpn-gateway-settings.md).

### <a name="what-is-a-policy-based-static-routing-gateway"></a>O que é um gateway baseado em políticas (encaminhamento estático)?

Os gateways baseados em políticas implementam VPNs baseadas em políticas. As VPNs baseadas em políticas encriptam e direcionam os pacotes através de túneis IPsec basedos nas combinações de prefixos de endereços entre a rede no local e a VNet do Azure. Normalmente, a política (ou o Seletor de Tráfego), é definido como uma lista de acesso na configuração de VPN.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>O que é um gateway baseado na rota (encaminhamento dinâmico)?

Os gateways baseados na rota implementam as VPNs baseadas na rota. As VPNs baseadas na rota utilizam “rotas” no reencaminhamento IP ou na tabela de encaminhamento para direcionar os pacotes para as respetivas interfaces de túnel. As interfaces de túnel, em seguida, encriptam ou desencriptam os pacotes dentro e fora dos túneis. A política ou o seletor de tráfego das VPNs baseadas na rota é configurado como “de qualquer ponto a qualquer ponto” (ou carateres universais).

### <a name="can-i-update-my-policy-based-vpn-gateway-to-route-based"></a>Posso atualizar o meu gateway de VPN baseado em Políticas para ser baseado na Rota?
Não. Um tipo de gateway de Vnet do Azure não pode ser alterado de baseado em políticas para baseado na rota, ou ao contrário. O gateway tem de ser eliminado e recriado, um processo que demora cerca de 60 minutos. O endereço IP do gateway não será mantido, nem a Chave Pré-partilhada (PSK).
1. Elimine todas as ligações associadas ao gateway a eliminar.
2. Elimine o gateway:
* [Portal do Azure](vpn-gateway-delete-vnet-gateway-portal.md)
* [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
* [Azure Powershell - clássico](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
3. [Criar um novo gateway do tipo pretendido e concluir a configuração VPN](vpn-gateway-howto-site-to-site-resource-manager-portal.md#VNetGateway)

### <a name="do-i-need-a-gatewaysubnet"></a>Preciso de um "GatewaySubnet"?

Sim. A sub-rede do gateway contém os endereços IP que os serviços do gateway de rede virtual utilizam. Tem de criar uma sub-rede do gateway para que a VNet configure um gateway de rede virtual. Para funcionarem corretamente, todas as sub-redes do gateway têm de ter o nome "GatewaySubnet". Não atribua outro nome à sub-rede do gateway. E não implemente VMs ou quaisquer outros elementos na sub-rede do gateway.

Quando cria a sub-rede do gateway, especifica o número de endereços IP que a sub-rede contém. Os endereços IP na sub-rede de gateway estão alocados no serviço do gateway. Algumas configurações necessitam que sejam alocados mais endereços IP para os serviços de gateway do que outros. Pretende certificar-se de que a sub-rede do gateway contém endereços IP suficientes para acomodar o futuro crescimento e possíveis novas configurações de ligação adicionais. Por isso, apesar de poder criar uma sub-rede do gateway tão pequena como /29, recomendamos que crie uma sub-rede do gateway de /27 ou superior (/ 27, /26, /25, etc.). Observe os requisitos para a configuração que pretende criar e certifique-se de que a sub-rede do gateway que tem irá satisfazer essas necessidades.

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>Posso implementar Virtual Machines ou instâncias de função na minha sub-rede do gateway?

Não.

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>Posso obter o meu endereço IP do gateway de VPN antes de o criar?

Não. Tem de criar o gateway primeiro para obter o endereço IP. O endereço IP é alterado se eliminar e voltar a criar o gateway de VPN.

### <a name="can-i-request-a-static-public-ip-address-for-my-vpn-gateway"></a>Posso pedir um endereço IP Público Estático para o meu gateway de VPN?

Não. Apenas é suportada a atribuição de endereços IP dinâmicos. No entanto, isto não significa que o endereço IP é alterado após ser atribuído ao gateway de VPN. O endereço IP do gateway de VPN só é alterado quando o gateway é eliminado e recriado. O endereço IP público do gateway de VPN não é alterado ao redimensionar, repor ou ao realizar qualquer outra manutenção/atualização interna do gateway de VPN. 

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>Como é que o meu túnel VPN é autenticado?

A VPN do Azure utiliza a autenticação PSK (Chave Pré-partilhada). Geramos uma chave pré-partilhada (PSK) quando criamos o túnel VPN. Pode alterar a PSK gerada automaticamente pela sua com o cmdlet do PowerShell ou a API REST Definir Chave Pré-Partilhada.

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>Posso utilizar a API Definir Chave Pré-Partilhada para configurar a minha VPN de gateway baseada em políticas (encaminhamento estático)?

Sim, o cmdlet do PowerShell e a API Definir Chave Pré-Partilhada podem ser utilizados para configurar VPNs (estáticas) baseadas em políticas e VPNs de encaminhamento (dinâmico) baseadas em políticas do Azure.

### <a name="can-i-use-other-authentication-options"></a>Posso utilizar outras opções de autenticação?

Estamos limitados à utilização de chaves pré-partilhadas (PSK) para a autenticação.

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>Como posso especificar que tráfego atravessa o gateway de VPN?

#### <a name="resource-manager-deployment-model"></a>Modelo de implementação Resource Manager

* PowerShell: utilize "AddressPrefix" para especificar o tráfego para o gateway de rede local.
* Portal do Azure: navegue para o Gateway de rede local > Configuração > Espaço de endereços.

#### <a name="classic-deployment-model"></a>Modelo de implementação clássica

* Portal do Azure: navegue para a rede virtual clássica > ligações VPN > ligações de Redes de VPN > Nome do site local >Site local > Espaço de endereço do cliente. 

### <a name="can-i-configure-force-tunneling"></a>Posso configurar a Imposição do Túnel?

Sim. Veja [Configurar a imposição do túnel](vpn-gateway-about-forced-tunneling.md).

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>Posso configurar o meu próprio servidor VPN no Azure e utilizá-lo para estabelecer ligação à minha rede no local?

Sim, pode implementar os seus servidores ou gateways de VPN no Azure a partir do Azure Marketplace ou criar os seus próprios routers VPN. Tem de configurar rotas definidas pelo utilizador na rede virtual para garantir que o tráfego é encaminhado corretamente entre as redes no local e as sub-redes da rede virtual.

### <a name="gatewayports"></a>Por que motivo determinadas portas são abertas no meu gateway de rede virtual?

São necessárias para a comunicação de infraestrutura do Azure. Estão protegidas (bloqueadas) pelos certificados do Azure. Sem os certificados adequados, as entidades externas, incluindo os clientes desses gateways, não terão qualquer efeito nesses pontos finais.

Um gateway de rede virtual é basicamente um dispositivo de hospedagem múltipla, com uma NIC a aceder à rede privada do cliente e uma NIC voltada para a rede pública. As entidades de infraestruturas do Azure não conseguem aceder às redes privadas do cliente por motivos de conformidade, pelo que precisam de utilizar pontos finais públicos para a comunicação de infraestrutura. Os pontos finais públicos são analisados periodicamente pela auditoria de segurança do Azure.

### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>Mais informações sobre tipos de gateways, requisitos e débito

Para obter mais informações, veja [About VPN Gateway configuration settings (Acerca das definições de configuração dos gateway de VPN)](vpn-gateway-about-vpn-gateway-settings.md).

## <a name="s2s"></a>Ligações Site a Site e dispositivos VPN

### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>O que devo considerar ao selecionar um dispositivo VPN?

Validamos um conjunto de dispositivos de Rede de VPNs padrão em parceria com fornecedores de dispositivos. Pode encontrar uma lista de dispositivos VPN compatíveis conhecidos, exemplos ou instruções de configuração correspondentes e especificações de dispositivo no artigo [About VPN devices (Acerca de dispositivos VPN)](vpn-gateway-about-vpn-devices.md). Todos os dispositivos das famílias de dispositivos listadas como compatíveis devem funcionar com a Rede Virtual. Para ajudar a configurar o dispositivo VPN, consulte o exemplo de configuração do dispositivo ou a ligação que corresponde a uma família de dispositivos adequados.

### <a name="where-can-i-find-vpn-device-configuration-settings"></a>Onde posso encontrar as definições de configuração de dispositivos VPN?

[!INCLUDE [vpn devices](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="how-do-i-edit-vpn-device-configuration-samples"></a>Como posso editar os exemplos de configuração do dispositivo VPN?

Para obter informações sobre a edição de amostras de configuração do dispositivo, consulte [Editing samples (Editar amostras)](vpn-gateway-about-vpn-devices.md#editing).

### <a name="where-do-i-find-ipsec-and-ike-parameters"></a>Onde posso encontrar os parâmetros do IPsec e do IKE?

Para os parâmetros de IPsec/IKE, consulte [Parameters (Parâmetros)](vpn-gateway-about-vpn-devices.md#ipsec).

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>Por que motivo o meu túnel VPN baseado em políticas diminui quando o tráfego está inativo?

Este comportamento está previsto para gateways de VPN baseados em políticas (também conhecido como encaminhamento estático). Quando o tráfego através do túnel estiver inativo durante mais de 5 minutos, o túnel é desativado. Quando o tráfego começar a fluir em qualquer direção, o túnel é restabelecido imediatamente.

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>Posso utilizar VPNs de software para ligar ao Azure?

Suportamos servidores de Encaminhamento e Acesso Remoto (RRAS) do Windows Server 2012 para uma configuração em vários locais Site a Site.

Outras soluções de VPN de software devem funcionar com o nosso gateway, desde que obedeçam às implementações de IPsec de norma da indústria. Contacte o fornecedor do software para as obter instruções de configuração e de suporte.

## <a name="P2S"></a>Ponto a Site com a autenticação de certificados nativa do Azure

Esta secção aplica-se ao Modelo de implementação Resource Manager.

[!INCLUDE [P2S Azure cert](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="P2SRADIUS"></a>Ponto a Site com a autenticação RADIUS

Esta secção aplica-se ao Modelo de implementação Resource Manager.

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="V2VMulti"></a>Ligações VNet a VNet e de Vários Sites

[!INCLUDE [vpn-gateway-vnet-vnet-faq-include](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>Posso utilizar o gateway de VPN do Azure para transitar o tráfego entre os meus sites no local ou para outra rede virtual?

**Modelo de implementação Resource Manager**<br>
Sim. Veja a secção [BGP](#bgp) para obter mais informações.

**Modelo de implementação clássica**<br>
É possível transitar o tráfego através do gateway de VPN do Azure com o modelo de implementação clássica, mas tal depende de espaços de endereços definidos estaticamente no ficheiro de configuração de rede. O BGP ainda não é suportado com Redes Virtuais do Azure nem gateways de VPN mediante a utilização do modelo de implementação clássica. Sem o BGP, a definição manual dos espaços de endereços de trânsito é muito propensa a erros e não se recomenda.

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>O Azure gera a mesma chave pré-partilhada IPsec/IKE para todas as minhas ligações VPN para a mesma rede virtual?

Não, por predefinição, o Azure gera chaves pré-partilhadas diferentes para diferentes ligações VPN. No entanto, pode utilizar o cmdlet do PowerShell ou a API REST Definir Chave de Gateway de VPN para definir o valor da chave que preferir. A chave TEM de ser uma cadeia alfanumérica com um comprimento entre 1 a 128 carateres.

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>Tenho mais largura de banda com mais Redes de VPNs do que para uma única rede virtual?

Não, todos os túneis VPN, incluindo VPNs Ponto a Site, partilham o mesmo gateway de VPN do Azure e a largura de banda disponível.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>Posso configurar vários túneis entre a minha rede virtual e o meu site no local através da VPN multilocal?

Sim, mas tem de configurar o BGP em ambos os túneis para a mesma localização.

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>Posso utilizar VPNs Ponto a Site com a minha rede virtual com vários túneis VPN?

Sim, as VPNs Ponto a Site (P2S) podem ser utilizadas com os gateways de VPN a ligar a vários sites no local e a outras redes virtuais.

### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>Posso ligar uma rede virtual com VPNs IPsec ao meu circuito ExpressRoute?

Sim, esta ação é suportada. Para obter mais informações, veja [Configurar as ligações de Rede de VPNs e o ExpressRoute que coexistem](../expressroute/expressroute-howto-coexist-classic.md).

## <a name="ipsecike"></a>IPsec/política IKE

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]


## <a name="bgp"></a>BGP

[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

## <a name="vms"></a>VMs e conectividade em vários locais

### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>Se a minha máquina virtual estiver numa rede virtual e tiver uma ligação em vários locais, como devo estabelecer ligação à VM?

Tem algumas opções. Se tiver o RDP ativado para a sua VM, pode ligar à máquina virtual através do endereço IP privado. Nesse caso, deve especificar o endereço IP privado e a porta à qual quer ligar (normalmente, 3389). Terá de configurar a porta na sua máquina virtual para o tráfego.

Também pode ligar à máquina virtual através do endereço IP privado a partir de outra máquina virtual que esteja localizada na mesma rede virtual. Não pode fazer o RDP à máquina virtual através do endereço IP privado se estiver a ligar a partir de uma localização fora da sua rede virtual. Por exemplo, se tiver uma rede virtual Ponto a Site configurada e não estabelecer ligação a partir do computador, não poderá ligar à máquina virtual através do endereço IP privado.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>Se a minha máquina virtual estiver numa rede virtual com conetividade em vários locais, todo o tráfego da minha VM passa por essa ligação?

Não. Apenas o tráfego que tem um IP de destino contido nos intervalos de endereços IP da Rede Local da rede virtual que especificou passará pelo gateway de rede virtual. O tráfego tem um IP de destino localizado na rede virtual e permanece na rede virtual. O outro tipo de tráfego é enviado através do balanceador de carga para as redes públicas, ou se a imposição do túnel for utilizada, é enviado através do gateway de VPN do Azure.

### <a name="how-do-i-troubleshoot-an-rdp-connection-to-a-vm"></a>Como resolver problemas de uma ligação RDP numa VM

[!INCLUDE [Troubleshoot VM connection](../../includes/vpn-gateway-connect-vm-troubleshoot-include.md)]


## <a name="faq"></a>FAQ da Rede Virtual

Pode ver informações adicionais sobre a rede virtual nas [FAQ da Rede Virtual](../virtual-network/virtual-networks-faq.md).

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre o Gateway de VPN, veja [About VPN Gateway (Acerca do Gateway de VPN)](vpn-gateway-about-vpngateways.md).
* Para obter mais informações sobre as definições de configuração do Gateway de VPN, veja [About VPN Gateway configuration settings (Acerca das definições de configuração do gateway de VPN)](vpn-gateway-about-vpn-gateway-settings.md).
