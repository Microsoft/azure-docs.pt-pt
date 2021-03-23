---
title: Conceitos - Nuvens privadas e aglomerados
description: Conheça as principais capacidades dos centros de dados definidos por software Azure VMware Solution e clusters vSphere.
ms.topic: conceptual
ms.date: 03/13/2021
ms.openlocfilehash: aff66e01ae4b056eb082d2000611718b1a5cf66a
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773930"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Azure VMware Solution conceitos de nuvem privada e cluster

A Azure VMware Solution oferece nuvens privadas baseadas em VMware em Azure. Nuvens privadas contêm aglomerados construídos com hospedeiros Azure dedicados e de metal nu. São implantados e geridos através do portal Azure, CLI ou PowerShell.  Os clusters a provisionados em nuvens privadas incluem vMware vSphere, vCenter, vSAN e software NSX. Azure VMware Solution hardware e software privados em nuvem são totalmente integrados e automatizados em Azure.

Há uma relação lógica entre subscrições Azure, nuvens privadas Azure VMware Solution, clusters vSAN e anfitriões. O diagrama mostra uma única assinatura Azure com duas nuvens privadas que representam o ambiente de desenvolvimento e produção.  Em cada uma dessas nuvens privadas há dois aglomerados. 

Este artigo descreve todos estes conceitos.

![Imagem de duas nuvens privadas em uma subscrição de cliente](./media/hosts-clusters-private-clouds-final.png)


## <a name="private-clouds"></a>Nuvens privadas

Nuvens privadas contêm aglomerados vSAN construídos com hospedeiros Azure dedicados e de metal nu. Cada nuvem privada pode ter vários clusters geridos pelo mesmo servidor vCenter e NSX-T Manager. Pode implementar e gerir nuvens privadas no portal, CLI ou PowerShell. 

Tal como acontece com outros recursos, as nuvens privadas são instaladas e geridas a partir de uma subscrição do Azure. O número de nuvens privadas dentro de uma subscrição é escalável. Inicialmente, há um limite de uma nuvem privada por subscrição.

## <a name="clusters"></a>Clusters
Para cada nuvem privada criada, há um cluster vSAN por padrão. Pode adicionar, eliminar e escalar agrupamentos utilizando o portal Azure ou através da API.  Todos os clusters têm um tamanho padrão de três anfitriões e podem escalar até 16 anfitriões. Pode ter até quatro aglomerados por nuvem privada.

Os clusters de ensaio estão disponíveis para avaliação e limitados a três anfitriões. Há um único aglomerado de ensaios por nuvem privada. Pode escalar um cluster de ensaio por um único hospedeiro durante o período de avaliação.

Você usa vSphere e NSX-T Manager para gerir a maioria dos outros aspetos da configuração ou funcionamento do cluster. Todo o armazenamento local de cada hospedeiro em um cluster está sob o controlo de vSAN.

## <a name="hosts"></a>Anfitriões

Os clusters Azure VMware Solution baseiam-se em infraestruturas de metal hiperconvernado e desnudadas. A tabela seguinte mostra as capacidades de RAM, CPU e disco do hospedeiro.

| Tipo de Anfitrião              |             CPU             |   RAM (GB)   |  vSAN NVMe cache Tier (TB, cru)  |  vSAN SSD nível de capacidade (TB, cru)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| AVS36          |  dual Intel 18 core 2.3 GHz  |     576      |                3.2               |                15.20               |

Os anfitriões usados para construir ou escalar aglomerados vêm de uma piscina isolada de anfitriões. Estes anfitriões passaram em testes de hardware e tiveram todos os dados eliminados de forma segura. 

## <a name="vmware-software-versions"></a>Versões de software VMware

[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]

## <a name="update-frequency"></a>Atualização de frequência

[!INCLUDE [vmware-software-update-frequency](includes/vmware-software-update-frequency.md)]

## <a name="host-maintenance-and-lifecycle-management"></a>Manutenção do anfitrião e gestão do ciclo de vida

A manutenção do hospedeiro e a gestão do ciclo de vida não têm qualquer impacto na capacidade ou desempenho dos aglomerados de nuvem privada.  Exemplos de manutenção automatizada do hospedeiro incluem upgrades de firmware e reparação de hardware ou substituição.

A Microsoft é responsável pela gestão do ciclo de vida de aparelhos NSX-T, tais como NSX-T Manager e NSX-T Edge. A Microsoft é responsável pela configuração da rede de bootstrapping, como criar o gateway Tier-0 e permitir North-South encaminhamento. É responsável pela configuração NSX-T SDN. Por exemplo, segmentos de rede, regras de firewall distribuídas, gateways de nível 1 e equilibres de carga.

## <a name="backup-and-restoration"></a>Backup e restauro

As configurações de nuvem privada vCenter e NSX-T estão num horário de backup de hora a hora.  Os reforços são mantidos por três dias. Se precisar de restaurar a partir de uma cópia de segurança, abra um pedido de [apoio](https://rc.portal.azure.com/#create/Microsoft.Support) no portal Azure para solicitar a restauração.

## <a name="next-steps"></a>Passos seguintes

Agora que cobriu os conceitos de nuvem privada Azure VMware Solution, talvez queira aprender sobre: 

- [Conceitos de networking e interconectividade Azure VMware Solution](concepts-networking.md).
- [Conceitos de armazenamento de solução Azure VMware](concepts-storage.md).
- [Como ativar o recurso Azure VMware Solution](enable-azure-vmware-solution.md).

<!-- LINKS - internal -->
[concepts-networking]: ./concepts-networking.md

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

