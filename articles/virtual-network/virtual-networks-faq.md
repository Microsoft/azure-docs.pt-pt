---
title: Perguntas frequentes sobre a rede virtual do Azure
titlesuffix: Azure Virtual Network
description: Respostas para as perguntas mais frequentes sobre Microsoft Azure redes virtuais.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2019
ms.author: kumud
ms.openlocfilehash: 642b99e3eaaf73844d30d1cd464ae0b777b0b3fa
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71957809"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Perguntas frequentes sobre a rede virtual do Azure

## <a name="virtual-network-basics"></a>Noções básicas da rede virtual

### <a name="what-is-an-azure-virtual-network-vnet"></a>O que é uma VNet (rede virtual) do Azure?
Uma VNet (rede virtual) do Azure é uma representação de sua própria rede na nuvem. É um isolamento lógico da nuvem do Azure dedicado à sua subscrição. Você pode usar o VNets para provisionar e gerenciar redes virtuais privadas (VPNs) no Azure e, opcionalmente, vincular o VNets a outros VNets no Azure, ou com sua infraestrutura de ti local para criar soluções híbridas ou entre locais. Cada VNet que você cria tem seu próprio bloco CIDR e pode ser vinculada a outras VNets e redes locais, desde que os blocos CIDR não se sobreponham. Você também tem controle das configurações do servidor DNS para VNets e segmentação da VNet em sub-redes.

Use VNets para:

* Crie uma VNet somente de nuvem privada dedicada. Às vezes, você não precisa de uma configuração entre locais para sua solução. Quando você cria uma VNet, seus serviços e VMs em sua VNet podem se comunicar de forma direta e segura entre si na nuvem. Você ainda pode configurar conexões de ponto de extremidade para as VMs e serviços que exigem comunicação com a Internet, como parte de sua solução.

* Estenda com segurança seu data center. Com o VNets, você pode criar VPNs de site a site (S2S) tradicionais para dimensionar com segurança a capacidade do datacenter. As VPNs S2S usam IPSEC para fornecer uma conexão segura entre o gateway de VPN corporativo e o Azure.

* Habilite cenários de nuvem híbrida. O VNets oferece a flexibilidade para dar suporte a uma variedade de cenários de nuvem híbrida. Você pode conectar aplicativos baseados em nuvem com segurança a qualquer tipo de sistema local, como mainframes e sistemas UNIX.

