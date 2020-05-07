---
title: Conceitos - Interconectividade da rede
description: Conheça os aspectos-chave e utilize casos de networking e interconectividade na Solução Azure VMware (AVS)
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 82e5497c30bf02313e5ff8ad24167af569a153c2
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740918"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Conceitos de solução Azure VMware (AVS) Pré-visualização de networking e interconectividade

A interconectividade da rede entre as suas nuvens privadas Azure VMware Solution (AVS) e ambientes no local ou redes virtuais em Azure permite-lhe aceder e utilizar a sua nuvem privada. Alguns conceitos-chave de networking e interconectividade que estabelecem a base da interconectividade são descritos neste artigo.

Uma perspetiva útil sobre a interconectividade é considerar os dois tipos de implementações privadas de nuvem AVS. As implementações com interconectividade básica só de Azure e as implementações com plena suposições para a interconectividade privada em nuvem.

Os casos de utilização para nuvens privadas AVS incluem:
- novas cargas de trabalho VMware VM na nuvem
- Carga de trabalho VM rebentando para a nuvem
- Migração de carga de trabalho VM para a nuvem
- recuperação de desastres
- consumo de serviços Azure

 Todos os casos de utilização do serviço AVS estão habilitados com as instalações para a conectividade privada da nuvem. O modelo básico de interconectividade é mais adequado para avaliações ou implementações de AVS que não requerem acesso a partir de ambientes no local.

Os dois tipos de interconectividade privada da nuvem AVS são descritos nas secções abaixo.  A interconectividade mais básica é a "conectividade da rede virtual Azure", e permite-lhe gerir e utilizar a sua nuvem privada com apenas uma rede virtual em Azure. A interconectividade descrita na "conectividade no local" alarga a conectividade da rede virtual para incluir também a interconectividade entre ambientes no local e nuvens privadas AVS.

## <a name="azure-virtual-network-interconnectivity"></a>Interconectividade da rede virtual Azure

A interconectividade básica da rede que é estabelecida no momento de uma implantação privada de nuvem é mostrada no diagrama abaixo. Mostra o networking lógico e baseado no ExpressRoute entre uma rede virtual em Azure e uma nuvem privada. A interconectividade preenche três dos casos de utilização primária:
- Acesso de entrada a redes de gestão onde o servidor vCenter e o gestor NSX-T estão localizados.
    - Acessível a partir de VMs dentro da sua subscrição Azure, não a partir dos seus sistemas no local.
- Acesso de saída de VMs para serviços Azure.
- Acesso e consumo de cargas de trabalho com uma nuvem privada.

![Rede virtual básica -para- conectividade privada em nuvem](./media/concepts/adjacency-overview-drawing-single.png)

O circuito ExpressRoute nesta rede virtual para o cenário de nuvem privada é estabelecido quando cria uma ligação a partir de uma rede virtual na sua subscrição ao circuito ExpressRoute da sua nuvem privada. O peering usa uma chave de autorização e um circuito de identificação que você solicita no portal Azure. A ligação ExpressRoute que é estabelecida através do peering é uma ligação privada, um a um entre a sua nuvem privada e a rede virtual. Você pode gerir sua nuvem privada, consumir cargas de trabalho na sua nuvem privada, e aceder aos serviços Azure sobre essa ligação ExpressRoute.

Quando se implanta uma nuvem privada AVS, é necessário um único espaço de endereço de rede privada /22. Este espaço de endereço não deve sobrepor-se a espaços de endereço utilizados noutras redes virtuais na sua subscrição. Neste espaço de endereço, as redes de gestão, provisionamento e vMotion são aprovisionadas automaticamente. O encaminhamento é baseado em BGP e é automaticamente provisionado e ativado por padrão para cada implantação privada em nuvem.

Quando uma nuvem privada é implantada, você é fornecido com os endereços IP para vCenter e NSX-T Manager. Para aceder a essas interfaces de gestão, irá criar recursos adicionais numa rede virtual na sua subscrição. Os procedimentos para a criação desses recursos e o estabelecimento de peering privado ExpressRoute são fornecidos nos tutoriais.

