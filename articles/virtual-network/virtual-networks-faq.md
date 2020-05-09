---
title: FaQ da Rede Virtual Azure
titlesuffix: Azure Virtual Network
description: Respostas às perguntas mais frequentes sobre as redes virtuais do Microsoft Azure.
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
ms.openlocfilehash: d59a2fe32742c2d1d50b9ed33ccace5d377c59c2
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791991"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Perguntas mais frequentes (FAQ) da Rede Virtual do Azure

## <a name="virtual-network-basics"></a>Básicos da Rede Virtual

### <a name="what-is-an-azure-virtual-network-vnet"></a>O que é uma Rede Virtual Azure (VNet)?
Uma Rede Virtual Azure (VNet) é uma representação da sua própria rede na nuvem. É um isolamento lógico da nuvem do Azure dedicado à sua subscrição. Pode utilizar VNets para fornecer e gerir redes privadas virtuais (VPNs) em Azure e, opcionalmente, ligar os VNets a outros VNets em Azure, ou com a sua infraestrutura de TI no local para criar soluções híbridas ou transversais. Cada VNet que cria tem o seu próprio bloco CIDR e pode estar ligado a outras redes VNets e no local, desde que os blocos CIDR não se sobreponham. Também tem o controlo das definições do servidor DNS para VNets e segmentação do VNet em subredes.

Utilize VNets para:

* Crie um VNet privado dedicado apenas à nuvem. Às vezes não é preciso uma configuração transversal para a sua solução. Quando cria um VNet, os seus serviços e VMs dentro do seu VNet podem comunicar-se direta e seguramente uns com os outros na nuvem. Ainda pode configurar ligações de ponto final para os VMs e serviços que requerem comunicação na Internet, como parte da sua solução.

* Estenda de forma segura o seu centro de dados. Com VNets, pode construir VPNs tradicionais site-to-site (S2S) para escalar de forma segura a sua capacidade de datacenter. As VPNs S2S utilizam o IPSEC para fornecer uma ligação segura entre o seu gateway VPN corporativo e o Azure.

* Ativar cenários de nuvem híbrida. Os VNets dão-lhe a flexibilidade para suportar uma série de cenários de nuvem híbrida. Pode ligar as aplicações baseadas na nuvem de forma segura a qualquer tipo de sistema no local, como os mainframes e os sistemas Unix.

