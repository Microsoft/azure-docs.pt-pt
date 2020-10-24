---
title: Plano Azure redes virtuais / Microsoft Docs
description: Saiba como planear as redes virtuais com base nos requisitos de isolamento, de conectividade e de localização.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2020
ms.author: kumud
ms.openlocfilehash: 29ea65e94e97b69e24c6935328cc01c2295adc5a
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518641"
---
# <a name="plan-virtual-networks"></a>Planear redes virtuais

Criar uma rede virtual para experimentar é fácil o suficiente, mas é provável que implemente várias redes virtuais ao longo do tempo para suportar as necessidades de produção da sua organização. Com algum planeamento, poderá implementar redes virtuais e ligar os recursos de que necessita de forma mais eficaz. A informação neste artigo é muito útil se já estiver familiarizado com redes virtuais e tiver alguma experiência a trabalhar com elas. Se não estiver familiarizado com redes virtuais, é aconselhável ler a visão geral da [rede virtual.](virtual-networks-overview.md)

## <a name="naming"></a>Atribuição de nomes

Todos os recursos Azure têm um nome. O nome deve ser único dentro de um âmbito, que pode variar para cada tipo de recurso. Por exemplo, o nome de uma rede virtual deve ser único dentro de um [grupo de recursos,](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)mas pode ser duplicado dentro de uma [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) ou [região](https://azure.microsoft.com/regions/#services)de Azure . Definir uma convenção de nomeação que você pode usar de forma consistente ao nomear recursos é útil na gestão de vários recursos de rede ao longo do tempo. Para sugestões, consulte [as convenções de nomeação.](../azure-resource-manager/management/resource-name-rules.md#microsoftnetwork)

## <a name="regions"></a>Regiões

Todos os recursos Azure são criados numa região de Azure e subscrição. Um recurso só pode ser criado numa rede virtual que exista na mesma região e subscrição que o recurso. No entanto, pode ligar redes virtuais que existem em diferentes subscrições e regiões. Para mais informações, consulte [a conectividade.](#connectivity) Ao decidir em que região ou regiões devem mobilizar recursos, considere onde os consumidores dos recursos estão fisicamente localizados:

- Os consumidores de recursos normalmente querem a menor latência da rede aos seus recursos. Para determinar as latências relativas entre uma determinada localização e regiões de Azure, consulte [as latências relativas](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Tem requisitos de residência, soberania, conformidade ou resiliência dos dados? Em caso afirmativo, escolher a região que se alinha com os requisitos é fundamental. Para mais informações, consulte [as geografias Azure.](https://azure.microsoft.com/global-infrastructure/geographies/)
- Necessita de resiliência através das Zonas de Disponibilidade Azure dentro da mesma região do Azure para os recursos que utiliza? Pode implementar recursos, como máquinas virtuais (VM) para diferentes zonas de disponibilidade dentro da mesma rede virtual. No entanto, nem todas as regiões de Azure apoiam zonas de disponibilidade. Para saber mais sobre as zonas de disponibilidade e as regiões que as suportam, consulte [zonas de disponibilidade.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="subscriptions"></a>Subscrições

Pode implementar o número de redes virtuais necessárias em cada subscrição, até ao [limite](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Algumas organizações têm subscrições diferentes para diferentes departamentos, por exemplo. Para obter mais informações e considerações em torno das subscrições, consulte [a governação da subscrição](/azure/cloud-adoption-framework/reference/migration-with-enterprise-scaffold#define-your-hierarchy).

## <a name="segmentation"></a>Segmentação

Pode criar várias redes virtuais por subscrição e por região. Pode criar várias sub-redes dentro de cada rede virtual. As considerações que se seguem ajudam-no a determinar quantas redes virtuais e sub-redes necessita:

### <a name="virtual-networks"></a>Redes virtuais

Uma rede virtual é uma parte virtual e isolada da rede pública Azure. Cada rede virtual é dedicada à sua subscrição. Coisas a considerar ao decidir se cria uma rede virtual ou várias redes virtuais numa subscrição:

- Existem requisitos de segurança organizacionais para isolar o tráfego em redes virtuais separadas? Pode optar por ligar redes virtuais ou não. Se ligar redes virtuais, pode implementar um aparelho virtual de rede, como uma firewall, para controlar o fluxo de tráfego entre as redes virtuais. Para mais informações, consulte [a segurança](#security) e [a conectividade.](#connectivity)
- Existem requisitos organizacionais para isolar redes virtuais em [assinaturas](#subscriptions) ou [regiões separadas?](#regions)
- Uma [interface de rede](virtual-network-network-interface.md) permite que um VM comunique com outros recursos. Cada interface de rede tem um ou mais endereços IP privados atribuídos à mesma. Quantas interfaces de rede e [endereços IP privados](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) necessita numa rede virtual? Existem [limites](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) para o número de interfaces de rede e endereços IP privados que pode ter dentro de uma rede virtual.
- Deseja ligar a rede virtual a outra rede virtual ou rede no local? Pode optar por ligar algumas redes virtuais entre si ou redes no local, mas não outras. Para mais informações, consulte [a conectividade.](#connectivity) Cada rede virtual que se conecta a outra rede virtual, ou rede no local, deve ter um espaço de endereço único. Cada rede virtual tem uma ou mais gamas de endereços públicos ou privados atribuídas ao seu espaço de endereço. Uma gama de endereços é especificada no formato de encaminhamento de domínio de internet sem classe (CIDR), tal como 10.0.0.0/16. Saiba mais sobre [os intervalos de endereços](manage-virtual-network.md#add-or-remove-an-address-range) para redes virtuais.
- Tem algum requisito de administração organizacional para recursos em diferentes redes virtuais? Em caso afirmativo, poderá separar os recursos em rede virtual separada para simplificar a [atribuição de permissão](#permissions) a indivíduos da sua organização ou atribuir diferentes políticas a diferentes redes virtuais.
- Quando colocas alguns recursos de serviço da Azure numa rede virtual, eles criam a sua própria rede virtual. Para determinar se um serviço Azure cria a sua própria rede virtual, consulte informações para cada [serviço Azure que pode ser implantado numa rede virtual](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network).

### <a name="subnets"></a>Sub-redes

Uma rede virtual pode ser segmentada em uma ou mais subesí redes até aos [limites](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Coisas a considerar ao decidir se cria uma sub-rede ou várias redes virtuais numa subscrição:

- Cada sub-rede deve ter uma gama de endereços única, especificada no formato CIDR, dentro do espaço de endereço da rede virtual. O intervalo de endereços não pode sobrepor-se a outras sub-redes na rede virtual.
- Se planeia implantar alguns recursos de serviço Azure numa rede virtual, eles podem necessitar ou criar a sua própria sub-rede, por isso deve haver espaço suficiente para que o façam. Para determinar se um serviço Azure cria a sua própria sub-rede, consulte informações para cada [serviço Azure que pode ser implantado numa rede virtual](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Por exemplo, se ligar uma rede virtual a uma rede no local utilizando um Gateway Azure VPN, a rede virtual deve ter uma sub-rede dedicada para o gateway. Saiba mais sobre [as sub-redes gateway.](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)
- A Azure liga o tráfego de rede entre todas as sub-redes numa rede virtual, por padrão. Pode anular o encaminhamento padrão da Azure para evitar o encaminhamento do Azure entre sub-redes ou para encaminhar o tráfego entre sub-redes através de um aparelho virtual de rede, por exemplo. Se necessitar que o tráfego entre recursos na mesma rede virtual flua através de um aparelho virtual de rede (NVA), implante os recursos em diferentes sub-redes. Saiba mais em [segurança.](#security)
- Pode limitar o acesso aos recursos do Azure, como uma conta de armazenamento Azure ou Azure SQL Database, a sub-redes específicas com um ponto final de serviço de rede virtual. Além disso, pode negar o acesso aos recursos a partir da internet. Pode criar várias sub-redes e ativar um ponto final de serviço para algumas sub-redes, mas não para outras. Saiba mais sobre [os pontos finais](virtual-network-service-endpoints-overview.md)de serviço e os recursos Azure para os quais pode capacitá-los.
- Pode associar zero ou um grupo de segurança de rede a cada sub-rede numa rede virtual. Pode associar o mesmo, ou um grupo de segurança de rede diferente a cada sub-rede. Cada grupo de segurança da rede contém regras que permitem ou negam o tráfego de e para fontes e destinos. Saiba mais sobre [grupos de segurança de rede.](#traffic-filtering)

## <a name="security"></a>Segurança

Pode filtrar o tráfego de rede de e para recursos numa rede virtual utilizando grupos de segurança de rede e aparelhos virtuais de rede. Pode controlar como a Azure encaminha o tráfego a partir de sub-redes. Também pode limitar quem na sua organização pode trabalhar com recursos em redes virtuais.

### <a name="traffic-filtering"></a>Filtragem do tráfego

- Pode filtrar o tráfego de rede entre recursos numa rede virtual utilizando um grupo de segurança de rede, um NVA que filtra o tráfego da rede, ou ambos. Para implantar um NVA, como uma firewall, para filtrar o tráfego da rede, consulte o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). Ao utilizar um NVA, também cria rotas personalizadas para encaminhar o tráfego de sub-redes para a NVA. Saiba mais sobre [o encaminhamento de tráfego.](#traffic-routing)
- Um grupo de segurança de rede contém várias regras de segurança padrão que permitem ou negam o tráfego de ou para recursos. Um grupo de segurança de rede pode ser associado a uma interface de rede, a sub-rede em que a interface de rede está, ou ambos. Para simplificar a gestão das regras de segurança, recomenda-se que associe um grupo de segurança de rede a sub-redes individuais, em vez de interfaces de rede individuais dentro da sub-rede, sempre que possível.
- Se diferentes VMs dentro de uma sub-rede precisarem de diferentes regras de segurança aplicadas a eles, pode associar a interface de rede no VM a um ou mais grupos de segurança de aplicações. Uma regra de segurança pode especificar um grupo de segurança de aplicação na sua origem, destino ou ambos. Esta regra aplica-se então apenas às interfaces de rede que são membros do grupo de segurança de aplicações. Saiba mais sobre [grupos de segurança de rede](security-overview.md) e [grupos de segurança de aplicações.](security-overview.md#application-security-groups)
- A Azure cria várias regras de segurança predefinidas dentro de cada grupo de segurança da rede. Uma regra padrão permite que todo o tráfego flua entre todos os recursos de uma rede virtual. Para anular este comportamento, utilize grupos de segurança de rede, encaminhamento personalizado para encaminhar o tráfego para um NVA, ou ambos. Recomenda-se que se familiarize com todas as [regras](security-overview.md#default-security-rules) de segurança padrão do Azure e compreenda como as regras do grupo de segurança de rede são aplicadas a um recurso.

Pode ver projetos de amostras para implementar uma rede de perímetro (também conhecida como DMZ) entre o Azure e a internet utilizando uma [NVA](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

### <a name="traffic-routing"></a>Encaminhamento de tráfego

A Azure cria várias rotas predefinidas para o tráfego de saída a partir de uma sub-rede. Pode anular o encaminhamento padrão do Azure criando uma tabela de rotas e associando-a a uma sub-rede. As razões comuns para anular o encaminhamento padrão do Azure são:
- Porque quer que o tráfego entre sub-redes flua através de uma NVA. Para saber mais sobre como [configurar tabelas de rotas para forçar o tráfego através de uma NVA.](tutorial-create-route-table-portal.md)
- Porque você quer forçar todo o tráfego ligado à Internet através de um NVA, ou no local, através de um gateway Azure VPN. Forçar o tráfego de internet no local para inspeção e exploração madeireira é muitas vezes referido como túnel forçado. Saiba mais sobre como configurar [túneis forçados.](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json)

Se precisar de implementar o encaminhamento personalizado, recomenda-se que se familiarize com [o encaminhamento em Azure.](virtual-networks-udr-overview.md)

## <a name="connectivity"></a>Conectividade

Pode ligar uma rede virtual a outras redes virtuais utilizando o espreitamento de rede virtual ou à sua rede no local, utilizando um gateway Azure VPN.

### <a name="peering"></a>Peering

Ao utilizar [o espreitamento de rede virtual,](virtual-network-peering-overview.md)as redes virtuais podem estar nas mesmas regiões Azure suportadas ou diferentes. As redes virtuais podem estar nas mesmas ou diferentes subscrições do Azure (mesmo subscrições pertencentes a diferentes inquilinos do Azure Ative Directory). Antes de criar um espreitamento, recomenda-se que se familiarize com todos os [requisitos e constrangimentos](virtual-network-manage-peering.md#requirements-and-constraints)de espreitar. A largura de banda entre os recursos em redes virtuais espreitadas na mesma região é a mesma que se os recursos estivessem na mesma rede virtual.

### <a name="vpn-gateway"></a>Gateway de VPN

Pode utilizar um Gateway Azure [VPN](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para ligar uma rede virtual à sua rede no local utilizando uma [VPN site-to-site,](../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ou utilizando uma ligação dedicada com a Azure [ExpressRoute.](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

Você pode combinar o peering e uma porta de entrada VPN para criar [redes de hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json), onde redes virtuais faladas se conectam a uma rede virtual hub, e o hub conecta-se a uma rede no local, por exemplo.

### <a name="name-resolution"></a>Resolução de nomes

Os recursos numa rede virtual não conseguem resolver os nomes dos recursos numa rede virtual espreitada utilizando [o DNS incorporado](virtual-networks-name-resolution-for-vms-and-role-instances.md)da Azure. Para resolver nomes numa rede virtual espreitada, [implemente o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)ou utilize [domínios privados](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)Azure DNS . A resolução de nomes entre recursos numa rede virtual e redes no local também requer que implemente o seu próprio servidor DNS.

## <a name="permissions"></a>Permissões

A Azure utiliza o [controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para recursos. As permissões são atribuídas a um [âmbito](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) na seguinte hierarquia: grupo de gestão, subscrição, grupo de recursos e recursos individuais. Para saber mais sobre a hierarquia, consulte [Organizar os seus recursos.](../azure-resource-manager/management-groups-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Para trabalhar com redes virtuais Azure e todas as suas capacidades relacionadas, tais como peering, grupos de segurança de rede, pontos finais de serviço e tabelas de rotas, pode atribuir membros da sua organização às funções [de Proprietário](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner)Incorporado, [Contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor)ou [Network,](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e atribuir o papel ao âmbito apropriado. Se pretender atribuir permissões específicas para um subconjunto de capacidades de rede virtuais, crie uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e atribua as permissões específicas necessárias para [redes virtuais,](manage-virtual-network.md#permissions) [sub-redes e pontos finais de serviço,](virtual-network-manage-subnet.md#permissions) [interfaces de rede,](virtual-network-network-interface.md#permissions) [grupos](virtual-network-manage-peering.md#permissions)de [segurança de rede e aplicações,](manage-network-security-group.md#permissions)ou [tabelas de rota](manage-route-table.md#permissions) para a função.

## <a name="policy"></a>Política

A Política Azure permite-lhe criar, atribuir e gerir definições políticas. As definições de política impõem regras diferentes sobre os seus recursos, para que os recursos permaneçam conformes com os seus padrões organizacionais e acordos de nível de serviço. A Azure Policy faz uma avaliação dos seus recursos, procurando recursos que não estejam em conformidade com as definições políticas que tem. Por exemplo, pode definir e aplicar uma política que permita a criação de redes virtuais apenas num grupo ou região de recursos específicos. Outra política pode exigir que cada sub-rede tenha um grupo de segurança de rede associado a ela. As políticas são então avaliadas na criação e atualização de recursos.

As políticas são aplicadas à seguinte hierarquia: grupo de gestão, subscrição e grupo de recursos. Saiba mais sobre [a Política Azure](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou implemente algumas definições de [Política Azure de](policy-samples.md)rede virtual .

## <a name="next-steps"></a>Passos seguintes

Conheça todas as tarefas, configurações e opções para uma [rede virtual,](manage-virtual-network.md) [sub-rede e ponto final de serviço,](virtual-network-manage-subnet.md) [interface de rede,](virtual-network-network-interface.md) [peering,](virtual-network-manage-peering.md) [grupo de segurança de rede e aplicação,](manage-network-security-group.md)ou [tabela de rotas.](manage-route-table.md)
