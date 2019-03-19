---
title: FAQ da rede Virtual do Azure
titlesuffix: Azure Virtual Network
description: Respostas às perguntas mais frequentes sobre as redes virtuais do Microsoft Azure.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2019
ms.author: jdial
ms.openlocfilehash: 19fdf2e7e1c7c56b6bfe8ddbf7329d3722f4e8de
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188616"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Rede Virtual do Azure perguntas mais frequentes (FAQ)

## <a name="virtual-network-basics"></a>Noções básicas de rede virtual

### <a name="what-is-an-azure-virtual-network-vnet"></a>O que é uma rede Virtual do Azure (VNet)?
Uma rede Virtual do Azure (VNet) é uma representação da sua própria rede na cloud. É um isolamento lógico da nuvem do Azure dedicado à sua subscrição. Pode utilizar VNets para aprovisionar e gerir redes privadas virtuais (VPNs) no Azure e, opcionalmente, ligar as VNets outras Vnet no Azure ou com o ambiente infraestrutura de TI para criar soluções híbridas ou em vários locais. Cada VNet que criou tem seu próprio bloco CIDR e pode ser associado a outras redes de VNets e no local, desde que os blocos CIDR não se sobrepõem. Também tem controlo das definições de servidor DNS para VNets e a segmentação da VNet em sub-redes.

Utilize VNets para:

* Crie um dedicado privada apenas na cloud VNet, às vezes, não exigem uma configuração em vários locais para a sua solução. Quando cria uma VNet, seus serviços e VMs dentro da VNet podem comunicar diretamente e segura entre si na cloud. Ainda é possível configurar ligações de ponto final para as VMs e serviços que requerem comunicação da Internet, como parte da sua solução.
* Expanda seu centro de dados com VNets de forma segura, é possível criar o site a site (S2S) VPNs tradicionais para dimensionar a capacidade do seu datacenter com segurança. VPNs de S2S utilize IPSEC para fornecer uma ligação segura entre o gateway de VPN empresarial e o Azure.
* Cenários de nuvem híbrida ativar VNets dão-lhe a flexibilidade para suportar uma variedade de cenários de nuvem híbrida. Pode ligar com segurança os aplicativos baseados na nuvem para qualquer tipo de sistema no local, como mainframes e sistemas Unix.

