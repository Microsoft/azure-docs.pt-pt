---
title: Planejar redes virtuais do Azure | Microsoft Docs
description: Saiba como planear as redes virtuais com base nos requisitos de isolamento, de conectividade e de localização.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 3a4a9aea-7608-4d2e-bb3c-40de2e537200
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2018
ms.author: kumud
ms.openlocfilehash: dfa7681b43bd18e15ee929156ab9a45bb9790c5b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845584"
---
# <a name="plan-virtual-networks"></a>Planear redes virtuais

A criação de uma rede virtual para experimentar é fácil o suficiente, mas é provável que você implante várias redes virtuais ao longo do tempo para dar suporte às necessidades de produção de sua organização. Com algum planejamento, você poderá implantar redes virtuais e conectar os recursos necessários com mais eficiência. As informações neste artigo serão mais úteis se você já estiver familiarizado com redes virtuais e tiver alguma experiência para trabalhar com elas. Se você não estiver familiarizado com redes virtuais, é recomendável ler [visão geral da rede virtual](virtual-networks-overview.md).

## <a name="naming"></a>Atribuição de nomes

Todos os recursos do Azure têm um nome. O nome deve ser exclusivo dentro de um escopo, que pode variar para cada tipo de recurso. Por exemplo, o nome de uma rede virtual deve ser exclusivo dentro de um [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), mas pode ser duplicado em uma [assinatura](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) ou em uma [região](https://azure.microsoft.com/regions/#services)do Azure. Definir uma Convenção de nomenclatura que você pode usar de forma consistente quando os recursos de nomenclatura são úteis ao gerenciar vários recursos de rede ao longo do tempo. Para obter sugestões, consulte [convenções de nomenclatura](/azure/architecture/best-practices/resource-naming#networking).

## <a name="regions"></a>Regiões

Todos os recursos do Azure são criados em uma região e uma assinatura do Azure. Um recurso só pode ser criado em uma rede virtual que exista na mesma região e assinatura que o recurso. No entanto, você pode conectar redes virtuais que existem em diferentes assinaturas e regiões. Para obter mais informações, consulte [conectividade](#connectivity). Ao decidir em quais regiões os recursos serão implantados, considere onde os consumidores dos recursos estão localizados fisicamente:

- Os consumidores de recursos normalmente desejam a menor latência de rede para seus recursos. Para determinar as latências relativas entre um local especificado e as regiões do Azure, consulte [Exibir latências relativas](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Você tem requisitos de residência de dados, soberania, conformidade ou resiliência? Nesse caso, escolher a região que se alinha aos requisitos é essencial. Para obter mais informações, consulte [geografias do Azure](https://azure.microsoft.com/global-infrastructure/geographies/).
- Você precisa de resiliência em Zonas de Disponibilidade do Azure na mesma região do Azure para os recursos que você implanta? Você pode implantar recursos, como máquinas virtuais (VM), em diferentes zonas de disponibilidade dentro da mesma rede virtual. Entretanto, nem todas as regiões do Azure dão suporte a zonas de disponibilidade. Para saber mais sobre as zonas de disponibilidade e as regiões que dão suporte a elas, consulte [zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="subscriptions"></a>Subscrições

Você pode implantar quantas redes virtuais forem necessárias em cada assinatura, até o [limite](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Algumas organizações têm assinaturas diferentes para diferentes departamentos, por exemplo. Para obter mais informações e considerações sobre assinaturas, consulte [governança de assinatura](/azure/cloud-adoption-framework/reference/migration-with-enterprise-scaffold#define-your-hierarchy).

## <a name="segmentation"></a>Segmentação

Você pode criar várias redes virtuais por assinatura e por região. Você pode criar várias sub-redes em cada rede virtual. As considerações a seguir ajudam a determinar quantas redes virtuais e sub-redes você precisa:

### <a name="virtual-networks"></a>Redes virtuais

Uma rede virtual é uma parte virtual e isolada da rede pública do Azure. Cada rede virtual é dedicada à sua assinatura. Itens a serem considerados ao decidir se deseja criar uma rede virtual ou várias redes virtuais em uma assinatura:

- Existem requisitos de segurança organizacional para isolar o tráfego em redes virtuais separadas? Você pode optar por conectar redes virtuais ou não. Se você conectar redes virtuais, poderá implementar uma solução de virtualização de rede, como um firewall, para controlar o fluxo de tráfego entre as redes virtuais. Para obter mais informações, consulte [segurança](#security) e [conectividade](#connectivity).
- Existem requisitos organizacionais para isolar redes virtuais em [assinaturas](#subscriptions) ou [regiões](#regions)separadas?
- Uma [interface de rede](virtual-network-network-interface.md) permite que uma VM se comunique com outros recursos. Cada interface de rede tem um ou mais endereços IP privados atribuídos a ele. Quantas interfaces de rede e [endereços IP privados](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) você precisa em uma rede virtual? Há [limites](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) para o número de interfaces de rede e endereços IP privados que você pode ter em uma rede virtual.
- Deseja conectar a rede virtual a outra rede virtual ou rede local? Você pode optar por conectar algumas redes virtuais entre si ou redes locais, mas não outras. Para obter mais informações, consulte [conectividade](#connectivity). Cada rede virtual que você conecta a outra rede virtual ou rede local deve ter um espaço de endereço exclusivo. Cada rede virtual tem um ou mais intervalos de endereços públicos ou privados atribuídos a seu espaço de endereço. Um intervalo de endereços é especificado em formato CIDR (roteamento de domínio de Internet sem classe), como 10.0.0.0/16. Saiba mais sobre [intervalos de endereços](manage-virtual-network.md#add-or-remove-an-address-range) para redes virtuais.
- Você tem algum requisito de administração organizacional para recursos em diferentes redes virtuais? Nesse caso, você pode separar recursos em uma rede virtual separada para simplificar a [atribuição de permissão](#permissions) para indivíduos em sua organização ou para atribuir políticas diferentes a diferentes redes virtuais.
- Quando você implanta alguns recursos de serviço do Azure em uma rede virtual, eles criam sua própria rede virtual. Para determinar se um serviço do Azure cria sua própria rede virtual, confira informações para cada [serviço do Azure que pode ser implantado em uma rede virtual](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network).

### <a name="subnets"></a>Sub-redes

Uma rede virtual pode ser segmentada em uma ou mais sub-redes até os [limites](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Itens a serem considerados ao decidir se deseja criar uma sub-rede ou várias redes virtuais em uma assinatura:

- Cada sub-rede deve ter um intervalo de endereços exclusivo, especificado no formato CIDR, dentro do espaço de endereço da rede virtual. O intervalo de endereços não pode se sobrepor a outras sub-redes na rede virtual.
- Se você planeja implantar alguns recursos de serviço do Azure em uma rede virtual, eles podem exigir ou criar sua própria sub-rede, portanto, deve haver espaço não alocado suficiente para que eles o façam. Para determinar se um serviço do Azure cria sua própria sub-rede, consulte informações para cada [serviço do Azure que pode ser implantado em uma rede virtual](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Por exemplo, se você conectar uma rede virtual a uma rede local usando um gateway de VPN do Azure, a rede virtual deverá ter uma sub-rede dedicada para o gateway. Saiba mais sobre as [sub-redes de gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub).
- Por padrão, o Azure roteia o tráfego de rede entre todas as sub-redes em uma rede virtual. Você pode substituir o roteamento padrão do Azure para impedir o roteamento do Azure entre sub-redes ou rotear o tráfego entre sub-redes por meio de uma solução de virtualização de rede, por exemplo. Se você precisar que o tráfego entre os recursos no mesmo fluxo de rede virtual por meio de uma NVA (solução de virtualização de rede), implante os recursos em sub-redes diferentes. Saiba mais em [segurança](#security).
- Você pode limitar o acesso aos recursos do Azure, como uma conta de armazenamento do Azure ou um banco de dados SQL do Azure, a sub-redes específicas com um ponto de extremidade de serviço de rede virtual. Além disso, você pode negar o acesso aos recursos da Internet. Você pode criar várias sub-redes e habilitar um ponto de extremidade de serviço para algumas sub-redes, mas não para outras. Saiba mais sobre os [pontos de extremidade de serviço](virtual-network-service-endpoints-overview.md)e os recursos do Azure para os quais você pode habilitá-los.
- Você pode associar zero ou um grupo de segurança de rede a cada sub-rede em uma rede virtual. Você pode associar o mesmo grupo de segurança de rede, ou outro, a cada sub-rede. Cada grupo de segurança de rede contém regras, que permitem ou negam o tráfego de e para origens e destinos. Saiba mais sobre [grupos de segurança de rede](#traffic-filtering).

## <a name="security"></a>Segurança

Você pode filtrar o tráfego de rede de e para recursos em uma rede virtual usando grupos de segurança de rede e dispositivos de virtualização de rede. Você pode controlar como o Azure roteia o tráfego de sub-redes. Você também pode limitar quem em sua organização pode trabalhar com recursos em redes virtuais.

### <a name="traffic-filtering"></a>Filtragem do tráfego

- Você pode filtrar o tráfego de rede entre os recursos em uma rede virtual usando um grupo de segurança de rede, um NVA que filtra o tráfego de rede ou ambos. Para implantar um NVA, como um firewall, para filtrar o tráfego de rede, consulte o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). Ao usar um NVA, você também cria rotas personalizadas para rotear o tráfego de sub-redes para o NVA. Saiba mais sobre o [Roteamento de tráfego](#traffic-routing).
- Um grupo de segurança de rede contém várias regras de segurança padrão que permitem ou negam o tráfego de ou para recursos. Um grupo de segurança de rede pode ser associado a um adaptador de rede, à sub-rede em que a interface de rede está, ou ambos. Para simplificar o gerenciamento de regras de segurança, é recomendável associar um grupo de segurança de rede a sub-redes individuais, em vez de interfaces de rede individuais na sub-rede, sempre que possível.
- Se VMs diferentes em uma sub-rede precisarem de regras de segurança diferentes aplicadas a elas, você poderá associar a interface de rede na VM a um ou mais grupos de segurança de aplicativo. Uma regra de segurança pode especificar um grupo de segurança de aplicativo em sua origem, destino ou ambos. Essa regra aplica-se somente às interfaces de rede que são membros do grupo de segurança de aplicativo. Saiba mais sobre os grupos de segurança de [rede](security-overview.md) e os grupos de segurança de [aplicativo](security-overview.md#application-security-groups).
- O Azure cria várias regras de segurança padrão em cada grupo de segurança de rede. Uma regra padrão permite que todo o tráfego flua entre todos os recursos em uma rede virtual. Para substituir esse comportamento, use grupos de segurança de rede, roteamento personalizado para rotear o tráfego para um NVA ou ambos. É recomendável que você se familiarize com todas as [regras de segurança padrão](security-overview.md#default-security-rules) do Azure e entenda como as regras do grupo de segurança de rede são aplicadas a um recurso.

Você pode exibir os designs de exemplo para implementar uma rede de perímetro (também conhecida como DMZ) entre o Azure e a Internet usando um [NVA](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

### <a name="traffic-routing"></a>Roteamento de tráfego

O Azure cria várias rotas padrão para o tráfego de saída de uma sub-rede. Você pode substituir o roteamento padrão do Azure criando uma tabela de rotas e associando-a a uma sub-rede. Os motivos comuns para substituir o roteamento padrão do Azure são:
- Porque você deseja que o tráfego entre as sub-redes flua por um NVA. Para saber mais sobre como [Configurar tabelas de rotas para forçar o tráfego por meio de um NVA](tutorial-create-route-table-portal.md).
- Porque você deseja forçar todo o tráfego de entrada na Internet por meio de um NVA, ou local, por meio de um gateway de VPN do Azure. Forçar o tráfego de Internet local para inspeção e registro em log geralmente é conhecido como túnel forçado. Saiba mais sobre como configurar o [túnel forçado](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

Se você precisar implementar o roteamento personalizado, é recomendável que você se familiarize com o [Roteamento no Azure](virtual-networks-udr-overview.md).

## <a name="connectivity"></a>Ligação

Você pode conectar uma rede virtual a outras redes virtuais usando o emparelhamento de rede virtual ou a sua rede local usando um gateway de VPN do Azure.

### <a name="peering"></a>Peering

Ao usar o [emparelhamento de rede virtual](virtual-network-peering-overview.md), as redes virtuais podem estar nas mesmas regiões do Azure, ou diferentes, com suporte. As redes virtuais podem estar na mesma assinatura ou em assinaturas diferentes do Azure (até mesmo assinaturas que pertencem a locatários Azure Active Directory diferentes). Antes de criar um emparelhamento, é recomendável que você se familiarize com todos os [requisitos e restrições](virtual-network-manage-peering.md#requirements-and-constraints)de emparelhamento. A largura de banda entre os recursos nas redes virtuais emparelhadas na mesma região é a mesma de se os recursos estivessem na mesma rede virtual.

### <a name="vpn-gateway"></a>Gateway de VPN

Você pode usar um [Gateway de VPN](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure para conectar uma rede virtual à sua rede local usando uma [VPN site a site](../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ou usando uma conexão dedicada com o Azure [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Você pode combinar o emparelhamento e um gateway de VPN para criar [redes de Hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json), em que as redes virtuais spoke se conectam a uma rede virtual de Hub e o Hub se conecta a uma rede local, por exemplo.

### <a name="name-resolution"></a>Resolução de nomes

Os recursos em uma rede virtual não podem resolver os nomes de recursos em uma rede virtual emparelhada usando o [DNS interno](virtual-networks-name-resolution-for-vms-and-role-instances.md)do Azure. Para resolver nomes em uma rede virtual emparelhada, [implante seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)ou use [domínios privados](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)do DNS do Azure. A resolução de nomes entre recursos em uma rede virtual e em redes locais também exige que você implante seu próprio servidor DNS.

## <a name="permissions"></a>Permissões

O Azure utiliza o RBAC ( [controle de acesso baseado em função](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ) para recursos. As permissões são atribuídas a um [escopo](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) na seguinte hierarquia: grupo de gerenciamento, assinatura, grupo de recursos e recurso individual. Para saber mais sobre a hierarquia, consulte [organizar seus recursos](../azure-resource-manager/management-groups-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Para trabalhar com redes virtuais do Azure e todos os seus recursos relacionados, como emparelhamento, grupos de segurança de rede, pontos de extremidade de serviço e tabelas de rotas, você pode atribuir membros da sua organização ao [proprietário](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner)interno, [colaborador](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor)ou funções de [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e, em seguida, atribuir a função ao escopo apropriado. Se você quiser atribuir permissões específicas para um subconjunto de recursos de rede virtual, crie uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e atribua as permissões específicas necessárias [para redes virtuais](manage-virtual-network.md#permissions), [sub-redes e pontos de extremidade de serviço](virtual-network-manage-subnet.md#permissions), [adaptadores de rede](virtual-network-network-interface.md#permissions), [emparelhamento](virtual-network-manage-peering.md#permissions), grupos de segurança de [aplicativo e de rede](manage-network-security-group.md#permissions)ou tabelas de [rota](manage-route-table.md#permissions) para a função.

## <a name="policy"></a>Política

Azure Policy permite criar, atribuir e gerenciar definições de política. As definições de política impõem diferentes regras sobre seus recursos, para que os recursos permaneçam em conformidade com seus padrões organizacionais e contratos de nível de serviço. Azure Policy executa uma avaliação de seus recursos, verificando os recursos que não estão em conformidade com as definições de política que você tem. Por exemplo, você pode definir e aplicar uma política que permite a criação de redes virtuais em apenas um grupo de recursos ou região específico. Outra política pode exigir que cada sub-rede tenha um grupo de segurança de rede associado a ela. As políticas são avaliadas durante a criação e atualização de recursos.

As políticas são aplicadas à hierarquia a seguir: grupo de gerenciamento, assinatura e grupo de recursos. Saiba mais sobre o [Azure Policy](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou implante alguns exemplos de [modelo de política](policy-samples.md) de rede virtual.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre todas as tarefas, configurações e opções de uma [rede virtual](manage-virtual-network.md), [ponto de extremidade de sub-rede e serviço](virtual-network-manage-subnet.md), interface de [rede](virtual-network-network-interface.md), [emparelhamento](virtual-network-manage-peering.md), grupo de [segurança de aplicativo e rede](manage-network-security-group.md)ou [tabela de rotas](manage-route-table.md).
