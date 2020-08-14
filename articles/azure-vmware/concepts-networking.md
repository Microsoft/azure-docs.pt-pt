---
title: Conceitos - Interconectividade da rede
description: Conheça os principais aspetos e utilize casos de networking e interconectividade na Solução VMware Azure (AVS)
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 6f1f1f5a089781f1f7e882c9c8692f0c845ae485
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214106"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Conceitos de rede e interconectividade da Pré-visualização do Azure VMware Solution (AVS)

A interconectividade da rede entre as suas nuvens privadas Azure VMware Solution (AVS) e ambientes no local ou redes virtuais em Azure permite-lhe aceder e utilizar a sua nuvem privada. Neste artigo, vamos abordar alguns conceitos-chave que estabelecem a base do networking e da interconectividade.

Uma perspetiva útil sobre a interconectividade é considerar os dois tipos de implementações em nuvem privada AVS:

1. [**A interconectividade básica só de Azure permite-lhe**](#azure-virtual-network-interconnectivity) gerir e utilizar a sua nuvem privada com apenas uma única rede virtual em Azure. Esta implementação é mais adequada para avaliações ou implementações avs que não requerem acesso a partir de ambientes no local.

1. [**As instalações completas para a interconectividade de nuvem privada**](#on-premises-interconnectivity) alargam a implementação básica apenas do Azure para incluir a interconectividade entre as nuvens privadas no local e as nuvens privadas AVS.
 
Pode encontrar mais informações sobre os requisitos e os dois tipos de implementações de interconectividade de nuvem privada AVS descritas nas secções abaixo.

## <a name="avs-private-cloud-use-cases"></a>Casos de uso de nuvem privada AVS

Os casos de utilização de nuvens privadas AVS incluem:
- novas cargas de trabalho VMware VMware na nuvem
- Carga de trabalho VM a rebentar até à nuvem (apenas no local para AVS)
- VM migração da carga de trabalho para a nuvem (apenas no local para AVS)
- recuperação de desastres (AVS a AVS ou no local para AVS)
- consumo de serviços Azure

 Todos os casos de utilização do serviço AVS estão habilitados com conectividade em nuvem privada. 

## <a name="virtual-network-and-expressroute-circuit-requirements"></a>Requisitos de rede virtual e circuito ExpressRoute
 
Quando cria uma ligação a partir de uma rede virtual na sua subscrição, o circuito ExpressRoute é estabelecido através do estorte, utiliza uma chave de autorização e um ID de esprevamento que solicita no portal Azure. O espreitamento é uma ligação privada, um a um entre a sua nuvem privada e a rede virtual.

> [!NOTE] 
> O circuito ExpressRoute não faz parte de uma nuvem privada. O circuito ExpressRoute está fora do âmbito deste documento. Se necessitar de conectividade no local para a sua nuvem privada, pode utilizar um dos circuitos ExpressRoute existentes ou comprar um no portal Azure.

Ao implementar uma nuvem privada, recebe endereços IP para vCenter e NSX-T Manager. Para aceder a essas interfaces de gestão, terá de criar recursos adicionais numa rede virtual na sua subscrição. Você pode encontrar os procedimentos para criar esses recursos e estabelecer o expressRoute private peering nos tutoriais.

A rede lógica de nuvem privada vem com NSX-T pré-a provisionado. Um portal tier-0 e porta de entrada tier-1 é pré-a provisionado para si. Pode criar um segmento e anexá-lo ao portal de nível 1 existente ou anexá-lo a um novo portal Tier-1 que definir. Os componentes de rede lógico NSX-T fornecem conectividade Leste-Oeste entre cargas de trabalho e também fornecem conectividade Norte-Sul à internet e serviços Azure. 

## <a name="routing-and-subnet-requirements"></a>Requisitos de encaminhamento e sub-rede

O encaminhamento é baseado em Border Gateway Protocol (BGP), que é automaticamente a provisionado e habilitado por padrão para cada implementação de nuvem privada. Para as nuvens privadas AVS, é necessário planear espaços de endereço de rede de nuvem privada com um mínimo de /22 blocos de endereços de rede CIDR de comprimento pré-fixo para sub-redes, mostrados na tabela abaixo. O bloco de endereços não deve sobrepor-se aos blocos de endereços utilizados noutras redes virtuais que se encontram nas suas redes de subscrição e no local. Dentro deste bloco de endereços, as redes de gestão, provisionamento e vMotion são aprovisionadas automaticamente.

Exemplo `/22` do bloco de endereços de rede CIDR:  `10.10.0.0/22`

As sub-redes:

| Utilização de rede             | Subrede | Exemplo        |
| ------------------------- | ------ | -------------- |
| Gestão de clouds privadas  | `/24`  | `10.10.0.0/24` |
| vMotion network           | `/24`  | `10.10.1.0/24` |
| Cargas de trabalho de VM              | `/24`  | `10.10.2.0/24` |
| ExpressRoute olhando      | `/24`  | `10.10.3.8/30` |


## <a name="azure-virtual-network-interconnectivity"></a>Interconectividade da rede virtual Azure

Na rede virtual para implementação de nuvem privada, pode gerir a sua nuvem privada AVS, consumir cargas de trabalho na sua nuvem privada e aceder aos serviços Azure sobre a ligação ExpressRoute. 

O diagrama abaixo mostra a interconectividade básica da rede estabelecida no momento de uma implantação de nuvem privada. Mostra a rede lógica baseada no ExpressRoute entre uma rede virtual em Azure e uma nuvem privada. A interconectividade preenche três dos casos de utilização primária:
* Acesso de entrada ao servidor vCenter e ao gestor NSX-T que esteja acessível a partir de VMs na sua subscrição Azure e não a partir dos seus sistemas no local. 
* Acesso de saída dos serviços VMs para Azure. 
* Acesso de entrada e consumo de cargas de trabalho com uma nuvem privada.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Rede virtual básica para conectividade em nuvem privada" border="false":::

## <a name="on-premises-interconnectivity"></a>Interconectividade no local

Na rede virtual e nas instalações para a plena implementação da nuvem privada, pode aceder às suas nuvens privadas AVS a partir de ambientes no local. Esta implementação é uma extensão da implementação básica descrita na secção anterior. Tal como a implementação básica, é necessário um circuito ExpressRoute, mas com esta implementação, é usado para ligar dos ambientes no local à sua nuvem privada em Azure. 

O diagrama abaixo mostra as instalações para a interconectividade da nuvem privada, o que permite os seguintes casos de utilização:
* Hot/Cold Cross-vCenter vMotion
* Acesso à gestão privada de nuvem da AVS

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Rede virtual e conectividade de nuvem privada completa" border="false":::

Para uma interconectividade total com a sua nuvem privada, ative o ExpressRoute Global Reach e, em seguida, solicite uma chave de autorização e iD de observação privada para o Alcance Global no portal Azure. A chave de autorização e iD de espreitar são usadas para estabelecer o Global Reach entre um circuito ExpressRoute na sua subscrição e o circuito ExpressRoute para a sua nova nuvem privada. Uma vez ligados, os dois circuitos ExpressRoute encaminham o tráfego de rede entre os ambientes no local e a sua nuvem privada.  Consulte o [tutorial para criar um ExpressRoute Global Reach olhando para uma nuvem privada](tutorial-expressroute-global-reach-private-cloud.md) para os procedimentos solicitarem e usarem a chave de autorização e o ID de espreitar.


## <a name="next-steps"></a>Passos seguintes 

O próximo passo é aprender sobre [conceitos privados de armazenamento em nuvem.](concepts-storage.md)

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