### <a name="how-do-i-get-started"></a>Como posso começar?
Visite o [documentação da rede Virtual](https://docs.microsoft.com/azure/virtual-network/) para começar a utilizar. Este conteúdo fornece informações de descrição geral e implementação para todos os recursos da VNet.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Pode utilizar VNets sem conectividade em vários locais?
Sim. Pode utilizar uma VNet sem o ligar a local. Por exemplo, pode executar controladores de domínio do Active Directory do Microsoft Windows Server e farms do SharePoint unicamente numa VNet do Azure.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Posso executar a otimização de WAN entre uma VNet ou VNets e o meu Datacenter no local?
Sim. Pode implementar um [aplicação virtual de rede de otimização de WAN](https://azure.microsoft.com/marketplace/?term=wan+optimization) de vários fornecedores através do Azure Marketplace.

## <a name="configuration"></a>Configuração

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Que ferramentas posso utilizar para criar uma VNet?
Pode utilizar as seguintes ferramentas para criar ou configurar uma VNet:

* Portal do Azure
* PowerShell
* CLI do Azure
* Um ficheiro de configuração de rede (netcfg - para VNets clássicas apenas). Consulte a [configurar uma VNet com um ficheiro de configuração de rede](virtual-networks-using-network-configuration-file.md) artigo.

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Os intervalos de endereços pode usar nas minhas VNets?
Qualquer intervalo de endereços IP definidos na [RFC 1918](https://tools.ietf.org/html/rfc1918). Por exemplo, 10.0.0.0/16. Não é possível adicionar os seguintes intervalos de endereços:
* 224.0.0.0/4 (Multicast)
* 255.255.255.255/32 (difusão)
* 127.0.0.0/8 (Loopback)
* 169.254.0.0/16 (Link-local)
* 168.63.129.16/32 (DNS interno)

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Pode ter endereços IP públicos nas minhas VNets?
Sim. Para obter mais informações sobre intervalos de endereços IP públicos, consulte [criar uma rede virtual](manage-virtual-network.md#create-a-virtual-network). Endereços IP públicos não são diretamente acessíveis a partir da internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Existe um limite para o número de sub-redes na minha VNet?
Sim. Ver [limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) para obter detalhes. Espaços de endereços de sub-rede não podem sobrepor um do outro.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Existem restrições sobre como utilizar endereços IP dentro destas sub-redes?
Sim. O Azure reserva alguns endereços IP em cada sub-rede. Os endereços IP do próprio e apelidos de cada sub-rede estão reservados para conformidade com o protocolo, juntamente com os endereços de x.x.x.1 x.x.x.3 de cada sub-rede, que são utilizadas para serviços do Azure.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Como pequenos e grandes como podem ser VNets e sub-redes?
A sub-rede menor suportada é /29, sendo o maior /8 (através de definições de sub-rede CIDR).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Posso usar meu VLANs para o Azure com VNets?
Não. As VNets estiverem sobreposições de camada 3. Azure não suporta qualquer semântica de camada 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Pode especificar políticas de encaminhamento personalizadas no meu VNets e sub-redes?
Sim. Pode criar uma tabela de rotas e associe-o a uma sub-rede. Para obter mais informações sobre o encaminhamento do Azure, consulte [descrição geral do encaminhamento](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>VNets suportarem multicast ou difusão?
Não. Multicast e de difusão não são suportadas.

### <a name="what-protocols-can-i-use-within-vnets"></a>Quais protocolos pode usar nas VNets?
Pode utilizar protocolos TCP, UDP e TCP/IP de ICMP nas VNets. Unicast é suportado nas VNets, com exceção de configuração protocolo DHCP (Dynamic Host) via Unicast (porta UDP/68 de origem / destino da porta UDP/67). Multicast, difusão, pacotes encapsulado IP no IP e pacotes de Generic Routing Encapsulation (GRE) são bloqueadas nas VNets. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Eu ping meu roteadores padrão dentro de uma VNet?
Não.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Pode utilizar tracert para diagnosticar a conectividade?
Não.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Posso adicionar sub-redes depois de criar a VNet?
Sim. Sub-redes podem ser adicionados às VNets em qualquer altura, desde que o intervalo de endereços de sub-rede não faz parte de outra sub-rede e há espaço disponível no intervalo de endereços da rede virtual.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Posso modificar o tamanho da minha sub-rede depois de criá-la?
Sim. Pode adicionar, remover, expandir ou reduzir uma sub-rede, se existirem não existem VMs ou serviços implementados no mesmo.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Posso modificar sub-redes depois que criei-los?
Sim. Pode adicionar, remover e modificar os blocos CIDR utilizados por uma VNet.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Se estiver a executar meus serviços numa VNet, pode ligar à internet?
Sim. Todos os serviços implementados numa VNet podem ligar a saída à internet. Para saber mais sobre ligações de internet de saída no Azure, veja [ligações de saída](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se desejar ligar entrada a um recurso implementado através do Resource Manager, o recurso tem de ter um endereço IP público atribuído ao mesmo. Para saber mais sobre endereços IP públicos, veja [endereços IP públicos](virtual-network-public-ip-address.md). Todos os serviços de Cloud do Azure implementadas no Azure tem um VIP publicamente endereçável atribuído ao mesmo. Defina pontos finais de entrada para as funções de PaaS e os pontos finais para máquinas virtuais para ativar estes serviços aceitar ligações a partir da internet.

### <a name="do-vnets-support-ipv6"></a>VNets suportarem o IPv6?
Não. Não é possível utilizar IPv6 com VNets neste momento. Pode no entanto, as máquinas virtuais de balanceamento de endereços IPv6 de atribuir a balanceadores de carga do Azure para carregar. Para obter detalhes, consulte [descrição geral de IPv6 para o Azure Load Balancer](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="can-a-vnet-span-regions"></a>Uma VNet pode abranger a regiões?
Não. Uma VNet está limitada a uma única região. Uma rede virtual, no entanto, abrangem as zonas de disponibilidade. Para saber mais sobre as zonas de disponibilidade, veja [Descrição geral das zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pode ligar redes virtuais em diferentes regiões com o peering de rede virtual. Para obter detalhes, consulte [descrição geral o peering de rede Virtual](virtual-network-peering-overview.md)

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Pode ligar uma rede virtual a outra VNet no Azure?
Sim. Pode ligar uma VNet para outra VNet através de um:
- **Peering de rede virtual**: Para obter detalhes, consulte [descrição geral do VNet peering](virtual-network-peering-overview.md)
- **Um Gateway VPN do Azure**: Para obter detalhes, consulte [configurar uma ligação VNet a VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Resolução de nomes (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Quais são minhas opções de DNS para VNets?
Utilize a tabela de decisão sobre o [resolução de nomes para VMs e instâncias de função](virtual-networks-name-resolution-for-vms-and-role-instances.md) página que os orientam durante todo o DNS opções disponíveis.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Pode especificar servidores DNS para uma VNet?
Sim. Pode especificar endereços IP do servidor DNS nas definições da VNet. A configuração é aplicada como servidores DNS predefinido para todas as VMs na VNet.

### <a name="how-many-dns-servers-can-i-specify"></a>O número de servidores DNS posso especificar?
Referência [limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Posso modificar meu servidores DNS após ter criado a rede?
Sim. Pode alterar a lista de servidores DNS para a sua VNet em qualquer altura. Se alterar a sua lista de servidores DNS, terá de reiniciar cada uma das VMs na sua VNet para que eles para pegar o novo servidor DNS.

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>O que é fornecida pelo Azure DNS e funciona com VNets?
O DNS fornecida pelo Azure é um serviço DNS de multi-inquilino oferecido pela Microsoft. Azure regista todas as suas VMs e instâncias de função do serviço de cloud neste serviço. Este serviço fornece resolução de nomes pelo nome de anfitrião para VMs e instâncias de função contidas no mesmo serviço cloud e através do FQDN para VMs e instâncias de função na mesma VNet. Para saber mais sobre o DNS, consulte [resolução de nomes para VMs e serviços Cloud instâncias de função](virtual-networks-name-resolution-for-vms-and-role-instances.md).

Existe uma limitação para os primeiros 100 serviços em nuvem uma VNet para a resolução de nome entre inquilinos através de DNS fornecida pelo Azure. Se estiver a utilizar o seu próprio servidor DNS, esta limitação não se aplica.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Pode substituir as minhas definições DNS numa base de serviços por VM ou na cloud?
Sim. Pode definir os servidores DNS por VM ou serviço cloud para substituir as predefinições de rede. No entanto, é recomendado que utilize toda a rede DNS tanto quanto possível.

### <a name="can-i-bring-my-own-dns-suffix"></a>Posso usar o meu próprio sufixo DNS?
Não. Não é possível especificar um sufixo DNS personalizado para as suas VNets.

## <a name="connecting-virtual-machines"></a>Ligação de máquinas virtuais

### <a name="can-i-deploy-vms-to-a-vnet"></a>Pode implementar VMs a uma VNet?
Sim. Todas as interfaces de rede (NIC) ligadas a uma VM implementada através do modelo de implementação do Resource Manager tem de estar ligadas a uma VNet. As VMs implementadas por meio do modelo de implementação clássica, podem ser ligadas opcionalmente a uma VNet.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Quais são os diferentes tipos de endereços IP que posso atribuir às VMs?
* **Privada:** Atribuído a cada NIC dentro de cada VM. O endereço é atribuído através do método estático ou dinâmico. Endereços IP privados são atribuídos a partir do intervalo que especificou nas definições de sub-rede da VNet. Recursos implementados através do modelo de implementação clássica são atribuídos endereços IP privados, mesmo que eles não estiverem conectados a uma VNet. O comportamento do método de alocação é diferente dependendo de um recurso foi implementado com o Resource Manager ou o modelo de implementação clássica: 

  - **Resource Manager**: Endereços IP privados atribuídos com o método dinâmico ou estático permanecerá atribuído a uma máquina virtual (Resource Manager) até que o recurso seja eliminado. A diferença é que selecionar o endereço a atribuir ao utilizar estático e escolhe o Azure ao utilizar dinâmico. 
  - **Clássico**: Endereços IP privados atribuídos com o método dinâmico podem ser alterados quando uma máquina virtual (clássico) VM é reiniciada após ter sido no estado parado (desalocada). Se precisa garantir que o endereço IP privado de um recurso implementado através do modelo de implementação clássica nunca mudará, atribua um endereço IP privado com o método estático.

* **Público:** Opcionalmente, é atribuído a NIC anexada às VMs implementadas por meio do modelo de implementação Azure Resource Manager. O endereço pode ser atribuído com o método de alocação estático ou dinâmico. Instâncias de função de todas as VMs e serviços Cloud implementadas através do modelo de implementação clássica existem no serviço cloud, que é atribuído um *dinâmica*, pública endereço IP virtual (VIP). Uma pública *estático* endereço IP, chamado um [endereço IP reservado](virtual-networks-reserved-public-ip.md), opcionalmente, pode ser atribuído como um VIP. Pode atribuir endereços IP públicos individuais instâncias de função VMs ou serviços Cloud implementados através do modelo de implementação clássica. Estes endereços são denominados [IP público de nível de instância (ILPIP](virtual-networks-instance-level-public-ip.md) endereços e pode ser atribuído dinamicamente.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Pode reservar um endereço IP privado para uma VM que vou criar mais tarde?
Não. Não é possível reservar um endereço IP privado. Se um endereço IP privado estiver disponível, é atribuído a uma instância de função de VM ou pelo servidor DHCP. A VM pode ou não ser aquele que pretende que o endereço IP privado atribuído a. No entanto, pode, alterar o endereço IP privado de uma VM já criado, para qualquer endereço IP privado disponível.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Fazer a alteração de endereços IP privada para as VMs numa VNet?
Depende. Se a VM foi implementada com o Resource Manager, não, independentemente do endereço IP foi atribuído com o método de alocação estático ou dinâmico. Se a VM foi implementada por meio do modelo de implementação clássica, endereços IP dinâmicos podem alterar quando uma VM é iniciada depois de ter sido no estado parado (desalocada). O endereço é libertado a partir de uma VM implementada através de qualquer modelo de implementação quando a VM é eliminada.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Posso manualmente atribuir endereços IP a NICs dentro do sistema operacional VM?
Sim, mas não é recomendada, a menos que necessário, por exemplo, quando a atribuição de IP de vários endereços para uma máquina virtual. Para obter detalhes, consulte [a adicionar IP de vários endereços para uma máquina virtual](virtual-network-multiple-ip-addresses-portal.md#os-config). Se o endereço IP atribuído a uma NIC do Azure anexados a uma VM alterações e o endereço IP dentro do sistema de operativo da VM é diferente, perder a conectividade à VM.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Se eu parar um bloco de implementação do serviço em nuvem ou encerrar uma VM a partir do sistema operativo, o que acontece aos meus endereços IP?
Nada. Os endereços IP (VIP público, público e privado) permanecem atribuídos ao bloco de implementação do serviço cloud ou VM.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Posso mover VMs de uma sub-rede para outra sub-rede numa VNet sem implementar novamente?
Sim. Pode encontrar mais informações na [como mover uma VM ou instância de função a uma sub-rede diferente](virtual-networks-move-vm-role-to-subnet.md) artigo.

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Pode configurar um endereço MAC estático para a minha VM?
Não. Um endereço MAC não pode ser configurado de forma estática.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>O endereço MAC permanecem os mesmos para a minha VM depois de criado?
Sim, o endereço MAC permanece o mesmo para uma VM implementada através do Resource Manager e os modelos de implementação clássica, até serem eliminada. Anteriormente, o endereço de MAC foi lançado se a VM foi parada (desalocada), mas agora o endereço MAC é mantido, mesmo quando a VM está no Estado desalocada.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Ligar à internet a partir de uma VM numa VNet?
Sim. Instâncias de função todas as VMs e serviços Cloud, implementadas numa VNet podem ligar à Internet.

## <a name="azure-services-that-connect-to-vnets"></a>Serviços do Azure que ligar VNets

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Pode utilizar aplicações de Web do serviço de aplicações do Azure com uma VNet?
Sim. Pode implementar aplicações Web dentro de uma VNet com um ASE (ambiente de serviço de aplicações). Se tiver uma ligação de ponto a site configurada para a sua VNet, todas as aplicações Web pode ligar em segurança e aceder aos recursos na VNet. Para obter mais informações, veja os artigos seguintes:

* [Criar aplicações Web num ambiente do serviço de aplicações](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Integrar a sua aplicação com uma rede Virtual do Azure](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Utilizar ligações híbridas e de integração de VNet com as aplicações Web](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json#hybrid-connections-and-app-service-environments)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Pode implementar serviços em nuvem com funções web e de trabalho (PaaS) numa VNet?
Sim. (Opcionalmente), pode implementar instâncias de função dos serviços Cloud nas VNets. Para tal, especifique o nome da VNet e os mapeamentos de função/sub-rede na seção de configuração de rede da sua configuração de serviço. Não é necessário atualizar qualquer um dos seus binários.

### <a name="can-i-connect-a-virtual-machine-scale-set-vmss-to-a-vnet"></a>Pode ligar uma Máquina Virtual de dimensionamento definido (VMSS) a uma VNet?
Sim. Tem de ligar um VMSS a uma VNet.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Existe que uma lista completa do Azure de serviços que posso implementar os recursos a partir de numa VNet?
Sim, para obter detalhes, consulte [integração da rede Virtual para serviços do Azure](virtual-network-for-azure-services.md).

### <a name="which-azure-paas-resources-can-i-restrict-access-to-from-a-vnet"></a>Que recursos de PaaS do Azure posso limitar o acesso a partir de uma VNet?

Recursos implementados por meio de alguns serviços PaaS do Azure (por exemplo, o armazenamento do Azure e base de dados do Azure SQL), pode restringir o acesso de rede apenas para os recursos numa VNet através da utilização de pontos finais de serviço de rede virtual. Para obter detalhes, consulte [descrição geral de pontos finais de serviço de rede Virtual](virtual-network-service-endpoints-overview.md).

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Pode mover meus serviços dentro e fora de VNets?
Não. Não é possível mover serviços dentro e fora de VNets. Para mover um recurso para outra VNet, terá de eliminar e voltar a implementar o recurso.

## <a name="security"></a>Segurança

### <a name="what-is-the-security-model-for-vnets"></a>O que é o modelo de segurança para VNets?
VNets estão isoladas um do outro e outros serviços alojados na infraestrutura do Azure. Uma VNet é um limite de fidedignidade.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Posso restringir o fluxo de tráfego de entrada ou saída para recursos ligados à VNet?
Sim. Pode aplicar [grupos de segurança de rede](security-overview.md) para sub-redes individuais dentro de uma VNet, NICs ligadas a uma VNet, ou ambos.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Pode implementar uma firewall entre os recursos ligados à VNet?
Sim. Pode implementar um [aplicação virtual de rede de firewall](https://azure.microsoft.com/marketplace/?term=firewall) de vários fornecedores através do Azure Marketplace.

### <a name="is-there-information-available-about-securing-vnets"></a>Há informações disponíveis sobre como proteger a VNets?
Sim. Para obter detalhes, consulte [descrição geral da segurança de rede de Azure](../security/security-network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="apis-schemas-and-tools"></a>APIs, os esquemas e ferramentas

### <a name="can-i-manage-vnets-from-code"></a>Pode gerir VNets a partir do código?
Sim. Pode utilizar as APIs REST para VNets na [do Azure Resource Manager](/rest/api/virtual-network) e [clássicas (gestão de serviço)](https://go.microsoft.com/fwlink/?LinkId=296833) modelos de implementação.

### <a name="is-there-tooling-support-for-vnets"></a>Existe suporte de ferramentas para VNets?
Sim. Saiba mais sobre como utilizar:
- Portal do Azure para implementar VNets através da [do Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) e [clássico](virtual-networks-create-vnet-classic-pportal.md) modelos de implementação.
- PowerShell para gerenciar VNets implementadas através da [Resource Manager](/powershell/module/az.network) e [clássico](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0) modelos de implementação.
- A interface de linha de comandos do Azure (CLI) para implementar e gerir VNets implementadas através da [Resource Manager](/cli/azure/network/vnet) e [clássico](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-commands-to-manage-network-resources) modelos de implementação.  

## <a name="vnet-peering"></a>VNet peering

### <a name="what-is-vnet-peering"></a>O que é o VNet peering?
VNet peering (ou peering da rede virtual) permite-lhe ligar redes virtuais. Uma ligação VNet peering entre redes virtuais permite-lhe encaminhar tráfego entre as mesmas em privado por meio de endereços IPv4. Máquinas virtuais nas VNets em modo de peering podem comunicar entre si, como se fossem dentro da mesma rede. Nestas redes virtuais podem estar na mesma região ou em diferentes regiões (também conhecido como Global VNet Peering). Também é possível criar ligações de VNet peering entre subscrições do Azure.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>Pode criar uma ligação de peering para uma VNet numa região diferente?
Sim. Global VNet peering permite-lhe configurar o peering entre VNets em diferentes regiões. Global VNet peering está disponível em todas as regiões públicas do Azure e regiões de cloud da China. Não é possível globalmente emparelhamento de regiões públicas do Azure para as regiões de cloud nacional. Global peering não está atualmente disponível na cloud do Governo.

### <a name="what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers"></a>Quais são as restrições relacionadas com a Global VNet Peering e Balanceadores de carga?
Se as duas redes virtuais estiverem numa região diferente (Global VNet Peering), é possível ligar a recursos que utilizar o Balanceador de carga básico. Pode ligar a recursos que utilizam o Balanceador de carga Standard.
Os seguintes recursos utilizam balanceadores de carga básico, que significa que não consegue comunicar aos mesmos através de Global VNet Peering:
- VMs por trás de balanceadores de carga básico
- Conjuntos de dimensionamento de VM com balanceadores de carga básico 
- Cache de Redis 
- Gateway de aplicação (v1) SKU
- Service Fabric
- SQL Always on
- SQL MI
- Gestão de API
- AD DS
- Aplicações Lógicas
- HD Insight
-   Azure Batch
- AKS
- Ambiente do Serviço de Aplicações

Pode ligar a estes recursos através do ExpressRoute ou VNet a VNet através de Gateways de VNet.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Posso habilitar o VNet Peering se meu redes virtuais pertencem a subscrições dentro de diferentes inquilinos do Azure Active Directory?
Sim. É possível estabelecer o VNet Peering (sejam locais ou globais) se as suas subscrições pertencem a diferentes inquilinos do Azure Active Directory. Pode fazê-lo através do PowerShell ou CLI. Portal ainda não é suportado.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>Minha pertença a ligação de peering de VNet *iniciado* Estado, por que não posso ligar?
Se a sua ligação de peering está num estado iniciado, isso significa que criou apenas uma ligação. Tem de ser criada uma ligação bidirecional para estabelecer uma ligação com êxito. Por exemplo, para se ligar a VNet A VNet b, tem de ser criada uma ligação de também a Vneta e de Vneta para também. Criação de ambas as ligações alterará o estado para *ligado.*

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>Minha pertença a ligação de peering de VNet *desligado* Estado, por que não é possível criar uma ligação de peering?
Se a sua ligação de peering de VNet está num estado desligado, significa um dos links criados foi eliminado. Para voltar a estabelecer uma ligação de peering, terá de eliminar a ligação e recriar.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>Posso ligar a minha VNet com uma VNet numa subscrição diferente?
Sim. Pode configurar o peering entre VNets entre subscrições e entre regiões.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Pode posso configurar o peering entre duas VNets com intervalos de endereços correspondente ou sobrepostos?
Não. Espaços de endereços não pode sobrepor ative o Peering de VNet.

### <a name="how-much-do-vnet-peering-links-cost"></a>Fazer quanto de custos de ligações de peering de VNet?
Não existe nenhum custo associado para a criação de uma ligação de peering de VNet. Transferência de dados em ligações de peering é cobrada. [Veja aqui](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="is-vnet-peering-traffic-encrypted"></a>Tráfego de VNet peering é encriptado?
Não. O tráfego entre recursos em VNets em modo de peering é privado e isolada. Continua a ser completamente no Backbone do Microsoft.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Por que é a minha ligação de peering num Estado desconectado?
Aprofundar a ligações de peering de VNet *desligado* estado quando uma ligação VNet peering é eliminada. Tem de eliminar ambas as ligações para restabelecer uma ligação de peering com êxito.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>Se eu configurar o peering também a Vneta e posso criar o peering Vneta no mesmo peer, que significa também e estão no mesmo peer em modo de peering?
Não. Peering transitivo não é suportada. Deve configurar o peering também e estão no mesmo peer para que isso ocorra.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Existem limitações de largura de banda para ligações de peering?
Não. VNet peering, sejam locais ou globais, não impõe quaisquer restrições de largura de banda. Largura de banda é limitada apenas pela VM ou o recurso de computação.

## <a name="virtual-network-tap"></a>TAP de rede virtual

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>Que regiões do Azure estão disponíveis para teste de rede virtual?
Pré-visualização de TOQUE de rede virtual está disponível em todas as regiões do Azure. As interfaces de rede monitorizado, o recurso TAP da rede virtual e a solução de recoletor ou análise tem de ser implementados na mesma região.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>O TAP da rede Virtual suporta qualquer capacidades de filtragem nos pacotes espelhados?
Recursos de filtragem não são suportados com a pré-visualização TAP da rede virtual. Quando uma configuração de TOQUE é adicionada a uma interface de rede uma cópia em profundidade de todos os a entrada e o tráfego de saída na interface de rede é transmitido para o destino de TOQUE.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>Várias configurações de TOQUE podem ser adicionadas a uma interface de rede monitorizado?
Uma interface de rede monitorizada pode ter apenas uma configuração de TOQUE. Verifique com o indivíduo [soluções de parceiros](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) para a capacidade de transmitir várias cópias do tráfego de TOQUE para as ferramentas de análise da sua preferência.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>O mesmo recurso TAP da rede virtual pode agregar o tráfego de interfaces de rede monitorizados em mais de uma rede virtual?
Sim. Da mesma rede virtual recursos de TOQUE pode ser utilizada para agregar tráfego de espelho de interfaces de rede monitorizados em redes virtuais em modo de peering na mesma subscrição ou numa subscrição diferente. O recurso de TOQUE de rede virtual e o destino de Balanceador de carga ou interface de rede de destino tem de estar na mesma subscrição. Todas as subscrições têm de estar no mesmo inquilino do Azure Active Directory.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>Existem quaisquer considerações de desempenho no tráfego de produção se ativar a uma configuração de TAP da rede virtual numa interface de rede?

Rede virtual TOQUE está em pré-visualização. Durante a pré-visualização, não existe nenhum contrato de nível de serviço. O recurso não deve ser utilizado para cargas de trabalho de produção. Quando uma interface de rede de máquina virtual é ativada com uma configuração de TOQUE, os mesmos recursos no anfitrião do azure atribuída à máquina virtual para enviar o tráfego de produção é utilizado para executar a função de espelhamento e enviar os pacotes espelhados. Selecione o correto [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) tamanho da máquina virtual para se certificar de que recursos suficientes disponíveis para a máquina virtual enviar o tráfego de produção e o tráfego de espelho.

### <a name="is-accelerated-networking-for-linuxcreate-vm-accelerated-networking-climd-or-windowscreate-vm-accelerated-networking-powershellmd-supported-with-virtual-network-tap"></a>É accelerated networking para [Linux](create-vm-accelerated-networking-cli.md) ou [Windows](create-vm-accelerated-networking-powershell.md) suportado com o teste de rede virtual?

Poderá adicionar uma configuração de TOQUE numa interface de rede ligada a uma máquina virtual que está ativada com redes aceleradas. Mas o desempenho e a latência na máquina virtual serão afetados pela adição de configuração de TOQUE, uma vez que a descarga de tráfego de espelhamento de não é atualmente suportada pelo Azure accelerated networking.

## <a name="virtual-network-service-endpoints"></a>Pontos finais de serviço de rede virtual

### <a name="what-is-the-right-sequence-of-operations-to-set-up-service-endpoints-to-an-azure-service"></a>O que é a sequência certa de operações para configurar pontos finais de serviço para um serviço do Azure?
Existem dois passos para proteger um recurso de serviço do Azure através de pontos finais de serviço:
1. Ative pontos finais de serviço para o serviço do Azure.
2. Configure ACLs de VNet no serviço do Azure.

A primeira etapa é uma operação do lado de rede e o segundo passo é uma operação de lado do recurso de serviço. Os dois passos podem ser executados ao mesmo administrador ou administradores diferentes com base nas permissões RBAC concedidas à função de administrador. Recomendamos que primeiro ativar pontos finais de serviço da rede virtual antes de configurar ACLs de VNet no lado do serviço do Azure. Por conseguinte, os passos devem ser efetuados na sequência listada acima para configurar pontos finais de serviço da VNet.

>[!NOTE]
> Ambas as operações descritas acima devem ser concluídas antes de pode limitar o acesso de serviço do Azure para a VNet e a sub-rede permitidos. Ativar apenas a pontos finais de serviço para o serviço do Azure no lado de rede não concede o acesso limitado. Além disso, tem também de configurar ACLs de VNet no lado do serviço do Azure.

Determinados serviços (como SQL e o cosmos DB) permitem exceções para a sequência de acima através da **IgnoreMissingVnetServiceEndpoint** sinalizador. Assim que o sinalizador é definido como **True**, ACLs de VNet pode ser definidas no lado do serviço do Azure antes de configurar os pontos de extremidade de serviço no lado de rede. Serviços do Azure fornecem este sinalizador para ajudar os clientes nos casos em que as firewalls IP específicas estão configuradas em serviços do Azure e ativar os pontos de extremidade de serviço do lado de rede pode levar a uma queda de conectividade, uma vez que o IP de origem é alterado de um endereço IPv4 público para um endereço privado. Configurar ACLs de VNet no lado do serviço do Azure antes de definir os pontos finais de serviço do lado de rede pode ajudar a evitar uma queda de conectividade.

### <a name="do-all-azure-services-reside-in-the-azure-virtual-network-provided-by-the-customer-how-does-vnet-service-endpoint-work-with-azure-services"></a>Todos os serviços Azure residem na rede virtual do Azure fornecida pelo cliente? Como funciona com serviços do Azure ponto final de serviço de VNet?

Não, nem todos os serviços do Azure residem na rede virtual do cliente. A maioria dos dados do Azure de serviços, como o armazenamento do Azure, SQL do Azure e Azure Cosmos DB, são serviços de multi-inquilinos, que podem ser acedidos através de endereços IP públicos. Pode saber mais sobre a integração de rede virtual para serviços do Azure [aqui](virtual-network-for-azure-services.md). 

Quando utiliza a funcionalidade de pontos finais de serviço de VNet (ativar o ponto final de serviço de VNet do lado de rede e configurar ACLs de VNet apropriadas no lado do serviço do Azure), acesso a um serviço do Azure é impedido de uma VNet e sub-rede permitidos.

### <a name="how-does-vnet-service-endpoint-provide-security"></a>Como é que o ponto final de serviço da VNet fornece segurança?

A funcionalidade de ponto final de serviço de VNet (ativar o ponto final de serviço de VNet do lado de rede e configurar ACLs de VNet apropriadas no lado do serviço do Azure) limita o acesso de serviço do Azure para permitidos VNet e sub-rede, fornecendo uma segurança ao nível da rede e o isolamento de o tráfego de serviço do Azure. Todo o tráfego através de pontos finais de serviço de VNet flui através do backbone do Microsoft, fornecendo outra camada de isolamento da internet pública. Além disso, os clientes podem escolher remover completamente o acesso público à Internet para os recursos de serviço do Azure e permitir tráfego apenas das suas redes virtuais através de uma combinação de firewall do IP e ACLs de VNet, protegendo, assim, os recursos de serviço do Azure de não autorizado acesso.      

### <a name="what-does-the-vnet-service-endpoint-protect---vnet-resources-or-azure-service"></a>O que faz a VNet em modo de proteção de ponto final de serviço - recursos da VNet ou de serviço do Azure?
Pontos finais de serviço de VNet ajudam a proteger os recursos de serviço do Azure. Recursos da VNet estão protegidos através de grupos de segurança de rede (NSGs).

### <a name="is-there-any-cost-for-using-vnet-service-endpoints"></a>Existe qualquer custo para a utilização de pontos finais de serviço de VNet?

Não, não existe nenhum custo adicional para a utilização de pontos finais de serviço da VNet.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-subscriptions"></a>Pode ligar pontos finais de serviço de VNet e configurar ACLs de VNet, se a rede virtual e os recursos de serviço do Azure pertencerem a subscrições diferentes?

Sim, é possível. Redes virtuais e os recursos de serviço do Azure podem ser uma nas subscrições idêntica ou diferentes. O único requisito é que a rede virtual e os recursos de serviço do Azure tem de estar no mesmo inquilino do Active Directory (AD).

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-ad-tenants"></a>Pode ligar pontos finais de serviço de VNet e configurar ACLs de VNet, se a rede virtual e os recursos de serviço do Azure pertencerem a diferentes inquilinos do AD?
Não, pontos finais de serviço de VNet e ACLs de VNet não são suportadas em inquilinos do AD.

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-express-route-gateway-access-azure-paas-service-over-vnet-service-endpoints"></a>Endereço IP de um dispositivo no local que está conectado por meio do gateway de rede Virtual do Azure (VPN) ou o gateway do Express route pode acessar serviços de PaaS do Azure através de pontos finais de serviço de VNet?
Por predefinição, os recursos de serviço do Azure obtidos para redes virtuais não são acessíveis a partir de redes no local. Se pretender permitir o tráfego no local, tem também de permitir o IP público (habitualmente NAT) endereços do seu no local ou ExpressRoute. Estes endereços IP podem ser adicionados através da configuração de firewall do IP para os recursos de serviço do Azure.

### <a name="can-i-use-vnet-service-endpoint-feature-to-secure-azure-service-to-multiple-subnets-with-in-a-virtual-network-or-across-multiple-virtual-networks"></a>Pode utilizar a funcionalidade de ponto final de serviço de VNet para proteger o serviço do Azure para várias sub-redes numa rede Virtual ou em várias redes virtuais?
Para proteger serviços do Azure para várias sub-redes numa rede virtual ou em várias redes virtuais, ativar pontos finais de serviço no lado de rede em cada um das sub-redes de forma independente e, em seguida, proteger os recursos de serviço do Azure para todas as sub-redes através da configuração ACLs de VNet apropriadas no lado do serviço do Azure.
 
### <a name="how-can-i-filter-outbound-traffic-from-a-virtual-network-to-azure-services-and-still-use-service-endpoints"></a>Como pode filtrar o tráfego de saída de uma rede virtual para serviços do Azure e continuar a utilizar pontos finais de serviço?
Se pretender inspecionar ou filtrar o tráfego destinado a um serviço do Azure de uma rede virtual, pode implementar uma aplicação virtual de rede na rede virtual. Em seguida, pode aplicar pontos finais de serviço para a sub-rede em que a aplicação virtual de rede é implementado e segura de serviço do Azure de recursos apenas para esta sub-rede através de ACLs da VNet. Este cenário também pode ser útil se pretender restringir o acesso de serviço do Azure da sua rede virtual apenas para recursos específicos do Azure com a filtragem de aplicação virtual de rede. Para obter mais informações, veja [saída com aplicações de rede virtual](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).

### <a name="what-happens-when-you-access-an-azure-service-account-that-has-virtual-network-access-control-list-acl-enabled-from-outside-the-vnet"></a>O que acontece quando acessar uma conta de serviço do Azure com a rede virtual lista de controle acesso (ACL) ativada a partir de fora da VNet?
É devolvido o erro HTTP 403 ou HTTP 404.

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-service-account-in-another-region"></a>São as sub-redes de uma rede virtual criados em regiões diferentes, permitidas para aceder a uma conta de serviço do Azure noutra região? 
Sim, para a maioria dos serviços do Azure, as redes virtuais criadas em regiões diferentes podem aceder a serviços do Azure noutra região através de pontos finais de serviço de VNet. Por exemplo, se uma conta do Azure Cosmos DB está na região E.U.A. oeste ou E.U.A. leste e redes virtuais estão em várias regiões, a rede virtual pode aceder ao Azure Cosmos DB. Armazenamento e SQL são exceções e são regionais por natureza e a rede virtual e o serviço do Azure, precisa de estar na mesma região.
  
### <a name="can-an-azure-service-have-both-vnet-acl-and-an-ip-firewall"></a>Um serviço do Azure pode ter a ACL de VNet e uma firewall do IP?
Sim, a ACL de VNet e uma firewall do IP podem coexistir. Os dois recursos complementam entre si para garantir a segurança e isolamento.
 
### <a name="what-happens-if-you-delete-a-virtual-network-or-subnet-that-has-service-endpoint-turned-on-for-azure-service"></a>O que acontece se eliminar uma rede virtual ou sub-rede que tem o ponto final de serviço ativado para o serviço do Azure?
A eliminação de VNets e sub-redes são operações independentes e são suportadas, mesmo quando estão ativados pontos finais de serviço para serviços do Azure. Em casos em que os serviços do Azure têm ACLs de VNet, configurar, para essas VNets e sub-redes, as informações de VNet ACLs associados com que o serviço do Azure é desabilitado quando a VNet ou sub-rede que tem o ponto final de serviço de VNet ativada é eliminado.
 
### <a name="what-happens-if-azure-service-account-that-has-vnet-service-endpoint-enabled-is-deleted"></a>O que acontece se eliminar a conta de serviço do Azure que tenha o ponto final de serviço de VNet ativado?
A eliminação da conta de serviço do Azure é uma operação independente e é suportada, mesmo quando o ponto final de serviço está ativado no lado da rede e ACLs de VNet estão configuradas no lado do serviço do Azure. 

### <a name="what-happens-to-the-source-ip-address-of-a-resource-like-a-vm-in-a-subnet-that-has-vnet-service-endpoint-enabled"></a>O que acontece com o endereço IP de origem de um recurso (como uma VM numa sub-rede) que tem o ponto final de serviço de VNet ativado?
Quando estão ativados pontos finais de serviço de rede virtual, os endereços IP de origem dos recursos na sub-rede da rede virtual muda de utilizar endereços IPV4 públicos para os endereços IP privados da rede virtual do Azure para o tráfego para o serviço do Azure. Tenha em atenção que isto pode causar específicas de firewall IP que estão definidas para o endereço IPV4 público anteriormente para efetuar a ativação de serviços do Azure. 

### <a name="does-service-endpoint-route-always-take-precedence"></a>Rota do ponto final de serviço sempre têm prioridade?
Pontos finais de serviço, adicione uma rota de sistema que tem precedência sobre as rotas BGP e fornece encaminhamento ideal para o tráfego de ponto final de serviço. Pontos finais de serviço assumem sempre o tráfego de serviço diretamente a partir da sua rede virtual para o serviço na rede backbone do Microsoft Azure. Para obter mais informações sobre como o Azure seleciona uma rota, consulte [encaminhamento de tráfego de rede Virtual do Azure](virtual-networks-udr-overview.md).
 
### <a name="how-does-nsg-on-a-subnet-work-with-service-endpoints"></a>Como funciona o NSG numa sub-rede com pontos finais de serviço?
Para alcançar o serviço do Azure, os NSGs necessário permitir a conectividade de saída. Se seus NSGs estão abertas para todo tráfego de saída da Internet, o tráfego de ponto final de serviço deve funcionar. Também pode limitar o tráfego de saída ao serviço de IPs utilizando apenas as etiquetas de serviço.  
 
### <a name="what-permissions-do-i-need-to-set-up-service-endpoints"></a>Que permissões preciso configurar pontos finais de serviço?
Pontos finais de serviço podem ser configurados numa rede virtual de forma independente por um utilizador com acesso de escrita para a rede virtual. Para proteger os recursos de serviço do Azure para uma VNet, o utilizador tem de ter permissão **Microsoft.Network/JoinServicetoaSubnet** para as sub-redes que estão a ser adicionadas. Esta permissão está incluída na função de administrador de serviço interno por padrão e pode ser modificada através da criação de funções personalizadas. Saiba mais sobre as funções incorporadas e atribuição de permissões específicas para [funções personalizadas](https://docs.microsoft.com/azure/role-based-access-control/custom-roles?toc=%2fazure%2fvirtual-network%2ftoc.json).
 

### <a name="can-i-filter-virtual-network-traffic-to-azure-services-allowing-only-specific-azure-service-resources-over-vnet-service-endpoints"></a>Pode, filtrar tráfego de rede virtual para serviços do Azure, permitindo que apenas os recursos específicos de serviço do azure, através de pontos finais de serviço de VNet? 

Políticas de ponto final de serviço de rede virtual (VNet) permitem-lhe filtrar o tráfego de rede virtual para serviços do Azure, permitindo que os recursos de serviço do Azure específico apenas sobre os pontos finais de serviço. Políticas de ponto final fornecem controlo de acesso granular do tráfego de rede virtual para os serviços do Azure. Pode saber mais sobre as políticas de ponto final de serviço [aqui](virtual-network-service-endpoint-policies-overview.md).
 
### <a name="are-there-any-limits-on-how-many-vnet-service-endpoints-i-can-set-up-from-my-vnet"></a>Existem limites sobre quantos VNet pontos finais de serviço que pode configurar na minha VNet?
Não existe nenhum limite no número total de pontos finais de serviço de VNet numa rede virtual. Para um recurso de serviço do Azure (como uma conta do Armazenamento do Microsoft Azure), os serviços podem impor limites ao número de sub-redes utilizadas para a proteção do recurso. A tabela seguinte mostra alguns limites de exemplo: 

|||
|---|---|
|Serviço do Azure| Limites para as regras de VNet|
|Storage do Azure| 100|
|SQL do Azure| 128|
|Azure SQL Data Warehouse|  128|
|Azure KeyVault|    127|
|Azure Cosmos DB|   64|
|Hub de Eventos do Azure|   128|
|Service Bus do Azure| 128|
|Azure Data Lake Store V1|  100|
 
>[!NOTE]
> Os limites estão sujeitos a alterações a critério do serviço do Azure. Consulte a documentação do respetivo serviço para obter detalhes de serviços. 




  