Você projeta a nuvem privada de rede lógica e implementá-lo com NSX-T. A nuvem privada vem com NSX-T pré-provisionado. Um Gateway Tier-0 & Gateway Tier-1 está pré-provisionado para si. Pode criar um segmento e anexá-lo ao gateway Tier-1 existente ou anexar-se a um novo gateway Tier-1 que possa definir. Os componentes lógicos de rede NSX-T fornecem conectividade Leste-Oeste entre cargas de trabalho e também fornecem conectividade Norte-Sul à internet e serviços Azure. 

## <a name="on-premises-interconnectivity"></a>Interconectividade no local

Também pode ligar ambientes no local às suas nuvens privadas AVS. Este tipo de interconectividade é uma extensão à interconectividade básica descrita na secção anterior.

![rede virtual e no local conectividade total nuvem privada](./media/concepts/adjacency-overview-drawing-double.png)

Para estabelecer a interconectividade total com uma nuvem privada, utiliza o portal Azure para permitir o ExpressRoute Global Reach entre um circuito privado de cloud ExpressRoute e um circuito ExpressRoute no local. Esta configuração estende a conectividade básica para incluir o acesso a nuvens privadas a partir de ambientes no local.

É necessário ligar do local um circuito de rede virtual Azure ExpressRoute para ligar a partir de ambientes no local à sua nuvem privada em Azure. Este circuito ExpressRoute está na sua subscrição e não faz parte de uma implantação privada em nuvem. O circuito ExpressRoute no local está fora do âmbito deste documento. Se necessitar de conectividade no local com a sua nuvem privada, pode utilizar um dos seus circuitos ExpressRoute existentes ou comprar um no portal Azure.

Uma vez ligados ao Global Reach, os dois circuitos ExpressRoute irão direcionar o tráfego da rede entre os ambientes no local e a sua nuvem privada. As instalações para a interconectividade da nuvem privada são mostradas no diagrama anterior. A interconectividade representada no diagrama permite os seguintes casos de utilização:

- VMotion hot/cold cross-vCenter
- Acesso à gestão privada de nuvem avs avs

Para permitir a conectividade completa, uma Chave de Autorização e identificação privada para o Alcance Global pode ser solicitada no portal Azure. Usa a chave e o ID para estabelecer o Global Reach entre um circuito ExpressRoute na sua subscrição e o circuito ExpressRoute para a sua nova nuvem privada. O [tutorial para criar uma nuvem privada](tutorial-create-private-cloud.md) fornece-lhe os procedimentos para solicitar e usar a chave e id.

Os requisitos de encaminhamento da solução exigem que planeie espaços privados de endereço sinuoso de rede de nuvem privada para evitar sobreposições com outras redes virtuais e redes no local. As nuvens privadas AVS requerem um mínimo de um `/22` bloco de endereços de rede CIDR para subredes, mostrado abaixo. Esta rede complementa as suas redes no local. Para se ligar aos ambientes no local e às redes virtuais, este deve ser um bloco de endereços de rede não sobreposto.

Exemplo `/22` do bloco de endereços da rede CIDR:`10.10.0.0/22`

As sub-redes:

| Utilização de rede             | Subrede | Exemplo        |
| ------------------------- | ------ | -------------- |
| Gestão de clouds privadas            | `/24`    | `10.10.0.0/24`   |
| rede vMotion       | `/24`    | `10.10.1.0/24`   |
| Cargas de trabalho de VM | `/24`   | `10.10.2.0/24`   |
| Peering ExpressRoute | `/24`    | `10.10.3.8/30`   |

## <a name="next-steps"></a>Passos seguintes 

O próximo passo é aprender sobre [conceitos privados](concepts-storage.md)de armazenamento de nuvens.

<!-- LINKS - external -->
[enable Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach

<!-- LINKS - internal -->

