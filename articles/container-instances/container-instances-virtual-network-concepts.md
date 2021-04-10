---
title: Cenários para usar uma rede virtual
description: Cenários, recursos e limitações para implantar grupos de contentores numa rede virtual Azure.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 20c2b4fe2f19402d6647f398a9696b7e16550d8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606893"
---
# <a name="virtual-network-scenarios-and-resources"></a>Cenários e recursos de rede virtual

[A Azure Virtual Network](../virtual-network/virtual-networks-overview.md) fornece redes seguras e privadas para os seus recursos Azure e no local. Ao implantar grupos de contentores numa rede virtual Azure, os seus contentores podem comunicar de forma segura com outros recursos na rede virtual. 

Este artigo fornece antecedentes sobre cenários de rede virtuais, limitações e recursos. Para exemplos de implantação utilizando o CLI Azure, consulte [as instâncias de implantação de contentores numa rede virtual Azure](container-instances-vnet.md).

> [!IMPORTANT]
> A implantação do grupo de contentores para uma rede virtual está geralmente disponível para contentores Linux, na maioria das regiões onde existem instâncias para contentores Azure. Para mais informações, consulte [Regiões e disponibilidade de recursos.](container-instances-region-availability.md) 

## <a name="scenarios"></a>Cenários

Grupos de contentores implantados numa rede virtual Azure permitem cenários como:

* Comunicação direta entre grupos de contentores na mesma sub-rede
* Enviar saída de carga de trabalho [baseada em tarefas](container-instances-restart-policy.md) de casos de contentores para uma base de dados na rede virtual
* Recuperar o conteúdo para instâncias de contentores a partir de um [ponto final](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço na rede virtual
* Permitir a comunicação de contentores com recursos no local através de uma [porta VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ExpressRoute](../expressroute/expressroute-introduction.md)
* Integre com [a Azure Firewall](../firewall/overview.md) para identificar o tráfego de saída originário do contentor 
* Resolver nomes através do Azure DNS interno para comunicação com os recursos do Azure na rede virtual, como máquinas virtuais
* Utilizar regras NSG para controlar o acesso de contentores a sub-redes ou outros recursos de rede

## <a name="unsupported-networking-scenarios"></a>Cenários de networking não suportados 

* **Balançador de carga Azure** - A colocação de um balançador de carga Azure em frente a casos de contentores num grupo de contentores em rede não é suportada
* **O espreitamento global da rede virtual** - O espreitamento global (que liga redes virtuais em regiões de Azure) não é suportado
* **Etiqueta PÚBLICA IP ou DNS** - Os grupos de contentores implantados numa rede virtual não suportam atualmente a exposição de contentores diretamente à internet com um endereço IP público ou um nome de domínio totalmente qualificado
* **Rede Virtual NAT** - Os grupos de contentores implantados numa rede virtual não suportam atualmente o uso de um recurso de gateway NAT para a conectividade de saída da Internet.

## <a name="other-limitations"></a>Outras limitações

* Atualmente, apenas os contentores Linux são suportados num grupo de contentores implantados numa rede virtual.
* Para implantar grupos de contentores numa sub-rede, a sub-rede não pode conter outros tipos de recursos. Remova todos os recursos existentes de uma sub-rede existente antes de colocar grupos de contentores na sua casa ou criar uma nova sub-rede.
* Não é possível utilizar uma [identidade gerida](container-instances-managed-identity.md) num grupo de contentores implantado numa rede virtual.
* Não é possível ativar uma [sonda de prontidão](container-instances-liveness-probe.md) ou [uma sonda de prontidão](container-instances-readiness-probe.md) num grupo de contentores implantado numa rede virtual.
* Devido aos recursos adicionais de rede envolvidos, as implantações para uma rede virtual são tipicamente mais lentas do que a implantação de uma instância padrão de contentor.
* Se estiver a ligar o seu grupo de contentores a uma Conta de Armazenamento Azure, tem de adicionar um [ponto final](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço a esse recurso.

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="required-network-resources"></a>Recursos de rede necessários

Existem três recursos da Rede Virtual Azure necessários para a implantação de grupos de contentores numa rede virtual: a [própria rede virtual,](#virtual-network) uma [sub-rede delegada](#subnet-delegated) dentro da rede virtual e um [perfil de rede.](#network-profile) 

### <a name="virtual-network"></a>Rede virtual

Uma rede virtual define o espaço de endereço no qual cria uma ou mais sub-redes. Em seguida, inserte recursos Azure (como grupos de contentores) nas sub-redes da sua rede virtual.

### <a name="subnet-delegated"></a>Sub-rede (delegada)

Sub-redes segmentam a rede virtual em espaços de endereço separados utilizáveis pelos recursos Azure que coloca neles. Cria-se uma ou várias sub-redes dentro de uma rede virtual.

A sub-rede que utiliza para grupos de contentores pode conter apenas grupos de contentores. Quando coloca pela primeira vez um grupo de contentores numa sub-rede, a Azure delega essa sub-rede para instâncias de contentores Azure. Uma vez delegada, a sub-rede só pode ser utilizada para grupos de contentores. Se tentar distribuir recursos que não grupos de contentores para uma sub-rede delegada, a operação falha.

### <a name="network-profile"></a>Perfil de rede

Um perfil de rede é um modelo de configuração de rede para recursos Azure. Especifica certas propriedades de rede para o recurso, por exemplo, a sub-rede em que deve ser implantada. Quando utiliza pela primeira vez o [contentor az cria][az-container-create] comando para implantar um grupo de contentores numa sub-rede (e, portanto, numa rede virtual), o Azure cria um perfil de rede para si. Em seguida, pode utilizar esse perfil de rede para futuras implementações na sub-rede. 

Para utilizar um modelo de Gestor de Recursos, um ficheiro YAML ou um método programático para implantar um grupo de contentores numa sub-rede, é necessário fornecer o ID completo do Gestor de Recursos de um perfil de rede. Pode utilizar um perfil previamente criado utilizando [o recipiente az criar,][az-container-create]ou criar um perfil utilizando um modelo de Gestor de Recursos (ver exemplo e [referência](/azure/templates/microsoft.network/networkprofiles) [do modelo).](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) Para obter o ID de um perfil previamente criado, utilize o comando [da lista de perfis de rede az.][az-network-profile-list] 

No diagrama seguinte, vários grupos de contentores foram destacados para uma sub-rede delegada em Instâncias de Contentores de Azure. Uma vez colocado um grupo de contentores numa sub-rede, pode colocar grupos de contentores adicionais para ele especificando o mesmo perfil de rede.

![Grupos de contentores dentro de uma rede virtual][aci-vnet-01]

## <a name="next-steps"></a>Passos seguintes

* Para exemplos de implantação com o CLI Azure, consulte [as instâncias de implantação de contentores numa rede virtual Azure](container-instances-vnet.md).
* Para implementar uma nova rede virtual, sub-rede, perfil de rede e grupo de contentores utilizando um modelo de Gestor de Recursos, consulte [Criar um grupo de contentores Azure com VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).
* Ao utilizar o [portal Azure](container-instances-quickstart-portal.md) para criar uma instância de contentor, também pode fornecer definições para uma rede virtual nova ou exsting no **separador Networking.**


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-virtual-network-concepts/aci-vnet-01.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
