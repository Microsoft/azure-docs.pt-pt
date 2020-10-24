---
title: FaQ de rede virtual Azure
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
ms.date: 06/26/2020
ms.author: kumud
ms.openlocfilehash: 86336ddb557613d42117480a5793e8b0ecbc7bae
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495832"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Perguntas mais frequentes (FAQ) da Rede Virtual do Azure

## <a name="virtual-network-basics"></a>Básicos de rede virtual

### <a name="what-is-an-azure-virtual-network-vnet"></a>O que é uma Rede Virtual Azure (VNet)?
Uma Rede Virtual Azure (VNet) é uma representação da sua própria rede na nuvem. É um isolamento lógico da nuvem do Azure dedicado à sua subscrição. Pode utilizar VNets para provisões e gerir redes privadas virtuais (VPNs) em Azure e, opcionalmente, ligar os VNets a outros VNets em Azure, ou com a sua infraestrutura de TI no local para criar soluções híbridas ou transversais. Cada VNet que cria tem o seu próprio bloco CIDR e pode ser ligado a outras redes VNets e no local, desde que os blocos CIDR não se sobreponham. Também tem o controlo das definições do servidor DNS para VNets e a segmentação do VNet em sub-redes.

Utilize VNets para:

* Crie um VNet privado dedicado apenas para nuvens. Às vezes não é necessário uma configuração transversal para a sua solução. Quando cria um VNet, os seus serviços e VMs dentro do seu VNet podem comunicar direta e seguramente uns com os outros na nuvem. Ainda pode configurar ligações de ponto final para os VMs e serviços que requerem comunicação à Internet, como parte da sua solução.

* Alargar de forma segura o seu centro de dados. Com vNets, pode construir VPNs locais tradicionais (S2S) para aumentar de forma segura a capacidade do seu datacenter. As VPNs S2S utilizam o IPSEC para fornecer uma ligação segura entre o gateway VPN corporativo e o Azure.

* Ativar cenários de nuvem híbrida. Os VNets dão-lhe a flexibilidade para suportar uma série de cenários híbridos em nuvem. Pode ligar aplicações baseadas em nuvem de forma segura a qualquer tipo de sistema no local, como os mainframes e os sistemas Unix.

