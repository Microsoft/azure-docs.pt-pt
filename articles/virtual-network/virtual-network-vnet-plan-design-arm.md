---
title: Planear as redes virtuais do Azure | Documentos da Microsoft
description: Saiba como planear para redes virtuais com base nos seus requisitos de localização, a conectividade e a isolamento.
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
ms.openlocfilehash: 0a80630ffa363d2b633667d8104cc0326c4afa2e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66478605"
---
# <a name="plan-virtual-networks"></a>Planear as redes virtuais

Criar uma rede virtual para experimentar é muito fácil, mas é provável que irá implementar várias redes virtuais ao longo do tempo para suportar as necessidades de produção da sua organização. Com algum planejamento, será capaz de implementar redes virtuais e ligue-se os recursos que necessários com mais eficiência. As informações neste artigo são mais útil se já estiver familiarizado com as redes virtuais e tem alguma experiência trabalhando com eles. Se não estiver familiarizado com as redes virtuais, é recomendável que leia [descrição geral de rede Virtual](virtual-networks-overview.md).

## <a name="naming"></a>Atribuição de nomes

Todos os recursos do Azure tem um nome. O nome tem de ser exclusivo dentro de um âmbito, que pode variar para cada tipo de recurso. Por exemplo, o nome de uma rede virtual tem de ser exclusivo dentro de um [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), mas pode ser duplicada dentro de um [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) ou do Azure [região](https://azure.microsoft.com/regions/#services). Definir uma convenção de nomenclatura que pode usar consistentemente quando atribuir nomes a recursos é útil quando gerir vários recursos de rede ao longo do tempo. Para obter sugestões, consulte [convenções de nomenclatura](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#networking).

## <a name="regions"></a>Regiões

Todos os recursos do Azure são criados numa região do Azure e subscrição. Só é possível criar um recurso numa rede virtual que exista na mesma região e subscrição que o recurso. No entanto, pode ligar redes virtuais que estejam em subscrições diferentes e regiões. Para obter mais informações, consulte [conectividade](#connectivity). Ao decidir quais regiões para implementar recursos no, considere onde os consumidores de recursos estão fisicamente localizados:

- Os consumidores de recursos, normalmente, pretendem a menor latência de rede aos respetivos recursos. Para determinar as latências relativas entre uma localização especificada e regiões do Azure, veja [ver latências relativas](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Tem requisitos de residência, de soberania, de conformidade ou de resiliência de dados? Se assim for, escolher a região que se alinha os requisitos de é essencial. Para obter mais informações, consulte [geografias do Azure](https://azure.microsoft.com/global-infrastructure/geographies/).
- Precisa resiliência em zonas de disponibilidade do Azure na mesma região do Azure para os recursos a que implementar? Pode implementar recursos, como as máquinas virtuais (VM) em diferentes zonas de disponibilidade dentro da mesma rede virtual. Nem todas as regiões do Azure suportam zonas de disponibilidade no entanto. Para saber mais sobre as zonas de disponibilidade e as regiões que suportam-las, veja [zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="subscriptions"></a>Subscriptions

Pode implementar redes virtuais tantos conforme necessário em cada subscrição, até a [limite](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Algumas organizações têm subscrições diferentes para diferentes departamentos, por exemplo. Para obter mais informações e considerações em torno de subscrições, veja [governação de subscrições](/azure/architecture/cloud-adoption-guide/subscription-governance#define-your-hierarchy).

## <a name="segmentation"></a>Segmentação

Pode criar várias redes virtuais por subscrição e por região. Pode criar várias sub-redes dentro de cada rede virtual. As considerações que se seguem ajudam a determinar quantos redes virtuais e sub-redes necessita:

### <a name="virtual-networks"></a>Redes virtuais

Uma rede virtual é uma parte isolada e virtual de rede pública do Azure. Cada rede virtual é dedicado à sua subscrição. Aspetos a considerar quando decidir se pretende criar uma rede virtual ou várias redes virtuais numa subscrição:

- Existem quaisquer requisitos de segurança organizacional para isolar o tráfego nas redes virtuais separadas? Pode optar por ligar redes virtuais ou não. Se ligar redes virtuais, pode implementar uma aplicação virtual de rede, como uma firewall, para controlar o fluxo de tráfego entre as redes virtuais. Para obter mais informações, consulte [security](#security) e [conectividade](#connectivity).
- Quaisquer requisitos organizacionais existem para isolamento de redes virtuais em separado [subscrições](#subscriptions) ou [regiões](#regions)?
- R [interface de rede](virtual-network-network-interface.md) permite a uma VM comunicar com outros recursos. Cada interface de rede tem um ou mais endereços IP privados atribuídos à mesma. O número de interfaces de rede e [endereços IP privados](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) de que precisa numa rede virtual? Existem [limites](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) para o número de interfaces de rede e endereços IP privados que pode ter numa rede virtual.
- Que pretende ligar a rede virtual a outra rede virtual ou a rede no local? Pode optar por ligar algumas redes virtuais entre si ou redes no local, mas não para outros. Para obter mais informações, consulte [conectividade](#connectivity). Cada rede virtual que se liga a outra rede virtual ou a rede no local, tem de ter um espaço de endereço exclusivo. Cada rede virtual tem um ou mais intervalos de endereços públicos ou privados atribuídos ao seu espaço de endereços. Um intervalo de endereços é especificado classless internet domínio encaminhamento (CIDR) formato, como 10.0.0.0/16. Saiba mais sobre [intervalos de endereços](manage-virtual-network.md#add-or-remove-an-address-range) para redes virtuais.
- Tem requisitos de administração organizacional de recursos em diferentes redes virtuais? Se assim, pode separar os recursos numa rede virtual separada para simplificar [atribuição de permissão](#permissions) para indivíduos da sua organização ou para atribuir políticas diferentes para redes virtuais diferentes.
- Ao implementar alguns recursos de serviço do Azure numa rede virtual, é criar sua própria rede virtual. Para determinar se um serviço do Azure cria a sua própria rede virtual, consulte informações para cada [serviço do Azure que pode ser implementado numa rede virtual](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network).

### <a name="subnets"></a>Sub-redes

Uma rede virtual pode ser segmentada numa ou mais sub-redes até a [limites](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Aspetos a considerar quando decidir se pretende criar uma sub-rede ou várias redes virtuais numa subscrição:

- Cada sub-rede tem de ter um intervalo de endereço exclusivo, especificado no formato CIDR, dentro do espaço de endereços da rede virtual. O intervalo de endereços não pode sobrepor-se com outras sub-redes na rede virtual.
- Se planeia implementar alguns recursos de serviço do Azure numa rede virtual, eles podem exigir, ou criar sua própria sub-rede, do espaço aqui tem de ser suficientemente não alocadas para os mesmos fazer isso. Para determinar se um serviço do Azure cria a sua própria sub-rede, consulte informações para cada [serviço do Azure que pode ser implementado numa rede virtual](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Por exemplo, se ligar uma rede virtual a uma rede no local com um Gateway de VPN do Azure, a rede virtual tem de ter uma sub-rede dedicada para o gateway. Saiba mais sobre [sub-redes de gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub).
- O Azure encaminha o tráfego entre todas as sub-redes numa rede virtual, por predefinição de rede. É possível substituir a predefinição do Azure, encaminhamento para impedir o encaminhamento do Azure entre sub-redes ou para encaminhar o tráfego entre sub-redes através de uma aplicação virtual de rede, por exemplo. Se necessitar de que o tráfego entre os recursos no mesmo fluxo de rede virtual através de uma aplicação virtual de rede (NVA), implemente os recursos em sub-redes diferentes. Saiba mais em [segurança](#security).
- Pode limitar o acesso aos recursos do Azure como uma conta de armazenamento do Azure ou a base de dados SQL do Azure, para sub-redes específicas com um ponto de extremidade do serviço de rede virtual. Além disso, pode negar o acesso aos recursos da internet. Pode criar várias sub-redes e ativar um ponto de final de serviço para alguns sub-redes, mas não para outros. Saiba mais sobre [pontos finais de serviço](virtual-network-service-endpoints-overview.md), e os recursos do Azure pode ativá-los para.
- Pode associar zero ou um grupo de segurança de rede para cada sub-rede numa rede virtual. Pode associar o mesmo ou outro, grupo de segurança a cada sub-rede de rede. Cada grupo de segurança de rede contém regras, que permitem ou negam o tráfego de e para origens e destinos. Saiba mais sobre [grupos de segurança de rede](#traffic-filtering).

## <a name="security"></a>Segurança

Pode filtrar o tráfego de rede de e para recursos numa rede virtual com grupos de segurança de rede e dispositivos de rede virtual. Pode controlar a forma como o Azure encaminha o tráfego das sub-redes. Também pode limitar quem na sua organização pode trabalhar com recursos em redes virtuais.

### <a name="traffic-filtering"></a>Filtragem do tráfego

- Pode filtrar o tráfego de rede entre recursos numa rede virtual com um grupo de segurança de rede, uma NVA que filtra o tráfego de rede, ou ambos. Para implementar uma NVA, como uma firewall, para filtrar o tráfego de rede, consulte a [do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). Quando utilizar uma NVA, também criar rotas personalizadas para encaminhar o tráfego de sub-redes para a NVA. Saiba mais sobre [encaminhamento de tráfego](#traffic-routing).
- Um grupo de segurança de rede contém várias regras de segurança predefinidas que permitem ou negam o tráfego de ou recursos. Um grupo de segurança de rede pode ser associado a uma interface de rede, a sub-rede na qual que a interface de rede se encontra ou ambos. Para simplificar a gestão de regras de segurança, é recomendável que associe um grupo de segurança de rede para sub-redes individuais, em vez de interfaces de rede individual dentro da sub-rede, sempre que possível.
- Se precisam de diferentes VMs numa sub-rede regras de segurança diferentes aplicadas às mesmas, pode associar a interface de rede na VM para um ou mais grupos de segurança de aplicação. Uma regra de segurança pode especificar um grupo de segurança de aplicação na respetiva origem, destino ou ambos. Essa regra, em seguida, aplica-se apenas às interfaces de rede que são membros do grupo de segurança de aplicações. Saiba mais sobre [grupos de segurança de rede](security-overview.md) e [grupos de segurança de aplicativo](security-overview.md#application-security-groups).
- O Azure cria o padrão de várias regras de segurança em cada grupo de segurança de rede. Uma regra padrão permite que todo o tráfego flua entre todos os recursos numa rede virtual. Para substituir esse comportamento, utilize a segurança de rede agrupa, personalizado de encaminhamento para encaminhar o tráfego para uma NVA, ou ambos. É recomendável que se familiarizar com todas as do Azure [regras de segurança predefinidas](security-overview.md#default-security-rules) e compreender como as regras do grupo de segurança de rede são aplicadas a um recurso.

Pode ver os designs de exemplo para a implementação de uma rede de perímetro (também conhecida como DMZ) entre o Azure e a internet utilizando um [NVA](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

### <a name="traffic-routing"></a>Encaminhamento de tráfego

O Azure cria várias rotas padrão para tráfego de saída a partir de uma sub-rede. É possível substituir a predefinição do Azure encaminhamento ao criar uma tabela de rotas e associá-lo a uma sub-rede. Motivos comuns para substituir a predefinição do Azure encaminhamento são:
- Uma vez que pretende que o tráfego entre sub-redes fluir através de uma NVA. Para obter mais informações sobre como [configurar tabelas de rota para forçar o tráfego através de uma NVA](tutorial-create-route-table-portal.md).
- Como deseja forçar todo o tráfego vinculado à internet através de uma NVA, ou no local, através de um gateway de VPN do Azure. Forçar internet tráfego no local para inspeção e registo é frequentemente referido como túnel forçado. Saiba mais sobre como configurar [túnel forçado](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

Se precisar de implementar o encaminhamento personalizado, recomenda-se que se familiarizar com [encaminhamento no Azure](virtual-networks-udr-overview.md).

## <a name="connectivity"></a>Conectividade

Pode ligar uma rede virtual a outras redes virtuais com o peering de rede virtual ou à sua rede no local, com um gateway de VPN do Azure.

### <a name="peering"></a>Peering

Ao usar [peering de rede virtual](virtual-network-peering-overview.md), as redes virtuais podem estar no mesmo, ou diferente, suportado regiões do Azure. As redes virtuais podem estar nas mesmas ou em diferentes subscrições do Azure (até mesmo subscrições que pertencem a diferentes inquilinos do Azure Active Directory). Antes de criar um peering, é recomendável que se familiarizar com todas as do peering [requisitos e restrições](virtual-network-manage-peering.md#requirements-and-constraints). Largura de banda entre os recursos em redes virtuais em modo de peering na mesma região é igual à medida que os recursos estivessem na mesma rede virtual.

### <a name="vpn-gateway"></a>Gateway de VPN

Pode usar do Azure [Gateway de VPN](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para ligar uma rede virtual à sua rede no local com um [site-site VPN](../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json), ou utilizar uma ligação dedicada com o Azure [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Pode combinar peering e criar um gateway de VPN [redes hub- and -spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json), onde redes virtuais indicadas ligam a uma rede virtual de hub e o hub se liga a uma rede no local, por exemplo.

### <a name="name-resolution"></a>Resolução de nomes

Recursos numa rede virtual não é possível resolver os nomes dos recursos numa rede virtual em modo de peering através do Azure [DNS incorporado](virtual-networks-name-resolution-for-vms-and-role-instances.md). Para resolver nomes numa rede virtual em modo de peering, [implementar seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server), ou utilizar o DNS do Azure [domínios privados](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Resolução de nomes entre os recursos numa rede virtual e redes no local também requer a implementação de seu próprio servidor DNS.

## <a name="permissions"></a>Permissões

Utiliza o Azure [controlo de acesso baseado em funções](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (RBAC) aos recursos. As permissões estão atribuídas a um [âmbito](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) na hierarquia do seguinte: Subscrição, grupo de gestão, grupo de recursos e recursos individuais. Para saber mais sobre a hierarquia, veja [organizar os recursos](../azure-resource-manager/management-groups-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Para trabalhar com redes virtuais do Azure e todos os seus recursos relacionados, tais como o peering, os grupos de segurança de rede, pontos finais de serviço e tabelas de rota, pode atribuir membros da sua organização incorporada [proprietário](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner), [Contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor), ou [Contribuidor de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) funções e, em seguida, atribuir a função para o âmbito adequado. Se pretender atribuir permissões específicas para um subconjunto de capacidades de rede virtual, crie uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e atribuir as permissões específicas necessárias [redes virtuais](manage-virtual-network.md#permissions), [ sub-redes e pontos finais de serviço](virtual-network-manage-subnet.md#permissions), [interfaces de rede](virtual-network-network-interface.md#permissions), [peering](virtual-network-manage-peering.md#permissions), [grupos de segurança de rede e a aplicação](manage-network-security-group.md#permissions), ou [tabelas de rotas](manage-route-table.md#permissions) à função.

## <a name="policy"></a>Política

O Azure Policy permite-lhe criar, atribuir e gerir definições de política. Definições de política impõem diferentes regras aos recursos, para que os recursos esteja em conformidade com os seus padrões organizacionais e contratos de nível de serviço. O Azure Policy realiza uma avaliação dos seus recursos, a análise de recursos que não estão em conformidade com as definições de política que existentes. Por exemplo, pode definir e aplicar uma política que permite a criação de redes virtuais em apenas um grupo de recursos específico ou a região. Outra política pode exigir que todas as sub-redes tem um grupo de segurança de rede associado à mesma. As políticas, em seguida, são avaliadas quando cria e atualiza recursos.

As políticas são aplicadas para a hierarquia seguinte: Subscrição, o grupo de gestão e o grupo de recursos. Saiba mais sobre [o Azure policy](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou implementar alguma rede virtual [modelo de política](policy-samples.md) exemplos.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre todas as tarefas, configurações e opções para um [rede virtual](manage-virtual-network.md), [sub-rede e serviço de ponto final](virtual-network-manage-subnet.md), [interface de rede](virtual-network-network-interface.md), [peering](virtual-network-manage-peering.md), [grupo de segurança de rede e da aplicação](manage-network-security-group.md), ou [tabela de rotas](manage-route-table.md).