### <a name="how-do-i-get-started"></a>Como posso começar?
Visite a [documentação](https://docs.microsoft.com/azure/virtual-network/) da rede Virtual para começar. Este conteúdo fornece informações de visão geral e implementação para todas as funcionalidades vNet.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Posso usar VNets sem conectividade transversal?
Sim. Pode utilizar um VNet sem ligá-lo às suas instalações. Por exemplo, pode executar controladores de domínio do Microsoft Windows Server Ative Directory e fazendas SharePoint apenas num Azure VNet.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Posso realizar a otimização WAN entre VNets ou um VNet e o meu centro de dados no local?
Sim. Pode implantar um aparelho virtual da [rede de otimização WAN](https://azuremarketplace.microsoft.com/en-us/marketplace/?term=wan%20optimization) de vários fornecedores através do Azure Marketplace.

## <a name="configuration"></a>Configuração

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Que ferramentas uso para criar um VNet?
Pode utilizar as seguintes ferramentas para criar ou configurar um VNet:

* Portal do Azure
* PowerShell
* CLI do Azure
* Um ficheiro de configuração de rede (netcfg - apenas para VNets clássicos). Consulte o [Configure a VNet utilizando um](virtual-networks-using-network-configuration-file.md) artigo de ficheiro de configuração de rede.

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Que intervalos de endereços posso usar nos meus VNets?
Qualquer gama de endereços IP definida no [RFC 1918](https://tools.ietf.org/html/rfc1918). Por exemplo, 10.0.0.0/16. Não é possível adicionar as seguintes gamas de endereços:
* 224.0.0.0.0/4 (Multicast)
* 255.255.255.255/32 (Transmissão)
* 127.0.0.0.0/8 (Loopback)
* 169.254.0.0.0/16 (Link-local)
* 168.63.129.16/32 (DNS Interno)

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Posso ter endereços IP públicos nos meus VNets?
Sim. Para mais informações sobre as gamas públicas de endereços IP, consulte [Criar uma rede virtual](manage-virtual-network.md#create-a-virtual-network). Os endereços IP públicos não são acessíveis diretamente a partir da internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Há um limite para o número de subredes no meu VNet?
Sim. Consulte [os limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) para mais detalhes. Os espaços de endereço subnet não se sobrepõem uns aos outros.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Existem restrições à utilização de endereços IP dentro destas subredes?
Sim. O Azure reserva cinco endereços IP em cada sub-rede. Estes são x.x.x.x.x.3 e o último endereço da sub-rede. x.x.x.1-x.x.3 é reservado em cada subnet para serviços Azure.   
- x.x.x.0: Endereço de rede
- x.x.x.1: Reservado pelo Azure para o gateway predefinido
- x.x.x.2, x.x.x.3: Reservado pelo Azure para mapear os IPs DNS Azure para o espaço VNet
- x.x.x.255: Endereço de transmissão de rede

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Quão pequenos e grandes podem ser vNets e subredes?
A sub-rede IPv4 suportada mais pequena é /29, e a maior é /8 (utilizando definições de sub-rede CIDR).  As subredes IPv6 devem ter exatamente /64 de tamanho.  

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Posso levar os meus VLANs para Azure usando VNets?
Não. VNets são sobreposições da Camada 3. O Azure não suporta nenhuma semântica da Camada 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Posso especificar políticas personalizadas de encaminhamento nos meus VNets e subnets?
Sim. Pode criar uma mesa de rota e associá-la a uma subnet. Para mais informações sobre o encaminhamento em Azure, consulte a [visão geral do Routing.](virtual-networks-udr-overview.md#custom-routes)

### <a name="do-vnets-support-multicast-or-broadcast"></a>Os VNets suportam multicast ou transmissão?
Não. Não são suportados multicast e transmissão.

### <a name="what-protocols-can-i-use-within-vnets"></a>Que protocolos posso usar dentro dos VNets?
Pode utilizar protocolos TCP, UDP e ICMP TCP/IP dentro dos VNets. A Unicast é suportada dentro de VNets, com exceção do Protocolo de Configuração de Hospedeiro Dinâmico (DHCP) via Unicast (porta de origem UDP/68 / porta de destino UDP/67) e da porta de origem da UDP 65330, reservada ao hospedeiro. Pacotes encapsulados de ip-in-IP e pacotes genéricos de encapsulamento encapsulamento (GRE) estão bloqueados dentro de VNets. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Posso ver os meus routers padrão dentro de um VNet?
Não.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Posso usar traços para diagnosticar conectividade?
Não.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Posso adicionar subredes depois da criação do VNet?
Sim. As subredes podem ser adicionadas aos VNets a qualquer momento, desde que a gama de endereços de sub-rede não faça parte de outra subrede e exista espaço disponível na gama de endereços da rede virtual.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Posso modificar o tamanho da minha subnet depois de criá-la?
Sim. Pode adicionar, remover, expandir ou encolher uma sub-rede se não houver VMs ou serviços implementados na mesma.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Posso modificar as subredes depois de as ter criado?
Sim. Pode adicionar, remover e modificar os blocos CIDR utilizados por um VNet.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Se estou a gerir os meus serviços num VNet, posso ligar-me à internet?
Sim. Todos os serviços implantados dentro de um VNet podem ligar a saída à internet. Para saber mais sobre ligações à internet de saída em Azure, consulte [as ligações Outbound](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se pretender ligar a entrada a um recurso implantado através do Gestor de Recursos, o recurso deve ter um endereço IP público atribuído ao mesmo. Para saber mais sobre endereços IP públicos, consulte [endereços IP públicos](virtual-network-public-ip-address.md). Todos os Serviços Azure Cloud implantados em Azure têm um VIP publicamente endereçado a ele. Define pontos finais de entrada para funções PaaS e pontos finais para máquinas virtuais para permitir que estes serviços aceitem ligações a partir da internet.

### <a name="do-vnets-support-ipv6"></a>Os VNets suportam o IPv6?
Sim, os VNets podem ser apenas iPv4 ou dual stack (IPv4+IPv6).  Para mais detalhes, consulte a [visão geral do IPv6 para redes virtuais Azure](./ipv6-overview.md).

### <a name="can-a-vnet-span-regions"></a>Um VNet pode abranger regiões?
Não. Um VNet está limitado a uma única região. Uma rede virtual, no entanto, abrange zonas de disponibilidade. Para saber mais sobre as zonas de disponibilidade, veja [Descrição geral das zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pode ligar redes virtuais em diferentes regiões com o peering de rede virtual. Para mais detalhes, consulte a visão geral da [rede virtual](virtual-network-peering-overview.md)

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Posso ligar um VNet a outro VNet em Azure?
Sim. Pode ligar um VNet a outro VNet utilizando qualquer um deles:
- **Peering de rede virtual**: Para mais detalhes, consulte [a visão geral do VNet](virtual-network-peering-overview.md)
- **Um Gateway Azure VPN**: Para mais detalhes, consulte [Configurar uma ligação VNet-to-VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Resolução de Nomes (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Quais são as minhas opções de DNS para VNets?
Utilize a tabela de decisão sobre a resolução de [nomes para VMs e página De Exemplos](virtual-networks-name-resolution-for-vms-and-role-instances.md) para guiá-lo através de todas as opções de DNS disponíveis.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Posso especificar servidores DNS para um VNet?
Sim. Pode especificar os endereços IP do servidor DNS nas definições vNet. A definição é aplicada como o servidor(s) dNS padrão para todos os VMs no VNet.

### <a name="how-many-dns-servers-can-i-specify"></a>Quantos servidores DNS posso especificar?
Limites de referência [Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Posso modificar os meus servidores DNS depois de ter criado a rede?
Sim. Pode alterar a lista de servidores DNS para o seu VNet a qualquer momento. Se alterar a sua lista de servidores DNS, necessita de realizar uma renovação de locação DHCP em todos os VMs afetados no VNet, para que as novas definições de DNS entrem em vigor. Para vMs que executam o `ipconfig /renew` Sistema Operativo Windows, pode fazê-lo digitando diretamente no VM. Para outros tipos de S, consulte a documentação de renovação de contrato de arrendamento DHCP para o tipo de Os específico. 

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>O que é DNS fornecido pelo Azure e funciona com VNets?
O DNS fornecido pelo Azure é um serviço DNS multi-inquilino oferecido pela Microsoft. O Azure regista todos os seus VMs e exemplos de serviço na nuvem neste serviço. Este serviço fornece resolução de nome de anfitrião para VMs e instâncias de papéis contidas no mesmo serviço na nuvem, e por FQDN para VMs e exemplos de papéis no mesmo VNet. Para saber mais sobre o DNS, consulte a [Resolução de Nomes para VMs e casos](virtual-networks-name-resolution-for-vms-and-role-instances.md)de papel de Serviços cloud .

Existe uma limitação aos primeiros 100 serviços de nuvem numa VNet para resolução de nomes de inquilinos cruzados usando DNS fornecido pelo Azure. Se estiver a utilizar o seu próprio servidor DNS, esta limitação não se aplica.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Posso anular as minhas definições de DNS numa base de serviço por VM ou cloud?
Sim. Pode definir servidores DNS por VM ou serviço de nuvem para anular as definições de rede predefinidas. No entanto, recomenda-se que utilize DNS em toda a rede o máximo possível.

### <a name="can-i-bring-my-own-dns-suffix"></a>Posso trazer o meu próprio sufixo DNS?
Não. Não é possível especificar um sufixo DNS personalizado para os seus VNets.

## <a name="connecting-virtual-machines"></a>Ligação de máquinas virtuais

### <a name="can-i-deploy-vms-to-a-vnet"></a>Posso enviar VMs para um VNet?
Sim. Todas as interfaces de rede (NIC) anexadas a um VM implantado através do modelo de implementação do Gestor de Recursos devem ser ligadas a um VNet. Os VMs implantados através do modelo de implementação clássico podem ser opcionalmente ligados a um VNet.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Quais são os diferentes tipos de endereços IP que posso atribuir aos VMs?
* **Privado:** Atribuído a cada NIC dentro de cada VM. O endereço é atribuído utilizando o método estático ou dinâmico. Os endereços IP privados são atribuídos a partir do intervalo que especificou nas definições da sub-rede do seu VNet. Os recursos implantados através do modelo de implementação clássico são atribuídos endereços IP privados, mesmo que não estejam ligados a um VNet. O comportamento do método de atribuição é diferente dependendo se um recurso foi implantado com o Gestor de Recursos ou modelo de implantação clássico: 

  - **Gestor de Recursos**: Um endereço IP privado atribuído com o método dinâmico ou estático permanece atribuído a uma máquina virtual (Gestor de Recursos) até que o recurso seja eliminado. A diferença é que seleciona o endereço para atribuir quando se utiliza estática, e o Azure escolhe quando se utiliza dinâmico. 
  - **Clássico**: Um endereço IP privado atribuído com o método dinâmico pode mudar quando uma máquina virtual (clássica) VM é reiniciada depois de ter estado no estado de paragem (deallocated). Se precisar de garantir que o endereço IP privado para um recurso implantado através do modelo de implementação clássico nunca se altera, atribua um endereço IP privado com o método estático.

* **Público:** Opcionalmente atribuído a NICs ligados a VMs implantados através do modelo de implementação do Gestor de Recursos Azure. O endereço pode ser atribuído com o método de atribuição estático ou dinâmico. Todas as instâncias de papel vMs e Cloud Services implementadas através do modelo de implementação clássico existem dentro de um serviço na nuvem, que é atribuído um endereço IP virtual *dinâmico*(VIP). Um endereço IP *estático* público, chamado [endereço IP reservado,](virtual-networks-reserved-public-ip.md)pode opcionalmente ser atribuído como VIP. Pode atribuir endereços IP públicos a instâncias individuais de VMs ou Cloud Services implementadas através do modelo de implementação clássico. Estes endereços são chamados [IP públicos de nível de instância (endereços ILPIP](virtual-networks-instance-level-public-ip.md) e podem ser atribuídos dinamicamente.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Posso reservar um endereço IP privado para um VM que vou criar mais tarde?
Não. Não é possível reservar um endereço IP privado. Se estiver disponível um endereço IP privado, este é atribuído a um VM ou a uma instância de funções pelo servidor DHCP. O VM pode ou não ser aquele a quem deseja o endereço IP privado atribuído. Pode, no entanto, alterar o endereço IP privado de um VM já criado, para qualquer endereço IP privado disponível.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Os endereços IP privados mudam para VMs num VNet?
Depende. Se o VM foi implantado através do Gestor de Recursos, não, independentemente de o endereço IP ter sido atribuído com o método de atribuição estático ou dinâmico. Se o VM foi implantado através do modelo de implementação clássico, os endereços IP dinâmicos podem mudar quando um VM é iniciado depois de ter estado no estado de paragem (deallocated). O endereço é libertado de um VM implantado através de qualquer modelo de implementação quando o VM é eliminado.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Posso atribuir manualmente endereços IP aos NICs dentro do sistema operativo VM?
Sim, mas não é recomendado a menos que seja necessário, como quando atribuir vários endereços IP a uma máquina virtual. Para mais detalhes, consulte [Adicionar vários endereços IP a uma máquina virtual](virtual-network-multiple-ip-addresses-portal.md#os-config). Se o endereço IP atribuído a um Nic Azure ligado a alterações vm, e o endereço IP dentro do sistema operativo VM for diferente, perde conectividade com o VM.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Se eu parar uma ranhura de implantação do Serviço cloud ou desligar um VM dentro do sistema operativo, o que acontece com os meus endereços IP?
Nada. Os endereços IP (VIP público, público e privado) permanecem atribuídos à ranhura de implantação do serviço na nuvem ou VM.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Posso mover VMs de uma subnet para outra subnet a uma VNet sem reimplantar?
Sim. Pode encontrar mais informações sobre como [mover um VM ou exemplo de papel para um artigo de sub-rede diferente.](virtual-networks-move-vm-role-to-subnet.md)

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Posso configurar um endereço MAC estático para o meu VM?
Não. Um endereço MAC não pode ser configurado estáticamente.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>O endereço MAC permanecerá o mesmo para o meu VM uma vez criado?
Sim, o endereço MAC permanece o mesmo para um VM implantado através do Gestor de Recursos e modelos de implementação clássicos até que seja eliminado. Anteriormente, o endereço MAC foi lançado se o VM fosse interrompido (deallocated), mas agora o endereço MAC é mantido mesmo quando o VM está no estado de negócio. O endereço MAC permanece atribuído à interface de rede até que a interface de rede seja eliminada ou o endereço IP privado atribuído à configuração IP primária da interface de rede primária seja alterado. 

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Posso ligar-me à internet a partir de um VM num VNet?
Sim. Todas as instâncias de papel vMs e Cloud Services implementadas dentro de um VNet podem ligar-se à Internet.

## <a name="azure-services-that-connect-to-vnets"></a>Serviços Azure que ligam a VNets

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Posso usar aplicativos Web do Serviço de Aplicações Azure com um VNet?
Sim. Pode implementar Web Apps dentro de um VNet utilizando um ASE (App Service Environment), ligar o backend das suas aplicações aos seus VNets com a VNet Integration e bloquear o tráfego de entrada na sua app com pontos finais de serviço. Para obter mais informações, veja os artigos seguintes:

* [Funcionalidades de rede de serviços de aplicação](../app-service/networking-features.md)
* [Criar aplicações web num ambiente de serviço de aplicações](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Integrar uma aplicação Web com uma Rede Virtual do Azure](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Restrições de acesso ao Serviço de Aplicações](../app-service/app-service-ip-restrictions.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Posso implantar serviços de nuvem com funções web e trabalhadores (PaaS) num VNet?
Sim. Pode (opcionalmente) implementar exemplos de serviços de nuvem dentro de VNets. Para isso, especifice o nome VNet e os mapeamentos de role/subnet na secção de configuração de rede da configuração do seu serviço. Não precisa de atualizar nenhum dos seus binários.

### <a name="can-i-connect-a-virtual-machine-scale-set-to-a-vnet"></a>Posso ligar uma escala virtual de máquina a um VNet?
Sim. Deve ligar uma balança de máquinavirtual a um VNet.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Existe uma lista completa de serviços Azure que posso enviar recursos de um VNet?
Sim, para mais detalhes, consulte a [integração da rede Virtual para os serviços Azure.](virtual-network-for-azure-services.md)

### <a name="how-can-i-restrict-access-to-azure-paas-resources-from-a-vnet"></a>Como posso restringir o acesso aos recursos do Azure PaaS a partir de um VNet?

Os recursos utilizados através de alguns serviços Azure PaaS (como o Azure Storage e o Azure SQL Database), podem restringir o acesso à rede à VNet através da utilização de pontos finais de serviço de rede virtual ou de Link Privado Azure. Para mais detalhes, consulte a visão geral dos [pontos finais do serviço](virtual-network-service-endpoints-overview.md)de rede virtual, visão geral do [Link Privado do Azure](../private-link/private-link-overview.md)

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Posso mudar os meus serviços para dentro e para fora dos VNets?
Não. Não pode mover serviços dentro e fora dos VNets. Para mover um recurso para outro VNet, tem de eliminar e recolocar o recurso.

## <a name="security"></a>Segurança

### <a name="what-is-the-security-model-for-vnets"></a>Qual é o modelo de segurança para vNets?
Os VNets estão isolados uns dos outros, e outros serviços alojados na infraestrutura Azure. Um VNet é um limite de confiança.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Posso restringir o fluxo de tráfego de entrada ou saída para recursos ligados à VNet?
Sim. Pode aplicar [grupos](security-overview.md) de segurança de rede a subredes individuais dentro de um VNet, NICs ligados a uma VNet, ou ambos.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Posso implementar uma firewall entre os recursos ligados ao VNet?
Sim. Pode implantar um [aparelho virtual](https://azure.microsoft.com/marketplace/?term=firewall) de rede de firewall de vários fornecedores através do Mercado Azure.

### <a name="is-there-information-available-about-securing-vnets"></a>Há informações disponíveis sobre a segurança dos VNets?
Sim. Para mais detalhes, consulte a [visão geral da Segurança da Rede Azure.](../security/fundamentals/network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="apis-schemas-and-tools"></a>APIs, schemas e ferramentas

### <a name="can-i-manage-vnets-from-code"></a>Posso gerir os VNets a partir do código?
Sim. Pode utilizar APIs REST para VNets no [Azure Resource Manager](/rest/api/virtual-network) e modelos [clássicos](https://go.microsoft.com/fwlink/?LinkId=296833) de implementação.

### <a name="is-there-tooling-support-for-vnets"></a>Existe suporte para ferramentas para VNets?
Sim. Saiba mais sobre a utilização:
- O portal Azure para implantar VNets através do [Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) e modelos [clássicos](virtual-networks-create-vnet-classic-pportal.md) de implantação.
- PowerShell para gerir VNets implantados através do Gestor de [Recursos](/powershell/module/az.network) e modelos de implementação [clássicos.](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0)
- A interface de linha de comando Azure (CLI) para implantar e gerir VNets implantados através do Gestor de [Recursos](/cli/azure/network/vnet) e modelos de implantação [clássicos.](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-resources)  

## <a name="vnet-peering"></a>VNet peering

### <a name="what-is-vnet-peering"></a>O que é que a VNet está a espreitar?
O peering VNet (ou peering virtual da rede) permite-lhe ligar redes virtuais. Uma ligação de peering VNet entre redes virtuais permite-lhe encaminhar o tráfego entre elas em privado através de endereços IPv4. As máquinas virtuais nos VNets em pares podem comunicar entre si como se estivessem dentro da mesma rede. Estas redes virtuais podem estar na mesma região ou em diferentes regiões (também conhecidas como Global VNet Peering). As ligações de observação vNet também podem ser criadas através de subscrições do Azure.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>Posso criar uma ligação de pares a um VNet numa região diferente?
Sim. O peering Global VNet permite-lhe peer VNets em diferentes regiões. O peering global VNet está disponível em todas as regiões públicas do Azure, regiões de nuvem da China e regiões de nuvem do Governo. Não se pode, globalmente, espreitar das regiões públicas de Azure para as regiões nacionais de nuvens.

### <a name="what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers"></a>Quais são os constrangimentos relacionados com o Global VNet Peering e os Load Balancers?
Se as duas redes virtuais em duas regiões diferentes forem espreitadas sobre o Global VNet Peering, não é possível ligar-se a recursos que estão por trás de um Equilíbrio de Carga Básico através do IP front end do Balancer de Carga. Esta restrição não existe para um Balancer de Carga Padrão.
Os seguintes recursos podem utilizar os Equilíbrios básicos de carga, o que significa que não pode alcançá-los através do IP front end do Balancer de carga sobre o Global VNet Peering. No entanto, pode utilizar o global VNet a espreitar para chegar diretamente aos recursos através dos seus IPs VNet privados, se permitido. 
- VMs por trás de Equilíbrios básicos de carga
- Conjuntos de escala de máquina virtual com equilíbrios básicos de carga 
- Cache de Redis 
- Gateway de aplicação (v1) SKU
- Service Fabric
- SQL MI
- Gestão de API
- Serviço de Domínio de Diretório Ativo (ADDS)
- Aplicações Lógicas
- HDInsight
-   Azure Batch
- Ambiente do Serviço de Aplicações

Pode ligar-se a estes recursos através do ExpressRoute ou VNet-to-VNet através de Gateways VNet.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Posso permitir o VNet Peering se as minhas redes virtuais pertencerem a subscrições dentro de diferentes inquilinos do Azure Ative Directory?
Sim. É possível estabelecer o VNet Peering (local ou global) se as suas subscrições pertencerem a diferentes inquilinos do Azure Ative Directory. Pode fazê-lo através do PowerShell ou do CLI. Portal ainda não é apoiado.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>A minha ligação de observação vnet está em estado *iniciado,* por que não posso ligar?
Se a sua ligação de pares estiver num estado *iniciado,* isto significa que criou apenas uma ligação. Deve ser criada uma ligação bidirecional para estabelecer uma ligação bem sucedida. Por exemplo, para peer VNet A a VNet B, deve ser criado um link de VNetA para VNetB e de VNetB para VNetA. A criação de ambas as ligações mudará o estado para *Connected*.

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>A minha ligação de observação vnet está em estado *desligado,* por que não posso criar uma ligação de pares?
Se a sua ligação de observação VNet estiver num estado *desligado,* significa que uma das ligações criadas foi eliminada. Para restabelecer uma ligação de pares, terá de eliminar o link e recriá-lo.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>Posso espreitar o meu VNet com um VNet numa subscrição diferente?
Sim. Você pode peer VNets através de subscrições e em todas as regiões.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Posso espreitar dois VNets com intervalos de endereços correspondentes ou sobrepostos?
Não. Os espaços de endereço não devem sobrepor-se para permitir o Peering VNet.

### <a name="how-much-do-vnet-peering-links-cost"></a>Quanto custam as ligações vNet?
Não há nenhuma taxa para criar uma ligação vnet peering. A transferência de dados através de ligações de pares é cobrada. [Veja aqui.](https://azure.microsoft.com/pricing/details/virtual-network/)

### <a name="is-vnet-peering-traffic-encrypted"></a>A VNet está a analisar o tráfego encriptado?
Não. O tráfego entre recursos em VNets peered é privado e isolado. Permanece completamente na Espinha dorsal da Microsoft.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Porque é que a minha ligação está num estado *desligado?*
As ligações de observação vNet entram em estado *desligado* quando uma ligação de observação VNet é eliminada. Deve eliminar ambas as ligações para restabelecer uma ligação de observação bem sucedida.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>Se eu peer VNetA para VNetB e eu peer VNetB para VNetC, isso significa vNetA e VNetC são espreitados?
Não. O olhar transitório não é apoiado. É preciso que a VNetA e a VNetC ocorram.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Existem limitações de largura de banda para ligar?
Não. O vNet peering, quer seja local ou global, não impõe quaisquer restrições de largura de banda. A largura de banda é limitada apenas pelo VM ou pelo recurso computacional.

### <a name="how-can-i-troubleshoot-vnet-peering-issues"></a>Como posso resolver problemas com a VNet Peering?
Aqui está um guia de resolução de [problemas](https://support.microsoft.com/en-us/help/4486956/troubleshooter-for-virtual-network-peering-issues) que pode tentar.

## <a name="virtual-network-tap"></a>TAP de rede virtual

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>Quais as regiões azure disponíveis para a rede virtual TAP?
A pré-visualização da rede virtual TAP está disponível em todas as regiões do Azure. As interfaces de rede monitorizadas, o recurso TAP da rede virtual e a solução de coleção ou análise devem ser implementadas na mesma região.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>A Rede Virtual TAP suporta alguma capacidade de filtragem nos pacotes espelhados?
As capacidades de filtragem não são suportadas com a pré-visualização tap da rede virtual. Quando uma configuração TAP é adicionada a uma interface de rede, uma cópia profunda de todo o tráfego de entrada e saída na interface de rede é transmitida para o destino TAP.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>Podem ser adicionadas várias configurações TAP a uma interface de rede monitorizada?
Uma interface de rede monitorizada pode ter apenas uma configuração TAP. Consulte a [solução](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) individual do parceiro para obter a capacidade de transmitir várias cópias do tráfego da TAP para as ferramentas de análise à sua escolha.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>A mesma rede virtual TAP pode agregar o tráfego a partir de interfaces de rede monitorizadas em mais de uma rede virtual?
Sim. O mesmo recurso TAP da rede virtual pode ser utilizado para agregar tráfego espelhado a partir de interfaces de rede monitorizadas em redes virtuais com pariadas na mesma subscrição ou numa subscrição diferente. O recurso TAP da rede virtual e o equilibrador de carga de destino ou interface de rede de destino devem estar na mesma subscrição. Todas as subscrições devem estar sob o mesmo inquilino do Azure Ative Directory.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>Existem considerações de desempenho no tráfego de produção se eu permitir uma configuração TAP de rede virtual numa interface de rede?

A rede virtual TAP está em pré-visualização. Durante a pré-visualização, não existe um acordo de nível de serviço. A capacidade não deve ser utilizada para cargas de trabalho de produção. Quando uma interface de rede de máquinas virtuais é ativada com uma configuração TAP, os mesmos recursos no hospedeiro Azure atribuídos à máquina virtual para enviar o tráfego de produção são utilizados para executar a função de espelhamento e enviar os pacotes espelhados. Selecione o tamanho correto da máquina virtual [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para garantir que existem recursos suficientes para a máquina virtual enviar o tráfego de produção e o tráfego espelhado.

### <a name="is-accelerated-networking-for-linux-or-windows-supported-with-virtual-network-tap"></a>A rede acelerada para [O Linux](create-vm-accelerated-networking-cli.md) ou [Windows](create-vm-accelerated-networking-powershell.md) é suportada com a rede virtual TAP?

Poderá adicionar uma configuração TAP numa interface de rede ligada a uma máquina virtual que está ativada com uma rede acelerada. Mas o desempenho e a latência na máquina virtual serão afetados pela adição da configuração TAP, uma vez que o descarregamento para espelhamento do tráfego não é atualmente suportado pelo azure acelerado em rede.

## <a name="virtual-network-service-endpoints"></a>Pontos finais de serviço de rede virtual

### <a name="what-is-the-right-sequence-of-operations-to-set-up-service-endpoints-to-an-azure-service"></a>Qual é a sequência certa de operações para criar pontos finais de serviço para um serviço Azure?
Existem dois passos para garantir um recurso de serviço Azure através de pontos finais de serviço:
1. Ligue os pontos finais de serviço para o serviço Azure.
2. Instale acLs VNet no serviço Azure.

O primeiro passo é uma operação lateral da rede e o segundo passo é uma operação do lado do recurso de serviço. Ambas as etapas podem ser executadas pelo mesmo administrador ou por diferentes administradores com base nas permissões RBAC concedidas ao papel de administrador. Recomendamos que ligue primeiro os pontos finais do serviço para a sua rede virtual antes de configurar os VNet ACLs no lado do serviço Azure. Por conseguinte, os passos devem ser efetuados na sequência acima listada para configurar os pontos finais do serviço VNet.

>[!NOTE]
> Ambas as operações acima descritas devem ser concluídas antes de poder limitar o acesso ao serviço Azure à VNet e subnet permitidas. Só ligar os pontos finais de serviço para o serviço Azure do lado da rede não lhe dá o acesso limitado. Além disso, também deve configurar VNet ACLs no lado do serviço Azure.

Certos serviços (como o SQL e o CosmosDB) permitem exceções à sequência acima através da bandeira **IgnoreMissingVnetServiceEndpoint.** Uma vez definida a bandeira para **True,** os VNet ACLs podem ser definidos no lado do serviço Azure antes de configurar os pontos finais do serviço no lado da rede. Os serviços azure fornecem esta bandeira para ajudar os clientes nos casos em que as firewalls IP específicas estão configuradas nos serviços Do Azure e ligar os pontos finais de serviço do lado da rede pode levar a uma queda de conectividade uma vez que a fonte de IP muda de um endereço IPv4 público para um endereço privado. A instalação de VNet ACLs no lado do serviço Azure antes de definir pontos finais de serviço no lado da rede pode ajudar a evitar uma queda de conectividade.

### <a name="do-all-azure-services-reside-in-the-azure-virtual-network-provided-by-the-customer-how-does-vnet-service-endpoint-work-with-azure-services"></a>Todos os serviços Azure residem na rede virtual Azure fornecida pelo cliente? Como funciona o endpoint do serviço VNet com os serviços Azure?

Não, nem todos os serviços Azure residem na rede virtual do cliente. A maioria dos serviços de dados azure como Azure Storage, Azure SQL e Azure Cosmos DB, são serviços multi-inquilinos que podem ser acedidos em endereços IP públicos. Pode saber mais sobre integração de rede virtual para serviços Azure [aqui.](virtual-network-for-azure-services.md) 

Quando utiliza a função de pontos finais do serviço VNet (ligar o ponto final do serviço VNet no lado da rede e configurar OS ACLs vNet apropriados no lado do serviço Azure), o acesso a um serviço Azure é restringido a partir de uma VNet e subnet permitidas.

### <a name="how-does-vnet-service-endpoint-provide-security"></a>Como é que o ponto final do serviço VNet fornece segurança?

A função de ponto final do serviço VNet (ligar o ponto final do serviço VNet no lado da rede e criar ACLs adequados no lado do serviço Azure) limita o acesso ao serviço Azure à VNet e subnet permitidas, proporcionando assim uma segurança de nível de rede e isolamento do tráfego de serviço Saque. Todo o tráfego que utiliza pontos finais de serviço VNet flui sobre a espinha dorsal da Microsoft, proporcionando assim outra camada de isolamento da internet pública. Além disso, os clientes podem optar por remover totalmente o acesso público à Internet aos recursos de serviço do Azure e permitir o tráfego apenas a partir da sua rede virtual através de uma combinação de firewall IP e VNet ACLs, protegendo assim os recursos de serviço Azure de acesso não autorizado.      

### <a name="what-does-the-vnet-service-endpoint-protect---vnet-resources-or-azure-service"></a>O que protege o ponto final do serviço VNet - Recursos VNet ou serviço Azure?
Os pontos finais do serviço VNet ajudam a proteger os recursos de serviço do Azure. Os recursos VNet são protegidos através de Grupos de Segurança da Rede (NSGs).

### <a name="is-there-any-cost-for-using-vnet-service-endpoints"></a>Existe algum custo para a utilização de pontos finais do serviço VNet?

Não, não há custos adicionais para a utilização de pontos finais do serviço VNet.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-subscriptions"></a>Posso ligar os pontos finais do serviço VNet e criar AcLs VNet se a rede virtual e os recursos de serviço Azure pertencerem a diferentes subscrições?

Sim, é possível. Redes virtuais e recursos de serviço Azure podem estar nas mesmas subscrições ou diferentes. O único requisito é que tanto a rede virtual como os recursos de serviço Azure devem estar sob o mesmo inquilino de Diretório Ativo (AD).

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-ad-tenants"></a>Posso ligar os pontos finais do serviço VNet e criar AcLs VNet se a rede virtual e os recursos de serviço Azure pertencerem a diferentes inquilinos aD?
Não, os pontos finais do serviço VNet e os VNet ACLs não são suportados em todos os inquilinos da AD.

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-expressroute-gateway-access-azure-paas-service-over-vnet-service-endpoints"></a>O endereço IP de um dispositivo no local que está ligado através do gateway da Rede Virtual Azure (VPN) ou do gateway ExpressRoute pode aceder ao Serviço Azure PaaS sobre os pontos finais do serviço VNet?
Por predefinição, os recursos de serviço do Azure obtidos para redes virtuais não são acessíveis a partir de redes no local. Se pretender permitir o tráfego a partir do local, também deve permitir endereços IP públicos (tipicamente, NAT) a partir das suas instalações ou ExpressRoute. Estes endereços IP podem ser adicionados através da configuração de firewall IP para os recursos de serviço Azure.

### <a name="can-i-use-vnet-service-endpoint-feature-to-secure-azure-service-to-multiple-subnets-within-a-virtual-network-or-across-multiple-virtual-networks"></a>Posso usar a funcionalidade VNet Service Endpoint para garantir o serviço Azure a várias subredes dentro de uma rede virtual ou através de várias redes virtuais?
Para garantir os serviços azure a várias subredes dentro de uma rede virtual ou através de várias redes virtuais, ative pontos finais de serviço no lado da rede em cada uma das subredes de forma independente e, em seguida, garanta os recursos de serviço Azure a todas as subredes, criando ACLs VNet adequados no lado do serviço Azure.
 
### <a name="how-can-i-filter-outbound-traffic-from-a-virtual-network-to-azure-services-and-still-use-service-endpoints"></a>Como posso filtrar o tráfego de saída de uma rede virtual para os serviços Azure e ainda utilizar pontos finais de serviço?
Se pretender inspecionar ou filtrar o tráfego destinado a um serviço Azure a partir de uma rede virtual, pode implantar um aparelho virtual de rede dentro da rede virtual. Em seguida, pode aplicar pontos finais de serviço na subrede onde o aparelho virtual da rede é implantado e proteger os recursos de serviço Azure apenas a esta subrede através de VNet ACLs. Este cenário também pode ser útil se pretender restringir o acesso ao serviço Azure da sua rede virtual apenas a recursos específicos do Azure utilizando a filtragem de aparelhos virtuais da rede. Para obter mais informações, veja [saída com aplicações de rede virtual](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).

### <a name="what-happens-when-you-access-an-azure-service-account-that-has-a-virtual-network-access-control-list-acl-enabled-from-outside-the-vnet"></a>O que acontece quando se acede a uma conta de serviço Azure que tem uma lista virtual de controlo de acesso à rede (ACL) ativada a partir de fora do VNet?
O erro HTTP 403 ou HTTP 404 é devolvido.

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-service-account-in-another-region"></a>As subredes de uma rede virtual criada em diferentes regiões podem aceder a uma conta de serviço Azure noutra região? 
Sim, para a maioria dos serviços Azure, as redes virtuais criadas em diferentes regiões podem aceder aos serviços Azure noutra região através dos pontos finais do serviço VNet. Por exemplo, se uma conta Azure Cosmos DB estiver nos EUA Ocidental ou Leste dos EUA e as redes virtuais estiverem em várias regiões, a rede virtual pode aceder ao Azure Cosmos DB. O armazenamento e o SQL são exceções e são de natureza regional e tanto a rede virtual como o serviço Azure precisam de estar na mesma região.
  
### <a name="can-an-azure-service-have-both-a-vnet-acl-and-an-ip-firewall"></a>Um serviço Azure pode ter uma Acl VNet e uma firewall IP?
Sim, um VNet ACL e uma firewall IP podem coexistir. Ambas as funcionalidades complementam-se mutuamente para garantir o isolamento e a segurança.
 
### <a name="what-happens-if-you-delete-a-virtual-network-or-subnet-that-has-service-endpoint-turned-on-for-azure-service"></a>O que acontece se eliminar uma rede virtual ou uma subnet que tenha ponto final de serviço ligado para o serviço Azure?
A eliminação de VNets e subredes são operações independentes e são apoiadas mesmo quando os pontos finais do serviço são ligados para os serviços Azure. Nos casos em que os serviços Azure tenham VNet ACLs criados, para esses VNets e subredes, as informações VNet ACL associadas a esse serviço Azure são desativadas quando um VNet ou subnet que tenha ponto final de serviço VNet ligado é eliminado.
 
### <a name="what-happens-if-an-azure-service-account-that-has-a-vnet-service-endpoint-enabled-is-deleted"></a>O que acontece se uma conta de serviço Azure que tenha um ponto final do Serviço VNet ativado for eliminada?
A eliminação de uma conta de serviço Azure é uma operação independente e é suportada mesmo quando o ponto final do serviço está ativado no lado da rede e os ACLs VNet são configurados no lado do serviço Azure. 

### <a name="what-happens-to-the-source-ip-address-of-a-resource-like-a-vm-in-a-subnet-that-has-vnet-service-endpoint-enabled"></a>O que acontece ao endereço IP fonte de um recurso (como um VM numa subnet) que tem o ponto final do serviço VNet ativado?
Quando os pontos finais do serviço de rede virtual estão ativados, os endereços IP de origem dos recursos na subnet da sua rede virtual mudam de usar endereços IPV4 públicos para os endereços IP privados da rede virtual Azure para tráfego para o serviço Azure. Note que isto pode fazer com que firewalls IP específicos que são definidos para o endereço IPV4 público mais cedo nos serviços Azure falhem. 

### <a name="does-the-service-endpoint-route-always-take-precedence"></a>A rota de fim de serviço tem sempre precedência?
Os pontos finais do serviço adicionam uma rota do sistema que tem precedência sobre as rotas do BGP e proporciona um encaminhamento ótimo para o tráfego de pontos finais de serviço. Os pontos finais do serviço levam sempre o tráfego de serviço diretamente da sua rede virtual para o serviço na rede de espinha dorsal microsoft Azure. Para obter mais informações sobre como o Azure seleciona uma rota, consulte o [encaminhamento](virtual-networks-udr-overview.md)de tráfego da rede Azure Virtual .
 
### <a name="how-does-nsg-on-a-subnet-work-with-service-endpoints"></a>Como é que a NSG numa subnet funciona com pontos finais de serviço?
Para chegar ao serviço Azure, os NSGs precisam de permitir a conectividade de saída. Se os seus NSGs forem abertos a todo o tráfego de saída da Internet, então o tráfego de ponto final de serviço deve funcionar. Também pode limitar o tráfego de saída aos IPs de serviço apenas utilizando as etiquetas de Serviço.  
 
### <a name="what-permissions-do-i-need-to-set-up-service-endpoints"></a>Que permissões preciso para criar pontos finais de serviço?
Os pontos finais do serviço podem ser configurados numa rede virtual de forma independente por um utilizador com acesso por escrito à rede virtual. Para garantir os recursos de serviço do Azure a um VNet, o utilizador deve ter permissão **Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action** for the subnets being added. Esta permissão está incluída na função de administrador de serviço incorporada por padrão e pode ser modificada através da criação de funções personalizadas. Saiba mais sobre funções incorporadas e a atribuição de permissões específicas a [funções personalizadas](https://docs.microsoft.com/azure/role-based-access-control/custom-roles?toc=%2fazure%2fvirtual-network%2ftoc.json).
 

### <a name="can-i-filter-virtual-network-traffic-to-azure-services-allowing-only-specific-azure-service-resources-over-vnet-service-endpoints"></a>Posso filtrar o tráfego de rede virtual para os serviços Azure, permitindo apenas recursos específicos de serviço azul, sobre os pontos finais do serviço VNet? 

As políticas de fim do serviço de rede virtual (VNet) permitem filtrar o tráfego de rede virtual para os serviços Azure, permitindo apenas recursos de serviço saem específicos do Azure sobre os pontos finais do serviço. As políticas endpoint fornecem o controlo de acesso granular do tráfego de rede virtual para os serviços Azure. Pode saber mais sobre as políticas de ponto final de serviço [aqui](virtual-network-service-endpoint-policies-overview.md).

### <a name="does-azure-active-directory-azure-ad-support-vnet-service-endpoints"></a>O Azure Ative Directory (Azure AD) suporta pontos finais de serviço VNet?

O Azure Ative Directory (Azure AD) não suporta pontos finais de serviço de forma nativa. A lista completa dos Serviços Azure que suportam os pontos finais do serviço VNet pode ser vista [aqui](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). Note que a etiqueta "Microsoft.AzureActiveDirectory" listada em pontos finais de serviços de suporte é usada para suportar pontos finais de serviço para ADLS Gen 1. Para a ADLS Gen 1, a integração de rede virtual para o Azure Data Lake Storage Gen1 utiliza a segurança do serviço de rede virtual entre a sua rede virtual e o Azure Ative Directory (Azure AD) para gerar reclamações de segurança adicionais no token de acesso. Essas afirmações são, posteriormente, utilizadas para autenticar a rede virtual na conta do Data Lake Storage Gen1 e permitir o acesso. Saiba mais sobre [a Integração azure Data Lake Store Gen 1 VNet](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="are-there-any-limits-on-how-many-vnet-service-endpoints-i-can-set-up-from-my-vnet"></a>Há limites para quantos pontos finais de serviço VNet posso configurar a partir do meu VNet?
Não existe limite para o número total de pontos finais do serviço VNet numa rede virtual. Para um recurso de serviço Azure (como uma conta de Armazenamento Azure), os serviços podem impor limites ao número de subredes utilizadas para a segurança do recurso. A tabela que se segue apresenta alguns limites de exemplo: 

|||
|---|---|
|Serviço do Azure| Limites às regras vnet|
|Storage do Azure| 100|
|SQL do Azure| 128|
|Azure SQL Data Warehouse|  128|
|Azure KeyVault|    127|
|Azure Cosmos DB|   64|
|Hub de Eventos do Azure|   128|
|Service Bus do Azure| 128|
|Loja azure Data Lake V1|  100|
 
>[!NOTE]
> Os limites são sujeitos a alterações ao critério do serviço Azure. Consulte a respetiva documentação de serviço para obter detalhes dos serviços. 




  



