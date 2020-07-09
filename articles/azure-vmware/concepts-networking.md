---
title: Conceitos - Interconectividade da rede
description: Conheça os principais aspetos e utilize casos de networking e interconectividade na Solução VMware Azure (AVS)
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 35d886fe0f6a68e522d7f2cf20b450b5d9afc199
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84629214"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Azure VMware Solution (AVS) Pré-visualização de conceitos de rede e interconectividade

A interconectividade da rede entre as suas nuvens privadas Azure VMware Solution (AVS) e ambientes no local ou redes virtuais em Azure permite-lhe aceder e utilizar a sua nuvem privada. Neste artigo são descritos alguns conceitos-chave de ligação em rede e interconectividade que estabelecem a base da interconectividade.

Uma perspetiva útil sobre a interconectividade é considerar os dois tipos de implementações em nuvem privada AVS: implementações com interconectividade básica apenas azure e implementações com completa inconectividade na nuvem privada.

Os casos de utilização de nuvens privadas AVS incluem:
- novas cargas de trabalho VMware VMware na nuvem
- Carga de trabalho VM rebentando para a nuvem
- VM migração de carga de trabalho para a nuvem
- recuperação de desastres
- consumo de serviços Azure

 Todos os casos de utilização do serviço AVS estão habilitados com conectividade em nuvem privada. O modelo básico de interconectividade é mais adequado para avaliações ou implementações avs que não requerem acesso a partir de ambientes no local.

Os dois tipos de interconectividade de nuvem privada AVS são descritos nas secções abaixo.  A interconectividade mais básica é a "conectividade da rede virtual Azure"; permite-lhe gerir e utilizar a sua nuvem privada com apenas uma rede virtual em Azure. A interconectividade descrita na "conectividade no local" alarga a conectividade da rede virtual para incluir também a interconectividade entre ambientes no local e nuvens privadas AVS.

## <a name="azure-virtual-network-interconnectivity"></a>Interconectividade da rede virtual Azure

A interconectividade básica da rede que é estabelecida no momento de uma implantação de nuvem privada é mostrada no diagrama abaixo. Mostra a rede lógica baseada no ExpressRoute entre uma rede virtual em Azure e uma nuvem privada. A interconectividade preenche três dos casos de utilização primária:
- Acesso de entrada a redes de gestão onde estão localizados o servidor vCenter e o gestor NSX-T.
    - Acessível a partir de VMs dentro da sua subscrição Azure, não a partir dos seus sistemas no local.
- Acesso de saída dos serviços VMs para Azure.
- Acesso de entrada e consumo de cargas de trabalho com uma nuvem privada.

![Rede virtual básica -para- conectividade de nuvem privada](./media/concepts/adjacency-overview-drawing-single.png)

O circuito ExpressRoute nesta rede virtual para cenário de nuvem privada é estabelecido quando cria uma ligação a partir de uma rede virtual na sua subscrição para o circuito ExpressRoute da sua nuvem privada. O estorva utiliza uma chave de autorização e um ID de circuito que você solicita no portal Azure. A ligação ExpressRoute que é estabelecida através do espreitamento é uma ligação privada, um a um entre a sua nuvem privada e a rede virtual. Você pode gerir a sua nuvem privada, consumir cargas de trabalho na sua nuvem privada, e aceder aos serviços Azure sobre essa ligação ExpressRoute.

Quando implementa uma nuvem privada AVS, é necessário um espaço de endereço de rede privada único /22. Este espaço de endereço não deve sobrepor-se aos espaços de endereço utilizados noutras redes virtuais na sua subscrição. Dentro deste espaço de endereço, as redes de gestão, provisionamento e vMotion são aprovisionadas automaticamente. O encaminhamento é baseado em BGP e é automaticamente a provisionado e ativado por padrão para cada implementação de nuvem privada.

