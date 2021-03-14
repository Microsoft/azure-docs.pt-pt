---
title: Conceitos - Interconectividade da rede
description: Conheça os principais aspetos e utilize casos de networking e interconectividade na Azure VMware Solution.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: cd62949c13b1f12e635d8d7bf07518a94c4e8d4b
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462593"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Conceitos de networking e interconectividade Azure VMware Solution

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Existem duas formas de interconectividade na nuvem privada Azure VMware Solution:

1. [**A interconectividade básica só de Azure permite-lhe**](#azure-virtual-network-interconnectivity) gerir e utilizar a sua nuvem privada com apenas uma única rede virtual em Azure. Esta implementação é mais adequada para avaliações ou implementações da Azure VMware Solution que não requerem acesso a partir de ambientes no local.

1. [**As instalações completas para a interconectividade de nuvem privada**](#on-premises-interconnectivity) alargam a implementação básica apenas do Azure para incluir a interconectividade entre as nuvens privadas do Azure VMware Solution.
 
Neste artigo, vamos cobrir os conceitos-chave que estabelecem networking e interconectividade, incluindo requisitos e limitações. Este artigo fornece-lhe as informações que precisa de saber para configurar a sua rede para trabalhar com a Azure VMware Solution.

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

Pode interligar a sua rede virtual Azure com a implementação da nuvem privada Azure VMware Solution. Pode gerir a sua nuvem privada Azure VMware Solution, consumir cargas de trabalho na sua nuvem privada e aceder a outros serviços Azure.

O diagrama abaixo mostra a interconectividade básica da rede estabelecida no momento de uma implantação de nuvem privada. Mostra a ligação lógica entre uma rede virtual em Azure e uma nuvem privada. Esta conectividade é estabelecida através de um backend ExpressRoute que faz parte do serviço Azure VMware Solution. A interconectividade preenche os seguintes casos de utilização primária:

- Acesso de entrada ao servidor vCenter e ao gestor NSX-T que esteja acessível a partir de VMs na sua subscrição Azure.
- Acesso de saída de VMs na nuvem privada para os serviços Azure.
- Acesso à entrada de cargas de trabalho em execução na nuvem privada.


:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Rede virtual básica para conectividade em nuvem privada" border="false":::

## <a name="on-premises-interconnectivity"></a>Interconectividade no local

No cenário totalmente interligado, pode aceder à Solução Azure VMware a partir da sua rede virtual Azure e no local. Esta implementação é uma extensão da implementação básica descrita na secção anterior. É necessário um circuito ExpressRoute para ligar do local à sua nuvem privada Azure VMware Solution em Azure.

O diagrama abaixo mostra as instalações para a interconectividade da nuvem privada, o que permite os seguintes casos de utilização:

- VCenter quente/frio vMotion entre as instalações e Azure VMware Solution.
- Acesso de gestão de nuvem privada Azure VMware Solution.

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Rede virtual e conectividade de nuvem privada completa" border="false":::

Para uma interconectividade total com a sua nuvem privada, precisa de ativar o ExpressRoute Global Reach e, em seguida, solicitar uma chave de autorização e iD de observação privada para o Global Reach no portal Azure. A chave de autorização e iD de espreitamento são usadas para estabelecer o Global Reach entre um circuito ExpressRoute na sua subscrição e o circuito ExpressRoute para a sua nuvem privada. Uma vez ligados, os dois circuitos ExpressRoute encaminham o tráfego de rede entre os ambientes no local e a sua nuvem privada. Para obter mais informações sobre os procedimentos, consulte o [tutorial para criar um ExpressRoute Global Reach olhando para uma nuvem privada.](tutorial-expressroute-global-reach-private-cloud.md)

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
