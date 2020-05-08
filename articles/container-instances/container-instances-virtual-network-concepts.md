---
title: Cenários para usar uma rede virtual
description: Cenários, recursos e limitações para implantar grupos de contentores numa rede virtual Azure.
ms.topic: article
ms.date: 04/29/2020
ms.author: danlep
ms.openlocfilehash: 77fbdb1720e571027f28b5bdca5c0e3c65c3ded2
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584405"
---
# <a name="virtual-network-scenarios-and-resources"></a>Cenários e recursos de rede virtual

[A Rede Virtual Azure](../virtual-network/virtual-networks-overview.md) fornece redes seguras e privadas para os seus recursos Azure e no local. Ao implantar grupos de contentores numa rede virtual Azure, os seus contentores podem comunicar de forma segura com outros recursos da rede virtual. 

Este artigo fornece antecedentes sobre cenários de rede virtual, limitações e recursos. Para exemplos de implantação utilizando o Azure CLI, consulte as instâncias de [contentores de implantação numa rede virtual Azure](container-instances-vnet.md).

## <a name="scenarios"></a>Cenários

Grupos de contentores implantados numa rede virtual Azure permitem cenários como:

* Comunicação direta entre grupos de contentores na mesma subrede
* Envie a saída de carga de trabalho [baseada em tarefas](container-instances-restart-policy.md) de instâncias de contentores para uma base de dados na rede virtual
* Recuperar conteúdo para instâncias de contentores a partir de um [ponto final](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço na rede virtual
* Permitir a comunicação de contentores com recursos no local através de um [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ExpressRoute](../expressroute/expressroute-introduction.md)
* Integre com a [Firewall Azure](../firewall/overview.md) para identificar o tráfego de saída originário do contentor 
* Resolver nomes através do DNS azure interno para comunicação com os recursos do Azure na rede virtual, como máquinas virtuais
* Utilize as regras do NSG para controlar o acesso dos contentores a subredes ou outros recursos de rede

## <a name="unsupported-networking-scenarios"></a>Cenários de networking não suportados 

* **Azure Load Balancer** - Colocação de um Balancer de Carga Azure em frente a instâncias de contentores num grupo de contentores em rede não é suportado
* **O peering global** da rede virtual - Peering global (conectando redes virtuais em regiões de Azure) não é suportado
* **Etiqueta IP pública ou DNS** - Grupos de contentores implantados numa rede virtual não suportam atualmente expor contentores diretamente à internet com um endereço IP público ou um nome de domínio totalmente qualificado

## <a name="other-limitations"></a>Outras limitações

* Atualmente, apenas os contentores Linux são suportados num grupo de contentores implantado para uma rede virtual.
* Para implantar grupos de contentores numa sub-rede, a sub-rede não pode conter outros tipos de recursos. Remova todos os recursos existentes de uma sub-rede existente antes de implantar grupos de contentores para ela, ou crie uma nova sub-rede.
* Não se pode usar uma [identidade gerida](container-instances-managed-identity.md) num grupo de contentores implantado numa rede virtual.
* Não é possível permitir uma sonda de [vivacidade](container-instances-liveness-probe.md) ou uma sonda de [prontidão](container-instances-readiness-probe.md) num grupo de contentores implantado numa rede virtual.
* Devido aos recursos adicionais de rede envolvidos, as implantações para uma rede virtual são tipicamente mais lentas do que a implementação de uma instância padrão de contentores.

## <a name="where-to-deploy"></a>Onde implantar

As seguintes regiões e recursos máximos estão disponíveis para implantar um grupo de contentores numa rede virtual Azure.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="required-network-resources"></a>Recursos de rede necessários

Existem três recursos da Rede Virtual Azure necessários para a implantação de grupos de contentores para uma rede virtual: a [própria rede virtual,](#virtual-network) uma [subrede delegada](#subnet-delegated) dentro da rede virtual e um perfil de [rede.](#network-profile) 

### <a name="virtual-network"></a>Rede virtual

Uma rede virtual define o espaço de endereço no qual cria uma ou mais subredes. Em seguida, implanta recursos Azure (como grupos de contentores) nas subredes da sua rede virtual.

### <a name="subnet-delegated"></a>Subnet (delegado)

As subredes segmentam a rede virtual em espaços de endereços separados utilizáveis pelos recursos Azure que você colocar neles. Cria-se uma ou várias subredes dentro de uma rede virtual.

A sub-rede que utiliza para grupos de contentores pode conter apenas grupos de contentores. Quando se coloca pela primeira vez um grupo de contentores numa sub-rede, o Azure delega essa sub-rede para as instâncias de contentores de Azure. Uma vez delegada, a sub-rede só pode ser utilizada para grupos de contentores. Se tentar mobilizar recursos que não os grupos de contentores para uma sub-rede delegada, a operação falha.

### <a name="network-profile"></a>Perfil de rede

Um perfil de rede é um modelo de configuração de rede para recursos Azure. Especifica certas propriedades da rede para o recurso, por exemplo, a subrede na qual deve ser implantado. Quando utiliza o [recipiente Az criar][az-container-create] um comando para implantar um grupo de contentores numa subrede (e, portanto, uma rede virtual), o Azure cria um perfil de rede para si. Em seguida, pode utilizar esse perfil de rede para futuras implementações na sub-rede. 

Para utilizar um modelo de Gestor de Recursos, ficheiro YAML ou um método programático para implantar um grupo de contentores numa subnet, precisa de fornecer o ID completo do recurso do Gestor de Recursos de um perfil de rede. Pode utilizar um perfil previamente criado usando a criação do [recipiente Az,][az-container-create]ou criar um perfil utilizando um modelo de Gestor de Recursos (ver exemplo de [modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) e [referência).](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles) Para obter a identificação de um perfil previamente criado, use o comando da lista de perfis de [rede az.][az-network-profile-list] 

No diagrama seguinte, vários grupos de contentores foram implantados numa subnet delegada em Instâncias de Contentores De Azure. Depois de ter implantado um grupo de contentores numa subnet, pode implantar grupos de contentores adicionais para o mesmo perfil de rede.

![Grupos de contentores dentro de uma rede virtual][aci-vnet-01]

## <a name="next-steps"></a>Passos seguintes

* Para exemplos de implantação com o Azure CLI, consulte as instâncias de [contentores de implantação numa rede virtual Azure](container-instances-vnet.md).
* Para implantar uma nova rede virtual, subnet, perfil de rede e grupo de contentores usando um modelo de Gestor de Recursos, consulte Criar um grupo de [contentores Azure com VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-virtual-network-concepts/aci-vnet-01.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list