Quando uma nuvem privada é implantada, você é fornecido com os endereços IP para vCenter e NSX-T Manager. Para aceder a essas interfaces de gestão, criará recursos adicionais numa rede virtual na sua subscrição. Os procedimentos para a criação desses recursos e para o estabelecimento do peering privado ExpressRoute são fornecidos nos tutoriais.

Você projeta a rede lógica de nuvem privada e implementá-la com NSX-T. A nuvem privada vem com NSX-T pré-a provisionado. Um Gateway Tier-0 & Gateway Tier-1 está pré-a ser a provisionado para si. Pode criar um segmento e anexá-lo ao portal de nível 1 existente ou anexar-se a um novo portal Tier-1 que pode definir. Os componentes de rede lógico NSX-T fornecem conectividade Leste-Oeste entre cargas de trabalho e também fornecem conectividade Norte-Sul à internet e serviços Azure. 

## <a name="on-premises-interconnectivity"></a>Interconectividade no local

Também pode ligar ambientes no local às suas nuvens privadas AVS. Este tipo de interconectividade é uma extensão à interconectividade básica descrita na secção anterior.

![rede virtual e conectividade de nuvem privada completa](./media/concepts/adjacency-overview-drawing-double.png)

Para estabelecer uma interconectividade total a uma nuvem privada, utiliza o portal Azure para permitir o ExpressRoute Global Reach entre um circuito ExpressRoute em nuvem privada e um circuito ExpressRoute no local. Esta configuração estende a conectividade básica para incluir o acesso a nuvens privadas de ambientes no local.

Um circuito ExpressRoute de rede virtual Azure é necessário para ligar dos ambientes no local à sua nuvem privada em Azure. Este circuito ExpressRoute está na sua subscrição e não faz parte de uma implementação de nuvem privada. O circuito ExpressRoute está fora do âmbito deste documento. Se necessitar de conectividade no local para a sua nuvem privada, pode utilizar um dos circuitos ExpressRoute existentes ou comprar um no portal Azure.

Uma vez ligados ao Global Reach, os dois circuitos ExpressRoute vão encaminhar o tráfego de rede entre os seus ambientes no local e a sua nuvem privada. As interconectividades de nuvem privada são mostradas no diagrama anterior. A interconectividade representada no diagrama permite os seguintes casos de utilização:

- Hot/Cold Cross-vCenter vMotion
- Acesso à gestão privada de nuvem da AVS

Para permitir a conectividade total, uma Chave de Autorização e ID de observação privada para Alcance Global podem ser solicitadas no portal Azure. Utiliza a chave e o ID para estabelecer o Global Reach entre um circuito ExpressRoute na sua subscrição e o circuito ExpressRoute para a sua nova nuvem privada. O [tutorial para criar uma nuvem privada](tutorial-create-private-cloud.md) fornece-lhe os procedimentos para solicitar e usar a chave e iD.

Os requisitos de encaminhamento da solução requerem que planeie espaços de endereço de rede de nuvem privada para evitar sobreposições com outras redes virtuais e redes no local. As nuvens privadas AVS requerem um mínimo de um bloco de `/22` endereços de rede CIDR para sub-redes, mostrado abaixo. Esta rede complementa as suas redes no local. Para se ligar a ambientes no local e redes virtuais, este deve ser um bloco de endereços de rede não sobreposto.

Exemplo `/22` do bloco de endereços de rede CIDR:`10.10.0.0/22`

As sub-redes:

| Utilização de rede             | Subrede | Exemplo        |
| ------------------------- | ------ | -------------- |
| Gestão de clouds privadas            | `/24`    | `10.10.0.0/24`   |
| vMotion network       | `/24`    | `10.10.1.0/24`   |
| Cargas de trabalho de VM | `/24`   | `10.10.2.0/24`   |
| ExpressRoute olhando | `/24`    | `10.10.3.8/30`   |

## <a name="next-steps"></a>Próximos passos 

O próximo passo é aprender sobre [conceitos privados de armazenamento em nuvem.](concepts-storage.md)

<!-- LINKS - external -->
[enable Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach

<!-- LINKS - internal -->