### <a name="how-do-i-get-started"></a>Como posso começar?
Visite a [documentação da rede virtual](https://docs.microsoft.com/azure/virtual-network/) para começar. Este conteúdo fornece informações de visão geral e de implantação para todos os recursos de VNet.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Posso usar VNets sem conectividade entre locais?
Sim. Você pode usar uma VNet sem conectá-la a seu local. Por exemplo, você pode executar o Microsoft Windows Server Active Directory controladores de domínio e farms do SharePoint somente em uma VNet do Azure.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Posso executar a otimização de WAN entre VNets ou uma VNet e minha data center local?
Sim. Você pode implantar uma [solução de virtualização de rede de otimização de WAN](https://azuremarketplace.microsoft.com/en-us/marketplace/?term=wan%20optimization) de vários fornecedores por meio do Azure Marketplace.

## <a name="configuration"></a>Configuração

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Quais ferramentas eu uso para criar uma VNet?
Você pode usar as seguintes ferramentas para criar ou configurar uma VNet:

* Portal do Azure
* PowerShell
* CLI do Azure
* Um arquivo de configuração de rede (netcfg – somente para VNets clássicas). Consulte o artigo [Configurar uma VNet usando um arquivo de configuração de rede](virtual-networks-using-network-configuration-file.md) .

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Quais intervalos de endereços posso usar em meu VNets?
Qualquer intervalo de endereços IP definido no [RFC 1918](https://tools.ietf.org/html/rfc1918). Por exemplo, 10.0.0.0/16. Você não pode adicionar os seguintes intervalos de endereços:
* 224.0.0.0/4 (multicast)
* 255.255.255.255/32 (difusão)
* 127.0.0.0/8 (loopback)
* 169.254.0.0/16 (link-local)
* 168.63.129.16/32 (DNS interno)

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Posso ter endereços IP públicos em meu VNets?
Sim. Para obter mais informações sobre intervalos de endereços IP públicos, consulte [criar uma rede virtual](manage-virtual-network.md#create-a-virtual-network). Endereços IP públicos não são diretamente acessíveis a partir da internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Há um limite para o número de sub-redes na minha VNet?
Sim. Consulte [limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) para obter detalhes. Espaços de endereço de sub-rede não podem se sobrepor um ao outro.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Há alguma restrição sobre o uso de endereços IP dentro dessas sub-redes?
Sim. O Azure reserva cinco endereços IP em cada sub-rede. Esses são x. x. x. 0-x. x. x. 3 e o último endereço da sub-rede. x. x. x. 1-x. x. 3 é reservado em cada sub-rede para os serviços do Azure.   
- x. x. x. 0: Endereço de rede
- x. x. x. 1: Reservado pelo Azure para o gateway padrão
- x. x. x. 2, x. x. x. 3: Reservado pelo Azure para mapear os IPs de DNS do Azure para o espaço VNet
- x. x. x. 255: Endereço de difusão de rede

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Qual é o tamanho e o tamanho de VNets e sub-redes?
A menor sub-rede com suporte é/29 e a maior é/8 (usando definições de sub-rede CIDR).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Posso colocar minhas VLANs no Azure usando o VNets?
Não. VNets são sobreposições de camada 3. O Azure não dá suporte a nenhuma semântica de camada 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Posso especificar políticas de roteamento personalizadas em minhas VNets e sub-redes?
Sim. Você pode criar uma tabela de rotas e associá-la a uma sub-rede. Para obter mais informações sobre roteamento no Azure, consulte [visão geral de roteamento](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>O VNets dá suporte a multicast ou difusão?
Não. Não há suporte para multicast e difusão.

### <a name="what-protocols-can-i-use-within-vnets"></a>Quais protocolos posso usar em VNets?
Você pode usar protocolos TCP/IP TCP, UDP e ICMP em VNets. O unicast tem suporte em VNets, com exceção do protocolo DHCP via unicast (porta de origem UDP/68/porta de destino UDP/67). O multicast, a difusão, os pacotes encapsulados IP em IP e os pacotes de encapsulamento de roteamento genérico (GRE) são bloqueados em VNets. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Posso executar ping em meus roteadores padrão em uma VNet?
Não.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Posso usar o tracert para diagnosticar a conectividade?
Não.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Posso adicionar sub-redes depois que a VNet é criada?
Sim. As sub-redes podem ser adicionadas ao VNets a qualquer momento, desde que o intervalo de endereços de sub-rede não faça parte de outra sub-rede e haja espaço disponível restante no intervalo de endereços da rede virtual.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Posso modificar o tamanho da minha sub-rede depois de criá-la?
Sim. Você pode adicionar, remover, expandir ou reduzir uma sub-rede se não houver VMs ou serviços implantados nela.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Posso modificar sub-redes depois de criá-las?
Sim. Você pode adicionar, remover e modificar os blocos CIDR usados por uma VNet.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Se eu estiver executando meus serviços em uma VNet, posso me conectar à Internet?
Sim. Todos os serviços implantados em uma VNet podem conectar a saída à Internet. Para saber mais sobre as conexões de Internet de saída no Azure, confira [conexões de saída](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se você quiser conectar a entrada a um recurso implantado por meio do Resource Manager, o recurso deverá ter um endereço IP público atribuído a ele. Para saber mais sobre endereços IP públicos, consulte [endereços IP públicos](virtual-network-public-ip-address.md). Cada serviço de nuvem do Azure implantado no Azure tem um VIP publicamente endereçável atribuído a ele. Você define pontos de extremidade de entrada para funções de PaaS e pontos de extremidade para máquinas virtuais para permitir que esses serviços aceitem conexões da Internet.

### <a name="do-vnets-support-ipv6"></a>O VNets dá suporte a IPv6?
Não. Você não pode usar o IPv6 com VNets no momento. No entanto, você pode atribuir endereços IPv6 aos balanceadores de carga do Azure para balancear a carga de máquinas virtuais. Para obter detalhes, consulte [visão geral do IPv6 para Azure Load Balancer](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="can-a-vnet-span-regions"></a>Uma VNet pode abranger regiões?
Não. Uma VNet é limitada a uma única região. No entanto, uma rede virtual abrange zonas de disponibilidade. Para saber mais sobre as zonas de disponibilidade, veja [Descrição geral das zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Você pode conectar redes virtuais em regiões diferentes com o emparelhamento de rede virtual. Para obter detalhes, consulte [visão geral de emparelhamento de rede virtual](virtual-network-peering-overview.md)

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Posso conectar uma VNet a outra VNet no Azure?
Sim. Você pode conectar uma VNet a outra VNet usando:
- **Emparelhamento de rede virtual**: Para obter detalhes, consulte [visão geral de emparelhamento VNet](virtual-network-peering-overview.md)
- **Um gateway de VPN do Azure**: Para obter detalhes, consulte [Configurar uma conexão vnet a vnet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Resolução de nomes (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Quais são minhas opções de DNS para VNets?
Use a tabela de decisão na página [resolução de nomes para VMs e instâncias de função](virtual-networks-name-resolution-for-vms-and-role-instances.md) para orientá-lo em todas as opções de DNS disponíveis.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Posso especificar servidores DNS para uma VNet?
Sim. Você pode especificar endereços IP do servidor DNS nas configurações de VNet. A configuração é aplicada como o (s) servidor (es) DNS padrão para todas as VMs na VNet.

### <a name="how-many-dns-servers-can-i-specify"></a>Quantos servidores DNS posso especificar?
Faça referência aos [limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Posso modificar meus servidores DNS depois de ter criado a rede?
Sim. Você pode alterar a lista de servidores DNS para sua VNet a qualquer momento. Se você alterar a lista de servidores DNS, será necessário reiniciar cada uma das VMs em sua VNet para que elas obtenham o novo servidor DNS.

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>O que é o DNS fornecido pelo Azure e ele funciona com o VNets?
O DNS fornecido pelo Azure é um serviço DNS de vários locatários oferecido pela Microsoft. O Azure registra todas as suas VMs e instâncias de função de serviço de nuvem nesse serviço. Esse serviço fornece a resolução de nomes pelo nome do host para VMs e instâncias de função contidas no mesmo serviço de nuvem e pelo FQDN para VMs e instâncias de função na mesma VNet. Para saber mais sobre o DNS, consulte [resolução de nomes para instâncias de função de VMs e serviços de nuvem](virtual-networks-name-resolution-for-vms-and-role-instances.md).

Há uma limitação para os primeiros serviços de nuvem 100 em uma VNet para resolução de nomes entre locatários usando o DNS fornecido pelo Azure. Se você estiver usando seu próprio servidor DNS, essa limitação não se aplicará.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Posso substituir minhas configurações de DNS por VM ou serviço de nuvem?
Sim. Você pode definir servidores DNS por VM ou serviço de nuvem para substituir as configurações de rede padrão. No entanto, é recomendável que você use o DNS em toda a rede o máximo possível.

### <a name="can-i-bring-my-own-dns-suffix"></a>Posso colocar meu próprio sufixo DNS?
Não. Você não pode especificar um sufixo DNS personalizado para seu VNets.

## <a name="connecting-virtual-machines"></a>Conectando máquinas virtuais

### <a name="can-i-deploy-vms-to-a-vnet"></a>Posso implantar VMs em uma VNet?
Sim. Todas as NICs (interfaces de rede) conectadas a uma VM implantada por meio do modelo de implantação do Resource Manager devem estar conectadas a uma VNet. As VMs implantadas por meio do modelo de implantação clássico podem ser conectadas opcionalmente a uma VNet.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Quais são os diferentes tipos de endereços IP que posso atribuir a VMs?
* **Pessoal** Atribuído a cada NIC em cada VM. O endereço é atribuído usando o método estático ou dinâmico. Os endereços IP privados são atribuídos do intervalo que você especificou nas configurações de sub-rede da sua VNet. Os recursos implantados por meio do modelo de implantação clássico recebem endereços IP privados, mesmo que não estejam conectados a uma VNet. O comportamento do método de alocação é diferente dependendo se um recurso foi implantado com o modelo de implantação do Resource Manager ou clássico: 

  - **Gerenciador de recursos**: Um endereço IP privado atribuído com o método dinâmico ou estático permanece atribuído a uma máquina virtual (Resource Manager) até que o recurso seja excluído. A diferença é que você seleciona o endereço a ser atribuído ao usar estático e o Azure escolhe ao usar dinâmico. 
  - **Clássico**: Um endereço IP privado atribuído com o método dinâmico pode ser alterado quando uma VM de máquina virtual (clássica) é reiniciada depois de ter sido no estado parado (desalocado). Se você precisar garantir que o endereço IP privado para um recurso implantado por meio do modelo de implantação clássico nunca seja alterado, atribua um endereço IP privado com o método estático.

* **Publicada** Opcionalmente atribuída a NICs anexadas a VMs implantadas por meio do modelo de implantação Azure Resource Manager. O endereço pode ser atribuído com o método de alocação estático ou dinâmico. Todas as instâncias de função de VMs e serviços de nuvem implantadas por meio do modelo de implantação clássico existem em um serviço de nuvem, que é atribuído a um endereço VIP (IP virtual público) *dinâmico*. Um endereço IP *estático* público, chamado de [endereço IP reservado](virtual-networks-reserved-public-ip.md), pode, opcionalmente, ser atribuído como um VIP. Você pode atribuir endereços IP públicos a VMs individuais ou instâncias de função de serviços de nuvem implantadas por meio do modelo de implantação clássico. Esses endereços são chamados de [IP público em nível de instância (endereços ILPIP](virtual-networks-instance-level-public-ip.md) e podem ser atribuídos dinamicamente.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Posso reservar um endereço IP privado para uma VM que vou criar em um momento posterior?
Não. Você não pode reservar um endereço IP privado. Se um endereço IP privado estiver disponível, ele será atribuído a uma VM ou instância de função pelo servidor DHCP. A VM pode ou não ser aquela à qual você deseja que o endereço IP privado seja atribuído. No entanto, você pode alterar o endereço IP privado de uma VM já criada, para qualquer endereço IP privado disponível.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Os endereços IP privados são alterados para VMs em uma VNet?
Depende. Se a VM foi implantada por meio do Resource Manager, não, independentemente se o endereço IP foi atribuído com o método de alocação estático ou dinâmico. Se a VM tiver sido implantada por meio do modelo de implantação clássico, os endereços IP dinâmicos poderão ser alterados quando uma VM for iniciada depois de ter sido no estado parado (desalocado). O endereço é liberado de uma VM implantada por meio de um modelo de implantação quando a VM é excluída.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Posso atribuir manualmente endereços IP a NICs no sistema operacional da VM?
Sim, mas não é recomendável, a menos que seja necessário, como ao atribuir vários endereços IP a uma máquina virtual. Para obter detalhes, consulte [adicionando vários endereços IP a uma máquina virtual](virtual-network-multiple-ip-addresses-portal.md#os-config). Se o endereço IP atribuído a uma NIC do Azure anexada a uma VM for alterado e o endereço IP dentro do sistema operacional da VM for diferente, você perderá a conectividade com a VM.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Se eu parar um slot de implantação do serviço de nuvem ou desligar uma VM de dentro do sistema operacional, o que acontecerá com meus endereços IP?
Nada. Os endereços IP (VIP público, público e privado) permanecem atribuídos ao slot de implantação do serviço de nuvem ou à VM.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Posso mover VMs de uma sub-rede para outra sub-rede em uma rede virtual sem reimplantação?
Sim. Você pode encontrar mais informações no artigo [como mover uma VM ou instância de função para uma sub-rede diferente](virtual-networks-move-vm-role-to-subnet.md) .

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Posso configurar um endereço MAC estático para minha VM?
Não. Um endereço MAC não pode ser configurado estaticamente.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>O endereço MAC permanecerá o mesmo para a minha VM depois que ele for criado?
Sim, o endereço MAC permanece o mesmo para uma VM implantada por meio do Gerenciador de recursos e dos modelos de implantação clássicos até que ela seja excluída. Anteriormente, o endereço MAC foi liberado se a VM foi interrompida (desalocada), mas agora o endereço MAC é retido mesmo quando a VM está no estado desalocado. O endereço MAC permanece atribuído à interface de rede até que a interface de rede seja excluída ou o endereço IP privado atribuído à configuração de IP primário da interface de rede primária seja alterado. 

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Posso me conectar à Internet de uma VM em uma VNet?
Sim. Todas as instâncias de função de VMs e serviços de nuvem implantadas em uma VNet podem se conectar à Internet.

## <a name="azure-services-that-connect-to-vnets"></a>Serviços do Azure que se conectam ao VNets

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Posso usar os aplicativos Web do serviço Azure App com uma VNet?
Sim. Você pode implantar aplicativos Web dentro de uma VNet usando um ASE (Ambiente do Serviço de Aplicativo), conectar o back-end de seus aplicativos ao seu VNets com integração VNet e bloquear o tráfego de entrada para seu aplicativo com pontos de extremidade de serviço. Para obter mais informações, veja os artigos seguintes:

* [Recursos de rede do serviço de aplicativo](../app-service/networking-features.md)
* [Criando aplicativos Web em um Ambiente do Serviço de Aplicativo](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Integrar seu aplicativo a uma rede virtual do Azure](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Restrições de acesso do serviço de aplicativo](../app-service/app-service-ip-restrictions.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Posso implantar serviços de nuvem com as funções Web e de trabalho (PaaS) em uma VNet?
Sim. Você pode (opcionalmente) implantar instâncias de função de serviços de nuvem em VNets. Para fazer isso, especifique o nome da VNet e os mapeamentos de função/sub-rede na seção de configuração de rede da sua configuração de serviço. Você não precisa atualizar nenhum de seus binários.

### <a name="can-i-connect-a-virtual-machine-scale-set-to-a-vnet"></a>Posso conectar um conjunto de dimensionamento de máquinas virtuais a uma VNet?
Sim. Você deve conectar um conjunto de dimensionamento de máquinas virtuais a uma VNet.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Há uma lista completa de serviços do Azure que posso implantar recursos de em uma VNet?
Sim, para obter detalhes, consulte [integração de rede virtual para serviços do Azure](virtual-network-for-azure-services.md).

### <a name="how-can-i-restrict-access-to-azure-paas-resources-from-a-vnet"></a>Como posso restringir o acesso aos recursos de PaaS do Azure de uma VNet?

Os recursos implantados por meio de alguns serviços de PaaS do Azure (como o armazenamento do Azure e banco de dados SQL do Azure) podem restringir o acesso à rede para VNet através do uso de pontos de extremidade de serviço de rede virtual ou do link privado do Azure. Para obter detalhes, consulte [visão geral de pontos de extremidade de serviço de rede virtual](virtual-network-service-endpoints-overview.md), [visão geral do link privado do Azure](../private-link/private-link-overview.md)

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Posso mover meus serviços para dentro e fora do VNets?
Não. Não é possível mover serviços para dentro e fora do VNets. Para mover um recurso para outra VNet, você precisa excluir e reimplantar o recurso.

## <a name="security"></a>Segurança

### <a name="what-is-the-security-model-for-vnets"></a>O que é o modelo de segurança para VNets?
As VNets são isoladas umas das outras e outros serviços hospedados na infraestrutura do Azure. Uma VNet é um limite de confiança.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Posso restringir o fluxo de tráfego de entrada ou saída para recursos conectados à VNet?
Sim. Você pode aplicar [grupos de segurança de rede](security-overview.md) a sub-redes individuais em uma vnet, NICs conectadas a uma vnet ou ambas.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Posso implementar um firewall entre recursos conectados à VNet?
Sim. Você pode implantar um [dispositivo virtual de rede de firewall](https://azure.microsoft.com/marketplace/?term=firewall) de vários fornecedores por meio do Azure Marketplace.

### <a name="is-there-information-available-about-securing-vnets"></a>Há informações disponíveis sobre como proteger o VNets?
Sim. Para obter detalhes, consulte [visão geral de segurança de rede do Azure](../security/fundamentals/network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="apis-schemas-and-tools"></a>APIs, esquemas e ferramentas

### <a name="can-i-manage-vnets-from-code"></a>Posso gerenciar o VNets a partir do código?
Sim. Você pode usar APIs REST para VNets nos modelos de implantação [Azure Resource Manager](/rest/api/virtual-network) e [clássico](https://go.microsoft.com/fwlink/?LinkId=296833) .

### <a name="is-there-tooling-support-for-vnets"></a>Há suporte para ferramentas para VNets?
Sim. Saiba mais sobre como usar:
- O portal do Azure implantar VNets por meio dos modelos de implantação [Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) e [clássico](virtual-networks-create-vnet-classic-pportal.md) .
- PowerShell para gerenciar o VNets implantado por meio do [Resource Manager](/powershell/module/az.network) e dos modelos de implantação [clássicos](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0) .
- A CLI (interface de linha de comando) do Azure para implantar e gerenciar VNets implantadas por meio do [Resource Manager](/cli/azure/network/vnet) e dos modelos de implantação [clássicos](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-commands-to-manage-network-resources) .  

## <a name="vnet-peering"></a>VNet peering

### <a name="what-is-vnet-peering"></a>O que é emparelhamento VNet?
O emparelhamento VNet (ou emparelhamento de rede virtual) permite que você conecte redes virtuais. Uma conexão de emparelhamento VNet entre redes virtuais permite rotear o tráfego entre elas de forma privada por meio de endereços IPv4. As máquinas virtuais no VNets emparelhado podem se comunicar entre si como se estivessem dentro da mesma rede. Essas redes virtuais podem estar na mesma região ou em regiões diferentes (também conhecidas como emparelhamento de VNet global). As conexões de emparelhamento VNet também podem ser criadas nas assinaturas do Azure.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>Posso criar uma conexão de emparelhamento com uma VNet em uma região diferente?
Sim. O emparelhamento de VNet global permite emparelhar VNets em regiões diferentes. O emparelhamento VNet global está disponível em todas as regiões públicas do Azure, nas regiões de nuvem da China e nas regiões de nuvem do governo. Não é possível globalmente emparelhar de regiões públicas do Azure para regiões de nuvem nacionais.

### <a name="what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers"></a>Quais são as restrições relacionadas ao emparelhamento de VNet global e aos balanceadores de carga?
Se as duas redes virtuais estiverem em regiões diferentes (emparelhamento VNet global), você não poderá se conectar a recursos que usam Load Balancer básica. Você pode se conectar a recursos que usam Standard Load Balancer.
Os recursos a seguir usam balanceadores de carga básicos, o que significa que você não pode se comunicar com eles entre o emparelhamento VNet global:
- VMs por trás de balanceadores de carga básicos
- Conjuntos de dimensionamento de máquinas virtuais com balanceadores de carga básicos 
- Cache de Redis 
- SKU do gateway de aplicativo (v1)
- Service Fabric
- MI SQL
- Gestão de API
- Serviço de Domínio do Active Directory (ADDS)
- Aplicações Lógicas
- HDInsight
-   Azure Batch
- Ambiente do Serviço de Aplicações

Você pode se conectar a esses recursos via ExpressRoute ou VNet a VNet por meio de gateways de VNet.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Posso habilitar o emparelhamento VNet se minhas redes virtuais pertencerem a assinaturas em locatários Azure Active Directory diferentes?
Sim. É possível estabelecer o emparelhamento VNet (seja local ou global) se suas assinaturas pertencerem a locatários Azure Active Directory diferentes. Você pode fazer isso por meio do PowerShell ou da CLI. O portal ainda não tem suporte.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>Minha conexão de emparelhamento VNet está em estado *iniciado* , por que não consigo me conectar?
Se a conexão de emparelhamento estiver em um estado *iniciado* , isso significa que você criou apenas um link. Um link bidirecional deve ser criado para estabelecer uma conexão bem-sucedida. Por exemplo, para emparelhar a VNet a na VNet B, um link deve ser criado de VNetA para VNetB e de VNetB para VNetA. A criação de ambos os links alterará o estado para *conectado*.

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>Minha conexão de emparelhamento VNet está em estado *desconectado* , por que não posso criar uma conexão de emparelhamento?
Se a conexão de emparelhamento VNet estiver em um estado *desconectado* , isso significará que um dos links criados foi excluído. Para restabelecer uma conexão de emparelhamento, será necessário excluir o link e recriá-lo.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>Posso emparelhar minha VNet com uma VNet em uma assinatura diferente?
Sim. Você pode emparelhar VNets em assinaturas e em regiões.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Posso emparelhar dois VNets com intervalos de endereços correspondentes ou sobrepostos?
Não. Os espaços de endereço não devem se sobrepor para habilitar o emparelhamento VNet.

### <a name="how-much-do-vnet-peering-links-cost"></a>Quanto custam os links de emparelhamento de VNet?
Não há nenhum encargo para a criação de uma conexão de emparelhamento VNet. A transferência de dados entre conexões de emparelhamento é cobrada. [Consulte aqui](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="is-vnet-peering-traffic-encrypted"></a>O tráfego de emparelhamento VNet está criptografado?
Não. O tráfego entre os recursos no VNets emparelhado é privado e isolado. Ele permanece completamente no backbone da Microsoft.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Por que minha conexão de emparelhamento está em um estado *desconectado* ?
As conexões de emparelhamento VNet entram no estado *desconectado* quando um link de emparelhamento vnet é excluído. Você deve excluir ambos os links para restabelecer uma conexão de emparelhamento bem-sucedida.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>Se eu VNetA a VNetB e eu emparelhar VNetB para VNetC, isso significa que VNetA e VNetC são emparelhados?
Não. Não há suporte para emparelhamento transitivo. Você deve emparelhar VNetA e VNetC para que isso ocorra.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Há limitações de largura de banda para conexões de emparelhamento?
Não. O emparelhamento VNet, seja local ou global, não impõe restrições de largura de banda. A largura de banda é limitada apenas pela VM ou pelo recurso de computação.

### <a name="how-can-i-troubleshoot-vnet-peering-issues"></a>Como posso solucionar problemas de emparelhamento VNet?
Este é um [Guia de solução de problemas](https://support.microsoft.com/en-us/help/4486956/troubleshooter-for-virtual-network-peering-issues) que você pode experimentar.

## <a name="virtual-network-tap"></a>TAP de rede virtual

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>Quais regiões do Azure estão disponíveis para toque de rede virtual?
A visualização de toque de rede virtual está disponível em todas as regiões do Azure. As interfaces de rede monitoradas, o recurso toque de rede virtual, e a solução coletor ou análise devem ser implantados na mesma região.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>O toque da rede virtual dá suporte a qualquer recurso de filtragem nos pacotes espelhados?
Os recursos de filtragem não têm suporte com a visualização de toque da rede virtual. Quando uma configuração TAP é adicionada a um adaptador de rede, uma cópia profunda de todo o tráfego de entrada e saída na interface de rede é transmitida para o destino TAP.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>Várias configurações de toque podem ser adicionadas a uma interface de rede monitorada?
Uma interface de rede monitorada pode ter apenas uma configuração TAP. Consulte a solução de [parceiro](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) individual para obter a capacidade de transmitir várias cópias do tráfego de toque para as ferramentas de análise de sua escolha.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>A mesma rede virtual pode tocar no tráfego agregado de recursos de interfaces de rede monitoradas em mais de uma rede virtual?
Sim. O mesmo recurso de toque de rede virtual pode ser usado para agregar tráfego espelhado de interfaces de rede monitoradas em redes virtuais emparelhadas na mesma assinatura ou em uma assinatura diferente. O recurso tocar na rede virtual e o balanceador de carga de destino ou a interface de rede de destino devem estar na mesma assinatura. Todas as assinaturas devem estar no mesmo locatário Azure Active Directory.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>Há considerações de desempenho sobre o tráfego de produção se eu habilitar uma configuração de toque de rede virtual em uma interface de rede?

O toque da rede virtual está em versão prévia. Durante a visualização, não há nenhum contrato de nível de serviço. A funcionalidade não deve ser usada para cargas de trabalho de produção. Quando uma interface de rede de máquina virtual é habilitada com uma configuração TAP, os mesmos recursos no host do Azure alocados para a máquina virtual para enviar o tráfego de produção são usados para executar a função de espelhamento e enviar os pacotes espelhados. Selecione o tamanho correto de máquina virtual [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para garantir que recursos suficientes estejam disponíveis para a máquina virtual enviar o tráfego de produção e o tráfego espelhado.

### <a name="is-accelerated-networking-for-linuxcreate-vm-accelerated-networking-climd-or-windowscreate-vm-accelerated-networking-powershellmd-supported-with-virtual-network-tap"></a>Há suporte para a rede acelerada para [Linux](create-vm-accelerated-networking-cli.md) ou [Windows](create-vm-accelerated-networking-powershell.md) com toque de rede virtual?

Você poderá adicionar uma configuração de toque em um adaptador de rede anexado a uma máquina virtual que está habilitada com rede acelerada. Mas o desempenho e a latência na máquina virtual serão afetados pela adição da configuração TAP, já que o descarregamento para o tráfego de espelhamento não tem suporte atualmente pela rede acelerada do Azure.

## <a name="virtual-network-service-endpoints"></a>Pontos finais de serviço de rede virtual

### <a name="what-is-the-right-sequence-of-operations-to-set-up-service-endpoints-to-an-azure-service"></a>Qual é a sequência certa de operações para configurar pontos de extremidade de serviço para um serviço do Azure?
Há duas etapas para proteger um recurso de serviço do Azure por meio de pontos de extremidade de serviço:
1. Ative os pontos de extremidade de serviço para o serviço do Azure.
2. Configure ACLs de VNet no serviço do Azure.

A primeira etapa é uma operação no lado da rede e a segunda etapa é uma operação do lado do recurso de serviço. Ambas as etapas podem ser executadas pelo mesmo administrador ou por administradores diferentes com base nas permissões de RBAC concedidas à função de administrador. Recomendamos que você ative primeiro os pontos de extremidade de serviço para sua rede virtual antes de configurar ACLs de VNet no lado do serviço do Azure. Portanto, as etapas devem ser executadas na sequência listada acima para configurar pontos de extremidade de serviço de VNet.

>[!NOTE]
> Ambas as operações descritas acima devem ser concluídas antes que você possa limitar o acesso do serviço do Azure à VNet e à sub-rede permitidas. A ativação somente dos pontos de extremidade de serviço do serviço do Azure no lado da rede não fornece o acesso limitado. Além disso, você também deve configurar ACLs de VNet no lado do serviço do Azure.

Determinados serviços (como SQL e CosmosDB) permitem exceções à sequência acima por meio do sinalizador **IgnoreMissingVnetServiceEndpoint** . Depois que o sinalizador é definido como **true**, as ACLs de VNet podem ser definidas no lado do serviço do Azure antes de configurar os pontos de extremidade de serviço no lado da rede. Os serviços do Azure fornecem esse sinalizador para ajudar os clientes nos casos em que os firewalls IP específicos são configurados nos serviços do Azure e a ativação dos pontos de extremidade de serviço no lado da rede pode levar a uma queda de conectividade, já que o IP de origem muda de um endereço IPv4 público para um endereço privado. Configurar ACLs de VNet no lado do serviço do Azure antes de definir pontos de extremidade de serviço no lado da rede pode ajudar a evitar uma queda de conectividade.

### <a name="do-all-azure-services-reside-in-the-azure-virtual-network-provided-by-the-customer-how-does-vnet-service-endpoint-work-with-azure-services"></a>Todos os serviços do Azure residem na rede virtual do Azure fornecida pelo cliente? Como o ponto de extremidade do serviço de VNet funciona com os serviços do Azure?

Não, nem todos os serviços do Azure residem na rede virtual do cliente. A maioria dos serviços de dados do Azure, como o armazenamento do Azure, o SQL do Azure e o Azure Cosmos DB, são serviços de vários locatários que podem ser acessados por endereços IP públicos. Você pode saber mais sobre a integração de rede virtual para os serviços do Azure [aqui](virtual-network-for-azure-services.md). 

Quando você usa o recurso de pontos de extremidade do serviço de VNet (ativando o ponto de extremidades do serviço VNet no lado da rede e configurando ACLs de VNet apropriadas no lado do serviço do Azure), o acesso a um serviço do Azure é restrito de uma VNet e sub-rede permitidas.

### <a name="how-does-vnet-service-endpoint-provide-security"></a>Como o ponto de extremidade do serviço de VNet fornece segurança?

O recurso de ponto de extremidade do serviço VNet (ativar o ponto de extremidade do serviço VNet no lado da rede e configurar ACLs de VNet apropriadas no lado do serviço do Azure) limita o acesso do serviço do Azure à VNet e à sub-rede permitidas, fornecendo, assim, segurança no nível da rede e isolamento do tráfego do serviço do Azure. Todo o tráfego usando pontos de extremidade de serviço de VNet flui sobre o backbone da Microsoft, fornecendo assim outra camada de isolamento da Internet pública. Além disso, os clientes podem optar por remover totalmente o acesso público à Internet para os recursos de serviço do Azure e permitir o tráfego somente de sua rede virtual por meio de uma combinação de firewall IP e ACLs de VNet, protegendo assim os recursos de serviço do Azure de não autorizados às.      

### <a name="what-does-the-vnet-service-endpoint-protect---vnet-resources-or-azure-service"></a>O que o ponto de extremidade do serviço de VNet protege-os recursos VNet ou o serviço do Azure?
Os pontos de extremidade do serviço de VNet ajudam a proteger os recursos de serviço do Azure. Os recursos de VNet são protegidos por meio de NSGs (grupos de segurança de rede).

### <a name="is-there-any-cost-for-using-vnet-service-endpoints"></a>Há algum custo para usar pontos de extremidade de serviço de VNet?

Não, não há nenhum custo adicional para usar pontos de extremidade de serviço de VNet.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-subscriptions"></a>Posso ativar os pontos de extremidade de serviço de VNet e configurar ACLs de VNet se a rede virtual e os recursos de serviço do Azure pertencerem a assinaturas diferentes?

Sim, é possível. As redes virtuais e os recursos de serviço do Azure podem estar na mesma assinatura ou em assinaturas diferentes. O único requisito é que tanto a rede virtual quanto os recursos de serviço do Azure devem estar no mesmo locatário de Active Directory (AD).

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-ad-tenants"></a>Posso ativar os pontos de extremidade de serviço de VNet e configurar ACLs de VNet se a rede virtual e os recursos de serviço do Azure pertencerem a locatários diferentes do AD?
Não, os pontos de extremidade do serviço VNet e as ACLs de VNet não têm suporte em locatários do AD.

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-expressroute-gateway-access-azure-paas-service-over-vnet-service-endpoints"></a>É possível um endereço IP do dispositivo local conectado por meio do gateway de rede virtual do Azure (VPN) ou do gateway do ExpressRoute acessar o serviço de PaaS do Azure por meio de pontos de extremidade de serviço de VNet?
Por predefinição, os recursos de serviço do Azure obtidos para redes virtuais não são acessíveis a partir de redes no local. Se você quiser permitir o tráfego local, também deverá permitir endereços IP públicos (normalmente, NAT) do seu local ou do ExpressRoute. Esses endereços IP podem ser adicionados por meio da configuração de firewall IP para os recursos de serviço do Azure.

### <a name="can-i-use-vnet-service-endpoint-feature-to-secure-azure-service-to-multiple-subnets-within-a-virtual-network-or-across-multiple-virtual-networks"></a>Posso usar o recurso de ponto de extremidade de serviço de VNet para proteger o serviço do Azure em várias sub-redes em uma rede virtual ou em várias redes virtuais?
Para proteger os serviços do Azure para várias sub-redes em uma rede virtual ou entre várias redes virtuais, habilite os pontos de extremidade de serviço no lado da rede em cada uma das sub-redes de forma independente e, em seguida, proteja os recursos de serviço do Azure para todas as sub-redes Configurando ACLs de VNet apropriadas no lado do serviço do Azure.
 
### <a name="how-can-i-filter-outbound-traffic-from-a-virtual-network-to-azure-services-and-still-use-service-endpoints"></a>Como posso filtrar o tráfego de saída de uma rede virtual para os serviços do Azure e ainda usar pontos de extremidade de serviço?
Se você quiser inspecionar ou filtrar o tráfego destinado a um serviço do Azure de uma rede virtual, poderá implantar um dispositivo de rede virtual na rede virtual. Em seguida, você pode aplicar pontos de extremidade de serviço à sub-rede em que a solução de virtualização de rede está implantada e proteger os recursos de serviço do Azure somente para essa sub-rede por meio de ACLs de VNet. Esse cenário também pode ser útil se você quiser restringir o acesso de serviço do Azure de sua rede virtual somente para recursos específicos do Azure usando a filtragem de dispositivo de virtualização de rede. Para obter mais informações, veja [saída com aplicações de rede virtual](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).

### <a name="what-happens-when-you-access-an-azure-service-account-that-has-a-virtual-network-access-control-list-acl-enabled-from-outside-the-vnet"></a>O que acontece quando você acessa uma conta de serviço do Azure que tem uma ACL (lista de controle de acesso) de rede virtual habilitada de fora da VNet?
O erro HTTP 403 ou HTTP 404 é retornado.

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-service-account-in-another-region"></a>As sub-redes de uma rede virtual criada em regiões diferentes têm permissão para acessar uma conta de serviço do Azure em outra região? 
Sim, para a maioria dos serviços do Azure, as redes virtuais criadas em regiões diferentes podem acessar os serviços do Azure em outra região por meio dos pontos de extremidade do serviço de VNet. Por exemplo, se uma conta de Azure Cosmos DB estiver no oeste dos EUA ou leste dos EUA e as redes virtuais estiverem em várias regiões, a rede virtual poderá acessar Azure Cosmos DB. O armazenamento e o SQL são exceções e são regionais por natureza e a rede virtual e o serviço do Azure precisam estar na mesma região.
  
### <a name="can-an-azure-service-have-both-a-vnet-acl-and-an-ip-firewall"></a>Um serviço do Azure pode ter uma ACL de VNet e um firewall de IP?
Sim, uma ACL de VNet e um firewall de IP podem coexistir. Os dois recursos complementam uns aos outros para garantir o isolamento e a segurança.
 
### <a name="what-happens-if-you-delete-a-virtual-network-or-subnet-that-has-service-endpoint-turned-on-for-azure-service"></a>O que acontecerá se você excluir uma rede virtual ou sub-rede com ponto de extremidade de serviço ativado para o serviço do Azure?
A exclusão de VNets e sub-redes são operações independentes e têm suporte mesmo quando os pontos de extremidade de serviço são ativados para os serviços do Azure. Nos casos em que os serviços do Azure têm ACLs de VNet configuradas, para essas VNets e sub-redes, as informações de ACL de VNet associadas a esse serviço do Azure são desabilitadas quando uma VNet ou sub-rede com o ponto de extremidade de serviço VNet ativado é excluída.
 
### <a name="what-happens-if-an-azure-service-account-that-has-a-vnet-service-endpoint-enabled-is-deleted"></a>O que acontece se uma conta de serviço do Azure que tem um ponto de extremidade de serviço de VNet habilitado for excluída?
A exclusão de uma conta de serviço do Azure é uma operação independente e tem suporte mesmo quando o ponto de extremidade de serviço está habilitado no lado da rede e as ACLs de VNet são configuradas no lado do serviço do Azure. 

### <a name="what-happens-to-the-source-ip-address-of-a-resource-like-a-vm-in-a-subnet-that-has-vnet-service-endpoint-enabled"></a>O que acontece com o endereço IP de origem de um recurso (como uma VM em uma sub-rede) que tem um ponto de extremidade de serviço de VNet habilitado?
Quando os pontos de extremidade de serviço de rede virtual estão habilitados, os endereços IP de origem dos recursos na sub-rede da sua rede virtual alternam do uso de endereços IPV4 públicos para os endereços IP privados da rede virtual do Azure para o tráfego para o serviço do Azure. Observe que isso pode fazer com que os firewalls IP específicos definidos como um endereço IPV4 público anteriormente nos serviços do Azure falhem. 

### <a name="does-the-service-endpoint-route-always-take-precedence"></a>A rota de ponto de extremidade de serviço sempre tem precedência?
Os pontos de extremidade de serviço adicionam uma rota do sistema que tem precedência sobre rotas BGP e fornece roteamento ideal para o tráfego do ponto de extremidade de serviço. Os pontos de extremidade de serviço sempre tomam o tráfego de serviço diretamente de sua rede virtual para o serviço na rede de backbone Microsoft Azure. Para obter mais informações sobre como o Azure seleciona uma rota, consulte [Roteamento de tráfego de rede virtual do Azure](virtual-networks-udr-overview.md).
 
### <a name="how-does-nsg-on-a-subnet-work-with-service-endpoints"></a>Como o NSG em uma sub-rede funciona com pontos de extremidade de serviço?
Para acessar o serviço do Azure, o NSGs precisa permitir a conectividade de saída. Se seus NSGs forem abertos para todo o tráfego de saída da Internet, o tráfego do ponto de extremidade de serviço deverá funcionar. Você também pode limitar o tráfego de saída para IPs de serviço usando apenas as marcas de serviço.  
 
### <a name="what-permissions-do-i-need-to-set-up-service-endpoints"></a>Quais permissões preciso para configurar os pontos de extremidade de serviço?
Os pontos de extremidade de serviço podem ser configurados em uma rede virtual independentemente de um usuário com acesso de gravação à rede virtual. Para proteger os recursos de serviço do Azure para uma VNet, o usuário deve ter permissão **Microsoft. Network/virtualNetworks/sub-redes/joinViaServiceEndpoint/Action** para as sub-redes que estão sendo adicionadas. Essa permissão é incluída na função de administrador de serviços interna por padrão e pode ser modificada com a criação de funções personalizadas. Saiba mais sobre funções internas e atribuindo permissões específicas a [funções personalizadas](https://docs.microsoft.com/azure/role-based-access-control/custom-roles?toc=%2fazure%2fvirtual-network%2ftoc.json).
 

### <a name="can-i-filter-virtual-network-traffic-to-azure-services-allowing-only-specific-azure-service-resources-over-vnet-service-endpoints"></a>Posso filtrar o tráfego de rede virtual para os serviços do Azure, permitindo apenas recursos específicos de serviço do Azure, por meio de pontos de extremidade de serviço de VNet? 

As políticas de ponto de extremidade de serviço de rede virtual (VNet) permitem filtrar o tráfego de rede virtual para os serviços do Azure, permitindo apenas recursos de serviço do Azure específicos nos pontos de extremidade de serviço. As políticas de ponto de extremidade fornecem controle de acesso granular do tráfego de rede virtual para os serviços do Azure. Você pode saber mais sobre as políticas de ponto de extremidade de serviço [aqui](virtual-network-service-endpoint-policies-overview.md).

### <a name="does-azure-active-directory-azure-ad-support-vnet-service-endpoints"></a>O Azure Active Directory (Azure AD) dá suporte a pontos de extremidade de serviço de VNet?

O Azure Active Directory (AD do Azure) não dá suporte a pontos de extremidade de serviço nativamente. A lista completa de serviços do Azure que dão suporte a pontos de extremidade de serviço VNet pode ser vista [aqui](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). Observe que a marca "Microsoft. AzureActiveDirectory" listada em serviços que dão suporte a pontos de extremidade de serviço é usada para dar suporte a pontos de extremidade de serviço para o ADLS Gen 1. Para o ADLS Gen 1, a integração de rede virtual para Azure Data Lake Storage Gen1 usa a segurança do ponto de extremidade do serviço de rede virtual entre sua rede virtual e Azure Active Directory (AD do Azure) para gerar declarações de segurança adicionais no token de acesso. Essas afirmações são, posteriormente, utilizadas para autenticar a rede virtual na conta do Data Lake Storage Gen1 e permitir o acesso. Saiba mais sobre a [integração de VNet do Azure data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="are-there-any-limits-on-how-many-vnet-service-endpoints-i-can-set-up-from-my-vnet"></a>Há algum limite de quantos pontos de extremidade de serviço de VNet posso configurar de minha VNet?
Não há limite para o número total de pontos de extremidade de serviço de VNet em uma rede virtual. Para um recurso de serviço do Azure (como uma conta de armazenamento do Azure), os serviços podem impor limites no número de sub-redes usadas para proteger o recurso. A tabela a seguir mostra alguns limites de exemplo: 

|||
|---|---|
|Serviço do Azure| Limites nas regras de VNet|
|Storage do Azure| 100|
|SQL do Azure| 128|
|Azure SQL Data Warehouse|  128|
|Cofre de chaves do Azure|    127|
|Azure Cosmos DB|   64|
|Hub de Eventos do Azure|   128|
|Service Bus do Azure| 128|
|Azure Data Lake Store v1|  100|
 
>[!NOTE]
> Os limites estão sujeitos a alterações a critério do serviço do Azure. Consulte a documentação do respectivo serviço para obter detalhes dos serviços. 




  