### <a name="how-do-i-get-started"></a>Como posso começar?
Visite a [documentação](https://docs.microsoft.com/azure/virtual-network/) da rede Virtual para começar. Este conteúdo fornece informações gerais e de implementação para todas as funcionalidades VNet.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Posso usar VNets sem conectividade transversal?
Sim. Pode utilizar um VNet sem ligá-lo às suas instalações. Por exemplo, pode executar controladores de domínio do Microsoft Windows Server Ative Directory e sharePoint apenas num Azure VNet.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Posso realizar a otimização wan entre VNets ou um VNet e o meu centro de dados no local?
Sim. Pode implementar um aparelho virtual de [rede de otimização WAN](https://azuremarketplace.microsoft.com/en-us/marketplace/?term=wan%20optimization) de vários fornecedores através do Azure Marketplace.

## <a name="configuration"></a>Configuração

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Que ferramentas uso para criar um VNet?
Pode utilizar as seguintes ferramentas para criar ou configurar um VNet:

* Portal do Azure
* PowerShell
* CLI do Azure
* Um ficheiro de configuração de rede (netcfg - apenas para VNets clássicos). Consulte o [Configure um VNet utilizando um artigo de ficheiro de configuração de rede.](virtual-networks-using-network-configuration-file.md)

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Que intervalos de endereços posso usar nos meus VNets?
Recomendamos que utilize os intervalos de endereços enumerados no [RFC 1918,](https://tools.ietf.org/html/rfc1918)que foram reservados pelo IETF para espaços de endereço privados e não encaminháveis:
* 10.0.0.0 - 10.255.255.255 (prefixo 10/8)
* 172.16.0.0 - 172.31.255.255 (prefixo 172.16/12)
* 192.168.0.0 - 192.168.255.255 (prefixo 192.168/16)

Outros espaços de endereço podem funcionar, mas podem ter efeitos colaterais indesejáveis.

Além disso, não é possível adicionar os seguintes intervalos de endereços:
* 224.0.0.0/4 (Multicast)
* 255.255.255.255/32 (Transmissão)
* 127.0.0.0/8 (Loopback)
* 169.254.0.0/16 (Link-local)
* 168.63.129.16/32 (DNS Internos)

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Posso ter endereços IP públicos nos meus VNets?
Sim. Para obter mais informações sobre os intervalos de endereços IP públicos, consulte [Criar uma rede virtual.](manage-virtual-network.md#create-a-virtual-network) Os endereços IP públicos não estão acessíveis diretamente a partir da internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Existe um limite para o número de sub-redes no meu VNet?
Sim. Consulte [os limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) para mais detalhes. Os espaços de endereço da sub-rede não podem sobrepor-se uns aos outros.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Existem restrições à utilização de endereços IP nestas sub-redes?
Sim. O Azure reserva cinco endereços IP em cada sub-rede. Estes são x.x.x.0-x.x.x.3 e o último endereço da sub-rede. x.x.x.1 x.x.x.3 é reservado em cada sub-rede para serviços Azure.   
- x.x.x.0: Endereço de rede
- x.x.x.1: Reservado por Azure para o gateway padrão
- x.x.x.2, x.x.x.3: Reservado pela Azure para mapear os IPs DNS do Azure para o espaço VNet
- x.x.x.255: Endereço de transmissão de rede

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Quão pequenas e grandes podem ser vNets e sub-redes?
A sub-rede IPv4 suportada mais pequena é /29, e a maior é /8 (utilizando definições de sub-rede CIDR).  As sub-redes IPv6 devem ter exatamente /64 de tamanho.  

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Posso levar os meus VLANs ao Azure usando VNets?
Não. Os VNets são sobreposições de camada 3. Azure não suporta nenhuma semântica camada 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Posso especificar as políticas de encaminhamento personalizados nos meus VNets e sub-redes?
Sim. Pode criar uma tabela de rotas e associá-la a uma sub-rede. Para obter mais informações sobre o encaminhamento em Azure, consulte [a visão geral do Encaminhamento](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Os VNets suportam multicast ou transmissão?
Não. A multicast e a emissão não são suportadas.

### <a name="what-protocols-can-i-use-within-vnets"></a>Que protocolos posso usar dentro de VNets?
Pode utilizar os protocolos TCP, UDP e ICMP TCP/IP dentro das VNets. O Unicast é suportado dentro de VNets, com exceção do Protocolo de Configuração dinâmica do Anfitrião (DHCP) via Unicast (porta de origem UDP/68 / porto de destino UDP/67) e porta de origem UDP 65330 que é reservada ao anfitrião. Pacotes multicast, transmitidos, ip-in-IP encapsulados e pacotes genéricos de encapsulamento de encaminhamento (GRE) estão bloqueados dentro de VNets. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Posso ver os meus routers padrão dentro de um VNet?
Não.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Posso usar o rasto para diagnosticar conectividade?
Não.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Posso adicionar sub-redes depois de o VNet ser criado?
Sim. As sub-redes podem ser adicionadas aos VNets a qualquer momento, desde que a gama de endereços da sub-rede não faça parte de outra sub-rede e haja espaço disponível na gama de endereços da rede virtual.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Posso modificar o tamanho da minha sub-rede depois de a criar?
Sim. Pode adicionar, remover, expandir ou encolher uma sub-rede se não houver VMs ou serviços implementados na mesma.

### <a name="can-i-modify-vnet-after-i-created-them"></a>Posso modificar o Vnet depois de os ter criado?
Sim. Pode adicionar, remover e modificar os blocos CIDR utilizados por um VNet.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Se estou a gerir os meus serviços num VNet, posso ligar-me à internet?
Sim. Todos os serviços implantados dentro de um VNet podem ligar a saída à internet. Para saber mais sobre as ligações de internet de saída em Azure, consulte [as ligações de saída.](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Se pretender ligar a entrada a um recurso implantado através do Gestor de Recursos, o recurso deve ter um endereço IP público atribuído ao mesmo. Para saber mais sobre endereços IP públicos, consulte [endereços IP públicos.](virtual-network-public-ip-address.md) Todos os Serviços Azure Cloud implantados em Azure têm um VIP publicamente endereçado. Define pontos finais de entrada para funções PaaS e pontos finais para máquinas virtuais para permitir que estes serviços aceitem ligações a partir da internet.

### <a name="do-vnets-support-ipv6"></a>Os VNets suportam o IPv6?
Sim, os VNets podem ser apenas iPv4 ou pilha dupla (IPv4+IPv6).  Para mais detalhes, consulte [a visão geral do IPv6 para redes virtuais Azure](./ipv6-overview.md).

### <a name="can-a-vnet-span-regions"></a>Pode uma região de extensão VNet?
Não. Um VNet está limitado a uma única região. Uma rede virtual, no entanto, abrange zonas de disponibilidade. Para saber mais sobre as zonas de disponibilidade, veja [Descrição geral das zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pode ligar redes virtuais em diferentes regiões com olhando para a rede virtual. Para mais detalhes, consulte [a visão geral da rede virtual](virtual-network-peering-overview.md)

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Posso ligar um VNet a outro VNet em Azure?
Sim. Pode ligar um VNet a outro VNet utilizando:
- **Observação da rede virtual**: Para mais detalhes, consulte [a visão geral do peering VNet](virtual-network-peering-overview.md)
- **Gateway Azure VPN**: Para mais detalhes, consulte [configurar uma ligação VNet-to-VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Resolução de nomes (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Quais são as minhas opções de DNS para VNets?
Utilize a tabela de decisão na página ['Resolução de Nomes para VMs' e Instâncias de Papel](virtual-networks-name-resolution-for-vms-and-role-instances.md) para guiá-lo através de todas as opções DNS disponíveis.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Posso especificar servidores DNS para um VNet?
Sim. Pode especificar endereços IP do servidor DNS nas definições do VNet. A definição é aplicada como o(s) servidor(s) DNS predefinido para todos os VMs no VNet.

### <a name="how-many-dns-servers-can-i-specify"></a>Quantos servidores DNS posso especificar?
Limites de referência [do Azul](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Posso modificar os meus servidores DNS depois de ter criado a rede?
Sim. Pode alterar a lista de servidores DNS para o seu VNet a qualquer momento. Se alterar a sua lista de servidores DNS, tem de efetuar uma renovação de locação DHCP em todos os VMs afetados no VNet, para que as novas definições de DNS produzam efeitos. Para VMs que executam o Windows OS, pode fazê-lo digitando `ipconfig /renew` diretamente no VM. Para outros tipos de SO, consulte a documentação de renovação do arrendamento DHCP para o tipo de SO específico. 

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>O que é DNS fornecido pelo Azure e funciona com VNets?
O DNS fornecido pelo Azure é um serviço DNS multi-inquilino oferecido pela Microsoft. O Azure regista todos os seus VMs e instâncias de função de serviço em nuvem neste serviço. Este serviço fornece resolução de nome por nome de anfitrião para VMs e instâncias de função contidas no mesmo serviço de nuvem, e por FQDN para VMs e instâncias de papel no mesmo VNet. Para saber mais sobre DNS, consulte [a Resolução de Nomes para VMs e serviços de nuvem.](virtual-networks-name-resolution-for-vms-and-role-instances.md)

Existe uma limitação aos primeiros 100 serviços em nuvem num VNet para resolução de nomes de inquilinos cruzados usando DNS fornecidos pelo Azure. Se estiver a utilizar o seu próprio servidor DNS, esta limitação não se aplica.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Posso anular as minhas definições de DNS numa base de serviço por VM ou cloud?
Sim. Pode configurar servidores DNS por VM ou serviço na nuvem para substituir as definições de rede predefinidos. No entanto, recomenda-se que utilize DNS em toda a rede o máximo possível.

### <a name="can-i-bring-my-own-dns-suffix"></a>Posso trazer o meu próprio sufixo DNS?
Não. Não é possível especificar um sufixo DNS personalizado para os seus VNets.

## <a name="connecting-virtual-machines"></a>Conectar máquinas virtuais

### <a name="can-i-deploy-vms-to-a-vnet"></a>Posso colocar VMs num VNet?
Sim. Todas as interfaces de rede (NIC) ligadas a um VM implantado através do modelo de implementação do Gestor de Recursos devem ser ligadas a um VNet. Os VM implantados através do modelo de implementação clássico podem opcionalmente ser ligados a um VNet.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Quais são os diferentes tipos de endereços IP que posso atribuir aos VMs?
* **Particular:** Atribuído a cada NIC dentro de cada VM. O endereço é atribuído utilizando o método estático ou dinâmico. Os endereços IP privados são atribuídos a partir do intervalo especificado nas definições de sub-redes do seu VNet. Os recursos utilizados através do modelo de implementação clássico são atribuídos endereços IP privados, mesmo que não estejam ligados a um VNet. O comportamento do método de atribuição é diferente dependendo se um recurso foi implementado com o Gestor de Recursos ou modelo de implementação clássica: 

  - **Gestor de Recursos**: Um endereço IP privado atribuído ao método dinâmico ou estático permanece atribuído a uma máquina virtual (Gestor de Recursos) até que o recurso seja eliminado. A diferença é que seleciona o endereço para atribuir quando se utiliza estática, e o Azure escolhe quando se utiliza a dinâmica. 
  - **Clássico**: Um endereço IP privado atribuído ao método dinâmico pode mudar quando uma máquina virtual (clássica) VM é reiniciada depois de ter estado no estado parado (deallocated). Se precisar de garantir que o endereço IP privado de um recurso implantado através do modelo de implementação clássico nunca muda, atribua um endereço IP privado com o método estático.

* **Público:** Atribuído opcionalmente a NICs ligados a VMs implantados através do modelo de implementação do Gestor de Recursos Azure. O endereço pode ser atribuído com o método de atribuição estática ou dinâmica. Todas as instâncias de função VMs e Cloud Services implementadas através do modelo de implementação clássica existem dentro de um serviço de cloud, que é atribuído um endereço *dinâmico,* virtual PÚBLICO IP (VIP). Um endereço IP *estático* público, chamado [de endereço IP reservado,](virtual-networks-reserved-public-ip.md)pode opcionalmente ser atribuído como VIP. Pode atribuir endereços IP públicos a vms individuais ou instâncias de funções de Serviços cloud implementadas através do modelo de implementação clássico. Estes endereços são chamados [de IP público nível de instância (endereços ILPIP](virtual-networks-instance-level-public-ip.md) e podem ser atribuídos dinamicamente.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Posso reservar um endereço IP privado para um VM que criarei mais tarde?
Não. Não é possível reservar um endereço IP privado. Se um endereço IP privado estiver disponível, é atribuído a um VM ou instância de função pelo servidor DHCP. O VM pode ou não ser aquele a que pretende o endereço IP privado atribuído. No entanto, pode alterar o endereço IP privado de um VM já criado, para qualquer endereço IP privado disponível.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Os endereços IP privados mudam para VMs num VNet?
Depende. Se o VM foi implementado através do Gestor de Recursos, não, independentemente de o endereço IP ter sido atribuído com o método de atribuição estática ou dinâmica. Se o VM foi implantado através do modelo de implementação clássico, os endereços IP dinâmicos podem mudar quando um VM é iniciado depois de ter estado no estado parado (deallocated). O endereço é libertado a partir de um VM implantado através de qualquer modelo de implantação quando o VM é eliminado.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Posso atribuir manualmente endereços IP a NICs dentro do sistema operativo VM?
Sim, mas não é recomendado a menos que seja necessário, como quando atribuir vários endereços IP a uma máquina virtual. Para mais informações, consulte [adicionar vários endereços IP a uma máquina virtual.](virtual-network-multiple-ip-addresses-portal.md#os-config) Se o endereço IP atribuído a um NIC Azure ligado a uma VM muda e o endereço IP dentro do sistema operativo VM for diferente, perde-se a conectividade com o VM.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Se eu parar uma ranhura de implementação do Cloud Service ou desligar um VM dentro do sistema operativo, o que acontece com os meus endereços IP?
Nada. Os endereços IP (VIP público, público e privado) permanecem atribuídos à ranhura de implementação de serviços na nuvem ou VM.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Posso mover VMs de uma sub-rede para outra sub-rede num VNet sem recolocar?
Sim. Pode encontrar mais informações no [Como mover um VM ou instância de função para um artigo de sub-rede diferente.](virtual-networks-move-vm-role-to-subnet.md)

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Posso configurar um endereço MAC estático para o meu VM?
Não. Um endereço MAC não pode ser configurado estáticamente.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>O endereço MAC permanecerá o mesmo para o meu VM uma vez que seja criado?
Sim, o endereço MAC permanece o mesmo para um VM implantado através do Gestor de Recursos e modelos de implementação clássica até que seja eliminado. Anteriormente, o endereço MAC foi lançado se o VM foi interrompido (deallocated), mas agora o endereço MAC é mantido mesmo quando o VM está no estado de negociação. O endereço MAC permanece atribuído à interface de rede até que a interface de rede seja eliminada ou o endereço IP privado atribuído à configuração IP primária da interface de rede primária é alterado. 

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Posso ligar-me à internet a partir de um VM num VNet?
Sim. Todas as instâncias de funções de VMs e Cloud Services implementadas dentro de um VNet podem ligar-se à Internet.

## <a name="azure-services-that-connect-to-vnets"></a>Serviços Azure que se ligam a VNets

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Posso utilizar aplicações web do Azure App Service com um VNet?
Sim. Pode implementar Aplicações Web dentro de um VNet utilizando um ASE (App Service Environment), ligar o backend das suas aplicações aos seus VNets com a Integração VNet e bloquear o tráfego de entrada na sua app com pontos finais de serviço. Para obter mais informações, veja os seguintes artigos:

* [Funcionalidades de networking do Serviço de Aplicações](../app-service/networking-features.md)
* [Criar aplicativos web num ambiente de serviço de aplicações](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Integre a sua aplicação com uma Rede Virtual Azure](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Restrições de acesso ao Serviço de Aplicações](../app-service/app-service-ip-restrictions.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Posso implementar serviços cloud com funções web e de trabalhadores (PaaS) num VNet?
Sim. Pode (opcionalmente) implementar as instâncias de função dos Serviços cloud dentro de VNets. Para tal, especifique o nome VNet e os mapeamentos de função/sub-rede na secção de configuração de rede da configuração do seu serviço. Não precisa de atualizar nenhum dos seus binários.

### <a name="can-i-connect-a-virtual-machine-scale-set-to-a-vnet"></a>Posso ligar uma balança de máquina virtual definida a um VNet?
Sim. Tem de ligar uma balança de máquina virtual a um VNet.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Existe uma lista completa de serviços Azure que posso enviar recursos de um VNet?
Sim, Para mais detalhes, consulte [a integração da rede Virtual para os serviços Azure.](virtual-network-for-azure-services.md)

### <a name="how-can-i-restrict-access-to-azure-paas-resources-from-a-vnet"></a>Como posso restringir o acesso aos recursos do Azure PaaS a partir de um VNet?

Os recursos implantados através de alguns serviços Azure PaaS (como o Azure Storage e a Azure SQL Database), podem restringir o acesso à rede ao VNet através da utilização de pontos finais de serviço de rede virtual ou link privado Azure. Para mais detalhes, consulte [os pontos finais do serviço](virtual-network-service-endpoints-overview.md)de rede virtual, [visão geral do Link Privado Azure](../private-link/private-link-overview.md)

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Posso transferir os meus serviços para dentro e para fora dos VNets?
Não. Não pode mover serviços dentro e fora dos VNets. Para mover um recurso para outro VNet, tem de eliminar e redistribuir o recurso.

## <a name="security"></a>Segurança

### <a name="what-is-the-security-model-for-vnets"></a>Qual é o modelo de segurança para os VNets?
Os VNets estão isolados uns dos outros, e outros serviços alojados na infraestrutura Azure. Um VNet é um limite de confiança.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Posso restringir o fluxo de tráfego de entrada ou saída aos recursos ligados à VNet?
Sim. Pode aplicar [grupos de segurança de rede](security-overview.md) a sub-redes individuais dentro de um VNet, NICs ligados a um VNet, ou ambos.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Posso implementar uma firewall entre recursos ligados à VNet?
Sim. Pode implantar um [aparelho virtual](https://azure.microsoft.com/marketplace/?term=firewall) de rede de firewall de vários fornecedores através do Azure Marketplace.

### <a name="is-there-information-available-about-securing-vnets"></a>Existe alguma informação disponível sobre a segurança de VNets?
Sim. Para mais informações, consulte [a visão geral da segurança da rede Azure](../security/fundamentals/network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="apis-schemas-and-tools"></a>APIs, esquemas e ferramentas

### <a name="can-i-manage-vnets-from-code"></a>Posso gerir os VNets a partir do código?
Sim. Pode utilizar APIs REST para VNets no [Azure Resource Manager](/rest/api/virtual-network) e modelos de implementação [clássicos.](https://go.microsoft.com/fwlink/?LinkId=296833)

### <a name="is-there-tooling-support-for-vnets"></a>Existe suporte de ferramentas para VNets?
Sim. Saiba mais sobre a utilização:
- O portal Azure para implantar VNets através do [Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) e modelos [clássicos](virtual-networks-create-vnet-classic-pportal.md) de implementação.
- PowerShell para gerir VNets implantados através do [Gestor de Recursos](/powershell/module/az.network) e modelos de implementação [clássicos.](/powershell/module/servicemanagement/azure.service/?view=azuresmps-3.7.0)
- A interface de linha de comando Azure (CLI) para implantar e gerir VNets implantados através do [Gestor de Recursos](/cli/azure/network/vnet) e modelos de implementação [clássicos.](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-resources)  

## <a name="vnet-peering"></a>VNet peering

### <a name="what-is-vnet-peering"></a>O que é o VNet a espreitar?
O espremiamento vNet (ou o espreitamento de rede virtual) permite-lhe ligar redes virtuais. Uma ligação de observação VNet entre redes virtuais permite-lhe encaminhar o tráfego entre eles em privado através de endereços IPv4. As máquinas virtuais nos VNets espreitados podem comunicar-se umas com as outras como se estivessem dentro da mesma rede. Estas redes virtuais podem estar na mesma região ou em diferentes regiões (também conhecidas como Global VNet Peering). As ligações de espremiamento VNet também podem ser criadas através de subscrições Azure.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>Posso criar uma ligação de espreitar a um VNet numa região diferente?
Sim. O estofo global VNet permite-lhe espreitar VNets em diferentes regiões. O persimento global da VNet está disponível em todas as regiões públicas do Azure, regiões de nuvem da China e regiões de nuvens do Governo. Não se pode fazer um par global das regiões públicas de Azure para as regiões de nuvem nacionais.

### <a name="what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers"></a>Quais são os constrangimentos relacionados com os Esparadores e Equilibradores globais de VNet?
Se as duas redes virtuais em duas regiões diferentes estiverem a parar com o VNet Peering Global, não é possível ligar-se a recursos que estão por detrás de um Balanceador de Carga Básico através do IP frontal do Balanceador de Carga. Esta restrição não existe para um Balanceador de Carga Padrão.
Os seguintes recursos podem utilizar balanceadores de carga básicos, o que significa que não pode alcançá-los através do IP frontal do balanceador de carga sobre o Global VNet Peering. No entanto, pode utilizar o VNet Global para chegar aos recursos diretamente através dos seus IPs VNet privados, se permitido. 
- VMs por trás de Balanceadores básicos de carga
- Conjuntos de escala de máquina virtual com balanceadores de carga básicos 
- Cache de Redis 
- Gateway de aplicação (v1) SKU
- Service Fabric
- Gestão de API
- Serviço de Domínio do Diretório Ativo (ADDS)
- Aplicações Lógicas
- HDInsight
-   Azure Batch
- Ambiente do Serviço de Aplicações

Pode ligar-se a estes recursos através do ExpressRoute ou VNet-to-VNet através do VNet Gateways.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Posso permitir que a VNet Peering se as minhas redes virtuais pertençam a subscrições dentro de diferentes inquilinos do Azure Ative Directory?
Sim. É possível estabelecer a VNet Peering (local ou global) se as suas subscrições pertencerem a diferentes inquilinos do Azure Ative Directory. Pode fazê-lo através do Portal, PowerShell ou CLI.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>A minha ligação de observação vNet está no estado *iniciado,* por que não posso ligar?
Se a sua ligação de observação estiver num estado *iniciado,* isto significa que criou apenas uma ligação. Deve ser criada uma ligação bidirecional para estabelecer uma ligação bem sucedida. Por exemplo, para peer VNet A a VNet B, deve ser criada uma ligação de VNetA para VNetB e de VNetB a VNetA. A criação de ambas as ligações mudará o estado para *Connected*.

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>A minha ligação de observação VNet está em estado *desligado,* por que não posso criar uma ligação de espreitar?
Se a sua ligação de observação VNet estiver num estado *desligado,* significa que um dos links criados foi eliminado. Para restabelecer uma ligação de espreitar, terá de eliminar o link e recriá-lo.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>Posso espreitar o meu VNet com um VNet numa subscrição diferente?
Sim. Pode espreitar VNets através de subscrições e regiões.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Posso espreitar dois VNets com intervalos de endereços correspondentes ou sobrepostos?
Não. Os espaços de endereço não devem sobrepor-se para ativar o VNet Peering.

### <a name="how-much-do-vnet-peering-links-cost"></a>Quanto custam os links de observação VNet?
Não há qualquer custo para criar uma ligação de espreitamento VNet. A transferência de dados através de ligações de espreitária é cobrada. [Veja aqui.](https://azure.microsoft.com/pricing/details/virtual-network/)

### <a name="is-vnet-peering-traffic-encrypted"></a>O tráfego de observação da VNet está encriptado?
Quando o tráfego Azure se move entre datacenters (limites físicos externos não controlados pela Microsoft ou em nome da Microsoft), a encriptação da [camada de ligação de dados MACsec](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit) é utilizada no hardware de rede subjacente.  Isto é aplicável ao tráfego de observação VNet.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Porque é que a minha ligação de espreitar está num estado *desligado?*
As ligações de observação VNet entram em estado *de desconexão* quando um link de observação VNet é eliminado. Tem de eliminar ambas as ligações para restabelecer uma ligação de observação bem sucedida.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>Se eu colega de VNetA para VNetB e eu par VNetB para VNetC, isso significa que VNetA e VNetC são espreitados?
Não. O espreitamento transitório não é suportado. Você deve peer VNetA e VNetC para que isto ocorra.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Existem limitações de largura de banda para as ligações de espreitar?
Não. O espreguiçadamento da VNet, local ou global, não impõe restrições à largura de banda. A largura de banda é limitada apenas pelo VM ou pelo recurso compute.

### <a name="how-can-i-troubleshoot-vnet-peering-issues"></a>Como posso resolver problemas com a VNet Peering?
Aqui está um [guia de resolução de problemas](https://support.microsoft.com/en-us/help/4486956/troubleshooter-for-virtual-network-peering-issues) que pode tentar.

## <a name="virtual-network-tap"></a>TAP de rede virtual

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>Quais as regiões Azure disponíveis para a rede virtual TAP?
A pré-visualização da rede virtual TAP está disponível em todas as regiões do Azure. As interfaces de rede monitorizadas, o recurso de rede virtual TAP e a solução de coletor ou de análise devem ser implementadas na mesma região.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>A Rede Virtual TAP suporta alguma capacidade de filtragem nos pacotes espelhados?
As capacidades de filtragem não são suportadas com a pré-visualização da rede virtual TAP. Quando uma configuração TAP é adicionada a uma interface de rede, uma cópia profunda de todo o tráfego de entrada e saída na interface de rede é transmitida para o destino TAP.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>Várias configurações TAP podem ser adicionadas a uma interface de rede monitorizada?
Uma interface de rede monitorizada pode ter apenas uma configuração TAP. Consulte a [solução de parceiro](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) individual para a capacidade de transmitir várias cópias do tráfego TAP para as ferramentas de análise à sua escolha.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>A mesma rede virtual TAP pode agregar tráfego a partir de interfaces de rede monitorizadas em mais de uma rede virtual?
Sim. O mesmo recurso de rede virtual TAP pode ser utilizado para agregar tráfego espelhado a partir de interfaces de rede monitorizadas em redes virtuais vigiadas na mesma subscrição ou numa subscrição diferente. O recurso de rede virtual TAP e o balanceador de carga de destino ou interface de rede de destino devem estar na mesma subscrição. Todas as subscrições devem estar sob o mesmo inquilino do Azure Ative Directory.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>Existem considerações de desempenho no tráfego de produção se eu permitir uma configuração de TAP de rede virtual numa interface de rede?

A rede virtual TAP está em pré-visualização. Durante a pré-visualização, não existe nenhum acordo de nível de serviço. A capacidade não deve ser utilizada para cargas de trabalho de produção. Quando uma interface de rede de máquinas virtuais é ativada com uma configuração TAP, os mesmos recursos no hospedeiro Azure atribuídos à máquina virtual para enviar o tráfego de produção são utilizados para executar a função de espelhamento e enviar os pacotes espelhados. Selecione o tamanho correto da máquina virtual [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para garantir que existem recursos suficientes para a máquina virtual enviar o tráfego de produção e o tráfego espelhado.

### <a name="is-accelerated-networking-for-linux-or-windows-supported-with-virtual-network-tap"></a>A rede acelerada para [o Linux](create-vm-accelerated-networking-cli.md) ou [Windows](create-vm-accelerated-networking-powershell.md) é suportada com a rede virtual TAP?

Será possível adicionar uma configuração TAP numa interface de rede ligada a uma máquina virtual que esteja ativada com uma rede acelerada. Mas o desempenho e a latência na máquina virtual serão afetados pela adição da configuração TAP, uma vez que a descarga para o tráfego de espelhamento não é suportada pelo Azure em rede acelerada.

## <a name="virtual-network-service-endpoints"></a>Pontos finais de serviço de rede virtual

### <a name="what-is-the-right-sequence-of-operations-to-set-up-service-endpoints-to-an-azure-service"></a>Qual é a sequência certa de operações para configurar pontos finais de serviço para um serviço Azure?
Existem duas etapas para garantir um recurso de serviço Azure através de pontos finais de serviço:
1. Ligue os pontos finais de serviço para o serviço Azure.
2. Configurar VNet ACLs no serviço Azure.

O primeiro passo é uma operação lateral de rede e o segundo passo é uma operação do lado do recurso de serviço. Ambas as etapas podem ser executadas pelo mesmo administrador ou por diferentes administradores com base nas permissões rbac concedidas à função de administrador. Recomendamos que ligue primeiro os pontos finais de serviço para a sua rede virtual antes de configurar acLs VNet no lado do serviço Azure. Assim, os passos devem ser executados na sequência acima listada para configurar os pontos finais de serviço VNet.

>[!NOTE]
> Ambas as operações descritas acima devem ser concluídas antes de poder limitar o acesso do serviço Azure à VNet e à sub-rede permitidas. Apenas ligar os pontos finais de serviço para o serviço Azure no lado da rede não lhe proporciona o acesso limitado. Além disso, também deve configurar ACLs VNet no lado do serviço Azure.

Certos serviços (como SQL e CosmosDB) permitem exceções à sequência acima através da bandeira **IgnoreMissingVnetServiceEndpoint.** Uma vez definida a bandeira para **True,** os ACLs VNet podem ser configurado no lado de serviço Azure antes de configurar os pontos finais de serviço no lado da rede. Os serviços Azure fornecem esta bandeira para ajudar os clientes nos casos em que as firewalls IP específicas são configuradas nos serviços Azure e ligar os pontos finais de serviço no lado da rede pode levar a uma queda de conectividade, uma vez que a fonte IP muda de um endereço público IPv4 para um endereço privado. A configuração de ACLs VNet no lado do serviço Azure antes de definir pontos finais de serviço no lado da rede pode ajudar a evitar uma queda de conectividade.

### <a name="do-all-azure-services-reside-in-the-azure-virtual-network-provided-by-the-customer-how-does-vnet-service-endpoint-work-with-azure-services"></a>Todos os serviços Azure residem na rede virtual Azure fornecida pelo cliente? Como funciona o ponto final de serviço VNet com os serviços Azure?

Não, nem todos os serviços da Azure residem na rede virtual do cliente. A maioria dos serviços de dados da Azure, tais como Azure Storage, Azure SQL e Azure Cosmos DB, são serviços multi-inquilinos que podem ser acedidos em endereços IP públicos. Pode saber mais sobre integração de rede virtual para serviços Azure [aqui.](virtual-network-for-azure-services.md) 

Quando utilizar a função de pontos finais de serviço VNet (ligar o ponto final de serviço VNet no lado da rede e configurar ACLs VNet apropriados no lado do serviço Azure), o acesso a um serviço Azure é restringido a partir de um VNet e sub-rede permitidos.

### <a name="how-does-vnet-service-endpoint-provide-security"></a>Como é que o ponto final de serviço VNet fornece segurança?

A função de ponto final de serviço VNet (ligar o ponto final do serviço VNet no lado da rede e configurar ACLs VNet adequados no lado do serviço Azure) limita o acesso do serviço Azure à VNet e sub-rede permitidas, proporcionando assim uma segurança e isolamento ao nível da rede do tráfego de serviço Azure. Todo o tráfego que utiliza os pontos finais do serviço VNet flui sobre a espinha dorsal da Microsoft, proporcionando assim mais uma camada de isolamento da internet pública. Além disso, os clientes podem optar por remover totalmente o acesso público à Internet aos recursos de serviço Azure e permitir o tráfego apenas a partir da sua rede virtual através de uma combinação de firewall IP e VNet ACLs, protegendo assim os recursos de serviço Azure de acesso não autorizado.      

### <a name="what-does-the-vnet-service-endpoint-protect---vnet-resources-or-azure-service"></a>O que protege o ponto final do serviço VNet - recursos VNet ou serviço Azure?
Os pontos finais do serviço VNet ajudam a proteger os recursos de serviço da Azure. Os recursos VNet são protegidos através de Grupos de Segurança de Rede (NSGs).

### <a name="is-there-any-cost-for-using-vnet-service-endpoints"></a>Existe algum custo para a utilização de pontos finais de serviço VNet?

Não, não há custo adicional para a utilização de pontos finais de serviço VNet.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-subscriptions"></a>Posso ligar os pontos finais do serviço VNet e configurar os VNet ACLs se a rede virtual e os recursos de serviço Azure pertencerem a diferentes subscrições?

Sim, é possível. As redes virtuais e os recursos de serviço Azure podem estar nas mesmas subscrições ou diferentes. A única exigência é que tanto a rede virtual como os recursos de serviço da Azure devem estar sob o mesmo inquilino ative directory (AD).

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-ad-tenants"></a>Posso ligar os pontos finais de serviço VNet e configurar os VNet ACLs se a rede virtual e os recursos de serviço Azure pertencerem a diferentes inquilinos da AD?
Sim, é possível quando se utilizam pontos finais de serviço para o Azure Storage e para o Azure Key Vault. Para o resto dos serviços, os pontos finais de serviço VNet e VNet ACLs não são suportados em inquilinos AD.

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-expressroute-gateway-access-azure-paas-service-over-vnet-service-endpoints"></a>O endereço IP de um dispositivo no local que está ligado através do Gateway da Rede Virtual Azure (VPN) ou do Gateway ExpressRoute pode aceder ao Serviço Azure PaaS através dos pontos finais do serviço VNet?
Por predefinição, os recursos de serviço do Azure obtidos para redes virtuais não são acessíveis a partir de redes no local. Se pretender permitir o tráfego a partir do local, também deve permitir endereços IP públicos (tipicamente, NAT) a partir das suas instalações ou ExpressRoute. Estes endereços IP podem ser adicionados através da configuração de firewall IP para os recursos de serviço Azure.

### <a name="can-i-use-vnet-service-endpoint-feature-to-secure-azure-service-to-multiple-subnets-within-a-virtual-network-or-across-multiple-virtual-networks"></a>Posso utilizar a funcionalidade VNet Service Endpoint para garantir o serviço Azure a várias sub-redes dentro de uma rede virtual ou através de várias redes virtuais?
Para garantir os serviços Azure a várias sub-redes dentro de uma rede virtual ou através de várias redes virtuais, ative os pontos finais de serviço no lado da rede em cada uma das sub-redes de forma independente e, em seguida, fixe os recursos de serviço Azure a todas as sub-redes, criando ACLs VNet apropriados no lado do serviço Azure.
 
### <a name="how-can-i-filter-outbound-traffic-from-a-virtual-network-to-azure-services-and-still-use-service-endpoints"></a>Como posso filtrar o tráfego de saída de uma rede virtual para os serviços Azure e ainda utilizar pontos finais de serviço?
Se pretender inspecionar ou filtrar o tráfego destinado a um serviço Azure a partir de uma rede virtual, pode implantar um aparelho virtual de rede dentro da rede virtual. Em seguida, pode aplicar pontos finais de serviço na sub-rede onde o aparelho virtual de rede é implantado e fixar recursos de serviço Azure apenas a esta sub-rede através de ACLs VNet. Este cenário também pode ser útil se pretender restringir o acesso do serviço Azure da sua rede virtual apenas a recursos específicos do Azure utilizando a filtragem de aparelhos virtuais de rede. Para obter mais informações, veja [saída com aplicações de rede virtual](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).

### <a name="what-happens-when-you-access-an-azure-service-account-that-has-a-virtual-network-access-control-list-acl-enabled-from-outside-the-vnet"></a>O que acontece quando acede a uma conta de serviço Azure que tem uma lista de controlo de acesso à rede virtual (ACL) ativada fora do VNet?
O erro HTTP 403 ou HTTP 404 é devolvido.

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-service-account-in-another-region"></a>As sub-redes de uma rede virtual criada em diferentes regiões podem aceder a uma conta de serviço Azure noutra região? 
Sim, para a maioria dos serviços Azure, redes virtuais criadas em diferentes regiões podem aceder aos serviços Azure noutra região através dos pontos finais do serviço VNet. Por exemplo, se uma conta DB Azure Cosmos estiver no Oeste dos EUA ou leste dos EUA e as redes virtuais estiverem em várias regiões, a rede virtual pode aceder a Azure Cosmos DB. O armazenamento e o SQL são exceções e são de natureza regional e tanto a rede virtual como o serviço Azure precisam de estar na mesma região.
  
### <a name="can-an-azure-service-have-both-a-vnet-acl-and-an-ip-firewall"></a>Um serviço Azure pode ter um VNet ACL e uma firewall IP?
Sim, um VNet ACL e uma firewall IP podem coexistir. Ambas as características complementam-se mutuamente para garantir o isolamento e a segurança.
 
### <a name="what-happens-if-you-delete-a-virtual-network-or-subnet-that-has-service-endpoint-turned-on-for-azure-service"></a>O que acontece se eliminar uma rede virtual ou uma sub-rede que tenha o ponto final de serviço ligado para o serviço Azure?
A eliminação de VNets e sub-redes são operações independentes e são suportadas mesmo quando os pontos finais de serviço são ligados para os serviços Azure. Nos casos em que os serviços Azure tenham VNet ACLs configurados, para esses VNets e sub-redes, as informações VNet ACL associadas a esse serviço Azure são desativadas quando um VNet ou sub-rede que tem o ponto final de serviço VNet ligado é eliminado.
 
### <a name="what-happens-if-an-azure-service-account-that-has-a-vnet-service-endpoint-enabled-is-deleted"></a>O que acontece se uma conta de serviço Azure que tenha um ponto final do Serviço VNet ativado for eliminada?
A supressão de uma conta de serviço Azure é uma operação independente e é suportada mesmo quando o ponto final de serviço está ativado no lado da rede e os ACLs VNet são criados no lado do serviço Azure. 

### <a name="what-happens-to-the-source-ip-address-of-a-resource-like-a-vm-in-a-subnet-that-has-vnet-service-endpoint-enabled"></a>O que acontece com o endereço IP de origem de um recurso (como um VM numa sub-rede) que tem o ponto final de serviço VNet ativado?
Quando os pontos finais do serviço de rede virtual estão ativados, os endereços IP de origem dos recursos na sub-rede da sua rede virtual passam a utilizar endereços IPV4 públicos para os endereços IP privados da rede virtual Azure para tráfego para o serviço Azure. Note que isto pode causar firewalls IP específicos que são definidos para endereço PÚBLICO IPV4 mais cedo sobre os serviços Azure falharem. 

### <a name="does-the-service-endpoint-route-always-take-precedence"></a>A rota do ponto final de serviço tem sempre precedência?
Os pontos finais de serviço adicionam uma rota de sistema que tem precedência sobre as rotas BGP e fornece o encaminhamento ideal para o tráfego de ponto final de serviço. Os pontos finais do serviço levam sempre o tráfego de serviço diretamente da sua rede virtual para o serviço na rede de espinha dorsal do Microsoft Azure. Para obter mais informações sobre como o Azure seleciona uma rota, consulte [o encaminhamento de tráfego da rede Virtual Azure.](virtual-networks-udr-overview.md)
 
### <a name="how-does-nsg-on-a-subnet-work-with-service-endpoints"></a>Como funciona a NSG numa sub-rede com pontos finais de serviço?
Para chegar ao serviço Azure, os NSGs precisam de permitir a conectividade de saída. Se os seus NSGs forem abertos a todo o tráfego de saída da Internet, então o tráfego de ponto final de serviço deve funcionar. Também pode limitar o tráfego de saída a IPs de serviço apenas utilizando as etiquetas de Serviço.  
 
### <a name="what-permissions-do-i-need-to-set-up-service-endpoints"></a>Que permissões preciso para configurar pontos finais de serviço?
Os pontos finais de serviço podem ser configurados numa rede virtual de forma independente por um utilizador com acesso por escrito à rede virtual. Para garantir os recursos de serviço do Azure a um VNet, o utilizador deve ter permissão **Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action** for the subnets being added. Esta permissão está incluída na função de administrador de serviço incorporada por padrão e pode ser modificada criando funções personalizadas. Saiba mais sobre funções incorporadas e a atribuição de permissões específicas a [funções personalizadas](https://docs.microsoft.com/azure/role-based-access-control/custom-roles?toc=%2fazure%2fvirtual-network%2ftoc.json).
 

### <a name="can-i-filter-virtual-network-traffic-to-azure-services-allowing-only-specific-azure-service-resources-over-vnet-service-endpoints"></a>Posso filtrar o tráfego de rede virtual para os serviços Azure, permitindo apenas recursos específicos de serviço azul, sobre os pontos finais do serviço VNet? 

As políticas de ponto final de serviço de rede virtual (VNet) permitem filtrar o tráfego de rede virtual para os serviços Azure, permitindo apenas recursos específicos de serviço Azure sobre os pontos finais do serviço. As políticas de ponto final fornecem controlo de acesso granular desde o tráfego de rede virtual até aos serviços Azure. Pode saber mais sobre as políticas de ponto final de serviço [aqui.](virtual-network-service-endpoint-policies-overview.md)

### <a name="does-azure-active-directory-azure-ad-support-vnet-service-endpoints"></a>O Azure Ative Directory (Azure AD) suporta pontos finais de serviço VNet?

O Azure Ative Directory (Azure AD) não suporta os pontos finais de serviço de forma nativa. A lista completa dos serviços Azure que suportam os pontos finais do serviço VNet pode ser vista [aqui.](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) Note que a tag "Microsoft.AzureActiveDirectory" listada nos pontos finais de serviço de suporte de serviços é utilizada para apoiar pontos finais de serviço para a ADLS Gen 1. Para o ADLS Gen 1, a integração virtual da rede para o Azure Data Lake Storage Gen1 utiliza a segurança do ponto final do serviço de rede virtual entre a sua rede virtual e o Azure Ative Directory (Azure AD) para gerar pedidos de segurança adicionais no token de acesso. Essas afirmações são, posteriormente, utilizadas para autenticar a rede virtual na conta do Data Lake Storage Gen1 e permitir o acesso. Saiba mais sobre [a Azure Data Lake Store Gen 1 VNet Integration](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="are-there-any-limits-on-how-many-vnet-service-endpoints-i-can-set-up-from-my-vnet"></a>Existem limites para quantos pontos finais de serviço VNet posso configurar a partir do meu VNet?
Não existe limite para o número total de pontos finais de serviço VNet numa rede virtual. Para um recurso de serviço Azure (como uma conta de Armazenamento Azure), os serviços podem impor limites ao número de sub-redes utilizadas para assegurar o recurso. A tabela a seguir mostra alguns limites de exemplo: 

|Serviço do Azure| Limites às regras VNet|
|---|---|
|Armazenamento do Azure| 100|
|SQL do Azure| 128|
|Azure Synapse Analytics|   128|
|Azure KeyVault|    127|
|Azure Cosmos DB|   64|
|Hub de Eventos do Azure|   128|
|Azure Service Bus| 128|
|Loja Azure Data Lake V1|  100|
 
>[!NOTE]
> Os limites são sujeitos a alterações ao critério do serviço Azure. Consulte a respetiva documentação de serviço para mais detalhes sobre os serviços. 




  



