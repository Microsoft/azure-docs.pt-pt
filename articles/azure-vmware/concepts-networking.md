---
title: Conceitos - Interconectividade da rede
description: Conheça os principais aspetos e utilize casos de networking e interconectividade na Azure VMware Solution.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 9531e08ea4e50ae30058b0630cd12c2383d90fde
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103197177"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Conceitos de networking e interconectividade Azure VMware Solution

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Uma perspetiva útil sobre interconectividade é considerar os dois tipos de implementações em nuvem privada Azure VMware Solution:

1. [**A interconectividade básica só de Azure permite-lhe**](#azure-virtual-network-interconnectivity) gerir e utilizar a sua nuvem privada com apenas uma única rede virtual em Azure. Esta implementação é mais adequada para avaliações ou implementações da Azure VMware Solution que não requerem acesso a partir de ambientes no local.

1. [**As instalações completas para a interconectividade de nuvem privada**](#on-premises-interconnectivity) alargam a implementação básica apenas do Azure para incluir a interconectividade entre as nuvens privadas do Azure VMware Solution.
 
Neste artigo, vamos abranger alguns conceitos-chave que estabelecem networking e interconectividade, incluindo requisitos e limitações. Também cobriremos mais informações sobre os dois tipos de implementações de interconectividade em nuvem privada Azure VMware Solution. Este artigo fornece-lhe as informações que precisa de saber para configurar a sua rede para trabalhar corretamente com a Azure VMware Solution.

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>Casos de uso de nuvem privada Azure VMware Solution

Os casos de utilização de nuvens privadas Azure VMware Solution incluem:
- Novas cargas de trabalho VMware VMware na nuvem
- Carga de trabalho VM a rebentar até à nuvem (apenas no local para a Solução VMware Azure)
- VM migração de carga de trabalho para a nuvem (apenas no local para Azure VMware Solution)
- Recuperação de desastres (Solução Azure VMware para Solução VMware Azure ou no local para Azure VMware Solution)
- Consumo de serviços Azure

> [!TIP]
> Todas as caixas de utilização do serviço Azure VMware Solution estão ativadas com conectividade na nuvem privada.

## <a name="azure-virtual-network-interconnectivity"></a>Interconectividade da rede virtual Azure

Na rede virtual para implementação de nuvem privada, pode gerir a sua nuvem privada Azure VMware Solution, consumir cargas de trabalho na sua nuvem privada e aceder aos serviços Azure sobre a ligação ExpressRoute. 

O diagrama abaixo mostra a interconectividade básica da rede estabelecida no momento de uma implantação de nuvem privada. Mostra a rede lógica baseada no ExpressRoute entre uma rede virtual em Azure e uma nuvem privada. A interconectividade preenche três dos casos de utilização primária:
* Acesso de entrada ao servidor vCenter e ao NSX-T Manager que esteja acessível a partir de VMs na sua subscrição Azure e não a partir dos seus sistemas no local. 
* Acesso de saída dos serviços VMs para Azure. 
* Acesso de entrada e consumo de cargas de trabalho com uma nuvem privada.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Rede virtual básica para conectividade em nuvem privada" border="false":::

## <a name="on-premises-interconnectivity"></a>Interconectividade no local

Na rede virtual e nas instalações para a implementação total da nuvem privada, pode aceder às suas nuvens privadas Azure VMware Solution a partir de ambientes no local. Esta implementação é uma extensão da implementação básica descrita na secção anterior. Tal como a implementação básica, é necessário um circuito ExpressRoute, mas com esta implementação, é usado para ligar dos ambientes no local à sua nuvem privada em Azure. 

O diagrama abaixo mostra as instalações para a interconectividade da nuvem privada, o que permite os seguintes casos de utilização:
* Hot/Cold Cross-vCenter vMotion
* Acesso de gestão de nuvem privada Azure VMware Solution

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Rede virtual e conectividade de nuvem privada completa" border="false":::

Para uma interconectividade total com a sua nuvem privada, ative o ExpressRoute Global Reach e, em seguida, solicite uma chave de autorização e iD de observação privada para o Alcance Global no portal Azure. A chave de autorização e iD de espreitar são usadas para estabelecer o Global Reach entre um circuito ExpressRoute na sua subscrição e o circuito ExpressRoute para a sua nova nuvem privada. Uma vez ligados, os dois circuitos ExpressRoute encaminham o tráfego de rede entre os ambientes no local e a sua nuvem privada.  Para obter mais informações sobre os procedimentos para solicitar e utilizar a chave de autorização e o ID de espreitar, consulte o [tutorial para criar um ExpressRoute Global Reach olhando para uma nuvem privada.](tutorial-expressroute-global-reach-private-cloud.md)

## <a name="limitations"></a>Limitações
[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## <a name="next-steps"></a>Passos seguintes 

Agora que cobriu a rede Azure VMware Solution e os conceitos de interconectividade, talvez queira aprender sobre:

- [Conceitos de armazenamento de solução Azure VMware](concepts-storage.md).
- [Conceitos de identidade Azure VMware Solution](concepts-identity.md).
- [Como ativar o recurso Azure VMware Solution](enable-azure-vmware-solution.md).

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
[concepts-storage]: ./concepts-storage.md
